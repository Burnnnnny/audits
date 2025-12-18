
# Pashov Audit Group 정보


Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이 분야에서 입증된 실력을 갖추고 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 여러 차례 챔피언에 올랐거나, 우리와의 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 의욕적인 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도운 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 귀하의 프로젝트를 위해 우리 팀이 최선을 다할 것을 보장합니다.

이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.
    

# 면책 조항


스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.


# 소개

<p><strong>0xOstium/smart-contracts</strong> 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며, <strong>unforgiven, t.aksoy, aslanbek</strong>이 참여하여 <strong>Ostium</strong>을 검토했습니다. 총 <strong>9</strong>개의 문제가 발견되었습니다.</p>

# Ostium 정보

<p>Ostium은 실물 자산(RWA)의 탈중앙화 무기한 거래 프로토콜입니다. 원자재, 외환, 암호화폐 및 다양한 롱테일 자산 전반에 걸쳐 작동합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [e5b6eefac33046adbb27bd1073b7a46321133d56](https://github.com/0xOstium/smart-contracts/tree/e5b6eefac33046adbb27bd1073b7a46321133d56)<br>&nbsp;&nbsp;(0xOstium/smart-contracts)

**수정 검토 커밋 해시:**<br>• [8ce6a171f5fd62d624d6c702005620709a113c7d](https://github.com/0xOstium/smart-contracts/tree/8ce6a171f5fd62d624d6c702005620709a113c7d)<br>&nbsp;&nbsp;(0xOstium/smart-contracts)




# 범위

- `OstiumPairInfos.sol`
- `OstiumPairsStorage.sol`
- `OstiumTrading.sol`
- `OstiumTradingCallbacks.sol`
- `OstiumTradingStorage.sol`
- `OstiumVault.sol`
- `radingCallbacksLib.sol`
- `TradingLib.sol`
- `interfaces/`

# 발견 사항



# [M-01] 델타 불균형 대신 최종 불균형을 사용하여 가격 영향 과다 청구

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

제안서에는 트레이더가 거래로 인해 불균형이 증가하는 정도까지만 "스프레드를 지불"해야 한다고 명시되어 있습니다(즉, 절대 불균형이 아니라 불균형 변화에 대해 지불).
`_priceImpactFunction`은 또한 트레이더가 도입한 불균형에 대해서만 지불하도록 설계되었습니다. 로직은 거래가 불균형을 줄일 때 0을 올바르게 반환합니다(@1).
```solidity
 function _priceImpactFunction(
        uint256 netVolThreshold,
        uint256 priceImpactK,
        bool buy,
        bool isOpen,
        uint256 tradeSize,
        int256 initialImbalance,
        uint256 midPrice,
        uint256 askPrice,
        uint256 bidPrice
    ) internal pure returns (uint256 priceImpactP) {
        --Snipped--
@1>     // Order decreases imbalance - no price impact
        if (absNextImbalance < absInitialImbalance) {
            return 0;
        }

        // Imbalance below threshold - no price impact
        if (absNextImbalance <= netVolThreshold) {
            return 0;
        }

        uint256 spread = (askPrice - bidPrice) * PRECISION_18 / midPrice;
        uint256 excessOverThreshold = absNextImbalance - netVolThreshold;
@2>     uint256 thresholdTradeSize = tradeSize < excessOverThreshold ? tradeSize : excessOverThreshold;
        uint256 spreadComponent = (spread * thresholdTradeSize) / SPREAD_DIVISOR;

        uint256 dynamicComponent = 0;
        if (excessOverThreshold > 0 && thresholdTradeSize > 0) {
            uint256 thresholdRatio = (thresholdTradeSize * PRECISION_18) / excessOverThreshold;
            uint256 excessSquared = (excessOverThreshold * excessOverThreshold) / PRECISION_18;
            dynamicComponent = (
                (thresholdTradeSize * thresholdRatio / PRECISION_18) * (priceImpactK * excessSquared / PRECISION_27)
            ) / PRECISION_18;
        }

        // Convert USD impact to percentage impact
        uint256 priceImpactUSD = spreadComponent + dynamicComponent;
        priceImpactP = priceImpactUSD * PRECISION_18 / tradeSize * 100;

        return priceImpactP;
    }
```

그러나 거래가 불균형을 증가시키면 함수는 청구된 부분을 잘못 계산합니다(@2).
`increase of absImbalance`를 기준으로 청구하는 대신 함수는 절대 최종 불균형을 기준으로 청구합니다. 이로 인해 사용자는 자신의 거래가 불균형을 아주 조금만 증가시켰더라도 전체 불균형에 기여한 것처럼 청구됩니다.

**예:**
`netVolThreshold = 0`이고 `initialImbalance = -100`이라고 가정합니다.
1. `tradeSize = 199`이고 `buy = true`이면 `nextImbalance = +99`입니다. `absNextImbalance (99) < absInitialImbalance (100)`이므로 (1)의 조건이 적용되고 함수는 `0` priceImpact를 올바르게 반환합니다.
2. 동일한 `initialImbalance = -100`으로 `tradeSize = 200`이면 `nextImbalance = +100`입니다. 이 경우 `absNextImbalance (100) >= absInitialImbalance (100)`이므로 @2의 분기가 실행됩니다. 함수는 `excessOverThreshold = 100` 및 `thresholdTradeSize = 100`을 계산하여 사용자에게 100단위를 청구합니다.

실제로는 불균형이 `1` 단위(99에서 100으로)만 증가했지만 사용자는 자신의 거래가 100 단위의 불균형을 생성한 것처럼 청구됩니다.

이는 제안서의 의도된 설계를 위반하고 사용자 비용의 급격한 상승을 초래합니다.

## 권장 사항

전체 최종 불균형보다는 불균형의 **증분 증가**만 반영하도록 `thresholdTradeSize` 계산을 조정하십시오.
```solidity
thresholdTradeSize = tradeSize < excessOverThreshold ? tradeSize : excessOverThreshold - absInitialImbalance;
```



# [M-02] 부분 종료 시 가격 영향의 잘못된 계산

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

closeTradeMarketCallback을 통해 거래를 닫을 때 계약은 사용자가 닫을 포지션의 백분율을 지정할 수 있도록 허용합니다. 콜백은 닫을 담보, 이익/손실 및 거래 가치를 계산할 때 이 백분율을 올바르게 적용합니다.

그러나 getDynamicTradePriceImpact를 통해 가격 영향을 계산할 때 함수는 줄어든 collateralToClose 대신 항상 전체 거래 담보를 사용합니다. 이러한 불일치로 인해 가격 영향이 과장되어 사용자 손실, 잘못된 PnL 또는 불공정한 실행이 발생할 수 있습니다.

```solidity
    function closeTradeMarketCallback(IOstiumPriceUpKeep.PriceUpKeepAnswer calldata a) external notDone {
        ...

        if (cancelReason != CancelReason.NO_TRADE) {
            if (cancelReason == CancelReason.NONE) {
                uint256 collateralToClose = t.collateral * closePercentage / 100e2;
                IOstiumPairInfos pairInfos = IOstiumPairInfos(registry.getContractAddress('pairInfos'));
                TradingCallbacksLib.PriceImpactResult memory result =
                    TradingCallbacksLib.getDynamicTradePriceImpact(a.price, a.ask, a.bid, false, t, pairInfos);

             ...

```

getDynamicTradePriceImpact에서 함수는 닫히는 부분이 아니라 전체 포지션 담보를 기반으로 거래 명목 가치를 계산합니다.

`uint256 tradeNotional = trade.collateral * trade.leverage * PRECISION_10;
`

## 권장 사항

전체 포지션 크기 대신 부분 담보를 사용하도록 closeTradeMarketCallback을 업데이트하십시오.



# [M-03] `dt`가 `max_dt`에 가까워지면 공격자가 볼륨 감소(decay)를 차단함

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`maxDecayInterval`은 볼륨이 0으로 감소하는 기간을 제어합니다. 그러나 decayRate 및 `maxDecayInterval`에 따라 `dt = maxDecayInterval - 1`과 `dt = maxDecayInterval` 사이에 감소된 볼륨이 급격히 떨어질 수 있습니다.

이를 알게 되면 공격자는 의도적으로 더미 거래를 생성하여 업데이트를 트리거하고 볼륨이 0으로 감소하는 것을 방지하여 다른 사람들의 미래 거래 중 일부가 약간 덜 유리한 가격으로 체결되도록 강제할 수 있습니다.

## 권장 사항

그러한 업데이트가 감소된 볼륨에 큰 영향을 미치지 않도록 max_dt가 충분히 긴지 확인하십시오.



# [L-01] `claimFees()`는 사용자 환불에 필요한 모든 자금을 인출할 수 있음

_인정됨_

정부(gov)가 `OstiumTradingStorage.claimFees()`를 호출하여 누적된 모든 수수료를 인출하면 계약은 전체 `devFees` 잔액을 이체합니다.
```solidity
    function claimFees(uint256 _amount) external onlyGov {
        uint256 _devFees = devFees;
        if (_amount > _devFees || _amount == 0) {
            revert WrongParams();
        }
        devFees -= _amount;

        SafeERC20.safeTransfer(IERC20(usdc), registry.dev(), _amount);
    }
```

나중에 `closeTradeMarketTimeout()`이 사용자(오라클 수수료 포함)에게 환불을 시도하면 계약의 자금 부족으로 인해 트랜잭션이 되돌려질(revert) 수 있습니다.

```solidity
    function closeTradeMarketTimeout(uint256 _order, bool retry) external notDone {
        --Snipped--
        if (percentage != PERCENT_BASE) {
            uint256 oracleFee =
                IOstiumPairsStorage(registry.getContractAddress('pairsStorage')).pairOracleFee(trade.pairIndex);
            storageT.refundOracleFee(oracleFee);
            storageT.transferUsdc(address(storageT), sender, oracleFee);
            emit OracleFeeRefunded(tradeId, sender, trade.pairIndex, oracleFee);
        }
       --Snipped--
    }
```



# [L-02] `closeTradeMarketTimeout()`의 잘못된 오라클 수수료 환불

_인정됨_

`closeTradeMarketTimeout()`에서 계약은 현재 `oracleFee`를 사용자에게 환불합니다. 그러나 오라클 수수료는 원래 거래가 개설된 이후 변경되었을 수 있습니다. 즉, 사용자는 처음에 지불한 것보다 더 많거나 적게 받을 수 있습니다.

```solidity
    function closeTradeMarketTimeout(uint256 _order, bool retry) external notDone {
        --Snipped--

        if (percentage != PERCENT_BASE) {
            uint256 oracleFee =
                IOstiumPairsStorage(registry.getContractAddress('pairsStorage')).pairOracleFee(trade.pairIndex);
            storageT.refundOracleFee(oracleFee);
            storageT.transferUsdc(address(storageT), sender, oracleFee);
            emit OracleFeeRefunded(tradeId, sender, trade.pairIndex, oracleFee);
        }
        --Snipped--
    }
```

이로 인해 회계 불일치 및 시스템의 잠재적 재정 불균형이 발생할 수 있습니다. 예를 들어, 거래가 개설된 후 오라클 수수료가 증가하면 사용자가 과도하게 환불받아 프로토콜 손실이 발생할 수 있습니다. 반대로 수수료가 감소하면 사용자가 과소 환불받아 불공정한 사용자 손실이 발생할 수 있습니다.

**권장 사항:**
현재 `oracleFee` 값에 의존하는 대신 거래 실행 시 사용자가 실제로 지불한 오라클 수수료 금액을 저장하고 `closeTradeMarketTimeout()`에서 정확히 그 금액을 환불하십시오.



# [L-03] `getHandleRemoveCollateralCancelReason()`에서 동등성에 대한 청산

_해결됨_

`TradingCallbacksLib` 라이브러리의 `getHandleRemoveCollateralCancelReason()`에서 청산 조건은 `<=`를 사용합니다.
이로 인해 거래 가치가 청산 마진 가치와 정확히 같은 경우에도 포지션이 청산됩니다.
```solidity
isLiquidated = tradeValue <= liqMarginValue;
```
프로토콜의 의도가 거래 가치가 청산 마진보다 엄격히 아래로 떨어질 때만 청산하는 것이라면 조기 청산으로 이어질 수 있습니다.

**권장 사항:**
동등성이 청산을 트리거해서는 안 되는 경우 엄격한 `<` 비교를 사용하십시오.



# [L-04] 반복적인 인출 요청에 대한 제한 누락

_인정됨_

makeWithdrawRequest 및 cancelWithdrawRequest 함수는 허용량이 있는 주소가 인출 관련 작업을 실행할 수 있도록 허용합니다. 이 확인은 지출자가 허용량 ≥ 주식을 가지고 있는지 확인하지만 허용량을 소비하거나 지출자가 인출 요청과 상호 작용할 수 있는 횟수를 제한하지 않습니다. 결과적으로 허용된 지출자는 소유자를 대신하여 여러 인출 요청을 스팸하고 인출 요청을 여러 번 취소할 수 있습니다.

반복 실행을 방지하기 위해 각 요청 후에 허용량을 사용하십시오.



# [L-05] 오라클 수수료에 대한 불충분한 담보 검증

_해결됨_

사용자가 거래 시스템을 열 때 최소 포지션 크기(collateral * leverage / 100 >= minLevPos)만 시행하고 최소 담보 요구 사항은 시행하지 않습니다.
이로 인해 담보 < 오라클 수수료인 경우가 발생하여 거래가 취소되거나 다른 시나리오에서 사용자가 예상보다 적은 수수료를 지불할 수 있습니다.

```solidity

        if (t.collateral * t.leverage / 100 < pairsStored.pairMinLevPos(t.pairIndex)) {
            revert BelowMinLevPos();
        }


 function openTradeMarketCallback(){
...

            uint256 oracleFee = pairsStorage.pairOracleFee(trade.pairIndex);
            if (trade.collateral > oracleFee) {
                storageT.transferUsdc(address(storageT), trade.trader, trade.collateral - oracleFee);
            } else {
                oracleFee = trade.collateral;
            }
            storageT.handleOracleFee(oracleFee);
```

거래 개설 시 오라클 수수료보다 높은지 확인하는 최소 담보 확인을 추가하십시오.



# [L-06] `priceImpactFunction` 및 `getTradePriceImpact`의 스프레드 계산 불일치

_인정됨_

`_priceImpactFunction` 함수는 스프레드를 다음과 같이 계산합니다.

```solidity
    uint spread = (askPrice - bidPrice) * PRECISION_18 / midPrice;
    uint excessOverThreshold = absNextImbalance - netVolThreshold;
    uint thresholdTradeSize =
      tradeSize < excessOverThreshold ? tradeSize : excessOverThreshold;
    uint spreadComponent = (spread * thresholdTradeSize) / SPREAD_DIVISOR;
 ````

반면 `_getTradePriceImpact`는 약간 다르게 수행합니다.

```solidity
    bool aboveSpot = (isOpen == isLong);

    int192 usedPrice = aboveSpot ? ask : bid;

    priceImpactP =
      (SignedMath.abs(price - usedPrice) * PRECISION_18 / uint192(price) * 100);
```

둘 다 spreadFee를 계산하는 동일한 목적을 수행해야 하지만 새로운 `_priceImpactFunction`은 다르게 수행하며(ask/bid와 mid의 차이 대신 spread/2 사용) `_getTradePriceImpact`에 비해 가격 영향이 높거나 낮을 수 있습니다.

**권장 사항**

_getTradePriceImpact의 동작과 일치하도록 _priceImpactFunction을 변경하십시오.
