# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**curio-research/flagship-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

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

_검토 커밋 해시_ - [19d6cd40f8a28ec842be9a52c91a1fad76d01df2](https://github.com/curio-research/flagship-contracts/tree/19d6cd40f8a28ec842be9a52c91a1fad76d01df2)

_수정 검토 커밋 해시_ - [25a268fd7a5ecf0dc576f3b1a165fead72ceafa2](https://github.com/curio-research/flagship-contracts/tree/25a268fd7a5ecf0dc576f3b1a165fead72ceafa2)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Game`

# 발견 사항 (Findings)

# [C-01] 게임 수수료 처리 오류

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`endGame` 함수는 관리자에 의해 게임을 종료하기 위해 호출됩니다. 관리자는 참여자 목록과 그들의 지급액을 제공하고 함수는 모든 참여자를 반복합니다. 사용자가 이기는 경우 게임은 보상에서 수수료를 받습니다. 이 수수료는 `gameFee` 변수에 누적됩니다. 새로운 사용자마다 증가하며 실제 사용자의 승리 금액은 `unsignedWinLossAmount - gameFee`입니다.

```solidity
                uint256 gameTakeRate = takeRate[currentSession.gameType];
                gameFee += (playerPayout.unsignedWinLossAmount * gameTakeRate) / 100;

                uint256 adjustedPlayerPayout = playerPayout.unsignedWinLossAmount - gameFee;
```

`gameFee`는 전체 루프에 사용되는 변수이며 각 주기마다 증가합니다. 새로운 주기마다 재설정되지 않습니다.
결과적으로 `adjustedPlayerPayout`이 올바르지 않게 되며, `gameFee > unsignedWinLossAmount`일 때 되돌릴(revert) 수 있습니다. 그러면 게임이 완료되지 않고 사용자는 자금을 돌려받을 수 없습니다.

또한 함수가 되돌리지 않더라도 `gameFee`와 `adjustedPlayerPayout`이 올바르지 않아 지급 분배가 잘못될 수 있습니다.

## 권장 사항

우선 모든 루프 주기 후에 gameFee를 지불해야 합니다. 또한 다음과 같은 변경이 필요합니다:

```solidity
                uint256 gameTakeRate = takeRate[currentSession.gameType];
                uint256 userFee = (playerPayout.unsignedWinLossAmount * gameTakeRate) / 100;
                gameFee += userFee;

                uint256 adjustedPlayerPayout = playerPayout.unsignedWinLossAmount - userFee;
```

# [C-02] 승자의 지급액이 잘못 계산됨

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

승자가 돈을 이기면 함수가 승자의 지급액을 잘못 계산하여 승자에게 의도한 것보다 훨씬 더 많은 지급액을 제공합니다.

관리자가 `endGame()`을 호출할 때 플레이어가 게임에서 이기면 총 인출 금액이 지급액으로 업데이트됩니다.

```
            if (playerPayout.payout > playerPayout.stake) {
                // Player won money case

                // update player revenue/in-game balance
>               balance[player] += playerPayout.payout;
            } else {
```

그러나 이 추가 `balance[player] += playerPayout.payout;`은 잘못된 금액을 더해 의도한 것보다 더 많은 지급액을 초래합니다.

예를 들어 플레이어 A와 B 두 명이 있습니다. 두 플레이어 모두 1 ether를 스테이킹합니다. 게임이 끝나면 플레이어 A는 0.5 ether를 이기고 플레이어 B는 0.5 ether를 잃습니다. 지급액(Payout)은 플레이어가 마지막에 갖는 돈의 양을 나타냅니다.

플레이어 A의 경우 `stake`는 1 ether이고 `payout`은 1.5 ether입니다.
플레이어 B의 경우 `stake`는 1 ether이고 `payout`은 0.5 ether입니다.

게임 수수료를 할인하고 `endGame()` 함수를 따르면 플레이어 A의 잔액은 1 + 1.5 ether인 2.5 ether가 됩니다. 즉, 그는 2.5 ether를 인출할 수 있습니다.

실제로 그의 잔액은 1.5 ether여야 합니다. 현재 계산대로 `withdraw()`를 호출하면 다른 승자와 패자의 비용으로 의도한 것보다 더 많은 ether를 얻을 수 있습니다.

## 권장 사항

이 `balance[player] += playerPayout.payout;` 추가를 업데이트하십시오:

```
            if (playerPayout.payout > playerPayout.stake) {
                // Player won money case

                // update player revenue/in-game balance
>               balance[player] += (playerPayout.payout - playerPayout.stake);
            } else {
```

`playerPayout.payout - playerPayout.stake`는 플레이어의 승리 금액을 가져옵니다. 이것을 플레이어가 가진 원래 합계에 더하십시오. 이렇게 하면 위의 예를 따라 플레이어 A의 잔액은 이제 1 ether + (1.5 - 1) ether인 1.5 ether가 됩니다.

# [C-03] `endGame` 함수의 잘못된 논리 확인

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`Game` 계약의 `endGame` 함수는 잘못된 논리 확인으로 인해 올바르게 작동하지 않습니다. 게임이 끝날 때 지급을 처리하도록 의도된 이 함수는 제공된 `gameId`의 해시와 각 플레이어의 `gameId`를 비교할 때 `!=` 대신 `==`를 잘못 사용합니다. 이로 인해 함수는 종료하려는 게임에 실제로 참여하는 모든 플레이어를 건너뜁니다. 결과적으로 플레이어 지급이나 게임 상태 업데이트가 처리되지 않으며, 성공적인 실행 상태를 반환함에도 불구하고 아무런 의미 있는 조치 없이 트랜잭션이 완료됩니다. 이로 인해 게임 상태가 `InProgress`일 때 인출할 수 없으므로 플레이어의 자금이 계약에 잠기게 됩니다.

## 권장 사항

`endGame` 함수의 비교 연산자를 `==`에서 `!=`로 변경하여 함수가 지정된 게임의 일부인 플레이어를 처리하도록 하십시오. 이 수정으로 논리가 수정되어 함수가 플레이어 잔액 조정 및 게임 상태 업데이트와 같은 의도된 작업을 올바르게 수행할 수 있습니다.

```diff
-if (keccak256(abi.encodePacked(playerGameId[player])) == keccak256(abi.encodePacked(gameId))) {
+if (keccak256(abi.encodePacked(playerGameId[player])) != keccak256(abi.encodePacked(gameId))) {
     continue;
 }
```

# [H-01] `earlyEndGame`에서 게임 수수료 잠김

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`earlyEndGame` 함수에서 플레이어의 지급액에서 공제된 게임 수수료가 이후 재무부 주소로 전송되지 않는 문제가 있습니다. 대신 수수료는 플레이어의 지급액에서 차감되어 계약에 남습니다. 이로 인해 수집된 수수료를 재무부로 전송할 수 있는 전용 방법 없이 자금이 계약 내에 잠기게 됩니다.

## 권장 사항

`earlyEndGame` 함수 내에 게임 수수료가 플레이어의 지급액에서 공제된 후 재무부 주소로 전송되도록 메커니즘을 구현하는 것이 중요합니다. 수수료 공제 로직 후 전송 호출을 추가하여 이를 달성할 수 있습니다:

```diff
function earlyEndGame(string calldata gameId, PlayerPayout memory playerPayout) external onlyAdmin whenNotPaused {
    ...

    uint256 gameTakeRate = takeRate[currentSession.gameType];
    uint256 gameFee = (playerPayout.payout * gameTakeRate) / 100;
    playerPayout.payout -= gameFee; // @audit gamefee will be stuck ?

    ...

    // Deduct money from players' balance
    balance[player] -= deduction;

    // Set the player game id to empty
    playerGameId[player] = "";

+    // Deposit game fee to reward pool
+   (bool sent,) = treasury.call{value: gameFee}("");
+   if (!sent) {
+        revert ValueTransferFailed();
+   }

    emit EarlyEndGame(playerPayout);
}
```

# [M-01] 액세스할 수 없는 일시 중지 및 일시 중지 해제 함수

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Game` 계약은 비상 상황이나 유지 관리를 위해 계약 기능을 일시 중지하고 해제하도록 설계된 기능인 `PausableUpgradeable`을 상속합니다. 그러나 계약은 `pause` 및 `unpause` 함수를 계약 관리자에게 노출하지 않습니다. 결과적으로 일시 중지 기능을 상속함에도 불구하고 관리자는 필요할 때 계약의 운영을 일시 중지하거나 재개하기 위해 이러한 중요한 제어를 사용할 수 없으며, 즉각적인 개입이 필요한 기간 동안 문제로 이어질 수 있습니다.

## 권장 사항

`PausableUpgradeable` 상속의 전체 기능을 활용하고 계약의 운영 보안 및 유연성을 향상시키려면 `Game` 계약에서 `pause` 및 `unpause` 함수를 노출하는 것이 좋습니다. 계약 소유자 또는 기타 승인된 역할이 액세스할 수 있어야 운영 요구 사항이나 비상 상황에 효과적으로 대응할 수 있습니다:

```solidity
    /**
     * @dev Pauses all functions affected by `whenNotPaused`.
     */
    function pause() public onlyOwner {
        _pause();
    }

    /**
     * @dev Unpauses all functions affected by `whenNotPaused`.
     */
    function unpause() public onlyOwner {
        _unpause();
    }
```

# [L-01] earlyEndGame은 동일한 사용자에 대해 여러 번 호출될 수 있음

`earlyEndGame` 함수는 게임이 끝나기 전에 사용자가 패배했을 때 관리자가 호출할 수 있습니다. 이 경우 사용자는 일정 금액의 자금을 잃고 잔액에서 차감됩니다.

```solidity
        address player = playerPayout.playerAddress;
        if (balance[player] < playerPayout.unsignedWinLossAmount) {
            revert InsufficientBalance();
        }

        // Deduct money from players' balance
        balance[player] -= playerPayout.unsignedWinLossAmount;
```

여기에는 2가지 문제가 있습니다:

- 함수는 사용자가 관리자가 제공한 게임에 참여하는지 확인하지 않습니다.
- 함수는 사용자가 이미 슬래시되지 않았는지 확인하지 않습니다.

이 두 경우 모두 사용자가 현재 제공된 게임에 참여한다는 확인으로 해결됩니다.

# [L-02] updateGameTakeRate는 현재 게임에 영향을 미침

`updateGameTakeRate` 함수는 소유자가 게임 유형에 대한 수수료율을 변경할 수 있도록 합니다. 이 유형의 게임 인스턴스가 현재 플레이 중인 경우 사용자의 지급액에 영향을 미칩니다. 제안으로 각 게임 세션에 대한 수수료율을 저장할 수 있습니다.

# [L-03] 게임은 빈 gameId로 시작되어서는 안 됨

`startGame` 함수에는 `gameId` 매개변수가 있습니다. 이 게임 ID는 게임에 참여하는 각 사용자에게 설정됩니다: `playerGameId[player] = gameId;`

함수에는 제공된 `gameId`가 비어 있지 않은지 확인하는 검사가 없습니다. 비어 있는 경우 `isPlayerInGame` 기능이 작동하지 않아 사용자가 게임 중에 인출하고 여러 세션에 참여할 수 있습니다.

# [L-04] 슬래시 금액은 minStake보다 클 수 있음

새 게임이 시작되면 함수는 각 사용자가 참여하기 위해 최소 `minStake` 금액의 자금을 가지고 있는지 확인합니다.

```solidity
            if (balance[player] < minStake) {
                revert InsufficientStake();
            }
```

게임이 완료되면 관리자는 `unsignedWinLossAmount`를 제공하는데, 이는 사용자가 패배할 경우의 손실 금액입니다.
이 금액이 시작 시 사용된 `minStake`보다 크지 않은지 확인하는 검사가 없으므로 사용자는 게임에 스테이킹한 것보다 더 많이 잃을 수 있습니다.

# [L-05] 기본 게임 상태가 "InProgress"로 설정됨

계약의 `GameStatus` 열거형은 열거형의 첫 번째 요소로 위치하기 때문에 모든 새 `GameSession` 인스턴스를 기본적으로 `InProgress`로 설정합니다. 이 기본 동작은 초기화되지 않은 게임 세션이 활성 상태인 것처럼 보이게 하므로 모호성을 초래할 수 있습니다. 일반적으로 초기화되지 않았거나 새로 생성된 게임 세션은 본질적으로 활성 상태를 소유해서는 안 됩니다. 이는 세션의 실제 상태에 대한 잘못된 해석으로 이어질 수 있기 때문입니다.

`NotStarted` 또는 `Uninitialized`와 같은 새 열거형 요소를 도입하고 `GameStatus` 열거형의 첫 번째 요소로 배치하는 것이 좋습니다. 이 변경으로 새로 인스턴스화된 모든 게임 세션이 명시적으로 시작되지 않았음을 나타내는 중립 상태로 기본 설정되도록 합니다:

```solidity
enum GameStatus {
    NotStarted, // Default state for new sessions
    InProgress,
    Ended
}
```

이 조정은 게임 세션 상태의 명확성과 정확성을 향상시켜 계약 내에서 게임 수명 주기 상태를 올바르게 관리하고 추적하는 데 도움이 됩니다.

# [L-06] `endGame` 함수에서 게임 상태 확인 누락

`endGame` 함수는 게임을 종료하기 전에 게임이 현재 진행 중인지 확인하는 데 필요한 검사가 빠져 있습니다. 이 간과로 인해 게임을 여러 번 종료하거나 초기화되지 않은 게임을 종료할 수 있는 가능성이 있어 게임 관리의 불일치 및 데이터 처리의 잠재적 오류로 이어질 수 있습니다.

이 문제를 해결하려면 `endGame` 함수의 시작 부분에 조건문을 추가하여 게임의 현재 상태를 확인하십시오. 함수는 게임이 실제로 진행 중인 경우에만 종료 프로세스가 진행되도록 허용해야 합니다. 게임이 진행 중이 아닌 경우 불필요한 작업을 방지하기 위해 함수가 되돌려야 합니다:

```diff
function endGame(string calldata gameId, PlayerPayout[] calldata playerPayouts, uint256 pot)
    external
    onlyAdmin
    whenNotPaused
{
    GameSession memory currentSession = games[gameId];

+    // Verify game is in progress before marking as ended
+    if (currentSession.status != GameStatus.InProgress) {
+        revert("Game is not in progress or already ended");
+    }

    // Mark game as ended status
    games[gameId].status = GameStatus.Ended;
}
```

# [L-07] `init` 함수의 불완전한 초기화

계약의 `init` 함수는 상속된 모든 계약, 특히 `UUPSUpgradeable` 및 `PausableUpgradeable`을 올바르게 초기화하지 않습니다. 현재 이 함수는 `__Ownable2Step_init_unchained`만 호출하고 다른 두 업그레이드 가능한 계약에 대한 표준 초기화 프로그램을 우회합니다. 이것이 즉각적인 위험을 초래하지는 않지만 업그레이드 가능한 계약 사용에 대한 모범 사례에서 벗어납니다.

모범 사례에 부합하고 계약의 모든 측면이 올바르게 초기화되도록 하려면 `Ownable2StepUpgradeable`의 기존 초기화와 함께 `__Pausable_init` 및 `__UUPSUpgradeable_init`에 대한 호출을 포함하도록 `init` 함수를 수정하십시오.

# [L-08] `earlyEndGame` 함수에서 플레이어 검증 부족

`earlyEndGame` 함수는 현재 게임 상태 및 플레이어 잔액 업데이트를 진행하기 전에 플레이어가 실제로 게임의 일부인지 확인하지 않습니다. 이러한 검증 부족으로 인해 비참여 플레이어의 데이터가 함수에 전달될 경우 잘못된 업데이트가 허용될 수 있지만, 게임 서버에 대한 신뢰 가정으로 인해 발생 가능성과 영향은 낮을 수 있습니다.

의도하지 않은 상태 변경의 위험을 완화하고 계약의 견고성을 향상시키려면 상태 업데이트 또는 잔액 수정심을 실행하기 전에 플레이어의 게임 참여를 확인하는 검사를 포함하는 것이 좋습니다:

```diff
    // When a player quits or is defeated before the game ends
    function earlyEndGame(string calldata gameId, PlayerPayout memory playerPayout) external onlyAdmin whenNotPaused {
        GameSession memory currentSession = games[gameId];
        if (currentSession.status == GameStatus.Ended) {
            revert GameEnded();
        }

        address player = playerPayout.playerAddress;

+       if (keccak256(abi.encodePacked(playerGameId[player])) != keccak256(abi.encodePacked(gameId))) {
+           // Player is not in the game - they may have ended early and/or joined another game
+           revert PlayerNotInGame();
+       }
```

# [L-09] updateGameTakeRate()에 상한선이 없음

`updateGameTakeRate()`는 모든 게임 후에 재무부에 제공되는 `gameFee`의 비율을 계산합니다. 이 값은 업데이트될 수 있으며, `updateGameTakeRate()` 함수에는 새 요율이 될 수 있는 것에 대한 제한이 없습니다.

```
 function updateGameTakeRate(string calldata gameMode, uint256 _gameTakeRate) external onlyOwner {
        takeRate[gameMode] = _gameTakeRate;

        emit UpdateGameTakeRate(gameMode, _gameTakeRate);
    }
```

비율이 >100으로 설정되면 발생할 수 있는 계산 중 불행한 오류를 방지하기 위해 제한을 최대 x%(20%)로 제한(cap)하십시오.

```
 function updateGameTakeRate(string calldata gameMode, uint256 _gameTakeRate) external onlyOwner {
>     require(_gameTakeRate < 20, maximum 20% fees);
        takeRate[gameMode] = _gameTakeRate;

        emit UpdateGameTakeRate(gameMode, _gameTakeRate);
    }
```

# [L-10] 새 게임 시작 시 `games`가 재정의되지 않도록 보장

관리자가 `startGame()`을 호출할 때 문자열 데이터 유형의 `gameId`를 선택하고 현재 `gameSession`으로 설정합니다.

```
    function startGame(
>       string calldata gameId,
        uint256 minStake,
        string calldata gameType,
        address[] calldata playerAddresses
    ) external onlyAdmin whenNotPaused {
           ...
>        games[gameId] = gameSession;
```

관리자가 다른 게임을 생성할 때 `gameId`가 재사용되지 않고 `games[gameId]`에 저장된 `gameSession`이 재정의되지 않도록 충분한 검사를 수행하십시오.
