# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100%의 보안을 보장할 수는 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾기 위해 시도합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**brr-eth** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# brrETH 소개

brrETH는 Compound III의 Base WETH 마켓을 기반으로 구축된 수익 창출형 ETH 파생 상품입니다.

brrETH는 사용하고 이해하기 쉽습니다: ETH를 예치하고 brrETH를 받으십시오. brrETH는 언제든지 원래 예치한 ETH 양과 발생한 이자를 합한 금액으로 상환할 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 심각한 물적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 공격 경로가 온체인 조건을 모방하는 합리적인 가정 하에 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 있습니다.
- 낮음 - 가정이 너무 많거나 희박하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (배포 전, 이미 배포되지 않은 경우)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

**_검토 커밋 해시_ - [694d7c6277669ffdbc947e21ff651a138a56d883](https://github.com/brritoxyz/brr-eth/tree/694d7c6277669ffdbc947e21ff651a138a56d883)**

**_수정 검토 커밋 해시_ - [3157ddf424572f132f2c4b60ec42965a4ae33721](https://github.com/brritoxyz/brr-eth/tree/3157ddf424572f132f2c4b60ec42965a4ae33721)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `BrrETH`
- `BrrETHRedeemHelper`
- `interfaces/**`

# 발견 사항

# [M-01] `getSwapOutput`의 결과가 스왑 작업에서 `minOutput`으로 사용하기에 부정확함

## 심각도

**영향:** 낮음, `minOutput`을 0.02% 감소시키기 때문입니다.

**가능성:** 높음, `harvest`가 호출될 때마다 발생하기 때문입니다.

## 설명

`harvest`가 호출되면 `router.getSwapOutput`을 호출하여 스왑 작업에서 `minOutput`으로 사용될 `quote` 금액을 계산합니다.

```solidity
function harvest() external {
    // ...
    // Fetching the quote onchain means that we're subject to front/back-running but the
    // assumption is that we will harvest so frequently that the rewards won't justify the effort.
>>  (uint256 index, uint256 quote) = router.getSwapOutput(
        keccak256(abi.encodePacked(rewardConfig.token, _WETH)),
        rewards
    );

    // `swap` returns the entire WETH amount received from the swap.
    uint256 supplyAssets = router.swap(
        rewardConfig.token,
        _WETH,
        rewards,
>>      quote,
        index,
        // Receives half of the swap fees (the other half remains in the router contract for the protocol).
        feeDistributor
    );
    // ...
}
```

다음은 `router.getSwapOutput` 내부의 계산으로, 수수료를 공제한 후의 출력을 계산합니다.

```solidity
    function getSwapOutput(
        bytes32 pair,
        uint256 input
    ) external view returns (uint256 index, uint256 output) {
        IPath[][] memory routes = _routes[pair];
        uint256 routesLength = routes.length;

        unchecked {
            for (uint256 i = 0; i < routesLength; ++i) {
                IPath[] memory route = routes[i];
                uint256 routeLength = route.length;
                uint256 quoteValue = input;

                for (uint256 j = 0; j < routeLength; ++j) {
                    quoteValue = route[j].quoteTokenOutput(quoteValue);
                }

                if (quoteValue > output) {
                    index = i;
                    output = quoteValue;
                }
            }
        }

>>      output = output.mulDiv(_FEE_DEDUCTED, _FEE_BASE);
    }
```

그러나 라우터 내부의 `swap` 작업에서는 수수료를 공제하기 **전에** `minOutput` 확인이 수행됩니다.

```solidity
function _swap(
        address inputToken,
        address outputToken,
        uint256 input,
        uint256 minOutput,
        address outputRecipient,
        uint256 routeIndex,
        address referrer
    ) private returns (uint256 output) {
        IPath[] memory route = _routes[
            keccak256(abi.encodePacked(inputToken, outputToken))
        ][routeIndex];
        uint256 routeLength = route.length;
        output = outputToken.balanceOf(address(this));

        for (uint256 i = 0; i < routeLength; ) {
            input = route[i].swap(input);

            unchecked {
                ++i;
            }
        }

        // The difference between the balances before/after the swaps is the canonical output.
        output = outputToken.balanceOf(address(this)) - output;

>>     if (output < minOutput) revert InsufficientOutput();

        unchecked {
            uint256 originalOutput = output;
            output = originalOutput.mulDiv(_FEE_DEDUCTED, _FEE_BASE);

            // Will not overflow since `output` is 99.98% of `originalOutput`.
            uint256 fees = originalOutput - output;

            outputToken.safeTransfer(outputRecipient, output);

            // If the referrer is non-zero, split 50% of the fees (rounded down) with the referrer.
            // The remainder is kept by the contract which can later be withdrawn by the owner.
            if (referrer != address(0) && fees > 1) {
                // Will not overflow since `fees` is 2 or greater.
                outputToken.safeTransfer(referrer, fees / 2);
            }

            emit Swap(inputToken, outputToken, routeIndex, output, fees);
        }
    }
```

`getSwapOutput`의 `quote` 결과를 사용하면 `output`을 원래보다 적은 값과 비교하게 됩니다.

## 권장 사항

라우터 내부의 `swap` 기능을 조정하여 수수료를 공제한 후 `minOutput`을 확인하거나, `quote` 결과를 조정하여 수수료가 공제되기 전의 출력 금액을 반영하도록 하십시오.

# [M-02] `harvest` 함수는 샌드위치 공격 및 예상치 못한 시장 이벤트에 취약함

## 심각도

**영향:** 높음, 공격자가 작업을 샌드위치하여 `swap` 가치를 훔치거나 예상치 못한 시장 이벤트에서 `swap`이 예상외로 낮은 가치를 초래할 수 있기 때문입니다.

**가능성:** 중간, 공격 벡터가 매우 일반적이고 잘 알려져 있으며 가격 변동성은 비-스테이블 코인 토큰에서 일반적이기 때문입니다.

## 설명

프로토콜 팀이 인정했지만, 온체인에서 스왑의 최소 출력을 계산하기 위해 `getSwapOutput`을 사용하는 것은 어떤 상황이나 가정하에서도 권장되지 않습니다. 이 방법은 샌드위치 공격에 취약할 뿐만 아니라 급격한 가격 변동과 같은 시장 이벤트에도 취약합니다.

그 외에도 `harvest`가 항상 호출 가능할 것이라는 가정은 정확하지 않습니다. `_COMET`에 대한 `supply` 기능이 일시 중지되어 호출이 되돌려질 수 있기 때문입니다. 드물지만 가능한 경우로, Compound가 WETH 풀을 일시 중지하더라도 이자는 여전히 발생하고 보상 금액은 샌드위치 공격이 가능해질 만큼 충분히 커질 수 있습니다.

```solidity
    function harvest() external {
        // ...

        // Fetching the quote onchain means that we're subject to front/back-running but the
        // assumption is that we will harvest so frequently that the rewards won't justify the effort.
        // @audit - quote here, already deducted by fee, while the minOutput check at swap, is step before fees are deducted
>>      (uint256 index, uint256 quote) = router.getSwapOutput(
            keccak256(abi.encodePacked(rewardConfig.token, _WETH)),
            rewards
        );

        // `swap` returns the entire WETH amount received from the swap.
>>      uint256 supplyAssets = router.swap(
            rewardConfig.token,
            _WETH,
            rewards,
            quote,
            index,
            // Receives half of the swap fees (the other half remains in the router contract for the protocol).
            feeDistributor
        );

       // ...
    }
```

## 권장 사항

`harvest` 함수 내에 최소 출력을 매개변수로 넣는 것을 고려하고, 이 함수가 봇에 의해 자주 호출될 계획이라면 특정 역할만 호출할 수 있도록 제한할 수 있습니다.

# [M-03] 초기 그리핑(Griefing) 공격 가능

## 심각도

**영향:** 높음, 피해자가 자금을 잃기 때문입니다.

**가능성:** 낮음, 공격자에게 비용이 발생하기 때문입니다.

## 설명

유명한 초기 예금 공격은 solady 라이브러리에 의해 대부분 완화됩니다. 그러나 이 공격을 수행하면 사용자에게 그리핑을 유발하고(공격자에게 높은 비용 발생) 금고를 이상한 상태로 만들 수 있는 이상한 동작이 발생할 수 있습니다.

다음은 영향을 보여주는 PoC입니다.

```solidity
    address bob = makeAddr("bob");

    function testInitialSupplyAttack() public {
    	// attacker starts with 13 ether
        _getCWETH(13e18 + 6);

        // initial small deposit
        vault.deposit(11,address(this));
        assertEq(10,vault.balanceOf(address(this)));

        // large deposit to inflate the exchange rate
        _COMET.safeTransfer(address(vault),11e18-9);

        // share price is not 1e18 assets
        assertEq(1e18,vault.convertToAssets(1));

        // boilerplate to get cWETHv3
        deal(_WETH,bob,1e18 + 3);
        vm.startPrank(bob);
        _WETH.safeApprove(_COMET,1e18+3);
        IComet(_COMET).supply(_WETH, 1e18+3);
        _COMET.safeApproveWithRetry(address(vault), type(uint256).max);

        // victim deposits into the vault
        vault.deposit(1e18+1,bob);
        // due to exchange rate gets 0 shares
        assertEq(0,vault.balanceOf(bob));
        vm.stopPrank();

        vault.redeem(10, address(this), address(this));
        console.log("exchange rate",vault.convertToAssets(1));
        console.log("_COMET.balanceOf(address(vault))",_COMET.balanceOf(address(vault)));
        console.log("_COMET.balanceOf(address(attacker))",_COMET.balanceOf(address(this)));
    }
```

출력 결과:

```
Logs:
  exchange rate 1090909090909090909
  _COMET.balanceOf(address(vault)) 1090909090909090908
  _COMET.balanceOf(address(attacker)) 12909090909090909091
```

보시다시피 공격자는 공격을 위해 `0.1 eth`를 지불해야 합니다. 그러나 그들은 피해자의 `1 eth`를 계약에 효과적으로 잠갔습니다.

이것은 공격자에게 수익성이 없지만 금고를 이상한 상태로 만들고 피해자는 여전히 토큰을 잃게 됩니다.

## 권장 사항

초기 소량 예금으로 이를 완화하는 것을 고려하십시오.

# [L-01] 지원되지 않는 ERC4626 함수는 조용히 성공하는 대신 호출을 되돌려야 함

`BrrETH`는 ERC4626을 완전히 준수하지 않도록 설계되었습니다. 그러나 지원되지 않는 함수(`mint`, `withdraw`, `previewWithdraw`, `previewMint`, `maxWithdraw`, `maxMint`)는 되돌리기보다는 빈 함수로 재정의됩니다. 제3자나 통합자가 `BrrETH`와 작업하는 경우 함수 호출은 조용히 성공할 것입니다. 지원되지 않는 함수는 빈 함수로 재정의하는 대신 되돌리는 것이 모범 사례로 간주됩니다.

# [L-02] `harvest`는 Comet 보상을 변경하기 전에 트리거되어야 함

`harvest` 함수를 트리거하지 않고 Comet 보상을 변경하면 이전 Comet 보상 계약의 보상 토큰이 `BrrETH` 내부에 갇힐 수 있습니다. `setCometRewards` 내부에서 호출자가 `harvest` 함수를 트리거할 수 있는 옵션을 제공하는 추가 부울 매개변수를 갖거나, 가능하다면 설정된 혜성 보상을 변경하기 전에 항상 `harvest`를 호출하라는 메모/주석을 남기는 것이 좋습니다.

# [L-03] `approveTokens`는 Comet 보상 및 라우터 변경 후 트리거되어야 함

`approveTokens` 함수를 즉시 트리거하지 않고 Comet 보상 및 라우터를 변경하면 승인 부족으로 인해 `harvest`가 트리거될 때 되돌리기 기간이 발생할 수 있습니다. `setCometRewards` 및 `setRouter` 함수 내부에서 즉시 `approveTokens`를 호출하는 것을 고려하십시오.

# [L-04] `deposit` 및 `redeem`에 대한 슬리피지 보호 부족

`deposit`의 계산된 주식(shares)과 `redeem`의 계산된 자산(assets)은 `BrrETH` 금고 내부의 현재 총 자산 및 총 공급에 크게 의존하며, 이는 가치가 변하여 주식/자산 계산 결과에 영향을 줄 수 있습니다. 슬리피지 확인을 제공하는 `deposit` 함수를 래핑하는 라우터/헬퍼를 만드는 것을 고려하십시오. 또한 `BrrETHRedeemHelper` 내부의 `redeem`에 대한 슬리피지 확인을 추가하십시오.

# [L-05] 불충분한 입력 유효성 검사

`setRewardFee`는 소유자가 `rewardFee`를 `_FEE_BASE`보다 크게 설정할 수 있도록 허용하여 예상치 못한 동작을 초래할 수 있습니다. `supplyAssets -= fees`가 언더플로되어 `supplyAssets`의 값이 지나치게 높아져 `COMET`에 대한 공급이 실패하므로 호출이 되돌려질 것입니다. 그 외에도 보상 수수료에 대한 최대 한도를 두는 것은 일반적으로 중앙화 위험 문제를 최소화하는 좋은 관행입니다.

### 토론

**Pashov Audit Group:** `rewardFee`는 이제 `_FEE_BASE`보다 클 수 없도록 확인됩니다. 그러나 중앙화 위험을 최소화하기 위해 여전히 최대 수수료 비율에 대한 확인을 구현하는 것이 좋습니다.

# [L-06] 이전 승인이 취소되지 않음

계약을 구성할 때 Comet 보상 토큰에 대해 라우터에 대한 최대 승인이 설정됩니다.

```solidity
        // Enable the router to swap our Comet rewards for WETH.
        rewardConfig.token.safeApproveWithRetry(
            address(router),
            type(uint256).max
        );
```

그러나 컴파운드 거버넌스는 `Comet`에서 보상 토큰을 변경할 수 있습니다.

```solidity
    function setRewardConfig(address comet, address token) external {
        setRewardConfigWithMultiplier(comet, token, FACTOR_SCALE);
    }
```

그리고 `owner`는 `BrrETH`에서 `router`를 변경할 수 있습니다.

```solidity
    function setRouter(address _router) external onlyOwner {
        if (_router == address(0)) revert InvalidRouter();

        router = IRouter(_router);

        emit SetRouter(_router);
    }
```

이 중 하나라도 변경되면 이전 승인은 그대로 유지됩니다.

Comet이 가진 것과 유사하게 `owner`만 호출할 수 있는 승인 취소/설정 호출을 추가하는 것을 고려하십시오.

```solidity
    function approveThis(address manager, address asset, uint amount) override external {
        if (msg.sender != governor) revert Unauthorized();

        ERC20(asset).approve(manager, amount);
    }
```

그렇게 하면 `owner`는 필요한 승인을 수정/취소할 수 있습니다. `setRouter`는 프로토콜의 제어 하에 있으므로 이미 있는 이전 라우터에 대한 승인을 재설정하는 호출을 포함하는 것도 고려하십시오.

