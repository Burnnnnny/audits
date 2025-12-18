# 소개

**pashov**가 **PunksBids** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **PunksBids** 소개

PunkBids 프로토콜은 CryptoPunks NFT 컬렉션을 위한 입찰 플랫폼입니다. 누구나 특정 속성이나 주어진 NFT ID 세트에 입찰할 수 있습니다. 이 프로토콜은 CryptoPunks Marketplace 위에 구축되었지만, 비수탁형(non-custodial)이라는 중요한 문제를 해결합니다. 원래 Marketplace 계약은 입찰자가 수탁 방식으로 계약에 ETH를 제출하도록 강제하는 반면, PunksBids 프로토콜은 오프체인 서명 입찰을 사용하여 원하는 구체적인 속성을 선택하면서 여러 펑크에 입찰할 수 있습니다.

## 관찰 사항

이 프로토콜은 `StringUtils` 라이브러리를 사용하여 온체인에서 문자열 조작 및 비교를 수행합니다. 이는 CryptoPunks 속성을 비교하고 확인하는 데 사용됩니다. 이는 일반적으로 머클 트리와 증명을 사용하여 온체인에서 속성을 확인하는 CryptoPunks Marketplace 위에 구축된 다른 프로토콜과는 대조적입니다.

입찰자와 판매자의 매칭은 릴레이어(relayer)를 통해 이루어지며, 릴레이어는 판매 트랜잭션에 대한 가스를 지불합니다.

입찰자는 `PunksBids` 계약에 `WETH`를 소비할 수 있는 허용을 제공해야 합니다.

## 특권 역할 및 행위자

- PunksBids owner - 입찰 매칭을 일시 중지하고, `feeRate` 및 `localFeeRate`를 변경하고, 누적된 수수료를 인출할 수 있습니다.
- Bidder - 오프체인에서 입찰에 서명하고 `PunksBids` 계약에 `WETH` 허용을 제공합니다.
- Bid matching relayer - 서명된 입찰로 `executeMatch`를 호출하고 판매를 위한 가스를 지불합니다.

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

**_검토 커밋 해시_ - [c783b2aa8d4a9e9efd631e921e2c3b21a2c26f18](https://github.com/datschill/PunksBidsSolidity/tree/c783b2aa8d4a9e9efd631e921e2c3b21a2c26f18)**

**_수정 검토 커밋 해시_ - [ba24b1f9e51091341e1775bcd7f5fd6d31892615](https://github.com/HoodLabs/PunksBidsSolidity/tree/ba24b1f9e51091341e1775bcd7f5fd6d31892615)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `interfaces/**`
- `lib/**`
- `PunksBids`

다음과 같은 수의 문제가 발견되었으며 심각도별로 분류되었습니다:

- 치명적 & 높음: 0개 문제
- 중간: 1개 문제
- 낮음: 2개 문제

---

# 결과 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [M-01] | 악의적인 소유자가 판매를 차익 거래할 수 있음 | 중간 |
| [L-01] | `chainId`가 캐시되지만 변경될 수 있음 | 낮음 |
| [L-02] | `ecrecover` 프리컴파일이 서명 가변성(signature malleability)에 취약함 | 낮음 |

# 상세 결과

# [M-01] 악의적인 소유자가 판매를 차익 거래할 수 있음

## 심각도

**영향:**
높음, 청구되어야 할 것보다 더 많은 비용을 사용자에게 청구할 것임

**가능성:**
낮음, 악의적/타협된 소유자가 필요함

## 설명

현재 `setFeeRate` 및 `setLocalFeeRate` 메서드에는 소유자가 설정하는 수수료율에 대한 상한선이 없습니다. 이는 소유자가 더 큰 수수료를 설정하여 거래를 프론트러닝 할 수 있는 중앙화 공격 벡터를 엽니다. 다음 시나리오를 고려하십시오:

1. Alice는 수수료가 1%라고 생각하고 실제로 99 ETH를 입찰하면서 Alien Punk에 대해 100 ETH 입찰을 합니다.
2. Bob은 98 ETH에 Alien Punk를 판매합니다.
3. 이제 Alice가 99 ETH를 지불하는 대신(프로토콜에 수수료로 1 또는 0.9를 제공) 펑크 + 1 ETH를 남기는 대신, 관리자가 수수료를 2%로 설정한 다음 거래를 실행하여 본질적으로 Alice에게서 1 ETH를 더 가져갈 수 있습니다.

## 권장 사항

`setFeeRate` 및 `setLocalFeeRate` 메서드 모두에 상한선(제한)을 설정하고 설정되는 값이 더 높으면 되돌리십시오(revert). 이렇게 하면 사용자는 수수료가 특정 숫자까지만 올라갈 수 있다는 것을 알 수 있습니다.

## 논의

**pashov:** 수정됨(Fixed).

# [L-01] `chainId`가 캐시되지만 변경될 수 있음

`chainId` 값을 캐시하는 것은 하드 포크가 네트워크의 chainId를 변경할 수 있으므로 좋은 관행이 아닙니다. 더 나은 해결책은 항상 현재 `block.chainid`가 캐시된 것과 같은지 확인하고 그렇지 않은 경우 업데이트하는 것입니다. [OpenZeppelin의 EIP712 구현](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/2271e2c58d007894c5fe23c4f03a95f645ac9175/contracts/utils/cryptography/EIP712.sol#L81-L87)의 접근 방식을 따르십시오.

## 논의

**pashov:** 인지함(Acknowledged).

# [L-02] `ecrecover` 프리컴파일이 서명 가변성(signature malleability)에 취약함

`s`와 `v`를 뒤집어 동일한 해시 및 서명자에 도달하는 다른 서명을 생성할 수 있습니다. 이것은 [여기](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/dfef6a68ee18dbd2e1f5a099061a3b8a0e404485/contracts/utils/cryptography/ECDSA.sol#L125-L136)와 같이 OpenZeppelin의 ECDSA 라이브러리에서 수정되었습니다. `canceledOrFilled` 매핑이 있기 때문에 이것이 문제가 되지는 않지만, ECDSA를 사용하여 문제를 해결하는 것이 매우 권장됩니다.

## 논의

**pashov:** 수정됨(Fixed).

