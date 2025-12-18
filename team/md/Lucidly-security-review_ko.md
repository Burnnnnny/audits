# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**lucidlyfi/lucidly-core-v1** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Lucidly 정보

Lucidly는 다양한 토큰으로 구성된 유동성 풀을 생성할 수 있습니다. 이 풀은 또한 이러한 토큰에 대한 AMM 역할을 하여 토큰 간의 교환을 허용합니다.

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

_검토 커밋 해시_ - [f00c45bbadc836b9eaa94717a0b3aa017e792588](https://github.com/lucidlyfi/lucidly-core-v1/tree/f00c45bbadc836b9eaa94717a0b3aa017e792588)

_수정 검토 커밋 해시_ - [f36389e44cc99ac5a762722de3e98644f5407032](https://github.com/lucidlyfi/lucidly-core-v1/tree/f36389e44cc99ac5a762722de3e98644f5407032)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Pool`
- `Staking`

# 발견 사항

# [C-01] 공급 계산 시 잘못된 변수 사용

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

풀 계약의 `_calculateSupply` 함수에서 중간 `_r`을 계산할 때 함수는 다음 로직을 사용합니다.

```solidity
_r = FixedPointMathLib.rawDiv(
    FixedPointMathLib.rawMul(_s, _sp), _s);
    // _r*_sp/_s
```

주석은 맞지만 구현이 틀렸습니다. `s` 대신 `_r`을 사용해야 합니다.

이것은 여기 yearn 계약에서도 볼 수 있습니다.
[링크](https://github.com/yearn/yETH/blob/8d831fd6b4de9f004d419f035cd2806dc8d5cf7e/contracts/Pool.vy#L1366)

## 권장 사항

줄을 다음과 같이 수정하십시오.

```solidity
_r = FixedPointMathLib.rawDiv(
    FixedPointMathLib.rawMul(_r, _sp), _s);
    // _r*_sp/_s
```

# [C-02] 비율 업데이트 시 잘못된 인덱스 사용

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`_updateRates` 함수에서 비율을 업데이트할 때 함수는 실제 토큰 배열의 인덱스를 나타내는 인덱스 모음이 포함된 정수를 입력으로 받습니다.

```solidity
    /// @param tokens_ integer where each byte represents a token index offset by one
```

다음으로 반복자 `t`가 있는 루프를 사용하여 값을 꺼냅니다.

```solidity
for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
            uint256 token = (tokens_ >> FixedPointMathLib.rawMul(8, t)) & 255;
```

참고로 `t`는 입력을 처리하기 위한 반복자입니다. `token`은 배열 요소에 액세스하는 데 사용해야 하는 실제 추출된 인덱스입니다.

그러나 코드는 실수로 `t`를 사용하여 비율을 찾습니다.

```solidity
address provider = rateProviders[token];
// ...
uint256 _rate = IRateProvider(provider).rate(tokens[t]);
```

따라서 `tokens[token]`을 사용하는 대신 `tokens[t]`에 대해 비율이 잘못 호출됩니다. 따라서 코드는 완전히 다른 토큰의 비율을 사용하게 됩니다.

올바른 사용법은 여기 YETH 계약에서 찾을 수 있습니다.
[링크](https://github.com/yearn/yETH/blob/8d831fd6b4de9f004d419f035cd2806dc8d5cf7e/contracts/Pool.vy#L1141)

잘못된 비율을 사용하면 시스템의 모든 계산이 틀어져 공격자가 가치를 추출할 수 있습니다.

## 권장 사항

`rate(tokens[token])`을 사용하십시오.

# [C-03] 잘못된 수학 연산으로 저장된 곱이 과소 예측됨

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`swap` 함수는 토큰을 교환하는 데 사용됩니다. 계약은 환율을 계산하기 위해 가상 잔액의 곱과 합계라는 두 가지 값을 추적합니다. `swap` 함수에서 곱이 업데이트됩니다.

```solidity
_virtualBalanceProd = _virtualBalanceProd
            + _powUp(_prevVirtualBalanceY, _wnY) / _powDown((_virtualBalanceX * PRECISION) / _prevVirtualBalanceX, _wnX);
```

문제는 곱셈과 나눗셈으로 곱을 업데이트하는 대신 덧셈으로 업데이트한다는 것입니다. 이로 인해 수학 및 업데이트 절차가 완전히 망가져 잘못된 환율이 발생합니다.

올바른 버전은 여기 yETH 계약에서 찾을 수 있습니다.
[링크](https://github.com/yearn/yETH/blob/8d831fd6b4de9f004d419f035cd2806dc8d5cf7e/contracts/Pool.vy#L279)

## 권장 사항

덧셈을 곱셈으로 변경하십시오.

```solidity
_virtualBalanceProd = _virtualBalanceProd
            * _powUp(_prevVirtualBalanceY, _wnY) / _powDown((_virtualBalanceX * PRECISION) / _prevVirtualBalanceX, _wnX);

```

# [C-04] 나눗셈 누락으로 저장된 곱이 크게 축소됨

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

스왑 중에 가상 잔액의 곱은 수수료에 맞게 조정되어야 합니다. 이것은 다음 줄에서 수행됩니다. (286행)

```solidity
_virtualBalanceProd = (_virtualBalanceProd * PRECISION)
                / _powDown(
                    (_virtualBalanceX + _changeInVirtualBalanceTokenIn)
                    * PRECISION,
                    _wnX
                    );
```

이 표현식은 잘못되었습니다. 거듭제곱은 백분율 변경에 대해 제기되어야 하지만 여기서는 총 변경에 대해 제기됩니다. 따라서 ((x+y)/x)^a를 수행하는 대신 코드는 (x+y)^a를 수행합니다. 이로 인해 잦은 오버플로가 발생할 수 있을 뿐만 아니라 저장된 곱의 가치가 크게 하락하여 시스템의 환율이 나빠질 수 있습니다.

올바른 구현은 yETH 계약에서 찾을 수 있습니다.
[링크](https://github.com/yearn/yETH/blob/8d831fd6b4de9f004d419f035cd2806dc8d5cf7e/contracts/Pool.vy#L296)

`powDown` 함수의 매개변수는 `_virtualBalanceX`만큼 축소되어야 합니다.

## 권장 사항

표현식을 다음과 같이 변경하십시오.

```solidity
_virtualBalanceProd = (_virtualBalanceProd * PRECISION)
                / _powDown(
                (_virtualBalanceX + _changeInVirtualBalanceTokenIn)
                * PRECISION
                / _virtualBalanceX, _wnX);
```

# [C-05] 잘못된 뺄셈으로 풀 고갈 발생

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`removeLiquiditySingle` 함수는 유동성을 제거할 때 인출할 토큰의 양을 계산합니다.

유동성 제거 전후의 가상 잔액 차이를 계산하여 이를 계산합니다.

```solidity
        uint256 _changeInVirtualBalance = _prevVirtualBalance = _virtualBalance;
```

그러나 위에서 볼 수 있듯이 빼기 `-` 연산자 대신 등호 `=` 연산자를 사용합니다. 따라서 차이를 계산하는 대신 차이를 `_virtualBalance`로 설정하는데, 이는 풀에 남아 있었을 유동성의 양입니다.

따라서 사용자가 1 wei를 제거하면 차이를 1 wei로 계산하는 대신 `_changeInVirtualBalance`를 풀의 전체 잔액으로 설정합니다. 따라서 공격자는 이 결함을 사용하여 풀에서 모든 토큰을 인출할 수 있습니다.

## 권장 사항

두 번째 등호를 빼기 기호로 변경하십시오.

```solidity
        uint256 _changeInVirtualBalance = _prevVirtualBalance - _virtualBalance;

```

# [H-01] `unpause` 및 `kill` 함수의 잘못된 `killed` 체크

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

풀은 일시 중지, 일시 중지 해제 또는 종료(kill)되도록 설계되었습니다. 종료되면 다시 일시 중지 해제할 수 없습니다.

`unpause` 함수를 살펴보겠습니다.

```solidity
 if (!paused) revert Pool__NotPaused();
if (!killed) revert Pool__Killed();
paused = false;
```

두 번째 줄은 종료 상태를 확인합니다. 기본적으로 풀이 종료되지 않은 경우 함수가 되돌려져야 한다고 말합니다. 이것은 논리적 결함입니다. 그렇지 않은 경우가 아니라 풀이 종료된 경우 함수가 되돌려져야 합니다. 풀 `killed` 변수는 false로 시작합니다. 따라서 풀이 일시 중지되었다가 다시 일시 중지 해제되면 `!false=true`이므로 되돌리기(revert)가 트리거되어 일시 중지 해제가 발생하지 않습니다.

`kill` 함수에도 유사한 문제가 존재합니다.

```solidity
if (!killed) revert Pool__Killed();
        killed = true;
```

다시 말하지만, 계약이 이미 종료된 경우 되돌려져야 합니다. 현재 상태에서는 `killed`가 기본적으로 false로 설정되어 있으므로 계약을 종료할 수 없으며 `!killed`는 항상 true가 되어 이 코드에 도달할 수 없습니다.

## 권장 사항

두 경우 모두 절을 `if(killed)`로 변경하십시오.

# [H-02] if 절에 부정 누락

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`setWeightBands` 함수를 통해 가중치를 설정할 때 특정 제한 사항을 따라야 합니다. 가중치는 1e18인 PRECISION 값보다 낮아야 합니다.

가중치의 합이 1e18이 되어야 하기 때문입니다.

이 확인은 `setWeightBands` 함수에 잘못 적용되었습니다.

```solidity
if ((lower_[t] <= PRECISION && upper_[t] <= PRECISION)) {
                revert Pool__BandsOutOfBounds();
            }
```

위에서 볼 수 있듯이 두 가중치가 모두 PRECISION 미만이면 계약이 되돌려집니다. 따라서 함수는 가중치 설정을 1e18 상한 이상으로 강제하며, 이는 시스템의 다른 부분을 망가뜨릴 것입니다. 사실 코드에는 부정 `!`가 누락되어 있으므로 계약은 기준이 충족된 경우가 아니라 충족되지 않은 경우에만 되돌려져야 합니다.

## 권장 사항

if 문을 다음과 같이 변경하십시오.

```solidity
if (!(lower_[t] <= PRECISION && upper_[t] <= PRECISION)) {

```

# [H-03] `swap` 함수의 잘못된 경계 검사

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`swap` 함수는 전달된 인덱스가 범위를 벗어나지 않았는지 확인합니다.

```solidity
if (tokenIn_ > _numTokens - 1 && tokenOut_ > _numTokens - 1) {
            revert Pool__IndexOutOfBounds();
        }
```

문제는 위 스니펫에서 계약은 두 확인이 모두 통과하는 경우, 즉 두 인덱스가 모두 범위를 벗어난 경우에 되돌려진다는 것입니다. 하나만 범위를 벗어나면 되돌리기가 발생하지 않습니다. 대신 0 값을 사용하고 배열의 크기가 풀의 최대 토큰 수보다 크므로 자연스럽게 되돌려지지 않아 예측할 수 없는 동작으로 이어집니다.

## 권장 사항

`&&`를 `||`로 변경하십시오.

# [H-04] 풀 생성자에서 `amplification_` 대 `amplification`

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

입력 유효성 검사 중 발생한 실수:

```
        if (amplification == 0) {
            revert Pool__MustBeInitiatedWithAGreaterThanZero();
        }

        amplification = amplification_;
```

[링크](https://github.com/lucidlyfi/lucidly-core-v1/blob/f00c45bbadc836b9eaa94717a0b3aa017e792588/src/Pool.sol#L151)

여기서 저장소 값은 기록되지 않은 상태(0과 같음)를 확인합니다.
결과적으로 모든 풀 배포가 실패합니다.

## 권장 사항

```diff
-        if (amplification == 0) {
+        if (amplification_ == 0) {
```

# [H-05] MAXLENGTH 미만의 루프 길이 반복

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

풀 배포 중 토큰 길이가 `32`인 `MAXLENGHT` 미만인 풀에 대해 트랜잭션이 되돌려집니다.

- [링크](https://github.com/lucidlyfi/lucidly-core-v1/blob/f00c45bbadc836b9eaa94717a0b3aa017e792588/src/Pool.sol#L160)

out-of-bound 오류로 되돌려지므로 32의 긴 토큰 길이를 제공해야 하는데 항상 그런 것은 아닙니다.

## 권장 사항

루프는 `_numTokens`까지 반복해야 합니다.
또는 루프는 루프가 최종 반복에 도달할 때 다른 함수의 이 패턴을 사용해야 합니다.

```
            if (t == _numTokens) {
                break;
            }
```

# [H-06] numTokens < MAX_NUM_TOKENS인 경우 유동성 추가/제거 불가능

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

```
function addLiquidity(
        uint256[MAX_NUM_TOKENS] calldata amounts_,
        uint256 minLpAmount_,
        address receiver_
        )
        external
        nonReentrant
        returns (uint256)
    {
```

여기서 \_amount는 32의 길이로 제공되어야 합니다.
반면 나중에 다른 것을 요구합니다.

```
if (amounts_.length != _numTokens) revert Pool__InvalidParams();
```

따라서 \_numTokens가 32 미만일 때 이 두 가지 요구 사항이 충돌합니다. 따라서 이러한 풀에 유동성을 제공하는 것은 불가능합니다.

`removeLiquidity()`도 마찬가지입니다.

## 권장 사항

다음과 같이 변경하는 것을 고려하십시오.

```diff
+ function addLiquidity(
        uint256[] calldata amounts_,
        uint256 minLpAmount_,
        address receiver_
        )
        external
        nonReentrant
        returns (uint256)
    {
```

`removeLiquidity()`에 대해서도 동일한 사항을 수정해야 합니다.

# [H-07] 현재 pow 수학은 정수만 허용함

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

Pow 수학은 두 번째 매개변수로 정수만 허용하기 때문에 `FixedPointMathLib.rpow`를 사용하는 것으로는 충분하지 않습니다.
[링크](https://github.com/lucidlyfi/lucidly-core-v1/blob/f00c45bbadc836b9eaa94717a0b3aa017e792588/src/Pool.sol#L1475)

그러나 예상되는 pow 계산은 예를 들어 0.5 ^0.5와 같은 계산을 허용해야 합니다(가중치와 함께 작동하기 때문).

결과적으로 `FixedPointMathLib.rpow`를 사용하는 모든 현재 계산은 오버플로로 인해 되돌려집니다.

## 권장 사항

대체 수학 라이브러리를 사용하는 것을 고려하십시오.

# [H-08] \_calculateSupply()에 대한 변수 잘못된 사용

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`addLiquidity()`는 `_calculateSupply()`를 두 번 사용합니다.

```
        (_supply, _virtualBalanceProd) = _calculateSupply(
            _numTokens,
            _prevSupply,
            amplification,
            _virtualBalanceProdFinal,
            _virtualBalanceSumFinal,
            true
        );
```

[링크](https://github.com/lucidlyfi/lucidly-core-v1/blob/f00c45bbadc836b9eaa94717a0b3aa017e792588/src/Pool.sol#L520)

몇 가지 실수가 있습니다.

1. `_prevSupply`가 사용되는데 `_supply`가 정확합니다.
   그렇지 않으면 여기에서 0 요구 사항으로 인해 첫 번째 유동성 공급 시 되돌려집니다.
   [링크](https://github.com/lucidlyfi/lucidly-core-v1/blob/f00c45bbadc836b9eaa94717a0b3aa017e792588/src/Pool.sol#L1303)
2. `_virtualBalanceProdFinal`이 사용되는데 `_virtualBalanceProd`가 정확합니다.
3. `_virtualBalanceSumFinal`이 사용되는데 `_virtualBalanceSum`이 정확합니다.
4. `true`가 마지막 인수로 사용되는데 `_prevSupply == 0`일 때 true입니다. 따라서 `_prevSupply == 0`으로 대체해야 합니다.

## 권장 사항

`_calculateSupply()`의 첫 번째 사용에서 필요한 조정을 적용하십시오.
그 이후의 두 번째 사용은 정확합니다.

# [H-09] 잘못된 루프 영역

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

여기 루프는 원하는 공식 계산에 따라 올바르게 종료되지 않습니다.
[링크](https://github.com/lucidlyfi/lucidly-core-v1/blob/f00c45bbadc836b9eaa94717a0b3aa017e792588/src/Pool.sol#L1306-L1308)

yETH의 다음 계산과 비교하십시오.
[링크](https://github.com/yearn/yETH/blob/8d831fd6b4de9f004d419f035cd2806dc8d5cf7e/contracts/Pool.vy#L1363-L1366)

루프는 `delta = 0` 이전에 종료됩니다.

## 권장 사항

이 줄들은 루프에만 포함되어야 합니다.

```diff
            for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
                if (t == numTokens_) break;
                _r = FixedPointMathLib.rawDiv(FixedPointMathLib.rawMul(_s, _sp), _s);
+               }
                uint256 _delta = 0;
                ...
```

그리고 그에 따라 다음 괄호를 변경하십시오.

# [H-10] 잘못된 `POOL_VB_MASK`로 인해 `_packPoolVirtualBalance()`가 되돌려짐

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

POOL_VB_MASK를 사용한 모든 계산은 저장된 값이 잘못되어 항상 되돌려집니다.
예: 유동성 공급 중 `_packPoolVirtualBalance()`에서 되돌려집니다.

## 권장 사항

`POOL_VB_MASK` 값이 수정되어야 합니다.

```diff
- uint256 public constant POOL_VB_MASK = 2 * 128 - 1;
+ uint256 public constant POOL_VB_MASK = 2 ** 128 - 1;
```

# [H-11] `updateWeights`에서 `_updateSupply` 호출 누락

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`updateWeights` 함수는 가상 잔액 제품(`vb_prod`)을 업데이트하지만 `_updateSupply`에 대한 호출은 포함하지 않습니다. 이러한 불일치로 인해 오래된 `supply`가 사용되어 풀 내의 스왑 및 유동성 조정의 정확성에 영향을 줄 수 있습니다.

문제는 이 함수가 공급을 업데이트하지 않는다는 것입니다. 가중치와 잔액이 업데이트되므로 풀의 상태를 동기화하려면 공급도 업데이트해야 합니다. 공급 업데이트는 여기 yETH 풀에 존재합니다.
[링크](https://github.com/yearn/yETH/blob/8d831fd6b4de9f004d419f035cd2806dc8d5cf7e/contracts/Pool.vy#L690)

## 권장 사항

공급 상태가 동기화되도록 `updateWeights` 함수 내에 `_updateSupply` 호출을 통합하십시오.

```diff
function updateWeights() external returns (bool) {
    _checkIfPaused();
    bool _updated = false;
    (uint256 _virtualBalanceProd, uint256 _virtualBalanceSum) = _unpackPoolVirtualBalance(packedPoolVirtualBalance);
    (_virtualBalanceProd, _updated) = _updateWeights(_virtualBalanceProd);
    if (_updated && _virtualBalanceSum > 0) {
+        uint256 _supply;
+        (_supply, _virtualBalanceProd) = _updateSupply(supply, _virtualBalanceProd, _virtualBalanceSum);
        packedPoolVirtualBalance = _packPoolVirtualBalance(_virtualBalanceProd, _virtualBalanceSum);
    }
    return _updated;
}
```

# [H-12] 잘못된 공급 사용

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

계약은 일부 장소에서는 전역 변수 `supply`를 사용하고 다른 장소에서는 함수 값 `totalSupply`를 사용합니다. 문제는 이 두 값이 동일하지 않다는 것입니다.

`totalSupply`는 유통 중인 총 발행 LP 토큰입니다.

`supply`는 풀을 구성하는 토큰의 가중치, 합계 및 곱을 기반으로 Newton-Raphson 반복을 사용하여 계산된 값입니다.

이 두 값은 `_calculateSupply` 다음에 `_updateSupply`를 호출한 후에만 동기화됩니다. 후자의 함수는 두 수량을 일치시키기 위해 LP 토큰 공급을 소각하거나 발행하기 때문입니다.

그러나 이것은 코드베이스에서 준수되지 않으며 `supply`를 사용해야 하는 곳에 `totalSupply`가 사용됩니다.

한 가지 예는 `removeLiquidity` 함수에 있습니다. 이 함수 호출 전에 두 값 `supply`와 `totalSupply`가 동기화되었다고 가정해 보겠습니다. 이 함수에서 먼저 LP 토큰이 소각됩니다.

```solidity
 uint256 _prevSupply = totalSupply();
_burn(msg.sender, lpAmount_);
```

이렇게 하면 `totalSupply`가 방금 내려갔지만 `supply`는 업데이트되지 않으므로 두 값이 즉시 달라집니다. 그런 다음 곱이 계산될 때 `totalSupply`가 사용되는데, 이는 이제 `supply` 값과 다릅니다.

```solidity
_virtualBalanceProd = FixedPointMathLib.rawDiv(
                FixedPointMathLib.rawMul(
                    _virtualBalanceProd,
                    _powDown(
                        FixedPointMathLib.rawDiv(FixedPointMathLib.rawMul(totalSupply(), __weight), vb),  //@audit totalSupply is used
                        FixedPointMathLib.rawMul(__weight, _numTokens)
                    )
                ),
                PRECISION
            );
```

이것은 백서의 계산과 다릅니다. 대신 `supply`를 사용해야 합니다. 이것은 여기 yETH 코드 베이스에도 표시됩니다.
[링크](https://github.com/yearn/yETH/blob/8d831fd6b4de9f004d419f035cd2806dc8d5cf7e/contracts/Pool.vy#L561)

이 문제로 인해 계산된 곱에 불일치가 발생합니다. 이로 인해 나중에 환율이 나빠질 수 있습니다.

## 권장 사항

`totalSupply` 사용의 모든 인스턴스를 yETH 계약과 같이 `supply` 또는 그에 상응하는 것으로 교체하십시오. `totalSupply`는 두 값이 먼저 동기화된 경우에만 사용할 수 있습니다.

# [H-13] 토큰 추가 시 잘못된 공급 업데이트

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`add_token` 함수는 공급을 계산한 다음 호출자에게 적절한 수의 LP 토큰을 발행합니다. 이 함수는 또한 새 풀 상태의 증폭 계수인 `amplification_`을 받습니다.

새 공급을 계산하려면 풀은 새로운 풀 상태이므로 이 새로운 증폭 값을 사용해야 합니다.

```solidity
function addToken(
        uint256 amplification_,
        // ...
    ) external onlyOwner {
        (__supply, _virtualBalanceProd) = _calculateSupply(
            _numTokens,
            _virtualBalanceSum,
            amplification,
            _virtualBalanceProd,
            _virtualBalanceSum,
            true
        );
```

그러나 위에서 볼 수 있듯이 함수는 새 증폭을 `aplification_`에 저장하지만 이전 `amplification`을 사용하여 공급을 계산합니다. 이것은 잘못된 것이며 증폭의 변화가 설명되지 않았기 때문에 잘못된 값이 계산됩니다.

올바른 사용법은 yETH 계약에서 찾을 수 있습니다.
[링크](https://github.com/yearn/yETH/blob/8d831fd6b4de9f004d419f035cd2806dc8d5cf7e/contracts/Pool.vy#L868)

새 증폭 값을 사용하여 공급을 계산해야 합니다.

## 권장 사항

`amplification`을 `amplification_`으로 변경하십시오.

# [H-14] 잘못된 매개변수가 특정 함수를 벽돌로 만듦(bricks)

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

solidity에서 함수 매개변수 목록에 정의된 길이의 배열이 있는 경우 사용자가 해당 길이의 배열을 전달하도록 강제합니다. 전달된 배열의 길이가 예상치 못한 길이면 호출이 되돌려집니다.

`setWeightBands` 및 `setRamp` 함수의 경우입니다.

```solidity
function setWeightBands(
        uint256[MAX_NUM_TOKENS] calldata tokens_,
        uint256[MAX_NUM_TOKENS] calldata lower_,
        uint256[MAX_NUM_TOKENS] calldata upper_
    )
```

`MAX_NUM_TOKENS=32`이므로 호출자는 32개 요소가 있는 배열을 전달해야 합니다. 더 낮으면 이 호출이 되돌려집니다. 이것은 `setRamp` 함수에도 존재합니다.

```solidity
function setRamp(
        uint256 amplification_,
        uint256[MAX_NUM_TOKENS] calldata weights_,
        uint256 duration_,
        uint256 start_
    )
```

그러나 이러한 함수는 `numTokens` 크기의 배열만 기대합니다. 사실 더 큰 배열이 전달되면 다른 줄 때문에 되돌려집니다.

```solidity
for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
           //...
            if (t >= _numTokens) revert Pool__IndexOutOfBounds();
```

따라서 사용자는 32길이 배열을 전달해야 하거나 호출이 되돌려지며, 그렇게 하면 풀이 실제로 32개 토큰을 사용하지 않는 경우 `_numTokens`가 초과되므로 호출이 여전히 되돌려집니다.

따라서 이 두 함수에 대한 모든 호출은 되돌려집니다. 따라서 이러한 함수는 사용할 수 없습니다.

## 권장 사항

함수 매개변수에서 크기 요구 사항을 제거하십시오.

```solidity
function setWeightBands(
        uint256[] calldata tokens_,
        uint256[] calldata lower_,
        uint256[] calldata upper_
    )
```

```solidity
function setRamp(
        uint256 amplification_,
        uint256[] calldata weights_,
        uint256 duration_,
        uint256 start_
    )
```

# [M-01] `setWeightBands` 함수의 잘못된 제한 확인

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`setWeightBands` 함수는 토큰의 가중치 대역을 설정하는 데 사용됩니다. 작업할 토큰의 인덱스 목록이 포함된 `tokens` 배열을 받습니다.

따라서 제한을 확인하려면 `tokens[i]`의 값을 확인해야 합니다.

```solidity
 for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
            //...
            if (t >= _numTokens) revert Pool__IndexOutOfBounds();
```

코드에서 `t`는 `tokens` 배열에서 인덱스를 꺼내는 데 사용되는 반복자입니다. 문제는 제한 확인이 `tokens_[t]`인 실제 인덱스 대신 반복자 `t`에서 수행된다는 것입니다. 이로 인해 오류 구문 분석이 잘못됩니다.

계약의 배열은 크기가 `MAX_NUM_TOKENS` 크기이므로 크기를 벗어난 요소에 액세스할 때 자연스럽게 되돌려지지 않습니다. 대신 사용되지 않은 토큰 슬롯의 가중치가 설정됩니다.

## 권장 사항

`Pool__IndexOutOfBounds` 확인을 다음과 같이 변경하십시오.

```solidity
if (tokens_[t] >= _numTokens) revert Pool__IndexOutOfBounds();
```

# [M-02] `_calculateVirtualBalanceProd`의 잘못된 확인

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`_calculateVirtualBalanceProd` 함수는 `weight`가 0이거나 토큰의 `virtualBalance`가 0인 경우 되돌려져야 합니다.

그러나 구현에서는 둘 다 0인 경우에만 중단됩니다.

```solidity
