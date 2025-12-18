# 소개

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**Pashov Audit Group**이 **Daydream-Labs/stardusts-v1-contracts** 저장소에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# Stardusts 소개

Stardusts는 본딩 커브(bonding curve)를 통해 거래를 가능하게 하고 16 ETH를 모금한 후 Uniswap V2 풀로 전환하는 게임 중심 ERC20 토큰입니다. 동반된 보상 프로세서는 플레이어 보상을 분배하고 토큰 환매(buyback)를 지원합니다.

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

_검토 커밋 해시_ - [13fe7d1e7739281c91daa4701fd94cb0432f827a](https://github.com/Daydream-Labs/stardusts-v1-contracts/tree/13fe7d1e7739281c91daa4701fd94cb0432f827a)

_수정 검토 커밋 해시_ - [446f14beb6faca4115badec1cf427436a7e854d3](https://github.com/Daydream-Labs/stardusts-v1-contracts/tree/446f14beb6faca4115badec1cf427436a7e854d3)

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `RewardProcessor`
- `StardustToken`

# 발견 사항

# [C-01] WETH를 직접 입금하여 `AddLiquidityETH` DoS 발생

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

프로토콜에 16ETH가 누적되면 프로토콜은 `_graduate()`를 호출하여 16ETH와 2억 개의 Stardust Token을 유동성으로 추가합니다. Stardust Token의 minOut 금액은 원하는 금액과 동일하며, 이는 프로토콜이 유동성을 제공하기 전에 다른 누군가가 유동성을 제공할 것이라고 예상하지 않음을 의미합니다.

```
 function _graduate() internal {
        tokenState = TokenState.Trading;

        uint256 ethLiquidity = address(this).balance;

        // Mint the secondary market supply to this contract
        _mint(address(this), X1);

        // Approve the Uniswap V2 router to spend this token for adding liquidity
        IERC20(address(this)).approve(swapRouter, X1);

        // Add liquidity to the Uniswap V2 pool
>       IUniswapV2Router02(swapRouter).addLiquidityETH{value: ethLiquidity}(
            address(this), X1, X1, ethLiquidity, owner(), block.timestamp + 5 minutes
        );

        emit Graduate(poolAddress, address(this), ethLiquidity, X1);
    }

```

`_update()` 함수는 "졸업(graduation)" 전에 누군가가 유동성을 추가하는 것을 방지하지만, 악의적인 사용자는 1 wei의 WETH 토큰을 쌍(pair) 주소로 직접 보내고 `sync()`를 호출하여 준비금(reserve) 잔고를 엉망으로 만들 수 있습니다.

StardustTokenTest.t.sol에 이 테스트를 추가하고 `forge test --mt test1 -vv --via-ir`을 실행하십시오. 함수는 `UniswapV2Library: INSUFFICIENT_LIQUIDITY`와 함께 실패해야 합니다.

```
  function test1() public {
        address user = address(0xaaa);
        vm.startPrank(user);
        vm.deal(user, 25 ether);
        address router = 0x4752ba5DBc23f44D87826276BF6Fd6b1C372aD24;
        IERC20 WETH1 = IERC20(IUniswapV2Router02(stardustToken.swapRouter()).WETH());
        deal(address(WETH1) ,user,10 ether);
        address factory = 0x8909Dc15e40173Ff4699343b6eB8132c65e18eC6;
        IUniswapV2Pair pair = IUniswapV2Pair(IUniswapV2Factory(factory).getPair(address(stardustToken), address(WETH1)));
        console.log("Pair Address:", address(pair));
        console.log(WETH1.balanceOf(address(pair)));

>        WETH1.transfer(address(pair), 1);
>        pair.sync();
        (uint112 reserve2, uint112 reserve3,) = pair.getReserves();
        console.log("Reserve0:", reserve2);
        console.log("Reserve1:", reserve3);
        console.log(WETH1.balanceOf(address(pair)));
        {
            // graduate buy
            uint256 actualEthCost = stardustToken.Y1() - stardustToken.Y0();
            uint256 fee = actualEthCost * stardustToken.feeBps() / 10000;
            uint256 totalRefund = 20 ether - (actualEthCost + fee);
            uint256 boughtStar = stardustToken.getOutTokenAmountAfterFee(0);
            // graduates when > 16 ether approx
            stardustToken.buy{value: 20 ether}(boughtStar);
        }
        (uint112 reserve0, uint112 reserve1,) = pair.getReserves();
        console.log("Reserve0:", reserve0);
        console.log("Reserve1:", reserve1);
    }
```

강조 표시된 두 줄이 제거되면 테스트가 통과하고 reserve0, reserve1은 16e18:1.8e26이어야 합니다.

```
  Reserve0: 16000000000000000000
  Reserve1: 180000000000000000000000000
```

## 권고 사항

유동성을 추가하기 전에 `_graduate()` 함수는 `skim()`을 호출하고 준비금이 조작되었는지 확인해야 합니다. 만약 그렇다면, 등가량의 StardustToken을 풀에 직접 민팅(mint)하고 `sync()`를 호출하십시오.

```
function _graduate() internal {
        tokenState = TokenState.Trading;

        uint256 ethLiquidity = address(this).balance;

        // Mint the secondary market supply to this contract
        _mint(address(this), X1);


        // Mint to Pair itself if someone already deposited directly into pair
>       // 1. Call skim first
        IUniswapV2Pair(poolAddress).skim(address(msg.sender));
        // 2. Check if reserve is not 0
        (uint112 reserve0, uint112 reserve1,) = IUniswapV2Pair(poolAddress).getReserves();
        uint balance;
        // 3. Mint the equivalent amount of StardustToken to the poolAddress directly
        if (reserve0 != 0) {
            balance = reserve0 * X1 / ethLiquidity;
            _mint(address(poolAddress), balance);
        } else if (reserve1 != 0) {
            balance = reserve1 * X1 / ethLiquidity;
            _mint(address(poolAddress), balance);
        }
        // 4. Call sync() to match the reserve address
        IUniswapV2Pair(poolAddress).sync();



        // Approve the Uniswap V2 router to spend this token for adding liquidity
        IERC20(address(this)).approve(swapRouter, X1);

        // Add liquidity to the Uniswap V2 pool
        IUniswapV2Router02(swapRouter).addLiquidityETH{value: ethLiquidity}(
        // Note that this strict slippage may result in failure, consider setting it lower.
>            address(this), X1, X1, ethLiquidity, owner(), block.timestamp + 5 minutes
        );

        emit Graduate(poolAddress, address(this), ethLiquidity, X1);
    }
```

위의 내용이 추가되면 악의적인 `sync()`가 있더라도 테스트를 다시 실행하면 통과해야 합니다. Reserve0에는 16e18 ETH와 1 wei가 있고, reserve1에는 1.8e26 토큰과 1 wei 상당의 Stardust 토큰이 있습니다.

```
If 1 wei is directly deposited:

  Reserve0: 16000000000000000001
  Reserve1: 180000000000000000012500000
```

StardustToken의 minOut이 너무 엄격하지 않은지 확인하십시오. 1 WETH가 계약에 입금되어도 함수는 여전히 성공하지만, 가장 좋은 방법은 0으로 만드는 것입니다.

```
If 1 WETH is directly deposited:

  Reserve0: 17000000000000000000
  Reserve1: 192500000000000000000000000
```

마지막으로 배포 전에 수정된 코드를 다시 철저히 테스트하십시오. 이는 전투 테스트(battle tested)가 되지 않았습니다.

# [H-01] 본딩 커브 구매 시 잘못된 수수료 계산 발생

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

사용자가 본딩 커브 구매를 수행하고 `trueOrderSize`가 `maxRemainingTokens`보다 클 때, 구매에 필요한 `ethNeeded`가 아니라 `totalCost`를 기준으로 `fee`를 계산합니다.

```solidity
    function _validateBondingCurveBuy(uint256 minOrderSize)
        internal
        returns (uint256 totalCost, uint256 trueOrderSize, uint256 fee, uint256 refund, bool shouldGraduate)
    {
        // Set the total cost to the amount of ETH sent
        totalCost = msg.value;

        // Calculate the fee
>>>     fee = (totalCost * feeBps) / 10000;

        // Calculate the amount of ETH remaining for the order
        uint256 remainingEth = totalCost - fee;

        // ...

        // If the order size is greater than the maximum number of remaining tokens:
        if (trueOrderSize > maxRemainingTokens) {
            // Reset the order size to the number of remaining tokens
            trueOrderSize = maxRemainingTokens;

            // Calculate the amount of ETH needed to buy the true order
            uint256 ethNeeded = Y1 - virtualEthLiquidity;

            // Recalculate the fee with the updated order size
>>>         fee = (ethNeeded * feeBps) / 10000;

            // Recalculate the total cost with the updated order size and fee
            totalCost = ethNeeded + fee;

            // Refund any excess ETH
            if (msg.value > totalCost) {
                refund = msg.value - totalCost;
            }

            shouldGraduate = true;
        }

    }
```

`trueOrderSize`가 `maxRemainingTokens`보다 클 때 `fee`가 구매에 필요한 ETH가 아니라 `totalCost`를 기준으로 함을 알 수 있습니다. 이로 인해 `trueOrderSize`가 `maxRemainingTokens`를 초과할 때 수수료가 과소평가됩니다. (문맥상 fee = (ethNeeded * feeBps) / 10000; 이 부분이 문제라고 지적하는 것으로 보임. 원래 totalCost 기반이었다가 ethNeeded 기반으로 바뀌는데, 수수료가 포함된 총 비용에서 수수료를 떼야 하는 구조라면 계산이 달라져야 함을 의미하는 듯)

## 권고 사항

다음과 같이 수수료 계산을 변경하십시오:

```diff
        if (trueOrderSize > maxRemainingTokens) {
            // Reset the order size to the number of remaining tokens
            trueOrderSize = maxRemainingTokens;

            // Calculate the amount of ETH needed to buy the true order
            uint256 ethNeeded = Y1 - virtualEthLiquidity;

            // Recalculate the fee with the updated order size
+            totalCost = (10000 * ethNeeded ) / (10000 - feeBps);
-            fee = (ethNeeded * feeBps) / 10000;
+            fee = totalCost - ethNeeded;

            // Recalculate the total cost with the updated order size and fee
-            totalCost = ethNeeded + fee;

            // Refund any excess ETH
            if (msg.value > totalCost) {
                refund = msg.value - totalCost;
            }

            shouldGraduate = true;
        }
```

# [M-01] `sell()` 함수의 `minPayoutSize` 확인 오류

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

```javascript
 function sell(uint256 tokensToSell, uint256 minPayoutSize) external nonReentrant returns (uint256) {
        // Ensure the sender has enough liquidity to sell
        if (tokensToSell > balanceOf(msg.sender)) revert InsufficientBalance();

        // Initialize the true payout size
        uint256 truePayoutSize;

        if (tokenState == TokenState.Trading) {
            truePayoutSize = _handleExchangeSell(tokensToSell, minPayoutSize);
        } else if (tokenState == TokenState.Raising) {
@>            truePayoutSize = _handleBondingCurveSell(tokensToSell, minPayoutSize);
            // Calculate the fee
            uint256 fee = (truePayoutSize * feeBps) / 10000;

            // Calculate the payout after the fee
@>             uint256 payoutAfterFee = truePayoutSize - fee;

            // Update the virtual ETH liquidity
            virtualEthLiquidity -= payoutAfterFee;

            // Handle the fees
            _distributeFees(fee);

            // Send the payout to the recipient
            Address.sendValue(payable(msg.sender), payoutAfterFee);
        }

        // truePayoutSize will be 0 if the sell is done through uniswap v2 pool
        // need to fetch that info from the pool events
        emit Trade(msg.sender, false, tokensToSell, truePayoutSize, currentEthPrice(), block.timestamp);
        return truePayoutSize;
    }
```

`minPayoutSize`는 사용자가 설정한 슬리피지 보호 매개변수로, 사용자가 결국 최소한 minPayoutSize ETH를 받도록 보장한다는 의미입니다. truePayoutSize를 확인하는 것은 이 보호를 달성하지 못하는데, payoutAfterFee가 여전히 minPayoutSize보다 작을 수 있기 때문입니다.

## 권고 사항

minPayoutSize 확인을 \_handleBondingCurveSell() 함수에서 제거하고 다음과 같이 sell() 함수로 이동하십시오:

```diff
// Calculate the payout after the fee
             uint256 payoutAfterFee = truePayoutSize - fee;
+             if (payoutAfterFee < minPayoutSize) revert SlippageTooHigh();
```

# [M-02] `minOrderSize` 확인에서의 오류

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

```javascript
function _validateBondingCurveBuy(uint256 minOrderSize)
        internal
        returns (uint256 totalCost, uint256 trueOrderSize, uint256 fee, uint256 refund, bool shouldGraduate)
    {
        // Set the total cost to the amount of ETH sent
        totalCost = msg.value;

        // Calculate the fee
        fee = (totalCost * feeBps) / 10000;

        // Calculate the amount of ETH remaining for the order
        uint256 remainingEth = totalCost - fee;

        // Get quote for the number of tokens that can be bought with the amount of ETH remaining
        trueOrderSize = getOutTokenAmount(remainingEth);

        // Ensure the order size is greater than the minimum order size
@>        if (trueOrderSize < minOrderSize) revert SlippageTooHigh();

        // Calculate the maximum number of tokens that can be bought on the bonding curve
        uint256 maxRemainingTokens = (X0 - X1) - totalSupply();

        // Start the market if the order size equals the number of remaining tokens
        if (trueOrderSize == maxRemainingTokens) {
            shouldGraduate = true;
        }

        // If the order size is greater than the maximum number of remaining tokens:
        if (trueOrderSize > maxRemainingTokens) {
            // Reset the order size to the number of remaining tokens
            trueOrderSize = maxRemainingTokens;

            // Calculate the amount of ETH needed to buy the true order
            uint256 ethNeeded = Y1 - virtualEthLiquidity;

            // Recalculate the fee with the updated order size
            fee = (ethNeeded * feeBps) / 10000;

            // Recalculate the total cost with the updated order size and fee
            totalCost = ethNeeded + fee;

            // Refund any excess ETH
            if (msg.value > totalCost) {
                refund = msg.value - totalCost;
            }

            shouldGraduate = true;
        }

        // ...

        // If the order size is greater than the maximum number of remaining tokens:
        if (trueOrderSize > maxRemainingTokens) {
            // Reset the order size to the number of remaining tokens
@>            trueOrderSize = maxRemainingTokens;

            // Calculate the amount of ETH needed to buy the true order
            uint256 ethNeeded = Y1 - virtualEthLiquidity;

            // Recalculate the fee with the updated order size
            fee = (ethNeeded * feeBps) / 10000;

            // Recalculate the total cost with the updated order size and fee
            totalCost = ethNeeded + fee;

            // Refund any excess ETH
            if (msg.value > totalCost) {
                refund = msg.value - totalCost;
            }

            shouldGraduate = true;
        }
    }
```

trueOrderSize > maxRemainingTokens 조건 하에서 trueOrderSize가 재할당되지만 minOrderSize에 대한 확인이 없음을 알 수 있습니다.
이로 인해 트랜잭션이 프런트러닝되어 사용자의 minOrderSize가 의도한 슬리피지 보호를 제공하지 못하는 상황이 발생할 수 있습니다.

## 권고 사항

if (trueOrderSize < minOrderSize) revert SlippageTooHigh(); 확인을 \_validateBondingCurveBuy() 함수의 끝으로 이동하여 minOrderSize 확인이 다른 모든 조건 후에 적용되도록 하십시오.

# [L-01] `block.timestamp + 5 minutes`는 트랜잭션 타이밍에 대해 어떠한 보호도 제공하지 않음

```javascript
IUniswapV2Router02(swapRouter).swapExactETHForTokensSupportingFeeOnTransferTokens{value: msg.value}(
                minAmountOut, path, msg.sender, block.timestamp + 5 minutes
            );
```

UniswapV2에서 거래할 때 block.timestamp + 5 minutes 매개변수를 사용하는 것은 block.timestamp와 완전히 동일합니다. block.timestamp는 트랜잭션이 포함된 블록의 타임스탬프를 반영하기 때문입니다. 예를 들어, 트랜잭션이 T0에 시작되더라도 여전히 T0 + 1시간의 블록에 포함될 수 있습니다.

의도가 트랜잭션이 시작 후 5분 이내에 블록에 포함되도록 하고 그렇지 않으면 폐기되는 것이라면, 올바른 접근 방식은 블록체인 변수 block.timestamp가 아닌 특정 현재 시스템 시간을 참조로 사용하는 것입니다.

# [L-02] StardustToken은 Uniswap V3에서 거래하여 전송 수수료를 피할 수 있음

```javascript
if (tokenState == TokenState.Trading && isTradingFromDex && !isPrizePool) {
                uint256 feeAmount = (value * feeBps) / 10000;
                uint256 protocolFee = feeAmount * protocolFeeBps / 10000;
                uint256 prizePoolFee = feeAmount - protocolFee;
                uint256 transferAmount = value - feeAmount;

                // Transfer fee to treasury and prize pool
                super._update(from, treasury, protocolFee);
                super._update(from, prizePool, prizePoolFee);

                // Transfer remainder to user
                super._update(from, to, transferAmount);
            }
```

\_update() 함수에서 전송 수수료는 Uniswap V2 풀에서 거래되는 토큰에만 적용됩니다. 결과적으로 사용자는 Uniswap V3에서 Uniswap V2와 가까운 가격으로 매수 또는 매도 주문을 할 수 있어 전송 수수료를 전혀 지불하지 않고 거래할 수 있습니다. 이것은 알려진 문제일 수 있습니다.

# [L-03] `currentEthPrice()` 계산에서의 오류

```javascript
function currentEthPrice() public view returns (uint256) {
        uint256 ethLiquidity = address(this).balance < Y0 ? Y0 : address(this).balance;
        if (tokenState == TokenState.Raising) {
            uint256 virtualTokenLiquidity = X0 - totalSupply();
            return ethLiquidity * 1e18 / virtualTokenLiquidity;
        } else if (tokenState == TokenState.Trading) {
            (uint112 reserve0, uint112 reserve1,) = IUniswapV2Pair(poolAddress).getReserves();
            address token0 = IUniswapV2Pair(poolAddress).token0();
            (uint112 reserveToken, uint112 reserveETH) =
                address(this) == token0 ? (reserve0, reserve1) : (reserve1, reserve0);

            return (uint256(reserveETH) * 1e18) / uint256(reserveToken);
        }

        return 0;
    }
```

본딩 커브 특성에 따르면 ETH 대비 토큰 가격은 address(this).balance가 아니라 virtualEthLiquidity에 의해 결정됩니다.

따라서:

```diff
-        uint256 ethLiquidity = address(this).balance < Y0 ? Y0 : address(this).balance;
+        uint256 ethLiquidity = virtualEthLiquidity
```

# [L-04] `rescue`는 수집된 ETH 보상 및 `boughtBackTokenBalances`를 인출할 수 있게 함

`rescue`는 호출자가 인출할 수 있는 ETH의 양을 제한하지 않으며 인출할 토큰이 `boughtBackTokenBalances`를 가지고 있는지 확인하지 않습니다. `rescue`가 `RewardProcessor` 내에서 문제를 일으키지 않도록 이러한 검증을 추가하는 것을 고려하십시오.

```diff
    function rescue(address token, uint256 amount, address to) external onlyOwner {
        // rescue eth
        if (token == address(0)) {
+          if (amount > address(this).balance - totalPrizeBalance) revert InvalidInput();
            Address.sendValue(payable(to), amount);
        } else {
+          if (amount > IERC20(token).balanceOf(address(this)) - boughtBackTokenBalances[token]) revert InvalidInput();
            IERC20(token).transfer(to, amount);
        }
    }
```

# [L-05] 풀이 이미 생성된 경우 `StardustToken` 생성이 항상 실패할 수 있음

`StardustToken`이 처음 생성될 때 WETH와 `StardustToken`으로 V2 풀 생성을 시도합니다.

```solidity
    function _createPool() internal returns (address) {
        // Get the factory and WETH address from the router
        address factory = IUniswapV2Router02(swapRouter).factory();
        address wethAddress = IUniswapV2Router02(swapRouter).WETH();
>>>     address pair = IUniswapV2Factory(factory).createPair(address(this), wethAddress);
        return pair;
    }
```

그러나 `StardustToken`의 주소를 예측할 수 있는 공격자가 풀 생성을 프런트러닝하여 풀이 이미 존재하기 때문에 `createPool`을 되돌릴(revert) 가능성이 있습니다. 풀이 아직 존재하지 않는 경우에만 `createPair`를 트리거하는 것을 고려하십시오.

```diff
    function _createPool() internal returns (address) {
        // Get the factory and WETH address from the router
        address factory = IUniswapV2Router02(swapRouter).factory();
        address wethAddress = IUniswapV2Router02(swapRouter).WETH();
+       address pair = IUniswapV2Factory(factory).getPair(address(this), wethAddress);
+       (if pair == address(0)) {
+         pair = IUniswapV2Factory(factory).createPair(address(this), wethAddress);
+       }
-        address pair = IUniswapV2Factory(factory).createPair(address(this), wethAddress);
        return pair;
    }
```

# [L-06] 토큰이 졸업하지 않은 경우에도 `settleRewards()`가 호출될 수 있음

`settleReward()` 함수에서 아이디어는 `settleAmount`(ETH)의 일부를 stardust 토큰으로 스왑하고 나머지를 사용자에게 분배하는 것입니다.
토큰이 졸업하지 않았더라도 함수는 여전히 호출될 수 있으며, 스왑을 건너뛰고 사용자에게 ETH를 직접 제공합니다.

```
 function settleRewards(
        address tokenToBuyBack,
        uint256 amountOutMin,
        uint256 settleAmount,
        address[] calldata users,
        uint256[] calldata rewardBps
    ) public onlyOperator {
        if (users.length != rewardBps.length || settleAmount > totalPrizeBalance) {
        ...
        uint256 ethForBuyBack = settleAmount * buyBackBpsIfGraduated / 10000;
        if (IStardustToken(tokenToBuyBack).tokenState() == IStardustToken.TokenState.Trading && ethForBuyBack > 0) {
            settleAmount -= ethForBuyBack;
            totalPrizeBalance -= ethForBuyBack;
            uint256 tokensReceived;

            ...
            emit BuyBack(tokenToBuyBack, ethForBuyBack, tokensReceived);
        }
>       // Update user rewards
        uint256 totalBps;
        for (uint256 i = 0; i < users.length; i++) {
```

의도한 것이 아니라면 `IStardustToken(tokenToBuyBack).tokenState() == IStardustToken.TokenState.Trading`인 경우에만 함수가 실행되도록 하십시오.

```
    if (users.length != rewardBps.length || settleAmount > totalPrizeBalance) {
            revert InvalidInput();
        }
>   if (IStardustToken(tokenToBuyBack).tokenState() != IStardustToken.TokenState.Trading){
            revert InvalidInput();
        }
```

# [L-07] `poolFeeBPS`의 상한선이 너무 높음

StardustToken 계약에서 소유자는 `feeBps`를 100%(10000)로 설정할 수 있습니다. 소유자는 사용자가 유동성 풀에서 토큰을 스왑할 때 모든 토큰을 얻기 위해 100%로 설정하거나, 수수료가 변경될 것이라는 사실을 모르는 사용자는 의도치 않게 의도한 것보다 더 많은 수수료를 지불할 수 있습니다.

중앙 집중화 위험을 줄이기 위해 최대 상한을 100% 미만(예: 20%)으로 설정하십시오.

```
    function setFeeBps(uint256 newFeeBps) public onlyOwner {
        if (newFeeBps >= 10000) {
            revert InvalidConfig();
        }
        feeBps = newFeeBps;
        emit SetFeeBps(newFeeBps);
    }
```

대신 최대 수수료를 10%(1000)로 설정하십시오.

```
    function setFeeBps(uint256 newFeeBps) public onlyOwner {
        if (newFeeBps >= 1000) {
            revert InvalidConfig();
        }
        feeBps = newFeeBps;
        emit SetFeeBps(newFeeBps);
    }
```

# [L-08] 본딩 커브를 통해 가장 최근에 구매한 토큰이 풀보다 더 비쌈

`totalSupply`(구매된 토큰)가 최대 구매 가능 토큰(`X0 - X1`)에 접근함에 따라 본딩 커브의 토큰 가격이 상승합니다. 이는 마지막으로 구매한 토큰이 풀에서 처음 구매한 토큰보다 더 비싸다는 것을 의미합니다.

모든 구매 가능 토큰이 구매되면 토큰과 ETH가 유동성으로 풀에 추가되므로, 다른 사용자가 본딩 커브에서 마지막 토큰을 구매할 때까지 기다렸다가 풀에서 더 낮은 가격으로 구매하려는 인센티브가 있습니다.

```solidity
    function testBuy_Price_Issue() public {
        address user = address(0xaaa);
        address buyer = address(0xbbb);
        address weth = IUniswapV2Router02(stardustToken.swapRouter()).WETH();
        uint256 buyAmount =  5_000_000 * 10 ** 18;

        vm.startPrank(user);
        vm.deal(user, 20 ether);
        uint256 maxTokenBuy = stardustToken.X0() - stardustToken.X1() - buyAmount;
        uint256 allBuyEthRequired = (stardustToken.K() / (stardustToken.X0() - maxTokenBuy)) - stardustToken.virtualEthLiquidity();
        uint256 buyEthWithFee = (10000 * allBuyEthRequired ) / (10000 - stardustToken.feeBps());
        stardustToken.buy{value: buyEthWithFee}(0);
        vm.stopPrank();

        vm.startPrank(buyer);
        vm.deal(buyer, 5 ether);
        uint256 balanceBefore = buyer.balance;
        stardustToken.buy{value: 5 ether}(0);
        console.log("buyer cost (bonding curve) : ");
        console.log(balanceBefore - buyer.balance);
        console.log("buyer balance (Token) after (bonding curve): ");
        console.log(stardustToken.balanceOf(buyer));

        // buy exact token
        balanceBefore = buyer.balance;
        // update buy amount to make it identical with previous received
        buyAmount = stardustToken.balanceOf(buyer);
        address[] memory path = new address[](2);
        path[0] = weth;
        path[1] = address(stardustToken);
        uint256 buyTokenWithFee = (10000 * buyAmount) / (10000 - stardustToken.feeBps());
        IUniswapV2Router02(stardustToken.swapRouter()).swapETHForExactTokens{value: buyer.balance}(buyTokenWithFee, path, buyer, block.timestamp);
        console.log("buyer cost (trading on pool) : ");
        console.log(balanceBefore - buyer.balance);
        console.log("buyer balance (Token) after (buy on pool): ");
        console.log(stardustToken.balanceOf(buyer));
        vm.stopPrank();
    }
```

출력 :

```shell
Logs:
  buyer cost (bonding curve) :
  536585365853658536
  buyer balance (Token) after (bonding curve):
  5000000000000000002050000

  buyer cost (trading on pool) :
  511087402334391711
  buyer balance (Token) after (buy on pool):
  10000000000000000004100000
```

본딩 커브에서 마지막으로 구매한 토큰이 풀에서 처음 구매한 토큰보다 비싸지 않도록 초기 유동성 비율을 조정하십시오.

