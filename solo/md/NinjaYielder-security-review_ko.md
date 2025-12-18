# pashov의 Ninja Yield 프로토콜 보안 검토

**_검토 커밋 해시 - [9e9367120d45fdd6144328964fabb8f57610661c](https://github.com/0xTK421/ninja-core-contracts/tree/9e9367120d45fdd6144328964fabb8f57610661c)_**

### 코드는 총 6시간 동안 검토되었습니다.
---

# [H-01] `withdraw` 메서드의 잘못된 사용자 회계

## 개념 증명

`NYProfitTakingVaultBaseV1`의 `withdraw` 메서드는 다음 줄에서 잘못된 사용자 회계를 수행합니다.

```solidity
user.amount = user.amount - _shares;
```

`deposit` 메서드에서 우리는 `user.amount`를 사용하여 예치된 `underlying` 토큰의 양을 저장하지만, `withdraw`에서 코드는 `underlying` 토큰을 빼는 대신 소각된 주식(shares)을 뺍니다.

## 영향

`shares`는 `underlying`과 1:1이 아니므로 출금할 때마다 사용자 회계가 완전히 엉망이 됩니다. 두 가지 방향이 가능합니다. `_shares`가 출금된 금액보다 적으면 사용자는 예치한 것보다 더 많이 출금할 수 있어 본질적으로 볼트를 0으로 고갈시킬 수 있습니다. `_shares`가 출금된 금액보다 많으면 사용자는 예치한 것보다 적게 출금하게 되어 본질적으로 사용자의 가치 손실이 발생합니다.

## 권장 사항

다음과 같이 변경하십시오.

```diff
- user.amount = user.amount - _shares;
+ user.amount = user.amount - r;
```

# [H-02] 첫 번째 볼트 예금자가 후속 예금자의 토큰을 훔칠 수 있음

## 개념 증명

다음 시나리오를 상상해 보십시오.

1. 새로운 볼트가 배포되고 구성되었으며 아직 예금자가 없습니다.
2. Alice가 10 ether(10e18) 상당의 `underlying`을 예치하고 싶어 공개 멤풀에 트랜잭션을 보냅니다.
3. MEV 봇이 Alice의 트랜잭션을 보고 1 wei(1e-18)의 `underlying`을 예치하여 프런트 런(front run)합니다. 그 결과 그는 1 wei(1e-18)의 볼트 토큰(shares)을 받습니다.
4. MEV 봇은 또한 `ERC20::transfer`를 통해 볼트로 10 ether(10e18)의 `underlying`을 전송하여 Alice의 트랜잭션을 프런트 런합니다.
5. 이제 코드는 Alice의 주식을 `shares = (_amount * totalSupply()) / _pool;`로 계산하는데, 이는 10e18 * 1 / (10e18 + 1)로 0입니다.
6. Alice는 0 주식을 민팅받지만 그녀는 10e18의 `underlying`을 예치했습니다.
7. 이제 MEV 봇은 자신의 1e-18(1 wei) 주식으로 `withdraw`를 호출하여 Alice의 트랜잭션을 백런(backrun)합니다(이는 총공급량임). 따라서 그는 자신의 예금 + Alice의 전체 예금을 인출합니다.

예금자가 문제를 알아차릴 때까지 이를 여러 번 반복할 수 있습니다.

## 영향

그 결과 모든 후속 예금자에 대해 100% 가치 손실이 발생합니다.

## 권장 사항

UniswapV2는 두 가지 유형의 보호로 이 문제를 해결했습니다.

[첫 번째](https://github.com/Uniswap/v2-core/blob/master/contracts/UniswapV2Pair.sol#L119-L121), 첫 번째 `mint`에서 실제로 첫 1000 주식을 0 주소로 민팅합니다.

[두 번째](https://github.com/Uniswap/v2-core/blob/ee547b17853e71ed4e0101ccfd52e70d5acded58/contracts/UniswapV2Pair.sol#L125), 민팅된 주식이 0이 아니어야 합니다.

이 둘을 모두 구현하면 이 취약점이 해결됩니다.

# [M-01] MEV는 누락된 슬리피지 허용 오차 값으로 인해 모든 수확(harvest)을 샌드위치 공격할 수 있음

## 개념 증명

`NyPtvFantomWftmBooSpookyV2StrategyToUsdc`에서 `_harvestCore` 메서드가 호출될 때마다(각 수확 시) `_swapFarmEmissionTokens` 메서드를 호출하며, 이 메서드에는 다음 코드가 있습니다.

```solidity
IUniswapV2Router02(SPOOKY_ROUTER).swapExactTokensForTokensSupportingFeeOnTransferTokens(
      booBalance,
      0,
      booToUsdcPath,
      address(this),
      block.timestamp
    );
```

여기서 "0"은 슬리피지 허용 오차에 사용되는 `amountOutMin` 인수의 값입니다. 여기서 0 값은 본질적으로 100% 슬리피지 허용 오차를 의미합니다. 이것은 MEV와 봇이 각 전략의 스왑에서 플래시 대출 샌드위치 공격을 수행하여 각 거래에서 매우 큰 슬리피지를 초래하는 매우 쉬운 표적입니다.

## 영향

100% 슬리피지 허용 오차는 전략(따라서 볼트와 사용자)이 원래 받아야 할 것보다 훨씬 적은 가치를 받도록 악용될 수 있습니다. 거래 트랜잭션이 공개 멤풀을 통과하는 경우 모든 거래에서 수행될 수 있습니다.

## 권장 사항

여기서 가장 좋은 해결책은 개인 멤풀을 통해 트랜잭션을 보낼 신뢰할 수 있는 주소 목록만 볼트의 `harvest` 메서드를 호출할 수 있도록 하는 것입니다. 이것을 슬리피지 허용 오차 백분율(전략의 설정자를 통해 구성 가능할 수 있음)만큼 예상 `amountOut`과 차이가 나는 `amountOutMin`에 대한 온체인 계산과 결합하면, MEV 샌드위치 공격으로부터 당신을 보호하기에 충분할 것입니다.

# [M-02] 하드코딩된 스왑 경로는 가장 최적/유동적이지 않을 수 있음

## 개념 증명

현재 `NyPtvFantomWftmBooSpookyV2StrategyToUsdc`에서 `booToUsdcPath` 거래 경로의 값은 구성할 수 없으며 기본적으로 `[BOO, USDC]`로 하드코딩되어 있습니다. 스왑 라우터의 경우도 마찬가지로 현재 SpookySwap 라우터를 가리키도록 하드코딩되어 있습니다. 문제는 SpookySwap의 `BOO/USDC` 풀이 가장 최적이고 유동적이지 않을 수 있으며, 대신 `BOO/USDT`로 간 다음 `USDT/USDC`로 가는 것이 더 나을 수 있다는 것입니다. 예를 들어 `BOO/USDC` 쌍이 유동성의 대부분을 잃으면(어쩌면 LP가 인센티브를 많이 받지 못하거나 다른 곳으로 이동하기로 결정한 경우) 전략은 여전히 SpookySwap의 비유동적/비최적 `BOO/USDC` 쌍을 통해 `harvest` 시 스왑을 수행하도록 강제됩니다.

## 영향

더 유동적인 풀이 스왑에 사용되었다면 슬리피지가 적어 더 큰 보상을 받을 수 있었을 것이므로, 이는 볼트 사용자의 가치 손실로 이어질 수 있습니다.

## 권장 사항

거래 라우터와 거래 경로 모두에 대한 설정자(setter) 함수를 추가하여 구성할 수 있도록 하십시오. 한 가지 가능한 옵션은 가장 유동적인 3개의 Fantom 거래소와 3개의 가능한 거래 경로를 하드코딩하고 설정자를 통해 전환하는 것입니다.

# [L-01] 외부 호출이 있는 함수에 `nonReentrant` 수정자 누락

## 개념 증명

`NYProfitTakingVaultBaseV1` 계약은 OpenZeppelin의 `ReentrancyGuard` 계약을 상속하며 ERC20 외부 호출을 수행하는 대부분의 상태 변경 메서드에 `nonReentrant` 수정자를 표시했습니다. 문제는 `depositOutputTokenForUsers` 및 `earn` 메서드와 같이 ERC20 외부 호출을 수행하는 일부 함수에 수정자가 누락되어 있다는 것입니다. 현재 메서드는 악용할 수 없지만 ERC777 토큰(ERC20 호환 가능)은 사전 및 사후 후크로 인해 메서드 호출에 재진입할 수 있으므로, `nonReentrant` 수정자는 안전하지 않은 ERC20 외부 호출을 수행할 때 중요한 보안 조치입니다.

## 영향

ERC777 토큰을 `rewardToken` 또는 `underlying`으로 사용한 경우 `depositOutputTokenForUsers` 및 `earn` 메서드에 재진입할 수 있습니다. 현재는 악용할 수 없지만 새 코드가 추가될 때 큰 문제가 될 수 있습니다.

## 권장 사항

`depositOutputTokenForUsers` 및 `earn` 메서드에 `nonReentrant` 수정자를 추가하십시오.

# [L-02] `underlying` 또는 `rewardToken`이 두 개의 주소를 가진 토큰인 경우 `inCaseTokensGetStuck` 메서드를 사용하여 사용자를 러그(rug)할 수 있음

## 개념 증명

블록체인의 일부 ERC20 토큰은 프록시 뒤에 배포되므로 기능에 대해 최소 2개의 진입점(프록시 및 구현)이 있습니다. 예로는 Synthetix의 `ProxyERC20` 계약이 있으며 여기에서 `sUSD, sBTC` 등과 상호 작용할 수 있습니다. 이러한 토큰이 볼트에서 `underlying` 토큰으로 사용된 경우 소유자는 `inCaseTokensGetStuck` 메서드로 모든 예금자를 러그할 수 있습니다. 다음 확인 사항이 있더라도 말입니다.

```solidity
if (_token == address(underlying)) {
      revert NYProfitTakingVault__CannotWithdrawUnderlying();
}
if (_token == address(rewardToken)) {
      revert NYProfitTakingVault__CannotWithdrawRewardToken();
}
```

토큰에 여러 주소가 있으므로 관리자는 다른 주소를 제공하고 이러한 확인을 통과할 수 있습니다.

## 영향

잠재적인 영향은 사용자에 대해 예치된 토큰의 100% 손실이지만, 볼트에서 사용되는 악의적/침해된 소유자와 특별한 유형의 ERC20 토큰이 필요합니다.

## 권장 사항

인출된 토큰의 주소를 확인하는 대신 전송 전후의 `underlying` 및 `rewardToken` 잔액을 확인하고 동일한지 확인하는 것이 더 나은 접근 방식입니다.

# [L-03] `totalSupply`가 0일 때 `getPricePerFullShare` 메서드가 잘못된 값을 반환함

## 개념 증명

`getPricePerFullShare` 메서드는 현재 다음 공식으로 결과를 계산합니다.

```solidity
return totalSupply() == 0 ? 1e18 : (balance() * 1e18) / totalSupply();
```

문제는 사용된 기본 토큰이 18 미만 또는 초과의 소수점을 가지고 있고 `totalSupply`가 여전히 0인 경우, 반환되는 가격이 1e18이므로 올바르지 않다는 것입니다. `totalSupply`가 0일 때 1e18을 반환하려는 의도는 첫 예치 시 민팅된 주식의 양에서 비롯된 것 같습니다.

```solidity
if (totalSupply() == 0) {
      shares = _amount;
}
```

따라서 1주가 1 토큰과 같아 보이지만 토큰의 소수점 자릿수가 18이 아닌 경우 1 토큰은 해당 토큰의 1e18 wei가 아닙니다.

## 영향

이 함수는 `public view`로 표시되어 있고 프로토콜 어디에서도 사용되지 않으므로 아마도 프런트 엔드에서만 사용되어 볼트 주식의 초기 가격 책정에 영향을 미칠 것입니다.

## 권장 사항

다음과 같이 변경하십시오.

```diff
- return totalSupply() == 0 ? 1e18 : (balance() * 1e18) / totalSupply();

+ return totalSupply() == 0 ? 10**underlying.decimals() : (balance() * 1e18) / totalSupply();
```
