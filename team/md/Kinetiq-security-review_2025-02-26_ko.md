# 소개 (About)
Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)
스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)
**Pashov Audit Group**에 의해 **kinetiq-research/lst** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Kinetiq 정보 (About Kinetiq)

Kinetiq는 Hyperliquid에 구축된 유동성 스테이킹 프로토콜로, 사용자가 HYPE 토큰을 스테이킹하고 그 대가로 kHYPE 토큰을 받아 유동성을 유지하면서 네트워크 보안에 참여할 수 있도록 합니다. 고급 검증자 관리, 성과 기반 지분 할당, 가스 최적화 리밸런싱, 보상 및 슬래싱 이벤트를 추적하기 위한 오라클 시스템을 특징으로 하며, 모두 역할 기반 액세스 제어 및 비상 메커니즘으로 보호됩니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)
_검토 커밋 해시_ - [c83aa178eb429a7e084bdda402aadafe1a58dcc6](https://github.com/kinetiq-research/lst/tree/c83aa178eb429a7e084bdda402aadafe1a58dcc6)

_수정 검토 커밋 해시_ - [48cfade5085f695771433094cbb8b01962a50be7](https://github.com/kinetiq-research/lst/tree/48cfade5085f695771433094cbb8b01962a50be7)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `KHYPE`
- `OracleManager`
- `PauserRegistry`
- `StakingManager`
- `ValidatorManager`
- `L1Read`
- `L1Write`
- `MinimalImplementation`
- `SystemOracle`
- `DefaultAdapter`

# 발견 사항 (Findings)
 # [H-01] `_AddRebalanceRequest`가 위임 인출 요청에 오래된 잔액을 사용할 수 있음 (`_AddRebalanceRequest` may use outdated balance for delegate withdrawal request)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`ValidatorManager._addRebalanceRequest()`는 검증자의 잔액을 확인하고 인출 요청을 기록합니다:

```solidity
    function _addRebalanceRequest(address validator, uint256 withdrawalAmount) internal {
        require(!_validatorsWithPendingRebalance.contains(validator), "Validator has pending rebalance");
        require(withdrawalAmount > 0, "Invalid withdrawal amount");

        (bool exists, uint256 index) = _validatorIndexes.tryGet(validator);
        require(exists, "Validator does not exist");
        require(_validators[index].balance >= withdrawalAmount, "Insufficient balance");

        validatorRebalanceRequests[validator] = RebalanceRequest({validator: validator, amount: withdrawalAmount});
        _validatorsWithPendingRebalance.add(validator);

        emit RebalanceRequestAdded(validator, withdrawalAmount);
    }
```
그러나 검증자의 잔액은 보상이나 슬래싱으로 인해 언제든지 변경될 수 있습니다. 이는 관리자가 `closeRebalanceRequest()`를 호출할 때 두 가지 잠재적인 문제를 야기합니다:
1. 잔액 감소: 요청 후 검증자의 잔액이 감소하면 위임 제거 또는 인출이 실패합니다.
2. 잔액 증가: 잔액이 증가하면 일부 자금이 검증자의 계정에 남을 수 있습니다.

이는 관리자가 모든 자금을 회수해야 하는 비상 인출 또는 검증자 비활성화 중에 특히 중요합니다.

## 권장 사항

1. `closeRebalanceRequest()` 함수가 인출 요청을 처리하기 전에 검증자의 잔액을 확인하여 가장 최신 금액을 반영하도록 하십시오.
2. `closeRebalanceRequest()`에 메커니즘을 추가하여 관리자가 검증자 잔액에 있는 현재 사용 가능한 모든 자금을 인출할 수 있도록 하십시오.



# [H-02] ValidatorManager: `deactivatevalidator` 함수에서 검증자로부터 자금 인출 누락 (ValidatorManager: missing fund withdrawal from validator in `deactivatevalidator` function)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명
`StakingManager.deactivateValidator()`는 인출 요청을 생성하지만 `processValidatorWithdrawals()`를 호출하여 검증자로부터 자금을 위임 해제/인출하지 않습니다:
```solidity
    function deactivateValidator(address validator) external whenNotPaused nonReentrant validatorExists(validator) {
        // Oracle manager will also call this, so limit the msg.sender to both MANAGER_ROLE and ORACLE_ROLE
        require(hasRole(MANAGER_ROLE, msg.sender) || hasRole(ORACLE_ROLE, msg.sender), "Not authorized");

        (bool exists, uint256 index) = _validatorIndexes.tryGet(validator);
        require(exists, "Validator does not exist");

        Validator storage validatorData = _validators[index];
        require(validatorData.active, "Validator already inactive");

        // Create withdrawal request before state changes
        if (validatorData.balance > 0) {
            _addRebalanceRequest(validator, validatorData.balance);
        }

        // Update state after withdrawal request
        validatorData.active = false;

        emit ValidatorDeactivated(validator);
    }
```
이 문제로 인해 자금이 검증자의 계정에 갇히게 됩니다:
1. 리밸런스 요청을 제거하기 위한 관리자의 `closeRebalanceRequests()` 호출은 스테이킹 관리자에 잔액이 충분하지 않아 되돌려질 것입니다.
2. 관리자의 `rebalanceWithdrawal()` 호출은 검증자가 보류 목록에 추가되어 되돌려질 것입니다.
3. Sentinel의 `requestEmergencyWithdrawal()` 호출도 같은 이유로 되돌려질 것입니다.

## 권장 사항
`deactivateValidator()` 함수에서 다음을 호출하여 검증자로부터 자금을 인출하십시오:
```IStakingManager(stakingManager).processValidatorWithdrawals();```



# [H-03] 비활성화된 검증자가 재활성화 후에도 이전 잔액을 유지함 (Deactivated validator retains old balance after reactivation)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`ValidatorManager::deactivateValidator`를 통해 검증자가 비활성화될 때, 검증자의 잔액은 업데이트되지 않습니다. 그러나 이 검증자에 대해 리밸런스 인출 요청이 생성되므로 **실제** 잔액은 0이 됩니다.

```solidity

    function deactivateValidator(address validator) external whenNotPaused nonReentrant validatorExists(validator) {

        // ...

        Validator storage validatorData = _validators[index];

        require(validatorData.active, “Validator already inactive”);

        // Create withdrawal request before state changes

        if (validatorData.balance > 0) {

            _addRebalanceRequest(validator, validatorData.balance);

        }

        // Update state after withdrawal request

        validatorData.active = false;

        emit ValidatorDeactivated(validator);

    }

```

문제는 비록 검증자의 실제 잔액이 0이라 할지라도, `OracleManager::updatePerformance()`가 비활성 검증자를 건너뛰기 때문에 절대 업데이트되지 않는다는 사실에서 비롯됩니다. 결과적으로 비활성 검증자는 이전의 0이 아닌 잔액을 저장소에 유지하게 됩니다. 이는 `ValidatorManager::reactivateValidator()`를 통해 검증자가 재활성화될 때 일시적으로 오래된 잔액을 유지하게 되므로 문제가 됩니다.

```solidity

    function reactivateValidator(address validator)

        // ...

    {

        // ...

        Validator storage validatorData = _validators[index];

        require(!validatorData.active, “Validator already active”);

        require(!_validatorsWithPendingRebalance.contains(validator), “Validator has pending rebalance”);

        // Reactivate the validator

        validatorData.active = true;

        emit ValidatorReactivated(validator);

    }

```

이것의 영향은 무엇입니까?

첫째, 비활성화된 검증자가 재활성화되고 잔액이 업데이트될 때까지 `totalBalance`가 잘못 부풀려집니다. 이는 재위임된 금액을 받는 검증자가 증가된 잔액을 보고하고, 이 증가분이 `generatePerformance` 동안 `totalBalance`에 포함되기 때문입니다.

다음 시나리오를 고려하십시오:

- `valA:100, valB:200, totalBalance:300`

- `valA`가 비활성화되고 잔액이 `valB`로 재위임됩니다.

- 다음 업데이트 동안 `valB`는 잔액 `300`을 보고하여 totalBalance가 `400`으로 증가하게 됩니다. 현재 잔액이 0인 `valA`는 비활성화되었으므로 잔액을 보고하지 않습니다.

새로운 자금이 시스템에 들어오지 않았고 잔액 이체만 발생했으므로 이는 올바르지 않습니다. 시스템은 실제 존재하는 것보다 더 많은 자금을 잘못 추적합니다.

둘째, 위 시나리오에서 `valA`가 나중에 재활성화되면 `OracleManager::generatePerformance`가 업데이트할 때까지 여전히 이전 잔액을 갖게 됩니다. `valA`의 이전 잔액이 현재 `totalBalance`보다 큰 경우, `ValidatorManager::updateValidatorPerformance()`가 `totalBalance`를 업데이트하려고 시도할 때 언더플로우로 인한 되돌림이 발생합니다.

예를 들어:

- `valA`는 비활성화될 때 100의 잔액을 가집니다.

- 잠시 후 `totalBalance`가 80으로 줄어듭니다.

- `valA`가 재활성화되면 `updateValidatorPerformance()`는 다음을 계산하려고 시도합니다:

```solidity

totalBalance = totalBalance - oldBalance + balance;

```

이는 다음과 같습니다:

```solidity

totalBalance = 80 - 100 + 0

```

이 언더플로우는 되돌림을 유발하여 업데이트에 대한 DoS를 초래합니다.

```solidity

    function updateValidatorPerformance(

        address validator,

        uint256 balance,

        // ...

    ) external whenNotPaused onlyRole(ORACLE_ROLE) validatorExists(validator) validatorActive(validator) {

        // ...

        // Cache old balance for total balance update

        uint256 oldBalance = val.balance;

        // ...

        // Update total balance

@>        totalBalance = totalBalance - oldBalance + balance;

        // ...

    }

```

## 권장 사항

비활성화 시 검증자의 잔액을 0으로 만들고 리밸런스 요청이 생성될 때 `totalBalance`에서 빼는 것을 고려하십시오. 이렇게 하면 회계 불일치와 재활성화 시 잠재적인 언더플로우를 피할 수 있습니다.



# [H-04] 오래된 잔액이 슬래싱 금액보다 적으면 `ReportSlashingEvent`가 되돌려짐 (`ReportSlashingEvent` reverts if outdated balance is below slashing amount)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`OracleManager::generatePerformance`는 매시간(또는 `MIN_UPDATE_INTERVAL`로 지정된 다른 간격으로) 한 번씩 호출되어야 합니다. 모든 검증자의 통계를 업데이트하고 `totalBalance`도 업데이트합니다. 보상이나 슬래싱 금액이 있는 경우 이를 반영해야 하며 `ValidatorManager`도 그에 따라 업데이트되어야 합니다.
```solidity
    function generatePerformance() external whenNotPaused onlyRole(OPERATOR_ROLE) returns (bool) {
        // ..

        // Update validators with averaged values
        for (uint256 i = 0; i < validatorCount; i++) {
            // ...

            // Handle slashing
            if (avgSlashAmount > previousSlashing) {
                uint256 newSlashAmount = avgSlashAmount - previousSlashing;
@>                validatorManager.reportSlashingEvent(validator, newSlashAmount);
            }

            // ...
        }

        // ...

        return true;
    }
```
보시다시피, 특정 검증자에 대한 누적 슬래싱 금액이 증가하면 증가분을 매개변수로 전달하여 `reportSlashingEvent`가 트리거됩니다.
```solidity
    function reportSlashingEvent(address validator, uint256 amount)
        internal
       // ...
    {
        require(amount > 0, "Invalid slash amount");

        Validator storage val = _validators[_validatorIndexes.get(validator)];
@>        require(val.balance >= amount, "Insufficient stake for slashing");

        // Update balances
        unchecked {
            // These operations cannot overflow:
            // - val.balance >= amount (checked above)
            // - totalBalance >= val.balance (invariant maintained by the contract)
            val.balance -= amount;
            totalBalance -= amount;
        }

       // ...
    }
```
강조 표시된 섹션에서 볼 수 있듯이 새로운 슬래싱 금액이 검증자의 이전에 보고된 잔액보다 크면 전체 `reportSlashingEvent`(따라서 `generatePerformance`도 마찬가지)가 되돌려집니다. 그러나 이는 실제로 매우 가능성이 높은 시나리오이며 업데이트 간의 간격으로 인해 쉽게 발생할 수 있습니다.

예를 들어 시간 `T`에 검증자의 잔액이 `100`일 수 있습니다. `T + 1시간`에 검증자의 잔액이 `500`으로 증가하고 `slashingAmount`가 `110`이 될 수 있습니다. 이 버그로 인해 코드는 `oldBalance > newSlashingAmount`를 요구하는데, `slashingAmount`는 `110`이고 `oldBalance`는 `100`에 불과하므로 되돌려질 것입니다. 이와 같은 상황은 검증자가 활성화된 후 처음 며칠 동안 발생할 가능성이 특히 높습니다.

## 권장 사항

보상이 처리되는 방식과 유사하게 슬래싱 금액을 처리하는 메커니즘을 구현하거나, 새로운 슬래싱 금액을 검증자의 실제 최신 잔액과 비교하는 것을 고려하십시오.



# [H-05] 안전하지 않은 형변환으로 인해 자금이 영구적으로 잠길 수 있음 (Funds can be permanently locked due to unsafe type casting)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`StakingManager` 계약은 HYPE 토큰에 대한 스테이킹 운영을 관리하여 사용자가 토큰을 스테이킹하고 대가로 `kHYPE` 토큰을 받을 수 있도록 합니다. 사용자가 `stake()` 함수를 통해 토큰을 스테이킹하면 계약은 `_distributeStake()` 내부 함수를 사용하여 이 토큰을 검증자에게 위임하며, 이 함수는 차례로 `l1Write.sendTokenDelegate()`를 호출합니다.

`_distributeStake()` 및 `_withdrawFromValidator()` 함수 모두에서 `amount` 매개변수의 형변환에 심각한 문제가 존재합니다. `l1Write.sendTokenDelegate()` 함수는 `uint64` 매개변수를 예상하지만 계약은 `uint256`에서 `uint64`로 안전하지 않은 캐스팅을 수행합니다. 금액이 `type(uint64).max` (18,446,744,073,709,551,615)를 초과하면 값이 조용히 0으로 오버플로우됩니다.

```solidity
            l1Write.sendTokenDelegate(delegateTo, uint64(amount), false);
```

이는 다음과 같은 경우에 특히 위험합니다:
1. `maxStakeAmount`가 0(무제한)으로 설정되거나 `type(uint64).max`보다 큰 값으로 설정된 경우
2. 사용자가 `type(uint64).max`보다 큰 금액을 스테이킹하는 경우

이러한 경우 자금은 계약에 의해 수락되고 `kHYPE` 토큰이 주조되지만 조용한 오버플로우로 인해 검증자에 대한 위임은 0으로 수행됩니다. 토큰은 복구할 메커니즘이 없으므로 계약에 영구적으로 잠기게 됩니다.

### 개념 증명 (Proof of Concept)

1. `maxStakeAmount`가 0(무제한)으로 설정됨
2. 사용자가 `stake()`를 19e18 HYPE 토큰 (> `type(uint64).max`)으로 호출
3. 계약이 토큰을 수락하고 해당하는 `kHYPE`를 주조
4. `_distributeStake()`에서 `amount`가 `uint64`로 캐스팅되어 0이 됨
5. `l1Write.sendTokenDelegate()`가 0 토큰으로 호출됨
6. 원래 HYPE 토큰은 복구할 방법 없이 계약에 잠김

## 권장 사항

OpenZeppelin의 SafeCast 라이브러리를 구현하여 안전한 형식 변환을 보장하십시오.



# [H-06] 일부 스테이커가 스테이킹된 HYPE를 인출하지 못할 수 있음 (Some stakers may fail to withdraw staking HYPE)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`StakingManager` 계약은 인출 처리를 위해 HYPE 토큰의 준비금을 유지하도록 의도된 버퍼 시스템을 구현합니다. 그러나 현재 구현은 이 버퍼를 효과적으로 활용하지 못하여 불필요한 검증자 종료를 강제하고 인출 수요가 높은 기간 동안 시스템 위험을 초래할 수 있습니다.

계약은 두 가지 버퍼 관련 상태 변수를 유지합니다:

- `hypeBuffer`: 준비금으로 보유된 현재 HYPE 토큰 금액
- `targetBuffer`: 원하는 버퍼 크기

사용자가 HYPE를 스테이킹할 때 계약은 `_distributeStake()` 함수를 통해 이 버퍼를 유지하려고 시도하며, 이 함수는 남은 토큰을 검증자에게 위임하기 전에 버퍼를 채우는 것을 우선시합니다. 그러나 치명적인 결함은 인출 로직에 있습니다.

`queueWithdrawal()`에서 계약은 버퍼에서 인출을 서비스하려고 시도하지 않고 `_withdrawFromValidator()`를 통해 즉시 검증자 인출을 시작합니다. 이는 버퍼 유지 목적을 무효화하며 다음으로 이어질 수 있습니다:

1. 버퍼에 충분한 자금이 있음에도 불구하고 불필요한 검증자 종료
2. 자금이 생산적인 검증에서 빠져나가므로 스테이킹 효율성 감소

```solidity
_withdrawFromValidator(currentDelegation, amount);
```

이는 인출이 먼저 버퍼에서 서비스되고 인출 수요가 가용 준비금을 초과할 때만 검증자 종료가 트리거되는 Lido의 버퍼링된 ETH 시스템과 같은 더 강력한 구현과 대조됩니다.

### 개념 증명 (Proof of Concept)

1. 사용자 A가 100 HYPE를 스테이킹하고 `targetBuffer`가 50 HYPE로 설정됨
    - 50 HYPE는 버퍼로 이동
    - 50 HYPE는 검증자에게 위임됨
2. 사용자 B가 40 HYPE 인출 요청
    - 버퍼에 50 HYPE가 있음에도 불구하고 계약은 `_withdrawFromValidator()`를 호출함

## 권장 사항

검증자 종료를 강제하기 전에 버퍼 사용을 우선시하도록 `queueWithdrawal()` 함수를 수정하십시오.



# [H-07] 토큰 작업에 사용되지 않는 환율 구현 (Exchange rate implementation not used in token operations)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`StakingManager` 계약은 HYPE와 kHYPE 토큰 간의 환율 메커니즘을 구현하지만 중요한 토큰 작업에서 이를 활용하지 못합니다. 구현된 환율 로직과 토큰 주조 및 소각 작업에서의 실제 사용 간의 불일치는 잘못된 토큰 회계 및 사용자의 가치 손실로 이어질 것입니다.

계약은 다음을 고려하여 `getExchangeRatio()`를 통해 환율 계산을 유지합니다:
- 총 스테이킹된 HYPE
- 총 보상
- 총 청구된 HYPE
- 총 슬래싱
- 현재 kHYPE 공급량

이 비율은 HYPE와 kHYPE 토큰 간의 실제 가치 관계를 반영하기 위한 것입니다. 그러나 계약의 토큰 작업 (`stake()`, `queueWithdrawal()` 등)은 구현된 환율 메커니즘을 완전히 무시하고 HYPE와 kHYPE 간의 1:1 관계를 가정합니다.

```solidity
kHYPE.mint(msg.sender, msg.value);
```

구현된 환율과 실제 사용 간의 불일치는 다음으로 이어질 수 있습니다:

1. 스테이킹 작업 중 잘못된 토큰 주조
2. 인출 중 잘못된 토큰 소각
3. 실제 환율이 1:1에서 벗어날 때 사용자의 가치 손실

### 개념 증명 (Proof of Concept)

다음 시나리오를 고려하십시오:

1. 사용자가 `stake()`를 통해 100 HYPE를 스테이킹
2. 계약은 `HYPEToKHYPE()`를 사용하는 대신 100 kHYPE (1:1 비율)를 주조
3. 나중에 슬래싱이나 보상으로 인해 환율이 0.9 (1 HYPE = 0.9 kHYPE)가 됨
4. 사용자가 100 kHYPE 인출 요청
5. 계약은 `kHYPEToHYPE()`를 사용하는 대신 100 kHYPE를 소각하고 100 HYPE (1:1 비율)를 전송
6. 사용자는 받아야 할 것보다 더 많은 HYPE를 받음 (90 대신 100)

이는 프로토콜에 대한 직접적인 가치 손실과 잘못된 토큰 회계를 생성합니다.

## 권장 사항

환율은 모든 토큰 작업에 올바르게 통합되어야 합니다. `stake()` 함수에 대한 수정 사항은 다음과 같습니다:

```solidity
function stake() external payable nonReentrant whenNotPaused {
    // ... existing validation code ...

    uint256 kHYPEAmount = HYPEToKHYPE(msg.value);
    totalStaked += msg.value;
    kHYPE.mint(msg.sender, kHYPEAmount);
}
```



# [H-08] 환율 계산이 올바르지 않음 (Exchange rate calculation is incorrect)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

프로토콜은 여러 `StakingManager` 계약이 공존할 수 있는 스테이킹 시스템을 구현하여 일반적인 `ValidatorManager`를 통해 이익과 손실을 공유합니다. 각 `StakingManager`는 `totalStaked` 및 `totalClaimed` 상태 변수를 통해 스테이킹 및 청구된 금액에 대한 자체 회계를 유지하면서 `ValidatorManager`를 통해 보상과 슬래싱을 공유합니다.

치명적인 문제는 HYPE와 kHYPE 토큰 간의 환율을 계산하는 `getExchangeRatio()` 함수에 있습니다. 현재 구현은 로컬 회계 값(local accounting values)을 사용하지만 글로벌 PnL 수치를 사용하여 서로 다른 `StakingManager` 인스턴스 간에 잘못된 환율 계산을 초래합니다.

환율 계산은 다음 공식을 따릅니다:

```solidity
exchangeRate = (totalStaked + totalRewards - totalClaimed - totalSlashing) / kHYPESupply
```

여기서:

- `totalStaked` 및 `totalClaimed`는 각 `StakingManager`에 로컬입니다
- `totalRewards` 및 `totalSlashing`은 `ValidatorManager`의 글로벌 값입니다
- `kHYPESupply`는 kHYPE 토큰의 총 공급량입니다

이로 인해 각 `StakingManager`가 동일한 보상과 슬래싱을 공유하면서 로컬 스테이킹/청구 값을 기반으로 다른 환율을 계산하게 되므로 상당한 불일치가 발생합니다.

### 개념 증명 (Proof of Concept)

1. 두 개의 `StakingManager` 계약 (SM1 및 SM2)이 배포됨

2. 사용자 A가 SM1을 통해 100 HYPE를 스테이킹
    - SM1.totalStaked = 100
    - SM2.totalStaked = 0

3. 사용자 B가 SM2를 통해 100 HYPE를 스테이킹
    - SM1.totalStaked = 100
    - SM2.totalStaked = 100

4. `ValidatorManager`가 20 HYPE의 보상을 기록

5. 환율 계산:
    - SM1: (100 + 20 - 0 - 0) / 100 = 1.2
    - SM2: (100 + 20 - 0 - 0) / 100 = 1.2

비율은 동일해 보이지만 각 관리자가 전체 스테이킹 금액의 절반만 보기 때문에 올바르지 않습니다.

6. 올바른 비율은 다음과 같아야 합니다: (200 + 20 - 0 - 0) / 200 = 1.1

현재 설계는 사용자가 상호 작용하는 `StakingManager`에 따라 차익 거래 기회와 kHYPE 토큰의 잘못된 평가를 생성합니다.

## 권장 사항

`ValidatorManager`는 글로벌 스테이킹 합계를 추적해야 하며, `StakingManager` 인스턴스는 환율 계산을 위해 이러한 글로벌 값을 쿼리해야 합니다.
# [M-01] StakingManager `getExchangeRatio`가 첫 번째 주조를 처리하지 않음 (StakingManager `getExchangeRatio` does not handle first mint)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명
`getExchangeRatio()`는 HYPE와 kHYPE 간의 환율을 계산하는 데 사용됩니다. kHYPESupply가 0보다 큰지 확인합니다:
```solidity
    function getExchangeRatio() public view returns (uint256) {
        uint256 totalSlashing = validatorManager.totalSlashing();
        uint256 totalRewards = validatorManager.totalRewards();
        uint256 kHYPESupply = kHYPE.totalSupply();

@>      require(kHYPESupply > 0, "No kHYPE supply");

        // Calculate total HYPE: totalStaked + totalRewards - totalClaimed - totalSlashing
        uint256 totalHYPE = totalStaked + totalRewards - totalClaimed - totalSlashing;

        // Return ratio = totalHYPE / kHYPE.totalSupply (scaled by 1e18)
        return (totalHYPE * 1e18) / kHYPESupply;
    }
```
결과적으로 첫 번째 사용자의 `stake` 호출은 `kHYPE.totalSupply()`가 0이므로 되돌려집니다.

## 권장 사항

`kHYPESupply > 0` 확인을 제거하십시오.



# [M-02] 리밸런스 요청이 진행 중인 검증자를 비활성화하는 경우 `OracleManage::generatePerformance`가 되돌려짐 (`OracleManage::generatePerformance` reverts if deactivating validator with ongoing rebalance request)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`OracleManager::generatePerformance`는 모든 검증자의 통계와 `ValidatorManager`의 `totalBalance`를 업데이트하기 위한 것입니다. 이 함수는 한 시간에 한 번(또는 `MIN_UPDATE_INTERVAL`에 설정된 간격으로) 호출됩니다. 되돌림이 발생하면 `ValidatorManager`가 오래된 상태로 유지되므로 이 호출이 되돌려지지 않는 것이 중요합니다. 또한 `OracleManager::generatePerformance`는 비정상적인 동작 확인을 수행하고 필요한 경우 검증자를 비활성화합니다.
```solidity
        // Check for anomalies
        if (
            !_checkValidatorBehavior(
                validator, previousSlashing, previousRewards, avgSlashAmount, avgRewardAmount, avgBalance
            )
        ) {
            // Deactivate validator if behavior is unexpected
@>            validatorManager.deactivateValidator(validator);
            continue;
        }
```
그러나 주어진 검증자가 활성 리밸런스 요청을 가지고 있는 경우 이 비활성화 호출은 쉽게 되돌려질 수 있습니다(따라서 전체 업데이트가 실패할 수 있음).
```solidity
    function deactivateValidator(address validator) external whenNotPaused nonReentrant validatorExists(validator) {
        // ...

        // Create withdrawal request before state changes
        if (validatorData.balance > 0) {
@>            _addRebalanceRequest(validator, validatorData.balance);
        }

        // ...
    }

    function _addRebalanceRequest(address validator, uint256 withdrawalAmount) internal {
@>        require(!_validatorsWithPendingRebalance.contains(validator), "Validator has pending rebalance");
        // ...
    }
```
이 문제의 결과는 영향을 받는 검증자에 대한 리밸런스 인출 요청이 해결될 때까지 검증자에 대한 성과 업데이트가 진행되지 못하게 하는 일시적인 DoS입니다. 검증자가 많은 대규모 시스템에서는 이러한 시나리오가 발생할 가능성이 높습니다.

## 권장 사항

이 문제를 완화하는 한 가지 방법은 리밸런스 요청이 생성될 때 검증자의 잔액을 0으로 만드는(그리고 `totalBalance`에서 빼는) 것입니다. 이렇게 하면 비활성화 시 검증자가 새 리밸런스 요청을 열려고 시도하지 않습니다.



# [M-03] 스테이킹 제한이 없을 때 잘못된 최대 스테이크 금액 검증으로 설정 방지 (Invalid max stake amount validation prevents setting when no staking limit exists)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`StakingManager` 계약은 세 가지 주요 매개변수를 통해 스테이킹 제한을 구현합니다:
- `stakingLimit`: 허용된 최대 총 스테이크 (0 = 무제한)
- `minStakeAmount`: 트랜잭션당 최소 스테이크
- `maxStakeAmount`: 트랜잭션당 최대 스테이크 (0 = 무제한)

이 매개변수는 전용 설정자 함수를 통해 `MANAGER_ROLE`을 가진 계정에서 구성할 수 있습니다. 그러나 `setMaxStakeAmount()` 함수에 논리적 오류가 있어 스테이킹 제한이 구성되지 않았을 때 유효한 최대 스테이크 금액을 설정하지 못하게 합니다.

```solidity
        if (newMaxStakeAmount > 0) {
            require(newMaxStakeAmount > minStakeAmount, "Max stake must be greater than min");
            require(newMaxStakeAmount < stakingLimit, "Max stake must be less than limit");
        }
```

문제는 `setMaxStakeAmount()`의 유효성 검사 로직에서 발생하며, 0이 아닌 `newMaxStakeAmount`가 `stakingLimit`보다 작아야 한다고 무조건 요구합니다. 이 검사는 `stakingLimit`가 0(무제한)인 경우를 고려하지 않아 총 제한이 없을 때 최대 스테이크 금액을 설정할 수 없게 만듭니다.

### 개념 증명 (Proof of Concept)

1. 초기에 `stakingLimit = 0` (총 스테이킹 무제한)
2. 관리자가 개별 트랜잭션을 제한하기 위해 `maxStakeAmount = 100 ether`로 설정을 시도
3. `setMaxStakeAmount(100 ether)` 호출은 다음 이유로 되돌려짐:
   - `newMaxStakeAmount > 0`이 유효성 검사 트리거
   - `require(newMaxStakeAmount < stakingLimit)`은 `100 ether < 0`이 거짓이므로 실패

## 권장 사항

제한이 실제로 설정된 경우에만 스테이킹 제한 유효성 검사를 수행하도록 `setMaxStakeAmount()` 함수를 수정하십시오:

```solidity
function setMaxStakeAmount(uint256 newMaxStakeAmount) external onlyRole(MANAGER_ROLE) {
    if (newMaxStakeAmount > 0) {
        require(newMaxStakeAmount > minStakeAmount, "Max stake must be greater than min");
        if (stakingLimit > 0) {
            require(newMaxStakeAmount < stakingLimit, "Max stake must be less than limit");
        }
    }
    maxStakeAmount = newMaxStakeAmount;
    emit MaxStakeAmountUpdated(newMaxStakeAmount);
}
```



# [M-04] 검증자가 비활성 상태일 때도 새로운 스테이크가 위임됨 (New stakes delegated even when validator is inactive)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`StakingManager` 계약은 HYPE 스테이킹 시스템의 스테이킹 작업 및 검증자 위임을 관리합니다. 사용자가 `stake()` 함수를 통해 HYPE 토큰을 스테이킹하면 자금은 내부 `_distributeStake()` 함수를 통해 분배되며, 이 함수는 버퍼 관리와 검증자에 대한 위임을 모두 처리합니다.

현재 `_distributeStake()`는 검증자가 여전히 활성 상태인지 확인하지 않고 `validatorManager.getDelegation(address(this))`에 의해 반환된 검증자 주소로 자금을 위임합니다. 이는 사용자 자금이 슬래싱이나 저조한 성과로 인해 비활성화된 검증자에게 위임될 위험을 초래합니다.

```solidity
        if (amount > 0) {
            address delegateTo = validatorManager.getDelegation(address(this));
            require(delegateTo != address(0), "No delegation set");

            // Send tokens to delegation
            l1Write.sendTokenDelegate(delegateTo, uint64(amount), false);

            emit Delegate(delegateTo, amount);
        }
```

이는 다음으로 이어질 수 있습니다:
1. 사용자 자금이 비활성/슬래싱된 검증자에게 위임됨
2. 비활성 검증자는 보상을 생성하지 않으므로 스테이커의 수익 감소

### 개념 증명 (Proof of Concept)

1. 검증자가 활성 상태이고 현재 위임 대상으로 설정됨
2. 검증자가 `OracleManager.generatePerformance()`를 통해 슬래싱되고 비활성화됨
3. 사용자가 10 HYPE로 `stake()` 호출
4. `_distributeStake()`가 비활성화된 검증자에게 자금을 위임함
5. 사용자의 스테이크는 이제 보상을 생성하지 않는 비활성 검증자에게 위임됨

## 권장 사항

위임 전에 검증자의 활성 상태를 확인하도록 `_distributeStake()`에 유효성 검사를 추가하십시오.



# [M-05] 스테이크 함수가 stakingLimit 확인 시 totalClaimed를 고려하지 않음 (Stake function does not account for totalClaimed when checking stakingLimit)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`StakingManager` 계약은 `stakingLimit` 매개변수를 통해 스테이킹 제한 메커니즘을 구현하여 시스템에 스테이킹할 수 있는 HYPE 토큰의 최대 양을 제어합니다. 그러나 현재 구현은 인출된 토큰을 고려하지 않아 관리 중인 토큰의 실제 양을 정확하게 추적하지 못합니다.

`stake()` 함수는 다음을 사용하여 제한 확인을 수행합니다:

```solidity
require(totalStaked + msg.value <= stakingLimit, "Staking limit reached");
```

그러나 `totalStaked`는 사용자가 스테이킹할 때만 증가하고 절대 감소하지 않는 반면, 별도의 `totalClaimed`가 인출을 추적합니다. 즉, 관리 중인 실제 토큰 양은 `totalStaked - totalClaimed`이지만 제한 확인은 원시 `totalStaked` 값을 사용합니다.

이로 인해 실제 관리 중인 금액이 제한보다 훨씬 낮더라도 계약이 새로운 스테이크를 거부할 수 있어 프로토콜의 용량을 불필요하게 줄이는 상황이 발생합니다.

### 개념 증명 (Proof of Concept)

- 관리자가 stakingLimit를 1000 HYPE로 설정
- Alice가 1000 HYPE 스테이킹 (totalStaked = 1000)
- Alice가 500 HYPE 인출 (totalClaimed = 500, totalStaked = 1000)
- Bob이 100 HYPE 스테이킹 시도
- 실제로 500 HYPE만 스테이킹되었음에도 불구하고 "Staking limit reached"로 인해 트랜잭션 되돌려짐

## 권장 사항

청구된 토큰을 고려하도록 `stake()`의 스테이킹 제한 확인을 업데이트하십시오:

```solidity
require(((totalStaked - totalClaimed) + msg.value) <= stakingLimit, "Staking limit reached");
```



# [M-06] `generatePerformance`의 부적절한 실행 순서 (Improper execution order in `generatePerformance`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

OracleManager에서 운영자 역할은 주기적으로 검증자의 성과 결과를 생성합니다. 성과 결과를 얻으면 검증자의 행동을 확인합니다. 이 검증자의 행동이 기대치보다 낮으면 이 검증자를 비활성화합니다.

문제는 검증자를 직접 비활성화하도록 트리거하여 최신 슬래시/보상/잔액 금액 업데이트를 놓친다는 것입니다.

몇 가지 가능한 영향이 있습니다:
1. 최근 기간에 슬래시보다 보상이 더 많으면 예상보다 적은 HYPE를 인출하게 됩니다.
2. 최근 기간에 보상보다 슬래시가 더 많으면 검증자에게 위임한 것보다 더 많은 HYPE를 인출하게 됩니다. 이는 위임 해제 실패를 유발합니다.
3. StakingManager에서 환율을 계산하기 위해 `slash` 및 `reward` 금액을 사용합니다. 계산이 부정확할 것입니다.

```solidity
        if (
            !_checkValidatorBehavior(
                validator, previousSlashing, previousRewards, avgSlashAmount, avgRewardAmount, avgBalance
            )
        ) {
            validatorManager.deactivateValidator(validator);
            continue;
        }

        if (avgSlashAmount > previousSlashing) {
            uint256 newSlashAmount = avgSlashAmount - previousSlashing;
            validatorManager.reportSlashingEvent(validator, newSlashAmount);
        }

        if (avgRewardAmount > previousRewards) {
            uint256 newRewardAmount = avgRewardAmount - previousRewards;
            validatorManager.reportRewardEvent(validator, newRewardAmount);
        }

        validatorManager.updateValidatorPerformance(
            validator, avgBalance, avgUptimeScore, avgSpeedScore, avgIntegrityScore, avgSelfStakeScore
        );
    }
```


## 권장 사항

잔액/보상/슬래시를 최신 금액으로 업데이트한 다음 이 검증자를 비활성화하십시오.



# [L-01] `rebalanceWithdrawal()`에서 검증자 활성 확인 누락 (Missing validator active check in `rebalanceWithdrawal()`)

`ValidatorManager` 계약에서:
`rebalanceWithdrawal()`은 인출을 처리하기 전에 검증자가 활성 상태인지 확인하지 않지만 `requestEmergencyWithdrawal()`은 확인합니다.
```solidity
    function rebalanceWithdrawal(
        address stakingManager,
        address[] calldata validators,
        uint256[] calldata withdrawalAmounts
    ) external whenNotPaused nonReentrant onlyRole(MANAGER_ROLE) {
        require(validators.length == withdrawalAmounts.length, "Length mismatch");
        require(validators.length > 0, "Empty arrays");

        for (uint256 i = 0; i < validators.length;) {
            require(validators[i] != address(0), "Invalid validator address");

            // Add rebalance request (this will check for duplicates)
            _addRebalanceRequest(validators[i], withdrawalAmounts[i]);

            unchecked {
                ++i;
            }
        }

        // Trigger withdrawals through StakingManager
        IStakingManager(stakingManager).processValidatorWithdrawals(validators, withdrawalAmounts);
    }
```
```solidity
    function requestEmergencyWithdrawal(address stakingManager, address validator, uint256 amount)
        external
        onlyRole(SENTINEL_ROLE)
        whenNotPaused
    {
        require(block.timestamp >= lastEmergencyTime + EMERGENCY_COOLDOWN, "Cooldown period");
        require(amount > 0, "Invalid amount");

        if (emergencyWithdrawalLimit > 0) {
            require(amount <= emergencyWithdrawalLimit, "Exceeds emergency limit");
        }

        (bool exists, uint256 index) = _validatorIndexes.tryGet(validator);
        require(exists, "Validator does not exist");

        Validator storage validatorData = _validators[index];
@>      require(validatorData.active, "Validator not active");

```



# [L-02] 업데이트가 수행되지 않아도 `OracleManager::lastUpdateTimestamp`가 업데이트됨 (`OracleManager::lastUpdateTimestamp` updated even if no update performed)

`OracleManager::generatePerformance()` 동안 `lastUpdateTimestamp`는 호출 간의 `MIN_UPDATE_INTERVAL` 지연이 시행되도록 업데이트되어야 합니다. 그러나 권한 있는 모든 오라클이 비활성 상태이면 검증자에 대한 업데이트가 수행되지 않지만 `lastUpdateTimestamp`는 여전히 `block.timestamp`로 설정됩니다. 이는 실제 업데이트가 `MIN_UPDATE_INTERVAL`이 지날 때까지 기다려야 하므로 `generatePerformance()`에 대한 나중에 합법적이고 유효한 호출을 차단하고 지연시킬 수 있습니다.
```solidity
    function generatePerformance() external whenNotPaused onlyRole(OPERATOR_ROLE) returns (bool) {
        // Check minimum interval between updates
        require(block.timestamp >= lastUpdateTime + MIN_UPDATE_INTERVAL, "Update too frequent");

        // ...

        // Update lastUpdateTime at the end of successful execution
@>        lastUpdateTime = block.timestamp;
        emit PerformanceUpdated(block.timestamp);

        return true;
    }
```



# [L-03] 구현의 생성자에서 `_disableInitializers()` 호출 누락 (Missing `_disableInitializers()` call in implementations' constructor)

프록시가 가리킬 구현인 다음 스마트 계약은 `constructor`에서 `_disableInitializers()`를 호출해야 하지만 그렇지 않습니다: `StakingManager.sol`, `KHYPE.sol`, `PauserRegistry.sol`, `ValidatorManager.sol` 및 `OracleManager.sol`. 이는 계약이 실수로 프록시 없이 배포되는 경우 의도하지 않은 초기화를 허용할 수 있습니다. `_disableInitializers()`를 추가하면 `initializer` 함수를 잠가 이 위험을 완화하는 데 도움이 됩니다.
```solidity
KHYPE.sol
    function initialize(
        // ...
    ) public initializer {
        // ...
    }
```



# [L-04] 무제한 루프로 인해 OracleManager 성과 업데이트가 되돌려질 수 있음 (OracleManager performance update may revert due to unbounded loops)

`generatePerformance()` 함수에는 검증자와 오라클에 대한 무제한 반복이 있는 4개의 중첩 루프가 포함되어 있습니다. `ValidatorManager`가 추가할 수 있는 검증자 수나 `OracleManager`가 승인할 수 있는 오라클 수에 대한 제한이 없기 때문에, 이 함수가 블록 가스 제한에 도달하여 되돌려질 위험이 있습니다.

이 함수는 이러한 루프 내에서 여러 외부 호출과 저장소 읽기/쓰기를 수행하여 검증자와 오라클 수가 증가함에 따라 가스 비용을 더욱 증가시킵니다. 이로 인해 중요한 성과 업데이트가 처리되지 않을 수 있습니다.

권장 사항:
한 번에 모든 검증자를 처리하려고 시도하는 대신 `OPERATOR_ROLE`이 `generatePerformance()`에 대한 각 호출에서 처리할 제한된 검증자 목록을 전달하도록 허용하십시오. 이렇게 하면 가스 제한 내에 맞는 배치로 성과 업데이트를 처리할 수 있습니다.



# [L-05] OracleManager가 오라클의 오래된 값을 확인하지 않음 (OracleManager does not check for oracle stale values)

`OracleManager.generatePerformance()`는 오라클에서 모든 데이터를 가져오지만 블록 번호, 타임스탬프 또는 기타 방법을 기반으로 오라클 데이터가 오래되었는지 확인하지 않습니다:
```solidity
function generatePerformance() external whenNotPaused onlyRole(OPERATOR_ROLE) returns (bool) {
              --snip--

            // Check each validator for this oracle
            for (uint256 i = 0; i < validatorCount; i++) {
                address validator = validators[i];
                if (!validatorManager.validatorActiveState(validator)) continue;

                try IOracleAdapter(oracle).getPerformance(validator) returns (
                    uint256 balance,
                    uint256 uptimeScore,
                    uint256 speedScore,
                    uint256 integrityScore,
                    uint256 selfStakeScore,
                    uint256 rewardAmount,
                    uint256 slashAmount
                ) {
                    // Sanity checks
                    if (
                        uptimeScore <= maxPerformanceBound && speedScore <= maxPerformanceBound
                            && integrityScore <= maxPerformanceBound && selfStakeScore <= maxPerformanceBound
                    ) {
                        // Aggregate scores
                        totalBalances[i] += balance;
                        totalUptimeScores[i] += uptimeScore;
                        totalSpeedScores[i] += speedScore;
                        totalIntegrityScores[i] += integrityScore;
                        totalSelfStakeScores[i] += selfStakeScore;
                        totalRewardAmounts[i] += rewardAmount;
                        totalSlashAmounts[i] += slashAmount;
                        validOracleCounts[i]++;
                    }
                } catch {
                    // Skip failed oracle calls
                    continue;
                }
            }
            --snip--
}
```

오라클 데이터 타임스탬프를 기록하고 `generatePerformance()` 함수에서 오래된 오라클 데이터를 무시하십시오.



# [L-06] 보상을 얻거나 슬래싱을 피하기 위한 `generatePerformance` 프론트 러닝 (Front-running `generatePerformance` to gain rewards or avoid slashing)

`OracleManager.generatePerformance()`는 `ValidatorManager`의 `reportRewardEvent()` 및 `reportSlashingEvent()` 함수를 호출하여 스테이커에게 보상/슬래시를 분배합니다:
```solidity
function generatePerformance() external whenNotPaused onlyRole(OPERATOR_ROLE) returns (bool) {
      --snipp--
       
       // Handle slashing
      if (avgSlashAmount > previousSlashing) {
          uint256 newSlashAmount = avgSlashAmount - previousSlashing;
          validatorManager.reportSlashingEvent(validator, newSlashAmount);
      }

      // Handle rewards
      if (avgRewardAmount > previousRewards) {
          uint256 newRewardAmount = avgRewardAmount - previousRewards;
          validatorManager.reportRewardEvent(validator, newRewardAmount);
      }
      --snipp--
}
```
사용자는 `generatePerformance()`를 프론트러닝하고 다음을 수행할 수 있습니다:
1. 기존 스테이커를 위한 보상에 불공정하게 참여하기 위해 `stake()` 호출.
2. 슬래싱 페널티를 피하기 위해 `confirmWithdrawal()` 호출.

이 공격이 발생할 확률을 줄이려면 다음을 고려하십시오:
1. 스테이킹 및 언스테이킹에 대한 시간 창을 설정하십시오. 요청은 시간 프레임 내에서만 처리되어야 하며 그렇지 않으면 취소되어야 합니다.
2. 다른 사람이 확정하도록 허용: 공격자의 불공정한 이득을 방지할 유인이 있으므로 다른 사용자가 사용자의 스테이크 또는 언스테이크 요청을 확정하도록 허용하십시오.



# [L-07] 활성 검증자가 0 `avgBalance`를 가지면 `OracleManager::generatePerformance()`가 되돌려질 것임 (`OracleManager::generatePerformance()` will revert if active validator has 0 `avgBalance`)

`OracleManager::generatePerformance()` 동안, 활성 검증자가 이 성과 보고서에 대해 0 잔액을 가질 수 있습니다. 그러나 현재 구현은 이를 능가하거나 즉시 비활성화하는 대신 `_checkValidatorBehavior()`의 `require()`로 인해 전체 트랜잭션을 되돌립니다. 이 되돌림은 `OracleManager::generatePerformance()`의 일시적인 DoS를 유발할 수 있습니다. 활성 검증자 중 하나만 0 `avgBalance`를 갖더라도 전체 트랜잭션 호출이 되돌려지고 업데이트가 수행되지 않기 때문입니다.
```solidity
    function _checkValidatorBehavior(
        // ...
    ) internal returns (bool isValid) {
        // Ensure we have a balance to compare against
@>        require(avgBalance > 0, "Zero balance");

        // ...
    }
```

`avgBalance = 0`인 경우 되돌리지 않고 계속 진행하는 것을 고려하십시오.
```diff
            // Check for anomalies
            if (
+              avgBalance == 0 ||
                !_checkValidatorBehavior(
                    validator, previousSlashing, previousRewards, avgSlashAmount, avgRewardAmount, avgBalance
                )
            ) {
                // Deactivate validator if behavior is unexpected
                validatorManager.deactivateValidator(validator);
                continue;
            }
```



# [L-08] 스테이커가 스테이킹 인출에 실패할 수 있음 (Stakers may fail to withdraw their staking)

StakingManager에서 스테이커는 `queueWithdrawal`을 통해 스테이킹 HYPE 인출을 요청할 수 있습니다.

인출을 대기열에 넣을 때 현재 검증자에서 관련 금액을 위임 해제합니다. 현재 구현에 따르면 하나의 스테이킹 관리자가 여러 검증자를 지원할 수 있으며 소유자는 실제 조건에 따라 다른 검증자의 위임 금액을 재조정하여 최대 이익을 얻을 수 있습니다.

문제는 사용자가 HYPE 인출을 대기열에 넣으려면 현재 검증자에서 위임을 해제해야 한다는 것입니다. 이 현재 검증자에 위임된 HYPE 금액이 충분하지 않을 수 있습니다.

예를 들어:
1. 소유자가 `setDelegation`을 통해 validatorA를 현재 검증자로 설정합니다.
2. Alice가 2000 HYPE를 스테이킹하고 이 2000 HYPE는 validatorA에 위임됩니다.
3. 소유자가 `setDelegation`을 통해 validatorB를 현재 검증자로 설정합니다.
4. Alice가 인출 대기열을 원하면 validatorB에서 2000 HYPE를 위임 해제하려고 합니다. 이 요청은 실패합니다.

```solidity
    function queueWithdrawal(uint256 amount) external nonReentrant whenNotPaused {
        require(amount > 0, "Invalid amount");
        require(kHYPE.balanceOf(msg.sender) >= amount, "Insufficient kHYPE balance");

        uint256 withdrawalId = nextWithdrawalId[msg.sender];

        // Lock kHYPE tokens
        kHYPE.transferFrom(msg.sender, address(this), amount);
        _withdrawalRequests[msg.sender][withdrawalId] = WithdrawalRequest({amount: amount, timestamp: block.timestamp});

        nextWithdrawalId[msg.sender]++;
        totalQueuedWithdrawals += amount;
        address currentDelegation = validatorManager.getDelegation(address(this));
        require(currentDelegation != address(0), "No delegation set");

        _withdrawFromValidator(currentDelegation, amount);

        emit WithdrawalQueued(msg.sender, withdrawalId, amount);
    }

```

권장 사항: 사용자가 인출 대기열을 할당할 검증자를 지정할 수 있도록 허용하십시오.
