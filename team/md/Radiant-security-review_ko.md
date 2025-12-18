# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**v2-core** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Radiant 정보

Radiant는 사용자가 모든 주요 체인의 주요 자산을 예치하고 여러 체인에서 다양한 지원 자산을 빌릴 수 있는 옴니체인 머니 마켓입니다. Radiant v2는 현재 ERC-20 RDNT 토큰을 LayerZero OFT 형식으로 마이그레이션합니다. 또한 LockZap 및 Compounder 계약은 이제 스왑에 UniswapV2 대신 UniswapV3를 활용합니다.

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

_검토 커밋 해시_ - [e7271904c8e951bc73c1235872b0ec0ab5bb27c0](https://github.com/radiant-capital/v2-core/pull/846/commits/e7271904c8e951bc73c1235872b0ec0ab5bb27c0)

_수정 검토 커밋 해시_ - [11447f34f417ad0c7b2401d26b3725ad2f147c38](https://github.com/radiant-capital/v2-core/pull/856/commits/11447f34f417ad0c7b2401d26b3725ad2f147c38)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Compounder`
- `LockZap`

# 발견 사항

# [C-01] WETH -> RDNT 스왑에 대해 전달된 잘못된 토큰 순서

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`BalancerPoolHelper.swapWethToRdnt()`는 WETH를 RDNT로 스왑하기 위한 것입니다. 그러나 입력 및 출력 토큰이 반대로 되어 있습니다.

```solidity
_swap(RDNT_ADDRESS, REAL_WETH_ADDR, _wethAmount, _minAmountOut, WETH_RDNT_POOL_ID, msg.sender);
```

결과적으로 대신 RDNT -> WETH 스왑을 시도하게 됩니다.

## 권장 사항

```diff
- _swap(RDNT_ADDRESS, REAL_WETH_ADDR, _wethAmount, _minAmountOut, WETH_RDNT_POOL_ID, msg.sender);
+ _swap(wethAddr, RDNT_ADDRESS, _wethAmount, _minAmountOut, WETH_RDNT_POOL_ID, msg.sender);
```

# [C-02] `rdntOut`이 항상 0이므로 `_wethToRdnt()`가 확실히 되돌려짐

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`BalancerPoolHelper._swap()`은 반환 문이나 반환 매개변수 할당이 없기 때문에 항상 `0`을 반환합니다. 따라서 `rdntOut = poolHelper_.swapWethToRdnt(_wethIn, 0);`는 항상 0이 되어 슬리피지 확인이 실패합니다.

## 권장 사항

```diff
- IVault(vaultAddr).swap(singleSwap, funds, _minAmountOut, block.timestamp);
+ return IVault(vaultAddr).swap(singleSwap, funds, _minAmountOut, block.timestamp);
```

# [H-01] 잘못된 `quoteWETH()` 구현 (범위 외)

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`UniswapPoolHelper.quoteWETH()`는 BSC의 WBNB-RDNT 풀에 LP를 공급하는 데 필요한 WBNB(WETH로 표시됨)를 계산하는 데 사용됩니다. 구현에는 2가지 문제가 있습니다.

`neededWeth`가 잘못된 준비금에서 파생됩니다.

```solidity
uint256 weth = lpToken.token0() != address(rdntAddr) ? reserve0 : reserve1;
uint256 rdnt = lpToken.token0() == address(rdntAddr) ? reserve0 : reserve1;
uint256 lpTokenSupply = lpToken.totalSupply();

uint256 neededWeth = (rdnt * lpAmount) / lpTokenSupply;
```

`neededWeth`는 `rdnt` 대신 `weth`를 사용해야 합니다.

필요한 금액은 스왑 후가 아니라 스왑 전 풀 금액에서 파생됩니다.

단면 유동성을 수행하는 것은 금액의 절반을 다른 토큰으로 스왑한 다음 남은 절반과 스왑된 금액으로 유동성을 추가하는 것과 유사합니다.

구현은 필요한 금액을 계산하기 위해 스왑 전의 풀 준비금을 사용하지만, `weth`가 증가하고 `rdnt`가 감소하는 스왑으로 변경된 준비금을 사용해야 합니다.

## 권장 사항

제안된 구현은 다음과 같습니다.

```solidity
uint256 neededRdnt = (lpAmount * rdnt) / (lpAmount + lpTokenSupply);
uint256 neededRdntInWeth = router.getAmountIn(neededRdnt, weth, rdnt);
uint256 neededWeth = (weth - neededRdntInWeth) * lpAmount / lpTokenSupply;
return neededWeth + neededRdntInWeth;
```

# [L-01] 사용되지 않는 코드

`LockZap.sol`

```solidity
	/// @notice Swap uniswap v2 routes from token0 to token1
	mapping(address => mapping(address => address[])) internal _uniV2Route;
```

`BalancerPoolHelper.sol`

```solidity
	/**
	 * @notice Swaps WETH to RDNT
	 * @param _wethAmount the amount of RDNT to sell
	 * @param _minAmountOut the minimum RDNT amount to accept without reverting
	 */
	function swapWethToRdnt(uint256 _wethAmount, uint256 _minAmountOut) external returns (uint256) {
		if (_wethAmount == 0) revert ZeroAmount();

>>		uint256 usdcBalanceAfter = IERC20(USDC_ADDRESS).balanceOf(address(this));
		return _swap(RDNT_ADDRESS, REAL_WETH_ADDR, _wethAmount, _minAmountOut, WETH_RDNT_POOL_ID, msg.sender);
	}
```

# [L-02] 두 개의 WETH 주소가 동일한 계약에 저장됨

`BalancerPoolHelper.sol`은 두 개의 WETH 주소 값을 저장하며, 하나는 하드코딩되어 있습니다.

```solidity
address public constant REAL_WETH_ADDR = address(0x82aF49447D8a07e3bd95BD0d56f35241523fBab1);
```

다른 하나는 초기화 시 지정됩니다.

```solidity
	function initialize(
		address _inTokenAddr,
		address _outTokenAddr,
		address _wethAddr,
		address _vault,
		IWeightedPoolFactory _poolFactory
	) external initializer {
                ...
		__Ownable_init();
		inTokenAddr = _inTokenAddr;
		outTokenAddr = _outTokenAddr;
>>		wethAddr = _wethAddr;
		vaultAddr = _vault;
		poolFactory = _poolFactory;
	}
```

둘 다 계약에서 사용됩니다.

```solidity
	function swapWethToRdnt(uint256 _wethAmount, uint256 _minAmountOut) external returns (uint256) {
		if (_wethAmount == 0) revert ZeroAmount();

		uint256 usdcBalanceAfter = IERC20(USDC_ADDRESS).balanceOf(address(this));
>>		return _swap(RDNT_ADDRESS, REAL_WETH_ADDR, _wethAmount, _minAmountOut, WETH_RDNT_POOL_ID, msg.sender);
	}

	function quoteWethToRdnt(uint256 _wethAmount) external view returns (uint256) {
		if (_wethAmount == 0) revert ZeroAmount();
>>		return _quote(wethAddr, RDNT_ADDRESS, _wethAmount, WETH_RDNT_POOL_ID);
	}
```

하나의 WETH 주소 변수/상수만 남겨두는 것을 고려하십시오.

# [L-03] `LockZap.quoteFromToken()`이 견적에 잘못된 API를 사용함

사용자는 유동성을 추가하기 위해 RDNT 토큰과 짝을 이룰 다른 `token`을 지정할 수 있습니다. `quoteFromToken(address _token, uint256 _amount)`는 이 페어링에 필요한 `token` 금액을 추정하는 데 도움을 줍니다.

```solidity
if (_token != weth_) {
  uint256 wethAmount = poolHelper.quoteFromToken(_amount);
  return _quoteUniswap(weth_, _token, wethAmount);
}

function _quoteUniswap(address _tokenIn, address _tokenOut, uint256 _amountIn) internal view returns (uint256) {
  bytes memory route = _uniV3Route[_tokenIn][_tokenOut];
  uint256 amountOut = uniV3Quoter.quoteExactInput(route, _amountIn);
  return amountOut;
}
```

인용된 금액은 `wethAmount`를 `_token`으로 스왑하는 것에서 파생되지만, 실제로는 그 반대(`_token`을 `WETH`로 zap한 다음 `RDNT _amount`와 페어링)로 수행되므로 실제 필요한 금액보다 적은 추정치가 나옵니다.

`quoteExactOutput()`으로 전환하는 것을 추천합니다.

```diff
- uniV3Quoter.quoteExactInput(route, _amountIn);
+ uniV3Quoter.quoteExactOutput(route, _amountIn); // consider renaming to _amountOut
```

예상 경로는 역순(= 정확한 입력 스왑)으로 인코딩되므로 `route`는 변경되지 않습니다.

# [L-04] `UniswapPoolHelper`가 `IUniswapPoolHelper`를 상속하지 않음

`UniswapPoolHelper`는 기본 `IPoolHelper`를 준수할 것으로 예상되지만 상속하지 않습니다. 이를 사용하는 다른 계약과의 호환성을 보장하기 위해 상속하는 것을 추천합니다.

# [L-05] 경로를 더 엄격하게 확인할 수 있음

```solidity
if (_route.length < MIN_UNIV3_ROUTE_LENGTH) revert WrongRoute(_tokenIn, _tokenOut);
```

새 경로를 설정할 때 수행되는 유일한 확인은 `>= MIN_UNIV3_ROUTE_LENGTH`여야 한다는 것입니다. 그러나 적절하게 인코딩되었는지 확인하는 검사는 없습니다.

경로를 검증하는 2가지 가능한 확인이 있습니다.

1. `(_route.length - 20) % 23) == 0` <=> `(_route.length - ADDR_SIZE) % NEXT_OFFSET) == 0` 확인
2. `uniV3Quoter.quoteExactInput(_route, someAmountIn) != 0;` 호출. 그러나 이는 `uniV3Quoter`가 적절하게 설정되고 사용될 풀에 충분한 유동성이 있는지에 달려 있습니다.
