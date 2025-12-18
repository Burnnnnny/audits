# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **ion-protocol** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Ion Protocol 정보 (About Ion Protocol)

Ion Protocol은 스테이킹 및 리스테이킹 자산을 위해 구축된 탈중앙화 머니 마켓입니다. 차용자는 수익을 창출하는 스테이킹 자산을 담보로 제공하여 WETH를 빌릴 수 있으며, 대출자는 차용자 담보에서 발생하는 부스트된 스테이킹 수익에 노출될 수 있습니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

_검토 커밋 해시_ - [0f78d89a16850cb880f0348bcc3d272aa0c2ee77](https://github.com/Ion-Protocol/ion-protocol/tree/0f78d89a16850cb880f0348bcc3d272aa0c2ee77)

_수정 검토 커밋 해시_ - [b42fbcb98b03183783672fc5765ae62ecda1c9cd](https://github.com/Ion-Protocol/ion-protocol/tree/b42fbcb98b03183783672fc5765ae62ecda1c9cd)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `IonLens`
- `RewardToken`
- `Vault`
- `VaultFactory`

# 발견 사항 (Findings)

# [C-01] 정규화된 잔액 대신 기초 자산을 입력 `amount`로 사용 (Using underlying as the input `amount` instead of normalized balances)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`IonPool`은 공급 지분의 양도 가능성을 활성화하기 위해 ERC20 기반 비리베이스(non-rebasing) 토큰인 `RewardToken`을 상속합니다. 그러나 토큰 전송 중 기초 자산 금액을 `amount` 입력으로 받습니다. 그런 다음 `supplyFactor`를 사용하여 이 금액을 `amountNormalized`로 변환하고, 변환된 `amountNormalized`를 기반으로 `from`과 `to`의 `_normalizedBalances`를 업데이트합니다.

```solidity
    function _transfer(address from, address to, uint256 amount) private {
        if (from == address(0)) revert ERC20InvalidSender(address(0));
        if (to == address(0)) revert ERC20InvalidReceiver(address(0));
        if (from == to) revert SelfTransfer(from);

        RewardTokenStorage storage $ = _getRewardTokenStorage();

        uint256 _supplyFactor = $.supplyFactor;
>>>     uint256 amountNormalized = amount.rayDivDown(_supplyFactor);

        uint256 oldSenderBalance = $._normalizedBalances[from];
        if (oldSenderBalance < amountNormalized) {
            revert ERC20InsufficientBalance(from, oldSenderBalance, amountNormalized);
        }
        // Underflow impossible
        unchecked {
>>>         $._normalizedBalances[from] = oldSenderBalance - amountNormalized;
        }
>>>     $._normalizedBalances[to] += amountNormalized;

        emit Transfer(from, to, amountNormalized);
    }
```

이 구현에는 몇 가지 문제가 있습니다:

- `supplyFactor`를 사용하여 기초 자산 금액을 `amountNormalized`로 변환할 때 먼저 이자를 발생시키지 않습니다. 이로 인해 `supplyFactor`가 이자 발생 후의 현재 공급 계수와 최신 상태가 아닐 수 있어 `amountNormalized`의 잘못된 양이 전송될 수 있습니다.

- `RewardingToken`은 이제 비리베이스 토큰입니다. ERC20 기반 `balanceOf` 함수는 이제 기초 자산 잔액 대신 사용자의 `_normalizedBalances`를 반환합니다. 사용자나 타사 통합자가 전송 금액에 `balanceOf` 결과를 사용하면 잘못된 양의 토큰을 전송하게 되어 이 ERC20 기반 비리베이스 토큰과의 모든 상호 작용이 중단될 수 있습니다.

## 권장 사항

이것이 이제 비리베이스 토큰이라는 점을 고려할 때, `_transfer`는 정규화된 금액을 `amount` 입력으로 받고 사용자의 `_normalizedBalances`를 직접 전송해야 합니다.

# [H-01] `IonPool`이 일시 중지된 동안 금고의 `_accrueFee` 트리거 (Triggering the vault's `_accrueFee` while the `IonPool` is paused)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`IonPool`이 일시 중지되면 이자 발생이 중지되고, Ion Protocol이 `IonPool`을 일시 중지 해제하면 모든 `ilks.lastRateUpdate`가 `block.timestamp`로 업데이트되어 풀이 일시 중지된 동안 프로토콜이 이자를 계산하는 것을 효과적으로 방지합니다. 그러나 `IonPool` 중 하나가 일시 중지된 상태에서 Vault의 `_accrueFee`가 트리거되면 여전히 이자 발생을 계산하고 총 자산에 반영하며 수수료 지분을 주조합니다.

```solidity
    function _accruedFeeShares() internal view returns (uint256 feeShares, uint256 newTotalAssets) {
>>>     newTotalAssets = totalAssets();
        uint256 totalInterest = _zeroFloorSub(newTotalAssets, lastTotalAssets);

        // The new amount of new iTokens that were created for this vault. A
        // portion of this should be claimable by depositors and some portion of
        // this should be claimable by the fee recipient.
        if (totalInterest != 0 && feePercentage != 0) {
            uint256 feeAssets = totalInterest.mulDiv(feePercentage, RAY);

            feeShares =
                _convertToSharesWithTotals(feeAssets, totalSupply(), newTotalAssets - feeAssets, Math.Rounding.Floor);
        }
    }
```

```solidity
    function totalAssets() public view override returns (uint256 assets) {
        uint256 _supportedMarketsLength = supportedMarkets.length();
        for (uint256 i; i != _supportedMarketsLength;) {
            IIonPool pool = IIonPool(supportedMarkets.at(i));

            uint256 assetsInPool =
>>>             pool == IDLE ? BASE_ASSET.balanceOf(address(this)) : pool.getUnderlyingClaimOf(address(this));

            assets += assetsInPool;

            unchecked {
                ++i;
            }
        }
    }
```

`_accrueFee`는 수수료 수령자를 위해 `newTotalAssets`와 `feeShares`를 계산하기 위해 `totalAssets()`에 의존한다는 것을 알 수 있습니다. `totalAssets()`는 각 등록된 풀의 자산을 계산하기 위해 `pool.getUnderlyingClaimOf`를 호출합니다.

```solidity
    function getUnderlyingClaimOf(address user) public view returns (uint256) {
        RewardTokenStorage storage $ = _getRewardTokenStorage();

        (uint256 totalSupplyFactorIncrease,,,,) = calculateRewardAndDebtDistribution();

>>>     return $._normalizedBalances[user].rayMulDown($.supplyFactor + totalSupplyFactorIncrease);
    }
```

`pool.getUnderlyingClaimOf`는 `IonPool`이 현재 일시 중지되었는지 여부와 관계없이 항상 증가된 공급 계수를 고려한 후 자산을 반환합니다. 이로 인해 총 자산 회계가 잘못되고 잘못된 양의 수수료 지분이 주조될 수 있습니다.

## 권장 사항

각 사용자에 대해 `getTotalUnderlyingClaimsUnaccrued`를 검색하는 함수를 `IonPool` 내부에 추가하는 것을 고려하십시오. 그런 다음 금고 내부에서 총 자산을 계산할 때 `IonPool`이 일시 중지되었는지 확인하는 검사를 포함하십시오. 풀이 일시 중지된 경우 `getUnderlyingClaimOf` 대신 해당 함수를 활용하십시오.

# [M-01] 새로운 이자 모듈이 과거 이자를 계산할 수 있음 (The new interest module may calculate past interest)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`updateInterestRateModule`은 Ion Protocol에서 호출하여 대출자 및 차용자에 대한 이자를 계산할 때 사용되는 `interestRateModule`을 변경할 수 있습니다.

```solidity
    function updateInterestRateModule(InterestRate _interestRateModule) external onlyRole(ION) {
        // @audit - should trigger accrueInterest here? or at least must update timestamp last
        if (address(_interestRateModule) == address(0)) revert InvalidInterestRateModule(_interestRateModule);

        IonPoolStorage storage $ = _getIonPoolStorage();

        // Sanity check
        if (_interestRateModule.COLLATERAL_COUNT() != $.ilks.length) {
            revert InvalidInterestRateModule(_interestRateModule);
        }
        $.interestRateModule = _interestRateModule;

        emit InterestRateModuleUpdated(address(_interestRateModule));
    }
```

그러나 `_accrueInterest`가 트리거되지 않으며, 모든 `ilk.lastRateUpdate` 값도 현재 `block.timestamp`로 업데이트되지 않는 것을 관찰할 수 있습니다. 이는 상당한 시간 동안 `_accrueInterest`가 이전에 호출되지 않은 경우 새 `interestRateModule`이 과거 대출자 및 차용자의 실적에 대한 이자를 계산하는 데 사용되기 때문에 문제를 일으킬 수 있습니다.

```solidity
    function _calculateRewardAndDebtDistributionForIlk(
        uint8 ilkIndex,
        uint256 totalEthSupply
    )
        internal
        view
        returns (
            uint256 supplyFactorIncrease,
            uint256 treasuryMintAmount,
            uint104 newRateIncrease,
            uint256 newDebtIncrease,
            uint48 timestampIncrease
        )
    {
        // ...
        uint256 totalDebt = _totalNormalizedDebt * ilk.rate; // [WAD] * [RAY] = [RAD]

        (uint256 borrowRate, uint256 reserveFactor) =
>>>         $.interestRateModule.calculateInterestRate(ilkIndex, totalDebt, totalEthSupply);
        if (borrowRate == 0) return (0, 0, 0, 0, 0);

        // Calculates borrowRate ^ (time) and returns the result with RAY precision
>>>     uint256 borrowRateExpT = _rpow(borrowRate + RAY, block.timestamp - ilk.lastRateUpdate, RAY);

        // Unsafe cast OK
        timestampIncrease = uint48(block.timestamp) - ilk.lastRateUpdate;

        // ...
        newRateIncrease = ilk.rate.rayMulUp(borrowRateExpT - RAY).toUint104(); // [RAY]

        // ...
    }
```

이 동작으로 인해 차용자나 대출자에게 예상치 못한 이자 발생 금액이 발생할 수 있습니다.

## 권장 사항

`interestRateModule`을 변경하기 전에 `_accrualInterest`를 트리거하는 것을 고려하십시오. 이전 `interestRateModule`이 고장 났거나 호출을 되돌리는 경우, `interestRateModule`을 변경한 후 모든 `ilk.lastRateUpdate`를 `block.timestamp`로 업데이트하여 향후 이자 발생에만 사용되도록 하십시오.

## Ion Protocol 의견

우리는 이 동작을 알고 있습니다. 관리자가 이자를 발생시키지 않고 IRM을 변경할 수 있는 것은 사실입니다.

- 하지만 이는 오작동하는 IRM을 되돌릴 수 있는 유일한 방법이므로 현재 기능으로 간주됩니다. ARM에 심각한 결함이 있는 경우 관리자는 이자를 발생시키지 않고 금리 모듈을 재배포할 수 있습니다(`pause()`는 이자를 발생시킵니다).

인지했으나 수정하지 않을 것입니다.

# [M-02] 할당자가 공급 상한을 우회할 수 있음 (Allocator can bypass supply cap)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

Vault의 소유자는 금고의 풀 목록에 대한 공급 상한을 지정하고 할당자는 해당 공급 상한을 기반으로 해당 풀에 자금을 분배합니다. 소유자는 일부 토큰이 금고에 머물도록 허용하기 위해 IDLE 풀을 지정할 수 있습니다. 문제는 할당자가 IDLE 풀의 공급 상한을 우회하고 모든 토큰을 금고에 유지하여 소유자가 설정한 제한을 우회하고 사용자가 더 적은 이자를 받을 수 있다는 것입니다. 근본 원인은 `reallocate()` 함수에서 `currentIdleDeposits` 값이 `for` 루프 외부에서 캐시되고 루프 내부에서 입금이 발생할 때 업데이트되지 않으며 코드가 해당 캐시된 값을 사용하여 공급 상한 제한을 확인하기 때문입니다. 따라서 할당자가 `allocations[]` 목록에서 IDLE 풀을 여러 번 사용하고 여러 번 입금하면 IDLE 풀에 대한 공급 상한 확인을 우회할 수 있습니다.

## 권장 사항

루프 내부에서 입금/출금이 발생할 때 `currentIdleDeposits`를 업데이트하십시오.

# [M-03] Vault에서의 인플레이션 공격 (Inflation attack in Vault)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

Vault 계약은 `_decimalsOffset`을 사용하여 공유 가치에 더 많은 정밀도를 추가합니다. 문제는 기초 토큰이 18자리 소수점(WETH 및 대부분의 토큰의 경우)을 갖는 경우 `_decimalsOffset` 값이 0이 된다는 것입니다. 따라서 지분 계산은 다음과 같습니다:

```solidity
          assets.mulDiv(newTotalSupply + 1, newTotalAssets + 1, rounding)
```

그리고 코드는 IDLE 풀 할당을 계산하기 위해 `balanceOf(address(this))`를 사용하기 때문에 1 wei 토큰을 예치하여 1 wei 지분을 주조한 다음 100e18 토큰을 기부하여 PPS 값을 부풀리고 다른 사용자가 계약과 상호 작용할 때 큰 나눗셈 반올림 오류로 인해 자금을 잃을 수 있습니다.

```solidity
    function test_initial_deposit_grief() public {

        IIonPool[] memory market = new IIonPool[](1);
        market[0] = IDLE;

        uint256[] memory allocationCaps = new uint256[](1);
        allocationCaps[0] = 250e18;

        IIonPool[] memory queue = new IIonPool[](4);
        queue[0] = IDLE;
        queue[1] = weEthIonPool;
        queue[2] = rsEthIonPool;
        queue[3] = rswEthIonPool;

        vm.prank(OWNER);
        vault.addSupportedMarkets(market, allocationCaps, queue, queue);

        setERC20Balance(address(BASE_ASSET), address(this), 11e18 + 10);

        uint256 initialAssetBalance = BASE_ASSET.balanceOf(address(this));
        console.log("attacker balance before : ");
        console.log(initialAssetBalance);


        vault.mint(10, address(this));

        IERC20(address(BASE_ASSET)).transfer(address(vault), 11e18);

        address alice = address(0xabcd);
        setERC20Balance(address(BASE_ASSET), alice, 10e18 + 10);
        vm.startPrank(alice);
        IERC20(address(BASE_ASSET)).approve(address(vault), 1e18);
        vault.deposit(1e18, alice);
        vm.stopPrank();

        uint256 aliceShares = vault.balanceOf(alice);
        console.log("alice shares : ");
        console.log(aliceShares);

        vault.redeem(vault.balanceOf(address(this)), address(this), address(this));
        uint256 afterAssetBalance = BASE_ASSET.balanceOf(address(this));
        console.log("attacker balance after : ");
        console.log(afterAssetBalance);

    }
```

테스트 출력 :

```shell
Logs:
  attacker balance before :
  11000000000000000010
  alice shares :
  0
  attacker balance after :
  10909090909090909100
```

공격자가 ~ `0.1 ETH`의 비용으로 Alice의 자산 `1 ETH`를 잠글 수 있음을 관찰할 수 있습니다.

## 권장 사항

`_decimalsOffset` 값을 6으로 설정하거나 소량의 초기 예금으로 이를 완화하는 것을 고려하십시오.

# [M-04] 새 풀이 일시적으로 차단될 수 있음 (New pool can be temporarily blocked)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

새로 배포된 Ion 풀은 소량의 기초 자산을 공급하고 빌려서 DoS 공격을 받을 수 있습니다. `accrueInterest`가 호출되면 Ion 풀은 `InterestRate` 계약에서 `borrowRate` 값을 요청합니다:

```solidity
        (uint256 borrowRate, uint256 reserveFactor) =
            $.interestRateModule.calculateInterestRate(ilkIndex, totalDebt, totalEthSupply);
```

`calculateInterestRate` 함수에서 `distributionFactor`가 0이 아니고 ETH 공급이 상대적으로 적은 경우 0으로 나누기가 발생할 수 있습니다:

```solidity
    function calculateInterestRate(
        uint256 ilkIndex,
        uint256 totalIlkDebt,
        uint256 totalEthSupply
    )
        external
        view
        returns (uint256, uint256)
    {
        ---SNIP---
        if (distributionFactor == 0) {
            return (ilkData.minimumKinkRate, ilkData.reserveFactor.scaleUpToRay(4));
        }
        // [RAD] / [WAD] = [RAY]
        uint256 utilizationRate =
>>          totalEthSupply == 0 ? 0 : totalIlkDebt / (totalEthSupply.wadMulDown(distributionFactor.scaleUpToWad(4)));
```

이 문제로 인해 `accrueInterest`가 모든 풀 작업에서 호출되므로 모든 풀 작업이 차단됩니다. 풀을 정상 상태로 되돌리는 유일한 방법은 이자율 모듈을 `distributionFactor`가 0인 모듈로 교체하는 것입니다.

공격자가 두 개의 별도 트랜잭션에서 풀을 차단하는 `IonPool.t.sol`에 대한 코딩된 POC입니다. (대출자의 `supply` 호출이 `setUp`에서 제거됨):

```solidity
    function test_DoS() public {
        uint256 collateralDepositAmount = 10e18;
        uint256 normalizedBorrowAmount = 1;

        vm.startPrank(lender1);
        underlying.approve(address(ionPool), type(uint256).max);
        ionPool.supply(lender1, 2, new bytes32[](0));
        vm.stopPrank();

        vm.startPrank(borrower1);
        ionPool.depositCollateral(0, borrower1, borrower1, collateralDepositAmount, new bytes32[](0));
        ionPool.borrow(0, borrower1, borrower1, normalizedBorrowAmount, new bytes32[](0));

        vm.warp(block.timestamp + 1);
        vm.expectRevert();
        ionPool.accrueInterest();
    }
```

## 권장 사항

`calculateInterestRate`의 확인을 업데이트하는 것을 고려하십시오.

```diff
+       if (distributionFactor == 0 || totalEthSupply.wadMulDown(distributionFactor.scaleUpToWad(4) == 0) {
            return (ilkData.minimumKinkRate, ilkData.reserveFactor.scaleUpToRay(4));
        }
```

# [M-05] 허용 목록에 없는 주소도 대출자가 될 수 있음 (Non-whitelisted addresses can be lenders)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

허용 목록에 있는 사용자만 기초 토큰 대출로 이자를 얻을 수 있습니다:

```solidity
    function supply(
        address user,
        uint256 amount,
        bytes32[] calldata proof
    )
        external
        whenNotPaused
>>      onlyWhitelistedLenders(user, proof)
    {
```

그러나 보상 토큰의 양도 기능은 목록에 없는 사용자도 단순히 전송받음으로써 이자 발생 토큰을 받을 수 있는 기회를 열어줍니다.

## 권장 사항

ERC20 `_transfer` 함수 내에서 Whitelist 계약에 증명을 전달하는 것은 불가능합니다. 따라서 잠재적인 해결책은 허용 목록이 있는 Ion 풀에 대한 전송을 비활성화하는 것일 수 있습니다.

## Ion Protocol 의견

`Whitelist`가 주조된 `iToken`을 받을 수 있는 사람을 제한하는 목적으로만 사용되는 것은 설계에 의한 것입니다. 해당 `iToken`의 소유자가 토큰을 다른 주소로 전송하고자 하는 경우 이를 제한할 의도가 없습니다.

또한 `Whitelist`는 프로토콜의 초기 출시를 위한 안전 장치로만 사용될 예정이며 지속적인 기능은 아닙니다.

수정하지 않을 것입니다.
# [M-06] 차용자는 즉시 청산될 수 있음 (The borrower can be instantly liquidated)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`IonPool.sol`은 즉시 청산될 수 있는 안전하지 않은 포지션을 생성할 수 있도록 허용합니다. 사용자가 포지션을 생성할 때 기본적인 포지션 확인만 수행됩니다:

```solidity
function _modifyPosition(

            ---SNIP---
            uint256 newTotalDebtInVault = ilkRate * _vault.normalizedDebt;

            uint256 ilkSpot = $.ilks[ilkIndex].spot.getSpot();
            // vault is either less risky than before, or it is safe
            if (
                both(
                    either(changeInNormalizedDebt > 0, changeInCollateral < 0),
>>                  newTotalDebtInVault > _vault.collateral * ilkSpot
                )
            ) revert UnsafePositionChange(newTotalDebtInVault, _vault.collateral, ilkSpot);
```

이를 `Liquidation.sol`의 검증과 비교하십시오:

```solidity
    function liquidate(

        ---SNIP---
        uint256 collateralValue = (collateral * exchangeRate).rayMulDown(configs.liquidationThreshold);
        {
>>          uint256 healthRatio = collateralValue.rayDivDown(normalizedDebt * rate); // round down in protocol favor
            if (healthRatio >= RAY) {
                revert VaultIsNotUnsafe(healthRatio);
            }
```

추가 값 `configs.liquidationThreshold`에 유의하십시오. 이러한 불일치로 인해 생성 시 건전하다고 간주되었던 포지션이 즉시 청산될 수 있습니다.

`Liquidation.t.sol`에 대한 코딩된 POC:

```solidity
    function test_InstaLiq() public {
        uint256 keeperInitialUnderlying = 100 ether;

        // calculating resulting state after liquidations
        DeploymentArgs memory dArgs;
        StateArgs memory sArgs;

        sArgs.collateral = 100e18; // [wad]
        sArgs.exchangeRate = 1e18; // [wad]
        sArgs.normalizedDebt = 50e18; // [wad]
        sArgs.rate = 1e27; // [ray]

        dArgs.liquidationThreshold = 0.5e27; // [ray]
        dArgs.targetHealth = 1.25e27; // [ray]
        dArgs.reserveFactor = 0.02e27; // [ray]
        dArgs.maxDiscount = 0.2e27; // [ray]
        dArgs.dust = 0; // [rad]

        Results memory results = calculateExpectedLiquidationResults(dArgs, sArgs);

        liquidation = new Liquidation(
            address(ionPool),
            protocol,
            exchangeRateOracles[0],
            dArgs.liquidationThreshold,
            dArgs.targetHealth,
            dArgs.reserveFactor,
            dArgs.maxDiscount
        );
        ionPool.grantRole(ionPool.LIQUIDATOR_ROLE(), address(liquidation));

        // set exchangeRate
        reserveOracle1.setExchangeRate(uint72(sArgs.exchangeRate));

        // create position
        borrow(borrower1, ILK_INDEX, 100 ether, 100 ether);

        // liquidate
        underlying.mint(keeper1, keeperInitialUnderlying);
        vm.startPrank(keeper1);
        underlying.approve(address(liquidation), keeperInitialUnderlying);
        liquidation.liquidate(ILK_INDEX, borrower1, keeper1);
        vm.stopPrank();
    }
```

## 권장 사항

`Liquidation.sol`과 동일한 포지션 안전 검증을 `_modifyPosition`에 구현하는 것을 고려하십시오.

## Ion Protocol 의견

이것은 `liquidationThreshold` 및 `LTV`(포지션 생성 시 최대 LTV) 값이 올바르게 구성되었는지에 따라 다릅니다.

- 다음을 고려하십시오:
  - `IonPool` 포지션 생성은 `debtQuantity <= collateralQuantity * min(marketPrice, exchangeRate) * LTV`를 시행합니다.
  - `debtQuantity < collateralQuantity * exchangeRate * liquidationThreshold`인 경우 청산이 불가능합니다.
- `liquidationThreshold > LTV`인 한 포지션이 즉시 청산되는 것은 불가능합니다.
  - 위의 두 방정식을 보면, `liquidationThreshold > LTV`라고 가정하면 `min(marketPrice, exchangeRate) < exchangeRate)`이므로 1이 참이면 방정식 2가 참이 되는 것은 불가능합니다.

이것이 올바른 매개변수에 달려 있음을 인정하며, 수정하지 않을 것입니다.

# [M-07] 일시 중지된 경우 `_depositable` 및 `_withdrawable`이 반환됨 (`_depositable` and `_withdrawable` return if paused)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

Vault는 입금 및 출금 대기열을 사용하여 작동합니다. 대기열의 첫 번째 풀에서 시작하여 처리할 수 있는 자산의 양을 확인합니다. 양이 충분하지 않으면 모든 사용자의 자산이 사용될 때까지 다음 풀로 이동합니다. `_depositable` 및 `_withdrawable` 함수는 이 작업에 사용됩니다:

```solidity

    function _withdrawable(IIonPool pool) internal view returns (uint256) {
        uint256 currentSupplied = pool.getUnderlyingClaimOf(address(this));
        uint256 availableLiquidity = uint256(pool.extsload(ION_POOL_LIQUIDITY_SLOT));

        return Math.min(currentSupplied, availableLiquidity);
    }

    function _depositable(IIonPool pool) internal view returns (uint256) {
        uint256 allocationCapDiff = _zeroFloorSub(caps[pool], pool.getUnderlyingClaimOf(address(this)));
        uint256 supplyCapDiff =
            _zeroFloorSub(uint256(pool.extsload(ION_POOL_SUPPLY_CAP_SLOT)), pool.getTotalUnderlyingClaims());

        return Math.min(allocationCapDiff, supplyCapDiff);
    }
```

불행히도 풀이 일시 중지되었는지 확인하지 않습니다. 풀이 일시 중지된 경우 자산 공급이나 출금 모두 불가능합니다. 이로 인해 다음과 같은 결과가 발생합니다:

- 일시 중지된 풀을 건너뛰지 않으므로 볼트 입금/주조/출금/상환 작업 실패
- maxDeposit/Mint/Withdraw/Redeem 함수에서 잘못된 결과

## 권장 사항

풀이 일시 중지되었는지 확인하고 `_depositable`, `_withdrawable` 내부에서 0을 반환하는 것을 고려하십시오.

# [L-01] RewardToken의 이벤트가 잘못된 금액 사용 (Event in RewardToken uses wrong amount)

RewardToken은 더 이상 리베이스 토큰이 아니며 사용자의 실제 잔액은 `balanceOf()` 함수가 반환하는 정규화된 잔액입니다. 문제는 코드의 모든 이벤트가 실제 금액을 반환하여 이벤트가 사용자의 작업이나 현재 잔액과 일치하지 않아 사용자에게 불편할 수 있다는 것입니다.

또한 전송은 기초 금액에 대한 것과 정규화된 금액에 대한 것 두 개의 Transfer 이벤트를 내보냅니다.

[링크1](https://github.com/Ion-Protocol/ion-protocol/blob/0f78d89a16850cb880f0348bcc3d272aa0c2ee77/src/token/RewardToken.sol#L299)

[링크2](https://github.com/Ion-Protocol/ion-protocol/blob/0f78d89a16850cb880f0348bcc3d272aa0c2ee77/src/token/RewardToken.sol#L337)

# [L-02] 슬리피지 보호 없음 (No slippage protection)

사용자는 `supply()`, `withdraw()`, `borrow()` 및 `repay()` 함수를 호출하여 IonPool과 상호 작용합니다. 문제는 사용자가 받거나 지불할 금액이 블록체인 상태에 따라 달라지며 사용자가 tx에 서명한 시점과 tx가 채굴될 때 사이에 다를 수 있다는 것입니다. 이 함수에는 슬리피지 확인이 없으며 사용자는 tx에 대해 슬리피지를 지정할 수 없으므로 사용자가 자금을 잃을 수 있습니다. 특히 `borrow()` 및 `repay()` 함수의 경우 사용자는 `amountOfNormalizedDebt` 값을 지정하고 실제 전송된 토큰 금액은 변경될 수 있는 `rate` 값에 따라 달라집니다.

## Ion Protocol 의견

인지했으나 수정하지 않을 것입니다.

- 슬리피지 보호는 주변 장치에서 구현할 수 있습니다. 예를 들어 플래시 레버리지 계약에는 최대 결과 부채 슬리피지 임계값이 있습니다.
- 슬리피지 임계값을 구현하는 데 문제는 없지만 현재 핵심에서 이 변경을 수행할 계획은 없습니다.

# [L-03] 새로 주조된 금액이 재무부에 고려되지 않음 (The newly minted amount not considered for the treasury)

`getTotalUnderlyingClaims`가 호출되면 먼저 `alculateRewardAndDebtDistribution`을 호출하여 `totalSupplyFactorIncrease`를 얻고 이 새로운 `totalSupplyFactorIncrease`를 고려하여 총 기초 금액을 계산합니다.

```solidity
    function getTotalUnderlyingClaims() public view returns (uint256) {
        RewardTokenStorage storage $ = _getRewardTokenStorage();

        uint256 _normalizedTotalSupply = $.normalizedTotalSupply;

        if (_normalizedTotalSupply == 0) {
            return 0;
        }

>>>     (uint256 totalSupplyFactorIncrease,,,,) = calculateRewardAndDebtDistribution();
>>>     return _normalizedTotalSupply.rayMulDown($.supplyFactor + totalSupplyFactorIncrease);
    }
```

그러나 이는 재무부를 위해 새로 주조된 토큰 금액을 고려하지 않아 이 값에 의존하는 함수가 잘못된 총 기초 금액을 반환하게 합니다. 이 함수는 Vault 내부의 해당 풀에 대해 예치된 토큰 금액을 계산할 때 사용됩니다. `supply`와 같이 `getTotalUnderlyingClaims`를 사용하는 `IonPool` 내부 함수는 먼저 `_accrueInterest`를 트리거하기 때문에 잘못된 값을 반환하지 않습니다.

```solidity
    function _depositable(IIonPool pool) internal view returns (uint256) {
        uint256 allocationCapDiff = _zeroFloorSub(caps[pool], pool.getUnderlyingClaimOf(address(this)));
       // @audit - this will result in incorrect supplyCapDiff amount
        uint256 supplyCapDiff =
>>>         _zeroFloorSub(uint256(pool.extsload(ION_POOL_SUPPLY_CAP_SLOT)), pool.getTotalUnderlyingClaims());

        return Math.min(allocationCapDiff, supplyCapDiff);
    }
```

`totalTreasuryMintAmount`를 고려하여 함수를 수정하는 것을 고려하십시오.

```diff
    function getTotalUnderlyingClaims() public view returns (uint256) {
        RewardTokenStorage storage $ = _getRewardTokenStorage();

        uint256 _normalizedTotalSupply = $.normalizedTotalSupply;

        if (_normalizedTotalSupply == 0) {
            return 0;
        }

-        (uint256 totalSupplyFactorIncrease,,,,) = calculateRewardAndDebtDistribution();
+       (uint256 totalSupplyFactorIncrease, uint256 totalTreasuryMintAmount,,,) = calculateRewardAndDebtDistribution();
-        return _normalizedTotalSupply.rayMulDown($.supplyFactor + totalSupplyFactorIncrease);
+        return _normalizedTotalSupply.rayMulDown($.supplyFactor + totalSupplyFactorIncrease) + totalTreasuryMintAmount;
    }
```

# [L-04] `feePercentage` 변경이 과거 이자에 영향을 미침 (Changing `feePercentage` will affect past interest)

`updateFeePercentage`를 호출하여 `feePercentage`를 변경할 때 `_accrueFee`를 트리거하지 않습니다.

```solidity
    function updateFeePercentage(uint256 _feePercentage) external onlyRole(OWNER_ROLE) {
        if (_feePercentage > RAY) revert InvalidFeePercentage();
        feePercentage = _feePercentage;
    }
```

즉, `feePercentage`를 업데이트하면 잠재적으로 과거 이자 발생에 영향을 미치고 이 새로운 `feePercentage`를 사용하여 과거 수수료 지분을 계산할 수 있습니다.

`feePercentage`를 업데이트하기 전에 `_accrueFee`를 트리거하는 것이 좋습니다.

# [L-05] 이전 `feeRecipient`가 적격 수수료를 잃을 수 있음 (Previous `feeRecipient` could lose the deserved fee)

`updateFeeRecipient`가 호출되고 `feeRecipient`가 변경될 때 `_accrueFee`를 트리거하지 않습니다.

```solidity
    function updateFeeRecipient(address _feeRecipient) external onlyRole(OWNER_ROLE) {
        feeRecipient = _feeRecipient;
    }
```

`feeRecipient`를 업데이트하기 전에 `_accrueFee`를 트리거하지 않으면 이전 `feeRecipient`에 대한 이자 발생에서 적격 수수료 지분을 잃을 수 있습니다.

`feeRecipient`를 업데이트하기 전에 `_accrueFee`를 트리거하십시오.

## Ion Protocol 의견

이 함수 내에서 수수료 발생을 강제하지 않으면 소유자에게 다음과 같은 유연성을 제공합니다.

1. 이미 발생한 수수료의 수령인 주소를 업데이트합니다.
2. 또는 현재 주소로 수수료를 발생시키고 향후 수수료가 새 주소로 발생하도록 설정합니다.

수정하지 않을 것입니다.

# [L-06] 사용자 자금에 대한 `addOperator` 보안 위험 (`addOperator` security risk for the user's funds)

`allowList`의 현재 구현은 운영자가 다음을 수행할 수 있도록 합니다:

- 담보 제거 및 사용자의 금고에 부채 추가
- 사용자의 젬 토큰 이동
- 사용자의 기초 토큰 전송

Alice가 Bob에게 젬 담보 사용 권한을 부여하려는 시나리오에서 그녀는 `addOperator`를 사용합니다. 그러나 이 작업은 Bob에게 담보 외에도 그녀의 금고 및 기초 토큰에 대한 제어 권한을 부여합니다. 이 문제를 해결하려면 다음과 같은 열거된 작업을 추가하는 것을 고려하는 것이 좋습니다:

```solidity
enum Ops {
    useVault,
    useCollateral,
    useInderlying
}

function addOperator(operator, Ops operation) external {
```

이 접근 방식은 더 많은 유연성을 제공하고 허용된 작업을 명확하게 정의하여 보안을 강화합니다.

## Ion Protocol 의견

운영자 추가는 사용자가 금고 제어를 다른 주소로 허용하기 위해 자발적으로 취하는 조치입니다.

- 제안된 방법이 위임 권한에 더 많은 세분성을 허용하지만 이 변경이 필요하다고 생각하지 않습니다.

# [L-07] 지원되는 시장 목록에 대한 최대 제한 없음 (No max limit for supported market list)

Vault에는 지원되는 시장을 반복하는 여러 작업이 있습니다. 문제는 지원되는 시장 길이에 대한 최대 제한이 없으며 관리자가 `addSupportedMarkets()`를 호출하여 많은 시장을 추가하면 `removeSupportedMarkets()`, `reallocate()`, `deposit()` 및 `withdraw()`와 같은 다른 함수가 `addSupportedMarkets()`보다 로직이 복잡하여 OOG가 발생할 수 있고 시장이 교착 상태에 빠질 수 있다는 것입니다.

# [L-08] `timestampIncrease`가 0으로 잘못 설정됨 (`timestampIncrease` is incorrectly set to 0)

이자 발생을 계산하기 위해 `_calculateRewardAndDebtDistributionForIlk`가 호출되면 `interestRateModule.calculateInterestRate`를 호출하여 새로운 증가된 부채 및 공급율을 결정하기 위한 `borrowRate` 및 `reserveFactor`를 얻습니다. 그러나 반환된 `borrowRate`가 0이면 `timestampIncrease`를 0으로 잘못 설정합니다.

```solidity
    function _calculateRewardAndDebtDistributionForIlk(
        uint8 ilkIndex,
        uint256 totalEthSupply
    )
        internal
        view
        returns (
            uint256 supplyFactorIncrease,
            uint256 treasuryMintAmount,
            uint104 newRateIncrease,
            uint256 newDebtIncrease,
            uint48 timestampIncrease
        )
    {
        IonPoolStorage storage $ = _getIonPoolStorage();
        Ilk storage ilk = $.ilks[ilkIndex];

        uint256 _totalNormalizedDebt = ilk.totalNormalizedDebt;
        if (_totalNormalizedDebt == 0 || block.timestamp == ilk.lastRateUpdate) {
            // Unsafe cast OK
            // block.timestamp - ilk.lastRateUpdate will almost always be 0
            // here. The exception is on first borrow.
            return (0, 0, 0, 0, uint48(block.timestamp - ilk.lastRateUpdate));
        }

        uint256 totalDebt = _totalNormalizedDebt * ilk.rate; // [WAD] * [RAY] = [RAD]
        (uint256 borrowRate, uint256 reserveFactor) =
            $.interestRateModule.calculateInterestRate(ilkIndex, totalDebt, totalEthSupply);
>>>     if (borrowRate == 0) return (0, 0, 0, 0, 0);
       // ...
}
```

`_totalNormalizedDebt`가 0이 아니지만 반환된 `borrowRate`가 0인 경우 언급된 시나리오가 발생할 수 있습니다. `borrowRate`가 0일 때 `timestampIncrease`를 `uint48(block.timestamp - ilk.lastRateUpdate)`로 설정하는 것도 고려하십시오.

# [L-09] `maxWithdraw` 및 `maxRedeem`이 잘못된 값을 반환할 수 있음 (`maxWithdraw` and `maxRedeem` could return the wrong value)

`maxWithdraw` 및 `maxRedeem`은 볼트의 상호 작용에 필요한 ERC4626 표준 함수입니다.

```solidity
    function maxWithdraw(address owner) public view override returns (uint256 assets) {
        (assets,,) = _maxWithdraw(owner);
    }
```

```solidity
    function maxRedeem(address owner) public view override returns (uint256) {
        (uint256 assets, uint256 newTotalSupply, uint256 newTotalAssets) = _maxWithdraw(owner);
        return _convertToSharesWithTotals(assets, newTotalSupply, newTotalAssets, Math.Rounding.Floor);
    }
```

함수가 호출되면 `_maxWithdraw`에서 반환된 자산이 필요합니다. `_maxWithdraw`는 `newTotalAssets`와 `feeShares`를 계산한 다음 `_convertToAssetsWithTotals`를 사용하여 자산을 계산합니다.

```solidity
    function _maxWithdraw(address owner)
        internal
        view
        returns (uint256 assets, uint256 newTotalSupply, uint256 newTotalAssets)
    {
        uint256 feeShares;
        (feeShares, newTotalAssets) = _accruedFeeShares();
        newTotalSupply = totalSupply() + feeShares;

>>>     assets = _convertToAssetsWithTotals(balanceOf(owner), newTotalSupply, newTotalAssets, Math.Rounding.Floor);

        assets -= _simulateWithdrawIon(assets);
    }
```

그러나 호출자가 볼트의 수수료 수령자인 경우 이 함수는 잘못된 값을 반환할 수 있습니다. `_convertToAssetsWithTotals`에 소유자의 잔액을 제공할 때 소유자가 수수료 수령자인 경우 계산에 `feeShares`도 추가해야 합니다.

수수료 수령자가 사용하는 경우 반환된 값은 잘못된 것이며 수수료 수령자 작업은 잘못된 값을 사용하여 진행됩니다.

소유자가 수수료 수령자인 경우 계산에 feeShares를 추가하십시오.

```diff
    function _maxWithdraw(address owner)
        internal
        view
        returns (uint256 assets, uint256 newTotalSupply, uint256 newTotalAssets)
    {
        uint256 feeShares;
        (feeShares, newTotalAssets) = _accruedFeeShares();
        newTotalSupply = totalSupply() + feeShares;
+       uint256 shareBalances = balanceOf(owner);
+       if (owner == feeRecipient) {
+          shareBalances += feeShares;
+      }

-       assets = _convertToAssetsWithTotals(balanceOf(owner), newTotalSupply, newTotalAssets, Math.Rounding.Floor);
+       assets = _convertToAssetsWithTotals(shareBalances, newTotalSupply, newTotalAssets, Math.Rounding.Floor);
        assets -= _simulateWithdrawIon(assets);
    }
```
