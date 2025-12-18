# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**reya-network** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Reya Network 정보

Reya Network는 무기한 선물(perpetuals)을 위해 거래 최적화된 모듈형 L2입니다. 체인 계층은 Arbitrum Orbit에 의해 구동되며 가스 요금이 없고 트랜잭션은 FIFO 기준으로 주문됩니다. 프로토콜 계층은 PnL 결제, 증거금 요구 사항, 청산과 같은 거래를 지원하기 위해 체인을 모듈형 구성 요소로 분해하여 DeFi 애플리케이션의 수직적 통합을 직접 해결합니다.

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

_검토 커밋 해시_ - [5ef6ed67b5478b734485f04ebc6167630812092c](https://github.com/Reya-Labs/reya-network/tree/5ef6ed67b5478b734485f04ebc6167630812092c)

_수정 검토 커밋 해시_ - [95671a3dd756d33a8cdce40dd728e388e23d2bce](https://github.com/Reya-Labs/reya-network/tree/95671a3dd756d33a8cdce40dd728e388e23d2bce)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `TransferCollateral`
- `ExecutionModule`
- `AccountCollateral`
- `BackstopLPConfiguration`
- `CollateralConfiguration`
- `CollateralPool`
- `Market`
- `ConfigurationModule`
- `PassivePerpInstrumentModule`
- `ExecutionModule`
- `OrderModule`
- `Configuration`
- `PrbMathHelper`
- `Timer`
- `Events`
- `Errors`
- `DataTypes`

# 발견 사항

# [H-01] 인출 한도 확인 누락

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

제공된 업데이트는 `CollateralConfiguration.checkWithdrawLimits` 함수에서 인출 한도 확인을 완전히 제외하고 이를 `AccountCollateral.updateBalance` 함수에 넣습니다. 따라서 `AccountCollateral.transferFunds` 및 `ExposedModule.updateCollateralShares` 함수는 `updateBalance` 함수를 우회하므로 `GlobalCollateralConfiguration.checkWithdrawLimits` 또는 `CollateralConfiguration.checkWithdrawLimits` 확인이 없습니다.

```solidity
    function transferFunds(uint128 fromAccountId, uint128 toAccountId) internal {
        if (fromAccountId == toAccountId) {
            return;
        }

        Data storage fromAccountCollateral = load(fromAccountId);
        address[] storage collaterals = GlobalCollateralConfiguration.load().collaterals;
        for (uint256 i = 0; i < collaterals.length; i++) {
            address activeCollateral = collaterals[i];
            int256 amount = fromAccountCollateral
                .collateralShares[activeCollateral];

            if (amount == 0) {
                continue;
            }

>>          updateShares(fromAccountId, activeCollateral, -amount);
            updateShares(toAccountId, activeCollateral, amount);
        }
    }
```

```solidity
// Export some internal functions
contract ExposedModule {
    using CollateralPool for CollateralPool.Data;
<...>
    function updateCollateralShares(uint128 cpId, address collateral, int256 sharesDelta) external {
>>      CollateralPool.updateCollateralShares(CollateralPool.exists(cpId), collateral, sharesDelta);
    }
}
```

## 권장 사항

이러한 분기에 대해 해당 확인을 구현하는 것을 고려하십시오.

# [H-02] 계정 병합 중 인출 한도 무시

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`CollateralPool.merge`는 담보 풀 `child`를 담보 풀 `parent`로 병합하며 `child` 풀의 인출 한도를 무시합니다.

```solidity
    function merge(Data storage parent, Data storage child) internal {
<...>
            // transfer funds from the child collateral pool to the parent
            {
                address[] storage collaterals = GlobalCollateralConfiguration.load().collaterals;

                for (uint256 i = 0; i < collaterals.length; i++) {
                    address collateral = collaterals[i];
                    int256 amount = child.collateralShares[collateral].toInt();

                    if (amount == 0) {
                        continue;
                    }

                    CollateralConfiguration.exists(parentId, collateral);
>>                  _updateCollateralShares(child, collateral, -amount);
                    _updateCollateralShares(parent, collateral, amount);
                }
            }
```

`child` 풀 인출 한도가 `parent` 풀로 이전되지 않으므로 `merge` 직후 자금을 직접 인출할 수 있습니다.

## 권장 사항

`child` 풀의 모든 인출 한도를 `parent` 풀로 이전하는 것을 고려하십시오.

# [M-01] 인출 쿨다운이 있어도 LP가 즉시 인출 가능

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

Backstop LP에 대한 인출 쿨다운 기간은 이제 `backstopLPConfig.withdrawCooldownDurationInSeconds`를 사용하여 구성됩니다. 0보다 크면 인출 쿨다운 기간이 켜지고 0과 같으면 꺼집니다.

그러나 문제는 Backstop LP가 인출 쿨다운이 켜지기 전에 인출 타이머를 시작할 수 있어 인출 쿨다운이 활성화된 후에도 즉시 인출할 수 있다는 것입니다.

1. 인출에 대한 쿨다운 기간이 한동안 비활성화되어 `withdrawCooldownDurationInSeconds == 0`이라고 가정합니다.
2. 쿨다운 기간이 곧 활성화될 것이라는 것을 알고 있는 Backstop LP는 `announceBackstopLpWithdraw()`를 사용하여 인출 타이머를 시작합니다.
3. 프로토콜은 이제 인출 쿨다운을 `X` 초로 활성화합니다.
4. 그러나 이전에 인출을 발표했던 Backstop LP는 인출 기간이 활성 상태이므로 즉시 인출할 수 있습니다.

```Solidity
    function announceBackstopLpWithdraw(Account.Data storage account) internal {
        CollateralPool.Data storage collateralPool = AccountCollateral.getPool(account.id);
        BackstopLPConfig memory backstopLPConfig = collateralPool.backstopLPConfig();
        uint128 backstopLpAccountId = backstopLPConfig.accountId;

        if (backstopLpAccountId != account.id) {
            revert Errors.AccountIsNotBackstopLp(
                account.id,
                backstopLpAccountId,
                block.timestamp
            );
        }

        Timer.Data storage backstopLpWithdrawTimer = Timer.loadOrCreate(
            backstopLpTimerId(backstopLpAccountId)
        );
        if (block.timestamp < backstopLpWithdrawTimer.startTimestamp) {
            revert Errors.BackstopLpCooldownPeriodAlreadyActive(
                backstopLpAccountId,
                backstopLpWithdrawTimer.startTimestamp,
                block.timestamp
            );
        }
        if (backstopLpWithdrawTimer.isActive()) {
            revert Errors.BackstopLpWithdrawPeriodAlreadyActive(
                backstopLpAccountId,
                block.timestamp
            );
        }

        backstopLpWithdrawTimer.schedule(
            block.timestamp
            +
            backstopLPConfig.withdrawCooldownDurationInSeconds,
            backstopLPConfig.withdrawDurationInSeconds
        );
    }
```

## 권장 사항

`backstopLPConfig.withdrawCooldownDurationInSeconds == 0`일 때 되돌려서 Backstop LP가 `announceBackstopLpWithdraw()`를 사용하여 인출 타이머를 시작하지 못하도록 하십시오.

# [M-02] 새로운 MarketConfigurationData로 인해 기존 주문이 실패함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`MarketConfigurationData`에는 슬리피지 및 노출 확인을 수행하기 위해 `Market.getPSlippage()`에서 사용되는 세 가지 새로운 저장 변수(`depthFactor`, `maxExposureFactor`, `maxPSlippage`)가 있습니다.

문제는 이러한 새로운 저장 변수가 이 업그레이드 전에는 사용되지 않았기 때문에 기존 `Market`에 대해 0으로 초기화된다는 것입니다.

`maxExposureFactor == 0`이면 순 노출이 유효하더라도 `getPSlippage()`가 잘못 되돌려집니다.

```Solidity
    function getPSlippage(
        Data storage self,
        SD59x18 deltaBase,
        UD60x18 oraclePrice
    )
        internal
        view
        returns (SD59x18 pSlippage)
    {
        MarketConfigurationData memory marketConfig = getConfig(self);

        uint256 riskMatrixIndex = marketConfig.riskMatrixIndex;
        UD60x18 depthFactor = marketConfig.depthFactor;
        UD60x18 maxExposureFactor = marketConfig.maxExposureFactor;
        UD60x18 maxPSlippage = marketConfig.maxPSlippage;

        (UD60x18 maxExposureShort, UD60x18 maxExposureLong, SD59x18[] memory exposures) = getPoolMaxExposures(self);
        SD59x18 deltaExposure = convertBaseToExposure(deltaBase, oraclePrice);

        SD59x18 netExposure = exposures[riskMatrixIndex].add(deltaExposure);
        UD60x18 maxExposure = netExposure.lt(ZERO_sd) ? maxExposureShort : maxExposureLong;

        //@audit when maxExposureFactor == 0, this will revert for valid net exposure too
        if (netExposure.abs().intoUD60x18().gte(maxExposure.mul(maxExposureFactor))) {
            revert Errors.ExceededMaxExposure(netExposure, maxExposure);
        }

        pSlippage = computePSlippage({ netExposure: netExposure, maxExposure: maxExposure, depthFactor: depthFactor });

        if (pSlippage.abs().intoUD60x18().gt(maxPSlippage)) {
            revert Errors.ExceededPSlippage(pSlippage, maxPSlippage);
        }
    }
```

## 권장 사항

계약 업그레이드를 수행할 때 새 저장 변수가 기본값으로 초기화되도록 하십시오. 그렇지 않으면 `getPSlippage()`에서 초기화되지 않은 값을 처리하십시오.

# [M-03] 담보 상한(cap) 확인 우회

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`CollateralPool.merge`는 `_updateCollateralShares` 함수를 직접 호출하므로 `updateCollateralShares` 함수의 상한 확인을 우회하여 담보 풀 `child`를 담보 풀 `parent`로 병합합니다.

```solidity
    function merge(Data storage parent, Data storage child) internal {
<...>
            // transfer funds from the child collateral pool to the parent
            {
                address[] storage collaterals = GlobalCollateralConfiguration.load().collaterals;

                for (uint256 i = 0; i < collaterals.length; i++) {
                    address collateral = collaterals[i];
                    int256 amount = child.collateralShares[collateral].toInt();

                    if (amount == 0) {
                        continue;
                    }

                    CollateralConfiguration.exists(parentId, collateral);
                    _updateCollateralShares(child, collateral, -amount);
>>                  _updateCollateralShares(parent, collateral, amount);
                }
            }
```

병합 전 `parent` 풀 잔액이 비어 있지 않은 경우 `collateralConfig.baseConfig.cap`이 초과될 수 있습니다.

## 권장 사항

`merge` 함수에서 담보 상한을 확인하는 것을 고려하십시오.

# [M-04] 인출이 글로벌 한도를 초과하면 `matchOrders`에 영향을 줌

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`AccountCollateral::updateBalance` 함수 내에서 인출이 매 X 기간 동안 글로벌 비율을 초과하지 않는지 확인하는 유효성 검사가 추가되었습니다(코드 120행).

```solidity
File: AccountCollateral.sol
107:     function updateBalance(Account.Data storage account, address collateral, int256 assets) internal {
108:         // Convert assets to shares
109:         int256 shares = GlobalCollateralConfiguration.convertToShares(collateral, assets);
110:
111:         // check withdrawal limits, globally and per collateral pool
112:         if (assets < 0) {
113:             uint256 withdrawnAssets = (-assets).toUint();
114:
115:             if (hasPool(account.id)) {
116:                 uint128 collateralPoolId = getPool(account.id).id;
117:                 CollateralConfiguration.exists(collateralPoolId, collateral).checkWithdrawLimits(withdrawnAssets);
118:             }
119:
120:             GlobalCollateralConfiguration.checkWithdrawLimits(collateral, withdrawnAssets);
121:         }
122:
123:         updateShares(account.id, collateral, shares);
124:     }
```

이 문제는 `MatchOrderModule::executeMatchOrder`에서, 특히 85행에서 수수료 금액이 계정에서 공제될 때 발생합니다.

```solidity
File: MatchOrderModule.sol
034:     function executeMatchOrder(
035:         address caller,
036:         uint128 marketId,
037:         uint128 accountId,
038:         uint128 exchangeId,
039:         uint128[] memory counterpartyAccountIds,
040:         bytes memory orderInputs
041:     )
042:         external
043:         override
044:         returns (bytes memory output)
045:     {
380: ...
381: ...
080:         MatchOrderFees memory matchOrderFees;
081:         (output, matchOrderFees) = market.executeMatchOrder({ matchOrderInputs: matchOrderInputs });
082:         validateMatchOrderFees(matchOrderFees, creditExchangeFees);
083:
084:         // deduct fees from the main account and track the total amounts of fees for protocol and exchange
085:         AccountCollateral.updateBalance(account, market.quoteCollateral, -(matchOrderFees.takerFeeDebit).toInt());
388: ...
389: ...
117:     }
```

문제는 매치 주문 실행에 영향을 미치는 다른 인출로 인해 글로벌 한도에 도달한 엣지 케이스에서 트랜잭션이 되돌려질 수 있으며, `periphery/src/modules/ExecutionModule::execute`에 추가된 새 함수에서 오는 명령 실행에도 영향을 미칠 수 있다는 것입니다. `checkWithdrawLimits`가 다시 인출을 허용할 때까지 `deadline`이 충분하지 않은 경우 서명을 다시 생성해야 하기 때문입니다.

## 권장 사항

수수료에 대해 계정에서 차감하는 경우 트랜잭션이 되돌려지지 않도록 하는 것이 좋습니다. 그렇지 않으면 매우 특정한 경우에 매치 주문 실행이 영향을 받습니다.

# [L-01] 0이 아닌 값에 대한 유효성 검사 누락

`ConfigurationModule::setMarketConfiguration` 내에서 `MarketConfigurationData.maxSlippage`가 0이 아니라는 유효성 검사가 없습니다.

```solidity
File: ConfigurationModule.sol
55:     function setMarketConfiguration(uint128 marketId, MarketConfigurationData memory config) external override {
56:         if (config.oracleNodeId == 0) {
57:             revert Errors.InvalidMarketConfiguration(marketId, config, "ORCLN");
58:         }
59:
60:         if (config.baseSpacing.eq(ZERO_ud)) {
61:             revert Errors.InvalidMarketConfiguration(marketId, config, "BSSP");
62:         }
63:
64:         NodeOutput.Data memory node =
65:             INodeModule(GlobalConfiguration.getOracleManagerAddress()).process(config.oracleNodeId);
66:         UD60x18 oraclePrice = UD60x18.wrap(node.price);
67:
68:         if (config.priceSpacing.eq(ZERO_ud) || oraclePrice.lte(config.priceSpacing.mul(ud(1000e18)))) {
69:             revert Errors.InvalidMarketConfiguration(marketId, config, "PRCSP");
70:         }
71:
72:         if (!config.minimumOrderBase.mod(config.baseSpacing).eq(ZERO_ud)) {
73:             revert Errors.InvalidMarketConfiguration(marketId, config, "MNOB");
74:         }
75:
76:         // TODO: it should be less or equal than 0.01 but it breaks a lot of testing doing so
77:         if (config.velocityMultiplier.gt(ud(1e18))) {
78:             revert Errors.InvalidMarketConfiguration(marketId, config, "VLCTM");
79:         }
80:
81:         if (config.depthFactor.eq(ZERO_ud)) {
82:             revert Errors.InvalidMarketConfiguration(marketId, config, "DPTHF");
83:         }
84:
85:         if (config.maxExposureFactor.gt(ONE_ud)) {
86:             revert Errors.InvalidMarketConfiguration(marketId, config, "MXEXF");
87:         }
88:
89:         Market.Data storage market = Market.exists(marketId);
90:         market.onlyAuthorized(Permissions.PASSIVE_PERP_MARKET_CONFIGURATOR);
91:
92:         MarketConfiguration.set(marketId, config);
93:     }
```

이것은 `Market::getPSlippage` 함수 내에서 `pSlippage` 계산에 영향을 미칠 수 있습니다. `maxPSlippage`가 0이면 `pSlippage`의 0이 아닌 값은 `ExceededPSlippage` 오류와 함께 함수를 되돌리기 때문입니다(301-303행).

```solidity
File: Market.sol
272:
273:     function getPSlippage(
274:         Data storage self,
275:         SD59x18 deltaBase,
276:         UD60x18 oraclePrice
277:     )
278:         internal
279:         view
280:         returns (SD59x18 pSlippage)
281:     {
282:         MarketConfigurationData memory marketConfig = getConfig(self);
283:
284:         uint256 riskMatrixIndex = marketConfig.riskMatrixIndex;
285:         UD60x18 depthFactor = marketConfig.depthFactor;
286:         UD60x18 maxExposureFactor = marketConfig.maxExposureFactor;
287:         UD60x18 maxPSlippage = marketConfig.maxPSlippage;
288:
289:         (UD60x18 maxExposureShort, UD60x18 maxExposureLong, SD59x18[] memory exposures) = getPoolMaxExposures(self);
290:         SD59x18 deltaExposure = convertBaseToExposure(deltaBase, oraclePrice);
291:
292:         SD59x18 netExposure = exposures[riskMatrixIndex].add(deltaExposure);
293:         UD60x18 maxExposure = netExposure.lt(ZERO_sd) ? maxExposureShort : maxExposureLong;
294:
295:         if (netExposure.abs().intoUD60x18().gte(maxExposure.mul(maxExposureFactor))) {
296:             revert Errors.ExceededMaxExposure(netExposure, maxExposure);
297:         }
298:
299:         pSlippage = computePSlippage({ netExposure: netExposure, maxExposure: maxExposure, depthFactor: depthFactor });
300:
301:         if (pSlippage.abs().intoUD60x18().gt(maxPSlippage)) {
302:             revert Errors.ExceededPSlippage(pSlippage, maxPSlippage);
303:         }
304:     }
```

`ConfigurationModule::setMarketConfiguration` 함수 내에서 `maxSlippage`가 0이 아닌지 평가하는 것이 좋습니다.

# [L-02] 명령 실행 실패 가능성

`CommandType` 열거형은 이전에 값 `4`에 할당되었던 `PropagateCashflow` 요소가 제거됨에 따라 수정되었습니다. 변경으로 인해 `TransferBetweenMarginAccounts`의 값이 `5`에서 `4`로 변경됩니다.

이로 인해 계약 업그레이드 직전에 구성된 명령이 업그레이드 후에 실행될 때 문제가 발생할 수 있습니다. 해당 명령에 `TransferBetweenMarginAccounts`가 포함된 경우 업그레이드 전의 이전 값 `5`를 기반으로 구성됩니다. 업그레이드 후 실행되면 이 명령은 계약 구현과 일치하지 않아 실패하게 됩니다.

```Solidity
enum CommandType {
    Deposit, // (core command) deposit collaterals
    Withdraw, // (core command) withdraw collaterals
    DutchLiquidation, // (core command) dutch liquidation of an account
    MatchOrder, // (market command) propagation of matched orders
    //@audit PropagateCashflow is removed from enum, causing TransferBetweenMarginAccounts to change from 5 to 4
    //PropagateCashflow, // (market command) propagation of realized PnL
    TransferBetweenMarginAccounts // (core command) transfer between two margin accounts

}
```

# [L-03] `Errors.ExceededMaxExposure`에 `maxExposureFactor` 누락

`maxExposureFactor`가 슬리피지 확인 중 최대 노출을 조정하기 위해 추가되었습니다. 그러나 `maxExposureFactor` 값은 사용자 정의 오류 `Errors.ExceededMaxExposure`에 존재하지 않아 트랜잭션이 되돌려질 때 올바른 매개변수를 내보내지 않습니다.

이 문제를 해결하려면 `Errors.ExceededMaxExposure`에 `maxExposureFactor`를 추가하십시오.

```Solidity
    function getPSlippage(
        Data storage self,
        SD59x18 deltaBase,
        UD60x18 oraclePrice
    )
        internal
        view
        returns (SD59x18 pSlippage)
    {
        ...
        if (netExposure.abs().intoUD60x18().gte(maxExposure.mul(maxExposureFactor))) {
           //@audit error does not contain the maxExposureFactor value
            revert Errors.ExceededMaxExposure(netExposure, maxExposure);
        }

        ...
    }
```
