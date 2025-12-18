
# Pashov Audit Group 소개


Pashov Audit Group은 업계에서 입증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 우승자이거나, 우리와 함께한 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 의욕적인 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도왔으며, 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위해 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.


# 면책 조항


스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.


# 서론

<p>Pashov Audit Group이 <strong>starkware-libs/starknet-staking</strong> 및 <strong>starkware-libs/starkware-starknet-utils</strong> 저장소에 대해 시간 제한이 있는 보안 검토를 수행했으며, <strong>Starknet Staking</strong>을 검토했습니다. 총 <strong>9</strong>개의 문제가 발견되었습니다.</p>

# Starknet Staking 소개

<p>Starknet Staking은 직접 스테이킹(direct staking)과 지분 위임(stake delegation)을 모두 포함하는 Cairo로 구현된 무허가형 온체인 스테이킹 프로토콜의 첫 번째 단계를 소개합니다. 이 단계는 민팅 커브 기반 보상 모델, 스테이커 정의 수수료율 및 출금 락업을 포함하여 프로토콜의 경제적 인센티브 메커니즘을 테스트하는 데 중점을 둡니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [e4f75ea411d1a46abdb5127d0abcc5b3891e9300](https://github.com/starkware-libs/starknet-staking/tree/e4f75ea411d1a46abdb5127d0abcc5b3891e9300)<br>&nbsp;&nbsp;(starkware-libs/starknet-staking)<br>• [142ba4fd8525368690969f623c682e33230ce866](https://github.com/starkware-libs/starkware-starknet-utils/tree/142ba4fd8525368690969f623c682e33230ce866)<br>&nbsp;&nbsp;(starkware-libs/starkware-starknet-utils)

**수정 검토 커밋 해시:**<br>• [5c11a5689f2d2e08ffecebf30d3e49569accbfca](https://github.com/starkware-libs/starknet-staking/tree/5c11a5689f2d2e08ffecebf30d3e49569accbfca)<br>&nbsp;&nbsp;(starkware-libs/starknet-staking)


**스테이킹 컨트랙트의 클래스 해시:** <br>
• Staking: <br>
&nbsp;  `0x05a93367d9e4fd00d9b17575cc52f70f8b48c3926da015cc7e87a3994f1c63a7` <br>
• RewardSupplier: <br>
&nbsp;  `0x05b312a712b50de7059ae1ad02f4445568ecc8ca49765989175a40f404cfceb9` <br>
• MintingCurve: <br>
&nbsp;  `0x0160a75d5bed7570e3eaab102286940d28242fc5211bd1503da7e7414b707f52` <br>
• Pool: <br>
&nbsp;  `0x069565cd9bd273116d8829ba461298a04bfe09adb0c4ac470e7bb66bcae84ab5` <br>



# 범위

- `constants.cairo`
- `errors.cairo`
- `minting_curve.cairo`
- `pool.cairo`
- `interface.cairo`
- `interface_v0.cairo`
- `objects.cairo`
- `utils.cairo`
- `reward_supplier.cairo`
- `eic_v1_v2.cairo`
- `trace.cairo`
- `replaceability.cairo`
- `roles.cairo`
- `iterable_map.cairo`
- `time.cairo`
- `identity.cairo`
- `staking.cairo`

# 발견 사항



# [M-01] 스테이커가 마이그레이션 전에 수수료를 설정하여 위임자를 악용할 수 있음

_해결됨 (Resolved)_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`set_commission()` 함수는 `V1` 정보를 가진 스테이커가 실제로 `V2`로 마이그레이션했는지 확인하지 않고 `staker_pool_info.commission`을 설정할 수 있게 합니다. 이는 `staker_info`를 가져오기 위해 호출되는 `internal_staker_info()`가 단순히 사용 가능한 `V1` 정보를 반환하기 때문입니다.

```rust
>>      let staker_info = self.internal_staker_info(:staker_address);
        assert!(staker_info.unstake_time.is_none(), "{}", Error::UNSTAKE_IN_PROGRESS);

        let staker_pool_info_mut = self.staker_pool_info.entry(staker_address);
        if let Option::Some(old_commission) = staker_pool_info_mut.commission.read() {
            //..
        } else {
>>          staker_pool_info_mut.commission.write(Option::Some(commission));
            self.emit(Events::CommissionInitialized { staker_address, commission });
        }
```

`staker_pool_info`는 `V2` 기능이므로 `V1`에 있는 스테이커는 아직 이를 가지고 있지 않으며, 따라서 `else` 블록이 실행됩니다.

그 후, 이 스테이커는 단순히 `set_open_for_delegation()` 함수를 호출하고 임의의 `BTC` 풀을 배포할 수 있습니다. 여기서 `staker_pool_info.commission`이 위에서 이미 설정되었기 때문에 확인을 통과합니다.

```rust
    let staker_info = self.internal_staker_info(:staker_address);
    // @audit commision was already set above
>>  let staker_pool_info_mut = self.staker_pool_info.entry(staker_address);
    assert!(staker_info.unstake_time.is_none(), "{}", Error::UNSTAKE_IN_PROGRESS);
    assert!(self.does_token_exist(:token_address), "{}", Error::TOKEN_NOT_EXISTS);
    assert!(
        !staker_pool_info_mut.has_pool_for_token(:token_address),
        "{}",
        Error::STAKER_ALREADY_HAS_POOL,
    );
    // @audit passes
>>  let commission = staker_pool_info_mut.commission();

    // Deploy delegation pool contract.
    let pool_contract = self
        .deploy_delegation_pool_from_staking_contract(
            :staker_address,
            staking_contract: get_contract_address(),
            :token_address,
            :commission,
        );
    // @audit Pool added
>>  staker_pool_info_mut.pools.write(pool_contract, token_address);
    // Initialize the delegated balance trace.
    self.initialize_staker_delegated_balance_trace(:staker_address, :pool_contract);
```

이 시점까지 위임자들은 트레이스가 현재 에포크로 초기화되었기 때문에 단순히 `BTC` 토큰을 이 풀에 위임할 수 있습니다.

**가정**

- 스테이커는 `2%`라는 매우 낮은 수수료를 설정합니다.
- 스테이커는 이미 `V1`에 `pool_info._deprecated_commission`이 `40%`인 `STRK` 풀을 가지고 있었으며, 아직 마이그레이션하지 않았습니다.
- 새로운 위임자들은 수수료가 `2%`라는 것을 알고 새로운 `BTC` 풀에 합류했습니다.

이제 스테이커는 `staker_pool_info.commission` 즉 `2%`에 따라 보상을 분배하고 나누는 `attestation`을 수행하려고 합니다.
그는 그 후 `staker_migration()`을 호출합니다:

```rust
    fn staker_migration(ref self: ContractState, staker_address: ContractAddress) {
        // @audit Assertion passes
        assert!(
>>          self.staker_own_balance_trace.entry(staker_address).is_empty(),
            "{}",
            Error::STAKER_INFO_ALREADY_UPDATED,
        );
        // Migrate staker pool info.
        let internal_staker_info = self.internal_staker_info(:staker_address);
        let staker_pool_info_mut = self.staker_pool_info.entry(staker_address);
        let mut pool_contract = Option::None;
        // @audit fetches his STRK pool from v1
>>      if let Option::Some(pool_info) = internal_staker_info._deprecated_pool_info {
            pool_contract = Option::Some(pool_info._deprecated_pool_contract);
            let token_address = STRK_TOKEN_ADDRESS;
            // @audit old commision from v1 fetched
>>          let commission = pool_info._deprecated_commission;
            // @audit previously set commission is overwritten
>>          staker_pool_info_mut.commission.write(Option::Some(commission));
            staker_pool_info_mut.pools.write(pool_contract.unwrap(), token_address);
        }
        // Note: Staker might have a commission commitment only if he has a pool.
        staker_pool_info_mut
            .commission_commitment
            .write(internal_staker_info._deprecated_commission_commitment);
        // @audit staker_own_balance_trace is then populated here
>>      self.migrate_staker_balance_trace(:staker_address, :pool_contract);
        // Add staker address to the stakers vector.
        self.stakers.push(staker_address);
    }
```

언급했듯이, 첫 번째 어설션은 이 시점까지 스테이커의 `staker_own_balance_trace` 매핑이 비어 있기 때문에 통과합니다. 이것은 `V2` 기능이며, 그가 여기서 어떠한 스테이킹도 수행하지 않았기 때문에 기록이 존재하지 않습니다.

그런 다음 함수는 그가 `STRK` 풀을 가지고 있는지 확인하는데, 그는 `V1`에서 가지고 있습니다. `V1`의 `pool_info._deprecated_commission` 즉 `40%`가 검색되어 `staker_pool_info_mut.commission`에 설정되고 해당 풀도 그의 `staker_pool_info_mut.pools`에 추가됩니다. 따라서 `BTC` 위임자들이 가입한 이전에 설정된 `2%` 수수료는 덮어씌워져 `40%`로 업데이트됩니다.

그 후 스테이커는 검증(attestation)을 수행하며, `update_rewards_from_attestation_contract()` 호출을 통해 `calculate_staker_pools_rewards()`에서 다음이 발생합니다.

```rust
            // @audit 40% is fetched
>>          let commission = staker_pool_info.commission();
            let curr_epoch = self.get_current_epoch();
            for (pool_contract, token_address) in staker_pool_info.pools {
                if !self.is_active_token(:token_address, :curr_epoch) {
                    continue;
                }
                let pool_balance_curr_epoch = self
                    .get_staker_delegated_balance_curr_epoch(
                        :staker_address, :pool_contract, :curr_epoch,
                    );
                let (epoch_rewards, total_stake) = if token_address == STRK_TOKEN_ADDRESS {
                    (strk_epoch_rewards, strk_total_stake)
                } else {
                    (btc_epoch_rewards, btc_total_stake)
                };
                // Calculate rewards for this pool.
                let pool_rewards_including_commission = if total_stake.is_non_zero() {
                    mul_wide_and_div(
                        lhs: epoch_rewards,
                        rhs: pool_balance_curr_epoch.to_amount_18_decimals(),
                        div: total_stake,
                    )
                        .expect_with_err(err: GenericError::REWARDS_ISNT_AMOUNT_TYPE)
                } else {
                    Zero::zero()
                };
                // @audit staker's commission_rewards is calculated based on 40% even for BTC stakes
>>              let (commission_rewards, pool_rewards) = self
                    .split_rewards_with_commission(
                        rewards_including_commission: pool_rewards_including_commission,
                        :commission,
                    );

                // @audit Staker accumulates excess
>>              total_commission_rewards += commission_rewards;
                ---snip---
            }
```

보시다시피, `BTC` 위임자들은 받아야 할 `pool_rewards`보다 훨씬 적게 받게 되므로 사기를 당한 것입니다.

이것이 문제가 되는 이유는 정상적인 흐름에서 스테이커는 `update_commission()`에서 강제하듯이 `commission_commitment`가 없으면 설정된 `commission`을 늘릴 수 있는 위치에 있어서는 안 되기 때문입니다.

```rust
            else {
>>              assert!(commission < old_commission, "{}", GenericError::INVALID_COMMISSION);
            }
```

## 권고 사항

`set_commission()`에 다음 확인을 추가하십시오:

```diff
        // Assert the staker has migrated.
+       assert!(
+           self.staker_own_balance_trace.entry(staker_address).is_non_empty(),
+           "{}",
+           Error::STAKER_INFO_NOT_UPDATED,
+       );
```



# [L-01] 연간 민팅 계산 불일치: 문서 vs 구현

_해결됨 (Resolved)_

연간 민팅 보상 계산에 대한 문서화된 동작과 실제 구현 간에 불일치가 있습니다. 주석은 간단한 선형 인플레이션 메커니즘을 설명하지만, 구현은 완전히 다른 제곱근 기반 공식을 사용하여 문서화된 것과 다른 경제적 결과를 초래합니다.

**불일치 사항:**

1. **주석은 선형 인플레이션을 설명함**:
```cairo
// The numerator of the inflation rate. The denominator is C_DENOM. C_NUM / C_DENOM is the
// fraction of the total supply that can be minted in a year.
```

이는 공식이 다음과 같아야 함을 시사합니다:
```
yearly_mint = total_supply * (C_NUM / C_DENOM)
```

2. **구현은 제곱근 공식을 사용함**:
```cairo
fn compute_yearly_mint(
    self: @ContractState, total_stake: Amount, total_supply: Amount,
) -> Amount {
    let stake_times_supply: u256 = total_stake.wide_mul(total_supply);
    self.c_num.read().into() * stake_times_supply.sqrt() / C_DENOM.into()
}
```

실제 공식:
```
yearly_mint = C_NUM * sqrt(total_stake * total_supply) / C_DENOM
```

이 불일치는 프로토콜의 전체 경제 모델에 영향을 미치므로 중요하며, 모든 이해 관계자의 혼란과 잠재적인 재정적 계산 오류를 방지하기 위해 즉시 해결해야 합니다.



# [L-02] 잘못된 계약 버전 번호

_해결됨 (Resolved)_

`staking.cairo` 계약은 `CONTRACT_VERSION: felt252 = '2.0.0'`을 선언하지만, 현재 배포된 계약이 이미 버전 2.0.0이므로 이는 버전 `3.0.0`(새로운 BTC 스테이킹 버전)이어야 합니다.

이는 `version()` 함수가 조회될 때 버전 추적에 혼란을 야기합니다.

`minting_curve` 계약과 같은 다른 계약들에도 불일치가 있다는 점에 유의하십시오. `minting_curve` 계약은 중요한 변경 사항이 있지만 `CONTRACT_VERSION`은 여전히 `1.0.0`을 가리키고 있으므로 변경되어야 합니다.
풀 계약도 마찬가지로 `2.0.0`을 가리키고 있지만, 풀 계약이 새로운 BTC 풀에도 사용됨을 보여주기 위해 `3.0.0`이어야 합니다.

**권고 사항**

올바른 버전을 반영하도록 계약 버전을 업데이트하십시오.
예시:
```rust
// staking.cairo
pub const CONTRACT_VERSION: felt252 = '3.0.0';
```



# [L-03] 언스테이크 작업 시 지워지지 않은 `staker_pool_info`가 남음

_인정됨 (Acknowledged)_

스테이커가 언스테이크 작업을 수행할 때 추가된 `pools`, `commission` 및 `commission_commitment`를 포함하는 풀 정보가 그대로 남습니다.

그러나 이 데이터는 더 이상 활용하는 기능이 없으므로 아무런 목적이 없습니다.
따라서 시스템 내에 고립된(orphaned) 데이터가 남게 됩니다.

**완화 방법**

`unstake_action()` 중에 이 매핑을 지우십시오:

```diff
    ---snip---
    // Return delegated stake to pools and zero their balances.
    self.transfer_to_pools_when_unstake(:staker_address, :staker_pool_info);

+   // now clear this mapping
+   self.staker_pool_info.write(
+       staker_address,
+       InternalStakerPoolInfoV2 {
+           commission: Option::None,
+           pools: IterableMapTrait::new(),
+           commission_commitment: Option::None,
+       }
+   );
    
    staker_amount
```

# [L-04] 0원 초기 위임 취소 의도로 이벤트 스팸 가능

_인정됨 (Acknowledged)_

위임 풀 계약의 `exit_delegation_pool_intent()` 함수는 `pool member`가 기존 위임 취소 의도가 없더라도 `amount = 0` 요청을 제출할 수 있게 합니다.

이는 의미 없는 이벤트 방출인 `PoolMemberExitIntent` 및 `PoolMemberBalanceChanged`(풀 측)와 `RemoveFromDelegationPoolIntent` 및 `StakeDelegatedBalanceChanged`(스테이킹 계약 측)를 트리거합니다.

```rust
    // @audit When 0, unpool_time is set to None but the rest of function flows normally
>>  if amount.is_zero() {
        pool_member_info.unpool_time = Option::None;
    } else {
        pool_member_info.unpool_time = Option::Some(unpool_time);
    }
    ...
    // pool::exit_delegation_pool_intent()
    self.emit(Events::PoolMemberExitIntent { ... });
    self.emit(Events::PoolMemberBalanceChanged { ... });

    // staking::remove_from_delegation_pool_intent()
    self.emit(Events::RemoveFromDelegationPoolIntent { ... });
    self.emit(Events::StakeDelegatedBalanceChanged { ... });
```

이러한 `0원` 호출 반복에 대한 제한이 없기 때문에 악의적인 사용자가 함수를 반복적으로 호출하여 스팸 이벤트를 생성하고 이벤트 로그를 오염시킬 수 있습니다.

**권고 사항**

호출자가 이미 활성 위임 취소 의도(`unpool_time.is_some()`)가 있는 경우에만 `0원` 호출이 허용되도록 확인을 추가하십시오.

```diff
+   assert!(
+       !amount.is_zero() || pool_member_info.unpool_time.is_some(),
+       "{}",
+       GenericError::ZERO_AMOUNT_INITIAL_INTENT
+   );

    // @audit proceed normaly
    if amount.is_zero() {
        pool_member_info.unpool_time = Option::None;
    } else {
        pool_member_info.unpool_time = Option::Some(unpool_time);
    }
```

이는 다음을 보장합니다:
- 초기 종료 의도는 `amount > 0`이어야 합니다.
- 0원 호출은 기존 의도의 취소로서만 유효합니다.



# [L-05] 잘못된 사용자 정의 오류 설명

_해결됨 (Resolved)_

`staking/contracts/src/staking/errors.cairo`의 `describe()` 기능은 정의된 사용자 정의 오류에 대해 잘 형성된 문자열 설명을 출력하는 것을 목표로 합니다.
그러나 `SELF_SWITCH_NOT_ALLOWED`와 `ILLEGAL_EXIT_DURATION`은 다른 오류들처럼 잘 설명되어 있지 않습니다:

```rust
    Error::SELF_SWITCH_NOT_ALLOWED => "SELF_SWITCH_NOT_ALLOWED",
    Error::ILLEGAL_EXIT_DURATION => "ILLEGAL_EXIT_DURATION",
```

따라서 출력 문자열이 잘못 형성되어 있습니다.

**권고 사항**

```diff
-   Error::SELF_SWITCH_NOT_ALLOWED => "SELF_SWITCH_NOT_ALLOWED",
-   Error::ILLEGAL_EXIT_DURATION => "ILLEGAL_EXIT_DURATION",
+   Error::SELF_SWITCH_NOT_ALLOWED => "Switching in the same pool is no allowed",
+   Error::ILLEGAL_EXIT_DURATION => "Exit window is incorrect",
```



# [L-06] `transfer_from`/`transfer` 함수가 반환 값을 확인하지 않음

_해결됨 (Resolved)_

`checked_transfer_from` 및 `checked_transfer` 함수는 전송 호출을 하기 전에 비교적 철저한 잔고 및 허용량 확인을 수행하지만, 여전히 전송 중 반환 값을 확인하지 않습니다. 이로 인해 다른 이유로 전송이 조용히 실패하여 false를 반환하는 동안 계약은 이를 성공적인 것으로 처리할 수 있습니다.

```rust
    fn checked_transfer_from(
        self: IERC20Dispatcher, sender: ContractAddress, recipient: ContractAddress, amount: u256,
    ) -> bool {
        assert!(amount <= self.balance_of(account: sender), "{}", Erc20Error::INSUFFICIENT_BALANCE);
        assert!(
            amount <= self.allowance(owner: sender, spender: get_contract_address()),
            "{}",
            Erc20Error::INSUFFICIENT_ALLOWANCE,
        );
        self.transfer_from(:sender, :recipient, :amount)
    }

    fn checked_transfer(self: IERC20Dispatcher, recipient: ContractAddress, amount: u256) -> bool {
        assert!(
            amount <= self.balance_of(account: get_contract_address()),
            "{}",
            Erc20Error::INSUFFICIENT_BALANCE,
        );
        self.transfer(:recipient, :amount)
    }
```

`transfer` 및 `transfer_from` 함수 호출의 반환 값을 확인하는 것이 좋습니다.



# [L-07] 비활성화된 토큰이 여전히 풀을 통해 스테이킹 가능함

_인정됨 (Acknowledged)_

비활성화된 토큰은 보상 분배를 위해 비활성화되었음에도 불구하고 기존 위임 풀을 통해 여전히 스테이킹될 수 있습니다. 이는 사용자가 실수로 보상을 받을 수 없는 토큰을 스테이킹하여 자금이 생산적이지 않게 되면서 자본의 기회비용을 잃게 되는 시나리오를 만듭니다.

**핵심 문제:**

**풀 스테이킹이 토큰 상태 확인을 우회함**: 사용자가 풀을 통해 스테이킹할 때 `add_stake_from_pool()` 함수는 토큰이 현재 활성 상태인지 확인하지 않습니다:

```cairo
fn add_stake_from_pool(
    ref self: ContractState, staker_address: ContractAddress, amount: Amount,
) {
    // ... prerequisite checks ...
    let token_address = self
        .staker_pool_info
        .entry(staker_address)
        .get_pool_token(:pool_contract)
        .expect_with_err(Error::CALLER_IS_NOT_POOL_CONTRACT);
    
    // ← NO CHECK: assert!(self.is_active_token(:token_address, curr_epoch))
    
    // Staking proceeds regardless of token status
    self.insert_staker_delegated_balance(:staker_address, :pool_contract, delegated_balance: new_delegated_stake);
    self.add_to_total_stake(:token_address, amount: normalized_amount);
}
```

**비활성화된 토큰은 0의 보상을 받음**: 보상 계산 중 비활성화된 토큰은 완전히 제외됩니다:

```cairo
fn calculate_staker_pools_rewards(/* ... */) -> (Amount, Amount, Array<(ContractAddress, NormalizedAmount, Amount)>) {
    for (pool_contract, token_address) in staker_pool_info.pools {
        if !self.is_active_token(:token_address, :curr_epoch) {
            continue; // ← Disabled tokens get ZERO rewards
        }
        // Only active tokens participate in reward calculation
    }
}
```

**사용자는 토큰 상태에 대한 표시를 받지 못함**: 풀 인터페이스는 스테이킹을 방지하거나 사용자에게 비활성화된 토큰에 대해 경고하지 않습니다.

**권고 사항**

**풀 스테이킹에 활성 토큰 확인 추가**:

```cairo
fn add_stake_from_pool(
    ref self: ContractState, staker_address: ContractAddress, amount: Amount,
) {
    // ... existing checks ...
    let token_address = self
        .staker_pool_info
        .entry(staker_address)
        .get_pool_token(:pool_contract)
        .expect_with_err(Error::CALLER_IS_NOT_POOL_CONTRACT);
    
    // ← ADD: Prevent staking disabled tokens
    let curr_epoch = self.get_current_epoch();
    assert!(
        self.is_active_token(:token_address, :curr_epoch), 
        "{}", 
        Error::TOKEN_NOT_ACTIVE
    );
    
    // Continue with staking...
}
```

**토큰이 비활성화될 때 경고 방출**:

```cairo
#[derive(Drop, starknet::Event)]
struct TokenDisabledWarning {
    token_address: ContractAddress,
    affected_pools: Span<ContractAddress>,
    users_should_unstake: bool,
}

fn disable_token(ref self: ContractState, token_address: ContractAddress) {
    // ... existing disable logic ...
    
    let affected_pools = self.get_pools_for_token(:token_address);
    self.emit(TokenDisabledWarning { 
        token_address, 
        affected_pools, 
        users_should_unstake: true
    });
}
```



# [L-08] `attestation_window` 업데이트로 인해 보상을 놓칠 수 있음

_인정됨 (Acknowledged)_

`set_attestation_window` 함수가 호출되어 `attestation_window`를 수정하면 변경 사항이 현재 에포크에 직접 적용됩니다.

```rust
fn set_attestation_window(ref self: ContractState, attestation_window: u16) {
    self.roles.only_app_governor();
    assert!(
        attestation_window >= MIN_ATTESTATION_WINDOW, "{}", Error::ATTEST_WINDOW_TOO_SMALL,
    );
    let old_attestation_window = self.attestation_window.read();
    self.attestation_window.write(attestation_window);
    self
        .emit(
            Events::AttestationWindowChanged {
                old_attestation_window, new_attestation_window: attestation_window,
            },
        );
}
```

`attestation_window`는 `target_attestation_block` 및 검증 창 계산에 관여하므로, 이를 수정하면 현재 블록이 변경 전에는 스테이커의 검증 창 내에 있었지만 수정 후에는 현재 블록이 이미 해당 스테이커의 검증 창을 지났을 수 있습니다. 스테이커가 변경 전에 검증을 제출하지 않은 경우 해당 에포크에 대한 보상을 놓치게 됩니다.


**권고 사항**

`set_attestation_window`를 통한 `attestation_window` 조정은 다음 에포크에 적용되도록 하는 것이 좋습니다.

