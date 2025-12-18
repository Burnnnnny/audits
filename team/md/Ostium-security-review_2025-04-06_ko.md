# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**0xOstium/smart-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Ostium 정보

Ostium은 실물 자산(RWA)의 탈중앙화 무기한 거래 프로토콜입니다. 원자재, 외환, 암호화폐 및 다양한 롱테일 자산 전반에 걸쳐 작동합니다.

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

_검토 커밋 해시_ - [30de704780857f350aacde97ff16f843236b13ba](https://github.com/0xOstium/smart-contracts/tree/30de704780857f350aacde97ff16f843236b13ba)

_수정 검토 커밋 해시_ - [b09f3bfd9b88578fae345e962ca5f38c0ef11f89](https://github.com/0xOstium/smart-contracts/tree/b09f3bfd9b88578fae345e962ca5f38c0ef11f89)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `OstiumPairInfos`
- `OstiumPairsStorage`
- `OstiumPriceUpKeep`
- `OstiumPrivatePriceUpKeep`
- `OstiumTrading`
- `OstiumTradingCallbacks`
- `Delegatable`
- `TradingCallbacksLib`
- `interfaces/`

# 발견 사항

# [M-01] `liqPrice == priceAfterImpact`일 때 청산 시 잘못된 담보 환불

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

청산이 트리거되고 사용된 오라클 가격이 `executeAutomationCloseOrderCallback()` 실행 중에 `liqPrice == priceAfterImpact`가 되는 경우, 시스템은 사용자 담보의 일부(약 `liquidationFee`와 동일)를 잘못 환불할 수 있습니다.

이는 `getTradeValuePure()` 함수 내에서 `value`와 `liqMarginValue`가 계산되는 방식의 불일치로 인해 발생합니다. 특정 조건(`liqPrice == priceAfterImpact`)에서 포지션이 완전히 청산되어야 함에도 불구하고 `value`가 `liqMarginValue`보다 커질 수 있습니다.

새로운 `마진 기반 청산(Margin-Based Liquidations)` 로직 내에서 사용자는 청산 중에 담보를 돌려받지 않아야 합니다. 전체 담보는 손실 거래를 충당하기 위해 `liquidationFee`와 `Vault` 사이에 분배되어야 합니다.

그러나 코드에 남아 있는 레거시 환불 로직으로 인해:

```solidity
@>      uint256 usdcSentToVault = usdcLeftInStorage - usdcSentToTrader;
        storageT.transferUsdc(address(storageT), address(this), usdcSentToVault);
        vault.receiveAssets(usdcSentToVault, trade.trader);
@>      if (usdcSentToTrader > 0) storageT.transferUsdc(address(storageT), trade.trader, usdcSentToTrader);
```

`value` 및 `liqMarginValue`의 잘못된 계산과 결합하여 `getTradeValue()` 함수에서 반환된 `usdcSentToTrader`는 대략 `liquidationFee`와 동일하게 되어 청산된 트레이더에게 의도하지 않은 환불을 초래할 수 있습니다.

## 권장 사항

청산 중에 `usdcSentToTrader`가 명시적으로 `0`으로 설정되도록 하여 담보 환불을 방지하십시오.

```diff
    if (liquidationFee > 0) {
        storageT.transferUsdc(address(storageT), address(this), liquidationFee);
        vault.distributeReward(liquidationFee);
        emit VaultLiqFeeCharged(orderId, tradeId, trade.trader, liquidationFee);
+
+       usdcSentToTrader = 0;
    }
```

# [L-01] `maxLeverage`가 `minLeverage`보다 낮게 설정될 수 있음

`_setPairOvernightMaxLeverage` 내부에는 `overnightMaxLeverage`가 `groups[_pair.groupIndex].minLeverage`보다 낮지 않은지 확인하는 유효성 검사가 없습니다. `overnightMaxLeverage`의 적절한 구성을 보장하기 위해 이 유효성 검사를 추가하는 것을 고려하십시오.

# [L-02] `liqMarginThresholdP` 및 `maxNegativePnlOnOpenP`의 일관성 없는 유효성 검사

`liqMarginThresholdP` 값은 고정된 `MAX_LIQ_MARGIN_THRESHOLD_P`에 대해 유효성을 검사하는 `_setLiqMarginThresholdP()`를 통해 설정됩니다. 그러나 이 값은 `value <= 100 - liqMarginThresholdP`로 제한되는 `maxNegativePnlOnOpenP`의 유효 범위에 직접적인 영향을 미칩니다.

업데이트 중에 이 두 매개변수 간의 교차 확인이 없기 때문에 `liqMarginThresholdP`를 변경하면 현재 `maxNegativePnlOnOpenP` 값이 간접적으로 무효화되어 잠재적으로 허용 한도를 초과할 수 있습니다.

권장 사항

임계값이 업데이트된 후에도 기존 `maxNegativePnlOnOpenP`가 유효하게 유지되도록 `_setLiqMarginThresholdP()`에 유효성 검사를 도입하십시오.

```diff
-       if (value > MAX_LIQ_MARGIN_THRESHOLD_P) { 
+       if (value > MAX_LIQ_MARGIN_THRESHOLD_P || maxNegativePnlOnOpenP > 100 - value) { 
            revert WrongParams();
        }
```
