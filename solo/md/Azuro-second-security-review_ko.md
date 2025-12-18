# 소개

**Azuro** 프로토콜에 대한 시간 제한 보안 검토가 pashov에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다.

# 프로토콜 개요

**첫 번째 보안 검토에서 복사됨**

Azuro는 탈중앙화 베팅 프로토콜입니다. 누구나 스마트 계약에 연결하는 프런트엔드 서비스를 시작할 수 있으며 해당 프런트엔드를 통해 이루어진 각 베팅에 대해 제휴 보너스를 받을 수 있습니다. 축구 경기와 같은 다양한 베팅 이벤트를 호스팅 할 수 있습니다. 배당률은 초기화를 위해 데이터 피드 제공자(오라클)에 의해 한 번 제공된 다음 플랫폼의 베팅에 따라 변경됩니다. 사용자 베팅은 사용자 지갑의 NFT로 자동 변환됩니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

# 보안 평가 요약

**_검토 커밋 해시_ - [1c475b43e47798ae0a49716fe949b523a2663d0a](https://github.com/Azuro-protocol/Azuro-v2/tree/1c475b43e47798ae0a49716fe949b523a2663d0a)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `BetExpress`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 1개 문제
- 중간: 2개 문제
- 낮음: 1개 문제
- 정보성: 8개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | `addReserve` 호출 시 `leaf` 인수의 값이 잘못 하드코딩됨 | 치명적 |
| [M-01] | `maxBetShare`에 대한 보호 확인은 악용될 수 있음 | 중간 |
| [M-02] | no-op fallback 함수가 있는 토큰을 사용하여 `LP`의 ETH 잔액을 훔칠 수 있음 | 중간 |
| [L-01] | 0을 인수 값으로 사용하는 것은 오류가 발생하기 쉬움 | 낮음 |
| [I-01] | 타임스탬프 확인 시 Off-by-one 오류 | 정보성 |
| [I-02] | "core"라는 단어는 프로토콜에서 여러 의미를 가지며 복잡성을 높임 | 정보성 |
| [I-03] | 불필요한 getter | 정보성 |
| [I-04] | 이벤트 방출 누락 | 정보성 |
| [I-05] | `override` 키워드 누락 | 정보성 |
| [I-06] | 사용되지 않는 import | 정보성 |
| [I-07] | 잘못된 주석 | 정보성 |
| [I-08] | 안전한 pragma 문 사용 | 정보성 |

# 상세 발견 사항

# [C-01] `addReserve` 호출 시 `leaf` 인수의 값이 잘못 하드코딩됨

## 심각도

**영향:**
높음, 유동성이 LiquidityTree로 반환되지 않기 때문

**가능성:**
높음, 잘못된 값이 하드코딩되어 변경할 수 없기 때문

## 설명

`BetExpress::resolvePayout`에서 다음 코드를 볼 수 있습니다:

```solidity
uint128 reward = lp.addReserve(
    0,
    fullPayout - amount,
    fullPayout - payout,
    0
    );
```

여기서 마지막 인수는 `leaf` 매개변수의 값으로 전송된 0입니다. 리프(leaf) 카운팅은 1에서 시작하므로 이는 항상 잘못된 것이며 유동성은 LiquidityTree로 반환되지 않습니다.

## 권장 사항

`leaf`의 값은 베팅에 있는 각 `condition`의 `leaf` 값이어야 합니다. `resolvePayout`의 현재 설계는 각 `condition`을 개별적으로 작업하는 것을 허용하지 않으므로 각 `condition`을 별도로 처리하는 재설계가 필요합니다.

# [M-01] `maxBetShare`에 대한 보호 확인은 악용될 수 있음

## 심각도

**영향:**
중간, 프로토콜 불변성이 깨질 수 있고 코드가 잘못된 보안 감각을 제공하기 때문

**가능성:**
중간, 쉽게 악용될 수 있지만 공격자에게 동기가 없기 때문

## 설명

`lockLiquidity` 메서드는 단일 베팅이 LP의 허용된 유동성 한도를 너무 많이 차지하는 것을 차단하려고 시도하지만, 이는 매우 큰 베팅을 수많은 작은 베팅으로 나누어 악용할 수 있으므로 이 `LargeBet` 사용자 정의 오류 확인은 의도한 것을 보장하지 않으므로 잘못된 보안 감각을 줄 수 있습니다.

## 권장 사항

검증을 각 베팅을 개별적으로 하는 대신 `BetExpress`를 통해 이루어진 모든 베팅을 기반으로 하도록 변경하십시오.

# [M-02] no-op fallback 함수가 있는 토큰을 사용하여 `LP`의 ETH 잔액을 훔칠 수 있음

## 심각도

**영향:**
높음, 프로토콜에서 자금이 도난당할 수 있기 때문

**가능성:**
낮음, `withdraw` 함수 없이 fallback 함수만 있는 토큰이 필요하기 때문

## 설명

`LP::withdrawPayout`에는 다음과 같은 코드가 있습니다:

```solidty
if (isNative) {
    IWNative(token).withdraw(amount);
    TransferHelper.safeTransferETH(account, amount);
} else {
    TransferHelper.safeTransfer(token, account, amount);
}
```

이제 다음 시나리오를 상상해 보십시오:

1. 계약에 사용된 `token`이 `withdraw` 함수는 없지만 fallback 함수가 있는 토큰입니다.
2. 공격자는 100 \* 1e18 토큰의 당첨 베팅을 가지고 있습니다.
3. 이제 그는 `withdrawPayout`을 호출하지만 `isNative` 플래그를 `true`로 설정합니다.
4. `IWNative(token).withdraw(amount);`는 되돌리지 않지만 `token`의 fallback 함수로 인해 아무런 작업도 수행하지 않음(no-op)이 됩니다.
5. 공격자는 100 \* 1e18 토큰 대신 100 ETH를 받게 됩니다.

공격은 [이것](https://medium.com/dedaub/phantom-functions-and-the-billion-dollar-no-op-c56f062ae49f)과 유사하며 특별한 토큰과 유동성을 보유한 `LP`가 필요하지만 여전히 잠재적인 공격 벡터입니다.

## 권장 사항

`LP`에서 사용할 특정 `token` 계약을 추가하는 것에 대한 팀 프로세스를 구현할 수 있습니다. 여기에는 `withdraw` 함수가 누락된 fallback 함수가 있는 토큰을 포함하지 않는 체크리스트가 포함됩니다. 또한 `withdraw` 호출 전후에 `LP`의 잔액을 확인하여 그에 따라 변경되었는지 확인할 수 있습니다.

# [L-01] 0을 인수 값으로 사용하는 것은 오류가 발생하기 쉬움

0이 유효한 값인 인수를 생략하는 서명을 갖도록 메서드를 오버로드하는 것이 모범 사례입니다. 0을 유효한 값으로 의도적으로 사용하는 것은 오류가 발생하기 쉽고 과거에 여러 프로토콜에서 높은 심각도의 문제로 이어졌습니다.

# [I-01] 타임스탬프 확인 시 Off-by-one 오류

`_conditionIsRunning`의 코드는 `block.timestamp >= startsAt`일 때 되돌리지만(revert), `block.timestamp == startsAt`이면 조건이 실행 중임을 의미하므로 되돌리면 안 됩니다.

```diff
- block.timestamp >= startsAt
+ block.timestamp > startsAt
```

# [I-02] "core"라는 단어는 프로토콜에서 여러 의미를 가지며 복잡성을 높임

"core"라는 단어는 계약 이름(`Core`, `CoreBase`)과 프로토콜의 일부를 의미하는 단어(예: `BetExpress`)로 모두 사용됩니다. 이는 직관적이지 않으며 프로토콜의 복잡성을 높이므로 이상적이지 않습니다. 코드베이스에서 "core"의 두 가지 의미에 대해 다른 단어를 사용하는 것을 고려하십시오.

# [I-03] 불필요한 getter

`_baseURI` getter는 이미 자동으로 생성된 `baseURI` getter가 있으므로 불필요합니다.

# [I-04] 이벤트 방출 누락

`BetExpress.sol`의 `setBaseURI` 메서드는 이벤트를 방출하지 않아 오프체인 모니터링에 좋지 않을 수 있습니다. 상태 변경 시 이벤트를 방출하십시오.

# [I-05] `override` 키워드 누락

`BetExpress.sol`의 `initialize` 및 `viewPayout` 메서드는 각각 `ICoreBased.sol` 및 `IBet.sol`에서 함수 서명을 상속받았음에도 불구하고 override 키워드가 누락되었습니다.

# [I-06] 사용되지 않는 import

`import "./libraries/Math.sol"` 및 `"@uniswap/lib/contracts/libraries/TransferHelper.sol"`은 `BetExpress.sol`에서 사용되지 않으므로 제거할 수 있습니다.

# [I-07] 잘못된 주석

`BetExpress.sol`의 `putBet` 메서드에 다음 주석이 있습니다:

```solidity
@notice Liquidity Pool: See {IBetEngine-putBet}.
```

이 경우 올바른 인터페이스는 `IBetEngine.sol` 대신 `IBet.sol`입니다.
또한 `IBet.sol`의 `putBet` 메서드에 `@return` 필드가 누락되었습니다. 하나 추가하는 것을 고려하십시오.

# [I-08] 안전한 pragma 문 사용

항상 안정적인 pragma 문을 사용하여 컴파일러 버전을 고정하십시오. 또한 코드베이스 전체에서 다양한 버전의 컴파일러가 사용되므로 하나만 사용하십시오. 마지막으로 컴파일러의 버그 수정 및 최적화를 사용하기 위해 버전을 최신 버전으로 업그레이드하는 것을 고려하십시오.
