# Pashov Audit Group 정보

Pashov Audit Group은 업계에서 잘 증명된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 챔피언이거나, 우리와 함께한 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하고 패치하는 데 도움을 주면서, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**RegnumAurumAcquisitionCorp/contracts** 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며, **DemoreXTess, pontifex, Hals, Oblivionis, TheWeb3Mechanic, smbv1923**이 **Regnum Aurum** 검토에 참여했습니다. 총 **15**개의 문제가 발견되었습니다.

# Regnum Aurum 정보

Regnum Aurum (RAAC)은 부동산을 NFT(RAACNFT)와 분할 인덱스 토큰(iRAAC)으로 토큰화하여 부동산 가치에 대한 온체인 대출, 차입 및 유동성을 가능하게 하는 분할 플랫폼입니다. Chainlink 기반 감정 평가, 하이브리드 RWA Vault 및 veRAAC 거버넌스를 결합하여 온체인 청산 메커니즘을 통해 부동산 자산에 대한 프로그래밍 가능한 부채 포지션을 가능하게 합니다.

# 보안 평가 요약

**검토 커밋 해시:**
• [d4f76fb86886d4d73093711d406755493e592b3a](https://github.com/RegnumAurumAcquisitionCorp/contracts/tree/d4f76fb86886d4d73093711d406755493e592b3a)
  (RegnumAurumAcquisitionCorp/contracts)

**수정 검토 커밋 해시:**
• [824afcb349666d90da377c6e3f4748c6606a6bf4](https://github.com/RegnumAurumAcquisitionCorp/contracts/tree/824afcb349666d90da377c6e3f4748c6606a6bf4)
  (RegnumAurumAcquisitionCorp/contracts)

# 범위

- `ERC20AssetAdapter.sol`
- `ERC721AssetAdapter.sol`
- `LendingPool.sol`
- `LendingPoolStorage.sol`
- `LiquidationProxy.sol`
- `VaultProxy.sol`
- `LiquidationStrategyProxy.sol`
- `LiquidationSwap.sol`
- `StabilityPool.sol`
- `StabilityPoolStorage.sol`
- `ComplianceRegistry.sol`
- `RAACHousePrices.sol`
- `WithCompliance.sol`
- `DebtToken.sol`
- `DEToken.sol`
- `RAACNFT.sol`
- `RToken.sol`
- `RWAIndexToken.sol`
- `ERC20VaultAdapter.sol`
- `ERC721VaultAdapter.sol`
- `RAACNFTVaultAdapter.sol`
- `RAACNFTVaultAdapterV2.sol`
- `RWAVault.sol`
- `PercentageMath.sol`
- `TimeWeightedAverage.sol`
- `WadRayMath.sol`
- `ReserveLibrary.sol`
- `StringUtils.sol`

# 발견 사항

# [H-01] 매개변수 변경으로 인한 getter 왜곡

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

이자 발생은 항상 준비금 상태를 업데이트하기 위해 마지막으로 계산된 변수를 사용하여 계산됩니다. 예를 들어, 이자가 마지막으로 5%였고 1시간 후에 `deposit` 함수를 호출하면 지난 1시간 동안 이 5% 이자를 사용합니다. 그러나 `getNormalizedIncome` 및 `getNormalizedDebt` 함수는 이러한 비율을 다시 계산하여 그에 따라 값을 반환합니다.

이로 인해 이러한 함수에서 잘못된 값이 반환됩니다. `getNormalizedIncome` 및 `getNormalizedDebt`는 코드베이스의 많은 중요한 지점에서 사용되기 때문에 매우 중요합니다.

```solidity
// Reserve update

        reserve.liquidityIndex = calculateLiquidityIndex( 
            rateData.currentLiquidityRate,
            timeDelta,
            reserve.liquidityIndex
        );

        // Update usage index (debt index) using compounded interest
        reserve.usageIndex = calculateUsageIndex(
            rateData.currentUsageRate,
            timeDelta,
            reserve.usageIndex
        );
```

```solidity
// Normalized income

    function getLiquidityIndex(ReserveData storage reserve, ReserveRateData storage rateData) internal view returns (uint256) {
        uint256 timeDelta = block.timestamp - uint256(reserve.lastUpdateTimestamp);
        if(timeDelta < 1) {
            return reserve.liquidityIndex;
        }

        return calculateLiquidityIndex(
            calculateLiquidityRate(rateData.currentUtilizationRate, rateData.currentUsageRate, rateData.protocolFeeRate, reserve.totalUsage),
            timeDelta,
            reserve.liquidityIndex
        );
    }
```

보시다시피 여기에서 유동성 비율을 다시 계산합니다. `protocolFeeRate`만 변경될 수 있으며 여기서 다른 실제 값을 가질 수 있습니다. 즉, 프로토콜이 프로토콜 수수료를 변경하면 정규화된 소득에 대해 잘못된 값을 반환합니다.

> 참고: 다른 매개변수 변경에 대해 부채에서도 동일한 상황이 발생합니다.

## 권장 사항

`getNormalizedIncome` 및 `getNormalizedDebt`에서 유동성 비율이나 사용 비율을 계산하지 말고 캐시된 버전을 사용하십시오.

# [H-02] 차용인이 대출자에 대한 이자 지불을 피할 수 있음

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

차용인의 총 부채 가치를 확인할 때 `_positionScaledDebt` 내부 함수를 사용합니다. 다음 공식을 사용합니다.

`rawDebtBalance * usageIndex / positionIndex`

그러나 `borrow` 함수에서 `positionIndex`를 새 값으로 업데이트하는 동안 `rawDebtBalance`를 올바르게 증가시키지 않습니다.

```solidity
(, uint256 underlyingAmount, , ) = IDebtToken(reserve.reserveDebtTokenAddress).mint(msg.sender, msg.sender, amount, reserve.usageIndex, abi.encode(adapter, data));

        // We need to update the position index of the user
        position.positionIndex = reserve.usageIndex;

        // Transfer borrowed amount to user
        IRToken(reserve.reserveRTokenAddress).transferAsset(msg.sender, amount);

@>      position.rawDebtBalance += underlyingAmount; 
```

여기서 기초 금액은 차용 금액과 같습니다. `debtToken.mint()` 호출에서 차용인에게 차용인 이자를 민팅하지만, 이 증가는 mint 함수의 3번째 매개변수로 반환되며 `rawDebtBalance`를 업데이트할 때 사용하지 않습니다. 또한 포지션 인덱스를 마지막으로 업데이트된 값으로 업데이트하므로 다음 표현식은 1과 같습니다.

`usageIndex / positionIndex`

올바른 부채 잔액을 반환하려면 `rawDebtBalance`도 이자를 고려해야 합니다.

이 상황에서 차용인은 X 금액의 crvUSD를 빌린 다음 20 crvUSD를 빌리고 즉시 20 crvUSD를 상환할 수 있습니다. 이 방법을 사용하면 이자의 100%를 우회할 수 있습니다.

> 참고: 부채 토큰은 이자를 올바르게 민팅하므로 부채 토큰의 잔액과 `_positionScaledDebt` 사이에 차이가 있습니다.

## 권장 사항

`rawDebtBalance`에도 이자 증가를 추가하십시오.

# [M-01] 인출 시 낮아진 주가 기준선으로 인한 수익 이중 계산

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`VaultStrategy` 계약에서 수익은 `_harvestYield()`에서 다음과 같이 계산되며, `lastSharePrice`는 `currentSharePrice`가 마지막으로 기록된 `lastSharePrice`보다 큰 경우에만 업데이트됩니다.

```solidity
function _harvestYield() internal {
        uint256 currentSharePrice = _getPricePerShare();

        if (vaultRewards.lastSharePrice == 0 || currentSharePrice < vaultRewards.lastSharePrice) {
            // Initialize on first deposit
            if (vaultRewards.lastSharePrice == 0) {
                vaultRewards.lastSharePrice = currentSharePrice;
            }
            return;
        }

        uint256 priceDifference = currentSharePrice - vaultRewards.lastSharePrice;
>>      uint256 totalYield = (vaultRewards.totalShares * priceDifference) / 1e18;

        vaultRewards.pendingRewards += totalYield;
>>      vaultRewards.lastSharePrice = currentSharePrice; // @note : will update with higher price

        //...
    }
```

그러나 `withdrawFromVault()` 함수는 `currentSharePrice`가 이전에 기록된 `lastSharePrice` 아래로 떨어지면 `lastSharePrice`를 하향 업데이트합니다. `lastSharePrice`는 **최고 수위 표시(high-water mark)** 역할을 해야 하기 때문에 문제가 발생합니다. 이를 낮추면 볼트가 **손실 회복**을 **새로운 수익**으로 잘못 인식하여 사실상 수익이라는 이름으로 원금을 인출하게 됩니다.

```solidity
function withdrawFromVault(uint256 amount) public onlyProxy {
        uint256 maxWithdrawable = _maxWithdraw(address(this));
        if (maxWithdrawable == 0) return;

        uint256 currentSharePrice = _getPricePerShare();

        uint256 totalYield = 0;
        uint256 priceDifference = 0;
        if (vaultRewards.lastSharePrice > 0 && currentSharePrice > vaultRewards.lastSharePrice) {
            priceDifference = currentSharePrice - vaultRewards.lastSharePrice;
            totalYield = (vaultRewards.totalShares * priceDifference) / 1e18;
        }

        vaultRewards.pendingRewards += totalYield;
>>      vaultRewards.lastSharePrice = currentSharePrice; // @audit : will update with lower price

       //...
    }
```

예시:

1. 볼트가 주가 **1.0**에 **1000 USDC**를 예치 → **1000주**를 받음.
   `lastSharePrice = 1.0`

2. 볼트가 주가 **1.2**로 성장 → 발생 수익 = **200 USDC**.
   `_harvestYield()`는 이 수익을 올바르게 인식합니다.

3. 시장 하락 → 주가가 **0.8**로 떨어짐.
   `withdrawFromVault()`가 `lastSharePrice = 0.8`로 재설정함.

4. 시장 회복 → 주가가 **1.2**로 돌아옴.
   `_harvestYield()`는 수익을 `(1.2 – 0.8) * 1000 = 400 USDC`로 계산합니다.
   그러나 200의 이전에 발생한 수익은 주가가 1.2일 때 이미 수확되었으므로 실제 수익은 400이 아니라 0입니다. 이 추가 400은 볼트의 원금(`totalDeposits`)에서 인출됩니다.

이 이중 계산은 가격이 하락 후 회복될 때마다 발생하여 원금 자산을 수익으로 인출하고 초과 수익을 수수료 징수자에게 불공정하게 할당하게 됩니다. 또한 `totalDeposits`가 더 이상 `totalShares`로 표시되는 원금과 일치하지 않으므로 `withdrawAll()`을 통해 볼트를 제거할 수 없게 됩니다.

## 권장 사항

`withdrawFromVault()` 함수를 수정하여 `currentPrice > lastSharePrice`일 때만 `lastSharePrice`를 업데이트하도록 하여 `_harvestYield()`가 **새로운 수익**만 고려하고 수익 수확 시 원금에서 인출하는 것을 방지하십시오.

# [M-02] 동일 블록 입금 확인이 청산 중 `StabilityPool` 인출을 차단함

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`StabilityPool`이 사용자 포지션을 청산할 때 청산을 충당하기에 충분한 `crvUSDToken`이 부족할 수 있습니다. 이러한 경우 `LendingPool`에서 부족분을 인출하여 해당 `rToken`을 소각하고 기본 `crvUSD`를 `StabilityPool`로 전송합니다.

```solidity
// LiquidationStrategyProxy contract:
function liquidateBorrower(address poolAdapter, address vaultAdapter, address user, bytes calldata data, uint256 minSharesOut) external onlyProxy {
        //...

        uint256 scaledPositionDebt = lendingPool.getPositionScaledDebt(poolAdapter, user, data);
        uint256 initialCRVUSDBalance = crvUSDToken.balanceOf(address(this));
        uint256 availableRTokens = rToken.balanceOf(address(this));

        // We need to get the amount of rToken that is needed to cover the debt, or 0 if the debt is covered
        uint256 rTokenAmountRequired = initialCRVUSDBalance >= scaledPositionDebt ? 0 : scaledPositionDebt - initialCRVUSDBalance;
        if (availableRTokens < rTokenAmountRequired) revert InsufficientBalance();

        // We unwind the position
        if (rTokenAmountRequired > 0) {
>>           lendingPool.withdraw(rTokenAmountRequired);
        }
        //...
}
```

청산이 끝나면 남은 `crvUSDToken`(예: `iRAAC`에서 스왑됨)은 다시 `LendingPool`에 예치됩니다. 이 예치는 `depositBlock[StabilityPool] = block.number`를 설정합니다.

```solidity
// LiquidationStrategyProxy contract
    function liquidateBorrower(address poolAdapter, address vaultAdapter, address user, bytes calldata data, uint256 minSharesOut) external onlyProxy {
    //...

        _handleLiquidation(poolAdapter, vaultAdapter, data, minSharesOut);

        // Deposit crvUSD back to get rTokens (including the excess)
        // Get the final crvUSD balance after the exchange
        uint256 finalCRVUSDBalance = crvUSDToken.balanceOf(address(this));
        if (finalCRVUSDBalance > 0) {
            // Approve lending pool to take crvUSD for deposit
            bool approveCRVUSDDeposit = crvUSDToken.approve(address(lendingPool), finalCRVUSDBalance);
            if (!approveCRVUSDDeposit) revert ApprovalFailed();
>>           lendingPool.deposit(finalCRVUSDBalance);
        }
        emit BorrowerLiquidated(user, IAssetAdapter(poolAdapter).getAssetToken(), data, scaledPositionDebt);
    }
```

```solidity
// LendingPool contract:
 function deposit(uint256 amount) external nonReentrant whenNotPaused onlyValidAmount(amount) notBlacklisted(msg.sender) {
        //...

>>       depositBlock[msg.sender] = block.number;

        //...
    }
```

다른 청산이 동일한 블록 내에서 발생하고 다시 `LendingPool`에서의 인출이 필요한 경우, `StabilityPool`에서 `LendingPool`로 `rToken`을 전송하려고 할 때 `RToken._update()`의 다음 확인으로 인해 `LendingPool.withdraw()` 호출이 되돌려집니다.

```solidity
 function _update(address from, address to, uint256 amount) internal override {
        //...
        if (ILendingPool(_lendingPool).isUserDepositInSameBlock(from)) revert CannotDepositAndTransferInSameBlock();
        //...
    }
```

이로 인해 청산이 동일한 블록 내에서 다른 청산을 완료하지 못하게 되어 청산 실패 및 발생한 이자로 인한 청산인의 비용 증가로 이어집니다.

## 권장 사항

`StabilityPool` 주소를 블록 기반 동일 블록 입금 확인에서 제외하십시오.

```diff
// LendingPool contract:
  function deposit(uint256 amount) external nonReentrant whenNotPaused onlyValidAmount(amount) notBlacklisted(msg.sender) {
         //...
 
-       depositBlock[msg.sender] = block.number;
+       if (msg.sender != stabilityPool) depositBlock[msg.sender] = block.number;
 
         //...
     }
```

# [M-03] 프로토콜이 차입 포지션에서 수수료를 놓침

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

프로토콜은 유동성 공급자를 위한 이자율 계산 중에 항상 프로토콜 수수료율을 뺍니다. 그러나 이 자금을 민팅하거나 할당하지 않습니다. 이는 차용인도 이 수수료를 지불하지만 대출자를 포함하여 아무도 이 자금을 받지 못한다는 것을 의미합니다.

즉, 모든 차용인이 포지션에 대해 지불하고 대출자가 자금의 100%를 인출하더라도 시스템에는 자금이 남게 됩니다.

```solidity
    function calculateLiquidityRate(uint256 utilizationRate, uint256 usageRate, uint256 protocolFeeRate, uint256 totalDebt) internal pure returns (uint256) {
        if (totalDebt < 1) {
            return 0;
        }

        uint256 grossLiquidityRate = utilizationRate.rayMul(usageRate);
        uint256 protocolFeeAmount = grossLiquidityRate.rayMul(protocolFeeRate);
@>      uint256 netLiquidityRate = grossLiquidityRate - protocolFeeAmount;

        return netLiquidityRate;
    }
```

## 권장 사항

차입 포지션에서 수수료를 모으기 위해 이 자금을 수수료 수령자에게 할당/민팅하십시오.

# [L-01] 하드코딩된 풀 코인 인덱스로 인한 잘못된 스왑/되돌리기 위험

_해결됨_

`_swap`은 하드코딩된 인덱스 `(1, 0)`으로 Curve 풀을 호출합니다.

```solidity
// Exchange index token (1) for crvUSD (0) since we swapped the order in the pool
liquidityPool.exchange(1, 0, amount, minDy, address(this)); // hardcoded indices
```

이는 단일 풀과 고정된 코인 순서를 가정합니다. 풀이 교체되거나 코인 순서가 다른 경우 승인 및 `exchange()` 인덱스가 실제 토큰과 일치하지 않아 **되돌리기 또는 의도하지 않은 스왑**이 발생합니다. 또한 `minDy`는 풀 자체의 견적 대신 `pricePerShare()`에서 파생되므로 슬리피지/가격 책정 오류 위험이 증가합니다.

# [L-02] 구현 계약에 `_disableInitializers()` 누락

_해결됨_

`StabilityPool` 구현 계약은 `Initializable`을 상속하지만 생성자는 `_disableInitializers()`를 호출하지 않습니다. 즉, 프록시 없이 배포되는 경우 로직 계약이 임의의 매개변수로 직접 초기화될 수 있으며, 이는 권장되는 OpenZeppelin 업그레이드 가능 계약 패턴에서 벗어납니다. 이것이 프록시의 저장소/소유권을 손상시키지는 않지만, 구현 계약과 직접 상호 작용하는 경우 혼란이나 오용을 일으킬 수 있습니다.

구현 계약의 생성자에서 `_disableInitializers();`를 호출하여 로직 계약을 명시적으로 잠그고 사용자 지정 매개변수로 직접 초기화되지 않도록 하십시오. 이렇게 하면 프록시만 적절하게 초기화될 수 있습니다.

# [L-03] `stringToUint("")`가 되돌리는 대신 `0`을 반환함

_해결됨_

`StringUtils.stringToUint`의 현재 구현은 빈 문자열(`""`)이 주어지면 `0`을 반환합니다. 이는 잘못된 형식이거나 누락된 입력을 가릴 수 있습니다. 예를 들어 `_processRequest`에서 `args[2]`가 빈 문자열일 수 있어 `houseId`가 `0`이 될 수 있습니다. `0`이 유효한 `houseId`가 아닌 경우 이는 잘못된 데이터를 조용히 전달하고 논리 오류로 이어질 수 있습니다.

```solidity
function stringToUint(string memory s) internal pure returns (uint256) {
    bytes memory b = bytes(s);
    uint256 result;
    for (uint256 i; i < b.length; i++) {
        uint8 digit = uint8(b[i]) - 48;
        if (digit > 9) revert NonNumericCharacter();
        result = result * 10 + digit;
    }
    return result; // "" -> 0
}
```

`LendingPool.sol`에서의 사용:

```solidity
uint256 houseId = args[2].stringToUint(); // "" becomes 0
```

**권장 사항**

조용한 강제 변환을 피하기 위해 `stringToUint`가 빈 문자열에서 되돌리도록 만드십시오.

# [L-04] `unregisterAdapter()`가 더스트 예치로 인해 DoS될 수 있음

_해결됨_

`RWAVault.unregisterAdapter()` 함수는 계약 소유자가 지원 목록에서 기존 어댑터를 제거하기 위한 것입니다. 제거하기 전에 `IVaultAssetAdapter(adapter).totalValue() == 0`인지 확인하여 어댑터에 자산이 남아 있지 않은지 확인합니다. 그러나 이 로직은 잠재적인 DoS 벡터를 도입합니다. 공격자(또는 일반 사용자)가 어댑터에 더스트 양을 예치하여 소유자가 등록을 취소하지 못하게 할 수 있습니다. 최소한의 0이 아닌 잔액이라도 프로세스를 차단하므로 어댑터가 시스템에 무기한 갇힐 수 있습니다.

```solidity
    function unregisterAdapter(address adapter) external onlyOwner {
        require(supportedAdapters[adapter], "RWAVAult: not supported");
>>      if (IVaultAssetAdapter(adapter).totalValue() > 0) revert("RWAVault: adapter has value");
        //...
    }
```

권장 사항:
더스트 예치를 처리하기 위한 강제 제거 메커니즘을 도입하십시오.

# [L-05] `redeemNFT` / `withdraw` 흐름의 읽기 전용 재진입

_해결됨_

`redeemNFT` 프로세스는 내부적으로 `super.withdraw`를 호출하는 `RAACNFTVaultAdapterV2.withdraw`에 의존합니다.
`super.withdraw`는 ERC721 `safeTransfer`를 수행하므로 어댑터가 내부 회계(`nftsValue`)를 업데이트하기 전에 수신자의 `onERC721Received`에 대한 **외부 호출**을 트리거합니다.

```solidity
value = super.withdraw(data, to); // external call → onERC721Received
(uint256 usdValue,) = IRAACHousePrices(address(priceOracle)).getRawPrice(tokenId);
if (nftsValue < usdValue) {
    nftsValue = 0;
} else {
    nftsValue -= usdValue;
}
```

`onERC721Received` 콜백 동안 악성 수신자는 `totalAssets()`, `convertToShares()` 또는 `pricePerShare()`와 같은 뷰 함수를 호출할 수 있습니다. `nftsValue`가 아직 감소되지 않았으므로 이러한 함수는 **부풀려진 자산 가치**를 반환하여 *읽기 전용 재진입*을 가능하게 합니다.
공격자는 동일한 트랜잭션 내에서 이를 사용하여 이러한 뷰를 신뢰하는 다른 프로토콜의 평가를 조작할 수 있습니다(예: 과도한 민팅, 불공정 스왑 또는 부풀려진 담보에 대한 차입).

**권장 사항**

1. **CEI(Checks-Effects-Interactions) 패턴 적용**:
   재진입이 발생하더라도 회계가 일관되도록 `super.withdraw`를 호출하기 **전**에 `nftsValue`를 업데이트하십시오.

2. **읽기 함수 보호**:
   민감한 뷰 함수(`totalAssets`, `pricePerShare`, `convertToShares`)에 가벼운 `nonReentrantView` 수정자를 추가하여 외부 전송/민팅 중 호출을 차단하십시오.

# [L-06] 더스트 차입이 최소 차입 제약을 우회함

_해결됨_

프로토콜은 `_validateBorrow`에서 최소 차입 금액(`MIN_BORROW_AMOUNT`)을 시행하지만, 차용인은 부채를 부분적으로 상환하고 최소 차입 임계값보다 작은 **더스트 크기의 남은 부분**을 남겨 이 제약을 우회할 수 있습니다.

구체적으로:

* `repay(...)`(차용인이 호출)에는 최소 상환 요건이 없습니다. `amount > 0`이면 수락됩니다.
* `repayOnBehalf(...)`는 1%의 최소 상환을 시행하지만 이는 제3자가 상환하는 경우에만 적용됩니다.
* 결과적으로 차용인은 `MIN_BORROW_AMOUNT` 이상의 유효한 대출을 받은 다음 즉시 대부분을 상환하고 작은 잔존 부채(`debt < MIN_BORROW_AMOUNT`)를 남길 수 있습니다.

이로 인해 직접적인 금전적 손실이 발생하지는 않지만 최소 차입 규칙의 의도를 훼손하고 프로토콜 운영에 부정적인 영향을 미칠 수 있습니다.

**권장 사항**

**`MIN_BORROW_AMOUNT`보다 작은 잔존 부채가 상환 후에 남을 수 없음**을 규칙으로 시행하십시오.

# [L-07] 체인 간에 Curve `price_oracle` 인덱스를 하드 코딩하면 잘못된 가격이 산출됨

_해결됨_

오라클 폴백은 현재 ETH/crvUSD에 대해 고정된 Curve `price_oracle` 인덱스를 가정합니다(예: `price_oracle(0)`을 호출하고 "crvUSD 단위의 WETH"가 될 것으로 예상).
체인과 풀 전반에 걸쳐 **토큰 순서가 다르므로** 동일한 인덱스가 다른 자산을 참조하여 **잘못되거나 반전된 가격**을 생성할 수 있습니다. 이는 `crvUSDPriceInUSD`로 전파되어 LTV 확인, 청산 및 회로 차단기 로직에 영향을 미칩니다.

직면할 수 있는 구체적인 차이점:

* **TriCrypto/crypto v2** 풀: `price_oracle(k)`는 `coin[0]` **단위의** `coin[k+1]`의 EMA 가격을 반환합니다. `coin[0] = crvUSD`인 경우:

  * `coin[1] = WETH`이면 → **`k = 0`**을 사용합니다.
  * `coin[2] = WETH`이면 → **`k = 1`**을 사용합니다.

체인 간 차이점의 예:

* 이더리움 메인넷 TriCRV 순서 `crvUSD, WETH, CRV` → **K = 0**
* 옵티미즘 TriCrypto-crvUSD 순서 `crvUSD, WBTC, WETH` → **K = 1**
* 베이스 `crvUSD/tBTC/WETH` 순서 `crvUSD, tBTC, WETH` → **K = 1**

"ETH"는 체인별로 주소가 다른 체인별 **WETH**를 의미하므로 고정 인덱스에 의존하는 것은 안전하지 않습니다.

**권장 사항**

소유자가 설정한 배포 시 불변 인덱스 K를 채택하십시오.
`uint8 public immutable K;`를 추가하고 **배포 시 한 번** 설정하십시오.

```solidity
    uint8 public immutable K;  // 0 or 1 for TriCrypto (coin[K+1] vs coin[0])

    constructor(address initialOwner, uint8 _k) Ownable(initialOwner) {
        require(_k <= 1, "invalid K"); // TriCrypto supports k in {0,1}
        K = _k;
        fallbackPriceInUSD = 1e18;
        lastFallbackUpdateTimestamp = block.timestamp;
    }

    function _crvUSDPriceInUSDFromCurve() internal view returns (uint256 crvUSDPriceInUSD, uint256 lastUpdateTimestamp, bool success) {
<...>
        uint256 ETHPriceInCrvUSD = curveOracle.price_oracle(K);
```

# [L-08] 모든 사용자가 사용 중단된 ERC-721 어댑터에서 상환할 수 있음

_해결됨_

볼트는 `supportedRedeemableERC721Adapters` 및 `redeemableERC721Adapters` 배열을 통해 상환 가능한 ERC-721 어댑터에 대한 화이트리스트를 유지 관리하며, 등록은 `registerRedeemableERC721Adapter`에 의해 제어되고 제거는 `unregisterRedeemableERC721Adapter`에 의해 제어됩니다. `onlySupportedDepositableAdapter`에 의해 명시적으로 제한되는 입금과 달리 상환 경로에는 동등한 제한이 없습니다. 사용자 대면 `redeemNFT(address _adapter, uint256 _tokenId, uint256 maxSharesBurn)` 함수는 `supportedRedeemableERC721Adapters[_adapter]`를 확인하지 않으며, 호출 시 여전히 지원되는지 확인하지 않고 `redeemableERC721Adapters` 배열에서 어댑터를 선택하는 `getNextRandomNFT()`에만 의존합니다.

이와 별도로 `unregisterAdapter(address adapter)`는 상환 가능 배열에서 제거하지 않고 볼트에 대해 어댑터를 미지원(`supportedAdapters[adapter] = false`)으로 표시할 수 있습니다. 이는 상환 흐름이 `supportedRedeemableERC721Adapters` 매핑을 참조하지 않거나 어댑터가 현재 지원되는지 시행하지 않기 때문에 어댑터가 볼트 수준에서 사용 중단되었지만 상환을 위해 선택 가능한 상태로 유지되는 비동기화 위험을 생성합니다. 이러한 상태에서 `redeemNFT`에 대한 호출은 거버넌스가 비활성화하려고 의도한 어댑터를 통해 계속 인출하거나, 사용 중단된 어댑터가 동작을 변경하면 예측할 수 없이 되돌려 서비스 거부 위험을 초래할 수 있습니다.

**권장 사항**

`redeemNFT`에서 지분 소각 및 인출을 진행하기 전에 `supportedRedeemableERC721Adapters[adapter]`를 확인하여 선택한 어댑터가 현재 상환 허용되었는지 검증하십시오.

# [L-09] 자산이 부채를 초과할 때 `rToken` 잉여분이 `StabilityPool`에 갇힘

_해결됨_

청산 중에 전략은 차용인의 부채를 `crvUSD`로 지불하고 **남은 `crvUSD`를 LendingPool에 다시 예치**하여 **StabilityPool에 `rToken`을 추가로** 민팅합니다.

```solidity
// LiquidationStrategyProxy (simplified)
uint256 finalCRVUSDBalance = crvUSDToken.balanceOf(address(this));
if (finalCRVUSDBalance > 0) {
    lendingPool.deposit(finalCRVUSDBalance); // mints rToken to StabilityPool
}
```

StabilityPool의 사용자 인출은 항상 **정확히 사용자의 스케일링된 금액**을 전송하고 잉여분을 비례 배분하지 **않습니다**.

```solidity
// StabilityPool.withdraw(...)
deToken.burn(msg.sender, scaledAmount);
rToken.safeTransfer(msg.sender, scaledAmount); // exactly "scaledAmount", no share of surplus
```

`DEToken`은 풀 지분이 아니라 **인덱스 표시**입니다. 잔액(및 총 공급량)은 `rToken.balanceOf(StabilityPool)`와 **무관하게** 원시 예치금 × 인덱스에서 파생됩니다.
따라서 청산으로 인해 다시 예치되는 추가 `crvUSD`가 남는 경우 풀의 **자산**이 DEToken 보유자에 대한 **부채**를 초과할 수 있습니다.

* **자산 (스케일링됨):** `A = rToken.balanceOf(StabilityPool)`
* **부채 (스케일링됨):** `L = deToken.getRawTotalDeposits() * getNormalizedIncome()`

`A > L`이면 차이 `A - L`은 다음과 같은 **잉여분**입니다.

* DEToken 보유자에게 **배분할 수 없음** (PPS/볼트 수학 없음, 잉여 인덱스 없음)
* 사용자가 인출할 수 없음 (소각할 일치하는 DEToken 없음)
* 현재 코드에 **관리자 스윕(admin sweep)**이 없음 (따라서 사실상 계약에 무기한 방치됨)
* 향후 청산에 의해서만 기회주의적으로 소비됨

이로 인해 **자본 잠금** 및 잘못된 회계 위험(명시적 정책 없이 주차된 자산)이 발생하고 회계/지표(TVL 대 청구 가능)를 혼란스럽게 할 수 있습니다.

**권장 사항**

**A. 잉여분 증가를 방지하기 위해 청산 후 재예치를 제한합니다.**
남은 `crvUSD`를 예치하기 전에 풀이 목표(부채 또는 부채 + 작은 버퍼)를 초과하지 않고 수락할 수 있는 양(스케일링된 용어)을 계산하십시오.

**B. 명시적인 `sweepSurplus()` 관리자 함수(이벤트 및 정책 포함)를 추가합니다.**
*초과분*만 안전하게 이동할 수 있도록 허용하십시오.

# [L-10] 백분율 기반 차입 한도 누락으로 모든 청산 조치가 DoS될 수 있음

_해결됨_

현재 시스템에는 백분율 기반 차입 한도가 없습니다. 이는 일반적인 한도 유형이 아니며 많은 프로토콜에 필요하지 않지만, 고유한 청산 설계로 인해 RAAC에는 필요합니다.

RAAC 청산 설계에서 부채는 대출자에 의해 커버되며, 이는 독특한 방식입니다. Stability 풀은 RToken을 보유하고 청산 시 이 RToken은 Lending Pool의 `withdraw` 함수를 호출하여 crvUSD로 변환됩니다.

```solidity
        uint256 rTokenAmountRequired = initialCRVUSDBalance >= scaledPositionDebt ? 0 : scaledPositionDebt - initialCRVUSDBalance;
        if (availableRTokens < rTokenAmountRequired) revert InsufficientBalance(); 

        // We unwind the position
        if (rTokenAmountRequired > 0) {
@>          lendingPool.withdraw(rTokenAmountRequired);
        }
```

이 설계는 차입 활용률이 100%에 도달하면 유동성 부족으로 인해 `withdraw` 호출이 되돌려지기 때문에 작동할 수 없습니다. 자금의 100%가 차용인에게 빌려졌고 이 경우 어떤 포지션도 청산할 방법이 없습니다.

차용인은 Lending Pool의 자금을 100% 지속적으로 빌려 청산을 피할 수 있으며, 청산이 거의 불가능하기 때문에 갚을 필요도 없습니다.

> 사실 프로토콜은 crvUSD 토큰을 Stability Pool로 직접 전송하여 포지션을 청산한 다음 청산할 수 있습니다. 그러나 이러한 crvUSD는 프로토콜 측에서 손실되며 어쨌든 자금 손실을 초래합니다.

이러한 이유로 백분율 기반 차입 한도는 현재 RAAC 시스템 설계에 매우 중요합니다.

**권장 사항**

80%와 같은 백분율 기반 차입 한도 적용을 고려하십시오.

> 참고: 이 한도에는 장단점이 있습니다. 풀에 너무 많은 갭을 남겨두면 Lending Pool의 이자율이 낮아집니다. 그러나 이 갭에 대해 정말 작은 값을 선택하면 큰 포지션을 청산할 수 없습니다.
