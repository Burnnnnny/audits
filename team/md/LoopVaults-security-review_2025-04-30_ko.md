# 정보
Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항
스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개
**spacegliderrrr/loopedVault** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Loop Vaults 정보

Looped는 사용자 예치금이 오프체인 할당자(allocator)에 의해 관리되어 Morpho 및 Pendle PT를 사용하여 레버리지 루핑 전략을 수행하는 볼트 시스템입니다. 노출을 늘리기 위해 반복적으로 차입하고 스왑함으로써 사용자는 차입 비용보다 높은 고정 이자를 얻는 것을 목표로 합니다.

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
_검토 커밋 해시_ - [08253ac43834c95019ce0ad884d3eb50d8351d6a](https://github.com/spacegliderrrr/loopedVault/tree/08253ac43834c95019ce0ad884d3eb50d8351d6a)

_수정 검토 커밋 해시_ - [5818614c023a8eb339c2cc44c707de5edef3f6fb](https://github.com/spacegliderrrr/loopedVault/tree/5818614c023a8eb339c2cc44c707de5edef3f6fb)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `LoopedVaults` 
 
# 발견 사항
# [H-01] 잘못된 베스팅 이자 계산으로 MEV 공격 가능

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

포지션 값은 `updateInterval` 시간이 지났을 때만 업데이트되므로 MEV 공격을 방지하기 위해 이자가 베스팅됩니다.

그러나 `_vestingInterest()`의 구현이 잘못되어 `block.timestamp == lastUpdate`일 때 0을 반환하고 `vestingDuration`에 도달할 때까지 선형적으로 증가합니다. 이는 업데이트 직후 `totalAssets()`를 호출하면 발생한 모든 이자가 포함되어 업데이트가 MEV 공격의 대상이 된다는 것을 의미합니다.

```solidity
    function totalAssets() public view override returns (uint256) {
        return lastTotalAssets - _vestingInterest();
    }

    function _vestingInterest() internal view returns (uint256) {
        if (block.timestamp - lastUpdate >= vestingDuration) return 0;

        uint256 __vestingInterest = (block.timestamp - lastUpdate) * vestingInterest / vestingDuration;
        return __vestingInterest;
    }
```

## 권장 사항

```diff
-       uint256 __vestingInterest = (block.timestamp - lastUpdate) * vestingInterest / vestingDuration;
+	uint256 __vestingInterest = (vestingDuration - (block.timestamp - lastUpdate)) * vestingInterest / vestingDuration;
```

# [L-01] 활성화된 경우에도 타임락 우회 가능

배포 후 일정 시간이 지나 타임락이 활성화되었다고 가정할 때, `addMarket`은 `scheduleAdd`가 초기화되었는지 확인하지 않으므로 타임락을 여전히 건너뛸 수 있습니다.

따라서 `scheduleAddMarket`을 호출하고 타임락을 기다리지 않고도 새로운 시장을 추가할 수 있습니다.

`scheduledAdd[id] != 0`인지 확인하는 것을 고려하십시오.

# [L-02] 프로토콜이 볼트와 통합하지 못하여 자금 손실 발생

`_deposit` 및 `_withdraw` 함수는 각각 `depositCap` 및 `idleBalance`에 의해 제한되지만 이러한 제한은 ERC4626 함수 `maxDeposit` 및 `maxWithdraw`에 구현되어 있지 않습니다.

이로 인해 허용 가능한 입금/출금 금액을 결정하기 위해 `maxDeposit` 및 `maxWithdraw`에 의존하는 경우, 규정을 준수하는 볼트를 기대하는 다른 프로토콜과의 통합 호환성 문제가 발생할 수 있습니다.

영향: 다른 프로토콜이 계약과 통합할 수 없는 경우 프로토콜 수수료의 간접적 손실 또는 일부 엣지 케이스에서 자금 잠김.

가능성: 래퍼(wrapper)를 사용하면 이 문제를 피할 수 있지만 규정을 준수하는 볼트를 기대하는 일부 프로토콜에서는 불가능할 수 있습니다.

권장 사항:

`maxDeposit` 및 `maxWithdraw`를 재정의하여 `_deposit` 및 `_withdraw`에서 사용되는 것과 동일한 제한을 적용하고, 계약이 입금/출금 가능한 금액을 적절하게 보고하도록 하십시오.

# [L-03] 손상된 할당자(allocator)가 포지션 순환을 통해 볼트를 고갈시킬 수 있음

손상된 할당자는 합법적인 슬리피지 허용치를 통해 가치를 추출하기 위해 포지션을 반복적으로 열고 닫음으로써 볼트 자금을 고갈시킬 수 있습니다. 구성 가능한 슬리피지 보호(`positionMaxSlippage`, 기본값 1%)를 통해 할당자는 다음과 같은 주기를 실행할 수 있습니다.

1. 포지션 오픈: 차입 토큰에서 담보 스왑으로 X% 손실.
2. 포지션 클로즈: 담보에서 차입 토큰 스왑으로 Y% 손실, 차입 자산이 볼트 자산과 다른 경우 추가 슬리피지.

결합된 주기 손실은 허용된 슬리피지 비율의 약 2배-3배입니다. 15개의 활성화된 시장과 속도 제한이 없으면 여러 번의 빠른 주기로 상당한 볼트 가치를 고갈시킬 수 있습니다.

권장 사항:

포지션 작업에 대한 속도 제한을 구현하십시오(예: 시장당 오픈/클로즈 주기 사이의 최소 시간).

# [L-04] 환율을 수정하는 작업은 프론트런 될 수 있음

`onMorphoSupplyCollateral()` 및 `onMorphoRepay()`는 작업으로 인한 순이익 또는 손실로 `lastTotalAssets` 변수를 업데이트합니다. 이것은 주식 대 자산의 환율을 즉시 수정하여 프론트러닝 공격의 문을 엽니다.

자산 가치가 상승하는 경우 공격자는 할당자의 호출 직전에 입금하고 직후에 주식을 상환하여 환율 차이에서 이익을 얻을 수 있습니다.

자산 가치가 하락하는 경우 주주는 할당자의 호출 직전에 주식을 상환하여 손실 발생을 피하고 나머지 주주들의 손실을 증가시킬 수 있습니다.

**개념 증명**

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "../src/LoopedVaults.sol";
import "./utils/MockPricefeed.sol";

contract AuditTest is Test {
    using MarketParamsLib for MarketParams;

    address morpho = 0xBBBBBbbBBb9cC5e90e3b3Af64bdAF62C37EEFFCb;
    address pendlerouter = 0x888888888889758F76e7103c6CbF23ABbF58F946;
    address usdc = 0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913;
    address pricefeed;

    LoopedVault vault;
    address feeRecipient = makeAddr("FeeRecipient");
    address allocator = makeAddr("Allocator");
    address alice = makeAddr("Alice");
    address bob = makeAddr("Bob");
    address ptUsr = 0xa6F0A4D18B6f6DdD408936e81b7b3A8BEFA18e77;

    MarketParams mp;

    function setUp() public {
        vm.createSelectFork("https://mainnet.base.org");

        pricefeed = address(new MockPricefeed());
        MockPricefeed(pricefeed).setPrice(usdc, 1e18);
        MockPricefeed(pricefeed).setPrice(ptUsr, 1e18);

        vault = new LoopedVault(usdc, pendlerouter, morpho, feeRecipient, allocator, pricefeed);

        mp.collateralToken = ptUsr;
        mp.loanToken = usdc;
        mp.oracle = 0x6AdeD60f115bD6244ff4be46f84149bA758D9085;
        mp.irm = 0x46415998764C29aB2a25CbeA6254146D50D22687;
        mp.lltv = 915000000000000000;

        vault.scheduleAddMarket(mp.id());
        vault.addMarket(mp);

        deal(usdc, alice, 10_000e6);
        deal(usdc, bob, 10_000e6);

        vm.prank(alice);
        IERC20(usdc).approve(address(vault), type(uint256).max);
        vm.prank(bob);
        IERC20(usdc).approve(address(vault), type(uint256).max);
    }

    function test_frontRunAllocator() public {
        vm.prank(alice);
        vault.deposit(100e6, alice);
        
        skip(7 days);

        MorphoLoop memory ml;
        ml.marketParams = mp;
        ml.amountToSupply = 5e18;
        ml.amountToBorrow = 4e6;

        SwapData memory swapData;
        swapData.swapType = SwapType(1);
        swapData.extRouter = 0x6131B5fae19EA4f9D964eAc0408E4408b66337b5;
        swapData.extCalldata = hex"e21fd0e90000000000000000000000000000000000000000000000000000000000000020000000000000000000000000c7d3ab410d49b664d03fe5b1038852ac852b1b29000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000a000000000000000000000000000000000000000000000000000000000000001c0000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000000e801010000003d02000000ebbe893eab7c830de0e04cb54a974ea279b9d37a000000000000000000000000004c4b4001fff6fbe64b68d618d47c209fe40b0d8ee6e23c900a833589fcd6edb6e08f4c7c32d4f71b54bda0291335e5db674d8e93a03d814fa0ada70731efe8a4b9888888888889758f76e7103c6cbf23abbf58f9460000000000000000000000007fffffff00000054000000000000000000000000000000000000000000000000000000000000000000000000000000000000048c75d8895400000000000000004568412f557adc1e4f82e73edb06d29ff62c91ec8f5ff06571bdeb29000000000000000000000000000000000000000000000000000000000000000000000000833589fcd6edb6e08f4c7c32d4f71b54bda0291300000000000000000000000035e5db674d8e93a03d814fa0ada70731efe8a4b9000000000000000000000000000000000000000000000000000000000000016000000000000000000000000000000000000000000000000000000000000001a000000000000000000000000000000000000000000000000000000000000001e00000000000000000000000000000000000000000000000000000000000000200000000000000000000000000888888888889758f76e7103c6cbf23abbf58f94600000000000000000000000000000000000000000000000000000000004c4b4000000000000000000000000000000000000000000000000041efd7869134b782000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002200000000000000000000000000000000000000000000000000000000000000001000000000000000000000000c7d3ab410d49b664d03fe5b1038852ac852b1b29000000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000004c4b4000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002607b22536f75726365223a2250656e646c65222c22416d6f756e74496e555344223a22342e39383936303630323834363839383635222c22416d6f756e744f7574555344223a22342e393839363937373936353138343436222c22526566657272616c223a22222c22466c616773223a302c22416d6f756e744f7574223a2235303031333139303537373438333139323632222c2254696d657374616d70223a313734353931373733342c22526f7574654944223a2235636136633763642d616261302d343630322d626131322d653130623239313939396431222c22496e74656772697479496e666f223a7b224b65794944223a2231222c225369676e6174757265223a224172574f7263504839787541726d6d6a6c593475596574516a4b362b715066656f687a396f55594f376f596439766974684c7467726f346b674b744c4732464735735465414c624b703070494c686d68362f587276594b4a303550692f597650683535305759715a6330703736646b317242593947367a5861322b36753356456f2f53306c6657467950564d784b4c6b657258466b787448372b463938464b59766c356c692f307a2f457345424c30615556556c4c3532464d5a6b4e7142516c4c57704f4c415055584944556d6d5049556d68393436556a6a75704e2f4355474a45415644783242446b68712b49545a6a35553061425a4571462b2f4c534b724b38644b2f6a4c36464e34343946666938367059706753596e355257424938534c65596e4b306c48616f6d75646e66372f7a544164754f442f464e453754654a2b544250434f6d37567159792b7449506553435147413d3d227d7d";

        TokenInput memory input;
        input.tokenIn = usdc;
        input.netTokenIn = 5e6;
        input.tokenMintSy = 0x35E5dB674D8e93a03d814FA0ADa70731efe8a4b9;
        input.pendleSwap = 0x313e7Ef7d52f5C10aC04ebaa4d33CDc68634c212;
        input.swapData = swapData;

        ml.input = input;
        ml.SY = 0x4665d514e82B2F9c78Fa2B984e450F33d9efc842;
        ml.pendleMarket = 0x715509Bde846104cF2cCeBF6fdF7eF1BB874Bc45;

        // 밥은 멤풀을 확인하고 트랜잭션이 주식의 가치를 증가시킬 것이라는 것을 알아차립니다.
        // 그래서 그는 입금으로 그것을 프론트런합니다.
        vm.prank(bob);
        uint256 bobDepositAmount = 10_000e6;
        uint256 bobShares = vault.deposit(bobDepositAmount, bob);

        vm.prank(allocator);
        vault.loopToMorpho(ml);

        // Bob은 즉시 이익을 얻기 위해 주식을 인출합니다. 이는 할당자의 트랜잭션을 프론트런하지 않았다면
        // Alice에게 해당했을 것입니다.
        vm.prank(bob);
        uint256 assetsReceived = vault.redeem(bobShares, bob, bob);
        assert(assetsReceived > bobDepositAmount);
    }
}
```

**권장 사항**

- 할당자의 작업으로 인한 이익에 대한 베스팅 메커니즘을 사용하십시오.
- 인출에 쿨다운 기간을 추가하십시오.
