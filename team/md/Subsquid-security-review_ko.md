# 정보

**Pashov Audit Group**은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**Subsquid-network-contracts** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# Subsquid Network에 대하여

Subsquid는 분산 쿼리 및 데이터 레이크 네트워크에 참여하는 노드를 관리하고 노드 간에 보상을 분배하는 프로토콜입니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| --- | --- | --- | --- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 비교적 가능성이 높습니다.

- 낮음 (Low) - 너무 많거나 비현실적인 가정을 필요로 하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준별 요구 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (이미 배포되지 않은 경우 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

**_검토 커밋 해시_ - [3545236f5b34076820fe747eb607a16d8b664a08](https://github.com/subsquid/subsquid-network-contracts/tree/3545236f5b34076820fe747eb607a16d8b664a08)**

**_수정 검토 커밋 해시_ - [4bcd8ee2f8de17d0877bba876574309fcaf227e1](https://github.com/subsquid/subsquid-network-contracts/tree/4bcd8ee2f8de17d0877bba876574309fcaf227e1)**

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `arbitrum/tSQD`
- `gateway-strategies/EqualStrategy`
- `AccessControlledPausable`
- `DistributedRewardsDistribution`
- `Executable`
- `GatewayRegistry`
- `NetworkController`
- `RewardCalculation`
- `RewardTreasury`
- `Router`
- `Staking`
- `TemporaryHolding`
- `TemporaryHoldingFactory`
- `Vesting`
- `VestingFactory`
- `WorkerRegistration`
- `tSQD`
- `interfaces/**`

# 발견 사항

# [C-01] 게이트웨이 생성자가 GatewayRegistry에서 모든 토큰을 훔칠 수 있음

## 심각도

**영향:** 높음 (High), 사용자의 자금이 도난당할 것입니다.

**가능성:** 높음 (High), 누구나 악용할 수 있으며 구현하기 쉽습니다.

## 설명

`GatewayRegistry` 컨트랙트는 사용자가 게이트웨이를 등록하고 게이트웨이에 토큰을 스테이킹하여 계산 단위(Computation Unit, CU)를 받을 수 있도록 합니다. 먼저 사용자는 게이트웨이를 등록합니다.

```solidity
  function register(bytes calldata peerId, string memory metadata, address gatewayAddress) public whenNotPaused {
    require(peerId.length > 0, "Cannot set empty peerId");
    bytes32 peerIdHash = keccak256(peerId);
    require(gateways[peerIdHash].operator == address(0), "PeerId already registered");

    gateways[peerIdHash] = Gateway({
      operator: msg.sender,
      peerId: peerId,
      strategy: defaultStrategy,
      ownAddress: gatewayAddress,
      metadata: metadata,
      totalStaked: 0,
>>    totalUnstaked: 0
    });
```

`totalUnstaked`가 0으로 설정되는 것에 유의하십시오. 그 후 토큰을 스테이킹할 수 있습니다.

```solidity
  function _stakeWithoutTransfer(bytes calldata peerId, uint256 amount, uint128 durationBlocks) internal {
    (Gateway storage gateway, bytes32 peerIdHash) = _getGateway(peerId);
    _requireOperator(gateway);

    uint256 _computationUnits = computationUnitsAmount(amount, durationBlocks);
    uint128 lockStart = router.networkController().nextEpoch();
    uint128 lockEnd = lockStart + durationBlocks;
>>  stakes[peerIdHash].push(Stake(amount, _computationUnits, lockStart, lockEnd));
```

`stakes` 매핑은 모든 사용자 스테이크를 추적하는 데 사용됩니다. 문제는 게이트웨이 등록을 취소할 때 `stakes`를 삭제하지 않는다는 것입니다. 이는 다음 단계로 악용될 수 있습니다:

- 게이트웨이에서 토큰 언스테이킹
- 게이트웨이 등록 취소
- 동일한 `peerId`로 새 게이트웨이 등록
- `totalUnstaked = 0`이므로 토큰을 다시 언스테이킹할 수 있습니다.

```solidity
  function _unstakeable(Gateway storage gateway) internal view returns (uint256) {
    Stake[] memory _stakes = stakes[keccak256(gateway.peerId)];
    uint256 blockNumber = block.number;
    uint256 total = 0;
    for (uint256 i = 0; i < _stakes.length; i++) {
      Stake memory _stake = _stakes[i];
      if (_stake.lockEnd <= blockNumber) {
        total += _stake.amount;
      }
    }
    return total - gateway.totalUnstaked;
  }
```

다음은 `GatewayRegistry.unstake.t.sol`에 대한 코드화된 POC입니다.

```solidity
  function test_StealStakes() public {
    uint256 amount = 100;
    address alice = address(0xA11cE);
    token.transfer(alice, amount);

    // stakers stake into their gateways
    gatewayRegistry.stake(peerId, amount, 200);
    vm.startPrank(alice);
    token.approve(address(gatewayRegistry), type(uint256).max);
    gatewayRegistry.register(bytes("alice"), "", address(0x6a7e));
    gatewayRegistry.stake(bytes("alice"), amount, 200);

    assertEq(token.balanceOf(address(gatewayRegistry)), 200);
    // exploit
    vm.roll(block.number + 300);
    gatewayRegistry.unstake(bytes("alice"), amount);
    gatewayRegistry.unregister(bytes("alice"));
    gatewayRegistry.register(bytes("alice"), "", address(0x6a7e));
    // unstake again
    gatewayRegistry.unstake(bytes("alice"), amount);
    assertEq(token.balanceOf(address(gatewayRegistry)), 0);
  }
```

## 권고 사항

게이트웨이 등록이 취소될 때 `stakes` 매핑을 삭제하십시오.

# [H-01] `toBlock` 확인 누락으로 인해 배포자가 과거 보상을 변경할 수 있음

## 심각도

**영향**: 높음 (High), 순차적 보상을 깨뜨림, 동일한 에포크에 여러 번 보상 가능

**가능성**: 중간 (Medium), 악의적인 배포자(distributor) 필요

## 설명

`DistributedRewardDistribution.sol`의 `commit` 함수는 보상 커밋을 저장하는 데 사용됩니다. 그러나 이 함수는 `toBlock`이 `fromBlock`보다 큰지 확인하지 않습니다. 따라서 배포자는 `toBlock`을 0으로 설정할 수도 있습니다.

```solidity
require(
    recipients.length == workerRewards.length,
    "Recipients and worker amounts length mismatch"
);
require(
    recipients.length == _stakerRewards.length,
    "Recipients and staker amounts length mismatch"
);

require(currentDistributor() == msg.sender, "Not a distributor");
//@audit missing check on block span
require(toBlock < block.number, "Future block");
```

이는 `distribute` 함수 자체에서 더 큰 문제를 야기합니다. 배포가 순차적이도록 강제하려는 확인이 있습니다.

```solidity
require(
    lastBlockRewarded == 0 || fromBlock == lastBlockRewarded + 1,
    "Not all blocks covered"
);
```

그러나 사용자는 `toBlock`을 `fromBlock`보다 낮게 설정할 수 있으며, 이는 순서를 깨뜨립니다. 사실 사용자가 `toBlock`을 0으로 설정하면, 할당이 다음 줄에서 일어나기 때문에 `lastBlockRewarded`를 0으로 설정할 수도 있습니다.

```solidity
lastBlockRewarded = toBlock;
```

POC:

`test_RunsDistributionAfter3Approves` 테스트를 블록 범위를 1에서 0까지 실행하도록 수정하여 문제를 입증할 수 있습니다.

```solidity
function test_RunsDistributionAfter3Approves() public {
    (
        uint256[] memory recipients,
        uint256[] memory workerAmounts,
        uint256[] memory stakerAmounts
    ) = prepareRewards(1);
    rewardsDistribution.addDistributor(address(1));
    rewardsDistribution.addDistributor(address(2));
    rewardsDistribution.setApprovesRequired(3);
    vm.roll(10);
    rewardsDistribution.commit(
        1,
        0,
        recipients,
        workerAmounts,
        stakerAmounts
    );
    hoax(address(1));
    rewardsDistribution.approve(
        1,
        0,
        recipients,
        workerAmounts,
        stakerAmounts
    );
    hoax(address(2));
    rewardsDistribution.approve(
        1,
        0,
        recipients,
        workerAmounts,
        stakerAmounts
    );
}
```

테스트는 문제없이 통과합니다.

## 권고 사항

다음 확인을 강제하십시오:

```solidity
require(toBlock > fromBlock, "Invalid block span");
```

`commit` 함수 또는 `distribute` 함수에서 수행하십시오.

# [H-02] 스테이킹 기간이 너무 짧으면 `computationUnitsAvailable`이 사용 가능한 단위 수를 과대평가할 수 있음

## 심각도

**영향**: 높음 (High), 계산 단위 할당이 부풀려질 수 있음

**가능성**: 중간 (Medium), 악의적인 사용자가 비용 없이 쉽게 악용 가능

## 설명

`computationUnitsAvailable` 함수는 `peerId`에 사용 가능한 계산 단위의 양을 계산합니다. 이는 아래와 같이 `GatewayRegistry.sol` 계약에 정의되어 있습니다.

```solidity
for (uint256 i = 0; i < _stakes.length; i++) {
    Stake memory _stake = _stakes[i];
    if (
        _stake.lockStart <= blockNumber && _stake.lockEnd > blockNumber
    ) {
        total +=
            (_stake.computationUnits * epochLength) /
            (uint256(_stake.lockEnd - _stake.lockStart));
    }
}
return total;
```

`_stake.lockEnd - _stake.lockStart`가 `epochLength`보다 작으면, 에포크 당 사용 가능한 계산 단위는 `peerId`에 사용 가능한 총 계산 단위 양보다 높아집니다.

`_stake.computationUnits`는 스테이킹 중에 계산되며, 전체 스테이킹 기간 동안 `peerId`에 사용할 수 있는 총 계산 단위 양입니다. `computationUnitsAvailable` 함수의 목적은 블록당 사용 가능한 계산 단위 양을 계산하는 것입니다. 그러나 스테이킹 기간이 에포크 길이보다 짧으면 블록당 사용 가능한 계산 단위가 `peerId`에 사용 가능한 총 계산 단위 양보다 높게 계산됩니다.

스테이킹 중에는 스테이킹 기간에 대한 제한이 없으므로 사용자는 이를 임의로 작게 설정할 수 있습니다. 따라서 `epochLength/duration`은 통과된 에포크 수의 역수를 계산하는 대신 큰 숫자를 제공합니다.

짧은 POC가 문제를 보여줍니다:

```solidity
function test_AttackStake() public {
    gatewayRegistry.stake(peerId, 10 ether, 1);
    GatewayRegistry.Stake[] memory stakes = gatewayRegistry.getStakes(
        peerId
    );
    goToNextEpoch();
    emit log_named_uint("Stake compute units", stakes[0].computationUnits);
    emit log_named_uint(
        "Available compute units",
        gatewayRegistry.computationUnitsAvailable(peerId)
    );
}
```

출력:

```bash
[PASS] test_AttackStake() (gas: 212925)
Logs:
  Stake compute units: 10
  Available compute units: 50
```

이는 할당된 총 단위 수가 10개였지만 에포크 당 사용 가능한 단위 수는 50개임을 보여줍니다.

`computationUnitsAvailable`은 오프체인에서 워커에게 할당할 계산 단위 양을 계산하는 데 사용되므로, 의도한 것보다 더 많은 계산 단위를 할당하는 데 사용될 수 있습니다.

따라서 악의적인 사용자는 반복적으로 소액을 스테이킹하여 사용 가능한 계산 단위 양을 늘린 다음 언스테이킹하여 스테이크를 돌려받을 수 있습니다. 그들은 기간을 1로 설정하여 계산 단위 할당을 `epochLength`의 요소만큼 늘릴 수 있습니다.

## 권고 사항

최소 스테이킹 기간을 1 에포크로 설정하십시오.

# [H-03] 워커가 등록 취소 및 잠금 기간 대기 없이 출금할 수 있음

## 심각도

**영향:** 높음 (High), 설계된 등록 취소 흐름을 우회하고 활성 워커 목록에서 워커를 삭제하지 않음.

**가능성:** 중간 (Medium), 현재 활성 상태가 아닌 한 워커로서 등록 취소 없이 직접 출금 가능.

## 설명

설계된 흐름과 본드를 출금하려는 워커의 예상 행동은 먼저 등록된 `peerId`를 등록 취소(deregister)하는 것입니다. 그 후, `withdraw` 함수의 예상 흐름에서 볼 수 있듯이 `withdraw`를 트리거하기 전에 잠금 기간을 기다려야 합니다.

```solidity
  /**
   * @dev Withdraws the bond of a worker.
   * @param peerId The unique peer ID of the worker.
   * @notice Worker must be inactive
   * @notice Worker must be registered by the caller
   * @notice Worker must be deregistered for at least lockPeriod // @audit - prerequisite for withdraw
   */
  function withdraw(bytes calldata peerId) external whenNotPaused {
    uint256 workerId = workerIds[peerId];
    require(workerId != 0, "Worker not registered");
    Worker storage worker = workers[workerId];
    require(!isWorkerActive(worker), "Worker is active");
    require(worker.creator == msg.sender, "Not worker creator");
    require(block.number >= worker.deregisteredAt + lockPeriod(), "Worker is locked");

    uint256 bond = worker.bond;
    delete workers[workerId];

    tSQD.transfer(msg.sender, bond);

    emit WorkerWithdrawn(workerId, msg.sender);
  }
```

그러나 워커가 아직 활성 상태가 아닌 한(현재 블록이 아직 `registeredAt`에 도달하지 않음), `deregister`를 호출하고 잠금 기간을 기다리지 않고 직접 출금할 수 있습니다. 이는 TVL 계산에 영향을 미치지 않지만 설계된 출금 흐름을 위반하고 `activeWorkerIds` 배열에서 워커를 제거하지 않아 무제한 루프 공격 벡터를 가능하게 합니다.

`WorkerRegistration.withdraw.t.sol`에 POC를 추가했습니다.

```solidity
  function testImmediatelyWithdraw() public {
    vm.roll(176329477);
    workerRegistration.register(workerId);
    workerRegistration.withdraw(workerId);
  }
```

## 권고 사항

`withdraw` 수행 시 추가 확인을 추가하는 것을 고려하십시오:

```diff
  function withdraw(bytes calldata peerId) external whenNotPaused {
    uint256 workerId = workerIds[peerId];
    require(workerId != 0, "Worker not registered");
    Worker storage worker = workers[workerId];
    require(!isWorkerActive(worker), "Worker is active");
    require(worker.creator == msg.sender, "Not worker creator");
-    require(block.number >= worker.deregisteredAt + lockPeriod(), "Worker is locked");
+    require(block.number >= worker.deregisteredAt + lockPeriod() && worker.deregisteredAt != 0, "Worker is locked");

    uint256 bond = worker.bond;
    delete workers[workerId];

    tSQD.transfer(msg.sender, bond);

    emit WorkerWithdrawn(workerId, msg.sender);
  }
```

# [H-04] tSQD의 `registerTokenOnL2`에 대한 접근 제어 부족

## 심각도

**영향**: 높음 (High), 악의적인 공격자가 L2 사용자 지정 주소를 다른 주소로 설정하여 브리지 토큰을 깨뜨릴 수 있음.

**가능성**: 중간 (Medium), 공격자가 `registerTokenOnL2`를 프론트러닝하여 브리지 토큰을 깨뜨릴 수 있음.

## 설명

tSQD는 Arbitrum의 일반 사용자 지정 게이트웨이를 통해 Ethereum(L1)에서 Arbitrum(L2)으로 브리지될 수 있도록 설계되었습니다.
그러나 `gateway.registerTokenToL2`를 통해 L2 토큰 주소를 설정하는 `registerTokenOnL2` 함수는 현재 제한되지 않았습니다.

```solidity
  function registerTokenOnL2(
    address l2CustomTokenAddress,
    uint256 maxSubmissionCostForCustomGateway,
    uint256 maxSubmissionCostForRouter,
    uint256 maxGasForCustomGateway,
    uint256 maxGasForRouter,
    uint256 gasPriceBid,
    uint256 valueForGateway,
    uint256 valueForRouter,
    address creditBackAddress
  ) public payable {
    require(!shouldRegisterGateway, "ALREADY_REGISTERED");
    shouldRegisterGateway = true;

    gateway.registerTokenToL2{value: valueForGateway}(
      l2CustomTokenAddress, maxGasForCustomGateway, gasPriceBid, maxSubmissionCostForCustomGateway, creditBackAddress
    );

    router.setGateway{value: valueForRouter}(
      address(gateway), maxGasForRouter, gasPriceBid, maxSubmissionCostForRouter, creditBackAddress
    );

    shouldRegisterGateway = false;
  }
```

공격자는 `registerTokenOnL2`를 프론트러닝하고 `l2CustomTokenAddress`에 잘못된 주소를 넣어 브리지 토큰을 깨뜨릴 수 있습니다. 일단 호출되면 L2 토큰은 게이트웨이 내부에서 변경할 수 없습니다.

## 권고 사항

tSQD 내에서 Ownable 기능을 사용하고 Arbitrum 브리지 토큰 설계에서 제안한 대로 소유자/관리자만 호출할 수 있도록 `registerTokenOnL2`를 제한하십시오.

# [M-01] `setGatewayAddress`가 게이트웨이 주소를 잘못 업데이트함

## 심각도

**영향:** 중간 (Medium), 게이트웨이가 여전히 이전 주소를 가짐

**가능성:** 중간 (Medium), 사용자가 게이트웨이에 새 주소를 설정하기로 결정한 경우에만 발생

## 설명

게이트웨이 소유자는 `setGatewayAddress` 함수를 사용하여 새 주소를 설정할 수 있습니다. 불행히도 이 함수는 `gatewayByAddress` 매핑만 업데이트하고 `Gateway` 구조체는 그대로 둡니다.

```solidity
  function setGatewayAddress(bytes calldata peerId, address newAddress) public {
    (Gateway storage gateway, bytes32 peerIdHash) = _getGateway(peerId);
    _requireOperator(gateway);

    if (gateway.ownAddress != address(0)) {
>>    delete gatewayByAddress[gateway.ownAddress];
    }

    if (address(newAddress) != address(0)) {
      require(gatewayByAddress[newAddress] == bytes32(0), "Gateway address already registered");
>>     gatewayByAddress[newAddress] = peerIdHash;
    }
```

사용자가 `allocateComputationUnits`를 호출하려고 하면, 컨트랙트는 `msg.sender`에서 이전 게이트웨이 주소를 예상합니다.

```solidity
  function allocateComputationUnits(bytes calldata peerId, uint256[] calldata workerId, uint256[] calldata cus)
    external
    whenNotPaused
  {
    require(workerId.length == cus.length, "Length mismatch");
    (Gateway storage gateway,) = _getGateway(peerId);
>>  require(gateway.ownAddress == msg.sender, "Only gateway can allocate CUs");
```

`GatewayRegistry.unstake.t.sol`에 대한 코드화된 POC

```solidity
  function test_SetAddress() public {
    GatewayRegistry.Gateway memory gt = gatewayRegistry.getGateway(bytes("peerId"));
    bytes32 peerIdHash = gatewayRegistry.gatewayByAddress(address(this));
    // check previous gateway address
    assertEq(gt.ownAddress, address(this));
    assertEq(peerIdHash, keccak256("peerId"));
    // try set a new one
    address newAddy = address(0x6a7e);
    gatewayRegistry.setGatewayAddress(bytes("peerId"), newAddy);
    gt = gatewayRegistry.getGateway(bytes("peerId"));
    peerIdHash = gatewayRegistry.gatewayByAddress(newAddy);
    assertEq(peerIdHash, keccak256("peerId"));
    // this will fail, since address is not updated
    assertEq(gt.ownAddress, newAddy);
  }
```

## 권고 사항

`ownAddress` 변수도 업데이트하십시오.

```solidity
    ...
    if (address(newAddress) != address(0)) {
      require(gatewayByAddress[newAddress] == bytes32(0), "Gateway address already registered");
      gatewayByAddress[newAddress] = peerIdHash;
>>    gateway.ownAddress = newAddress;
    }
```

# [M-02] `activeWorkerIds`에 크기 제한이 없어 무한히 커질 수 있고 가스 괴롭힘(gas-grief) / OOG 오류 유발 가능

## 심각도

**영향**: 높음 (High), 서비스 거부(DoS) 및 사용자에게 높은 가스 비용

**가능성**: 낮음 (Low), 제출된 본드로 인해 수익성 없음

## 설명

`WorkerRegistration.sol` 계약의 `register` 함수는 새 워커가 등록될 때마다 `activeWorkerIds` 배열에 새 요소를 추가합니다. 등록된 `peerId`에 대한 확인이 없으므로 누구나 `peerId`를 워커로 등록할 수 있습니다. 유일한 제안은 사용자가 그렇게 하기 위해 나중에 잠금 해제될 토큰 본드 금액을 제출해야 한다는 것입니다.

문제는 이 배열이 너무 커지면 많은 함수가 이 배열을 반복하므로 다른 사용자에게 과도한 가스 비용을 초래한다는 것입니다. 극단적인 시나리오는 배열을 반복하는 가스 비용이 블록 가스 제한을 초과하여 작업을 DOS하는 경우입니다.

`activeWorkerIds`에 대한 이러한 루프의 예는 다음과 같습니다.

```solidity
function deregister(bytes calldata peerId) external whenNotPaused {
    for (uint256 i = 0; i < activeWorkerIds.length; i++) {
        if (activeWorkerIds[i] == workerId) {
            activeWorkerIds[i] = activeWorkerIds[
                activeWorkerIds.length - 1
            ];
            activeWorkerIds.pop();
            break;
        }
    }
```

```solidity
function getActiveWorkers() public view returns (Worker[] memory) {
    for (uint256 i = 0; i < activeWorkerIds.length; i++) {
        uint256 workerId = activeWorkerIds[i];
        Worker storage worker = workers[workerId];
        if (isWorkerActive(worker)) {
            activeWorkers[activeIndex] = worker;
            activeIndex++;
        }
    }
```

이는 `getActiveWorkerIds` 및 `getActiveWorkerCount` 함수에도 유효합니다.

사용자가 자신에게 비용을 들이지 않고 다른 사용자의 가스 비용을 늘릴 수 있으므로 이는 문제입니다. 악의적인 사용자는 항상 잠금 기간 후에 돌아와서 등록을 취소하고 본드 금액을 인출할 수 있습니다. 그러나 그 사이에 프로토콜과 상호 작용한 사용자는 이 조작으로 인해 부풀려진 가스 비용을 지불합니다.

가능성은 낮지만 부풀려진 배열이 블록 가스 제한으로 인해 단일 블록에서 순회하기에 너무 커서 전체 프로토콜을 DOS하는 시나리오가 있습니다. 그러나 이는 공격자에게 수익성이 없을 것입니다.

## 권고 사항

`peerId`의 화이트리스트를 사용하고 `activeWorkerIds` 배열의 크기를 적절한 양으로 제한하십시오.

# [M-03] 사용자가 TemporaryHolding에서 토큰을 "무한한" 시간 동안 잠글 수 있음

## 심각도

**영향:** 중간 (Medium), 관리자는 잠금 시간 종료 후 토큰을 회수할 수 없음

**가능성:** 중간 (Medium), 공격자는 임시 보유(TemporaryHolding) 수혜자여야 함

## 설명

`TemporaryHoldings.sol`은 `beneficiary` 주소가 제한된 시간 동안 화이트리스트에 있는 프로토콜 컨트랙트에서 tSQD를 사용할 수 있도록 허용합니다.

```solidity
  function execute(address to, bytes calldata data, uint256 requiredApprove) public returns (bytes memory) {
    require(_canExecute(msg.sender), "Not allowed to execute");
    require(router.networkController().isAllowedVestedTarget(to), "Target is not allowed");

    // It's not likely that following addresses will be allowed by network controller, but just in case
    require(to != address(this), "Cannot call self");
    require(to != address(tSQD), "Cannot call tSQD");

    if (requiredApprove > 0) {
      tSQD.approve(to, requiredApprove);
    }
    return to.functionCall(data);
  }
```

`lockedUntil` 시간이 지난 후 `admin`은 내부 자금에 대한 통제권을 되찾습니다.

```solidity
  function _canExecute(address executor) internal view override returns (bool) {
    if (block.timestamp < lockedUntil) {
      return executor == beneficiary;
    }
    return executor == admin;
  }
```

화이트리스트 대상 중 하나는 `GatewayRegistry`입니다. 요령 있는 수혜자는 먼 미래의 시간 동안 `TemporaryHolding`에서 토큰을 스테이킹하고 부스트된 CU를 즐길 수 있으며, 관리자는 `lockedUntil` 이후에도 이 토큰을 언스테이킹할 수 없습니다.

```solidity
  function _stakeWithoutTransfer(bytes calldata peerId, uint256 amount, uint128 durationBlocks) internal {
    (Gateway storage gateway, bytes32 peerIdHash) = _getGateway(peerId);
    _requireOperator(gateway);

    uint256 _computationUnits = computationUnitsAmount(amount, durationBlocks);
    uint128 lockStart = router.networkController().nextEpoch();
>>  uint128 lockEnd = lockStart + durationBlocks;
    ...
}
```

## 권고 사항

가장 쉬운 해결책은 `TemporaryHolding.sol`을 통한 게이트웨이 레지스트리 사용을 허용하지 않는 것입니다. 만약 유효한 대상으로 유지해야 한다면, `execute` 함수에서 페이로드를 디코딩하고 `stake` 또는 `extend` 함수인 경우 기간을 검증하는 것을 고려하십시오.

# [M-04] 프로토콜이 조작 가능한 무작위성을 사용함

## 심각도

**영향**: 중간 (Medium), 배포자(distributor)는 일반적으로 신뢰할 수 있는 행위자이지만 조작의 벡터가 될 수 있습니다.

**가능성**: 중간 (Medium), 무작위성은 여러 벡터를 통해 조작될 수 있습니다.

## 설명

프로토콜은 블록 범위에 대해 보상을 커밋할 수 있는 배포자를 선택하기 위해 난수를 생성합니다. 문제는 프로토콜이 이 무작위성을 생성하는 방식에 있습니다.

```solidity
function distributorIndex() public view returns (uint256) {
    uint256 slotStart = (block.number / 256) * 256;
    return uint256(blockhash(slotStart)) % distributors.length();
}
```

이 `distributionIndex`가 계산된 후 배열에서 배포자를 선택하는 데 사용됩니다.

이 설계에는 두 가지 문제가 있습니다:

1. `blockhash`를 사용하는 것은 무작위성을 생성하는 안전하지 않은 방법입니다.
2. `slotStart`는 256 블록마다 조작 가능합니다.

### 1. 무작위성의 원천으로서의 `blockhash`

Arbitrum에서 트랜잭션은 선착순으로 정렬되며 트랜잭션 정렬을 담당하는 시퀀서는 중앙 집중화되어 있습니다. 그러나 Arbitrum DAO는 앞으로 시퀀서를 탈중앙화할 계획이며, 이는 악의적인 시퀀서가 블록에 대해 유리한 해시를 얻을 때까지 트랜잭션을 계속 포함하고 브로드캐스팅할 수 있음을 의미합니다. Arbitrum의 특성상 이는 Ethereum 메인넷보다 수행하기 어렵지만 시퀀서 탈중앙화가 달성되면 여전히 가능합니다.

### 2. `slotStart`는 조작 가능함

`slotStart`는 `(block.number / 256) * 256`으로 계산됩니다. 이제 `block.number`가 256의 배수일 때마다 `slotStart`는 `block.number` 자체로 계산됩니다.

Ethereum 황서에 따르면 현재 블록의 `blockhash`는 항상 0을 반환합니다. 이는 현재 블록의 블록 해시가 아직 계산되지 않았기 때문입니다. 따라서 `block.number`가 256의 배수일 때마다 `uint256(blockhash(slotStart))`는 0으로 계산됩니다. 이를 통해 0번째 배포자는 `block.number`가 256의 배수일 때마다 커밋을 수행할 수 있습니다.

시스템의 무작위성이 이 두 가지 방식으로 깨졌으므로 이는 문제입니다.

## 권고 사항

첫째, `blockhash`는 결코 현재 블록에 대해 호출되어서는 안 되며, 이는 위 시나리오 2에서 발생하는 일입니다. 따라서 `slotStart-1`의 블록해시를 사용하면 0번째 인덱스가 무작위성을 조작할 수 없이 동일한 효과를 낼 것입니다.

둘째, 미래에 Arbitrum의 블록해시는 더 조작 가능해질 수 있으므로 Chainlink VRF를 사용하여 보다 강력한 방식으로 무작위성을 생성하는 것을 고려하십시오.

# [M-05] Vesting 계약은 빈번한 되돌림(Revert)을 초래할 수 있음

## 심각도

**영향**: 중간 (Medium), 자금의 일시적 DOS

**가능성**: 중간 (Medium), 자금이 프로토콜에서 스테이킹 / 사용될 때 발생

## 설명

`Vesting`은 사용자 대신 자금을 잠그는 동시에 사용자가 프로토콜에 참여할 수 있는 능력을 제공하는 데 사용됩니다. 계약의 아이디어는 계약의 자금 양이 시간이 지남에 따라 점진적으로 잠금 해제되는 동시에 소유자가 해당 토큰을 사용하여 스테이킹하거나 워커 및 게이트웨이를 등록할 수 있는 권한을 제공하는 것입니다. 이를 통해 사용자는 토큰을 한꺼번에 출금할 수 있는 옵션 없이 생태계에서 토큰을 사용할 수 있습니다.

`VestingWallet` OpenZeppelin 계약은 이미 지급된 토큰을 추적하는 `_erc20Released` 변수를 추적합니다. 새로운 지급을 트리거할 때 사용 가능한 토큰 양은 아래와 같이 계산됩니다.

```solidity
function releasable() public view virtual returns (uint256) {
    return vestedAmount(uint64(block.timestamp)) - released();
}
```

문제는 계약이 베스팅 금액을 추적하기 위해 `erc20.balanceOf` 함수를 사용하기 때문에 위 표현식이 언더플로되고 되돌릴 수 있다는 것입니다. 이는 사용자가 베스팅된 금액의 일부를 스테이킹이나 워커 및 게이트웨이 등록에 할당하려는 경우 계약의 잔액이 감소할 수 있기 때문입니다.

이 문제는 아래 POC에서 가장 잘 입증됩니다.

이 문제는 다음 단계에서 재현됩니다.

1. 8 eth 토큰이 베스팅 계약으로 전송됩니다.
2. 중간 시점에 절반(4 eth) 토큰이 베스팅되었습니다. 이러한 토큰은 `release`를 호출하여 수집됩니다. 따라서 해당 토큰에 대해 `_erc20Released`는 4 eth로 설정됩니다.
3. 계약에 남은 4 eth 중 2 eth가 스테이킹에 할당됩니다.
4. 얼마 후, 더 많은 토큰이 베스팅됩니다.
5. 이제 `release`가 호출되면 함수가 되돌려집니다. 이는 `vestedAmount()`가 4 eth 미만의 값을 반환하고 `_erc20Released`가 4 eth이기 때문입니다. 이로 인해 `releasable` 함수가 언더플로되고 되돌려집니다.

따라서 계약에 자금이 있고 일부 베스팅된 보상을 지급할 여유가 있더라도 이 함수는 되돌려집니다.

```solidity
function test_AttackVesting() public {
    token.transfer(address(vesting), 8 ether);

    // Half (4 eth) is vested out
    vm.warp(vesting.start() + vesting.duration() / 2);
    vesting.release();
    assert(vesting.released(address(token)) == 4 ether);

    // Stake half of rest (2 ETH)
    bytes memory call = abi.encodeWithSelector(
        Staking.deposit.selector,
        0,
        2 ether
    );
    vesting.execute(address(router.staking()), call, 2 ether);

    // pass some time
    vm.warp(vesting.start() + (vesting.duration() * 60) / 100);
    // check rewards
    vm.expectRevert();
    vesting.release();
}
```

이로 인해 일시적인 DOS가 발생하며, 사용자는 스테이크 또는 등록이 지급될 때까지 베스팅된 토큰을 릴리스할 수 없습니다.

사용자가 받을 자격이 있는 자금의 일부에 대한 액세스 권한을 잃기 때문에 이는 중간 심각도 문제입니다.

## 권고 사항

`depositForVesting(unit amount)` 함수를 추가하고 이 함수에서 업데이트되는 저장 변수 `baseAmount`로 금액을 추적하는 것을 고려하십시오. 이렇게 하면 베스팅 보상은 `erc20.balanceOf` 값이 아니라 이를 기반으로 계산됩니다. 결과적으로 토큰이 스테이킹을 위해 전송될 때 `baseAmount`를 줄일 필요가 없으며, 베스팅된 금액은 잔액만이 아니라 계약의 가치에 따라 올바르게 계산됩니다. 이렇게 하면 자금을 사용할 수 있어도 청구가 되돌려지는 시나리오를 방지할 수 있습니다.

# [L-01] Arbitrum에서 `PUSH0` 지원되지 않음

계약은 `0.8.20` 솔리디티 버전을 사용하는데, 이는 `PUSH0` 연산코드를 도입합니다. 이 연산코드는 Arbitrum에 존재하지 않습니다. `foundry.toml` 파일은 `evm_version`을 지정하지 않으므로 계약은 `PUSH0` 연산코드를 생성하지 않는 기본 `paris` 버전으로 컴파일되므로 현재 상태의 계약은 괜찮습니다.

그러나 `evm_version`이 `shanghai` 이상으로 변경되면 `PUSH0` 연산코드로 인해 Arbitrum에 계약을 배포하지 못합니다.

나중에 evm_version으로 인한 실수를 방지하기 위해 컴파일러 버전을 `0.8.19` 이전으로 롤백하거나 `foundry.toml` 파일에 `evm_version=paris`를 지정하는 것이 좋습니다.

# [L-02] 본드 금액이 증가한 경우 워커 본드를 업데이트할 직접적인 방법이 없음

현재 `returnExcessiveBond` 함수는 `WorkerRegistration` 내부에서 사용할 수 있습니다. 시스템에서 본드가 줄어드는 경우 초과 금액을 청구하여 생성자에게 반환할 수 있습니다. 그러나 본드가 증가하는 경우 워커가 본드를 업데이트할 직접적인 방법이 없으며, 전체 등록 취소 및 등록 절차를 거쳐야 하므로 잠금 기간을 기다려야 합니다. 이는 시스템 내부의 실제 본드가 TVL 계산과 일치하는 데 시간 지연을 추가합니다. `returnExcessiveBond`와 유사하게 본드가 증가한 경우 본드를 업데이트하는 기능을 추가하는 것을 고려하십시오.

# [L-03] 워커가 활성 워커 목록에서 너무 일찍 제거됨

`deregister` 호출 중 `activeWorkerIds` 배열에서 워커를 즉시 제거하지만 `deregisteredAt` 값은 다음 에포크로 설정됩니다.

```solidity
  function deregister(bytes calldata peerId) external whenNotPaused {
    uint256 workerId = workerIds[peerId];
    require(workerId != 0, "Worker not registered");
    require(isWorkerActive(workers[workerId]), "Worker not active");
    require(workers[workerId].creator == msg.sender, "Not worker creator");

>>   workers[workerId].deregisteredAt = nextEpoch();

    for (uint256 i = 0; i < activeWorkerIds.length; i++) {
      if (activeWorkerIds[i] == workerId) {
        activeWorkerIds[i] = activeWorkerIds[activeWorkerIds.length - 1];
        activeWorkerIds.pop();
        break;
      }
    }
```

그리고 `isWorkerActive`에 따르면,

```solidity
  function isWorkerActive(Worker storage worker) internal view returns (bool) {
    return worker.registeredAt > 0 && worker.registeredAt <= block.number
      && (worker.deregisteredAt == 0 || worker.deregisteredAt >= block.number);
  }
```

`deregisteredAt >= block.number`인 경우 워커는 활성 상태로 간주됩니다.

# [L-04] Staking의 `deposit` 함수가 워커가 여전히 활성 상태인지 확인하지 않음

스테이커는 Staking 계약의 `deposit` 함수를 호출할 때 제공된 워커가 현재 활성 상태가 아닌 경우 보상을 받지 못하며 스테이킹된 토큰을 인출하기 위해 다음 에포크 지연까지 기다려야 합니다. `deposit` 호출 시 `WorkerRegistration` 계약에서 제공된 워커가 활성 상태인지 확인하는 것이 좋습니다.

# [L-05] 실패/덮어쓴 커밋은 다시 승인할 수 없음

`DistributedRewardDistribution`을 사용하면 배포자가 보상 분배를 커밋한 다음 다른 배포자가 승인하도록 할 수 있습니다. 승인 횟수가 임계값에 도달하면 커밋이 승인된 것으로 간주되고 보상이 분배됩니다.

커밋 프로세스 중 현재 커밋은 아래와 같이 매핑에 저장됩니다.

```solidity
commitments[fromBlock][toBlock] = commitment;
approves[fromBlock][toBlock] = 1;
```

현재 배포자는 항상 이 함수를 호출할 수 있으며 동일한 `fromBlock` 및 `toBlock`을 사용하는 경우 언제든지 이전 약속을 덮어쓸 수 있습니다. 또한 승인 수를 1로 재설정합니다.

문제는 `approve` 함수에서 덮어쓴 커밋인지 새 커밋인지에 관계없이 특정 커밋은 한 번만 승인될 수 있다는 것입니다.

```solidity
require(!alreadyApproved[commitment][msg.sender], "Already approved");
    approves[fromBlock][toBlock]++;
    alreadyApproved[commitment][msg.sender] = true;
```

이제 다음 시나리오를 고려하십시오.

1. 배포자가 10명 있습니다. 승인 제한은 5입니다.
2. 배포자 1이 블록 1-100에 대한 커밋을 추가합니다. 다른 3명의 배포자가 승인합니다.
3. 256 블록 후 배포자 2가 제어권을 얻습니다. 그들은 `commit`을 다시 호출하고 이전 커밋을 다른 커밋으로 덮어쓰지만 동일한 1-100 블록에 대해 수행합니다.
4. 또 다른 256 블록 후 배포자 3이 제어권을 얻고 배포자 1이 1-100 블록에 대해 만든 원래 커밋을 다시 제출합니다. 이제 이전에 투표한 배포자는 `alreadyApproved[commitment][msg.sender]`가 true로 설정되어 있으므로 더 이상 투표할 수 없습니다. 따라서 계약이 배포자에 의해 승인된 것으로 표시하더라도 `approves` 매핑은 이를 반영하지 않습니다.

이로 인해 다시 제출하는 경우 투표가 거부될 수 있으며 `approves`에 `alreadyApproved` 매핑이 true인 고유 주소 수와 동일한 숫자가 포함되어야 한다는 불변성도 깨집니다.

`approves` 수를 1로 재설정하고 있으므로 새로운 `alreadyApproved` 매핑을 사용해야 합니다. 모든 커밋에서 증가하는 `commitmentId` 변수를 추가하는 것을 고려하십시오. 그러면 승인 매핑은 추가 키를 구현하고 `alreadyApproved[commitment][commitmentId][msg.sender]`를 대신 사용해야 합니다. 그러면 다시 제출된 커밋에 대해 이미 부여된 승인이 재설정됩니다.

