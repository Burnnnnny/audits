# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**roots-fi/roots-core** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Roots 정보

Roots는 사용자가 Berachain 네이티브 자산을 담보로 사용하여 $MEAD 스테이블 코인을 빌릴 수 있는 DeFi 프로토콜로, 청산을 방지하기 위해 최소 120%의 담보 비율을 요구합니다. 사용자는 $MEAD 부채를 상환하여 담보를 상환할 수 있으며, 안정성 풀(Stability Pool)에 스테이킹하거나 청산 이벤트에 참여하여 보상을 얻을 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 희박하거나, 인센티브가 거의 또는 전혀 없이 공격자가 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정하는 것이 좋음

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [c5dfa1eb74bf368e231203d33d7099c682fbe712](https://github.com/roots-fi/roots-core/tree/c5dfa1eb74bf368e231203d33d7099c682fbe712)

_수정 검토 커밋 해시_ - [ce3f41d9d96211fdcd6fb4a68510fa8195c90637](https://github.com/roots-fi/roots-core/tree/ce3f41d9d96211fdcd6fb4a68510fa8195c90637)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Factory`
- `TroveManager`
- `StabilityPool`
- `BorrowerOperations`
- `Staker`

# 발견 사항

# [C-01] `Staker`에서 게이지(gauge) 계약에 사용된 잘못된 인터페이스

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`Staker` 계약은 `RewardVault` 계약과 상호 작용하도록 되어 있습니다. 그러나 `IGauge` 인터페이스는 [`getReward` 함수](https://docs.berachain.com/developers/contracts/reward-vault#getreward)에 대한 `RewardVault` 연락처의 인터페이스와 일치하지 않습니다.

```solidity
File: IRewardVault.sol
function getReward(address account, address recipient) external returns (uint256);
```

```solidity
File: IGauge.sol
function getReward(address) external returns (uint256);
```

이로 인해 `_fetchRewards()`가 실행될 때 `_updateRewardIntegral()` 함수가 되돌려집니다(revert).

```solidity
File: Staker.sol
    function _fetchRewards() internal {
        gauge.getReward(address(this));
```

`_updateRewardIntegral()` 함수는 트로브(trove) 개설, 폐쇄 및 조정, 담보 상환 또는 청산과 같은 프로토콜의 가장 중요한 작업에서 실행되므로 프로토콜의 기능을 망가뜨리고 자금을 잠기게 합니다.

## 권장 사항

```diff
    function _fetchRewards() internal {
-       gauge.getReward(address(this));
+       gauge.getReward(address(this), address(this));
```

# [H-01] 담보 덮어쓰기로 인한 담보 이익 로직 파손

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`StabilityPool` 계약에서 담보의 일몰(sunset) 이후 180일이 지나면 새 담보를 활성화하면 이전 담보를 덮어씁니다.

이 과정에서 `epochToScaleToSums` 매핑은 지워지지만 `collateralGainsByDepositor` 및 `depositSums` 매핑은 지워지지 않습니다. 이는 이전 담보에 대한 보류 중인 이익이 새 담보에 대한 이익으로 계산됨을 의미합니다.

결과적으로 담보 이익 로직이 깨져 다음과 같은 상황이 발생합니다.

- `uint256 firstPortion = sums[i] - depSums[i];`에서의 언더플로로 인해 `_accrueDepositorCollateralGain`을 실행하는 함수에 대한 DoS 발생.
- 풀에 충분한 담보가 없어서 사용자가 담보 이익을 청구할 수 없음.
- 사용자가 다른 사용자로부터 가져와야 할 것보다 더 많은 이익을 청구함(개념 증명 참조).

또한 현재 덮어쓰기 시스템에서는 일몰 담보를 비활성화하려면 새 담보를 활성화해야 하는데, 이는 이상적이지 않습니다.

## 개념 증명

다음 코드를 `test/unit/Roots.t.sol` 파일에 추가하고 `forge test --mt test_sunsetCollateral`을 실행하십시오.

```solidity
function _openTroveAndProvideToSP(
    TroveManager troveManager,
    address user,
    uint256 collAmount,
    uint256 debtAmount
) private {
    Collateral collateral = Collateral(address(troveManager.collateralToken()));
    vm.startPrank(user);
    collateral.mint(collAmount);
    collateral.approve(address(system.borrowerOperations), collAmount);
    system.borrowerOperations.openTrove(troveManager, user, 1e16, collAmount, debtAmount, address(0), address(0));
    system.debtToken.approve(address(system.stabilityPool), debtAmount);
    system.stabilityPool.provideToSP(debtAmount);
    vm.stopPrank();
}

function test_sunsetCollateral() external {
    uint256 collateralToProvide = 4e18;
    uint256 amountToBorrow = 1e18;

    // Alice opens trove with coll A and provides MEAR to stability pool
    _openTroveAndProvideToSP(troveManagerA, ALICE, collateralToProvide * 100, amountToBorrow);

    // Bob opens trove with coll A and provides MEAR to stability pool
    _openTroveAndProvideToSP(troveManagerA, BOB, collateralToProvide, amountToBorrow);

    // Bob becomes liquidable and is liquidated
    uint256 newCollateralPrice = (12e17 * amountToBorrow) / collateralToProvide;
    priceFeed.setPrice(address(collateralA), newCollateralPrice);
    vm.prank(LIQUIDATOR);
    system.liquidationManager.liquidate(troveManagerA, BOB);

    // Alice triggers accrual of collateral gains
    skip(1);
    vm.prank(ALICE);
    system.stabilityPool.withdrawFromSP(0);

    // Collateral A is sunset
    vm.startPrank(OWNER);
    troveManagerA.startSunset();
    system.stabilityPool.startCollateralSunset(collateralA);
    vm.stopPrank();

    // After 180 days, a new collateral is added
    skip(180 days + 1);
    Collateral collateralC = new Collateral("Collateral C", "CC");
    priceFeed.setPrice(address(collateralC), 2e18);

    vm.prank(OWNER);
    system.factory.deployNewInstance(
        address(collateralC),
        address(priceFeed),
        address(0),
        address(0),
        address(0),
        abi.encodeWithSelector(
            TroveManager.setParameters.selector,
            minuteDecayFactor,
            redemptionFeeFloor,
            maxRedemptionFee,
            borrowingFeeFloor,
            maxBorrowingFee,
            interestRateInBps,
            maxDebt,
            MCR
        ),
        bytes("")
    );
    TroveManager troveManagerC = TroveManager(system.factory.troveManagers(2));

    // Alice opens trove with coll C and provides MEAR to stability pool
    _openTroveAndProvideToSP(troveManagerC, ALICE, collateralToProvide * 100, amountToBorrow);

    // Bob opens trove with coll C and provides MEAR to stability pool
    _openTroveAndProvideToSP(troveManagerC, BOB, collateralToProvide, amountToBorrow);

    // Bob becomes liquidable and is liquidated
    priceFeed.setPrice(address(collateralC), newCollateralPrice);
    vm.prank(LIQUIDATOR);
    system.liquidationManager.liquidate(troveManagerC, BOB);

    // Alice claims gains for coll C, receiving also the amount corresponding to coll A
    uint256[] memory collateralIndexes = new uint256[](1);
    collateralIndexes[0] = 0;
    vm.prank(ALICE);
    system.stabilityPool.claimCollateralGains(ALICE, collateralIndexes);

    // Bob tries to claim gains for coll C, but his gains have been sent to Alice
    vm.prank(BOB);
    vm.expectRevert("ERC20: transfer amount exceeds balance");
    system.stabilityPool.claimCollateralGains(BOB, collateralIndexes);
}
```

## 권장 사항

일몰 담보를 덮어쓰지 말고 `expiry` 시간에 도달한 후 비활성화하는 새 함수를 만드십시오.

# [H-02] `openTrove`에서 사용된 오래된 `totalActiveDebt`로 인한 잘못된 부채 업데이트

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`TroveManager::openTrove` 함수에서 `totalActiveDebt`는 `_accrueActiveInterests` 함수가 호출되기 전에 로컬 변수 `supply`에 캐시됩니다. `_accrueActiveInterests` 함수 자체는 발생한 이자를 반영하기 위해 `totalActiveDebt`를 업데이트합니다. 그러나 `openTrove` 함수는 잠재적으로 업데이트된 `totalActiveDebt` 대신 캐시된 `supply` 값을 사용하여 `totalActiveDebt`를 업데이트합니다.

구체적으로 코드는 754행에서 `totalActiveDebt`를 캐시합니다.

```solidity
File: TroveManager.sol
754:    uint256 supply = totalActiveDebt;
```

그런 다음 이자가 발생하여 잠재적으로 761행에서 `_accrueActiveInterests()`를 호출하여 `totalActiveDebt`를 업데이트합니다.

```solidity
File: TroveManager.sol
761:    uint256 currentInterestIndex = _accrueActiveInterests();
```

`_accrueActiveInterests` 내부에서 `totalActiveDebt`가 업데이트됩니다.

```solidity
File: TroveManager.sol
1183:           totalActiveDebt = currentDebt + activeInterests;
```

마지막으로 776행에서 `totalActiveDebt`는 _캐시된_ `supply` 값을 사용하여 업데이트됩니다.

```solidity
File: TroveManager.sol
774:@>       uint256 _newTotalDebt = supply + _compositeDebt;
775:         require(_newTotalDebt + defaultedDebt <= maxSystemDebt, "Collateral debt limit reached");
776:@>       totalActiveDebt = _newTotalDebt;
```

`supply`는 `_accrueActiveInterests`의 이자 적립 이전의 `totalActiveDebt` 값이므로 776행의 업데이트는 동일한 `openTrove` 트랜잭션 내에서 발생한 이자를 포함하지 않습니다. 이는 `totalActiveDebt`가 잘못 업데이트되어 잠재적으로 시스템의 실제 부채를 과소 평가할 수 있음을 의미합니다.

다음 시나리오를 고려해 보십시오.

1.  초기 상태:

    - `totalActiveDebt = 1000`
    - 이자율은 `_accrueActiveInterests()`가 `totalActiveDebt`를 100만큼 증가시키도록 설정됩니다.

2.  `openTrove` 호출:

    - 사용자가 `_compositeDebt = 500`으로 `openTrove`를 호출합니다.
    - 754행: `supply = totalActiveDebt;` => `supply = 1000` (캐시된 값).
    - 761행: `_accrueActiveInterests()`가 호출됩니다. `_accrueActiveInterests()` 내부(1183행): `totalActiveDebt`가 `1000 + 100 = 1100`으로 업데이트됩니다.
    - 776행: `totalActiveDebt = _newTotalDebt;` 여기서 `_newTotalDebt = supply + _compositeDebt = 1000 + 500 = 1500`입니다.
    - `totalActiveDebt`는 `1500`으로 설정됩니다.

3.  예상 vs 실제 `totalActiveDebt`:
    - 예상 `totalActiveDebt`: 초기 부채(1000) + 발생 이자(100) + 신규 부채(500) = `1600`.
    - `openTrove` 이후 실제 `totalActiveDebt`: `1500`.

`totalActiveDebt`는 `100`(동일한 트랜잭션 내에서 발생한 이자)만큼 과소 평가됩니다.

## 권장 사항

이 문제를 해결하려면 함수의 시작 부분에서 `totalActiveDebt`를 캐싱하는 대신 이자 적립 _후_ 가장 최근 값으로 `totalActiveDebt`가 업데이트되도록 하십시오.

# [H-03] 잘못된 부스트 관리로 인한 스테이킹 보상 손실

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`rewardCache::dropBoost` 함수(BGT 0x656b95E550C07a9ffe548bd4085c72418Ceb1dba)는 주어진 검증인에 대한 부스트 보상을 삭제하도록 설계되었습니다. 그러나 마지막 부스트 이후 충분한 시간이 지났는지 확인하기 위해 `dropBoostQueue` 스토리지를 확인합니다. 문제는 `Staker::_redeemRewards` 및 `Staker::setValidator`와 같은 함수에서 `dropBoost` 전에 `rewardCache::queueDropBoost`가 호출되지 않는다는 것입니다. 결과적으로 `dropBoostQueue`는 비어 있는 상태로 유지되고 `dropBoost`는 항상 `false`를 반환하여 보상 조정을 방지합니다.

1. `queueDropBoost` 함수는 부스트된 토큰의 향후 삭제를 대기열에 넣는 데 사용됩니다.

```solidity
    function queueDropBoost(bytes calldata pubkey, uint128 amount) external {
@>      QueuedDropBoost storage qdb = dropBoostQueue[msg.sender][pubkey];
        uint128 dropBalance = qdb.balance + amount;
        // check if the user has enough boosted balance to drop
        if (boosted[msg.sender][pubkey] < dropBalance) NotEnoughBoostedBalance.selector.revertWith();
@>      (qdb.balance, qdb.blockNumberLast) = (dropBalance, uint32(block.number));
        emit QueueDropBoost(msg.sender, pubkey, amount);
    }
```

2. `dropBoost` 함수는 부스트를 삭제하려고 시도하지만 유효한 `dropBoostQueue` 항목이 필요합니다.

```solidity
    function dropBoost(address user, bytes calldata pubkey) external returns (bool) {
@>      QueuedDropBoost storage qdb = dropBoostQueue[user][pubkey];
@>      (uint32 blockNumberLast, uint128 amount) = (qdb.blockNumberLast, qdb.balance);
        // `amount` must be greater than zero to avoid reverting as
        // `withdraw` will fail with zero amount.
@>      if (amount == 0 || !_checkEnoughTimePassed(blockNumberLast, dropBoostDelay)) return false;
..
```

3. `_redeemRewards`는 `queueDropBoost` 없이 `dropBoost`를 직접 호출합니다.

```solidity
File: Staker.sol
305:             if (toFulfill > 0) {
306:                 rewardCache.dropBoost(validator, uint128(toFulfill));
307:             }
```

4. 마찬가지로 `setValidator`는 `queueDropBoost`를 사용하지 않고 `dropBoost`를 호출합니다.

```solidity
File: Staker.sol
80:         uint256 boosted = rewardCache.boosts(address(this));
81:         if (boosted > 0) {
82:             rewardCache.dropBoost(oldValidator, uint128(boosted));
83:         }
```

## 권장 사항

`Staker::_redeemRewards` 및 `Staker::setValidator`의 로직을 업데이트하여 `dropBoost`를 호출하기 전에 `rewardCache::queueDropBoost`가 올바르게 호출되도록 하십시오.

# [H-04] 차용자가 잉여 담보를 청구하지 못함

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

Roots에서 TCR이 CCR보다 작으면 시스템은 복구 모드에 들어갑니다. 복구 모드에서는 차용 포지션의 ICR이 MCR보다 크더라도 일부 차용 포지션이 청산됩니다.

이런 종류의 청산에서 우리는 차용자로부터 모든 담보 토큰을 압류하지 않습니다. 우리는 MCR에 따라 담보 토큰을 계산합니다. 이는 차용자에게 `collSurplus`라는 남은 담보 토큰이 있음을 의미합니다. 우리는 청산된 차용자의 `surplusBalances`를 `addCollateralSurplus` 함수를 통해 기록합니다.

청산된 차용자는 `claimCollateral` 함수를 통해 남은 담보를 청구할 수 있습니다. 문제는 Roots 프로토콜이 Prisma와 약간 다르다는 것입니다. 모든 담보 토큰은 Staker 계약에 스테이킹되어 일부 보상을 얻습니다. 그러나 `claimCollateral` 함수에서는 TroveManager에서 직접 담보 토큰을 전송하려고 시도합니다. 이 트랜잭션은 되돌려집니다. Trove Manager에서 일부 담보를 인출하려면 `staker.onWithdrawal`을 사용하여 Staker 계약에서 이러한 담보를 인출해야 합니다.

```solidity
    function claimCollateral(address _receiver) external {
        uint256 claimableColl = surplusBalances[msg.sender];
        require(claimableColl > 0, "No collateral available to claim");

        surplusBalances[msg.sender] = 0;

        collateralToken.safeTransfer(_receiver, claimableColl);
    }
```

## 권장 사항

하나의 차용 포지션이 청산될 때 Staker 계약에서 잉여 담보를 인출하십시오.

# [H-05] 잘못된 `activateBoost` 인터페이스가 사용됨

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

Staker 계약에서 대기 중인 부스트 BGT를 활성화합니다. 사용하는 인터페이스는 다음과 같습니다.

```solidity
    function activateBoost(address validator) external;
```

```solidity
    function _tryToBoost() internal {
        if (queued > 0 && blockDelta > 8191) {
            rewardCache.activateBoost(validator);
        }
    }
```

BGT의 구현을 확인하면 올바른 인터페이스는 다음과 같아야 함을 알 수 있습니다.

```solidity
    function activateBoost(address user, bytes calldata pubkey) external returns (bool) {
        ...
    }
```

우리는 잘못된 인터페이스를 사용하며, 하나의 `address user` 매개변수가 필요합니다. 이로 인해 예상대로 부스트할 수 없습니다. 그리고 이 `_tryToBoost()`는 `_updateRewardIntegral`에 의해 트리거됩니다. 그리고 TroveManger의 대부분의 함수가 영향을 받습니다.

## 권장 사항

BGT의 구현을 따르고 올바른 `activateBoost()` 인터페이스를 트리거하십시오.

# [H-06] BGT 스테이크 보상이 잠김

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

Staker 계약에서 담보 토큰을 스테이킹하여 BGT 보상 토큰을 얻습니다. BGT 보상 토큰은 부스트될 수 있습니다. staker 계약에서 BGT 토큰을 부스트할 때 BGT 계약은 BGT 토큰을 BGTStaker 계약(https://berascan.com/address/0x44F07Ce5AfeCbCC406e6beFD40cc2998eEb8c7C6)에 스테이킹하도록 돕습니다.

```solidity
    function _tryToBoost() internal {
            if (queued > 0 && blockDelta > 8191) {
            rewardCache.activateBoost(validator);
        }
```

```solidity
    function activateBoost(address user, bytes calldata pubkey) external returns (bool) {
        IBGTStaker(staker).stake(user, amount);
        return true;
    }
```

BGTStaker의 구현을 확인하면 BGT Staker에 BGT 토큰을 스테이킹할 때 HONEY 보상을 얻을 수 있습니다. 우리는 `getReward` 인터페이스를 통해 이러한 보상을 얻을 수 있습니다. 그러나 Staker 계약에는 이 보상 부분을 얻을 수 있는 인터페이스가 하나 누락되어 있습니다.

```solidity
    function getReward() external returns (uint256) {
        return _getReward(msg.sender, msg.sender);
    }
```

## 권장 사항

이 부스트 보상을 청구할 수 있도록 Staker 계약에 하나의 인터페이스를 추가하십시오.

# [M-01] `StabilityPool`에서 담보 이익을 청구하는 마지막 사용자에 대한 DoS

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`StabilityPool` 계약에서 일몰 담보를 덮어쓸 때 `lastCollateralError_Offset` 값이 재설정되지 않습니다. 즉, 새 담보가 이전 담보의 값을 상속합니다. 이로 인해 `collateralGainPerUnitStaked` 값이 원래보다 높아질 수 있습니다.

```solidity
    function _computeRewardsPerUnitStaked(
(...)
        uint256 collateralNumerator = (_collToAdd * DECIMAL_PRECISION) + lastCollateralError_Offset[idx];
(...)
        collateralGainPerUnitStaked = collateralNumerator / _totalDebtTokenDeposits;
```

이 값은 `epochToScaleToSums` 매핑에 저장되며 나중에 예금자의 담보 이익을 계산하는 데 사용됩니다.

결과적으로 마지막으로 담보 이익을 청구하는 사용자는 과대 평가된 이익을 충당할 만큼 풀에 담보가 충분하지 않을 수 있으므로 청구하지 못할 수 있습니다.

## 권장 사항

`_overwriteCollateral` 함수에서 새 담보에 재할당된 인덱스에 대해 `lastCollateralError_Offset` 값을 재설정하십시오.

```diff
+       lastCollateralError_Offset[idx] = 0;
        indexByCollateral[_newCollateral] = idx + 1;
        emit CollateralOverwritten(collateralTokens[idx], _newCollateral);
        collateralTokens[idx] = _newCollateral;
```

# [M-02] 기본 금리는 예상보다 느린 속도로 붕괴(decay)될 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

기본 금리는 상환 및 신규 부채 발행 수수료를 계산하는 데 사용되며, 그 가치는 시간이 지남에 따라 줄어들고 상환 규모에 따라 증가합니다.

상환 시 기본 금리의 붕괴는 마지막 수수료 운영 이후 경과된 분(minute) 수를 기준으로 계산됩니다. 여기서 주의해야 할 점은 경과된 분 수가 가장 가까운 분으로 내림(round down)된다는 것입니다.

```solidity
File: TroveManager.sol
    function _calcDecayedBaseRate() internal view returns (uint256) {
@>      uint256 minutesPassed = (block.timestamp - lastFeeOperationTime) / SECONDS_IN_ONE_MINUTE;
```

그러나 작업 종료 시 `lastFeeOperationTime`이 업데이트될 때 저장되는 값은 현재 블록 타임스탬프입니다.

```solidity
File: TroveManager.sol
    function _updateLastFeeOpTime() internal {
        uint256 timePassed = block.timestamp - lastFeeOperationTime;

        if (timePassed >= SECONDS_IN_ONE_MINUTE) {
@>          lastFeeOperationTime = block.timestamp;
```

이러한 불일치로 인해 기본 금리가 예상보다 느리게 붕괴될 수 있습니다.

예를 들어 마지막 수수료 운영 이후 119초가 경과한 경우 경과된 분 수는 1로 내림됩니다. 기본 금리는 60초만 경과한 것처럼 붕괴되지만 `lastFeeOperationTime`은 전체 119초를 고려하여 업데이트됩니다. 즉, 최악의 시나리오에서는 기본 금리가 119초마다 60초에 해당하는 양만큼만 붕괴됩니다.

## 권장 사항

```diff
    function _updateLastFeeOpTime() internal {
-       uint256 timePassed = block.timestamp - lastFeeOperationTime;
+       uint256 minutesPassed = (block.timestamp - lastFeeOperationTime) / SECONDS_IN_ONE_MINUTE;

-       if (timePassed >= SECONDS_IN_ONE_MINUTE) {
+       if (minutesPassed > 0) {
-           lastFeeOperationTime = block.timestamp;
+           lastFeeOperationTime += minutesPassed * SECONDS_IN_ONE_MINUTE;
            emit LastFeeOpTimeUpdated(block.timestamp);
        }
    }
```

# [M-03] 사용자가 샌드위치 청산을 통해 악성 부채 흡수를 방지할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

청산이 발생하고 안정성 풀(stability pool)에 악성 부채를 흡수할 자금이 충분하지 않으면 이 부채는 모든 활성 트로브에 분산됩니다.

악성 부채 재분배를 유발할 대규모 청산 이전에 트로브 소유자는 청산 직전에 담보를 인출하고 청산 직후에 트로브를 다시 열어 악성 부채 재분배를 피하고 다른 트로브의 악성 부채를 증가시킬 수 있습니다.

## 권장 사항

트로브 폐쇄 요청과 실행이 시간 지연으로 분리되도록 트로브 폐쇄를 위한 2단계 메커니즘을 구현하십시오.

# [M-04] `SetRewardsFeeBps`가 보상 계산을 업데이트하지 못함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Staker::setRewardsFeeBps` 함수는 `Staker` 계약이 얻은 보상에 적용되는 수수료 비율을 업데이트합니다. 그러나 수수료를 수정하기 전에 `_updateRewardIntegral`을 호출하지 않습니다. `_updateRewardIntegral`은 보상과 수수료를 계산하여 시스템에 통합하는 역할을 합니다. `_updateRewardIntegral`을 호출하지 않으면 모든 보류 중인 보상이 새 수수료율을 사용하여 계산되어 보상 분배가 부정확해질 수 있습니다.

코드 분석:

1. `setRewardsFeeBps` 함수는 다음과 같이 정의됩니다.

```solidity
File: Staker.sol
111:     function setRewardsFeeBps(uint16 _rewardsFeeBps) external onlyOwner {
112:         rewardsFeeBps = _rewardsFeeBps;
113:     }
```

2. 보상 계산은 다음 섹션에서 발생합니다.

```solidity
File: Staker.sol
185:                 uint256 earned = gauge.earned(address(this));
186:@>               uint256 fees = earned.mulDiv(rewardsFeeBps, 10000, Math.Rounding.Down);
187:                 integral += (earned - fees).mulDiv(1e18, supply, Math.Rounding.Down);
188:                 rewardIntegral = integral;
189:                 earnedFees += fees;
190:                 _fetchRewards();
```

보상 수수료는 계산 시점의 `rewardsFeeBps`를 사용하여 적용됩니다. 보상을 다시 계산하지 않고 `rewardsFeeBps`가 업데이트되면 보류 중인 보상이 새 수수료율로 처리되어 부정확한 결과가 발생합니다. 다음 시나리오를 고려해 보십시오.

1. 프로토콜은 5%의 `rewardsFeeBps`로 구성됩니다.
2. `Staker`는 보상을 얻었지만 보상은 아직 처리되지 않았습니다.
3. 소유자는 수수료를 10%로 늘리기 위해 `setRewardsFeeBps(10)`를 호출합니다.
4. 결국 `_updateRewardIntegral`이 호출되면 보류 중인 보상이 원래 5% 대신 새 10% 수수료를 사용하여 계산되어 잘못된 보상 및 수수료 계산이 발생합니다.

## 권장 사항

정확한 보상 분배를 보장하기 위해 `setRewardsFeeBps` 함수는 `rewardsFeeBps`를 업데이트하기 전에 `_updateRewardIntegral`을 호출해야 합니다. 이렇게 하면 보류 중인 보상이 이전 수수료율을 사용하여 처리됩니다.

# [M-05] 검증인 전환 지연으로 인한 부스트 토큰 관리 부실

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Staker.sol`의 `setValidator` 함수에는 `rewardCache.dropBoost` 함수 호출을 통한 부스트 토큰 관리와 관련된 취약점이 포함되어 있습니다. 이 문제는 마지막 부스트 작업 이후 시간이 충분하지 않아 `oldValidator`와 관련된 부스트를 삭제하는 데 `dropBoost`가 실패할 가능성이 있기 때문에 발생합니다.

```solidity
File: Staker.sol
68:     function setValidator(address _newValidator) external onlyOwner {
...
80:         uint256 boosted = rewardCache.boosts(address(this));
81:         if (boosted > 0) {
82:@>           rewardCache.dropBoost(oldValidator, uint128(boosted));
83:         }
...
90:@>       validator = _newValidator;
91:     }
```

`rewardCache`의 `dropBoost` 함수(BGT 0x656b95E550C07a9ffe548bd4085c72418Ceb1dba)에는 부스트 삭제를 허용하기 위해 마지막 dropBoost 작업 이후 충분한 시간이 지났는지 여부를 결정하는 조건 `_checkEnoughTimePassed`가 있습니다. 이 조건이 실패하면( `false` 반환) `dropBoost`는 `oldValidator`와 관련된 부스트를 삭제하지 않아 `userBoosts[oldValidator].boost`가 변경되지 않은 상태로 유지됩니다. 함수가 계속 진행되므로 새 검증인이 할당됩니다(코드 라인 `Staker#L90`).

`oldValidator`에 대한 부스트가 성공적으로 삭제되지 않으면 `_newValidator`를 위한 부스트가 차단되어 부스트된 토큰이 올바르게 할당되지 않아 가치 유출이 발생할 수 있습니다. 결국 소수의 토큰만 새 검증인에게 이동합니다.

## 권장 사항

dropBoost에서 반환된 값이 true인지 확인하는 검사를 추가하십시오. 그렇지 않은 경우 트랜잭션을 되돌릴 수 있습니다.

```diff
  function setValidator(address _newValidator) external onlyOwner {
        ...
        uint256 boosted = rewardCache.boosts(address(this));
        if (boosted > 0) {
-           rewardCache.dropBoost(oldValidator, uint128(boosted));
+           bool successDropBoost = rewardCache.dropBoost(oldValidator, uint128(boosted));
+           require(successDropBoost, "dropBoost function failed");
        }
        ...
```

# [M-06] 이자 발생으로 인해 트로브가 잘못 정렬될 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`SortedTroves` 계약은 트로브를 내림차순 명목 ICR(NICR)로 정렬하여 유지할 것으로 예상됩니다. 이는 트로브가 청산되고 상환되는 순서를 결정하므로 시스템에 중요한 불변(invariant)입니다. 그러나 이자 발생 메커니즘으로 인해 트로브가 잘못 정렬될 수 있습니다.

NICR은 각 트로브에 대해 `collateral / debt`로 계산되며 `collateral` 및 `debt` 값은 다음과 같이 계산됩니다.

```solidity
    function getEntireDebtAndColl(address _borrower)
        public
        view
        returns (uint256 debt, uint256 coll, uint256 pendingDebtReward, uint256 pendingCollateralReward)
    {
        Trove storage t = Troves[_borrower];
        debt = t.debt;
        coll = t.coll;

        (pendingCollateralReward, pendingDebtReward) = getPendingCollAndDebtRewards(_borrower);
        // Accrued trove interest for correct liquidation values. This assumes the index to be updated.
        uint256 troveInterestIndex = t.activeInterestIndex;
        if (troveInterestIndex > 0) {
            (uint256 currentIndex,) = _calculateInterestIndex();
            debt = (debt * currentIndex) / troveInterestIndex;
        }

        debt = debt + pendingDebtReward;
        coll = coll + pendingCollateralReward;
    }
```

부채 계산을 위해 "통합된" 부채에 대해 이자가 적용된 다음 보류 중인 부채 보상이 추가됩니다. 이는 트로브가 부채 보상을 청구했는지 여부에 따라 "통합된" 부채가 달라지므로 시간이 지남에 따라 발생하는 이자가 달라진다는 것을 의미합니다. 이로 인해 원래 부채와 담보가 동일한 두 트로브의 NICR이 시간이 지남에 따라 달라질 수 있습니다.

이러한 트로브의 잘못된 정렬 효과는 시간과 새로운 트로브가 개설되거나 기존 트로브가 정렬된 목록에 다시 삽입될 때 증폭됩니다. 결과적으로 청산 및 상환이 올바른 순서로 수행되지 않아 시스템의 핵심 기능 중 두 가지가 깨집니다.

## 권장 사항

이자 발생 메커니즘의 복잡성을 감안할 때 이 문제에 대한 간단한 해결책은 없는 것 같습니다. 팀은 트로브가 항상 올바르게 정렬되도록 이자 발생 메커니즘을 재설계하거나 제로 이자율을 부과하여 이 기능을 비활성화하는 것을 고려해야 합니다.

# [M-07] TroveManager는 비-18 소수점 토큰과 작동하지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`redeemCollateral()`에서 내부적으로 사용하는 `_redeemCollateralFromTrove()` 함수는 모든 담보 토큰이 부채 토큰과 일치하는 18 소수점을 갖는다고 가정합니다. 그러나 정밀도가 다른 토큰을 처리할 때 다음 공식으로 인해 잘못된 계산이 발생합니다.

```solidity
        singleRedemption.collateralLot = (singleRedemption.debtLot * DECIMAL_PRECISION) / _price;
```

`singleRedemption.debtLot`, `_price` 및 `DECIMAL_PRECISION`은 모두 18 소수점 값이므로 소수점이 다른 토큰에 대해 유효하지 않은 collateralLot이 발생합니다. 이로 인해 다음과 같은 문제가 발생합니다.

- 정밀도 불일치로 인한 언더플로.
- 잘못된 담보 잔액으로 인해 회계 계산 오류 발생.

또한 `_CollateralDrawn`이 담보의 정밀도와 일치해야 하는 반면 `_price` 및 `_totalDebtSupply`는 1e18 정밀도로 유지되므로 `_updateBaseRateFromRedemption()`도 영향을 받습니다.

## 권장 사항

모든 계산이 일관된 정밀도(예: 18 소수점)로 정규화되도록 하십시오.

# [L-01] `Staker.setRewardsFeeBps()`가 새 수수료가 100% 미만인지 확인하지 않음

보상 수수료가 100%보다 큰 값으로 설정되는 것을 방지하려면 새 수수료가 10,000 베이시스 포인트(bps) 이하인지 확인하는 것이 좋습니다.

```diff
    function setRewardsFeeBps(uint16 _rewardsFeeBps) external onlyOwner {
+       require(_rewardsFeeBps <= 10000);
        rewardsFeeBps = _rewardsFeeBps;
    }
```

# [L-02] `BGT`의 `activateBoostDelay`가 변경될 경우 부스트의 비효율적인 활성화

`Staker._tryToBoost()` 함수는 마지막 부스트 이후 8191 블록 이상이 경과한 경우에만 `BGT.activateBoost()` 및 `BGT.queueBoost()`를 호출합니다.

`BGT`의 `activateBoostDelay` 매개변수는 현재 8191로 설정되어 있지만 이 매개변수는 구성 가능하므로 나중에 변경될 수 있습니다. 결과적으로 부스트 활성화는 가능한 것보다 덜 자주 발생하여 사용자에게 더 적은 보상이 제공됩니다.

하드코딩된 값 8191을 사용하는 대신 `BGT` 계약에서 현재 `activateBoostDelay` 값을 확인하는 것을 고려하십시오. 또 다른 접근 방식은 `BGT` 계약이 `activateBoostDelay` 값을 변경할 때 소유자가 업데이트할 수 있는 `Staker` 계약의 스토리지 값을 사용하는 것입니다.

# [L-03] 반내림(rounding down)으로 인해 사용자가 더 적은 담보를 받을 수 있음

`TroveManager.redeemCollateral()`에서 트로브는 총 부채 금액이 상환되거나 트로브의 남은 부채가 최소 부채 금액 미만일 때까지 반복됩니다.

마지막 반복에서 `remainingDebt`가 너무 낮아 교환된 담보가 0으로 반내림될 수 있습니다.

사용자는 `remainingDebt` 부채 토큰 양에 대한 담보를 받지 못하지만 이러한 토큰은 잔액에서 소각되어 트로브의 총 부채를 줄이는 데 사용됩니다.

대가로 담보를받지 못하는 경우 사용자의 부채 토큰 소각(및 트로브 부채 감소)을 방지하기 위한 확인을 추가하는 것을 고려하십시오.

# [L-04] `StabilityPool`에서 활성화된 담보의 양이 제한되지 않음

`depositSums`, `collateralGainsByDepositor`, `epochToScaleToSums` 및 `lastCollateralError_Offset`의 배열 크기는 256입니다. 그러나 `enableCollateral` 함수에는 계약에서 이미 활성화된 총 담보 수에 대한 확인이 없습니다.

256개 이상의 담보가 활성화되면 위에 언급된 배열을 사용하는 작업은 범위 밖(out-of-bounds) 액세스로 인해 되돌려집니다.

`enableCollateral` 함수에서 활성화된 총 담보 수에 대한 확인을 추가하는 것을 고려하십시오.

# [L-05] 안정성 예금자에 의한 손실 회피

극단적인 시나리오에서 CDP의 담보 비율은 100% 아래로 떨어질 수 있습니다. 정상 모드 중에 이 문제가 발생하면 안정성 풀의 예금자는 청산 프로세스가 시작되기 전에 손실을 방지하기 위해 예금을 인출하도록 동기를 부여받을 수 있습니다.

청산 가능한 트로브가 있을 때 인출을 방지하기 위해 `StabilityPool.withdrawFromSP()`에 확인을 추가하는 것을 고려하십시오.

# [L-06] 담보 상환이 불필요하게 방지될 수 있음

`TroveManager`는 TCR이 MCR보다 낮을 때 담보 상환을 허용하지 않습니다.

Liquity V1 [문서](https://github.com/liquity/dev/tree/main?tab=readme-ov-file#lusd-token-redemption)에서 읽을 수 있듯이 이 확인의 목적은 많은 부채 토큰을 가진 공격자에 의해 트리거될 수 있는 TCR이 담보 부족에 가까울 때 페깅 해제 이벤트를 방지하는 것입니다.

그러나 Root의 구현에서는 글로벌 TCR이 특정 트로브의 MCR과 비교하여 확인됩니다.

```solidity
        require(IBorrowerOperations(borrowerOperationsAddress).getTCR() >= _MCR, "Cannot redeem when TCR < MCR");
```

다음 시나리오를 상상해 보십시오.

1. MCR이 145%인 변동성 담보 토큰에 대한 트로브가 있습니다.
2. 트로브의 TCR은 160%이므로 건전합니다.
3. 글로벌 TCR은 144%이므로 시스템은 페깅 해제 이벤트와 거리가 멉니다.

담보 상환을 막을 이유가 없지만 현재 구현에서는 글로벌 TCR이 트로브의 MCR보다 낮기 때문에 허용하지 않습니다.

확인을 위해 트로브의 MCR 대신 글로벌 MCR을 사용하고/하거나 트로브의 TCR을 트로브의 MCR과 비교하는 것을 고려하십시오.

# [L-07] `StabilityPool` 및 `BorrowerOperations`를 다시 초기화할 수 있음

`StabilityPool` 및 `BorrowerOperations` 계약의 `initialize()` 함수는 여러 번 호출되어 초기화 후 변경되지 않을 것으로 예상되는 중요한 상태 변수의 값을 변경할 수 있습니다.

이러한 함수는 신뢰할 수 있는 엔터티로 간주되는 소유자만 호출할 수 있지만 소유자의 기능은 다른 함수에서는 최소 요구 사항으로 권한을 줄이기 위해 제한됩니다. 예를 들면 다음과 같습니다.

```solidity
File: Staker.sol
    function setGauge(address _newGauge, address _validator, uint256 _boostThreshold) external onlyOwner {
@>      require(address(gauge) == address(0));
```

재초기화를 방지하기 위해 `initialize()` 함수에 확인을 추가하는 것이 좋습니다.

# [L-08] `Staker` 및 `TroveManager`는 비준수 ERC20 토큰과 작동할 수 없음

`Staker` 및 `TroveManager` 계약은 안전 확인 없이 `transfer()`, `transferFrom()` 및 `approve()`를 직접 호출합니다. 많은 ERC-20 토큰은 값을 반환하지 않아 표준에서 벗어나므로 이러한 호출이 되돌려집니다.

또한 `Staker.onWithdraw()` 함수는 `TroveMnager`에 의해 호출되어 사용자 담보를 반송합니다.

```solidity
    function onWithdrawal(address _to, uint256 _amount) external {
        require(msg.sender == address(troveManager), "Only trove manager");

        if (address(gauge) != address(0)) {
            gauge.withdraw(_amount);
        }

        asset.transfer(_to, _amount);
    }
```

- 일부 ERC20 토큰은 전송 함수 실패 시 되돌리지 않고 대신 bool 값을 반환합니다.
- 일부는 아무 값도 반환하지 않습니다. 따라서 값이 반환되었는지 확인하고 참이면 어떤 값인지 확인해야 합니다.
- `onWithdrawal()` 전송이 조용히 실패하면 자금은 Staker 계약 내에 남아 있으며 차용자는 이를 복구할 기회가 없습니다.

권장 사항: OpenZeppelin의 SafeERC20을 사용하여 호환성을 보장하고 예상치 못한 실패를 방지하십시오.

# [L-09] 전송 시 수수료(fee on transfer) 토큰이 지원되지 않음

BorrowerOperations 계약은 전송된 금액이 수신된 금액과 동일하다고 가정합니다.

```solidity
        // Move the collateral to the Trove Manager
        collateralToken.safeTransferFrom(msg.sender, address(troveManager), _collateralAmount);

        // Create the trove
        (vars.stake, vars.arrayIndex) =
            troveManager.openTrove(account, _collateralAmount, vars.compositeDebt, vars.NICR, _upperHint, _lowerHint);
```

그러나 이 가정은 특정 유형의 토큰에는 적용되지 않습니다.

- 전송 수수료 토큰: PAXG와 같은 일부 토큰은 전송 중에 수수료를 공제합니다. USDT와 같은 토큰도 수수료를 부과할 수 있는 기능이 있지만 현재는 그렇지 않습니다.
- stETH 코너 케이스: stETH와 같은 토큰에는 수신된 금액이 예상보다 약간 적은 [1-2 wei 불일치](https://docs.lido.fi/guides/lido-tokens-integration-guide/#1-2-wei-corner-case)가 있습니다.
- 리베이스 토큰: 일부 토큰은 동적으로 리베이스됩니다. 즉, 시간이 지남에 따라 잔액이 증가하거나 감소할 수 있습니다. stETH도 이 범주에 속합니다.

결과적으로 BorrowerOperations 내부 잔액이 예상보다 낮아져 작업이 실패할 수 있습니다. 정확성을 보장하려면 전송 전후의 계약 잔액을 측정하여 실제 수신된 금액을 계산하십시오.

# [L-10] `Staker` 함수의 누락된 이벤트

`setValidator`, `_updateRewardIntegral` 및 `claimReward`를 포함한 `Staker` 계약 내의 여러 중요한 함수에는 이벤트 방출이 없습니다.

계약의 관찰 가능성을 개선하고 오프체인 모니터링을 용이하게 하기 위해 언급된 함수에서 이벤트를 방출하는 것이 좋습니다.

# [L-11] `setValidator`에서 `boostThreshold` 확인 누락

Staker에는 하나의 `boostThreshold` 변수가 있습니다. 계약에 `boostThreshold`보다 더 많은 비부스트 BGT가 있으면 대기열에 넣고 부스트합니다. 문제는 `setValidator` 함수에서 `boostThreshold` 제한을 확인하지 않는다는 것입니다. 그러면 비부스트 BGT가 거의 없더라도(예: 몇 웨이) 이 비부스트 BGT를 대기열에 넣습니다.

부스트 지연이 하나 있다는 것을 고려하면 더 많은 BGT를 대기열에 넣기 위해 몇 시간을 기다려야 하며 예상보다 약간의 보상을 잃을 수 있습니다.

제안: `setValidator` 함수에 `boostThreshold` 확인을 추가하십시오.

# [L-12] 트로브 개설 또는 담보 추가가 차단될 수 있음

하나의 트로브를 열거나 하나의 트로브에 여분의 담보 토큰을 추가할 때 우리는 이러한 담보 토큰을 게이지(gauge)에 스테이킹합니다.

RewardVault(gauge)의 구현을 확인하면 `stake` 함수에는 하나의 `whenNotPaused` 수정자가 있습니다. 어떤 이유로 이 계약이 일시 중지되면 트로브 개설/담보 추가가 차단됩니다.

스테이크 동작은 더 많은 보상을 얻는 것을 목표로 합니다. 게이지가 일시 중지되면 정상적인 트로브 개설이나 담보 추가를 차단해서는 안 됩니다. 특히 사용자가 차용 포지션의 위험을 줄이기 위해 담보를 추가하려는 경우 사용자의 행동이 차단되어서는 안 됩니다.

# [L-13] `setValidator` 남용으로 인한 `Staker`의 부스트 활성화 지연 취약점

검증인 주소를 업데이트하기 위한 `Staker` 계약의 `setValidator` 함수에는 악의적인 소유자가 부스트 활성화를 지연시키거나 방지하기 위해 남용할 수 있는 의도하지 않은 부작용이 있습니다. 취약점은 `setValidator`가 `rewardCache.queueBoost(_newValidator, ...)`를 무조건 호출한다는 사실에 있습니다.

`RewardCache` 계약의 `queueBoost` 함수는 새 부스트가 실제로 대기열에 있는지 여부에 관계없이 호출될 때마다 `blockNumberLast` 타임스탬프를 업데이트합니다. `activateBoost` 함수는 대기열에 있는 부스트를 활성화하기 위해 `blockNumberLast` 이후의 시간 지연(`8191` 블록의 `blockBufferDelay`)에 의존합니다.

악의적인 소유자는 동일한 검증인 주소로 `setValidator`를 반복적으로 호출할 수 있습니다. 각 호출은 새로운 `queueBoost` 호출을 트리거하여 `blockNumberLast`를 현재 블록 번호로 재설정합니다. `blockNumberLast`를 지속적으로 재설정하면 `blockDelta`(현재 블록과 `blockNumberLast`의 차이)가 필요한 `8191` 블록의 `blockBufferDelay`를 초과하지 못합니다. 결과적으로 `activateBoost` 함수가 호출되지 않아 부스트 활성화 메커니즘이 사실상 중단됩니다.

이 취약점은 부스트 활성화를 지연시키거나 방지함으로써 스테이커에게 부정적인 영향을 미칠 수 있습니다.

권장 사항:

불필요한 대기열을 방지하려면 `blockDelta > 8191` 조건을 `setValidator`에서 검증해야 합니다. 이렇게 하면 `_tryBoost` 실행의 대기열 프로세스가 완료되고 필요한 지연 기간이 경과한 후에만 검증인 마이그레이션이 가능해집니다. 또한 소유자가 `setValidator`를 실행하기 전에 `_tryBoost`가 다시 실행되지 않도록 "검증인 마이그레이션 프로세스"를 시작해야 한다고 생각합니다.

또한 `_newValidator` 주소가 현재 `validator`와 동일할 때 `rewardCache.queueBoost`를 호출하지 않도록 `Staker` 계약의 `setValidator` 함수를 수정하십시오.
