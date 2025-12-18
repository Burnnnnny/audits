# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**superseed-xyz/community-raise-contracts** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# SuperSale에 대하여

Supersale 스마트 컨트랙트는 사용자가 구조화된 판매 기간 동안 토큰을 구매할 수 있도록 하며, USDC 또는 USDT를 결제 수단으로 허용합니다. 계층화된 가격 책정, 구매 한도 및 화이트리스트 검증을 위한 머클 증명(Merkle proofs)을 사용합니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| --- | --- | --- | --- |
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

_검토 커밋 해시_ - [7a28d3bfa5d26e736d755a5b8db0a40514150359](https://github.com/superseed-xyz/community-raise-contracts/tree/7a28d3bfa5d26e736d755a5b8db0a40514150359)

_수정 검토 커밋 해시_ - [717d1116edbc2206f202afce23019d505759fe4d](https://github.com/superseed-xyz/community-raise-contracts/tree/717d1116edbc2206f202afce23019d505759fe4d)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `SuperSaleDeposit`

# 발견 사항

# [M-01] `withdrawAssets`가 항상 되돌려짐(revert)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

계약이 자산을 받을 것으로 예상되지는 않지만, 실수로 인해 계약이 받았을 수 있는 자산을 관리자가 인출할 수 있도록 `withdrawAssets` 함수가 제공됩니다.

`withdrawAssets`는 `fromStage` modifier를 사용하여 계약이 `Completed` 단계에 있는지 확인합니다.

```solidity
    function withdrawAssets(address recipient, IERC20 asset)
        external
        onlyRole(ADMIN_ROLE)
  @>    fromStage(Stages.Completed)
```

그러나 이 modifier는 현재 단계가 `Completed`일 때 되돌려집니다.

```solidity
    modifier fromStage(Stages _requiredStage) {
        Stages _currentStage = getCurrentStage();

        // comingSoon = 0, onlyKyc = 1, tokenPurchase = 2, completed = 3
  @>    if (_requiredStage > _currentStage || _currentStage == Stages.Completed) {
            revert WrongStage(msg.sig, _currentStage, _requiredStage);
        }
```

이는 `withdrawAssets`가 항상 되돌려짐을 의미합니다.

## 권고 사항

```diff
    function withdrawAssets(address recipient, IERC20 asset)
        external
        onlyRole(ADMIN_ROLE)
-       fromStage(Stages.Completed)
    {
+       Stages _currentStage = getCurrentStage();
+       if (_currentStage != Stages.Completed) {
+           revert WrongStage(msg.sig, _currentStage, Stages.Completed);
+       }
        uint256 contractBalance = asset.balanceOf(address(this));
```

# [M-02] 잘못된 토큰 전송 계산으로 인해 입금 흐름이 중단됨

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

비공개 함수 `_calculateTokensToTransfer`는 입금된 자금의 양을 기준으로 사용자에게 전송할 토큰의 양을 계산하는 데 사용됩니다. 현재 티어의 한도(cap)에 도달하면 함수는 입력된 금액에 도달할 때까지 남은 티어를 반복합니다.

다음 티어로 이동해야 하는지 여부를 알기 위해 `_remainingAmount`가 현재 티어의 한도보다 작거나 같은지 확인합니다. 이는 잘못된 것인데, 티어 한도는 누적되며 `_remainingAmount`는 대신 티어의 한계 금액(marginal amount)과 비교해야 하기 때문입니다.

마찬가지로 조건이 거짓으로 평가되면 `_computeTokens`는 한도 대신 티어의 한계 금액을 받아야 합니다.

```solidity
        for (uint256 i = (_activeTierIndex + 1); i < _tiers.length; i++) {
            _tier = _tiers[i];

    @>      if (_remainingAmount <= _tier.cap) {
                resultingTokens_ += _computeTokens(_remainingAmount, _tier.price);
                resultingTierIndex_ = i;
                break;
            }

    @>      resultingTokens_ += _computeTokens(_tier.cap, _tier.price);
            _remainingAmount -= _tier.cap;
        }
```

이 문제의 직접적인 결과는 사용자가 예상보다 낮은 가격으로 토큰을 구매할 수 있다는 것입니다.

예를 들어, 사용자가 6,000,000 USDC를 입금하면 처음 2,000,000 USDC는 첫 번째 티어의 가격으로 토큰을 구매하고 4,000,000 USDC는 두 번째 티어의 가격으로 토큰을 구매합니다. 이는 두 번째 티어의 한도가 4,022,400 USD이므로 잘못된 것이며, 이를 초과하는 금액은 세 번째 티어의 가격으로 구매해야 합니다.

더 중요한 것은 다음 줄에서 언더플로가 발생하여 다음 입금이 되돌려진다는 것입니다.

```solidity
        uint256 _remainingTierCap = _tier.cap - _totalFundsCollected;
```

이는 `_totalFundsCollected`가 6,000,000e6인 반면 `_tier.cap`은 4,022,400e6이 되기 때문입니다.

결과적으로 계약에서 더 이상의 입금이 불가능해집니다.

## 개념 증명

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import { SuperSaleDeposit, IERC20 } from "contracts/SuperSaleDeposit.sol";
import { ERC20Mock } from "contracts/mocks/ERC20Mock.sol";

contract AuditTests is Test {
    address alice = makeAddr("Alice");
    address bob = makeAddr("Bob");
    address admin = makeAddr("Admin");
    address operator = makeAddr("Operator");
    address treasury = makeAddr("Treasury");

    bytes32 merkleRoot;
    bytes32[] proofAlice;
    bytes32[] proofBob;
    ERC20Mock usdc;
    ERC20Mock usdt;
    SuperSaleDeposit ssd;

    function setUp() public {
        merkleRoot = 0xd3065b0f6565f0bd25fb4a4c8244880cbb025dbee3fca8058e69a8c80ead4379;
        proofAlice.push(0xf21944a07e01d96dde2b17ede609c9175358722d5933b0177f1f978164c35503);
        proofBob.push(0x49167babc98c47d7595b258ed848414135923f3e527a3ddf2c1bc11f74e18053);

        usdc = new ERC20Mock("USD Coin", "USDC", 6);
        usdc.mint(alice, 21_000_000e6);
        usdc.mint(bob, 21_000_000e6);
        usdt = new ERC20Mock("Tether", "USDT", 6);
        usdt.mint(alice, 21_000_000e6);
        usdt.mint(bob, 21_000_000e6);

        ssd = new SuperSaleDeposit(
            address(this),
            admin,
            operator,
            treasury,
            IERC20(address(usdc)),
            IERC20(address(usdt)),
            merkleRoot
        );

        vm.startPrank(admin);
        ssd.setSaleSchedule(
            block.timestamp + 1 days,
            block.timestamp + 2 days,
            block.timestamp + 3 days
        );
        ssd.setSaleParameters(10e6, 20_000_000e6);
        ssd.unpause();
        vm.stopPrank();
    }

    function test_depositUSDC() public {
        skip(2 days);

        vm.startPrank(alice);
        usdc.approve(address(ssd), type(uint256).max);
        ssd.depositUSDC(6_000_000e6, proofAlice);

        uint256 activeTierIndex = ssd.activeTierIndex();
        assertEq(activeTierIndex, 1);

        vm.expectRevert(abi.encodeWithSignature("Panic(uint256)", 0x11));
        ssd.depositUSDC(1_000e6, proofAlice);
        vm.stopPrank();
    }
}
```

## 권고 사항

```diff
    function _calculateTokensToTransfer(
        uint256 _amount,
        uint256 _totalFundsCollected,
        uint256 _activeTierIndex,
        Tier[4] memory _tiers
-   ) private pure returns (uint256, uint256) {
+   ) private view returns (uint256, uint256) {
  (...)
-       if (_remainingAmount <= _tier.cap) {
+       if (_totalFundsCollected + _amount <= _tier.cap) {
            resultingTokens_ += _computeTokens(_remainingAmount, _tier.price);
            resultingTierIndex_ = i;
            break;
        }

-       resultingTokens_ += _computeTokens(_tier.cap, _tier.price);
-       _remainingAmount -= _tier.cap;
+       uint256 _tierAmount = _tier.cap - tiers[i - 1].cap;
+       resultingTokens_ += _computeTokens(_tierAmount, _tier.price);
+       _remainingAmount -= _tierAmount;
```

# [L-01] 마지막 입금이 DOS에 취약함

`_verifyDepositConditions` 함수에는 입금 금액이 남은 한도(cap)보다 작거나 같아야 한다는 엄격한 확인이 있습니다.

```solidity
        uint256 _remainingCap = _getRemainingCap();
        if (_amount > _remainingCap) {
            revert InvalidPurchaseInput(
                msg.sig, bytes32("_amount"), bytes32("exceeds maxTotalFundsCollected"), _remainingCap
            );
        }
```

공격자는 판매가 `maxTotalFunds`에 도달하기 직전에 `minDepositAmount` 입금으로 트랜잭션을 프론트 러닝하여 남은 한도를 줄여서 합법적인 사용자의 최종 입금이 `amount > remaining cap`으로 인해 되돌려지게 함으로써 완료를 방해할 수 있습니다.

금액을 남은 한도로 조정하여 부분 입금을 허용하는 것이 좋습니다.

```solidity
    if (_amount > _remainingCap) {
        _amount = _remainingCap;
    }
```

# [L-02] 관리자 기능에 대한 중앙화 위험

일부 관리자 기능은 불필요한 단계에서 호출될 수 있어, 올바르게 사용되지 않을 경우 프로토콜이나 사용자에게 해를 끼칠 수 있습니다. 코드는 `setMerkleRoot()`를 KYC 단계 이후에도 호출할 수 있도록 허용하며, 구매 단계에서도 호출하여 이전에 화이트리스트에 있던 사용자를 화이트리스트에서 제거할 수 있습니다. 또한 코드는 `setTiers()`를 모든 단계에서 호출할 수 있도록 하여, 구매 단계에서 이 함수를 호출하고 티어를 변경하면 사용자마다 다른 토큰 가격이 발생할 수 있습니다. 코드는 이러한 기능이 KYC 단계에서만 호출되고 그 이후에는 호출되지 않도록 허용해야 합니다.

# [L-03] USDC 및 USDT 가치에 대한 잘못된 가정

SuperSaleDeposit 계약은 두 스테이블코인이 USD와 1:1 페깅을 유지한다는 암묵적인 가정하에 USDC 및 USDT 입금을 직접 허용합니다. 이 가정은 모든 티어에서 토큰 구매를 위한 중요한 가격 계산에 사용됩니다. 예를 들어 `_computeTokens`에서 토큰 양을 계산할 때:

```solidity
    function _computeTokens(uint256 _amount, uint256 _price) private pure returns (uint256) {
        return (_amount * 1e18) / _price;
    }
```

`_amount`는 가격 검증 없이 동등한 USD 가치로 취급됩니다. 이는 스테이블코인이 상당한 디페깅을 경험할 수 있고 실제로 경험했기 때문에 위험을 초래합니다.

- 2023년 3월, 실리콘 밸리 은행 붕괴 이후 USDC는 $0.88로 디페깅되었습니다.
- USDT는 여러 차례 디페깅을 경험하여 $0.95까지 떨어졌습니다.

스테이블코인이 $1 미만으로 거래되면 사용자는 의도하지 않은 할인으로 토큰을 구매할 수 있습니다.
스테이블코인이 $1 이상으로 거래되면 사용자는 토큰에 대해 초과 지불하게 되어 USD 기준으로 의도한 티어 한도를 잠재적으로 초과할 수 있습니다.

토큰 구매 금액을 계산하기 위해 Chainlink 오라클을 사용하여 USDC 및 USDT의 가격을 가져오는 것을 고려하십시오.

# [L-04] 작은 나눗셈 절삭(truncation)

계약은 티어당 판매될 토큰의 목표 금액을 기준으로 각 티어에 대해 미리 계산된 가격을 사용합니다.

```solidity
    function _computeTokens(uint256 _amount, uint256 _price) private pure returns (uint256) {
        // multiply _amount by 10^(12+6)
        // because the tier prices are already stored as USD 1 = 1 * 10^12
        // adding 6 decimals precision for the token
        return (_amount * 1e18) / _price;
    }
```

그러나 사용자가 보낸 금액에 따라 나눗셈 절삭의 누적으로 인해 총 판매된 토큰 양이 목표 금액과 일치하지 않을 수 있습니다.

다음 예를 고려하십시오:

    계약은 현재 가격이 9_090_909_091e6인 티어 1에 있습니다.

    A) Alice가 500 USDC를 입금합니다.
    Alice는 54999999999 토큰(500e6 * 1e18 / 9_090_909_091e6)을 받습니다.

    B) Bob이 250 USDC를 두 번 입금합니다.
    Bob은 27499999999 토큰(250e6 * 1e18 / 9_090_909_091e6)을 두 번 받아 총 54999999998 토큰을 받습니다.

보시다시피, 전송할 토큰 계산 시 나눗셈 절삭으로 인해 1 토큰의 불일치가 발생합니다. 이 불일치는 시간이 지남에 따라 누적되어 판매된 총 토큰 양과 목표 금액 사이에 상당한 차이를 초래할 수 있습니다.

각 티어에 대해 가격을 미리 계산하는 대신, 티어당 판매될 목표 최대 토큰 양과 현재 판매된 토큰 양을 기준으로 구매된 토큰을 계산하십시오.

다음은 계산 수행 방법에 대한 의사 코드 예제입니다.

```js
if (amount < _remainingTierCap) {
  tokensToTransfer = (remainingTierTargetTokens * amount) / remainingTierCap;
} else {
  tokensToTransfer += remainingTierTargetTokens;
  remainingAmount -= _remainingTierCap;
  // continue looping through the tiers
  // ...
}
```

최대 차이는 1\*10^-6입니다.

# [L-05] 반올림으로 인한 총 판매 토큰 수 감소

프로젝트는 판매되는 토큰의 최대 목표량을 다음과 같이 설정합니다:

- 티어 1: 220 M
- 티어 2: 210 M
- 티어 3: 206 M
- 티어 4: 1,304 M
- 보너스: 60 M

이는 전역적으로 판매되는 최대 토큰 양이 2,000 M임을 의미합니다. 구매된 최대 토큰 양은 1,940 M이어야 합니다.

배포 시 계약은 목표 금액에 도달하도록 각 티어의 가격과 한도를 설정합니다. 그러나 가격 값은 1e6 정밀도로 반올림되므로 정확하지 않습니다.

그 결과 티어당 판매되는 총 토큰 양은 일부 티어에서는 과소평가되고 다른 티어에서는 과대평가되며, 전역적으로 판매되는 총 토큰 양은 목표보다 낮아집니다.

개념 증명:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import { SuperSaleDeposit, IERC20 } from "contracts/SuperSaleDeposit.sol";
import { ERC20Mock } from "contracts/mocks/ERC20Mock.sol";

contract AuditTests is Test {
    address alice = makeAddr("Alice");
    address bob = makeAddr("Bob");
    address admin = makeAddr("Admin");
    address operator = makeAddr("Operator");
    address treasury = makeAddr("Treasury");

    bytes32 merkleRoot;
    bytes32[] proofAlice;
    bytes32[] proofBob;
    ERC20Mock usdc;
    ERC20Mock usdt;
    SuperSaleDeposit ssd;

    function setUp() public {
        merkleRoot = 0xd3065b0f6565f0bd25fb4a4c8244880cbb025dbee3fca8058e69a8c80ead4379;
        proofAlice.push(0xf21944a07e01d96dde2b17ede609c9175358722d5933b0177f1f978164c35503);
        proofBob.push(0x49167babc98c47d7595b258ed848414135923f3e527a3ddf2c1bc11f74e18053);

        usdc = new ERC20Mock("USD Coin", "USDC", 6);
        usdc.mint(alice, 21_000_000e6);
        usdc.mint(bob, 21_000_000e6);
        usdt = new ERC20Mock("Tether", "USDT", 6);
        usdt.mint(alice, 21_000_000e6);
        usdt.mint(bob, 21_000_000e6);

        ssd = new SuperSaleDeposit(
            address(this),
            admin,
            operator,
            treasury,
            IERC20(address(usdc)),
            IERC20(address(usdt)),
            merkleRoot
        );

        vm.startPrank(admin);
        ssd.setSaleSchedule(
            block.timestamp + 1 days,
            block.timestamp + 2 days,
            block.timestamp + 30 days
        );
        ssd.setSaleParameters(250e6, 20_000_000e6);
        ssd.unpause();
        vm.stopPrank();
    }

    function test_totalSold() public {
        skip(2 days);

        vm.prank(alice);
        usdc.approve(address(ssd), type(uint256).max);

        uint256 amountDeposited;
        uint256 purchasedTokens;
        uint256 prevCap;
        uint256 prevPurchasedTokens;

        for (uint256 i = 0; i < 4; i++) {
            (, uint256 cap) = ssd.tiers(i);
            uint256 tierAmount = cap - prevCap;
            prevCap = cap;

            vm.prank(alice);
            ssd.depositUSDC(tierAmount, proofAlice);

            (amountDeposited, purchasedTokens) = ssd.userDeposits(alice);
            console.log("purchased tier %s: %s", i + 1, purchasedTokens - prevPurchasedTokens);
            prevPurchasedTokens = purchasedTokens;
        }

        console.log("deposited total:  %s", amountDeposited);
        console.log("purchased total:  %s", purchasedTokens);
    }
}
```

콘솔 출력:

```js
  purchased tier 1: 219999999997800
  purchased tier 2: 210000000010383
  purchased tier 3: 206000000005667
  purchased tier 4: 1303999999956602
  deposited total:  20000000000000
  purchased total:  1939999999970452
```

권고 사항:

```diff
        _setTiers(
            [
-               Tier(9090909091e6, 2_000_000e6), // 0
-               Tier(9630476190e6, 4_022_400e6), // 1
-               Tier(9880582524e6, 6_057_800e6), // 2
-               Tier(10691871166e6, 20_000_000e6) // 3
+               Tier(9090909090909090, 2_000_000e6), // 0
+               Tier(9630476190476190, 4_022_400e6), // 1
+               Tier(9880582524271844, 6_057_800e6), // 2
+               Tier(10691871165644171, 20_000_000e6) // 3
            ]
        );
```

