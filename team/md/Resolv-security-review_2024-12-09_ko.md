# Pashov Audit Group 정보

Pashov Audit Group은 업계에서 잘 증명된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 챔피언이거나, 우리와 함께한 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하고 패치하는 데 도움을 주면서, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

<p><strong>resolv-im/resolv-contracts</strong> 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며, <strong>T1MOH, MrPotatoMagic, ast3ros</strong>가 <strong>Resolv</strong> 검토에 참여했습니다. 총 <strong>5</strong>개의 문제가 발견되었습니다.</p>

# Resolv 정보

<p>Resolv는 ETH로 뒷받침되는 스테이블코인 USR을 발행하고 숏 선물 포지션으로 ETH 가격 위험을 헤지하여 미달러 대비 가치를 안정적으로 유지하는 프로토콜입니다. 또한 보험 풀인 RLP를 유지하여 USR이 초과 담보 상태를 유지하도록 하고 사용자가 예치된 담보로 이러한 토큰을 민팅하고 상환할 수 있도록 합니다. 이 범위는 새로운 스왑 및 확장된 상환 메커니즘을 추가합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [a66183cfa825338cc2c6fef954bfc03a932a1544](https://github.com/resolv-im/resolv-contracts/tree/a66183cfa825338cc2c6fef954bfc03a932a1544)<br>&nbsp;&nbsp;(resolv-im/resolv-contracts)

**수정 검토 커밋 해시:**<br>• [d44104b36b6b94db811e2e66442524983e761931](https://github.com/resolv-im/resolv-contracts/tree/d44104b36b6b94db811e2e66442524983e761931)<br>&nbsp;&nbsp;(resolv-im/resolv-contracts)

# 범위

- `RlpPriceStorage.sol`
- `TheCounter.sol`
- `ExternalRequestsManager.sol`
- `UsrRedemptionExtension.sol`
- `ChainlinkOracle.sol`

# 발견 사항

# [H-01] `transferFee()`가 잘못된 전송 방법을 사용함

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`TheCounter` 계약의 `transferFee` 함수는 계약에서 관리자에게 수집된 수수료를 전송할 때 `safeTransfer` 대신 `safeTransferFrom`을 잘못 사용합니다. 이 구현 오류는 승인 누락으로 인해 함수가 되돌려지게 합니다.

결과적으로 관리자는 프로토콜 수수료를 수집할 수 없습니다.

```solidity
    function transferFee() external onlyRole(DEFAULT_ADMIN_ROLE) {
        ...
        token.safeTransferFrom(address(this), msg.sender, feeToTransfer); // @audit should use safeTransfer
        ...
    }
```

## 권장 사항

```diff
    function transferFee() external onlyRole(DEFAULT_ADMIN_ROLE) {
        ...
-        token.safeTransferFrom(address(this), msg.sender, feeToTransfer);
+        token.safeTransfer(msg.sender, feeToTransfer);
        ...
    }

```

# [L-01] 상한 유효성 검사 누락

_해결됨_

`RlpPriceStorage` 계약의 `setUpperBoundPercentage` 및 `setLowerBoundPercentage` 함수는 바운드 백분율이 `BOUND_PERCENTAGE_DENOMINATOR` (1e18)를 초과하지 않도록 하는 유효성 검사가 부족합니다.

특히 `lowerBoundPercentage` > `BOUND_PERCENTAGE_DENOMINATOR`인 경우 `currentPrice` < `(currentPrice * lowerBoundPercentage / BOUND_PERCENTAGE_DENOMINATOR)`이기 때문에 `setPrice` 함수는 항상 되돌려집니다.

바운드 백분율이 `BOUND_PERCENTAGE_DENOMINATOR`를 초과하지 않도록 유효성 검사를 추가하는 것이 좋습니다. 그렇지 않으면 하한 백분율 계산의 언더플로로 인해 setPrice()가 되돌려집니다. 이로 인해 SERVICE_ROLE이 시기 적절하게 가격을 업데이트하지 못하여 가격이 오래될 수 있습니다.

# [L-02] 상환 함수에 슬리피지 보호 누락

_해결됨_

`UsrExternalRequestsManager`의 `redeem` 함수에는 트랜잭션 제출과 실행 사이의 가격 변동으로부터 사용자를 보호하기 위한 최소 예상 금액 매개변수가 없습니다. 상환 비율은 실행 시 Aave 오라클 가격에 의해 결정됩니다. 네트워크 혼잡으로 인해 트랜잭션이 지연되거나 가격 변동성이 큰 경우:
- 사용자는 현재 가격을 기준으로 X 토큰을 기대하고 상환을 제출합니다.
- 가격이 불리하게 움직이는 동안 트랜잭션은 보류 상태로 유지됩니다.
- 실행되면 사용자는 X 토큰보다 훨씬 적은 금액을 받습니다.
- 최소 금액이 지정되지 않았으므로 사용자는 통제할 수 없습니다.

```solidity
    function redeem(
        uint256 _amount,
        address _receiver,
        address _withdrawalTokenAddress
    ) public whenNotPaused onlyAllowedProviders {
        IERC20(ISSUE_TOKEN_ADDRESS).safeTransferFrom(msg.sender, address(this), _amount);
        usrRedemptionExtension.redeem(_amount, _receiver, _withdrawalTokenAddress);

        emit Redeemed(msg.sender, _receiver, _amount, _withdrawalTokenAddress);
    }
```

redeem 함수에 `_minExpectedAmount` 매개변수를 추가하는 것이 좋습니다.

# [L-03] Aave V3 가격 소스가 부정확할 수 있음

_해결됨_

`UsrRedemptionExtension` 계약은 상환 중 인출 토큰 금액을 결정하기 위해 Aave V3의 오라클 시스템에 의존합니다. 그러나 구현에는 `Chainlink` 오라클과 상호 작용하는 방식으로 인해 잠재적인 가격 정확도 문제가 있습니다.

```solidity
    function redeem(
        uint256 _amount,
        address _receiver,
        address _withdrawalTokenAddress
    ) public whenNotPaused allowedWithdrawalToken(_withdrawalTokenAddress) onlyRole(SERVICE_ROLE) {
        ...
        IAaveOracle aavePriceOracle = IAaveOracle(ADDRESSES_PROVIDER.getPriceOracle());
        uint256 withdrawalTokenAmount = (_amount * aavePriceOracle.getAssetPrice(_withdrawalTokenAddress))
            / (aavePriceOracle.BASE_CURRENCY_UNIT() * 10 ** (USR_DECIMALS - withdrawalTokenDecimals));
        ...
    }
```

이 문제는 Aave V3의 오라클 구현에 있는 두 가지 주요 문제에서 비롯됩니다.

```solidity
  function getAssetPrice(address asset) public view override returns (uint256) {
    AggregatorInterface source = assetsSources[asset];

    if (asset == BASE_CURRENCY) {
      return BASE_CURRENCY_UNIT;
    } else if (address(source) == address(0)) {
      return _fallbackOracle.getAssetPrice(asset);
    } else {
      int256 price = source.latestAnswer(); // @audit call source.latestAnswer
      if (price > 0) {
        return uint256(price);
      } else {
        return _fallbackOracle.getAssetPrice(asset);
      }
    }
```

USDT의 Aave V3 가격 소스를 살펴보겠습니다: `0xC26D4a1c46d884cfF6dE9800B6aE7A8Cf48B4Ff8`

- 안전하지 않은 가격 가져오기: Aave 오라클은 Chainlink 가격 피드를 쿼리할 때 `latestRoundData` 대신 `latestAnswer`를 사용합니다. 가격 최신성 검증이 수행되지 않습니다.
- 인위적으로 제한된 가격은 실제 시장 상황을 반영하지 않을 수 있습니다.

`PriceCapAdapterStable` 계약:
```solidity
  function latestAnswer() external view override returns (int256) {
    int256 basePrice = ASSET_TO_USD_AGGREGATOR.latestAnswer(); // @audit call latestAnswer instead of latestRoundData
    int256 priceCap = _priceCap;

    if (basePrice > priceCap) { // @audit price is capped
      return priceCap;
    }

    return basePrice;
  }

```

결과적으로 사용자는 토큰 상환 중 잘못된 금액을 받을 수 있습니다.

직접 Chainlink 오라클 가격 가져오기를 구현하고 가격 최신성을 검증하는 것이 좋습니다.

# [L-04] 상환 한도 우회

_인정됨_

`UsrRedemptionExtension`의 `redeem` 함수는 24시간마다 재설정되는 일일 상환 한도를 구현합니다. 그러나 현재 구현은 재설정 창이 처리되는 방식으로 인해 한도 우회 공격에 취약합니다.

```solidity
    function redeem(
        uint256 _amount,
        address _receiver,
        address _withdrawalTokenAddress
    ) public whenNotPaused allowedWithdrawalToken(_withdrawalTokenAddress) onlyRole(SERVICE_ROLE) {
        ...
        uint256 currentTime = block.timestamp;
        if (currentTime >= lastResetTime + 1 days) {
            // slither-disable-start divide-before-multiply
            uint256 periodsPassed = (currentTime - lastResetTime) / 1 days;
            lastResetTime += periodsPassed * 1 days;
            // slither-disable-end divide-before-multiply

            currentRedemptionUsage = 0;

            emit RedemptionLimitReset(lastResetTime);
        }
        ...
    }
```

다음 시나리오를 고려하십시오.

- 공격자는 `lastResetTime`을 모니터링하고 24시간 재설정 창 직전까지 기다립니다.
- 그들은 허용된 최대 금액(예: 100,000 USR)에 대해 상환을 실행합니다.
- 재설정이 트리거된 후(다음 블록일 수 있음) 그들은 즉시 최대 금액에 대해 또 다른 상환을 실행합니다.
- 이를 통해 매우 짧은 시간 내에 의도한 한도의 최대 2배(예: 200,000 USR)를 상환할 수 있습니다.

상환 확장 문서에 따르면 상한선은 중요한 안전 매개변수입니다.
`24시간당 상환 한도 = USR 100,000`.

24시간의 롤링 윈도우로 상환 한도 메커니즘을 구현하는 것이 좋습니다.
