
# Pashov Audit Group 소개

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 입증된 인재들입니다. 대부분은 공개 대회 보상으로 10만 달러 이상을 획득했거나, 다회 우승자이거나, 저희와의 감사에서 진정으로 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도우면서, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

<p><strong>ValantisLabs/sthype-contracts</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, <strong>Shaka, Tejas Warambhe, iamnmt, IvanFitro</strong>가 <strong>stHYPE</strong> 검토에 참여했습니다. 총 <strong>9</strong>개의 이슈가 발견되었습니다.</p>

# stHYPE 소개

<p>stHYPE는 Hyperliquid의 HyperEVM을 위해 구축된 유동성 스테이킹 프로토콜(Liquid Staking Protocol)로, 사용자가 네이티브 HYPE 토큰을 스테이킹하고 stHYPE를 받을 수 있게 합니다. stHYPE는 거래 가능한 상태를 유지하면서 보상을 축적하는 유동성 스테이킹 파생 상품입니다. 이는 Overseer 계약을 통한 모듈식 스테이킹, 리베이스(rebasing) stHYPE 토큰, 그리고 DeFi 통합을 위한 랩핑된 비리베이스(wrapped non-rebasing) wstHYPE 버전을 특징으로 합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [19e5a53195be079f6764b4153fcc591b51e2bb97](https://github.com/ValantisLabs/sthype-contracts/tree/19e5a53195be079f6764b4153fcc591b51e2bb97)<br>&nbsp;&nbsp;(ValantisLabs/sthype-contracts)

**수정 검토 커밋 해시:**<br>• [e97f932c307104c6799bea0c10c45a9da552f14f](https://github.com/ValantisLabs/sthype-contracts/tree/e97f932c307104c6799bea0c10c45a9da552f14f)<br>&nbsp;&nbsp;(ValantisLabs/sthype-contracts)

# 범위

- `Overseer.sol`
- `HyperCoreUtils.sol`
- `HyperCoreStakingModule.sol`

# 발견 사항

# [M-01] 상환 가능한 자금의 잘못된 관리 (Incorrect management of redeemable funds)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Overseer._redeemable()`은 계약이 소각(burn) 금액에서 이미 상환된 금액과 보류 중인 프로토콜 수수료를 뺀 금액을 충당할 수 있는 충분한 잔액을 가지고 있는지 확인하여 소각을 상환할 수 있는지 확인합니다.

```solidity
    function _redeemable(uint256 burnId) internal view returns (bool) {
        uint256 sum = burns[burnId].sum;
        uint256 difference = sum < redeemed ? 0 : sum - redeemed;
        return burns[burnId].completed == false && difference + protocolPendingFee <= address(this).balance;
    }
```

이 계산은 더 높은 소각 ID를 가진 모든 상환이 이전의 모든 소각이 상환될 수 있도록 충분한 잔액을 남겨두었다고 가정하는데, 이는 정확합니다. 그러나 그 이후에 새로운 수수료가 발생하고 인출되어 초기 소각을 상환하기에 충분하지 않은 잔액이 남을 수 있다는 점은 고려하지 않습니다.

### 개념 증명

```solidity
function test_audit_redeemReverts() public {
	// Setup
	_addStakingModule();
	address[] memory stakingModules = overseer.getStakingModules();
	address stakingModule = stakingModules[0];

	address user1 = makeAddr("user1");
	address user2 = makeAddr("user2");
	address user3 = makeAddr("user3");
	vm.deal(user1, 1 ether);
	vm.deal(user2, 1 ether);
	vm.deal(user3, 1 ether);

	vm.startPrank(gov);
	overseer.setSyncInterval(84_600);
	overseer.grantRole(overseer.FEE_RECIPIENT_ROLE(), address(this));
	vm.stopPrank();

	// Users mint stHYPE
	vm.prank(user1);
	overseer.mint{value: 1 ether}(user1);

	vm.prank(user2);
	overseer.mint{value: 1 ether}(user2);

	vm.prank(user3);
	overseer.mint{value: 1 ether}(user3);

	// Deposit to staking module
	Overseer.DepositInput[] memory inputs = new Overseer.DepositInput[](1);
	inputs[0] = Overseer.DepositInput({index: 0, amount: 1 ether, data: ""});
	vm.prank(gov);
	overseer.deposit(inputs);
	CoreSimulatorLib.nextBlock();

	// Simulate the effect of staking to validators
	stakingModule.call{value: 0.01 ether}("");

	// User 1 burns
	vm.prank(user1);
	uint256 user1BurnId = overseer.burn(user1, 1 ether, "");

	// User 2 burns and redeems
	vm.prank(user2);
	overseer.burnAndRedeemIfPossible(user2, 1 ether, "");

	// Rebase accrues protocol fees
	vm.warp(block.timestamp + 84_600);
	vm.prank(gov);
	overseer.rebase();
	
	// Protocol fees are claimed
	overseer.claimFee(0, true);

	// User 1 burn is supposed to be redeemable
	assertTrue(overseer.redeemable(user1BurnId));

	// Redemption reverts due to lack of funds
	vm.prank(user1);
	vm.expectRevert(bytes("Transfer failed"));
	overseer.redeem(user1BurnId);
	assertTrue(address(overseer).balance < 1 ether);
}
```

## 권장 사항

부분적인 수정은 `_redeemable()` 함수에 `burns[burnId].amount <= address(this).balance` 조건을 추가하는 것입니다. 이렇게 하면 계약이 특정 소각 금액을 충당할 충분한 잔액이 없는 경우 `_redeemable()` 함수가 true를 반환하는 것을 방지할 수 있습니다.

소각 ID의 상환이 항상 이전의 모든 소각이 상환되도록 허용하는 것을 보장하려면, `burns[latestBurnId].sum - redeemed + protocolPendingFee <= address(this).balance`일 때만 인출을 허용하도록 프로토콜 수수료 인출을 조정해야 합니다. 또한 `burnId < latestBurnId`일 때 `protocolPendingFee`는 `_redeemable()` 함수에서 고려해서는 안 됩니다.

# [M-02] 스테이킹 모듈 계정이 활성화되지 않은 경우 입금 실패 가능성 (Deposits might fail if staking module account is not activated)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`deposit()`에서 스테이킹 모듈은 HYPE를 HyperCore의 현물(spot) 잔액으로 보낸 다음 스테이킹 잔액으로 이동합니다. 그러나 스테이킹 모듈 계정이 HyperCore에서 활성화되지 않은 경우(즉, L1에서 자금을 받은 적이 없는 경우) 입금은 조용히 실패합니다.

그 결과:

- 예치된 HYPE는 계정이 활성화될 때까지 현물 잔액에 적립되지 않으므로 전체 잔액에서 사라집니다. 이는 슬래싱(slashing) 이벤트 발생과 유사하게 총 공급량을 예상보다 낮게 만들어, 주가 하락 및 공급이 회복되면 프로토콜이 추가 수수료를 받는 것과 같은 다양한 문제를 일으킵니다.

- 계정이 활성화되면 예치된 HYPE는 현물 잔액에서 사용할 수 있습니다. 이를 위해서는 관리자가 자금을 EVM으로 인출한 다음 다시 예치해야 하는데, 현물 잔액에서 스테이킹 잔액으로 직접 자금을 이동하는 기능이 없기 때문입니다.

## 권장 사항

`PrecompileLib.coreUserExists()` 함수를 사용하여 스테이킹 모듈 계정이 HyperCore에서 활성화되었는지 확인하는 확인을 `addStakingModule()`에 추가하십시오.

# [M-03] 오래된 프리컴파일 데이터로 인한 총 공급량 오계산 가능성 (Outdated precompile data might cause miscalculation of total supply)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`Overseer.rebase()`는 `getNewSupply()` 함수를 호출하여 프로토콜이 소유한 HYPE의 총 공급량을 결정합니다. 이 계산에서 L1의 다른 주소의 잔액을 얻기 위해 다른 프리컴파일(precompiles)이 사용됩니다. 이와 관련하여 이러한 프리컴파일은 **현재 블록의 시작** 시점의 상태를 반환한다는 점에 유의하는 것이 중요합니다.

즉, 같은 블록에서 이전 작업이 발생한 경우 총 공급량이 오계산될 수 있습니다. 이러한 작업에는 스테이킹 모듈 또는 임시 주소(interim address)에 의한 L1 간의 HYPE 이동이 포함됩니다.

그 결과 총 공급량은 슬래싱 이벤트 발생과 유사하게 예상보다 낮을 수 있으며, 이는 주가가 하락하고 공급이 회복되면 프로토콜이 추가 수수료를 받는 등의 다른 결과를 초래할 수 있습니다.

## 권장 사항

- 현재 블록에서 계약이 스테이킹 모듈과 상호 작용한 경우 `rebase()`에서 되돌립니다(revert).
- `interimAddress`에 의한 HYPE 이동과 `rebase()` 호출이 같은 블록에서 발생하지 않도록 조정합니다.

# [M-04] 대규모 공급 증가로 `rebase()`가 차단될 수 있음 (`rebase()` can be blocked by large supply increase)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`Overseer.rebase()`는 마지막 리베이스 이후 계산된 APR 변경이 미리 정의된 임계값(`aprThresholdBps`)을 초과하면 되돌립니다.

```solidity
        int256 aprChange = _calculateApr(timeElapsed, currentSupply, newSupply);

        if (aprChange > 0) {
            if (aprChange + 1 >= int256(aprThresholdBps)) {
                revert AprTooHigh(aprChange + 1, aprThresholdBps);
```

`aprThresholdBps`는 10,000 베이시스 포인트(100%)로 제한되므로 stHYPE 공급이 크게 증가하면 `rebase()` 함수가 오랫동안 또는 영구적으로 차단될 수 있습니다.

예를 들어, 리베이스 간격이 하루인 경우 stHYPE 공급이 0.28% 증가하면 APR이 100%를 초과하여 `rebase()` 함수가 되돌립니다. 리베이스 빈도가 낮을수록 `rebase()` 함수를 차단하는 데 필요한 stHYPE 공급 증가량이 더 작아집니다. 공격자가 해당 금액을 기부해야 하지만, 시간이 지남에 따라 stHYPE 공급이 감소하면(예: 사용자가 토큰을 소각함에 따라) `rebase()` 함수를 차단하는 데 필요한 금액도 감소합니다.

결과적으로 프로토콜은 누적된 스테이킹 보상에 대한 stHYPE 공급을 조정할 수 없게 됩니다.

## 권장 사항

`setAprThresholdBps()` 함수에서 `aprThresholdBps` 매개변수의 최대 값에 대한 제한을 제거하십시오. 이를 통해 거버넌스는 충분히 높은 임계값을 설정하여 `rebase()` 함수가 차단되는 것을 방지할 수 있으며, 이는 큰 `syncInterval` 값과 결합하여 주요 영향을 주지 않고 stHYPE 공급의 높은 증가를 처리할 수 있습니다.

# [L-01] APR이 최대 2 베이시스 포인트까지 과대평가될 수 있음 (APR can be overestimated by up to 2 basis points)

_해결됨_

`Overseer.rebase()`는 내부 함수 `_calculateApr()`을 호출하여 마지막 리베이스 이후 APR 변경을 계산합니다. 이 함수는 반환하기 전에 결과에 1을 더하여 계산된 APR 변경을 반올림합니다. 또한 `rebase()`에서 APR 임계값 확인도 임계값과 비교하기 전에 계산된 APR 변경에 1을 더합니다. 이는 APR이 최대 2 베이시스 포인트까지 과대평가될 수 있음을 의미합니다.

```solidity
    function rebase() public onlyRole(REBASER_ROLE) {
(...)
        int256 aprChange = _calculateApr(timeElapsed, currentSupply, newSupply);

        if (aprChange > 0) {
    @>      if (aprChange + 1 >= int256(aprThresholdBps)) {
                revert AprTooHigh(aprChange + 1, aprThresholdBps);

(...)

    function _calculateApr(uint256 timeElapsed, uint256 currentSupply, uint256 newSupply)
(...)
            uint256 apr =
                (((newSupply * E18) / currentSupply - E18) * E18) / ((timeElapsed * E18) / ONE_YEAR) / (E18 / BIPS);

    @>      return int256(apr + 1);
```

`rebase()` 함수에서 `+ 1` 조정을 제거하는 것이 좋습니다.

# [L-02] 부채 증가로 인한 입금 차단으로 더스트가 남을 수 있음 (Deposit blockage by liability increase may leave dust)

_인정됨_

`Overseer::deposit()`을 사용하면 관리자가 스테이킹 모듈에 HYPE를 입금할 수 있으며, 여기에는 계약이 상환 및 프로토콜 수수료 청구를 이행하기 위한 최소한의 잔액을 포함하는지 확인하는 충분한 확인이 포함되어 있습니다.

```solidity
    function deposit(DepositInput[] calldata inputs) external onlyRole(MANAGER_ROLE) {
        uint256 numStakingModules = _getOverseerStorage().stakingModules.length();

        for (uint256 i = 0; i < inputs.length; i++) {
            // . . .
            uint256 preBalance = address(this).balance;
            // Cannot deposit more than native token balance minus total liability
            require(input.amount + totalLiability() <= preBalance, "Excessive deposit amount");        <<@

            IStakingModule(stakingModule).deposit{value: input.amount}(input.data);

            require(address(this).balance + input.amount >= preBalance, "Unexpected balance decrease");
        }
    }
```

그러나 `deposit()` 호출 직전에 소각 요청이 생성되어 `totalLiability()`가 증가할 수 있고 마지막 호출이 실패하더라도 `deposit()`이 되돌려지기 때문에, 관리자가 잔액을 완전히 활용할 수 없는 상태가 될 수 있습니다. 악의적인 행위자도 입금을 차단하기 위해 동일한 것을 목표로 할 수 있습니다. 
따라서 전체 잔액이 활용되지 않는 상황은 항상 발생할 수 있습니다.

소각 요청이 현재 에포크(epoch)에 영향을 미치지 않는 에포크 기반 상환 시스템을 사용하는 것이 좋습니다.

# [L-03] `aprChange = 0`이면 `rebase()`가 항상 되돌림(revert) (If `aprChange = 0`, `rebase()` will always revert)

_해결됨_

`rebase()`에서 `aprChange > 0`일 때 `aprThresholdBps`를 확인하고 `aprChange <= 0`일 때 `slashThresholdBps`를 확인합니다. 문제는 `_calculateApr()`에서 `newSupply == currentSupply`인 경우 `aprChange`가 0이 될 수 있다는 것입니다.

```solidity
function _calculateApr(uint256 timeElapsed, uint256 currentSupply, uint256 newSupply)
        internal
        pure
        returns (int256)
    {
        if (newSupply > currentSupply) {
            uint256 apr =
                (((newSupply * E18) / currentSupply - E18) * E18) / ((timeElapsed * E18) / ONE_YEAR) / (E18 / BIPS);

            return int256(apr + 1);
        } else {
@>          uint256 supplyDecrease = BIPS - ((newSupply * BIPS) / currentSupply);

            return -int256(supplyDecrease);
        }
    }
```

문서에 언급된 대로 현재 Hyperliquid에서 슬래싱이 아직 활성화되지 않았으므로 `slashThresholdBps`는 `0`으로 설정되어 있습니다. 이로 인해 `aprChange = 0`인 모든 경우는 항상 `SupplyDecreaseTooHigh` 사용자 정의 오류와 함께 되돌려집니다.

```solidity
if (aprChange > 0) {
            if (aprChange + 1 >= int256(aprThresholdBps)) {
                revert AprTooHigh(aprChange + 1, aprThresholdBps);
            }
        } else {
            if (-aprChange >= int256(slashThresholdBps)) {
                revert SupplyDecreaseTooHigh(aprChange);
            }
        }
```

권장 사항: `aprThresholdBps` 확인에 `>=`를 사용하도록 조건을 수정하거나 `slashThresholdBps`를 0이 아닌 값으로 설정하십시오.

# [L-04] `rebase()` 없는 `protocolFee` 변경은 잘못된 수수료를 초래함 (`protocolFee` change without `rebase()` leads to incorrect fees)

_해결됨_

`changeProtocolFee()`는 `rebase()`가 실행될 때 적용되는 수수료를 업데이트하는 데 사용됩니다.

```solidity
function changeProtocolFee(uint256 fee_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        if (fee_ > BIPS) revert InvalidInput();

        protocolFee = fee_;

        emit ProtocolFeeSet(fee_);
    }
```

문제는 수수료를 변경하기 전에 `rebase()`가 호출되지 않는다는 것입니다. 이로 인해 이전 수수료 대신 새 수수료가 적용되어 해당 기간 동안 잘못된 `protocolFee`가 발생합니다.

**권장 사항**

현재 기간에 대해 올바른 수수료가 발생하도록 `protocolFee`를 변경하기 전에 `rebase()`를 호출하십시오. 이렇게 하려면 `DEFAULT_ADMIN_ROLE`도 `REBASER_ROLE`을 가지고 있어야 합니다. 그렇지 않으면 `rebase()` 호출이 되돌려집니다.

```diff
function changeProtocolFee(uint256 fee_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        if (fee_ > BIPS) revert InvalidInput();

+       rebase();

        protocolFee = fee_;

        emit ProtocolFeeSet(fee_);
    }
```

# [L-05] `rebase()`에서 수수료 전에 계산된 APR은 가치를 부풀림 (APR calculated before fees in `rebase()`, inflates value)

_해결됨_

`rebase()`는 스테이커를 위한 새 APR을 계산하고 업데이트하는 데 사용됩니다.

```solidity
function rebase() public onlyRole(REBASER_ROLE) {
        if (sthype.syncEnd() >= block.timestamp) revert SyncNotEnded();

        uint256 timeElapsed = block.timestamp - lastRebaseTime;

        uint256 newSupply = getNewSupply();
        uint256 currentSupply = sthype.totalSupply();

        uint256 protocolFeeIncrease;
        if (newSupply > currentSupply) {
            protocolFeeIncrease = ((newSupply - currentSupply) * protocolFee) / BIPS;
        }

        if (protocolFeeIncrease > 0) {
            protocolPendingFee += protocolFeeIncrease;
        }

@>      int256 aprChange = _calculateApr(timeElapsed, currentSupply, newSupply);

        if (aprChange > 0) {
            if (aprChange + 1 >= int256(aprThresholdBps)) {
                revert AprTooHigh(aprChange + 1, aprThresholdBps);
            }
        } else {
            if (-aprChange >= int256(slashThresholdBps)) {
                revert SupplyDecreaseTooHigh(aprChange);
            }
        }

        if (protocolFeeIncrease > 0) {
@>          newSupply -= protocolFeeIncrease;
        }

        sthype.syncSupply(newSupply, syncInterval);

        lastRebaseTime = block.timestamp;

        emit Rebase(currentSupply, newSupply, syncInterval, aprChange, sthype.balancePerShare(), timeElapsed);
    }
```

문제는 `aprChange`가 수수료가 적용되기 전에 `newSupply`를 사용하여 계산된 APR이 실제보다 높게 나타난다는 것입니다. 결과적으로 사용자는 실제로 받게 될 APR보다 부풀려진 APR을 보게 됩니다.

또한 수수료를 적용하면 더 낮은 값이 나오는데 실제로는 그렇지 않은 경우에도 `aprThresholdBps`에 도달한 것처럼 보일 수 있습니다.

**권장 사항**

이 문제를 해결하려면 먼저 `newSupply`에서 `protocolFeeIncrease`를 뺀 다음 `aprChange`를 계산하십시오.

```diff
function rebase() public onlyRole(REBASER_ROLE) {
        if (sthype.syncEnd() >= block.timestamp) revert SyncNotEnded();

        uint256 timeElapsed = block.timestamp - lastRebaseTime;

        uint256 newSupply = getNewSupply();
        uint256 currentSupply = sthype.totalSupply();

        uint256 protocolFeeIncrease;
        if (newSupply > currentSupply) {
            protocolFeeIncrease = ((newSupply - currentSupply) * protocolFee) / BIPS;
        }

        if (protocolFeeIncrease > 0) {
            protocolPendingFee += protocolFeeIncrease;
        }

+       if (protocolFeeIncrease > 0) {
+           newSupply -= protocolFeeIncrease;
+       }

        int256 aprChange = _calculateApr(timeElapsed, currentSupply, newSupply);

        if (aprChange > 0) {
            if (aprChange + 1 >= int256(aprThresholdBps)) {
                revert AprTooHigh(aprChange + 1, aprThresholdBps);
            }
        } else {
            if (-aprChange >= int256(slashThresholdBps)) {
                revert SupplyDecreaseTooHigh(aprChange);
            }
        }

-       if (protocolFeeIncrease > 0) {
-           newSupply -= protocolFeeIncrease;
-       }

        sthype.syncSupply(newSupply, syncInterval);

        lastRebaseTime = block.timestamp;

        emit Rebase(currentSupply, newSupply, syncInterval, aprChange, sthype.balancePerShare(), timeElapsed);
    }
```
