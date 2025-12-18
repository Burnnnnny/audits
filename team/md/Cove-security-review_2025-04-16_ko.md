# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Storm-Labs-Inc/cove-contracts-core** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Cove 소개 (About Cove)

Cove는 코프로그래밍 방식 주문을 사용하여 포트폴리오를 효율적으로 리밸런싱함으로써 수익을 극대화하고, 오프체인 거래 매칭과 최적화된 실행을 통해 리밸런싱 대비 손실(LVR)을 제거하는 자동화된 자산 관리 시스템입니다. ERC-4626 토큰화된 볼트를 활용하여 예금을 집계하고 슬리피지와 MEV를 최소화하며, 원활한 온체인 수익 창출을 위한 전문가가 선별한 전략을 제공합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 희박하거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약 (Security Assessment Summary)

_검토 커밋 해시_ - [b791eb90714b724c005ffb10db28df8b2b837c4f](https://github.com/Storm-Labs-Inc/cove-contracts-core/tree/b791eb90714b724c005ffb10db28df8b2b837c4f)

_수정 검토 커밋 해시_ - [c0011630afa02078cc6c2bbe48b5c46e69769f64](https://github.com/Storm-Labs-Inc/cove-contracts-core/tree/c0011630afa02078cc6c2bbe48b5c46e69769f64)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `AssetRegistry`
- `BasketManager`
- `BasketToken`
- `FeeCollector`
- `BasketManagerUtils`
- `AnchoredOracle`
- `ChainedERC4626Oracle`
- `CurveEMAOracleUnderlying`
- `ERC4626Oracle`
- `ManagedWeightStrategy`
- `StrategyRegistry`
- `CoWSwapAdapter`
- `CoWSwapClone`
- `BasketManagerStorage`

# 발견 사항 (Findings)

# [L-01] `ERC4626Oracle` 및 `ChainedERC4626Oracle`은 가격 조작에 취약함

코드는 `convertToAssets()` 및 `convertToShares()`를 사용하여 토큰 변환 금액을 계산합니다. 문제는 ERC4626 볼트의 주가(share price)가 기부 공격(donation attacks)에 의해 조작될 수 있으며, 그 결과 공격자가 가격 조작 공격을 수행할 수 있다는 것입니다. 공격을 수행하는 것은 비용이 많이 들며, 공격은 ERC4626을 담보로 사용하는 것과 같은 특수한 상황에서만 수익성이 있습니다.

# [L-02] `setManagementFee()`에서 관리 수수료가 적시에 업데이트되지 않음

Cove에서 관리 수수료는 `currentTotalSupply`, `feeBps` 및 `timeSinceLastHarvest`를 통해 계산됩니다. `setManagementFee` 함수에서 관리 수수료를 변경하지만 이전 시간대의 관리 수수료는 적립하지 않습니다. 이로 인해 잘못된 관리 수수료 계산이 발생합니다.

```solidity
    function setManagementFee(address basket, uint16 managementFee_) external onlyRole(_TIMELOCK_ROLE) {
        if (managementFee_ > _MAX_MANAGEMENT_FEE) {
            revert InvalidManagementFee();
        }

        if (basket != address(0)) {
            uint256 indexPlusOne = _bmStorage.basketTokenToIndexPlusOne[basket];
            if (indexPlusOne == 0) {
                revert BasketTokenNotFound();
            }
            if ((_bmStorage.rebalanceStatus.basketMask & (1 << indexPlusOne - 1)) != 0) {
                revert MustWaitForRebalanceToComplete();
            }
        }
        emit ManagementFeeSet(basket, _bmStorage.managementFees[basket], managementFee_);
        _bmStorage.managementFees[basket] = managementFee_;
    }
```

```solidity
    function _harvestManagementFee(uint16 feeBps, address feeCollector) internal {
        uint256 fee = FixedPointMathLib.fullMulDiv(
            currentTotalSupply,
            feeBps * timeSinceLastHarvest,
            ((_MANAGEMENT_FEE_DECIMALS - feeBps) * uint256(365 days))
        );
    }
```

권장 사항:
관리 수수료를 업데이트하기 전에 관리 수수료를 수확하십시오.

# [L-03] `completeRebalance()`에서 수수료 업데이트 누락

Cove에서는 바구니의 자산 가중치를 주기적으로 리밸런싱합니다. 리밸런싱 프로세스에서 아래 4단계를 트리거할 수 있습니다:

1. 타임스탬프 X에 리밸런스 제안.
2. 타임스탬프 X + 1000에 스왑 제안 및 내부 거래 완료.
3. 타임스탬프 X + 2000에 스왑 실행.
4. 타임스탬프 X + 3000에 리밸런스 완료. 주의: 누구나 이 함수를 트리거할 수 있습니다.

1단계에서는 `timestamp X` 이전에 관리 수수료를 적립하기 위해 `_harvestManagementFee`를 트리거한다는 것을 알 수 있습니다. 그러나 `completeRebalance` 함수에서는 타임스탬프 X와 타임스탬프 X + 3000 사이의 관리 수수료를 적립하지 않습니다.

`completeRebalance` 함수에는 `block.timestamp - self.rebalanceStatus.timestamp < self.stepDelay`라는 확인이 하나 있습니다. 기본 `stepDelay`는 `15분`입니다. 최대값은 `1시간`입니다. 즉, 리밸런싱 프로세스를 완료하기 전에 약 15분의 관리 수수료 적립을 놓칠 수 있습니다.

영향:

1. 리밸런스를 완료하려고 할 때 주가에 따라 보류 중인 상환 지분의 자산 금액을 계산합니다. 마지막 시간대의 관리 수수료를 획득하지 못했기 때문에 `totalSupply`는 예상 값보다 작을 것입니다. (관리 수수료를 적립할 때 수수료 수취인을 위해 일부 주식을 발행합니다). 상환자는 마지막 시간대 수수료 지불을 피할 수 있습니다.
2. 다음에 관리 수수료를 적립하려고 할 때 타임스탬프 X와 타임스탬프 X + 3000 사이의 관리 수수료를 다시 계산합니다. 그리고 최신 `totalSupply`를 사용합니다. 4단계에서 큰 보류 중인 상환이 있는 경우 현재 `totalSupply`는 타임스탬프 X와 타임스탬프 X + 3000 사이의 관리 수수료를 계산하는 데 사용해야 하는 실제 총 공급량보다 작을 것입니다. 프로토콜은 예상 관리 수수료의 일부를 놓치게 됩니다.

```solidity
    function proposeRebalance(BasketManagerStorage storage self, address[] calldata baskets) external {
        (uint256 pendingDeposits, uint256 pendingRedeems) =
            BasketToken(basket).prepareForRebalance(self.managementFees[basket], feeCollector);
    }
    function prepareForRebalance(
        uint16 feeBps,
        address feeCollector
    ) {
        _harvestManagementFee(feeBps, feeCollector);
    }
    function completeRebalance(
        BasketManagerStorage storage self,
        ExternalTrade[] calldata externalTrades,
        address[] calldata baskets,
        uint64[][] calldata basketTargetWeights,
        address[][] calldata basketAssets
    )
        if (block.timestamp - self.rebalanceStatus.timestamp < self.stepDelay) {
            revert TooEarlyToCompleteRebalance();
        }
    }
```

```solidity
    function _finalizeRebalance(
        BasketManagerStorage storage self,
        EulerRouter eulerRouter,
        address[] calldata baskets,
        address[][] calldata basketAssets
    ) {
        if (pendingRedeems > 0) {
            uint256 withdrawAmount = eulerRouter.getQuote(
                FixedPointMathLib.fullMulDiv(basketValue, pendingRedeems, BasketToken(basket).totalSupply()),
                // _USD_ISO_4217_CODE --> 840
                _USD_ISO_4217_CODE,
                baseAsset
            );
    }
    }
```

`completeRebalance` 함수에서 관리 수수료를 적립하십시오.
