# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 잘 알려진 40명 이상의 프리랜서 보안 연구원으로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 $100k 이상을 획득했거나, 여러 번의 우승 경력이 있거나, 우리와의 감사에서 탁월한 성과를 보였습니다. 우리는 검증되고 동기 부여된 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도왔으며, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Enclave-Money/solver-fund-pool-anchor** 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, **0xAlix2, ZeroTrust01, shaflow, Johny**가 **Enclave Solver Fund Pool** 검토에 참여했습니다. 총 **6**개의 문제가 발견되었습니다.

# Enclave Solver Fund Pool 소개 (About Enclave Solver Fund Pool)

Enclave는 사용자가 오프체인 승인 및 리플레이 방지(replay-protected) 서명을 통해 즉시 토큰을 빌릴 수 있는 자금 풀을 제공하는 Solana 프로그램입니다.

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [668adb2eac1ecb81843ffda226db1fd1e1ed9396](https://github.com/Enclave-Money/solver-fund-pool-anchor/tree/668adb2eac1ecb81843ffda226db1fd1e1ed9396)<br>&nbsp;&nbsp;(Enclave-Money/solver-fund-pool-anchor)

**수정 검토 커밋 해시:**<br>• [3034df5741fb9e3a1c0c912a5ccd8c28f9d90a55](https://github.com/Enclave-Money/solver-fund-pool-anchor/tree/3034df5741fb9e3a1c0c912a5ccd8c28f9d90a55)<br>&nbsp;&nbsp;(Enclave-Money/solver-fund-pool-anchor)

# 범위 (Scope)

- `admin_management.rs`
- `borrow.rs`
- `initialize.rs`
- `mod.rs`
- `signer_management.rs`
- `error.rs`
- `lib.rs`
- `state.rs`
- `utils.rs`

# 발견 사항 (Findings)

# [C-01] 자금이 리디렉션될 수 있음

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

```rust
 #[account(mut)]
    pub user_token_account: Account<'info, TokenAccount>
```

`programs/solver-fund-pool-anchor/src/instructions/borrow.rs:142-143`에서 `user_token_account`는 쓰기 가능하기만 하면 됩니다. 프로그램은 `owner == user.key()`인지 확인하지 않습니다. 서명된 메시지도 `user_token_account`를 생략하며, 계약은 `user_token_account.owner`가 `user`와 같은지, 이것이 사용자의 ATA(Associated Token Account)인지 검증하지 않습니다.

결과적으로 공격자는 합법적인 서명(`user/mint/amount/time window/nonce/...`만 바인딩함)을 획득하고 `user_token_account`를 자신의 토큰 계정으로 교체하여 풀 자금을 자신에게 직접 빼돌릴 수 있습니다.

## 권장 사항

```rust
#[account(
    mut,
    constraint = user_token_account.mint == token_mint.key() @ ErrorCode::InvalidTokenAccountMint,
    constraint = user_token_account.owner == user.key() @ ErrorCode::InvalidTokenAccountOwner,
)]
pub user_token_account: Account<'info, TokenAccount>,
```

또는 사용자의 ATA로 제한하십시오:

```rust
use anchor_spl::associated_token::get_associated_token_address;
require_keys_eq!(
    user_token_account.key(),
    get_associated_token_address(&Pubkey::try_from(user.key())?, &token_mint.key()),
    ErrorCode::NotUserATA
);
```

# [C-02] 잘못된 오프셋 Ed25519 명령 내부 검사로 인한 위조된 승인 활성화

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

프로그램은 명령 sysvar를 통해 Ed25519 서명을 "검증"하여 오프체인 승인을 수락합니다. `borrow`는 동일한 트랜잭션의 이전 명령이 기본 Ed25519 검증이기를 기대하고 해당 명령의 원시 데이터를 검사하여 서명이 유효한지 결정합니다.

그러나 현재 검증기는 이 패턴을 건전하게 만드는 필수 검증을 수행하지 않습니다:

* 이전 명령의 `program_id`가 기본 Ed25519 프로그램인지 주장(assert)하지 않습니다.
* `Ed25519SignatureOffsets`의 세 가지 `*_instruction_index` 필드가 `0xFFFF`(이 Ed25519 명령의 데이터를 사용함을 의미)가 되도록 요구하여 "인라인 모드"를 강제하지 않습니다.
* Ed25519 명령 데이터 내부의 특정 바이트 범위를 읽는 데 의존하지만 제공된 오프셋과 크기가 실제로 해당 범위를 가리키는지 검증하지 않습니다.

Solana에서 Ed25519 검증기는 유연합니다. 명령은 서명, 공개 키 및 메시지를 읽을 위치에 대한 오프셋과 명령 인덱스를 지정할 수 있습니다. 계약이 해당 필드를 검증하지 않으면 공격자는 Ed25519 프로그램이 공격자 제어 데이터에 대한 서명을 검증하도록 하는 선행 명령을 만들 수 있으며, 프로그램이 나중에 확인하는 위치에 무해해 보이는 바이트(예상 서명자 공개 키, 메시지 해시, 서명)를 배치할 수 있습니다. 그러면 권한 있는 서명자의 서명이 실제로 검증되지 않았음에도 검증기는 성공을 반환합니다.

결과적으로 모든 호출자는 `borrow` 직전에 악의적으로 구성된 "검증" 명령을 포함하는 트랜잭션을 제출하고 프로그램이 이를 유효한 것으로 수락하도록 할 수 있습니다. 이를 통해 풀에서 무단 대출이 가능하고 사용자 논스가 증가하여 잠재적으로 합법적인 사용자가 나중에 정품 승인을 상환하는 것을 차단할 수 있습니다.

## 권장 사항

다음이 모두 참인 경우에만 이전 명령을 유효한 증거로 처리하도록 Ed25519 검증을 강화하십시오:

* 이전 명령의 `program_id`가 기본 Ed25519 프로그램입니다.
* 명령은 정확히 하나의 서명을 인코딩하고 보조 계정을 전달하지 않습니다.
* `Ed25519SignatureOffsets`의 세 가지 `*_instruction_index` 필드가 모두 `0xFFFF`(인라인 모드)이므로 서명, 공개 키 및 메시지는 동일한 Ed25519 명령에서 가져옵니다.
* 슬라이싱 전에 모든 오프셋과 크기가 명령 데이터 길이에 대해 범위 확인됩니다.
* 인라인 공개 키, 메시지 및 서명 바이트는 예상되는 권한 있는 서명자 및 메시지와 정확히 일치합니다.
* `borrow`가 첫 번째 명령인 경우 거부하십시오(검사할 "이전" 검증이 없음).

# [L-01] 프로그램이 `Token-2022` 발행을 지원하지 않음

_해결됨 (Resolved)_

프로그램은 레거시 SPL 토큰 인터페이스만 독점적으로 가져오고 사용합니다:

```rust
use anchor_spl::token::{self, Mint, Token, TokenAccount, Transfer};
pub token_program: Program<'info, Token>;
```

Token-2022 발행(`spl_token_2022::ID` 소유)이 전달되면 런타임이 각 계정 유형에 대해 올바른 프로그램 ID를 강제하므로 CPI가 실패합니다.

**권장 사항:**

SPL 토큰(레거시) 및 Token-2022 프로그램 모두와 호환되는 Anchor의 **토큰 인터페이스** 계층으로 전환하는 것을 고려하십시오.

# [L-02] `borrow`의 `user_borrow_state`에 `has_one = user` 제약 조건 부족

_해결됨 (Resolved)_

`Borrow` 계정 구조체(`programs/solver-fund-pool-anchor/src/instructions/borrow.rs:109-128`)는 시드 `[USER_BORROW_SEED, user.key().as_ref()]`를 사용하여 사용자별 상태 PDA를 파생하지만, `UserBorrowState` 내부의 저장된 `user` 필드가 실제로 명령에 제공된 `user` 계정과 일치하는지 강제하지 않습니다. 계정 데이터가 수정되는 경우(예: 확인되지 않은 CPI 또는 데이터 손상), 프로그램은 일치하지 않는 튜플을 기꺼이 수락하여 온체인에 저장된 논스 소유자가 토큰 전송 및 서명 검증에 사용되는 수신자와 다르게 만듭니다. 이는 차용 논스를 소유한 사람에 대한 중요한 가정을 깨뜨리고 리플레이 보호 또는 다운스트림 회계를 복잡하게 만들 수 있습니다.

**권장 사항**
`has_one = user`를 `user_borrow_state` 계정 제약 조건에 추가하여 Anchor가 지속된 `user` 필드가 제공된 `user` 계정과 일치하는지 검증하고 PDA 주변의 일관성 확인을 강화하도록 하십시오.

# [L-03] 시드 상수 및 바이트 리터럴의 일관성 없는 사용

_해결됨 (Resolved)_

`fund_pool` 주소를 확인할 때 사용되는 시드가 일관성이 없습니다. 일부 위치에서는 상수 `FUND_POOL_SEED`를 사용하는 반면 다른 위치에서는 리터럴 `b"fund_pool"`을 직접 사용합니다.

```rust
    #[account(
        mut,
        seeds = [b"fund_pool"],
        bump = fund_pool.bump,
        constraint = fund_pool.is_admin(&admin.key()) @ ErrorCode::Unauthorized
    )]
    pub fund_pool: Account<'info, FundPoolState>,

    #[account(
        seeds = [FUND_POOL_SEED],
        bump = fund_pool.bump
    )]
    pub fund_pool: Account<'info, FundPoolState>,
```

둘 다 현재 동일하지만 업그레이드 및 유지 관리 중에 실수로 하나를 수정하면 불일치가 발생할 수 있습니다.

유지 관리성을 향상시키고 업그레이드로 인한 위험을 줄이기 위해 `FUND_POOL_SEED` 상수를 일관되게 사용하는 것이 좋습니다.

# [L-04] 프론트런 가능한 초기화

_인정됨 (Acknowledged)_

`initialize` 명령은 전역 `fund_pool` PDA(`seed = b"fund_pool"`)를 생성하고 호출자가 제공한 임의의 공개 키로 `initial_admin`을 설정합니다. 이 첫 번째 사용 초기화 프로그램을 호출할 수 있는 사람을 제한하는 액세스 제어가 없습니다. 공격자는 배포를 프론트런하고, 풀을 초기화하고, 프로그램 수명 동안(재배포까지) 모든 관리자 및 서명자 게이트 작업에 대한 제어권을 장악할 수 있습니다.

**권장 사항**

프로그램 배포자 또는 미리 정의된 주소와 같은 신뢰할 수 있는 엔티티만 `initialize` 함수를 호출할 수 있도록 하는 강력한 액세스 제어 메커니즘을 구현하십시오. 이 제한은 호출자의 신원을 확인하거나 초기화 중에 특정 서명을 사용하여 시행할 수 있습니다.
