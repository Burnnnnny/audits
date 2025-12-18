# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **karak-restaking** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Karak 정보 (About Karak)

Karak은 사용자가 스테이킹된 자산을 다른 애플리케이션에 재사용할 수 있도록 합니다. 스테이커는 자산을 Karak 네트워크의 분산 보안 서비스(DSS)에 할당하고 스테이킹된 자산에 대한 추가 집행 권한을 부여하는 데 동의할 수 있습니다. 옵트인 기능은 데이터 가용성 프로토콜, 브리지 또는 오라클과 같은 보안 서비스의 조건을 충족하기 위해 추가 슬래싱 조건을 생성합니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

_검토 커밋 해시_ - [18ab80f7d6c650dd62f570891546c1d47d08afc3](https://github.com/Andalusia-Labs/karak-restaking/tree/18ab80f7d6c650dd62f570891546c1d47d08afc3)

_수정 검토 커밋 해시_ - [f0971648ce32f59f8a65a265361c41193714115f](https://github.com/Andalusia-Labs/karak-restaking/tree/f0971648ce32f59f8a65a265361c41193714115f)
_(Andalusia-Labs 저장소로 이동됨)_

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Vault`
- `VaultSupervisor`
- `VaultSupervisorLib`
- `DelegationSupervisor`
- `DelegationSupervisorLib`
- `Staker`
- `Withdraw`

# 발견 사항 (Findings)

# [M-01] `returnShares()`를 통해 입금한 사용자는 인출할 수 없음 (Users who deposit via `returnShares()` can be unable to withdraw)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

문제는 `gimmieShares()` 및 `returnShares()` 함수가 볼트 배열 `stakersVaults`를 업데이트하지 않는다는 것입니다.
예를 들어, 사용자의 첫 번째 입금이 `returnShares`를 통해 이루어진 경우, 해당 볼트는 사용자의 볼트 배열에 추가되지 않습니다. 결과적으로 해당 볼트를 제거하려고 할 때 인출이 되돌려집니다:

```solidity
    function removeShares(address staker, IVault vault, uint256 shares)
        external
        onlyDelegationSupervisor
        onlyChildVault(vault)
        nonReentrant
    {
        ...
        if (userShares == 0) {
            removeVaultFromStaker(staker, vault);
        }
    }
```

이 문제는 `gimmieShares()`를 호출하여 모든 지분을 전송한 다음 일반 `deposit`을 호출하여 완화할 수 있지만, 이 시나리오는 제대로 문서화되지 않았으며 불변 통합자 계약에는 적용할 수 없습니다.

## 권장 사항

```diff
    function gimmieShares(IVault vault, uint256 shares) public onlyChildVault(vault) nonReentrant {
        require(shares != 0);
        IERC20 shareToken = IERC20(vault);

        VaultSupervisorLib.Storage storage self = _self();
        // Verify the user is the owner of these shares
        if (self.stakerShares[msg.sender][vault] < shares) revert NotEnoughShares();

        self.stakerShares[msg.sender][vault] -= shares;
+       if (userShares == 0) {
+           removeVaultFromStaker(staker, vault);
+       }

        shareToken.transfer(msg.sender, shares);
    }

    function returnShares(IVault vault, uint256 shares) external onlyChildVault(vault) nonReentrant {
        IERC20 shareToken = IERC20(vault);

        VaultSupervisorLib.Storage storage self = _self();
+
+       require(shares != 0);
+       if (self.stakerShares[staker][vault] == 0) {
+           if (self.stakersVaults[staker].length >= Constants.MAX_VAULTS_PER_STAKER) revert MaxStakerVault();
+           self.stakersVaults[staker].push(vault);
+       }

        self.stakerShares[msg.sender][vault] += shares;

        shareToken.transferFrom(msg.sender, address(this), shares);
    }
```

# [M-02] 첫 입금자 공격 가능 (First depositor attack is possible)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

다음과 같은 흐름을 가진 고전적인 공격입니다:

1. 사용자가 볼트와 처음으로 상호 작용하며 입금 트랜잭션을 보냅니다.
2. 공격자는 2가지 작업을 수행하여 사용자 tx보다 먼저 실행합니다: 1) 1 wei 자산을 입금하여 1 wei 지분을 주조합니다; 2) 볼트에 많은 양의 자산을 기부합니다.
3. ERC4626 내부에서 사용되는 공식 `shares = assets * sharesSupply / totalAssets`의 반올림으로 인해 사용자의 트랜잭션이 실행되고 사용자는 0 지분을 주조합니다. 이는 `sharesSupply`가 1이고 `totalAssets`가 기부로 인해 크기 때문입니다.
4. 그런 다음 공격자는 기부된 자산과 사용자의 예치금을 인출합니다.

그러나 Karak-restaking은 Solady의 ERC4626 구현을 사용하여 공격의 수익성을 낮추고 수행하기 어렵게 만듭니다. 기본적으로 다른 공식을 사용합니다: `shares` 및 `assets` 계산에 추가 1을 더합니다.
결과적으로 멤풀에 크기가 거의 비슷한 첫 번째 입금이 2개 이상 있는 경우 공격이 수익성이 있습니다. 볼트는 프로토콜의 핵심 메커니즘이므로 공격을 수행할 기회가 여러 번 있을 것임을 언급할 가치가 있습니다.

## 권장 사항

2가지 가능한 완화 방법이 있습니다:

1. Vault.sol에서 `_decimalsOffset()` 함수를 재정의합니다. 그러면 Vault의 소수점이 증가합니다.
2. UniswapV2에서 수행되는 것처럼 "죽은 지분"을 생성합니다. 즉, 초기화 단계에서 볼트에 초기 금액을 예치합니다.

# [L-01] 목록 길이에 대한 최대 제한이 없어 인출 시 OOG가 발생할 수 있음 (Withdrawals may encounter OOG as there is no max limit for list length)

사용자는 시스템에서 자금을 인출하기 위해 `startWithdraw()`를 호출한 다음 `finishWithdraw()`를 호출해야 합니다. 사용자가 인출 기능을 호출할 때 인출하려는 볼트 목록을 지정하는데, 문제는 `finishWithdraw()` 내부의 로직이 더 복잡하여 더 많은 가스를 소비한다는 것입니다. 사용자의 `startWithdraw()` 호출은 실행되지만 나중에 OOG로 인해 `finishWithdraw()`를 호출할 수 없는 시나리오가 있을 수 있습니다. 사용자가 지정하는 볼트 목록에는 중복 항목이 있을 수도 있으므로 목록에 `MAX_VAULTS_PER_STAKER`보다 더 많은 항목이 있을 수 있습니다.

# [L-02] 인출이 일시 중지될 수 있으므로 중앙화 위험 (Centralization risk as the withdrawals can be paused)

사용자는 자금을 인출하기 위해 `startWithdraw()` 및 `finishWithdraw()`를 호출해야 하지만, 문제는 계약이 일시 중지되면 해당 함수가 되돌려지므로 프로토콜 관리자가 손상된 경우 사용자가 자금에 액세스하여 인출할 수 없는 중앙화 위험이 있다는 것입니다.

# [L-03] 계약 Vault가 `maxMint()` 함수를 재정의하지 않음 (Contract Vault doesn't override the `maxMint()` function)

Vault 계약에는 Vault가 해당 금액 이상의 자산을 받지 않도록 하는 `assetLimit`이 있습니다. `deposit()` 함수는 이 제한을 확인하지만 `mint()` 함수에는 그러한 확인이 없어 `mint()` 함수를 사용하여 해당 제한을 우회할 수 있습니다.

또한 코드는 `maxMint()` 함수를 재정의하지 않으며 Vault 코드에 `maxDeposit()` 함수가 존재하고 `assetLimit`에 따라 올바른 값을 반환하지만 `maxMint()` 함수는 Vault에 존재하지 않으며 Solady 라이브러리에 설정된 `uint(256).max`를 반환합니다. `maxMint()` 및 `maxDeposit()`이 반환하는 값이 일치하지 않으므로 ERC4626 표준에 위배됩니다.

`maxMint()` 함수를 추가하고 `mint()` 함수에도 `assetLimit` 확인을 추가하십시오.
