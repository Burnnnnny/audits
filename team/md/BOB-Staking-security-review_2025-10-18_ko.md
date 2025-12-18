
# Pashov Audit Group 소개


Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원들로 구성되어 있으며, 이들은 이 분야에서 입증된 실력을 갖추고 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 획득했거나, 여러 번 챔피언이 되었거나, 우리와의 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 의욕적인 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위해 우리 팀이 최선의 노력을 다할 것을 보장합니다.

이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.


# 면책 조항


스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾기 위해 시도합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.


# 소개

<p><strong>bob-collective/bob-staking</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, <strong>zark, Tejas Warambhe, IvanFitro, afriauditor</strong>가 <strong>BOB Staking</strong>을 검토하는 데 참여했습니다. 총 <strong>20</strong>개의 이슈가 발견되었습니다.</p>

# BOB Staking 소개

<p>BOB Staking은 언본딩(unbonding) 메커니즘과 즉시 출금 기능을 구현하는 스테이킹 시스템입니다. 사용자는 토큰을 스테이킹하고 보상을 받을 수 있으며, 표준 언본딩 프로세스 또는 페널티가 있는 즉시 출금 중에서 선택할 수 있습니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [73158d3c424bdc7c0402bf59c6e42f4dd261f9d6](https://github.com/bob-collective/bob-staking/tree/73158d3c424bdc7c0402bf59c6e42f4dd261f9d6)<br>&nbsp;&nbsp;(bob-collective/bob-staking)

**수정 검토 커밋 해시:**<br>• [44842c634a3a4b87b889384de2f648c8e4fab1b8](https://github.com/bob-collective/bob-staking/tree/44842c634a3a4b87b889384de2f648c8e4fab1b8)<br>&nbsp;&nbsp;(bob-collective/bob-staking)




# 범위

- `BobStaking.sol`
- `BonusWrapper.sol`

# 발견 사항



# [C-01] `instantWithdraw()`가 `_amountForContract`를 전송하지 않아 토큰이 잠김

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명
`instantWithdraw()`는 스테이킹된 모든 토큰을 즉시 출금하는 데 사용되며, 조기 출금에 대한 페널티를 적용합니다.
```solidity
function instantWithdraw(address _receiver) external nonReentrant {
        if (stakers[_stakeMsgSender()].unlockTimestamp > block.timestamp) {
            revert TokensLocked();
        }

        if (unbondEndTimes[_stakeMsgSender()] != 0) {
            revert UnbondAlreadyStarted();
        }

        _claimRewards(_stakeMsgSender(), false);

        uint256 amount = stakers[_stakeMsgSender()].amountStaked;
        if (amount == 0) revert NotEnoughBalance();

        stakingTokenBalance -= amount;

        // Calculate the penalty amount to the user
        uint256 _amountForUser = (amount * instantWithdrawalRate) / 100;
        uint256 _amountForContract = amount - _amountForUser;

        rewardTokenBalance += _amountForContract;

@>      if (stakers[_stakeMsgSender()].governanceDelegatee != address(0)) {
            // If the user has a governance delegatee, the tokens are stored in the surrogate contract
            DelegationSurrogate surrogate = storedSurrogates[stakers[_stakeMsgSender()].governanceDelegatee];
            IERC20(stakingToken).safeTransferFrom(address(surrogate), _receiver, _amountForUser);
        } else {
            // If the user does not have a governance delegatee, the tokens are stored in this contract
            IERC20(stakingToken).safeTransfer(_receiver, _amountForUser);
        }

        delete stakers[_stakeMsgSender()];

        emit TokensWithdrawn(_stakeMsgSender(), _receiver, amount);
    }
```
사용자가 토큰을 위임한 경우, `_amountForUser`는 대리인(surrogate)에서 수신자로 전송됩니다. 문제는 `_amountForContract`가 대리인에서 `BobStaking` 계약으로 전송되지 않아 해당 토큰이 대리인에 영구적으로 갇히게 된다는 것입니다.

게다가 페널티가 `rewardTokenBalance`에 추가되지만 실제로는 계약으로 전송되지 않으므로, `rewardTokenBalance`가 계약이 실제로 보유한 것보다 더 높은 가치를 반영하게 됩니다.


이 문제를 더 잘 설명하기 위해 다음 POC를 `BobStaking.t.sol`에 복사하십시오.

테스트가 올바르게 실행되려면 `storedSurrogates` 매핑을 `public`으로 설정하고 테스트 파일에 `import {DelegationSurrogate} from "@tally/staker/DelegationSurrogate.sol"`를 추가하십시오.
```solidity
function test_PenaltyIsNotTransferedToBobStakingContracts() public {
        // Deposit reward tokens
        stakingToken.approve(address(stakeContract), 1000 ether);
        stakeContract.depositRewardTokens(1000 ether);

        uint256 stakedAmount = 1 ether;
        vm.prank(stakerOne);
        stakeContract.stake(stakedAmount, stakerOne, 0);

        address delegatee = makeAddr("delegatee one");
        vm.prank(address(this));
        address[] memory whitelistedGovernanceDelegateesToAdd = new address[](1);
        whitelistedGovernanceDelegateesToAdd[0] = delegatee;
        stakeContract.setWhitelistedDelegatees(
            whitelistedGovernanceDelegateesToAdd, new address[](0), new address[](0), new address[](0)
        );

        uint256 timeOfStake = vm.getBlockTimestamp();

        vm.prank(stakerOne);
        stakeContract.alterGovernanceDelegatee(delegatee);

        assertEq(stakingToken.getVotes(delegatee), stakedAmount);

        uint256 contractBalanceBefore = stakingToken.balanceOf(address(stakeContract));

        vm.prank(stakerOne);
        stakeContract.instantWithdraw(stakerOne);

        uint256 contractBalanceAfter = stakingToken.balanceOf(address(stakeContract));

        DelegationSurrogate surrogate = stakeContract.storedSurrogates(delegatee);

        uint256 surrogateBalance = stakingToken.balanceOf(address(surrogate));

        assertEq(contractBalanceBefore, contractBalanceAfter);
        assertEq(surrogateBalance, stakedAmount / 2);  
    }
```
## 권장 사항
문제를 해결하려면 `_amountForContract`를 `BobStaking` 계약으로 전송하십시오.
```diff
function instantWithdraw(address _receiver) external nonReentrant {
        if (stakers[_stakeMsgSender()].unlockTimestamp > block.timestamp) {
            revert TokensLocked();
        }

        if (unbondEndTimes[_stakeMsgSender()] != 0) {
            revert UnbondAlreadyStarted();
        }

        _claimRewards(_stakeMsgSender(), false);

        uint256 amount = stakers[_stakeMsgSender()].amountStaked;
        if (amount == 0) revert NotEnoughBalance();

        stakingTokenBalance -= amount;

        // Calculate the penalty amount to the user
        uint256 _amountForUser = (amount * instantWithdrawalRate) / 100;
        uint256 _amountForContract = amount - _amountForUser;

        rewardTokenBalance += _amountForContract;

        if (stakers[_stakeMsgSender()].governanceDelegatee != address(0)) {
            // If the user has a governance delegatee, the tokens are stored in the surrogate contract
            DelegationSurrogate surrogate = storedSurrogates[stakers[_stakeMsgSender()].governanceDelegatee];
            IERC20(stakingToken).safeTransferFrom(address(surrogate), _receiver, _amountForUser);
+           IERC20(stakingToken).safeTransferFrom(address(surrogate), address(this), _amountForContract);
        } else {
            // If the user does not have a governance delegatee, the tokens are stored in this contract
            IERC20(stakingToken).safeTransfer(_receiver, _amountForUser);
        }

        delete stakers[_stakeMsgSender()];

        emit TokensWithdrawn(_stakeMsgSender(), _receiver, amount);
    }
```



# [C-02] 위임 후 스테이크가 전달되지 않아 포지션 출금 불가

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`BobStaking`에서 사용자가 `alterGovernanceDelegatee`를 통해 거버넌스를 위임하면 기존 스테이크가 `DelegationSurrogate`로 이동됩니다.
그러나 이후 `stake(_amount, receiver, lockPeriod)` 호출은 **새로운 토큰을 스테이킹 계약에 유지합니다**:

```solidity
IERC20(_stakingToken).safeTransferFrom(_stakeMsgSender(), address(this), _amount);
stakers[receiver].amountStaked += _amount;
```

`stakers[receiver].governanceDelegatee != address(0)`일 때 전달이 발생하지 않습니다.
출금 경로는 **모든** `amountStaked`가 대리인(surrogate)에 있다고 가정합니다:

* `unbond()`는 `safeTransferFrom(surrogate, this, amountStaked);`를 시도합니다.
* `instantWithdraw()`는 `safeTransferFrom(surrogate, _receiver, _amountForUser);`를 시도합니다.

스테이크의 일부가 이 계약에 남아 있는 경우(재스테이킹 후 흔함), 대리인은 **충분히 보유하고 있지 않으며**(승인하지 않음), 따라서 이러한 호출은 **되돌려집니다(revert)**. 사용자는 언본딩하거나 즉시 출금할 수 없으며 → 자금이 사실상 묶이게 됩니다.

**최소 재현**

1. 100 스테이킹 → 위임 → 100이 대리인으로 이동.
2. 50 다시 스테이킹 → 50이 스테이킹 계약에 남음; `amountStaked = 150`.
3. `unbond()` 또는 `instantWithdraw()` 호출 → 계약이 대리인으로부터 150을 가져오려고 시도 → **되돌려짐(revert)**.

## 권장 사항

* **위임 시 단일 보관 장소 강제 (선호됨):**
  `stake()`에서 `governanceDelegatee != 0`인 경우, 즉시 `_amount`를 사용자의 대리인으로 전달하십시오:

  ```solidity
  if (stakers[receiver].governanceDelegatee != address(0)) {
      DelegationSurrogate s = storedSurrogates[stakers[receiver].governanceDelegatee];
      IERC20(stakingToken).safeTransfer(address(s), _amount);
  }
  ```



# [H-01] 결함이 있는 잠금 기간으로 인해 적절한 잠금 없이 보너스 획득 가능

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

BonusWrapper의 `stake()`를 통해 사용자는 스테이킹 보너스를 받는 대가로 지정된 기간 동안 토큰을 잠글 수 있습니다.
```solidity
function stake(uint256 amount, address receiver, uint80 lockPeriod) external nonReentrant {
        // If the bonus period has ended, the lock period must be 0
        if (lockPeriod != 0 && bonusEndTime < block.timestamp) {
            revert BonusPeriodEnded();
        }

        // First transfer the user's tokens to this contract
        uint256 balanceBefore = IERC20(stakingToken).balanceOf(address(this));
        IERC20(stakingToken).safeTransferFrom(msg.sender, address(this), amount);
        uint256 actualAmount = IERC20(stakingToken).balanceOf(address(this)) - balanceBefore;

        uint256 bonus = _calculateBonus(actualAmount, lockPeriod);
        uint256 totalAmount;
        if (bonus > 0) {
            // try to claim the bonus for the user from the reward owner, revert if it fails. Reward owner needs to top up their account
            IERC20(stakingToken).safeTransferFrom(rewardOwner, address(this), bonus);
            totalAmount = amount + bonus;
            emit TokensBonus(receiver, bonus);
        } else {
            totalAmount = amount;
        }

        //staking is BobStaking
        stakingToken.approve(address(staking), totalAmount);
        staking.stake(totalAmount, receiver, lockPeriod);
    }
````

이것은 BobStaking의 `stake()`를 호출합니다.

```solidity
function stake(uint256 _amount, address receiver, uint80 lockPeriod) external nonReentrant {
        if (_amount == 0) revert ZeroTokenStake();
        // lock period must be valid
        //this needs to be sync with the lockPeriods in BonusWrapper
        if (!_contains(lockPeriods, lockPeriod)) {
            revert InvalidLockPeriod();
        }
        // If the user already has a lock period, the lock period supplied must be the same as the existing lock period
        if (stakers[receiver].lockPeriod != 0 && stakers[receiver].lockPeriod != lockPeriod) {
            revert InconsistentLockPeriod();
        }
        if (unbondEndTimes[receiver] != 0) {
            revert UnbondAlreadyStarted();
        }

        address _stakingToken = stakingToken;

        if (stakers[receiver].amountStaked > 0) {
            _updateUnclaimedRewardsForStaker(receiver);
        } else {
            stakers[receiver].timeOfLastUpdate = uint80(block.timestamp);
            stakers[receiver].conditionIdOflastUpdate = nextConditionId - 1;
            stakers[receiver].lockPeriod = lockPeriod;
            stakers[receiver].unlockTimestamp = uint80(block.timestamp) + lockPeriod;
        }

        IERC20(_stakingToken).safeTransferFrom(_stakeMsgSender(), address(this), _amount);

        stakers[receiver].amountStaked += _amount;
        stakingTokenBalance += _amount;

        emit TokensStaked(receiver, _amount);
    }
```

사용자가 처음 스테이킹하는 경우 정보가 `stakers` 매핑에 저장됩니다. 후속 스테이크의 경우 제공된 `lockPeriod`가 기존 것과 일치해야 합니다. 그러나 사용자가 먼저 `lockPeriod = 0`으로 스테이킹한 다음 나중에 다른 `lockPeriod`로 스테이킹하면 이 조건을 우회할 수 있습니다.

```solidity
if (stakers[receiver].lockPeriod != 0 && stakers[receiver].lockPeriod != lockPeriod) {
            revert InconsistentLockPeriod();
        }
```

사용자는 먼저 `lockPeriod = 0`으로 스테이킹하고 나중에 다른 `lockPeriod`(예: `21 * 30 days`)로 다시 스테이킹할 수 있습니다. `stakers[receiver].lockPeriod == 0`이므로 계약은 새로운 `lockPeriod`를 수락하고 확인이 트리거되지 않으며 함수는 되돌려지지 않습니다.

`unlockTimestamp`는 첫 번째 스테이크에서만 설정되므로, 사용자는 초기 `lockPeriod`를 사용하지만 잠금 보너스를 받게 됩니다. `unlockTimestamp`가 첫 번째 스테이크 중에만 초기화되므로 실제로는 토큰을 잠그지 않음에도 불구하고 말입니다.

공격자는 먼저 `lockPeriod = 0`으로 스테이킹한 다음 긴 잠금 기간으로 다시 스테이킹하여 실제로 토큰을 잠그지 않고 최대 보너스를 얻음으로써 이를 악용할 수 있습니다.

그들은 `instantWithdraw()`를 통해 즉시 출금할 수 있으며(페널티만 적용됨), 의도된 잠금을 피하면서 보너스를 효과적으로 얻을 수 있습니다. 또는 `unbond()`를 호출하고 언본딩 기간을 기다려 출금할 수 있으며, 실제로 토큰을 잠그지 않고도 동일한 이점을 얻을 수 있습니다.

또한 이 결함은 유사한 스테이킹 시나리오에서 관찰되는 다른 타이밍 관련 악용을 허용합니다:

* 사용자는 가장 짧은 잠금 기간(예: 3개월)으로 최소 금액을 스테이킹하고 이 잠금이 끝날 무렵 동일한 기간 동안 많은 금액을 스테이킹할 수 있습니다. 잠금 해제 시간이 업데이트되지 않으므로 거의 즉시 출금하면서 큰 보너스를 받을 수 있습니다.
* 사용자는 매우 긴 잠금(예: 21개월) 동안 소량(dust)을 스테이킹하고 만료될 때까지 기다린 다음 동일한 `lockPeriod`로 다시 많은 금액을 스테이킹하고 즉시 출금하여 `rewardOwner`를 고갈시킬 수 있습니다.
* 관리자가 나중에 `BonusWrapper::setBonusEndTime()`을 사용하는 경우, 공격자는 의도적으로 소량의 스테이크를 활성 상태로 유지하여 자격을 유지하고 부당하게 보너스를 극대화하기 위해 대규모 스테이크 타이밍을 맞출 수 있습니다.

주요 문제를 재현하려면 다음 POC를 `BonusWrapper.t.sol`에 복사하십시오.

```solidity
function test_FreeBonusWithoutLockingTokens() public {
        vm.startPrank(staker);

        stakingToken.approve(address(bonusWrapper), type(uint256).max);

        //lockPeriod = 0
        bonusWrapper.stake(400 * 10 ** 18, staker, 0);

        //lockPeriod = 21 * 30 days
        vm.expectEmit();
        emit BonusWrapper.TokensBonus(staker, 800 * 10 ** 18);
        bonusWrapper.stake(400 * 10 ** 18, staker, 21 * 30 days);

        stakeContract.unbond();
    }
```

## 권장 사항

문제를 해결하려면 `amountStaked > 0`인지 확인하고 `lockPeriod == 0`인 경우 다른 `lockPeriod` 설정을 방지하십시오.

```solidity
if (stakers[receiver].lockPeriod != 0 && stakers[receiver].lockPeriod != lockPeriod || stakers[receiver].amountStaked > 0 &&  stakers[receiver].lockPeriod != lockPeriod) {
            revert InconsistentLockPeriod();
        }
```

또한 기존 스테이커가 0이 아닌 `lockPeriod`로 더 많이 예치할 때 `unlockTimestamp`가 업데이트되거나 연장되어 모든 스테이킹된 토큰에 대해 유효한 잠금 기간을 유지하도록 하십시오.
선택적으로 정당한 사용자가 타이밍 이점을 만들지 않고 기존 스테이크에 추가할 수 있도록 짧은 유예 기간을 도입할 수 있습니다.




# [H-02] `address(0)`에 위임하면 `alterGovernanceDelegatee()`를 통해 계약이 비워짐

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`alterGovernanceDelegatee()`는 사용자의 토큰을 대리인에게 위임하는 데 사용됩니다.
```solidity
function alterGovernanceDelegatee(address newDelegatee) external nonReentrant {
        Staker storage staker = stakers[_stakeMsgSender()];
        if (staker.governanceDelegatee == newDelegatee) revert DelegateeUnchanged();
        if (staker.amountStaked == 0) revert ZeroTokenStake();

        //update rewards before
        _updateUnclaimedRewardsForStaker(_stakeMsgSender());

        DelegationSurrogate newSurrogate = _fetchOrDeploySurrogate(newDelegatee);

        if (staker.governanceDelegatee == address(0)) {
            // First time delegation, staker's tokens are in this contract
            IERC20(stakingToken).safeTransfer(address(newSurrogate), staker.amountStaked);
        } else {
            // Changing delegation, staker's tokens are in the old surrogate
            DelegationSurrogate oldSurrogate = storedSurrogates[staker.governanceDelegatee];
            IERC20(stakingToken).safeTransferFrom(address(oldSurrogate), address(newSurrogate), staker.amountStaked);
        }
        
        staker.amountStaked = staker.amountStaked;  
        staker.governanceDelegatee = newDelegatee;

        emit GovernanceDelegateeAltered(_stakeMsgSender(), newDelegatee);
    }
```
`alterGovernanceDelegatee()`는 사용자의 스테이킹된 토큰을 대리인에게 위임합니다. 사용자가 처음 위임할 때 토큰은 계약에서 대리인으로 전송됩니다. 후속 위임 시 토큰은 대리인 간에 이동합니다.

이전에 위임했던 사용자는 `newDelegatee = address(0)`으로 설정할 수 있습니다. 그렇게 되면 다음 전송은 사용자의 자체 스테이킹 금액이 아니라 계약 잔액(다른 사용자의 스테이크 및 보상을 보유함)에서 토큰을 가져옵니다.

먼저 0이 아닌 주소에 위임한 다음 `address(0)`에 반복적으로 위임함으로써 공격자는 제로 주소에 토큰을 위임하고 토큰을 대리인으로 전송하여 점진적으로 계약 잔액을 고갈시킬 수 있습니다.

문제를 재현하려면 다음 POC를 `BobStaking.t.sol`에 복사하십시오.
```solidity
function test_ContractCanBeCompletelyEmptied() public {
       // Deposit reward tokens
        stakingToken.approve(address(stakeContract), 1000 ether);
        stakeContract.depositRewardTokens(1000 ether);

        uint256 stakedAmount = 1 ether;
        vm.prank(stakerOne);
        stakeContract.stake(stakedAmount, stakerOne, 0);

        address delegatee = makeAddr("delegatee one");
        vm.prank(address(this));
        address[] memory whitelistedGovernanceDelegateesToAdd = new address[](1);
        whitelistedGovernanceDelegateesToAdd[0] = delegatee;
        stakeContract.setWhitelistedDelegatees(
            whitelistedGovernanceDelegateesToAdd, new address[](0), new address[](0), new address[](0)
        );

        uint256 timeOfStake = vm.getBlockTimestamp();

        address delegate0 = address(0);

        uint256 contractBalanceBefore = stakingToken.balanceOf(address(stakeContract));

        vm.prank(stakerOne);
        stakeContract.alterGovernanceDelegatee(delegatee);

        uint256 contractBalanceAfter = stakingToken.balanceOf(address(stakeContract));

        //user delegatee correctly the tokens
        assertEq(contractBalanceBefore - contractBalanceAfter, stakedAmount);
        
        //delegates to address(0)
        vm.prank(stakerOne);
        stakeContract.alterGovernanceDelegatee(delegate0);

        vm.prank(stakerOne);
        stakeContract.alterGovernanceDelegatee(delegatee);

        contractBalanceAfter = stakingToken.balanceOf(address(stakeContract));

        //the contract’s balance should remain constant since tokens are only being moved between delegates, but currently this is not happening
        assertEq(contractBalanceBefore - contractBalanceAfter, 2 * stakedAmount);
        //rewards are decreased by 1 ether, but this value should remain constant since it represents reward allocation
        assertEq(stakingToken.balanceOf(address(stakeContract)), 999 ether);
    }
```

## 권장 사항
문제를 해결하려면 `address(0)`에 대한 위임을 비활성화하십시오.



# [M-01] 즉시 출금 시 사용자가 자신의 페널티로 잔여 보상을 자체 조달 가능

_인정됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

사용자가 `BobStaking::instantWithdraw`를 호출하면 함수는 먼저 `_claimRewards(msg.sender, false)`를 호출합니다. `rewardTokenBalance`가 부족하면 `_claimRewards`는 부족분을 `residualRewardBalance[msg.sender]`에 적립합니다(그리고 `rewardsToPay == 0`을 지불할 수 있음). 그 직후 `instantWithdraw`는 페널티를 계산하여 `rewardTokenBalance`에 추가합니다:

```solidity
    function instantWithdraw(address _receiver) external nonReentrant {
        // ...
        
        _claimRewards(_stakeMsgSender(), false); #AUDIT: Here we may increase the residual rewards

        uint256 amount = stakers[_stakeMsgSender()].amountStaked;
        if (amount == 0) revert NotEnoughBalance();

        stakingTokenBalance -= amount;

        // Calculate the penalty amount to the user
        uint256 _amountForUser = (amount * instantWithdrawalRate) / 100;
        uint256 _amountForContract = amount - _amountForUser;

        rewardTokenBalance += _amountForContract; # AUDIT: Here we increase the rewardTokenBalance, so the before increased residual rewards, will now "self-payed", bypassing the instant withdraw fee effectively.

        // ...
    }
```

그런 다음 사용자는 `claimResidualRewards`를 호출하고 잔여 보상을 출금하여 사실상 즉시 출금 페널티를 풀에 지불하고 즉시 잔여금 지급으로 회수할 수 있습니다. 결과적으로 페널티는 전체 또는 일부(잔여금 크기에 의해 제한됨) 무력화되어 수수료의 목적을 훼손하고 비용을 다른 스테이커에게 전가합니다.

이 문제를 더 잘 이해하려면 다음 시나리오를 고려하십시오:
1. 풀의 `rewardTokenBalance`가 거의 0에 가깝습니다. 앨리스는 미지급 보상이 많이 쌓였습니다.
2. 앨리스가 `instantWithdraw(). _claimRewards(..., false)`를 호출하여 큰 `residualRewardBalance[Alice]`를 기록합니다.
3. `instantWithdraw`는 페널티를 `rewardTokenBalance`에 적립합니다.
4. 앨리스는 즉시 `claimResidualRewards`를 호출하고 방금 지불한 페널티로 자금이 조달된 잔여금을 출금합니다.
5. 앨리스의 유효 `penalty ≈ max(0, penalty − residual)`; `residual ≥ penalty`인 경우 그녀는 순 수수료를 지불하지 않습니다.

## 권장 사항

페널티를 풀에 적립하기 전에 잔여금과 상계하는 것이 좋습니다. `_claimRewards` 호출 후 `instantWithdraw`에서 다음을 수행하십시오:
```solidity
Step 1. uint256 residual = residualRewardBalance[msg.sender];
Step 2.1. If residual >= penalty: set residualRewardBalance[msg.sender] = residual - penalty; penalty = 0;
Step 2.2. Else: penalty -= residual; residualRewardBalance[msg.sender] = 0;
Step 3. Only then: rewardTokenBalance += penalty;
```
이는 사용자가 자신의 잔여금을 지불하기 위해 자신의 페널티를 재활용할 수 없음을 보장합니다.



# [M-02] 조건 설정 함수가 고장남

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`BobStaking::_setStakingCondition`을 사용하면 관리자가 `setRewardRatios()`, `setWhitelistedDelegatees()` 및 `setWhitelistedHybridNodeDelegateesViaController()`를 통해 스테이킹 조건을 변경할 수 있습니다.
그러나 `_setStakingCondition()`을 주의 깊게 살펴보면 위의 함수를 통해 매개변수로 전달될 때 스테이킹 조건에 대한 값을 올바르게 유지합니다:
```solidity
    /// @dev Additional entry point for the hybrid node controller to set the whitelisted hybrid node delegatees without going through the standard governance process
    function setWhitelistedHybridNodeDelegateesViaController(
        address[] memory _whitelistedHybridNodeDelegateesToAdd,
        address[] memory _whitelistedHybridNodeDelegateesToRemove
    ) external {
        if (!hasRole(HYBRID_NODE_CONTROLLER_ROLE, _msgSender())) revert NotAuthorized();

        StakingCondition storage condition = stakingConditions[nextConditionId - 1];
        _setStakingCondition(
            condition.baseRewardRatioNumerator,
            condition.governanceDelegationRewardRatioNumerator,
            condition.hybridNodeDelegationRewardRatioNumerator,
            new address[](0),                                                    <<@
            new address[](0),                                                    <<@
            _whitelistedHybridNodeDelegateesToAdd,                               <<@
            _whitelistedHybridNodeDelegateesToRemove                             <<@
        );
    }
```
```solidity
    function setRewardRatios(
        uint256 _baseNumerator,
        uint256 _governanceDelegationNumerator,
        uint256 _hybridNodeDelegationNumerator
    ) external {
        if (!_canSetStakeConditions()) {
            revert NotAuthorized();
        }

        StakingCondition storage condition = stakingConditions[nextConditionId - 1];
        if (
            _baseNumerator == condition.baseRewardRatioNumerator
                && _governanceDelegationNumerator == condition.governanceDelegationRewardRatioNumerator
                && _hybridNodeDelegationNumerator == condition.hybridNodeDelegationRewardRatioNumerator
        ) {
            revert RewardRatioUnchanged();
        }
        _setStakingCondition(
            _baseNumerator,
            _governanceDelegationNumerator,
            _hybridNodeDelegationNumerator,
            new address[](0),             <<@
            new address[](0),             <<@
            new address[](0),             <<@
            new address[](0)              <<@
        );

        emit UpdatedRewardRatios(_baseNumerator, _governanceDelegationNumerator, _hybridNodeDelegationNumerator);
    }
```
그러나 `_setStakingCondition()`은 마지막 조건 ID의 값을 유지하지 않으며, 제공된 매개변수를 최신 조건 ID에 직접 할당합니다.
따라서 `setWhitelistedHybridNodeDelegateesViaController()`, `setRewardRatios()`, `setWhitelistedDelegatees()`를 사용해도 실제로 `_whitelistedHybridNodeDelegateesToAdd` 주소가 과거 화이트리스트 열거형 집합에 추가되지 않습니다. 마찬가지로 `_whitelistedHybridNodeDelegateesToRemove`는 상태가 이월되지 않았으므로 마지막 조건에서 아무것도 제거하지 않습니다.


## 권장 사항

현재 조건 ID의 무결성을 보장하기 위해 마지막 조건 ID의 값을 유지하는 것이 좋습니다.



# [M-03] 보호되지 않은 수신자 잠금 기간으로 인한 스테이킹 DoS

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`BobStaking::stake()` 및 `BonusWrapper::stake()`를 통해 사용자는 보상과 보너스를 받기 위해 스테이킹할 수 있습니다. 이 함수들은 누구나 수신자를 대신하여 스테이킹할 수 있도록 허용하며, 스테이크에 0이 아닌 잠금 기간이 포함된 경우 추가 스테이크 호출은 유사한 잠금 기간 구성을 사용해야 합니다:
```solidity
    function stake(uint256 _amount, address receiver, uint80 lockPeriod) external nonReentrant {
        if (_amount == 0) revert ZeroTokenStake();
        // lock period must be valid
        if (!_contains(lockPeriods, lockPeriod)) {
            revert InvalidLockPeriod();
        }
        // If the user already has a lock period, the lock period supplied must be the same as the existing lock period
        if (stakers[receiver].lockPeriod != 0 && stakers[receiver].lockPeriod != lockPeriod) {            <<@
            revert InconsistentLockPeriod();
        }
       // . . .
    }
```
그러나 이 로직은 공격자가 소량(dust)을 사용하여 잠재적 스테이커를 프론트런/스팸하여 `lockPeriod`를 의도하지 않은 것으로 설정할 수 있도록 합니다. 예를 들어, 사용자가 3개월 동안 스테이킹하려고 하는 경우 공격자는 소량을 사용하여 6개월 또는 21개월 동안 스테이킹할 수 있습니다.
따라서 이러한 공격은 사용자에 대한 장기적인 스테이킹 DoS로 이어질 수 있습니다.

### 개념 증명

`BobStaking.t.sol` 내에 다음 테스트 케이스를 추가하십시오:
```solidity
    function test_BlockStakesUsingDust() public {
        uint256 initialBalance = stakingToken.balanceOf(stakerOne);
        uint256 stakeAmount = 400 * 10 ** 18;
        uint256 dustAmount = 1; // 1 wei dust

        vm.prank(stakerOne);
        stakingToken.approve(address(stakeContract), type(uint256).max);

        // Attacker address
        address attacker = makeAddr("attacker");
        // Transfer dust to random address
        vm.prank(address(this));
        stakingToken.transfer(attacker, dustAmount);

        // approve staking contract
        vm.prank(attacker);
        stakingToken.approve(address(stakeContract), type(uint256).max);

        // Attacker frontruns / randomly stakes dust along with valid amount on behalf of stakerOne
        vm.prank(attacker);
        stakeContract.stake(dustAmount, stakerOne, 3 * 30 days);

        // Check that stakerOne has dustAmount staked
        (uint256 amountStaked,, uint256 unlockTimestamp,,) = stakeContract.getStakeInfo(stakerOne);
        assertEq(amountStaked, dustAmount);

        // Now stakerOne tries to stake valid amount for 6 months, but there is dust already staked, hence fails with `InconsistentLockPeriod`
        vm.prank(stakerOne);
        vm.expectRevert(BobStaking.InconsistentLockPeriod.selector);
        stakeContract.stake(stakeAmount, stakerOne, 6 * 30 days);

    }
```

## 권장 사항

**옵션 1:**
각 잠금을 분리하고 스테이킹에 대한 최소 금액 보호를 구현하는 것이 좋습니다.

**옵션 2:**
첫 번째 스테이크 시 수신자의 동의를 요구하십시오. 예: `receiver == _stakeMsgSender()`를 강제하거나 `{receiver, lockPeriod, minAmount, deadline}`을 승인하는 수신자 서명 EIP-712 허가를 수락합니다.




# [L-01] 부족 시 잔여금 청구 되돌려짐(revert)

_인정됨_

`BobStaking::claimResidualRewards`는 `rewardTokenBalance`가 사용자의 잔여 할당량보다 낮을 때마다 되돌려지는 반면, `BobStaking::_claimRewards`는 이미 동일한 상황에서 부분 지급을 지원합니다. 풀에 자금이 일시적으로 부족한 경우 잔여금 청구가 완전히 차단되어 일부 토큰을 사용할 수 있음에도 불구하고 사용자가 지연됩니다. 사용 가능한 잔액을 전송하고, 0으로 만들고, 나머지를 빚으로 남겨두는 방식으로 `_claimRewards`와 동작을 일치시키십시오.

```solidity
function claimResidualRewards(address receiver) external nonReentrant {
    uint256 residualBalance = residualRewardBalance[_stakeMsgSender()];
    if (rewardTokenBalance < residualBalance || residualBalance == 0) revert NotEnoughBalance();

    residualRewardBalance[_stakeMsgSender()] = 0;
    // The residual rewards come from the reward token balance
    rewardTokenBalance -= residualBalance;

    IERC20(rewardToken).safeTransfer(receiver, residualBalance);

    emit ResidualRewardsClaimed(receiver, residualBalance);
}
```



# [L-02] 만료된 잠금이 새로운 스테이크 수락

_인정됨_

`BobStaking::stake`는 `unbondEndTimes[receiver] != 0`일 때만 예금을 차단하므로 `unlockTimestamp`가 이미 지난 포지션에 누구나 스테이크를 추가할 수 있도록 허용합니다. 즉, 만기된 포지션을 채우고 즉시 언본딩하여 신규 예금에 대한 의도된 잠금 기간을 우회할 수 있습니다. 사용자가 만료된 포지션에 스테이크를 추가할 때 잠금을 다시 초기화하거나(예: `set unlockTimestamp = max(currentUnlock, block.timestamp + lockPeriod)`), 사용자가 처음부터 다시 스테이킹할 때까지 추가 충전을 허용하지 않는 것을 권장합니다.

```solidity
// From BobStaking::stake
        if (stakers[receiver].amountStaked > 0) {
            _updateUnclaimedRewardsForStaker(receiver);
        } else {
            stakers[receiver].timeOfLastUpdate = uint80(block.timestamp);
            stakers[receiver].conditionIdOflastUpdate = nextConditionId - 1;
            stakers[receiver].lockPeriod = lockPeriod;
            stakers[receiver].unlockTimestamp = uint80(block.timestamp) + lockPeriod;
        }
```



# [L-03] 누락된 유효성 검사로 인해 `bonusEndTime`을 과거 타임스탬프로 설정 가능

_해결됨_

`BonusWrapper::setBonusEndTime`에는 새 타임스탬프가 미래인지 확인하는 유효성 검사가 없습니다. 관리자가 실수로 `bonusEndTime`을 과거 타임스탬프로 설정할 수 있으며, 이로 인해 0이 아닌 잠금 기간을 가진 모든 다음 스테이크 시도가 `BonusPeriodEnded()`로 되돌려집니다:

```solidity
function setBonusEndTime(uint256 _bonusEndTime) external onlyOwner {
    bonusEndTime = _bonusEndTime;
}
```

새 타임스탬프가 미래(또는 보상 분배를 차단하기 위해 `block.timestamp`)인지 확인하는 유효성 검사를 추가하는 것을 고려하십시오.



# [L-04] 주요 설정자에 대한 이벤트 누락

_인정됨_

`BonusWrapper::setBonusEndTime`, `BonusWrapper::setRewardOwner`, `BobStaking::setWhitelistedDelegatees`, `BobStaking::setWhitelistedHybridNodeDelegateesViaController`는 이벤트를 방출하지 않고 중요한 구성을 업데이트합니다. 운영자와 사용자가 구성 업데이트를 안정적으로 추적할 수 있도록 각 설정자에서 새 매개변수를 전달하는 전용 이벤트를 방출하는 것을 고려하십시오.
```solidity
function setBonusEndTime(uint256 _bonusEndTime) external onlyOwner {
    bonusEndTime = _bonusEndTime;
}

function setRewardOwner(address _rewardOwner) external onlyOwner {
    rewardOwner = _rewardOwner;
}
```



# [L-05] 다단계 나눗셈으로 인한 정밀도 손실

_인정됨_

`BobStaking::_calculateRewards()`의 보상 계산은 `TIME_UNIT` 및 `REWARD_RATIO_DENOMINATOR` 변수를 사용하며, 이는 새로운 보상을 누적하는 데 사용됩니다:
```solidity
    /// @dev Calculate rewards for a staker.
    function _calculateRewards(address _staker) internal view returns (uint256 _rewards) {
           // . ..

        for (uint256 i = _stakerConditionId; i < _nextConditionId; i += 1) {
            // . . .

            uint256 rewardsSum = _rewards + ((rewardsProduct / TIME_UNIT) / REWARD_RATIO_DENOMINATOR);      <<@

            _rewards = rewardsSum;
        }

        // Adding the boosted rate to the rewards, this is handled separately from the staking conditions
        if (block.timestamp < boostedRateEndTime) {
            uint256 boostedRewardsProduct =
                (block.timestamp - staker.timeOfLastUpdate) * staker.amountStaked * boostedRateNumerator;
            _rewards += ((boostedRewardsProduct / TIME_UNIT) / REWARD_RATIO_DENOMINATOR);            <<@
        } else if (block.timestamp >= boostedRateEndTime && staker.timeOfLastUpdate < boostedRateEndTime) {
            uint256 boostedRewardsProduct =
                (boostedRateEndTime - staker.timeOfLastUpdate) * staker.amountStaked * boostedRateNumerator;
            _rewards += ((boostedRewardsProduct / TIME_UNIT) / REWARD_RATIO_DENOMINATOR);             <<@
        }
```
그러나 관찰할 수 있듯이 이러한 계산에는 다단계 나눗셈이 포함되어 있어 정밀도 손실이 두 번 발생하여 장기간 사용자 보상이 손실될 수 있습니다.

정밀도 손실이 한 번만 발생하도록 `TIME_UNIT * REWARD_RATIO_DENOMINATOR`로 나누는 것이 좋습니다.



# [L-06] `withdrawRewardTokens()`는 `residualRewardBalance`를 무시하고 초과 출금 허용

_인정됨_

사용자가 `unbond()`를 호출할 때 `rewardTokenBalance`가 충분하면 보상이 즉시 사용자에게 적립되고 언본딩 기간이 끝나면 청구할 수 있습니다. 잔액이 충분하지 않으면 남은 보상은 `residualRewardBalance` 매핑에 저장됩니다. 현재 `withdrawRewardTokens()`는 다음과 같이 구현되어 있습니다:
```solidity
function withdrawRewardTokens(uint256 _amount) external nonReentrant {
    if (!hasRole(DEFAULT_ADMIN_ROLE, _msgSender())) revert NotAuthorized();
    if (_amount > rewardTokenBalance) revert NotEnoughBalance();

    IERC20(rewardToken).safeTransfer(_msgSender(), _amount);
    rewardTokenBalance -= _amount;

    emit RewardTokensWithdrawnByAdmin(rewardTokenBalance);
}
```

문제는 사용자의 `residualRewardBalance`를 고려하지 않아 일부가 이미 사용자를 위해 예약되어 있음에도 불구하고 관리자가 사용 가능한 것보다 더 많은 토큰을 출금할 수 있다는 것입니다.

권장 사항: `withdrawRewardTokens()`를 조정하여 모든 사용자의 총 `residualRewardBalance`를 고려하십시오. 한 가지 접근 방식은 전역 `totalResidualRewardBalance`를 사용하여 `_claimRewards()`에서 모든 사용자의 잔여 잔액을 추적하는 것입니다.
```diff
function withdrawRewardTokens(uint256 _amount) external nonReentrant {
        if (!hasRole(DEFAULT_ADMIN_ROLE, _msgSender())) revert NotAuthorized();
-       if (_amount > rewardTokenBalance) revert NotEnoughBalance();
+       if (_amount + totalResidualRewardBalance > rewardTokenBalance) revert NotEnoughBalance()

        IERC20(rewardToken).safeTransfer(_msgSender(), _amount);
        rewardTokenBalance -= _amount;

        emit RewardTokensWithdrawnByAdmin(rewardTokenBalance);
    }
```
> 참고: `totalResidualRewardBalance`는 보상이 잔여 잔액에 할당될 때 `_claimRewards()`에서 증가하고 사용자가 잔여 보상을 출금할 때 `claimResidualRewards()`에서 감소해야 합니다.



# [L-07] `TIME_UNIT`이 정확히 1년이 아님

_해결됨_

`TIME_UNIT`은 시간을 기준으로 보상을 계산하는 데 사용됩니다.
```solidity
/// @dev Time unit for the reward ratio calculation is 1 year
uint80 public constant TIME_UNIT = 12 * 30 days;
```
주석은 이것이 1년을 나타내야 함을 나타내지만 현재는 360일과 같습니다.

권장 사항: `TIME_UNIT`을 365일로 설정하십시오.



# [L-08] 사용자가 하이브리드 노드 위임을 거부할 수 없음

_해결됨_

`BobStaking::alterHybridNodeDelegatee` 함수는 `address(0)`을 포함한 모든 새로운 대리인 주소에 대해 화이트리스트 유효성 검사를 강제함으로써 사용자가 하이브리드 노드 위임을 거부하는 것을 방지합니다:

```solidity
function alterHybridNodeDelegatee(address newDelegatee) external nonReentrant {
    Staker storage staker = stakers[_stakeMsgSender()];
    if (staker.hybridNodeDelegatee == newDelegatee) revert DelegateeUnchanged();
    if (staker.amountStaked == 0) revert ZeroTokenStake();

    StakingCondition storage condition = stakingConditions[nextConditionId - 1];
    if (!condition.whitelistedHybridNodeDelegatees.contains(newDelegatee)) revert DelegateeNotWhitelisted();

    _updateUnclaimedRewardsForStaker(_stakeMsgSender());

    staker.hybridNodeDelegatee = newDelegatee;

    emit HybridNodeDelegateeAltered(_stakeMsgSender(), newDelegatee);
}
```

`address(0)`은 화이트리스트에 절대 포함되지 않으므로 하이브리드 노드 대리인을 설정한 사용자는 기본 스테이킹 보상만 받도록 재설정할 수 없습니다. 사용자가 하이브리드 노드 위임을 선택하면 이 보상 메커니즘 참여를 중단하고 싶더라도 화이트리스트 노드 중 하나에 영구적으로 위임해야 합니다.

**권장 사항**

`alterHybridNodeDelegatee`에서 사용자가 거부할 수 있도록 `address(0)`을 유효한 매개변수로 허용하는 것을 고려하십시오.



# [L-09] 보호되지 않은 `claimRewards()`를 이용하여 즉시 출금 수수료를 거부할 수 있음

_해결됨_

`claimRewards()` 함수는 보호되지 않은 특성으로 인해 누구나 수신자에 대한 보상을 청구할 수 있도록 허용합니다:
```solidity
    /**
     *  @notice    Claim accumulated rewards.
     *  @dev       Adds rewards to staked balance.
     */
    function claimRewards(address receiver) external nonReentrant {
        _claimRewards(receiver, true);
    }
```
그러나 이로 인해 일반적인 프로토콜 기능으로 인해 `rewardTokenBalance`가 0으로 떨어지면 공격자가 모든 적격 수신자에 대해 `claimRewards()`를 간단히 호출하여 잔여 보상 잔액이 누적되는 상황이 발생할 수 있습니다:
```solidity
    function _claimRewards(address receiver, bool shouldRevert) internal {
        _updateUnclaimedRewardsForStaker(receiver);
        if (stakers[receiver].amountStaked == 0) revert NoRewardsError();

        uint256 rewards = stakers[receiver].unclaimedRewards + _calculateRewards(receiver);

        uint256 rewardsToPay = rewards;

        if (rewards > rewardTokenBalance) {
            rewardsToPay = rewardTokenBalance;

            // add a residual reward balance for the user
            // note: penalty wont apply to this amount if instantWithdraw is called
            residualRewardBalance[receiver] += rewards - rewardTokenBalance;
            stakers[receiver].unclaimedRewards = 0;
        }
        // . . .
    }
```
이러한 잔여 보상 잔액은 즉시 출금 페널티에서 면제되며 `rewardTokenBalance`가 충분할 때 `claimResidualRewards()`를 통해 직접 출금할 수 있습니다.
따라서 이러한 공격은 사용자가 보상을 청구할 의도가 없더라도 강제로 청구하게 하여 프로토콜의 정당한 즉시 출금 페널티 수수료를 거부하는 불법 청구를 허용할 수 있습니다.

**권장 사항**

`msg.sender`만이 자신의 보상을 청구할 수 있도록 `claimRewards()` 함수를 보호하는 것이 좋습니다.



# [L-10] `_setStakingCondition()`에서 합계가 `REWARD_RATIO_DENOMINATOR`를 초과할 수 있음

_인정됨_

`_setStakingCondition()`은 보상 비율 분자와 대리인을 할당하는 데 사용됩니다.
```solidity
function _setStakingCondition(
        uint256 _baseNumerator,
        uint256 _governanceNumerator,
        uint256 _hybridNodeNumerator,
        address[] memory _whitelistedGovernanceDelegateesToAdd,
        address[] memory _whitelistedGovernanceDelegateesToRemove,
        address[] memory _whitelistedHybridNodeDelegateesToAdd,
        address[] memory _whitelistedHybridNodeDelegateesToRemove
    ) internal {
        uint256 conditionId = nextConditionId;
        nextConditionId += 1;

        stakingConditions[conditionId].baseRewardRatioNumerator = _baseNumerator;
        stakingConditions[conditionId].governanceDelegationRewardRatioNumerator = _governanceNumerator;
        stakingConditions[conditionId].hybridNodeDelegationRewardRatioNumerator = _hybridNodeNumerator;
        stakingConditions[conditionId].startTimestamp = uint80(block.timestamp);
        stakingConditions[conditionId].endTimestamp = 0;

        for (uint256 i = 0; i < _whitelistedGovernanceDelegateesToAdd.length; i++) {
            stakingConditions[conditionId].whitelistedGovernanceDelegatees.add(_whitelistedGovernanceDelegateesToAdd[i]);
        }
        for (uint256 i = 0; i < _whitelistedGovernanceDelegateesToRemove.length; i++) {
            stakingConditions[conditionId].whitelistedGovernanceDelegatees.remove(
                _whitelistedGovernanceDelegateesToRemove[i]
            );
        }

        for (uint256 i = 0; i < _whitelistedHybridNodeDelegateesToAdd.length; i++) {
            stakingConditions[conditionId].whitelistedHybridNodeDelegatees.add(_whitelistedHybridNodeDelegateesToAdd[i]);
        }
        for (uint256 i = 0; i < _whitelistedHybridNodeDelegateesToRemove.length; i++) {
            stakingConditions[conditionId].whitelistedHybridNodeDelegatees.remove(
                _whitelistedHybridNodeDelegateesToRemove[i]
            );
        }

        //store the time where this conditions needs to be applied
        if (conditionId > 0) {
            stakingConditions[conditionId - 1].endTimestamp = uint80(block.timestamp);
        }
    }
```
문제는 `_baseNumerator + _governanceNumerator + _hybridNodeNumerator`가 `REWARD_RATIO_DENOMINATOR`에 대해 확인되지 않아 합계가 100%를 초과할 수 있다는 것입니다.

이로 인해 `_calculateRewards()`에서 보상이 100% 이상으로 계산될 수 있습니다.
```solidity
uint256 rewardsProduct = (endTime - startTime) * (staker.amountStaked)
                * (
                    condition.baseRewardRatioNumerator + governanceDelegationRewardRatioNumerator 
                        + hybridNodeDelegationRewardRatioNumerator
                );

            uint256 rewardsSum = _rewards + ((rewardsProduct / TIME_UNIT) / REWARD_RATIO_DENOMINATOR);
```

**권장 사항**

이 문제를 해결하려면 `_setStakingCondition()`에서 `_baseNumerator + _governanceNumerator + _hybridNodeNumerator <= REWARD_RATIO_DENOMINATOR`인지 확인하고 이 조건이 충족되지 않으면 되돌려야 합니다.
```diff
function _setStakingCondition(
        uint256 _baseNumerator,
        uint256 _governanceNumerator,
        uint256 _hybridNodeNumerator,
        address[] memory _whitelistedGovernanceDelegateesToAdd,
        address[] memory _whitelistedGovernanceDelegateesToRemove,
        address[] memory _whitelistedHybridNodeDelegateesToAdd,
        address[] memory _whitelistedHybridNodeDelegateesToRemove
    ) internal {
        uint256 conditionId = nextConditionId;
        nextConditionId += 1;

+      require(_baseNumerator + _governanceNumerator + _hybridNodeNumerator =< REWARD_RATIO_DENOMINATOR, "REWARD_RATIO_DENOMINATOR superated");
 
        stakingConditions[conditionId].baseRewardRatioNumerator = _baseNumerator;
        stakingConditions[conditionId].governanceDelegationRewardRatioNumerator = _governanceNumerator;
        stakingConditions[conditionId].hybridNodeDelegationRewardRatioNumerator = _hybridNodeNumerator;
        stakingConditions[conditionId].startTimestamp = uint80(block.timestamp);
        stakingConditions[conditionId].endTimestamp = 0;

        for (uint256 i = 0; i < _whitelistedGovernanceDelegateesToAdd.length; i++) {
            stakingConditions[conditionId].whitelistedGovernanceDelegatees.add(_whitelistedGovernanceDelegateesToAdd[i]);
        }
        for (uint256 i = 0; i < _whitelistedGovernanceDelegateesToRemove.length; i++) {
            stakingConditions[conditionId].whitelistedGovernanceDelegatees.remove(
                _whitelistedGovernanceDelegateesToRemove[i]
            );
        }

        for (uint256 i = 0; i < _whitelistedHybridNodeDelegateesToAdd.length; i++) {
            stakingConditions[conditionId].whitelistedHybridNodeDelegatees.add(_whitelistedHybridNodeDelegateesToAdd[i]);
        }
        for (uint256 i = 0; i < _whitelistedHybridNodeDelegateesToRemove.length; i++) {
            stakingConditions[conditionId].whitelistedHybridNodeDelegatees.remove(
                _whitelistedHybridNodeDelegateesToRemove[i]
            );
        }

        //store the time where this conditions needs to be applied
        if (conditionId > 0) {
            stakingConditions[conditionId - 1].endTimestamp = uint80(block.timestamp);
        }
    }
```



# [L-11] 잔여금 재활용으로 공격자가 새로운 자본 없이 래퍼 보너스를 파밍할 수 있음

_해결됨_

**보너스 기간** 동안 `BonusWrapper.stake(...)`는 래퍼를 통해 예치된 금액에 대해 보너스를 제공합니다. `BobStaking.claimRewards(receiver)`는 **누구나** 호출할 수 있고, **잔여 보상**(`rewardTokenBalance`가 낮을 때 생성됨)은 **유동 토큰**으로 지급되기 때문에 공격자는 시스템을 조작할 수 있습니다:

**공격 흐름**

1. `rewardTokenBalance`에 자금이 있는 동안 공격자는 많은 사용자에 대해 `claimRewards(user)`를 호출합니다 → 그들의 보상은 `BobStaking` 내부에서 **자동 복리**됩니다(`BonusWrapper`를 통하지 않음). 보상 풀을 **소비**하고 BonusWrapper의 보너스 자격을 **얻지 못합니다**.
2. 그 직후, 풀이 이제 낮아졌을 때 공격자는 `claimRewards(attacker)`를 호출합니다 → 그들의 보상은 **`residualRewardBalance[attacker]`**로 이동합니다(복리 없음).
3. 관리자가 나중에 **보상을 예치**하면, 공격자는 **즉시 잔여금을 청구**합니다(이제 유동 ERC20).
4. 공격자는 **보너스 기간 동안 `BonusWrapper.stake`를 통해 동일한 토큰을 다시 예치**하여, **새로운 자본을 추가하지 않고** 보너스를 얻습니다(이미 얻은 보상을 래퍼를 통해 재활용하는 것일 뿐입니다).

**효과**

* **불공정한 보너스 포착**

**권장 사항**

자신에게만 청구하도록 제한:

```solidity
function claimRewards(address receiver) external nonReentrant {
    require(receiver == msg.sender, "Only self-claim");
    _claimRewards(receiver, true);
}
```



# [L-12] 누구나 다른 사람의 청구를 트리거하여 프론트런 보상 손실을 일으킬 수 있음

_해결됨_

`claimRewards(address receiver)`는 **모든 호출자**가 `receiver`를 전달할 수 있도록 합니다:

```solidity
function claimRewards(address receiver) external nonReentrant {
    _claimRewards(receiver, true);
}
```

`_claimRewards` 내부에서 계약은 **수신자의** 보상을 정산합니다. `rewardTokenBalance`가 낮을 때, 작은 부분만 스테이킹되고 나머지는 `residualRewardBalance[receiver]`로 푸시됩니다(복리되지 않고 나중에 일반 전송으로 지급됨).

악의적인 행위자는 사용자의 자체 청구를 **프론트런**하고 `claimRewards`를 호출하여 `rewardTokenBalance`를 줄일 수 있습니다. 이로 인해 피해자의 보상이 피해자가 자동 복리를 시도하기 직전에 `residualRewardBalance`로 강제 이동되고(그리고 `unclaimedRewards`는 0이 됨), 피해자는 잔여 부분에 대한 **복리 수익을 놓치게 됩니다**. 도난은 발생하지 않지만, 피해자의 APR을 감소시키는 반복 가능한 **괴롭힘(grief)**입니다.

**권장 사항**
자체 청구만 제한: `receiver == msg.sender`를 요구하십시오.



# [L-13] 부스트 윈도우가 30일이 아닌 `TIME_UNIT`으로 나누어 잘못 스케일링됨

_인정됨_

테스트에 표시된 대로 의도된 설계가 **"30일 기간 동안 분산된 고정 총 부스트 금액 지불"**인 경우 현재 구현은 과소 지불합니다. `_calculateRewards`에서 부스트 보상은 **부스트 윈도우 길이**가 아닌 **연간** 단위 `TIME_UNIT`(360일)으로 스케일링됩니다:

```solidity
// current
uint256 boostedRewardsProduct =
    (effectiveElapsedSeconds) * staker.amountStaked * boostedRateNumerator;

_rewards += ((boostedRewardsProduct / TIME_UNIT) / REWARD_RATIO_DENOMINATOR);
```

30일 윈도우(`boostedRateEndTime = now + 30 days`)와 `TIME_UNIT = 12 * 30 days`인 경우, 이는 테스트 파일이 보여주는 ~30일이 아닌 ~360일로 나눕니다. `boostedRateNumerator`가 **30일 기간 동안의 고정 총 부스트**를 나타내는 경우, 사용자는 의도된 부스트의 약 **1/12(~8.3%)**만 지급받습니다(≈ **91.7% 과소 지불**).

**권장 사항**

부스트 윈도우 길이로 스케일링하십시오(`TIME_UNIT` 아님).

