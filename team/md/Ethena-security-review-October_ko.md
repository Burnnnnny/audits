# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**ethena-labs/ethena-ustb-audit** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Ethena 소개 (About Ethena)

Ethena USDB 스마트 계약은 엄격한 제한과 역할 기반 액세스 제어를 통해 토큰화된 자산을 담보로 하는 스테이블코인을 발행(minting)하고 상환(redeeming)할 수 있도록 합니다.

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

**_검토 커밋 해시_ - [ae1856b5f23db2436ffc811f648194328bbbe58c](https://github.com/ethena-labs/ethena-ustb-audit/tree/ae1856b5f23db2436ffc811f648194328bbbe58c)**

**_수정 검토 커밋 해시_ - [c2256464c586b984cf97a0e9e41c9fcd4a3ef554](https://github.com/ethena-labs/ethena-ustb-audit/tree/c2256464c586b984cf97a0e9e41c9fcd4a3ef554)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `SingleAdminAccessControl`
- `SingleAdminAccesscontrolUpgradeable`
- `UStb`
- `UStbMinting`

# 발견 사항 (Findings)

# [L-01] 일부 변수가 사용되지 않음

UStbMinting.sol에 그러한 변수가 2개 있습니다:

```solidity
  bytes32 private constant EIP712_DOMAIN_TYPEHASH = keccak256(abi.encodePacked(EIP712_DOMAIN));
  bytes32 private constant ROUTE_TYPE = keccak256("Route(address[] addresses,uint128[] ratios)");
```

제거하거나 누락된 기능을 추가하십시오.

# [L-02] `WHITELIST_ENABLED` 상태에서 블랙리스트에 오른 토큰을 전송할 수 있음

`FULLY_ENABLED`에서는 블랙리스트에 오른 토큰의 전송을 허용하지 않습니다. 그러나 `WHITELIST_ENABLED` 중에는 블랙리스트 로직이 누락되었습니다:

```solidity
  function _beforeTokenTransfer(address from, address to, uint256) internal virtual override {
    // 상태 2 - 블랙리스트 주소를 제외하고 전송이 완전히 활성화됨
    if (transferState == TransferState.FULLY_ENABLED) {
      if (hasRole(BLACKLISTED_ROLE, msg.sender) || hasRole(BLACKLISTED_ROLE, to)){
          revert OperationNotAllowed();
      }
      if (hasRole(BLACKLISTED_ROLE, from) && to != address(0)) {
        revert OperationNotAllowed();
      }
    // 상태 1 - 화이트리스트 주소 간에만 전송 가능
    } else if (transferState == TransferState.WHITELIST_ENABLED) {
      if (!hasRole(WHITELISTED_ROLE, msg.sender) || !hasRole(WHITELISTED_ROLE, to)){
        revert OperationNotAllowed();
      }
    // 상태 0 - 전송 완전히 비활성화됨
    } else if (transferState == TransferState.FULLY_DISABLED) {
      revert OperationNotAllowed();
    }
  }
```

화이트리스트에 있는 주소는 블랙리스트에 있는 소유자로부터 토큰을 전송할 수 있습니다. `WHITELIST_ENABLED` 상태에 추가 검사를 추가하십시오:

```diff
    } else if (transferState == TransferState.WHITELIST_ENABLED) {
      if (!hasRole(WHITELISTED_ROLE, msg.sender) || !hasRole(WHITELISTED_ROLE, to)){
        revert OperationNotAllowed();
      }
+     if (hasRole(BLACKLISTED_ROLE, from) && to != address(0)) {
+       revert OperationNotAllowed();
+     }
```

# [L-03] `ORDER_TYPE` 계산에 잘못된 변수 유형 사용

여기에서 `uint128 expiry,uint120 nonce`를 볼 수 있습니다:

```solidity
  bytes32 private constant ORDER_TYPE = keccak256(
    "Order(string order_id,uint8 order_type,uint128 expiry,uint120 nonce,address benefactor,address beneficiary,address collateral_asset,uint128 collateral_amount,uint128 ustb_amount)"
  );
```

그러나 실제 유형은 약간 다릅니다:

```solidity
  struct Order {
    string order_id;
    OrderType order_type;
@>  uint120 expiry;
@>  uint128 nonce;
    ...
  }
```

EIP712를 그대로 사용하면 잘못된 서명이 생성되지만 쉽게 완화할 수 있습니다. 올바른 유형을 포함하도록 `ORDER_TYPE`을 업데이트하십시오.

# [L-04] `_beforeTokenTransfer()`는 WHITELISTED_ROLE이 BLACKLISTED_ROLE을 가지고 있지 않은지 확인해야 함

주소는 UStb.sol에서 화이트리스트 및 블랙리스트 역할을 모두 가질 수 있습니다.

`BLACKLIST_MANAGER_ROLE`과 `WHITELIST_MANAGER_ROLE`이 동일한 사람이 아닌 시나리오를 고려하십시오. 화이트리스트에 있는 주소가 악의적으로 변하고 화이트리스트 관리자 역할을 사용하여 화이트리스트를 제거할 수 없는 경우, 블랙리스트 관리자는 여전히 해당 주소를 블랙리스트에 올릴 수 있습니다.

`transferState == TransferState.WHITELIST_ENABLED`에서 `WHITELISTED_ROLE`이 `BLACKLISTED_ROLE`도 가지고 있지 않은지 확인하십시오.

```
 // 상태 1 - 화이트리스트 주소 간에만 전송 가능
    } else if (transferState == TransferState.WHITELIST_ENABLED) {
+     if (!hasRole(WHITELISTED_ROLE, msg.sender) || !hasRole(WHITELISTED_ROLE, to) || hasRole(BLACKLISTED_ROLE, msg.sender) || hasRole(BLACKLISTED_ROLE, to)){
        revert OperationNotAllowed();
      }
```

# [L-05] 함수는 tokenAsset이 활성 상태인지 확인해야 함

`setMaxMintPerBlock()`은 DEFAULT_ADMIN_ROLE에 의해 호출되어 블록당 자산의 새 최대 발행량을 설정합니다. 이 함수에는 `tokenConfig[asset].isActive`를 확인하는 것과 같은 추가 확인이 있어야 합니다.

```
function setMaxMintPerBlock(uint128 _maxMintPerBlock, address asset) external onlyRole(DEFAULT_ADMIN_ROLE) {
    _setMaxMintPerBlock(_maxMintPerBlock, asset);
  }

  function _setMaxMintPerBlock(uint128 _maxMintPerBlock, address asset) internal {
    //@audit - like other functions, should check `tokenConfig[asset].isActive`
    uint128 oldMaxMintPerBlock = tokenConfig[asset].maxMintPerBlock;
    tokenConfig[asset].maxMintPerBlock = _maxMintPerBlock;
    emit MaxMintPerBlockChanged(oldMaxMintPerBlock, _maxMintPerBlock, asset);
  }
```

다른 함수에서도 수행되므로 0 금액 확인을 추가하는 것도 좋습니다.

# [L-06] 블랙리스트 사용자는 승인(approvals)을 통해 제한을 우회할 수 있음

UStb 토큰은 발행 및 소각 기능과 다음과 같은 여러 전송 상태를 포함하는 업그레이드 가능한 ERC20 계약입니다:

- FULLY_DISABLED
- WHITELIST_ENABLED
- FULLY_ENABLED

문서에 따르면 블랙리스트 주소는 토큰을 보내거나 받을 수 없도록 제한되어야 합니다:

> "어떤 경우에도 블랙리스트 주소는 토큰을 보내거나 받을 수 없습니다."

그러나 OpenZeppelin ERC20 계약은 승인된 주소가 다른 주소를 대신하여 토큰을 전송할 수 있도록 허용합니다. 이는 허점을 만듭니다. `_beforeTokenTransfer()` 함수가 `from` 주소의 블랙리스트 여부를 확인하지 않으므로 화이트리스트가 활성화된 경우 블랙리스트 사용자는 여전히 토큰을 전송할 수 있습니다.

다음은 문제를 보여주는 코드로 된 PoC입니다:

```solidity
    function testBypassBlacklistRole() public {
        address user1 = makeAddr("user1");
        address user2 = makeAddr("user2");

        vm.startPrank(newOwner);
        UStbContract.updateTransferState(IUStbDefinitions.TransferState.WHITELIST_ENABLED);
        UStbContract.grantRole(WHITELISTED_ROLE, user1);
        UStbContract.grantRole(WHITELISTED_ROLE, user2);
        vm.stopPrank();

        vm.prank(minter_contract);
        UStbContract.mint(user1, _amount);

        vm.prank(newOwner);
        UStbContract.revokeRole(WHITELISTED_ROLE, user1);

        vm.prank(newOwner);
        UStbContract.grantRole(BLACKLISTED_ROLE, user1);

        vm.prank(user1);
        UStbContract.approve(user2, _amount);

        vm.prank(user2);
        UStbContract.transferFrom(user1, user2, _amount);

        assert((UStbContract.balanceOf(user2)) == _amount);
    }
```

테스트 설정:

- `UStbTest`에 테스트 통합
- 실행: **`forge test --mc UStbTest --mt testBypassBlacklist`**

블랙리스트 주소에서의 전송을 차단하기 위해 `_beforeTokenTransfer()` 함수에 블랙리스트 확인을 추가하면 관리자가 `redistributeLockedAmount()`를 호출하지 못하게 되는 등의 문제가 발생할 수 있습니다. 더 나은 접근 방식은 블랙리스트 사용자가 승인(approve) 함수를 사용하지 못하도록 차단하는 것입니다. 다음과 같은 확인을 추가하여 이를 달성할 수 있습니다:

```solidity
  function _approve(address owner, address spender, uint256 value) internal virtual override {
    if (hasRole(BLACKLISTED_ROLE, owner)) {
      revert OperationNotAllowed();
    }
    super._approve(owner, spender, value);
  }
```

# [L-07] 블랙리스트 사용자는 `redistributeLockedAmount`를 프론트런하여 토큰을 소각할 수 있음

UStb 토큰 계약에는 특정 주소가 토큰을 전송하는 것을 제한하는 블랙리스트 메커니즘이 포함되어 있습니다. 또한 관리자가 블랙리스트 주소에서 블랙리스트가 아닌 주소로 토큰을 강제로 전송할 수 있는 관리자 기능을 제공합니다.

그러나 블랙리스트 사용자가 `redistributeLockedAmount` 함수를 프론트런하고 자신의 토큰을 소각할 수 있는 취약점이 있습니다. 이를 통해 관리자가 토큰을 다른 주소로 재분배하는 것을 막을 수 있습니다. 그들은 관리자의 재분배가 이루어지기 전에 버너(burner) 계정이 토큰을 사용하고 소각하도록 승인함으로써 이를 달성합니다.

다음은 문제를 보여주는 코드로 된 PoC입니다:

```solidity
    function testFrontrunRedistributeLockedAmount() public {
        address alex = makeAddr("Alex");
        address alexBurner = makeAddr("AlexBurner");

        vm.prank(minter_contract);
        UStbContract.mint(alex, _amount);

        vm.startPrank(newOwner);
        UStbContract.updateTransferState(IUStbDefinitions.TransferState.FULLY_ENABLED);
        UStbContract.grantRole(BLACKLISTED_ROLE, alex);
        vm.stopPrank();

        // 관리자가 alex 토큰 재분배 시도
        // Alex(블랙리스트 사용자)가 자신의 버너 계정이 토큰을 사용하도록 승인
        // alexBurner가 burnFrom을 호출하여 Alex 토큰 소각

        vm.prank(alex);
        UStbContract.approve(alexBurner, _amount);

        vm.prank(alexBurner);
        UStbContract.burnFrom(alex, _amount);

        uint256 balanceBef = UStbContract.balanceOf(newOwner);

        vm.prank(newOwner);
        UStbContract.redistributeLockedAmount(alex, newOwner);

        uint256 balanceAft = UStbContract.balanceOf(newOwner);

        // 모두 소각되었으므로 newOwner는 0 토큰을 받음
        assert(balanceBef == balanceAft);
    }
```

- `UStbTest`에 테스트 통합
- 실행: **`forge test --mc UStbTest --mt testFrontrunRedistributeLockedAmount`**

이 문제를 완화하려면 블랙리스트 사용자가 자신의 토큰을 소각하는 것을 방지하기 위해 `burnFrom()` 함수에 액세스 제어를 추가하는 것을 고려하십시오.
