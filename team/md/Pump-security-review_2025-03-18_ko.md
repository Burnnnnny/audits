# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**pump-fun/fixed-price-token-sale** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Pump 정보

Pump Token Fixed Price Sale은 사용자가 사전 정의된 기간 동안 고정 가격으로 USDC, USDT 또는 SOL을 입금하는 스마트 계약 관리 이벤트입니다. 판매가 종료된 후 PUMP 토큰이 배포되며, 통합된 CEX는 PDA 및 API 호출을 통해 기여를 추적하여 과잉 판매를 방지합니다. 정산에는 프로젝트와 거래소 간의 수동 조정이 포함되며, CEX는 사용자에게 최종 토큰 분배를 처리합니다.

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

_검토 커밋 해시_ - [52715ef1e5639b8192d17764ef0cc746b782c879](https://github.com/pump-fun/fixed-price-token-sale/tree/52715ef1e5639b8192d17764ef0cc746b782c879)

_수정 검토 커밋 해시_ - [328cc64ea2dc9293f0e72771a7a97c43c15be71b](https://github.com/pump-fun/fixed-price-token-sale/tree/328cc64ea2dc9293f0e72771a7a97c43c15be71b)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `blacklist_wallet`
- `initialize_global_state`
- `initialize_global_vault`
- `mod`
- `post_cex_sale`
- `update_global_state`
- `update_oracle`
- `withdraw`
- `buy`
- `distribute`
- `mod`
- `constants`
- `errors`
- `lib`
- `states`
- `utils`

# 발견 사항

# [M-01] SOL 가격 초기화로 인한 불공정한 거래

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`initialize_global_state` 인스트럭션에서 SOL 가격은 200 USD/SOL로 초기화됩니다.
```rust
pub fn initialize_global_state_ix(
    ctx: Context<InitializeGlobalState>,
    start_time: i64,
    end_time: i64,
    tokens_for_sale: u64,
) -> Result<()> {
    ...
    oracle_account.mint = constants::DEPOSIT_MINT3;
    oracle_account.price = 200_00000;
    Ok(())
}
```
그러나 실제 SOL 가격은 크게 다를 수 있습니다. 예를 들어, 보고서 작성 당시 SOL 가격은 128.7 USD/SOL입니다.
이는 첫 번째 거래자 그룹이 `oracle_account`가 업데이트되기 전에 더 높은 가격의 SOL로 토큰을 구매하여 이익을 얻을 수 있게 합니다.

## 권장 사항

SOL 가격을 초기화할 때 하드코딩된 값을 사용하지 않는 것이 좋습니다. 대신 `super_admin`이 초기화를 위해 실제 가격을 제공해야 합니다.

# [M-02] `blacklist_user_ix` 인스트럭션이 `is_blacklisted` 업데이트를 건너뜀

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`super_admin`은 `blacklist_user_ix` 인스트럭션을 호출하여 `user_state`가 구매한 토큰을 환불할 수 있지만, 계약은 `user_state`의 `is_blacklisted` 플래그를 `true`로 업데이트하지 않아 이 사용자가 계속해서 토큰을 구매하는 것을 방지하지 못합니다.

## 권장 사항

`blacklist_user_ix` 인스트럭션에서 `user_state`의 `is_blacklisted` 플래그를 `true`로 설정하십시오.
```diff
pub fn blacklist_user_ix<'a, 'b, 'c, 'info>(
    ctx: Context<'a, 'b, 'c, 'info, BlacklistUser<'info>>,
) -> Result<()> {
    //...
+   user_state.is_blacklisted = true;
    Ok(())
}
```

# [M-03] 배포 후 구매 시 자금 잠김

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

배포 후 사용자가 토큰을 구매할 수 있도록 허용하면 자금이 영구적으로 손실될 수 있습니다. `distribute` 함수는 사용자당 한 번만 호출할 수 있으므로, 그 이후에 추가 구매를 하면 배포를 다시 실행할 수 없기 때문에 자금이 무기한 잠기게 됩니다.

관련 코드 스니펫:

```rust
    if user_state.tokens_received > 0 {
        // Only one distribute per user
        return Err(SaleError::AlreadyDistributed.into());
    }
```

사용자는 여러 번, 여러 민트(mint)에 걸쳐 구매할 수 있으므로, 자신도 모르게 토큰을 청구할 수 없는 상태로 자금을 잠글 위험이 있습니다.

## 권장 사항

`user_state`에 `is_distributed` 플래그를 도입하고 `buy` 함수에서 확인을 강제하여 배포가 발생한 후에는 구매를 방지하십시오. 예:

```rust
if user_state.is_distributed {
    return Err(SaleError::AlreadyDistributed.into());
}
```

# [M-04] 승인되지 않은 글로벌 및 오라클 상태 초기화

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`InitializeGlobalState` 인스트럭션은 모든 사용자가 `super_admin` 및 `admin`에 대한 임의의 값을 사용하여 `global_state` 및 `oracle` 계정을 모두 초기화할 수 있도록 허용합니다. 이 인스트럭션에는 필요한 접근 제어가 부족합니다.

```rust
#[derive(Accounts)]
pub struct InitializeGlobalState<'info> {
    #[account(mut)]
    pub super_admin: Signer<'info>,

    /// CHECK: no need to check
    #[account(mut)]
    pub admin: AccountInfo<'info>,

    #[account(
        init,
        seeds = [
            GLOBAL_STATE_SEED.as_bytes(), super_admin.key().as_ref()
        ],
        bump,
        payer = super_admin,
        space = GlobalState::SIZE
    )]
    pub global_state: Account<'info, GlobalState>,

    #[account(
        init,
        seeds = [
            ORACLE_STATE_SEED.as_bytes(), admin.key().as_ref()
        ],
        bump,
        payer = super_admin,
        space = OracleAccount::SIZE
    )]
    pub oracle: Account<'info, OracleAccount>,

    pub clock: Sysvar<'info, Clock>,
    pub system_program: Program<'info, System>,
}
```

`super_admin`이 신뢰할 수 있는 기관임을 강제하는 제약 조건이 없으며, `admin`이 서명자일 필요도 없습니다. 이를 통해 모든 사용자가 실제 `admin` 공개 키를 포함한 임의의 입력을 사용하여 가짜 `global_state` 및 `oracle`을 생성할 수 있습니다.

---

### 영향 1: 가짜 글로벌 상태를 통한 토큰 추출

악의적인 행위자는 자신의 통제하에 가짜 `global_state`를 초기화할 수 있습니다. `super_admin`이 합법적인지 확인하는 유효성 검사가 없기 때문에, `buy` 함수를 호출하는 사용자는 자신도 모르게 이 가짜 상태와 상호 작용할 수 있습니다.

```rust
pub struct Buy<'info> {
    #[account(mut)]
    pub wallet_address: Signer<'info>,

    /// CHECK: No need to check
    pub super_admin: AccountInfo<'info>,

    #[account(
        mut,
        has_one = super_admin,
        seeds = [GLOBAL_STATE_SEED.as_bytes(), super_admin.key().as_ref()],
        bump
    )]
    pub global_state: Account<'info, GlobalState>,
}
```

결과적으로:

1. 일반 사용자는 공격자가 통제하는 악의적인 금고로 토큰을 보낼 수 있습니다.
2. 공격자는 나중에 `withdraw` 함수를 호출하여 해당 토큰을 자신을 위해 추출할 수 있습니다.
3. 사용자는 자신이 상호 작용하는 `global_state`가 신뢰할 수 있는지 확인할 방법이 없습니다.

이로 인해 의심하지 않는 사용자의 **복구할 수 없는 토큰 손실**이 발생합니다.

---

### 영향 2: 합법적인 관리자 오라클 계정 생성에 대한 서비스 거부(DoS)

`oracle` 계정은 `admin`의 공개 키를 사용하여 파생되지만 초기화 중에 `admin`이 **서명할 필요가 없기** 때문에, 악의적인 행위자는 **어떤 관리자에 대해서도 선제적으로 오라클 계정을 생성**할 수 있습니다.

```rust
#[account(
    init,
    seeds = [
        ORACLE_STATE_SEED.as_bytes(), admin.key().as_ref()
    ],
    bump,
    payer = super_admin,
    space = OracleAccount::SIZE
)]
pub oracle: Account<'info, OracleAccount>,
```

공격 흐름:
1. 공격자는 실제 `admin`의 공개 키와 잘못된 시작/종료 시간 또는 판매용 토큰과 같은 임의의(유효하지 않은) 오라클 매개변수를 사용하여 `global_state`를 초기화합니다.
2. PDA는 결정론적이므로 오라클 계정이 고정됩니다.
3. 나중에 합법적인 관리자가 자신의 오라클을 초기화하려고 시도하면 PDA가 이미 존재하므로 유효한 오라클을 설정할 수 없습니다.
4. 이는 PDA가 탈취된 경우 관리자가 시스템에서 운영하는 것을 효과적으로 차단합니다.

이로 인해 **합법적인 관리자에 대한 서비스 거부 조건**이 발생하여 의도한 구성을 설정할 수 없게 됩니다.

---

## 권장 사항

- **`super_admin` 제한**: `super_admin`을 상수 신뢰 주소(하드코딩되거나 빌드 타임 설정으로 전달됨)로 설정하십시오. 이렇게 하면 배포자나 거버넌스 당국만이 `global_state` 및 `oracle` 계정을 초기화할 수 있습니다.

- **`InitializeGlobalState`에서 `admin` 서명 요구**: 관리자의 동의 없이 관리자의 공개 키를 사용하여 오라클 PDA가 탈취되는 것을 방지하기 위해 `InitializeGlobalState`에서 `admin`이 서명자가 되도록 요구하십시오.

- **PDA 파생 재고**: 여러 합법적인 `global_state` 계정이 필요한 경우 `global_state`의 시드에 `super_admin.key()` 대신 `admin.key()`를 사용하십시오. 이를 통해 합법적인 관리자가 자신의 네임스페이스를 제어할 수 있습니다.

이러한 변경 사항은 사용자 토큰 도난과 오라클 초기화 DoS 공격을 모두 방지합니다.

# [L-01] 구매 인스트럭션에 최소 수령 토큰 금액 추가

SOL을 사용하여 `buy` 인스트럭션으로 토큰을 구매할 때, 오라클 가격 업데이트로 인해 트랜잭션 실행 결과 사용자가 더 적은 토큰을 받을 수 있습니다.
```rust
pub fn buy_ix(ctx: Context<Buy>, amount: u64) -> Result<()> {
    ...
    let buy_value =
        oracle_state.get_token_value_usd(mint_key, amount as u128, ctx.accounts.mint.decimals)?;

    let number_of_tokens = global_state.usd_to_tokens(buy_value)?;

    user_state.deposit_value += buy_value;
    user_state.tokens_bought += number_of_tokens;
    global_state.tokens_sold += number_of_tokens;
    ...
}
```
사용자가 예상치 않게 더 적은 토큰을 받는 것을 방지하기 위해 최소 수령 토큰 금액을 추가하는 것이 좋습니다.

# [L-02] 판매가 예상 금액에 근접할 때 사용자가 CEX 업데이트에 DoS를 유발할 수 있음

판매 수량이 예상 금액에 도달하려고 할 때, CEX가 최종 판매를 완료하고 온체인 업데이트 인스트럭션을 호출하면, 악의적인 사용자가 소량의 토큰을 구매하여 총 토큰 공급량이 CEX 업데이트 후 예상을 초과하게 만들어 트랜잭션을 되돌릴 수 있습니다.
```rust
    pub fn is_active(self, current_time: i64) -> Result<()> {
        ...
        if self.tokens_sold + self.cex_tokens_sold >= self.tokens_for_sale {
            return err!(SaleError::SaleEnded);
        }
        Ok(())
    }
```
관리자가 악의적인 주소를 블랙리스트에 올리더라도 공격자는 지갑을 바꿔 계속 공격할 수 있습니다.

블록체인에 대한 CEX 트랜잭션 업데이트에는 지연이 있습니다. CEX의 예상 토큰 판매 수량과 온체인의 예상 토큰 구매 수량을 구분하는 것이 좋습니다.

# [L-03] `buy_ix`에서의 불충분한 토큰 처리

`buy_ix` 함수에서 사용자는 세 가지 민트 중 하나의 금액을 지정하고 함수는 사용자에게 판매할 토큰의 양을 계산합니다. 그러나 요청된 금액이 사용 가능한 토큰을 초과하면 `(>tokens_for_sale)`, 함수가 되돌려집니다. 결과적으로 사용자의 요청을 부분적으로 이행할 수 있는 유효한 스왑이 방지됩니다.
이 문제는 요청된 토큰 금액이 남은 토큰을 초과할 때 부분 구매를 방지합니다.

권장 사항:

구매 기능을 수정하여 요청된 금액이 사용 가능한 토큰을 초과하는 경우를 처리하십시오. 요청된 금액이 더 큰 경우 함수는 `tokens_for_sale`을 완료하는 데 필요한 금액을 고려해야 합니다.

# [L-04] `blacklist_fee`에 대한 수수료 내림

`blacklist_user_ix` 함수에서 `blacklist_fee`는 다음과 같이 계산됩니다.

```rust
        let blacklist_fee = amount
            .checked_mul(BLACKLIST_FEE_BPS as u64)
            .unwrap()
            .checked_div(10_000 as u64)
            .unwrap();
```
이로 인해 특히 소규모 거래의 경우 수수료가 0으로 줄어드는 시나리오가 발생할 수 있습니다. 이로 인해 프로토콜이 잠재적인 수수료를 잃을 수 있습니다.

권장 사항:

프로토콜이 올바른 수수료를 받도록 하려면 함수는 수수료를 내림하는 대신 올림해야 합니다. 이는 수수료 계산 결과가 소수점 이하 값인 경우에도 가장 가까운 정수로 올림하여 수수료 손실을 방지합니다.

```rust
        let blacklist_fee = amount
            .checked_mul(BLACKLIST_FEE_BPS as u64)
            .unwrap()
            .checked_add(9_999)
            .unwrap()
            .checked_div(10_000 as u64)
            .unwrap();
```
나눗셈 전에 9_999를 더하면 수수료 계산이 항상 올림됩니다.

# [L-05] `is_active` 조건이 판매 완료를 차단함

`is_active` 함수는 현재 다음 조건으로 인해 전체 판매를 방지합니다.

```rust
if self.tokens_sold + self.cex_tokens_sold >= self.tokens_for_sale
```

이 조건은 마지막 토큰이 판매되는 것을 허용하지 않아 사실상 완전한 판매를 차단합니다.

권장 사항:

조건을 `>=` 대신 `>`를 사용하도록 수정하십시오.

```diff
- if self.tokens_sold + self.cex_tokens_sold >= self.tokens_for_sale {
+ if self.tokens_sold + self.cex_tokens_sold > self.tokens_for_sale {
```

# [L-06] 불필요한 `is_active` 확인으로 지연 시 이벤트 방출이 차단될 수 있음

지연된 트랜잭션의 경우 상태 변경이 필요하지 않으므로 `is_active` 상태를 확인할 필요가 없습니다. 그러나 현재 구현은 여전히 `is_active`를 확인하며, 일반 트랜잭션과 지연된 트랜잭션(이전에 예약됨) 사이에 상태가 비활성 상태가 되면 지연된 트랜잭션이 이벤트를 방출하지 않습니다. 이로 인해 로그 누락 및 트랜잭션 실행 추적의 불일치가 발생할 수 있습니다.

# [L-07] `initializeCexEvent` 방출 누락

`initialize_sale_state_ix` 함수는 구현된 대로 `InitializeCexEvent`를 방출해야 합니다. 그러나 함수는 이 이벤트를 방출하지 않습니다.

# [L-08] `max_staleness_allowed`에 대한 유효성 검사 누락

`max_staleness_allowed` 변수에 값이 할당되지만 오라클 계정의 가격 유효성 검사 로직에서는 고려되지 않습니다. 이러한 간과로 인해 시스템은 제약 없이 오래된 가격 데이터를 사용할 수 있게 되며, 잠재적으로 부정확한 토큰 평가로 이어질 수 있습니다.

이 문제는 `initialize_global_state_ix` 함수에서 비롯됩니다.

```rust
pub fn initialize_global_state_ix(
    ctx: Context<InitializeGlobalState>,
    start_time: i64,
    end_time: i64,
    tokens_for_sale: u64,
) -> Result<()> {
    ...

    oracle_account.max_staleness_allowed = u64::MAX;
    ...
}
```

또한 `get_token_value_usd` 함수는 `get_token_price`를 사용하여 토큰 가격을 검색하지만 검색된 가격이 허용된 부실 임계값 내에 있는지 확인하는 검사가 없습니다.

```rust
pub fn get_token_value_usd(&self, mint: Pubkey, amount: u128, decimals: u8) -> Result<u64> {
    let price = self.get_token_price(mint)? as u128;
    let value_in_u128 = price
        .checked_mul(amount)
        .ok_or_else(|| SaleError::MathOverflow)?
        .checked_div(10u128.pow(decimals as u32))
        .ok_or_else(|| SaleError::MathOverflow)?;
    let value_in_u64: u64 = value_in_u128
        .try_into()
        .map_err(|_| SaleError::MathOverflow)?;
    Ok(value_in_u64)
}
```

`max_staleness_allowed`를 검증하지 않으면 계약이 오래되거나 조작된 가격을 사용할 위험이 있으며, 이는 잘못된 토큰 평가 및 불공정한 거래 조건으로 이어질 수 있습니다.

권장 사항:

가격 유효성 검사 로직에 `max_staleness_allowed` 확인을 구현하여 토큰 가격이 최신 상태인지 확인하십시오. 이 확인은 `get_token_price`에서 토큰 가격을 검색할 때 강제되어야 합니다. 예:

```rust
pub fn get_token_price(&self, mint: Pubkey) -> Result<u64> {
    if self.last_update_timestamp + self.max_staleness_allowed < Clock::get()?.unix_timestamp {
        return Err(SaleError::StalePriceData.into());
    }

    if mint == constants::DEPOSIT_MINT1 || mint == constants::DEPOSIT_MINT2 {
        return Ok(100_000);
    } else if mint == constants::DEPOSIT_MINT3 && mint == self.mint {
        return Ok(self.price);
    } else {
        return Err(SaleError::UnsupportedMint);
    }
}
```

# [L-09] 하드코딩된 스테이블코인 가격

`states.rs`에서 `get_token_price` 함수는 스테이블코인의 가격을 항상 1 USD로 가정합니다.
```rust
pub fn get_token_price(&self, mint: Pubkey) -> Result<u64> {
    if mint == constants::DEPOSIT_MINT1 || mint == constants::DEPOSIT_MINT2 {
        return Ok(100000);
    } else if mint == constants::DEPOSIT_MINT3 && mint == self.mint {
        return Ok(self.price);
    } else {
        return err!(SaleError::UnsupportedMint);
    }
}
```

이 하드코딩된 가격은 디페깅 이벤트 시 문제를 일으킬 수 있습니다. 예를 들어, 스테이블코인이 0.95로 디페깅되면 사용자는 여전히 스테이블코인을 기여하고 0.95 USD 대신 1 USD와 동일한 가치의 토큰을 할당받을 수 있습니다.

권장 사항:

스테이블코인에 대해서도 오라클 가격을 추가하는 것을 고려하십시오.
