# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**moleculeprotocol/pump-science-contract** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Pump Science 정보

Pump Science는 본딩 커브, 동적 수수료 구조 및 자동화된 유동성 관리를 통해 토큰 출시를 용이하게 하는 Solana 기반 프레임워크입니다. 이는 구매에 따라 토큰 및 SOL 준비금이 동적으로 조정되는 상수 곱(constant product) 본딩 커브를 사용하여 작동합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 희박하거나, 인센티브가 거의 또는 전혀 없이 공격자가 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정하는 것이 좋음

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [54daf1b93cf6abf955c69f043f73b4df671f97f7](https://github.com/moleculeprotocol/pump-science-contract/tree/54daf1b93cf6abf955c69f043f73b4df671f97f7)

_수정 검토 커밋 해시_ - [e730b6b1a4852d35d05ca3019e42e29b93c9c3d1](https://github.com/moleculeprotocol/pump-science-contract/tree/e730b6b1a4852d35d05ca3019e42e29b93c9c3d1)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `util.rs`
- `lib.rs`
- `events.rs`
- `errors.rs`
- `constants.rs`
- `whitelist.rs`
- `mod.rs`
- `meteora.rs`
- `global.rs`
- `structs.rs`
- `mod.rs`
- `locker.rs`
- `curve.rs`
- `create_pool.rs`
- `lock_pool.rs`
- `create_bonding_curve.rs`
- `mod.rs`
- `swap.rs`
- `add_wl.rs`
- `initialize.rs`
- `remove_wl.rs`
- `set_params.rs`

# 발견 사항

# [C-01] 락 풀(Lock pool)이 DoS될 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

lock_pool 인스트럭션에서 LP 토큰에 대한 연관 토큰 계정(ATA) 생성은 계정이 존재하는지 확인하기 위해 램포트 잔액을 확인하는 것에 의존합니다. `ctx.accounts.escrow_vault.get_lamports() == 0` 확인은 ATA 생성 여부를 결정하는 데 사용됩니다.

```rust
if ctx.accounts.escrow_vault.get_lamports() == 0 {
    associated_token::create(CpiContext::new(
        ctx.accounts.associated_token_program.to_account_info(),
        associated_token::Create {
            payer: ctx.accounts.payer.to_account_info(),
            associated_token: ctx.accounts.escrow_vault.to_account_info(),
            authority: ctx.accounts.lock_escrow.to_account_info(),
            mint: ctx.accounts.lp_mint.to_account_info(),
            token_program: ctx.accounts.token_program.to_account_info(),
            system_program: ctx.accounts.system_program.to_account_info(),
        },
    ))?;
}
```

[programs/pump-science/src/instructions/migration/lock_pool.rs#L157](https://github.com/moleculeprotocol/pump-science-contract/blob/main/programs/pump-science/src/instructions/migration/lock_pool.rs#L157)

이것이 문제가 되는 이유는 다음과 같습니다.

- 공격자는 사전에 escrow_vault 주소로 SOL을 전송하여 ATA 생성을 방지할 수 있습니다.
- 주소에 SOL이 있다고 해서 적절하게 초기화된 토큰 계정이 보장되는 것은 아닙니다.
- 적절하게 초기화된 ATA가 없으면 LP 토큰을 받을 유효한 토큰 계정이 없으므로 잠금 메커니즘이 실패합니다.

결과적으로 락 풀은 DoS 상태가 됩니다.

## 권장 사항

현재 구현을 램포트 잔액에 관계없이 ATA 생성을 안전하게 처리하는 `create_idempotent`로 대체하십시오.

# [H-01] 본딩 커브 에스크로로의 직접적인 SOL 전송이 프로토콜 불변성을 깰 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

본딩 커브 프로토콜은 `real_sol_reserves` 상태 변수가 `bonding_curve_sol_escrow` 계정의 SOL 잔액(램포트)과 정확히 일치해야 한다는 불변성을 유지합니다. 이 불변성은 모든 스왑 작업이 끝날 때 확인됩니다.

```rust
if sol_escrow_lamports != bonding_curve.real_sol_reserves {
    msg!(
        "real_sol_r:{}, bonding_lamps:{}",
        bonding_curve.real_sol_reserves,
        sol_escrow_lamports
    );
    msg!("Invariant failed: real_sol_reserves != bonding_curve_pool_lamports");
    return Err(ContractError::BondingCurveInvariant.into());
}
```

그러나 `real_sol_reserves`는 스왑 작업 중에만 업데이트되는 반면, 에스크로 계정의 SOL 잔액은 직접 전송을 통해 외부적으로 수정될 수 있습니다. 에스크로로의 외부 SOL 전송은 `real_sol_reserves`를 업데이트하지 않고 `sol_escrow_lamports`를 증가시켜 불변성 확인이 실패하게 만들고 수정될 때까지 프로토콜을 사용할 수 없게 만듭니다.

참고: 유사한 문제인 "DOS Attack Vector on Bonding Curve Creation Through Escrow Pre-funding"을 확인하십시오.

## 권장 사항

필요한 경우 `real_sol_reserves`를 실제 램포트 잔액과 동기화할 수 있는 정리 함수를 추가하십시오.

# [H-02] 에스크로 사전 자금 조달을 통한 본딩 커브 DOS

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

공격자는 본딩 커브가 생성되기 전에 sol_escrow 계정에 SOL을 선제적으로 자금을 조달할 수 있습니다. `create_bonding_curve` 인스트럭션은 `real_sol_reserves`를 0으로 초기화하지만, 불변성 확인은 실제 SOL 잔액이 이 값과 일치하는지 확인하므로 에스크로 계정에 어떤 SOL이라도 있으면 커브 생성이 실패하게 됩니다.

이것이 가능한 이유는 다음과 같습니다.

- sol_escrow PDA 주소는 결정론적이며 민트 주소를 아는 사람이라면 누구나 계산할 수 있습니다.
- 누구나 커브가 생성되기 전에 이 주소로 SOL을 보낼 수 있습니다.
- 불변성은 `sol_escrow_lamports == real_sol_reserves`를 엄격하게 강제합니다.

코드 스니펫:

```rust
// In create_bonding_curve.rs
pub fn handler(ctx: Context<CreateBondingCurve>, params: CreateBondingCurveParams) -> Result<()> {
    // real_sol_reserves initialized to 0
    ctx.accounts.bonding_curve.update_from_params(...);

    // Invariant check will fail if escrow has SOL
    BondingCurve::invariant(locker)?;
}

// In curve.rs
pub fn invariant<'info>(ctx: &mut BondingCurveLockerCtx<'info>) -> Result<()> {
    if sol_escrow_lamports != bonding_curve.real_sol_reserves {
        return Err(ContractError::BondingCurveInvariant.into());
    }
}
```

## 권장 사항

생성 중에 기존 SOL 잔액과 일치하도록 `real_sol_reserves`를 초기화하십시오.

바람직하지 않다면 커브 생성 중에 에스크로에 있는 기존 SOL을 생성자에게 전송하는 SOL 스윕(sweep) 메커니즘을 추가하십시오. 예를 들어:

- 에스크로에 기존 SOL이 있는지 확인
- 발견되면 서명된 CPI를 사용하여 생성자/관리자에게 스윕
- 그런 다음 일반적인 커브 생성으로 계속

# [H-03] 사용되지 않는 동결 권한 취소가 토큰 잠금 위험을 초래함

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

코드베이스에는 마이그레이션 후 토큰이 동결되는 것을 방지하는 데 필요함에도 불구하고 풀 생성 중에 호출되지 않는 `revoke_freeze_authority` 함수가 `locker.rs`에 포함되어 있습니다.

동결 권한이 취소되지 않은 경우:

- 본딩 커브 프로그램은 토큰 계정을 동결할 수 있는 능력을 유지합니다.
- 이는 마이그레이션 후 잠재적으로 사용자 토큰을 잠그는 데 사용될 수 있습니다.
- 사전 거래 중에만 잠금이 가능해야 한다는 의도된 설계에 위배됩니다.

**코드 위치 :** [locker.rs#L83](https://github.com/moleculeprotocol/pump-science-contract/blob/54daf1b93cf6abf955c69f043f73b4df671f97f7/programs/pump-science/src/state/bonding_curve/locker.rs#L83)

## 권장 사항

동결 권한 취소 호출을 추가하는 것이 좋습니다.

# [M-01] 시작 슬롯 검증이 과거 슬롯을 허용함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

프로토콜은 출시 후 경과된 슬롯에 따라 높은 수수료(99%)에서 낮은 수수료(1%)로 진행되어야 하는 수수료 일정을 구현합니다. 그러나 현재 구현은 `start_slot` 매개변수를 잘못 검증합니다.

```rust
pub fn validate(&self, params: &CreateBondingCurveParams) -> Result<()> {
    let clock = Clock::get()?;
    // validate start time
    if let Some(start_slot) = params.start_slot {
        require!(start_slot <= clock.slot, ContractError::InvalidStartTime)
    }
    // ...
}
```

이 검증은 start_slot이 미래가 아님(`start_slot <= clock.slot`)만 확인하므로, 작성자는 시작 슬롯을 먼 과거로 설정할 수 있습니다. `slots_passed = current_slot - start_slot`이 즉시 나중의 수수료 단계에서 시작되므로 의도한 수수료 진행이 깨집니다.

예를 들어:
현재 슬롯은 1000입니다.

- 작성자가 start_slot을 800으로 설정
  첫 거래는 슬롯 1000에서 발생, slots_passed = 1000 - 800 = 200
- 이는 거래가 1단계(99% 수수료) 대신 2단계(~5% 수수료)에서 시작됨을 의미합니다.

## 권장 사항

시작 슬롯 검증은 현재 슬롯의 작은 범위 내에서만 시작 슬롯을 허용하도록 업데이트되어야 하며, 수수료 일정이 의도한 대로 1단계부터 시작되도록 해야 합니다.

# [M-02] CreateBondingCurve의 DOS

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`CreateBondingCurve` 인스트럭션에는 `bonding_curve_token_account` 전달이 필요합니다.

```rust
    #[account(
        init,
        payer = creator,
        associated_token::mint = mint,
        associated_token::authority = bonding_curve,
    )]
    bonding_curve_token_account: Box<Account<'info, TokenAccount>>,
```

`bonding_curve_token_account`는 `associated_token::mint` 및 `associated_token::authority`를 사용하므로 `TokenAccount`를 미리 생성할 수 있습니다.
이것이 이미 존재하면 `init`이 사용되므로 함수 생성이 실패합니다.

공격자는 계산된 `bonding_curve` 주소를 추출하고 `TokenAccount`를 생성하여 사용자가 본딩 커브를 생성하지 못하게 할 수 있습니다.

## 권장 사항

````diff
    #[account(
-        init,
+        init_if_needed,
        payer = creator,
        associated_token::mint = mint,
        associated_token::authority = bonding_curve,
    )]
    bonding_curve_token_account: Box<Account<'info, TokenAccount>>,```

# [L-01] 불충분한 SOL 잔액 확인이 임대 면제를 무시함

스왑 함수의 구매 토큰 검증에서 코드는 임대 면제를 고려하지 않고 사용자의 총 SOL 잔액이 스왑 금액에 충분한지 여부만 확인합니다.

```rust
require!(
    ctx.accounts.user.get_lamports() >= exact_in_amount,
    ContractError::InsufficientUserSOL,
);
````

[programs/pump-science/src/instructions/curve/swap.rs#L177](https://github.com/moleculeprotocol/pump-science-contract/blob/main/programs/pump-science/src/instructions/curve/swap.rs#L177)

이 확인은 임대 면제 금액을 포함한 총 잔액을 반환하는 `get_lamports()`를 사용합니다. 계정이 임대 면제 미만으로 고갈되더라도 트랜잭션이 성공하여 계정이 다음과 같이 됩니다.

- 비 임대 면제 상태 진입
- Solana 런타임에 의해 시간이 지남에 따라 임대료가 부과되고, 잔액이 완전히 고갈되면 결국 퍼지(purged)됩니다.

임대 면제를 설명하는 보다 포괄적인 잔액 확인을 추가하십시오.

```rust
let rent = Rent::get()?;
let min_rent = rent.minimum_balance(0); // 0 for data size since this is just a native SOL account
require!(
    ctx.accounts.user.get_lamports() >= exact_in_amount.checked_add(min_rent).unwrap(),
    ContractError::InsufficientUserSOL,
);
```

# [L-02] 전역 매개변수 업데이트의 유효성 검사 불충분

`set_params` 함수는 적절한 유효성 검사 없이 프로토콜 매개변수를 업데이트할 수 있도록 허용합니다.

```rust
pub fn set_params(ctx: Context<SetParams>, params: GlobalSettingsInput) -> Result<()> {
    SetParams::handler(ctx, params)
}
```

`GlobalSettingsInput` 구조체를 보면:

```rust
pub struct GlobalSettingsInput {
    pub initial_virtual_token_reserves: Option<u64>,
    pub initial_virtual_sol_reserves: Option<u64>,
    pub initial_real_token_reserves: Option<u64>,
    pub token_total_supply: Option<u64>,
    pub mint_decimals: Option<u8>,
    pub migrate_fee_amount: Option<u64>,
    pub migration_token_allocation: Option<u64>,
    pub fee_receiver: Option<Pubkey>,
    pub whitelist_enabled: Option<bool>,
    pub meteora_config: Option<Pubkey>,
}
```

포괄적인 매개변수 유효성 검사 추가를 고려하십시오.

# [L-03] Solana 롤백으로 인한 상태 불일치

프로토콜의 한 함수는 Solana 롤백 발생 시 상태 불일치에 취약합니다.

1. **구성 매개변수 설정**:
   - Solana 롤백 중에 전역 구성 매개변수가 오래될 수 있습니다.
   - 프로토콜이 오래된 유효하지 않은 설정으로 작동할 수 있습니다.
   - 시스템 오작동 또는 취약점의 가능성

권장 사항:

1. **오래된 구성 감지**

   - `LastRestartSlot` sysvar를 활용하여 구성 유효성 확인
   - 구성이 오래된 경우 프로토콜 자동 일시 중지
   - 작업 재개 전 관리자 개입 필요

2. **last_updated_slot 필드 추가**

   - 본딩 커브 상태에 추적 필드 포함
   - 구성 업데이트 타임스탬프 모니터링

3. **오래된 구성 확인 구현**
   ```rust
   fn is_config_outdated(global: &Global) -> Result<bool> {
       let last_restart_slot = LastRestartSlot::get()?;
       Ok(global.last_updated_slot <= last_restart_slot.last_restart_slot)
   }
   ```

# [L-04] 풀 초기화 시 불충분한 토큰 계정 검증

`InitializePoolWithConfig` 인스트럭션의 여러 토큰 계정에는 적절한 소유권 및 민트 관계 검증이 부족하며 적절한 토큰 계정 검증 대신 `UncheckedAccount`에 의존합니다. 이로 인해 잘못된 토큰 계정이 전달될 수 있습니다.

현재 구현은 여러 토큰 관련 계정에 대해 `UncheckedAccount`를 사용합니다.

```rust
#[derive(Accounts)]
pub struct InitializePoolWithConfig<'info> {
    #[account(mut)]
    /// CHECK: Token A LP
    pub a_vault_lp: UncheckedAccount<'info>,

    #[account(mut)]
    /// CHECK: Vault LP accounts and mints for token A
    pub a_token_vault: UncheckedAccount<'info>,

    // ... other unchecked token accounts
}
```

다음 사항에 대한 검증이 부족합니다.

1. 토큰 계정 소유권 확인
2. 민트 관계 확인
3. Anchor의 계정 유효성 검사가 제공하는 타입 안전성

적절한 토큰 계정 검증 추가:

```rust
#[derive(Accounts)]
pub struct InitializePoolWithConfig<'info> {
    #[account(
        mut,
        constraint = a_vault_lp.owner == vault_program.key() @ ContractError::InvalidOwner,
        constraint = a_vault_lp.mint == a_vault_lp_mint.key() @ ContractError::InvalidMint
    )]
    pub a_vault_lp: Box<Account<'info, TokenAccount>>,

    #[account(
        mut,
        constraint = a_token_vault.owner == vault_program.key() @ ContractError::InvalidOwner,
        constraint = a_token_vault.mint == token_a_mint.key() @ ContractError::InvalidMint
    )]
    pub a_token_vault: Box<Account<'info, TokenAccount>>,

    // Similar changes for other token accounts
}
```

새 오류 변형 추가:

```rust
#[error_code]
pub enum ContractError {
    #[msg("Invalid token account owner")]
    InvalidOwner,

