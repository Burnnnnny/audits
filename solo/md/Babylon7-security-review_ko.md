# 소개

**Babylon7** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Babylon7** 소개

Babylon7 프로토콜은 온체인 추첨(raffle)을 구현합니다. 구독 방식을 사용하여 Chainlink의 VRF 기술을 통합하며, 아티스트가 목록을 생성하면 참가자가 추첨 티켓을 구매하여 ERC721 또는 일부 ERC1155 토큰을 획득하려고 시도하는 것이 주요 사용 사례입니다. 추첨에는 활성(Active), 해결 중(Resolving), 성공(Successful), 완료(Finalized) 및 취소(Canceled)의 5단계가 있습니다. 추첨은 또한 예약된 티켓을 지원하며, 이를 위해 머클 트리를 사용한 화이트리스팅 접근 방식을 사용합니다.

[더 많은 문서](https://dot-warlock-cc5.notion.site/Babylon7-contracts-3d162487600b4555ae1657cf8d44424d)

## 관찰 사항

프로토콜은 비수탁(non-custodial) 방식입니다. 즉, 추첨할 토큰은 계약에 보관되지 않으며, 계약은 마지막에 토큰을 소유자의 지갑에서 옮길 수 있는 승인(allowance)을 가질 것으로 기대합니다. 소유자가 계약에 대한 승인을 제거하면 누구나 `refund`를 호출하고 `listing.state = ListingState.Canceled` 코드를 실행할 수 있으므로 이는 "취소"로 작용합니다.

프로토콜은 성공적으로 완료된 각 추첨에서 `_minDonationBps`의 최소 값과 100%의 최대 값을 가진 수수료를 받습니다.

`Babylon7Core` 계약은 업그레이드 가능하므로 프로토콜과 사용자에게 중앙 집중화 위험을 초래합니다.

모든 추첨(목록)의 최대 기간은 7일입니다(이 값은 프로토콜 소유자가 변경할 수 있음).

# 위협 모델

## 특권 역할 및 행위자

- 무작위 제공자(Random Provider) - 무작위성 요청을 받을 수 있는 계약으로, `VRFConsumerBaseV2`를 상속할 것으로 예상됩니다. 목록 승자를 선택하기 위해 임의의 값을 제공하는 `resolveClaimer`를 호출할 것으로 예상됩니다.
- 재무부(Treasury) - 성공적으로 완료된 추첨에서 수수료를 받을 EOA/계약입니다.
- 목록 생성자(Listing Creator) - 목록 제한 사항 업데이트, 목록 정산, 목록 취소를 할 수 있습니다.
- 티켓 발행자(Ticket Minter) - 아이템을 획득하기 위해 티켓을 발행하여 활성 추첨에 참여할 수 있으며 목록이 취소된 경우 환불을 청구할 수 있습니다.
- 허용 목록 티켓 발행자(Allowlist Ticket Minter) - 일반 티켓 발행자와 같지만 보장된 티켓 발행 슬롯이 있습니다.
- 코어 계약 소유자(Core Contract Owner) - 추첨이 현재 활성 상태인 동안 `maxListingDuration`, `randomProvider`, `minDonationBps` 및 `treasury`를 변경할 수 있습니다.

## 각 역할이 악의적인 경우

무작위 제공자는 자신에게 유리하지 않은 경우(예: 당첨되지 않은 ID로 티켓을 구매한 경우) 무작위성을 보류할 유해가 있을 수 있습니다.

재무부 주소는 가능한 한 많은 수수료를 받기를 원하므로 `listing.donationBps`를 늘리는 것이 목표입니다.

목록 생성자는 아이템을 보내지 않고 티켓 지급금을 받으려고 시도할 수 있습니다.

티켓 발행자는 추첨에 당첨될 가능성을 최대한 높이면서 가능한 한 적게 지불하기를 원합니다. 그는 또한 환불 메커니즘을 악용하여 자신이 투자한 것보다 더 많은 것을 청구하려고 시도할 수 있습니다.

허용 목록 티켓 발행자는 허용된 것보다 더 많은 티켓을 발행하려고 시도할 수도 있습니다.

코어 계약 소유자는 생성자의 승인을 악용하여 아이템을 훔치려고 시도할 수 있습니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

**A:** 프로토콜이 승인을 가진 아이템(ERC721/ERC1155)과 추첨 참가자의 티켓 지불금(기본 자산 가치)입니다.

**Q:** 프로토콜/사용자가 돈을 잃을 수 있는 경우는 언제인가요?

**A:** 승인이 악용되거나 티켓 지불금이 추첨 계약에 묶이는 경우입니다.

**Q:** 공격자가 목표를 달성할 수 있는 방법에는 어떤 것이 있나요?

**A:** 목록 정산 또는 환불 메커니즘을 강제로 되돌리거나(revert) 자신을 추첨의 승자로 만드는 것입니다.

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

**_검토 커밋 해시_ - [b5de7e8384df77ac5f04abe012fd6a22388ef436](https://github.com/babylon-art/babylon7-contracts/tree/b5de7e8384df77ac5f04abe012fd6a22388ef436)**

**_수정 검토 커밋 해시_ - [524e190518e7b10f4f22eb613d293d66160c1a3c](https://github.com/babylon-art/babylon7-contracts/tree/524e190518e7b10f4f22eb613d293d66160c1a3c)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Babylon7Core`
- `RandomProvider`
- `interfaces/**`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 0개 문제
- 중간: 1개 문제
- 낮음: 5개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [M-01] | 프로토콜 관리자 계정은 큰 중앙 집중화 공격 표면을 가짐 | 중간 |
| [L-01] | Checks-Effects-Interactions 패턴 미준수 | 낮음 |
| [L-02] | 무작위성이 수신되면 캐시 된 요청을 삭제해야 함 | 낮음 |
| [L-03] | Solidity에서는 푸시(push)보다 풀(pull) 패턴이 선호됨 | 낮음 |
| [L-04] | `minDonationBps` 변수에는 상한이 필요함 | 낮음 |
| [L-05] | 구조체(struct)에 사용된 열거형(Enum)에는 유효하지 않은 기본 요소가 있어야 함 | 낮음 |

# 상세 발견 사항

# [M-01] 프로토콜 관리자 계정은 큰 중앙 집중화 공격 표면을 가짐

## 심각도

**영향:**
높음, 생성자가 추첨된 아이템과 지급금을 잃을 수 있기 때문

**가능성:**
낮음, 악의적이거나 침해된 관리자가 필요하기 때문

## 설명

악의적이거나 침해된 관리자는 프로토콜에 대해 다음과 같은 다양한 러그(rug) 같은 공격을 실행할 수 있습니다:

1. `Babylon7Core` 계약을 업그레이드하여 생성자의 승인을 악용하는 코드를 추가함으로써 추첨된 아이템을 프로토콜 소유자가 훔침
2. `_treasury` 주소를 `payable` `fallback` 또는 `receive` 메서드가 없는 주소(예: `address(0)`)로 설정하여 `listing.creator`가 `transferETHToCreator` 트랜잭션을 실행할 수 없게 만듦
3. 언제든지 소유자가 제어하는 무작위 제공자를 설정하여 추첨의 승자를 제어함

다음과 같은 작은 문제들도 있습니다:

1. `_maxListingDuration`을 매우 작게 설정
2. `_minDonationBps`를 매우 크게 설정 (`initialize` 및 `setMinDonationBps` 모두에서)
3. `RandomProvider`에서 `core` 주소를 변경하여 `requestRandom`에 대한 직접 호출을 허용할 수 있음

## 권장 사항

사용자가 관리자의 프로토콜 업그레이드 또는 기타 작업을 실제로 모니터링할 수 있도록 TimeLock 계약을 프로토콜 소유자로 사용하십시오. 또 다른 옵션은 관리자를 거버넌스 제어 주소로 만드는 것입니다.

또한 `MINIMUM_MAX_LISTING_DURATION` 상수를 사용하고 `setMaxListingDuration`에서 `maxListingDuration` 값을 검증해야 하며, `minDonationBps` 값에 대해 `initialize` 및 `setMinDonationBps` 모두에서 `MAXIMUM_MIN_DONATION_BPS` 상수로 동일한 작업을 수행해야 합니다. 마지막으로 `setBabylon7Core`는 한 번만 호출되고 나중에 `core`를 변경할 수 없도록 만들어야 합니다.

# [L-01] Checks-Effects-Interactions 패턴 미준수

`transferETHToCreator`에서 `nonReentrant` 수정자가 있더라도 CEI 패턴을 따르지 않습니다. `listing.state = ListingState.Finalized;` 코드는 이더 전송 후에 실행됩니다. 외부 호출을 수행하기 전에 항상 상태를 먼저 변경하는 것이 좋습니다. `nonReentrant` 수정자로 인해 현재 코드가 취약하지 않지만 따라야 할 모범 사례입니다.

# [L-02] 무작위성이 수신되면 캐시 된 요청을 삭제해야 함

`RandomProvider::fulfillRandomWords`에서 `requests[_requestId].exists == true`인지 확인합니다. 이것은 맞지만 요청이 이행된 후(메서드에서 방출된 이벤트 이름과 같이) 요청 값은 매핑에서 삭제되어 더 이상 존재하지 않아야 합니다.

```diff
        require(requests[_requestId].exists, 'RandomProvider: requestId not found');
        _core.resolveClaimer(requests[_requestId].listingId, randomWords[0]);
        emit RequestFulfilled(_requestId, requests[_requestId].listingId, randomWords);
+       delete requests[_requestId];
```

# [L-03] Solidity에서는 푸시(push)보다 풀(pull) 패턴이 선호됨

`Babylon7Core`의 `resolveClaimer` 메서드는 "푸시" 패턴을 사용하여 ERC721/ERC1155 아이템을 추첨 승자에게 강제로 전송합니다. 이는 `safeTransferFrom` 메서드로 인해 가스 그리핑(gas griefing), 강제 되돌리기(force reverts) 및 콜백과 같은 일부 공격 벡터를 엽니다. 승자가 아이템을 청구하기 위해 직접 트랜잭션을 보내야 하는 푸시보다 풀 접근 방식을 사용하는 것이 좋습니다.

# [L-04] `minDonationBps` 변수에는 상한이 필요함

`initialize`나 `setMinDonationBps` 모두 `minDonationBps` 값이 `> BASIS_POINTS`가 아닌지 확인하지 않아 소유자(악의적이거나 실수로)가 그렇게 만들 수 있습니다.

# [L-05] 구조체(struct)에 사용된 열거형(Enum)에는 유효하지 않은 기본 요소가 있어야 함

현재 `_listingInfos[id].state`의 기본값은 `id`가 존재하지 않더라도 `ListingState.Active`입니다. `Active`는 존재하지 않는 목록과 활성 목록 모두에 사용되므로 이는 취약점을 엽니다. 다른 존재하는 확인으로 인해 코드가 취약하지 않지만 목록의 기본값으로 `ListingState.Inactive`를 사용하는 것이 정확하므로 첫 번째 열거형 요소로 추가해야 합니다.
