# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Abstract-Foundation/clave-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Clave 소개 (About Clave)

Clave는 ZKsync를 기반으로 구축된 자체 수탁형(self-custodial) 스마트 지갑으로, 사용자가 계정 추상화(account abstraction)를 통해 온체인 자산을 관리할 수 있도록 합니다. 가스 요금으로 모든 토큰을 사용하여 거래할 수 있고, 닉네임과 같은 사용자 지정 기능을 제공하며, 링크 기반 공유를 통해 자산 전송을 간소화합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
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

_검토 커밋 해시_ - [3e5cb6784ad067ecba23758aa39ab78468026f5d](https://github.com/Abstract-Foundation/clave-contracts/tree/3e5cb6784ad067ecba23758aa39ab78468026f5d)

_수정 검토 커밋 해시_ - [1aba0968925632b57bccbfc07bc488687f3e8937](https://github.com/Abstract-Foundation/agw-contracts/tree/1aba0968925632b57bccbfc07bc488687f3e8937)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `ClaveImplementation`
- `BatchCaller`
- `TimestampAsserter`
- `TimestampAsserterLocator`
- `ClaveStorage`
- `Errors`
- `SessionLib`
- `ValidatorManager`
- `SessionKeyValidator`

# 발견 사항 (Findings)

# [M-01] `SessionKeyValidator`가 ZkSync 메인넷에서 작동하지 않음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`SessionKeyValidator` 계약은 `TimestampAsserterLocator.locate` 함수가 체인 ID `324`(ZkSync 메인넷)에서 명시적으로 되돌려지기 때문에 ZkSync 메인넷에서 작동하지 않습니다. 이로 인해 메인넷 배포의 모든 세션 키 기능이 중단됩니다.

```solidity
  function locate() internal view returns (ITimestampAsserter) {
    ...
    if (block.chainid == 324) { // @audit revert if the chain is ZkSync mainnet
      revert("Timestamp asserter is not deployed on ZKsync mainnet yet");
    }
    ...
  }
```

## 권장 사항

메인넷 주소로 `TimestampAsserterLocator`를 업데이트하십시오.

# [M-02] `SessionKeyValidator` 제거 시 `sessionCounter` 확인이 시행되지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

사용자가 `SessionKeyValidator`를 제거할 때 모듈 제거를 완료하기 위해 `removeModuleValidator` 및 `removeHook`을 트리거하기 전에 `sessionCounter`가 0인지 확인합니다. `sessionCounter` 확인은 나중에 모듈이 다시 설치될 경우 과거의 활성 세션 키 사용을 방지하는 데 필요합니다.

```solidity
  function disable() external {
    if (_isInitialized(msg.sender)) {
      // Here we have to revoke all keys, so that if the module
      // is installed again later, there will be no active sessions from the past.
      // Problem: if there are too many keys, this will run out of gas.
      // Solution: before uninstalling, require that all keys are revoked manually.
>>>   require(sessionCounter[msg.sender] == 0, "Revoke all keys first");
      IValidatorManager(msg.sender).removeModuleValidator(address(this));
      IHookManager(msg.sender).removeHook(address(this), true);
    }
  }
```

그러나 `sessionCounter` 확인으로 인해 `disable`이 되돌려지더라도 `disable` 호출의 성공 상태를 확인하지 않기 때문에 모듈 제거는 여전히 성공합니다.

```solidity
    function _removeModule(address module) internal {
        _modulesLinkedList().remove(module);

>>>     (bool success, ) = module.excessivelySafeCall(
            gasleft(),
            0,
            0,
            abi.encodeWithSelector(IInitable.disable.selector)
        );
        (success); // silence unused local variable warning

        emit RemoveModule(module);
    }
```

## 권장 사항

모듈을 제거할 때 `disable` 호출이 성공하는지 확인하는 옵션(추가 매개변수)을 추가하는 것을 고려하십시오. 또는 `sessionCounter[msg.sender] == 0` 확인을 `init`으로 이동하십시오.

# [M-03] `SessionKeyValidator` 모듈을 제대로 제거할 수 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

모듈이 제거되면 등록된 목록에서 모듈이 제거된 후 `disable`이 호출됩니다.

```solidity
    function _removeModule(address module) internal {
        _modulesLinkedList().remove(module);

>>>     (bool success, ) = module.excessivelySafeCall(
            gasleft(),
            0,
            0,
            abi.encodeWithSelector(IInitable.disable.selector)
        );
        (success); // silence unused local variable warning

        emit RemoveModule(module);
    }
```

`SessionKeyValidator` 내부에서 `removeModuleValidator` 및 `removeHook`을 트리거하려고 시도하는데, 이는 자체 호출(self-call) 또는 등록된 모듈에 의해서만 호출될 수 있는 제한된 함수입니다.

```solidity
  function disable() external {
    if (_isInitialized(msg.sender)) {
      // Here we have to revoke all keys, so that if the module
      // is installed again later, there will be no active sessions from the past.
      // Problem: if there are too many keys, this will run out of gas.
      // Solution: before uninstalling, require that all keys are revoked manually.
      require(sessionCounter[msg.sender] == 0, "Revoke all keys first");
>>>   IValidatorManager(msg.sender).removeModuleValidator(address(this));
>>>   IHookManager(msg.sender).removeHook(address(this), true);
    }
  }
```

```solidity
    function removeModuleValidator(address validator) external onlySelfOrModule {
        _removeModuleValidator(validator);
    }
```

```solidity
    function removeHook(address hook, bool isValidation) external override onlySelfOrModule {
        _removeHook(hook, isValidation);
    }
```

그러나 `SessionKeyValidator`가 더 이상 모듈로 등록되지 않으므로 `removeModule`이 `disable` 호출 성공을 강제하지 않기 때문에 호출은 자동으로 실패합니다. 이는 사용자에게 `SessionKeyValidator`가 더 이상 훅 및 모듈 검증자로 등록되지 않았다는 잘못된 인상을 줄 것입니다.

## 권장 사항

목록에서 모듈을 제거하기 전에 `disable` 호출을 이동하는 것을 고려하십시오.
# [M-04] `validateFeeLimit`이 거래 수수료를 적절하게 제한하지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`validateFeeLimit`는 호출자가 제공한 `maxFeePerGas` 및 `gasLimit`가 사용 제한 내에 있는지 확인하기 위해 `SessionKeyValidator`의 `validationHook` 내에서 호출됩니다.

```solidity
  function validationHook(bytes32 signedHash, Transaction calldata transaction, bytes calldata hookData) external {
    if (hookData.length == 0) {
      // There's no session data so we aren't validating anything
      return;
    }
    (bytes memory signature, address validator, ) = abi.decode(transaction.signature, (bytes, address, bytes[]));
    if (validator != address(this)) {
      // This transaction is not meant to be validated by this module
      return;
    }
    (SessionLib.SessionSpec memory spec, uint64[] memory periodIds) = abi.decode(
      hookData,
      (SessionLib.SessionSpec, uint64[])
    );
    require(spec.signer != address(0), "Invalid signer (empty)");
    bytes32 sessionHash = keccak256(abi.encode(spec));
    // this generally throws instead of returning false
    sessions[sessionHash].validate(transaction, spec, periodIds);
    (address recoveredAddress, ECDSA.RecoverError recoverError, ) = ECDSA.tryRecover(signedHash, signature);
    if (recoverError != ECDSA.RecoverError.NoError || recoveredAddress == address(0)) {
      return;
    }
    require(recoveredAddress == spec.signer, "Invalid signer (mismatch)");
    // This check is separate and performed last to prevent gas estimation failures
>>> sessions[sessionHash].validateFeeLimit(transaction, spec, periodIds[0]);

    // Set the validation result to 1 for this hash, so that isValidSignature succeeds
    uint256 slot = uint256(signedHash);
    assembly {
      tstore(slot, 1)
    }
  }
```

그러나 `validateFeeLimit`는 `paymaster`가 구성되지 않았을 때가 아니라 구성되었을 때 수수료를 잘못 확인합니다.

```solidity
  function validateFeeLimit(
    SessionStorage storage state,
    Transaction calldata transaction,
    SessionSpec memory spec,
    uint64 periodId
  ) internal {
    // This is split from `validate` to prevent gas estimation failures.
    // When this check was part of `validate`, gas estimation could fail due to
    // fee limit being smaller than the upper bound of the gas estimation binary search.
    // By splitting this check, we can now have this order of operations in `validateTransaction`:
    // 1. session.validate()
    // 2. ECDSA.tryRecover()
    // 3. session.validateFeeLimit()
    // This way, gas estimation will exit on step 2 instead of failing, but will still run through
    // most of the computation needed to validate the session.

    // TODO: update fee allowance with the gasleft/refund at the end of execution
>>> if (transaction.paymaster != 0) {
      // If a paymaster is paying the fee, we don't need to check the fee limit
      uint256 fee = transaction.maxFeePerGas * transaction.gasLimit;
      spec.feeLimit.checkAndUpdate(state.fee, fee, periodId);
    }
  }
```

이로 인해 수수료 한도가 제대로 작동하지 않아 세션 키 호출자가 제한 없이 수수료를 제공해야 합니다.

## 권장 사항

`checkAndUpdate` 조건을 다음과 같이 업데이트하십시오.

````diff
```solidity
  function validateFeeLimit(
    SessionStorage storage state,
    Transaction calldata transaction,
    SessionSpec memory spec,
    uint64 periodId
  ) internal {
    // This is split from `validate` to prevent gas estimation failures.
    // When this check was part of `validate`, gas estimation could fail due to
    // fee limit being smaller than the upper bound of the gas estimation binary search.
    // By splitting this check, we can now have this order of operations in `validateTransaction`:
    // 1. session.validate()
    // 2. ECDSA.tryRecover()
    // 3. session.validateFeeLimit()
    // This way, gas estimation will exit on step 2 instead of failing, but will still run through
    // most of the computation needed to validate the session.

    // TODO: update fee allowance with the gasleft/refund at the end of execution
-    if (transaction.paymaster != 0) {
+    if (transaction.paymaster == 0) {
      // If a paymaster is paying the fee, we don't need to check the fee limit
      uint256 fee = transaction.maxFeePerGas * transaction.gasLimit;
      spec.feeLimit.checkAndUpdate(state.fee, fee, periodId);
    }
  }
````

# [L-01] 임시 저장 슬롯(transient storage slots)에 0이 아닌 값을 남겨둠

EIP-1153 표준의 보안 고려 사항은 임시 저장 슬롯에 0이 아닌 값을 남겨두는 것을 권장하지 않습니다(https://eips.ethereum.org/EIPS/eip-1153#security-considerations).

```solidity
  function handleValidation(bytes32 signedHash, bytes memory signature) external view returns (bool) {
    // This only succeeds if the validationHook has previously succeeded for this hash.
    uint256 slot = uint256(signedHash);
    uint256 hookResult;
    assembly {
>>    hookResult := tload(slot)
    }
    require(hookResult == 1, "Can't call this function without calling validationHook");
    return true;
  }
```

# [L-02] `sessionStatus` 및 `sessionState`는 `expiresAt`을 고려하지 않음

사용자가 제공된 세션 키 상태를 얻기 위해 `sessionStatus` 및 `sessionState`를 호출하면 세션이 이미 만료되었더라도 `Active`를 반환합니다. 이 반환 값은 사용자의 오해를 불러일으켜 세션 키를 계속 사용할 수 있음을 암시할 수 있습니다. 추가 상태(예: `Expired`)를 추가하거나 세션이 이미 만료된 경우 `Closed`를 반환하는 것을 고려하십시오.

# [L-03] 32바이트를 초과하는 동적 유형에 대한 잘못된 매개변수 추출

`checkAndUpdate` 함수는 32바이트보다 큰 동적 유형을 처리할 때 호출 데이터 매개변수를 잘못 추출합니다. 이 함수는 고정된 32바이트 오프셋을 사용하여 모든 매개변수를 추출할 수 있다고 가정하지만, 더 복잡한 인코딩 체계를 사용하는 동적 유형의 경우 실패합니다.

```solidity
  function checkAndUpdate(
    Constraint memory constraint,
    UsageTracker storage tracker,
    bytes calldata data,
    uint64 period
  ) internal {
    uint256 index = 4 + constraint.index * 32;
    bytes32 param = bytes32(data[index:index + 32]); // @audit Incorrect assumption that parameter can be extracted with fixed offset
    Condition condition = constraint.condition;
    bytes32 refValue = constraint.refValue;

    ...
  }
```

다음 예를 고려해 보겠습니다.

이것은 함수 서명입니다:

```solidity
contract Example {
    function exampleFunction(string memory text, uint256[] memory numbers) external {}
}
```

두 가지 경우가 있습니다.

1.  텍스트가 32바이트 미만인 첫 번째 경우 - hello word는 32바이트 워드에 포함됩니다.

입력: `hello`, [1,2,3]

```text
- 0x303430f7                                                       // function signature
- 0000000000000000000000000000000000000000000000000000000000000040 // offset for text
- 0000000000000000000000000000000000000000000000000000000000000080 // offset for numbers
- 0000000000000000000000000000000000000000000000000000000000000005 // length of string
- 68656c6c6f000000000000000000000000000000000000000000000000000000 // hello
- 0000000000000000000000000000000000000000000000000000000000000003 // length of the array
- 0000000000000000000000000000000000000000000000000000000000000001 // 1
- 0000000000000000000000000000000000000000000000000000000000000002 // 2
- 0000000000000000000000000000000000000000000000000000000000000003 // 3
```

2. 텍스트가 32바이트보다 큰 두 번째 경우 - 데이터가 여러 32바이트 워드에 걸쳐 있습니다.

입력: `helloooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo`, [1,2,3]

```text
- 0x303430f7                                                       // function signature
- 0000000000000000000000000000000000000000000000000000000000000040 // offset for text
- 00000000000000000000000000000000000000000000000000000000000000c0 // offset for numbers
- 0000000000000000000000000000000000000000000000000000000000000048 // length of string
- 68656c6c6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f // text
- 6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f6f // text
- 6f6f6f6f6f6f6f6f000000000000000000000000000000000000000000000000 // text
- 0000000000000000000000000000000000000000000000000000000000000003 // length of the array
- 0000000000000000000000000000000000000000000000000000000000000001 // 1
- 0000000000000000000000000000000000000000000000000000000000000002 // 2
- 0000000000000000000000000000000000000000000000000000000000000003 // 3
```

두 번째 경우, `checkAndUpdate` 함수처럼 하나의 인덱스를 사용하여 텍스트를 올바르게 추출할 수 없습니다. 이는 다음과 같은 영향을 미쳐 실제 매개변수 값을 잘못 해석하게 합니다.

- 세션 키 제약 조건을 우회할 수 있음
- 동적 매개변수에 제약 조건이 설정된 경우 동적 매개변수가 있는 함수는 세션 키와 함께 사용할 수 없음

여러 32바이트 워드를 사용하여 `checkAndUpdate` 함수에서 동적 유형을 올바르게 처리하십시오.

# [L-04] 트랜잭션 대상으로 `SessionKeyValidator`를 제한하지 않음

`SessionLib.validate` 내에서 `transaction.to`가 `msg.sender`로 설정되지 않았는지 확인하여 호출자가 세션 키를 악용하여 스마트 계정을 관리하는 것을 방지합니다. 그러나 `transaction.to`를 `SessionKeyValidator`로 설정하는 것은 허용하므로 호출자가 허용되는 경우 제한 없는 호출로 추가 세션 키를 생성할 수 있습니다.

```solidity
  function validate(
    SessionStorage storage state,
    Transaction calldata transaction,
    SessionSpec memory spec,
    uint64[] memory periodIds
  ) internal {
    // Here we additionally pass uint64[] periodId to check allowance limits
    // periodId is defined as block.timestamp / limit.period if limitType == Allowance, and 0 otherwise (which will be ignored).
    // periodIds[0] is for fee limit,
    // periodIds[1] is for value limit,
    // periodIds[2:] are for call constraints, if there are any.
    // It is required to pass them in (instead of computing via block.timestamp) since during validation
    // we can only assert the range of the timestamp, but not access its value.

    require(state.status[msg.sender] == Status.Active, "Session is not active");
    TimestampAsserterLocator.locate().assertTimestampInRange(0, spec.expiresAt);

    address target = address(uint160(transaction.to));

    if (transaction.paymasterInput.length >= 4) {
      bytes4 paymasterInputSelector = bytes4(transaction.paymasterInput[0:4]);
      require(
        paymasterInputSelector != IPaymasterFlow.approvalBased.selector,
        "Approval based paymaster flow not allowed"
      );
    }

    if (transaction.data.length >= 4) {
      // Disallow self-targeting transactions with session keys as these have the ability to administer
      // the smart account.
>>>   require(address(uint160(transaction.to)) != msg.sender, "Can not target self");

   // ...
  }
```

`transaction.to`가 `SessionKeyValidator` 주소로 설정된 경우 되돌리는(revert) 것을 고려하십시오.
