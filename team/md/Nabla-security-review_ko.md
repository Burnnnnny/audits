# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **NablaFinance/contracts-audit-07-2024** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Nabla 정보 (About Nabla)

Nabla는 비영구적 손실을 줄이고 암호화폐, 실물 자산 및 수익 창출 자산 거래에 대해 높은 자본 효율성을 제공하는 AMM입니다. 오라클을 통한 지능형 가격 책정을 사용하고, 위험도가 낮은 단면 스왑 풀(Swap Pools)과 위험을 커버하는 백스톱 풀(Backstop Pool)을 통해 자산 제공과 위험 감수를 분리함으로써, 유동성 제공자에게는 더 높은 수익을, 트레이더에게는 더 낮은 스왑 비용을 제공합니다.

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

**_검토 커밋 해시_ - [5b44b7bf8bcfa5f33be543884e960e8ddbb202f7](https://github.com/NablaFinance/contracts-audit-07-2024/tree/5b44b7bf8bcfa5f33be543884e960e8ddbb202f7)**

**_수정 검토 커밋 해시_ - [f8c9a257ff3396a739b4ec3a7e1a5b82932e99b3](https://github.com/NablaFinance/contracts-audit-07-2024/tree/f8c9a257ff3396a739b4ec3a7e1a5b82932e99b3)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `BackstopPoolCore`
- `PythAdapter`
- `OraclePriceAdapter`
- `GenericPool`
- `NablaBackstopPool`
- `NablaPortal`
- `NablaRouter`
- `RouterCore`
- `SwapPool`

# 발견 사항 (Findings)

# [H-01] 스왑 풀 입금에 대한 냉각 기간 우회 가능 (Cool-off period for deposits in swap pools can be bypassed)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

스왑 풀에 대한 입금은 백스톱 풀을 통해 인출할 수 있기 전에 냉각 기간(cool-off period)을 거쳐야 합니다.

그러나 입금으로 받은 LP 토큰을 다른 주소로 전송함으로써 이 제한을 우회할 수 있습니다.

## 개념 증명 (Proof of concept)

`BackstopPool.t.sol` 파일에 다음 코드를 추가하고 `forge test --mt test_bypassDepositCoolOff`를 실행하십시오.

```solidity
function test_bypassDepositCoolOff() public
    changeSwapPoolCoverageTo(swapPool2, 0.2e18)
{
    uint256 initialUsdBalance = usd.balanceOf(address(this));
    uint256 initialAsset2Balance = asset2.balanceOf(address(this));

    // Setup
    vm.roll(20e6);
    address secondaryAccount = makeAddr("secondaryAccount");
    deal(secondaryAccount, 100);
    backstop.setInsuranceFee(address(swapPool2), 100);
    bytes[] memory _mockUpdateData = _makeMockPriceUpdateData(2);

    // Deposit 10 asset2 into swapPool2 and transfer LP tokens to secondary account
    (uint256 lpTokens, ) = swapPool2.deposit(10e18, 0, block.timestamp);
    swapPool2.transfer(secondaryAccount, lpTokens);

    // From secondary account, redeem LP tokens and transfer USD token to main account
    vm.startPrank(secondaryAccount);
    asset2.approve(address(swapPool2), MAX_UINT256);
    backstop.redeemSwapPoolShares{value: 2}(
        address(swapPool2),
        lpTokens,
        0,
        block.timestamp,
        _mockUpdateData
    );
    usd.transfer(address(this), usd.balanceOf(secondaryAccount));
    vm.stopPrank();

    // The result is an instant profit > 9%
    uint256 usdReceived = usd.balanceOf(address(this)) - initialUsdBalance;
    uint256 asset2Spent = initialAsset2Balance - asset2.balanceOf(address(this));
    uint256 asset2SpentInUsd = asset2Spent * ASSET2_PRICE / 1e18;
    uint256 profit = usdReceived - asset2SpentInUsd;
    assert(profit > 0.9e18);
}
```

## 권장 사항

해결책은 `SwapPool` 계약의 `_transfer` 함수를 재정의하여 수신자 주소에 대한 `latestDepositAtBlockNo` 값을 업데이트하는 것일 수 있습니다. 그러나 이는 수신자 주소로 1 wei를 보내 백스톱 풀을 통한 인출을 DoS하는 데 사용될 수 있습니다. 더 나은 해결책은 각 주소에 대해 냉각 상태인 토큰 수와 마지막으로 받은 입금 또는 전송의 블록 번호를 저장하고 업데이트하는 것입니다. 이렇게 하면 사용자는 냉각 상태의 토큰을 초과하는 토큰 양을 인출할 수 있습니다.

# [M-01] Pyth 오라클 가격이 적절하게 검증되지 않음 (Pyth oracle price is not validated properly)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`PythAdapter.getAssetPrice`는 `price`, `conf`, `expo` 값에 대해 입력 검증을 수행하지 않으므로 계약이 유효하지 않거나 신뢰할 수 없는 가격을 수락할 수 있습니다.

[Pyth 문서](https://docs.pyth.network/price-feeds/best-practices#confidence-intervals)에 명시된 대로 신뢰 구간(confidence interval)을 검증하여 계약이 신뢰할 수 없는 가격을 수락하는 것을 방지하는 것이 특히 중요합니다.

## 권장 사항

```diff
-       (int64 price, int32 expo) = (
+       (int64 price, uint64 conf, int32 expo) = (
             pythStructsPrice.price,
+           pythStructsPrice.conf,
             pythStructsPrice.expo
         );

+       if (price <= 0 || expo < -18) {
+           revert("PA:getAssetPrice:INVALID_PRICE");
+       }
+
+       if (conf > 0 && (price / int64(conf) < MIN_CONFIDENCE_RATIO)) {
+           revert("PA:getAssetPrice:UNTRUSTED_PRICE");
+       }
```

# [M-02] 누구나 함수를 호출할 수 있음 (Function can be called by anyone)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`NablaRouter` 계약에서 `swapExactTokensForTokens` 함수는 `allowed` 수정자를 사용하여 호출자가 함수에 액세스할 수 있는지 확인합니다. 그러나 `swapExactTokensForTokensWithoutPriceFeedUpdate` 함수에는 이 수정자가 없습니다.

따라서 모든 주소는 `PriceOracleAdapter.updatePriceFeeds`와 `swapExactTokensForTokensWithoutPriceFeedUpdate`를 호출하여 `swapExactTokensForTokens`의 액세스 확인을 우회할 수 있습니다.

## 권장 사항

`swapExactTokensForTokensWithoutPriceFeedUpdate`에 `allowed` 수정자를 추가하십시오.

# [M-03] 백스톱 풀에서 스왑 풀을 제거할 수 없음 (Inability to remove swap pools from the backstop pool)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

백스톱 풀에서 스왑 풀이 커버되는 풀 목록에 추가되면 제거할 수 없습니다.

Pyth 네트워크가 커버되는 풀 중 하나의 자산에 대한 가격 피드를 더 이상 사용하지 않거나 잘못된 데이터를 반환하기 시작하면 `_getAllTokenPrices` 실행이 되돌려져 백스톱 풀에서 입금이나 인출이 불가능해지고, 결과적으로 커버되는 풀의 LP 자금이 잠기게 됩니다.

## 권장 사항

백스톱 풀 소유자에게 커버되는 스왑 풀 목록에서 스왑 풀을 제거할 수 있는 기능을 제공하십시오.

# [M-04] 초과 ETH가 환불되지 않음 (Excess ETH is not refunded)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

여러 인스턴스에서 가격 피드가 업데이트되지만 전송된 초과 ETH는 환불되지 않습니다. 이로 인해 자금이 영원히 계약에 갇히게 됩니다.

1. NablaRouter.sol - `swapExactTokensForTokens`

2. NablaBackstopPool.sol - `deposit`, `finalizeWithdrawBackstopLiquidity`, `redeemSwapPoolShares`, `finalizeWithdrawExcessSwapLiquidity`, `redeemCrossSwapPoolShares`

3. NablaPortal.sol - `swapExactTokensForEth`, `_updatePriceFeeds`

## 권장 사항

두 가지 잠재적인 수정 사항:

1. 이 함수들에서 업데이트 수수료 확인을 구현하고, 전송된 `msg.value`가 충분한지 비교하고, 초과 토큰을 환불합니다.

`swapEthForExactTokens` 함수에서 대략적으로 유사한 로직을 찾을 수 있으며, 아래 코드를 적용할 수 있습니다.

```solidity
//...
        uint256 updateFee = getUpdateFee(oracleAdapter, _priceUpdateData); //@note a function to query fee can be created in OraclePriceUpdater.sol
        uint256 refundAmount = msg.value - updateFee;
        if (refundAmount > 0) {
            (bool success, ) = msg.sender.call{value: refundAmount}("");
            require(success, "NP:swapEthForExactTokens:REFUND_FAILED");
        }

```

2. 이러한 계약에 관리자가 초과 토큰을 회수하는 데 사용할 수 있는 스윕(sweep) 함수를 생성합니다.

# [M-05] 보험 타임락에 결함이 있음 (Insurance timelock is flawed)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

사용자가 스왑 풀에 입금하면 최신 입금 블록 번호가 등록됩니다. 이는 사용자가 필요한 보험 기간을 기다리지 않고 `backstopBurn` 함수를 통해 스왑 풀 LP 토큰을 즉시 백스톱 자산으로 상환하는 것을 방지하기 위함입니다. 그러나 이를 추적하는 방식은 보낸 사람에게 `block.number`를 직접 매핑하는 것이므로, 사용자가 새로운 입금을 할 때마다 이전에 추적된 `block.number`를 덮어씁니다. 결과적으로 대규모 풀 예금자는 LP 토큰을 상환할 수 있기 전까지 더 긴 기간을 기다려야 합니다.

```solidity
    function deposit(
//...
        latestDepositAtBlockNo[msg.sender] = block.number;
//...
        _processDeposit(_depositAmount, sharesToMint_);
//...
    }
```

BackstopPoolCore.sol의 `initiateWithdraw` 함수에서도 동일한 현상이 관찰되며, 함수의 코드 주석에 따르면 이는 의도된 설계로 보입니다.

## 권장 사항

대신 각 입금을 ID와 해당 `block.number`로 추적하는 것을 권장합니다. 그러면 사용자, 특히 지속적인 풀 예금자는 더 긴 대기 기간을 감수할 필요가 없습니다.

# [M-06] 전송 수수료(Fee-on-transfer) 및 리베이스(rebase) 토큰

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

코드베이스의 여러 부분에서 자산은 전송된 금액이 곧 수신된 금액이라는 가정하에 전송됩니다. 그러나 특정 토큰을 다룰 때는 그렇지 않습니다.

i. 일부는 전송 중 수수료를 부과합니다(예: PAXG). 또한 USDT와 같은 토큰에는 수수료를 부과할 수 있는 옵션이 있지만 현재는 부과하지 않는다는 점에 유의해야 합니다.

ii. 특히 stETH와 같은 일부 토큰에는 전송 중 수신된 금액이 지정된 금액보다 적은 [1~2 wei 코너 케이스](https://docs.lido.fi/guides/lido-tokens-integration-guide/#1-2-wei-corner-case)가 있습니다.

iii. 일부 토큰은 긍정적 및 부정적으로 리베이스하여 보유자의 잔액이 시간이 지남에 따라 증가하거나 감소합니다. stETH도 이 작업을 수행합니다. 여기에는 에어드랍 등을 제공하는 토큰도 포함됩니다.

이러한 토큰을 사용하면 프로토콜의 회계를 엉망으로 만들 수 있습니다. 전송 함수가 이를 처리하도록 최적화되어 있지 않기 때문에, 풀의 자산 잔액이 예상 및 추적된 금액보다 훨씬 적은 상황이 발생할 수 있습니다. 예를 들어, 프로토콜은 이러한 상황을 커버하기 위해 추가 비용을 부담하게 됩니다.
또한 에어드랍 및 긍정적 리베이스로 받은 토큰은 회수할 방법이 없으므로 영원히 손실될 수 있습니다.

영향을 받는 함수는 다음과 같습니다.

1. NablaPortal.sol - `swapExactTokensForEth` #L234, `swapExactTokensForTokens` #L293

2. GenericPool.sol - `_processDeposit` #L96, `_processWithdrawal` #L118

3. BackstopPoolCore.sol - `_redeemSwapPoolShares` L466,

4. SawPool.sol - `backstopDrain` #L511, `swapIntoFromRouter` #L585, `swapOutFromRouter` #L670 #L675,

5. RouterCore.sol - `_executeSwap` #L230 #L245,

## 권장 사항

프로토콜 안팎으로 토큰을 전송하기 전에 전후의 계약 잔액을 확인하고 차액을 보낸 금액으로 등록하는 것을 권장합니다. 이는 전송 수수료 토큰과 1 wei 코너 케이스를 처리하는 데 도움이 됩니다.
리베이스 토큰 및 가변 잔액의 경우 잔액 추적 및 초과 토큰 스윕(sweep) 함수 시스템을 구현하여 계약에서 초과 토큰을 주기적으로 걷어내어 손실되는 것을 방지할 수 있습니다.

대안으로, 이러한 토큰 유형을 명시적으로 차단 목록에 추가하여 풀 자산으로 만들어지는 것을 방지하십시오.

# [M-07] `swapIntoFromRouter`에 `checkPoolCap` 수정자 누락 (`swapIntoFromRouter` missing the `checkPoolCap` modifier)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

풀에 입금할 때 풀 캡(pool cap)이 강제됩니다. 이는 `checkPoolCap` 수정자를 통해 수행됩니다. 수정자는 입금되는 금액과 함께 풀의 자산 잔액을 추적하고 풀 캡과 비교합니다.

```solidity
    modifier checkPoolCap(uint256 _additionalDeposit) {
        require(poolAsset.balanceOf(address(this)) + _additionalDeposit <= poolCap, "deposit: CAP_EXCEEDED");
        _;
    }
```

이는 `deposit` 함수를 통해서든 아니든 풀에 들어오는 모든 토큰이 잠재적으로 캡에 합산됨을 의미합니다.

이는 `swapIntoFromRouter` 함수에서도 발생하는데, 풀 자산이 라우터에서 풀로 전송되어 풀의 자산 잔액을 높이고 간접적으로 스왑되는 풀에 대한 풀 캡 요구 사항을 높이기 때문입니다. 결과적으로 풀 캡은 쉽게 고갈될 수 있으며 스왑을 통해 우회될 수도 있습니다. 이런 일이 발생하면 사용자는 풀에 입금할 수 없습니다.

```solidity
    function swapIntoFromRouter(
        uint256 _amount
    )
        external
        nonReentrant
        onlyRouter
        whenNotPaused
        returns (uint256 effectiveAmount_)
    {
        effectiveAmount_ = _quoteSwapInto(_amount);
        reserve = reserve + effectiveAmount_;
        reserveWithSlippage = reserveWithSlippage + _amount;

        poolAsset.safeTransferFrom(msg.sender, address(this), _amount);
    }
```

풀 캡과 관련하여 주목해야 할 다른 중요한 요소:

1. 고래(whales)에 의해 의도적으로 또는 의도하지 않게 DoS될 수 있습니다.
2. 풀 캡에 도달하려고 할 때 잠재적 경쟁 조건(race condition)이 발생할 수 있습니다.
3. 악의적인 사용자는 풀에 많은 양의 자산을 보내 풀 잔액을 간접적으로 높일 수 있습니다. 이는 토큰의 가치가 크지 않아 공격 비용이 저렴하거나 다양한 공격자가 조직적으로 노력하는 경우에 가장 효과적입니다.

## 권장 사항

`swapIntoFromRouter`에 `checkPoolCap` 수정자를 추가하거나 소스별로 풀 자산을 추적하는 것을 고려하십시오. 그리고 `checkPoolCap` 수정자에서 입금된 자산만 쿼리하십시오.

# [M-08] 사용자가 부실 가격을 사용하기 위해 가격 피드 업데이트를 생략할 수 있음 (Users can omit to update the price feed to use stale prices)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

> [!NOTE]
> 내부적으로 `_updatePriceFeeds()`를 호출하는 모든 함수는 이 공격에 취약합니다.

Nabla 프로토콜의 모든 함수를 호출할 때 사용자는 사용하려는 자산 가격을 업데이트하기 위해 업데이트 수수료와 `_priceUpdateData()`를 제공해야 합니다. 예를 들어 `redeemCrossSwapPoolShares()` 함수를 고려해 보겠습니다.

```solidity
    function redeemCrossSwapPoolShares(
        address _swapPool,
        address _targetSwapPool,
        uint256 _shares,
        uint256 _minAmount,
        uint256 _deadline,
        bytes[] calldata _priceUpdateData
    )
        external
        payable
        nonReentrant
        whenNotPaused
        returns (uint256 finalAmount_)
    {
        _updatePriceFeeds(address(router.oracleAdapter()), _priceUpdateData);

        (finalAmount_, ) = _executeRedeemCrossSwapPoolShares(
            _swapPool,
            _targetSwapPool,
            _shares,
            _minAmount
        );
    }
```

위에서 보듯이 `_updatePriceFeeds()`는 사용자가 제공한 `_priceUpdateData` 배열과 함께 호출됩니다. 프로토콜은 사용자가 항상 유효한 가격 피드 데이터를 제공할 것이라고 가정하지만 악의적인 사용자는 이 가정을 악용할 수 있습니다. 그들은 `_updatePriceFeeds()`에 관련 없는 토큰 데이터를 전달하여 `priceMaxAge` 제한 내에 있는 한 부실 가격(stale prices)을 효과적으로 사용할 수 있습니다.

예를 들어, 스왑 풀 자산이 가격이 1분 이내에 변동하는 변동성이 큰 토큰인 경우, 공격자는 가격 피드를 업데이트하지 않고 `redeemCrossSwapPoolShares()`를 호출하여 토큰을 할인된 가격에 받아 이를 악용할 수 있습니다.

## 권장 사항

특정 자산에 대한 `_priceUpdateData` 배열을 계산하는 표준 함수를 구현하여 가격 피드가 일관되게 업데이트되도록 하십시오.

# [M-09] SwapPool은 플래시론 공격에 취약함 (SwapPool's are vulnerable to flashloan attacks)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

SwapPool은 사용자가 유동성(LP)을 제공하고 수수료를 받을 수 있도록 합니다. 그러나 유동성 제공자가 동일한 블록 내에서 입금 및 인출할 수 있는 기능은 시스템을 플래시론 공격에 취약하게 만들어 다른 LP의 보상을 착취할 수 있게 합니다. 악의적인 사용자는 합법적인 입금을 플래시론 입금으로 프론트런닝(front-run)한 다음 동일한 블록에서 인출로 백런닝(back-run)하여 LP 수수료의 일부를 효과적으로 빼돌릴 수 있습니다. 다음은 공격을 보여주는 개념 증명입니다.

```solidity
    function testFlahloan()
        public
        changeSwapPoolCoverageTo(pool, 0.3e18)
    {
        uint256 _accumulatedSlippageInitial = pool.reserveWithSlippage() -
            pool.reserve();

        // attacker frontrun the user deposit with 10m flashloan
        address attacker = makeAddr("attacker");
        uint256 flashloan = 10_000_000 ether;

        vm.startPrank(attacker);
        asset.approve(address(pool), MAX_UINT256);
        asset.mint(attacker, flashloan);
        (uint256 _lpTokens,) = pool.deposit(flashloan, 0, block.timestamp + USER_DEADLINE_TOLERANCE);
        vm.stopPrank();

        // user deposit 100k assets to the pool
        pool.deposit(
            100_000 ether,
            0,
            block.timestamp + USER_DEADLINE_TOLERANCE
        );

        // the attacker withdraw his shares and rip 0.01193665464548911 assets
        vm.prank(attacker);
        pool.withdraw(
            _lpTokens,
            flashloan,
            block.timestamp + USER_DEADLINE_TOLERANCE
        );
    }
```

이 예에서 공격자는 0.01193665464548911 자산(자산 가격이 1000달러인 경우 11달러)을 얻습니다. `_targetCoverageRatio`(이 PoC에서는 0.3e18로 설정)가 낮을수록 공격자의 이득은 커집니다.

## 권장 사항

이 취약점을 완화하려면 입금과 인출 사이에 지연을 강제하는 것을 고려하십시오.

# [M-10] 동일한 블록에서 다른 가격을 사용한 차익 거래 기회 (Arbitrage opportunity using different prices in the same block)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`PythAdapter` 계약의 현재 구현은 동일한 블록에서 동일한 토큰에 대해 다른 가격을 사용할 수 있도록 허용합니다. 악의적인 사용자는 이를 악용하여 위험 부담 없이 이익을 얻을 수 있습니다.

특정 토큰의 가격 피드를 업데이트하지 않고 작업을 수행하는 방법에는 여러 가지가 있습니다.

- `updatePriceFeeds`에 전송된 배열에서 대상 토큰을 생략합니다.
- 대상 토큰에 대해 오래된 가격을 제출합니다(`updatePriceFeeds`는 이 경우 되돌리지 않음).
- `swapExactTokensForTokensWithoutPriceFeedUpdate`를 통해 스왑합니다.

이런 식으로 사용자는 오래된 가격으로 스왑을 수행한 다음 업데이트된 가격으로 반대 방향으로 작업을 수행할 수 있습니다.

> 어떤 작업 전에 가격 피드를 성공적으로 업데이트하도록 강제하더라도 동일한 블록에서 수행된 다른 작업에서 나중에 더 최신 가격을 제출하는 것이 가능하므로 여전히 차익 거래가 가능하다는 점에 유의하십시오.

## 개념 증명 (Proof of concept)

타임스탬프 `t`에서 Pyth 네트워크의 가격은 다음과 같습니다:

- WBTC/USD: $50,000
- USDC/USD: $1

블록 `b`에서 위의 가격으로 `updatePriceFeeds`가 호출됩니다.

타임스탬프 `t + 10`에서 `WBTC/USD` 가격이 $51,000로 상승합니다.

블록 `b + 1`에서 앨리스는 `WBTC` 가격을 업데이트하지 않고 `swapExactTokensForTokens`를 호출하여 50,000 USDC를 1 WBTC로 스왑합니다. 동일한 블록에서 앨리스는 다시 `swapExactTokensForTokens`를 호출하고 이번에는 `WBTC` 가격을 $51,000로 업데이트하여 1 WBTC를 51,000 USDC로 스왑하여 위험 없이 이익을 얻습니다.

이 예에서는 단순화를 위해 수수료를 고려하지 않았습니다. `총 수수료 < Δ 가격 * 금액`인 한 거래는 수익성이 있습니다. 이 예에서 수수료가 $1,000(2%) 미만이면 앨리스는 이익을 얻었을 것입니다.

## 권장 사항

가능한 해결책은 블록에서 처음 사용될 때 토큰의 현재 가격을 저장하고 블록의 나머지 부분에서 이 가격을 사용하는 것입니다. 이렇게 하면 토큰 가격이 블록 전체에서 일관되게 유지됩니다.

# [L-01] 특정 스왑 풀을 등록 해제할 수 없음 (Certain swap pools cannot be unregistered)

RouterCore.sol에서 소유자는 `unregisterPool` 함수를 호출하여 풀을 등록 해제할 수 있습니다. 그 다음에는 0에 대한 승인이 이어져 자산에 대한 풀의 이전 승인을 제거합니다. [BNB](https://etherscan.io/token/0xB8c77482e45F1F44dE1745F52C74426C631bDD52#code#L94)와 같은 일부 토큰은 0 값 승인 시 되돌립니다. 결과적으로 이러한 풀은 등록 해제할 수 없습니다.

```solidity
    function unregisterPool(
        address _asset
    ) external onlyOwner returns (bool success_) {
        require(_asset != address(0), "RC:unregisterPool:NO_ASSET");
        address pool = address(poolByAsset[_asset]);
        require(pool != address(0), "RC:unregisterPool:NO_POOL");

        delete poolByAsset[_asset];
        IERC20(_asset).safeApprove(pool, 0);

        emit SwapPoolUnregistered(msg.sender, _asset);
        return true;
    }
```

이에 대한 최선의 수정 사항은 이러한 유형의 토큰을 주의하고 애초에 등록하지 않는 것입니다.
코드에서 이를 수정하려면 `safeDecreaseAllowance` 함수를 사용하여 풀의 허용량을 `type(uint256).max`에서 1 wei와 같이 매우 무시할 수 있는 양으로 줄일 수 있습니다. 설정은 try-catch 블록으로 래핑할 수 있습니다.

# [L-02] 자산을 등록 해제하는 기능이 없음 (No function to unregister assets)

NablaPortal.sol에는 자산을 라우터에 연결하는 `registerAsset` 함수가 있지만 연결을 해제하는 함수는 없습니다. 자산이나 라우터가 손상되면 해당 자산을 등록 해제할 방법이 없습니다. 이를 수행하는 권한이 있는 함수를 도입하는 것을 권장하며, 이 함수는 라우터의 허용량도 지워야 합니다. RouterCore.sol의 `unregisterPool` 구현을 참조하십시오.

# [L-03] 허용되지 않은 사용자가 공유를 인출할 수 있음 (Non-allowed users can withdraw shares)

NFT 게이팅 스왑 풀에서는 승인된 사용자만 풀에 입금할 수 있습니다.

```solidity
    function deposit(
        uint256 _depositAmount,
        uint256 _minLPAmountOut,
        uint256 _deadline
    )
        external
        nonReentrant
        whenNotPaused
        allowed
        returns (uint256 sharesToMint_, int256 fee_)
    {
```

그러나 승인되지 않은 사용자가 공유를 인출할 수 있습니다. 사용자는 입금 액세스 권한이 없더라도 오픈 마켓에서 직접 공유를 구매하여 풀에서 인출할 수 있습니다. 이를 완화하려면 인출 함수에 allowed 수정자를 추가하는 것을 고려하십시오.

# [L-04] 작은 조각으로 상환하여 수수료를 우회할 수 있음 (Fees can be bypassed by redeeming in small fragments)

NablaBackstopPool 계약은 모든 ERC20 토큰을 지원합니다. 사용자가 스왑 풀 공유를 상환할 때 풀은 다음과 같이 계산된 보험료를 받습니다.

```solidity
        uint256 fee = (backstopTokenAmount * swapPoolConfig.insuranceFeeBps) /
            INSURANCE_FEE_PRECISION;

        unchecked {
            amountOut_ = backstopTokenAmount - fee;
        }
```

사용자는 작고 조각난 상환을 통해 이 계산을 악용하여 수수료가 0으로 반올림되도록 할 수 있습니다. 이 문제는 [2 소수점](https://etherscan.io/token/0x056Fd409E1d7A124BD7017459dFEa2F387b6d5Cd#readContract#F3)을 가진 [GUSD](https://etherscan.io/token/0x056Fd409E1d7A124BD7017459dFEa2F387b6d5Cd)와 같이 소수점 정밀도가 낮은 토큰의 경우 특히 문제가 됩니다. 다음 시나리오를 고려하십시오.

> [!IMPORTANT]
> 공격은 가스 비용이 낮은 L2에서만 효과적입니다.

- `poolAsset` = GUSD
- `insuranceFeeBps` = 100
  - Bob은 공유와 자산 간의 1:1 변환을 가정하고 `_shares = 99`로 `redeemSwapPoolShares()`를 호출합니다.
  - 계산:
    - `(backstopTokenAmount * insuranceFeeBps) / INSURANCE_FEE_PRECISION = (99 * 100) / 10000 = 0`
    - 보험료는 0으로 반올림되어 풀은 99 토큰의 각 상환에 대해 수수료를 받지 못합니다.

이 익스플로잇을 방지하려면 다음 확인을 추가하는 것을 고려하십시오.

```solidity
if (swapPoolConfig.insuranceFeeBps > 0) {
        require(
            (backstopTokenAmount * swapPoolConfig.insuranceFeeBps) >=
                INSURANCE_FEE_PRECISION,
            "BC:_redeemSwapPoolShares:ZERO_FEES"
        );
}
```

# [L-05] BackstopPool은 인플레이션 공격에 취약함 (BackstopPool is vulnerable to inflation attack)

- ERC4626 금고와 유사하게 작동하는 BackstopPool은 인플레이션 공격에 취약합니다.
- 공격자는 최소한의 초기 입금(예: 1 wei)을 한 다음 인위적으로 totalPoolWorth를 팽창시켜 이 취약점을 악용할 수 있습니다.
- 이 조작으로 인해 기본 주식 가격이 훨씬 높아져 모든 후속 입금에 영향을 미칩니다.
- 내림(rounding down)으로 인해 악의적인 초기 입금이 다른 입금을 프론트런닝하는 경우 공격자는 부풀려진 가격으로 1 wei 상당의 주식을 받게 됩니다.

다음은 이 공격을 보여주는 개념 증명(PoC)입니다.

```solidity
    function testInflationAttack() public {
        bytes[] memory _mockUpdateData = _makeMockPriceUpdateData(BACKSTOP_ASSETS_QUANTITY);
        uint256 _updateFee = pythAdapter.getUpdateFee(_mockUpdateData);
        address attacker = makeAddr("Attacker");
        address victim = makeAddr("Victim");

        setupPostion(attacker);
        backstop.deposit{value: _updateFee}(
            1 wei,
            0,
            block.timestamp + USER_DEADLINE_TOLERANCE,
            _mockUpdateData
        );
        usd.transfer(address(backstop), 100 ether);
        vm.stopPrank();

        setupPostion(victim);
        backstop.deposit{value: _updateFee}(
            200 ether,
            0,
            block.timestamp + USER_DEADLINE_TOLERANCE,
            _mockUpdateData
        );
        vm.stopPrank();

        vm.prank(attacker);
        backstop.initiateWithdraw(1 wei);

        vm.warp(INITIAL_WITHDRAWAL_DELAY_PERIOD + 1);

        uint256 balanceBefore = IERC20(backstop.asset()).balanceOf(attacker);

        vm.prank(attacker);
        backstop.finalizeWithdrawBackstopLiquidity{value: _updateFee}(
            1 wei,
            block.timestamp + USER_DEADLINE_TOLERANCE,
            _mockUpdateData
        );

        uint256 balanceAfter = IERC20(backstop.asset()).balanceOf(attacker);

        console.log(balanceAfter - balanceBefore); // 150e18
    }
```

보시다시피 공격자는 성공적으로 50e18 자산을 얻었습니다.

> 전체 설정: [링크](https://gist.github.com/0xbtk/7d53c10f222da80ece9aa4b5b8f49fb7)

첫 번째 입금에 대해 고정된 양의 주식을 발행하거나 ERC4626에 구현된 대로 "가상 주식"을 사용하는 것을 고려하십시오.

