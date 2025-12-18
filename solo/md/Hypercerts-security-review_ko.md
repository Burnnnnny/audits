# 소개

**Hypercerts** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다.

# 프로토콜 개요

**Hypercerts**는 IFS(Impact Funding Systems)가 블록체인에서 효율적으로 구축될 수 있도록 하는 프로토콜입니다. 사용자는 작업에 대한 "인증서"와 같은 ERC1155 준대체불가능(semi-fungible) 토큰을 민팅할 수 있습니다. 이러한 "인증서"는 규칙에 따라 분할 및/또는 전송이 가능하도록 구성될 수 있습니다. Hypercerts의 훌륭한 사용 사례 중 하나는 소급 자금 조달 메커니즘으로, "인증서"가 있으면 미래에 들어오는 소급 자금 지급을 받을 수 있기 때문입니다. 민팅된 Hypercert는 분할(소수점화) 및 병합할 수 있습니다. 분할은 예상 보상의 일부를 다른 당사자에게 주거나 팔고 싶을 때 유용한 기능입니다.

누구나 새로운 Hypercert("클레임"이라고도 함)를 생성할 수 있는 3가지 방법이 있습니다:

1. `mintClaim`을 호출하여 토큰의 모든 `units`을 호출자에게 민팅
2. `mintClaimWithFractions`를 호출하여 토큰을 `fractions`(분수)로 나누고 호출자에게 민팅
3. `createAllowlist`를 호출하여 호출자가 민팅하기 위해 화이트리스트 액세스(Merkle 트리를 통해)가 필요한 ERC1155 토큰을 생성

3번의 기능을 위해 새로운 Hypercert를 민팅하는 다음 메서드가 추가되었습니다:

1. `mintClaimFromAllowlist` - 호출자는 `claimID` 유형 토큰의 `units` 양을 민팅할 권한을 부여하는 `proof`를 제출하여 `account`로 민팅할 수 있습니다.
2. `batchMintClaimsFromAllowlists` - `mintClaimFromAllowlist`와 동일하지만 단일 트랜잭션에서 여러 민트를 위한 것입니다.

그리고 Hypercert/클레임 소유자를 위해 다음 기능이 존재합니다:

1. `splitValue` - 클레임 토큰을 분수로 나눕니다.
2. `mergeValue` - 분수를 하나의 클레임 토큰으로 병합합니다.
3. `burnValue` - 클레임 토큰을 소각합니다.

# 위협 모델

### 시스템 행위자

- 프로토콜 소유자(Protocol owner) - `HypercertMinter` 계약을 업그레이드하고 일시 중지/일시 중지 해제할 수 있으며, 프로토콜 초기화 중에 설정됩니다.
- 클레임 민터(Claim minter) - 새로운 클레임 유형을 생성할 수 있으며, 권한 제어가 없습니다.
- 클레임의 화이트리스트 민터(Whitelisted minter of a claim) - 이미 존재하는 유형의 토큰을 민팅할 수 있으며, Merkle 트리 루트는 유형 생성 시 설정됩니다.
- 유형 생성자(Type creator) - 정책이 `FromCreatorOnly`인 경우 그만이 토큰을 전송할 수 있습니다.
- 분수 소유자(Fraction owner) - 클레임의 분수를 전송, 소각, 분할 및 병합할 수 있습니다.

Q: 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

A: 클레임과 그 분수 소유권은 미래에 (예를 들어) 소급 자금 조달로부터 보상을 받거나 거버넌스에 사용될 수 있기 때문에 가치가 있습니다.

Q: 프로토콜에 일어날 수 있는 최악의 일은 무엇인가요?

1. 클레임의 소유자도 운영자도 아닌 사용자에 의한 클레임 도난/소각
2. 분할 또는 병합을 통해 의도한 것보다 더 많은 units 생성
3. 계약의 무단 업그레이드/일시 중지

### 흥미롭거나 예상치 못한 설계 선택:

`mintClaimFromAllowlist` 메서드는 `msg.sender`가 Merkle 트리에 포함되어 있는지 확인하지만 토큰은 `account` 주소 인수로 민팅됩니다. `msg.sender`가 모든 리프(leaf)에 있어야 하는 `batchMintClaimsFromAllowlists` 기능의 경우도 마찬가지입니다.

