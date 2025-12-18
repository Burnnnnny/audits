# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**resolv-im/resolv-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Resolv Staking 정보

Resolv Staking은 사용자가 $RESOLV를 스테이킹하고 거버넌스 및 시간 가중 보상 분배(WAHP)에 사용되는 양도 불가능한 stRESOLV 토큰을 받을 수 있도록 합니다. 보상은 소급 변경 없이 업데이트된 스테이크 잔액을 기준으로 앞으로만 계산됩니다. 동시에 RewardDistributor 계약은 $USR 보상을 민팅하고 드립(drip) 모델과 리베이스 로직을 사용하여 stUSR 계약에 점진적으로 분배함으로써 수동 청구 없이 수동적이고 비례적인 수익을 가능하게 합니다.

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

- 낮음 - 가정이 너무 많거나 희박하거나, 인센티브가 거의 또는 전혀 없이 공격자가 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정하는 것이 좋음

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [a3ae84fc7eb03cf3fc601dae0639b3e002c477a8](https://github.com/resolv-im/resolv-contracts/tree/a3ae84fc7eb03cf3fc601dae0639b3e002c477a8)

_수정 검토 커밋 해시_ - [8c57a2d72c42333126d5b799cbd5b02e7ca7d7c5](https://github.com/resolv-im/resolv-contracts/tree/8c57a2d72c42333126d5b799cbd5b02e7ca7d7c5)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `ResolvStakingSilo`
- `ResolvStaking`
- `ResolvStakingCheckpoints`
- `ResolvStakingErrors`
- `ResolvStakingEvents`
- `ResolvStakingStructs`
- `StakedTokenDistributor`

# 발견 사항

# [M-01] 청구 가능한 보상에 대한 그리핑(Griefing) 공격

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`ResolvStakingCheckpoints.checkpoint()` 함수에서 수행되는 보상 계산에서 새로운 청구 가능한 금액의 값이 잘립니다. 이로 인해 청구 가능한 금액은 0으로 내림되어 사용자에 대한 토큰당 누적 보상은 업데이트되지만 청구 가능한 금액은 업데이트되지 않을 수 있습니다.

```solidity
File: ResolvStakingCheckpoints.sol
		uint256 userAccReward = userData.accumulatedRewardsPerToken[token];
		uint256 newClaimable = 0;
		if (userAccReward < reward.accumulatedRewardPerToken) {
			userData.accumulatedRewardsPerToken[token] = reward.accumulatedRewardPerToken;
@>			newClaimable = userBalance * (reward.accumulatedRewardPerToken - userAccReward) / REWARD_DENOMINATOR;
		}
```

`updateCheckpoint()` 함수를 사용하면 모든 사용자의 체크포인트를 업데이트할 수 있으므로, 악의적인 행위자는 다른 사용자의 체크포인트를 계속 업데이트하여 토큰당 누적 보상을 업데이트하면서 청구 가능한 금액은 0으로 유지할 수 있습니다. 이는 사용자가 청구 가능한 보상을 잃게 하여 괴롭히는 데 악용될 수 있습니다.

개념 증명 (Proof of concept)

- 프로젝트에 [Foundry 설정](https://book.getfoundry.sh/getting-started/installation).
- 테스트 설정을 용이하게 하기 위해 `ResolvStaking` 및 `ResolvToken`의 생성자에서 `_disableInitializers()`를 주석 처리합니다.
- 아래 코드로 새 파일 `test/foundry/ResolvStaking.t.sol`을 만듭니다.
- `forge test --mt test_griefRewards`를 실행합니다.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;

import "forge-std/Test.sol";

import { ResolvToken } from "contracts/ResolvToken.sol";
import { ResolvStaking } from "contracts/staking/ResolvStaking.sol";
import { ResolvStakingSilo } from "contracts/staking/ResolvStakingSilo.sol";
import { IResolvStakingSilo } from "contracts/interfaces/staking/IResolvStakingSilo.sol";
import { ERC20TestToken } from "contracts/mocks/tests/ERC20TestToken.sol";

contract ResolvStakingTests is Test {
    address alice = makeAddr("Alice");
    address bob = makeAddr("Bob");

    ResolvToken resolvToken;
    ResolvStakingSilo stakingSilo;
    ResolvStaking resolvStaking;
    ERC20TestToken rewardToken;

    function setUp() public {
        resolvToken = new ResolvToken();
        resolvToken.initialize("Resolv coin", "RSLV", address(this), 1_000_000e18);
        resolvToken.transfer(alice, 100_000e18);
        resolvToken.transfer(bob, 100_000e18);

        stakingSilo = new ResolvStakingSilo(resolvToken);

        resolvStaking = new ResolvStaking();
        resolvStaking.initialize("Resolv Staking", "stRSLV", resolvToken, stakingSilo, 14 days);
        stakingSilo.grantRole(stakingSilo.RESOLV_STAKING_ROLE(), address(resolvStaking));
        vm.prank(alice);
        resolvToken.approve(address(resolvStaking), type(uint256).max);
        vm.prank(bob);
        resolvToken.approve(address(resolvStaking), type(uint256).max);

        rewardToken = new ERC20TestToken(6);
        resolvStaking.addRewardToken(rewardToken);
        resolvStaking.grantRole(resolvStaking.DISTRIBUTOR_ROLE(), address(this));
        rewardToken.approve(address(resolvStaking), type(uint256).max);
    }

    function test_griefRewards() public {
        // Distributor deposits reward tokens
        resolvStaking.depositReward(address(rewardToken), 100_000e6, 14 days);

        // Alice and Bob deposit RESOLV tokens
        vm.prank(alice);
        resolvStaking.deposit(0.9e18, alice);
        vm.prank(bob);
        resolvStaking.deposit(99_999.1e18, bob);

        // Take state snapshot
        uint256 initialTimestamp = block.timestamp;
        uint256 snapshot = vm.snapshotState();

        // Bob updates Alice's checkpoint on every 2 seconds for 200 seconds
        for (uint256 i = 0; i < 100; i++) {
            skip(2 seconds);
            vm.prank(bob);
            resolvStaking.updateCheckpoint(alice);
        }

        // Alice's claimable amount is 0
        uint256 aliceClaimableAmount = resolvStaking.getUserClaimableAmounts(alice, address(rewardToken));
        assertEq(aliceClaimableAmount, 0);
        // Alice's accumulated reward per token is up to date
        uint256 aliceAccRewardPerToken = resolvStaking.getUserAccumulatedRewardPerToken(alice, address(rewardToken));
        (,,,,uint256 accRewardPerToken) = resolvStaking.rewards(address(rewardToken));
        assertEq(aliceAccRewardPerToken, accRewardPerToken);
        // Elapsed time is 200 seconds
        assertEq(block.timestamp - initialTimestamp, 200);

        // Rollback to snapshot state
        vm.revertToState(snapshot);

        // Alice calls updateCheckpoint after 200 seconds
        skip(200 seconds);
        vm.prank(alice);
        resolvStaking.updateCheckpoint(alice);

        // Alice's claimable amount is 148
        aliceClaimableAmount = resolvStaking.getUserClaimableAmounts(alice, address(rewardToken));
        assertEq(aliceClaimableAmount, 148);
        // Alice's accumulated reward per token is up to date
        aliceAccRewardPerToken = resolvStaking.getUserAccumulatedRewardPerToken(alice, address(rewardToken));
        (,,,,accRewardPerToken) = resolvStaking.rewards(address(rewardToken));
        assertEq(aliceAccRewardPerToken, accRewardPerToken);
        // Elapsed time is 200 seconds
        assertEq(block.timestamp - initialTimestamp, 200);
    }
}
```

## 권장 사항

호출자가 자신의 체크포인트만 업데이트할 수 있도록 `updateCheckpoint()` 함수를 수정하십시오.

```solidity
    function updateCheckpoint(bool updateUser) external nonReentrant {
        checkpoint(updateUser ? msg.sender : address(0), false, address(0), 0);
    }
```

# [L-01] 청구 가능한 보상 보기 함수가 최신 보상 데이터를 반영하지 않음

보기 함수 `getUserClaimableAmounts`는 단순히 사용자 데이터 매핑에 저장된 값을 반환합니다.

```solidity
    function getUserClaimableAmounts(address _user, address _token) external view returns (uint256 amount) {
        return usersData[_user].claimableAmounts[_token];
    }
```

그러나 이 값은 사용자의 현재 보상 상태를 정확하게 반영하지 않습니다. 더 정확한 접근 방식은 현재 토큰당 보상을 계산하고 그에 따라 사용자의 청구 가능한 보상을 도출하는 것입니다.

# [L-02] `ReentrancyGuardUpgradeable`이 초기화되지 않음

모든 OpenZeppelin의 업그레이드 가능한 계약은 [초기화 시 호출되어야 하는](https://docs.openzeppelin.com/contracts/5.x/upgradeable) `__{ContractName}__init` 함수를 제공합니다.

그러나 `ResolvStaking` 계약은 초기화 시 `__ReentrancyGuard_init` 함수 호출을 누락했습니다. 이 초기화 누락의 결과는 `nonReentrant` 수정자의 첫 실행 시 더 높은 가스 비용일 뿐이지만, 초기화 함수를 호출하는 것이 좋은 관행이며 계약이 수정될 경우 향후 문제를 예방할 수 있습니다.

# [L-03] 스테이킹된 토큰에 대한 관리자의 과도한 권한

`ResolvStaking` 및 `ResolvStakingSilo` 계약 모두 관리자가 계약에서 ERC20 토큰을 인출할 수 있는 `emergencyWithdrawERC20()` 함수가 있습니다. 이 함수는 계약에서 지원하지 않는 토큰이 실수로 전송되었거나 반올림 오류로 인해 발생한 보상 토큰의 더스트를 복구하는 데 유용할 수 있습니다.

그러나 이 함수는 계약에서 스테이킹된 토큰(RESOLV)을 인출할 수도 있습니다. 관리자는 신뢰할 수 있는 당사자로 예상되지만, 권한을 줄이면 관리자의 개인 키가 손상되더라도 사용자의 스테이킹된 토큰이 안전하다는 것을 보장할 수 있습니다.

관리자가 총 스테이킹 금액을 초과하는 스테이크 토큰만 인출할 수 있도록 허용하거나 스테이킹된 토큰 인출에 대한 타임락 메커니즘을 구현하는 것이 좋습니다.

# [L-04] 인출 시 지정된 수신자에게 보상이 전송되지 않음

사용자가 스테이킹된 토큰을 인출하고 `_receiver`를 지정하면서 `_claimRewards`를 true로 설정하면, 인출된 토큰은 `_receiver`에게 올바르게 전송되지만 청구된 보상은 다음 중 하나로 전송됩니다.

- 기본 보상 수신자(`setRewardsReceiver`를 통해 설정된 경우).
- 기본 수신자가 설정되지 않은 경우 호출자(`msg.sender`).

이로 인해 원금과 보상이 서로 다른 주소로 전송되는 일관성 없는 동작이 발생합니다.

```solidity
    function withdraw(
        bool _claimRewards,
        address _receiver
    ) external nonReentrant {
        ...
        checkpoint(msg.sender, _claimRewards, address(0), 0); // @audit receiver is address(0)

        silo.withdraw(_receiver, amount);
        ...
    }
```

# [L-05] 기간 종료 시 `_totalEffectiveSupply`가 0이면 보상 토큰이 잠김

`updateRewardPerToken`에서 `_totalEffectiveSupply`가 0이면 보상이 분배되지 않습니다. `accumulatedRewardPerToken`을 업데이트하지 않습니다.

```solidity
    function updateRewardPerToken(
        ResolvStakingStructs.Reward storage reward,
        uint256 _totalEffectiveSupply
    ) internal {
        ...

        if (lastUpdate - reward.lastUpdate != 0 && _totalEffectiveSupply != 0) { // @audit _totalEffectiveSupply = 0 then accumulatedRewardPerToken is not updated, or rewards are not distributed
            accRewardPerToken += reward.rewardRate * (lastUpdate - reward.lastUpdate) * REWARD_DENOMINATOR / _totalEffectiveSupply;
            reward.accumulatedRewardPerToken = accRewardPerToken;
            reward.lastUpdate = lastUpdate;
        }
    }
```

이 문제는 기간이 종료된 후 새로운 보상이 예치될 때 발생합니다. 새로운 보상 기간이 시작되면 함수는 새로 예치되는 보상만 고려합니다. 지난 기간의 `_totalEffectiveSupply`가 0이어서 분배되지 않은 보상은 고려하지 않습니다. 이러한 토큰은 계약에 잠겨 있으며 새로운 스테이커에게 분배되지 않습니다.

```solidity
    function depositReward(
        address _token,
        uint256 _amount,
        uint256 _duration
    ) external nonReentrant onlyRole(DISTRIBUTOR_ROLE) {
        ...
        uint256 periodFinish = reward.periodFinish;
        if (block.timestamp >= periodFinish) {
            reward.rewardRate = amountReceived / duration; // @audit doesn't take into account the undistributed amount
        ...
    }
```

# [L-06] 보상 계산의 정밀도 오류로 인해 토큰이 잠길 수 있음

보상 토큰이 스테이킹 계약에 예치될 때 보상률은 다음과 같이 계산됩니다.

```solidity
    function depositReward(
        address _token,
        uint256 _amount,
        uint256 _duration
    ) external nonReentrant onlyRole(DISTRIBUTOR_ROLE) {
        require(_amount > 0, ResolvStakingErrors.InvalidAmount());
        ResolvStakingStructs.Reward storage reward = rewards[_token];
        require(address(reward.token) != address(0), ResolvStakingErrors.RewardTokenNotFound(_token));
        require(_duration <= REWARD_DURATION, ResolvStakingErrors.InvalidDuration());
        uint256 duration = _duration == 0 ? REWARD_DURATION : _duration;

        checkpoint(address(0), false, address(0), 0);

        uint256 amountReceived = reward.token.balanceOf(address(this));
        reward.token.safeTransferFrom(msg.sender, address(this), _amount);
        amountReceived = reward.token.balanceOf(address(this)) - amountReceived;
        require(amountReceived > duration, ResolvStakingErrors.InvalidAmount());

        uint256 periodFinish = reward.periodFinish;
        if (block.timestamp >= periodFinish) {
>>          reward.rewardRate = amountReceived / duration;
        } else {
            uint256 remainingReward = (periodFinish - block.timestamp) * reward.rewardRate;
>>          reward.rewardRate = (amountReceived + remainingReward) / duration;
        }
```

이 계산은 토큰 금액을 초 단위의 기간으로 나누므로 소수점 이하 자릿수가 낮은 토큰(예: USDC, WBTC)의 경우 정밀도 손실이 발생합니다. 예를 들어 0.1 WBTC를 10일 동안 분배하면 다음과 같은 결과가 발생합니다.

- `rate = (0.1 * 10^8) / (86400 * 10) ≈ 11`.
- 분배된 토큰: `11 * 86400 * 10 = 9,504,000`.
- 예상: `10,000,000`.
- 정밀도 손실: `496,000 = 0.00496 WBTC ≈ $400`.

이 할당되지 않은 금액은 계약에 남게 되며 비상 인출 메커니즘을 통해서만 검색할 수 있습니다.

권장 사항:

보상률이 계산될 때 1e18 승수를 적용하는 것을 고려하십시오.

```solidity
     reward.rewardRate = amountReceived * ResolvStakingCheckpoints.REWARD_DENOMINATOR / duration;
```

# [L-07] 제로 공급(zero supply) 후 첫 번째 스테이커가 모든 보상을 청구함

Resolv 스테이킹 계약에서 보상은 `totalEffectiveSupply`(스테이킹된 토큰의 총량)가 0인 경우에도 `rewardRate`에 따라 지속적으로 생성됩니다.

이러한 기간 동안 `updateRewardPerToken` 함수는 `_totalEffectiveSupply`가 0이므로 `accumulatedRewardPerToken` 또는 `lastUpdate` 타임스탬프를 업데이트하지 않습니다.

이후 사용자가 이 제로 공급 기간 후에 첫 번째 예금을 하면:

- `checkpoint` 함수가 호출됩니다.
- `updateRewardPerToken`이 실행됩니다. 이제 `_totalEffectiveSupply`는 0이 아닙니다.
- `lastUpdate - reward.lastUpdate` 계산은 현재 시간(`lastUpdate`)과 고정된 `reward.lastUpdate` 타임스탬프를 사용하므로 전체 제로 공급 기간을 포괄하는 큰 시간 델타가 발생합니다.
- 전역 `reward.accumulatedRewardPerToken`은 이 큰 시간 델타를 기반으로 크게 증가합니다.
- 예금한 사용자의 `accumulatedRewardsPerToken`은 0에서 시작합니다. 그들의 청구 가능한 보상 계산 `userBalance * (reward.accumulatedRewardPerToken - 0) / REWARD_DENOMINATOR`는 사실상 전체 제로 공급 기간 동안 `rewardRate`에 의해 생성된 모든 보상을 그들에게 부여합니다.

이로 인해 첫 번째 스테이커가 스테이킹하지 않은 기간 동안 보상의 횡재를 받아 참여를 통해 얻지 못한 가치를 효과적으로 포착하는 불공정한 분배가 발생합니다.

```solidity
function updateRewardPerToken(...) internal {
    // ...
    uint256 lastUpdate = Math.min(block.timestamp, reward.periodFinish);

    // If totalEffectiveSupply is 0, this block is skipped
    if (lastUpdate - reward.lastUpdate != 0 && _totalEffectiveSupply != 0) {
        accRewardPerToken += reward.rewardRate * (lastUpdate - reward.lastUpdate) * REWARD_DENOMINATOR / _totalEffectiveSupply;
        reward.accumulatedRewardPerToken = accRewardPerToken;
        reward.lastUpdate = lastUpdate; // @audit lastUpdate timestamp is not updated
    }
}
```

권장 사항:

`updateRewardPerToken` 함수에서 `totalEffectiveSupply`가 0인 경우에도 `lastUpdate` 타임스탬프를 업데이트하십시오.

# [L-08] 보상 토큰 전송 실패 시 사용자가 보상을 잃을 수 있음

ResolvStaking 계약은 사용자가 청구할 때 청구 가능한 금액을 계산하고 전송하기 위해 모든 보상 토큰을 반복하는 `checkpoint` 함수를 통해 보상을 처리합니다. 구현에 문제가 있습니다. 단일 보상 토큰의 전송이 실패하면(예: 토큰 일시 중지 또는 블랙리스트 등) 전체 트랜잭션이 되돌려져 사용자가 보상에 액세스할 수 없습니다.

이 문제는 여러 보상 토큰을 처리할 때 `ResolvStakingCheckpoints` 라이브러리 내의 `checkpoint` 함수에서 발생합니다.

```solidity
    function checkpoint(
        mapping(address user => ResolvStakingStructs.UserData) storage usersData,
        mapping(address token => ResolvStakingStructs.Reward reward) storage rewards,
        address[] storage rewardTokens,
        ResolvStakingCheckpoints.CheckpointParams memory _params
    ) external {
        ...
        for (uint256 i = 0; i < rewardTokensSize; i++) {
            ...
                if (totalClaimable > 0) {
                    if (_params.claim) {
                        uint256 transferred = safeRewardTransfer(token, receiver, totalClaimable); // @audit can revert
                        userData.claimableAmounts[token] = totalClaimable > transferred ? totalClaimable - transferred : 0;

                        emit RewardClaimed(_params.user, receiver, token, transferred);
                    ...
                }
            }
        }
    }

```

다음 시나리오를 고려하십시오.

- 프로토콜은 세 가지 보상 토큰을 분배합니다: Resolv 토큰, USDC 및 USDT.
- 사용자는 세 가지 토큰 모두에서 보상을 얻었습니다.
- 사용자가 USDC에 의해 블랙리스트에 올랐습니다.
- 보상을 청구하려고 할 때 USDC 전송이 실패하여 전체 트랜잭션이 되돌려집니다.
- 사용자는 Resolv 및 USDT 보상에 대한 액세스 권한을 영구적으로 잃게 됩니다.

권장 사항:

사용자가 개별 토큰에 대한 보상을 청구할 수 있도록 허용하십시오.

# [L-09] `accRewardPerToken`의 정밀도 손실로 인해 0 보상 발생

`ResolvStakingCheckpoints` 라이브러리의 updateRewardPerToken 함수는 정밀도 손실을 겪어 특히 소수점 이하 자릿수가 낮은 토큰(예: USDT와 같은 6자리)의 경우 보상이 0이 될 수 있습니다.

```solidity
    function updateRewardPerToken(
        ResolvStakingStructs.Reward storage reward,
        uint256 _totalEffectiveSupply
    ) internal {
        ...
        if (lastUpdate - reward.lastUpdate != 0 && _totalEffectiveSupply != 0) {
            accRewardPerToken += reward.rewardRate * (lastUpdate - reward.lastUpdate) * REWARD_DENOMINATOR / _totalEffectiveSupply; // @audit can be rounded down to 0
            reward.accumulatedRewardPerToken = accRewardPerToken;
            reward.lastUpdate = lastUpdate;
        }
    }
```

계산에서:

        accRewardPerToken += reward.rewardRate * (lastUpdate - reward.lastUpdate) * REWARD_DENOMINATOR / _totalEffectiveSupply;

정밀도 문제는 다음 요인으로 인해 발생합니다.

- `reward.rewardRate`는 토큰의 기본 소수점(예: USDT의 경우 6)을 사용합니다.
- `_totalEffectiveSupply`는 18 자릿수를 사용합니다(스테이킹된 토큰과 일치).
- `REWARD_DENOMINATOR`는 1e18로 정의됩니다.

소수점 이하 자릿수가 낮은 토큰의 경우 `_totalEffectiveSupply`가 `reward.rewardRate`에 비해 클 때 정수 나눗셈으로 인해 정밀도 손실이 발생합니다. 이는 아래 예에서 볼 수 있듯이 체크포인트가 빈번할 때 특히 문제가 됩니다.

- 14일 동안 분배된 10,000 USDT 보상(6자리).
- `reward.rewardRate` = 10,000e6 / (14 \* 86,400) ≈ 8,267 tokens/second.
- \_`totalEffectiveSupply` = 10,000e18.
- 체크포인트 호출 간 시간 = 1초.

이로 인해 계산이 0으로 내림되어 보상이 누적되지 않습니다.

        accRewardPerToken += 8,267 * 1 * 1e18 / 10,000e18
        accRewardPerToken += 0

이 문제는 다음과 같은 이유로 가능합니다.

- 높은 빈도의 체크포인트(예: "체크포인트 스패밍").
- Arbitrum(0.3초) 또는 Base(2초)와 같은 체인의 낮은 평균 블록 시간.

이로 인해 사용자는 보상을 상당히 줄여 받거나 아예 받지 못하게 됩니다.

권장 사항:

소수점 자릿수가 낮은 보상 토큰에 대한 계산 정밀도를 높이십시오.
