# 소개

**Metalabel** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다.

# 프로토콜 개요

Metalabel은 릴리스 클럽 프로토콜입니다. 비슷한 관심사를 가진 사람들의 그룹이 함께 모여 협력자로서 작품을 출시(drop)할 수 있습니다. 이 프로토콜은 크리에이터가 "메타라벨"이 받은 경제적 보상을 그들 간에 나눌 수 있도록 합니다. 다음과 같은 여러 기술적 추상화가 있습니다:

- 리소스(Resources)

  - 컬렉션(Collection) - 토큰(레코드)을 민팅하는 ERC721 계약
  - 스플릿(Split) - 다양한 기여자에게 서로 다른 비율 할당을 적용하는 지불 로직 계약
  - 워터폴(Waterfall) - 다른 당사자에게 지불하기 전에 한 당사자에게 특정 금액을 지불하도록 강제하는 지불 로직 계약

- 계정(Accounts) - 사용자가 프로토콜의 기능을 잠금 해제하려면 계정을 등록해야 합니다(처음에는 계정 생성이 통제되지만 나중에 권한이 필요 없어짐(permissionless)).

- 노드(Node) - 레코드와 리소스를 그룹화하고 소유자 계정이나 컨트롤러 주소가 관리할 수 있도록 하는 소유 가능한 추상화

- 엔진(Engine) - 새로운 컬렉션을 드롭하기 위한 계약으로, ERC721의 민팅, 로열티, 렌더링(`tokenURI`)을 관리합니다.

프로토콜은 100% 코드 커버리지(라인, 분기, 함수)를 가지고 있어 잘 테스트 되었습니다.