1 unit만 있는 토큰을 민팅하면 나중에 분할할 수 없습니다. UI는 민팅 시 100 분수를 권장합니다. 아마도 이것은 스마트 계약 수준에서 최소값으로 강제되어야 할 것입니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

# 보안 평가 요약

**_검토 커밋 해시_ - [73cd850e96d4fd50924640b838bcc0f6905b0abf](https://github.com/Network-Goods/hypercerts-protocol/commits/73cd850e96d4fd50924640b838bcc0f6905b0abf)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `AllowlistMinter`
- `HypercertMinter`
- `SemiFungible1155`
- `Upgradeable1155`
- `interfaces/**`
- `libs/**`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 2개 문제
- 중간: 2개 문제
- 낮음: 4개 문제
- 정보성: 12개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | 사용자가 토큰을 `tokenID`에 보유된 `units`보다 더 많은 분수로 나눌 수 있음 | 치명적 |
| [H-01] | 토큰 인덱스가 최신이 아닐 때 `splitValue`를 호출하면 다른 클레임의 저장소를 덮어씀 | 높음 |
| [M-01] | 사용되지 않는 함수 매개변수로 인해 사용자 측에서 잘못된 가정을 할 수 있음 | 중간 |
| [M-02] | 입력 및 데이터 검증이 누락되었거나 불완전함 | 중간 |
| [L-01] | 주석에 부정확하고 위험할 수 있는 가정이 있음 | 낮음 |
| [L-02] | 이벤트 누락 및 잘못된 이벤트 인수 | 낮음 |
| [L-03] | 역할 전송에 2단계 패턴 선호 | 낮음 |
| [L-04] | 계약 일시 중지 가능성 및 업그레이드 가능성은 다중 서명(multi-sig) 또는 거버넌스 계정 뒤에 있어야 함 | 낮음 |
| [I-01] | 현재 코드에서 전송 후크가 필요하지 않음 | 정보성 |
| [I-02] | 사용되지 않는 import, 지역 변수 및 사용자 지정 오류 | 정보성 |
| [I-03] | 상속을 사용하는 대신 하나의 스마트 계약으로 로직 병합 | 정보성 |
| [I-04] | 잘못된 사용자 지정 오류가 발생함 | 정보성 |
| [I-05] | 주석의 오타 | 정보성 |
| [I-06] | 인터페이스 상속 메서드에 대한 `override` 키워드 누락 | 정보성 |
| [I-07] | 비트 시프트 연산이 불필요하게 복잡함 | 정보성 |
| [I-08] | 코드의 중복 확인 | 정보성 |
| [I-09] | 불완전하거나 잘못된 NatSpec 문서 | 정보성 |
| [I-10] | 오해의 소지가 있는 변수 이름 | 정보성 |
| [I-11] | Solidity safe pragma 모범 사례가 사용되지 않음 | 정보성 |
| [I-12] | 코드베이스의 매직 넘버 | 정보성 |

# 상세 발견 사항

# [C-01] 사용자가 토큰을 `tokenID`에 보유된 `units`보다 더 많은 분수로 나눌 수 있음

## 심각도

**영향**
높음, 중요한 프로토콜 불변성을 위반하기 때문

**가능성**
높음, 이러한 유형의 문제는 흔하고 쉽게 악용될 수 있기 때문

## 설명

`SemiFungible1155`의 `_splitValue` 메서드는 Checks-Effects-Interactions 패턴을 따르지 않으며 OpenZeppelin의 ERC1155 구현에서 `_mintBatch`를 호출하는데, 이는 실제로 안전 확인으로 수신자 계정 훅(hook) 호출을 수행합니다. 이 호출은 `_splitValue` 메서드에 재진입(reenter)할 수 있어 안전하지 않으며, `tokenValues[_tokenID]`가 아직 업데이트되지 않았기 때문에 토큰을 더 많은 분수로 다시 나눈 다음 엄청난 양의 토큰이 민팅될 때까지 반복할 수 있습니다.

## 권장 사항

Checks-Effects-Interactions 패턴을 따르십시오.

```diff
-_mintBatch(_account, toIDs, amounts, "");
-
-tokenValues[_tokenID] = valueLeft;
+tokenValues[_tokenID] = valueLeft;
+
+_mintBatch(_account, toIDs, amounts, "");
```

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [H-01] 토큰 인덱스가 최신이 아닐 때 `splitValue`를 호출하면 다른 클레임의 저장소를 덮어씀

## 심각도

**영향:**
높음, 사용자의 조치 없이 계정의 unit 손실로 이어질 수 있기 때문

**가능성:**
중간, 최신 인덱스가 아닌 토큰에서만 발생할 수 있기 때문

## 설명

여기서 `_splitValue`의 로직에 결함이 있습니다:

```solidity
uint256 currentID = _tokenID;
...
toIDs[i] = ++currentID;
...
for (uint256 i; i < len; ) {
    valueLeft -= values[i];

    tokenValues[toIDs[i]] = values[i];

    unchecked {
        ++i;
    }
}
...
_mintBatch(_account, toIDs, amounts, "");
```

다음 시나리오를 살펴보겠습니다:

1. 앨리스는allowlist를 통해 토큰 1, 10 units를 민팅합니다.
2. 밥은 allowlist를 통해 토큰 2, 100 units를 민팅합니다.
3. 앨리스는 토큰 1에 대해 `splitValue`를 호출하여 2개의 새 토큰, 각각 5 units로 나눕니다.

이제 `tokenValues[toIDs[i]] = values[i]`를 갖게 되는데, 여기서 `toIDs[i]`는 `++currentID`인 2이고 `values[i]`는 5이므로, 이제 `tokenValues[2] = 5`가 되어 밥의 `tokenValues`를 덮어씁니다. 또한 나중에 `_mintBatch`가 밥의 토큰 ID를 토큰 ID로 하여 호출되므로 분할된 토큰 중 일부는 밥의 토큰 유형이 됩니다.

## 권장 사항

코드를 다음과 같이 변경하십시오:

```diff
- maxIndex[_typeID] += len;
...
- toIDs[i] = ++currentID;
+ toIDs[i] = _typeID + ++maxIndex[typeID];
```

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [M-01] 사용되지 않는 함수 매개변수로 인해 사용자 측에서 잘못된 가정을 할 수 있음

## 심각도

**영향:**
낮음, 호출자가 여전히 민팅된 토큰을 제어하기 때문

**가능성:**
높음, 사용자가 해당 매개변수에 값을 제공하고 사용법에 대한 가정이 항상 거짓이 되기 때문

## 설명

`mintClaimWithFractions`의 `units` 매개변수는 이벤트 배출(emission)에서만 사용됩니다. 실제로 `fractions.length` 수의 분수가 민팅되므로 이는 오해의 소지가 있습니다. `units != fractions.length`이면 사용자에게 예상치 못한 결과가 발생할 수 있습니다. `mintClaim` 및 `mintClaimWithFractions`의 `account` 매개변수에서도 동일한 문제가 있습니다. 메서드에서 사용되지 않으며 실제로 `msg.sender`는 토큰이 민팅되는 계정이며 토큰 생성자로 설정됩니다. 다시 말해 `account != msg.sender`인 경우 사용자 입장에서는 예상치 못한 일이며, 최상의 시나리오에서는 UX가 좋지 않지만 최악의 경우 `account` 주소가 받은 값에 대한 잘못된 가정으로 이어질 수 있습니다.

## 권장 사항

`mintClaimWithFractions`에서 `units` 매개변수를 제거하고 `mintClaim` 및 `mintClaimWithFractions`의 `_mintValue` 호출에서 `msg.sender` 대신 `account`를 사용하십시오.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [M-02] 입력 및 데이터 검증이 누락되었거나 불완전함

## 심각도

**영향:**
높음, 경우에 따라 DoS 및 예상치 못한 동작으로 이어질 수 있기 때문

**가능성:**
낮음, 악의적인 사용자 또는 사용자 측의 큰 오류가 필요하기 때문

## 설명

여러 메서드에서 입력/데이터 검증이 누락되었거나 불완전합니다.

1. `SemiFungible1155`의 `splitValue` 메서드는 `maxIndex[_typeID] += len;` 코드를 가지고 있으므로 인덱스에 대한 `notMaxItem` 확인도 수행해야 합니다.
2. `createAllowlist` 메서드는 `units` 인수를 허용하며, 이는 allowlist를 통해 민팅할 수 있는 최대 units이어야 합니다. 이는 allowlist에서 클레임을 민팅할 때 확인하여 강제해야 합니다.
3. `AllowlistMinter`의 `_createAllowlist`는 `merkleRoot == ""`일 때 되돌려야(revert) 합니다.
4. `SemiFungible1155`의 `_mintClaim` 및 `_batchMintClaims` 메서드는 각각 `_units == 0` 또는 `_units[i] == 0`일 때 되돌려야 합니다.

## 논의

**pashov**: 클라이언트가 문제를 부분적으로 수정했습니다.

## 권장 사항

모든 입력 및 로직에 대해 언급된 확인을 추가하십시오.

# [L-01] 주석에 부정확하고 위험할 수 있는 가정이 있음

`SemiFungible1155` 끝에 있는 주석은 상수 값이 계약 저장소에 저장된다고 가정하지만 사실이 아닙니다. 상수(constant)와 불변(immutable) 값 모두 계약 저장소에 저장되지 않습니다. 주석을 업데이트하고 스마트 계약 저장소의 작동 방식을 이해하여 나중에 계약을 업그레이드할 때 문제가 발생하지 않도록 하십시오.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [L-02] 이벤트 누락 및 잘못된 이벤트 인수

`SemiFungible1155`의 `_mergeValue` 메서드는 이벤트를 방출하지 않지만 `_splitValue`는 방출합니다. 오프체인 모니터링을 위해 이벤트를 방출하는 것을 고려하십시오. 또한 `_createTokenType`의 `TransferSingle` 이벤트 방출은 `amount` 인수에 대해 값 1을 갖지만 실제로는 토큰을 전송하거나 민팅하지 않으므로 값은 0이어야 합니다.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [L-03] 역할 전송에 2단계 패턴 선호

`Upgradeable1155` 계약은 소유권 이전을 위해 단일 단계 패턴을 사용하는 `OwnableUpgradeable`을 상속합니다. 2단계 소유권 이전 패턴을 사용하는 것이 모범 사례입니다. 즉, 소유권 이전이 "대기(pending)" 상태가 되고 새 소유자가 새 권한을 청구해야 하며, 그렇지 않으면 이전 소유자가 여전히 계약을 제어합니다. 2단계 접근 방식을 사용하는 것을 고려하십시오.

## 논의

**pashov**: 클라이언트가 문제를 인지했습니다.

# [L-04] 계약 일시 중지 가능성 및 업그레이드 가능성은 다중 서명(multi-sig) 또는 거버넌스 계정 뒤에 있어야 함

침해되거나 악의적인 소유자는 `pause`를 호출한 다음 `renounceOwnership`을 호출하여 일시 중지 가능성을 기반으로 프로토콜에 대한 DoS 공격을 실행할 수 있습니다. 문제는 업그레이드 가능성으로 인해 공격 표면이 더 넓어집니다. 소유자는 언제든지 임의의 코드로 계약을 업그레이드할 수 있습니다. 계약이 한동안 라이브 상태가 된 후 다중 서명 또는 거버넌스를 프로토콜 소유자로 사용하거나 Timelock 스마트 계약을 사용하는 것이 좋습니다.

## 논의

**pashov**: 클라이언트는 이 방향으로 나아가기 위한 조치를 취하고 있습니다.

# [I-01] 현재 코드에서 전송 후크가 필요하지 않음

`SemiFungible1155`의 `_beforeTokenTransfer` 후크는 기본 유형 토큰이 전송되는지 여부만 확인하지만 현재 상태의 시스템은 실제로 그러한 토큰을 민팅하지 않으므로 이 확인은 필요하지 않으며 가스만 낭비합니다. `SemiFungible1155`에서 `_beforeTokenTransfer` 후크 무효화(override)를 제거하십시오.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-02] 사용되지 않는 import, 지역 변수 및 사용자 지정 오류

