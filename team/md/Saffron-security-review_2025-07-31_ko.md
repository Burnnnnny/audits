
# Pashov Audit Group 소개


Pashov Audit Group은 업계에서 입증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 챔피언이거나, 우리와의 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 의욕적인 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의해 주세요.
    

# 면책 조항


스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.


# 서론

<p><strong>saffron-finance/fixed-income</strong> 저장소에 대한 시간 제한이 있는 보안 검토가 Pashov Audit Group에 의해 수행되었으며, 이 기간 동안 Pashov Audit Group은 <strong>Saffron Fixed Income</strong>을 검토했습니다. 총 <strong>12</strong>개의 문제가 발견되었습니다.</p>

# Saffron Fixed Income 소개

<p>Saffron Fixed Income 볼트는 Uniswap V3와 통합되어 사용자가 안정적인 수익을 위한 고정 측(fixed side) 또는 유동성 수수료로 더 높고 위험한 보상을 얻기 위한 가변 측(variable side)에 참여할 수 있게 합니다. 이는 수익 플랫폼 통합을 위한 어댑터, 사용자 지분을 나타내는 베어러 토큰(bearer tokens), 그리고 예금, 수익 정산 및 수수료 분배를 위한 자동화된 볼트 수명 주기 관리를 사용합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [65b34c3a07c8f8e38c4b999a166c529048de8465](https://github.com/saffron-finance/fixed-income/tree/65b34c3a07c8f8e38c4b999a166c529048de8465)<br>&nbsp;&nbsp;(saffron-finance/fixed-income)

**수정 검토 커밋 해시:**<br>• [64e488539107548ab88521afe339e157df8a4f6a](https://github.com/saffron-finance/fixed-income/tree/64e488539107548ab88521afe339e157df8a4f6a)<br>&nbsp;&nbsp;(saffron-finance/fixed-income)

# 범위

- `RestrictedVaultFactory.sol`
- `UniV3Vault.sol`
- `Vault.sol`
- `VaultBearerToken.sol`
- `VaultFactory.sol`
- `AdapterBase.sol`
- `UniV3FullRangeAdapter.sol`
- `UniV3LimitedRangeAdapter.sol`

# 발견 사항



# [M-01] 볼트는 저장이 아닌 수수료 수신자를 위한 팩토리를 사용해야 함

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

현재 `feeReceiver` 주소는 초기화 중에 각 볼트 컨트랙트 내에 상태 변수로 저장됩니다. 이는 `initializeVault(...)` 함수를 통해 배포 시 볼트 팩토리에서 전달됩니다:

```solidity
IVault(vaultInfo[vaultId].addr).initialize(..., feeBps, feeReceiver);
```

이 설계는 나중에 팩토리의 `feeReceiver` 주소가 변경되더라도(예: 거버넌스 업데이트 또는 운영 재구성으로 인해), 이전에 배포된 볼트는 구식 주소를 계속 사용한다는 것을 의미합니다. 그 결과:

* 수수료가 더 이상 사용되지 않거나 잘못된 수신자에게 전송될 수 있습니다.
* 수수료 라우팅이 시간이 지남에 따라 볼트 간에 일관성이 없어집니다.

이 접근 방식은 수수료 로직을 단일 배포 시점 값에 밀접하게 결합하여 중앙화되거나 업그레이드 가능한 수수료 관리를 약화시킵니다.

## 권고 사항

각 볼트에 `feeReceiver`를 유지하는 대신, 볼트는 팩토리에서 현재 주소를 동적으로 쿼리해야 합니다. 이렇게 하면 프로토콜 전체에서 일관된 동작이 보장됩니다. 예를 들어:

```solidity
function getFeeReceiver() public view returns (address) {
    return IVaultFactory(factory).feeReceiver();
}
```



# [M-02] 하드코딩된 주소는 멀티체인 배포를 제한함

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`UniV3LimitedRangeAdapter` 컨트랙트에서 **Uniswap V3 NonfungiblePositionManager**의 주소는 다음과 같이 하드코딩되어 있습니다:

```solidity
INonfungiblePositionManager public constant positionManager = 
INonfungiblePositionManager(0xC36442b4a4522E871399CD717aBDD847Ab11FE88);
```

이 주소는 **Ethereum 메인넷**, **Arbitrum**, **Optimism**에는 정확하지만, **Base에 배포된 주소와 일치하지 않으며** 다른 레이어 2(예: Base, Unichain, zkSync, Scroll)에서도 다를 수 있습니다.

PositionManager 주소가 다른 네트워크에 컨트랙트가 배포되면 Uniswap V3 포지션과의 모든 상호 작용(민팅, 소각, 쿼리)이 **되돌려져(revert)** 어댑터와 종속된 볼트가 작동하지 않게 됩니다.

프로젝트가 **Base** 및 잠재적으로 다른 L2를 지원할 계획이 명확하므로, 이 하드코딩은 **배포 실패 가능성을 높이고** 모든 체인에 대해 재컴파일 또는 코드 수정이 필요하게 만듭니다.

참고: 심각도는 직접적인 보안 취약점이 아니더라도 배포에 치명적인 영향을 미치므로 높음(High)으로 평가되지 않았습니다(역주: 원문은 High로 언급했으나 표기상 Medium으로 되어 있음, 문맥상 배포 실패의 중요성을 강조).

## 권고 사항

* **PositionManager 주소를 불변(immutable) 생성자 인수로 매개변수화하십시오**:

```solidity
  INonfungiblePositionManager public immutable positionManager;

  constructor(INonfungiblePositionManager _positionManager) {
      require(address(_positionManager).code.length > 0, "Invalid PositionManager address");
      positionManager = _positionManager;
  }
```



# [M-03] 악성 어댑터 풀이 고정 투자자를 검열하거나 과도한 담보를 설정할 수 있음

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

프로토콜에는 현재 자본 배포 중 고정 측 투자자가 제공하는 실제 유동성에 대한 상한 확인이 부족합니다. `deployCapital()` 프로세스에서 토큰 금액(amount0, amount1)은 `pool.slot0()`을 통해 얻은 풀 매개변수를 사용하여 계산됩니다. 그러나 제공된 풀 주소는 Uniswap V3 Factory에 대해 진위 여부가 확인되지 않습니다.

### 어댑터 배포 (검증되지 않은 풀):

```solidity
function createAdapter(
    uint256 adapterTypeId,
    address poolAddress,
    bytes calldata data
) public virtual {
    ...
    IAdapter(adapterAddress).initialize(adapterId, poolAddress, defaultDepositTolerance, data);
}
```

### 어댑터 초기화:

```solidity
function initialize(
    uint256 _id,
    address _pool,
    uint256 _depositTolerance,
    bytes memory uniV3InitData
) public virtual override onlyWithoutVaultAttached onlyFactory {
    ...
    IUniswapV3Pool uniswapV3Pool = IUniswapV3Pool(_pool);
    pool = uniswapV3Pool;
}
```

악의적인 행위자는 조작된 `sqrtPriceX96` 값을 반환하는 가짜 풀 컨트랙트를 가리키는 어댑터를 배포하여 필요한 토큰 금액을 잘못 계산하게 할 수 있습니다. 결과적으로 고정 측 투자자는(의도치 않게) 포지션을 민팅할 때 의도한 것보다 **훨씬 더 많은 유동성**을 제공할 수 있습니다.

### slot0()을 사용한 토큰 금액 계산:

```solidity
(uint160 sqrtRatioX96, , , , , , ) = pool.slot0();
(uint256 amount0, uint256 amount1) = LiquidityAmounts.getAmountsForLiquidity(
    sqrtRatioX96,
    poolMinTick.getSqrtRatioAtTick(),
    poolMaxTick.getSqrtRatioAtTick(),
    fixedSideCapacity
);
```

프로토콜은 실제 민팅된 유동성이 최소 임계값(`l * invDepositTolerance`)을 충족하는지 확인하지만, 유동성이 최대 한도를 **초과하지 않는지** 확인하는 절차는 없습니다:

```solidity
require(uint256(l) * invDepositTolerance / 10000 < _liquidity, "L");
```

### 공격 시나리오:

1. **과도한 담보(Overcollateralization) 익스플로잇:**

   * 고정 측을 과도하게 담보하면 포지션의 가중치가 가변 측 투자자 쪽으로 이동합니다.
   * 가변 측 참여자는 포지션 수익의 더 큰 몫을 부당하게 받는 반면, 고정 측 투자자는 의도한 것보다 더 많은 자본을 위험에 빠뜨립니다.

2. **고정 측 참여 검열 (서비스 거부):**

   * 악성 풀의 slot0()이 극단적인 값을 반환하도록 설정함으로써, 공격자는 계산된 `amount0`와 `amount1`이 비현실적으로 작게 만들 수 있습니다.
   * 정직한 고정 측 투자자의 호출은 현재 슬리피지 보호로 인해 되돌려져(revert), 사실상 **자본 배포 능력을 차단**합니다.
   * 이 **검열 벡터**는 특정 볼트에서 고정 측 참여를 마비시키는 데 사용될 수 있습니다.

이 시나리오는 고정 측 투자자가 악의적이지 않더라도 발생할 수 있습니다. 그러나 의도적인 공격자는 다음을 통해 이를 악용할 수 있습니다:

1. 조작된 풀이 있는 악성 어댑터 배포.
2. 고정 측 투자자가 유동성을 과도하게 기여하도록 강제.
3. 또는 고정 측 투자자의 참여를 완전히 방지(검열).
4. 그런 다음 비례적인 위험 없이 수익의 막대한 몫을 얻기 위해 가변 측 투자자로 진입.

## 권고 사항

1. **상한 유동성 확인 강제:**

   * 민팅 후 실제 유동성이 정의된 허용 오차를 초과하지 않는지 확인하십시오:

     ```solidity
     require(_liquidity <= uint256(l) * depositTolerance / 10000, "Excessive liquidity");
     ```
   * 여기서 `depositTolerance`는 합리적인 상한 슬리피지(예: 10100 = +1%)로 설정될 수 있습니다.

2. **어댑터 풀 진위 확인:**

   * 악성 풀 주입을 방지하기 위해 어댑터 생성자에서 Uniswap V3 Factory에 대해 풀 주소를 검증하십시오.



# [L-01] `PositionValue` 라이브러리의 산술 오버플로 또는 언더플로

_해결됨_

Uniswap V3 Core 및 Periphery 코드베이스의 일부 라이브러리는 Solidity 0.8.0 이상에서 사용할 수 있도록 조정되었습니다.

`PositionValue` 라이브러리의 경우 [수수료 계산이 오버플로 및 언더플로에 의존](https://github.com/Uniswap/v3-periphery/issues/198)하므로 조정이 올바르게 수행되지 않았습니다. 따라서 Solidity 0.8.0 이상에서는 실행이 산술 오버플로 또는 언더플로 오류로 인해 패닉 상태가 될 수 있습니다.

다음 코드 스니펫은 문제를 보여줍니다:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import {PositionValue} from "../../contracts/vendor/@uniswap/v3-periphery/contracts/libraries/PositionValue.sol";
import {INonfungiblePositionManager} from "../../contracts/vendor/@uniswap/v3-periphery/contracts/interfaces/INonfungiblePositionManager.sol";

contract PoC is Test {
    INonfungiblePositionManager positionManager = INonfungiblePositionManager(0xC36442b4a4522E871399CD717aBDD847Ab11FE88);

    // forge test --fork-url https://eth.llamarpc.com --fork-block-number 23054470 --mt test_PositionValue
    function test_PositionValue() public {
        PositionValue.fees(positionManager, 19);
    }
}
```

```shell
Ran 1 test for test/foundry/PoC.sol:PoC
[FAIL: panic: arithmetic underflow or overflow (0x11)] test_PositionValue() (gas: 52155)
```

`PositionValue` 라이브러리의 산술 연산을 `unchecked` 블록으로 감싸는 것이 좋습니다.



# [L-02] 가변 측 예금자가 의도한 것보다 적게 예치할 수 있음

_해결됨_

`UniV3Vault`에서 가변 측의 예금은 예치할 금액을 가변 측의 남은 용량으로 제한합니다.

```solidity
      // Deposit only up to capacity
      amount = (amount + variableBearerToken.totalSupply() >= variableSideCapacity)
        ? variableSideCapacity - variableBearerToken.totalSupply()
        : amount;
      require(amount > 0, "NZD");
```

결과적으로 사용자는 예치하려는 금액보다 적게 예치할 수 있습니다.

다음 시나리오를 고려하십시오:
- 앨리스는 볼트의 총 가변 측 용량만큼 자산을 예치하고 싶어합니다.
- 밥은 앨리스를 프론트러닝하여 총 가변 측 용량에서 몇 개의 토큰을 뺀 금액을 예치합니다.
- 앨리스는 소량의 가변 베어러 토큰만 남게 됩니다.

가변 측 예금자가 예치할 최소 금액을 지정할 수 있도록 하고, 예치된 금액이 최소 금액보다 적으면 되돌리는(revert) 것이 좋습니다.



# [L-03] 예상치 못한 `feeBps`로 볼트가 초기화될 수 있음

_해결됨_

`VaultFactory.initializeVault()`는 매개변수 중 하나로 `feeBps`를 전달하여 `Vault.initialize()`를 호출합니다. 이 상태 변수는 소유자가 변경할 수 있으므로 볼트 생성자는 예상과 다른 값으로 초기화할 수 있습니다.

다음 시나리오를 고려하십시오:
- 앨리스가 볼트를 생성하고, 그 당시 `feeBps`는 100(1%)입니다.
- 소유자가 `feeBps`를 500(5%)으로 변경합니다.
- 앨리스는 1% 수수료가 적용될 것이라고 가정하고 볼트를 초기화하지만 실제로는 5% 수수료가 적용됩니다.

볼트 생성자가 예상하는 `feeBps`가 팩토리에 설정된 것과 일치하도록 다음 변경을 제안합니다:

```diff
  function initializeVault(
    uint256 vaultId,
    uint256 fixedSideCapacity,
    uint256 variableSideCapacity,
    uint256 duration,
-   address variableAsset
+   address variableAsset,
+   uint256 expectedFeeBps
  ) public {
    // Get vault info for the vault we want to initialize and make sure msg.sender is the creator
    require(vaultInfo[vaultId].creatorAddress == msg.sender, "CMI");

+  require(feeBps == expectedFeeBps, "FEE_MISMATCH");
```



# [L-04] 볼트 만료 누락으로 인해 무기한 휴면 볼트 가능

_인지됨(Acknowledged)_

프로토콜에는 완전히 자금이 조달되지 않은 볼트를 만료시키는 메커니즘이 부족합니다. 이로 인해 다음이 발생할 수 있습니다:

- **오래된 볼트**: 구성 당시와 다른 시장 상황에서 시작하는 볼트.
- **기회주의적 시작**: 오래된 볼트에 남은 자금은 누군가 누락된 측면을 채워 볼트를 트리거하고 해당 자금을 청구하도록 유혹할 수 있습니다(볼트 기간이 짧은 경우 매력적임).

휴면 볼트 예시: [0x2443eEAC7B1523636c94aFbf4D6fc55fd1507E3b](https://etherscan.io/address/0x2443eEAC7B1523636c94aFbf4D6fc55fd1507E3b)

볼트 구성에 선택적 만료 기간(예: `vaultStartDeadline`)을 추가하는 것을 고려하십시오.
충족되지 않으면 볼트를 시작할 수 없으며 참가자는 여전히 자금을 인출할 수 있습니다.



# [L-05] `initializeVault()`에 접근 제어가 없어 승인되지 않은 초기화 허용

_해결됨_

`RestrictedVaultFactory` 컨트랙트는 볼트 생성을 현재 컨트랙트 소유자로 제한합니다. 그러나 `initializeVault()`를 재정의(override)하지 못해 볼트 생성자로 기록된 이전 소유자가 여전히 접근할 수 있습니다.

볼트 생성 후 초기화 전이나 초기화되지 않은 상태로 남겨진 볼트가 있는 상태에서 `RestrictedVaultFactory` 컨트랙트의 소유권이 변경되면, 이전 소유자는 여전히 임의의 매개변수로 해당 볼트를 초기화하여 의도된 제한적 제어를 우회할 수 있습니다.

```solidity
  function initializeVault(
    uint256 vaultId,
    uint256 fixedSideCapacity,
    uint256 variableSideCapacity,
    uint256 duration,
    address variableAsset
  ) public {
    // Get vault info for the vault we want to initialize and make sure msg.sender is the creator
    require(vaultInfo[vaultId].creatorAddress == msg.sender, "CMI");

    --- SNIPPED ---
  }
```

`RestrictedVaultFactory`에서 `initializeVault()`를 재정의하여 `onlyOwner` 수정자를 추가하는 것을 고려하십시오.



# [L-06] `liquidityRemoved` 이벤트의 잘못된 변수

_해결됨_

`_removeLiquidity` 함수에서 `LiquidityRemoved` 이벤트는 저장 변수인 `liquidity` 변수를 방출(emit)합니다. 그러나 제거된 실제 유동성은 다음을 통해 동적으로 얻어집니다:

```solidity
(,,,,,,, uint128 currentLiquidity,,,,) = positionManager.positions(tokenId);
...
emit LiquidityRemoved(vaultAddress, address(pool), tokenId, liquidity, amount0, amount1);
```

이 `currentLiquidity` 값은 `decreaseLiquidity`로 전달되어 제거된 유동성의 정확한 표현이 됩니다.

이벤트에서 저장 `liquidity` 변수를 사용하면 이벤트 로그와 실제 작업 간에 불일치가 발생할 수 있으며, 특히 저장 변수가 오래되었거나 실시간 포지션 상태와 동기화되지 않은 경우 그렇습니다. 대부분의 경우 일치하지만 편차가 있으면 정확한 이벤트 데이터에 의존하는 오프체인 시스템(예: 인덱서, 대시보드)을 오도할 수 있습니다.



# [L-07] 악성 `variableAsset` 토큰 괴롭힘(griefing) 공격

_인지됨(Acknowledged)_

프로토콜은 `variableAsset`이 사용자 제공 ERC20 토큰 주소인 볼트 생성을 허용합니다. 토큰의 동작이 준수하고 안전한지 확인하기 위한 온체인 검증(예: 신뢰할 수 있는 토큰 화이트리스트)이 시행되지 않습니다.

```solidity
  function initializeVault(
    uint256 vaultId,
    uint256 fixedSideCapacity,
    uint256 variableSideCapacity,
    uint256 duration,
    address variableAsset
  ) public {
    // Get vault info for the vault we want to initialize and make sure msg.sender is the creator
    require(vaultInfo[vaultId].creatorAddress == msg.sender, "CMI");

    // Initialize vault and assign its corresponding adapter
>>  IVault(vaultInfo[vaultId].addr).initialize(vaultId, duration, vaultInfo[vaultId].adapterAddress, fixedSideCapacity, variableSideCapacity, variableAsset, feeBps, feeReceiver);
...
```

```solidity
  function initialize(
    uint256 _vaultId,
    uint256 _duration,
    address _adapter,
    uint256 _fixedSideCapacity,
    uint256 _variableSideCapacity,
    address _variableAsset,
    uint256 _feeBps,
    address _feeReceiver
  ) public virtual override notInitialized {
    // Validate args
    // vaultId and feeBps are already checked in the VaultFactory
    require(msg.sender == factory, "NF");
    require(_duration != 0, "NEI");
    require(_adapter != address(0), "NEI");
    require(_variableSideCapacity != 0, "NEI");
    require(_fixedSideCapacity != 0, "NEI");
>>  require(_variableAsset != address(0), "NEI");
...
>>  variableAsset = _variableAsset;
...
  }
```
공격자는 다음과 같은 악성 ERC20 호환 토큰을 배포할 수 있습니다:

* 기본 ERC20 인터페이스 확인(`totalSupply`, `balanceOf`)을 통과합니다.
* `transfer()` 또는 `transferFrom()` 함수가 특정 조건(예: 인출 중 호출될 때만)에서 **선택적으로** 되돌려지는 허니팟 동작을 구현합니다.

**공격 시나리오:**

1. 공격자는 악성 `variableAsset` 토큰을 사용하여 볼트를 생성합니다.
2. 공격자는 꿀단지 토큰을 사용하여 가변 측 용량을 채웁니다.
3. 의심하지 않는 고정 측 투자자가 겉보기에 정상적인 용량과 수익률을 보고 실제 자산(예: USDC/DAI)을 볼트에 예치합니다.
4. 고정 측 투자자가 `claim()`을 호출하여 `variableAsset` 토큰을 받으려고 시도합니다.
5. 악성 토큰이 전송 시 되돌려져(revert) 전체 트랜잭션이 실패합니다.
6. 볼트 정산/만기 시 공격자는 풀에서 수익을 받을 수 있습니다.
7. 볼트 로직은 토큰 전송이 성공하고 고정 측 투자자가 0이 아닌 `fixedBearerToken` 금액을 가지고 있다고 가정하므로 **고정 측 자금은 무기한 잠겨 있게 됩니다**.

이 벡터는 **인출에 대한 서비스 거부(DoS)**를 초래하여 프로토콜 업그레이드나 수동 개입이 발생하지 않는 한 고정 측 투자자의 자금을 복구할 수 없게 만듭니다. 원금이 영구적으로 잠기기 때문에 고정 측 투자자에게 미치는 영향은 치명적입니다.

공격 벡터가 간단하고 비용이 저렴하지만, 성공 여부는 고정 측 투자자가 신뢰할 수 없는 variableAsset 토큰을 사용하는 볼트와 기꺼이 상호 작용하는지에 달려 있습니다. 다음과 같은 가정하에 가능성은 낮음(Low)입니다:

* 프론트엔드가 알 수 없는 토큰을 사용하는 볼트를 필터링하거나 플래그를 지정합니다.

* 숙련된 사용자는 볼트의 자산 구성에 대해 실사를 수행합니다.

그러나 자동화된 봇과 사회 공학(예: 비정상적으로 높은 수익을 제공하는 볼트)이 존재하는 비허가형 시스템에서는 악용될 위험이 확실히 존재합니다.

**권고 사항**

이 공격 벡터를 완화하려면:

**가변 자산에 대한 온체인 화이트리스트 강제:**

   * 사전 승인된 ERC20 토큰으로만 볼트 생성을 허용합니다.
   * 토큰 승인을 위한 거버넌스 또는 관리자 제어 프로세스.



# [L-08] 어댑터 인스턴스에 대한 바이트코드 유효성 검사 누락

_해결됨_

`createVault` 함수를 통해 볼트가 생성될 때 팩토리는 사용자가 제공한 어댑터 주소가 등록된 어댑터에 해당하는지 확인합니다. 그러나 어댑터의 관련 바이트코드(템플릿)가 여전히 유효하고 팩토리의 `adapterTypeByteCode` 매핑에 존재하는지는 **확인하지 않습니다**.

시스템은 컨트랙트 소유자가 `adapterTypeByteCode`에서 바이트코드를 지워 어댑터 템플릿을 "삭제"(비활성화)할 수 있도록 허용합니다. 그럼에도 불구하고 악의적인 사용자는 여러 어댑터 인스턴스(곧 중단될 어댑터 유형 사용)를 미리 배포할 수 있으며, 소유자가 바이트코드를 삭제한 후에도 이러한 인스턴스를 볼트 생성에 계속 사용할 수 있습니다. 이를 통해 소유자가 의도한 어댑터 유형 비활성화를 우회하여 중단된 어댑터 로직을 가진 볼트가 의도치 않게 생성될 수 있습니다.

이 문제는 **활성(삭제되지 않은) 어댑터 유형 바이트코드가 있는 어댑터 인스턴스만 사용할 수 있도록** 강제해야 하는 로직이 누락되어 발생합니다.

**권고 사항**

`createVault` 함수에 확인을 추가하여 어댑터의 관련 유형 ID가 팩토리 저장소에 여전히 유효한 바이트코드를 가지고 있는지 확인하십시오. 구체적으로:

```solidity
bytes memory adapterBytecode = adapterTypeByteCode[_adapterInfo.adapterTypeId];
require(adapterBytecode.length != 0, "BDE"); // Bytecode Deleted
```

이렇게 하면 바이트코드 삭제 전에 어댑터 인스턴스가 배포되었더라도 템플릿이 비활성화된 후에는 새 볼트를 생성하는 데 사용할 수 없습니다.



# [L-09] 시작 전 거래 수수료로 인해 불공정한 투자자 혜택 발생 가능

_인지됨(Acknowledged)_

현재 설계에서 고정 측 투자자의 유동성은 볼트가 공식적으로 시작되기 **전**(`isStarted == false`)에 Uniswap V3 풀에 추가되고 거래 수수료 생성을 시작할 수 있습니다.

그러나 일단 볼트가 시작되면 이러한 **시작 전 수수료는 시작 후 수수료와 구별할 수 없게 되며**, 인출 또는 청구 프로세스 중에 가변 측 투자자에게 간접적으로 분배될 수 있습니다. 이는 다음과 같은 **경제적 불균형**을 초래합니다.

* 고정 측 투자자는 **모든 초기 위험을 감수**하고 자본을 풀에 제공합니다.
* 반면 가변 측 투자자는 해당 시간 동안 위험이나 자본을 기여하지 않고도 **시작 전 수수료 혜택을 받을 수 있습니다**.

이러한 동작은 가변 측 투자자가 시작 후 활동에서**만** 수익을 얻을 것으로 예상되는 볼트의 의도된 설계와 모순됩니다.

**권고 사항**

다음과 같이 **시작 전 수수료를 분리하거나 귀속시키는** 메커니즘을 도입하십시오:

* `start()`에서 `collect()`를 호출하고 가변 측 참여를 활성화하기 전에 누적된 모든 시작 전 수수료를 고정 측 투자자에게 독점적으로 할당합니다.
* 또는 총 수익에서 시작 전 수수료를 추적하고 차감하여 가변 측 투자자가 볼트가 공식적으로 시작된 **후**에 얻은 수수료 혜택만 받도록 합니다.
