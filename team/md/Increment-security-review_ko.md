# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **peripheral-contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Increment Finance 소개 (About Increment Finance)

Increment Protocol을 위한 주변(Peripheral) 스마트 계약들입니다. Increment 프로토콜은 유동성을 위해 풀링된 담보 기반 가상 자산을 사용하고 거래 실행을 위해 Curve V2의 AMM(Automated Market Maker)을 활용하는 자율적인 비업그레이드형 스마트 계약 시스템을 도입합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 희박하거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [d62bba59f3bd15b47f32ff417a29b61667a7727c](https://github.com/Increment-Finance/peripheral-contracts/tree/d62bba59f3bd15b47f32ff417a29b61667a7727c)**

**_수정 검토 커밋 해시_ - [bc56572bf73a534500e79a6944399acc6e3c4b37](https://github.com/Increment-Finance/peripheral-contracts/tree/bc56572bf73a534500e79a6944399acc6e3c4b37)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `AdminControlledEcosystemReserve`
- `AuctionModule.sol`
- `EcosystemReserve`
- `PerpRewardDistributor`
- `RewardController`
- `RewardDistributor`
- `SMRewardDistributor`
- `SafetyModule`
- `StakedToken`
- `IAdminControlledEcosystemReserve`
- `IPerpRewardDistributor`
- `IRewardController`
- `IRewardDistributor`
- `ISMRewardDistributor`
- `ISafetyModule`
- `IStakedToken`

# 발견 사항 (Findings)

# [C-01] 상환(Redeem) 기간이 의도한 것보다 짧아 0이 될 수 있음

## 심각도

**영향:** 높음 (High), 쿨다운 기간이 언스테이크 창(unstake window)보다 2배 더 긴 경우 StakedTokens를 상환할 수 없으므로 기본 토큰이 영구적으로 갇히게 됩니다.

**가능성:** 높음 (High), 상환 시 계산 실수

## 설명

StakedToken을 상환하려면 사용자는 `cooldown()`에 요청을 제출하고 `COOLDOWN_SECONDS`의 시간을 기다려야 합니다.
그런 다음 쿨다운 후 `UNSTAKE_WINDOW` 기간 동안 상환할 수 있어야 합니다.

그러나 이 검사는 오픈 윈도우를 `2 * COOLDOWN_SECONDS`만큼 과소평가합니다:

```solidity
    function _redeem(address from, address to, uint256 amount) internal {
        ...

        // Users can redeem without waiting for the cooldown period in a post-slashing state
        if (!isInPostSlashingState) {
            // Make sure the user's cooldown period is over and the unstake window didn't pass
            uint256 cooldownStartTimestamp = _stakersCooldowns[from];
            if (block.timestamp < cooldownStartTimestamp + COOLDOWN_SECONDS) {
                revert StakedToken_InsufficientCooldown(cooldownStartTimestamp + COOLDOWN_SECONDS);
            }
@>          if (block.timestamp - cooldownStartTimestamp + COOLDOWN_SECONDS > UNSTAKE_WINDOW) {
                revert StakedToken_UnstakeWindowFinished(cooldownStartTimestamp + COOLDOWN_SECONDS + UNSTAKE_WINDOW);
            }
        }

        // ... redeem logic
    }
```

[여기서 PoC를 확인할 수 있습니다](https://gist.github.com/T1MOH593/e0a5424ca14facb3661b78c3d14530b1)

## 권장 사항

검사를 다음과 같이 리팩토링하십시오:

```diff
-          if (block.timestamp - cooldownStartTimestamp + COOLDOWN_SECONDS > UNSTAKE_WINDOW) {
+          if (block.timestamp - cooldownStartTimestamp - COOLDOWN_SECONDS > UNSTAKE_WINDOW) {
```

# [H-01] `StakedToken`은 기부(donation)를 통한 지분 인플레이션 공격에 취약함

## 심각도

**영향:** 높음 (High), 대상 스테이커가 자금을 잃게 됨

**가능성:** 중간 (Medium), 모든 스테이커가 지분을 상환했을 때 가능함

## 설명

`StakedToken`은 기본 토큰(자산)을 스테이킹하여 스테이킹된 토큰(지분)을 얻을 수 있게 합니다. 이는 지분 가격 계산을 위해 명시적인 `exchangeRate`를 사용하며, 이는 슬래싱(slashing) 또는 자금 반환 시 업데이트됩니다.

`StakedToken`에서 `UNDERLYING_TOKEN.balanceOf(address(this))`를 사용하여 `exchangeRate`가 업데이트되므로, `StakedToken` 계약으로 직접 기본 토큰을 보내는 기부를 통해 조작될 수 있습니다.

```Solidity
    function returnFunds(address from, uint256 amount) external onlySafetyModule {

        if (amount == 0) revert StakedToken_InvalidZeroAmount();
        if (from == address(0)) revert StakedToken_InvalidZeroAddress();

        // Update the exchange rate
        //@audit the exchange rate can be manipulated via direct donation due to the use of balanceOf()
        _updateExchangeRate(UNDERLYING_TOKEN.balanceOf(address(this)) + amount, totalSupply());

        // Transfer the underlying tokens back to this contract
        UNDERLYING_TOKEN.safeTransferFrom(from, address(this), amount);
        emit FundsReturned(from, amount);
    }

```

공격자는 다음 시나리오에서 문제를 악용할 수 있습니다:

1. 기본 토큰의 50%가 슬래싱되는 슬래시 이벤트가 발생하여 환율이 1:2(자산 대 지분)가 된다고 가정합니다.
2. 남은 1 wei 기본 토큰에 대해 2 wei 지분(`StakedToken`)을 가진 공격자를 제외한 모든 스테이커가 자신의 지분을 상환했습니다.
3. 그동안 공격자는 주기적으로 쿨다운을 활성화하여 쿨다운 기간과 언스테이크 창 내에 머물러 필요할 때 상환할 수 있도록 합니다.
4. 거버넌스가 `SafetyModule._returnFunds()`를 호출하고, 이는 간접적으로 `StakedToken._updateExchangeRate()`를 호출합니다. 이는 경매가 완료되거나 거버넌스가 수동으로 `StakedToken`에 자금을 반환할 때 발생할 수 있습니다.
5. 이제 피해자가 `1000e18` 기본 토큰을 `StakedToken`에 스테이킹하려고 시도합니다.
6. 공격자가 멤풀에서 tx 5와 tx 6을 관찰하면, `StakedToken`에 `1000e18*2`를 직접 기부하여 지분 가격을 부풀리기 위해 프론트런(front-run)합니다. 이로 인해 `exchangeRate = (1000e18*2+1)/2`가 됩니다.
7. 이제 피해자는 tx 5에 대해 0 지분을 받게 됩니다. `stakeAmount = amountToStake/exchangeRate = 1000e18/((1000e18*2+1)/2) = 0`이기 때문입니다.
8. 그런 다음 공격자는 피해자로부터 훔쳐서 `1000e18` 기본 토큰의 이익으로 자신의 2 wei 지분을 상환할 수 있습니다. 기부 비용도 상환으로 회수됩니다.

## 권장 사항

`StakedToken`에서 기본 토큰에 대한 내부 잔액 추적을 구현하십시오.

이 완화 조치는 또한 `returnFunds()`와 같이 지분을 늘리지 않고 기본 토큰 잔액을 늘리는 함수에 대해 `total underlying token balance <= total shares`라는 불변성을 구현해야 합니다. 그렇지 않으면 공격자가 스테이킹을 통해 스텔스 기부를 수행할 수 있습니다. 예를 들어 환율이 2:1(자산 대 지분)일 때 1 wei 기본 토큰을 스테이킹하면 0 지분이 발행되는데, 이는 1 wei를 기부하고 지분 수는 동일하게 유지하면서 지분 가격을 부풀리는 효과가 있습니다.

또한 이는 리베이스(rebasing) 토큰에서는 작동하지 않으며, `StakedToken`을 통하지 않은 직접적인 기본 토큰 전송을 통한 의도하지 않은 기부를 위한 구제 자금(rescue fund) 기능이 필요할 수 있습니다.

# [H-02] 스테이커는 대리 적립(accrual on behalf)으로 인해 추가 보상을 잃을 수 있음

## 심각도

**영향:** 중간 (Medium), 미지급 보상을 축적하고 최대 보상 배율로 청구하려는 사용자의 추가 보상 손실

**가능성:** 높음 (High), 누구나 대리로 보상을 청구할 수 있으므로 항상 발생함

## 설명

스테이커에게는 장기간 스테이킹을 유지/증가하도록 장려하는 보상 배율이 할당됩니다. 보상 배율은 시간이 지남에 따라 증가하며, 스테이커가 보상을 청구하거나 스테이크 포지션을 변경할 때 미지급 보상에 적용됩니다.

이 설계는 스테이커가 한 번 스테이킹하고 보상 배율이 최대값에 도달했을 때만 청구함으로써 보상을 극대화할 수 있도록 합니다. 즉, 스테이커가 청구를 진행할 때 미지급 보상에 최대 보상 배율이 적용됩니다.

그러나 스테이커가 다른 사람으로부터 1 wei의 더스트(dust) 스테이크 토큰을 받으면 `SMRewardDistributor.updatePosition()`이 호출되어 미지급 보상의 축적이 중단될 수 있습니다. 누군가 스테이커를 대신하여 `claimRewardsFor()`를 트리거할 때도 동일한 중단이 발생합니다. 두 작업 모두 보상의 적립(accrual)을 트리거하고 해당 시점의 보상 배율을 적용하여, 스테이커가 최대 보상 배율로 보상을 극대화하는 것을 방지합니다.

또한 다른 사람으로부터 스테이크 토큰을 받으면 `_multiplierStartTimeByUser`가 지연되지만, 토큰 가중치 계산으로 인해 더스트 전송의 경우 영향은 적습니다.

```Solidity
    function updatePosition(address market, address user) external virtual override {
        ...

        // Accrue rewards to the user for each reward token
        uint256 rewardMultiplier = computeRewardMultiplier(user, market);
        uint256 numTokens = rewardTokens.length;
        for (uint256 i; i < numTokens;) {
            address token = rewardTokens[i];
            ...

            //@audit When staker receives new staked tokens, this will trigger the reward accrual
            //      and apply the current reward multiplier.
            uint256 newRewards = prevPosition.mul(
                _cumulativeRewardPerLpToken[token][market] - _cumulativeRewardPerLpTokenPerUser[user][token][market]
            ).mul(rewardMultiplier);

            // Update the user's stored accumulator value
            _cumulativeRewardPerLpTokenPerUser[user][token][market] = _cumulativeRewardPerLpToken[token][market];
        }
            ...

            //@audit a transfer by someone else will also delay the multiplier start time
            _multiplierStartTimeByUser[user][market] += (block.timestamp - _multiplierStartTimeByUser[user][market]).mul(
                (newPosition - prevPosition).div(newPosition)
            );
            ...
    }

    function claimRewardsFor(address _user, address[] memory _rewardTokens)
        public
        override
        nonReentrant
        whenNotPaused
    {
        uint256 numMarkets = _getNumMarkets();
        for (uint256 i; i < numMarkets;) {

            //@audit When someone claims reward on behalf of staker, it will trigger
            //      the reward accrual and apply the current reward multiplier.
            _accrueRewards(_getMarketAddress(_getMarketIdx(i)), _user);

            ...
        }
        ...
    }
```

다음 시나리오를 가정해 보십시오:

1. Alice는 1000 기본 토큰을 스테이킹하고 보상 배율이 최대값에 도달할 때까지 50일을 기다린 후 보상을 청구하려고 합니다.
2. 25일째 되는 날, Bob이 Alice를 방해하기 위해 1 wei의 스테이크 토큰을 Alice에게 전송하여 미지급 보상의 축적을 중단합니다.
3. 전송은 `SMRewardDistributor.updatePosition()`을 트리거하고 25일째의 보상 배율 값으로 축적된 보상을 적립합니다. 보상 배율이 최대값에 도달하지 않았으므로 이는 Alice의 의도에 반합니다.
4. 50일째 되는 날, Alice의 보상 배율이 최대값에 도달하여 보상을 청구합니다. 그러나 Bob이 3단계에서 이미 이전 적립을 트리거했으므로 25일 이후의 적립된 보상에만 최대 보상 배율이 적용됩니다.
5. Bob의 행동으로 인해 Alice는 50일째에 최대 보상 배율에 도달했을 때 전체 보상 적립이 수행되었다면 얻었을 추가 보상을 잃었습니다.

참고: Bob은 Alice의 보상을 더욱 줄이기 위해 동일한 전송을 더 자주 반복할 수도 있습니다. `claimRewardsFor()`를 사용해서도 동일한 중단을 달성할 수 있습니다.

## 권장 사항

첫째, 대리 보상 청구를 방지하고 스테이커 본인만 청구할 수 있도록 허용하십시오.
둘째, 수신자가 잃어버린 보상보다 전송으로 인해 더 많은 혜택을 볼 수 있도록 최소 전송 금액을 설정하십시오.

# [H-03] 보상 토큰 제거 후 사용자가 모든 보상을 청구할 수 없음

## 심각도

**영향:** 높음 (High), 보상을 청구할 수 없는 사용자가 항상 존재함

**가능성:** 중간 (Medium), 토큰 제거는 일반적인 작업은 아니지만 가능함

## 설명

적립되었지만 아직 청구되지 않은 보상만 예비로 남겨두고 나머지 부분을 거버넌스로 전송하는 `removeRewardToken()` 메서드가 있습니다. 문제는 이 메서드가 보류 중이지만 아직 적립되지 않은(pending but not yet accrued) 보상을 고려하지 않는다는 것입니다.

관련 코드 블록:

```solidity
    function removeRewardToken(address _rewardToken) external onlyRole(GOVERNANCE) {
        ...
        // Determine how much of the removed token should be sent back to governance
        uint256 balance = _rewardTokenBalance(_rewardToken);
        uint256 unclaimedAccruals = _totalUnclaimedRewards[_rewardToken];
        uint256 unaccruedBalance;
        if (balance >= unclaimedAccruals) {
            unaccruedBalance = balance - unclaimedAccruals;
            // Transfer remaining tokens to governance (which is the sender)
            IERC20Metadata(_rewardToken).safeTransferFrom(ecosystemReserve, msg.sender, unaccruedBalance);
        }
    }
```

변수 `_totalUnclaimedRewards`는 보상 청구 및 포지션 업데이트 시에만 업데이트되므로 보류 중인 보상을 포함하지 않습니다.

## 권장 사항

토큰 제거 후 사용자에 대한 현재 보상을 충당할 수 있도록 예비비에 충분한 토큰을 남겨두십시오. 그러나 사용자에 따라 달라 미리 계산할 수 없는 `earlyWithdrawalPenalty` 및 `rewardMultiplier`로 인해 수정이 명확하지 않습니다.

# [H-04] 그리퍼(Griefer)가 사용자의 배율을 1로 재설정할 수 있음

## 심각도

**영향:** 높음 (High), 특정 사용자의 배율이 공격자의 의지에 따라 영구적으로 1로 유지될 수 있어 사용자의 보상이 여러 번 감소함

**가능성:** 중간 (Medium), 공격자가 이를 수행하여 얻을 수 있는 직접적인 이익은 없지만, 전제 조건이 없음

## 설명

`SMRewardDistributor.sol`에서 배율은 여러 상황에서 재설정되어야 합니다:

```solidity
        if (prevPosition == 0 || newPosition <= prevPosition) {
            // Removed stake, started cooldown or staked for the first time - need to reset reward multiplier
            if (newPosition != 0) {
                /**
                 * Partial removal, cooldown or first stake - reset multiplier to 1
                 * Rationale:
                 * - If prevPosition == 0, it's the first time the user has staked, naturally they start at 1
                 * - If newPosition < prevPosition, the user has removed some or all of their stake, and the multiplier
                 *   is meant to encourage stakers to keep their tokens staked, so we reset the multiplier to 1
                 * - If newPosition == prevPosition, the user has started their cooldown period, and to avoid gaming
                 *   the system by always remaining in either the cooldown or unstake period, we reset the multiplier
                 */
                _multiplierStartTimeByUser[user][market] = block.timestamp;
            } else {
                // Full removal - set multiplier to 0 until the user stakes again
                delete _multiplierStartTimeByUser[user][market];
            }
```

그러나 `newPosition == prevPosition` 조건은 쿨다운뿐만 아니라 2가지 추가 상황에서도 트리거될 수 있습니다:

1. `destination` 주소로 0을 전송하면 `destination` 사용자의 배율이 1로 재설정됩니다. 이는 그리핑(griefing)을 도입합니다.
2. 사용자가 자신에게 토큰을 전송하면 배율이 재설정되지만, 자연스럽게 그는 포지션을 조정하지 않았으므로 배율은 동일하게 유지되어야 합니다.

## 권장 사항

조건에서 `=`를 제거하고 대신 `isStartCooldown()` 플래그를 사용하여 `cooldown()` 함수에서 이를 true로 설정하십시오.

```diff
-   function updatePosition(address market, address user) external virtual override {
+   function updatePosition(address market, address user, bytes calldata data) external virtual override {
+   bool isStartCooldown = abi.decode(data, (bool));
        ...
-       if (prevPosition == 0 || newPosition <= prevPosition) {
+       if (prevPosition == 0 || newPosition < prevPosition) {
            // Removed stake, started cooldown or staked for the first time - need to reset reward multiplier
            if (newPosition != 0) {
                /**
                 * Partial removal, cooldown or first stake - reset multiplier to 1
                 * Rationale:
                 * - If prevPosition == 0, it's the first time the user has staked, naturally they start at 1
                 * - If newPosition < prevPosition, the user has removed some or all of their stake, and the multiplier
                 *   is meant to encourage stakers to keep their tokens staked, so we reset the multiplier to 1
                 * - If newPosition == prevPosition, the user has started their cooldown period, and to avoid gaming
                 *   the system by always remaining in either the cooldown or unstake period, we reset the multiplier
                 */
                _multiplierStartTimeByUser[user][market] = block.timestamp;
            } else {
                // Full removal - set multiplier to 0 until the user stakes again
                delete _multiplierStartTimeByUser[user][market];
            }
+       } else if (isStartCooldown) {
+           _multiplierStartTimeByUser[user][market] = block.timestamp;
        } else {
            /**
             * User added to their existing stake - need to update multiplier start time
             * Rationale:
             * - To prevent users from gaming the system by staked a small amount early to start the multiplier and
             *   then staked a large amount once their multiplier is very high in order to claim a large reward.
             * - We shift the start time of the multiplier forward by an amount proportional to the ratio of the
             *   increase in stake, i.e., `newPosition - prevPosition`, to the new position. By shifting the start
             *   time forward we reduce the multiplier proportionally in a way that strongly disincentivizes this
             *   bad behavior while limiting the impact on users who are genuinely increasing their stake.
             */
            _multiplierStartTimeByUser[user][market] += (block.timestamp - _multiplierStartTimeByUser[user][market]).mul(
                (newPosition - prevPosition).div(newPosition)
            );
        }
...
    }
```

# [H-05] 경매에 갇힌 기본 토큰

## 심각도

**영향:** 높음 (High), `기본 토큰`이 `staked token` 계약으로 반환될 수 없어 해당 기본 토큰이 `AuctionModule` 계약 내에 갇히게 되므로. 또한 경매를 닫을 수 없어 전체 로트(lot)를 구매할 수 없습니다. `isInPostSlashingState`가 true이므로 스테이킹할 수 없어 `staked token`은 사용할 수 없게 됩니다.

**가능성:** 중간 (Medium), 사용자가 모든 토큰을 상환하는 데 제한이 없으므로. 사용자가 모든 토큰을 상환하기로 결정하면 `staked token`의 공급량은 0이 됩니다.

## 설명

지급 불능 이벤트 동안 `governance`는 `SafetyModule::slashAndStartAuction` 기능을 사용하여 `StakedToken` 계약에서 `기본 토큰`을 가져와 경매에 부칠 수 있습니다. 이 작업은 기본 토큰을 `AuctionModule.sol` 계약으로 전송하여 경매를 시작합니다.

그 후 `AuctionModule::_completeAuction` 함수를 사용하여 경매를 종료할 수 있습니다. 이 함수는 모든 기본 토큰이 `AuctionModule::buyLots`에서 경매되거나, 경매가 만료되어 누군가 `AuctionModule::completeAuction` 함수로 경매를 종료하기로 결정하거나, 거버넌스가 `SafetyModule::terminateAuction` 함수로 경매를 조기에 종료하기로 결정할 때 호출할 수 있습니다.

문제는 경매 과정 중 `staked tokens` 상환에 대한 제한이 없을 때 발생합니다. 사용자는 완전히 종료하고 모든 토큰을 상환할 수 있습니다. 나중에 경매를 닫으려고 할 때 `0으로 나누기 오류로 인해 실패`합니다. 이는 `AuctionModule::_completeAuction`이 호출될 때 `StakedToken::returnFunds`를 호출한 다음 `StakedToken::_updateExchangeRate` 함수를 사용하여 `exchange rate`를 업데이트하기 때문입니다. 이 함수는 `totalSupply()`로 나누기를 수행하는데, 이는 0입니다(코드 라인 `StakedToken#L240`):

```solidity
File: StakedToken.sol
235:     function returnFunds(address from, uint256 amount) external onlySafetyModule {
236:         if (amount == 0) revert StakedToken_InvalidZeroAmount();
237:         if (from == address(0)) revert StakedToken_InvalidZeroAddress();
238:
239:         // Update the exchange rate
240:         _updateExchangeRate(UNDERLYING_TOKEN.balanceOf(address(this)) + amount, totalSupply());
241:
242:         // Transfer the underlying tokens back to this contract
243:         UNDERLYING_TOKEN.safeTransferFrom(from, address(this), amount);
244:         emit FundsReturned(from, amount);
245:     }
```

다음 시나리오를 고려해 보십시오:

1. `UserA`가 `100e18 underlyingTokens`를 스테이킹하고 `100e18 staked tokens`를 받습니다.
2. 지급 불능 이벤트가 발생하고 `20e18 underlyingTokens` 경매가 시작됩니다.
3. `UserA`는 모든 토큰을 상환하기로 결정하여 `stakedToken.totalSupply=0`이 됩니다.
4. 경매가 종료되고 `AuctionModule::_completeAuction`이 호출되지만 `0으로 나누기 오류`로 인해 닫을 수 없습니다.
5. 마지막 로트는 구매할 수 없습니다. `AuctionModule::buyLots` 함수가 경매를 종료하려고 시도하여 `0으로 나누기 오류`로 인해 트랜잭션이 되돌려지기 때문입니다.
6. 스테이킹이 더 이상 불가능(`isInPostSlashingState`가 true)하므로 `staked token`은 사용할 수 없게 됩니다.
7. 경매되지 않은 `기본 토큰`은 `AuctionModule` 내에 갇힌 채로 남습니다.

`liquidityProviderOne`이 모든 `staked tokens`를 상환하여 나머지 기본 토큰이 `AuctionModule.sol`에 갇히게 될 때 경매 종료가 되돌려짐을 보여주는 다음 테스트를 수행했습니다:

```solidity
    // Filename: test/unit/SafetyModuleTest.sol:SafetyModuleTest
    // $ forge test --match-test "testFuzz_TerminateAuctionErrorZero" -vvv
    function testFuzz_TerminateAuctionErrorZero(
        uint8 numLots,
        uint128 lotPrice,
        uint128 initialLotSize,
        uint64 slashPercent,
        uint16 lotIncreasePeriod,
        uint32 timeLimit
    ) public {
        /* bounds */
        numLots = uint8(bound(numLots, 2, 10));
        lotPrice = uint128(bound(lotPrice, 1e8, 1e12)); // denominated in USDC w/ 6 decimals
        slashPercent = uint64(bound(slashPercent, 1e16, 1e18));
        // lotSize x numLots should not exceed auctionable balance
        uint256 auctionableBalance = stakedToken1.totalSupply().wadMul(slashPercent);
        initialLotSize = uint128(bound(initialLotSize, 1e18, auctionableBalance / numLots));
        uint96 lotIncreaseIncrement = uint96(bound(initialLotSize / 50, 2e16, type(uint96).max));
        lotIncreasePeriod = uint16(bound(lotIncreasePeriod, 1 hours, 18 hours));
        timeLimit = uint32(bound(timeLimit, 5 days, 30 days));
        //
        // 1. Start an auction and check that it was created correctly
        uint256 auctionId = _startAndCheckAuction(
            stakedToken1,
            numLots,
            lotPrice,
            initialLotSize,
            slashPercent,
            lotIncreaseIncrement,
            lotIncreasePeriod,
            timeLimit
        );
        //
        // 2. `liquidityProviderOne` redeems all tokens
        vm.startPrank(liquidityProviderOne);
        uint256 stakedBalance = stakedToken1.balanceOf(liquidityProviderOne);
        stakedToken1.redeem(stakedBalance);
        vm.stopPrank();
        //
        // 3. `SafetyModule` terminates auction, the transaction will be reverted by "panic: division or modulo by zero"
        vm.expectRevert();
        safetyModule.terminateAuction(auctionId);
        //
        // 4. underlying token trapped in `AuctionModule` contract
        assertGt(stakedToken1.getUnderlyingToken().balanceOf(address(auctionModule)), 0); // AuctionModule.underlyingBalance > 0
    }
```

## 권장 사항

`stakedToken.totalSupply=0`일 때 `exchangeRate`를 `1e18`로 설정하는 것이 좋습니다.

```diff
    function _updateExchangeRate(uint256 totalAssets, uint256 totalShares) internal {
++      if (totalShares == 0)
++          exchangeRate = 1e18;
++      else
++          exchangeRate = totalAssets.wadDiv(totalShares);
        emit ExchangeRateUpdated(exchangeRate);
    }
```

# [M-01] `returnFunds()`가 주가 상승으로 이익을 얻기 위해 프론트런될 수 있음

## 심각도

**영향:** 높음 (High), 공격자가 주가 상승으로 이익을 얻을 수 있음

**가능성:** 낮음 (Low), 많은 양의 자금이 반환되는 경우에만 수익성이 있음

## 설명

`SafetyModule.returnFunds()`는 거버넌스가 자금을 기본 토큰 형태로 `StakedToken`에 다시 주입하는 데 사용됩니다. 예를 들어 경매에서 모금된 초과 자금이 있을 때 스테이커들에게 보상하기 위해 반환될 수 있습니다.

문제는 누구나 `stake()`로 `returnFunds()`를 프론트런하여 주가 상승으로 이익을 얻은 다음 언스테이크 창에 도달하면 곧바로 상환할 수 있다는 것입니다. 이는 트랜잭션 내에서 많은 양의 자금이 반환되는 경우 수익성이 있을 것입니다.

또한 자금 반환은 단기적으로 슬래시 이벤트가 없을 것임을 나타내므로, 이를 활용하여 언스테이크 창이 상환될 때까지 기다리는 것은 위험 없는 거래가 됩니다.

## 권장 사항

조달된 초과 자금을 반환하는 것이 `returnFunds()`가 사용되는 유일한 시나리오라면, 조달할 목표 자금 금액을 설정하고 도달했을 때 경매를 일찍 종료하는 것이 해결책이 될 수 있습니다. 이렇게 하면 경매가 목표에 도달했을 때 초과 자금이 거의/전혀 조달되지 않도록 보장하며, `StakedToken`에 반환할 자금이 적거나/없습니다.

그렇지 않은 경우 대안적인 해결책은 이유를 명시하지 않고 계약을 일시 중지하여(예측을 막기 위해) 프론트런을 방지하기 위해 몇 블록 후에 `returnFunds()`를 호출하는 것입니다. 완료되면 마지막으로 계약 중지를 해제하십시오. 이는 경매가 끝난 후 사용된다는 점을 제외하면 `stake()`를 비활성화하는 슬래싱 후 상태 확인과 동일한 효과를 가집니다.

또 다른 가능한 해결책은 보상 토큰을 통해 자금을 반환하는 것입니다. 이는 사용자가 이익을 챙기고 쿨다운 기간 후 인출할 수 있는 주가를 높이는 것보다 사용자가 더 오랫동안 스테이킹하도록 유지하는 더 좋은 인센티브가 될 것입니다. 그러면 `returnFunds()`를 사용할 필요가 없으며 필요하지 않은 경우 제거할 수 있습니다.

# [M-02] `addStakedToken()`이 그리핑(griefed)될 수 있음

## 심각도

**영향:** 중간 (Medium), StakedToken 추가 방지

**가능성:** 중간 (Medium), 1 wei를 스테이킹하여 수행할 수 있음

## 설명

`StakedToken`이 `addStakedToken()`을 통해 `SafetyModule`에 추가되면 `initMarketStartTime(StakedToken)`을 호출하여 `_timeOfLastCumRewardUpdate[StakedToken] = block.timestamp`를 설정합니다. 해당 `StakedToken`에 대해 `_timeOfLastCumRewardUpdate`가 이미 설정된 경우 시작 시간이 초기화되지 않았는지 확인하기 위한 검사로 인해 되돌려집니다.

```Solidity
    function initMarketStartTime(address _market) external onlySafetyModule {
        //@audit When this function is called by addStakedToken(), this check will revert
       //        if start time has already been initialized
        if (_timeOfLastCumRewardUpdate[_market] != 0) {
            revert RewardDistributor_AlreadyInitializedStartTime(_market);
        }
        _timeOfLastCumRewardUpdate[_market] = block.timestamp;
    }
```

그러나 공격자는 단 1 wei로 `StakedToken.stake()`를 수행한 후 `registerPositions([StakedToken])`을 수행하여 이 검사를 악용해 `addStakedToken()`이 실패하게 할 수 있습니다. 이는 간접적으로 `_updateMarketRewards(StakedToken)`을 호출하며, 아직 초기화되지 않았으므로 `_timeOfLastCumRewardUpdate[StakedToken] = block.timestamp`를 설정합니다.

이제 `StakedToken`에 대해 `_timeOfLastCumRewardUpdate`가 초기화되었으므로 해당 특정 `StakedToken`에 대한 후속 `addStakedToken()`은 되돌려지고 실패합니다.

```Solidity
    function _updateMarketRewards(address market) internal override {
        uint256 numTokens = rewardTokens.length;
        uint256 deltaTime = block.timestamp - _timeOfLastCumRewardUpdate[market];
        if (deltaTime == 0 || numTokens == 0) return;
        if (deltaTime == block.timestamp || _totalLiquidityPerMarket[market] == 0) {
            // Either the market has never been updated or it has no liquidity,
            // so just initialize the timeOfLastCumRewardUpdate and return

            //@audit This can be triggered by attacker via stake() and registerPositions(),
            //       before the StakedToken (market) is added to SafetyModule
            //       to cause addStakedToken() to revert.
            _timeOfLastCumRewardUpdate[market] = block.timestamp;
            return;
        }
```

## 권장 사항

필요하지 않으므로 `SMRewardDistributor`에 대한 `registerPositions()`를 제거하십시오.

또는 `SMRewardDistributor._registerPosition()`에서 `safetyModule.getStakedTokenIdx(market)` 검사를 사용하여 `StakedToken`이 `SafetyModule`에 추가되었는지 확인하십시오.

# [M-03] 스테이커는 일시 중지 중에 쿨다운을 활성화하고 슬래싱을 피하려고 시도할 수 있음

## 심각도

**영향:** 높음 (High), 스테이커가 슬래시 이벤트를 회피하여 나머지 스테이커가 슬래싱에 대해 더 많은 비용을 지불하게 할 수 있으므로

**가능성:** 낮음 (Low), 프로토콜이 일시 중지된 후 슬래시 이벤트가 발생할 때

## 설명

`StakedToken.cooldown()`에 `whenNotPaused` 수정자(modifier)가 누락되었습니다. 즉, 프로토콜이 일시 중지되었을 때 스테이커가 쿨다운을 활성화할 수 있습니다.

스테이커는 일시 중지로 인해 다가오는 슬래시 이벤트를 인지하거나 예상하고, 프로토콜이 일시 중지되었을 때 쿨다운을 활성화하여 언스테이크 창 내에 머물려고 시도할 수 있습니다. 일시 중지 이벤트는 특정 위험을 완화하기 위한 비상 조치이므로, 그 후 프로토콜 적자가 발생하여 스테이킹된 토큰의 슬래시가 필요할 것이라고 믿을 만한 이유가 있습니다.

프로토콜 일시 중지 중에 쿨다운을 활성화함으로써 스테이커는 슬래시 이벤트가 언스테이크 창 내에서 발생하는 경우 상환으로 슬래시 이벤트를 프론트런하려고 시도할 수 있습니다. 슬래시 이벤트를 회피하는 데 성공한 사람들은 나머지 스테이커들이 슬래싱에 대해 더 많은 비용을 지불하게 할 것입니다.

스테이커는 쿨다운 활성화로 인해 보상 배율이 재설정되는 페널티를 받게 되지만, 슬래시 이벤트를 회피하는 이점이 비상 일시 중지 이벤트에서의 추가 보상보다 클 가능성이 높습니다.

```Solidity
    //@audit missing whenNotPaused could allow
    function cooldown() external override {
        if (balanceOf(msg.sender) == 0) {
            revert StakedToken_ZeroBalanceAtCooldown();
        }
        if (isInPostSlashingState) {
            revert StakedToken_CooldownDisabledInPostSlashingState();
        }
        //solium-disable-next-line
        _stakersCooldowns[msg.sender] = block.timestamp;

        // Accrue rewards before resetting user's multiplier to 1
        smRewardDistributor.updatePosition(address(this), msg.sender);

        emit Cooldown(msg.sender);
    }
```

## 권장 사항

`cooldown()`에 `whenNotPaused` 수정자를 추가하십시오.

# [M-04] 슬래싱 후 쿨다운 비활성화를 우회할 수 있음

## 심각도

**영향:** 중간 (Medium), 스테이커가 쿨다운 비활성화를 우회할 수 있으므로

**가능성:** 중간 (Medium), 포스트 슬래시 기간 동안

## 설명

`StakedToken`이 포스트 슬래싱(post-slashing) 상태일 때 쿨다운 기능이 비활성화되어 스테이커가 `_stakersCooldowns[msg.sender] = block.timestamp`를 설정하여 활성화하는 것을 방지합니다.

그러나 스테이커는 유효한 쿨다운 타임스탬프가 있는 다른 계정으로 전송하여 쿨다운 기능 비활성화를 우회할 수 있습니다.

이는 `fromCooldownTimestamp`가 만료되었거나 설정되지 않았고 `toCooldownTimestamp`가 유효한 경우, 수신 계정의 쿨다운 타임스탬프에 대해 가중 평균이 설정되기 때문입니다.

이를 통해 스테이커는 전송 계정에서 보낸 스테이킹된 토큰에 대해 쿨다운을 활성화할 수 있습니다.

```Solidity
    function getNextCooldownTimestamp(
        uint256 fromCooldownTimestamp,
        uint256 amountToReceive,
        address toAddress,
        uint256 toBalance
    ) public view returns (uint256) {
        uint256 toCooldownTimestamp = _stakersCooldowns[toAddress];
        if (toCooldownTimestamp == 0) return 0;

        uint256 minimalValidCooldownTimestamp = block.timestamp - COOLDOWN_SECONDS - UNSTAKE_WINDOW;

        //@audit when `toCooldownTimestamp` is still valid, this will continue to next line
        if (minimalValidCooldownTimestamp > toCooldownTimestamp) return 0;

        //@audit when `fromCooldownTimestamp` has expired/not set, it will be set to current time
        if (minimalValidCooldownTimestamp > fromCooldownTimestamp) {
            fromCooldownTimestamp = block.timestamp;
        }
        //@audit weighted-average will be set for recieving account, when `toCooldownTimestamp` is still valid
        //       and this will activate cooldown for the sent amount
        if (fromCooldownTimestamp >= toCooldownTimestamp) {
            toCooldownTimestamp = (amountToReceive * fromCooldownTimestamp + (toBalance * toCooldownTimestamp))
                / (amountToReceive + toBalance);
        }

        return toCooldownTimestamp;
    }
```

## 권장 사항

쿨다운 비활성화 우회를 방지하기 위해 포스트 슬래싱 상태 동안 `StakedToken`의 전송을 비활성화하십시오.

# [M-05] 최종 슬래싱 금액이 예상보다 훨씬 낮을 수 있음

## 심각도

**영향:** 중간 (Medium), 예상보다 낮은 최종 슬래싱 금액은 추가적인 슬래싱을 필요로 할 수 있으며, 이로 인해 남은 스테이커들이 더 많은 손실을 입게 됨

**가능성:** 중간 (Medium), 슬래싱이 발생할 때 일어남

## 설명

`slashAndStartAuction()`은 거버넌스가 프로토콜 적자를 해결하기 위해 `StakedToken`의 일정 비율을 슬래싱할 수 있도록 합니다. 슬래시 비율은 매개변수로 제공되며 적자를 충당하는 데 필요한 절대값에서 파생됩니다.

그러나 슬래시 트랜잭션은 상대적인 비율 값을 기반으로 실행되기 때문에, 그 전에 여러 번의 `redeem()`이 발생하는 경우 최종 슬래싱된 가치가 예상보다 낮아질 수 있습니다.

이는 스테이커들이 슬래싱에 대한 공개 제안을 보거나 단순히 경쟁 상태(race conditions)로 인해 슬래싱을 프론트런하려고 할 때 발생할 수 있습니다.

이런 일이 발생하면 최종 슬래시 금액이 초기 예상 금액보다 낮아져 적자를 충당하지 못하게 됩니다. 즉, 또 다른 슬래시 이벤트가 필요할 가능성이 높으며 문제가 재발할 수 있습니다.

```Solidity
    function slashAndStartAuction(
        address _stakedToken,
        uint8 _numLots,
        uint128 _lotPrice,
        uint128 _initialLotSize,
        uint64 _slashPercent,
        uint96 _lotIncreaseIncrement,
        uint16 _lotIncreasePeriod,
        uint32 _timeLimit
    ) external onlyRole(GOVERNANCE) returns (uint256) {
        if (_slashPercent > 1e18) {
            revert SafetyModule_InvalidSlashPercentTooHigh();
        }

        IStakedToken stakedToken = stakedTokens[getStakedTokenIdx(_stakedToken)];

        // Slash the staked tokens and transfer the underlying tokens to the auction module
       //@audit slashAmount could end up lesser than expected if multiple redemption occurred before this
        uint256 slashAmount = stakedToken.totalSupply().mul(_slashPercent);
        uint256 underlyingAmount = stakedToken.slash(address(auctionModule), slashAmount);
        ...
   }
```

## 권장 사항

슬래시 이전의 상환 금액을 예측하고 이를 사용하여 더 높은 슬래시 비율을 설정하는 것도 실행 가능하지 않습니다. 따라서 절대적인 슬래시 금액을 사용하는 것이 좋습니다.

참고로 `slashAndStartAuction()`은 절대 슬래시 금액이 슬래싱 가능한 금액이나 비율 상한보다 큰 경우, 가능한 최대 금액을 사용하여 되돌려지는(revert) 것을 방지해야 합니다.

# [M-06] `_cumulativeRewardPerLpToken`의 오작동으로 인한 추가 보상

## 심각도

**영향:** 높음 (High), 사용자가 할당된 것보다 더 많은 보상을 받을 수 있으므로.

**가능성:** 낮음 (Low), `rewardToken`의 제거 및 재추가가 필요하므로.

## 설명

`RewardDistributor::_updateMarketRewards` 함수를 사용하여 시장에 대한 보상이 업데이트되면, 나중에 각 사용자에 대해 `_accrueRewards` 함수에서 사용하기 위해 `_cumulativeRewardPerLpToken` 변수가 증가합니다.

```solidity
File: RewardDistributor.sol
281:     function _updateMarketRewards(address market) internal override {
...
315:             uint256 newRewards = getInflationRate(token).mulDiv(_marketWeightsByToken[token][market], MAX_BASIS_POINTS)
316:                 .mulDiv(deltaTime, 365 days).div(_totalLiquidityPerMarket[market]);
317:             if (newRewards != 0) {
318:                 _cumulativeRewardPerLpToken[token][market] += newRewards;
319:                 emit RewardAccruedToMarket(market, token, newRewards);
320:             }
...
327:     }
```

```solidity
File: SMRewardDistributor.sol
328:     function _accrueRewards(address market, address user) internal virtual override {
...
...
357:             uint256 newRewards = userPosition.mul(
358:                 _cumulativeRewardPerLpToken[token][market] - _cumulativeRewardPerLpTokenPerUser[user][token][market]
359:             ).mul(rewardMultiplier);
360:             // Update the user's stored accumulator value
361:             _cumulativeRewardPerLpTokenPerUser[user][token][market] = _cumulativeRewardPerLpToken[token][market];
...
382:     }
```

문제는 `RewardDistributor::addRewardToken` 함수를 사용하여 토큰을 다시 추가할 때 이 `_cumulativeRewardPerLpToken` 변수가 0으로 초기화되지 않아 잘못된 집계가 발생한다는 것입니다. 다음 시나리오를 고려해 보십시오:

1. `RewardDistributor::removeRewardToken` 함수를 사용하여 `rewardTokenA`가 제거됩니다. 이 시점에서 `_cumulativeRewardPerLpToken`은 예를 들어 `100`으로 남아 있습니다.
2. 시간이 지나고 `RewardDistributor::addRewardToken` 함수를 사용하여 동일한 `rewardTokenA`를 다시 추가하기로 결정합니다.
3. 이 시점에서 사용자는 자신에게 할당되어서는 안 될 보상을 축적하게 됩니다. `_accrueRewards` 함수가 새 보상에 대해 다음 계산을 수행하기 때문입니다:

```solidity
uint256 newRewards = lpPosition.mul(_cumulativeRewardPerLpToken[token][market] - _cumulativeRewardPerLpTokenPerUser[user][token][market]);
```

따라서 사용자가 예를 들어 `10e18 tokens`의 포지션을 가지고 있는 경우, 보상은 `newRewards = 10e18 * (100 - 0) = 1000e18`로 계산됩니다. 이는 해당 보상(`_cumulativeRewardPerLpToken`)이 `1단계` 실행 전에 할당되었기 때문에 잘못된 것입니다. 올바른 계산은 `newRewards = 10e18 * (0 - 0) = 0`이어야 합니다. `rewardToken`이 다시 추가되는 시점에서는 새로운 보상 생성을 시작하기 때문입니다.

## 권장 사항

`RewardDistributor::addRewardToken` 함수에서 `_cumulativeRewardPerLpToken`을 0으로 재설정하는 것이 좋습니다. 이렇게 하면 어떤 이유로든 `governance`가 동일한 `rewardToken`을 다시 추가하기로 결정하더라도 보상 집계가 올바르게 할당됩니다.

추가적으로, 보상 토큰이 제거된 기간 동안 토큰을 청구하지 않은 사용자는 보상을 잃게 되므로 구현 시 주의를 기울여야 합니다.

# [M-07] `_marketWeightsByToken`의 승인되지 않은 `rewardTokens`

## 심각도

**영향:** 높음 (High), `market`이 승인되지 않은 `rewardTokens`를 받을 수 있으므로.

**가능성:** 낮음 (Low), 거버넌스가 `rewardToken`을 제거한 후 다시 추가해야 하므로.

## 설명

`_marketWeightsByToken` 변수는 `RewardDistributor::_updateMarketRewards` 함수에서 해당 시장에 할당된 가중치를 기반으로 `market`에 보상을 할당하는 데 사용됩니다:

```solidity
File: RewardDistributor.sol
281:     function _updateMarketRewards(address market) internal override {
...
315:             uint256 newRewards = getInflationRate(token).mulDiv(_marketWeightsByToken[token][market], MAX_BASIS_POINTS)
316:                 .mulDiv(deltaTime, 365 days).div(_totalLiquidityPerMarket[market]);
317:             if (newRewards != 0) {
318:                 _cumulativeRewardPerLpToken[token][market] += newRewards;
319:                 emit RewardAccruedToMarket(market, token, newRewards);
320:             }
...
327:     }
```

문제는 `RewardDistributor::removeRewardToken` 함수를 사용하여 `rewardToken`을 제거한 후 나중에 `RewardDistributor::addRewardToken` 함수를 사용하여 다시 추가할 때 발생합니다. 다음 시나리오를 고려해 보십시오:

1. `rewardTokenA`가 `stakedToken1` 시장에 100% 할당됩니다. 따라서 `_marketWeightsByToken[rewardTokenA][stakedToken1]=100%`입니다.
2. `rewardTokenA`가 `RewardDistributor::removeRewardToken` 함수를 사용하여 제거됩니다. 이 시점에서 `_marketWeightsByToken[rewardTokenA][stakedToken1]`은 지워지지 않습니다.
3. `rewardTokenA`가 `RewardDistributor::addRewardToken` 함수를 사용하여 다시 추가되지만 다른 시장에 할당되어, `rewardTokenA`는 이제 `stakedToken2` 시장에 100%를 분배하므로 `_marketWeightsByToken[rewardTokenA][stakedToken2]=100%`입니다.
4. 그런 다음 악의적인 사용자가 `stakedToken1` 시장을 사용하여 `updatePosition`을 호출하여 `RewardDistributor::_updateMarketRewards(stakedToken1)`을 트리거합니다. `_marketWeightsByToken[rewardTokenA][stakedToken1]`이 0으로 재설정되지 않았기 때문에, `rewardTokenA`가 더 이상 할당되지 않았음에도 불구하고 보상이 이 시장(stakedToken1)에 할당됩니다.

`rewardTokenA`가 `3단계`에서 `stakedToken1` 시장이 아닌 `stakedToken2` 시장에 100%를 분배하더라도 `stakedToken1` 시장은 승인되지 않은 보상을 받게 됩니다.

## 권장 사항

`rewardToken`을 제거할 때 관련된 `_marketWeightsByToken`도 지워졌는지 확인하십시오.

```diff
    function removeRewardToken(address _rewardToken) external onlyRole(GOVERNANCE) {
        ...
        ...
        // Update rewards for all markets before removal
        uint256 numMarkets = _rewardInfoByToken[_rewardToken].marketAddresses.length;
        for (uint256 i; i < numMarkets;) {
            _updateMarketRewards(_rewardInfoByToken[_rewardToken].marketAddresses[i]);
            unchecked {
                ++i; // saves 63 gas per iteration
            }
++          delete _marketWeightsByToken[_rewardToken][_rewardInfoByToken[_rewardToken].marketAddresses[i]];
        }
        ...
        ...
    }
```

# [L-01] 슬래싱 후 상태에서 쿨다운 기간을 비활성화하면 경매에 영향을 줄 수 있음

`StakedToken.redeem()` 함수는 스테이커의 쿨다운 기간 이후 스테이킹된 토큰의 상환을 허용합니다. 경매가 진행 중인 포스트 슬래싱 상태 동안 스테이커는 쿨다운 기간을 기다리지 않고 즉시 상환할 수 있습니다.

그러나 경매 중 쿨다운 기간을 비활성화하면 즉각적인 대규모 상환이 발생하고, 상환된 토큰의 매도가 뒤따를 가능성이 높습니다. 이는 매도 압력을 높이고 경매된 토큰의 시장 가치를 낮추어 경매에 영향을 줄 수 있습니다. 결과적으로 경매에서 조달된 자금이 예상보다 훨씬 적어지고 훨씬 더 작은 스테이킹된 토큰 풀에 대해 또 다른 슬래시 이벤트가 필요할 수 있습니다. 최악의 경우 스테이킹된 토큰의 죽음의 소용돌이(death spiral)로 이어질 수도 있습니다.

상환의 효과를 예측하기 어렵고 완벽하게 실행된 경매를 보장하기 어렵습니다.

포스트 슬래싱 상태에서도 쿨다운 기간을 계속 시행하여 거버넌스가 조치를 취할 시간을 제공하면서 보다 점진적인 상환을 허용하는 것이 더 나은 생각일 수 있습니다. 더 긴 쿨다운 기간을 가진 사람들에게는 공평하지 않을 수 있지만, 스테이킹된 토큰의 시장 가치가 현저히 낮은 가치로 폭락하지 않는다면 여전히 그들에게 더 나은 결과가 될 수 있습니다.

```Solidity
    function _redeem(address from, address to, uint256 amount) internal {
        if (amount == 0) revert StakedToken_InvalidZeroAmount();
        if (exchangeRate == 0) revert StakedToken_ZeroExchangeRate();

        // Users can redeem without waiting for the cooldown period in a post-slashing state
        if (!isInPostSlashingState) {
            // Make sure the user's cooldown period is over and the unstake window didn't pass
            uint256 cooldownStartTimestamp = _stakersCooldowns[from];
            if (block.timestamp < cooldownStartTimestamp + COOLDOWN_SECONDS) {
                revert StakedToken_InsufficientCooldown(cooldownStartTimestamp + COOLDOWN_SECONDS);
            }
            if (block.timestamp - cooldownStartTimestamp + COOLDOWN_SECONDS > UNSTAKE_WINDOW) {
                revert StakedToken_UnstakeWindowFinished(cooldownStartTimestamp + COOLDOWN_SECONDS + UNSTAKE_WINDOW);
            }
        }
       ...
      }
```

# [L-02] `_updateMarketRewards()`가 예상보다 낮은 인플레이션율을 적용할 수 있음

`_updateMarketRewards()`는 `(inflationRatePerSecond x marketWeight x deltaTime) / totalLiquidity` 공식을 사용하여 특정 시장 및 토큰에 대한 새 보상을 계산합니다. 인플레이션율은 감소 요인(reduction factor)과 경과 시간에 따라 점차 감소합니다.

그러나 `inflationRatePerSecond`는 `_updateMarketRewards()`를 호출하는 시점에 파생되므로, 오랜 시간이 지난 후 호출되면 예상보다 낮아집니다. 그 시나리오에서 `deltaTime`도 더 커지는데, 이는 큰 `deltaTime`에 더 낮은 `inflationRatePerSecond`가 곱해지기 때문에 보상 계산이 의도한 것보다 낮아지게 합니다.

보상 계산이 의도한 것보다 낮더라도 해당 시장/스테이킹된 토큰의 모든 스테이커에게 계산이 균일하게 적용되므로 영향은 미미합니다.

이 문제는 `deltaTime`이 너무 클 때 `_updateMarketRewards()`를 모니터링하고 트리거하여 완화할 수 있습니다.

# [L-03] `PerpRewardDistributor`에서 `totalWeight`가 100%와 같은지 확인하는 온전성 검사(sanity check) 추가

초기 `rewardToken`을 추가할 때 `PerpRewardDistributor`의 `constructor()`를 제외한 모든 곳에서 이 검사를 수행합니다.

# [L-04] 영구(perp) 포지션 등록 시 출금 타이머 추가 고려

`PerpRewardDistributor`가 배포되기 전에 포지션이 생성된 경우 사용자는 `registerPositions()`를 통해 수동으로 포지션을 등록할 수 있습니다.

일반 포지션 업데이트처럼 작동해야 하지만 `_withdrawTimerStartByUserByMarket` 필드 업데이트를 놓칩니다. 이는 조기 출금 또는 청산에 대한 페널티를 적용하는 데 사용됩니다:

```solidity
            if (newLpPosition < prevLpPosition) {
                // Removed liquidity - need to check if within early withdrawal threshold
                uint256 deltaTime = block.timestamp - _withdrawTimerStartByUserByMarket[user][market];
                if (deltaTime < _earlyWithdrawalThreshold) {
                    // Early withdrawal - apply penalty
                    // Penalty is linearly proportional to the time remaining in the early withdrawal period
@>                  uint256 penalty = (newRewards * (_earlyWithdrawalThreshold - deltaTime)) / _earlyWithdrawalThreshold;
                    newRewards -= penalty;
                    emit EarlyWithdrawalPenaltyApplied(user, market, token, penalty);
                }
            }
```

예를 들어 `SMRewardDistributor`에서는 유사한 필드 `_multiplierStartTimeByUser`가 업데이트됩니다:

```solidity
    function _registerPosition(address _user, address _market) internal override {
        super._registerPosition(_user, _market);
        if (_lpPositionsPerUser[_user][_market] != 0) {
            _multiplierStartTimeByUser[_user][_market] = block.timestamp;
        }
    }
```

# [L-05] 진행 중인 경매가 AuctionModule 교체 중에 닫히지 않을 수 있음

`AuctionModule`은 `SafetyModule::setAuctionModule` 함수를 사용하여 교체할 수 있습니다. 그러나 진행 중인 경매가 있는 상태에서 `AuctionModule`이 변경될 때 문제가 발생합니다. 완료되면 이러한 경매는 `SafetyModule::auctionEnded` 함수를 호출할 수 있습니다.

```solidity
File: AuctionModule.sol
361:     function _completeAuction(uint256 _auctionId, bool _terminatedEarly) internal {
...
377:         // Notify SafetyModule if necessary
378:         if (!_terminatedEarly) {
379:             safetyModule.auctionEnded(_auctionId, remainingBalance);
380:         }
...
390:     }
```

`safetyModule::auctionEnded`의 `onlyAuctionModule` 수정자로 인해 잠재적인 오류가 발생합니다. 이는 호출하는 경매가 더 이상 예상된 모듈에 해당하지 않기 때문입니다.

```solidity
File: SafetyModule.sol
093:     function auctionEnded(uint256 _auctionId, uint256 _remainingBalance) external onlyAuctionModule {
```

교체되는 `AuctionModule` 내에 활성 경매가 있는 경우 모듈 변경을 방지하는 확인을 `SafetyModule::setAuctionModule` 함수에 포함하는 것이 좋습니다.

# [L-06] AuctionModule.paymentToken이 SafetyModule 계약에 무기한 갇힐 수 있음

경매가 종료되면 축적된 지불 토큰이 `SafetyModule` 계약으로 전송됩니다:

```solidity
File: AuctionModule.sol
361:     function _completeAuction(uint256 _auctionId, bool _terminatedEarly) internal {
...
373:         // SafetyModule will transfer funds to governance when `withdrawFundsRaisedFromAuction` is called
374:         if (fundsRaised != 0) {
375:             paymentToken.safeTransfer(address(safetyModule), fundsRaised);
376:         }
...
390:     }
```

그 후 `SafetyModule::withdrawFundsRaisedFromAuction` 함수의 도움으로 `governance`가 이러한 토큰을 얻을 수 있습니다:

```solidity
File: SafetyModule.sol
180:     function withdrawFundsRaisedFromAuction(uint256 _amount) external onlyRole(GOVERNANCE) {
181:         IERC20 paymentToken = auctionModule.paymentToken();
182:         paymentToken.safeTransfer(msg.sender, _amount);
183:     }
```

문제는 `AuctionModule::setPaymentToken` 함수를 사용하여 `paymentToken`이 변경될 때 발생합니다. 다음 시나리오를 고려해 보십시오:

1. `paymentToken=X`를 사용하여 진행 중인 경매가 있습니다.
2. 경매가 종료되고 `paymentToken X`가 `SafetyModule`로 전송됩니다. 그러나 `governance`는 `SafetyModule::withdrawFundsRaisedFromAuction` 함수를 사용하여 이를 청구하지 않습니다.
3. 지불 토큰을 변경해야 할 필요가 있어 `AuctionModule::setPaymentToken` 함수를 사용하여 `paymentToken=Y`로 전환됩니다.
4. `paymentToken Y`를 사용하여 새 경매가 시작됩니다.
5. 거버넌스는 `SafetyModule::withdrawFundsRaisedFromAuction` 함수를 호출하기로 결정합니다. 그러나 이 함수는 `SafetyModule`이 아직 소유하지 않은 `paymentToken Y`의 잔액을 전송하므로 토큰을 검색하지 못합니다. 반면에 `paymentToken X`는 청구할 수 없으며, `Governance`는 진행 중인 경매에 영향을 미치므로 `paymentToken` 변경을 되돌릴 수 없습니다.

권장 사항은 `SafetyModule`이 해당 토큰을 청구하기 전에 지불 토큰을 변경하지 못하도록 유효성 검사를 구현하는 것입니다:

```diff
    function setPaymentToken(IERC20 _newPaymentToken) external onlyRole(GOVERNANCE) {
        if (address(_newPaymentToken) == address(0)) {
            revert AuctionModule_InvalidZeroAddress(0);
        }
++      if (paymentToken.balanceOf(address(safetyModule)) > 0) revert();
        emit PaymentTokenChanged(address(paymentToken), address(_newPaymentToken));
        paymentToken = _newPaymentToken;
    }
```

# [L-07] 여러 staked tokens가 동일한 기본 토큰을 사용하는 경우 경매 오작동

`safed token`의 `기본 토큰`은 지급 불능 시 `Auctions`에서 경매에 사용됩니다. 경매의 구매자는 기본 토큰을 로트 단위로 구매하고 판매되지 않은 잔여분은 `safed token`에 반환됩니다. 문제는 동일한 `기본 토큰`을 사용하는 `safed tokens`가 동시에 경매될 때 발생합니다. 이 상황은 경매 오작동으로 이어져 프로토콜 내 불안정을 초래할 수 있습니다.

다음 테스트가 문제를 보여줍니다:

1. `safedToken1`과 `safedToken3`이 동일한 `underlying`을 사용합니다.
2. 거버넌스는 `SafetyModule::slashAndStartAuction` 기능을 사용하여 `safedToken1`의 슬래시 및 경매를 시작하고 `50e18 underlying tokens`를 `AuctionModule`로 전송합니다.
3. 거버넌스는 `safedToken3`의 슬래시 및 경매를 시작하고 `100e18 underlying tokens`를 전송합니다.
4. 구매자가 기본 토큰의 일부 로트를 구매합니다.
5. 거버넌스는 `SafetyModule::terminateAuction` 기능을 사용하여 첫 번째 경매를 종료하기로 결정하고, 이는 `AuctionModule::_completeAuction`을 호출합니다. 이 함수는 기본 토큰의 전체 잔액을 스테이킹된 토큰 `AuctionModule#365`로 전송하여, 활성 상태로 남아 있는 경매(3단계)가 구매자에게 지불할 토큰이 부족하게 만듭니다.

```solidity
File: AuctionModule.sol
361:     function _completeAuction(uint256 _auctionId, bool _terminatedEarly) internal {
...
377:         // Notify SafetyModule if necessary
378:         if (!_terminatedEarly) {
379:             safetyModule.auctionEnded(_auctionId, remainingBalance);
380:         }
...
...
390:     }
```

결과적으로 `safedToken1`은 활성 경매(3단계)를 위해 예약된 기본 토큰을 얻게 되며, 해당 경매는 여전히 활성 상태입니다.

코드에 제한이 없으므로 `safedTokens`가 동일한 기본 토큰을 공유할 가능성이 있습니다. 게다가 동일한 기본 토큰을 사용하면서 언스테이크 쿨다운이나 다른 환율 등 다른 매개변수를 가진 `safed tokens`를 갖는 것은 스테이킹된 토큰 보유자에게 인센티브가 될 수 있습니다.

```solidity
    // Filename: test/unit/SafetyModuleTest.sol:SafetyModuleTest
    function test_StakedTokenUsingSameUnderlyingWillbreakAuctions() public {
        //
        // 1. Deploy a third staked token which uses the same underlying token than `stakedToken1`
        StakedToken stakedToken3 = new StakedToken(
            rewardsToken, safetyModule, COOLDOWN_SECONDS, UNSTAKE_WINDOW, MAX_STAKE_AMOUNT_1, "Staked INCR 3", "stINCR3"
        );
        //
        // 2. Add the third staked token to the safety module and `liquidityProviderTwo` stakes `10_000e18` tokens
        safetyModule.addStakedToken(stakedToken3);
        rewardsToken.transfer(liquidityProviderTwo, 10_000 ether);
        vm.prank(liquidityProviderTwo);
        rewardsToken.approve(address(stakedToken3), type(uint256).max);
        _stake(stakedToken3, liquidityProviderTwo, 10_000 ether);
        //
        // 3. An auction is initiated to the `stakedToken1`
        uint128 lotPrice = 1e18;
        uint128 initialLotSize = 10e18;
        uint96 lotIncreaseIncrement = 1e17;
        uint16 lotIncreasePeriod = 1 hours;
        uint8 numLots = 5;
        uint64 slashPercent = 1e16;
        uint32 timeLimit = 10 days;
        assertEq(stakedToken1.getUnderlyingToken().balanceOf(address(auctionModule)), 0);
        uint256 auctionIdStakedToken1 = _startAndCheckAuction(
            stakedToken1,
            numLots,
            lotPrice,
            initialLotSize,
            slashPercent,
            lotIncreaseIncrement,
            lotIncreasePeriod,
            timeLimit
        );
        assertEq(stakedToken1.getUnderlyingToken().balanceOf(address(auctionModule)), 50e18); // AuctionModule has 50e18 underlying tokens
        //
        // 4. Another auction is initiated to the `stakedToken3`. `100e18` will be slashed from `stakedToken3` and it will be transferred to AuctionModule
        uint256 auctionIdStakedToken3 = _startAndCheckAuction(
            stakedToken3,
            numLots,
            lotPrice,
            initialLotSize,
            slashPercent,
            lotIncreaseIncrement,
            lotIncreasePeriod,
            timeLimit
        );
        uint256 underLyingAfterStakedToken3IsAuctioned = stakedToken3.getUnderlyingToken().balanceOf(address(auctionModule));
        assertEq(underLyingAfterStakedToken3IsAuctioned, 50e18 + 100e18);  // 50e18 from stakedToken1 and 100e18 from stakedToken3
        //
        // 5. Someone buys one lot from stakedToken1 and the payment token balance is now on AuctionModule
        address buyer = address(1337);
        _dealAndBuyLots(buyer, auctionIdStakedToken1, 1, lotPrice);
        assertEq(auctionModule.paymentToken().balanceOf(address(auctionModule)), 1e18);
        assertEq(stakedToken1.getUnderlyingToken().balanceOf(buyer), 10e18); // buyer receive 10e18 underlying tokens
        //
        // 6. Someone buys one lot from stakedToken3 and the payment token is added to the AuctionModule balance. Now there are 2e18 payment token balance
        _dealAndBuyLots(buyer, auctionIdStakedToken3, 1, lotPrice);
        assertEq(auctionModule.paymentToken().balanceOf(address(auctionModule)), 2e18);
        assertEq(stakedToken1.getUnderlyingToken().balanceOf(buyer), 20e18); // buyer receives another 10e18 underlying tokens
        //
        // 7. Governance terminates the auctionIdStakedToken1 and all the remaining underlying is transferred to stakedToken1
        // That is incorrect because the auctionStakedToken3 is still active and now there are not underlying tokens in
        // the auctionModule contract
        uint256 balanceUnderlyingBeforeAuctionends = stakedToken1.getUnderlyingToken().balanceOf(address(stakedToken1));
        safetyModule.terminateAuction(auctionIdStakedToken1);
        assertGt(stakedToken1.getUnderlyingToken().balanceOf(address(stakedToken1)), balanceUnderlyingBeforeAuctionends); // currentUnderLyingBalance > balanceBeforeAuctionEnds
        assertEq(stakedToken1.getUnderlyingToken().balanceOf(address(auctionModule)), 0); // now there is zero undelying balance on AuctionModule
        assertEq(stakedToken3.getUnderlyingToken().balanceOf(address(auctionModule)), 0); // now there is zero undelying balance on AuctionModule
    }
```

결과적으로 활성 경매에 대한 기본 토큰이 손실될 수 있습니다.

권장 사항은 활성 경매를 위해 예약된 기본 토큰을 사용하지 않도록 경매 프로세스를 수정하는 것입니다. 또는 동일한 기본 토큰을 가진 스테이킹된 토큰이 존재하지 않도록 조치를 취할 수 있습니다.

# [L-08] 일부 수정으로 인해 영향을 받는 스테이커

스테이커는 `SMRewardDistributor::computeRewardMultiplier`의 계산에 따라 보상을 받습니다. 이 함수는 `_smoothingValue` 또는 `_maxRewardMultiplier`의 값을 고려합니다. 사용자가 `RewardsDistributor::claimRewardsFor` 함수를 호출하면 `SMRewardDistributor::_accrueRewards`를 호출하여 사용자에 대한 해당 보상이 계산됩니다(코드 라인 340 및 357-359).

```solidity
File: SMRewardDistributor.sol
328:     function _accrueRewards(address market, address user) internal virtual override {
...
...
340:         uint256 rewardMultiplier = computeRewardMultiplier(user, market);
341:         uint256 numTokens = rewardTokens.length;
342:         for (uint256 i; i < numTokens;) {
...
...
357:             uint256 newRewards = userPosition.mul(
358:                 _cumulativeRewardPerLpToken[token][market] - _cumulativeRewardPerLpTokenPerUser[user][token][market]
359:             ).mul(rewardMultiplier);
...
...
381:         }
382:     }
```

문제는 거버넌스가 `SMRewardDistributor::setMaxRewardMultiplier` 및 `SMRewardDistributor::setSmoothingValue` 함수를 사용하여 `_smoothingValue` 또는 `_maxRewardMultiplier`를 수정할 때 발생합니다. 이러한 수정은 사용자에게 소급하여 영향을 미칩니다. 즉, 사용자가 스테이킹을 하고 있었고 `_smoothingValue`가 증가하면 사용자는 더 적은 보상을 받게 됩니다. 다음 시나리오를 고려해 보십시오:

1. `UserA`가 `100e18` 토큰을 스테이킹하고 `RewardsDistributor::claimRewardsFor`를 호출하지 않고 10일 동안 스테이킹을 계속합니다.
2. `SMRewardDistributor::computeRewardMultiplier` 함수는 예를 들어 `5e18`의 배율을 반환합니다. 그러나 여러 가지 이유로 사용자는 보상을 청구하지 않습니다.
3. 거버넌스가 `_smoothingValue`를 늘리고 이제 `SMRewardDistributor::computeRewardMultiplier`는 `2e18`의 배율을 반환합니다. 이제 사용자는 청구할 때 2단계와 비교하여 더 적은 보상을 받게 됩니다.

분명히 `_smoothingValue` 또는 `_maxRewardMultiplier`에 대한 수정은 스테이킹을 하고 있던 사용자에게 영향을 미칩니다. 반면에 악의적인 사용자는 이러한 수정을 프론트런하여 해당 보상을 축소되기 전에 청구하여 아직 청구하지 않은 사용자보다 더 많은 보상을 얻을 수 있습니다.

`_smoothingValue`의 증가가 `SMRewardDistributor::computeRewardMultiplier`의 계산에 어떤 영향을 미치는지 보여주는 테스트를 수행했습니다:

```solidity
    // Filename: test/unit/SafetyModuleTest.sol:SafetyModuleTest
    // $ forge test --match-test "test_RewardMultiplierIsModifiedRetroactively" -vvv
    function test_RewardMultiplierIsModifiedRetroactively() public {
        //
        // 1. User stakes 100 ether and the computeRewardMultiplier is 1e18. smoothing value of 30 and max multiplier of 4
        _stake(stakedToken1, liquidityProviderTwo, 100 ether);
        assertEq(
            rewardDistributor.computeRewardMultiplier(liquidityProviderTwo, address(stakedToken1)),
            1e18,
            "Reward multiplier mismatch after initial stake"
        );
        //
        // 2. Time passes 2 days and computeRewardMultiplier=1.5e18
        skip(2 days);
        assertEq(
            rewardDistributor.computeRewardMultiplier(liquidityProviderTwo, address(stakedToken1)),
            1.5e18,
            "Reward multiplier mismatch after 2 days"
        );
        //
        // 3. Gov increase the smothing value reducing the computeRewardMultiplier
        rewardDistributor.setSmoothingValue(60e18);
        assertLt(
            rewardDistributor.computeRewardMultiplier(liquidityProviderTwo, address(stakedToken1)),
            1.5e18
        ); // computeMultiplier < 1.5e18
    }
```

이는 사용자가 얻을 수 있는 보상 금액에 영향을 줄 수 있으며, 악의적인 사용자는 수정이 실행되기 전에 예상하여 `_smoothingValue` 또는 `_maxRewardMultiplier`가 수정되기 전에 보상을 청구하지 않은 사용자보다 더 많은 보상을 얻을 수 있습니다. `governance`가 `_smoothingValue` 또는 `_maxRewardMultiplier`를 수정하는 것은 필요하지만, 함수가 이러한 변경을 수행하도록 설계되었으므로 가능합니다.

`_smoothingValue` 또는 `_maxRewardMultiplier`가 수정되는 경우 이러한 수정은 새로운 스테이킹에 적용되어야 합니다. 스테이킹을 하고 있고 보상을 청구하지 않은 사용자는 이전 `_smoothingValue` 또는 `_maxRewardMultiplier`를 사용하여 보상을 받아야 합니다.

# [L-09] 공격자가 더스트(dust) 전송으로 고래 스테이커를 괴롭힐 수 있음(griefing)

각 사용자의 스테이킹된 금액을 `maxStakeAmount` 이내로 유지하기 위해 `StakedToken`에 스테이크 금액 상한이 구현되어 있습니다. 사용자의 총 스테이킹된 금액이 `maxStakeAmount`를 초과하면 `stake()`에 대해 되돌리기(revert)가 발생합니다.

공격자는 이를 악용하여 전체 `maxStakeAmount`를 스테이킹하려는 고래 스테이커를 괴롭힐 수 있습니다. 공격은 더스트 전송으로 고래 스테이커의 `stake()` 트랜잭션을 프론트런하여 `maxStakeAmount`를 초과하고 실패하게 함으로써 수행될 수 있습니다.

```Solidity

    function _stake(address from, address to, uint256 amount) internal whenNotPaused {
        if (amount == 0) revert StakedToken_InvalidZeroAmount();
        if (exchangeRate == 0) revert StakedToken_ZeroExchangeRate();

        if (isInPostSlashingState) {
            revert StakedToken_StakingDisabledInPostSlashingState();
        }

        // Make sure the user's stake balance doesn't exceed the max stake amount
        uint256 stakeAmount = previewStake(amount);
        uint256 balanceOfUser = balanceOf(to);

        //@audit this can be used to grief a whale staker by transferring dust token to the account
        if (balanceOfUser + stakeAmount > maxStakeAmount) {
            revert StakedToken_AboveMaxStakeAmount(maxStakeAmount, maxStakeAmount - balanceOfUser);
        }

        // Update cooldown timestamp
        _stakersCooldowns[to] = getNextCooldownTimestamp(0, stakeAmount, to, balanceOfUser);

        // Mint staked tokens
        _mint(to, stakeAmount);

        // Transfer underlying tokens from the sender
        UNDERLYING_TOKEN.safeTransferFrom(from, address(this), amount);

        // Update user's position and rewards in the SafetyModule
        smRewardDistributor.updatePosition(address(this), to);

        emit Staked(from, to, amount);
    }
```

총 스테이크 금액이 `maxStakeAmount`를 초과할 때 되돌리는 대신, 총 스테이크 금액이 `maxStakeAmount` 내에서 유지되도록 스테이킹할 `amount`를 줄이는 것을 고려하십시오.

# [L-10] `addRewardToken()`의 누락된 확인으로 인한 초과 보상 발생 가능성

`RewardDistributor.addRewardToken()`은 거버넌스가 지정된 시장에 대한 새로운 보상 토큰과 매개변수를 추가하기 위해 호출합니다. 이는 시장에 배포될 보상 토큰의 양을 결정하는 데 사용됩니다.

그러나 `addRewardToken()`은 추가할 보상 토큰이 이미 존재하는지 확인하지 않습니다. 거버넌스가 실수로 중복 보상 토큰을 추가하면 시장에 의도한 것보다 더 많은 보상을 배포할 수 있습니다.

```Solidity
    function addRewardToken(
        address _rewardToken,
        uint88 _initialInflationRate,
        uint88 _initialReductionFactor,
        address[] calldata _markets,
        uint256[] calldata _marketWeights
    ) external onlyRole(GOVERNANCE) {
        if (_initialInflationRate > MAX_INFLATION_RATE) {
            revert RewardController_AboveMaxInflationRate(_initialInflationRate, MAX_INFLATION_RATE);
        }
        if (MIN_REDUCTION_FACTOR > _initialReductionFactor) {
            revert RewardController_BelowMinReductionFactor(_initialReductionFactor, MIN_REDUCTION_FACTOR);
        }
        if (_marketWeights.length != _markets.length) {
            revert RewardController_IncorrectWeightsCount(_marketWeights.length, _markets.length);
        }
        if (rewardTokens.length >= MAX_REWARD_TOKENS) {
            revert RewardController_AboveMaxRewardTokens(MAX_REWARD_TOKENS);
        }
        // Validate weights
        uint256 totalWeight;
        uint256 numMarkets = _markets.length;
        for (uint256 i; i < numMarkets;) {
            address market = _markets[i];
            _updateMarketRewards(market);
            uint256 weight = _marketWeights[i];
            if (weight == 0) {
                unchecked {
                    ++i; // saves 63 gas per iteration
                }
                continue;
            }
            if (weight > MAX_BASIS_POINTS) {
                revert RewardController_WeightExceedsMax(weight, MAX_BASIS_POINTS);
            }
            totalWeight += weight;
            _marketWeightsByToken[_rewardToken][market] = weight;
            emit NewWeight(market, _rewardToken, weight);
            unchecked {
                ++i; // saves 63 gas per iteration
            }
        }
        if (totalWeight != MAX_BASIS_POINTS) {
            revert RewardController_IncorrectWeightsSum(totalWeight, MAX_BASIS_POINTS);
        }
        // Add reward token info
        rewardTokens.push(_rewardToken);
        _rewardInfoByToken[_rewardToken].token = IERC20Metadata(_rewardToken);
        _rewardInfoByToken[_rewardToken].initialTimestamp = uint80(block.timestamp);
        _rewardInfoByToken[_rewardToken].initialInflationRate = _initialInflationRate;
        _rewardInfoByToken[_rewardToken].reductionFactor = _initialReductionFactor;
        _rewardInfoByToken[_rewardToken].marketAddresses = _markets;

        emit RewardTokenAdded(_rewardToken, block.timestamp, _initialInflationRate, _initialReductionFactor);
    }
```

보상 토큰이 아직 추가되지 않았는지 확인하는 검사를 추가하는 것을 고려하십시오.

# [L-11] `StakedToken::_redeem()`을 위한 추가 매개변수

`StakedToken::_redeem` 함수는 `staked tokens`를 `underlying tokens`로 상환하는 것을 용이하게 합니다. 그러나 트랜잭션 중 문제가 발생할 수 있는데, 상환 조치가 진행되는 동안 `exchange rate`에 변화가 생겨 상환자가 더 적은 기본 토큰을 받을 수 있기 때문입니다. 다음 시나리오를 고려해 보십시오:

1. 사용자가 `10e18 underlying tokens`를 스테이킹하고 `10e18 staked tokens`를 받습니다(exchangeRate=1).
2. 사용자는 쿨다운 프로세스를 시작하고 결국 `StakedToken::redeem` 함수를 호출합니다.
3. `2단계`가 실행되기 전에 슬래시가 발생하고 `exchange rate`가 `0.7`로 변경됩니다. 이 트랜잭션은 `2단계` 전에 실행됩니다.
4. `2단계`의 트랜잭션이 실행되어 `10e18 staked tokens`를 상환하지만, 사용자는 `7e18 underlying tokens`를 받습니다(previewRedeem = 10e18 \* 0.7).

결국 사용자는 온체인에서 발생할 수 있는 요인들의 결과로 예상보다 적은 기본 토큰을 갖게 됩니다.

`StakedToken::_redeem` 함수에 상환자가 수락할 의향이 있는 `최소 기본 토큰 금액`을 지정할 수 있는 매개변수를 추가하는 것을 권장합니다. 이 매개변수는 사용자에게 상환 트랜잭션에 대한 더 많은 통제권을 제공하고 환율 변동 위험을 완화하는 데 도움이 될 것입니다.

# [L-12] `_earlyWithdrawalThreshold` 변경으로 인한 사용자 불이익

`_earlyWithdrawalThreshold` 변수의 변경으로 인해 사용자가 예상치 못한 페널티를 받을 수 있습니다. 이 변수는 조기 유동성 제거 시 사용자에게 페널티가 적용되는지 여부를 계산하는 데 사용됩니다 `PerpRewardDistributor#L132-L138`.

```solidity
File: PerpRewardDistributor.sol
102:     function updatePosition(address market, address user) external virtual override onlyClearingHouse {
...
...
129:             if (newLpPosition < prevLpPosition) {
130:                 // Removed liquidity - need to check if within early withdrawal threshold
131:                 uint256 deltaTime = block.timestamp - _withdrawTimerStartByUserByMarket[user][market];
132:                 if (deltaTime < _earlyWithdrawalThreshold) {
133:                     // Early withdrawal - apply penalty
134:                     // Penalty is linearly proportional to the time remaining in the early withdrawal period
135:                     uint256 penalty = (newRewards * (_earlyWithdrawalThreshold - deltaTime)) / _earlyWithdrawalThreshold;
136:                     newRewards -= penalty;
137:                     emit EarlyWithdrawalPenaltyApplied(user, market, token, penalty);
138:                 }
139:             }
...
...
```

다음 시나리오를 고려해 보십시오:

1. `_earlyWithdrawalThreshold=10일`. 사용자가 X 토큰을 인출하고 `_withdrawTimerStartByUserByMarket`이 `block.timestamp`로 기록됩니다.
2. 11일이 지나고 사용자가 더 많은 토큰을 인출하기로 결정합니다. 이 시점에서 `11일 경과 < 10일 earlyWithdrawalThreshold`가 `false`이므로 페널티 없이 인출할 수 있습니다 `PerpRewardDistributor#L132`.
3. 그러나 `governance`가 `_earlyWithdrawalThreshold=15일`로 변경하기로 결정합니다. 이 트랜잭션은 `2단계` 전에 실행됩니다.
4. 마지막으로 `2단계`의 트랜잭션이 실행되어 `11일 경과 < 15일 _earlyWithdrawalThreshold`가 `true`이므로 사용자에게 페널티가 발생합니다.

`_earlyWithdrawalThreshold`가 변경되면 유동성을 제거하는 많은 사용자가 영향을 받을 수 있습니다.

사용자가 페널티로 지불할 의사가 있는 금액을 지정할 수 있도록 허용하는 것을 고려하는 것이 좋습니다. 이 접근 방식은 사용자에게 유동성 인출 트랜잭션에 대한 더 많은 통제권을 제공하고 `_earlyWithdrawalThreshold`의 급격한 변경으로 인한 영향을 완화할 것입니다.

# [L-13] `_totalUnclaimedRewards`가 감소하지 않는 경우

보상 토큰이 `USDC`일 수 있으므로 `USDC` 계약에 의해 차단된 스테이킹 토큰 보유자가 있을 수 있으며, 이로 인해 해당 보상을 사용자와 프로토콜 모두 복구할 수 없게 됩니다. 이는 `RewardDistributor::_distributeRewards#L348` 함수가 항상 되돌려지기 때문입니다:

```solidity
File: RewardDistributor.sol
343:     function _distributeReward(address _token, address _to, uint256 _amount) internal returns (uint256) {
344:         uint256 rewardsRemaining = _rewardTokenBalance(_token);
345:         if (rewardsRemaining == 0) return _amount;
346:         if (_amount <= rewardsRemaining) {
347:             _totalUnclaimedRewards[_token] -= _amount;
348:             IERC20Metadata(_token).safeTransferFrom(ecosystemReserve, _to, _amount);
349:             return 0;
350:         } else {
351:             _totalUnclaimedRewards[_token] -= rewardsRemaining;
352:             IERC20Metadata(_token).safeTransferFrom(ecosystemReserve, _to, rewardsRemaining);
353:             return _amount - rewardsRemaining;
354:         }
355:     }
```

따라서 `_totalUnclaimedRewards`는 결코 감소하지 않으며 차단된 사용자에게 할당된 보상이 전송될 수 없으므로 0이 되지 않습니다.

`USDC`의 사용자 차단으로 인해 전달되지 않은 보상에 따라 `_totalUnclaimedRewards`를 조정하는 관리자 기능을 두는 것이 좋으며, 이러한 보상은 반드시 `ecosystemReserve`에 있을 필요는 없습니다.
