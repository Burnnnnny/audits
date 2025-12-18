# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**ulti-org/ulti-protocol-contract** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# Ulti 소개

ULTI는 사용자가 이더리움 메인넷의 ETH와 같은 블록체인의 기본 통화를 예치하고 그 대가로 ULTI 토큰을 받을 수 있는 DeFi 프로토콜입니다. Ulti는 초기 토큰 분배를 처리하면서 Uniswap에 유동성 풀을 생성하여 토큰을 출시합니다. 사용자는 입력 토큰을 예치하여 ULTI 토큰을 얻을 수 있으며, 보상 청구 및 추천 관리 시스템이 마련되어 있습니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 비교적 가능성이 높습니다.

- 낮음 (Low) - 너무 많거나 비현실적인 가정을 필요로 하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준별 요구 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (이미 배포되지 않은 경우 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [2024dbadc12f6c4efea1d5ec29e6d994c6bc6636](https://github.com/ulti-org/ulti-protocol-contract/tree/2024dbadc12f6c4efea1d5ec29e6d994c6bc6636)

_수정 검토 커밋 해시_ - [f603232cdb5f06bea18ccf2c69f4479d71449871](https://github.com/ulti-org/ulti-protocol-contract/tree/f603232cdb5f06bea18ccf2c69f4479d71449871)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `ULTI`

# 발견 사항

# [C-01] 슬리피지 보호 계산에서의 치명적인 정밀도 손실 (Critical precision loss in slippage protection calculation)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

ULTI 계약에는 토큰 스왑 중 슬리피지 보호 메커니즘에 정밀도 오류가 포함되어 있습니다(1e18 대신 18 사용). `_swapInputTokenForUlti` 함수에서 `expectedUltiAmountWithoutSlippage` 계산에 잘못된 배율 인자를 사용하여 심각한 정밀도 손실이 발생합니다.

```solidity
    function _swapInputTokenForUlti(uint256 inputAmountToSwap, uint256 minUltiAmount, uint256 twap, uint256 deadline)
        private
        returns (uint256 ultiAmount)
    {
        ...
        // 2. Calculate expected output without slippage
        uint256 expectedUltiAmountWithoutSlippage = inputAmountToSwap * 18 / twap; // @audit should be inputAmountToSwap * 1e18 / twap

        // 3. Choose the higher minimum amount between user-specified and internal slippage protection
        uint256 minUltiAmountInternal = (expectedUltiAmountWithoutSlippage * (10000 - MAX_SWAP_SLIPPAGE_BPS)) / 10000;
        uint256 effectiveMinUltiAmount = minUltiAmount > minUltiAmountInternal ? minUltiAmount : minUltiAmountInternal;
        ...

        return ultiAmount;
    }
```

이 오류는 다음을 초래합니다:

- 정밀도 손실: 배율 인자로 1e18 대신 18을 사용하면 `expectedUltiAmountWithoutSlippage`가 대부분의 실제 입력 금액에 대해 급격히 저평가되거나 0으로 내림됩니다.
- 슬리피지 보호 기능 고장: `expectedUltiAmountWithoutSlippage`가 0이면 `minUltiAmountInternal`도 0이 되어 사실상 계약의 내부 슬리피지 보호가 비활성화됩니다.

악의적인 행위자는 다음을 수행할 수 있습니다:

- 매우 낮은 minUltiAmount 설정 (예: 1)
- 시장 운영을 통해 ULTI/입력 토큰 가격 조작
- 극심한 슬리피지로 스왑 실행
- 프로토콜 준비금에서 가치 추출

## 권장 사항

`inputAmountToSwap`에 `18` 대신 `1e18`을 곱하여 `expectedUltiAmountWithoutSlippage` 계산을 수정하십시오.

# [C-02] 상위 기여자 목록의 최하위 기여자가 불공정하게 제거될 수 있음 (The Lowest contributor of the top contributor's list can be removed unfairly)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

새로운 입금이 이루어지면 `_updateTopContributors` 함수에서 상위 기여자 목록이 업데이트됩니다. 목록이 꽉 차면(33명), 최하위 기여자가 검색됩니다. 현재 기여자의 기여도가 최하위 기여자의 기여도보다 크면 최하위 기여자가 제거되고 현재 기여자가 목록에 추가됩니다.

```solidity
1394:             // Replace lowest contributor if new contribution is higher
1395:             if (updatedContribution > minContribution) {
1396:                 _topContributors.remove(minContributor);
1397:                 _topContributors.set(contributorAddress, updatedContribution);
1398:             }
```

그러나 현재 기여자가 이미 목록에 있는지 확인하지 않습니다. 따라서 현재 기여자가 이미 목록에 있는 경우 최하위 기여자가 불공정하게 제거되어 목록에 32명의 기여자만 남게 됩니다.

이는 다음과 같은 여러 가지 의미를 갖습니다:

- 최하위 기여자는 `pump` 함수에 대해 DoS 당할 수 있습니다.
- 최하위 기여자는 상위 배포자에 대한 보상 분배에서 제거될 수 있습니다.
- 목록에 있는 기여자가 최하위 기여자를 제거하고 다른 계정을 사용하여 최소 허용 금액을 입금함으로써 다른 위치를 차지하는 데 사용될 수 있으며, 더 높은 기여도를 가진 다른 기여자가 많더라도 가능합니다.

## 권장 사항

```diff
-           // Replace lowest contributor if new contribution is higher
+           // Add or update contributorAddress contributor if is higher than minContributor
            if (updatedContribution > minContribution) {
-               _topContributors.remove(minContributor);
-               _topContributors.set(contributorAddress, updatedContribution);
+               if (_topContributors.set(contributorAddress, updatedContribution)) {
+                   // Remove minContributor if contributorAddress is added
+                   _topContributors.remove(minContributor);
+               }
            }
```

# [H-01] 잘못된 활성 펌퍼 보너스 계산 (Wrong active pumper bonus calculation)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

백서에 따르면 활성 펌퍼(Active Pumpers)는 Guardian 보너스의 +3%를 받습니다.

그러나 현재 구현에서는 계산 시 잘못된 스케일링으로 인해 보너스가 0.03%로 상당히 낮게 책정됩니다:

- ACTIVE_PUMPERS_BONUS_PERCENTAGE = ULTI_NUMBER / 10 = 3.
- 따라서 보너스는 3 / 10000 = 0.03%입니다.

활성 펌퍼는 의도된 3% 대신 0.03%의 추가 보너스만 받게 되며, 이는 약속된 보너스의 99% 감소를 의미합니다. 이는 활성 펌퍼에게 상당한 가치 손실입니다.

```solidity
    function _allocateTopContributorsBonuses(uint32 cycle) private {
        ...
            // Apply active pumper bonus (+3.3%) if they qualify
            if (_isActivePumper(cycle, contributor) && bonus > 0) {
                bonus = bonus * (10000 + ACTIVE_PUMPERS_BONUS_PERCENTAGE) / 10000; // @audit using 10000 as denominator makes the 3 become 0.03%
            }

        ...
    }
```

## 권장 사항

보너스 계산을 수정하십시오:

```diff
            if (_isActivePumper(cycle, contributor) && bonus > 0) {
-               bonus = bonus * (10000 + ACTIVE_PUMPERS_BONUS_PERCENTAGE) / 10000;
+               bonus = bonus * (100 + ACTIVE_PUMPERS_BONUS_PERCENTAGE) / 100;
            }
```

# [H-02] 과도하게 낮은 슬리피지 허용 오차로 인한 `pump` 함수 DoS (DoS on the `pump` function due to excessively low slippage tolerance)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

펌프 과정에서 ULTI 계약이 보유한 입력 토큰의 일부가 ULTI 토큰으로 교환됩니다. 시스템은 TWAP를 기반으로 예상되는 ULTI 토큰의 양을 계산하고, `MAX_SWAP_SLIPPAGE_BPS`보다 큰 슬리피지가 감지되면 교환이 실패하도록 하는 슬리피지 보호를 적용합니다.

```solidity
157:     /// @notice Maximum allowed slippage for swaps in basis points (0.99%)
158:     uint256 public constant MAX_SWAP_SLIPPAGE_BPS = 3 * ULTI_NUMBER;
    (...)
1193:         // 3. Choose the higher minimum amount between user-specified and internal slippage protection
1194:         uint256 minUltiAmountInternal = (expectedUltiAmountWithoutSlippage * (10000 - MAX_SWAP_SLIPPAGE_BPS)) / 10000;
1195:         uint256 effectiveMinUltiAmount = minUltiAmount > minUltiAmountInternal ? minUltiAmount : minUltiAmountInternal;
1196:
```

`MAX_SWAP_SLIPPAGE_BPS`의 현재 값은 99 베이시스 포인트(0.99%)입니다. 풀 수수료가 1%라는 점을 감안하면, 최상의 시나리오(TWAP가 현물 가격과 동일하고 소량의 입력 토큰이 전송됨)에서도 교환은 실패할 것입니다.

결과적으로 펌프 과정을 실행할 수 없습니다. 이것이 ULTI 프로토콜 메커니즘의 핵심 부분이라는 점을 감안할 때 이 문제는 심각도가 높은 것으로 간주됩니다.

**참고:** `_swapInputTokenForUlti` 함수와 관련된 또 다른 문제가 있어 예상되는 ULTI 토큰 양이 잘못 계산됩니다(1e18 대신 18을 곱함). 이는 받아야 할 ULTI 토큰의 수를 크게 과소평가하여, 이 문제로 인해 `pump` 함수의 DoS가 발생하는 것을 방지하고 있습니다. 그러나 다른 문제가 수정되면 이 문제의 효과로 인해 `pump` 함수의 DoS가 발생할 것입니다. 이러한 이유로 이 문제의 가능성은 중간으로 간주됩니다.

## 권장 사항

다음을 커버할 수 있을 만큼 충분히 높은 최대 슬리피지 비율을 허용하십시오:

- 풀 수수료 (1%).
- 현물 가격과의 TWAP의 합리적인 편차.
- 스왑 규모로 인한 슬리피지 (ULTI 계약이 보유한 입력 토큰이 많을수록, 풀의 유동성이 낮을수록 높음).
- 풀의 잠재적 프로토콜 수수료 (Uniswap은 향후 수수료를 활성화할 수 있음).

# [H-03] 조기 참여 기간 동안 입금 시 DoS 발생 (DoS on deposits during the early bird period)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

입금 시 입금 가격은 TWAP를 사용하여 계산되지만, 출시 후 첫 24시간 동안은 고정 가격이 대신 사용됩니다. 이 고정 가격은 풀의 초기 가격입니다.

```solidity
981:         if (block.timestamp < launchTimestamp + EARLY_BIRD_PRICE_DURATION) {
982:             depositPrice = 1e18 / INITIAL_RATIO;
983:         }
```

사용자가 예치한 입력 토큰의 일부와 이에 상응하는 양의 ULTI 토큰이 풀에 유동성으로 추가됩니다. ULTI 토큰의 양은 언급된 입금 가격을 사용하여 계산됩니다.

```solidity
992:         inputTokenForLP = (inputTokenAmount * LP_CONTRIBUTION_PERCENTAGE) / 100;
993:         ultiForLP = inputTokenForLP * 1e18 / depositPrice;
```

두 토큰이 유동성으로 추가될 때, 계약은 제공된 금액이 원하는 금액과 비교하여 `MAX_ADD_LP_SLIPPAGE_BPS` 베이시스 포인트 이상 감소하지 않도록 보장합니다. 즉, 입금 가격과 현물 가격의 차이가 `MAX_ADD_LP_SLIPPAGE_BPS` 베이시스 포인트보다 크면 트랜잭션이 되돌려집니다. 입금 가격이 첫 24시간 동안 고정되어 있다는 점을 감안할 때, 현물 가격이 `MAX_ADD_LP_SLIPPAGE_BPS`보다 크게 변동하면 조기 참여 기간이 끝날 때까지 모든 입금이 되돌려집니다.

```solidity
703:         uint256 minUltiAmount = (ultiForLP * (10000 - MAX_ADD_LP_SLIPPAGE_BPS)) / 10000;
704:         uint256 minInputTokenAmount = (inputTokenForLP * (10000 - MAX_ADD_LP_SLIPPAGE_BPS)) / 10000;
```

이 문제는 몇 가지 중요한 의미를 갖습니다:

- 프로젝트에 대한 초기 채택의 관련성을 고려할 때, 처음 24시간 동안 계약의 주요 기능을 잠그면 사용자 참여가 크게 줄어들 수 있습니다.
- 악의적인 행위자가 입금한 후 풀을 스왑하여 다른 사용자가 입금하지 못하도록 차단할 수 있으며, 유일한 조기 참여 입금 가격 혜택을 누리고 조기 참여 기간이 끝나면 더 높은 가격에 해당 토큰을 판매할 수 있습니다.

## 개념 증명 (Proof of Concept)

```solidity
function test_DoSDepositsAfterLaunch() public {
    // Swap to move spot price
    uint256 wethIn = 0.06 ether;
    ISwapRouter.ExactInputSingleParams memory swapParams = ISwapRouter.ExactInputSingleParams({
        tokenIn: address(weth),
        tokenOut: address(ulti),
        fee: 10_000,
        recipient: address(this),
        deadline: block.timestamp,
        amountIn: wethIn,
        amountOutMinimum: 0,
        sqrtPriceLimitX96: 0
    });
    v3Router.exactInputSingle(swapParams);

    // Deposit on ULTI reverts due to price slippage
    vm.expectRevert("Price slippage check");
    ulti.depositNative{value: 0.00001 ether}(address(0), 0, block.timestamp, false);
}
```

## 권장 사항

조기 참여 기간 동안 현물 가격을 사용하여 풀에 유동성으로 추가할 ULTI 토큰의 양을 계산하십시오.

# [M-01] (범위 밖) TickMath 라이브러리에 unchecked 블록 누락 (Missing unchecked block in TickMath library)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

Uniswap 수학 라이브러리는 산술 연산을 수행하기 위해 래핑 동작(wrapping behavior)에 의존합니다. Solidity 버전 0.8.0은 오버플로우를 일으키는 연산이 되돌려지도록 하는 체크된 산술(checked arithmetic)을 기본값으로 도입했습니다.

`lib/uniswap/TickMath.sol` 라이브러리는 Uniswap에서 수정되었으며 Solidity 버전 0.8.24로 작성되었으므로, 이러한 산술 연산은 unchecked 블록으로 래핑되어야 합니다.

## 권장 사항

`getSqrtRatioAtTick` 및 `getTickAtSqrtRatio`에서 Uniswap 수학 라이브러리와의 호환성을 유지하기 위해 산술 연산을 unchecked 블록으로 래핑하십시오.

# [M-02] `_getSpotPrice()`에서 `mulDiv` 미사용으로 인한 잠재적 오버플로우 위험 (Potential overflow risk due to not using `mulDiv` in `_getSpotPrice()`)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

ULTI가 `token0`이고 가격이 크게 상승할 때:

- `priceX192`가 `2^256 / 1e18`을 초과할 수 있습니다.
- `priceX192 * 1e18` 곱셈은 오른쪽 시프트(right shift) 이전에 오버플로우됩니다.
- 이로 인해 거래가 되돌려집니다.

ULTI가 token 0인 경우 오버플로우로 인해 거래가 되돌려져 다음을 방지합니다:

- 현물 가격을 확인하는 입금
- 가격 계산에 의존하는 펌프
- `_getSpotPrice()`를 사용하는 기타 모든 함수

오류는 Uniswap의 `FullMath.mulDiv()`와 같은 안전한 산술 연산을 사용하지 않은 것입니다. `FullMath.mulDiv()`는 정밀도를 유지하면서 중간 오버플로우를 허용합니다.

```solidity
    function _getSpotPrice() internal view returns (uint256 spotPrice) {
        ...

        if (isUltiToken0) {
            // 3. Calculate price as input token amount needed per 1 ULTI
            spotPrice = priceX192 * 1e18 >> 192; // @audit potential overflow if priceX192 > 2^256 / 1e18
        } else {
            // 4. Calculate inverse price ratio for consistent pricing format
            spotPrice = (1 << 192) * 1e18 / priceX192;
        }
    }
```

## 권장 사항

Uniswap v3 주변 라이브러리에서 FullMath.mulDiv는 정밀도 손실 없이 중간 값의 오버플로우를 허용하기 위해 사용되며, 중간 값이 256비트를 초과하는("phantom overflow") 곱셈 및 나눗셈을 허용합니다.

오버플로우를 방지하고 정확한 가격 계산을 보장하기 위해 `FullMath.mulDiv`를 사용하여 현물 가격을 계산하는 것이 좋습니다:

```diff
        if (isUltiToken0) {
-           spotPrice = priceX192 * 1e18 >> 192;
+           spotPrice = FullMath.mulDiv(1e18, priceX192, 1 << 192);
        ...
```

# [M-03] 추천 보너스 한도가 의도한 것보다 낮음 (Referral bonus cap will be lower than what was intended)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

문서와 코드 내부의 주석에 따르면, 추천인이 누적할 수 있는 최대 보너스는 시작 이후 직접 입금에서 누적한 ULTI 총액의 10배입니다. 코드는 `claimableBonuses[]`에서 미청구 보너스를 추적하고 `claimableBonuses[]`가 최대 한도보다 높지 않은지 확인하여 이 최대 한도를 보장합니다:

```solidity
           // 3a. Cap referral bonus based on skin-in-game limit
            // Note: if the cap is reached, no referrer and referred bonuses will be acculmulated. This is intentional
            uint256 skinInAGameCap = _getSkinInTheGameCap(effectiveReferrer);
            uint256 remainingBonusAllowance = skinInAGameCap > claimableBonuses[effectiveReferrer]
                ? skinInAGameCap - claimableBonuses[effectiveReferrer]
                : 0;
            referrerBonus = referrerBonus > remainingBonusAllowance ? remainingBonusAllowance : referrerBonus;
```

문제는 `claimableBonuses[]`에 다른 보너스 유형도 포함된다는 것입니다. 따라서 사용자가 많은 양의 연속/추천된/상위 기여자 보너스를 받으면, `claimableBonuses[]`에 이러한 다른 보너스 유형이 포함되어 있기 때문에 정의된 한도까지 추천인 보너스를 받을 수 없습니다. 이는 상위 기여자와 높은 연속 기록을 가진 사용자가 더 많은 추천을 하는 것을 낙담시킬 것입니다.

## 권장 사항

다른 변수에서 추천 보너스를 추적하고 해당 변수를 기반으로 최대 한도를 보장하십시오.

# [M-04] `tickCumulativeDelta`가 음수일 때 TWAP의 잘못된 반올림 (Wrong rounding of TWAP when `tickCumulativeDelta` is negative)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

TWAP 계산의 경우 `tickCumulativeDelta`는 두 tickCumulative 간의 차이로 계산됩니다. 이 값을 첫 번째 틱 누적으로 나누어 시간 가중 평균 틱을 얻습니다.

Solidity 나눗셈은 결과를 자르기(truncate) 때문에, 이 자르기의 효과는 `tickCumulativeDelta`의 부호에 따라 다릅니다. `tickCumulativeDelta`가 양수이면 값을 자르면 틱 값이 감소합니다. 그러나 `tickCumulativeDelta`가 음수이면 값을 자르면 틱 값이 증가합니다.

```solidity
588:         try liquidityPool.observe(secondsAgos) returns (int56[] memory tickCumulatives, uint160[] memory) {
589:             // 3a. Calculate time-weighted average tick
590:             int56 tickCumulativeDelta = tickCumulatives[1] - tickCumulatives[0];
591:             int24 timeWeightedAverageTick = int24(tickCumulativeDelta / int32(secondsAgos[0]));
592:
593:             // 3b. Convert tick to price quote
594:             // Always pass ULTI as base token (amount of 1e18 = 1 ULTI) and input token as quote token
595:             // This ensures we get the price in INPUT_TOKEN/ULTI format consistently
596:             twap = OracleLibrary.getQuoteAtTick(
597:                 timeWeightedAverageTick,
598:                 1e18, // amountIn: 1 ULTI token (18 decimals)
599:                 address(this), // base token (ULTI)
600:                 inputTokenAddress // quote token (INPUT_TOKEN)
601:             );
```

현재 구현은 `tickCumulativeDelta`가 음수일 때 TWAP 값을 조정하지 않아 `tickCumulativeDelta`의 부호에 따라 계산된 TWAP 값에 불일치가 발생합니다.

## 권장 사항

```diff
        int56 tickCumulativeDelta = tickCumulatives[1] - tickCumulatives[0];
        int24 timeWeightedAverageTick = int24(tickCumulativeDelta / int32(secondsAgos[0]));
+       if (tickCumulativesDelta < 0 && (tickCumulativesDelta % int56(uint56(MIN_TWAP_TIME)) != 0)) {
+           timeWeightedAverageTick--;
+       }
```

# [M-05] 비원래(non-native) 입금에 대해 추천인의 기여가 올바르게 업데이트되지 않음 (Referrer's contributions are not updated correctly for non-native deposits)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

입금 시 `_updateReferrals` 함수는 추천 보너스를 처리합니다. 프로세스의 3단계에서 `_updateContributors` 함수가 호출되어 추천인의 기여를 업데이트합니다.

```solidity
1661:                 _updateContributors(cycle, effectiveReferrer, 0, msg.value, referrerBonus);
```

`_updateContributors` 함수의 네 번째 인수는 추천된 사용자가 입금한 토큰의 양인 `inputTokenReferred`입니다. 그러나 `msg.value`가 인수로 전달되며, `depositNative` 함수가 아닌 `deposit` 함수를 사용하여 입금한 경우 이 값은 0이 됩니다.

결과적으로 추천인에 대한 `totalInputTokenReferred` 매핑이 업데이트되지 않습니다. 이 값은 현재 계약에서 사용되지 않지만 뷰 함수를 통해 노출되므로 외부 구현을 통해 추가 보상을 분배하는 계산에 사용될 수 있습니다. 이와 관련하여 백서에서 향후 구현을 위한 "기여도(입금, 추천, 연속) 기반 평판 시스템"을 언급하고 있다는 점도 주목할 가치가 있습니다.

## 권장 사항

```diff
        // 7. Calculate referral bonus and allocate it based on the ULTI just allocated to the user including the streak bonus
        (address effectiveReferrer, uint256 referrerBonus, uint256 referredBonus) =
-           _updateReferrals(referrer, ultiForUserWithStreakBonus, cycle);
+           _updateReferrals(referrer, inputTokenAmount, ultiForUserWithStreakBonus, cycle);
    (...)
-   function _updateReferrals(address referrer, uint256 ultiToMint, uint32 cycle)
+   function _updateReferrals(address referrer, uint256 inputTokenReferred, uint256 ultiToMint, uint32 cycle)
        private
        returns (address effectiveReferrer, uint256 referrerBonus, uint256 referredBonus)
    (...)
                // 3c. Update referrer's allocated bonuses and contributions
                claimableBonuses[effectiveReferrer] += referrerBonus;
-               _updateContributors(cycle, effectiveReferrer, 0, msg.value, referrerBonus);
+               _updateContributors(cycle, effectiveReferrer, 0, inputTokenReferred, referrerBonus);
```

# [M-06] 입력 토큰이 WETH일 것이라는 잘못된 가정 (Wrong assumption that the input token will be WETH)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

ULTI는 모든 ERC20 토큰을 입력 토큰으로 허용하며 생성자에서 입력 토큰의 소수 자릿수가 `inputTokenDecimals` 변수에 저장됩니다.

```solidity
219:     /// @notice Address of the input token (e.g. WETH, DAI, etc.)
220:     address public immutable inputTokenAddress;
    (...)
451:         inputTokenDecimals = IERC20(_inputTokenAddress).decimals();
```

그러나 이 값은 사용되지 않으며 일부 상수 및 계산은 입력 토큰이 WETH일 것이라는 가정을 기반으로 하여 계산에 ETH의 예상 값을 사용합니다.

```solidity
130:     // Deposit and economic constants
131:     /// @notice Minimum amount required for a deposit to be valid (0.000001 ETH)
132:     /// @dev Economic considerations for minimum deposit:
133:     /// - At ETH=$1,000,000: minimum = $1 worth of ETH (0.000001 ETH)
134:     /// - Small enough to allow $1 deposits even if the price of ETH goes to $1M per ETH
135:     /// - Prevents streak bonus gaming while maintaining accessibility
136:     uint256 public constant MINIMUM_DEPOSIT_AMOUNT = 0.000001 ether;
137:
138:     /// @notice Initial ratio ULTI:ETH: 330000:1 Amount of ULTI for 1 ETH.
139:     uint256 public constant INITIAL_RATIO = ULTI_NUMBER * 1e4;
    (...)
490:         if (inputTokenAmount < ULTI_NUMBER * 1e18) revert LaunchMinimumFounderGiveawayRequired();
```

결과적으로 입력 토큰이 WETH가 아니고, 특히 18자리 소수점이 아닌 경우 계약이 예상대로 작동하지 않아 예상치 못한 동작이 발생할 수 있습니다.

예를 들어 USDC(6자리 소수점)를 입력 토큰으로 사용하면 소유자가 계약을 시작하기 위해 33조 USDC를 입금해야 하는데, 이는 실행 불가능합니다.

반면에 WPOL(Polygon의 네이티브 토큰 래퍼)을 입력 토큰으로 사용하면 POL 토큰의 가치가 ETH보다 훨씬 낮기 때문에 USD 기준으로 ULTI 토큰의 가치가 예상보다 훨씬 낮습니다.

## 권장 사항

`MINIMUM_DEPOSIT_AMOUNT`, `INITIAL_RATIO` 및 최소 입금 금액을 계약 생성 시 구성할 수 있도록 만들고, 입력 토큰과 관련된 계산에 하드코딩된 값 1e18 대신 `10 ** inputTokenDecimals`를 사용하십시오.

# [M-07] 과도하게 낮은 슬리피지 허용 오차로 인한 `deposit` 함수 DoS (DoS on the `deposit` function due to excessively low slippage tolerance)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

입금 시 입력 토큰의 일부와 새로 발행된 ULTI 토큰이 풀에 유동성으로 추가됩니다. 슬리피지 보호 메커니즘으로서, TWAP와 현물 가격의 차이가 `MAX_ADD_LP_SLIPPAGE_BPS` 베이시스 포인트보다 크면 유동성 추가가 되돌려집니다.

이 상수의 값은 33 베이시스 포인트로 설정되어 있습니다. 이는 현물 가격의 매우 작은 변동에도 트랜잭션이 되돌려져 사용자가 입력 토큰을 입금할 수 없음을 의미합니다.

## 개념 증명 (Proof of Concept)

```solidity
function test_depositSlippage() public {
    // Increase pool observation cardinality
    v3Pool.increaseObservationCardinalityNext(90);

    // Skip early bird period
    skip(1 days);

    // Simulate a swap every new block for 90 blocks
    for (uint256 i = 0; i < 90; i++) {
        ISwapRouter.ExactInputSingleParams memory swapParams = ISwapRouter.ExactInputSingleParams({
            tokenIn: address(weth),
            tokenOut: address(ulti),
            fee: 10_000,
            recipient: address(this),
            deadline: block.timestamp,
            amountIn: 1,
            amountOutMinimum: 0,
            sqrtPriceLimitX96: 0
        });
        v3Router.exactInputSingle(swapParams);
        skip(12 seconds);
        vm.roll(block.timestamp + 1);
    }

    // Create a swap that moves the price a small amount
    ISwapRouter.ExactInputSingleParams memory swapParams = ISwapRouter.ExactInputSingleParams({
        tokenIn: address(weth),
        tokenOut: address(ulti),
        fee: 10_000,
        recipient: address(this),
        deadline: block.timestamp,
        amountIn: 0.06 ether,
        amountOutMinimum: 0,
        sqrtPriceLimitX96: 0
    });
    v3Router.exactInputSingle(swapParams);
    skip(12 seconds);
    vm.roll(block.timestamp + 1);

    // Deposit on ULTI reverts due to price slippage
    vm.expectRevert("Price slippage check");
    ulti.depositNative{value: 0.00001 ether}(address(0), 0, block.timestamp, false);
}
```

## 권장 사항

`MAX_ADD_LP_SLIPPAGE_BPS` 값을 풀 가격의 변동성을 고려한 보다 합리적인 값으로 늘리십시오.

# [M-08] TWAP 조작 가능성 (TWAP can be manipulated)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

계약은 TWAP 오라클을 사용하여 입금 및 펌프 메커니즘을 통해 수행되는 스왑에 대한 가격을 계산합니다. 관측 창(observation window)은 18.15분으로 설정됩니다.

그러나 두 가지 문제로 인해 TWAP가 쉽게 조작될 수 있습니다:

1. Uniswap V3 풀은 관측 카디널리티 1로 초기화되므로 TWAP 오라클은 마지막 가격 업데이트만 고려합니다.
2. 관측 카디널리티가 관측 창에 충분히 높더라도 기록된 관측 수가 충분하지 않을 수 있습니다.

즉, 공격자가 ULTI 토큰의 가격을 조작할 수 있습니다. `deposit` 및 `pump` 함수 모두 슬리피지 보호 메커니즘이 있으므로 공격의 결과는 이러한 기능에 대한 DoS가 됩니다.

## 권장 사항

1. 풀 초기화 후 관측 창을 커버할 만큼 충분히 높은 값으로 `IUniswapV3Pool.increaseObservationCardinalityNext(uint16 observationCardinalityNext)` 함수를 호출하십시오. 이 값은 최소 `observationWindow / averageBlockProductionRageOfTheNetwork`여야 합니다.
2. 기록된 관측 수가 관측 창을 커버할 만큼 충분히 많은지 확인하십시오.

# [L-01] 조기 참여 입금 시 반올림 오류 (Rounding error in early bird deposit)

조기 참여 기간 동안 가격 계산 시 반올림 오류로 인해 사용자가 의도한 것보다 약간 더 많은 ULTI 토큰을 받을 수 있습니다.

이 문제는 조기 참여 기간 동안 ULTI 토큰 금액을 계산할 때 `_allocateDeposit` 함수에서 발생합니다. 이 함수는 반올림 오류를 유발할 수 있는 두 번의 별도 나눗셈을 수행합니다:

```solidity
    function _allocateDeposit(uint256 inputTokenAmount, uint256 minUltiToAllocate, uint256 deadline)
        private
        returns (uint256 ultiForUser, uint256 ultiForLP, uint256 inputTokenForLP)
    {
        ...
        if (block.timestamp < launchTimestamp + EARLY_BIRD_PRICE_DURATION) {
            depositPrice = 1e18 / INITIAL_RATIO; // @audit First division - slightly smaller deposit price due to integer division rounding down
        }

        ultiForUser = inputTokenAmount * 1e18 / depositPrice; // @audit Second division - resulting in slightly more ULTI tokens being minted
        ...
        // 5. Calculate INPUT and ULTI portions for liquidity pool
        inputTokenForLP = (inputTokenAmount * LP_CONTRIBUTION_PERCENTAGE) / 100;
        ultiForLP = inputTokenForLP * 1e18 / depositPrice;
        ...
    }
```

반올림 오류를 피하기 위해 `INITIAL_RATIO`를 직접 곱하는 것이 좋습니다.

```solidity
    ultiForUser = inputTokenAmount * INITIAL_RATIO
    ultiForLP = inputTokenForLP * INITIAL_RATIO
```

# [L-02] USDT와 같은 비표준 ERC20 토큰과의 계약 비호환성 (Contract incompatibility with non-standard ERC20 tokens like USDT)

ULTI 계약은 `IERC20.approve` 및 `IERC20.transferFrom`을 사용하여 입력 토큰과 상호 작용합니다. 그러나 USDT와 같이 IERC20 인터페이스를 따르지 않는 일부 토큰이 있습니다. 이로 인해 계약 배포, 새 풀 출시 또는 토큰 입금 시 트랜잭션이 되돌려질 수 있습니다.

입력 토큰과 상호 작용하려면 OpenZeppelin의 SafeERC20 라이브러리에서 forceApprove 및 safeTransferFrom과 같은 함수를 사용하는 것이 좋습니다.

# [L-03] `_calculateTWAP()` 내부에서 현물 가격 사용으로 인한 가격 조작 취약성 (Using spot price inside `_calculateTWAP()` makes code vulnerable to price manipulations)

코드는 `_calculateTWAP()`를 사용하여 슬리피지 확인을 수행하고 악의적인 사용자가 유동성 풀을 조작하고 ULTI 토큰과 상호 작용하여 가치를 추출하는 것을 방지합니다. 문제는 `_calculateTWAP()`가 TWAP 가격을 계산할 수 없을 때 현물 가격을 사용하는데, 이는 가격 조작 공격에 취약하게 만든다는 것입니다. 공격자는 풀의 가격을 조작한 다음 토큰을 입금하여 현물 가격이 잘못된 상태에서 ULTI가 유동성을 제공하게 하고, 공격자는 유동성 풀에서 가치를 추출할 수 있습니다.

# [L-04] 공격자가 길이 3 이상의 순환 추천을 생성할 수 있음 (Attacker can create circular referrals with lengths of three and higher)

코드는 길이 1과 2의 순환 추천을 방지하지만, 더 긴 길이의 순환 추천을 생성하고 시스템을 악용하는 것은 여전히 가능합니다.

```solidity
        if (referrer == msg.sender) revert DepositCannotReferSelf();
        if (referrers[referrer] == msg.sender) revert DepositCircularReferral();
```

# [L-05] 펌프/스왑에 대한 사용자 지정 슬리피지 확인이 비효율적임 (User specified slippage check for pump/swap is inefficient)

상위 기여자가 ULTI 토큰을 펌핑하고자 할 때 입력 토큰을 ULTI 토큰으로 교환할 때 슬리피지를 방지하기 위해 `minUltiAmount`를 지정합니다. 문제는 사용자의 펌프 트랜잭션이 멤풀(mempool) 내에 있는 동안 계약 입력 토큰 금액이 증가할 수 있고, 코드가 증가된 입력 토큰 금액을 입력으로 사용하기 때문에 스왑에 나쁜 슬리피지가 있더라도 스왑에서 받은 ULTI 토큰이 `minUltiAmount`보다 높을 수 있다는 것입니다. 슬리피지를 제한하려면 사용자가 가격을 지정하고 코드가 사용자 지정 가격과 현재 계약의 입력 토큰 잔액을 기반으로 `minUltiAmount`를 계산하도록 하는 것이 좋습니다.

# [L-06] `getGlobalData`에서의 잘못된 계산 (Incorrect calculation in `getGlobalData`)

`getGlobalData` 함수는 현재 유동성 풀에 있는 입력 토큰 및 ULTI 토큰의 양을 포함하여 계약의 데이터를 노출합니다.

```solidity
1972:      * @param inputTokenInLP Amount of input token currently in Uniswap V3 liquidity pool
1973:      * @param ultiInLP Amount of ULTI currently in Uniswap V3 liquidity pool
```

이 값의 계산은 `_getLiquidityAmounts` 함수에서 수행됩니다:

```solidity
786:     function _getLiquidityAmounts() internal view returns (uint256 inputTokenAmount, uint256 ultiAmount) {
787:         uint128 liquidity = liquidityPool.liquidity();
788:         (uint160 sqrtPriceX96,,,,,,) = liquidityPool.slot0();
789:
790:         // Get sqrt price at the boundaries of our position
791:         uint160 sqrtRatioAX96 = TickMath.getSqrtRatioAtTick(LP_MIN_TICK);
792:         uint160 sqrtRatioBX96 = TickMath.getSqrtRatioAtTick(LP_MAX_TICK);
793:
794:         (uint256 amount0, uint256 amount1) =
795:             LiquidityAmounts.getAmountsForLiquidity(sqrtPriceX96, sqrtRatioAX96, sqrtRatioBX96, liquidity);
```

그러나 787행에서 얻은 값은 현재 풀에서 사용할 수 있는 범위 내(in-range) 유동성을 나타내므로, 현재 범위 내에 있지 않은 모든 포지션의 유동성은 계산에 포함되지 않습니다. 결과적으로 `getGlobalData` 함수가 반환하는 값은 정확하지 않습니다.

현재 범위 내 유동성을 나타내는 토큰 양을 반환하려는 의도라면, 그에 따라 주석과 NatSpec을 업데이트하십시오. 그렇지 않은 경우 `IERC20.balanceOf()` 함수를 사용하여 풀에 있는 토큰의 양을 계산하십시오.

# [L-07] fee-on-transfer 입력 토큰 미지원 (No support for fee-on-transfer input token)

입력 토큰 입금은 계약이 받은 토큰 양이 사용자가 보낸 토큰 양과 동일하다고 가정합니다. 입력 토큰에 fee-on-transfer 메커니즘이 있는 경우 그렇지 않습니다. 이것이 원인인 경우, 계약에 수수료를 충당할 잔액이 충분하지 않으면 입금이 실패하거나, 잔액이 충분하면 준비금에서 토큰을 소비하게 됩니다.

fee-on-transfer 토큰이 입력 토큰으로 사용되는 경우 `launch` 함수가 되돌려질 것으로 예상되지만, 토큰을 계약에 기부(donation)하는 경우 되돌려지지 않을 수도 있습니다.

프로토콜이 fee-on-transfer 토큰을 지원하려면 입금 전후의 계약 잔액을 확인하고 그 차이를 입금 금액으로 사용해야 합니다.

fee-on-transfer 토큰을 지원할 의도가 없는 경우, `launch` 함수에서 계약이 받은 토큰 양이 사용자가 보낸 토큰 양과 동일한지 확인하는 것이 좋습니다.

# [L-08] `getUserData`가 주기 1에 대해 잘못된 연속 입력 상한을 반환함 (`getUserData` returns the wrong streak input upper boundary for cycle 1)

`getUserData`가 반환하는 `streakInputTokenAmountBoundaries` 값은 다음 주기에서 연속 기록을 유지하기 위해 필요한 (최소, 최대) 입력 토큰 금액의 튜플입니다.

이 함수가 주기 1에 대해 호출되면 경계의 상한값은 0이 되는데, 코드가 첫 번째 주기에 대해 어떤 경계도 적용하지 않으므로 이는 올바르지 않습니다.

또한 주기 번호가 0보다 큰지 확인하는 것이 좋습니다.

다음 변경 사항을 적용하는 것을 고려해 보십시오:

```diff
    function getUserData(uint32 cycle, address user) external view returns (UserData memory) {
-       uint256 inputTokenDepositedPreviousCycle = totalInputTokenDeposited[cycle - 1][user];
-       uint256[2] memory streakInputTokenAmountBoundaries =
-            [inputTokenDepositedPreviousCycle, inputTokenDepositedPreviousCycle * 10];
+       require(cycle > 0, "Cycle number must be greater than 0");
+
+       uint256[2] memory streakInputTokenAmountBoundaries;
+
+       if (cycle == 1) {
+           streakInputTokenAmountBoundaries = [0, type(uint256).max];
+       } else {
+           uint256 inputTokenDepositedPreviousCycle = totalInputTokenDeposited[cycle - 1][user];
+           streakInputTokenAmountBoundaries = [inputTokenDepositedPreviousCycle, inputTokenDepositedPreviousCycle * 10];
+       }
```

# [L-09] `launch` 함수의 DoS 벡터 (DoS vector in the `launch` function)

계약의 임시 소유자는 `launch` 함수를 호출하여 ULTI 토큰을 출시합니다. 개인 함수 `_createLiquidity`에서는 ULTI 토큰에 대한 Uniswap 풀이 존재하지 않는지 확인하고, 존재하면 함수가 되돌려집니다.

```solidity
624:     function _createLiquidity(uint256 inputTokenForLP, uint256 deadline) private {
625:         // 1. Check if the Uniswap pool already exists
626:         // This check prevents a potential DoS attack on the `launch` function
627:         // If an attacker creates the pool before the contract owner,
628:         // it would revert here instead of allowing the attacker to manipulate the initial state
629:         address liquidityPoolAddress = uniswapFactory.getPool(address(this), inputTokenAddress, LP_FEE);
630:
631:         // 2. Create and store pool if it doesn't exist
632:         if (liquidityPoolAddress == address(0)) {
633:             liquidityPoolAddress = uniswapFactory.createPool(address(this), inputTokenAddress, LP_FEE);
634:             liquidityPool = IUniswapV3Pool(liquidityPoolAddress);
635:         } else {
636:   @>        revert LiquidityPoolAlreadyExists();
637:         }
```

주석에는 DoS 공격이 방지된다고 명시되어 있지만 실제로는 그렇지 않습니다. 풀이 이미 존재하는 경우 트랜잭션을 되돌리면 풀의 초기 가격 조작은 방지되지만, 그렇게 함으로써 DoS 벡터가 생성됩니다. 공격자가 계약 소유자보다 먼저 풀을 생성하면 `launch` 함수가 되돌려져 계약을 재배포해야 합니다.

`launch` 함수의 로직을 계약의 생성자(constructor)에서 실행하는 것을 고려해 보십시오.

# [L-10] `receive` 및 `fallback`으로 받은 네이티브 토큰이 잠길 수 있음 (Native tokens received by the `receive` and `fallback` can be locked)

ULTI 계약은 네이티브 토큰을 받기 위해 `receive` 및 `fallback` 함수를 구현하지만, 이를 처리하는 로직은 구현하지 않아 토큰이 계약에 무기한 잠기게 됩니다.

계약이 장기 준비금에 대한 기부(donation)를 수락하도록 의도된 경우 다음 변경 사항을 구현하는 것을 고려해 보십시오:

```solidity
    // Receive function
    receive() external payable {
        require(inputTokenAddress == wrappedNativeTokenAddress);
        IWrappedNative(wrappedNativeTokenAddress).deposit{value: msg.value}();
    }

    // Fallback function
    fallback() external payable {
        require(inputTokenAddress == wrappedNativeTokenAddress);
        IWrappedNative(wrappedNativeTokenAddress).deposit{value: msg.value}();
    }
```

그렇지 않은 경우 `receive` 및 `fallback` 함수를 제거하는 것을 고려해 보십시오.

# [L-11] `_getSpotPrice()`에서 가격 계산 중 잠재적 오버플로우 (Potential overflow in `_getSpotPrice()` during price calculation)

`_getSpotPrice()` 함수는 오버플로우를 확인하지 않고 `sqrtPriceX96`을 자체와 직접 곱하여 `priceX192`를 계산합니다. `sqrtPriceX96`은 `uint160`이므로 값이 `2^128`을 초과하면 곱셈이 오버플로우될 수 있습니다.

오버플로우는 트랜잭션을 되돌리게 하여 다음을 방지합니다:

- 현물 가격을 확인하는 입금
- 가격 계산에 의존하는 펌프
- `_getSpotPrice()`를 사용하는 기타 모든 함수

```solidity
    function _getSpotPrice() internal view returns (uint256 spotPrice) {
        // 1. Get square root price from pool slot0
        (uint160 sqrtPriceX96,,,,,,) = liquidityPool.slot0();

        // 2. Square the price to get priceX192
        uint256 priceX192 = uint256(sqrtPriceX96) * uint256(sqrtPriceX96); // @audit overflow if sqrtPriceX96 > 2^128

        ...
    }
```

`sqrtpriceX96`이 `2^128`을 초과하는 시나리오를 해결하십시오. Uniswap v3의 Oracle 라이브러리에 있는 `getQuoteAtTick` 함수를 참조하십시오.

```solidity
        // Calculate quoteAmount with better precision if it doesn't overflow when multiplied by itself
        if (sqrtRatioX96 <= type(uint128).max) {
            // Calculate quoteAmount with higher precision
            ...
        } else {
            // Calculate quoteAmount with lower precision
            ...
        }
```

# [L-12] 여러 트랜잭션으로 입금 시 연속 보너스의 일부 손실 (Losing some part of the streak bonus if depositing in multiple transactions)

사용자가 연속적인 주기에 입금하고 입금 한도 확인을 따르면, 코드는 연속 횟수(streak counts)를 기반으로 연속 보너스 보상을 제공합니다. 확인 사항 중 하나는 현재 주기의 입금액이 이전 주기의 입금액보다 높아야 한다는 것이며, 그렇지 않은 경우 코드는 사용자에게 연속 보너스를 제공하지 않습니다.

```solidity
    // 1. Cumulative input token deposits this cycle is too low compared to previous cycle
        bool lowCumulativeDepositTooLow = inputTokenDepositedForCycleN < inputTokenDepositedForCycleNMinusOne;

        // 2. Cumulative input token deposits this cycle is too high compared to previous cycle
        bool cumulativeDepositTooHigh = inputTokenDepositedForCycleN > 10 * inputTokenDepositedForCycleNMinusOne;

        // 4. Update streak count based on deposit validity
        if (!lowCumulativeDepositTooLow && !cumulativeDepositTooHigh) {
            streakCounts[cycle][user] = streakCounts[cycle - 1][user] + 1;
        } else {
            // Break the streak
            streakCounts[cycle][user] = 1;
        }
```

문제는 사용자가 한 주기 내에 여러 트랜잭션으로 토큰을 입금하는 경우, 해당 주기의 초기 입금에 대해서는 연속 보너스를 받지 못하고 현재 주기의 입금 총액이 이전 주기보다 커질 때만 연속 보너스를 받게 된다는 것입니다. 이는 사용자가 여러 트랜잭션으로 입금할 경우 전체 연속 보너스를 받지 못하게 하며, 사용자는 자신의 입금액이 이전 주기 입금액보다 클 때만 입금해야 합니다. 이는 사용자가 늦게 입금하게 만들고 할인된 기여도가 낮아지게 됩니다.

사용자가 입금하는 횟수에 의존하지 않도록 보너스 계산을 변경하십시오. 한 가지 해결책은 `(totalInputTokenDeposited[cycle][user] < totalInputTokenDeposited[cycle - 1][user]) and (totalInputTokenDeposited[cycle][user] + inputTokenDeposited >  totalInputTokenDeposited[cycle - 1][user])`인 경우 현재 주기 입금의 합계가 유효한 구간에 진입하는 것이므로 코드가 모든 `totalInputTokenDeposited[cycle][user] + inputTokenDeposited`에 대해 연속 보너스를 계산하도록 확인을 추가하는 것입니다.


