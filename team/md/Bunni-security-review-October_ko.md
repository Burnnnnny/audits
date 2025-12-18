# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Bunniapp/tokenomics** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Bunni 소개 (About Bunni)

Bunni v2는 유동성 공급자가 복잡한 유동성 형태를 관리하고 이들 간에 원활하게 이동할 수 있도록 하는 AMM입니다. 스왑에 대한 일정한 가스 비용, 수수료의 자동 복리 및 최적의 토큰 비율을 유지하기 위한 자율 리밸런싱과 같은 기능을 포함합니다. 범위에는 Uniswap X용 실행자(executor) 계약, Bunni v2와의 상호 작용 향상을 위한 Zap 계약, BUNNI 토큰 마이그레이션용 스마트 계약 및 상한선이 있는 스테이킹 풀이 포함되었습니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
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

_검토 커밋 해시_ :

- [13a77bfa1983336e6fb5980a042d503f0e8b6c25](https://github.com/Bunniapp/tokenomics/tree/13a77bfa1983336e6fb5980a042d503f0e8b6c25)
- [762868c8283ff4812dedab8813a971f77fe14b10](https://github.com/Bunniapp/uniswap-x-executor/tree/762868c8283ff4812dedab8813a971f77fe14b10)
- [2a0e0782776dd753ef88fd23978e18d0cff27ce0](https://github.com/Bunniapp/zap-v2/tree/2a0e0782776dd753ef88fd23978e18d0cff27ce0)

_수정 검토 커밋 해시_ :

- [5721ae480dca2b5f85226342466ad5dcce54c4d3](https://github.com/Bunniapp/tokenomics/tree/5721ae480dca2b5f85226342466ad5dcce54c4d3)
- [762c8835fb56087a3c9317a3965c5fc15f2b39ac](https://github.com/Bunniapp/uniswap-x-executor/tree/762c8835fb56087a3c9317a3965c5fc15f2b39ac)
- [45f7e77410bfbc5e12deb086251154743839fa26](https://github.com/Bunniapp/zap-v2/tree/45f7e77410bfbc5e12deb086251154743839fa26)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `BunniHookOracle`
- `ERC20Multicaller`
- `RecurPoolId`
- `RecurPoolKey`
- `RushPoolId`
- `RushPoolKey`
- `BUNNI`
- `MasterBunni`
- `OptionsToken`
- `SmartWalletChecker`
- `TokenMigrator`
- `VeAirdrop`
- `LibMulticaller`
- `VotingEscrow`
- `BunniExecutor`
- `BunniZapIn`
- `ReentrancyGuard`

# 발견 사항 (Findings)

# [C-01] `claimRecurPool`이 제공된 `incentiveToken`이 `rewardToken`과 동일한지 확인하지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`claimRecurPool`이 호출될 때 제공된 `incentiveToken`이 청구되는 `RecurPoolKey`의 `rewardToken`과 동일한지 확인하지 않는다는 것을 알 수 있습니다.

```solidity
    function claimRecurPool(RecurClaimParams[] calldata params, address recipient) external nonReentrant {
        address msgSender = LibMulticaller.senderOrSigner();
        for (uint256 i; i < params.length; i++) {
>>>         address incentiveToken = params[i].incentiveToken;
            uint256 totalClaimableAmount;

            for (uint256 j; j < params[i].keys.length; j++) {
                RecurPoolKey calldata key = params[i].keys[j];
                RecurPoolId id = key.toId();

                // key should be valid
                if (!isValidRecurPoolKey(key)) continue;

                /// -----------------------------------------------------------------------
                /// Storage loads
                /// -----------------------------------------------------------------------

                // load state
                RecurPoolState storage state = recurPoolStates[id];
                uint64 lastUpdateTime = state.lastUpdateTime;
                uint64 periodFinish = state.periodFinish;
                uint64 lastTimeRewardApplicable =
                    block.timestamp < periodFinish ? uint64(block.timestamp) : periodFinish;
                uint256 rewardPerTokenUpdated = _rewardPerToken(
                    state.rewardPerTokenStored,
                    state.totalSupply,
                    lastTimeRewardApplicable,
                    lastUpdateTime,
                    state.rewardRate
                );

                /// -----------------------------------------------------------------------
                /// State updates
                /// -----------------------------------------------------------------------

                // accrue rewards
                uint256 reward = _earned(
                    state.userRewardPerTokenPaid[msgSender],
                    state.balanceOf[msgSender],
                    rewardPerTokenUpdated,
                    state.rewards[msgSender]
                );
                state.rewardPerTokenStored = rewardPerTokenUpdated;
                state.lastUpdateTime = lastTimeRewardApplicable;
                state.userRewardPerTokenPaid[msgSender] = rewardPerTokenUpdated;

                if (reward != 0) {
                    // delete accrued rewards
                    delete state.rewards[msgSender];

                    // accumulate claimable amount
                    totalClaimableAmount += reward;
                }
            }

            // transfer incentive tokens to user
            if (totalClaimableAmount != 0) {
                // @audit - incentiveToken is not checked!
>>>             incentiveToken.safeTransfer(recipient, totalClaimableAmount);
            }

            // emit event
            emit ClaimReward(msgSender, incentiveToken, recipient, totalClaimableAmount);
        }
    }
```

공격자는 가짜 Recur Pool을 만들고 임의의/가치 없는 `rewardToken`을 제공하여 `rewardRate`를 증가시킴으로써 이를 악용할 수 있습니다. 그런 다음 `claimRecurPool`이 호출될 때 공격자는 `incentiveToken`을 `MasterBunni`에서 훔치고자 하는 다른 토큰으로 설정할 수 있습니다.

## 권장 사항

제공된 `incentiveToken`이 각 `RecurPoolKey`의 `rewardToken`과 동일한지 확인하십시오.

# [C-02] `incentivizeRecurPool` 내에서 `incentiveToken`이 확인되지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`incentivizeRecurPool`이 호출되면 매개변수를 반복하고 제공된 `incentiveAmount`를 기반으로 `state.rewardRate`를 업데이트합니다.

```solidity
   function incentivizeRecurPool(RecurIncentiveParams[] calldata params, address incentiveToken)
        external
        returns (uint256 totalIncentiveAmount)
    {
        address msgSender = LibMulticaller.senderOrSigner();
        for (uint256 i; i < params.length; i++) {
            /// -----------------------------------------------------------------------
            /// Validation
            /// -----------------------------------------------------------------------

            if (params[i].incentiveAmount == 0) continue;

            RecurPoolKey calldata key = params[i].key;
            if (!isValidRecurPoolKey(key)) continue;

            // ...

            /// -----------------------------------------------------------------------
            /// State updates
            /// -----------------------------------------------------------------------

            // ...

            // record new reward
            uint256 newRewardRate;
            if (block.timestamp >= periodFinish) {
                // current period is over
                // uint256 internal constant REWARD_RATE_PRECISION = 1e6;
                newRewardRate = params[i].incentiveAmount.mulDiv(REWARD_RATE_PRECISION, key.duration);
                state.rewardRate = newRewardRate;
                state.lastUpdateTime = uint64(block.timestamp);
                state.periodFinish = uint64(block.timestamp + key.duration);
            } else {
                // period is still active
                // add the new reward to the existing period
                uint256 remaining = periodFinish - block.timestamp;
                newRewardRate += params[i].incentiveAmount.mulDiv(REWARD_RATE_PRECISION, remaining);

                state.rewardRate = newRewardRate;
                state.lastUpdateTime = uint64(block.timestamp);
            }
            // prevent overflow when computing rewardPerToken
            if (newRewardRate >= ((type(uint256).max / PRECISION_DIV_REWARD_RATE_PRECISION) / key.duration)) {
                revert MasterBunni__AmountTooLarge();
            }

            totalIncentiveAmount += params[i].incentiveAmount;
        }

        // transfer incentive tokens from msgSender to this contract
        if (totalIncentiveAmount != 0) {
>>>         incentiveToken.safeTransferFrom2(msgSender, address(this), totalIncentiveAmount);
        }

        // ...
    }
```

그러나 루프 내에서 현재 `rewardToken`이 `incentiveToken`과 동일한지 확인하지 않으므로 공격자가 실제 `rewardToken`을 제공하지 않고 `state.rewardRate`를 높일 수 있습니다. 이로 인해 정당한 인센티브가 있더라도 사용자는 계약에서 `rewardToken`을 사용할 수 없어 보상을 청구할 수 없게 됩니다.

## 권장 사항

`incentivizeRecurPool` 호출 내에서 `rewardToken`이 `incentiveToken`과 동일한지 확인하십시오.

# [C-03] `periodFinish`에 아직 도달하지 않았을 때 이전 `rewardRate`가 제거됨

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`incentivizeRecurPool`이 호출되고 `periodFinish`에 아직 도달하지 않았을 때, `state.rewardRate`에 추가하는 대신 새로운 `incentiveAmount`만으로 구성된 `newRewardRate`를 `state.rewardRate`로 잘못 설정합니다.

```solidity
    function incentivizeRecurPool(RecurIncentiveParams[] calldata params, address incentiveToken)
        external
        returns (uint256 totalIncentiveAmount)
    {
        // ...

            // record new reward
>>>         uint256 newRewardRate;
            if (block.timestamp >= periodFinish) {
                // current period is over
                // uint256 internal constant REWARD_RATE_PRECISION = 1e6;
                newRewardRate = params[i].incentiveAmount.mulDiv(REWARD_RATE_PRECISION, key.duration);
                state.rewardRate = newRewardRate;
                state.lastUpdateTime = uint64(block.timestamp);
                state.periodFinish = uint64(block.timestamp + key.duration);
            } else {
                // period is still active
                // add the new reward to the existing period
                uint256 remaining = periodFinish - block.timestamp;
                // @audit - this is removing previous rewardRate
>>>             newRewardRate += params[i].incentiveAmount.mulDiv(REWARD_RATE_PRECISION, remaining);

>>>             state.rewardRate = newRewardRate;
                state.lastUpdateTime = uint64(block.timestamp);
            }
            // prevent overflow when computing rewardPerToken
            if (newRewardRate >= ((type(uint256).max / PRECISION_DIV_REWARD_RATE_PRECISION) / key.duration)) {
                revert MasterBunni__AmountTooLarge();
            }

            totalIncentiveAmount += params[i].incentiveAmount;
        }

        // ...
    }
```

이로 인해 다른 인센티브 제공자의 이전 `rewardRate`가 효과적으로 제거되어 보상이 손실됩니다.

## 권장 사항

`newRewardRate`로 `state.rewardRate`를 대체하는 대신 `state.rewardRate`에 `newRewardRate`를 추가하세요.

```diff
    function incentivizeRecurPool(RecurIncentiveParams[] calldata params, address incentiveToken)
        external
        returns (uint256 totalIncentiveAmount)
    {
        // ...

            // record new reward
            uint256 newRewardRate;
            if (block.timestamp >= periodFinish) {
                // current period is over
                // uint256 internal constant REWARD_RATE_PRECISION = 1e6;
                newRewardRate = params[i].incentiveAmount.mulDiv(REWARD_RATE_PRECISION, key.duration);
                state.rewardRate = newRewardRate;
                state.lastUpdateTime = uint64(block.timestamp);
                state.periodFinish = uint64(block.timestamp + key.duration);
            } else {
                // period is still active
                // add the new reward to the existing period
                uint256 remaining = periodFinish - block.timestamp;
                // @audit - this is removing previous rewardRate
                newRewardRate += params[i].incentiveAmount.mulDiv(REWARD_RATE_PRECISION, remaining);

-                state.rewardRate = newRewardRate;
+                state.rewardRate += newRewardRate;
                state.lastUpdateTime = uint64(block.timestamp);
            }
            // prevent overflow when computing rewardPerToken
            if (newRewardRate >= ((type(uint256).max / PRECISION_DIV_REWARD_RATE_PRECISION) / key.duration)) {
                revert MasterBunni__AmountTooLarge();
            }

            totalIncentiveAmount += params[i].incentiveAmount;
        }

        // ...
    }
```
# [H-01] `joinRecurPool`이 `userPoolCounts`를 잘못 증가시킬 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

사용자는 현재 `stakeToken` 잔액이 증가할 때 `joinRecurPool`을 호출하여 스테이킹된 잔액을 업데이트할 수 있습니다.

```solidity
    function joinRecurPool(RecurPoolKey[] calldata keys) external nonReentrant {
        address msgSender = LibMulticaller.senderOrSigner();
        for (uint256 i; i < keys.length; i++) {
            RecurPoolKey calldata key = keys[i];

            /// -----------------------------------------------------------------------
            /// Validation
            /// -----------------------------------------------------------------------

            // key should be valid
            if (!isValidRecurPoolKey(key)) continue;

            // user should have non-zero balance
            uint256 balance = ERC20(address(key.stakeToken)).balanceOf(msgSender);
            if (balance == 0) {
                continue;
            }

            // user's balance should be locked with this contract as the unlocker
            if (
                !key.stakeToken.isLocked(msgSender)
                    || key.stakeToken.unlockerOf(msgSender) != IERC20Unlocker(address(this))
            ) {
                continue;
            }

            /// -----------------------------------------------------------------------
            /// Storage loads
            /// -----------------------------------------------------------------------

            RecurPoolId id = key.toId();
            RecurPoolState storage state = recurPoolStates[id];
            uint256 stakedBalance = state.balanceOf[msgSender];

            // can't stake in a pool twice
>>>         if (balance <= stakedBalance) {
                continue;
            }

            // ...

            // stake
            state.totalSupply = totalSupply - stakedBalance + balance;
            state.balanceOf[msgSender] = balance;

            // increment user pool count
            // @audit - this should check balance before is 0
            unchecked {
>>>             ++userPoolCounts[msgSender][key.stakeToken];
            }

            // emit event
            emit JoinRecurPool(msgSender, keys[i]);
        }
    }
```

그러나 이 작업이 `stakedBalance`만 업데이트하고 반복 풀(recur pool)에 처음 참여하는 것이 아닌 경우에도 `userPoolCounts`가 증가합니다. 여기서 `userPoolCounts`를 잘못 증가시키면 사용자가 토큰을 잠금 해제하지 못하게 됩니다.

## 권장 사항

이전 `stakedBalance`가 0일 때만 `userPoolCounts`를 증가시키십시오.

# [H-02] `_settleCurrency`가 `settle`을 호출할 때 `poolManager`와 제대로 상호 작용하지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

실행자(executor)의 `unlockCallback` 내에서 스왑이 수행된 후 `_settleCurrency`를 트리거하여 모든 통화를 정산합니다. 그러나 Uniswap V4의 `poolManager`에서 통화를 정산할 때 올바른 `settle` 함수가 사용되지 않아 Uniswap V4와의 상호 작용이 항상 실패합니다.

```solidity
    function _settleCurrency(Currency currency) internal {
        int256 amount = poolManager.currencyDelta(address(this), currency);
        if (amount < 0) {
            // address(this) owes PoolManager currency
            // contract already has input tokens in its balance
            // so we directly transfer tokens to PoolManager
            uint256 absAmount = uint256(-amount);
            if (currency.isNative()) {
                // native currency (e.g. ETH)
                poolManager.settle{value: absAmount}();
            } else {
                // ERC20 token
                poolManager.sync(currency);
                currency.transfer(address(poolManager), absAmount);
                // @audit - should use poolManager.settle()
                IPoolManagerOld(address(poolManager)).settle(currency); // TODO: compatible with old sepolia v4 deploy, use poolManager.settle() in production
            }
        } else if (amount > 0) {
            // address(this) has positive balance in PoolManager
            // take tokens from PoolManager to address(this)
            // the reactor will use transferFrom() to take tokens from address(this)
            poolManager.take(currency, address(this), uint256(amount));
        }
    }
```

## 권장 사항

메인넷 `settle`을 사용하십시오:

```diff
    function _settleCurrency(Currency currency) internal {
        int256 amount = poolManager.currencyDelta(address(this), currency);
        if (amount < 0) {
            // address(this) owes PoolManager currency
            // contract already has input tokens in its balance
            // so we directly transfer tokens to PoolManager
            uint256 absAmount = uint256(-amount);
            if (currency.isNative()) {
                // native currency (e.g. ETH)
                poolManager.settle{value: absAmount}();
            } else {
                // ERC20 token
                poolManager.sync(currency);
                currency.transfer(address(poolManager), absAmount);
                // @audit - should use poolManager.settle()
-               IPoolManagerOld(address(poolManager)).settle(currency); // TODO: compatible with old sepolia v4 deploy, use poolManager.settle() in production
+               poolManager.settle();
            }
        } else if (amount > 0) {
            // address(this) has positive balance in PoolManager
            // take tokens from PoolManager to address(this)
            // the reactor will use transferFrom() to take tokens from address(this)
            poolManager.take(currency, address(this), uint256(amount));
        }
    }
```

# [H-03] `receive()` 함수 누락

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`BunniZapIn` 계약은 `unwrapEthOutput` 함수 실행 결과와 입금 및 스왑에서 환불된 금액으로 네이티브 ETH를 받을 수 있어야 합니다. 이 ETH를 받으려면 계약에 `fallback` 또는 `receive` 메서드가 있어야 합니다. 그러나 이 계약에는 어느 것도 없습니다.

```solidity
    function unwrapEthOutput() external nonReentrant {
        weth.withdraw(weth.balanceOf(address(this)));
    }
```

## 권장 사항

`receive()` 함수 구현을 고려하십시오:

```solidity
receive() external payable {}
```

# [M-01] `depositIncentive`와 `incentivizeRecurPool`이 `incentiveToken`이 존재하는지 확인하지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`depositIncentive`와 `incentivizeRecurPool` 모두 solady의 `SafeTransferLib`를 사용하여 `incentiveToken`을 `MasterBunni`로 전송합니다.

```solidity
    function depositIncentive(RushIncentiveParams[] calldata params, address incentiveToken, address recipient)
        external
        nonReentrant
        returns (uint256 totalIncentiveAmount)
    {
        // ...

        // transfer incentive tokens to this contract
        if (totalIncentiveAmount != 0) {
            // @audit - doesn't use the latest version of solady
            incentiveToken.safeTransferFrom2(msgSender, address(this), totalIncentiveAmount);
        }

        // emit event
        emit DepositIncentive(msgSender, incentiveToken, recipient, params, totalIncentiveAmount);
    }
```

solady의 구현 내부에서는 반환 데이터가 없으면 함수가 항상 성공합니다.

```solidity
    function trySafeTransferFrom(address token, address from, address to, uint256 amount)
        internal
        returns (bool success)
    {
        /// @solidity memory-safe-assembly
        assembly {
            let m := mload(0x40) // Cache the free memory pointer.
            mstore(0x60, amount) // Store the `amount` argument.
            mstore(0x40, to) // Store the `to` argument.
            mstore(0x2c, shl(96, from)) // Store the `from` argument.
            mstore(0x0c, 0x23b872dd000000000000000000000000) // `transferFrom(address,address,uint256)`.
            success :=
                and( // The arguments of `and` are evaluated from right to left.
                    or(eq(mload(0x00), 1), iszero(returndatasize())), // Returned 1 or nothing.
                    call(gas(), token, 0, 0x1c, 0x64, 0x00, 0x20)
                )
            mstore(0x60, 0) // Restore the zero slot to zero.
            mstore(0x40, m) // Restore the free memory pointer.
        }
    }
```

이는 공격자가 계약이 아닌 주소(non-contract)를 함수에 제공할 수 있고 함수가 성공할 수 있음을 의미합니다. 이는 `incentiveToken`이 예측 가능한 주소를 가진 곧 생성될 계약인 경우, 예를 들어 Bunni LP 토큰인 경우에 문제가 됩니다. 예를 들어, 사용자가 곧 생성될 다른 Bunni LP 토큰과 함께 Bunni LP를 스테이킹하기 위한 보상 풀을 생성하려는 경우 공격자는 작업을 선행 실행(front-run)하고 가짜 보상을 제공하여 풀 보상 회계를 방해할 수 있습니다.

## 권장 사항

`incentiveToken`의 코드 크기를 확인하거나 코드 크기도 라이브러리 내에서 확인되는 최신 버전의 solady를 사용하는 것을 고려하십시오.

# [M-02] joinRushPool에서의 DOS 공격

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

MasterBunni의 `joinRushPool` 함수를 사용하면 사용자가 최대 상한선까지 토큰을 스테이킹할 수 있습니다. 그러나 풀 가입과 종료 사이에 시간 제한이 없어 악의적인 행위자가 샌드위치 공격을 실행하여 합법적인 사용자가 스테이킹하는 것을 막을 수 있는 취약점이 발생합니다.

```solidity
    function joinRushPool(RushPoolKey[] calldata keys) external nonReentrant {
        ...
            {
                uint256 balance = ERC20(address(keys[i].stakeToken)).balanceOf(msgSender);
                stakeAmountUpdated = remainderStakeAmount + balance > keys[i].stakeCap
                    ? keys[i].stakeCap - remainderStakeAmount
                    : balance;
            }

            // ensure there is capacity left and that we're increasing the user's stake
            // the user's stake may increase when either
            // 1) the user isn't staked yet or
            // 2) the user staked & hit the stake cap but more capacity has opened up since then
            if (stakeAmountUpdated == 0 || stakeAmountUpdated <= userState.stakeAmount) {
                continue;
            }
        ...
    }
```

공격자의 단계:

- 공격자는 보류 중인 `joinRushPool` 트랜잭션을 관찰합니다.
- 공격자는 `joinRushPool`을 호출하여 풀을 상한선까지 채워 선행 실행(front-runs)합니다.
- 피해자의 트랜잭션은 남은 용량이 없어 되돌려집니다(revert).
- 공격자는 `exitRushPool`을 호출하여 스테이크를 인출하여 후행 실행(back-runs)합니다.

공격자는 이 패턴을 반복하여 다른 사용자가 풀에 가입하는 것을 지속적으로 차단할 수 있습니다. 유일한 비용은 샌드위치 트랜잭션에 대한 가스 요금입니다.

## 권장 사항

최소 스테이크 기간을 추가하거나 언스테이킹 지연을 구현하는 것이 좋습니다.

# [M-03] RecurPools에서 totalSupply = 0일 때 보상이 영구적으로 잠김

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

MasterBunni 계약에서 `_rewardPerToken` 함수를 사용하여 RecurPools에 대한 보상을 계산할 때 `totalSupply = 0`인 기간 동안 할당된 보상은 계약에 영구적으로 잠깁니다. 이는 다음과 같은 이유로 발생합니다.

1. `totalSupply = 0`일 때 함수는 경과 시간을 고려하지 않고 기존 rewardPerTokenStored를 반환합니다.

```solidity
    function _rewardPerToken(
        uint256 rewardPerTokenStored,
        uint256 totalSupply,
        uint256 lastTimeRewardApplicable,
        uint256 lastUpdateTime,
        uint256 rewardRate
    ) internal pure returns (uint256) {
        if (totalSupply == 0) {
            return rewardPerTokenStored; // @audit rewardPerTokenStored isn't updated but lastUpdateTime is updated
        }
        // mulDiv won't overflow since we check that rewardRate is less than (type(uint256).max / PRECISION_DIV_REWARD_RATE_PRECISION / duration)
        return rewardPerTokenStored
            + FixedPointMathLib.mulDiv(
                (lastTimeRewardApplicable - lastUpdateTime) * PRECISION_DIV_REWARD_RATE_PRECISION, rewardRate, totalSupply
            );
    }
```

2. `_rewardPerToken`이 호출된 후 `lastUpdateTime`은 항상 업데이트됩니다.

```solidity
    state.lastUpdateTime = lastTimeRewardApplicable;
```

이는 인센티브가 할당된(`rewardRate > 0`) 기간 동안 `total supply is 0`(스테이커 없음)이고 `lastUpdateTime`과 `periodFinish` 사이에 시간이 흐르면 이 기간 동안 분배될 예정이었던 보상이 다음과 같은 이유로 계약에 영구적으로 잠기게 됨을 의미합니다.

- 어떤 스테이커에게도 분배되지 않음
- 인센티브 예금자가 이를 회수할 메커니즘이 없음
- `lastUpdateTime`이 업데이트되어 기간이 처리된 것으로 표시됨

## 권장 사항

다음과 같이 하는 것이 좋습니다.

- 미래의 스테이커를 위해 보상이 누적되도록 `totalSupply = 0`일 때 `lastUpdateTime` 업데이트를 건너뛰거나
- 기간이 끝난 후 인센티브 제공자가 분배되지 않은 보상을 회수할 수 있도록 허용
# [M-04] TWAP 계산 시 잘못된 틱 반올림

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`BunniHookOracle._queryTwap()`에서 산술 평균 틱은 `tickCumulativesDelta`를 윈도우 크기로 나누어 계산합니다. 그러나 `tickCumulativesDelta`가 음수일 때 Solidity의 정수 나눗셈은 0을 향해(위쪽으로) 반올림합니다. 이로 인해 틱 값이 1만큼 차이가 나 가격 계산에 영향을 줄 수 있습니다. 음의 무한대를 향해 일관되게 내림해야 합니다.

```solidity
    function _queryTwap(PoolKey memory poolKey, uint32 twapSecondsAgoStart, uint32 twapSecondsAgoEnd)
        internal
        view
        returns (int24 arithmeticMeanTick)
    {
        ...
        int56 tickCumulativesDelta = tickCumulatives[1] - tickCumulatives[0];
        return int24(tickCumulativesDelta / int56(uint56(windowSize))); // @audit rounding towards zero if tickCumulativesDelta < 0
    }
```

## 권장 사항

틱 계산에 대해 일관된 내림 동작을 구현하십시오:

```diff
    function _queryTwap(PoolKey memory poolKey, uint32 twapSecondsAgoStart, uint32 twapSecondsAgoEnd)
        internal
        view
        returns (int24 arithmeticMeanTick)
    {
        ...
        int56 tickCumulativesDelta = tickCumulatives[1] - tickCumulatives[0];
+       arithmeticMeanTick = int24(tickCumulativesDelta / int56(uint56(windowSize)));
+       // Always round to negative infinity
+       if (tickCumulativesDelta < 0 && (tickCumulativesDelta % windowSize != 0)) arithmeticMeanTick--;
+       return arithmeticMeanTick;
-       return int24(tickCumulativesDelta / int56(uint56(windowSize)));
    }
```

# [L-01] 잘못된 도메인 구분자 캐싱으로 permit2 통합이 깨질 수 있음

`BunniZapIn` 계약은 생성 중에 Permit2 도메인 구분자를 불변(immutable) 변수로 캐시합니다. 체인이 `chainID`를 변경하는 하드 포크를 겪는 경우 도메인 구분자가 Permit2에서 사용하는 것과 더 이상 일치하지 않으므로 서명 검증 실패가 발생할 수 있습니다.

```solidity
    constructor(address payable zeroExProxy_, WETH weth_, IBunniHub bunniHub_) {
        zeroExProxy = zeroExProxy_;
        weth = weth_;
        bunniHub = bunniHub_;
        permit2DomainSeparator = IPermit2(SafeTransferLib.PERMIT2).DOMAIN_SEPARATOR(); // @audit permit2DomainSeparator is fixed
    }
```

Permit2 계약은 체인 ID가 변경될 때 도메인 구분자를 동적으로 다시 계산합니다.

```solidity
    /// @notice Returns the domain separator for the current chain.
    /// @dev Uses cached version if chainid and address are unchanged from construction.
    function DOMAIN_SEPARATOR() public view returns (bytes32) {
        return block.chainid == _CACHED_CHAIN_ID
            ? _CACHED_DOMAIN_SEPARATOR
            : _buildDomainSeparator(_TYPE_HASH, _HASHED_NAME);
    }
```

도메인 구분자를 불변으로 캐싱하는 대신 필요할 때 Permit2에서 동적으로 쿼리하는 것이 좋습니다.

# [L-02] 토큰이 USDT인 경우 `safeApprove`가 되돌려질(revert) 수 있음

`BunniExecutor` 계약에서 원자로(reactor)에 스왑 출력 토큰을 승인할 때 토큰이 USDT이고 이미 0이 아닌 다른 금액으로 승인된 경우 다음 승인은 되돌려집니다.

```solidity
    function unlockCallback(bytes calldata data) external onlyPoolManager returns (bytes memory) {
        ...
        // approve swap output tokens to the reactor
        for (uint256 i; i < tokensToApproveForReactor.length; i++) {
            tokensToApproveForReactor[i].safeApprove(address(reactor), type(uint256).max); // @audit revert if token is USDT
        }
        ...
    }
```

새로운 금액을 승인하기 전에 승인을 0으로 설정하는 것이 좋습니다.

# [L-03] `BunniHookOracle`의 매개변수 유효성 검사 부족

`BunniHookOracle` 계약 매개변수는 계약의 예상치 못한 동작을 방지하기 위해 합리적인 범위 내에 있어야 합니다. `multiplier`는 `MULTIPLIER_DENOM`보다 작아야 하며 `secs_` 및 `ago_`는 합리적이어야 합니다.

```solidity
    constructor(
        IBunniHook bunniHook_,
        PoolKey memory poolKey,
        address paymentToken_,
        address underlyingToken_,
        address owner_,
        uint16 multiplier_,
        uint32 secs_,
        uint32 ago_,
        uint128 minPrice_
    ) {
        bunniHook = bunniHook_;
        paymentToken = paymentToken_;
        underlyingToken = underlyingToken_;

>>      multiplier = multiplier_;
>>      secs = secs_;
>>      ago = ago_;
        minPrice = minPrice_;
<...>
    function setParams(uint16 multiplier_, uint32 secs_, uint32 ago_, uint128 minPrice_) external onlyOwner {
>>      multiplier = multiplier_;
>>      secs = secs_;
>>      ago = ago_;
        minPrice = minPrice_;
        emit SetParams(multiplier_, secs_, ago_, minPrice_);
    }
```

# [L-04] 토큰 소수점(decimals) 확인 부족

`OptionsToken` 계약은 18 소수점 토큰과만 호환되지만 `OptionsToken`이나 `BunniHookOracle` 계약에는 다른 소수점을 가진 토큰의 사용을 방지할 수 있는 확인이 없습니다.

```solidity
/// @dev Assumes the underlying token and the payment token both use 18 decimals.
contract OptionsToken is ERC20Multicaller, Ownable {
```

`OptionsToken` 또는 `BunniHookOracle` 계약에 해당 확인을 구현하는 것을 고려해 보십시오.

# [L-05] 머클 트리 리프 생성이 단일 해시되어 두 번째 프리이미지 공격(second preimage attack)으로 이어질 수 있음

리프가 단일 해시된 머클 트리는 [두 번째 프리이미지 공격](https://flawed.net.nz/2018/02/21/attacking-merkle-trees-with-a-second-preimage-attack/)에 취약합니다. 올바른 방법은 [OpenZeppelin이 제안하는](https://github.com/OpenZeppelin/merkle-tree#standard-merkle-trees) 대로 이중 해시하는 것입니다. 문제는 `VeAirdrop` 계약에 존재합니다.

```solidity
    function claim(uint256 amount, bytes32[] calldata proof) external {
<...>
        bytes32 leaf = keccak256(abi.encodePacked(msgSender, amount));
```

OpenZeppelin 권장 사항을 따르는 것을 고려해 보십시오:

```solidity
bytes32 leaf = keccak256(bytes.concat(keccak256(abi.encode(addr, amount))));
```

# [L-06] 전송 수수료 토큰(fee-on-transfer tokens)에 대한 잘못된 회계

`MasterBunni` 계약은 전송 수수료 토큰이 인센티브 토큰으로 사용될 때 이를 제대로 회계 처리하지 못합니다. 계약은 수수료가 차감된 후 실제로 받은 금액이 아니라 수수료 전 금액을 기록합니다.

depositIncentive 함수에서:

```solidity
    function depositIncentive(RushIncentiveParams[] calldata params, address incentiveToken, address recipient)
        ...
            rushPoolIncentiveAmounts[id][incentiveToken] += params[i].incentiveAmount;

            // add incentive to depositor
            rushPoolIncentiveDeposits[id][incentiveToken][recipient] += params[i].incentiveAmount; // @audit Records full amount before fees
        ...

        // transfer incentive tokens to this contract
        if (totalIncentiveAmount != 0) {
            incentiveToken.safeTransferFrom2(msgSender, address(this), totalIncentiveAmount); // @audit Actual received amount will be less than totalIncentiveAmount for fee-on-transfer tokens
        }
        ...
    }
```

이로 인해 계약 상태에 기록된 인센티브 금액과 계약이 보유한 실제 토큰 잔액 간에 불일치가 발생합니다. 이는 다음과 같은 결과를 초래할 수 있습니다:

- 일부 사용자는 계약이 회계 처리된 것보다 적은 토큰을 보유하고 있으므로 기록된 전체 인센티브 금액을 인출할 수 없습니다.
- 마지막으로 인출하는 사용자는 잔액 부족으로 인해 트랜잭션이 되돌려질(revert) 수 있습니다.

```solidity
    function withdrawIncentive(RushIncentiveParams[] calldata params, address incentiveToken, address recipient)
        external
        nonReentrant
        returns (uint256 totalWithdrawnAmount)
    {

        // transfer incentive tokens to recipient
        if (totalWithdrawnAmount != 0) {
            incentiveToken.safeTransfer(recipient, totalWithdrawnAmount); // @audit Will revert if contract balance < totalWithdrawnAmount
        }
        ...
    }
```

전송 전후의 토큰 잔액 차이를 계산하여 실제 수령 금액을 추적하십시오.

# [L-07] 안전하지 않은 uint64 캐스팅으로 인한 오버플로우

이 문제는 `MasterBunni.incentivizeRecurPool()`에서 `block.timestamp + key.duration`이 `uint64`로 안전하지 않게 캐스팅될 때 발생합니다. `key.duration`은 사용자 제어 `uint256` 매개변수이므로 합계가 `type(uint64).max`를 쉽게 초과할 수 있습니다.

```solidity
    function incentivizeRecurPool(RecurIncentiveParams[] calldata params, address incentiveToken)
        external
        returns (uint256 totalIncentiveAmount)
    {
        ...
                state.rewardRate = newRewardRate;
                state.lastUpdateTime = uint64(block.timestamp);
                state.periodFinish = uint64(block.timestamp + key.duration); // @audit overflow if block.timestamp + duration > type(uint64).max
                ...
    }
```

타임스탬프 합계가 uint64 최대값을 초과하지 않도록 유효성 검사를 추가하는 것이 좋습니다.

# [L-08] `incentivizeRecurPool`이 추가된 `newRewardRate`가 0인지 확인하지 않음

`incentivizeRecurPool`이 호출될 때 사용자는 제공하려는 `incentiveAmount`를 포함한 매개변수를 제공할 수 있습니다. 그러나 이 작업은 계산된 `newRewardRate`가 0이 아닌지 확인하지 않습니다. 이로 인해 호출자가 보상 비율을 높이지 못하면서 여전히 계약에 토큰을 보낼 수 있습니다. 이 문제는 사용자가 먼지(dust) 수준의 `incentiveAmount`를 제공하거나 인센티브 토큰의 소수점 정밀도가 낮은 경우 발생할 수 있습니다. 계산된 `newRewardRate`가 0이 아닌지 확인하는 검사를 추가하는 것을 고려해 보십시오.

# [L-09] `nonReentrant` 수정자 누락

`MasterBunni.incentivizeRecurPool` 함수는 외부 호출이 있는 다른 모든 함수에는 있는 `nonReentrant` 수정자를 포함하지 않습니다.

```solidity
    function incentivizeRecurPool(RecurIncentiveParams[] calldata params, address incentiveToken)
        external
        returns (uint256 totalIncentiveAmount)
    {
```

# [L-10] `recipient` 주소에 대한 address(0) 확인 부족

`MasterBunni.depositIncentive` 함수는 `recipient` 주소가 0이 아닌지 확인하지 않습니다. 이로 인해 자산이 계약에 잠길 수 있습니다. `recipient` 주소가 0일 때 `msgSender` 변수를 기본값으로 사용하거나 해당 확인을 추가하는 것을 고려해 보십시오.

```solidity
    function depositIncentive(RushIncentiveParams[] calldata params, address incentiveToken, address recipient)
        external
        nonReentrant
        returns (uint256 totalIncentiveAmount)
    {
        address msgSender = LibMulticaller.senderOrSigner();

        // record incentive in each pool
        for (uint256 i; i < params.length; i++) {
            if (!isValidRushPoolKey(params[i].key) || block.timestamp >= params[i].key.startTimestamp) {
                // key is invalid or program is already active, skip
                continue;
            }

            // sum up incentive amount
            totalIncentiveAmount += params[i].incentiveAmount;

            RushPoolId id = params[i].key.toId();

            // add incentive to pool
            rushPoolIncentiveAmounts[id][incentiveToken] += params[i].incentiveAmount;

            // add incentive to depositor
>>          rushPoolIncentiveDeposits[id][incentiveToken][recipient] += params[i].incentiveAmount;
        }
<...>

    function withdrawIncentive(RushIncentiveParams[] calldata params, address incentiveToken, address recipient)
        external
        nonReentrant
        returns (uint256 totalWithdrawnAmount)
    {
        address msgSender = LibMulticaller.senderOrSigner();

        // subtract incentive tokens from each pool
        for (uint256 i; i < params.length; i++) {
            if (!isValidRushPoolKey(params[i].key) || block.timestamp >= params[i].key.startTimestamp) {
                // key is invalid or program is already active, skip
                continue;
            }

            // sum up withdrawn amount
            totalWithdrawnAmount += params[i].incentiveAmount;

            RushPoolId id = params[i].key.toId();

            // subtract incentive from pool
            rushPoolIncentiveAmounts[id][incentiveToken] -= params[i].incentiveAmount;

            // subtract incentive from sender
>>          rushPoolIncentiveDeposits[id][incentiveToken][msgSender] -= params[i].incentiveAmount;
        }
```

# [L-11] 슬리피지 보호 부족

`TokenMigrator.migrate` 함수는 `newTokenPerOldToken` 변수를 비율로 사용하여 이전 토큰을 새 토큰으로 스왑합니다. 사용자는 호출 직전에 계약 소유자가 `newTokenPerOldToken` 변수를 변경하는 경우 예상보다 적은 수의 새 토큰을 받을 위험이 있습니다.

```solidity
    function migrate(uint256 oldTokenAmount, address recipient) external returns (uint256 newTokenAmount) {
<...>
    function setNewTokenPerOldToken(uint256 newTokenPerOldToken_) external onlyOwner {
        newTokenPerOldToken = newTokenPerOldToken_;
        emit SetNewTokenPerOldToken(newTokenPerOldToken_);
    }
```
