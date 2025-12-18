# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**veildotcash/veil_contracts_audit** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# Veil Cash 소개

Veil Cash는 Tornado Cash의 포크이며 Base 레이어 2(L2) 블록체인에 배포됩니다. zk-SNARK(Zero-Knowledge Succinct Non-Interactive Arguments of Knowledge)를 활용하여 사용자가 온체인 개인 정보 보호 및 익명성을 달성할 수 있도록 합니다. Tornado Cash와의 주요 변경 사항은 Veil이 입금에 프록시 계약을 사용하고, 업그레이드가 VeilValidator.sol을 통해 관리되며, 온체인 사용자 확인 및 특정 예금자 화이트리스트 메커니즘이 포함된다는 점입니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 비교적 가능성이 높습니다.

- 낮음 (Low) - 너무 많거나 비현실적인 가정을 필요로 하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준별 요구 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (이미 배포되지 않은 경우 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [326f141c0a0e8ebd14dccd3d93ecd61ae48e7e69](https://github.com/veildotcash/veil_contracts_audit/tree/326f141c0a0e8ebd14dccd3d93ecd61ae48e7e69)

_수정 검토 커밋 해시_ - [e67267a65f5c17ffbf8305e284be2f75283fb4c5](https://github.com/veildotcash/veildotcash_contracts/tree/e67267a65f5c17ffbf8305e284be2f75283fb4c5)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `Veil_0005_ETH`
- `Veil_001_ETH`
- `Veil_005_ETH`
- `Veil_01_ETH`
- `Veil_1_ETH`
- `VeilValidatorV5`
- `iVerify`
- `verify`

# 발견 사항

# [L-01] 예금자 수가 실수로 증가할 수 있음 (Depositor count can accidentally increase)

VeilValidatorV4에서 `veilManager`는 허용된 예금자를 설정할 수 있습니다. 만약 `veilManager`가 실수로 동일한 `_depositor`를 true로 두 번 이상 설정하면 `depositorCount`가 증가합니다.

```
 function setAllowedDepositor(address _depositor, bool _isAllowed, string memory _details) public {
        if (msg.sender != veilManager) revert OnlyVeilManager();
>       allowedDepositors[_depositor] = _isAllowed;
        depositorDetails[_depositor] = _details;
>       if (_isAllowed) {
            depositorCount++;
        } else {
            depositorCount--;
        }
        emit DepositorStatusChanged(_depositor, _isAllowed, _details);
    }
```

이러한 문제를 방지하려면 카운트를 늘리거나 줄이기 전에 예금자가 설정되거나 설정 해제되었는지 확인하십시오. 다음과 같이:

```
 function setAllowedDepositor(address _depositor, bool _isAllowed, string memory _details) public {
        if (msg.sender != veilManager) revert OnlyVeilManager();

       if (_isAllowed) {
+           if(allowedDepositors[_depositor] != _isAllowed){
            depositorCount++;
        }
        } else {
+        if(allowedDepositors[_depositor] != _isAllowed){
            depositorCount--;
        }
        }

        allowedDepositors[_depositor] = _isAllowed;
        depositorDetails[_depositor] = _details;
        emit DepositorStatusChanged(_depositor, _isAllowed, _details);
    }
```

# [L-02] 올바르지 않은 이벤트 방출 (Incorrect event emissions)

`VeilValidatorV4::deposit005ETH` 함수는 잘못된 `poolSize`로 `Deposited` 이벤트를 잘못 방출합니다. `005` 풀은 풀 ID 4에 해당하지만 이벤트는 ID 0으로 방출됩니다.

```diff
- emit Deposited(msg.sender, 0, depositAmount, fee);
+ emit Deposited(msg.sender, 4, depositAmount, fee);
```

또한 `VeilValidatorV4::setRewardsTracker`, `VeilValidatorV4::setVeilVerifiedOnchain` 및 `Veil_005_ETH::updateValidatorContract`는 중요한 상태 변경 함수이지만 수행하는 업데이트를 기록하기 위한 이벤트를 방출하지 않습니다.

마지막으로 `VeilValidatorV4::VeilTokenAmountSet` 이벤트와 `Veil_005_ETH::UpdateVerifiedDepositor` 이벤트는 사용되지 않으므로 계약 크기와 가스 비용을 줄이기 위해 안전하게 제거할 수 있습니다.

# [L-03] 플래시론을 사용하여 잔액 요구 사항 우회 (Using flashloans to bypass balance requirement)

예금자는 각 풀에 입금하기 위해 특정 양의 `VEIL` 토큰을 보유해야 합니다. 각 풀에는 고유한 필수 금액이 있으며, 이 확인은 `VeilValidatorV4::_hasVeil` 함수에 의해 시행됩니다:

```solidity
function _hasVeil(address _depositor, uint8 _poolSize) internal view returns (bool) {
     return veilToken.balanceOf(_depositor) >= poolVeilAmount[_poolSize];
}
```

그러나 사용자는 플래시론(flash loan)을 사용하여 이 요구 사항을 우회할 수 있습니다. 입금 직전에 `VEIL` 토큰을 빌리고 동일한 블록 내에서 갚는 것입니다. 이를 통해 사용자는 실질적인 약속 없이 `_hasVeil` 확인을 충족할 수 있어 입금 요구 사항을 약화시킵니다.

이 공격을 방지하려면 시간 기반 검증 메커니즘을 사용하여 예금자가 입금 전 충분한 기간 동안 필요한 `VEIL` 잔액을 유지하도록 보장하는 것을 고려하십시오.
