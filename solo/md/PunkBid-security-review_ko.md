# 소개

**pashov**가 **PunkBid** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **PunkBid** 소개

**PunkBid** 프로토콜은 [CryptoPunksMarket](https://github.com/larvalabs/cryptopunks/blob/11532167fa705ced569fc3206df0484f9027e1ee/contracts/CryptoPunksMarket.sol) 위에 구축된 CryptoPunk NFT를 위한 온체인 입찰(bid-side) 오더북입니다. CryptoPunks는 현재 네이티브 온체인 마켓에서 판매되지만 몇 가지 단점(수탁형임)과 보안 문제(펑크 당 단일 활성 입찰, 입찰 수락이 `bid.amount + 1 wei`의 새 입찰로 프론트러닝 될 수 있음)가 있습니다. 이 프로토콜은 여러 사용자가 단일 펑크에 동시에 입찰할 수 있도록 허용하고 사용자가 여러 펑크에 동시에 입찰할 수 있도록 하여 이러한 문제를 해결합니다. 프로토콜에는 비수탁형(non-custodial) 입찰이 있기 때문입니다.

현재 입찰자는 스마트 계약에 `WETH`에 대한 무제한 지출 허용을 제공하는 반면, 판매자는 `PunkBidMarketV1::acceptBid`를 호출하기 바로 전에 `CryptoPunksMarket::offerPunkForSaleToAddress`를 호출해야 하며, 이를 통해 `PunkBidMarketV1` 계약이 펑크를 0 `wei`에 구매할 수 있습니다. 두 흐름 모두 각 작업(입찰 및 판매)이 비수탁 방식으로 발생할 수 있도록 수행됩니다.

새 입찰을 입력하면 입찰하려는 펑크에 대한 데이터를 보유한 `bytes cartMetadata` 인수를 받습니다([자세한 정보는 여기](https://github.com/punkbid/punkbid-js-sdk)). 오프체인 인덱서는 `cartMetadata`를 기반으로 머클 트리를 구축하며, 입찰하려는 각 CryptoPunk의 ID가 트리의 리프(leaf)가 됩니다. 계산된 루트가 `itemsChecksum` 인수와 일치하지 않으면 프론트엔드에서 입찰이 폐기됩니다. 입찰을 수락하려면 수락될 입찰이 판매되는 펑크에 대한 것임을 보여주는 `bytes32[] proof` 인수가 필요합니다.

# 위협 모델

## 역할 및 행위자

- Bidder - 펑크를 구매하기 위해 새 입찰을 입력하고 프로토콜이 `WETH`를 소비하도록 승인합니다.
- Seller - 펑크를 판매하므로 프로토콜이 펑크를 "소비"하도록 허용하여 입찰을 수락합니다.
- Indexer - 잘못된 입찰을 폐기하고 유효한 입찰을 인덱싱하는 오프체인 구성 요소입니다.
- Protocol admin - 프로토콜에 누적된 수수료를 인출할 수 있습니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇입니까?

**A:** 프로토콜은 `WETH`와 CryptoPunk NFT를 모두 처리하며, 언제든지 WETH를 "소비"할 수 있는 권한과 판매자가 `acceptBid`를 호출하기 바로 전에 펑크를 "소비"할 수 있는 권한을 갖습니다.

**Q:** 프로토콜에 일어날 수 있는 최악의 상황은 무엇입니까?

**A:** 판매자가 잘못된 입찰을 수락하거나 입찰자가 잘못된 CryptoPunk NFT에 입찰하는 것입니다.

**Q:** 어떤 경우에 프로토콜/사용자가 돈을 잃을 수 있습니까?

**A:** 공격자가 계약에 포함된 수수료를 훔치는 경우 프로토콜이 돈을 잃을 수 있으며, 사용자가 잘못된 입찰을 수락하거나 잘못된 NFT에 입찰하거나, 공격자가 계약에 대한 입찰자의 `WETH` 허용 또는 계약에 대한 판매자의 펑크 NFT 0 `wei` 판매 허용을 악용하는 경우 돈을 잃을 수 있습니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

**영향(Impact)** - 성공적인 공격의 기술적, 경제적, 평판적 손해
**가능성(Likelihood)** - 특정 취약점이 발견되고 악용될 확률
**심각도(Severity)** - 위험의 전반적인 중요도

# 보안 평가 요약

**_검토 커밋 해시_ - [ff349a602259792b7b7601eaacbb4118b0453a58](https://github.com/punkbid/punkbid-v1-contract/tree/ff349a602259792b7b7601eaacbb4118b0453a58)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `PunkBidMarketV1`
- `interfaces/**`

다음과 같은 수의 문제가 발견되었으며 심각도별로 분류되었습니다:

- 치명적 & 높음: 0개 문제
- 중간: 0개 문제
- 낮음: 2개 문제
- 정보성: 2개 문제

---

# 결과 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [L-01] | 프로토콜이 0 `wei` 펑크 판매 제안을 기대하는 것은 위험함 | 낮음 |
| [L-02] | 입찰 만료가 제한되지 않음 | 낮음 |
| [I-01] | `withdraw` 메서드가 비허가형일 수 있음 | 정보성 |
| [I-02] | `FEE`를 상수로 변경 | 정보성 |

# 상세 결과

# [L-01] 프로토콜이 0 `wei` 펑크 판매 제안을 기대하는 것은 위험함

`acceptBid` 함수는 판매용 펑크가 0 `wei`의 시장 매도 제안을 가질 것으로 예상합니다. [CryptoPunksMarket](https://github.com/larvalabs/cryptopunks/blob/11532167fa705ced569fc3206df0484f9027e1ee/contracts/CryptoPunksMarket.sol)에는 펑크에 대한 매도 제안을 생성하는 두 가지 방법인 `offerPunkForSale`과 `offerPunkForSaleToAddress`가 있습니다. 전자를 사용하면 시장의 누구든지 제안을 수락할 수 있고, 후자를 사용하면 보낸 `toAddress` 인수만 제안을 수락할 수 있습니다.

여기서 프론트엔드와 온체인에서 직접 프로토콜과 상호 작용하는 사용자가 `offerPunkForSaleToAddress` 메서드를 사용하고 `toAddress` 인수는 `PunkBidMarketV1` 계약의 주소여야 한다는 것이 중요합니다. 0 `wei` 제안과 함께 `offerPunkForSale`을 사용하면 봇이 즉시 제안을 백러닝하여 펑크를 가로챌 것입니다. `ICryptoPunksMarket` 인터페이스와 `PunkBidMarketV1`의 단위 테스트가 올바르지 않은 `offerPunkForSale` 메서드를 사용하고 있기 때문에 이것이 제 주의를 끌었습니다. 인터페이스와 테스트를 `offerPunkForSaleToAddress` 메서드를 사용하도록 업데이트하고, `acceptBid`의 NatSpec과 프론트엔드가 `offerPunkForSaleToAddress` 메서드를 사용하도록 잘 문서화되었는지 확인하십시오.

# [L-02] 입찰 만료가 제한되지 않음

`enterBid` 메서드는 `expiration` 인수에 대한 입력 검증이 누락되어 있어 이미 만료된 입찰이나 만료되지 않는 입찰을 입력할 수 있습니다. 또 다른 점은 `updateBids` 메서드가 이미 만료된 입찰을 업데이트할 수 있다는 것입니다. 현재 이는 약간의 저장 공간 사용과 이벤트 방출만 초래하지만, 시스템에서 유효한 입찰만 허용되도록 입력 위생(sanitization)을 수행하는 것이 좋습니다. 예를 들어 하한 및 상한 제약을 추가하는 것을 고려하십시오.

```solidity
require(expiration > block.timestamp && expiration < block.timestamp + 365 days);
```

# [I-01] `withdraw` 메서드가 비허가형일 수 있음

`withdraw`에서 `onlyOwner` 수정자를 사용하는 대신 호출자로 `msg.sender`를 `owner`로 바꾸고 수정자를 제거하십시오. 이렇게 하면 가스를 약간 절약하고 프로토콜의 모든 액세스 제어를 제거하여 완전히 비허가형(permissionless)으로 만들 수 있습니다.

# [I-02] `FEE`를 상수로 변경

현재 `FEE` 변수는 배포 전에 값이 알려져 있음에도 불구하고 `immutable`입니다. 여기서 `constant`를 사용하는 것이 더 정확하며, 온체인에서 읽을 것으로 예상되지 않고 소스 코드를 보면 오프체인에서 쉽게 볼 수 있으므로 `private`으로 만들 수도 있습니다.

