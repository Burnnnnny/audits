# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Pashov Audit Group**은 **LayerZero-Labs/monorepo/packages/layerzero-v2/solana/programs/oft** 저장소에 대한 시간 제한 보안 검토를 수행했으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Layer Zero 정보

LayerZero는 옴니체인, 상호 운용 가능한 애플리케이션 생성을 용이하게 하도록 설계된 불변의 메시징 프로토콜입니다. 범위에는 Solana용 Layer Zero OFT V2가 포함되었습니다.

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

_검토 커밋 해시_ - [aa003678c6e6fac40a503bb81113620aefce9dd6](https://github.com/LayerZero-Labs/monorepo/commit/aa003678c6e6fac40a503bb81113620aefce9dd6)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `init_oft`
- `lz_receive`
- `lz_receive_types`
- `mod`
- `quote_oft`
- `quote_send`
- `send`
- `set_oft_config`
- `set_pause`
- `set_peer_config`
- `withdraw_fee.rs`
- `oft`
- `peer_config`
- `compose_msg_codec`
- `errors`
- `events`
- `lib`
- `msg_codec`

# 발견 사항

# [M-01] 민트 10진수 조작

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`ld2sd_rate`(로컬 대 공유 소수점 비율)는 초기자가 Solana 프로그램의 `MintCloseAuthority` 확장 기능을 악용하여 조작할 수 있습니다. 이러한 조작은 초기자가 민트 생성 후 변경할 수 있는 민트의 소수점 값에 대한 제어 권한을 가지고 있기 때문에 토큰 회계에서 치명적인 불일치와 잠재적 금융 악용으로 이어질 수 있습니다.

이 악용을 실행하는 과정은 다음과 같습니다.

1. 관리자는 `MintCloseAuthority` 확장을 사용하여 초기 소수점 값이 18인 민트를 생성하고 닫기 권한을 자신이 제어하는 주소에 할당합니다.
2. 관리자는 `InitOFT`에서 민트를 사용하여 `ld2sd_rate` 값으로 1e12(6개의 공유 소수점 가정)를 얻습니다.
3. 민트 공급량이 여전히 0인 상태에서 관리자는 닫기 권한을 사용하여 민트 계정을 닫습니다.
4. 민트가 닫힌 후 관리자는 동일한 주소에 새 민트를 다시 초기화할 수 있지만 이번에는 6과 같은 감소된 소수점 값을 사용합니다.

이러한 소수점 값 조작으로 인해 프로그램(OFT)은 여전히 민트를 18개의 소수점이 있는 것처럼 취급하지만 실제로는 6개의 소수점만 사용하므로 `ld2sd_rate`가 부풀려집니다. 이러한 불일치는 잘못된 토큰 계산으로 이어지며 다양한 금융 악용에 사용될 수 있습니다.

### 악용 예시:

- **정당한 전송이 먼지(dust)로 취급됨:**
   1 토큰이 `1e6` 단위(소수점 6)와 같은 1달러 가치의 토큰을 가정합니다. 관리자 또는 공격자는 100,000 토큰(총 가치 1,000,000달러)을 보낼 수 있습니다. 조작된 `ld2sd_rate`를 사용하면 프로그램은 `1e12`의 부풀려진 비율을 사용하여 전체 금액인 `1e11`(1,000억 단위)을 `remove_dust`로 인한 먼지로 취급합니다. 즉, 토큰이 의도한 대로 전송되지 않습니다.

- **크로스 체인 조작:**
   공격자는 다른 토큰 에스크로 계정으로 다른 `OFTStore`를 초기화하고 조작된 `ld2sd_rate`를 사용하여 다른 체인(예: 이더리움)에서 솔라나로 토큰을 전송할 수 있습니다. 솔라나의 부풀려진 비율로 인해 수령한 금액이 의도한 것보다 훨씬 높습니다. 토큰이 전송되면 공격자는 정확한(낮은) 비율을 사용하여 피어를 새 `OFTStore`로 전환하고 토큰을 원래 체인으로 다시 전송하여 `OFTStore` 계정 간의 비율 불일치로 인해 차익 거래와 같은 이점을 얻습니다.

```rust
use crate::*;
use anchor_spl::token_interface::{Mint, TokenAccount, TokenInterface};
use oapp::endpoint::{instructions::RegisterOAppParams, ID as ENDPOINT_ID};

#[derive(Accounts)]
pub struct InitOFT<'info> {
    #[account(mut)]
    pub payer: Signer<'info>,
    #[account(
        init,
        payer = payer,
        space = 8 + OFTStore::INIT_SPACE,
        seeds = [OFT_SEED, token_escrow.key().as_ref()],
        bump
    )]
    pub oft_store: Account<'info, OFTStore>,
    #[account(
        init,
        payer = payer,
        space = 8 + LzReceiveTypesAccounts::INIT_SPACE,
        seeds = [LZ_RECEIVE_TYPES_SEED, oft_store.key().as_ref()],
        bump
    )]
    pub lz_receive_types_accounts: Account<'info, LzReceiveTypesAccounts>,
    #[account(mint::token_program = token_program)]
    pub token_mint: InterfaceAccount<'info, Mint>,
    #[account(
        init,
        payer = payer,
        token::authority = oft_store,
        token::mint = token_mint,
        token::token_program = token_program,
    )]
    pub token_escrow: InterfaceAccount<'info, TokenAccount>,
    pub token_program: Interface<'info, TokenInterface>,
    pub system_program: Program<'info, System>,
}

impl InitOFT<'_> {
    pub fn apply(ctx: &mut Context<InitOFT>, params: &InitOFTParams) -> Result<()> {
        // Initialize the oft_store
        ctx.accounts.oft_store.oft_type = params.oft_type.clone();
        require!(
            ctx.accounts.token_mint.decimals >= params.shared_decimals,
            OFTError::InvalidDecimals
        );
    }
```

현재 구현은 `MintCloseAuthority`가 비활성화되어 있는지 확인하거나 보장하지 않습니다. 공격자는 초기화 후 비율이 조정되지 않으므로 이전 민트 계정을 닫은 후 민트 계정을 다시 초기화할 수 있습니다.

## 권장 사항

이 문제를 완화하려면 다음을 강력히 권장합니다.

1. `InitOFT` 지침에 `MintCloseAuthority` 확장이 활성화되지 않았는지 확인하는 검사를 추가합니다.
2. 초기화 중에 민트의 닫기 권한이 명시적으로 `None`으로 설정되어 있는지 확인합니다.

이렇게 하면 `MintCloseAuthority`의 악용을 방지하고 생성 후 민트의 소수점 값이 조작되지 않도록 하여 `ld2sd_rate`가 부풀려지는 것을 방지할 수 있습니다.

# [L-01] OFT 인코딩에서 메시지 크기 유효성 검사 부족

OFT 구현에는 인코딩 함수의 `compose_msg` 매개변수에 대한 명시적인 크기 검사가 없습니다. 이로 인해 잠재적으로 크기가 큰 메시지가 생성되어 크로스 체인 통신 및 솔라나 트랜잭션 처리에 문제가 발생할 수 있습니다.

영향을 받는 코드는 주로 `msg_codec.rs` 및 `compose_msg_codec.rs` 파일에 있습니다.

```rust
// In msg_codec.rs
pub fn encode(
    send_to: [u8; 32],
    amount_sd: u64,
    sender: Pubkey,
    compose_msg: &Option<Vec<u8>>,
) -> Vec<u8> {
    if let Some(msg) = compose_msg {
        let mut encoded = Vec::with_capacity(72 + msg.len()); // 32 + 8 + 32
        encoded.extend_from_slice(&send_to);
        encoded.extend_from_slice(&amount_sd.to_be_bytes());
        encoded.extend_from_slice(sender.to_bytes().as_ref());
        encoded.extend_from_slice(&msg);
        encoded
    } else {
        // ... (omitted for brevity)
    }
}

// In compose_msg_codec.rs
pub fn encode(
    nonce: u64,
    src_eid: u32,
    amount_ld: u64,
    compose_msg: &Vec<u8>, // [composeFrom][composeMsg]
) -> Vec<u8> {
    let mut encoded = Vec::with_capacity(20 + compose_msg.len()); // 8 + 4 + 8
    encoded.extend_from_slice(&nonce.to_be_bytes());
    encoded.extend_from_slice(&src_eid.to_be_bytes());
    encoded.extend_from_slice(&amount_ld.to_be_bytes());
    encoded.extend_from_slice(&compose_msg);
    encoded
}
```

[oft/src/msg_codec.rs:L7](https://github.com/LayerZero-Labs/monorepo/blob/aa003678c6e6fac40a503bb81113620aefce9dd6/packages/layerzero-v2/solana/programs/oft/src/msg_codec.rs#L7)

[oft/src/compose_msg_codec.rs:L7](https://github.com/LayerZero-Labs/monorepo/blob/aa003678c6e6fac40a503bb81113620aefce9dd6/packages/layerzero-v2/solana/programs/oft/src/compose_msg_codec.rs#L7)

# [L-02] 일관성 없는 `compose_msg` 구현

OFT 구현에는 `msg_codec.rs` 및 `compose_msg_codec.rs`에 위치한 `compose_msg` 함수의 두 가지 다른 버전이 포함되어 있습니다. 이러한 함수는 입력 바이트 배열에서 구성된 메시지의 추출을 처리합니다. 이러한 구현 간의 불일치는 특정 시나리오에서 잠재적으로 자동 실패 및 예상치 못한 동작으로 이어질 수 있습니다.

`msg_codec.rs`의 구현은 다음과 같습니다.

```rust
pub fn compose_msg(message: &[u8]) -> Option<Vec<u8>> {
    if message.len() > COMPOSE_MSG_OFFSET {
        Some(message[COMPOSE_MSG_OFFSET..].to_vec())
    } else {
        None
    }
}
```

[oft/src/msg_codec.rs:L40](https://github.com/LayerZero-Labs/monorepo/blob/aa003678c6e6fac40a503bb81113620aefce9dd6/packages/layerzero-v2/solana/programs/oft/src/msg_codec.rs#L40)

이 구현은 안전하게 `Option<Vec<u8>>`을 반환하여 호출자가 메시지가 너무 짧은 경우를 명시적으로 처리할 수 있도록 합니다.

그러나 `compose_msg_codec.rs`의 구현은 다음과 같습니다.

```rust
pub fn compose_msg(message: &[u8]) -> Vec<u8> {
    if message.len() > COMPOSE_MSG_OFFSET {
        message[COMPOSE_MSG_OFFSET..].to_vec()
    } else {
        Vec::new()
    }
}
```

[oft/src/compose_msg_codec.rs:L45](https://github.com/LayerZero-Labs/monorepo/blob/aa003678c6e6fac40a503bb81113620aefce9dd6/packages/layerzero-v2/solana/programs/oft/src/compose_msg_codec.rs#L45)

이 버전은 메시지가 너무 짧을 때 빈 `Vec`을 반환하므로 호출자가 적절하게 처리하지 않으면 자동 실패가 발생할 수 있습니다.

이 불일치의 영향은 메인 프로그램 로직이 주로 `msg_codec.rs`의 더 안전한 구현을 사용한다는 사실로 인해 완화됩니다. 그러나 `compose_msg_codec.rs`에 잠재적으로 문제가 있는 구현이 존재하면 특히 향후 수정 사항이 실수로 이 버전을 사용하는 경우 예상치 못한 동작의 위험이 발생합니다.

코드베이스 전체에서 `compose_msg` 함수 구현을 표준화하십시오. `compose_msg_codec.rs`의 구현을 `Option<Vec<u8>>`을 반환하는 더 안전한 버전으로 교체하십시오.

# [L-03] 향후 업그레이드를 위한 유연한 계정 크기 조정 및 데이터 재할당 부족

**설명**

프로토콜은 현재 계정에 향후 업데이트를 위한 충분한 공간이 있는지 확인하는 적절한 검사 없이 하드코딩된 계정 크기를 사용합니다. 또한 솔라나가 지원하는 잠재적인 계정 데이터 재할당을 처리하지 않습니다. 이러한 유연성 부족으로 인해 향후 계정 구조 업그레이드가 복잡해질 수 있습니다.

예를 들어 `init_oft` 함수에서:

```rs
    #[account(mut)]
    pub payer: Signer<'info>,
    #[account(
        init,
        payer = payer,
        space = 8 + OFTStore::INIT_SPACE,
        seeds = [OFT_SEED, token_escrow.key().as_ref()],
        bump
    )]
    pub oft_store: Account<'info, OFTStore>,
```

계정에 대해 일정한 공간을 사용하면 향후 업데이트에서 계정을 수정하거나 확장하기 어렵습니다.

**권장 사항**

향후 프로토콜 업데이트를 허용하기 위해 PDA(Program Derived Accounts)에 패딩 공간을 추가하십시오. 이렇게 하면 나중에 복잡한 마이그레이션이나 재할당 없이 변경 사항을 처리할 수 있는 유연성이 프로토콜에 제공됩니다.

예를 들어 다음과 같이 공간 할당을 늘릴 수 있습니다.

```diff
-        space = 8 + OFTStore::INIT_SPACE,
+        space = 8 + OFTStore::INIT_SPACE + PADDING_SPACE,
```

여기서 `PADDING_SPACE`는 계정 구조의 향후 필드나 변경 사항을 수용하기 위해 추가된 추가 버퍼입니다.

# [L-04] 피어 주소에 대한 유효성 검사 부족

OFT는 크로스 체인 토큰 전송 중 피어 주소에 대한 적절한 유효성 검사가 부족합니다. 구체적으로 `send` 함수에서 `peer_address`는 대상 엔드포인트 ID(`dst_eid`)와의 대응 관계를 확인하지 않고 피어 구성에서 직접 사용됩니다.

```rust
	let msg_receipt = oapp::endpoint_cpi::send(
    ctx.accounts.oft_store.endpoint_program,
    ctx.accounts.oft_store.key(),
    ctx.remaining_accounts,
    &[OFT_SEED, ctx.accounts.token_escrow.key().as_ref(), &[ctx.accounts.oft_store.bump]],
    EndpointSendParams {
        dst_eid: params.dst_eid,
        receiver: ctx.accounts.peer.peer_address,
        message: msg_codec::encode(
            params.to,
            amount_sd,
            ctx.accounts.signer.key(),
            &params.compose_msg,
        ),
        // ... other parameters ...
    },
)?;
```

[oft/src/instructions/send.rs#L129](https://github.com/LayerZero-Labs/monorepo/blob/aa003678c6e6fac40a503bb81113620aefce9dd6/packages/layerzero-v2/solana/programs/oft/src/instructions/send.rs#L129)

`peer` 계정은 `dst_eid`에 따라 로드되지만 저장된 `peer_address`가 주어진 `dst_eid`에 대해 실제로 올바른지 확인하는 명시적인 검사는 없습니다.

전송을 시작하기 전에 주어진 `dst_eid`에 대한 `peer_address`의 정확성을 확인하기 위해 `send` 함수에 유효성 검사 메커니즘을 구현하십시오.

# [L-05] 수수료 인출에 대한 최소 임계값 확인 부족

`withdraw_fee` 함수에서 인출된 금액이 사용 가능한 수수료 잔액을 초과하지 않도록 하는 검사가 있지만 최소 임계값은 적용되지 않습니다.

```rust
pub fn apply(ctx: &mut Context<WithdrawFee>, params: &WithdrawFeeParams) -> Result<()> {
    require!(
        ctx.accounts.token_escrow.amount - ctx.accounts.oft_store.tvl_ld >= params.fee_ld,
        OFTError::InvalidFee
    );
    // ... rest of the function
}
```

[oft/src/instructions/withdraw_fee.rs:L36](https://github.com/LayerZero-Labs/monorepo/blob/aa003678c6e6fac40a503bb81113620aefce9dd6/packages/layerzero-v2/solana/programs/oft/src/instructions/withdraw_fee.rs#L36)

수수료 부과 방식의 경우 관리자는 수수료를 공제한 후 0(또는 너무 낮은) 금액을 받을 수 있습니다. 이는 수수료 인출이 프로그램이나 클라이언트 측 봇에 의해 자동화될 때 발생할 가능성이 큽니다.

슬리피지 보호를 추가하거나 최소 임계값을 적용하는 것을 고려하십시오.

# [L-06] 대규모 거래 지배에 대한 속도 제한기 취약성

속도 제한기는 특히 솔라나의 높은 처리량 환경에서 대규모 거래에 의해 지배되기 쉽습니다. 속도 제한기는 거래 규모나 사용자 공정성을 고려하지 않고 선착순으로 작동하므로 속도 제한이 급격히 고갈되고 불공정한 리소스 할당이 발생할 수 있습니다.

영향을 받는 코드:

```rust
// In peer_config.rs
pub fn try_consume(&mut self, amount: u64) -> Result<()> {
    self.refill(0)?;
    match self.tokens.checked_sub(amount) {
        Some(new_tokens) => {
            self.tokens = new_tokens;
            Ok(())
        },
        None => Err(error!(OFTError::RateLimitExceeded)),
    }
}

// In send.rs
if let Some(rate_limiter) = ctx.accounts.peer.outbound_rate_limiter.as_mut() {
    rate_limiter.try_consume(amount_received_ld)?;
}
```

[oft/src/state/peer_config.rs:L52](https://github.com/LayerZero-Labs/monorepo/blob/aa003678c6e6fac40a503bb81113620aefce9dd6/packages/layerzero-v2/solana/programs/oft/src/state/peer_config.rs#L52)

[oft/src/instructions/send.rs:L68](https://github.com/LayerZero-Labs/monorepo/blob/aa003678c6e6fac40a503bb81113620aefce9dd6/packages/layerzero-v2/solana/programs/oft/src/instructions/send.rs#L68)

**잠재적 흐름:**

1. 사용자가 속도 제한의 상당 부분을 소비하는 대규모 거래(즉, 높은 금액)를 시작합니다.
2. 솔라나의 높은 처리량으로 인해 여러 사용자가 거의 동시에 유사한 대규모 거래를 시도할 수 있습니다.
3. 처리된 첫 번째 거래는 속도 제한의 대부분 또는 전부를 고갈시킵니다.
4. 후속 거래는 크기에 관계없이 속도 제한 토큰이 부족하여 실패합니다.
5. 속도 제한은 시간이 지남에 따라 리필되지만 다음 대규모 거래로 주기가 반복될 수 있습니다.

**잠재적 영향:**

- 대규모 거래가 브리지의 용량을 독점하여 소규모 거래가 처리되지 못하게 할 수 있습니다.
- 잠재적 서비스 거부: 일련의 대규모 거래가 속도 제한을 지속적으로 고갈시켜 사실상 다른 사용자에 대한 서비스를 거부할 수 있습니다.

다양한 거래 크기 범위(금액)에 대해 별도의 버킷이 있는 토큰 버킷 알고리즘을 구현하십시오. 이렇게 하면 대규모 거래와 소규모 거래 모두 할당된 용량을 확보하여 대규모 거래가 전체 속도 제한을 지배하는 것을 방지할 수 있습니다.

# [L-07] 솔라나 재시작에 취약한 중요 OFT 설정

OFTStore 구조에는 솔라나 체인 재시작에 취약한 중요 설정이 포함되어 있습니다. 구체적으로 `paused` 및 `default_fee_bps` 필드는 이전 상태로 되돌아갈 수 있습니다.

```rust
pub struct OFTStore {
    // ... other fields ...
    pub default_fee_bps: u16,
    pub paused: bool,
    // ... other fields ...
}
```

솔라나가 이전 슬롯으로 다시 시작하는 경우:

1. `paused` 필드가 true에서 false로 되돌아가 보안상의 이유로 일시 중지해야 할 때 시스템이 다시 활성화될 수 있습니다. 이는 감지된 취약점이나 진행 중인 공격에 대응하여 일시 중지가 제정된 경우 특히 위험합니다.
2. `default_fee_bps`가 오래된 값으로 되돌아갈 수 있습니다. 이로 인해 잘못된 수수료 계산이 발생하여 사용자 또는 프로토콜 자체에 잠재적으로 금전적 손실을 초래할 수 있습니다.

이러한 문제는 솔라나의 재시작 메커니즘이 이러한 중요한 OFT 설정을 포함하여 전체 상태(검증자가 투표할 때)를 이전 슬롯으로 되돌리기 때문에 발생합니다. 현재 구현에는 이러한 경우에 대한 안전 장치가 없습니다.

**LastRestartSlot** sysvar를 활용하여 오래된 구성을 감지하는 것을 고려하십시오. 구성이 오래된 경우 관리자가 조치를 취할 때까지 프로토콜이 일시 중지된 것으로 간주하십시오.

예시:

```rust
pub struct OFTStore {
    // ... other fields ...
    pub last_updated_slot: u64,
    // ... other fields ...
}

fn is_config_outdated(oft_store: &OFTStore) -> Result<bool> {
    let last_restart_slot = LastRestartSlot::get()?;
    Ok(oft_store.last_updated_slot <= last_restart_slot.last_restart_slot)
}
```

# [L-08] 동결된 `token_escrow`로 인한 DOS 위험

`init_oft` 명령어에서 `token_mint`는 유효성 검사 없이 설정되어 `freeze_authority`가 있는 `token_mint`를 초기화할 수 있습니다. 동결 권한이 있는 SPL 토큰은 토큰 발행자 또는 권한 있는 엔터티에 의해 계정이 동결될 수 있으며, 이는 OFT(Omni-Chain Fungible Token) 기능에 위험을 초래합니다.

예를 들어:

```rs
pub struct InitOFT<'info> {
    #[account(mut)]
    pub payer: Signer<'info>,
    #[account(
        init,
        payer = payer,
        space = 8 + OFTStore::INIT_SPACE,
        seeds = [OFT_SEED, token_escrow.key().as_ref()],
        bump
    )]
    pub oft_store: Account<'info, OFTStore>,
    #[account(
        init,
        payer = payer,
        space = 8 + LzReceiveTypesAccounts::INIT_SPACE,
        seeds = [LZ_RECEIVE_TYPES_SEED, oft_store.key().as_ref()],
        bump
    )]
    pub lz_receive_types_accounts: Account<'info, LzReceiveTypesAccounts>,
    #[account(mint::token_program = token_program)]
    pub token_mint: InterfaceAccount<'info, Mint>,
}
```

`token_escrow`가 동결되면 토큰 수수료를 전송하는 것이 불가능하여 `send` 명령어에서 서비스 거부(DoS)가 발생합니다. 이 시점에서 수수료 전송이 되돌려지므로 OFT를 사용할 수 없게 됩니다.

```rs
if oft_fee_ld > 0 {
    token_interface::transfer_checked(
        CpiContext::new(
            ctx.accounts.token_program.to_account_info(),
            TransferChecked {
                from: ctx.accounts.token_source.to_account_info(),
                mint: ctx.accounts.token_mint.to_account_info(),
                to: ctx.accounts.token_escrow.to_account_info(),
                authority: ctx.accounts.signer.to_account_info(),
            },
        ),
        oft_fee_ld,
        ctx.accounts.token_mint.decimals,
    )?;
}
```

1. OFT 초기화 중에 `token_mint`에 `freeze_authority`가 있는지 확인하고 감지되면 오류를 반환합니다.
2. 이러한 토큰에 대한 지원이 필요한 경우 UI에 경고를 표시하여 거래자에게 관련 위험을 알립니다.
3. USDC와 같은 주요 규제 스테이블코인에는 보안상의 이유(예: 자금 세탁 방지)로 `freeze_authority`가 있습니다. 프로토콜이 USDC 또는 유사한 토큰을 지원하려는 경우 신뢰할 수 있는 토큰에 대한 허용 목록을 구현하는 동시에 다른 토큰에는 엄격한 검사를 적용하십시오.

# [L-09] 로컬 토큰 민트의 무제한 소수점

`init_oft()` 함수에서:

```rs
        require!(
            ctx.accounts.token_mint.decimals >= params.shared_decimals,
            OFTError::InvalidDecimals
        );
```

이 코드는 프로토콜에서 요구하는 대로 값이 `u64`에 저장될 수 있도록 최대 소수점을 제한해야 합니다.

`lz_receive()` 함수에서 로컬 소수점과 공유 소수점 사이의 상당한 차이로 인해 `ld2sd`가 큰 값을 갖는 경우 오버플로가 발생합니다. 되돌려지지만 `sd2ld`에 사용된 수학은 확인되지 않습니다.

```rs
        let mut amount_received_ld = ctx.accounts.oft_store.sd2ld(amount_sd);
```

`sd2ld` 함수는 오버플로를 방지하기 위해 `checked_mul`을 사용하지 않습니다.

```rs
    pub fn sd2ld(&self, amount_sd: u64) -> u64 {
        amount_sd * self.ld2sd_rate
    }
```

`ld2sd_rate`는 여기에서 계산됩니다.

```rs
        ctx.accounts.oft_store.ld2sd_rate =
            10u64.pow((ctx.accounts.token_mint.decimals - params.shared_decimals) as u32);
```

**개념 증명(POC)**
로컬 토큰에 18개의 소수점이 있고 공유 토큰에 6개의 소수점이 있는 경우 `ld2sd_rate`는 `10^(12)`가 됩니다. `10_000 * 10^(6)`의 금액을 받으면 `sd2ld`에서 계산된 금액은 `10_000 * 10^(18)`이 됩니다. 이 값은 `u64`에 저장할 수 없으므로 오버플로가 발생합니다.

`init_oft` 지침에 소수점 제한을 추가하여 적절한 소수점이 있는 토큰만 `u64`에 저장되도록 허용하는 것을 고려하십시오.

```diff
+        require!(
+            ctx.accounts.token_mint.decimals - params.shared_decimals <= 9,
+            OFTError::InvalidDecimals
+        );

+        require!(
+            ctx.accounts.token_mint.decimals <= 12,
+            OFTError::InvalidDecimals
+        );
```

# [L-10] 동일한 토큰 민트에 대해 여러 네이티브 OFT를 생성할 수 있음

기술 문서에 따르면:

> 각 토큰 민트는 최대 1개의 네이티브 OFT 및 무제한 어댑터 OFT와 연결될 수 있습니다.

프로토콜은 각 토큰 민트가 **하나의 네이티브 OFT**에만 연결될 수 있지만 여러 어댑터 OFT를 가질 수 있음을 강조합니다. 그러나 이 제약 조건은 `init_oft` 지침에서 적용되지 않습니다.

현재 구현에서 `oft_store`는 아래와 같이 `token_escrow`를 기반으로 하는 시드로 초기화됩니다.

```rs
pub struct InitOFT<'info> {
    #[account(
        init,
        payer = payer,
        space = 8 + OFTStore::INIT_SPACE,
        seeds = [OFT_SEED, token_escrow.key().as_ref()],
        bump
    )]
    pub oft_store: Account<'info, OFTStore>,

    #[account(
        init,
        payer = payer,
        token::authority = oft_store,
        token::mint = token_mint,
        token::token_program = token_program,
    )]
    pub token_escrow: InterfaceAccount<'info, TokenAccount>,
```

`token_escrow`가 시드에 사용되므로 동일한 `token_mint`에 대해 여러 `oft_store` 계정을 생성할 수 있습니다. 이는 어댑터 OFT에는 허용되지만 토큰 민트 당 **하나의 네이티브 OFT**만 존재해야 한다는 프로토콜 규칙을 위반합니다. 코드는 이 규칙을 강제하지 않으므로 동일한 토큰 민트에 대해 여러 네이티브 OFT가 생성될 가능성이 있으며 이는 프로토콜의 의도된 설계와 모순됩니다.

**영향**
동일한 `token_mint`에 대해 여러 네이티브 OFT가 생성되면 프로토콜의 보장이 깨져 불일치, 토큰 처리 시 잠재적 충돌 및 크로스 체인 작업에서 예상치 못한 동작이 발생할 수 있습니다. 이는 프로토콜의 의도된 구조를 훼손하고 토큰 공급 및 상호 작용 관리에 문제를 일으킬 수 있습니다.

OFT 유형에 따라 `oft_store`의 시드를 조건부로 만들어 프로토콜 규칙을 시행합니다.

- **네이티브 OFT:** `token_mint`를 시드로 사용하여 각 토큰 민트에 대해 하나의 `Native OFT`만 존재할 수 있도록 보장합니다.
- **어댑터 OFT:** `token_escrow`를 시드로 사용하여 각 토큰 민트에 대해 여러 어댑터 OFT를 허용합니다.

예를 들어:

```diff
if oft_type == OFTType::Native {
+    seeds = [OFT_SEED, token_mint.key().as_ref()];
} else {
+    seeds = [OFT_SEED, token_escrow.key().as_ref()];
}
```

이렇게 하면 각 토큰 민트 당 하나의 네이티브 OFT만 생성되는 동시에 여러 어댑터 OFT에 대한 유연성을 허용합니다.

# [L-11] 사용자가 수수료 지불을 피할 수 있음

취약점은 체인 간에 토큰을 전송하는 역할을 하는 `send` 함수에 존재합니다. 이 함수는 다음 코드에서 보여지는 것처럼 사용자가 토큰의 로컬 표현으로 프로토콜에 수수료를 지불하도록 요구합니다.

```rust
if oft_fee_ld > 0 {
    token_interface::transfer_checked(
        CpiContext::new(
            ctx.accounts.token_program.to_account_info(),
            TransferChecked {
                from: ctx.accounts.token_source.to_account_info(),
                mint: ctx.accounts.token_mint.to_account_info(),
                to: ctx.accounts.token_escrow.to_account_info(),
                authority: ctx.accounts.signer.to_account_info(),
            },
        ),
        oft_fee_ld,
        ctx.accounts.token_mint.decimals,
    )?;
}
```

수수료는 로컬 토큰으로 지불되지만 프로토콜은 실수로 수수료 금액에 먼지 제거를 적용합니다. 먼지 제거는 다른 소수점을 포함하므로 크로스 체인으로 전송되는 금액에만 적용해야 합니다. 수수료는 로컬로 지불되므로 수수료에 대한 먼지 제거는 부적절하며 사용자가 잠재적으로 수수료를 회피할 수 있습니다. 수수료 계산에는 아래와 같이 먼지 제거가 포함됩니다.

```rust
let oft_fee_ld = oft_store.remove_dust(calculate_fee(
    amount_received_ld,
    oft_store.default_fee_bps,
    fee_bps,
));
```

이로 인해 사용자가 수수료 계산을 조작할 수 있는 치명적인 취약점이 열립니다. 소액을 전송함으로써 사용자는 먼지 제거로 인해 수수료를 0으로 만들어 사실상 프로토콜의 수수료 시스템을 우회할 수 있습니다.

`remove_dust` 함수는 다음과 같이 구현됩니다.

```rust
pub fn remove_dust(&self, amount_ld: u64) -> u64 {
    amount_ld - amount_ld % self.ld2sd_rate
}
```

사용자가 보낸 금액으로 인해 먼지 제거로 인해 0으로 줄어들 수 있는 수수료가 발생하는 경우 `default_fee_bps`가 상당한 값으로 설정되더라도 프로토콜은 수수료를 잃게 됩니다.

**악용 시나리오**

- 로컬 소수점: 9
- 공유 소수점: 3 (또는 6)
- 사용자가 보낸 금액: 19,999,999
- `OFTType`: 네이티브
- `default_fee_bps`: 500 (5%에 해당)

이 시나리오에서 먼지 제거 후 받은 금액은 19,000,000입니다. 프로토콜이 가져가는 수수료는 0이며 이는 상당한 자금 손실을 나타냅니다.

**개념 증명(POC)**

`quote_send.rs` 파일에 다음 테스트를 추가하고 인쇄된 값을 관찰할 수 있습니다.

```rust
#[test]
fn tests() {
    let mock_addr = Pubkey::default();

    let ofts = OFTStore {
        oft_type: OFTType::Native,
        ld2sd_rate: 10u64.pow(6),
        token_mint: mock_addr,
        token_escrow: mock_addr,
        endpoint_program: mock_addr,
        bump: 1 as u8,
        tvl_ld: 100_000_000 as u64,
        admin: mock_addr,
        default_fee_bps: 500 as u16,
        paused: false,
        pauser: Some(mock_addr),
        unpauser: Some(mock_addr),
    };

    let amountld: u64 = 19_999_999;
    let amount_received = ofts.remove_dust(amountld);

    println!("Amount received from the user after dust removal {:?}", amount_received);

    let oft_fee = ofts.remove_dust(calculate_fee(amount_received, ofts.default_fee_bps, None));

    println!("The amount of fee taken from the user: {:?}", oft_fee);
}
```

**예상 출력**

```rust
Amount received from the user after dust removal: 19,000,000
The amount of fee taken from the user: 0
```

이 문제를 해결하려면 수수료 금액에 `remove_dust`를 적용하지 마십시오. 수수료는 불필요한 반올림 없이 실제 가치를 반영해야 합니다. 권장 변경 사항은 아래와 같습니다.

```diff
- let oft_fee_ld = oft_store.remove_dust(calculate_fee(
-     amount_received_ld,
-     oft_store.default_fee_bps,
-     fee_bps,
- ));

+ let oft_fee_ld = calculate_fee(
+     amount_received_ld,
+     oft_store.default_fee_bps,
+     fee_bps,
+ );
```
