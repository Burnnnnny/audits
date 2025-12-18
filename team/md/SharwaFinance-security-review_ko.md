# 소개

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**SharwaFinance/MarginTrading** 저장소에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# Sharwa 소개

Sharwa는 사용자가 Uniswap v3에서 최대 10배 레버리지로 거래할 수 있고 담보의 일부로 Hegic의 미국식 옵션을 사용할 수 있는 마진 거래 프로토콜입니다.

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

_검토 커밋 해시_ - [ec6beb584386f9d72f4a236e3df7cfae90678e41](https://github.com/SharwaFinance/MarginTrading/tree/ec6beb584386f9d72f4a236e3df7cfae90678e41)

_수정 검토 커밋 해시_ - [74e84ef79dec39318a170221dcb7f91eaa804030](https://github.com/SharwaFinance/MarginTrading/tree/74e84ef79dec39318a170221dcb7f91eaa804030)

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `MarginAccount`
- `MarginTrading`
- `LiquidityPool`
- `MarginAccountManager`
- `ModularSwapRouter`
- `HegicModule`
- `UniswapModule`

# 발견 사항

# [C-01] Uniswap 현물 가격 사용은 조작될 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

프로토콜은 Uniswap V3 quoter 컨트랙트를 사용하여 기본 토큰(USDC) 측면에서 지원되는 토큰의 현재 가치를 가져옵니다. quoter가 반환하는 값은 현재 풀 상태를 감안한 시뮬레이션된 스왑의 결과입니다. 즉, 예를 들어 플래시 론을 사용하여 유동성을 추가하고 프로토콜과 상호 작용한 후 제거하는 등 이러한 값은 쉽게 조작될 수 있습니다.

토큰의 견적(quote)은 출금, 대출, 상환 및 청산과 같은 프로토콜의 가장 중요한 부분에서 사용됩니다. 이는 공격자가 현재 토큰 가격을 자신에게 유리하게 조작하여 다른 사용자에게 손실을 입힐 수 있음을 의미합니다.

또한 일부 트랜잭션에는 여러 스왑이 포함됩니다. 이는 스왑의 결과가 다음 스왑에 영향을 미칠 풀의 변화를 일으킬 수 있으며, 이는 quote 프로세스에서 고려되지 않는다는 것을 의미합니다.

## 개념 증명

```solidity
function test_priceManipulation() public {
    provideInitialLiquidity();

    vm.startPrank(alice);
    // Provide 1 WETH = 4_000 USDC
    marginTrading.provideERC20(marginAccountID[alice], address(WETH), 1e18);

    // Simulate price manipulation (2x WETH price in USDC)
    quoter.setSwapPrice(address(WETH), address(USDC), 8_000e6);

    // Borrow 7_000 USDC
    marginTrading.borrow(marginAccountID[alice], address(USDC), 7_000e6);

    // Withdraw 7_000 USDC
    marginTrading.withdrawERC20(marginAccountID[alice], address(USDC), 7_000e6);
    vm.stopPrank();

    // Simulate price manipulation recovery
    quoter.setSwapPrice(address(WETH), address(USDC), 4_000e6);

    // Alice got 3_000 USDC profit and left her position with bad debt
    uint256 accountRatio = marginTrading.getMarginAccountRatio(marginAccountID[alice]);
    assert(accountRatio < 0.6e5);
}
```

## 권고 사항

자산의 가격을 얻기 위해 Chainlink 오라클을 사용하십시오.

# [C-02] 옵션 NFT는 모든 계정에서 행사될 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`exercise` 함수 흐름은 마진 계정이 행사할 토큰을 소유하고 있는지 확인하지 않습니다. 이를 통해 모든 계정이 다른 계정의 옵션을 행사하고 이익을 얻을 수 있습니다.

## 개념 증명

```solidity
function test_exerciseByNotOwner() public {
    uint256 aliceAccountValue = marginTrading.calculateMarginAccountValue(marginAccountID[alice]);
    uint256 bobAccountValue = marginTrading.calculateMarginAccountValue(marginAccountID[bob]);
    uint256 aliceOptionID = optionID[alice];

    vm.prank(alice);
    marginTrading.provideERC721(marginAccountID[alice], address(hegicPositionsManager), aliceOptionID);

    vm.prank(bob);
    marginTrading.exercise(marginAccountID[bob], address(hegicPositionsManager), aliceOptionID);

    uint256 aliceAccountIncrease = marginTrading.calculateMarginAccountValue(marginAccountID[alice]) - aliceAccountValue;
    uint256 bobAccountIncrease = marginTrading.calculateMarginAccountValue(marginAccountID[bob]) - bobAccountValue;
    assert(aliceAccountIncrease == 0);
    assert(bobAccountIncrease == 1_000e6);
}
```

## 권고 사항

```diff
    function exercise(uint marginAccountID, address token, uint collateralTokenID) external nonReentrant onlyApprovedOrOwner(marginAccountID)  {
+       require(marginAccount.checkERC721Value(marginAccountID, token, value), "You are not allowed to execute this ERC721 token");
        marginAccount.exercise(marginAccountID, token, BASE_TOKEN, collateralTokenID, msg.sender);
```

# [C-03] 사용자가 부채 지분(debt shares)을 생성하지 않고 토큰을 빌릴 수 있음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`LiquidityPool` 컨트랙트의 `borrow` 함수는 새로운 대출 금액에 총 부채 지분을 곱하고 총 대출 금액(이자 포함)으로 나누어 대출자에게 크레딧할 부채 지분 금액을 계산합니다.

```solidity
File: LiquidityPool.sol

142:         uint newDebtShare = borrows > 0
143:   @>        ? (debtSharesSum * amount) / borrows
144:             : (amount * 10 ** decimals()) / 10 ** poolToken.decimals();
```

`debtShares * amount`가 `borrows`보다 작은 경우, `amount`가 1일 때 나눗셈은 0으로 내림됩니다. 이를 통해 사용자는 부채 지분을 생성하지 않고 토큰을 빌릴 수 있습니다.

악의적인 사용자는 부채 지분을 생성하지 않고 풀에서 사용 가능한 모든 토큰을 빌려 출금함으로써 풀을 고갈시킬 수 있습니다. 또한 풀의 다른 대출자들은 공격자의 부채를 떠안게 되어 청산당하고 담보를 잃을 수 있습니다.

## 개념 증명

```solidity
function test_debtSharesRoundDown() public {
    uint256 bobInitialWethBalance = WETH.balanceOf(address(bob));

    // Setup
    provideInitialLiquidity();
    vm.startPrank(alice);
    marginTrading.provideERC20(marginAccountID[alice], address(USDC), 10_000e6);
    marginTrading.provideERC20(marginAccountID[alice], address(WETH), 1e18);
    vm.stopPrank();
    vm.startPrank(bob);
    marginTrading.provideERC20(marginAccountID[bob], address(USDC), 10_000e6);
    marginTrading.borrow(marginAccountID[bob], address(WBTC), 100);
    vm.stopPrank();

    // Alice borrows 1 WETH
    vm.prank(alice);
    marginTrading.borrow(marginAccountID[alice], address(WETH), 1e18);

    // Interest accrues over time
    skip(10);

    // Bob borrows 1 wei and is credited 0 debt shares
    vm.prank(bob);
    marginTrading.borrow(marginAccountID[bob], address(WETH), 1);

    // Alice repays debt and interest
    vm.prank(alice);
    marginTrading.repay(marginAccountID[alice], address(WETH), 2e18);

    // Bob borrows huge amount of WETH and is credited 0 debt shares
    for (uint i = 0; i < 20; i++) {
        for (uint j = 0; j < 10; j++) {
            vm.prank(bob);
            marginTrading.borrow(marginAccountID[bob], address(WETH), 10 **i);
        }
    }

    // Bob withdraws all WETH
    uint256 bobAvailableWeth = marginAccount.getErc20ByContract(marginAccountID[bob], address(WETH));
    vm.prank(bob);
    marginTrading.withdrawERC20(marginAccountID[bob], address(WETH), bobAvailableWeth);

    uint256 bobWethProfit = WETH.balanceOf(address(bob)) - bobInitialWethBalance;
    assert(bobWethProfit == bobAvailableWeth);
    assert(bobWethProfit > 100e18);
}
```

## 권고 사항

`borrow` 함수에서 부채 지분 금액을 가장 가까운 정수로 올림하십시오.

```diff
+import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

/**
 * @title LiquidityPool
 * @dev This contract manages a liquidity pool for ERC20 tokens, allowing users to provide liquidity, borrow, and repay loans.
 * @notice Users can deposit tokens to earn interest and borrow against their deposits.
 * @author 0nika0
 */
contract LiquidityPool is ERC20, ERC20Burnable, AccessControl, ILiquidityPool, ReentrancyGuard {
+   using Math for uint;
(...)
        uint newDebtShare = borrows > 0
-           ? (debtSharesSum * amount) / borrows
+           ? debtSharesSum.mulDiv(amount, borrows, Math.Rounding.Up)
            : (amount * 10 ** decimals()) / 10 ** poolToken.decimals();
```

# [C-04] 사용자가 대량의 포지션 NFT를 제공하여 청산을 피할 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

사용자는 `provideERC721` 함수를 사용하여 마진 계정에 임의의 수의 포지션 NFT를 제공할 수 있으며, 각각의 새 토큰은 마진 계정 ID와 연결된 배열에 푸시됩니다(178행):

```solidity
File: MarginAccount.sol
176:     function provideERC721(uint marginAccountID, address txSender, address token, uint collateralTokenID, address baseToken) external onlyRole(MARGIN_TRADING_ROLE) {
177:         require(isAvailableErc721[token], "Token you are attempting to deposit is not available for deposit");
178:         erc721ByContract[marginAccountID][token].push(collateralTokenID);
179:         IERC721(token).transferFrom(txSender, address(this), collateralTokenID);
180:         IERC721(token).approve(modularSwapRouter.getModuleAddress(token, baseToken), collateralTokenID);
181:     }
```

청산 중에 사용자 NFT 배열은 라우터에서 각 NFT에 대한 청산을 실행하는 데 사용됩니다(131행):

```solidity
File: ModularSwapRouter.sol
110:     function liquidate(ERC20PositionInfo[] memory erc20Params, ERC721PositionInfo[] memory erc721Params)
111:         external
112:         onlyRole(MARGIN_ACCOUNT_ROLE)
113:         returns(uint amountOut)
114:     {
115:         address marginTradingBaseToken = marginTrading.BASE_TOKEN();
116:         for (uint i; i < erc20Params.length; i++) {
117:             address moduleAddress = tokenInToTokenOutToExchange[erc20Params[i].tokenIn][erc20Params[i].tokenOut];
118:             if (
119:                 erc20Params[i].tokenIn == marginTradingBaseToken &&
120:                 erc20Params[i].tokenOut == marginTradingBaseToken
121:             ) {
122:                 amountOut += erc20Params[i].value;
123:             } else if (moduleAddress != address(0)) {
124:                 amountOut += IPositionManagerERC20(moduleAddress).liquidate(erc20Params[i].value);
125:             }
126:         }
127:
128:         for (uint i; i < erc721Params.length; i++) {
129:             address moduleAddress = tokenInToTokenOutToExchange[erc721Params[i].tokenIn][erc721Params[i].tokenOut];
130:             if (moduleAddress != address(0)) {
131:                 amountOut += IPositionManagerERC721(moduleAddress).liquidate(erc721Params[i].value, erc721Params[i].holder);
132:             }
133:         }
134:     }
```

이를 통해 사용자는 마진 계정에서 청산이 호출될 때마다 가스 부족(out-of-gas) 오류를 유발하는 대량의 포지션 NFT를 제공함으로써 청산을 피할 수 있는 방법을 갖게 됩니다.

## 권고 사항

청산 중 가스 부족을 방지하기 위해 마진 계정당 포지션 NFT의 최대 수를 제한하는 것을 고려하십시오.

# [H-01] 청산 진행 중인 `MarginAccounts`에 대해 스왑이 가능함

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`MarginTrading.redCoeff` 및 `MarginTrading.yellowCoeff`는 `MarginAccount`가 청산 상태에 있는지 확인하는 데 사용됩니다. 계정이 청산 중인 경우 `borrow` 및 `withdraw`와 같은 작업은 청산 목적을 위해 계정의 무결성을 보존하기 위해 제한됩니다. 그러나 현재 구현에서는 계정의 청산 상태를 확인하지 않고 `swaps`를 계속할 수 있습니다.

```solidity
File: MarginTrading.sol
165:     function swap(uint marginAccountID, address tokenIn, address tokenOut, uint amountIn, uint amountOutMinimum) external nonReentrant onlyApprovedOrOwner(marginAccountID) {
166:         emit Swap(marginAccountID, swapID, tokenIn, tokenOut, amountIn);
167:
168:         marginAccount.swap(marginAccountID, swapID, tokenIn, tokenOut, amountIn, amountOutMinimum);
169:
170:         swapID++;
171:     }
```

악의적인 사용자는 `amountOutMinimum`을 수동으로 설정하여 더 높은 위험의 스왑을 실행할 수 있으며, 이는 청산에 사용할 수 있는 자산의 손실로 이어질 수 있습니다. 또한 유동성이 제한된 풀에서 사용자는 `AmountOutMinimum=0`을 설정하여 아무런 대가 없이 스왑을 실행함으로써 풀을 조작할 수 있으며, 잠재적으로 슬리피지로부터 이익을 얻을 수 있습니다.

## 권고 사항

`swap` 함수를 정상 상태인 `MarginAccounts`로만 제한하는 것을 권장합니다:

```diff
    function swap(uint marginAccountID, address tokenIn, address tokenOut, uint amountIn, uint amountOutMinimum) external nonReentrant onlyApprovedOrOwner(marginAccountID) {
++      uint marginAccountValue = calculateMarginAccountValue(marginAccountID);
++      uint debtWithAccruedInterest = calculateDebtWithAccruedInterest(marginAccountID);
++      uint marginAccountRatio = _calculatePortfolioRatio(marginAccountValue, debtWithAccruedInterest);
++      require(marginAccountRatio >= yellowCoeff, "Cannot swap");
        emit Swap(marginAccountID, swapID, tokenIn, tokenOut, amountIn);

        marginAccount.swap(marginAccountID, swapID, tokenIn, tokenOut, amountIn, amountOutMinimum);

        swapID++;
    }
```

# [H-02] `UniswapModule`에서 잠재적인 스왑 실패

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

청산 프로세스 중에 `MarginAccount`의 모든 자산은 `baseToken`(USDC)으로 변환되고, 각 `LiquidityPool`의 부채는 `MarginAccount`의 `_clearDebtsWithPools` 함수 도움으로 청산됩니다. 충분한 자금을 사용할 수 있는 경우(`MarginAccount#L294-L296`), `modularSwapRouter.swapOutput`을 사용하여 USDC를 유동성 풀 상환에 필요한 정확한 양의 `poolToken`으로 변환합니다:

```solidity
File: MarginAccount.sol
217:     function liquidate(uint marginAccountID, address baseToken, address marginAccountOwner) external onlyRole(MARGIN_TRADING_ROLE) {
...
...
233:         uint amountOutInUSDC = modularSwapRouter.liquidate(erc20Params,erc721Params);
234:
235:         erc20ByContract[marginAccountID][baseToken] += amountOutInUSDC;
236:
237:         _clearDebtsWithPools(marginAccountID, baseToken);
238:     }
```

```solidity
File: MarginAccount.sol
283:     function _clearDebtsWithPools(uint marginAccountID, address baseToken) private {
...
...
294:                 } else {
295:                     uint amountIn = modularSwapRouter.swapOutput(baseToken, availableTokenToLiquidityPool[i], poolDebt);
296:                     erc20ByContract[marginAccountID][baseToken] -= amountIn;
297:                 }
298:                 ILiquidityPool(liquidityPoolAddress).repay(marginAccountID, poolDebt);
....
...
```

`UniswapModule`에서 `swapOutput` 함수는 스왑을 위한 매개변수를 준비하고 `swapRouter.exactOutput`을 실행합니다. 그러나 `amountInMaximum` 매개변수는 항상 0으로 설정됩니다. Uniswap 문서에 따르면 `amountInMaximum`은 필요한 자산으로 스왑하는 데 사용할 수 있는 최대 USDC 양이어야 합니다. 항상 0이므로 스왑은 되돌려지며(revert), USDC를 필요한 `poolToken`으로 변환하지 못합니다:

```solidity
File: UniswapModule.sol
106:     function swapOutput(uint amountOut) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountIn) {
107:         ISwapRouter.ExactOutputParams memory params = _preparationOutputParams(amountOut);
108:
109:         amountIn = getOutputPositionValue(amountOut);
110:         IERC20(tokenInContract).transferFrom(marginAccount, address(this), amountIn);
111:
112:         swapRouter.exactOutput(params);
113:
114:         IERC20(tokenOutContract).transfer(marginAccount, amountOut);
115:     }
```

```solidity
File: UniswapModule.sol
139:     function _preparationOutputParams(uint256 amount) private view returns(ISwapRouter.ExactOutputParams memory params) {
140:         params = ISwapRouter.ExactOutputParams({
141:             path: uniswapPath,
142:             recipient: address(this),
143:             deadline: block.timestamp,
144:             amountOut: amount,
145:             amountInMaximum: 0
146:         });
147:     }
```

이 잘못된 구성으로 인해 스왑이 실패하여 잠재적으로 시스템이 청산 중 부채를 청산할 수 없게 만들 수 있습니다.

## 권고 사항

`UniswapModule::swapOutput` 함수의 `amountInMaximum`을 조정하여 스왑에 예상되는 실제 최대 입력을 반영하는 것이 좋습니다. 이 조정을 통해 입력 금액 부족으로 인해 스왑이 되돌려지지 않도록 합니다:

```diff
    function swapOutput(uint amountOut) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountIn) {
        ISwapRouter.ExactOutputParams memory params = _preparationOutputParams(amountOut);

        amountIn = getOutputPositionValue(amountOut);
++      params.amountInMaximum = amountIn;
        IERC20(tokenInContract).transferFrom(marginAccount, address(this), amountIn);

        swapRouter.exactOutput(params);

        IERC20(tokenOutContract).transfer(marginAccount, amountOut);
    }
```

# [H-03] 부채가 없는 마진 계정을 청산할 수 있음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`MarginTrading` 컨트랙트의 `liquidate` 메서드를 사용하면 마진 비율이 `redCoeff` 임계값 미만인 비정상 계정을 누구나 청산할 수 있습니다. 그러나 부채가 없는 계정(예: 신규 계정 및 갓 자금을 조달한 계정)의 경우 마진 비율이 0이 됩니다. 결과적으로 `0 <= redCoeff` 확인을 통과하고 적대자는 부채가 없는 계정을 청산함으로써 괴롭힐 수 있습니다.

청산 중에 모든 마진 계정의 자산(예: 토큰 및 옵션)은 기본 토큰(일반적으로 `USDC`)으로 변환됩니다. 이는 마진 계정 소유자에게 불쾌할 뿐만 아니라, 예고 없이 불리한 시점에 해당 자산이 청산되어 경제적으로 해로운 영향을 미칠 수 있습니다.

```solidity
function _calculatePortfolioRatio(uint marginAccountValue, uint debtWithAccruedInterest) private pure returns (uint marginAccountRatio) {
    if (debtWithAccruedInterest == 0) {
        return 0;  // @audit ratio is 0 when debt-free
    }
    require(marginAccountValue*COEFFICIENT_DECUMALS > debtWithAccruedInterest, "Margin Account value should be greater than debt with accrued interest");
    marginAccountRatio = marginAccountValue*COEFFICIENT_DECUMALS/debtWithAccruedInterest;
}

function getMarginAccountRatio(uint marginAccountID) public returns(uint) {
    uint marginAccountValue = calculateMarginAccountValue(marginAccountID);
    uint debtWithAccruedInterest = calculateDebtWithAccruedInterest(marginAccountID);
    return _calculatePortfolioRatio(marginAccountValue, debtWithAccruedInterest);  // @audit ratio is 0 when debt-free
}

function liquidate(uint marginAccountID) external {
    require(getMarginAccountRatio(marginAccountID) <= redCoeff, "Margin Account ratio is too high to execute liquidation");  // @audit will pass when ratio is 0
    marginAccount.liquidate(marginAccountID, BASE_TOKEN, marginAccountManager.ownerOf(marginAccountID));

    emit Liquidate(marginAccountID);
}
```

## 개념 증명

위의 청산 괴롭힘 공격을 재현하려면 `margin_trading.spec.ts`에 다음 테스트 케이스를 추가하십시오.

```typescript
it.only("liquidation griefing of debt-free account", async () => {
  await c.MarginAccountManager.connect(c.deployer).createMarginAccount();
  let WETHprovideAmount = parseUnits("1", await c.WETH.decimals());
  let USDCafterLiquidationAmount = parseUnits("4000", await c.USDC.decimals()); // here: 1 ETH = 4000 USD

  // fund margin account with WETH
  await expect(
    c.MarginTrading.connect(c.deployer).provideERC20(
      0,
      await c.WETH.getAddress(),
      WETHprovideAmount
    )
  ).to.changeTokenBalances(
    c.WETH,
    [await c.signers[0].getAddress(), await c.MarginAccount.getAddress()],
    [-WETHprovideAmount, WETHprovideAmount]
  );
  // expect account to be funded with WETH
  expect(
    await c.MarginAccount.getErc20ByContract(0, await c.WETH.getAddress())
  ).to.be.eq(WETHprovideAmount);

  // griefer liquidates account
  await c.MarginTrading.connect(c.signers[9]).liquidate(0);

  // expect account to be liquidated to USDC
  expect(
    await c.MarginAccount.getErc20ByContract(0, await c.USDC.getAddress())
  ).to.be.eq(USDCafterLiquidationAmount);
});
```

## 권고 사항

`liquidate` 메서드에 부채가 없는 시나리오에 대한 추가 확인을 추가하는 것이 좋습니다.

```solidity
function liquidate(uint marginAccountID) external {
    uint ratio = getMarginAccountRatio(marginAccountID);
    require(ratio > 0, "Margin Account is debt-free");
    require(ratio <= redCoeff, "Margin Account ratio is too high to execute liquidation");
    marginAccount.liquidate(marginAccountID, BASE_TOKEN, marginAccountManager.ownerOf(marginAccountID));

    emit Liquidate(marginAccountID);
}
```

# [H-04] 인출 시 누적 이자가 계산되지 않음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

사용자가 `LiquidityPool`에 유동성을 제공할 때 차용인이 발생시킨 부채에 대한 이자는 사용자의 풀 지분 계산에 고려됩니다. 그러나 사용자가 풀에서 인출할 때 누적 이자는 계산되지 않습니다.

```solidity
File: LiquidityPool.sol

097:     function provide(uint amount) external nonReentrant {
098:  @>     uint totalLiquidity = getTotalLiquidity();
099:         require(
100:             totalLiquidity + amount <= maximumPoolCapacity,
101:             "Maximum liquidity has been achieved!"
102:         );
103:         poolToken.transferFrom(msg.sender, address(this), amount);
104:         uint shareChange = totalLiquidity > 0
105:             ? (depositShare * amount) / totalLiquidity
106:             : (amount * 10 ** decimals()) / 10 ** poolToken.decimals();
(...)
113:     function withdraw(uint amount) external nonReentrant {
114:  @>     uint totalLiquidity = poolToken.balanceOf(address(this)) + netDebt;
115:         require(totalLiquidity != 0, "Liquidity pool has no pool tokens");
116:         uint amountWithdraw = (amount * totalLiquidity) / depositShare;
```

다음 예를 고려해 보십시오:

- 풀에 10 WETH가 예치되어 있고 10 LP 토큰이 발행되었습니다.
- 누군가 5 WETH를 빌리고 시간이 지남에 따라 이자가 발생하여 또 5 WETH에 도달합니다.
- 사용자가 15 WETH를 제공합니다.
- `totalLiquidity`는 5 WETH(컨트랙트 잔액) + 5 WETH(순부채) + 5 WETH(누적 이자) = 15 WETH입니다.
- 사용자는 `(depositShare * amount) / totalLiquidity` = 10 LP \* 15 WETH / 15 WETH = 10 LP를 받습니다.
- 사용자가 10 LP를 인출합니다.
- `totalLiquidity`는 20 WETH(컨트랙트 잔액) + 5 WETH(순부채) = 25 WETH입니다.
- 사용자는 `(amount * totalLiquidity) / depositShare` = 10 LP \* 25 WETH / 20 LP = 12.5 WETH를 받습니다.

## 개념 증명

```solidity
function test_missingAccruedInterestInWithdrawal() public {
    // Alice provides 1 WETH
    vm.prank(alice);
    liquidityPoolWETH.provide(1e18);

    // Bob borrows 0.8 WETH
    vm.startPrank(bob);
    marginTrading.provideERC20(marginAccountID[bob], address(USDC), 10_000e6);
    marginTrading.borrow(marginAccountID[bob], address(WETH), 0.8e18);
    vm.stopPrank();

    // Interest accrues over time
    skip(60 * 60 * 24 * 365);

    // Charlie provides 1 WETH
    vm.prank(charlie);
    liquidityPoolWETH.provide(1e18);
    uint256 charlieLpTokens = liquidityPoolWETH.balanceOf(address(charlie));

    // Charlie withdraws all from liquidity pool, receiving less WETH than his original deposit
    uint256 charlieWETHBalance = WETH.balanceOf(address(charlie));
    vm.prank(charlie);
    liquidityPoolWETH.withdraw(charlieLpTokens);
    uint256 charlieWETHReceived = WETH.balanceOf(address(charlie)) - charlieWETHBalance;
    assert(charlieWETHReceived < 0.981e18);
}
```

## 권고 사항

```diff
    function withdraw(uint amount) external nonReentrant {
-       uint totalLiquidity = poolToken.balanceOf(address(this)) + netDebt;
+       uint totalLiquidity = getTotalLiquidity();
        require(totalLiquidity != 0, "Liquidity pool has no pool tokens");
        uint amountWithdraw = (amount * totalLiquidity) / depositShare;
```

# [H-05] 옵션 이익의 기본 토큰 스왑이 올바르게 처리되지 않음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`HegicModule.sol`의 `liquidate` 함수는 매개변수로 받은 옵션을 행사합니다. 각 옵션이 내가격(in the money)인 경우:

1. `getOptionValue` 함수를 호출하여 이익을 계산합니다. 아래 코드 조각에서 볼 수 있듯이 이익은 USDCe에서 USDC로 변환됩니다.
2. 옵션을 마진 계정에서 컨트랙트로 전송하고 행사하여 마진 계정을 이익 수령자로 설정합니다.
3. `assetExchangerUSDCetoUSDC` 컨트랙트를 사용하여 이익을 USDCe에서 USDC로 스왑하며, 입력 금액으로 `profit`을 전달합니다.

```solidity
File: HegicModule.sol

45:     function liquidate(uint[] memory value, address holder) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountOut) {
46:         for (uint i; i < value.length; i++) {
47:             if (getPayOffAmount(value[i]) > 0 && isOptionActive(value[i]) && getExpirationTime(value[i]) > block.timestamp) {
48:   @>            uint profit = getOptionValue(value[i]);
49:                 hegicPositionManager.transferFrom(marginAccount, address(this), value[i]);
50:                 operationalTreasury.payOff(value[i], marginAccount);
51:   @>            amountOut += assetExchangerUSDCetoUSDC.swapInput(profit, 0);
52:                 hegicPositionManager.transferFrom(address(this), holder, value[i]);
53:             }
64:         }
55:     }
(...)
76:     function getOptionValue(uint id) public returns (uint positionValue) {
77:         if (isOptionActive(id) && getExpirationTime(id) > block.timestamp) {
78:             uint profit = getPayOffAmount(id);
79:   @>        positionValue = assetExchangerUSDCetoUSDC.getInputPositionValue(profit);
80:         }
81:     }
```

문제는 `profit`이 USDC 가격인 반면 입력 금액은 USDCe여야 한다는 것입니다. 즉, `assetExchangerUSDCetoUSDC.swapInput`은 마진 계정에서 `profit`만큼의 USDCe를 가져오려고 하지만, USDCe 가격이 정확히 1이 아닌 한 마진 계정은 다른 양의 USDCe를 받게 됩니다.

다음 예를 고려해 보십시오:

- 1 USDCe = 1.001 USDC
- 옵션 이익은 1,000 USDCe입니다. 이는 마진 계정이 받을 금액입니다.
- `getOptionValue`는 이익을 USDC로 변환하며, 이는 1,000 \* 1.001 = 1,001 USDC입니다.
- `swapInput`은 마진 계정에서 1,001 USDCe를 가져오려고 하지만 계정에는 1,000 USDCe만 있습니다.

유사한 로직을 가진 `exercise` 함수에도 동일한 문제가 존재합니다.

이 문제의 결과는 USDCe 가격이 1 USDC보다 높은 경우 청산 및 행사 함수가 되돌려지고(revert), USDCe 가격이 1 USDC보다 낮은 경우 스왑이 예상보다 적은 금액으로 수행된다는 것입니다.

## 개념 증명

```solidity
function test_swapInputFailsInLiquidation() public {
    provideInitialLiquidity();
    quoter.setSwapPrice(address(USDCe), address(USDC), 1.001e6);

    vm.startPrank(alice);
    marginTrading.provideERC721(marginAccountID[alice], address(hegicPositionsManager), optionID[alice]);
    marginTrading.borrow(marginAccountID[alice], address(USDC), 900e6);
    marginTrading.withdrawERC20(marginAccountID[alice], address(USDC), 900e6);
    vm.stopPrank();
    hegicStrategy.setPayOffAmount(optionID[alice], 800e6);

    vm.expectRevert("ERC20: transfer amount exceeds balance");
    marginTrading.liquidate(marginAccountID[alice]);
}
```

## 권고 사항

```diff
    function liquidate(uint[] memory value, address holder) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountOut) {
        for (uint i; i < value.length; i++) {
+            uint profit = getPayOffAmount(value[i]);
-            if (getPayOffAmount(value[i]) > 0 && isOptionActive(value[i]) && getExpirationTime(value[i]) > block.timestamp) {
+            if (profit > 0 && isOptionActive(value[i]) && getExpirationTime(value[i]) > block.timestamp) {
-               uint profit = getOptionValue(value[i]);
                hegicPositionManager.transferFrom(marginAccount, address(this), value[i]);
                operationalTreasury.payOff(value[i], marginAccount);
                amountOut += assetExchangerUSDCetoUSDC.swapInput(profit, 0);
                hegicPositionManager.transferFrom(address(this), holder, value[i]);
            }
        }
    }

    function exercise(uint id) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountOut) {
        require(getPayOffAmount(id) > 0 && isOptionActive(id) && getExpirationTime(id) > block.timestamp, "The option is not active or there is no profit on it");
-       uint profit = getOptionValue(id);
+       uint profit = getPayOffAmount(id);
        hegicPositionManager.transferFrom(marginAccount, address(this), id);
        operationalTreasury.payOff(id, marginAccount);
        amountOut = assetExchangerUSDCetoUSDC.swapInput(profit, 0);
        hegicPositionManager.transferFrom(address(this), marginAccount, id);
    }
```

# [M-01] 설명되지 않은 자산 제거의 리스크

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`MarginAccount.availableErc20` 목록은 `MarginAccount::preparationTokensParams`(계정 가치 평가에 도움) 및 `MarginAccount::liquidate`와 같은 함수에 중요합니다. `availableErc20` 목록에서 토큰을 제거하면 사용자가 부채를 충당하기 위해 남은 활성 토큰만 사용하여 청산될 수 있습니다:

```solidity
File: MarginAccount.sol
217:     function liquidate(uint marginAccountID, address baseToken, address marginAccountOwner) external onlyRole(MARGIN_TRADING_ROLE) {
218:         IModularSwapRouter.ERC20PositionInfo[] memory erc20Params = new IModularSwapRouter.ERC20PositionInfo[](availableErc20.length);
219:         IModularSwapRouter.ERC721PositionInfo[] memory erc721Params = new IModularSwapRouter.ERC721PositionInfo[](availableErc721.length);
220:
221:         for(uint i; i < availableErc20.length; i++) {
222:             uint erc20Balance = erc20ByContract[marginAccountID][availableErc20[i]];
223:             erc20Params[i] = IModularSwapRouter.ERC20PositionInfo(availableErc20[i], baseToken, erc20Balance);
224:             erc20ByContract[marginAccountID][availableErc20[i]] -= erc20Balance;
225:         }
226:
227:         for(uint i; i < availableErc721.length; i++) {
228:             uint[] memory erc721TokensByContract = erc721ByContract[marginAccountID][availableErc721[i]];
229:             erc721Params[i] = IModularSwapRouter.ERC721PositionInfo(availableErc721[i], baseToken, marginAccountOwner, erc721TokensByContract);
230:             delete erc721ByContract[marginAccountID][availableErc721[i]];
231:         }
232:
233:         uint amountOutInUSDC = modularSwapRouter.liquidate(erc20Params,erc721Params);
234:
235:         erc20ByContract[marginAccountID][baseToken] += amountOutInUSDC;
236:
237:         _clearDebtsWithPools(marginAccountID, baseToken);
238:     }
```

토큰이 비활성화되어 `availableErc20`에서 제거되면 청산 중 부채 정산 프로세스에서 사용되지 않습니다. 이러한 간과로 인해 `InsurancePool`이 부족분을 커버하게 될 수 있습니다:

```solidity
File: MarginAccount.sol
282:      */
283:     function _clearDebtsWithPools(uint marginAccountID, address baseToken) private {
...
293:                     IERC20(availableTokenToLiquidityPool[i]).transferFrom(insurancePool, address(this), poolDebt-amountOut);
...
301:     }
```

## 권고 사항

`MarginAccount::setAvailableErc20` 함수 내에서 `MarginAccount.availableErc20` 목록에서 토큰을 제거하는 것을 방지하는 것이 좋습니다. 원래 자산으로 간주된 모든 토큰이 계정의 수명 주기 동안 책임을 지도록 하면 보험 풀에 대한 예상치 못한 부담을 방지할 수 있습니다.

# [M-02] 청산 호출에 대한 인센티브 부족

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`liquidation`은 다음과 같이 작동합니다:

1. `marginAccount`가 소유한 모든 담보 잔액은 `MarginAccount#L221-L231` 코드 라인에서 검색됩니다.
2. 자산은 `ModularSwapRouter::liquidate` 함수를 통해 USDC로 청산되어 계정의 보유 자산이 더 유동적인 형태로 변환됩니다. 코드 라인 `MarginAccount#L233`.
3. 해당 `LiquidityPools`의 부채는 청산(2단계)에서 획득한 USDC를 사용하여 `MarginAccount#L237` 코드 라인에서 청산됩니다. 부채가 청산 수익금을 초과하면 `insurancePool`에서 추가 자금을 인출하여 부족분을 충당합니다(`MarginAccount#L293`).

문제는 시스템의 재정 건전성을 유지하는 데 중요한 `MarginTrading::liquidate` 함수를 호출하는 데 대한 인센티브가 없다는 것입니다. 청산 시작에 대한 인센티브 부족은 여러 가지 위험을 초래합니다:

1. 마진 계정 내의 담보 가치가 급락하고 청산이 적시에 트리거되지 않으면 계정이 지급 불능 상태가 되어 **`InsurancePool` 컨트랙트의 지급 능력을 위협할 수 있습니다**.
2. 지연된 청산은 특히 시장 상황이 변동함에 따라 재정적 위험에 대한 노출을 증가시켜 잠재적으로 담보 가치를 더욱 떨어뜨릴 수 있습니다.
3. 여러 계정의 동시 지급 불능은 전체 시스템을 불안정하게 만들 수 있으며, 이는 사전 예방적 청산 관리의 중요성을 강조합니다.

## 권고 사항

청산인에 대한 인센티브를 구현하는 것이 좋습니다. 여기에는 청산된 자산의 일정 비율을 호출자에게 제공하도록 청산 프로세스를 조정하여 부채를 충당하는 동시에 시스템 리스크를 효과적으로 관리하는 데 대한 보상을 제공하는 것이 포함될 수 있습니다.

# [M-03] 잠재적인 청산 회피

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

사용자는 `MarginTrading::provideERC721` 함수를 사용하여 ERC721 토큰을 예치할 수 있으며, 이는 대출을 위한 담보로 사용될 수 있습니다. 또한 사용자는 `MarginTrading::exercise` 함수를 실행하여 ERC721을 `baseToken` 가치로 변환하고 해당 `marginAccount`에 크레딧을 제공할 수 있습니다.

문제는 악의적인 사용자가 프로토콜 컨텍스트 내에서 유효하지 않은(예: 비활성, 만료 또는 가치 없는) ERC721 토큰을 예치할 때 발생합니다:

```solidity
File: HegicModule.sol
68:     function checkValidityERC721(uint id) external returns(bool) {
69:         if (getPayOffAmount(id) > 0 && isOptionActive(id) && getExpirationTime(id) > block.timestamp) {
70:             return true;
71:         }
72:     }
```

다음 시나리오를 고려해 보십시오:

1. 사용자가 `MarginTrading::provideERC721`을 사용하여 여러 개의 유효하지 않은 ERC721 토큰(지불 없음, 비활성 또는 만료됨)을 예치합니다.
2. 사용자가 청산 대상이 되고, 청산인이 `MarginTrading::liquidate` 함수를 호출하여 `MarginAccount#L228`에서 ERC721 토큰을 검색합니다.

```solidity
File: MarginAccount.sol
217:     function liquidate(uint marginAccountID, address baseToken, address marginAccountOwner) external onlyRole(MARGIN_TRADING_ROLE) {
...
...
227:         for(uint i; i < availableErc721.length; i++) {
228:             uint[] memory erc721TokensByContract = erc721ByContract[marginAccountID][availableErc721[i]];
229:             erc721Params[i] = IModularSwapRouter.ERC721PositionInfo(availableErc721[i], baseToken, marginAccountOwner, erc721TokensByContract);
230:             delete erc721ByContract[marginAccountID][availableErc721[i]];
231:         }
...
...
238:     }
```

3. 그런 다음 `HegicModule#L46`에서 각 토큰 ID를 개별적으로 확인합니다:

```solidity
File: HegicModule.sol
45:     function liquidate(uint[] memory value, address holder) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountOut) {
46:         for (uint i; i < value.length; i++) {
47:             if (getPayOffAmount(value[i]) > 0 && isOptionActive(value[i]) && getExpirationTime(value[i]) > block.timestamp) {
48:                 uint profit = getOptionValue(value[i]);
49:                 hegicPositionManager.transferFrom(marginAccount, address(this), value[i]);
50:                 operationalTreasury.payOff(value[i], marginAccount);
51:                 amountOut += assetExchangerUSDCetoUSDC.swapInput(profit, 0);
52:                 hegicPositionManager.transferFrom(address(this), holder, value[i]);
53:             }
64:         }
55:     }
```

이로 인해 사용자가 더 많은 유효하지 않은 토큰 ID를 도입함에 따라 청산 프로세스가 더 비용이 많이 들고 복잡해집니다.

다음 테스트는 행사된 토큰 ID가 `MarginAccount`에 어떻게 다시 도입될 수 있는지 보여줍니다. 이는 토큰 ID가 향후 청산에서 사용할 수 없고 인출될 수도 없기 때문에 불필요합니다(이는 사용자가 유효하지 않은 ERC721을 얻을 수 있는 방법의 예이며, 다른 소스에서 유효하지 않은 ERC721을 얻을 수도 있습니다):

```solidity
  describe("Exercise: provide exercised ERC721", async () => {
    let optionId: bigint
    let marginAccountID: bigint
    it("exercise: provide exercised ERC721", async () => {
      //
      // 1. Provide ERC721 to the marginAccount
      await c.MarginAccountManager.connect(c.deployer).createMarginAccount()
      optionId = BigInt(0)
      marginAccountID = BigInt(0)
      await c.MarginTrading.connect(c.deployer).provideERC721(
        marginAccountID,
        await c.HegicPositionsManager.getAddress(),
        optionId)
      expect(await c.HegicPositionsManager.ownerOf(optionId)).to.be.eq(await c.MarginAccount.getAddress());
      //
      // 1. Excercise a ERC721
      const marginAccountValue = await c.MarginTrading.calculateMarginAccountValue.staticCall(marginAccountID)
      expect(await c.MarginAccount.getErc20ByContract(marginAccountID, c.USDC.getAddress())).to.be.eq(BigInt(0))
      expect(await c.MarginAccount.getErc721ByContract(
        marginAccountID, c.HegicPositionsManager.getAddress())).to.be.eql([BigInt(0)])
      await c.MarginTrading.connect(c.deployer).exercise(
        marginAccountID, await c.HegicPositionsManager.getAddress(), optionId)
      expect(await c.MarginTrading.calculateMarginAccountValue.staticCall(marginAccountID)).to.be.eq(marginAccountValue)
      expect(await c.MarginAccount.getErc20ByContract(marginAccountID, c.USDC.getAddress())).to.be.eq(marginAccountValue)
      expect(await c.MarginAccount.getErc721ByContract(marginAccountID, c.HegicPositionsManager.getAddress())).to.be.eql([])
      //
      // 2. Provide again the same exercised ERC721 to the marginAccount
      expect(await c.HegicPositionsManager.ownerOf(optionId)).to.be.eq(await c.deployer.getAddress());
      await c.HegicPositionsManager.approve(await c.MarginAccount.getAddress(), optionId)
      await c.MarginTrading.connect(c.deployer).provideERC721(
        marginAccountID,
        await c.HegicPositionsManager.getAddress(),
        optionId)
      //
      // 3. MarginAccount has an exercised ERC721 that is unusable. Additionally the ERC721 can't be withdrawn
      expect(await c.MarginAccount.getErc721ByContract(
        marginAccountID, c.HegicPositionsManager.getAddress())).to.be.eql([BigInt(0)])
      expect(await c.HegicPositionsManager.ownerOf(optionId)).to.be.eq(await c.MarginAccount.getAddress());
      await expect(
        c.MarginTrading.connect(c.deployer).withdrawERC721(
          marginAccountID, await c.HegicPositionsManager.getAddress(), optionId)
      ).to.be.revertedWith("token id is not valid")
    })
  })
```

## 권고 사항

`MarginTrading::provideERC721`이 유효한 토큰 ID만 수락하도록 권장합니다:

```diff
File: MarginTrading.sol
    function provideERC721(uint marginAccountID, address token, uint collateralTokenID) external nonReentrant onlyApprovedOrOwner(marginAccountID) {
++      require(modularSwapRouter.checkValidityERC721(token, BASE_TOKEN, collateralTokenID), "token id is not valid");
        marginAccount.provideERC721(marginAccountID, msg.sender, token, collateralTokenID, BASE_TOKEN);

        emit ProvideERC721(marginAccountID, msg.sender, token, collateralTokenID);
    }
```

# [M-04] 유동성 풀 이자 발생이 조작될 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`LiquidityPool` 컨트랙트의 `borrow` 메서드를 호출할 때마다 `_fixAccruedInterest` 메서드가 호출되어 `totalBorrowsSnapshotTimestamp`를 현재 `block.timestamp`로 업데이트합니다. 빈번한 호출의 경우, 이로 인해 `totalBorrows` 메서드의 `ownershipTime` 값이 낮아지며(최악의 경우 1), 이는 최종 `totalBorrows` / 이자 스냅샷 계산에서 정밀도 손실(precision loss)로 이어집니다. 따라서 결과적인 풀 이자가 과소평가됩니다.

적대자는 `MarginTrading` 컨트랙트를 통해 `borrow` 메서드를 0 금액으로 자주 호출하여 풀의 이자 발생을 조작함으로써 위의 동작을 악용할 수 있으며, 이는 다음과 같은 영향을 미칩니다:

- 적대자가 대출자이기도 한 경우 상환할 이자 금액을 줄일 수 있습니다. 높은 대출 이자 금액과 L2의 낮은 트랜잭션 수수료의 경우 이는 수익성이 있을 수 있습니다.
- 풀 제공자는 이자 감소로 인해 예상 수익을 거부당함으로써 피해를 입을 수 있습니다.

```solidity
function totalBorrows() public view returns (uint) {
    uint ownershipTime = block.timestamp - totalBorrowsSnapshotTimestamp;  // @audit is 1 in worst case on repeated calls
    uint precision = 10 ** 18;
    UD60x18 temp = div(
        convert(
            ((INTEREST_RATE_COEFFICIENT + interestRate) *
                precision) / INTEREST_RATE_COEFFICIENT
        ),
        convert(precision)
    );
    return
        ((netDebt + totalInterestSnapshot) *
            intoUint256(pow(temp, div(convert(ownershipTime), convert(ONE_YEAR_SECONDS))))) / 1e18;  // @audit precision loss on low 'ownershipTime' and final division by 1e18
}

function _fixAccruedInterest() private returns (uint) {
    uint newTotalBorrows = totalBorrows();  // @audit updates with understated value in case of lost precision
    totalInterestSnapshot = newTotalBorrows - netDebt;
    totalBorrowsSnapshotTimestamp = block.timestamp;
    return newTotalBorrows;
}
```

## 개념 증명

위의 이자 조작 공격을 재현하려면 `liquidity_pool.spec.ts`에 다음 테스트 케이스를 추가하십시오.

```typescript
it.only("Expect interest to be correctly accrued despite repeated calls to borrow with zero amount", async function () {
  await USDC.connect(firstInvestor).transfer(
    await marginTrading.getAddress(),
    ethers.parseUnits("50", 6)
  );

  // Investor provides 3000 USDC to pool
  const amountFirstInvestor = ethers.parseUnits("3000", 6);
  await USDC.connect(firstInvestor).approve(
    liquidityPoolUSDC.getAddress(),
    amountFirstInvestor
  );
  await liquidityPoolUSDC.connect(firstInvestor).provide(amountFirstInvestor);

  // Trader borrows 1000 USDC
  const firstTraderDebtAmount = ethers.parseUnits("1000", 6);
  await marginTrading
    .connect(firstTrader)
    .borrow(ethers.parseUnits("1", 0), firstTraderDebtAmount);

  const timeBefore = await time.latest();
  // OK case: Wait 1h
  // await time.increaseTo(await time.latest() + 60 * 60);
  // .. or ..
  // Manipulated case: Trader borrows with zero amount for 1h
  for (let i = 0; i < 60 * 60; i++) {
    await marginTrading
      .connect(firstTrader)
      .borrow(ethers.parseUnits("1", 0), 0);
  }
  const timeAfter = await time.latest();
  // Benchmark elapsed time to make sure both cases take equally long
  console.log(timeAfter - timeBefore);

  // Trader repays entire debt
  const returnAmount = await liquidityPoolUSDC.getDebtWithAccruedInterestOnTime(
    ethers.parseUnits("1", 0),
    (await time.latest()) + 1
  );
  await marginTrading.connect(firstTrader).repay(
    ethers.parseUnits("1", 0),
    returnAmount, // repayment of the entire debt, it is expected that MarginTrading will independently call this function
    await USDC.getAddress(),
    returnAmount
  );

  // Expect interest to be correctly accrued
  const accruedInterest = returnAmount - firstTraderDebtAmount;
  expect(accruedInterest).to.equal(ethers.parseUnits("5571", 0));
});
```

이 테스트 케이스는 예상 동작과 비교할 때 누적 이자가 약 35% 감소함을 드러내기 위해 실패하도록 의도되었습니다.

## 권고 사항

제시된 공격 벡터의 경제적 타당성을 줄이기 위해 `borrow` 메서드에서 0이 아닌 금액을 `require`하는 것이 좋습니다.

# [M-05] 전액 상환 후에도 대출자에게 부채 지분이 남을 수 있음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

대출자는 부채를 전액 상환한 후에도 부채 지분을 남길 수 있습니다. 이는 `shareChange` 값 계산이 내림되기 때문입니다.

```solidity
File: LiquidityPool.sol

155:     function repay(uint marginAccountID, uint amount) external onlyRole(MARGIN_ACCOUNT_ROLE) {
(...)
164:  @>     uint shareChange = (amount * debtSharesSum) / newTotalBorrows; // Trader's share to be given away
165:         uint profit = (accruedInterest * shareChange) / shareOfDebt[marginAccountID];
166:         uint profitInsurancePool = (profit * insuranceRateMultiplier) / INTEREST_RATE_COEFFICIENT;
167:         totalInterestSnapshot -= totalInterestSnapshot * shareChange / debtSharesSum;
168:         debtSharesSum -= shareChange;
169:         shareOfDebt[marginAccountID] -= shareChange;
```

## 개념 증명

```solidity
function test_borrowerKeepsDebtSharesAfterRepay() public {
    provideInitialLiquidity();

    vm.startPrank(alice);
    marginTrading.provideERC20(marginAccountID[alice], address(USDC), 10_000e6);
    marginTrading.provideERC20(marginAccountID[alice], address(WETH), 1e18);
    marginTrading.borrow(marginAccountID[alice], address(WETH), 1e18);
    vm.stopPrank();

    vm.startPrank(bob);
    marginTrading.provideERC20(marginAccountID[bob], address(USDC), 10_000e6);
    marginTrading.borrow(marginAccountID[bob], address(WETH), 0.1e18);
    vm.stopPrank();

    skip(10);

    // Alice repays all debt plus interest
    vm.startPrank(alice);
    marginTrading.repay(marginAccountID[alice], address(WETH), 2e18);

    // Alice keeps debt shares after full repayment
    uint256 aliceDebtAmount = liquidityPoolWETH.portfolioIdToDebt(marginAccountID[alice]);
    uint256 aliceDebtShares = liquidityPoolWETH.shareOfDebt(marginAccountID[alice]);
    assert(aliceDebtAmount == 0);
    assert(aliceDebtShares > 0);
}
```

## 권고 사항

개별 부채 금액과 개별 부채 지분을 모두 추적하는 것은 평가에 불일치를 일으키고 예상치 못한 동작으로 이어질 수 있으므로 그 필요성을 재평가하는 것이 좋습니다.

# [M-06] 대출자가 미지불된 먼지(dust)를 남길 수 있음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`MarginTrading` 컨트랙트의 `withdrawERC20`, `withdrawERC721`, `borrow` 및 `liquidate` 함수는 포트폴리오 비율을 계산하여 계정이 담보 부족 상태인지 확인합니다. 이 비율은 계정이 보유한 자산 가치를 부채 가치로 나누어 계산하며, 둘 다 기본 통화(USDC)로 표시됩니다.

```solidity
File: MarginTrading.sol

196:     function _calculatePortfolioRatio(uint marginAccountValue, uint debtWithAccruedInterest) private pure returns (uint marginAccountRatio) {
197:         if (debtWithAccruedInterest == 0) {
198:             return 0;
199:         }
200:         require(marginAccountValue*COEFFICIENT_DECUMALS > debtWithAccruedInterest, "Margin Account value should be greater than debt with accrued interest");
201:         marginAccountRatio = marginAccountValue*COEFFICIENT_DECUMALS/debtWithAccruedInterest;
202:     }
```

`debtWithAccruedInterest`는 계정이 빌린 다양한 토큰을 반복하고, 지불할 금액을 기본 토큰으로 변환하고, 결과 값을 합산하여 계산됩니다.

```solidity
File: ModularSwapRouter.sol

82:     function calculateTotalPositionValue(ERC20PositionInfo[] memory erc20Params, ERC721PositionInfo[] memory erc721Params)
83:         external
84:         onlyRole(MARGIN_TRADING_ROLE)
85:         returns (uint totalValue)
86:     {
87:         address marginTradingBaseToken = marginTrading.BASE_TOKEN();
88:         for (uint i; i < erc20Params.length; i++) {
89:             address moduleAddress = tokenInToTokenOutToExchange[erc20Params[i].tokenIn][erc20Params[i].tokenOut];
90:             if (
91:                 erc20Params[i].tokenIn == marginTradingBaseToken &&
92:                 erc20Params[i].tokenOut == marginTradingBaseToken
93:             ) {
94:                 totalValue += erc20Params[i].value;
95:             } else if (moduleAddress != address(0)) {
96:   @>            totalValue += IPositionManagerERC20(moduleAddress).getInputPositionValue(erc20Params[i].value);
97:             }
98:         }
```

`getInputPositionValue`는 주어진 토큰에 대한 부채 가치를 기본 토큰으로 표시하여 반환합니다. 부채 토큰의 양이 적은 경우, 이 변환은 0으로 내림되어 사실상 부채를 0으로 평가하므로 사용자는 남은 부채를 상환하지 않고 담보를 인출할 수 있습니다.

이 금액은 적지만, 시간이 지남에 따라 다른 사용자와 토큰에 의해 계속 누적될 수 있습니다.

## 개념 증명

```solidity
function test_borrowerLeavesUnpaidDust() public {
    uint256 collateralAmount = 10_000e6;
    uint256 borrowAmount = 1e18;
    uint256 dustAmount = 249_999_999;

    provideInitialLiquidity();

    vm.startPrank(alice);
    marginTrading.provideERC20(marginAccountID[alice], address(USDC), collateralAmount);

    // Alice borrows 1 WETH
    marginTrading.borrow(marginAccountID[alice], address(WETH), borrowAmount);

    // Alice repays debt and interest
    marginTrading.repay(marginAccountID[alice], address(WETH), borrowAmount - dustAmount);

    // Alice withdraws collateral and dust
    marginTrading.withdrawERC20(marginAccountID[alice], address(USDC), collateralAmount);
    marginTrading.withdrawERC20(marginAccountID[alice], address(WETH), dustAmount);
}
```

## 권고 사항

가능한 해결책은 사용자가 모든 담보를 인출하도록 허용하기 전에 부채 토큰으로 평가된 미결제 부채도 0인지 확인하는 것입니다.

# [M-07] 신규 유동성 제공자에게 부당하게 비용이 청구됨

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

사용자가 `LiquidityPool`에 유동성을 제공할 때 총 유동성은 풀 토큰 잔액과 총 대출 금액의 합계로 계산됩니다. 총 대출 금액에는 순부채와 누적 이자가 포함됩니다. 그러나 이자의 일부가 보험 풀로 전송된다는 점은 고려되지 않습니다.

보험 풀로 전송된 이자를 총 유동성 계산에 포함함으로써 이 금액의 일부가 신규 유동성 제공자의 지분에서 차감됩니다.

```solidity
File: LiquidityPool.sol

097:     function provide(uint amount) external nonReentrant {
098:  @>     uint totalLiquidity = getTotalLiquidity();
099:         require(
100:             totalLiquidity + amount <= maximumPoolCapacity,
101:             "Maximum liquidity has been achieved!"
102:         );
103:         poolToken.transferFrom(msg.sender, address(this), amount);
104:         uint shareChange = totalLiquidity > 0
105:             ? (depositShare * amount) / totalLiquidity
106:             : (amount * 10 ** decimals()) / 10 ** poolToken.decimals
(...)
214:     function totalBorrows() public view returns (uint) {
215:         uint ownershipTime = block.timestamp - totalBorrowsSnapshotTimestamp;
216:         uint precision = 10 ** 18;
217:         UD60x18 temp = div(
218:             convert(
219:                 ((INTEREST_RATE_COEFFICIENT + interestRate) *
220:                     precision) / INTEREST_RATE_COEFFICIENT
221:             ),
222:             convert(precision)
223:         );
224:         return
225:             ((netDebt + totalInterestSnapshot) *
226:                 intoUint256(pow(temp, div(convert(ownershipTime), convert(ONE_YEAR_SECONDS))))) / 1e18;
227:     }
228:
229:     function getTotalLiquidity() public view returns (uint) {
230:  @>     return poolToken.balanceOf(address(this)) + totalBorrows();
231:     }
```

다음 예를 고려해 보십시오:

- 제공자 A가 풀에 10 WETH를 예치하고 10 LP 토큰을 받습니다.
- 차용인이 5 WETH를 빌리고 시간이 지남에 따라 이자가 발생하여 또 5 WETH에 도달합니다.
- 제공자 B가 15 WETH를 제공합니다.
- `totalLiquidity`는 5 WETH(컨트랙트 잔액) + 5 WETH(순부채) + 5 WETH(누적 이자) = 15 WETH입니다.
- 제공자 B는 `(depositShare * amount) / totalLiquidity` = 10 LP \* 15 WETH / 15 WETH = 10 LP를 받습니다.
- 차용인이 5 WETH 부채와 5 WETH 이자를 상환하고, 1 WETH가 보험 풀로 이동합니다.
- 제공자 B가 10 LP를 인출합니다.
- `totalLiquidity`는 29 WETH(컨트랙트 잔액)입니다.
- 제공자 B는 `(amount * totalLiquidity) / depositShare` = 10 LP \* 29 WETH / 20 LP = 14.5 WETH를 받습니다.
- 제공자 B는 0.5 WETH를 잃었습니다.
- 제공자 A가 10 LP를 인출하고 14.5 WETH를 받습니다.
- 제공자 A는 14 WETH(10 WETH 예치 + 4 WETH 이자)를 받아야 했지만, 제공자 B에게서 가져간 0.5 추가 WETH를 받았습니다.

## 개념 증명

```solidity
function test_newLpIsChargedForInsuranceFee() public {
    // Alice provides 10 WETH
    vm.prank(alice);
    liquidityPoolWETH.provide(1e18);

    // Bob borrows 1 WETH
    vm.startPrank(bob);
    marginTrading.provideERC20(marginAccountID[bob], address(USDC), 1_000_000e6);
    marginTrading.borrow(marginAccountID[bob], address(WETH), 0.8e18);
    vm.stopPrank();

    // Interest accrues over time
    skip(60 * 60 * 24 * 365);

    // Charlie provides 1 WETH
    vm.prank(charlie);
    liquidityPoolWETH.provide(1e18);
    uint256 charlieLpTokens = liquidityPoolWETH.balanceOf(address(charlie));

    // Bob repays principal and interest
    uint256 bobTotalDebt = liquidityPoolWETH.getDebtWithAccruedInterest(marginAccountID[bob]);
    vm.startPrank(bob);
    marginTrading.provideERC20(marginAccountID[bob], address(WETH), bobTotalDebt - 0.1e18);
    marginTrading.repay(marginAccountID[bob], address(WETH), bobTotalDebt);
    vm.stopPrank();

    // Charlie withdraws all from liquidity pool, receiving less WETH than his original deposit
    uint256 charlieWETHBalance = WETH.balanceOf(address(charlie));
    vm.prank(charlie);
    liquidityPoolWETH.withdraw(charlieLpTokens);
    uint256 charlieWETHReceived = WETH.balanceOf(address(charlie)) - charlieWETHBalance;
    assert(charlieWETHReceived < 0.9991e18);
}
```

## 권고 사항

`provide` 및 `withdraw` 함수에서 총 유동성 계산 시 보험 풀로 보내는 수수료를 제외하십시오.

# [M-08] 청산 후 내가격(ITM)이 된 옵션 NFT가 잠길 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

옵션 NFT의 청산은 `HegicModule.liquidate`에서 처리됩니다. 이 함수는 모든 토큰 ID를 반복하고 옵션이 내가격이고 활성이며 만료되지 않은 경우 청산을 처리합니다. 이 처리가 끝나면 NFT는 계정 소유자에게 다시 전송됩니다. 즉, 옵션이 내가격이 아니거나 활성이 아니거나 만료된 경우 NFT는 `MarginAccount` 컨트랙트에 남아 있습니다.

```solidity
File: HegicModule.sol

45:     function liquidate(uint[] memory value, address holder) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountOut) {
46:         for (uint i; i < value.length; i++) {
47:    @>       if (getPayOffAmount(value[i]) > 0 && isOptionActive(value[i]) && getExpirationTime(value[i]) > block.timestamp) {
48:                 uint profit = getOptionValue(value[i]);
49:                 hegicPositionManager.transferFrom(marginAccount, address(this), value[i]);
50:                 operationalTreasury.payOff(value[i], marginAccount);
51:                 amountOut += assetExchangerUSDCetoUSDC.swapInput(profit, 0);
52:    @ >          hegicPositionManager.transferFrom(address(this), holder, value[i]);
53:             }
54:         }
55:     }
```

그러나 `MarginAccount.liquidate` 함수에서 NFT는 청산되는 계정의 `erc721ByContract` 매핑에서 삭제됩니다.

```solidity
File: MarginAccount.sol

217:     function liquidate(uint marginAccountID, address baseToken, address marginAccountOwner) external onlyRole(MARGIN_TRADING_ROLE) {
(...)
227:         for(uint i; i < availableErc721.length; i++) {
228:             uint[] memory erc721TokensByContract = erc721ByContract[marginAccountID][availableErc721[i]];
229:             erc721Params[i] = IModularSwapRouter.ERC721PositionInfo(availableErc721[i], baseToken, marginAccountOwner, erc721TokensByContract);
230:  @>         delete erc721ByContract[marginAccountID][availableErc721[i]];
231:         }
```

옵션이 아직 만료되지 않았고 외가격(OTM)이지만 만료 시간 전에 청산 가치(payoff)가 양수가 될 수 있습니다. 이 경우 NFT가 어떤 계정에도 크레딧되지 않으므로 옵션의 이익은 손실됩니다.

## 개념 증명

```solidity
function test_nftLockedOnLiquidation() public {
    provideInitialLiquidity();
    hegicStrategy.setPayOffAmount(optionID[alice], 0);

    // Setup for liquidatable account
    vm.startPrank(alice);
    marginTrading.provideERC20(marginAccountID[alice], address(WETH), 1e18);
    marginTrading.provideERC721(marginAccountID[alice], address(hegicPositionsManager), optionID[alice]);
    marginTrading.borrow(marginAccountID[alice], address(USDC), 3_500e6);
    marginTrading.withdrawERC20(marginAccountID[alice], address(USDC), 3_500e6);
    vm.stopPrank();
    quoter.setSwapPrice(address(WETH), address(USDC), 3_000e6);

    // Liquidate
    marginTrading.liquidate(marginAccountID[alice]);

    // Option pay off goes positive and is still locked and not expired
    hegicStrategy.setPayOffAmount(optionID[alice], 1_000e6);
    (IOperationalTreasury.LockedLiquidityState state, , , , uint32 expiration)
        = operationTreasury.lockedLiquidity(optionID[alice]);
    assert(state == IOperationalTreasury.LockedLiquidityState.Locked);
    assert(expiration > block.timestamp);

    // NFT is still owned by MarginAccount and is not credited to Alice, so cannot be withdrawn
    assert(hegicPositionsManager.ownerOf(optionID[alice]) == address(marginAccount));
    assert(marginAccount.getErc721ByContract(marginAccountID[alice], address(hegicPositionsManager)).length == 0);
}
```

## 권고 사항

원하는 동작에 따라 다른 접근 방식이 있습니다.

1. 어떤 상황에서도 NFT를 계정 소유자에게 다시 전송합니다.

```diff
    function liquidate(uint[] memory value, address holder) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountOut) {
        for (uint i; i < value.length; i++) {
            if (getPayOffAmount(value[i]) > 0 && isOptionActive(value[i]) && getExpirationTime(value[i]) > block.timestamp) {
                uint profit = getOptionValue(value[i]);
                hegicPositionManager.transferFrom(marginAccount, address(this), value[i]);
                operationalTreasury.payOff(value[i], marginAccount);
                amountOut += assetExchangerUSDCetoUSDC.swapInput(profit, 0);
-               hegicPositionManager.transferFrom(address(this), holder, value[i]);
            }
+           hegicPositionManager.transferFrom(address(this), holder, value[i]);
        }
    }
```

2. NFT를 보험 풀 주소에 크레딧하여 내가격이 되는 경우 옵션을 행사할 수 있도록 합니다.

3. 보험 풀에서 자금을 끌어모으지 않고 모든 부채가 청산된 경우에만 NFT를 계정 소유자에게 전송하고, 그렇지 않으면 NFT를 보험 풀 주소로 전송합니다.

# [M-09] 포트폴리오 비율이 매우 낮은 포지션은 청산할 수 없음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`MarginTrading.sol`에서 `_calculatePortfolioRatio` 함수는 마진 계정 가치와 누적 이자가 포함된 부채 간의 비율을 계산합니다. 마진 계정 가치가 발생한 부채보다 5자릿수 더 낮은 경우 이 함수는 되돌려집니다(revert).

```solidity
File: MarginTrading.sol

196:     function _calculatePortfolioRatio(uint marginAccountValue, uint debtWithAccruedInterest) private pure returns (uint marginAccountRatio) {
197:         if (debtWithAccruedInterest == 0) {
198:             return 0;
199:         }
200:   @>    require(marginAccountValue*COEFFICIENT_DECUMALS > debtWithAccruedInterest, "Margin Account value should be greater than debt with accrued interest");
201:         marginAccountRatio = marginAccountValue*COEFFICIENT_DECUMALS/debtWithAccruedInterest;
202:     }
```

이 함수는 `liquidate` 함수에서 실행되므로 포트폴리오 비율이 매우 낮은 포지션의 청산이 불가능하여 남은 담보가 컨트랙트에 잠기게 됩니다.

```solidity
File: MarginTrading.sol

181:     function liquidate(uint marginAccountID) external {
182:   @>    require(getMarginAccountRatio(marginAccountID) <= redCoeff, "Margin Account ratio is too high to execute liquidation");
```

## 권고 사항

```diff
File: MarginTrading.sol

-       if (portfolioRatio != 0) {
            require(portfolioRatio > yellowCoeff, "portfolioRatio is too low");
-       }

(...)

-       if (portfolioRatio != 0) {
            require(portfolioRatio > yellowCoeff, "portfolioRatio is too low");
-       }

(...)

    function _calculatePortfolioRatio(uint marginAccountValue, uint debtWithAccruedInterest) private pure returns (uint marginAccountRatio) {
        if (debtWithAccruedInterest == 0) {
-           return 0;
+           return type(uint256).max;
        }
-        require(marginAccountValue*COEFFICIENT_DECUMALS > debtWithAccruedInterest, "Margin Account value should be greater than debt with accrued interest");
        marginAccountRatio = marginAccountValue*COEFFICIENT_DECUMALS/debtWithAccruedInterest;
    }
```

# [M-10] 사용자가 잠재적으로 청산을 피할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

사용자는 `provideERC721` 함수를 사용하여 마진 계정에 포지션 NFT를 제공할 수 있으며, 사용자 주소에서 전송한 후 NFT는 적절한 모듈에 대해 승인(approve)됩니다:

```solidity
File: MarginAccount.sol
176:     function provideERC721(uint marginAccountID, address txSender, address token, uint collateralTokenID, address baseToken) external onlyRole(MARGIN_TRADING_ROLE) {
177:         require(isAvailableErc721[token], "Token you are attempting to deposit is not available for deposit");
178:         erc721ByContract[marginAccountID][token].push(collateralTokenID);
179:         IERC721(token).transferFrom(txSender, address(this), collateralTokenID);
180:         IERC721(token).approve(modularSwapRouter.getModuleAddress(token, baseToken), collateralTokenID);
181:     }
```

이 승인은 `HegicModule`의 49행과 60행에서 포지션의 성공적인 실행 및 청산에 필요합니다:

```solidity
File: HegicModule.sol
45:     function liquidate(uint[] memory value, address holder) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountOut) {
46:         for (uint i; i < value.length; i++) {
47:             if (getPayOffAmount(value[i]) > 0 && isOptionActive(value[i]) && getExpirationTime(value[i]) > block.timestamp) {
48:                 uint profit = getOptionValue(value[i]);
49:                 hegicPositionManager.transferFrom(marginAccount, address(this), value[i]);
50:                 operationalTreasury.payOff(value[i], marginAccount);
51:                 amountOut += assetExchangerUSDCetoUSDC.swapInput(profit, 0);
52:                 hegicPositionManager.transferFrom(address(this), holder, value[i]);
53:             }
64:         }
55:     }
56:
57:     function exercise(uint id) external onlyRole(MODULAR_SWAP_ROUTER_ROLE) returns(uint amountOut) {
58:         require(getPayOffAmount(id) > 0 && isOptionActive(id) && getExpirationTime(id) > block.timestamp, "The option is not active or there is no profit on it");
59:         uint profit = getOptionValue(id);
60:         hegicPositionManager.transferFrom(marginAccount, address(this), id);
61:         operationalTreasury.payOff(id, marginAccount);
62:         amountOut = assetExchangerUSDCetoUSDC.swapInput(profit, 0);
63:         hegicPositionManager.transferFrom(address(this), marginAccount, id);
64:     }
```

그러나 두 가지 경우에 이 함수들이 DoS(거부)될 수 있습니다:

1. `MarginAccount` 컨트랙트의 `isAvailableErc721`에 NFT 주소가 이미 추가되었지만 `ModularSwapRouter`에 해당 모듈 주소가 아직 설정되지 않은 경우. 이로 인해 0 주소에 대한 승인 호출이 발생합니다.
2. 사용자가 처음에 NFT를 제공한 시점부터 `ModularSwapRouter`에서 모듈 주소가 업데이트된 경우.

## 권고 사항

모듈 주소가 0 값이 아닌지 확인하는 것을 고려하십시오. 또한 초기 예치 후 모듈 주소가 업데이트되더라도 토큰이 승인되도록 `liquidate` 및 `exercise` 함수로 승인 호출을 이동하는 것을 고려하십시오.

# [M-11] 상환 중 부채 계산을 올림해야 함

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

부분 상환의 경우 `LiquidityPool#repay` 함수는 현재 부채와 상환 금액을 기준으로 남은 사용자의 부채를 계산합니다(171행):

```solidity
File: LiquidityPool.sol
155:     function repay(uint marginAccountID, uint amount) external onlyRole(MARGIN_ACCOUNT_ROLE) {
156:         uint newTotalBorrows = totalBorrows();
157:         uint newTotalInterestSnapshot = newTotalBorrows - netDebt;
158:         uint accruedInterest = (newTotalInterestSnapshot * shareOfDebt[marginAccountID]) / debtSharesSum; // Accrued interest only
159:         uint debt = portfolioIdToDebt[marginAccountID] + accruedInterest;
160:         if (debt < amount) {
161:             // If you try to return more tokens than were borrowed, the required amount will be taken to repay the debt, the rest will remain untouched
162:             amount = debt;
163:         }
164:         uint shareChange = (amount * debtSharesSum) / newTotalBorrows; // Trader's share to be given away
165:         uint profit = (accruedInterest * shareChange) / shareOfDebt[marginAccountID];
166:         uint profitInsurancePool = (profit * insuranceRateMultiplier) / INTEREST_RATE_COEFFICIENT;
167:         totalInterestSnapshot -= totalInterestSnapshot * shareChange / debtSharesSum;
168:         debtSharesSum -= shareChange;
169:         shareOfDebt[marginAccountID] -= shareChange;
170:         if (debt > amount) {
171:             uint tempDebt = (portfolioIdToDebt[marginAccountID] * (debt - amount)) / debt;
172:             netDebt = netDebt - (portfolioIdToDebt[marginAccountID] - tempDebt);
173:             portfolioIdToDebt[marginAccountID] = tempDebt;
174:         } else {
175:             netDebt -= portfolioIdToDebt[marginAccountID];
176:             portfolioIdToDebt[marginAccountID] = 0;
177:         }
178:         poolToken.transferFrom(msg.sender, address(this), amount);
179:         if (profitInsurancePool > 0) {
180:             poolToken.transfer(insurancePool, profitInsurancePool);
181:         }
182:
183:         emit Repay(marginAccountID, amount, profit);
184:     }
```

그러나 이 계산은 내림되므로 사용자는 매번 먼지(dust) 금액만큼 더 적은 부채를 상환할 수 있습니다. 이 먼지는 풀 예금자의 손실로 축적됩니다.

## 권고 사항

`LiquidityPool#repay` 함수에서 `tempDebt` 변수를 올림하는 것을 고려하십시오.

# [M-12] 청산 중 잠재적 자산 손실

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`MarginAccount` 청산 프로세스에서 `_clearDebtsWithPools` 함수를 사용하여 `LiquidityPools`에 부채를 상환할 때, `UniswapModule::swapInput#L291`이 호출되어 사용자의 USDC를 필요한 유동성 풀 토큰으로 변환합니다:

```solidity
File: MarginAccount.sol
283:     function _clearDebtsWithPools(uint marginAccountID, address baseToken) private {
284:         for (uint i; i < availableTokenToLiquidityPool.length; i++) {
285:             address liquidityPoolAddress = tokenToLiquidityPool[availableTokenToLiquidityPool[i]];
286:             uint poolDebt = ILiquidityPool(liquidityPoolAddress).getDebtWithAccruedInterest(marginAccountID);
287:             if (poolDebt != 0) {
288:                 uint amountInUSDC = modularSwapRouter.calculateAmountInERC20(baseToken, availableTokenToLiquidityPool[i], poolDebt);
289:                 uint userUSDCbalance = getErc20ByContract(marginAccountID, baseToken);
290:                 if (amountInUSDC > userUSDCbalance) {
291:                     uint amountOut = modularSwapRouter.swapInput(baseToken, availableTokenToLiquidityPool[i], userUSDCbalance, 0);
292:                     erc20ByContract[marginAccountID][baseToken] -= userUSDCbalance;
293:                     IERC20(availableTokenToLiquidityPool[i]).transferFrom(insurancePool, address(this), poolDebt-amountOut);
294:                 } else {
295:                     uint amountIn = modularSwapRouter.swapOutput(baseToken, availableTokenToLiquidityPool[i], poolDebt);
296:                     erc20ByContract[marginAccountID][baseToken] -= amountIn;
297:                 }
298:                 ILiquidityPool(liquidityPoolAddress).repay(marginAccountID, poolDebt);
299:             }
300:         }
301:     }
```

문제는 `amountOutMinimum` 매개변수가 0으로 설정되어 있다는 것입니다. 이는 스왑 중에 최소 출력이 시행되지 않음을 의미하며, 이는 잠재적인 시장 조작 위험에 트랜잭션을 노출시킵니다. 이러한 보호되지 않은 스왑은 마진 계정이 예상보다 훨씬 적게 받거나 심지어 아무것도 받지 못하는 결과를 초래할 수 있습니다.

## 권고 사항

Uniswap 견적(quote)을 사용하여 현재 시장 상황에 따라 `amountOutMinimum`을 계산하는 것이 좋습니다.

# [M-13] 수수료 또는 이자 부과 부족으로 인한 무료 플래시 론

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`LiquidityPool.sol`은 LP 제공자가 토큰을 예치하고 `LiquidityPool::borrow()` 함수를 통해 `marginAccounts`가 이를 빌릴 수 있도록 설계되었습니다. 그러나 현재 구현에서는 수수료나 이자를 발생시키지 않고 동일한 트랜잭션 내에서 대출 및 상환이 가능하여 사실상 무료 플래시 론 메커니즘을 제공합니다.

문제는 토큰이 대출된 시간을 기준으로 누적 이자를 계산하는 `LiquidityPool::getDebtWithAccruedInterest` 함수에서 발생합니다:

```solidity
File: LiquidityPool.sol
207:     function getDebtWithAccruedInterest(uint marginAccountID) external view returns (uint debtByPool) {
208:         if (debtSharesSum == 0) return 0;
209:         return (totalBorrows() * shareOfDebt[marginAccountID]) / debtSharesSum;
210:     }
```

`LiquidityPool::totalBorrows()` 함수는 마지막 스냅샷 이후 경과된 시간을 기준으로 이자를 계산합니다. 플래시 론 시나리오에서 자산이 동일한 트랜잭션에서 상환되면 `ownershipTime`은 0이 되어 이자나 수수료가 부과되지 않습니다:

```solidity
File: LiquidityPool.sol
214:     function totalBorrows() public view returns (uint) {
215:         uint ownershipTime = block.timestamp - totalBorrowsSnapshotTimestamp;
216:         uint precision = 10 ** 18;
217:         UD60x18 temp = div(
218:             convert(
219:                 ((INTEREST_RATE_COEFFICIENT + interestRate) *
220:                     precision) / INTEREST_RATE_COEFFICIENT
221:             ),
222:             convert(precision)
223:         );
224:         return
225:             ((netDebt + totalInterestSnapshot) *
226:                 intoUint256(pow(temp, div(convert(ownershipTime), convert(ONE_YEAR_SECONDS))))) / 1e18;
227:     }
```

다음 테스트는 공격자가 수수료나 이자를 발생시키지 않고 동일한 트랜잭션 내에서 `2,000 ether`를 빌리고 상환하여 이 취약점을 악용하는 방법을 보여줍니다:

```solidity
// File: LiquidityPool.t.sol
// $ forge test --match-test="test_borrow_repay_sametx" --fork-url https://arb-sepolia.g.alchemy.com/v2/... -vvv
//
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {Test, console} from "forge-std/Test.sol";
import {LiquidityPool} from "../contracts/LiquidityPool.sol";
import {MockERC20} from "../contracts/mock/MockERC20.sol";


contract LiquidityPoolTest is Test {

    function test_borrow_repay_sametx() public {
        address insurancePool = address(0x123);
        address marginAccountStorage = address(this);
        MockERC20 baseToken = new MockERC20("USDC Token", "USDC", 6);
        MockERC20 poolToken = new MockERC20("WETH Token", "WETH", 18);
        uint poolCapacity = 10_000 * 10 ** poolToken.decimals();

        LiquidityPool liquidityPool = new LiquidityPool(
            insurancePool,
            marginAccountStorage,
            baseToken,
            poolToken,
            "Liquidity Pool Token",
            "LPT",
            poolCapacity
        );
        //
        // 1. LP provide some tokens to the pool
        deal(address(poolToken), address(this), 20_000 ether);
        poolToken.approve(address(liquidityPool), 20_000 ether);
        liquidityPool.provide(9_000 ether);
        //
        // 2. Attacker obtains a borrow and repay in the same tx without pay any interest (flash loan for free)
        uint marginAccountID = 2;
        console.log("\nBorrow of 2_000 ether to marginAccountID=2");
        console.log("Total borrows before attack:  ", liquidityPool.totalBorrows());
        console.log("Total marginAccountID debt:   ", liquidityPool.getDebtWithAccruedInterest(marginAccountID));
        liquidityPool.borrow(marginAccountID, 2_000 ether);
        console.log("Total current borrows:        ", liquidityPool.totalBorrows());
        console.log("Total marginAccountID debt:   ", liquidityPool.getDebtWithAccruedInterest(marginAccountID));
        liquidityPool.repay(marginAccountID, 2_000 ether);
        assertEq(liquidityPool.portfolioIdToDebt(marginAccountID), 0);
        assertEq(liquidityPool.totalInterestSnapshot(), 0);
        console.log("Total borrows after repayment:", liquidityPool.totalBorrows());
        console.log("Total marginAccountID debt:   ", liquidityPool.getDebtWithAccruedInterest(marginAccountID));
        console.log("Total interest paid:          ", liquidityPool.totalInterestSnapshot());
    }
}
```

출력:

```bash
Borrow of 2_000 ether to marginAccountID=2
  Total borrows before attack:   0
  Total marginAccountID debt:    0
  Total current borrows:         2000000000000000000000
  Total marginAccountID debt:    2000000000000000000000
  Total borrows after repayment: 0
  Total marginAccountID debt:    0
  Total interest paid:           0
```

## 권고 사항

이 위험을 완화하려면 동일한 트랜잭션 내에서 상환되더라도 대출 활동에 대해 최소한의 수수료 또는 이자를 부과하는 것이 좋습니다. 이 수수료는 플래시 론 메커니즘의 남용을 억제하고 프로토콜이 이러한 작업에서 수익을 창출하도록 하는 데 도움이 될 수 있습니다.

# [M-14] Uniswap V3 quoter 컨트랙트는 온체인에서 호출되어서는 안 됨

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`UniswapModule`에는 Uniswap V3 quoter 컨트랙트에 대한 호출이 포함되어 있습니다.

```solidity
File: UniswapModule.sol

61:     function getInputPositionValue(uint256 amountIn) external returns (uint amountOut) {
62:         ISwapRouter.ExactInputParams memory params = _preparationInputParams(amountIn);
63:
64:         amountOut = quoter.quoteExactInput(params.path, amountIn);
65:     }
66:
67:     function getOutputPositionValue(uint256 amountOut) public returns (uint amountIn) {
68:         ISwapRouter.ExactOutputParams memory params = _preparationOutputParams(amountOut);
69:
70:         amountIn = quoter.quoteExactOutput(params.path, amountOut);
71:     }
```

이러한 함수는 코드베이스의 여러 부분에서 호출되어 스왑의 예상 토큰 입력 또는 출력 양을 얻습니다. 그러나 [컨트랙트 natspec](https://github.com/Uniswap/v3-periphery/blob/464a8a49611272f7349c970e0fadb7ec1d3c1086/contracts/lens/Quoter.sol#L18-L19)에 언급된 바와 같이, 이러한 함수는 가스 집약적일 수 있으며 온체인에서 사용하도록 설계되지 않았습니다.

```solidity
/// @dev These functions are not gas efficient and should _not_ be called on chain. Instead, optimistically execute
/// the swap and check the amounts in the callback.
```

함수는 동일한 트랜잭션에서 여러 번 실행될 수 있으며, 이는 높은 가스 비용과 블록 가스 한도에 도달할 경우 잠재적인 DoS로 이어질 수 있습니다.

## 권고 사항

이 문제를 완화하려면 다음 방식으로 프로토콜을 재설계해야 합니다:

- 토큰 평가에 오라클을 사용하십시오.
- 예상 금액을 인용(quoting)하는 대신 실제 스왑의 출력을 사용하십시오.

# [M-15] 보험 풀에 자금이 없는 경우 청산 시 부분 상환이 불가능함

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

청산 프로세스가 끝나면 청산되는 계정의 모든 부채가 청산됩니다. 이는 계정의 자금으로 각 유동성 풀의 부채를 상환하여 수행됩니다. 계정이 담보 부족 상태인 경우 부채를 상환하는 데 필요한 나머지 자금은 보험 풀에서 가져옵니다.

보험 풀에 전체 부채를 충당할 만큼 충분한 자금이 없으면 부채 중 어느 것도 상환되지 않으며, 계정 담보는 계정 소유자가 `repay` 함수를 호출하는 경우에만 부분적으로 부채를 상환하는 데 사용할 수 있습니다. 그러나 담보가 컨트랙트에 잠겨 있기 때문에 계정 소유자가 그렇게 할 인센티브가 없습니다.

## 개념 증명

- 계정은 100 WETH(300,000 USDC 상당)와 10 WBTC(800,000 USDC 상당)를 빚지고 있습니다.
- 계정에는 500,000 USDC가 있습니다.
- 계정이 청산되려고 할 때 각 유동성 풀에서 부채가 청산됩니다.
- 첫 번째 반복에서 WETH 부채를 상환하기 위해 계정에서 300,000 USDC를 가져옵니다.
- 두 번째 반복에서 WBTC 부채를 상환하기 위해 계정에서 200,000 USDC를 가져옵니다. 이는 2.5 WBTC를 커버하므로 나머지 7.5 WBTC는 보험 풀에서 이체되어야 합니다.
- 보험 풀에는 5 WBTC만 있으므로 트랜잭션이 되돌려지고(revert) 부채 중 어느 것도 상환되지 않습니다.

## 권고 사항

보험 풀에 전체 부채를 충당할 만큼 충분한 자금이 없는 경우 부채의 부분 상환을 허용하는 한 가지 옵션이 있습니다. 이 경우 악성 부채(bad debt)는 프로토콜에 의해 흡수됩니다.

```diff
    if (amountInUSDC > userUSDCbalance) {
        uint amountOut = modularSwapRouter.swapInput(baseToken, availableTokenToLiquidityPool[i], userUSDCbalance, 0);
        erc20ByContract[marginAccountID][baseToken] -= userUSDCbalance;
+       uint256 insurancePoolBalance = IERC20(availableTokenToLiquidityPool[i]).balanceOf(insurancePool);
+       if (insurancePoolBalance < poolDebt - amountOut) {
+           poolDebt = insurancePoolBalance + amountOut;
+       }
        IERC20(availableTokenToLiquidityPool[i]).transferFrom(insurancePool, address(this), poolDebt-amountOut);
    } else {
        uint amountIn = modularSwapRouter.swapOutput(baseToken, availableTokenToLiquidityPool[i], poolDebt);
        erc20ByContract[marginAccountID][baseToken] -= amountIn;
    }
    ILiquidityPool(liquidityPoolAddress).repay(marginAccountID, poolDebt);
```

또 다른 옵션은 전체 부채를 상환할 수 없는 유동성 풀에 대해서만 부채 청산을 건너뛰는 것입니다.

```diff
    if (amountInUSDC > userUSDCbalance) {
+       uint amountOut = calculateAmountOutERC20(baseToken, availableTokenToLiquidityPool[i], userUSDCbalance);
+       uint256 insurancePoolBalance = IERC20(availableTokenToLiquidityPool[i]).balanceOf(insurancePool);
+       if (insurancePoolBalance < poolDebt - amountOut) {
+           continue;
+       }
        modularSwapRouter.swapInput(baseToken, availableTokenToLiquidityPool[i], userUSDCbalance, 0);
```

# [M-16] `LiquidityPool` 컨트랙트에 대한 기부는 지분 계산을 조작할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`LiquidityPool` 컨트랙트의 `provide` 함수는 제공된 유동성 양에 따라 호출자에게 새 지분을 발행합니다. 발행되는 지분의 양은 다음과 같이 계산됩니다:

- 총 유동성(컨트랙트 잔액 + 대출 금액)이 0보다 큰 경우, 지분 변경은 `(depositShare * amount) / totalLiquidity`로 계산됩니다.
- 총 유동성이 0인 경우, 지분 변경은 `(amount * 10 ** decimals()) / 10 ** poolToken.decimals()`로 계산됩니다.

```solidity
File: LiquidityPool.sol

097:     function provide(uint amount) external nonReentrant {
098:         uint totalLiquidity = getTotalLiquidity();
099:         require(
100:             totalLiquidity + amount <= maximumPoolCapacity,
101:             "Maximum liquidity has been achieved!"
102:         );
103:         poolToken.transferFrom(msg.sender, address(this), amount);
104:         uint shareChange = totalLiquidity > 0
105:   @>        ? (depositShare * amount) / totalLiquidity
106:   @>        : (amount * 10 ** decimals()) / 10 ** poolToken.decimals();
107:         _mint(msg.sender, shareChange);
108:         depositShare += shareChange;
109:
110:         emit Provide(msg.sender, shareChange, amount);
111:     }
```

`totalLiquidity`가 컨트랙트 잔액을 사용하여 계산된다는 점을 감안할 때, 악의적인 사용자는 토큰을 기부하여 지분 계산을 조작할 수 있습니다.

첫 번째 공격은 사용자가 유동성을 제공하기 전에 적은 양의 토큰을 기부하는 것으로 구성될 수 있습니다. 첫 번째 사용자가 유동성을 제공할 때 `totalLiquidity`는 0보다 크고 지분 변경은 `(depositShare * amount) / totalLiquidity`로 계산됩니다. `depositShare`가 0이므로 지분 변경도 0이 되어 사용자는 지분을 받지 못합니다. 첫 번째 사용자 이후에 유동성을 제공하는 모든 사용자에게도 동일한 일이 발생합니다. 따라서 모든 사용자는 제공한 토큰을 잃게 됩니다.

"첫 번째 예금자 인플레이션 공격(first depositor inflation attack)"으로 알려진 두 번째 공격은 첫 번째 예금자가 지분 계산을 자신에게 유리하게 부풀림으로써 수행될 수 있습니다. 이는 1 wei를 제공(1 지분 발행)하고 대량의 토큰을 기부함으로써 수행됩니다. 다음 예금자가 유동성을 제공할 때 지분 계산에서 내림이 발생하여 예상보다 적은 지분을 받게 됩니다.

자세한 내용은 개념 증명을 참조하십시오.

## 개념 증명

```solidity
function test_provideMintsZeroShares() public {
    vm.prank(alice);
    WETH.transfer(address(liquidityPoolWETH), 1);

    vm.prank(bob);
    liquidityPoolWETH.provide(1e18);

    assert(liquidityPoolWETH.balanceOf(address(bob)) == 0);
}
```

```solidity
// Victim deposit > attacker donation
function test_firstDepositorInflation_1() public {
    // Alice provides 1 wei and donates 0.5 WETH
    vm.startPrank(alice);
    liquidityPoolWETH.provide(1);
    WETH.transfer(address(liquidityPoolWETH), 0.5e18);
    vm.stopPrank();

    // Bob provides 1 WETH
    // Shares minted = (depositShare * amount) / totalLiquidity = (1 * 1e18) / (0.5e18 + 1) = 1
    vm.prank(bob);
    liquidityPoolWETH.provide(1e18);

    // Both Alice and Bob have 1 share
    assert(liquidityPoolWETH.balanceOf(address(alice)) == 1);
    assert(liquidityPoolWETH.balanceOf(address(bob)) == 1);

    // Alice withdraws 1 share and gets 0.75 WETH
    uint256 aliceWETHBalance = WETH.balanceOf(address(alice));
    vm.prank(alice);
    liquidityPoolWETH.withdraw(1);
    uint256 aliceWETHBalanceIncrease = WETH.balanceOf(address(alice)) - aliceWETHBalance;
    assert(aliceWETHBalanceIncrease == 0.75e18);
}

// Victim deposit < attacker donation
function test_firstDepositorInflation_2() public {
    // Alice provides 1 wei and donates 0.5 WETH
    vm.startPrank(alice);
    liquidityPoolWETH.provide(1);
    WETH.transfer(address(liquidityPoolWETH), 0.5e18);
    vm.stopPrank();

    // Bob provides 0.2 WETH
    // Shares minted = (depositShare * amount) / totalLiquidity = (1 * 0.2e18) / (0.5e18 + 1) = 0
    vm.prank(bob);
    liquidityPoolWETH.provide(0.2e18);

    // Both Alice and Bob have 1 share
    assert(liquidityPoolWETH.balanceOf(address(alice)) == 1);
    assert(liquidityPoolWETH.balanceOf(address(bob)) == 0);

    // Alice withdraws 1 share and gets 0.7 WETH + 1 wei
    uint256 aliceWETHBalance = WETH.balanceOf(address(alice));
    vm.prank(alice);
    liquidityPoolWETH.withdraw(1);
    uint256 aliceWETHBalanceIncrease = WETH.balanceOf(address(alice)) - aliceWETHBalance;
    console.log(aliceWETHBalanceIncrease);
    assert(aliceWETHBalanceIncrease == 0.7e18 + 1);
}
```

## 권고 사항

이를 완화하는 방법은 다음과 같이 다양합니다:

- 죽은 지분(dead shares) 사용: 컨트랙트 배포 시 유동성을 제공하도록 강제합니다. 이것이 구현하기 가장 쉽습니다.
- 가상 예치금(virtual deposit) 사용: OpenZeppelin의 ERC4626은 이 솔루션을 구현하며 [문서](https://docs.openzeppelin.com/contracts/4.x/erc4626)에 잘 설명되어 있습니다.
- 내부 잔액 추적 사용: `IRC20.balanceOf` 함수에 의존하는 대신 변수에서 토큰 잔액을 추적하는 것으로 구성됩니다.

# [L-01] 안전한 전송 메커니즘 부족

`MarginAccount::withdrawERC721` 함수는 이전에 `MarginAccount::provideERC721`을 사용하여 프로토콜에 예치된 ERC721 토큰의 전송을 용이하게 합니다. 이 함수는 승인된 운영자 또는 `MarginAccountManager`가 관리하는 ERC721 토큰인 `MarginAccount`의 소유자가 인출을 실행할 수 있도록 합니다. 그러나 구현에서 중대한 간과가 관찰됩니다:

```solidity
File: MarginAccount.sol
188:     function withdrawERC721(uint marginAccountID, address token, uint value, address txSender) external onlyRole(MARGIN_TRADING_ROLE) {
189:         _deleteERC721TokenFromContractList(marginAccountID, token, value);
190:         IERC721(token).transferFrom(address(this), txSender, value);
191:     }
```

현재 함수는 `IERC721(token).safeTransferFrom(...)` 대신 `IERC721(token).transferFrom(...)`을 사용합니다. 이 누락에는 수신자가 ERC721 토큰을 받을 수 있는지 확인하는 중요한 검사가 결여되어 있으며, 이는 ERC721 토큰 수신자 인터페이스를 구현하지 않는 컨트랙트로 전송될 때 토큰이 잠기거나 손실되는 것을 방지하는 데 필수적입니다.

다음 시나리오를 고려해 보십시오:

1. `MarginAccountManager`를 통해 `MarginAccount`가 생성됩니다.
2. 계정 소유자가 `ERC721::setApprovalForAll`로 운영자를 설정합니다.
3. 운영자는 특정 상황에서 ERC721 토큰을 올바르게 처리할 수 없는 승인된 주소(X)를 추가합니다.
4. 이 승인된 주소(X)는 `MarginTrading::withdrawERC721`을 호출하여 `MarginAccount::withdrawERC721`로 이어집니다. ERC721은 `safeTransferFrom`의 안전 검사가 없는 `transferFrom`을 사용하여 전송되므로, 토큰은 ERC721 토큰과 올바르게 상호 작용할 수 없는 주소로 전송되어 수신자 컨트랙트 내에서 효과적으로 손실되거나 잠길 수 있습니다.

`MarginAccount::withdrawERC721` 함수에서 `transferFrom`을 `safeTransferFrom`으로 대체하는 것이 좋습니다. 이러한 변경은 특히 여러 운영자 또는 승인된 주소를 포함하는 시나리오에서 더 안전한 메커니즘을 보장합니다.

# [L-02] `isAvailableErc20` 확인의 불일치

`MarginAccount.isAvailableErc20` 변수는 지원되는 ERC20 토큰만 프로토콜 내에서 토큰 제공, 스왑 또는 옵션 행사와 같은 작업에 사용되도록 하는 데 중요한 역할을 합니다. 이 검사는 토큰이 프로토콜 내에서 활성 상태가 아니거나 사용할 수 없는 경우 이러한 기능에 대한 액세스를 제한하기 위해 시행됩니다. 예: `MarginAccount#L171`:

```solidity
File: MarginAccount.sol
170:     function provideERC20(uint marginAccountID, address txSender, address token, uint amount) external onlyRole(MARGIN_TRADING_ROLE) {
171:         require(isAvailableErc20[token], "Token you are attempting to deposit is not available for deposit");
172:         erc20ByContract[marginAccountID][token] += amount;
173:         IERC20(token).transferFrom(txSender, address(this), amount);
174:     }
```

그러나 `MarginAccount::borrow` 함수는 현재 이 유효성 검사를 통합하지 않아 잠재적으로 사용자가 비활성화되었거나 유동성 우려 또는 외부 시장 요인과 같은 다양한 문제로 인해 액세스할 수 없어야 하는 토큰에 대해 대출할 수 있도록 합니다.

```solidity
File: MarginAccount.sol
193:     function borrow(uint marginAccountID, address token, uint amount) external onlyRole(MARGIN_TRADING_ROLE) {
194:         address liquifityPoolAddress = tokenToLiquidityPool[token];
195:         require(liquifityPoolAddress != address(0), "Token is not supported");
196:
197:         erc20ByContract[marginAccountID][token] += amount;
198:         ILiquidityPool(liquifityPoolAddress).borrow(marginAccountID, amount);
199:     }
```

예를 들어, 프로토콜이 처음에 `wETH` 유동성 풀에 대한 액세스를 허용하고 나중에 문제로 인해 비활성화하는 경우, 사용자는 `wETH`를 예치하거나 스왑할 수 없지만 이전 잔액이 있는 경우 여전히 잠재적으로 대출 작업을 시작할 수 있습니다. 이는 비활성화된 토큰과 관련된 원치 않는 위험에 프로토콜을 노출시킬 수 있습니다.

모든 관련 함수에서 토큰 가용성 확인의 일관성을 보장하기 위해 `isAvailableErc20` 확인을 `MarginAccount::borrow` 함수로 확장하는 것이 좋습니다:

```diff
# File: MarginAccount.sol
    function borrow(uint marginAccountID, address token, uint amount) external onlyRole(MARGIN_TRADING_ROLE) {
++      require(isAvailableErc20[token], "Token is not available");
        address liquifityPoolAddress = tokenToLiquidityPool[token];
        require(liquifityPoolAddress != address(0), "Token is not supported");

        erc20ByContract[marginAccountID][token] += amount;
        ILiquidityPool(liquifityPoolAddress).borrow(marginAccountID, amount);
    }
```

# [L-03] 보험 풀 관리의 중앙집중화 위험

`LiquidityPool::repay` 함수는 유동성 풀에 대한 상환을 처리하도록 설계되었습니다. 이익이 있으면 `insurancePool`로 전송됩니다:

```solidity
File: LiquidityPool.sol
155:     function repay(uint marginAccountID, uint amount) external onlyRole(MARGIN_ACCOUNT_ROLE) {
...
...
179:         poolToken.transferFrom(msg.sender, address(this), amount);
180:         if (profitInsurancePool > 0) {
181:             poolToken.transfer(insurancePool, profitInsurancePool);
182:         }
...
...
185:     }
```

그러나 `insurancePool`은 `LiquidityPool::setInsurancePool` 함수를 통해 관리자가 수정할 수 있으며, 이는 중앙집중화 위험을 초래합니다:

```solidity
File: LiquidityPool.sol
75:     function setInsurancePool(address newInsurancePool) external onlyRole(MANAGER_ROLE) {
76:         insurancePool = newInsurancePool;
77:
78:         emit UpdateInsurancePool(newInsurancePool);
79:     }
```

`insurancePool`을 `address(0)`으로 설정함으로써 `manager`는 `ERC20InvalidReceiver(address(0))` 오류로 인해 `LiquidityPool::repay` 함수를 되돌릴(revert) 수 있습니다. 이는 단일 행위자에 의해 전체 유동성 풀의 기능이 손상될 수 있는 취약점을 초래합니다.

이 위험을 완화하려면 컨트랙트 생성 시 `insurancePool`이 설정되는 `MarginAccount`에서 취한 접근 방식과 유사하게, 컨트랙트 초기화 단계에서만 `insurancePool` 주소를 설정하는 것이 좋습니다. 이 변경은 `manager`가 배포 후 임의로 `insurancePool`을 유효하지 않은 주소로 변경하는 것을 방지합니다.

# [L-04] 누락된 슬리피지 확인

슬리피지 / 출력 금액 확인 부족은 다음 인스턴스에서 발견되었습니다:

1. `UniswapModule` 컨트랙트의 `liquidate` 메서드에서 `params.amountOutMinimum`이 설정되지 않았으므로 기본적으로 0입니다.
   _비교를 위해 `swapInput` 메서드를 참조하십시오._
2. `HegicModule` 컨트랙트의 `liquidate` 및 `exercise` 메서드에서 `swapInput` 호출의 `amountOutMinimum` 매개변수는 명시적으로 0으로 설정됩니다.
3. `MarginAccount` 컨트랙트의 `_clearDebtsWithPools` 메서드에서 `swapInput` 호출의 `amountOutMinimum` 매개변수는 명시적으로 0으로 설정됩니다.

# [L-05] 누락된 ERC721 존재 확인

`MarginAccount` 컨트랙트의 `_deleteERC721TokenFromContractList` 메서드는 주어진 `tokenID`가 `userTokesByContract` 배열에 존재하지 않는 경우 되돌리지(revert) 않습니다.
주어진 `tokenID`를 찾아서 삭제할 수 없는 경우 오류 메시지와 함께 `revert`하는 것이 좋습니다.

```solidity
function _deleteERC721TokenFromContractList(uint marginAccountID, address token, uint tokenID) private {
    uint[] storage userTokesByContract = erc721ByContract[marginAccountID][token];

    for(uint i = 0; i < userTokesByContract.length; i++) {
        if(userTokesByContract[i] == tokenID) {
            userTokesByContract[i] = userTokesByContract[userTokesByContract.length - 1];
            userTokesByContract.pop();
            return;
        }
    }
}
```

# [L-06] 불리한 require 확인 위치

`MarginAccount` 컨트랙트의 `repay` 메서드에서 `amount`는 잠재적으로 몇 줄 아래에서 다시 수정되기 전에 확인됩니다. 초기에 `amount == 0`인 경우, 트랜잭션은 오류 메시지와 함께 실패하는 대신 산술 언더플로 오류로 인해 되돌려질(revert) 수 있습니다.
`require` 확인을 잠재적인 금액 변경 아래로 이동하는 것을 고려하십시오.

```solidity
function repay(uint marginAccountID, address token, uint amount) external onlyRole(MARGIN_TRADING_ROLE) {
    address liquifityPoolAddress = tokenToLiquidityPool[token];
    require(liquifityPoolAddress != address(0), "Token is not supported");

    require(amount <= erc20ByContract[marginAccountID][token], "Insufficient funds to repay the debt");  // @audit move this check


    uint debtWithAccruedInterest = ILiquidityPool(liquifityPoolAddress).getDebtWithAccruedInterest(marginAccountID);
    if (amount == 0 || amount > debtWithAccruedInterest) {
        amount = debtWithAccruedInterest;  // @audit amount is modified here again
    }

    // @audit move here

    erc20ByContract[marginAccountID][token] -= amount; // @audit potential underflow error
    ILiquidityPool(liquifityPoolAddress).repay(marginAccountID, amount);
}
```

# [L-07] 런타임 중 핵심 계수 변경

`MANAGER_ROLE`을 가진 모든 사람은 런타임 중에 프로토콜의 `yellowCoeff`(정상 포트폴리오 비율) 및 `redCoeff`(청산 비율)를 변경할 수 있습니다. 그러나 프로토콜이 라이브 상태가 되면 이러한 계수 중 하나를 변경하면 사용자에게 해로운 영향을 미칠 수 있습니다. 증가된 `yellowCoeff`에서 인출 및 대출이 예기치 않게 실패할 수 있으며, 증가된 `redCoeff`는 마진 계정을 예기치 않게 청산할 수 있습니다.

이 두 계수를 `immutable`로 만드는 것이 좋습니다.

# [L-08] 옵션 NFT를 인출할 수 없음

`MarginTrading.sol`의 `withdrawERC721` 함수는 NFT가 인출되기 위해 행사될 수 있어야 함을 요구합니다. 내가격(ITM)인 옵션은 인출할 수 있지만 외가격(OTM)인 옵션은 인출할 수 없도록 허용하는 데에는 타당한 정당성이 있어 보입니다. 사용자는 아직 내가격이 아닌 옵션을 인출하여 2차 시장에서 판매하거나 다른 프로토콜에서 사용하거나 다른 목적으로 사용하기를 원할 수 있습니다.

```solidity
File: MarginTrading.sol

125:     function withdrawERC721(uint marginAccountID, address token, uint value) external nonReentrant onlyApprovedOrOwner(marginAccountID) {
126:         require(marginAccount.checkERC721Value(marginAccountID, token, value), "The ERC721 token you are attempting to withdraw is not available for withdrawal");
127:   @>    require(modularSwapRouter.checkValidityERC721(token, BASE_TOKEN, value), "token id is not valid");
128:
```

```solidity
File: HegicModule.sol

68:     function checkValidityERC721(uint id) external returns(bool) {
69:         if (getPayOffAmount(id) > 0 && isOptionActive(id) && getExpirationTime(id) > block.timestamp) {
70:             return true;
71:         }
72:     }
```

사용자가 행사할 수 없는 NFT를 인출할 수 있도록 `MarginTrading.sol`에서 두 번째 `require` 문을 제거하는 것을 고려하십시오.

# [L-09] 마진 계정과 연결된 ERC721 토큰 ID 확인으로 인한 DoS

`MarginAccount:checkERC721tokenID` 함수는 특정 ERC721 토큰 ID가 마진 계정과 연결되어 있는지 확인합니다. 이는 마진 계정과 연결된 모든 ERC721 토큰 ID를 반복하고 제공된 토큰 ID가 존재하는지 확인하여 수행됩니다.

```solidity
File: MarginAccount.sol

65:     function checkERC721tokenID(uint marginAccountID, address token, uint value) public view returns(bool hasERC721Id) {
66:         uint[] memory userERC721 = new uint[](erc721ByContract[marginAccountID][token].length);
67:         userERC721 = erc721ByContract[marginAccountID][token];
68:         for (uint i; i < userERC721.length; i++) {
69:             if (userERC721[i] == value) {
70:                 return true;
71:             }
72:         }
73:     }
```

이 함수는 `MarginTrading:withdrawERC721`이 호출될 때 실행됩니다. 마진 계정과 연결될 수 있는 ERC721 토큰 수에는 제한이 없으므로 모든 ERC721 토큰 ID를 반복하면 호출자가 높은 가스 비용을 부담하게 되어 잠재적으로 서비스 거부(DoS)로 이어질 수 있습니다.

특정 ERC721 토큰 ID가 마진 계정과 연결되어 있는지 여부를 저장하기 위해 매핑을 구현하는 것을 고려하십시오.

또한 `checkERC721Value`는 `checkERC721tokenID`를 호출하고 결과를 반환하기만 하므로 중복됩니다. 제거하는 것을 고려하십시오.


