# 소개

**NFT Loots** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **NFT Loots** 소개

NFT Loots는 온체인에서 ERC721 토큰을 사용하여 전리품 상자(lootboxes)를 구현하는 프로토콜입니다. 게임은 25개의 상자를 제공하며 플레이어는 하나를 클릭하여 당첨 여부를 확인합니다. 플레이하려면 사용자는 `USDT`로 "내기(bet)"를 지불해야 합니다. 애플리케이션은 Polygon Mainnet에서 출시될 예정이며 Chainlink의 VRF 기술을 사용하고 있습니다.

## 관찰 사항

전리품 상자 게임은 NFT 또는 USD 보상을 담을 수 있는 25개의 상자를 제공합니다. 시간제한이 있습니다.

현재 두 개의 다른 계정이 동일한 상품을 받을 가능성이 있습니다.

관리자는 언제든지 `NFTLootbox` 계약에서 모든 ERC20 토큰을 인출할 수 있습니다.

## 특권 역할 및 행위자

- VRF 확인 소유자 (VRF confirmed owner) - `keyHash`를 변경할 수 있으며 `requestRandomWords` 메서드의 액세스 제어도 관리합니다.
- VRF 관리자 (VRF admin) - `requestRandomWords`를 호출할 수 있으며, 이는 `NFTLootbox`만 가능해야 합니다(현재 강제되지 않음).
- NFT Lootbox 소유자 (NFT Lootbox owner) - 실제 전리품 상자를 생성하고 `betCoin`, `vrfV2Consumer` 주소를 설정하고 ERC20/ERC721 토큰을 인출할 수 있습니다.
- 플레이어 (Player) - 승인이 필요하지 않으며 누구나 플레이하고 NFT 전리품 상자 게임에서 승리하려고 시도할 수 있습니다.

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

