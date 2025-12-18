
# Pashov Audit Group 소개

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 입증된 인재들입니다. 대부분은 공개 대회 보상으로 10만 달러 이상을 획득했거나, 다회 우승자이거나, 저희와의 감사에서 진정으로 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도우면서, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

<p><strong>yield-basis/yb-core</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, Pashov Audit Group이 <strong>YieldBasis</strong> 검토에 참여했습니다. 총 <strong>13</strong>개의 이슈가 발견되었습니다.</p>

# YieldBasis 소개

<p>YieldBasis는 거래 수수료를 얻으면서 가치가 기초 자산을 추적하도록 유동성 포지션을 활용하여 비영구적 손실을 제거하는 것을 목표로 합니다. Curve 스타일 AMM 내에서 레버리지를 동적으로 조정함으로써 이 접근 방식은 기초 자산의 가격을 면밀히 추적하면서 지속 가능한 수익을 달성합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [d29f47000c80851bb6c4ad92463b8ddb48cad944](https://github.com/yield-basis/yb-core/tree/d29f47000c80851bb6c4ad92463b8ddb48cad944)<br>&nbsp;&nbsp;(yield-basis/yb-core)

**수정 검토 커밋 해시:**<br>• [d9b8eebf84b2bca4b761a86080fe381ffff6a0ba](https://github.com/yield-basis/yb-core/tree/d9b8eebf84b2bca4b761a86080fe381ffff6a0ba)<br>&nbsp;&nbsp;(yield-basis/yb-core)

# 범위

- `LT.vy`
- `AMM.vy`
- `Factory.vy`

# 발견 사항

# [M-01] `new_total_value`가 음수가 아니라고 가정함 (`new_total_value` is assumed to be non-negative)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`_calculate_values`가 수행될 때, `value_change`를 기반으로 `new_total_value`를 계산하고 `uint256`으로 변환되는 `total`로 반환합니다.

```python
@internal
@view
def _calculate_values(p_o: uint256) -> LiquidityValuesOut:
    prev: LiquidityValues = self.liquidity
    staker: address = self.staker
    staked: int256 = 0
    if staker != empty(address):
        staked = convert(self.balanceOf[self.staker], int256)
    supply: int256 = convert(self.totalSupply, int256)

    f_a: int256 = convert(
        10**18 - (10**18 - self.min_admin_fee) * self.sqrt(convert(10**36 - staked * 10**36 // supply, uint256)) // 10**18,
        int256)

    cur_value: int256 = convert((staticcall self.amm.value_oracle()).value * 10**18 // p_o, int256)
    prev_value: int256 = convert(prev.total, int256)
>>> value_change: int256 = cur_value - (prev_value + prev.admin)

    v_st: int256 = convert(prev.staked, int256)
    v_st_ideal: int256 = convert(prev.ideal_staked, int256)
    # ideal_staked is set when some tokens are transferred to staker address

>>> dv_use: int256 = value_change * (10**18 - f_a) // 10**18
    prev.admin += (value_change - dv_use)

    dv_s: int256 = dv_use * staked // supply
    if dv_use > 0:
        dv_s = min(dv_s, max(v_st_ideal - v_st, 0))

>>> new_total_value: int256 = prev_value + dv_use
    new_staked_value: int256 = v_st + dv_s

    # Solution of:
    # staked - token_reduction       new_staked_value
    # -------------------------  =  -------------------
    # supply - token_reduction         new_token_value
    token_reduction: int256 = unsafe_div(staked * new_total_value - new_staked_value * supply, new_total_value - new_staked_value)
    # token_reduction = 0 if nothing is staked
    # XXX need to consider situation when denominator is very close to zero

    # Supply changes each time:
    # value split reduces the amount of staked tokens (but not others),
    # and this also reduces the supply of LP tokens

    return LiquidityValuesOut(
        admin=prev.admin,
>>>     total=convert(new_total_value, uint256),
        ideal_staked=prev.ideal_staked,
        staked=convert(new_staked_value, uint256),
        staked_tokens=convert(staked - token_reduction, uint256),
        supply_tokens=convert(supply - token_reduction, uint256)
    )
```

`dv_use`가 `prev_value`를 초과하여 `new_total_value`가 음수가 될 수 있습니다. 이 경우 `uint256`으로 변환하면 되돌려집니다(revert).

## 권장 사항

`new_total_value`가 음수가 되면 0으로 설정하는 것을 고려하십시오.

# [M-02] 스테이커가 호출자일 때 `withdraw`가 `staked` 데이터를 줄이지 않음 (`withdraw` does not reduce `staked` data when staker is caller)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`withdraw`가 호출되면 소각된 지분(shares)의 양에 따라 `liquidity.total`을 업데이트하지만, 호출자가 `staker`인지 확인하지 않습니다. `staker`가 `withdraw` 작업을 호출하면 `staked` 값도 업데이트하고 줄여야 합니다.

```python
@external
@nonreentrant
def withdraw(shares: uint256, min_assets: uint256, receiver: address = msg.sender) -> uint256:
    """
    @notice Method to withdraw assets (e.g. like BTC) by spending shares (e.g. like yield-bearing BTC)
    @param shares Shares to withdraw
    @param min_assets Minimal amount of assets to receive (important to calculate to exclude sandwich attacks)
    @param receiver Receiver of the shares who is optional. If not specified - receiver is the sender
    """
    assert shares > 0, "Withdrawing nothing"

    amm: LevAMM = self.amm
    liquidity_values: LiquidityValuesOut = self._calculate_values(self._price_oracle_w())
    supply: uint256 = liquidity_values.supply_tokens
    self.liquidity.admin = liquidity_values.admin
    self.liquidity.total = liquidity_values.total
    self.liquidity.staked = liquidity_values.staked
    self.totalSupply = supply
    staker: address = self.staker
    if staker != empty(address):
        self.balanceOf[staker] = liquidity_values.staked_tokens
    state: AMMState = staticcall amm.get_state()

    admin_balance: uint256 = convert(max(liquidity_values.admin, 0), uint256)

    withdrawn: Pair = extcall amm._withdraw(10**18 * liquidity_values.total // (liquidity_values.total + admin_balance) * shares // supply)
    assert extcall COLLATERAL.transferFrom(amm.address, self, withdrawn.collateral)
    crypto_received: uint256 = extcall COLLATERAL.remove_liquidity_fixed_out(withdrawn.collateral, 0, withdrawn.debt, 0)

    self._burn(msg.sender, shares)  # Changes self.totalSupply
>>> self.liquidity.total = liquidity_values.total * (supply - shares) // supply
    if liquidity_values.admin < 0:
        # If admin fees are negative - we are skipping them, so reduce proportionally
        self.liquidity.admin = liquidity_values.admin * convert(supply - shares, int256) // convert(supply, int256)
    assert crypto_received >= min_assets, "Slippage"
    assert extcall STABLECOIN.transfer(amm.address, withdrawn.debt)
    assert extcall DEPOSITED_TOKEN.transfer(receiver, crypto_received)

    log Withdraw(sender=msg.sender, receiver=receiver, owner=msg.sender, assets=crypto_received, shares=shares)
    return crypto_received
```

`liquidity.staked` 값이 제대로 업데이트되지 않으면 `_calculate_values`가 호출될 때 잘못된 값을 사용하여 잘못된 `staked_tokens` 및 `supply_tokens`가 발생합니다.

```python
@internal
@view
def _calculate_values(p_o: uint256) -> LiquidityValuesOut:
    prev: LiquidityValues = self.liquidity
    staker: address = self.staker
    staked: int256 = 0
    if staker != empty(address):
        staked = convert(self.balanceOf[self.staker], int256)
    supply: int256 = convert(self.totalSupply, int256)

    f_a: int256 = convert(
        10**18 - (10**18 - self.min_admin_fee) * self.sqrt(convert(10**36 - staked * 10**36 // supply, uint256)) // 10**18,
        int256)

    cur_value: int256 = convert((staticcall self.amm.value_oracle()).value * 10**18 // p_o, int256)
    prev_value: int256 = convert(prev.total, int256)
    value_change: int256 = cur_value - (prev_value + prev.admin)

    v_st: int256 = convert(prev.staked, int256)
    v_st_ideal: int256 = convert(prev.ideal_staked, int256)
    # ideal_staked is set when some tokens are transferred to staker address

    dv_use: int256 = value_change * (10**18 - f_a) // 10**18
    prev.admin += (value_change - dv_use)

    dv_s: int256 = dv_use * staked // supply
    if dv_use > 0:
        dv_s = min(dv_s, max(v_st_ideal - v_st, 0))

    new_total_value: int256 = prev_value + dv_use
    new_staked_value: int256 = v_st + dv_s

    # Solution of:
    # staked - token_reduction       new_staked_value
    # -------------------------  =  -------------------
    # supply - token_reduction         new_token_value
>>> token_reduction: int256 = unsafe_div(staked * new_total_value - new_staked_value * supply, new_total_value - new_staked_value)
    # token_reduction = 0 if nothing is staked
    # XXX need to consider situation when denominator is very close to zero

    # Supply changes each time:
    # value split reduces the amount of staked tokens (but not others),
    # and this also reduces the supply of LP tokens

    return LiquidityValuesOut(
        admin=prev.admin,
        total=convert(new_total_value, uint256),
        ideal_staked=prev.ideal_staked,
        staked=convert(new_staked_value, uint256),
>>>     staked_tokens=convert(staked - token_reduction, uint256),
>>>     supply_tokens=convert(supply - token_reduction, uint256)
    )
```

## 권장 사항

스테이커가 `withdraw`를 호출하는 경우 스테이커의 `liquidity.staked`를 업데이트하거나, `staker`가 `withdraw` 작업을 호출하지 못하도록 하십시오.

# [M-03] 포지션 손실 중 토큰 리베이스 계산 오류 (Token rebase miscalculation during position losses)

_해결됨_

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

포지션이 손실 상태인 경우(dv_use < 0), 토큰 감소는 이론적으로 0이어야 합니다:

```python
@internal
@view
def _calculate_values(p_o: uint256) -> LiquidityValuesOut:
    ...
    token_reduction: int256 = unsafe_div(staked * new_total_value - new_staked_value * supply, new_total_value - new_staked_value)
    ...
```
계산의 분자는 0으로 평가되어야 합니다:

```
  staked * new_total_value - new_staked_value * supply 
= staked * (prev_value + dv_use) - (v_st + dv_s) * supply
= staked * (prev_value + dv_use) - (v_st + dv_use * staked / supply) * supply
= staked * prev_value - v_st * supply
= staked * prev_value - (staked * prev_value / supply) * supply 
(because v_st / staked == prev_value / supply => v_st = staked *  prev_value / supply)
= 0
```

그러나 정수 나눗셈 반올림으로 인해 계산 결과가 0이 아닌 값이 되어 잘못된 토큰 감소가 발생할 수 있습니다. 이는 스테이킹된 유동성 공급자와 스테이킹되지 않은 유동성 공급자 간의 불공정한 가치 분배로 이어질 수 있습니다.

## 권장 사항

포지션 손실 시 `token_reduction` = 0으로 설정하십시오.

# [M-04] 스테이커 주소 변경 시 상태가 업데이트되지 않음 (State not updated when staker address changes)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

LT 계약의 `set_staker` 함수를 통해 스테이커 주소를 변경할 때, 코드는 중요한 회계 변수인 `liquidity.staked` 및 `liquidity.ideal_staked`를 업데이트하지 못합니다. 이로 인해 프로토콜에 회계 불일치가 발생합니다.

```python
    @external
    @nonreentrant
    def set_staker(staker: address):
        self._check_admin()
        self.staker = staker
        log SetStaker(staker=staker)
```

이로 인해 다음 간의 불일치가 생성됩니다:

- 새 스테이커를 가리키는 스테이커 주소(`self.staker`)
- 여전히 이전 스테이커의 값을 반영하는 회계 변수(`liquidity.staked` 및 `liquidity.ideal_staked`)

스테이커가 변경된 후 `_calculate_values`가 실행되면, `staked`는 새 스테이커 주소의 토큰 잔액입니다. `v_st`는 이전 스테이커의 과거 회계 값을 사용합니다.

`token_reduction` 계산:

```python
token_reduction: int256 = unsafe_div(staked * new_total_value - new_staked_value * supply, new_total_value - new_staked_value)
```

다음 시나리오를 고려해 보십시오:

- 관리자가 `set_staker`를 호출하여 self.staker를 Addr_A(많은 토큰 보유)에서 Addr_B(매우 적은 토큰 보유)로 변경합니다.
- `liquidity.staked` (가치 변수)는 Addr_A가 스테이커였을 때 스테이킹된 부분에 발생/할당된 가치를 반영하여 높게 유지됩니다.
- 다음에 `_calculate_values`가 실행될 때:
- `staked` = self.balanceOf[Addr_B] (매우 낮은 토큰 수)를 사용합니다.
- `new_staked_value`를 사용하는데, 이는 높은 과거 `liquidity.staked`에서 파생됩니다.
- 비율 `staked / supply` (낮음 / 전체)는 비율 `new_staked_value / new_total_value` (높음 / 전체)보다 훨씬 작습니다.

토큰 비율이 가치 비율에 비해 너무 낮으므로 시스템은 스테이커(Addr_B)가 보유한 토큰 수를 늘려야 합니다.
시스템은 단순히 스테이커 주소가 변경되었다는 이유만으로 허공에서 새 토큰을 발행하여 새 스테이커(Addr_B)에게 할당합니다. 이 새로 발행된 가치는 다른 모든 토큰 보유자를 희석시키는 데서 나옵니다.

## 권장 사항

스테이커 주소에 변경이 있는 경우 `staked` 및 `ideal_staked`를 업데이트하십시오.

# [M-05] `_transfer()`를 통해 리베이스 우회 가능 (Rebase bypass possible through `_transfer()`)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

스테이킹된 유동성 회계를 정확하게 유지하는 데 중요한 토큰 감소 메커니즘은 스테이커에게 직접 입금하는 경우에만 적용됩니다.

```python
# File: LT.vy
...
238:     token_reduction: int256 = unsafe_div(staked * new_total_value - new_staked_value * supply, new_total_value - new_staked_value)
...
246:     return LiquidityValuesOut(
247:         admin=prev.admin,
248:         total=convert(new_total_value, uint256),
249:         ideal_staked=prev.ideal_staked,
250:         staked=convert(new_staked_value, uint256),
251:@>       staked_tokens=convert(staked - token_reduction, uint256),
252:@>       supply_tokens=convert(supply - token_reduction, uint256)
253:     )
```

그러나 사용자가 `non-staker` 계정에 입금한 후 나중에 지분을 스테이커에게 전송하는 경우, `_transfer` 함수의 재계산은 토큰 감소 로직을 호출하지 않고 전송 금액에 따라 스테이킹된 유동성만 조정하므로 토큰 감소가 적용되지 않습니다. 이는 `_transfer` 함수에서 볼 수 있습니다:

```python
# File: LT.vy
548: @internal
549: def _transfer(_from: address, _to: address, _value: uint256):
...
564:         elif _to == staker:
565:             # Increase the staked part
566:             d_staked_value: uint256 = liquidity.total * _value // liquidity.supply_tokens
567:@>           liquidity.staked += d_staked_value
568:             if liquidity.staked_tokens > 10**10:
569:                 liquidity.ideal_staked = liquidity.ideal_staked * (liquidity.staked_tokens + _value) // liquidity.staked_tokens
570:             else:
571:                 # To exclude division by zero and numerical noise errors
572:                 liquidity.ideal_staked += d_staked_value
573:@>       self.liquidity.staked = liquidity.staked
574:         self.liquidity.ideal_staked = liquidity.ideal_staked
575: 
576:     self.balanceOf[_from] -= _value
577:     self.balanceOf[_to] += _value
...
```

다음 테스트는 비스테이커에게 입금한 다음 `staker`에게 전송하면 `staked`와 `staked_balance`가 모두 0이 아닌 상태가 됨(토큰 감소 우회)을 보여줍니다.

```python
# File: tests/lt/test_unitary.py
def test_deposit_then_transfer_to_staker(yb_lt, collateral_token, yb_allocated, seed_cryptopool, yb_staker, accounts, admin):
    user = accounts[0]
    p = 100_000
    amount = 10**18

    with boa.env.prank(admin):  # Set the staker
        yb_lt.set_staker(yb_staker.address)
        assert yb_lt.staker() == yb_staker.address

    # First deposit just to populate the pool and set the staker
    collateral_token._mint_for_testing(accounts[1], amount)
    with boa.env.prank(accounts[1]):
        shares = yb_lt.deposit(amount, p * amount, int(amount * 0.9999))

    # 1. Deposit but staking this time using the deposit -> transfer method
    collateral_token._mint_for_testing(user, amount)
    with boa.env.prank(user):
        shares = yb_lt.deposit(amount, p * amount, int(amount * 0.9999))
    with boa.env.prank(user): # Transfer to staker
        yb_lt.transfer(yb_staker, shares)

    # 2. Rebase mechanism has applied but the `staked` is not zero
    post_values = yb_lt.internal._calculate_values(100_000 * 10**18)
    assert post_values[3] > 0 # staked > 0
    assert post_values[4] > 0 # staked_tokens > 0
```

사용자가 `staker` 계정에 직접 입금했다면 토큰 감소가 적용되어 스테이킹된 금액이 0이 되었을 것이므로 불일치가 있습니다.

```python
# File: tests/lt/test_unitary.py
def test_deposit_directly_to_staker(yb_lt, collateral_token, yb_allocated, seed_cryptopool, yb_staker, accounts, admin):
    user = accounts[0]
    p = 100_000
    amount = 10**18

    # First deposit just to populate the pool and set the staker
    with boa.env.prank(admin):  # Set the staker
        yb_lt.set_staker(yb_staker.address)
        assert yb_lt.staker() == yb_staker.address
    collateral_token._mint_for_testing(accounts[1], amount)
    with boa.env.prank(accounts[1]):
        shares = yb_lt.deposit(amount, p * amount, int(amount * 0.9999))

    # 1. Deposit but staking directly to staker
    collateral_token._mint_for_testing(user, amount)
    with boa.env.prank(user):
        # Deposit
        shares = yb_lt.deposit(amount, p * amount, int(amount * 0.9999), yb_lt.staker())
        assert shares == yb_lt.balanceOf(yb_lt.staker())

    # 2. Rebase mechanism has applied so the `staked` is zero
    post_values = yb_lt.internal._calculate_values(100_000 * 10**18)
    assert post_values[3] == 0 # staked == 0
    assert post_values[4] == 0 # staked_tokens == 0
```

## 권장 사항

입금이 스테이커에게 직접 발생하든 후속 전송을 통해 발생하든 토큰 감소 메커니즘이 일관되게 적용되도록 하십시오.

# [M-06] `set_rate()`가 누적 수수료를 재설정하여 수수료 손실 발생 (`set_rate()` resets accrued fees causing fee loss)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`AMM::set_rate` 함수는 시간이 지남에 따라 차입 부채에 적용되는 이자율을 변경하는 데 사용됩니다. 내부적으로 현재 시간과 그 시점까지 누적된 이자를 기반으로 요율 승수(`rate_mul`)를 재설정합니다.

```python
# File: AMM.vy
172: def set_rate(rate: uint256) -> uint256:
...
178:     assert msg.sender == DEPOSITOR, "Access"
179:     rate_mul: uint256 = self._rate_mul()
180:@>   self.rate_mul = rate_mul
181:@>   self.rate_time = block.timestamp
182:     self.rate = rate
183:     log SetRate(rate=rate, rate_mul=rate_mul, time=block.timestamp)
184:     return rate_mul
```

그러나 **`AMM::collect_fees()`가 사전에 호출되지 않으면** 이전 요율의 누적 이자가 **징수되지 않으며**, 이는 이전 기간 동안 프로토콜에 지불해야 할 수수료가 사실상 지워짐을 의미합니다.

이는 `AMM::collect_fees()`가 다음 공식을 사용하는 `AMM::_debt_w()`에 의존하기 때문에 발생합니다.

```python
# File: AMM.vy
196:     debt: uint256 = self.debt * rate_mul // self.rate_mul
```

이 공식에서:

- `self.rate_mul`은 `set_rate()` 중에 업데이트됩니다.
- 수수료가 징수되기 전에 `set_rate()`가 호출되면 기본 `rate_mul`이 *새* 값으로 재설정되고 누적 델타가 손실됩니다.
- 이후 `collect_fees()`를 호출하면 **이전 이자 기간을 고려하지 않고** 새 요율이 설정된 후의 시간에 대해서만 수수료를 계산합니다.

제공된 테스트는 이 동작을 확인합니다:

- 이자가 발생한 후 `admin_fees()`는 수수료 > 0을 올바르게 반영합니다(2단계).
- `set_rate`가 호출된 후 `admin_fees()`는 0으로 떨어지며(4단계), 요율 재설정으로 인해 수수료가 조용히 사라졌음을 증명합니다.

```python
def test_fees_loss_on_set_rate(token_mock, price_oracle, amm_deployer, accounts, admin):
    # Deploy tokens and AMM (using 18 decimals for simplicity)
    stablecoin = token_mock.deploy('Stablecoin', 'USD', 18)
    collateral_decimals = 18
    collateral_token = token_mock.deploy('Collateral', 'COL', collateral_decimals)
    with boa.env.prank(admin):
        price_oracle.set_price(10**18)
        amm = amm_deployer.deploy(
            admin,
            stablecoin.address,
            collateral_token.address,
            2 * 10**18,    # leverage = 2x
            10**16,        # fee
            price_oracle.address
        )
        amm.set_rate(10**18)  # Set initial rate
    # Fund AMM with tokens
    with boa.env.prank(admin):
        stablecoin._mint_for_testing(amm.address, 10**12 * 10**18)
        stablecoin._mint_for_testing(admin, 10**12 * 10**18)
        collateral_token._mint_for_testing(admin, 10**12 * 10**18)
        stablecoin.approve(amm.address, 2**256 - 1)
        collateral_token.approve(amm.address, 2**256 - 1)
    
    # 1. Make a deposit to generate some minted debt (and thus potential fees)
    d_collateral = 10**18
    d_debt = 10**17
    with boa.env.prank(admin):
        amm._deposit(d_collateral, d_debt)

    # 2. Simulate passage of time to increase the accrued interest
    boa.env.time_travel(60 * 60 * 24)
    fees_before_set_rate = amm.admin_fees()
    assert fees_before_set_rate > 0

    # 3. Call set_rate without prior fee collection, which resets the rate multiplier
    new_rate = 11**17  # arbitrary new rate
    with boa.env.prank(admin):
        amm.set_rate(new_rate)
    
    new_fees = amm.admin_fees()

    # 4. The test asserts that the new computed fees are lower than before,
    # proving that fees accrued (if any) are lost when set_rate is called without collecting fees.
    assert new_fees == 0
    assert fees_before_set_rate > new_fees
```

## 권장 사항

요율 업데이트 전에 수수료 징수를 강제하십시오. `collect_fees()`가 먼저 호출되도록 `set_rate()`에 로직을 추가하십시오. 예:

```python
# File: AMM.vy
def set_rate(rate: uint256) -> uint256:
    self.collect_fees()
    ...
```

# [M-07] `withdraw_admin_fees()`의 토큰 계산 오류로 지분 인플레이션 발생 (Token miscalculation in `withdraw_admin_fees()` inflates shares)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

LT 계약에서 `withdraw_admin_fees` 함수는 수수료 수령자에게 Yield Basis 토큰을 발행하도록 설계되었습니다. 그러나 과도한 토큰 발행을 초래하는 계산 오류가 있습니다. 문제는 `to_mint` 계산에 있습니다:

```python
def withdraw_admin_fees():
    ...
    to_mint: uint256 = v.supply_tokens * new_total // v.total
    ...
```

이 공식은 관리자 수수료를 나타내는 증분 차이가 아니라 전체 새로운 공급과 동일한 양을 발행합니다. 이는 토큰 공급의 인플레이션과 기존 보유자의 희석으로 이어집니다.

## 권장 사항

관리자 수수료를 나타내는 증분 토큰 수만 발행하려면:

```diff
- to_mint: uint256 = v.supply_tokens * new_total // v.total
+ to_mint: uint256 = v.supply_tokens * new_total // v.total - v.supply_tokens
```

# [M-08] 관리자 수수료 인출 중 총 공급량이 잘못 계산됨 (Incorrect total supply calculated during admin fee withdrawal)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

LT 계약에서 `withdraw_admin_fees` 함수가 호출되면 `_calculate_values` 함수가 실행되어 모든 유동성 값을 다시 계산합니다. 이 계산 중에 가치 변동에 따라 토큰 수를 조정하는 토큰 감소 메커니즘(하향 리베이스)으로 인해 총 공급량이 줄어들 수 있습니다.

그러나 함수는 다양한 유동성 매개변수를 업데이트하지만, 수수료 수령자에게 새 토큰을 발행하기 전에 `self.totalSupply`를 최신 값 `v.supply_tokens`로 업데이트하지 못합니다. 이 실수로 인해 관리자 수수료가 인출된 후 총 공급량 계산이 잘못됩니다.

```python
def withdraw_admin_fees():
    ...
    v: LiquidityValuesOut = self._calculate_values(self._price_oracle_w())
    ...
    self.liquidity.total = new_total
    self.liquidity.admin = 0
    self.liquidity.staked = v.staked
    staker: address = self.staker
    if staker != empty(address):
        self.balanceOf[staker] = v.staked_tokens

    log WithdrawAdminFees(receiver=fee_receiver, amount=to_mint)
```

## 권장 사항

수수료 수령자에게 토큰을 발행하기 전에 `self.totalSupply`를 재계산된 값으로 업데이트하십시오.

# [M-09] `set_allocator()` 상태 업데이트 누락으로 잘못된 잔액 발생 (`set_allocator()` state update missing causes incorrect balances)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`set_allocator` 함수에서 계약은 새 `amount`를 `self.allocators[allocator]`에 저장된 `old_allocation`과 비교하여 주어진 할당자에 대한 할당을 조정하려고 합니다. 그러나 함수는 `self.allocators` 매핑을 새 `amount`로 업데이트하지 않습니다. 결과적으로 `set_allocator`에 대한 모든 호출은 항상 이전 할당(old_allocation)을 0으로 보게 되며, `self.allocators`에 의존하는 후속 로직(예: 할당자가 자산을 인출하려고 할 때)은 0 값을 기반으로 합니다.

```python
# File: Factory.vy
212: @external
213: @nonreentrant
214: def set_allocator(allocator: address, amount: uint256):
215:     assert msg.sender == self.admin, "Access"
216:     assert allocator != self.mint_factory, "Minter"
217:     assert allocator != empty(address)
218: 
219:     old_allocation: uint256 = self.allocators[allocator]
220:     if amount > old_allocation:
221:         # Use transferFrom
222:         extcall STABLECOIN.transferFrom(allocator, self, amount - old_allocation)
223:     elif amount < old_allocation:
224:         # Allow to take back the allocation via transferFrom, but not more than the allocation reduction
225:         extcall STABLECOIN.approve(allocator, (staticcall STABLECOIN.allowance(self, allocator)) + old_allocation - amount)
226: 
227:     log SetAllocator(allocator=allocator, amount=amount)
```

이 함수에서 새 할당 금액을 `old_allocation`(220행)과 비교하고 적절한 토큰 전송 또는 승인(220-225행)을 수행한 후 계약은 상태 변수를 업데이트하지 않습니다. 따라서 `self.allocators[allocator]`는 변경되지 않은 상태(아마도 0)로 유지되므로 향후 작업(예: 자산 인출)은 잘못된 값이나 0 값을 기반으로 할당을 계산합니다.

다음 테스트는 `self.allocators`가 업데이트되지 않는 방식을 보여줍니다:

```python
# File: tests/lt/test_factory.py
def test_allocator_not_registered(factory, admin, accounts, stablecoin):
    # Mint tokens and set allocator using admin privileges
    with boa.env.prank(accounts[0]):
        stablecoin.approve(factory.address, 2**256-1)
    with boa.env.prank(admin):
        stablecoin._mint_for_testing(accounts[0], 10**18)
        factory.set_allocator(accounts[0], 10**18)
    # Verify that the allocator deposit amount is not registered in self.allocators (remains zero)
    deposit = factory.allocators(accounts[0])
    assert deposit == 0
```

## 권장 사항

새 할당 값으로 `self.allocators[allocator]`를 업데이트하도록 함수를 수정하십시오.

```diff
def set_allocator(allocator: address, amount: uint256):
    ...
+   self.allocators[allocator] = amount
    ...
```

# [L-01] `LT` 계약 생성 후 스테이커 계약 누락 (Staker contract missing in `LT` contract post-creation)

_해결됨_

Factory 계약에서 `add_market` 함수는 `staker_impl`이 제공된 경우 시장에 대한 스테이커 계약을 생성합니다. 그러나 코드는 이 새로 생성된 스테이커를 해당 LT 계약에 설정하지 않습니다.

```python
def add_market(
    pool: CurveCryptoPool,
    fee: uint256,
    rate: uint256,
    debt_ceiling: uint256
) -> Market:
    ...
    if self.staker_impl != empty(address):
        market.staker = create_from_blueprint(
            self.staker_impl,
            market.lt)

    ...
```

따라서 스테이커는 존재하지만 LT 계약에서 제대로 구성되지 않았습니다. 결과적으로 별도의 트랜잭션에서 스테이커를 수동으로 설정할 때까지 스테이킹 기능이 제대로 작동하지 않습니다.

add_market 함수에서 LT에 대한 스테이커를 설정하는 것이 좋습니다.

```diff
    if self.staker_impl != empty(address):
        market.staker = create_from_blueprint(
            self.staker_impl,
            market.lt)
+       extcall LT(market.lt).set_staker(market.staker)
```

# [L-02] `min_admin_fee` 초기화 및 업데이트 부족 (`min_admin_fee` lacks initialization and update)

_해결됨_

`LT.vy`에서 변수 `min_admin_fee`는 public으로 선언되며 `_calculate_values` 함수 내의 수수료 계산에 사용됩니다:

```python
# File: LT.vy
136: min_admin_fee: public(uint256)
...
204: def _calculate_values(p_o: uint256) -> LiquidityValuesOut:
...
212:     f_a: int256 = convert(
213:         10**18 - (10**18 - self.min_admin_fee) * self.sqrt(convert(10**36 - staked * 10**36 // supply, uint256)) // 10**18,
214:         int256)
...
```

`f_a` 계산은 `self.min_admin_fee`를 사용하여 적용해야 할 최소 수수료를 결정합니다. 그러나 계약에는 관리자가 `min_admin_fee`를 설정하거나 업데이트할 수 있는 함수가 없으며 계약 배포 시 0이 아닌 값으로 초기화되지도 않습니다. 결과적으로 `self.min_admin_fee`는 0으로 유지됩니다.

다음 테스트는 계약 배포 시 `min_admin_fee`가 0이며 이를 조정할 수 있는 설정자가 없음을 보여줍니다.

```python
File: tests/lt/test_factory.py
51: 
52: def test_min_admin_fee_default(factory, cryptopool, seed_cryptopool, lt_interface, admin):
53:     fee = int(0.007e18)
54:     rate = int(0.1e18 / (365 * 86400))
55:     ceiling = 100 * 10**6 * 10**18
56: 
57:     with boa.env.prank(admin):
58:         market = factory.add_market(cryptopool.address, fee, rate, ceiling)
59:     # Assert that LT.min_admin_fee remains 0 (no setter to adjust it)
60:     lt = market[3]
61:     assert lt_interface.at(lt).min_admin_fee() == 0
```

생성자에서 `self.min_admin_fee`를 의도한 최소 관리자 수수료를 반영하는 0이 아닌 값으로 설정하도록 업데이트하십시오. 또한 `min_admin_fee`를 업데이트하는 함수(적절한 접근 제어 포함)를 구현하십시오.

# [L-03] `deposit`이 `value_before`가 0인 경우를 고려하지 못함 (`deposit` fails to account for cases where `value_before` equals 0)

_해결됨_

`deposit`이 0이 아닌 총 공급량으로 호출되면 다음 공식을 사용하여 지분을 계산합니다: `supply * value_after // value_before - supply`.

```python
@external
@nonreentrant
def deposit(assets: uint256, debt: uint256, min_shares: uint256, receiver: address = msg.sender) -> uint256:
    """
    @notice Method to deposit assets (e.g. like BTC) to receive shares (e.g. like yield-bearing BTC)
    @param assets Amount of assets to deposit
    @param debt Amount of debt for AMM to take (approximately BTC * btc_price)
    @param min_shares Minimal amount of shares to receive (important to calculate to exclude sandwich attacks)
    @param receiver Receiver of the shares who is optional. If not specified - receiver is the sender
    """
    amm: LevAMM = self.amm
    assert extcall STABLECOIN.transferFrom(amm.address, self, debt)
    assert extcall DEPOSITED_TOKEN.transferFrom(msg.sender, self, assets)
    lp_tokens: uint256 = extcall COLLATERAL.add_liquidity([debt, assets], 0, amm.address)
    p_o: uint256 = self._price_oracle_w()

    supply: uint256 = self.totalSupply
    shares: uint256 = 0

    liquidity_values: LiquidityValuesOut = empty(LiquidityValuesOut)
    if supply > 0:
        liquidity_values = self._calculate_values(p_o)

    v: ValueChange = extcall amm._deposit(lp_tokens, debt)
    value_after: uint256 = v.value_after * 10**18 // p_o

    # Value is measured in USD
    # Do not allow value to become larger than HALF of the available stablecoins after the deposit
    # If value becomes too large - we don't allow to deposit more to have a buffer when the price rises
    assert staticcall amm.max_debt() // 2 >= v.value_after, "Debt too high"

    staker: address = self.staker

    if supply > 0:
        supply = liquidity_values.supply_tokens
        self.liquidity.admin = liquidity_values.admin
        value_before: uint256 = liquidity_values.total
        value_after = convert(convert(value_after, int256) - liquidity_values.admin, uint256)
        self.liquidity.total = value_after
        self.liquidity.staked = liquidity_values.staked
        self.totalSupply = liquidity_values.supply_tokens  # will be increased by mint
        if staker != empty(address):
            self.balanceOf[staker] = liquidity_values.staked_tokens
        # ideal_staked is only changed when we transfer coins to staker
>>>     shares = supply * value_after // value_before - supply

    else:
        # Initial value/shares ratio is EXACTLY 1.0 in collateral units
        # Value is measured in USD
        shares = value_after
        # self.liquidity.admin is 0 at start but can be rolled over if everything was withdrawn
        self.liquidity.ideal_staked = 0  # Likely already 0 since supply was 0
        self.liquidity.staked = 0        # Same: nothing staked when supply is 0
        self.liquidity.total = shares    # 1 share = 1 crypto at first deposit
        self.liquidity.admin = 0         # if we had admin fees - give them to the first depositor; simpler to handle
        self.balanceOf[staker] = 0

    assert shares >= min_shares, "Slippage"

    self._mint(receiver, shares)
    log Deposit(sender=msg.sender, owner=receiver, assets=assets, shares=shares)
    return shares
```

이는 `value_before`가 0으로 떨어지면 `deposit` 작업이 되돌려짐(revert)을 의미합니다.

**권장 사항**

공급량이 0이 아니지만 `value_before`가 0이 되면 지분을 `value_after`로 설정하는 추가 조건을 추가하십시오.

# [L-04] 주소 설정자 없이 `fill_staker_vpool()` 실패 (`fill_staker_vpool()` fails without address setters)

_해결됨_

Factory 계약의 `fill_staker_vpool` 함수는 기존 시장에 누락된 가상 풀 및 스테이커 구성 요소를 추가하도록 설계되었습니다. 그러나 이 함수는 계약 배포 후 업데이트할 수 없는 구현 주소에 의존하기 때문에 효과적으로 작동할 수 없습니다.

함수는 두 가지 조건을 확인합니다:

- `market.virtual_pool == empty(address) AND self.virtual_pool_impl != empty(address) AND self.flash != empty(address)`인 경우
- `market.staker == empty(address) AND self.staker_impl != empty(address)`인 경우

이러한 구현 주소가 배포 중에 처음에 `empty(address)`로 설정된 경우 `fill_staker_vpool` 함수는 이러한 구현 주소를 나중에 설정할 방법이 없으므로 기존 시장에 대한 가상 풀이나 스테이커를 생성할 수 없습니다.

```python
def fill_staker_vpool(i: uint256):
    assert msg.sender == self.admin, "Access"
    market: Market = self.markets[i]
    if market.virtual_pool == empty(address) and self.virtual_pool_impl != empty(address) and self.flash != empty(address):
        market.virtual_pool = create_from_blueprint(
            self.virtual_pool_impl,
            market.amm,
            self.flash
        )
    if market.staker == empty(address) and self.staker_impl != empty(address):
        market.staker = create_from_blueprint(
            self.staker_impl,
            market.lt)
    self.markets[i] = market
```

계약 배포 후 구현 주소를 업데이트하는 setter 함수를 추가하십시오:

```python
@external
def set_virtual_pool_impl(impl: address):
    assert msg.sender == self.admin, "Access"
    self.virtual_pool_impl = impl
    
@external
def set_staker_impl(impl: address):
    assert msg.sender == self.admin, "Access"
    self.staker_impl = impl
```
