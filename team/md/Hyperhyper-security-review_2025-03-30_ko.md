# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

이 감사의 수정 검토는 이 보고서 시점에는 완전히 완료되지 않았으며 팀의 후속 보안 검토를 통해 계속될 것으로 예상됩니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **Hyperhyperfi/protocol** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Hyperhyper 정보 (About Hyperhyper)

Hyperhyper는 짧은 시간 프레임 내에서 청산 위험 없이 높은 레버리지를 추구하는 투기꾼을 위해 설계된 하이퍼 투기 애플리케이션입니다. 이 플랫폼은 초단기 만기를 강조하여 빠른 진입과 종료를 허용하여 더 잦은 거래 기회와 수익으로 이어집니다.

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

_검토 커밋 해시_ - [212acfdcd4066334ff5ea06ce46b0bbca7ca212f](https://github.com/Hyperhyperfi/protocol/tree/212acfdcd4066334ff5ea06ce46b0bbca7ca212f)

_수정 검토 커밋 해시_ - [a4430e759e445e3ca4b90444ae6720f1c5e90ffe](https://github.com/Hyperhyperfi/protocol/tree/a4430e759e445e3ca4b90444ae6720f1c5e90ffe)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Oracle`
- `AdminOperationalTreasury`
- `OperationalTreasury`
- `OperationalTreasuryStorage`
- `PoolDiamond`
- `LiquidityFacet`
- `PoolAdminFacet`
- `PoolViewFacet`
- `PositionInteractionFacet`
- `LiquidityRouter`
- `PoolErrors`
- `PoolHelpers`
- `PoolStorage`
- `PositionsManager`
- `Call`
- `Put`
- `AdminStrategy`
- `Strategy`
- `StrategyStorage`
- `StrategyView`
- `CoreTypes`
- `PositionParams`
- `MathUtils`
- `FullMath`
- `FixedPoint128`
- `Decimals`
- `BlackScholes`
- `SafeDecimalMath`
- `SignedSafeDecimalMath`
- `LPToken`
- `ERC721WithURIBuilderUpgradeable`
- `ETHUnwrapper`
- `IETHUnwrapper`
- `Fenwicks`
- `IV`

# 발견 사항 (Findings)

# [C-01] 옵션 잠금 해제 메커니즘 누락으로 인한 영구적 잠금 (Permanent lock due to missing unlock mechanism for options)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

옵션이 지정된 창(`exerciseWindowDuration`) 내에 행사되지 않거나 `PNL`이 0인 경우(손실 발생 시) 유동성이 풀에 영구적으로 잠길 수 있는 치명적인 문제가 프로토콜에 있습니다. 다음은 문제가 되는 흐름입니다:

```solidity
// In PositionInteractionFacet.sol
function _lockLiquidity(Position memory pos) internal {
    PoolStorage.Layout storage strg = PoolStorage.layout();
    if (pos.opType == OptionType.CALL)
        strg.ledger.state.lockedToken[pos.uAsset] += pos.sizeUa;
    else if (pos.opType == OptionType.PUT)
        strg.ledger.state.lockedUSD += pos.sizeUSD;
}

// In StrategyView.sol
function isPayoffAvailable(uint256 positionID, address caller, address /*recipient*/ )
    external
    view
    virtual
    override
    returns (bool)
{
    return strg.setUp.treasury.manager().isApprovedOrOwner(caller, positionID)
        && _calculateStrategyPayOff(positionID) > 0
        && block.timestamp >= maturity
        && block.timestamp <= maturity + strg.setUp.base.exerciseWindowDuration;
}
```

이 문제는 여러 가지 방식으로 나타납니다:

- 옵션 생성 시 유동성이 잠깁니다.
- 유동성은 `exerciseOrLiquidatePosition`을 통해서만 잠금 해제됩니다.
- `exerciseOrLiquidatePosition`은 `payoff` 중에만 호출됩니다.
- `payoff`는 다음 경우에만 사용할 수 있습니다:
  - 옵션이 수익성이 있음 (`_calculateStrategyPayOff > 0`).
  - 행사 창 내에 있음 (만기에서 만기 + exerciseWindowDuration).

이로 인해 유동성이 영구적으로 잠기는 시나리오가 생성됩니다:

- 시간 창 내에 행사되지 않은 옵션.
- 보유자가 행사하지 못한 옵션.

또한 이것은 잠재적인 공격 벡터를 생성합니다:

1. 공격자가 작은 프리미엄으로 여러 옵션을 엽니다.
2. 의도적으로 행사하지 않습니다.
3. 풀 유동성의 영구적 잠금을 유발합니다.

## 권장 사항

이 상황에 대한 잠금 해제 메커니즘을 구현하십시오.

# [C-02] PLP 전송 또는 잔액 업데이트가 보상 인덱스를 업데이트하지 않음 (PLP transfer or balance updates do not update reward index)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`_getPendingRewards`의 보상 계산은 두 가지 주요 요인인 사용자의 PLP 토큰 잔액과 보상 인덱스(`usrRwdIndexQ128`)에 따라 달라집니다.

```solidity
    function _getPendingRewards(address token, address user) internal view returns (uint256) {
        RewardInfo storage rewards = PoolStorage.layout().ledger.state.rewardsByToken[token];
        ILPToken plp_ = ILPToken(PoolStorage.layout().setUp.base.plp);
        uint256 lpBalance = plp_.balanceOf(user);
        uint8 plpDecimals = IERC20Metadata(PoolStorage.layout().setUp.base.plp).decimals();
        uint8 tokenDecimals = IERC20Metadata(token).decimals();

        uint256 rewardIndexQ128 = PoolStorage.layout().ledger.state.usrRwdIndexQ128[token][user];
        rewardIndexQ128 = rewards.rewardsPerLPQ128 - rewardIndexQ128;

        return lpBalance.mulDiv(rewardIndexQ128, Q128).toDecimals(plpDecimals, tokenDecimals);
    }
```

PLP 잔액은 다양한 작업(`addLiquidity`, `withdrawLiquidity` 또는 직접 토큰 전송)을 통해 변경될 수 있지만 보상 인덱스는 특정 시나리오에서만 업데이트됩니다. 이로 인해 `PLP 토큰 전송 또는 기타 잔액 업데이트`(예: `partial liquidity withdraw`) 중에 보상에 대한 잘못된 회계가 발생할 수 있습니다.

```solidity
// Current update points for usrRwdIndexQ128:
// 1. First deposit (_initRewardIndex)
// 2. Full exit (_initRewardIndex)
// 3. Manual claim (_claimRewards)
function _updateLpRewardIndex(address token, address user) internal {
    PoolStorage.layout().ledger.state.usrRwdIndexQ128[token][user] =
        PoolStorage.layout().ledger.state.rewardsByToken[token].rewardsPerLPQ128;
}
```

이러한 경우 사용자는 `usrRwdIndexQ128`을 업데이트하지 않고 `PLP` 토큰을 전송하여 보상을 조작할 수 있습니다:

- 악의적인 사용자는 보상을 청구하기 전에 자신에게 많은 양의 PLP 토큰을 전송할 수 있습니다.
- `lpBalance.mulDiv(rewardIndexQ128, Q128)` 계산은 부풀려진 잔액을 사용합니다.

## 권장 사항

PLP 토큰 잔액이 변경될 때마다 보상 인덱스를 업데이트하는 후크/회계 시스템을 구현하십시오.

# [C-03] 전체 인출 후 `Fenwick tree`에서의 잘못된 인덱스 재설정 (Incorrect index reset in `Fenwick tree` after full withdrawal)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

사용자가 풀에 유동성을 추가하면 시스템은 `_updateLockData` 함수를 트리거하고, 이 함수는 차례로 `Fenwicks._deposit` 함수를 호출하여 입금 금액과 관련 잠금 기간(예: 7일)을 기록합니다. `Fenwicks._deposit` 함수는 Fenwick 트리는 알려진 데이터 구조를 업데이트하여 시간이 지남에 따라 입금 금액을 관리하고 잠급니다.

사용자의 첫 입금(`isDirty=true`)의 경우, 시스템은 잘못된 데이터가 후속 입금에 영향을 미치는 것을 방지하기 위해 트리 값을 재설정합니다:

```solidity
function _fenwicksUpdate(FenwicksData storage self, uint256 maxDepositEntries, uint256 treeIndex, uint256 value)
    internal
{
    // when registering new values after full withdrawal, we need to overwrite dirty values
    bool isDirty = self.sortedKeys.length == 1;
    while (treeIndex <= maxDepositEntries) {
        // when dirty overwrite, otherwise add
@1>      isDirty ? self.fenwicksTree[treeIndex] = value : self.fenwicksTree[treeIndex] += value;
@2>      uint256 nextIndex = treeIndex + (treeIndex & (~treeIndex + 1));
        if (nextIndex > maxDepositEntries) break; // Stop if next update would exceed maxDepositEntries
        treeIndex = nextIndex;
    }
}
```

문제는 1, 2, 4, 8 등과 같은 특정 인덱스만 재설정하고 **3, 5, 6 등과 같은 다른 인덱스는 재설정하지 않기 때문에** 발생합니다.
전체 인출 후 사용자가 이전에 인덱스 3과 5에 입금한 경우 해당 인덱스는 그대로 유지됩니다. 결과적으로 이러한 인덱의 이전 값은 실수로 새 입금 금액에 추가됩니다. 사용자는 더 많은 금액을 잠금 해제할 수 있으며, 실제로 풀에서 다른 사용자의 자금을 얻을 수(`removeLiquidity`) 있습니다.

**시나리오 예:**

1. 초기 입금:
   사용자가 인덱스 1, 2, 3에 입금하고 그에 따라 Fenwick 트리를 업데이트합니다.
2. 전체 인출:
   사용자가 모든 자금을 인출합니다(잠금 기간 후).
3. 인출 후 첫 입금:
   사용자가 다시 입금하며 `isDirty=true`입니다. 인덱스 1, 2, 4, 8, 16 등은 새 값으로 업데이트(재설정)됩니다.
4. 두 번째 입금:
   사용자가 다시 입금하여 새 값으로 인덱스 2를 업데이트합니다.
5. 세 번째 입금:
   사용자가 다시 입금하고 인덱스 3이 업데이트됩니다. 그러나 초기 입금(1단계)의 이전 값이 인덱스 3에 남아 있으므로 새 입금 값이 이전 값에 추가됩니다.

결과:

첫 번째 입금의 이전 값이 인덱스 3에 잘못 유지되므로 사용자는 실제로 입금한 것보다 더 많은 자금을 잠금 해제하게 됩니다. 이를 통해 사용자는 원래 기여하지 않은 자산을 인출할 수 있으며 잠재적으로 다른 사용자의 자금을 인출할 수 있습니다.

## 권장 사항

시스템이 전체 인출 후 1, 2, 4, 8, 16 등과 같은 특정 인덱스뿐만 아니라 Fenwick 트리의 모든 인덱스를 재설정하도록 하십시오.

# [H-01] 옵션 프리미엄 계산이 옵션 가격을 과소책정함 (Option premium calculation underprices options)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

옵션 프리미엄은 입력 `period`를 사용하여 계산되지만 실제 만기일까지의 기간은 해당 날짜의 끝으로 올림되어 일반적으로 주어진 `period`보다 깁니다. 이를 통해 거래자는 비용을 지불한 것보다 더 긴 옵션 기간을 얻을 수 있습니다.

`Strategy._calculatePremium()`의 프리미엄 계산은 초 단위의 입력 `period`를 사용하여 옵션 가격을 계산합니다:

```solidity
function _calculatePremium(uint256 amount, uint256 period, uint256 preciseCurrentPrice, uint256 strike, uint256 iv)
    internal
    view
    returns (uint256 premium)
{
    (uint256 callPremium, uint256 putPremium) =
@>      StrategyStorage.layout().setUp.base.bs.optionPrices(period, iv, preciseCurrentPrice, strike, 0);
```

그러나 만기일은 `PositionParams._computeMaturityDate()`에서 하루의 끝으로 올림됩니다:

```solidity
function _computeMaturityDate(uint256 period) internal view returns (uint32) {
    uint256 maturityTimestamp = block.timestamp + period;
    maturityTimestamp -= (maturityTimestamp % 1 days);
    maturityTimestamp += 1 days - 1;
    return uint32(maturityTimestamp);
}
```

따라서 입력 `period`와 실제 만기일까지의 기간 간의 차이는 최대 `86399`초가 될 수 있으며, 이는 단기 포지션의 경우 상당한 차이가 될 수 있습니다.

다음 시나리오를 고려하십시오:

1. 현재 시간: `1743638400` (2025년 4월 3일 목요일 00:00:00).
2. 사용자 입력 기간 = 7일 = `604800`초.
3. 실제 만기: `1744329599` (2025년 4월 10일 목요일 23:59:59).

입력 기간: `604800`초
실제 기간: `1744329599 - 1743638400` = `691199`초 (7일 + 86399초 ≈ 8일).

프리미엄은 Black-Scholes 공식에서 입력 기간(`604800`초)을 사용하여 계산되므로 만기까지의 시간을 과소평가하여 프리미엄이 낮아집니다.

7일 `CALL` 옵션(3500 달러 현물 가격, 10% 상대 행사가, 48.9% IV)의 예제 출력입니다.
이로 인해 이 `CALL` 옵션 포지션에 대해 약 23%의 과소 가격이 책정됩니다.

```bash
Logs:
  Period: 604800 sec
  Maturity: 1744329599
  Actual Period: 691199 sec
  --- Premium: period ---
  call: 8963759233891000000
  put: 358963759233891000000
  --- Premium: maturity - uint32(block.timestamp)---
  call: 11675104193168500000
  put: 361675104193168500000

  Premium difference: 2711344959277500000
```

## 권장 사항

만기일까지의 실제 기간을 사용하도록 프리미엄 계산을 업데이트하십시오.

# [H-02] `PUT`에 대한 부적절한 유동성 확인으로 인해 행사 실패 발생 가능 (Insufficient liquidity checks for `PUT` may cause exercise failures)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

프로토콜은 `PUT` 포지션을 열고 스테이블코인 인출을 처리할 때 **총 스테이블코인 가치**를 확인합니다. 그러나 **포지션 행사 중 지급에는 특정 스테이블코인(`pos.buyToken`)**이 사용 가능해야 합니다. 이러한 불일치로 인해 전체 풀이 지불 능력이 있는 것처럼 보이더라도 `PUT` 포지션을 행사할 수 없게 될 수 있습니다.

다음 시나리오를 고려하십시오:
가정:

- `OperationalTreasury.baseSetUp.token`: `USDC` (`buyToken`)
- 풀의 스테이블코인: `USDC` (상장됨 & 보상), `USDXL` (상장됨)
- 스테이블 코인 가격은 1:1

#### 시나리오 1: 지급 토큰에 대해 0이 예약된 상태로 PUT 포지션이 열림

가정:

- `poolAmount[USDC]` = 0 USDC -> 0 USD
- `poolAmount[USDXL]` = 3500 USDXL -> 3500 USD
- `_getPoolValue(true, false)` = 0 + 3500 = 3500 USD

1. 사용자가 `PUT` 포지션을 엽니다, 결과 `lockedUSD` = 3500 USD
   - 확인 통과: `availableStable = 3500 ≥ lockedUSD:0 + pos.sizeUSD: 3500`
   - 이를 통해 포지션이 이익일 경우 프로토콜에 `USDC`가 없더라도 `USDC`를 지불하는 `PUT` 포지션을 생성할 수 있습니다.

```solidity
function _checkEnoughLiquidity(
    //...
) internal pure {
    if (pos.opType == OptionType.CALL) {
        --- SNIPPED ---
    } else if (pos.opType == OptionType.PUT) {
@>      if (lockedUSD + pos.sizeUSD > availableStable) {
            revert NotEnoughLiquidityPut(pos.sizeUSD, availableStable, lockedUSD);
        }
    }
}
```

2. 사용자가 자신의 `PUT` 포지션을 행사하려고 시도하지만, 그 당시 `poolAmount[USDC]`가 여전히 0 `USDC`를 보유하고 있다면 `_payout()`은 되돌려집니다.

```solidity
function _payout(Position memory pos, PositionClose memory close) internal returns (uint256 pnl) {
    --- SNIPPED ---
@>  strg.ledger.state.poolAmount[pos.buyToken] -= pnl;
    _doTransferOut(pos.buyToken, msg.sender, pnl);
}
```

#### 시나리오 2: 포지션이 열린 후 LP 인출로 지급 토큰 고갈

가정:

- `poolAmount[USDC]` = 2000 USDC -> 2000 USD
- `poolAmount[USDXL]` = 3500 USDXL -> 3500 USD
- `_getPoolValue(true, false)` = 2000 + 3500 = 5500 USD

1. 사용자가 `PUT` 포지션을 엽니다, 결과 `lockedUSD` = 3500 USD
   - 확인 통과: `availableStable = 5500 ≥ lockedUSD:0 + pos.sizeUSD: 3500`
2. 유동성 제공자가 유동성을 제거하고 토큰 출력을 `USDC`로 요청합니다.
   - LP 금액 가치 2000 `USDC` 등가
   - `_checkWithdrawlImpact()` 확인: `newUSDValuation = 5500 - 2000 = 3500 USD`, 이는 `newUSDValuation: 3500 >= lockedUSD: 3500`
   - 프로세스는 2000 `USDC` 금액 인출을 허용합니다.
   - `poolAmount[USDC]` = 2000 - 2000 = 0 `USDC`

```solidity
function _checkWithdrawlImpact(
    //...
) internal view {
    --- SNIPPED ---
    else if (strg.setUp.assets.stablecoins.contains(_token)) {
        uint8 decimals = IERC20Metadata(_token).decimals();
        uint256 withdrawUSD = (_amount * _assetPrice).toDecimals(decimals + 8, 18);
@>      uint256 newUSDValuation = _getPoolValue(true, false) - withdrawUSD;

@>      if (newUSDValuation < strg.ledger.state.lockedUSD) revert PoolErrors.InsufficientPoolAmount(_token);
    }
}
```

3. 사용자가 `PUT` 포지션을 행사하려고 시도하지만 `poolAmount[USDC]` = 0 `USDC`이므로 `_payout()`이 되돌려집니다.

## 권장 사항

`PUT` 포지션 개설 및 인출 영향 확인 모두에서 buyToken(스테이블코인)별 잠금 금액을 추적하고 개별 잔액에 대해 검증하십시오.

# [H-03] `removeLiquidity()`의 부정확한 회계로 인한 유동성 과대계상 (Inaccurate accounting in `removeLiquidity()` overstating liquidity)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`LiquidityFacet.removeLiquidity()`는 수수료 금액(`pFee`)을 별도로 추적하면서 순 금액(`netOutAmount`)만 차감하여 풀의 토큰 잔액을 잘못 업데이트합니다.

전체 인출 금액(`outAmount = netOutAmount + pFee`)이 풀의 토큰 잔액에서 인출되지만 `poolAmount`의 회계 잔액에서는 `netOutAmount`만 차감되기 때문에 불일치가 발생합니다.

다음 예를 고려하십시오:

1. 사용자가 `_lpAmount` LP 토큰으로 `LiquidityFacet.removeLiquidity()`를 호출합니다.
2. 프로토콜 계산:
   - `outAmount` = 인출할 총 토큰
   - `pFee` = 프로토콜 수수료
   - `netOutAmount = outAmount - pFee` (수수료 후 인출할 총 토큰)
3. 프로토콜 상태 업데이트:

```solidity
function _removeLiquidityFromPool(
    ...
) internal {
    --- SNIPPED ---
@>  strg.ledger.protocolFee[_tokenOut] += pFee;
@>  strg.ledger.state.poolAmount[_tokenOut] -= netOutAmount;
    _refreshVirtualPoolValue();
}
```

4. 풀에서 추출된 실제 토큰은 `netOutAmount + pFee: outAmount`이지만 `poolAmount`에서는 `netOutAmount`만 차감됩니다. 이로 인해 `poolAmount`가 `pFee`만큼 부풀려집니다.

## 권장 사항

`removeLiquidity` 함수는 `poolAmount`에서 전체 금액을 차감하고 `protocolFee`에 일부 수수료를 유지하는 것이므로 `poolAmount`는 `outAmount: netOutAmount + pFee`만큼 차감되어야 합니다.

# [H-04] `PositionInteractionFacet`에서 잠긴 자금을 유동성으로 부적절하게 사용 (Improper use of locked funds as liquidity in `PositionInteractionFacet`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

누군가 콜 옵션을 행사하면 `buyToken`으로 이익(`pnl`)을 받습니다. 계약은 이 지급을 위해 사용 가능한(잠기지 않은) 토큰만 사용하거나 필요한 경우 자산을 스왑해야 합니다.
`_payout()` 함수는 옵션 지급을 충당하기 위해 청구되지 않은 LP 보상(프리미엄)을 사용하려고 시도합니다:

```solidity
    function _payout(Position memory pos, PositionClose memory close) internal returns (uint256 pnl) {
        --snip--
        uint256 availablePayoutToken = strg.ledger.state.rewardsByToken[pos.buyToken].totalRewards;

        pnl = close.pnl;

        if (pos.opType == OptionType.CALL && availablePayoutToken < pnl) {
            uint256 swapOut = pnl - availablePayoutToken;
            _swapAssetToAsset(pos.uAsset, pos.buyToken, swapOut);
        }
        --snip--

        strg.ledger.state.poolAmount[pos.buyToken] -= pnl;
        _doTransferOut(pos.buyToken, msg.sender, pnl);
    }
```

그러나 이 접근 방식은 근본적으로 결함이 있습니다:

1. **보상은 이미 분배됨**: 보상 시스템은 인덱스를 업데이트하여 작동합니다. 즉, 보상이 아직 청구되지 않았더라도 발생하는 즉시 사용자에게 분배된 것으로 간주됩니다.
2. **실제 토큰 예약 없음**: 계약은 `rewardsByToken[pos.buyToken].totalRewards`를 사용 가능한 유동성으로 잘못 취급합니다. 이 자금은 이미 사용자에게 할당되어 있고 일부 사용자는 보상을 청구했을 수도 있습니다.

결과적으로 Call 옵션을 지급할 때 계약은 사용자의 이익(pnl)을 충당하기 위해 `buyToken` 잔액의 일부(totalRewards와 동일한 금액)를 잘못 사용합니다. 그러나 Put 옵션 지급을 이행하기 위해 잠겨 있고 예약된 `buyToken` 부분을 고려하지 않습니다.

## 권장 사항

Call 옵션 지급의 적절한 처리를 보장하기 위해 여러 솔루션을 고려할 수 있습니다:

1. Call 옵션 지급에 사용 가능한 `buyToken`을 사용하지 마십시오. 대신 필요에 따라 `uAssets`를 스왑하십시오.
2. `buyToken`의 사용 가능 잔액과 잠긴 잔액을 모두 추적하십시오.
3. `총 스테이블코인 가치` 및 `lockedUsd`에 대한 확인을 통합하고 지급 프로세스의 일부로 스테이블코인을 `buyToken`으로 스왑하는 것을 고려하십시오.

# [H-05] 현물 가격으로 인한 `PositionInteractionFacet` 슬리피지 제어의 취약점 (Vulnerability in `PositionInteractionFacet` slippage control due to spot price)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`_swapAssetToAsset` 함수는 토큰 스왑을 수행할 때 `amountInMaximum`을 계산하기 위해 Uniswap 풀의 현물 가격을 사용합니다:

```solidity
 function _swapAssetToAsset(
        address assetIn,
        address assetOut,
        uint256 amountOut // in assetOut decimals
    ) internal returns (uint256 amountIn) {
        PoolStorage.Layout storage strg = PoolStorage.layout();
        PoolStorage.Dex memory dex = strg.setUp.base.dex;
        uint160 sqrtPriceLimitX96 = 0;

        IQuoterV2.QuoteExactOutputSingleParams memory quoteParams = IQuoterV2.QuoteExactOutputSingleParams({
            tokenIn: assetIn,
            tokenOut: assetOut,
            amount: amountOut,
            fee: dex.dexFee,
            sqrtPriceLimitX96: sqrtPriceLimitX96
        });
        (uint256 amountInMaximum,,,) = IQuoterV2(dex.dexQuoter).quoteExactOutputSingle(quoteParams);
        amountInMaximum = _applySlippage(amountInMaximum);

        IERC20Metadata(assetIn).safeIncreaseAllowance(dex.dexRouter, amountInMaximum);

        --snip--
    }
```

이는 악의적인 행위자가 자신의 트랜잭션을 실행하여 현물 가격을 조작할 수 있기 때문에 계약을 프런트 러닝 및 샌드위치 공격에 노출시킵니다. 이는 다음과 같이 발생합니다:

1. 공격자는 `exerciseOrLiquidatePosition`의 트랜잭션 흐름과 그에 따른 `_swapAssetToAsset` 호출을 관찰할 수 있습니다.
2. 공격자는 트랜잭션을 프런트런하고 Uniswap 풀의 현물 가격을 조작할 수 있습니다.
3. 계약은 `quoteExactOutputSingle`을 호출하여 산출 토큰 금액(`amountOut`)을 충족하는 데 필요한 입력 토큰 금액(`amountInMaximum`)을 결정합니다. 이 계산은 조작된 현물 가격을 기반으로 합니다.
4. 계약은 슬리피지 허용 오차(`_applySlippage`)를 적용하여 스왑 계산에 오차 범위를 허용합니다. 그러나 현물 가격이 이미 공격자에게 유리하도록 조작되었으므로 슬리피지를 적용하면 공격자에게 더 많은 이익이 됩니다.

결과적으로 공격자는 현물 가격을 자신에게 유리하게 조작하여 계약이 공격자에게 유리한 비율로 토큰을 스왑하도록 하여 계약에 손실을 입힐 수 있습니다.

## 권장 사항

가격 조작이 계약에 영향을 미치는 것을 방지하기 위해 오라클이나 TWAP 소스를 사용하는 것과 같은 보다 안전한 가격 계산 방법을 사용하는 것을 고려하십시오.

# [H-06] `OperationalTreasury`의 `payOff()` 언더플로우가 자산을 영구적으로 잠금 (`payOff()` underflow in `OperationalTreasury` locks assets permanently)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`OperationalTreasury` 계약의 `payOff()` 함수는 실제 이익(`pnl`)을 고려하지 않고 전체 포지션 크기와 현재 가격을 기준으로 `feesUSD`를 계산합니다. 구체적으로:

- `feesUSD = positionAmount × FeePercentage × CurrentPrice`
- `pnl = positionAmount × (CurrentPrice - StrikePrice)`

즉, `CurrentPrice`가 `StrikePrice`보다 약간 높은 포지션의 경우 계산된 `feesUSD`가 `pnl`을 초과할 수 있습니다. 이러한 경우 `netAmount = pnl - feesUSD` 라인이 **언더플로우**됩니다:

```solidity
    function payOff(uint256 positionID, address account) external override nonReentrant whenNotPaused {
        --snip--
@2>     IPositionInteraction(baseSetUp.pool).exerciseOrLiquidatePosition(close);

        uint256 feesUSD = _calculateFeesUSD(uAsset, uint256(amount)).toDecimals(18, baseSetUp.token.decimals());
@1>     uint256 netAmount = pnl - feesUSD;

        // fees deduced from PnL
        strg.ledger.collectedFees[address(baseSetUp.token)] += feesUSD;
        --snip--
    }
```

`@1>`에서 언더플로우로 인해 `payOff()`가 되돌려지면 `@2>`의 `exerciseOrLiquidatePosition()` 호출이 실행되지 않습니다. 결과적으로 **풀의 기본 자산은 영구적으로 잠긴 상태로 유지됩니다**.

## 권장 사항

언더플로우를 방지하고 포지션 종료를 허용하기 위해 수수료가 실제 pnl보다 크지 않도록 제한하십시오:

```solidity
uint256 feesUSD = _calculateFeesUSD(...);
if (feesUSD > pnl) {
    feesUSD = pnl;
}
uint256 netAmount = pnl - feesUSD;
```

# [M-01] 하드코딩된 슬리피지 허용 오차는 열악한 거래 또는 DOS 위험을 초래함 (Hardcoded slippage tolerance risks poor trading or DOS)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`PositionInteractionFacet.sol` 계약에서 슬리피지 허용 오차는 `_applySlippage` 함수에서 2%로 하드코딩되어 있습니다:

```solidity
function _applySlippage(uint256 amountIn) internal pure returns (uint256) {
    // 2% slippage from quoter estimation
    return amountIn + (amountIn * 20_000) / DEX_PRECISION;
}
```

이 구현은 몇 가지 중요한 문제를 제시합니다:

1. 고정된 2% 슬리피지 허용 오차는 다양한 시장 상황과 유동성 풀 깊이를 고려하지 않습니다.
2. 시장 변동성이 큰 기간 동안 이 고정된 허용 오차는 불충분하여 트랜잭션 실패(DOS)로 이어질 수 있습니다.
3. 유동성이 높은 풀의 경우 2% 슬리피지는 불필요하게 높아 사용자의 실행 가격이 악화됩니다.
4. 유동성이 낮은 풀의 경우 2%가 너무 낮아 더 높은 허용 오차로 성공할 수 있었던 트랜잭션이 실패할 수 있습니다.

## 권장 사항

관리자/소유자가 허용 오차를 설정할 수 있도록 허용하십시오.

# [M-02] 덱스 스왑 마감일에 `block.timestamp` 사용은 열악한 거래를 유발할 수 있음 (`block.timestamp` use in dex swap deadlines may cause poor trading)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

PositionInteractionFacet.sol 계약에서 DEX 스왑을 실행할 때 `deadline` 매개변수는 `block.timestamp + 30 minutes`로 설정됩니다:

```solidity
IV3SwapRouter.ExactOutputSingleParams memory params = IV3SwapRouter.ExactOutputSingleParams({
    tokenIn: assetIn,
    tokenOut: assetOut,
    fee: dex.dexFee,
    recipient: address(this),
    deadline: block.timestamp + 30 minutes,  // Unnecessary buffer
    amountOut: amountOut,
    amountInMaximum: amountInMaximum,
    sqrtPriceLimitX96: sqrtPriceLimitX96
});
```

이 구현은 여러 가지 이유로 문제가 있습니다:

1. 트랜잭션은 어차피 현재 `block.timestamp`에서 실행되므로 `30분` 버퍼는 불필요합니다.
2. 이 확장된 마감일 창은 악의적인 MEV 봇이 잠재적으로 트랜잭션을 보류하고 덜 최적의 시간에 실행할 수 있도록 허용합니다.
3. 이 구현은 실제 실행 시간이 네트워크의 블록 생성에 의해 결정되므로 트랜잭션 지연에 대한 실질적인 보호를 제공하지 않습니다.

## 권장 사항

현재 마감일 구현을 사용자 입력 `deadline`으로 교체하십시오.

# [M-03] 수수료 계산이 풀 영향에 대해 순 금액 대신 전체 금액을 사용함 (Fee calculation uses full instead of net amount for pool impact)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`_calcAddLiquidity` 함수에는 수수료 계산에 사용된 금액(`_amountIn`)과 풀에 실제로 추가된 금액(`netAmount`) 사이에 불일치가 존재합니다. 이 문제는 다음 순서에서 발생합니다:

```solidity
// In _calcAddLiquidity:
valueChange = (_amountIn * tokenPrice).toDecimals(tokenDecimals + provDecimals, 18);
(, pFee) = _calcFeeRate(_token, _amountIn, valueChange, baseSetUp.addRemoveLiquidityFee, true);
netAmount = _amountIn - pFee;
```

`valueChange`는 수수료가 공제되기 전의 전체 `_amountIn`을 사용하여 계산됩니다. 그런 다음 이 값은 `_calcFeeRate`에서 사용되어 `_calcDelta` 함수를 통해 풀의 토큰 비율 영향을 결정합니다.
그러나 풀에 추가되는 실제 금액은 `netAmount` (`_amountIn - pFee`)입니다.

이것은 다음 사이에 불일치를 생성합니다:

1. 수수료 계산에 사용된 영향 평가 (전체 금액 기준).
2. 풀에 대한 실제 영향 (수수료 후 순 금액 기준).

예를 들어 사용자가 3% 수수료로 `1000` 토큰을 추가하는 경우:

- 수수료 계산은 `1000` 토큰을 사용하여 풀 영향을 평가합니다.
- 그러나 실제로는 `970` 토큰만 풀에 추가됩니다.
- 이는 실제 풀 영향이 수수료 계산에 사용된 것보다 적기 때문에 잘못된 수수료 계산으로 이어집니다.

## 권장 사항

유동성 제공자에게 `_amountIn+pFee`를 청구하십시오.

# [M-04] 오라클 계약에서 가격 피드 검증 누락 (Missing price feed validation in oracle contract)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Oracle` 계약의 `_getLastPrice` 함수는 중요한 유효성 검사를 수행하지 않고 오라클 가격 피드에서 가격 데이터를 검색하므로 프로토콜에서 오래되거나 유효하지 않은 가격 데이터가 사용될 수 있습니다.

```solidity
    function _getLastPrice(address token) internal view returns (uint256 price, uint256 timestamp) {
        (, int256 answer, /* uint256 startedAt */, uint256 updatedAt,) = tokenOracle[token].latestRoundData();

        price = answer.toUint256();
        timestamp = updatedAt;
    }
```

이 취약점은 다음 영역에 존재합니다:

- 최신성 확인(Staleness Check):
  - 함수는 `updatedAt` 타임스탬프를 현재 `block.timestamp`와 비교하여 가격 데이터가 최신인지 확인하지 않습니다.
  - 중요한 프로토콜 작업에 오래된 가격 데이터가 사용될 수 있습니다.
- 가격 유효성:
  - 응답 값이 `breaker`에 의해 정의된 `minAnswer` 아래로 내려가지 않도록 하는 유효성 검사가 없습니다.
- L2 관련 우려:
  - L2 네트워크에 배포될 때 계약에는 시퀀서 가동 시간 유효성 검사가 부족합니다 (검증 필요).

## 권장 사항

`_getLastPrice` 함수에 포괄적인 가격 피드 유효성 검사를 구현하십시오.

# [M-05] 잠금 데이터 전송 없는 Lp 토큰 전송 (Lp token transfer without lock data transfer)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`LPToken` 계약은 LP 토큰이 주소 간에 자유롭게 전송될 수 있도록 허용합니다. 그러나 프로토콜은 토큰 자체가 아니라 원래 예금자의 주소를 사용하여 풀 계약의 유동성 잠금 데이터를 추적합니다. LP 토큰이 새 주소로 전송되면 잠금 데이터는 원래 예금자에게 남아 있어 새 보유자가 기본 자산을 상환할 수 없습니다.

개념 증명(Proof of Concept):

1. Alice는 1000 USDC를 입금하고 7일 잠금이 있는 1000 LP 토큰을 받습니다:
   - Alice의 총 잠금: 1000 LP.
2. Alice는 500 LP 토큰을 Bob에게 전송합니다:
   - Bob의 총 잠금: 0 LP.
   - Alice의 총 잠금 여전히: 1000 LP.
3. 7일 후 Bob은 500 LP 토큰을 상환하려고 시도합니다:
   - Bob에게 잠금 데이터가 없으므로 `500 LP > _getUserUnlockedLP(bob): 0`이기 때문에 트랜잭션이 되돌려집니다.
4. LP 토큰은 Bob이 남은 500 LP 토큰을 Alice에게 다시 전송하지 않는 한 Alice(더 이상 토큰을 소유하지 않음)나 Bob(잠금 데이터 없음) 모두 상환할 수 없으므로 잠기게 됩니다.

## 권장 사항

LP 토큰 전송을 완전히 허용하지 않거나 잠금 데이터를 전송 가능한 방식으로 관리하도록 시스템을 재설계하는 것을 고려하십시오.

# [M-06] 제한 없는 LP 소각으로 예금을 악용하기 위한 가격 인플레이션 가능 (Unrestricted LP burn enables price inflation to exploit deposits)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`LPToken` 계약은 `burn()` 및 `burnFrom()`을 통해 LP 토큰의 무제한 소각을 허용합니다. 이를 통해 첫 번째 예금자가 자신의 LP 토큰을 거의 모두 소각하여 LP 가격을 조작하는 공격이 가능합니다. 이로 인해 풀 상태가 왜곡되어 후속 예금이 **더 적거나 0인 LP 토큰**을 발행하게 되어 서비스 거부(슬리피지 확인이 사용되는 경우) 또는 조용한 자금 손실(그렇지 않은 경우)이 발생합니다.

```solidity
function _calcAddLiquidity() internal view returns (uint256 netAmount, uint256 pFee, uint256 lpAmount) {
    --- SNIPPED ---
    if (lpSupply == 0) {
        lpAmount = netAmount * tokenPrice;
        lpAmount = lpAmount.toDecimals(tokenDecimals + 8, plpDecimals);
    } else {
@1>     lpAmount = netAmount.mulDiv(tokenPrice * lpSupply, _getPoolValue(false, false) /*18, USD*/);
@2>     lpAmount = lpAmount.toDecimals(tokenDecimals + 8, plpDecimals);
    }
}
```

```solidity
//> src/utils/tokens/lp/LPToken.sol
function burn(uint256 value) public override(ILPToken, ERC20Burnable) {
    ERC20Burnable.burn(value);
}

function burnFrom(address _account, uint256 _amount) public override(ILPToken, ERC20Burnable) {
    ERC20Burnable.burnFrom(_account, _amount);
}
```

다음 시나리오를 고려하십시오: 0. 초기 상태: - ETH 오라클 가격: 1500e8 (8 소수점, 1500 USD). - USDC 오라클 가격: 1e8 (8 소수점, 1 USD). - LP 토큰 소수점: 18. - 0% 수수료.

1. Bob은 1 ETH 유동성을 추가하려고 시도하며, 이는 1500 USD에 해당합니다.
2. Alice는 1501 USDC로 Bob의 입금을 프런트런하며, 이는 1501 USD에 해당합니다:
   - LP 발행 금액: `1501e6 * 1e8 = 1501e14` -- 18 소수점으로 --> `1501e14 * 1e(18-14) = 1501e18 LPs`.
   - PoolValue: 1501e18 ≈ 1501 USD.
3. Alice는 1 wei의 LP 토큰을 제외하고 모두 소각하므로 이제 1 LP는 1501 USD의 가치가 있습니다.
4. Bob의 트랜잭션이 실행됩니다:
   - PoolValue: 1501e18 ≈ 1501 USD.
   - LP 발행 금액: `(1e18 * 1500e8) / 1501e18 = 99999999` -- 18 소수점으로 --> `99999999 / 1e(26-18) = 99999999 / 1e8 = 0 (내림)`.
   - `lpAmount` 계산의 내림으로 인해 `lpAmount`가 0이 됩니다.
5. Bob은 또한 슬리피지 `_minLpAmount`를 지정합니다. `_minLpAmount`가 지정되면 이 공격은 DoS를 생성합니다. 그렇지 않으면 이 공격은 Bob의 예금을 훔칩니다.

## 권장 사항

소각을 프로토콜 역할로 제한하십시오: 유동성 제거를 위한 풀 계약 또는 `lpSupply`가 최소 LP 공급보다 적은 경우 공개 `burn()` 및 `burnFrom()` 허용 안 함.

# [M-07] 풋 옵션 가격의 반올림 오류로 안전 마진 감소 (Rounding error in put option price reduces safety margin)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

프로토콜은 `CALL` 및 `PUT` 옵션 모두에 동일한 행사가 반올림 로직을 적용합니다. 이는 `CALL` 옵션의 경우 프로토콜에 유리하게 작용하지만 `PUT` 옵션의 경우 안전성을 감소시킵니다.

구체적으로 행사가격은 먼저 현재 가격에 상대적 할인을 적용하여 계산하고(PUT의 경우), `_roundPrice()`로 전달되는데, 이 함수는 나머지가 중간점을 초과하면 **위로** 반올림합니다. 이는 행사가격을 **가격 내(In-The-Money)에 더 가깝게 또는 그 안으로** 밀어 넣어 PUT 옵션에 의도된 외가격(OTM) 버퍼를 줄입니다.

```solidity
function _calculateStrike(uint256 preciseCurrentPrice, uint256 relativeStrike) internal view returns (uint256) {
    uint256 calculatedStrike = preciseCurrentPrice - ((preciseCurrentPrice * relativeStrike) / 100);
@>  return _roundPrice(calculatedStrike);
}
```

```solidity
function _roundPrice(uint256 price) internal view returns (uint256) {
    RoundingOption currentRoundingOption_ = StrategyStorage.layout().setUp.base.currentRoundingOption;
    uint256 remainder;
    uint256 roundedDown;
    if (currentRoundingOption_ == RoundingOption.NEAREST_HUNDRED) {
        remainder = price % (100 * 1e18);
        roundedDown = price - remainder;
@>      if (remainder >= 50 * 1e18) return roundedDown + (100 * 1e18);
        else return roundedDown;
    } else if (currentRoundingOption_ == RoundingOption.NEAREST_TEN) {
        remainder = price % (10 * 1e18);
        roundedDown = price - remainder;
@>      if (remainder >= 5 * 1e18) return roundedDown + (10 * 1e18);
        else return roundedDown;
    } else {
        return price;
    }
}
```

`NEAREST_HUNDRED` 반올림을 사용하는 이 시나리오를 고려하십시오:
**시나리오 1:**

- 현물 가격: 1000 USD.
- 상대 행사가: 5%.
- 원시 PUT 행사가 = 950 USD.
- 반올림된 행사가 = 1000 USD (OTM 대신 ATM).

**시나리오 2:**

- 현물 가격: 3500 USD.
- 상대 행사가: 10%.
- 원시 PUT 행사가 = 3150 USD.
- 반올림된 행사가 = 3200 USD (350 OTM 대신 300 (~8.25% 상대 행사가) OTM).

두 경우 모두 위로 반올림하면 PUT 포지션에 대한 행사가와 현물 가격 간의 거리가 줄어듭니다.

## 권장 사항

상반된 위험 의미를 반영하기 위해 `CALL` 및 `PUT` 옵션에 대해 다른 반올림 방향을 사용하십시오:

- `CALL` 옵션의 경우 행사가를 계속 올리면 더 외가격이 되고 프로토콜에 이익이 됩니다.
- `PUT` 옵션의 경우 대신 내림하여 행사가가 현재 가격에서 더 멀리 떨어져 있도록 하고 의도치 않게 거래자에게 유리하지 않도록 하십시오.

# [M-08] 약한 서명 검증으로 다중 옵션 잘못된 가격 책정 벡터 허용 (Weak signature validation allows multiple option mispricing vectors)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

프로토콜의 서명 검증 메커니즘은 기간 조작 및 오래된 서명을 통해 옵션 잘못된 가격 책정을 가능하게 합니다:

1. **기간 및 만기 불일치**

```solidity
function _hash(...) internal pure returns (bytes32) {
@>  return keccak256(abi.encode(iv, uAsset, oType, maturityDate, relativeStrike));
}

function iv(uint48 period, OptionType oType, uint256 strikePrice) external view returns (uint256 value) {
@>  value = _ivValues[period][oType][strikePrice]; // IV is queried based on `period`, `oType` and `strikePrice`
}
```

2. **누락된 서명 마감일**

```solidity
function _recoverSigner(...) internal view returns (address) {
@>  address recoveredSigner = _recoverSigner(sig, iv, strategy, period._computeMaturityDate(), relativeStrike);
}
```

이는 두 가지 주요 공격 벡터를 가능하게 합니다:

1. **기간 조작**

- 사용자가 4월 3일에 14일 기간에 대한 서명을 요청합니다(만기: 4월 17일 23:59).
- 4월 10일에 사용자는 7일 기간으로 옵션을 실행합니다(만기: 4월 17일 23:59, 동일한 만기 결과).
- 프로세스는 14일 기간을 위해 의도된 IV로 이 포지션을 검증하여 옵션 가격을 잘못 책정합니다.

2. **오래된 행사가**

- 사용자가 현물 가격 3500 달러(행사가 = 3850 달러)에서 10% 상대 행사가에 대한 서명을 요청합니다.
- 사용자는 시장이 4200 달러와 같은 어떤 현물 가격으로 움직일 때까지 기다립니다(새 행사가 = 4620 달러).
- 사용자는 행사가 3850 달러에 대한 IV를 여전히 사용하여 오래된 서명으로 옵션을 실행합니다.
- 프로토콜은 오래된 IV를 적용하여 옵션 가격을 잘못 책정합니다.

## 권장 사항

모든 중요한 매개변수를 포함하고 서명 만료 검증을 시행하십시오:

```diff
function _hash(...) internal pure returns (bytes32) {
    return keccak256(abi.encode(
        iv,
        uAsset,
        oType,
+       period,
        maturityDate,
        relativeStrike,
+       deadline
    ));
}
```

# [M-09] `strategy` 계약: 프리미엄 계산에서 행사 창 무시됨 (`strategy` contracts: exercise window ignored in premium calculation)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

프로토콜은 현재 옵션이 **만기일 이후 창**(즉, `exerciseWindowDuration`) 내에 행사될 수 있도록 허용합니다:

```solidity
    function isPayoffAvailable(uint256 positionID, address caller, address /*recipient*/ )
        external
        view
        virtual
        override
        returns (bool)
    {
        --snip--
        return strg.setUp.treasury.manager().isApprovedOrOwner(caller, positionID)
            && _calculateStrategyPayOff(positionID) > 0 && block.timestamp >= maturity
@>          && block.timestamp <= maturity + strg.setUp.base.exerciseWindowDuration;
    }
```

그러나 **프리미엄 계산**은 이 연장된 행사 창을 고려하지 않습니다. 만기일의 가격이 옵션의 가치를 계산하는 데 사용되어야 하지만 만기일 이후의 현재 가격이 대신 사용되고 있기 때문에 불일치가 발생합니다.
옵션 구매자는 만기일 이후 옵션 가격이 상승할 때까지 기다렸다가 더 유리한 가격으로 옵션을 행사하여 긍정적인 손익(PnL)을 얻을 수 있습니다.

**참고:** 프로토콜은 **유럽형 옵션**과 그 가격 모델을 사용하는데, 이는 옵션이 **만기일 가격**에서만 행사되어야 한다고 가정합니다. 그러나 현재 전략 계약에서는 만기일 이후 일정 기간(`exerciseWindowDuration`) 내에 행사가 허용됩니다. 옵션은 만기일 가격이 아닌 현재 가격을 기준으로 지급됩니다.

**참고:** 행사 창 기간을 줄이면 사용자가 `payOff`를 호출할 기회를 놓치고 잠재적 이익을 잃을 위험이 증가합니다.

### 시나리오 예 (테스트 및 스크립트 번호 기준):

- **옵션 기간** = 7일
- **행사 창 기간** = 3일 (원래 기간의 50% 연장)

사용자는 7일 옵션에 대한 프리미엄을 지불하지만 만기일 이후 3일의 추가 창을 통해 옵션을 행사할 수 있습니다. 이 기간 동안 사용자는 가격이 상승할 때까지 기다렸다가 옵션을 행사하여 이익을 극대화할 수 있습니다. 그러나 프리미엄은 이 추가 시간을 고려하도록 조정되지 않습니다.

## 권장 사항

이 문제를 해결하려면 다음 대안을 고려하십시오:

1. **항상 만기일 가격으로 행사**: 과거 가격 오라클을 사용하여 옵션이 만기일 가격으로 행사되도록 보장합니다.

2. **프리미엄 계산에 행사 창 기간 포함**: `exerciseWindowDuration`에 의해 제공되는 시간 연장을 공정하게 반영하도록 프리미엄을 조정하여 프리미엄이 옵션의 원래 가치와 일치하도록 하고 조작 가능성을 제거합니다.

대안 1은 *유럽형 옵션 가격 모델*과 더 일치하며 옵션을 행사하기 위해 `payOff`를 호출하는 엄격한 창을 가질 필요성을 제거합니다.

# [M-10] `liquidityFacet`: 다중 입금을 통한 펜윅 트리 공격 (`liquidityFacet`: fenwick tree attack through multiple deposits)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`addLiquidity()` 함수의 현재 구현에서 악의적인 사용자는 펜윅 트리 구조를 악용하여 다른 사용자를 대신하여 유동성을 여러 번 추가함으로써 트리를 채우고 합법적인 입금이 발생하는 것을 효과적으로 막을 수 있습니다.
펜윅 트리에는 `maxDepositEntries` 제한이 있으며 이 제한에 도달하면 더 이상 항목을 추가할 수 없습니다. 이를 통해 공격자가 단일 사용자에 대해 여러 입금을 추가하여 트리가 재설정될 때까지 합법적인 사용자가 유동성을 입금하지 못하게 할 수 있는 그리핑(griefing) 동작이 가능합니다.

```solidity
    function _deposit(FenwicksData storage self, uint256 maxDepositEntries, uint256 lockDuration, uint256 amount)
        internal
    {
        uint256 treeIndex = _registerKey(self, maxDepositEntries, block.timestamp + lockDuration);
        _fenwicksUpdate(self, maxDepositEntries, treeIndex, amount);
        self.totalMintedPLP += amount;
    }

    function _registerKey(FenwicksData storage self, uint256 maxDepositEntries, uint256 newTimestamp)
        internal
        returns (uint256 treeIndex)
    {
        uint256 keyCount = self.sortedKeys.length;

@>      if (keyCount >= maxDepositEntries) revert Fenwicks_TooManyKeys();

        self.sortedKeys.push(newTimestamp);
        self.keyToIndex[newTimestamp] = ++keyCount;

        treeIndex = self.keyToIndex[newTimestamp];
    }
```

사용자는 모든 유동성을 인출하여 트리를 재설정할 수 있지만 이 작업은 잠금 기간이 지난 후에만 수행할 수 있으므로 악의적인 행동이 지속될 수 있는 기간이 길어질 수 있습니다.

## 권장 사항

무단 액세스를 방지하기 위해 유동성 추가 시 `msg.sender`를 확인하십시오.

# [M-11] `liquidityFacet`: 다른 사용자에 대한 무단 유동성 제거 (`liquidityFacet`: unauthorized liquidity removal for other users)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`removeLiquidity()` 함수는 현재 작업을 수행하는 사용자에게 액세스를 적절하게 제한하지 않기 때문에 모든 사용자가 함수를 호출하고 다른 사용자를 대신하여 유동성을 제거할 수 있도록 허용합니다. 이는 다른 사용자의 계정에서 무단으로 유동성을 제거하여 특히 악의적인 행위자가 함수를 악용하려고 시도하는 시나리오에서 자산 손실을 초래할 수 있습니다.

```solidity
    function removeLiquidity(address _tokenOut, uint256 _lpAmount, uint256 _minOut, address _to)
        external
        override
        nonReentrant
        whenNotPaused
    {
        _requireAmount(_lpAmount);
        _validateAsset(_tokenOut);

        if (_lpAmount > _getUserUnlockedLP(_to)) revert PoolErrors.LiquidityLocked();

        // remove liquidity
        (, uint256 netOutAmount, uint256 pFee, uint256 tokenOutPrice) = _calcRemoveLiquidity(_tokenOut, _lpAmount);
        _removeLiquidityFromPool(_minOut, netOutAmount, _tokenOut, tokenOutPrice, pFee);

        uint256 rewardsTokenOut = _isFullyExiting(_to, _lpAmount) ? _claimAllRewards(_to, _tokenOut) : 0;

        // PLP management
        _updateLockData(false, _lpAmount, _to);
        _exitAndBurnPLP(_lpAmount, _to);

        _doTransferOut(_tokenOut, _to, netOutAmount + rewardsTokenOut);
        _emitPLPPrice();
        emit LiquidityRemoved(_to, _tokenOut, _lpAmount, netOutAmount, rewardsTokenOut, pFee);
    }
```

## 권장 사항

유동성을 소유한 사용자만 유동성을 제거할 수 있도록 액세스 제어를 구현하십시오. 이는 유동성 제거를 진행하기 전에 `_to` 주소가 호출자(`msg.sender`)와 같은지 확인하여 수행할 수 있습니다.

# [M-12] `operationalTreasury`: 기본 토큰에 대해 1:1 USD 가치를 가정함 (`operationalTreasury`: assumes 1:1 USD value for base token)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`OperationalTreasury`에서 프로토콜은 USD로 수수료와 프리미엄 금액을 계산하지만 `baseSetUp.token`(매수 토큰) 가치가 항상 1 USD와 같다고 가정합니다. 이 가정은 토큰의 가치가 1 USD 미만이거나 더 나아가 페깅이 해제되는 경우 잘못된 자금 수집으로 이어질 수 있습니다.

`_takeFeesLockLiquidity` 함수:

```solidity
function _takeFeesLockLiquidity(Position memory pos, uint256 amount, uint256 premium, uint8 tokenDecimals)
    internal
    returns (uint256 fees)
{
    OperationalTreasuryStorage.BaseSetUp memory baseSetUp = OperationalTreasuryStorage.layout().setUp.base;

    // fees and transfer funds here
    {
        fees = _calculateFeesUSD(pos.uAsset, amount).toDecimals(18, tokenDecimals);

        // fees added on top of premium
        OperationalTreasuryStorage.layout().ledger.collectedFees[address(baseSetUp.token)] += fees;

        // funds transfer from buyer to treasury. Premium transferred to pool, fees stay in treasury
        baseSetUp.token.safeTransferFrom(msg.sender, address(this), premium + fees);
    }

    // lock liquidity in pool
    {
        baseSetUp.token.safeIncreaseAllowance(baseSetUp.pool, premium);
        IPositionInteraction(baseSetUp.pool).openPosition(pos);
    }
}
```

현재 구현에서:

- 수수료는 18 소수점의 USD로 계산됩니다.
- 그런 다음 .toDecimals()를 사용하여 토큰 소수점(예: 6 또는 18)으로 변환됩니다.
- 기본 토큰의 양은 msg.sender로부터 전송됩니다.
- 그러나 기본 토큰과 USD 간의 실제 환율은 고려되지 않습니다.
  이로 인해 특히 토큰이 페그를 잃거나 크게 변동하는 경우 사용자로부터 잘못된 수수료 및 프리미엄 금액이 전송됩니다.

참고: `payoff`에서도 동일한 문제가 발생합니다. `pnl` 금액은 USD로 계산되지만 최종적으로 기본 토큰 금액이 사용자에게 전송됩니다.

## 권장 사항

계산에 기본 토큰(매수 토큰) 가격과 가치를 사용하는 것을 고려하십시오.

# [M-13] `AdminStrategy`에서 `connect()`를 외부에서 호출할 수 있어 DoS 유발 (`connect()` can be called externally in `AdminStrategy` causing DoS)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`AdminStrategy` 계약의 `connect()` 함수는 외부에서 호출 가능하며 액세스 제어가 부족하여 `AdminOperationalTreasury`를 통한 공식 초기화 전에 모든 주소가 이를 호출할 수 있습니다. 호출되면 `setUp.treasury` 필드를 설정하고 `TreasuryAlreadySet()` 확인으로 인해 후속 합법적 호출(`init()`에서의 호출 포함)이 되돌려집니다.

```solidity
    function connect() external override {
        StrategyStorage.SetUp storage setUp = StrategyStorage.layout().setUp;
        IOperationalTreasury treasury_ = IOperationalTreasury(msg.sender);
        if (address(setUp.treasury) != address(0)) revert TreasuryAlreadySet();
        setUp.treasury = treasury_;
    }
```

이는 공격자가 `connect()`를 호출하고 악의적인 재무부 주소를 잠그고 `AdminOperationalTreasury` 초기화가 돌이킬 수 없게 실패하도록 할 수 있는 서비스 거부(DoS) 벡터를 생성합니다. 이는 프로토콜 설정 흐름을 중단시키고 전략 계약이 적절하게 초기화되기 전에 벽돌로 만들 수 있습니다.

참고:
전략의 `connect` 함수를 직접 호출하면 관리자가 새 전략을 성공적으로 추가할 수 없는 유사한 문제가 `addStrategy`에도 존재합니다.

## 권장 사항

`connect()` 액세스를 신뢰할 수 있는 계약으로만 제한하십시오(예: `onlyRole` 또는 `msg.sender` 확인을 통해).

# [M-14] 반올림으로 인한 보상 인덱스 인플레이션으로 보상이 갇힐 수 있음 (Reward Index Inflation Due to Rounding Up Can Trap Rewards)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`PositionInteractionFacet.sol` 내의 `_depositPremium` 함수에서 옵션 프리미엄이 유동성 제공자에 대한 보상으로 추가될 때, `rewards.rewardsPerLPQ128` 인덱스는 `mulDivRoundingUp`을 사용하여 업데이트됩니다:

```solidity
if (lpSupply > 0) rewards.rewardsPerLPQ128 += upscaledPremium.mulDivRoundingUp(Q128, lpSupply);
```

`mulDivRoundingUp`을 사용하면 LP 토큰 단위당 추가된 계산된 보상(Q128로 조정됨)이 고정 소수점 표현에서 가장 가까운 정수로 **위로** 반올림됩니다. 개별 예금의 경우 작아 보이지만 이러한 상향 반올림 오류는 시간이 지남에 따라 각 프리미엄 예금과 함께 `rewardsPerLPQ128` 인덱스에 누적됩니다.

이로 인해 `rewardsPerLPQ128` 인덱스는 풀에 물리적으로 입금된 실제 `totalRewards` 금액보다 약간 더 높은 총 보상 분배 약속을 나타낼 수 있습니다.

사용자가 `LiquidityFacet.sol`의 `_claimRewards`를 통해 보상을 청구할 때, 보류 중인 보상(`pending`)은 마지막 청구 체크포인트와 관련된 (잠재적으로 부풀려진) 인덱스의 차이를 기반으로 계산됩니다. 그런 다음 함수는 실제 사용 가능한 `totalRewards`에서 이 `pending` 보상을 뺍니다:

```solidity
strg.ledger.state.rewardsByToken[token].totalRewards -= pending;
```

`rewardsPerLPQ128` 인덱스에 누적된 상향 반올림 오류로 인해 사용자의 계산된 `pending` 금액(특히 많은 프리미엄 입금이 발생한 후에 청구하는 사용자의 경우)이 실제 남은 `totalRewards`를 초과하게 되면, 이 뺄셈은 언더플로우되어 **트랜잭션을 되돌립니다**.

결과적으로 보상을 청구하려는 마지막 사용자는 그렇게 할 수 없을 수 있으며, 합법적으로 얻은 보상은 계약에 **영구적으로 갇히게** 됩니다.

## 권장 사항

보상 인덱스 인플레이션을 방지하고 회계 지급 능력을 보장하려면 토큰당 보상 인덱스 증분을 계산할 때 표준 내림을 사용하십시오.

# [M-15] `withdrawFee`에서 잘못된 `ERC20` 전송으로 수수료 인출 방지 (Incorrect `ERC20` transfer in `withdrawFee` prevents fee withdrawal)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`AdminOperationalTreasury.withdrawFee()` 함수는 프로토콜 수수료를 인출할 때 `safeTransfer()` 대신 `safeTransferFrom()`을 사용합니다. 이는 다음과 같은 이유로 잘못되었습니다:

1. `safeTransferFrom()`은 지출자가 소유자의 허용을 받아야 합니다.
2. 계약은 자신(`address(this)`)에서 수신자에게 전송하려고 합니다.
3. 계약은 자신의 토큰을 지출하도록 스스로 승인하지 않았습니다.

```solidity
function withdrawFee(address token_, address recipient_) external {
    OperationalTreasuryStorage.Layout storage strg = OperationalTreasuryStorage.layout();

    if (msg.sender != strg.setUp.base.feesReceiver) revert("FeesReceiverOnly");
    uint256 amount = strg.ledger.collectedFees[token_];
    strg.ledger.collectedFees[token_] = 0;

@>  IERC20Metadata(token_).safeTransferFrom(address(this), recipient_, amount);
    emit ProtocolFeeWithdrawn(token_, recipient_, amount);
}
```

일반 `ERC20`의 경우 `transferFrom`은 `from`이 `msg.sender`와 같은 경우에도 항상 지출자의 허용량을 확인합니다. 결과적으로 수수료 전송은 허용량 부족으로 인해 실패합니다.

## 권장 사항

계약이 소유한 토큰을 전송하므로 `safeTransferFrom()`을 `safeTransfer()`로 교체하십시오.

# [L-01] 연산 순서로 인한 수수료 계산의 정밀도 손실 (Precision loss in fee calculation due to order of operations)

`OperationalTreasury.sol`의 `_calculateFeesUSD` 함수에서 수수료 계산은 **곱셈 전 나눗셈**으로 인해 정밀도 손실을 초래할 수 있는 방식으로 수행됩니다. 현재 구현:

```solidity
fees = price.mulDivRoundingUp(baseSetUp.protocolFees, FEES_UNIT);
fees = (fees * amount).toDecimals(36, 18);
```

이 문제는 프로토콜 수수료가 `amount`를 곱하기 전에 `FEES_UNIT`로 나누어지기 때문에 발생합니다. 이 연산 순서는 중간 값의 내림을 초래하여 **잠재적으로 수수료 과소 청구로 이어질 수 있습니다.**

정밀도 손실을 최소화하려면 계산 순서를 변경해야 합니다. 권장 구현은 다음과 같아야 합니다:

```solidity
fees = (price * amount).mulDivRoundingUp(baseSetUp.protocolFees, FEES_UNIT);
fees = fees.toDecimals(36, 18);
```

# [L-02] 구현 계약에서 `_disableInitializers()` 누락 (Missing `_disableInitializers()` in implementation contract)

`Oracle` 계약은 `UUPSUpgradeable`을 상속하고 `initializer` 패턴을 사용하지만 생성자에서 초기화 프로그램을 비활성화하지 않습니다.

```solidity
# The current implementation only has an init function:
function init(address owner) public initializer {
    _grantRole(AccessControlStorage.DEFAULT_ADMIN_ROLE, owner);
}
```

생성자에서 `_disableInitializers()`가 없으면 누군가 구현 계약을 직접 초기화할 위험이 있으며, 이는 예상치 못한 동작이나 잠재적인 보안 문제로 이어질 수 있습니다.

이 문제를 완화하려면 구현 계약의 생성자에 `_disableInitializers()`를 추가하는 것이 좋습니다.

# [L-03] `supportsInterface()`에서 `IERC165` 지원 누락 (Missing `IERC165` support in `supportsInterface()`)

`ERC721WithURIBuilderUpgradeable` 계약의 `supportsInterface` 함수는 IERC165 인터페이스를 재정의하지만 필요한 인터페이스 ID(`IERC165`)에 대한 지원을 제공하지 못합니다. 이는 좋은 관행이 아니며 잘못된 결과를 반환할 수 있습니다.

```solidity
    function supportsInterface(bytes4 interfaceId)
        public
        view
        virtual
        override(IERC165, ERC721EnumerableUpgradeable)
        returns (bool)
    {
        return ERC721EnumerableUpgradeable.supportsInterface(interfaceId);
    }
```

이 문제를 완화하려면 다음과 같이 변경하십시오:

````solidity
    function supportsInterface(bytes4 interfaceId)
        public
        view
        virtual
        override(IERC165, ERC721EnumerableUpgradeable)
        returns (bool)
    {
        return ERC721EnumerableUpgradeable.supportsInterface(interfaceId) || super.supportsInterface(interfaceId);
    }
# [L-04] 작업 중간에 기본 토큰 변경 시 문제 발생 가능 (Changes in underlying token mid-operation may cause issues)

`AdminStrategy.sol` 계약의 `setUnderlyingToken` 함수를 사용하면 적절한 역할을 가진 사용자가 언제든지 기본 토큰을 변경할 수 있습니다.

```solidity
    function setUnderlyingToken(address underlyingToken_) external onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE) {
        StrategyStorage.layout().setUp.base.underlyingToken = underlyingToken_;
    }
````

이는 특히 `underlyingToken`에 의존하는 작업이 진행 중일 때 토큰이 수정되는 경우 특정 위험을 초래합니다. 예를 들어 전략이 현재 `underlyingToken`을 기반으로 거래를 실행하거나 포지션을 관리하는 경우, 중간에 토큰을 변경하면 일관성 없는 상태와 예상치 못한 동작이 발생할 수 있습니다.

이 문제를 완화하려면 전략이 연결된 후에는 `setUnderlyingToken`을 허용하지 마십시오.

# [L-05] `setTargetWeight`에서 중복 토큰 유효성 검사 누락 (Missing duplicate token validation in `setTargetWeight`)

`setTargetWeight` 함수는 입력 배열의 중복 토큰에 대한 유효성 검사가 부족하여 일관성 없는 가중치 할당으로 이어질 수 있습니다. 문제가 되는 코드는 다음과 같습니다:

```solidity
function setTargetWeight(TokenWeight[] calldata tokens)
    external
    onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE)
{
    PoolStorage.AssetsConfig storage assets = PoolStorage.layout().setUp.assets;
    uint256 nTokens = tokens.length;
    if (nTokens != assets.allAssets.length()) revert PoolErrors.RequireAllTokens();

    uint256 total;
    TokenWeight memory item;
    uint256 weight;
    for (uint8 i; i < nTokens;) {
        item = tokens[i];
        assert(assets.allAssets.contains(item.token));
        weight = assets.isListed[item.token] ? item.weight : 0;
        assets.targetWeights[item.token] = weight;
        total += weight;
        // ... increment i
    }
    assets.totalWeight = total;
}
```

이 문제는 다음과 같은 이유로 발생합니다:

1. 함수는 토큰 수가 assets.allAssets.length()와 일치하는지 확인합니다.
2. 각 토큰이 입력 배열에 정확히 한 번 나타나는지 확인하는 유효성 검사가 없습니다.
3. 토큰이 입력 배열에 여러 번 나타나는 경우:
   - 각 발생은 이전 가중치 할당을 덮어씁니다.
   - 마지막 발생의 가중치만 저장됩니다.
   - `totalWeight` 계산에는 모든 발생이 포함됩니다.

이 문제를 완화하려면 각 토큰이 정확히 한 번 나타나는지 확인하는 유효성 검사를 추가하는 것이 좋습니다(주소 순서 확인 등을 통해).

# [L-06] `PoolAdminFacet` 초기화 시 역할 할당 불일치 (Role assignment mismatch in `PoolAdminFacet` initialization)

`PoolAdminFacet`의 초기화 프로세스에서 역할 할당과 액세스 제어 간의 불일치로 인해 초기화가 실패할 수 있는 중요한 문제가 존재합니다.

문제는 다음 순서에서 발생합니다:

```solidity
function init(address owner, AddToken[] calldata assets, PoolStorage.BaseSetUp memory baseSetUp)
    external
    initializer
{
    _grantRole(AccessControlStorage.DEFAULT_ADMIN_ROLE, owner);
    baseSetUp = _applyDefaultValues(baseSetUp);
    PoolStorage.layout().setUp.base = baseSetUp;
    addTokens(assets);  // This call will fail if owner != msg.sender
}

function addTokens(AddToken[] calldata newAssets) public onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE) {
    // ... implementation
}
```

이 문제는 다음과 같은 이유로 발생합니다:

- init 함수는 `DEFAULT_ADMIN_ROLE`을 지정된 `owner` 주소에 할당합니다.
- 바로 직후, `onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE)` 수정자가 있는 `addTokens`를 호출합니다.
- `owner != msg.sender`일 때, `addTokens`가 호출될 때 `msg.sender`에 필요한 역할이 없으므로 초기화 프로세스가 실패합니다.

이 문제를 완화하려면 역할 할당을 수정하여 `DEFAULT_ADMIN_ROLE`을 `msg.sender`에 직접 할당하십시오.

# [L-07] 정리되지 않은 DEX 스왑 토큰 승인으로 인한 권한 잔존 (Lingering permissions from uncleaned DEX swap token approvals)

`PositionInteractionFacet._swapAssetToAsset()`에서 함수는 스왑을 위해 `amountInMaximum`을 승인하지만 필요한 `amountIn`은 `amountInMaximum`보다 작거나 같을 수 있습니다. 이로 인해 외부 Dex 라우터에 승인이 남게 됩니다.

```solidity
function _swapAssetToAsset(
    ...
) internal returns (uint256 amountIn) {
    --- SNIPPED ---
@>    (uint256 amountInMaximum,,,) = IQuoterV2(dex.dexQuoter).quoteExactOutputSingle(quoteParams);
@>    amountInMaximum = _applySlippage(amountInMaximum);

@>    IERC20Metadata(assetIn).safeIncreaseAllowance(dex.dexRouter, amountInMaximum);

    IV3SwapRouter.ExactOutputSingleParams memory params = IV3SwapRouter.ExactOutputSingleParams({
        tokenIn: assetIn,
        tokenOut: assetOut,
        fee: dex.dexFee,
        recipient: address(this),
        deadline: block.timestamp + 30 minutes,
        amountOut: amountOut,
        amountInMaximum: amountInMaximum,
        sqrtPriceLimitX96: sqrtPriceLimitX96
    });

@>  amountIn = IV3SwapRouter(dex.dexRouter).exactOutputSingle(params);

    --- SNIPPED ---
}
```

이것은 다음과 같은 이유로 중대한 보안 위험을 초래하지 않지만:

1. 신뢰할 수 있는 라우터 계약만 승인을 가집니다.
2. 향후 작업은 `safeIncreaseAllowance`를 통해 승인을 재설정합니다.

공격 표면을 최소화하기 위해 사용하지 않는 승인을 정리하는 것이 모범 사례입니다.

스왑 후 승인을 취소하는 것을 고려하십시오.

# [L-08] 동적 `maxDepositPerUser`로 인해 펜윅 트리 상태가 오래될 수 있음 (Dynamic `maxDepositPerUser` can stale Fenwick tree state)

프로토콜은 펜윅 트리를 사용하여 사용자 시간 잠금 LP 예금을 효율적으로 추적하며, 각 업데이트 및 접두사 합계 연산은 구성 가능한 `maxDepositPerUser`에 의해 제한됩니다.

그러나 구현은 트리가 **현재 최대값까지 일관되게 채워져** 있다고 가정하는데, 이는 두 가지 중요한 시나리오에서 실패합니다:

1. **이전에 낮았던 최대값이 증가된 경우**, 새로운 노드는 **쿼리(접두사 합계)에 사용**되지만 **과거 예금에 대해서는 업데이트되지 않았습니다**.
2. **최대값이 감소된 경우** 나중에 최대값이 다시 증가하면, 이러한 **오래된 값이 접두사 합계를 손상**시킵니다.

이로 인해 **불완전하거나 오래된 합계**가 발생하여 잠금 해제 로직 및 예금 추적의 정확성이 깨집니다.

펜윅 트리는 각 노드가 특정 범위를 포함하는 이진 인덱싱 시스템을 사용합니다:

```
Node 1 (0001) = Sum of elements [1]
Node 2 (0010) = Sum of elements [1-2]
Node 3 (0011) = Sum of elements [3]
Node 4 (0100) = Sum of elements [1-4]
Node 5 (0101) = Sum of elements [5]
Node 6 (0110) = Sum of elements [5-6]
Node 7 (0111) = Sum of elements [7]
Node 8 (1000) = Sum of elements [1-8]
```

`maxDepositPerUser`가 변경될 때 다음 시나리오를 고려하십시오:

1. 5에서 8로 증가:

   - 노드 8은 합계[1-8]을 포함해야 합니다.
   - 그러나 요소 [1-5]는 이전에 전파되지 않았습니다.
   - 결과: 노드 8은 불완전한 합계를 가집니다.

2. 5에서 3으로 감소한 다음 다시 5로 증가:
   - 노드 [4-5]는 오래된 값을 유지합니다.
   - 새 예금은 상위 노드를 제대로 업데이트하지 않습니다.
   - 결과: 손상된 접두사 합계.

그러나 이 문제의 실제 영향은 프로토콜의 기본 구성에 의해 완화됩니다. `maxDepositPerUser`는 잠재적으로 높은 값으로 설정됩니다. 높은 값의 최대값으로 사용자는 이러한 노드 대부분을 채우는 예금을 해야 합니다. 이는 정상적인 운영에서는 가능성이 낮지만, 프로토콜이 예금 한도를 자주 조정해야 하는 경우 여전히 가능합니다.

`maxDepositPerUser`를 업데이트하면 펜윅 트리 상태가 잠재적으로 오래될 수 있으며 궁극적으로 사용자의 잠금 해제 금액이 올바르지 않게 될 수 있음을 확인하십시오. `maxDepositPerUser`를 변경할 때 접두사 합계가 정확하게 유지되도록 하려면 프로토콜은 펜윅 트 크기를 조정할 때 안전한 처리 메커니즘을 구현해야 합니다.

# [L-09] `setPositionsManager`에서 통제되지 않은 id로 인한 손상 위험 (Risk corruption from uncontrolled id in `setPositionsManager`)

`AdminOperationalTreasury.setPositionsManager()` 함수를 사용하면 프로토콜 관리자가 활성 `PositionsManager` 계약을 교체할 수 있습니다. 그러나 새 관리자의 포지션 ID 카운터(`nextTokenId()`)에 대한 유효성 검사를 시행하지 않습니다.

포지션 ID는 포지션 저장소, 전략 추적 및 지급 로직을 포함한 여러 구성 요소에서 공유 키로 사용됩니다. 새 `PositionsManager`가 등록되고 **기존 ID와 충돌하는** ID를 발행하기 시작하면 기존 포지션 상태를 덮어쓰거나 손상시켜 데이터 불일치 또는 자금 손실로 이어질 수 있습니다.

ID 시퀀스 연속성을 보장하기 위해 새 포지션 관리자를 할당할 때 확인을 추가하는 것을 고려하십시오.

```diff
function setPositionsManager(address manager_) external onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE) {
-    OperationalTreasuryStorage.layout().setUp.base.manager = IPositionsManager(manager_);
+    uint256 currentNextId = OperationalTreasuryStorage.layout().setUp.base.manager.nextTokenId();
+    if (currentNextId < IPositionsManager(manager_).nextTokenId()) revert InvalidPositionManager();
     OperationalTreasuryStorage.layout().setUp.base.manager = IPositionsManager(manager_);
}
```

# [L-10] `Strategy.create()`에서 초기화되지 않은 `maturityDate` (Uninitialized `maturityDate` in `Strategy.create()`)

`Strategy.create()` 함수는 `maturityDate` 반환 값을 선언하지만 초기화하거나 설정하지 않습니다. 기본값이 `0`인 이 초기화되지 않은 값은 반환되어 `OperationalTreasury._buy()`로 전달되며, 여기서 `LiquidityLocked` 이벤트를 통해 방출됩니다.

이로 인해 잘못되거나 오도하는 이벤트 로그, 특히 `maturityDate = 0`이 표시됩니다.

초기화되지 않은 값은 이벤트 방출로 이어집니다:

```solidity
function _buy(...) internal {
    // ...
    (sizeUSD, premium, maturityDate) = strategy.create(...);
    // maturityDate passed unchanged -> maturityDate: 0
    _emitEvent(optionID, amount, sizeUSD, buyToken, premium, fees, maturityDate);
}
```

`Strategy.create()`가 `maturityDate`를 명시적으로 설정하고 반환하는지 확인하십시오.

# [L-11] `setProtocolFees()`의 제한 없는 프로토콜 수수료 문제 (Unbounded protocol fee issue in `setProtocolFees()`)

`AdminOperationalTreasury`의 `setProtocolFees` 함수를 사용하면 관리자가 최대 한도나 제약 없이 프로토콜 수수료를 설정할 수 있습니다:

```solidity
    function setProtocolFees(uint24 protocolFees_) external onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE) {
        OperationalTreasuryStorage.BaseSetUp storage baseSetUp = OperationalTreasuryStorage.layout().setUp.base;

        emit ProtocolFeesSet(baseSetUp.protocolFees, protocolFees_);
        baseSetUp.protocolFees = protocolFees_;
    }
