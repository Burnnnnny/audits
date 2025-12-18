# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Storm-Labs-Inc/cove-contracts-core** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Cove 소개 (About Cove)

Cove는 코프로그래밍 방식 주문을 사용하여 포트폴리오를 효율적으로 리밸런싱함으로써 수익을 극대화하고, 오프체인 거래 매칭과 CoW Swap을 통한 최적화된 실행을 통해 리밸런싱 대비 손실(LVR)을 제거하는 자동화된 자산 관리 시스템입니다. ERC-4626 토큰화된 볼트를 활용하여 예금을 집계하고 슬리피지와 MEV를 최소화하며, 원활한 온체인 수익 창출을 위한 전문가가 선별한 전략을 제공합니다.

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

_검토 커밋 해시_ - [6b607d137f898c0f421b4ba4e748f41b09b41518](https://github.com/Storm-Labs-Inc/cove-contracts-core/tree/6b607d137f898c0f421b4ba4e748f41b09b41518)

_수정 검토 커밋 해시_ - [5d917e9026c2d6072c56d4e1f8e8c2bd69f9124e](https://github.com/Storm-Labs-Inc/cove-contracts-core/tree/5d917e9026c2d6072c56d4e1f8e8c2bd69f9124e)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `AnchoredOracle`
- `AssetRegistry`
- `BasketManager`
- `BasketToken`
- `FeeCollector`
- `Rescuable`
- `BasketManagerStorage`
- `Trades`
- `CoWSwapAdapter`
- `CoWSwapClone`
- `TokenSwapAdapter`
- `AutomaticWeightStrategy`
- `ManagedWeightStrategy`
- `StrategyRegistry`
- `WeightStrategy`
- `BasketManagerUtils`
- `BitFlag`
- `Errors`
- `MathUtils`

# 발견 사항 (Findings)

# [H-01] 잘못된 바구니 USD 가치는 잘못된 결과를 초래함

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

토큰 스왑을 제안할 때 다음과 같은 함수 호출 시퀀스가 있습니다:

```solidity
uint256[] memory totalValues = new uint256[](numBaskets);
// 2d array of asset balances for each basket
uint256[][] memory basketBalances = new uint256[][](numBaskets);
_initializeBasketData(self, baskets, basketAssets, basketBalances, totalValues);
// NOTE: for rebalance retries the internal trades must be updated as well
_processInternalTrades(self, internalTrades, baskets, basketBalances);
_validateExternalTrades(self, externalTrades, baskets, totalValues, basketBalances);
if (!_isTargetWeightMet(self, baskets, basketTargetWeights, basketAssets, basketBalances, totalValues)) {
         revert TargetWeightsNotMet();
}
```

`totalValues` 배열은 바구니의 총 USD 가치이며, 배열의 요소당 하나의 바구니입니다. `_initialBasketData()`를 호출할 때 채워진 다음 `_validateExternalTrades()`에 제공되어 거래에 따라 배열이 조작됩니다. 그 후 `_isTargetWeightMet()`에서 편차를 확인할 때 사용됩니다:

```solidity
uint256 afterTradeWeight = FixedPointMathLib.fullMulDiv(assetValueInUSD, _WEIGHT_PRECISION, totalValues[i]);
if (MathUtils.diff(proposedTargetWeights[j], afterTradeWeight) > _MAX_WEIGHT_DEVIATION) {
         return false;
}
```

코드는 호출 중 `_processInternalTrades()` 동안 바구니의 USD 가치가 고정되어 있다고 가정하면 올바르게 작동합니다. 그러나 내부 거래 처리 시 수수료가 발생하기 때문에 실제로는 그렇지 않습니다:

```solidity
if (swapFee > 0) {
                info.feeOnSell = FixedPointMathLib.fullMulDiv(trade.sellAmount, swapFee, 20_000);
                self.collectedSwapFees[trade.sellToken] += info.feeOnSell;
                emit SwapFeeCharged(trade.sellToken, info.feeOnSell);
            }
if (swapFee > 0) {
                info.feeOnBuy = FixedPointMathLib.fullMulDiv(initialBuyAmount, swapFee, 20_000);
                self.collectedSwapFees[trade.buyToken] += info.feeOnBuy;
                emit SwapFeeCharged(trade.buyToken, info.feeOnBuy);
            }
```

이로 인해 수수료 금액에 따라 `fromBasket`과 `toBasket`의 USD 가치가 모두 낮아지므로, 내부 거래 처리 중에 `totalValues` 배열이 변경되지 않아 동기화되지 않으므로 편차 가중치 확인이 부정확해집니다.

## 권장 사항

내부 거래를 처리할 때 총 가치 배열을 제공하고 적용된 수수료를 반영하십시오.

# [H-02] 목표 가중치 확인 시 상환 요청 자산을 고려하지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

리밸런스 과정에서 목표 잔액은 구성된 목표 가중치뿐만 아니라 리밸런스 에포크 내에서 처리된 인출 요청에도 따라 달라집니다. 그러나 `proposeTokenSwap` 및 `completeRebalance` 내에서 `_isTargetWeightMet` 확인이 수행될 때 구성된 목표 가중치만 고려하고 처리된 인출 요청은 고려하지 않습니다.

```solidity
    function _isTargetWeightMet(
        BasketManagerStorage storage self,
        address[] calldata baskets,
        uint64[][] calldata basketsTargetWeights,
        address[][] calldata basketAssets,
        uint256[][] memory basketBalances,
        uint256[] memory totalValues
    )
        private
        view
        returns (bool)
    {
        // _isTargetWeightMet(self, baskets, basketTargetWeights, basketAssets, basketBalances, totalValues)
        // Check if total weight change due to all trades is within the _MAX_WEIGHT_DEVIATION threshold
        uint256 len = baskets.length;
        for (uint256 i = 0; i < len;) {
            // slither-disable-next-line calls-loop
            uint64[] calldata proposedTargetWeights = basketsTargetWeights[i];
            // nosemgrep: solidity.performance.state-variable-read-in-a-loop.state-variable-read-in-a-loop
            address[] calldata assets = basketAssets[i];
            // nosemgrep: solidity.performance.array-length-outside-loop.array-length-outside-loop
            uint256 proposedTargetWeightsLength = proposedTargetWeights.length;
            for (uint256 j = 0; j < proposedTargetWeightsLength;) {
                address asset = assets[j];
                uint256 assetValueInUSD =
                // nosemgrep: solidity.performance.state-variable-read-in-a-loop.state-variable-read-in-a-loop
                 self.eulerRouter.getQuote(basketBalances[i][j], asset, _USD_ISO_4217_CODE);
                // Rounding direction: down
                uint256 afterTradeWeight =
                    FixedPointMathLib.fullMulDiv(assetValueInUSD, _WEIGHT_PRECISION, totalValues[i]);
                if (MathUtils.diff(proposedTargetWeights[j], afterTradeWeight) > _MAX_WEIGHT_DEVIATION) {
                    return false;
                }
                unchecked {
                    // Overflow not possible: j is bounded by proposedTargetWeightsLength
                    ++j;
                }
            }
            unchecked {
                // Overflow not possible: i is bounded by len
                ++i;
            }
        }
        return true;
    }
```

즉, 기본 자산의 총 인출 가치가 기본 자산 가중치를 `_MAX_WEIGHT_DEVIATION` (5%) 이상 편차나게 하면 리밸런스 프로세스가 되돌리고 현재 인출 요청을 처리할 수 없습니다.

## 권장 사항

`_isTargetWeightMet` 확인에서 총 상환 요청을 고려하십시오.

# [M-01] `initialize()`에서 ERC20Permit 초기화 호출 누락

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

ERC20PermitUpgradeable을 상속하는 Solidity 스마트 계약 BasketToken에서 초기화 과정에 누락이 있는 것으로 보입니다. 계약의 초기 상태를 설정하도록 의도된 initialize 함수에는 `__ERC20Permit_init()`에 대한 호출이 포함되어 있지 않습니다. 이 함수는 BasketToken 계약의 일부인 ERC20 Permit 기능을 초기화하는 데 중요합니다. ERC20 Permit 기능을 사용하면 사용자가 개인 키로 토큰 전송에 대한 승인을 서명하여 가스 없는 거래를 수행할 수 있습니다. `__ERC20Permit_init()`을 호출하지 않으면 BasketToken 계약에서 이 기능이 올바르게 설정되지 않습니다.

[링크](https://github.com/Storm-Labs-Inc/cove-contracts-core/blob/6b607d137f898c0f421b4ba4e748f41b09b41518/src/BasketToken.sol#L193)

## 권장 사항

`__ERC20Permit_init()` 호출을 포함하도록 initialize 함수를 수정하십시오.
이 함수는 ERC20 Permit 기능을 올바르게 초기화하기 위해 적절한 인수(일반적으로 토큰 이름)와 함께 호출되어야 합니다.

# [M-02] ERC-1271 주문이 임의의 데이터를 허용하는 경우 잉여금 손실

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

[CoWSwapClone::isValidSignature()](https://github.com/Storm-Labs-Inc/cove-contracts-core/blob/6b607d137f898c0f421b4ba4e748f41b09b41518/src/swap_adapters/CoWSwapClone.sol#L86)는 앱 데이터 필드를 확인하지 않습니다.

이는 앱 데이터 필드가 공격자에 의해 변경되어 해당 주문에 대해 서명이 유효하게 유지될 수 있는 모든 ERC-1271 주문에 적용됩니다 (예: isValidSignature가 주문의 appData 필드를 무시하기 때문).

공격자는 취약한 ERC-1271 주문을 조작하여 사용자 주문에서 예상되는 잉여금의 일부를 공격자가 제어하는 주소로 전송할 수 있습니다.

자세한 내용은 [여기](https://docs.cow.fi/cow-protocol/reference/contracts/core#loss-of-surplus-if-erc-1271-order-allows-arbitrary-app-data)에서 확인할 수 있습니다.

## 권장 사항

앱 데이터를 배포 시 변경 불가능하게 만들거나(또는 bytes(0)와 동일하게), 앱 데이터가 일치하지 않는 경우 isValidSignature가 주문을 거부하도록 하십시오.

# [M-03] 외부 거래 검증 시 편차 확인이 단방향임

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

외부 거래를 검증할 때 다음과 같은 코드가 있습니다:

```solidity
info.sellValue = self.eulerRouter.getQuote(trade.sellAmount, trade.sellToken, _USD_ISO_4217_CODE);
info.internalMinAmount = self.eulerRouter.getQuote(info.sellValue, _USD_ISO_4217_CODE, trade.buyToken);
info.diff = MathUtils.diff(info.internalMinAmount, trade.minAmount);

// Check if the given minAmount is within the _MAX_SLIPPAGE threshold of internalMinAmount
if (info.internalMinAmount < trade.minAmount) {
         if ((info.diff * _WEIGHT_PRECISION) / info.internalMinAmount > _MAX_SLIPPAGE) {
               revert ExternalTradeSlippage();
         }
}
```

제공된 `trade.minAmount`가 내부적으로 계산된 최소 금액의 `MAX_SLIPPAGE` 임계값 내에 있는지 확인합니다. 그러나 보시다시피 내부 최소 금액이 제공된 것보다 낮은 경우에만 수행합니다. 이것이 의도된 설계라면 다른 시나리오에서도 편차를 확인하는 것이 더 좋습니다. 다음과 같은 시나리오가 발생할 수 있기 때문입니다:

1. 거래된 자산의 가격이 1:1이며, 둘 다 100$ 가치가 있습니다.
2. 스왑 제안자가 자산을 거래하고 다른 자산 1개를 판매하면서 받을 최소 금액을 0.98 자산으로 설정합니다.
3. 거래가 여전히 멤풀(mempool)에 있는 동안, 구매할 자산의 가격은 90$로 떨어지고 판매할 자산은 100$로 유지됩니다.
4. 내부 최소 금액은 약 1.11 자산이 됩니다(100$ 상당의 한 자산을 제공하여 90$ 상당의 자산을 구매하기 때문).
5. 제공된 최소 금액인 0.98보다 높으므로 확인을 건너뜁니다.
6. 그러면 거래는 실제보다 훨씬 나쁜 비율로 실행될 수 있으며, 최악의 경우 100$ 상당의 자산을 주었는데 90$ 미만인 0.98 자산을 받을 수 있습니다.

## 권장 사항

양쪽에서 편차 확인을 실행하십시오.

# [M-04] `orderValidTo`는 `stepDelay`가 항상 15분이라고 가정함

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

리밸런스 프로세스가 수행되고 `externalTrades`를 통합할 때, 토큰 스왑 실행자가 `executeTokenSwap`을 호출하므로 `orderValidTo`를 15분으로 설정합니다. 이는 스왑 프로세스가 특정 시간 창을 초과하지 않도록 마감 기한을 설정합니다.

```solidity
    function executeTokenSwap(ExternalTrade[] calldata externalTrades, bytes calldata) external payable override {
>>>     uint32 validTo = uint32(block.timestamp + 15 minutes);
        _cowswapAdapterStorage().orderValidTo = validTo;
        for (uint256 i = 0; i < externalTrades.length;) {
            _createOrder(
                externalTrades[i].sellToken,
                externalTrades[i].buyToken,
                externalTrades[i].sellAmount,
                externalTrades[i].minAmount,
                validTo
            );
            unchecked {
                // Overflow not possible: i is bounded by externalTrades.length
                ++i;
            }
        }
    }
```

`completeRebalance` 함수는 또한 초기에 15분으로 구성된 `stepDelay`를 확인합니다. 이는 `completeRebalance`가 호출되기 전에 스왑 실행을 위한 약간의 시간을 제공합니다.

```solidity
    function completeRebalance(
        BasketManagerStorage storage self,
        ExternalTrade[] calldata externalTrades,
        address[] calldata baskets,
        uint64[][] calldata basketTargetWeights,
        address[][] calldata basketAssets
    )
        external
    {
        // Revert if there is no rebalance in progress
        // slither-disable-next-line incorrect-equality
        if (self.rebalanceStatus.status == Status.NOT_STARTED) {
            revert NoRebalanceInProgress();
        }
        _validateBasketHash(self, baskets, basketTargetWeights, basketAssets);
        // Check if the rebalance was proposed more than 15 minutes ago
        // slither-disable-next-line timestamp
>>>     if (block.timestamp - self.rebalanceStatus.timestamp < self.stepDelay) {
            revert TooEarlyToCompleteRebalance();
        }
        // ...
    }
```

그러나 `stepDelay`가 변경되면 `orderValidTo`는 여전히 15분으로 설정됩니다. 이는 문제를 일으킵니다. 예를 들어, 스왑 실행을 위해 더 많은 시간을 허용하도록 `stepDelay`가 15분 이상으로 구성된 경우 스왑 프로세스에는 여전히 15분의 실행 시간만 주어집니다.

## 권장 사항

`orderValidTo` 시간 창을 `stepDelay`와 일치시키는 것을 고려하십시오.

# [M-05] `updateBitFlag`는 기본 자산의 인덱스를 업데이트하지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`updateBitFlag` 함수를 사용하면 바구니의 자산 목록을 업데이트할 수 있지만, 새 `bitFlag`가 이전 `bitFlag`의 상위 집합이어야 한다는 제한이 있습니다. `updateBitFlag` 프로세스 중에 모든 검증이 통과하면 함수는 새 자산 목록을 반복하고 `basketAssetToIndexPlusOne`을 업데이트합니다. 그러나 `basketTokenToBaseAssetIndexPlusOne`은 업데이트하지 않습니다.

이전 `bitFlag`의 상위 집합인 `bitFlag`를 업데이트하면 `basketTokenToBaseAssetIndexPlusOne`에 잠재적으로 영향을 미칠 수 있습니다. 다음 시나리오를 고려하십시오:

1. 현재 bitFlag는 `1110`이며, 이는 자산 목록에 3개의 자산이 포함되어 있음을 의미합니다. 이 시나리오에서는 반환된 배열의 첫 번째 자산이 `basketTokenToBaseAssetIndexPlusOne`으로 설정되어 값 1 (0 + 1)이 됩니다.
2. 새 bitFlag는 `1111`이며, 이는 이전 `bitFlag`의 상위 집합입니다. 자산 목록에는 이제 4개의 자산이 포함됩니다. 그러나 `basketTokenToBaseAssetIndexPlusOne`이 업데이트되지 않았으므로 기본 자산 인덱스는 새로 포함된 자산을 가리킵니다. 이로 인해 이 인덱스에 의존하는 모든 함수가 잘못된 자산을 처리하게 됩니다.

## 권장 사항

`updateBitFlag`가 호출될 때 `basketTokenToBaseAssetIndexPlusOne`을 업데이트하는 것을 고려하십시오.

# [L-01] `ManagementFee` 계산이 문서와 일치하지 않음

문서는 계산이 `_MANAGEMENT_FEE_DECIMALS`로 나누어야 한다고 명시하고 있지만, 실제 코드는 `(_MANAGEMENT_FEE_DECIMALS - feeBps)`로 나눕니다.

```solidity
function _harvestManagementFee(uint16 feeBps, address feeCollector) internal {
        // Checks
        if (feeBps > _MAX_MANAGEMENT_FEE) {
            revert InvalidManagementFee();
        }
        uint256 timeSinceLastHarvest = block.timestamp - lastManagementFeeHarvestTimestamp;

        // Effects
        lastManagementFeeHarvestTimestamp = uint40(block.timestamp);
        if (feeBps != 0) {
            if (timeSinceLastHarvest != 0) {
                if (timeSinceLastHarvest != block.timestamp) {
                    // remove shares held by the treasury or currently pending redemption from calculation
                    uint256 currentTotalSupply = totalSupply() - balanceOf(feeCollector)
                        - pendingRedeemRequest(lastRedeemRequestId[feeCollector], feeCollector);
                    uint256 fee = FixedPointMathLib.fullMulDiv(
                        currentTotalSupply,
                        feeBps * timeSinceLastHarvest,
@>>                        ((_MANAGEMENT_FEE_DECIMALS - feeBps) * uint256(365 days))
                    );
                    if (fee != 0) {
                        emit ManagementFeeHarvested(fee);
                        _mint(feeCollector, fee);
                        // Interactions
                        FeeCollector(feeCollector).notifyHarvestFee(fee);
                    }
                }
            }
        }
    }
```

# [L-02] `_processInternalTrades()`의 `trade.maxAmount` 확인

[링크](https://github.com/Storm-Labs-Inc/cove-contracts-core/blob/6b607d137f898c0f421b4ba4e748f41b09b41518/src/libraries/BasketManagerUtils.sol#L827)
`trade.maxAmount`는 거래 상대방(`toBasket`)을 보호하도록 설계된 것으로 보입니다.

- `fromBasket`은 궁극적으로 `trade.minAmount` 이상의 `buyToken`을 받습니다.
- `toBasket`은 `trade.maxAmount` 이하의 `buyToken`을 지불합니다.

따라서 `info.netBuyAmount` 대신 `initialBuyAmount`와 비교해야 합니다.

# [L-03] `proRataRedeem()`에 `msg.sender`가 운영자인지 확인하는 기능이 없음

```solidity
function proRataRedeem(uint256 shares, address to, address from) public {
        // Effects
        uint16 feeBps = BasketManager(basketManager).managementFee(address(this));
        address feeCollector = BasketManager(basketManager).feeCollector();
        _harvestManagementFee(feeBps, feeCollector);
@>        if (msg.sender != from) {
            _spendAllowance(from, msg.sender, shares);
        }

        // Interactions
        BasketManager(basketManager).proRataRedeem(totalSupply(), shares, to);

        // We intentionally defer the `_burn()` operation until after the external call to
        // `BasketManager.proRataRedeem()` to prevent potential price manipulation via read-only reentrancy attacks. By
        // performing the external interaction before updating balances, we ensure that total supply and user balances
        // cannot be manipulated if a malicious contract attempts to reenter during the ERC20 transfer (e.g., through
        // ERC777 tokens or plugins with callbacks).
        _burn(from, shares);
    }
```

운영자는 모든 자금을 관리할 권한이 있습니다. msg.sender가 운영자인 경우 승인 없이 상환 작업을 수행할 수 있습니다.
권장 사항:
if (msg.sender != from) {
if (!isOperator[from][msg.sender]) {
\_spendAllowance(from, msg.sender, shares);
}
}

# [L-04] 수수료를 완전히 청구하려면 2회의 청구 배치가 필요함

스폰서 또는 재무부 수수료를 청구할 때 `BasketToken.proRataRedeem()`을 호출합니다:

```solidity
function proRataRedeem(uint256 shares, address to, address from) public {
        ...
        _harvestManagementFee(feeBps, feeCollector);
        ...

       BasketManager(basketManager).proRataRedeem(totalSupply(), shares, to);
       ...
}
```

여기서 우리는 다시 한번 수수료를 수확하여 지난 수확 이후 경과된 시간을 기준으로 재무부 및 스폰서 수수료를 다시 증가시키고 수수료 수집가에게 수확에 대해 알립니다:

```solidity
FeeCollector(feeCollector).notifyHarvestFee(fee);
```

이로 인해 다음과 같은 시나리오가 발생합니다:

1. 재무부가 `FeeCollector.claimTreasuryFees()`를 호출하여 수수료를 받습니다. 이는 해당 바구니 토큰에 대한 `claimableTreasuryFees` 매핑에 보관되어 있으며 청구할 주식은 100주입니다.
2. 매핑이 지워지고 `BasketToken.proRataRedeem()`이 호출되어 50 수수료를 수확하고, 알림 후 `claimableTreasuryFees`를 50주로 늘립니다.
3. 100주가 상환됩니다.
4. 아직 청구할 50주가 있습니다.

수수료를 완전히 청구하려면 2회의 호출 배치가 필요합니다. 대신, 상환할 수수료가 설정되기 전에 수수료를 수확하여 단일 호출로 충분하도록 하는 것을 고려하십시오.

# [L-05] 하드코딩된 슬리피지는 위험함

외부 거래를 검증할 때 다음과 같은 코드가 있습니다:

```solidity
if ((info.diff * _WEIGHT_PRECISION) / info.internalMinAmount > _MAX_SLIPPAGE) {
     revert ExternalTradeSlippage();
}
```

우리는 금액 간의 편차를 검증합니다. 문제는 하드코딩된 상수 `_MAX_SLIPPAGE`입니다. 가격이 빠르고 급격하게 변하는 시기에 하드코딩된 슬리피지는 함수를 호출할 수 없게 하여 풀을 리밸런싱 할 수 없게 하고, 결과적으로 상환이 막힐 수 있습니다. 최근 하드코딩된 슬리피지 확인으로 인해 계약의 중요한 기능을 호출할 수 없었던 사례가 있었습니다. 대신, 이러한 시나리오에서 변경할 수 있도록 슬리피지에 대한 설정자(setter)를 추가하십시오.

# [L-06] `BasketManager`의 `execute`에 대한 제한 부족

현재 `execute` 함수는 임의의 주소를 호출하고 `rescue` 하에서 제한된 작업인 `BasketManager` 내에 보관되고 `assetRegistry`에 등록된 자산의 전송을 포함하여 임의의 작업을 수행할 수 있습니다. `target`도 제한하는 것을 고려하십시오. `target`이 `assetRegistry`에 등록된 자산인 경우 작업이 되돌려져야 합니다.

# [L-07] 빈 `externalTrades`로 `executeTokenSwap`을 호출할 수 있음

리밸런싱 프로세스 중에 `externalTrades`가 비어 있고 외부 스왑을 수행해서는 안 될 가능성이 있습니다. 그러나 여전히 빈 `externalTrades`로 `executeTokenSwap`을 호출할 수 있습니다. 이는 `validTo`, `timestamp` 및 상태만 `TOKEN_SWAP_EXECUTED`로 업데이트하므로 심각한 영향은 없지만 `externalTrades` 길이가 0일 때 `executeTokenSwap`을 되돌리는 것을 고려하십시오.

# [L-08] 외부 거래 `tradeOwnership`이 검증되지 않음

`tradeOwnership`에 대한 검증이 부족하기 때문에 외부 거래의 총 `tradeOwnership`이 `_WEIGHT_PRECISION`보다 낮거나 클 수 있으며, 이는 바구니 내 자산 잔액 계산을 깨뜨릴 수 있습니다. 각 외부 거래의 총 `tradeOwnershi`p가 `_WEIGHT_PRECISION`과 같은지 확인하는 것을 고려하십시오.

# [L-09] 리밸런스 요구 사항 확인이 너무 민감함

현재 자산의 현재 `balances`와 `targetBalances` 간의 차이가 0이 아닌 한 `shouldRebalance`는 true로 설정됩니다.

```solidity
    function _isRebalanceRequired(
        address[] memory assets,
        uint256[] memory balances,
        uint256[] memory targetBalances
    )
        private
        pure
        returns (bool shouldRebalance)
    {
        uint256 assetsLength = assets.length;
        for (uint256 j = 0; j < assetsLength;) {
            // slither-disable-start calls-loop
            if (
>>>             MathUtils.diff(balances[j], targetBalances[j]) > 0 // nosemgrep
            ) {
                shouldRebalance = true;
                break;
            }
            // slither-disable-end calls-loop
            unchecked {
                // Overflow not possible: j is less than assetsLength
                ++j;
            }
        }
    }
```

이로 인해 리밸런스 결정이 잔액 변경에 너무 민감해집니다. 0을 사용하는 대신 합리적인 임계값을 추가하는 것을 고려하십시오.

# [L-10] `_harvestManagementFee`는 청구 가능한 폴백 주식을 고려하지 않음

`_harvestManagementFee` 내에서 `currentTotalSupply`를 계산할 때 `feeCollector`의 현재 잔액과 보류 중인 상환 요청은 제외됩니다. 그러나 계산에는 `feeCollector`의 청구 가능한 폴백 주식이 포함되지 않으므로 `currentTotalSupply`에 잠재적으로 `feeCollector`의 주식이 여전히 포함될 수 있습니다. `currentTotalSupply` 계산에 청구 가능한 폴백 주식을 포함하는 것을 고려하십시오.

```diff
    function _harvestManagementFee(uint16 feeBps, address feeCollector) internal {
        // Checks
        if (feeBps > _MAX_MANAGEMENT_FEE) {
            revert InvalidManagementFee();
        }
        uint256 timeSinceLastHarvest = block.timestamp - lastManagementFeeHarvestTimestamp;

        // Effects
        lastManagementFeeHarvestTimestamp = uint40(block.timestamp);
        if (feeBps != 0) {
            if (timeSinceLastHarvest != 0) {
                if (timeSinceLastHarvest != block.timestamp) {
                    // remove shares held by the treasury or currently pending redemption from calculation
                    uint256 currentTotalSupply = totalSupply() - balanceOf(feeCollector)
-                        - pendingRedeemRequest(lastRedeemRequestId[feeCollector], feeCollector);
+                        - pendingRedeemRequest(lastRedeemRequestId[feeCollector], feeCollector)
+                        - claimableFallbackShares(feeCollector);
                    uint256 fee = FixedPointMathLib.fullMulDiv(
                        currentTotalSupply,
                        feeBps * timeSinceLastHarvest,
                        ((_MANAGEMENT_FEE_DECIMALS - feeBps) * uint256(365 days))
                    );
                    if (fee != 0) {
                        emit ManagementFeeHarvested(fee);
                        _mint(feeCollector, fee);
                        // Interactions
                        FeeCollector(feeCollector).notifyHarvestFee(fee);
                    }
                }
            }
        }
    }
```

# [L-11] 악의적인 사용자가 관리 수수료 적립을 줄일 수 있음

관리 수수료를 적립하기 위해 다음과 같은 코드가 있습니다:

```solidity
uint256 currentTotalSupply = totalSupply() - balanceOf(feeCollector) - pendingRedeemRequest(lastRedeemRequestId[feeCollector], feeCollector);
uint256 fee = FixedPointMathLib.fullMulDiv(currentTotalSupply, feeBps * timeSinceLastHarvest, ((_MANAGEMENT_FEE_DECIMALS - feeBps) * uint256(365 days)));
...
_mint(feeCollector, fee);
FeeCollector(feeCollector).notifyHarvestFee(fee);
```

수수료 수집가 주식 잔액만큼 총 공급량을 줄여 결정하고 있으므로, 악의적인 사용자는 총 공급량을 인위적으로 줄이기 위해 수수료 수집가에게 직접 토큰을 보낼 수 있습니다. 이는 악의적인 사용자에게는 손실이지만, 청구할 잔액을 내부 회계 값으로 만드는 `notifyHarvestFee()` 기능 때문에 특히 문제가 됩니다. 이로 인해 수수료 수집가에게 직접 보낸 주식은 청구할 수 없게 되며 공격자와 수수료 수취인 모두 자금을 잃게 되는 그리프(grief) 공격이 발생합니다.

`pendingRedeemRequest`의 경우에도 절대적으로 동일합니다. 사용자가 직접 상환을 예약하여 `currentTotalSupply` 값을 낮출 수 있기 때문입니다. `FeeCollector` 계약에는 이를 수행하는 기능이 없고 운영자를 설정하는 기능도 없으므로 상환은 절대 청구될 수 없습니다.

공격자가 피해를 입히는 것을 불가능하게 하기 위해 수수료 수집가 계약에 구조(rescue) 유형 기능을 추가하고, 상환을 청구하거나 운영자를 추가하는 기능을 추가하거나, 악의적인 사용자가 예약하지 않는 한 수수료 수집가가 상환을 청구할 가능성이 낮으므로 보류 중인 상환 요청에 대한 차감을 제거하는 것을 고려하십시오.

# [L-12] 관리 수수료가 적립되지 않을 수 있음

관리 수수료는 다음 공식을 사용하여 적립됩니다:

```solidity
uint256 fee = FixedPointMathLib.fullMulDiv(currentTotalSupply, feeBps * timeSinceLastHarvest, ((_MANAGEMENT_FEE_DECIMALS - feeBps) * uint256(365 days)));
```

이 공식은 내림할 수 있으며 그럴 가능성이 높은 2가지 이유가 있습니다. 첫째, 악의적인 사용자가 `timeSinceLastHarvest`가 매우 낮도록 충분히 자주 1주를 상환하여 `BasketToken.proRataRedeem()`을 호출할 수 있습니다. 두 번째 이유는 총 공급 초기화에 잘못된 변수가 사용된 것입니다:

```solidity
uint256 requiredDepositShares = basketValue > 0 ? FixedPointMathLib.fullMulDiv(pendingDepositValue, totalSupply, basketValue) : pendingDeposit;
```

바구니 가치가 0일 때 첫 번째 리밸런싱 시 `pendingDeposit`을 사용하여 주식을 얻습니다. 사용해야 할 변수는 실제로 `pendingDepositValue`여야 합니다. 첫째, 바구니 가치가 0을 넘을 때 주식을 계산할 때 사용되는 변수임을 알 수 있고, 둘째, 낮은 소수점 자산이 기본 자산인 경우 총 공급이 낮은 값이 되기 때문입니다. 예를 들어 기본 자산이 `WBTC`인 경우 총 공급은 8 소수점 자리가 되며 수수료 계산에서 내림되기 매우 쉽습니다.

첫째, 낮은 소수점 기본 자산이 사용되는 경우 공급의 소수점 자리가 낮지 않도록 `pendingDepositValue`를 사용하십시오. 둘째, 수수료가 적립되지 않은 경우 마지막 수확 이후 시간을 업데이트하지 않도록 `_harvestManagementFee()`를 리팩토링하는 것을 고려하십시오.

# [L-13] 0 주식/자산으로 `mint` 또는 `withdraw` 호출 시 손실 발생

사용자가 현재 이행되지 않은 보류 중인 인출 또는 예금을 가지고 있는 경우, 0 주식 또는 자산으로 `mint` 또는 `withdraw`를 호출하면 자산 손실이 발생할 수 있습니다.

```solidity
    function mint(uint256 shares, address receiver, address controller) public returns (uint256 assets) {
        // Checks
        _onlySelfOrOperator(controller);
        DepositRequestStruct storage depositRequest = _depositRequests[lastDepositRequestId[controller]];
        uint256 fulfilledShares = depositRequest.fulfilledShares;
        uint256 depositAssets = depositRequest.depositAssets[controller];
        // @audit - should also check, if shares is 0, revert
>>>     if (shares != _maxMint(fulfilledShares, depositAssets, depositRequest.totalDepositAssets)) {
            revert MustClaimFullAmount();
        }
        // Effects
        assets = _claimableDepositRequest(fulfilledShares, depositAssets);
        _claimDeposit(depositRequest, assets, shares, receiver, controller);
    }
```

```solidity
    function withdraw(uint256 assets, address receiver, address controller) public override returns (uint256 shares) {
        // @audit - should check assets is not 0
        // Checks
        _onlySelfOrOperator(controller);
        RedeemRequestStruct storage redeemRequest = _redeemRequests[lastRedeemRequestId[controller]];
        uint256 fulfilledAssets = redeemRequest.fulfilledAssets;
        uint256 redeemShares = redeemRequest.redeemShares[controller];
>>>     if (assets != _maxWithdraw(fulfilledAssets, redeemShares, redeemRequest.totalRedeemShares)) {
            revert MustClaimFullAmount();
        }
        shares = _claimableRedeemRequest(fulfilledAssets, redeemShares);
        // Effects
        _claimRedemption(redeemRequest, assets, shares, receiver, controller);
    }
```

`fulfilledShares`와 `fulfilledAssets`가 0이므로 `_maxWithdraw`와 `_maxMint`는 0을 반환하여 검사를 통과할 수 있습니다. 결과적으로 `_claimDeposit` 및 `_claimRedemption` 작업 내에서 주식과 자산은 0으로 설정됩니다.

```solidity
    function _claimRedemption(
        RedeemRequestStruct storage redeemRequest,
        uint256 assets,
        uint256 shares,
        address receiver,
        address controller
    )
        internal
    {
        // Effects
>>>     redeemRequest.redeemShares[controller] = 0;
        emit Withdraw(msg.sender, receiver, controller, assets, shares);
        // Interactions
        IERC20(asset()).safeTransfer(receiver, assets);
    }
```

```solidity
    function _claimDeposit(
        DepositRequestStruct storage depositRequest,
        uint256 assets,
        uint256 shares,
        address receiver,
        address controller
    )
        internal
    {
        // Effects
>>>     depositRequest.depositAssets[controller] = 0;
        emit Deposit(controller, receiver, assets, shares);
        // Interactions
        _transfer(address(this), receiver, shares);
    }
```

`shares`와 `assets`가 0인 경우 되돌리는 것을 고려하십시오.

# [L-14] `AutomaticWeightStrategy`가 완전히 구현되지 않음

```solidity
contract AutomaticWeightStrategy is WeightStrategy, AccessControlEnumerable, Multicall {
    constructor(address admin) payable {
        _grantRole(DEFAULT_ADMIN_ROLE, admin);
    }

@>    function getTargetWeights(uint256 bitFlag) public view virtual override returns (uint64[] memory targetWeights) { }

@>    function supportsBitFlag(uint256 bitFlag) public view virtual override returns (bool) { }
}
```

getTargetWeights() 및 supportsBitFlag()에 구현된 로직이 없음을 알 수 있습니다. 이는 알려진 문제일 수 있습니다.

AutomaticWeightStrategy가 감사 범위 내에 있으므로 이 문제는 제출되어야 합니다.

getTargetWeights() 및 supportsBitFlag() 함수를 완전히 구현하십시오.

# [L-15] 이미 수집된 `claimableSponsorFees`를 전송하지 않음

`setSponsor` 함수를 사용하면 관리자가 주어진 `basketToken`의 스폰서를 변경할 수 있습니다. 그러나 수집된 `claimableSponsorFees`를 이전 스폰서에게 전송하지 않으므로 새 스폰서가 이전 스폰서가 수집한 수수료를 청구할 수 있습니다. `setSponsor`가 호출될 때 금액이 0보다 큰 경우 `claimableSponsorFees`를 이전 스폰서에게 전송하는 것을 고려하십시오.

# [L-16] `_processExternalTrades()`의 정밀도 손실

```solidity
function _processExternalTrades(
        BasketManagerStorage storage self,
        ExternalTrade[] calldata externalTrades
    )
        private
    {
        uint256 externalTradesLength = externalTrades.length;
        uint256[2][] memory claimedAmounts = _completeTokenSwap(self, externalTrades);
        // Update basketBalanceOf with amounts gained from swaps
        for (uint256 i = 0; i < externalTradesLength;) {
            ExternalTrade calldata trade = externalTrades[i];
            // nosemgrep: solidity.performance.array-length-outside-loop.array-length-outside-loop
            uint256 tradeOwnershipLength = trade.basketTradeOwnership.length;
            for (uint256 j; j < tradeOwnershipLength;) {
                BasketTradeOwnership calldata ownership = trade.basketTradeOwnership[j];
                address basket = ownership.basket;
                // Account for bought tokens
                self.basketBalanceOf[basket][trade.buyToken] +=
@>                    FixedPointMathLib.fullMulDiv(claimedAmounts[i][1], ownership.tradeOwnership, _WEIGHT_PRECISION);
                // Account for sold tokens
                self.basketBalanceOf[basket][trade.sellToken] = self.basketBalanceOf[basket][trade.sellToken]
@>                   + FixedPointMathLib.fullMulDiv(claimedAmounts[i][0], ownership.tradeOwnership, _WEIGHT_PRECISION)
@>                    - FixedPointMathLib.fullMulDiv(trade.sellAmount, ownership.tradeOwnership, _WEIGHT_PRECISION);
                unchecked {
                    // Overflow not possible: i is less than tradeOwnerShipLength.length
                    ++j;
                }
            }
            unchecked {
                // Overflow not possible: i is less than externalTradesLength.length
                ++i;
            }
        }
    }
```

구매한 토큰을 예로 들면:

tradeOwnershipLength가 2이고, claimedAmounts[i][1]이 111이고, basket0의 tradeOwnership이 0.3e18이고, basket1의 tradeOwnership이 0.7e18이면 계산에 FixedPointMathLib.fullMulDiv()를 사용하면 basket0은 33 buyToken을 받고 basket1은 77 buyToken을 받습니다. 그러나 33 + 77 = 110 < 111입니다.

동일한 문제가 판매된 토큰에서도 발생할 수 있습니다.
