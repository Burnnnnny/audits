# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**labs-solo/aegis-vault** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Aegis Vault 소개

Aegis Vault는 고급 전략을 사용하여 한 유형의 토큰을 다른 유형으로 자동 변환합니다. Uniswap V3 풀과 통합하여 유동성을 최적화하고 사용자가 선호도에 따라 다양한 방식으로 자금을 인출할 수 있도록 합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [b88e9915b8087aea18b3c7e51b5f5cecab014a8f](https://github.com/labs-solo/aegis-vault/tree/b88e9915b8087aea18b3c7e51b5f5cecab014a8f)

_수정 검토 커밋 해시_ - [0583052755dd9533f0b2dc1e2a6c16ca4ad3b8a1](https://github.com/labs-solo/aegis-vault/tree/0583052755dd9533f0b2dc1e2a6c16ca4ad3b8a1)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `AegisVault`
- `AegisVaultERC20`
- `AegisVaultFactory`
- `AegisVaultCore`
- `Constants`
- `ERC20Initializable`
- `SymbolLib`
- `UV3Math`
- `PctMath`

# 발견 사항

# [C-01] 스테이킹이 활성화되었을 때 Aegis Vault에 대한 DoS 공격

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

Aegis Vault의 스테이킹 기능이 활성화되면 중요한 작업 내에서 `preAction` 및 `postAction` 함수가 실행됩니다. 이러한 작업은 Berachain 보상 볼트와 상호 작용합니다. `preAction`은 언스테이킹하고 보상을 청구하며 보상을 파밍 계약으로 전송하고, `postAction`은 ICHI 지분을 다시 Berachain 보상 볼트에 스테이킹합니다.

그러나 `_unstakeAndClaimRewards` 내에서 `rewardsVault.exit`를 호출하는 데 취약점과 위험한 가정이 있습니다. 이는 전체 `stakedBalance`가 자체 스테이킹된 것이라고 가정합니다. 공격자가 자신의 지분을 Aegis vault에 위임하면 Aegis vault가 `exit`를 시도할 때 DoS가 발생합니다. 이는 `_checkSelfStakedBalance`가 먼저 호출자(vault)의 `_accountInfo[msg.sender].balance`에서 `delegateTotalStaked`를 뺀 값이 충분한지, 그리고 `exit`를 통해 호출될 때 `_accountInfo[msg.sender].balance`와 동일한 `amount`를 초과하지 않는지 확인하기 때문에 되돌리기(revert)됩니다. 이로 인해 호출자가 다른 사용자로부터 위임받은 잔액이 있는 경우 호출이 항상 되돌려집니다.

```solidity
   function exit() external nonReentrant {
>>>     uint256 amount = _accountInfo[msg.sender].balance;
>>>     _checkSelfStakedBalance(msg.sender, amount);
        _withdraw(msg.sender, amount);
        _getReward(msg.sender, msg.sender);
    }
```

```solidity
    function _checkSelfStakedBalance(address account, uint256 amount) internal view {
        unchecked {
            uint256 balance = _accountInfo[account].balance;
            uint256 delegateTotalStaked = _delegateStake[account].delegateTotalStaked;
>>>         uint256 selfStaked = balance - delegateTotalStaked;
>>>         if (selfStaked < amount) InsufficientSelfStake.selector.revertWith();
        }
    }
```

공격자는 쉽게 `delegateStake`를 호출하고 대상 Aegis vault를 `account`로 지정하여 Aegis vault의 `delegateTotalStaked`를 증가시킬 수 있습니다.

```solidity
    function delegateStake(address account, uint256 amount) external nonReentrant whenNotPaused {
        _stake(account, amount);
        if (account != msg.sender) {
            unchecked {
>>>             DelegateStake storage info = _delegateStake[account];
                uint256 delegateStakedBefore = info.delegateTotalStaked;
                uint256 delegateStakedAfter = delegateStakedBefore + amount;
                // `<=` and `<` are equivalent here but the former is cheaper
                if (delegateStakedAfter <= delegateStakedBefore) DelegateStakedOverflow.selector.revertWith();
>>>             info.delegateTotalStaked = delegateStakedAfter;
                // if the total staked by all delegates doesn't overflow, the following won't
                info.stakedByDelegate[msg.sender] += amount;
            }
        }
        emit DelegateStaked(account, msg.sender, amount);
    }
```

스테이킹을 비활성화하려고 시도할 때 `setStakingStatus`도 `_unstakeAndClaimRewards`를 호출하기 때문에 항상 실패하여 Aegis Vault가 동결됩니다.

## 권장 사항

`exit` 대신 `balanceOf - getTotalDelegateStaked`를 제공하여 `withdraw`를 사용한 다음 `getReward`를 사용하는 것을 고려하십시오.

# [H-01] BGT가 보상 토큰일 때 발생할 수 있는 잠재적 문제

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

Berachain에서 스테이킹하여 얻을 것으로 예상되는 보상 토큰은 잠재적으로 Bera Governance Token인 BGT입니다.

```solidity
    /// @notice ERC20 token in which rewards are denominated and distributed.
    ///         Typically the BGT(i.e. Berachain Governance Token)
    function REWARD_TOKEN() external view returns (IERC20);
```

BGT는 설계상 지정된 발신자를 제외하고는 양도할 수 없습니다. [문서](https://docs.berachain.com/learn/pol/tokens/bgt)에서:

> $BGT는 양도할 수 없으며 PoL 적격 자산(예: Bex의 유동성)에 유동성을 제공해야만 획득할 수 있습니다.

그리고 [토큰 계약](https://bartio.beratrail.io/address/0xbDa130737BDd9618301681329bF2e46A016ff9Ad/contract/80084/code)에서 볼 수 있듯이(비록 테스트넷이지만) 승인된 발신자만 토큰을 전송할 수 있습니다. 사용자는 토큰을 보내기 위해 승인을 받아야 하지만 누구나 토큰을 받을 수 있다는 점에 유의하는 것이 중요합니다.

```solidity
    function transfer(
        address to,
        uint256 amount
    )
        public
        override(IERC20, ERC20Upgradeable)
        onlyApprovedSender(msg.sender)
        checkUnboostedBalance(msg.sender, amount)
        returns (bool)
    {
        return super.transfer(to, amount);
    }
```

스테이킹이 활성화되어 있다고 가정하면 입금, 출금 및 리밸런싱 중에 `_preAction` 함수가 호출됩니다. 이 함수는 berachain vault에서 언스테이킹하고 수수료 수령인에게 보내려고 시도합니다.

```solidity
            for (uint256 i = 0; i < rewardsVaults.length; i++) {
                IBerachainRewardsVault rewardsVault = rewardsVaults[i];
                if (address(rewardsVault) != NULL_ADDRESS) {
                    _unstakeAndClaimRewards(rewardsVault); //@note
                    _transferRewardsToFarmingContract(self, rewardsVault); //@note
                }
```

여기서 두 가지 문제가 발생할 가능성이 있습니다.

1. 보상을 청구하려는 함수는 모두 실패합니다. aegis vault가 BerachainRewardsVaults로부터 토큰을 받을 수는 있지만 승인된 발신자가 아니기 때문에 파밍 계약으로 전송할 수 없기 때문입니다. 이는 잠재적으로 `_preAction` 함수에 의존하는 모든 주요 프로토콜 작업, `collectRewards` 함수 및 `setStakingStatus` 함수입니다.
2. 청구된 보상이 화이트리스트 발신자로 지정되지 않은 경우 파밍 계약에 갇힐 수 있습니다.

## 권장 사항

이를 처리하는 것은 외부 관리자 의존적이기 때문에 약간 까다롭습니다. 토큰이 전송되고 파밍 계약이 설정되기 전에 화이트리스트 상태에 대한 쿼리를 도입하는 것이 좋습니다. 계약이 화이트리스트에 없으면 전송을 건너뛰십시오.

예를 들어:

파밍 계약을 설정할 때 화이트리스트된 파밍 계약만 설정할 수 있도록 요구할 수 있습니다.

```diff
    function setFarmingContract(address _farmingContract) external override {
        _onlyAdmin();
+       IERC20 rewardToken = rewardsVault.REWARD_TOKEN();
+       require (rewardToken.isWhitelistedSender(_farmingContract), "Not whitelisted");
        if (berachainState.farmingContract != _farmingContract) {
            berachainState.farmingContract = _farmingContract;
            emit FarmingContract(msg.sender, _farmingContract);
        }
    }
```

`preAction` 및 `collectRewards`에서 보상 전송을 처리할 때 볼트 자체가 화이트리스트에 없으면 보상 전송을 건너뛸 수 있습니다.

```diff
    function _transferRewardsToFarmingContract(BerachainState memory self, IBerachainRewardsVault rewardsVault) private {
           if (self.farmingContract != NULL_ADDRESS) {
           IERC20 rewardToken = rewardsVault.REWARD_TOKEN();
           uint256 rewardBalance = rewardToken.balanceOf(address(this));
-          if (rewardBalance > 0 ) {
+          if (rewardBalance > 0 && rewardToken.isWhitelistedSender(address(this)) {
                rewardToken.safeTransfer(self.farmingContract, rewardBalance);
            }
        }
    }
```

전반적으로 수정 사항은 토큰에 대한 프로토콜의 계획에 따라 다르지만 토큰의 고유한 속성을 고려해야 합니다.

# [M-01] `getStakedAmount` 조작 가능

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`getStakedAmount`는 `balanceOf`를 호출하여 Berachain 보상 볼트 내의 Aegis vault의 `stakedAmount`를 반환합니다.

```solidity
    function getStakedAmount(IBerachainRewardsVault rewardsVault) private view returns (uint256 stakedAmount) {
        if (address(rewardsVault) != NULL_ADDRESS) {
            stakedAmount = rewardsVault.balanceOf(address(this));
        }
        /// @dev else stakedAmount defaults to 0
    }
```

이 함수는 `getUserBalance`, `getDepositPosition`, `getTargetPosition` 및 `checkUpkeep`과 같은 여러 중요한 getter 함수에서 사용되는 `_calculateAegisVaultAmountsInICHIVaultIncludingStaked`에 의해 호출됩니다.

공격자는 Berachain 보상 계약 내에서 `delegateStake`를 호출하고 Aegis Vault에 스테이킹된 잔액을 제공하여 이 값을 조작할 수 있으며, 이로 인해 해당 getter에서 반환된 값이 부정확해지고 실제 자체 스테이킹된 잔액을 사용하지 않게 됩니다.

```solidity
    function delegateStake(address account, uint256 amount) external nonReentrant whenNotPaused {
        _stake(account, amount);
        if (account != msg.sender) {
            unchecked {
                DelegateStake storage info = _delegateStake[account];
                uint256 delegateStakedBefore = info.delegateTotalStaked;
                uint256 delegateStakedAfter = delegateStakedBefore + amount;
                // `<=` and `<` are equivalent here but the former is cheaper
                if (delegateStakedAfter <= delegateStakedBefore) DelegateStakedOverflow.selector.revertWith();
                info.delegateTotalStaked = delegateStakedAfter;
                // if the total staked by all delegates doesn't overflow, the following won't
                info.stakedByDelegate[msg.sender] += amount;
            }
        }
        emit DelegateStaked(account, msg.sender, amount);
    }
```

## 권장 사항

다음과 같이 `getStakedAmount`를 수정하십시오.

```diff
    function getStakedAmount(IBerachainRewardsVault rewardsVault) private view returns (uint256 stakedAmount) {
        if (address(rewardsVault) != NULL_ADDRESS) {
-            stakedAmount = rewardsVault.balanceOf(address(this));
+            stakedAmount = rewardsVault.balanceOf(address(this)) - rewardsVault.getTotalDelegateStaked(address(this));
        }
        /// @dev else stakedAmount defaults to 0
    }
```

# [L-01] `resetICHIVaultApprovals` 제거

AegisVaultCore.sol에서 `resetICHIVaultApprovals`가 이제 사용자에게 공개되었지만 여전히 IAegisVaultOwnerActions 인터페이스에 있습니다. 대신 `IAegisVaultActions` 인터페이스로 이동하는 것이 좋습니다.

# [L-02] 입금 중 uniswap quoter와의 상호 작용

AegisVaultCore.sol은 uniswap quoter 계약과 상호 작용합니다. 이는 사용자가 targetVault에서 스왑할 경우 얼마나 받을지 계산하기 위해 입금 중에 사용됩니다.

```solidity
import { IQuoter } from "@uniswap/v3-periphery/contracts/interfaces/IQuoter.sol";
```

```solidity
    function __deposit(
//...
                // figure out how much token0 the user would get if they were to the swap on the targetVault
                uint256 realDeltaOut0 = _quoter.quoteExactInputSingle(
                    token1, token0, targetVault.fee(), ctx.userDepositAmount1.sub(ctx.userContributionToTotal1), 0
                );
//...
                // figure out how much token1 the user would get if they were to the swap on the targetVault
                uint256 realDeltaOut1 = _quoter.quoteExactInputSingle(
                    token0, token1, targetVault.fee(), ctx.userDepositAmount0.sub(ctx.userContributionToTotal0), 0
                );
  //...
```

그러나 Uniswap Quoter는 가스 집약적일 수 있으며 예기치 않은 되돌리기를 유발할 수 있으므로 온체인에서 사용하도록 설계되지 않았습니다. 실제로 uniswap은 백엔드에서 사용할 것을 권장합니다. [문서](https://docs.uniswap.org/contracts/v3/reference/periphery/lens/Quoter)에서:

> 스왑을 실행하지 않고 주어진 스왑에 대한 예상 금액 또는 입력 금액을 얻을 수 있습니다.

> 이 함수들은 가스 효율적이지 않으므로 온체인에서 호출해서는 안 됩니다. 대신 낙관적으로 스왑을 실행하고 콜백에서 금액을 확인하십시오.

`deposit` 함수가 얼마나 복잡한지를 고려할 때 가스 집약적일 수 있는 이러한 불안정한 외부 호출을 도입하면 예상치 못한 되돌리기, 가스 한도 도달 및 잠재적으로 사용자의 입금 거부로 이어질 수 있습니다.

이 문제를 해결하려면 약간의 재설계가 필요할 수 있습니다.

예를 들어, 토큰 평가를 위해 오라클을 사용하거나 uniswap에서 권장하는 대로 예상 금액을 인용하는 대신 실제 스왑의 출력을 사용하는 것입니다.

## Aegis 팀 의견

우리는 입금 중 가스 집약적인 Uniswap Quoter 상호 작용에 대한 우려를 인정합니다. 이를 완화하기 위해 가스 비용이 주된 관심사인 체인에서 활용할 수 있는 QuoterView 계약을 구현하고 테스트했습니다. 그러나 Berachain을 포함한 대부분의 경우 기본 AMM에 의해 배포된 표준 Uniswap V3 Quoter를 계속 사용할 것입니다.

또한, 모든 Aegis 예금자가 알려진 시나리오에서 Quoter를 완전히 우회하여 효율성을 더욱 최적화할 수 있는 doCheckImpliedSlippage 부울을 도입했습니다.

