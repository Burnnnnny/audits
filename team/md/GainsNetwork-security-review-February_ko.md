# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**gTrade-contracts** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Gains Network 소개

Gains Network는 유동성 효율적인 탈중앙화 레버리지 거래 플랫폼입니다. 거래 쌍에 관계없이 DAI, USDC 또는 WETH 담보로 거래가 시작됩니다. 레버리지는 합성(synthetic)이며 각각의 gToken 저장소(vault)와 GNS 토큰에 의해 지원됩니다. 트레이더의 수익은 저장소에서 가져와 트레이더의 PnL(양수인 경우)을 지불하거나, PnL이 음수인 경우 거래에서 발생한 트레이더의 손실을 받습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간 - 프로토콜 자산의 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터일 뿐이지만 여전히 발생 가능성이 상대적으로 높습니다.

- 낮음 - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 또는 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정해야 함 (Should fix)

- 낮음 - 수정할 수 있음 (Could fix)

# 보안 평가 요약

_검토 커밋 해시_ - [725b997cfc632361a0e01b3d996f63bc09783e3c](https://github.com/GainsNetwork-org/gTrade-contracts/tree/725b997cfc632361a0e01b3d996f63bc09783e3c)

_수정 검토 커밋 해시_ - [7de396938464d6d5e9e3d92ffa717d574e73be33](https://github.com/GainsNetwork-org/gTrade-contracts/tree/7de396938464d6d5e9e3d92ffa717d574e73be33)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

```
- GNSAddressStore
- GNSDiamondCut
- GNSDiamondLoupe
- GNSDiamondStorage
- GNSBorrowingFees
- GNSFeeTiers
- GNSPairsStorage
- GNSPriceAggregator
- GNSPriceImpact
- GNSReferrals
- GNSTrading
- GNSTradingCallbacks
- GNSTradingStorage
- GNSTriggerRewards
- GNSMultiCollatDiamond
- AddressStoreUtils
- BorrowingFeesUtils
- ChainUtils
- ChainlinkClientUtils
- CollateralUtils
- DiamondUtils
- FeeTiersUtils
- PackingUtils
- PairsStorageUtils
- PriceAggregatorUtils
- PriceImpactUtils
- ReferralsUtils
- StorageUtils
- TradingCallbacksUtils
- TradingStorageUtils
- TradingUtils
- TriggerRewardsUtils
```

# 발견 사항

# [C-01] 18 소수점이 아닌 담보에 대해 청산이 방지됨

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`getTradeLiquidationPrice`는 `triggerOrder`가 청산 주문이나 대기 주문을 처리할 때 호출됩니다. `liqPrice`를 계산하고 손절매(stop-loss) 가격에 도달하면 주문을 `SL_CLOSE`로 업데이트해야 하는지 확인합니다. `getTradeLiquidationPrice`는 차입 수수료와 청산 가격을 계산하기 위해 제공된 담보 금액이 담보 정밀도(collateral precision)여야 합니다.

```solidity
  /**
   * @dev Pure function that returns the liquidation price for a trade (1e10 precision)
   * @param _openPrice trade open price (1e10 precision)
   * @param _long true if long, false if short
   * @param _collateral trade collateral (collateral precision)
   * @param _leverage trade leverage (1e3 precision)
   * @param _borrowingFeeCollateral borrowing fee amount (collateral precision)
   * @param _collateralPrecisionDelta collateral precision delta (10^18/10^decimals)
   */
  function _getTradeLiquidationPrice(
      uint256 _openPrice,
      bool _long,
      uint256 _collateral,
      uint256 _leverage,
      uint256 _borrowingFeeCollateral,
      uint128 _collateralPrecisionDelta
  ) internal pure returns (uint256) {
      uint256 precisionDeltaUint = uint256(_collateralPrecisionDelta);

      int256 openPriceInt = int256(_openPrice);
      // LIQ_THRESHOLD_P = 90; // -90% pnl
      int256 collateralLiqNegativePnlInt = int256((_collateral * LIQ_THRESHOLD_P * precisionDeltaUint * 1e3) / 100); // 1e18 * 1e3
      int256 borrowingFeeInt = int256(_borrowingFeeCollateral * precisionDeltaUint * 1e3); // 1e18 * 1e3

      // PRECISION
      int256 liqPriceDistance = (openPriceInt * (collateralLiqNegativePnlInt - borrowingFeeInt)) / // 1e10 * 1e18 * 1e3
          int256(_collateral) /
          int256(_leverage) /
          int256(precisionDeltaUint); // 1e10

      int256 liqPrice = _long ? openPriceInt - liqPriceDistance : openPriceInt + liqPriceDistance; // 1e10

      return liqPrice > 0 ? uint256(liqPrice) : 0; // 1e10
  }
```

그러나 `triggerOrder`에서 `getTradeLiquidationPrice`를 호출할 때 제공되는 담보는 담보 정밀도가 아닙니다.

```solidity
  function triggerOrder(uint256 _packed) internal onlyTradingActivated(true) {
      (uint256 _orderType, address trader, uint32 index) = _packed.unpackTriggerOrder();

      // ...

      if (orderType == ITradingStorage.PendingOrderType.LIQ_CLOSE) {
          if (t.sl > 0) {
              uint256 liqPrice = _getMultiCollatDiamond().getTradeLiquidationPrice(
                  IBorrowingFees.LiqPriceInput(
                      t.collateralIndex,
                      t.user,
                      t.pairIndex,
                      t.index,
                      t.openPrice,
                      t.long,
>>>                   t.collateralAmount / collateralPrecisionDelta / PRECISION,
                      t.leverage
                  )
              );

              // If liq price not closer than SL, turn order into a SL order
              if ((t.long && liqPrice <= t.sl) || (!t.long && liqPrice >= t.sl)) {
                  orderType = ITradingStorage.PendingOrderType.SL_CLOSE;
              }
          }
      } else {
          if (orderType == ITradingStorage.PendingOrderType.SL_CLOSE && t.sl == 0) revert ITradingUtils.NoSl();
          if (orderType == ITradingStorage.PendingOrderType.TP_CLOSE && t.tp == 0) revert ITradingUtils.NoTp();
      }

      // ...
  }
```

담보가 18 소수점 토큰이 아닌 경우, `collateralAmount` / `precisionDelta` / `PRECISION`이 대부분의 경우 0이 되어 `triggerOrder`가 되돌려지는(revert) 시나리오가 발생할 수 있습니다. 이는 0으로 나누기를 시도하기 때문에 `getTradeLiquidationPrice`가 되돌려지게 합니다.

USDC의 경우:

decimals = 6
precisionDelta = 1e12
PRECISION = 1e10

따라서 담보가 최소 1이 되려면 `collateralAmount = precisionDelta * PRECISION` = 1e10 x 1e12 = 1e22 또는 100,000,000,000,000,000 USDC여야 합니다. USDC를 사용하고 손절매가 설정되어 있으며 해당 값보다 적은 담보를 가진 경우 청산할 수 없습니다.

결론적으로, USDC, WBTC 또는 기타 18 소수점이 아닌 자산을 담보로 사용하고 손절매를 설정하는 거래는 거의 모든 시나리오에서 청산할 수 없습니다.

## 권장 사항

`getTradeLiquidationPrice`에 `t.collateralAmount / collateralPrecisionDelta / PRECISION` 대신 `t.collateralAmount`를 제공하십시오.

# [C-02] `updateTp()` 및 `updateSl()`을 악용하여 무위험 거래 획득 가능

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`executeTriggerOpenOrderCallback()` 내에서 오픈 주문이 실행될 때, 잘못된 주문을 취소하기 위해 `_openTradePrep()`에서 유효성 검사가 수행됩니다. 여기에는 TP(익절) 또는 SL(손절)이 충족되었는지 확인하는 것이 포함됩니다.

그러나 `TP_CLOSE` 또는 `SL_CLOSE` 대기 주문이 없는 한 트레이더는 TP/SL을 업데이트할 수 있습니다.

즉, 트레이더는 오픈 가격에 도달했더라도 `updateTp()` 또는 `updateSl()`을 악용하여 대기 주문의 실행을 차단할 수 있습니다. 트레이더는 가격이 유리해질 때까지 이를 차단할 수 있으며, 이는 실행이 차단되는 한 트레이더가 수수료를 지불할 필요가 없음을 의미합니다.

가격이 트레이더에게 유리해지면 트레이더는 오픈 주문 실행을 차단 해제하기 위해 TP/SL을 다시 업데이트할 수 있으며, 그러면 지정가 거래가 생성되었을 때의 가격으로 실행됩니다. 그런 다음 트레이더는 즉시 거래를 마감하여 이익을 얻을 수 있습니다.

다음 시나리오를 가정해 봅시다.

1. 블록 `t1`에서 트레이더가 가격 X로 LIMIT(지정가) 주문을 열기 위해 `openTrade()`를 호출합니다. 이것은 `storeTrade()`를 트리거 하여 `createdBlock = t1`이 됩니다.
2. 트레이더는 동시에 `updateTP()`를 호출하여 `executeTriggerOpenOrderCallback()`에서 되돌려지게 하는 TP를 설정하여 모든 주문 실행을 차단합니다. 이는 오픈 가격에 도달했을 때도 거래가 등록되는 것을 방지합니다.
3. 블록 `t2`에서 가격이 유리할 때 트레이더는 더 이상 실행을 차단하지 않도록 `updateTP()`를 다시 호출합니다.
4. 이제 트레이더는 새로운 오라클 요청으로 거래 콜백을 트리거 하기 위해 `triggerOrder()`를 다시 호출할 수 있습니다.
5. LIMIT 주문은 지정가로 실행되고 열리며, `triggerOrder()`가 `createdBlock = t1`을 기반으로 룩백(lookback) 가격을 요청하므로 충족됩니다.
6. 거래가 등록되면 트레이더는 즉시 거래를 마감하여 이익을 확정할 수 있습니다.

### 개념 증명 (POC)

`executeTriggerOpenOrderCallback.test.js`의 L311에 다음 테스트를 추가하십시오.

```javascript

  describe('POC for C-01', () => {
    it('[C-01] `UpdateTp()` and `UpdateSL()` can be abused to obtain risk-free trade', async () => {
      limitId = [accounts[0], 0];
      tradeId = [accounts[0], 3];

      console.log("-------------------------");

      let currentBlock = await d.block.getBlockNumber();
      console.log("currentBlock       : %d\n",currentBlock);


      requestTx = await d.diamond.triggerOrder(packNft(ENUMS.PENDING_ORDER_TYPE.LIMIT_OPEN, limitId[0], limitId[1]), { from: gov });
      orderId = getOrderId(requestTx);

      console.log("calling updateTp() to block order execution for fulfill(), by setting a TP that will cancel order due to TP_REACHED");
       await d.diamond.updateTp(0, 1 * 1e10);

      let td = await d.diamond.getTrade(accounts[0], 0);
      let tdInfo = await d.diamond.getTradeInfo(accounts[0], 0);
      console.log("trade.tp           : %d ", td.tp);
      console.log("tpLastUpdatedBlock : %d ",tdInfo.tpLastUpdatedBlock);
      console.log("createdBlock       : %d (used by getPrice() to retrieve historical price)",tdInfo.createdBlock);

      const priceData1 = pack([1499 * 1e10, 1505 * 1e10, 1495 * 1e10, dummyTsSec].map(BigInt), [64, 64, 64, 64].map(BigInt));
      await fulfillOracleRequests(requestTx, d.oracles.slice(0, 3), priceData1);

      td = await d.diamond.getTrade(accounts[0], 3);
      console.log("\n");
      console.log("trade is not registered due to cancellation.");
      console.log("trade.isOpen           : %s ", td.isOpen);

      console.log("-------------------------");

      await d.block.increase(10);
      currentBlock = await d.block.getBlockNumber();
      console.log("currentBlock       : %d\n",currentBlock);


      console.log("Once price is favorable, call updateTp() to unblock the cancellation.");
      await d.diamond.updateTp(0, 2175 * 1e10);

      td = await d.diamond.getTrade(accounts[0], 0);
      tdInfo = await d.diamond.getTradeInfo(accounts[0], 0);
      console.log("trade.tp           : %d ", td.tp);
      console.log("tpLastUpdatedBlock : %d ",tdInfo.tpLastUpdatedBlock);
      console.log("createdBlock       : %d (used by getPrice() to retrieve historical price)",tdInfo.createdBlock);

      console.log("call triggerOrder() to request oracle to fulfill order, with price from original createdBlock.");
      requestTx = await d.diamond.triggerOrder(packNft(ENUMS.PENDING_ORDER_TYPE.LIMIT_OPEN, limitId[0], limitId[1]), { from: gov });
      orderId = getOrderId(requestTx);

      // ignore price data, as this POC just shows that it is possible to delay order execution till price is favorable
      const priceData2 = pack([1499 * 1e10, 1505 * 1e10, 1495 * 1e10, dummyTsSec].map(BigInt), [64, 64, 64, 64].map(BigInt));
      await fulfillOracleRequests(requestTx, d.oracles.slice(0, 3), priceData2);

      td = await d.diamond.getTrade(accounts[0], 3);
      console.log("\n");
      console.log("trade is now registered as cancellation is unblocked by trader.");
      console.log("trade.isOpen           : %s ", td.isOpen);

      console.log("\n");
      console.log("now that price is favorable, we can close this registered trade immediately for a profit.");


    });
```

## 권장 사항

일반적으로 사용자가 대기 중인 오픈 주문의 취소에 영향을 미치지 못하도록 하십시오.

이 문제를 해결하려면 `updateTp()` 및 `updateSl()`을 수정하여 등록된 거래에만 사용할 수 있고 오픈 지정가 주문(open limit orders)에는 사용할 수 없도록 하십시오.

사용자는 여전히 `updateOpenOrder()`를 대신 사용하여 오픈 지정가 주문에 대한 TP/SL을 업데이트할 수 있습니다. 그렇게 하면 `createdBlock`이 재설정되고 TP/SL이 검증됩니다.

# [H-01] 프록시 업그레이드 후 역할(roles) 손실

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

현재 `GNSMultiCollatDiamond` 계약 구현에서 `accessControl` 매핑은 프록시 스토리지의 슬롯 `2`를 차지합니다:
[링크](https://arbiscan.io/address/0x1b7aeaeab4ffd6b3070ae601a038146b80c6ea8a#code#F20#L20)

처음 두 슬롯은 `Initializable` 계약의 변수와 현재 하나의 주소(`gns`)만 보유하는 `Addresses` 구조체가 차지합니다.

그러나 `GNSMultiCollatDiamond` 계약의 새 버전에서는 `Addresses` 구조체가 2개의 스토리지 슬롯을 더 차지합니다:

```solidity
File: IAddressStore.sol
15:     struct Addresses {
16:         address gns; // GNS token address
17:         address gnsStaking; // GNS staking address
18:         address linkErc677; // ERC677 LINK token address
19:     }
20:
21:     struct AddressStore {
22:         Addresses globalAddresses;
23:         mapping(address => mapping(Role => bool)) accessControl;
24:         uint256[47] __gap;
25:     }
```

이로 인해 `accessControl` 매핑은 슬롯 `4`에 위치하게 됩니다.
Solidity에서 매핑 값은 키와 매핑이 차지하는 스토리지 슬롯 번호를 기반으로 주소가 지정됩니다. 즉, 현재 버전의 `GNSMultiCollatDiamond`에서 사용되는 주소 지정 방식이 새 버전에서 변경된다는 의미입니다. 이로 인해 `hasRole`은 이제 `2` 대신 스토리지 슬롯 `4`를 사용하여 `accessControl` 매핑을 주소 지정하므로 모든 현재 역할 정보가 손실됩니다.

자금은 위험에 처하지 않지만 모든 역할 기반 기능에 액세스 할 수 없게 되며 스토리지 레이아웃을 수정하는 프록시의 새로운 업그레이드가 필요합니다.

다음 foundry 테스트는 설명된 문제를 보여줄 수 있습니다:

```solidity
pragma solidity 0.8.23;

import "lib/forge-std/src/Test.sol";
import "contracts/core/GNSMultiCollatDiamond.sol";
import "node_modules/@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";

contract Unit is Test {
    function setUp() public {}

    function test_UpgradeBrokeRoles() public {
        uint256 mainnetFork = vm.createFork("https://arb1.arbitrum.io/rpc");
        vm.selectFork(mainnetFork);

        // Fetch current proxy address
        ITransparentUpgradeableProxy proxyDiamond;
        proxyDiamond = ITransparentUpgradeableProxy(
            payable(0xFF162c694eAA571f685030649814282eA457f169)
            );

        // Check that address has manager role before upgrade
        bool _hasRole = GNSAddressStore(payable(address(proxyDiamond)))
            .hasRole(0x1632C38cB208df8409753729dBfbA5c58626F637, IAddressStore.Role.ROLES_MANAGER);

        GNSMultiCollatDiamond newDiamondImplementation;
        newDiamondImplementation = new GNSMultiCollatDiamond();
        // Upgrading diamond with new implementation
        vm.prank(0xe18be0113c38c91b3B429d04fDeb84359fBCb2eB);
        proxyDiamond.upgradeTo(address(newDiamondImplementation));

        // Check that address has manager role after upgrade
        bool hasRole_ = GNSAddressStore(payable(address(proxyDiamond)))
            .hasRole(0x1632C38cB208df8409753729dBfbA5c58626F637, IAddressStore.Role.ROLES_MANAGER);

        assertTrue(_hasRole == hasRole_, "Address should not lose role after upgrade");
    }
}
```

## 권장 사항

현재 변수의 위치를 변경하지 않는 방식으로 스토리지를 업데이트하는 것을 고려하십시오. 예를 들어, `gnsStaking` 및 `linkErc677`은 `accessControl` 매핑 뒤에 추가될 수 있습니다.

# [H-02] `ReferralsUtils.updateStartReferrerFeeP()`가 잘못된 확인 구현

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`updateStartReferrerFeeP` 함수의 L64에서 비교 부호가 잘못하여 `<=`로 설정되어 있지만 `>`여야 합니다. 이로 인해 `startReferrerFeeP`를 올바른 범위의 값으로 설정할 수 없습니다:

```solidity
File: ReferralsUtils.sol
63:     function updateStartReferrerFeeP(uint256 _value) internal {
64:         if (_value <= MAX_START_REFERRER_FEE_P) revert IGeneralErrors.AboveMax();
65:
66:         _getStorage().startReferrerFeeP = _value;
67:
68:         emit IReferralsUtils.UpdatedStartReferrerFeeP(_value);
69:     }
```

## 권장 사항

비교의 부호를 `>`로 업데이트하는 것을 고려하십시오.

# [H-03] `ReferralsUtils.updateReferralsOpenFeeP()`가 잘못된 확인 구현

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`updateReferralsOpenFeeP` 함수의 L75에서 비교 부호가 잘못하여 `<=`로 설정되어 있지만 `>`여야 합니다. 이로 인해 `openFeeP`를 올바른 범위의 값으로 설정할 수 없습니다:

```solidity
File: ReferralsUtils.sol
74:     function updateReferralsOpenFeeP(uint256 _value) internal {
75:         if (_value <= MAX_OPEN_FEE_P) revert IGeneralErrors.AboveMax();
76:
77:         _getStorage().openFeeP = _value;
78:
79:         emit IReferralsUtils.UpdatedOpenFeeP(_value);
80:     }
```

## 권장 사항

비교의 부호를 `>`로 업데이트하는 것을 고려하십시오.

# [H-04] GToken 주소 업데이트 후 담보가 승인되지 않음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

프로토콜에 새로운 담보 토큰을 추가하는 동안 해당 `_gToken` 계약 주소 및 GNS `staking` 주소에 대해 승인됩니다:

```solidity
File: TradingStorageUtils.sol
54:     function addCollateral(address _collateral, address _gToken) internal {
...
81:         // Setup Staking and GToken approvals
82:         IERC20 collateral = IERC20(_collateral);
83:         collateral.approve(_gToken, type(uint256).max);
84:         collateral.approve(staking, type(uint256).max);
85:
86:         emit ITradingStorageUtils.CollateralAdded(_collateral, index, _gToken);
87:     }
```

나중에 `TradingStorageUtils#updateGToken` 함수를 사용하여 `_gToken` 주소를 업데이트할 수 있습니다:

```solidity
File: TradingStorageUtils.sol
109:     function updateGToken(address _collateral, address _gToken) internal {
110:         ITradingStorage.TradingStorage storage s = _getStorage();
111:
112:         uint8 index = s.collateralIndex[_collateral];
113:
114:         if (index == 0) {
115:             revert IGeneralErrors.DoesntExist();
116:         }
117:
118:         if (_gToken == address(0)) {
119:             revert IGeneralErrors.ZeroAddress();
120:         }
121:
122:         s.gTokens[index] = _gToken;
123:
124:         emit ITradingStorageUtils.GTokenUpdated(_collateral, index, _gToken);
125:     }
```

그러나 이 함수는 담보 토큰에 대한 승인 호출을 놓쳐서 새로운 GToken 계약이 `GNSMultiCollatDiamond` 주소에서 담보를 전송할 수 없게 됩니다.

## 권장 사항

`TradingStorageUtils.updateGToken()` 함수에 담보 계약에 대한 `approve` 호출을 추가하는 것을 고려하십시오.

# [H-05] `closeTradeMarket`이 `getPrice`를 트리거 할 때 잘못된 값을 제공

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`getPrice`가 트리거 되면 처리된 주문 유형 및 데이터를 기반으로 정보를 제공하여 Chainlink 요청을 구성합니다. 그런 다음 제공된 `positionSizeCollateral`을 사용하여 `linkFeePerNode`를 계산하고 최종적으로 링크 요청 및 `linkFeePerNode`를 포함하여 오라클에 요청을 보냅니다. `linkFeePerNode`를 계산할 때 `getLinkFee` 함수를 사용하며, 담보 정밀도의 `_positionSizeCollateral`을 기대합니다.

```solidity
    function getLinkFee(
        uint8 _collateralIndex,
        uint16 _pairIndex,
        uint256 _positionSizeCollateral // collateral precision
    ) internal view returns (uint256) {
        (, int256 linkPriceUsd, , , ) = _getStorage().linkUsdPriceFeed.latestRoundData();
        // NOTE: all [token / USD] feeds are 8 decimals
        return
            (getUsdNormalizedValue(
                _collateralIndex,
                _getMultiCollatDiamond().pairOracleFeeP(_pairIndex) * _positionSizeCollateral
            ) * 1e8) /
            uint256(linkPriceUsd) /
            PRECISION /
            100;
    }
```

그러나 `closeTradeMarket`에서 `getPrice`가 트리거 되면 레버리지를 곱하고 정밀도 델타와 `PRECISION`으로 나눈 `collateralAmount`를 제공합니다.

```solidity
    function closeTradeMarket(uint32 _index) internal onlyTradingActivated(true) {
        address sender = _msgSender();

        ITradingStorage.Trade memory t = _getMultiCollatDiamond().getTrade(sender, _index);
        if (
            _getMultiCollatDiamond().getTradePendingOrderBlock(
                ITradingStorage.Id({user: t.user, index: t.index}),
                ITradingStorage.PendingOrderType.MARKET_CLOSE
            ) > 0
        ) revert ITradingUtils.AlreadyBeingMarketClosed();

        if (!t.isOpen) revert ITradingUtils.NoTrade();

        if (t.tradeType != ITradingStorage.TradeType.TRADE) revert ITradingUtils.WrongTradeType();

        ITradingStorage.Collateral memory collateralConfig = _getMultiCollatDiamond().getCollateral(t.collateralIndex);

        ITradingStorage.PendingOrder memory pendingOrder;
        pendingOrder.trade.user = t.user;
        pendingOrder.trade.index = t.index;
        pendingOrder.trade.pairIndex = t.pairIndex;
        pendingOrder.user = sender;
        pendingOrder.orderType = ITradingStorage.PendingOrderType.MARKET_CLOSE;

        pendingOrder = _getMultiCollatDiamond().storePendingOrder(pendingOrder);
        ITradingStorage.Id memory orderId = ITradingStorage.Id({user: pendingOrder.user, index: pendingOrder.index});

        _getMultiCollatDiamond().getPrice(
            t.collateralIndex,
            t.pairIndex,
            orderId,
            pendingOrder.orderType,
>>>         (t.collateralAmount * t.leverage) / 1e3 / collateralConfig.precisionDelta / PRECISION,
            ChainUtils.getBlockNumber()
        );

        emit ITradingUtils.MarketOrderInitiated(orderId, sender, t.pairIndex, false);
    }
```

이로 인해 오라클에 보내야 하는 수수료를 계산할 때 잘못된 값이 발생합니다.

## 권장 사항

필요한 담보 정밀도를 유지하기 위해 대신 `(_trade.collateralAmount * _trade.leverage) / 1e3`을 제공하십시오.

# [H-06] `toggleCollateralActiveState()`의 잘못된 검증

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

담보의 활성 상태는 `toggleCollateralActiveState()`를 사용하여 토글(toggle) 할 수 있으며, 이는 지정된 담보 인덱스에 대해 `isActive`를 설정합니다. 이를 통해 거버넌스는 필요할 때 지정된 담보를 비활성화하고 해당 담보를 사용하여 새로운 거래가 시작되는 것을 방지할 수 있습니다.

그러나 `toggleCollateralActiveState()`에는 유효성 검사 확인에 오류가 있습니다. `collateral.precision > 0`일 때 되돌려지는데(revert), 추가될 때 정밀도가 설정되므로 모든 기존 담보에 대해 발생합니다.

이로 인해 스테이블 코인 담보의 디페깅(depegging)과 같은 비상 상황에서 필요할 때 거버넌스가 거래를 위한 담보를 비활성화하는 것을 방지할 수 있습니다.

```Solidity
    function toggleCollateralActiveState(uint8 _collateralIndex) internal {
        ITradingStorage.TradingStorage storage s = _getStorage();
        ITradingStorage.Collateral storage collateral = s.collaterals[_collateralIndex];

        //@audit this will revert as precision is > 0 for existing collaterals
        if (collateral.precision > 0) {
            revert IGeneralErrors.DoesntExist();
        }

        bool toggled = !collateral.isActive;
        collateral.isActive = toggled;

        emit ITradingStorageUtils.CollateralUpdated(_collateralIndex, toggled);
    }
```

## 권장 사항

확인을 `collateral.precision == 0`으로 변경하십시오.

# [H-07] 취소된 `MARKET_CLOSE` 주문이 TP/SL 트리거에 영향을 미침

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

거래에서 TP/SL이 설정되면 `tpLastUpdatedBlock` / `slLastUpdatedBlock`이 현재 블록 번호로 설정됩니다. 이는 콜백 처리가 TP/SL 마지막 업데이트 블록 번호를 기반으로 TP_CLOSE / SL_CLOSE 주문을 처리하므로 TP 및 SL의 트리거가 보장되도록 하기 위함입니다.

그러나 취소된 `MARKET_CLOSE`는 TP/SL이 과거에 설정되었음에도 불구하고 `tpLastUpdatedBlock` 및 `slLastUpdatedBlock`을 모두 현재 블록 번호로 잘못 재설정합니다.

이는 `MARKET_CLOSE` 주문이 접수되었으나 성공적으로 실행되지 않아 주문이 취소된 경우 발생합니다. 취소 중 `collateralAmount`는 오라클 비용을 지불하기 위해 차감되므로 `updateTrade()`를 통해 `collateralAmount`를 업데이트해야 합니다. `updateTrade()` 호출은 `tpLastUpdatedBlock` 및 `slLastUpdatedBlock`을 모두 현재 블록 번호로 재설정합니다.

취소된 `MARKET_CLOSE` 주문으로 인해 잘못 재설정된 블록 번호를 기반으로 TP/SL 트리거가 발생하므로 TP/SL이 이미 구성된 경우 문제가 됩니다.

````Solidity


    function closeTradeMarketCallback(
        ITradingCallbacks.AggregatorAnswer memory _a
    ) internal onlyTradingActivated(true) {
        ...
            if (cancelReason == ITradingCallbacks.CancelReason.NONE) {
            ...
            } else {
                //@audit when MARKET_CLOSE order is cancelled, it will call updateTrade()
                t.collateralAmount -= uint120(govFee);
                _getMultiCollatDiamond().updateTrade(t, 0);
    }

    function updateTrade(ITradingStorage.Trade memory _t, uint16 _maxSlippageP) internal {
        ```
        i.createdBlock = uint32(ChainUtils.getBlockNumber());
        //@audit both TP last updated block will be reset to current block number  when MARKET_CLOSE is cancelled
        i.tpLastUpdatedBlock = i.createdBlock;
        i.slLastUpdatedBlock = i.createdBlock;
    }
````

## 권장 사항

`closeTradeMarketCallback()`의 취소 로직에서 `updateTrade()`를 `tpLastUpdatedBlock` 및 `slLastUpdatedBlock`을 재설정하지 않고 `collateralAmount`만 업데이트하는 새 함수로 대체하십시오.

# [H-08] 시장 폐장 시 DOS 공격에 대한 취약성

## 심각도

영향: 중간 (Medium) - 오라클 시스템은 시장 폐장 시 서비스 거부(DOS) 공격에 취약하여 암호화폐와 같은 다른 활성 시장의 합법적인 거래 처리를 잠재적으로 지연시킬 수 있습니다.

가능성: 높음 (High) - 전통적인 시장이 닫힐 때마다 취약점을 악용할 수 있습니다.

## 설명

트레이더가 포지션을 닫으려고 시도하고 오라클이 `_a.price == 0`을 반환하면 시장이 닫혔음을 나타내며 거래를 실행할 수 없습니다. 이 시나리오는 오라클 서비스 비용을 충당하기 위해 수수료(`govFee`)를 트리거 합니다.

        function closeTradeMarketCallback(
            ITradingCallbacks.AggregatorAnswer memory _a
        ) internal onlyTradingActivated(true) {

            ...

            ITradingCallbacks.CancelReason cancelReason = !t.isOpen
                ? ITradingCallbacks.CancelReason.NO_TRADE
                : (_a.price == 0 ? ITradingCallbacks.CancelReason.MARKET_CLOSED : ITradingCallbacks.CancelReason.NONE);
                ...

                } else { // MARKET_CLOSED
                    // Gov fee to pay for oracle cost
                    _updateTraderPoints(t.collateralIndex, t.user, 0, t.pairIndex);
                    uint256 govFee = _handleGovFees(
                        t.collateralIndex,
                        t.user,
                        t.pairIndex,
                        v.positionSizeCollateral,
                        _getMultiCollatDiamond().isTradeCollateralInStorage(
                            ITradingStorage.Id({user: t.user, index: t.index})
                        )
                    );
                    t.collateralAmount -= uint120(govFee);
                    _getMultiCollatDiamond().updateTrade(t, 0);
                    ...
                }
            }

            ...
        }

외환 및 원자재와 같은 자산 시장은 몇 시간에서 하루 종일 닫힐 수 있으며 시스템은 매우 높은 레버리지(원자재의 경우 최대 250배, 외환의 경우 1000배)를 허용하므로 최소한의 비용으로 잠재적인 DOS 공격을 위한 허점이 존재합니다.

공격 시나리오를 살펴보겠습니다:

- `openFeeP`가 3%라고 가정합니다.
- 트레이더는 최소 요구 사항을 충족하기 위해 $1.5 담보와 1000배 레버리지로 $1500 포지션 크기와 동일한 외환 거래 포지션을 엽니다.
- 거래 쌍의 시장이 닫히면 트레이더는 `closeTradeMarket` 요청을 시작합니다.
- 시장이 닫혀 있으므로 모든 오라클 노드는 0을 반환하고 `closeTradeMarketCallback`이 트리거 됩니다. 트레이더는 담보 금액의 3% 또는 $0.045($1.5\*3%=$0.045)를 청구 받습니다.
- `closeTradeMarket` 요청을 반복적으로 발행함으로써 트레이더는 시도당 최소 비용(감소된 담보 금액의 3%)으로 오라클 시스템을 마비시킬 수 있습니다.
- 공격 규모를 늘리기 위해 악의적인 트레이더는 모든 거래 쌍에 대해 포지션을 열고 시장이 닫혔을 때 동시에 모두 스팸을 보낼 수 있습니다.
- 공격은 시장이 다시 열릴 때만 끝납니다.

## 권장 사항

다음 옵션을 고려하십시오:

- 시장 폐쇄 중에 트레이더가 `closeTradeMarket`을 두 번 이상 요청하지 못하도록 제한합니다.
- 시장이 닫혔을 때 `closeTradeMarket` 요청에 대해 고정된 최소 수수료를 도입하여 공격 비용을 증가시켜 스팸 행위를 억제합니다.
- 최소 포지션 크기를 늘려 공격 비용을 효과적으로 증가시킵니다.

# [M-01] `getCollaterals()`가 마지막 담보를 반환하지 않음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`getCollaterals()` 함수는 프로토콜의 모든 기존 담보를 검색하는 데 사용됩니다. 그리고 `addCollateral()`에서 알 수 있듯이 기존 담보에 대한 `collateralIndex`는 1부터 시작하며 0은 어떤 담보에도 사용되지 않습니다.

그러나 `getCollateral()`은 `Collateral[]`에 잘못 액세스 하여 인덱스 0부터 담보를 검색합니다. 이로 인해 인덱스 0의 빈 담보를 반환하고 마지막 담보도 반환하지 못하게 됩니다.

```Solidity
    function getCollaterals() internal view returns (ITradingStorage.Collateral[] memory) {
        ITradingStorage.TradingStorage storage s = _getStorage();

        ITradingStorage.Collateral[] memory collaterals = new ITradingStorage.Collateral[](s.lastCollateralIndex);


         //@audit it should be  for (uint8 i = 1; i < s.lastCollateralIndex +1; ++i) { instead
        for (uint8 i; i < s.lastCollateralIndex; ++i) {
            collaterals[i] = s.collaterals[i];
        }

        return collaterals;
    }
```

## 권장 사항

다음에서

```Solidity
for (uint8 i; i < s.lastCollateralIndex; ++i) {
```

다음을

```Solidity
for (uint8 i = 1; i < s.lastCollateralIndex + 1; ++i) {
```

로 변경하십시오.

# [M-02] 잘못된 스토리지 슬롯 할당

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

GNSMultiCollatDiamond 계약의 추출된 스토리지 레이아웃이 있습니다.

```
| Name                       | Type                        | Slot | Offset | Bytes |

| _initialized               | uint8                       | 0    | 0      | 1     |
| _initializing              | bool                        | 0    | 1      | 1     |
| addressStore               | AddressStore                | 1    | 0      | 1632  |
| pairsStorage               | PairsStorage                | 52   | 0      | 1600  |
| referralsStorage           | ReferralsStorage            | 102  | 0      | 1600  |
| feeTiersStorage            | FeeTiersStorage             | 152  | 0      | 1600  |
| priceImpactStorage         | PriceImpactStorage          | 202  | 0      | 1600  |
| diamondStorage             | DiamondStorage              | 252  | 0      | 1600  |
| tradingStorage             | TradingStorage              | 302  | 0      | 1600  |
| triggerRewardsStorage      | TriggerRewardsStorage       | 352  | 0      | 1568  |
| tradingInteractionsStorage | TradingInteractionsStorage  | 401  | 0      | 1600  |
| tradingCallbacksStorage    | TradingCallbacksStorage     | 451  | 0      | 1600  |
| borrowingFeesStorage       | BorrowingFeesStorage        | 501  | 0      | 1600  |
| priceAggregatorStorage     | PriceAggregatorStorage      | 551  | 0      | 1696  |
```

다음은 스토리지 변수를 검색하는 데 사용하도록 할당된 스토리지 슬롯입니다.

```solidity
        library StorageUtils {
            uint256 internal constant GLOBAL_ADDRESSES_SLOT = 1;
            uint256 internal constant GLOBAL_PAIRS_STORAGE_SLOT = 51;
            uint256 internal constant GLOBAL_REFERRALS_SLOT = 101;
            uint256 internal constant GLOBAL_FEE_TIERS_SLOT = 151;
            uint256 internal constant GLOBAL_PRICE_IMPACT_SLOT = 201;
            uint256 internal constant GLOBAL_DIAMOND_SLOT = 251;
            uint256 internal constant GLOBAL_TRADING_STORAGE_SLOT = 301;
            uint256 internal constant GLOBAL_TRIGGER_REWARDS_SLOT = 351;
            uint256 internal constant GLOBAL_TRADING_SLOT = 401;
            uint256 internal constant GLOBAL_TRADING_CALLBACKS_SLOT = 451;
            uint256 internal constant GLOBAL_BORROWING_FEES_SLOT = 501;
            uint256 internal constant GLOBAL_PRICE_AGGREGATOR_SLOT = 551;
        }
```

[링크](https://github.com/GainsNetwork-org/gTrade-contracts/blob/ac1ec54fbfc381e4d52569c41f41cb7348abcb03/contracts/libraries/StorageUtils.sol#L12-L25)

`PairsStorage`, `ReferralsStorage`, `FeeTiersStorage`, `PriceImpactStorage`, `DiamondStorage`, `TradingStorage` 및 `TriggerRewardsStorage`의 스토리지 슬롯이 잘못 할당된 것을 볼 수 있습니다.

이는 스토리지 충돌로 이어지고 `GNSMultiCollatDiamond` 계약을 파손시킬 수 있습니다.

## 권장 사항

`StorageUtils`의 상수를 업데이트하여 올바른 스토리지 슬롯을 반영하십시오.

# [M-03] `v.positionSizeCollateralAfterReferralFees`의 부정확한 계산

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

v.positionSizeCollateralAfterReferralFees 및 reward1(실제 레퍼럴 보상)을 계산할 때 다음 계산이 사용됩니다.

```solidity
    // ...
    if (_getMultiCollatDiamond().getTraderActiveReferrer(_trade.user) != address(0)) {
        // Use this variable to store position size for dev/gov fees after referral fees
        // and before volumeReferredUsd increases
>>>     v.positionSizeCollateralAfterReferralFees =
            (v.positionSizeCollateral *
                (100 *
                    PRECISION -
                    _getMultiCollatDiamond().calculateFeeAmount(
                        _trade.user,
                        _getMultiCollatDiamond().getReferralsPercentOfOpenFeeP(_trade.user)
                    ))) /
            100 /
            PRECISION;

>>>     v.reward1 = _distributeReferralReward(
            _trade.collateralIndex,
            _trade.user,
            _getMultiCollatDiamond().calculateFeeAmount(_trade.user, v.positionSizeCollateral), // apply fee tiers here to v.positionSizeCollateral itself to make correct calculations inside referrals
            _getMultiCollatDiamond().pairOpenFeeP(_trade.pairIndex),
            v.gnsPriceCollateral
        );

        _sendToVault(_trade.collateralIndex, v.reward1, _trade.user);

        _trade.collateralAmount -= uint120(v.reward1);

        emit ITradingCallbacksUtils.ReferralFeeCharged(_trade.user, _trade.collateralIndex, v.reward1);
    }
    // ...
```

여기서 레퍼럴 오픈 수수료의 백분율은 `getReferralsPercentOfOpenFeeP`에서 나오며 다음 공식을 사용합니다.

```solidity
    function getPercentOfOpenFeeP_calc(uint256 _volumeReferredUsd) internal view returns (uint256 resultP) {
        IReferralsUtils.ReferralsStorage storage s = _getStorage();
        uint startReferrerFeeP = s.startReferrerFeeP;
        uint openFeeP = s.openFeeP;
        resultP =
            (openFeeP *
                (startReferrerFeeP *
                    PRECISION +
                    (_volumeReferredUsd * PRECISION * (100 - startReferrerFeeP)) /
                    1e18 /
                    s.targetVolumeUsd)) /
            100;

        resultP = resultP > openFeeP * PRECISION ? openFeeP * PRECISION : resultP;
    }
```

그러나 `reward1`을 계산할 때 `getReferrerFeeP`를 사용하는데, 이는 레퍼러(referrer) 수수료를 계산하는 공식이 다릅니다:

```solidity
    function getReferrerFeeP(uint256 _pairOpenFeeP, uint256 _volumeReferredUsd) internal view returns (uint256) {
        IReferralsUtils.ReferralsStorage storage s = _getStorage();

        uint256 maxReferrerFeeP = (_pairOpenFeeP * 2 * s.openFeeP) / 100;

        uint256 minFeeP = (maxReferrerFeeP * s.startReferrerFeeP) / 100;

        uint256 feeP = minFeeP + ((maxReferrerFeeP - minFeeP) * _volumeReferredUsd) / 1e18 / s.targetVolumeUsd;

        return feeP > maxReferrerFeeP ? maxReferrerFeeP : feeP;
    }
```

이로 인해 `v.positionSizeCollateralAfterReferralFees`가 실제 레퍼럴 수수료를 기반으로 하지 않게 됩니다. 결과적으로 `v.positionSizeCollateralAfterReferralFees`가 `govFee`를 계산하기 위해 `_handleGovFees`에 전달될 때 잘못된 값을 처리하게 됩니다.

## 권장 사항

`v.positionSizeCollateralAfterReferralFees`를 계산할 때 대신 실제 `reward1`을 사용하십시오:

```solidity
v.positionSizeCollateralAfterReferralFees = v.positionSizeCollateral - v.reward1
```

# [M-04] 레퍼러 및 동맹에 대한 레퍼럴 수수료의 부정확한 계산

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

레퍼럴 보상을 계산할 때 수수료 등급이 `positionSizeCollateral`에 즉시 적용되므로 포지션 크기는 이미 트레이더의 `feeMultiplierCache`에 의해 조정됩니다.

        function _registerTrade(
            ITradingStorage.Trade memory _trade,
            ITradingStorage.PendingOrder memory _pendingOrder
        ) internal returns (ITradingStorage.Trade memory, uint256) {
            ...
                v.reward1 = _distributeReferralReward(
                    _trade.collateralIndex,
                    _trade.user,
                    _getMultiCollatDiamond().calculateFeeAmount(_trade.user, v.positionSizeCollateral), // apply fee tiers here to v.positionSizeCollateral itself to make correct calculations inside referrals
                    _getMultiCollatDiamond().pairOpenFeeP(_trade.pairIndex),
                    v.gnsPriceCollateral
                );
            ...
        }

조정된 `positionSizeCollateral`을 사용하는 것은 레퍼러 수수료 계산에 있어 정확합니다. 그러나 포지션이 `(1e3 - feeMultiplierCache) / FEE_MULTIPLIER_SCALE` 비율만큼 줄어들기 때문에 레퍼러의 `volumeReferredUsd`에 대해서는 잘못된 계산입니다.

        function distributeReferralReward(
            address _trader,
            uint256 _volumeUsd, // 1e18
            uint256 _pairOpenFeeP,
            uint256 _gnsPriceUsd // PRECISION (1e10)
        ) internal returns (uint256) {
            ...
            if (a.active) {
                ...
                a.volumeReferredUsd += _volumeUsd;
                ...
            }

            r.volumeReferredUsd += _volumeUsd;
            ...
        }

따라서 `_volumeReferredUsd`가 `feeP`를 계산하는 데 사용되므로 레퍼러의 잘못된 레퍼러 수수료로 이어집니다.

        function getReferrerFeeP(uint256 _pairOpenFeeP, uint256 _volumeReferredUsd) internal view returns (uint256) {
            IReferralsUtils.ReferralsStorage storage s = _getStorage();

            uint256 maxReferrerFeeP = (_pairOpenFeeP * 2 * s.openFeeP) / 100;
            uint256 minFeeP = (maxReferrerFeeP * s.startReferrerFeeP) / 100;

            uint256 feeP = minFeeP + ((maxReferrerFeeP - minFeeP) * _volumeReferredUsd) / 1e18 / s.targetVolumeUsd;
            return feeP > maxReferrerFeeP ? maxReferrerFeeP : feeP;
        }

## 권장 사항

레퍼러 및 동맹에 대한 `volumeReferredUsd`를 계산할 때 `calculateFeeAmount(_trade.user, v.positionSizeCollateral)` 대신 `positionSizeCollateral`을 사용하십시오.

# [M-05] Reorg(재구성) 사고가 주문 이행에 영향을 줄 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

오라클 노드는 각 요청을 식별하기 위해 `requestId` 매개변수를 사용하여 가격 데이터를 이행합니다. 이 매개변수는 다이아몬드 주소와 L190의 현재 요청 카운트(논스)를 기반으로 생성됩니다.

```solidity
File: ChainlinkClientUtils.sol
183:     function _rawRequest(
184:         address oracleAddress,
185:         uint256 nonce,
186:         uint256 payment,
187:         bytes memory encodedRequest
188:     ) private returns (bytes32 requestId) {
189:         IPriceAggregator.PriceAggregatorStorage storage s = _getStorage();
190:         requestId = keccak256(abi.encodePacked(this, nonce));
191:         s.pendingRequests[requestId] = oracleAddress;
192:         emit ChainlinkRequested(requestId);
193:         if (!s.link.transferAndCall(oracleAddress, payment, encodedRequest))
194:             revert IPriceAggregatorUtils.TransferAndCallToOracleFailed();
195:     }
```

Reorg 사건 발생 시 다른 주문에 대해 요청된 가격 데이터로 주문이 이행되는 상황이 발생할 수 있습니다. 이로 인해 완전히 잘못된 가격으로 주문이 실행되어 발생해서는 안 되는 사용자의 손실/이득이 발생할 수 있습니다.

다음 시나리오를 고려해 보십시오(단순화를 위해 시스템에 오라클이 하나만 존재한다고 가정).

1. 앨리스가 거래를 시작합니다. 그녀의 주문에 대한 `requestId`는 다이아몬드 스토리지의 현재 논스 "1"을 기반으로 합니다 - "abc123".
2. 밥이 거래를 시작합니다. 그의 주문에 대한 `requestId`는 다이아몬드 스토리지의 현재 논스 "2"를 기반으로 합니다 - "def456".
3. 오라클은 예상대로 `requestId`를 기반으로 각 주문에 대한 정확한 가격으로 2개의 이행 트랜잭션을 보냅니다.
4. 트랜잭션 1과 2의 실행 순서에 영향을 미치는 Reorg 사건이 발생합니다. 이제 앨리스의 주문은 논스 "2"- "def456"을 기반으로 하는 `requestId`를 갖게 됩니다. 그러나 밥의 주문에 대한 가격이 포함된 이 `requestId`에 대해 오라클에 의해 이행 트랜잭션이 이미 전송되었으므로 앨리스의 거래는 완전히 잘못된 데이터로 실행되며, 밥도 마찬가지입니다.

## 권장 사항

`orderId` 값을 `requestId` 생성에 섞는 것을 고려하십시오. 이렇게 하면 각 요청이 고유한 식별을 갖도록 보장됩니다.

# [M-06] 수익성 있는 거래 포지션이 부당한 청산 및 이익 손실 위험에 처함

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

청산인이 거래를 청산하기 위해 `triggerOrder`를 호출하면 `_fromBlock` 매개변수는 `tradeInfo.createdBlock`으로 설정됩니다. 시스템에서 거래가 실행되고 저장된 블록입니다.

        function _getPriceTriggerOrder(
            ITradingStorage.Trade memory _trade,
            ITradingStorage.Id memory _orderId,
            ITradingStorage.PendingOrderType _orderType,
            uint256 _positionSizeCollateral // collateral precision
        ) internal {
            ITradingStorage.TradeInfo memory tradeInfo = _getMultiCollatDiamond().getTradeInfo(_trade.user, _trade.index);

            _getMultiCollatDiamond().getPrice(
                _trade.collateralIndex,
                _trade.pairIndex,
                _orderId,
                _orderType,
                _positionSizeCollateral,
                _orderType == ITradingStorage.PendingOrderType.SL_CLOSE
                    ? tradeInfo.slLastUpdatedBlock
                    : _orderType == ITradingStorage.PendingOrderType.TP_CLOSE
                        ? tradeInfo.tpLastUpdatedBlock
        @>              : tradeInfo.createdBlock
            );
        }

오라클이 가격을 반환할 때 3가지 값으로 가격을 반환합니다:

- \_a.open: 현재 가격.
- \_a.high: `tradeInfo.createdBlock`에서 현재까지 가장 높은 가격.
- \_a.low: `tradeInfo.createdBlock`에서 현재까지 가장 낮은 가격.

청산 가격이 `_a.low` 및 `_a.high` 범위 내에 있으면 `exactExecution`(정확한 실행)을 의미하며 `executionPrice`는 `liqPrice`와 같습니다.

        function executeTriggerCloseOrderCallback(
            ITradingCallbacks.AggregatorAnswer memory _a
        ) internal onlyTradingActivated(true) {
            ...
                if (o.orderType == ITradingStorage.PendingOrderType.LIQ_CLOSE) {
                    v.liqPrice = _getMultiCollatDiamond().getTradeLiquidationPrice(
                        IBorrowingFees.LiqPriceInput(
                            t.collateralIndex,
                            t.user,
                            t.pairIndex,
                            t.index,
                            t.openPrice,
                            t.long,
                            uint256(t.collateralAmount),
                            t.leverage
                        )
                    );
                }

                v.executionPrice = o.orderType == ITradingStorage.PendingOrderType.TP_CLOSE
                    ? t.tp
                    : (o.orderType == ITradingStorage.PendingOrderType.SL_CLOSE ? t.sl : v.liqPrice);

                v.exactExecution = v.executionPrice > 0 && _a.low <= v.executionPrice && _a.high >= v.executionPrice;

                if (v.exactExecution) {
                    v.reward1 = o.orderType == ITradingStorage.PendingOrderType.LIQ_CLOSE
                        ? (uint256(t.collateralAmount) * 5) / 100
                        : (v.positionSizeCollateral * _getMultiCollatDiamond().pairTriggerOrderFeeP(t.pairIndex)) /
                            100 /
                            PRECISION;
                } else {
                    v.executionPrice = _a.open;
            ...
        }

문제는 차입 수수료의 누적으로 인해 시간이 지남에 따라 청산 가격이 점진적으로 상승하여 `liqPriceDistance`가 감소하고 그에 따라 `liqPrice`가 증가함에 따라 발생합니다.

        function _getTradeLiquidationPrice(
            uint256 _openPrice,
            bool _long,
            uint256 _collateral,
            uint256 _leverage,
            uint256 _borrowingFeeCollateral,
            uint128 _collateralPrecisionDelta
        ) internal pure returns (uint256) {
            ...
            int256 borrowingFeeInt = int256(_borrowingFeeCollateral * precisionDeltaUint * 1e3); // 1e18 * 1e3

            // PRECISION
            int256 liqPriceDistance = (openPriceInt * (collateralLiqNegativePnlInt - borrowingFeeInt)) / // 1e10 * 1e18 * 1e3
                int256(_collateral) /
                int256(_leverage) /
                int256(precisionDeltaUint); // 1e10

            int256 liqPrice = _long ? openPriceInt - liqPriceDistance : openPriceInt + liqPriceDistance; // 1e10

            return liqPrice > 0 ? uint256(liqPrice) : 0; // 1e10
        }

이 메커니즘은 포지션의 수익성을 충분히 고려하지 않습니다. 예를 들어, 트레이더의 수익성 높은 포지션은 호의적인 시장 추세에도 불구하고 차입 수수료로 인해 청산 가격이 저가 및 고가 범위 내로 조정될 경우 청산 대상이 될 수 있습니다. 거래 포지션이 충분히 오래 열려 있으면 청산 가격이 상승하여 `_a.low`와 같아질 수 있습니다. 그 순간 포지션은 현재 수익 수준에 관계없이 청산 가능합니다.

쌍의 가격이 상승하고 거래에 스톱 리밋(stop limit)이 없는 시나리오를 고려해 보겠습니다.

- 트레이더가 100배 레버리지로 $1505에 롱 포지션에 진입합니다. 청산 가격은 $1494로 계산됩니다.
- 시장이 상승하고 트레이더는 포지션을 열린 상태로 유지합니다.
- 3500 블록 (~ 7000초 ~ 1.95시간) 후 쌍의 가격이 `$1550`으로 상승하고 포지션의 수익성이 매우 높습니다. 그러나 차입 수수료로 인해 청산 가격이 `$1506`으로 인상됩니다.
- 따라서 `_a.low ($1505) < liqPrice ($1506) < _a.high ($1550)`이므로 누구나 이 포지션에 대한 청산을 트리거 할 수 있습니다. 그리고 청산 시 실행 가격은 $1506입니다.
- 청산 시 실행 가격(`$1506`)이 현재 쌍의 가격(`$1550`)보다 훨씬 낮기 때문에 트레이더는 3배의 수익을 얻는 대신 차입 수수료로 인해 모든 담보 금액을 잃고 이익을 받지 못합니다.

## 권장 사항

청산 가격 계산을 조정하여 오픈 가격 및 차입 수수료와 함께 포지션의 현재 수익성을 통합하십시오.

# [M-07] USDC 블랙리스트에 있는 트레이더에 대한 청산 실패

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

계정을 청산하는 과정에서 관련 거래가 등록 취소되고 남은 담보는 트레이더에게 반환됩니다. 청산이 발생하는 경우 `tradeValueCollateral`은 0입니다.

        function _unregisterTrade(
            ITradingStorage.Trade memory _trade,
            bool _marketOrder,
            int256 _percentProfit,
            uint256 _closingFeeCollateral,
            uint256 _triggerFeeCollateral
        ) internal returns (uint256 tradeValueCollateral) {
            ...
                if (tradeValueCollateral > collateralLeftInStorage) {
                    vault.sendAssets(tradeValueCollateral - collateralLeftInStorage, _trade.user);
                    _transferCollateralToAddress(_trade.collateralIndex, _trade.user, collateralLeftInStorage);
                } else {
                    _sendToVault(_trade.collateralIndex, collateralLeftInStorage - tradeValueCollateral, _trade.user);
                    _transferCollateralToAddress(_trade.collateralIndex, _trade.user, tradeValueCollateral);
                }

                // 4.2 If collateral in vault, just send collateral to trader from vault
            } else {
                vault.sendAssets(tradeValueCollateral, _trade.user);
            }
        }

그러나 트레이더가 USDC 계약에 의해 블랙리스트에 오른 경우 이 프로세스는 실패합니다. 구체적으로 `_transferCollateralToAddress` 함수에서의 되돌리기(revert)로 인해 0인 `tradeValueCollateral`을 전송하려고 할 때 청산 시도가 실패합니다. 이로 인해 포지션은 청산 가능성 없이 계속 가치가 하락할 수 있으므로 저장소에 재정적 손실을 초래할 수 있습니다.

## 권장 사항

담보 금액을 트레이더에게 푸시(push) 하는 대신 그들이 청구(claim) 하도록 하십시오(풀 오버 푸시(Pull over push) 패턴).

# [M-08] 스톱(Stop) 주문이 제시간에 이행되지 않을 때 트레이더가 손실을 피할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

스톱 주문의 경우 특정 시나리오에서 부정확한 실행(`exactExecution == false`)이 발생할 수 있으며, 이는 오픈/손절매 가격 대신 시장 가격을 기반으로 주문을 이행합니다.

이러한 시나리오 중 하나는 시장 재개장 중 가격 갭(price gap)으로, 시장 오픈 가격이 오픈/손절매 가격보다 훨씬 높게 형성되는 반면 `_a.low == 0 && _a.high == 0`인 경우입니다. 이 시나리오에서 스톱 주문은 시장 폐쇄로 인해 지난 1시간 동안 가격이 없었기 때문에 오픈/손절매 가격 대신 시장 가격을 기반으로 이행됩니다. 오라클은 주문 이행을 위해 최대 1시간의 가격 답변만 반환한다는 점에 유의하십시오.

그러나 시퀀서 다운타임으로 인해 시장 재개장 후 1시간 이내에 스톱 주문이 이행되지 않으면 실제로 오픈/손절매 가격(`exactExecution == true`)에서 주문을 잘못 이행할 수 있습니다.

손절매(Stop-Loss)에 대한 시나리오를 가정해 봅시다,

1. 트레이더 A가 `1000`에 거래를 시작하고 손절매를 `900`에 설정합니다.
2. 잠시 후 시장이 닫힙니다.
3. 이제 시퀀서가 다운되어 트리거 봇이 주문을 트리거 하지 못하게 합니다.
4. 시장이 다시 열리고 가격이 `500`으로 갭 하락했습니다.
5. 그러나 시퀀서가 다운됨에 따라 시장 가격 `500`에서 손절매를 이행하기 위한 부정확한 실행이 실패합니다.
6. 1시간 이상 지난 후 시퀀서가 복구됩니다.
7. 가격은 이제 `900`으로 상승했습니다.
8. 주문이 이제 다시 트리거 되고 지난 1시간 동안 가격이 있으므로 정확한 실행으로 손절매를 이행하고 `900`의 가격으로 닫습니다.
9. 이제 트레이더 A는 `500` 대신 `900`에 거래가 닫혔으므로 손실을 피할 수 있게 되어 저장소 사용자에게 이익 손실을 초래합니다.

스톱 오픈(Stop-Open) 주문의 경우에도 잘못된 정확한 실행이 발생할 수 있어, 시장 가격 대신 시장 재개장 중 오픈 가격으로 주문을 열 수 있습니다. 이는 트레이더가 잠재적으로 더 나은 가격으로 오픈하고 가격 갭을 피하여 손실을 피할 수 있음을 의미합니다.

## 권장 사항

시장 재개장 후 1시간 이내에 시퀀서가 다운되면 오라클은 시장 가격을 기반으로 스톱 주문을 올바르게 이행할 수 있도록 여전히 시장 재개장 시점의 가격 답변을 사용해야 합니다.

이 완화 조치는 시퀀서가 마지막으로 오프라인 상태가 되기 전에 설정된 스톱 로스 및 스톱 오픈 주문에 적용됩니다.

# [L-01] 불필요한 `_transferCollateralToAddress()` 사용

`TradingCallBacksUtils` 내에서 `_distributeStakingReward()`, `_sendToVault()` 및 `_unregisterTrade()` 함수는 `_transferCollateralToAddress()`를 호출하여 담보를 `address(this)`로 전송합니다. 담보가 이미 계약 자체 내에 있으므로 이는 불필요합니다.

`_transferCollateralToAddress()` 호출을 제거하여 해결할 수 있습니다.

```Solidity
    function _distributeStakingReward(uint8 _collateralIndex, address _trader, uint256 _amountCollateral) internal {
        //@audit redundant transfer as it is transferring to itself
        _transferCollateralToAddress(_collateralIndex, address(this), _amountCollateral);
        IGNSStaking(AddressStoreUtils.getAddresses().gnsStaking).distributeReward(
            _getMultiCollatDiamond().getCollateral(_collateralIndex).collateral,
            _amountCollateral
        );
        emit ITradingCallbacksUtils.GnsStakingFeeCharged(_trader, _collateralIndex, _amountCollateral);
    }

    function _sendToVault(uint8 _collateralIndex, uint256 _amountCollateral, address _trader) internal {
        //@audit redundant transfer as it is transferring to itself
        _transferCollateralToAddress(_collateralIndex, address(this), _amountCollateral);
        _getGToken(_collateralIndex).receiveAssets(_amountCollateral, _trader);
    }


    function _unregisterTrade(
        ITradingStorage.Trade memory _trade,
        bool _marketOrder,
        int256 _percentProfit,
        uint256 _closingFeeCollateral,
        uint256 _triggerFeeCollateral
    ) internal returns (uint256 tradeValueCollateral) {
             ....
            // 5. gToken vault reward
            uint256 vaultClosingFeeP = uint256(_getStorage().vaultClosingFeeP);
            v.reward2 = (_closingFeeCollateral * vaultClosingFeeP) / 100;

            //@audit redundant transfer as it is transferring to itself
            _transferCollateralToAddress(_trade.collateralIndex, address(this), v.reward2);
            vault.distributeReward(v.reward2);
            ...
    }
```

# [L-02] Chainlink 피드는 18 소수점을 가질 수 있음

코드의 여러 곳에서 모든 Chainlink USD 가격 피드가 8 소수점을 갖는다는 가정을 바탕으로 구축되었습니다. 예를 들어:

```solidity
File: PriceAggregatorUtils.sol
360:     function getLinkFee(
361:         uint8 _collateralIndex,
362:         uint16 _pairIndex,
363:         uint256 _positionSizeCollateral // collateral precision
364:     ) internal view returns (uint256) {
365:         (, int256 linkPriceUsd, , , ) = _getStorage().linkUsdPriceFeed.latestRoundData();
366:
367:         // NOTE: all [token / USD] feeds are 8 decimals
368:         return
369:             (getUsdNormalizedValue(
370:                 _collateralIndex,
371:                 _getMultiCollatDiamond().pairOracleFeeP(_pairIndex) * _positionSizeCollateral
372:             ) * 1e8) /
373:             uint256(linkPriceUsd) /
374:             PRECISION /
375:             100;
376:     }
```

그러나 18 소수점을 갖는 USD 가격 피드가 있습니다. 예를 들어, Arbitrum의 PEPE/USD입니다:
[Arbiscan 링크](https://arbiscan.io/address/0x02DEd5a7EDDA750E3Eb240b54437a54d57b74dBE#readContract#F3)

이 토큰은 현재 프로토콜 담보로 예상되지 않지만 18 소수점 피드가 있는 일부 미래 토큰이 될 수 있으며 이러한 토큰의 통합에는 프로토콜 업그레이드가 필요합니다.

추가 시 각 담보에 대한 가격 피드 소수점 수를 저장하고 가격 계산에 이 값을 사용하는 것을 고려하십시오.

# [L-03] `s.traders`는 중복을 포함하고 `getTraders()`에 영향을 미침

`storeTrade()` 및 `storePendingOrder()` 모두 `s.traders[]` 배열을 채워 거래 또는 대기 주문을 생성한 트레이더 목록을 추적합니다. 중복 주소 추가를 방지하기 위해 `s.traderStored[]` 매핑을 사용하여 기존 트레이더를 확인합니다.

그러나 트레이더 주소를 `s.traders[]` 배열에 푸시 한 후 `s.traderStored[_trade.user] = true`로 설정하지 못합니다. 이로 인해 `s.traders[]`는 모든 거래/주문마다 증가하고 중복된 트레이더 주소를 포함하게 됩니다. 이는 `getTraders()`에 대한 잘못된 결과로 이어집니다.

이 문제를 해결하려면 `storeTrade()` 및 `storePendingOrder()`에 `s.traderStored[_trade.user] = true`를 추가하십시오.

```Solidity
    function storeTrade(
        ITradingStorage.Trade memory _trade,
        ITradingStorage.TradeInfo memory _tradeInfo
    ) internal returns (ITradingStorage.Trade memory) {
       ITradingStorage.TradingStorage storage s = _getStorage();
        ...
        if (!s.traderStored[_trade.user]) {
            //@audit missing s.traderStored[_trade.user] = true
            s.traders.push(_trade.user);
        }
   }


    function storePendingOrder(
        ITradingStorage.PendingOrder memory _pendingOrder
    ) internal returns (ITradingStorage.PendingOrder memory) {
        if (_pendingOrder.user == address(0) || _pendingOrder.trade.user == address(0))
            revert IGeneralErrors.ZeroAddress();

        ITradingStorage.TradingStorage storage s = _getStorage();
         ...
        if (!s.traderStored[_pendingOrder.user]) {
            //@audit missing s.traderStored[_pendingOrder.user] = true
            s.traders.push(_pendingOrder.user);
        }
   }
```

# [L-04] `updateTrade`가 호출될 때 `_correctTp` 및 `_correctSl`이 호출되어야 함

`storeTrade`를 사용하여 거래가 생성되고 저장될 때 `_correctTp` 및 `_correctSl`이 호출되어 TP 및 SL을 조정합니다. 그러나 트레이더가 `updateOpenOrder`를 호출하여 거래의 TP 및 SL을 업데이트하고 결국 `updateTrade`를 트리거 하는 경우 `_correctTp` 및 `_correctSl`을 사용하여 TP 및 SL이 조정되지 않습니다. `updateTrade`가 트리거 될 때 TP 및 SL 수정도 수행하는 것을 고려하십시오.

# [L-05] 추가적인 더스트(dust) 양의 GNS가 발행됨

`TriggerRewardsUtils#distributeTriggerReward` 함수는 L46에서 오라클 당 동일한 양의 보상을 계산합니다:

```solidity
File: TriggerRewardsUtils.sol
42:     function distributeTriggerReward(uint256 _rewardGns) internal {
43:         ITriggerRewards.TriggerRewardsStorage storage s = _getStorage();
44:
45:         address[] memory oracles = _getMultiCollatDiamond().getOracles();
46:         uint256 rewardPerOracleGns = _rewardGns / oracles.length;
47:
48:         for (uint256 i; i < oracles.length; ++i) {
49:             s.pendingRewardsGns[oracles[i]] += rewardPerOracleGns;
50:         }
51:
52:         IERC20(AddressStoreUtils.getAddresses().gns).mint(address(this), _rewardGns);
53:
54:         emit ITriggerRewardsUtils.TriggerRewarded(rewardPerOracleGns, oracles.length);
55:     }
```

그러나 내림으로 인해 저장된 보상의 합계 `rewardPerOracleGns * oracles.length`는 종종 L52의 실제 발행 금액보다 적으므로 더스트 추가 양의 GNS 토큰이 발행됩니다.

정확한 보상 합계인 `rewardPerOracleGns * oracles.length`로 `mint` 함수를 호출하는 것을 고려하십시오.

# [L-06] `getAllTrades` 및 `getAllTradeInfos`는 루프를 더 일찍 종료해야 함

`getAllTrades` 및 `getAllTradeInfos`는 제공된 `_offset` 및 `_limit`를 기반으로 데이터 배열을 얻기 위해 모든 트레이더 및 `Trade`/`TradeInfo`를 루프 합니다. 그러나 `currentTradeIndex`가 `_limit`에 도달해도 루프는 계속됩니다.

```solidity
    function getAllTrades(uint256 _offset, uint256 _limit) internal view returns (ITradingStorage.Trade[] memory) {
        // Fetch all traders with open trades (no pagination, return size is not an issue here)
        address[] memory traders = getTraders(0, 0);

        uint256 currentTradeIndex; // current global trade index
        uint256 currentArrayIndex; // current index in returned trades array

        ITradingStorage.Trade[] memory trades = new ITradingStorage.Trade[](_limit - _offset + 1);

        // Fetch all trades for each trader
        for (uint256 i; i < traders.length; ++i) {
            ITradingStorage.Trade[] memory traderTrades = getTrades(traders[i]);

            // Add trader trades to final trades array only if within _offset and _limit
            for (uint256 j; j < traderTrades.length; ++j) {

                if (currentTradeIndex >= _offset && currentTradeIndex <= _limit) {
                    trades[currentArrayIndex++] = traderTrades[j];
                }
                // @audit - will continue even when _limit is reached
                currentTradeIndex++;
            }
        }

        return trades;
    }
```

`currentTradeIndex`가 `_limit`에 도달하면 루프를 중단하는 것을 고려하십시오.

