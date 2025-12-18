# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**primodiumxyz/primodium-empires-audit-pashov** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Primodium 정보

이 프로토콜은 승리한 제국에서의 포인트에 따라 플레이어에게 보상을 제공하고, 인출을 위한 자금을 분배하며, 플레이어가 포인트를 잠가 게임 플레이에 영향을 줄 수 있도록 합니다. 가격은 각 거래에 따라 동적으로 조정되며, 동점 시나리오에서는 의사 난수 함수가 승자를 선택합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [10ee58e648e0d457cec789443a9589dd83b6b307](https://github.com/primodiumxyz/primodium-empires-audit-pashov/tree/10ee58e648e0d457cec789443a9589dd83b6b307)

_수정 검토 커밋 해시_ - [f757766f191be09b9f842ae1345a573b12392b22](https://github.com/primodiumxyz/primodium-empires-audit-pashov/tree/f757766f191be09b9f842ae1345a573b12392b22)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `PointsMap`
- `InitPrice`
- `LibOverride`
- `LibPoints`
- `LibPrice`
- `WithdrawRakeSystem`
- `RewardsSystem`
- `ResetSystem`
- `OverrideShipSystem`
- `OverrideShieldSystem`
- `OverrideShieldEaterSystem`
- `OverridePointsSystem`
- `OverrideMagnetsSystem`
- `OverrideAirdropSystem`
- `OverrideAcidSystem`
- `EmpiresSystem`
- `AdminSystem`
- `ResetClearLoopSubsystem`
- `PayoutManager.sol`

# 발견 사항

# [C-01] 게임이 초기화되면 플레이어가 수익금을 인출하지 못할 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

팟(pot)의 자금은 플레이어가 여유롭게 수익금을 인출할 수 있도록 `PayoutManager` 계약으로 전송되어야 합니다. 그러나 현재 구현에서는 자금이 `PayoutManager` 계약으로 전송되지 않으므로 수익금 인출은 `RewardsSystem.withdrawEarnings` 함수에 의해 처리됩니다.

문제는 `RewardsSystem.withdrawEarnings` 함수는 게임이 끝난 경우에만 호출할 수 있다는 것입니다. 즉, 플레이어는 게임 종료 후 다음 게임이 시작되기 전까지만 수익금을 인출할 수 있습니다. 게임이 초기화되면 플레이어는 수익금을 인출할 수 없습니다.

`resetGame` 함수에 확인 절차가 부족하여 누구나 언제든지 게임을 초기화할 수 있다는 또 다른 보고된 문제가 있습니다. 그러나 해당 문제를 픽스하더라도 게임이 초기화되면 플레이어는 여전히 수익금을 인출할 수 없습니다.

## 권장 사항

필요한 조건이 충족되면 게임을 종료하고 자금을 `PayoutManager` 계약으로 보내는 새로운 시스템으로 `RewardsSystem`을 교체하십시오.

# [C-02] 잠긴 포인트에 해당하는 팟의 일부가 플레이어에게 분배되지 않음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`RewardsSystem` 계약의 `withdrawEarnings` 함수는 승리한 제국에서 플레이어가 가진 포인트 수에 따라 플레이어에게 분배할 팟의 금액을 계산합니다. 다음은 함수 단계 중 일부입니다.

1. 승리한 제국에 발행된 총 포인트를 가져옵니다(131행).
2. 승리한 제국에서 플레이어의 포인트를 가져오며(136행), 잠긴 포인트는 제외합니다(137행).
3. 포인트에 따라 팟에서 플레이어의 몫을 계산합니다(142행).

```solidity
File: RewardsSystem.sol

125:   function withdrawEarnings() public _onlyGameOver {
126:     EEmpire winningEmpire = WinningEmpire.get();
127:     require(winningEmpire != EEmpire.NULL, "[RewardsSystem] No empire has won the game");
128:
129:     bytes32 playerId = addressToId(_msgSender());
130:
131:     uint256 empirePoints = Empire.getPointsIssued(winningEmpire);
132:     if (empirePoints == 0) {
133:       return;
134:     }
135:
136:     uint256 playerEmpirePoints = PointsMap.getValue(winningEmpire, playerId) -
137:       PointsMap.getLockedPoints(winningEmpire, playerId);
138:
139:     if (playerEmpirePoints == 0) return;
140:
141:     uint256 pot = (Balances.get(EMPIRES_NAMESPACE_ID));
142:     uint256 playerPot = (pot * playerEmpirePoints) / empirePoints;
143:
144:     PlayersMap.setGain(playerId, PlayersMap.get(playerId).gain + playerPot);
145:     PointsMap.remove(winningEmpire, playerId);
146:
147:     IWorld(_world()).transferBalanceToAddress(EMPIRES_NAMESPACE_ID, _msgSender(), playerPot);
145: 148:   }
```

문제는 `empirePoints`에는 잠긴 포인트와 잠금 해제된 포인트가 모두 포함되는 반면 `playerEmpirePoints`에는 잠금 해제된 포인트만 포함된다는 것입니다. 즉, 잠긴 포인트에 해당하는 팟의 일부가 플레이어에게 분배되지 않고 계약에 잠기게 됩니다.

다음 시나리오를 고려해 보겠습니다.

- 승리한 제국에 발행된 총 포인트는 15이고 총 팟은 15 ether입니다.
- 플레이어 A는 승리한 제국에 10 포인트를 가지고 있으며 그중 5 포인트는 잠겨 있습니다.
- 플레이어 B는 승리한 제국에 5 포인트를 가지고 있으며 모두 잠금 해제되어 있습니다.
- 플레이어 A는 팟에서 5 ether를 인출합니다: `15 ether * (10 포인트 - 5 포인트) / 15 포인트`.
- 플레이어 B는 팟에서 5 ether를 인출합니다: `15 ether * 5 포인트 / 15 포인트`.
- 팟에 남은 5 ether는 계약에 잠깁니다.

## 권장 사항

옵션 1: 게임이 종료되면 승리한 제국의 모든 포인트 잠금을 해제합니다. 이렇게 하면 잠긴 포인트에 해당하는 팟의 일부가 잠긴 포인트 수에 따라 해당 포인트를 잠근 플레이어에게 분배됩니다.

옵션 2: `empirePoints`에서 승리한 제국에 잠긴 총 포인트를 뺍니다. 이렇게 하면 잠긴 포인트에 해당하는 팟의 일부가 잠금 해제된 포인트 수에 따라 모든 플레이어에게 균등하게 분배됩니다.

# [C-03] 게임 초기화 시 `MagnetTurnPlanets` 테이블이 지워지지 않음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`ResetSystem` 계약의 `resetGame` 함수는 필요한 테이블을 지우고 초기 데이터를 설정하여 게임을 초기화합니다.

그러나 `MagnetTurnPlanets` 테이블은 게임이 초기화될 때 지워지지 않습니다. 이 테이블의 데이터는 제거해야 할 자석을 확인하기 위해 `updateWorld`가 호출될 때마다 사용됩니다.

```solidity
File: UpdateMagnetsSubsystem.sol

contract UpdateMagnetsSubsystem is EmpiresSystem {
  function updateMagnets() public {
    uint256 nextTurn = Turn.getValue() + 1;
    // remove magnets that should be removed for the next turn for each empire, so the turn starts on an updated state
    for (uint8 i = 1; i <= P_GameConfig.getEmpireCount(); i++) {
      EEmpire empire = EEmpire(i);
@>    bytes32[] memory magnetEmpireTurnPlanets = MagnetTurnPlanets.get(empire, nextTurn);
      for (uint j = 0; j < magnetEmpireTurnPlanets.length; j++) {
        // clear magnet
@>      LibMagnet.removeMagnet(empire, magnetEmpireTurnPlanets[j]);
      }

      MagnetTurnPlanets.deleteRecord(empire, nextTurn);
    }
  }
}
```

테이블에 이 데이터를 남겨두면 자석이 제거되어야 할 때보다 먼저 제거되어 게임 메커니즘이 깨지고 자석을 배치한 사용자가 지불해야 하는 것보다 더 많은 턴 비용을 지불하게 될 수 있습니다.

자세한 예제는 개념 증명을 참조하십시오.

개념 증명:

`test` 디렉토리에 다음 계약을 추가하고 `pnpm mud test --forgeOptions='--mt test_magnet -vv' --skipBuild`를 실행하십시오.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.24;

import { console, PrimodiumTest } from "test/PrimodiumTest.t.sol";
import { P_GameConfig, Planet, Turn, Magnet, MagnetData, TurnData, MagnetTurnPlanets } from "codegen/index.sol";
import { EEmpire, EOverride } from "codegen/common.sol";
import { LibPrice } from "libraries/LibPrice.sol";
import { RoutineThresholds } from "src/Types.sol";
import { PlanetsSet } from "adts/PlanetsSet.sol";
import { CitadelPlanetsSet } from "adts/CitadelPlanetsSet.sol";

contract AuditTest is PrimodiumTest {
  uint256 constant ROUND_TURNS = 500;
  uint256 constant TURN_LENGTH_BLOCKS = 3;
  uint256 internal EMPIRE_COUNT;
  uint256 internal GAME_OVER_BLOCK;
  bytes32 internal planetId;

  function setUp() public override {
    super.setUp();

    EMPIRE_COUNT = P_GameConfig.getEmpireCount();
    GAME_OVER_BLOCK = block.number + ROUND_TURNS * TURN_LENGTH_BLOCKS;
    planetId = _getFirstPlanet(EEmpire(1));

    vm.startPrank(creator);
    P_GameConfig.setTurnLengthBlocks(TURN_LENGTH_BLOCKS);
    P_GameConfig.setGameOverBlock(GAME_OVER_BLOCK);
    Turn.setNextTurnBlock(block.number + TURN_LENGTH_BLOCKS);
    vm.stopPrank();
  }

  function test_magnetTurnPlanetsNotCleared() public {
    // EEmpire.RED conquers all citadels
    createShips(95);
    skipTurns(EMPIRE_COUNT);
    for (uint256 i = 0; i < EMPIRE_COUNT; i++) {
      vm.roll(block.number + TURN_LENGTH_BLOCKS);
      createPendingMove();
      skipTurns(2 * EMPIRE_COUNT - 1);
      createShips(95);
    }

    // Alice places a magnet that is meant to be removed after 1 full turn
    placeMagnet(1, alice);
    uint256 aliceMagnetDeletionTurn = Turn.getValue() + 1 * EMPIRE_COUNT;
    bytes32[] memory magnetEmpireTurnPlanets = MagnetTurnPlanets.get(EEmpire(1), aliceMagnetDeletionTurn);
    assert(magnetEmpireTurnPlanets.length == 1);

    // Game ends by domination
    vm.prank(alice);
    world.Empires__withdrawEarnings();

    // Game is reset, but MagnetTurnPlanets is not cleared
    world.Empires__resetGame();
    magnetEmpireTurnPlanets = MagnetTurnPlanets.get(EEmpire(1), aliceMagnetDeletionTurn);
    assert(magnetEmpireTurnPlanets.length == 1);

    // Game advances until aliceMagnetDeletionTurn - 1
    skipTurns(aliceMagnetDeletionTurn - 2);

    // Bob places a magnet for 10 full turns (10 * EMPIRE_COUNT)
    placeMagnet(10, bob);
    MagnetData memory magnetData = Magnet.get(EEmpire(1), planetId);
    uint256 bobMagnetDeletionTurn = Turn.getValue() + 10 * EMPIRE_COUNT;
    assert(magnetData.endTurn == bobMagnetDeletionTurn);

    // One turn passes, reaching aliceMagnetDeletionTurn, and the magnet Bob placed for 10 turns is removed
    skipTurns(1);
    magnetData = Magnet.get(EEmpire(1), planetId);
    assert(!magnetData.isMagnet);

    // The data for Bob's magnet deletion is still present, so if Charlie places a magnet,
    // the same issue will occur, and so on
    magnetEmpireTurnPlanets = MagnetTurnPlanets.get(EEmpire(1), bobMagnetDeletionTurn);
    assert(magnetEmpireTurnPlanets.length == 1);
  }

  function skipTurns(uint256 turns) public {
    for (uint256 i = 0; i < turns; i++) {
      vm.roll(block.number + TURN_LENGTH_BLOCKS);
      if (block.number < GAME_OVER_BLOCK) {
        executePendingMove();
      }
    }
  }

  function createShips(uint256 numShips) public {
    uint256 cost = LibPrice.getTotalCost(EOverride.CreateShip, EEmpire(1), numShips);
    vm.prank(alice);
    world.Empires__createShip{ value: cost }(planetId, numShips);
  }

  function placeMagnet(uint256 fullTurnDuration, address player) public {
    uint256 cost = LibPrice.getTotalCost(EOverride.PlaceMagnet, EEmpire(1), fullTurnDuration);
    vm.prank(player);
    world.Empires__placeMagnet{ value: cost }(EEmpire(1), planetId, fullTurnDuration);
  }

  function createPendingMove() public {
    RoutineThresholds[] memory routineThresholds = new RoutineThresholds[](1);
    routineThresholds[0] = RoutineThresholds({
      planetId: planetId,
      moveTargetId: _getNotOwnedCitadel(Turn.get().empire),
      accumulateGold: 0,
      buyShields: 0,
      buyShips: 0,
      moveShips: 10000
    });

    vm.prank(creator);
    world.Empires__updateWorld(routineThresholds);
  }

  function executePendingMove() public {
    RoutineThresholds[] memory routineThresholds = new RoutineThresholds[](1);
    routineThresholds[0] = RoutineThresholds({
      planetId: planetId,
      moveTargetId: bytes32(0),
      accumulateGold: 10000,
      buyShields: 0,
      buyShips: 0,
      moveShips: 0
    });

    vm.prank(creator);
    world.Empires__updateWorld(routineThresholds);
  }

  function _getNotOwnedCitadel(EEmpire empire) public view returns (bytes32 planetId_) {
    bytes32[] memory citadelPlanets = CitadelPlanetsSet.getCitadelPlanetIds();
    for (uint256 i = 0; i < citadelPlanets.length; i++) {
      if (empire != Planet.getEmpireId(citadelPlanets[i])) {
        return citadelPlanets[i];
      }
    }
  }

  function _getFirstPlanet(EEmpire empire) internal view returns (bytes32 planetId_) {
    bytes32[] memory allPlanets = PlanetsSet.getPlanetIds();
    for (uint256 i = 0; i < allPlanets.length; i++) {
      if (Planet.getEmpireId(allPlanets[i]) == empire) {
        return allPlanets[i];
      }
    }
  }
}
```

## 권장 사항

게임이 초기화될 때 `MagnetTurnPlanets` 테이블을 지우십시오.

# [C-04] `ResetSystem::resetGame()`의 접근 제어 부족

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`ResetSystem` 계약의 `resetGame` 함수는 어떤 형태의 접근 제어도 구현하지 않아 누구나 호출할 수 있습니다. 이는 권한 없는 사용자가 전체 게임 상태를 초기화하여 잠재적인 진행 상황 손실 및 게임 플레이 중단을 초래할 수 있으므로 심각한 취약점을 생성합니다.

또한 `mud.config.ts` 구성 파일에서 `ResetClearLoopSubsystem`을 포함한 여러 하위 시스템에 대한 `openAccess` 플래그는 `false`로 올바르게 설정되어 있지만, 이 접근 제어는 `ResetSystem` 계약에 적용되지 않았습니다. 접근 제한을 강제하는 수정자가 없기 때문에 문제가 악화됩니다.

다음은 접근 제어가 누락된 코드입니다.

```solidity
function resetGame() public {
    IWorld world = IWorld(_world());
    world.Empires__clearLoop();
    P_GameConfigData memory config = P_GameConfig.get();

    P_GameConfig.setGameOverBlock(block.number + config.nextGameLengthTurns * config.turnLengthBlocks);
    P_GameConfig.setGameStartTimestamp(block.timestamp);
    createPlanets(); // Planet and Empire tables are reset to default values
    LibShieldEater.initialize(); // ShieldEater relocated, charge reset, and destination set
    initPrice(); // Empire.setPointPrice and OverrideCost tables are reset to default values
    Turn.set(block.number + config.turnLengthBlocks, EEmpire.Red, 1);
}
```

이러한 제어 부족으로 인해 악의적인 행위자가 언제든지 게임을 초기화할 수 있으며, 이는 특히 경쟁이나 이해관계가 높은 환경에서 비참한 결과를 초래할 수 있습니다.

## 권장 사항

- **접근 제어 수정자 구현**: `onlyOwner` 또는 `onlyAdmin`과 같은 제한적인 수정자를 추가하여 권한이 있는 사용자만 `resetGame` 함수를 호출할 수 있도록 하십시오.
- **구성 업데이트**: `mud.config.ts` 파일의 `openAccess`가 올바르게 적용되고 계약 수준에서도 접근 제어가 강제되는지 확인하십시오.

```diff
export const worldInput = {
  namespace: "Empires",
  systems: {
    UpdateEmpiresSubsystem: { openAccess: false },
    UpdateAcidSubsystem: { openAccess: false },
    UpdateMagnetsSubsystem: { openAccess: false },
    UpdatePriceSubsystem: { openAccess: false },
    UpdateShieldEaterSubsystem: { openAccess: false },
    ResetClearLoopSubsystem: { openAccess: false },
+   ResetSystem: { openAccess: false },
  },
```

# [M-01] 오버라이드 비용 및 포인트의 비대칭성

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

오버라이드 비용과 구매 시 받는 포인트 수는 게임 내 제국 수에 따라 달라집니다. 그러나 후퇴(regress) 오버라이드의 경우 패배한 제국은 고려되지 않는 반면, 진행(progress) 오버라이드의 경우 모든 제국이 고려됩니다. 이는 두 가지 유형의 오버라이드에 대한 비용과 받는 포인트에 비대칭을 만듭니다.

예를 들어 게임에 2개의 패배하지 않은 제국만 남았고 고정 포인트 가격을 가정할 때, 후퇴 오버라이드 비용은 게임 시작 시 비용보다 5배만 낮아지지만 진행 오버라이드 비용은 전혀 변경되지 않습니다.

## 권장 사항

```diff
    function getProgressPointCost(EOverride _overrideType, EEmpire _empireImpacted, uint256 _overrideCount) internal view returns (uint256) {
        uint8 empireCount = P_GameConfig.getEmpireCount();
+       uint8 enemiesCount;
+       for (uint8 i = 1; i <= empireCount; i++) {
+           if (EEmpire(i) != _empireImpacted && !Empire.getIsDefeated(EEmpire(i))) {
+               enemiesCount++;
+           }
+       }
+
-       return getPointCost(_empireImpacted, _overrideCount * (empireCount - 1) * P_PointConfig.getPointUnit() * P_OverrideConfig.getPointMultiplier(_overrideType));
+       return getPointCost(_empireImpacted, _overrideCount * enemiesCount * P_PointConfig.getPointUnit() * P_OverrideConfig.getPointMultiplier(_overrideType));
    }
```

```diff
  function _purchaseOverride(
(...)
    if (progressOverride) {
+       uint8 enemiesCount;
+       for (uint8 i = 1; i <= empireCount; i++) {
+           if (EEmpire(i) != _empireImpacted && !Empire.getIsDefeated(EEmpire(i))) {
+               enemiesCount++;
+           }
+       }
-       uint256 numPoints = _overrideCount * (empireCount - 1) * pointUnit * pointMultiplier;
+       uint256 numPoints = _overrideCount * enemiesCount * pointUnit * pointMultiplier;
```

# [M-02] 오버라이드 구매 시 실제 지출 금액보다 플레이어의 손실이 더 증가할 수 있음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

오버라이드 시스템은 각 오버라이드 구매를 처리하는 `LibOverride._purchaseOverride` 함수를 호출합니다. 이 함수는 오버라이드에 지출된 금액인 `_spend` 매개변수를 수신하며, 이 값은 플레이어의 `loss`를 증가시키는 데 사용됩니다.

```solidity
File: LibOverride.sol

36    PlayersMap.setLoss(playerId, PlayersMap.get(playerId).loss + _spend);
```

그러나 `_spend` 매개변수에 전달되는 값은 `_msgValue()`인데, 초과 전송된 금액은 플레이어에게 환불되므로 실제 플레이어가 지출한 금액보다 높을 수 있습니다.

```solidity
File: OverrideAcidSystem.sol

32    LibOverride._purchaseOverride(playerId, EOverride.PlaceAcid, empire, 1, _msgValue());
```

결과적으로 플레이어의 `loss`는 오버라이드에 지출한 실제 금액보다 높아지게 됩니다.

## 권장 사항

모든 오버라이드 시스템 함수의 경우, `_msgValue()` 대신 오버라이드 `cost`를 `_purchaseOverride` 함수에 전달하십시오.

# [M-03] 수정자 `onlyNotGameOver()`가 정복 승리를 확인하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

일부 함수에는 포인트 판매 또는 오버라이드 구매와 같은 `onlyNotGameOver()` 수정자가 있습니다. `onlyNotGameOver()`는 게임 마감 시간과 승자를 확인하여 게임 종료를 확인합니다.

```solidity
  modifier _onlyNotGameOver() {
    require(Ready.get(), "[EmpiresSystem] Game not ready");
    require(WinningEmpire.get() == EEmpire.NULL, "[EmpiresSystem] Game over");
    uint256 endBlock = P_GameConfig.getGameOverBlock();
    require(endBlock == 0 || block.number < endBlock, "[EmpiresSystem] Game over");
    _;
  }
```

문제는 코드가 정복 승리 시나리오를 확인하지 않는다는 것입니다. 따라서 게임이 끝났을 수 있지만 이 수정자는 이를 감지하지 못합니다. 이 문제로 인해 사용자는 게임이 사실상 끝난 동안 보호된 함수를 호출하고 작업을 수행할 수 있습니다. 한 가지 중요한 시나리오는 사용자가 게임이 곧 종료될 것으로 보이면(정복 시나리오) `WinningEmpire` 값을 설정하려는 트랜잭션을 실행(front-run)하고 `WinningEmpire`가 `NULL`인 동안 패배한 제국의 포인트를 판매할 수 있습니다.

## 권장 사항

`_checkDominationVictory()`를 호출하여 정복 시나리오로 인해 게임이 끝나지 않았는지 확인하십시오.

# [M-04] `turnEmpirePointPriceDown()`에 대한 샌드위치 공격

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

관리자는 `turnEmpirePointPriceDown()`을 호출하여 제국의 포인트 가격을 줄입니다.

```solidity
  function turnEmpirePointPriceDown(EEmpire _empire) internal {
    P_PointConfigData memory config = P_PointConfig.get();
    uint256 newPointPrice = Empire.getPointPrice(_empire);
    if (newPointPrice >= config.minPointPrice + config.pointGenRate) {
      newPointPrice -= config.pointGenRate;
    } else {
      newPointPrice = config.minPointPrice;
    }
    Empire.setPointPrice(_empire, newPointPrice);
    HistoricalPointPrice.set(_empire, block.timestamp, newPointPrice);
  }
```

문제는 코드가 제국 포인트 가격에서 절대값을 뺀다는 것입니다. 따라서 포인트 가격이 낮으면 가격 하락 비율이 더 높아지고 사용자는 샌드위치 공격을 통해 가격 하락으로 이익을 얻을 수 있습니다. 사용자는 포인트를 직접 구매할 수 없으며 오버라이드를 구매해야 하지만 결과는 동일합니다. 사용자는 관리자의 트랜잭션에 맞춰 오버라이드 구매 시간을 정하고 샌드위치 공격을 수행하여 관리자의 트랜잭션에서 이익을 얻을 수 있습니다.

다음은 POC입니다(포인트 구매는 포인트를 구매하는 결과를 초래하는 일부 오버라이드 구매를 의미함).

1. 제국의 포인트 가격이 100이라고 가정합니다.
2. 사용자 1은 해당 제국에 50 포인트를 가지고 있으며 토큰을 하나 더 구매하면 100이 듭니다.
3. 관리자는 `turnEmpirePointPriceDown()`을 호출하여 가격을 10단위 줄입니다.
4. 사용자 1은 관리자의 트랜잭션에 대해 샌드위치 공격을 수행하고 먼저 50 포인트를 판매하여 가격이 50으로 감소합니다.
5. 그런 다음 관리자의 트랜잭션이 실행되고 포인트 가격은 49가 됩니다.
6. 이제 사용자 1은 가격 50단위로 1 토큰을 구매하고 판매 가격으로 판매된 50 포인트를 구매합니다.
7. 결국 사용자는 100 대신 50의 가격으로 1 토큰을 구매할 수 있었습니다.

## 권장 사항

가격 하락에 따라 백분율 또는 가치(`point * price`)를 사용하거나 시간이 지남에 따라 가격을 낮추십시오.

# [M-05] 승자 목록이 커서 한 트랜잭션으로 처리할 수 없음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

PayoutManager 계약은 승자 보상을 분배하는 데 사용되었습니다. 관리자는 `record()`를 호출하여 승자와 보상 금액을 설정하고 코드는 해당 승자를 반복하고 저장소에 보상을 설정합니다.

```solidity
    function record(
        address[] memory _victors,
        uint256[] memory _gains,
        uint256 _roundNumber
    ) public payable {
        require(msg.sender == owner, "[PAYMAN] Only owner can add winners");
        uint256 allocated = 0;

        for (uint256 i = 0; i < _victors.length; i++) {
            winners[_roundNumber].push(Winner(_victors[i], _gains[i]));
            balances[_victors[i]] = balances[_victors[i]] + _gains[i];
            allocated = allocated + _gains[i];
        }
```

문제는 승자 목록이 커서 코드 로직이 OOG(Out Of Gas)가 발생할 수 있고 관리자가 한 트랜잭션에 모든 승자를 설정할 수 있다는 것입니다. 코드는 특정 라운드에 대한 승자 보상의 일부 설정을 지원하지 않습니다.

## 권장 사항

한 트랜잭션에서 특정 라운드 승자의 하위 집합을 설정할 수 있도록 허용하십시오.

# [M-06] 구매 트랜잭션에서 프런트러닝을 통해 플랜트의 제국이 변경될 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`createShip()`, `placeAcid()` 및 `chargeShield()` 함수에서 사용자는 `planetId`를 정의하고 코드는 해당 행성 및 제국에 대한 작업을 수행하지만 코드는 플랜트의 제국을 검증하지 않습니다.

```solidity
  function createShip(bytes32 _planetId, uint256 _overrideCount) public payable _onlyNotGameOver {
    PlanetData memory planetData = Planet.get(_planetId);
    require(planetData.isPlanet, "[OverrideSystem] Planet not found");
    require(planetData.empireId != EEmpire.NULL, "[OverrideSystem] Planet is not owned");
    uint256 cost = LibPrice.getTotalCost(EOverride.CreateShip, planetData.empireId, _overrideCount);
    require(_msgValue() >= cost, "[OverrideSystem] Insufficient payment");
    bytes32 playerId = addressToId(_msgSender());

    LibOverride._purchaseOverride(playerId, EOverride.CreateShip, planetData.empireId, _overrideCount, _msgValue());
```

문제는 사용자가 트랜잭션을 보낸 후(전투가 해결될 때) 해당 플랜트의 제국이 변경될 수 있다는 것입니다. 따라서 전투 중에 사용자가 자신의 제국을 지원하고 행성에 대한 오버라이드를 구매하려고 할 때 결국 반대 제국을 지원하게 될 것입니다. 공격자는 사용자의 트랜잭션을 프런트러닝하고 `resolveCombat()`를 트리거하여 사용자 정의 플랜트의 제국을 변경하여 공격을 수행하거나 높은 부하 및 시간 집약적인 전투 중에 문제가 스스로 발생할 수 있습니다.

## 권장 사항

함수의 입력에서 제국을 가져와서 플랜트가 해당 제국에 속하는지 확인하십시오.

# [M-07] 제국과 행성에 대한 중첩 루프로 인해 `clearLoop()`에서 OOG 발생 가능

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`clearLoop()` 함수는 제국과 행성을 반복하고 기록을 삭제합니다.

```solidity
   for (uint8 i = 1; i <= empireCount; i++) {
      EEmpire empire = EEmpire(i);
      for (uint256 j = 0; j < planets.length; j++) {
        PendingMove.deleteRecord(planets[j]);
        Magnet.deleteRecord(empire, planets[j]);
      }
```

시스템에는 6개의 제국과 2500개의 행성이 있으므로 루프의 총 반복 횟수는 15000이 됩니다. 각 반복 함수가 계약의 저장소 값을 변경하므로 각 반복의 가스는 2000보다 높을 수 있으며 총 필요 가스는 30M보다 높을 것이므로 한 트랜잭션에서 실행할 수 없습니다.

## 권장 사항

각 트랜잭션에서 데이터의 일부를 지워 여러 트랜잭션으로 작업을 나누십시오.

# [M-08] `clearLoop()`의 OOG 오류

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`ResetClearLoopSubsystem.clearLoop()` 함수는 `PointsMap.clear(empire)`를 호출하여 제국과 관련된 모든 유틸리티를 지웁니다. 문제는 이 함수가 제국의 모든 플레이어를 반복하고 데이터를 지우는데 플레이어 수가 매우 많으면 실행 시 OOG가 발생할 수 있다는 것입니다.

```solidity
  function clear(EEmpire empire) internal {
    bytes32[] memory players = keys(empire);
    for (uint256 i = 0; i < players.length; i++) {
      Value_PointsMap.deleteRecord(empire, players[i]);
      Meta_PointsMap.deleteRecord(empire, players[i]);
    }
    Keys_PointsMap.deleteRecord(empire);
    Empire.setPointsIssued(empire, 0);
  }
```

## 권장 사항

플레이어 수에 제한을 추가하거나 한 트랜잭션에서 모든 플레이어를 반복하는 것을 피하십시오.

# [M-09] 포인트 구매 및 판매 시스템의 MEV 샌드위치 공격 취약점

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

구매할 때마다 포인트 가격이 상승하고 판매할 때마다 하락하는 현재의 포인트 시스템 설계는 MEV(Maximal Extractable Value) 샌드위치 공격에 취약합니다. MEV 봇은 멤풀(mempool)을 모니터링하고 합법적인 구매자나 판매자를 프런트러닝할 수 있습니다.

- **구매자 프런트러닝:** MEV 봇은 멤풀에서 포인트 구매를 감지하고 합법적인 구매자보다 먼저 포인트를 구매하여 가격을 상승시킬 수 있습니다. 결과적으로 합법적인 구매자는 구매 시 더 높은 가격을 지불해야 하며 봇은 더 높은 가격에 판매할 수 있습니다.
- **판매자 프런트러닝:** MEV 봇은 멤풀에서 포인트 판매를 감지하고 합법적인 판매자보다 먼저 더 높은 가격에 포인트를 판매하여 가격을 하락시킬 수 있습니다. 이로 인해 합법적인 판매자는 포인트에 대해 더 낮은 판매 가치를 받게 됩니다. 봇은 할인된 가격으로 다시 구매하여 합법적인 판매자를 희생시켜 가치를 추출할 수 있습니다.

이러한 역학은 구매자의 비용을 늘리고 판매자의 수익을 줄여 사용자 경험에 심각한 영향을 미칠 수 있습니다. 특히 고가치 거래에서 상당한 위험을 초래합니다.

## 권장 사항

MEV 샌드위치 공격의 위험을 완화하려면 구매 및 판매 함수 모두에 슬리피지 보호 메커니즘을 도입하는 것이 좋습니다. 이를 통해 사용자는 포인트를 구매하거나 판매할 때 허용 가능한 최소 또는 최대 가격을 지정하여 MEV 봇으로 인한 상당한 가격 변동으로부터 보호할 수 있습니다.

# [M-10] `placeMagnet`이 행성이 제국 소유인지 확인하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`OverrideMagnetsSystem.sol`의 `placeMagnet` 함수는 행성에 자석을 추가하여 선박의 움직임에 영향을 줍니다. 자석을 배치하려면 플레이어는 공격하려 할 때 적의 배가 자신의 행성에서 멀어지는 것을 방지하기 위해 포인트를 잠가야 합니다.

그러나 이 함수는 `_planetId`가 `_empire` 소유인지 확인하지 않습니다. 즉, 플레이어는 자신의 제국에서 포인트를 획득하고 잠그면서 적 행성에 자석을 배치할 수 있습니다.

플레이어는 적 제국의 포인트를 잠그지 않고도 적의 배의 움직임에 잠재적으로 영향을 줄 수 있습니다. 선박의 움직임은 오프체인에서 처리되므로 자석이 효과를 발휘하기 위해 행성이 제국 소유인지 확인하는지 불확실하므로 문제의 영향은 "중간"으로 간주됩니다.

오프체인 인프라가 행성이 제국 소유인지 확인하는 경우에도 플레이어는 정복할 것으로 예상되는 적 행성에 자석을 배치하여 행성을 차지하자마자 자석이 선박의 움직임에 영향을 미치기 시작하도록 할 수 있습니다. 미래에 오버라이드 가격이 상승할 것으로 예상되는 경우 행성을 소유하기 전에 자석을 배치할 인센티브가 있을 수 있습니다.

## 권장 사항

```diff
  function placeMagnet(
-   EEmpire _empire,
    bytes32 _planetId,
    uint256 turnDuration
- ) public payable _onlyNotGameOver _notDefeated(_empire) {
+ ) public payable _onlyNotGameOver {
+   PlanetData memory planetData = Planet.get(_planetId);
+   EEmpire _empire = planetData.empireId;
+   require(_empire != EEmpire.NULL, "[OverrideSystem] Planet is not owned");
```

제국이 행성을 소유하고 패배한 제국은 어떤 행성도 소유하지 않도록 보장하므로 `_notDefeated` 수정자는 필요하지 않습니다.

# [L-01] `_checkTimeVictory` 함수의 잘못된 게임 종료 확인

`block.number`가 `P_GameConfig.getGameOverBlock()` 값보다 크거나 같으면 게임이 종료된 것으로 간주됩니다. 이 확인은 `EmpiresSystem` 계약의 `_onlyNotGameOver` 수정자에서 올바르게 수행됩니다.

```solidity
File: EmpiresSystem.sol

12:   modifier _onlyNotGameOver() {
13:     require(Ready.get(), "[EmpiresSystem] Game not ready");
14:     require(WinningEmpire.get() == EEmpire.NULL, "[EmpiresSystem] Game over");
15:     uint256 endBlock = P_GameConfig.getGameOverBlock();
16: @>  require(endBlock == 0 || block.number < endBlock, "[EmpiresSystem] Game over");
17:     _;
18:   }
```

그러나 `RewardsSystem` 계약의 `_checkTimeVictory` 함수는 게임이 종료되었는지 올바르게 확인하지 않습니다. 이 함수는 게임이 끝나면 승리한 제국을 얻기 위한 것이지만 `block.number`가 `P_GameConfig.getGameOverBlock()` 값과 같을 때 호출되면 실패합니다.

```solidity
File: RewardsSystem.sol

71:   function _checkTimeVictory() internal view returns (EEmpire) {
72:     uint256 endBlock = P_GameConfig.getGameOverBlock();
73: @>  if (endBlock == 0 || block.number <= endBlock) {
74:       return EEmpire.NULL;
75:     }
```

`_checkTimeVictory` 함수에 다음 변경 사항을 적용하는 것이 좋습니다.

```diff
-    if (endBlock == 0 || block.number <= endBlock) {
+    if (endBlock == 0 || block.number < endBlock) {
```

# [L-02] `PlaceAcidOverrideLog` 테이블의 `ethSpent` 필드에 잘못된 값 할당

`OverrideAcidSystem.placeAcid` 함수는 구매 데이터를 `PlaceAcidOverrideLog` 오프체인 테이블에 등록합니다. `ethSpent` 필드에는 트랜잭션과 함께 전송된 이더리움 양인 `_msgValue()`가 반환하는 값이 할당됩니다. 그러나 이 값은 플레이어가 실제로 사용한 금액보다 클 수 있으므로 `cost`를 대신 사용해야 합니다.

```solidity
File: OverrideAcidSystem.sol

37    PlaceAcidOverrideLog.set(
38      pseudorandomEntity(),
39      PlaceAcidOverrideLogData({
40        playerId: playerId,
41        turn: Turn.getValue(),
42        planetId: _planetId,
43 @>     ethSpent: _msgValue(),
44        overrideCount: 1,
45        timestamp: block.timestamp
46      })
47    );
```

# [L-03] `PostManager.record()`가 라운드 건너뛰기 허용

`PayoutManager` 계약에서 `record` 함수는 `_roundNumber`가 `lastRound`보다 커야 합니다. 이로 인해 라운드를 건너뛰는 것이 가능하며 `_roundNumber`가 `type(uint256).max`로 설정된 경우 함수에 대한 향후 호출을 방지할 수도 있습니다.

이 함수는 신뢰할 수 있는 당사자에 의해 호출되지만 실수로 라운드를 건너뛰지 않도록 `_roundNumber`가 `lastRound`보다 1 더 큰지 확인하는 것이 좋습니다. 라운드를 건너뛰어야 하는 경우 `_victors = [address(0)]` 및 `_gains = [0]`으로 함수를 호출하여 명시적으로 수행할 수 있습니다.

```diff
       require(
-           _roundNumber > lastRound,
-           "[PAYMAN] Round number must be greater than last round"
+           _roundNumber == lastRound + 1,
+           "[PAYMAN] Round number must be one greater than last round"
