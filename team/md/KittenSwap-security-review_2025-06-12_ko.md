# 정보

Pashov Audit Group은 이 분야 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 숙련된 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**KittenSwap/contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# KittenSwap 정보

KittenSwap은 LayerZero 및 hyperlane 래핑 브리지 토큰을 지원하는 HyperEVM에 배포된 맞춤형 게이지 및 팩토리 수정 사항이 있는 DEX입니다.
이 감사는 투표 에스크로 토큰 잠금 시스템, 투표자 제어 게이지 가중치 분배, 외부 뇌물 보상 메커니즘 및 집중 유동성 풀 팩토리 관리에 중점을 두었습니다.

# 위험 분류

| 심각도               | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음**   | 치명적     | 높음           | 중간      |
| **가능성: 중간** | 높음         | 중간         | 낮음         |
| **가능성: 낮음**    | 중간       | 낮음            | 낮음         |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 해를 끼칩니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 공격 경로가 온체인 조건을 모방하는 합리적인 가정 하에 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정해야 함

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [65c8bdd6df9cc63cc2654a7329cee8591a79770a](https://github.com/Kittenswap/contracts/commit/65c8bdd6df9cc63cc2654a7329cee8591a79770a)

_수정 검토 커밋 해시_ - [83798b6c99f61c87ac800e92624cf363a649ee22](https://github.com/Kittenswap/contracts/commit/83798b6c99f61c87ac800e92624cf363a649ee22)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `CLGauge`
- `CLGaugeFactory`
- `Gauge`
- `GaugeFactory`
- `RebaseReward`
- `Reward`
- `VotingReward`
- `VotingRewardFactory`
- `Kitten`
- `Minter`
- `Voter`
- `VotingEscrow`

# 발견 사항

# [C-01] 잘못된 토큰 처리로 인해 `RebaseReward` 실패

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`RebaseReward`는 토큰 ID로 획득한 보상을 `VotingEscrow` 계약에 예치합니다.

```solidity
	if (reward > 0) {
		veKitten.deposit_for(_tokenId, reward);
		emit ClaimReward(_period, _tokenId, _token, _owner);
	}
```

`notifyRewardAmount()` 함수는 Kitten 토큰에 대한 보상만 허용하도록 재정의되지만, `incentivize()`는 여전히 누구나 화이트리스트에 있는 모든 토큰의 보상을 보낼 수 있도록 허용합니다.

결과적으로 Kitten 이외의 토큰 보상은 `VotingEscrow` 계약에 Kitten을 잘못 예치하게 됩니다.

이로 인해 일부 사용자는 다른 사용자에 해당하는 Kitten 보상을 받게 되며, `incentivize()` 함수로 예치된 토큰 보상은 계약에 잠기게 됩니다.

### 개념 증명

`TestRebaseReward.t.sol` 파일에 다음 코드를 추가하고 `forge test -f https://rpc.hyperliquid.xyz/evm --mt test_audit_RebaseRewardBrokenRewards --mc TestRebaseReward`를 실행하십시오.

```solidity
function test_audit_RebaseRewardBrokenRewards() public {
	test_CreateGauge();
	CLGauge _gauge = CLGauge(gauge.get(poolList[0]));
	RebaseReward _rebaseReward = RebaseReward(voter.rebaseReward());
	address user1 = userList[0];
	address user2 = userList[1];
	uint256 kittenAmount = 1 ether;

	vm.startPrank(address(voter));
	// user1 votes
	uint256 tokenId1 = veKitten.tokenOfOwnerByIndex(user1, 0);
	_rebaseReward._deposit(1 ether, tokenId1);

	// user2 votes
	uint256 tokenId2 = veKitten.tokenOfOwnerByIndex(user2, 0);
	_rebaseReward._deposit(1 ether, tokenId2);
	vm.stopPrank();

	// user1 (or anyone else) sends 1e18 token0 as reward to RebaseReward
	deal(address(_gauge.token0()), user1, 1 ether);
	vm.startPrank(address(user1));
	_gauge.token0().approve(address(_rebaseReward), 1 ether);
	_rebaseReward.incentivize(address(_gauge.token0()), 1 ether);
	vm.stopPrank();

	// notify reward after epoch ends/star of next epoch
	vm.warp(ProtocolTimeLibrary.epochNext(block.timestamp));
	vm.startPrank(address(minter));
	kitten.mint(address(minter), kittenAmount);
	kitten.approve(address(_rebaseReward), kittenAmount);
	_rebaseReward.notifyRewardAmount(address(kitten), kittenAmount);
	vm.stopPrank();

	// user1 claims reward
	(int128 veKittenBalBefore, ) = veKitten.locked(tokenId1);
	vm.prank(user1);
	_rebaseReward.getRewardForTokenId(tokenId1);
	(int128 veKittenBalAfter, ) = veKitten.locked(tokenId1);

	// all Kitten rewards have been claimed by user1
	uint256 user1Claimed = uint256(uint128(veKittenBalAfter - veKittenBalBefore));
	assertEq(user1Claimed, kittenAmount);

	// user2 tries to claim reward, but it reverts, as there is no Kitten left
	vm.prank(user2);
	vm.expectRevert();
	_rebaseReward.getRewardForTokenId(tokenId2);
}
```

## 권장 사항

`RebaseReward`의 `incentivize()` 함수를 Kitten에 대한 보상만 허용하도록 재정의하십시오.

또 다른 옵션은 모든 토큰의 보상을 수락하고 이러한 토큰에 대한 보상을 올바르게 처리하도록 `_getReward()`를 조정하는 것입니다.

# [C-02] `lastMintedPeriod` 업데이트 부족으로 Kitten 무제한 발행 허용

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`Minter.updatePeriod()`는 기간당 한 번 `RebaseReward` 및 `Voter` 계약을 위해 Kitten 토큰을 발행하기 위한 것입니다. 그러나 이 함수는 `lastMintedPeriod` 변수 업데이트를 놓칩니다. 결과적으로 첫 번째 기간이 지나면 다음 조건이 항상 참이 됩니다.

```solidity
        if (currentPeriod > lastMintedPeriod) {
```

이로 인해 Kitten 토큰을 무기한 발행할 수 있어 Kitten 토큰 공급이 무제한으로 늘어납니다.

### 개념 증명

`TestVoter.t.sol` 파일에 다음 코드를 추가하고 `forge test -f https://rpc.hyperliquid.xyz/evm --mc TestVoter --mt test_audit_unlimitedMinting`을 실행하십시오.

```solidity
    function test_audit_unlimitedMinting() public {
        test_Vote();
        vm.warp(block.timestamp + 1 weeks);

        uint256 totalSupplyBefore = kitten.totalSupply();
        for (uint256 i; i < 10; i++) {
            minter.updatePeriod();
            uint256 totalSupplyAfter = kitten.totalSupply();
            assertGt(totalSupplyAfter, totalSupplyBefore);
            totalSupplyBefore = totalSupplyAfter;
        }
    }
```

## 권장 사항

```diff
            emit Mint(
                msg.sender,
                emissions,
                circulatingSupply(),
                _tailEmissions
            );
+
+           lastMintedPeriod = currentPeriod;

            return true;
```

# [C-03] 인센티브 보상 도난 가능성

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

VotingReward에서 투표자는 투표로 인해 일부 보상을 받을 수 있습니다. VotingReward에는 두 가지 종류의 가능한 보상이 있습니다.

1. 투표자의 Kitten 토큰.
2. 사용자가 `incentivize` 함수를 통해 추가할 수 있는 인센티브 토큰.

사용자가 `incentivize` 함수를 통해 일부 보상을 인센티브로 제공하면 다음 기간에 이러한 보상을 기록합니다. 사용자는 관련 풀에 투표하여 일부 보상을 받을 수 있습니다.

여기서 문제는 `getRewardForPeriod` 함수에서 `_period` 유효성 검사를 하나 놓쳤다는 것입니다. 사용자는 미래 기간의 보상을 받을 수 있습니다.

아래 시나리오를 고려해 보겠습니다.

1. Alice가 다음 기간(다음 기간 = X)에 대해 500 USDC를 인센티브로 제공합니다.
2. Bob이 첫 번째 투표자로서 관련 풀에 투표합니다.
3. Bob은 기간 X에 대한 보상을 즉시 받습니다. 현재 Bob은 이 기간에 이 풀에 투표한 유일한 사람입니다. Bob은 모든 보상을 받게 됩니다.

```solidity
    function incentivize(
        address _token,
        uint256 _amount
    ) external virtual nonReentrant {
        // Here we have one white list for token. So we cannot manipulate the token.
        if (voter.isWhitelisted(_token) == false)
            revert NotWhitelistedRewardToken();
        uint256 currentPeriod = getCurrentPeriod() + 1;
        uint256 amount = _addReward(currentPeriod, _token, _amount);
    }
    function _deposit(uint256 _amount, uint256 _tokenId) external onlyVoter {
        uint256 nextPeriod = getCurrentPeriod() + 1;

        tokenIdVotesInPeriod[nextPeriod][_tokenId] += _amount;
        totalVotesInPeriod[nextPeriod] += _amount;
    }
    function getRewardForPeriod(
        uint256 _period,
        uint256 _tokenId,
        address _token
    ) external nonReentrant {
        // Only owner or approved can get rewards.
        if (!veKitten.isApprovedOrOwner(msg.sender, _tokenId))
            revert NotApprovedOrOwner();
        _getReward(_period, _tokenId, _token, msg.sender);
    }
```

## 권장 사항

입력 매개변수 확인을 하나 추가하여 미래 기간의 보상을 받지 못하도록 하십시오.

# [H-01] `Gauge`에 `votingReward`가 설정되지 않음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`Gauge` 계약은 초기화 중에 `votingReward`를 설정하지 않으며 이에 대한 설정자 함수도 없습니다.

결과적으로 `notifyRewardAmount()`가 호출되고 쌍에서 수수료가 청구되면 트랜잭션이 되돌려집니다.

```solidity
        (claimed0, claimed1) = IPair(address(lpToken)).claimFees();
        (address _token0, address _token1) = IPair(address(lpToken)).tokens();
        if (claimed0 > 0) {
            IERC20(_token0).approve(address(votingReward), claimed0);
    @>      votingReward.notifyRewardAmount(_token0, claimed0);
```

새로운 버전의 `Gauge`가 배포될 때까지 수수료는 분배되지 않고 `notifyRewardAmount()` 함수는 항상 되돌려지며, 예금자 간의 분배가 변경될 수 있으므로 새로운 `Gauge` 구현이 배포되면 보상의 불공정한 분배가 발생할 수 있습니다.

## 권장 사항

`Gauge` 초기화 시 `votingReward`를 설정하십시오.

# [M-01] `veKITTEN` 변경 후 `RebaseReward`의 보상 청구 불가

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

풀에 투표하면 다음 기간에 `VotingReward` 및 `RebaseReward`에 보상이 생성됩니다. 즉, 투표가 재설정되면 현재 및 이전 기간의 보상이 보류 중일 수 있습니다.

`veKITTEN` 토큰에 대해 `withdraw()`, `merge()` 또는 `split()`이 호출되면 `locked[_tokenId]`의 `amount`와 `end`가 0으로 설정되지만 토큰은 소각되지 않습니다. 이를 통해 토큰 소유자는 `VotingReward`에서 보상을 청구할 수 있지만 `RebaseReward`에서는 청구할 수 없습니다. 이 계약은 0이 아닌 잠금 금액과 종료 타임스탬프가 도달하지 않아야 하는 `VotingEscrow.deposit_for()` 함수를 실행하기 때문입니다.

```solidity
    function deposit_for(
        uint256 _tokenId,
        uint256 _value
    ) external nonReentrant {
        LockedBalance memory _locked = locked[_tokenId];

        if (_value == 0) revert Invalid();
  @>    if (_locked.amount == 0) revert LockNotExist();
  @>    if (block.timestamp >= _locked.end) revert LockExpired();
```

결과적으로 토큰 소유자는 `RebaseReward`에서 보류 중인 보상을 청구할 수 없습니다.

## 권장 사항

`VotingEscrow`에 기존 토큰 ID를 재사용하여 새 잠금을 생성하는 함수를 생성하여 `deposit_for()`를 되돌리지 않고 호출할 수 있도록 하십시오.

# [M-02] 게이지가 종료되면 누적된 수수료가 묶임

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

이러한 수수료 청구를 담당하는 내부 `_claimFees()` 함수는 `notifyRewardAmount()`를 통한 보상 알림 중에만 호출됩니다. 그러나 게이지가 종료되면 새로운 보상은 채굴자에게 리디렉션되고 게이지에 대해 `notifyRewardAmount()`가 더 이상 호출되지 않습니다.

```solidity
        claimable[_gauge] = 0;
        if (_claimable > 0) kitten.transfer(minter, _claimable);
```

```solidity
                if (isAlive[_gauge]) {
                    claimable[_gauge] += _share;
                } else {
                    IERC20(kitten).transfer(minter, _share);
                }
```

결과적으로 누적된 수수료는 쌍 계약에 남아 청구할 수 없게 되어 사용자의 가치 손실로 이어집니다.

```solidity
// Only called within notifyRewardAmount()
function _claimFees() internal { ... }
```

## 권장 사항

`voter`만 호출할 수 있는 `_claimFees()`를 호출하는 공개 함수를 도입하십시오. 게이지가 종료될 때 이 함수가 호출되어 남은 수수료가 청구되고 적절하게 분배되도록 하십시오.

# [M-03] 투표자의 보상이 조작될 수 있음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

voter 계약에서 투표자는 다양한 게이지에 투표합니다. 투표자는 실제 투표 가중치에 따라 rebaseReward 및 votingReward 계약에서 일부 보상을 얻을 수 있습니다. 투표 가중치가 높을수록 투표자는 더 많은 보상을 받을 수 있습니다.

여기서 문제는 veToken의 투표권이 시간이 지남에 따라 감소한다는 것입니다. 투표자가 새로운 투표 기간이 시작될 때 투표를 마칠 때입니다. 악의적인 사용자는 투표 기간이 끝나갈 때 `poke` 함수를 트리거하여 투표 가중치를 업데이트할 수 있습니다. 그러면 사용자는 예상보다 적은 보상을 받을 수 있습니다.

하나의 veToken이 긴 잠금 기간을 가지면 7일 동안 투표권이 너무 많이 감소하지 않을 수 있습니다. 그러나 하나의 veToken 잠금 기간이 1개월 만료되면 악의적인 사용자는 해당 사용자가 이 보상 에포크 기간에 보상의 1/3 또는 1/2을 잃게 만들 수 있습니다.

```solidity
    function _vote(
        uint256 _tokenId,
        address[] memory _poolVote,
        uint256[] memory _weights
    ) internal {
        IVotingReward(votingReward[_gauge])._deposit(
            uint256(_poolWeight),
            _tokenId
        );
        rebaseReward._deposit(uint256(_poolWeight), _tokenId);
    }
    function poke(uint256 _tokenId) external {
        address[] memory _poolVote = poolVote[_tokenId];
        uint256 _poolCnt = _poolVote.length;
        uint256[] memory _weights = new uint256[](_poolCnt);

        for (uint256 i = 0; i < _poolCnt; i++) {
            _weights[i] = votes[_tokenId][_poolVote[i]];
        }

        _vote(_tokenId, _poolVote, _weights);
    }
```

## 권장 사항

`poke` 함수는 사용자가 투표권을 업데이트하도록 돕는 것을 목표로 합니다. 여기서의 문제는 투표자가 이미 투표한 경우 악의적인 사용자가 여전히 찔러서(poke) 보상을 약간 조작할 수 있다는 것입니다.

# [L-01] `Gauge`, `VotingReward`, `RebaseReward`에 토큰 복구 기능 누락

`CLGauge` 계약에는 토큰 복구 기능이 있지만 `Gauge`, `VotingReward`, `RebaseReward` 계약에는 없습니다.

이는 분배되지 않은 보상, 반올림 오류로 인한 먼지(dust) 금액 또는 실수로 이러한 계약으로 전송된 토큰의 복구를 방지합니다.

이러한 계약에 토큰 복구 기능을 구현하는 것이 좋습니다.

# [L-02] `CLGauge.transferERC20()`의 안전하지 않은 전송

`CLGauge.transferERC20()` 함수는 게이지에서 토큰을 구하려는 의도이나, 모든 토큰에 대해 가능하지 않을 수 있습니다.

일부 토큰은 ERC20 표준을 제대로 구현하지 않지만 여전히 널리 사용됩니다. 여기에는 `transfer()` 및 `transferFrom()` 함수에서 부울 값을 반환하지 않는 토큰이 포함됩니다. 이러한 종류의 토큰이 `IERC20`으로 캐스팅되면 함수 서명이 일치하지 않아 호출이 되돌려집니다.

OpenZeppelin의 `SafeERC20` 라이브러리에 있는 `safeTransfer()` 함수를 사용하는 것이 좋습니다.

# [L-03] `Minter`에 리베이스 비율에 대한 설정자(Setter) 누락

`Minter` 계약에는 `MAX_REBASE_RATE` 상수가 있습니다. 또한 `rebaseRate` 변수 초기화 시 "30% bootstrap"이라는 주석이 있습니다. 이는 리베이스 비율이 조정 가능하도록 의도되었음을 시사하지만 배포 후 변경할 수 있는 설정자(setter) 함수가 제공되지 않습니다.

리베이스 비율을 조정할 수 있도록 의도한 경우 소유자가 변경할 수 있는 설정자 함수가 있어야 합니다.

# [L-04] 문서와 구현 간의 방출 계획 불일치

문서(https://docs.kittenswap.finance/tokenomics/initial-distribution)에 따르면 초기 방출은 `20,000,000 KITTEN (2%)`부터 시작됩니다.

구현을 확인하면 실제 초기 금액은 `20,000,000 + 20,000,000 * 30% + (20,000,000 * 130% * 5%)`입니다. 이것은 우리 문서의 예상 방출량보다 클 것입니다.

```solidity
    function updatePeriod() external returns (bool) {
        uint256 currentPeriod = getCurrentPeriod();
        if (currentPeriod > lastMintedPeriod) {
            uint256 emissions = nextEmissions;
            nextEmissions = calculateNextEmissions(emissions);
            // 2% * circulating supply
            uint256 _tailEmissions = tailEmissions();
            uint256 rebase = calculateRebase(emissions);
            // emssion + emission * 30%
            uint256 treasuryEmissions = ((emissions + rebase) * treasuryRate) /
                PRECISION;
            uint256 mintAmount = emissions + rebase + treasuryEmissions;
            // kitten token in this minter.
            uint256 kittenBal = kitten.balanceOf(address(this));
            if (kittenBal < mintAmount) {
                kitten.mint(address(this), mintAmount - kittenBal);
            }
        }
    }
```

권장 사항: 문서와 구현 간의 일관성을 유지하십시오.

# [L-05] `rewardRate`에 대한 일관성 없는 구현

풀에 따라 두 가지 다른 종류의 게이지가 있습니다. 보상을 알릴 때 보상률의 계산 방식이 다릅니다.

게이지에서는 `(_amount * PRECISION) / DURATION`을 사용합니다. CLGauge에서는 `rewardRate = amount / epochDurationLeft;`를 사용합니다. `epochDurationLeft`는 다른 타임스탬프에 따라 1주 미만이 될 수 있습니다.

voter에는 `distro`라는 하나의 함수가 있습니다. 모든 게이지의 보상이 하나의 트랜잭션으로 분배될 수 있습니다. 이로 인해 CL 게이지에서는 다음 7일 동안 한 시간 슬롯에 보상을 분배하고 다른 시간 슬롯에는 보상을 분배하지 않게 됩니다.

```solidity
    function notifyRewardAmount(
        uint256 _amount
    ) external onlyVoterOrAuthorized updateReward(address(0)) {
        _claimFees();
        // reword token is kitten.
        kitten.safeTransferFrom(msg.sender, address(this), _amount);
        // If last period is finished, we will start one new period.
        if (block.timestamp >= finishAt) {
            // PRECISION = 1e18
            rewardRate = (_amount * PRECISION) / DURATION;
        }
    }
    function notifyRewardAmount(uint256 amount) external nonReentrant {
        require(amount > 0);
        require(msg.sender == address(voter));
        _claimFees();
        // When we notify rewards, we need to update or accrue the previous rewards.
        pool.updateRewardsGrowthGlobal();
        address msgSender = msg.sender;
        uint256 timestamp = block.timestamp;
        // epoch remaining time.
        // epochDurationLeft may be less than 1 week.
        uint256 epochDurationLeft = ProtocolTimeLibrary.epochNext(timestamp) -
            timestamp;
        if (block.timestamp >= periodFinish) {
            rewardRate = amount / epochDurationLeft;
            pool.syncReward({
                rewardRate: rewardRate,
                rewardReserve: amount,
                periodFinish: nextPeriodFinish
            });
        }
    }
```

권장 사항: rewardRate 계산에 유사한 수식을 사용할 것을 제안합니다.

# [L-06] 게이지에 대한 롤오버 메커니즘 누락

CLGauge는 롤오버 메커니즘을 지원합니다. 예를 들어 보상 기간이 비활성 상태로 지나가면 보상은 다음에 알림된 보상 금액으로 전달됩니다.

```solidity
// CLGauge
       address msgSender = msg.sender;
        uint256 timestamp = block.timestamp;
        uint256 epochDurationLeft = ProtocolTimeLibrary.epochNext(timestamp) -
            timestamp;

        kitten.safeTransferFrom(msgSender, address(this), amount);
 @>     amount = amount + pool.rollover();

        uint256 nextPeriodFinish = timestamp + epochDurationLeft;
```

이 메커니즘은 게이지 구현에 존재하지 않으며 비활성 보상 기간의 보상은 계약에 잠깁니다.

# [L-07] 게이지 종료 후에도 여전히 사용 가능

CLGauge는 항상 종료되었는지 여부를 확인합니다. 그러나 게이지의 경우는 그렇지 않습니다. 사용자는 여전히 게이지를 사용할 수 있습니다.

```solidity
// Gauge
    function deposit(
        uint256 _amount
    ) external nonReentrant actionLock updateReward(msg.sender) {
        if (_amount == 0) revert ZeroAmount();
        lpToken.safeTransferFrom(msg.sender, address(this), _amount);
        balanceOf[msg.sender] += _amount;
        totalSupply += _amount;
    }
...
// CLGauge
    function deposit(uint256 nfpTokenId) public nonReentrant actionLock {
        if (
            IVoter(voter).isGauge(address(this)) == false ||
            IVoter(voter).isAlive(address(this)) == false
        ) revert NotGaugeOrNotAlive();
```

게이지 내부에도 동일한 로직을 구현하는 것을 고려하십시오.

# [L-08] 종료 전에 게이지가 업데이트되지 않음

```solidity
    function killGauge(address _gauge) external onlyRole(AUTHORIZED_ROLE) {
        if (isAlive[_gauge] == false) revert GaugeDead();
        isAlive[_gauge] = false;
@>        uint256 _claimable = claimable[_gauge];
        claimable[_gauge] = 0;
        if (_claimable > 0) kitten.transfer(minter, _claimable);
        emit GaugeKilled(_gauge);
    }
```

주어진 경우 청구 가능한 가치가 오래되었습니다. 더 적은 토큰이 채굴자에게 전송되고 게이지 토큰은 `Voter` 계약에 갇히게 됩니다. 대신 종료하기 전에 게이지 보상을 업데이트하십시오.

# [L-09] 총 공급량이 0일 때 `Gauge` 보상이 분배되지 않음

`Gauge`는 새로운 작업(예금, 인출, 보상 청구 또는 보상 알림)이 수행될 때마다 보상을 업데이트합니다. `totalSupply`가 0이면 `rewardPerToken`은 업데이트되지 않지만 마지막 업데이트 타임스탬프는 업데이트됩니다.

```solidity
    modifier updateReward(address _account) {
        rewardPerTokenStored = rewardPerToken();
        updatedAt = lastTimeRewardApplicable();

(...)

    function rewardPerToken() public view returns (uint256) {
        if (totalSupply == 0) {
            return rewardPerTokenStored;
        }
```

이로 인해 총 공급량이 0인 기간 동안 분배될 보상이 잠기게 됩니다. 적어도 한 명의 사용자가 게이지에 0이 아닌 잔액을 보유할 경제적 인센티브가 있지만, 게이지 생성 순간이나 부활 후 사용자가 즉시 LP 토큰을 예치하지 않을 수 있으므로 이것이 보장되지는 않습니다.

개념 증명

`TestGauge.t.sol` 파일에 다음 코드를 추가하고 `forge test -f https://rpc.hyperliquid.xyz/evm --mt test_audit_GaugeLockedRewards --mc TestGauge`를 실행하십시오.

```solidity
function test_audit_GaugeLockedRewards() public {
	// setup
	test_CreateGauge();
	Gauge _gauge = Gauge(gauge.get(pairListVolatile[0]));
	address user1 = userList[0];
	address lpToken = address(_gauge.lpToken());
	vm.prank(kitten.minter());
	kitten.mint(address(voter), 2 ether);
	deal(lpToken, user1, 1 ether);

	// voter distributes 1e18 KITTEN to the gauge
	vm.prank(address(voter));
	_gauge.notifyRewardAmount(1 ether);

	// after 1 week, user1 deposits LP tokens into the gauge
	vm.warp(block.timestamp + 1 weeks);
	vm.startPrank(user1);
	IERC20(lpToken).approve(address(_gauge), 1 ether);
	_gauge.deposit(1 ether);
	vm.stopPrank();

	// voter distributes another 1e18 KITTEN to the gauge
	vm.prank(address(voter));
	_gauge.notifyRewardAmount(1 ether);

	// after another week, user1 claims rewards
	vm.warp(block.timestamp + 1 weeks);
	vm.prank(user1);
	_gauge.getReward(user1);

	// gauge has 1e18 KITTEN balance, but no rewards left
	assertGe(kitten.balanceOf(address(_gauge)), 1 ether);
	assertEq(_gauge.left(), 0);
}
```

**권장 사항**
총 공급량이 0이라 분배되지 않은 보상의 분배 또는 복구를 처리하십시오. 이는 다음과 같은 다양한 방법으로 수행할 수 있습니다.

- 분배되지 않은 보상을 VotingReward 계약으로 전송.
- 분배되지 않은 보상을 이 목적을 위해 지정된 특정 주소로 전송.
- 분배되지 않은 보상을 다음 분배 주기에 추가.

# [L-10] `Voter`에 보상이 잠길 수 있음

게이지가 종료되면 청구 가능한 보상이 채굴자에게 전송되고 후속 보상도 그 곳으로 리디렉션됩니다. 그러나 `updateGauge()`가 호출되기 전에 게이지가 나중에 부활하고 그 직전에 인덱스 업데이트(공개 `notifyRewardAmount()` 함수를 통해)가 발생하면 채굴자를 위한 보상이 `Voter` 계약에 잠기게 됩니다.

이는 `supplyIndex`는 업데이트되지만 게이지의 상태는 업데이트되지 않아 이러한 보상의 적절한 분배를 방지하기 때문입니다.

```solidity
// reviveGauge() does not call updateGauge(), leading to locked rewards
function reviveGauge(address gauge) external onlyOwner {
    // missing: updateGauge(gauge);
    // ... revive logic ...
}
```

**권장 사항**

`reviveGauge()` 함수를 수정하여 부활한 게이지에 대해 `updateGauge()`를 호출하도록 하십시오.

# [L-11] Reward 계약에 갭(Gap) 누락

Kittenswap 계약은 향후 업데이트를 위해 갭이 필요할 수 있는 업그레이드 가능한 계약이지만 Reward 계약만 부모 계약으로 사용됩니다. Voting reward 및 Rebase reward 계약은 Reward 계약을 부모로 사용합니다. 그러나 Reward 계약에 필요한 갭이 누락되어 있어 Reward 계약의 새로운 업그레이드로 인해 스토리지 충돌이 발생할 수 있습니다.

현재 Voting Reward 계약은 내부에 스토리지 변수가 없기 때문에 안전하지만 Rebase Reward 계약은 스토리지에서 `kitten` 변수를 사용하기 때문에 Reward 계약 업그레이드에 안전하지 않습니다.

```solidity
contract RebaseReward is IRebaseReward, Reward {
    IERC20 public kitten;
```

새 변수가 Reward에 추가되면 Reward 계약의 누락된 갭으로 인해 kitten 스토리지 변수가 덮어쓰여집니다.

**권장 사항**

스토리지 업데이트를 위해 Reward 계약에 갭을 추가하는 것을 고려하십시오.

# [L-12] 잠금이 만료되면 사용자가 보상을 청구할 수 없음

Rebase Reward 계약에서 보상은 추가 잠금 금액으로 투표 에스크로 잠금에 분배됩니다. 사용자가 보상을 청구할 때마다 `_getReward` 함수에서 `deposit_for` 함수가 호출됩니다.

```solidity
    function _getReward(
        uint256 _period,
        uint256 _tokenId,
        address _token,
        address _owner
    ) internal override {
        if (totalVotesInPeriod[_period] > 0) {
            uint256 reward = _earned(_period, _tokenId, _token); reward is not kitten, directly transfer it
            tokenIdRewardClaimedInPeriod[_period][_tokenId][_token] += reward;

            if (reward > 0) {
@>              veKitten.deposit_for(_tokenId, reward);
                emit ClaimReward(_period, _tokenId, _token, _owner);
            }
        }
    }
```

Voting escrow는 만료된 잠금에 대한 입금을 허용하지 않습니다.

시나리오:

1. 사용자가 2년 동안 100e18 토큰을 투표 에스크로에 잠갔습니다.
2. 사용자가 풀에 투표했고 리베이스 보상이 사용자에 대한 수익을 창출했습니다.
3. 2년 후 사용자가 보상을 청구하고 싶었지만 불가능합니다.

따라서 보상을 적극적으로 청구하지 않는 사용자는 설계로 인해 모든 보상을 잃게 됩니다.

```solidity
    function deposit_for(
        uint256 _tokenId,
        uint256 _value
    ) external nonReentrant {
        LockedBalance memory _locked = locked[_tokenId];

        if (_value == 0) revert Invalid();
        if (_locked.amount == 0) revert LockNotExist();
@>      if (block.timestamp >= _locked.end) revert LockExpired()
```

**권장 사항**

리베이스 보상에 의해서만 호출될 수 있는 승인된 함수와 같이 이러한 보상을 투표 에스크로에 입금하는 다른 방법을 고려하십시오.
