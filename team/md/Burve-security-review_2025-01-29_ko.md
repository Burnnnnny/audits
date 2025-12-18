# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**itos-finance/Burve** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Burve 소개 (About Burve)

Burve는 Uniswap 포지션으로 구축된 곡선형(curve-like) 스테이블 스왑을 사용하여 최대 16개의 토큰을 시장 조성(market-make)하는 다중 풀 시스템입니다. "내재적(implied)" AMM, ERC4626 볼트 및 사용자 관리형 클로저(Closures)를 사용하여 유동성을 관리하고 단 16개의 토큰 예치로 120개의 토큰 쌍에서 거래를 가능하게 합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
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

_검토 커밋 해시_ - [f0597768fee2d00c17429941f4721475e1ca5723](https://github.com/itos-finance/Burve/tree/f0597768fee2d00c17429941f4721475e1ca5723)

_수정 검토 커밋 해시_ - [e89ebff2c7daafc98e94c66e4273e4c366949c76](https://github.com/itos-finance/Burve/tree/e89ebff2c7daafc98e94c66e4273e4c366949c76)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

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

# 발견 사항 (Findings)

# [C-01] 잘못된 괄호 사용으로 인해 완전히 잘못된 계산이 발생함

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

가격이 좁은 범위(narrow range)의 낮은 틱(low tick) 아래일 때 `Edge.calcLowerImplied()`를 호출하면 다음과 같은 코드가 있습니다.

```solidity
uint256 xyX192 = (x << (128 / y)) << 64;
```

이 코드는 먼저 `x`를 128비트 정밀도로 이동하고 `y`로 나눈 다음 64비트 왼쪽으로 이동하여 192비트 정밀도로 갈 때 192비트 정밀도로 `x/y` 비율을 계산하는 것을 목표로 합니다. 그러나 이 공식에는 괄호 사용으로 인해 `x`가 `128 / y` 비트만큼 왼쪽으로 이동되는 치명적인 문제가 있으며, 이는 거의 모든 경우에 0으로 내림되어 변수가 단순히 64비트 왼쪽으로 이동된 `x` 값을 갖게 되는 결과를 낳습니다.

이 취약점은 다른 몇 가지 함수에도 존재합니다.

## 권장 사항

```diff
+ uint256 xyX192 = ((x << 128) / y) << 64;
- uint256 xyX192 = (x << (128 / y)) << 64;
```

# [C-02] `LiqFacet::addLiq`의 잘못된 `cumulativeValue`로 인해 사용자에게 더 적은 지분을 제공함

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

유동성을 추가할 때 다음 코드를 사용하여 사용자에게 줄 지분을 계산합니다.

```solidity

uint256 addedBalance = tokenBalance - preBalance[idx];

uint256 cumulativeValue = tokenBalance;

        TokenRegistry storage tokenReg = Store.tokenRegistry();

        for (uint256 i = 0; i < n; ++i) {

            if (i == idx) {

                continue;

            } else if (preBalance[i] != 0) {

                address otherToken = tokenReg.tokens[i];

                Edge storage e = Store.edge(token, otherToken);

                uint256 priceX128 = (token < otherToken) ? e.getInvPriceX128(tokenBalance, preBalance[i]) : e.getPriceX128(preBalance[i], tokenBalance);

                cumulativeValue += FullMath.mulX128(preBalance[i], priceX128, true);

            }

        }

        shares = AssetLib.add(recipient, cid, addedBalance, cumulativeValue);

```

우리가 유동성을 추가하는 토큰 측면에서 모든 토큰의 총 가치를 계산하고 이를 `cumulativeValue`에 저장합니다. 그런 다음 `num`이 `addedBalance` 입력이고 `denom`이 `cumulativeValue` 입력이며 `total`이 총 지분 양인 지분에 대해 다음 공식을 사용합니다.

```solidity

shares = FullMath.mulDiv(num, total, denom);

```

사용자가 제공한 값과 총 가치를 기준으로 총 지분 양을 단순히 축소합니다. 문제는 `cumulativeValue`에 사용자의 예치금이 포함되어 있어 총 가치가 더 크기 때문에 사용자가 더 적은 지분을 받게 된다는 것입니다.

다음 시나리오를 상상해 봅시다:

1. 예치된 토큰의 `preBalance`는 10이고 추가된 잔액도 10이므로 `tokenBalance`는 20입니다.
2. `cumulativeValue`를 `tokenBalance` 또는 20으로 잘못 설정합니다.
3. 누적 가치가 루프에서 10씩 두 번 증가하여 총 40이 된다고 가정합니다.
4. 사용자의 지분은 `10 * 100 / 40 = 25`(총 지분이 100이라고 가정)가 되고, 총 지분은 125로 이동하며 사용자 지분은 25입니다.
5. 사용자가 인출하면 총 가치 40의 `25 / 125 = 0.2`를 얻을 수 있으므로 8이 됩니다(예치한 것보다 2가 적음, 동일해야 함).

이는 `cumulativeValue`가 `preBalance[idx]` 값으로 초기화되어야 하기 때문입니다. 그러면 사용자 지분 공식은 `10 * 100 / 30 = 0.33`(30은 `preBalance[idx]`의 10 토큰과 다른 토큰의 2 * 10에서 옴)이 됩니다. 이제 사용자가 인출하면 40 자산 중 `33 / 133 = 0,2481203008`을 얻을 수 있으며 이는 약 10입니다.

## 권장 사항

`LiqFacet::addLiq`를 다음과 같이 수정하십시오:

```diff

-        uint256 cumulativeValue = tokenBalance;

+        uint256 cumulativeValue = preBalance[idx];

```

`AssetLib::add`에서 다음 확인을 제거하십시오:

```solidity

require(num == denom, “NDE”);

```

# [C-03] 제한 없는 `diamondCut`으로 무단 패싯(facet) 수정 허용

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`SimplexDiamond` 계약은 선택자(selector)에 `DiamondCutFacet.diamondCut.selector`를 포함합니다. 이 함수는 계약의 기능을 결정하는 패싯 컷(facet cuts)을 추가, 제거 또는 수정할 수 있게 합니다. 그러나 **이 함수는 제한되지 않아** **누구나** 호출하여 선택자나 패싯을 제거하거나 교체할 수 있습니다.

### **개념 증명 (Proof of Concept - PoC)**

아래의 테스트 케이스는 임의의 사용자가 `diamondCut`을 호출하여 계약 기능을 제거하거나 수정하여 잠재적으로 계약에 대한 통제력을 잃을 수 있음을 보여줍니다.

```solidity
// SPDX-License-Identifier: BUSL-1.1
pragma solidity ^0.8.17;

import {Test} from "forge-std/Test.sol";
import {console2} from "forge-std/console2.sol";
import {BurveDeploymentLib} from "../../src/deployment/BurveDeployLib.sol";
import {SimplexDiamond} from "../../src/multi/Diamond.sol";
import {StorageFacet} from "../mocks/StorageFacet.sol";
import {IDiamond} from "Commons/Diamond/interfaces/IDiamond.sol";
import {LibDiamond} from "Commons/Diamond/libraries/LibDiamond.sol";

contract EdgeFacetTest is Test {
    SimplexDiamond public diamond;
    StorageFacet public storageFacet;
    address public owner = makeAddr("owner");
    StorageFacet public storageFacetContract;

    function setUp() public {
        vm.startPrank(owner);

        // Deploy the diamond and facets
        (address liqFacetAddr, address simplexFacetAddr, address swapFacetAddr) = BurveDeploymentLib.deployFacets();

        // Deploy storage facet
        storageFacetContract = new StorageFacet();

        // Create the diamond with initial facets
        diamond = new SimplexDiamond(liqFacetAddr, simplexFacetAddr, swapFacetAddr);

        // Add storage facet using LibDiamond directly since we're the owner
        bytes4[] memory selectors = new bytes4[](1);
        selectors[0] = StorageFacet.getEdge.selector;

        IDiamond.FacetCut[] memory cuts = new IDiamond.FacetCut[](1);
        cuts[0] = IDiamond.FacetCut({
            facetAddress: address(storageFacetContract),
            action: IDiamond.FacetCutAction.Add,
            functionSelectors: selectors
        });

        LibDiamond.diamondCut(cuts, address(0), "");

        vm.stopPrank();
    }

    function test_rediamondCut() public {
        vm.startPrank(makeAddr("random_user"));
        // Add storage facet using LibDiamond directly since we're the owner
        bytes4[] memory selectors = new bytes4[](1);
        selectors[0] = StorageFacet.getEdge.selector;

        IDiamond.FacetCut[] memory cuts = new IDiamond.FacetCut[](1);
        cuts[0] = IDiamond.FacetCut({
            facetAddress: address(0),
            action: IDiamond.FacetCutAction.Remove,
            functionSelectors: selectors
        });

        LibDiamond.diamondCut(cuts, address(0), "");
    }
}
```

## 권장 사항

`diamondCut` 함수가 `AdminLib.validateOwner()` 또는 유사한 권한 확인을 구현하여 권한 있는 관리자만 호출할 수 있도록 제한하십시오.
# [C-04] 제한 없는 `uniswapV3MintCallback()`으로 인한 승인된 토큰 유출

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`Burve::uniswapV3MintCallback`은 접근 제어가 없는 외부 함수입니다. 이 함수는 `amount0Owed`, `amount1Owed`, `data`의 세 가지 매개변수를 받습니다. 그런 다음 `data`를 디코딩하여 주소를 얻고 해당 주소에서 유동성 풀(lp)로 토큰을 전송합니다. 공격자는 어떤 주소가 `Burve.sol`에 승인되었는지 확인하고 해당 주소의 토큰을 풀로 전송할 수 있습니다.

```solidity
  function uniswapV3MintCallback(uint256 amount0Owed, uint256 amount1Owed, bytes calldata data) external {
        address source = abi.decode(data, (address));
        TransferHelper.safeTransferFrom(token0, source, address(pool), amount0Owed);
        TransferHelper.safeTransferFrom(token1, source, address(pool), amount1Owed);
    }
```

1. `Burve`에 대한 승인이 남아 있고 승인된 금액과 동일한 금액을 가진 사용자 주소로 디코딩되는 `data`를 제공합니다.
2. 함수에 접근 제어가 없으므로 자금이 풀로 전송됩니다.
3. 이로 인해 사용자의 자금 손실이 직접 발생합니다.

## 권장 사항

이 함수를 풀만 호출할 수 있도록 제한하십시오.

# [H-01] island.mint() 전에 토큰 전송 누락

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

Burve 계약은 사용자가 아일랜드(island) 풀과 Uniswap V3 풀 모두에 유동성을 제공할 수 있도록 허용합니다. 아일랜드 풀에 유동성을 추가할 때 Uniswap에 유동성을 제공하기 전에 전송자(Burve 계약 자체)로부터 토큰을 전송합니다.

```solidity
    function mint(address recipient, uint128 liq) external {
        for (uint256 i = 0; i < distX96.length; ++i) {
            uint128 liqAmount = uint128(shift96(liq * distX96[i], true));
            mintRange(ranges[i], recipient, liqAmount);
        }

        _mint(recipient, liq);
    }
```

그러나 사용자가 `Burve.mint()`를 호출할 때 필요한 토큰을 사전에 전송하지 않습니다. 이로 인해 트랜잭션이 실패하여 사용자가 Burve 계약을 통해 아일랜드 풀에 유동성을 성공적으로 제공하지 못하게 됩니다.

## 권장 사항

`island.mint()`를 호출하기 전에 필요한 토큰이 전송되고 승인되었는지 확인하십시오.

# [H-02] 토큰 가중치 정규화 시 되돌리기(revert) 발생 가능성

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

스왑 시 가중치를 정규화할 때 `weights` 배열을 금액이 있는 배열에서 백분율이 있는 배열로 바꾸는 다음과 같은 코드가 있습니다.

```solidity
self.weights[i] = FullMath.mulDivX256(
                self.weights[i],
                self.totalWeight
            );
```

문제는 배열에 단일 요소가 있는 경우 해당 요소가 `totalWeight`와 같아지며, 다음 확인이 있는 `mulDivX256()`을 호출할 때 되돌리기(revert)가 발생한다는 것입니다.

```solidity
require(denominator > num, "0");
```

이들이 같으므로 되돌려집니다.

## 권장 사항

값이 같을 때 `weights` 요소를 `2 ^ 256 - 1` 또는 `type(uint256).max`로 설정하십시오.

# [H-03] 잘못된 수신자로 인해 포지션을 제대로 상환할 수 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

발행(minting) 시 다음과 같은 코드가 있습니다.

```solidity
if (range.lower == 0 && range.upper == 0) {
            uint256 mintShares = islandLiqToShares(liq);
            island.mint(mintShares, recipient);
        } else {
            // mint the V3 ranges
            pool.mint(address(this), range.lower, range.upper, liq, abi.encode(msg.sender));
        }
```

아일랜드에 대해 발행할 때 수신자를 `recipient` 입력으로 설정하고 Uniswap V3 유동성을 발행할 때 수신자를 `address(this)`로 설정합니다. 후자는 정확하지만 전자는 그렇지 않습니다. 소각 시 대상의 `burn()` 함수 호출자, 즉 `Burve` 계약에서 지분이 소각되기 때문입니다. 아일랜드에 대해 발행할 때 `Burve` 계약에 발행된 지분이 없으므로 단순히 되돌려집니다.

사용자는 여전히 2가지 옵션을 가지고 있으므로 영향은 중간(medium)입니다:

- 지분을 `Burve` 계약으로 전송한 다음 유동성을 소각하여 트랜잭션을 일괄 처리하면 올바른 결과를 얻을 수 있습니다.
- 아일랜드에서 직접 지분을 소각합니다. 이 경우 사용자는 여전히 발행된 `Burve` 지분을 갖게 됩니다.

## 권장 사항

```diff
-  island.mint(mintShares, recipient);
+  island.mint(mintShares, address(this));
```

# [M-01] 슬리피지 보호 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

Burve 계약은 사용자가 아일랜드 풀과 Uniswap V3 풀 모두에 유동성을 제공할 수 있도록 허용합니다. 사용자에게 발행되는 지분의 양은 다음 계산을 사용하여 결정됩니다:

```solidity
    function islandLiqToShares(uint128 liq) internal view returns (uint256 shares) {
        if (address(island) == address(0x0)) {
            revert NoIsland();
        }

        (uint160 sqrtRatioX96,,,,,,) = pool.slot0();

        (uint256 amount0, uint256 amount1) = getAmountsFromLiquidity(
            sqrtRatioX96,
            island.lowerTick(),
            island.upperTick(),
            liq
        );

        (,, shares) = island.getMintAmounts(amount0, amount1);
    }
```

지분의 양은 `sqrtRatioX96`에 따라 달라지므로 이 값의 변동은 사용자가 받는 지분의 수에 영향을 미칩니다. 결과적으로 사용자는 예상보다 적은 토큰을 받을 수 있습니다.

## 권장 사항

mint/burn 함수에 `minSharesOut` 매개변수를 추가하는 것을 고려하십시오.

# [M-02] 볼트가 일시 중지될 때 `LiqFacet::removeLiq`가 일시적으로 비활성화됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`LiqFacet`에서 사용자가 유동성을 추가할 때 특정 볼트에 단일 토큰을 예치합니다.

```solidity
    function addLiq(
        address recipient,
        uint16 _closureId,
        address token,
        uint128 amount
    ) external nonReentrant returns (uint256 shares) {
```

그러나 유동성을 제거할 때 함수는 토큰이 클로저(closure)에 있는 모든 볼트에서 인출을 시도합니다.

```solidity
        for (uint8 i = 0; i < n; ++i) {
            VertexId v = newVertexId(i);
            VaultPointer memory vPtr = VaultLib.get(v);
            uint128 bal = vPtr.balance(cid, false);
            if (bal == 0) continue;
            // If there are tokens, we withdraw.
            uint256 withdraw = FullMath.mulX256(percentX256, bal, false);
            vPtr.withdraw(cid, withdraw);
            vPtr.commit();
            address token = tokenReg.tokens[i];
            TransferHelper.safeTransfer(token, recipient, withdraw);
        }
```

문제는 이러한 볼트 중 하나가 일시 중지될 때 발생합니다. 함수가 일시 중지된 볼트를 고려하지 않으므로 전체 인출 프로세스가 차단되어 사용자가 유동성을 제거할 수 없습니다.

## 권장 사항

일시 중지된 볼트를 건너뛰고 나머지 활성 볼트에서 인출을 허용하는 메커니즘을 구현하여 원활한 유동성 제거를 보장하십시오.

# [M-03] 프로토콜 `Burve::mint`에서의 전송 수수료 토큰(Fee-on-transfer token) 호환성 문제

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Burve::mint` 함수는 `uniswapV3MintCallback`과 상호 작용하여 `token0`과 `token1`을 Uniswap 풀로 전송합니다. 그러나 이 구현에서는 **전송 수수료** 토큰(각 전송 시 수수료로 일정 비율을 공제하는 토큰)이 제대로 고려되지 않았습니다.

전송 수수료 토큰을 사용할 때 풀이 받는 금액은 **예상 금액보다 적어** 산술 언더플로로 인해 발행 프로세스가 실패하게 됩니다.

### **개념 증명 (Proof of Concept - PoC)**

문제를 시뮬레이션하기 위해 수수료 공제를 모방하기 위해 전송된 값에서 고정 금액을 빼서 `Burve::uniswapV3MintCallback` 함수를 수정했습니다.

```solidity
function uniswapV3MintCallback(uint256 amount0Owed, uint256 amount1Owed, bytes calldata data) external {
    address source = abi.decode(data, (address));
    TransferHelper.safeTransferFrom(token0, source, address(pool), amount0Owed - 10);
    TransferHelper.safeTransferFrom(token1, source, address(pool), amount1Owed - 10);
}
```

### **테스트 케이스 & 로그**

테스트:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.27;

import {Test, console} from "forge-std/Test.sol";
import {Burve, TickRange} from "../src/Burve.sol";
import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

contract BurveTest is Test {
    Burve public burve;
    address public pool = 0xc473e2aEE3441BF9240Be85eb122aBB059A3B57c;
    address public USDC = 0xaf88d065e77c8cC2239327C5EDb3A432268e5831;
    address public holderu = 0xf89d7b9c864f589bbF53a82105107622B35EaA40;
    address public WETH = 0x82aF49447D8a07e3bd95BD0d56f35241523fBab1;
    address public holderw = 0x70d95587d40A2caf56bd97485aB3Eec10Bee6336;
    uint128[] public weights = [4249, 3893, 9122];
    TickRange public range = TickRange(-199800, -194880);
    TickRange[] public ranges = [range, range, range];
    address public alice = makeAddr("random_user");

    function setUp() public {
        // Create main net fork
        vm.createSelectFork({urlOrAlias: "arbitrum_api", blockNumber: 260000000});
        burve = new Burve(pool, address(0), ranges, weights);
        vm.prank(holderu);
        IERC20(USDC).transfer(alice, 1e11);
        vm.prank(holderw);
        IERC20(WETH).transfer(alice, 1e20);
    }

    function test_fee() public {
        vm.startPrank(alice);
        IERC20(USDC).approve(address(burve), 1e11);
        IERC20(WETH).approve(address(burve), 1e20);
        burve.mint(alice, 100);
    }
}
```

로그:

```
    │   │   │   └─ ← [Return] 81792443700 [8.179e10]
    │   │   ├─ [20564] Burve::uniswapV3MintCallback(90917 [9.091e4], 1, 0x000000000000000000000000e2f70c5cbd298fb122db24264d8923e348cedae3)
    │   │   │   ├─ [14480] 0x82aF49447D8a07e3bd95BD0d56f35241523fBab1::transferFrom(random_user: [0xE2F70c5cbD298Fb122db24264d8923E348CeDaE3], 0xc473e2aEE3441BF9240Be85eb122aBB059A3B57c, 90907 [9.09e4])
    │   │   │   │   ├─ [13728] 0x8b194bEae1d3e0788A1a35173978001ACDFba668::transferFrom(random_user: [0xE2F70c5cbD298Fb122db24264d8923E348CeDaE3], 0xc473e2aEE3441BF9240Be85eb122aBB059A3B57c, 90907 [9.09e4]) [delegatecall]
    │   │   │   │   │   ├─ emit Transfer(from: random_user: [0xE2F70c5cbD298Fb122db24264d8923E348CeDaE3], to: 0xc473e2aEE3441BF9240Be85eb122aBB059A3B57c, value: 90907 [9.09e4])
    │   │   │   │   │   ├─ emit Approval(owner: random_user: [0xE2F70c5cbD298Fb122db24264d8923E348CeDaE3], spender: Burve: [0x5615dEB798BB3E4dFa0139dFa1b3D433Cc23b72f], value: 99999999999999909093 [9.999e19])
    │   │   │   │   │   └─ ← [Return] true
    │   │   │   │   └─ ← [Return] true
    │   │   │   └─ ← [Revert] panic: arithmetic underflow or overflow (0x11)
    │   │   └─ ← [Revert] panic: arithmetic underflow or overflow (0x11)
    │   └─ ← [Revert] panic: arithmetic underflow or overflow (0x11)
```

**참고:** 이는 다중 토큰 풀에도 동일하게 적용됩니다.

# [M-04] 다이아몬드 컷을 위한 `SimplexFacet`의 선택자(selectors) 누락

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`SimplexFacet`의 여러 함수가 다이아몬드 컷에서 누락되었습니다:

- `withdrawFees`
- `setName`
- `getName`

이러한 함수는 선택자 목록에 포함되지 않으므로 다이아몬드 계약을 통해 호출할 수 없어 **사실상 접근할 수 없습니다**.

## 권장 사항

다이아몬드 컷 프로세스에 이러한 함수가 선택자로 추가되었는지 확인하십시오.

```diff
        {
-            bytes4[] memory simplexSelectors = new bytes4[](2);
+            bytes4[] memory simplexSelectors = new bytes4[](5);
            simplexSelectors[0] = SimplexFacet.addVertex.selector;
            simplexSelectors[1] = SimplexFacet.setDefaultEdge.selector;
+            simplexSelectors[2] = SimplexFacet.withdrawFees.selector;
+            simplexSelectors[3] = SimplexFacet.setName.selector;
+            simplexSelectors[4] = SimplexFacet.setDefaultEdge.getName;
            cuts[5] =
                FacetCut({facetAddress: simplexFacet, action: FacetCutAction.Add, functionSelectors: simplexSelectors});
        }
```

# [M-05] `DiamondLoupeFacet`에서 `supportsInterface` 선택자 누락

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`SimplexDiamond` 계약은 `IERC165`, `IDiamondCut`, `IDiamondLoupe`, `IERC173`을 포함한 여러 인터페이스를 지원하도록 의도되었습니다. `SimplexDiamond`는 이러한 인터페이스를 `ds.supportedInterfaces`에 올바르게 추가하지만, **`DiamondLoupeFacet`의 `supportsInterface` 함수를 선택자로 포함하는 데 실패했습니다**. 이 누락으로 인해 **계약이 인터페이스를 올바르게 지원하지 못하게 되어 표준 인터페이스 감지 메커니즘과 호환되지 않습니다**.

## 권장 사항

`supportsInterface`가 `DiamondLoupeFacet` 선택자에 포함되었는지 확인하십시오:

```diff
        {
-            bytes4[] memory loupeFacetSelectors = new bytes4[](4);
+            bytes4[] memory loupeFacetSelectors = new bytes4[](5);
            loupeFacetSelectors[0] = DiamondLoupeFacet.facets.selector;
            loupeFacetSelectors[1] = DiamondLoupeFacet.facetFunctionSelectors.selector;
            loupeFacetSelectors[2] = DiamondLoupeFacet.facetAddresses.selector;
            loupeFacetSelectors[3] = DiamondLoupeFacet.facetAddress.selector;
+            loupeFacetSelectors[4] = DiamondLoupeFacet.supportsInterface.selector;
            cuts[1] = FacetCut({
                facetAddress: address(new DiamondLoupeFacet()),
                action: FacetCutAction.Add,
                functionSelectors: loupeFacetSelectors
            });
```
# [L-01] `LPToken.burn()`은 `account == msg.sender`일 때도 허용량(allowance)을 소비함

`LPToken.burn()` 함수는 지정된 양의 LP 토큰을 소각하고 기본 자산을 상환합니다:

```solidity
    function burn(address account, uint256 shares) external {
        _spendAllowance(account, _msgSender(), shares);
        burveMulti.removeLiq(_msgSender(), ClosureId.unwrap(cid), shares);
        _burn(account, shares);
    }
```

현재 이 함수는 `account == msg.sender`인 경우에도 `account`에서 `msg.sender`로의 허용량을 차감합니다. 이는 사용자가 토큰을 상환하기 전에 자신을 승인해야 함을 의미하며, 이는 불필요한 가스 비용과 비효율적인 사용자 경험으로 이어집니다.

`account == msg.sender`일 때 허용량 확인을 건너뛰는 것을 고려하십시오:

```solidity
if (account != _msgSender()) {
    _spendAllowance(account, _msgSender(), shares);
}
```

# [L-02] ERC20 구현이 제로 승인(zero approval) 시 되돌려질(revert) 수 있음

ERC4626 구현에서 토큰 허용량은 작업을 실행하기 전에 필요한 금액에 대해 승인을 부여한 다음 0으로 재설정하는 패턴을 따릅니다:

```solidity
            self.token.approve(address(self.vault), assetsToDeposit);
            self.totalVaultShares += self.vault.deposit(
                assetsToDeposit,
                address(this)
            );
            self.token.approve(address(self.vault), 0);
```

그러나 일부 ERC20 구현은 [제로 값을 승인할 때 되돌려집니다](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-zero-value-approvals). 주목할만한 예는 [approve(0)이 호출될 때 오류를 발생시키는](https://etherscan.io/token/0xB8c77482e45F1F44dE1745F52C74426C631bDD52#code#L94) BNB입니다. 이러한 동작은 서비스 거부(DoS)로 이어져 이러한 토큰이 사용될 때 프로토콜이 작동하지 않게 할 수 있습니다. 대신 OpenZeppelin의 forceApprove()를 사용하는 것을 고려하십시오.

# [L-03] 예치 시 총 자산 계산에서 임시 인출 차감을 놓침

볼트에 예치할 때 다음과 같이 `totalAssets`를 계산합니다:

```solidity
uint256 totalAssets = temp.vars[0] + newlyAdding;
```

문제는 임시 인출 금액에 해당하는 `temp.vars[2]`를 빼지 않는다는 것입니다. 현재는 예치 중에 보류 중인 인출 금액이 있을 수 있는 방법이 없으므로 영향이 없지만, 향후 코드가 변경되면 심각한 문제가 될 수 있습니다.

# [L-04] USDT와 같은 토큰은 사용할 수 없음

스왑 후 커밋하고 볼트에 예치할 때 다음과 같은 코드가 있습니다.

```solidity
self.token.approve(address(self.vault), assetsToDeposit);
self.totalVaultShares += self.vault.deposit(assetsToDeposit, address(this));
```

부울(boolean) 반환 값을 예상하는 `IERC20` 인터페이스를 사용하여 토큰에 대해 `approve()`를 호출하고 있습니다. USDT와 같은 토큰은 부울을 반환하지 않으므로 호출이 항상 되돌려집니다. 대신 `SafeERC20` 라이브러리의 `forceApprove()`를 사용하는 것을 고려하십시오.

# [L-05] 일부 풀은 통합이 불가능함

`Burve` 계약을 배포할 때 다음 코드가 있는 `Burve.nameFromPool()`을 호출합니다:

```solidity
name = string.concat(ERC20(t0).name(), "-", ERC20(t1).name(), "-Stable-KodiakLP");
```

이는 `token0`과 `token1` 이름의 연결을 기반으로 이름을 생성합니다. 그러나 모든 토큰에 문자열 심볼이 있다고 잘못 가정합니다. `MKR`과 같은 토큰은 `bytes32` 유형의 이름과 심볼을 가집니다. 대신 저수준(low-level) 호출을 구현하는 것을 고려하십시오.

# [L-06] `Closure::newClosureId`에서 등록되지 않은 토큰이 토큰 0으로 오인됨

`Closure::newClosureId` 함수는 인덱스를 사용하기 전에 토큰이 **등록되었는지 확인하지 않습니다**. 토큰이 **등록되지 않은 경우** 다음 표현식은:

```solidity
tokenReg.tokenIdx[tokens[i]]
```

`0`을 반환합니다. 계약은 토큰 인덱스가 유효하다고 **잘못 가정**하고 이를 `cid` (Closure ID)에 포함합니다.
사용자가 **등록되지 않은 토큰**을 제공하면 **토큰 0**(첫 번째 등록된 토큰)으로 잘못 매핑됩니다.

# [L-07] Receive 함수는 구현되었으나 ETH를 인출할 방법이 없음

계약에는 `receive` 함수가 포함되어 있지만, ETH를 인출할 방법이 없습니다.

# [L-08] lowTick 및 highTick에 대한 잘못된 경계 확인

Edge.setRange() 함수를 사용하면 소유자가 lowTick 및 highTick 매개변수를 초기화할 수 있습니다:

```solidity
    function setRange(
        Edge storage self,
        uint128 amplitude,
        int24 lowTick,
        int24 highTick
    ) internal {
```

이러한 값을 할당하기 전에 함수는 경계 확인을 시행합니다:

```solidity
        self.lowTick = lowTick;
        require(lowTick > MIN_NARROW_TICK, "ERL");
        self.highTick = highTick;
        require(highTick < MAX_NARROW_TICK, "ERH");
```

이러한 확인은 다음을 보장합니다:

- `lowTick`은 `MIN_NARROW_TICK`보다 엄격하게 큽니다.
- `highTick`은 `MAX_NARROW_TICK`보다 엄격하게 작습니다.

그러나 `MIN_NARROW_TICK` 및 `MAX_NARROW_TICK`이 절대적으로 가장 낮고 가장 높은 유효한 틱 값을 나타내는 경우 조건이 올바르지 않습니다.

- >= 대신 >를 사용하면 불필요하게 `MIN_NARROW_TICK`이 제외됩니다.
- <= 대신 <를 사용하면 불필요하게 `MAX_NARROW_TICK`이 제외됩니다.

다음과 같이 확인을 업데이트하십시오:

```solidity
        require(lowTick >= MIN_NARROW_TICK, "ERL");
        require(highTick <= MAX_NARROW_TICK, "ERH");

```
