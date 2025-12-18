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

_검토 커밋 해시_ - [fc4b76f5f8739c80a7c3074e6b19bfb090ccf550](https://github.com/resolv-im/resolv-contracts/tree/fc4b76f5f8739c80a7c3074e6b19bfb090ccf550)

_수정 검토 커밋 해시_ - [49969c0e55cdf40236c8530d6b4cbbfa4bf780c9](https://github.com/resolv-im/resolv-contracts/tree/49969c0e55cdf40236c8530d6b4cbbfa4bf780c9)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `ResolvStaking`
- `RewardDistributor`

# 발견 사항

# [M-01] `withdraw()`를 통해 `claimEnabled`가 비활성화된 경우에도 보상 청구 허용

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`ResolvStaking` 계약에서 `DEFAULT_ADMIN_ROLE`은 `claimEnabled` 필드를 설정할 수 있습니다. `false`로 설정하면 보상 청구를 위한 `claim` 함수가 비활성화됩니다.

```solidity
    function claim(address _user, address _receiver) external nonReentrant {
        require(claimEnabled, ResolvStakingErrors.ClaimNotEnabled());
        require(msg.sender == _user ||
                msg.sender == usersData[_user].checkpointDelegatee,
            ResolvStakingErrors.InvalidCheckpointCaller());
        checkpoint(_user, true, _receiver, 0);
    }
```

그러나 `withdraw` 함수에서 사용자는 보상 청구 여부를 임의로 지정할 수 있으며 `claimEnabled`가 비활성화되어 있는지 확인하지 **않습니다**.

```solidity
    function withdraw(
        bool _claimRewards,
        address _receiver
    ) external nonReentrant {
        ResolvStakingStructs.PendingWithdrawal storage pendingWithdrawal = usersData[msg.sender].pendingWithdrawal;
        uint256 amount = pendingWithdrawal.amount;
        require(amount != 0, ResolvStakingErrors.WithdrawalRequestNotFound());
        require(block.timestamp >= pendingWithdrawal.cooldownEnd, ResolvStakingErrors.CooldownNotMet());

        checkpoint(msg.sender, _claimRewards, _receiver, 0);
        ...
```

즉, 사용자는 단순히 1 wei로 `initiateWithdrawal`을 호출하고 `cooldownEnd`를 기다린 다음 `_claimRewards`를 `true`로 설정하여 `withdraw`를 호출함으로써 보상을 성공적으로 청구하여 `claimEnabled`를 우회할 수 있습니다.

## 권장 사항

`claimEnabled`가 비활성화된 경우 `_claimRewards`가 `false`로 설정되어야 합니다.

```diff
    function withdraw(
        bool _claimRewards,
        address _receiver
    ) external nonReentrant {
+       if( _claimRewards && !claimEnabled) revert();
```

# [L-01] `dripReward` 샌드위치 공격 가능성

모든 `dripReward`는 stUSR/USR 환율을 증가시켜 MEV를 유발합니다. 봇은 다음을 수행할 수 있습니다.

1. 멤풀에서 dripReward 트랜잭션을 모니터링합니다.
2. USR을 stUSR에 `deposit`합니다.
3. `dripReward`가 채굴된 후 stUSR에서 USR을 `withdraw`하여 스테이킹 없이 USR 보상을 받습니다.

권장 사항:
stUSR#deposit은 시작 부분에서 `dripReward`를 호출해야 합니다.

# [L-02] 더스트 `_stakingReward`가 대규모 `stakingReward`의 베스팅(vesting)에 영향을 미침

`_stakingReward < DRIP_DURATION`인 경우 보상은 즉시 릴리스될 것으로 예상됩니다.
그러나 `allocateReward` 호출 중에 이전에 베스팅되지 않은 토큰이 먼저 릴리스된 다음 새 `stakingReward` 및 `lastCollect` 값이 설정됩니다.
결과적으로 더스트 보상은 다음 `dripReward` 호출 시에만 성공적으로 릴리스됩니다.

```solidity
    function dripReward(bool _isNewAllocation, uint256 _stakingReward) internal {
        if (_isNewAllocation || _stakingReward < DRIP_DURATION) {
            uint256 balance = IERC20(TOKEN_ADDRESS).balanceOf(address(this));
            if (balance > 0) {
                IERC20(TOKEN_ADDRESS).safeTransfer(ST_USR_ADDRESS, balance);
            }
            drip.stakingReward = _stakingReward;
            drip.lastCollect = block.timestamp;
            emit RewardDripped(balance);
            return;
        }
        ...
```

예를 들어：

1. 아직 선형적으로 릴리스되고 있는 대규모 `_stakingReward`가 계약에 있는 경우.
2. 더스트 크기의 `_stakingReward`로 `allocateReward`가 호출되면 이전에 릴리스되지 않은 대규모 `stakingReward`가 즉시 릴리스됩니다.
   3 더스트 `_stakingReward`는 즉시 릴리스되지 않고 `dripReward` 함수를 호출한 후에만 릴리스됩니다.

입력 보상 금액이 더스트 값인 경우 프로토콜이 이전에 베스팅되지 않은 `stakingReward`를 즉시 릴리스하지 않는 것이 좋습니다. 대신 진행 중인 선형 베스팅 일정을 방해하지 않도록 더스트 금액만 직접 릴리스해야 합니다.
