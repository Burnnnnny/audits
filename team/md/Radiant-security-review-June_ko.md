# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**radiant-capital/v2-core** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Radiant 정보

UniV3TokenizedLp는 Uniswap V3와 유사한 풀에서 토큰화된 유동성 포지션을 관리합니다. 이를 통해 사용자는 유동성 풀의 지분을 나타내는 ERC20 토큰을 대가로 예치할 수 있습니다. 이 계약에는 유동성 포지션을 재조정하고 가격 조작을 방지하기 위해 외부 오라클 가격 피드의 정확성을 보장하는 메커니즘이 포함되어 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 희박하거나, 인센티브가 거의 또는 전혀 없이 공격자가 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정하는 것이 좋음

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [2f6eaadd32dd2d650499ee21e2b75824dd429ebf](https://github.com/radiant-capital/v2-core/tree/2f6eaadd32dd2d650499ee21e2b75824dd429ebf)

_수정 검토 커밋 해시_ - [d64d0bb5211c4cbf392f5cb39c078e18c928cc48](https://github.com/radiant-capital/v2-core/tree/d64d0bb5211c4cbf392f5cb39c078e18c928cc48)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `UniV3TokenizedLp`
- `UniV3PoolHelper`

# 발견 사항

# [C-01] Helper에서 TokenizedLP로 승인 없음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

자금 흐름은 LockZap을 통해 `UniV3PoolHelper.zapETH()` 또는 `UniV3PoolHelper.zapTokens()`로 이동합니다. 그러나 나중에 토큰은 `UniV3TokenizedLp.deposit()`에 예치되어야 합니다.

```solidity
	function zapWETH(uint256 amount) public onlyLockZap returns (uint256 liquidity) {
		IERC20(weth9Addr).safeTransferFrom(msg.sender, address(this), amount);
		uint256 token0Amt = token0 == weth9Addr ? amount : 0;
		uint256 token1Amt = token1 == weth9Addr ? amount : 0;
		liquidity = tokenizedLpToken.deposit(token0Amt, token1Amt, msg.sender);
	}
```

`UniV3TokenizedLp`는 승인이 필요한 `trasnferFrom()`을 통해 `UniV3PoolHelper.zapETH()`로부터 토큰을 가져옵니다. 그러나 `UniV3PoolHelper`는 토큰을 승인하지 않으므로 전체 자금 흐름이 되돌려집니다.

## 권장 사항

`UniV3PoolHelper`에서 `UniV3TokenizedLp`로 승인을 제공하는 것을 고려하십시오.

# [H-01] `UniV3TokenizedLp`는 초기 기부로 공격받을 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`UniV3TokenizedLp`에 사용자 보증금이 없을 때, 첫 번째 사용자는 `1` wei LP를 민팅한 다음 `1e18` WETH를 `UniV3TokenizedLp` 계약으로 전송할 수 있습니다. 이로 인해 풀의 자산 가치는 매우 높지만 `totalSupply`는 1이 됩니다.

그런 다음 새 사용자가 자산을 전송한 후 나눗셈 손실로 인해 0 LP를 민팅할 수 있습니다. 이러한 자산은 모두 1wei LP가 보유한 자산으로 계산되며 공격자가 `withdraw`를 호출할 때 결국 도난당하게 됩니다.

```solidity
		if (totalSupply() != 0) {
			// Price the pool0 in token1 at oracle price
			uint256 pool0PricedInToken1 = (pool0 * oraclePrice) / PRECISION;
			// Compute ratio of total shares to pool AUM in token1
			shares = (shares * totalSupply()) / (pool0PricedInToken1 + pool1);
		}
```

## 권장 사항

이것은 우회 해결책입니다. 동일한 블록에서 `UniV3TokenizedLp`를 배포하고 주소 `0`에 일부 LP를 예치하십시오.

또는 가상 자산과 가상 주식을 사용하십시오. https://blog.openzeppelin.com/a-novel-defense-against-erc4626-inflation-attacks를 참조하십시오.

# [H-02] 지속적인 입출금을 통한 그리핑(Griefing) 공격

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`deposit` 함수는 사용자가 리밸런서가 관리하는 유동성 풀(LP)에 추가할 자금을 예치할 수 있도록 합니다. 토큰을 인출할 때 계약은 인출 요청을 이행하기 위해 풀에서 필요한 유동성을 제거합니다. 이 메커니즘은 공격자가 그리핑 공격을 수행하는 데 악물될 수 있습니다. 공격자는 각 블록에서 많은 양을 지속적으로 예치하고 다음 블록에서 인출하여 계약이 풀에서 유동성을 반복적으로 제거하도록 강제할 수 있습니다.

이러한 반복적인 유동성 인출은 자금을 계약에 투자되지 않은 상태로 남겨두어 다음 재조정까지 생성될 수 있었던 잠재적 수수료 손실을 초래합니다. 이 공격의 심각성은 `checkLastBlockAction` 수정자 우회 문제와 결합될 때 증가합니다. 이를 통해 공격자는 플래시 론을 활용하고 각 재조정 트랜잭션을 백런(backrun)하여 사실상 풀에서 모든 유동성을 지속적으로 제거할 수 있기 때문입니다.

## 권장 사항

계약의 자금이 충분하지 않은 경우 풀에서 유동성을 제거하기 전에 먼저 계약의 잔액에서 인출을 이행하는 것을 고려하십시오.

# [H-03] `withdraw()`가 보류 중인 수수료를 고려하지 않음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

사용자의 지분에 해당하는 자산은 다음과 같습니다.

1. 유동성
2. 보류 중인 수수료
3. UniV3TokenizedLp의 남은 잔액

`withdraw` 함수는 `_burnLiquidity`를 호출하여 사용자의 유동성을 추출합니다. `_burnLiquidity`는 `IUniswapV3Pool.burn`을 호출합니다. `burn`의 반환 값인 `owed0` 및 `owed1`에는 보류 중인 수수료가 포함되지 않습니다(보류 중인 수수료는 포지션에 직접 포함됨).

```bash
			(uint256 owed0, uint256 owed1) = IUniswapV3Pool(pool).burn(tickLower, tickUpper, liquidity);
```

보류 중인 수수료가 포함되지 않으므로 사용자는 더 적은 수입을 받게 됩니다.

## 권장 사항

`IUniswapV3Pool.burn`을 호출한 후 사용자가 받을 자격이 있는 보류 중인 수수료를 계산하여 함께 수집하십시오.

# [M-01] 수수료 조정 전 보류 중인 수수료 정산 필요

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`setFeeRecipient`, `setBaseFee`, `setBaseFeeSplit`, `setAffiliate`와 같은 수수료 관련 함수는 배포 대상과 수수료 금액에 영향을 미칩니다. 변경 사항이 적용되기 전에 보류 중인 수수료를 정산해야 합니다. 그렇지 않으면 보류 중인 수수료 정산에 오류가 발생할 수 있습니다.

예를 들어, 현재 `affiliate`가 Alice인 경우 보류 중인 수수료는 100입니다. affiliate를 Bob으로 변경한 후 이 수수료는 재조정 중에 Alice 대신 Bob에게 할당됩니다.

## 권장 사항

수수료를 조정하기 전에 보류 중인 수수료를 먼저 정산해야 합니다.

# [M-02] `rebalance`가 잘못된 잔액을 사용함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`swapQuantity` 매개변수가 `0`이면 `rebalance` 함수는 스왑을 먼저 수행합니다. 스왑 후 계약의 `token0` 및 `token1` 잔액이 변경됩니다. 그러나 `_liquidityForAmounts`는 여전히 스왑 전의 잔액을 사용합니다.

```solidity
	function rebalance(int24 _baseLower, int24 _baseUpper, int256 swapQuantity) public nonReentrant isRebalancer {
		if (_baseLower >= _baseUpper || _baseLower % tickSpacing != 0 || _baseUpper % tickSpacing != 0) {
			revert UniV3TokenizedLp_BasePositionInvalid();
		}
		(uint256 token0Bal, uint256 token1Bal) = _updateAndCollectPositionFees();

		// Swap tokens if required as specified by `swapQuantity`
		if (swapQuantity != 0) {
			IUniswapV3Pool(pool).swap(
				address(this),
				swapQuantity > 0, // zeroToOne == true if swapQuantity is positive
				swapQuantity > 0 ? swapQuantity : -swapQuantity,
				// No limit on the price, swap through the ticks, until the `swapQuantity` is exhausted
				swapQuantity > 0 ? UniV3PoolMath.MIN_SQRT_RATIO + 1 : UniV3PoolMath.MAX_SQRT_RATIO - 1,
				abi.encode(address(this))
			);
		}

		baseLower = _baseLower;
		baseUpper = _baseUpper;

		// Mint liquidity at the new baseLower and baseUpper ticks
		uint128 baseLiquidity = _liquidityForAmounts(baseLower, baseUpper, token0Bal, token1Bal);
		_mintLiquidity(baseLower, baseUpper, baseLiquidity);
	}
```

이로 인해 두 가지 결과가 발생합니다.

1. uniswap 풀에 더 적은 유동성 예치
2. 또는 토큰 부족으로 인한 되돌리기(revert)

## 권장 사항

스왑 후 새 잔액을 `_liquidityForAmounts`에 전달해야 합니다.

# [M-03] 유니스왑 풀 생성 및 가격 초기화를 통한 `initializePool` DoS

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`initializePool`은 유니스왑 풀에서 `sqrtPriceX96`을 가져오는 대신 계약의 잔액을 사용하여 `sqrtPriceX96`을 계산합니다.

```solidity
		uint256 initRdntBal = IERC20(rdntAddr).balanceOf(address(this));
		uint256 initWeth9Bal = IERC20(weth9Addr).balanceOf(address(this));
		uint160 sqrtPriceX96 = UniV3PoolMath.encodePriceSqrtX96(
			rdntAddr == token0 ? initRdntBal : initWeth9Bal,
			rdntAddr == token1 ? initRdntBal : initWeth9Bal
		);
```

LP 토큰을 민팅하기 전에 `initializePool`은 위 `sqrtPriceX96`을 기반으로 예상되는 LP 토큰 수를 계산합니다. 즉, `initializePool`은 계약의 토큰 잔액을 기반으로 민트 LP를 최대화합니다.

```solidity
			uint128 fullRangeliquidity = UniV3PoolMath.getLiquidityForAmounts(
				sqrtPriceX96,
				UniV3PoolMath.MIN_SQRT_RATIO,
				UniV3PoolMath.MAX_SQRT_RATIO,
				amount0,
				amount1
			);

			pool.mint(address(this), baseLower_, baseUpper_, fullRangeliquidity, abi.encode(address(this)));
```

그러나 공격자는 사전에 유니스왑 풀에서 약간의 스왑을 수행하여 `initializePool`이 예상 수의 LP를 민팅하지 못하게 할 수 있습니다.

## 권장 사항

유니스왑 풀이 이미 존재하는 경우 토큰 잔액을 기준으로 계산하는 대신 풀에서 `sqrtPriceX96`을 가져오십시오. 동시에 최소 LP 수입 금액을 설정하십시오.

또한 풀이 존재하지 않는 경우 공격자가 사전에 풀을 생성하고 가격을 초기화할 수도 있습니다.

# [M-04] 공격자가 오라클 가격 차이를 이용하여 차익 거래를 할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

체인링크 오라클은 가격 변동이 편차 임계값에 도달할 때만 새로운 가격을 제출합니다. 예를 들어 ETH/USD의 편차 임계값은 `0.5%`입니다. 공격자는 이를 사용하여 차익 거래를 할 수 있습니다. 구체적인 시나리오는 다음과 같습니다.

1. 공격자1이 일부 USDC를 예치합니다.
2. 오라클이 ETH/USD 가격을 `0.5%` 증가하도록 업데이트합니다.
3. 공격자1은 `checkLastBlockAction`을 우회하기 위해 모든 LP를 공격자2에게 전송합니다.
4. 공격자2는 모든 LP를 인출하고 ETH + USDC를 얻습니다.
5. 공격자2는 토큰을 공격자1에게 반환합니다.

위 단계는 동일한 블록에서 완료될 수 있습니다. 공격자는 더 낮은 가격에 ETH를 샀고 다른 DEX에서 ETH를 팔 수 있습니다. 더 자세한 예시는 PoC를 참조하십시오.

## 권장 사항

인출 함수가 약간의 수수료를 부과하게 하십시오.

# [M-05] 모든 사용자에 대해 블록당 한 번만 `UniV3PoolHelper` 예치

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`checkLastBlockAction`은 주어진 `msg.sender`에 대해 하나의 예치만 허용합니다.

```solidity
	modifier checkLastBlockAction() {
		if (_callerLastBlockAction[msg.sender] == block.number) revert UniV3TokenizedLp_NoDepositOrWithdrawLoop();
		_;
		_callerLastBlockAction[msg.sender] = block.number;
```

실제로 이는 `UniV3PoolHelper` 사용자가 블록당 한 번만 예치할 수 있음을 의미하며, 이는 모든 `UniV3PoolHelper` 사용자(따라서 모든 LockZap 사용자)에게 해당됩니다.
이 조건은 너무 엄격하여 `UniV3PoolHelper`가 자주 사용되는 경우 블록 내 사용자에게 되돌리기(revert)를 초래할 수 있습니다.

## 권장 사항

해결책 중 하나는 LockZap + UniV3PoolHelper 최종 사용자에 대한 작업을 별도로 추적하거나, 별도의 수정자에서 또는 checkLastBlockAction 수정자의 특수 조건(예: `msg.sender`가 아닌 `to` 입력 확인)을 사용하는 것일 수 있습니다.

# [M-06] `latestAnswer` 대신 `latestRoundData` 사용

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`UniV3TokenizedLp`는 마지막 오라클 가격을 얻기 위해 `latestAnswer()`를 호출하고 있습니다. 이 메서드는 마지막 값을 반환하지만 데이터가 최신인지 확인할 수 없습니다. 반면 `latestRoundData()` 메서드를 호출하면 몇 가지 추가 유효성 검사를 실행할 수 있습니다.

## 권장 사항

다음과 같은 추가 확인과 함께 latestRoundData()를 사용하는 것을 고려하십시오.

```solidity
 (
          roundId,
          rawPrice,
          ,
          updateTime,
          answeredInRound
        ) = IChainlinkAdapter(_oracle).latestRoundData();
        require(rawPrice > 0, "Chainlink price <= 0");
        require(updateTime != 0, "Incomplete round");
        require(answeredInRound >= roundId, "Stale price");
```

# [M-07] `rebalance` 함수에 슬리피지 확인 부족

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`rebalance` 함수는 유니스왑 V3 LP 포지션을 업데이트하고 지정된 하한 및 상한 틱을 기반으로 표시된 스왑을 수행합니다. 그러나 이 함수에는 슬리피지 확인이 없어 샌드위치 공격에 취약합니다. 공격자는 리밸런스 트랜잭션을 프론트런(front-run)하여 불리한 가격 변동을 일으킨 다음 트랜잭션을 백런(back-run)하여 가격을 되돌릴 수 있으며, 이로 인해 스왑 시 계약에 자금 손실이 발생할 수 있습니다.

```solidity
function rebalance(int24 _baseLower, int24 _baseUpper, int256 swapQuantity) public nonReentrant isRebalancer {
    ...
    if (swapQuantity != 0) {
        IUniswapV3Pool(pool).swap(
            address(this),
            swapQuantity > 0, // zeroToOne == true if swapQuantity is positive
            swapQuantity > 0 ? swapQuantity : -swapQuantity,
            // No limit on the price, swap through the ticks, until the `swapQuantity` is exhausted
            swapQuantity > 0 ? UniV3PoolMath.MIN_SQRT_RATIO + 1 : UniV3PoolMath.MAX_SQRT_RATIO - 1,
            abi.encode(address(this))
        );
    }
    ...
}
```

슬리피지 확인이 없다는 것은 함수가 스왑의 가격 영향을 확인하지 않는다는 것을 의미하며, 외부 행위자에 의한 조작에 취약합니다.

## 권장 사항

이 문제를 해결하려면 `rebalance` 함수에 슬리피지 확인을 추가하여 슬리피지 보호를 구현하십시오. 이는 스왑에 허용되는 가격 제한을 지정하고 이러한 제한을 위반할 경우 트랜잭션을 되돌리는 방식으로 달성할 수 있습니다.

# [M-08] 우회 가능한 `checkLastBlockAction` 수정자

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`checkLastBlockAction` 수정자는 사용자가 동일한 블록에서 입금 및 인출을 하는 것을 방지하기 위한 것이며, 아마도 플래시 론 공격에 대한 조치일 것입니다. 그러나 이 확인은 쉽게 우회될 수 있습니다. 사용자는 입금하여 공유 토큰을 받은 다음, 이 공유 토큰을 자신이 제어하는 다른 주소로 전송하고 해당 주소에서 동일한 블록 내에 인출을 진행할 수 있습니다.

```solidity
	modifier checkLastBlockAction() {
		if (_callerLastBlockAction[msg.sender] == block.number) revert UniV3TokenizedLp_NoDepositOrWithdrawLoop();
		_;
		_callerLastBlockAction[msg.sender] = block.number;
	}
<...>
	function deposit(
		uint256 deposit0,
		uint256 deposit1,
		address to
	) external override nonReentrant checkLastBlockAction returns (uint256 shares) {
<...>
	function withdraw(
		uint256 shares,
		address to
	) external override nonReentrant checkLastBlockAction returns (uint256 amount0, uint256 amount1) {
```

## 권장 사항

이 문제를 해결하려면 `_afterTokenTransfer` 함수를 재정의하여 `to` 주소에 대한 `_callerLastBlockAction` 매핑을 업데이트하십시오. 이렇게 하면 토큰이 주소 간에 전송될 때도 블록 작업에 대한 제한이 일관되게 적용됩니다.

```solidity
function _afterTokenTransfer(address from, address to, uint256 amount) internal override {
    super._afterTokenTransfer(from, to, amount);
    _callerLastBlockAction[to] = block.number;
}
```

# [L-01] `getTotalAmounts` 및 `getBasePosition`이 DOS될 수 있음

`getTotalAmounts` 및 `getBasePosition`은 `external view` 함수이며 외부적으로 의존할 수 있습니다.

```solidity
		require(_checkHysteresis(), NEXT_BLOCK);
```

이들은 모두 `_checkHysteresis`를 호출하는데, 이는 동일한 블록 내에서 해당 유니스왑 풀의 토큰 스왑을 허용하지 않습니다. 따라서 공격자가 사전에 해당 유니스왑 풀을 호출하여 임의의 수의 토큰을 스왑하는 한 `getTotalAmounts` 및 `getBasePosition` 함수에 의존하는 모든 로직이 DoS될 수 있습니다.

오라클과 상당한 가격 편차가 있을 때만 확인하거나 `getTotalAmountsUnSafe` 및 `getBasePositionUnSafe`를 제공하는 것이 좋습니다.

# [L-02] 함수 내 보류 중인 수입 없음

`getTotalAmounts` 및 `getBasePosition`은 `external view` 함수이며 외부적으로 의존할 수 있습니다. 이들 중 어느 것도 유니스왑 풀의 보류 중인 수입을 계산하지 않으므로 이 두 함수에 의존하는 외부 로직에서 잘못된 계산이 발생할 수 있습니다.

`getTotalAmountsFeeAccumulated` 및 `getBasePositionFeeAccumulated`라는 두 가지 non-view 함수를 제공하는 것이 좋습니다. 이 함수들은 해당 값을 반환하기 전에 먼저 `IUniswapV3Pool(pool).burn(baseLower, baseUpper, 0)`을 호출해야 합니다.

# [L-03] 관리자가 `initializePool`에서 토큰을 잃을 수 있음

`initializePool` 함수는 `UniV3TokenizedLp` 계약을 배포하고 초기화하는 데 사용됩니다. 또한 `initializePool` 함수는 일부 초기 토큰을 유니스왑 풀에 예치합니다.

```solidity
			pool.mint(address(this), baseLower_, baseUpper_, fullRangeliquidity, abi.encode(address(this)));
```

그러나 `pool.mint`는 두 토큰을 정확한 비율로 소비하지 않을 수 있으므로 계약에 일부 토큰이 남을 수 있습니다. RDNT 토큰의 경우 계약에 영구적으로 갇히게 됩니다. WETH 토큰의 경우 사용자가 `swapWethToRdnt`를 호출하여 훔칠 수 있습니다.

# [L-04] 사용되지 않는 승인

`UniswapV3PoolHelper.initializePool()`은 초기 LP 토큰을 민팅하기 위한 승인을 제공합니다.

```solidity
	IERC20(rdntAddr).forceApprove(address(pool), initRdntBal);
	IERC20(weth9Addr).forceApprove(address(pool), initWeth9Bal);
```

그러나 실제로는 `uniswapV3MintCallback()`을 통해 토큰이 직접 전송됩니다.

```solidity
	function uniswapV3MintCallback(uint256 amount0, uint256 amount1, bytes calldata data) external {
		if (msg.sender != address(pool)) revert SwapCallbackUnauthorized();

		address payer = abi.decode(data, (address));

		if (payer == address(this)) {
			if (amount0 > 0) IERC20(token0).safeTransfer(msg.sender, amount0);
			if (amount1 > 0) IERC20(token1).safeTransfer(msg.sender, amount1);
		} else {
			if (amount0 > 0) IERC20(token0).safeTransferFrom(payer, msg.sender, amount0);
			if (amount1 > 0) IERC20(token1).safeTransferFrom(payer, msg.sender, amount1);
		}
	}
```

결과적으로 이 초기 승인은 필요하지 않았으며 제거할 수 있습니다.

# [L-05] 오라클 소수점 정규화 누락

`fetchOracle` 함수는 가격 피드에 사용되는 오라클이 동일한 소수점을 갖는다고 가정하지만 항상 그런 것은 아닙니다. 정확한 가격 계산을 보장하기 위해 함수는 token0 및 token1 소수점을 처리하는 방식과 유사하게 오라클의 소수점을 정규화해야 합니다.

```solidity
function fetchOracle(
    address tokenIn_,
    address tokenOut_,
    uint256 amountIn_
) public view returns (uint256 amountOut) {
    uint256 valueIn = tokenIn_ == token0
        ? _getUsdValue(tokenIn_, amountIn_, usdOracle0Ref)
        : _getUsdValue(tokenIn_, amountIn_, usdOracle1Ref);

    amountOut = tokenOut_ == token0
        ? _getTokenFromUsdValue(tokenOut_, usdOracle0Ref, valueIn)
        : _getTokenFromUsdValue(tokenOut_, usdOracle1Ref, valueIn);
}
```

```solidity
function _getUsdValue(
    address token_,
    uint256 amount_,
    IChainlinkAdapter usdOracle_
) internal view returns (uint256) {
    return (uint256(usdOracle_.latestAnswer()) * amount_) / (10 ** uint256(ERC20(token_).decimals()));
}
```

```solidity
function _getTokenFromUsdValue(
    address token_,
    IChainlinkAdapter usdOracle_,
    uint256 value_
) internal view returns (uint256) {
    return (value_ * 10 ** ERC20(token_.decimals()) / uint256(usdOracle_.latestAnswer());
}
```

오라클의 다양한 소수점을 처리하려면 `fetchOracle` 함수에 소수점 정규화를 도입하십시오.

# [L-06] LP 가격 책정이 조작에 노출됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

계약은 준비금의 가치를 기반으로 LP 토큰의 가격을 계산하므로 조작 공격에 취약할 수 있습니다. 이 방법은 공격자가 준비금을 조작하고 잘못된 LP 토큰 가격을 도출하는 데 악용될 수 있습니다. 보다 조작에 강한 접근 방식을 제공하려면 오라클 가격을 기반으로 공정 준비금을 계산하고 그로부터 LP 토큰 가격을 도출하는 것을 고려하십시오.

```solidity
function getLpPrice(uint256) public view returns (uint256 priceInWeth9) {
    (uint256 rdntReserve, uint256 wethReserve, uint256 lpSupply) = getReserves();
    uint256 weth9ForRdnt = tokenizedLpToken.fetchOracle(rdntAddr, weth9Addr, rdntReserve);
    uint256 allReservesInWeth = wethReserve + weth9ForRdnt;
    priceInWeth9 = (allReservesInWeth * 1e8) / lpSupply;
}
```

### 권장 사항

준비금 조작 위험을 완화하려면 오라클 가격을 기반으로 공정 준비금을 계산하고 이를 사용하여 LP 토큰 가격을 결정하십시오. [Alpha Venture DAO 블로그](https://blog.alphaventuredao.io/fair-lp-token-pricing/)에 자세히 설명된 방법을 따르십시오.