**_검토 커밋 해시_ - [6b252b1086021c1f57bf7db65506e7d7ead18c42](https://dev.azure.com/blockstars/Solidity%20Projects%20Public/_git/NFT%20Lootbox?version=GC6b252b1086021c1f57bf7db65506e7d7ead18c42)**

**_수정 검토 커밋 해시_ - [9e9c86fed5f4498574614a4a434d75fd391fe7cf](https://dev.azure.com/blockstars/Solidity%20Projects%20Public/_git/NFT%20Lootbox?version=9e9c86fed5f4498574614a4a434d75fd391fe7cf)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `random/VRFv2Consumer`
- `NFTLootbox`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 2개 문제
- 중간: 4개 문제
- 낮음: 1개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | Polygon 체인 재구성(reorgs)이 게임 결과를 자주 변경함 | 치명적 |
| [H-01] | 계약에 승자가 상금을 청구하기에 충분한 잔액이 없을 수 있음 | 높음 |
| [M-01] | 두 사람이 같은 상품을 받을 수 있음 | 중간 |
| [M-02] | 일부 일반적인 비표준 ERC20 토큰은 프로토콜과 호환되지 않음 | 중간 |
| [M-03] | 전리품 상자 입력 검증이 존재해야 함 | 중간 |
| [M-04] | 코드에 중앙 집중화 공격 벡터가 존재함 | 중간 |
| [L-01] | 계약이 `safeTransferFrom` 메서드로 전송된 NFT를 받을 수 없음 | 낮음 |

# 상세 발견 사항

# [C-01] Polygon 체인 재구성(reorgs)이 게임 결과를 자주 변경함

## 심각도

**영향:**
높음, 이미 당첨된 사용자가 보상을 잃게 되므로

**가능성:**
높음, Polygon에서는 깊이가 3보다 큰 재구성이 자주 발생하므로

## 설명

`VRFv2Consumer`의 `REQUEST_CONFIRMATION` 상수는 3으로 설정됩니다. 이 값은 무작위성을 수신하기 전에 최소한 얼마나 많은 블록을 기다려야 하는지 Chainlink VRF 서비스에 알리는 데 사용됩니다. 이 값이 추가된 이유는 체인 재구성(chain reorganizations) 때문입니다. 이 이벤트가 발생하면 블록과 트랜잭션이 재구성되고 변경됩니다. 이는 Polygon에서 출시될 것으로 예상되므로(`README.md`에 언급됨) 이 애플리케이션에서 심각한 문제입니다. [여기](https://polygonscan.com/blocks_forked)에서 볼 수 있듯이 하루에 5회 이상 3 블록 이상의 깊이로 블록 재구성이 발생합니다. [이 기사](https://protos.com/polygon-hit-by-157-block-reorg-despite-hard-fork-to-reduce-reorgs/)에서는 최근 Polygon에서 156 블록 깊이의 체인 재구성이 발생한 사건을 볼 수도 있습니다. 즉, VRF에서 무작위성을 요청하는 트랜잭션이 다른 블록으로 이동하면 무작위성도 변경되므로 전리품 상자 게임의 승자가 자주 변경될 수 있습니다.

## 권장 사항

더 큰 `REQUEST_CONFIRMATIONS` 값을 사용하십시오. 안전을 위해 약 60을 제안합니다. 지난 7일 동안 가장 깊은 체인 재구성은 30 블록 미만이었습니다. 60이 게임 사용 사례에 맞지 않을 수 있지만 25-30 미만은 프로젝트 사용자 및 평판에 잠재적으로 위험하다고 생각합니다.

# [H-01] 계약에 승자가 상금을 청구하기에 충분한 잔액이 없을 수 있음

## 심각도

**영향:**
높음, 사용자가 상금 가치를 청구할 수 없으므로

**가능성:**
중간, 소유자가 충불한 잔액을 입력하지 않아야 하므로

## 설명

프로토콜은 `NFTLootbox` 계약의 실제 잔액이 보상을 지급하기에 충분하도록 강제하지 않습니다. 첫 번째 문제는 `createLootbox` 메서드에서 발생합니다. 코드는 다음과 같이 수행합니다:

```solidity
loot.usdPrizes = prizes;
```

그러나 실제로 `prizes` 양의 스테이블 코인을 계약으로 전송하지 않습니다. 단지 잔액으로 가지고 있을 것이라고 예상할 뿐이며, 이는 강제성이 없고 오류가 발생하기 쉽습니다. 또한 계약은 보상으로 제공되는 각 NFT의 가치를 USD 가치로도 보유해야 합니다. 따라서 BAYC NFT가 보상으로 예치된 경우 계약은 BAYC뿐만 아니라 스테이블 코인으로 된 USD 가치도 보유해야 합니다. 승자가 NFT 자체 또는 USD 가치를 청구할 수 있더라도 이 방식은 자본 효율성이 절반으로 줄어듭니다. 이것 또한 강제되지 않습니다.

현재 상황은 계약이 무신뢰(trustless) 상태가 아니라는 것입니다. 승자에게 지급할 보상이 충분하지 않을 수 있습니다.

## 권장 사항

당첨 가능한 모든 보상을 항상 무신뢰 방식으로 청구할 수 있도록 하십시오. 계약이 적어도 모든 가능한 보상의 합계만큼 잔액을 보유하도록 강제하십시오.

# [M-01] 두 사람이 같은 상품을 받을 수 있음

## 심각도

**영향:**
높음, 사람들이 상품을 받지 못할 수 있으므로

**가능성:**
낮음, 동일한 `prizeIndex` 당첨이 필요하므로

## 설명

`NFTLootbox`에서 플레이어가 당첨되었는지와 무엇을 당첨되었는지 결정하는 현재 방식은 `getPrizeIndex` 메서드이며 구현은 다음과 같습니다:

```solidity
uint256[] storage _probabilities = lootboxes[_lootboxId].probabilities;
uint256 sum;

// Calculate the cumulative sum of probabilities and find the winning prize
for (uint256 i; i < _probabilities.length; ++i) {
    sum += _probabilities[i];
    if (_randomNumber <= sum) {
        return i;
    }
}

// If no prize is won, return a missing prize index (100001)
return MAX_PROBABILITY + 1;
```

이것은 `randomNumber`가 작을수록 당첨 확률이 높다는 것을 보여줍니다. 결함은 여러 사람이 작은 `randomNumber`를 뽑아 동일한 `prizeIndex`를 반환받아 동일한 보상을 청구할 수 있다는 것입니다. 이는 또한 `probabilities` 배열이 정렬되도록 강제되지 않는다는 사실에 의해 증폭됩니다. `probabilities` 배열의 첫 번째 값이 크면 승자가 동일한 `prizeIndex`와 상품을 받을 가능성이 더 높습니다.

여러 사용자가 동일한 `prizeIndex`로 동일한 상품을 청구할 수 있으므로 게임이 현재 이를 처리하는 것처럼 보이지만, 이래서는 안 됩니다. 이는 프런트 런(front-running)을 사용하여 나중에 플레이한 사람이라도(동일한 `prizeIndex` 당첨을 얻었다면) 다른 승자로부터 ERC721 토큰을 가져올 수 있기 때문에 첫 번째 사람이 NFT의 `prizeIndex`를 얻기 위한 경쟁 조건(race condition)이 있음을 의미합니다. 또한 USD 기반 상품인 경우 여러 사람이 당첨될 수 있지만 계약이 이 잔액을 보유하도록 강제되지 않습니다. 이는 일부 사람들이 예상 보상을 잃을 수 있음을 의미할 수 있습니다.

## 권장 사항

`prizeIndex` 당 1명의 승자만 강제하고 `probabilities` 배열이 정렬되도록 강제하십시오.

# [M-02] 일부 일반적인 비표준 ERC20 토큰은 프로토콜과 호환되지 않음

## 심각도

**영향:**
중간, 재배포를 위한 가스 이외의 자금 손실로 이어지지 않으므로

**가능성:**
중간, 문서에 해당 토큰이 나열되어 있으므로

## 설명

`NFTLootbox`의 코드는 ERC20의 `transfer` 및 `transferFrom` 메서드를 직접 사용하고 있습니다. 여기에는 두 가지 문제가 있습니다:

1. 일부 토큰은 `transfer` 또는 `transferFrom` 실패 시 되돌리지(revert) 않고 false를 반환합니다 (`ZRX`가 예시).
2. 일부 토큰은 `transfer` 또는 `transferFrom` 호출 시 bool을 반환하지 않습니다 (`USDT`, `BNB`, `OMG`가 예시).

애플리케이션은 이 중 하나와 호환되지 않습니다. 더 문제가 되는 것은 `USDT`인데, 이러한 결함이 있는 것으로 널리 알려져 있고 실제로 문서에 직접 나열되어 있기 때문입니다. 그럼에도 Ethereum과 다른 Polygon상의 `USDT` 토큰 [구현 코드](https://polygonscan.com/address/0xba2a995bd4ab9e605454ccef88169352cd5f75a6#code)를 살펴보면 문제가 존재하지 않는 것 같습니다. 이것이 이 문제가 중간 심각도로만 표시된 이유이지만 더 안전하게 처리하려면 처리가 필요합니다.

## 권장 사항

OpenZeppelin의 `SafeERC20` 라이브러리와 `safeTransfer`/`safeTransferFrom` 메서드를 사용하십시오.

# [M-03] 전리품 상자 입력 검증이 존재해야 함

## 심각도

**영향:**
높음, NFT가 계약에 영원히 갇힐 수 있으므로

**가능성:**
낮음, `NFTLootbox` 소유자의 실수(fat-finger) 또는 잘못된 구성이 필요하므로

## 설명

`_priceForPlay` 및 `_duration` 값은 모두 `NFTLootbox::createLootbox`에서 적절하게 검증되어야 합니다. `_priceToPlay`는 상한선이 있어야 합니다. 너무 크면 아무도 참여하고 싶어 하지 않을 것이며 기간이 지날 때까지 NFT가 계약에 갇히게 됩니다. `_duration` 값의 경우 너무 낮은 기간은 의미가 없지만 너무 긴 기간은 NFT를 계약에 영원히 갇히게 할 수 있으므로 하한 및 상한이 있어야 합니다. 소유자가 실수로 기간에 한두 자릿수를 더하면 큰 문제가 될 수 있습니다.

## 권장 사항

`createLootbox` 메서드에서 `_duration`에 대한 하한 및 상한 확인과 `_priceForPlay`에 대한 최대 값 확인을 추가하십시오.

# [M-04] 코드에 중앙 집중화 공격 벡터가 존재함

## 심각도

**영향:**
높음, 일부 계정은 러그 풀(rug pull)을 실행하거나 게임을 먹통(brick)으로 만들 수 있으므로

**가능성:**
낮음, 악의적이거나 침해된 소유자/관리자 계정이 필요하므로

## 설명

`NFTLootbox` 및 `VRFv2Consumer` 계약의 소유자 계정은 게임이 실행되는 동안 게임을 망가뜨릴 수 있는 권한이 있습니다.

- `NFTLootbox::withdrawERC20`은 계약 소유자가 계약에서 모든 보상 토큰을 인출하여 러그 풀을 실행하는 데 사용할 수 있습니다.
- `NFTLootbox::withdrawERC721`은 전리품 상자가 닫혔지만 게임 승자가 아직 청구하려고 할 때 ERC721 토큰을 훔치는 데 사용할 수 있습니다.
- `VRFv2Consumer::requestRandomWords`는 `NFTLootbox`에서만 호출할 수 있어야 합니다. 현재는 `NFTLootbox`에서 호출하는 것이 금지될 수 있어 실행 중인 게임에서 DoS가 발생할 수 있습니다.
- `NFTLootbox::changeVrfV2Consumer`는 무작위성 공급자를 관리자가 제어하는 공급자로 업데이트하기 위해 악의적으로 사용될 수 있습니다.
- `NFTLootbox::changeBetCoin`은 게임이 실행되는 동안 호출할 수 없어야 합니다. 스테이블 코인이 아니면 `priceForPlay`가 매우 달라질 수 있기 때문입니다.

## 권장 사항

특별한 조건에서만 호출할 수 있도록 하거나 특정 인수를 사용하여 이러한 메서드의 사용을 제한하십시오.

# [L-01] 계약이 `safeTransferFrom` 메서드로 전송된 NFT를 받을 수 없음

`NFTLootbox` 계약은 ERC721 토큰을 수신하고 보유할 것으로 예상되지만 일부 스마트 지갑이나 계약은 수신자가 계약인 경우 `onERC721Received` 메서드를 확인하는 `safeTransferFrom`을 사용하여 nft를 보냅니다. 이를 처리하기 위해 OpenZeppelin에서 `ERC721Holder`를 확장(extend)하십시오.
