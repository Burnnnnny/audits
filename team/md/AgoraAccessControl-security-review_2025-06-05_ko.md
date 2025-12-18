# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**agora-finance/contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Agora Access Control 소개

Agora Access Control 계약은 여러 관리자, 업그레이드 가능한 프록시 및 엄격한 액세스 검증을 지원하는 모듈식 역할 기반 권한 시스템을 제공합니다. 역할 관리(`AgoraAccessControl`), 임의 호출 실행(`AgoraAccessControlWithExecutor`), 업그레이드 가능한 프록시 로직 및 액세스 처리(`AgoraProxyAdmin`, `AgoraTransparentUpgradeableProxy`), ERC-1967 프록시 관리자 및 구현 주소를 위한 하위 수준 스토리지 슬롯 액세스 노출(`Erc1967Implementation`)을 위한 구성 요소가 포함됩니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시:_
- [4f1387699378ff67e8d4bf29bd853184c86d1216](https://github.com/agora-finance/contracts/tree/4f1387699378ff67e8d4bf29bd853184c86d1216)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `AgoraAccessControl`
- `AgoraAccessControlWithExecutor`
- `AgoraProxyAdmin`
- `AgoraTransparentUpgradeableProxy`
- `Erc1967Implementation`

# 발견 사항

# [L-01] 관리자 취소 프론트 러닝으로 인해 무단 액세스가 가능하게 유지됨

`AgoraAccessControl.sol`의 공개 `assignRole` 함수는 기존 관리자가 한 번의 원자적 호출로 관리자 역할을 추가하고 제거할 수 있도록 허용하며, *마지막* 관리자를 제거하는 것만 방지합니다. 악의적인 관리자(M₂)는 두 가지 프론트 러닝 벡터를 악용할 수 있습니다.

1. **취소 프론트 러닝 (Revocation Front‐run)**
   M₂는 M₁이 M₂를 취소하려는 보류 중인 작업을 보고 M₁을 먼저 취소하여 이를 프론트 러닝합니다. 이로 인해 M₁의 취소 트랜잭션은 실행되는 순간 되돌려집니다(M₁은 더 이상 관리자가 아니기 때문, 코드 라인 52). M₂는 관리자로 남습니다.
2. **재할당 프론트 러닝 (Reassignment Front‐run)**
   M₂를 제거하려는 시도가 발생하기 전에, M₂는 선제적으로 `assignRole`을 호출하여 새로운 주소(M₃)에 `ACCESS_CONTROL_MANAGER_ROLE`을 부여할 수 있습니다. 나중에 M₂가 취소되더라도 M₃는 관리자로 남아 있어 제거를 효과적으로 회피합니다.

```solidity
50: function assignRole(string memory _role, address _newAddress, bool _addRole) public virtual {
51:     // Checks: Only Admin can transfer role
52:@>   _requireIsRole({ _role: ACCESS_CONTROL_MANAGER_ROLE, _address: msg.sender });
53:
54:@>   _assignRole({ _role: _role, _newAddress: _newAddress, _addRole: _addRole });
55:@>   if (
56:         bytes(_role).length == bytes(ACCESS_CONTROL_MANAGER_ROLE).length &&
57:         keccak256(bytes(_role)) == keccak256(bytes(ACCESS_CONTROL_MANAGER_ROLE)) &&
58:         _getPointerToAgoraAccessControlStorage().roleMembership[_role].length() == 0
59:     ) revert CannotRemoveLastManager();
60: }
```

* **라인 52**에서 계약은 호출자가 관리자 역할을 보유하고 있는지 확인합니다.
* **라인 54**에서 추가 또는 제거를 수행합니다.
* **라인 55–59**의 가드는 *마지막 관리자를 제거*하는 것만 방지할 뿐, 새 주소로의 재할당은 막지 않습니다.

다음 시나리오를 고려해 보십시오.

1. **초기 상태:** `managers = [M₁, M₂]`.
2. M₁이 트랜잭션 제출 (보류 중):
   ```solidity
   assignRole("ACCESS_CONTROL_MANAGER_ROLE", M₂, false);
   ```
3. **재할당 프론트 러닝:** M₂가 다음으로 선점:
   ```solidity
   assignRole("ACCESS_CONTROL_MANAGER_ROLE", M₃, true);
   ```
   * 트랜잭션 후: `managers = [M₁, M₂, M₃]`.
4. 2단계의 트랜잭션이 실행됨:
   ```solidity
   assignRole("ACCESS_CONTROL_MANAGER_ROLE", M₂, false);
   ```
   * 트랜잭션 후: `managers = [M₁, M₃]`.
5. **결과:** M₂는 관리자로 남고 자신의 통제하에 있는 공동 관리자 M₃를 갖게 되어 제거 시도를 회피합니다.

권장 사항:
"재할당 프론트 러닝" 문제에 대해 어떤 주소로든 관리자 역할을 부여하는 것을 허용하지 마십시오. "취소 프론트 러닝" 문제의 경우 관리자 간의 즉각적인 취소를 허용하지 않고 관리자 역할에 대한 더 나은 메커니즘을 갖추는 것이 가장 좋습니다.

