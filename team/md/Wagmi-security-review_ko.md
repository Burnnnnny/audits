# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**wagmi-leverage** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# Wagmi 소개

Wagmi Leverage는 가격 기반 청산이나 가격 오라클 없이 집중된 유동성을 기반으로 구축된 레버리지 제품입니다. 이 시스템은 유동성 공급자와 거래자(대출자)를 대상으로 합니다. 거래자는 이자를 지불하는 한 포지션을 보유할 수 있는 시간에 대해 비용을 지불합니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 비교적 가능성이 높습니다.

- 낮음 (Low) - 너무 많거나 비현실적인 가정을 필요로 하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준별 요구 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (이미 배포되지 않은 경우 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [15ef9740b196b146dae0a48d75811512788040a1](https://github.com/RealWagmi/wagmi-leverage/tree/15ef9740b196b146dae0a48d75811512788040a1)

_수정 검토 커밋 해시_ - [48cbe0b73ef952ed7c6a12817cffc61c0535f3b5](https://github.com/RealWagmi/wagmi-leverage/tree/48cbe0b73ef952ed7c6a12817cffc61c0535f3b5)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `LightQuoterV3`
- `LiquidityBorrowingManager`
- `Vault`
- `FlashLoanAggregator`
- `TransferHelper`
- `Constants`
- `ErrLib`
- `ExternalCall`
- `AmountsLiquidity`
- `Keys`
- `ApproveSwapAndPay`
- `LiquidityManager`
- `OwnerSettings`
- `DailyRateAndCollateral`

# 발견 사항

# [M-01] 프로토콜이 플랫폼 수수료를 두 번 청구함 (Protocol charges platform fees twice)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

LP는 수중 대출(underwater loan, 담보 가치가 대출금보다 낮아진 상태)에 대해 상환을 호출하여 포지션 복구 없이 토큰을 다시 회수할 수 있습니다. 이 대출과 관련된 다른 포지션이 없는 경우 호출자는 플랫폼 수수료와 청산 보너스를 제외한 담보를 받게 됩니다. 그러나 플랫폼 수수료가 두 번 청구되는 문제가 있습니다.

```solidity
        if (params.isEmergency) {
            (!underLiquidation).revertError(ErrLib.ErrorCode.FORBIDDEN);
            (
                uint256 removedAmt,
                uint256 feesAmt,
                bool completeRepayment
            ) = _calculateEmergencyLoanClosure(
                    zeroForSaleToken,
                    params.borrowingKey,
                    currentFees,
                    borrowing.borrowedAmount
                );
            (removedAmt == 0).revertError(ErrLib.ErrorCode.LIQUIDITY_IS_ZERO);
            // Subtract the removed amount and fees from borrowedAmount and feesOwed
            borrowing.borrowedAmount -= removedAmt;
            borrowing.dailyRateCollateralBalance -= feesAmt;
            // 첫 번째 수수료 청구
>>          feesAmt =
                _pickUpPlatformFees(borrowing.holdToken, feesAmt) /
                Constants.COLLATERAL_BALANCE_PRECISION;
            // Deduct the removed amount from totalBorrowed
            unchecked {
                holdTokenRateInfo.totalBorrowed -= removedAmt;
            }
            // If loansInfoLength is 0, remove the borrowing key from storage and get the liquidation bonus
            if (completeRepayment) {
                LoanInfo[] memory empty;
                _removeKeysAndClearStorage(borrowing.borrower, params.borrowingKey, empty);
                // 두 번째 수수료 청구
>>              feesAmt =
                  _pickUpPlatformFees(borrowing.holdToken, currentFees) /
                    Constants.COLLATERAL_BALANCE_PRECISION +
                    liquidationBonus;
            } else {
```

기록된 토큰 합계가 실제 금액보다 커지므로 프로토콜 회계가 깨질 것입니다.

다음은 `LiquidityBorrowingManager.t.sol`의 POC 코드입니다:

```solidity
    function testDoublePlatformFee() public {
        uint128 minLiqAmt = _minimumLiquidityAmt(253_320, 264_600);
        address[] memory tokens = new address[](1);
        tokens[0] = address(WETH);
        address vault = borrowingManager.VAULT_ADDRESS();

        vm.startPrank(bob);
        borrowingManager.borrow(createBorrowParams(tokenId, minLiqAmt), block.timestamp + 1);
        bytes32[] memory key = borrowingManager.getBorrowingKeysForTokenId(tokenId);
        vm.stopPrank();

        ILiquidityBorrowingManager.FlashLoanRoutes memory routes;
        ILiquidityBorrowingManager.SwapParams[] memory swapParams;

        ILiquidityBorrowingManager.RepayParams memory repay = ILiquidityBorrowingManager.RepayParams({
            isEmergency: true,
            routes: routes,
            externalSwap: swapParams,
            borrowingKey: key[0],
            minHoldTokenOut: 0,
            minSaleTokenOut: 0
        });

        // time to repay underwater loan
        vm.warp(block.timestamp + 86401);
        vm.prank(alice);
        (uint saleOut, uint holdToken) = borrowingManager.repay(repay, block.timestamp + 1);

        borrowingManager.collectProtocol(address(this), tokens);

        vm.expectRevert(bytes("W-ST"));
        vm.prank(alice);
        borrowingManager.collectLoansFees(tokens);
    }
```

이 시나리오에서 LP는 플랫폼 수수료가 징수된 후 보상을 징수할 수 없습니다.

## 권장 사항

```diff
            if (completeRepayment) {
                LoanInfo[] memory empty;
                _removeKeysAndClearStorage(borrowing.borrower, params.borrowingKey, empty);
+               feesAmt +=
-                   _pickUpPlatformFees(borrowing.holdToken, currentFees) /
-                   Constants.COLLATERAL_BALANCE_PRECISION +
                    liquidationBonus;
            } else {
```

# [L-01] 사용자가 대출 시 초과 지불할 수 있음 (Users might overpay while borrowing)

대출의 최종 비용은 `marginDeposit`, `liquidationBonus`, `dailyRateCollateral` 및 `holdTokenEntranceFee`의 합계로 결정됩니다.

```solidity
## LiquidityBorrowingManager.sol

uint256 amountToPay;
unchecked {
    // Updating borrowing details
    borrowing.borrowedAmount += cache.borrowedAmount;
    borrowing.liquidationBonus += liquidationBonus;
    // Transfer the required tokens to the VAULT_ADDRESS for collateral and holdTokenBalance
    borrowing.dailyRateCollateralBalance +=
        cache.dailyRateCollateral *
        Constants.COLLATERAL_BALANCE_PRECISION;
    amountToPay =
        marginDeposit +
        liquidationBonus +
        cache.dailyRateCollateral +
        cache.holdTokenEntranceFee;
}
```

사용자는 `maxDailyRate`를 전달하여 트랜잭션을 제출하는 시점과 트랜잭션이 실행되는 시점 사이에 `dailyRateCollateral`이 변경되었는지 확인할 수 있습니다.

하지만 `liquidationBonus`와 `entranceFeeBps`가 변경되었는지 확인할 수 있는 옵션은 없습니다. `entranceFee`와 `liquidationBonus`의 최댓값은 10%입니다.

대출자가 큰 대출을 받고 거버넌스가 이러한 매개변수를 변경하는 불운한 상황이 발생하면 대출자는 의도한 것보다 훨씬 더 많은 비용을 지불할 수 있습니다.

이 두 매개변수가 특정 값을 초과하는 경우 차입(borrow) 함수 호출자가 되돌릴 수 있도록(revert) 허용하는 것을 고려하십시오.

# [L-02] `_excuteCallback` 함수 내 확인 누락 (Missing check in the `_excuteCallback` function)

`FlashLoanAggregator.flashLoan` 함수는 `address(POOL) != address(0)` 확인을 포함합니다. 그러나 `flashLoanParams[0].protocol`이 `Protocol.AAVE`가 아닌 경우 확인을 건너뜁니다. 결과적으로 `FlashLoanAggregator._excuteCallback` 함수는 잘못된 오류를 발생시킬 수 있습니다. 이로 인해 오류 원인을 해결하는 데 어려움을 겪을 수 있습니다.
