# 정보 (About Pashov Audit Group)

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이 분야에서 입증된 실력을 갖추고 있습니다. 대부분은 공개 콘테스트 보상으로 $100k 이상을 획득했거나, 여러 번의 우승 경력이 있거나, 우리와의 감사에서 탁월한 성과를 보였습니다. 우리는 검증되고 동기 부여된 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도왔으며, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

<p><strong>euler-xyz/euler-earn</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, 이 기간 동안 Pashov Audit Group은 <strong>Euler Earn</strong> 검토에 참여했습니다. 총 <strong>14</strong>개의 문제가 발견되었습니다.</p>

# Euler Earn 소개 (About Euler Earn)

<p>Euler Earn은 MetaMorpho의 포크로, 최대 30개의 승인된 ERC-4626 전략에 예금을 할당하며, 제로 쉐어(zero-share) 보호, 주식 인플레이션을 방지하기 위한 내부 잔액 추적, skim, ERC-2612 permit 및 multicall 기능 제거와 같은 변경 사항이 있습니다. EulerEarn의 사용자는 자신의 포지션 위험을 적극적으로 관리할 필요 없이 대출 이자를 얻고자 하는 유동성 제공자입니다. 입금 및 출금은 할당자(allocator)가 정의한 대기열을 따르며, 즉각적인 가용성을 위해 비대출 금고에 자금을 보관할 수 있는 옵션이 있습니다.</p>

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [12c0220226cdefc8fee2f229c5e75bd656c7b2b5](https://github.com/euler-xyz/euler-earn/tree/12c0220226cdefc8fee2f229c5e75bd656c7b2b5)<br>&nbsp;&nbsp;(euler-xyz/euler-earn)

**수정 검토 커밋 해시:**<br>• [b95775f4235fe4ea738dde226524d25f317d987c](https://github.com/euler-xyz/euler-earn/tree/b95775f4235fe4ea738dde226524d25f317d987c)<br>&nbsp;&nbsp;(euler-xyz/euler-earn)

# 범위 (Scope)

- `EulerEarn.sol`
- `EulerEarnFactory.sol`
- `PublicAllocator.sol`
- `ConstantsLib.sol`
- `ErrorsLib.sol`
- `EventsLib.sol`
- `PendingLib.sol`
- `SafeERC20Permit2Lib.sol`
- `UtilsLib.sol`
- `interfaces/`

# 발견 사항 (Findings)

# [M-01] `PublicAllocator`와 `EulerEarn` 간의 주식(share) 추적 문제로 인해 재할당에 영향을 미침

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`PublicAllocator::reallocateTo()` 함수는 가용 자산을 계산하기 위해 `id.maxWithdraw(vault)`를 통해 실제 전략 주식 잔액을 사용하는 반면, `EulerEarn::reallocate()`는 실제 작업을 위해 `config[id].balance`를 통한 내부 주식 추적을 사용합니다. 이러한 불일치는 실제 주식 잔액이 `EulerEarn`의 내부 추적과 다를 때 심각한 문제를 일으킵니다(전략 주식을 금고로 직접 전송하는 경우 발생할 수 있음).

```solidity
  // PublicAllocator.sol -> 실제 전략 주식 사용
  uint256 assets = id.maxWithdraw(vault); // 전략 계약의 실제 주식
  allocations[i].assets = assets - withdrawnAssets; // 실제 주식을 기반으로 한 목표 할당
  
  // EulerEarn.sol -> 내부 주식 추적 사용
  uint256 supplyShares = config[id].balance; // 내부 추적
  uint256 supplyAssets = id.previewRedeem(supplyShares); // 내부 주식 기반
  uint256 withdrawn = supplyAssets.zeroFloorSub(allocation.assets); // 실제 출금 계산
```

아래 PoC에서 더 잘 보여지듯이, 이 취약점은 실제 작업이 다른 금액을 처리하는 동안 흐름 제한(flow caps)이 요청된 출금 금액에 따라 업데이트되도록 합니다. 그 외에도 `PublicAllocator`의 할당자는 특정 금액 `x`를 출금하려고 의도할 수 있지만 궁극적으로 출금된 금액은 **다를 것**입니다. 이 취약점이 존재하려면 전략 주식이 금고로 직접 전송되어야 합니다. 이 경우 `PublicAllocator`의 모든 `reallocate()` 호출이 오작동합니다.

이 취약점을 재현하려면 `PublicAllocatorTest.sol`에 다음 함수를 붙여넣고 `forge test --mt testRellocateToBug -vv`를 실행하십시오:

```solidity
    function testRellocateToBug() public {
        flowCaps.push(FlowCapsConfig(idleVault, FlowCaps(100e18, 100e18)));
        flowCaps.push(FlowCapsConfig(allMarkets[0], FlowCaps(100e18, 100e18)));
        vm.prank(OWNER);
        publicAllocator.setFlowCaps(address(vault), flowCaps);
        
        // PublicAllocator를 통한 초기 입금 (내부적으로 추적됨)
        withdrawals.push(Withdrawal(idleVault, 50e18));
        publicAllocator.reallocateTo(address(vault), withdrawals, allMarkets[0]);
        
        console.log("=== After normal deposit ===");
        uint256 internalShares1 = vault.config(allMarkets[0]).balance;
        uint256 realShares1 = allMarkets[0].balanceOf(address(vault));
        console.log("Internal shares:                   ", internalShares1);
        console.log("Real shares:                       ", realShares1);
        
        // 전략 주식을 직접 발행하여 금고로 전송
        // 이것이 내부 추적과 실제 잔액 간의 불일치를 생성합니다.
        loanToken.setBalance(address(this), 20e18);
        loanToken.approve(address(allMarkets[0]), 20e18);
        uint256 directShares = allMarkets[0].deposit(20e18, address(vault)); // 금고로 직접 주식 발행
        
        console.log("\n=== After direct share transfer ===");
        uint256 internalShares2 = vault.config(allMarkets[0]).balance;
        uint256 realShares2 = allMarkets[0].balanceOf(address(vault));
        uint256 realAssets = allMarkets[0].maxWithdraw(address(vault));
        uint256 internalAssets = allMarkets[0].previewRedeem(internalShares2);
        console.log("Internal shares:                   ", internalShares2);
        console.log("Real shares:                       ", realShares2);
        
        // 이 시점에서 주식 불일치가 발생하지만, EulerEarn이 주식을 내부적으로 추적하고 이것만이 계산의 진실 소스여야 하므로 문제가 되지 않는 것처럼 보입니다. 그러나 재할당을 수행하려고 하면 문제를 볼 수 있습니다.

        // 문제를 보여주기 위해 재할당 전 흐름 제한 기록
        FlowCaps memory withdrawFlowCapsBefore = publicAllocator.flowCaps(address(vault), allMarkets[0]);
        FlowCaps memory supplyFlowCapsBefore = publicAllocator.flowCaps(address(vault), idleVault);
        
        console.log("\n=== Flow caps BEFORE reallocation ===");
        console.log("Withdraw strategy maxIn:            ", withdrawFlowCapsBefore.maxIn);
        console.log("Withdraw strategy maxOut:          ", withdrawFlowCapsBefore.maxOut);
        console.log("Supply strategy maxIn:             ", supplyFlowCapsBefore.maxIn);
        console.log("Supply strategy maxOut:             ", supplyFlowCapsBefore.maxOut);

        delete withdrawals;
        uint256 requestedWithdraw = 30e18;
        withdrawals.push(Withdrawal(allMarkets[0], uint128(requestedWithdraw)));
        
        uint256 beforeAssets = allMarkets[0].maxWithdraw(address(vault));

        console.log("\n=== Reallocation ===");
        console.log("Requested withdrawal:              ", requestedWithdraw);
        publicAllocator.reallocateTo(address(vault), withdrawals, idleVault);
        
        uint256 afterAssets = allMarkets[0].maxWithdraw(address(vault));
        uint256 actualWithdrawn = beforeAssets - afterAssets;
        console.log("Actual withdrawn:                  ", actualWithdrawn);
        
        // 버그: 할당자는 `requestedWithdraw` 금액을 출금하고 입금하기를 원했지만, 결국 `actualWithdrawn`이 출금 및 입금되었습니다.
        assertNotEq(actualWithdrawn, requestedWithdraw);

        // 흐름 제한 손상 표시
        FlowCaps memory withdrawFlowCapsAfter = publicAllocator.flowCaps(address(vault), allMarkets[0]);
        FlowCaps memory supplyFlowCapsAfter = publicAllocator.flowCaps(address(vault), idleVault);
        
        console.log("\n=== Flow caps AFTER reallocation ===");
        console.log("Withdraw strategy maxIn:           ", withdrawFlowCapsAfter.maxIn);
        console.log("Withdraw strategy maxOut:          ", withdrawFlowCapsAfter.maxOut);
        console.log("Supply strategy maxIn:             ", supplyFlowCapsAfter.maxIn);
        console.log("Supply strategy maxOut:            ", supplyFlowCapsAfter.maxOut);
        
        console.log("\n=== Flow caps CHANGES (should match actual withdrawal, but matches requested) ===");
        console.log("Withdraw maxIn increase:           ", withdrawFlowCapsAfter.maxIn - withdrawFlowCapsBefore.maxIn);
        console.log("Withdraw maxOut decrease:          ", withdrawFlowCapsBefore.maxOut - withdrawFlowCapsAfter.maxOut);
        console.log("Supply maxIn decrease:             ", supplyFlowCapsBefore.maxIn - supplyFlowCapsAfter.maxIn);
        console.log("Supply maxOut increase:            ", supplyFlowCapsAfter.maxOut - supplyFlowCapsBefore.maxOut);

        uint256 flowCapChange = withdrawFlowCapsAfter.maxIn - withdrawFlowCapsBefore.maxIn;
        console.log("\n=== Bug Demonstration ===");
        console.log("Flow caps updated by:              ", flowCapChange);
        console.log("But actual operation was:          ", actualWithdrawn);
    }
```

## 권장 사항

이 문제를 완화하려면 `reallocateTo()`가 `EulerEarn::_expectedSupplyAssets()`를 사용하여 금고의 공급 자산을 계산해야 합니다. 그러나 이 함수는 internal이므로 공개적으로 노출하는 것을 고려하십시오. 또는 `EulerEarn`에서 `config[id].balance`를 검색하여 `id.maxWithdraw` 대신 사용하십시오.

옵션 1: `EulerEarn.sol`에서 내부 함수 노출
```solidity
// In EulerEarn.sol -> make internal function public
function expectedSupplyAssets(IERC4626 id) external view returns (uint256) {
    return _expectedSupplyAssets(id);
}

// In PublicAllocator.sol -> use internal tracking instead of real shares
uint256 assets = IEulerEarn(eulerEarn).expectedSupplyAssets(id);
```

옵션 2: `PublicAllocator.sol`에서 직접 설정 잔액 사용
```solidity
// In PublicAllocator.sol -> use internal balance
uint256 internalShares = IEulerEarn(eulerEarn).config(id).balance;
uint256 assets = id.previewRedeem(internalShares);
```

# [L-01] `createEulerEarn()`은 프론트러닝에 취약함

_인정됨 (Acknowledged)_

`EulerEarnFactory.createEulerEarn()` 함수는 사용자가 제공한 `salt`와 함께 `CREATE2` opcode를 사용하여 새 `EulerEarn` 금고를 배포합니다. 그러나 `salt`는 임의적이며 `msg.sender`에 연결되지 않으므로, 악의적인 행위자가 트랜잭션을 프론트런하여 동일한 주소에 계약을 먼저 배포하여 원래 배포자의 트랜잭션을 되돌리게(revert) 함으로써 괴롭힐 수 있습니다.

이는 공격자가 예상 계약 주소를 선점하여 합법적인 사용자가 금고를 배포하는 것을 방지할 수 있는 서비스 거부 벡터를 생성합니다.

```solidity
 function createEulerEarn(
        address initialOwner,
        uint256 initialTimelock,
        address asset,
        string memory name,
        string memory symbol,
        bytes32 salt
    ) external returns (IEulerEarn eulerEarn) {
        eulerEarn = IEulerEarn(
            address(
                new EulerEarn{salt: salt}(
                    initialOwner, address(evc), permit2Address, initialTimelock, asset, name, symbol
                )
            )
        );

        isVault[address(eulerEarn)] = true;

        vaultList.push(address(eulerEarn));

        emit EventsLib.CreateEulerEarn(
            address(eulerEarn), _msgSender(), initialOwner, initialTimelock, asset, name, symbol, salt
        );
    }
```

**권장 사항:**

제공된 `salt`와 호출자 주소를 모두 사용하여 salt를 파생하는 것을 고려하십시오.

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다.

# [L-02] `maxWithdraw()`는 `feeRecipient`에 대한 자산을 과소평가함

_인정됨 (Acknowledged)_

`EulerEarn.maxWithdraw()` 함수는 출금 호출을 통해 금고의 소유자 잔액에서 출금할 수 있는 기본 자산의 최대 금액을 반환하도록 설계되었습니다. 이 함수는 먼저 `_accruedFeeAndAssets()`를 통해 수수료를 발생시킨 다음 `balanceOf(owner)`를 호출하고 결과 주식을 자산으로 변환하여 출금 가능한 자산을 계산합니다.
그러나 `owner`가 `feeRecipient`인 경우 청구되지 않은 `feeShares`는 잔액에 반영되지 않아 출금할 수 있는 실제 금액이 과소평가됩니다.

이러한 불일치는 `feeShares`가 유효한 청구 가능 주식임에도 불구하고 별도로 추적되고 `balanceOf(feeRecipient)`에 포함되지 않기 때문에 발생합니다.

```solidity
   function _maxWithdraw(
        address owner
    )
        internal
        view
        returns (uint256 assets, uint256 newTotalSupply, uint256 newTotalAssets)
    {
        uint256 feeShares;
        (feeShares, newTotalAssets, ) = _accruedFeeAndAssets();
        newTotalSupply = totalSupply() + feeShares;

        assets = _convertToAssetsWithTotals(
            balanceOf(owner),
            newTotalSupply,
            newTotalAssets,
            Math.Rounding.Floor
        );
        assets -= _simulateWithdrawStrategy(assets);
    }
```

**권장 사항:**

자산으로 변환하는 데 사용되는 총 주식에 포함시켜 `owner == feeRecipient`일 때 `feeShares`를 고려하도록 로직을 업데이트하십시오.

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다.

참고로 balanceOf(feeRecipient)도 수수료를 계산하지 않으므로 과소 보고됩니다.

# [L-03] `permit2 == address(0)`일 때 `USDT` 승인을 취소하지 않음

_해결됨 (Resolved)_

`SafeERC20Permit2Lib` 라이브러리에서 `revokeApprovalWithPermit2()` 함수는 금고의 한도가 0으로 설정될 때(일반적으로 잘못된 동작이나 타협으로 인해 금고를 제거할 준비를 할 때) 토큰 허용량을 취소하는 역할을 합니다.

`permit2`가 `address(0)`일 때 함수는 다음과 같이 토큰 허용량을 취소하려고 시도합니다:

```solidity
    function revokeApprovalWithPermit2(IERC20 token, address spender, address permit2) internal {
        if (permit2 == address(0)) {
            try token.approve(spender, 1) {} catch {} //<@audit : allownace not revoked for USDT
        } else {
            IAllowanceTransfer(permit2).approve(address(token), spender, 0, 0);
        }
    }
```

이 대체 로직은 `USDT`와 같이 허용량을 0이 아닌 값으로 업데이트하기 전에 0으로 설정해야 하는 토큰에 대해 조용히 실패합니다. 결과적으로 지출자(제거될 전략 금고)는 여전히 활성 승인을 유지하며, 손상된 금고가 이 매달린(dangling) 허용량을 악용할 수 있으므로 잠재적인 보안 위험을 초래합니다.

권장 사항:
`forceApprove()`를 사용하여 허용량을 재설정하는 것을 고려하십시오:
```diff
    function revokeApprovalWithPermit2(IERC20 token, address spender, address permit2) internal {
        if (permit2 == address(0)) {
-           try token.approve(spender, 1) {} catch {}
+           try SafeERC20.forceApprove(token, spender, 1) {} catch {}
        } else {
            IAllowanceTransfer(permit2).approve(address(token), spender, 0, 0);
        }
    }
```

# [L-04] 슬리피지 및 마감일 보호 누락으로 인해 사용자가 가격 위험에 노출됨

_인정됨 (Acknowledged)_

`EulerEarn` 계약은 `deposit()`, `mint()`, `withdraw()`, `redeem()` 함수에서 사용자에게 슬리피지 보호 또는 마감일 강제를 제공하지 않습니다. 이러한 함수는 여러 ERC4626 호환 전략 금고와 상호 작용하며, 사용자 자산은 가용 용량에 따라 다른 전략 금고에 분산되거나 인출될 수 있습니다.

주요 우려 사항:

- **슬리피지 위험**: 입금이나 발행 중에 자산은 주가가 다른 여러 전략 금고에 분산될 수 있습니다. 출금이나 상환 중에는 전략 금고가 고정된 순서로 액세스되며, 유동성이나 주가가 변동하여 사용자가 예상보다 적은 주식이나 자산을 받을 수 있습니다.

- **마감일 강제 없음**: 트랜잭션이 멤풀(mempool)에 갇히면(예: 높은 네트워크 혼잡 중), 사용자는 실행 전에 더 큰 가격 변동에 노출되어 슬리피지 위험이 더욱 증가할 수 있습니다.

사용자 정의 제어가 부족하여 기본 금고의 타이밍 및 가격 변동으로 인해 예금자와 인출자가 불리한 결과에 노출됩니다.

권장 사항:
사용자가 거래에 대해 허용 가능한 최소 출력 및 시간 제한을 강제할 수 있도록 `minShares`(`maxShares`) 및 `minAssets`(`maxAssets`)와 같은 매개변수와 선택적 `deadline` 필드를 도입하십시오.

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다.

# [L-05] 금고 활성화 프로세스에서의 프론트러닝

_인정됨 (Acknowledged)_

큐레이터(curator)가 강제 제거된 금고(자금이 인출되지 않음)에 새 한도를 설정하거나 기존 자금이 있는 금고를 추가할 때, 시스템의 총 자산은 새 주식을 발행하지 않고 `_setCap()`을 통해 직접 증가합니다. 이는 사용자가 금고가 다시 추가되기 바로 전에 입금하여 더 큰 자산 지분을 획득하고 즉시 이익을 위해 인출함으로써 악용될 수 있습니다. 사용자는 금고 활성화를 프론트런하여 무위험 이익을 추출할 수 있습니다.

```solidity
// In _setCap function when enabling vault
marketConfig.balance = id.balanceOf(address(this)).toUint112();
// Direct asset increase without share dilution
_updateLastTotalAssets(lastTotalAssets + _expectedSupplyAssets(id));
```

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다. 동작은 원래 코드베이스와 동일합니다.

# [L-06] `setFlowCaps()`를 프론트런하여 효과적인 한도 사용 증가 가능

_인정됨 (Acknowledged)_

`PublicAllocator`의 `setFlowCaps()` 함수는 새로운 `FlowCaps` 값을 직접 설정합니다. 이를 통해 악의적인 행위자가 기존 한도 값을 완전히 사용하는 `reallocateTo()`를 제출하여 업데이트를 프론트런할 수 있습니다. 그런 다음 업데이트가 적용되면 행위자는 새 한도를 다시 사용할 수 있습니다.

```solidity
function setFlowCaps(address vault, FlowCapsConfig[] calldata config) external onlyAdminOrVaultOwner(vault) {
    for (uint256 i = 0; i < config.length; i++) {
        IERC4626 id = config[i].id;
        if (!IEulerEarn(vault).config(id).enabled && (config[i].caps.maxIn > 0 || config[i].caps.maxOut > 0)) {
            revert ErrorsLib.MarketNotEnabled(id);
        }
        if (config[i].caps.maxIn > MAX_SETTABLE_FLOW_CAP || config[i].caps.maxOut > MAX_SETTABLE_FLOW_CAP) {
            revert ErrorsLib.MaxSettableFlowCapExceeded();
        }
        flowCaps[vault][id] = config[i].caps;
    }

    emit EventsLib.SetFlowCaps(_msgSender(), vault, config);
}
```

예를 들어 다음 시나리오를 고려하십시오. 현재 `maxIn`은 280이고 관리자는 이를 250으로 줄이려고 합니다. 공격자는 280을 입금하는 `reallocateTo()`로 프론트런할 수 있습니다. 그런 다음 관리자의 `setFlowCaps()`는 상태를 다시 `maxIn = 250`으로 덮어씁니다. 이를 통해 전략에 250을 추가로 입금할 수 있습니다. 결과적으로 총 `280 + 250 = 530`이 효과적으로 유입됩니다(이는 관리자가 의도한 것보다 훨씬 많음).

이 문제를 해결하려면 흐름 제한을 직접 할당하지 말고 변경 사항(증가/감소)을 매개변수로 전달하는 것을 고려하십시오.

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다. 동작은 원래 코드베이스와 동일하며 호환성을 유지하는 것을 선호합니다.

# [L-07] 특권 역할이 유효하지 않은 EVC 하위 계정(sub-accounts)을 수락함

_인정됨 (Acknowledged)_

EulerEarn 금고는 인증을 위해 하위 계정을 지원하는 EVC(Ethereum Vault Connector)를 사용합니다. 따라서 EularEarn 금고는 이러한 하위 계정이 금고와 상호 작용하는 경우를 처리해야 합니다. 이러한 하위 계정은 금고 주식을 받고 전송할 수 있지만 개인 키를 알 수 없으므로 기본 자산 전송과는 호환되지 않습니다.

또한 하위 계정은 `onlyAdminOrVaultOwner`, `onlyCuratorRole`, `onlyAllocatorRole` 등과 같은 대부분의 역할 기반 수정자에 의해 접근 제어되는 특권 함수를 호출하는 것이 명시적으로 차단됩니다. 이러한 수정자는 다음에 의존합니다:

```solidity
_authenticateCallerWithStandardContextState(true);
```

인증된 호출자가 계정 소유자가 아닌 경우(즉, 하위 계정인 경우) 이 호출은 되돌려집니다.

그러나 특권 역할(예: `setAdmin`, `setCurator` 또는 `owner` 설정)을 할당할 때 유효성 검사가 수행되지 않습니다. 결과적으로 EVC 하위 계정에 특권 역할이 할당될 수 있지만 나중에 관련 함수를 호출할 수 없게 됩니다:

```solidity
function setAdmin(address vault, address newAdmin) external onlyAdminOrVaultOwner(vault) {
    if (admin[vault] == newAdmin) revert ErrorsLib.AlreadySet();
    admin[vault] = newAdmin;
}
```

흥미롭게도 하위 계정 금고 `소유자`는 여전히 `onlyOwner` 수정자에 의해 보호되는 함수를 호출할 수 있습니다. 해당 수정자는 `_msgSender()`를 사용하기 때문인데, 이는 추가 검증 없이 EVC에서 `onBehalfOfAccount`를 직접 검색합니다.

따라서 특권 역할을 확인하는 로직이 일관되지 않습니다. 하위 계정 금고 `소유자`는 `EulerEarn::setFee`( `onlyOwner`에 의해 보호됨)를 호출할 수 있지만 `PublicAllocator::setFee`( `onlyAdminOrVaultOwner`에 의해 보호됨)는 호출할 수 없습니다.

사용할 수 없는 특권 계정을 할당하지 않으려면 `_withdraw`에서 수행되는 것과 유사하게 특권 역할을 설정할 때 주소가 EVC 하위 계정이 아닌지 확인하는 추가 검사를 고려하십시오:

```solidity
    address evcOwner = evc.getAccountOwner(candidate);
    if (evcOwner != address(0) && evcOwner != candidate) {
        revert ErrorsLib.InvalidPrivilegedAccount();
    }
```

또한 금고 `소유자`의 능력에 관한 현재 불일치가 의도된 것이 아니라면, `onlyOwner` 수정자 로직을 재정의하여 `_authenticateCallerWithStandardContextState(true)` 함수를 사용하여 하위 계정이 이러한 함수를 호출하는 것을 명시적으로 차단하는 것을 고려하십시오.

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다. 역할 부여 실수는 하위 계정뿐만 아니라 모든 주소에서 발생할 수 있습니다.

# [L-08] 엣지 케이스로 인해 소유자가 사용자로부터 자산을 은밀하게 훔칠 수 있음

_해결됨 (Resolved)_

**참고**:
현재 `EulerEarn`과 호환되는 초기 지원 전략 금고는 `EulerEarn` 금고 및 EVK 금고로 제한되므로 이 공격은 불가능합니다. 이 악용이 발생할 수 있도록 허용하는 전략 금고는 EVK 금고와 동일한 기능/로직을 가질 수 있지만, 일반 EVK 금고에서 이 악용을 차단하는 읽기 전용 재진입 보호(뷰 함수에 `nonReentrantView` 수정자 사용)가 *없을* 것입니다. 이 보고서는 이러한 금고가 향후 잠재적으로 지원될 수 있다고 가정합니다.

이 악용의 또 다른 주요 전제 조건은 곧 악의적일 전략 금고가 `perspective` 계약에 의해 검증된 전략으로 간주된다는 것입니다. 이 시나리오에서 금고는 무해해 보일 수 있지만 악의적인 후크 계약을 숨겨야 합니다(후크를 업그레이드 가능하게 만들거나 공격 직전에 악의적인 후크를 직접 설정할 수 있음). 이 전략 금고는 사용자 자산을 처리할 수 있는 권한이 *절대* 부여되지 않았을 수 있으며 실제 악용 전까지는 `EulerEarn` 금고에 악의적으로 보일 필요가 없습니다. 이는 1개의 트랜잭션으로 실행될 수 있으므로 사용자 자금 손실로 이어질 수 있는 실행 가능한 롱테일 공격이 됩니다.

위의 전제 조건이 충족되면 다음과 같은 이유로 이 악용이 가능합니다:
- `setFee` 및 `setFeeRecipient`에는 재진입 보호가 없습니다.
- `_withdraw`는 외부 전략 상호 작용 전에 `lastTotalAssets`를 낙관적으로 업데이트합니다.

이 공격을 위한 설정은 은밀하게 수행될 수 있으며 다음과 같은 이유로 사용자가 감지하지 못할 수 있습니다:
- `malVault`(악의적인 금고)는 거의 항상 0 한도로 설정됩니다(즉시 0으로 재설정됨).
- `malVault`는 공급 대기열에 절대 추가되지 않습니다.
- `malVault`는 출금 대기열의 끝에 있으며 일반 사용자는 절대 상호 작용하지 않습니다.

**취약점 세부 정보**

`feeRecipient`가 `EulerEarn::withdraw`를 호출한다고 가정해 보겠습니다. `_withdraw` 함수는 전략 금고와 상호 작용하기 전에 잠재적으로 유효하지 않은 값으로 `lastTotalAssets` 저장 변수를 낙관적으로 업데이트합니다:

```solidity
        _updateLastTotalAssets(lastTotalAssets.zeroFloorSub(assets)); // @audit: optimistically update state as if caller can withdraw all `assets` specified

        _withdrawStrategy(assets); // @audit: then interact with external contracts

        super._withdraw(caller, receiver, owner, assets, shares);
```

`lastTotalAssets`를 업데이트한 후 `EulerEarn`은 전략 금고에서 `withdraw`를 호출합니다:

```solidity
                try id.withdraw(toWithdraw, address(this), address(this)) returns (uint256 withdrawnShares) {
```

전략 금고가 후크를 지원하는 경우 악의적인 후크는 `EulerEarn`에 다시 진입하여 `setFee` 함수를 호출할 수 있습니다(후크는 먼저 소유권을 가정해야 함). `setFee`는 `_accrueInterest`를 호출하여 신뢰할 수 있는 전략이 보유한 총 자산에서 조기에 업데이트된 `lastTotalAssets`를 뺀 값으로 발생 이자를 계산합니다:

```solidity
        uint256 realTotalAssets;
        for (uint256 i; i < withdrawQueue.length; ++i) {
            IERC4626 id = withdrawQueue[i];
            realTotalAssets += _expectedSupplyAssets(id);
        }

        uint256 lastTotalAssetsCached = lastTotalAssets;
        if (realTotalAssets < lastTotalAssetsCached - lostAssets) {
            // 금고가 일부 자산을 잃은 경우(realTotalAssets 감소), lostAssets가 증가합니다.
            newLostAssets = lastTotalAssetsCached - realTotalAssets;
        } else {
            // 그렇지 않은 경우 lostAssets는 동일하게 유지됩니다.
            newLostAssets = lostAssets;
        }

        newTotalAssets = realTotalAssets + newLostAssets;
        uint256 totalInterest = newTotalAssets - lastTotalAssetsCached;
```

자산이 아직 전략을 떠나지 않았으므로 이 이자는 부풀려질 것이며, 결과적으로 수수료 주식이 `feeRecipient`(현재 `EulerEarn::withdraw` 함수의 호출자)에게 발행됩니다. 이를 통해 `feeRecipient`는 `x' + y'` 자산(`x + y` 주식에 해당)의 출금을 지정하여 `EulerEarn`에서 초과 자산을 인출할 수 있습니다. 트랜잭션 시작 시 그들은 `x` 주식만 가지고 있었을 수 있으며, 이는 `x'` 자산에 대한 청구권이 있지만 중간 실행에서 이자가 발생하기 때문에 `feeRecipient`는 트랜잭션 종료 전에 `x + y`(`y` == 발행된 수수료 주식) 주식을 갖게 되며, 이는 `x' + y'` 자산에 대한 청구권을 제공합니다.

첨부된 POC는 `feeRecipient`가 이 악용을 활용하여 `EulerEarn` 금고를 고갈시키는 방법을 보여줍니다.

**악용 단계 (코드 단계 관찰을 위해 개념 증명으로 건너뛸 수 있음)**
악의적인 소유자가 악용을 설정하기 위해 수행할 수 있는 단계는 다음과 같습니다:

0. EulerEarn 배포 시 초기 타임락을 0으로 설정하여 전략을 즉시 구성합니다.
1. 소유자는 EulerEarn을 대신하여 `malVault`(1 wei 자산)에 공급하여 EulerEarn에 대한 주식을 발행합니다.
2. 신뢰할 수 있는 금고에 대한 유효한 한도를 설정하고 `malVault`에 대해 `1 wei`의 초기 한도를 설정합니다(`malVault`는 출금 대기열 끝에 추가됨).
    - `malVault`는 1단계에서 대신 발행된 주식으로 인해 `> 0` 내부 잔액이 구성됩니다:

```solidity
                marketConfig.enabled = true;
                marketConfig.balance = id.balanceOf(address(this)).toUint112();
```

3. 신뢰할 수 있는 전략을 공급 대기열에 추가합니다.
4. 즉시 `malVault`의 한도를 0으로 설정합니다.

이 시점에서 악용이 준비되었으며, 이제 소유자는 정상적인 작업(즉, 0이 아닌 타임락 설정 및 입금된 자산을 신뢰할 수 있는 금고에 할당)을 수행할 수 있습니다. `malVault`는 이제 출금 대기열의 끝에 있고 한도가 0이며, 소유자가 악용을 시작하기 위해 금고에서 관리하는 자산이 충분하다고 결정할 때까지 휴면 상태로 유지됩니다. 악용이 시작되기 전에 `malVault`의 후크 계약이 악의적인 로직을 포함하도록 업그레이드됩니다.

나머지 악용은 1개의 트랜잭션으로 실행될 수 있습니다:
1. 소유자는 자신을 `feeRecipient`로 설정하고 `EulerEarn` 금고에 대한 최대 수수료를 설정합니다.
2. 소유자는 수수료 수령인으로서 자산을 EulerEarn에 입금합니다.
3. 소유자는 소유권을 `malVault`의 후크로 이전합니다(2단계 이전이므로 `malVault` 후크는 이제 보류 중인 소유자임).
4. 소유자는 `updateWithdrawQueue`를 호출하여 `malVault`를 대기열의 맨 앞으로 이동합니다.
5. 소유자는 `EulerEarn::withdraw`를 호출하고 `EulerEarn` 금고의 모든 자산을 지정합니다. 이는 악의적인 후크를 트리거하며, 먼저 `EulerEarn::acceptOwnership`을 호출한 다음 `EulerEarn::setFee(0)`을 호출하여 이자 발생을 트리거합니다.

**개념 증명 (Proof Of Concept)**

먼저, 이 보호를 제공하지 않는 전략 금고를 시뮬레이션하기 위해 `lib/euler-vault-kit/src/EVault/shared/Base.sol`에서 `nonReentrantView` 수정자에 대한 로직을 제거합니다(`EulerEarn`의 이자 발생은 `strategyVault::previewRedeem`을 호출하며, 이는 일반 EVK 금고에 대해 읽기 전용 재진입을 트리거함).

둘째, 다음 파일을 테스트 모음에 추가합니다:

(코드 생략)

**권장 사항**

이 엣지 케이스로부터 보호하기 위해 `setFee` 및 `setFeeRecipient`에 `nonReentrant` 수정자를 배치하는 것이 좋습니다.

# [L-09] 제거된 금고 자산 재추가는 `lastTotalAssets`의 회계를 중복시킴

_인정됨 (Acknowledged)_

전략 금고가 자산을 여전히 보유하고 있는 동안(`strategy.balanceOf(address(EulerEarn)) > 0`) 긴급 제거를 통해 제거되면, 이 복구 불가능한 자산은 `_accruedFeeAndAssets()`의 `lostAssets` 변수에 추가됩니다:

```solidity
 function _accruedFeeAndAssets()
        internal
        view
        returns (uint256 feeShares, uint256 newTotalAssets, uint256 newLostAssets)
    {
        // Earn 금고가 전략 금고에 가지고 있는 자산.
        uint256 realTotalAssets;
        for (uint256 i; i < withdrawQueue.length; ++i) {
            IERC4626 id = withdrawQueue[i];
            realTotalAssets += _expectedSupplyAssets(id);
        }

        uint256 lastTotalAssetsCached = lastTotalAssets;
        if (realTotalAssets < lastTotalAssetsCached - lostAssets) {
            // 금고가 일부 자산을 잃은 경우(realTotalAssets 감소), lostAssets가 증가합니다.
            newLostAssets = lastTotalAssetsCached - realTotalAssets;
        } else {
            // 그렇지 않은 경우 lostAssets는 동일하게 유지됩니다.
            newLostAssets = lostAssets;
        }

        newTotalAssets = realTotalAssets + newLostAssets;
        uint256 totalInterest = newTotalAssets - lastTotalAssetsCached;
       //...
    }
```

그러나 **동일한 금고가 나중에 `EulerEarn` 금고에 다시 추가되면**, 새 전략으로 취급되어 `withdrawQueue`에 추가됩니다. 이 과정에서 구성은 `marketConfig.balance = id.balanceOf(address(this))`로 초기화됩니다. 이 잔액에는 이전에 잃어버린 주식이 여전히 포함되어 있으므로 **동일한 자산이 `totalAssets()`에서 다시 계산되어 이중 계산이 발생합니다**:

```solidity
function _setCap(IERC4626 id, uint136 supplyCap) internal {
        //...
        if (supplyCap > 0) {
            IERC20(asset()).forceApproveMaxWithPermit2(address(id), permit2);

            if (!marketConfig.enabled) {
                withdrawQueue.push(id);

                //...
                marketConfig.balance = id.balanceOf(address(this)).toUint112();

                // 수수료를 적용하지 않고 새 금고의 자산을 고려합니다.
                _updateLastTotalAssets(lastTotalAssets + _expectedSupplyAssets(id));

                //...
            }

            //...
        } else {
            IERC20(asset()).revokeApprovalWithPermit2(address(id), permit2);
        }

        //...
    }
```

이로 인해 부풀려진 `totalAssets()` 값이 발생하여 `EaulerEarn` 금고의 주가가 상승합니다. 결과적으로 금고와 상호 작용하는 사용자(예: 발행)는 원래 받아야 할 것보다 적은 주식을 받을 수 있습니다.

**권장 사항**

다시 추가된 전략 금고를 현재 잔액으로 초기화하지 마십시오. 대신 이전에 제거된 금고를 추가할 때는 `marketConfig.balance = 0`을 명시적으로 설정하십시오.

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다. 동작은 원래 코드베이스와 동일합니다.

# [L-10] 더스트(Dust) 주식으로 인해 일관성 없는 확인으로 인한 즉각적인 전략 금고 제거 방지

_해결됨 (Resolved)_

`EulerEarn` 금고는 자금을 다른 전략 금고로 재할당하여 `withdrawQueue`에서 전략 금고를 즉시 제거하는 것을 지원합니다. 그러나 이 프로세스는 전략 금고가 진정으로 "비어 있는지" 확인할 때 일관성 없는 로직으로 인해 방해를 받습니다.

- `reallocate()`에서 전략 금고는 `supplyAssets`(`id.previewRedeem(supplyShares)`를 통해 계산됨)를 기반으로 비어 있는 것으로 간주됩니다:

```solidity
function reallocate(MarketAllocation[] calldata allocations) external nonReentrant onlyAllocatorRole {
//...
            if (withdrawn > 0) { //@audit : if supplyAssets rounds down to zero, this block is skipped

                //...

                config[id].balance = uint112(supplyShares - withdrawnShares);

                //...
            } else {
                //...
            }
}
```

- `updateWithdrawQueue()`에서는 해당 전략 금고에 `shares`가 있는지 확인합니다(`config[id].balance`를 통해):

```solidity
    function updateWithdrawQueue(uint256[] calldata indexes) external onlyAllocatorRole {
        //...

        for (uint256 i; i < currLength; ++i) {
            if (!seen[i]) {
                IERC4626 id = withdrawQueue[i];

                //...

                if (config[id].balance != 0) {
                    if (config[id].removableAt == 0) revert ErrorsLib.InvalidMarketRemovalNonZeroSupply(id);

                    if (block.timestamp < config[id].removableAt) {
                        revert ErrorsLib.InvalidMarketRemovalTimelockNotElapsed(id);
                    }
                }

                delete config[id];
            }
        }

        //...
    }
```

이러한 불일치는 전략 금고가 `reallocate()`에서 비어 있는 것처럼 보이지만(상환 가능한 자산이 0이므로) 금고가 여전히 더스트 주식을 보유하고 있기 때문에 `updateWithdrawQueue()`를 통해 제거할 수 없는 시나리오를 허용합니다. 이 상황은 반올림 및 낮은 주가로 인해 발생할 수 있으며, 이로 인해 상환 시 자산이 0이 됩니다.

예를 들어 주가가 1 미만인 경우 1주는 단일 자산 단위로 상환되지 않을 수 있습니다. 이 경우:

- `id.previewRedeem(config[id].balance)`는 `0`을 보고하므로 계산된 `withdrawn`은 `0`이 됩니다.
- `reallocate()`는 전략 금고가 비어 있다고 가정하고 아무런 조치도 취하지 않습니다.
- 그러나 `updateWithdrawQueue()`는 여전히 `config[id].balance` > 0을 보고 `removableAt == 0`이 이전에 `submitMarketRemoval()`을 통해 전략 금고를 제거하도록 설정되었으므로 제거를 차단합니다.

결과는 다음과 같습니다:

- 가치 관점에서는 비어 있는 것처럼 보이지만 여전히 사용할 수 없는 더스트 주식을 보유하고 있는 전략 금고의 즉각적인 제거를 방지합니다.
- 느린 타임락 경로에 의존하도록 강제하여 유연성을 줄입니다.

**권장 사항**

`withdrawn == 0`이라도 남은 주식의 상환을 강제하도록 `reallocate()` 함수를 업데이트하는 것을 고려하십시오. 이는 제거할 금고에 대해 `allocation.assets == 0` 플래그를 지정하여 수행할 수 있습니다.

# [L-11] 플래시 론 공격으로 유동성을 더 위험한 전략 금고로 재할당

_인정됨 (Acknowledged)_

`EulerEarn`에서 사용자 예금은 남은 용량에 따라 기본 `supplyQueue` 전략 금고에 분배됩니다. `_supplyStrategy()` 함수는 이 대기열을 반복하고 가용 용량이 있는 첫 번째 공급 전략 금고에 자금을 입금합니다. 공급 전략 금고가 한도에 도달하면 `EulerEarn` 금고는 전체 예금이 할당될 때까지 다음 금고로 진행합니다.

그러나 이 설계는 **조작적 유동성 재할당**의 위험을 도입합니다. 악의적인 행위자가 플래시 론 공격을 수행할 수 있습니다. `EulerEarn`에 대량의 자산을 입금하고 `supplyQueue` 전체에 재분배를 트리거한 다음 동일한 트랜잭션에서 해당 자산을 인출합니다. 이로 인해 유동성이 더 크고 안전한 공급 전략 금고에서 더 작거나 위험한 전략 금고로 이동하게 됩니다.

예를 들어:

- `supplyQueue`에 100M 및 20M의 한도를 가진 두 개의 전략 금고(A 및 B)가 있다고 가정합니다. 공급 전략 금고 A는 현재 20M을 보유하고 있고 공급 전략 금고 B는 1M을 보유하고 있습니다.
- 공격자는 99M을 `EulerEarn`에 입금하여 자금이 A에서 B로 넘치게 할 수 있습니다. 그런 다음 즉시 99M을 인출하여 B에 금고 총 유동성의 불균형한 양을 남깁니다.

이 악용을 통해 공격자는 `EulerEarn`의 포트폴리오 할당을 조작하여 성능을 저하시키고 위험 노출을 증가시킬 수 있습니다. 또한 한 전략 금고에서 큰 지분을 인출하면 유동성 부족이 발생하고 해당 전략 금고의 사용자에게 부정적인 영향을 미칠 수 있습니다.

**권장 사항**

`supplyQueue`의 첫 번째 전략 금고의 공급 한도를 높은 값으로 늘려 일시적인 급증을 흡수하고 더 작은 전략 금고를 보호하는 버퍼 역할을 하도록 하는 것을 고려하십시오.

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다. 동작은 원래 코드베이스와 동일합니다.

# [L-12] `_expectedSupplyAssets()`의 반올림으로 `supplyCap` 초과 가능

_인정됨 (Acknowledged)_

`_supplyStrategy()` 함수는 입금 직후 전략 금고에 공급된 총 자산이 정의된 `supplyCap`을 초과하지 않도록 보장해야 합니다. 이를 달성하려면 `toSupply` 값은 보수적이어야 합니다. 즉, 사용 가능한 공급 공간을 과대평가해서는 안 됩니다.

여기서 계산된 `supplyAssets`는 가장 최신의 **약간 과대평가된 값(즉, 올림)**을 반영하여 `supplyCap.zeroFloorSub(supplyAssets)`가 내림되도록 해야 합니다.

그러나 `supplyAssets`는 `id.previewRedeem()`을 통해 계산되며, 여기서 **상환 가능한 자산을 내림**하므로 `toSupply`가 금고에 제공되어야 하는 것보다 높을 수 있습니다.

```solidity
  function _expectedSupplyAssets(IERC4626 id) internal view returns (uint256) {
        return id.previewRedeem(config[id].balance);
    }
```

```solidity
// @note : openzeppelin-contracts/contracts/token/ERC20/extensions/ERC4626.sol
   /// @inheritdoc IERC4626
    function previewRedeem(uint256 shares) public view virtual returns (uint256) {
        return _convertToAssets(shares, Math.Rounding.Floor);
    }
```

이로 인해 입금 직후 전략 금고가 `supplyCap`을 위반할 수 있습니다.

**권장 사항**

엣지 케이스에서도 공급 한도가 준수되도록 `_supplyStrategy()` 함수 내부의 `supplyCap` 계산에 사용될 때 최종 결과를 올림하도록 `_expectedSupplyAssets()`를 조정하는 것을 고려하십시오.

### Euler 의견

발견 사항을 인정하며 그대로 유지할 것입니다. 전략의 이자 발생도 바로 다음 블록에서 한도를 초과할 수 있지만 우려할 사항은 아닙니다.

# [L-13] 전략 손실이 다른 전략의 이자 수익으로 가려질 수 있음

_인정됨 (Acknowledged)_

`EulerEarn` 금고는 여러 전략의 수익률을 집계합니다. 그러나 `_accruedFeeAndAssets()`에서 `lostAssets`를 계산할 때 구현은 총 자산의 순 증가가 손실이 발생하지 않았음을 의미한다고 잘못 가정하므로 `lostAssets`를 제대로 추적하지 못합니다.

```solidity
        // Earn 금고가 전략 금고에 가지고 있는 자산.
        uint256 realTotalAssets;
        for (uint256 i; i < withdrawQueue.length; ++i) {
            IERC4626 id = withdrawQueue[i];
            realTotalAssets += _expectedSupplyAssets(id);
        }

        uint256 lastTotalAssetsCached = lastTotalAssets;
        if (realTotalAssets < lastTotalAssetsCached - lostAssets) {
            // 금고가 일부 자산을 잃은 경우(realTotalAssets 감소), lostAssets가 증가합니다.
            newLostAssets = lastTotalAssetsCached - realTotalAssets;
        } else {
            // 그렇지 않은 경우 lostAssets는 동일하게 유지됩니다.
            newLostAssets = lostAssets;
        }
```

문제를 더 잘 이해하기 위해 다음 시나리오를 고려하십시오:

1. EulerEarn은 두 가지 전략 S1 및 S2를 지원하며 각각 1e18이 예치되어 `totalAssets = 2e18`입니다.
2. 얼마 후 S2는 0.25e18의 이자가 발생하고 S1은 0.1e18의 손실이 발생합니다.
3. 이 시점에서 `_accruedFeeAndAssets()`가 트리거되면 총 발생 이자가 0.15e18이라고 가정하고 `lostAssets`를 업데이트하지 **않습니다**. 이는 부정확합니다. S1은 0.1e18을 잃었지만 이 손실은 포착되지 않습니다.

이 취약점의 영향은 `lostAssets` 값에 주요 회계 부정확성을 초래하고 시간이 지남에 따라 더 악화되기 때문에 큽니다. 개별 전략의 손실은 포착되지 않아 `lostAssets` 메트릭을 신뢰할 수 없게 만듭니다. 또한 이는 `feeRecipient`에 대한 수수료 과소 징수로 이어집니다. 수수료는 발생한 순 이자를 기준으로 발행되므로 한 전략의 손실은 다른 전략의 명백한 이익을 감소시켜 궁극적으로 프로토콜의 수익을 낮춥니다. 이 결함으로 인해 `lostAssets`(금고 성과 저조와 사용자 손실을 격리해야 함) 간의 의도된 구분이 유지되지 않습니다.

**권장 사항**

이 문제를 해결하는 것은 사소하지 않을 수 있지만, 확실한 접근 방식은 전략별 마지막 총 자산을 추적하는 것입니다. 이렇게 하면 특정 전략이 자산을 잃었는지 또는 수익률이 독립적으로 발생했는지 명확하게 확인할 수 있습니다.

### Euler 의견

발견 사항을 인정하지만 동작은 의도된 것이므로 그대로 유지할 것입니다.
