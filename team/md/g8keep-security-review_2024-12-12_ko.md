# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**g8keep/audit-v2** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# g8keep 소개 (About g8keep)

g8keep 계약은 본딩 커브를 사용하여 토큰 판매를 용이하게 하며, 페널티 관련 토큰 가격이 포함된 2차 단계로 전환되는 초기 유동성 설정으로 시작하여, 외부 조작을 방지하기 위해 시작 시 Uniswap V3 풀을 초기화합니다. 계약은 유동성 및 토큰 볼륨에 대한 엄격을 조건을 유지하고 스크립트화된 마이그레이션을 사용하여 정확한 가격 책정을 보장합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 희박하거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [7ed875cf8104c6267be505ca8c31372448a6f16e](https://github.com/g8keep/audit-v2/tree/7ed875cf8104c6267be505ca8c31372448a6f16e)**

**_수정 검토 커밋 해시_ - [7759b78293329222f64a196d332f5d185e74310e](https://github.com/g8keep/audit-v2/tree/7759b78293329222f64a196d332f5d185e74310e)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Tickmath`
- `g8keepBondingCurve`
- `g8keepBondingCurveCode`
- `g8keepBondingCurveFactory`
- `g8keepBondingCurveFactoryConfiguration`
- `g8keepLiquidityLocker`
- `g8keepLockerFactory`

# 발견 사항 (Findings)

# [H-01] 목표 가격에 도달하기 위해 1 wei의 토큰을 교환하려고 할 때 마이그레이션이 실패함

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

코드가 Uniswap V3 풀로 토큰을 마이그레이션하려고 할 때, 먼저 `_adjustPoolPrice()`에서 임시 유동성을 추가하고 스왑을 수행하여 풀의 가격을 변경하려고 시도합니다:

```solidity
       uint256 tmpLPTokenId =
            _addTemporaryLiquidity(ethAmount / 100, tokenAmount / 100, constainedTickLower, constrainedTickUpper);

        if (sqrtPriceX96Start > sqrtPriceX96) {
            IUniswapV3Pool(poolAddress).swap(address(this), true, int256(uint256(tokenAmount)), sqrtPriceX96, "");
        } else {
            IUniswapV3Pool(poolAddress).swap(address(this), false, int256(uint256(1)), sqrtPriceX96, "");
        }
----snip
        (uint160 sqrtPriceX96New,,,,,,) = IUniswapV3Pool(poolAddress).slot0();
        adjustFailed = sqrtPriceX96 != sqrtPriceX96New;
```

그리고 풀의 가격이 목표 가격에 도달하지 않으면 코드는 마이그레이션하지 않습니다. 문제는 코드가 1 wei의 ETH로 스왑하려고 시도하며 이는 목표 가격에 도달하기에 충분하지 않을 것이라는 점입니다. 가격은 `constrainedTickUpper`에만 도달할 것이며, 결과적으로 `adjustFailed`가 true가 되어 마이그레이션을 완료할 수 없고 항상 되돌려지게(revert) 됩니다.

## 권장 사항

목표 가격 수준에 도달하는 데 필요한 정확한 금액을 계산하여 스왑에 사용하고, 또한 풀의 가격과 목표 가격을 확인할 때 일정 비율의 오차를 허용하십시오.

# [H-02] 잘못된 준비금 할당으로 인한 준비금 불일치 및 자금 관리 부실

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`g8keepBondingCurve::_handleMigrationFailed` 함수에서 실패한 마이그레이션을 처리할 때 ETH 및 토큰에 대한 준비금이 잘못 할당됩니다. `tokenAmount`는 기본 토큰(ETH)을 위한 `bReserve.reserve0`에 잘못 할당되고, `ethAmount`는 토큰 금액을 보유해야 하는 `bReserve.reserve1`에 할당됩니다.

```solidity
File: g8keepBondingCurve.sol
942:     function _handleMigrationFailed(uint256 ethAmount, uint112 tokenAmount) internal {
943:         curveStatus = curveStatus | STATUS_MIGRATION_FAILED_FLAG;
944:         WETH.withdraw(WETH.balanceOf(address(this)));
945:
946:@>>      bReserve.reserve0 = tokenAmount;
947:@>>      bReserve.reserve1 = uint112(ethAmount);
948:
949:         emit MigrationFailed();
950:     }
```

이 역방향 할당은 잘못된 준비금 계산과 매매 작업 중 불일치로 이어질 수 있습니다. 마이그레이션 실패 후에도 매매가 계속 발생할 수 있으므로 이 문제는 재정적 불일치를 일으키고 거래 조작을 허용할 수 있습니다.

## 권장 사항

`_handleMigrationFailed` 함수의 할당을 업데이트하여 `ethAmount`를 `bReserve.reserve0`에, `tokenAmount`를 `bReserve.reserve1`에 올바르게 할당하도록 하십시오.

# [H-03] 토큰이 마이그레이션되지 않은 동안 UniswapV3에 유동성을 추가할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

코드는 토큰이 아직 마이그레이션되지 않았을 때 UniswapV3 풀로 토큰을 전송하는 것을 허용하지 않으므로 아무도 풀에 유동성을 추가하고 풀을 조작할 수 없습니다. 문제는 이러한 확인을 우회하고 Uniswap 풀에 유동성을 추가하는 것이 가능하다는 것입니다. 공격자는 `buy(to)` 함수를 사용하여 Uniswap V3 풀 주소로 토큰을 구매하고 풀의 토큰 잔액을 늘릴 수 있습니다. Uniswap V3에 유동성을 추가할 때 `uniswapV3MintCallback()`을 호출하고 해당 함수가 풀의 주소로 토큰을 전송할 것으로 예상합니다:

```solidity
        if (amount0 > 0) balance0Before = balance0();
        if (amount1 > 0) balance1Before = balance1();
        IUniswapV3MintCallback(msg.sender).uniswapV3MintCallback(amount0, amount1, data);
        if (amount0 > 0) require(balance0Before.add(amount0) <= balance0(), 'M0');
        if (amount1 > 0) require(balance1Before.add(amount1) <= balance1(), 'M1');
```

공격자의 계약은 `mint()`를 호출하여 풀에 유동성을 추가할 수 있으며 `uniswapV3MintCallback()` 콜백 중에 `buy(pool)`을 호출하여 풀의 잔액을 늘리고 결과적으로 Uniswap V3 풀의 유동성이 증가합니다. 이를 수행함으로써 공격자는 마이그레이션 프로세스를 DOS 공격할 수 있으며 마이그레이션 중 토큰 가격을 조작할 수도 있습니다.

## 권장 사항

풀의 주소로 토큰을 구매하는 것을 허용하지 마십시오.

# [M-01] maxBuyWithoutPenalty에서 잘못된 계산

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`maxBuyWithoutPenalty` 함수는 페널티 없이 구매할 수 있는 토큰의 양을 계산합니다. 그러나 `expectedBalance`를 계산하는 공식이 잘못되었습니다. `TOTAL_SUPPLY` 대신 `SNIPE_PROTECTED_SUPPLY`를 사용해야 합니다.

```solidity
function maxBuyWithoutPenalty() external view returns (uint256) {
    ...
    
    uint256 elapsedSeconds = block.timestamp - GENESIS_TIME;
@>  uint256 expectedBalance = TOTAL_SUPPLY - (TOTAL_SUPPLY * elapsedSeconds) / SNIPE_PROTECTION_SECONDS;

    ...
}
```

## 권장 사항

`TOTAL_SUPPLY` 대신 `SNIPE_PROTECTED_SUPPLY`를 사용하십시오.

```diff
function maxBuyWithoutPenalty() external view returns (uint256) {
    ...
    
    uint256 elapsedSeconds = block.timestamp - GENESIS_TIME;
-   uint256 expectedBalance = TOTAL_SUPPLY - (TOTAL_SUPPLY * elapsedSeconds) / SNIPE_PROTECTION_SECONDS;
+   uint256 expectedBalance = TOTAL_SUPPLY - (SNIPE_PROTECTED_SUPPLY * elapsedSeconds) / SNIPE_PROTECTION_SECONDS;

    ...
}
```

# [M-02] `curveLiquidityMet`가 true일 때 `_curveBuy()`에서 `ethB` 값을 설정해야 함

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`_curveBuy()`에서 `curveLiquidityMet`가 true일 때, 코드는 풀 B에서 모든 토큰을 구매하지만 `ethB` 대신 `ethA`의 값을 설정합니다:

```solidity
    function _curveBuy(uint112 buyValue, bool curveLiquidityMet)
        internal
        returns (uint112 ethA, uint112 ethB, uint112 classA, uint112 classB, bool updatedCurveLiquidityMet)
    {
        updatedCurveLiquidityMet = curveLiquidityMet;
        uint112 remainingBuyValue;

        if (updatedCurveLiquidityMet) {
            remainingBuyValue = buyValue;
            ethA = buyValue;
        } else {
```

`ethB`는 풀 B에 소비된 ETH의 양을 보여주므로 값이 틀리게 되고 이 함수의 반환 값에 의존하는 함수는 제대로 작동하지 않을 것입니다. 동일한 문제가 `_curveBuyCalculation()`에도 존재합니다.

## 권장 사항

`curveLiquidityMet`가 true일 때 `ethB` 값을 설정하십시오.

# [M-03] `forceSafeTransferETH`의 잠재적 오용

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`g8keepBondingCurve` 계약은 보상 메커니즘의 일부로 Ether를 안전하게 전송하기 위해 840행에서 `SafeTransferLib.forceSafeTransferETH` 함수를 활용합니다. 이 함수는 주소로 값을 전송하려고 시도하고 실패하면 잔액을 보유하는 임시 계약을 생성하고 자기 파괴(self-destruction)를 사용하여 ETH를 주소로 강제 전송하는 방식으로 작동하며, 이는 어떤 상황에서도 마이그레이션이 실패하지 않도록 하기 위한 것입니다.

```solidity
File: g8keepBondingCurve.sol
840:         SafeTransferLib.forceSafeTransferETH(DEPLOYER, DEPLOYER_REWARD, 50_000);
```

그러나 `DeployToken` 함수가 `g8keepBondingCurveFactory` 계약에서 호출될 때 `msg.sender`로 설정되는 `DEPLOYER` 주소 결정과 관련하여 잠재적인 문제가 있습니다.

```solidity
File: g8keepBondingCurveFactory.sol
187:                     DEPLOYER: msg.sender,
```

문제는 `deployToken` 함수가 `Multicaller` 또는 중간 계약에 의해 호출될 때 발생합니다. 이러한 경우 중간 계약은 `msg.sender`가 되고 결과적으로 지정된 `DEPLOYER`가 되어 ETH가 계약의 잔액으로 강제 전송됩니다. 이로 인해 의도한 사용자 대신 중간 계약이 Ether 전송을 받습니다. 이는 MEV 봇과 같은 악의적인 행위자가 이 상황을 악용하여 Ether를 탈취하는 경쟁 조건(race condition)으로 이어질 수 있습니다.

또한 토큰이 마이그레이션될 때 Uniswap 포지션은 **DEPLOYER**에게 전송되어 잠재적으로 이 포지션을 잃을 수 있습니다.

```solidity
File: g8keepBondingCurve.sol
837:         UNISWAP_POSITION_MANAGER.approve(address(LOCKER_FACTORY), lpTokenId);
838:         LOCKER_FACTORY.deploy(lpTokenId, DEPLOYER);
```

## 권장 사항

`g8keepBondingCurveFactory::deployToken`을 호출할 때 `msg.sender`를 **DEPLOYER**로 사용하는 대신, 배포자 보상 수령인을 지정하는 매개변수를 추가하여 토큰을 받을 자격이 있는지 확인하십시오.

# [M-04] 마이그레이션 실패 중 유동성 부족

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`reserves0` 또는 기본 토큰이 `MIGRATION_MINIMUM_LIQUIDITY` 값에 도달하고 볼륨이 충족되면 `_migrateToken`을 실행할 수 있습니다:

```solidity
File: g8keepBondingCurve.sol
509:         if (curveLiquidityMet && curveVolumeMet && !migrationFailed) {
510:             _migrateToken(0);
511:         }
```

문제는 마이그레이션이 실패하면 매매가 가능한 상태로 유지된다는 것입니다. `curveLiquidityMet`가 true로 설정된 상태에서 `_curveSell` 함수가 호출되면 판매 처리에 `bReserve`를 활용할 수 있습니다. 이 상황은 판매가 `bReserves` 미만에서 발생하여 유동성 잔액에 잠재적으로 영향을 미치는 시나리오로 이어질 수 있습니다.

```solidity
File: g8keepBondingCurve.sol
708:         if (remainingToSell > 0) {
709:             Reserves storage sellFromReserves = aReserve;
710:@>>          if (curveLiquidityMet) {
711:@>>              sellFromReserves = bReserve;
712:@>>          }
713:             uint112 reserve0 = sellFromReserves.reserve0;
714:             uint112 reserve1 = sellFromReserves.reserve1;
715:
300: 716:             uint112 amountToSell = remainingToSell;
717:             if (classA < amountToSell) {
718:                 revert InsufficientBalance();
719:             } else {
720:                 classA -= amountToSell;
721:             }
722:
723:             uint112 reserve0Out = _getAmountOut(amountToSell, reserve1, reserve0);
724:             amountOut += reserve0Out;
309: 725:@>>          sellFromReserves.reserve0 -= reserve0Out;
726:             sellFromReserves.reserve1 += amountToSell;
727:         }
```

다음 시나리오를 고려해 보십시오:

1. 커브가 구성되어 마이그레이션 준비가 되었지만 오류 또는 조건으로 인해 마이그레이션이 실패합니다.
2. 마이그레이션 실패에 반응한 사용자는 토큰을 판매하여 기본 토큰/ETH 유동성을 필수 `MIGRATION_MINIMUM_LIQUIDITY` 미만으로 줄일 수 있습니다.
3. 그 후 `executeMigration` 함수가 다시 호출되고 이번에는 마이그레이션이 성공합니다. 그러나 사용자 판매로 인한 유동성 감소로 인해 마이그레이션은 처음 의도한 것보다 적은 유동성으로 발생하며 잠재적으로 `MIGRATION_MINIMUM_LIQUIDITY` 미만일 수 있습니다.

이로 인해 예상되는 유동성 보장이 충족되지 않는 손상된 마이그레이션 상태가 발생할 수 있습니다.

## 권장 사항

마이그레이션을 진행하기 전에 유동성 수준이 `MIGRATION_MINIMUM_LIQUIDITY`를 충족하거나 초과하는지 확인하기 위해 `executeMigration` 함수에 추가 확인을 구현하십시오.

# [L-01] 토큰 배포자가 초기 공급을 구매하도록 강제함

토큰 배포자는 초기 공급을 구매하고 싶지 않을 수 있습니다. 그러나 배포 중에 `buy` 함수가 항상 호출되어 구매를 강제합니다.

배포자가 `buy` 함수를 호출할 때 토큰 금액(`msg.value`)을 0으로 설정하면 `g8keepBondingCurve._buy` 함수가 트랜잭션을 되돌리므로 배포자는 토큰을 구매해야 합니다.

```solidity
function deployToken(
    ...
) external payable returns (address _tokenAddress) {
    uint256 bundleBuyAmount = msg.value;
    uint256 _deploymentFee = deploymentFee;
    if (msg.value < _deploymentFee) revert InvalidDeploymentFee();
    if (_deploymentFee > 0) {
        bundleBuyAmount -= _deploymentFee;
        SafeTransferLib.safeTransferETH(g8keepFeeWallet, _deploymentFee);
    }
    if (bundleBuyAmount > maxBundleBuyAmount) {
        revert InvalidBundleBuy();
    }
    ...
@>  g8keepBondingCurve(payable(_tokenAddress)).buy{value: bundleBuyAmount}(msg.sender, 0);
    ...
}
```

또한 `elapsedSeconds`가 0이므로 토큰 배포자는 최대 페널티를 받으므로 대부분의 초기 토큰은 classB로 구매됩니다.

`bundleBuyAmount`가 0보다 클 때만 `buy` 함수를 호출하여 초기 공급을 구매하지 않는 옵션을 제공할 수 있습니다.

# [L-02] `withdrawETH`가 수수료를 부과함

g8keepLiquidityLocker 계약에는 계약에 예치된 토큰을 인출하는 함수가 있습니다. ERC20과 달리 ETH 인출 함수는 베스팅 기간 이후에도 g8keep 수수료를 부과합니다. 계약으로 전송된 토큰은 Uniswap V3 수수료가 아니므로 g8keep 수수료를 부과하는 것은 잘못된 구현으로 보입니다.

`withdrawETH` 함수에서 g8keep 수수료를 제거하여 문제를 해결할 수 있습니다.

# [L-03] 일부 토큰이 토큰 계약에 잠길 수 있음

코드는 `_balances[address(this)].classA`를 사용하여 마이그레이션 및 초과 자금 인출 중 계약의 토큰 잔액을 가져옵니다. 문제는 계약 주소에도 `classB` 잔액이 있을 수 있으며 해당 자금은 마이그레이션에 사용되지 않고 배포자도 인출할 수 없다는 것입니다. `classB` 잔액을 계약 주소로 전송하거나 계약 주소에 대해 토큰을 구매하여 계약 주소의 `classB` 잔액을 늘릴 수 있습니다. 남은 것이 없도록 하기 위해 `balanceOf(This)`를 사용하여 계약의 잔액을 가져오는 것이 좋습니다.

# [L-04] 하나 차이 오류(off by one error)로 인해 STATUS_VOLUME_FLAG가 정확한 순간에 설정되지 않을 수 있음

토큰을 매매할 때마다 `_applyLiquiditySupplement`가 호출되어 `liquiditySupplement` 및 `updatedCurveVolumeMet`을 업데이트합니다. 이 트랜잭션으로 인해 `liquiditySupplement`가 최대값에 도달하면 `STATUS_VOLUME_FLAG` 플래그가 설정됩니다.

비교할 때 `<=` 대신 `<`를 사용합니다. 따라서 `maxSupplementFee == liquiditySupplementFee`일 때 플래그가 설정되지 않습니다. 이로 인해 마이그레이션이 한 번 지연될 수 있습니다.

동일한 문제가 `_applyLiquiditySupplementCalculation` 함수에도 존재합니다.

`maxSupplementFee`와 `liquiditySupplementFee`를 비교할 때 `<` 대신 `<=`를 사용하십시오.

# [L-05] `getCurveStatus`가 잘못된 값을 반환함

함수는 `_getCurveStatus` 함수의 결과를 수신하기 위해 동일한 이름으로 변수를 다시 선언합니다. `getCurveStatus` 함수의 명명된 반환 변수가 업데이트되지 않으므로 올바른 상태를 반환할 수 없습니다.

```solidity
function getCurveStatus() external view returns (
    Reserves memory _aReserve,
    Reserves memory _bReserve,
@>  bool _curveLiquidityMet,
@>  bool _curveVolumeMet,
@>  bool _curveMigrated,
@>  bool _migrationFailed
) {
    _aReserve = aReserve;
    _bReserve = _bReserve;

    (
@>      bool _curveLiquidityMet,
@>      bool _curveVolumeMet,
@>      bool _curveMigrated,
@>      bool _migrationFailed
    ) = _getCurveStatus();
}
```

값을 명명된 반환 변수에 직접 설정하십시오.

```diff
function getCurveStatus() external view returns (
    Reserves memory _aReserve,
    Reserves memory _bReserve,
    bool _curveLiquidityMet,
    bool _curveVolumeMet,
    bool _curveMigrated,
    bool _migrationFailed
) {
    _aReserve = aReserve;
    _bReserve = _bReserve;

    (
-       bool _curveLiquidityMet,
-       bool _curveVolumeMet,
-       bool _curveMigrated,
-       bool _migrationFailed
+       _curveLiquidityMet,
+       _curveVolumeMet,
+       _curveMigrated,
+       _migrationFailed
    ) = _getCurveStatus();
}
```

# [L-06] `_getAmountIn()` 함수는 입력 토큰 금액을 계산할 때 올림해야 함

코드는 `_getAmountIn()` 함수를 사용하여 구매 트랜잭션 중 스왑 결과를 계산합니다:

```solidity
            if (amount1Out > classA) {
                uint112 classACost = _getAmountIn(classA, reserve0, reserve1);
                remainingBuyValue = remainingBuyValue + reserveAIn - classACost;
                reserveAIn = classACost;
                ethA = classACost;
            }
```

문제는 `_getAmountIn()`이 입력 토큰 금액을 계산할 때 사용자에게 유리하게 내림하므로 결과적으로 사용자는 더 적은 토큰을 지불한다는 것입니다. 이는 공격자가 가격을 부풀린 다음 이 반올림 방향 문제를 사용하여 가치를 추출할 수 있는 새로운 공격 벡터를 열 수 있습니다.

`_getAmountIn()`에서 풀에 유리하게 올림하십시오.

# [L-07] 공격자가 프론트러닝으로 토큰 생성을 DOS 공격할 수 있음

사용자가 새 토큰을 생성하려면 팩토리 계약의 `deploy()` 함수를 호출합니다. 문제는 공격자가 프론트러닝으로 토큰 생성을 DOS 공격할 수 있다는 것입니다.

```solidity
        IUniswapV3Factory factory = IUniswapV3Factory(UNISWAP_POSITION_MANAGER.factory());
        if (factory.getPool(address(this), address(WETH), UNISWAP_FEE_TIER) != address(0)) {
            revert PoolAlreadyCreated();
        }
```

공격자는 프론트런하여 토큰 주소에 대한 Uniswap V3 풀을 생성할 수 있습니다. 결과적으로 공격자는 다른 사용자의 배포 트랜잭션을 DOS 공격할 수 있습니다.

이에 대한 쉬운 해결책은 없습니다. 사용자에게 프라이빗 멤풀을 사용하도록 요청하는 것이 좋습니다.

# [L-08] 손상된 `G8KEEP_FEE_WALLET`이 매매 작업을 방해할 수 있음

`g8keepBondingCurve::_applyFee` 함수는 `G8KEEP_FEE_WALLET`으로 수수료를 보내는 역할을 합니다. 이 함수는 매매 작업 중에 호출됩니다.

`G8KEEP_FEE_WALLET`이 손상된 경우 공격자는 `safeTransferETH` 호출이 되돌려지게 하는 높은 가스 소비 작업을 실행하여 이를 악용할 수 있습니다. 이로 인해 매매 작업이 중단되어 공격자가 거래 활동을 조작하고 제어할 수 있게 됩니다.

`G8KEEP_FEE_WALLET`이 손상되더라도 풀이 계속 작동할 수 있도록 `forceSafeTransferETH`를 사용하십시오.

```diff
    function _applyFee(uint112 value) internal returns (uint112 remainingValue) {
        unchecked {
            uint112 fee = value * G8KEEP_FEE / FEE_DENOMINATOR;
-           SafeTransferLib.safeTransferETH(G8KEEP_FEE_WALLET, fee);
+           SafeTransferLib.forceSafeTransferETH(G8KEEP_FEE_WALLET, fee);
            remainingValue = value - fee;
        }
    }
```

# [L-09] 토큰 배포자가 g8keep 수수료를 피할 수 있음

g8keepLiquidityLocker 계약에서 `collectFees`를 호출하면 UniV3에서 얻은 수수료를 징수할 수 있습니다. UniV3 수수료의 n%는 g8keep 수수료로 제공되어야 합니다. 이 함수는 관리자(토큰 배포자)만 호출할 수 있습니다.

그러나 베스팅 기간 후 UniV3 NFT를 관리자(토큰 배포자)에게 반환하는 `release` 함수는 수수료를 정산하지 않습니다. 따라서 토큰 배포자는 `collectFees`를 호출하지 않고 베스팅 기간이 끝날 때까지 기다린 다음 NFT를 돌려받은 후 수수료를 징수하여 g8keep 수수료 지불을 피할 수 있습니다.

`release` 함수에서 `collectFees`를 호출하여 NFT를 반환하기 전에 수수료를 징수하고 분배하십시오.