```

이것은 두 가지 취약점을 도입합니다:

1. 관리자는 수수료를 100%(또는 그 이상)로 설정하여 프로토콜이 옵션을 구매하는 사용자로부터 수수료+프리미엄을 전송하기 위해 `_takeFeesLockLiquidity`를 호출할 때 전체 사용자 허용량을 전송할 수 있습니다.
2. 마찬가지로 `payOff` 중에 관리자는 모든 사용자 PnL을 프로토콜 수익으로 흡수하도록 수수료를 구성할 수 있습니다.

# [L-12] 오라클: 되돌릴 수 없는 토큰 구성 (Oracle: irreversible token configuration)

`configToken` 함수를 사용하면 관리자가 토큰을 화이트리스트에 추가하고 가격 피드를 설정할 수 있습니다.

```solidity
    function configToken(address token, address priceFeed) external onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE) {
        if (address(tokenOracle[token]) != address(0)) revert TokenAlreadyConfigured();
        else whitelistedTokens.push(token);

        tokenOracle[token] = AggregatorV3Interface(priceFeed);
        emit TokenAdded(token);
    }
```

그러나 토큰이 구성되면 업데이트하거나 제거할 수 없습니다. 토큰이나 관련 가격 피드에 결함이 있거나 더 이상 사용되지 않거나 잘못된 경우 문제가 됩니다.

잘못된 구성에서 복구하거나 오라클 관련 문제에 대응할 방법이 없으므로 운영 위험이 발생하고 시스템의 유연성이 제한됩니다.

# [L-13] PoolAdminFacet: `removeToken()`에 프로토콜 수수료 확인 누락 (PoolAdminFacet: `removeToken()` lacks protocol fee check)

`removeToken` 함수는 제거하기 전에 토큰에 0이 아닌 프로토콜 수수료가 있는지 확인하지 않습니다. 이로 인해 feeDistributor가 토큰이 제거된 후 수수료를 청구하지 못할 수 있습니다.

````solidity
function removeToken(address _token) external onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE) {
    PoolStorage.Layout storage strg = PoolStorage.layout();
    if (strg.ledger.state.poolAmount[_token] != 0) revert PoolErrors.PoolNotEmpty(_token);
    strg.setUp.assets.allAssets.remove(_token);
    strg.setUp.assets.stablecoins.remove(_token);

    if (strg.ledger.state.rewardsByToken[_token].totalRewards == 0) strg.setUp.assets.rewardTokens.remove(_token);
}
```

