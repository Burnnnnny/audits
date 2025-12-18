# 소개

**Museum of Mahomes** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Museum of Mahomes** 소개

Museum of Mahomes 프로토콜은 예술품의 실제 사본을 교환하기 위해 토큰을 소각하는 것과 온체인 "리빌(reveal, 공개)" 메커니즘과 같은 몇 가지 특별한 기능을 갖춘 NFT 컬렉션입니다.

[추가 문서](https://docs.google.com/document/d/1Fymxg-rwKHznj03GaSOcySE5IHkq0eTwXm_ijqysdnE/edit)

## 관찰 사항

토큰의 최대 공급량은 일반적인 10000개가 아닌 3090개입니다. 계약 소유자는 언제든지 `price`를 어떤 값으로든 업데이트할 수 있습니다.

`DelegationRegistry`는 리빌 및 상환(redeeming)에 대한 토큰 허용량을 관리하므로 공격 벡터입니다. 이 감사의 범위에서 제외되었습니다.

## 특권 역할 및 행위자

- 컬렉션 소유자(Collection owner) - 민트 자금을 청구할 수 있을 뿐만 아니라 소유권을 이전하고 민트 가격을 설정하고 `treasury`가 될 주소를 설정할 수 있습니다.
- 컬렉션 메타데이터 소유자(Collection metadata owner) - `revealOpen`, `redeemOpen` 및 `baseURI` 속성을 제어합니다.
- 재무 계정(Treasury account) - 무료로 NFT를 민팅할 수 있습니다.
- 위임 레지스트리(Delegation Registry) - NFT 리빌 및 상환(소각)을 위한 허용량인 토큰 위임을 관리합니다.
- 민터(Minter) - ETH를 지불하여 NFT를 민팅할 수 있습니다.

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

**_검토 커밋 해시_ - [c63f28585f73b94a556bdce45831bbcd017790e3](https://github.com/0xfoobar/museumofmahomes/tree/c63f28585f73b94a556bdce45831bbcd017790e3)**

**_수정 검토 커밋 해시_ - [e4d0115b931c31e0dcd92433e54aba0f1b09ec7f](https://github.com/0xfoobar/museumofmahomes/tree/e4d0115b931c31e0dcd92433e54aba0f1b09ec7f)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `MuseumOfMahomes`

---

# 발견 사항 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [H-01] | 공급량의 마지막 NFT를 민팅할 수 없음 | 높음 | 수정됨 |
| [L-01] | Reveal 및 Redeem은 `true`로만 설정되어야 함 | 낮음 | 인지됨 |
| [L-02] | 모든 상태 변경 메서드는 이벤트를 방출해야 함 | 낮음 | 인지됨 |
| [L-03] | `treasury` 계정은 모든 NFT를 민팅할 수 있음 | 낮음 | 인지됨 |
| [L-04] | 계약이 상태 머신으로 작동하지 않음 | 낮음 | 인지됨 |
| [L-05] | 2단계 액세스 제어 이전 패턴 사용 | 낮음 | 인지됨 |

# 상세 발견 사항

# [H-01] 공급량의 마지막 NFT를 민팅할 수 없음

## 심각도

**영향:**
중간, 단 하나의 NFT만 민팅할 수 없지만 이는 프로토콜에 가치 손실이므로

**가능성:**
높음, 마지막 NFT를 민팅하는 것이 불가능하므로

## 설명

현재 `mint` 및 `mintPhysical` 메서드 모두 다음과 같은 확인이 있습니다:

```solidity
if (nextId + amount >= MAX_SUPPLY) revert ExceedsMaxSupply();
```

이는 `nextId`가 `MAX_SUPPLY - 1`일 때 `amount` 1이 허용되어야 하지만 현재 확인으로는 코드가 되돌려지기(revert) 때문에 잘못되었습니다. 이는 확인에 있는 등호(`=`) 때문인데, 여기에 있어서는 안 됩니다. 다음은 문제를 보여주는 개념 증명(POC) 단위 테스트입니다(`MuseumOfMahomes.t.sol`에 추가):

```solidity
    function testNotAllNFTsCanBeMinted() public {
        museum.setPrice(PRICE);
        uint256 allButOneNFTSupply = 3089;

        // mint all but one from the NFT `MAX_SUPPLY` (3090)
        museum.mint{value: allButOneNFTSupply * PRICE}(address(this), allButOneNFTSupply);
        require(allButOneNFTSupply == museum.balanceOf(address(this)), "Mint did not work");

        // try to mint the last NFT from the supply, but it doesn't work
        vm.expectRevert(MuseumOfMahomes.ExceedsMaxSupply.selector);
        museum.mint{value: PRICE}(address(this), 1);
    }
```

## 권장 사항

`mint` 및 `mintPhysical` 모두에서 다음과 같이 변경하십시오:

```diff
- if (nextId + amount >= MAX_SUPPLY) revert ExceedsMaxSupply();
+ if (nextId + amount > MAX_SUPPLY) revert ExceedsMaxSupply();
```

# [L-01] Reveal 및 Redeem은 `true`로만 설정되어야 함

현재 `setRevealOpen` 및 `setRedeemOpen` 메서드를 사용하면 `metadataOwner`가 결정하는 횟수만큼 값을 `true`와 `false`로 모두 설정할 수 있습니다. 두 곳 모두 `true`로 한 번만 설정할 수 있고 그 이후에는 `false`로 설정해서는 안 되므로 이렇게 되어서는 안 됩니다. 설정자(setters)를 값을 `true`로만 설정하는 메서드로 변경하고 메서드에서 매개변수를 제거하십시오.

# [L-02] 모든 상태 변경 메서드는 이벤트를 방출해야 함

현재 `MuseumOfMahomes` 계약의 상태 변경 메서드 대부분은 이벤트를 방출하지 않습니다. 예로 `setPrice` 메서드가 있는데, 이는 NFT의 현재 가격을 모니터링하고 추적하려는 사용자 또는 프런트엔드/UI 클라이언트에게 중요할 수 있습니다. 모든 상태 변경 메서드에 적절한 이벤트 방출을 추가하십시오.

# [L-03] `treasury` 계정은 모든 NFT를 민팅할 수 있음

현재 `treasury` 매핑에 있는 계정은 모든 NFT를 무료로 민팅할 수 있습니다. 그러한 계정이 토큰 민팅 비용을 지불하지 않는 것이 바람직하지만 `treasury` 계정에서 민팅하는 NFT 수를 제한하기 위해 `MAX_TREASURY_MINTS` 상한선을 추가하는 것을 고려하십시오. 또한 `treasury` 계정이 민팅할 때 `msg.value`가 0인지 확인할 수도 있습니다.

# [L-04] 계약이 상태 머신으로 작동하지 않음

현재 `revealOpen`이 아직 `true`로 설정되지 않은 상태에서 `metadataOwner`가 `redeemOpen` 값을 `true`로 설정할 수 있습니다. 계약 작동 방식에 대한 순서/흐름이 있어야 합니다. 먼저 민팅, 그 다음 공개(reveal), 그 다음 상환(redeem)(또는 공개 직후 상환)입니다. `revealOpen == true`인 경우에만 `redeemOpen`을 `true`로 설정할 수 있도록 허용하고, 민트가 완료된 경우(`totalSupply == MAX_SUPPLY`)에만 `revealOpen`을 `true`로 설정할 수 있도록 허용하십시오.

# [L-05] 2단계 액세스 제어 이전 패턴 사용

`MuseumOfMahomes` 계약은 `setOwner` 및 `setMetadataOwner`에서 단일 단계 액세스 제어 이전 패턴을 사용합니다. 즉, 현재 `owner` 또는 `metadataOwner` 계정이 잘못된 주소로 메서드를 호출하면 해당 역할은 의존하는 모든 기능과 함께 영원히 손실됩니다. OpenZeppelin의 [Ownable2Step](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) 패턴을 따르고 작업에 대한 2단계 이전 패턴을 구현하십시오.