`SemiFungible1155`의 `IERC1155ReceiverUpgradeable` import는 실제로 사용되지 않으므로 제거해야 하며, `SemiFungible1155::mergeValue`의 `_typeID` 지역 변수, `ToZeroAddress`, `MaxValue` 및 `FractionalBurn` 사용자 지정 오류도 마찬가지입니다.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-03] 상속을 사용하는 대신 하나의 스마트 계약으로 로직 병합

`SemiFungible1155` 계약은 `Upgradeable1155`를 상속하지만 로직이 매우 결합되어 있고 `Upgradeable1155`가 다른 계약에서 상속되지 않으므로 자체 추상화가 필요하지 않습니다. 두 계약을 하나로 병합하고 현재 사용되는 두 이름이 `ERC1155` 표준과 너무 가까우므로 좋은 이름을 부여하십시오.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-04] 잘못된 사용자 지정 오류가 발생함

`AllowlistMinter::_processClaim`의 코드는 리프(leaf)가 이미 청구되었을 때 `DuplicateEntry`를 던집니다. `AlreadyClaimed` 사용자 지정 오류도 함께 던지십시오. 또한 거기서 `node` 지역 변수의 이름을 `leaf`로 바꾸는 것을 고려하십시오.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-05] 주석의 오타

`AlloslistMinter` -> `AllowlistMinter`

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-06] 인터페이스 상속 메서드에 대한 `override` 키워드 누락

