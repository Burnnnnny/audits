# 소개

**Ambire** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. 그의 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Ambire** 소개

**첫 번째 보안 검토에서 복사됨**

Ambire는 스마트 지갑 프로토콜입니다. 사용자는 자신 또는 "권한(privileges)"을 가진 다른 주소가 제어하는 지갑(계정)을 갖습니다. 사용자는 트랜잭션 번들을 오프체인에서 서명할 수 있으며 누구나 온체인에서 이를 실행할 수 있습니다. EIP712, Schnorr, Multisig 등 다양한 서명 체계가 허용됩니다. 이 프로토콜은 반사실적(counterfactual) 방식으로 작동하며, 이는 사용자 지갑이 첫 번째 트랜잭션에서만 배포됨을 의미합니다. 실제 배포는 지갑 스마트 계약에 대한 EIP1167 최소 프록시입니다.

**계속됨**

`Ambire` 프로토콜은 "외부 서명 검증기(external signature validator)" 옵션을 추가하여 서명 검증기 옵션을 확장했습니다. 그러한 옵션 중 하나는 `DKIMRecoverySigValidator`이며, 이는 기본적으로 이메일을 사용하여 스마트 지갑에 대한 액세스를 복구하는 방법입니다. 보조 키와 이메일에 대한 액세스 및 제어 권한이 있지만 기본 키를 분실한 경우 계정에 대한 액세스를 즉시 복구할 수 있습니다. 둘 중 하나에 대한 액세스/제어 권한을 잃은 경우에도 복구를 대기열에 넣을 수 있지만 타임락(timelock)이 지날 때까지 기다려야 합니다.

## 관찰 사항

`DKIMRecoverySigValidator`가 사용하는 데이터는 프론트엔드 서비스에 의해 정규화(canonized)됩니다.

DKIM 검증 로직(DNSSEC)의 일부는 이더리움 메인넷의 ENS 애플리케이션에서 포크되었습니다.

`DKIMRecoverySigValidator`는 싱글톤으로 배포되며 `Ambire`는 `authorizedToSubmit` 및 `authorizedToRevoke` 값을 제어합니다.

DNSSEC를 지원하지 않는 이메일 제공업체의 경우 "브리지" 기능이 추가되었습니다. 이는 기본적으로 Ambire가 DNS 레코드의 진위 여부를 확인하는 "DNSSEC 브리지" 도메인을 지원함을 의미합니다. 이는 예를 들어 Gmail이 DNSSEC를 지원하지 않는다는 사실에 기반한 중앙 집중화 절충안입니다.

## 특권 역할 및 행위자

- 외부 서명 검증기 (External Signature Validator) - 서명을 검증하는 특별한 접근 방식(예: DKIM)을 가진 스마트 계약
- DNSSEC 오라클 (DNSSEC Oracle) - 서명된 DNS 레코드 배열이 신뢰 체인을 나타내는지 확인
- `authorizedToSubmit` - 복구에 사용되는 새로운 DKIM 키를 추가할 수 있는 주소
- `authorizedToRevoke` - 복구에 사용되는 DKIM 키를 제거할 수 있는 주소

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

**영향 (Impact)** - 성공적인 공격의 기술적, 경제적, 평판적 손상

**가능성 (Likelihood)** - 특정 취약점이 발견되고 악용될 확률

**심각도 (Severity)** - 위험의 전반적인 임계도

# 보안 평가 요약

