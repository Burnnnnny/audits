# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적인 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 절대 보장할 수 없지만, 경험 많은 연구원들의 블록체인 프로토콜에 대한 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**adapter-fi/AdapterVault** 리포지토리에 대해 **Pashov Audit Group**이 시간 제한 보안 검토를 수행했으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Adapter Finance 소개

Adapter Finance는 등록된 어댑터를 사용하여 프로토콜과 통합하고 수익을 위한 유동성 제공을 자동화 및 최적화하는 유연한 ERC4626 금고(vault)를 생성할 수 있도록 합니다. 전략은 금고별 자금 할당자 스마트 계약을 사용하여 등록된 어댑터를 통한 토큰 분배를 지시합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향(Impact)

- 높음(High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간(Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음(Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성(Likelihood)

- 높음(High) - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 도난당하거나 분실될 수 있는 자금의 양에 비해 공격 비용이 비교적 낮습니다.

- 중간(Medium) - 조건부 인센티브가 있는 공격 벡터일 뿐이지만 여전히 발생할 가능성이 비교적 높습니다.

- 낮음(Low) - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적(Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음(High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간(Medium) - 수정해야 함

- 낮음(Low) - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [c410c4d7eccb614de864e19faaa271ef0886ae36](https://github.com/adapter-fi/AdapterVault/tree/c410c4d7eccb614de864e19faaa271ef0886ae36)

_수정 검토 커밋 해시_ - [0e826db27b9a99b1903a4a5490323a9e12648d69](https://github.com/adapter-fi/AdapterVault/tree/0e826db27b9a99b1903a4a5490323a9e12648d69)

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `AdapterVault`
- `FundsAllocator`
- `Governance`
- `PendleAdapter`
- `IAdapter`

# 결과

# [M-01] 여러 전략이 동일한 자산을 공유함

## 심각도

**영향:** 높음(High)

**가능성:** 낮음(Low)

## 설명

AdapterVault 설계는 각 전략에 고유한 자산(예: PT 토큰)이 있다고 가정합니다. 그렇지 않으면 주요 회계 함수가 부풀려진 값을 반환합니다.

예를 들어 `_totalAssetsNoCache()`는 각 전략을 통과하고 관련 기본 자산에 대해 `balanceOf()`를 호출하여 합산합니다.

```python
@internal
@view
def _totalAssetsNoCache() -> uint256:
    assetqty : uint256 = ERC20(asset).balanceOf(self)
    for adapter in self.adapters:
        assetqty += IAdapter(adapter).totalAssets()

    return assetqty
```

`IAdapter(adapter).totalAssets()`는 `AdapterVault`에서 관련 기본 자산의 잔액을 확인하고 오라클 가격(`PendleAdapter`에 따라)을 곱합니다.

그러나 두 전략이 동일한 기본 자산을 공유하는 경우 `AdapterVault`는 이 잔액이 두 전략 간에 어떻게 분할되는지 식별할 수 없으므로 동일한 토큰을 두 번 계산합니다.

결과적으로 이는 부풀려진 `totalAssets()`와 주식 및 자산 간의 계산 오류로 이어집니다. 일시적인 캐시 값도 잘못된 잔액을 보유하게 됩니다.

## 권장 사항

`_addAdapter()`는 `adapter.asset`이 현재 전략에서 사용되지 않는지 확인해야 합니다.

# [M-02] `submitStrategy()` DOS

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

`Governance.submitStrategy()`는 금고당 하나의 보류 중인 전략만 엄격하게 갖도록 합니다. 또한 새로운 전략은 투표를 위해 수락될 수 없습니다. 보류 중인 전략을 수락하거나 거부한 후 또는 제출 후 6시간이 지난 후에만 수락될 수 있습니다.

또한 이 기능은 공개되어 있으므로 누구나 제출할 수 있습니다.

결과적으로 가장 간단한 공격 벡터는 제출이 가능해지자마자 새로운 전략을 스팸 처리하는 것입니다. 새로운 제출은 6시간을 기다리거나 거버넌스가 개입해야 합니다. 그러나 그 후에도 동일한 스팸 공격이 가능합니다.
또한 제출 전 트랜잭션을 프론트러닝 하여 차단할 수도 있습니다.

## 권장 사항

공격자의 제출을 무시할 수 있도록 여러 보류 중인 전략을 허용하는 것을 고려하십시오.
또는 신뢰할 수 있는 주소에만 제출을 허용하십시오.

# [M-03] replaceGovernanceContract가 재설정되지 않음

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

거버넌스가 교체된 후 `VotesGCByVault`는 0으로 재설정되지 않습니다. 이는 금고의 거버넌스가 이전 계약으로 되돌아가는 경우 투표 수가 여전히 기록되어 가드가 적절한 투표 없이 다시 변경할 수 있는 상황으로 이어질 수 있습니다. 예를 들어 거버넌스 계약 A와 B를 고려하십시오. A는 B로 교체되도록 투표되었습니다. B에 문제나 버그가 있어 A로 다시 교체되는 경우 B에 대한 투표 수는 여전히 기록됩니다. 그러면 가드는 적절한 투표 없이 즉시 B로 다시 변경하도록 투표할 수 있습니다.

```python
@external
def replaceGovernance(NewGovernance: address, vault: address):
    ...
    #Add Vote to VoteCount
    for guard_addr in self.LGov:
        if self.VotesGCByVault[vault][guard_addr] == NewGovernance:
            VoteCount += 1

    if len(self.LGov) == VoteCount:
        AdapterVault(vault).replaceGovernanceContract(NewGovernance)
```

## 권장 사항

`VotesGCByVault`를 0으로 재설정하십시오.

# [M-04] 제안자가 변경될 때 이전 제안자에게 0 수수료 전송

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

`set_strategy`가 호출되고 `current_proposer`가 제공된 `_proposer`와 같지 않은 경우 `_claim_fees`를 호출하여 이전 제안자에게 수수료를 보내려고 시도합니다.

```python
@internal
def _set_strategy(_proposer: address, _strategies : AdapterStrategy[MAX_ADAPTERS], _min_proposer_payout : uint256, pregen_info: DynArray[Bytes[4096], MAX_ADAPTERS]) -> bool:
    assert msg.sender == self.governance, "Only Governance DAO may set a new strategy."
    assert _proposer != empty(address), "Proposer can't be null address."

    # Are we replacing the old proposer?
    if self.current_proposer != _proposer:

        current_assets : uint256 = self._totalAssetsNoCache() # self._totalAssetsCached()

        # Is there enough payout to actually do a transaction?
        yield_fees : uint256 = 0
        strat_fees : uint256 = 0
        yield_fees, strat_fees = self._claimable_fees_available(current_assets)
        # @audit - should be equal
        if strat_fees > self.min_proposer_payout:

            # Pay prior proposer his earned fees.
>>>         self._claim_fees(FeeType.PROPOSER, 0, pregen_info, current_assets)

        self.current_proposer = _proposer
        self.min_proposer_payout = _min_proposer_payout

    # .....

    return True
```

`_asset_amount`로 0을 제공하는 것을 볼 수 있는데, `current_vault_assets`가 `min_fees_to_claim`을 충당하기에 충분하다면 `fees_to_claim`으로 잘못 설정될 것입니다.

```python
@internal
def _claim_fees(_yield : FeeType, _asset_amount: uint256, pregen_info: DynArray[Bytes[4096], MAX_ADAPTERS], _current_assets : uint256 = 0, _min_assets: uint256 = 0) -> uint256:
    # self._claim_fees(FeeType.PROPOSER, 0, pregen_info, current_assets)
    yield_fees : uint256 = 0
    strat_fees : uint256 = 0

    yield_fees, strat_fees = self._claimable_fees_by_me(_yield, _asset_amount, _current_assets)

    fees_to_claim : uint256 = yield_fees + strat_fees
    if _asset_amount > 0:               # Otherwise we take it all.
        fees_to_claim = _asset_amount   # This will be lower than or equal to the total available fees.

    # Account for slippage minimums.
    min_fees_to_claim : uint256 = self._defaultSlippage(fees_to_claim, _min_assets)

    # Do we have enough balance locally to satisfy the claim?
    current_vault_assets : uint256 = ERC20(asset).balanceOf(self)

    if current_vault_assets < min_fees_to_claim:
        # Need to liquidate some shares to fulfill. Insist on withdraw only semantics.
        # Note - there is a chance that balance adapters could return more than we asked for so
        #        don't just give it all away in case there's an overage.
        fees_to_claim = min(self._balanceAdapters(fees_to_claim, pregen_info, True), fees_to_claim)
        assert fees_to_claim >= min_fees_to_claim, "Couldn't get adequate assets into the vault to support fee request."
    else:
        # @audit - if asset_amount is 0, it will set fees_to_claim to 0
>>>     fees_to_claim = min(_asset_amount, current_vault_assets)
    # Adjust fees proportionally to account for slippage.
    if strat_fees > 0 and yield_fees > 0:
        strat_fees = convert((convert(strat_fees, decimal)/convert(strat_fees+yield_fees, decimal))*convert(fees_to_claim, decimal), uint256)
        yield_fees = fees_to_claim - strat_fees
    elif strat_fees > 0:
        strat_fees = fees_to_claim
    else:
        yield_fees = fees_to_claim

    # .....

    return fees_to_claim
```

이로 인해 이전 제안자가 정당한 수수료를 받지 못하게 됩니다.

## 권장 사항

`_asset_amount`를 사용하여 최소값을 확인하는 대신 `fees_to_claim`을 사용하십시오.

```diff
# .....
    if current_vault_assets < min_fees_to_claim:
        # Need to liquidate some shares to fulfill. Insist on withdraw only semantics.
        # Note - there is a chance that balance adapters could return more than we asked for so
        #        don't just give it all away in case there's an overage.
        fees_to_claim = min(self._balanceAdapters(fees_to_claim, pregen_info, True), fees_to_claim)
        assert fees_to_claim >= min_fees_to_claim, "Couldn't get adequate assets into the vault to support fee request."
    else:
-        fees_to_claim = min(_asset_amount, current_vault_assets)
+        fees_to_claim = min(fees_to_claim, current_vault_assets)
# .....
```

# [M-05] `set_strategy`가 `last_asset_value`를 잘못 제거함

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

`set_strategy`는 어댑터 전략 목록을 변경하기 위해 거버넌스에 의해 호출될 수 있습니다. 여기에는 새 어댑터를 통합하거나 이전 어댑터의 비율을 변경하는 것이 포함될 수 있습니다. 그러나 새 전략이 이전에 사용된 어댑터를 통합하는 경우 어댑터 전략의 `last_asset_value`를 잘못 제거합니다.

```python
def _set_strategy(_proposer: address, _strategies : AdapterStrategy[MAX_ADAPTERS], _min_proposer_payout : uint256, pregen_info: DynArray[Bytes[4096], MAX_ADAPTERS]) -> bool:
    assert msg.sender == self.governance, "Only Governance DAO may set a new strategy."
    assert _proposer != empty(address), "Proposer can't be null address."

    # Are we replacing the old proposer?
    if self.current_proposer != _proposer:

        current_assets : uint256 = self._totalAssetsNoCache() # self._totalAssetsCached()

        # Is there enough payout to actually do a transaction?
        yield_fees : uint256 = 0
        strat_fees : uint256 = 0
        yield_fees, strat_fees = self._claimable_fees_available(current_assets)
        if strat_fees > self.min_proposer_payout:

            # Pay prior proposer his earned fees.
            self._claim_fees(FeeType.PROPOSER, 0, pregen_info, current_assets)

        self.current_proposer = _proposer
        self.min_proposer_payout = _min_proposer_payout

    # Clear out all existing ratio allocations.
    for adapter in self.adapters:
        self.strategy[adapter] = empty(AdapterValue)

    # Now set strategies according to the new plan.
    for strategy in _strategies:
        plan : AdapterValue = empty(AdapterValue)
        plan.ratio = strategy.ratio
        # @audit - it will clear `last_asset_value` if it previously exist
>>>     self.strategy[strategy.adapter] = plan

    log StrategyActivation(_strategies, _proposer)

    return True
```

`last_asset_value`는 갑작스러운 손실이나 LP가 훼손된 경우에 대비해 구현된 안전장치/보호 장치입니다. 값을 0으로 설정하면 리밸런싱이 발생할 때 보호 장치가 제거되고 우회됩니다.

## 권장 사항

`last_asset_value`가 이전에 존재했다면 이전 값으로 설정하십시오.

# [M-06] `min_transfer_balance`가 잘못된 값과 대조 확인됨

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

`_withdraw`가 호출되고 `transfer_balance`가 금고 내부의 `current_balance`보다 클 때 `current_balance`가 `min_transfer_balance`를 충당하기에 충분한지 확인해야 합니다. 그러나 현재는 `transfer_balance`와 대조하여 확인합니다.

```python
@internal
def _withdraw(_asset_amount: uint256, _receiver: address, _owner: address, pregen_info: DynArray[Bytes[4096], MAX_ADAPTERS], _min_assets: uint256 = 0) -> uint256:
    # _withdraw(assetqty, _receiver, _owner, pregen_info, 0)
    min_transfer_balance : uint256 = self._defaultSlippage(_asset_amount, _min_assets)

    # ...

    # Make sure we have enough assets to send to _receiver. Do a withdraw only balance.
    self._balanceAdapters(_asset_amount, pregen_info, True )

    # Now account for possible slippage.
    current_balance : uint256 = ERC20(asset).balanceOf(self)
    transfer_balance : uint256 = _asset_amount
    if transfer_balance > current_balance:
        # Didn't get as much as we expected. Is it above the minimum?
        # @audit - this should check against current_balance instead
>>>     assert transfer_balance >= min_transfer_balance, "ERROR - Unable to meet minimum slippage requested for this withdraw."

        # We'll transfer what we received.
        transfer_balance = current_balance
        log SlippageWithdraw(msg.sender, _receiver, _owner, _asset_amount, shares, transfer_balance)

    # Now send assets to _receiver.
    ERC20(asset).transfer(_receiver, transfer_balance)

    # Clear the asset cache for vault but not adapters.
    self._dirtyAssetCache(True, False)

    # Update all-time assets withdrawn for yield tracking.
    self.total_assets_withdrawn += transfer_balance

    log Withdraw(msg.sender, _receiver, _owner, transfer_balance, shares)

    return shares
```

이로 인해 최소 계산된 슬리피지 금액을 초과하지 않는 경우에도 항상 검사를 통과하고 `transfer_balance`를 `current_balance`로 잘못 설정하게 됩니다.

## 권장 사항

`min_transfer_balance`를 `current_balance`와 대조하여 확인하십시오.

```diff
    if transfer_balance > current_balance:
        # Didn't get as much as we expected. Is it above the minimum?
        # @audit - this should check against current_balance instead
-        assert transfer_balance >= min_transfer_balance, "ERROR - Unable to meet minimum slippage requested for this withdraw."
+        assert current_balance >= min_transfer_balance, "ERROR - Unable to meet minimum slippage requested for this withdraw."
        # We'll transfer what we received.
        transfer_balance = current_balance
        log SlippageWithdraw(msg.sender, _receiver, _owner, _asset_amount, shares, transfer_balance)
```

# [M-07] `PendleAdapter`가 0 슬리피지를 제공함

## 심각도

**영향:** 높음(High)

**가능성:** 낮음(Low)

## 설명

금고가 `_adapter_deposit`을 트리거하고 호출을 `PendleAdapter.deposit`에 위임하면 제공된 `_asset_amount`를 구성된 Pendle 시장에 입금합니다.

```python
@external
@nonpayable
def deposit(asset_amount: uint256, pregen_info: Bytes[4096]=empty(Bytes[4096])):
    # ....

    #mint if minting price is better, then sell the YT.
    if pg.mint_returns > pg.spot_returns:
        #Mint PY
        inp: TokenInput = empty(TokenInput)
        inp.tokenIn = asset
        inp.netTokenIn = asset_amount
        inp.tokenMintSy = asset

        netPyOut: uint256 = 0
        netSyInterm: uint256 = 0
        ERC20(asset).approve(pendleRouter, asset_amount)
        #Mint PY+PT using asset
        netPyOut, netSyInterm = PendleRouter(pendleRouter).mintPyFromToken(
            self,
            yt_token,
>>>         0,
            inp
        )

        #Swap any YT gained to PT
        ERC20(yt_token).approve(pendleRouter, netPyOut)

        PendleRouter(pendleRouter).swapExactYtForPt(
            self,
            pendleMarket,
            netPyOut,
>>>         0,
            pg.approx_params_swapExactYtForPt
        )

    else:
        #swapExactTokenForPt
        inp: TokenInput = empty(TokenInput)
        inp.tokenIn = asset
        inp.netTokenIn = asset_amount
        inp.tokenMintSy = asset

        limit: LimitOrderData = empty(LimitOrderData)
        ERC20(asset).approve(pendleRouter, asset_amount)
        PendleRouter(pendleRouter).swapExactTokenForPt(
            self,
            pendleMarket,
>>>         0,
            pg.approx_params_swapExactTokenForPt,
            inp,
            limit
        )
        #NOTE: Not doing any checks and balances, minPtOut=0 is intentional.
        #It's up to the vault to revert if it does not like what it sees.
```

모든 작업의 최소 아웃 반환 값이 0으로 설정되어 있고 슬리피지를 제공하지 않는 것은 의도적인 것이며 금고가 슬리피지를 확인하도록 남겨둘 것이라고 언급되어 있습니다. 그러나 `_balanceAdapters` 내부에서 `_adapter_deposit`이 호출된 후 발행/스왑 된 토큰 금액이 예상 범위 내에 있는지 확인하는 슬리피지 검사가 없습니다. 샌드위치 공격은 `_balanceAdapters`가 호출될 때 실행되어 작업에서 가치를 추출할 수 있습니다.

## 권장 사항

금고 내부의 `_balanceAdapters` 및 `_adapter_deposit` 내부에 최소 허용 반환 값을 추가하는 것을 고려하십시오.

# [M-08] `TDelay` 조건에 기반한 경쟁 조건

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

전략이 가드에 의해 거부되거나 지지되지 않으면 `TDelay` 기간 후에 활성화될 수 있습니다.

```python
@external
def activateStrategy(Nonce: uint256, vault: address):
    ...
    #Confirm strategy is approved by guards
    assert (len(pending_strat.VotesEndorse) >= (len(self.LGov)/2)+1) or \
           ((pending_strat.TSubmitted + self.TDelay) < block.timestamp), "Premature activation with insufficience endorsements."
    ...
```

그러나 `TDelay` 기간 후에 전략이 새로운 전략으로 대체될 수도 있습니다. 이는 어떤 트랜잭션이 먼저 실행되는지에 따라 전략이 활성화되거나 대체될 수 있는 상황을 만듭니다. 악의적인 제안자는 활성화 트랜잭션을 프론트러닝 하고 새로운 전략을 제출하여 현재 전략을 거부할 수 있습니다.

```python
@external
def submitStrategy(strategy: ProposedStrategy, vault: address) -> uint256:
    ...
            # First is it the same as the current one?
            # Otherwise has it been withdrawn?
            # Otherwise, has it been short circuited down voted?
            # Has the period of protection from being replaced expired already?
    assert  (self.CurrentStrategyByVault[vault].Nonce == pending_strat.Nonce) or \
            (pending_strat.Withdrawn == True) or \
            len(pending_strat.VotesReject) > 0 and \
            (len(pending_strat.VotesReject) >= pending_strat.no_guards/2) or \
            (convert(block.timestamp, decimal) > (convert(pending_strat.TSubmitted, decimal)+(convert(self.TDelay, decimal)))), "Invalid proposed strategy!" # @audit strategy can be replaced after TDelay
    ...
```

## 권장 사항

활성화 시간과 교체 시간이 겹치지 않도록 하십시오. 전략은 `TDelay` 기간보다 긴 기간 후에만 교체할 수 있어야 합니다. 프론트러닝 및 경쟁 조건을 방지하기 위해 교체를 위한 추가 지연 기간(교체 지연)을 도입하는 것을 고려하십시오.

# [M-09] 가드 수에 기반한 경쟁 조건

## 심각도

**영향:** 높음(High)

**가능성:** 낮음(Low)

## 설명

제출된 전략은 반대표에 의해 단락(short-circuited) 된 경우 새로운 전략으로 대체될 수 있습니다. 그러나 단락에 필요한 반대표 수는 `no_guards / 2`이고 내림되므로 필요한 반대표 수는 `no_guards`의 절반 미만일 수 있습니다. 이로 인해 반대표에 의해 적절하게 단락 되지 않았음에도 전략이 대체되는 상황이 발생할 수 있습니다.

`no_guards`가 3인 시나리오를 고려하십시오:

- 2명의 가드가 전략을 `endorseStrategy` 합니다. 이는 전략을 활성화하기에 충분해야 합니다(2/3 지지).
- 나중에 1명의 가드가 전략을 `rejectStrategy` 하고 현재 보류 중인 전략을 새 전략으로 교체할 수 있습니다(`no_guards` / 2가 1로 내림되기 때문).

```python
def submitStrategy(strategy: ProposedStrategy, vault: address) -> uint256:
    ...

    # Confirm there's no currently pending strategy for this vault so we can replace the old one.

            # First is it the same as the current one?
            # Otherwise has it been withdrawn?
            # Otherwise, has it been short circuited down voted?
            # Has the period of protection from being replaced expired already?
    assert  (self.CurrentStrategyByVault[vault].Nonce == pending_strat.Nonce) or \
            (pending_strat.Withdrawn == True) or \
            len(pending_strat.VotesReject) > 0 and \
            (len(pending_strat.VotesReject) >= pending_strat.no_guards/2) or \ # @audit round down so can be short circuited
            (convert(block.timestamp, decimal) > (convert(pending_strat.TSubmitted, decimal)+(convert(self.TDelay, decimal)))), "Invalid proposed strategy!"
    ...
```

## 권장 사항

전략을 단락 시키기 위해 필요한 반대표 수를 올림 하십시오.

```diff
def submitStrategy(strategy: ProposedStrategy, vault: address) -> uint256:
    ...

    # Confirm there's no currently pending strategy for this vault so we can replace the old one.

            # First is it the same as the current one?
            # Otherwise has it been withdrawn?
            # Otherwise, has it been short circuited down voted?
            # Has the period of protection from being replaced expired already?
    assert  (self.CurrentStrategyByVault[vault].Nonce == pending_strat.Nonce) or \
            (pending_strat.Withdrawn == True) or \
            len(pending_strat.VotesReject) > 0 and \
-           (len(pending_strat.VotesReject) >= pending_strat.no_guards/2) or \
+           (len(pending_strat.VotesReject) >= pending_strat.no_guards/2+1) or \
            (convert(block.timestamp, decimal) > (convert(pending_strat.TSubmitted, decimal)+(convert(self.TDelay, decimal)))), "Invalid proposed strategy!"
    ...
```

# [L-01] 보류 중인 투표 중 가드 변경

가드를 제거할 수 있는 두 가지 기능이 있습니다:

- `swapGuard()`
- `removeGuard()`

그러나 문제는 제거된 가드가 이미 보류 중인 전략에 찬성 또는 반대 투표를 하여 `PendingStrategyByVault[vault].VotesEndorse` `PendingStrategyByVault[vault].VotesReject`에 계속 존재할 수 있다는 것입니다.

이 경우 `len(pending_strat.VotesEndorse)` 및 `len(pending_strat.VotesReject)`는 정확하지 않으며 비 gov 주소를 포함할 수 있습니다.

예: 일부 가드 Alice가 이전 주소에 대해 이미 투표한 상태에서 이전 주소에서 새 주소로 `swapGuard()`를 수행하는 경우를 고려하십시오. 이제 그녀는 두 번째 투표를 할 수 있으며 `activateStrategy()`는 두 표를 계산합니다.

`swapGuard()` 및 `removeGuard()` 중에 `PendingStrategyByVault[vault].VotesEndorse` 및 `PendingStrategyByVault[vault].VotesReject`에서 삭제된 가드 주소를 제거하는 것을 고려하십시오.

옵션 중 하나는 `len(pending_strat.VotesEndorse)` 및 `len(pending_strat.VotesReject)`를 피하고 대신 기존 `LGov` 주소만 반복하여 합산하는 별도의 계산을 사용하여 더 이상 `LGov`에 없는 주소를 무시하는 것일 수 있습니다.

# [L-02] `_getTargetBalancesWithdrawOnly` 잘못된 계산

`_getTargetBalancesWithdrawOnly`에서 어댑터를 반복하고 필요한 경우 목표 인출 잔액을 충족하기 위해 인출합니다. 그러나 `adapter.delta`가 0이 아닐 때 `adapter_assets_allocated`를 `adapter.delta * -1`로 잘못 추가합니다.

```python
@internal
@pure
def _getTargetBalancesWithdrawOnly(_vault_balance: uint256, _d4626_asset_target: uint256, _total_assets: uint256, _total_ratios: uint256, _adapter_balances: BalanceAdapter[MAX_ADAPTERS]) -> (uint256, int256, uint256, BalanceAdapter[MAX_ADAPTERS], address[MAX_ADAPTERS]):
    # self._getTargetBalancesWithdrawOnly(_vault_balance, _d4626_asset_target, _total_assets, _total_ratios, _adapter_balances)
    # ....

        if adapter.delta != 0:
>>>         adapter_assets_allocated += convert(adapter.delta * -1, uint256)    # TODO : eliminate adapter_assets_allocated if never used.
            d4626_delta += adapter.delta * -1
            adapters[tx_count] = adapter
            tx_count += 1

        # NEW
>>>     adapter_result : int256 = convert(adapter.current, int256) + adapter.delta
        assert adapter_result >= 0, "Adapter resulting balance can't be less than zero!"
>>>     adapter_assets_allocated += convert(adapter_result, uint256)

    assert target_withdraw_balance == 0, "ERROR - Unable to fulfill this withdraw!"

    return adapter_assets_allocated, d4626_delta, tx_count, adapters, blocked_adapters
```

어댑터 `_assets_allocated`는 함수 끝에서 올바르게 추가되는 어댑터의 잔액으로 업데이트되어야 함을 알 수 있습니다. 불필요한 `adapter_assets_allocated += convert(adapter.delta * -1, uint256)` 작업을 제거하는 것을 고려하십시오.

# [L-03] `target_withdraw_balance`가 감소하지 않음

인출 전용 목표 잔액을 계산할 때 어댑터 비율이 0이면 어댑터의 모든 자산이 금고로 다시 인출됩니다. 그러나 인출된 금액은 `target_withdraw_balance`를 줄이지 않습니다. 이는 다른 어댑터의 자산도 인출해야 할 때 자산의 과도한 인출로 이어져 슬리피지 및 추가 리밸런싱 비용이 발생할 수 있습니다.

```python
def _getTargetBalancesWithdrawOnly(_vault_balance: uint256, _d4626_asset_target: uint256, _total_assets: uint256, _total_ratios: uint256, _adapter_balances: BalanceAdapter[MAX_ADAPTERS]) -> (uint256, int256, uint256, BalanceAdapter[MAX_ADAPTERS], address[MAX_ADAPTERS]):
    ...
        if adapter.ratio == 0 and adapter.current > 0:
            adapter.target = 0
            adapter.delta = max(convert(adapter.current, int256)*-1, adapter.max_withdraw) # Withdraw it all! @audit doesn't reduce target_withdraw_balance
    ...
```

비율이 0이고 현재 잔액이 0보다 큰 어댑터에서 인출할 때 `target_withdraw_balance`를 줄이십시오. 이렇게 하면 인출 목표를 정확하게 계산하고 다른 어댑터에서의 과도한 인출을 방지할 수 있습니다.