`HypercertMinter` 계약은 `IHypercertToken` 인터페이스를 상속하고 해당 메서드를 구현하지만 재정의된 메서드에 `override` 키워드가 누락되었습니다. 모범 사례 및 컴파일러 확인을 위해 해당 키워드에 키워드를 추가하십시오.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-07] 비트 시프트 연산이 불필요하게 복잡함

단순성을 위해 다음과 같이 변경하는 것이 좋습니다:

```diff
-    uint256 internal constant TYPE_MASK = uint256(uint128(int128(~0))) << 128;
+    uint256 internal constant TYPE_MASK = type(uint256).max << 128;

    /// @dev Bitmask used to expose only lower 128 bits of uint256
-    uint256 internal constant NF_INDEX_MASK = uint128(int128(~0));
+    uint256 internal constant NF_INDEX_MASK = type(uint256).max >> 128;
```

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-08] 코드의 중복 확인

`SemiFungible1155`의 `_beforeValueTransfer` 후크에는 항상 `true`로 평가되는 `getBaseType(_to) > 0` 확인이 있으므로 제거할 수 있습니다.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-09] 불완전하거나 잘못된 NatSpec 문서

외부 메서드의 NatSpec 문서는 불완전합니다. `@param`, `@return` 및 프로토콜 기능에 대한 기타 설명 문서가 누락되었습니다. 또한 `IAllowlist`의 NatSpec 일부는 `IHypercertToken`에서 복사하여 붙여넣었으며 `AllowlistMinter`도 마찬가지입니다. 사용자, 개발자 및 감사자에게 도움이 될 각 메서드 및 계약에 대한 설명 문서를 작성하십시오.

## 논의

**pashov**: 클라이언트는 아직 문제를 수정하지 않았지만 코드를 더 잘 문서화하는 데 더 높은 우선순위를 두고 있습니다.

# [I-10] 오해의 소지가 있는 변수 이름

`mintClaim`, `mintClaimWithFractions` 및 `createAllowlist` 메서드에서 지역 변수 `claimID`는 실제로 `typeID` 값을 보유하므로 오해의 소지가 있는 이름이 있습니다 - 세 메서드에서 `typeID`로 이름을 바꾸십시오.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-11] Solidity safe pragma 모범 사례가 사용되지 않음

매번 Solidity 코드를 동일한 바이트코드로 결정론적으로 컴파일하기 위해 항상 안정적인 pragma를 사용하십시오. 프로젝트는 현재 floatable 버전을 사용하고 있습니다.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.

# [I-12] 코드베이스의 매직 넘버

`SemiFungible1155`에는 다음 코드가 있습니다:

```solidity
if (_values.length > 253 || _values.length < 2) revert Errors.ArraySize();
```

`253` 값을 잘 명명된 상수로 추출하여 코드에서 숫자의 의도가 명확해지도록 하십시오.

## 논의

**pashov**: 클라이언트가 문제를 수정했습니다.
