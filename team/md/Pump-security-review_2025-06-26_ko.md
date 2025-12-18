# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**pump-fun/pump_transfer_hook** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Pump Transfer Hook 정보

이 Pump 프로그램은 화이트리스트 시스템이 있는 토큰 전송 후크(hook)를 구현하여 슈퍼 관리자가 전송 메타데이터를 초기화하고, 특정 지갑을 전송 화이트리스트에 추가하고, 화이트리스트 확인을 영구적으로 비활성화할 수 있도록 합니다. 이는 화이트리스트가 활성화인 동안 화이트리스트에 있는 계정만 전송을 수행할 수 있도록 강제하여 통제된 토큰 분배 메커니즘을 가능하게 합니다.

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

_검토 커밋 해시:_
- [042e8febfd07ac92f70dd2e2fa0fa378d2532c8c](https://github.com/pump-fun/pump_transfer_hook/tree/042e8febfd07ac92f70dd2e2fa0fa378d2532c8c)

_수정 검토 커밋 해시:_
- [620ff3eff8ce97d1bc958a07cb889580b39a34e5](https://github.com/pump-fun/pump_transfer_hook/tree/620ff3eff8ce97d1bc958a07cb889580b39a34e5)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `lib`

# 발견 사항

# [M-01] 모든 지갑이 임의의 민트(mint)에 대해 `super_admin`으로 자체 할당할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

임의의 서명자가 `initialize_extra_account_metas`에서 `super_admin`으로 허용되며 해당 서명자와 대상 민트 간의 관계를 확인하지 않습니다.

```
#[account(init, seeds = [TRANSFER_HOOK_STATE_SEED.as_bytes(), mint.key().as_ref()], ...)]
pub transfer_hook_state: Account<'info, TransferHookState>,
```

`mint.mint_authority` 또는 `mint.freeze_authority`에 대한 제약 조건이 없습니다.
추가 계정(예: 확장 권한)이 서명할 필요가 없습니다.
[TRANSFER_HOOK_STATE_SEED, mint]의 PDA가 초기화되면 다시 초기화할 수 없습니다.

언급된 점들로 인해, 누구든지 실제 토큰 팀보다 먼저 PDA를 초기화하고 화이트리스트를 비활성화한 다음 위험이나 큰 비용 없이 떠날 수 있습니다.

`toggle_off_transfer_whitelist`는 단방향이므로 복구 경로가 없습니다.

## 권장 사항

민트 권한 제어 뒤에 초기화를 게이트(Gate)하십시오.

```
pub struct InitializeExtraAccountMeta<'info> {
    #[account(constraint = mint.mint_authority == COption::Some(super_admin.key()).into())]
    pub mint: InterfaceAccount<'info, Mint>,
    #[account(mut, signer)]
    pub super_admin: Signer<'info>,
    // ...
}
```

# [L-01] 구조체에 필요하지만 활용되지 않는 토큰 프로그램

현재 `InitializeExtraAccountMeta` 구조체에는 다음 계정이 포함되어 있습니다.

```
pub struct InitializeExtraAccountMeta<'info> {
    #[account(mut)]
    pub super_admin: Signer<'info>,
    /// CHECK: ExtraAccountMetaList Account, must use these exact seeds
    #[account(mut, seeds=[b"extra-account-metas", mint.key().as_ref()], bump)]
    pub extra_account_meta_list: AccountInfo<'info>,
    pub mint: InterfaceAccount<'info, Mint>,
    #[account(init, seeds=[TRANSFER_HOOK_STATE_SEED.as_bytes(), mint.key().as_ref()], bump, payer=super_admin, space=8+ TransferHookState::SIZE)]
    pub transfer_hook_state: Account<'info, TransferHookState>,
    pub token_program: Interface<'info, TokenInterface>,
    pub associated_token_program: Program<'info, AssociatedToken>,
    pub system_program: Program<'info, System>,
}
```

문제는 `initialize_extra_account_metas_ix()` 인스트럭션에서 사용되는 유일한 프로그램이 `system_program`이므로 `associated_token_program` 및 `token_program`은 필요하지 않다는 것입니다. 런타임이 이들과 함께 아무 작업도 수행하지 않으므로 심각한 영향은 없지만 전체적인 가독성에 영향을 미칩니다.

# [L-02] 화이트리스트 항목 수정/제거 메커니즘 부족

현재 관리자만이 `transfer_hook_state` 화이트리스트에 주소를 추가할 수 있지만, 일단 추가된 주소를 제거하거나 수정할 수 있는 방법은 없습니다. 화이트리스트에 있는 주소가 악의적으로 변하면 토큰 전송을 수행하는 것을 막을 메커니즘이 없습니다.

```rust
pub fn add_whitelist_ix(
    ctx: Context<UpdateTransferWhitelist>,
    whitelist_address: Pubkey,
) -> Result<()> {
    let transfer_hook_state = &mut ctx.accounts.transfer_hook_state;

    if let Some(pos) = transfer_hook_state
        .whitelisted_wallets
        .iter()
        .position(|key| *key == Pubkey::default())
    {
        transfer_hook_state.whitelisted_wallets[pos] = whitelist_address;
    } else {
        return err!(TransferError::WhitelistFull);
    }

    Ok(())
}
```

화이트리스트 항목을 제거하거나 수정하는 메커니즘을 추가하는 것이 좋습니다.

# [L-03] `add_whitelist_ix`가 중복 주소를 확인하지 않음

`add_whitelist_ix` 인스트럭션은 토큰 화이트리스트에 새 주소를 추가하는 데 사용됩니다.
```rust
pub fn add_whitelist_ix(
    ctx: Context<UpdateTransferWhitelist>,
    whitelist_address: Pubkey,
) -> Result<()> {
    let transfer_hook_state = &mut ctx.accounts.transfer_hook_state;

    if let Some(pos) = transfer_hook_state
        .whitelisted_wallets
        .iter()
        .position(|key| *key == Pubkey::default())
    {
        transfer_hook_state.whitelisted_wallets[pos] = whitelist_address;
    } else {
        return err!(TransferError::WhitelistFull);
    }

    Ok(())
}
```
그러나 주소가 이미 추가되었는지 여부는 확인하지 않습니다. 결과적으로 중복 주소가 화이트리스트에 삽입될 수 있습니다. 또한 기존 주소를 제거하거나 교체할 메커니즘이 없으며 화이트리스트의 크기는 제한되어 있습니다. 관리자가 실수로 중복 주소를 추가하면 화이트리스트 슬롯이 낭비될 수 있습니다.

새로 추가된 화이트리스트 주소가 중복되지 않는지 확인한 후 삽입하는 것이 좋습니다.

# [L-04] 사전 후크(Pre-hook) 전송 기간으로 인해 무제한 토큰 이동 가능

`extra_account_meta_list` 계정이 초기화되기 전에 민트가 생성되면 전송 후크 확장이 아직 시행되지 않습니다. 해당 기간 동안 주조되거나 전송된 토큰은 화이트리스트를 우회하여 화이트리스트에 없는 지갑에 도달할 수 있습니다.

**권장 사항**
동일한 트랜잭션에서 민트를 생성하고 `extra_account_meta_list`를 초기화하고 TransferHook 프로그램을 설정하십시오(또는 세 단계가 모두 완료될 때까지 민트를 동결하십시오).

# [L-05] `TransferHookState` 계정이 8바이트 초과 할당됨

PDA는 다음과 같이 초기화됩니다.

```
#[account(init, space = 8 + TransferHookState::SIZE, …)]
```

하지만 `TransferHookState`에도 8바이트를 추가합니다.

```
impl TransferHookState {
    pub const SIZE: usize = 8 + Self::INIT_SPACE;
}
```

`#[account(init, …)]` 라인에 또 다른 "+ 8"을 추가하면 두 번째 헤더가 추가되므로 계정은 다음과 같이 생성됩니다.
8 (추가) + 8 (SIZE 내) + 673 (필드) = 689 바이트
필요한 것은 8 (헤더) + 673 (필드) = 681 바이트뿐입니다.
이러한 이유로 배포당 8바이트의 임대료가 낭비됩니다.

**권장 사항**
1. SIZE 상수로 계정을 초기화하십시오.

```
#[account(init, space = TransferHookState::SIZE, …)]
```

# [L-06] `SetAuthority`로 토큰 계정 주소 화이트리스팅 우회

`check_transfer_account()` 함수는 소스 토큰 계정의 주소(`source_token.key()`)를 `whitelisted_wallets` 배열과 비교하여 전송을 검증합니다.
즉, 후크는 계정을 제어하는 지갑이 아니라 계정 자체를 신뢰합니다.

SPL Token은 모든 계정의 소유자가 다음을 호출할 수 있도록 허용하기 때문입니다.
`SetAuthority { authority_type: AccountOwner, new_authority }`,
화이트리스트에 있는 계정은 단일 인스트럭션으로 소유권을 임의의 공개 키로 넘길 수 있습니다.
호출 후:
1. 계정 주소는 변경되지 않은 상태로 유지됩니다(여전히 화이트리스트에 포함됨).
2. 승인된 적이 없는 새 권한이 이제 후크가 수락할 전송에 서명할 수 있습니다.

사실상 한 명의 합법적인 사용자가 화이트리스트 상태를 공격자에게 위임하거나 판매하여 의도한 접근 제어를 우회할 수 있습니다. 추가 자금이나 권한이 필요하지 않으며 표준 SPL 인스트럭션만 있으면 됩니다.

**권장 사항**

자체 주소가 아닌 토큰 계정 소유자가 접근 권한을 확인하십시오.

```
let owner = ctx.accounts.source_token.owner;
require!(
 transfer_hook_state.whitelisted_wallets.contains(&owner),
    TransferError::UnauthorizedTransfer
);
```