# [L-14] AdminOperationalTreasury에서 제거 후 전략을 추가할 수 없음 (Strategy cannot be added after removal in AdminOperationalTreasury)

`addStrategy` 함수가 전략의 `connect`를 호출하지만 `connect`는 한 번만 호출될 수 있으므로 되돌려지기 때문에 관리자는 이미 제거된 전략을 추가할 수 없습니다:

```solidity
    function addStrategy(IStrategy s) external override onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE) {
        OperationalTreasuryStorage.SetUp storage setUp = OperationalTreasuryStorage.layout().setUp;

        if (setUp.acceptedStrategy[s]) revert StrategyAlreadyAdded();
        _connect(s, setUp);
    }

    function _connect(IStrategy s, OperationalTreasuryStorage.SetUp storage strg) internal {
        s.connect();
        strg.acceptedStrategy[s] = true;
    }
````

`AdminStrategy` 계약의 `connect` 함수는 한 번만 호출할 수 있습니다:

```solidity

    function connect() external override {
        StrategyStorage.SetUp storage setUp = StrategyStorage.layout().setUp;
        IOperationalTreasury treasury_ = IOperationalTreasury(msg.sender);
        if (address(setUp.treasury) != address(0)) revert TreasuryAlreadySet();
        setUp.treasury = treasury_;
    }
