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

_검토 커밋 해시_ - [0ffc82581ea1e4c002435fa3bb328080cced4179](https://github.com/GainsNetwork-org/gTrade-contracts/tree/0ffc82581ea1e4c002435fa3bb328080cced4179)

_수정 검토 커밋 해시_ - [fc44d22a485e6be9eafa70f0967449c8f21d60ea](https://github.com/GainsNetwork-org/gTrade-contracts/tree/fc44d22a485e6be9eafa70f0967449c8f21d60ea)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `BorrowingFeesUtils`
- `ConstantsUtils`
- `PairsStorageUtils`
- `PriceAggregatorUtils`
- `PriceImpactUtils`
- `ReferralsUtils`
- `TradingCallbackUtils`
- `TradingCommonUtils`
- `TradingInteractionsUtils`
- `TradingStorageUtils`
- `UpdateLeverageLifecycles`
- `DecreasePositionUtils`
- `IncreasePositionUtils`
- `UpdatePositionSizeLifecycles`

# 발견 사항 (Findings)

# [C-01] 레버리지 업데이트를 통한 포지션 크기 감소를 악용하여 다이아몬드(Diamond) 자산을 훔칠 수 있음 (Decreasing position size via leverage update can be abused to steal from diamond)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

트레이더는 레버리지 업데이트를 사용하여 포지션 크기를 줄일 수 있으며, 그러면 포지션 크기 델타를 기반으로 부분적인 손익이 실현됩니다.

그러나 이익이 발생한 경우 `handleTradePnl()`은 종료 수수료를 금고 대신 트레이더에게 잘못 보냅니다. 이를 통해 트레이더는 다이아몬드에서 자금을 훔쳐 예상보다 더 많은 이익을 얻을 수 있습니다. 시간이 지남에 따라 종료 수수료가 다이아몬드에 수신되지 않고 배포됨에 따라 다이아몬드는 서서히 고갈될 것입니다.

트레이더가 다음과 같은 포지션을 가지고 있다고 가정해 보겠습니다.

- 기존 레버리지 = 10
- 기존 담보 = 100 DAI
- 기존 포지션 크기 = 1000 DAI
- 기존 이익 500 DAI

그리고 트레이더가 레버리지 델타만큼 포지션 크기를 줄입니다.

- 레버리지 델타 = 5
- 포지션 크기 델타 = 500 DAI
- 이익 가치 델타 = 500 \* 500/1000 = 250 DAI
- 거래 가치 델타 = 500 + 250 = 750 DAI

수수료 공제 후,

- 차입 수수료 = 1
- 스테이킹/금고 수수료 = 5

다음과 같은 값을 얻게 됩니다.

- `_availableCollateralInDiamond` = 0 - 5 = -5
- `collateralSentToTrader` = 0 + 250 - 1 - 5 = 244

이제 `handleTradePnl()`이 이를 처리할 때,

- 금고는 트레이더에게 `collateralSentToTrader - collateralLeftInStorage` = 244 - (-5) = 249 DAI를 보냅니다.
- 종료 수수료 5 DAI는 다이아몬드로 보내지는 대신 금고에서 트레이더에게 잘못 보내집니다.

```Solidity
    function handleTradePnl(
        ITradingStorage.Trade memory _trade,
        int256 _collateralSentToTrader,
        int256 _availableCollateralInDiamond,
        uint256 _borrowingFeeCollateral
    ) external {
        if (_collateralSentToTrader > _availableCollateralInDiamond) {
            //@audit this will incorrectly send closing fees to trader when _availableCollateralInDiamond is negative
            getGToken(_trade.collateralIndex).sendAssets(
                uint256(_collateralSentToTrader - _availableCollateralInDiamond),
                _trade.user
            );

            if (_availableCollateralInDiamond > 0)
                transferCollateralTo(_trade.collateralIndex, _trade.user, uint256(_availableCollateralInDiamond));
        } else {
            getGToken(_trade.collateralIndex).receiveAssets(
                uint256(_availableCollateralInDiamond - _collateralSentToTrader),
                _trade.user
            );
            if (_collateralSentToTrader > 0)
                transferCollateralTo(_trade.collateralIndex, _trade.user, uint256(_collateralSentToTrader));
        }

        emit ITradingCallbacksUtils.BorrowingFeeCharged(_trade.user, _trade.collateralIndex, _borrowingFeeCollateral);
    }
```

## 권장 사항

레버리지로 포지션을 줄일 때 종료 수수료를 트레이더에게 보내는 대신 금고에서 다이아몬드로 보내십시오.

# [H-01] `FeeTierPoints`가 잘못하여 두 번 증가함 (`FeeTierPoints` is incorrectly increased twice)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

포지션 크기 증가를 실행하면 `TradingCommonUtils.updateFeeTierPoints()`를 호출하여 포지션 크기 델타만큼 트레이더 수수료 티어 포인트를 업데이트합니다.

그러나 트레이더 수수료 티어 포인트는 선행 호출인 `TradingCommonUtils.processOpeningFees()`에서 이미 업데이트되었습니다.

즉, 트레이더 수수료 티어 포인트가 잘못하여 두 번 업데이트되어 트레이더가 두 배의 포인트를 받게 됩니다. 트레이더는 이를 악용하여 더 많은 포인트를 얻고 수수료 감면을 더 빨리 받을 수 있으며, 이로 인해 프로토콜, 금고 사용자 및 스테이커는 수수료 수익 손실을 입게 됩니다.

```Solidity
    function executeIncreasePositionSizeMarket(
        ITradingStorage.PendingOrder memory _order,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) external {
            ...
            // 5. If passes further validation, execute callback
            if (cancelReason == ITradingCallbacks.CancelReason.NONE) {
                // 5.1 Update trade collateral / leverage / open price in storage, and reset trade borrowing fees
                IncreasePositionSizeUtils.updateTradeSuccess(existingTrade, values);


                // 5.2 Distribute opening fees
                //@audit this will update trader fee tier points for the increase of position size
                TradingCommonUtils.processOpeningFees(
                    existingTrade,
                    values.positionSizeCollateralDelta,
                    _order.orderType
                );

                // 5.3 Store trader fee tier points for position size delta
                //@audit this will incorrectly update trader fee tier points again
                TradingCommonUtils.updateFeeTierPoints(
                    existingTrade.collateralIndex,
                    existingTrade.user,
                    existingTrade.pairIndex,
                    values.positionSizeCollateralDelta
                );
            }
        }

        ;;;
    }
```

## 권장 사항

수수료 티어 포인트의 중복 업데이트를 제거하여 이를 해결할 수 있습니다.

# [H-02] `requestIncreasePositionSize` 내부에서 `newLeverage`가 잘못 계산됨 (`newLeverage` wrongly calculated inside `requestIncreasePositionSize`)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

사용자가 `increasePositionSize`를 호출하여 포지션 크기 증가를 요청하면 결국 요청을 검증하기 위해 `IncreasePositionSizeUtils.validateRequest`가 트리거됩니다. 그러나 `newLeverage`를 계산할 때 `(existingPositionSizeCollateral + positionSizeCollateralDelta) * 1e3 / newCollateralAmount` 대신 `(existingPositionSizeCollateral + positionSizeCollateralDelta * 1e3) / newCollateralAmount`로 잘못 계산하여 `newLeverage`가 실제보다 낮아집니다.

```solidity
    function validateRequest(
        ITradingStorage.Trade memory _trade,
        IUpdatePositionSizeUtils.IncreasePositionSizeInput memory _input
    ) internal view returns (uint256 positionSizeCollateralDelta) {
        // ....
        uint256 newCollateralAmount = _trade.collateralAmount + _input.collateralDelta;
        uint256 newLeverage = isLeverageUpdate
            ? _trade.leverage + _input.leverageDelta
>>>         : (existingPositionSizeCollateral + positionSizeCollateralDelta * 1e3) / newCollateralAmount;
        {
            uint256 borrowingFeeCollateral = TradingCommonUtils.getTradeBorrowingFeeCollateral(_trade);
            uint256 openingFeesCollateral = ((_getMultiCollatDiamond().pairOpenFeeP(_trade.pairIndex) *
                2 +
                _getMultiCollatDiamond().pairTriggerOrderFeeP(_trade.pairIndex)) *
                TradingCommonUtils.getPositionSizeCollateralBasis(
                    _trade.collateralIndex,
                    _trade.pairIndex,
                    positionSizeCollateralDelta
                )) /
                ConstantsUtils.P_10 /
                100;

            uint256 newPositionSizeCollateral = existingPositionSizeCollateral +
                positionSizeCollateralDelta -
                ((borrowingFeeCollateral + openingFeesCollateral) * newLeverage) /
                1e3;

            if (newPositionSizeCollateral <= existingPositionSizeCollateral)
                revert IUpdatePositionSizeUtils.NewPositionSizeSmaller();
        }
```

`newLeverage`는 `newPositionSizeCollateral`을 계산하는 데 사용되며, 이는 `existingPositionSizeCollateral`과 비교 확인하는 데 사용됩니다. `newLeverage`를 잘못 계산하면 `newPositionSizeCollateral`이 실제보다 높아져 검증 확인을 잘못 통과할 수 있습니다. 또한 더 낮은 `newLeverage` 금액을 사용하여 `isWithinExposureLimits`를 계산하고 확인하므로 잠재적으로 `exposureLimits`를 우회할 수 있습니다.

## 권장 사항

`newLeverage` 계산을 업데이트하십시오.

```diff
        uint256 newLeverage = isLeverageUpdate
            ? _trade.leverage + _input.leverageDelta
-            : (existingPositionSizeCollateral + positionSizeCollateralDelta * 1e3) / newCollateralAmount;
+            : (existingPositionSizeCollateral + positionSizeCollateralDelta) * 1e3 / newCollateralAmount;
```

# [H-03] 종료 및 트리거 수수료 과다 청구 (Overcharging of closing and trigger fees)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

현재 종료 및 트리거 수수료 계산은 모든 `non-MARKET_CLOSE` 주문에 고정 `5%` 수수료를 적용합니다. 이 수수료는 `LIQ_CLOSE` 주문에만 적용되어야 합니다. `TP_CLOSE` 및 `SL_CLOSE` 주문의 경우 계산은 각각 쌍(pair) 종료 수수료 비율과 쌍 트리거 주문 수수료 비율을 사용해야 합니다. 결과적으로 `TP_CLOSE` 및 `SL_CLOSE` 주문에 대해 과다 청구가 발생하고 있습니다.

```solidity
    function processClosingFees(
        ITradingStorage.Trade memory _trade,
        uint256 _positionSizeCollateral,
        ITradingStorage.PendingOrderType _orderType
    ) external returns (ITradingCallbacks.Values memory values) {
        // 1. Calculate closing fees
        values.positionSizeCollateral = getPositionSizeCollateralBasis(
            _trade.collateralIndex,
            _trade.pairIndex,
            _positionSizeCollateral
        ); // Charge fees on max(min position size, trade position size)

        values.closingFeeCollateral = _orderType == ITradingStorage.PendingOrderType.MARKET_CLOSE
            ? (values.positionSizeCollateral * _getMultiCollatDiamond().pairCloseFeeP(_trade.pairIndex)) /
                100 /
                ConstantsUtils.P_10
            : (_trade.collateralAmount * 5) / 100; // @audit charge fixed 5% for non-MARKET_CLOSE

        values.triggerFeeCollateral = _orderType == ITradingStorage.PendingOrderType.MARKET_CLOSE
            ? (values.positionSizeCollateral * _getMultiCollatDiamond().pairTriggerOrderFeeP(_trade.pairIndex)) /
                100 /
                ConstantsUtils.P_10
            : values.closingFeeCollateral; // @audit charge fixed 5% for non-MARKET_CLOSE
        ...
    }
```

## 권장 사항

`TP_CLOSE` 및 `SL_CLOSE` 주문에 대해 쌍 종료 수수료 비율과 쌍 트리거 주문 수수료 비율을 구체적으로 적용하도록 수수료 계산 로직을 수정하고, `LIQ_CLOSE` 주문에 대해서는 5% 수수료를 유지하십시오.

# [H-04] 가격 영향을 제대로 제거할 수 없음 (Price impact cannot properly be removed)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

사용자의 포지션 크기 증가 또는 레버리지 업데이트 요청이 성공적으로 실행되면 `updateTradePosition`을 호출하고 새로운 `newCollateralAmount` 및 `newLeverage`를 제공하여 사용자의 거래 포지션을 업데이트합니다.

```solidity
    function updateTradeSuccess(
        ITradingStorage.Trade memory _existingTrade,
        IUpdatePositionSizeUtils.IncreasePositionSizeValues memory _values
    ) internal {
        // 1. Charge borrowing fees and opening fees from trade collateral
        _values.newCollateralAmount -= _values.borrowingFeeCollateral + _values.openingFeesCollateral;

        // 2. Update trade in storage
>>>     _getMultiCollatDiamond().updateTradePosition(
            ITradingStorage.Id(_existingTrade.user, _existingTrade.index),
            uint120(_values.newCollateralAmount),
            uint24(_values.newLeverage),
            uint64(_values.newOpenPrice)
        );

        // 3. Reset trade borrowing fees to zero
        _getMultiCollatDiamond().resetTradeBorrowingFees(
            _existingTrade.collateralIndex,
            _existingTrade.user,
            _existingTrade.pairIndex,
            _existingTrade.index,
            _existingTrade.long
        );
    }
```

그리고 `updateTradePosition` 내부에서는 거래 데이터를 업데이트하고 `TradingCommonUtils.handleOiDelta`를 트리거하여 가격 영향에 새로운 포지션 크기 델타를 추가합니다.

```solidity
    function handleOiDelta(
        ITradingStorage.Trade memory _trade,
        ITradingStorage.TradeInfo memory _tradeInfo,
        uint256 _newPositionSizeCollateral
    ) external {
        uint256 existingPositionSizeCollateral = getPositionSizeCollateral(_trade.collateralAmount, _trade.leverage);

        if (_newPositionSizeCollateral > existingPositionSizeCollateral) {
>>>         addOiCollateral(_trade, _newPositionSizeCollateral - existingPositionSizeCollateral);
        } else if (_newPositionSizeCollateral < existingPositionSizeCollateral) {
            removeOiCollateral(_trade, _tradeInfo, existingPositionSizeCollateral - _newPositionSizeCollateral);
        }
    }
```

```solidity
    function addPriceImpactOpenInterest(uint128 _openInterestUsd, uint256 _pairIndex, bool _long) internal {
        IPriceImpact.PriceImpactStorage storage priceImpactStorage = _getStorage();
        IPriceImpact.OiWindowsSettings storage settings = priceImpactStorage.oiWindowsSettings;

>>>     uint256 currentWindowId = _getCurrentWindowId(settings);
        IPriceImpact.PairOi storage pairOi = priceImpactStorage.windows[settings.windowsDuration][_pairIndex][
            currentWindowId
        ];

        if (_long) {
            pairOi.oiLongUsd += _openInterestUsd;
        } else {
            pairOi.oiShortUsd += _openInterestUsd;
        }

        emit IPriceImpactUtils.PriceImpactOpenInterestAdded(
            IPriceImpact.OiWindowUpdate(settings.windowsDuration, _pairIndex, currentWindowId, _long, _openInterestUsd)
        );
    }
```

가격 영향이 `currentWindowId`에 추가되는 것을 볼 수 있으며, 거래가 종료되고 거래 가격 영향이 제거되면 `_tradeInfo.lastOiUpdateTs`에서의 가격 영향만 제거합니다.

```solidity
    function removeOiCollateral(
        ITradingStorage.Trade memory _trade,
        ITradingStorage.TradeInfo memory _tradeInfo,
        uint256 _positionSizeCollateral
    ) public {
        _getMultiCollatDiamond().handleTradeBorrowingCallback(
            _trade.collateralIndex,
            _trade.user,
            _trade.pairIndex,
            _trade.index,
            _positionSizeCollateral,
            false,
            _trade.long
        );
        // @audit - is this always correct?
        _getMultiCollatDiamond().removePriceImpactOpenInterest(
            // when removing OI we need to use the collateral/usd price when the OI was added
            convertCollateralToUsd(
                _positionSizeCollateral,
                _getMultiCollatDiamond().getCollateral(_trade.collateralIndex).precisionDelta,
                _tradeInfo.collateralPriceUsd
            ),
            _trade.pairIndex,
            _trade.long,
            _tradeInfo.lastOiUpdateTs // @audit - this will only remove price impact from initial collateral size
        );
    }
```

이로 인해 가격 영향이 적절하게 제거되지 않고 실제보다 더 높은 가격 영향을 초래하게 됩니다.

## 권장 사항

가격 영향을 동일한 `_tradeInfo.lastOiUpdateTs`로 업데이트하거나 포지션 크기가 증가할 때 윈도우 ID를 별도로 추적할 수 있습니다.

# [M-01] 새 청산 가격의 잘못된 계산 (Incorrect calculation of new liquidation price)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`IncreasePositionSizeUtils.prepareCallbackValues`에서 `newLiqPrice`를 계산할 때 함수는 `newCollateralAmount` 및 `newLeverage`를 사용합니다.

```solidity
   function prepareCallbackValues(
        ITradingStorage.Trade memory _existingTrade,
        ITradingStorage.Trade memory _partialTrade,
        ITradingCallbacks.AggregatorAnswer memory _answer
    ) internal view returns (IUpdatePositionSizeUtils.IncreasePositionSizeValues memory values) {
        ...
        values.newLiqPrice = _getMultiCollatDiamond().getTradeLiquidationPrice(
            IBorrowingFees.LiqPriceInput(
                _existingTrade.collateralIndex,
                _existingTrade.user,
                _existingTrade.pairIndex,
                _existingTrade.index,
                uint64(values.newOpenPrice),
                _existingTrade.long,
                values.newCollateralAmount, // @audit use newCollateralAmount & newLeverage => borrowing fee is overstated
                values.newLeverage
            )
        );
        ...
    }
```

청산 가격을 계산하려면 현재 차입 수수료 금액을 고려해야 합니다. `newCollateralAmount` 및 `newLeverage`를 입력으로 사용하면 이 값이 현재 담보 및 레버리지보다 높을 경우 차입 수수료가 과대계상될 수 있습니다. 이로 인해 잘못된 새 청산 가격이 생성되어 롱 포지션의 경우 더 높고 숏 포지션의 경우 더 낮아집니다. 이 잘못된 계산은 청산 가격이 도달한 것으로 잘못 계산되어 콜백을 검증할 때 예상치 못한 되돌림(revert)을 유발할 수 있습니다.

```solidity
    function getTradeBorrowingFee(
        IBorrowingFees.BorrowingFeeInput memory _input
    ) internal view returns (uint256 feeAmountCollateral) {
        ...

        feeAmountCollateral = (_input.collateral * _input.leverage * borrowingFeeP) / 1e3 / ConstantsUtils.P_10 / 100; // collateral precision
    }
```

## 권장 사항

`newCollateralAmount` 및 `newLeverage`로 차입 수수료를 다시 계산하는 대신 현재 차입 수수료를 전달하여 새 청산 가격을 계산하십시오.

# [M-02] 기존 미결제 약정의 이중 계산 (Double counting of existing open interest)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

포지션 크기 증가 요청을 검증할 때 `validateRequest` 함수는 거래가 노출 한도 내에 있는지 확인합니다. 그러나 사용된 금액과 레버리지는 `collateralDelta` 및 `leverageDelta` 대신 `newCollateralAmount` 및 `newLeverage`입니다.

`newPositionSizeCollateral = existingPositionSizeCollateral + positionSizeCollateralDelta`입니다. 현재 미결제 약정(open interest) 수준은 이미 `existingPositionSizeCollateral`을 고려하고 있습니다. 따라서 포지션 크기를 늘린 후 거래가 노출 한도 내에 있는지 확인하려면 추가 금액인 `positionSizeCollateralDelta`가 노출 한도 내에 있는지 확인해야 합니다.

검사의 `newCollateralAmount` 및 `newLeverage`를 사용하는 경우, 현재 미결제 약정 수준이 이중으로 계산되기 때문에 추가 금액이 한도 내에 있더라도 거래가 노출 한도를 초과하는 것으로 간주될 수 있습니다.

```solidity
    function validateRequest(
        ITradingStorage.Trade memory _trade,
        IUpdatePositionSizeUtils.IncreasePositionSizeInput memory _input
    ) internal view returns (uint256 positionSizeCollateralDelta) {
        ...

        // 4. Make sure trade stays within exposure limits
        if (
            !TradingCommonUtils.isWithinExposureLimits(
                _trade.collateralIndex,
                _trade.pairIndex,
                _trade.long,
                newCollateralAmount, // @audit already counted the existing collateral
                newLeverage
            )
        ) revert ITradingInteractionsUtils.AboveExposureLimits();
    }
```

동일한 문제가 `IncreasePositionSizeUtils.validateCallback`에서도 식별될 수 있습니다.

```solidity
    function validateCallback(
        ITradingStorage.Trade memory _existingTrade,
        IUpdatePositionSizeUtils.IncreasePositionSizeValues memory _values,
        ITradingCallbacks.AggregatorAnswer memory _answer,
        uint256 _expectedPrice,
        uint256 _maxSlippageP
    ) internal view returns (ITradingCallbacks.CancelReason cancelReason) {
        ...
            : !TradingCommonUtils.isWithinExposureLimits(
                _existingTrade.collateralIndex,
                _existingTrade.pairIndex,
                _existingTrade.long,
                _values.newCollateralAmount, // @audit already counted the existing collateral
                _values.newLeverage
            )
        ...
    }
```

## 권장 사항

거래가 노출 한도 내에 머무르는지 확인할 때 추가 포지션 크기만 고려하십시오. 다양한 시나리오를 처리하도록 코드를 조정하십시오.

- `collateralDelta` > 0이고 `leverageDelta` > 0이면 `collateralDelta` 및 `leverageDelta`를 사용하십시오.
- `collateralDelta` = 0이면 `collateralAmount` 및 `leverageDelta`를 사용하십시오.

# [M-03] 미결제 약정 계산이 올바르지 않음 (Open interest calculation is incorrect)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

미결제 약정(OI) 델타는 `OI를 추가할 때` `현재 collateral/USD` 가격을 사용하여 계산됩니다. 반대로 `OI를 제거할 때` 델타는 `OI가 추가되었을 때의 collateral/USD 가격`을 사용하여 계산됩니다. 이 방법은 주문이 한 번만 열리고 닫힐 경우 올바르게 작동합니다. 그러나 주문 포지션이 여러 번 증가하거나 감소하면 OI 계산이 올바르지 않게 됩니다.

예를 들어, 담보 가격이 $100일 때 포지션을 열고 가격이 $110일 때 포지션을 늘리면, 마감 시 제거될 OI는 $110 가격을 사용하여 잘못 계산됩니다.

```solidity
    function handleOiDelta(
        ITradingStorage.Trade memory _trade,
        ITradingStorage.TradeInfo memory _tradeInfo,
        uint256 _newPositionSizeCollateral
    ) external {
        uint256 existingPositionSizeCollateral = getPositionSizeCollateral(_trade.collateralAmount, _trade.leverage);

        if (_newPositionSizeCollateral > existingPositionSizeCollateral) {
            addOiCollateral(_trade, _newPositionSizeCollateral - existingPositionSizeCollateral); // @audit using current collateral/usd price
        } else if (_newPositionSizeCollateral < existingPositionSizeCollateral) {
            removeOiCollateral(_trade, _tradeInfo, existingPositionSizeCollateral - _newPositionSizeCollateral); // @audit using the collateral/usd price when the OI was added last time
        }
    }
```

## 권장 사항

포지션의 총 미결제 약정을 추적하고 포지션이 닫힐 때 최대 총 미결제 약정을 기준으로 미결제 약정을 조정하십시오.

# [M-04] `updateTradePosition()`이 TP/SL 거리를 제한하지 못함 (`updateTradePosition()` fails to limit TP/SL distance)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

포지션 크기를 늘리면 다른 시가를 가진 부분 거래로 인해 거래의 시가가 업데이트됩니다. 거래의 새로운 시가는 `updateTradePosition()`을 통해 업데이트됩니다.

그러나 `updateTradePosition()`은 시가를 변경함에도 불구하고 `_limitTpDistance()` 및 `_limitSlDistance()`를 사용하여 TP/SL을 조정하지 못합니다. 이로 인해 한도에 가까운 기존 TP/SL이 최대 PnL에 따른 TP/SL 한도를 잘못 초과하게 될 수 있습니다.

트레이더는 이를 악용하여 최대 이익 900%를 초과하는 TP를 설정할 수 있습니다.

```Solidity
    function updateTradePosition(
        ITradingStorage.Id memory _tradeId,
        uint120 _collateralAmount,
        uint24 _leverage,
        uint64 _openPrice
    ) external {
        ITradingStorage.TradingStorage storage s = _getStorage();
        ITradingStorage.Trade storage t = s.trades[_tradeId.user][_tradeId.index];
        ITradingStorage.TradeInfo storage i = s.tradeInfos[_tradeId.user][_tradeId.index];

        if (!t.isOpen) revert IGeneralErrors.DoesntExist();
        if (t.tradeType != ITradingStorage.TradeType.TRADE) revert IGeneralErrors.WrongTradeType();
        if (_collateralAmount * _leverage == 0) revert ITradingStorageUtils.TradePositionSizeZero();
        if (_openPrice == 0) revert ITradingStorageUtils.TradeOpenPriceZero();

        //@audit TODO
        TradingCommonUtils.handleOiDelta(
            t,
            i,
            TradingCommonUtils.getPositionSizeCollateral(_collateralAmount, _leverage)
        );

        t.collateralAmount = _collateralAmount;
        t.leverage = _leverage;
        t.openPrice = _openPrice;

        i.createdBlock = uint32(ChainUtils.getBlockNumber());

        emit ITradingStorageUtils.TradePositionUpdated(_tradeId, _collateralAmount, t.leverage, t.openPrice);
    }
```

## 권장 사항

TP/SL을 조정하도록 `updateTradePosition()`을 수정하고 `_limitTpDistance()` 및 `_limitSlDistance()`를 사용하십시오.

# [M-05] `removePriceImpactOpenInterest()`가 만료된 OI를 고려하지 못함 (`removePriceImpactOpenInterest()` fails to account for expired OI)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`removePriceImpactOpenInterest()`는 거래를 종료할 때 `expiredOiUsd`에서 실제 제거된 OI를 차감하여 필요 이상으로 OI를 제거하지 않도록 합니다.

그러나 부분적으로 포지션 크기를 줄일 때 OI를 제거할 때 `expiredOiUsd`를 고려하지 못합니다. 이로 인해 감소된 OI가 요구량보다 높아져 후속 거래에 대한 가격 영향이 낮아집니다.

다음과 같은 시나리오를 가정해 보겠습니다.

- 기존 포지션 크기 = 1000 (오래된 윈도우)
- 거래에 대한 활성 OI = 0 (오래되었으므로)

그리고 트레이더가 포지션 크기를 늘리면,

- 델타 포지션 크기 = 500
- 새 포지션 크기 = 1000 + 500 = 1500
- 거래에 대한 만료된 OI = 1000 (기존 포지션에서)
- 거래에 대한 활성 OI = 500

이제 최신 OI가 여전히 활성 상태일 때 트레이더가 포지션 크기를 줄이면,

- 델타 포지션 크기 = -700
- 새 포지션 크기 = 1500 - 700 = 800
- 거래에 대해 제거된 OI = 700 (활성 OI는 500에 불과하므로 이는 부정확함)
- 제거할 올바른 OI는 `min(deltaOiUsd, positionSizeUsd - expiredOiUsd)`여야 합니다.

```Solidity
    function removePriceImpactOpenInterest(
        address _trader,
        uint32 _index,
        bool _isPartial,
        uint128 _oiDeltaUsd
    ) internal {
        ...

        if (notOutdated) {
            IPriceImpact.PairOi storage pairOi = priceImpactStorage.windows[settings.windowsDuration][trade.pairIndex][
                addWindowId
            ];

            //@audit When isPartial == true, need to handle the case where _oiDeltaUsd > active OI
            // 3.1 If full close, deduct any OI that wasn't moved due to being already expired (partial add size)
            if (!_isPartial) _oiDeltaUsd -= priceImpactStorage.tradePriceImpactInfos[_trader][_index].expiredOiUsd;

            // 3.2 Remove OI from trade last oi updated window
            if (trade.long) {
                pairOi.oiLongUsd = _oiDeltaUsd < pairOi.oiLongUsd ? pairOi.oiLongUsd - _oiDeltaUsd : 0;
            } else {
                pairOi.oiShortUsd = _oiDeltaUsd < pairOi.oiShortUsd ? pairOi.oiShortUsd - _oiDeltaUsd : 0;
            }
        }

        ...
    }
```

## 권장 사항

제거할 올바른 OI는 `min(deltaOiUsd, positionSizeUsd - expiredOiUsd)`여야 합니다.

# [M-06] `addPriceImpactOpenInterest()`가 `expiredOiUsd`를 조정(scaling)하지 못함 (`addPriceImpactOpenInterest()` fails to scale `expiredOiUsd`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`addPriceImpactOpenInterest()`는 OI를 추가할 때 만료된 OI를 추적하여 거래 종료 시 필요 이상으로 OI를 줄이지 않도록 합니다.

그러나 현재 collateral/usd 가격으로 OI USD 값을 조정하지 못합니다. `tradeInfo.collateralPriceUsd`가 현재 collateral/usd 가격으로 업데이트됨에 따라, 만료된 OI가 마지막 업데이트된 collateral/usd 가격이 아닌 이전 collateral/usd 가격을 기준으로 하므로 OI 제거가 올바르지 않게 됩니다.

```Solidity
     function addPriceImpactOpenInterest(address _trader, uint32 _index, bool _isPartial, uint128 _oiDeltaUsd) internal {
       ...

        if (_isPartial) {
            uint256 existingOiWindowId = _getWindowId(tradeInfo.lastOiUpdateTs, settings);

            if (_isWindowPotentiallyActive(existingOiWindowId, currentWindowId)) {
                removePriceImpactOpenInterest(_trader, _index, false, tradePriceImpactInfo.lastOiDeltaUsd);

                openInterestUsdToAdd += uint128(
                    (tradePriceImpactInfo.lastOiDeltaUsd * currentCollateralPriceUsd) / tradeInfo.collateralPriceUsd
                );
            } else {
                //@audit the added expiredOiUsd should also be scaled with current collateral/usd price
                tradePriceImpactInfo.expiredOiUsd += tradePriceImpactInfo.lastOiDeltaUsd;
            }
        }
```

## 권장 사항

추가된 `expiredOiUsd`를 `(currentCollateralPriceUsd) / tradeInfo.collateralPriceUsd`로 조정하십시오.

# [L-01] 정밀도 손실을 방지하기 위해 `_trade.collateralAmount`를 직접 사용 (`_trade.collateralAmount` directly to prevent precision loss)

`DecreasePositionSizeUtils.validateRequest` 함수에서 레버리지가 업데이트된 경우 새 담보 금액을 계산할 때 다음 공식이 사용됩니다.

        ((TradingCommonUtils.getPositionSizeCollateral(_trade.collateralAmount, _trade.leverage) - positionSizeCollateralDelta) * 1e3) / (_trade.leverage - _input.leverageDelta)

그러나 이 공식의 결과는 사실상 `_trade.collateralAmount`이며, 계산 중 발생할 수 있는 정밀도 손실을 방지하기 위해 이를 직접 사용할 수 있습니다.

```solidity
    function validateRequest(
        ITradingStorage.Trade memory _trade,
        IUpdatePositionSizeUtils.DecreasePositionSizeInput memory _input
    ) internal view returns (uint256 positionSizeCollateralDelta) {
        ...

        uint256 newCollateralAmount = isLeverageUpdate
            ? ((TradingCommonUtils.getPositionSizeCollateral(_trade.collateralAmount, _trade.leverage) -
                positionSizeCollateralDelta) * 1e3) / (_trade.leverage - _input.leverageDelta) // @audit = trade.collateralAmount
            : _trade.collateralAmount - _input.collateralDelta;
        ...
    }
```

# [L-02] `requestIncreasePositionSize` 실패 가능성 (`requestIncreasePositionSize` could fail)

사용자가 포지션 크기를 늘릴 때 레버리지만 늘리려는 경우 `_input.collateralDelta`를 0으로 설정할 수 있습니다.

```solidity
    function requestIncreasePositionSize(IUpdatePositionSizeUtils.IncreasePositionSizeInput memory _input) external {
        // 1. Base validation
        ITradingStorage.Trade memory trade = _baseValidateRequest(_input.user, _input.index);

        // 2. Increase position size validation
        uint256 positionSizeCollateralDelta = IncreasePositionSizeUtils.validateRequest(trade, _input);

        // 3. Transfer collateral delta from trader to diamond contract (nothing transferred for leverage update)
        // @audit - if collateral delta 0, should just skip
>>>     TradingCommonUtils.transferCollateralFrom(trade.collateralIndex, _input.user, _input.collateralDelta);

        // 4. Create pending order and make price aggregator request
        ITradingStorage.Id memory orderId = _initiateRequest(
            trade,
            true,
            _input.collateralDelta,
            _input.leverageDelta,
            positionSizeCollateralDelta,
            _input.expectedPrice,
            _input.maxSlippageP
        );

        emit IUpdatePositionSizeUtils.PositionSizeUpdateInitiated(
            orderId,
            trade.user,
            trade.pairIndex,
            trade.index,
            true,
            _input.collateralDelta,
            _input.leverageDelta
        );
    }
```

그러나 사용된 담보 토큰이 0 전송 시 리버트(revert)되는 경우 업데이트는 항상 실패합니다.

`_input.collateralDelta`가 0이면 전송을 건너뛰십시오.

# [L-03] 최대 및 최소 레버리지 제한 우회 (Bypassing max and min leverage limits)

포지션 크기를 늘리는 요청이 이루어지면 새 레버리지가 최소 및 최대 레버리지 제한 내에 있는지 확인합니다.

```solidity
    function validateRequest(
        ITradingStorage.Trade memory _trade,
        IUpdatePositionSizeUtils.IncreasePositionSizeInput memory _input
    ) internal view returns (uint256 positionSizeCollateralDelta) {
        ...

        // 2. Revert if new leverage is below min leverage or above max leverage
        bool isLeverageUpdate = _input.collateralDelta == 0;
        {
            uint24 leverageToValidate = isLeverageUpdate
                ? _trade.leverage + _input.leverageDelta
                : _input.leverageDelta;
            if (
                leverageToValidate > _getMultiCollatDiamond().pairMaxLeverage(_trade.pairIndex) * 1e3 || // @audit only check in validateReqquest, not when validateCallback
                leverageToValidate < _getMultiCollatDiamond().pairMinLeverage(_trade.pairIndex) * 1e3
            ) revert ITradingInteractionsUtils.WrongLeverage();
        }
        ...
    }
```

그러나 콜백 검증 중에는 새 레버리지가 최소 및 최대 제한 내에 있는 것으로 가정하고 다시 확인하지 않습니다. 즉, 요청과 콜백 사이에 레버리지 제한이 업데이트되면 레버리지 제한을 우회할 수 있습니다. 사용자는 업데이트 트랜잭션을 실행하기 직전에 제한을 벗어난 레버리지로 포지션 크기를 늘리는 요청을 하여 이를 악용할 수 있습니다.

```solidity
    function validateCallback(
        ITradingStorage.Trade memory _existingTrade,
        IUpdatePositionSizeUtils.IncreasePositionSizeValues memory _values,
        ITradingCallbacks.AggregatorAnswer memory _answer,
        uint256 _expectedPrice,
        uint256 _maxSlippageP
    ) internal view returns (ITradingCallbacks.CancelReason cancelReason) {
        uint256 maxSlippage = (uint256(_expectedPrice) * _maxSlippageP) / 100 / 1e3;

        cancelReason = (
            _existingTrade.long
                ? _values.priceAfterImpact > _expectedPrice + maxSlippage
                : _values.priceAfterImpact < _expectedPrice - maxSlippage
        )
            ? ITradingCallbacks.CancelReason.SLIPPAGE // 1. Check price after impact is within slippage limits
            : _existingTrade.tp > 0 &&
                (_existingTrade.long ? _answer.price >= _existingTrade.tp : _answer.price <= _existingTrade.tp)
            ? ITradingCallbacks.CancelReason.TP_REACHED // 2. Check TP has not been reached
            : _existingTrade.sl > 0 &&
                (_existingTrade.long ? _answer.price <= _existingTrade.sl : _answer.price >= _existingTrade.sl)
            ? ITradingCallbacks.CancelReason.SL_REACHED // 3. Check SL has not been reached
            : (
                _existingTrade.long
                    ? (_answer.price <= _values.existingLiqPrice || _answer.price <= _values.newLiqPrice)
                    : (_answer.price >= _values.existingLiqPrice || _answer.price >= _values.newLiqPrice)
            )
            ? ITradingCallbacks.CancelReason.LIQ_REACHED // 4. Check current and new LIQ price not reached
            : !TradingCommonUtils.isWithinExposureLimits(
                _existingTrade.collateralIndex,
                _existingTrade.pairIndex,
                _existingTrade.long,
                _values.newCollateralAmount,
                _values.newLeverage
            )
            ? ITradingCallbacks.CancelReason.EXPOSURE_LIMITS // 5. Check trade still within exposure limits
            : ITradingCallbacks.CancelReason.NONE;  // @audit not check if the leverage is within limit when callback
    }
```

초기 요청과 콜백 사이의 한도가 업데이트된 경우에도 새 레버리지가 최소 및 최대 레버리지 제한 내에 유지되도록 콜백 중에 새 레버리지를 검증하십시오.

# [L-04] `__placeholder`가 Trade 데이터 내부에서 0으로 설정되지 않음 (`__placeholder` is not set to 0 inside the Trade data)

거래가 시작될 때 사용자는 저장될 `Trade` 데이터를 제공해야 합니다. `Trade` 구조체 데이터 내부에는 나중에 사용하기 위해 예약된 `__placeholder`가 있습니다. 그러나 공개 거래 작업은 현재 사용자가 `__placeholder`에 임의의 값을 제공하도록 허용합니다. 나중에 이 필드가 사용되는 경우 악의적인 사용자가 업그레이드가 실행되기 전에 `__placeholder`에 값을 입력하여 입력 유효성 검사를 피하고 잠재적으로 문제를 일으킬 수 있습니다. `_openTrade` 작업 내부에서 `_trade.__placeholder`를 0으로 설정하는 것을 고려하십시오.
