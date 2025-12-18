# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**0xOstium/smart-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Ostium 정보

Ostium은 Arbitrum을 기반으로 구축된 플랫폼으로, 토큰화 없이 가상 가격 노출을 통해 실물 자산(RWA)의 온체인 무기한 거래를 가능하게 합니다. 유동성 버퍼와 시장 조성 볼트가 협력하여 트레이더 PnL 및 미결제 약정 불균형을 관리하는 이중 볼트 유동성 모델을 사용하며, 고속 오라클이 실시간 가격을 제공합니다. 시스템은 외부 서비스를 통해 청산 및 주문 실행을 자동화하는 한편, 위험 조정 수수료 구조는 노출의 균형을 유지하고 시장 안정성을 장려합니다.

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

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [e8d0b546bc420cd31e280ea0ff901e8514fcc0f4](https://github.com/0xOstium/smart-contracts/tree/e8d0b546bc420cd31e280ea0ff901e8514fcc0f4)

_수정 검토 커밋 해시_ - [ee3640b7da1d44f880b58e6b0ae850b5cbd41788](https://github.com/0xOstium/smart-contracts/tree/ee3640b7da1d44f880b58e6b0ae850b5cbd41788)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `OstiumLockedDepositNft`
- `OstiumOpenPnl`
- `OstiumPairInfos`
- `OstiumPairsStorage`
- `OstiumPriceRouter`
- `OstiumPriceUpKeep`
- `OstiumPrivatePriceUpKeep`
- `OstiumRegistry`
- `OstiumTimelockManager`
- `OstiumTimelockOwner`
- `OstiumTradesUpKeep`
- `OstiumTrading`
- `OstiumTradingCallbacks`
- `OstiumTradingStorage`
- `OstiumVault`
- `OstiumVerifier`
- `ChainUtils`
- `TradingCallbacksLib`
- `Delegatable`

# 발견 사항

# [H-01] accFundingRate가 음수일 때 펀딩 수수료 과소 평가

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

펀딩 수수료 회계는 롱이 숏에 지불할 때와 숏이 롱에 지불할 때 서로 다르며, 숏은 oiDelta가 음수일 때 원래 지불해야 하는 것보다 항상 적은 펀딩 수수료를 지불합니다.

이 문제는 어느 쪽이 지불하든 관계없이 스케일링이 항상 `valueShort` 회계에 적용된다는 사실에서 비롯됩니다. 따라서 숏은 항상 롱으로부터 전체 펀딩 수수료를 받지만 롱에게는 부분적인 펀딩 수수료만 지불합니다.

다음을 고려하십시오.

- oiDelta가 양수일 때:

롱은 숏에게 전체 `accFundingRate`를 지불합니다. 전체 `accFundingRate`는 `accPerOiLong`에 올바르게 추가되고, `* openInterestLong / openInterestShort`로 스케일링된 `accFundingRate`는 `accPerOiShort`에 추가됩니다. 이 스케일링은 숏에게 지불된 토큰별 금액을 올바르게 반영하는 데 필요합니다.

- oiDelta가 음수일 때:

숏은 롱에게 전체 `accFundingRate`를 지불해야 하지만, 전체 `accFundingRate`는 `accPerOiLong`에 잘못 추가되어 과소 평가되며, 여기서 `* openInterestShort / openInterestLong`으로 스케일링되어야 합니다. 마찬가지로 `* openInterestLong / openInterestShort`로 스케일링된 `accFundingRate`는 `accPerOiShort`에 추가되어 숏이 더 적은 수수료를 지불하게 되는데, 여기서 이렇게 줄여서는 안 되며 전체 `accFundingRate`가 `accPerOiShort`에 추가되어야 합니다.

보시다시피 oiDelta가 음수이면 롱에게 지불되는 펀딩 수수료는 oiDelta의 크기만큼 과소 평가됩니다.

## 권장 사항

oiDelta가 음수일 때 펀딩 수수료를 적절하게 계상하여이 불일치를 수정하십시오.

# [M-01] 거래 종료 시 슬리피지 보호 누락

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

closeTradeMarketCallback 함수(OstiumTradingCallback.sol)에는 트레이더가 포지션을 닫을 때 받는 담보 금액에 대한 슬리피지 보호 기능이 없습니다. 거래 종료가 이행될 때(신뢰할 수 있는 포워더를 통해) 종료가 시작되었을 때보다 시장 상황이 좋지 않아 트레이더가 예상보다 훨씬 적은 담보를 받을 수 있습니다. 이행은 허용된 기간 내 언제든지 발생할 수 있고 실행 포워더가(의도적이든 아니든) 불리한 시장 상황에서 종료를 처리할 수 있으므로 이는 특히 중요합니다.

```solidity
function closeTradeMarketCallback(
    bytes32[] calldata priceData,
    Trade memory trade,
    TradeInfo memory tradeInfo
) {
    // Calculates collateral to return
    uint256 collateralToReturn = getTradeValue(percentProfit, trade.collateral);
    // Transfers funds without minimum amount verification
}
```

## 권장 사항

closeTradeMarketCallback 함수에 minCollateralToReceive 매개변수를 추가하십시오.

# [M-02] `updateTp`가 TP를 검증하고 수정하지 않음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`updateTp`가 호출될 때 제공된 TP 값이 `openPrice` 및 거래 포지션(`buy`)과 관련하여 적절한지 확인하기 위해 유효성을 검사하지 않습니다. 또한 TP가 허용된 최대 이익을 초과하지 않도록 조정되지 않습니다.

```solidity
    function updateTp(uint16 pairIndex, uint8 index, uint192 newTp) external notDone {
        address sender = _msgSender();
        IOstiumTradingStorage storageT = IOstiumTradingStorage(registry.getContractAddress('tradingStorage'));

        if (!checkNoPendingTrigger(sender, pairIndex, index, IOstiumTradingStorage.LimitOrder.TP)) {
            revert TriggerPending(sender, pairIndex, index);
        }

        IOstiumTradingStorage.Trade memory t = storageT.getOpenTrade(sender, pairIndex, index);

        if (t.leverage == 0) {
            revert NoTradeFound(sender, pairIndex, index);
        }

        storageT.updateTp(sender, pairIndex, index, newTp);

        emit TpUpdated(storageT.getOpenTradeInfo(sender, pairIndex, index).tradeId, sender, pairIndex, index, newTp);
    }
```

## 권장 사항

제공된 tp의 유효성을 검사하고 `TradingCallbacksLib.correctTp`를 호출하여 제공된 tp를 조정하는 것을 고려하십시오.

# [M-03] 최대 개설 수수료 확인 부족으로 인해 문제 발생 가능

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

새 거래가 실행되고 등록되면 개설 수수료가 사용자 담보에서 차감됩니다.

```solidity
    function registerTrade(uint256 tradeId, IOstiumTradingStorage.Trade memory trade, uint256 latestPrice)
        private
        returns (IOstiumTradingStorage.Trade memory)
    {
        // ...

        // 2.1 Charge opening fee
        {
>>>         (uint256 reward, uint256 vaultReward) = storageT.handleOpeningFees(
                trade.pairIndex, latestPrice, trade.collateral * trade.leverage / 100, trade.leverage, trade.buy
            );

            trade.collateral -= reward;

            emit DevFeeCharged(tradeId, trade.trader, reward);

            if (vaultReward > 0) {
                IOstiumVault vault = IOstiumVault(registry.getContractAddress('vault'));
                storageT.transferUsdc(address(storageT), address(this), vaultReward);
                vault.distributeReward(vaultReward);
                trade.collateral -= vaultReward;
                emit VaultOpeningFeeCharged(tradeId, trade.trader, vaultReward);
            }

            // 2.2 Charge the oracle fee
            reward = storageT.handleOracleFees(trade.pairIndex, true);

            trade.collateral -= reward;

            emit OracleFeeCharged(tradeId, trade.trader, reward);
        }
       // ...
    }
```

`handleOpeningFees`는 다른 사용자가 개설한 거래에 의존하므로 트레이더의 개설 요청과 실제 거래 실행 사이에 차이가 있을 수 있음을 알 수 있습니다. 이로 인해 다른 트레이더의 거래 규모 변동으로 인해 트레이더가 예상치 못한 높은 수수료를 지불할 수 있습니다.

```solidity
    function handleOpeningFees(
        uint16 _pairIndex,
        uint256 latestPrice,
        uint256 _leveragedPositionSize,
        uint32 leverage,
        bool isBuy
    ) external onlyCallbacks returns (uint256 devFee, uint256 vaultFee) {
        uint256 oiCap = openInterest[_pairIndex][2];
        uint256 oiLong = openInterest[_pairIndex][0] * latestPrice / PRECISION_18 / 1e12;
        uint256 oiShort = openInterest[_pairIndex][1] * latestPrice / PRECISION_18 / 1e12;

        uint256 openInterestMax = oiLong > oiShort ? oiLong : oiShort;
        // @audit - the logic is incorrect here?
        oiCap = openInterestMax > oiCap ? openInterestMax : oiCap;

        int256 oiDelta = oiLong.toInt256() - oiShort.toInt256();
        uint256 usageOi = isBuy ? oiLong : oiShort;

        (devFee, vaultFee) = IOstiumPairInfos(registry.getContractAddress('pairInfos')).getOpeningFee(
            _pairIndex,
            isBuy ? _leveragedPositionSize.toInt256() : -_leveragedPositionSize.toInt256(),
            leverage,
            oiDelta,
            oiCap,
            usageOi
        );

        devFees += devFee;
    }
```

## 권장 사항

사용자가 담보에서 지불할 것으로 예상하는 최대 수수료를 지정할 수 있도록 허용하는 것을 고려하십시오.

# [M-04] `correctTp()`가 과도하게 부풀려진 값을 반환함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

사용자가 포지션에 담보를 추가하면 `leverage` 매개변수가 업데이트되어 `initialLeverage` 매개변수보다 낮아집니다. 최대 이익이 포지션이 생성된 초기 담보의 900%를 초과해서는 안 된다는 요구 사항으로 인해 `_currentPercentProfit()`에서 반환된 최대 이익 백분율 값은 새 담보가 추가될 때 축소됩니다.

```solidity
        p = p * leverage / leverageToUse;
```

이로 인해 추가 담보가 추가된 후 `initialLeverage`를 기반으로 한 계산이 900%를 크게 초과하는 경우에도 `_currentPercentProfit()`에서 반환된 최대 백분율 값이 `900%`에 도달하지 않는 상황이 발생합니다. 이는 새 `leverage` 값으로 스케일링하기 전에 최대 백분율 값을 `900%`로 제한하기 때문입니다.

```solidity
        p = p > maxPnlP ? maxPnlP : p;
```

이러한 업데이트의 조합으로 인해 `correctTp()` 함수가 `tp`를 잘못 계산합니다. 구체적으로 `if()` 검사의 요구 사항이 충족되지 않아 사용자 포지션이 달성할 수 있는 실제 최대 이익에 비해 과도하게 부풀려진 `tp`가 발생합니다.

이러한 잘못된 계산으로 인해 담보가 추가된 포지션은 `TP` 지정가 주문을 통해 예상대로 최대 이익에서 마감되지 않습니다. 대신 사용자는 초기 담보의 최대 이익에 도달한 후 시장가 주문으로 포지션을 수동으로 마감해야 하므로 비효율성과 기회 상실로 이어질 수 있습니다.

`currentPercentProfit()` 뷰(view) 함수에서 값을 검색할 때도 유사한 문제가 발생합니다.

## 권장 사항

조정된 레버리지를 올바르게 설명하기 위해 `correctTp()` 함수의 `if()` 검사에 배율 인자(scaling factor)를 도입하십시오.

```diff
        uint256 maxGain = int16(MAX_GAIN_P) * int32(PRECISION_6) * leverage / initialLeverage;
        if (
            tp == 0
                || _currentPercentProfit(openPrice.toInt256(), tp.toInt256(), buy, int32(leverage), int32(initialLeverage))
-                   == int16(MAX_GAIN_P) * int32(PRECISION_6)
+                   >= maxGain
        ) {
```

# [M-05] 사용자가 오라클 수수료 부과를 방지할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

이 문제는 `closeTradeMarketCallback()` 함수에서 발생합니다. 전체 종료(`closePercentage == 100e2`) 및 취소 사유가 `MARKET_CLOSED`인 경우 트레이더에게 오라클 수수료의 절반이 부과되기 때문입니다.

```solidity
            } else if (closePercentage == 100e2) {
                // Charge only half
                uint256 oracleFee = storageT.handleOracleFees(t.pairIndex, false);

                storageT.transferUsdc(t.trader, address(storageT), oracleFee);

                emit OracleFeeCharged(a.orderId, t.trader, oracleFee);
            }
```

`OstiumTradingCallbacks` 계약 내의 함수 실행이 `USDC` 허용과 같은 사용자 작업에 의존해서는 안 되기 때문에 문제가 됩니다. 사용자는 모든 계약에 대해 특정 금액만 승인해야 하며 스마트 계약에 무기한 허용을 부여해서는 안 됩니다.

`closeTradeMarketCallback()`의 경우 취소 사유가 `MARKET_CLOSED`이면 소액의 오라클 수수료를 공제하려고 할 때 불충분한 허용으로 인해 함수가 되돌려질(revert) 가능성이 높습니다.

이 상황은 트레이더의 의도적인 것(예: 허용량을 의도적으로 제한함)이거나 단순히 예상치 못한 허용량 부족으로 인한 것일 수 있습니다. 관계없이 `OstiumTradingCallbacks` 계약 내의 함수는 예상치 못한 되돌림으로 이어질 수 있는 사용자 작업에 의존해서는 안 됩니다.

## 권장 사항

오라클 수수료는 다음 중 하나여야 합니다.
1. 취소 사유가 발생한 경우 **절대 부과되지 않음**, 또는
2. 사용자 제어 허용에 대한 의존성을 제거하기 위해 **선불로 부과됨**.

# [L-01] 최소 담보 금액 부족으로 인한 문제 발생 가능

거래를 개설할 때(`openTrade`), 레버리지 포지션 크기가 구성된 최소 포지션보다 큰 한 담보 또는 남은 담보가 최소값에 대해 유효성을 검사하지 않습니다.

```solidity
    function openTrade(
        IOstiumTradingStorage.Trade calldata t,
        IOstiumTradingStorage.OpenOrderType orderType,
        uint256 slippageP // for market orders only
    ) external notDone notPaused pairIndexListed(t.pairIndex) {
        // ...

        if (
            t.leverage == 0 || t.leverage < pairsStored.pairMinLeverage(t.pairIndex)
                || t.leverage > pairsStored.pairMaxLeverage(t.pairIndex)
        ) revert WrongLeverage(t.leverage);

        if (t.collateral > maxAllowedCollateral) {
            revert AboveMaxAllowedCollateral();
        }

>>>     if (t.collateral * t.leverage / 100 < pairsStored.pairMinLevPos(t.pairIndex)) {
            revert BelowMinLevPos();
        }

        if (t.tp != 0 && (t.buy ? t.tp <= t.openPrice : t.tp >= t.openPrice)) {
            revert WrongTP();
        }

        if (t.sl != 0 && (t.buy ? t.sl >= t.openPrice : t.sl <= t.openPrice)) {
            revert WrongSL();
        }

        storageT.transferUsdc(sender, address(storageT), t.collateral);
        // ...
    }
```

이로 인해 문제가 발생할 수 있습니다. 예를 들어 `registerTrade`가 트리거되면 `vaultReward` 및 `reward`를 계산하는데, 이는 레버리지 포지션 크기뿐만 아니라 잠재적으로 담보보다 클 수 있는 오라클 수수료를 기반으로 합니다.

```solidity
    function registerTrade(uint256 tradeId, IOstiumTradingStorage.Trade memory trade, uint256 latestPrice)
        private
        returns (IOstiumTradingStorage.Trade memory)
    {
        IOstiumTradingStorage storageT = IOstiumTradingStorage(registry.getContractAddress('tradingStorage'));
        IOstiumPairInfos pairInfos = IOstiumPairInfos(registry.getContractAddress('pairInfos'));

        // 2.1 Charge opening fee
        {
            (uint256 reward, uint256 vaultReward) = storageT.handleOpeningFees(
                trade.pairIndex, latestPrice, trade.collateral * trade.leverage / 100, trade.leverage, trade.buy
            );

>>>         trade.collateral -= reward;

            emit DevFeeCharged(tradeId, trade.trader, reward);

            if (vaultReward > 0) {
                IOstiumVault vault = IOstiumVault(registry.getContractAddress('vault'));
                storageT.transferUsdc(address(storageT), address(this), vaultReward);
                vault.distributeReward(vaultReward);
>>>             trade.collateral -= vaultReward;
                emit VaultOpeningFeeCharged(tradeId, trade.trader, vaultReward);
            }

            // 2.2 Charge the oracle fee
            reward = storageT.handleOracleFees(trade.pairIndex, true);
>>>         trade.collateral -= reward;

            emit OracleFeeCharged(tradeId, trade.trader, reward);
        }
        // ..
    }
```

또는 시장이 닫히고 개방 거래가 실행되면 `oracleFees`가 `trade.collateral`보다 클 때 되돌려집니다.

```solidity
    function openTradeMarketCallback(IOstiumPriceUpKeep.PriceUpKeepAnswer calldata a) external notDone {
        // ...

        if (cancelReason == CancelReason.NONE) {
            trade = registerTrade(a.orderId, trade, uint192(a.price));
            uint256 tradeNotional = storageT.getOpenTradeInfo(trade.trader, trade.pairIndex, trade.index).oiNotional;
            IOstiumOpenPnl(registry.getContractAddress('openPnl')).updateAccTotalPnl(
                a.price, trade.openPrice, 0, tradeNotional, trade.pairIndex, trade.buy, true
            );
            emit MarketOpenExecuted(a.orderId, trade, priceImpactP, tradeNotional);
        } else {
            // Charge only half
            uint256 oracleFees = storageT.handleOracleFees(trade.pairIndex, false);
>>>         storageT.transferUsdc(address(storageT), trade.trader, trade.collateral - oracleFees);

            emit OracleFeeCharged(a.orderId, trade.trader, oracleFees);
            emit MarketOpenCanceled(a.orderId, trade.trader, trade.pairIndex, cancelReason);
        }
        storageT.unregisterPendingMarketOrder(a.orderId, true);
    }
```

새 거래를 개설할 때 최소 담보 확인을 추가하는 것을 고려하십시오.

# [L-02] 상장된 쌍을 제거하면 거래 종료가 불가능함

쌍 인덱스가 제거되면 트레이더는 여전히 제거된 쌍을 사용하여 기존 미결제 거래에 대해 `closeTradeMarket`, `topUpCollateral` 및 `removeCollateral`을 요청할 수 있습니다. 그러나 거래가 실행되면 그룹 담보를 업데이트하려고 할 때 되돌려집니다.

```solidity
    function updateGroupCollateral(uint16 _pairIndex, uint256 _amount, bool _long, bool _increase) external {
        if (
            msg.sender != registry.getContractAddress('callbacks')
                && msg.sender != registry.getContractAddress('trading')
        ) revert NotAuthorized(msg.sender);

>>>     if (!isPairIndexListed[_pairIndex]) revert PairNotListed(_pairIndex);

        uint256[2] storage collateralOpen = groupsCollaterals[pairs[_pairIndex].groupIndex];
        uint256 index = _long ? 0 : 1;

        if (_increase) {
            collateralOpen[index] += _amount;
        } else {
            collateralOpen[index] = collateralOpen[index] > _amount ? collateralOpen[index] - _amount : 0;
        }
    }
```

이렇게 하면 제거된 쌍 인덱스를 사용하는 현재 미결제 거래가 정산되는 것을 방지합니다.

제거된 쌍 인덱스에서 미결제 거래를 정산하는 기능을 구현하는 것을 고려하십시오.

# [L-03] `MAX_TRADE_SIZE_REF`에 대한 유효성 검사 누락

프로토콜은 `MAX_TRADE_SIZE_REF`를 상수로 정의하지만 거래 크기 참조를 설정할 때 유효성 검사 확인이 부족합니다. 이로 인해 거래 규모 참조가 의도한 최대 한도를 초과하여 잠재적으로 가격 영향 계산에 영향을 미치는 시나리오가 발생할 수 있습니다.

```solidity
// Constant defined but not enforced
uint256 constant MAX_TRADE_SIZE_REF = 10000000e6; // 10M
```

거래 규모 참조를 설정할 때 유효성 검사 확인을 추가하고 체인링크 보고서에서 반환된 값의 유효성을 검사하십시오.

# [L-04] 불변성이 깨질 수 있음

`setLiqThresholdP()` 함수에서 `liqThresholdP`가 업데이트되는 상황에서 `maxNegativePnlOnOpenP`가 새 값 아래로 유지되는지 확인하기 위해 다시 확인되지 않습니다.

이로 인해 `maxNegativePnlOnOpenP < liqThresholdP` 불변성이 깨질 수 있습니다. 큰 가격 영향과 관련된 극단적인 상황에서 이는 청산 가능한 포지션 생성으로 이어질 수 있습니다.

`setLiqThresholdP()` 함수에서도 `maxNegativePnlOnOpenP`를 다시 확인하는 것을 고려하십시오.

# [L-05] 새로운 `PairFundingFeesV2` 매개변수가 검증되지 않음

`initializeV2()` 함수에서 새 펀딩 수수료 매개변수를 초기화하는 동안 이러한 값은 주어진 최대값에 대해 온전성 검사가 수행되지 않습니다.

`PairFundingFeesV2` 매개변수가 업데이트될 때 다른 설정자(setter) 함수에서 온전성 검사가 수행되므로 `initializeV2()` 함수에도 적절한 유효성 검사를 추가하는 것을 고려하십시오.

# [L-06] 타임아웃의 경우 오라클 수수료가 환불되지 않음

사용자가 보류 중인 부분 종료(partial close) 주문을 생성하면 주문이 타임아웃될 수 있습니다. 그러나 부분 종료의 경우 작업에 대한 추가 오라클 수수료가 미리 청구됩니다.

이러한 주문이 타임아웃되고 사용자가 `closeTradeMarketTimeout()` 함수를 통해 오라클을 사용하지 않고 보류 중인 시장가 주문을 닫는 경우, `retry == true` 호출이 아닌 경우 오라클 수수료는 환불되어야 합니다.

# [L-07] 잘못된 담보 가치가 전달됨

`openTradeMarketCallback()` 및 `executeAutomationOpenOrderCallback()` 함수 모두에서 취소 사유 확인에 전달된 담보 가치는 `open` 및 `oracle` 수수료에 의해 아직 감소되지 않았습니다. 취소 확인은 `registerTrade()` 함수 호출 전에 수행되기 때문에 발생합니다.

결과적으로 `withinExposureLimits()` 확인은 항상 부풀려진 값에 대해 수행됩니다. 극단적인 상황에서 이는 실제로 `tradingStorage.openInterest(pairIndex, 2)` 또는 `pairsStorage.groupMaxCollateral(pairIndex)` 제한에 맞는 주문의 거부로 이어질 수 있습니다.
