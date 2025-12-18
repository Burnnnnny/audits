# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**FydeTreasury/core-refactored** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Fyde 소개 (About Fyde)

Fyde 프로토콜은 사용자가 다양한 자산 풀에 예치하도록 장려하는 동시에 거버넌스 권한을 유지하고 할당에 대한 통제권을 보존할 수 있도록 하는 재무 관리 솔루션입니다. 토큰은 각 자산의 시장 성과를 추적하고 풀에 대한 동적 가중치 메커니즘을 구현하는 포트폴리오 관리 전략을 구성합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 희박하거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [0ae92bfe60c660f56747009a6c3ef3b4013eb0ee](https://github.com/FydeTreasury/core-refactored/tree/0ae92bfe60c660f56747009a6c3ef3b4013eb0ee)**

**_수정 검토 커밋 해시_ - [5ddd2edd31ad44c022652fc05f5bcb9f910a8178](https://github.com/FydeTreasury/core-refactored/pull/121/commits/5ddd2edd31ad44c022652fc05f5bcb9f910a8178)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `RelayerV2`
- `TaxModule`
- `OracleModule`
- `QuarantineList`
- `UniswapAdapter`
- `ChainlinkAdapter`
- `Ownable`
- `AccessControl`

# 발견 사항 (Findings)

# [C-01] 공격자가 프로토콜의 AUM을 조작할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

새로운 설계에서 AUM 값은 오프체인 봇에 의해 설정되고 업데이트됩니다. 문제는 봇이 AUM의 절대값을 정의하고 Fyde 계약이 봇이 제공한 값을 기반으로 현재 AUM을 변경한다는 것입니다. 따라서 공격자는 봇의 트랜잭션이 실행될 때 프론트러닝(front-running)하여 현재 AUM을 변경하고 잘못된 AUM 값을 유발할 수 있습니다. 다음은 POC입니다:

1. 현재 AUM이 $100K이고 공격자가 프로토콜에 $20K를 예치했다고 가정합니다.
2. AUM이 $98K로 감소하고 오프체인 봇이 AUM을 $98K로 업데이트하는 트랜잭션을 보냅니다.
3. 멤풀(mempool)을 보고 있던 공격자는 AUM 업데이트 트랜잭션을 보고 프론트런하여 프로토콜에서 $4K를 인출합니다. 이제 프로토콜 AUM은 $96K이고 실제 AUM은 $94K입니다.
4. AUM 업데이트 트랜잭션이 실행되면 AUM은 $98K로 설정되고 프로토콜 AUM과 실제 AUM의 차이는 `98K - 94K = 4K`가 됩니다.
5. 따라서 처음에는 프로토콜의 AUM이 $2K 차이(약 2%)가 났지만 결국 $4K 차이(4% 이상)가 납니다.
6. 공격자는 각 AUM 업데이트에 대해 이 공격을 반복하여 프로토콜의 AUM과 실제 AUM 간의 차이를 더 크게 만들 수 있습니다. 차이가 커짐에 따라 공격자가 토큰을 인출할 때 프로토콜은 더 적은 지분(share)을 계산하게 됩니다.

사실, 각 사용자는 AUM이 감소하고 있기 때문에 AUM 업데이트 트랜잭션 전에 인출할 유인이 있습니다. 따라서 프로토콜은 불안정한 상황에 처하게 되고 실제 AUM과 프로토콜 AUM 간의 차이는 증가할 것입니다.

## 권장 사항

코드는 AUM 차등 업데이트를 허용해야 합니다. 예를 들어, 오프체인 봇은 AUM이 감소하거나 증가해야 하는 양을 설정해야 하며, 이 방식을 사용하면 AUM 값은 항상 올바른 방향으로 변경됩니다.

# [H-01] 사용자는 많은 입금/출금을 작은 부분으로 나누어 세금을 줄일 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

지불해야 할 세금은 입금/출금이 현재 자산 집중도(asset concentration)를 목표 값에서 얼마나 벗어나게 만드는지에 따라 달라지므로, 금액이 클수록 더 큰 세금을 내게 됩니다.
그러나 사용자는 동일한 자산으로 요청 배열을 제공할 수 있으며, 이는 지불해야 할 세금을 크게 줄일 수 있습니다. 예를 들어, 자산 집중도가 너무 낮을 때 이 자산에 대한 입금 세금은 입금을 장려하기 위해 0이 될 수 있습니다.
입금의 델타 집중도를 사용하는 수수료 계산 부분은 동일한 자산이 있는 배열이 제공될 때 올바르게 작동하지 않습니다. 초기 집중도가 사용되기 때문에 반복 시 업데이트되지 않습니다:

```solidity
  function _computeDepositTaxableAmount(
    ProcessParam memory processParam,
    uint256 protocolAUM,
    uint256 totalUsdDeposit
  ) internal pure returns (ProcessParam memory) {
    int256 deltaConc = protocolAUM.toInt()
      * (processParam.currentConc.toInt() - processParam.targetConc.toInt()) / 1e20;
```

결과적으로 델타 집중도가 과소평가되어 의도한 것보다 낮은 세금이 부과됩니다.

[이 테스트들](https://gist.github.com/T1MOH593/c6effcbcbea1a0a13f4aa54c2b2f60a3)에서 금액의 차이를 확인할 수 있습니다.

## 권장 사항

`deposit()` 및 `withdraw()`에 중복 확인을 추가하십시오.

# [M-01] 공격자가 주가를 부풀려 사용자에게 손실을 입힐 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

지분 양이 계산되고 `totalSupply`가 0일 때, 코드는 입금 금액을 사용하고 추가 정밀도를 더하지 않습니다. 이는 공격자가 주가를 최대 `1000 * 1e18`까지 부풀릴 수 있는 능력을 제공하며 라운딩 오류로 인해 예금자에게 손실을 입힐 수 있습니다.

```solidity
  function _convertToShares(uint256 _usdValue, uint256 _usdAUM, uint256 totalTrsySupply)  {
    return totalTrsySupply == 0 ? _usdValue : (_usdValue * totalTrsySupply) / _usdAUM;
  }
```

이런 일이 발생할 수 있는 한 가지 예는 다음과 같습니다:

1. Fyde 토큰 목록에 가중치가 100%인 토큰(T1) 하나가 있고 세금은 없다고 가정합니다. T1 가격은 1입니다.
2. 공격자는 `10 * 1e18` T1 토큰을 Fyde에 예치하고 `10 * 1e18` 지분을 받습니다. Fyde의 AUM은 `10 * 1e18`이 됩니다.
3. 이제 토큰 T1의 가격이 10% 상승하여 1.1이 되고 Fyde의 AUM은 아직 업데이트되지 않아 여전히 `10 * 1e18`입니다.
4. 공격자는 `9.1 * 1e18` T1 토큰을 인출하고 그 가치는 `1.1 * 9.09 * 1e18 = 10 * 1e18`이 되어 AUM과 같으므로 코드는 공격자의 `10 * 1e18` 지분을 소각합니다.
5. 이제 `totalSupply`는 0이지만 실제 AUM은 `1e18`입니다.
6. 공격자는 1 wei T1 토큰을 예치하고 주가는 약 `1e18`이 됩니다.

## 권장 사항

지분에 더 많은 정밀도를 추가하십시오.
function \_convertToShares(uint256 \_usdValue, uint256 \_usdAUM, uint256 totalTrsySupply) {
return totalTrsySupply == 0 ? 10^18 _ \_usdValue : (\_usdValue _ totalTrsySupply) / \_usdAUM;
}

# [M-02] 입금 목록에 중복 토큰이 있는 경우 사용자가 자금을 잃을 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

RelayerV2는 토큰 목록에 중복 항목이 없는지 확인하지 않으며 목록에서 중복 항목을 지원하지 않습니다. 토큰 목록에 중복 항목이 있으면 일부 로직이 설계된 대로 작동하지 않기 때문에 사용자가 자금을 잃을 수 있습니다. 예를 들어 사용자가 `_keepGovRights`를 True로 설정하면 코드는 토큰 목록을 반복하고 RelayerV2의 모든 남은 잔액을 언스테이킹(unstake)하는데, 이로 인해 사용자가 중복 금액을 받지 못하게 됩니다. 다음은 POC입니다:

1. 사용자는 토큰 목록 `[T1, T1]` 및 `_keepGovRights = True`로 `deposit()`을 호출합니다.
2. 코드는 토큰을 Fyde 계약에 예치하고 `2* sharesAfterTax` sTrsy 토큰을 받습니다.
3. 이제 코드는 토큰 목록을 반복하여 사용자 sTrsy를 전송하고 세금 sTrsy를 언스테이킹합니다.
4. 첫 번째 T1의 경우 코드는 `sharesAfterTax`를 전송하고 또한 남은 잔액인 `sharesAfterTax`를 모두 언스테이킹합니다.
5. 두 번째 T2의 경우 잔액이 0이므로 코드는 사용자에게 토큰을 전송하지 않습니다. 따라서 사용자는 `2 * sharesAfterTax`를 받아야 했지만 `sharesAfterTax`만 받게 됩니다.

## 권장 사항

코드가 중복 항목을 지원하도록 하거나 목록에 중복 항목이 없는지 확인하거나 사용자에게 이 위험에 대해 경고하는 확인을 추가하십시오.

# [M-03] `_keepGovRights=True`일 때 `deposit()`에서 슬리피지 확인이 비효율적일 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`deposit()` 함수에서 코드는 `slippageChecker`가 발행된 지분 `sharesToMint`의 합계와 같은지 확인합니다. 문제는 `_keepGovRights = True`일 때 코드가 사용자에게 다른 sTrsy 토큰을 보내고 그 가치에 대한 슬리피지 확인이 없다는 것입니다. 따라서 일부 토큰이 `slippageChecker`보다 높더라도 사용자는 불리한 sTrsy 토큰을 받을 수 있습니다. Fyde의 토큰 가격이 실제 토큰 가격보다 높거나 낮을 수 있고 코드는 해당 토큰에 대해 더 많거나 적은 sTrsy 토큰을 발행하기 때문에 이것은 문제입니다.

또한 코드는 sTrsy 전송 금액을 계산하기 위해 `mint(contract balance, sharesAfterTax)`를 사용하므로 총 전송 금액이 `sharesToMint`보다 낮을 수 있으며 코드는 실제 전송 금액을 `slippageChecker`와 확인해야 합니다.

## 권장 사항

사용자는 받는 각 개별 sTrsy에 대해 슬리피지 금액을 추가할 수 있어야 합니다.

# [M-04] AUM 값 업데이트가 샌드위치 공격을 당해 프로토콜에서 가치를 추출할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

프로토콜은 오프체인 봇이 AUM 값을 업데이트해야 입출금 함수에서 지분 양을 계산할 수 있는데, 공격자는 이를 이용하여 샌드위치 공격을 수행하고 프로토콜에서 가치를 추출할 수 있습니다. 다음은 POC입니다:

1. T1 토큰 가격이 1이고 프로토콜의 `AUM`은 $98K, `totalShare`는 $98K라고 가정합니다.
2. 멤풀을 모니터링하던 공격자는 오프체인 봇이 AUM을 $102K로 설정하는 AUM 업데이트 트랜잭션을 봅니다.
3. 이제 공격자는 이 샌드위치 공격을 수행합니다:
4. AUM 업데이트 전에 공격자는 2K T1 토큰을 예치하고 2K 지분을 받으며 `totalShare`와 `AUM`은 $100K가 됩니다.
5. 그런 다음 AUM 업데이트 tx가 실행되고 AUM은 $102K로 설정됩니다.
6. 이제 공격자는 2.04K T1 토큰을 인출하고 코드는 지분을 `shares = token * price * totalShare / AUM = 2.04 * 1 * 100K / 102K = 2K`로 계산하고 사용자의 2K 지분을 소각하면서 2.04K T1 토큰을 전송합니다.
7. 결국 사용자는 0.04K 토큰을 더 받았습니다.

## 권장 사항

공격을 어렵게 하기 위해 각 사용자에 대해 동일한 블록에서 입출금을 허용하지 마십시오.
전체 블록에 대해 동일한 AUM 값을 사용하십시오.
인출 또는 입금에 지연을 추가하십시오.

# [M-05] 가디언(Guardian)이 RelayerV2의 일시 중지를 해제할 수 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

문서에 따르면 가디언은 프로토콜을 일시 중지/해제할 수 있어야 합니다.

> Guard : 프로토콜을 일시 중지/해제하고 자산을 격리할 수 있음.

문제는 일시 중지 해제 함수의 접근 제어가 가디언이 프로토콜의 일시 중지를 해제하는 것을 허용하지 않는다는 것입니다.

```solidity
  ///@notice Unpause the protocol
  function unpauseProtocol() external onlyOwner {
    paused = false;
    emit Unpause(block.timestamp);
  }

  ///@notice Unpause the swaps
  function unpauseSwap() external onlyOwner {
    swapPaused = false;
    emit Unpause(block.timestamp);
  }
```

## 권장 사항

가디언이 프로토콜의 일시 중지를 해제하도록 허용하십시오.

# [M-06] 자금 손실 또는 잠금을 유발하는 중앙 집중화 위험

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

AUM을 업데이트하는 오프체인 봇과 가격 설정자 역할과 같이 프로토콜이 올바르게 작동하는 데 중요한 여러 역할이 있습니다. 이러한 역할이 손상되거나 예상대로 작동하지 않으면 계약의 중요한 기능이 작동하지 않아 사용자가 자금을 잃을 수 있습니다. 예를 들어:

1. 오프체인 AUM 업데이트 봇이 손상되면 공격자는 각 블록에서 AUM을 2%씩 늘리거나 줄일 수 있으므로 10분 후에는 AUM을 150% 늘리고 위에서 설명한 대로 계약 자금을 거의 모두 인출할 수 있습니다. 이를 방지하려면 코드는 5분 내에 AUM이 10% 이상 변경되지 않도록 하는 등 AUM 변경에 더 많은 제한을 추가해야 합니다.
2. 오프체인 가격 설정자가 손상되면 토큰에 대해 잘못된 수동 가격을 설정할 수 있으며 공격자는 프로토콜과 상호 작용하여 프로토콜 자금을 훔칠 수 있습니다. 이를 피하기 위해 각 토큰의 최소/최대 제한 가격을 자주 업데이트하여 수동 가격 설정자가 더 이상 피해를 입히지 않도록 해야 합니다.

## 권장 사항

오프체인 운영자가 설정할 수 있는 최대/최소 제한을 추가하고 자주 업데이트하십시오. 또한 더 긴 기간의 가격 변경 감지를 추가하십시오. 예를 들어 5분 내에 AUM이 10% 이상 변경되지 않도록 합니다.

# [M-07] OracleModule은 모든 Chainlink 피드의 하트비트가 24시간이라고 가정함

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`OracleModule.sol`에는 체인링크 피드의 스테일(stale) 기간을 설정하는 함수가 있습니다.

```
  function setStalePeriod(uint32 _stalePeriod) public onlyOwner {
    stalePeriod = _stalePeriod;
  }
```

이 스테일 기간은 `latestRoundData()` 호출에서 반환된 `updatedAt` 변수가 최신인지 확인합니다.

```
  function _isStale(uint256 timestamp) internal view returns (bool) {
    return block.timestamp - timestamp <= stalePeriod ? false : true;
  }
```

`stalePeriod`는 대부분의 체인링크 데이터 피드가 24시간 편차 임계값을 갖는다고 가정하여 OracleModule.sol에서 90000초(25시간)로 하드코딩되어 있습니다:

```solidity
  ///@notice Period for checking if chainlink data is stale
  ///@dev At init set at 25 hours, most of the chainlink feed have an heartbeat of 24h
  uint32 public stalePeriod;
```

그러나 일부 자산은 1시간의 하트비트를 가지며, 가장 중요한 것은 ETH/USD입니다: [DefiLlama에 따르면](https://defillama.com/protocol/fyde-protocol) Fyde TVL의 43%가 WETH입니다.

문제는 ETH-USD 및 BTC-USD와 같이 프로토콜에서 사용하는 일부 체인링크 피드의 하트비트가 1시간이라는 것입니다. 이러한 경우 체인링크에서 파생된 가격이 오래된 경우, 프로토콜은 `stalePeriod`를 25시간으로 설정하므로 가격이 건전하다고 가정하여 TRSY에 자산을 예치하거나 TRSY를 소각하여 자산을 얻을 때 잘못된 회계로 이어질 것입니다.

## 권장 사항

프로토콜은 프로토콜과 상호 작용하는 자산을 선택할 수 있는 기능이 있으며 잠재적으로 많은 체인링크 피드를 사용할 것입니다. 모든 Chainlink AggregatorV3Interface를 매핑에 저장하고 AccessControlledOffchainAggregator와 직접 상호 작용하는 대신 다른 하트비트에 대해 다른 `stalePeriod`를 사용하는 것을 고려하십시오.

# [M-08] RelayerV2.withdraw()가 일시 중지되면 사용자는 자산 교환을 위해 TRSY를 소각할 수 없음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

RelayerV2.sol에서 키퍼가 임명한 가드는 `pauseProtocol()`을 통해 프로토콜을 일시 중지할 수 있습니다.

```
  function pauseProtocol() public onlyGuard {
    paused = true;
    emit Pause(block.timestamp);
  }
```

프로토콜이 일시 중지되면 `withdraw()` 및 `governanceWithdraw()`와 같이 `whenNotPaused` 수정자가 있는 함수를 사용할 수 없습니다.

```
  function withdraw(UserRequest[] calldata _userRequest, uint256 _maxTRSYToPay)
    external
    payable
>   whenNotPaused
    onlyUser
  {
```

대부분의 경우 프로토콜이 일시 중지되어도 사용자는 항상 자금을 인출할 수 있어야 합니다. 자금을 인출하기 위해 소유자/가드에게 의존해서는 안 됩니다.

## 권장 사항

`withdraw()` 함수에서 `whenNotPaused` 수정자를 제거할 것을 권장합니다.

# [M-09] 18자리 이상의 자산이 UniswapAdaptor에서 잘못 처리됨

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`_getUniswapPrice()` 함수는 견적(quote) 토큰의 소수점 자릿수가 18자리가 넘을 때 가격을 축소하여 처리해야 합니다. 그러나 `factor`가 `uint`이기 때문에 첫 번째 줄에서 되돌려집니다(revert):

```solidity
  function _getUniswapPrice(address asset, AssetInfo calldata assetInfo, uint32 twapPeriod)
    internal
    view
    returns (uint256)
  {
    uint256 baseAmount = 10 ** assetInfo.assetDecimals;
@>  uint256 factor = (18 - assetInfo.quoteTokenDecimals); // 18 decimals
    uint256 finalPrice;
```

## 권장 사항

다음과 같이 리팩토링하십시오:

```diff
    uint256 baseAmount = 10 ** assetInfo.assetDecimals;
-   uint256 factor = (18 - assetInfo.quoteTokenDecimals); // 18 decimals
+   int256 factor = 18 - int256(int8(assetInfo.quoteTokenDecimals)); // 18 decimals
    uint256 finalPrice;

    uint32[] memory secondsAgos = new uint32[](2);
    secondsAgos[0] = twapPeriod;
    secondsAgos[1] = 0;

    try IUniswapV3Pool(assetInfo.uniswapPool).observe(secondsAgos) returns (
      int56[] memory tickCumulatives, uint160[] memory
    ) {
      int24 tick = _computeTick(tickCumulatives, twapPeriod);

      int256 price = OracleLibrary.getQuoteAtTick(
        tick, baseAmount.to128(), asset, assetInfo.uniswapQuoteToken
      ).toInt();

      finalPrice = factor > 0
-       ? price.upscale(factor).toUint()
-       : price.downscale((-factor.toInt()).toUint()).toUint();
+       ? price.upscale(factor.toUint()).toUint()
+       : price.downscale((-factor).toUint()).toUint();
      return finalPrice;
    } catch {
      return finalPrice;
    }
  }
```

# [M-10] 공격자가 ERC777 토큰으로 RelayerV2에 재진입하여 자금을 훔칠 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

RelayerV2 계약에는 재진입 방지(reentrancy guard)가 없으며 입금 또는 출금 토큰 중 하나에 훅(ERC777 또는 ...)이 있는 경우, 상태가 잘못된 동안 RelayerV2 계약에 다시 진입하여 자금을 훔칠 수 있습니다. 공격자가 이를 악용할 수 있는 방법은 여러 가지가 있지만, 한 가지 방법은 현재 작업에 대해 토큰의 이전 캐싱 가격을 설정하는 것입니다. 다음은 POC입니다:

1. 토큰 T1이 ERC777이고 전송에 훅이 있으며 Fyde 계약에서 사용된다고 가정합니다.
2. 먼저 공격자는 `deposit()`을 호출하여 T1 토큰을 예치합니다.
3. 코드는 `_enableOracleCache(T1)`를 호출하고 OracleModule은 `cacheEnabled = true`를 설정합니다.
4. 이제 `deposit()`이 T1 토큰을 전송하려고 할 때 훅 함수가 호출되고 실행이 공격자 계약에 도달합니다.
5. 이제 공격자는 `deposit()`을 호출하고 T2 및 T3 토큰을 사용하여 예치합니다.
6. 이 `deposit()` 호출에서 코드가 `OracleModule.useCache()`에 도달하면 `cacheEnabled = true`이므로 캐시 가격이 사용되고 T2 및 T3에 대해 설정됩니다(`getPriceInUSD()`는 캐시 가격을 반환).
7. 결국 공격자는 토큰 T2 및 T3에 대해 오래된 캐시 가격을 설정할 수 있었습니다. 공격자는 오래된 캐시 가격이 매우 잘못된 값을 표시할 때마다(예: 과거에 토큰 가격이 매우 높았고 여전히 캐시에 있는 경우) 이를 사용하여 자금을 훔칠 수 있습니다.

이를 악용하는 다른 방법도 있을 수 있습니다.

## 권장 사항

RelayerV2 계약에 재진입 방지를 추가하십시오.

# [M-11] 코드가 오래된 AUM 값을 검증하지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

시스템의 새로운 설계에서 AUM 값은 오프체인 봇에 의해 업데이트되어야 합니다. 문제는 AUM 값에 대한 검증 시간이 없고 코드가 AUM이 유효하다고 가정한다는 것입니다. 이는 어떤 이유로든 오프체인 봇이 AUM 값을 업데이트할 수 없을 때 문제를 일으킬 것입니다. AUM이 업데이트되지 않는 데에는 여러 가지 이유가 있습니다. 예를 들어 이더리움 네트워크가 바쁠 때, 오프체인 봇에 버그가 있거나 손상된 경우입니다. 다음은 이 문제에 대한 POC입니다:

1. AUM이 $100K이고 오프체인 봇이 프로토콜 AUM 값을 업데이트했다고 가정합니다.
2. 오프체인 봇이 다운되고 실제 AUM이 $120K에 도달했지만 프로토콜 상태의 AUM 값은 여전히 $100K라고 가정합니다.
3. 이제 공격자는 프로토콜에 토큰을 예치하고 더 많은 지분을 받을 수 있습니다(프로토콜에서 AUM이 더 낮기 때문).
4. 프로토콜은 AUM이 오랫동안 업데이트되지 않았음에도 불구하고 사용자가 계약과 상호 작용하도록 허용합니다.

## 권장 사항

오프체인 운영자가 설정하고 만료 시간이 있는 오라클의 수동 가격과 같이, 오프체인 봇이 설정한 AUM도 유효 기간(만료 시간)이 있어야 하며 코드는 AUM 값을 사용할 때 이를 확인해야 합니다. AUM 값이 오래된 경우 코드는 상호 작용을 허용해서는 안 됩니다.

# [L-01] 악성 키퍼(Keeper)에 의해 프로토콜이 악용될 수 있음

프로토콜 팀은 키퍼가 악의적이 될 때 최악의 결과가 무엇인지 알고 싶어 합니다. 악성 키퍼가 프로토콜을 어떻게 악용할 수 있는지 살펴보겠습니다.

1부. 키퍼는 관리 대상 자산(AUM)을 업데이트할 수 있지만 업데이트는 설정된 값에서 최대 허용 편차를 초과해서는 안 됩니다. 이를 N이라고 합시다.
2부. 입금/출금에서 세금은 어떻게 계산됩니까? 정액 세금(flat tax)이 항상 적용됩니다. 즉, 고정 비율입니다. 또한 자산이 작업 후 목표 자산 집중도를 초과하면 추가 세금이 적용됩니다. 그러나 공격자가 프로토콜의 균형을 깨지 않는다고 가정하면 정액 세금만 입금 및 출금에 적용됩니다.
3부. 공격 예. 다음 시나리오를 가정합니다:

1. 공격자는 플래시론을 받습니다. 메인넷의 플래시론 잠재력은 약 10억 달러이며, 비교를 위해 현재 Fyde TVL은 200만 달러입니다. 1단계로 인해 그는 입금 시 의도한 것보다 더 많은 양의 TRSY를 받습니다.
2. 공격자는 세금을 최소화하기 위해 올바른 비율로 자산을 예치하므로 `flatTax`만 적용됩니다.
3. 키퍼는 AUM을 최대 허용 값으로 늘립니다.
4. 이제 공격자의 TRSY 토큰은 입금 후 TRSY 공급은 동일하지만 AUM이 증가했기 때문에 이전보다 더 가치가 있습니다.
5. 공격자는 TRSY를 인출하고 `flatTax`를 지불합니다.

이익은 `flatTax`와 `AUMDeviationThreshold`의 현재 값에 따라 달라집니다. `flatTax`가 `AUMDeviationThreshold`에 비해 상대적으로 크면 공격은 수익성이 없습니다.
경제적 분석을 수행하여 올바른 값을 선택하십시오.

# [L-02] RelayerV2와 상호 작용할 때 payable 함수의 위험한 사용

`deposit()`, `withdraw()` 및 `swap()`과 같이 사용자가 상호 작용하는 일부 함수에는 payable 수정자가 있습니다. 프로토콜은 기본 토큰과 상호 작용하지 않는 것 같습니다. 사용자가 실수로 자산과 함께 기본 토큰을 보내면 기본 토큰이 계약에 갇히게 됩니다.

```
  function withdraw(UserRequest[] calldata _userRequest, uint256 _maxTRSYToPay)
    external
    payable
>   whenNotPaused
    onlyUser
  {
```

payable 함수를 제거하거나 계약에 갇힌 기본 토큰을 인출할 수 있는 방법을 갖는 것을 고려하십시오.

# [L-03] ChainlinkAdapter가 라운드 완료를 확인하지 않아 오래된 데이터가 발생할 수 있음

`latestRoundData()`에서 결과를 쿼리할 때 라운드 완료가 검증되지 않습니다. 라운드 완료를 확인하지 않으면 오래된 가격과 잘못된 가격 반환 값 또는 구식 가격이 발생할 수 있습니다.

```
    try FeedRegistryInterface(clRegistry).latestRoundData(asset, clQuoteToken) returns (
      uint80, int256 clPrice, uint256, uint256 updatedAt, uint80
    ) {
```

`latestRoundData()`를 호출하는 모든 함수에 대해 데이터 피드의 라운드 완료를 검증하십시오.

```
    try FeedRegistryInterface(clRegistry).latestRoundData(asset, clQuoteToken) returns (
+     uint80 roundID, int256 clPrice, uint256, uint256 updatedAt, uint80 answeredInRound
    ) {
+   require(answeredInRound >= roundID, "round not complete");
```

# [L-04] 첫 번째 예금자는 항상 세금을 냄

모든 자산에 대해 목표 집중도와 현재 집중도가 있습니다. 프로토콜 AUM이 0이면 자산의 현재 집중도는 0이 됩니다. 첫 번째 예금자가 자산을 예치하고 목표 집중도가 100%가 아니면 세금이 부과됩니다. 세금을 내지 않는 유일한 방법은 모든 목표 집중도 세트와 동일한 자산을 예치하는 것입니다.

세금이 발생하지 않도록 예치하기 전에 사용자가 자산의 위험과 목표 집중도를 알고 있는지 확인하십시오.

`_getTargetConcentrationDeposit` 감사는 첫 번째 예금자에 대해 targetConcDeposit이 100%인지 확인해야 합니다.

이 문제를 방지하는 가장 좋은 방법은 프로토콜 자체가 먼저 재무부에 자금을 지원하고 첫 번째 예금자가 되는 것입니다.

# [L-05] 입금 및 출금에 대한 목표 집중도가 100%와 동일한지 확인되지 않음

소유자는 화이트리스트 자산에 대한 세금 매개변수를 설정하고 `setTaxParams()` 함수에서 각 자산에 대한 `targetConcDeposit` 및 `targetConcWithdraw`를 나타냅니다.

```
  function setTaxParams(address[] calldata _assets, TaxParams[] calldata _taxParams)
    external
    onlyOwner
  {
    for (uint256 i; i < _assets.length; ++i) {
      taxParams[_assets[i]] = _taxParams[i];
    }
  }
```

`setTaxParams()`는 모든 자산의 목표 집중도 합계가 100%인지 확인하지 않습니다. 예를 들어 WBTC, WETH 및 USDC가 화이트리스트에 있는 경우 각각에 대해 설정된 목표 집중도는 합계가 100%(예: 40%, 40%, 20%)여야 합니다. 함수에 확인이 없으므로 소유자는 잠재적으로 각 자산의 목표 집중도를 100%, 100%, 100%로 설정하여 총 300%가 되게 하여 프로토콜의 회계를 깨뜨릴 수 있습니다.

모든 자산의 `targetConcDeposit` 및 `targetConcWithdraw`가 항상 100%와 같은지 확인하십시오.

# [L-06] 체인링크 가격을 사용할 수 없을 때 공격자가 프로토콜에서 가치를 추출할 수 있음

체인링크 가격 피드를 사용할 수 없는 경우 OracleModule은 Uniswap TWAP 가격 또는 수동으로 지정된 가격을 사용합니다. 이 시간 동안 토큰 가격이 급격하게 변동하면 공격자가 프로토콜에서 가치를 추출할 수 있습니다. 다음은 POC입니다:

1. T1 토큰에 대한 체인링크 가격 피드가 중단되고 Uniswap TWAP 가격은 1이며 프로토콜은 이 가격을 사용한다고 가정합니다.
2. 갑자기 T1의 실시간 가격이 0.9로 떨어지지만 Uniswap TWAP 가격은 여전히 1입니다.
3. 이제 공격자는 T1 토큰을 프로토콜에 예치할 수 있으며 프로토콜은 예치된 T1 토큰에 대해 더 높은 가치를 계산(TWAP 가격이 뒤쳐지기 때문)하고 공격자에게 더 많은 지분을 발행합니다.
4. 이 시간 동안 오프체인 봇이 AUM을 업데이트하고 새로운 T1 가격을 기준으로 AUM을 줄이면 공격은 더 큰 영향을 미칩니다.

공격은 수동 가격으로도 가능합니다(공격자는 수동 가격 업데이트를 프론트런할 수 있음).

이에 대한 쉬운 해결책은 없습니다. 30분 TWAP 가격 범위에 있도록 더 짧은 TWAP 기간(예: 30초)을 확인하십시오.
