# 소개

Pashov Audit Group은 해당 분야 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**SocksNFlops/rivus-staking** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Rivus 정보

Rivus Staking은 사용자가 토큰을 예치, 스테이킹 및 언스테이킹할 수 있도록 하며 수수료, 한도 및 기타 매개변수를 업데이트할 수 있는 유연성을 제공합니다. 여기에는 스테이킹 및 언스테이킹 수수료 계산, 한도 적용, 승인 관리, 일시 중지, 업그레이드 및 리베이스(rebase)와 같은 특정 작업 제어 메커니즘이 포함됩니다.

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

_검토 커밋 해시_ - [f5472fba6e56b14ec333f93cd8fe4a7bacb1293b](https://github.com/SocksNFlops/rivus-staking/tree/f5472fba6e56b14ec333f93cd8fe4a7bacb1293b)

_수정 검토 커밋 해시_ - [03b457bc6c4b22a02ef4d1a002317bb2f41b8ab4](https://github.com/SocksNFlops/rivus-staking/tree/03b457bc6c4b22a02ef4d1a002317bb2f41b8ab4)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `StakingToken`
- `RsTAO`
- `RsNMT`
- `RebasingERC20Upgradeable`

# 발견 사항

# [H-01] `rebase()`에서 `principle`이 업데이트되지 않아 언더플로 및 인출 실패 발생

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`StakingToken` 계약의 `principle` 변수는 계약에 예치된 기본 토큰의 총량을 추적하기 위한 것입니다. 그러나 토큰의 총 공급량을 조정하는(예: 이자 또는 보상 반영) `rebase` 함수 내에서 `principle` 변수는 새로운 총 공급량과 일치하도록 업데이트되지 않습니다.

이러한 불일치는 치명적인 문제로 이어집니다. 리베이스로 인해 총 공급량이 증가했지만 `principle`이 동일하게 유지되면 `principle`에 의존하는 후속 계산이 언더플로될 수 있습니다. 구체적으로 `_approveUnstakeRequest` 함수의 언스테이킹 프로세스 중에 계약은 `principle`에서 언스테이킹된 금액을 빼려고 시도합니다. 언스테이킹된 금액이 변경되지 않은 `principle`을 초과하면 이 뺄셈은 언더플로되어 트랜잭션이 실패합니다.

결과적으로 사용자는 총 공급량을 증가시키는 리베이스 후에 자금을 인출할 수 없게 되어, 특히 마지막으로 인출하는 사용자에게 DoS가 발생합니다.

**POC:**

```solidity
function test_rebase_doesnt_update_principle() public {
    uint256 amount = 1235038820;
    uint256 minStakingAmount = 1e6;
    uint16 stakingFeeBPS = 0;
    uint256 bridgingFee = 0;

    address rebaseAccount = makeAddr("Rebaser");
    address withdrawingAccount = makeAddr("Withdrawer");

    // Have the admin grant the WITHDRAWAL_ROLE  and REBASE_ROLE to the accounts
    vm.startPrank(admin);
    stakingToken.grantRole(stakingToken.REBASE_ROLE(), rebaseAccount);
    stakingToken.grantRole(stakingToken.WITHDRAWAL_ROLE(), withdrawingAccount);
    vm.stopPrank();

    // Have the config manager set configs
    vm.startPrank(configManager);
    stakingToken.setMinStakingAmount(minStakingAmount);
    stakingToken.setStakingFeeBPS(stakingFeeBPS);
    stakingToken.setBridgingFee(bridgingFee);
    stakingToken.setProtocolVault(protocolVault);
    stakingToken.setMaxDepositPerRequest(type(uint256).max);
    stakingToken.setCap(type(uint256).max);
    vm.stopPrank();

    // Mint the amount of undelying tokens to userA
    mockUnderlyingToken.mint(userA, amount);

    // Have userA approve the staking token to pull the underlying token from them
    vm.prank(userA);
    mockUnderlyingToken.approve(address(stakingToken), amount);

    // Have userA deposit half the amount of underlying tokens
    vm.startPrank(userA);
    stakingToken.deposit(amount/2);
    vm.stopPrank();

    // Have the account call rebase with the new total supply +20%
    vm.startPrank(rebaseAccount);
    stakingToken.rebase(stakingToken.totalSupply() * 6/5);
    vm.stopPrank();

    // Have userA deposit the second half of underlying tokens
    vm.startPrank(userA);
    stakingToken.deposit(amount/2);
    vm.stopPrank();

    // Get the staking token balance of userA
    uint256 unstakingAmount = stakingToken.balanceOf(userA);

    // Mint the unstakingAmount of underlyingTokens to the withdrawingAccount and approve the staking token to pull them
    vm.startPrank(withdrawingAccount);
    mockUnderlyingToken.mint(withdrawingAccount, unstakingAmount);
    mockUnderlyingToken.approve(address(stakingToken), unstakingAmount);
    vm.stopPrank();

    vm.startPrank(userA);
    stakingToken.requestUnstake(unstakingAmount);
    vm.stopPrank();

    IStakingToken.UnstakeRequest memory request = stakingToken.getUnstakeRequest(0);

    console.log("Asset Amount = %s", stakingToken.convertToAssets(request.shares));

    console.log("principle = %s", stakingToken.principle());

    // Have the withdrawingAccount approve the unstake request (Which will revert due to underflow)
    vm.startPrank(withdrawingAccount);
    vm.expectRevert(stdError.arithmeticError);
    stakingToken.approveUnstakeRequest();
    vm.stopPrank();
}
```

## 권장 사항

`rebase` 함수 내에서 `principle` 변수를 업데이트하여 총 공급량의 변경 사항을 정확하게 반영하십시오. `principle`을 조정된 총 공급량과 동기화하면 `principle`에 의존하는 모든 계산이 정확하게 유지되어 언더플로 오류를 방지하고 사용자가 문제 없이 자금을 인출할 수 있습니다.

구체적으로 `rebase` 함수에 다음과 유사한 로직을 추가하는 것을 고려하십시오.

```diff
function rebase(uint256 newTotalSupply) external onlyRole(REBASE_ROLE) {
    // Existing rebase logic...
    _updateApy(newTotalSupply, currentTotalSupply);
    lastRebaseTimestamp = block.timestamp;
    lastTotalSupply = currentTotalSupply;
    _rebase(newTotalSupply);

+   // Update principle to match the new total supply
+   principle = newTotalSupply;
}
```

이 조정은 리베이스 후 `principle`이 총 공급량과 일치하도록 하여 계약 재무 계산의 무결성을 유지합니다.

# [M-01] 수수료 포함으로 인한 잘못된 원금(principle) 추적

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

예금(deposit) 함수에서 `principle` 상태 변수는 실제로 스테이킹되지 않은 수수료를 포함한 전체 예금 금액을 추가하여 잘못 업데이트됩니다.

```solidity
function deposit(uint256 amount) external whenNotPaused returns (uint256 mintedAmount) {
    // Calculate fees
    (mintedAmount, stakingFee) = _calculateMintAmountAndFees(amount);

    // Incorrect: Updates principle with full amount including fees
    principle += amount;  // @audit - includes stakingFee and bridgingFee

    // Transfer and mint logic...
    IERC20(underlyingToken).safeTransfer(protocolVault, stakingFee);
    bool success = _bridge(mintedAmount + bridgingFee);
}
```

원금은 다음을 포함하는 총 예금 금액이 아니라 실제 스테이킹된 금액(`mintedAmount`)만 추적해야 합니다.

- 스테이킹 수수료 (`stakingFee`)
- 브리징 수수료 (`bridgingFee`)
- 실제로 스테이킹된 금액 (`mintedAmount`)

**영향**

- 프로토콜이 부풀려진 총 원금 금액을 보고합니다.
- TVL 계산이 올바르지 않게 됩니다.
- 잘못된 기본 값으로 인해 APY 계산이 왜곡될 수 있습니다.

## 권장 사항

실제 스테이킹된 금액만 포함하도록 원금을 업데이트하십시오.

# [M-02] 소수점 불일치로 인한 잘못된 페깅(pegging) 발생

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`StakingToken` 계약은 기본 토큰 `wTAO`에 페깅된 스테이킹 토큰을 생성하기 위한 것입니다. `IStakingToken` 인터페이스의 NatSpec 주석에 따르면:

```solidity
/**
 * @notice The address of the underlying token that this staking token is pegged to
 */
function underlyingToken() external view returns (address);
```

그러나 두 토큰 간의 소수점 자릿수 차이로 인해 상당한 불일치가 있습니다. 기본 토큰 `wTAO`는 **9 소수점**을 가지고 있는 반면, 스테이킹 토큰은 **18 소수점**을 가지고 있습니다.

차이로 인해 가치 표현에 불일치가 발생합니다.

- **의도된 페깅**: 1 `wTAO`(9 소수점을 고려하면 `1e9`단위)는 1 스테이킹 토큰(18 소수점을 고려하면 `1e18`단위)과 같아야 합니다.
- **실제 동작**: 사용자가 1 `wTAO`(`1e9`단위)를 예치하면 `1e9` 단위의 스테이킹 토큰을 받게 되며, 이는 18 소수점을 고려할 때 `0.000000001` 스테이킹 토큰에 불과합니다.

결과적으로 스테이킹 토큰은 기본 `wTAO` 토큰에 올바르게 페깅되지 않습니다.

**POC:**

```solidity
function test_deposit() public {

    uint256 amount = 1 * 10**mockUnderlyingToken.decimals();
    uint256 minStakingAmount = 1e6;
    uint16 stakingFeeBPS = 0;
    uint256 bridgingFee = 0;

    // Have the config manager set configs
    vm.startPrank(configManager);
    stakingToken.setMinStakingAmount(minStakingAmount);
    stakingToken.setStakingFeeBPS(stakingFeeBPS);
    stakingToken.setBridgingFee(bridgingFee);
    stakingToken.setProtocolVault(protocolVault);
    stakingToken.setMaxDepositPerRequest(type(uint256).max);
    stakingToken.setCap(type(uint256).max);
    vm.stopPrank();

    // Mint the amount of undelying tokens to userA
    mockUnderlyingToken.mint(userA, amount);

    // Have userA approve the staking token to pull the underlying token from them
    vm.prank(userA);
    mockUnderlyingToken.approve(address(stakingToken), amount);

    // Have userA mint the amount of staking tokens
    vm.startPrank(userA);
    stakingToken.deposit(amount);
    vm.stopPrank();

    // Get the staking token balance of userA
    uint256 stakingTokenBalance = stakingToken.balanceOf(userA);

    console.log("balance = %s", stakingTokenBalance);
}
```

**POC 출력**

```python
Ran 1 test for test/Attack.t.sol:AttackTest
[PASS] test_deposit() (gas: 365732)
Logs:
  balance = 1000000000

Suite result: ok. 1 passed; 0 failed; 0 skipped; finished in 1.21ms (172.65µs CPU time)

Ran 1 test suite in 3.65ms (1.21ms CPU time): 1 tests passed, 0 failed, 0 skipped (1 total tests)
```

## 권장 사항

페깅 문제를 해결하려면 스테이킹 토큰의 소수점을 조정하거나 소수점 차이를 고려하여 변환 로직을 수정하십시오.

1. **소수점 일치**: 스테이킹 토큰의 소수점을 기본 `wTAO` 토큰(9 소수점)과 일치하도록 설정하십시오. 이렇게 하면 두 토큰 간에 1:1 페깅이 보장됩니다.

   ```solidity
   function decimals() public view virtual override returns (uint8) {
       return 9; // Match the underlying wTAO token's decimals
   }
   ```

2. **변환 로직 조정**: 스테이킹 토큰의 소수점을 변경하는 것이 불가능한 경우, 금액을 적절하게 조정하도록 예금 및 인출 함수를 수정하십시오.

# [M-03] 사용자가 장기 스테이커를 희생하여 이익을 추출하기 위해 리베이스를 프론트러닝(frontrun)할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`StakingToken` 계약은 리베이스 프로세스 중에 프론트러닝 공격에 취약합니다. 온체인 이벤트를 모니터링하는 사용자는 특히 호출 데이터에서 총 공급량의 증가를 예측하거나 관찰할 수 있는 경우 리베이스가 임박한 시기를 감지할 수 있습니다. 그들은 다음과 같이 이를 악용할 수 있습니다.

1. **리베이스 프론트러닝**: 리베이스가 발생하기 직전에 상당한 양의 토큰을 예치하여 총 공급량에서 차지하는 점유율을 늘립니다.

2. **리베이스 혜택**: 리베이스가 총 공급량을 증가시키면 이러한 사용자는 단기 스테이크에 비해 불균형적인 몫의 새로 발행된 토큰을 받습니다.

3. **인출 백러닝(Back-running)**: 리베이스 직후 언스테이킹을 요청하여 장기적인 약속 없이 리베이스 보상을 획득합니다.

이러한 행동은 기회주의적 사용자가 부당한 이익을 추출하여 장기 스테이커들의 보유 가치를 효과적으로 희석시킬 수 있게 합니다. 이는 스테이킹 메커니즘의 공정성과 의도된 보상 분배를 훼손합니다.

## 권장 사항

사용자가 토큰을 언스테이킹하거나 인출하기 전에 최소 스테이킹 기간을 강제하십시오. 이렇게 하면 리베이스 혜택을 받은 후 즉시 인출하는 것을 방지할 수 있습니다.

# [L-01] 누락된 언스테이크 요청 취소 기능으로 인한 자금 잠금

`StakingToken` 계약에는 사용자가 보류 중인 언스테이크 요청을 취소할 수 있는 기능이 없습니다. `requestUnstake()`를 통해 요청이 제출되면 사용자는 취소할 수 없으며 요청이 처리될 때까지 기다려야 합니다. 사용자가 취소하고 싶어도 요청이 처리될 때까지 자금은 계약에 잠겨 있습니다.

```solidity
function requestUnstake(uint256 amount) external payable whenNotPaused {
    // Validate that the msg.value is equal to the gasFee
    if (msg.value < gasFee) {
        revert InsufficientGasFee(msg.value, gasFee);
    }

    // Validate that the amount is greater than 0
    if (amount == 0) {
        revert InvalidUnstakeAmount(amount);
    }

    // Build the unstake request
    UnstakeRequest memory request;
    request.shares = convertToShares(amount);
    request.recipient = msg.sender;
    request.requestTime = block.timestamp;

    // Add the unstake requests to the queue
    unstakeRequests[unstakeRequestsIndex + unstakeRequestsCount] = request;
    unstakeRequestsCount++;

    // Transfer the tokens to the staking contract to be locked
    _transfer(msg.sender, address(this), amount);

    // Emit the unstake request event
    emit UnstakeRequested(msg.sender, amount, request.shares);
}
```

### 권장 사항

취소 기능을 추가하는 것을 고려하십시오.

# [L-02] 제한 없는 리베이스 권한으로 인한 토큰 가치 조작 가능성

`rebase()` 함수는 REBASE_ROLE 보유자가 경계나 속도 제한 제어 없이 총 토큰 공급량을 임의로 수정할 수 있도록 합니다. 악의적이거나 손상된 역할 보유자는 토큰 가치를 과감하게 변경하고 과도한 리베이스를 통해 사용자 자금을 고갈시킬 수 있습니다.

현재 구현:

```solidity
function rebase(uint256 newTotalSupply) external onlyRole(REBASE_ROLE) {
    if (block.timestamp < lastRebaseTimestamp + rebaseDelay) {
        revert RebaseDelayNotElapsed(lastRebaseTimestamp, rebaseDelay, block.timestamp);
    }
    uint256 currentTotalSupply = totalSupply();
    _updateApy(newTotalSupply, currentTotalSupply);
    lastRebaseTimestamp = block.timestamp;
    lastTotalSupply = currentTotalSupply;
    _rebase(newTotalSupply);
}
```

주요 취약점:

1. 리베이스 작업당 최대 변경 한도 없음
2. 리베이스 방향(양수/음수)에 대한 검증 없음
3. 실제 수익률 또는 프로토콜 성능과의 상관관계 없음

리베이스 작업에 대한 엄격한 경계 및 검증 구현:

**리베이스 제한 추가**:

```solidity
contract StakingToken {
    uint256 public constant MAX_REBASE_PERCENT = 1000; // 10% in BPS
    uint256 public constant MIN_REBASE_INTERVAL = 1 days;

    // Track cumulative rebase changes
    uint256 public dailyRebaseAmount;
    uint256 public lastRebaseReset;

    function rebase(uint256 newTotalSupply)
        external
        onlyRole(REBASE_ROLE)
    {
        // Validate timing
        require(
            block.timestamp >= lastRebaseTimestamp + rebaseDelay,
            "Rebase delay not met"
        );

        // Reset daily tracking if needed
        if (block.timestamp >= lastRebaseReset + 1 days) {
            dailyRebaseAmount = 0;
            lastRebaseReset = block.timestamp;
        }

        uint256 currentSupply = totalSupply();

        // Calculate absolute change
        uint256 changeAmount;
        if (newTotalSupply > currentSupply) {
            changeAmount = newTotalSupply - currentSupply;
            require(
                changeAmount * 10000 <= currentSupply * MAX_REBASE_PERCENT,
                "Rebase increase too large"
            );
        } else {
            changeAmount = currentSupply - newTotalSupply;
            require(
                changeAmount * 10000 <= currentSupply * MAX_REBASE_PERCENT,
                "Rebase decrease too large"
            );
        }

        // Track daily changes
        dailyRebaseAmount += changeAmount;
        require(
            dailyRebaseAmount * 10000 <= currentSupply * MAX_REBASE_PERCENT,
            "Daily rebase limit exceeded"
        );

        // Perform rebase
        _updateApy(newTotalSupply, currentSupply);
        _rebase(newTotalSupply);

        emit RebaseExecuted(
            currentSupply,
            newTotalSupply,
            changeAmount,
            block.timestamp
        );
    }
}
```

# [L-03] `renounceRole`은 재정의(override)되고 되돌려져야(revert) 함

프로토콜은 AccessControlUpgradeable을 사용하고 `DEFAULT_ADMIN_ROLE`을 설정합니다. AccessControlUpgradeable에는 호출자의 역할을 취소하는 `renounceRole` 함수가 있습니다.

```
  function renounceRole(bytes32 role, address callerConfirmation) public virtual {
        if (callerConfirmation != _msgSender()) {
            revert AccessControlBadConfirmation();
        }

        _revokeRole(role, callerConfirmation);
    }
```

일반적으로 이 함수는 우발적인 포기, 특히 `DEFAULT_ADMIN_ROLE`을 방지하기 위해 재정의되고 되돌려집니다.

가장 좋은 방법은 함수를 재정의하고 되돌리는 것입니다.

# [L-04] GasFee는 0이 아니어야 함

사용자는 `StakingToken.requestUnstake()`를 호출하여 리베이스 토큰을 기본 토큰으로 다시 변환합니다. `requestUnstake()`가 호출되면 `WITHDRAWAL_ROLE`이 언스테이킹 요청을 승인해야 합니다. 언스테이킹 요청은 순차적인 순서로 되어 있으므로 다음 언스테이크가 승인되기 전에 이전 언스테이크가 승인되어야 합니다.

`requestUnstake()`에는 `gasFee`가 있지만 이 gasFee는 0으로 설정될 수 있습니다.

```
  function setGasFee(uint256 gasFee_) external onlyRole(CONFIG_ROLE) {
         if (gasFee_ > 0.01 ether) {
             revert InvalidGasFee(gasFee_);
         }
         gasFee = gasFee_;
         emit GasFeeUpdated(gasFee_);
     }
```

`gasFee`가 0이면 사용자는 `requestUnstake`를 스팸하여 일시적으로 언스테이크 주문에 DoS를 걸 수 있습니다.

누군가가 언스테이킹 시퀀스를 스팸하는 것을 방지하기 위해 `gasFee`에 최소 합계를 설정하고 `require(gasFee_ > 0.0001 ether)`를 고려하십시오.

# [L-05] 초과 `msg.value`가 사용자에게 반환되지 않음

`StakingToken.requestUnstake()`에서 호출자는 함수 실행을 계속하려면 일정량의 `gasFee`를 보내야 합니다.

```
  function requestUnstake(uint256 amount) external payable whenNotPaused {
         // Validate that the msg.value is equal to the gasFee
         if (msg.value < gasFee) {
             revert InsufficientGasFee(msg.value, gasFee);
         }
```

호출자가 초과량의 `msg.value`를 보내면 사용자에게 반환되지 않습니다.

사용자가 `gasFee`를 초과 지불하지 않도록 엄격한 동등성 `msg.value == gasFee`를 설정하는 것을 고려하십시오.

# [L-06] `convertToShares()` 반올림으로 인한 잠재적인 지급 불능 문제

`RebasingERC20Upgradeable` 계약에서 `convertToShares` 함수는 자산을 주식(shares)으로 변환할 때 반올림합니다. 구체적으로 `Math.Rounding.Ceil`과 함께 `Math.mulDiv`를 사용합니다.

```solidity
function convertToShares(uint256 assets) public view virtual returns (uint256) {
    RebasingERC20Storage storage $ = _getRebasingERC20Storage();
    if ($._totalShares == 0 && $._totalSupply == 0) {
        return assets * (10 ** _decimalsOffset());
    } else {
        return Math.mulDiv(assets, $._totalShares, $._totalSupply, Math.Rounding.Ceil);
    }
}
```

이 반올림 방법은 정확한 비례 금액보다 더 많은 주식을 잠재적으로 부여하기 때문에 사용자가 언스테이크를 요청할 때 사용자에게 유리합니다. 시간이 지남에 따라, 특히 트랜잭션 양이 많을 때 이러한 작은 불일치가 축적되어 프로토콜이 실제로 보유한 것보다 더 많은 자산을 분배하게 될 수 있습니다. 이는 사용자가 상환한 총 주식이 계약에서 사용 가능한 실제 자산을 초과하는 지급 불능 문제를 일으킬 수 있습니다.

자산에서 주식을 계산할 때 반올림하는 대신 내림(round down)하도록 `convertToShares` 함수를 수정하십시오. 이렇게 변경하면 사용자가 약간 더 적은 수의 주식을 받게 되어 프로토콜이 자산을 과도하게 할당하는 것을 방지할 수 있습니다.

`Math.mulDiv` 함수에서 반올림 방향을 `Math.Rounding.Down`으로 변경하거나 반올림 매개변수를 생략하여(기본값은 내림이므로) 이를 구현할 수 있습니다.

```diff
function convertToShares(uint256 assets) public view virtual returns (uint256) {
    RebasingERC20Storage storage $ = _getRebasingERC20Storage();
    if ($._totalShares == 0 && $._totalSupply == 0) {
        return assets * (10 ** _decimalsOffset());
    } else {
+       return Math.mulDiv(assets, $._totalShares, $._totalSupply);
+       // or explicitly specify rounding down
+       // return Math.mulDiv(assets, $._totalShares, $._totalSupply, Math.Rounding.Down);
    }
}
```

내림을 함으로써 프로토콜은 지급 불능으로 이어질 수 있는 누적 반올림 오류로부터 스스로를 보호합니다. 이 접근 방식은 모범 사례와 일치하여 시스템이 모든 참가자에게 지급 능력을 유지하고 공정하도록 보장합니다.
