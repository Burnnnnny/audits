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

_검토 커밋 해시_ - [b2c88fc704b41430a5225f60416e86161628c178](https://github.com/Abstract-Foundation/clave-contracts/tree/b2c88fc704b41430a5225f60416e86161628c178)

_수정 검토 커밋 해시_ - [bbc9b2e09c4dc213681c99534246b9d43545bd67](https://github.com/Abstract-Foundation/clave-contracts/tree/bbc9b2e09c4dc213681c99534246b9d43545bd67)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `AccountFactory`
- `ClaveImplementation`
- `ClaveProxy`
- `EOAValidator`
- `PasskeyValidator`
- `ERC1271Handler`
- `ValidationHandler`
- `BatchCaller`
- `BatchCaller`
- `ClaveStorage`
- `Errors`
- `SignatureDecoder`
- `HookManager`
- `ModuleManager`
- `OwnerManager`
- `UpgradeManager`
- `ValidatorManager`
- `TokenCallbackHandler`
- `Auth`
- `LinkedList`
- `VerifierCaller`
- `ClaveRegistry`
- `BootloaderAuth`
- `SelfAuth`
- `HookAuth`
- `ModuleAuth`

# 발견 사항 (Findings)

# [H-01] `executeTransactionFromOutside`는 nonce를 증가시키지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`ClaveImplementation`은 `executeTransactionFromOutside`를 구현하며, 지갑 소유자가 폴백(fallback)으로 트랜잭션을 수동으로 실행하기 위해 이를 호출할 수 있습니다. 그러나 이 실행은 지갑의 nonce를 증가시키지 않습니다.

```solidity
    function executeTransactionFromOutside(
        Transaction calldata transaction
    ) external payable override {
        // Check if msg.sender is authorized
        if (!_k1IsOwner(msg.sender)) {
            revert Errors.UNAUTHORIZED_OUTSIDE_TRANSACTION();
        }

        // Extract hook data from transaction.signature
        bytes[] memory hookData = SignatureDecoder.decodeSignatureOnlyHookData(
            transaction.signature
        );

        // Get the hash of the transaction
        bytes32 signedHash = transaction.encodeHash();

        // Run the validation hooks
        if (!runValidationHooks(signedHash, transaction, hookData)) {
            revert Errors.VALIDATION_HOOK_FAILED();
        }

        _executeTransaction(transaction);
    }
```

실행 운영자가 응답하지 않아 지갑 소유자가 트랜잭션을 수동으로 실행하기로 결정하는 시나리오를 고려해 보십시오. 운영자가 다시 활성화되어 요청을 처리하면 `executeTransactionFromOutside`가 호출될 때 nonce가 증가하지 않으므로 트랜잭션을 여전히 실행할 수 있어 잠재적으로 의도하지 않은 이중 실행이 발생할 수 있습니다.

## 권장 사항

`executeTransactionFromOutside` 실행 내에서 nonce를 증가시키십시오.

# [M-01] 등록된 모듈이 중요한 함수에 접근할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

등록된 모듈은 `onlySelfOrModule` 수정자를 통해 `addModule` / `removeModule`, `r1AddOwner` / `k1AddOwner`, `r1RemoveOwner` / `k1RemoveOwner` / `resetOwners`, `r1AddValidator` / `k1AddValidator`, `r1RemoveValidator` / `k1RemoveValidator`와 같은 여러 중요한 함수에 접근할 수 있습니다. 이러한 과도한 접근 권한은 악의적으로 등록된 모듈이 소유자나 검증자를 변경하여 지갑을 가로채는 것을 허용할 수 있습니다.

## 권장 사항

언급된 함수를 `onlySelf`로 제한하여 등록된 모듈에 대한 과도한 접근 권한을 방지하는 것을 고려하십시오.

# [M-02] `removeHook`은 컨텍스트(context)가 존재하면 제거를 시도해야 함

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`runExecutionHooks` 수정자 내에서 등록된 모든 실행 훅을 반복하고 `preExecutionHook`을 트리거하고 훅의 `context`를 설정합니다. 그런 다음 실행이 끝날 때 다시 반복하여 `context`가 존재하면 `postExecutionHook`을 트리거하고 마지막으로 컨텍스트를 삭제합니다.

```solidity
    modifier runExecutionHooks(Transaction calldata transaction) {
        mapping(address => address) storage executionHooks = _executionHooksLinkedList();

        address cursor = executionHooks[AddressLinkedList.SENTINEL_ADDRESS];
        // Iterate through hooks
        while (cursor > AddressLinkedList.SENTINEL_ADDRESS) {
            // Call the preExecutionHook function with transaction struct
            bytes memory context = IExecutionHook(cursor).preExecutionHook(transaction);
            // Store returned data as context
            _setContext(cursor, context);

            cursor = executionHooks[cursor];
        }

        _;

        cursor = executionHooks[AddressLinkedList.SENTINEL_ADDRESS];
        // Iterate through hooks
        while (cursor > AddressLinkedList.SENTINEL_ADDRESS) {
            bytes memory context = _getContext(cursor);
            if (context.length > 0) {
                // Call the postExecutionHook function with stored context
                IExecutionHook(cursor).postExecutionHook(context);
                // Delete context
                _deleteContext(cursor);
            }

            cursor = executionHooks[cursor];
        }
    }
```

`runExecutionHooks` 수정자가 있는 함수를 실행하는 동안 `removeHook`을 호출하고 등록된 훅 중 하나를 제거할 수 있습니다. 그러나 제거된 훅은 더 이상 `executionHooks`에 등록되지 않으므로 `runExecutionHooks` 수정자의 실행이 끝날 때 컨텍스트가 제거되지 않습니다.

이는 특히 제거된 훅이 나중에 다시 추가되는 경우 이전 컨텍스트가 일부 작업 중에 사용될 수 있으므로 예기치 않은 동작을 초래할 수 있습니다.

## 권장 사항

`removeHook`이 호출될 때 훅의 컨텍스트를 제거하십시오.

# [M-03] 조기 반환(early return)으로 인해 거래 수수료가 과소평가될 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`IAccount.validateTransaction` 메서드는 검증 외에도 `가스 요금 추정 중에도 사용됩니다`. 이러한 방식으로 프로토콜은 `유효한 트랜잭션과 유효하지 않은 트랜잭션 모두에 대해 가능한 한 많은 단계를 보존`해야 합니다. 결과적으로 `ClaveImplementation.validateTransaction` 함수에는 가스 요금 추정 호출에 대한 조기 반환이 있습니다. 따라서 많은 가스 소비 검사가 추정되지 않은 상태로 유지됩니다.

```solidity
    function _validateTransaction(
        bytes32 signedHash,
        Transaction calldata transaction
    ) internal returns (bytes4 magicValue) {
        if (transaction.signature.length == 65) {
            // This is a gas estimation
>>          return bytes4(0);
        }

        // Extract the signature, validator address and hook data from the transaction.signature
        (bytes memory signature, address validator, bytes[] memory hookData) = SignatureDecoder
            .decodeSignature(transaction.signature);

        // Run validation hooks
        bool hookSuccess = runValidationHooks(signedHash, transaction, hookData);

        if (!hookSuccess) {
>>          return bytes4(0);
        }

        bool valid = _handleValidation(validator, signedHash, signature);

        magicValue = valid ? ACCOUNT_VALIDATION_SUCCESS_MAGIC : bytes4(0);
    }
```

## 권장 사항

가스 요금 추정에 대한 `IAccount` 권장 사항을 따르고, 명백히 잘못된 서명 길이의 경우 서명이 유효한 것처럼 보이게 하는 것을 고려하십시오. 예를 들어 (https://code.zksync.io/tutorials/native-aa-multisig#prerequisites):

```solidity
        if (_signature.length != 130) {
            // Signature is invalid anyway, but we need to proceed with the signature verification as usual
            // in order for the fee estimation to work correctly
            _signature = new bytes(130);

            // Making sure that the signatures look like a valid ECDSA signature and are not rejected rightaway
            // while skipping the main verification process.
            _signature[64] = bytes1(uint8(27));
            _signature[129] = bytes1(uint8(27));
        }
```

`hookSuccess` 결과는 나중에 확인할 수도 있습니다.

```diff
        bool hookSuccess = runValidationHooks(signedHash, transaction, hookData);

-       if (!hookSuccess) {
-           return bytes4(0);
-       }

        bool valid = _handleValidation(validator, signedHash, signature);

-       magicValue = valid ? ACCOUNT_VALIDATION_SUCCESS_MAGIC : bytes4(0);
+       magicValue = hookSuccess && valid ? ACCOUNT_VALIDATION_SUCCESS_MAGIC : bytes4(0);
```
# [M-04] 고장 난 초기 호출 기능

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`ClaveImplementation.initialize` 함수는 임의의 `value`로 초기 호출을 진행할 수 있지만, `initialize` 및 `AccountFactory.deployAccount` 함수 모두에서 `payable` 수정자가 누락되었습니다. `deployAccount` 함수는 또한 하드코딩된 `0` 값으로 계정을 초기화합니다. 이 방식으로는 초기 호출 기능이 부분적으로만 구현됩니다.

```solidity
    function deployAccount(
        bytes32 salt,
        bytes memory initializer
>>  ) external returns (address accountAddress) {
<...>
        // Initialize the account
        bool initializeSuccess;

        assembly ('memory-safe') {
            initializeSuccess := call(
                gas(),
                accountAddress,
>>              0,
                add(initializer, 0x20),
                mload(initializer),
                0,
                0
            )
        }
<...>
    function initialize(
        address initialK1Owner,
        address initialK1Validator,
        bytes[] calldata modules,
        Call calldata initCall
>>  ) public initializer {
<...>
        if (initCall.target != address(0)) {
>>          uint128 value = Utils.safeCastToU128(initCall.value);
            _executeCall(initCall.target, value, initCall.callData, initCall.allowFailure);
        }
    }
```

## 권장 사항

`deployAccount` 및 `initialize` 함수를 `payable`로 선언하고 하드코딩된 0 대신 `msg.value`를 `value` 매개변수로 제공하는 것을 고려하십시오.

# [M-05] 사용자가 배포 제한을 우회할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`deployAccount` 함수는 사용자가 제어된 초기화 매개변수로 자신의 스마트 계정을 배포할 수 있도록 의도되었습니다. 사용자 제공 `initializer` 데이터를 받아들이며, 이는 계정을 올바르게 설정하기 위해 배포된 `claveProxy` 계약의 `initialize` 함수를 호출할 것으로 예상됩니다.

```solidity
function deployAccount(
    bytes32 salt,
    bytes memory initializer
) external returns (address accountAddress) {
    // Deployment logic
}
```

배포 중에 `claveProxy` 계약이 생성됩니다.

```solidity
(bool success, bytes memory returnData) = SystemContractsCaller.systemCallWithReturndata(
    uint32(gasleft()),
    address(DEPLOYER_SYSTEM_CONTRACT),
    uint128(0),
    abi.encodeCall(
        DEPLOYER_SYSTEM_CONTRACT.create2Account,
        (
            salt,
            proxyBytecodeHash,
            abi.encode(implementationAddress),
            IContractDeployer.AccountAbstractionVersion.Version1
        )
    )
);
```

배포 후, 사용자 제공 `initializer` 호출 데이터는 저수준 호출을 통해 배포된 계약으로 직접 전달됩니다.

```solidity
assembly ('memory-safe') {
    initializeSuccess := call(
        gas(),
        accountAddress,
        0,
        add(initializer, 0x20),
        mload(initializer),
        0,
        0
    )
}
```

`claveProxy` 계약의 `initialize` 함수에는 승인된 배포자만 `initialK1Owner`를 설정할 수 있도록 하는 확인이 포함되어 있습니다.

```solidity
if (thisDeployer != factory.deployer()) {
    if (initialK1Owner != thisDeployer) {
        revert Errors.NOT_FROM_DEPLOYER();
    }
}
```

그러나 `initializer` 데이터는 전적으로 사용자가 제어하므로 공격자는 이를 조작하여 다른 함수를 호출한 다음 자신의 `AccountFactory` 계약 복제본을 배포하여 이를 사용하여 `initialize` 함수를 호출하고 원하는 K1 소유자 주소를 설정하여 확인을 우회할 수 있습니다.

## 권장 사항

사용자 제공 `initializer` 데이터를 제한하거나 검증하여 신뢰할 수 있는 매개변수로 `initialize` 함수만 독점적으로 호출하도록 하십시오. 조작을 방지하기 위해 `deployAccount` 함수 내에서 함수 선택자(selector)를 `initialize` 함수로 하드코딩하는 것을 고려하십시오.

# [M-06] WebAuthn 프로토콜에서 상수 인증자 데이터 사용

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`PasskeyValidator` 계약의 `_validateSignature` 함수는 WebAuthn 프로토콜에서 파생된 서명을 확인하는 역할을 합니다. 이 검증 프로세스는 트랜잭션 해시, 특정 WebAuthn 프로토콜 문자열 및 상수 `AUTHENTICATOR_DATA`를 포함하는 메시지 해시를 구성합니다.

```solidity
clientData = bytes.concat(
    bytes(ClIENT_DATA_PREFIX),
    challengeBase64,
    bytes(IOS_ClIENT_DATA_SUFFIX)
);

bytes32 message = _createMessage(AUTHENTICATOR_DATA, clientData);
```

`AUTHENTICATOR_DATA`는 하드코딩된 카운터 값이 0인 상수로 정의됩니다.

```solidity
// hash of 'https://getclave.io' + (BE, BS, UP, UV) flags set + unincremented sign counter
bytes constant AUTHENTICATOR_DATA =
    hex'175faf8504c2cdd7c01778a8b0efd4874ecb3aefd7ebb7079a941f7be8897d411d00000000';
```

**`authData`**는 다음 필드로 구성되며 제시된 순서대로 연결됩니다.

1. **`rpIdHash` (32바이트):** 신뢰 당사자 식별자(RP ID)의 SHA-256 해시.
2. **`flags` (1바이트):** 사용자 존재, 사용자 확인 및 추가 데이터 포함을 나타내는 비트 플래그.
3. **`signCount` (4바이트):** 재생(replay) 공격을 방지하기 위한 서명 카운터.
4. **`attestedCredentialData` (가변 길이, 선택 사항):** 자격 증명 정보를 포함합니다. 등록 중에 존재합니다.
5. **`extensions` (가변 길이, 선택 사항):** 확장 데이터를 포함합니다. 확장이 사용되는 경우 포함됩니다.

정적 카운터가 있는 상수 `AUTHENTICATOR_DATA`를 사용함으로써 계약은 인증자 카운터의 동적 특성을 고려하지 못합니다. 인증자(예: 보안 인클레이브 또는 TPM)는 각 서명 작업마다 이 카운터를 증가시킵니다. 결과적으로 인증자가 생성한 서명에는 계약에서 예상하는 정적 값과 일치하지 않는 카운터 값이 포함됩니다.

이러한 불일치로 인해 모든 서명 검증이 실패하여 `PasskeyValidator`를 통한 서명 검증에 의존하는 모든 사용자 계정 작업에 대해 사실상 서비스 거부(DoS)가 발생합니다. 그러면 사용자는 대체 검증자나 키 소유자에 의존해야 하므로 시스템의 보안과 사용성이 약화됩니다.

## 권장 사항

인증자의 증가하는 카운터를 포함하는 동적 `authenticatorData`를 수락하고 처리하도록 계약을 수정하십시오.

# [L-01] `ClaveImplementation` 내에 `payable fallback` 부족

`ClaveImplementation`에 `fallback payable` 함수가 없기 때문에 데이터가 포함된 트랜잭션으로 네이티브 ETH를 전송하려는 시도는 항상 되돌려집니다. `ClaveImplementation` 내에 `fallback payable`을 추가하는 것을 고려하십시오.

# [L-02] 최대 개수 확인 부족

`runValidationHooks` 및 `runExecutionHooks`는 등록된 `validationHooks` 및 `executionHooks`를 반복하여 정의된 작업을 수행합니다. 그러나 등록된 훅이 너무 많으면 이러한 작업에서 가스가 부족할 수 있어 예기치 않은 되돌리기(revert) 및 열악한 사용자 경험으로 이어질 수 있습니다. 지갑 내에 등록된 훅의 수를 제한하는 것을 고려하십시오.

# [L-03] 잘못된 `hookData` 길이로 인한 `validateTransaction`에서의 예기치 않은 되돌리기(revert)

`HookManager.runValidationHooks` 함수는 임의 길이의 `hookData` 배열을 허용하고 `hookData.length != idx`일 때 `false`를 반환합니다. 그러나 실제로 이 확인은 `hookData.length`가 검증자 수를 초과하는지 여부만 검증합니다. 결과적으로 `hookData.length`가 검증자 수보다 작을 때 예기치 않은 되돌리기가 발생할 수 있습니다. `idx < hookData.length`일 때만 다음 검증자를 호출하고 그렇지 않으면 `false`를 반환하는 것을 고려하십시오.

```solidity
    function runValidationHooks(
        bytes32 signedHash,
        Transaction calldata transaction,
        bytes[] memory hookData
    ) internal returns (bool) {
        mapping(address => address) storage validationHooks = _validationHooksLinkedList();

        address cursor = validationHooks[AddressLinkedList.SENTINEL_ADDRESS];
        uint256 idx = 0;
        // Iterate through hooks
        while (cursor > AddressLinkedList.SENTINEL_ADDRESS) {
            // Call it with corresponding hookData
            bool success = _call(
                cursor,
                abi.encodeWithSelector(
                    IValidationHook.validationHook.selector,
                    signedHash,
                    transaction,
>>                  hookData[idx++]
                )
            );

            if (!success) {
                return false;
            }

            cursor = validationHooks[cursor];
        }

        // Ensure that hookData is not tampered with
>>      if (hookData.length != idx) return false;

        return true;
    }
```

# [L-04] 잘못된 `validator` 주소로 인해 `validateTransaction` 메서드가 되돌려짐(revert)

잘못된 `validator` 주소 매개변수는 `AddressLinkedList.exists` 뷰(view) 함수의 `validAddress` 수정자로 인해 잘못된 서명에 대해 `bytes4(0)`을 반환하는 대신 `ClaveImplementation.validateTransaction` 함수에서 되돌리기(revert)를 유발할 수 있습니다. 의무 사항은 아니지만 수수료 추정이 작동하도록 하려면 되돌리기를 자제하는 것이 좋습니다. `validator <= SENTINEL_ADDRESS`일 때 `_handleValidation`에서 `false`를 반환하는 것을 고려하십시오.

```solidity
<...>
    function _handleValidation(
        address validator,
        bytes32 signedHash,
        bytes memory signature
    ) internal view returns (bool) {
>>      if (_r1IsValidator(validator)) {
<...>
>>      } else if (_k1IsValidator(validator)) {
<...>
        }

        return false;
    }
<...>
    function _r1IsValidator(address validator) internal view returns (bool) {
        return _r1ValidatorsLinkedList().exists(validator);
    }

    function _k1IsValidator(address validator) internal view returns (bool) {
        return _k1ValidatorsLinkedList().exists(validator);
    }
<...>
    modifier validAddress(address value) {
        if (value <= SENTINEL_ADDRESS) {
>>          revert Errors.INVALID_ADDRESS();
        }
        _;
    }
<...>
    function exists(
        mapping(address => address) storage self,
        address value
>>  ) internal view validAddress(value) returns (bool) {
        return self[value] != address(0);
    }
```
