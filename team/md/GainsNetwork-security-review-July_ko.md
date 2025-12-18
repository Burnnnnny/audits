# Pashov Audit Group 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **GainsNetwork-org/gTrade-contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Gains Network 정보 (About Gains Network)

Gains Network는 유동성 효율적인 탈중앙화 레버리지 거래 플랫폼입니다. 거래 쌍에 관계없이 DAI, USDC, 또는 WETH 담보로 거래를 엽니다. 레버리지는 합성이며 각 gToken 금고와 GNS 토큰으로 뒷받침됩니다. 트레이더의 수익은 금고에서 가져와 PnL을 지불하며(양수인 경우), PnL이 음수인 경우 거래 손실을 금고가 받습니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**

- [9847881d77ebacdf0824b3440a65e8ed1ac52cbc](https://github.com/GainsNetwork-org/gTrade-contracts/tree/9847881d77ebacdf0824b3440a65e8ed1ac52cbc)
- [3586456d5b11230365823f954b46e2e8dc7e886d](https://github.com/GainsNetwork-org/gTrade-contracts/tree/3586456d5b11230365823f954b46e2e8dc7e886d)

**수정 검토 커밋 해시:**

- [e0f440b2cdddcdbe9022df6414717700906de45c](https://github.com/GainsNetwork-org/gTrade-contracts/tree/e0f440b2cdddcdbe9022df6414717700906de45c)
- [a1477f67c6125576cd55670a76e9bfd9889d65f9](https://github.com/GainsNetwork-org/gTrade-contracts/tree/a1477f67c6125576cd55670a76e9bfd9889d65f9)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

`Scope v9.1`:

- `GNSStaking`
- `GNSDiamondStorage`
- `GNSOtc`
- `OtcUtils`
- `StorageUtils`
- `TradingCommonUtils`

`Scope v8.0.1`:

- `GNSPriceAggregator`
- `LiquidityPoolUtils`
- `PriceAggregatorUtils`

# 발견 사항 (Findings)

# [H-01] 스토리지 슬롯 충돌 (Storage slot collision)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`PriceAggregatorStorage` 구조체에 대한 스토리지 레이아웃 할당에 계산 오류가 있어 `OtcStorage`와의 잠재적인 스토리지 슬롯 충돌이 발생합니다.

주요 문제는 `bytes32[2] jobIds` 필드입니다. 64비트를 차지하는 것으로 잘못 계산되었지만 실제로는 64바이트(512비트)를 차지하며 전체 스토리지 슬롯 2개에 걸쳐 있습니다.

```solidity
    struct PriceAggregatorStorage {
        IChainlinkFeed linkUsdPriceFeed; // 160 bits
        uint24 twapInterval; // 24 bits
        uint8 minAnswers; // 8 bits
        bytes32[2] jobIds; // 64 bits @audit 64 bytes instead of 64 bits
        // ... other fields
        uint256[41] __gap;
    }
```

이 계산 오류로 인해 `PriceAggregatorStorage`는 의도한 50개 슬롯 대신 실제로 52개 슬롯을 차지하게 됩니다. 스토리지 슬롯은 다음과 같이 할당됩니다.

```solidity
    uint256 internal constant GLOBAL_PRICE_AGGREGATOR_SLOT = 551;
    uint256 internal constant GLOBAL_OTC_SLOT = 601;
```

`PriceAggregatorStorage`가 551번 슬롯부터 시작하여 52개 슬롯을 차지하면, 601번 슬롯에서 시작하는 `OtcStorage`의 처음 두 슬롯과 겹치게 됩니다. 이 겹침은 데이터 손상을 유발하여 `OtcStorage`의 처음 두 슬롯이 `PriceAggregatorStorage`의 마지막 두 슬롯에 의해 덮어쓰여지게 됩니다.

## 권장 사항

`PriceAggregatorStorage`의 `__gap` 배열 크기를 39로 줄이거나 `GLOBAL_OTC_SLOT`이 603번 슬롯에서 시작하도록 변경하여 겹치지 않도록 하십시오.

# [M-01] `MIN_GNS_WEI_IN`이 너무 작아 보상이 0이 됨 (`MIN_GNS_WEI_IN` is too small leading to zero rewards)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`sellGnsForCollateral`이 실행될 때, 충분한 `collateralBalances`가 있고 계산된 `gnsAmount`가 `MIN_GNS_WEI_IN`(100)보다 큰 한 호출자는 자신의 gns를 제공된 담보로 교환할 수 있습니다.

```solidity
 function sellGnsForCollateral(uint8 _collateralIndex, uint256 _collateralAmount) internal {
 IOtc.OtcStorage storage s = _getStorage();

 uint256 availableCollateral = s.collateralBalances[_collateralIndex];
 uint256 gnsPriceCollateral = getOtcRate(_collateralIndex);
 uint256 gnsAmount = _calculateGnsAmount(_collateralIndex, _collateralAmount, gnsPriceCollateral);

 // 1. Validation
 if (_collateralAmount == 0) revert IGeneralErrors.ZeroValue();
 if (_collateralAmount > availableCollateral) revert IGeneralErrors.InsufficientBalance();
>>>     if (gnsAmount < MIN_GNS_WEI_IN) revert IGeneralErrors.BelowMin();

 // 2. Receive GNS from caller
 TradingCommonUtils.transferGnsFrom(msg.sender, gnsAmount);

 // 3. Reduce available OTC balance for collateral
 uint256 newBalanceCollateral = availableCollateral - _collateralAmount;
 s.collateralBalances[_collateralIndex] = newBalanceCollateral;
 emit IOtcUtils.OtcBalanceUpdated(_collateralIndex, newBalanceCollateral);

 // 4. Distribute GNS
 (uint256 treasuryAmountGns, uint256 stakingAmountGns, uint256 burnAmountGns) = _calculateGnsDistribution(
 gnsAmount
 );

 if (treasuryAmountGns > 0) _distributeTreasuryGns(treasuryAmountGns);
 if (stakingAmountGns > 0) _distributeStakingGns(stakingAmountGns);
 if (burnAmountGns > 0) _burnGns(burnAmountGns);

 // 5. Transfer collateral to caller
 TradingCommonUtils.transferCollateralTo(_collateralIndex, msg.sender, _collateralAmount);

 emit IOtcUtils.OtcExecuted(
 _collateralIndex,
 _collateralAmount,
 gnsPriceCollateral,
 treasuryAmountGns,
 stakingAmountGns,
 burnAmountGns
 );
 }
```

수신된 `gnsAmount`는 구성된 백분율을 사용하여 `_calculateGnsDistribution`에 의해 계산된 대로 재무부, GNS 스테이킹 및 소각으로 분배됩니다. 그러나 Gns 스테이킹에 분배할 때 제공된 금액이 작은 값으로 인해 0의 추가 `accRewardPerGns`를 초래할 수 있습니다.

```solidity
 function distributeReward(address _token, uint256 _amountToken) external override onlyRewardToken(_token) {
 require(gnsBalance > 0, "NO_GNS_STAKED");

 IERC20(_token).safeTransferFrom(msg.sender, address(this), _amountToken);

 RewardState storage rewardState = rewardTokenState[_token];
>>>     rewardState.accRewardPerGns += uint128((_amountToken * rewardState.precisionDelta * 1e18) / gnsBalance);

 emit RewardDistributed(_token, _amountToken);
 }
```

이로 인해 GNS 스테이킹 계약으로 전송된 GNS 토큰이 보상으로 기록되지 않게 됩니다.

## 권장 사항

`MIN_GNS_WEI_IN` 값을 더 안전하고 합리적인 금액으로 늘리십시오.