[추가 문서](https://metalabel.notion.site/Metalabel-Protocol-Walkthrough-2080c68cc6f242ebb7813b1a9236cab1)

# 위협 모델

### 시스템 행위자

- 계정(Account) - 새 노드를 생성할 수 있습니다.
- 노드 소유자(Node owner) - 노드를 관리(컬렉션, 민트, 로열티 및 가격 구성)하고 컨트롤러를 추가할 수 있습니다.
- 컨트롤러(Controller) - 노드를 관리할 수 있지만 컨트롤러를 추가할 수는 없습니다.
- 민트 권한(Mint Authority) - 허가된 시퀀스를 민팅할 수 있습니다.

### 외부 함수:

- `AccountRegistry` 모든 메서드 - 누구나 계정을 등록하기 위해 호출 가능합니다(`owner`가 설정된 경우 제외).
- `NodeRegistry::createNode` - 계정을 생성한 누구나 호출 가능합니다.
- `DropEngine::mint` - 누구나 호출 가능합니다(시퀀스에 대해 `mintAuthorities` 매핑이 설정된 경우 제외).

Q: 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

A: ERC721 토큰 민트는 ETH로 지불될 수 있으므로 ETH 가치와 ERC721 토큰 자체입니다.

Q: 프로토콜에 일어날 수 있는 최악의 일은 무엇인가요?

1. 노드 소유권 도난
2. 공격자가 민트 지불 수신자로 자신을 설정
3. 무료 또는 무제한 민트를 허용하도록 `mint` 기능 악용

### 흥미롭거나 예상치 못한 설계 선택:

그룹 노드의 소유자는 그룹의 다른 모든 노드에 대해 여러 컨트롤러를 설정할 수 있습니다.

그룹 노드의 컨트롤러는 그룹의 다른 모든 노드를 관리할 수 있습니다.

컨트롤러는 스마트 계약일 수 있으며 계정도 스마트 계약일 수 있습니다.

노드의 컨트롤러는 소유자와 동일한 권한을 가집니다(컨트롤러 추가 제외).

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

# 보안 평가 요약

**_검토 커밋 해시_ - [effe2e89996d6809c5ec6c6da10c663246f91fc1](https://github.com/metalabel/metalabel-contracts-v1/tree/effe2e89996d6809c5ec6c6da10c663246f91fc1)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `AccountRegistry`
- `Collection`
- `CollectionFactory`
- `DropEngine`
- `NodeRegistry`
- `Resource`
- `ResourceFactory`
- `SplitFactory`
- `WaterfallFactory`
- `interfaces/**`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 높음: 0개 문제
- 중간: 4개 문제
- 낮음: 2개 문제
- 정보성: 8개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [M-01] | `configureSequence`에 대한 입력 데이터 검증 불충분 | 중간 |
| [M-02] | `address payable`의 `transfer` 함수 사용은 권장되지 않음 | 중간 |
| [M-03] | 단일 단계 방식으로 수행되는 역할 이전 작업은 위험함 | 중간 |
| [M-04] | ERC721 토큰을 처리할 수 없는 스마트 계약 주소로 민팅된 레코드는 영원히 갇힘 | 중간 |
| [L-01] | 주소에 계정이 없어도 `resolveId`는 0 ID를 반환함 | 낮음 |
| [L-02] | 일관성 없는 `tokenData.owner` 검증 | 낮음 |
| [I-01] | 함수 매개변수에 더 추상적인 이름 사용 | 정보성 |
| [I-02] | `NodeData.nodeType`은 존재 확인 용도로만 사용됨 | 정보성 |
| [I-03] | NatSpec 문서가 불완전함 | 정보성 |
| [I-04] | 사용되지 않는 import | 정보성 |
| [I-05] | `WaterfallFactory`의 복사-붙여넣기 주석 | 정보성 |
| [I-06] | `override` 키워드 누락 | 정보성 |
| [I-07] | 안전한 pragma 문 사용 | 정보성 |
| [I-08] | 오타 및 문법 오류 | 정보성 |

# 상세 발견 사항

# [M-01] `configureSequence`에 대한 입력 데이터 검증 불충분

## 심각도

**영향:**
높음, 일부는 DoS 또는 너무 큰 로열티 지불로 이어질 수 있기 때문

**가능성:**
낮음, 구성 오류 또는 악의적인 행위자가 필요하기 때문

## 설명

노드에 대해 승인된 주소는 `Collection::configureSequence`를 호출할 수 있는데, 입력의 대부분이 제대로 검증되지 않습니다. 메서드의 `_sequence` 매개변수는 필드가 검증되지 않은 `SequenceData` 유형입니다. 누락된 확인 사항은 다음과 같습니다:

1. `sealedBeforeTimestamp`가 `block.timestamp`보다 큼
2. `sealedAfterTimestamp`가 항상 `sealedBeforeTimestamp`보다 큼
3. `sealedBeforeTimestamp`와 `sealedAfterTimestamp`의 차이가 최소 `2일` 등
4. `sealedBeforeTimestamp`와 `sealedAfterTimestamp`의 차이가 최대 `500일` 이하 등
5. `sealedBeforeTimestamp`와 `block.timestamp`의 차이가 최대 `10일` 이하 등

또한 `DropEngine::configureSequence`에서 `royaltyBps`가 100%(값 10000)를 넘지 않는다는 검증이 없습니다. `royaltyBps` 상한선을 낮추는 것을 제안합니다.

## 권장 사항

위에서 언급한 모든 사용자 입력에 대해 합리적인 제약 조건과 검증을 추가하십시오.

# [M-02] `address payable`의 `transfer` 함수 사용은 권장되지 않음

## 심각도

**영향:**
중간, 민트가 되돌아가므로(revert) 시퀀스를 사용할 수 없기 때문

**가능성:**
중간, 수신자가 스마트 계약이거나 2300 가스 이상을 사용하는 수신(receive) 함수가 있는 다중 서명 지갑일 때마다 발생하기 때문

## 설명

`DropEngine`의 `mint` 함수는 `address payable`의 `transfer` 메서드를 사용하여 기본 자산 자금을 주소로 전송합니다. 이 주소는 노드 소유자가 설정하며 `transfer`의 한계인 2300 가스 이상을 차지하는 `receive` 또는 `fallback` 함수가 있는 스마트 계약일 수 있습니다. 예를 들면 일부 스마트 계약 지갑이나 다중 서명 지갑이 있으므로 `transfer` 사용은 권장되지 않습니다.

## 권장 사항

`transfer` 대신 값을 포함한 `call`을 사용하십시오. `mint` 메서드에는 호출자가 EOA인지 확인하는 기능이 있으므로 재진입 위험이 없습니다. 이 작업이 완료되면 `revenueRecipient` 변수에서 `payable` 키워드를 제거할 수 있습니다.

# [M-03] 단일 단계 방식으로 수행되는 역할 이전 작업은 위험함

## 심각도

**영향:**
높음, 중요한 프로토콜 기능을 사용할 수 없게 되기 때문

**가능성:**
낮음, 관리자/소유자 오류가 필요하기 때문

## 설명

역할이나 관리자 권한을 다른 주소로 이전할 때 이 주소가 잘못되었고 실제로 어떤 사용자가 제어하지 않는 경우 문제가 발생할 수 있다는 것은 일반적인 문제입니다. 이는 노드 소유권 이전이 2단계 작업인 `NodeRegistry`에서 고려되었습니다. 그러나 `AccountRegistry`에서는 동일한 접근 방식이 사용되지 않았습니다. 여기서 계약은 단일 단계 소유권 이전 패턴을 가진 `Owned`를 상속하며 그 안의 `transferAccount` 로직도 단일 단계 패턴을 사용하고 있습니다.

## 권장 사항

`AccountRegistry` 소유권과 `transferAccount` 메서드 모두에서 2단계 소유권/권한 이전 패턴을 사용하십시오. `NodeRegistry`에서 사용한 접근 방식을 재사용할 수 있습니다.

# [M-04] ERC721 토큰을 처리할 수 없는 스마트 계약 주소로 민팅된 레코드는 영원히 갇힘

## 심각도

**영향:**
높음, 레코드가 영원히 갇히기 때문

**가능성:**
낮음, 엔진이 스마트 계약을 민터(minter)로 허용해야 하고 해당 계약이 ERC721 토큰 처리를 지원하지 않아야 하기 때문

## 설명

`Collection`의 두 `mintRecord` 메서드 모두 수신자가 ERC721 토큰을 실제로 처리할 수 있는 스마트 계약인지 확인하는 기능이 누락된 `ERC721`의 `_mint` 메서드를 사용합니다. 수신자가 ERC721 토큰을 처리할 수 없는 경우 영원히 갇히게 됩니다. 이 특정 문제에 대해 ERC721 표준에 `safe` 메서드가 추가되었으며 `Solmate`는 민팅 컨텍스트에서 이 문제를 처리하기 위해 확인하는 `_safeMint` 메서드를 추가했습니다. 이것은 `DropEngine`이 EOA만 민팅하도록 허용하기 때문에 실제로는 문제가 되지 않지만 사용자가 자유롭게 엔진을 구현할 수 있으므로 유효한 문제입니다.

## 권장 사항

ERC721 토큰에 대해 `_mint`보다 `_safeMint`를 선호하되, 재진입 공격 벡터를 열어주므로 매우 신중하게 수행하십시오. 이 때문에 `_safeMint`를 호출하는 메서드에 `nonReentrant` 수정자를 추가하는 것이 가장 좋습니다.

# [L-01] 주소에 계정이 없어도 `resolveId`는 0 ID를 반환함

이는 오류가 발생하기 쉽고 이 메서드가 호출되는 `NodeRegistry`에서 볼 수 있듯이 `resolveId` 함수의 모든 클라이언트가 `!= 0`을 확인해야 합니다. 더 좋고 안전한 접근 방식은 `subject`에 계정이 없을 때 `resolveId` 메서드에서 되돌리는(revert) 것입니다. 그러면 메서드의 클라이언트가 오류를 잡고 싶다면 그렇게 할 수 있습니다.

# [L-02] 일관성 없는 `tokenData.owner` 검증

`Collection`의 `mintRecord` 기능을 사용하면 호출자가 `to` 인수의 유효성을 검사하지 않으므로 모든 주소로 토큰을 민팅할 수 있습니다. 문제는 상속된 함수 `getTokenData`에 다음과 같은 확인이 있다는 것입니다:

```solidity
require(data.owner != address(0), "NOT_MINTED");
```

이는 실제로 사실이 아닙니다. 실수로라도 토큰이 0 주소로 민팅될 수 있기 때문입니다. 일관성을 위해 `Collection`의 두 `mintRecord` 함수 모두에서 `to` 인수에 대한 0 주소 확인을 추가할 것을 제안합니다.

# [I-01] 함수 매개변수에 더 추상적인 이름 사용

`ResourceFactory`의 `broadcast` 및 `broadcastAndStore` 메서드에는 `waterfall` 매개변수가 있지만 Collection이나 Split과 같은 다른 유형의 리소스가 사용될 수 있습니다. 두 메서드 모두에서 매개변수 이름을 `waterfall`에서 `resource`로 변경하십시오.

# [I-02] `NodeData.nodeType`은 존재 확인 용도로만 사용됨

시스템에서 `nodeType`이 사용되는 유일한 목적은 노드가 존재하는지 확인하는 것입니다. 이것은 간단한 부울로 수행하거나 실제로 "유형"이 필요한 경우 첫 번째 값이 `NON_EXISTENT`인 열거형을 사용하십시오.

# [I-03] NatSpec 문서가 불완전함

거의 모든 메서드에서 매개변수와 반환 변수가 누락되어 NatSpec 문서가 불완전합니다. NatSpec 문서는 개발자와 감사자가 코드를 더 잘 이해하는 데 필수적이며 강력히 권장됩니다. [NatSpec 형식](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html)을 참조하고 그곳에 설명된 지침을 따르십시오.

# [I-04] 사용되지 않는 import

`Collection`의 `IERC721` import는 사용되지 않습니다. 코드에서 제거하십시오.

# [I-05] `WaterfallFactory`의 복사-붙여넣기 주석

계약의 거의 모든 메서드가 `waterfall` 대신 `split`을 참조하므로 `SplitFactory`에서 복사하여 붙여넣은 것으로 예상됩니다. `WaterfallFactory`의 모든 주석에서 `split`을 `waterfall`로 바꾸십시오.

# [I-06] `override` 키워드 누락

인터페이스에서 구현되고 상속된 계약의 대부분 메서드에 `override` 키워드가 누락되었습니다. 각 메서드를 살펴보고 올바른 위치에 키워드를 적용하십시오. 몇 가지 예로는 `mintRecord` 및 `royaltyInfo` 메서드가 있습니다.

# [I-07] 안전한 pragma 문 사용

항상 안정적인 pragma 문을 사용하여 컴파일러 버전을 잠그고 바이트코드에 대한 결정론적 컴파일을 수행하십시오. 코드베이스가 어셈블리를 사용하지 않더라도 `0.8.13`에는 어셈블리 사용 시 버그가 있음을 명심하십시오.

# [I-08] 오타 및 문법 오류

코드베이스에서 수정해야 할 오타가 다수 있습니다:

`AccountTransfered` -> `AccountTransferred`

`has be` -> `has been`

`Inheritting` -> `Inheriting`

`Otherise` -> `Otherwise`

`managaeable` -> `manageable`

`so long as their is` -> `as long as there is`

`entites` -> `entities`

`Otherise` -> `Otherwise`

`additonal` -> `additional`

`seqeuence` -> `sequence`

`funcionality` -> `functionality`

`reliquish` -> `relinquish`
