# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**pump-fun/pump-contracts-solana** 및 **pump-fun/pump-amm-2** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Pump AMM 및 Pump Solana 정보

Solana의 Pump는 초기 유동성을 제공할 필요 없이 본딩 커브에서 거래할 수 있는 SPL 코인을 출시하기 위한 플랫폼입니다. 코인이 특정 시가 총액에 도달하면 유동성이 본딩 커브에서 Raydium으로 예치되고 수령한 LP 토큰은 소각됩니다.

Pump AMM은 Anchor 프레임워크로 구축된 Solana 블록체인의 AMM입니다.

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
- [d38d4896c1e29e4f66a39c07caaadae0961ba17f](https://github.com/pump-fun/pump-contracts-solana/commit/d38d4896c1e29e4f66a39c07caaadae0961ba17f)
- [c564cdcbc515919b20836679932cc88af08bb18f](https://github.com/pump-fun/pump-amm-2/commit/c564cdcbc515919b20836679932cc88af08bb18f)

_수정 검토 커밋 해시:_
- [24587fd504c1ce7a11b7e214462332abdf79e323](https://github.com/pump-fun/pump-contracts-solana/commit/24587fd504c1ce7a11b7e214462332abdf79e323)
- [f42ced72c159b31bcd8fe3efe7159563e900a5bf](https://github.com/pump-fun/pump-amm-2/commit/f42ced72c159b31bcd8fe3efe7159563e900a5bf)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `collect_creator_fee`
- `mod`
- `set_creator`
- `set_metaplex_creator`
- `lib`
- `migrate`
- `fee`
- `create_config`
- `update_fee_config`
- `collect_coin_creator_fee`
- `set_coin_creator`
- `create_pool`
- `buy`
- `sell`
- `global_config`
- `token`
- `pool`

# 발견 사항

# [L-01] 비효율적인 크기 할당 패턴

프로그램은 `BondingCurve` 계정 구조에서 비효율적인 저장소 할당 전략을 구현합니다. 코드는 작성자 수수료 업데이트를 포함한 업그레이드 후 요구 사항이 있을 때 각 계정에 대해 `SIZE+175` 바이트를 할당하여 불필요한 리소스 소비와 비용 비효율성을 초래합니다.

이러한 과도한 사전 할당 패턴은 몇 가지 경제적 및 성능 문제를 야기합니다.

1. 사용자 비용 증가: 사용자는 과도하게 할당된 공간에 대해 더 높은 임대료를 지불해야 하므로 거래 비용이 필요 이상으로 비싸집니다.

2. 검증인 저장소 부담: 많은 수의 계정이 생성됨에 따라 과도한 공간 할당은 생태계 전체에 걸쳐 배가되어 검증인 저장소 요구 사항에 불필요한 데이터를 기가바이트 단위로 추가할 수 있습니다.

```rust
impl BondingCurve {
    pub const SIZE: usize = 8 + BondingCurve::INIT_SPACE;
+   pub const NEW_SIZE: usize = BondingCurve::SIZE + 175;

    pub fn buy_quote(&self, amount: u64) -> Result<u128> {
        let amount = u128::from(amount);
        let virtual_sol_reserves = u128::from(self.virtual_sol_reserves);
        let virtual_token_reserves = u128::from(self.virtual_token_reserves);

        let sol_cost = amount
            .checked_mul(virtual_sol_reserves)
            .ok_or_else(|| error!(PumpError::Overflow))?
            .checked_div(
                virtual_token_reserves
                    .checked_sub(amount)
                    .ok_or_else(|| error!(PumpError::Overflow))?,
            )
            .ok_or_else(|| error!(PumpError::DivisionByZero))?;

        sol_cost
            .checked_add(1)
            .ok_or_else(|| error!(PumpError::Overflow)) // always round up
    }
```

권장 사항:
수백 퍼센트가 아닌 향후 확장을 위한 합리적인 버퍼(예: 50-100% 추가 공간)를 두고 실제 요구 사항에 더 가까운 공간을 할당하는 더 효율적인 계정 크기 조정 모델을 구현하십시오.

# [L-02] 수수료가 징수되지 않는 경우에도 수수료 금고가 임대 면제됨

Pump 프로그램은 이제 작성자 수수료를 징수하여 작성자 금고로 보내며, 작성자는 나중에 금고에서 수수료를 인출할 수 있습니다. 수수료 이체 실패를 방지하려면 수수료 금고 계정이 임대 면제(rent-exempt)되어야 합니다. 이것이 `buy` 및 `sell` 인스트럭션 중에 작성자가 있는 경우 금고가 임대 면제되는 이유입니다.

```Rust
if has_creator {
    make_creator_vault_rent_exempt(system_program, user, creator_vault)?;

    // transfer the sol creator_fee to creator_vault
    transfer_sol_from_user(system_program, user, creator_vault, creator_fee)?;
}
```

그러나 수수료가 0일 수 있으며, 그럼에도 불구하고 작성자 금고는 0 램포트(lamports)의 이체와 함께 임대 면제됩니다. 이는 거래 수수료에 불필요한 추가 비용을 의미합니다. 비용은 낮지만(작성 당시 약 $0.13인 `0.00089088` SOL), 일반적인 Solana 거래에 비해 상당한 금액입니다.

이체할 수수료가 0이 아닌지 확인하도록 조건을 확장하는 것을 고려하십시오.

```diff
-       if has_creator {
+       if has_creator && creator_fee > 0 {
            make_creator_vault_rent_exempt(system_program, user, creator_vault)?;

            // transfer the sol creator_fee to creator_vault
            transfer_sol_from_user(system_program, user, creator_vault, creator_fee)?;
        }
```
