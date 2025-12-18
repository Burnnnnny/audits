
# Pashov Audit Group 소개


Pashov Audit Group은 업계에서 입증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 우승자이거나, 우리와 함께한 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 의욕적인 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도왔으며, 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위해 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.


# 면책 조항


스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.


# 서론

<p>Pashov Audit Group이 <strong>stake-dao/contracts-monorepo</strong> 저장소에 대해 시간 제한이 있는 보안 검토를 수행했으며, <strong>Stake DAO</strong>를 검토했습니다. 총 <strong>21</strong>개의 문제가 발견되었습니다.</p>

# Stake DAO 소개

<p>Stake DAO는 veTokenomics와 Liquid Lockers를 활용하여 여러 블록체인 프로토콜에 걸쳐 거버넌스 유틸리티, 수익 창출 및 유동성을 극대화하는 플랫폼입니다. sdTokens, 부스트 전략 및 온체인 Votemarket과 같은 제품을 통합하여 토큰 잠금, 투표권 및 크로스체인 보상 인센티브를 최적화합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [2fc661343b77cc0c153c58a2ca75578395c66907](https://github.com/stake-dao/contracts-monorepo/tree/2fc661343b77cc0c153c58a2ca75578395c66907)<br>&nbsp;&nbsp;(stake-dao/contracts-monorepo)

**수정 검토 커밋 해시:**<br>• [07874f72b8142090b261438617be93d671a99994](https://github.com/stake-dao/contracts-monorepo/tree/07874f72b8142090b261438617be93d671a99994)<br>&nbsp;&nbsp;(stake-dao/contracts-monorepo)

# 범위

- `Accountant.sol`
- `RewardVult.sol`
- `CurveLendingMarketFactory.sol`
- `CurveCryptoswapOracle.sol`
- `CurveStableswapOracle.sol`
- `MorphoMarketFactory.sol`
- `StrategyWrapper.sol`
- `interfaces/`

# 발견 사항



# [C-01] 입금 시 토큰당 추가 보상 업데이트 누락

_해결됨 (Resolved)_

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`StrategyWrapper._deposit()`은 입금 시 추가 보상에 대해 지급되는 토큰당 보상을 업데이트합니다.

```solidity
		// 3. Keep track of the Extra reward tokens checkpoints at deposit time
		address[] memory rewardTokens = REWARD_VAULT.getRewardTokens();
		for (uint256 i; i < rewardTokens.length; i++) {
	@>		checkpoint.rewardPerTokenPaid[rewardTokens[i]] = extraRewardPerToken[rewardTokens[i]];
		}
```

그러나 입금 중에 추가 토큰당 보상이 업데이트되지 않으므로 `extraRewardPerToken` 값은 추가 보상이 마지막으로 청구된 시간에 해당합니다.

결과적으로 마지막 청구 이후 누적된 추가 보상은 새로운 예금자에게 부당하게 분배되는 반면, 기존 예금자는 추가 보상의 공정한 몫을 받지 못하게 됩니다.

### 개념 증명 (Proof of concept)

`StrategyWrapper.t.sol` 파일에 다음 코드를 추가하고 `forge test --mt test_audit_extraRewardsUnfairDistribution`을 실행하십시오.

```solidity
function test_audit_extraRewardsUnfairDistribution() public {
	// Setup
	address firstUser = makeAddr("firstUser");
	address secondUser = makeAddr("secondUser");

	(RewardVault[] memory vaults,) = deployRewardVaults();
	RewardVault rewardVault = vaults[0];

	wrapper = new RestrictedStrategyWrapper(rewardVault, address(new MorphoMock()), address(this));

	vm.mockCall(
		address(protocolController),
		abi.encodeWithSelector(IProtocolController.isRegistrar.selector, address(this)),
		abi.encode(true)
	);
	rewardVault.addRewardToken(Common.WETH, address(this));

	// 1. First user deposits to the vault
	_depositIntoWrapper(rewardVault, firstUser, 1e18);

	// 2. Deposit some extra reward token into the reward vault
	uint128 extraRewardAmount = 1e20;
	deal(Common.WETH, address(this), extraRewardAmount);
	IERC20(Common.WETH).approve(address(rewardVault), extraRewardAmount);
	rewardVault.depositRewards(Common.WETH, extraRewardAmount);

	// 3. Second user deposits to the vault after 1 week
	skip(7 days);
	_depositIntoWrapper(rewardVault, secondUser, 100_000e18);

	// 4. Second user withdraws immediately after depositing
	_withdrawFromWrapper(secondUser, 100_000e18);

	// 5. First user claims extra rewards
	_claimExtraRewardsFromWrapper(firstUser);

	// The second user received most of the extra rewards, while they should have been
	// all distributed to the first user
	assertApproxEqRel(
		IERC20(Common.WETH).balanceOf(firstUser),
		extraRewardAmount * 1 / 100_001,
		1e15 // 0.1% (rounding)
	);
	assertApproxEqRel(
		IERC20(Common.WETH).balanceOf(secondUser),
		extraRewardAmount * 100_000 / 100_001,
		1e15 // 0.1% (rounding)
	);
}
```

## 권고 사항

```diff
        // 3. Keep track of the Extra reward tokens checkpoints at deposit time
        address[] memory rewardTokens = REWARD_VAULT.getRewardTokens();
+       _updateExtraRewardState(rewardTokens);
        for (uint256 i; i < rewardTokens.length; i++) {
            checkpoint.rewardPerTokenPaid[rewardTokens[i]] = extraRewardPerToken[rewardTokens[i]];
        }
```



# [C-02] `_update` 재정의(override) 누락으로 인해 체크포인트가 거의 항상 오래됨

_인정됨 (Acknowledged)_

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

전략 토큰은 Morpho Blue 시장에서 담보 토큰으로 사용됩니다. 체크포인트는 항상 `msg.sender`로 처리되므로 전송의 경우 체크포인트를 업데이트하는 것이 중요합니다. 현재 `_update` 함수는 전략 래퍼에서 재정의되지 않았으며, 이는 체크포인트가 거의 항상 오래된 상태임을 의미합니다.

이 상황의 영향은 많습니다:

1. 청산인(Liquidator)은 해당 토큰에 대한 체크포인트가 없기 때문에 LP 토큰을 상환(redeem)할 수 없습니다. 즉, 청산인에게는 가치가 없습니다.

```solidity
        UserCheckpoint storage checkpoint = userCheckpoints[msg.sender];
        checkpoint.balance -= amount; // revert here due to underflow
```

2. 차입자가 시장에서 청산되더라도 체크포인트가 여전히 존재하기 때문에 보상을 청구할 수 있습니다. 체크포인트는 `deposit` 및 `withdraw` 호출이 이루어지는 동안에만 업데이트됩니다.

3. ERC20의 대체 가능성(fungibility) 기능을 깨뜨립니다. 대체 불가능한 토큰처럼 동작합니다.

## 권고 사항

`_update` 함수를 재정의하고 그 안에서 체크포인트를 업데이트하십시오.



# [M-01] `StrategyWrapper.depositAssets()` 승인 누락으로 인해 되돌려짐(revert)

_해결됨 (Resolved)_

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`StrategyWrapper.depositAssets()`는 사용자가 받은 기본 LP 토큰을 보상 볼트에 입금하고 받은 지분(shares)을 래핑합니다.

그러나 입금 전에 함수는 래퍼 계약에서 기본 LP 토큰을 전송하도록 보상 볼트를 승인하지 않아 입금이 되돌려집니다.

결과적으로 `depositAssets()` 함수를 사용할 수 없으며, 사용자는 기본 LP 토큰을 보상 볼트에 직접 입금하고 대신 `depositShares()` 함수를 호출해야 합니다.

## 권고 사항

```diff
        SafeERC20.safeTransferFrom(IERC20(REWARD_VAULT.asset()), msg.sender, address(this), amount);
+       SafeERC20.forceApprove(IERC20(REWARD_VAULT.asset()), address(REWARD_VAULT), amount);
        uint256 shares = REWARD_VAULT.deposit(amount, address(this), address(this));
```



# [M-02] `IERC20Metadata` 인터페이스가 `USDT`와 호환되지 않음

_해결됨 (Resolved)_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`MorphoMarketFactory.create()`는 Morpho 프로토콜이 대출 토큰을 전송하도록 승인합니다. 그러나 `IERC20Metadata` 인터페이스는 `approve()` 시 값을 반환하지 않는 `USDT` 및 기타 비준수 ERC20 토큰과 호환되지 않아 트랜잭션이 되돌려집니다.

```solidity
        // Feed the market with exactly 1 token so that there is available liquidity for the subsequent borrow.
        uint256 loanToSupply = 10 ** loan.decimals();
        loan.safeTransferFrom(msg.sender, address(this), loanToSupply);
    @>  loan.approve(address(MORPHO_BLUE), loanToSupply);
```

결과적으로 `USDT` 및 유사한 토큰은 Morpho 시장에서 대출 토큰으로 사용할 수 없습니다.

## 권고 사항

```diff
-   loan.approve(address(MORPHO_BLUE), loanToSupply);
+   loan.forceApprove(address(MORPHO_BLUE), loanToSupply);
```



# [M-03] 초기 포지션의 유동성이 청산에 매우 가까움

_해결됨 (Resolved)_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

현재 배포 및 생성 후 Morpho Blue 시장 시스템은 직접 포지션을 엽니다. 그러나 이 포지션은 청산에 매우 가까워서 즉시 청산될 수 있습니다.

```solidity
        uint256 collateralToSupply = Math.mulDiv(
            Math.mulDiv(borrowAmount, 10 ** oracle.ORACLE_BASE_EXPONENT(), oracle.price(), Math.Rounding.Ceil),
            1e18,
            lltv,
            Math.Rounding.Ceil
        );
        uint256 buffer = 2 * (10 ** (collateral.decimals() - loan.decimals()));
        collateralToSupply += buffer;
```

여기서 추가 담보로 2 wei만 추가됩니다. 아마도 이자 발생으로 인해 다음 블록에서 청산될 수 있습니다.

## 권고 사항

모든 대출 프로토콜이 그랬던 것처럼 LLTV 지점이 아닌 LTV 지점에서 포지션을 만드십시오. (Morpho에는 LTV가 없습니다). 90%가 LLTV 레벨이라면 85%가 초기 포지션에 적합한 LTV 레벨일 수 있습니다.



# [M-04] 배포 함수가 DoS 공격 벡터에 취약함

_해결됨 (Resolved)_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

Morpho 시장을 생성하는 동안 Morpho Blue의 시장 매개변수에 대한 몇 가지 확인이 있습니다. 동일한 매개변수로 시장이 이미 생성된 경우 필요한 라인에서 실패합니다.

```solidity
        require(isIrmEnabled[marketParams.irm], ErrorsLib.IRM_NOT_ENABLED);
        require(isLltvEnabled[marketParams.lltv], ErrorsLib.LLTV_NOT_ENABLED);
@>      require(market[id].lastUpdate == 0, ErrorsLib.MARKET_ALREADY_CREATED);

        // Safe "unchecked" cast.
        market[id].lastUpdate = uint128(block.timestamp);
```

악의적인 행위자는 트랜잭션을 시뮬레이션하고 createMarket 함수로 시장 매개변수를 프런트러닝할 수 있습니다. 여기서 공격을 실행하려면 공격자가 오라클 주소를 결정해야 하지만, 오라클 주소는 컨트랙트의 nonce와 주소에 의존하기 때문에 이미 매우 쉽습니다.

## 권고 사항

시장이 이미 생성되었는지 여부를 확인하십시오.



# [M-05] 사용자가 보상을 청구하기 전에 입금하면 미청구 보상이 손실됨

_해결됨 (Resolved)_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`StrategyWrapper` 컨트랙트에서 사용자가 입금하면 rewardVault 지분이 래퍼로 전송되고 `_deposit()` 함수가 호출되어 사용자 잔고와 `rewardPerTokenPaid`를 체크포인트합니다.

```solidity
 function _deposit(uint256 amount) internal {
        // 1. Update the internal user checkpoint
        UserCheckpoint storage checkpoint = userCheckpoints[msg.sender];
        checkpoint.balance += amount;

        // 2. Keep track of the Main reward token checkpoint
        checkpoint.rewardPerTokenPaid[MAIN_REWARD_TOKEN_SLOT] = _getGlobalIntegral();

        // 3. Keep track of the Extra reward tokens checkpoints at deposit time
        address[] memory rewardTokens = REWARD_VAULT.getRewardTokens();
        for (uint256 i; i < rewardTokens.length; i++) {
            checkpoint.rewardPerTokenPaid[rewardTokens[i]] = extraRewardPerToken[rewardTokens[i]];
        }

        // 4. Mint wrapped tokens (1:1) for the caller
        _mint(msg.sender, amount);

        emit Deposited(msg.sender, amount);
    }
```

그러나 사용자가 이 입금 전에 미청구 보상이나 추가 보상이 있었던 경우, `rewardPerTokenPaid`가 현재 전역 값으로 **덮어씌워지기** 때문에 이러한 보상은 **손실**되어 사용자가 새 입금 전에 누적된 보상을 청구할 수 없게 됩니다.

## 권고 사항

사용자가 새로운 입금을 하기 전에 **대기 중인 보상을 청구**하도록 하십시오.

# [M-06] `oracle` 배포 시 잘못된 풀 주소로 인한 시장 생성 실패

_해결됨 (Resolved)_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`CurveLendingMarketFactory` 컨트랙트 `deploy()` 함수에서 오라클(`CurveCryptoswapOracle` 또는 `CurveStableswapOracle`) 배포 중에 전달된 `_curvePool` 매개변수가 `rewardVault.asset()`으로 잘못 설정되었습니다. 그러나 `_curvePool`은 LP 토큰 가격을 가져오는 데 사용되는 실제 Curve 풀의 주소여야 합니다.

적절한 Curve 풀 주소 대신 `rewardVault.asset()` 주소를 사용하면 오라클 가격 가져오기가 실패합니다. 결과적으로 가격을 가져오려는 시도가 실패(`CURVE_POOL.price()`가 되돌려짐)하므로 시장을 생성하려는 모든 시도가 실패합니다.

```solidity
function deploy(
        IRewardVault rewardVault,
        StableswapOracleParams calldata oracleParams,
        MarketParams calldata marketParams,
        ILendingFactory lendingFactory
    ) external onlyOwner returns (IStrategyWrapper, IOracle, bytes memory) {
        //...

        // 2. Deploy the oracle
        IOracle oracle = new CurveStableswapOracle(
            rewardVault.asset(),
            address(collateral),
            oracleParams.loanAsset,
            oracleParams.loanAssetFeed,
            oracleParams.loanAssetFeedHeartbeat,
            oracleParams.baseFeed,
            oracleParams.baseFeedHeartbeat
        );
        //...
    }
```

```solidity
onstructor(
        address _curvePool,
        address _collateralToken,
        address _loanAsset,
        address _loanAssetFeed,
        uint256 _loanAssetHeartbeat,
        address[] memory _token0ToUsdFeeds,
        uint256[] memory _token0ToUsdHeartbeats
    ) {
        //...
        CURVE_POOL = ICurveCryptoSwapPool(_curvePool);
        //...
    }
```

## 권고 사항

`rewardVault.asset()` 대신 올바른 Curve 풀 주소를 오라클 생성자에 전달하도록 `CurveLendingMarketFactory.deploy()` 함수를 업데이트하십시오.



# [M-07] 오라클에 대한 Lp 가격 계산이 올바르지 않음

_인정됨 (Acknowledged)_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

crpto 및 stable swap 오라클 모두에서 먼저 LP 가격의 추정 가격을 얻지만 LP 토큰의 가격을 계산하기에는 충분히 정확하지 않습니다. LP 토큰의 가격은 Curve 풀 컨트랙트 내부의 추가 **ramp** 로직으로 추정되므로 계산이 부정확해집니다.

자산의 가격을 결정하려면 LP 토큰을 상환(redeem)하는 동안 다른 자산을 얼마나 인출할 수 있는지 확인해야 합니다. 예를 들어, 1e18 LP 토큰을 상환하여 1e18 wETH와 1e18 rETH를 반환한다면 LP 가격은 `1 + 1 * rETH_exchange_rate`와 같습니다. 그러나 Curve 풀에는 가격에 부정확하게 영향을 미치는 추가 `ramp` 로직이 있습니다.

#### get_virtual_price 함수

```vyper

@view
@external
def get_virtual_price() -> uint256:
    """
    @notice The current virtual price of the pool LP token
    @dev Useful for calculating profits
    @return LP token virtual price normalized to 1e18
    """
    D: uint256 = self.get_D(self._xp(self._stored_rates()), self._A())
    # D is in the units similar to DAI (e.g. converted to precision 1e18)
    # When balanced, D = n * x_u - total virtual value of the portfolio
    token_supply: uint256 = ERC20(self.lp_token).totalSupply()
    return D * PRECISION / token_supply
```

그러나 removeLiquidity 함수를 확인하면 로직이 이보다 훨씬 간단합니다. 정확한 가격을 얻으려면 잔고를 총 LP 공급량과 비교하기만 하면 됩니다.

> 참고: stable swap과 crypto swap 모두 동일한 문제가 존재합니다.

```vyper
@external
@nonreentrant('lock')
def remove_liquidity(_amount: uint256, min_amounts: uint256[N_COINS]) -> uint256[N_COINS]:
    """
    @notice Withdraw coins from the pool
    @dev Withdrawal amounts are based on current deposit ratios
    @param _amount Quantity of LP tokens to burn in the withdrawal
    @param min_amounts Minimum amounts of underlying coins to receive
    @return List of amounts of coins that were withdrawn
    """
    _lp_token: address = self.lp_token
    total_supply: uint256 = ERC20(_lp_token).totalSupply()
    amounts: uint256[N_COINS] = empty(uint256[N_COINS])

    for i in range(N_COINS):
        _balance: uint256 = self.balances[i]
        value: uint256 = _balance * _amount / total_supply
        assert value >= min_amounts[i], "Withdrawal resulted in fewer coins than expected"
        self.balances[i] = _balance - value
        amounts[i] = value
        if i == 0:
            raw_call(msg.sender, b"", value=value)
        else:
            assert ERC20(self.coins[1]).transfer(msg.sender, value)

    CurveToken(_lp_token).burnFrom(msg.sender, _amount)  # Will raise if not enough
```

또한 rETH stable 풀의 가격 차이를 확인했는데 실제 가격과 매우 다릅니다(보통 정확한 가격보다 낮습니다).

rETH stable swap 풀에 대한 올바른 가격 공식은 다음과 같습니다:

```vyper
eth_bal   = pool.balances[0]
reth_bal  = pool.balances[1]
rate_rETH = rETH_contract.exchangeRate()

# convert both to ETH‑units:
total_ETH_value = eth_bal + reth_bal * rate_rETH / 1e18

# get LP supply:
lp_supply = ERC20(lp_token).totalSupply()

# price per LP in ETH:
price = total_ETH_value / lp_supply
```

이러한 가격 불일치는 Morpho Blue 시장에서 잘못된 청산과 잘못된 계산을 초래합니다. 이것이 정확하게 계산하는 것이 중요한 이유입니다.

## 권고 사항

`get_virtual_price`/`lp_price`로 직접 가져오는 대신 잔고와 LP 토큰의 토큰 공급량을 사용하여 LP 토큰의 가격을 계산하십시오.



# [M-08] 오라클은 플래시 론 공격 벡터에 취약함

_인정됨 (Acknowledged)_

## 심각도

**영향:** 높음

**가능성:** 낮음 (Law -> Low 오타로 보임)

## 설명

두 오라클 모두 풀에서 LP 토큰의 현재 가격을 직접 가져옵니다. 그러나 여러 공격 벡터에 의해 매우 짧은 시간 범위 동안 가격이 조작될 수 있기 때문에 이는 안전하지 않습니다. 가장 큰 위협 중 하나는 플래시 론 공격 벡터입니다. 유동성 측면에서 풀이 불균형할 때 오라클은 LP 가격에 대해 잘못된 값을 반환합니다.

이 상황의 근본 원인은 오라클 가격 계산에서 Pool 상태를 직접 사용하는 것입니다. 이러한 종류의 시나리오에서는 TWAP와 같은 구현이 중요합니다. Morpho Blue는 이 취약한 오라클을 사용할 것이며, 공격자는 LP 토큰의 가격을 조작하여 모든 포지션을 청산할 수 있습니다.

```solidity
        uint256 priceLpInPeg = CURVE_POOL.get_virtual_price(); // @audit on-chain price without TWAP
```

## 권고 사항

불균형한 Curve pool 상태 시나리오를 제거하기 위해 TWAP 로직을 구현하십시오. 시간 가중 평균 가격은 현재 가격 계산보다 더 정확하고 매끄러울 것입니다.



# [L-01] `getConversionPath()`가 0 주소 피드를 처리하지 않음

_해결됨 (Resolved)_

`CurveCryptoswapOracle.getConversionPath()`는 가격 계산을 위한 변환 경로가 포함된 문자열을 반환합니다.

```solidity
    function getConversionPath() external view returns (string memory path) {
        uint256 length = token0ToUsdFeeds.length;
        for (uint256 i; i < length; i++) {
    @>      path = string.concat(path, token0ToUsdFeeds[i].description(), " -> ");
        }
        path = string.concat(path, " -> ", LOAN_ASSET_FEED.description());
    }
```

그러나 `token0ToUsdFeeds`가 변환 경로에서 "identity hop"을 나타내기 위해 0 주소를 포함할 수 있다는 점은 고려되지 않았습니다. 만약 그렇다면 0 주소에 대한 `description()` 호출 시 트랜잭션이 되돌려질(revert) 것입니다.

`getConversionPath()` 함수에서 0 주소에 대한 케이스를 처리하거나, 0 주소 설정이 실질적인 용도가 없으므로 애초에 설정하지 못하도록 하는 것이 좋습니다.



# [L-02] 레이어 2 네트워크에 대한 시퀀서 가용성 확인 부족

_인정됨 (Acknowledged)_

레이어 2 네트워크의 Chainlink 가격 피드는 시퀀서를 통해 업데이트되는데, 시퀀서는 사용 불가능 상태가 될 수 있습니다. [Chainlink 문서](https://docs.chain.link/data-feeds/l2-sequencer-feeds)는 시퀀서의 마지막으로 알려진 상태를 지속적으로 모니터링하고 기록하는 Sequencer Uptime Data Feed를 통합할 것을 권장합니다.

`CurveStableswapOracle` 및 `CurveCryptoswapOracle`에 대한 `_fetchFeedPrice()` 구현에는 이 유효성 검사가 부족하여 프로토콜이 오래된 가격을 사용할 수 있습니다.

시퀀서의 가용성에 대한 확인을 포함하도록 `_fetchFeedPrice()` 함수를 재정의하는 레이어 2 네트워크용 래퍼 계약을 만드는 것을 고려하십시오.



# [L-03] 일부 Curve 풀에 대해 `name()` 함수가 되돌려질(revert) 수 있음

_해결됨 (Resolved)_

[일부 Curve 풀](https://etherscan.io/address/0xDC24316b9AE028F1497c275EB9192a3Ea0f67022#code)은 `name()` 및 `decimals()` 함수가 없으므로 `ICurvePool` 인터페이스와 호환되지 않습니다.

그러한 풀의 경우 오라클 계약의 `name()` 함수가 되돌려질 것입니다.

```solidity
    function name() external view returns (string memory _name) {
        _name = string.concat(CURVE_POOL.name(), "-", LOAN_ASSET.symbol(), " Stable Oracle");
    }
```

이러한 경우를 처리하기 위해 폴백 메커니즘을 추가하는 것을 고려하십시오.

```diff
+   string private immutable POOL_NAME;

    constructor(
        address _curvePool,
        address _collateralToken,
        address _loanAsset,
        address _loanAssetFeed,
        uint256 _loanAssetHeartbeat,
        address _baseFeed,
-       uint256 _baseFeedHeartbeat
+       uint256 _baseFeedHeartbeat,
+	string memory _poolName
    ) {
+       try CURVE_POOL.name() returns (string memory poolName) {
+           POOL_NAME = poolName;
+       } catch {
+           require(bytes(_poolName).length > 0, "EmptyPoolName()");
+           POOL_NAME = _poolName;
+       }

(...)

	function name() external view returns (string memory _name) {
-		_name = string.concat(CURVE_POOL.name(), "-", LOAN_ASSET.symbol(), " Stable Oracle");
+		_name = string.concat(POOL_NAME, "-", LOAN_ASSET.symbol(), " Stable Oracle");
	}
```



# [L-04] `CurveLendingMarketFactory` 계약 크기가 메인넷 제한을 초과함

_해결됨 (Resolved)_

`CurveLendingMarketFactory` 계약이 메인넷 배포 크기 제한을 초과합니다. 옵티마이저 실행 횟수를 1로 설정하더라도 런타임 크기는 24,988 바이트인 반면 제한은 24,576 바이트입니다.

```shell
forge build --sizes
Runtime Size (B) | Initcode Size (B) | Runtime Margin (B) | Initcode Margin (B) |
24,988           | 25,327            | -412               | 23,825              |

(...)

Error: some contracts exceed the runtime size limit (EIP-170: 24576 bytes)
```



# [L-05] Curve 대출 시장 배포 시 프로토콜 ID 확인 부족

_해결됨 (Resolved)_

`CurveLendingMarketFactory.sol`에서 cryptoswap 오라클을 사용하여 Curve에 새 시장을 생성할 때 보상 볼트의 프로토콜 ID가 Curve의 예상 ID와 일치하는지 확인합니다.

```solidity
    function deploy(
        IRewardVault rewardVault,
        CryptoswapOracleParams calldata oracleParams,
        MarketParams calldata marketParams,
        ILendingFactory lendingFactory
    ) external onlyOwner returns (IStrategyWrapper, IOracle, bytes memory) {
        require(PROTOCOL_CONTROLLER.vaults(rewardVault.gauge()) == address(rewardVault), InvalidRewardVault());
@>      require(rewardVault.PROTOCOL_ID() == bytes4(keccak256("CURVE")), InvalidProtocolId());
```

그러나 stableswap 오라클을 사용하여 Curve에 새 시장을 생성할 때는 이 확인이 수행되지 않습니다.

일관성을 유지하고 배포 시 사용자의 실수를 방지하기 위해 stableswap 오라클용 `deploy()` 버전에 동일한 확인을 추가하는 것을 고려하십시오.



# [L-06] Morpho 시장 생성 시 과도한 자금이 필요할 수 있음

_해결됨 (Resolved)_

`MorphoMarketFactory.create()`는 시장 생성 시 제로 이용률 감쇠(zero utilization rate decay)를 방지합니다. 이는 [Morpho 문서의 지침에 따라](https://docs.morpho.org/curation/tutorials-market-v1/creating-market/#fill-all-attributes) 1개의 토큰을 공급하고 가치의 90%를 빌려서 수행됩니다.

그러나 권장 사항대로 1달러 상당의 대출 토큰을 공급하는 대신 1개의 대출 토큰이 공급됩니다. 이는 대출 토큰의 가격에 따라 시장을 생성하는 데 매우 많은 양의 자금이 필요할 수 있습니다. 예를 들어 대출 토큰이 WBTC인 경우 현재 가격으로 1 WBTC($118,000 상당)를 공급하고 공급된 WBTC의 90%에 해당하는 $106,200 상당의 담보 토큰을 추가해야 합니다. 시장을 생성하려면 총 $224,200 상당의 자금이 필요합니다.

`create()` 함수에서 공급할 대출 토큰의 양을 매개변수로 받아 시장이 더 합리적인 금액의 자금으로 생성될 수 있도록 하는 것을 고려하십시오.



# [L-07] 일부 IRM에 대해 Morpho 시장 생성이 실패할 수 있음

_해결됨 (Resolved)_

`MorphoMarketFactory.create()`는 시장 생성 시 제로 이용률 감쇠를 방지합니다. 이는 [Morpho 문서의 지침에 따라](https://docs.morpho.org/curation/tutorials-market-v1/creating-market/#fill-all-attributes) 1개의 토큰을 공급하고 가치의 90%를 빌려서 수행됩니다.

그러나 이 문제와 해결책은 Adaptive Curve IRM에 국한된 것임을 유의해야 합니다. 현재 Morpho에서 활성화된 유일한 IRM이지만 향후 [다른 것들](https://github.com/morpho-org/morpho-blue-irm/blob/main/src/fixed-rate-irm/FixedRateIrm.sol)이 추가될 수 있습니다. 최선의 경우 필요 없이 완화 전략이 수행되고, 최악의 경우 예치된 토큰의 90%까지 차입을 허용하지 않는 등의 IRM 구현 제한으로 인해 배포가 실패할 수 있습니다.

계약 및 공식 문서에 이 제한 사항을 문서화하거나 Adaptive Curve IRM이 사용될 때만 완화 전략을 적용하는 것이 좋습니다.



# [L-08] 소액 보상이 묶임

_인정됨 (Acknowledged)_

`StrategyWrapper` 컨트랙트에서 `_updateExtraRewardState()` 함수는 사용자가 `claimExtraRewards()`를 통해 추가 보상을 청구하기 전에 호출됩니다. 여기서 먼저 `RewardVault`에서 보류 중인 추가 보상을 가져온 다음 각 추가 보상 토큰에 대해 `extraRewardPerToken` 값을 다음과 같이 업데이트합니다:

```solidity
function _updateExtraRewardState(address[] memory tokens) internal {
        //...
        uint256[] memory amounts = REWARD_VAULT.claim(tokens, address(this));
        for (uint256 i; i < tokens.length; i++) {
            uint256 amount = amounts[i];
            if (amount > 0) extraRewardPerToken[tokens[i]] += Math.mulDiv(amount, 1e18, supply);
        }
    }
```

그러나 새로 청구된 금액이 `totalSupply`에 비해 너무 작으면 이 계산은 0으로 내림(round down)되어 사실상 값을 버리고 `extraRewardPerToken`에 대한 업데이트를 방지할 수 있습니다. 결과적으로 소량의 추가 보상 토큰은 계약에 갇혀 예금자가 청구할 수 없게 됩니다.

권장 사항:
설명되지 않은 토큰당 보상 금액을 저장하고 누적하여 나중에 `extraRewardPerToken`에 영향을 줄 만큼 충분히 커지면 추가하는 것을 고려하십시오.



# [L-09] 하트비트 구성 가능성 부족으로 인해 오라클이 Chainlink 변경에 취약함

_인정됨 (Acknowledged)_

`CurveStableswapOracle` 및 `CurveCryptoswapOracle` 컨트랙트 모두에서 Chainlink `heartbeat` 값은 배포 시 **불변(immutable)**으로 설정됩니다. Chainlink가 가격 피드의 하트비트 구성을 변경하는 경우(예: 유효한 업데이트 간의 지속 시간 증가), 이 컨트랙트의 오라클은 계속해서 이전 임계값을 사용합니다.

이로 인해 Chainlink 피드가 새로운 구성 하에서 예상대로 작동하더라도 오래된 데이터 확인 실패로 인해 `price()` 함수가 **되돌려집니다(revert)**.
이 문제는 문서에 따라 **인정된** 것으로 보이지만, Morpho 시장은 배포 후 **오라클 업데이트를 지원하지 않으므로** 이러한 오라클에 의존하는 모든 시장은 **중단되거나 손상되어** 차입 및 청산과 같은 핵심 기능에 영향을 줄 것입니다.

권장 사항:
거버넌스 또는 관리자가 업데이트할 수 있는 변경 가능한 저장 변수에 하트비트 값을 저장하는 것을 고려하십시오.



# [L-10] 오라클 가격 조작으로 인한 잘못된 담보 및 활용

_인정됨 (Acknowledged)_

`MorphoMarketFactory.create()` 함수에서 `collateralToSupply`의 양은 `CurveStableswapOracle` 또는 `CurveCryptoswapOracle`의 현재 `lp_price()`를 기반으로 계산됩니다. 그러나 두 오라클의 문서는 플래시 입금/출금으로 인해 **단일 블록 내에서 LP 토큰 가격이 조작될 수 있음**을 **인정**합니다.

`collateralToSupply`는 이 가격을 기반으로 배포자에게서 가져오기 때문에 두 가지 위험이 발생합니다:

- 가격이 인위적으로 낮으면 필요 이상의 담보가 배포자에게서 빠져나갑니다.
- 가격이 인위적으로 높으면 필요한 것보다 적은 담보가 빠져나가고 예상 활용 비율(예: 90%)이 정확하게 달성되지 않습니다.

블록이 정산된 후 LP 가격이 실제 값으로 떨어지면 포지션이 담보 부족으로 보일 수 있어 다음 블록에서 잠재적으로 청산이 트리거될 수 있습니다.

```solidity
 function create(IStrategyWrapper collateral, IERC20Metadata loan, IOracle oracle, address irm, uint256 lltv)
        external
        onlyDelegateCall
        returns (Id id)
    {
        //...
        uint256 collateralToSupply = Math.mulDiv(
            Math.mulDiv(borrowAmount, 10 ** oracle.ORACLE_BASE_EXPONENT(), oracle.price(), Math.Rounding.Ceil),
            1e18,
            lltv,
            Math.Rounding.Ceil
        );

        //...
        vault.safeTransferFrom(msg.sender, address(this), collateralToSupply);
        //...
    }
```

권장 사항:
시장 생성을 완료하기 전에 최근 가격에 대한 슬리피지 허용 오차 검증을 추가하는 것을 고려하십시오.



# [L-11] `CurveStableswapOracle.price()`가 담보 토큰의 가격을 과대평가할 수 있음

_해결됨 (Resolved)_

`CurveStableswapOracle.price()` 함수는 대출 토큰 측면에서 담보 토큰 1개의 가격을 반환합니다. 계산의 첫 단계는 다음과 같습니다:

1. "계정 단위"로 LP 토큰의 가격을 가져옵니다. 예를 들어 `cbBTC/wBTC` 풀의 경우 BTC 측면에서의 LP 토큰 가격이 됩니다.
2. 기본 피드에서 "계정 단위"의 가격을 USD로 가져옵니다. 예를 들어 BTC의 USD 가격이 됩니다.

```solidity
194:     function price() external view returns (uint256) {
195:         // 1. Get the price of the LP token in its "unit of account" (e.g., USD for USDC/crvUSD or ETH for wstETH/wETH).
196:         // This value always has 18 decimals.
197:         uint256 priceLpInPeg = CURVE_POOL.get_virtual_price();
198: 
199:         // 2. Get the price of the "unit of account" in USD from the base feed.
200:         uint256 pricePegInUsd;
201:         if (address(BASE_FEED) != address(0)) {
202:             pricePegInUsd = _fetchFeedPrice(BASE_FEED, BASE_FEED_HEARTBEAT);
```

따라서 `priceLpInPeg * pricePegInUsd`는 USD로 된 LP 토큰의 가격을 제공합니다(18 + `BASE_FEED_DECIMALS`로 스케일링됨).

첫 번째 단계에서 `get_virtual_price`는 풀의 총 유동성을 LP 토큰의 총 공급량으로 나눈 값을 반환하지만, 총 유동성 계산은 풀의 모든 토큰 가격이 동일하다고 가정합니다. 그러나 **반드시 그런 것은 아닙니다**.

`cbBTC/wBTC` 풀의 예에서 `BTC`, `cbBTC` 및 `wBTC`의 가격은 특히 변동성이 큰 기간 동안 크게 다를 수 있습니다. 기초 자산에 대한 편차를 제거하고 풀의 자산 중 하나(예: `cbBTC`)를 계정 단위로 사용할 수 있지만, `cbBTC`의 가격이 `wBTC`의 가격과 같다고 가정해야 하는데 이는 반드시 사실이 아닙니다.

 이로 인해 오라클이 LP 토큰의 가치를 과대평가하는 가격을 반환하여 잠재적으로 대출 시장에서 대출의 담보 부족으로 이어질 수 있습니다.

**권고 사항**

[이 기사](https://news.curve.finance/chainlink-oracles-and-curve-pools/)는 문제와 이를 완화하는 방법에 대한 좋은 개요를 제공합니다.

권장되는 접근 방식은 풀의 각 자산 가격을 USD 기준으로 쿼리하고 `pricePegInUsd` 변수에 최소 가격을 사용하는 것입니다. 이렇게 하면 담보 토큰이 항상 보수적으로 가격이 책정됩니다.

