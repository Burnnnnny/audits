# 소개

**Lumin** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Lumin** 소개

Lumin은 Arbitrum을 시작으로 여러 EVM 호환 체인에 배포될 고정 금리 P2P 대출 프로토콜입니다. 대출(lending), 차입(borrowing), 상환 및 청산 메커니즘이 마련되어 있습니다.

모든 사용자는 Lumin 플랫폼에 자금을 입금할 수 있습니다. 그들은 그것으로 대출을 제공하거나(고정 금리로) 대출자로서 대출을 받기 위해 담보로 사용할 수 있습니다.

대출자는 `LoanManager::createLoan`을 호출하여 대출 기관의 대출 구성, 차입 자산 금액, 선택한 가격 피드 및 선택한 가격 피드가 있는 담보 자산에 대한 데이터를 제공합니다.

[추가 문서](https://docs.lumin.finance/_/)

## 관찰 사항

코드베이스는 업그레이드 가능성(upgradeability) 및 일시 중지 가능성(pausability)을 사용하고 있으며, 이는 중앙 집중화 공격 벡터입니다. 특히 프로토콜이 사용자 허용량을 처리하므로 업그레이드 가능성과 결합하여 사용자 허용량을 악용하는 데 사용될 수 있어 위험합니다.

담보 자산은 `AssetManager`를 통해 Asset 관리자가 화이트리스트에 추가하는 과정을 거칩니다. 통합된 각 토큰은 코너 케이스 시나리오에 대해 신중하게 테스트해야 합니다. 예를 들어 Aave 토큰은 일시 중지할 수 있으므로 청산에 문제가 될 수 있습니다.

현재 상태의 프로토콜은 불완전합니다. 예를 들어 청산 수수료는 `LoanManager` 계약에서 청구할 수 없습니다.

플랫폼은 ERC20 자산의 예치/담보 사용만 허용합니다.

대출은 "부실(underwater)" 상태일 때뿐만 아니라 이자/원금 지불 기한이 지났거나 대출 기간이 완료된 경우에도 청산될 수 있습니다.

플랫폼에서 대출 및 구성 ID는 0이 아닌 1부터 시작합니다.

## 특권 역할 및 행위자

- Lumin 관리자(admin) 역할 - `AssetManager` 및 `LoanManagerDelegator` 계약을 업그레이드, 일시 중지 및 일시 중지 해제할 수 있습니다.
- 자산 관리자(Asset Admin) 역할 - 허용된 자산과 해당 가격 피드를 관리할 수 있습니다.
- 대출 관리자(Loan Manager) 역할 - 잠금, 잠금 해제, 대출, 압류 등 사용자 자산으로 모든 유형의 작업을 수행할 수 있습니다. `LoanManagerDelegator` 계약에만 부여되어야 합니다. 또한 대출 지분(loan shares)을 민팅/소각할 수 있습니다.
- 모든 사용자 - `AssetManager`에서 잠겨있지 않은 자산을 예치(유동성 제공)하고 인출할 수 있습니다.
- 대출 기관(Lender) - 대출을 제공하기 위한 구성을 생성한 다음 삭제하거나 활성화/비활성화합니다.
- 대출자(Borrower) - 새로운 대출을 생성(받기)한 다음 이자/원금을 상환합니다(누구나 대출자에 대해 상환할 수 있음).
- 청산인(Liquidator) - 대출을 청산하고 보상을 받을 수 있습니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

**영향 (Impact)** - 성공적인 공격으로 인한 기술적, 경제적, 평판적 손해

**가능성 (Likelihood)** - 특정 취약점이 발견되고 악용될 확률

**심각도 (Severity)** - 위험의 전반적인 중요성

# 보안 평가 요약

**_검토 커밋 해시_ - [a4c32f0ad8dd8e424587b1fa10003603e2088a44](https://github.com/bull-haus/lumin-contracts/tree/a4c32f0ad8dd8e424587b1fa10003603e2088a44)**

**_수정 검토 커밋 해시_ - [3c10ad12db9906bdea2dbf9ebad035f81b190b9e](https://github.com/bull-haus/lumin-contracts/tree/3c10ad12db9906bdea2dbf9ebad035f81b190b9e)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `interfaces/**`
- `access/LuminAccessManager`
- `access/Roles`
- `asset/AssetManager`
- `loan/LoanConfigManager`
- `loan/LoanShares`
- `loan/LoanManager`
- `loan/LoanManagerBase`
- `loan/LoanManagerDelegator`
- `pricefeed/PriceFeedProxyChainlink`

---

# 발견 사항 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [C-01] | 청산 후 담보 이중 지출(double-spend) 가능 | 치명적 | 수정됨 |
| [H-01] | 비활성화된 대출 기관의 대출 구성을 대출자가 사용할 수 있음 | 높음 | 수정됨 |
| [M-01] | 자산 대출 구성이 EnumerableSet에서 제거되지 않을 수 있음 | 중간 | 수정됨 |
| [M-02] | 프로토콜 일시 중지 해제 후 즉시 청산 발생 가능 | 중간 | 수정됨 |
| [M-03] | 가격 피드 오라클 데이터 검증 누락 | 중간 | 부분 수정됨 |
| [M-04] | Lumin 관리자가 프로토콜에 대한 사용자 허용량을 악용할 수 있음 | 중간 | 인지됨 |
| [M-05] | 전송 수수료(fee-on-transfer) 및 리베이스(rebasing) 토큰 미지원 | 중간 | 인지됨 |
| [L-01] | `priceFeedProxy`가 설정되었는지 확인하는 절차 누락 | 낮음 | 수정됨 |
| [L-02] | 자산 고유성은 심볼에 기반해서는 안 됨 | 낮음 | 인지됨 |
| [L-03] | 동일한 ID를 가진 `LoanShares`의 최대 수를 명시적으로 제한 | 낮음 | 수정됨 |
| [L-04] | 코드 경로에 자산 존재 확인 누락 | 낮음 | 수정됨 |
| [L-05] | 기본 구성으로 프로토콜을 배포할 수 없음 | 낮음 | 인지됨 |
| [L-06] | `address(0)`을 유효한 값으로 사용 | 낮음 | 수정됨 |
| [L-07] | 너무 많은 이자를 지불한 사용자는 환불받지 못함 | 낮음 | 인지됨 |
| [L-08] | 코드가 Checks-Effects-Interactions 패턴을 따르지 않음 | 낮음 | 수정됨 |
| [L-09] | 사용자가 자신에게 대출을 받을 수 있음 | 낮음 | 수정됨 |

# 상세 발견 사항

# [C-01] 청산 후 담보 이중 지출(double-spend) 가능

## 심각도

**영향:**
높음, 대출자가 자신의 담보를 이중 지출할 수 있기 때문

**가능성:**
높음, 청산 시 담보 금액을 차감하지 않기 때문

## 설명

`AssetManager::assetTransferOnLoanAction`의 `if (action == AssetActionType.Seize)` 문에서 전송된 금액은 `userDepositFrom.lockedAmount`에서만 차감되지만 `userDepositFrom.depositAmount`에서도 차감되어야 했습니다. 즉, 대출 지분 보유자가 담보의 대부분을 받더라도 대출자의 담보 잔액은 청산 시 감소하지 않습니다.

다음은 취약점을 보여주는 실행 가능한 개념 증명(PoC) 단위 테스트입니다(`LoanManager.Repay` 테스트 파일 끝에 추가 가능):

```solidity
function test_LiquidateExploit() public {
    // because of the `setUp` method, at this point Bob has taken a loan from Alice through Lumin
    uint256 bobDeposit = wrappedAssetManager.depositOf(assetId[1], bob).depositAmount;

    // make loan expire
    vm.warp(block.timestamp + 300 days + 1);
    // random user liquidates Bob's loan, so collateral (asset[1]) should be transferred to the lender
    vm.prank(0x1111111111111111111111111111111111111111);
    wrappedLoanManagerDelegator.liquidate(1);

    // Bob can withdraw his whole deposit even though part of it was used as collateral and was liquidated
    uint256 bobCollateralWalletBalance = mockERC20Token[1].balanceOf(bob);
    vm.prank(bob);
    wrappedAssetManager.assetDepositWithdraw(assetId[1], IAssetManager.AssetActionType.Withdraw, bobDeposit);

    assertEq(mockERC20Token[1].balanceOf(bob) - bobCollateralWalletBalance, bobDeposit);
}
```

## 권장 사항

`assetTransferOnLoanAction`의 코드를 다음과 같이 변경하십시오:

```diff
else if (action == AssetActionType.Seize) {
        userDepositFrom.lockedAmount -= amount;
+       userDepositFrom.depositAmount -= amount;
        userDepositTo.depositAmount += amount;
    }
```

# [H-01] 비활성화된 대출 기관의 대출 구성을 대출자가 사용할 수 있음

## 심각도

**영향:**
중간, 대출자가 비활성화된 대출 구성을 사용할 수 있지만 정상적인 대출로 작동하므로 대출 기관의 가치 손실은 없음

**가능성:**
높음, 현재 코드로는 비활성화 기능이 작동할 수 없기 때문

## 설명

`LoanConfig` 구조체에는 `enabled` 필드가 있으며, 새로 생성된 대출 구성에 대해 `true`로 설정되고 `LoanConfigManager::updateLoanConfigEnabledStatus`에서 `true/false`로 설정할 수 있습니다. 문제는 `LoanManager::createLoan`에서 대출자가 대출 기관으로부터 대출을 받을 때 `configId`가 인수로 제공되고 해당 `LoanConfig` 구조체 객체가 메서드에서 사용되지만 `enabled` 속성은 확인되지 않는다는 것입니다. 즉, 대출 기관이 생성한 대출 구성에 대해 `enabled == false`로 `LoanConfigManager::updateLoanConfigEnabledStatus`를 호출하더라도 대출자는 구성을 계속 사용할 수 있습니다.

다음은 취약점을 보여주는 실행 가능한 개념 증명(PoC) 단위 테스트입니다(`LoanManager` 테스트 파일 끝에 추가 가능):

```solidity
function test_DisabledLoanConfigCanStillBeUsed() public {
    // Alice disables her loan config
    vm.prank(alice);
    wrappedLoanManagerDelegator.updateLoanConfigEnabledStatus(loan.configId, false);
    vm.startPrank(bob);

    // there are no current loans
    assertEq(0, wrappedLoanManagerDelegator.getLoanCounter());

    vm.expectEmit();
    emit LoanCreated(1, 1);
    wrappedLoanManagerDelegator.createLoan(loan, collateralAssets);

    // a loan was created
    assertEq(1, wrappedLoanManagerDelegator.getLoanCounter());

    // Alice's free deposit was lowered
    IAssetManager.DepositData memory aliceDepositAfterLending = wrappedAssetManager.depositOf(assetId[0], alice);
    assertEq(aliceDepositAfterLending.depositAmount, userDepositAlice[0] - loan.principalAmount);
    assertEq(aliceDepositAfterLending.lockedAmount, 0);
}
```

## 권장 사항

`LoanManager::createLoan`에서 대출 구성이 활성화되어 있는지 확인하고 그렇지 않은 경우 트랜잭션을 되돌리십시오(revert).

# [M-01] 자산 대출 구성이 EnumerableSet에서 제거되지 않을 수 있음

## 심각도

**영향:**
중간, 사용자가 플랫폼 사용에 제한을 받지만 가치는 잃지 않음

**가능성:**
중간, 사용자가 자산에 대해 최대 총 대출 구성을 생성했을 때 발생

## 설명

`LoanConfigManager` 계약은 `userConfigIds` 매핑을 사용하여 사용자가 자산에 대해 생성한 대출 구성 수를 추적하고 `maxLoanConfigsPerAsset`(`LuminDeploy` 배포 스크립트에서 10으로 설정됨)까지 제한합니다. 문제는 다음과 같은 `deleteLoanConfig` 메서드에 있습니다:

```solidity
function deleteLoanConfig(uint256 configId) external {
    LoanConfig storage config = loanConfigs[configId];
    if (config.lender != msg.sender) {
        revert NotAuthorized();
    }

    if (config.totalPendingPrincipalAmount > 0) {
        revert LoanConfigInUse();
    }

    delete (loanConfigs[configId]);
    userConfigIds[msg.sender][config.config.principalAssetId].remove(configId);

    emit LoanConfigDeleted(configId);
}
```

여기서 문제는 상당히 미묘합니다. `userConfigIds`를 사용하는 줄에서 `config` 저장소 포인터가 사용되는데, 이 포인터가 가리키는 저장소 셀의 값은 이전 `delete` 명령으로 인해 이미 0이 되었습니다. 이전 명령이 `config` 포인터가 가리키는 저장소 슬롯을 삭제하기 때문입니다. 즉, `config.config.principalAssetId`는 0이 되고 `EnumerableSet::remove` 메서드는 제거할 항목이 집합에 없을 때 되돌리지(revert) 않으므로 실제 제거는 실행되지 않습니다. 이는 `userConfigIds`의 집합이 증가할 수만 있음을 의미하지만 특정 시점에 10개 항목으로 제한되므로 사용자는 `createLoanConfig`에서 다음 확인에 걸리게 됩니다:

```solidity
if (userConfigIds[msg.sender][config.principalAssetId].length() >= limits.maxLoanConfigsPerAsset) {
    revert MaxConfigsReached();
}
```

즉, 사용자는 무엇을 하든 주어진 자산에 대해 더 이상 대출 구성을 추가할 수 없게 됩니다.

다음은 취약점을 보여주는 실행 가능한 개념 증명(PoC) 단위 테스트입니다(`LoanConfigManager` 테스트 파일 끝에 추가 가능):

```solidity
function test_AssetLoanConfigIsNotRemovedFromEnumerableSet() public {
    uint256 amount = 100_000;

    vm.startPrank(alice);
    mockERC20Token[0].approve(address(assetManagerProxy), amount);
    wrappedAssetManager.assetDepositWithdraw(
        loanConfigUser.principalAssetId, IAssetManager.AssetActionType.Deposit, amount
    );

    // Create 10 (the maximum total allowed for a single asset) loan configs for the same asset
    for (uint256 i = 0; i < 10; i++) {
        wrappedLoanManagerDelegator.createLoanConfig(loanConfigUser, loanConfigAssetUsage);
    }

    // Remove one of the loan configs
    wrappedLoanManagerDelegator.deleteLoanConfig(1);

    // Try to create another loan config for the same asset, but
    // it reverts since the count (length of the EnumerableSet) wasn't decremented
    vm.expectRevert(abi.encodeWithSelector(ILoanConfigManager.MaxConfigsReached.selector));
    wrappedLoanManagerDelegator.createLoanConfig(loanConfigUser, loanConfigAssetUsage);
}
```

## 권장 사항

코드를 다음과 같이 변경하십시오:

```diff
function deleteLoanConfig(uint256 configId) external {
    LoanConfig storage config = loanConfigs[configId];
    if (config.lender != msg.sender) {
        revert NotAuthorized();
    }

    if (config.totalPendingPrincipalAmount > 0) {
        revert LoanConfigInUse();
    }

-    delete (loanConfigs[configId]);
-    userConfigIds[msg.sender][config.config.principalAssetId].remove(configId);
+    userConfigIds[msg.sender][config.config.principalAssetId].remove(configId);
+    delete (loanConfigs[configId]);

    emit LoanConfigDeleted(configId);
}
```

또한 모범 사례로서 항상 `remove` 메서드의 반환 값을 확인하고 `false`인 경우 되돌리십시오(revert).

# [M-02] 프로토콜 일시 중지 해제 후 즉시 청산 발생 가능

## 심각도

**영향:**
높음, 사용자가 즉시 청산되어 가치를 잃을 수 있기 때문

**가능성:**
낮음, 프로토콜 관리자의 긴 일시 중지가 필요하기 때문

## 설명

모든 프로토콜 상호 작용이 발생하는 `LoanManagerDelegator` 계약은 Lumin 관리자가 일시 중지할 수 있습니다. 프로토콜이 오랫동안 일시 중지된 경우 대출자의 담보 자산 가격이 하락하여 대출자가 상환하거나 담보를 추가할 방법 없이 대출 청산이 가능해질 수 있으며, 심지어 대출 기간이 지날 수도 있습니다. 즉, 프로토콜이 일시 중지 해제되면 대출이 즉시 청산되어 대출자에게 손실이 발생할 수 있습니다.

## 권장 사항

사용자가 대출을 상환하거나 담보를 추가할 시간을 주기 위해 청산에 대한 일시 중지 해제 후 유예 기간을 추가하십시오.

# [M-03] 가격 피드 오라클 데이터 검증 누락

## 심각도

**영향:**
높음, 불공정한 청산으로 이어질 수 있기 때문

**가능성:**
낮음, 특정 드문 조건에서만 발생하기 때문

## 설명

`PriceFeedProxyChainlink`에서 가격 피드 데이터를 검증할 때 여러 문제가 있습니다:

- `intPrice == 0`일 때 코드가 되돌리지(revert) 않음
- 가격 최신성(staleness)이 확인되지 않음
- 시퀀서가 다운된 후 `GRACE_PERIOD`를 기다리지 않음

잘못된 가격을 사용하면 불공정한 대출/청산으로 이어질 수 있으므로 프로토콜에 해로울 수 있습니다.

## 권장 사항

시퀀서 `GRACE_PERIOD`의 경우 [Chainlink 문서](https://docs.chain.link/data-feeds/l2-sequencer-feeds#example-code)를 따르십시오. 또한 다음 변경 사항을 구현하십시오:

```diff
- if (intPrice < 0) {
+ if (intPrice <= 0) {
    revert ImplausiblePrice();
}
```

마지막으로 `latestRoundData` 호출의 타임스탬프 값을 확인하고 가격 피드의 하트비트 간격(플러스 10-30분 버퍼 기간)보다 오래되지 않았는지 확인하십시오.

## 논의

**pashov:** 가격 최신성 확인을 제외하고 부분적으로 수정되었습니다.

# [M-04] Lumin 관리자가 프로토콜에 대한 사용자 허용량을 악용할 수 있음

## 심각도

**영향:**
높음, 사용자의 허용량을 도난당할 수 있기 때문

**가능성:**
낮음, 악의적이거나 침해된 소유자가 필요하기 때문

## 설명

`AssetManager` 계약은 사용자가 자산을 플랫폼에 입금할 때 계약에 `ERC20::transferFrom` 호출을 실행할 수 있는 허용량을 제공하는 데 사용됩니다. 문제는 계약이 업그레이드 가능하다는 것입니다. 즉, Lumin 관리자는 계약을 통해 사용자로부터 자신의 주소로 `transferFrom`을 실행하는 기능을 추가하는 업그레이드로 사용자의 `AssetManager` 승인을 백런(back-run)할 수 있습니다.

## 권장 사항

사용자가 관리자 작업에 대응할 수 있도록 Lumin Admin 역할 보유자 주소를 Timelock 계약 뒤에 두십시오.

# [M-05] 전송 수수료(fee-on-transfer) 및 리베이스(rebasing) 토큰 미지원

## 심각도

**영향:**
높음, 토큰이 프로토콜에 갇힐 수 있기 때문

**가능성:**
낮음, 일반적으로 사용되는 토큰의 일부만이 그러한 메커니즘을 가지고 있기 때문

## 설명

`AssetManager`의 `_depositWithdraw` 메서드에는 다음 구현이 있습니다:

```solidity
function _depositWithdraw(address assetAddress, bool deposit, address sender, uint256 amount) private {
    if (deposit) {
        IERC20(assetAddress).safeTransferFrom(sender, address(this), amount);
    } else {
        IERC20(assetAddress).safeTransfer(sender, amount);
    }
}
```

또한 호출 전후에 다음과 같은 코드가 있습니다:

```solidity
assetDeposit.depositAmount -= amount;
userDeposit.depositAmount -= amount;
```

그리고 이것:

```solidity
assetDeposit.depositAmount += amount;
userDeposit.depositAmount += amount;
```

이 코드는 전송 수수료가 있거나 리베이스(전송 없이 토큰 잔액이 오르내림) 메커니즘이 있는 토큰을 고려하지 않습니다. ERC20 토큰의 `transfer` 또는 `transferFrom` 메서드에 제공된 `amount`를 캐싱(또는 제거)함으로써, 이것이 실제로 프로토콜이 받거나 보낸 금액이며 정적일 것이라고 암시하지만, 항상 그런 것은 아닙니다. 전송 수수료 토큰을 사용하는 경우 입금 시 실제 수령 금액은 더 적으므로 동일한 잔액을 인출할 수 없습니다. 리베이스 토큰의 경우 시간이 지남에 따라 계약 잔액이 감소할 수도 있는데, 이는 전송 수수료 토큰과 동일한 문제로 이어지며, 잔액이 증가하면 보상이 `AssetManager` 계약에 갇히게 됩니다.

## 권장 사항

전송 수수료 또는 리베이스 메커니즘이 있는 토큰을 지원하지 않는다고 명시적으로 문서화하거나 다음을 수행할 수 있습니다:

전송 수수료 토큰의 경우 전송 전후의 잔액을 확인하고 제공된 `amount` 인수와 동일한지 검증하십시오.
리베이스 토큰의 경우 가치가 하락하면 보유 잔액을 기준으로 캐시 된 준비금을 그에 따라 업데이트하는 메서드가 있어야 합니다. 이것은 복잡한 솔루션입니다.
리베이스 토큰의 경우 가치가 상승하면 초과 토큰을 프로토콜 밖으로(아마도 사용자에게 직접) 실제로 전송하는 메서드를 추가해야 합니다.

# [L-01] `priceFeedProxy`가 설정되었는지 확인하는 절차 누락

`AssetManager::updatePriceFeedProxyEnableStatus`에서 `priceFeedProxies[index] == address(0)`인지 확인하고 그렇다면 실행을 되돌려야(revert) 합니다. 이는 아직 설정되지 않은 프록시의 활성화 상태를 설정하는 것을 의미하기 때문입니다.

# [L-02] 자산 고유성은 심볼에 기반해서는 안 됨

`AssetManager::addAsset`에서 자산의 ID를 계산하는 데이터는 현재 `symbol` 인수 값(플러스 `collectionId`에 대한 하드코딩 된 0 값)입니다. 이것은 차선책이며 애플리케이션을 제한할 수 있습니다. 데이터를 해싱 하여 ID를 생성할 때 해당 데이터가 고유하기를 원하기 때문입니다. 심볼(NFT 심볼, ERC20 토큰 심볼)은 고유성이 보장되지 않으며 두 개의 다른 토큰/컬렉션이 동일한 심볼을 가질 수 있습니다. 고유 데이터에서 ID를 생성하는 올바른 방법은 주소가 고유하고 다른 토큰/컬렉션에 대해 동일할 수 없으므로 `assetAddress` 인수 값(ERC721/ERC1155 컬렉션의 경우 `collectionID`와 함께)을 사용하는 것입니다.

# [L-03] 동일한 ID를 가진 `LoanShares`의 최대 수를 명시적으로 제한

`LoanShares` 계약은 ERC1155의 구현이므로 ID가 같은 항목이 여러 개 있을 수 있습니다. 현재 `mint` 메서드는 항상 주어진 ID에 대해 `NUM_SHARES_PER_LOAN`(10으로 하드코딩 됨)을 민팅하지만 동일한 ID에 대한 여러 민트를 제한하지 않습니다. `LoanShares`의 `burn` 및 `sharesOf` 메서드는 주어진 ID의 모든 항목을 반복하는 `nftOwners[id].values()`를 호출하므로 `EnumberableSet::values` 호출의 배열 반복으로 인해 DoS 상태가 되지 않도록 동일한 ID로 민팅할 항목의 최대 수를 10개로 명시적으로 제한해야 합니다.

# [L-04] 코드 경로에 자산 존재 확인 누락

`AssetManager`의 `assetTransferOnLoanAction` 메서드는 수신된 `action` 인수를 기반으로 다른 코드 경로를 거칩니다. `action == AssetActionType.Lend`이면 `assetExists` 수정자가 있는 `_assetAction`을 호출합니다. 문제는 다른 모든 경우, 예를 들어 `action == AssetActionType.Lend`일 때 자산 존재 여부가 확인되지 않는다는 것입니다. `assetTransferOnLoanAction`의 모든 코드 경로에 자산 존재 확인을 추가하십시오.

# [L-05] 기본 구성으로 프로토콜을 배포할 수 없음

프로토콜의 계약은 컴파일러 버전 0.8.20을 사용하고 있으며 문서는 프로토콜이 Arbitrum에 배포될 것이라고 구체적으로 명시하고 있습니다. Arbitrum은 EVM 호환이지만 PUSH0 opcode는 네트워크에서 아직 지원되지 않으며 0.8.20 컴파일러 버전은 PUSH0 opcode가 포함된 바이트코드를 생성합니다. 이로 인해 계약을 Arbitrum(또는 기타 이러한 L2 네트워크)에 배포하려고 할 때 실패하게 됩니다. Solidity 컴파일러 버전 0.8.19를 사용하십시오.

# [L-06] `address(0)`을 유효한 값으로 사용

`AssetManager::_assetAction`에서 다음 코드 줄을 볼 수 있습니다:

```solidity
DepositData storage assetDeposit = deposits[assetId][address(0)];
```

이는 자산에 대한 사용자의 총 예치금을 추적하는 데 사용됩니다(또한 `AssetManager::balancesOf`에서 특수 처리가 구현됨). 문제는 여기서 `address(0)`이 유효한 값으로 사용되지만 `address(0)`은 `address` 유형 변수 또는 저장소 슬롯의 기본값이기도 하다는 것입니다. 이는 오류가 발생하기 쉽고 `deposits` 매핑의 기본값이 사용되고 비어 있지 않은 버그로 이어질 수 있습니다. 특히 `assetTransferOnLoanAction`에 `userAddressFrom` 또는 `userAddressTo` 인수 값에 대한 0 주소 확인이 없다는 점을 고려하면 더욱 그렇습니다. 모범 사례로서 별도의 매핑을 사용하여 `address(0)`을 유효한 값으로 사용하지 않도록 하십시오.

# [L-07] 너무 많은 이자를 지불한 사용자는 환불받지 못함

`LoanManager`의 `pay` 메서드는 NatSpec 문서에 명시적으로 다음과 같이 말합니다:

> /// @dev Does not refund when too much interest is paid.

즉, 대출자가 빚진 것보다 더 많은 이자를 상환하면 초과 가치는 대출 기관에 제공됩니다. 이것이 설계의 일부로 명시적으로 언급되어 있지만, 이는 여전히 대출자에게 가치 손실로 이어질 수 있으며(`pay`의 `interestAssetAmount` 인수에 잘못된 값을 입력한 경우) 이는 가능해서는 안 됩니다. 이 값을 대출자가 청구할 수 있는 매핑에 보관하는 것이 좋습니다.

# [L-08] 코드가 Checks-Effects-Interactions 패턴을 따르지 않음

`LoanManager`의 `createLoan` 메서드는 ERC1155 토큰을 민팅하지만 토큰 수신자에게 안전하지 않은 호출(`onERC1155Received` 호출)을 수행합니다. 이 호출은 `LoanManager` 계약이 변경 보류 상태(`assetTransferOnLoanAction` 호출이 이 시점에서 실행되지 않음)에 있는 동안 재진입할 수 있습니다. CEI 패턴을 따르도록 `mint` 호출을 `createLoan` 메서드의 마지막으로 이동하십시오.

# [L-09] 사용자가 자신에게 대출을 받을 수 있음

`LoanManager` 계약은 사용자가 자신에게 대출을 받을 수 있도록 허용합니다. 이는 프로토콜에 기능적 가치가 없지만 잠재적인 취약점에 대한 공격 표면을 넓힙니다. 대출 구성을 생성한 것과 동일한 주소가 시스템에서 대출을 받는 데 사용할 때 되돌리기(revert)하는 확인을 추가하십시오.
