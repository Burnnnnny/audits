# Pashov Audit Group 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **GainsNetwork-org/gTrade-contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Gains Network 정보 (About Gains Network)

Gains Network는 유동성 효율적인 탈중앙화 레버리지 거래 플랫폼입니다. 거래 쌍에 관계없이 DAI, USDC, 또는 WETH 담보로 거래를 엽니다. 레버리지는 합성이며 각 gToken 금고와 GNS 토큰으로 뒷받침됩니다. 트레이더의 수익은 금고에서 가져와 PnL을 지불하며(양수인 경우), PnL이 음수인 경우 거래 손실을 금고가 받습니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:** [59a031048323276bfbec6c8751ae7c0ee81df5ab](https://github.com/GainsNetwork-org/gTrade-contracts/tree/59a031048323276bfbec6c8751ae7c0ee81df5ab)

**수정 검토 커밋 해시:** [60cd9dde908940d1acb0b1f67803269b7e21522a](https://github.com/GainsNetwork-org/gTrade-contracts/tree/60cd9dde908940d1acb0b1f67803269b7e21522a)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `GNSPairsStorage`
- `GNSPriceAggregator`
- `GNSPriceImpact`
- `GNSTradingStorage`
- `BorrowingFeesUtils`
- `ConstantsUtils`
- `PairsStorageUtils`
- `PriceAggregatorUtils`
- `PriceImpactUtils`
- `TradingCallbackUtils`
- `TradingCommonUtils`
- `TradingInteractionUtils`
- `TradingStorageUtils`
- `UpdateLeverageLifecycles`
- `DecreasePositionSizeUtils`
- `IncreasePositionSizeUtils`
- `UpdatePositionSizeLifecycles`

# 발견 사항 (Findings)

# [H-01] 일관성 없는 스프레드 및 가격 영향 부과 (Inconsistent spread and price impact charges)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

이 프로토콜은 v9.2 이전과 이후에 개설된 거래에 대해 서로 다른 스프레드 및 가격 영향 부과 메커니즘을 구현합니다. 그러나 이러한 메커니즘의 불일치로 인해 사용자는 포지션을 수정할 때 전체 스프레드 및 가격 영향 부과를 피하기 위해 v9.2 이전 거래를 악용할 수 있습니다.

포지션 크기를 늘릴 때 스프레드 및 가격 영향 비율은 거래가 v9.2 이전 또는 이후에 개설되었는지 여부에 관계없이 50%가 부과됩니다.

```solidity
    function prepareCallbackValues(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal view returns (IUpdatePositionSizeUtils.IncreasePositionSizeValues memory values) {
        ...
        // 3. Calculate price impact values
        (, values.priceAfterImpact) = _getMultiCollatDiamond().getTradePriceImpact(
            TradingCommonUtils.getMarketExecutionPrice(_answer.price, _answer.spreadP, _existingTrade.long, true), // @audit charge half of spread and price impact regardless of trade opened before or after v9.2
            _existingTrade.pairIndex,
            _existingTrade.long,
            _getMultiCollatDiamond().getUsdNormalizedValue(
                _existingTrade.collateralIndex,
                values.positionSizeCollateralDelta
            ),
            false,
            true,
            0
        );
        ...
    }
```

그러나 포지션 크기를 줄이거나 거래를 닫을 때 v9.2 이전에 개설된 거래인 경우 스프레드나 가격 영향이 부과되지 않습니다.

```solidity
function getTradeClosingPriceImpact(
        ITradingCommonUtils.TradePriceImpactInput memory _input
    ) external view returns (uint256 priceImpactP, uint256 priceAfterImpact, uint256 tradeValueCollateralNoFactor) {
        ITradingStorage.Trade memory trade = _input.trade;

        // 0. If trade opened before v9.2, return market price (no closing spread or price impact)
        if (_getMultiCollatDiamond().getTradeLiquidationParams(trade.user, trade.index).maxLiqSpreadP == 0) {
            return (0, _input.marketPrice, 0);
        }
        ...
    }
```

이러한 불일치는 다음과 같은 악용을 초래합니다.

- v9.2 이전의 미결제 거래를 보유한 사용자는 포지션 크기를 늘릴 수 있으며 스프레드 및 가격 영향의 50%만 지불합니다.
- 그런 다음 포지션 크기를 줄이거나 거래를 닫을 때 스프레드나 가격 영향 부과 없이 진행할 수 있습니다.

## 권장 사항

포지션 크기를 늘릴 때 거래가 v9.2 이전에 개설된 경우 100% 스프레드 및 가격 영향 비율을 부과하십시오.

# [M-01] 거래 청산 시 슬리피지 보호 부족 (Lack of slippage protection on trade closures)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

거래를 닫거나 포지션 크기를 줄일 때 실행 가격은 두 가지 주요 요인으로 인해 예상과 크게 다를 수 있습니다.

- 관리자에 의한 스프레드 비율 업데이트.
- 가격 영향 비율 변동.

사용되는 `spreadP` 및 계산된 `priceImpactP`는 거래 청산이 요청된 시점과 콜백에서 실제로 실행되는 시점 사이에 변경되었을 수 있습니다. 그러나 현재 이러한 변동으로 인해 예상치 못한 불리한 가격으로 거래가 종료되는 것을 방지하기 위한 슬리피지 보호 메커니즘이 없습니다.

이 문제는 `decreasePositionSize` 함수에도 `closeTradeMarketCallback`과 유사한 방식으로 영향을 미칩니다.

```solidity
    function closeTradeMarketCallback(
        ITradingCallbacks.AggregatorAnswer memory _a
    ) internal tradingActivatedOrCloseOnly {
        ...
        (uint256 priceImpactP, uint256 priceAfterImpact, ) = TradingCommonUtils.getTradeClosingPriceImpact( // @audit price impact can fluctuate due to open interest and depth
            ITradingCommonUtils.TradePriceImpactInput(
                t,
                _a.price,
                _a.spreadP, // @audit spreadP can be updated between closeTrade and closeTradeMarketCallback
                TradingCommonUtils.getPositionSizeCollateral(t.collateralAmount, t.leverage)
            )
        );
        ...
    }
```

## 권장 사항

- 사용자가 거래 종료를 요청할 때 최대 허용 슬리피지를 지정할 수 있도록 허용하십시오.

# [M-02] `getLiqPnlThresholdP`가 되돌려질(revert) 수 있음 (`getLiqPnlThresholdP` could revert)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (low)

## 설명

청산 매개변수가 구성될 때 `startLeverage`와 `endLeverage`가 동일한 값을 가질 수 있습니다. 이는 레버리지에 관계없이 일정한 청산 임계값을 원하는 자산 그룹에 유용할 수 있습니다.

그러나 `getLiqPnlThresholdP`가 호출되고 제공된 `_leverage`가 `startLeverage` / `endLeverage`와 같을 때, `startLeverage`가 `endLeverage`와 같으면 0으로 나누기로 인해 되돌려집니다.

```solidity
    function getLiqPnlThresholdP(
        IPairsStorage.GroupLiquidationParams memory _params,
        uint256 _leverage
    ) public pure returns (uint256) {
        // For trades opened before v9.2, use legacy liquidation threshold
        // LEGACY_LIQ_THRESHOLD_P = 90 * P_10; // -90% pnl
        if (_params.maxLiqSpreadP == 0) return ConstantsUtils.LEGACY_LIQ_THRESHOLD_P;

        if (_leverage < _params.startLeverage) return _params.startLiqThresholdP;
        if (_leverage > _params.endLeverage) return _params.endLiqThresholdP;

        return
            _params.startLiqThresholdP -
            ((_leverage - _params.startLeverage) * (_params.startLiqThresholdP - _params.endLiqThresholdP)) /
>>>         (_params.endLeverage - _params.startLeverage);
    }
```

## 권장 사항

`_leverage`가 `_params.startLeverage` / `_params.endLeverage`와 같을 때 `_params.startLiqThresholdP` / `_params.endLiqThresholdP`를 반환하는 것을 고려하십시오.

```diff
    function getLiqPnlThresholdP(
        IPairsStorage.GroupLiquidationParams memory _params,
        uint256 _leverage
    ) public pure returns (uint256) {
        // For trades opened before v9.2, use legacy liquidation threshold
        // LEGACY_LIQ_THRESHOLD_P = 90 * P_10; // -90% pnl
        if (_params.maxLiqSpreadP == 0) return ConstantsUtils.LEGACY_LIQ_THRESHOLD_P;

-        if (_leverage < _params.startLeverage) return _params.startLiqThresholdP;
+        if (_leverage <= _params.startLeverage) return _params.startLiqThresholdP;
-        if (_leverage > _params.endLeverage) return _params.endLiqThresholdP;
+        if (_leverage >= _params.endLeverage) return _params.endLiqThresholdP;

        return
            _params.startLiqThresholdP -
            ((_leverage - _params.startLeverage) * (_params.startLiqThresholdP - _params.endLiqThresholdP)) /
            (_params.endLeverage - _params.startLeverage);
    }
```

권장 사항 설명

# [M-03] 거래 종료 시 잘못된 거래 가격 영향 계산 (Wrong trade price impact calculation when closing trades)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

거래 종료에 대한 가격 영향을 계산할 때 `_getTradePriceImpact` 함수는 기존 미결제 약정(open interest)에서 거래의 미결제 약정을 빼는 대신 더합니다. 이는 포지션을 닫는 것이 포지션을 여는 것과 동일한 방향으로 시장 영향을 증가시킨다고 잘못 가정하므로 거래를 닫을 때 가격 영향을 과대평가하게 합니다.

```solidity
    function _getTradePriceImpact(
        uint256 _marketPrice,
        bool _long,
        uint256 _startOpenInterestUsd,
        uint256 _tradeOpenInterestUsd,
        uint256 _onePercentDepthUsd,
        bool _open,
        uint256 _protectionCloseFactor
    )
        internal
        pure
        returns (
            uint256 priceImpactP, // 1e10 (%)
            uint256 priceAfterImpact // 1e10
        )
    {
        if (_onePercentDepthUsd == 0) {
            return (0, _marketPrice);
        }
        priceImpactP =
            ((_startOpenInterestUsd + _tradeOpenInterestUsd / 2) * _protectionCloseFactor) / // @audit when closing still add _tradeOpenInterestUsd instead of minus
            _onePercentDepthUsd /
            1e18 /
            2;
        ...
    }
```

## 권장 사항

- 거래를 닫을 때 `startOpenInterestUsd`에서 `tradeOpenInterestUsd`를 빼야 합니다.
  priceImpactP = ((\_open ? \_startOpenInterestUsd + \_tradeOpenInterestUsd / 2
  : \_startOpenInterestUsd - \_tradeOpenInterestUsd / 2) \* \_protectionCloseFactor) /
  \_onePercentDepthUsd /
  1e18 /
  2;

# [M-04] 트레이더가 새로운 청산 구성을 피할 수 있음 (Traders can avoid the new liquidation configuration)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

새로운 업데이트는 청산 임계값이 이제 구성 가능하고 레버리지에 의존하는 그룹 청산 매개변수를 도입합니다. 레버리지가 높을수록 청산 임계값이 낮아져 포지션이 가격 변동에 더 민감해지고 청산되기 쉽습니다.

```solidity
    function getLiqPnlThresholdP(
        IPairsStorage.GroupLiquidationParams memory _params,
        uint256 _leverage
    ) public pure returns (uint256) {
        // For trades opened before v9.2, use legacy liquidation threshold
        // LEGACY_LIQ_THRESHOLD_P = 90 * P_10; // -90% pnl
>>>     if (_params.maxLiqSpreadP == 0) return ConstantsUtils.LEGACY_LIQ_THRESHOLD_P;
        if (_leverage < _params.startLeverage) return _params.startLiqThresholdP;
        if (_leverage > _params.endLeverage) return _params.endLiqThresholdP;

        return
            _params.startLiqThresholdP -
            ((_leverage - _params.startLeverage) * (_params.startLiqThresholdP - _params.endLiqThresholdP)) /
            (_params.endLeverage - _params.startLeverage);
    }
```

사용자가 포지션 크기나 레버리지를 업데이트할 때 항상 초기 청산 매개변수를 사용합니다. 즉, 주문이 업데이트 이전에 생성된 경우 항상 기존 청산 임계값을 사용합니다.

```solidity
   // update leverage
    function _prepareCallbackValues(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _pendingTrade,
        bool _isIncrease
    ) internal view returns (IUpdateLeverageUtils.UpdateLeverageValues memory values) {
        if (_existingTrade.isOpen == false) return values;

        values.newLeverage = _pendingTrade.leverage;
        values.govFeeCollateral = TradingCommonUtils.getGovFeeCollateral(
            _existingTrade.user,
            _existingTrade.pairIndex,
            TradingCommonUtils.getMinPositionSizeCollateral(_existingTrade.collateralIndex, _existingTrade.pairIndex) /
                2 // use min fee / 2
        );
        values.newCollateralAmount =
            (
                _isIncrease
                    ? _existingTrade.collateralAmount - _pendingTrade.collateralAmount
                    : _existingTrade.collateralAmount + _pendingTrade.collateralAmount
            ) -
            values.govFeeCollateral;
>>>     values.liqPrice = _getMultiCollatDiamond().getTradeLiquidationPrice(
            IBorrowingFees.LiqPriceInput(
                _existingTrade.collateralIndex,
                _existingTrade.user,
                _existingTrade.pairIndex,
                _existingTrade.index,
                _existingTrade.openPrice,
                _existingTrade.long,
                _isIncrease ? values.newCollateralAmount : _existingTrade.collateralAmount,
                _isIncrease ? values.newLeverage : _existingTrade.leverage,
                true
            )
        ); // for increase leverage we calculate new trade liquidation price and for decrease leverage we calculate existing trade liquidation price
    }
```

```solidity
// increase position size
    function prepareCallbackValues(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal view returns (IUpdatePositionSizeUtils.IncreasePositionSizeValues memory values) {
        // ...
        values.newOpenPrice =
            (positionSizePlusPnlCollateral *
                uint256(_existingTrade.openPrice) +
                values.positionSizeCollateralDelta *
                values.priceAfterImpact) /
            (positionSizePlusPnlCollateral + values.positionSizeCollateralDelta);

        // 8. Calculate existing and new liq price
        values.existingLiqPrice = TradingCommonUtils.getTradeLiquidationPrice(_existingTrade, true);
>>>     values.newLiqPrice = _getMultiCollatDiamond().getTradeLiquidationPrice(
            IBorrowingFees.LiqPriceInput(
                _existingTrade.collateralIndex,
                _existingTrade.user,
                _existingTrade.pairIndex,
                _existingTrade.index,
                uint64(values.newOpenPrice),
                _existingTrade.long,
                values.newCollateralAmount,
                values.newLeverage,
                false
            )
        );
    }
```

트레이더는 업데이트 전에 작고 위험이 낮은 거래를 생성한 다음 나중에 포지션 크기나 레버리지를 조정하여 이를 악용할 수 있습니다. 이렇게 하면 레버리지에 의존하는 새로운 청산 임계값 계산을 피하고 이전에 고정된 기존 임계값을 사용할 수 있으며, 이는 그들에게 더 바람직합니다.

## 권장 사항

사용자가 거래를 업데이트하고 새로운 청산 가격을 계산할 때 거래가 기존 거래인 경우 거래를 업데이트하고 청산 구성을 적용하십시오.

# [L-01] 사용자가 손절매 설정 시 손실 수준을 제어할 수 없음 (Users cannot control their loss level when setting stop loss)

사용자가 손절매를 설정할 때 포지션이 지정된 가격 수준 또는 그 근처에서 청산되기를 기대합니다. 그러나 현재 구현에서는 실행 가격이 사용자가 지정한 손절매 수준보다 훨씬 나쁠 수 있습니다. 이는 스프레드와 가격 영향을 모두 포함하는 `priceAfterImpact` 계산이 손절매가 트리거된 후에 적용되기 때문입니다.

사용자가 거래 종료 스프레드 및 가격 영향 계산 후의 가격으로 손절매를 설정할 수 있도록 허용하는 것이 좋습니다.

```solidity
    function executeTriggerCloseOrderCallback(
        ITradingCallbacks.AggregatorAnswer memory _a
    ) internal tradingActivatedOrCloseOnly {
        ...
            if (o.orderType != ITradingStorage.PendingOrderType.LIQ_CLOSE) {
                (, uint256 priceAfterImpact, ) = TradingCommonUtils.getTradeClosingPriceImpact( // @audit execution price can be unfavourable than expected when setting SL
                    ITradingCommonUtils.TradePriceImpactInput(
                        t,
                        v.executionPrice,
                        _a.spreadP,
                        TradingCommonUtils.getPositionSizeCollateral(t.collateralAmount, t.leverage)
                    )
                );
                v.executionPrice = priceAfterImpact;
            }
        ...
    }
```
