# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**itos-finance/Burve** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Burve 소개

Burve는 Uniswap 포지션에서 구축된 커브(Curve)형 스테이블 스왑을 사용하여 최대 16개의 토큰을 마켓 메이킹 하는 다중 풀(multi-pool) 시스템입니다. "암시적(implied)" AMM, ERC4626 저장소(vaults), 사용자 관리 클로저(Closures)를 사용하여 유동성을 관리하고 단 16개의 토큰 예치만으로 120개의 토큰 쌍에서 거래를 가능하게 합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간 - 프로토콜 자산의 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터일 뿐이지만 여전히 발생 가능성이 상대적으로 높습니다.

- 낮음 - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 또는 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정해야 함 (Should fix)

- 낮음 - 수정할 수 있음 (Could fix)

# 보안 평가 요약

_검토 커밋 해시_ - [b48765aba6f0d0172f5a75ad209ea5e2d4e352a3](https://github.com/itos-finance/Burve/tree/b48765aba6f0d0172f5a75ad209ea5e2d4e352a3)

_수정 검토 커밋 해시_ - [61419ff42e43ee945e22a9ac3e1b743248b306b3](https://github.com/itos-finance/Burve/tree/61419ff42e43ee945e22a9ac3e1b743248b306b3)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Burve`
- `KodiakIsland`
- `TransferHelper`
- `EdgeFacet`
- `LiqFacet`
- `SimplexFacet`
- `SwapFacet`
- `Asset`
- `Closure`
- `Diamond`
- `E4626`
- `Edge`
- `LPToken`
- `Store`
- `BurveDeployLib.sol`
- `Token`
- `UniV3Edge`
- `VaultProxy`
- `Vertex`

# 발견 사항

# [H-01] 가용 유동성 과대평가로 인한 `mint()` 및 `burn()`의 서비스 거부(DoS)

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

Burve 계약에서 `mint()` 또는 `burn()`이 호출될 때 `compoundV3Ranges()`가 실행됩니다. 이 함수는 다음 작업을 수행합니다:

1. 모든 범위에 대해 UniswapV3 풀에서 수수료를 징수합니다.
2. token0과 token1의 가용 잔액으로 민팅할 수 있는 총 유동성을 계산합니다.
3. 가중치 분포에 따라 각 범위에 대해 민팅할 수 있는 유동성을 계산합니다.
4. 각 범위에 대해 각각의 유동성을 민팅합니다.

2단계는 `collectAndCalcCompound()` 함수에 의해 수행되며 다음 단계를 따릅니다:

1. Burve 계약의 token0과 token1 잔액을 얻습니다.
2. 범위의 가중치 분포를 고려하여 `2^64` 유동성을 민팅하는 데 필요한 token0과 token1의 양을 계산합니다.
3. token0과 token1의 양을 `2^64`로 스케일링하고 이전 단계에서 계산된 양으로 나누어 각 토큰의 명목 유동성을 계산합니다.
4. 명목 유동성을 token0과 token1 유동성 중 최소값으로 제한하고 민팅 시 반올림 오류를 조정합니다.

그러나 스케일링된 분포의 분포가 풀의 실제 유동성 분포와 일치하지 않을 수 있다는 점은 고려되지 않아, 민팅할 수 있는 유동성의 양이 과대평가 되어 트랜잭션이 실패할 수 있습니다.

다음 예시를 고려해 보겠습니다:

- Burve 계약에는 동일한 가중치 분포를 가진 두 개의 범위가 있습니다.
- 소각(burn) 시 token0과 token1의 잔액은 1입니다.
- `amount0InUnitLiqX64`와 `amount1InUnitLiqX64`는 1e18로 계산됩니다.
- `nominalLiq0 = (1 << 64) / 1e18 = 18`입니다 (`nominalLiq1`도 동일).
- `mintNominalLiq`는 반올림 오류를 조정하여 `mintNominalLiq = 18 - 2 * 2 = 14`를 제공합니다.
- 그러나 1 wei는 두 범위로 나눌 수 없으므로 실제 사용 가능한 유동성은 0입니다.

결과적으로 `mint()` 및 `burn()` 함수는 되돌려집니다(revert). 사용자는 명목 유동성 계산이 과대평가 되지 않도록 token0 또는 token1의 양을 계약에 기부하도록 강제됩니다. 그러나 악의적인 공격자가 트랜잭션이 되돌려지도록 하는 양을 기부하여 트랜잭션을 프론트 러닝 하여 서비스 거부 공격을 일으킬 수도 있다는 점에 유의하십시오.

### 개념 증명 (Proof of concept)

여러 범위를 사용하도록 `test/single/Burve.t.sol`의 `forkSetup()` 함수를 수정하십시오:

```diff
-       TickRange[] memory v3Ranges = new TickRange[](1);
-       v3Ranges[0] = TickRange(
-           clampedCurrentTick - v3RangeWidth,
-           clampedCurrentTick + v3RangeWidth
-       );
-
-       uint128[] memory v3Weights = new uint128[](1);
-       v3Weights[0] = 1;

+       TickRange[] memory v3Ranges = new TickRange[](2);
+       v3Ranges[0] = TickRange(
+           clampedCurrentTick - v3RangeWidth,
+           clampedCurrentTick + v3RangeWidth
+       );
+       v3Ranges[1] = TickRange(
+           clampedCurrentTick + v3RangeWidth,
+           clampedCurrentTick + v3RangeWidth + tickSpacing * 5
+       );
+
+       uint128[] memory v3Weights = new uint128[](2);
+       v3Weights[0] = 1;
+       v3Weights[1] = 1;
```

다음 테스트 함수를 추가하십시오:

```solidity
function test_burnRevert() public forkOnly {
    deal(address(token0), address(alice), 1e32);
    deal(address(token1), address(alice), 1e32);

    // 1. Mint
    vm.startPrank(alice);
    token0.approve(address(burveV3), type(uint128).max);
    token1.approve(address(burveV3), type(uint128).max);
    burveV3.mint(address(alice), 2e18, 0, type(uint128).max);
    vm.stopPrank();

    // 2. Accumulate fees
    (uint160 sqrtPriceX96, , , , , , ) = pool.slot0();
    deal(address(token0), address(this), 100_100_000e18);
    deal(address(token1), address(this), 100_100_000e18);

    pool.swap(
        address(this),
        true,
        100_100_000e18,
        TickMath.MIN_SQRT_RATIO + 1,
        new bytes(0)
    );
    pool.swap(
        address(this),
        false,
        100_100_000e18,
        sqrtPriceX96,
        new bytes(0)
    );
    vm.roll(block.timestamp * 100_000);

    // 3. Burn
    vm.prank(alice);
    vm.expectRevert(bytes("STF"));
    burveV3.burn(1e18, 0, type(uint128).max);
}
```

다음 명령으로 테스트를 실행하십시오:

```shell
forge test --mt test_burnRevert --fork-url https://rpc.berachain.com/ --fork-block-number 2109308 -vvvv
```

로그를 검토하면 24080586665097 token0이 수집된 것을 볼 수 있습니다. 첫 번째 범위에 대해 16105268222585 token0이 전송되고 두 번째 범위에 대해 7975318442513 token0을 전송하려고 할 때 트랜잭션이 실패합니다.
문제는 유동성이 과대평가 되어 계산된 유동성을 민팅하려면 총 24080586665098 token0(실제 잔액보다 1 초과)이 필요하다는 것입니다.

## 권장 사항

가능한 해결책은 오프체인 서비스가 오프라인에서 유동성 가치를 계산하도록 하여 `mint()` 및 `burn()` 호출자가 유동성 양을 제출하고 계약이 제공된 유동성이 최적의 양인지 확인하는 것입니다.

# [H-02] 첫 번째 예치자 프론트 러닝 공격

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`Burve` 계약은 공격자가 첫 번째 예치를 프론트 러닝 하는 ERC-4626 저장소의 일반적인 문제를 겪을 수 있습니다.

1. 사용자가 공유 지분(shares)을 발행하기 위해 계약에 유동성을 추가합니다.
2. 공격자는 1개의 공유 지분을 발행하고 사용자가 제공한 유동성의 50%를 기부하여 사용자의 트랜잭션을 프론트 러닝 합니다.
3. 사용자 트랜잭션이 실행되고 발행된 공유 지분은 `mintNominalLiq * totalShares / totalNominalLiq`로 계산됩니다. `compoundV3Ranges()`가 실행됨에 따라 `totalNominalLiq`가 공격자의 기부로 인해 팽창하므로 나눗셈이 잘리고 사용자는 1개의 공유 지분만 발행합니다: `x * 1 / (0.5x + 1) = 1`.
4. 공격자는 자신의 공유 지분을 소각하여 계약 유동성의 절반을 받습니다.

### 개념 증명 (Proof of concept)

`test/single/Burve.t.sol` 파일에 다음 코드를 추가하고 `forge test --mt test_firstDepositorDonation --fork-url https://rpc.berachain.com/ --fork-block-number 2073450`을 실행하십시오.

```solidity
function test_firstDepositorDonation() public forkOnly {
    deal(address(token0), address(alice), 10e18);
    deal(address(token1), address(alice), 10e18);
    deal(address(token0), address(charlie), 10e18);
    deal(address(token1), address(charlie), 10e18);

    // Alice provides 1 wei of liquidity
    uint128 liq = 1;
    (int24 lower, int24 upper) = burveV3.ranges(0);
    (uint256 mint0, uint256 mint1) = getAmountsForLiquidity(liq, lower, upper, true);

    vm.startPrank(alice);
    token0.approve(address(burveV3), mint0);
    token1.approve(address(burveV3), mint1);
    burveV3.mint(address(alice), liq, 0, type(uint128).max);
    vm.stopPrank();

    // Alice donates 1e18 of liquidity
    liq = 1e18;
    (mint0, mint1) = getAmountsForLiquidity(liq, lower, upper, true);

    vm.startPrank(alice);
    token0.transfer(address(burveV3), mint0);
    token1.transfer(address(burveV3), mint1);
    vm.stopPrank();

    // Charlie provides 2e18 of liquidity and mints only 1 share
    liq = 2e18;
    (mint0, mint1) = getAmountsForLiquidity(liq, lower, upper, true);

    vm.startPrank(charlie);
    token0.approve(address(burveV3), mint0);
    token1.approve(address(burveV3), mint1);
    burveV3.mint(address(charlie), liq, 0, type(uint128).max);
    vm.stopPrank();

    assertEq(burveV3.totalShares(), 2);
    assertEq(burveV3.balanceOf(charlie), 1);
}
```

## 권장 사항

다음을 포함하여 이 문제를 완화하는 다양한 방법이 있습니다:

- 데드 셰어(Dead shares) 사용: 계약 배포 시 유동성을 제공하도록 강제합니다. 가장 구현하기 쉽습니다.
- 가상 예치(Virtual deposit) 사용: OpenZeppelin의 ERC4626은 이 솔루션을 구현하며 [문서화가 잘 되어 있습니다](https://docs.openzeppelin.com/contracts/4.x/erc4626).

# [H-03] Burve 토큰 전송 시 Island 공유 지분 소유권이 업데이트되지 않음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

Kodiak island 계약이 `Burve`에 설정되면, `mint()` 시 유동성의 일부가 island 공유 지분을 발행하는 데 사용되며, 이는 수신자의 이름으로 스테이션 프록시에 예치되고 수신자에 대한 `islandSharesPerOwner` 매핑이 업데이트됩니다. 수신자는 또한 Uniswap 풀에 제공된 유동성에 대해 `Burve` 토큰(지분)을 받습니다.

그러나 수신자가 `Burve` 토큰을 다른 주소로 전송하면 island 공유 지분은 `StationProxy`와 `Burve`의 `islandSharesPerOwner` 매핑 모두에서 여전히 그에게 할당된 상태로 남습니다. 즉, 전송 후 `Burve` 토큰의 새 소유자는 `StationProxy`에서 보상을 수확할 수 없으며 기저 유동성을 대가로 island 공유 지분을 소각할 수도 없습니다.

## 권장 사항

`_update()` 함수를 덮어써서 `Burve` 전송 시:

- LP 토큰의 비례 금액이 `StationProxy`에서 인출되도록 하십시오.
- LP 토큰이 새 소유자의 이름으로 다시 예치되도록 하십시오.
- `islandSharesPerOwner`가 이전 소유자와 새 소유자 모두에 의해 업데이트되도록 하십시오.

# [M-01] `EnsureClosure`는 나중에 추가된 정점(vertex)의 클로저를 추가하지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

3개의 vertexId를 포함해야 하는 closureId가 있다고 가정해 봅시다. 하지만 현재는 그중 2개만 Token.sol에 등록되어 있습니다. `addLiq()`로 유동성이 추가될 때 클로저를 보장하기 위해 수행되는 방식이므로 이것은 괜찮습니다 ->

```solidity
for (uint8 i = 0; i < n; ++i) {
            VertexId v = newVertexId(i);
            if (cid.contains(v)) {
                Vertex storage vert = Store.vertex(v);
                // We need to add it to the Vertex so we can use it in swaps.
                vert.ensureClosure(cid);
```

따라서 처음 2개의 정점에 대해 `homSet` 및 `homs`가 설정됩니다.

이제 나중에 세 번째 토큰이 새 정점으로 추가되고 이 토큰이 위의 원래 closureId에 포함된다고 가정해 보겠습니다. 따라서 사용자가 유동성을 추가할 때 동일한 코드 조각이 호출되지만 `ensureClosure()` 함수에서 다음으로 인해 세 번째 정점의 `homSet` 및 `homs`가 설정되지 않습니다 ->

```solidity
if (closure.contains(neighbor)) {
                if (self.homSet[neighbor][closure]) {
                    // We've already added this closure
                    return;
                }
```

첫 번째 토큰/정점에 대해 `homSet`이 이미 설정되었으므로 세 번째 정점의 상태를 추가하는 대신 반환됩니다.

## 권장 사항

`ensureClosure()` 함수로 반환하는 대신 `continue`를 사용하십시오.

# [M-02] `FeeLib` 계산이 되돌려질 수 있음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`FeeLib` 라이브러리는 [Uniswap의 V3 `PositionValue` 라이브러리](https://github.com/Uniswap/v3-periphery/blob/main/contracts/libraries/PositionValue.sol)를 기반으로 합니다. 그러나 수수료 계산이 [오버플로 및 언더플로를 예상](https://github.com/Uniswap/v3-periphery/issues/198)한다는 점은 고려되지 않았습니다. 원래 라이브러리는 오버플로/언더플로에 대해 되돌리지 않는 0.8.0보다 낮은 solc 버전을 사용하므로 문제가 되지 않았습니다. 그러나 Burve 구현에서는 이로 인해 트랜잭션이 되돌려집니다.

이 문제는 Berachain 네트워크 포크에서 `test_QueryValue_V3_NoFees` 테스트를 실행하여 재현할 수 있습니다:

```shell
forge test --mt test_QueryValue_V3_NoFees --fork-url https://rpc.berachain.com/ --fork-block-number 2073480
```

결과적으로 `Burve.sol`의 모든 쿼리 기능이 예기치 않게 되돌려질 수 있습니다.

## 권장 사항

`FeeLib` 라이브러리를 조정하여 확인되지 않은(unchecked) 수학 연산을 사용하도록 하여 라이브러리가 트랜잭션을 되돌리지 않고 오버플로 및 언더플로를 처리할 수 있도록 하십시오.

# [M-03] `SimplexFacet`에서 `setAdjustor` 선택기가 포함되지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`SimplexFacet` 계약의 `simplexSelectors` 배열에서 `setAdjustor` 선택기가 누락되었습니다. 즉, `setAdjustor` 함수를 호출할 수 없습니다.

## 권장 사항

```diff
-       bytes4[] memory simplexSelectors = new bytes4[](9);
+       bytes4[] memory simplexSelectors = new bytes4[](10);
        simplexSelectors[0] = SimplexFacet.getVertexId.selector;
        simplexSelectors[1] = SimplexFacet.addVertex.selector;
        simplexSelectors[2] = SimplexFacet.getTokens.selector;
        simplexSelectors[3] = SimplexFacet.getIndexes.selector;
        simplexSelectors[4] = SimplexFacet.numVertices.selector;
        simplexSelectors[5] = SimplexFacet.withdrawFees.selector;
        simplexSelectors[6] = SimplexFacet.setDefaultEdge.selector;
        simplexSelectors[7] = SimplexFacet.setName.selector;
        simplexSelectors[8] = SimplexFacet.getName.selector;
+       simplexSelectors[9] = SimplexFacet.setAdjustor.selector;
```

# [L-01] 중복된 island 범위에 대한 확인 없음

`Burve` 계약의 계약자는 설정 데이터에 대해 일련의 확인을 수행합니다. 여기에는 범위 값에 대한 유효성 검사와 island 주소가 제공된 경우에만 island 범위 (0, 0)이 사용되는지 확인하는 것이 포함됩니다.

그러나 island 범위가 두 번 이상 사용되는지 여부는 확인하지 않습니다. island 범위가 기본값이므로 한 번만 사용되는지 확인하는 것이 좋습니다.

```diff
+   bool islandRangeUsed = false;
    for (uint256 i = 0; i < _ranges.length; ++i) {
        TickRange memory range = _ranges[i];

        ranges.push(range);

-       if (range.isIsland() && address(island) == address(0x0)) {
-           revert NoIsland();
+       if (range.isIsland()) {
+           if (islandRangeUsed) {
+               revert IslandRangeDuplicated();
+           } else if (address(island) != address(0x0)) {
+               revert IslandAddressProvided();
+           }
+           islandRangeUsed = true;
        }

        if (
            (range.lower % tickSpacing != 0) ||
            (range.upper % tickSpacing != 0)
        ) {
            revert InvalidRange(range.lower, range.upper);
        }
    }
```

# [L-02] 프로덕션을 위해 `forge-std/interfaces`에서 인터페이스를 가져오는 것은 권장되지 않음

`DecimalAdjustor` 계약은 natspec 주석에 따라 `forge-std/interfaces`에서 `IERC20.sol`을 직접 가져옵니다.

```solidity
// Open zeppelin IERC20 doesn't have decimals for some reason.
import {IERC20} from "forge-std/interfaces/IERC20.sol";
```

`forge-std`는 스마트 계약 개발 및 테스트 툴체인인 Foundry와 함께 사용하기 위한 유용한 유틸리티 및 계약 모음입니다. 의존성 버전 관리 및 호환성 문제로 인해 프로덕션 코드에서 forge-std/interfaces를 직접 사용하는 것은 권장되지 않습니다.

`decimals()` 함수를 구현하는 OpenZeppelin의 `IERC20Metadata.sol` 인터페이스와 같은 `안정적이고 널리 사용되는 라이브러리`를 사용하는 것이 좋습니다.

# [L-03] `defaultEdge` 정확성에 대한 온전성 확인(sanity check) 누락

토큰 간의 가장자리(edge)가 설정되지 않은 경우 `edge()` 함수는 기본 가장자리를 반환합니다. 그러나 이 기본 가장자리는 정확성에 대해 제대로 확인되지 않습니다. 구체적으로 `defaultEdge.amplitude == 0`인 경우 `SwapFacet._preSwap()` 및 `LiqFacet.addLiq()` 함수에서 사용되어 예기치 않은 동작을 유발할 수 있으므로 `edge()` 함수가 잘못된 가장자리 사용을 방지하기 위해 `revert` 되어야 합니다.

이 문제는 `Edge.getSlot0()` 함수에서 적절한 확인이 제거되고 `Store::edge()` 함수에 다시 도입되지 않아 발생했습니다. 또한 이 확인을 제거하면 코드에서 `error NoEdgeSettings(address token0, address token1);`이 사용되지 않은 상태로 남습니다.

`Store::edge()` 함수에 누락된 온전성 확인을 추가하는 것을 고려하십시오.

# [L-04] 테스트 전용 `ViewFacet` 패싯 포함

테스트 전용 `ViewFacet` 패싯이 프로덕션 코드에 직접 포함되어 있어 외부 계약에서 진실의 소스(source of truth)로 잠재적으로 사용될 수 있습니다. 예를 들어 `ViewFacet.getVertex()` 함수는 테스트 전용으로 가능한 첫 번째 정점만 반환합니다.

`ViewFacet`은 프로덕션에 포함되어서는 안 됩니다. `diamondCut` 기능을 사용하여 테스트 목적으로만 추가하고 SimplexDiamond `production` 다이아몬드 컷에 테스트에서만 추가해야 합니다.

SimplexDiamond 초기 다이아몬드 컷에서 `ViewFacet` 패싯을 제거하는 것을 고려하십시오.

# [L-05] `RemoveLiq`는 정점이 잠겨 있는지 확인하지 않음

LiqFacet에서 `removeLiq()`로 유동성을 제거할 때 정점이 클로저 내에 있으면(잔액이 0이 아니면) vertexId를 가져옵니다 ->

```solidity
for (uint8 i = 0; i < n; ++i) {
            VertexId v = newVertexId(i);//AUDIT-no check if the vertex is locked.
            VaultProxy memory vProxy = VaultLib.getProxy(v);
            uint128 bal = vProxy.balance(cid, false);
```

그러나 vertexId가 현재 잠겨 있을 가능성이 있으며, 이 경우 정점의 클로저에서 인출하는 것도 잠겨야 합니다.

**권장 사항**

유동성을 제거할 때 정점이 잠겨 있는지 확인하십시오.

# [L-06] Kodiac Island 일시 중지 메커니즘이 자금을 잠글 수 있음

[Kodiac Island 계약](https://berascan.com/address/0x4a254B11810B8EBb63C5468E438FC561Cb1bB1da#code)에는 `mint()` 및 `burn()` 기능을 비활성화하는 일시 중지 메커니즘과 민팅 기능을 관리자로 제한하는 `restrictedMint` 매개변수가 있습니다.

`Burve` 계약이 Island 계약을 사용하고 언급된 메커니즘 중 하나가 활성화된 경우 `burn()` 및/또는 `mint()` 함수가 되돌려지게 됩니다. 이는 특히 `burn()` 함수의 경우 사용자의 자금이 잠길 수 있으므로 중요합니다.

**권장 사항**

Island 계약이 일시 중지되거나 제한될 때 island 공유 지분의 민팅 및 소각을 건너뛰도록 허용하고 제한이 해제되면 island 공유 지분 민팅 및 소각을 완료하는 메커니즘을 제공하십시오.
