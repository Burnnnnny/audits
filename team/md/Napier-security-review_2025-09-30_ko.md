# Pashov Audit Group 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 입증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 여러 번 우승했거나, 우리와의 감사에서 진정으로 탁월한 성과를 거두었습니다. 우리는 오직 검증되고 의욕적인 인재와만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도운 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위해 우리 팀이 최선을 다할 것을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

<p>Pashov Audit Group에 의해 <strong>napierfi/napier-v2</strong> 저장소에 대한 시간 제한 보안 검토가 수행되었으며, <strong>Ch_301, Said, JCN, merlinboii</strong>가 <strong>Napier</strong> 검토에 참여했습니다. 총 <strong>18</strong>개의 문제가 발견되었습니다.</p>

# Napier 정보 (About Napier)

<p>Napier V2는 사용자가 예금에 대해 고정 금리 수익을 얻거나 원금 및 수익 토큰을 통해 미래 수익 노출을 거래할 수 있게 해주는 수익 분리(yield-stripping) 프로토콜입니다. 사용자 지정 훅과 재담보(rehypothecation)를 통해 Uniswap V4와 통합하여 LP 자본 효율성을 극대화하고 수익 창출을 자동화합니다.</p>

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [7f1de6956883f976a95678ba749657b4c077caa7](https://github.com/napierfi/napier-v2/tree/7f1de6956883f976a95678ba749657b4c077caa7)<br>&nbsp;&nbsp;(napierfi/napier-v2)

**수정 검토 커밋 해시:**<br>• [3ac71f39eccfd4263ee1ae1942d09a99d10dfc6f](https://github.com/napierfi/napier-v2/tree/3ac71f39eccfd4263ee1ae1942d09a99d10dfc6f)<br>&nbsp;&nbsp;(napierfi/napier-v2)

# 범위 (Scope)

- `Events.sol`
- `Factory.sol`
- `TokiHook.sol`
- `TokiHookLogic.sol`
- `ITokiHook.sol`
- `AssetPriceProvider.sol`
- `StateView.sol`
- `TokiLens.sol`
- `TokiQuoter.sol`
- `AccessManager.sol`
- `TokiPoolDeployer.sol`
- `PoolFeeModule.sol`
- `ChainlinkOracleFactory.sol`
- `TokiLinearChainlinkOracle.sol`
- `TokiTWAPChainlinkOracle.sol`
- `LinearPrice.sol`
- `TokiOracle.sol`
- `TWAPPrice.sol`
- `TokiPoolToken.sol`
- `Types.sol`
- `ApproximationParams.sol`
- `FeePctsPool.sol`
- `Flags.sol`
- `Packing.sol`
- `ContractValidation.sol`
- `CurrencySettler.sol`
- `FeePctsPoolLib.sol`
- `HookletLib.sol`
- `LibAccessGuard.sol`
- `LibApproximation.sol`
- `LibExpiry.sol`
- `LibOracle.sol`
- `LibPauseGuard.sol`
- `LibRehypothecation.sol`
- `LiquidityAmounts.sol`
- `TokiSwap.sol`
- `TokiSwapBinSearch.sol`
- `V4Rehypothecation.sol`
- `WrapperFactory.sol`
- `NapierV2Immutables.sol`
- `PrincipalTokenRouter.sol`
- `SwapAggregatorRouter.sol`
- `TokiPoolRouter.sol`
- `TransientState.sol`
- `V4Permit2Payments.sol`
- `VaultConnectorRouter.sol`
- `Dispatcher.sol`
- `UniswapV4Router.sol`
- `FeeModule.so`

# 발견 사항 (Findings)

# [M-01] 스왑 후 `lnImpliedRate`로 오라클을 업데이트하면 TWAP 조작 허용 (Updating oracle with post-swap `lnImpliedRate` allows TWAP manipulation)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`TokiHookLogic.beforeSwap()`은 오라클 관측값을 새로운 `lnImpliedRate`로 잘못 업데이트합니다. 결과적으로 관측값은 **스왑 후** 금리를 사용하여 전체 경과된 간격을 "마감"하며, 마치 이전 관측 이후 전체 기간 동안 해당 금리가 활성 상태였던 것처럼 처리합니다.

```solidity
function beforeSwap(
    ...
) external returns (bytes4, BeforeSwapDelta, uint24) {
    PoolId id = key.toId();
    ITokiHook.PoolStorage memory state = $.s_states[id];

    --- SNIPPED ---
    // Compute swap result and update token account balances and fees in memory state
@1>  (BeforeSwapDelta returnDelta, SwapResult memory result) = _swap(env, key, params, hookData, state, immutables);

    {
        // Write state to storage: any state updates must be blocked by reentrancy guard
        // Update oracle
@2>     _updateOracle($.s_observations[id], state);

        // Write state to storage ...
    }
    --- SNIPPED ---
}
```

관측 변환기(observation transformer)는 마지막 관측 이후의 전체 시간에 새로운 금리를 곱합니다.

```solidity
function transform(Observation memory last, uint32 blockTimestamp, uint96 lnImpliedRate)
    internal
    pure
    returns (Observation memory)
{
    return Observation({
        blockTimestamp: blockTimestamp,
@3>     lnImpliedRateCumulative: last.lnImpliedRateCumulative
            + uint216(lnImpliedRate) * (blockTimestamp - last.blockTimestamp),
        initialized: true
    });
}
```

`state.lnImpliedRate`가 스왑 중에 업데이트되므로(`@1>`), 그 후 `_updateOracle`을 호출하면(`@2>`) 새 관측값은 스왑 후 금리가 마지막 관측 이후 전체 간격 동안 활성 상태였던 것처럼 기록합니다(`@3>`에 표시된 대로).

이는 부정확한 TWAP 계산으로 이어지며, 새로운 금리는 간격 끝의 짧은 기간 동안만 활성 상태였지 전체 기간 동안은 아니었기 때문입니다. **결과적으로 단일 스왑이 TWAP에 불균형적인 영향을 미칠 수 있으며, 조작된 금리가 전체 기간 동안 적용된 것처럼 처리됩니다**.

## 권장 사항

스왑 전 `lnImpliedRate`로 오라클을 업데이트하십시오. 스왑 전 금리를 사용하면 이전 블록의 마감 가격을 효과적으로 기록하기 때문입니다.

이는 [Uniswap](https://docs.uniswap.org/contracts/v2/concepts/core-concepts/oracles)의 블록 내 조작 저항 설계와 일치합니다. 새 블록의 첫 번째 관측값은 이전 블록의 마지막 거래에 의해 설정된 시장 가격을 반영합니다. 이제 오라클을 왜곡하려는 시도는 공격자가 다음 블록에 영향을 미치기 위해 블록 종료 가격을 설정해야 하며, 이는 비용이 많이 듭니다.

# [L-01] `HookSwap` 이벤트가 잘못된 lp 수수료 금액을 표시함 (`HookSwap` event shows wrong lp fee amount)

_해결됨_

모든 성공적인 스왑 후 `HookSwap` 이벤트가 방출되어 오프체인 인덱서에 거래에 대한 데이터를 제공합니다. 이벤트에는 `hookLPfeeAmount0`이라는 매개변수가 포함되어 있으며, 이는 풀의 유동성 제공자(LP)에게 지급되는 스왑 수수료의 일부를 나타내기 위한 것입니다.

그러나 현재 구현에서는 `result.swapFee`(총 스왑 수수료)를 이 매개변수에 잘못 전달합니다. `result.swapFee`에는 LP의 몫과 프로토콜/큐레이터의 몫(`result.fees`)이 모두 포함됩니다.

결과적으로 이벤트는 LP 부분만 의미하는 이름으로 총 수수료를 방출하여, 이 이벤트를 소비하는 모든 오프체인 서비스에 오해의 소지가 있고 잘못된 데이터를 제공합니다.

**코드 참조:**
```solidity
// in TokiHookLogic.sol#beforeSwap
(result.amount0, result.amount1, result.swapFee, result.fees) = TokiSwap.swap(...);

// ...

Events.emitHookSwap({
    // ...
    hookLPfeeAmount0: result.swapFee.toUint128(), // Incorrect: This is the total fee
    hookLPfeeAmount1: 0
});
```

**권장 수정 사항:**
유동성 제공자에게 발생하는 수수료 부분만 올바르게 보고하도록 이벤트 방출을 수정하십시오.

```diff
-        hookLPfeeAmount0: result.swapFee.toUint128(),
+        hookLPfeeAmount0: (result.swapFee - result.fees).toUint128(),
```
또는 `hookLPfeeAmount0`의 이름이 잘못된 경우 이름을 변경하십시오.

# [L-02] 백런닝(Back-running)이 큐레이터의 `unwindVault()` 작업을 무력화할 수 있음 (Back-running can neutralize curator's `unwindVault()` action)

_인정됨_

`TokiHook` 계약은 허가된 `unwindVault()` 함수를 제공하여 큐레이터가 비상시 또는 리밸런싱 목적으로 외부 재담보 금고에서 자금을 인출할 수 있도록 합니다. 그러나 계약에는 풀의 `targetRawTokenRatio`를 유지하기 위해 모든 스왑 후 자동으로 트리거되는 `_rebalance()` 함수도 포함되어 있습니다.

악의적인 사용자나 MEV 봇은 이 두 함수 간의 상호 작용을 악용할 수 있습니다. 멤풀에서 성공적인 `unwindVault` 트랜잭션을 관찰함으로써 공격자는 동일한 블록 내에서 동일한 풀에 대해 작은 스왑을 수행하는 백런 트랜잭션을 즉시 제출할 수 있습니다.

`unwindVault` 작업은 모든 자산을 금고에서 훅의 원시 잔액(raw balance)으로 이동시켜 원시 토큰 비율을 `targetRawTokenRatio`로 푸시합니다. 백런너의 후속 스왑은 `_rebalance()` 로직을 트리거하여 원시 비율이 구성된 최대치를 초과했음을 감지하고 방금 인출된 자산의 상당 부분을 동일한 금고에 자동으로 다시 입금합니다.

이는 큐레이터의 관리 조치를 효과적으로 무력화하고, `unwindVault` 호출에 사용된 가스를 낭비하며, 큐레이터가 나가려고 했던 잠재적으로 손상된 금고에 LP의 자금을 노출시킵니다.
참고: PT가 만기에 도달한 경우에는 불가능합니다.

**권장 사항**
프로토콜은 큐레이터가 자동 리밸런싱 기능을 일시적으로 중단할 수 있는 메커니즘을 도입해야 합니다.

# [L-03] `AssetPriceProvider`가 오래된 Chainlink 가격 확인을 놓침 (`AssetPriceProvider` misses stale Chainlink price check)

_인정됨_

`AssetPriceProvider.sol`의 `tryGetPriceUSDInWad()` 함수는 Chainlink 가격 피드 오라클에서 자산 가격(USD)을 가져오는 역할을 합니다. 이 함수는 오라클 계약에서 `latestRoundData()`를 올바르게 호출하여 가격을 검색합니다.

그러나 가격과 함께 반환되는 `updatedAt` 타임스탬프를 확인하지 못합니다. 검색된 가격 데이터가 최신인지 확인하는 검증이 없습니다.

Chainlink 오라클 네트워크에 중단이 발생하거나 장기간 온체인 가격 피드를 업데이트하지 못하는 경우, 이 함수는 마지막으로 알려진(오래된) 가격을 마치 현재 가격인 것처럼 계속 반환합니다.

**권장 수정 사항:**
```diff
// in AssetPriceProvider.sol
+   uint256 private constant STALE_PRICE_GRACE_PERIOD = 3 hours;

    // ... inside tryGetPriceUSDInWad ...
    try oracle.latestRoundData() returns (
        uint80, int256 retAnswer, uint256, uint256 updatedAt, uint80
    ) {
+       if (block.timestamp > updatedAt + STALE_PRICE_GRACE_PERIOD) {
+           // Price is stale, treat as if the call failed
+       } else {
            answer = retAnswer;
+       }
    } catch {}
```

# [L-04] 높은 `PrincipalToken` 수수료로 인해 단면 유동성 계산이 빗나감 (Single-sided liquidity calculation off with high `PrincipalToken` fees)

_인정됨_

`_splitUnderlyingTokenLiquidityKeepYt` 함수는 유동성 제공을 위해 단일 기본 토큰(YBT)의 최적 분할을 계산합니다. 이는 풀의 현재 자산 비율을 기반으로 `amount0ToTokenize`를 계산합니다.

그러나 이 계산은 후속 `principalToken.supply()` 호출에 수수료가 없다는 잘못된 가정하에 수행됩니다(`performanceFee`가 합리적이라면 문제는 크지 않음). 계산이 이러한 수수료를 고려하지 않기 때문에 실제로 발행되는 원금 토큰(`PT`)의 양은 분할 계산에서 예상한 양보다 적습니다. 이로 인해 최종 `TP_ADD_LIQUIDITY` 명령에 전달되는 자산 쌍의 불균형이 발생합니다.
이 문제의 영향은 수수료 설정에 비례합니다. 일반적으로 작지만 `FeeModule`은 성과 및 발행 수수료를 최대 100%까지 허용하므로 이 기능 사용자에게 상당하고 피할 수 없는 가치 손실을 초래할 수 있습니다.
```solidity
FeeModule.sol#initialize()
//..
        if (performanceFee > MAX_FEE_BPS) {
            revert Errors.FeeModule_PerformanceFeeExceedsMaximum();
        }
```
`supply` 호출 중에 공제될 수수료(원금 토큰의 수수료가 큰 경우에만 해당)를 고려하도록 `_splitUnderlyingTokenLiquidityKeepYt()`의 계산을 업데이트하면 이를 피할 수 있습니다.
**코드 참조:**
```solidity
TokiPoolRouter.sol#_splitUnderlyingTokenLiquidityKeepYt()

uint256 amount0ToTokenize = (amount0 * balances.value1()) / (balances.value1() + TokiSwap.convertToAssets(balances.value0(), maxscale));
```

# [L-05] YT를 기본 토큰으로 변환할 때 유리한 스왑 잉여금이 사용자에게 반환되지 않음 (Favorable swap surplus not returned to user in YT to Underlying conversion)

_인정됨_

`YT_SWAP_YT_FOR_UNDERLYING` 명령은 사용자가 YT를 기본 토큰으로 판매하는 잽(zap)을 용이하게 합니다. 이 프로세스에는 사용자의 YT와 새로 획득한 PT를 결합하여 기본 자산을 상환하는 과정이 포함됩니다. PT는 내부 exact-out 스왑(`underlying -> PT`)을 통해 획득되며, 여기서 비용은 `underlyingDebt`로 미리 추정됩니다.

최종 사용자 지급액은 `underlyingWithdrawn - underlyingDebt`로 계산됩니다. 그러나 `underlyingDebt`는 스왑에 대한 미리 계산된 *최대* 입력입니다. 스왑에 소비된 기본 토큰의 *실제* 양은 `underlyingDebt`보다 적을 수 있습니다.

이 함수는 이 차이를 설명하지 못합니다. 실제 비용 대신 추정된 최대 부채를 사용하여 사용자 지급액을 계산하므로 스왑에서 발생한 잉여 자금은 `UniswapV4Router` 계약에 영구적으로 갇히게 됩니다.

이는 YT를 판매하는 사용자에게 작지만 직접적인 가치 손실을 초래합니다. 사용자는 내부 스왑 중 유리한 시장 움직임의 혜택을 받지 못하며 결과적인 잉여금은 라우터 계약에 갇힙니다.

**코드 참조:**
```solidity

TokiPoolRouter.sol#_swapYtForUnderlying()

uint256 underlyingDebt = uint256(-_underlyingDebt); // This is a pre-calculated maximum

// The actual swap happens inside combine(), and may consume less than underlyingDebt
uint256 underlyingWithdrawn = PrincipalToken(...).combine(...);

// This calculation uses the stale maximum debt, not the actual amount spent
uint256 amountOut = underlyingWithdrawn - underlyingDebt;

SafeTransferLib.safeTransfer(Currency.unwrap(key.currency0), recipient, amountOut); // This transfers an incorrect, smaller amount
```

**권장 수정 사항:**
```diff
-        SafeTransferLib.safeTransfer(Currency.unwrap(key.currency0), recipient, amountOut);
+        uint256 finalBalance = SafeTransferLib.balanceOf(Currency.unwrap(key.currency0), address(this));
+        if (finalBalance > 0) {
+            SafeTransferLib.safeTransfer(Currency.unwrap(key.currency0), recipient, finalBalance);
+        }
```

# [L-06] 기본 토큰-YT 잽에서 유리한 스왑 결과가 사용자에게 반환되지 않음 (Favorable swap outcomes not returned to user in Underlying-to-YT zap)

_인정됨_

`Dispatcher.sol`의 *YT_SWAP_UNDERLYING_FOR_YT* 명령은 사용자가 기본 토큰을 수익 토큰(YT)으로 스왑하는 복잡한 "잽" 트랜잭션을 용이하게 합니다. 이 작업에는 플래시 민트된 원금 토큰(PT)을 판매하여 기본 토큰 부채를 획득하는 내부 스왑이 포함됩니다. 이 내부 스왑은 추정 입력 금액(bestUnderlying)을 얻기 위해 시뮬레이션되지만 실제 스왑 실행은 더 많은 YBT 금액을 초래할 수 있습니다.

이 내부 스왑의 정산을 처리하는 `onSupply()` 콜백 함수는 추정된 `amountToTransfer`와 `bestUnderlying` 금액을 사용하여 PrincipalToken 계약에 부채를 올바르게 상환합니다.
```solidity
File: src/zap/modules/TokiPoolRouter.sol#onSupply()

payOrPermit2Transfer( Currency.unwrap(key.currency0)/*token*/, payer, Currency.unwrap(key.currency1)/*recipient PT*/, amountToTransfer.toUint160() );
 // ...
SafeTransferLib.safeTransfer(Currency.unwrap(key.currency0), msg.sender, bestUnderlying);
```

그러나 실제 스왑이 이 추정 금액(bestUnderlying)보다 더 많은 기본 토큰을 산출하는 경우, 잉여금(스왑의 "추가" 자금)은 트랜잭션을 시작한 사용자에게 환불되지 않습니다.

결과적으로 이러한 잉여 자금은 `UniswapV4Router.sol` 계약에 무기한 갇히게 됩니다.
코드 참조:
```solidity
 TokiPoolRouter.sol#onSupply()
function onSupply(uint256, /* underlyingDebt */ uint256 principals, bytes calldata data) external override {
    // ...
    // Executes the swap, receiving the actual amount which may be > bestUnderlying
    _unlock(abi.encodeCall(poolManager.unlock, (abi.encode(COMMAND_V4_SWAP_EXACT_IN, inputs))));
    // ...
```

# [L-07] `addLiquidity` 환불이 지분을 반환할 수 있어 통합 문제 발생 가능 (`addLiquidity` refunds may return shares, potentially causing integration issues)

_해결됨_

`addLiquidity`는 남은 금액을 기본 토큰 대신 ERC4626 금고 지분으로 `refundReceiver`에게 반환할 수 있습니다. 사용자/통합은 지분이 아닌 자산으로 환불을 기대할 수 있습니다. 수신자가 금고 지분을 처리하지 않는 계약인 경우 환불로 인해 자금이 묶일 수 있습니다.

```solidity
    function _deposit(DepositParams memory params) internal returns (DepositReturnData memory returnData) {
        // ..

        // Take tokens that is going to lie in the pool (custodied by PoolManager) - this will call unlockCallback.
        // Hook doesn't pull more tokens than the original amount user specified.\
        uint256 rawAmount0;
        {
           // ...

            if (refundShares0 > 0) {
>>>             SafeTransferLib.safeTransfer(address(params.vault0), params.refundReceiver, refundShares0);
            }
        }

        uint256 rawAmount1;
        {
            // ...

            if (refundShares1 > 0) {
>>>             SafeTransferLib.safeTransfer(address(params.vault1), params.refundReceiver, refundShares1);
            }
        }
```

현재 동작을 유지하는 경우 이를 명확하게 문서화하고 지분이 환불되었음을 나타내는 명시적 이벤트를 방출하십시오.

# [L-08] `initialAnchor`가 `>= IWAD`인지 검증되지 않음 (`initialAnchor` not validated to be `>= IWAD`)

_해결됨_

`initialAnchor`는 거래가 가장 자본 효율적인 지점을 중심으로 이자율을 조정하는 데 사용됩니다. `initialAnchor < IWAD`인 경우 음의 기준 금리를 의미하며, 초기 유동성을 추가할 때 초기 `lnImpliedRate`(및 확장하여 초기 `exchangeRate`)가 `< IWAD` 값으로 계산될 수 있습니다. 따라서 `initialAnchor < IWAD`는 풀에 대한 초기 유동성 공급을 되돌릴 수 있습니다.

```solidity
    function _getExchangeRateNoFee(
        uint256 totalAssets,
        uint256 totalPt,
        int256 rateScalar,
        int256 rateAnchor,
        int256 netPtToAccount
    ) internal pure returns (int256 exchangeRate) {
        int256 newTotalPt = totalPt.toInt256() - netPtToAccount;

        if (newTotalPt < 0) {
            revert Errors.TokiSwap_InsufficientPrincipalsLiquidity();
        }

        int256 proportion = newTotalPt * IWAD / (totalPt + totalAssets).toInt256();

        // Sanity check - Too much principal token is going to be in the pool
        if (proportion > MAX_PROPORTION) {
            revert Errors.TokiSwap_MarketProportionTooHigh();
        }

        int256 lnProportion = _lnProportion(proportion);
        exchangeRate = lnProportion * IWAD / rateScalar + rateAnchor;

        // Sanity check - Negative implied rate / Principal token in in premium is not allowed
        if (exchangeRate < IWAD) revert Errors.TokiSwap_ExchangeRateBelowOne(exchangeRate);
    }
```

이상적으로 사용자는 `TokiQuoter::computePoolParameters` 함수를 통해 최적의 `scalarRoot` 및 `initialAnchor` 값을 계산하며, 이 함수는 `initialAnchor < IWAD`를 산출해서는 안 됩니다. 그러나 사용자가 다른 수단을 통해 이러한 값을 계산할 수 있으므로 풀 배포 중에 `scalarRoot`와 `initialAnchor`를 모두 검증하는 것이 가장 좋습니다.

그러나 현재 `scalarRoot`만 검증됩니다.

```solidity
            (uint256 scalarRoot, int256 initialAnchor) = abi.decode(ammParams, (uint256, int256));
            if (scalarRoot == 0) Errors.TokiHook_InvalidScalarRoot.selector.revertWith();

            requestedCardinalityNext = cardinalityNext;
            immutables.scalarRoot = scalarRoot;
            immutables.initialAnchor = initialAnchor;
```

비교를 위해 Pendle은 시장 생성 시 제공된 `initialAnchor`가 `>= 1e18`이어야 합니다.

```solidity
    int256 public constant minInitialAnchor = PMath.IONE;
...
    function createNewMarket(
        address PT,
        int256 scalarRoot,
        int256 initialAnchor,
        uint80 lnFeeRateRoot
    ) external returns (address market) {
...
        if (initialAnchor < minInitialAnchor)
            revert Errors.MarketFactoryInitialAnchorTooLow(initialAnchor, minInitialAnchor);
```

풀 배포 시 `initialAnchor`도 검증하는 것을 고려하십시오.

# [L-09] 특권 함수에 대한 이벤트 방출 누락 (Missing event emission for privileged function)

_해결됨_

`FeeModule::updateFeeSplitRatio` 함수는 중요한 상태를 업데이트하지만 이벤트 방출이 누락되었습니다.

```solidity
    function updateFeeSplitRatio(uint256 _splitRatio) external restrictedBy(i_factory().i_accessManager()) {
        if (_splitRatio > MAX_SPLIT_RATIO_BPS) {
            revert Errors.FeeModule_SplitFeeExceedsMaximum();
        }
        if (_splitRatio == 0) {
            revert Errors.FeeModule_SplitFeeTooLow();
        }
        s_feePcts = FeePctsLib.updateSplitFeePct(s_feePcts, _splitRatio.toUint16());
    }
```

비교를 위해 `PoolFeeModule::updateFeeSplitRatio` 함수에는 상태 변경을 기록하기 위한 이벤트 방출이 포함되어 있습니다.

`FeeModule::updateFeeSplitRatio`에서 이벤트를 방출하는 것을 고려하십시오.

# [L-10] `TokiOracle`에 대한 추가 문서화 필요 (`TokiOracle` can benefit from further documentation)

_해결됨_

현재 `TokiOracle.md`는 사용자가 `twapWindow`를 `0`으로 지정하여 마지막으로 저장된 `lnImpliedRate`에서 현물 가격을 얻을 수 있다고 제안합니다. 그러나 Toki 풀에서 `lnImpliedRate`는 재담보 금고 수익으로 인해 지속적으로 변경됩니다. 이는 환율이 지속적으로 변경되고 있으며, 풀 상태에 기록된 마지막 `lnImpliedRate`가 풀의 실제 현물 `lnImpliedRate`와 같지 않을 가능성이 높음을 의미합니다.

따라서 `TokiOracle`이 자산의 현물 가격을 계산하는 데 사용되어서는 *안 된다*는 점을 명시적으로 문서화해야 합니다. 현물 가격은 업데이트된 `lnImpliedRate`를 고려하는 `TokiQuoter`에서만 독점적으로 읽어야 한다는 점에 유의해야 합니다.

```solidity
// TokiQuoter.sol

        // CHANGED: Rehypothecation Awareness
        // Dry-run swap to refresh lnImpliedRate due to rehypothecation vault interest
        _dryRunSwap(poolState, immutables, feePcts, true, 0);
...
    function _dryRunSwap(
        TokiSwap.PoolState memory poolState,
        ITokiHook.ImmutableParams memory immutables,
        FeePctsPool feePcts,
        bool zeroForOne,
        int256 amountSpecified
    ) internal view returns (int256 underlyingAmount, int256 principals) {
        ApproximationParams memory zeroApprox;
        // CHANGED: Dry-run Swap for Rate Refresh
        // Dry-run swap with 0-amount to refresh the lnImpliedRate without executing trades.
        //
        // KEY DIFFERENCE from Pendle:
        // - Pendle: lnImpliedRate remains static between trades
        // - Napier: lnImpliedRate changes continuously due to rehypothecation vault yield accrual
        //
        // Why this is needed:
        // - Rehypothecation vaults (e.g., Aave, Compound) earn interest continuously
        // - This changes the underlying asset value and affects PT pricing
        // - Without this refresh, quotes would become stale and inaccurate over time
        // - The dry-run simulates market dynamics to get current fair value
```

`lnImpliedRate`가 지속적으로 변경됨에 따른 또 다른 결과는 자산의 TWAP 가격이 시간이 지남에 따라 현물 가격에서 자연스럽게 더 멀어질 것이라는 점입니다. 이는 풀이 유휴 상태일 때, 즉 스왑이 발생하지 않을 때도 발생합니다.

또한 구성된 재담보 금고 중 하나가 수익률의 급격한 급등을 경험하면 TWAP 가격이 현물 가격과 상당히 달라질 수 있습니다. 이는 자연스럽게 발생할 수 있거나(가능성은 낮지만) 금고의 주당 가격이 조작되는 경우 발생할 수 있습니다. 이 경우 TWAP 가격은 현물 가격에 비해 과소 또는 과대 평가될 수 있습니다. 악의적인 행위자는 자산이 통합 프로토콜(예: 대출 프로토콜의 담보)에서 사용될 때 이 잘못된 가격 책정을 이용할 수 있습니다.

# [L-11] `v4-integration.md` 문서를 업데이트해야 함 (`v4-integration.md` docs should be updated)

_해결됨_

`v4-intergration.md`의 [Example Workflow](https://github.com/napierfi/napier-v2/blob/dev/uniswap/docs/v4-integration.md#example-workflow) 섹션, `Create new pool and add liquidity spending wstETH as initial liquidity` 예제는 `TP_ADD_LIQUIDITY` 명령에 대한 올바른 입력 값을 지정하지 않습니다.

```
const inputs = [
  // 1. PERMIT2_PERMIT_BATCH
  {
    data: viem.encodeAbiParameters(
      ABI_DEFINITION[CommandType.PERMIT2_PERMIT_BATCH],
      permitBatchParams
    ),
  },

  // 2. TP_CREATE_POOL
  {
    data: viem.encodeAbiParameters(
      ABI_DEFINITION[CommandType.TP_CREATE_POOL],
      createPoolParams
    ),
  },
  // 3. TP_SPLIT_INITIAL_LIQUIDITY
  {
    data: viem.encodeAbiParameters(
      ABI_DEFINITION[CommandType.TP_SPLIT_INITIAL_LIQUIDITY],
      [ZERO_KEY, amount0, MSG_SENDER, desiredImpliedRate]
    ),
  },

  // 4. TP_ADD_LIQUIDITY
  // Pass ZERO_KEY to load pool key right after creating the pool
  {
    data: viem.encodeAbiParameters(
      ABI_DEFINITION[CommandType.TP_ADD_LIQUIDITY],
      [ZERO_KEY, amount0, amount1, liquidityMinimum, ADDRESS_THIS]
    ),
  },
];

```

초기 유동성을 공급할 때 `TP_SPLIT_INITIAL_LIQUIDITY` 명령은 추가할 YBT 및 PT 금액이 라우터의 잔액에 저장되도록 합니다.

따라서 후속 `TP_ADD_LIQUIDITY` 명령은 `amount0` 및 `amount1` 대신 `ActionConstants.CONTRACT_BALANCE` 플래그를 PT 및 YBT 금액으로 지정해야 합니다.

# [L-12] `TokiQuoter`의 구조체에 사용되지 않는 필드가 있음 (Struct with unused fields in `TokiQuoter`)

_해결됨_

`TokiQuoter`의 `QuoteRemoveLiquidityResult` 구조체는 `TokiQuoter::quoteRemoveLiquidity`의 반환 값으로만 사용됩니다. 그러나 이 구조체의 두 필드만 실제로 이 함수에서 채워집니다.

```solidity
    struct QuoteRemoveLiquidityResult {
        uint256 amount0Out;
        uint256 amount1Out;
        int256 spotExchangeRateBefore;
        int256 executionExchangeRate;
        int256 priceImpact;
    }

    function quoteRemoveLiquidity(PoolKey memory key, uint256 liquidity)
        public
        view
        checkPoolKey(key)
        returns (QuoteRemoveLiquidityResult memory result)
    {
..
        // Amounts withdrawn from vaults + raw amounts
        result.amount0Out = assets0 + rawAmount0;
        result.amount1Out = assets1 + rawAmount1;
```

위에서 볼 수 있듯이 가격 영향 컨텍스트와 관련된 3개의 필드는 활용되지 않습니다. 또한 `TokiQuoter.md`의 문서는 `quoteRemoveLiquidity`가 가격 영향 컨텍스트를 반환할 것이라고 가정하지만 그렇지 않습니다.

`QuoteRemoveLiquidityResult` 구조체를 `amount0Out` 및 `amount1Out` 필드만 포함하도록 업데이트하고, 이 변경 사항을 반영하도록 문서를 업데이트하는 것을 고려하십시오.

# [L-13] `TokiOracle`이 재담보로 인해 LP 토큰 가격을 잘못 책정할 수 있음 (`TokiOracle` can potentially misprice LP tokens due to rehypothecation)

_인정됨_

Napier는 LP 토큰 가격 책정을 위해 Pendle의 접근 방식을 채택했습니다. 이는 현재 현물 내재 금리를 TWAP 내재 금리 쪽으로 이동시키는 [가상 거래를 모델링](https://github.com/pendle-finance/pendle-core-v2-public/blob/6ca87b6e5a823d603cb8f66f983f5bc63b53218a/whitepapers/LP_Oracle.pdf)합니다. 이 모델의 정확성은 풀 비율이 거래 간에 일정하게 유지된다는 Pendle의 가정에 달려 있습니다. 그러나 Toki 풀의 경우 재담보 금고 수익으로 인해 풀의 비율이 지속적으로 변경될 수 있으므로 이 가정은 Napier에 적용되지 않습니다. 이러한 비례적 변화는 내재 금리(`lnImpliedRate`)에 직접적인 영향을 미치며, 이는 시간이 지남에 따라 현물 내재 금리가 지속적으로 변화함을 의미합니다.

그러나 `TWAPPrice::_convertLpToAssetsRaw`에 구현된 LP 오라클의 현재 구현은 거래 간에 `lnImpliedRate`가 정적인 것처럼 `rateBlended`를 계산합니다.

```solidity
(int256 rateOracle, int256 rateBlended) =
    _computeExchangeRates(poolState.lnImpliedRate, key, twapWindow, expiry); // `poolState.lnImpliedRate` is stale previously stored rate

```

```solidity
    function _computeExchangeRates(uint96 lastLnImpliedRate, PoolKey memory key, uint32 twapWindow, uint256 expiry)
        private
        view
        returns (int256 rateOracle, int256 rateBlended)
    {
        uint256 timeToExpiry = expiry - block.timestamp;

        uint256 twapLnImpliedRate = getTwapLnImpliedRate(key, twapWindow);
        rateOracle = TokiSwap.convertToExchangeRate(twapLnImpliedRate, timeToExpiry);

        int256 rateLastTrade = TokiSwap.convertToExchangeRate(lastLnImpliedRate, timeToExpiry); // @audit: exchange rate does not reflect rehypothecation yield

        rateBlended = (rateLastTrade + rateOracle) / 2;
    }
```

`_computeExchangeRates` 함수는 `rateLastTrade`를 계산하기 위해 오래된 `poolState.lnImpliedRate`(현재 저장된 `lnImpliedRate`에서)를 사용하는 반면, 다른 매개변수(예: `ammParams.rateAnchor`, `ammParams.totalAssets`, `balances.value1()` 및 `balances.value0()`)는 금고 수익을 포함하는 업데이트된 풀 잔액에서 파생됩니다.

이는 전반적인 방정식에 비대칭성을 도입합니다. 다른 모든 매개변수는 금고 수익을 포함하는 업데이트된 잔액을 반영하는 반면, `rateLastTrade` 및 궁극적으로 `rateBlended`는 해당 업데이트된 잔액에 해당하지 않는 오래된 내재 금리를 사용하여 계산되기 때문입니다.

결과적으로 오라클이 계산한 LP 토큰 가격은 의도한 것보다 현물 가격에서 더 많이 벗어날 수 있습니다. 이 잘못된 가격 책정의 규모는 정상적인 상황(높은 거래량 및 적당하고 일관된 금고 수익률)에서는 작을 것으로 예상되지만, 활동이 적은 풀이나 스왑 사이에 금고 수익률이 크게 누적되는 경우 잠재적으로 더 커질 수 있습니다.

`rateBlended`가 현재 풀 비율과 일치하도록 `poolState.lnImpliedRate`를 갱신하기 위해 null "드라이 런 스왑"( `TokiQuoter`에서 수행되는 방식과 유사)을 수행하는 것을 고려하십시오.

# [L-14] `UniswapV4Router`를 통해 호출되는 특정 명령 시퀀스는 괴롭힘(griefed)을 당할 수 있음 (Certain command sequences invoked via `UniswapV4Router` can be griefed)

_인정됨_

`ActionConstants.CONTRACT_BALANCE` 플래그는 라우터가 지정된 토큰 금액에 대해 자체 내부 잔액을 사용해야 함을 나타내는 데 사용됩니다. 이는 사용자가 이 플래그를 토큰 금액에 활용하는 명령 이전에 필요한 자산을 라우터로 전송했거나, 라우터가 시퀀스의 이전 명령에서 사용자를 대신하여 토큰 금액을 받았음을 의미합니다. 이는 복잡한 명령 시퀀스에 유연성을 제공하지만, 외부 행위자가 사용자 트랜잭션 전에 라우터에 토큰을 기부하여 후속 명령에 대해 라우터가 사용하는 토큰 금액을 조작함으로써 특정 명령 시퀀스를 괴롭힐 수 있습니다.

다음은 이것이 발생할 수 있는 세 가지 예입니다.

1. 사용자가 YBT를 YBT + PT 금액으로 분할한 다음 이 금액을 Toki 풀에 초기 유동성으로 추가하려고 합니다. 사용자는 `PERMIT2_PERMIT_BATCH` + `TP_SPLIT_INITIAL_LIQUIDITY` + `TP_ADD_LIQUIDITY`의 명령 시퀀스를 준비합니다. `PERMIT2_PERMIT_BATCH` 명령은 라우터가 후속 `TP_SPLIT_INITIAL_LIQUIDITY` 명령에 대해 사용자가 지정한 `amount0`의 YBT를 처리하도록 허용합니다. 그러면 `TP_SPLIT_INITIAL_LIQUIDITY` 명령은 제공된 YBT의 일부를 사용하여 라우터에 PT와 YT를 발행합니다. PT는 라우터에 남고 YT는 사용자에게 전송됩니다. 남은 YBT도 라우터에 남습니다. 남은 YBT 및 수신된 PT는 사용자가 지정한 `desiredImpliedRate`와 동일한 초기 `lnImpliedRate`를 산출하는 풀 비율에 해당합니다.

```solidity
    /// @notice Split part of initial liquidity into PTs and spending them issuing PTs and YTs
    /// @dev This function means to be used with `TP_ADD_LIQUIDITY`
    /// @param receiver YT receiver address. PTs are issued to router contract
    /// @param desiredImpliedRate The desired implied rate in wad (Note e.g. 0.185e18 for 18.5%)
    function _splitInitialLiquidity(PoolKey memory key, uint256 amount0, address receiver, uint256 desiredImpliedRate)
        internal
    {
...
        // Transfer underlying token to the contract
        amount0 = payIfNeeded(Currency.unwrap(key.currency0), amount0);

        approveIfNeeded(Currency.unwrap(key.currency0), address(pt));

        // Issue PTs and YTs with part of underlying token liquidity
        // Remaining amount of underlying token `amount0 - amount0ToTokenize` is going to be left in the contract, then deposited to the pool later
        uint256 amount0ToTokenize = (amount0 * initialProportion) / 1e18;
        uint256 principals = pt.supply(amount0ToTokenize, address(this));

        // Send YTs to receiver
        SafeTransferLib.safeTransfer(address(pt.i_yt()), receiver, principals);
    }
```

위에서 볼 수 있듯이 `TP_SPLIT_INITIAL_LIQUIDITY` 명령은 `TP_ADD_LIQUIDITY`와 함께 사용되도록 의도되었으므로, `TP_ADD_LIQUIDITY` 명령에 대한 입력 데이터는 추가할 YBT 및 PT 금액(이전 명령에서 라우터 잔액에 이미 보유됨)으로 `CONTRACT_BALANCE` 플래그를 지정해야 합니다.

따라서 외부 행위자가 사용자 트랜잭션보다 먼저 라우터에 YBT를 기부하여 라우터가 `TP_ADD_LIQUIDITY` 명령에 활용할 YBT 금액을 조작할 수 있습니다. 이로 인해 사용자의 `TP_ADD_LIQUIDITY`는 팽창된 YBT 금액을 초기 유동성으로 추가하게 됩니다. 초기 `lnImpliedRate`는 풀의 PT 비율에 비례하여 계산되므로, 이 팽창된 YBT 금액은 축소된 PT 비율을 초래하며, 결국 사용자가 원래 지정한 `desiredImpliedRate`와 관련하여 축소된 초기 `lnImpliedRate`를 초래합니다.

2. **참고: 사용자가 토큰 전송에 PERMIT2를 사용할 가능성이 높기 때문에 이 특정 시퀀스는 실제로 발생할 가능성이 낮을 수 있습니다. 그러나 또 다른 복잡한 시퀀스로 인해 라우터가 이전 명령에서 사용자를 대신하여 PT 및 YT를 수신한 다음 `PT_COMBINE` 명령에 수신된 금액을 사용할 수 있습니다. 이 예시의 시퀀스는 이 특정 `PT_COMBINE` 명령이 어떻게 괴롭힘에 취약한지 보여주는 데 사용됩니다.**

사용자가 PT와 YT를 YBT로 결합하려고 합니다. 사용자는 `TRANSFER` + `TRANSFER` + `PT_COMBINE`의 명령 시퀀스를 준비합니다. PT와 YT는 먼저 라우터로 전송되고(또는 라우터가 다른 복잡한 시퀀스의 일부로 사용자를 대신하여 이 금액을 받을 수 있음), `PT_COMBINE` 명령은 결합할 PT 및 YT의 `principals` 금액으로 `CONTRACT_BALANCE` 플래그를 지정합니다(라우터는 자체 잔액을 사용).

```solidity
    function _principalTokenCombine(PrincipalToken principalToken, uint256 principals, address receiver) internal {
        ContractValidation.checkPrincipalToken(_i_factory, address(principalToken));

        address yt = address(principalToken.i_yt());
        uint256 actualPrincipals = payIfNeeded(address(principalToken), principals);
        uint256 actualYts = payIfNeeded(yt, principals);

        if (actualYts < actualPrincipals) Errors.Zap_InsufficientYieldTokenBalance.selector.revertWith();
        principalToken.combine(actualPrincipals, receiver);
    }
```

위에서 볼 수 있듯이 이 경우 라우터의 YT 금액은 PT 금액보다 `>=`이어야 하며, 그렇지 않으면 트랜잭션이 되돌려집니다.

이 확인을 통해 외부 행위자는 사용자 트랜잭션보다 먼저 라우터 계약에 최소 `1 wei`의 PT를 전송하여 사용자 트랜잭션을 괴롭힐 수 있습니다. 이로 인해 `actualPrincipals`가 라우터 잔액에 보유된 `actualYTs`보다 커지게 되어 사용자 트랜잭션이 되돌려집니다.

3. **참고: 이 시나리오는 발생할 가능성이 가장 낮지만 잠재적으로 사용자 자산 손실로 이어질 수 있습니다.**

사용자가 PT와 YT를 YBT로 결합하려고 합니다. 라우터에 이미 `1`의 PT 잔액이 있다고 가정해 보겠습니다. 사용자는 예제 `2`에서 본 동일한 명령 시퀀스를 준비하여 이를 활용하려고 시도합니다. 이번에는 사용자가 `9` PT와 `10` YT를 계약으로 전송하여 `PT_COMBINE` 명령 중 라우터의 총 잔액이 `10` PT와 `10` YT가 될 것으로 예상합니다.

그러나 외부 행위자가 사용자 트랜잭션을 프론트런닝하고 `SWEEP` 명령을 사용하여 사용자 트랜잭션이 실행되기 전에 라우터에서 `1` PT를 추출합니다. 결과적으로 `actualYts`(`10`과 동일)는 `actualPrincipals`(`9`와 동일)보다 큽니다. `_principalTokenCombine`은 `actualPrincipals`와 `actualYTs` 금액이 같을 필요가 없으며 `PrincipalToken::combine` 호출에 `actualPrincipals` 금액을 사용한다는 점에 유의하십시오. 이는 사용자의 PT 및 YT 중 `9`만 결합에 사용됨을 의미합니다. 그 후 외부 행위자는 다른 `SWEEP` 명령으로 사용자 트랜잭션을 백런닝하여 사용자의 남은 `1` YT를 추출할 수 있습니다.

**권장 사항**
이러한 엣지 케이스를 문서화하고, 외부 행위자가 후속 명령에 대한 토큰 금액을 잠재적으로 조작하는 것을 방지하기 위해 특정 명령 시퀀스의 맨 위 또는 맨 아래에 `SWEEP` 명령을 사용할 수 있음을 명시하는 것을 고려하십시오.

# [L-15] `TokiHook.getTotalBalances()`에서 읽기 전용 재진입 보호 누락 (Missing read-only reentrancy protection in `TokiHook.getTotalBalances()`)

_해결됨_

`TokiHook.getTotalBalances()`는 `TokiQuoter`, `StateView`, `TWAPPrice`, `TokiPoolRouter` 및 `TokiSwapBinSearch`에서 널리 사용되지만, 훅렛(hooklet) 전송 콜백 또는 금고 콜백을 통해 `addLiquidity()` 또는 `removeLiquidity()` 중에 호출될 때 일관성 없는 상태를 반환하여 잠재적으로 잘못된 상태 뷰 또는 잘못된 가격 계산을 초래할 수 있습니다.

```solidity
function getTotalBalances(PoolId id) external view returns (Uint128x2) {
    PoolStorage storage $ = s_hookStorage.s_states[id];
    address pointer = $.immutableParamsPointer;

    ContractValidation.checkTokiPoolExists(pointer);

    (address vault0, address vault1) = ImmutableParamsLib.getVaults(pointer);
    return LibRehypothecation.getTotalBalances({
        vault0: ERC4626(vault0),
        vault1: ERC4626(vault1),
        reserves: $.reserves,
        rawBalances: $.rawBalances
    });
}
```

**권장 사항**

`getTotalBalances()`에 읽기 전용 재진입 보호를 추가하거나 잠재적인 취약점을 명시적으로 문서화하는 것을 고려하십시오.

또한 유동성 작업을 재구성하여 상태 변경이 완료된 후 LP 토큰 발행/소각을 수행하도록 하십시오. 이 작업은 훅렛 전송 콜백을 트리거할 수 있기 때문입니다.

# [L-16] 견적 함수에서 `PrincipalToken` 만료 및 일시 중지 확인 누락 (`PrincipalToken` expiry and pause checks missing in quote functions)

_해결됨_

`TokiQuoter`의 여러 견적 함수는 예상 출력을 계산하기 위해 `PrincipalToken.previewSupply()` 및 `PrincipalToken.previewIssue()`에 의존합니다.

그러나 이 미리보기 함수들은 PrincipalToken이 **일시 중지되거나 만료된** 경우 `0`을 반환하여, 견적 함수가 오해의 소지가 있는 결과를 반환하거나 암시적으로 되돌아가게 합니다.

또한 만료되면 PT를 유동성 추가 또는 스왑에 사용할 수 없으므로 유동성 추가 관련 함수에 대해 PrincipalToken이 만료된 후 명시적인 확인이 없습니다.

**권장 사항**

견적 함수에 일시 중지 또는 만료된 상태에 대한 명시적 확인을 추가하고 작업을 사용할 수 없는 경우 설명적인 오류 메시지와 함께 되돌리십시오.

# [L-17] `quoteCreateAndAddLiquidity()`의 `salt` 비효율성으로 인한 주소 불일치 (`salt` inefficiency in `quoteCreateAndAddLiquidity()` mismatches address)

_해결됨_

`TokiQuoter.quoteCreateAndAddLiquidity()`는 적절한 Uniswap V4 풀 생성을 위해 PT 주소가 `currency0` 주소보다 큰지 확인하기 위해 `salt`를 채굴합니다. 그러나 선택된 salt는 결국 팩토리에서 다시 해시될 것이므로 비효율적입니다.

결과적으로 팩토리는 쿼터가 예측한 것과 다른 `salt`를 사용하여 잘못된 주소 예측으로 이어지고 실행이 되돌려질 수 있습니다.

```solidity
function quoteCreateAndAddLiquidity(
    ...
    address currency0
) external returns (PreviewAddLiquidityResult memory) {
    uint256 salt;
    Factory factory = principalTokenQuoter().factory();
    while (true) {
        assembly {
            let m := mload(0x40)
            mstore(m, chainid())
            mstore(add(m, 0x20), address())
            mstore(add(m, 0x40), salt)
@>          salt := keccak256(m, 0x60)
        }

@>       address predictedAddress =
            LibBlueprint.computeCreate2Address(bytes32(salt), suite.ptBlueprint, address(factory));
        if (predictedAddress > currency0) break;
        unchecked {
            salt++;
        }
    }
    
@>   (,, address pool) = factory.deployDeterministic(suite, modules, expiry, msg.sender, bytes32(salt));
    --- SNIPPED ---
}
```

그러나 `Factory._deploy()`는 사용하기 전에 `salt`를 다시 해시합니다.

```solidity
function _deploy(...)
    internal
    returns (address pt, address yt, address pool)
{
    --- SNIPPED ---
    // Switch salt calculation for true deterministic deployment
    // If the salt is not provided, use the default way.
    if (salt == bytes32(0)) {
        --- SNIPPED ---
    } else {
        // Re-hash the user-provided salt to avoid using the same salt of the above salt.
@>      salt = EfficientHashLib.hash(block.chainid, uint256(uint160(msg.sender)), uint256(salt));
    }
    --- SNIPPED ---
}
```

이것은 다음과 같은 salt 시퀀스를 생성합니다:
1. 쿼터에서: `predictedAddress`에 대한 `salt_A: hash(chainid, address(this), 0)` 및 `predictedAddress > currency0` 확인.
2. `salt_A`가 유효하면 `salt_A`를 반환하고, 그렇지 않으면 `salt_A++`하고 1단계를 반복.
3. 팩토리에서: `pt` 배포를 위한 `salt_B: hash(chainid, uint256(uint160(msg.sender)), uint256(salt_A))`.
4. 실제 PT 주소는 `salt_B`를 사용하여 계산되며, 이는 `salt_A`와 다릅니다.
5. 결과적으로 **실제 PT 주소가 `currency0`보다 크지 않으면 `salt`를 미리 예측했더라도 실행이 되돌려집니다**.

**권장 사항**

`TokiQuoter.quoteCreateAndAddLiquidity()`를 업데이트하여 쿼터에서 PT 주소를 예측하는 데 사용되는 원래 `salt`(해시 전)를 사용하십시오.

또한 쿼터에서 `salt`를 `bytes32(0)`으로 사용하거나 시작하지 마십시오. 이렇게 하면 팩토리에서도 다른 salt 계산을 사용하여 잘못된 예측으로 이어질 수 있습니다.

