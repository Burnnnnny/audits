
# Pashov Audit Group에 대하여

Pashov Audit Group은 업계에서 입증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 여러 번 챔피언이 되었거나, 우리와의 감사에서 탁월한 성과를 보였습니다. 우리는 검증되고 의욕적인 인재와만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위해 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

<p><strong>Tangent-labs/tangent-contracts</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, <strong>Bretzel, t.aksoy, btk, pontifex, jesjupyter</strong>가 <strong>Tangent</strong> 검토에 참여했습니다. 총 <strong>23</strong>개의 이슈가 발견되었습니다.</p>

# Tangent에 대하여

<p>Tangent는 사용자가 자산을 예치하고, USG를 빌리고, 포지션을 레버리지하고, 상환하고, 인출하고, 포지션을 마이그레이션하고, 외부 라우팅 계약을 통해 복잡한 청산 또는 잽(zap)을 수행할 수 있는 담보 대출 시장입니다. 담보 회계, 부채 공유(debt-share) 메커니즘, 가격 오라클, 청산 임계값 및 보상 분배를 조정하는 동시에 플래시 민트 기반 레버리지, 다중 자산 잽, 마이그레이터 제어 상태 이동 및 전체 청산 흐름과 같은 고급 작업의 넓은 표면을 노출합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [687d78d70e48d68a3438c9148b015fa9d0904a8e](https://github.com/Tangent-labs/tangent-contracts/tree/687d78d70e48d68a3438c9148b015fa9d0904a8e)<br>&nbsp;&nbsp;(Tangent-labs/tangent-contracts)

**수정 검토 커밋 해시:**<br>• [fc8a8f822d67a7a99ab6e2f6764b643fbd4e1c70](https://github.com/Tangent-labs/tangent-contracts/tree/fc8a8f822d67a7a99ab6e2f6764b643fbd4e1c70)<br>&nbsp;&nbsp;(Tangent-labs/tangent-contracts)

# 범위

- `Collateral.sol`
- `DebtIR.sol`
- `MarketCore.solMarketExternalActions.sol`
- `PauseSettings.sol`
- `BasicERC20Market.sol`
- `ConvexCrvLPMarket.sol`
- `ConvexFxnLPMarket.sol`
- `OracleCryptoSwap.sol`
- `OracleDuoPoolStable.sol`
- `OracleBase.sol`
- `OraclePendlePT.sol`
- `OracleChainlinkWrapper.sol`
- `OracleCoinFromCurveLP.sol`
- `OracleERC4626.sol`
- `PendlePTRouter.sol`
- `TAN.sol`
- `USG.sol`
- `VsTAN.sol`
- `WStable.sol`
- `LightOwnable.sol`
- `LightReentrancyGuardTransient.sol`
- `ZappingUtil.sol`
- `ControlTower.sol`
- `IRCalculator.sol`
- `MarketCreator.sol`
- `Migratoor.sol`
- `RewardAccumulator.sol`
- `ZappingProxy.sol`

# 발견 사항

# [M-01] 잘못된 보상 분배로 인한 낭비 또는 불공정성

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`VsTAN.sol`과 `RewardAccumulator.sol` 둘 다 Synthetix 스타일의 스테이킹 보상 시스템을 구현하지만 `totalSupply (또는 totalCollateral)가 0`일 때 결함이 있습니다.

- 공급이 0인 기간 동안 보상이 추가될 때, 이전 기간의 남은 보상이 새로운 `rewardRate`에 포함되어 배포되지 않은 보상을 낭비하게 됩니다.

```solidity
if (timestamp >= rData.periodFinish) {
    rewardData[rewardToken].rewardRate = amount / ONE_WEEK;
} else {
    uint256 leftover = (rData.periodFinish - timestamp) * rData.rewardRate;
    rewardData[rewardToken].rewardRate = (amount + leftover) / ONE_WEEK;
}
```
`totalSupply = 0`일 때 실제로 배포된 보상은 없습니다(배포할 대상이 없음). 그러나 `leftover`는 마치 이 기간에 대한 보상이 배포되고 있는 것처럼 계산되어 실제로 배포되지 않은 보상을 낭비하게 됩니다.

- 또한 공급이 0인 기간 이후 첫 번째 사용자가 스테이킹할 때, 공급이 0인 기간 동안 `lastUpdateTime`이 업데이트되지 않아 해당 기간의 모든 누적 보상이 첫 번째 스테이커에게 잘못 배포되어 불공정한 이점이 발생합니다.

```solidity
if (_totalSupplyVsTan != 0) {  // or totalCollateral != 0
    rewardData[token].rewardPerTokenStored = _rewardPerToken(token);
    rewardData[token].lastUpdateTime = _lastTimeRewardApplicable(rewardData[token].periodFinish);
}
```

```solidity
return rewardData[_rewardToken].rewardPerTokenStored +
    (((_lastTimeRewardApplicable(...) - rewardData[_rewardToken].lastUpdateTime) * 
      rewardData[_rewardToken].rewardRate * 1e18) / totalSupplyVsTan);
```

1. `totalSupply = 0`일 때 lastUpdateTime은 업데이트되지 않습니다.
2. 첫 번째 사용자가 스테이킹할 때 totalSupply는 업데이트 전이라 여전히 0입니다.
3. `lastUpdateTime`은 여전히 (공급이 0인 기간 전의) 이전 타임스탬프를 반영하며 업데이트되지 않습니다.
4. 스테이크 후 `totalsupply`가 업데이트됩니다.
5. 다음 스테이크/기타 작업에서 `_rewardPerToken()`은 `lastUpdateTime`부터 현재까지의 보상을 계산하며, 여기에는 공급이 0인 전체 기간이 포함되고 이를 첫 번째 사용자에게 배포합니다.

## 권고 사항
공급이 0인 기간은 일관되고 올바르게 처리되어야 합니다.

# [M-02] 담보 확인 누락으로 인한 극단적인 시나리오에서의 손실

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`_liquidate()` 함수는 오라클 가격을 사용하여 `collatValue`를 계산하고 청산인이 `USGToRepay + fee`를 소각하도록 요구하지만, 담보 가치가 부채 상환을 커버하기에 충분한지 확인하지 않습니다.

`minUSGOut`은 스왑 및 청산 수수료에 대한 슬리피지 보호를 제공하지만, 오라클 기반 `collatValue`가 `USGToRepay`보다 작은 시나리오에 대해서는 보호하지 않습니다.

극단적인 가격 변동이나 오라클이 오래된 경우(staleness), 청산인은 담보를 판매하여 받는 것보다 더 많은 USG를 소각해야 할 수 있어 손실이 발생합니다. 프로토콜은 `minCollatValue` 확인을 추가하여 청산인을 보호하거나, `collatValue < USGToRepay`일 때 청산을 거부하고 대신 `seizeCollateral()`로 라우팅해야 합니다.

```solidity
if (collatValue > USGToRepay) {
    // Fee is taken on the liquidation profits
    uint256 delta = collatValue - USGToRepay;
    fee = (liquidationFee * delta) / DENOMINATOR;
}
```
`collatValue < USGToRepay`일 때 보호가 없음을 관찰할 수 있습니다. 이로 인해 청산인은 극단적인 가격 변동 시나리오에서 가격 차이의 위험을 감수해야 할 수 있습니다.

## 권고 사항

청산인을 보호하기 위해 최소 담보 가치 확인을 추가하거나 `collatValue`가 항상 `USGToRepay`보다 커야 한다고 요구하십시오.

# [M-03] USDT 토큰 지원 실패

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

PendlePTRouter 계약은 메인넷에서 USDT와 같은 토큰과 상호 작용할 때 실패합니다.

이는 내부 함수(_approveIfNotAllowed 및 _transferFrom)가 원시(raw) ERC20 호출(token.approve(...) 및 token.transferFrom(...))을 사용하기 때문에 발생합니다.
USDT와 같은 토큰은 성공 시 부울 값을 반환하지 않습니다. Solidity가 이러한 함수를 호출하면 트랜잭션이 되돌려지며 비표준 동작을 실패로 처리합니다.

## 권고 사항

내부 헬퍼 함수의 원시 호출을 SafeERC20 버전으로 대체하십시오.

# [L-01] WStable mint/burn이 ERC4626 preview 함수를 오용함

_해결됨_

`WStable` 계약은 `_savingAccount`와 상호 작용할 때 `mint()` 및 `burn()`의 변환을 위해 ERC4626의 `previewMint()` 및 `previewWithdraw()` 함수를 오용합니다. 이러한 preview 함수는 원래 볼트를 보호하기 위해 올림(round up)을 수행하지만(사용자에게 불리함), WStable 계층에서는 이 보호가 예상대로 작동하지 않습니다. 대신, mint/burn 주기에 걸쳐 최대 1 wei 더 많은 가치를 허용함으로써 의도치 않게 WStable 사용자에게 유리하게 작용합니다.

- `mint()` (`isSaving == true`): `_savingAccount.previewMint(amountIn)`은 예치된 주식(shares)에 필요한 자산의 올림된 추정치를 사용합니다. 사용자는 주식의 실제 가치보다 최대 1 wei의 추가 WStable 가치를 받을 수 있습니다.

- `burn()` (`isSaving == true`): `_savingAccount.previewWithdraw(amount)`는 소각된 금액에 대한 주식의 올림된 추정치를 사용합니다. 사용자는 필요한 것보다 최대 1 wei 더 많은 주식을 얻을 수 있습니다.

주식에 기초 자산이 발생하므로 모든 사용자가 `burn()` (`isSaving == true`)으로 종료할 수 있어 영향은 발견되지 않았습니다.

정확한 평가를 위해 표준 변환 함수로 대체하십시오.

# [L-02] 잠금 해제 시 1만큼 차이(Off-by-one): `== endLockTime`은 잠금 해제할 수 없음

_해결됨_

`VsTAN.unlock`에서 가드는 엄격한 `<`를 사용합니다:

```solidity
require(endLockTime < block.timestamp, LockNotOver());
```

이는 소유자가 **정확한 만료 시점**(`block.timestamp == endLockTime`)에 잠금을 해제하는 것을 방지하여 사실상 1블록 대기를 강제합니다.

만료 시 잠금 해제를 허용하도록 비엄격 확인으로 전환하십시오:

```solidity
require(endLockTime <= block.timestamp, LockNotOver());
```

# [L-03] 킥(Kick) 인센티브가 0으로 반올림될 수 있음 (작은 잠금에 대한 동기 부여 없음)

_해결됨_

`VsTAN.kickPosition`에서 인센티브는 정수 나눗셈으로 계산됩니다:

```solidity
uint256 kickIncentivization = (_kick.percentage * amount) / 100_000;
```

작은 `amount`나 낮은 `percentage`의 경우 이는 `0`으로 반올림되어 키커(kicker)에게 보상을 지급하지 않고 활성도(liveness)를 줄입니다(포지션이 지연 시간을 지나 남아 있음).

최소 포지션 및 인센티브 강제:

* `minLockAmount` 및 `minKickIncentive` 유지 (wei TAN). 새로운/수정된 잠금은 `amount ≥ minLockAmount`를 충족해야 합니다.
* 구성 업데이트 시 `(kick.percentage * minLockAmount) / 100_000 ≥ minKickIncentive`를 강제하십시오 (그렇지 않으면 되돌림).

# [L-04] 차입 경로의 이중 바닥(Double-floor) 반올림이 사용자에게 약간 유리함

_해결됨_

차입 흐름에서 두 변환 모두 내림(floor) 반올림을 사용하는데, 이는 (a) 현재 부채를 약간 과소평가하고 (b) 동일한 차입 금액에 대해 약간 더 적은 부채 주식을 발행합니다(작업당 ≤1 wei USG). 경제적으로는 무시할 수 있지만 이 편향은 체계적입니다.

```solidity
    function _borrow(address receiver, uint256 USGToBorrow, uint256 collatAmount, bool isLeverage) internal returns (uint256, uint256) {
        _verifyIsBorrowNotPaused();
        _verifyDebtInputNotZero(USGToBorrow);
        uint256 newDebtIndex = _checkpointIR();

        uint256 _userDebtShares = userDebtShares[msg.sender];

>>      uint256 newUserDebt = USGToBorrow + _convertToAmount(_userDebtShares, newDebtIndex); 

        //  Cache the new value in USG of the debt
>>      uint256 newUserDebtShares = _convertToShares(USGToBorrow, newDebtIndex);
<..>

    function _convertToAmount(uint256 debtShares, uint256 index) internal pure returns (uint256) {
        return _mulDiv(debtShares, index, RAY);
    }
<..>
    function _convertToShares(uint256 debt, uint256 index) internal pure returns (uint256) {
        return _mulDiv(debt, RAY, index);
    }    
```
`amount`는 내림으로 유지하되 부채 주식은 **올림(ceil)**으로 발행(예: `shares = mulDivRoundingUp(amount, RAY, index)`)하거나 OZ의 `Math.mulDiv(amount, RAY, index, Math.Rounding.Up)`를 사용하여 대칭적이고 프로토콜 친화적인 회계를 보장하십시오.

# [L-05] "실패 없음(No-fail)" 경로가 폴백 없이 기본(primary) 실패 시 유효하지 않은 가격을 반환할 수 있음

_해결됨_

`OracleChainlinkWrapper`의 폴백 없는 "실패 없음" 분기에서 래퍼는 **0**을 포함한 원시(유효하지 않은) 가격을 반환할 수 있으며, 이는 청산 로직을 왜곡할 수 있습니다.

0/유효하지 않은 값을 반환하지 마십시오. 양의 가격(및 최신성)을 강제하거나 캐시된 마지막 양호한 값을 사용하십시오. 그렇지 않으면 되돌리십시오.

# [L-06] `irParam.pMax`가 `irParam.pMin`과 같을 때 `IRCalculator`의 0으로 나누기 위험

_해결됨_

이자율 구성을 통해 `irParam.pMax`가 `irParam.pMin`과 같을 수 있습니다. 스프레드 `(irParam.pMax - irParam.pMin)`로 나누는 모든 계산은 0으로 나누기로 인해 되돌려집니다:

```solidity
            ((uint256(irParam.pMax) * E12) - USGPrice) / (irParam.pMax - irParam.pMin), 
```

이는 거버넌스가 매개변수를 업데이트할 때까지 이자율 계산기에 의존하는 작업을 일시적으로 DoS할 수 있습니다. 이러한 매개변수는 초기화 후 변경 가능하기 때문입니다.

```solidity
    function _verifyIRParams(IRParams calldata _irParam) internal pure {
        require(_irParam.a1 <= 20_000, A1TooBig());
        require(_irParam.a2 <= 20_000, A2TooBig());
        require(_irParam.k <= 20_000, KTooBig());
        require(_irParam.rMax <= 400_000, RMaxTooBig());
        require(_irParam.rMin <= _irParam.rMax, RMinBiggerThanRMax());
        require(_irParam.pMin <= _irParam.pInf, PMinBiggerThanPInf());
        require(_irParam.pInf <= _irParam.pMax, PInfBiggerThanPMax());
        require(_irParam.pMax <= 1_000_000, PMaxBiggerThanOneDollar());
    }
```

권고 사항: 구성 시 엄격한 부등식을 강제하십시오: 초기화 프로그램에서 `irParam.pMax > irParam.pMin`을 요구하십시오.

# [L-07] `MarketCore` 초기화 및 `Collateral`에서 충돌하는 청산 수수료 제한

_해결됨_

`MarketCore.initialize()`는 허용적인 상한선(~80%)에 대해 `liquidationFee`를 검증합니다:

```solidity
        require(_marketInit.liquidationFee < 80_000, LiquidationFeeTooHigh());
```

반면 `Collateral` 계층은 15%의 더 엄격한 상한선을 강제합니다:

```solidity
        require(_liquidationFee < 15_000, LiquidationFeeTooHigh());
```

이 불일치로 인해 `MarketCore.initialize()`는 통과하지만 `Collateral`이 설정한 의도된 프로토콜 동작을 위반하는 `liquidationFee` 값으로 시장이 초기화될 수 있습니다.

권고 사항: `liquidationFee <= 15_000`을 강제하여 `MarketCore.initialize()` 검증을 더 엄격한 불변성과 일치시키십시오.

# [L-08] 1만큼 차이(Off-by-one) 한도로 인해 `Collateral`에서 최대 허용 청산 수수료 설정 불가

_해결됨_

`Collateral` 기본 계약은 `liquidationFee`에 상한선을 강제하지만, 이 상한선은 엄격한 미만(less-than) 확인을 사용하여 수수료를 의도한 최대값(15%)으로 설정하는 것을 방지합니다:

```solidity
    function setLiquidationFee(uint256 _liquidationFee) external onlyOwner {
        require(_liquidationFee < 15_000, LiquidationFeeTooHigh());
        liquidationFee = _liquidationFee;
    }
```

결과적으로 거버넌스는 `liquidationFee`를 정확히 15%로 구성할 수 없으며, 문서화된 제한 내에 있음에도 불구하고 이를 시도할 때 되돌림이 발생합니다.

권고 사항:
- 검증을 다음과 같이 변경하십시오:

```solidity
        require(_liquidationFee < 15_000, LiquidationFeeTooHigh());
```

# [L-09] `maxWithdraw()`가 최댓값을 반환하면 `claimRewards()`에서 무제한 발행(minting)

_해결됨_

`WStable.sol`의 `claimRewards()` 함수는 보상을 계산하기 위해 기본 ERC4626 볼트의 `maxWithdraw()`를 사용합니다.

그러나 많은 ERC4626 구현은 인출 제한이 없을 때 `uint256.max`를 반환하는데, 이로 인해 `claimRewards()`가 수수료 금고(treasury)에 무제한의 토큰을 발행하게 됩니다. `maxWithdraw()`가 `uint256.max`를 반환하면 `totalStableStaked - dueAmount` 계산 결과가 uint256.max에 가까운 값이 되어 엄청난 토큰 인플레이션과 토큰 경제 모델의 완전한 파괴를 초래합니다.

```solidity
    function mint(uint256 amountIn, address receiver, bool isSaving) public {
        require(amountIn != 0, ZeroAmount());

        uint256 amountToMint = amountIn;

        if (isSaving) {
            IERC4626 _savingAccount = savingAccount;
            amountToMint = _savingAccount.previewMint(amountIn);
            _savingAccount.transferFrom(msg.sender, address(this), amountIn);
        } else {
            stable.transferFrom(msg.sender, address(this), amountIn);
            savingAccount.deposit(amountIn, address(this));
        }

        // Mints the amount of WStable for the receiver
        _mint(receiver, amountToMint);
    }
```

따라서 이 함수는 많은 ERC4626 볼트와 호환성 문제가 있을 수 있으며, 이에 유의해야 합니다.

# [L-10] 중요한 설정자(setter) 함수에 매개변수 변경에 대한 이벤트 방출 누락

_해결됨_

중요한 프로토콜 매개변수를 수정하는 여러 `setter/update` 함수가 값이 변경될 때 이벤트를 방출하지 않습니다. 이로 인해 오프체인에서 매개변수 변경을 추적하고, 거버넌스 조치를 모니터링하고, 프로토콜 구성 업데이트의 감사 추적을 유지하기 어렵습니다.

다음 예를 보십시오:

```solidity
function setCollatOracle(IPriceOracle _collatOracle) external onlyOwner {
    collatOracle = _collatOracle;
}
```

```solidity
function setMaxLTV(uint256 _maxLTV) external onlyOwner {
    require(_maxLTV < liquidationThreshold, MaxLTVBiggerThanLiquidationThreshold());
    maxLTV = _maxLTV;
}
```

```solidity
function setMaxMarketDebt(uint256 _maxMarketDebt) external onlyOwner {
    maxMarketDebt = _maxMarketDebt;
}
```

# [L-11] `updateRCParams()`의 수수료 계산 불일치

_인정됨_

updateRCParams()가 호출되면 함수는 보류 중인 보상을 처리하기 전에 rcParams[market]을 업데이트합니다.
결과적으로 processRewards()는 다음을 사용합니다:

- 새로운 harvestFeePercentage,
- (lastRewardCuts[market]의) 이전 rewardCutPercentage.

```solidity
        RCParams memory _rcParams = rcParams[market];

        // Computes and actulize the new reward cut
        lastRewardCuts[market] = _calculateRC(USGOracle.price_w(), _rcParams);

        uint16 harvestFeePercentage = _rcParams.harvestFeePercentage;
```

이로 인해 수확자(harvester)의 수수료는 새 매개변수에 따라 청구되는 반면 보상 컷은 이전 구성을 기반으로 하는 일시적인 불일치가 발생합니다.

**권고 사항**

보상 컷과 수확자 수수료 모두 이전 매개변수 세트에 대해 계산되도록 하십시오.

# [L-12] 기본 ERC4626 주가 하락 시 백킹(backing) 손실 위험

_인정됨_

WStable 계약은 기본 ERC4626 볼트(savingAccount)의 가치가 상승(수익 발생)하기만 한다고 가정합니다. 그러나 손실, 디페깅 또는 기타 위험으로 인해 볼트의 주식 대 자산 비율이 감소하면 WStable의 회계가 기본 자산과 일치하지 않게 됩니다.

발행(minting) 중 계약은 현재 ERC4626 환율에 관계없이 예치된 스테이블 토큰에 대해 1:1로 WStable을 발행합니다. 그러나 savingAccount의 주가가 떨어지면 모든 미지불 WStable 토큰을 커버할 만큼 자산이 충분하지 않을 수 있습니다. 손실 후 인출하거나 상환하려는 사용자는 전액을 인출하지 못할 수 있습니다.

예:

> 초기: 100 주식 = 110 자산 → 사용자가 110 자산을 예치하고 110 WStable을 받음.
>
> 나중: 볼트 비율이 100 주식 = 100 자산으로 하락 → 총 공급 = 110 WStable이지만 100 자산만 사용 가능.
>
> 인출을 시도하는 사용자는 되돌려짐(revert)

마이너스 수익에 대한 명시적 처리를 구현하거나 기본 볼트 주식 비율이 감소하지 않도록 하십시오.

# [L-13] 부분 청산 중 슬리피지 보호 실패

_해결됨_

청산 함수는 과도한 슬리피지로부터 청산인을 보호하기 위해 maxUSGToBurn 및 minUSGOut 매개변수를 사용합니다. 그러나 호출자의 트랜잭션이 실행되기 전에 다른 부분 청산이 발생하면 이러한 보호가 효과가 없을 수 있습니다. 구체적으로 함수는 다음과 같이 청산 금액을 조정합니다:
```solidity
if (collatAmountToLiquidate >= liquidateInput._collateralBalance) {
    collatAmountToLiquidate = liquidateInput._collateralBalance;
    USGToRepay = liquidateInput.userDebt;
}
```
이전 청산으로 인해 사용자의 담보가 이미 감소한 경우 collatAmountToLiquidate는 더 적은 금액으로 제한되는 반면, 호출자의 maxUSGToBurn은 오래된 상태(청산 전 비율)를 기준으로 유지됩니다.

결과적으로:
- maxUSGToBurn은 더 이상 담보 단위당 실제 상환 금액을 정확하게 제한하지 않아 청산인이 초과 지불하는 것을 방지하지 못할 수 있습니다.

- 예상보다 적은 담보가 청산되어 더 적은 출력 금액으로 이어지므로 minUSGOut이 실패할 수 있습니다.

총 USG 금액뿐만 아니라 담보 단위에 비례하여 슬리피지 보호를 구현하십시오.

# [L-14] 부분 청산에서 반올림으로 인한 상환 0 위험

_해결됨_

_liquidate() 함수에서 다음 줄은 정수 나눗셈을 수행합니다:
`USGToRepay = (collatAmountToLiquidate * liquidateInput.userDebt) / liquidateInput._collateralBalance;`

Solidity 정수 나눗셈은 0을 향해 절삭하므로 매우 작은 collatAmountToLiquidate 값은 USGToRepay가 0이 되게 할 수 있습니다.
이는 담보 잔액을 줄이지만 실제로 부채를 상환하지 않는(또는 부채 주식을 제거하지 않는) 청산 호출을 허용할 수 있습니다.
이러한 시나리오는 일반적으로 수익성이 없지만 담보 가격이 매우 높은 경우 작은 담보 금액이라도 한계 이익을 낼 수 있으므로 이 엣지 케이스를 방어할 가치가 있습니다.

진행하기 전에 결과 debtSharesToRemove가 0이 아닌지 확인하는 온전성 확인(sanity check)을 추가하십시오.

# [L-15] 인출 수수료 무시로 인한 OracleERC4626의 위험

_인정됨_

OracleERC4626의 현재 구현은 ERC4626 함수 convertToAssets(1e18)를 사용하여 주당 기본 자산 금액을 결정합니다.
이 접근 방식은 인출 또는 상환 수수료를 고려하지 않아 잠재적으로 볼트 토큰의 실제 상환 가치를 과대평가할 수 있습니다. convertToAssets() 함수는 수수료나 슬리피지를 제외하고 주식과 자산 간의 수학적 변환을 제공합니다. 따라서 가격 오라클에서 이를 사용하면 수수료가 없는 볼트를 가정하므로 가격 보고가 부풀려질 수 있습니다.

상환 수수료를 정확하게 반영하기 위해 변환 호출을 previewRedeem()으로 대체하십시오.

# [L-16] `startCutPrice`에 대한 잘못된 단위 검증

_해결됨_

RewardAccumulator 계약은 startCutPrice 및 endCutPrice 매개변수를 1e18에 대해 검증하는데, 이는 이러한 값이 18자리 정밀도여야 함을 의미합니다.
`        require(_rcParam.startCutPrice <= 1e18, StartCutPriceTooHigh());`

그러나 _calculateRC 함수에서 두 값 모두 1e12를 곱합니다. 이는 구현이 실제로 6자리 가격으로 저장될 것으로 예상함을 의미합니다. 이 불일치는 상한 검증 확인(<= 1e18)이 잘못된 스케일에 적용되어 지나치게 큰 입력 값을 허용하고 의도한 보상 컷 곡선을 깨뜨림을 의미합니다.

# [L-17] 총 공급이 0인 상태에서 `processRewards`가 호출될 때 남은 보상 계산 오류

_해결됨_

VsTAN 및 RewardAccumulator 계약 모두의 processRewards 함수에서 totalSupplyVsTan == 0(즉, 활성 포지션 없음)인 동안 다시 호출되면 계약은 이전 보상이 완전히 배포되었다고 가정하고 새로운 rewardRate를 설정합니다. 그러나 _updateReward는 총 공급이 0일 때 업데이트를 건너뛰므로 실제로는 보상이 배포되지 않습니다.

`uint256 leftover = (rData.periodFinish - timestamp) * rData.rewardRate;`

이는 타임스탬프까지 모든 이전 보상이 올바르게 스트리밍되었다고 가정하지만 스테이커가 없었다면 부정확합니다. 결과적으로 경과된 기간에 해당하는 일부 토큰이 계약에 효과적으로 잠기게 되어 청구할 수 없게 됩니다.

남은 미배포 보상을 계산하기 위해 현재 타임스탬프가 아닌 마지막 업데이트 이후의 시간을 사용하십시오.

# [L-18] 전역적, 가격 중심 보상 컷을 통한 악성 부채의 시장 간 사회화

_인정됨_

`RewardAccumulator`가 적용하는 보상 컷은 USG 가격만의 함수입니다:

```solidity
// RewardAccumulator.processRewards / processMultiRewards
uint256 USGPrice = USGOracle.price_w();
lastRewardCuts[market] = _calculateRC(USGPrice, rcParams[market]); // global, price-based
```

그리고 `_calculateRC` 자체는 시장의 `badDebt` 상태가 아니라 오직 `USGPrice` 임계값/단계에만 의존합니다. 결과적으로 한 시장이 악성 부채를 발생시켜 USG 가격을 끌어내리면 증가된 보상 컷이 모든 시장에 적용되어 사실상 관련 없는 시장 전체에 비용을 사회화합니다. 이는 특정 시장의 "이자 및 수익이 악성 부채를 상환하는 데 사용될 수 있다"는 문서의 진술과 모순되며, 전역적 희석보다는 목표 회복을 시사합니다 (https://docs.tangent.finance/docs/usg/health#bad-debt).

**권고 사항**

악성 부채 회복을 전역 가격 신호에서 분리하십시오:

* 컷에 시장별 구성 요소를 추가하십시오 (예: `cut = max(cutByPrice(USG), cutByDebtRatio(market))`), 여기서 `cutByDebtRatio`는 해당 시장의 `badDebt`/담보 가치에 따라 상승합니다.
* `processRewards`에서 변환 후 시장의 몫을 배정하고 `badDebt == 0`이 될 때까지 전용 훅(예: `market.repayBadDebtFromRewards(amountUSG)`)을 호출하십시오. 단일 시장의 손실로 인해 다른 시장의 보상이 영향을 받지 않도록 하십시오.

# [L-19] USG가 프리미엄으로 거래될 때 청산 수익성 저하

_인정됨_

시장은 건강 요인(health-factor) / 차입 한도 경로에서 USG 표시 부채를 USG당 $1로 평가하는 반면, 청산인은 시장 가격(P > 1)으로 USG를 조달해야 합니다. 이는 청산 마진을 압박하고:
(profit = gamma - (P - 1) - (fees && slippage))
수중 포지션(underwater positions)에 대해서도 청산을 비경제적으로 만들 수 있습니다(레버리지 루프의 경우 더 나쁨).

```solidity
    function _healthRatio(uint256 userDebt_, uint256 collateralBalance, uint256 collatPrice) internal view returns (uint256) {
        if (userDebt_ != 0) {
>>          return (collateralBalance * 10 ** (18 - collatDecimals) * collatPrice * liquidationThreshold) / (userDebt_ * DENOMINATOR);
        }
        return MAX_UINT; // Fully healthy if no debt
    }
```

**권고 사항**

HF/한도 및 청산 수학에서 부채를 평가할 때 유효 USG 가격을 고려하여 USG 프리미엄 동안 청산 인센티브가 양수를 유지하도록 하십시오. 예: (P_eff = max(USG_TWAP, 1)) 사용.

# [L-20] `ZappingProxy`가 ETH 환불을 받을 수 없어 잽(zap) 실패 초래

_해결됨_

`ZappingProxy`는 `zapProxy()` @ZappingProxy.sol을 통해 사용자가 제공한 자산과 호출 데이터를 임의의 유동성 라우터로 전달하는 라우팅 심(shim) 역할을 합니다. 잽 경로가 ETH를 입력 자산으로 사용하는 경우 `msg.value`가 라우터로 전달되고, 프록시는 나중에 남은 ETH를 `controlTower.feeTreasury()`로 스윕(sweep)하려고 시도합니다.

```solidity
        } else {
            balanceTokenInLeft = address(this).balance;
            if (0 != balanceTokenInLeft) {
                (bool isSuccess, ) = payable(controlTower.feeTreasury()).call{value: balanceTokenInLeft}("");
                require(isSuccess, ZapCallError(""));
            }
        }
```

그러나 `ZappingProxy`는 `receive()`나 `fallback()` 함수를 제공하지 않으므로 라우터가 프록시로 ETH를 환불하려고 시도하면 라우터 호출이 즉시 되돌려집니다. 따라서 라우터가 잔돈을 반환하려고만 했음에도 불구하고 전체 잽이 실패합니다. 이는 이전에 보고된 "sherlock의 M-5"를 재현하며 알려진 문제가 해결되지 않았음을 보여줍니다.

**권고 사항**

라우터 통합이 되돌림 없이 남은 자금을 반환할 수 있도록 지불 가능한(payable) `receive()` 함수를 구현하십시오.

