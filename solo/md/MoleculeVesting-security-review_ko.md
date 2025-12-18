# 소개

**Molecule Vesting** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Molecule Vesting** 소개

이 프로토콜은 온체인 베스팅(vesting) 스킴을 나타냅니다. 클리프(cliff)와 베스팅 기간을 포함하는 일반적인 베스팅 스킴을 사용합니다. 사용자당 여러 베스팅 일정을 허용하며, 베스팅되지 않은 토큰 금액은 중앙 엔티티(계약 소유자)가 취소할 수 있습니다. 베스팅 일정은 사용자의 양도 불가능한 ERC20 잔액으로 표시되며 거버넌스 목적으로도 활용될 수 있습니다.

추가 문서는 [여기](https://github.com/moleculeprotocol/token-vesting-contract/blob/main/README.md)에 있습니다.

## 관찰 사항

18 소수점 토큰만 허용됩니다.

계약은 많은 메서드에 대해 `nonReentrant` 수정자를 사용하므로 ERC777 유형 재진입 공격으로부터 보호됩니다.

계약은 베스팅할 토큰을 받기 위해 `transferFrom`을 사용하지 않으며 토큰이 직접 전송될 것으로 예상합니다.

관리자가 베스팅 일정을 취소하면 이미 베스팅된 모든 토큰이 사용자에게 전송됩니다.

# 위협 모델

## 특권 역할 및 행위자

- 계약 소유자(Contract Owner) - 베스팅 일정을 생성, 취소하고 계약을 일시 중지하고 초과 잔액을 인출하며 베스팅된 토큰을 사용자에게 릴리스 할 수 있습니다.
- 베스팅 사용자(Vesting user) - 계약에서 베스팅된 토큰을 청구할 수 있습니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

**A:** 베스팅될 토큰 측면에서의 계약 잔액입니다.

**Q:** 프로토콜/사용자가 돈을 잃을 수 있는 경우는 언제인가요?

**A:** 베스팅 계산이 올바르지 않거나 계약이 DoS 상태가 되는 경우입니다.

**Q:** 공격자가 목표를 달성하는 방법에는 어떤 것이 있나요?

**A:** 다른 사용자의 청구 트랜잭션을 항상 되돌리거나(revert) 베스팅된 토큰 수학의 계산 오류를 악용하는 것입니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

**영향 (Impact)** - 성공적인 공격으로 인한 기술적, 경제적, 평판적 손해

**가능성 (Likelihood)** - 특정 취약점이 발견되고 악용될 확률

**심각도 (Severity)** - 위험의 전반적인 중요성

# 보안 평가 요약

**_검토 커밋 해시_ - [88c1cda0e77d881fffb8a9deab1f65585503d504](https://github.com/moleculeprotocol/token-vesting-contract/tree/88c1cda0e77d881fffb8a9deab1f65585503d504)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `TokenVesting`
- `TokenVestingMerkle`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 0개 문제
- 중간: 4개 문제
- 낮음: 2개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [M-01] | `revoke` 메커니즘은 차단 목록 기능을 구현하는 토큰과 호환되지 않음 | 중간 |
| [M-02] | `createVestingSchedule` 함수의 입력 검증 불충분 | 중간 |
| [M-03] | 계약은 ETH를 받을 수 있지만 인출 기능이 없음 | 중간 |
| [M-04] | 계약이 일시 중지된 경우 사용자는 베스팅된 토큰을 청구할 수 없음 | 중간 |
| [L-01] | `vestingSchedulesIds` 배열 및 `holdersVestingScheduleCount`의 최대 크기 제한 | 낮음 |
| [L-02] | `onlyIfVestingScheduleNotRevoked` 수정자는 주어진 `vestingScheduleId`가 존재하지 않더라도 되돌리지 않음 | 낮음 |

# 상세 발견 사항

# [M-01] `revoke` 메커니즘은 차단 목록 기능을 구현하는 토큰과 호환되지 않음

## 심각도

**영향:**
높음, 프로토콜의 중요한 기능이 작동하지 않기 때문

**가능성:**
낮음, 특별한 유형의 ERC20 토큰을 사용해야 하고 공격자의 주소가 차단 목록에 있어야 하기 때문

## 설명

`USDC` 및 `USDT`와 같은 일부 토큰은 관리자가 제어하는 주소 차단 목록을 구현합니다. 차단된 주소로의 모든 전송은 되돌려집니다(revert). `revoke` 기능은 청구 가능한 베스팅 토큰을 `vestingSchedule`이 있는 주소로 강제 전송하므로 해당 주소에 청구 가능한 잔액이 있고 토큰의 차단 목록에 있는 경우 `revoke`에 대한 모든 호출이 되돌려집니다.

## 권장 사항

`revoke` 시나리오에서 계약 밖으로 토큰을 보낼 때 [Pull over Push](https://fravoll.github.io/solidity-patterns/pull_over_push.html) 패턴을 사용하십시오.

## 논의

**pashov:** 팀이 프로토콜 문서에 해당 토큰이 지원되지 않을 것이라고 나열했으므로 인지됨(Acknowledged).

# [M-02] `createVestingSchedule` 함수의 입력 검증 불충분

## 심각도

**영향:**
높음, 사용자가 토큰을 절대 베스팅하지 못할 수 있기 때문

**가능성:**
낮음, 악의적/침해된 관리자 또는 그 측의 오류가 필요하기 때문

## 설명

`createVestingSchedule` 함수의 입력 인수는 충분히 검증되지 않았습니다. 다음은 몇 가지 문제가 되는 시나리오입니다:

1. `_start`는 이미 지났거나 미래에 너무 먼 타임스탬프일 수 있습니다.
2. `_cliff`가 너무 커서 사용자가 청구할 수 없을 수 있습니다.
3. 1은 `duration`에 유효한 값이며 `!= 0` 확인으로는 충분하지 않습니다.
4. `_slicePeriodSeconds`가 너무 크면 `_computeReleasableAmount`의 수학에서 반올림 오류가 발생합니다.

## 권장 사항

`createVestingSchedule` 메서드의 모든 인수에 대해 합리적인 하한 및 상한을 추가하십시오.

## 논의

**pashov:** [8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f](https://github.com/moleculeprotocol/token-vesting-contract/commit/8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f)에서 수정됨.

# [M-03] 계약은 ETH를 받을 수 있지만 인출 기능이 없음

## 심각도

**영향:**
높음, 가치가 영원히 갇힐 수 있기 때문

**가능성:**
낮음, 누군가 실수로 계약에 ETH를 보내야 하기 때문

## 설명

`TokenVesting` 계약에는 `payable`인 `receive` 및 `fallback` 함수가 있습니다. 누군가 `msg.value != 0`으로 트랜잭션을 보내면 ETH는 인출할 방법 없이 계약에 영원히 갇히게 됩니다.

## 권장 사항

계약에서 ETH 잔액이 어차피 사용되지 않으므로 `receive` 및 `fallback` 함수를 제거하십시오.

## 논의

**pashov:** [8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f](https://github.com/moleculeprotocol/token-vesting-contract/commit/8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f)에서 수정됨.

# [M-04] 계약이 일시 중지된 경우 사용자는 베스팅된 토큰을 청구할 수 없음

## 심각도

**영향:**
높음, 소유자가 사용자가 베스팅된 토큰을 청구할 수 없도록 만들 권한이 있기 때문

**가능성:**
낮음, 악의적이거나 침해된 소유자가 필요하기 때문

## 설명

소유자는 현재 다음과 같은 공격을 실행할 수 있습니다:

1. `paused == true`로 `setPaused`를 호출하여 계약을 일시 중지합니다.
2. 이제 `releaseAvailableTokensForHolder`에 `whenNotPaused` 수정자가 있으므로 모든 사용자 호출이 실패합니다.
3. 그는 영원히 계약 일시 중지를 해제하지 않거나 소유권을 포기할 수도 없습니다.

이것은 계약 소유자가 사용자를 "러그(rug)"할 수 있음을 의미하는 일반적인 중앙 집중화 문제입니다.

## 권장 사항

`releaseAvailableTokensForHolder`에서 `whenNotPaused` 수정자를 제거하여 관리자가 계약을 일시 중지하더라도 사용자가 베스팅된 토큰을 청구할 수 있도록 하십시오.

## 논의

**pashov:** [8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f](https://github.com/moleculeprotocol/token-vesting-contract/commit/8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f)에서 수정됨.

# [L-01] `vestingSchedulesIds` 배열 및 `holdersVestingScheduleCount`의 최대 크기 제한

사용자에 대해 너무 많은 베스팅 일정이 추가되면 `getVestingSchedulesIds` 메서드가 너무 많은 가스를 사용하여 실행할 수 없게 될 수 있습니다(예: 블록 가스 제한 초과). 또한 `releaseAvailableTokensForHolder`에는 `vestingScheduleCount` 횟수만큼 반복하는 `for` 루프가 있어 함수가 DoS 상태가 될 수 있으므로 문제가 될 수 있습니다. 예를 들어 계약에서 생성된 베스팅 일정을 최대 500개까지로 제한하는 등 둘 모두의 최대 크기를 제한하십시오.

## 논의

**pashov:** [8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f](https://github.com/moleculeprotocol/token-vesting-contract/commit/8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f)에서 수정됨.

# [L-02] `onlyIfVestingScheduleNotRevoked` 수정자는 주어진 `vestingScheduleId`가 존재하지 않더라도 되돌리지 않음

베스팅 일정의 기본 `Status`는 어차피 `INITIALIZED`이므로 존재하지 않는 베스팅 일정의 `vestingScheduleId`가 전달되면 수정자가 성공적으로 통과합니다. `vestingSchedules[vestingScheduleId].duration != 0`을 확인하여 `vestingSchedules`가 존재하는지 검증하십시오.

## 논의

**pashov:** [8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f](https://github.com/moleculeprotocol/token-vesting-contract/commit/8f8f786d95ee8db1e3d3ae96e26a86b7e250de0f)에서 수정됨.
