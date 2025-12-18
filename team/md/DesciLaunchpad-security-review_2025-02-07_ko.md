# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**merklelabshq/desci-launchpad** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Desci Launchpad 소개 (About Desci Launchpad)

Desci Launchpad는 큐레이션, 가속화 및 분리를 포함하여 프로젝트를 시작하고 자금을 조달하기 위한 시스템입니다. 참가자가 프로젝트를 후원할 수 있도록 하며, 모금된 자금은 유동성 풀을 생성하고 커뮤니티 혜택을 제공합니다.

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

_검토 커밋 해시_ - [7167b472dd4e1c1a45ed2d49f00cd1dfadad0fcd](https://github.com/merklelabshq/desci-launchpad/tree/7167b472dd4e1c1a45ed2d49f00cd1dfadad0fcd)

_수정 검토 커밋 해시_ - [e41b734a8280fb7fe6440f9daf5f647b6ac5dec9](https://github.com/merklelabshq/desci-launchpad/tree/e41b734a8280fb7fe6440f9daf5f647b6ac5dec9)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `buy_token.rs`
- `claim_revenue`
- `claim_token`
- `create_token`
- `deposit_token`
- `init_stats`
- `mod`
- `update_token`
- `withdraw_tokenstate`
- `lib`
- `error`
- `constants`

# 발견 사항 (Findings)

# [C-01] 미청구 토큰이 `stats_token`에 잠겨 영구적인 자금 손실 발생

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`claim_token` 함수에서 토큰은 총 `claimed_supply`에 대해 구매한 토큰의 비율에 따라 사용자에게 분배됩니다. `claimed_supply`가 `sale_supply`보다 작으면 사용자는 전체 `sale_supply`가 아닌 `claimed_supply`의 비례 금액을 받습니다.

이는 다음 코드 조각에 반영되어 있습니다:

```rust
let adjusted_tokens = ((user_stats.tokens_purchased as f64 / token_stats.claimed_supply as f64)
    * (token_stats.sale_supply.min(token_stats.claimed_supply) as f64))
    as u64;

transfer(
    CpiContext::new_with_signer(
        ctx.accounts.token_program.to_account_info(),
        Transfer {
            from: ctx.accounts.stats_token.to_account_info(),
            to: ctx.accounts.user_token.to_account_info(),
            authority: ctx.accounts.stats.to_account_info(),
        },
        &[signer_seed],
    ),
    adjusted_tokens,
)?;
```

`stats_token`에서 분배된 금액은 아래와 같이 `deposit_token`에서 `stats` 계정으로 원래 전송된 `sale_supply`보다 작습니다:

```rust
pub fn deposit_token_handler(ctx: Context<DepositToken>) -> Result<()> {
    let token_stats = &mut ctx.accounts.token_stats;
    transfer(
        CpiContext::new(
            ctx.accounts.token_program.to_account_info(),
            Transfer {
                from: ctx.accounts.authority_token.to_account_info(),
                to: ctx.accounts.stats_token.to_account_info(),
                authority: ctx.accounts.authority.to_account_info(),
            },
        ),
        token_stats.sale_supply,
    )?;
```

이로 인해 `sale_supply`와 `claimed_supply`의 차이가 `stats_token` 계정에 무기한 잠겨 프로토콜에 영구적인 자금 손실이 발생합니다.

## 권장 사항

이 문제를 완화하려면 다음 솔루션 중 하나를 구현하는 것을 고려하십시오:

1. 잉여금이 있는 경우 `sale_supply`와 `claimed_supply`의 차액을 권한(authority) 계정으로 다시 전송하는 함수를 도입합니다.
2. 이 차이를 고려하고 남은 토큰을 그에 따라 전송하도록 `claim_revenue` 함수를 수정합니다.

# [H-01] `withdraw_tokens`가 `claimed_supply`를 조정하지 못해 토큰이 영구적으로 잠김

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`withdraw_tokens` 함수를 사용하면 `min_threshold`가 충족되지 않을 때 사용자가 결제 토큰을 회수할 수 있습니다. 그러나 함수는 `claimed_supply`를 업데이트하거나 환불된 금액을 반영하기 위해 `tokens_purchased`를 권한(authority)으로 다시 전송하지 않습니다.

사용자의 통계가 청구됨(claimed)으로 표시되므로 해당 `tokens_purchased`는 무기한 잠긴 상태로 유지됩니다.

예를 들어, 사용자가 `tokens_purchased = 1000`이고 `claimed_supply = 5000`을 가지고 있는 경우 인출 후 `withdraw_tokens` 함수를 호출하여 결제 토큰을 돌려받으면 다른 사람들은 `4000` 토큰만 청구할 수 있고 `1000` 토큰은 영구적으로 잠긴 상태로 유지됩니다.

관련 코드 조각은 아래와 같습니다:

```rust
pub fn withdraw_token_handler(ctx: Context<WithdrawToken>) -> Result<()> {
    let token_stats = &mut ctx.accounts.token_stats;
    let user_stats: &mut Account<UserStats> = &mut ctx.accounts.user_stats;

    let curr_time = Clock::get()?.unix_timestamp;
    require!(
        token_stats.end_time < curr_time,
        RocketxLaunchpadError::InvalidWithdrawTime
    );
    require!(
        token_stats.is_launched,
        RocketxLaunchpadError::TokenNotLaunched
    );
    require!(
        !user_stats.is_claimed,
        RocketxLaunchpadError::TokenAlreadyClaimed
    );
    require!(
        token_stats.revenue < token_stats.min_threshold,
        RocketxLaunchpadError::InvalidThreshold
    );

    user_stats.is_claimed = true;

    let signer_seed = &[STATS_SEED, &[ctx.accounts.stats.bump]];

    let refund_amount = (((user_stats.tokens_purchased as f64)
        / (10u64.pow(token_stats.decimals as u32) as f64))
        * (token_stats.price_per_token as f64)) as u64;

    transfer(
        CpiContext::new_with_signer(
            ctx.accounts.token_program.to_account_info(),
            Transfer {
                from: ctx.accounts.stats_pay_token.to_account_info(),
                to: ctx.accounts.user_pay_token.to_account_info(),
                authority: ctx.accounts.stats.to_account_info(),
            },
            &[signer_seed],
        ),
        refund_amount,
    )?;

    Ok(())
}
```

## 권장 사항

토큰 환불을 적절하게 반영하고 영구적인 토큰 잠금을 방지하려면 다음 완화 조치를 고려하십시오:

1. 사용자가 결제 토큰을 인출할 때 **`claimed_supply`에서 `tokens_purchased`를 뺍니다**.
2. **해당 `tokens_purchased` 금액을 권한(authority)으로 다시 전송**하여 토큰이 무기한 잠기지 않도록 합니다.

# [H-02] `claim_revenue`는 관리자가 최소 임계값 미만일 때 사용자 인출을 차단할 수 있게 함

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`claim_revenue` 함수는 수익이 `min_threshold` 아래로 떨어지면 실행할 수 없어야 합니다. 이 경우 사용자는 판매 기간이 종료된 후 결제 토큰을 회수할 수 있습니다. 그러나 관리자가 판매 종료 직후 `claim_revenue` 함수를 호출하면 사용자는 더 이상 `withdraw_token`을 통해 결제 토큰을 인출할 수 없습니다.

`withdraw_token` 함수는 아래 코드 조각과 같이 인출을 활성화하려면 수익이 `min_threshold`보다 작아야 합니다:

```rust
pub fn withdraw_token_handler(ctx: Context<WithdrawToken>) -> Result<()> {
    require!(
        token_stats.is_launched,
        RocketxLaunchpadError::TokenNotLaunched
    );

    require!(
        !user_stats.is_claimed,
        RocketxLaunchpadError::TokenAlreadyClaimed
    );

    require!(
        token_stats.revenue < token_stats.min_threshold,
        RocketxLaunchpadError::InvalidThreshold
    );
```

관리자가 수익을 인출하면 사용자는 결제 토큰을 인출할 수 없습니다. 또한 사용자가 일부 결제 토큰을 인출하면 `stats_pay_token`의 잔액이 `token_stats.revenue`보다 낮아져 전송이 실패하므로 관리자는 `claim_revenue`를 호출할 수 없습니다.

## 권장 사항

다음 수정 사항 중 하나를 구현하는 것을 고려하십시오:

1. **런치패드가 최소 수익 임계값에 도달하지 않은 경우 `claim_revenue`를 비활성화**하여 사용자만 결제 토큰을 회수할 수 있도록 합니다.
2. **`claim_revenue`에 대한 쿨다운 기간을 도입**하여 관리자가 수익을 청구하기 전에 사용자에게 결제 토큰을 인출할 시간을 줍니다. `stats_pay_token` 계정의 남은 결제 토큰 잔액만 전송하도록 `claim_revenue`를 수정합니다.

# [M-01] 최소 임계값이 초과되지 않은 경우 토큰 청구 방지

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`min_threshold`가 충족되지 않을 때 사용자가 **토큰을 청구**하거나 **결제 토큰을 인출**하도록 허용하면 자금이 잠길 수 있습니다. 일부 사용자는 토큰을 청구하고 다른 사용자는 인출하는 경우 미수급 결제가 계약에 남아 자금 손실로 이어지는 시나리오가 생성됩니다.

이를 방지하기 위해 최소 수익 임계값이 초과되지 않은 경우 토큰 청구를 제한해야 합니다.

## 권장 사항

`claim_tokens` 함수에 다음 검사를 추가하여 수익이 최소 임계값을 충족하거나 초과할 때만 토큰을 청구할 수 있도록 하십시오:

```rust
require!(
    token_stats.revenue >= token_stats.min_threshold,
    RocketxLaunchpadError::InvalidThreshold
);
```

# [L-01] > 0 구매 금액에 대한 잘못된 확인

`buy_token`은 `buy_amount`가 > 0인지 확인하는 대신 `token_amount`가 0보다 큰지 잘못 확인합니다. 결과적으로 0 금액의 토큰을 구매할 수 있습니다.

```rust
    require!(args.token_amount > 0, RocketxLaunchpadError::InvalidAmount);
```

대신 `buy_amount`를 확인하도록 확인을 리팩터링하는 것을 권장합니다. `buy_amount`는 `token_amount`와 `limit_per_wallet - tokens_purchased` 중 최소값으로 먼저 설정되므로 아래 확인도 수정됩니다:

```rust
    require!(
        user_stats.tokens_purchased <= token_stats.limit_per_wallet,
        RocketxLaunchpadError::ExceedsLimit
    );
```

# [L-02] 최대 쿨다운 기간 검증 누락

`args.cooldown_duration`에 제공된 `cooldown_duration` 값은 유효성 검사 없이 `token_stats.cooldown_duration`에 직접 할당됩니다. 이를 통해 사용자는 과도하게 긴 쿨다운 기간을 설정할 수 있으며, 이는 프로토콜의 유용성과 공정성에 부정적인 영향을 미칠 수 있습니다.

```rust
token_stats.cooldown_duration = args.cooldown_duration;
```

`cooldown_duration`이 미리 정의된 상한을 초과하지 않도록 확인을 추가하여 **최대 쿨다운 기간**을 도입하십시오.

수정 예:

```rust
require!(
    args.cooldown_duration <= MAX_COOLDOWN_DURATION,
    RocketxLaunchpadError::InvalidCooldownDuration
);

token_stats.cooldown_duration = args.cooldown_duration;
```

# [L-03] 사용되지 않는 `is_lp_created` 플래그

`token_stats.is_lp_created` 플래그는 `update_token_handler` 함수에서 업데이트되지만 계약의 다른 곳에서는 사용되지 않습니다. 이는 불필요한 상태 저장을 나타낼 수 있으며, 중복 데이터 또는 계약 로직의 잠재적 혼란으로 이어질 수 있습니다.

```rust
pub fn update_token_handler(ctx: Context<UpdateToken>) -> Result<()> {
    let token_stats = &mut ctx.accounts.token_stats;

    token_stats.is_lp_created = true;

    Ok(())
}
```

관련 로직에서 `is_lp_created` 플래그를 활용하거나 필요하지 않은 경우 제거하는 것을 고려하십시오.

# [L-04] Anchor.toml에 툴체인 버전 누락

`Anchor.toml` 파일은 `[toolchain]` 섹션 아래에 `anchor_version` 또는 `solana_version`을 지정하지 않습니다. 이는 특히 다른 팀원이나 CI/CD 파이프라인이 다른 버전의 Anchor 또는 Solana를 사용하는 경우 프로그램을 빌드하거나 배포할 때 호환성 문제로 이어질 수 있습니다.

권장 사항:

`[toolchain]` 섹션에 `anchor_version` 및 `solana_version`을 추가하여 일관된 빌드 및 배포를 보장하십시오.

# [L-05] `transfer_checked` 대신 `transfer` 사용

`buy_token_handler` 함수 및 기타 함수에서 `transfer` 지침은 사용자의 결제 토큰 계정에서 통계 결제 토큰 계정으로 토큰을 전송하는 데 사용됩니다. 그러나 `transfer`는 민트 계정이나 소수자리 수를 검증하지 않으므로 잠재적인 문제가 발생할 수 있습니다:

1. **민트 불일치**: 잘못된 민트 계정이 전달되면 전송이 성공할 수 있지만 잘못된 토큰을 포함할 수 있습니다.
2. **소수점 불일치**: 소수자리 수가 올바르지 않으면 전송된 금액이 의도한 것과 다를 수 있습니다(예: 소수점 불일치로 인해 10 대신 1,000,000 토큰 전송).

민트 계정과 전달될 소수자리 수가 필요한 `transfer_checked`로 `transfer` 지침을 교체하십시오.

# [L-06] 모든 관리자 키가 동일함

상수 `DEV_PUBKEY`, `ADMIN_PUBKEY` 및 `MINT_AUTHORITY_PUBKEY`는 모두 동일한 값으로 설정됩니다:

```rust
pub const DEV_PUBKEY: Pubkey = pubkey!("BjjFpCbTrFVn3ZgcdCv4jTLAzbbDCMV1Vo115XJSJ7XG");
pub const ADMIN_PUBKEY: Pubkey = pubkey!("BjjFpCbTrFVn3ZgcdCv4jTLAzbbDCMV1Vo115XJSJ7XG");
pub const MINT_AUTHORITY_PUBKEY: Pubkey = pubkey!("BjjFpCbTrFVn3ZgcdCv4jTLAzbbDCMV1Vo115XJSJ7XG");
```

이 설계는 모든 관리 권한을 단일 키로 중앙 집중화하기 때문에 위험을 초래합니다. 이 키가 손상되면 공격자는 다음을 포함한 모든 관리 기능에 대한 제어 권한을 얻을 수 있습니다:

- 개발 운영 (`DEV_PUBKEY`)
- 관리 작업 (`ADMIN_PUBKEY`)
- 민팅 권한 (`MINT_AUTHORITY_PUBKEY`)

공유 키가 손상되면 전체 시스템이 위험에 처하게 됩니다. 공격자는 토큰 민팅, 구성 수정 또는 민감한 데이터 액세스와 같은 승인되지 않은 작업을 수행할 수 있습니다.

권장 사항:

역할 분리를 보장하기 위해 `DEV_PUBKEY`, `ADMIN_PUBKEY` 및 `MINT_AUTHORITY_PUBKEY`에 고유한 공개 키를 할당하십시오.

# [L-07] Solana 롤백으로 인한 상태 불일치

프로토콜의 한 함수는 Solana 롤백 시 상태 불일치에 취약합니다:

1. **구성 매개변수 설정**:
   - 글로벌 구성 매개변수는 Solana 롤백 중에 오래된 상태가 될 수 있습니다.
   - 프로토콜은 오래되고 유효하지 않은 설정으로 작동할 수 있습니다.
   - 시스템 오작동 또는 취약점의 가능성

권장 사항:

1. **오래된 구성 감지**

   - `LastRestartSlot` sysvar를 활용하여 구성 유효성 확인
   - 구성이 오래된 경우 프로토콜 자동 일시 중지
   - 운영 재개 전 관리자 개입 필요

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

# [L-08] `start_time` 및 `end_time`에 대한 유효성 검사 누락

`create_token_handler` 함수는 `CreateTokenArgs`에 제공된 `start_time` 및 `end_time`이 현재 시간보다 미래인지 확인하지 않습니다.

이로 인해 다음과 같은 문제가 발생할 수 있습니다:

**유효하지 않은 런치패드 타이밍**: `start_time` 또는 `end_time`이 과거인 경우 토큰 판매가 즉시 시작되거나 종료될 수 있습니다.

**코드 위치 :** [create_token.rs](https://github.com/merklelabshq/desci-launchpad/blob/master/contract/programs/desci-launchpad/src/instructions/create_token.rs)

`start_time`과 `end_time`이 모두 현재 시간보다 미래인지 확인하는 유효성 검사를 추가하십시오. `Clock::get()?` 함수를 사용하여 현재 타임스탬프를 검색하고 `start_time` 및 `end_time`과 비교하십시오.
