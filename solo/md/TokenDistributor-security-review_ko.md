# 정보
 Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. 그의 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항
 스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개
 **token-distributor** 리포지토리에 대해 시간 제한이 있는 보안 검토를 **pashov**가 수행했으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Token Distributor 소개
 이 프로토콜은 ETH와 교환하여 ERC20 토큰의 배포를 용이하게 하는 데 사용됩니다. **Florence Finance** 생태계의 일부입니다. 사용자가 ETH를 기부하면 프로토콜 소유자가 제어하는 Safe 지갑으로 직접 전송되고, 미리 정의된 환율에 따라 토큰을 돌려받습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향(Impact)

- 높음(High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간(Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음(Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성(Likelihood)

- 높음(High) - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 도난당하거나 분실될 수 있는 자금의 양에 비해 공격 비용이 비교적 낮습니다.

- 중간(Medium) - 조건부 인센티브가 있는 공격 벡터일 뿐이지만 여전히 발생할 가능성이 비교적 높습니다.

- 낮음(Low) - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적(Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음(High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간(Medium) - 수정해야 함

- 낮음(Low) - 수정할 수 있음

# 보안 평가 요약
 **_검토 커밋 해시_ - [3b5d0c74c2375815f313d9a5616ecf8068739e7c](https://github.com/florence-finance/token-distributor/tree/3b5d0c74c2375815f313d9a5616ecf8068739e7c)**

**_수정 검토 커밋 해시_ - [eb02818e559bc2e20b0169497b0563e76d39c0f3](https://github.com/florence-finance/token-distributor/tree/eb02818e559bc2e20b0169497b0563e76d39c0f3)**

# 결과
 # [M-01] 초기화 시 DoS 공격 가능함

## 심각도

**영향:**
높음, 초기화가 차단될 수 있음

**가능성:**
낮음, 공격자에게 이득이 없는 프론트러닝 유형의 공격임

## 설명

`TokenDistributor`의 `initializeDistributor` 메서드에는 다음 확인이 있습니다:

```solidity
require(token.balanceOf(address(this)) == _totalTokensToDistribute, "totalTokensToDistribute must match token balance of contract");
```

이는 소유자가 계약에 예를 들어 10개의 토큰을 미리 전송한 다음 `initializeDistributor`를 호출할 때 `_totalTokensToDistribute` 인수를 10으로 설정할 것이라는 예상을 줍니다. 이 문제점은 악의적인 사용자가 1 wei 가치의 `token`을 계약으로 전송하여 소유자 호출을 프론트러닝 하는 경우, 잔액이 더 이상 동일하지 않으므로 확인 및 트랜잭션이 되돌아간다는(revert) 것입니다.

## 권장 사항

코드를 다음과 같이 변경하십시오:

```diff
- require(
-    token.balanceOf(address(this)) == _totalTokensToDistribute,
-    "totalTokensToDistribute must match token balance of contract"
- );
+ token.safeTransferFrom(msg.sender, address(this), _totalTokensToDistribute);
```

그리고 계약에 토큰을 미리 전송하지 마십시오.

# [L-01] 불충분한 입력 검증

`initializeDistributor`에서 여러 매개변수가 불충분하게 검증되었습니다:

- `_maxEthContributionPerAddress`는 > 0인지만 확인되지만, 이는 너무 낮은 제한인 것 같습니다. 아마도 1e18을 사용할 수 있습니다.
- `_distributionStartTimestamp`는 `>= block.timestamp`인지 확인되지만 너무 먼 미래가 아닌지는 확인되지 않습니다.
- `_distributionEndTimestamp`는 `> _distributionStartTimestamp`인지 확인되지만 너무 먼 미래가 아닌지는 확인되지 않습니다.

`_maxEthContributionPerAddress`의 경우 예를 들어 `1e18`과 같이 더 큰 하한을 사용하는 것을 고려하십시오. `_distributionStartTimestamp`의 경우 사용 사례에 따라 미래에 1주, 1달 또는 1년 이상 떨어져 있지 않은지 확인하고, `_distributionEndTimestamp`의 경우 시작 타임스탬프 후 (다시) 1주, 1달 또는 1년 이상 떨어져 있지 않은지 확인하십시오.