```

# [L-15] 계약이 일시 중지된 경우 옵션 행사를 위해 `payOff()` 차단됨 (`payOff()` blocked for option exercise when contract is paused)

만기 후 포지션을 행사하기 위해 옵션 보유자가 사용하는 `OperationalTreasury.payOff` 함수는 `PositionInteractionFacet.sol` 계약의 `exerciseOrLiquidatePosition` 함수를 내부적으로 호출합니다. `exerciseOrLiquidatePosition` 함수에는 `whenNotPaused` 수정자가 포함되어 있습니다.

따라서 풀 계약이 일시 중지된 경우(예: 비상 유지 보수, 업그레이드 또는 외부 종속성 문제로 인해 관리자에 의해) 사용자는 `payOff`를 호출하여 옵션을 행사할 수 없습니다. 일시 중지 중에 옵션이 가격 내(ITM)에 있지만 계약이 일시 중지 해제될 때 가격 외(OTM)가 되면 사용자는 **이익 실현 기회를 잃게 됩니다**. 이는 관리 조치(일시 중지)에 따라 옵션 보유자의 재무 결과에 직접적인 영향을 미칩니다.

프로토콜의 사용자 대상 문서에 이 동작과 옵션 보유자에 대한 관련 위험을 명시적으로 문서화하십시오. 사용자는 계약이 일시 중지된 경우 옵션 행사 기능이 일시적으로 중단될 수 있음을 알고 있어야 합니다.

# [L-16] `payOff()`에서 비효율적인 지급 전송 확인 (Inefficient payout transfer check in `payOff()`)

`OperationalTreasury.sol`의 `payOff` 함수는 총 이익(`pnl`)에서 수수료(`feesUSD`)를 빼서 순 이익(`netAmount`)을 계산합니다. 그러나 이 순 이익을 사용자에게 최종 전송하는 것은 순 금액(`netAmount`) 자체가 아니라 총 이익(`pnl`)이 양수인지에 따라 결정됩니다:

```solidity
uint256 netAmount = pnl - feesUSD;
// ...
if (pnl > 0) baseSetUp.token.safeTransfer(account, netAmount);
```

이 로직은 비효율적입니다. 총 이익(`pnl`)이 양수이더라도 계산된 수수료가 이익과 같으면 순 금액(`netAmount = pnl - feesUSD`)이 0이 될 수 있기 때문입니다. `pnl > 0`이지만 `netAmount == 0`인 경우 `if (pnl > 0)` 조건은 여전히 참으로 평가되어 0을 전송하려고 시도하는 불필요한 `safeTransfer` 호출로 이어집니다. 0값 전송을 시도하면 상태를 변경하지 않고 가스만 낭비합니다.

전송을 시도하기 전에 `netAmount`가 0보다 확실히 큰지 확인하도록 조건을 수정하십시오.

# [L-17] `_init()` 함수에서 이니셜라이저 가드 누락 (Missing initializer guard in `_init()` function)

`ERC721WithURIBuilderUpgradeable.sol` 내의 `internal` 함수 `_init`에는 `onlyInitializing` 수정자가 없습니다. 이 함수는 업그레이드 가능한 구현 계약 `PositionsManager.sol`의 `initializer` 함수(`init`)에 의해 호출됩니다.

메인 `PositionsManager.init` 함수는 `initializer` 수정자에 의해 보호되고 `_init`에 의해 호출되는 기본 `__ERC721_init`도 보호되지만, `_init` 자체에 가드가 없으면 잠재적인 위험이 발생합니다. `PositionsManager`에 대한 향후 업계가 초기 배포 후 실수로 `_init`를 다시 호출하는 새 함수를 도입하는 경우 `_init` 수준에서 이 호출 시도를 방지할 직접적인 보호가 없습니다.

권장 사항:

`ERC721WithURIBuilderUpgradeable._init` 함수에 `onlyInitializing` 수정자를 추가하여 초기화 전용 컨텍스트를 명시적으로 시행하고 표준 업그레이드 가능 계약 패턴을 준수하십시오.

```solidity
    function _init(string memory name_, string memory symbol_) internal onlyInitializing {
        __ERC721_init(name_, symbol_);
    }
