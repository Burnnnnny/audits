# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Blueberryfi/blueberry-v2-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Blueberry 소개

Blueberry는 사용자가 최대 25배의 레버리지로 자산을 대출하고 차입할 수 있게 하여 DeFi의 주요 중개업체 역할을 하여 다양한 거래 전략을 실행할 수 있도록 하는 레버리지 대출 프로토콜입니다. Blueberry의 두 번째 버전은 디자인 개선 및 분산형 레버리지 거래를 더 쉽게 접근할 수 있도록 확장성에 중점을 둔 완전한 재작성입니다. 이 감사는 Blueberry 프로토콜의 V2에 중점을 두었습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [ac6bbab032bd36823e8762ee6ecbdbec6488a610](https://github.com/Blueberryfi/blueberry-v2-contracts/tree/ac6bbab032bd36823e8762ee6ecbdbec6488a610)

_수정 검토 커밋 해시_ - [b310ae785521695bbc6ea952655e28cd0eb5a046](https://github.com/Blueberryfi/blueberry-v2-contracts/tree/b310ae785521695bbc6ea952655e28cd0eb5a046)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `EscrowAssetStorage`
- `HyperVaultRouter`
- `HyperliquidEscrow`
- `L1EscrowActions`
- `WrappedVaultShare`

# 발견 사항

# [H-01] `Escrow.tvl()`이 기내 USDC 금액을 추가하지 않음

## 심각도

**영향:** 중간  

**가능성:** 높음

## 설명

`HyperliquidEscrow` 계약의 `tvl()` 함수는 총 잠금 가치(TVL)를 계산할 때 현재 브리지 중인(기내) USDC 금액을 포함하지 않습니다. 다른 자산의 경우 동일한 블록에서 브리지가 발생하면 해당 금액이 TVL에 추가됩니다. 그러나 USDC의 경우 이 단계는 건너뜁니다. 즉, USDC가 브리지 중일 때 보고된 TVL은 실제보다 낮습니다.

```solidity
    function tvl() external view override returns (uint256 tvl_) {
            --Snipped--
            if (assetIndex == USDC_SPOT_INDEX) {
                tvl_ += IERC20(assetAddr).balanceOf(address(this)) * evmScaling;
            } else {
                uint256 rate = getRate(details.spotMarket, details.szDecimals);
                uint256 balance = IERC20(assetAddr).balanceOf(address(this)) * evmScaling;

                uint256 lastBridgeToL1Block = $$.inFlightBridge[assetIndex].blockNumber;
                // If we are still in the L1 bridge period (same EVM block as last bridge action took place), we need to add the in-flight bridge amounts
                if (block.number == lastBridgeToL1Block) {
                    balance += $$.inFlightBridge[assetIndex].amount * evmScaling;
                }

                balance += _spotAssetBalance(uint64(assetIndex));
                tvl_ += balance.mulWadDown(rate);
            }
        }
    }
```
결과적으로 USDC 브리지가 발생하는 동일한 블록에서 입금하거나 상환하는 사용자는 잘못된 TVL과 주가를 보게 됩니다. 이로 인해 사용자가 자금을 잃거나 부당하게 더 많이 받을 수 있습니다.

### POC 1

이 문제에 대한 테스트 유닛입니다. `test/hyperliquid` 폴더에 있어야 합니다. 
[test_tvl.txt](https://github.com/user-attachments/files/20303490/test_tvl.txt)

### POC 2

PoC는 [여기](https://gist.github.com/zarkk01/dcf5c8f2147c74186c0b85492d7c687b)에서 찾을 수 있으며 `forge test --mt testTVLIgnoresUSDCBridged`를 실행하십시오.

## 권장 사항

다른 자산과 마찬가지로 현재 블록에서 브리지 중인 경우 기내 USDC 금액도 TVL에 추가되도록 로직을 수정하십시오. 이렇게 하면 TVL 계산이 정확하고 일관되게 됩니다.

# [L-01] 지원되지 않는 자산 제거 시 USDC가 잘못 제거될 수 있음

`removeAsset()` 함수는 `$.assetIndexes[asset]`를 사용하여 자산 인덱스를 가져옵니다. 자산 주소가 실제로 지원되지 않는 경우 매핑은 기본적으로 `0`을 반환합니다. 이는 USDC의 인덱스입니다. 이로 인해 지원되지 않거나 잘못된 주소가 함수에 전달될 때 실수로 USDC를 제거할 수 있습니다.

```solidity
    function removeAsset(address asset) external onlyOwner {
        V1Storage storage $ = _getV1Storage();
        uint64 assetIndex_ = $.assetIndexes[asset];

        // Withdraw asset cannot be set to address(0) once it is set
        require(asset != $.withdrawAsset, Errors.INVALID_OPERATION());

        if (assetIndex_ == USDC_EVM_SPOT_INDEX) $.usdcSupported = false;
        delete $.assetIndexes[asset];
        delete $.assetDetails[assetIndex_];
        $.supportedAssets.remove(assetIndex_);

        uint256 len = $.escrows.length;
        for (uint256 i = 0; i < len; ++i) {
            HyperliquidEscrow($.escrows[i]).removeAsset(assetIndex_);
        }

        emit AssetRemoved(assetIndex_);
    }
```

이와 동일한 패턴이 `setWithdrawAsset()` 함수에도 존재합니다. 자산이 존재하는지 먼저 확인하지 않고 `$.assetIndexes[asset]`를 사용하여 자산 인덱스를 가져옵니다. 지원되지 않는 자산이 제공되면 함수는 이를 인덱스 0으로 처리하고 실수로 USDC를 인출 자산으로 다시 설정하도록 허용합니다.

권장 사항:
매핑에서 반환된 인덱스를 사용하기 전에 자산이 실제로 지원되는지 확인하는 유효성 검사 단계를 추가하십시오. 구체적으로:
- `$.assetDetails[assetIndex_].evmContract == asset`인지 확인하여 자산이 등록되었는지 확인하십시오.
- 자산이 제대로 매핑되지 않은 경우 작업을 거부하십시오.

이 패턴은 의도하지 않은 동작과 우발적인 USDC 제거 또는 오용을 방지하기 위해 `removeAsset()` 및 `setWithdrawAsset()` **모두**에서 수정되어야 합니다.

# [L-02] `minDepositValue`는 대규모 예금에 대해 부적절한 비트 크기를 사용

`V1Storage` 구조체의 `minDepositValue` 필드는 `uint64`로 정의되어 있으며 최대값은 약 `18.44e18`입니다. 이는 최소한으로 20e18 USD 이상의 대규모 예금 금액을 처리해야 하는 라우터에 불충분할 수 있습니다. 어쨌든 변수의 이러한 제한은 문제가 될 수 있습니다.

```solidity
    struct V1Storage {
        /// @notice The last time the fees were accrued
        uint64 lastFeeCollectionTimestamp;
        /// @notice The management fee in basis points
        uint64 managementFeeBps;
        /// @notice The minimum value in USD that can be deposited into the vault scaled to 1e18
@>        uint64 minDepositValue;
```

`minDepositValue`에 대해 더 큰 `uint` 유형을 사용하는 것을 고려하십시오.

# [L-03] `maxRedeemable`은 잘못된 라운딩 방법 사용

`HyperVaultRouter.sol`의 `maxRedeemable` 함수는 최대 상환 가능 주식을 계산하기 위해 `mulDivDown`을 사용하며, 이는 결과를 내림합니다. 그러나 이 맥락에서는 사용자가 동일한 금액의 토큰에 대해 더 많은 주식을 상환할 수 있도록 하므로 올림이 더 적절할 것입니다.

```solidity
    function maxRedeemable() external view override returns (uint256) {
        // ...

        // Calculate the max redeemable amount based on the USD value of the withdraw asset
        uint256 usdValue = maxWithdraw.mulWadDown(rate * scaler);
@>        return usdValue.mulDivDown(_shareSupply(), tvl());
    }
```

프로토콜에 유리하게 동일한 자산 금액에 대해 더 많은 주식을 상환할 수 있도록 올림하는 것을 고려하십시오.

# [L-04] `maxRedeemable`은 미징수 수수료로 인해 주식을 과소 평가함

`HyperVaultRouter.sol`의 `maxRedeemable` 함수는 현재 TVL 및 주식 공급을 기반으로 상환할 수 있는 최대 주식 수를 계산하지만 미징수 관리 수수료를 고려하지 않습니다. 이로 인해 실제 상환 가능 주식이 과소 평가됩니다.

```solidity
    function maxRedeemable() external view override returns (uint256) {

        // Calculate the max redeemable amount based on the USD value of the withdraw asset
        uint256 usdValue = maxWithdraw.mulWadDown(rate * scaler);
@>        return usdValue.mulDivDown(_shareSupply(), tvl()); // # this `_shareSupply()` is outdated
    }
```

이 문제를 해결하려면 `maxRedeemable`() 내에서 `_takeFee()`를 호출하는 것을 고려하십시오.

# [L-05] 수수료 계산에 새 자산의 과거 잔액이 포함됨

`HyperVaultRouter.sol`의 `addAsset` 함수에는 에스크로에 이미 잔액이 있는 라우터에 새 자산을 추가할 때 수수료 계산에 문제가 있어 이 새 자산에 대해 이미 과도하게 청구하게 됩니다. 문제는 다음과 같은 이유로 발생합니다.
1. 새 자산이 추가되면 잔액이 즉시 TVL 계산에 포함됩니다.
2. 수수료 계산은 lastFeeCollectionTimestamp를 사용하여 수수료 발생 기간을 결정합니다.
3. 에스크로가 나중에 추가되는 지원되지 않는 토큰의 잔액을 이미 보유하고 있는 경우 프로토콜은 자산이 실제로 추가된 시점이 아니라 `lastFeeCollectionTimestamp` 이후 잔액이 존재했던 것처럼 수수료를 계산합니다.

```solidity
    function addAsset(address assetAddr, uint32 assetIndex_, uint32 spotMarket) external onlyOwner {
        // ...

        // Iterate through all the escrows to add supported assets
        uint256 len = $.escrows.length;
        for (uint256 i = 0; i < len; ++i) {
            HyperliquidEscrow($.escrows[i]).addAsset(assetIndex_, details);
        }

        emit AssetAdded(assetIndex_, details);
    }
```

이 문제를 해결하려면 `addAsset()` 함수 내에서 `_takeFees()`를 호출하는 것을 고려하십시오.

# [L-06] 인출 변경 후 슬리피지 보호가 잘못된 자산을 참조할 수 있음

`HyperVaultRouter.sol`의 `redeem` 함수에는 사용자가 거래를 제출한 시점과 실행되는 시점 사이에 인출 자산이 변경될 때 슬리피지 보호에 문제가 있습니다. 이는 다음과 같은 이유로 발생합니다.
1. 사용자는 `withdrawAsset`이 `X`임을 보고 상환을 위한 tx를 제출하고 있습니다.
2. 그러나 `withdrawAsset`의 변경은 tx가 실행되기 전에 발생합니다.
3. 따라서 이제 `redeem`은 다른 토큰 단위를 참조하는 `minOut`과 함께 전달됩니다.

```solidity
    function redeem(uint256 shares, uint256 minOut) external override nonReentrant returns (uint256 amount) {
         // ...

        // Convert the USD amount to withdraw to the withdraw asset amount
        HyperliquidEscrow escrow = HyperliquidEscrow($.escrows[depositEscrowIndex()]);
        uint256 scaler = 10 ** (18 - details.evmDecimals);
        uint256 rate = _getRate(address(escrow), assetIndex_, details);
        amount = usdAmount.divWadDown(rate * scaler);
        require(amount > 0, Errors.AMOUNT_ZERO());
        require(amount >= minOut, Errors.SLIPPAGE_TOO_HIGH());

        // ...
    }
```

이것의 영향은 예를 들어 누군가가 `10e6` USDC로 슬리피지를 원한다고 결정하지만 `withdrawAsset`이 변경되면 거래가 실행되고 슬리피지는 `10e6` PURR가 됩니다.

이 문제를 해결하려면 `redeem`에서 `withdrawAsset`을 매개변수로 사용하는 것을 고려하십시오.

# [L-07] `bridgeToL1`의 데드 코드

`bridgeToL1`은 브리지된 자산을 추적합니다.

```solidity
        uint256 evmBlock = block.number;
        uint64 lastBridgeToL1Block = $$.inFlightBridge[assetIndex].blockNumber;


        if (evmBlock != lastBridgeToL1Block) {
            $$.inFlightBridge[assetIndex] = InflightBridge({blockNumber: uint64(evmBlock), amount: amountAdjusted});
        } else {
            $$.inFlightBridge[assetIndex].amount += amountAdjusted;
        }
```

함수에 `singleActionBlock` 수정자가 있으므로 마지막 줄은 `bridgeToL1`이 동일한 EVM 블록 내에서 두 번 호출되는 경우에만 도달할 수 있으며 이는 불가능합니다.

# [L-08] 자산 제거 시 기내 브리지 금액 무시

`HyperVaultRouter.sol`의 `removeAsset` 함수는 현재 HyperEVM L2에서 HyperCore L1으로 브리지되는 자산을 설명하지 못합니다. 이는 다음과 같은 이유로 문제가 됩니다.
1. 프로토콜은 V1L1EscrowActionsStorage 구조체에서 기내 브리지 금액을 추적합니다.
```solidity
    struct InflightBridge {
        /// @notice The evm block number that the asset was sent to L1
        uint64 blockNumber;
        /// @notice The amount of the asset that is in-flight to L1
        uint256 amount;
    }
```
2. 그러나 제거 시 에스크로의 잔액 또는 현물 잔액이 0인지 확인하며, 현물 잔액은 다음 블록에서 현물 잔액에 포함될 기내 금액을 고려하지 않습니다.
```solidity
    function _spotAssetBalance(uint64 token) internal view override returns (uint256) {
        V1AssetStorage storage $ = _getV1AssetStorage();
        (bool success, bytes memory result) =
            SPOT_BALANCE_PRECOMPILE_ADDRESS.staticcall(abi.encode(address(this), token));
        require(success, Errors.PRECOMPILE_CALL_FAILED());
        SpotBalance memory balance = abi.decode(result, (SpotBalance));

        if (token == USDC_SPOT_INDEX) {
            return balance.total * USDC_SPOT_SCALING;
        }

        uint256 scaler = 10 ** (18 - $.assetDetails[token].weiDecimals);
        return balance.total * scaler;
    }
```
이러한 방식으로 `LIQUIDITY_ADMIN`이 에스크로 전액의 브리지를 만들었을 가능성이 있으며(따라서 기내 구조체를 제외한 모든 잔액은 0이 됨) 그 시점에서 `removeAsset`이 트리거되면 되돌려지지 않고 자산이 잠기게 됩니다.

[여기](https://gist.github.com/zarkk01/c2be0a49cd45229a7dc6f2a3c78f0519)에서 PoC를 확인하고 `forge test --mt testRemoveAssetDoesntSeeSpot`을 실행하십시오.

권장 사항:
기내 금액도 0인지 확인하고 고려하는 것을 고려하십시오.

