# 소개

**Pashov Audit Group**은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의해 주세요.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**lido-fiv** 저장소에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# lido-fiv 소개

Saffron Lido Fixed Income Vaults는 고정 및 가변 수익 교환을 가능하게 합니다. Saffron Lido Vaults의 수익은 Lido ETH 스테이킹에서 생성됩니다. 고정(Fixed) 측 참가자는 볼트에 ETH를 예치하고 선불로 고정 금액을 지급받으며, 이 ETH는 Lido에 스테이킹됩니다. 가변(Variable) 측은 볼트 수명 동안 생성된 모든 스테이킹 수익을 얻는 대가로 고정 금액을 지급합니다. 당사자들은 락업 기간, 총 자산 가치, 고정 지급 금액 등을 포함한 조건에 미리 합의합니다.

# 리스크 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 훔치거나 잃을 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 있는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 너무 비현실적이거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (배포 전이라면 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

**_검토 커밋 해시_ - [064791d13381af094a921970a0e280961c0049ad](https://github.com/saffron-finance/lido-fiv/tree/064791d13381af094a921970a0e280961c0049ad)**

**_수정 검토 커밋 해시_ - [61a8273ec2b941651a444b645084b875ac1b741a](https://github.com/saffron-finance/lido-fiv/tree/61a8273ec2b941651a444b645084b875ac1b741a)**

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `AdminLidoAdapter`
- `Initialized`
- `LidoAdapter`
- `LidoVault`
- `NonTransferrableVaultBearerToken`
- `VaultBearerToken`
- `VaultFactory`
- `interfaces/**`

# 발견 사항

# [C-01] 가변 참가자에 대한 잘못된 인출 금액 계산

## 심각도

**영향:** 높음, 잘못된 인출 금액으로 인해 볼트가 모든 인출을 처리하지 못할 수 있음

**가능성:** 높음, 가변 참가자가 수익을 인출할 때 항상 발생

## 설명

가변 참가자가 `vaultEndedWithdraw()`를 호출하면, `calculateVariableWithdrawState()`를 사용하여 참가자의 `totalEarnings` 및 `feeEarnings` 지분에 따라 인출할 `sendAmount`를 결정합니다.

`totalEarnings`와 `feeEarnings` 모두 인출된 구성 요소인 `withdrawnStakingEarnings`와 `withdrawnFeeEarnings`를 가지고 있습니다. 이들은 볼트가 진행 중일 때 가변 참가자가 인출한 스테이킹/수수료 수익을 추적하는 데 사용됩니다.

문제는 `vaultEndedStakingEarnings`와 `feeEarnings`는 `vaultEndedWithdraw()`에서 감소되는 반면, `withdrawnStakingEarnings`와 `withdrawnFeeEarnings`는 감소되지 않는다는 것입니다. 이들은 `totalEarnings`와 `feeEarnings`를 결정하는 데 사용되므로, 일관성 없는 동작으로 인해 지분 계산이 올바르지 않게 됩니다.

`variableBearerToken`이 `vaultEndedWithdraw()`에서 소각되므로, `totalEarnings`와 `feeEarnings`는 그에 따라 감소되어야 합니다. 그러나 `withdrawnStakingEarnings`와 `withdrawnFeeEarnings`가 감소되지 않으므로, 후속 `vaultEndedWithdraw()`는 예상보다 높은 `sendAmount`를 계산하게 됩니다. 이는 볼트에 자금이 부족하여 모든 인출을 처리할 수 없게 만듭니다.

```Solidity
  function vaultEndedWithdraw(uint256 side) internal {
      ...
      //@audit totalEarnings will not be reduced correctly as withdrawnStakingEarnings is not reduced
      uint256 totalEarnings = withdrawnStakingEarnings + vaultEndedStakingEarnings;

      if (totalEarnings > 0) {
        //@audit stakingEarningsShare will be wrong as totalEarnings will be incorrect
        (uint256 currentState, uint256 stakingEarningsShare) = calculateVariableWithdrawState(
          totalEarnings,
          variableToWithdrawnStakingEarnings[msg.sender]
        );
        stakingShareAmount = stakingEarningsShare;
        variableToWithdrawnStakingEarnings[msg.sender] = currentState;
      }

      uint256 feeShareAmount = 0;
      //@audit totalFees will not be reduced correctly as withdrawnFeeEarnings is not reduced
      uint256 totalFees = withdrawnFeeEarnings + feeEarnings;

      if (totalFees > 0) {
        //@audit feesShare will be wrong as totalFees will be incorrect
        (uint256 currentState, uint256 feesShare) = calculateVariableWithdrawState(
          totalFees,
          variableToWithdrawnFees[msg.sender]
        );
        feeShareAmount = feesShare;
        variableToWithdrawnFees[msg.sender] = currentState;
      }

      //@audit only vaultEndedStakingEarnings and feeEarnings are reduced
      vaultEndedStakingEarnings -= stakingShareAmount;
      feeEarnings -= feeShareAmount;

      variableBearerToken.burn(msg.sender, bearerBalance);

      uint256 sendAmount = stakingShareAmount + feeShareAmount;
      sendFunds(sendAmount);

      emit VariableFundsWithdrawn(sendAmount, msg.sender, isStarted(), isEnded());
      return;
    }
```

## 권고 사항

이상적으로는 단순성을 위해 일관된 지분 계산을 가져야 하므로, 볼트 진행 중 인출과 동일한 지분 금액 계산을 사용하는 것이 좋습니다.

즉, 볼트 종료 인출의 경우 `totalEarnings`와 `totalFees`는 인출 시 감소되어서는 안 됩니다. 그리고 `variableBearerToken`은 여전히 소각되어야 하므로 지분 금액 계산을 위해 `variableBearerToken.totalSupply()` 대신 `variableSideCapacity`를 사용할 수 있습니다.

# [C-02] 고정 참가자가 예상보다 많은 프리미엄을 청구할 수 있음

## 심각도

**영향:** 높음, 잘못된 프리미엄 금액으로 인해 일부 참가자가 다른 참가자보다 더 많이 인출할 수 있음

**가능성:** 높음, 고정 참가자가 프리미엄을 청구할 때 항상 발생

## 설명

`claimFixedPremium()`은 볼트가 시작될 때 고정 참가자가 고정 프리미엄을 청구할 수 있도록 호출됩니다. 그리고 청구될 프리미엄 금액인 `sendAmount`는 `fixedLidoSharesTotalSupply()` 대비 `fixedClaimTokens` 잔액을 사용하여 참가자의 `variableSideCapacity` 지분에 의해 결정됩니다.

`fixedLidoSharesTotalSupply()`는 `fixedBearerToken.totalSupply()`와 `fixedClaimTokens.totalSupply()`의 합이므로, 고정 참가자가 진행 중인 볼트 인출을 수행할 때 참가자의 `fixedBearerToken` 잔액이 소각되면서 감소하게 됩니다.

이로 인해 `claimFixedPremium()` 내에서 `sendAmount` 계산에 문제가 발생하여, 고정 참가자가 볼트 인출 후 `variableSideCapacity`에 대해 예상보다 높은 지분을 얻게 됩니다. 이를 통해 고정 참가자는 일정량의 인출 후 프리미엄을 청구함으로써 허용된 것보다 더 많은 프리미엄을 청구할 수 있습니다.

```Solidity
  function claimFixedPremium() external nonReentrant {
    require(isStarted(), "CBS");

    // Check and cache balance for gas savings
    uint256 claimBal = fixedClaimToken.balanceOf(msg.sender);
    require(claimBal > 0, "NCT");

    //@audit fixedLidoSharesTotalSupply() will reduces upon withdrawal, but variableSideCapacity is not.
    //       this will cause sendAmount to be higher for subsequent claims
    // Send a proportional share of the total variable side deposits (premium) to the fixed side depositor
    uint256 sendAmount = claimBal.mulDiv(1e18, fixedLidoSharesTotalSupply()).mulDiv(variableSideCapacity, 1e18);

    // Track premiums
    userToFixedUpfrontPremium[msg.sender] = sendAmount;

    (bool sent, ) = msg.sender.call{value: sendAmount}("");
    require(sent, "ETF");

    // Mint bearer token
    fixedBearerToken.mint(msg.sender, claimBal);

    // Burn claim tokens
    fixedClaimToken.burn(msg.sender, claimBal);

    emit FixedPremiumClaimed(sendAmount, claimBal, msg.sender);
  }
```

## 권고 사항

`claimFixedPremium()`에서 L439에 `fixedLidoSharesTotalSupply()` 대신 `fixedSideCapacity`를 다음과 같이 사용하세요:

```Solidity
uint256 sendAmount = fixedETHDepositToken.balanceOf(msg.sender).mulDiv(variableSideCapacity, fixedSideCapacity);
```

# [C-03] 고정 측 참가자가 프리미엄을 훔칠 수 있음

## 심각도

**영향:** 높음, 가변 측 참가자가 예치금을 잃을 수 있음

**가능성:** 높음, 벡터가 쉽게 수행될 수 있음

## 설명

`vaultEndedWithdraw` 함수는 볼트 기간이 종료된 후 사용자가 모든 스테이킹 잔액 인출을 요청할 수 있도록 합니다(L754). 볼트 잔액이 Lido의 최소 인출 금액보다 적은 경우 - 새로운 인출 요청을 생성할 필요가 없고 사용자가 수익이 있는 경우 인출할 수 있으므로 함수는 볼트를 `vaultEndedWithdrawalsFinalized` 상태로 설정합니다(L752):

```solidity
File: LidoVault.sol
746:   function vaultEndedWithdraw(uint256 side) internal {
747:     if (vaultEndedWithdrawalRequestIds.length == 0 && !vaultEndedWithdrawalsFinalized) {
748:       emit VaultEnded(block.timestamp, msg.sender);
749:
750:       if (lidoAdapter.stakingBalance() < lidoAdapter.minStETHWithdrawalAmount()) {
751:         // not enough staking ETH to withdraw just override vault ended state and continue the withdraw
752:         vaultEndedWithdrawalsFinalized = true;
753:       } else {
754:         vaultEndedWithdrawalRequestIds = lidoAdapter.requestEntireBalanceWithdraw(msg.sender);
755:
756:         emit LidoWithdrawalRequested(msg.sender, vaultEndedWithdrawalRequestIds, side, isStarted(), isEnded());
757:         // need to call finalizeVaultEndedWithdrawals once request is processed
758:         return;
759:       }
760:     }
...
```

그러나 마지막 경우 함수는 `finalizeVaultEndedWithdrawals`가 L673-L676에서 수행하는 것과 동일한 방식으로 진행 중 단계에서 생성된 모든 요청을 청구하지 못했습니다:

```solidity
File: LidoVault.sol
665:   function finalizeVaultEndedWithdrawals(uint256 side) external nonReentrant {
666:     require(side == FIXED || side == VARIABLE, "IS");
667:     require(vaultEndedWithdrawalRequestIds.length != 0 && !vaultEndedWithdrawalsFinalized, "WNR");
668:
669:     vaultEndedWithdrawalsFinalized = true;
670:
671:     // claim any ongoing fixed withdrawals too
672:     uint256 arrayLength = fixedOngoingWithdrawalUsers.length;
673:     for (uint i = 0; i < arrayLength; i++) {
674:       address fixedUser = fixedOngoingWithdrawalUsers[i];
675:       fixedToPendingWithdrawalAmount[fixedUser] = claimFixedVaultOngoingWithdrawal(fixedUser);
676:     }
...
```

이로 인해 다음 시나리오가 발생할 수 있습니다:

1. 새 볼트가 생성되었습니다. 앨리스는 가변 측 용량의 90%를 넣습니다.
2. 밥은 고정 측 용량의 100%와 가변 측의 마지막 10%를 넣습니다. 볼트가 시작됩니다.
3. 밥은 모든 프리미엄을 청구하고 고정 예치금의 진행 중 인출을 요청합니다.
4. 볼트가 종료되고, 앨리스는 볼트를 `vaultEndedWithdrawalsFinalized` 상태로 만드는 `withdraw` 함수를 호출합니다. 수익이 0이므로 앨리스는 현재 소각된 `variableBearerToken`의 90% 공급에 대해 0을 받습니다.
5. 밥은 `finalizeVaultOngoingFixedWithdrawals`를 호출하여 프리미엄과 조기 종료 수수료를 뺀 고정 측 예치금을 받습니다.
6. 밥은 `withdraw`를 호출하고 이제 유일한 가변 측 참가자이므로 앨리스의 초기 예치금 합계와 동일한 모든 프리미엄과 조기 종료 수수료를 받습니다.

## 권고 사항

`lidoAdapter.stakingBalance()`가 `lidoAdapter.minStETHWithdrawalAmount()`보다 작더라도 볼트는 여전히 `vaultEndedWithdraw` 함수에서 모든 고정 진행 중 인출 요청을 청구해야 합니다.

# [H-01] 마지막 인출 요청이 100 wei 미만일 때 `finalizeVaultEndedWithdrawals()` 실패

## 심각도

**영향:** 높음, 이 문제로 인해 stETH 인출이 방지됨

**가능성:** 중간, 마지막 인출 요청이 < 100 wei일 때

## 설명

LidoVault가 종료되면 `LidoVault.finalizeVaultEndedWithdrawals()`를 호출하여 Lido에서 전체 stETH 잔액을 인출할 수 있습니다. 이는 `LidoAdapter.requestEntireBalanceWithdraw()`를 트리거하고, 이는 다시 `_requestWithdraw()`를 호출하여 인출을 수행합니다. 최대 인출은 1000 stETH (MAX_STETH_WITHDRAWAL_AMOUNT)이므로, 아래 코드와 같이 인출은 1000 stETH의 여러 인출 요청으로 분할됩니다. 최소 인출 금액은 require 문에 표시된 대로 100 wei(stETH)입니다.

```Solidity
  /// @notice Request a withdrawal on Lido to exchange stETH for ETH
  /// @param stETHAmount amount of stETH to withdraw
  /// @return requestIds Ids of the withdrawal requests
  function _requestWithdraw(address user, uint256 stETHAmount) internal returns (uint256[] memory) {
    require(stETHAmount >= MIN_STETH_WITHDRAWAL_AMOUNT, "WM");

    // Approve the withdrawal queue contract to pull the stETH tokens
    bool approved = lido.approve(address(lidoWithdrawalQueue), stETHAmount);
    require(approved, "AF");

    uint256[] memory amounts = new uint256[](calculateWithdrawals(stETHAmount));
    if (stETHAmount > MAX_STETH_WITHDRAWAL_AMOUNT) {
      uint256 amountLeft = stETHAmount;
      uint256 i = 0;
      while (amountLeft > 0) {
        if (amountLeft >= MAX_STETH_WITHDRAWAL_AMOUNT) {
          amounts[i] = MAX_STETH_WITHDRAWAL_AMOUNT;
          amountLeft -= MAX_STETH_WITHDRAWAL_AMOUNT;
        } else {
          amounts[i] = amountLeft;
          amountLeft = 0;
        }
        i++;
      }
    } else {
      amounts[0] = stETHAmount;
    }

    //@audit - the following will revert if last withdrawal request is less than 100 wei
    // Submit the stETH to the withdrawal queue
    uint256[] memory requestIds = lidoWithdrawalQueue.requestWithdrawals(amounts, address(this));
    require(requestIds.length > 0, "IWR");

    emit WithdrawalRequested(stETHAmount, requestIds, user);

    return requestIds;
  }
```

문제는 마지막 인출 요청이 100 wei(stETH) 미만인 경우 `lidoWithdrawalQueue.requestWithdrawals(amounts, address(this))` 호출이 되돌려진다는(revert) 것입니다.

이로 인해 `LidoVault`가 볼트 종료 인출을 완료하지 못하게 되어 모든 stETH가 `LidoAdapter` 내에 잠기게 됩니다.

## 권고 사항

모든 인출 요청이 최소 100 wei인지 확인하세요.

# [H-02] 적어도 한 명의 고정 참가자가 진행 중인 볼트에서 인출하면 `finalizeVaultEndedWithdrawals`가 서비스 거부(DoS)됨

## 심각도

**영향:** 중간, 볼트에서 인출하는 것이 일시적으로 차단되므로

**가능성:** 높음, 진행 중인 볼트에서의 인출은 일반적인 사용자 흐름으로 예상되므로

## 설명

고정 참가자는 `withdraw` 함수를 사용하여 진행 중인 볼트에서 인출을 요청할 수 있습니다. 이는 사용자를 대신하여 Lido에 인출 요청을 호출하고 L549에서 `fixedOngoingWithdrawalUsers` 배열에 해당 사용자의 주소를 푸시합니다:

```solidity
File: LidoVault.sol
458:   function withdraw(uint256 side) external nonReentrant {
..
516:       // Vault started and in progress
517:     } else if (!isEnded()) {
518:       if (side == FIXED) {
519:         require(
520:           fixedToVaultOngoingWithdrawalRequestIds[msg.sender].requestIds.length == 0 &&
521:             fixedToVaultNotStartedWithdrawalRequestIds[msg.sender].length == 0,
522:           "WAR"
523:         );
524:
525:         // require that they have claimed their upfront premium to simplify this flow
526:         uint256 bearerBalance = fixedBearerToken.balanceOf(msg.sender);
527:         require(bearerBalance > 0, "NBT");
528:
529:         uint256 initialDepositAmount = fixedETHDepositToken.balanceOf(msg.sender);
530:         require(initialDepositAmount > 0, "NET");
531:
532:         // since the vault has started only withdraw their initial fixed deposit - unless we are in a loss
533:         uint256 withdrawAmount = initialDepositAmount;
534:         uint256 lidoStETHBalance = lidoAdapter.stakingBalance();
535:         uint256 fixedETHDeposits = fixedETHDepositToken.totalSupply();
536:
537:         if (fixedETHDeposits > lidoStETHBalance) {
538:           // our staking balance if less than our initial ETH deposits only return a proportional amount of the balance to the fixed user
539:           withdrawAmount = lidoStETHBalance.mulDiv(initialDepositAmount, fixedETHDeposits);
540:         }
541:
542:         fixedBearerToken.burn(msg.sender, bearerBalance);
543:         fixedETHDepositToken.burn(msg.sender, initialDepositAmount);
544:
545:         fixedToVaultOngoingWithdrawalRequestIds[msg.sender] = WithdrawalRequest({
546:           requestIds: lidoAdapter.requestWithdrawViaETH(msg.sender, withdrawAmount),
547:           timestamp: block.timestamp
548:         });
549:         fixedOngoingWithdrawalUsers.push(msg.sender);
...
```

나중에 사용자는 `claimFixedVaultOngoingWithdrawal` 도우미 함수를 호출하는 `finalizeVaultOngoingFixedWithdrawals` 함수를 사용하여 인출을 완료할 수 있습니다. 마지막 함수는 사용자의 요청을 청구하고 해당 수수료 금액 등을 계산합니다. 가장 중요한 것은 이 도우미 함수가 L876에서 `fixedOngoingWithdrawalUsers` 배열에서 사용자 주소를 삭제한다는 것입니다:

```solidity
File: LidoVault.sol
637:   function finalizeVaultOngoingFixedWithdrawals() external nonReentrant {
638:     uint256 sendAmount = claimFixedVaultOngoingWithdrawal(msg.sender);
639:
640:     sendFunds(sendAmount);
641:
642:     emit FixedFundsWithdrawn(sendAmount, msg.sender, isStarted(), isEnded());
643:   }
...
863:   function claimFixedVaultOngoingWithdrawal(address user) internal returns (uint256) {
864:     WithdrawalRequest memory request = fixedToVaultOngoingWithdrawalRequestIds[user];
865:     uint256[] memory requestIds = request.requestIds;
866:     require(requestIds.length != 0, "WNR");
867:
868:     uint256 upfrontPremium = userToFixedUpfrontPremium[user];
869:
870:     delete userToFixedUpfrontPremium[user];
871:     delete fixedToVaultOngoingWithdrawalRequestIds[user];
872:
873:     uint256 arrayLength = fixedOngoingWithdrawalUsers.length;
874:     for (uint i = 0; i < arrayLength; i++) {
875:       if (fixedOngoingWithdrawalUsers[i] == user) {
876:         delete fixedOngoingWithdrawalUsers[i];
877:       }
878:     }
..
```

Solidity의 `delete` 키워드는 배열 요소에 0 값을 할당할 뿐이며, 배열의 길이는 동일하게 유지됩니다. 따라서 이 삭제는 나중에 `finalizeVaultEndedWithdrawals`를 호출할 때 DOS를 유발합니다.
L675에서 컨트랙트는 `fixedOngoingWithdrawalUsers` 배열의 모든 주소에 대해 도우미 함수를 호출하려고 시도하는데, 그중 하나는 이전에 사용자 주소를 삭제한 후 배열에 남아 있는 `address(0)`입니다:

```solidity
File: LidoVault.sol
665:   function finalizeVaultEndedWithdrawals(uint256 side) external nonReentrant {
...
671:     // claim any ongoing fixed withdrawals too
672:     uint256 arrayLength = fixedOngoingWithdrawalUsers.length;
673:     for (uint i = 0; i < arrayLength; i++) {
674:       address fixedUser = fixedOngoingWithdrawalUsers[i];
675:       fixedToPendingWithdrawalAmount[fixedUser] = claimFixedVaultOngoingWithdrawal(fixedUser);
676:     }
...
```

`claimFixedVaultOngoingWithdrawal`은 `address(0)` 매개변수에 대해 관련된 인출 요청이 없으므로(L866) 되돌려집니다. 관리자만이 `adminSettleDebt` 함수를 사용하여 볼트 인출 흐름을 복원할 수 있습니다.

## 권고 사항

`claimFixedVaultOngoingWithdrawal`은 `address(0)`이 `user` 매개변수로 제공되는 경우 0을 반환해야 합니다.

# [H-03] adminSettleDebt 타임락 기간 동안 고정 참가자에게 잘못된 페널티 부과

## 심각도

**영향:** 높음, 고정 참가자가 손실을 입음

**가능성:** 중간, 관리자가 부채를 청산할 때 발생

## 설명

`initiatingAdminSettleDebt()`가 호출되면 부채 청산 프로세스가 초기화됩니다. 이로 인해 관리자가 `adminSettleDebt()`를 호출하고 stETH 잔액을 `AdminLidoAdapter`로 전송하기 전에 3일의 타임락(`adminSettleDebtLockPeriod` 기준)이 시작됩니다. 이 타임락은 초기화 중에 설정된 `settleDebtAmount`가 적절하게 보상하지 않을 때(예 : 불량 관리자가 큰 손실로 부채를 청산하려고 시도함) 고정/가변 참가자가 `adminSettleDebt()` 전에 인출할 수 있도록 설계된 것으로 보입니다.

그러나 고정 참가자가 `initiatingAdminSettleDebt()` 이후 그리고 볼트 종료 전에 예치금을 인출하면, `calculateFixedEarlyExitFees()`에서 계산된 대로 조기 인출 수수료로 페널티를 받게 됩니다. 이는 타임락의 목적을 무효화하므로 잘못된 것이며, 타임락에 관계없이 고정 참가자가 과소 보상을 받게 합니다.

```Solidity
  function calculateFixedEarlyExitFees(
    uint256 upfrontPremium,
    uint256 timestampRequested
  ) internal view returns (uint256) {
    uint256 remainingProportion = (endTime > timestampRequested ? endTime - timestampRequested : 0).mulDiv(
      1e18,
      duration
    );

    //@audit the scaling fees will should not be applied after initiatingAdminSettleDebt() is called
    // Calculate the scaling fee based on the quadratic scaling factor and earlyExitFeeBps
    uint256 earlyExitFees = upfrontPremium.mulDiv(
      earlyExitFeeBps.mulDiv(remainingProportion.mulDiv(remainingProportion, 1e18), 1e18),
      10000
    );

    // Calculate the amount to be paid back of their original upfront claimed premium, not influenced by quadratic scaling
    earlyExitFees += upfrontPremium - upfrontPremium.mulDiv(timestampRequested - startTime, duration);

    return earlyExitFees;
  }
```

## 권고 사항

`initiatingAdminSettleDebt()`가 호출된 후에는 `calculateFixedEarlyExitFees()`가 스케일링 수수료를 적용해서는 안 됩니다.

# [H-04] 부채 청산으로 볼트가 일찍 종료되더라도 고정 참가자가 전체 프리미엄을 얻음

## 심각도

**영향:** 높음, 가변 참가자로부터 프리미엄을 훔칠 수 있음

**가능성:** 중간, 관리자가 부채를 청산할 때 발생

## 설명

전체 볼트 기간 전에 `adminSettleDebt()`가 호출되면 볼트가 일찍 종료되어 모든 참가자가 예치금을 인출할 수 있게 됩니다.

이 시나리오에서 부채는 가변 참가자가 조기 볼트 종료 시간에 따라 수익과 고정 프리미엄의 일부를 모두 인출할 수 있도록 양의 스테이킹 수익으로 청산됩니다.

문제는 고정 참가자가 남은 시간(볼트에 있지 않은 시간)에 기반한 부분 프리미엄을 갚지 않아, 볼트가 일찍 종료되었을 때에도 전체 초기 예치금과 전체 고정 프리미엄을 모두 인출할 수 있다는 것입니다. 이는 아래와 같이 `vaultEndedWithdraw()`에서 부분 프리미엄을 반환하는 계산이 누락되었기 때문입니다.

따라서 가변 참가자는 부분 프리미엄을 돌려받지 못하므로 손실을 입게 됩니다.

또한 악의적인 관리자는 이를 악용하여 고정 참가자로 예치 및 인출하여 전체 프리미엄을 얻을 수 있습니다.

```Solidity
  function vaultEndedWithdraw(uint256 side) internal {
    ...

    // have to call finalizeVaultEndedWithdrawals first
    require(vaultEndedWithdrawalsFinalized, "WNF");

    if (side == FIXED) {
      require(
        fixedToVaultOngoingWithdrawalRequestIds[msg.sender].requestIds.length == 0 &&
          fixedToVaultNotStartedWithdrawalRequestIds[msg.sender].length == 0,
        "WAR"
      );

      uint256 sendAmount = fixedToPendingWithdrawalAmount[msg.sender];

      // they submitted a withdraw before the vault had ended and the vault ending should have claimed it
      if (sendAmount > 0) {
        delete fixedToPendingWithdrawalAmount[msg.sender];
      } else {
        uint256 bearerBalance = fixedBearerToken.balanceOf(msg.sender);
        require(bearerBalance > 0, "NBT");

        //@audit fixed participants will obtain the full deposit as vaultEndedFixedDepositsFunds
        //        will be equal to fixedETHDepositToken.totalSupply() when debt is settled with positive earnings
        sendAmount = fixedBearerToken.balanceOf(msg.sender).mulDiv(
          vaultEndedFixedDepositsFunds,
          fixedLidoSharesTotalSupply()
        );

        fixedBearerToken.burn(msg.sender, bearerBalance);
        fixedETHDepositToken.burn(msg.sender, fixedETHDepositToken.balanceOf(msg.sender));
        vaultEndedFixedDepositsFunds -= sendAmount;
      }

      sendFunds(sendAmount);

      emit FixedFundsWithdrawn(sendAmount, msg.sender, isStarted(), true);
      return;
    } else {
```

## 권고 사항

관리자 부채 청산이 볼트를 일찍 종료할 때, `feeEarnings`의 일부로 가변 참가자에게 반환될 프리미엄 금액만큼 `vaultEndedFixedDepositsFunds`를 차감하세요.

# [H-05] 먼지(Dust) 예치 및 인출 스팸으로 볼트 자금이 일시적으로 잠길 수 있음

## 심각도

**영향:** 중간, 볼트 자금이 일시적으로 잠김

**가능성:** 높음, 항상 발생 가능

## 설명

`finalizeVaultEndedWithdrawals()`는 진행 중인 고정 인출을 포함하여 모든 고정 인출을 완료하기 위해 호출되어야 합니다.

이슈는 조작되어 제한이 없어질 수 있는 `fixedOngoingWithdrawalUsers`를 반복한다는 것입니다. 공격자는 100 wei(여러 EOA 사용)의 먼지 고정 예치를 스팸한 다음 볼트가 진행 중일 때 이를 인출하여 `fixedOngoingWithdrawalUsers`를 극도로 크게 만들 수 있습니다. 이로 인해 `finalizeVaultEndedWithdrawals()`가 DoS되어 볼트 종료 시 인출을 방지하고 결과적으로 볼트 자금이 잠기게 됩니다.

관리자가 채무 청산 기능으로 문제를 복구할 수 있으므로 이를 중간 영향으로 분류했습니다.

먼지 예치 및 인출로 발생할 수 있는 또 다른 문제는 공격자가 볼트 시작을 방해할 수 있다는 것입니다. 공격은 여러 1 wei 가변 예치를 스팸한 다음 마지막 예금자를 프론트러닝하여 볼트가 시작될 때마다 그중 하나를 인출함으로써 수행될 수 있습니다.

## 권고 사항

가능한 완화책 중 하나는 고정/가변 참가자에 대해 더 높은 최소 예치 금액(예: 0.1 ETH)을 설정하여 공격 비용을 높이는 것입니다.

채워지지 않은 용량이 최소 예치 금액보다 작게 되지 않도록 주의하세요. 그렇지 않으면 볼트 시작이 방지될 수 있습니다.

# [H-06] 가변 참가자가 수수료 수익을 잃을 수 있음

## 심각도

**영향:** 높음, 영향을 받은 가변 참가자는 수수료 수익을 잃음

**가능성:** 중간, 최종 총 스테이킹 수익이 진행 중일 때보다 낮을 때 발생

## 설명

`calculateVariableWithdrawState()`는 `ethAmountOwed`를 계산하는 데 사용되며, 이는 인출되지 않은 수익의 잔액입니다. 이는 `totalEarnings`의 참가자 지분에서 `previousWithdrawnAmount`를 뺀 값을 사용하여 계산됩니다(아래 코드 참조).

```Solidity
  function calculateVariableWithdrawState(
    uint256 totalEarnings,
    uint256 previousWithdrawnAmount
  ) internal view returns (uint256, uint256) {
    uint256 bearerBalance = variableBearerToken.balanceOf(msg.sender);
    require(bearerBalance > 0, "NBT");

    //@audit this can revert if participant share of earnings is less than previousWithdrawnAmount
    uint256 ethAmountOwed = bearerBalance.mulDiv(totalEarnings, variableBearerToken.totalSupply()) -
      previousWithdrawnAmount;

    return (ethAmountOwed + previousWithdrawnAmount, ethAmountOwed);
  }
```

문제는 계산이 `bearerBalance.mulDiv(totalEarnings, variableBearerToken.totalSupply())`가 항상 `previousWithdrawnAmount`보다 클 것이라고 가정한다는 것입니다.

그러나 그 가정을 깨는 엣지 케이스가 있습니다.

1. 음의 리베이스(negative rebase)가 발생하여 볼트 종료 인출 시 `totalEarnings`가 이전 인출보다 훨씬 낮아지는 경우.
2. 관리자가 음의 수익으로 부채를 청산하여 이전보다 낮은 `totalEarnings`로 볼트를 종료하는 경우.

이러한 상황이 발생하면 `vaultEndedWithdraw()`에서 `calculateVariableWithdrawState()`가 되돌려져 영향을 받는 가변 참가자가 `feeEarnings`의 지분을 인출하지 못하게 됩니다(아래 코드 참조).

```Solidity
  function vaultEndedWithdraw(uint256 side) internal {
    ...
      if (totalEarnings > 0) {
        //@audit this could reverts due to edge cases where participant share of totalEarnings < previous withdrawn earnings
        //       that will prevent the affected participants from withdrawing fee earnings, which is executed below
        (uint256 currentState, uint256 stakingEarningsShare) = calculateVariableWithdrawState(
          totalEarnings,
          variableToWithdrawnStakingEarnings[msg.sender]
        );
        stakingShareAmount = stakingEarningsShare;
        variableToWithdrawnStakingEarnings[msg.sender] = currentState;
      }

      uint256 feeShareAmount = 0;
      uint256 totalFees = withdrawnFeeEarnings + feeEarnings;

      if (totalFees > 0) {
        (uint256 currentState, uint256 feesShare) = calculateVariableWithdrawState(
          totalFees,
          variableToWithdrawnFees[msg.sender]
        );
        feeShareAmount = feesShare;
        variableToWithdrawnFees[msg.sender] = currentState;
      }

       vaultEndedStakingEarnings -= stakingShareAmount;
      feeEarnings -= feeShareAmount;

      variableBearerToken.burn(msg.sender, bearerBalance);

      uint256 sendAmount = stakingShareAmount + feeShareAmount;
      sendFunds(sendAmount);

      emit VariableFundsWithdrawn(sendAmount, msg.sender, isStarted(), isEnded());
      return;
    }
  }
```

## 권고 사항

`previousWithdrawnAmount >= bearerBalance.mulDiv(totalEarnings, variableBearerToken.totalSupply())`일 때 `ethAmountOwed`를 0으로 설정하세요.

# [H-07] 관리자가 부채 청산으로 손실을 입을 수 있음

## 심각도

**영향:** 높음, 관리자는 부채 청산에 필요한 것보다 더 많이 지불하게 되어 손실을 입음

**가능성:** 중간, 관리자가 부채를 청산할 때 발생

## 설명

LidoVault의 관리자는 Lido와의 예상치 못한 문제로 인해 부채 청산을 수행하여 볼트를 일찍 종료할 수 있습니다. 이 프로세스는 관리자가 ETH로 지불한 `adminSettleDebtAmount`와 함께 `initiatingAdminSettleDebt()`를 사용하여 초기화됩니다. 타임락 후 `adminSettleDebt()`를 호출하여 부채가 청산되며, 이는 `adminSettleDebtAmount`로 지불된 대가로 stETH 잔액을 관리자에게 전송합니다.

기본적으로 위의 교환을 통해 지불된 올바른 `adminSettleDebtAmount`는 `LidoAdapter`의 인출되지 않은 고정 예치금 및 스테이킹/수수료 수익과 동일해야 합니다.

그러나 문제는 `adminSettleDebtAmount` 지불 후에도 참가자가 고정 예치금 및 스테이킹/수수료 수익을 인출하여 `LidoAdapter`의 stETH 잔액을 줄일 수 있다는 것입니다. 그런 일이 발생하면 관리자는 실제로 `initiatingAdminSettleDebt()` 동안 예상했던 것보다 적은 양의 stETH를 받게 됩니다.

게다가 잘못된 `adminSettleDebtAmount`를 기반으로 하므로 `vaultEndedStakingEarnings` 및 `vaultEndedFixedDepositsFunds`의 계산도 올바르지 않게 됩니다.

```Solidity
  function adminSettleDebt(address adminLidoAdapterAddress) external onlyAdmin {
    require(block.timestamp > adminSettleDebtLockPeriodEnd, "ANM");

    vaultEndedWithdrawalsFinalized = true;
    adminSettledDebt = true;

    //@audit adminSettleDebtAmount is set during initiatingAdminSettleDebt(),
    //        which could be higher than required when withdrawals are made during the timelock period
    vaultEndedStakingEarnings = fixedETHDepositToken.totalSupply() < adminSettleDebtAmount
      ? adminSettleDebtAmount - fixedETHDepositToken.totalSupply()
      : 0;
    vaultEndedFixedDepositsFunds = adminSettleDebtAmount - vaultEndedStakingEarnings;

    //@audit the stETH transferred to admin will be lesser than adminSettleDebtAmount due to withdrawals
    if (vaultEndedWithdrawalRequestIds.length > 0) {
      IAdminLidoAdapter adminLidoAdapter = IAdminLidoAdapter(adminLidoAdapterAddress);
      adminLidoAdapter.setLidoWithdrawalRequestIds(vaultEndedWithdrawalRequestIds);
      for (uint i = 0; i < vaultEndedWithdrawalRequestIds.length; i++) {
        lidoAdapter.transferWithdrawalERC721(adminLidoAdapterAddress, vaultEndedWithdrawalRequestIds[i]);
      }
    } else {
      lidoAdapter.transferStETH(adminLidoAdapterAddress, lidoAdapter.stakingBalance());
    }

    emit AdminSettledDebt(msg.sender);
  }
```

## 권고 사항

`adminSettleDebt()`에서 LidoAdapter의 sETH 잔액에 해당하는 ETH 금액을 볼트에 남겨두고 `adminSettleDebtAmount`의 잔액 금액을 `AdminLidoAdapter`로 환불하세요.

# [H-08] 프로토콜이 수수료의 일부를 놓칠 것임

## 심각도

**영향:** 중간, 프로토콜 수수료의 일부가 누락됨

**가능성:** 높음, 진행 중인 볼트에서 각 사용자가 한 번 이상 수익을 청구할 때마다 더 적은 수수료를 지불하게 됨

## 설명

볼트는 가변 스테이커가 `withdraw` 함수를 사용하여 진행 중 단계에서 수익을 청구할 수 있도록 합니다.

L571의 청구 금액은 `calculateVariableWithdrawState` 도우미 함수를 사용하여 Lido에 누적된 현재 스테이킹 수익과 호출자에 대해 이미 인출된 금액을 기준으로 계산됩니다:

```solidity
File: LidoVault.sol
907:   function calculateVariableWithdrawState(
908:     uint256 totalEarnings,
909:     uint256 previousWithdrawnAmount
910:   ) internal view returns (uint256, uint256) {
911:     uint256 bearerBalance = variableBearerToken.balanceOf(msg.sender);
912:     require(bearerBalance > 0, "NBT");
913:
914:     uint256 ethAmountOwed = bearerBalance.mulDiv(totalEarnings, variableBearerToken.totalSupply()) -
915:       previousWithdrawnAmount;
916:
917:     return (ethAmountOwed + previousWithdrawnAmount, ethAmountOwed);
918:   }
```

그러나 L582에서 프로토콜 수수료가 `variableToWithdrawnStakingEarnings`에 저장된 인출 금액에서 차감되는 것을 볼 수 있습니다. 첫 번째 수익 인출 후 다음 호출에서 이 값은 L915의 `calculateVariableWithdrawState` 내부에서 사용되어 `ethAmountOwed`가 원래보다 커지게 됩니다. `previousWithdrawnAmount`는 사용자의 인출 금액에만 해당하지만, 이전 인출 호출의 프로토콜 수수료는 사용자의 수익으로 간주되기 때문에 이전 수익 인출의 프로토콜 수수료 금액이 포함됩니다.

이로 인해 진행 중인 볼트에서 스테이킹 수익을 한 번 이상 인출하는 사용자는 더 큰 금액을 받게 됩니다.

## 권고 사항

L573에서 `variableToWithdrawnStakingEarnings[msg.sender].mulDiv(10000, 10000 - protocolFeeBps)`를 사용하세요. 이렇게 하면 프로토콜 수수료가 `previousWithdrawnAmount` 매개변수 값에 포함되므로 `calculateVariableWithdrawState` 함수가 사용자의 올바른 수익을 계산합니다. 또한 L582는 사용자의 인출된 수익의 올바른 금액을 저장하도록 업데이트되어야 합니다:

```solidity
     variableToWithdrawnStakingEarnings[msg.sender] = currentState - currentState.mulDiv(protocolFeeBps, 10000) ;
```

# [H-09] 사용자가 `adminSettleDebt()` 실행 전에 인출을 요청하고 그 후에 완료되면 자금이 `lidoAdapter`에 갇힐 수 있음

## 심각도

**영향:** 높음, 사용자가 예치금을 잃을 수 있음

**가능성:** 중간, 시나리오는 관리자 부채 청산 실행 및 특정 트랜잭션 순서가 필요함

## 설명

관리자가 `initiatingAdminSettleDebt`를 호출하면 사용자는 예치금을 인출할 수 있는 타임락 기간을 갖습니다. 그들은 이를 위해 `withdraw` 함수를 사용할 것입니다. 볼트가 어떤 단계에 있는지에 따라, 컨트랙트는 적절한 사용자의 토큰 잔액을 소각하고, `lidoAdapter`를 사용하여 Lido에서 인출을 요청하며, 관련 배열 슬롯에 요청 ID를 저장합니다.

관리자가 `adminSettleDebt`를 호출한 후 모든 `stEth` 잔액 또는 `vaultEndedWithdrawalRequestIds`는 `lidoAdapter`에서 `adminLidoAdapterAddress`로 전송됩니다:

```solidity
File: LidoVault.sol
712:   function adminSettleDebt(address adminLidoAdapterAddress) external onlyAdmin {
...
723:     if (vaultEndedWithdrawalRequestIds.length > 0) {
724:       IAdminLidoAdapter adminLidoAdapter = IAdminLidoAdapter(adminLidoAdapterAddress);
725:       adminLidoAdapter.setLidoWithdrawalRequestIds(vaultEndedWithdrawalRequestIds);
726:       for (uint i = 0; i < vaultEndedWithdrawalRequestIds.length; i++) {
727:         lidoAdapter.transferWithdrawalERC721(adminLidoAdapterAddress, vaultEndedWithdrawalRequestIds[i]);
728:       }
729:     } else {
730:       lidoAdapter.transferStETH(adminLidoAdapterAddress, lidoAdapter.stakingBalance());
731:     }
...
```

동시에 `adminSettledDebt`가 이제 `true`와 같으므로, `sendFunds` 함수에 대한 모든 호출은 이전과 같이 `lidoAdapter`가 아닌 볼트 잔액을 사용하여 실행됩니다:

```solidity
File: LidoVault.sol
845:   function sendFunds(uint256 ethSendAmount) internal {
846:     if (adminSettledDebt) {
847:       (bool sent, ) = msg.sender.call{value: ethSendAmount}("");
848:       require(sent, "ETF");
849:     } else {
850:       lidoAdapter.transferWithdrawnFunds(msg.sender, ethSendAmount);
851:     }
852:   }
```

이것은 `adminSettleDebt()` 실행 후에 완료되는 인출 요청은 `lidoAdapter`로 청구되어 거기에 머무르는 반면, 볼트는 잔액으로 사용자의 인출을 갚으려고 한다는 것을 의미합니다.

다음 시나리오를 고려하십시오:

1. 10e18 고정 측 용량과 0.5e18 가변 측 용량이 있는 볼트가 있습니다. 앨리스는 고정 측에 4 eth를 예치하고, 밥은 6 eth를 예치하고, 찰리는 가변 측에 0.5 eth를 제공하며, 볼트가 시작됩니다.
2. 앨리스와 밥은 프리미엄을 청구합니다.
3. 관리자는 10.5 eth와 같은 `msg.value`로 `initiatingAdminSettleDebt`를 호출하기로 결정합니다.
4. 밥은 `withdraw` 함수로 인출을 요청했습니다. `adminSettleDebt`가 아직 `true`가 아니므로, 그의 요청은 `lidoAdapter.requestWithdrawViaETH()`를 사용하여 실행되고 요청 id는 `fixedToVaultOngoingWithdrawalRequestIds` 배열에 저장됩니다.
5. 관리자는 `adminSettleDebt` 함수를 호출합니다. 밥이 이전 단계에서 `fixedETHDepositToken` 잔액(6 eth)을 소각했으므로 이제 `vaultEndedStakingEarnings`는 `10.5e18 - 4e18 = 6.5e18`과 같고 유일한 가변 측 참가자인 찰리에 의해 완전히 청구됩니다. 동시에 `vaultEndedFixedDepositsFunds`는 4e18과 같으며, 이는 앨리스의 `fixedETHDepositToken` 잔액 및 해당 토큰의 총 공급량과 동일합니다.
6. 앨리스와 찰리는 자금을 인출하여 볼트 잔액을 0으로 남깁니다.
7. 밥은 인출을 완료하려고 시도하지만, `OutOfFund` 오류로 인해 실패합니다. 인출 요청은 먼저 `lideAdapter` 잔액을 성공적으로 증가시키지만, `sendFunds` 함수는 이제 어댑터 대신 볼트 잔액에서 자금을 보내므로 함수가 되돌려집니다.

이 시나리오에서 관리자는 찰리에게 초과 지불하여 [H-07]에 설명된 것과 같은 방식으로 자금의 일부를 잃지만, 동시에 밥의 잔액은 이제 완전히 갇히게 됩니다.

## 권고 사항

`LidoAdapter#_claimWithdrawals` 함수는 인출된 모든 eth를 `msg.sender`(이 경우 볼트)로 보내야 합니다. 이렇게 하면 이전에 요청된 모든 인출이 볼트 잔액으로 끝나도록 보장됩니다.

# [M-01] Deposit 함수가 DoS될 수 있음

## 심각도

**영향:** 중간, 핵심 컨트랙트 기능이 DoS되지만, 프라이빗 릴레이어를 사용하여 공격을 우회하는 것은 여전히 가능함

**가능성:** 중간, 공격 벡터는 저렴하지만 동시에 악의적인 행위자는 사용자를 괴롭히는 것 외에는 인센티브가 없음

## 설명

deposit 함수는 고정 및 가변 예치금의 합계가 각각 `fixedSideCapacity` 및 `variableSideCapacity`와 엄격하게 동일해야 합니다. 이는 예치 토큰의 공급과 현재 `msg.value`를 확인하여 수행됩니다(L395 및 L409). 그러나 `msg.value`가 예치 토큰의 총 공급과 측면 용량 간의 차이보다 크면, 전체 deposit tx는 되돌려집니다.

이는 악의적인 행위자가 deposit 함수를 DoS할 수 있는 괴롭힘(griefing) 공격 벡터를 엽니다. 볼트는 비허가형이므로 공격자는 볼트를 시작할 사용자의 tx를 발견하고 먼지 금액 예치로 프론트러닝하여 첫 번째 tx를 되돌릴 수 있습니다. 이는 여러 번 반복될 수 있어 효과적으로 볼트 시작을 방지합니다.

```solidity
File: LidoVault.sol
383:   function deposit(uint256 side) external payable isInitialized nonReentrant {
384:     require(!isStarted(), "DAS");
385:     // don't allow deposits once settle debt process has been initialized to prevent vault from starting
386:     require(!isAdminSettleDebtInitialized(), "AAI");
387:     require(side == FIXED || side == VARIABLE, "IS");
388:     require(msg.value > 0, "NZV");
389:
390:     uint256 amount = msg.value;
391:     if (side == FIXED) {
392:       // Fixed side deposits
393:
394:       // no refunds allowed
395:       require(amount <= fixedSideCapacity - fixedETHDepositToken.totalSupply(), "OED");
...
```

## 권고 사항

Deposit 함수는 `msg.value`가 현재 볼트 용량보다 큰 경우 사용자에게 환불해야 합니다.

# [M-02] `initiatingAdminSettleDebt()`와의 경쟁 조건으로 참가자가 과소 보상을 받을 수 있음

## 심각도

**영향:** 중간, 참가자는 관리자가 부채 청산에 대해 과소 지불함에 따라 인출 시 손실을 입을 것입니다. 그러나 관리자(신뢰됨)는 그 차액을 별도로 환불하여 손실을 보상할 수 있습니다.

**가능성:** 중간, 관리자가 부채를 청산할 때 발생

## 설명

`LidoVault.deposit()` 내에 `require(!isAdminSettleDebtInitialized(), "AAI")` 확인이 있어 관리자 부채 청산 프로세스가 초기화되었을 때 고정/가변 참가자가 마지막 예치로 볼트를 시작하는 것을 방지합니다.

그러나 이 확인은 예상치 못한 볼트 시작 `deposit()`이 `initiatingAdminSettleDebt()` 전에 발생하는 경쟁 조건으로 인해 우회될 수 있으며, 이로 인해 볼트가 강제로 시작됩니다.

경쟁 조건은 다음과 같이 발생할 수 있습니다:

1. 가변 참가자가 LidoVault에 예치하여 프리미엄을 채웠습니다.
2. 관리자는 `adminSettleDebtAmount [msg.value] = fixedETHDepositToken.totalSupply()`로 `initiatingAdminSettleDebt()`를 호출하여 볼트를 취소하고 수익 제로로 부채를 청산하기로 결정합니다. 볼트가 아직 시작되지 않았으므로 `adminSettleDebtAmount`는 `fixedSideCapacity`보다 작다는 점에 유의하십시오.
3. 고정 참가자가 우연히 마지막 `deposit()` 호출을 수행하며, 이는 MEV/재정렬로 인해 `initiatingAdminSettleDebt()`보다 먼저 실행됩니다.
4. 볼트가 의도치 않게 시작되어 `fixedETHDepositToken.totalSupply() == fixedSideCapacity`가 됩니다. 그러나 3단계로 인해 `adminSettleDebtAmount < fixedSideCapacity`이므로, 볼트의 ETH가 LidoAdapter stETH 잔액과 동일하지 않아 관리자는 참가자에게 과소 보상을 하게 됩니다.
5. 타임락 후 수익 제로로 볼트가 종료되면, 인출할 획득 스테이킹 수익이 없으므로 가변 참가자는 손실을 입습니다.
6. 또한 `vaultEndedFixedDepositsFund`가 예상보다 훨씬 낮으면, 고정 참가자는 프리미엄을 청구한 후에도 초기 예치금의 일부만 청구할 수 있는 가능성도 있습니다.

## 권고 사항

`initiatingAdminSettleDebt()`에 `expectedVaultStarted` (bool)를 매개변수로 추가하고 `require(expectedVaultStarted == isStarted());` 확인을 통해 볼트 시작 상태가 관리자가 예상한 것인지 확인하십시오.

확인이 실패하면 `initiatingAdminSettleDebt()`가 되돌려져야 하며, 그러면 관리자는 참가자를 보상하는 올바른 `msg.value`로 `initiatingAdminSettleDebt()`를 반복할 수 있습니다.

# [M-03] `adminSettleDebtLockPeriod`가 충분히 길지 않을 수 있음

## 심각도

**영향:** 높음, 사용자가 `stETH`가 `AdminLidoAdapter`로 전송되기 전에 제때 인출하지 못할 수 있으므로

**가능성:** 낮음, Lido 인출 대기열 지연 및 관리자가 부채 청산을 트리거하는 사건이 있어야 하므로

## 설명

`admin`은 예기치 않은 상황이 발생할 경우 볼트를 일찍 종료할 수 있는 능력이 있습니다. 이는 먼저 `initiatingAdminSettleDebt`를 호출하여 수행되며, 이는 `adminSettleDebt`가 호출되기 전 타임락을 트리거합니다. 이는 사용자가 모든 `stETH` 및 보류 중인 인출 요청이 `AdminLidoAdapter`로 전송되기 전에 인출을 선택할 수 있도록 하기 위함입니다.

타임락 `adminSettleDebtLockPeriod`는 `VaultFactory`에서 `3일`로 설정됩니다.

그러나 이것만으로는 충분하지 않을 수 있습니다. Lido에 따르면 인출 요청은 1-5일이 소요될 수 있습니다:
https://blog.lido.fi/ethereum-withdrawals-overview-faq/#:~:text=How%20does%20the,1%2D5%20days.

> 인출 프로세스는 어떻게 작동합니까?
> 인출 프로세스는 간단하며 두 단계가 있습니다:
>
> 요청: 인출 요청을 발행하여 stETH/wstETH를 잠급니다. 요청을 이행하기 위해 ETH가 소싱되고, 잠긴 stETH가 소각되어 인출 요청이 청구 가능으로 표시됩니다. **정상적인 상황에서는 1-5일이 소요될 수 있습니다.**

## 권고 사항

스테이커에게 반응할 하루를 주기 위해 타임락을 5일 또는 6일로 늘리는 것을 고려하십시오. 또한 [H-09]를 해결하십시오.

# [M-04] `adminSettleDebt()`가 여러 번 호출되어 자금 손실로 이어질 수 있음

## 심각도

**영향:** 높음, 일부 사용자가 자금을 잃을 수 있고 다른 사용자가 이 자금을 청구할 수 있으므로

**가능성:** 낮음, `adminSettleDebt()`의 다중 호출은 실수로 또는 악의적인 관리자에 의해서만 수행될 수 있으므로

## 설명

`adminSettleDebt`는 이미 호출되었는지 확인하는 절차가 부족합니다:

```solidity
File: LidoVault.sol
712:   function adminSettleDebt(address adminLidoAdapterAddress) external onlyAdmin {
713:     require(block.timestamp > adminSettleDebtLockPeriodEnd, "ANM");
714:
715:     vaultEndedWithdrawalsFinalized = true;
716:     adminSettledDebt = true;
717:
718:     vaultEndedStakingEarnings = fixedETHDepositToken.totalSupply() < adminSettleDebtAmount
719:       ? adminSettleDebtAmount - fixedETHDepositToken.totalSupply()
720:       : 0;
721:     vaultEndedFixedDepositsFunds = adminSettleDebtAmount - vaultEndedStakingEarnings;
...
```

이로 인해 다음과 같은 시나리오가 발생할 수 있습니다:

1. 관리자가 `adminSettleDebt()`를 호출하고 `vaultEndedStakingEarnings` 및 `vaultEndedFixedDepositsFunds` 변수에 대한 올바른 값을 설정합니다.
2. 사용자가 포지션을 인출하고 `fixedETHDepositToken.totalSupply()`를 변경합니다.
3. 관리자가 `adminSettleDebt()`를 다시 호출하면 1단계의 변수가 완전히 잘못된 값으로 끝나 아직 포지션을 인출하지 않은 사용자의 자금 손실로 이어집니다.

## 권고 사항

`adminSettleDebt()` 함수의 중복 호출을 방지하는 확인을 추가하세요.

# [M-05] 관리자가 이전 `initiatingAdminSettleDebt` 호출 없이 언제든지 `adminSettleDebt`를 호출할 수 있음

## 심각도

**영향:** 높음, 타임락과 관련된 핵심 기능이 우회됨

**가능성:** 낮음, 관리자에 의해 실수로 또는 악의적인 행동의 경우에만 수행될 수 있으므로

## 설명

`adminSettleDebt` 함수는 관리자가 타임락 종료 후 부채를 청산할 수 있도록 설계되었습니다. 마지막 함수는 `initiatingAdminSettleDebt` 중에 초기화되어야 합니다. `adminSettleDebt`는 타임락이 종료되었는지 성공적으로 확인하지만 타임락이 시작되었는지는 확인하지 못합니다. 따라서 L713의 확인은 현재 타임스탬프를 `uint256` 유형의 기본값인 `0`과 비교하게 되며, 이는 타임락이 아직 시작되지 않은 경우 관리자가 언제든지 `adminSettleDebt`를 실행할 수 있음을 의미합니다. 또한 `vaultEndedStakingEarnings` 및 `vaultEndedFixedDepositsFunds`는 0 값으로 설정되어 사용자의 인출 흐름이 중단됩니다.

```solidity
File: LidoVault.sol
712:   function adminSettleDebt(address adminLidoAdapterAddress) external onlyAdmin {
713:     require(block.timestamp > adminSettleDebtLockPeriodEnd, "ANM");
714:
715:     vaultEndedWithdrawalsFinalized = true;
716:     adminSettledDebt = true;
717:
718:     vaultEndedStakingEarnings = fixedETHDepositToken.totalSupply() < adminSettleDebtAmount
719:       ? adminSettleDebtAmount - fixedETHDepositToken.totalSupply()
720:       : 0;
721:     vaultEndedFixedDepositsFunds = adminSettleDebtAmount - vaultEndedStakingEarnings;
...
```

## 권고 사항

L713의 require 문에 `adminSettleDebtLockPeriodEnd > 0` 조건을 추가하세요.

# [M-06] 볼트 시작 전의 수익 발생이 허니팟(honeypot)으로 사용될 수 있음

## 심각도

**영향:** : 낮음, 피해자가 더 잘 알았어야 한다고 주장할 수 있으므로

**가능성:** : 높음, 공격자가 낮은 위험으로 수행하기 매우 쉽기 때문에

## 설명

고정 측 스테이커가 예치하자마자 자금은 lido로 전송되어 보상을 받기 시작합니다:

```solidity
397:      // Stake on Lido
398:      uint256 shares = lidoAdapter.stakeFunds{value: amount}(msg.sender);
```

고정 측의 누구든지 볼트가 시작되기 전에 언스테이크를 선택하고 자신의 지분 *그리고* 발생한 보상을 받을 수 있습니다:

```solidity
489:          fixedToVaultNotStartedWithdrawalRequestIds[msg.sender] = lidoAdapter.requestWithdrawViaShares(
490:            msg.sender,
491:            claimBalance
492:          );
```

```solidity
627:    // give fixed depositor all of their principal + any staking earnings
628:    uint256 sendAmount = lidoAdapter.claimWithdrawals(msg.sender, requestIds);
629:
630:    sendFunds(sendAmount);
```

따라서 시작되기 전에 볼트에 참여하는 것은 lido에 직접 스테이킹하는 것과 같습니다.

따라서 공격자는 높은 프리미엄으로 볼트를 생성하고 서서히 수익이 발생하는 전체 고정 프리미엄 측을 예치할 수 있습니다.

어느 순간 피해자는 큰 수익이 이미 쌓인 볼트가 있다는 것을 알게 되고 참여하기로 선택합니다. 프리미엄이 높더라도 이미 높은 프리미엄을 덮는 수익이 있으므로 가변 측에 참여합니다.

공격자는 가변 예치를 프론트러닝하고 예치금과 스테이킹 수익을 인출합니다. 그런 다음 동일한 tx에서 새로운 예치금으로 볼트에 다시 참여하여 처음부터 다시 시작할 수 있습니다.

따라서 그들은 고정 측 공격자에게 거의 또는 전혀 위험 없이 불공정한 프리미엄으로 볼트에 가입하도록 피해자를 유인했습니다.

## 권고 사항

이에 대한 적절한 온체인 완화책은 없으므로 볼트 시작 전의 수익은 보장되지 않음을 문서에 명확히 하는 것이 제안됩니다.

# [M-07] 가변 측의 진행 중 인출이 가변 수익에 해를 끼침

## 심각도

**영향:** 중간, 이것이 가변 측의 위험의 일부라고 주장할 수 있으므로

**가능성:** 중간, 인출하는 사람에게도 해를 끼치므로

## 설명

볼트가 활성 상태일 때, 가변 측 스테이커는 지금까지 발생한 수익의 지분을 인출하기로 선택할 수 있습니다:

```solidity
571:          (uint256 currentState, uint256 ethAmountOwed) = calculateVariableWithdrawState(
572:            (lidoStETHBalance - fixedETHDeposits) + withdrawnStakingEarnings + totalProtocolFee,
573:            variableToWithdrawnStakingEarnings[msg.sender]
574:          );
575:
576:          if (ethAmountOwed >= lidoAdapter.minStETHWithdrawalAmount()) {
...
584:            variableToVaultOngoingWithdrawalRequestIds[msg.sender] = lidoAdapter.requestWithdrawViaETH(
585:              msg.sender,
586:              ethAmountOwed
587:            );
```

L584에서 이것은 lido에서 언스테이크하므로, 앞으로 다른 가변 스테이커(및 자신)가 얻는 보상이 줄어들게 됩니다. 이는 주로 초기 인출이 얼마나 많이 이루어질지 계산하기 어렵기 때문에 가변 측 스테이커가 예상 수익을 계산하기 어렵게 만듭니다.

## 권고 사항

이를 완화하는 몇 가지 방법이 있습니다. 고정 측의 조기 종료 수수료와 유사하게 가변 측에 조기 인출 수수료를 부과하거나, 진행 중인 가변 인출 허용 여부(true/false) 플래그를 추가하여 볼트 생성자가 선택하게 하는 방법이 있습니다.

# [L-01] 곱셈 전 불필요한 나눗셈

고정 청구자가 프리미엄을 결정할 때 가변 측의 지분으로 받아들입니다:

```solidity
438:    // Send a proportional share of the total variable side deposits (premium) to the fixed side depositor
439:    uint256 sendAmount = claimBal.mulDiv(1e18, fixedLidoSharesTotalSupply()).mulDiv(variableSideCapacity, 1e18);
```

이 계산은 곱셈 전에 나눗셈을 수행하여 정밀도에 약간의 손실을 초래합니다(청구자의 잔액이 작을수록 정밀도 손실이 커짐). 이를 피하기 위해 계산을 다음과 같이 단순화할 수 있습니다:

```solidity
claimBal.mulDiv(variableSideCapacity, fixedLidoSharesTotalSupply());
```

그러나 `fixedLidoSharesTotalSupply()` 문제에 대해서는 [C-02]를 참조하십시오.

# [L-02] 조기 종료로 인해 컨트랙트에 자금이 잠길 수 있음

## 설명

고정 스테이커가 스테이킹 기간 초기에 존재하면 `earlyExitFee`로 페널티를 받습니다.

`earlyExitFee`는 `calculateFixedEarlyExitFees`의 다음 계산에 의해 결정됩니다:

```solidity
        // @audit-info max value 1e18
945:    uint256 remainingProportion = (endTime > timestampRequested ? endTime - timestampRequested : 0).mulDiv(
946:      1e18,
947:      duration
948:    );
949:
950:    // Calculate the scaling fee based on the quadratic scaling factor and earlyExitFeeBps
        // @audit-info max value upfrontPremium * 0.1 (1_000/10_000)
951:    uint256 earlyExitFees = upfrontPremium.mulDiv(
952:      earlyExitFeeBps.mulDiv(remainingProportion.mulDiv(remainingProportion, 1e18), 1e18),
953:      10000
954:    );
955:
956:    // Calculate the amount to be paid back of their original upfront claimed premium, not influenced by quadratic scaling
        // @audit-info max value upfrontPremium * 0.1 + upfrontPremium = 1.1 * upfrontPremium
957:    earlyExitFees += upfrontPremium - upfrontPremium.mulDiv(timestampRequested - startTime, duration);
```

`earlyExitFeeBps`는 10%(`VaultFactory`에서 생성 시 설정됨)입니다.

그런 다음 수수료는 `claimFixedVaultOngoingWithdrawal`에서 적용됩니다:

```solidity
880:    uint256 amountWithdrawn = lidoAdapter.claimWithdrawals(msg.sender, requestIds);
881:
         // @audit-info max value 1.1 * upfrontPremium
882:    uint256 earlyExitFees = calculateFixedEarlyExitFees(upfrontPremium, request.timestamp);
...
889:    return amountWithdrawn - earlyExitFees;
```

문제는 마지막 뺄셈 `amountWithdrawn - earlyExitFees`입니다. 볼트의 가변 대 고정 용량 비율이 충분히 크면 스테이커가 충분히 일찍 인출할 경우 이 계산이 언더플로 될 수 있습니다. 따라서 이 인출은 불가능해집니다. `fixedToVaultOngoingWithdrawalRequestIds`의 항목은 제거되지 않으므로 이러한 자금은 컨트랙트에 잠기게 됩니다.

비율이 1-1 고정 대 가변인 볼트를 상상해 보십시오. 그러면 `upfrontPremium`은 `amountWithdrawn`과 같을 것입니다. 충분히 일찍 언스테이킹하면 `amountWithdrawn - earlyExitFees`가 언더플로 됩니다.

## 권고 사항

`min(earlyExitFee,amountWithdrawn)`을 `earlyExitFee`로 사용하는 것을 고려하십시오. 즉, `earlyExitFee`는 최대로 인출하는 금액이 될 수 있습니다.

# [L-03] `createVault()`에서 취소된 어댑터 확인 누락

`revokeAdapterType()`은 취소된 어댑터로 새 볼트가 배포될 수 없도록 `adapterTypeByteCode = ""`로 설정합니다. 그러나 `createVault()`에는 취소된 어댑터에 대한 확인이 없으며 취소된 어댑터로 볼트를 배포할 수 있습니다.

이를 수정하려면 `createVault()` L174에 `require(adapterTypeByteCode[_adapterInfo.adapterTypeId] != "");` 확인을 추가하세요.
