# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **gammaswap/v1-vaults** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# GammaSwap Vault 정보 (About GammaSwap Vault)

GammaSwap의 V1-Vaults는 GammaSwap에서 유동성 대출로 포지션을 헤지하여 Uniswap V3 풀에 델타 중립 유동성 전략을 제공합니다. 사용자는 ERC-20 토큰으로 표시되는 금고에 자금을 예치하고, 관리자는 재조정, 입금 및 출금을 감독하여 최적의 유동성을 유지합니다. 헤지에서 발생한 이익이나 손실은 동적으로 조정되고 재투자되어 지정된 토큰 가치의 안정성을 보장합니다. 이 시스템은 별도의 자금 조달 계약을 사용하여 사용자 상호 작용을 간소화하고 가스비를 최소화합니다.

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

_검토 커밋 해시_ - [08f90c7e9687586b016e6ccc7c50d4dee4ef295a](https://github.com/gammaswap/v1-vaults/tree/08f90c7e9687586b016e6ccc7c50d4dee4ef295a)

_수정 검토 커밋 해시_ - [dbfbc9c288147c36f784446c838a174a30d245c0](https://github.com/gammaswap/v1-vaults/tree/dbfbc9c288147c36f784446c838a174a30d245c0)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `FundingVaultERC20`
- `GammaVaultERC20`
- `FundingVault`
- `GSHedgeMath`
- `RangedPoolMath`
- `VaultStorage`
- `VaultCollateralManager`
- `VaultERC165Storage`
- `VaultLoanObserver`
- `BaseProcess`
- `DepositProcess`
- `HedgeProcess`
- `RebalanceProcess`
- `WithdrawProcess`
- `VaultAppStorage`
- `VaultRebalancer`
- `GammaVault`

# 발견 사항 (Findings)

# [C-01] `disburseClaims()`가 청구되지 않은 자금을 훔칠 수 있게 함 (`disburseClaims()` allows theft of unclaimed funds)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

FundingVault의 `disburseClaims` 함수는 이전 기간의 청구되지 않은 보상을 고려하지 않고 현재의 모든 계약 토큰 잔액을 현재 기간에 잘못 할당합니다. 이로 인해 이전 기간에 할당된 자금을 현재 기간의 사용자가 청구할 수 있게 됩니다.

FundingVault는 사용자가 이전 기간에 대한 청구를 할 수 있도록 합니다. 청구 가능한 금액은 `_claimable`과 claimToken의 현재 잔액 중 더 적은 값으로 결정됩니다.

```solidity
    function claim(uint256 _period) external override virtual lock {
        ...
        _claimable = GSMath.min(GammaSwapLibrary.balanceOf(claimToken, address(this)), _claimable);
        ...
        GammaSwapLibrary.safeTransfer(claimToken, _to, _claimable);
```

그러나 `disburseClaims`는 이전 기간의 청구되지 않은 금액을 추적하지 못합니다. 그리고 보류 중인 청구에 대한 예약 없이 전체 토큰 잔액을 현재 기간에 할당합니다.

```solidity
    function disburseClaims() external override virtual isManager {
        ...
        totalClaimable[_period] = GammaSwapLibrary.balanceOf(claimToken, address(this)); // @audit Incorrectly assigns all current balance to current period
        ...
    }
```

시나리오:

- 기간 2에 앨리스는 1000 토큰을 청구할 자격이 있지만 아직 청구하지 않았습니다.
- 기간 3이 시작되고 500 토큰 상당의 새로운 청구 금액이 추가됩니다.
- `disburseClaims`가 호출되어 totalClaimable[3] = 1500 (1000 + 500)으로 설정됩니다.
- 기간 3에 100 토큰만 받을 자격이 있는 밥이 먼저 청구하여 총 1500 토큰에서 자신의 공정한 몫보다 더 많은 것을 받습니다.
- 앨리스가 기간 2의 1000 토큰을 청구하려고 할 때 자금이 충분하지 않습니다. 모든 토큰이 기간 3에 청구되면 그녀는 0을 받을 수 있습니다.

## 권장 사항

`disburseClaims`의 매개변수를 통해 각 기간의 `totalClaimable`을 별도로 추적하십시오:

```diff
- function disburseClaims() external override virtual isManager {
- + function disburseClaims(uint256 newClaimable) external override virtual isManager {
    ...
-    totalClaimable[_period] = GammaSwapLibrary.balanceOf(claimToken, address(this));
+    totalClaimable[_period] = newClaimable;
    ...
}
```

# [H-01] `processWithdrawals()`의 잘못된 교환 경로 (Incorrect swapping path in `processWithdrawals()`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`GammaVault` 관리자가 `processWithdrawals()`를 호출하면, `withdrawVault`는 보유하고 있는 `GammaVault` 토큰을 `GammaVault`로 전송하고, `GammaVault`는 Uniswap V3에서 인출할 해당 유동성을 계산하고, 헤지 비율을 유지하기 위해 GammaSwap 헤지를 비례적으로 조정하며, 모든 자금(이익 또는 손실 포함)을 다시 `assetToken`으로 변환한 다음, 결과 자금을 `withdrawVault`로 전송합니다.

`withdrawVault`는 `assetToken`을 받을 것으로 예상되며(현재 프로토콜은 `WETH/USDC` 쌍만 지원하므로 `assetToken`은 `WETH`임), Uniswap 라우터를 통해 다른 토큰 쌍을 `assetToken`으로 변환하기 위해 스왑이 수행됩니다.

`baseBalance`는 다른 토큰 쌍(`USDC`)을 나타내지만, 기본 토큰이 `token0`인지 `token1`인지 여부에 관계없이 스왑 경로는 항상 `path1`로 설정됩니다.

```solidity
    function processWithdrawals(
         bytes memory path0,
         bytes memory path1
     ) external virtual override {
         //...
         uint256 baseBalance = GammaSwapLibrary.balanceOf(
                     isAssetToken0 ? token1 : token0,
                     address(this)
                 );
         if (baseBalance > 0) {
                     IUniversalRouter(router).swapExactTokensForTokens(
                         baseBalance,
                         0,
                         path1, // @audit :  path is incorrect
                         address(this),
                         type(uint256).max
                     );
                     uint256 assetBalance = GammaSwapLibrary.balanceOf(
                         assetToken,
                         address(this)
                     );
                     GammaSwapLibrary.safeTransfer(
                         assetToken,
                         s.withdrawVault,
                         assetBalance
                     );
                 }
         //...
     }
```

프로토콜은 현재 `WETH`가 `assetToken`인 `WETH/USDC`만 지원하고, 프로토콜은 처음에 Arbitrum에 배포될 예정이므로; 여기서 `token1`은 USDC가 됩니다(Arbitrum 및 Base에서 USDC 주소 > WETH 주소). 따라서 여기서 `baseToken` 경로는 정확하며(`path1`) 아무런 문제가 발생하지 않습니다.

**그러나** `WETH` 주소 > `USDC` 주소인 이더리움에 프로토콜이 배포될 때(`WETH` 주소: `0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2`, `USDC` 주소 `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48`), `token0`은 `USDC`(`baseToken`)가 되므로 `baseToken`을 판매하는 데 사용되는 경로가 올바르지 않아 스왑이 조용히 실패하고 `withdrawVault`가 더 적은 `assetToken`(`WETH`)을 받게 되어 사용자 청구에 부정적인 영향을 미칩니다.

이 문제는 `baseToken`을 판매하기 위한 스왑 경로가 항상 `path1`이 아닌 다른 토큰 쌍이 프로토콜에 채택될 때 더 심각해집니다.

## 권장 사항

`assetToken`이 `token0`인지 `token1`인지에 따라 올바른 경로를 동적으로 선택하도록 코드를 업데이트하십시오:

```diff
    function processWithdrawals(
         bytes memory path0,
         bytes memory path1
     ) external virtual override {
         //...
         uint256 baseBalance = GammaSwapLibrary.balanceOf(
                     isAssetToken0 ? token1 : token0,
                     address(this)
                 );
         if (baseBalance > 0) {
                     IUniversalRouter(router).swapExactTokensForTokens(
                         baseBalance,
                         0,
-                       path1,
+                       isAssetToken0 ? path1 : path0,
                         address(this),
                         type(uint256).max
                     );
                     uint256 assetBalance = GammaSwapLibrary.balanceOf(
                         assetToken,
                         address(this)
                     );
                     GammaSwapLibrary.safeTransfer(
                         assetToken,
                         s.withdrawVault,
                         assetBalance
                     );
                 }
         //...
     }
```

# [H-02] AMM과 상호 작용할 때 슬리피지 보호 없음 (No slippage protection when interacting with AMM)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

- 스왑 및 유동성 추가를 위해 AMM 라우터(Uniswap)와 상호 작용하는 동안 `amount0Min` 및 `amount1Min`이 0으로 설정되는 것이 확인되었습니다. 이러한 매개변수는 슬리피지로부터 보호하기 위한 것입니다. 이를 0으로 설정하면 트랜잭션이 슬리피지에 노출되어 의도한 것보다 적은 금액을 받거나 추가할 가능성이 있습니다.

- 이 문제는 스왑, 유동성 추가 및 유동성 제거 시 AMM(Uniswap)과의 모든 상호 작용에서 확인되었으며, 이러한 다양한 상호 작용은 프로토콜에 다른 영향을 미칩니다:

\_depositAsset()
\_increaseLiquidity()
\_addMissingAmountsToHedge()
\_decreaseLiquidity()
\_depositAsset()
processWithdrawals()
rebalanceCollateralToRatio()

## 권장 사항

최소 금액을 0으로 하드코딩하는 대신, 슬리피지 비율(예: 스왑 또는 추가된 금액의 80%, 높은 시장 변동성 동안 슬리피지를 업데이트하는 설정자 포함)을 제어하는 상태 변수를 도입하고 AMM과 상호 작용할 때 이를 활용하십시오.

또는 minLiquidity를 수령하도록 설정하고 전체 트랜잭션의 총 유동성 수령액이 minLiquidity보다 작으면 되돌리도록 하는 것이 좋습니다. minLiquidity는 현재(조작되지 않은) 가격을 사용하여 시뮬레이션으로 결정할 수 있습니다.

# [M-01] 한도 없는 펀딩 기간 입금으로 인해 DoS가 발생할 수 있음 (Uncapped funding period deposits could lead to DoS)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`depositVault`는 한도 없이 펀딩 기간당 무제한 입금을 허용하므로 단일 기간 동안 너무 많은 유동성이 `depositVault`에 입금되면 `DepositProcess.processDeposit()` 실행 중 DoS 시나리오가 발생할 수 있습니다. 이로 인해 금고가 GammaSwap 풀이 지원할 수 있는 것보다 더 큰 포지션을 헤지하려고 시도하게 되어 `IGammaPool(gsPool).borrowLiquidity()` 호출이 되돌려질(revert) 수 있습니다.

```solidity
function _increaseHedge(
        uint256 _liquidity,
        bytes memory path0,
        bytes memory path1
     ) internal virtual {
      //...
        uint256 _hedgeLpTokens = GSHedgeMath.convertLiquidityToLPTokens(
             cfmm,
             _liquidity
         );
 
          IGammaPool(gsPool).borrowLiquidity(
             _gsTokenId,
             _hedgeLpTokens,
             new uint256[](0)
         ); // use externalRebalancing here or hedge with internal AMM
 
         _updateHedge(path0, path1, s.ratio0, s.ratio1);
     }
 
```

`_hedgeLpTokens` 금액은 Uniswap 풀의 총 유동성과 LP 토큰 공급 간의 비율을 사용하여 계산됩니다:

```solidity
     function convertLiquidityToLPTokens(
         address _cfmm,
         uint256 _liquidity
     ) internal view returns (uint256) {
         (uint128 reserve0, uint128 reserve1, ) = ICPMM(_cfmm).getReserves();
         uint256 lastCFMMInvariant = GSMath.sqrt(reserve0 * reserve1);
         uint256 lastCFMMTotalSupply = GammaSwapLibrary.totalSupply(_cfmm);
         return (_liquidity * lastCFMMTotalSupply) / lastCFMMInvariant;
     }
```

그리고 `IGammaPool(gsPool).borrowLiquidity()` 함수는 요청된 LP 토큰이 풀의 가용 잔액(`s.LP_TOKEN_BALANCE`)을 초과하면 되돌려집니다:

```solidity
 function borrowLiquidity(uint256 tokenId, uint256 lpTokens, uint256[] calldata ratio) external virtual override whenNotPaused(12) returns(uint256 liquidityBorrowed, uint256[] memory amounts, uint128[] memory tokensHeld) {
         return abi.decode(callStrategy(borrowStrategy, abi.encodeCall(IBorrowStrategy._borrowLiquidity, (tokenId, lpTokens, ratio))), (uint256, uint256[], uint128[]));
  }
 
 //...
 /// @dev See {IBorrowStrategy-_borrowLiquidity}.
  function _borrowLiquidity(uint256 tokenId, uint256 lpTokens, uint256[] calldata ratio) external virtual override lock returns(uint256 liquidityBorrowed, uint256[] memory amounts, uint128[] memory tokensHeld) {
         // Revert if borrowing all CFMM LP tokens in pool
         if(lpTokens >= s.LP_TOKEN_BALANCE) revert ExcessiveBorrowing();
     //...
  }
```

GammaSwap 풀의 `s.LP_TOKEN_BALANCE`를 초과하는 것은 두 가지 방법으로 달성될 수 있습니다:

1.  악의적인 행위자가 (프로토콜이 이더리움에 배포될 것이므로) 플래시론을 통해 대량의 ETH를 입금하여 `processDeposit()` 트랜잭션을 프론트런하여 헤지를 늘리는 데 필요한 lp 토큰이 GammaSwap 풀 잔액 `s.LP_TOKEN_BALANCE`를 초과하도록 부풀릴 수 있습니다.
2.  사용자가 같은 펀딩 기간 동안 자연스럽게 많은 금액을 입금하는 경우입니다.

## 권장 사항

- `depositVault`가 도달 시 사용자로부터 더 이상의 입금을 받을 수 없는 기간별 입금 한도를 구현하십시오.
- 요청된 LP 토큰(`_hedgeLpTokens`)이 GammaSwap 풀의 `s.LP_TOKEN_BALANCE`를 초과하지 않도록 `convertLiquidityToLPTokens()` 계산을 업데이트하십시오.

# [M-02] `rebalancePosition()` 트랜잭션이 프론트런에 취약함 (`rebalancePosition()` transaction is susceptible to frontrunning)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

관리자가 `rebalancePosition`을 호출하면 포지션 담보가 `GammaVault` 관리자가 지정한 원하는 비율로 재조정됩니다. 재조정 프로세스 중에 수행되는 단계는 다음과 같습니다:

1. Uniswap V3 포지션에서 수수료 징수.
2. 유동성을 0으로 감소.
3. 포지션 종료.
4. 수정된 헤지로 새 포지션 개설.

```javascript
  function rebalancePosition(
         IGammaVault.RebalancePositionData memory params
     ) external virtual override {
         _validatePaths(params.path0, params.path1);
 
         _collectFees(s.tokenId, address(this));
         _decreaseLiquidity(
             s.tokenId,
             s.totalLiquidity,
             params.amount0Min,
             params.amount1Min
         );
         _closeLP(s.tokenId);
 
         //...
     }
```

```javascript
     function _closeLP(uint256 _tokenId) internal {
         if (_tokenId > 0) {
             IUniswapV3PositionManager(nftPosMgr).burn(_tokenId);
             s.tokenId = 0;
         }
     }
```

`IUniswapV3PositionManager(nftPosMgr).burn()` 호출은 포지션의 유동성이 0이어야 합니다. 그렇지 않으면 트랜잭션이 되돌려집니다:

```javascript
    function burn(uint256 tokenId) external payable override isAuthorizedForToken(tokenId) {
         Position storage position = _positions[tokenId];
         require(position.liquidity == 0 && position.tokensOwed0 == 0 && position.tokensOwed1 == 0, 'Not cleared');
         delete _positions[tokenId];
         _burn(tokenId);
     }
```

`s.totalLiquidity` 값은 인출 후 포지션을 종료하는 데 필요한 포지션의 총 유동성으로 예상되지만, 이 캐시된 값을 사용하면 `rebalancePosition()` 트랜잭션이 프론트런에 노출되어 악의적인 사용자가 `NonfungiblePositionManager.increaseLiquidity()`를 호출하고 포지션에 1 wei의 유동성을 추가하여 트랜잭션을 프론트런할 수 있습니다.

이로 인해 유동성이 더 이상 0이 아니므로 `burn()` 호출이 되돌려지고 포지션이 종료되지 않습니다.

## 권장 사항

`rebalancePosition()` 내에서 Uniswap V3 포지션 관리자로부터 직접 포지션 유동성을 가져오십시오:

```diff
  function rebalancePosition(
         IGammaVault.RebalancePositionData memory params
     ) external virtual override {
         _validatePaths(params.path0, params.path1);
-        uint256 _totalLiquidity = _getTotalLiquidity(_tokenId);
         _collectFees(s.tokenId, address(this));
         _decreaseLiquidity(
             s.tokenId,
-            s.totalLiquidity,
+            _totalLiquidity,
            params.amount0Min,
             params.amount1Min
         );
         _closeLP(s.tokenId);
 
         //...
     }
```

# [M-03] `processWithdrawals()`가 `totalLiquidity`를 잘못 업데이트함 (`processWithdrawals()` incorrectly updates of `totalLiquidity`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

- `processWithdrawals()` 함수에서: `_withdrawShares = 0`일 때 `s.totalLiquidity`는 포지션의 징수 및 예치된 수수료의 유동성만큼 증가하며, 여기서 `_reviseHedge()`가 호출될 때 포지션의 totalLiquidity가 감소하지 않을 것이라고 가정합니다:

```javascript
  function processWithdrawals(
         bytes memory path0,
         bytes memory path1
     ) external virtual override {
         //...
         uint256 _liquidity = _depositFees(_tokenId, path0, path1);
         _totalLiquidity += _liquidity;
 
         if (_withdrawShares == 0) {
             // if no withdrawals we only compound fees
             if (_liquidity > 0) {
                 _reviseHedge(_totalLiquidity, path0, path1); // liquidity only goes up here
             }
              s.totalLiquidity = _totalLiquidity;
             FundingVault(s.withdrawVault).disburseClaims();
             return;
         }
 
         //...
     }
```

여기서:

```javascript
  function _reviseHedge(
         uint256 _totalLiquidity,
         bytes memory path0,
         bytes memory path1
     ) internal virtual {
         uint256 _newHedgeLiquidity = (_totalLiquidity * s.hedgeSize) / 1e18;
 
         uint256 _prevHedgeLiquidity = _loanData.liquidity;
 
         if (_newHedgeLiquidity > _prevHedgeLiquidity) {
             _increaseHedge(
                 _newHedgeLiquidity - _prevHedgeLiquidity,
                 path0,
                 path1
             );
         } else if (_newHedgeLiquidity < _prevHedgeLiquidity) {
             _decreaseHedge(
                 _prevHedgeLiquidity - _newHedgeLiquidity,
                 path0,
                 path1
             );
         }
     }
```

- `hedgeSize`에 대한 설정자가 있다는 점을 감안할 때(`_newHedgeLiquidity`를 계산하는 데 사용됨), `hedgeSize`가 업데이트된 경우 유동성 포지션에 따라 `s.totalLiquidity`가 업데이트되어야 합니다(또한 `_reviseHedge()`에서 헤지 유동성은 시간이 지남에 따라 헤지가 이자를 발생시켰는지 확인하지 않고 마지막으로 업데이트된 데이터를 기반으로 가져옵니다).

- 따라서 새 `hedgeSize`가 감소하여 `_newHedgeLiquidity < _prevHedgeLiquidity`가 되면 헤지가 감소하여 헤지에 손실이 발생할 수 있으므로 유동성에 영향을 미치고, 이는 인출 후 실행될 다음 프로세스에 영향을 미칩니다: 다음 프로세스가 입금인 경우 이 잘못된 `s.totalLiquidity`는 유동성이 실제 유동성을 반영하지 않으므로(현금화된 것 + LP 포지션의 징수 및 예치된 수수료의 유동성이므로) 해당 예치된 기간에 대해 잘못된 지분을 발행하는 결과를 초래합니다.

- `DepositProcess.processDeposits()` 함수에서도 동일한 문제가 발생합니다.

## 권장 사항

```diff
  function processWithdrawals(
         bytes memory path0,
         bytes memory path1
     ) external virtual override {
         //...
         uint256 _liquidity = _depositFees(_tokenId, path0, path1);
         _totalLiquidity += _liquidity;
 
         if (_withdrawShares == 0) {
             // if no withdrawals we only compound fees
             if (_liquidity > 0) {
                 _reviseHedge(_totalLiquidity, path0, path1); // liquidity only goes up here
             }
-            s.totalLiquidity = _totalLiquidity;
+            s.totalLiquidity = _getTotalLiquidity(s.tokenId);
             FundingVault(s.withdrawVault).disburseClaims();
             return;
         }
 
         //...
     }
```
# [M-04] GammaVault 담보 계산 시 가격 조작 위험 (Price manipulation risk in GammaVault collateral calculation)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

GammaVault 계약은 조작에 더 강한 가격 소스 대신 Uniswap V3의 현재 현물 가격을 사용하여 GammaPool 포지션에 대한 외부 담보를 계산합니다. 이로 인해 공격자가 현물 가격을 조작하여 담보 계산에 영향을 미치고 불공정한 청산을 강제할 수 있는 잠재적인 취약점이 발생합니다.

```solidity
    function _getCollateral(address _gammaPool, uint256 _tokenId) internal override virtual view returns(uint256 collateral) {
        uint160 sqrtPriceX96 = getCurrentPrice(); // @audit collateral is depent on spot price
        uint160 sqrtPriceAX96 = RangedPoolMath.calcSqrtRatioAtTick(s.tickLower);
        uint160 sqrtPriceBX96 = RangedPoolMath.calcSqrtRatioAtTick(s.tickUpper);

        uint128[] memory tokensHeld = new uint128[](2);
        {
            (uint256 amount0, uint256 amount1) = RangedPoolMath.getAmountsForLiquidity(sqrtPriceX96, sqrtPriceAX96,
            sqrtPriceBX96, uint128(s.totalLiquidity));
            tokensHeld[0] = uint128(amount0);
            tokensHeld[1] = uint128(amount1);
        }

        collateral = GSHedgeMath.convertAmountsToLiquidity(cfmm, tokensHeld, numOfDecimals0, numOfDecimals1, mathLib);
    }
```

공격 시나리오를 살펴보겠습니다:

- 공격자는 다음과 같은 GammaVault 포지션을 식별합니다: - Uniswap V3의 좁은 틱 범위(높은 유동성 집중) - 높은 헤지 비율 - 청산 임계값에 가까운 담보 가치
- 공격자는 대규모 스왑을 실행하여 Uniswap V3 현물 가격을 조작합니다.
- 이 조작은 `_getCollateral`을 통해 계산된 담보 가치를 일시적으로 줄입니다.
- 공격자는 헤지 포지션의 청산을 트리거합니다.
- 공격자는 내부 담보(tokensHeld)를 할인된 가격에 청구하여 이익을 얻습니다.

## 권장 사항

외부 담보를 계산할 때 현물 가격 대신 TWAP 가격을 사용해야 합니다.

# [M-05] 풀의 가격이 최소/최대 틱에 도달하면 입금이 실패함 (Deposits will fail when pool's price reaches the min/max tick)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

입금 중 코드는 `calculateTokenRatio()`를 호출하여 `ratio0`과 `ratio1`을 계산합니다. 풀의 가격이 최고가 또는 최저가에 도달하면 `ratio0` 또는 `ratio1`은 0이 됩니다:

```solidity
        } else if (price <= priceLower) {
            ratio0 = 0;
            ratio1 = decimals1;
        } else if (price >= priceUpper) {
            ratio0 = decimals0;
            ratio1 = 0;
        }
```

나중에 코드는 `ratio0` 및 `ratio1`을 사용하여 `calculateMaxDeposit()`을 호출하여 최대 입금 금액을 계산합니다. 문제는 `ratio0` 또는 `ratio1`이 0인 경우 0으로 나누기(division by zero)로 인해 `calculateMaxDeposit()` 함수가 되돌려질(revert) 수 있다는 것입니다:

```solidity
    function calculateMaxDeposit(bool isToken0, uint256 amount0, uint256 amount1, uint256 ratio0, uint256 ratio1)
        internal view returns(uint256 deposit0, uint256 deposit1) {
        if(isToken0) {
            deposit0 = amount0;
            deposit1 = ratio1 * amount0 / ratio0;
            if(deposit1 > amount1) {
                deposit0 = ratio0 * amount1 / ratio1;
                deposit1 = ratio1 * deposit0 / ratio0;
            }
        } else {
            deposit1 = amount1;
            deposit0 = ratio0 * amount1 / ratio1;
            if(deposit0 > amount0) {
                deposit1 = ratio1 * amount0 / ratio0;
                deposit0 = ratio0 * deposit1 / ratio1;
            }
        }
    }
```

결과적으로 가격이 최소 또는 최대 틱과 같으면 입금이 항상 되돌려집니다.

## 권장 사항

`calculateMaxDeposit()`에서 ratio0 또는 raio1이 0인 경우를 처리하십시오.

# [L-01] 누락된 ECDSA 확인으로 인한 서명 가변성 (Signature malleability due to missing ECDSA checks)

FundingVault20의 `permit` 함수는 서명 가변성에 대한 검사를 구현하지 않고 `ecrecover`를 사용하여 서명을 검증합니다. `s`가 곡선 차수의 하반부에 있는지에 대한 검증이 없습니다.

```solidity
    function permit(address owner, address spender, uint256 value, uint256 deadline, uint8 _v, bytes32 _r, bytes32 _s)
        external
        override
    {
        ...
        address recoveredAddress = ecrecover(digest, _v, _r, _s); // @audit s isn't verified
        ...
    }
```

논스 메커니즘(`nonces[owner]++`)이 서명 재생 공격을 방지하지만, 적절한 ECDSA 확인이 없다는 것은 모범 사례에서 벗어난 것입니다.

`s` 값이 하반부 차수에 있어야 한다고 요구하는 것이 좋습니다.
참조: [링크](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol#L143)

# [L-02] 낮은 가격 값에 대해 제곱근 가격을 디코딩할 때 잠재적인 정밀도 손실 (Potential precision loss when decoding square root prices for low price values)

decodeSqrtPrice 함수에서 sqrt 가격을 디코딩할 때 낮은 가격 값에 대해 정밀도 손실이 발생하여 유동성 제공 중 토큰 비율 계산에 잠재적으로 영향을 줄 수 있습니다.

decodeSqrtPrice 함수는 다음 공식을 사용합니다:

```solidity
        sqrtPrice = sqrtPriceX96 * GSMath.sqrt(decimals) / (2 ** 96);
```

다음과 같은 경우 계산은 0으로 내림될 수 있습니다: `sqrtPriceX96 < (2**96) / GSMath.sqrt(decimals)`

18자리 소수를 가진 token0의 경우:

- `sqrtPriceX96` < 7.922816251426434e19 (약 틱 -414487)일 때 0으로 내림됩니다.

6자리 소수를 가진 token0의 경우:

- `sqrtPriceX96` < 7.922816251426434e25 (약 틱 -138163)일 때 0으로 내림됩니다.

이 두 틱 값 모두 `MIN_TICK`(-887272)보다 높으므로 GammaVault는 낮은 가격대를 포함하는 넓은 범위에 걸쳐 유동성을 제공할 때 정밀도 손실을 경험할 수 있습니다.

# [L-03] 출금된 헤지 이익이 Uniswap V3 포지션에 재투자되지 않음 (Withdrawn hedge profit is not reinvested into Uniswap V3 position)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

담보 재조정 후 헤지가 수정되고 줄여야 할 때, 대출 상환 후 헤지가 이익을 낸 경우 이 이익은 `IGammaPool(gsPool).decreaseCollateral()`을 통해 `GammaVault`로 출금됩니다:

```solidity
  function _rebalanceAndRepayLiquidity(uint256 _gsTokenId, uint256 _liquidity, bytes memory _path0, bytes memory _path1) internal {
    //...
        if(_missingAmounts[0] > 0 && _missingAmounts[1] > 0) {
            //... when the hedge incurs a loss
        } else {
            //... when the hedge incurs profit
            (,uint256[] memory paidAmounts) = IGammaPool(gsPool).repayLiquidity(_gsTokenId, _liquidity, 0, address(0));

            _loanData1 = getLoanData(_gsTokenId, false);

            _amounts = GSHedgeMath.calcWithdrawShare(_amounts, paidAmounts, _loanData1.tokensHeld);
            IGammaPool(gsPool).decreaseCollateral(_gsTokenId, _amounts, address(this), new uint256[](0));
        }
    //...
```

문서에 따르면:

- 헤지 이익은 **재조정(re-balancing)** 프로세스 중에 수행되는 경우 Uniswap 포지션에 **재투자**되어야 합니다.
- 헤지 이익은 **출금(withdrawal)** 프로세스 중에 수행되는 경우 사용자에게 **분배**되어야 합니다.

출금된 이익은 출금 프로세스 중에 사용자에게 올바르게 분배되지만, 재조정 중에 이익이 발생하면 출금된 담보는 Uniswap 포지션에 재투자되지 않고 대신 다음 프로세스까지 금고에 남아 있어 이 미투자 담보에 대한 LP 수수료 손실이 발생합니다.

## 권장 사항

재조정 프로세스 중에 수집된 경우 수집된 헤지 이익을 Uniswap 포지션에 입금하십시오.

# [L-04] 사용자에게 유리한 `shares` 계산의 내림 (Rounding down in the `shares` calculation in favor of the user)

`FundingVault()`에서: 사용자가 금고에서 예치된 자산을 인출하기 위해 `withdraw()`를 호출할 때, `shares` 금액은 인출하도록 요청된 `assets`를 기반으로 계산됩니다:

```solidity
 function withdraw(
        uint256 assets,
        address to,
        address from
    ) external virtual override lock returns (uint256 shares) {
        //...
        shares = _convertToShares(assets, _period);

        // Revert if redeeming 0 GS LP tokens
        if (shares == 0) revert ZeroShares();

        // Send CFMM LP tokens to receiver (`to`) and burn corresponding GS LP tokens from msg.sender
        withdrawAssets(msg.sender, to, from, assets, shares, _period);
    }
```

여기서:

```solidity
 function _convertToShares(
        uint256 assets,
        uint256 _period
    ) internal view virtual returns (uint256) {
        if (assets == 0) return 0;

        uint256 _totalSupply = periodTotalSupply[_period]; // Saves an extra SLOAD if totalSupply is non-zero.
        uint256 _totalAssets = periodTotalAssets[_period];
        if (_totalSupply == 0 || _totalAssets == 0) {
            require(assets > MIN_SHARES, "FundingVault: MIN_SHARES");
            unchecked {
                return assets - MIN_SHARES;
            }
        }

        return ((assets * _totalSupply) / _totalAssets);
    }
```

- 알 수 있듯이 `_convertToShares()` 함수는 계산된 주식 금액(요청된 인출 금액을 얻기 위해 소각될 금액)을 내림(round down)하는데, 이는 사용자가 예치된 자산을 얻기 위해 더 적은 주식을 소각할 수 있으므로 금고 대신 사용자에게 유리합니다.

`_convertToShares()` 함수를 업데이트하여 주식을 올림(round up)하십시오:

```diff
 function _convertToShares(
        uint256 assets,
        uint256 _period
    ) internal view virtual returns (uint256) {
    //...

-       return ((assets * _totalSupply) / _totalAssets);
+       return (assets * _totalSupply + (_totalAssets - 1)) / _totalAssets;
    }
```

# [L-05] 스왑 후 입금 금액을 계산할 때 오래된 가격 비율 사용 (Stale price ratios used when calculating deposit amounts after swaps)

BaseProcess.sol의 `_depositAsset` 함수는 토큰 스왑 수행 후 최적의 입금 금액을 계산할 때 잠재적으로 오래된 가격 비율을 사용합니다. 이로 인해 최적이 아니거나 잘못된 입금 금액이 계산될 수 있습니다.

문제는 다음과 같은 이유로 존재합니다.

- 초기 가격 비율이 계산됨
- 풀의 가격을 변경할 수 있는 스왑이 수행됨
- 원래(이제는 오래된) 비율을 사용하여 `calculateMaxDeposit`을 통해 입금 금액을 계산함

이로 인해 잘못된 `amount0` 및 `amount1`이 유동성 증가에 사용됩니다.

```solidity
    function _depositAsset(
        uint160 sqrtPriceX96,
        uint160 sqrtPriceAX96,
        uint160 sqrtPriceBX96,
        bytes memory path0,
        bytes memory path1
    ) internal returns (uint256 _totalLiquidity) {
        uint256 amount0 = GammaSwapLibrary.balanceOf(token0, address(this));
        uint256 amount1 = GammaSwapLibrary.balanceOf(token1, address(this));
        while (amount0 > s.dust0 || amount1 > s.dust1) {
            (uint256 ratio0, uint256 ratio1) =
            RangedPoolMath.calculateTokenRatio(sqrtPriceX96, sqrtPriceAX96, sqrtPriceBX96, decimals0, decimals1);
            ...
            (amount0, amount1) = RangedPoolMath.calculateMaxDeposit(isAssetToken0, amount0, amount1, ratio0, ratio1); // @audit using the stale ratio
            ...
        }
    }
```

`calculateMaxDeposit` 함수에서 사용하기 위해 각 스왑 후 새 비율을 계산하는 것이 좋습니다.

```diff
    function _depositAsset(
        uint160 sqrtPriceX96,
        uint160 sqrtPriceAX96,
        uint160 sqrtPriceBX96,
        bytes memory path0,
        bytes memory path1
    ) internal returns (uint256 _totalLiquidity) {
        uint256 amount0 = GammaSwapLibrary.balanceOf(token0, address(this));
        uint256 amount1 = GammaSwapLibrary.balanceOf(token1, address(this));
        while (amount0 > s.dust0 || amount1 > s.dust1) {
-            (uint256 ratio0, uint256 ratio1) =
-            RangedPoolMath.calculateTokenRatio(sqrtPriceX96, sqrtPriceAX96, sqrtPriceBX96, decimals0, decimals1);
+            (uint256 ratio0, uint256 ratio1) = RangedPoolMath.calculateTokenRatio(sqrtPriceX96, sqrtPriceAX96, sqrtPriceBX96, decimals0, decimals1);
            ...
            
            (amount0, amount1) = RangedPoolMath.calculateMaxDeposit(isAssetToken0, amount0, amount1, ratio0, ratio1); // @audit using the stale ratio
            ...
        }
    }
```

# [L-06] 누락된 트랜잭션 마감일 검증 (Missing transaction deadline validation)

금고의 유동성 관리 함수(`_increaseLiquidity` 및 `_decreaseLiquidity`)는 트랜잭션 `deadlines`를 `type(uint256).max`로 설정하여 유동성 작업에 대한 시간 초과 보호를 사실상 제거합니다. 이는 네트워크 혼잡 기간 동안 트랜잭션이 무기한 보류 상태로 남을 수 있는 여러 위험을 초래합니다. 그리고 지연된 트랜잭션은 특히 변동성이 큰 시장 상황에서 불리한 가격이나 조건에서 실행될 수 있습니다. 예를 들어, Uniswap V3 및 CPMM의 가격이 차이가 날 때 트랜잭션이 실행됩니다.

```solidity
    function _increaseLiquidity(
        uint256 _tokenId,
        uint256 amount0Desired,
        uint256 amount1Desired,
        uint256 amount0Min,
        uint256 amount1Min
    ) internal returns (uint128 liquidity, uint256 amount0, uint256 amount1) {
        if(_tokenId == 0) {
            IUniswapV3PositionManager.MintParams memory mintParams = IUniswapV3PositionManager.MintParams({
                ...
                deadline: type(uint256).max  // @audit No deadline protection
            });
            (s.tokenId, liquidity, amount0, amount1) = IUniswapV3PositionManager(nftPosMgr).mint(mintParams);
        } else {
            IUniswapV3PositionManager.IncreaseLiquidityParams memory params = IUniswapV3PositionManager
                .IncreaseLiquidityParams({
                ...
                    deadline: type(uint256).max // @audit No deadline protection
            });
            (liquidity, amount0, amount1) = IUniswapV3PositionManager(nftPosMgr).increaseLiquidity(params);
        }
    }

    function _decreaseLiquidity(uint256 _tokenId, uint256 liquidity, uint256 amount0Min, uint256 amount1Min)
        internal returns (uint256 amount0, uint256 amount1) {
        ...
        IUniswapV3PositionManager.DecreaseLiquidityParams memory params = IUniswapV3PositionManager
            .DecreaseLiquidityParams({
                ...
                deadline: type(uint256).max // @audit No deadline protection
        ...
    }
```

함수에 인수로 전달하여 마감일을 설정하십시오.

# [L-07] `calcUpdatedCollateral()` 호출 시 잘못된 값 사용 (Using the wrong value when calling `calcUpdatedCollateral()`)

`_rebalanceAndRepayLiquidity()` 함수에서 코드는 재조정을 실행한 다음 `calcUpdatedCollateral()`을 호출하여 업데이트된 담보 금액을 계산합니다:

```solidity
 if(_liquidity < _loanData0.liquidity) {
            uint256 remaining = _loanData0.liquidity - _liquidity;
            if(remaining <= 1e3) {
                _liquidity += 1e4; // Close entire position. 1e3 is MIN_BORROW in GammaPool. 1e4 to avoid rounding issues
            }
        }

        uint128[] memory _collateral = GSHedgeMath.calcCollateralShare(_loanData0.tokensHeld, _liquidity, _loanData0.liquidity);
        uint128[] memory _tokensOwed = GSHedgeMath.calcTokensOwedShare(gsPool, _liquidity);

--snip--

        uint128[] memory _amounts = new uint128[](2);
        _amounts[0] = _collateral[0] + _collateral[0] / 1e4; // padding
        _amounts[1] = _collateral[1] + _collateral[1] / 1e4; // padding
        _amounts[0] = _amounts[0] > _loanData0.tokensHeld[0] ? _loanData0.tokensHeld[0] : _amounts[0];
        _amounts[1] = _amounts[1] > _loanData0.tokensHeld[1] ? _loanData0.tokensHeld[1] : _amounts[1];
        IGammaPoolExternal(gsPool).rebalanceExternally(_gsTokenId, _amounts, 0, hedgeRebalancer, abi.encode(data));

        IGammaPool.LoanData memory _loanData1 = getLoanData(_gsTokenId, false);
        _amounts = GSHedgeMath.calcUpdatedCollateral(_collateral, _loanData0.tokensHeld, _loanData1.tokensHeld);

       uint128[] memory _missingAmounts = GSHedgeMath.calcShortfall(_amounts, _tokensOwed);

       if(_missingAmounts[0] > 0 && _missingAmounts[1] > 0) {
--snip--
            IGammaPool(gsPool).repayLiquidity(_gsTokenId, _liquidity, 0, address(0));
        } else {
--snip--
            IGammaPool(gsPool).decreaseCollateral(_gsTokenId, _amounts, address(this), new uint256[](0));
        }
```

남은 유동성이 1e3 미만일 때 코드가 `liquidity`를 1e4만큼 증가시키기 때문입니다. 따라서 `liquidity`는 `_loanData0.liquidity`보다 높을 수 있으며, `_collateral[]`은 `_loanData0.tokensHeld`보다 높을 수 있습니다.
문제는 코드가 `calcUpdatedCollateral()`을 호출할 때 `_collateral[]` 값을 사용하는데, `_collateral[]` 값은 `tokensHeld` 금액보다 높을 수 있으며, 이는 `rebalanceExternally()`를 수행하는 데 사용된 실제 금액인 `_amounts[]`를 보여주지 않는다는 것입니다. 결과적으로 제거된 유동성이 거의 모든 유동성일 때 재계산된 `_amounts[]`는 실제 값보다 약간 높아지고 이로 인해 `_missingAmounts[]`가 실제 값보다 낮아집니다. 코드는 `_missingAmounts[]` 값을 기반으로 유동성 상환 또는 담보 감소를 수행하며, 그 값이 잘못된 경우(실제 값보다 낮음) 유동성 상환 함수를 수행했어야 할 때 담보 감소를 수행하고 유동성을 상환할 자금이 충분하지 않아 되돌려질(revert) 것입니다.

`calcUpdatedCollateral()`을 호출할 때 `_amounts[]` 값을 사용하는 것을 고려하십시오.

# [L-08] 잘못된 `missingAmounts` 확인 (Incorrect `missingAmounts` check)

헤지가 수정되어 줄여야 할 때, 담보 재조정 후 대출/헤지가 보유한 담보가 부채 상환을 충당하기에 불충분한 경우, 부족분(`missingAmounts`)을 Uniswap 유동성 포지션에서 인출하여 손실을 충당해야 합니다.
프로토콜은 현재 WETH/USDC만 지원하며 초기에 Arbitrum에 배포될 예정입니다. 여기서:

- `token0` = WETH
- `token1` = USDC (Arbitrum 및 Base에서 USDC 주소 > WETH 주소).

```javascript
  function _rebalanceAndRepayLiquidity(
        uint256 _gsTokenId,
        uint256 _liquidity,
        bytes memory _path0,
        bytes memory _path1
     ) internal {
 
         //...
     if (_missingAmounts[0] > 0 && _missingAmounts[1] > 0) {
 
             _missingAmounts[0] = _missingAmounts[0] < 1e12
                 ? 1e12
                 : _missingAmounts[0];
 
             _missingAmounts[1] = _missingAmounts[1] < 1e4
                 ? 1e4
                 : _missingAmounts[1];
 
             _addMissingAmountsToHedge(
                 _gsTokenId,
                 _missingAmounts,
                 _path0,
                 _path1
             );
 
              IGammaPool(gsPool).repayLiquidity(
                 _gsTokenId,
                 _liquidity,
                 0,
                 address(0)
             );
         } else {
             //..
         }
         //...
     }
```

알 수 있듯이; `missingAmounts`에 대한 최소 임계값은 다음과 같이 하드코딩되어 있습니다:
`token0` (WETH) → 1e12
`token1` (USDC) → 1e4

**그러나** 프로토콜이 이더리움에 배포될 때, `WETH` 주소 > `USDC` 주소이므로(`WETH` 주소: `0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2`, `USDC` 주소 `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48`), 따라서:
`token0` = USDC (6 decimals)
`token1` = WETH (18 decimals)

여기서 `missingAmounts[0]`에 대한 하드코딩된 확인 1e12는 100만 USDC(1_000_000e6)로 변환되며, 이는 누락된 금액 확인에 대해 너무 높고 비현실적입니다.

- 따라서 최소 금액 비교가 거대하고 올바르지 않아 부정확한 유동성 제거 및 과잉 헤지가 발생합니다.

권장 사항:

`missingAmounts`의 최소값을 하드코딩하는 대신 `token0` 및 `token1`의 소수점 자릿수를 기반으로 동적으로 계산되도록 업데이트하십시오.

# [L-09] 재조정 루프의 작은 먼지 임계값으로 인해 토큰 손실이 발생할 수 있음 (Small dust thresholds in rebalancing loop can cause token loss)

VaultRebalancer 계약은 토큰 소수점 자릿수를 고려하지 않고 재조정 루프를 언제 빠져나갈지 결정하기 위해 고정된 먼지 임계값 `1e3`을 사용합니다. 이는 토큰 손실로 이어지는 문제를 생성합니다.

WETH(18 소수점)와 같이 소수점 자릿수가 높은 토큰의 경우 1e3 Wei의 WETH는 교환 시 0이 될 가능성이 높은 매우 작은 양(0.000000000000001 WETH)입니다. 이는 계약이 의미 있게 교환할 수 없는 먼지 양을 반복적으로 보내기 때문에 토큰 손실로 이어집니다.

이는 먼지 양이 0 출력으로 교환됨오로 인한 토큰 손실로 이어집니다. 각 외부 재조정에서 여러 번 반복됩니다. 시간이 지남에 따라 누적된 가치는 상당히 클 수 있습니다.

```solidity
    function rebalanceCollateralToRatio(address[] memory tokens, uint256[] memory ratio,
        ...
 
        for(uint256 i = 0; i < 10;) {
        ...
            if(bR0 < aR1 && (aR1 - bR0 > 1e10)) { // sell A
                ...
 
                if(sellQtyA < 1e3) break;
 
                IUniversalRouter(router).swapExactTokensForTokens(
                    sellQtyA, 0, path0, address(this), type(uint256).max
                );
            } else if(bR0 > aR1 && (bR0 - aR1 > 1e10)) { // sell B
                ...
                if(sellQtyB < 1e3) break;
 
                IUniversalRouter(router).swapExactTokensForTokens(
                    sellQtyB, 0, path1, address(this), type(uint256).max
                );
            } else {
                break;
            }
 
            unchecked {
                ++i;
            }
        }
    }
```

예를 들어: WETH(amountIn)가 더 일찍 멈추는 대신 10번의 루프 후에 USDC(amountOut)로 스왑되는 테스트 케이스에서 관찰할 수 있습니다. 스왑 5부터 amountOut은 0입니다:

1.  \_swap - amountIn: 98069305391033220
    \_swap - amountOut: 318441758
2.  \_swap - amountIn: 98069305391033220
    \_swap - amountOut: 318441758
3.  \_swap - amountIn: 46046461845172
    \_swap - amountOut: 149517
4.  \_swap - amountIn: 21720088027
    \_swap - amountOut: 70
5.  \_swap - amountIn: 86364762
    \_swap - amountOut: 0
6.  \_swap - amountIn: 40540410
    \_swap - amountOut: 0
7.  \_swap - amountIn: 19030039
    \_swap - amountOut: 0
8.  \_swap - amountIn: 8932874
    \_swap - amountOut: 0
9.  \_swap - amountIn: 4193173
    \_swap - amountOut: 0
10. \_swap - amountIn: 1968314
    \_swap - amountOut: 0

토큰 소수점 자릿수를 기반으로 임계값을 조정하는 것이 좋습니다.
