# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**defi-app/defi-app-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Defi App 소개 (About Defi App)

DefiApp은 기본 계정 추상화 및 가스 없는 거래와 같은 기능을 제공하여 체인 간에 DeFi 자산을 관리하는 플랫폼입니다. 범위는 VAirdrop 및 Airdrop 계약의 변경 사항과 "롤링" 방식으로 주기적으로 토큰을 에어드롭하고 에어드롭을 청구하기 위해 충족해야 하는 조건을 설정할 수 있는 RollingAirdrop에 중점을 두었습니다.

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

_검토 커밋 해시:_
- [ad0eed6ad1eea90aa34966555f11b01546e294f9](https://github.com/defi-app/defi-app-contracts/tree/ad0eed6ad1eea90aa34966555f11b01546e294f9)
- [4706765931710de87e26d9aa91d45c193aa9c58b](https://github.com/defi-app/defi-app-contracts/tree/4706765931710de87e26d9aa91d45c193aa9c58b)

_수정 검토 커밋 해시:_
- [27b691c4b55758e394fcc15496d278c3d12e4c60](https://github.com/defi-app/defi-app-contracts/tree/27b691c4b55758e394fcc15496d278c3d12e4c60)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `VAirdrop`
- `Airdrop`
- `MFDDataTypes`
- `MFDLogic`
- `IConditioner`
- `Conditioner`
- `ConditionerS1`
- `RollingAirdrop`

# 발견 사항 (Findings)

# [L-01] 함수 `canClaim()`은 계약의 잔액과 일시 중지 상태를 고려하지 않음

함수 `canClaim()`은 사용자가 에어드롭을 청구할 수 있는 경우 true를 반환해야 합니다. 사용자가 에어드롭을 청구할 수 있는지 확인하기 위해 여러 사항을 확인하지만 문제는 함수가 계약 잔액을 확인하지 않는다는 것입니다. 계약의 잔액이 에어드롭 금액보다 낮으면 사용자는 에어드롭 토큰을 청구할 수 없습니다. 또한 계약이 일시 중지되면 토큰을 청구할 수 없으며 이 함수는 계약이 일시 중지되었는지 여부를 확인하지 않습니다. 이러한 조건도 확인하도록 `canClaim()` 코드를 업데이트하거나 잔액이 부족하거나 계약이 일시 중지되었을 때 이 함수 동작을 명확히 하도록 주석을 업데이트하는 것이 좋습니다.

# [L-02] 에어드롭 토큰이 `smartAccount` 수신자가 아닌 `msg.sender`로 이동

계약에는 에어드롭의 의도된 수신자 주소로 설명된 머클 리프(Merkle leaf)에 `smartAccount` 필드가 포함되어 있습니다. 그러나 `claim` 및 `claimAndStake` 함수 모두 스테이킹이 완료된 경우를 제외하고는 `smartAccount` 값에 관계없이 항상 에어드롭 토큰을 `msg.sender`(호출자)에게 직접 전송합니다.

```solidity
    function claim(bytes memory leafElements, bytes32[] calldata merkleProof) external whenNotPaused nonReentrant {
        // ...

        // Transfer the tokens
@>        distributionToken.safeTransfer(msg.sender, claimableAmount);
        emit AirdropRewardsClaim(msg.sender, claimableAmount, globalAmount);
    }
```

# [L-03] `stakeAmount`가 `minDLPBalance()` 미만인 경우 `claimAndStake` 실패

스테이킹 흐름에서 `RollingAirdrop` 계약은 사용자가 `claimAndStake`(`condition`이 true인 경우)를 사용하도록 요구할 수 있습니다. 그러나 스테이킹된 금액이 `IBountyManager($.bountyManager).minDLPBalance()`보다 작으면 `MFDLogic.stakeLogic`의 스테이킹 작업은 이 줄로 인해 무기한 되돌려집니다:

```solidity
    function stakeLogic(
        MultiFeeDistributionStorage storage $,
        uint256 _amount,
        address _onBehalf,
        uint256 _typeIndex,
        bool _isRelock
    ) public {
        if (_amount == 0) return;
        if ($.bountyManager != address(0)) {
@>            if (_amount < IBountyManager($.bountyManager).minDLPBalance()) revert MFDLogic_invalidAmount();
        }
```

즉, 청구에 `claimAndStake`만 사용하도록 허용된 사용자는 금액이 스테이킹 계약의 최소 요구 사항을 충족하기에 너무 낮으면 에어드롭을 받을 수 없으며 전체 청구가 되돌려질 수 있습니다.

스테이킹을 시도하기 전에 스테이크 금액이 최소 스테이킹 계약에서 요구하는 최소 금액 이상인지 확인하는 로직을 에어드롭 계약의 `claimAndStake` 함수에 추가하는 것을 고려하십시오.

# [L-04] 코드는 `smartAccount`를 사용하여 청구된 금액을 추적해야 함

코드는 변수 `claimedAmounts[]`에서 사용자 청구 에어드롭 금액을 추적합니다. 문제는 함수 `claim()` 및 `claimAndStake()`가 `claimedAmounts[smartAccount]` 대신 `claimedAmounts[msg.sender]`를 사용하여 사용자 청구 금액을 확인한다는 것입니다. 시스템의 사용자 계정은 `smartAccount`이며 사용자는 스마트 계정에 대해 여러 주소를 등록할 수 있습니다. 에어드롭은 `smartAccount`에 속하므로 코드는 `smartAccount` 주소를 사용하여 사용자의 청구된 금액을 추적해야 합니다. 현재 구현에서 사용자가 `smartAccount` 및 다른 지갑 주소를 기반으로 두 개의 에어드롭을 받으면 청구 가능 계산이 잘못되고, `claimedAmounts[msg.sender]`가 두 번째 지갑 주소에 대한 청구 금액을 표시하지 않으므로 사용자는 에어드롭 금액의 최대 두 배를 받게 됩니다.

권장 사항:
모든 곳에서 사용자 청구 금액을 추적하기 위해 `smartAccount`를 사용하십시오.
