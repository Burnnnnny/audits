# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Pashov Audit Group**은 **LayerZero-Labs/monorepo** 저장소에 대한 시간 제한 보안 검토를 수행했으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Layer Zero 정보

LayerZero는 옴니체인, 상호 운용 가능한 애플리케이션 생성을 용이하게 하도록 설계된 불변의 메시징 프로토콜입니다. 범위에는 Solana용 Layer Zero V2가 포함되었습니다.

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

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [25c8b94370f63bc7b05d65f0471a2078779d3dc3](https://github.com/LayerZero-Labs/monorepo/tree/25c8b94370f63bc7b05d65f0471a2078779d3dc3)

_수정 검토 커밋 해시_ - [a2e5ad2e582f98bb32d27ff774476a051b201cc5](https://github.com/LayerZero-Labs/monorepo/tree/a2e5ad2e582f98bb32d27ff774476a051b201cc5)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `packages/layerzero-v2/solana/programs/blocked-messagelib/*`
- `packages/layerzero-v2/solana/programs/dvn/*`
- `packages/layerzero-v2/solana/programs/endpoint/*`
- `packages/layerzero-v2/solana/programs/executor/*`
- `packages/layerzero-v2/solana/programs/messagelib-interface/*`
- `packages/layerzero-v2/solana/programs/oft/*`
- `packages/layerzero-v2/solana/programs/pricefeed/*`
- `packages/layerzero-v2/solana/programs/uln/*`
- `packages/layerzero-v2/solana/programs/worker-interface/*`
- `packages/layerzero-v2/solana/libs/*`

# 발견 사항

# [H-01] 악의적인 행위자가 excuctor 및 ULN을 통해 메시지 전송을 방지할 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`Executor` 프로그램에는 `quote`를 호출할 때 확인되는 `acl`이 있습니다.

```rust
impl Quote<'_> {
    pub fn apply(ctx: &Context<Quote>, params: &QuoteExecutorParams) -> Result<u64> {
        require!(!ctx.accounts.executor_config.paused, ExecutorError::Paused);
        let config = &ctx.accounts.executor_config;

        config.acl.assert_permission(&params.sender)?;
-----------------
```

권한은 `SetDenylist` 함수를 통해 취소됩니다. 이 함수를 사용하면 호출자가 `deny_list` acl에 포함될 주소를 설정할 수 있습니다.

```rust
#[derive(Accounts)]
pub struct SetDenylist<'info> {
    pub owner: Signer<'info>,
    #[account(
        mut,
        seeds = [EXECUTOR_CONFIG_SEED],
        bump = config.bump
    )]
    pub config: Account<'info, ExecutorConfig>,
}

impl SetDenylist<'_> {
    pub fn apply(ctx: &mut Context<SetDenylist>, params: &SetDenylistParams) -> Result<()> {
        for i in 0..params.denylist.len() {
            ctx.accounts.config.acl.set_denylist(&params.denylist[i])?;
        }
        Ok(())
    }
}
```

액세스 제어가 없으며 모든 사용자가 이 함수를 호출할 수 있습니다.
악의적인 행위자는 executor quote 함수를 호출하는 데 사용되는 모든 `sender`(OApp)에 대한 액세스를 방지할 수 있습니다.

현재 `ULN`은 `send`에 대해 executor에게 지불할 때 `quote` 함수를 호출합니다.

```rust
fn pay_executor<'c: 'info, 'info>(
    uln: &Pubkey,
    payer: &AccountInfo<'info>,
    executor_config: &ExecutorConfig,
    dst_eid: u32,
    sender: &Pubkey,
    calldata_size: u64,
    options: Vec<LzOption>,
    accounts: &[AccountInfo<'info>], /* [executor_program, executor_config, price_feed_program,
                                      * price_feed_config] */
) -> Result<WorkerFee> {
    let fee =
        quote_executor(uln, executor_config, dst_eid, sender, calldata_size, options, accounts)?;
--------------
```

따라서 악의적인 행위자는 거부 목록을 업데이트하는 한 ULN을 통한 실행을 방지할 수 있습니다.

## 권장 사항

`config` 계정에 다음을 추가하는 것을 고려하십시오.

```rust
        has_one = owner,
```

# [M-01] `lz_receive_types` 반환 시 누락된 계정

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`lz_receive_types` 함수의 반환 값에 `account 8 - associated token program` 및 `account 9 - system program`이 누락되었습니다.

`lz_receive_types` 함수는 입력으로 `LzReceiveParams`에서 시작하여 `lz_receive`를 호출하는 데 필요한 계정을 추출하는 데 사용됩니다.

이러한 계정이 없으면 `lz_receive_types`에서 반환된 계정에 의존할 때 `lz_receive` 함수가 실패합니다.

```rust
File: programs/oft/src/instructions/lz_receive_types.rs#L70-L75

accounts.extend_from_slice(&[
    LzAccount { pubkey: receiver, is_signer: false, is_writable: false },
    LzAccount { pubkey: token_dest, is_signer: false, is_writable: true },
    LzAccount { pubkey: oft.token_mint, is_signer: false, is_writable: false },
    LzAccount { pubkey: oft.token_program, is_signer: false, is_writable: false },
]);
```

## 권장 사항

`lz_receive_types` 함수의 반환 값에 누락된 계정을 포함하십시오.

이렇게 하면 `lz_receive` 함수를 성공적으로 실행하는 데 필요한 모든 계정을 사용할 수 있습니다.

# [M-02] 안전하지 않은 초기화 함수

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

코드베이스 전반에 걸쳐 액세스 제어가 없는 많은 초기화 함수가 있습니다.
즉, 공격자가 실제 호출자보다 먼저 초기화할 수 있습니다.

이러한 초기화 함수 중 일부에는 관리자 키와 같이 호출 중에 설정되는 민감한 매개변수가 있습니다.
여기에는 다음이 포함됩니다.

1. `ULN::init_uln`
2. `pricefeed::init_price_feed`
3. `oft::init_oft`
4. `oft::init_adapter_oft`
5. `executor::init_executor`
6. `endpoint::init_endpoint`
7. `dvn::init_dvn`

다음은 액세스 제어가 없는 ULN 초기화의 예입니다.

```rust
#[instruction(params: InitUlnParams)]
pub struct InitUln<'info> {
    #[account(mut)]
    pub payer: Signer<'info>,
    #[account(
        init,
        payer = payer,
        space = 8 + UlnSettings::INIT_SPACE,
        seeds = [ULN_SEED],
        bump
    )]
    pub uln: Account<'info, UlnSettings>,
    pub system_program: Program<'info, System>,
}

impl InitUln<'_> {
    pub fn apply(ctx: &mut Context<InitUln>, params: &InitUlnParams) -> Result<()> {
        ctx.accounts.uln.eid = params.eid;
        ctx.accounts.uln.endpoint = params.endpoint;
        ctx.accounts.uln.endpoint_program = params.endpoint_program;
        ctx.accounts.uln.admin = params.admin;
        ctx.accounts.uln.treasury = None;
        ctx.accounts.uln.treasury_fee_cap = params.treasury_fee_cap;
        ctx.accounts.uln.treasury_admin = params.treasury_admin;
        ctx.accounts.uln.bump = ctx.bumps.uln;
        Ok(())
    }
}
```

이러한 초기화 함수는 프로그램 배포 시 직접 호출할 수 없으므로 공격자는 배포자보다 먼저 호출하고 임의의 매개변수(관리자 키 포함)를 설정할 수 있습니다. 이는 배포자가 알아차리지 못할 수 있으며 해커가 프로그램에 대한 권한을 얻거나 알아차리고 프로그램이 다시 배포될 수 있습니다(아마도 여러 번).

## 권장 사항

배포자 또는 기타 알려진 주소는 초기화 함수에서 확인되어야 합니다.
Anchor 문서는 `upgrade_authority_address`를 사용할 것을 제안합니다.
[링크](https://docs.rs/anchor-lang/latest/anchor_lang/accounts/account/struct.Account.html#example-1)

# [L-01] 안전하지 않은 권한 이전 기능

제공된 코드 조각은 솔라나 프로그램에서 권한 이전 기능의 안전하지 않은 구현을 보여줍니다. TransferAdmin 구조체 및 관련 apply 함수를 사용하면 현재 관리자가 추가적인 보호 장치나 유효성 검사 없이 관리자 역할을 새 주소로 직접 이전할 수 있습니다.

취약점은 현재 관리자가 제안된 새 관리자의 동의나 수락 없이 임의의 주소로 관리자 역할을 일방적으로 이전할 수 있다는 사실에 있습니다. 적절한 2단계 이전 프로세스가 없으면 프로그램이 다음과 같은 위험에 노출됩니다.

**우발적 이전:** 현재 관리자가 실수로 관리자 역할을 잘못되거나 의도하지 않은 주소로 이전하여 프로그램에 대한 통제력을 잃을 수 있습니다.
**악의적 탈취:** 현재 관리자의 개인 키를 손상시킨 공격자는 이 기능을 악용하여 관리자 역할을 자신의 통제하에 있는 주소로 이전하여 사실상 프로그램을 장악할 수 있습니다.
**동의 부족:** 제안된 새 관리자는 권한 이전을 수락하거나 거부할 수 없으므로 명시적 동의 원칙을 위반하고 예상치 못한 책임이나 부채로 이어질 수 있습니다.

```rust
impl TransferAdmin<'_> {
    pub fn apply(ctx: &mut Context<TransferAdmin>, params: &TransferAdminParams) -> Result<()> {
        ctx.accounts.oft_config.admin = params.admin;
        Ok(())
    }
}
```

**_참고: 이 문제는 모든 권한 이전에 존재합니다._**

이 취약점을 완화하고 안전하고 합의된 권한 이전 프로세스를 보장하려면 2단계 이전 메커니즘을 구현하는 것이 좋습니다.

**현재 관리자에 의한 지명:**

1. 현재 관리자가 새 관리자 주소를 지명할 수 있도록 nominate_new_admin과 같은 새로운 명령어를 도입합니다.
2. 지명된 주소를 저장할 pending_admin 필드를 포함하도록 OftConfig 구조체를 업데이트합니다.
3. OftConfig 계정의 pending_admin 필드를 설정하도록 nominate_new_admin 침을 수정합니다.

**지명된 관리자에 의한 수락:**

1. 지명된 관리자가 권한 이전을 명시적으로 수락할 수 있도록 accept_admin_role과 같은 또 다른 명령어를 도입합니다.
2. accept_admin_role 지침에서 서명자가 pending_admin인지 확인하고 OftConfig 계정의 admin 필드를 pending_admin 주소로 업데이트합니다.
3. pending_admin 필드를 지워 이전 프로세스가 완료되었음을 나타냅니다.

# [L-02] 설정자(Setter) 함수의 이벤트 누락

주어진 솔라나 프로그램에는 구성 설정이나 매개변수와 같은 중요한 프로그램 상태 변수를 수정하는 함수가 있습니다. 그러나 이러한 상태 수정 함수는 프로그램 상태 변경을 알리는 이벤트를 방출하지 않습니다.

코드 위치:

- [set_dst_config.rs#L21](https://github.com/LayerZero-Labs/monorepo/blob/fa653ef3942a3021c251f4c3e985723f1e5d1cb9/packages/layerzero-v2/solana/programs/executor/src/instructions/admin/set_dst_config.rs#L21)
- [set_price_feed.rs#L18](https://github.com/LayerZero-Labs/monorepo/blob/fa653ef3942a3021c251f4c3e985723f1e5d1cb9/packages/layerzero-v2/solana/programs/executor/src/instructions/admin/set_price_feed.rs#L18)
- [set_supported_option_types.rs#L21](https://github.com/LayerZero-Labs/monorepo/blob/fa653ef3942a3021c251f4c3e985723f1e5d1cb9/packages/layerzero-v2/solana/programs/executor/src/instructions/admin/set_supported_option_types.rs#L21)

누락된 이벤트 문제를 해결하려면 상태 수정 함수에 이벤트 방출을 추가하는 것이 좋습니다.

# [L-03] Solana OFT 프로그램의 버전 관리 누락

[lib.rs](https://github.com/LayerZero-Labs/monorepo/blob/25c8b94370f63bc7b05d65f0471a2078779d3dc3/packages/layerzero-v2/solana/programs/oft/src/lib.rs)에 위치한 Solana OFT 프로그램에는 EVM 대응 프로그램에 비해 버전 관리 기능이 부족합니다.

LayerZero OFT용 EVM 스마트 계약에서 버전 관리는 계약 업그레이드를 관리하고 하위 호환성을 보장하기 위해 구현됩니다. 버전 관리를 통해 계약 개발자는 새로운 기능을 도입하고 버그를 수정하거나 개선하는 동시에 계약의 다른 버전 간의 명확한 구분을 유지할 수 있습니다.

그러나 Solana OFT 프로그램에는 명시적인 버전 관리 메커니즘이 포함되어 있지 않습니다.

Solana OFT 프로그램의 누락된 버전 관리 문제를 해결하려면 EVM 스마트 계약에서 사용되는 것과 유사한 버전 관리 메커니즘을 구현하는 것이 좋습니다.

# [L-04] `signers_setter` 함수에서 고유 서명자 확인 누락

DVM 프로그램을 초기화할 때 프로그램에 고유한 서명자만 추가되도록 하여 고유하지 않은 서명자로 인한 운영 문제를 방지하는 확인이 관찰되었습니다. 그러나 서명자를 업데이트하기 위해 DVM 권한으로 실행할 수 있는 `signers_setter` 함수에는 유사한 확인이 누락되었습니다. 이 확인이 없으면 고유하지 않은 서명자가 추가되어 프로그램이 작동하지 않을 수 있습니다.

DVM 프로그램을 초기화하는 동안 다음 코드는 고유한 서명자만 추가되도록 합니다.

```rust
impl InitDvn<'_> {
    pub fn apply(ctx: &mut Context<InitDvn>, params: &InitDvnParams) -> Result<()> {
        assert_unique_signers(&params.signers)?;
        require!(
            params.quorum > 0 && params.quorum as usize <= params.signers.len(),
            DvnError::InvalidQuorum
        );
        // Initialization logic continues...
    }
}
```

이 검사는 서명자 벡터에 중복 서명자가 포함되는 것을 방지하며, 이는 프로그램의 운영 무결성에 매우 중요합니다.

그러나 서명자를 업데이트하는 데 사용되는 SetSigners 함수에는 고유 서명자에 대한 확인이 누락되어 있습니다.

```rust
impl SetSigners<'_> {
    pub fn apply(ctx: &mut Context<SetSigners>, params: &SetSignersParams) -> Result<()> {
        //@audit-issue they should check if signers do not overlap - same as they did in init
        ctx.accounts.config.multisig.signers = params.signers.clone();
        Ok(())
    }
}

```

`SetSigners` 함수에 `assert_unique_signers` 확인을 추가합니다.

# [L-05] 크로스 체인 통신의 확장성 과제

LayerZero의 Solana 구현에서 프로그램 이벤트는 Anchor의 CPI 기반 로그 메시지를 사용하여 방출됩니다. 이 접근 방식에는 직렬화된 이벤트를 명령어 데이터로 사용하여 프로그램에 대한 자체 CPI 호출을 수행하여 트랜잭션에 유지되고 필요에 따라 나중에 가져올 수 있도록 합니다.
이 방법은 이벤트 데이터를 저장하고 검색하는 방법을 제공하지만 몇 가지 제한 사항이 있습니다.

- 제한된 메시지 크기: CPI 호출의 크기는 약 1.2kB로 제한되어 방출된 로그 메시지에 포함될 수 있는 데이터 양이 제한됩니다. 이는 더 큰 메시지 페이로드가 필요할 수 있는 다른 체인과의 브리지 상호 운용성에 영향을 미칠 수 있습니다.
- 호출 스택 깊이 제약: 로그 메시지 방출을 위한 자체 CPI 호출 사용은 호출 스택 깊이에 기여하며, 이는 Solana의 런타임 보안 조치에 의해 제한됩니다. 이는 더 깊은 호출 스택 수준이 필요할 수 있는 다른 프로그램 및 계약과의 브리지 상호 운용성을 제한할 수 있습니다.

이러한 제한 사항은 저장 및 전송할 수 있는 로그 메시지 데이터의 양을 제한하고 브리지가 광범위한 계약 및 체인과 통합하는 능력을 제한하므로 LayerZero의 Solana 구현에 확장성 문제를 제기합니다.

확장성 및 상호 운용성 문제를 해결하려면 로그 메시지 내용을 저장하기 위한 대체 접근 방식을 모색하는 것이 좋습니다. 잠재적인 해결책 중 하나는 [결정적 주소의 전용 스토리지 계정을 사용하여 로그 메시지 데이터를 저장](https://developers.circle.com/stablecoins/docs/solana-programs#program-events)하는 것입니다.

# [L-06] `required_dvns` 및 `optional_dvns`에서 중복 확인 누락

`init_default_config` 명령어에는 `UlnConfig` 내의 `required_dvns` 및 `optional_dvns` 벡터에 중복 항목에 대한 확인이 없습니다. 이러한 간과로 인해 페이로드 확인에 대한 이중 투표가 발생하는 중복 DVN이 발생할 수 있습니다.

이 문제를 해결하려면 기본 구성을 설정하기 전에 `required_dvns` 및 `optional_dvns` 모두에서 중복 `Pubkey` 항목을 확인하고 방지하는 유효성 검사 단계를 구현하십시오. 이는 데이터 무결성을 유지하고 중복 구성과 관련된 잠재적 문제를 피하는 데 도움이 될 수 있습니다.

# [L-07] 엔드포인트 모듈의 전송 명령어에서 PDA 계정 사칭 가능성

## 설명

`simplemessage` 라이브러리에서 `SendWithLzToken` 명령어는 다음과 같은 계정을 수락합니다.

```rust
    pub endpoint: Signer<'info>,
    pub message_lib: Account<'info, MessageLib>,
    pub message_lib_lz_token: InterfaceAccount<'info, TokenAccount>,
    pub payer: Signer<'info>,
…
```

그리고 CPI는 여기에서 수행됩니다(`packages/layerzero-v2/solana/programs/endpoint/src/instructions/oapp/send.rs:87`).

```rust
        let seeds: &[&[&[u8]]] =
            &[&[MESSAGE_LIB_SEED, send_library.as_ref(), &[ctx.accounts.send_library_info.bump]]];
        let cpi_ctx = CpiContext::new_with_signer(
            ctx.accounts.send_library_program.to_account_info(),
            messagelib_interface::cpi::accounts::Interface {
                endpoint: ctx.accounts.send_library_info.to_account_info(),
            },
            seeds,
        )
        .with_remaining_accounts(ctx.remaining_accounts.to_vec());
```

이러한 방식으로 검증되는 유일한 계정은 `endpoint`(CPI 호출에도 서명함)입니다. CPI 호출의 다른 계정은 Anchor의 나머지 계정을 사용하여 발신자가 제공합니다. 이러한 나머지 계정은 엔드포인트와 다양한 `msglibraries` 간의 구성 가능성을 유지하기 위해 검증되지 않으므로 엔드포인트 계정(`send_library_info`)을 사칭할 가능성이 열립니다. 결과적으로 엔드포인트 계정이 지불인과 동일한 상태에서 엔드포인트의 `send` 명령어를 속여 `SendWithLzToken`에 대한 CPI 호출을 생성할 수 있습니다.

Anchor에 다음과 같은 컨텍스트가 있더라도:

```rust
pub endpoint: Signer<'info>,
pub payer: Signer<'info>,
```

엔드포인트와 지불인이 동일한 계정인 경우 하나의 서명으로 트랜잭션에 서명하기에 충분합니다.

엔드포인트 PDA가 램포트와 토큰을 보유하고 있지 않으므로 심각한 문제는 아닙니다. 하지만 여전히 프로세스 무결성을 깨뜨릴 수 있습니다. 엔드포인트 PDA에 대한 토큰 계정을 만들고 이 계정으로 토큰을 보낸 다음 엔드포인트 전송 지침에 이 계정을 제공할 수 있습니다. 따라서 시스템 참가자는 토큰이 엔드포인트가 소유한 계정에서 전송되었다고 생각하도록 속일 수 있습니다.

이러한 시나리오에서:

1. PDA 계정의 소유자만 램포트를 전송할 수 있으므로 기본 램포트 전송은 실패합니다.
2. `messagelib` PDA가 소유한 계정에서 토큰 전송이 발생할 수 있으므로 토큰 전송은 성공할 수 있습니다.
3. 이 취약점은 악의적인 트랜잭션을 실행하는 데 악용될 수 있으며, 마치 엔드포인트(PDA)가 트랜잭션 비용을 지불하는 것처럼 보이게 합니다.

해당 악용 시나리오:

1. 사용자가 PDA용 토큰 계정을 생성합니다.
2. 사용자가 PDA 토큰 계정으로 토큰을 보냅니다.
3. 사용자는 사칭 취약점을 악용하여 PDA 토큰 계정을 사용하여 지불하고, 마치 PDA(엔드포인트)가 승인한 것처럼 트랜잭션을 효과적으로 실행합니다.

**위치**
[send.rs](packages/layerzero-v2/solana/programs/endpoint/src/instructions/oapp/send.rs)
[send_with_lz_token.rs](packages/layerzero-v2/solana/programs/simple-messagelib/src/instructions/endpoint/send_with_lz_token.rs)

## 권장 사항

이 문제를 완화하려면 다음을 수행하는 것이 좋습니다.

1. `SendWithLzToken`에서 엔드포인트와 지불인이 다른 계정인지 확인하는 검사를 만듭니다.
2. 사용자 지정 `messagelib` 생성에 대한 설명서를 업데이트하여 이 공격 벡터 및 필요한 검사에 대한 참고 사항을 포함합니다.
