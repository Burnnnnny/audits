# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**curio-research/duper-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Curio 소개 (About Curio)

Curio Game 스마트 계약은 결과를 정산하는 것과 함께 게임을 생성할 수 있도록 합니다. 게임을 종료하고 예치된 금액을 분배할 수 있습니다.

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

_검토 커밋 해시_ - [9f5534d88eb71d73f1007540c1a6dbbf0e83d4a8](https://github.com/curio-research/duper-contracts/tree/9f5534d88eb71d73f1007540c1a6dbbf0e83d4a8)

_수정 검토 커밋 해시_ - [9e331ee3139a3f2614fdc70460f46ef728089e96](https://github.com/curio-research/duper-contracts/tree/9e331ee3139a3f2614fdc70460f46ef728089e96)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Game`

# 발견 사항 (Findings)

# [L-01] `earlyEndGame` 메서드는 존재하지 않는 게임 ID로 호출될 수 있음

현재 `earlyEndGame` 메서드는 게임이 끝나지 않았는지 검증합니다:

```solidity
if (currentSession.status == GameStatus.Ended) revert GameEnded();
```

더 합리적인 확인은 게임이 진행 중인지 검증하는 것입니다. 이는 더 엄격한 확인입니다. 현재의 느슨한 확인으로는 게임이 존재하지 않아 시작되지 않은 것으로 간주되므로 관리자가 빈 문자열 게임 ID로 호출할 수 있습니다. 다른 게임에 참여하지 않은 플레이어는 빈 문자열 게임 ID에 할당되므로 이 경로를 통해 사용자로부터 임의의 잔액을 차감할 수 있습니다.

# [L-02] 게임 종료 로직에서 플레이어 스테이크 및 지급에 대한 검증 없음

현재 관리자는 게임을 종료할 때 사용자 스테이크와 지급액을 임의로 제공할 수 있습니다. 게임이 시작되었을 때 플레이어 스테이크가 무엇이었는지, 플레이어 스테이크의 합계가 올바른지, 지급액의 합계도 올바른 값인지에 대한 온체인 검증이 없습니다.

따라서 오프체인 프로세스가 악용되면 사용자에게 임의의 지급을 하고 계약을 고갈시키는 것이 가능합니다. 지급이 주요 불변성을 깨지 않는지 검증하기 위해 온체인 확인을 추가하는 것을 강력히 권장합니다.

존재하는 유일한 검증은 다음과 같습니다:

```solidity
if (balance[player] < deduction) revert InsufficientBalance();
```

관리자가 임의의 입력을 제공할 수 있으므로 제 생각에는 이것은 실제로 최선의 확인이 아닙니다. 이것은 사용자 잔액을 0으로 설정하도록 조정하거나(되돌리고 DoS로 작동하는 대신) 위에서 제안한 대로 입력을 온체인에서 검증해야 합니다.

# [L-03] 플레이어는 임의의 금액으로 슬래시될 수 있음

`slashPlayer`는 관리자가 플레이어를 슬래시할 수 있도록 합니다.
이 기능은 플레이어가 부정행위를 할 때 사용하도록 되어 있습니다.

그러나 이 기능은 슬래시되는 금액을 제한(cap)하지 않습니다.
이 금액은 플레이어가 부정행위를 한 게임의 `minStake` 값으로 제한되어야 합니다.

# [L-04] 슬래시된 플레이어로 인해 `endGame`이 깨질 수 있음

플레이어가 돈을 잃고 잔액이 손실을 충당하지 못할 때 `endGame` 기능이 되돌려질(revert) 수 있습니다.

이는 플레이어가 슬래시될 때(잔액 감소) 발생할 가능성이 높습니다.
그런 다음 잔액 부족으로 인해 `endGame`이 되돌려집니다.

```solidity
    function endGame(string calldata gameId, PlayerPayout[] memory playerPayouts) external onlyAdmin whenNotPaused {
        ...

        for (uint256 i = 0; i < playerPayouts.length; i++) {
            ...

            if (playerPayout.payout > playerPayout.stake) {
                // Player won money case
                ...
            } else {
                // Player lost money case

                uint256 deduction = playerPayout.stake - playerPayout.payout;

                if (balance[player] < deduction) revert InsufficientBalance(); // @POC: balance can be lower than deduction, reverting the transaction

                // deduct money from players' balance
                balance[player] -= deduction;
            }

            ...
        }

        ...
    }
```

플레이어가 슬래시되면 플레이어는 게임에서 제외되어야 합니다.

# [L-05] `gameId`에 인쇄할 수 없는 문자가 포함될 수 있음

`gameId` 문자열을 등록할 때 이 문자열에 인쇄 가능한 문자만 포함되어 있는지 확인하는 검사가 없습니다.

이로 인해 인쇄할 때는 동일하지만 Solidity 표현에서는 다른 `gameId` 문자열을 등록할 수 있는 기능이 제공됩니다.

예를 들면 다음과 같습니다:

- `0461626364` 바이트 표현은 `abcd`로 표시됩니다.
- `056162636400` 바이트 표현도 `abcd`로 표시되지만 계약에서는 다른 gameId로 간주됩니다.

`startGame`은 문자열 매개변수에 인쇄 가능한 문자만 포함되어 있는지 확인해야 합니다. 이는 `gameId` 및 `gameType` 매개변수에 적용됩니다.

# [L-06] 소유자 설정자(setters)가 이벤트를 방출하지 않음

`updateWithdrawalPeriod` 및 `updateSparksTopUpPrice` 설정자는 소유자만 호출할 수 있습니다.

이 두 함수는 이벤트를 방출하지 않습니다. 이로 인해 인출 기간 및 스파크 충전 가격 업데이트를 모니터링할 수 없게 됩니다.

# [L-07] `emergencyEnd`가 이벤트를 방출하지 않음

`emergencyEnd` 함수는 이벤트를 방출하지 않습니다.

이로 인해 이 기능의 사용을 모니터링할 수 없게 됩니다.

# [L-08] 단일 인출에 대해 `StartWithdrawal`이 여러 번 방출될 수 있음

`startWithdrawal` 함수는 호출자의 주소, 금액 및 현재 타임스탬프에서 파생된 고유 식별자로 인출을 생성합니다.
그러나 함수는 이 고유 식별자가 이미 사용되었는지 확인하지 않습니다.

단일 블록에서 동일한 금액으로 `startWithdrawal`을 호출하면 이 확인 부족으로 인해 플레이어가 동일한 이벤트를 여러 번 방출할 수 있습니다. 이는 잠재적으로 오프체인 모니터링을 깨뜨릴 수 있습니다.

인출 식별자가 이미 사용 중이지 않은지 확인하는 검사를 수행해야 합니다.

```diff
@@ -89,6 +89,9 @@ contract Game is Ownable2StepUpgradeable, UUPSUpgradeable, PausableUpgradeable {
     /// @notice Thrown when player has topped up sparks
     error HasToppedUpSparks();

+    /// @notice Thrown when withdrawal already exist
+    error WithdrawalIdUsed();
+
     /*//////////////////////////////////////////////////////////////
                                 MODIFIERS
     //////////////////////////////////////////////////////////////*/
@@ -216,6 +219,7 @@ contract Game is Ownable2StepUpgradeable, UUPSUpgradeable, PausableUpgradeable {
         withdrawal.timestamp = block.timestamp;
         string memory withdrawalId = _generateWithdrawalId(withdrawal);

+        if (allWithdrawals[withdrawalId].timestamp != 0) revert WithdrawalIdUsed();
         allWithdrawals[withdrawalId] = withdrawal;

         if (address(withdrawalIdSet[msg.sender]) == address(0)) {

```

# [L-09] 사용자가 관리자가 아니더라도 `RemoveAdmin` 이벤트가 방출됨

`removeAdmin` 함수는 `admins` AddressSet 계약의 `remove` 함수를 호출합니다.
이 `AddressSet.remove` 함수는 항목이 존재하지 않을 때 되돌리지(revert) 않습니다.

```solidity
    function remove(address e) public onlyOwner {
        if (!includes(e)) return; // @POC: return and not revert

        ...
    }
```

관리자가 아닌 주소로 `Game.removeAdmin`을 호출하면 성공하고, 그렇지 않아야 할 때 `RemoveAdmin` 이벤트가 방출됩니다.

# [L-10] 게임이 없는 플레이어에게 `emergencyEnd`를 사용할 수 있음

`emergencyEnd` 함수는 게임이 없는 플레이어에게 사용할 수 있습니다.

함수는 플레이어가 현재 게임에 있는지 확인해야 합니다.

```diff
     /// @dev In emergency scenarios where players are locked, this function can be used to unlock player and pay
     function emergencyEnd(address player, uint256 payout) external onlyAdmin whenNotPaused {
+        if (_strEq(playerGameId[player], "")) revert PlayerNotInGame(player);
         playerGameId[player] = "";

         balance[player] += payout;
```

# [L-11] 계약이 일시 중지되었을 때 관리자가 여러 함수를 호출할 수 있음

계약이 일시 중지되었을 때 여러 관리자 함수를 여전히 호출할 수 있지만 다른 함수는 호출할 수 없습니다.

`startGame`, `earlyEndGame`, `emergencyEnd` 및 `endGame`은 계약이 일시 중지되었을 때 호출할 수 없지만
`giveCredit`, `depositTotalCredits`, `setInstantWithdraw` 및 `slashPlayer`는 호출할 수 있습니다.

# [L-12] 인출 기간 업데이트가 진행 중인 인출에 영향을 미침

`updateWithdrawalPeriod` 함수를 사용하면 소유자가 인출 기간을 수정할 수 있습니다.

그러나 인출 기간을 수정하면 새 인출뿐만 아니라 진행 중인 모든 인출에도 영향을 미칩니다.

# [L-13] 인출이 예상치 못한 기간을 가질 수 있음

사용자가 `startWithdrawal`을 호출하여 인출을 시작하면 블록 타임스탬프가 인출 구조체에 저장됩니다. 인출 기간이 지나면 사용자는 `finishWithdrawal`을 호출하여 현금화할 수 있습니다.

그러나 현재 로직의 문제점은 사용자가 인출을 시작한 시점과 인출을 마친 시점 사이에 `withdrawalPeriod` 변수가 변경될 수 있어 사용자에게 비결정적인 경험을 제공하고 스마트 계약에서 기대하는 투명성을 위반할 수 있다는 것입니다. 예를 들어 인출 시작 시 인출 기간은 1일일 수 있습니다. 1일 후 사용자가 인출을 마치러 돌아오지만, 인출이 시작된 이후 소유자가 인출 기간을 30일로 변경했습니다. 사용자는 인출 기간이 1일일 때 시작했음에도 불구하고 이제 29일을 더 기다려야 합니다.

인출 시작 시간을 인출 구조체에 저장하는 대신 잠금 해제 시간을 저장하십시오. 이렇게 하면 사용자가 인출을 시작할 때 잠금 해제 시간이 결정적입니다.

# [L-14] 플레이어는 관리자에게 가스 그리프(gas grief) 공격을 할 수 있음

언제든지 플레이어는 임의의 금액으로 `startWithdrawal`을 호출하여 인출을 시작할 수 있습니다. 사용자가 한 번에 인출할 수 있는 최소 금액과 동시에 시작할 수 있는 인출 수에는 제한이 없습니다.

따라서 사용자는 동시에 매우 적은 금액(예: 극단적인 경우 1 wei)의 인출을 대량으로 시작하고 완료하지 않은 채로 둘 수 있습니다. 이제 사용자가 게임에 참여하고 싶을 때마다 `startGame`을 호출하는 관리자는 사용자의 보류 중인 모든 인출을 반복하고 불필요한 양의 가스를 태워야 합니다. 이는 내부적으로 사용자의 보류 중인 모든 인출을 반복하는 `getWithdrawableBalance`가 호출되기 때문입니다. 사용자는 합법적으로 게임을 플레이하면서도 관리자에게 가스 그리프 공격을 하고 있습니다.

플레이어가 한 번에 할 수 있는 동시 인출 수를 제한(cap)하십시오. 예를 들어 한 번에 최대 5개의 보류 중인 인출(정확한 숫자는 논의 대상임)로 제한합니다.

# [L-15] 관리자가 게임 자금을 고갈시킬 수 있음

`emergencyEnd` 기능을 통해 관리자는 모든 플레이어(자신 포함)에게 임의의 양의 토큰을 할당할 수 있습니다.

그런 다음 `setInstantWithdraw`를 통해 이 플레이어를 즉시 인출자로 설정할 수 있습니다.

마지막으로 이 플레이어는 `startWithdrawal`을 통해 할당된 임의의 양의 토큰을 즉시 인출할 수 있습니다.

`emergencyEnd` 함수는 현재 게임에 참여 중인 플레이어에 대해서만 호출 가능해야 합니다.
또한 할당된 지급액은 이 플레이어 게임의 `minStake` 금액으로 제한되어야 합니다.

```diff
     /// @dev In emergency scenarios where players are locked, this function can be used to unlock player and pay
     function emergencyEnd(address player, uint256 payout) external onlyAdmin whenNotPaused {
+        string memory gameId = playerGameId[player];
+        if (_strEq(gameId, "")) revert PlayerNotInGame(player);
+        if (payout > games[gameId].minStake) revert ValueOutsideRange();
         playerGameId[player] = "";

         balance[player] += payout;

```

# [L-16] gameFee 계산이 승자에게 불공평함

모든 게임에서 모든 플레이어는 수수료율(퍼센트)에 따라 일정 금액의 수수료를 지불해야 합니다.

수수료는 `earlyEndGame()` 또는 `endGame()`을 통해 게임이 끝날 때 플레이어의 지급액을 통해 계산됩니다.

플레이어 수수료를 계산할 때 플레이어의 지급액이 높을수록 지불하는 수수료가 더 높은 것으로 보입니다. 게임의 패자는 지급액이 스테이크 금액보다 적으므로 비용을 지불할 필요가 없습니다.

```
    // Fetch game take rate
>           uint256 playerFee = (playerPayout.payout * gameFeeRate) / 100;
            gameFee += playerFee;
            playerPayout.payout -= playerFee;
```

이 테스트 케이스 시나리오는 패자가 지급액이 0이기 때문에 수수료를 지불할 필요가 없고 승자가 훨씬 더 높은 수수료를 지불해야 함을 보여줍니다.

```
struct PlayerPayout {
    address playerAddress;
    uint256 stake;
    uint256 payout;
}

    function testEndGame() public {
        ...
        PlayerPayout[] memory payouts = new PlayerPayout[](2);
>       payouts[0] = PlayerPayout(player1, 0, depositAmount * 2);
>       payouts[1] = PlayerPayout(player2, depositAmount * 2, 0); // we are taking twice what player2 has
```

의도한 것이 아니라면 게임 종료 시 대신 게임 시작 시 모든 사람이 균일한 기본 수수료를 지불하도록 권장합니다.
