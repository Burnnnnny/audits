# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Reya-Labs/reya-network** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Reya Network 정보

Reya Network는 무기한 선물(perpetuals)을 위해 거래 최적화된 모듈형 L2입니다. 체인 계층은 Arbitrum Orbit에 의해 구동되며 가스 요금이 없고 트랜잭션은 FIFO 기준으로 주문됩니다. 프로토콜 계층은 PnL 결제, 증거금 요구 사항, 청산과 같은 거래를 지원하기 위해 체인을 모듈형 구성 요소로 분해하여 DeFi 애플리케이션의 수직적 통합을 직접 해결합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 희박하거나, 인센티브가 거의 또는 전혀 없이 공격자가 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정하는 것이 좋음

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [71f999550540da759355f042c7efdf15fd19ed63](https://github.com/Reya-Labs/reya-network/tree/71f999550540da759355f042c7efdf15fd19ed63)

_수정 검토 커밋 해시_ - [accd2420aa24a8424172b175ef4cf253a610358b](https://github.com/Reya-Labs/reya-network/tree/accd2420aa24a8424172b175ef4cf253a610358b)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `OracleAdaptersProxy`
- `DataTypes`
- `Errors`
- `Events`
- `FeatureFlagSupport`
- `ConfigurationModule`
- `FeatureFlagModule`
- `OwnerUpgradeModule`
- `StorkERC7412WrapperModule`
- `StorkPriceInformationModule`
- `Configuration`
- `StorkPrice`
- `NodeModule`
- `FallbackReducerNode`
- `StorkOffchainLookupNode`
- `NodeDefinition`

# 발견 사항

# [M-01] Stork 오라클 가격이 재정 거래(arbitrated)될 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`StorkERC7412WrapperModule.fulfillOracleQuery`는 Stork 오라클의 가격을 업데이트하는 데 사용됩니다.

오라클이 푸시 모델로 작동한다는 점을 감안할 때, 이는 동일한 트랜잭션 내에서 거래를 수행하고 새 가격을 설정하고 포지션을 닫음으로써 가격 업데이트를 재정 거래할 수 있는 기회를 엽니다.

게다가 동일한 블록에서 가격을 여러 번 업데이트할 수 있으므로 공격자는 다음 조건을 충족하는 한 초기 가격과 최종 가격을 모두 선택할 수 있습니다.

- 초기 가격 타임스탬프가 이전 가격 타임스탬프와 같거나 큽니다.
- 최종 가격 타임스탬프가 초기 가격 타임스탬프와 같거나 큽니다.

이는 변동성이 큰 순간에 수익성 있는 재정 거래를 수행할 가능성을 높입니다.

`FeatureFlagSupport.ensureExecutorAccess()`는 호출자가 가격 업데이트를 수행하도록 허용되었는지 확인하고 프로토콜은 가격 업데이트를 신뢰할 수 있는 엔터티로 제한하기 시작하지만, 실행자 기능 플래그에 대해 `allowAll`을 `true`로 설정하여 나중에 누구나 이 작업을 수행할 수 있도록 허용할 계획이라는 점에 유의하십시오.

## 권장 사항

Stork 가격이 블록당 한 번 이상 업데이트되지 않고 새 가격이 오래된 가격이 아닌지 확인하십시오. 다음은 이를 수행할 수 있는 방법에 대한 참조 구현입니다.

```diff
File: StorkPrice.sol

library StorkPrice {
    struct Data {
        StorkPricePayload latestPrice;
+       uint256 updatedAtBlock;
    }

(...)

+   function getLatestPriceUpdatedAtBlock(string memory assetPairId) internal view returns (uint256) {
+       return load(assetPairId).updatedAtBlock;
+   }
}
```

```diff
File: StorkERC7412WrapperModule.sol

        if (latestPrice.timestamp > pricePayload.timestamp) {
            revert Errors.StorkPayloadOlderThanLatest(pricePayload, latestPrice);
        }
+
+       uint256 latestPriceUpdatedAtBlock = StorkPrice.getLatestPriceUpdatedAtBlock(pricePayload.assetPairId);
+       // Do not revert if the price has already been updated in the current block, as users are expected to
+       // do a multicall to update the price and perform a trade in the same transaction. If the price has already
+       // been updated in the current block, we can continue.
+       if (latestPriceUpdatedAtBlock == block.number) {
+           return;
+       }
+
+       if (pricePayload.timestamp + MAX_PAYLOAD_DELAY < block.timestamp) {
+           revert Errors.StorkPayloadStaleData(pricePayload);
+       }
+
-       StorkPrice.load(pricePayload.assetPairId).latestPrice = pricePayload;
+       StorkPrice storage storkPrice = StorkPrice.load(pricePayload.assetPairId);
+       storkPrice.updatedAtBlock = block.number;
+       storkPrice.latestPrice = pricePayload;
```

Stork 오라클 가격이 소비될 때 가격이 현재 블록에서 업데이트되었는지 확인하십시오.

```diff
File: NodeDefinition.sol

    function isPriceFresh(bytes32 id, uint256 timestamp) internal view returns (bool) {
+       NodeDefinition.Data memory nodeDefinition = NodeDefinition.load(id);
+       if (nodeDefinition.nodeType == NodeType.STORK_OFFCHAIN_LOOKUP) {
+           uint256 latestPriceUpdatedAtBlock = StorkPrice.getLatestPriceUpdatedAtBlock(pricePayload.assetPairId);
+           return latestPriceUpdatedAtBlock == block.number;
+       }
+
        Data storage storedConfig = load(id);
```

# [L-01] 가격 가져오기가 되돌려지면 RedstoneNode가 가격 계산을 진행함

`RedstoneNode.getAveragePrice` 함수는 시간 간격 동안 TWAP 가격을 계산해야 합니다. 이를 위해 라운드에 대한 정보를 가져오고 오래되지 않은 경우 가격을 포함하여 평균을 계산합니다.

```solidity
while (latestRoundId > 0) {
    try redstone.getRoundData(--latestRoundId) returns (
        uint80, int256 answer, uint256, uint256 updatedAt, uint80
    ) {
        if (updatedAt < startTime) {
            break;
        }
        if (answer < 0) {
            revert NegativePrice(answer, redstone);
        }
        priceSum += answer.toUint();
        priceCount++;
    } catch {
        break;
    }
}
```

`redstone.getRoundData(--latestRoundId)`가 되돌려지는 경우 함수는 되돌리는 대신 라운드 루프를 중단합니다. 결과적으로 가격은 원래보다 적은 수의 소스에서 계산됩니다.

# [L-02] 존재하지 않는 노드에 대해 최대 비활성 기간(max stale duration)을 설정할 수 있는 기능

`owner`는 존재하지 않는 노드에 대해 `maxStaleDuration`을 설정할 수 있습니다.

```solidity
    function setMaxStaleDuration(bytes32 nodeId, uint256 maxStaleDuration) external {
        OwnableStorage.onlyOwner();
        NodeDefinition.setMaxStaleDuration(nodeId, maxStaleDuration);

        emit MaxStaleDurationUpdated(nodeId, maxStaleDuration);
    }
```

이를 통해 새로 생성된 노드가 0이 아닌 `maxStaleDuration` 값을 가질 수 있습니다. 매개변수를 수정하기 전에 노드의 존재를 검증하는 것이 좋습니다.
