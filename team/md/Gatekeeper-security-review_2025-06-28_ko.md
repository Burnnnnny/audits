# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**saguarocrypto/gatekeeper** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Gatekeeper 소개

Gatekeeper는 효율적인 비트맵 구조를 사용하여 샌드위치(sandwich) 거래 촉진 작업에 허용되는 슬롯을 제어함으로써 검증인(validator)의 슬롯 게이팅(gating)을 관리하는 솔라나(Solana) 프로그램입니다.
이 프로그램은 멀티시그(multisig) 권한으로 보호되는 에포크(epoch) 별 게이팅 구성을 생성, 읽기, 업데이트 및 삭제(CRUD) 할 수 있는 전체 인터페이스를 제공하여 정밀하고 확장 가능한 슬롯 수준 제어를 가능하게 합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간 - 프로토콜 자산의 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터일 뿐이지만 여전히 발생 가능성이 상대적으로 높습니다.

- 낮음 - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 또는 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정해야 함 (Should fix)

- 낮음 - 수정할 수 있음 (Could fix)

# 보안 평가 요약

_검토 커밋 해시_ - [c009d361238a121e7d4543c2550d75f085a84800](https://github.com/saguarocrypto/gatekeeper/tree/c009d361238a121e7d4543c2550d75f085a84800)

_수정 검토 커밋 해시_ - [74998cb761a6d709171b69c5d2028f7c3745fbe5](https://github.com/saguarocrypto/gatekeeper/tree/74998cb761a6d709171b69c5d2028f7c3745fbe5)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `lib`
- `constants`
- `append_data_sandwich_validators_bitmap`
- `clear_data_sandwich_validators_bitmap`
- `close_sandwich_validator`
- `expand_sandwich_validators_bitmap`
- `mod`
- `modify_sandwich_validators`
- `set_sandwich_validators`
- `update_sandwich_validator`
- `validate_sandwich_validators`

# 발견 사항

# [C-01] `append_data_sandwich_validators_bitmap`의 치명적인 PDA 검증 결함

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`append_data_sandwich_validators_bitmap` 명령어는 `sandwich_validators` PDA(Program Derived Address) 계정을 **올바르게 검증하지 않습니다**. 구체적으로, 이 명령어에 사용된 `AppendDataSandwichValidatorsBitmap` 컨텍스트는 시드(seeds)에서 PDA 계정을 파생하지 않습니다.

이 결함으로 인해 한 권한이 **다른 권한의 PDA 검증인 비트맵에 데이터를 추가**할 수 있게 되어 **승인되지 않은 상태 수정**이 발생할 수 있습니다.

단순한 검증 간과와 달리, 이 문제는 권한 간의 격리를 깨고 **권한 간 데이터 손상 또는 조작**으로 이어질 수 있으며 16번째 바이트부터 해당 PDA 계정의 데이터를 다시 쓰게 만들므로 **매우 중요한 보안 위험**을 제기합니다. 이는 치명적인 버그로 간주됩니다.

## 권장 사항

명령어에 전달된 계정을 사용하는 대신 `AppendDataSandwichValidatorsBitmap` 내부에서 PDA 계정을 파생하십시오:

```rust
pub struct AppendDataSandwichValidatorsBitmap<'info> {
@>   #[account(mut,
        seeds = [SandwichValidators::SEED_PREFIX, multisig_authority.key().as_ref(), &epoch_arg.to_le_bytes()],
        bump
    )]
    pub sandwich_validators: AccountLoader<'info, SandwichValidators>,
    #[account(mut)]
    pub multisig_authority: Signer<'info>,
}
```

# [C-02] `handler`의 부적절한 PDA 검증으로 인한 임의 데이터 삭제 가능

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`clear_data_sandwich_validators_bitmap` 명령어의 핸들러는 `sandwich_validators` PDA 계정을 **제대로 검증하지 않습니다**. 이 간과로 인해 악성 서명자는 **다른 서명자나 권한에 속한 다른 PDA 계정을 제공**하고 여전히 확인을 통과할 수 있습니다.

결과적으로 핸들러는 **다른 권한에 해당하는 유효한 데이터를 잠재적으로 지워버림**으로써 다른 PDA에 속한 **비트맵 데이터를 잘못 지울 수 있습니다**.

이는 엄격한 PDA 파생 및 검증으로 보호되어야 하는 계정 데이터의 무단 수정을 가능하게 하므로 **매우 중요한 위험**을 제기합니다.

## 권장 사항

아래와 같이 `ClearDataSandwichValidatorsBitmap` 구조체 자체에서 PDA 계정을 파생하십시오:

```rust
pub struct ClearDataSandwichValidatorsBitmap<'info> {

@>  #[account(
        mut,
        seeds = [SandwichValidators::SEED_PREFIX, multisig_authority.key().as_ref(), &epoch_arg.to_le_bytes()],
        bump
    )]
    pub sandwich_validators: AccountLoader<'info, SandwichValidators>,
    #[account(mut)]
    pub multisig_authority: Signer<'info>,
}
```

# [M-01] `expand_sandwich_validators_bitmap` 핸들러의 부적절한 PDA 검증

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`expand_sandwich_validators_bitmap` 명령어는 `sandwich_validators` PDA 계정을 **올바르게 검증하지 않습니다**. 특히 `ExpandSandwichValidatorsBitmap` 구조체는 **올바른 권한을 사용하여 PDA를 파생**하지 못하므로 다른 권한에 해당하는 PDA 계정으로 이 명령어를 실행하게 됩니다.

이 문제는 **즉각적인 악의적 위험을 제기하지는 않지만**, 프로그램의 계정 검증에 **논리적 불일치**를 반영합니다. 이는 **예상치 못한 동작**으로 이어질 수 있습니다.

## 권장 사항

아래와 같이 `ExpandSandwichValidatorsBitmap` 구조체 내부에서 PDA 계정을 파생하십시오:

```rust
pub struct ExpandSandwichValidatorsBitmap<'info> {
@>   #[account(mut,
        close = multisig_authority,
        seeds = [SandwichValidators::SEED_PREFIX, multisig_authority.key().as_ref(), &epoch_to_close.to_le_bytes()],
        bump
    )]
    pub sandwich_validators: AccountInfo<'info>,
    #[account(mut)]
    pub multisig_authority: Signer<'info>,
    pub system_program: Program<'info, System>,
}
```

# [L-01] 100개가 아닌 최대 200개의 슬롯이 수정될 수 있음

사양과 주석은 각 트랜잭션에 대해 최대 100개의 슬롯을 수정할 수 있다고 정의합니다. 그러나 이는 게이트(gate) 및 언게이트(ungate) 작업별로 시행되므로 실제 양은 최대 200개가 될 수 있습니다.

```rust
    if slots_to_gate.len() > MAX_SLOTS_PER_TRANSACTION {
        return err!(GatekeeperError::TooManySlots);
    }

    if slots_to_ungate.len() > MAX_SLOTS_PER_TRANSACTION {
        return err!(GatekeeperError::TooManySlots);
    }
```

`slots_to_gate.len() + slots_to_ungate.len() > MAX_SLOTS_PER_TRANSACTION`인지 확인하는 것을 고려하십시오.

# [L-02] `modify_sandwich_validators`의 충돌하는 슬롯

`modify_sandwich_validators` 명령어를 호출할 때 동일한 슬롯이 `slots_to_gate`와 `slots_to_ungate` 모두에 나타날 수 있어 조용히 잘못된 구성을 초래할 수 있습니다.

**권장 사항** - 두 목록이 교차하는 트랜잭션을 거부하거나(예: 새로운 `ConflictSlots` 오류) 중복을 자동으로 no-op(무작동)으로 처리하십시오.

# [L-03] 점진적 비트맵 확장 중 임대(Rent) 초과 자금 조달

`expand_sandwich_validators_bitmap` 명령어는 각 호출이 계정을 `MAX_REALLOC_SIZE` (10 KiB)만큼만 증가시킬 수 있음에도 불구하고 최종 `TARGET_ACCOUNT_SIZE`에 대해 계정을 임대 면제(rent-exempt)로 만들기에 충분한 램포트(lamports)를 전송합니다. 확장이 중간에 중단되면 초과 램포트는 계정에 잠긴 상태로 남습니다.

**권장 사항** - 이 명령어에서 실제로 도달한 다음 크기(`current_size + expansion_size`)에 대한 `minimum_balance`를 계산하고 델타(차이)만 전송하십시오. 나머지 충전은 마지막 청크가 계정을 `TARGET_ACCOUNT_SIZE`로 가져올 때까지 미루십시오.

# [L-04] Append 명령어가 추가하는 대신 덮어씀

`append_data_sandwich_validators_bitmap` 함수는 "추가(append)" 유틸리티로 문서화되어 있습니다.

```rust
/// Append data to the sandwich validators bitmap account.
------------
/// Handler for appending data to a sandwich validators bitmap account.
------------
msg!("Appending {} bytes of data to large bitmap", data.len());
------------
msg!("Successfully appended {} bytes to large bitmap", max_write);
```

그러나 그 구현은 실제로 오프셋 0에서 시작하여 제공된 데이터로 비트맵의 시작 부분을 **덮어씁니다(overwrites)**. 진정한 추가(append)를 지원하지 않습니다.

```rust
// Write data to bitmap data section
let max_write = data.len().min(bitmap_data.len());
bitmap_data[..max_write].copy_from_slice(&data[..max_write]);
```

사용자나 통합자가 이 함수가 데이터를 추가할 것으로 예상하는 경우 실수로 기존 게이팅 상태를 덮어써서 보호 손실이나 불필요한 게이팅으로 이어질 수 있습니다.

**권장 사항**

사양을 다시 작성하거나 덮어쓰는 대신 추가(append)를 구현하는 것을 고려하십시오.
