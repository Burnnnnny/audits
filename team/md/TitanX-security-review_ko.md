# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**dragon-software-devs/TitanX-Farms** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# TitanX Farm 소개

TitanX Farm 프로토콜은 사용자가 UniswapV3에 유동성을 제공하고 TINC 토큰으로 보상을 받을 수 있게 하며, TINC의 가치를 지원하기 위해 매수 후 소각(buy-and-burn) 메커니즘을 구현합니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 비교적 가능성이 높습니다.

- 낮음 (Low) - 너무 많거나 비현실적인 가정을 필요로 하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준별 요구 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (이미 배포되지 않은 경우 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [181af1ffd7a8bcfe57c0bdeadccabf1b709619a9](https://github.com/dragon-software-devs/TitanX-Farms/tree/181af1ffd7a8bcfe57c0bdeadccabf1b709619a9)

_수정 검토 커밋 해시_ - [e7d6476452e2aa4b937f292da906c65e427e0962](https://github.com/dragon-software-devs/TitanX-Farms/tree/e7d6476452e2aa4b937f292da906c65e427e0962)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `LiquidityAmounts`
- `Oracle`
- `PathDecoder`
- `PoolAddress`
- `PositionKey`
- `PositionValue`
- `TickMath`
- `Constants`
- `Farms`
- `InputTokens`
- `FarmKeeper`
- `TINC`
- `UniversalBuyAndBurn`

# 발견 사항

# [H-01] 내림(rounding down)으로 인한 수수료 손실

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`FarmKeeper` 계약은 유동성 지분당 수수료를 누적할 때 `1e12`의 `SCALE_FACTOR`를 사용합니다. 소수 자릿수가 낮은 토큰(예: 소수 자릿수가 6인 USDC)과 큰 유동성 금액의 경우, 이는 정밀도 손실과 0으로 내림되는 결과를 초래하여 사용자의 수수료 영구 손실로 이어질 수 있습니다.

USDC와 같이 소수 자릿수가 6인 토큰을 고려해 보겠습니다.

이 문제는 다음과 같은 이유로 발생합니다:

- amount0/amount1은 1단위만큼 낮을 수 있습니다(예: USDC의 경우 1e6).
- 유동성은 uint128이므로 최대 ~3.4e38까지 가능합니다.
- SCALE_FACTOR는 겨우 1e12입니다.

즉, 계산 결과는 `1e6 * 1e12 / 1e38 = 1e-20`이 될 수 있으며, 정수 나눗셈으로 인해 0으로 내림됩니다.

토큰이 6자리 소수점인 경우, 유동성 금액이 1e18(e38일 필요 없음)보다 크면 토큰에 대한 지분당 증분 수수료가 0으로 내림됨을 알 수 있습니다.

`updateFarm`과 결합하면 누구나 수수료를 징수하기 위해 호출할 수 있습니다. 악의적인 행위자는 금액이 적을 때 수수료 징수를 강제하기 위해 `updateFarm`을 자주 호출하여 이를 악용할 수 있습니다.

```solidity
  function _collectFees(Farm storage farm) private {

    // Handle token0
    if (isInputToken0) {
    ...
    } else {
      farm.accFeePerShareForToken0 += Math.mulDiv(amount0, Constants.SCALE_FACTOR, liquidity);
    }

    // Handle token1
    if (isInputToken1) {
    ...
    } else {
      farm.accFeePerShareForToken1 += Math.mulDiv(amount1, Constants.SCALE_FACTOR, liquidity);
    }
  }
```

그 영향은 유동성이 높을 때 징수된 수수료가 영구적으로 손실되어 사용자가 청구할 수 없게 된다는 것입니다.

## 권장 사항

`SCALE_FACTOR` 값을 18로 늘려 계산이 0으로 내림되는 것을 방지하십시오.

# [M-01] TWAP 가격 조작

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

FarmKeeper의 `_getTwaPrice` 함수와 UniversalBuyAndBurn의 `getQuote` 함수는 관측 카디널리티(observation cardinality)가 낮은 Uniswap V3 풀에서 가격 조작에 취약합니다. 이 취약점은 부정확한 가격 계산으로 이어져 불공정한 토큰 스왑이나 유동성 제공을 초래할 수 있습니다.

유동성 제공 및 스왑 시 슬리피지를 방지하기 위해 twap 가격을 가져올 때, 요청된 시간 창(time window)이 사용 가능한 가격 기록보다 크면 `_getTwaPrice` 및 `getQuote` 함수는 가장 오래된 관측으로 대체(fallback)합니다.

이는 풀 카디널리티를 고려하지 않기 때문에 문제입니다. 카디널리티가 1로 초기화된 새로 생성된 풀에서는, 가장 오래된 관측이 현재 `block.timestamp`로 조작되어 TWAP 가격을 현재(조작될 수 있는) 가격으로 설정할 수 있습니다. Uniswap v3 풀에서는 오라클 관측이 블록의 첫 번째 스왑이나 유동성 제공에 의해 기록되기 때문입니다. UniversalBuyAndBurn 계약이 스왑을 수행하기 전에, 악의적인 행위자가 카디널리티가 1이고 가장 오래된 관측이 0이기 때문에 가격을 조작하기 위해 프런트런(front-run)하고 스왑한다고 가정해 봅시다.

또 다른 결과는 유동성이 각 블록마다 변경되는 경우 `oldestObservation`이 `cardianility * 12 seconds (블록당 시간)`와 같을 수 있다는 것입니다. 이는 `oldestObservation < secondsAgo`인 경우 TWAP 기간이 의도한 것보다 짧을 수 있음을 의미하며, 잠재적으로 슬리피지 위험을 증가시킵니다.

```solidity
  function _getTwaPrice(address id, uint32 priceTwa) private view returns (uint160 slotPrice, uint160 twaPrice) {
    ...
    // Default TWA price to slot
    twaPrice = slotPrice;

    uint32 secondsAgo = uint32(priceTwa * 60);
    uint32 oldestObservation = 0;

    // Load oldest observation if cardinality greater than zero
    oldestObservation = OracleLibrary.getOldestObservationSecondsAgo(id);

    // Limit to oldest observation (fallback)
    if (oldestObservation < secondsAgo) {
      secondsAgo = oldestObservation; // @audit fallback to oldest observation
    }
    ...
  }
```

```solidity
  function getQuote(
    address inputTokenAddress,
    address outputTokenAddress,
    uint24 fee,
    uint256 twap,
    uint256 inputTokenAmount
  ) public view returns (uint256 quote, uint32 secondsAgo) {
    ...
    secondsAgo = uint32(twap * 60);
    uint32 oldestObservation = 0;

    // Load oldest observation if cardinality greather than zero
    oldestObservation = OracleLibrary.getOldestObservationSecondsAgo(poolAddress);

    // Limit to oldest observation
    if (oldestObservation < secondsAgo) {
      secondsAgo = oldestObservation; // @audit fallback to oldest observation
    }

    // If TWAP is enabled and price history exists, consult oracle
    if (secondsAgo > 0) {
      // Consult the Oracle Library for TWAP
      (int24 arithmeticMeanTick, ) = OracleLibrary.consult(poolAddress, secondsAgo);

      // Convert tick to sqrtPriceX96
      sqrtPriceX96 = TickMath.getSqrtRatioAtTick(arithmeticMeanTick);
    }
    ...
  }
```

## 권장 사항

다음 중 하나를 권장합니다:

- `oldestObservation < secondsAgo`인 경우 되돌려(revert) 의도된 전체 TWAP 기간이 사용되도록 보장합니다.
- 풀이 파밍 또는 매수 후 소각 작업에 적합하려면 최소 카디널리티(예: 10)를 요구합니다.
  참조: https://docs-v1.euler.finance/euler-protocol/eulers-default-parameters#uniswap-observation-cardinality

# [L-01] 잠재적인 가스 고갈 (Potential out of gas)

`massUpdateFarms` 함수는 모든 농장을 반복하고 업데이트합니다. 이는 농장의 수가 매우 많아지면 가스 부족 오류로 인해 되돌려질(revert) 잠재적 위험을 제시합니다. `collectFees`가 false로 설정되었을 때 함수가 되돌려지면, `massUpdateFarms`에 의존하는 `enableFarm` 및 `setAllocation`과 같은 주요 기능이 차단될 수 있습니다.

`_farms` 배열에 농장을 추가하는 것은 관리자로 제한되며, `collectFees`가 `false`로 설정된 경우 각 반복 비용은 약 1300 가스에 불과합니다. 따라서 가스 부족 오류가 발생할 가능성은 매우 낮습니다.

그러나 관리자는 이 잠재적 문제를 염두에 두고 더 이상 필요하지 않은 비활성 농장을 제거하는 등의 해결책 구현을 고려해야 합니다.

```solidity
  function massUpdateFarms(bool collectFees) public nonReentrant {
    uint256 length = _farms.length();

    // Iterate all farms and update them
    for (uint256 idx = 0; idx < length; idx++) {
      Farm storage farm = _farms.at(idx);
      _updateFarm(farm, collectFees); // @audit Potential out of gas if the number of farms is very high
    }
  }

```

# [L-02] 잠재적인 0으로 나누기 (Potential division by zero)

`enableFarm` 함수에서 `params.allocPoints`는 `MAX_ALLOCATION_POINTS`보다 작은지 검증되지만, 새 농장을 추가한 후 `totalAllocPoints`가 0보다 크게 유지되는지 확인하는 검사는 없습니다. 이론적으로 `totalAllocPoints`가 어떻게든 0이 되고 농장에 0이 아닌 유동성이 있는 경우, 보상을 계산할 때 `_updateFarm` 함수에서 0으로 나누기 오류가 발생할 수 있습니다.

```solidity
  function enableFarm(AddFarmParams calldata params) external restricted {
    ...
    _validateAllocPoints(params.allocPoints); // @audit only verify that allocPoints is not greater than MAX_ALLOCATION_POINTS, not totalAllocPoints
    ...
  }
```

```solidity
  function _updateFarm(Farm storage farm, bool collectFees) private {
    ...
    uint256 incentiveTokenReward = Math.mulDiv( // @audit Potential division by zero if totalAllocPoints is 0
      timeMultiplier * Constants.INCENTIVE_TOKEN_PER_SECOND,
      farm.allocPoints,
      totalAllocPoints
    );
    ...
  }
```

`enableFarm` 함수에서 새 농장을 추가한 후 `totalAllocPoints`가 0보다 큰지 확인하는 검사를 추가하는 것이 좋습니다.

```diff
function enableFarm(AddFarmParams calldata params) external restricted {
  // ...
  _validateAllocPoints(params.allocPoints);
+ require(totalAllocPoints + params.allocPoints > 0, "Total allocation points must be greater than zero");
  // ...
  totalAllocPoints += params.allocPoints;
}
```

# [L-03] 특정 농장에 대한 자세한 정보가 최신이 아닐 수 있음

`farmView` 함수는 마지막 업데이트 이후의 기간에 따라 달라지는 저장 변수를 포함하고 보류 중인 풀 수수료를 포함하지 않기 때문에 오래된 정보를 반환할 수 있습니다. `userView` 함수와 유사하게 실제 정보를 반환하는 보기 기능을 구현하는 것을 고려해 보십시오.

```solidity
  function farmView(address id) public view returns (FarmView memory) {
    if (!_farms.contains(id)) revert InvalidFarmId();

    Farm storage farm = _farms.get(id);

    (uint160 slotPrice, ) = _getTwaPrice(farm.id, 0);
    (uint256 balanceToken0, uint256 balanceToken1) = LiquidityAmounts.getAmountsForLiquidity(
      slotPrice,
      TickMath.getSqrtRatioAtTick(Constants.MIN_TICK),
      TickMath.getSqrtRatioAtTick(Constants.MAX_TICK),
      farm.lp.liquidity
    );

    return
      FarmView({
        id: farm.id,
        poolKey: farm.poolKey,
        lp: farm.lp,
        allocPoints: farm.allocPoints,
        lastRewardTime: farm.lastRewardTime,
>>      accIncentiveTokenPerShare: farm.accIncentiveTokenPerShare,
>>      accFeePerShareForToken0: farm.accFeePerShareForToken0,
>>      accFeePerShareForToken1: farm.accFeePerShareForToken1,
>>      protocolFee: farm.protocolFee,
        priceTwa: farm.priceTwa,
        slippage: farm.slippage,
        balanceToken0: balanceToken0,
        balanceToken1: balanceToken1
      });
  }
```

# [L-04] 파밍이 시작되지 않았더라도 초기 마지막 보상 시간이 변경될 수 있음

`farm.lastRewardTime` 변수의 초기값은 `startTime`으로 설정된 다음, `_updateFarm` 함수에 의해 `block.timestamp < startTime`인 경우에도 `block.timestamp`로 변경될 수 있습니다. `block.timestamp`가 `startTime`보다 작은 경우 `farm.lastRewardTime`을 `block.timestamp` 값으로 변경하지 않는 것을 고려해 보십시오.

```solidity
  function enableFarm(AddFarmParams calldata params) external restricted {
    PoolAddress.PoolKey memory poolKey = PoolAddress.getPoolKey(params.tokenA, params.tokenB, params.fee);

    // Compute pool address to check for duplicates
    address id = PoolAddress.computeAddress(Constants.FACTORY, poolKey);

    // Check for duplicates
    if (_farms.contains(id)) revert DuplicatedFarm();

    _validateAllocPoints(params.allocPoints);
    _validatePriceTwa(params.priceTwa);
    _validateSlippage(params.slippage);
    _validateProtocolFee(params.protocolFee);

    // Update all farms but do not collect fees as only
    // the incentive token allocations are affected by enabling a new farm
>>  massUpdateFarms(false);

    // Append new farm
    _farms.add(
      Farm({
        id: id,
        poolKey: poolKey,
        lp: LP({tokenId: 0, liquidity: 0}),
        allocPoints: params.allocPoints,
>>      lastRewardTime: block.timestamp > startTime ? block.timestamp : startTime,
        accIncentiveTokenPerShare: 0,
        accFeePerShareForToken0: 0,
        accFeePerShareForToken1: 0,
        protocolFee: params.protocolFee,
        priceTwa: params.priceTwa,
        slippage: params.slippage
      })
    );

    totalAllocPoints += params.allocPoints;
    emit FarmEnabled(id, params);
  }
<...>
  function massUpdateFarms(bool collectFees) public nonReentrant {
    uint256 length = _farms.length();

    // Iterate all farms and update them
    for (uint256 idx = 0; idx < length; idx++) {
      Farm storage farm = _farms.at(idx);
>>    _updateFarm(farm, collectFees);
    }
  }
<...>
  function _updateFarm(Farm storage farm, bool collectFees) private {
    // Total liquidity
    uint256 liquidity = farm.lp.liquidity;
    if (liquidity == 0) {
>>    farm.lastRewardTime = block.timestamp;
      return;
    }
```

# [L-05] Permit 서명은 마감일 전에 취소할 수 없음

TINC permit 서명은 서명자에게 EIP-712 서명을 생성할 수 있는 옵션을 제공합니다. 이 서명 후 서명자가 취소하고 싶을 수도 있지만 취소할 수 없습니다. 이는 컨트랙트가 OpenZeppelin의 `ERC20Permit.sol`을 기반으로 하기 때문인데, 여기에는 nonce를 증가시키는 함수가 존재하지 않으며 `Nonces` 내의 [\_useNonce](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v5.0.2/contracts/utils/Nonces.sol#L28-L35) 함수는 내부(internal)로 표시되어 있습니다.

서명자가 자신의 nonce를 소비하여 서명을 취소할 수 있도록 직접 사용할 수 있는 공개 함수를 도입하는 것을 고려해 보십시오.

```solidity
    function useNonce() external returns (uint256) {
        return _useNonce(msg.sender);
    }
```

# [L-06] 값이 0인지 확인하지 않음

`_collectFees`가 트리거되고 수집된 수수료가 0이고 토큰이 `buyAndBurn` 내부의 입력 토큰인 경우에도, 여전히 0 값을 제공하여 `_safeTransferToken`을 트리거합니다.

```solidity
  function _collectFees(Farm storage farm) private {
    // Cache State Variables
    uint256 liquidity = farm.lp.liquidity;
    INonfungiblePositionManager manager = INonfungiblePositionManager(Constants.NON_FUNGIBLE_POSITION_MANAGER);

    // Collect the maximum amount possible of both tokens
    INonfungiblePositionManager.CollectParams memory params = INonfungiblePositionManager.CollectParams(
      farm.lp.tokenId,
      address(this),
      type(uint128).max,
      type(uint128).max
    );
    (uint256 amount0, uint256 amount1) = manager.collect(params);

    // Identify tokens which are accepted as input for buy and burn
    bool isInputToken0 = buyAndBurn.isInputToken(farm.poolKey.token0);
    bool isInputToken1 = buyAndBurn.isInputToken(farm.poolKey.token1);

    // Handle token0
    if (isInputToken0) {
      uint256 protocolFee = 0;

      if (farm.protocolFee > 0) {
        protocolFee = Math.mulDiv(amount0, farm.protocolFee, Constants.BASIS);
        protocolFees[farm.poolKey.token0] += protocolFee;
      }

      // Send core tokens to the buy and burn contract
>>>   _safeTransferToken(farm.poolKey.token0, address(buyAndBurn), amount0 - protocolFee);
    } else {
      farm.accFeePerShareForToken0 += Math.mulDiv(amount0, Constants.SCALE_FACTOR, liquidity);
    }

    // Handle token1
    if (isInputToken1) {
      uint256 protocolFee = 0;

      if (farm.protocolFee > 0) {
        protocolFee = Math.mulDiv(amount1, farm.protocolFee, Constants.BASIS);
        protocolFees[farm.poolKey.token1] += protocolFee;
      }

      // Send core tokens to the buy and burn contract
>>>   _safeTransferToken(farm.poolKey.token1, address(buyAndBurn), amount1 - protocolFee);
    } else {
      farm.accFeePerShareForToken1 += Math.mulDiv(amount1, Constants.SCALE_FACTOR, liquidity);
    }
  }
```

토큰이 0 전송 값에서 되돌려지는 경우, 작업이 되돌려질 수 있으며 `_collectFees`에 의존하는 작업이 항상 되돌려질 수 있습니다.

수집된 금액이 0인 경우 수수료 계산 및 전송을 건너뛰는 것을 고려해 보십시오.

# [L-07] 불필요한 TWAP 가격 쿼리

`getAmountsForLiquidity`가 호출되면 `_getDesiredAmountsForLiquidity`를 호출하여 `amount0`과 `amount1`을 가져옵니다.

```solidity
  function getAmountsForLiquidity(
    address id,
    uint128 liquidity
  ) external view returns (address token0, address token1, uint256 amount0, uint256 amount1) {
    if (!_farms.contains(id)) revert InvalidFarmId();

    Farm storage farm = _farms.get(id);
>>> (amount0, amount1, , ) = _getDesiredAmountsForLiquidity(farm, liquidity);

    token0 = farm.poolKey.token0;
    token1 = farm.poolKey.token1;
  }
```

`_getDesiredAmountsForLiquidity` 내부에서는 slot0 가격과 TWAP 가격을 검색한 다음, 해당 가격을 기반으로 amount0/amount1을 계산합니다. 그러나 `getAmountsForLiquidity`는 slot0 가격을 사용하여 계산된 금액만 필요로 합니다.

```solidity
  function _getDesiredAmountsForLiquidity(
    Farm storage farm,
    uint128 liquidity
  ) private view returns (uint256 desiredAmount0, uint256 desiredAmount1, uint256 minAmount0, uint256 minAmount1) {
    (uint160 slotPrice, uint160 twaPrice) = _getTwaPrice(farm.id, farm.priceTwa);
    // Calculate desired amounts based on current slot price
    (desiredAmount0, desiredAmount1) = LiquidityAmounts.getAmountsForLiquidity(
      slotPrice,
      TickMath.getSqrtRatioAtTick(Constants.MIN_TICK),
      TickMath.getSqrtRatioAtTick(Constants.MAX_TICK),
      liquidity
    );

    // Calculate minimal amounts based on TWA price for slippage protection
    (minAmount0, minAmount1) = LiquidityAmounts.getAmountsForLiquidity(
      twaPrice,
      TickMath.getSqrtRatioAtTick(Constants.MIN_TICK),
      TickMath.getSqrtRatioAtTick(Constants.MAX_TICK),
      liquidity
    );

    // Apply slippage
    minAmount0 = (minAmount0 * (Constants.BASIS - farm.slippage)) / Constants.BASIS;
    minAmount1 = (minAmount1 * (Constants.BASIS - farm.slippage)) / Constants.BASIS;
  }
```

`getAmountsForLiquidity`를 다음과 같이 수정하여 반드시 TWAP 가격을 쿼리하지 않도록 하는 것을 고려해 보십시오:

```diff
  function getAmountsForLiquidity(
    address id,
    uint128 liquidity
  ) external view returns (address token0, address token1, uint256 amount0, uint256 amount1) {
    if (!_farms.contains(id)) revert InvalidFarmId();

-    Farm storage farm = _farms.get(id);
-    (amount0, amount1, , ) = _getDesiredAmountsForLiquidity(farm, liquidity);
+    (uint160 slotPrice, ) = _getTwaPrice(id, 0);
+    (amount0, amount1) = LiquidityAmounts.getAmountsForLiquidity(
+      slotPrice,
+      TickMath.getSqrtRatioAtTick(Constants.MIN_TICK),
+      TickMath.getSqrtRatioAtTick(Constants.MAX_TICK),
+      liquidity
+    );

    token0 = farm.poolKey.token0;
    token1 = farm.poolKey.token1;
  }
```

유사한 시나리오가 `getLiquidityForAmount` 내부에서도 발생합니다. `getLiquidityForAmount`를 다음과 같이 업데이트하십시오:

```diff
  function getLiquidityForAmount(
    address id,
    address token,
    uint256 amount
  ) external view returns (address token0, address token1, uint128 liquidity, uint256 amount0, uint256 amount1) {
    if (!_farms.contains(id)) revert InvalidFarmId();

    Farm storage farm = _farms.get(id);
    token0 = farm.poolKey.token0;
    token1 = farm.poolKey.token1;

    // Get prices
-    (uint160 slotPrice, ) = _getTwaPrice(farm.id, farm.priceTwa);
+    (uint160 slotPrice, ) = _getTwaPrice(farm.id, 0);
    uint160 sqrtRatioAX96 = TickMath.getSqrtRatioAtTick(Constants.MIN_TICK);
    uint160 sqrtRatioBX96 = TickMath.getSqrtRatioAtTick(Constants.MAX_TICK);

    if (token == token0) {
      liquidity = LiquidityAmounts.getLiquidityForAmount0(slotPrice, sqrtRatioBX96, amount);
    } else if (token == token1) {
      liquidity = LiquidityAmounts.getLiquidityForAmount1(sqrtRatioAX96, slotPrice, amount);
    } else {
      revert InvalidTokenId();
    }

    // Calculate amounts based on the slot price
    (amount0, amount1) = LiquidityAmounts.getAmountsForLiquidity(
      slotPrice,
      TickMath.getSqrtRatioAtTick(Constants.MIN_TICK),
      TickMath.getSqrtRatioAtTick(Constants.MAX_TICK),
      liquidity
    );
  }
```

# [L-08] 수수료가 잘못 처리될 수 있음

`UniversalBuyAndBurn` 계약의 `setDisabled` 함수는 입력 토큰이 매수 후 소각 메커니즘에 대해 활성 상태인지 여부를 전환할 수 있게 해줍니다. 그러나 이 함수는 비활성화 상태를 변경하기 전에 `FarmKeeper` 계약에서 수수료 징수를 트리거하지 않습니다. 이는 다음과 같은 시나리오에서 잘못된 수수료 처리로 이어질 수 있습니다:

- 이전에 활성화된 토큰을 비활성화할 때:

  - 마지막 징수 이후 비활성화 시점까지 누적된 수수료가 BuyAndBurn 계약으로 전송되지 않습니다.
  - 이 기간 동안 프로토콜 수수료가 과소 계산됩니다.

- 이전에 비활성화된 토큰을 활성화할 때:
  - 비활성화 기간 동안 누적된 수수료가 갑자기 BuyAndBurn 계약으로 전송됩니다.
  - 비활성화 기간 동안의 수수료를 포함하여 프로토콜 수수료가 과대 계산됩니다.

```solidity
  function setDisabled(address inputTokenAddress, bool disabled) external restricted {
    if (!_inputTokens.contains(inputTokenAddress)) revert InvalidInputTokenAddress();
    _inputTokens.get(inputTokenAddress).disabled = disabled; // @audit should collect fees in FarmKeeper before disable or enable
    emit DisabledUpdated(inputTokenAddress, disabled);
  }
```

```solidity
  function _collectFees(Farm storage farm) private {
    ...
    // Identify tokens which are accepted as input for buy and burn
    bool isInputToken0 = buyAndBurn.isInputToken(farm.poolKey.token0);
    bool isInputToken1 = buyAndBurn.isInputToken(farm.poolKey.token1);

    ...
  }
```

영향은 일관성 없는 수수료 징수 및 분배, 그리고 부정확한 프로토콜 수수료 계산입니다.

토큰을 비활성화하기 전에 `setDisabled` 함수에서 수수료를 징수하는 것이 좋습니다.

# [L-09] `buyAndBurn`에 대한 최소 `toBuy` 및 `toBurn` 부재

`buyAndBurn`이 호출되면 `burnPercentage` 값을 기반으로 `toBuy` 및 `toBurn` 금액을 계산합니다. 값 중 하나가 0이 아닌 한 작업이 처리됩니다.

```solidity
  function buyAndBurn(address inputTokenAddress) external nonReentrant {
    // Ensure processing a valid input token
    if (!_inputTokens.contains(inputTokenAddress)) {
      revert InvalidInputTokenAddress();
    }
    InputToken storage inputTokenInfo = _inputTokens.get(inputTokenAddress);

    // prevent contract accounts (bots) from calling this function
    // becomes obsolete with EIP-3074, there are other measures in
    // place to make MEV attacks inefficient (cap per swap, interval control)
    if (msg.sender != tx.origin) {
      revert InvalidCaller();
    }

    if (inputTokenInfo.paused) {
      revert InputTokenPaused();
    }

    // keep a minium gap of interval between each call
    // update stored timestamp
    if (block.timestamp - inputTokenInfo.lastCallTs <= inputTokenInfo.interval) {
      revert CooldownPeriodActive();
    }
    inputTokenInfo.lastCallTs = block.timestamp;

    // Get the input token amount to buy and incentive fee
    // this call will revert if there are no input tokens left in the contract
    (uint256 toBuy, uint256 toBurn, uint256 incentiveFee) = _getAmounts(inputTokenInfo);

>>> if (toBuy == 0 && toBurn == 0) {
      revert NoInputTokenBalance();
    }
    // ...
}
```

이로 인해 공격자(griefer)는 계약에 먼지(dust) 양의 입력 토큰을 기부하고 `buyAndBurn`을 트리거하여, 부적절한 `buyAndBurn`이 실행되었음에도 불구하고 토큰의 `lastCallTs`를 업데이트하여 간격을 기다리게 할 수 있습니다.

`buyAndBurn`이 트리거될 때 `toBuy` 및 `toBurn`에 대한 최소값을 추가하는 것을 고려해 보십시오. 또한 새로운 입력 토큰이 활성화될 때 `lastCallTs`를 `block.timestamp`로 설정하여 처음 활성화될 때의 괴롭힘(griefing)을 최소화하는 것을 고려해 보십시오.

```diff
function enableInputToken(EnableInputToken calldata params) external restricted {
    // ...

    _inputTokens.add(
      InputToken({
        id: params.id,
        totalTokensUsedForBuyAndBurn: 0,
        totalTokensBurned: 0,
        totalIncentiveFee: 0,
-        lastCallTs: 0,
+        lastCallTs: block.timestamp,
        capPerSwap: params.capPerSwap,
        interval: params.interval,
        incentiveFee: params.incentiveFee,
        burnProxy: IBurnProxy(params.burnProxy),
        burnPercentage: params.burnPercentage,
        priceTwa: params.priceTwa,
        slippage: params.slippage,
        path: params.path,
        paused: params.paused,
        disabled: false
      })
    );

    emit InputTokenEnabled(params.id, params);
  }
```