**_검토 커밋 해시_ - [8521c4e68b147c0002b2e2c337178e4f5d23d594](https://github.com/AmbireTech/ambire-common/tree/8521c4e68b147c0002b2e2c337178e4f5d23d594)**

**_수정 검토 커밋 해시_ - [20aae8ec666d1341dc1462962ea2ac50bf2edd6f](https://github.com/AmbireTech/ambire-common/tree/20aae8ec666d1341dc1462962ea2ac50bf2edd6f)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `AmbireAccount`
- `DKIMRecoverySigValidator`

---

# 발견 사항 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [C-01] | `SigMode.OnlyDKIM` 유형의 복구는 누구나 되돌릴 수 있음 | 치명적 | 수정됨 |
| [C-02] | 프론트 러닝으로 복구를 차단할 수 있음 | 치명적 | 수정됨 |
| [C-03] | 누구나 단일 서명 DKIM 복구를 차단할 수 있음 | 치명적 | 수정됨 |
| [H-01] | `SigMode.OnlySecond` 유형의 복구는 끝없이 재생될 수 있음 | 높음 | 수정됨 |
| [M-01] | 불충분한 입력 검증 | 중간 | 인지됨 |
| [L-01] | 제거된 DKIM 키의 `dateRemoved` 필드를 재설정할 수 있음 | 낮음 | 수정됨 |
| [L-02] | `from`과 마찬가지로 `to` 이메일 검증 | 낮음 | 수정됨 |

# 상세 발견 사항

# [C-01] `SigMode.OnlyDKIM` 유형의 복구는 누구나 되돌릴 수 있음

## 심각도

**영향:**
높음, 복구가 되돌려지고 사용자는 여전히 스마트 지갑에 대한 액세스 권한을 얻을 수 없기 때문입니다.

**가능성:**
높음, `SigMode.OnlyDKIM`이 복구에 사용될 때 누구나 이 공격을 실행할 수 있기 때문입니다.

## 설명

`DKIMRecoverySigValidator::validateSig`의 복구 작동 방식은 복구가 실행될 때 `sigMeta.newAddressToSet` 주소에 `sigMeta.newPrivilegeValue` 권한이 부여되는 것입니다. `AmbireAccount`에서 주소에 0이 아닌 권한이 있으면 스마트 지갑에서 모든 것을 실행할 수 있어 소유자가 됩니다. 문제는 `sigMeta.newPrivilegeValue`가 검증되지 않는다는 것입니다.

사용자가 `SigMode.OnlyDKIM` 유형의 복구를 사용하여 주소 "X"에 권한 "1"을 갖도록 복구 트랜잭션을 보내면 타임락이 설정되고, 그 후 사용자가 동일한 트랜잭션을 다시 보내면 "X" 주소는 권한 "1"을 갖게 됩니다. 이제 악의적인 공격자가 사용자가 사용한 것과 동일한 트랜잭션 페이로드를 사용하지만 `DKIMRecoverySigValidator::validateSig`의 `calls` 인수에 있는 첫 번째 `Transaction`의 `data`만 변경하고 `sigMeta.newPrivilegeValue`를 0으로 설정하면, "X" 주소가 더 이상 권한을 갖지 않도록 설정하는 새로운 복구가 시작될 수 있습니다. `sigMeta`는 `identifier` 계산의 일부이며 이제 다르기 때문에 복구의 `identifier`가 달라져 작동하게 됩니다.

이 공격의 변형이 있습니다. 악의적인 사용자는 일반 사용자의 `SigMode.OnlyDKIM` 트랜잭션을 백런(back-run)하여 사용자가 타임락을 실행한 직후에 자신의 타임락을 실행할 수 있습니다.

[Github gist 링크](https://gist.github.com/pashov/f154ecb2be94e672324e9df122e15617)에서 공격이 실제로 실행되는 개념 증명 단위 테스트를 확인할 수 있습니다 (`DKIMTest.ts`의 `'DKIM sigMode OnlyDKIM'` 제품군에 넣어서 실행할 수 있습니다).

## 권장 사항

복구의 경우 `sigMeta.newPrivilegeValue == 1`만 허용하거나 `identifier` 구성에서 `sigMeta.newPrivilegeValue`를 제거하십시오.

# [C-02] 프론트 러닝으로 복구를 차단할 수 있음

## 심각도

**영향:**
높음, 복구 기능을 사용할 수 없게 되기 때문입니다.

**가능성:**
높음, 모든 복구에 대해 이러한 공격을 실행하기 위한 전제 조건이 없기 때문입니다.

## 설명

`AmbireAccount`에서 `DKIMRecoverySigValidator::validateSig`로의 각 호출은 동일한 인수로 메서드를 호출하여 프론트 러닝 할 수 있으며, 이는 복구 페이로드에 대한 `recoveries` 매핑 값을 `true`로 설정하여 다음 확인으로 인해 실제 트랜잭션을 되돌립니다.

```solidity
require(!recoveries[identifier], 'recovery already done');
```

[Github gist 링크](https://gist.github.com/pashov/6f85756cca9aedfb059f082e71e138c7)에서 공격이 실제로 실행되는 개념 증명 단위 테스트를 확인할 수 있습니다 (`DKIMTest.ts`의 `'DKIM sigMode Both'` 제품군에 넣어서 실행할 수 있습니다).

## 권장 사항

`validateSig`에서 `accountAddress` 매개변수 대신 `msg.sender`를 사용하십시오. 이렇게 하면 누구도 `accountAddress`를 `AmbireAccount` 주소로 보낼 수 없습니다.

# [C-03] 누구나 단일 서명 DKIM 복구를 차단할 수 있음

## 심각도

**영향:**
높음, 사용자가 지갑에 대한 액세스 권한을 복구할 수 없기 때문입니다.

**가능성:**
높음, 누구나 복구를 프론트 러닝 하여 이 공격을 실행할 수 있기 때문입니다.

## 설명

`DKIMRecoverySigValidator`에는 단일 서명 복구 메커니즘이 있습니다. 문제는 이를 사용하는 경우 복구에 타임락이 있어야 한다는 것인데, 이는 미리 정의된 숫자(`AccInfo.onlyOneSigTimelock`에 설정되어야 함)입니다. 타임락이 만료될 때까지 지갑 액세스를 복구할 수 없습니다. 여기서 문제는 `identifier`(일부 복구 데이터를 해싱하여 구축됨)에 대한 잠금을 실제로 설정하는 `checkTimelock` 메서드가 `public`이어서 누구나 호출할 수 있다는 것입니다. 즉, 누구나 `validateSig` 호출을 `checkTimelock`으로 프론트 러닝 하여 복구 잠금 해제까지의 `time`을 엄청나게 설정하여 기본적으로 복구를 차단할 수 있습니다.

[Github gist 링크](https://gist.github.com/pashov/8a9956d918045c73425177600c497ac2)에서 공격이 실제로 실행되는 개념 증명 단위 테스트를 확인할 수 있습니다 (`DKIMTest.ts`의 `'DKIM sigMode OnlySecond with a timelock of 2 minutes'` 제품군에 넣어서 실행할 수 있습니다).

## 권장 사항

`checkTimelock` 메서드를 `public`에서 `private`으로 변경하여 `validateSig`에 의해서만 내부적으로 호출되도록 하거나 메서드를 인라인(inline) 하십시오.

# [H-01] `SigMode.OnlySecond` 유형의 복구는 끝없이 재생될 수 있음

## 심각도

**영향:**
높음, 지갑 액세스 권한 손실을 초래할 수 있기 때문입니다.

**가능성:**
중간, 특정 조건이 필요하지만 지갑을 수년 동안 사용한 후에도 발생할 수 있기 때문입니다.

## 설명

`DKIMRecoverySigValidator`의 `recoveries` 매핑은 `recovery`가 이미 실행되었는지 확인하는 방법으로 사용되므로 복구 재생(replay)을 차단하는 방법입니다. 문제점은 해당 매핑의 키가 약간 변경될 수 있는 복구 페이로드의 필드(예: `string` 또는 `bytes` 유형 필드에 임의의 문자를 추가하여 변경 가능, 예: `SignatureMeta.canonizedHeaders` 또는 `SignatureMeta.key.pubKeyModulus`)에서 계산되므로 다른 모든 값은 동일한 복구 페이로드를 실행할 수 있다는 것입니다.

구체적인 문제는 다음과 같습니다:

```solidity
bytes32 identifier = keccak256(abi.encode(accountAddr, data, sigMeta));
```

`recoveries` 매핑의 키로 사용되는 `identifier`는 `string canonizedHeaders` 필드가 있는 `SignatureMeta sigMeta`를 사용하고 있습니다. `SigMode.OnlySecond`가 사용될 때 `canonizedHeaders`에는 아무런 검증이 없으므로 약간 변경될 수 있으며, 다른 모든 값은 동일하고 여전히 유효함에도 불구하고 `identifier`는 달라집니다. 즉, 모든 `SigMode.OnlySecond` 복구는 끝없이 재생될 수 있습니다. 원하는 만큼 영원히.

`SignatureMeta.newKeyToSet` 값은 변경할 수 없으므로(`SigMode.onlySecond`가 사용될 때 검증됨), 언급된 모든 재생은 실행될 때마다 동일한 `newKeyToSet` 주소에 액세스 권한을 부여하게 되어 문제가 완화됩니다. 하지만 다음 시나리오를 살펴보겠습니다:

1. Alice는 `AmbireAccount`에 대한 액세스 권한을 잃어버려서 `SigMode.OnlySecond`와 함께 `DKIMRecoverySigValidator`를 사용하여 복구를 실행하여 `SignatureMeta.newKeyToSet`에 설정한 주소에 `AmbireAccount`에 대한 제어 권한을 부여합니다.
2. 1년이 지나고 Alice는 키를 교체(rotate)하고 이제 이전 키를 버립니다.
3. Bob은 `canonizedHeaders` 필드를 약간 변경하여 이전 복구를 재생합니다. 이로 인해 동일한 주소(Alice는 이제 제어하지 않음)가 `AmbireAccount`를 제어하게 됩니다.
4. 이제 Alice는 지갑에 대한 제어 권한을 잃었고, 복구가 이전에 설정된 `SignatureMeta.newKeyToSet`에 대해서만 작동하도록 구성되어 있기 때문에 지갑을 복구할 수 없습니다.

## 권장 사항

`recoveries` 매핑에 대한 `identifier` 키를 만들 때, 복구 재생이 불가능하도록 모든 코드 경로에서 검증된 `SignatureMeta`의 필드만 사용하십시오.

# [M-01] 불충분한 입력 검증

## 심각도

**영향:**
높음, 영구적으로 차단된 복구 또는 지갑 액세스 손실을 초래할 수 있기 때문입니다.

**가능성:**
낮음, 사용자가 복구를 구성할 때 오류를 범해야 하기 때문입니다.

## 설명

`DKIMRecoverySigValidator` 계약은 값이 검증되지 않은 필드가 있는 `AccInfo` 구조체를 사용합니다. 문제가 되는 필드는 다음과 같습니다:

- `secondaryKey`는 0 주소일 수 있으며, 이로 인해 `AmbireAccount` 지갑에 대한 액세스 권한을 잃을 수 있습니다.
- `waitUntilAcceptAdded`는 시간 값이며 너무 클 수 있어 DKIM 키를 추가할 수 없게 될 수 있습니다.
- `waitUntilAcceptRemoved`는 시간 값이며 너무 클 수 있어 DKIM 키를 제거할 수 없게 될 수 있습니다.
- `onlyOneSigTimelock`은 시간 값이며 너무 클 수 있어 복구가 영구적으로 잠길 수 있습니다.

## 권장 사항

시간 값에 합리적인 상한선을 추가하고 `secondaryKey != address(0)`인지 확인하는 검사를 추가하십시오.

# [L-01] 제거된 DKIM 키의 `dateRemoved` 필드를 재설정할 수 있음

`DKIMRecoverySigValidator`의 `removeDKIMKey` 메서드는 주어진 키가 이미 제거되었는지 확인하지 않습니다. `authorizedToRevoke` 계정은 동일한 키에 대해 메서드를 원하는 만큼 여러 번 호출할 수 있으며, 이로 인해 `dateRemoved` 속성이 매번 `block.timestamp`로 재설정됩니다.

# [L-02] `from`과 마찬가지로 `to` 이메일 검증

`DKIMRecoverySigValidator::_verifyHeaders`에서 `from` 메일은 "fake@gmail.com@gmail.com"과 같은 이메일로부터 보호하는 방식으로 검증됩니다. `to` 메일에 대해서는 이 확인이 수행되지 않으며, 이메일 뒤에 올 것으로 예상되는 특수 문자까지 문자열을 슬라이싱하고 이를 `toHeader`와 비교하여 추가할 수 있습니다.
