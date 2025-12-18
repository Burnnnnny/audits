# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **galacticcouncil/hydration-node** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Hydration 정보 (About Hydration)

Hydration은 Polkadot의 DeFi 프로토콜로, 모든 자산을 하나의 고효율 거래 풀로 결합하는 'Omnipool'을 제공하여 슬리피지를 줄이고 자본 효율성을 높입니다. 단일 측면 유동성 제공, 인센티브 보상 및 고급 보안과 같은 기능을 통해 Hydration은 비영구적 손실을 최소화하고 유동성 제공자와 DAO 모두에게 더 안전하고 간소화된 거래를 보장합니다.

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

_검토 커밋 해시_ - [90eb2543cbe037626ed2c5f263f876bc5db6825a](https://github.com/galacticcouncil/hydration-node/tree/90eb2543cbe037626ed2c5f263f876bc5db6825a)

_수정 검토 커밋 해시_ - [5d4121cbe3fd852b8e8341d2358c19cf118946bc](https://github.com/galacticcouncil/hydration-node/tree/5d4121cbe3fd852b8e8341d2358c19cf118946bc)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `lib.rs`
- `erc20_currency.rs`
- `executor.rs`
- `evm.rs`
- `erc20_mapping.rs`
- `multicurrency.rs`
- `permit.rs`

# 발견 사항 (Findings)

# [H-01] `TransferFrom`이 조회(view) 함수로 잘못 처리됨 (`TransferFrom` is incorrectly treated as a view function)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`MultiCurrencyPrecompile` 모듈의 `execute` 함수에서 `check_function_modifier`는 함수 호출이 실행 컨텍스트와 호환되는지, 특히 지불 가능(payable) 함수인지 비지불 가능(non-payable) 함수인지 확인하기 위한 것입니다. 그러나 `Function::TransferFrom` 케이스가 매치(match) 문에서 누락되었습니다:

```rust
handle.check_function_modifier(match selector {
    Function::Transfer => FunctionModifier::NonPayable,
    // Function::TransferFrom is not included here
    _ => FunctionModifier::View,
})?;
```

결과적으로 `TransferFrom`은 `FunctionModifier::View`로 기본 설정되며, 이는 `TransferFrom`이 비지불 가능으로 표시되어야 하는 상태 변경 함수이기 때문에 올바르지 않습니다. 이를 조회 함수로 처리하면 실행 환경이 조회 전용 작업을 위한 컨텍스트에서 상태 변경을 제한할 수 있으므로 호출 시 예상치 못한 오류나 실패가 발생할 수 있습니다.

## 권장 사항

`Transfer` 함수와 유사하게 `Function::TransferFrom`을 `check_function_modifier` 매치 문에 포함하고 `FunctionModifier::NonPayable`을 할당하십시오:

```rust
handle.check_function_modifier(match selector {
    Function::Transfer => FunctionModifier::NonPayable,
    Function::TransferFrom => FunctionModifier::NonPayable, // Add this line
    _ => FunctionModifier::View,
})?;
```

이 조정은 `TransferFrom`이 상태 변경 함수로 올바르게 인식되도록 하여 조회 함수로 잘못 처리되는 것을 방지하고 실행 컨텍스트의 무결성을 유지합니다.

# [M-01] EVM 주소 등록 마이그레이션에서의 제한 없는 스토리지 반복 (Unbounded storage iteration in EVM address registration migration)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`SetCodeForErc20Precompile::on_runtime_upgrade()`는 EVM 주소를 등록하기 위해 레지스트리의 모든 자산에 대해 제한 없는 반복을 수행합니다. 이 접근 방식은 몇 가지 위험을 초래합니다.

- **블록 공간 고갈**: 자산 수가 많을 경우(>1000), 마이그레이션이 블록 가중치 제한을 초과하여 업그레이드가 실패할 수 있습니다.

- **네트워크 중단**: 블록 제한 초과로 인한 업그레이드 실패는 해결을 위해 네트워크 조정이 필요하므로 잠재적으로 다운타임으로 이어질 수 있습니다.

```rust
fn on_runtime_upgrade() -> frame_support::weights::Weight {
    pallet_asset_registry::Assets::<Runtime>::iter().for_each(|(asset_id, _)| {
        // ... processing each asset in a single block
    });
}
```

참조: [링크](https://docs.substrate.io/reference/how-to-guides/parachains/upgrade-a-parachain/)

## 권장 사항

- Scheduler 팔렛을 사용하여 예약된 다중 블록 마이그레이션을 구현하십시오:
  ```rust
  - Define migration state storage
  - Process assets in configurable batches (e.g., 100 per block)
  - Use the Scheduler pallet to ensure consistent execution
  - Track progress for migration resumption
  ```

# [M-02] `handle_result()`에서 ERC20 함수 반환 값 확인 실패 (Failure to verify ERC20 function return values in `handle_result()`)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`Erc20Currency` 구현의 `handle_result` 함수는 `transfer` 및 `approve`와 같은 ERC20 계약 호출 결과를 처리하는 역할을 합니다. 현재 이 함수는 `exit_reason`만 확인하여 EVM 호출이 성공했는지 확인합니다:

```rust
fn handle_result(result: CallResult) -> DispatchResult {
    let (exit_reason, value) = result;
    match exit_reason {
        ExitReason::Succeed(ExitSucceed::Returned) => Ok(()),
        ExitReason::Succeed(ExitSucceed::Stopped) => Ok(()),
        _ => Err(DispatchError::Other(&*Box::leak(
            format!("evm:0x{}", hex::encode(value)).into_boxed_str(),
        ))),
    }
}
```

그러나 일부 ERC20 토큰은 작업의 성공(`true`) 또는 실패(`false`)를 나타내는 부울 값을 반환합니다. 반환된 데이터(`value`)를 확인하지 않음으로써 함수는 실제로 실패했음에도 불구하고 작업이 성공했다고 잘못 가정할 수 있습니다. 이러한 간과로 인해 ERC20 계약이 반환 값을 통해 실패를 알렸음에도 불구하고 시스템에서 전송이나 승인이 성공한 것으로 간주되는 상황이 발생할 수 있습니다.

## 권장 사항

길이가 0이 아닐 때 반환된 데이터를 확인하도록 `handle_result` 함수를 수정하십시오. 데이터가 부울 값을 나타내는 경우 디코딩하여 `true`인지 확인하십시오. 디코딩된 부울이 `false`이면 함수는 작업 결과의 오해를 방지하기 위해 트랜잭션을 되돌려야(revert) 합니다.

# [M-03] EVM 오류 메시지 할당을 통한 제한 없는 메모리 증가 (Unbounded memory growth via EVM Error message allocations)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`executor.rs`에서 EVM 오류 처리 코드는 `Box::leak`를 사용하여 각각의 고유한 오류 값에 대해 영구적인 메모리 할당을 생성합니다. 이는 참조를 삭제하여 발생하는 기존 메모리 누수는 아니지만 제한 없는 메모리 증가를 통해 보안 위험을 나타냅니다:

```rust
fn handle_result(result: CallResult) -> DispatchResult {
    let (exit_reason, value) = result;
    match exit_reason {
        ExitReason::Succeed(ExitSucceed::Returned) => Ok(()),
        ExitReason::Succeed(ExitSucceed::Stopped) => Ok(()),
        _ => Err(DispatchError::Other(&*Box::leak(
            format!("evm:0x{}", hex::encode(value)).into_boxed_str(),
        ))),
    }
}
```

각각의 고유한 오류 값은 다음과 같은 이유로 새로운 영구 메모리 할당을 생성합니다:

- 오류 값이 16진수로 인코딩됨 (`hex::encode(value)`)
- 각 고유 값에 대해 새 문자열 생성
- `Box::leak`는 이 할당을 영구적으로 만듦
- 런타임 중에 메모리를 해제할 수 없음

공격자는 다음을 통해 이를 악용할 수 있습니다:

- EVM 오류를 유발하는 트랜잭션 생성
- 각 오류에 고유한 데이터가 있는지 확인 (예: 전송이 호출될 때마다 카운터++ 오류를 반환하는 ERC20 impl)
- 각 고유 오류는 추가 메모리를 소비함
- 고유 오류에 따라 메모리 사용량이 선형적으로 증가함

공격 패턴 예:

```rust
// Each creates a permanent allocation
tx1 -> error [1,2,3] -> "evm:0x010203"
tx2 -> error [1,2,4] -> "evm:0x010204"
tx3 -> error [1,2,5] -> "evm:0x010205"
// Memory grows with each unique error
```

심각도는 다음과 같은 이유로 치명적입니다:

- 메모리 증가가 제한 없음
- 할당은 노드의 수명 동안 영구적임
- 모든 네트워크 노드에 영향을 미침
- DoS 벡터로 사용될 수 있음
- 노드 재시작 없이는 메모리를 회수할 수 없음

## 권장 사항

- 동적 데이터가 없는 정적 오류 메시지 사용 (권장):

```rust
const EVM_ERROR: &'static str = "EVM execution error";

fn handle_result(result: CallResult) -> DispatchResult {
    let (exit_reason, value) = result;
    match exit_reason {
        ExitReason::Succeed(ExitSucceed::Returned) => Ok(()),
        ExitReason::Succeed(ExitSucceed::Stopped) => Ok(()),
        _ => {
            // Log error details separately if needed
            log::error!("EVM error: 0x{}", hex::encode(&value));
            Err(DispatchError::Other(EVM_ERROR))
        }
    }
}
```

- 제한된 오류 캐시 구현 (예: BTreeMap).

옵션 1이 권장되는 방식이며 그 이유는 다음과 같습니다:

- 메모리 증가 벡터를 완전히 제거
- 오류 로깅 기능 유지
- 구현 및 유지 관리가 간단함
- 성능 오버헤드가 없음

# [L-01] Permit 검증에서의 EIP-2 서명 가변성 (EIP-2 signature malleability in Permit validation)

## 설명

permit.rs의 `validate_permit` 함수는 ECDSA 서명의 `s` 값이 곡선 차수의 하반부에 있는지 확인하지 않아 EIP-2 요구 사항을 시행하지 못합니다. 이는 동일한 메시지에 대해 여러 유효한 서명이 존재할 수 있는 서명 가변성을 허용합니다. 즉각적인 보안 위험을 초래하지는 않지만 이더리움 표준 및 모범 사례에서 벗어납니다.

참조:
https://eips.ethereum.org/EIPS/eip-2

## 권장 사항

`s` 값이 곡선 차수의 하반부에 있는지 확인하는 유효성 검사를 추가하십시오.

# [L-02] 런타임 업그레이드 구현의 로깅 누락 (Missing logging in runtime upgrade implementation)

`SetCodeForErc20Precompile::on_runtime_upgrade`에는 업그레이드 프로세스의 진행 상황과 결과를 추적하는 로깅 메커니즘이 없습니다. 이로 인해 다음과 같은 어려움이 발생합니다:

- 프로덕션에서 업그레이드 진행 상황 모니터링
- 업그레이드가 실패하거나 예기치 않게 작동하는 경우 문제 디버깅
- 업그레이드 완료 후 변경 사항 감사
- 모든 자산이 올바르게 처리되었는지 확인

업그레이드 프로세스 전반에 걸쳐 구조화된 로깅을 추가하는 것을 고려하십시오.

```rust
impl frame_support::traits::OnRuntimeUpgrade for SetCodeForErc20Precompile {
    fn on_runtime_upgrade() -> frame_support::weights::Weight {
        log::info!("Starting ERC20 precompile code setup");

        // Track statistics
        let mut assets_processed = 0;
        let mut assets_updated = 0;

        // Log individual updates
        pallet_asset_registry::Assets::<Runtime>::iter().for_each(|(asset_id, _)| {
            assets_processed += 1;
            // Log progress...
        });

        // Log final results
        log::info!(
            "Completed setup. Processed: {}, Updated: {}",
            assets_processed,
            assets_updated
        );

        // Return weight...
    }
}
```

그리고 업그레이드 전/후 로깅(및 필요한 경우 유효성 검사)을 추가하는 것을 고려하십시오. 예:

```rust
#[cfg(feature = "try-runtime")]
fn pre_upgrade() -> Result<Vec<u8>, &'static str> {
    log::info!("Starting pre_upgrade checks");
    Ok(Vec::new())
}
```

# [L-03] `handle_result()`에서의 EVM 종료 상태 오분류 (EVM exit status misclassification in `handle_result()`)

`handle_result` 함수는 `ExitSucceed::Returned` 및 `ExitSucceed::Stopped`만 성공적인 결과로 간주하고 다른 모든 종료 이유는 오류로 처리합니다:

```rust
fn handle_result(result: CallResult) -> DispatchResult {
    let (exit_reason, value) = result;
    match exit_reason {
        ExitReason::Succeed(ExitSucceed::Returned) => Ok(()),
        ExitReason::Succeed(ExitSucceed::Stopped) => Ok(()),
        _ => Err(DispatchError::Other(&*Box::leak(
            format!("evm:0x{}", hex::encode(value)).into_boxed_str(),
        ))),
    }
}
```

이 구현은 EVM 작업에서 유효하고 의도적인 결과임에도 불구하고 `ExitSucceed::Suicided`를 오류 조건으로 잘못 처리합니다. 스마트 계약이 자체 파괴 작업(SELFDESTRUCT opcode)을 실행하면 성공적인 계약 파괴를 나타내기 위해 `ExitSucceed::Suicided`를 반환합니다.

결과적으로:

- 유효한 계약 자체 파괴 작업은 실패한 트랜잭션으로 표시됩니다.
- 이는 자체 파괴가 유효한 작업인 표준 EVM 동작과 다릅니다.

SputnikVM의 Suicide 코드:

```rust
pub fn suicide<S: AsRef<RuntimeState>, H: RuntimeEnvironment + RuntimeBackend, Tr>(
	machine: &mut Machine<S>,
	handler: &mut H,
) -> Control<Tr> {
	.
	match machine.stack.perform_pop1_push0(|target| {
	.
	.
		Ok(((), ()))
	}) {
		Ok(()) => Control::Exit(ExitSucceed::Suicided.into()),
		Err(e) => Control::Exit(Err(e)),
	}
}
```

[interpreter/src/eval/system.rs#L370](https://github.com/rust-ethereum/evm/blob/master/interpreter/src/eval/system.rs#L370)

SputnikVM은 Hydration에서 활용되는 문빔 재단(moonbeam-foundation)의 프론티어(폴카닷 EVM/프론티어의 포크)에서 사용하는 EVM 엔진입니다.

```sh
[[package]]
name = "pallet-evm"
version = "6.0.0-dev"
source = "git+https://github.com/moonbeam-foundation/frontier?branch=moonbeam-polkadot-v1.11.0#45d16ae4c8b32e26fdab8353b704fd59ea0ddb8c"
```

[hydration-node/blob/evm-binding/Cargo.lock#L8237](https://github.com/galacticcouncil/hydration-node/blob/evm-binding/Cargo.lock#L8237)

참고: 현재 `handle_result`의 사용이 ERC20 작업으로 제한되어 있으므로 자체 파괴 opcode의 가능성은 낮습니다. 그러나 여전히 표준 EVM 동작을 따르지 않습니다.

모든 ExitSucceed 변형을 성공적인 작업으로 적절하게 처리하도록 handle_result 함수를 수정해야 합니다:

```rust
fn handle_result(result: CallResult) -> DispatchResult {
    let (exit_reason, value) = result;
    match exit_reason {
        ExitReason::Succeed(_) => Ok(()), // Accept all ExitSucceed variants
        _ => Err(DispatchError::Other(&*Box::leak(
            format!("evm:0x{}", hex::encode(value)).into_boxed_str(),
        ))),
    }
}
```

# [L-04] 전체 값 인코딩으로 인한 대형 EVM 오류 메시지 (Oversized EVM error messages due to full value encoding)

`erc20_currency.rs`의 `handle_result` 함수는 16진수 인코딩을 사용하여 전체 반환 값을 오류 메시지로 인코딩하여 EVM 호출에 대한 오류 처리를 구현합니다.

```rust
fn handle_result(result: CallResult) -> DispatchResult {
    let (exit_reason, value) = result;
    match exit_reason {
        ExitReason::Succeed(ExitSucceed::Returned) => Ok(()),
        ExitReason::Succeed(ExitSucceed::Stopped) => Ok(()),
        _ => Err(DispatchError::Other(&*Box::leak(
            format!("evm:0x{}", hex::encode(value)).into_boxed_str(),
        ))),
    }
}
```

이 함수는 실패한 모든 EVM 작업에 대해 전체 `value` 매개변수를 오류 메시지로 무조건 인코딩합니다. 이 디자인은 큰 반환 값을 생성할 수 있는 EVM 작업을 처리할 때 문제가 됩니다.

Polkadot의 EVM 호환성 계층을 감안할 때 이는 몇 가지 위험을 초래합니다:

- 큰 오류 메시지는 문자열 서식 지정 및 16진수 인코딩을 위해 상당한 메모리 할당이 필요합니다.
- 이러한 오류가 온체인에 기록되거나 네트워크 기록에 저장되면 불필요한 스토리지 팽창이 발생합니다.

오류 정보를 제한합니다. 예를 들어 적절한 크기로 자르거나 특정 크기 제한을 초과하는 경우 해시를 반환합니다.

# [L-05] `call()`의 확인되지 않은 메시지 크기 (Unchecked message size in `call()`)

실행자의 `call` 함수에서 아래 스니펫과 같이 크기 유효성 검사 없이 입력 데이터가 허용됩니다:

```rust
fn call(context: CallContext, data: Vec<u8>, value: U256, gas: u64) -> CallResult {
    Self::execute(context.origin, gas, |executor| {
        executor.transact_call(context.sender, context.contract, value, data, gas, vec![])
    })
}
```

이 함수는 계약에 전달되는 메시지를 나타내는 `data` 벡터를 처리합니다. 그러나 입력 데이터에 대한 크기 유효성 검사가 없으므로 매우 큰 메시지가 제한 없이 전송될 수 있습니다.

크고 확인되지 않은 메시지를 처리하도록 허용하면 과도한 가스 소비가 발생하여 잠재적으로 가스 자원이 고갈되고 실행이 중단될 수 있습니다. 이 취약점은 시스템이 큰 메시지를 처리하도록 강요되어 의도하지 않은 동작이 발생하거나 가스 부족으로 인해 중단되는 서비스 거부(DoS) 공격으로 이어질 수 있습니다.

메시지를 실행하기 전에 입력 데이터의 크기를 허용 가능한 최대 제한에 대해 검증하여 지나치게 큰 메시지가 처리되는 것을 방지하십시오. 예를 들어, 크기가 사전 정의된 최대값을 초과하는 메시지를 거부하기 위한 확인을 추가하여 가스 자원이 불필요하게 소비되지 않도록 하십시오. 예:

```rust
fn call(context: CallContext, data: Vec<u8>, value: U256, gas: u64) -> CallResult {
    let max_size: usize = 1024; // Set an appropriate maximum size limit
    if data.len() > max_size {
        return Err("Data size exceeds maximum allowed limit");
    }

    Self::execute(context.origin, gas, |executor| {
        executor.transact_call(context.sender, context.contract, value, data, gas, vec![])
    })
}
```

이렇게 하면 큰 메시지로 인한 가스 고갈을 방지하고 잠재적인 DoS 공격으로부터 시스템을 보호할 수 있습니다.