```

# [L-18] 계약 지갑이 `EIP-1271` 서명 검증과 호환되지 않음 (Contract wallets incompatible with `EIP-1271` signature verification)

`IvSignerRecovery.sol`의 서명 검증 메커니즘은 서명을 검증하기 위해 ECDSA 복구에만 의존합니다:

```solidity
function _recoverSigner(
    SplitSig memory sig,
    uint256 iv,
    IStrategy strategy,
    uint256 maturityDate,
    uint256 relativeStrike
) internal view returns (address) {
    // Uses ECDSA recovery directly
    return _digest(iv, strategy, maturityDate, relativeStrike).recover(sig.v, sig.r, sig.s);
}
```

이 구현은 외부 소유 계정(EOA)의 서명만 지원하며 **EIP-1271**(`계약을 위한 표준 서명 검증 방법`)과의 호환성이 부족합니다. 결과적으로 스마트 계약 지갑(예: Gnosis Safe)을 사용하는 사용자는 계약 기반 서명이 `ECDSA.recover`로 검증될 수 없기 때문에 `OperationalTreasury.buy()` 함수에서 프로토콜의 옵션 구매 기능을 사용할 수 없습니다.

향상된 보안 기능과 다중 서명 기능을 위한 스마트 계약 지갑의 채택이 증가함에 따라 이 제한은 불필요하게 프로토콜의 사용자 기반을 제한합니다.

EIP-1271을 지원하도록 서명 검증 로직을 확장하십시오. 이는 일반적으로 서명자의 주소가 계약에 해당하는지 확인하는 것을 포함합니다. 계약인 경우 `isValidSignature` 함수를 호출하여 계약 자체의 로직에 따라 서명의 유효성을 확인하십시오.

# [L-19] `_computeMaturityDate` `uint32` 타임스탬프가 프로토콜 수명을 제한함 (`_computeMaturityDate` `uint32` timestamp limits protocol lifespan)

`PositionParams.sol`의 `_computeMaturityDate` 함수는 만기 타임스탬프를 반환하기 전에 `uint32`로 캐스트합니다:

```solidity
    function _computeMaturityDate(uint256 period) internal view returns (uint32) {
        uint256 maturityTimestamp = block.timestamp + period;
        maturityTimestamp -= (maturityTimestamp % 1 days);
        maturityTimestamp += 1 days - 1;
        return uint32(maturityTimestamp); // <-- Cast to uint32
    }
