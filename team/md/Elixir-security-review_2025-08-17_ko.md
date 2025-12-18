# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 잘 알려진 40명 이상의 프리랜서 보안 연구원으로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 $100k 이상을 획득했거나, 여러 번의 우승 경력이 있거나, 우리와의 감사에서 탁월한 성과를 보였습니다. 우리는 검증되고 동기 부여된 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도왔으며, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**ElixirProtocol/move-contracts-v2** 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, **0xTheBlackPanther, Lucasz, shaflow**가 **Elixir** 검토에 참여했습니다. 총 **13**개의 문제가 발견되었습니다.

# Elixir 소개 (About Elixir)

Elixir는 사용자가 deUSD 토큰을 스테이킹하여 프로토콜 생성 보상에서 분배되는 수익을 얻을 수 있는 Sui 기반 프로젝트입니다. 스마트 계약을 사용하여 입금, 쿨다운 기간, 보상 가득(vesting) 및 전용 보상 분배기를 통한 자동 분배를 처리합니다.

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [594a240937946e77e72d03a73a2e8001580ee78c](https://github.com/ElixirProtocol/move-contracts-v2/tree/594a240937946e77e72d03a73a2e8001580ee78c)<br>&nbsp;&nbsp;(ElixirProtocol/move-contracts-v2)

**수정 검토 커밋 해시:**<br>• [301af9582aa2ced9de992858aa0f0213eda71dd5](https://github.com/ElixirProtocol/move-contracts-v2/tree/301af9582aa2ced9de992858aa0f0213eda71dd5)<br>&nbsp;&nbsp;(ElixirProtocol/move-contracts-v2)

# 범위 (Scope)

- `acl.move`
- `admin_cap.move`
- `config.move`
- `deusd.move`
- `deusd_lp_staking.move`
- `deusd_minting.move`
- `locked_funds.move`
- `roles.move`
- `sdeusd.move`
- `staking_rewards_distributor.move`
- `clock_utils.move`
- `cryptography.move`
- `math_u64.move`
- `set.move`

# 발견 사항 (Findings)

# [H-01] 잘못된 타임스탬프 비교로 인한 쿨다운 우회

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명
`sdeusd.move`에서 언스테이킹(unstake)은 `clock::timestamp_ms(clock)`(밀리초)를 사용하여 `clock_utils::timestamp_seconds(clock)`(초)로 설정된 `cooldown_end`와 비교합니다. 그 결과, 다음 확인이 항상 통과됩니다:

```rust
assert!(current_time >= cooldown.cooldown_end || management.cooldown_duration == 0, EInvalidCooldown);
```

이는 current_time(ms)이 cooldown_end(s)보다 약 1000배 더 크기 때문입니다.

`update_user_cooldown` 코드:

```rust
fun update_user_cooldown(
    management: &mut SdeUSDManagement,
    user: address,
    amount: u64,
    clock: &Clock,
) {
    if (management.cooldowns.contains(user)) {
        let cooldown = management.cooldowns.borrow_mut(user);
        cooldown.cooldown_end = clock_utils::timestamp_seconds(clock) + management.cooldown_duration;
        cooldown.underlying_amount = cooldown.underlying_amount + amount;
    } else {
        management.cooldowns.add(user, UserCooldown {
            cooldown_end: clock_utils::timestamp_seconds(clock) + management.cooldown_duration,
            underlying_amount: amount,
        })
    };
}
```

나중에 `unstake` 함수에서:

```rust
[...]
    let current_time = clock::timestamp_ms(clock);
    assert!(
        current_time >= cooldown.cooldown_end || management.cooldown_duration == 0,//@audit-high compared to seconds ??
        EInvalidCooldown
    );
[...]
```

## 권장 사항
unstake에서 `clock_utils::timestamp_seconds`를 사용하여 비교를 수행하십시오.

# [M-01] 블랙리스팅 로직의 불일치

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명
`sdeusd.move`의 전체/소프트 제한 구현은 우회를 허용할 수 있는 몇 가지 불일치를 보여줍니다. 자세한 내용은 아래와 같습니다.

전체 제한(Full restrictions)에는 사용자를 `sdeUSD` 거부 목록(deny list)에 추가하여 기본 메커니즘을 통해 코인을 운영하지 못하도록 하는 것이 포함됩니다. 또한 일부 프로토콜 기능에 대한 액세스를 방지하기 위한 소프트 제한(soft restriction)도 있습니다.

- `deposit` 및 `mint` 함수에는 `is_soft_restricted_staker`에 대한 확인만 있습니다. 그런 다음 sdeUSD가 `receiver`에게 전송됩니다. 그러나 제한된 사용자가 다른 `receiver` 주소를 지정하면 sdeUSD가 차단되지 않은 주소로 전송되므로 여전히 스테이킹할 수 있습니다.
- deposit/mint 함수의 경우 새로 발행된 sdeUSD는 현재 에포크(epoch)에서 완전히 블랙리스트에 오른 주소로 전송될 수 있습니다. 토큰 수신 제한은 다음 에포크에서만 적용되기 때문입니다.
- 사용자가 쿨다운이 시작된 후 제한되는 경우 `unstake`에서도 유사한 경우가 발생할 수 있습니다. 전체 제한에 대한 마지막 확인이 `withdraw_to_silo`에서 발생하므로 해당 사용자는 여전히 자금을 언스테이킹하고 프로토콜을 종료할 수 있습니다.
- 마지막으로 전체 제한은 소프트 제한의 모든 기능과 추가 제한을 구현하므로 `is_soft_restricted_staker`는 내부적으로 `is_full_restricted_staker`를 호출할 수 있습니다. 모든 전체 제한 스테이커는 동시에 소프트 제한되지만(모든 소프트 제한자가 전체 제한되는 것은 아님), 그 반대는 아니기 때문입니다.

## 권장 사항
위의 사항에 따라 추가 제한 확인을 구현하는 것을 고려하십시오.

# [M-02] 첫 번째 스테이커가 고아(Orphaned) 보상을 차지함

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`sdeusd.move` 계약의 `transfer_in_rewards()` 및 `convert_to_shares()` 함수에서 sdeUSD 보유자가 없는 상태에서 보상이 분배되면, 이후 첫 번째 스테이커가 모든 고아 보상을 1:1 전환율로 가져갑니다.

**취약한 로직**을 확인하면 활성 스테이커가 있는지 확인하지 않습니다.
```move
// transfer_in_rewards() - No check for active stakers
public fun transfer_in_rewards(...) {
    // Missing: assert!(total_supply(management) > 0, ENoActiveStakers);
    update_vesting_amount(management, amount, clock);
}

// convert_to_shares() - 1:1 ratio when no existing stakers
if (total_supply == 0 || total_assets == 0) {
    assets  // First staker gets 1:1 regardless of unvested rewards
}
```

이 현상이 **발생하는 방식**의 예는 다음과 같습니다:
1. 프로토콜에 sdeUSD 보유자가 없습니다(total_supply = 0).
2. `transfer_in_rewards()`를 통해 100 deUSD 보상이 분배됩니다.
3. Alice가 가득 기간 동안 1000 deUSD를 스테이킹합니다 → 1000주(shares)를 얻습니다 (1:1 비율).
4. 가득이 완료됩니다 → Alice의 1000주는 이제 1100 deUSD 총 자산을 나타냅니다.
5. Alice는 100 deUSD 수익(고아 커뮤니티 보상)을 위해 상환합니다.

스테이커 풀이 비어 있을 때 보상 분배를 방지하는 검증이 없으며, 1:1 전환과 결합되어 미가득 자산을 무시합니다.

### 영향

**빈 기간 이후의 첫 번째 스테이커가 타이밍 조작을 통해 모든 고아 커뮤니티 보상을 차지합니다.**

## 권장 사항

활성 스테이커가 없을 때 보상 분배를 방지하십시오.

```move
public fun transfer_in_rewards(...) {
    assert!(total_supply(management) > 0, ENoActiveStakers);
    update_vesting_amount(management, amount, clock);
    // ... rest of function
}
```

# [L-01] 전체 인출 시 불완전한 스토리지 정리

_해결됨 (Resolved)_

`locked_funds.move` 함수 `update_user_collateral_coin_types()`에서 사용자가 특정 유형의 모든 토큰을 인출할 때 추적 목록은 업데이트되지만 기본 `BalanceStore<T>` 동적 필드 항목은 스토리지에서 제거되지 않습니다.

이것이 **발생하는 방식**은 다음과 같습니다:
1. 사용자 입금 ETH → `get_or_create_balance_store_mut`를 호출하여 입금 시 `BalanceStore<ETH>` 동적 필드 생성.
2. 사용자가 모든 ETH를 인출할 때 → 잔액이 0이 됨.
3. `update_user_collateral_coin_types()`가 추적 목록에서 ETH를 제거함.
4. 잔액이 0인 `BalanceStore<ETH>`는 스토리지에 영구적으로 남음.

**근본 원인**은 정리 로직이 누락되었기 때문입니다. 추적만 업데이트되고 실제 스토리지는 유지됩니다.

이로 인한 구체적인 영향 중 하나는 빈 잔액 항목이 정리되지 않고 시간에 따라 누적되므로 **점진적인 스토리지 팽창(bloat)**입니다.

권장 사항으로 잔액이 0에 도달하면 동적 필드 제거를 추가하십시오. `update_user_collateral_coin_types` else 부분에 아래 코드를 추가하십시오:

```move
} else {
    // Current: remove from tracking
    coin_types.remove(&coin_type);
    
    // ADD: remove empty storage
    let balance_key = get_balance_store_key<T>(owner);
    if (df::exists_(&management.id, balance_key)) {
        df::remove<vector<u8>, BalanceStore<T>>(&mut management.id, balance_key);
    }
}
```

# [L-02] 잘못된 로직으로 중복 관리인 추가 허용

_해결됨 (Resolved)_

`deusd_minting.move` 함수 `add_custodian_address_internal()`에서 잘못된 어설션(assertion) 로직으로 인해 중복 관리인 주소 추가를 방지하지 못합니다.

아래 코드를 확인하면 OR 연산자를 사용합니다.
```move
assert!(custodian != @0x0 || management.custodian_addresses.contains(custodian), EInvalidCustodianAddress);
```

OR 조건은 관리인이 이미 존재할 때 통과하며, 이는 의도된 동작과 반대입니다.

이 현상이 **발생하는 방식**의 예는 다음과 같습니다:
1. 관리자가 @alice를 관리인으로 성공적으로 추가합니다.
2. 관리자가 @alice를 다시 추가합니다.
3. 어설션 평가: `(@alice != @0x0) || (contains(@alice))` = `true || true` = `true`.
4. 중복을 거부해야 할 때 검증이 통과됩니다.

이는 잘못된 논리 연산자와 누락된 부정(negation) 때문에 발생했습니다. 주소가 이미 존재할 때 수락하는 것이 아니라 거부했어야 합니다.

이로 인한 영향은 명확한 오류 대신 중복 관리인이 수락되어 혼란과 잠재적인 스토리지 낭비를 초래한다는 것입니다.

권장 사항으로 적절한 검증 로직으로 교체하십시오:

```move
assert!(custodian != @0x0, EZeroAddress);
assert!(!management.custodian_addresses.contains(custodian), EDuplicateCustodian);
```

# [L-03] ACL의 유령 멤버(Ghost members)로 인한 잠재적 스토리지 팽창

_해결됨 (Resolved)_

acl.move의 `remove_role` 및 `set_roles` 함수에서 발생하는 버그로, 모든 역할이 취소된 멤버(roles=0)가 자동 제거 없이 `roles_by_member` LinkedTable에 남아 있어 반복적인 추가/삭제 작업을 통해 쓸모없는 항목이 축적됩니다. 이는 스토리지를 팽창시키고 `get_members`에서 가스 비용/반복 시간을 증가시켜 시간이 지남에 따라 성능을 저하시킵니다.

권장 사항으로 `remove_role` 및 `set_roles`에 로직을 추가하여 업데이트 후 roles==0인지 확인하고 `remove_member`를 호출하여 빈 항목을 제거함으로써 기존 기능에 영향을 주지 않고 효율적인 테이블 관리를 보장하십시오.

# [L-04] `has_role`에서 누락된 역할 범위 확인

_해결됨 (Resolved)_

`sources/acl.move`의 has_role 함수는 다른 역할 함수와 달리 역할 범위 검증이 부족합니다. 이러한 불일치로 인해 유효하지 않은 역할 값(≥128)이 전달되면 런타임 중단(abort)이 발생할 수 있습니다. 이 문제는 외부 계약이 사용자 제어 역할 매개변수를 전달하거나 검증되지 않은 입력으로 크로스 계약 호출을 수행하는 향후 통합에서 발생할 수 있습니다.

영향 -> 우아한 오류 처리 대신 런타임 트랜잭션 중단이 발생하여 일관성 없는 API 동작으로 이어집니다.

권장 사항 -> add_role 및 remove_role과의 일관성을 위해 `has_role` 함수 시작 부분에 `assert!(role < 128, EInvalidRole);`을 추가하십시오.

# [L-05] 사용할 수 없는 뷰(View) 함수

_해결됨 (Resolved)_

`cooldown_underlying_amount` 및 `cooldown_end_time` 함수는 `UserCooldown`을 가져와 `underlying_amount` 및 `cooldown_end` 필드를 반환합니다.
```rust
/// Get the underlying amount from a cooldown
public fun cooldown_underlying_amount(cooldown: &UserCooldown): u64 {
    cooldown.underlying_amount
}

/// Get the cooldown end time
public fun cooldown_end_time(cooldown: &UserCooldown): u64 {
    cooldown.cooldown_end
}
```
그러나 `UserCooldown`은 외부 모듈에서 직접 액세스할 수 없는 `SdeUSDManagement` 내의 필드이므로 이 두 뷰 함수는 실제로 사용할 수 없습니다. 또한 모듈에는 `UserCooldown`에 대한 참조를 반환하는 함수가 포함되어 있지 않습니다. 따라서 이 두 함수는 실제로 사용할 수 없습니다.

이 두 함수를 완전히 제거하는 것이 좋습니다. 또는 `get_user_cooldown_info` 함수에서 사용할 도우미 함수로 변환할 수 있습니다.

# [L-06] `deposit` 함수에 자산 지원 확인이 누락됨

_해결됨 (Resolved)_

`deposit` 함수는 누구나 `management`로 코인을 보낼 수 있도록 허용하며, 주로 리밸런싱 후 인출에 필요한 토큰을 주입하기 위한 라우터용으로 의도되었습니다.
```rust
public fun deposit<T>(
    management: &mut DeUSDMintingManagement,
    global_config: &GlobalConfig,
    coins: Coin<T>,
    ctx: &TxContext,
) {
    assert_package_version_and_initialized(management, global_config);
    let amount = coins.value();
    assert!(amount > 0, EInvalidAmount);

    let balance = get_or_create_balance_store_mut<T>(management);
    balance.join(coin::into_balance(coins));

    event::emit(Deposit {
        depositor: ctx.sender(),
        asset: type_name::get<T>(),
        amount,
    });
}
```
그러나 지원되지 않는 자산을 실수로 수락하지 않으려면 여기에서 `is_support_asset` 확인을 수행하는 것이 좋습니다. 이러한 자산은 전송하려면 여전히 `collateral_manager` 및 `AdminCap`이 필요하기 때문입니다.

# [L-07] `AdminCap` 객체를 전송하는 함수 누락

_해결됨 (Resolved)_

`AdminCap`은 프로토콜의 권한 자격 증명입니다. `init` 함수에서 생성되어 발신자에게 전송됩니다.
```rust
public struct AdminCap has key {
    id: UID,
}
```
그러나 `AdminCap` 객체에는 `key` 기능만 있으므로 모듈 외부로 `public_transfer`를 통해 자유롭게 전송할 수 없습니다. 또한 모듈 내에는 `AdminCap` 보유자가 소유권을 이전할 수 있는 함수가 없습니다. 이는 소유자 이전 기능이 누락되었음을 나타냅니다.

`admin_cap` 모듈에 `AdminCap`의 소유권을 이전하는 함수를 추가하는 것이 좋습니다.

# [L-08] 토큰 전송을 통한 소프트 제한 우회

_인정됨 (Acknowledged)_

`sdeusd.move` 함수 `add_to_blacklist()` 함수에서 소프트 제한된 사용자는 직접 스테이킹만 차단되지만 전송을 통해 sdeUSD 토큰을 받을 수 있어 제한을 완전히 우회할 수 있습니다.

**취약한 코드**를 확인해 보겠습니다:
```move
if (is_full_blacklisting) {
    coin::deny_list_v2_add(deny_list, &mut management.deny_cap, target, ctx);
} else {
    management.soft_restricted_stakers.add(target);  // Only internal tracking
    // Missing: deny_list addition for soft restrictions @audit-poc
}
```

이 현상이 **발생하는 방식**은 다음과 같습니다:
1. Alice가 소프트 블랙리스트에 오릅니다(`deposit()` 호출 불가).
2. Bob은 정상적으로 deUSD를 스테이킹하고 sdeUSD 주식을 얻습니다.
3. Bob은 `transfer::public_transfer()`를 통해 sdeUSD 토큰을 Alice에게 전송합니다.
4. Alice는 제한 확인을 트리거하지 않고 스테이크를 성공적으로 늘렸습니다.

이는 소프트 제한이 직접적인 프로토콜 상호 작용만 방지하고 토큰 수령은 방지하지 않음을 의미합니다.

**영향**

**소프트 제한된 사용자는 공모 또는 토큰 선물을 통해 스테이킹 제한을 우회할 수 있습니다.**

**권장 사항**

소프트 제한된 사용자를 코인 별도 거부 목록에 추가하여 직접 전송도 비활성화하십시오. 이렇게 하면 다른 사용자가 블랙리스트에 오른 사용자에게 sdeUSD를 보내는 것이 허용되지 않습니다.

# [L-09] 쿨다운 타이머 재설정으로 인한 사용자 인출 지연

_인정됨 (Acknowledged)_

`deusd_lp_staking.move` 함수 `unstake()`에서 각 언스테이킹 작업은 새로 언스테이킹된 금액뿐만 아니라 모든 쿨링(cooling) 토큰에 대한 쿨다운 타임스탬프를 덮어씁니다.

이 **취약한 코드**를 확인하면 모든 쿨링 토큰에 대한 타이머가 재설정됩니다.
```move
stake_data.cooldown_start_timestamp = clock_utils::timestamp_seconds(clock);
// ↑ Resets timer for ALL cooling tokens
```

이 현상이 **발생하는 방식**은 다음과 같습니다:
1. 사용자가 0일차에 1000 토큰을 언스테이킹합니다 (30일차에 인출 가능).
2. 사용자가 20일차에 100 토큰을 더 언스테이킹합니다 (독립적이라고 생각함).
3. 쿨다운 재설정: 이제 모든 1100 토큰은 50일차(20일차부터 30일)에 인출 가능합니다.

여기서 **근본 원인**은 단일 타임스탬프가 배치별 추적 대신 모든 쿨링 토큰에서 공유된다는 것입니다.

구체적인 영향은 여러 언스테이킹 트랜잭션이 쿨다운 기간을 재설정하여 발생하는 사용자 혼란과 의도하지 않은 인출 지연입니다.

**권장 사항**

토큰이 이미 쿨링 중인 경우 쿨다운 재설정을 방지하십시오.

```move
// In unstake() function:
if (stake_data.cooling_down_amount == 0) {
    stake_data.cooldown_start_timestamp = clock_utils::timestamp_seconds(clock);
}
```

# [L-10] 쿨다운 기간 변경이 소급 적용됨

_인정됨 (Acknowledged)_

`deusd_lp_staking.move` 함수 `withdraw()`에서 쿨다운 기간은 사용자가 언스테이킹할 때 고정되는 것이 아니라 인출 시 현재 매개변수에서 읽습니다. 관리자는 쿨다운 기간을 중간에 변경할 수 있으며, 이는 이미 쿨다운을 시작한 사용자에게 영향을 미칩니다.

이 **시나리오**를 확인해 보겠습니다:
1. 사용자가 쿨다운 = 30일일 때 1000 토큰을 언스테이킹합니다.
2. 사용자는 30일차에 인출을 예상합니다.
3. 관리자가 20일차에 쿨다운을 60일로 업데이트합니다.
4. 사용자가 30일차에 인출을 시도합니다 → `assert!(current_time >= stake_data.cooldown_start_timestamp + params.cooldown, ECooldownNotOver);`에 60일이 필요하므로 실패합니다.

**근본 원인**은 `params.cooldown`이 사용자가 쿨다운을 시작했을 때 활성 상태였던 기간이 아니라 항상 현재 전역 설정을 읽는다는 것입니다.

명확하고 구체적인 영향 중 하나는 **예측할 수 없는 인출 시간**이며, 관리자가 락업(lockup) 기간을 소급하여 연장할 수 있으므로 사용자는 자금을 언제 사용할 수 있을지 확실하게 계획할 수 없습니다.

**권장 사항**

언스테이킹 시 쿨다운 기간을 고정하는 것이 좋습니다:

```move
public struct StakeData has store, copy, drop {
    staked_amount: u64,
    cooling_down_amount: u64,
    cooldown_start_timestamp: u64,
    locked_cooldown_duration: u64,  // ADD: Store duration when unstaking
}

// In unstake():
stake_data.locked_cooldown_duration = params.cooldown;

// In withdraw():
assert!(current_time >= stake_data.cooldown_start_timestamp + stake_data.locked_cooldown_duration, ECooldownNotOver);
```

또 다른 해결책은 쿨다운 기간이 언제든지 변경될 수 있고 소급 적용될 수 있음을 명확하게 문서화하여 사용자가 언스테이킹하기 전에 이 위험을 이해하도록 하는 것입니다.
