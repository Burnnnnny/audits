# 소개

**Metalabel** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다.

# 프로토콜 개요

**첫 번째 보안 검토에서 복사됨**

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

**여기서부터 이어짐**

새로운 버전의 Metalabel 프로토콜은 프로토콜의 UX를 개선하는 기능과 스쿼드에 NFT를 민팅하기 위한 `Memberships` 기능을 제공합니다.
새로운 추상화 중 일부는 다음과 같습니다:

- 컨트롤러(Controller) - 새로운 메타라벨 설정 및 새로운 릴리스 게시를 위한 도우미 계약
- 엔진 V2(EngineV2) - 내장된 가격 하락(decay) 메커니즘, 메타데이터 마법, 최적화 및 소유자를 위한 수수료가 포함되어 있습니다.
- 멤버십(Memberships) - 메타라벨의 일부임을 보여주는 NFT

새로 추가된 기능은 100% 코드 커버리지(라인, 분기, 함수)를 가지고 있어 잘 테스트 되었습니다.

[추가 문서](https://metalabel.notion.site/Metalabel-Protocol-Walkthrough-V1-1-Addendum-3e18e13a1ccc48d68e777956a20279c6)

# 위협 모델

**첫 번째 보안 검토에서 복사됨**

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

**여기서부터 이어짐**

Q: 프로토콜에 일어날 수 있는 최악의 일은 무엇인가요? (이번 반복에서 발생하는 공격 표면)

1. `ControllerV1`의 액세스 제어 의미 체계가 깨져 카탈로그가 망가짐
2. 가격 하락에 따른 `DropEngineV2`의 민트 가격 책정 오류
3. 전체 시퀀스 드롭을 스나이핑하는 봇
4. 민터/사용자가 소유자의 민트 수수료를 훔침

### 흥미롭거나 예상치 못한 설계 선택:

동일한 `ControllerV1` 인스턴스가 여러 노드의 컨트롤러로 설정되므로 공유됩니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

# 보안 평가 요약

**_검토 커밋 해시_ - [fb04291dfdf7114bbec12ef5ec30b4135eac4878](https://github.com/metalabel/metalabel-contracts-v1_1/tree/fb04291dfdf7114bbec12ef5ec30b4135eac4878)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Memberships`
- `MembershipsFactory`
- `ControllerV1`
- `DropEngineV2`
- `RevenueModuleFactory`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 0개 문제
- 중간: 3개 문제
- 낮음: 4개 문제
- 정보성: 5개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [M-01] | `maxRecordsPerTransaction`에 대한 보호 확인을 우회할 수 있음 | 중간 |
| [M-02] | 불충분한 입력 검증으로 인해 여러 공격 벡터가 열림 | 중간 |
| [M-03] | 소유자가 수수료를 100%로 설정하여 시퀀스 구성을 프런트런(front-run)할 수 있음 | 중간 |
| [L-01] | 사용자가 `Memberships` NFT를 민팅하고 소각한 다음 다시 민팅할 수 있음 | 낮음 |
| [L-02] | 누구나 당신을 위해 당신의 멤버십을 민팅할 수 있음 | 낮음 |
| [L-03] | 사용자 제공 값에 대한 상한 검증 없음 | 낮음 |
| [L-04] | Merkle 트리 리프 생성이 결함이 있으며 두 번째 프리이미지(preimage) 공격으로 이어질 수 있음 | 낮음 |
| [I-01] | 중복된 사용자 지정 오류 | 정보성 |
| [I-02] | NatSpec이 불완전함 | 정보성 |
| [I-03] | 중복 코드 | 정보성 |
| [I-04] | 주석의 오타 및 문법 오류 | 정보성 |
| [I-05] | `override` 키워드 누락 | 정보성 |

# 상세 발견 사항

# [M-01] `maxRecordsPerTransaction`에 대한 보호 확인을 우회할 수 있음

## 심각도

**영향:**
중간, 프로토콜 불변성이 깨질 수 있고 코드가 잘못된 보안 감각을 주기 때문

**가능성:**
중간, 공격이 수행하기 쉽고 과거에 이러한 공격을 본 적이 있기 때문

## 설명

다음 시나리오를 살펴보겠습니다:

1. 컬렉션이 `maxRecordsPerTransaction`이 1이고 총공급량이 10인 드롭을 생성합니다.
2. 많은 사람이 민팅할 수 있을 것으로 예상됩니다.
3. 악의적인 행위자가 충분한 가치가 있는 다른 지갑을 로드하고 10개의 민트에 대한 트랜잭션을 번들로 묶는 스크립트를 작성합니다.
4. 악의적인 행위자만 민팅했고 다른 사람은 아무도 민팅하지 못했습니다.

봇/스나이퍼에 대한 보호 장치가 있었음에도 불구하고 결과적으로 1개의 계정만 민팅하게 되었습니다.

## 권장 사항

`maxRecordsPerTransaction` 확인이 스나이핑 공격으로부터 프로토콜을 보호하지 않는다고 문서화하십시오. 이로부터 보호하려면 Merkle 트리에 넣은 다음 `mint`에서 검증할 주소의 사전 등록을 위한 오프체인 프로세스를 사용하기로 결정할 수 있습니다.

# [M-02] 불충분한 입력 검증으로 인해 여러 공격 벡터가 열림

## 심각도

**영향:**
높음, 잔액 오버플로 및 소각된 NFT 재민팅 가능성 때문

**가능성:**
낮음, 악의적/침해된 소유자 계정 또는 소유자 입력 오류가 필요하기 때문

## 설명

`adminTransferFrom` 메서드는 `from` 인수의 값이 `address(0)`이 아니어야 함을 검증하지 않습니다. 이제 `from == address(0)`이면 여러 공격 벡터가 열립니다:

1. 소각된 NFT를 "재민팅"할 수 있으며 `totalSupply`를 변경하지 않고도 발생합니다.
2. `from` 잔액이 0이면 `_balanceOf[from]`이 언더플로 됩니다.
3. 0 ID 토큰(존재해서는 안 됨)을 이 메서드로 "민팅"할 수 있습니다.

## 권장 사항

`from` 인수가 `address(0)`이 아닌지 확인하고 주장(assert)하는 기능을 추가하십시오.

# [M-03] 소유자가 수수료를 100%로 설정하여 시퀀스 구성을 프런트런(front-run)할 수 있음

## 심각도

**영향:**
높음, 눈에 띄지 않게 지나가면 `revenueRecipient` 주소를 러그(rug)할 수 있기 때문

**가능성:**
낮음, 악의적/침해된 소유자 계정이 필요하기 때문

## 설명

`setPrimarySaleFeeBps`는 계약 소유자 주소에서 언제든지 호출할 수 있으며 수수료 변수를 즉시 업데이트합니다. 이제 사용자가 `configureSequence`를 호출하려고 할 때, 소유자는 사용자 호출을 프런트런하고 수수료를 100%로 업데이트할 수 있습니다. `configureSequence`에 다음 코드가 있기 때문입니다:

```solidity
dropData.primarySaleFeeBps = primarySaleFeeBps;
```

이제 이 시퀀스 드롭에 대한 전체 민트 지불금이 계약 소유자에게 돌아갑니다. 그는 또한 이 공격을 실행하고 각 `configureSequence` 호출을 프런트런하여 모든 민트의 ETH 가치를 얻을 수 있습니다.

## 권장 사항

사용자가 `dropData.primarySaleFeeBps`를 제공하므로 그가 기대한 수수료가 현재 `DropEngineV2`에 설정된 수수료와 동일한지 확인하고 현재 수수료가 더 크면 트랜잭션을 되돌리십시오(revert). 또한 일반적으로 수수료가 100%까지 올라가는 것을 허용하지 않는 것이 좋으며 상한선을 합리적인 숫자로 낮추십시오.

# [L-01] 사용자가 `Memberships` NFT를 민팅하고 소각한 다음 다시 민팅할 수 있음

`mintMemberships` 메서드는 동일한 Merkle 트리 리프의 다중 재사용을 허용합니다. 즉, 사용자는 민팅한 다음 소각하고 다시 민팅할 수 있습니다. 이렇게 하면 이벤트를 스팸으로 보내고 `totalMinted` 및 `totalSupply`를 늘릴 수 있습니다. Merkle 트리에서 동일한 리프의 재사용을 금지하는 확인을 추가하십시오.

# [L-02] 누구나 당신을 위해 당신의 멤버십을 민팅할 수 있음

`mintMembership` 메서드는 `msg.sender` 대신 리프(leaf)에 `mints[i].to` 값이 있는 Merkle 트리를 사용합니다. 즉, 누구나 당신을 위해 당신의 멤버십을 민팅할 수 있습니다. 이는 사용자가 원하지 않을 수 있는 NFT의 ID에 영향을 줄 수 있음을 의미합니다. 리프 생성 시 사용자 제공 값 대신 `msg.sender`를 사용하는 것을 선호하십시오.

# [L-03] 사용자 제공 값에 대한 상한 검증 없음

`DropEngineV2`의 `DropData`의 `decayStopTimestamp` 및 `priceDecayPerDay` 속성에는 상한 검증이 없습니다. 너무 큰 값이 설정되면(예: 오류로 인해) 민팅 프로세스가 DoS될 수 있습니다. 두 값 모두에 대해 합리적인 상한선을 추가하십시오.

# [L-04] Merkle 트리 리프 생성이 단일 해시되어 있으며 코드가 변경될 경우 두 번째 프리이미지(preimage) 공격으로 이어질 수 있음

리프가 단일 해시된 Merkle 트리는 [두 번째 프리이미지 공격](https://flawed.net.nz/2018/02/21/attacking-merkle-trees-with-a-second-preimage-attack/)에 취약합니다. 올바른 방법은 [OpenZeppelin이 제안하는 대로](https://github.com/OpenZeppelin/merkle-tree#standard-merkle-trees) 이중 해시하는 것입니다. 이 문제는 `ControllerV1`과 `Memberships` 모두에 존재하지만 후자의 경우 최대 1 잔액 확인으로 인해 트랜잭션이 되돌려지고(revert) 전자에서는 새 Metalabel을 설정하기만 하지만 `subdomains` 매핑을 엉망으로 만들 수 있습니다.

# [I-01] 중복된 사용자 지정 오류

`NotAuthorized` 사용자 지정 오류는 코드베이스 전체에서 중복됩니다. 한 번 선언하고 재사용하십시오. 별도의 파일로 추출해야 하는 중복 인터페이스도 마찬가지입니다.

# [I-02] NatSpec이 불완전함

코드베이스 전체에서 @param 및 @return 필드가 누락되었습니다. NatSpec 문서는 개발자와 감사자가 코드를 더 잘 이해하는 데 필수적이며 강력히 권장됩니다. [NatSpec 형식](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html)을 참조하고 그곳에 설명된 지침을 따르십시오. 또한 `createMemberships`의 NatSpec이 올바르지 않으며 `RevenueModuleFactory`도 마찬가지입니다.

# [I-03] 중복 코드

다음 import는 사용되지 않으므로 제거할 수 있습니다: `ControllerV1`의 `DropEngineV2` 및 `DropData`, `Memberships`의 `MerkleProofLib`. `_mintAndBurn`의 `onlyAuthorized` 수정자는 이 메서드를 호출하는 함수가 이미 가지고 있으므로 제거할 수 있습니다. 또한 `DropEngineV2`의 `baseTokenURIs` 및 `mintAuthorities`는 사용되지 않는 저장소 변수이므로 제거해야 합니다.

# [I-04] 주석의 오타 및 문법 오류

`Information provided with publishing a new release` -> Information provided when publishing a new release

`Lanch` -> `Launch`

`offchain` -> `off-chain`

`inheritted` -> `inherited`

`contorller` -> `controller`

`Tranfer` -> `Transfer`

`addidtional` -> `additional`

`iniitalization` -> `initialization`

`additonal` -> `additional`

`A an` -> `An`

`Admin can use a merkle root to set a large list of memberships that can /// minted by anyone with a valid proof to socialize gas` -> `that can be minted`

`Token URI computation defaults to baseURI + tokenID, but can be modified /// by a future external metadata resolver contract that implements IEngine` -> `IEngine` 대신 `ICustomMetadataResolver`

`having to a separate storage` -> `having to do a separate storage`

`programatically` -> `programmatically`

`psuedo` -> `pseudo`

# [I-05] `override` 키워드 누락

`DropEngineV2`는 `IEngine`에서 `configureSequence` 메서드를 상속하지만 `override` 키워드가 누락되었습니다.
