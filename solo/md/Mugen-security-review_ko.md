# 소개

**Mugen** 프로토콜에 대한 시간 제한 보안 검토가 pashov에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다.

# 프로토콜 개요

이 프로토콜은 복잡한 트랜잭션을 허용하는 DEX/스왑 어댑터입니다. 예를 들어 여러 DEX(Uniswap, Sushiswap, 3xcalibur)에서 다중 토큰을 다중/단일 토큰으로 또는 그 반대로 스왑할 수 있습니다. 이 프로토콜은 토큰의 원활한 전송을 보장하기 위해 DEX에서 제공하는 기본 어댑터를 통합합니다. 또한 LayerZero의 Stargate 프로토콜과 통합되어 있어 크로스 체인 스왑을 허용합니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

# 보안 평가 요약

**_검토 커밋 해시_ - [61564a3e1eac743cb9b89976cbefbcb0fd15f38f](https://github.com/Mugen-Finance/Products/tree/61564a3e1eac743cb9b89976cbefbcb0fd15f38f)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `ArbitrumSwaps`
- `StargateArbitrum`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 높음: 2개 문제
- 중간: 1개 문제
- 낮음: 3개 문제
- 정보성: 10개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [H-01] | 누구나 `ArbitrumSwaps` 기본 자산 잔액을 사용하거나 훔칠 수 있음 | 높음 |
| [H-02] | 악의적인 사용자가 Uniswap에서 모든 `USDT` 스왑에 대해 프로토콜을 쉽게 되돌릴(revert) 수 있음 | 높음 |
| [M-01] | `swap` 호출의 가스 비용으로 모든 기본 자산 잔액을 보내는 대신 `quoteLayerZeroFee` 사용 | 중간 |
| [L-01] | 배열 인수의 길이가 같은지 확인 | 낮음 |
| [L-02] | `require` 확인을 쉽게 우회할 수 있음 | 낮음 |
| [L-03] | 가스가 제한된 외부 호출 후의 `gasLeft()`가 트랜잭션을 완료하기에 충분하지 않을 수 있음 | 낮음 |
| [I-01] | 일반적인 패턴을 다시 구현하는 것보다 검증된 코드를 선호 | 정보성 |
| [I-02] | `ArbitrumSwaps`의 "단계(step)" 유형에 열거형(enum) 사용 | 정보성 |
| [I-03] | 응집력을 높이기 위해 코드 이동 | 정보성 |
| [I-04] | 양수 전용 uint 값을 얻으려면 `x != 0` 사용 | 정보성 |
| [I-05] | 불필요한 사용자 지정 오류 제거 | 정보성 |
| [I-06] | Solidity 안전 pragma 모범 사례가 사용되지 않음 | 정보성 |
| [I-07] | 외부 메서드에 NatSpec 누락 | 정보성 |
| [I-08] | 계약 이름과 파일 이름 불일치 | 정보성 |
| [I-09] | `override` 키워드 누락 | 정보성 |
| [I-10] | 주석의 오타 | 정보성 |

# 상세 발견 사항

# [H-01] 누구나 `ArbitrumSwaps` 기본 자산 잔액을 사용하거나 훔칠 수 있음

## 심각도

**가능성:**
높음, 쉽게 발견되고 악용될 수 있기 때문

**영향:**
중간, 가치가 도난당할 수 있지만 가스 환불로 제한되어야 하기 때문

## 설명

공격자는 `WETH_DEPOSIT` 및 `WETH_WITHDRAW` 단계를 사용하여 `arbitrumSwaps` 메서드를 호출하여 `ArbitrumSwaps` 기본 자산 잔액을 훔칠 수 있습니다. 이렇게 하면 전체 계약 잔액이 호출자가 제공한 주소로 전송됩니다. 계약은 "스왑 라우터"이며 언제든지 기본 자산 잔액을 보유할 것으로 예상되지 않기 때문에 이는 문제가 되지 않아야 합니다. 하지만 `stargateSwap` 메서드에서 `stargateRouter`에 대한 `swap` 메서드 호출의 `_refundAddress` 인수가 `address(this)`이므로 이 가정은 성립하지 않습니다. 즉, 환불된 모든 기본 자산은 `ArbitrumSwaps` 계약에 보관되며 공격자는 이 환불을 백런(back-run)하여 잔액을 훔칠 수 있습니다.

## 권장 사항

환불 주소는 `address(this)`가 아닌 `msg.sender`여야 합니다. 이렇게 하면 프로토콜이 기본 자산을 받을 것으로 예상되지 않으므로 누군가 실수로 `ArbitrumSwaps` 계약에 보내는 경우(예상되는 위험)에만 도난당할 수 있습니다.

# [H-02] 악의적인 사용자가 Uniswap에서 모든 `USDT` 스왑에 대해 프로토콜을 쉽게 되돌릴(revert) 수 있음

## 심각도

**가능성:**
높음, 공격을 쉽게 수행할 수 있고 `USDT`의 잘 알려진 공격 벡터를 악용하기 때문

**영향:**
중간, 프로토콜이 하나의 ERC20 토큰으로만 작동하지 않지만 널리 사용되는 토큰이기 때문

## 설명

악의적인 사용자는 토큰 구현의 잘 알려진 공격 벡터 때문에 Uniswap의 각 `USDT` 스왑에서 `ArbitrumSwaps` 계약을 되돌릴 수 있습니다. 문제는 `UniswapAdapter.sol`의 다음 코드에 있으며 `swapExactInputSingle` 및 `swapExactInputMultihop` 모두에 존재합니다.

```solidity
        TransferHelper.safeApprove(
            swapParams.token1, address(swapRouter), IERC20(swapParams.token1).balanceOf(address(this))
        );
```

공격 방법은 다음과 같습니다:

1. 악의적인 사용자가 수동으로 1 wei의 USDT를 `ArbitrumSwaps`로 전송합니다.
2. 이제 그는 `step == UNI_SINGLE`로 `ArbitrumSwaps::arbitrumSwaps`를 호출하여 1 USDT를 스왑합니다.
3. 이제 `swapExactInputSingle`은 `balanceOf` 호출로 인해 1 + 1e-18 USDT를 승인합니다.
4. 트랜잭션은 성공적으로 완료되지만, 다음에 누군가 Uniswap에서 USDT를 스왑하려고 하면 USDT 승인 경쟁 조건(approval race condition)으로 인해 트랜잭션이 되돌려집니다. `approve` 호출을 수행하려면 허용량이 0 또는 `type(uint256).max`여야 하는데 허용량이 1 wei이므로 그렇지 않습니다.

## 권장 사항

승인된 허용량으로 `IERC20(multiParams.token1).balanceOf(address(this))`를 사용하는 대신 `amountIn` 매개변수를 사용하십시오.

# [M-01] `swap` 호출의 가스 비용으로 모든 기본 자산 잔액을 보내는 대신 `quoteLayerZeroFee` 사용

## 심각도

**가능성:**
높음, 항상 잘못된 값이 전송되기 때문

**영향:**
낮음, `swap` 함수에는 가스 환불 메커니즘이 있기 때문

## 설명

현재 `StargateArbitrum::stargateSwap`에서 `stargateRouter`의 `swap` 메서드에 대한 호출을 수행할 때 계약의 모든 기본 자산 잔액이 가스 비용를 지불하는 데 사용될 수 있도록 전송됩니다. [Stargate 문서](https://stargateprotocol.gitbook.io/stargate/developers/cross-chain-swap-fee)에 따르면 수수료를 계산하는 적절한 방법이 있으며 `stargateRouter`의 `quoteLayerZeroFee` 메서드를 활용하는 것입니다.

## 권장 사항

환불 메커니즘이 있더라도 항상 전체 계약의 잔액을 수수료로 보내는 대신 문서를 따르라 수수료를 올바르게 계산하십시오.

# [L-01] 배열 인수의 길이가 같은지 확인

`ArbitrumSwaps::arbitrumSwaps` 및 `StargateArbitrum::stargateSwap` 모두에 여러 배열 유형 인수가 있습니다. 두 곳 모두에서 인수의 길이가 동일한지 검증하여 그렇지 않은 경우 예상치 못한 오류가 발생하지 않도록 하십시오.

# [L-02] `require` 확인을 쉽게 우회할 수 있음

`StargateArbitrum::stargateSwap`에 다음 코드가 있습니다.

```solidity
if (msg.value <= 0) revert MustBeGt0();
```

이 확인은 1 wei만 보내면 쉽게 우회할 수 있습니다. 어차피 `msg.value`는 메서드에서 사용되지 않으므로 확인을 완전히 제거하십시오.

# [L-03] 가스가 제한된 외부 호출 후의 `gasLeft()`가 트랜잭션을 완료하기에 충분하지 않을 수 있음

`StargateArbitrum::sgReceive`에 다음 코드가 있습니다.

```solidity
try IArbitrumSwaps(payable(address(this))).arbitrumSwaps{gas: 200000}(steps, data) {}
        catch (bytes memory) {
            IERC20(_token).safeTransfer(to, amountLD);
            failed = true;
        }
```

이제 `arbitrumSwaps` 호출이 모든 가스를 사용했다면 `safeTransfer` 호출과 그 아래 코드에 대한 가스가 충분하지 않을 수 있습니다. `gasleft()`를 확인하고 충분한지 확인하는 다른 접근 방식을 고려하십시오. [이 메서드](https://github.com/sushiswap/sushiswap/blob/9a85946574135d57194c44bf27376732091974cc/protocols/sushixswap/contracts/adapters/StargateAdapter.sol#L114-L171)와 비슷한 방식입니다.

# [I-01] 일반적인 패턴을 다시 구현하는 것보다 검증된 코드를 선호

`ArbitrumSwaps`의 `locked` 수정자를 OpenZeppelin의 `nonReentrant`로 교체하십시오. 잘 테스트 되고 최적화되었기 때문입니다.

# [I-02] `ArbitrumSwaps`의 "단계(step)" 유형에 열거형(enum) 사용

현재 단계 유형은 정수 값을 가지며 순차적이지 않은(숫자 7에서 11이 누락됨) 상수로 처리됩니다. 이것은 순차적 번호 지정과 적절한 명명이 있는 열거형을 사용하기에 좋은 사례입니다. 상수를 제거하고 열거형을 사용하십시오.

# [I-03] 응집력을 높이기 위해 코드 이동

이벤트 `FeePaid`와 `calculateFee` 메서드는 Stargate 로직과 관련이 없지만 일부 스왑/전송 시나리오에서 사용되므로 `StargateArbitrum` 대신 `ArbitrumSwaps`에 있어야 합니다.

# [I-04] 양수 전용 uint 값을 얻으려면 `x != 0` 사용

코드의 "양수 uint" 확인은 가능한 최선의 방법으로 수행되지 않습니다. 한 예로 `_amount <= 0`이 있습니다. 숫자가 `uint` 유형일 것으로 예상되는 경우 `uint`는 음수가 될 수 없으므로 `x != 0`을 수행하여 양수인지 확인할 수 있습니다. `x`가 `uint`일 때 `x <= 0` 발생을 모두 `x != 0`으로 바꾸십시오.

# [I-05] 불필요한 사용자 지정 오류 제거

`ArbitrumSwaps`의 `MoreThanZero` 사용자 지정 오류는 이름이 잘못 지정되었으며 `StargateArbitrum`에서 상속된 사용자 지정 오류 `MustBeGt0`을 복제합니다. 후자를 사용하고 전자를 제거하십시오.

# [I-06] Solidity 안전 pragma 모범 사례가 사용되지 않음

항상 안정적인 pragma를 사용하여 매번 동일한 바이트코드로 Solidity 코드를 결정론적으로 컴파일하십시오. 또한 `IStargateReceiver` 및 `IStargateRouter` 인터페이스는 이전 컴파일러 버전을 사용하고 있습니다. 최신 버전으로 업그레이드하고 전체 코드베이스에서 동일한 pragma 문을 사용하십시오.

# [I-07] 외부 메서드에 NatSpec 누락

`ArbitrumSwaps`의 `arbitrumSwaps` 메서드에 NatSpec 문서가 없습니다. 코드 기술 문서를 개선하기 위해 추가하십시오.

# [I-08] 계약 이름과 파일 이름 불일치

`ArbitrumSwaps` 계약은 `SushiAdapter.sol`에서 가져온 `SushiLegacyAdapter`를 상속합니다. 스마트 계약과 파일에 동일한 이름을 사용하십시오.

# [I-09] `override` 키워드 누락

`ArbitrumSwaps`의 메서드 `arbitrumSwaps`는 `IArbitrumSwaps`에서 메서드를 상속하지만 있어야 할 `override` 키워드가 누락되었습니다.

# [I-10] 주석의 오타

`StargateArbitrum`에서 `arrat` -> `array`라고 썼습니다.
