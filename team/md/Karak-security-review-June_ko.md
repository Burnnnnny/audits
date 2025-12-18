# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **karak-network/karak-restaking** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

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

_검토 커밋 해시_ - [8aad9ad5592edec06528c5fc75a8eaee395850d2](https://github.com/karak-network/karak-restaking/tree/8aad9ad5592edec06528c5fc75a8eaee395850d2)

_수정 검토 커밋 해시_ - [0d59567becac7887b79ee70dcda0063a458ad791](https://github.com/karak-network/karak-restaking/tree/0d59567becac7887b79ee70dcda0063a458ad791)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `NativeNode`
- `NativeVault`
- `SlashStore`
- `NativeVaultLib`
- `BeaconProofsLib`
- `MerkleProofs`
- `Constants`
- `Errors`
- `Events`

# 발견 사항 (Findings)

# [H-01] 사용자가 인출을 통해 잔액 업데이트를 방지할 수 있음 (User can prevent balance updates by withdrawing)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`finishWithdraw()`가 호출되면 ETH 금액이 기본 노드 계약의 잔액에서 전송되지만 코드는 `creditedNodeETH`의 값을 감소시키지 않아 그 값이 실제보다 높아지고 `_startSnapshot()`이 언더플로우로 인해 되돌려집니다:

```solidity
        // Calculate unattributed node balance
        uint256 nodeBalanceWei = node.nodeAddress.balance - node.creditedNodeETH;
```

이 문제는 토큰을 인출하는 모든 사용자에게 발생하며 악의적인 사용자는 잔액 업데이트를 방지하기 위해 의도적으로 이를 수행할 수 있습니다 (비콘 체인에서 슬래싱될 때).

## 권장 사항

`finishWithdraw()`에서 `creditedNodeETH`의 값을 업데이트하십시오.

# [H-02] 슬래시 이벤트 발생 시 잔액 업데이트가 고장남 (Broken balance update if a slash event happens)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`_transferToSlashStore()`가 호출되면 슬래시 ETH 금액이 기본 노드 계약의 잔액에서 전송되지만 코드는 `creditedNodeETH`의 값을 감소시키지 않아 그 값이 실제보다 높아지고 `_startSnapshot()`이 언더플로우로 인해 되돌려집니다:

```solidity
        // Calculate unattributed node balance
        uint256 nodeBalanceWei = node.nodeAddress.balance - node.creditedNodeETH;
```

## 권장 사항

`_transferToSlashStore()`에서 `creditedNodeETH`의 값을 업데이트하십시오.

# [H-03] `activeValidatorCount`가 설정되거나 증가되지 않음 (`activeValidatorCount` is never set or increased)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`NativeVault` 계약에서 `Storage.ownerToNode[nodeOwner].activeValidatorCount`는 인출 자격 증명이 특정 노드를 가리키는 총 검증자 수를 추적합니다.

이 값은 `NativeVaultLib.validateSnapshotProof`에서 비콘 체인의 검증자 잔액이 0일 때 감소합니다.

```solidity
File: NativeVaultLib.sol

        if (newBalanceWei == 0) {
@>          self.ownerToNode[nodeOwner].activeValidatorCount--;
            validatorDetails.status = ValidatorStatus.WITHDRAWN;

            emit ValidatorWithdrawn(nodeOwner, nodeAddress, timestamp, validatorIndex);
        }
```

이 값은 `_startSnapshot`에서 `Snapshot` 구조체의 `remainingProofs` 필드를 설정하는 데 사용됩니다.

```solidity
File: NativeVault.sol

        NativeVaultLib.Snapshot memory snapshot = NativeVaultLib.Snapshot({
            parentBeaconBlockRoot: _getParentBlockRoot(uint64(block.timestamp)),
            nodeBalanceWei: nodeBalanceWei,
            balanceDeltaWei: 0,
@>          remainingProofs: node.activeValidatorCount
        });
```

그러나 `activeValidatorCount`는 결코 설정되거나 증가되지 않습니다. 결과적으로 `remainingProofs`는 항상 0으로 초기화되어 다음과 같은 결과를 초래합니다:

- `validateSnapshotProofs`는 항상 언더플로우 오류로 인해 되돌려집니다.

```solidity
File: NativeVault.sol

        for (uint256 i = 0; i < balanceProofs.length; i++) {
(...)
@>          snapshot.remainingProofs--;
            snapshot.balanceDeltaWei += balanceDeltaWei;
        }
```

- `_updateSnapshot`은 항상 `snapshot.remainingProofs == 0` 조건을 `true`로 평가합니다. 결과적으로 `node.creditedNodeETH`는 두 번 업데이트될 수 있습니다: `startSnapshot`에서 한 번, 그리고 빈 `balanceProofs` 배열로 호출된 `validateSnapshotProofs`에서 또 한 번.

```solidity
File: NativeVault.sol

@>      if (snapshot.remainingProofs == 0) {
            int256 totalDeltaWei = int256(snapshot.nodeBalanceWei) + snapshot.balanceDeltaWei;

@>          node.creditedNodeETH += snapshot.nodeBalanceWei;

            node.lastSnapshotTimestamp = node.currentSnapshotTimestamp;
            delete node.currentSnapshotTimestamp;
            delete node.currentSnapshot;

            _updateBalance(nodeOwner, totalDeltaWei);
            emit SnapshotFinished(nodeOwner, node.nodeAddress, node.lastSnapshotTimestamp, totalDeltaWei);
        } else {
            node.currentSnapshot = snapshot;
        }
```

## 권장 사항

`NativeVaultLib.validateWithdrawalCredentials`에 다음 줄을 추가하십시오:

```diff
        validatorDetails.status = NativeVaultLib.ValidatorStatus.ACTIVE;
        validatorDetails.validatorIndex = validatorFieldsProof.validatorProof.validatorIndex;
        validatorDetails.lastBalanceUpdateTimestamp = updateTimestamp;
        validatorDetails.restakedBalanceWei = restakedBalanceWei;
        self.ownerToNode[nodeOwner].validatorPubkeyHashToDetails[validatorPubkeyHash] = validatorDetails;
+       self.ownerToNode[nodeOwner].activeValidatorCount++;
```

# [H-04] `_increaseBalance()`가 예상보다 적은 지분을 주조함 (`_increaseBalance()` mints fewer shares than expected)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

노드 소유자의 잔액을 늘리기 위해 `NativeVault._increaseBalance` 함수가 호출됩니다. 이 함수는 총 자산을 늘리고, 주조할 지분을 계산하고, 노드 소유자를 위해 지분을 주조하고, 노드 소유자의 `totalRestakedETH`를 업데이트합니다.

그러나 작업 순서가 올바르지 않습니다. 주조할 지분을 계산하기 전에 `totalAssets`를 업데이트하면 수신된 지분 수가 예상보다 적어지기 때문입니다.

```solidity
File: NativeVault.sol

    function _increaseBalance(address _of, uint256 assets) internal {
        NativeVaultLib.Storage storage self = _state();
        if (assets + self.totalAssets > maxDeposit(_of)) revert DepositMoreThanMax();
 @>     self.totalAssets += assets;
        uint256 shares = convertToShares(assets);
        _mint(_of, shares);
        self.ownerToNode[_of].totalRestakedETH += assets;
        emit IncreasedBalance(self.ownerToNode[_of].totalRestakedETH);
    }
```

## 개념 증명 (Proof of concept)

금고의 상태는 다음과 같습니다:

- totalAssets: 32 ETH
- totalSupply: 32e18
- exchange rate: 1:1

Alice의 잔액이 32 ETH 증가하므로 그녀는 32e18 지분을 받아야 합니다. 그러나 그렇지 않습니다. 계산은 다음과 같습니다:

- totalAssets = 32 ETH + 32 ETH = 64 ETH
- shares = assets _ totalSupply / totalAssets = 32 ETH _ 32e18 / 64 ETH = 16e18
- totalSupply = 32e18 + 16e18 = 48e18

Alice는 16e18 지분을 받습니다. 이를 자산으로 변환하면 다음과 같습니다:

- assets = shares _ totalAssets / totalSupply = 16e18 _ 64 ETH / 48e18 = 21.33 ETH

Alice는 10.67 ETH를 잃었습니다.

## 권장 사항

```diff
    function _increaseBalance(address _of, uint256 assets) internal {
        NativeVaultLib.Storage storage self = _state();
        if (assets + self.totalAssets > maxDeposit(_of)) revert DepositMoreThanMax();
-       self.totalAssets += assets;
        uint256 shares = convertToShares(assets);
        _mint(_of, shares);
+       self.totalAssets += assets;
        self.ownerToNode[_of].totalRestakedETH += assets;
        emit IncreasedBalance(self.ownerToNode[_of].totalRestakedETH);
    }
```

# [H-05] `NativeVault` 토큰을 전송하면 일부 기능이 손상될 수 있음 (Transferring `NativeVault` tokens can break some functionalities)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`NativeVault` 토큰(지분)은 노드 소유자에서 다른 주소로 전송될 수 있습니다. 이는 프로토콜 동작에 다양한 문제를 일으킬 수 있습니다. 몇 가지 예는 다음과 같습니다:

1. 토큰 보낸 사람이 자신의 잔액은 줄었지만 `node.totalRestakedETH` 값은 줄어들지 않았으므로 원래보다 더 많은 자산을 슬래싱당할 수 있습니다.

```
File: NativeVault.sol

    function _transferToSlashStore(address nodeOwner) internal {
        NativeVaultLib.Storage storage self = _state();
        NativeVaultLib.NativeNode storage node = self.ownerToNode[nodeOwner];

        // slashed ETH = total restaked ETH (node + beacon) - share price equivalent ETH
  @>    uint256 slashedAssets = node.totalRestakedETH - convertToAssets(balanceOf(nodeOwner));
```

2. 토큰 수신자가 자신의 노드에 충분한 잔액이 있는 노드 소유자가 아닌 경우, 지분을 사용하여 인출하거나 다른 작업을 수행할 수 없어 지분이 쓸모없는 자산이 됩니다.

3. 수신자가 노드 소유자이고 프로토콜에 슬래싱 이벤트가 발생한 경우, 자신의 잔액은 늘어났지만 `node.totalRestakedETH` 값은 늘어나지 않았으므로 `slashedAssets` 계산이 언더플로우될 수 있습니다. 이는 비콘 체인의 검증자 중 하나에 슬래싱 이벤트가 발생했을 때 노드 소유자가 `validateExpiredSnapshot` 실행을 방지하는 데 잠재적으로 사용될 수 있습니다.

## 권장 사항

토큰 전송을 허용하지 않도록 `transfer` 및 `transferFrom` 함수를 재정의하십시오.

# [H-06] `finishWithdrawal()`이 인출 정보를 제거하지 않음 (`finishWithdrawal()` doesn't remove withdrawal info)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

자금을 인출하려면 사용자는 인출 요청을 해야 합니다. 이는 사용자가 인출하려는 금액에 대한 정보를 저장하고 요청은 실행을 위해 대기열에 추가됩니다.

`MIN_WITHDRAWAL_DELAY`가 지나면 사용자는 자금을 얻기 위해 `finishWithdrawal`을 호출할 수 있습니다. 사용자가 `MIN_WITHDRAWAL_DELAY`를 기다려야 하는 이유는 사용자가 슬래시 조치를 앞질러 슬래싱을 피할 수 없도록 하기 위함입니다.

문제는 `finishWithdrawal` 함수가 실행 후 인출에 대한 정보를 삭제하지 않아 사용자가 원하는 만큼 여러 번 실행할 수 있고 더 이상 `MIN_WITHDRAWAL_DELAY`를 기다릴 필요가 없다는 것입니다.

## 권장 사항

실행 후 인출 정보를 삭제하십시오.

# [H-07] 코드는 상태 루트를 사용하여 인출 자격 증명을 검증해야 함 (Code should use state root to validate withdraw credentials)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`validateWithdrawalCredentials()` 함수에서 코드는 상태 루트의 증명을 검증하지만 그 후 블록의 루트를 사용하여 검증자의 정보를 확인합니다:

```solidity
            totalRestakedWei += self.validateWithdrawalCredentials(
                nodeOwner,
                beaconStateRootProof.timestamp,
                _getParentBlockRoot(beaconStateRootProof.timestamp),
                validatorFieldsProofs[i]
            );
```

이 문제로 인해 시스템에 검증자를 추가할 수 없습니다.

## 권장 사항

검증자 정보는 상태 루트 내부에 저장되며 코드는 `validateWithdrawalCredentials()`를 호출할 때 `beaconStateRoot`를 사용해야 합니다.

# [M-01] 사용자가 슬래싱된 토큰 지불을 거부할 수 있음 (The User can deny paying slashed tokens)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

슬래시 토큰은 사용자가 또는 스냅샷이 만료되었을 때 다른 사람이 `startSnapshot()`을 호출할 때만 전송됩니다. 문제는 사용자에게 활성 검증자가 없는 경우 `validateExpiredSnapshot()`을 호출할 수 없고 슬래시 토큰이 기본 노드에 갇히게 된다는 것입니다. 이것은 POC입니다:

1. 사용자1은 모든 검증자의 잔액을 기본 노드로 인출하고 스냅샷을 업데이트합니다.
2. 슬래시 이벤트가 발생하고 기본 금고의 사용자가 슬래싱됩니다.
3. 이제 사용자1이 `startSnapshot()`을 호출하지 않고 스냅샷이 만료되면 다른 사람이 `validateExpiredSnapshot()`을 호출할 수 없고 슬래시 토큰이 전송되지 않습니다.

다른 사람이 `validateExpiredSnapshot()`을 호출할 수 없는 이유는 다음 확인 때문입니다:

```solidity
        if (validatorDetails.status != NativeVaultLib.ValidatorStatus.ACTIVE) revert ValidatorNotActive();
```

## 권장 사항

`validateExpiredSnapshot()`의 확인을 수정하고 이전 스냅샷의 타임스탬프 만료 시간을 확인하십시오.

# [M-02] 운영자의 관리자가 금고 작업을 일시 중지할 수 있음 (Operator's manager can pause vault actions)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

NativeVault가 생성되면 관리자는 다양한 작업을 일시 중지할 수 있는 역할을 부여받습니다. 이러한 권한으로 관리자는 프로토콜의 일부 기능을 중단시킬 수 있습니다.
이러한 작업의 예로 슬래싱이 있습니다. 관리자가 이를 일시 중지하면 핵심 계약은 운영자의 자금을 슬래싱할 수 없습니다.
또 다른 예: 관리자는 사용자의 인출을 금지할 수 있습니다.

## 권장 사항

운영자가 완전히 신뢰할 수 있는 주체가 아니므로 관리자 역할을 제거하는 것을 고려해보십시오.

# [M-03] 잘못된 인출 주소 확인 (Wrong withdrawal address check)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`validateWithdrawalCredentials` 함수를 사용하여 소유자에게 새 검증자가 추가될 때 인출 수신자 주소가 `NativaVault`와 같은지 확인합니다.

```solidity
 if (
            BeaconProofs.getWithdrawalCredentials(validatorFieldsProof.validatorFields)
                != bytes32(abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(this)))
        ) {
            revert WithdrawalCredentialsMismatchWithNode();
        }
```

프로토콜은 모든 보상이 검증자 소유자의 `NativeNode`로 전송되어야 하므로 이것은 올바르지 않으며, 이것이 이 주소를 인출 수령자로 확인해야 하는 이유입니다.

## 권장 사항

인출 수령자가 검증자 소유자의 NativeNode 주소인지 확인하십시오.

# [M-04] 현재 블록에 새 검증자를 추가하여 잔액 업데이트 방지 (Preventing balance updates by adding a new validator in the current block)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

사용자 검증자의 잔액은 새 스냅샷을 생성하고 검증자의 잔액을 증명하여 업데이트할 수 있습니다. 사용자가 제때 새 스냅샷을 시작하지 않으면 누구나 해당 사용자를 위해 스냅샷을 시작하고 사용자 잔액을 업데이트할 수 있습니다. 사용자가 비콘 체인에서 슬래싱당하는 경우 잔액을 업데이트하지 않을 유인이 있습니다. 사용자는 이 공격을 수행하여 스냅샷 확정 및 스냅샷 생성 프로세스를 차단하고 잔액 업데이트를 중지할 수 있습니다. 문제는 `validateSnapshotProofs()` 코드가 스냅샷 생성 후 검증자 잔액이 업데이트되는 경우 검증자 잔액 업데이트를 허용하지 않는다는 것입니다:

```solidity
            if (validatorDetails.lastBalanceUpdateTimestamp >= node.currentSnapshotTimestamp) {
                revert ValidatorAlreadyProved();
            }
```

그리고 스냅샷은 `remainingProofs`가 0일 때만 확정됩니다. 따라서 이것은 공격 POC입니다:

1. 비콘 체인에서 슬래싱된 사용자1은 잔액 업데이트를 방지하려고 합니다.
2. 사용자1은 새 검증자를 생성하고 동일한 이더리움 블록에서 `validateWithdrawalCredentials()` 및 `startSnapshot()`을 호출합니다.
3. 새 검증자의 결과로: `validatorDetails.lastBalanceUpdateTimestamp`는 `node.currentSnapshotTimestamp`와 같습니다.
4. 새 검증자가 `remainingProofs`에 포함되었고 현재 스냅샷에 대해 잔액을 증명할 수 없으므로 `remainingProofs`는 0에 도달하지 않고 현재 스냅샷은 완료되지 않으며 잔액 업데이트 프로세스가 중단됩니다.

## 권장 사항

`validateWithdrawalCredentials()`를 변경하고 현재 블록의 타임스탬프에 대해 새 검증자 생성을 허용하지 않는 것이 가장 좋습니다.
`validateSnapshotProofs()`에서 위 조건을 `validatorDetails.lastBalanceUpdateTimestamp > node.currentSnapshotTimestamp`로 변경하면 또 다른 문제가 발생합니다.

# [L-01] `NativeVault` 토큰을 비 노드 소유자에게 전송하면 쓸모없는 자산이 됨 (Transferring `NativeVault` tokens to non-node owners turns them into useless assets)

`NativeVault` 토큰(지분)은 노드 소유자에서 다른 주소로 전송될 수 있습니다. 그러나 수신자 주소가 자신의 노드에 충분한 잔액이 있는 노드 소유자가 아닌 경우, 지분을 사용하여 인출하거나 다른 작업을 수행할 수 없어 지분이 쓸모없는 자산이 됩니다.

토큰 전송을 허용하지 않도록 `transfer` 및 `transferFrom` 함수를 재정의하는 것이 좋습니다.

# [L-02] 노드 소유자는 인출을 시작하고 슬래싱의 경우에만 실행할 수 있음 (Node owners can start withdrawal and execute it just in case of slashing)

노드 소유자는 `NativeVault.startWithdrawal`을 호출하여 인출을 대기열에 넣고 `MIN_WITHDRAW_DELAY`가 지난 후 `NativeVault.finishWithdrawal`을 호출하여 자금을 인출할 수 있습니다. 문서에 명시된 대로 "이 인출 대기열은 나쁜 행위자가 슬래싱 가능한 행동에 빠지고 즉시 자금을 인출하는 것을 방지하기 위한 것입니다".

그러나 `startWithdrawal`은 사용자의 잔액에서 금액을 차감하지 않으며 인출 완료 기한을 부과하지도 않습니다. 즉, 노드 소유자는 계속해서 보상을 축적하고 `MIN_WITHDRAW_DELAY`가 지나면 언제든지 `finishWithdrawal`을 호출할 수 있습니다.

노드 소유자는 `startWithdrawal`을 호출하고 인출을 무기한 대기 상태로 둘 수 있으므로 슬래싱 이벤트가 발생하기 직전에 `finishWithdrawal`을 호출하여 슬래싱 이벤트를 앞질러 실행할 수 있습니다.

게다가 대기 중인 인출 수에는 제한이 없으므로 비콘 체인에서 보상을 받을 때마다 업데이트된 금액으로 `startWithdrawal`을 계속 호출할 수 있습니다.

우리는 다음을 권장합니다:

- 이미 진행 중인 인출이 있는 경우 노드 소유자가 새 인출을 대기열에 넣지 못하도록 하십시오.

- 인출 기한을 구현하여 노드 소유자가 `finishWithdrawal`을 호출할 수 있는 시간을 제한하고 그 후에는 인출이 만료된 것으로 간주되도록 하십시오.

이 기한은 `MIN_WITHDRAW_DELAY`에 비해 너무 길지 않은 인출 창을 제공해야 합니다. 그 이유는 다음과 같습니다:

`MIN_WITHDRAW_DELAY`가 7일이고 인출 기한이 `block.timestamp + 21일`인 경우 21일마다 `startWithdrawal`을 호출하면 노드 소유자는 시간의 2/3 동안 자금을 인출할 수 있어 슬래싱 이벤트를 앞질러 실행할 수 있습니다.
