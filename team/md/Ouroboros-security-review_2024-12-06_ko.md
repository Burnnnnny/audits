# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Ouroboros-Protocol/Ouroboros-USDx** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Ouroboros 정보

Ouroboros는 USDx와 ORX라는 두 가지 주요 토큰을 가진 차입 및 CDP 스테이블코인 프로토콜입니다. USDx는 TitanX 및 DragonX와 같은 자산을 담보로 사용하는 완전히 담보된 달러 페그 스테이블코인입니다. ORX는 Ouroboros 생태계 내에서 스테이킹 보상 및 수수료 분배를 촉진하는 제한된 공급의 ERC20 토큰입니다. 52주 동안 TitanX 예치에 대한 베스팅 메커니즘과 12주 동안 ETH 기여에 대한 선형 드립 프로세스를 통해 할당됩니다.

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

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [4801614104f3fc8f1e3fd465322455010ff1ed90](https://github.com/Ouroboros-Protocol/Ouroboros-USDx/tree/4801614104f3fc8f1e3fd465322455010ff1ed90)

_수정 검토 커밋 해시_ - [d5f529ad4ec39d87e48a2a77a0898cee5e2c8ab2](https://github.com/Ouroboros-Protocol/Ouroboros-USDx/tree/d5f529ad4ec39d87e48a2a77a0898cee5e2c8ab2)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `BackstopPool`
- `BaseRateAbstract`
- `GasCompensationPool`
- `PositionController`
- `Stable`
- `NonPayableProxy`
- `CollateralControllerState`
- `CollateralControllerImpl`
- `CollateralController`
- `ActivePool`
- `CollateralSurplusPool`
- `DefaultPool`
- `LiquidationManager`
- `PositionManager`
- `PositionState`
- `SortedPositions`
- `DragonXPriceFeed`
- `EthPriceFeed`
- `TitanXPriceFeed`
- `HintHelpers`
- `MultiTroveGetter`

# 발견 사항

# [M-01] 상환 시 TCR 과대 평가

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

시스템은 작업의 성격에 따라 다양한 가격 유형(최저, 최고 및 가중 평균)을 사용합니다. 따라서 일부 작업에서는 프로토콜 안전성 관점에서 가장 보수적인 가격이 사용됩니다.

예를 들어, 부채 생성 시 최저 가격을 사용하여 생성된 부채 금액이 적절한 수준을 초과하지 않도록 합니다.

마찬가지로 상환 시 최고 가격을 사용하여 프로토콜에서 인출되는 담보 금액이 적절한 수준을 초과하지 않도록 합니다. 그러나 `redeemCollateral`에서는 TCR이 MCR보다 높은지 확인하는 데에도 동일한 가격이 사용됩니다. 따라서 최고 가격을 사용하면 TCR을 과대 평가할 수 있으며, 결과적으로 실제 TCR이 MCR보다 낮을 때 상환을 허용할 수 있습니다.

```solidity
File: PositionManager.sol

423:  @>    (totals.price, totals.suggestedAdditiveFeePCT) = priceFeed.fetchHighestPriceWithFeeSuggestion(
424:            totals.loadIncrease,
425:            totals.utilizationPCT,
426:            true,
427:            true
428:        );
429:
430:        _requireValidMaxFeePercentage(totals.maxFeePCT, totals.suggestedAdditiveFeePCT);
431:
432:        uint MCR = collateralController.getMCR(address(collateralToken), totals.version);
433:  @>    _requireTCRoverMCR(totals.price, MCR);
```

## 권장 사항

TCR을 계산할 때 최저 또는 가중 평균 가격을 사용하십시오.

# [M-02] Chainlink 오라클이 잘못된 데이터를 반환하거나 오프라인 상태가 되어 발생하는 DoS

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

Chainlink 오라클 가격 피드는 극단적인 경우 잘못된 데이터를 반환하거나 오프라인 상태가 될 수 있으며, 이로 인해 `EthPriceFeed.fetchEthPrice()` 함수가 되돌려집니다(revert). 이 함수는 담보 상환, 포지션 개설, 담보 추가, 포지션 폐쇄, 에스크로 청구 및 포지션 청산과 같은 시스템의 주요 작업을 실행하기 위해 호출됩니다.

트랜잭션 되돌림의 영향은 청산의 경우 특히 심각하여 시스템이 미담보 상태가 될 수 있으며, 담보 추가의 경우 가격 피드가 다시 온라인 상태가 될 때 사용자가 담보를 잃을 수 있습니다.

이를 방지하기 위해 시스템은 보호자(guardian)가 `resetBreaker` 함수를 호출하여 수동으로 새 가격을 제공하고 오라클 상태를 작동 상태로 설정하는 것에 의존합니다. 그러나 이는 이상적이지 않습니다.

- 오라클이 오프라인 상태가 된 후 보호자가 신속하게 조치를 취해야 합니다.
- 오라클이 다시 온라인 상태가 될 때까지 시스템과의 모든 상호 작용 후에 프로세스를 반복해야 합니다. 모든 호출이 오라클 상태를 다시 작동하지 않음으로 변경하기 때문입니다.
- 중앙 집중화 벡터를 도입합니다.

또 다른 문제는 50%보다 큰 가격 변동이 유효하지 않은 것으로 간주되어 시스템이 극심한 가격 변동에 신속하게 대응할 수 없다는 것입니다.

## 권장 사항

메인 오라클이 실패하거나 가격 변동이 너무 큰 경우 가격을 쿼리하기 위해 대체 오라클을 사용하십시오.

# [M-03] `_maxIterations`에 도달하면 `getRedemptionHints`가 되돌려짐

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`HintHelpers.sol`의 `getRedemptionHints()` 함수는 `redeemCollateral()`에 전달할 올바른 힌트를 찾는 데 사용됩니다. 상환을 위해 고려할 포지션 수는 가스 부족을 방지하기 위해 `_maxIterations`로 제한될 수 있습니다. 그러나 구현에 결함이 있어 `_maxIterations`에 도달하면 함수가 되돌려집니다.

```solidity
File: HintHelpers.sol

48:      while (vars.currentPositionUser != address(0) && vars.remainingStables > 0 && _maxIterations-- > 0) {
```

`_maxIterations` 변수의 감소는 조건 확인 후에 수행됩니다. 즉, `_maxIterations`가 0인 마지막 반복에서 조건은 false로 평가되지만 감소는 여전히 실행되어 언더플로가 발생하고 함수가 되돌려집니다.

Liquity의 원래 코드는 언더플로가 발생하도록 의도적으로 허용했기 때문에 이 문제의 영향을 받지 않습니다. 그러나 solc 0.8.0 이상을 사용하는 경우 언더플로로 인한 되돌림을 방지하도록 코드를 조정해야 합니다.

결과적으로 많은 반복을 수반하는 쿼리(즉, `_maxIterations`에 도달하기 전에 루프가 종료되지 않음)는 되돌려집니다.

## 권장 사항

```diff
-       while (vars.currentPositionUser != address(0) && vars.remainingStables > 0 && _maxIterations-- > 0) {
+       uint i;
+       while (vars.currentPositionUser != address(0) && vars.remainingStables > 0 && i++ < _maxIterations) {
```

# [M-04] 최대 가격 편차가 너무 높음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`MAX_PRICE_DEVIATION_FROM_PREVIOUS_ROUND` 매개변수는 가격 조작을 방지하기 위한 것입니다. `_chainlinkPriceChangeAboveMax()` 함수 내에서 연속 라운드 간에 가격이 크게 변경되었는지 확인하는 데 사용됩니다.

```solidity
    function _chainlinkPriceChangeAboveMax(
        ChainlinkResponse memory _currentResponse,
        ChainlinkResponse memory _prevResponse
    ) internal pure returns (bool) {
        // code ...

        // Return true if price has more than doubled, or more than halved.
        return percentDeviation > MAX_PRICE_DEVIATION_FROM_PREVIOUS_ROUND;
    }
```

함수는 가격이 두 배 이상 또는 반감 이상으로 변한 경우에만 true를 반환합니다. 이 논리는 가격이 49% 변하더라도 함수가 이를 유효한 것으로 간주함을 의미합니다. 그러나 정상적인 시장 상황에서 라운드 간에 이러한 상당한 가격 변동은 가능성이 매우 낮습니다. 결과적으로 현재 구성은 잠재적으로 49%의 가격 조작 창을 허용하며 이는 너무 높습니다.

## 권장 사항

`MAX_PRICE_DEVIATION_FROM_PREVIOUS_ROUND`를 낮추는 것이 좋습니다. 20%와 같은 더 보수적인 값은 [FortressFinance](https://github.com/FortressFinance/fortress-contracts/blob/main/src/shared/lending/oracles/BaseOracle.sol#L98-L104)와 같은 프로토콜에서 볼 수 있는 모범 사례와 일치합니다.

# [M-05] 현재 상환 설계로 인해 사용자가 잘못한 것이 없는데도 페널티가 부과됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

사용자가 에스크로 포지션을 상환할 때 쿨다운 및 유예 기간이 만료된 경우 다음 코드가 있습니다.

```solidity
else {
    // We are about to return stables to user and charge the timeout fee on total remaining amount
    timedOut = true;
    effectiveAmount = redemption.stableAmount;
    redemption.stableAmount = 0;
}
```

이 경우 사용자에게 페널티가 부과되고 에스크로 포지션이 취소됩니다. 이 상황이 발생하는 데는 2가지 시나리오가 있습니다.

시나리오 1:

1. 사용자가 부채 토큰을 담보로 상환하기 위해 에스크로 포지션을 생성합니다.
2. 그는 진지한 사용자가 아니기 때문에 제시간에 포지션을 상환하지 않습니다.
3. 그는 정당하게 페널티 수수료를 부과받습니다.

시나리오 2:

1. 사용자가 에스크로 포지션을 생성합니다.
2. 그는 포지션을 계속해서 상환하려고 시도하지만 담보를 상환할 포지션이 충분하지 않아 결국 여기에 도달합니다(이론적으로는 담보 인출이 0보다 큰지 확인하는 위 코드에 도달하기 전에 되돌릴 수도 있음).

```solidity
else if (unusedStables != 0) {
            // otherwise, transfer back left over stables for next attempt
            stableToken.transferForRedemptionEscrow(msg.sender, address(this), unusedStables);
            escrow.stableAmount += unusedStables;
        }
```

3. 위의 코드에서 사용하지 않은 토큰으로 에스크로 포지션을 다시 채웁니다.
4. 유예 기간이 끝나고 사용자는 잘못한 것이 없는데도 수수료가 부과됩니다.

## 권장 사항

가능한 해결책은 사용자가 담보 상환을 시도했는지 여부를 나타내는 부울 플래그를 상환 포지션에 추가하는 것입니다. 그런 다음 페널티를 적용할 때 사용자가 담보 상환을 시도한 경우 부울이 false인 경우에만 적용하십시오.

# [M-06] 유니스왑 오라클 가격 조작 가능성

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

프로토콜은 유니스왑 V3 오라클을 사용하여 담보 토큰 TitanX 및 DragonX의 가격을 계산합니다. TitanX 가격의 경우 ETH/TITANX 풀이 사용되고, DragonX 가격의 경우 ETH/TITANX 및 TITANX/DRAGONX 풀이 모두 사용됩니다.

이러한 가격 조작을 가능하게 하는 몇 가지 요인이 있습니다.

- 풀의 현재 유동성이 상대적으로 낮습니다. ETH/TITANX 풀의 경우 $4.7M, TITANX/DRAGONX 풀의 경우 $1.9M입니다.
- TWAP 윈도우가 너무 짧습니다. 짧은 TWAP의 경우 36초, 긴 TWAP의 경우 5분입니다.
- DragonX의 경우 두 개의 오라클을 사용하면 두 풀에서 가격을 조작하면 최종 가격에 미치는 영향이 증폭될 수 있으므로 가격 조작이 더 쉬워집니다.

프로토콜은 일부 작업에서 가격 조작을 방지하기 위해 몇 가지 보호 조치를 사용합니다.

- 가격 편차가 특정 임계값보다 높은지 확인합니다.
- 다른 TWAP 윈도우를 사용하고 가장 보수적인 가격을 선택합니다.

첫 번째 보호 조치는 합법적인 사용자에 대한 서비스 거부를 초래할 수 있으므로 모든 작업에 사용할 수 없거나 너무 엄격할 수 없습니다. 두 번째 보호 조치는 유용하지만 가격 조작을 방지하기에는 충분하지 않습니다.

특히 가중 평균 가격의 경우 공격자가 3개 블록을 연속으로 제안하도록 할당된 경우 상대적으로 낮은 자본으로 DragonX 가격을 크게 변경할 수 있습니다. 후자는 매우 드물게 보일 수 있지만 [추정](https://www.alvarorevuelta.com/posts/ethereum-mev-multiblock)에 따르면 1% 지분을 가진 검증자가 평균 5개월에 한 번씩 3개 블록에 연속으로 할당될 것으로 예상됩니다.

공격자는 이 취약점을 악용하여 포지션을 청산하거나, 에스크로된 자금을 청구하거나, 기존 포지션을 폐쇄함으로써 가격 조작으로 이익을 얻을 수 있습니다.

## 개념 증명

이 개념 증명은 Foundry로 생성되었으므로 [프로젝트에 추가](https://book.getfoundry.sh/config/hardhat)해야 합니다.

Ethereum 메인넷 포크에서 테스트를 실행하려면 `{FORK_URL}`을 RPC URL로 바꾸고 다음 명령을 사용하십시오.

```shell
forge test -vv --mt test_manipulateTWAP --fork-url {FORK_URL}`
```

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import { IERC20 } from "forge-std/interfaces/IERC20.sol";
import "contracts/stable/collateral/oracle/TitanXPriceFeed.sol";
import "contracts/stable/collateral/oracle/DragonXPriceFeed.sol";

contract AuditOracleTests is Test {
    address UNI_FACTORY = 0x1F98431c8aD98523631AE4a59f267346ea31F984;
    address SWAP_ROUTER = 0xE592427A0AEce92De3Edee1F18E0157C05861564;
    address PRICE_AGGREGATOR = 0x5f4eC3Df9cbd43714FE2740f5E3616155c5b8419;
    address WETH = 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2;
    address TITANX = 0xF19308F923582A6f7c465e5CE7a9Dc1BEC6665B1;
    address DRAGONX = 0x96a5399D07896f757Bd4c6eF56461F58DB951862;

    uint32 shortTwapSeconds = 36 seconds;
    uint32 longTwapSeconds = 5 minutes;

    TitanXPriceFeed titanXPriceFeed;
    DragonXPriceFeed dragonXPriceFeed;

    uint256 prevDragonUsdSpotPrice;
    uint256 prevDragonUsdShortTWAP;
    uint256 prevDragonUsdLongTWAP;
    uint256 prevDragonUsdLowestPrice;
    uint256 prevDragonUsdWeightedAveragePrice;

    function setUp() public {
        address[] memory _priceAggregatorAddresses = new address[](1);
        _priceAggregatorAddresses[0] = PRICE_AGGREGATOR;

        titanXPriceFeed = new TitanXPriceFeed(
            UNI_FACTORY,
            TITANX,
            WETH,
            address(this),
            address(this),
            _priceAggregatorAddresses
        );

        dragonXPriceFeed = new DragonXPriceFeed(
            UNI_FACTORY,
            DRAGONX,
            TITANX,
            WETH,
            address(this),
            address(this),
            _priceAggregatorAddresses
        );
        dragonXPriceFeed.setPositionManager(address(this));
    }

    function test_manipulateTWAP() public {
        uint256 wethIn = 100e18;
        deal(WETH, address(this), wethIn);

        _skip(1);
        _logPrices();

        uint256 titanOut = _swap(wethIn, WETH, TITANX);
        uint256 dragonOut = _swap(titanOut, TITANX, DRAGONX);

        _skip(1);
        _logPrices();

        titanOut = _swap(dragonOut, DRAGONX, TITANX);
        uint256 wethOut = _swap(titanOut, TITANX, WETH);

        uint256 wethCost = wethIn - wethOut;
        uint256 ethPrice = titanXPriceFeed.fetchEthPrice();
        uint256 usdInitialCapital = wethIn * ethPrice / 1e18;
        uint256 usdCost = wethCost * ethPrice / 1e18;
        console.log("Initial capital: %s WETH (%s USD)", _toDec(wethIn), usdInitialCapital / 1e18);
        console.log("Cost: %s WETH (%s USD)", _toDec(wethCost), usdCost / 1e18);
    }

    function _swap(uint256 amountIn, address tokenIn, address tokenOut) public returns (uint256) {
        IERC20(tokenIn).approve(SWAP_ROUTER, amountIn);

        uint256 amountOut = ISwapRouter(SWAP_ROUTER).exactInputSingle(
            ISwapRouter.ExactInputSingleParams({
                tokenIn: tokenIn,
                tokenOut: tokenOut,
                fee: 10_000,
                recipient: address(this),
                deadline: block.timestamp,
                amountIn: amountIn,
                amountOutMinimum: 0,
                sqrtPriceLimitX96: 0
            })
        );

        return amountOut;
    }

    function _skip(uint256 blocks) private {
        vm.roll(block.number + blocks);
        vm.warp(block.timestamp + blocks * 12);
    }

    function _logPrices() private {
        uint256 dragonUsdSpotPrice = dragonXPriceFeed.getPrice(0);
        uint256 dragonUsdShortTWAP = dragonXPriceFeed.getPrice(shortTwapSeconds);
        uint256 dragonUsdLongTWAP = dragonXPriceFeed.getPrice(longTwapSeconds);
        uint256 dragonUsdLowestPrice = dragonXPriceFeed.fetchLowestPrice(false, false);
        uint256 dragonUsdWeightedAveragePrice = dragonXPriceFeed.fetchWeightedAveragePrice(false, false);

        string memory dragonUsdSpotPriceChange;
        string memory dragonUsdShortTWAPChange;
        string memory dragonUsdLongTWAPChange;
        string memory dragonUsdLowestPriceChange;
        string memory dragonUsdWeightedAveragePriceChange;

        dragonUsdSpotPriceChange = _percentChangeString(prevDragonUsdSpotPrice, dragonUsdSpotPrice);
        dragonUsdShortTWAPChange = _percentChangeString(prevDragonUsdShortTWAP, dragonUsdShortTWAP);
        dragonUsdLongTWAPChange = _percentChangeString(prevDragonUsdLongTWAP, dragonUsdLongTWAP);
        dragonUsdLowestPriceChange = _percentChangeString(prevDragonUsdLowestPrice, dragonUsdLowestPrice);
        dragonUsdWeightedAveragePriceChange = _percentChangeString(prevDragonUsdWeightedAveragePrice, dragonUsdWeightedAveragePrice);

        prevDragonUsdSpotPrice = dragonUsdSpotPrice;
        prevDragonUsdShortTWAP = dragonUsdShortTWAP;
        prevDragonUsdLongTWAP = dragonUsdLongTWAP;
        prevDragonUsdLowestPrice = dragonUsdLowestPrice;
        prevDragonUsdWeightedAveragePrice = dragonUsdWeightedAveragePrice;

        console.log("=> DragonX price (in USD)");
        console.log("spot:         %e", dragonUsdSpotPrice, dragonUsdSpotPriceChange);
        console.log("short TWAP:   %e", dragonUsdShortTWAP, dragonUsdShortTWAPChange);
        console.log("long TWAP:    %e", dragonUsdLongTWAP, dragonUsdLongTWAPChange);
        console.log("lowest:       %e", dragonUsdLowestPrice, dragonUsdLowestPriceChange);
        console.log("weighted avg: %e", dragonUsdWeightedAveragePrice, dragonUsdWeightedAveragePriceChange);
        console.log("");
    }

    function _percentChangeString(uint256 prev, uint256 current) private pure returns (string memory) {
        if (prev == 0) return "";
        int256 change = int256(current) - int256(prev);
        int256 percentChange = (change * 100) / int256(prev);
        if (percentChange == 0) return "";
        return string.concat("(", percentChange > 0 ? "+" : "", vm.toString(percentChange), "%)");
    }

    function _toDec(uint256 value) private returns (string memory) {
        uint256 integerPart = value / 1e18;
        uint256 fractionalPart = (value % 1e18) / 1e16;
        return string.concat(vm.toString(integerPart), ".", vm.toString(fractionalPart));
    }
}
```

콘솔 출력:

```js
  => DragonX price (in USD)
  spot:         6.217954575461e12
  short TWAP:   6.216051602698e12
  long TWAP:    6.216051610562e12
  lowest:       6.216051602698e12
  weighted avg: 6.21605160794e12

  => DragonX price (in USD)
  spot:         8.508795576428e12 (+36%)
  short TWAP:   6.902127347209e12 (+11%)
  long TWAP:    6.296124413169e12 (+1%)
  lowest:       6.296124413169e12 (+1%)
  weighted avg: 6.498125391182e12 (+4%)

  Initial capital: 100.0 WETH (393203 USD)
  Cost: 3.63 WETH (14302 USD)
```

## 권장 사항

가격 조작에 대한 저항을 높이려면 TWAP 윈도우를 늘리십시오. 이 변경을 위해서는 ETH/TITANX 및 TITANX/DRAGONX 풀에 대한 관찰 카디널리티(observation cardinality)도 늘려야 할 수 있습니다.

# [M-07] 기본 요율(Base rate)이 예상보다 느린 속도로 감소할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

기본 요율은 상환 및 신규 부채 발행에 대한 수수료를 계산하는 데 사용되며, 그 가치는 시간이 지남에 따라 감소하고 상환량에 따라 증가합니다.

상환 시 기본 요율의 감소는 마지막 수수료 작업 이후 경과된 시간을 분 단위로 계산합니다. 주목할 점은 경과된 분 수가 가장 가까운 분으로 내림된다는 것입니다.

```solidity
File: BaseRateAbstract.sol
68:    function _minutesPassedSinceLastFeeOp() internal view returns (uint) {
69:        return (block.timestamp - _lastFeeOperationTime) / SECONDS_IN_ONE_MINUTE;
70:    }
```

그러나 작업이 끝날 때 `_lastFeeOperationTime`이 업데이트되면 값이 현재 블록 타임스탬프에 저장됩니다.

```solidity
File: BaseRateAbstract.sol
80:    _lastFeeOperationTime = block.timestamp;
```

이러한 불일치로 인해 기본 요율이 예상보다 느리게 감소할 수 있습니다.

예를 들어 마지막 수수료 작업 이후 119초가 경과한 경우 경과된 분 수는 1로 내림됩니다. 기본 요율은 60초만 경과한 것처럼 감소하지만 `_lastFeeOperationTime`은 전체 119초를 고려하여 업데이트됩니다. 즉, 최악의 시나리오에서는 기본 요율이 119초마다 60초에 해당하는 양만큼만 감소합니다.

## 권장 사항

```diff
    function _updateLastFeeOpTime() internal {
-       uint timePassed = block.timestamp - _lastFeeOperationTime;
+       uint minutesPassed = _minutesPassedSinceLastFeeOp();

-       if (timePassed >= SECONDS_IN_ONE_MINUTE) {
+       if (minutesPassed > 0) {
-           _lastFeeOperationTime = block.timestamp;
+           _lastFeeOperationTime += minutesPassed * SECONDS_IN_ONE_MINUTE;
            emit LastFeeOpTimeUpdated(block.timestamp);
        }
    }
```

# [M-08] 유예 기간 동안 TCR < MCR이면 에스크로된 스테이블이 있는 사용자에게 페널티가 부과됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

상환 쿨다운 요구 사항이 설정되면 사용자는 담보를 상환하기 전에 특정 기간 동안 스테이블을 에스크로해야 합니다. 쿨다운 기간이 끝나면 유예 기간이 시작되고 사용자는 담보를 상환할 수 있습니다. 그러나 유예 기간이 끝나기 전에 상환이 이루어지지 않으면 사용자에게 페널티 수수료가 부과됩니다.

TCR이 MCR 미만이면 `redeemCollateral` 함수가 되돌려지기 때문에 쿨다운 기간 동안 TCR이 MCR 미만으로 떨어지고 유예 기간이 끝나기 전에 회복되지 않으면 사용자는 담보를 상환할 수 없으며 페널티가 부과됩니다.

```solidity
File: PositionManager.sol

433:        _requireTCRoverMCR(totals.price, MCR);
```

## 권장 사항

TCR < MCR 일 때 사용자가 에스크로된 스테이블을 대기열에서 뺄 수 있도록 허용하십시오.

# [M-09] 담보가 일몰(sunset)된 후 대기 중인 스테이블이 `PositionManager`에 갇힐 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

담보가 일몰된 후 고아 담보 토큰은 보호자가 모두 인출할 수 있습니다. 사용자가 대기 중인 상환이 있는 경우 상환할 담보가 없기 때문에 대기 중인 스테이블이 `PositionManager` 계약에 갇히게 됩니다.
사용자가 스테이블을 상환할 수 있도록 하는 한 가지 방법은 `redemptionCooldownPeriod`를 0으로 설정하는 것입니다. 이렇게 하면 `redeemCollateral()`이 호출될 때 `_dequeueEscrow()`가 트리거되기 때문입니다. 그러나 일몰된 담보에는 이 값을 설정할 수 없습니다.

## 권장 사항

담보가 일몰된 후 사용자가 에스크로된 스테이블을 복구할 수 있도록 `activeToSunset()`에서 `redemptionCooldownPeriod`를 0으로 설정하십시오.

# [M-10] 일몰 기간 동안 담보가 복구 모드로 전환되면 차용인은 스테이블을 부당하게 잃게 됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

PositionController 계약은 에스크로 메커니즘을 사용하여 차용인의 스테이블 코인을 청구하기 전에 임시로 보유합니다. 그러나 차용인이 담보와 스테이블 코인을 모두 잃을 수 있는 엣지 케이스가 존재합니다. 예를 들어 다음 시나리오를 고려해 보십시오.

- 차용인이 포지션을 개설하고 스테이블 코인이 에스크로에 배치됩니다.
- 그 직후 담보가 일몰됩니다(예: 변동성이 크기 때문).
- 담보 가격이 하락하여 시스템이 복구 모드로 전환됩니다.

에스크로 기간이 끝나면 차용인은 스테이블 코인이나 담보를 청구할 수 없습니다. 또한 담보가 폐기되었기 때문에 (ICR)을 개선하고 자금을 인출하기 위해 담보를 추가할 수도 없습니다.

## 권장 사항

에스크로된 사용자가 스테이블을 인출하기 위해 폐기 기간 동안 더 많은 담보를 추가할 수 있는 기능을 도입하십시오.

# [M-11] 다시 에스크로된 스테이블을 소비할 수 없어 발생하는 상환 DoS

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

담보 상환 시 상환에 사용된 포지션의 남은 부채가 최소 순 부채보다 낮고 쿨다운 상환 요구 사항이 있는 경우 남은 스테이블이 다시 에스크로되면 모든 `_stableAmount`가 소비되지 않을 수 있습니다.

```solidity
File: PositionManager.sol

288:        if (
289:            ((newDebt - GAS_COMPENSATION) < MIN_NET_DEBT) ||
290:            ((newNICR != _partialRedemptionHintNICR) && !firstInLine)
291:        ) {
292:            singleRedemption.cancelledPartial = true;
293:            return singleRedemption;
294:        }
    (...)
483:        if (totals.cooldownRequirement != 0) {
484:            _checkIfEscrowDepletedAndReEscrowUnusedStables(totals.remainingStables);
485:        }
```

남은 에스크로된 스테이블을 소비하려면 사용자가 `redeemCollateral`을 다시 호출해야 합니다.

그러나 다음과 같은 여러 원인으로 인해 트랜잭션이 되돌려질 수 있습니다.

1. 가장 가능성 있는 시나리오는 상환하려는 포지션이 처음에 상환할 수 없었던 것과 동일한 포지션이며 동일한 조건으로 인해 상환할 담보 부족으로 트랜잭션이 되돌려지는 것입니다.
2. 다시 에스크로된 스테이블 금액이 너무 낮으면 기본 요율 업데이트 시 트랜잭션이 되돌려질 수 있습니다. 이는 a) 현재 기본 요율이 0이고 b) `(_CollateralDrawn * _price) / _totalStableSupply`가 0으로 내림되는 경우 발생할 수 있습니다.

```solidity
File: BaseRateAbstract.sol

33:    function _updateBaseRateFromRedemption(uint _CollateralDrawn, uint _price, uint _totalStableSupply) internal returns (uint) {
34:        // First, decay the base rate based on time passed since last fee operation
35:        uint decayedBaseRate = _calcDecayedBaseRate();
36:
37:        // Calculate the fraction of total supply that was redeemed at face value
38:        uint redeemedStableFraction = (_CollateralDrawn * _price) / _totalStableSupply;
39:
40:        // Increase the base rate based on the redeemed fraction
41:        uint newBaseRate = decayedBaseRate + (redeemedStableFraction / BETA);
42:        newBaseRate = StableMath._min(newBaseRate, StableMath.DECIMAL_PRECISION); // Cap base rate at 100%
43:        assert(newBaseRate > 0); // Base rate is always non-zero after redemption
```

 이로 인해 사용자는 상환을 완료할 수 없으므로 유예 기간이 만료될 때까지 새 상환을 대기열에 넣을 수 없습니다. 또한 사용자에게는 타임아웃 수수료가 부과됩니다.

## 권장 사항

사용자가 에스크로된 모든 스테이블을 소비하려고 시도하지만 현재 조건이 허용하지 않는 경우 남은 스테이블을 대기열에서 빼는 것을 고려하십시오.

# [L-01] `adjustPosition`은 폐기 기간 동안 스테이블 상환을 허용하지 않음

`PositionController.sol`의 `adjustPosition` 함수는 사용자에게 담보 및/또는 스테이블을 늘리거나 줄여 포지션을 조정할 수 있는 유연성을 제공합니다. 그러나 `repayStable` 함수를 통해서는 허용되지만, 폐기 기간 동안에는 스테이블 상환을 허용하지 않습니다.

결과적으로 폐기된 기간에 스테이블을 상환하고 담보를 인출하려는 사용자는 `adjustPosition` 함수를 사용하는 대신 `repayStable`과 `withdrawColl`에 대해 각각 두 번의 호출을 수행해야 합니다.

폐기 기간에 스테이블 상환을 허용하도록 `adjustPosition` 함수를 수정하는 것을 고려하십시오.

```diff
--      if (_collAddition > 0 || _stableChange > 0) {_requireNotSunsetting(asset, version);}
++      if (_collAddition > 0 || (_stableChange > 0 && _isDebtIncrease)) {_requireNotSunsetting(asset, version);}
```

# [L-02] Chainlink 오라클이 실패할 때 `fetchEthPrice()`의 일관성 없는 동작

`EthPriceFeed.fetchEthPrice()` 함수는 `status == Status.chainlinkBroken`일 때 되돌려집니다(revert). 이 상태는 Chainlink 오라클이 잘못되었거나 오래된 데이터를 반환했거나 가격이 이전 가격과 너무 많이 벗어난 후에 설정됩니다. 그러나 이 경우 최신 유효 가격이 반환됩니다.

이로 인해 Chainlink 오라클이 실패한 트랜잭션에서 잘못된 값이 반환될 수 있지만 다음 트랜잭션에서는 되돌려질 수 있습니다.

다음 시나리오를 고려해 보겠습니다.
현재 ETH 가격은 $4000입니다.

- 가격이 $1999로 떨어집니다.
- `fetchEthPrice`가 호출되면 최신 유효 가격인 $4000를 반환하고 상태를 `chainlinkBroken`으로 설정합니다.
- 다음 트랜잭션에서 가격은 여전히 $1999이지만 이 경우 함수는 되돌려집니다.

Chainlink 오라클이 실패할 때 항상 되돌리거나 항상 최신 유효 가격을 반환하는 것을 고려하십시오.

# [L-03] 가격 편차의 오해의 소지가 있는 계산

`TitanXPriceFeed.sol`의 `_priceDeviationInSafeRange()` 함수는 최저 가격과 최고 가격 간의 가격 편차가 안전한 범위 내에 있는지 확인합니다.

```solidity
File: TitanXPriceFeed.sol

334:        uint avgPrice = (minPrice + maxPrice) / 2;
335:        uint percentDifference = ((maxPrice - minPrice) * DECIMAL_PRECISION) / avgPrice;
```

보시다시피 백분율 차이를 계산하기 위해 평균 가격을 기준 가격으로 사용합니다. 이 계산은 최저 가격과 최고 가격 간의 실제 백분율 차이를 나타내지 않으므로 오해의 소지가 있습니다.

다음 예를 고려해 보겠습니다.

```
maxPrice = 100
minPrice = 10
percentDifference = (100 - 10) / ((100 + 10) / 2) = 90 / 55 = 1.63 = 163%
```

결과는 163%이며, 이는 최소 가격과 최대 가격 간의 실제 백분율 차이를 나타내지 않습니다. 이로 인해 `maxOracleDeltaPCT` 매개변수의 값을 해석하고 선택하기가 어렵습니다.

최소 가격을 계산의 기준으로 선택하고 가격이 900% 상승했다고 해석하거나 최대 가격을 기준으로 사용하고 가격이 90% 하락했다고 해석해야 합니다.

이와 관련하여 `EthPriceFeed.sol` 계약의 백분율 편차 계산과 일치하므로 maxPrice를 기준 가격으로 사용하는 것이 좋습니다.

```solidity
File: EthPriceFeed.sol

254:        uint percentDeviation = ((maxPrice - minPrice) * StableMath.DECIMAL_PRECISION) / maxPrice;
```

코드에 다음 변경 사항을 적용하는 것을 고려하십시오.

```diff
-        uint avgPrice = (minPrice + maxPrice) / 2;
-        uint percentDifference = ((maxPrice - minPrice) * DECIMAL_PRECISION) / avgPrice;
+        uint percentDifference = ((maxPrice - minPrice) * DECIMAL_PRECISION) / maxPrice;
```

# [L-04] `getGlobalStats`는 항상 보호자에 대해 0 주소를 반환함

`HintHelpers.sol`의 `getGlobalStats` 함수는 `guardian` 필드를 포함하는 구조체를 반환합니다. 이 필드의 데이터는 `CollateralController` 계약의 `getGuardian` 함수를 사용하여 검색됩니다.

```solidity
File: HintHelpers.sol

206:    stats.guardian = collateralController.getGuardian();
```

`CollateralController`가 `getGuardian` 함수를 직접 구현하지 않기 때문에 호출은 `CollateralControllerImpl` 계약으로 위임되며, 이는 18번 인덱스에 있는 슬롯인 `_guardian` 변수에 해당하는 슬롯의 값을 반환합니다. 그러나 `CollateralController` 계약에서 이 슬롯은 `AccessControl` 계약의 `_roles` 매핑에 해당하며, [항상 비어 있습니다](https://docs.soliditylang.org/en/latest/internals/layout_in_storage.html#mappings-and-dynamic-arrays). 위임된 호출은 `CollateralController`의 컨텍스트에서 실행되므로 `getGuardian`은 항상 0 주소를 반환합니다.

`CollateralController` 계약의 상속 순서를 변경하여 `_guardian` 변수가 `CollateralControllerImpl` 계약과 동일한 슬롯을 차지하도록 하는 것이 좋습니다.

```diff
contract CollateralController
    is CollateralControllerState,
       NonPayableProxy,
-      TimelockController,
-      Guardable {
+      Guardable,
+      TimelockController {
```

# [L-05] `SafeTransferFrom`을 사용하지 않음

`PostionController._activePoolAddColl()`의 현재 구현은 성공적인 전송 시 부울을 반환하지 않는 토큰을 지원하지 않습니다. 문제는 다음 코드에 있습니다.

```solidity
        require(
            collateral.asset.transferFrom(
                msg.sender,
                address(collateral.activePool),
                _amount
            ),
            "PositionController: Sending collateral to ActivePool failed"
        );
```

이러한 토큰을 처리하려면 `safeTransferFrom()`을 사용하는 것을 고려하십시오.

# [L-06] 잘못된 부호로 인해 잘못된 되돌림 발생

`PositionManager._validateAndReleaseStablesForRedemption()`에 다음과 같은 확인이 있습니다.

```solidity
require(cooldownExpiry < block.timestamp, "Redemption escrow timelock not satisfied");
```

쿨다운이 만료되는 시간이므로 `<=`이어야 하므로 확인이 잘못되었습니다. 예를 들어 다음 코드를 살펴보면 다음과 같습니다.

```solidity
bool isGracePeriod = block.timestamp < gracePeriodExpiry;
```

시간이 `gracePeriodExpiry` 이전일 때 유예 기간으로 간주되므로 `gracePeriodExpiry`는 이미 만료된 것으로 간주됩니다. 그러나 쿨다운 만료 확인에서 `cooldownExpiry`는 만료되지 않은 것으로 간주됩니다.

# [L-07] 비율 제한으로 인해 부채 발행을 통한 시스템 복구가 불가능할 수 있음

포지션이 개설될 때 기존 포지션의 부채가 증가하고 시스템이 복구 모드에 있는 경우 사용자에게 차입 수수료가 부과되지 않습니다. 이는 복구 모드에서 필요한 최소 ICR이 더 높고 새로운 부채 발행이 시스템을 복구하는 핵심 메커니즘이 되므로 차입을 최대한 장려하기 위해 수행됩니다.

그러나 사용 가능한 모든 대출 포인트가 소비되면 시스템은 더 이상 복구를 위해 새로운 부채 발행에 의존할 수 없으며 극단적인 경우 시스템이 미담보 상태가 될 수 있습니다.

시스템이 복구 모드일 때 대출 포인트 소비를 우회하는 것을 고려하십시오.

# [L-08] 사용자가 사용률 수수료 계산을 악용하여 더 적은 수수료를 지불할 수 있음

포지션을 개설하면 포인트 사용률에 따라 추가 수수료가 발생합니다.

```solidity
(vars.price, vars.suggestedFeePCT) = collateral.priceFeed.fetchLowestPriceWithFeeSuggestion(
            vars.loadIncrease,
            vars.utilizationPCT,
            true,
            true
        );
```

사용률은 일반적인 공식을 사용하여 계산됩니다.

```solidity
return (usedPoints * DECIMAL_PRECISION) / maxPoints;
```

그러나 이 2가지 요소를 결합하면 사용자가 포지션을 여러 개의 작은 포지션으로 분할하여 예상보다 적은 수수료를 지불할 수 있습니다. 숫자를 사용하여 시나리오를 상상해 보겠습니다. 시나리오는 매우 단순화되었으며 발생하는 다른 유형의 수수료는 무시하고 사용률에 따른 추가 수수료에만 초점을 맞춥니다.

현재 상태:

- usedPoints = 0
- maxPoints = 10_000
