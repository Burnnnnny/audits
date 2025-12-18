# 소개

**pashov**가 **Topia Staking** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **Topia Staking** 소개

이 검토의 계약은 유동성 제공자가 LP 토큰을 스테이킹하여 일정 기간 동안 유동성을 잠그고 잠금 기간이 끝난 후 이에 대한 수익(보상)을 받을 수 있도록 합니다.

사용 사례 시나리오:

1. Alice는 `TOPIA/ETH` Uniswap V2 유동성 풀에 유동성을 제공하여 지갑에 LP 토큰을 발행합니다.
2. Alice는 `TopiaLpStaking`에 LP 토큰을 스테이킹하고 스테이크의 기간(잠금 시간)을 선택합니다.
3. 특별한 공식이 각 포지션의 가중치와 포지션 수에 따라 보상률과 기간을 계산합니다.
4. Alice는 스테이킹 잠금 시간이 끝난 후에만 보상을 청구할 수 있습니다. LP 토큰을 일찍 언스테이킹하면 보상을 0으로 받게 됩니다.
5. 스테이킹 포지션은 사용자가 언스테이킹 하지 않았더라도 스테이킹 기간이 끝나면 보상을 산출하지 않습니다.
6. Alice는 동시에 여러 스테이킹 포지션을 보유할 수 있습니다.

## 관찰 사항

## 특권 역할 및 행위자

- Staking owner - 보상, 보상/스테이킹 토큰을 관리하고 잠금 간격을 추가할 수 있습니다.
- LP staker - LP 토큰을 스테이킹하고 `rewardToken`으로 수익을 기대할 수 있습니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

**영향(Impact)** - 성공적인 공격의 기술적, 경제적, 평판적 손해
**가능성(Likelihood)** - 특정 취약점이 발견되고 악용될 확률
**심각도(Severity)** - 위험의 전반적인 중요도

# 보안 평가 요약

