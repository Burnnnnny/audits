# 정보 (About Pashov Audit Group)

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**ethena** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Ethena 소개 (About ethena)

**첫 번째 보안 검토에서 복사됨**

Ethena 프로토콜은 Ethereum에 배포된 수익 창출 속성을 가진 합성 달러인 `USDe`를 구축하고 있습니다. 스테이블코인은 은행 시스템 내의 담보 없이 100% 담보화되며, USDC, stETH 및 기타 LSD를 담보로 사용합니다. 수익은 `stETH`와 차익 거래에서 나올 것으로 예상됩니다. `USDe` 스마트 계약의 발행(minting) 및 상환(redeeming)은 Ethena 팀에 의해 신뢰할 수 있는 방식으로 처리됩니다.

[추가 문서](https://ethena-labs.gitbook.io/ethena-labs/Fy1XpH0vy9LnSDCMdikd/)

**계속됨**

프로토콜은 이제 사람들이 Ethena 풀에 유동성을 제공하도록 장려하기 위해 `ENA` 거버넌스 토큰과 LP 스테이킹 기능을 추가했습니다.

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

**_검토 커밋 해시_ - [d74901ab42048e142ad53cf4cdfa98a5a82c4ef7](https://github.com/ethena-labs/ethena/tree/d74901ab42048e142ad53cf4cdfa98a5a82c4ef7)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `EthenaLPStaking`
- `ENA`

# 발견 사항 (Findings)

# [L-01] 동일한 사용자에 대해 여러 쿨다운이 관리되지 않음

`unstake()` 시 특정 사용자는 `cooldownStartTimestamp`를 업데이트합니다.

```solidity
...
StakeData storage stakeData = stakes[msg.sender][token];
...
stakeData.cooldownStartTimestamp = uint104(block.timestamp);
```

이는 사용자의 다른 언스테이크 요청이 병렬로 진행되지 않음을 의미합니다. 마지막 언스테이크만 이전의 모든 보류 중인 언스테이크를 누적하며, `마지막 언스테이크 시간 + stakeParameters.cooldown`에 출금할 수 있습니다.
또한 충분한 `쿨다운`을 기다려 출금 준비가 된 언스테이크의 경우에도 새로운 언스테이크가 호출되면 새로운 `쿨다운`을 기다려야 합니다.

이 동작이 바람직하지 않은 경우, 모든 언스테이크가 자체 저장소(storage)를 갖는 언스테이크 및 출금 대기열을 별도로 관리하는 것을 고려하십시오.