```

이것은 표현 가능한 최대 타임스탬프에 엄격한 제한을 부과합니다. `uint32`는 최대 **4,294,967,295**까지의 Unix 타임스탬프만 나타낼 수 있으며, 이는 **2106년 2월 7일**에 해당합니다. 이 날짜 이후에는 `block.timestamp`를 사용하여 계산된 타임스탬프를 `uint32`로 캐스트할 때 오버플로되어 잘못된 만기 날짜가 발생합니다.

이 함수는 옵션 생성(`Strategy::_create`) 및 서명 검증(`OperationalTreasury::_checksOnBuy`)을 포함한 핵심 프로토콜 구성 요소에서 사용됩니다.

이 제한은 먼 미래(현재 2025년 4월에서 약 81년 후)이지만 해결되지 않는 한 현재 형태의 프로토콜에 대해 **하드 수명 종료**를 나타냅니다. 잠재적으로 장기 운영을 위해 설계된 금융 프로토콜의 경우 이는 불필요하고 피할 수 있는 시간 제약을 만듭니다.

프로토콜 전체에서 만기 타임스탬프에 대한 `uint32` 사용을 더 큰 부호 없는 정수 유형으로 교체하십시오.

# [L-20] `__UUPSUpgradeable_init()`에서 부모 초기화 호출 누락 (Missing parent initializer call in `__UUPSUpgradeable_init()`)

`Oracle.sol` 계약은 `UUPSUpgradeable`을 상속하지만 `init` 함수는 해당 부모 초기화 함수인 `__UUPSUpgradeable_init()`를 호출하지 못합니다. 사용된 OpenZeppelin 라이브러리의 현재 버전에서 이 부모 초기화 함수가 비어 있을 수 있지만 호출을 생략하면 OpenZeppelin 업그레이드 가능 계약에 필요한 명시적 초기화 패턴을 위반하게 됩니다. 이러한 편차는 라이브러리가 해당 초기화 프로그램의 로직으로 업데이트될 경우 향후 호환성에 대한 위험을 초래하며 코드를 비표준화하고 유지 관리를 어렵게 만듭니다.

`Oracle.init()` 함수 내에서 `__UUPSUpgradeable_init()`를 명시적으로 호출하여 표준 OpenZeppelin 업그레이드 가능 패턴을 준수하십시오.

```solidity
    function init(address owner) public initializer {
        __UUPSUpgradeable_init(); // Initialize UUPSUpgradeable parent
        _grantRole(AccessControlStorage.DEFAULT_ADMIN_ROLE, owner);
    }