    #[msg("Invalid token account mint")]
    InvalidMint,
}
```

모든 토큰 계정에 유사한 유효성 검사 적용:

- Vault LP 계정 (a_vault_lp, b_vault_lp)
- 토큰 금고 계정 (a_token_vault, b_token_vault)
- 지불자 토큰 계정 (payer_token_a, payer_token_b)
- 프로토콜 수수료 계정 (protocol_token_a_fee, protocol_token_b_fee)

# [L-05] 공격자가 전역 계정을 일찍 초기화할 수 있음

## 심각도

**영향:** 중간

**가능성:** 낮음

## 설명

전역 계정은 고정 문자열(Global::SEED_PREFIX)을 시드로 사용하며, `global.initialized`가 설정되지 않은 한 누구나 초기화 함수를 호출할 수 있습니다.

```rust
#[event_cpi]
#[derive(Accounts)]
#[instruction(params: GlobalSettingsInput)]
pub struct Initialize<'info> {
    #[account(mut)]
    authority: Signer<'info>,

    #[account(
        init,
        space = 8 + Global::INIT_SPACE,
@>    seeds = [Global::SEED_PREFIX.as_bytes()],
@>    constraint = global.initialized != true @ ContractError::AlreadyInitialized,
        bump,
        payer = authority,
    )]
    global: Box<Account<'info, Global>>,

    system_program: Program<'info, System>,
}

impl Initialize<'_> {
    pub fn handler(ctx: Context<Initialize>, params: GlobalSettingsInput) -> Result<()> {
        let global = &mut ctx.accounts.global;
        global.update_authority(GlobalAuthorityInput {
            global_authority: Some(ctx.accounts.authority.key()),
            migration_authority: Some(ctx.accounts.authority.key()),
        });
        global.update_settings(params.clone());

        require_gt!(global.mint_decimals, 0, ContractError::InvalidArgument);

        global.initialized = true;
        emit_cpi!(global.into_event());
        Ok(())
    }
}
```

따라서 공격자는 선제적으로 초기화 함수를 호출하여 자신의 주소를 관리자로 설정할 수 있습니다. 결과적으로 전역 계정을 재설정할 수 없습니다.

## 권장 사항

서명자의 주소를 시드로 호출하거나 고정 주소만 초기화 함수를 호출하도록 하십시오.
