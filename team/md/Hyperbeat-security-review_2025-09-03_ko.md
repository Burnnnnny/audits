# Pashov Audit Group 소개 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 입증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 대회 보상으로 10만 달러 이상을 획득했거나, 다회 우승자이거나, 우리와의 감사에서 진정으로 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여가 된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

<p><strong>etherfi-protocol/BeHYPE</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, <strong>IvanFitro, 0xl33, unforgiven, Tejas Warambhe</strong>가 <strong>Hyperbeat</strong> 검토에 참여했습니다. 총 <strong>17</strong>개의 문제가 발견되었습니다.</p>

# Hyperbeat 정보 (About Hyperbeat)

<p>Hyperbeat는 사용자가 HYPE를 입금하여 beHYPE 토큰으로 교환하고 즉시 또는 지연된 인출 및 동적 환율 관리를 제공하는 스테이킹 솔루션입니다.</p>

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [c620e61aaf8fe5767e713bb9d7447bd9fbc40148](https://github.com/etherfi-protocol/BeHYPE/tree/c620e61aaf8fe5767e713bb9d7447bd9fbc40148)<br>&nbsp;&nbsp;(etherfi-protocol/BeHYPE)

# 범위 (Scope)

- `BeHYPE.sol`
- `BeHYPETimelock.sol`
- `RoleRegistry.sol`
- `StakingCore.sol`
- `WithdrawManager.sol`
- `interfaces/`

# 발견 사항 (Findings)

# [M-01] `getTotalProtocolHype()`가 대기 중인 인출 금액을 포함함 (`getTotalProtocolHype()` includes pending withdrawal amounts)

_인정됨 (Acknowledged)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`StakingCore.sol`의 `getTotalProtocolHype()` 함수는 프로토콜이 소유한 총 HYPE를 계산하지만, 인출 요청되었지만 아직 확정되지 않은 HYPE를 계산에 포함하지 못합니다. 이는 즉시 인출 흐름(instant withdrawal flow) 중에 TVL 계산을 부풀리는 결과를 초래합니다.

사용자가 `WithdrawManager.withdraw()`를 통해 인출을 대기열에 넣으면 `hypeRequestedForWithdraw` 금액이 증가하지만 `getTotalProtocolHype()`는 여전히 이 HYPE를 계산에 포함합니다. 이로 인해 사용자가 허용된 범위를 넘어 즉시 인출을 수행할 수 있는 창이 생성됩니다.

문제 발생 원인은 다음과 같습니다:
1. `getTotalProtocolHype()`는 대기 중인 인출을 뺀 금액이 아닌 전체 잔액을 반환합니다.
2. `getLiquidHypeAmount()`는 대기 중인 인출을 뺀 금액이 아닌 전체 잔액을 반환합니다.
3. `getTotalInstantWithdrawableBeHYPE()`는 이 부풀려진 값을 사용하여 `withdrawableAmount`를 계산합니다.
4. 이를 통해 실제 가용 유동성이 허용해야 하는 것보다 더 많은 즉시 인출이 가능해집니다.

이 버그는 대기열에 미확정 인출이 있을 때만 나타납니다. `finalizeWithdrawals()`가 호출되면 HYPE가 `StakingCore`에서 `WithdrawManager`로 전송 되어 반환 값이 자연스럽게 줄어듭니다.

참고: `getTotalProtocolHype()`는 `withdraw()` 흐름 중에 호출될 때만 `hypeRequestedForWithdraw`를 빼야 하며, `updateExchangeRatio()` 흐름 중에 호출될 때는 현재 동작을 유지해야 합니다. `updateExchangeRatio()` 흐름 중에 대기 중인 인출을 빼면 잘못된 환율 계산이 발생하기 때문입니다.

다음 시나리오를 고려하십시오:

1. 프로토콜에 총 `10000e18` HYPE가 있습니다.
2. 저수위(Low watermark)는 10% (`1000e18`) HYPE입니다.
3. 사용자가 `1000e18` HYPE의 인출을 대기열에 넣습니다.
4. 관리자는 위임 해제(undelegate)하고 HyperCore의 스테이킹에서 현물 잔액으로 이체하고 HyperCore에서 StakingCore 계약으로 인출합니다.
5. `getTotalProtocolHype()`는 여전히 `10000e18` HYPE를 반환합니다 (`finalizeWithdrawals()`가 아직 호출되지 않음).
6. `lowWatermarkInHYPE()`는 `1000e18` HYPE (`10000e18`의 10%)로 계산합니다.
7. `getLiquidHypeAmount()`는 `2000e18` HYPE를 반환합니다 (기본적으로 남은 `1000e18` + 인출 요청을 이행하기 위해 HyperCore에서 인출된 `1000e18`).
8. 즉시 인출 가능 금액: `withdrawableAmount = 2000e18 - 1000e18 = 1000e18` HYPE <--- 이것은 잘못되었습니다.
9. 실제로는 다음과 같이 가능해야 합니다: `withdrawableAmount = 1000e18 - 900e18 = 100e18` HYPE, 총액에 포함된 `1000e18` HYPE는 프로토콜 소유가 아니기 때문입니다.
10. `finalizeWithdrawals()`가 호출되기 전에 사용자는 올바른 `100e18` HYPE 한도 대신 `1000e18` HYPE를 즉시 인출할 수 있습니다.
11. 관리자가 `finalizeWithdrawals()`를 호출하면 `StakingCore`의 잔액은 0이 되지만, 이 시점에서는 `900e18` (`9000e18`의 10%)이어야 합니다.

## 권장 사항

1. `getTotalProtocolHype()`와 동일한 작업을 수행하지만 합계에서 `hypeRequestedForWithdraw`를 빼는 별도의 함수 `getAvailableProtocolHype()`를 만드십시오. `lowWatermarkInHYPE()` 함수에서 이를 사용하십시오.
2. `getLiquidHypeAmount()`에서 잔액에서 `hypeRequestedForWithdraw`를 뺍니다 (언더플로우가 처리되었는지 확인):

```solidity
    function getLiquidHypeAmount() public view returns (uint256) {
        if (address(stakingCore).balance <= hypeRequestedForWithdraw) return 0;
        return address(stakingCore).balance - hypeRequestedForWithdraw;
    }
```

이러한 완화 조치 후에는 즉시 인출 한도가 `finalizeWithdrawals()` 호출 전후 모두 올바르게 시행됩니다.

# [M-02] 유휴 HYPE 잔액 고려로 인해 환율 가드가 조용히 우회됨 (Idle HYPE balance consideration allows silent bypass of exchange rate guard)

_인정됨 (Acknowledged)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`updateExchangeRatio()` 함수는 HyperCore의 프리컴파일에서 잘못된 읽기(bad reads)로 인한 잠재적인 문제를 완화하기 위해 환율 가드(exchange rate guard)를 활용합니다. 새로운 비율은 프로토콜의 토큰 보유량에 따라 계산됩니다:
```solidity
    function getTotalProtocolHype() public view returns (uint256) {
        L1Read.DelegatorSummary memory delegatorSummary = l1Read.delegatorSummary(address(this));
        uint256 totalHypeInStakingAccount = _convertTo18Decimals(delegatorSummary.delegated) + _convertTo18Decimals(delegatorSummary.undelegated) + _convertTo18Decimals(delegatorSummary.totalPendingWithdrawal);

        L1Read.SpotBalance memory spotBalance = l1Read.spotBalance(address(this), HYPE_TOKEN_ID);
        uint256 totalHypeInSpotAccount = _convertTo18Decimals(spotBalance.total);

        uint256 totalHypeInLiquidityPool = address(this).balance;                               <<@

        uint256 total = totalHypeInStakingAccount + totalHypeInSpotAccount + totalHypeInLiquidityPool;
        
        return total;
    }
```
그러나 가드는 전체적으로 APR에 작용하여 HyperCore에 존재하는 토큰의 일부가 아닌 `StakingCore` 계약의 HYPE 값을 포함한 전체 잔액을 고려합니다.

따라서 잘못된 프리컴파일 읽기는 `StakingCore` 계약의 충분한 유휴 유동성으로 인해 가드를 조용히 우회하여 비율을 손상시키고 `WithdrawManager::lowWatermarkInHYPE()`에 영향을 줄 수 있습니다.

## 권장 사항

`delegatorSummary`의 값을 개별적으로 보호(guard)하는 것이 좋습니다.

# [L-01] `_convertToBucketUnit()`이 오버플로를 확인하기 위해 `<=` 대신 `<`를 사용함 (`_convertToBucketUnit()` uses `<` instead of `<=` to check for overflow)

_인정됨 (Acknowledged)_

`_convertToBucketUnit()`은 `amount < type(uint64).max * BUCKET_UNIT_SCALE`을 사용하여 결과가 `uint64`를 오버플로하는지 확인합니다. 올바른 접근 방식은 비교에 `<=`를 사용하는 것입니다. 그렇지 않으면 유효한 값이 오버플로되지 않더라도 제외되기 때문입니다.

권장 사항: `<`를 `<=`로 바꾸십시오.

# [L-02] `stake()`가 최소 금액을 강제하지 않아 이벤트 스팸 발생 (Event spamming because `stake()` does not enforce a minimum amount)

_인정됨 (Acknowledged)_

`stake()`는 사용자가 HYPE를 대가로 beHYPE를 발행할 수 있도록 합니다. 문제는 최소 스테이킹 금액을 강제하지 않아 `Deposit` 이벤트가 스팸 처리될 수 있다는 것입니다. 이로 인해 특히 `communityCode`를 추적해야 하는 경우 오프체인에서 의미 있는 활동을 추적하기 어렵게 만듭니다.

권장 사항: 사용자가 스테이킹해야 하는 최소 금액을 설정하십시오.

# [L-03] `BeHYPE` 및 `WithdrawManager` 계약이 `IBeHYPE.sol`을 잘못 가져옴 (`BeHYPE` and `WithdrawManager` contracts incorrectly import `IBeHYPE.sol`)

_인정됨 (Acknowledged)_

`BeHYPE` 및 `WithdrawManager`는 `IBeHYPE` 인터페이스를 가져오지만 계약은 `IBeHYPE.sol` 대신 `IBeHype.sol`을 사용하고 있어 컴파일되지 않습니다.

권장 사항: 인터페이스를 올바르게 참조하려면 `IBeHype.sol` 대신 `IBeHYPE.sol`을 가져오십시오.

# [L-04] `updateExchangeRatio()`의 정밀도 손실 (Precision loss in `updateExchangeRatio()`)

_인정됨 (Acknowledged)_

`updateExchangeRatio()` 함수는 누적 정밀도 손실을 도입하는 순차적인 수학 연산을 수행하여 잠재적으로 `yearlyRateInBps16` 값을 원래보다 작게 만들 수 있습니다. 이 함수는 백분율 변화를 계산한 다음 연간 이율을 계산하고 여러 나눗셈 연산을 통해 베이시스 포인트(basis points)로 변환합니다:

```solidity
uint256 percentageChange = Math.mulDiv(ratioChange, 1e18, exchangeRatio);
uint256 yearlyRate = Math.mulDiv(percentageChange, 365 days, elapsedTime);  
uint256 yearlyRateInBps = yearlyRate / 1e14;
```

`Math.mulDiv`는 개별 연산에 대한 정밀도를 유지하지만 순차적 접근 방식은 작은 반올림 오류를 복합적으로 만듭니다. `percentageChange` 결과(이미 반올림됨)는 `yearlyRate` 계산의 입력으로 사용되며 `1e14`로 나누는 최종 나눗셈은 일반 나눗셈을 사용하여 소수 부분을 잘라냅니다.

이 정밀도 손실은 `yearlyRateInBps16`을 `acceptablAprInBps`와 비교하는 `exchangeRateGuard` 확인에만 영향을 미칩니다. 실제 비율이 임계값 바로 위에 있지만 정밀도 손실로 인해 계산된 값이 임계값 아래로 떨어지는 엣지 케이스에서 가드 확인이 잘못 우회되어 거부되어야 하는 환율 업데이트가 허용될 수 있습니다.

가능한 경우 연산을 결합하여 중간 반올림을 최소화하도록 계산을 재구성하고 나눗셈 전에 곱셈이 발생하도록 하십시오.

예:    
`yearlyRateInBps = Math.mulDiv(ratioChange * 365 days, 1e18, exchangeRatio * elapsedTime * 1e14)` (여기서는 오버플로가 불가능해야 함).

# [L-05] 수수료가 beHYPE 형태로 지불되어 트레저리가 스테이킹 보상을 받음 (Treasury receives staking rewards due to fees being taken in form of beHYPE)

_인정됨 (Acknowledged)_

즉시 인출 시 인출 수수료는 beHYPE 토큰으로 징수되어 프로토콜 트레저리로 전송됩니다. beHYPE 토큰은 환율 상승을 통해 스테이킹 보상을 얻는 지분(share) 토큰이므로 트레저리는 시간이 지남에 따라 사용자 스테이킹 보상의 일부를 효과적으로 캡처합니다. 트레저리가 이러한 beHYPE 토큰을 오래 보유할수록 더 많은 보상이 사용자로부터 트레저리로 전환되며 이는 스테이커에게 불공평합니다.

인출 수수료를 HYPE 토큰 형태로 트레저리에 전송하는 것을 고려하십시오. HYPE 토큰은 스테이킹 보상을 축적하지 않으며 사용자로부터 보상을 전환하지 않습니다.

# [L-06] `emergencyWithdrawFromStaking()`이 `lastWithdrawalTimestamp`를 업데이트하지 않음 (`emergencyWithdrawFromStaking()` does not update `lastWithdrawalTimestamp`)

_인정됨 (Acknowledged)_

`emergencyWithdrawFromStaking()` 함수는 `withdrawFromStaking()`과 동일한 인출 작업을 수행하지만 `lastWithdrawalTimestamp` 상태 변수를 업데이트하지 않습니다. 이러한 불일치는 비상 인출이 인출 쿨다운 기간을 재설정하지 않으므로 정기 인출 간의 의도된 쿨다운 메커니즘을 우회할 수 있음을 의미합니다.

`withdrawFromStaking()`의 동작과 일치하도록 `emergencyWithdrawFromStaking()`에서 `lastWithdrawalTimestamp` 상태 변수를 업데이트하는 것을 고려하십시오.

# [L-07] 수수료 계산의 내림 반올림으로 인해 시간이 지남에 따라 프로토콜 수익 손실 발생 (Rounding down in fee calculation causes protocol revenue loss over time)

_인정됨 (Acknowledged)_

즉시 인출을 위한 `withdraw()` 함수에서 프로토콜은 인출 수수료를 다음과 같이 계산합니다:

```solidity
uint256 instantWithdrawalFee = beHypeAmount.mulDiv(
    instantWithdrawalFeeInBps,
    BASIS_POINT_SCALE
);
````

`mulDiv`는 기본적으로 내림(rounding down)을 사용하므로 계산된 수수료는 항상 사용자에게 유리하게 잘립니다. `minWithdrawalAmount`에 의해 더스트 인출이 방지되지만, 이 반올림 효과는 여전히 프로토콜이 수수료의 작은 부분을 체계적으로 잃게 만듭니다. 시간이 지나고 많은 인출이 발생하면 이는 프로토콜 트레저리에 측정 가능한 수익 누출로 이어집니다.

**권장 사항:**
수수료 계산에 `Math.Rounding.Ceil`을 사용하여 프로토콜이 항상 의도한 전체 수수료를 징수하도록 하십시오:

# [L-08] 다중 호출을 통해 스테이킹 인출 한도를 우회할 수 있음 (Withdraw from staking limit can be bypassed via multiple calls)

_인정됨 (Acknowledged)_

`withdrawFromStaking()` 함수는 `amount`가 `hypeRequestedForWithdraw()`를 초과하지 않는지 확인하여 스테이킹 볼트에서 `WithdrawManager`로 필요한 자산 금액만 인출되도록 합니다. 그러나 이 제한은 총 인출 합계가 아닌 개별 호출별로만 시행됩니다. 결과적으로 프로토콜 관리자는 제한 바로 아래의 금액으로 `withdrawFromStaking()`을 반복적으로 호출하여 결국 총 `hypeRequestedForWithdraw`보다 더 많은 금액을 인출할 수 있습니다.

```solidity
function withdrawFromStaking(uint256 amount) external {
    if (!roleRegistry.hasRole(roleRegistry.PROTOCOL_ADMIN(), msg.sender)) revert NotAuthorized();
    if (amount > IWithdrawManager(withdrawManager).hypeRequestedForWithdraw()) revert ExceedsLimit();
    
    if (block.timestamp < lastWithdrawalTimestamp + withdrawalCooldownPeriod) {
        revert WithdrawalCooldownNotMet();
    }
    
    _encodeAction(5, abi.encode(_convertTo8Decimals(amount)));
    lastWithdrawalTimestamp = block.timestamp;
    emit HyperCoreStakingWithdraw(amount);
}
````

이로 인해 총 인출 금액이 실제 요청된 인출을 초과하여 사용자 상환에 사용할 수 있어야 하는 것보다 더 많은 자금을 스테이킹에서 고갈시킬 수 있습니다.

# [L-09] `withdrawManager` 업데이트 전에 자금이 이체되지 않음 (Funds not transferred before `withdrawManager` update)

_인정됨 (Acknowledged)_

`setWithdrawManager()` 함수는 프로토콜 업그레이더가 인출 관리자 주소를 변경하는 데 사용할 수 있습니다:

```solidity
    function setWithdrawManager(address _withdrawManager) external {
        roleRegistry.onlyProtocolUpgrader(msg.sender);
        withdrawManager = _withdrawManager;

        emit WithdrawManagerUpdated(withdrawManager);
    }
```
그러나 이 함수는 누적된 `hypeRequestedForWithdraw`를 교체하기 전에 현재 스테이킹 계약으로 다시 보내지 못합니다.

따라서 변경하기 전에 인출 요청된 자금을 청산하는 것이 좋습니다. 이는 요청된 자금을 스테이킹 코어로 이체하고 `hypeRequestedForWithdraw`를 청산하거나 요청된 금액을 새 인출 관리자 계약으로 직접 이체하여 달성할 수 있습니다.

# [L-10] `depositToHyperCore()`가 `hypeRequestedForWithdraw`를 고려하지 않음 (`depositToHyperCore()` fails to account for `hypeRequestedForWithdraw`)

_인정됨 (Acknowledged)_

`depositToHyperCore()` 함수를 통해 프로토콜 관리자는 HYPE 토큰을 HyperCore에 입금할 수 있습니다:
```solidity
    function depositToHyperCore(uint256 amount) external {
        if (!roleRegistry.hasRole(roleRegistry.PROTOCOL_ADMIN(), msg.sender)) revert NotAuthorized();
        uint256 truncatedAmount = amount / 1e10 * 1e10;
        if (amount != truncatedAmount) {
            revert PrecisionLossDetected(amount, truncatedAmount);
        }

        (bool success,) = payable(L1_HYPE_CONTRACT).call{value: amount}("");
        if (!success) revert FailedToDepositToHyperCore();
        
        lastHyperCoreOperationBlock = block.number;
        emit HyperCoreDeposit(amount);
    }
```
그러나 이 함수는 인출 확정을 위해 손대지 않은 상태로 유지해야 하는 HYPE 금액을 나타내는 `hypeRequestedForWithdraw`를 고려하지 않습니다.
따라서 의도치 않게 `StakingCore` 계약에 자금이 부족한 경우 확정 준비가 되었더라도 `finalizeWithdrawals()`가 실패할 수 있습니다.

스테이킹에서의 초과 인출을 허용하지 않기 위한 확인이 `withdrawFromStaking()`에서 관찰될 수 있습니다:
```solidity
    function withdrawFromStaking(uint256 amount) external {
        if (!roleRegistry.hasRole(roleRegistry.PROTOCOL_ADMIN(), msg.sender)) revert NotAuthorized();
        if (amount > IWithdrawManager(withdrawManager).hypeRequestedForWithdraw()) revert ExceedsLimit();       <<@
```

`depositToHyperCore()`에도 유사한 확인을 추가하는 것이 좋습니다.

# [L-11] beHYPE의 `totalSupply()`가 0이면 첫 번째 스테이커가 잘못된 `exchangeRatio`를 사용함 (First staker uses wrong `exchangeRatio` if `totalSupply()` of beHYPE is 0)

_인정됨 (Acknowledged)_

`updateExchangeRatio()`는 beHYPE와 HYPE 간의 교환 비율을 계산하는 데 사용되며 관리자가 업데이트하기 위해 호출합니다.
```solidity
function updateExchangeRatio() external {
        if (!roleRegistry.hasRole(roleRegistry.PROTOCOL_ADMIN(), msg.sender)) revert NotAuthorized();

        uint256 blocksPassed = block.number - lastHyperCoreOperationBlock;
        if (blocksPassed < MIN_BLOCKS_BEFORE_EXCHANGE_RATIO_UPDATE) {
            revert ExchangeRatioUpdateTooSoon(MIN_BLOCKS_BEFORE_EXCHANGE_RATIO_UPDATE, blocksPassed);
        }

        uint256 totalProtocolHype = getTotalProtocolHype();
        
@>      uint256 newRatio = Math.mulDiv(totalProtocolHype, 1e18, beHypeToken.totalSupply());

        uint256 ratioChange;
        if (newRatio > exchangeRatio) {
            ratioChange = newRatio - exchangeRatio;
        } else {
            ratioChange = exchangeRatio - newRatio;
        }
        
        uint256 elapsedTime = block.timestamp - lastExchangeRatioUpdate;

        if (elapsedTime == 0) revert ElapsedTimeCannotBeZero();

        uint256 percentageChange = Math.mulDiv(ratioChange, 1e18, exchangeRatio);
        uint256 yearlyRate = Math.mulDiv(percentageChange, 365 days, elapsedTime);
        uint256 yearlyRateInBps = yearlyRate / 1e14;

        uint16 yearlyRateInBps16 = uint16(Math.min(yearlyRateInBps, type(uint16).max));

        if (exchangeRateGuard) {
            if (yearlyRateInBps16 > acceptablAprInBps) revert ExchangeRatioChangeExceedsThreshold(yearlyRateInBps16);
        }

        uint256 oldRatio = exchangeRatio;
        exchangeRatio = newRatio;
        lastExchangeRatioUpdate = block.timestamp;

        emit ExchangeRatioUpdated(oldRatio, exchangeRatio, yearlyRateInBps16);
    }
```
보시다시피 비율은 총 HYPE 토큰을 beHYPE의 총 공급량으로 나누어 계산됩니다. 문제는 beHYPE의 총 공급량이 0인 경우(모든 토큰이 소각되었음을 의미) 발생합니다. 이 경우 관리자가 비율을 업데이트하기 위해 `updateExchangeRatio()`를 호출하면 0으로 나누기로 인해 트랜잭션이 항상 되돌려집니다.

이로 인해 환율이 업데이트되지 않아 첫 번째 새 스테이커가 오래된 비율에 의존하게 됩니다. 결과적으로 그들은 의도한 것보다 많거나 적은 잘못된 양의 beHYPE를 발행할 수 있습니다.

예를 들어 문제를 설명해 보겠습니다. 계약이 `100e18` HYPE 토큰과 `50e18` beHYPE 토큰을 보유하고 있으므로 `exchangeRatio = 100e18 / 50e18 = 2`라고 가정합니다.

1. Bob은 `50e18` beHYPE를 보유한 마지막 사용자입니다.
2. Bob은 `withdraw()`를 호출하여 `50e18` beHYPE를 소각하고 `100e18` HYPE를 받습니다.
3. 그런 다음 관리자는 비율을 업데이트하기 위해 `updateExchangeRatio()`를 호출하지만 beHYPE의 총 공급량이 이제 0이므로 되돌려집니다.
4. Alice는 `100e18` HYPE로 `stake()`를 호출하지만 비율이 업데이트되지 않았으므로 이전 값(2)이 사용됩니다.
5. Alice는 `100e18` beHYPE(beHYPE 공급량이 0일 때 초기 비율은 1:1) 대신 `50e18` beHYPE(`100e18 / 2`)만 받습니다.

결과적으로 Alice는 비율을 업데이트할 수 없었기 때문에 받아야 할 beHYPE의 절반만 받습니다.

이 문제는 다음 POC로도 재현할 수 있습니다. 실행하려면 코드를 `WithdrawManager.t.sol`에 복사하십시오.
```solidity
function test_CantUpdateExchangeRatio() public {

        uint256 oldExchangeRatio = stakingCore.exchangeRatio();

        vm.startPrank(user);
        beHYPE.approve(address(withdrawManager), 1 ether);
        withdrawManager.withdraw(1 ether, false, 0.9 ether);
        vm.stopPrank();

        vm.startPrank(user2);
        beHYPE.approve(address(withdrawManager), 10 ether);
        withdrawManager.withdraw(10 ether, false, 9 ether);
        vm.stopPrank();

        vm.prank(admin);
        withdrawManager.finalizeWithdrawals(2);

        vm.warp(block.timestamp + (365 days * 100));
        vm.roll(block.number + 5);

        vm.expectRevert("panic: division or modulo by zero (0x12)");
        vm.prank(admin);
        stakingCore.updateExchangeRatio();

        uint256 actualExchangeRatio = stakingCore.exchangeRatio();

        assertEq(oldExchangeRatio, actualExchangeRatio);
    }
```

**권장 사항**

beHYPE의 `totalSupply()`가 0인 경우 초기 `exchangeRatio`인 `1e18` (1:1 비율)을 반환하십시오.

# [L-12] APR 가드로 인해 손실 이벤트가 환율 업데이트에서 차단될 수 있음 (Loss events may be blocked from updating exchange ratio due to APR guard)

_인정됨 (Acknowledged)_

`updateExchangeRatio()` 함수는 환율의 급격한 변화를 제한하기 위한 가드레일로 `acceptablAprInBps`를 사용합니다. 변화가 이익을 반영하든 손실을 반영하든 동일한 임계값이 적용됩니다:

```solidity
if (exchangeRateGuard) {
    if (yearlyRateInBps16 > acceptablAprInBps) 
        revert ExchangeRatioChangeExceedsThreshold(yearlyRateInBps16);
}
````

이 설계는 시간이 지남에 따라 점진적으로 누적되는 **이익**에는 효과적이지만 갑자기 발생할 수 있는 **손실**(예: HyperCore의 슬래싱 이벤트)에는 실패합니다.

슬래시로 인해 프로토콜 가치가 급락하면 결과 비율 변경이 허용 가능한 APR 임계값을 쉽게 초과할 수 있습니다. 손실을 반영하도록 환율을 업데이트하는 대신 함수가 되돌려집니다. 이로 인해 비율이 올바른(더 낮은) 값으로 업데이트되지 않아 시스템이 부풀려진 기한(stale) 상태로 남게 됩니다.

**권장 사항**

긍정적인 변화와 부정적인 변화에 대해 **별도의 임계값**을 사용하십시오. 손실이 환율에 반영되는 것을 차단해서는 안 됩니다.

# [L-13] `stake()`의 반올림은 사용자에게 유리함 (Rounding in `stake()` is in favour of users)

_인정됨 (Acknowledged)_

스테이킹 흐름은 현재 `exchangeRatio`를 기반으로 예치된 HYPE에 대해 BeHYPE를 발행합니다. 적용된 최종 반올림 로직은 사용자에게 유리합니다:
`stake()` 함수에서 BeHYPE는 다음과 같이 발행됩니다:
```solidity
function stake(string memory communityCode) public payable {
    if (paused()) revert StakingPaused();

    beHypeToken.mint(msg.sender, HYPEToBeHYPE(msg.value));
    emit Deposit(msg.sender, msg.value, communityCode); 
}
````

HYPE에서 BeHYPE로의 변환은 다음을 사용합니다:
```solidity
function HYPEToBeHYPE(uint256 HYPEAmount) public view returns (uint256) {
    return Math.mulDiv(HYPEAmount, 1e18, exchangeRatio);
}
```
여기서 `exchangrRatio`는 분모입니다. 이미 내림 반올림되었기 때문에 더 작은 분모로 나누면 결과가 부풀려져 사용자에게 공정 가치보다 약간 더 많은 BeHYPE를 부여합니다.

이 반올림 불일치는 시간이 지남에 따라 복합적으로 발생합니다. 모든 스테이크는 해야 할 것보다 조금 더 많은 BeHYPE를 발행합니다.

참고: 환율은 업데이트될 때 내림 반올림됩니다:
```solidity
    function updateExchangeRatio() external {
        ...
        uint256 newRatio = Math.mulDiv(totalProtocolHype, 1e18, beHypeToken.totalSupply());
        ...
```

**권장 사항**

- `HYPEToBeHYPE` 함수에서 올림 반올림된 `exchangeRatio`를 사용하십시오.
- `BeHYPEToHYPE` 함수에서 내림 반올림된 `exchangeRatio`를 사용하십시오.

# [L-14] 대기 중인 인출은 총 Hype 자산에서 고려되지 않음 (Pending withdrawals are not considered in total Hype assets)

_인정됨 (Acknowledged)_

현재 인출 흐름에서 사용자가 즉시 인출이 아닌 인출을 요청하면 BeHYPE 토큰이 `WithdrawManager`로 전송되지만 인출이 확정될 때까지 소각되지 않습니다.

```solidity
// withdraw() - non-instant branch
withdrawalQueue.push(WithdrawalEntry({
    user: msg.sender,
    beHypeAmount: beHypeAmount,
    hypeAmount: hypeAmount,
    claimed: false
}));
userWithdrawals[msg.sender].push(withdrawalId);

// Tokens are transferred but not yet burned
beHypeToken.transferFrom(msg.sender, address(this), beHypeAmount);
````

소각은 나중에 `finalizeWithdrawals()`에서만 발생합니다:

```solidity
// finalizeWithdrawals()
beHypeToken.burn(address(this), beHypeAmountToFinalize);
stakingCore.sendFromWithdrawManager(hypeAmountToFinalize, address(this));
```

그러나 인출되는 토큰은 `StakingCore` 볼트 회계에서 제외되지 않으며 주문이 확정될 때까지 총 Hype 자산 및 총 BeHype 공급의 일부로 남습니다. 즉, 사용자의 상환 가치는 인출 요청 시(`hypeAmount`) 고정되지만 토큰은 계속해서 볼트 내에서 수익 또는 손실을 발생시킵니다.

사용자가 나중에 주문을 확정하면 원래 기록된 자산 금액만 전송되고 대기 기간 동안 누적된 수익은 볼트에 남습니다. 이로 인해 인출하는 사용자는 수익의 일부를 잃게 되며 해당 수익은 대신 나머지 참가자에게 한 번에 재분배됩니다.

볼트의 총 Hyper 자산의 이러한 급격한 증가는 악의적인 사용자에 의해 악용될 수 있습니다.

**권장 사항**

대기 중인 인출이 볼트 회계에서 즉시 제외되도록 인출 로직을 업데이트하십시오. 가장 간단한 해결책은 인출 요청 시 `BeHYPE` 토큰을 소각하여 사용자의 청구를 수정하고 인출 시작 후 보상이나 손실을 계속 누적하지 않도록 하는 것입니다.

# [L-15] `StakingCore`가 Hyper Core에 대한 대기 중인 입금을 고려하지 않음 (`StakingCore` does not account for pending deposits to Hyper Core)

_인정됨 (Acknowledged)_

`getTotalProtocolHype()` 함수는 **현재 블록** 내에서 Hyper Core에 입금된 예치금을 포함하지 못합니다.

사용자가 `StakingCore.depositToHyperCore`를 호출하면 토큰이 브리지로 전송되고 다음 블록까지 **사전 크레딧 상태(pre-credit state)**로 유지됩니다. 이 기간 동안 `getTotalProtocolHype()`는 크레딧된 잔액만 반영하므로 프로토콜 잔액을 과소 보고합니다.
```solidity
    function getTotalProtocolHype() public view returns (uint256) {
        L1Read.DelegatorSummary memory delegatorSummary = l1Read.delegatorSummary(address(this));
        uint256 totalHypeInStakingAccount = _convertTo18Decimals(delegatorSummary.delegated) + _convertTo18Decimals(delegatorSummary.undelegated) + _convertTo18Decimals(delegatorSummary.totalPendingWithdrawal);

        L1Read.SpotBalance memory spotBalance = l1Read.spotBalance(address(this), HYPE_TOKEN_ID);
        uint256 totalHypeInSpotAccount = _convertTo18Decimals(spotBalance.total);

        uint256 totalHypeInLiquidityPool = address(this).balance;

        uint256 total = totalHypeInStakingAccount + totalHypeInSpotAccount + totalHypeInLiquidityPool;
        return total;
    }
```
결과적으로 프로토콜 상태가 일관되지 않습니다. 입금 블록에서 토큰은 이미 브리지에 잠겨 있지만 `getTotalProtocolHype()`에는 반영되지 않습니다. 이러한 불일치는 부정확한 프로토콜 계산(예: 스테이킹 보상, 거버넌스 가중치 또는 총 hype 공급에 의존하는 로직)으로 이어질 수 있습니다.

**권장 사항**

현재 블록에서 이루어진 Hyper Core에 대한 입금을 추적하고 즉시 `getTotalProtocolHype`에 추가하십시오.
그런 다음 이중 계산을 방지하기 위해 다음 블록에서 크레딧되면 이러한 추적된 대기 중인 입금을 제거하십시오.
