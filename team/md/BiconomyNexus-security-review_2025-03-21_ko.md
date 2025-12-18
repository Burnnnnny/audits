# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**bcnmy/nexus** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Nexus 소개

Nexus는 크로스 체인 상호 운용성을 기본적으로 지원하는 계정 추상화를 위한 표준화된 프레임워크를 제공하는 ERC-7579 모듈식 스마트 계정의 Biconomy 구현입니다. 아키텍처는 ERC-4337 및 ERC-7484 표준을 준수하면서 가스 추상화, 트랜잭션 일괄 처리 및 세션 키 관리를 위한 플러그형 모듈을 가능하게 합니다.

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

_검토 커밋 해시_ - [6395f8fdd7ff7ddd8b4a1c005c5f9a18d854a2de](https://github.com/bcnmy/nexus/tree/6395f8fdd7ff7ddd8b4a1c005c5f9a18d854a2de)

_수정 검토 커밋 해시_ - [9af2d1d2d71c45b1a519000f51ae39a8944322f3](https://github.com/bcnmy/nexus/tree/9af2d1d2d71c45b1a519000f51ae39a8944322f3)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `BaseAccount` 
- `ExecutionHelper` 
- `ModuleManager` 
- `RegistryAdapter` 
- `Storage` 
- `Stakeable` 
- `BiconomyMetaFactory` 
- `K1ValidatorFactory` 
- `NexusAccountFactory` 
- `LocalCallDataParserLib` 
- `AssociatedArrayLib` 
- `BootstrapLib` 
- `BytesLib` 
- `EnumerableMap4337` 
- `EnumerableSet4337` 
- `ExecLib` 
- `Initializable` 
- `ModeLib` 
- `ModuleTypeLib` 
- `NonceLib` 
- `ProxyLib` 
- `interfaces/` 
- `K1Validator` 
- `Constants` 
- `DataTypes` 
- `Nexus` 

# 발견 사항

# [M-01] 모듈 설치 시 활성화 모드를 통한 훅 우회

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

Nexus 스마트 계정은 훅 메커니즘을 사용하여 모듈 설치와 같은 작업에 대한 사전 검사 및 사후 검사를 구현합니다. 그러나 `validateUserOp`의 모듈 활성화 모드를 통해 모듈이 설치될 때 이러한 훅을 우회할 수 있는 취약점이 있습니다.

`installModule`을 사용하여 일반적인 흐름을 통해 모듈이 설치되면 `withHook` 수정자가 적용되어 훅의 `preCheck` 및 `postCheck` 함수를 올바르게 호출합니다.

```solidity
function installModule(uint256 moduleTypeId, address module, bytes calldata initData) external payable onlyEntryPointOrSelf {
    _installModule(moduleTypeId, module, initData);
    emit ModuleInstalled(moduleTypeId, module);
}

function _installModule(uint256 moduleTypeId, address module, bytes calldata initData) internal withHook {
    ...
}
```

훅의 preCheck 및 postCheck는 다음을 받습니다.
- msg.sender: 호출자(예: 사용자 또는 EntryPoint).
- msg.data: 함수 선택자를 포함한 installModule에 대한 calldata.

이 경우 훅은 msg.data에 installModule 선택자가 포함되어 있으므로 모듈 설치가 발생하고 있음을 올바르게 식별할 수 있습니다. 그런 다음 의도한 보안 검사를 적용할 수 있습니다.

그러나 `validateUserOp`의 모듈 활성화 모드를 통해 모듈을 설치할 때 실행 경로는 다릅니다.

`validateUserOp`는 내부 함수 `_enableMode`를 호출합니다.

```solidity
    function validateUserOp(
        PackedUserOperation calldata op,
        bytes32 userOpHash,
        uint256 missingAccountFunds
    )
        external
        virtual
        payPrefund(missingAccountFunds)
        onlyEntryPoint
        returns (uint256 validationData)
    {
        ...
        } else if (op.nonce.isModuleEnableMode()) {
            // if it is module enable mode, we need to enable the module first
            // and get the cleaned signature
            userOp.signature = _enableMode(userOpHash, op.signature);
        ...
    }
```

내부 함수 `_enableMode`는 내부 함수 `_installModule`을 호출합니다.

```solidity
    function _enableMode(bytes32 userOpHash, bytes calldata packedData) internal returns (bytes calldata userOpSignature) {
        ...
        _installModule(moduleType, module, moduleInitData);
    }
```

`_installModule`이 호출되고 `withHook`을 트리거합니다.

```solidity
    function _installModule(uint256 moduleTypeId, address module, bytes calldata initData) internal withHook {
        ...
    }
```

`_installModule`에 `withHook` 수정자가 있지만 훅의 preCheck 및 postCheck는 다음 컨텍스트를 받습니다.

- msg.sender: EntryPoint 계약 (사용자의 주소가 아님)
- msg.data: `installModule`이 아닌 `validateUserOp`에 대한 함수 선택자를 포함합니다.

이 컨텍스트 불일치는 취약점을 생성합니다. 함수 선택자를 기반으로 보안 결정을 내리는 모든 훅은 모듈 설치가 발생하고 있음을 식별하지 못합니다. 훅은 `installModule` 호출 대신 `validateUserOp` 호출을 보기 때문에 작업이 진행되도록 허용하여 효과적으로 훅의 보호를 우회할 수 있습니다.

```solidity
    modifier withHook() {
        ...
                tstore(HOOKING_FLAG_TRANSIENT_STORAGE_SLOT, 1)
            }
            bytes memory hookData = IHook(hook).preCheck(msg.sender, msg.value, msg.data);
            _;
            IHook(hook).postCheck(hookData);
        }
    }
```

## 권장 사항

`_installModule` 내부 함수 대신 `installModule` 외부 함수를 호출하십시오.

# [M-02] 사용자는 `Nexus` 계정에서 모든 검증자를 삭제할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

Nexus 스마트 계정 시스템에서 검증자는 `validateUserOp` 함수를 통해 트랜잭션을 승인하는 데 필수적입니다. `_uninstallValidator` 함수를 사용하면 사용자가 계정에서 검증자를 제거할 수 있습니다. 사용자가 모든 검증자를 제거하면 계정은 `_DEFAULT_VALIDATOR`로 돌아갑니다. 그러나 이 기본 검증자가 초기화되지 않은 경우(사용자가 계정 설정 중에 구성하지 않은 경우 가능) 계정을 사용할 수 없게 됩니다. 구체적으로 `validateUserOp` 함수는 활성 검증자가 없기 때문에 실패하여 사용자가 계정에서 잠기게 됩니다.

```solidity
    function _uninstallValidator(address validator, bytes calldata data) internal virtual {
        SentinelListLib.SentinelList storage validators = _getAccountStorage().validators;
        (address prev, bytes memory disableModuleData) = abi.decode(data, (address, bytes));
    

        // Perform the removal first
        validators.pop(prev, validator);

        validator.excessivelySafeCall(gasleft(), 0, 0, abi.encodeWithSelector(IModule.onUninstall.selector, disableModuleData));
    }
```

## 권장 사항

다음 구현을 고려하십시오.
- 마지막 검증자의 제거를 방지합니다. 또는
- 계정 생성 시 기본 검증자를 초기화합니다.

# [L-01] 스토리지 패턴 불일치로 `registry` 변수 노출

현재 `registry`는 `setRegistry`를 사용하여 자체적으로 호출해야만 업데이트됩니다. 그러나 `RegistryAdapter` 계약은 코드베이스의 나머지 부분에서 사용되는 ERC-7201 네임스페이스 스토리지 패턴을 사용하지 않고 `registry` 스토리지 변수를 `slot 0`에 저장합니다. 이는 레지스트리 변수가 delegate call이나 업그레이드 중에 조작될 수 있으므로 보안 취약점을 생성합니다.

```solidity
abstract contract RegistryAdapter {
    IERC7484 public registry; 
```
악의적인 행위자가 정교하게 제작된 delegate call을 통해 레지스트리 주소를 수정하거나 향후 구현이 실수로 이 스토리지 슬롯을 덮어쓸 수 있습니다.

`RegistryAdapter` 계약은 코드베이스의 나머지 부분과 일치하는 ERC-7201 네임스페이스 스토리지 패턴을 사용하는 것이 좋습니다.

# [L-02] `handlePREP()`의 길이 유효성 검사 불충분

`_handlePREP` 함수에서 코드는 data.length가 최소 0x61(97) 바이트인지 확인합니다. 그러나 이 유효성 검사는 불완전하며 initData의 최소 크기를 제대로 고려하지 않습니다.

데이터 구조는 다음을 포함해야 합니다.

- `saltAndDelegation`: 32 바이트.
- `initData`에 대한 포인터: 32 바이트.
- `cleanedSignature`에 대한 포인터: 32 바이트.
- `initData`의 길이: 32 바이트.
- `initData`: 최소 4 바이트 (함수 선택자).
- `cleanedSignature`의 길이: 32 바이트.
- `cleanedSignature`: ECDSA 서명의 경우 최소 65 바이트.

합계는 229 바이트입니다.

```solidity
    function _handlePREP(bytes calldata data) internal returns (bytes calldata cleanedSignature, bytes calldata initData) {
        bytes32 saltAndDelegation;
        // unpack the data
        assembly {
            if lt(data.length, 0x61) { // @audit 97 bytes
                mstore(0x0, 0xaed59595) // NotInitializable()
                revert(0x1c, 0x04)
            }
        ...
    }
```

모든 데이터 구성 요소의 최소 크기 요구 사항을 229 바이트로 고려하여 길이 확인을 업데이트하는 것이 좋습니다.

# [L-03] Nexus `checkERC7739Support()`는 사용되지 않더라도 `DEFAULT_VALIDATOR`를 확인

`Nexus` 계약의 `checkERC7739Support` 함수는 설치된 검증자를 반복하고 추가로 `_DEFAULT_VALIDATOR`를 확인하여 `ERC-7739` 지원을 확인합니다. 그러나 예를 들어 사용자 지정 검증자만 지정할 수 있는 `NexusBootstrap.initNexus`를 사용하여 스마트 계정이 초기화된 경우 이 기본 검증자가 전혀 설치되지 않았을 수 있습니다.

```solidity
    function checkERC7739Support(bytes32 hash, bytes calldata signature) public view virtual returns (bytes4) {
        bytes4 result;
        unchecked {
            // ...
        }
        result = _get7739Version(_DEFAULT_VALIDATOR, result, hash, signature); // check default validator
        return result == bytes4(0) ? bytes4(0xffffffff) : result;
    }
```
`checkERC7739Support` 내에서 호출하기 전에 `_DEFAULT_VALIDATOR`가 이 스마트 계정에 대해 설치되어 있는지 확인하는 검사를 추가하십시오.

# [L-04] `validateUserOp()` 검증자에 대한 `withRegistry` 수정자 누락

`Nexus` 스마트 계약의 `validateUserOp` 함수는 검증자 모듈에 대해 `withRegistry` 확인을 수행하지 않습니다.

```solidity
    function validateUserOp(
        PackedUserOperation calldata op,
        bytes32 userOpHash,
        uint256 missingAccountFunds
    )
        external
        virtual
        payPrefund(missingAccountFunds)
        onlyEntryPoint
        returns (uint256 validationData)
    {
```
이는 예를 들어 `executeFromExecutor`에서 `withRegistry` 수정자를 통해 검증되는 실행자 모듈의 동작과 일치하지 않습니다.

```solidity
    function executeFromExecutor(
        ExecutionMode mode,
        bytes calldata executionCalldata
    )
        external
        payable
        onlyExecutorModule
        withHook
        withRegistry(msg.sender, MODULE_TYPE_EXECUTOR)
        returns (bytes[] memory returnData)
    {
```
이러한 불일치로 인해 더 이상 `registry`에서 확인되지 않은 검증자 모듈이 사용자 작업 유효성 검사 중에 사용될 수 있으며 결국 레지스트리 기반 증명 확인을 우회할 수 있습니다.

검증자의 주소를 결정하기 위해 `_handleValidator`를 사용하여 `validateUserOp` 내부의 검증자 모듈에 대한 `withRegistry` 확인을 추가하는 것을 고려하십시오.

# [L-05] `createAccount()`는 사전 검증 훅에 대해 `isModuleAllowed`를 건너뜀

`RegistryFactory` 계약의 `createAccount` 함수는 `_isModuleAllowed` 확인을 사용하여 각 모듈(검증자, 실행자, 훅 및 폴백)이 화이트리스트에 있는지 확인합니다. 그러나 `Nexus` 계정(`MODULE_TYPE_PREVALIDATION_HOOK_ERC1271` 및 `MODULE_TYPE_PREVALIDATION_HOOK_ERC4337` 하에)에서 새로 지원되는 모듈 유형인 Pre-Validation Hook 모듈에 대해서는 이 검사를 수행하지 않습니다.

`registry`로 모든 모듈의 유효성을 확인하는 일관된 방법을 보장하기 위해 `createAccount` 함수에 `MODULE_TYPE_PREVALIDATION_HOOK_ERC1271` 및 `MODULE_TYPE_PREVALIDATION_HOOK_ERC4337`에 대한 `_isModuleAllowed` 확인을 포함하는 것을 고려하십시오.

# [L-06] `createAccount()`에서 훅을 선택 해제할 수 없음

`NexusBootstrap::_initNexus` 및 기타 관련 초기화 함수(예: `_initNexusWithDefaultValidatorAndOtherModules`)를 사용하면 사용자가 `hook.module`을 `address(0)`으로 설정하여 훅 모듈 설치를 선택 해제할 수 있습니다.

```solidity
if (hook.module != address(0)) {
    _installHook(hook.module, hook.data);
    emit ModuleInstalled(MODULE_TYPE_HOOK, hook.module);
}
```
이는 의도적인 것이며 훅이 필요하지 않은 유연한 계정 설정을 가능하게 합니다.

그러나 `NexusBootstrap`이 이를 지원하더라도 팩토리 함수 `RegistryFactory::createAccount`는 지원하지 않습니다. 훅 모듈에서 무조건 `_isModuleAllowed`를 호출합니다.

```solidity
require(_isModuleAllowed(hook.module, MODULE_TYPE_HOOK), ModuleNotWhitelisted(hook.module));
```

`hook.module == address(0)`(사용자가 훅을 선택 해제했음을 의미)인 경우 부트스트랩 로직이 설치를 건너뛰었더라도 0 주소에 대한 화이트리스트에 없는 모듈 검사로 인해 이 호출이 되돌려집니다.

이 문제를 해결하고 `RegistryFactory::createAccount`를 부트스트랩 로직과 일치시키려면 `hook.module == address(0)`일 때 `_isModuleAllowed`를 건너뛰는 것을 고려하십시오.

```diff
+ if (hook.module != address(0)) {
+     require(_isModuleAllowed(hook.module, MODULE_TYPE_HOOK), ModuleNotWhitelisted(hook.module));
+ }
```

# [L-07] RegistryFactory::removeAttester는 증명자 수를 제한 미만으로 줄일 수 있음

`RegistryFactory` 생성자는 초기 `threshold`가 제공된 `attesters` 수보다 작거나 같아야 한다고 강제합니다. 그러나 `removeAttester` 함수는 이 제약 조건을 강제하지 않습니다. 결과적으로 증명자 목록이 임계값 아래로 줄어들 수 있으며, 이는 `_checkRegistry`를 통한 레지스트리 사용 시 심각한 문제를 일으킬 수 있습니다.

`removeAttester`에 확인을 추가하여 새로운 증명자 길이가 현재 임계값보다 크거나 같은지 확인하는 것을 고려하십시오.

```solidity
require(attesters.length - 1 >= threshold, "Cannot go below threshold");
```

# [L-08] 오래된 `emergencyUninstallTimelock`이 `emergencyUninstallHook`에 영향을 미칠 수 있음

스마트 계정에서 훅이 작동하지 않거나 악의적인 경우 하나의 훅을 제거합니다. `emergencyUninstallHook`, `uninstallModule`과 같이 하나의 훅을 제거하는 몇 가지 방법이 있습니다.

`emergencyUninstallHook`에는 제거를 위한 하나의 타임락이 있습니다. 타임락을 통과한 후 검증자 모듈을 제거할 수 있습니다.

아래 시나리오를 고려해 보겠습니다.
1. 사용자가 이 훅을 제거할 준비를 하기 위해 `emergencyUninstallHook`을 트리거합니다. 그리고 `emergencyUninstallTimelock`은 타임스탬프 X에 기록됩니다.
2. 실행자는 `executeFromExecutor`를 통해 이 훅을 제거하려고 시도합니다. 그러면 훅이 즉시 제거됩니다. 그러나 `emergencyUninstallTimelock`을 재설정하지 않습니다. 오래된 `emergencyUninstallTimelock`은 계약에 유지됩니다.
3. 검증자가 타임스탬프 X + 1일 + 1에 다시 설치됩니다.
4. 이 검증자를 제거하려면 최소 3일을 기다려야 합니다.

대부분의 경우 이러한 작업은 신뢰할 수 있어야 하므로 이 시나리오의 가능성은 매우 낮습니다.

```solidity
    function emergencyUninstallHook(EmergencyUninstall calldata data, bytes calldata signature) external payable {
        if (hookTimelock == 0) {
            // if the timelock hasnt been initiated, initiate it
            accountStorage.emergencyUninstallTimelock[hook] = block.timestamp;
            emit EmergencyHookUninstallRequest(hook, block.timestamp);
        } else if (block.timestamp >= hookTimelock + 3 * _EMERGENCY_TIMELOCK) {
            accountStorage.emergencyUninstallTimelock[hook] = block.timestamp;
            emit EmergencyHookUninstallRequestReset(hook, block.timestamp);
        } else if (block.timestamp >= hookTimelock + _EMERGENCY_TIMELOCK) {
            accountStorage.emergencyUninstallTimelock[hook] = 0;
            _uninstallHook(hook, hookType, deInitData);
            emit ModuleUninstalled(hookType, hook);
        }
    }
```

권장 사항: `uninstallModule`을 통해 훅을 제거할 때 `emergencyUninstallTimelock`을 지우십시오.

# [L-09] 검증자 제거 실패 가능성

스마트 계정에서 검증자는 하나의 모듈로서 설치하거나 제거할 수 있습니다. 검증자에 문제가 있으면 이 검증자를 제거해야 합니다.

`uninstallModule` 함수에서 우리는 검증자에게 알리기 위해 `onUninstall` 함수를 트리거합니다. 검증자의 예기치 않은 오류가 발생하는 경우 `excessivelySafeCall`을 사용합니다. `onUninstall` 함수가 되돌려지더라도 우리는 여전히 검증자를 신뢰할 수 없고 악의적인 것으로 간주하여 제거하려고 합니다.

여기서 문제는 모든 `gasleft()`를 검증자에게 전송한다는 것입니다. 악의적인 검증자는 모든 가스를 소모할 수 있습니다. 1/64 가스를 남겨두지만 `uninstallModule` 함수에는 하나의 `withHook` 수정자가 있다는 점에 유의해야 합니다. `excessivelySafeCall` 후 `IHook(hook).postCheck(hookData);`를 실행해야 합니다. 사후 검사가 복잡한 경우 남은 가스가 이 검사를 완료하기에 충분하지 않을 수 있습니다.

```solidity
    function uninstallModule(uint256 moduleTypeId, address module, bytes calldata deInitData) external payable onlyEntryPointOrSelf withHook {
        // If this module is not installed, we do not need to uninstall this module.
        require(_isModuleInstalled(moduleTypeId, module, deInitData), ModuleNotInstalled(moduleTypeId, module));
        if (moduleTypeId == MODULE_TYPE_VALIDATOR) {
            _uninstallValidator(module, deInitData);
        }
    }
    function _uninstallValidator(address validator, bytes calldata data) internal virtual {
        // get current validators.
        SentinelListLib.SentinelList storage validators = _getAccountStorage().validators;

        (address prev, bytes memory disableModuleData) = abi.decode(data, (address, bytes));

        validators.pop(prev, validator);
        validator.excessivelySafeCall(gasleft(), 0, 0, abi.encodeWithSelector(IModule.onUninstall.selector, disableModuleData));
    }
```

권장 사항: `postCheck` 함수가 완료하기에 충분한 가스를 갖도록 하나의 `remaining_gas` 매개변수를 추가하십시오.

# [L-10] 훅 검사가 우회될 수 있음

Nexus에는 하나의 훅 메커니즘이 있습니다. 우리는 이 훅 메커니즘을 사용하여 트랜잭션의 사전 검사 및 사후 검사를 수행하여 조건 및 상태 일관성을 보장합니다.

`execute`, `executeFromExecutor`와 같은 일부 주요 함수에서는 이 `withHook` 수정자를 추가합니다. `withHook` 수정자에서는 이 `tload/tsotre`를 사용하여 트랜잭션 데이터를 이미 확인했는지 여부를 기록합니다.

여기서 문제는 `tload/tstore`의 수명 주기가 전체 트랜잭션이라는 것입니다. 누군가가 한 트랜잭션에서 주요 함수를 두 번 트리거할 수 있는 경우 일부 트랜잭션 데이터는 훅을 통해 데이터를 확인하지 못합니다.

예를 들어, 한 실행자가 한 트랜잭션에서 `executeFromExecutor`를 두 번 트리거하면 두 번째 데이터는 데이터를 확인하지 못합니다.

```solidity
    modifier withHook() {
        address hook = _getHook();
        bool hooking;
        assembly {
            hooking := tload(HOOKING_FLAG_TRANSIENT_STORAGE_SLOT)
        }
        // If there is not any hook.
        if (hook == address(0) || hooking) {
            _;
        } else {
            assembly {
                tstore(HOOKING_FLAG_TRANSIENT_STORAGE_SLOT, 1)
            }
            bytes memory hookData = IHook(hook).preCheck(msg.sender, msg.value, msg.data);
            _;
            IHook(hook).postCheck(hookData);
        }
    }
```
```solidity
    function executeFromExecutor(
        ExecutionMode mode,
        bytes calldata executionCalldata
    )
        external
        payable
        onlyExecutorModule // Only installed executor.
        withHook
        withRegistry(msg.sender, MODULE_TYPE_EXECUTOR) // Check msg.sender is MODULE_TYPE_EXECUTOR.
        returns (bytes[] memory returnData)
    {
    	...
    }
```

권장 사항: `msg.sender`, `msg.data`를 tstore하십시오. `msg.data`가 변경되면 새 `msg.sender` 및 `msg.data`를 확인해야 합니다.

# [L-11] `NexusBoostrap`에 `preValidationHooks`를 설치할 수 없음

이것은 `NexusBoostrap`에서 누락된 기능일 수 있습니다. 다른 모든 모듈은 거기서 설치할 수 있으며 `NexusBoostrap`에 대한 `delegatecall` 바로 직후 `preValidationHook` 훅이 있으므로 사용자는 그러한 훅을 설치할 선택권이 있어야 합니다. 사용되는 곳은 다음과 같습니다.

```solidity
    function validateUserOp(
        PackedUserOperation calldata op,
        bytes32 userOpHash,
        uint256 missingAccountFunds
    ) external virtual payPrefund(missingAccountFunds) onlyEntryPoint returns (uint256 validationData) {
        address validator;
        PackedUserOperation memory userOp = op;

        if (op.nonce.isValidateMode()) {
            // do nothing special. This is introduced
            // to quickly identify the most commonly used
            // mode which is validate mode
            // and avoid checking two above conditions
        } else if (op.nonce.isModuleEnableMode()) {
            // if it is module enable mode, we need to enable the module first
            // and get the cleaned signature
            userOp.signature = _enableMode(userOpHash, op.signature);
        } else if (op.nonce.isPrepMode()) {
            // PREP Mode. Authorize prep signature
            // and initialize the account
            // PREP mode is only used for the uninited PREPs
            require(!isInitialized(), AccountAlreadyInitialized());
            bytes calldata initData;
            (userOp.signature, initData) = _handlePREP(op.signature);
 boostrap dellcall @>            _initializeAccount(initData);
        }
        validator = _handleValidator(op.nonce.getValidator());
ERC4337 preValHook @>        (userOpHash, userOp.signature) = _withPreValidationHook(userOpHash, userOp, missingAccountFunds);
        validationData = IValidator(validator).validateUserOp(userOp, userOpHash);
    }

```

`NexusBoostrap`에 그러한 옵션을 구현하십시오.

# [L-12] 사용자가 `withHook` 수정자를 우회할 수 있음

`NexusBoostrap`에서 모듈을 설치하여 우회할 수 있습니다. 이는 상태 변경 확인 작업을 우회하고 싶거나 예를 들어 돈을 얻을 때 유용할 수 있습니다. erc7579 표준에서도 권장하는 대로 `NexusBoostrap` 함수에 withHook 수정자를 구현하십시오.

# [L-13] `MODULE_ENABLE_MODE_TYPE_HASH`가 eip-712와 호환되지 않음

현재 `MODULE_ENABLE_MODE_TYPE_HASH`는 다음과 같습니다.

```solidity
// keccak256("ModuleEnableMode(address module,uint256 moduleType,bytes32 userOpHash,bytes32 initDataHash)")
bytes32 constant MODULE_ENABLE_MODE_TYPE_HASH = 0xbe844ccefa05559a48680cb7fe805b2ec58df122784191aed18f9f315c763e1b;
```
그러나 이는 `structHash`가 계산되는 방식 때문에 `ModuleManager::_enableMode` 함수에서 되돌리기를 초래할 수 있습니다.

```solidity
    function _getEnableModeDataHash(address module, uint256 moduleType, bytes32 userOpHash, bytes calldata initData) internal view returns (bytes32) {
@>        return keccak256(abi.encode(MODULE_ENABLE_MODE_TYPE_HASH, module, moduleType, userOpHash, keccak256(initData)));
    }
```
eip712에 따르면:
>정의: 동적 유형은 바이트와 문자열입니다. 이들은 유형 선언의 목적을 위한 원자 유형과 같지만 인코딩에서의 처리는 다릅니다.

인코딩 처리는 준수되지만 `typehash`의 선언은 준수되지 않아 `_enableMode` 함수에서 잘못된 `structHash`가 발생합니다.

권장 사항:

다음과 같이 `MODULE_ENABLE_MODE_TYPE_HASH`를 변경하십시오.

```solidity
bytes32 constant MODULE_ENABLE_MODE_TYPE_HASH = keccak256("ModuleEnableMode(address module,uint256 moduleType,bytes32 userOpHash,bytes initDataHash)")
```
이렇게 하면 `MODULE_ENABLE_MODE_TYPE_HASH`가 eip712를 준수하게 됩니다.

