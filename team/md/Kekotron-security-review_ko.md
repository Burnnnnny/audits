# 소개 (About)

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **KekotronRouter** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Kekotron Router 정보 (About Kekotron Router)

이 라우터는 Uniswap v2 및 v3 스타일 스왑을 처리하도록 설계된 범용 스왑 라우터입니다. 지원되는 모든 프로토콜에 대한 안전성과 일관성을 보장하기 위해 동적 처리가 있습니다.

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

**_검토 커밋 해시_ - [1787678a9dc5bb8fb1879f695f1c09455c5ff34e](https://github.com/astralparrot/KekotronRouter/tree/1787678a9dc5bb8fb1879f695f1c09455c5ff34e)**

**_수정 검토 커밋 해시_ - [f8c49dbbb62d0adc69c74d9f2cbcdb7dbb3019d8](https://github.com/astralparrot/KekotronRouter/tree/f8c49dbbb62d0adc69c74d9f2cbcdb7dbb3019d8)**

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `KekotronRouterV2`
- `KekotronRouterInitializer`
- `KekotronSwapV2`
- `KekotronSwapTraderJoeV2_1`
- `Constants`
- `DataTypes`
- `KekotronErrors`
- `KekotronSwapV3`
- `KekotronLib`
- `interfaces/**`

# 발견 사항 (Findings)

# [H-01] 사용자가 feeOn > 1로 설정하여 수수료를 피할 수 있음 (Users can set feeOn > 1 to avoid fees)

## 심각도

**영향:** 중간 (Medium), 프로토콜 수령 수수료 감소

**가능성:** 높음 (High), 모든 스왑에서 간단한 입력 변경

## 설명

`feeOn`은 모든 스왑에서 사용자가 표시하며 `feeIn`과 `feeOut`을 선택하는 역할을 합니다.
`feeOn` == 0은 `feeIn` 참, `feeOut` 거짓을 의미합니다.
`feeOn` == 1은 `feeIn` 거짓, `feeOut` 참을 의미합니다.

```
(bool feeIn, bool feeOut) = fee > 0 ? (feeOn == 0, feeOn == 1) : (false, false);
```

그러나 `feeOn`이 1보다 크면 `fee`가 설정되어 있어도 `feeIn`과 `feeOut` 모두 거짓이 됩니다.
결과적으로 수수료가 발생하지 않습니다.

## 권장 사항

`feeOn`이 0 또는 1인지 확인하거나, feeOn 입력에 대해 부울을 사용하는 것을 고려하십시오(테스트에서 항상 부울과 uint8 간에 변환된다는 점을 고려).

# [M-01] FeeIn을 선택하는 것이 항상 FeeOut보다 유리함 (Choosing FeeIn is always more beneficial than FeeOut)

## 심각도

**영향:** 낮음 (Low), 대다수 스왑에서 차이가 매우 작음

**가능성:** 높음 (High), 모든 스왑에서 FeeIn을 선택할 수 있음

## 설명

사용자는 `FeeIn`(tokenIn-amountIn에 대한 수수료)과 `FeeOut`(tokenOut-amountOut에 대한 수수료) 중에서 선택할 수 있습니다. 두 대안을 모두 계산하면 `FeeIn`을 선택하면 항상 수수료가 줄어듭니다(사용자가 더 많은 순 amountOut을 받음).

기술적으로 이는 FeeIn이 스왑할 자금이 적고 결국 슬리피지로 인한 손실이 적음을 의미하기 때문에 발생합니다.
두 옵션 간의 차이는 스왑 규모가 커질수록(더 많은 슬리피지) 더 커집니다.

옵션 간의 차이는 준비금의 100% 스왑의 경우 0.5%, 준비금의 10%의 경우 0.09%입니다.

그러나 `FeeIn`은 슬리피지 및 DEX 수수료 전에 수수료를 가져가므로 FeeReceiver에게 더 유리합니다.

결과적으로 `FeeIn`은 프로토콜과 사용자 모두에게 항상 재정적으로 더 좋습니다.

## 권장 사항

다음 옵션 중 일부를 고려하십시오:

1. 기본 설정으로 FeeIn만 남기거나 FeeOut만 남기기
2. 두 옵션이 동일하도록 공식 조정
3. 있는 그대로 두기 (그리고 차이가 무시할 만하다는 것을 인정)

# [M-02] ETH 제공 == amountIn 스왑 확인되지 않음 (Not checked ETH provided == amountIn swapped)

## 심각도

**영향:** 높음 (High), 자금 손실

**가능성:** 낮음 (Low), 입력 데이터 실수 및 tokenIn으로 네이티브 토큰 선택 필요

## 설명

RouterV2는 `tokenIn == address(0)`인 경우 `msg.value`가 `amountIn`과 같은지 확인하지 않습니다.
사용자가 실제로 덜 보내면 트랜잭션이 되돌려집니다.
사용자가 실제로 더 많이 보내면 스왑 후 일부 네이티브 토큰이 라우터에 갇히게 됩니다(델타는 스왑되지 않음).

## 권장 사항

네이티브 토큰이 `tokenIn`인 경우 `msg.value == amountIn`인지 확인하는 것을 고려하십시오.

# [M-03] 스왑 함수에 기한 확인 누락 (Missing deadline check in swap functions)

## 심각도

영향: 낮음 (Low), 사용자는 손실을 입지 않으며 긍정적인 슬리피지만 놓칠 수 있습니다.

가능성: 높음 (High), deadline 매개변수가 누락되었습니다.

## 설명

스왑 함수에는 deadline 매개변수가 없습니다. 이 매개변수는 사용자에게 보류 중인 트랜잭션의 실행을 제한할 수 있는 옵션을 제공할 수 있습니다.
deadline 매개변수가 없으면 사용자는 시장 상황이 불리할 때 예상치 못한 시간에 트랜잭션을 실행할 수 있습니다.

그러나 이 경우 함수에 슬리피지 보호 기능이 있으므로 큰 문제는 아닙니다. 사용자는 최소한 설정한 만큼은 받지만 트랜잭션이 블록에 포함될 때 환율이 유리해지면 긍정적인 슬리피지를 놓칠 수 있습니다.

## 권장 사항

모든 스왑 함수에 `deadline` 매개변수를 도입하십시오.

# [L-01] allowedV2Callbacks - 편집할 방법 없음 (allowedV2Callbacks - no way to edit)

RouterV2는 선택한 콜백에 대해 매핑을 가지고 있습니다:

```
/// @dev Fallback selectors for callbacks, allowing for adding additional protocols in the future
    mapping(bytes4 => bool) public allowedV2Callbacks;
    mapping(bytes4 => bool) public allowedV3Callbacks;
```

그러나 V3만 편집할 수 있고 V2에 대한 기능은 없습니다.

```
    function updatedAllowedV3Callbacks(bytes4 selector, bool allowed) external onlyOwner {
        allowedV3Callbacks[selector] = allowed;

        emit AllowedV3CallbackUpdated(selector, allowed);
    }
```

`allowedV2Callbacks`에서도 변경할 수 있는 기능을 고려하십시오.

# [L-02] 하드코딩된 가스 제한으로 인해 `safeTransferETH()`가 되돌려질 수 있음 (`safeTransferETH()` may revert due to a hardcoded gas limit)

`KekotronLib.sol`에는 ETH 전송에 사용되는 `safeTransferETH()`가 있습니다:

```solidity
    function safeTransferETH(address to, uint256 value) internal {
        (bool success,) = to.call{value: value, gas: PUSH_PAYMENT_GAS_LIMIT}(new bytes(0));
        if (!success) {
            revert EthTransfer();
        }
    }
```

이 함수는 ETH를 보낼 때 프로토콜의 여러 곳에서 사용됩니다. 문제는 이 함수가 고정된 가스 값을 사용한다는 것입니다:

```solidity
uint256 constant PUSH_PAYMENT_GAS_LIMIT = 10_000;
```

가스 제한을 고정하는 것은 좋지 않습니다. 가스가 부족하면 함수가 되돌려집니다. solidity 문서에서도 다음과 같이 읽을 수 있습니다:

> 상태를 읽든 쓰든 스마트 계약 코드에서 하드코딩된 가스 값에 의존하는 것을 피하는 것이 가장 좋습니다. 이는 많은 함정을 가질 수 있기 때문입니다. 또한 가스에 대한 액세스는 미래에 변경될 수 있습니다.

가스 필드를 제거하거나 가스 제한을 늘리는 것을 고려하십시오.

# [L-03] 비 ETH 네이티브 통화가 있는 체인에 대한 혼란스러운 명명 (Confusing naming for chains with non-ETH native currency)

코드에서 네이티브 통화의 래핑된 ERC20 주소를 얻기 위해 `_weth()` 함수를 사용합니다. 또한 `_swapExactEthForTokensV_X()` 및 `_swapExactTokensForEthV_X()`와 같은 함수도 있습니다.

문제는 프로토콜이 BSC, Polygon, Avalanche를 포함한 다양한 체인에 배포될 예정이라는 것입니다. 이들은 비 ETH 네이티브 통화를 가지고 있으며 네이티브 통화에서/로 스왑하는 함수는 래핑된 네이티브 토큰 대신 `weth` 변수에 WETH 주소를 순진하게 설정하면 작동하지 않습니다.

권장 사항: weth 및 eth의 표현을 네이티브 통화로 바꾸십시오.

# [L-04] 최소 `sqrtPriceLimitX96`이 실제 최소 가격 제한과 약간 다름 (Minimal `sqrtPriceLimitX96` slightly differs from actual minimal price limit)

현재 값 4295128749가 사용됩니다:

```solidity
uint160 sqrtPriceLimitX96 = (zeroForOne ? 4295128749 : 1461446703485210103287273052203988822378723970341);
```

그러나 [UniswapV3에 따르면](https://github.com/Uniswap/v3-core/blob/d8b1c635c275d2a9450bd6a78f3fa2484fef73eb/contracts/libraries/TickMath.sol#L14) 가장 작은 값은 `4295128739 + 1`입니다:

```solidity
    uint160 internal constant MIN_SQRT_RATIO = 4295128739;
```

결과적으로 극히 낮은 가격에서는 거래가 발생할 수 없습니다.

# [L-05] `tokenIn == tokenOut`일 때 스왑이 예기치 않게 실행됨 (Swap unexpectedly executes when `tokenIn == tokenOut`)

현재 사용자가 `tokenIn == tokenOut`을 제출하면 calldata가 유효하지 않아 거래가 발생해서는 안 됨에도 불구하고 라우터는 tokenIn에 대해 tokenOut을 거래합니다.

추가 확인을 추가하십시오:

```diff
    function _swapV2(Swap memory param, address to, uint16 poolFee) private returns (uint256) {
+       require(param.tokenIn != param.tokenOut);
        bool zeroForOne = param.tokenIn < param.tokenOut;

        ...
    }

    function _deriveData(Swap memory param, address payer, uint16 poolFee, uint8 protocol)
        private
        pure
        returns (bool, int256, uint160, bytes memory)
    {
+       require(param.tokenIn != param.tokenOut);
        bool zeroForOne = param.tokenIn < param.tokenOut;

        ...
    }

    function _swapV2_1(Swap memory param, address to) private returns (uint256) {
+       require(param.tokenIn != param.tokenOut);
        bool zeroForOne = param.tokenIn < param.tokenOut;

        ...
    }
```

# [L-06] KekotronSwapV3.sol은 수수료 > 6.5535%인 풀을 지원하지 않음 (KekotronSwapV3.sol doesn't support pools with fee > 6.5535%)

Kekotron은 poolFee를 uint16으로 저장하며 최대 값은 65535로 수수료는 6.5535%입니다. 더 높은 수수료를 가진 풀을 처리하기 위해 `poolFee`를 10만큼 이동하여 uint24로 만듭니다. 그러나 계산은 uint16 유형으로 수행되므로 결과가 65335보다 높은 경우 되돌려집니다.

```solidity
    function _deriveData(Swap memory param, address payer, uint16 poolFee, uint8 protocol)
        private
        pure
        returns (bool, int256, uint160, bytes memory)
    {
        bool zeroForOne = param.tokenIn < param.tokenOut;
        // We shift the fee to save some calldata space in the original call
@>      uint24 shiftedFee = poolFee * 10;

        ...
    }
```

[PoC는 여기 있습니다](https://gist.github.com/T1MOH593/7bceddf53f57d9950dc5740b70c165c7)

권장 사항: uint24로 캐스팅하십시오

```diff
-       uint24 shiftedFee = poolFee * 10;
+       uint24 shiftedFee = uint24(poolFee) * 10;
```

# [L-07] `safeTransfer()` 및 `safeTransferFrom`은 계약 존재 여부를 확인하지 않음 (`safeTransfer()` and `safeTransferFrom` doesn't check contract existence)

현재 구현은 `token` 주소가 스마트 계약이 아닌 경우 성공합니다. 코드가 0인 주소에 대한 모든 호출은 `success == true`를 반환하기 때문입니다.

```solidity
    function safeTransfer(address token, address to, uint256 value) internal {
@>      (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.transfer.selector, to, value));
        if (!(success && (data.length == 0 || abi.decode(data, (bool))))) {
            revert TokenTransfer();
        }
    }

    function safeTransferFrom(address token, address from, address to, uint256 value) internal {
        (bool success, bytes memory data) =
@>          token.call(abi.encodeWithSelector(IERC20.transferFrom.selector, from, to, value));
        if (!(success && (data.length == 0 || abi.decode(data, (bool))))) {
            revert TokenTransferFrom();
        }
    }
```

이 동작은 향후 간과될 수 있으며 잠재적인 문제로 이어질 수 있습니다.

모범 사례로 온전성 검사를 추가하십시오:

```diff
+       require(address.code.length > 0);
```
