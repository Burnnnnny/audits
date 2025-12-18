# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **mass-core-tetris** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Mass 정보 (About Mass)

Mass Smart Accounts는 계정 추상화(account abstraction) 프로토콜인 Tetris를 기반으로 생성된 스마트 계약 지갑입니다. 계정 추상화는 HyVM이라는 이더리움 가상 머신(EVM) 하이퍼바이저를 사용하여 스마트 계약 계층에서 달성됩니다. 이를 통해 배포된 로직 없이도 임의의 EVM 바이트코드를 실행할 수 있습니다.

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

**_검토 커밋 해시_ - [ccaafae41483e8cc07ba253e542430705390dae7](https://github.com/MassDotMoney/mass-core-tetris/tree/ccaafae41483e8cc07ba253e542430705390dae7)**

**_수정 검토 커밋 해시_ - [e9edf9d37e4ac82648f37c92005e2825340620ef](https://github.com/MassDotMoney/mass-core-tetris/tree/e9edf9d37e4ac82648f37c92005e2825340620ef)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `ReentrancyGuardUpgradeable`
- `TimelockControllerEmergency`
- `SmartAccountFactoryUtils`
- `Errors`
- `FlashloanerMSA`
- `ImplementationResolver`
- `MassSmartAccount`
- `Proxy`
- `SmartAccountFactory`
- `SmartAccountOwnerResolver`
- `WithdrawerWeth`

# 발견 사항 (Findings)

# [L-01] 불변 Aave 풀 주소 (Immutable Aave pool address)

Aave 문서에 따르면, Aave 풀의 주소는 풀 주소 제공자(pool address provider) 계약에서 가져와야 합니다. 풀 주소 제공자의 주소는 변경되지 않을 것으로 예상되므로 불변입니다. 제안 사항은 [여기](https://docs.aave.com/developers/core-contracts/pooladdressesprovider#pooladdressesprovider) 문서에서 찾을 수 있습니다.

FlashloanerMSA.sol 계약의 불변 변수에 풀 주소를 저장하는 대신, 풀 주소 제공자 계약 주소를 저장하고 `getAddress()` 함수를 호출하여 풀의 주소를 가져오는 것을 고려하십시오.

# [L-02] 일부 함수에서 `msg.value` 확인 누락 (Missing `msg.value` check in some functions)

`MassSmartAccount.sol` 계약의 일부 함수에는 `msg.value == value` 확인이 누락되어 있습니다. 이 확인은 사용자가 함수를 호출할 때 ETH를 보낼 수 있는 함수에서 필요하며, 사용자가 지정한 것보다 더 많은 ETH를 보내게 되어 해당 ETH가 계정에 남게 되는 것을 방지합니다.

이 문제는 `executeCall`, `executeHyVMCall` 및 `performReentrantCall` 함수에 존재합니다. ETH를 처리하는 다른 함수들은 이 확인이 있지만 이 몇 가지 함수들에는 없습니다. 이러한 함수들에 이 확인을 추가하는 것을 고려하십시오.
