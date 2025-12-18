# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**ethena-labs/ethena** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# StakedENA 소개 (About StakedENA)

`StakedENA` 계약은 사용자가 ENA 토큰을 스테이킹하고, 가득(vesting) 기간에 따라 보상 분배를 관리하며, 쿨다운 기간 및 블랙리스트 관리와 같은 기능을 포함할 수 있도록 합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
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

**_검토 커밋 해시_ - [a4afb516085b2870ff66f77c0cb7c77c0d0cf75f](https://github.com/ethena-labs/ethena/tree/a4afb516085b2870ff66f77c0cb7c77c0d0cf75f)**

**_수정 검토 커밋 해시_ - [a8cd4217edb234a30f13cf983458b997bd0fae24](https://github.com/ethena-labs/ethena/tree/a8cd4217edb234a30f13cf983458b997bd0fae24)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `StakedENA`
- `DeployStakedENA`

# 발견 사항 (Findings)

# [L-01] 배포 중 올바른 `_INITIAL_REWARDER` 주소를 즉시 설정하지 않음

`DeployStakedEna` 배포 스크립트에서 구성된 `_INITIAL_REWARDER`가 유효한 주소가 아니므로, 실제 보상자가 배포된 `StakedENA`에 보상을 추가할 수 있도록 `REWARDER_ROLE`을 부여하는 추가 단계가 필요합니다.

```solidity
contract DeployStakedEna is Script, DeploymentUtils {
  struct StakedENADeploymentAddresses {
    address proxyAddress;
    address stakedEnaImplementation;
    address admin;
    address proxyAdminAddress;
  }

      // We intentionally want our protocol multisig to control upgrades, as well as admin functionality on the contract
      // It is well protected with distributed hardware keys and various other security measures
      // Eventually, a timelock on upgrades may be added but this is out of scope for now in the interest of time, and
      // because governance is not yet fully decentralized
      address constant _DESIRED_PROXY_ADMIN_AND_STAKED_ENA_OWNER = address(0x3B0AAf6e6fCd4a7cEEf8c92C32DFeA9E64dC1862);
      // @audit - should just put correct rewarder here
>>>   address constant _INITIAL_REWARDER = address(0x0000000000000000000000000000000000000002);
      address constant _ENA_ADDRESS = address(0x57e114B691Db790C35207b2e685D4A43181e6061);

      ProxyAdmin proxyAdminContract;

      function run() public virtual {
        uint256 deployerPrivateKey = vm.envUint("STAKED_ENA_DEPLOYER_PRIVATE_KEY");
        deployment(deployerPrivateKey);
      }
      // ...
}
```

배포 프로세스 후 불필요한 추가 단계를 제거하기 위해 `_INITIAL_REWARDER`로 올바른 주소를 설정하는 것을 고려하십시오.

# [L-02] `_withdraw` 내부에서 `caller`가 블랙리스트인지 불필요한 확인

`_withdraw`가 트리거되면 `caller`가 블랙리스트인지 확인합니다.

```solidity
  function _withdraw(address caller, address receiver, address _owner, uint256 assets, uint256 shares)
    internal
    override
    nonReentrant
    notZero(assets)
    notZero(shares)
  {
    // @audit - caller check is unnecessary
>>> if (hasRole(BLACKLISTED_ROLE, _owner) || hasRole(BLACKLISTED_ROLE, caller) || hasRole(BLACKLISTED_ROLE, receiver)) {
      revert OperationNotAllowed();
    }

    super._withdraw(caller, receiver, _owner, assets, shares);
    _checkMinShares();
  }
```

그러나 `_withdraw`는 결국 `_burn`을 트리거하고, 이는 `_beforeTokenTransfer`를 호출하여 `msg.sender` 또는 `caller` 블랙리스트 확인도 수행됩니다.

```solidity
  function _beforeTokenTransfer(address from, address to, uint256) internal virtual override {
>>> if (hasRole(BLACKLISTED_ROLE, msg.sender)) {
      revert OperationNotAllowed();
    }
    if (hasRole(BLACKLISTED_ROLE, from) && to != address(0)) {
      revert OperationNotAllowed();
    }
    if (hasRole(BLACKLISTED_ROLE, to)) {
      revert OperationNotAllowed();
    }
  }
```

`_withdraw` 내부의 불필요한 `caller` 블랙리스트 확인을 제거하는 것을 고려하십시오.

# [L-03] `redistributeLockedAmount`는 `from` 잔액이 0이 아닌지 확인하지 않음

`StakedENA` 함수들, 특히 `_deposit` 및 `transferInRewards`에서는 제공된 금액이 0이 아닌지 확인하는 검사가 항상 있습니다. 그러나 `redistributeLockedAmount`에는 이 검사가 누락되어 있습니다. `redistributeLockedAmount`가 0의 `from` 잔액으로 호출되면 `vestingAmount`를 0으로 설정하고 `to` 주소가 0 주소일 때 `lastDistributionTimestamp`를 업데이트합니다. `to` 주소가 0이 아니면 해당 주소로 0주(share)를 발행합니다.

```solidity
  function redistributeLockedAmount(address from, address to) external nonReentrant onlyRole(DEFAULT_ADMIN_ROLE) {
    if (!hasRole(BLACKLISTED_ROLE, from) || hasRole(BLACKLISTED_ROLE, to)) revert OperationNotAllowed();
   // @audit - no check if amountToDistribute is non-zero
    uint256 amountToDistribute = balanceOf(from);
    uint256 enaToVest = previewRedeem(amountToDistribute);
    _burn(from, amountToDistribute);
    // to address of address(0) enables burning
    if (to == address(0)) {
      _updateVestingAmount(enaToVest);
    } else {
      _mint(to, amountToDistribute);
    }

    emit StakedENARedistributed(from, to, amountToDistribute);
  }
```

다른 함수의 안전장치와 일치하도록 0이 아닌지 확인하는 검사를 추가하는 것을 고려하십시오.

# [L-04] `initialize`가 `__ReentrancyGuard_init`를 트리거하지 않음

`StakedENA`는 `ReentrancyGuardUpgradeable`을 사용하지만 `__ReentrancyGuard_init`를 트리거하지 않습니다. 이는 재진입 보호에 영향을 미치지 않지만, 업그레이드 가능한 계약에 대해 초기화 프로그램을 트리거하는 것이 모범 사례로 간주됩니다.

# [L-05] 블랙리스트 주식(shares)이 계속 보상을 받음

`redistributeLockedAmount` 함수는 블랙리스트 사용자의 주식을 소각하고 잠재적으로 `_updateVestingAmount`를 통해 새로운 가득 기간을 시작하여 처리합니다. 그러나 `_updateVestingAmount`는 가장 최근의 가득 기간이 종료된 후에만 호출될 수 있으므로 블랙리스트 사용자의 주식은 가득 기간이 끝날 때까지 계속 보상을 받습니다.

```solidity
  function redistributeLockedAmount(address from, address to) external nonReentrant onlyRole(DEFAULT_ADMIN_ROLE) {
    if (!hasRole(BLACKLISTED_ROLE, from) || hasRole(BLACKLISTED_ROLE, to)) revert OperationNotAllowed();
    uint256 amountToDistribute = balanceOf(from);
    uint256 enaToVest = previewRedeem(amountToDistribute);
    _burn(from, amountToDistribute);
    // to address of address(0) enables burning
    if (to == address(0)) {
      _updateVestingAmount(enaToVest);
    } else {
      _mint(to, amountToDistribute);
    }

    emit StakedENARedistributed(from, to, amountToDistribute);
  }
```

블랙리스트 사용자의 자산은 결국 향후 기간에 재분배되지만, 블랙리스트 지정 즉시 주식을 소각하고 보상 발생을 중단할 수 없으므로 현재 가득 기간 동안 합법적인 사용자가 얻을 수 있는 잠재적 보상이 줄어듭니다.

가능한 대안은 주식을 즉시 소각하고 보관 계약으로 이동하는 것입니다. 이렇게 하면 블랙리스트 주식이 보상을 받는 것을 방지하고 가득 기간 동안 합법적인 사용자를 위해 보상 풀이 보존되도록 할 수 있습니다.

# [L-06] sENA 허가(permit) 서명을 마감일 전에 취소할 수 없음

sENA 허가 서명은 서명자에게 EIP-712 서명을 생성할 수 있는 옵션을 제공합니다. 이 서명에 서명한 후 서명자가 이를 취소하고 싶어도 할 수 없습니다. 이는 계약이 논스를 증가시키는 함수가 존재하지 않고 [`_useNonce`](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/2d081f24cac1a867f6f73d512f2022e1fa987854/contracts/token/ERC20/extensions/ERC20PermitUpgradeable.sol#L97) 함수가 internal로 표시된 OpenZeppelin의 ERC20PermitUpgradeable.sol을 기반으로 하기 때문입니다.

서명자가 논스를 사용하여 서명을 취소할 수 있도록 직접 사용할 수 있는 공개 함수를 도입하는 것을 권장합니다.

# [L-07] 쿨다운 요청을 취소/줄일 방법이 없음

자산 및 주식의 쿨다운은 시간과 자산 모두에서 증가만 가능합니다. 사용자가 쿨다운에 대해 마음을 바꾸면 이 요청을 취소할 방법이 없습니다.

```solidity
  function cooldownAssets(uint256 assets) external ensureCooldownOn returns (uint256 shares) {
//...
    cooldowns[msg.sender].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
    cooldowns[msg.sender].underlyingAmount += uint152(assets);
//...
  }

  function cooldownShares(uint256 shares) external ensureCooldownOn returns (uint256 assets) {
//...
    cooldowns[msg.sender].underlyingAmount += uint152(assets);
//...
  }
```

사용자가 선택한 것에 따라 `cooldowns.underlyingAmount`를 늘리거나 줄이는 bool 매개변수 등을 통해 이를 수행할 수 있는 방법을 도입하는 것을 권장합니다.
