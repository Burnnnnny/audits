
# Pashov Audit Group 소개

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 입증된 인재들입니다. 대부분은 공개 대회 보상으로 10만 달러 이상을 획득했거나, 다회 우승자이거나, 저희와의 감사에서 진정으로 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도우면서, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

<p><strong>Telos-Consilium/ouroboros-contracts</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, <strong>unforgiven, merlinboii, IvanFitro, ni8mare</strong>가 <strong>YuzuUSD</strong> 검토에 참여했습니다. 총 <strong>18</strong>개의 이슈가 발견되었습니다.</p>

# YuzuUSD 소개

<p>YuzuUSD는 USDC에 1:1로 지원되는 안정적인 ERC-20 토큰이며 Yuzu 프로토콜 전체에서 가치의 핵심 단위 역할을 합니다. 생태계에는 YuzuUSD를 스테이킹하기 위한 ERC-4626 저장소인 StakedYuzuUSD와 유동성, 수익 및 위험의 균형을 유지하도록 설계된 발행, 상환 및 스테이킹 메커니즘이 있는 보험 유동성 풀(Insurance Liquidity Pool)의 예금을 나타내는 ERC-20 토큰인 YuzuILP가 포함됩니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [6dab29807b9e54d2b41cff9ffccbc63b8442d6a8](https://github.com/Telos-Consilium/ouroboros-contracts/tree/6dab29807b9e54d2b41cff9ffccbc63b8442d6a8)<br>&nbsp;&nbsp;(Telos-Consilium/ouroboros-contracts)

**수정 검토 커밋 해시:**<br>• [b4f1d18d8d21b00394b105e44bdab3739fb3037d](https://github.com/Telos-Consilium/ouroboros-contracts/tree/b4f1d18d8d21b00394b105e44bdab3739fb3037d)<br>&nbsp;&nbsp;(Telos-Consilium/ouroboros-contracts)

# 범위

- `YuzuUSD.sol`
- `YuzuILP.sol`
- `StakedYuzuUSD.sol`
- `YuzuIssuer.sol`
- `YuzuOrderBook.sol`
- `YuzuProto.sol`
- `interfaces/`

# 발견 사항

# [H-01] `YuzuILP` 계약의 보류 중인 인출이 totalAssets 계산에서 고려되지 않음 (Pending withdrawals in `YuzuILP` contract are not considered in totalAssets calculation)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`YuzuILP`에서 사용자가 상환 주문을 생성하면 계약은 해당 시점의 저장소 가격을 기준으로 자격이 있는 자산 금액을 계산하고 기록합니다.

```Solidity
    // from YuzuOrderBook file
    function createRedeemOrder(uint256 tokens, address receiver, address owner)
        public
        virtual
        returns (uint256, uint256)
    {
        if (receiver == address(0)) {
            revert InvalidZeroAddress();
        }
        uint256 maxTokens = maxRedeemOrder(owner);
        if (tokens > maxTokens) {
            revert ExceededMaxRedeemOrder(owner, tokens, maxTokens);
        }

        uint256 assets = previewRedeemOrder(tokens);
        address caller = _msgSender();
        uint256 orderId = _createRedeemOrder(caller, receiver, owner, tokens, assets);

        emit CreatedRedeemOrder(caller, receiver, owner, orderId, assets, tokens);

        return (orderId, assets);
    }
```

그러나 인출되는 토큰은 저장소 회계에서 제외되지 않으며 주문이 확정될 때까지 `totalAssets()` 및 `totalSupply()`의 일부로 유지됩니다. 즉, 사용자의 상환 가치는 고정되어 있지만 토큰은 저장소 내에서 계속 수익을 창출합니다. 사용자가 나중에 주문을 확정하면 원래 기록된 자산 금액만 전송되고, **보류 기간 동안 누적된 수익은 저장소에 남습니다**. 이로 인해 인출하는 사용자는 정당한 이익의 일부를 잃게 되며, 해당 이익은 대신 남은 참여자들에게 재분배됩니다.

## 권장 사항

상환 주문이 생성되는 즉시 `totalAssets()` 및 `totalSupply()`에서 제외하도록 회계를 조정하여 보류 중인 인출이 추가 수익을 창출하는 것을 방지해야 합니다.

# [H-02] 비원자적 updatePool()로 샌드위치 공격 및 추출 가능 (Non-atomic updatePool() enables sandwich attacks and extraction)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`YuzuILP.updatePool()`은 풀 상태를 업데이트할 때 원자성(atomicity)이 부족하여 샌드위치 공격을 통해 악용될 수 있는 방식으로 `poolSize`를 조작할 수 있습니다.

이 함수는 현재 상태에 대해 검증하거나 진행 중인 사용자 작업을 고려하지 않고 외부에서 제공된 입력으로 `poolSize`를 직접 설정합니다.

```solidity
function updatePool(uint256 newPoolSize, uint256 newDailyLinearYieldRatePpm) external onlyRole(POOL_MANAGER_ROLE) {
    if (newDailyLinearYieldRatePpm > 1e6) {
        revert InvalidYield(newDailyLinearYieldRatePpm);
    }

    poolSize = newPoolSize;
    dailyLinearYieldRatePpm = newDailyLinearYieldRatePpm;
    lastPoolUpdateTimestamp = block.timestamp;

    emit UpdatedPool(newPoolSize, newDailyLinearYieldRatePpm);
}
```

다음 시나리오를 고려해 보십시오:

1. User1과 User2가 각각 `100e6` 자산을 풀에 예치합니다.
2. 재무부(treasury)가 예금을 통해 유동성 버퍼에 자금을 조달합니다 (총 `200e6`).
3. 풀 관리자가 `updatePool(200e6, 0.1e6)`을 호출하여 `10%` 수익률을 설정할 준비를 합니다.
4. User1이 이 트랜잭션을 선행 실행(front-runs)하고 지분의 절반을 상환하여 실제 `poolSize`를 줄입니다.
5. `updatePool()` 트랜잭션이 실행되어 `poolSize`를 인위적으로 `200e6`으로 복원합니다.
6. User1이 남은 지분에 대해 또 다른 상환으로 후속 실행(back-runs)을 수행하며, 이제 가치가 부풀려졌습니다.
7. User1은 공정한 몫보다 더 많은 자산을 추출하여 사실상 User2로부터 훔칩니다.

**개념 증명**: [test_audit_sandwichPoolUpdate](https://gist.github.com/merlinboii/b125979b081266de2491e883712713fc#file-yuzuilp-t-sol-L116)

## 권장 사항

`updatePool()`은 전체 풀의 가격 책정 및 회계에 사용되는 상태를 업데이트하므로 원자성을 유지해야 합니다.

가능한 해결책은 프로토콜이 일시 중지된 동안에만 호출할 수 있게 하거나 관리자 업데이트 후 사용자 작업에 대한 짧은 냉각 기간(cooldown)을 추가하는 것입니다. 이를 통해 운영자는 예금이나 상환이 재개되기 전에 상태 일관성을 확인할 시간을 갖게 됩니다.

# [H-03] 풀 회계에서 미수금 수수료로 인한 수수료 회피 가능 (Fee avoidance possible by uncollected fees in pool accounting)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`YuzuILP` 및 `StakedYuzuUSD` 계약 모두 인출 및 상환 중에 징수된 수수료가 풀의 사용 가능한 자산에서 제대로 분리되지 않는 회계 문제가 있습니다. 이는 남은 지분이 이전 상환으로 징수된 수수료의 혜택을 받기 때문에 수수료를 최소화할 수 있는 기회를 만듭니다.

- `YuzuILP._withdraw()`에서 사용자가 지분을 상환할 때 수수료는 그들이 받는 자산에서 공제되지만 수수료 금액은 `poolSize`에 남습니다.
- 마찬가지로 `StakedYuzuUSD._initiateRedeem()`에서 상환 주문 시작 중에 지분이 소각되면 `totalSupply`는 감소하지만 기본 자산(수수료 포함)은 계약에 남습니다.

즉, 수수료는 프로토콜에 의해 징수되지 않고 풀의 사용 가능한 자산의 일부로 유지되어 남은 주주에게 이익이 됩니다.

**다음 시나리오를 고려해 보십시오**:
0. User A와 User B가 자산을 저장소에 예치하고 지분을 받습니다.
    - User A가 100 `yzUSD`를 예치하고 100 `st-yzUSD`를 받습니다.
    - User B가 100 `yzUSD`를 예치하고 100 `st-yzUSD`를 받습니다.
    - **상환 수수료는 10%입니다**.

1. User A가 100 `st-yzUSD`의 전체 상환을 시작합니다.
    - 수수료 적용: `(100) - (100*0.1/1.1) = 90.9090909 yzUSD`
    - `totalPendingOrderValue` += 90.9 `yzUSD`
    - `totalSupply` -= 100 `st-yzUSD` = 100 `st-yzUSD`
    - `totalAssets` = 200 `yzUSD` - 90.9 `yzUSD` = 109.1 `yzUSD`
    
2. User B가 100 `st-yzUSD`의 전체 상환을 시작합니다.
    - 이제 100 `st-yzUSD` 가치 `totalAssets() = 109.1 `yzUSD`
    - 수수료 적용: `(109.1) - (109.1*0.1/1.1) = 99.1090909 `yzUSD`
    - `totalPendingOrderValue` += 99.1090909 `yzUSD` = 90.9 + 99.1090909 `yzUSD` = 190 `yzUSD`
    - `totalSupply` -= 100 `st-yzUSD` = 0 `st-yzUSD`
    - `totalAssets` = 200 `yzUSD` - 190 `yzUSD` = 10 `yzUSD`

3. **결과**: 
    - 순 User A 지불 10% 수수료: `((100 - 90.9090909) / 100) * 100`
    - 순 User B 지불 0.8909091% 수수료: `((100 - 99.1090909) / 100) * 100`

**개념 증명**: 
- [test_audit_feeHasNotBeenCollected_StateProof](https://gist.github.com/merlinboii/b125979b081266de2491e883712713fc#file-yuzuilp-t-sol-L24)
- [test_audit_feeHasNotBeenCollected_MinimizeRedeemFee](https://gist.github.com/merlinboii/b125979b081266de2491e883712713fc#file-yuzuilp-t-sol-L71)

## 권장 사항

- 풀의 사용 가능한 자산에서 수수료를 적절히 징수하십시오. 인출 또는 상환 중에 수수료가 징수되면 수수료는 풀에 남지 않고 별도의 수수료 저장소 또는 재무부 주소로 전송되어야 합니다.

- 징수된 수수료를 별도로 추적하고 `totalAssets()` 및 모든 저장소의 계산에서 제외하도록 회계를 수정하십시오.

# [M-01] 슬리피지 보호 누락으로 예상치 못한 자산 금액 허용 (Missing slippage protection allows unexpected asset amounts)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`StakedYuzuUSD`, `YuzuUSD` 및 `YuzuILP`의 `ERC4626` 상호 작용 함수에는 슬리피지 보호 메커니즘이 없습니다. 사용자는 현재 미리보기를 기반으로 특정 금액의 자산을 받을 것으로 예상하고 `initiateRedeem()`과 같은 함수를 호출하지만, 트랜잭션 제출과 실행 사이의 상태 변경으로 인해 실제 수령 금액이 다를 수 있습니다.

- `StakedYuzuUSD` 및 `YuzuUSD` 계약에서 사용자는 상환 수수료 변경으로 인해 손해를 볼 수 있습니다.
- `YuzuILP` 계약에서 이 풀은 `YuzuUSD`에 대한 위험도 부담할 것으로 예상되므로 사용자는 수수료 변경과 풀 크기 변경 모두로 인해 손해를 볼 수 있습니다.

슬리피지 보호가 없다는 것은 사용자가 수령할 의사가 있는 최소 허용 자산 금액을 지정할 수 없음을 의미하며, 이는 잠재적으로 예상치 못한 결과로 이어질 수 있습니다.

## 권장 사항

사용자가 `StakedYuzuUSD`, `YuzuUSD` 및 `YuzuILP` 계약과 상호 작용할 때 슬리피지 허용 오차를 지정할 수 있도록 허용하십시오.

추가적인 접근 방식은 트랜잭션이 특정 시간 창 내에서만 실행되도록 하는 마감일(deadline) 매개변수를 구현하여 제출과 실행 사이에 매개변수가 크게 변경될 가능성을 줄이는 것입니다.

# [M-02] 수익 분배를 샌드위칭하여 사용자가 상환으로 이익을 얻을 수 있음 (Sandwiching yield distributions allows users to profit from redemption)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`StakedYuzuUSD`를 사용하면 사용자는 보상 분배 직후에 입금하고 가치 상환 요청을 시작할 수 있습니다. 보상이 `StakedYuzuUSD` 계약에 분배되면 `totalSupply()`는 일정하게 유지되는 반면 `totalAssets()`는 증가하여 모든 주주의 환율이 향상됩니다.

그러나 사용자는 입금 및 상환 요청으로 보상 분배를 샌드위칭하여 상환 요청을 시작할 수 있습니다. 그런 다음 그들은 지불하는 수수료보다 큰 수익의 혜택을 누릴 수 있으며, 잠재적으로 상환 수수료를 고려한 후에도 순 긍정적 결과를 초래할 수 있습니다.

**개념 증명:**: [test_fuzz_audit_sandwichYieldDistribution](https://gist.github.com/merlinboii/b125979b081266de2491e883712713fc#file-stakedyuzuusd-t-sol-L54)

## 권장 사항

사용자가 입금하는 시점과 상환 요청을 시작할 수 있는 시점 사이에 냉각 기간(cooldown period)을 구현하십시오.

# [L-01] 상환 주문 인센티브에 대한 하한 확인 누락 (Missing lower bound check for redeem order incentive)

_해결됨_

계약의 `setRedeemOrderFee` 함수는 수수료가 `1e6`보다 크지 않은지만 확인하고 하한을 강제하지 않습니다. 이를 통해 상환 주문 중 지불금을 안전한 한도를 넘어 증가시키는 과도하게 음수의 값을 설정할 수 있습니다. 이는 관리자 오구성에 따라 달라지지만 잘못된 인센티브나 의도하지 않은 자산 인플레이션을 초래할 수 있습니다.

```solidity
    function setRedeemOrderFee(int256 newFeePpm) external onlyRole(REDEEM_MANAGER_ROLE) {
        if (newFeePpm > 1e6) {
            revert FeeTooHigh(SafeCast.toUint256(newFeePpm), 1e6);
        }
        int256 oldFee = redeemOrderFeePpm;
        redeemOrderFeePpm = newFeePpm;
        emit UpdatedRedeemOrderFee(oldFee, newFeePpm);
    }
```

**참고:**
인센티브의 경우 `feeBPM`이 음수일 수 있습니다:
```solidity
    function _applyFeeOrIncentiveOnTotal(uint256 assets, int256 feePpm) internal pure returns (uint256) {
        if (feePpm >= 0) {
            /// @dev Positive fee - reduce assets returned
            uint256 fee = _feeOnTotal(assets, SafeCast.toUint256(feePpm));
            return assets - fee;
        } else {
            /// @dev Negative fee (incentive) - increase assets returned
            uint256 incentive = _feeOnRaw(assets, SafeCast.toUint256(-feePpm));
            return assets + incentive;
        }
    }
```

# [L-02] `YuzuUSD`와 `USDC` 간의 지속적인 1:1 페그는 대규모 프로토콜 손실 후 실패할 수 있음 (Constant 1:1 peg between `YuzuUSD` and `USDC` may fail after large protocol loss)

_인정됨_

프로토콜이 사용 가능한 보험 보상 범위를 초과하는 손실을 입는 경우 YuzuUSD와 USDC 간의 하드 코딩된 1:1 가격 가정은 문제가 됩니다. 이러한 시나리오에서 시스템은 YuzuUSD의 가치가 하락한 보증을 반영할 수 없어, 잠재적인 지급 불능 위험이나 불공정한 상환으로 이어질 수 있습니다. 프로토콜 손실을 고려하고 이에 따라 YuzuUSD 환율을 조정하는 동적 가격 책정 메커니즘을 도입하는 것이 좋습니다.

```solidity
    function _convertToShares(uint256 assets, Math.Rounding) internal view override returns (uint256) {
        return assets * 10 ** _decimalsOffset();
    }

    function _convertToAssets(uint256 shares, Math.Rounding rounding) internal view override returns (uint256) {
        if (rounding == Math.Rounding.Floor) {
            return shares / 10 ** _decimalsOffset();
        } else {
            return Math.ceilDiv(shares, 10 ** _decimalsOffset());
        }
    }
```

# [L-03] `YuzuProto.previewWithdraw()`의 잘못된 반올림 (Incorrect rounding in `YuzuProto.previewWithdraw()`)

_해결됨_

`YuzuProto.previewWithdraw()`는 `previewDeposit()`을 호출하여 특정 자산 금액을 인출하는 데 필요한 지분 수를 잘못 계산합니다. 자산을 지분으로 변환할 때 예금에는 적절하지만 인출에는 부적절한 `Math.Rounding.Floor`를 잘못 사용합니다. 인출 시에는 반올림(`Math.Rounding.Ceil`)을 해야 합니다.

```solidity
/// @notice See {IERC4626-previewWithdraw}
function previewWithdraw(uint256 assets) public view virtual override returns (uint256) {
    uint256 fee = _feeOnRaw(assets, redeemFeePpm);
    uint256 tokens = previewDeposit(assets + fee);
    return tokens;
}
```

그러나 `YuzuUSD` 및 `YuzuILP` 계약의 사용은 재정의된 `previewWithdraw()`에 올바르게 적용됩니다.

`previewDeposit()`을 호출하는 대신 `Math.Rounding.Ceil`을 사용하는 것을 고려하십시오.

```diff
-   uint256 tokens = previewDeposit(assets + fee);
+   uint256 tokens = _convertToShares(assets + fee, Math.Rounding.Ceil);
    return tokens;
```

또는

```diff
-   uint256 tokens = previewDeposit(assets + fee);
+   uint256 tokens = super.previewWithdraw(assets + fee);
```

# [L-04] `_applyFeeOrIncentiveOnTotal()`의 인센티브 계산 시 잘못된 반올림 (Incorrect rounding on incentive calculation in `_applyFeeOrIncentiveOnTotal()`)

_해결됨_

`_applyFeeOrIncentiveOnTotal()`은 자산에 수수료 또는 인센티브를 적용합니다. 음수 `feePpm`(인센티브)을 처리할 때 `Math.Rounding.Ceil`을 통해 반올림하는 `_feeOnRaw()`를 사용합니다.

이 반올림 방향은 인센티브가 수학적으로 정확한 것보다 지속적으로 약간 높게 계산되므로 프로토콜 비용으로 사용자에게 유리합니다.

```solidity
function _applyFeeOrIncentiveOnTotal(uint256 assets, int256 feePpm) internal pure returns (uint256) {
    if (feePpm >= 0) {
        --- SNIPPED ---
    } else {
        /// @dev Negative fee (incentive) - increase assets returned
@>      uint256 incentive = _feeOnRaw(assets, SafeCast.toUint256(-feePpm));
        return assets + incentive;
    }
}

function _feeOnRaw(uint256 assets, uint256 feePpm) internal pure returns (uint256) {
@>   return Math.mulDiv(assets, feePpm, 1e6, Math.Rounding.Ceil);
}
```

인센티브 계산에 `Math.Rounding.Floor`를 사용하는 것을 고려하십시오.

```diff
-       uint256 incentive = _feeOnRaw(assets, SafeCast.toUint256(-feePpm));
+       uint256 incentivePpm = SafeCast.toUint256(-feePpm);
+       uint256 incentive = Math.mulDiv(assets, incentivePpm, 1e6, Math.Rounding.Floor);
```

# [L-05] 자산이 6자리 이상의 소수를 갖는 경우 반환된 지분 토큰 소수가 잘못됨 (Returned shares token decimals wrong if asset has over 6 decimals)

_해결됨_

`YuzuProto` 계약 및 해당 자식(`YuzuUSD`, `YuzuILP`)은 OpenZeppelin의 `ERC20Upgradeable`을 상속합니다. 이 기본 계약은 `18`을 반환하는 기본 `decimals()` 함수를 제공합니다.

```solidity
// ERC20Upgradeable.sol
function decimals() public view virtual returns (uint8) {
    return 18;
}
```

```solidity
// YuzuProto.sol
function _decimalsOffset() internal view virtual returns (uint8) {
    return 12;
}
```

그러나 이러한 계약으로 표시되는 지분 토큰은 기본 자산에 오프셋을 더한 값을 기준으로 소수를 가져야 합니다.

예를 들어, `YuzuProto`는 기본 자산의 소수에 더해야 하는 `12`의 `_decimalsOffset()`을 정의합니다. 이는 기본 자산이 6자리 소수(`USDC` 또는 `USDT`와 같은)를 가질 때 올바르게 작동하여 지분 토큰에 대해 18자리 소수를 생성합니다. 그러나 기본 자산이 6자리 이상의 소수(18자리 소수를 갖는 `DAI`와 같은)를 갖는 경우 지분 토큰은 30자리 소수(`18+12`)를 반환해야 합니다.

지분 토큰의 정확한 표현을 보장하기 위해 기본 자산의 소수에 `_decimalsOffset()`을 더한 값을 적절히 반환하도록 `decimals()` 함수를 재정의해야 합니다.

# [L-06] 더스트 인출(Dust withdrawals)이 `liquidityBufferSize`를 줄여 관리자 담보를 차단함 (Dust withdrawals block admin collateral by reducing `liquidityBufferSize`)

_해결됨_

`YuzuProto.withdrawCollateral()`은 관리자가 인출하려는 자산 금액을 지정하여 프로토콜에서 초과 담보를 인출할 수 있도록 하며, 이 함수는 `liquidityBufferSize()`에 의존하여 최대 인출 가능 금액을 결정합니다.

`liquidityBufferSize()` 계산은 계약의 자산 잔액에서 `totalUnfinalizedOrderValue()`를 뺍니다. 이는 보류 중인 상환 주문에 의해 줄어들 수 있습니다.

```solidity
function withdrawCollateral(uint256 assets, address receiver) public virtual {
    uint256 liquidityBuffer = liquidityBufferSize();
    if (assets > liquidityBuffer) {
        revert ExceededLiquidityBuffer(assets, liquidityBuffer);
    }
    SafeERC20.safeTransfer(IERC20(asset()), receiver, assets);
    emit WithdrawnCollateral(receiver, assets);
}

function liquidityBufferSize() public view virtual override returns (uint256) {
    return super.liquidityBufferSize() - totalUnfinalizedOrderValue();
}
```

사용자가 즉시 인출을 수행하면 사용 가능한 유동성을 소비하여 사용 가능한 `liquidityBufferSize()`를 줄일 수 있습니다. 이로 인해 합법적인 관리자 담보 인출이 예기치 않게 실패할 수 있는 상황이 발생합니다.

이는 정상적인 사용자 작업이나 적은 수수료/수수료가 없는 작은 즉시 인출(더스트 금액)을 의도적으로 실행하여 발생할 수 있습니다.

**권장 사항**

남은 사용 가능한 유동성 버퍼를 인출하는 특수 플래그(예: `type(uint256).max` 전달)를 구현하여 관리자가 사용 가능한 최대 금액을 인출할 수 있도록 허용하는 것을 고려하십시오.

# [L-07] `maxWithdraw()` 반환 값이 수수료 제외로 인해 실제 인출 가능 금액을 초과함 (`maxWithdraw()` return values exceed actual withdrawable amounts due to fee exclusion)

_해결됨_

`YuzuIssuer.maxWithdraw()`는 인출 수수료를 고려하지 않고 사용자가 인출할 수 있는 최대 자산 금액을 반환합니다.

```solidity
function maxWithdraw(address _owner) public view virtual returns (uint256) {
    return Math.min(previewRedeem(_maxRedeem(_owner)), _maxWithdraw(_owner));
}

function _maxWithdraw(address) internal view virtual returns (uint256) {
    return liquidityBufferSize();
}

function liquidityBufferSize() public view virtual override returns (uint256) {
    return super.liquidityBufferSize() - totalUnfinalizedOrderValue();
}
```

사용자가 자산을 인출하려고 할 때:
1. 사용자는 저장소에 100 지분을 가지고 있습니다.
2. `previewRedeem(100)`은 ~90 자산을 반환합니다(10% 수수료 후).
3. `_maxWithdraw()`는 현재 최대 인출 가능 금액으로 50 자산을 반환합니다.
4. 사용자가 `withdraw(50, user, user)`를 호출합니다.
5. 인출 프로세스는 다음을 계산합니다:
   ```solidity
   fee = _feeOnRaw(50, redeemFeePpm)
   // fee = 50 * 0.1e6 / 1e6 = 5 assets
   
   tokens = previewWithdraw(50)
   // tokens = 55 shares (equivalent to 50 assets + 5 assets fee)
   ```
6. 저장소에 남겨야 하는 실제 자산은 55(50 + 5 수수료)이며, 이는 보고된 `_maxWithdraw()` 값인 50 자산을 초과하지만 트랜잭션은 여전히 허용됩니다.

**권장 사항**

수수료가 적용된 후 순 인출 가능 금액을 계산하여 수수료를 반영하도록 `maxWithdraw()`를 업데이트하는 것을 고려하십시오.

# [L-08] `setRedeemDelay()` 및 `setFillWindow()`에서 허용되는 과도하게 큰 최대 값 (Excessively large max values allowed in `setRedeemDelay()` and `setFillWindow()`)

_해결됨_

`StakedYuzuUSD`의 `setRedeemDelay()` 및 `YuzuProto` 계약의 `setFillWindow()`는 사용자가 상환을 확정하기 전에 기다려야 하는 대기 기간을 설정합니다. 문제는 이 값이 136년에 해당하는 `type(uint32).max`까지 설정될 수 있다는 것입니다. 실수로 매우 높은 값으로 설정하면 일부 상환은 인간의 수명 내에 호출되지 않을 수 있습니다.

권장 사항: 최대 지연을 인간의 수명 내에서 완료할 수 있는 합리적인 값으로 제한하십시오.

# [L-09] `PausableUpgradeable` 계약에 대한 초기화 호출 누락 (Missing initialization call for `PausableUpgradeable` contract)

_해결됨_

`YuzuProto` 및 `StakedYuzuUSD` 계약은 `PausableUpgradeable` 계약의 초기화 함수를 호출하지 않습니다. 결과적으로 `PausableUpgradeable`은 부적절하게 초기화된 상태로 유지되어 계약을 일시 중지하거나 일시 중지 해제하려고 할 때 예상치 못한 동작이 발생할 수 있습니다.

권장 사항: `initialize()` 함수 내에서 `__Pausable_init()`이 호출되도록 하여 `PausableUpgradeable` 계약을 올바르게 초기화하십시오.

# [L-10] 최소 상환 금액 부족으로 `ORDER_FILLER_ROLE`에 대한 스팸 발생 (Lack of minimum redemption causes spam for `ORDER_FILLER_ROLE`)

_해결됨_

현재 `YuzuOrderBook`의 `createRedeemOrder` 함수에는 `maxTokens` 이상이 상환되지 않도록 하는 확인이 있습니다. 그러나 인출에 대한 최소 금액에 대한 확인은 없습니다. 사용자는 1 wei만큼 적은 금액을 인출할 수 있습니다. 이는 스패머가 상환을 위해 매우 적은 토큰 금액으로 여러 `createRedeemOrder` 트랜잭션을 생성할 수 있게 합니다. 이 생성된 상환 주문은 `fillRedeemOrder` 함수를 호출하여 이 주문을 이행하는 `ORDER_FILLER_ROLE`에 의해 이행되어야 하기 때문에 문제입니다. 주문이 매우 작으므로 `ORDER_FILLER_ROLE`은 특히 이더리움과 같은 체인에서 이행되는 주문 금액보다 가스에 더 많은 비용을 지출할 수 있습니다. 따라서 이는 `ORDER_FILLER_ROLE`에 대한 가스 낭비를 초래합니다. 또한 이는 다른 상환 주문이 처리되는 데 불필요한 지연을 유발합니다.

**권장 사항**

상환에 대한 최소 금액을 추가하십시오. 이보다 낮은 금액은 `createRedeemOrder`를 통해 상환할 수 없어야 합니다.

# [L-11] 사용자는 작은 수익을 포기하여 ILP 손실을 피하기 위해 선행 실행할 수 있음 (Users can frontrun to escape ILP losses by forfeiting small yield)

_해결됨_

`YuzuILP`(보험 유동성 풀)는 수익을 창출하고 사용자의 손실을 충당하도록 설계되었습니다. 문서에 따르면 **상환** 시 토큰은 **마지막 풀 크기 업데이트 이후 발생한 수익을 제외한** 풀 지분을 기준으로 가격이 책정됩니다.

```solidity
    function _convertToSharesRedeemed(uint256 assets, Math.Rounding rounding) internal view returns (uint256) {
        // slither-disable-next-line incorrect-equality
        if (poolSize == 0) {
            return totalSupply();
        }
        return Math.mulDiv(totalSupply(), assets, poolSize, rounding);  //Note: poolSize is used instead of totalAssets() to simulate withdraw on last update
    }
```

이 설계는 공격자가 업데이트 후 입금하고 다음 업데이트 전에 인출하여 위험을 감수하지 않고 대부분의 수익을 캡처하는 "수익 스나이핑(yield sniping)"을 방지하는 것을 목표로 합니다. 이를 달성하기 위해 마지막 업데이트 이후 발생한 모든 수익은 상환 시 몰수됩니다.

그러나 이 메커니즘은 여전히 사용자가 큰 손실을 선행 실행할 수 있도록 허용합니다. ILP가 큰 손실을 커버해야 하는 사고가 발생하면 사용자는 즉시 포지션을 상환할 수 있습니다. 그렇게 함으로써 그들은 마지막 업데이트 이후 발생한 소액의 수익(+ 소액의 수수료)만 포기하고 보험 보장에서 오는 훨씬 더 큰 손실을 피할 수 있습니다:

이는 합리적인 사용자가 큰 손실이 적용되기 직전에 탈퇴할 수 있는 **비대칭 위험 노출**을 생성하여 프로토콜의 보험 설계를 훼손합니다.

**권장 사항**

- `YuzuILP`에서 1단계 상환/인출을 방지하십시오.
- 2단계 상환의 경우 사용자가 조기 종료하여 보류 중인 손실을 피할 수 없도록 손실을 반영하는 **두 번째 단계**의 주가를 사용하도록 하십시오.

# [L-12] `StakedYuzuUSD`에 인플레이션 공격 완화 부족 (Lack of inflation attack mitigation in `StakedYuzuUSD`)

_인정됨_

`StakedYuzuUSD` 계약은 공격자가 `yzUSD`를 계약에 직접 기부하여 주가를 조작할 수 있는 인플레이션 공격에 취약합니다.

`_assetDecimalOffset`이 `0`이고 `StakedYuzuUSD`의 자산 잔액이 주식 수를 계산하는 데 사용되므로 `StakedYuzuUSD` 계약에 직접 `yzUSD`(`YuzuUSD`에 예치하여 획득) 금액을 기부하여 주가를 부풀릴 수 있습니다.

이 기부는 새로운 주식을 발행하지 않고 저장소의 `totalAssets()`를 증가시켜 주당 가격을 급격히 부풀립니다.

**개념 증명**: [test_audit_inflationAttack](https://gist.github.com/merlinboii/b125979b081266de2491e883712713fc#file-stakedyuzuusd-t-sol-L28)

**권장 사항**

이 문제를 완화하는 데는 여러 가지 방법이 있습니다:
- `_decimalsOffset` 값을 0이 아닌 값(예: 6)으로 설정하십시오.
- 또는 소액의 초기 예금(죽은 주식, dead shares)으로 이를 완화하는 것을 고려하십시오.
- 기부가 주가에 영향을 미치지 않도록 예치된 자산에 대한 내부 회계를 구현하십시오.

# [L-13] StakedYuzuUSD가 ERC4626 표전을 위반함 (StakedYuzuUSD violates the ERC4626 standard)

_해결됨_

[EIP4626]([https://eips.ethereum.org/EIPS/eip-4626#:~:text=type%3A uint256-,maxWithdraw,-Maximum amount of](https://eips.ethereum.org/EIPS/eip-4626#:~:text=type%3A%20uint256-,maxWithdraw,-Maximum%20amount%20of))의 maxWithdraw 사양을 보십시오:

> 인출이 완전히 비활성화된 경우(일시적으로라도) 0을 반환해야 하는 등 전역 및 사용자별 제한을 모두 고려해야 합니다(MUST).
> 

`StakedYuzuUSD`에서 인출은 비활성화되어 있습니다:

```solidity
    function withdraw(uint256, address, address) public pure override returns (uint256) {
        revert WithdrawNotSupported();
    }
```

그러나 `maxWithdraw`는 여전히 `super.maxRedeem`을 기반으로 값을 반환합니다. 이는 단순히 사용자의 지분 잔액이며 이를 자산 가치로 변환합니다:

```
    function maxWithdraw(address _owner) public view override returns (uint256) {
        return previewRedeem(super.maxRedeem(_owner)); //@audit - supposed to return 0?
    }
```

이는 ERC4626 표준을 위반하는 것입니다. `redeem` 함수도 비활성화되었으므로 `maxRedeem` 함수에도 동일한 주장을 사용할 수 있습니다. `initiateRedeem`에서 `maxRedeem`이 사용되고 있지만 사양에 부합하려면 `maxRedeem` 대신 다른 함수를 사용하는 것이 좋습니다.

또한 프로토콜이 동일한 방식으로 `maxRedeem`을 사용하려는 경우 임시 비활성화 시 0을 반환하는 것에 대한 참고 사항도 있습니다.

> 상환이 완전히 비활성화된 경우(일시적으로라도) 0을 반환해야 하는 등 전역 및 사용자별 제한을 모두 고려해야 합니다(MUST).
> 

따라서 프로토콜이 계약을 일시 중지하기로 결정하면 `maxRedeem`도 0을 반환해야 합니다. `initiateRedeem`도 일시 중지되므로 `maxRedeem`이 0을 반환하는 것은 표준에 충실한 것입니다.

**권장 사항**

ERC4626 표준을 준수하기 위해 언급된 경우에 위의 함수에서 0을 반환하십시오.