```

# [L-21] 옵션의 행사가 계산에 슬리피지 보호 누락 (Missing slippage protection in strike price calculation for options)

프로토콜의 옵션 생성 프로세스에는 사용자가 트랜잭션을 제출하는 시점과 실행되는 시점 사이의 가격 슬리피지에 대한 보호 기능이 없습니다. 문제가 되는 흐름은 다음과 같습니다:

```solidity
// In OperationalTreasury.sol
function buy(
    IStrategy strategy,
    address holder,
    uint256 amount,
    uint256 period,
    uint256 relativeStrike,  // User inputs relative strike
    uint256 iv,
    IvSignerRecovery.SplitSig memory sig
) external virtual override nonReentrant whenNotPaused {
    _checksOnBuy(strategy, period, relativeStrike, iv, sig);
    _buy(holder, strategy, amount, period, relativeStrike, iv);
}

// In Call.sol
function _calculateStrike(uint256 preciseCurrentPrice, uint256 relativeStrike)
    internal
    view
    override
    returns (uint256)
{
    uint256 calculatedStrike = preciseCurrentPrice + ((preciseCurrentPrice * relativeStrike) / 100);
    return _roundPrice(calculatedStrike);
}

// In Put.sol
function _calculateStrike(uint256 preciseCurrentPrice, uint256 relativeStrike)
    internal
    view
    override
    returns (uint256)
{
    uint256 calculatedStrike = preciseCurrentPrice - ((preciseCurrentPrice * relativeStrike) / 100);
    return _roundPrice(calculatedStrike);
}
```

이 문제는 다음과 같은 이유로 발생합니다:

1. 사용자는 원하는 행사가에 대한 `relativeStrike` 백분율을 지정합니다.
2. 실제 행사가는 실행 시 `currentPrice`를 사용하여 계산됩니다.
3. 트랜잭션 제출과 실행 사이:
   - 기본 자산 가격이 크게 변동될 수 있습니다.
   - 이로 인해 사용자가 의도한 것과는 다른 행사가가 발생하며 사용자는 최종 행사가에 대한 통제권이 없습니다.

예를 들어:

1. 사용자는 ETH가 $2000일 때 10% 콜을 원합니다.
2. 예상 행사가: $2200.
3. 실행 전에 가격이 $2100로 이동하는 경우.
4. 실제 행사가: $2310 ($2100보다 10% 높음).

이로 인해 옵션이 훨씬 더 비싸지거나 수익성이 떨어질 수 있으며 이는 나쁜 사용자 경험으로 이어질 수 있습니다.

권장 사항:

행사가 계산에 슬리피지 보호를 구현하십시오.

# [L-22] `_refreshVirtualPoolValue()` 누락으로 인한 오래된 수수료 및 PLP 가격 (Omission of `_refreshVirtualPoolValue()` causes stale fees and PLP price)

`PositionInteractionFacet`의 `_payout()` 함수는 풀 잔액을 업데이트하지만 가상 풀 상태를 업데이트하고 업데이트된 PLP 가격을 방출하지 못합니다. 이로 인해 동적 수수료 계산에 오래된 가상 풀 값이 사용되고 오래된 PLP 가격 보고가 발생합니다.

이 문제는 다음과 같은 이유로 발생합니다:

1. `_payout()`에서의 풀 잔액 변경: `poolAmount[pos.uAsset]` 및/또는 `poolAmount[pos.buyToken]`는 총 풀 가치에 영향을 미칩니다.

2. 오래된 `virtualNetPoolValue` 값은 동적 수수료 적용에 영향을 미칩니다:

```solidity
function _calcFeeRate(...) internal view returns (int256 delta, uint256 feeRate) {
    if (PoolStorage.layout().setUp.assets.totalWeight == 0
            || PoolStorage.layout().ledger.state.virtualNetPoolValue == 0
    ) return (0, feeRate);
}
```

3. PLP 가격 방출 또한 놓칩니다.

권장 사항:

`_payout()`에서 `_refreshVirtualPoolValue()`를 적용하고 `_emitPLPPrice()`를 호출하십시오.

# [L-23] `operationalTreasury`의 토큰 불일치로 풀 지급에 영향 (Token mismatch in `operationalTreasury` affects pool payouts)

프로토콜에는 `OperationalTreasury`와 `Pool` 계약 간의 `pnl` 지급에 대해 일관되지 않은 토큰 전송이 있을 수 있습니다. `Pool`은 지급에 포지션의 `buyToken`을 사용하는 반면 `OperationalTreasury`는 현재 `baseSetUp.token`을 사용합니다. `OperationalTreasury`가 업데이트되고 다른 기본 토큰으로 기본 설정이 다시 초기화되면 사용자는 잘못된 토큰으로 지급을 받게 됩니다.

```solidity
function payOff(uint256 positionID, address account) external override nonReentrant whenNotPaused {
    --- SNIPPED ---
    if (pnl > 0) baseSetUp.token.safeTransfer(account, netAmount);
    emit Paid(positionID, account, netAmount, feesUSD);
}
```

```solidity
function _payout(Position memory pos, PositionClose memory close) internal returns (uint256 pnl) {
    --- SNIPPED ---
    strg.ledger.state.poolAmount[pos.buyToken] -= pnl;
    _doTransferOut(pos.buyToken, msg.sender /* operational treasury*/, pnl);
}
```

권장 사항:

`OperationalTreasury`의 각 포지션에 원래 `baseSetUp.token` 또는 `buyToken`을 기록하고 지급에 사용하십시오.

# [L-24] `poolAdminFacet`의 `updateDexConfig()`가 쌍별 DEX 수수료를 무시함 (`updateDexConfig()` in `poolAdminFacet` ignores pair-specific DEX fees)

`PoolAdminFacet`의 `updateDexConfig()` 함수는 DEX 라우터 주소당 단일 수수료 값을 설정합니다:

```solidity
function updateDexConfig(address router, uint24 fee, bool isValid)
    external
    onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE)
{
    PoolStorage.Dex storage dex = PoolStorage.layout().setUp.dex;
    dex.valid[router] = isValid;
@>  dex.fee[router] = fee;
    emit DexUpdated(router, isValid, fee);
}
```

그러나 이 구조는 다른 수수료(예: 500, 3000, 10000)를 가진 토큰 쌍당 여러 풀을 지원하는 Uniswap V3와 같은 DEX를 수용하지 못합니다. 주어진 라우터의 모든 스왑에 단일 수수료를 사용하면 `_swapAssetToAsset` 함수에서 되돌리기 또는 더 높은 슬리피지가 발생할 수 있습니다.

권장 사항:

쌍별 수수료 구성을 허용하도록 설계를 업데이트하십시오.

# [L-25] 유동성 계산에서 일관성 없는 소수점 처리 (Inconsistent decimal handling in liquidity calculation)

`_calcAddLiquidity` 함수는 유동성 계산 중에 소수점 자릿수를 일관성 없게 처리합니다:

1. **초기 계산 (올바름):**

   - `valueChange`를 계산할 때 `provDecimals`(가격 피드에서)를 사용합니다:

   ```solidity
   (tokenPrice, provDecimals) = _getLastPrice(_token);
   valueChange = (_amountIn * tokenPrice).toDecimals(tokenDecimals + provDecimals, 18);
   ```

2. **LP 금액 계산 (부정확함):**
   - `provDecimals`를 사용하는 대신 8 소수점을 하드코딩합니다:
   ```solidity
   lpAmount = lpAmount.toDecimals(tokenDecimals + 8, plpDecimals);  // Should use provDecimals
   ```

이러한 불일치는 LP 금액 계산의 두 분기(`lpSupply == 0`일 때와 그 외의 경우) 모두에서 발생합니다. 이 함수는 가격 피드 소수점이 항상 8이라고 가정하지만 모든 오라클 제공자에게 해당되지 않을 수 있습니다.

**권장 사항**

코드베이스 전체에서 `proveDecimals`를 일관되게 사용하여 다양한 토큰 가격 소수점 형식 처리를 표준화하십시오.

# [L-26] `block.timestamp` 의존성으로 인해 하루 말 옵션 구매 취약 (End-of-day option buys vulnerable due to `block.timestamp` dependency)

`OperationalTreasury.sol`의 `buy()` 함수를 사용하면 사용자는 신뢰할 수 있는 서명자가 오프체인에서 생성한 서명(`sig`)을 포함한 매개변수를 제공하여 옵션을 구매할 수 있습니다. 이 서명은 요청된 `period`에서 파생된 `maturityDate`를 포함한 여러 매개변수를 다룹니다.

검증 프로세스는 내부 `_checksOnBuy` 함수에서 발생합니다:

```solidity
    function _checksOnBuy(
        IStrategy strategy,
        uint256 period,
        uint256 relativeStrike,
        uint256 iv,
        IvSignerRecovery.SplitSig memory sig
    ) internal view {
        // ... other checks ...
        // maturityDate is recalculated here using block.timestamp
        address recoveredSigner = _recoverSigner(sig, iv, strategy, period._computeMaturityDate(), relativeStrike);
        if (recoveredSigner != strg.setUp.base.signer) revert InvalidIVSigner();
    }
```

문제는 서명 검증에 사용되는 `maturityDate`가 `period._computeMaturityDate()`를 통해 온체인에서 계산되는 방식에 있습니다. `PositionParams.sol`에 정의된 이 함수는 실행 시점의 **현재 `block.timestamp`**를 기반으로 만기 타임스탬프를 계산합니다.

```solidity
    function _computeMaturityDate(uint256 period) internal view returns (uint32) {
        uint256 maturityTimestamp = block.timestamp + period; // Uses current block.timestamp
        maturityTimestamp -= (maturityTimestamp % 1 days); // Rounds down to start of day
        maturityTimestamp += 1 days - 1; // Sets to 23:59:59 of that day
        return uint32(maturityTimestamp);
    }
```

`_computeMaturityDate`는 `block.timestamp`에 의존하고 결과를 반올림하기 때문에 다음 시나리오가 발생합니다:

1. 오프체인 서명자 서비스는 현재 타임스탬프(예: 1일차, 23:55:00)를 기반으로 `maturityDate`를 계산하고 이 날짜를 포함한 페이로드에 서명합니다(예: X일차, 23:59:59 결과).
2. 사용자는 1일차 끝 무렵에 이 서명으로 `buy()` 트랜잭션을 제출합니다.
3. 네트워크 혼잡이나 블록 타이밍으로 인해 트랜잭션은 자정 *이후*에 채굴된 블록에 포함됩니다(예: 2일차, 00:01:00).
4. 실행 중 `_checksOnBuy`는 `period._computeMaturityDate()`를 호출합니다. 이것은 2일차의 `block.timestamp`를 사용하여 `maturityDate`를 다시 계산합니다. 결과는 원래 서명된 `maturityDate`보다 정확히 24시간 늦습니다(예: X+1일차, 23:59:59).
5. `_recoverSigner` 함수는 이 **새롭고 더 늦은 `maturityDate`**를 포함하는 데이터 페이로드에 대해 서명을 검증하려고 시도합니다.
6. 서명된 데이터(이전 `maturityDate` 포함)가 온체인에서 다시 계산된 데이터(더 늦은 `maturityDate` 포함)와 일치하지 않기 때문에 서명 검증이 실패합니다. 트랜잭션은 `InvalidIVSigner`와 함께 되돌려집니다.

이는 특히 네트워크 혼잡 시 UTC 하루가 끝날 때 옵션을 구매하려는 사용자에게 **서비스 거부(DoS)** 조건을 효과적으로 생성합니다. 사용자는 서명과 실행 사이에 `block.timestamp`에 따라 검증에 사용되는 `maturityDate`가 변경되기 때문에 트랜잭션 실패와 가스비 손실을 경험하게 됩니다.

권장 사항:

이 문제를 해결하려면 서명 검증에 사용되는 `maturityDate`가 서명 시점과 실행 시점 간에 일관되어야 합니다. 서명된 데이터에 대한 검증 중에 `block.timestamp`에 대한 의존성을 제거해야 합니다.

1.  **명시적 만기일 서명:** 오프체인 서명자는 정확한 `maturityDate` 타임스탬프를 계산하고(예: 현재 로직 또는 고정 일일 계산 사용) 서명되는 데이터 페이로드에 이 **명시적 타임스탬프**를 포함해야 합니다.
2.  **`buy()`에 명시적 날짜 전달:** 서명된 이 명시적 `maturityDate` 타임스탬프를 수락하도록 `buy` 함수 서명을 수정하십시오.
3.  **검증에 명시적 날짜 사용:** `period._computeMaturityDate()`를 사용하여 다시 계산하는 대신 이 **명시적 `maturityDate`**(인수로 수신됨)를 `_recoverSigner`에 전달하도록 `_checksOnBuy`를 수정하십시오.

# [L-27] 최소 예금으로 토큰 제거 차단 가능 (Token removal can be blocked by minimal deposits)

현재 구현에서 프로토콜의 토큰 제거 기능은 악의적인 행위자가 최소 토큰 입금을 통해 영구적으로 차단할 수 있습니다. 이 취약점은 PoolAdminFacet.sol의 엄격한 유효성 검사로 인해 존재합니다:

```solidity
    function removeToken(address _token) external onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE) {
        PoolStorage.Layout storage strg = PoolStorage.layout();
        if (strg.ledger.state.poolAmount[_token] != 0) revert PoolErrors.PoolNotEmpty(_token);
        strg.setUp.assets.allAssets.remove(_token);
        strg.setUp.assets.stablecoins.remove(_token);

        if (strg.ledger.state.rewardsByToken[_token].totalRewards == 0) strg.setUp.assets.rewardTokens.remove(_token);
    }