**_검토 커밋 해시_ - [fac6a6a3c751fb01bb3125fce14065099792a579](https://github.com/Topia-Game/token-lp-staking/tree/fac6a6a3c751fb01bb3125fce14065099792a579)**

**_수정 검토 커밋 해시_ - [277815bcf6150be906fc8006b945c1736f2f71ab](https://github.com/Topia-Game/token-lp-staking/tree/277815bcf6150be906fc8006b945c1736f2f71ab)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `ITopiaLpStaking`
- `TopiaLpStaking`

다음과 같은 수의 문제가 발견되었으며 심각도별로 분류되었습니다:

- 치명적 & 높음: 1개 문제
- 중간: 3개 문제
- 낮음: 1개 문제

---

# 결과 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | 보상 계산 오류로 인해 사용자의 보상이 0이 됨 | 치명적 |
| [M-01] | 여러 중앙화 취약점이 프로토콜을 손상시킬 수 있음 | 중간 |
| [M-02] | 보상이 계약에 갇힐 가능성이 있음 | 중간 |
| [M-03] | 스테이킹이 비표준 ERC20 토큰과 올바르게 작동하지 않음 | 중간 |
| [L-01] | 곱셈 전 나눗셈으로 인한 정밀도 손실 | 낮음 |

# 상세 결과

# [C-01] 보상 계산 오류로 인해 사용자의 보상이 0이 됨

## 심각도

**영향:**
높음, 사용자가 보상을 잃을 것이기 때문

**가능성:**
높음, 거의 항상 발생할 것이기 때문

## 설명

`getUserStakeReward`에서 보상을 계산하는 공식은 다음과 같습니다:

```solidity
rewardsPerWeight_.accumulated = (rewardsPerWeight_.accumulated +
    (unaccountedTime * rewardsPerWeight_.rate) / rewardsPerWeight_.totalWeight).toUint96();
```

`updateRewardsPerWeight`에서도 동일합니다. 이 코드의 문제는 `(unaccountedTime * rewardsPerWeight_.rate) / rewardsPerWeight_.totalWeight`가 거의 항상 0으로 버림 된다는 것입니다. `totalWeight`와 `rate`가 모두 18 소수점 토큰으로 측정되므로(예상), 더 많은 사용자가 스테이킹할수록 `totalWeight`가 정적 `rate`보다 훨씬 더 커질 가능성이 높습니다. `unaccountedTime` 변수는 마지막 스테이크/언스테이크 이벤트 이후 몇 초가 지났는지만 보유하며, 이는 항상 꽤 작은 숫자(1일은 86400초, 작은 5자리 숫자)입니다. 이제 `(unaccountedTime * rewardsPerWeight_.rate)`가 `rewardsPerWeight_.totalWeight`보다 작으면 이 수학은 0으로 버림 되고 `rewardsPerWeight_.accumulated`는 동일한 값으로 유지됩니다. 즉, 사용자에게 배포될 새로운 보상이 더 이상 축적되지 않습니다.

## 권장 사항

`getUserStakeReward`와 `updateRewardsPerWeight` 모두에서 코드를 다음과 같이 변경하십시오:

```diff
- (unaccountedTime * rewardsPerWeight_.rate) / rewardsPerWeight_.totalWeight
+ (unaccountedTime * rewardsPerWeight_.rate).divWadDown(rewardsPerWeight_.totalWeight)
```

그리고 `getUserStakeReward`에서도 코드를 다음과 같이 변경하십시오:

```diff
- return getUserStakeWeight(userStake) * (rewardsPerWeight_.accumulated - userStake.checkpoint);
+ return getUserStakeWeight(userStake).mulWadDown(rewardsPerWeight_.accumulated - userStake.checkpoint);
```

[Solmate의 FixedPointMathLib](https://github.com/transmissions11/solmate/blob/main/src/utils/FixedPointMathLib.sol)를 사용하여.

## 논의

**pashov:** 수정됨(Fixed).

# [M-01] 여러 중앙화 취약점이 프로토콜을 손상시킬 수 있음

## 심각도

**영향:**
높음, 사용자가 스테이크를 인출하거나 보상을 청구하지 못할 수 있기 때문

**가능성:**
낮음, 타협되거나 악의적인 소유자가 필요하기 때문

## 설명

`TopiaLpStaking`의 여러 메서드는 중앙화 취약점이며 일부는 사용자를 위해 프로토콜을 손상시키는 데 사용될 수 있습니다:

- `setRewardsToken`은 `rewardsToken` 주소를 임의의 주소로 업데이트하는 데 사용될 수 있으며, 모든 보상 청구를 되돌리게(revert) 하여 자금 동결로 이어질 수 있습니다.
- `setUniswapPair`는 `uniswapPair` 주소를 임의의 주소로 업데이트하는 데 사용될 수 있으며, 모든 스테이크/언스테이크를 되돌리게 하여 자금 동결로 이어질 수 있습니다.
- `addLockupInterval`은 거대한 배수(multipliers)를 가진 이상한 잠금 간격을 추가하는 데 사용될 수 있습니다.

`setRewards` 메서드 자체에도 여러 가지 문제가 있습니다:

- 여러 번 호출되어 호출할 때마다 보상 기간을 밀어낼 수 있습니다.
- `_start` 값이 너무 먼 미래일 수 있습니다.
- `_end` 값이 이미 지났을 수 있습니다.
- `_start`와 `_end` 사이의 기간이 너무 클 수 있습니다(예: 70년).
- 보상 토큰의 계약 잔액이 검증되지 않습니다. 이는 사용자가 보상을 청구할 수 있다는 보장이 없음을 의미할 수 있습니다.

## 권장 사항

`rewardsToken`, `uniswapPair` 및 `lockupIntervals`를 불변(immutable) 변수로 만드십시오. 변경할 필요가 없어야 합니다. 또한 `setRewards`가 한 번만 호출될 수 있도록 하십시오.

## 논의

**pashov:** 수정됨(Fixed).

# [M-02] 보상이 계약에 갇힐 가능성이 있음

## 심각도

**영향:**
높음, 프로토콜/사용자에게 가치 손실이기 때문

**가능성:**
낮음, 특별한 시나리오가 존재해야 하기 때문

## 설명

현재 `TopliaLpStaking::setRewards`에는 다음과 같은 주석이 있습니다:

> // This must be called AFTER some LP are staked (or ensure at least 1 LP position is staked before the start timestamp)

문제가 여기서 지적되었지만 스마트 계약 네이티브 방식으로 강제되지 않으며 코드는 여전히 취약합니다. 문제는 `rewardsPeriod.start` 타임스탬프가 지났고 아무도 스테이킹하지 않은 경우, 첫 번째 스테이크까지 축적된 보상은 계약에 영원히 갇히게 된다는 것입니다. 이는 `stake` 메서드가 실제로 스테이커의 체크포인트를 설정하기 전에 `updateRewardsPerWeight`를 호출하기 때문입니다.

## 권장 사항

`rewardsPeriod.start` 이전에 적어도 1명의 사용자가 스테이킹 했음을 보장하는 메커니즘을 추가하십시오. 한 가지 가능한 해결책은 `setRewards`를 호출하기 전에 적어도 하나의 스테이크가 있었고 `_start >= block.timestamp`인지 강제하는 것입니다.

## 논의

**pashov:** 수정됨(Fixed).

# [M-03] 스테이킹이 비표준 ERC20 토큰과 올바르게 작동하지 않음

## 심각도

**영향:**
높음, 가치 손실로 이어질 수 있기 때문

**가능성:**
낮음, 이러한 토큰은 흔하지 않음

## 설명

일부 토큰은 `transfer` 또는 `transferFrom` 실패 시 되돌리지(revert) 않고 대신 `false`를 반환합니다(예: [ZRX](https://etherscan.io/address/0xe41d2489571d322189246dafa5ebde1f4699f498#code)). 이러한 토큰은 기술적으로 표준을 준수하지만 `transfer`/`transferFrom` 호출의 반환 값을 확인하는 것을 잊는 것은 흔한 문제입니다. 현재 코드로 인해 이러한 호출이 실패했지만 되돌아가지 않으면 사용자가 원했음에도 불구하고 보상을 청구하지 않고 언스테이킹하는 결과를 초래할 수 있습니다. 그 보상은 계약에 영원히 갇히게 됩니다.

일부 토큰은 수수료 부과(fee-on-transfer) 메커니즘도 구현합니다. 즉, `stake` 시 계약 잔액으로 전송되는 실제 가치는 `_lpAmount`가 아니라 `_lpAmount - fee`가 됩니다. 이는 `unstake` 시 계약의 잔액 부족으로 인해 마지막으로 호출하는 사용자의 트랜잭션이 되돌아가기 때문에 문제가 될 것입니다.

낮은 소수점 토큰은 `setRewards`와 작동하지 않습니다. 이 메서드는 초당 보상으로 최소 10e18 가치의 보상 토큰을 요구하는데, 스테이블 코인의 경우 미친 일일 보상률이 되며 장기간 충족하기 거의 불가능하기 때문입니다. ETH나 BTC와 같이 고가치 토큰을 사용하면 더 나빠질 것입니다.

README가 스테이킹 토큰이 `TOPIA/ETH` Uniswap V2 LP 토큰이 되고 보상 토큰이 `TOPIA`가 될 것임을 암시하므로 이것들이 문제가 되지 않을 것으로 예상되지만, 현재 계약에는 두 토큰을 모두 업데이트하는 메커니즘이 있으며 스테이킹 계약과 호환되지 않는 토큰을 사용하는 공격 벡터를 엽니다.

## 권장 사항

OpenZeppelin의 `SafeERC20` 라이브러리와 ERC20 전송을 위한 `safe` 메서드를 사용하십시오. 수수료 부과 토큰의 경우 예치(`stake`) 전후의 잔액을 확인하고 그 차이를 실제 전송된 가치로 사용하십시오. `setRewards`에서 더 낮은 보상률을 허용하는 것을 고려하십시오.

아니면 `setRewardsToken` 및 `setUniswapPair` 메서드를 제거할 수도 있습니다.

## 논의

**pashov:** 수정됨(Fixed).

# [L-01] 곱셈 전 나눗셈으로 인한 정밀도 손실

`estimateStakeReward` 메서드는 곱셈 전에 나눗셈을 수행하여 Solidity에서 불필요한 정밀도 손실을 초래합니다. 이는 보상 예측을 보고 싶어 하는 사용자의 프론트엔드에서 잘못된 추정으로 이어져, 있어야 할 것보다 적게 표시될 것입니다. 곱셈이 나눗셈보다 먼저 오도록 코드를 만드십시오.

## 논의

**pashov:** 수정됨(Fixed).

