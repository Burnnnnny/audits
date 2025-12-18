# Pashov Audit Group 정보

Pashov Audit Group은 업계에서 잘 증명된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 챔피언이거나, 우리와 함께한 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하고 패치하는 데 도움을 주면서, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**pump-fun/pump-contracts-solana** 및 **pump-fun/pump-amm-2** 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며, **ctrus, FrankCastle, newspace**가 **Pump** 검토에 참여했습니다. 총 **3**개의 문제가 발견되었습니다.

# Pump 정보

Solana의 Pump는 초기 유동성을 제공할 필요 없이 본딩 커브에서 거래할 수 있는 SPL 코인을 출시하기 위한 플랫폼입니다. 코인이 특정 시가 총액에 도달하면 유동성이 본딩 커브에서 Raydium으로 예치되고 수령한 LP 토큰은 소각됩니다. Pump AMM은 Solana 블록체인의 AMM입니다.

# 보안 평가 요약

**검토 커밋 해시:**
• [3c16c4c7b1a67b4c70818baf220ff0e8fc30c470](https://github.com/pump-fun/pump-contracts-solana/tree/3c16c4c7b1a67b4c70818baf220ff0e8fc30c470)
  (pump-fun/pump-contracts-solana)
• [8325db469809c58f489f4ba94f69872483a90754](https://github.com/pump-fun/pump-amm-2/tree/8325db469809c58f489f4ba94f69872483a90754)
  (pump-fun/pump-amm-2)

**수정 검토 커밋 해시:**
• [2e7fc66d31e63a28fd7262ec7de3b023b93305d8](https://github.com/pump-fun/pump-contracts-solana/tree/2e7fc66d31e63a28fd7262ec7de3b023b93305d8)
  (pump-fun/pump-contracts-solana)
• [ad66e35541d04b6fd9561d6fe83a7809c1c081c3](https://github.com/pump-fun/pump-amm-2/tree/ad66e35541d04b6fd9561d6fe83a7809c1c081c3)
  (pump-fun/pump-amm-2)

# 범위

- `buy.rs`
- `lib.rs`
- `fee.rs`
- `mod.rs`
- `common.rs`
- `constant_product.rs`
- `volume_accumulator.rs`

# 발견 사항

# [L-01] `get_token_account_rent`가 확장을 무시하여 Token-2022 임대료를 잘못 계산함

_해결됨_

[`utils/rent.rs`](https://github.com/pump-fun/pump-amm-2/blob/8325db469809c58f489f4ba94f69872483a90754/programs/pump-amm/src/utils/rent.rs#L50)에서 `get_token_account_rent` 함수는 임대 면제 금액을 계산할 때 표준 SPL Token과 Token-2022 계정 모두에 대해 **고정된 바이트 크기**를 가정합니다.

그러나 **Token-2022 계정에는 여러 확장**(전송 수수료, 기밀 전송, 이자 발생 계정 등)이 포함될 수 있으며, 이는 계정의 데이터 크기를 기본 165바이트 이상으로 증가시킵니다.

현재 구현:

```rust
pub fn get_token_account_rent(rent: &Rent, token_program: &Pubkey) -> Result<u64> {
    Ok(if token_program == &spl_token::ID {
        rent.minimum_balance(spl_token::state::Account::LEN)
    } else {
        rent.minimum_balance(spl_token_2022::state::Account::LEN)
    })
}
```

이 로직은 `spl_token::Account`와 `spl_token_2022::Account`를 모두 동일한 정적 길이(`165`)를 갖는 것으로 취급하여 확장이 있는 Token-2022 계정에 대해 **임대 면제 금액을 과소 계산**하게 합니다.

결과적으로 확장이 있는 계정은 **임대 면제에 실패**하여 토큰 초기화 및 전송 중에 **예상치 못한 램포트 소모**나 런타임 오류가 발생할 수 있습니다.

**권장 사항**

Token-2022 계정에 대해 **확장을 포함한 총 계정 크기**를 동적으로 계산하도록 함수를 업데이트하십시오.

더 정확한 구현에는 계정에 적용된 확장을 쿼리하거나 시뮬레이션하는 것이 포함됩니다. 예를 들어:

```rust
use spl_token_2022::extension::ExtensionType;

pub fn get_token_account_rent(rent: &Rent, token_program: &Pubkey, extensions: &[ExtensionType]) -> Result<u64> {
    if token_program == &spl_token::ID {
        Ok(rent.minimum_balance(spl_token::state::Account::LEN))
    } else {
        let account_size = spl_token_2022::extension::get_account_len::<spl_token_2022::state::Account>(extensions);
        Ok(rent.minimum_balance(account_size))
    }
}
```

이렇게 하면 사용하는 확장에 관계없이 Token-2022 계정에 대해 임대료가 올바르게 계산되어 적절한 임대 면제를 유지하고 예상치 못한 램포트 부족을 방지할 수 있습니다.

# [L-02] `net_sol`을 조정하기 전에 계산되어 수수료가 과다 청구됨

_해결됨_

프로토콜은 초기 계산 후 초과 금액을 차감하여 `net_sol` 값을 조정합니다. 그러나 **수수료는 조정이 이루어지기 전의 이전(더 큰) `net_sol` 값을 사용하여 계산**됩니다.

이로 인해 수수료 계산이 실제로 전송되거나 사용되는 SOL의 최종 금액을 반영하지 않으므로 **수수료 과다 청구**가 발생합니다.

간단히 말해서:

1. `net_sol`이 먼저 계산됩니다.
2. 수수료는 이 값을 기준으로 계산됩니다.
3. 나중에 코드는 `net_sol`을 조정합니다(예: 초과 SOL 차감).
4. 그러나 수수료는 오래된 더 높은 `net_sol`을 기준으로 유지되므로 초과 지불이 발생합니다.

이러한 불일치로 인해 사용자는 **수수료로 추가 SOL을 잃고**, 구매자/판매자 금액 간의 회계가 어긋나며, 환불 또는 조정 로직이 복잡해질 수 있습니다.

**권장 사항**

**`net_sol`을 조정한 후** 수수료를 다시 계산하여 최종 수수료가 **업데이트된 순 가치**를 반영하도록 하십시오.

수정 예시:

```rust
// Adjust net_sol first
net_sol = net_sol.saturating_sub(excess_amount);

// Then recalculate fees based on the adjusted net_sol
let fee_amount = calculate_fees(net_sol);
```

이는 사용자가 실제로 전송된 순 SOL에 비례하여 정확한 수수료를 청구받도록 보장하여 과다 청구를 방지하고 회계 무결성을 개선합니다.

# [L-03] 임대료 계산이 부족한 잔액 대신 전체 면제 금액을 사용함

_해결됨_

[`Buy::calculate_rent`](https://github.com/pump-fun/pump-contracts-solana/pull/99/files#diff-53abacb17a0e41134c0f871af33a9e8a4065871c446ee825bfee2bb8fe7d2047R1311-R1338)에서 함수는 현재 잔액과 필요한 임대 면제 금액 간의 **차이**만 계산하는 것이 아니라 아직 임대 면제가 되지 않은 계정에 대해 **전체 임대 면제 금액**을 추가합니다.

계정에 이미 일부 잔액이 있을 수 있으므로 이는 **필요한 램포트의 과대평가**를 유발합니다. 결과적으로 초기화 또는 구매 작업 중에 초과 SOL이 불필요하게 전송되거나 예약되어 **자금의 비효율적인 사용**으로 이어질 수 있습니다.

현재 구현:

```rust
total = total
    .checked_add(rent.minimum_balance(CREATOR_VAULT_DATA_LEN))
    .ok_or(PumpError::Overflow)?;
```

그러나 계정의 현재 잔액을 무시합니다. 임대료는 임대 면제 임계값에 도달하는 데 필요한 **남은** 금액만 고려해야 합니다.

올바른 계산은 다음과 같아야 합니다.

```rust
let current_balance = self.creator_vault.lamports();
let rent_exemption_lamports = rent.minimum_balance(CREATOR_VAULT_DATA_LEN);

if current_balance < rent_exemption_lamports {
    let rent_amount = rent_exemption_lamports - current_balance;
    total = total.checked_add(rent_amount).ok_or(PumpError::Overflow)?;
}
```

**권장 사항**

임대 면제 금액과 현재 계정 잔액 간의 차이만 고려하도록 임대료 계산 로직을 업데이트하십시오.

```rust
rent_amount = rent_exemption_lamports.saturating_sub(current_balance);
```

이를 통해 **필요한 램포트**만 추가하고 과다 청구를 방지하며 관련된 모든 계정에서 더 정확하고 효율적인 임대료 추정을 유지할 수 있습니다.