```

모든 사용자는 토큰의 최소 금액(1 `wei`만큼 적음)을 풀에 입금할 수 있습니다. 토큰에 0이 아닌 잔액이 있으면 관리자는 프로토콜에서 제거할 수 없습니다. 이는 토큰 제거 기능에 대한 영구적인 서비스 거부(DOS) 벡터를 생성합니다.

**이 문제는 스테이블 코인 중 하나가 의존하는 경우 더욱 심각합니다. `strg.setUp.assets.stablecoins`에서 코인을 제거할 수 없으면 잘못된 `getPoolValue` 계산이 발생할 수 있습니다.**

권장 사항:

관리자가 잔액이 적더라도 토큰을 제거할 수 있는 보다 강력한 토큰 제거 시스템을 구현하십시오.

# [L-28] `LiquidityFaucet` 계산에서 잘못된 소수점 변환 (Incorrect decimal conversion in `LiquidityFaucet` calculation)

`_calcAddLiquidity` 함수에서 `lpAmount` 변환은 잘못된 소수점 계산을 사용합니다:

```solidity
    function _calcAddLiquidity(address _token, uint256 _amountIn)
        internal
        view
        returns (uint256 netAmount, uint256 pFee, uint256 lpAmount, uint256 tokenPrice)
    {
        --snip--
        if (lpSupply == 0) {
            lpAmount = netAmount * tokenPrice;
            lpAmount = lpAmount.toDecimals(tokenDecimals + 8, plpDecimals);
        } else {
@1>          lpAmount = netAmount.mulDiv(tokenPrice * lpSupply, _getPoolValue(false, false));
@2>          lpAmount = lpAmount.toDecimals(tokenDecimals + 8, plpDecimals);
        }
    }
```

`@1>` 단계에서 `lpAmount` 소수점을 계산하는 올바른 방법은 다음과 같아야 합니다:

```
lpAmount decimals = (tokenDecimals * priceDecimals * plpDecimals) / poolValueDecimals(18)
```

그러나 `@2>` 단계에서 코드는 소수점이 `tokenDecimals + 8`이라고 잘못 가정하고 `plpDecimals`로 변환하려고 시도합니다. 반면에 코드는 `plpDecimals`가 `poolValueDecimals (18)`과 같다고 가정합니다.

소수점 변환에 이 잘못된 공식을 사용하면 부정확한 LP 토큰 할당이 발생하여 사용자 지분과 풀 회계의 전반적인 무결성에 직접적인 영향을 미칩니다.

권장 사항:

올바른 소수점 계산을 사용하도록 `lpAmount` 소수점 변환 로직을 업데이트하십시오.

# [L-29] 잘못된 `EIP-712` 데이터 해싱 구현 (Incorrect `EIP-712` data hashing implementation)

`IvSignerRecovery.sol` 계약은 `OperationalTreasury.buy()` 함수에 필요한 옵션 매개변수(`iv`, `strategy`, `maturityDate`, `relativeStrike`)와 관련된 서명을 검증하기 위해 EIP-712를 사용하는 것을 목표로 합니다. 그러나 `_hash` 함수 내의 구조화된 데이터 해싱 구현은 EIP-712 표준에서 크게 벗어납니다.

EIP-712 표준은 구조 해시(`structHash`)가 다음과 같이 계산되어야 한다고 명시합니다: `hashStruct(s : 𝕊) = keccak256(typeHash ‖ encodeData(s))` 여기서:

- `typeHash`는 구조의 유형 정의 문자열의 `keccak256` 해시입니다.
- `encodeData(s)`는 구조체 멤버를 ABI 인코딩하며, 결정적으로 `string` 및 `bytes`와 같은 동적 유형을 내용의 `keccak256` 해시로 인코딩합니다.

현재 `_hash` 함수는 다음과 같이 해시를 계산합니다:

```solidity
    function _hash(uint256 iv, IStrategy strategy, uint256 maturityDate, uint256 relativeStrike)
        internal
        view
        returns (bytes32)
    {
        (string memory uAsset, string memory oType) = _getAssetAndType(strategy);
        // Incorrect hashing: Missing typeHash and incorrect string encoding
        return keccak256(abi.encode(iv, uAsset, oType, maturityDate, relativeStrike));
    }
```

이 구현에는 EIP-712 표준과 관련하여 두 가지 중요한 결함이 있습니다:

1.  **`typeHash` 누락:** `typeHash` 구성 요소를 완전히 생략합니다. `typeHash`는 서로 다른 데이터 구조 간의 서명 충돌을 방지합니다.
2.  **동적 유형의 잘못된 인코딩:** `string` 변수 `uAsset` 및 `oType`에 대해 `abi.encode`를 사용합니다. EIP-712는 `encodeData` 내의 동적 유형(`string`, `bytes`)이 바이트 내용의 `keccak256` 해시(즉, `keccak256(bytes(uAsset))` 및 `keccak256(bytes(oType))`)로 표현되도록 요구합니다.

- **서명 불일치 위험 높음 (DoS):** 오프체인 서명 서비스가 EIP-712 표준을 **올바르게** 구현하는 경우(`typeHash` 계산 및 문자열 해싱 적절히 수행), 오프체인에서 생성된 서명은 온체인의 비표준 `_hash` 함수에 의해 계산된 해시와 **결코** 일치하지 않습니다. 이로 인해 모든 `buy()` 트랜잭션이 서명 검증에 실패하여 옵션 구매의 핵심 기능에 대한 서비스 거부가 발생합니다.

권장 사항:

EIP-712 `hashStruct` 사양을 엄격히 준수하도록 `_hash` 함수를 리팩토링하십시오.

1.  **`TYPEHASH` 정의:** 서명된 데이터 구조에 대한 유형 문자열을 정의하고 그 `keccak256` 해시를 계산하십시오. 이것은 상수로 저장될 수 있습니다.

2.  **올바른 `encodeData` 구현:** `SIGNED_DATA_TYPEHASH`를 앞에 붙이고 EIP-712 규칙(동적 유형 해싱)에 따라 멤버를 올바르게 인코딩하도록 해싱 로직을 수정하십시오.

# [L-30] 옵션 구매자가 프리미엄을 줄이기 위해 변동성 선택 (Option buyers choose volatility to reduce premium)

내재 변동성은 옵션 가격을 계산하기 위한 Black-Scholes 방정식에서 중요한 역할을 합니다:

**유럽형 콜 옵션**의 경우:

C = S₀ × N(d₁) − K × e^(−rT) × N(d₂)

**유럽형 풋 옵션**의 경우:

P = K × e^(−rT) × N(−d₂) − S₀ × N(−d₁)

여기서:

- (C) = 콜 옵션 가격
- (P) = 풋 옵션 가격
- (S₀) = 기본 자산의 현재 가격
- (K) = 행사가
- (r) = 무위험 이자율 (연간)
- (T) = 만기까지의 시간 (년 단위)
- (σ) = 기본 자산의 변동성 (연간)
- (N(x)) = 표준 정규 분포의 누적 분포 함수 (CDF)

또한 계수 (d₁) 및 (d₂):

d₁ = [ln(S₀ / K) + (r + (½) × σ²) × T] / (σ × √T),
d₂ = d₁ − σ × √T

위에서 언급한 공식에서 알 수 있듯이 σ(변동성)를 줄이면 d₁과 d₂가 각각 증가합니다. 그러나 여기서 핵심 포인트는 N(x)가 증가하더라도 행사가 N(d₂)가 현물 N(d₁)보다 더 많이 증가하고 전체 프리미엄이 감소한다는 것입니다.

또한 N(d₂)가 N(d₁)보다 커지는 경우 계약은 프리미엄을 0으로 만듭니다:

````Solidity
        call = strikeNd2 <= spotNd1 ? spotNd1 - strikeNd2 : 0
````

# [L-31] `Uniswap V3` 쿼터로 인한 높은 가스 사용 위험 (High gas use risk from `Uniswap V3` quoter)

`PositionInteractionFacet` 계약은 포지션 행사 및 청산 중 온체인 가격 계산을 위해 Uniswap V3의 쿼터 계약을 사용합니다. [Uniswap 문서](https://github.com/Uniswap/v3-periphery/blob/464a8a49611272f7349c970e0fadb7ec1d3c1086/contracts/lens/Quoter.sol#L18-L19)에 언급된 바와 같이, 이러한 쿼터 함수는 높은 가스 소비로 인해 온체인 사용을 위해 설계되지 않았습니다.

이 문제는 `_swapAssetToAsset` 함수에 존재합니다:

```solidity
function _swapAssetToAsset(
    address assetIn,
    address assetOut,
    uint256 amountOut
) internal returns (uint256 amountIn) {
    PoolStorage.Layout storage strg = PoolStorage.layout();
    PoolStorage.Dex memory dex = strg.setUp.base.dex;

    // Gas-intensive quote operation
    IQuoterV2.QuoteExactOutputSingleParams memory quoteParams = IQuoterV2.QuoteExactOutputSingleParams({
        tokenIn: assetIn,
        tokenOut: assetOut,
        amount: amountOut,
        fee: dex.dexFee,
        sqrtPriceLimitX96: 0
    });
    (uint256 amountInMaximum,,,) = IQuoterV2(dex.dexQuoter).quoteExactOutputSingle(quoteParams);

    // Swap execution
    IV3SwapRouter.ExactOutputSingleParams memory params = IV3SwapRouter.ExactOutputSingleParams({
        tokenIn: assetIn,
        tokenOut: assetOut,
        fee: dex.dexFee,
        recipient: address(this),
        deadline: block.timestamp + 30 minutes,
        amountOut: amountOut,
        amountInMaximum: amountInMaximum,
        sqrtPriceLimitX96: 0
    });

    amountIn = IV3SwapRouter(dex.dexRouter).exactOutputSingle(params);
}
````

이 구현은 여러 가지 이유로 문제가 있습니다:

1. 쿼터 함수는 전체 스왑 경로를 시뮬레이션하여 가격을 계산하므로 가스 집약적입니다.
2. Uniswap 문서에 따르면 이러한 함수는 온체인 사용을 위해 명시적으로 설계되지 않았습니다.
3. 견적은 중요한 포지션 관리 기능(행사/청산)에 사용됩니다.

포지션 행사/청산은 가스 비용이 가장 높은 변동성이 큰 시장 상황에서 발생할 가능성이 가장 높기 때문에 위험이 특히 높습니다.

권장 사항:

1. 가격 계산에 오라클 기반 접근 방식을 사용하십시오.
2. 또는 합리적인 경계를 가진 낙관적 실행을 구현하십시오.

# [L-32] 이벤트의 `Indexed` 키워드 사용으로 인한 구조체 데이터 손실 (`Indexed` keyword in events causes struct data loss)

동적 배열이나 구조체와 같은 참조 유형 변수에 `indexed` 키워드를 사용하면 언급된 변수의 해시를 반환합니다. 따라서 방출 트랜잭션에 가입된 모든 애플리케이션(예: DApp의 프런트엔드 또는 해당 이벤트의 백엔드 리스너)에 정보를 제공해야 하는 이벤트는 인코딩된 구조체/배열의 keccak256에 해당하는 의미 없고 모호한 32바이트를 얻게 됩니다. 이로 인해 DApp 측에서 몇 가지 문제가 발생하고 데이터 손실로 이어질 수도 있습니다.

문제는 `IStrategy` 계약 내에 존재합니다. `Acquired` 이벤트는 구조체 인스턴스인 데이터 필드가 인덱싱되는 방식으로 정의됩니다. 그렇게 하면 예상 매개변수가 제대로 방출되지 않고 프런트엔드가 의미 없는 단방향 해시를 얻게 됩니다.

`Acquired1`이라는 다른 이벤트를 정의하여 이벤트에 `indexed` 키워드를 생략하는 효과를 볼 수 있습니다:

```Solidity
    event Acquired(
        uint256 indexed id,
        StrategyData indexed data,
        uint256 sizeUSD
    );

    event Acquired1(
        uint256 indexed id,
        StrategyData data,
        uint256 sizeUSD
    );

    struct StrategyData {
        uint128 amount; // 18 decimals
        uint128 strike; // 18 decimals
    }

    function eventEmitter() public {
        StrategyData memory st1 = StrategyData({amount: 42e6, strike: 5});
        emit Acquired(1, st1, 100);
        emit Acquired1(1, st1, 100);
    }
```

결과는 다음과 같습니다:

이벤트 `Acquired`:

```Markdown
	{
		"from": "0x5Ea69b5B2f499Ad9f1127E77d1179cCb471b5e19",
		"topic": "0x8139f37233b28b6f84e65bdca1901a93aaf3c54ef5572840702eb6d3a389ada6",
		"event": "Acquired",
		"args": {
			"0": "1",
			"1": {
				"_isIndexed": true,
				"hash": "0xf4941371d1251c4c97c60a36bd52b6033835913929454e8601110c6f3faecf76"
			},
			"2": "100"
		}
```

이벤트 `Acquired1`:

```Markdown
	{
		"from": "0x5Ea69b5B2f499Ad9f1127E77d1179cCb471b5e19",
		"topic": "0x303c7cf269a5457a46482b73a49633deaacadb51f603cb724dceebbcb647cea1",
		"event": "Acquired1",
		"args": {
			"0": "1",
			"1": [
				"42000000",
				"5"
			],
			"2": "100",
			"id": "1",
			"data": [
				"42000000",
				"5"
			],
			"sizeUSD": "100"
		}
	}
```

권장 사항:

```diff
    event Acquired(
        uint256 indexed id,
-       StrategyData indexed data,
+       StrategyData data,
        uint256 sizeUSD,
        uint256 premium,
        uint256 period,
        uint256 indexed iv
    );
```
