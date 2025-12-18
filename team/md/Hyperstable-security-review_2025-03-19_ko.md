# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **hyperstable/contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Hyperstable 정보 (About Hyperstable)

Hyperstable은 사용자가 지원되는 담보를 예치하여 $USDH를 발행하는 1 USD로 거래되도록 설계된 과담보 스테이블코인입니다. 거버넌스 및 유동성 인센티브는 투표 및 게이지를 통해 관리되므로 vePEG 보유자가 PEG 배출을 지시하고 보상을 받고 유동성 분배에 영향을 미칠 수 있습니다.

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

_검토 커밋 해시_ - [4f650122d3927fd45015c40ad58172508b148876](https://github.com/hyperstable/contracts/tree/4f650122d3927fd45015c40ad58172508b148876)

_수정 검토 커밋 해시_ - [f2cfa5cb9db05b1e30b048f802c2cf3a62c7878a](https://github.com/hyperstable/contracts/tree/f2cfa5cb9db05b1e30b048f802c2cf3a62c7878a)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `DebtToken`
- `InterestRateStrategyV1`
- `LiquidationBuffer`
- `LiquidationManager`
- `PositionManager`
- `Vault`
- `BribeFactory`
- `EmissionScheduler`
- `ExternalBribe`
- `Gauge`
- `GaugeFactory`
- `InternalBribe`
- `Minter`
- `Peg`
- `PegAirdrop`
- `RewardsDistributor`
- `VeArtProxy`
- `Voter`
- `vePeg`

# 발견 사항 (Findings)

# [C-01] `delegate()`는 무단 투표 위임을 허용함 (`delegate()` permits unauthorized vote delegation)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`vePeg` 계약의 `delegate()` 함수는 NFT(잠금)의 투표를 다른 NFT/잠금(위임자)으로 위임하기 위한 것입니다. 그러나 문제는 이 함수가 누구나 모든 잠금(NFT) 소유자를 대신하여 위임할 수 있도록 허용한다는 것인데, 이는 바람직하지 않습니다. 위임은 잠금의 소유자 또는 운영자에게만 허용되어야 합니다:

```solidity
function delegate(uint256 _from, uint256 _to) public {
        return _delegate(_from, _to);
    }
```

이로 인해 다음과 같은 위험이 발생합니다:

- 악의적인 행위자가 잠금(NFT) 소유자를 대신하여 투표를 위임하여 투표를 훔칠 수 있습니다.
- 악의적인 행위자가 소유자를 괴롭히기 위해 대리 투표를 하여 체크포인트를 `MAX_DELEGATES`인 128개를 초과하도록 늘릴 수 있으며, 이는 `lock_perpetually()`, `unlock_perpetual()`, `delegateBySig()`와 같이 `_delegate()`를 호출하는 모든 함수에 대한 DoS로 이어집니다.

## 권장 사항

잠금 소유자 또는 운영자만 투표를 위임할 수 있도록 `delegate()` 함수를 업데이트하십시오:

```diff
function delegate(uint256 _from, uint256 _to) public {
+      require(_isApprovedOrOwner(msg.sender, _from));
       return _delegate(_from, _to);
}
```

# [H-01] 잘못된 `deposit_for()` 확인 (Faulty `deposit_for()` check)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`RewardsDistributor` 계약의 `claim()` 함수는 `vePeg` 잠금에 대한 보상을 청구하도록 설계되었습니다. 잠금이 만료된 경우 보상을 직접 전송하거나, 잠금이 만료되지 않았거나 영구적으로 잠겨 있는 경우 `vePeg.deposit_for()` 함수를 호출합니다:

```solidity
 function claim(uint256 _tokenId) external returns (uint256) {
        //...
        if (amount != 0) {
            //...
            if (locked.end > block.timestamp || locked.perpetuallyLocked) {
                // lock has not expired
                ve.deposit_for(_tokenId, amount);
            } else {
                // lock expired
                address owner = ve.ownerOf(_tokenId);
                token.safeTransfer(owner, amount);
            }
            //...
        }
        //...
    }
```

문제는 영구 잠금(perpetual locks)에서 발생합니다. `deposit_for()` 함수는 `lock.end > block.timestamp`인지 확인하지만, 영구 잠금의 경우 잠금 종료 시간이 항상 0으로 설정되어 있어 `deposit_for()` 함수의 구현된 확인에 의해 만료된 것으로 간주됩니다.
이로 인해 영구 잠금에 대한 보상을 예치하려고 할 때 `deposit_for()` 함수가 되돌려집니다:

```solidity
function deposit_for(uint256 _tokenId, uint256 _value) external nonreentrant {
        LockedBalance memory _locked = locked[_tokenId];
        //...
        require(_locked.end > block.timestamp, "Cannot add to expired lock. Withdraw");
        //...
    }
```

결과적으로 영구 잠금은 보상을 받을 수 없으며 청구된 금액만큼 투표권이 증가하는 것을 방지합니다. 유일한 해결 방법은 소유자가 `unlock_perpetual()` 함수를 사용하여 영구 잠금을 해제하는 것입니다.

## 권장 사항

영구 잠금의 상태를 확인하고 그에 따라 `perpetuallyLockedBalance`를 업데이트하여 영구 잠금을 올바르게 처리하도록 `deposit_for()` 함수를 업데이트하십시오:

```diff
function deposit_for(uint256 _tokenId, uint256 _value) external nonreentrant {
        LockedBalance memory _locked = locked[_tokenId];
        //...
-       require(_locked.end > block.timestamp, "Cannot add to expired lock. Withdraw");
+       require(_locked.end > block.timestamp || _locked.perpetuallyLocked , "Cannot add to expired lock. Withdraw");

+       if (_locked.perpetuallyLocked) {
+           perpetuallyLockedBalance += _value;
+       }

        //...
    }
```

# [H-02] 보상 분배 시 투표권 손실 (Losing voting power in rewards distribution)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`RewardsDistributor` 및 `TokenRewardsDistributor` 계약 모두 투표권 및 보상 계산에서 영구 잠금을 적절하게 설명하지 않습니다. 계약은 선형 감소 공식(`bias - slope * time`)을 사용하여 투표권을 계산하는데, 영구 잠금은 시간이 지남에 따라 일정한 투표권을 유지해야 하므로 이를 잘못 처리합니다.

이 문제는 두 계약의 다음 함수에 영향을 미칩니다:

- `ve_for_at()`
- `_checkpoint_total_supply()`
- `_claim()`
- `_claimable()`

영구 잠금은 `bias = 0` 및 `slope = 0`으로 초기화되므로 현재 보상 로직은 이에 대해 `0` 투표권을 계산합니다.

결과적으로 영구 잠금이 있는 사용자는 활성 및 지속적인 잠금이 있음에도 불구하고 보상을 청구할 수 없습니다. 이러한 사용자가 보상을 받는 유일한 방법은 먼저 영구 잠금을 일반 잠금으로 변환하는 것입니다.

## 권장 사항

영구 잠금에 대한 케이스를 처리하도록 구현을 업데이트하십시오.

# [H-03] 비영구적 잠금이 추가 위임 권한을 얻음 (Non-perpetual locks gaining extra delegation power)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`vePeg._delegate()` 함수는 위임을 허용하기 전에 소스 토큰(`_from`)이 영구적으로 잠겨 있어야 한다고 요구합니다. 그러나 `_moveAllDelegates()` 함수는 이 제한을 무시하고 비영구적 잠금을 포함하여 사용자가 소유한 모든 토큰을 새 위임자에게 이동합니다.

이는 영구적 잠금만 위임 권한을 가져야 한다는 의도를 완전히 우회합니다.

```solidity
function _delegate(uint256 _from, uint256 _to) internal {
    LockedBalance memory currentLock = locked[_from];
    require(currentLock.perpetuallyLocked == true, "Lock is not perpetual");
    --- SNIPPED ---
    _moveAllDelegates(delegator, currentDelegate, delegatee);
}
```

`_moveAllDelegates()` 함수는 영구적일 뿐만 아니라 사용자가 소유한 모든 토큰을 위임에 추가합니다.

```solidity
//File: src/governance/vePeg.sol

function _moveAllDelegates(address owner, address srcRep, address dstRep) internal {
    --- SNIPPED ---

    if (dstRep != address(0)) {
        --- SNIPPED ---

        // Plus all that's owned
        for (uint256 i = 0; i < ownerTokenCount; i++) {
@>          uint256 tId = ownerToNFTokenIdList[owner][i];   //@audit This contains all locks, including non-perpetual locks
            dstRepNew.push(tId);
        }

        --- SNIPPED ---
    }
}
```

## 권장 사항

`_moveTokenDelegates()` 함수를 사용하여 특정 토큰을 대상 사용자에게 위임할 수 있습니다.

그러나 `_delegate()` 함수는 현재 토큰 ID를 받지만 주소 수준에서 위임을 수행하여 불일치를 초래하고 대상 토큰 ID를 중복으로 만듭니다. 의도된 동작이 단일 토큰의 투표권을 위임하는 것이라면 `_delegate()`가 위임자 주소를 수락하고 그에 따라 `_moveTokenDelegates()`를 사용하도록 업데이트하는 것이 더 명확할 것입니다. 그렇지 않고 주소 수준 위임이 의도된 설계라면 혼란을 피하기 위해 함수 인터페이스가 이를 반영해야 합니다.

# [M-01] 청산 실패 (Failing liquidation)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`PositionManager` 계약의 `setLiquidationManager()` 함수에서 `liquidationManager` 주소가 변경되면, 새 계약은 등록된 모든 금고의 지분 토큰에 대해 승인을 받지 못합니다. 이로 인해 `liquidationManager`가 청산인에게 지불하기 위해 자산을 상환하려고 시도할 때 **이러한 금고에 대한 청산 실패**가 발생합니다:

```solidity
  function setLiquidationManager(address _newLiquidationManager) external onlyOwner {
        emit NewLiquidationManager(liquidationManager, _newLiquidationManager);

        liquidationManager = _newLiquidationManager;
    }
```

## 권장 사항

등록된 모든 금고 주식에 대해 새로운 `liquidationManager` 주소 승인을 부여하는 메커니즘을 구현하십시오.

# [M-02] `delegateBySig()`가 위임을 잘못 제한함 (`delegateBySig()` wrongly limits delegation)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`vePeg` 계약의 `delegateBySig()` 함수는 유효한 서명이 있는 사람이 위임자를 대신하여 투표를 위임할 수 있도록 하기 위한 것입니다. 그러나 문제는 이 함수에 호출자가 잠금(NFT)의 소유자 또는 운영자(승인된 주소)여야 액세스할 수 있다는 확인이 포함되어 있다는 것입니다. 이는 서명에 의한 위임을 사실상 쓸모없게 만드는데, 왜냐하면 호출자 상태에 관계없이 유효한 서명이 있으면 누구나 호출할 수 있어야 하기 때문입니다:

```solidity
function delegateBySig(uint256 from, uint256 to, uint256 nonce, uint256 expiry, uint8 v, bytes32 r, bytes32 s)
        public
    {
        //...
        require(_isApprovedOrOwner(msg.sender, from), "Not approved");
        //...
    }
```

이 동작은 외부 당사자가 유효한 서명으로 위임을 수행하는 것을 방지하여 서명에 의한 위임의 의도된 설계 및 기능과 모순됩니다.

## 권장 사항

`delegateBySig()` 함수는 유효한 서명이 있는 사람이면 누구나 투표를 위임할 수 있도록 허용해야 하므로 호출자를 소유자 또는 운영자로 제한하는 확인을 제거하십시오:

```diff
 function delegateBySig(uint256 from, uint256 to, uint256 nonce, uint256 expiry, uint8 v, bytes32 r, bytes32 s)
        public
    {
        //...
-       require(_isApprovedOrOwner(msg.sender, from), "Not approved");
        //...
    }
```

# [M-03] `increase_amount()` 영구 잠금 금액 지원 부족 (`increase_amount()` lacks support for perpetual lock amounts)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`vePeg` 계약의 `increase_amount()` 함수는 잠금 해제 시간을 수정하지 않고 누구나 잠금(`tokenId`)에 대해 추가 토큰을 입금할 수 있도록 설계되었습니다. 그러나 이 함수는 영구 및 비영구 잠금 모두와 호환되어야 하지만 **현재 구현은 비영구 잠금에 대해서만 금액을 늘릴 수 있도록 허용합니다**. 이는 `_locked.end > block.timestamp`를 확인하기 때문인데, 영구 잠금의 경우 `lock.end`가 0으로 설정되므로 적용할 수 없습니다:

```solidity
function increase_amount(uint256 _tokenId, uint256 _value) external nonreentrant {
    //...
    require(_locked.end > block.timestamp, "Cannot add to expired lock. Withdraw");
    //...
}
```

이로 인해 소유자/운영자가 잠금을 해제하지 않는 한 영구 잠금은 이 기능에서 제외됩니다.

## 권장 사항

다음과 같이 영구 잠금을 처리하도록 `increase_amount()` 함수의 확인을 업데이트하십시오:

```diff
    function increase_amount(
        uint256 _tokenId,
        uint256 _value
    ) external nonreentrant {
     //...
-   require(_locked.end > block.timestamp, "Cannot add to expired lock. Withdraw");
+   require(_locked.end > block.timestamp || _locked.perpetuallyLocked , "Cannot add to expired lock. Withdraw");
    //...
}
```

# [M-04] 초기화되지 않은 비율로 인한 지속적인 인플레이션 (Persistent inflation from uninitialized rates)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`PositionManager.setInterestRateStrategy()` 함수를 사용하면 프로토콜 소유자가 이자율 전략 계약을 업데이트할 수 있습니다. 그러나 이 작업에는 새 전략에 필요한 내부 상태를 마이그레이션하거나 초기화하는 메커니즘이 포함되어 있지 않습니다. 각 금고에 대한 `targetUtilization`, `endRateAt` 및 `lastUpdate`와 같은 매개변수는 새 전략에서 초기화되지 않은 상태로 유지되어 부정확하고 부풀려진 이자율 계산으로 이어집니다.

```solidity
function setInterestRateStrategy(address _newInterestRateStrategy) external onlyOwner {
    if (_newInterestRateStrategy == address(0)) {
        revert ZeroAddress();
    }

    emit NewInterestRateStrategy(address(interestRateStrategy), address(_newInterestRateStrategy));

    interestRateStrategy = IInterestRateStrategy(_newInterestRateStrategy);
}
```

이자율 계산 함수인 `interestRate()`는 금고별 매개변수에 의존하여 현재 활용도와 목표 활용도 간의 차이에 따라 부드럽고 적응적인 비율 곡선을 계산합니다. 이러한 매개변수가 누락되거나 0으로 초기화되면 함수는 큰 오류(`err`)를 계산하여 과도한 선형 적응과 인위적으로 높은 이자율을 초래합니다.

그러나 이 함수는 소유자에 의해 엄격하게 제어되며 예외적인 상황에서만 사용될 것으로 예상됩니다.

## 권장 사항

새 `interestRateStrategy` 계약은 각 금고의 현재 상태로 초기화하거나 이전 전략에서 관련 매개변수를 적절하게 마이그레이션하여 이자율 계산의 정확성과 일관성을 보장해야 합니다.

# [M-05] 공격자는 사용자가 잠금을 수행하여 DOS 공격을 수행할 수 있습니다. (Attacker can perform DOS attack by locking user)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

공격자는 `create_lock_for()`를 사용하여 단 1 wei와 `MAXTIME`을 사용하여 다른 사용자를 대신하여 잠금을 생성하고 `MAX_DELEGATES`에 도달할 수 있습니다. 결과적으로 영향을 받는 사용자가 잠금을 생성하려고 하면 트랜잭션이 `"dstRep would have too many tokenIds"`와 함께 되돌려져 새 잠금을 생성할 수 없습니다.

```solidity
function create_lock_for(uint256 _value, uint256 _lock_duration, address _to)
        external
        nonreentrant
        returns (uint256)
    {
        return _create_lock(_value, _lock_duration, _to);
    }

```

이는 사용자가 잠금 기간이 만료될 때까지 기다려야 하므로 `withdraw()`를 호출하여 잠금을 제거함으로써 해결할 수 없기 때문에 심각한 문제입니다. 공격자는 잠금을 `MAXTIME`으로 설정하여 사용자가 새 잠금을 생성하기 전에 1년 전체를 기다리도록 강제할 수 있습니다.

```solidity
require(block.timestamp >= _locked.end, "The lock didn't expire");
```

문제를 더 잘 이해하려면 다음 POC를 `vePeg.t.sol`에 복사하십시오.

```solidity
function test_DOS_createlockfor() external {

        uint256 aliceAmountToLock = 1;
        uint256 aliceLockDuration = 365 days;

        vm.startPrank(ALICE);

        peg.approve(address(ve), type(uint256).max);

        //Alice creates 128 locks, each with 1 wei and the maximum duration, to perform a DOS attack on Bob
        for(uint256 i = 0; i < 128; i++) {

            uint256 aliceLockId = ve.create_lock_for(aliceAmountToLock, aliceLockDuration, BOB);
        }

        vm.stopPrank();

        vm.startPrank(BOB);

        uint256 bobAmountToLock = 1e18;
        uint256 bobLockDuration = 365 days;

        peg.approve(address(ve), bobAmountToLock);

        //Bob attempts to create a lock, but the transaction reverts due to Alice's DoS attack.
        vm.expectRevert("dstRep would have too many tokenIds");
        ve.create_lock(bobAmountToLock, bobLockDuration);

        vm.stopPrank();
    }
```

## 권장 사항

두 가지 잠재적인 수정 사항:

1. 잠금 생성에 필요한 금액에 대한 최소 가치 요구 사항을 설정하여 공격을 덜 실현 가능하게 만듭니다.
2. `create_lock_for()` 액세스를 신뢰할 수 있는 엔터티로만 제한합니다.

# [M-06] `MAX_DELEGATES` 제한을 우회할 수 있음 (`MAX_DELEGATES` limit can be bypassed)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`delegate()`는 투표를 한 ID에서 다른 ID로 위임할 수 있도록 허용합니다. ID의 각 소유자는 최대 128명의 위임자 제한(`MAX_DELEGATES = 128`)이 있습니다. 그러나 `require` 문이 새로 획득한 위임자를 검증하지 않기 때문에 이 제한을 우회할 수 있습니다. 이 문제는 `_moveAllDelegates()`에서 관찰할 수 있습니다.

게다가 위임자 수가 크게 증가하면 DOS가 발생할 수 있습니다. 이는 루프를 반복하면 블록의 가스 한도에 도달하여 NFT를 전송하거나 투표를 위임하는 것이 불가능해질 수 있기 때문입니다.

```solidity
function _moveAllDelegates(address owner, address srcRep, address dstRep) internal {

///code...

if (dstRep != address(0)) {
                uint32 dstRepNum = numCheckpoints[dstRep];
                uint256[] storage dstRepOld =
                    dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].tokenIds : checkpoints[dstRep][0].tokenIds;
                uint256[] storage dstRepNew = checkpoints[dstRep][dstRepNum].tokenIds;
                uint256 ownerTokenCount = ownerToNFTokenCount[owner];
@>              require(dstRepOld.length <= MAX_DELEGATES, "dstRep would have too many tokenIds");
                // All the same
                for (uint256 i = 0; i < dstRepOld.length; i++) {
                    uint256 tId = dstRepOld[i];
                    dstRepNew.push(tId);
                }
                // Plus all that's owned
                for (uint256 i = 0; i < ownerTokenCount; i++) {
                    uint256 tId = ownerToNFTokenIdList[owner][i];
                    dstRepNew.push(tId);
                }

                if (_isCheckpointInNewBlock(dstRep)) {
                    numCheckpoints[dstRep] = dstRepNum + 1;
                    checkpoints[dstRep][dstRepNum].timestamp = _timestamp;
                } else {
                    checkpoints[dstRep][dstRepNum - 1].tokenIds = dstRepNew;
                    delete checkpoints[dstRep][dstRepNum];
                }
            }
        }
    }
```

보시다시피 `MAX_DELEGATES`에 대한 확인은 새 위임자가 추가되기 전에 수행되어 제한을 우회할 수 있습니다.

문제를 더 잘 이해하려면 다음 POC를 `vePeg.t.sol`에 복사하십시오.

```solidity
function test_MAX_DELEGATES_can_be_bypassed() external {

        uint256 aliceAmountToLock = 1;
        uint256 aliceLockDuration = 365 days;

        vm.startPrank(ALICE);

        peg.approve(address(ve), type(uint256).max);

        for(uint256 i = 0; i < 128; i++) {

            uint256 aliceLockId = ve.create_lock_for(aliceAmountToLock, aliceLockDuration, ALICE);
            ve.lock_perpetually(aliceLockId);
        }

        vm.stopPrank();

        vm.startPrank(BOB);

        uint256 bobAmountToLock = 1;
        uint256 bobLockDuration = 365 days;

        peg.approve(address(ve), type(uint256).max);

        for(uint256 i = 0; i < 128; i++) {

            uint256 bobLockId = ve.create_lock_for(bobAmountToLock, bobLockDuration, BOB);
            ve.lock_perpetually(bobLockId);
        }

        vm.stopPrank();

        //1 is from Alice and 150 from Bob
        vm.prank(ALICE);
        ve.delegate(1, 150);

        //As a result, Bob ends up with 256 delegates, exceeding the MAX_DELEGATES limit of 128.
    }
```

결과를 관찰하려면 새 위임자가 추가된 후 `_moveAllDelegates()` 내에서 `dstRepOld.length`를 인쇄하십시오. 이를 위해 `import {console} from "forge-std/console.sol";`을 가져오고 `vePeg.sol`에서 `console.log("dstRepNew:", dstRepNew.length);`를 사용하십시오.
Foundry는 공개 매핑에서 구조체 내부의 배열에 직접 액세스할 수 없으므로 이 접근 방식이 필요합니다. 이렇게 하면 Bob의 위임자가 256명에 도달하여 `MAX_DELEGATES` 제한인 128명을 초과하는 것을 볼 수 있습니다.

## 권장 사항

문제를 해결하려면 새 항목이 추가된 후 위임자의 길이를 확인하십시오.

```diff
if (dstRep != address(0)) {
                uint32 dstRepNum = numCheckpoints[dstRep];
                uint256[] storage dstRepOld =
                    dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].tokenIds : checkpoints[dstRep][0].tokenIds;
                uint256[] storage dstRepNew = checkpoints[dstRep][dstRepNum].tokenIds;
                uint256 ownerTokenCount = ownerToNFTokenCount[owner];
-              require(dstRepOld.length <= MAX_DELEGATES, "dstRep would have too many tokenIds");
                // All the same
                for (uint256 i = 0; i < dstRepOld.length; i++) {
                    uint256 tId = dstRepOld[i];
                    dstRepNew.push(tId);
                }
                // Plus all that's owned
                for (uint256 i = 0; i < ownerTokenCount; i++) {
                    uint256 tId = ownerToNFTokenIdList[owner][i];
                    dstRepNew.push(tId);
                }

+              require(dstRepNew.length <= MAX_DELEGATES, "dstRep would have too many tokenIds");

                if (_isCheckpointInNewBlock(dstRep)) {
                    numCheckpoints[dstRep] = dstRepNum + 1;
                    checkpoints[dstRep][dstRepNum].timestamp = _timestamp;
                } else {
                    checkpoints[dstRep][dstRepNum - 1].tokenIds = dstRepNew;
                    delete checkpoints[dstRep][dstRepNum];
                }
            }
        }
    }
```

# [L-01] 위임받는 사람은 NFT를 소유해야 함 (The delegatee must own an NFT)

위임자는 다른 사용자로부터 위임받으려면 vePEG NFT를 소유해야 합니다.

```solidity
File: vePeg.sol

         address delegatee = _to == 0 ? address(0) : ownerOf(_to);

```

# [L-02] 슬리피지 보호 누락 (Missing protection against slippage)

`LiquidationBuffer` 계약의 `claimExcess()` 함수에서 계약 소유자는 지정된 금고에서 지분을 상환하여 초과 버퍼를 청구할 수 있습니다. 그러나 소유자가 수령할 수 있는 상환 자산의 최소 금액에 대한 확인이 없어 이 함수는 슬리피지로부터 보호되지 않으며, 따라서 의도한 것보다 적은 상환 자산을 받을 수 있습니다:

```solidity
  function claimExcess(uint8 _vaultId) external onlyOwner {
        uint256 excess = claimableExcess[_vaultId];
        claimableExcess[_vaultId] = 0;

        if (excess > 0) {
            IVault(POSITION_MANAGER.getVault(_vaultId).addr).redeem(excess, msg.sender, address(this));
        }
    }
```

권장 사항: 슬리피지를 완화하기 위해 `minAmountOut` 매개변수를 추가하고 확인하십시오.

# [L-03] 이전 청산 관리자에 대한 취소 없음 (No revocation for previous liquidation manager)

`PositionManager` 계약의 `setLiquidationManager()` 함수에서 `liquidationManager` 주소를 변경할 때 이전 `liquidationManager` 주소의 승인은 **모든 등록된 금고에서 취소되지 않습니다**. 이전 `liquidationManager` 계약이 손상되면 여전히 금고의 자산을 관리할 승인을 보유하므로 이 간과로 인해 `PositionManager` 계약 지분이 손상될 수 있습니다:

```solidity
  function setLiquidationManager(address _newLiquidationManager) external onlyOwner {
        emit NewLiquidationManager(liquidationManager, _newLiquidationManager);

        liquidationManager = _newLiquidationManager;
    }
```

권장 사항: 이전 `liquidationManager` 주소에서 금고 승인을 취소하는 메커니즘을 구현하십시오.

# [L-04] `increase_unlock_time()`의 오해의 소지가 있는 오류 (Misleading error in `increase_unlock_time()`)

`increase_unlock_time()` 함수에는 사용자가 영구 잠금의 잠금 시간을 늘리려고 할 때 오해의 소지가 있는 오류 메시지가 포함되어 있습니다. 함수는 영구 잠금인지 확인하기 전에 잠금이 만료되었는지 확인합니다:

```solidity
function increase_unlock_time(uint256 _tokenId, uint256 _lock_duration) external nonreentrant {
    --- SNIPPED ---
@>  require(_locked.end > block.timestamp, "Lock expired");
    require(_locked.amount > 0, "Nothing is locked");
    require(_locked.perpetuallyLocked == false, "Lock is perpetual");
    --- SNIPPED ---
}
```

영구 잠금은 `end` 시간이 `0`으로 설정되므로 함수는 `"Lock is perpetual"` 메시지 대신 항상 `"Lock expired"`와 함께 되돌려집니다.
