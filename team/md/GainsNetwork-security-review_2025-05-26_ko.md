# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **GainsNetwork-org/gTrade-contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Gains Network 정보 (About Gains Network)

Gains Network는 유동성 효율적인 탈중앙화 레버리지 거래 플랫폼입니다. 거래 쌍에 관계없이 DAI, USDC 또는 WETH 담보로 거래를 엽니다. 레버리지는 합성이며 각 gToken 금고와 GNS 토큰으로 뒷받침됩니다. 트레이더의 수익은 금고에서 가져와 PnL을 지불하며(양수인 경우), PnL이 음수인 경우 거래 손실을 금고가 받습니다.

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

_검토 커밋 해시_ - [c9bc0b058b0bd336ff99ce31d50d152b32a32d43](https://github.com/GainsNetwork-org/gTrade-contracts/tree/c9bc0b058b0bd336ff99ce31d50d152b32a32d43)

_수정 검토 커밋 해시_ - [6af8860727d71f5f44c815c1f34ca0f64480ce54](https://github.com/GainsNetwork-org/gTrade-contracts/tree/6af8860727d71f5f44c815c1f34ca0f64480ce54)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `GToken`
- `GNSAddressStore`
- `GNSDiamondCut`
- `GNSDiamondStorage`
- `GNSBorrowingFees`
- `GNSFundingFees`
- `GNSPairsStorage`
- `GNSPriceAggregator`
- `GNSPriceImpact`
- `GNSTradingCallbacks`
- `GNSTradingInteractions`
- `GNSTradingStorage`
- `interfaces/`
- `BorrowingFeesUtils`
- `ConstantsUtils`
- `FundingFeesUtils`
- `PackingUtils`
- `PairsStorageUtils`
- `PriceAggregatorUtils`
- `PriceImpactUtils`
- `StorageUtils`
- `TradeManagementCallbacksUtils`
- `TradingCallbacksUtils`
- `TradingCommonUtils`
- `TradingInteractionsUtils`
- `TradingStorageGetters`
- `UpdateLeverageLifecycles`
- `DecreasePositionSizeUtils`
- `IncreasePositionSizeUtils`
- `UpdatePositionSizeLifecycles`
- `Timelock`

# 발견 사항 (Findings)

# [C-01] 포지션 감소를 악용하여 PnL을 이중으로 인출할 수 있음 (Decrease position can be abused to withdraw PnL twice)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

사용자가 포지션을 줄일 때 결국 `executeDecreasePositionSizeMarket`이 트리거됩니다.

```solidity
    function executeDecreasePositionSizeMarket(
        ITradingStorage.PendingOrder memory _order,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) external {
        // 1. Prepare vars
        ITradingStorage.Trade memory partialTrade = _order.trade;
        ITradingStorage.Trade memory existingTrade = _getMultiCollatDiamond().getTrade(
            partialTrade.user,
            partialTrade.index
        );
        IUpdatePositionSizeUtils.DecreasePositionSizeValues memory values;

        // 2. Refresh trader fee tier cache
        TradingCommonUtils.updateFeeTierPoints(
            existingTrade.collateralIndex,
            existingTrade.user,
            existingTrade.pairIndex,
            0
        );

        // 3. Base validation (trade open, market open)
        ITradingCallbacks.CancelReason cancelReason = _validateBaseFulfillment(existingTrade);

        // 4. If passes base validation, validate further
        if (cancelReason == ITradingCallbacks.CancelReason.NONE) {
            // 4.1 Prepare useful values (position size delta, closing fees, borrowing fees, etc.)
>>>         values = DecreasePositionSizeUtils.prepareCallbackValues(existingTrade, partialTrade, _answer);

            // 4.2 Further validation
            cancelReason = DecreasePositionSizeUtils.validateCallback(existingTrade, _order, values, _answer);

            // 5. If passes further validation, execute callback
            if (cancelReason == ITradingCallbacks.CancelReason.NONE) {
                // 5.1 Send collateral delta (partial trade value - fees) if positive or remove from trade collateral if negative
                // Then update trade collateral / leverage in storage, and reset trade borrowing fees
                DecreasePositionSizeUtils.updateTradeSuccess(existingTrade, values, _answer);

                // 5.2 Distribute closing fees
                TradingCommonUtils.processFees(existingTrade, values.positionSizeCollateralDelta, _order.orderType);
            }
        }

        // 6. If didn't pass validation and trade exists, charge gov fee and remove corresponding OI
        if (cancelReason != ITradingCallbacks.CancelReason.NONE)
            DecreasePositionSizeUtils.handleCanceled(existingTrade, cancelReason, _answer);

        // 7. Close pending decrease position size order
        _getMultiCollatDiamond().closePendingOrder(_answer.orderId);

        emit IUpdatePositionSizeUtils.PositionSizeDecreaseExecuted(
            _answer.orderId,
            cancelReason,
            existingTrade.collateralIndex,
            existingTrade.user,
            existingTrade.pairIndex,
            existingTrade.index,
            existingTrade.long,
            _answer.current,
            _getMultiCollatDiamond().getCollateralPriceUsd(existingTrade.collateralIndex),
            partialTrade.collateralAmount,
            partialTrade.leverage,
            values
        );
    }
```

`executeDecreasePositionSizeMarket` 내부에서는 `DecreasePositionSizeUtils.prepareCallbackValues` 내부에서 거래를 업데이트하는 데 사용될 모든 값을 계산합니다.

```solidity
    function prepareCallbackValues(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal view returns (IUpdatePositionSizeUtils.DecreasePositionSizeValues memory values) {
        // 1. Calculate position size delta and existing position size
        bool isLeverageUpdate = _partialTrade.leverage > 0;
        values.positionSizeCollateralDelta = TradingCommonUtils.getPositionSizeCollateral(
            isLeverageUpdate ? _existingTrade.collateralAmount : _partialTrade.collateralAmount,
            isLeverageUpdate ? _partialTrade.leverage : _existingTrade.leverage
        );
        values.existingPositionSizeCollateral = TradingCommonUtils.getPositionSizeCollateral(
            _existingTrade.collateralAmount,
            _existingTrade.leverage
        );

        // 2. Calculate partial trade closing fees
        values.closingFeeCollateral = TradingCommonUtils.getTotalTradeFeesCollateral(
            _existingTrade.collateralIndex,
            _existingTrade.user,
            _existingTrade.pairIndex,
            values.positionSizeCollateralDelta,
            _existingTrade.isCounterTrade
        );

        // 3. Calculate new collateral amount and leverage
        values.newCollateralAmount = _existingTrade.collateralAmount - _partialTrade.collateralAmount;
        values.newLeverage = _existingTrade.leverage - _partialTrade.leverage;

        // 4. Apply spread and price impact to answer.current
        (values.priceImpact, ) = TradingCommonUtils.getTradeClosingPriceImpact(
            ITradingCommonUtils.TradePriceImpactInput(
                _existingTrade,
                _answer.current,
                values.positionSizeCollateralDelta,
                _answer.current
            )
        );

        // 5. Calculate existing trade pnl
>>>     values.existingPnlCollateral =
            (TradingCommonUtils.getPnlPercent(
                _existingTrade.openPrice,
                uint64(values.priceImpact.priceAfterImpact),
                _existingTrade.long,
                _existingTrade.leverage
            ) * int256(uint256(_existingTrade.collateralAmount))) /
            100 /
            int256(ConstantsUtils.P_10);

        // 6. Calculate value sent to trader
>>>     int256 partialTradePnlCollateral = (values.existingPnlCollateral * int256(values.positionSizeCollateralDelta)) /
            int256(values.existingPositionSizeCollateral);

        values.availableCollateralInDiamond = TradingCommonUtils.getTradeAvailableCollateralInDiamond(_existingTrade);
        values.availableCollateralInDiamond = _partialTrade.collateralAmount > values.availableCollateralInDiamond
            ? values.availableCollateralInDiamond
            : _partialTrade.collateralAmount;
        // @audit - should this consider pnl withdrawal
>>>     values.collateralSentToTrader = int256(uint256(_partialTrade.collateralAmount)) + partialTradePnlCollateral;

        // 7. Calculate existing and new trade liquidation price
        values.existingLiqPrice = TradingCommonUtils.getTradeLiquidationPrice(_existingTrade, _answer.current);
        values.newLiqPrice = TradingCommonUtils.getTradeLiquidationPrice(
            _existingTrade,
            _existingTrade.openPrice,
            values.newCollateralAmount,
            values.newLeverage,
            values.closingFeeCollateral +
                uint256(values.collateralSentToTrader < 0 ? -values.collateralSentToTrader : int256(0)),
            _getMultiCollatDiamond().getTradeLiquidationParams(_existingTrade.user, _existingTrade.index),
            _answer.current
        );
    }
```

`existingPnlCollateral`을 계산할 때 `_existingTrade.collateralAmount`를 사용합니다. 그런 다음 `partialTradePnlCollateral`을 계산할 때 `values.positionSizeCollateralDelta` 및 `values.existingPositionSizeCollateral`을 기반으로 `existingPnlCollateral`의 일부를 사용합니다. 그러나 이러한 계산 중 어느 것도 인출되었거나 실현된 PnL을 고려하지 않습니다.

이로 인해 이전에 이미 실현된 PnL을 사용자가 다시 인출하게 됩니다.

## 권장 사항

`values.existingPnlCollateral`을 계산할 때 `realizedPnlCollateral`을 고려하십시오.

# [H-01] 담보가 수수료보다 적을 때 보유 수수료 회계 불일치 (Holding fees accounting discrepancy when collateral is less than fees)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

고 레버리지 거래가 시간이 지남에 따라 양수 PnL을 축적할 때 보유 수수료 처리 방식에 회계 불일치가 발생하여 프로토콜에 잠재적인 자금 손실이 발생할 수 있습니다.

이 문제는 `holdingFeesCollateral`이 `availableCollateralInDiamond`를 초과할 때 `realizeHoldingFeesOnOpenTrade` 함수에서 발생합니다. 이 시나리오에서는 사용 가능한 담보만 금고로 보내지지만 전체 금액이 `realizedTradingFeesCollateral`에 기록됩니다.

```solidity
    function realizeHoldingFeesOnOpenTrade(address _trader, uint32 _index, uint64 _currentPairPrice) external {
        ...

        if (holdingFees.totalFeeCollateral > 0) {
            ...

            if (availableCollateralInDiamond > 0) {
                amountSentToVaultCollateral = holdingFeesCollateral > availableCollateralInDiamond
                    ? availableCollateralInDiamond
                    : holdingFeesCollateral;

                TradingCommonUtils.transferFeeToVault(trade.collateralIndex, amountSentToVaultCollateral, trade.user);
            }

            uint128 newRealizedTradingFeesCollateral = tradeFeesData.realizedTradingFeesCollateral +
                uint128(holdingFeesCollateral); // @audit Full amount recorded here
            tradeFeesData.realizedTradingFeesCollateral = newRealizedTradingFeesCollateral;
            ...
    }
```

불일치는 다음과 같은 경우에 발생합니다.

- 거래에 사용 가능한 담보를 초과하는 보유 수수료가 누적되었습니다.
- 이 수수료 중 일부만 실제로 금고로 이전됩니다.
- 전체 금액은 "실현된 수수료(realized fees)"로 기록됩니다.
- 트레이더는 나중에 포지션에 더 많은 담보를 추가합니다.

이로 인해 프로토콜이 실제로 가진 것보다 더 많은 수수료를 징수한 기록이 생겨 사용 가능한 담보를 계산할 때 잘못된 회계가 발생합니다.

```solidity
    function getTradeAvailableCollateralInDiamond(ITradingStorage.Trade memory _trade) external view returns (uint256) {
        ...
        int256 availableCollateralInDiamondRaw = int256(uint256(_trade.collateralAmount)) -
            int256(realizedTradingFeesCollateral) -
            int256(manuallyRealizedNegativePnlCollateral);

        return availableCollateralInDiamondRaw > 0 ? uint256(availableCollateralInDiamondRaw) : 0;
    }
```

다음 시나리오를 고려하십시오.

- 트레이더가 500배 레버리지로 0.6 ETH 담보로 포지션을 엽니다.
- 포지션이 수익을 내어 0.82 ETH의 보유 수수료가 누적됩니다.
- 수수료가 실현되지만 0.6 ETH만 금고로 전송되고 0.82 ETH가 실현된 것으로 기록됩니다.
- 트레이더는 0.5 ETH 담보를 더 추가합니다 (총 1.1 ETH).
- 포지션 종료 시 사용 가능한 담보는 다음과 같이 계산됩니다: 1.1 ETH - 0.82 ETH = 0.28 ETH.
- 실제 사용 가능해야 하는 금액: 0.5 ETH (원래 0.6 ETH는 이미 금고로 보내졌으므로 새 담보만 해당).
- 금고는 이 시나리오에서 사실상 0.22 ETH를 잃고 있습니다.

## 권장 사항

거래를 종료할 때 담보가 충분하면 전송되지 않은 수수료를 확인하고 보류 중인 수수료를 금고로 보내십시오.

# [H-02] `GetTradeSkewPriceImpactP` 함수가 거래 계약 버전을 무시함 (`GetTradeSkewPriceImpactP` function ignores trade contract version)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`PriceImpactUtils.getTradeSkewPriceImpactP`가 거래 계약 버전을 고려하지 않습니다. 이로 인해 모든 계약 버전에 동일한 스큐 가격 영향(skew price impact)이 적용됩니다.
따라서 거래가 열릴 때 스큐 가격 영향이 적용되었는지 여부와 관계없이 종료 시 가격 영향의 절반이 적용됩니다.

```solidity
    function getTradeSkewPriceImpactP(
        uint8 _collateralIndex,
        uint16 _pairIndex,
        bool _long,
        uint256 _tradeOpenInterestUsd, // 1e18 USD
        bool _open
    )
        internal
        view
        returns (
            int256 priceImpactP // 1e10 (%)
        )
    {
        IPriceImpact.PriceImpactValues memory v;

        v.tradePositiveSkew = (_long && _open) || (!_long && !_open);
        v.depth = _getStorage().pairSkewDepths[_collateralIndex][_pairIndex];
        v.tradeSkewMultiplier = v.tradePositiveSkew ? int256(1) : int256(-1);
>>      v.priceImpactDivider = int256(2); // so depth is on same scale as cumul vol price impact

        return
            _getTradePriceImpactP(
                TradingCommonUtils.getPairOiSkewCollateral(_collateralIndex, _pairIndex) *
                    int256(_getMultiCollatDiamond().getCollateralPriceUsd(_collateralIndex)),
                int256(_tradeOpenInterestUsd) * v.tradeSkewMultiplier,
                v.depth,
                ConstantsUtils.P_10,
                ConstantsUtils.P_10
>>          ) / v.priceImpactDivider;
    }
```

## 권장 사항

업데이트 전에 열린 거래, 즉 계약 버전 < 10인 거래를 종료할 때 스큐 가격 영향을 전혀 적용하지 않고 해당 거래에 대한 포지션을 늘릴 때 스큐 가격 영향의 전체 크기를 적용하는 것을 고려하십시오.

# [H-03] 포지션 감소의 `updateTradeSuccess`가 잘못된 `availableCollateralInDiamond`를 사용함 (Decrease position's `updateTradeSuccess` will use wrong `availableCollateralInDiamond`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

포지션 감소의 `updateTradeSuccess` 내부에서 종료 수수료가 사용자로부터 차감됩니다. 그러나 `handleTradeValueTransfer` 작업에 `_values.availableCollateralInDiamond`를 제공할 때 이는 고려되지 않습니다.

```solidity
    function updateTradeSuccess(
        ITradingStorage.Trade memory _existingTrade,
        IUpdatePositionSizeUtils.DecreasePositionSizeValues memory _values,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal {
        // 1. Update trade in storage
        _getMultiCollatDiamond().updateTradePosition(
            ITradingStorage.Id(_existingTrade.user, _existingTrade.index),
            _values.newCollateralAmount,
            _values.newLeverage,
            _existingTrade.openPrice, // open price stays the same
            ITradingStorage.PendingOrderType.MARKET_PARTIAL_CLOSE, // don't refresh liquidation params
            _values.existingPnlCollateral > 0,
            _answer.current
        );
        // 2. Realize trading fees and negative pnl (leverage decrease)
>>>     _getMultiCollatDiamond().realizeTradingFeesOnOpenTrade(
            _existingTrade.user,
            _existingTrade.index,
            _values.closingFeeCollateral +
                (_values.collateralSentToTrader < 0 ? uint256(-_values.collateralSentToTrader) : 0), // reduces collateral available in diamond
            _answer.current
        );

        // 3. Handle collateral/pnl transfers
        TradingCommonUtils.handleTradeValueTransfer(
            _existingTrade,
            _values.collateralSentToTrader,
>>>         int256(_values.availableCollateralInDiamond)
        );
    }
```

이로 인해 `handleTradeValueTransfer`가 금고에서 가져와야 하는 잘못된 담보 금액을 처리하게 됩니다.

## 권장 사항

`handleTradeValueTransfer`에 `availableCollateralInDiamond`를 제공할 때 `closingFeeCollateral`을 포함하는 것을 고려하십시오.

```diff
// ...
        // 3. Handle collateral/pnl transfers
        TradingCommonUtils.handleTradeValueTransfer(
            _existingTrade,
            _values.collateralSentToTrader,
-            int256(_values.availableCollateralInDiamond)
+            int256(_values.availableCollateralInDiamond - _values.closingFeeCollateral)
        );
// ...
```

# [H-04] 카운터 거래(counter trade) 포지션 크기를 늘릴 때 트레이더가 자산을 잃을 수 있음 (Traders could lose assets when increasing counter trade position size)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

사용자가 포지션 크기 증가를 요청하면 결국 실행되어 `executeIncreasePositionSizeMarket`이 트리거됩니다.

```solidity
    function executeIncreasePositionSizeMarket(
        ITradingStorage.PendingOrder memory _order,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) external {
        // 1. Prepare vars
        ITradingStorage.Trade memory partialTrade = _order.trade;
        ITradingStorage.Trade memory existingTrade = _getMultiCollatDiamond().getTrade(
            partialTrade.user,
            partialTrade.index
        );
        IUpdatePositionSizeUtils.IncreasePositionSizeValues memory values;
        // 2. Refresh trader fee tier cache
        TradingCommonUtils.updateFeeTierPoints(
            existingTrade.collateralIndex,
            existingTrade.user,
            existingTrade.pairIndex,
            0
        );

        // 3. Base validation (trade open, market open)
        ITradingCallbacks.CancelReason cancelReason = _validateBaseFulfillment(existingTrade);

        // 4. If passes base validation, validate further
        if (cancelReason == ITradingCallbacks.CancelReason.NONE) {
            // 4.1 Prepare useful values (position size delta, pnl, fees, new open price, etc.)
>>>         values = IncreasePositionSizeUtils.prepareCallbackValues(existingTrade, partialTrade, _answer);

            // 4.2 Further validation
>>>         cancelReason = IncreasePositionSizeUtils.validateCallback(
                existingTrade,
                values,
                _answer,
                partialTrade.openPrice,
                _order.maxSlippageP
            );

            // 5. If passes further validation, execute callback
            if (cancelReason == ITradingCallbacks.CancelReason.NONE) {
                // 5.1 Update trade collateral / leverage / open price in storage, and reset trade borrowing fees
                IncreasePositionSizeUtils.updateTradeSuccess(_answer.orderId, existingTrade, values, _answer);
                // 5.2 Distribute opening fees and store fee tier points for position size delta
                TradingCommonUtils.processFees(existingTrade, values.positionSizeCollateralDelta, _order.orderType);
            }
        }

        // 6. If didn't pass validation, charge gov fee (if trade exists) and return partial collateral (if any)
        if (cancelReason != ITradingCallbacks.CancelReason.NONE)
>>>         IncreasePositionSizeUtils.handleCanceled(existingTrade, partialTrade, cancelReason, _answer);

        // 7. Close pending increase position size order
        _getMultiCollatDiamond().closePendingOrder(_answer.orderId);

        emit IUpdatePositionSizeUtils.PositionSizeIncreaseExecuted(
            _answer.orderId,
            cancelReason,
            existingTrade.collateralIndex,
            existingTrade.user,
            existingTrade.pairIndex,
            existingTrade.index,
            existingTrade.long,
            _answer.current,
            _getMultiCollatDiamond().getCollateralPriceUsd(existingTrade.collateralIndex),
            partialTrade.collateralAmount,
            partialTrade.leverage,
            values
        );
    }
```

`executeIncreasePositionSizeMarket` 내부에서 `prepareCallbackValues`가 호출되어 포지션 크기 증가에 필요한 모든 값을 계산하며, 포지션이 `isCounterTrade`가 true로 설정된 경우 `values.counterTradeCollateralToReturn`을 포함합니다. 이 경우 `_partialTrade.collateralAmount`도 `values.counterTradeCollateralToReturn`만큼 감소합니다.

```solidity
    function prepareCallbackValues(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal view returns (IUpdatePositionSizeUtils.IncreasePositionSizeValues memory values) {
        // 1.1 Calculate position size delta
        bool isLeverageUpdate = _partialTrade.collateralAmount == 0;
        values.positionSizeCollateralDelta = TradingCommonUtils.getPositionSizeCollateral(
            isLeverageUpdate ? _existingTrade.collateralAmount : _partialTrade.collateralAmount,
            _partialTrade.leverage
        );

        // 1.2 Validate counter trade and update position size delta if needed
        if (_existingTrade.isCounterTrade) {
>>>         (values.isCounterTradeValidated, values.exceedingPositionSizeCollateral) = TradingCommonUtils
                .validateCounterTrade(_existingTrade, values.positionSizeCollateralDelta);

            if (values.isCounterTradeValidated && values.exceedingPositionSizeCollateral > 0) {
                if (isLeverageUpdate) {
                    // For leverage updates, simply reduce leverage delta to reach 0 skew
                    _partialTrade.leverage -= uint24(
                        (values.exceedingPositionSizeCollateral * 1e3) / _existingTrade.collateralAmount
                    );
                } else {
                    // For collateral adds, reduce collateral delta to reach 0 skew
                    values.counterTradeCollateralToReturn =
                        (values.exceedingPositionSizeCollateral * 1e3) /
                        _partialTrade.leverage;
>>>                 _partialTrade.collateralAmount -= uint120(values.counterTradeCollateralToReturn);
                }

                values.positionSizeCollateralDelta = TradingCommonUtils.getPositionSizeCollateral(
                    isLeverageUpdate ? _existingTrade.collateralAmount : _partialTrade.collateralAmount,
                    _partialTrade.leverage
                );
            }
        }

        // ...
    }
```

그러나 `validateCallback`이 `CancelReason.NONE`이 아닌 값을 반환하고 `handleCanceled`가 트리거되면, `values.counterTradeCollateralToReturn`을 고려하지 않고 `_partialTrade.collateralAmount`만 사용자에게 보냅니다.

```solidity
    function handleCanceled(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        ITradingCallbacks.CancelReason _cancelReason,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal {
        // 1. Charge gov fee on trade (if trade exists)
        if (_cancelReason != ITradingCallbacks.CancelReason.NO_TRADE) {
            // 1.1 Distribute gov fee
            uint256 govFeeCollateral = TradingCommonUtils.getMinGovFeeCollateral(
                _existingTrade.collateralIndex,
                _existingTrade.user,
                _existingTrade.pairIndex
            );
            uint256 finalGovFeeCollateral = _getMultiCollatDiamond().realizeTradingFeesOnOpenTrade(
                _existingTrade.user,
                _existingTrade.index,
                govFeeCollateral,
                _answer.current
            );
            TradingCommonUtils.distributeExactGovFeeCollateral(
                _existingTrade.collateralIndex,
                _existingTrade.user,
                finalGovFeeCollateral
            );
        }

        // 2. Send back partial collateral to trader
        TradingCommonUtils.transferCollateralTo(
            _existingTrade.collateralIndex,
            _existingTrade.user,
>>>         _partialTrade.collateralAmount
        );
    }
```

이로 인해 사용자에게 반환되는 담보 금액이 부정확해져 자금 손실이 발생합니다.

## 권장 사항

`handleCanceled` 내부에서 자금을 반환할 때 `values.counterTradeCollateralToReturn`도 고려하십시오.

```diff
//...
        // 2. Send back partial collateral to trader
        TradingCommonUtils.transferCollateralTo(
            _existingTrade.collateralIndex,
            _existingTrade.user,
-            _partialTrade.collateralAmount
+            _partialTrade.collateralAmount + values.counterTradeCollateralToReturn
        );
//...
```
# [M-01] 음수 PnL로 레버리지를 줄이면 자산이 금고로 잘못 전달될 수 있음 (Decreasing leverage with negative PnL may misdirect assets to the vault)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

음수 `collateralSentToTrader`를 유발하는 음수 PnL을 가진 거래에 대해 레버리지 감소가 실행되면, 담보가 금고로 전송됩니다(`_collateralSentToTrader < _availableCollateralInDiamond`이기 때문). 이는 `partialNetPnlCollateral`이 음수인 경우 발생할 수 있습니다.

```solidity
    function updateTradeSuccess(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        IUpdatePositionSizeUtils.DecreasePositionSizeValues memory _values,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal {
        // 1. Update trade in storage (realizes pending holding fees)
        _getMultiCollatDiamond().updateTradePosition(
            ITradingStorage.Id(_existingTrade.user, _existingTrade.index),
            _values.newCollateralAmount,
            _values.newLeverage,
            _existingTrade.openPrice, // open price stays the same
            ITradingStorage.PendingOrderType.MARKET_PARTIAL_CLOSE, // don't refresh liquidation params
            _values.priceImpact.positionSizeToken,
            _values.existingPnlPercent > 0,
            _answer.current
        );

        // 2. Calculate remaining success callback values
        prepareSuccessCallbackValues(_existingTrade, _partialTrade, _values);

        // 3. Handle collateral/pnl transfers with vault/diamond/user
>>>     TradingCommonUtils.handleTradeValueTransfer(
            _existingTrade,
            _values.collateralSentToTrader,
            int256(_values.availableCollateralInDiamond)
        );

        if (_values.isLeverageUpdate) {
            // 4.1.1 Realize pnl so new trade value + collateral sent to trader = previous trade value (no value created or destroyed)
            // First we realize the partial pnl amount so prev raw pnl = new raw pnl, and then we remove what we sent from trader
            _getMultiCollatDiamond().realizePnlOnOpenTrade(
                _existingTrade.user,
                _existingTrade.index,
                _values.pnlToRealizeCollateral
            );

            // 4.1.2 Realize trading fee (if leverage decrease)
            // Not needed for collateral decreases because closing fee is removed from available collateral in diamond and trade value
            // So vault sends as much as if there was no closing fee, but we send less to the trader, so we always have the partial closing fee in diamond
            // This is especially important for lev decreases when nothing is available in diamond, this will transfer the closing fee from vault
>>>         _getMultiCollatDiamond().realizeTradingFeesOnOpenTrade(
                _existingTrade.user,
                _existingTrade.index,
                _values.closingFeeCollateral,
                _answer.current
            );

            // 4.1.3 Decrease collat available in diamond (if we've sent negative PnL to vault)
            if (_values.collateralSentToTrader < 0) {
>>>             _getMultiCollatDiamond().storeAlreadyTransferredNegativePnl(
                    _existingTrade.user,
                    _existingTrade.index,
                    uint256(-_values.collateralSentToTrader)
                );
            }
        } else {
            // ...
    }
```

그런 다음 이것이 레버리지 업데이트이므로 `realizeTradingFeesOnOpenTrade`가 트리거됩니다. 수수료로 인해 거래가 청산 가능하게 되면 청산되거나, 그렇지 않으면 사용자의 `realizedTradingFeesCollateral`이 업데이트됩니다.

```solidity
    function realizeTradingFeesOnOpenTrade(
        address _trader,
        uint32 _index,
        uint256 _feesCollateral,
        uint64 _currentPairPrice
    ) external returns (uint256 finalFeesCollateral) {
        IFundingFees.FundingFeesStorage storage s = _getStorage();
        IFundingFees.RealizeTradingFeesValues memory v;

        v.trade = TradingCommonUtils.getTrade(_trader, _index);
        v.liqPrice = TradingCommonUtils.getTradeLiquidationPrice(v.trade, int256(_feesCollateral), _currentPairPrice);

        IFundingFees.TradeFeesData storage tradeFeesData = s.tradeFeesData[_trader][_index];
        v.newRealizedFeesCollateral = tradeFeesData.realizedTradingFeesCollateral;
        v.newRealizedPnlCollateral = tradeFeesData.realizedPnlCollateral;

        // @audit - this should not be possible on openTrade
        // 1. Check if trade can be liquidated after charging trading fee
        if ((v.trade.long && _currentPairPrice <= v.liqPrice) || (!v.trade.long && _currentPairPrice >= v.liqPrice)) {
>>>         TradingCallbacksUtils._unregisterTrade(
                v.trade,
                0,
                ITradingStorage.PendingOrderType.LIQ_CLOSE,
                _currentPairPrice,
                v.liqPrice,
                _currentPairPrice
            );

            uint256 collateralPriceUsd = _getMultiCollatDiamond().getCollateralPriceUsd(v.trade.collateralIndex);

            emit ITradingCallbacksUtils.LimitExecuted(
                ITradingStorage.Id(address(0), 0),
                v.trade.user,
                v.trade.index,
                0,
                v.trade,
                address(0),
                ITradingStorage.PendingOrderType.LIQ_CLOSE,
                _currentPairPrice,
                _currentPairPrice,
                v.liqPrice,
                ITradingCommonUtils.TradePriceImpact(0, 0, 0, 0, 0, 0),
                -100 * 1e10,
                0,
                collateralPriceUsd,
                false
            );
        } else {
            finalFeesCollateral = _feesCollateral;
            // 2. Send fee delta from vault if total realized fees are above trade collateral, so collateral in diamond always >= 0
>>>         uint256 availableCollateralInDiamond = TradingCommonUtils.getTradeAvailableCollateralInDiamond(
                _trader,
                _index,
                v.trade.collateralAmount
            );

            if (finalFeesCollateral > availableCollateralInDiamond) {
                v.amountSentFromVaultCollateral = finalFeesCollateral - availableCollateralInDiamond;
                TradingCommonUtils.receiveCollateralFromVault(v.trade.collateralIndex, v.amountSentFromVaultCollateral);

                v.newRealizedPnlCollateral -= int128(int256(v.amountSentFromVaultCollateral));
                tradeFeesData.realizedPnlCollateral = v.newRealizedPnlCollateral;
            }

            // 3. Register new realized fees
            v.newRealizedFeesCollateral += uint128(finalFeesCollateral - v.amountSentFromVaultCollateral);
            tradeFeesData.realizedTradingFeesCollateral = v.newRealizedFeesCollateral;

            /// @dev This makes the transaction revert in case new available collateral in diamond is < 0
            TradingCommonUtils.getTradeAvailableCollateralInDiamond(_trader, _index, v.trade.collateralAmount);

            // 4. Update UI realized pnl mapping
            s.tradeUiRealizedPnlData[_trader][_index].realizedTradingFeesCollateral += uint128(finalFeesCollateral);
        }

        emit IFundingFeesUtils.TradingFeesRealized(
            v.trade.collateralIndex,
            _trader,
            _index,
            _feesCollateral,
            finalFeesCollateral,
            v.newRealizedFeesCollateral,
            v.newRealizedPnlCollateral,
            v.amountSentFromVaultCollateral
        );
    }
```

두 경우 모두 `getTradeAvailableCollateralInDiamond`가 업데이트되기 전에 액세스됩니다. `storeAlreadyTransferredNegativePnl`은 `realizeTradingFeesOnOpenTrade` 이후에 트리거되므로 `getTradeAvailableCollateralInDiamond`는 거래 수수료가 실현될 때 잘못된 금액을 반환합니다.

```solidity
    function getTradeAvailableCollateralInDiamondRaw(
        address _trader,
        uint32 _index,
        uint256 _collateralAmount
    ) public view returns (int256) {
        IFundingFees.TradeFeesData memory tradeFeesData = _getMultiCollatDiamond().getTradeFeesData(_trader, _index);
        return
            int256(_collateralAmount + uint256(tradeFeesData.virtualAvailableCollateralInDiamond)) -
            int256(
                uint256(
                    tradeFeesData.realizedTradingFeesCollateral +
                        tradeFeesData.manuallyRealizedNegativePnlCollateral +
>>>                     tradeFeesData.alreadyTransferredNegativePnlCollateral
                )
            );
    }
```

## 권장 사항

`realizeTradingFeesOnOpenTrade` 이전에 `storeAlreadyTransferredNegativePnl`을 트리거하십시오.

# [M-02] 부과된 수수료와 지불된 수수료의 차이 (Difference between charged and paid fees)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

프로토콜은 지불된 오라클 수수료를 충당하기 위해 정부 수수료(government fees)를 부과합니다.

```solidity
    function openTradeMarketCallback(ITradingCallbacks.AggregatorAnswer memory _a) external tradingActivated {
<...>
            // Gov fee to pay for oracle cost
            TradingCommonUtils.updateFeeTierPoints(t.collateralIndex, t.user, t.pairIndex, 0);

            uint256 govFeeCollateral = TradingCommonUtils.getMinGovFeeCollateral(
                t.collateralIndex,
                t.user,
                t.pairIndex
            );
```

문제는 오라클 비용이 거래 포지션 크기에 의존한다는 것입니다.

```solidity
    function getLinkFee(
        uint8 _collateralIndex,
        address _trader,
        uint16 _pairIndex,
>>      uint256 _positionSizeCollateral, // collateral precision
        bool _isCounterTrade
    ) public view returns (uint256) {
        if (_positionSizeCollateral == 0) return 0;

        (, int256 linkPriceUsd, , , ) = _getStorage().linkUsdPriceFeed.latestRoundData(); // 1e8

        uint256 feeRateMultiplier = _isCounterTrade
            ? _getMultiCollatDiamond().getPairCounterTradeFeeRateMultiplier(_pairIndex)
            : 1e3;

        uint256 linkFeeCollateral = _getMultiCollatDiamond().pairOraclePositionSizeFeeP(_pairIndex) *
            feeRateMultiplier *
            TradingCommonUtils.getPositionSizeCollateralBasis(
                _collateralIndex,
                _pairIndex,
>>              _positionSizeCollateral,
                feeRateMultiplier
            ); // avoid stack too deep

        uint256 rawLinkFee = (getUsdNormalizedValue(_collateralIndex, linkFeeCollateral) * 1e8) /
            uint256(linkPriceUsd) /
            ConstantsUtils.P_10 /
            100 /
            1e3;

        return _getMultiCollatDiamond().calculateFeeAmount(_trader, rawLinkFee);
    }
```

그러나 많은 경우 프로토콜은 최소 포지션 크기(위의 코드 스니펫과 같이) 또는 감소된 포지션 크기에서 정부 수수료를 부과합니다.

```solidity
    function openTradeMarketCallback(ITradingCallbacks.AggregatorAnswer memory _a) external tradingActivated {
<...>
        if (v.cancelReason == ITradingCallbacks.CancelReason.NONE) {
            // Return excess counter trade collateral
            if (v.collateralToReturn > 0) {
                TradingCommonUtils.transferCollateralTo(t.collateralIndex, t.user, v.collateralToReturn);
>>              t.collateralAmount = v.newCollateralAmount;
                emit ITradingCallbacksUtils.CounterTradeCollateralReturned(_a.orderId, t.collateralIndex, t.user, v.collateralToReturn);
            }
```

이런 식으로 콜백 중에 포지션 크기를 조정하여 큰 LINK 수수료를 유발하는 예상치 못한 포지션으로 카운터 거래를 전송함으로써 프로토콜 잔액이 고갈될 수 있습니다.

## 권장 사항

주문의 성공 여부와 관계없이 `getLinkFee`에서 사용된 전체 포지션 크기를 고려하여 정부 수수료를 부과하는 것을 고려하십시오.

# [M-03] 수동 음수 PnL 실현이 손실을 부정확하게 실현할 수 있음 (Manual negative PnL realization can incorrectly realize losses)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`executeManualNegativePnlRealizationCallback` 함수는 가격 영향 후의 가격 대신 현재 가격을 사용하여 미실현 PnL을 계산합니다. 이 계산은 가격 영향이 실제로 트레이더의 포지션에 이익이 되는 상황을 설명하지 못합니다.

스큐 가격 영향이 크고(`totalPriceImpactP` < 0) 포지션에 이익이 되는 경우(예: 롱 포지션의 경우 청산 가격이 더 높거나 숏 포지션의 경우 더 낮음), 가격 영향을 고려한 후 포지션이 실제로 수익성이 있을 수 있습니다. 그러나 함수는 현재 오라클 가격만 사용하여 PnL을 계산하므로 가격 영향 후에는 손실이 없을 때에도 손실을 잘못 실현할 수 있습니다.

```solidity
    function executeManualNegativePnlRealizationCallback(
        ITradingStorage.PendingOrder memory _order,
        ITradingCallbacks.AggregatorAnswer memory _a
    ) external {
        if (trade.isOpen) {
            int256 unrealizedPnlCollateral = TradingCommonUtils.getTradeUnrealizedRawPnlCollateral(
                trade,
                _a.current // @audit Uses current price instead of price after impact
            );
            uint256 unrealizedNegativePnlCollateral = unrealizedPnlCollateral < 0
                ? uint256(-unrealizedPnlCollateral)
                : uint256(0);

            ...

    }
```

다음 시나리오를 고려하십시오.

- 트레이더가 자산에 대해 롱 포지션을 엽니다.
- 시장 상황이 유리한 스큐 가격 영향(예: 많은 숏, 적은 롱)으로 이어집니다.
- `executeManualNegativePnlRealizationCallback`이 호출될 때:
  - 현재 오라클 가격은 작은 손실(음수 PnL)을 나타냅니다.
  - 스큐 가격 영향을 고려한 후 포지션은 실제로 수익성이 있을 것입니다.
  - 함수는 오라클 가격을 기반으로 음수 PnL을 잘못 실현합니다.
  - 담보가 불필요하게 금고로 전송됩니다.

## 권장 사항

가격 영향이 포지션에 유리한지 확인하십시오. 그렇다면 `unrealizedPnlCollateral`을 계산하는 데 사용하십시오.

# [M-04] 카운터 거래에 대한 레버리지 델타 계산 시 잘못된 반올림 방향 (Wrong rounding direction when calculating leverage delta for counter trades)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`IncreasePositionSizeUtils.prepareCallbackValues`에서 카운터 거래가 레버리지 증가를 통해 포지션 크기를 늘리고 스큐를 중화하는 데 필요한 가용 포지션 크기를 초과할 때, 함수는 스큐를 0으로 만들기 위해 레버리지 델타 감소를 계산합니다.

그러나 `(values.exceedingPositionSizeCollateral * 1e3) / _existingTrade.collateralAmount` 계산은 버림(rounds down)하는 정수 나눗셈을 사용합니다. 이 버림은 레버리지 감소가 스큐를 완전히 중화하기에 불충분함을 의미하며, 스큐를 반대 방향으로 약간 밀어내는 작은 초과 포지션 크기를 남깁니다.

이 반올림의 영향은 담보 금액에 따라 다릅니다. 단일 레버리지 단위(버림)의 경우 포지션 크기는 최대 `_existingTrade.collateralAmount / 1e3` 담보 단위까지 오버슈트할 수 있습니다. 더 큰 담보 금액의 경우 카운터 거래의 핵심 목적과 모순되게 반대 방향으로 상당한 스큐가 생성될 수 있습니다.

```solidity
    function prepareCallbackValues(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal view returns (IUpdatePositionSizeUtils.IncreasePositionSizeValues memory values) {
        ...

        // 1.2 Validate counter trade and update position size delta if needed
        if (_existingTrade.isCounterTrade) {
            (values.isCounterTradeValidated, values.exceedingPositionSizeCollateral) = TradingCommonUtils
                .validateCounterTrade(_existingTrade, values.positionSizeCollateralDelta);

            if (values.isCounterTradeValidated && values.exceedingPositionSizeCollateral > 0) {
                if (isLeverageUpdate) {
                    // For leverage updates, simply reduce leverage delta to reach 0 skew
                    _partialTrade.leverage -= uint24(
                        (values.exceedingPositionSizeCollateral * 1e3) / _existingTrade.collateralAmount // @audit Rounds down instead of up
                    );
            ...
    }
```

## 권장 사항

레버리지를 줄일 때 버림(round down) 대신 올림(round up)하도록 계산을 수정하여 스큐가 반대 방향으로 밀리지 않도록 하십시오.

# [M-05] 초과 담보가 있는 카운터 거래의 시가가 잘못 계산됨 (Open price for counter trades with excess collateral calculated wrongly)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`openTradeMarketCallback` 함수는 거래가 시장에 미칠 가격 영향을 기반으로 계산된 `openPrice`(L102)로 거래를 엽니다. 거래가 `counter` 거래인 경우 쌍(pair)의 현재 스큐를 초과하는 담보 금액의 일부가 사용자에게 반환됩니다(L106).

```solidity
File: TradingCallbacksUtils.sol
094:     function openTradeMarketCallback(ITradingCallbacks.AggregatorAnswer memory _a) external tradingActivated {
095:         ITradingStorage.PendingOrder memory o = _getPendingOrder(_a.orderId);
096:
097:         if (!o.isOpen) return;
098:
099:         ITradingStorage.Trade memory t = o.trade;
100:         ITradingCallbacks.Values memory v = _openTradePrep(t, _a.current, o.maxSlippageP);
101:
102:         t.openPrice = uint64(v.priceImpact.priceAfterImpact);
103:
104:         if (v.cancelReason == ITradingCallbacks.CancelReason.NONE) {
105:             // Return excess counter trade collateral
106:             if (v.collateralToReturn > 0) {
107:                 TradingCommonUtils.transferCollateralTo(t.collateralIndex, t.user, v.collateralToReturn);
108:                 t.collateralAmount = v.newCollateralAmount;
109:                 emit ITradingCallbacksUtils.CounterTradeCollateralReturned(_a.orderId, t.collateralIndex, t.user, v.collateralToReturn);
110:             }
111:
112:             t = _registerTrade(t, o, _a.current);
```

문제는 `_openTradePrep` 함수 내부에서 `priceAfterImpact`(== 시가)가 먼저 거래의 초기(전체) 담보 금액에 대해 계산된 다음(L698) 거래가 `counter`인 경우에만 `newCollateralAmount`(감소된) 금액이 계산된다는 것입니다(L730).

```solidity
File: TradingCallbacksUtils.sol
688:     function _openTradePrep(
689:         ITradingStorage.Trade memory _trade,
690:         uint256 _executionPriceRaw,
691:         uint256 _maxSlippageP
692:     ) internal  returns (ITradingCallbacks.Values memory v) {
693:         uint256 positionSizeCollateral = TradingCommonUtils.getPositionSizeCollateral(
694:             _trade.collateralAmount,
695:             _trade.leverage
696:         );
697:
698:         v.priceImpact = TradingCommonUtils.getTradeOpeningPriceImpact(
699:             ITradingCommonUtils.TradePriceImpactInput(_trade, _executionPriceRaw, positionSizeCollateral, 0),
700:             _getMultiCollatDiamond().getCurrentContractsVersion()
701:         );
702:
703:         uint256 maxSlippage = (uint256(_trade.openPrice) * _maxSlippageP) / 100 / 1e3;
704:
...
728:
729:         if (_trade.isCounterTrade && v.cancelReason == ITradingCallbacks.CancelReason.NONE) {
730:             (v.cancelReason, v.collateralToReturn, v.newCollateralAmount) = _validateCounterTrade(
731:                 _trade,
732:                 positionSizeCollateral
733:             );
734:         }
735:     }
```

이는 초과 담보가 있는 `counter` 거래가 실제 감소된 금액이 아닌 초기(전체) 담보 금액을 기준으로 계산되기 때문에 불리한 시가에 직면하게 됨을 의미합니다.

동일한 문제가 `executeTriggerOpenOrderCallback`에도 존재합니다. 이 함수 역시 `_openTradePrep`을 활용하기 때문입니다.

## 권장 사항

`TradingCallbacksUtils#_openTradePrep` 함수를 업데이트하여 먼저 `newCollateralAmount`(사용자에게 반환될 초과 담보 없이)를 계산한 다음 초기(전체) 담보 금액 대신 이 값을 기반으로 가격 영향을 계산하도록 고려하십시오.

# [M-06] `PNL_WITHDRAWAL` 주문 유형에 대해 잘못된 LINK 수수료가 부과됨 (Incorrect LINK fee is charged for the `PNL_WITHDRAWAL` order type)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`TradingInteractionsUtils.withdrawPositivePnl` 함수는 `GetPriceInput.positionSizeCollateral` 매개변수로 `TradingCommonUtils.getMinPositionSizeCollateral(t.collateralIndex, t.pairIndex)`(쌍에 대한 담보 토큰의 최소 포지션 크기) 값의 절반을 사용합니다.
이 매개변수는 `PriceAggregatorUtils.getLinkFee` 함수에서 LINK 수수료 계산에 사용됩니다. 그러나 `getLinkFee` 함수는 항상 `_positionSizeCollateral`과 쌍에 대한 담보 토큰의 최소 포지션 크기 사이의 최대값을 사용합니다.
따라서 실제 LINK 수수료는 항상 예상보다 높을 것입니다.

```solidity
    function withdrawPositivePnl(uint32 _index, uint120 _amountCollateral) external tradingActivatedOrCloseOnly {
<...>
        _getMultiCollatDiamond().getPrice(
            IPriceAggregator.GetPriceInput(
                t.collateralIndex,
                t.pairIndex,
                ITradingStorage.Id({user: t.user, index: t.index}),
                orderId,
                pendingOrder.orderType,
>>              TradingCommonUtils.getMinPositionSizeCollateral(t.collateralIndex, t.pairIndex) / 2,
                ChainUtils.getBlockNumber(),
                t.isCounterTrade
            )
        );
```

```solidity
    function getLinkFee(
<...>
        uint256 linkFeeCollateral = _getMultiCollatDiamond().pairOraclePositionSizeFeeP(_pairIndex) *
            feeRateMultiplier *
            TradingCommonUtils.getPositionSizeCollateralBasis(
                _collateralIndex,
                _pairIndex,
>>              _positionSizeCollateral,
                feeRateMultiplier
            ); // avoid stack too deep
```

TradingCommonUtils.sol

```solidity
    function getPositionSizeCollateralBasis(
        uint8 _collateralIndex,
        uint256 _pairIndex,
        uint256 _positionSizeCollateral,
        uint256 _feeRateMultiplier
    ) public view returns (uint256) {
>>      uint256 minPositionSizeCollateral = getMinPositionSizeCollateral(_collateralIndex, _pairIndex);
        uint256 adjustedMinPositionSizeCollateral = (minPositionSizeCollateral * 1e3) / _feeRateMultiplier;
        return
            _positionSizeCollateral > adjustedMinPositionSizeCollateral
                ? _positionSizeCollateral
                : adjustedMinPositionSizeCollateral;
    }
```

## 권장 사항

`_input.orderType == ITradingStorage.PendingOrderType.PNL_WITHDRAWAL`일 때 `linkFeePerNode`를 2로 나누는 것을 고려하십시오.

```diff
    function getPrice(
        IPriceAggregator.GetPriceInput memory _input
    ) external validCollateralIndex(_input.collateralIndex) {
<...>
        // 2. Calculate link fee for each oracle
        TradingCommonUtils.updateFeeTierPoints(_input.collateralIndex, _input.tradeId.user, _input.pairIndex, 0);
        uint256 linkFeePerNode = getLinkFee(
            _input.collateralIndex,
            _input.tradeId.user,
            _input.pairIndex,
            _input.positionSizeCollateral,
            _input.isCounterTrade
        ) / s.oracles.length;
+
+       if (_input.orderType == ITradingStorage.PendingOrderType.PNL_WITHDRAWAL) {
+           linkFeePerNode = linkFeePerNode / 2;
+       }
```

```diff
    function withdrawPositivePnl(uint32 _index, uint120 _amountCollateral) external tradingActivatedOrCloseOnly {
<...>
        _getMultiCollatDiamond().getPrice(
            IPriceAggregator.GetPriceInput(
                t.collateralIndex,
                t.pairIndex,
                ITradingStorage.Id({user: t.user, index: t.index}),
                orderId,
                pendingOrder.orderType,
-               TradingCommonUtils.getMinPositionSizeCollateral(t.collateralIndex, t.pairIndex) / 2,
+               TradingCommonUtils.getMinPositionSizeCollateral(t.collateralIndex, t.pairIndex),
                ChainUtils.getBlockNumber(),
                t.isCounterTrade
            )
        );
```

# [L-01] SetRoles가 `GOV_TIMELOCK`에 대해서만 자체 할당을 방지함 (SetRoles prevents self-assignment for `GOV_TIMELOCK` only)

setRoles 함수는 `GOV_TIMELOCK` 또는 `GOV_EMERGENCY_TIMELOCK` 역할이 있는 계정만 다른 계정에 역할을 할당할 수 있도록 설계되었습니다.
권한 상승을 방지하기 위해 함수는 호출자가 자신에게 `GOV_TIMELOCK` 또는 `GOV_EMERGENCY_TIMELOCK` 역할을 할당하는 것을 허용해서는 안 됩니다.
그러나 현재 구현은 `GOV_TIMELOCK` 역할의 자체 할당만 확인합니다.

```
if (_roles[i] == Role.GOV_TIMELOCK && _accounts[i] == msg.sender) {
    revert NotAllowed();
}
```

함수가 해당 역할에 의해서도 호출될 수 있음에도 불구하고 `Role.GOV_EMERGENCY_TIMELOCK`에 대한 동등한 확인이 없습니다.

권장 사항:

```
if (
    (_roles[i] == Role.GOV_TIMELOCK && _accounts[i] == msg.sender) ||
    (_roles[i] == Role.GOV_EMERGENCY_TIMELOCK && _accounts[i] == msg.sender)
) {
    revert NotAllowed();
}
```

# [L-02] `executeManualHoldingFeesRealizationCallback`의 상태 업데이트로 인해 수수료 오류가 발생할 수 있음 (State update in `executeManualHoldingFeesRealizationCallback` may cause fee errors)

`executeManualHoldingFeesRealizationCallback` 함수는 거래의 차입 수수료를 재설정하기 위해 `resetTradeBorrowingFees`를 호출합니다.

```solidity
    function executeManualHoldingFeesRealizationCallback(
        ITradingStorage.PendingOrder memory _order,
        ITradingCallbacks.AggregatorAnswer memory _a
    ) external {
        ...
        _getMultiCollatDiamond().resetTradeBorrowingFees(
            trade.collateralIndex,
            trade.user,
            trade.pairIndex,
            trade.index,
            trade.long,
            _a.current
        );
        ...
    }
```

`resetTradeBorrowingFees` 함수는 `getBorrowingPairPendingAccFees`를 사용하여 현재 누적된 수수료를 계산하고 이를 거래의 새 initialAccFees로 저장합니다.

```solidity
    function resetTradeBorrowingFees(
        uint8 _collateralIndex,
        address _trader,
        uint16 _pairIndex,
        uint32 _index,
        bool _long,
        uint256 _currentPairPrice
    ) public validCollateralIndex(_collateralIndex) {
        uint256 currentBlock = ChainUtils.getBlockNumber();

        (uint64 pairAccFeeLong, uint64 pairAccFeeShort, , ) = getBorrowingPairPendingAccFees(
            _collateralIndex,
            _pairIndex,
            currentBlock,
            _currentPairPrice
        );
        ...

        _getStorage().initialAccFees[_collateralIndex][_trader][_index] = initialFees;

        ...
    }
```

문제는 이 함수가 거래별 initialAccFees만 업데이트하고 새로 계산된 누적 수수료를 전역 상태(`borrowingFeesStorage.pairs`)로 다시 업데이트하지 않는다는 것입니다. 그리고 `pairAccFeeLong/pairAccFeeShort`는 이제 현재 쌍 가격의 영향을 받습니다.

다음 시나리오를 고려하십시오.

- 높은 가격에서의 수동 실현: 쌍 가격이 높을 때(예: 100e10) `executeManualHoldingFeesRealizationCallback`이 호출됩니다.
- 거래 기준 업데이트: 거래의 `initialAccFees`는 높은 가격에서 계산된 누적 수수료를 기준으로 설정됩니다.
- 전역 상태 유지: 쌍의 전역 누적 수수료(BorrowingFeesStorage.pairs.accFeeLong/accFeeShort)가 업데이트되지 않습니다.
- 가격 대폭 하락: 쌍 가격이 훨씬 낮은 값(예: 50e10)으로 떨어집니다.
- 후속 수수료 계산: 차입 수수료가 다시 계산될 때 현재 전역 누적 수수료(낮은 가격에서 계산됨)가 저장된 `initialAccFees`보다 낮을 수 있습니다.
- 언더플로 되돌리기: `current global accumulated fees - initialAccFees` 계산이 언더플로되어 트랜잭션이 되돌려집니다.

`executeManualHoldingFeesRealizationCallback`이 호출될 때 전역 누적 수수료를 업데이트하는 것이 좋습니다.

# [L-03] 관리자가 활성 거래에 대한 가격 영향 매개변수를 변경할 수 있음 (Admin can change price impact parameters for active trades)

`PriceImpactUtils.setUserPriceImpact` 내부 함수는 권한이 있는 관리자가 특정 트레이더-쌍 조합에 대해 사용자 지정 `cumulVolPriceImpactMultiplier` 및 `fixedSpreadP`를 설정할 수 있도록 합니다. 현재 구현의 문제는 사용자의 `이미 열려 있는 포지션`에 대해 이러한 매개변수를 변경하여 진입 후 게임의 규칙을 변경할 수 있다는 것입니다. 이는 유효 청산 가격 및 청산 가격 계산을 악화시킬 수 있습니다.

권장 사항:
`setUserPriceImpact`를 통해 설정된 매개변수가 해당 사용자-쌍에 대해 매개변수가 설정된 후 열린 거래에만 적용되도록 로직을 수정하는 것이 좋습니다.

# [L-04] 반올림 오류로 인해 사용자에게 더 유리한 청산 가격이 제공될 수 있음 (Rounding errors can favor users by a better liquidation price)

`DecreasePositionSizeUtils` 및 `BorrowingFeesUtils` 계약에는 사용자가 더 나은 청산 가격으로 유리해질 수 있는 잠재적인 반올림 오류가 있습니다. 구체적으로 `DecreasePositionSizeUtils`의 `prepareCallbackValues` 함수에서 `newLiqPrice` 계산에는 반올림 오류를 유발할 수 있는 나눗셈 연산이 포함되며, `BorrowingFeesUtils`의 `getTradeLiquidationPrice` 함수에서도 계산 결과 반올림 오류가 발생할 수 있습니다.

DecreasePositionSizeUtils.sol

```solidity
    function prepareCallbackValues(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal view returns (IUpdatePositionSizeUtils.DecreasePositionSizeValues memory values) {
<...>
        // 6. Calculate existing and new trade liquidation price
        values.existingLiqPrice = TradingCommonUtils.getTradeLiquidationPrice(_existingTrade, _answer.current);
        values.newLiqPrice = TradingCommonUtils.getTradeLiquidationPrice(
            _existingTrade,
            _existingTrade.openPrice,
            values.newCollateralAmount,
            values.newLeverage,
            values.isLeverageUpdate ? int256(values.closingFeeCollateral) - values.pnlToRealizeCollateral : int256(0),
            _getMultiCollatDiamond().getTradeLiquidationParams(_existingTrade.user, _existingTrade.index),
            _answer.current,
            values.isLeverageUpdate
                ? 1e18
>>              : ((values.existingPositionSizeCollateral - values.positionSizeCollateralDelta) * 1e18) /
                    values.existingPositionSizeCollateral,
            false
        );


        /// @dev Other calculations are in separate helper called after realizing pending holding fees
        // Because they can be impacted by pending holding fees realization (eg. available in diamond calc)
    }
```

BorrowingFeesUtils.sol

```solidity
    function getTradeLiquidationPrice(IBorrowingFees.LiqPriceInput calldata _input) internal view returns (uint256) {
        uint256 closingFeesCollateral = TradingCommonUtils.getTotalTradeFeesCollateral(
            _input.collateralIndex,
            address(0), // never apply fee tiers
            _input.pairIndex,
            (_input.collateral * _input.leverage) / 1e3,
            _input.isCounterTrade
        );


        IFundingFees.TradeHoldingFees memory holdingFees;
        int256 totalRealizedPnlCollateral;


        if (!_input.beforeOpened) {
            holdingFees = _getMultiCollatDiamond().getTradePendingHoldingFeesCollateral(
                _input.trader,
                _input.index,
                _input.currentPairPrice
            );


            (, , totalRealizedPnlCollateral) = _getMultiCollatDiamond().getTradeRealizedPnlCollateral(
                _input.trader,
                _input.index
            );
        }


        uint256 spreadP = _getMultiCollatDiamond().pairSpreadP(_input.trader, _input.pairIndex);
        ITradingStorage.ContractsVersion contractsVersion = _input.beforeOpened
            ? _getMultiCollatDiamond().getCurrentContractsVersion()
            : _getMultiCollatDiamond().getTradeContractsVersion(_input.trader, _input.index);


        return
            _getTradeLiquidationPrice(
                _input.openPrice,
                _input.long,
                _input.collateral,
                _input.leverage,
                int256(closingFeesCollateral) +
                    ((holdingFees.totalFeeCollateral - totalRealizedPnlCollateral) *
>>                      int256(_input.partialCloseMultiplier)) /
                    1e18 +
                    _input.additionalFeeCollateral,
                _getMultiCollatDiamond().getCollateral(_input.collateralIndex).precisionDelta,
                _input.liquidationParams,
                contractsVersion,
                spreadP
            );
    }
```

`_feesCollateral` 값을 양의 무한대(positive infinity)로 반올림하는 것을 고려하십시오.

# [L-05] 여러 작업 중에 `updateFeeTierPoints`가 제대로 호출되지 않음 (`updateFeeTierPoints` is not properly called during several operations)

`getTradeClosingPriceImpact`는 여러 요인을 기반으로 특정 거래에 대한 가격 영향을 계산하는 함수이며, 그 중 하나는 담보 토큰의 총 수수료(`getTotalTradeFeesCollateral`)입니다.

```solidity
    function getTradeClosingPriceImpact(
        ITradingCommonUtils.TradePriceImpactInput memory _input
    ) public view returns (ITradingCommonUtils.TradePriceImpact memory output, uint256 tradeValueCollateralNoFactor) {

        // ...

        tradeValueCollateralNoFactor = getTradeValueCollateral(
            trade,
            getPnlPercent(
                trade.openPrice,
                getPriceAfterImpact(
                    _input.oraclePrice,
                    output.fixedSpreadP + cumulVolPriceImpactP + output.skewPriceImpactP
                ),
                trade.long,
                trade.leverage
            ),
>>>         getTotalTradeFeesCollateral(
                trade.collateralIndex,
                trade.user,
                trade.pairIndex,
                getPositionSizeCollateral(trade.collateralAmount, trade.leverage),
                trade.isCounterTrade
            ),
            _input.currentPairPrice
        );
// ...
}
```

`getTotalTradeFeesCollateral`은 `calculateFeeAmount`에 캐시된 트레이더의 수수료 승수를 사용하여 수수료를 계산합니다. 그러나 여러 경우에 트레이더의 수수료 포인트를 업데이트하지 않고 `getTradeClosingPriceImpact`가 호출됩니다.

```solidity
    function getTotalTradeFeesCollateral(
        uint8 _collateralIndex,
        address _trader,
        uint16 _pairIndex,
        uint256 _positionSizeCollateral,
        bool _isCounterTrade
    ) public view returns (uint256) {
        uint256 feeRateP = _getMultiCollatDiamond().pairTotalPositionSizeFeeP(_pairIndex);

        uint256 feeRateMultiplier = _isCounterTrade
            ? _getMultiCollatDiamond().getPairCounterTradeFeeRateMultiplier(_pairIndex)
            : 1e3;

        uint256 rawFeeCollateral = (getPositionSizeCollateralBasis(
            _collateralIndex,
            _pairIndex,
            _positionSizeCollateral,
            feeRateMultiplier
        ) * feeRateP) /
            ConstantsUtils.P_10 /
            100 /
            1e3;

        // Fee tier is applied on min fee, but counter trade fee rate multiplier doesn't impact min fee
>>>     return _getMultiCollatDiamond().calculateFeeAmount(_trader, rawFeeCollateral);
    }
```

다음은 함수 목록입니다.

`executeManualNegativePnlRealizationCallback` :

```solidity
    function executeManualNegativePnlRealizationCallback(
        ITradingStorage.PendingOrder memory _order,
        ITradingCallbacks.AggregatorAnswer memory _a
    ) external {
        ITradingStorage.Trade memory trade = _getTrade(_order.trade.user, _order.trade.index);

        if (!trade.isOpen) return;

        (ITradingCommonUtils.TradePriceImpact memory priceImpact, ) = TradingCommonUtils.getTradeClosingPriceImpact(
            ITradingCommonUtils.TradePriceImpactInput(
                trade,
                _a.current,
                TradingCommonUtils.getPositionSizeCollateral(trade.collateralAmount, trade.leverage),
                _a.current,
                false // don't use cumulative volume price impact
            )
        );
// ..
}
```

`closeTradeMarketCallback` :

```solidity
    function closeTradeMarketCallback(
        ITradingCallbacks.AggregatorAnswer memory _a
    ) external tradingActivatedOrCloseOnly {
        ITradingStorage.PendingOrder memory o = _getPendingOrder(_a.orderId);

        _validatePendingOrderOpen(o);

        ITradingStorage.Trade memory t = _getTrade(o.trade.user, o.trade.index);
        ITradingStorage.TradeInfo memory i = _getTradeInfo(o.trade.user, o.trade.index);

        (ITradingCommonUtils.TradePriceImpact memory priceImpact, ) = TradingCommonUtils.getTradeClosingPriceImpact(
            ITradingCommonUtils.TradePriceImpactInput(
                t,
                _a.current,
                TradingCommonUtils.getPositionSizeCollateral(t.collateralAmount, t.leverage),
                _a.current,
                true
            )
        );
// ...
}
```

`executeTriggerCloseOrderCallback => `validateTriggerCloseOrderCallback` :

```solidity
    function validateTriggerCloseOrderCallback(
        ITradingStorage.Id memory _tradeId,
        ITradingStorage.PendingOrderType _orderType,
        uint64 _open,
        uint64 _high,
        uint64 _low,
        uint64 _currentPairPrice
    ) public view returns (ITradingStorage.Trade memory t, ITradingCallbacks.Values memory v) {
       // ...
        // Return early if trade is not open
        if (v.cancelReason != ITradingCallbacks.CancelReason.NONE) return (t, v);
        v.liqPrice = TradingCommonUtils.getTradeLiquidationPrice(t, _currentPairPrice);
        uint256 triggerPrice = _orderType == ITradingStorage.PendingOrderType.TP_CLOSE
            ? t.tp
            : (_orderType == ITradingStorage.PendingOrderType.SL_CLOSE ? t.sl : v.liqPrice);

        v.exactExecution = triggerPrice > 0 && _low <= triggerPrice && _high >= triggerPrice;
        v.executionPriceRaw = v.exactExecution ? triggerPrice : _open;

        // Apply closing spread and price impact for TPs and SLs, not liquidations (because trade value is 0 already)
        if (_orderType != ITradingStorage.PendingOrderType.LIQ_CLOSE) {
>>>         (v.priceImpact, ) = TradingCommonUtils.getTradeClosingPriceImpact(
                ITradingCommonUtils.TradePriceImpactInput(
                    t,
                    v.executionPriceRaw,
                    TradingCommonUtils.getPositionSizeCollateral(t.collateralAmount, t.leverage),
                    _currentPairPrice,
                    true
                )
            );
            v.executionPrice = v.priceImpact.priceAfterImpact;
        } else {
            v.executionPrice = v.executionPriceRaw;
        }

        // ...
    }
```

**권장 사항**

`updateFeeTierPoints` 호출을 추가하거나 작업 시작 부분으로 이동하는 것을 고려하십시오.

# [L-06] Ape 체인의 MillisecondsPerBlock 값이 오래됨 (MillisecondsPerBlock value for Ape chain is outdated)

`ChainUtils.convertBlocksToSeconds` 함수에서 Ape 체인에 대해 블록당 1000밀리초(1초)의 하드코딩된 값이 사용됩니다. 그러나 현재 네트워크 지표에 따르면 Ape 체인의 평균 블록 시간은 1초가 아니라 블록당 약 1.5초입니다.

```solidity
    function convertBlocksToSeconds(uint256 _blocks) internal view returns (uint256) {
        uint256 millisecondsPerBlock;
        ...
        } else if (block.chainid == APECHAIN_MAINNET) {
            millisecondsPerBlock = 1000; // @audit 1500 miliseconds
        ...

        return Math.mulDiv(_blocks, millisecondsPerBlock, 1000, Math.Rounding.Up);
    }
```

현재 네트워크의 평균 블록 시간과 일치하도록 Ape 체인의 `millisecondsPerBlock` 값을 1500밀리초로 업데이트하는 것이 좋습니다.

참조: https://apescan.io/chart/blocktime

# [L-07] 수수료 한도 매개변수를 업데이트하기 전에 차입 수수료가 적립되지 않음 (Borrowing fees not accrued before updating fee per block cap parameters)

관리자가 `setBorrowingFeePerBlockCap` 또는 `setBorrowingPairFeePerBlockCapArray`를 통해 전역 또는 쌍별 차입 수수료 한도 매개변수를 업데이트할 때, 프로토콜은 변경 사항을 적용하기 전에 이전 매개변수를 기반으로 지출 차입 수수료를 적립하지 못합니다.

```solidity
    function setBorrowingFeePerBlockCap(IBorrowingFees.BorrowingFeePerBlockCap memory _feePerBlockCap) external {
        _validateBorrowingFeePerBlockCap(_feePerBlockCap);

        _getStorage().feePerBlockCap = _feePerBlockCap; // @audit missing fee accrual before update
        ...
    }

    function setBorrowingPairFeePerBlockCapArray(
        uint8 _collateralIndex,
        uint16[] calldata _indices,
        IBorrowingFees.BorrowingFeePerBlockCap[] calldata _values
    ) external validCollateralIndex(_collateralIndex) {
            ...
        for (uint256 i; i < len; ++i) {
            IBorrowingFees.BorrowingFeePerBlockCap memory value = _values[i];
            _validateBorrowingFeePerBlockCap(value);

            uint16 pairIndex = _indices[i];
            _getStorage().pairFeePerBlockCaps[_collateralIndex][pairIndex] = value; // @audit missing fee accrual before update

            ...
        }
    }
```

이 문제는 새 매개변수가 이 기간에 소급 적용되므로 마지막 적립부터 매개변수 업데이트까지의 기간 동안 잘못된 수수료 계산을 유발합니다. 트레이더는 새 한도 매개변수가 이전 매개변수보다 높은지 낮은지에 따라 차입 수수료가 과다 청구되거나 과소 청구될 수 있습니다.

수수료 한도 매개변수를 업데이트하기 전에 보류 중인 차입 수수료를 적립하여 과거 수수료가 올바르게 계산되도록 하십시오.

# [L-08] 음수 PnL이 일찍 실현되면 소각량이 적음 (Less burn amount if negative PnL is realized early)

`executeManualNegativePnlRealizationCallback` 함수를 통해 음수 PnL이 수동으로 실현되면 손실은 GToken 금고로 보내지지만 최종적인 것으로 간주되지 않으므로 소각되지 않습니다.

```solidity
    function executeManualNegativePnlRealizationCallback(
        ITradingStorage.PendingOrder memory _order,
        ITradingCallbacks.AggregatorAnswer memory _a
    ) external {
            ...
                TradingCommonUtils.getGToken(trade.collateralIndex).receiveAssets(
                    negativePnlToRealizeCollateral,
                    trade.user,
                    false // don't burn unrealized pnl since it's not final
                );
            ...
    }
```

그러나 거래가 결국 종료될 때 프로토콜은 이전에 실현된(이 시점에서 확정된) 음수 PnL을 설명하지 않습니다. 수동으로 실현된 음수 PnL이 이미 `availableCollateralInDiamond`를 줄였기 때문에 남은 음수 PnL(있는 경우)만 소각 플래그가 true로 설정된 상태로 GToken 금고로 전송됩니다.

```solidity
        function handleTradeValueTransfer(
        ITradingStorage.Trade memory _trade,
        int256 _collateralSentToTrader,
        int256 _availableCollateralInDiamond
    ) external {
            ...
        } else if (_collateralSentToTrader < _availableCollateralInDiamond) {
            // Send loss to gToken
            getGToken(_trade.collateralIndex).receiveAssets(
                uint256(_availableCollateralInDiamond - _collateralSentToTrader),
                _trade.user,
                true // any amount sent to vault at this point is negative PnL, funding fees already sent with _burn = false
            );
        }
    }
```

이로 인해 두 시나리오 간에 소각량에 불일치가 발생합니다.

- 수동 음수 PnL 실현 시:

  - X1 금액은 GToken으로 전송되지만 수동 실현을 통해 소각되지 않습니다.
  - 나중에 거래가 종료될 때 X2 금액이 GToken으로 전송되고 소각됩니다.
  - 총 소각: X2.

- 수동 음수 PnL 실현 없음 시:
  - 거래는 전체 금액(X1 + X2)이 GToken으로 전송되고 소각되면서 종료됩니다.
  - 총 소각: X1 + X2.

이 문제는 소각 가능한 담보 금액을 줄여 프로토콜의 토큰노믹스를 약화시킵니다.

거래가 종료될 때 프로토콜은 소각 계산에서 이전에 실현된 음수 PnL을 고려해야 합니다.

# [L-09] 포지션 크기 검증이 수수료 전 금액을 사용함 (Position size validation uses pre-fee amounts)

프로토콜은 수수료가 공제된 실제 포지션 크기(`totalPositionSizeFeeP`)가 아닌 수수료 전 금액을 사용하여 포지션 크기를 잘못 검증합니다. 이는 두 가지 주요 영역에 영향을 미칩니다.

**1. 카운터 거래 담보 반환:**

`exceedingPositionSizeCollateral`이 너무 높습니다. `_positionSizeCollateral`이 수수료 전 금액을 나타내기 때문입니다. 이로 인해 카운터 거래 사용자에게 의도한 것보다 더 많은 담보가 반환됩니다.

```solidity
    function validateCounterTrade(
        ITradingStorage.Trade memory _trade,
        uint256 _positionSizeCollateral
    ) external view returns (bool isValidated, uint256 exceedingPositionSizeCollateral) {
        ...
        if (_positionSizeCollateral > maxPositionSizeCollateral)
            exceedingPositionSizeCollateral = _positionSizeCollateral - maxPositionSizeCollateral; // @audit Pre-fee amount

        return (true, exceedingPositionSizeCollateral);
    }
```

이는 카운터 거래 사용자가 실제 포지션 크기를 기준으로 해야 하는 것보다 더 많은 담보를 돌려받음을 의미합니다.

**2. 노출 한도 검증:**

노출 한도 확인은 수수료 전 포지션 크기를 사용하므로 수수료 후 포지션이 실제로 한도 내에 있을 때 유효한 거래가 잘못 거부될 수 있습니다.

예를 들어, 개설 수수료가 담보에서 공제되면 `newSkewCollateral`은 `maxSkewCollateral`보다 작습니다. 그러나 개설 수수료가 계산되지 않으므로 개설 거래 트랜잭션이 되돌려집니다.

```solidity
    function _openTradePrep(
        ITradingStorage.Trade memory _trade,
        uint256 _executionPriceRaw,
        uint256 _maxSlippageP
    ) internal view returns (ITradingCallbacks.Values memory v) {
        ...
        v.cancelReason = (
                ...
                    : !TradingCommonUtils.isWithinExposureLimits(
                        _trade.collateralIndex,
                        _trade.pairIndex,
                        _trade.long,
                        positionSizeCollateral // @audit Pre-fee amount
                    )
                ...
    }
```

이로 인해 부풀려진 포지션 크기를 사용하는 노출 한도 확인으로 인해 유효한 거래가 거부될 수 있습니다.

한도에 대해 검증하기 전에 수수료 후 포지션 크기를 계산하십시오.

# [L-10] `ExecuteManualNegativePnlRealizationCallback`이 0 전송 시 되돌려질 수 있음 (`ExecuteManualNegativePnlRealizationCallback` could revert on 0 transfer)

`executeManualNegativePnlRealizationCallback` 내부에서 `realizedNegativePnlToCancelCollateral`을 계산하고 금고에서 `GNSMultiCollatDiamond` 계약으로 자산을 전송하려고 시도합니다.

```solidity
    function executeManualNegativePnlRealizationCallback(
        ITradingStorage.PendingOrder memory _order,
        ITradingCallbacks.AggregatorAnswer memory _a
    ) external {
        ITradingStorage.Trade memory trade = _getTrade(_order.trade.user, _order.trade.index);

        if (trade.isOpen) {
            // ...

            if (unrealizedNegativePnlCollateral > existingManuallyRealizedNegativePnlCollateral) {
                // ...

                newManuallyRealizedNegativePnlCollateral += negativePnlToRealizeCollateral;
            } else {
>>>             uint256 realizedNegativePnlToCancelCollateral = existingManuallyRealizedNegativePnlCollateral -
                    unrealizedNegativePnlCollateral;

>>>             TradingCommonUtils.receiveCollateralFromVault(
                    trade.collateralIndex,
                    realizedNegativePnlToCancelCollateral
                );

                newManuallyRealizedNegativePnlCollateral -= realizedNegativePnlToCancelCollateral;
            }
```

그러나 `realizedNegativePnlToCancelCollateral`이 0인 시나리오는 설명하지 않습니다. 0 전송 시 담보 전송이 되돌려지면 작업이 실패합니다. `realizedNegativePnlToCancelCollateral`이 0보다 클 때만 `receiveCollateralFromVault`를 트리거하십시오.

# [L-11] 되돌려진 요청 ID가 프로토콜에 대해 보류 상태로 유지됨 (Reverted request IDs stay pending for the protocol)

`ChainlinkClientUtils.validateChainlinkCallback` 함수는 프로토콜 저장소에서 보류 중인 요청 ID를 삭제합니다. 즉, 유효하지 않게 만듭니다. 그러나 유효하지 않은 캔들로 인해 되돌려지는 경우 프로토콜 저장소는 이전 상태로 돌아갑니다. 따라서 요청 ID는 보류 상태로 유지됩니다. Chainlink 프로토콜에는 유사한 유효성 검사가 있지만 오류는 무시합니다. 유효하지 않은 캔들의 경우 되돌리기(revert)를 피하고 대신 조기 반환(early return)을 사용하는 것을 고려하십시오.

```solidity
    function fulfill(bytes32 _requestId, uint256 _priceData) external {
>>      ChainlinkClientUtils.validateChainlinkCallback(_requestId);
        
        // ...

        if (usedInMedian) {
            // ...
            if (
                newAnswer.current == 0 ||
                (order.isLookback &&
                    (newAnswer.high < newAnswer.open ||
                        newAnswer.low > newAnswer.open ||
                        newAnswer.open == 0 ||
                        newAnswer.low == 0))
>>          ) revert IPriceAggregatorUtils.InvalidCandle();
```

# [L-12] 반올림 오류로 인해 최소 포지션 크기가 예상보다 작음 (Rounding errors causes minimal position size less than expected)

`TradingCommonUtils.getPositionSizeCollateralBasis` 함수는 `minPositionSizeCollateral` 값을 `_feeRateMultiplier`로 나누어 조정된 `adjustedMinPositionSizeCollateral`과 `_positionSizeCollateral`을 비교하여 수수료를 부과할 때 사용할 최소 포지션 크기를 결정합니다. 문제는 반환 값에 `_feeRateMultiplier`가 다시 곱해진다는 것입니다. 따라서 `minPositionSizeCollateral`을 `_feeRateMultiplier`로 나눈 다음 곱할 때 불필요한 반올림 손실이 발생할 수 있습니다.

```solidity
    function getPositionSizeCollateralBasis(
        uint8 _collateralIndex,
        uint256 _pairIndex,
        uint256 _positionSizeCollateral,
        uint256 _feeRateMultiplier
    ) public view returns (uint256) {
        uint256 minPositionSizeCollateral = getMinPositionSizeCollateral(_collateralIndex, _pairIndex);
>>      uint256 adjustedMinPositionSizeCollateral = (minPositionSizeCollateral * 1e3) / _feeRateMultiplier;
        return
            _positionSizeCollateral > adjustedMinPositionSizeCollateral
                ? _positionSizeCollateral
                : adjustedMinPositionSizeCollateral;
    }
```

`_feeRateMultiplier`에 의한 나눗셈을 피하는 것을 고려하십시오.
