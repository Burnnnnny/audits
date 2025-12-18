# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**tapioca-periph, tapioca-yieldbox-strategies, Tapioca-bar** 및 **PermitC** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# TapiocaDAO에 대하여

TapiocaDAO는 BoringCrypto의 Yieldbox를 포함하는 크로스체인 머니 마켓입니다. Yieldbox는 유휴 자금을 설명하는 데 사용되는 지분(share)을 통해 사용자의 예금을 추적하며, 동시에 동일한 자금이 전략에 적용됩니다. Tapioca Periph 스마트 컨트랙트는 PermitC를 사용합니다. PermitC는 승인 프로세스를 추상화하고 만료 기능을 추가한 ERC20, ERC721 및 ERC1155를 위한 확장 승인 시스템입니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| --- | --- | --- | --- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 비교적 가능성이 높습니다.

- 낮음 (Low) - 너무 많거나 비현실적인 가정을 필요로 하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준별 요구 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (이미 배포되지 않은 경우 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_:

- [f968050e8468d712b93fdd37382e24f302b626dd](https://github.com/Tapioca-DAO/tapioca-periph/tree/f968050e8468d712b93fdd37382e24f302b626dd)
- [c431dc5e80690c1d8c3727f5992d519df3d38254](https://github.com/limitbreakinc/PermitC/tree/c431dc5e80690c1d8c3727f5992d519df3d38254)
- [d91e5c7472ed74f8d51d400a893df1b4b3c087b7](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies/tree/d91e5c7472ed74f8d51d400a893df1b4b3c087b7)
- [d1377d5bbd85a781398eadc6e0c2f2b2594e3688](https://github.com/Tapioca-DAO/Tapioca-bar/tree/d1377d5bbd85a781398eadc6e0c2f2b2594e3688)

_수정 검토 커밋 해시_:

- [bc07c576f6da578f9ba336f522c6577ffeb70914](https://github.com/Tapioca-DAO/tapioca-periph/tree/bc07c576f6da578f9ba336f522c6577ffeb70914)
- [c431dc5e80690c1d8c3727f5992d519df3d38254](https://github.com/limitbreakinc/PermitC/tree/c431dc5e80690c1d8c3727f5992d519df3d38254)
- [38752fc217fe83fafa2d648679c698b9554731bd](https://github.com/Tapioca-DAO/tapioca-yieldbox-strategies/tree/38752fc217fe83fafa2d648679c698b9554731bd)
- [c2031ac2e2667ac8f9ac48eaedae3dd52abef559](https://github.com/Tapioca-DAO/Tapioca-bar/tree/c2031ac2e2667ac8f9ac48eaedae3dd52abef559)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

`tapioca-periph`의 경우:

- `pearImit/PearImit`
- `pearImit/PearImitHandler`
- `pearImit/PearImitHash`

`tapioca-yieldbox-strategies`의 경우:

- `glp/GlpStrategy`
- `sDai/sDaiStrategy`
- `feeCollector`

`Tapioca-bar`의 경우:

- `markets/leverage/AssetToSGLPLeverageExecutor`
- `markets/leverage/AssetTotsDaiLeverageExecutor`
- `markets/leverage/BaseLeverageExecutor`
- `markets/leverage/SimpleLeverageExecutor`

`PermitC`의 경우:

- `PermitC`
- `Errors`
- `Datatypes`
- `Constants`
- `libraries/PermitHash`
- `interfaces/IPermitC`

# 발견 사항

# [C-01] 공격자가 `sDaiStrategy` 수수료를 영구히 징수하지 못하게 할 수 있음

## 심각도

**영향:** 높음 (High), 전략에 의해 0원의 수수료가 포착(capture)되므로

**가능성:** 높음 (High), 단일 공격자에 의해 이 공격이 쉽게 수행되고 지속될 수 있으므로

## 설명

`sDaiStrategy`에서 사용자가 자산을 전략에 예치할 때, 소유자가 설정한 `depositThreshold`에 도달할 때만 예치된 자산이 기본 풀에 배치됩니다:

```solidity
function _deposited(uint256 amount) internal override nonReentrant {
        if (paused) revert Paused();

        // Assume that YieldBox already transferred the tokens to this address
        uint256 queued = IERC20(contractAddress).balanceOf(address(this));
        totalActiveDeposits += queued; // Update total deposits

        if (queued >= depositThreshold) {
            ITDai(contractAddress).unwrap(address(this), queued);
            dai.approve(address(sDai), queued);
            sDai.deposit(queued, address(this));
            emit AmountDeposited(queued);
            return;
        }
        emit AmountQueued(amount);
    }
```

이 흐름 동안 `totalActiveDeposits` 변수는 예치된 자산의 계약 잔액만큼 증가합니다.

그런 다음 인출 시 이 변수는 보류 중인 수수료를 계산하는 데 사용됩니다:

```solidity
// Compute the fees
        {
            uint256 _totalActiveDeposits = totalActiveDeposits; // Cache total deposits
            (uint256 fees, uint256 accumulatedTokens) = _computePendingFees(_totalActiveDeposits, maxWithdraw); // Compute pending fees
            if (fees > 0) {
                feesPending += fees; // Update pending fees
            }

            // Act as an invariant, totalActiveDeposits should never be lower than the amount to withdraw from the pool
            totalActiveDeposits = _totalActiveDeposits + accumulatedTokens - amount; // Update total deposits
        }
```

이 로직은 전략에 예치된 동일한 자본에 대해 수수료를 여러 번 청구하지 않도록 보장하기 때문에 중요합니다. `_computePendingFees` 메서드는 `maxWithdraw`가 `_totalActiveDeposits`보다 클 때(즉, 전략이 수익을 창출했으며, 그 중 수수료 수령자가 일정 비율을 받을 자격이 있는 경우)에만 수수료를 청구합니다:

```solidity
function _computePendingFees(uint256 _totalDeposited, uint256 _amountInPool)
        internal
        view
        returns (uint256 result, uint256 accumulated)
    {
        if (_amountInPool > _totalDeposited) {
            accumulated = _amountInPool - _totalDeposited; // Get the occurred gains amount
            (, result) = _processFees(accumulated); // Process fees
        }
    }
```

따라서 `totalActiveDeposits`는 `maxWithdraw`보다 커서는 안 되며, 그렇지 않으면 수수료가 전혀 청구되지 않습니다.

공격자는 다음과 같이 이 상황을 강제로 발생시킬 수 있습니다:

1. 공격자가 전략의 대기 중인 자산 잔액을 `depositThreshold` 바로 아래로 만드는 예치를 수행합니다.
2. 공격자는 이제 자산 잔액을 `depositThreshold`에 가깝지만 그 아래로 유지하는 여러 번의 소액 예치를 수행할 수 있습니다.

이는 다음 로직 때문에 문제입니다:

```
uint256 queued = IERC20(contractAddress).balanceOf(address(this));
totalActiveDeposits += queued; // Update total deposits
```

공격자가 소액 예치를 할 때마다 `totalActiveDeposits` 변수는 `~depositThreshold`만큼 증가하지만, 기본 sDai 풀에는 예치가 이루어지지 않습니다. 결과적으로 `totalActiveDeposits` 변수는 이제 풀의 `maxWithdraw`보다 훨씬 커지며 생성된 수익에서 수수료가 징수되지 않습니다. 이 공격은 누적된 보상이 부풀려진 활성 예치 회계를 따라잡으면 나중에 다시 수행될 수 있습니다.

## 권고 사항

`_deposited` 메서드에서 `totalActiveDeposits` 변수는 대기 중인 예치금이 실제로 `depositThreshold`보다 크고 sDai 풀에 예치된 경우에만 증가해야 합니다.

# [C-02] 회계된 잔액에 미청구 보상이 포함되지 않아 공격자가 `GlpStrategy`에서 미청구 보상을 훔칠 수 있음

## 심각도

**영향:** 높음 (High), 사용자가 미청구 보상을 훔칠 수 있으므로

**가능성:** 높음 (High), 미청구 보상이 있기만 하면 누구나 수행할 수 있으므로. 미청구 보상의 수가 많고 사용자의 예치금이 클수록 영향이 큽니다. 예치 빈도가 낮은 전략일수록 이 공격을 받을 가능성이 높습니다.

## 설명

사용자가 YieldBox에 예치한 자산을 인출하고자 할 때 YieldBox 계약에서 다음 로직이 사용됩니다:

```solidity
        uint256 totalAmount = _tokenBalanceOf(asset);
        if (share == 0) {
            // value of the share paid could be lower than the amount paid due to rounding, in that case, add a share (Always round up)
            share = amount._toShares(totalSupply[assetId], totalAmount, true);
        } else {
            // amount may be lower than the value of share due to rounding, that's ok
            amount = share._toAmount(totalSupply[assetId], totalAmount, false);
        }

        _burn(from, assetId, share);

        // Interactions
        asset.strategy.withdraw(to, amount);

```

여기서 사용자가 소유한 지분을 전략 내 자산의 지분으로 매핑한 다음, 지분을 소각하고 전략에서 해당 자산 수를 인출합니다. `deposit` 흐름도 유사한 패턴을 사용합니다. `_tokenBalanceOf` 메서드는 전략을 호출하는데, `GlpStrategy`의 경우 다음과 같습니다:

```solidity
function _currentBalance() internal view override returns (uint256 amount) {
        amount = sGLP.balanceOf(address(this));
        amount += pendingRewards();
    }

```

따라서 전략의 자산 잔액은 전략의 GLP 잔액과 전략의 대기 중인 보상의 합계이며, 대기 중인 WETH 보상은 현재 환율을 기준으로 GLP로 교환됩니다.

그러나 이러한 "대기 중인 보상"은 실제로 기술적으로 대기 중인 보상이 아닙니다. 이미 GMX에서 수확(harvest)되었지만 아직 GLP로 변환되지 않았기 때문입니다. 결과적으로 YieldBox의 자산 <-> 지분 변환 로직은 아직 GMX에 잠겨 있어 청구 대기 중인 실제 대기 보상을 고려하지 않습니다. 이는 공격자가 다음과 같은 방법으로 자신을 위해 비례하지 않은 양의 미청구 보상을 훔칠 수 있게 하므로 문제입니다:

1. 미청구 보상이 있을 때 자산을 예치합니다. 미청구 보상은 고려되지 않으므로 그들은 과도한 지분을 얻게 됩니다. 예치 과정에서 미청구 보상은 수확되므로 회계된 잔액은 증가하지만, 이는 지분이 발행된 후입니다.
2. 이제 공격자가 자신의 지분을 인출하면 이전에 청구되지 않은 보상의 일부를 얻습니다. 더 많은 부분의 미청구 보상을 얻으려면 공격자는 전략의 기존 자산 대비 가능한 한 큰 예치를 해야 합니다.

이 모든 것이 원자적으로 발생하므로 공격자는 플래시론을 사용하여 전략에서 미청구 보상의 상당 부분을 훔칠 수 있습니다.

## 권고 사항

`_currentBalance` 메서드는 GMX에 여전히 잠겨 있는 실제 대기 보상을 포함하도록 업데이트되어야 합니다. 이를 위해서는 GMX 계약의 적절한 메서드를 쿼리해야 합니다.

# [H-01] 비상 인출이 다시 예치를 허용함

## 심각도

**영향:** 높음 (High), 비상 인출 후 토큰을 다시 예치할 수 있으며 대량 인출이 완료되지 않을 수 있습니다

**가능성:** 중간 (Medium), 비상 인출은 중요한 관리자 기능이지만 여전히 일반적인 작업은 아닙니다

## 설명

`sDaiStrategy.emergencyWithdraw()`의 마지막 단계는 tDai로의 변환과 일시 중지(pause) 설정입니다.

```
    function emergencyWithdraw() external onlyOwner {
        paused = true; // Pause the strategy

        // Withdraw from the pool, convert to Dai and wrap it into tDai
        uint256 maxWithdraw = sDai.maxWithdraw(address(this));
        sDai.withdraw(maxWithdraw, address(this), address(this));
        dai.approve(contractAddress, maxWithdraw);
        ITDai(contractAddress).wrap(address(this), address(this), maxWithdraw);
    }
```

즉, 사용자가 tDai를 인출할 수 있도록 소유자가 일시 중지를 비활성화해야 합니다(그렇지 않으면 tDai가 전략에 갇히게 됩니다).
그러나 소유자가 일시 중지를 비활성화하면 다시 예치도 활성화됩니다. 게다가 첫 번째 예치는 전략이 모든 tDai 잔액을 다시 예치하도록 트리거하여 `emergencyWithdraw()`를 다시 요구하게 됩니다.

## 권고 사항

예치와 인출에 대해 서로 다른 일시 중지 유형을 별도로 관리하거나, `emergencyWithdraw()`가 호출된 후 예치를 비활성화하고 인출은 활성화된 상태로 두는 것을 고려하십시오.
또한 가장 간단한 해결책 중 하나는 인출에 대한 일시 중지를 제거하는 것입니다.

# [H-02] `BaseLeverageExecutor`에 WETH로부터 ETH 인출을 수신할 `receive()` 메서드가 없음

## 심각도

**영향:** 중간 (Medium), 출력 토큰이 ETH용 tOFT일 때 레버리지 실행기가 중단되므로

**가능성:** 높음 (High), 이 출력 토큰에 대해 항상 발생하므로

## 설명

`BaseLeverageExecutor.sol` 계약은 다른 모든 레버리지 실행기 계약이 상속하는 기본 계약입니다. 여기에는 모든 레버리지 실행기에 공통적인 토큰 스왑 및 tOFT 랩핑(wrapping)과 언랩핑(unwrapping)을 처리하는 로직이 포함되어 있습니다.

레버리지 작업의 최종 토큰이 ETH tOFT인 경우, 스왑의 WETH는 ETH로 변환되고 ETH는 tOFT로 랩핑됩니다:

```solidity
function _handleToftWrapToSender(bool sendBack, address tokenOut, uint256 amountOut) internal {
        address toftErc20 = ITOFT(tokenOut).erc20();
        address wrapsTo = sendBack == true ? msg.sender : address(this);

        if (toftErc20 == address(0)) {
            // If the tOFT is for ETH, withdraw from WETH and wrap it.
            weth.withdraw(amountOut);
            ITOFT(tokenOut).wrap{value: amountOut}(address(this), wrapsTo, amountOut);
        } else {
            // If the tOFT is for an ERC20, wrap it.
            toftErc20.safeApprove(tokenOut, amountOut);
            ITOFT(tokenOut).wrap(address(this), wrapsTo, amountOut);
            toftErc20.safeApprove(tokenOut, 0);
        }
    }
```

여기서 문제는 `weth.withdraw` 호출이 우리가 태우고(burn) 싶은 WETH 양에 해당하는 ETH 양과 함께 이 계약에 대한 콜백을 포함한다는 것입니다. 이 ETH를 받으려면 `fallback` 또는 `receive` 메서드가 필요하지만 이 계약에는 둘 다 없습니다. 결과적으로 `withdraw` 호출은 되돌려지며(revert), 따라서 ETH tOFT로 끝나는 모든 레버리지 호출은 항상 되돌려집니다.

이는 `AssetTotsDaiLeverageExecutor`와 `AssetToSGLPLeverageExecutor` 모두에 적용됩니다. `assetAddress`가 ETH용 tOFT인 `getAsset`에 대한 모든 호출은 항상 실패합니다.

## 권고 사항

다음 스니펫을 `BaseLeverageExecutor.sol` 계약에 추가해야 합니다:

```solidity
receive() external payable {}
```

`SimpleLeverageExecutor.sol` 계약( `BaseLeverageExecutor`를 상속하므로)에서는 이를 제거할 수 있습니다.

# [H-03] AssetTotsDaiLeverageExecutor.getAsset()은 `redeem(convertToShares(collateralAmountIn))` 대신 `redeem(collateralAmountIn)`을 사용해야 함

## 심각도

**영향:** 중간 (Medium), 사용자가 sDAI를 변환할 때 더 적은 DAI를 얻게 됨.

**가능성:** 높음 (High), `getAsset()`이 호출될 때마다 발생함.

## 설명

sDai는 ERC4626 계약입니다. Dai는 기본 자산이고 sDai는 지분(share)입니다. 자산을 인출할 때 `withdraw()`가 호출되면 함수는 자산 가치를 받아 기본 자산의 양을 반환합니다. `redeem()`이 호출되면 함수는 지분 가치를 받아 기본 자산의 양을 반환합니다. 일반적으로 지분은 자산보다 가치가 더 큽니다. 예: 1 sDai : 1.05 Dai.

AssetTotsDaiLeverageExecutor.getAsset()에서 함수는 tsDai 언랩(unwrap) > sDai 인출 > Dai > USDO를 의도합니다. sDai를 인출할 때 `redeem(convertToShares())`를 호출합니다.

```
//unwrap tsDai
        ITOFT(collateralAddress).unwrap(address(this), collateralAmountIn);
        //redeem from sDai
>       uint256 obtainedDai = ISavingsDai(sDaiAddress).redeem(
>           ISavingsDai(sDaiAddress).convertToShares(collateralAmountIn), address(this), address(this)
        );
```

sDai는 이미 지분이므로 `convertToShares()`를 호출하면 의도한 것보다 적은 Dai를 반환합니다. `convertToShares()`는 자산 금액을 받아 지분 금액을 반환하기 때문입니다. 사용자는 더 적은 Dai를 돌려받고 남은 지분은 AssetTotsDaiLeverageExecutor 계약에 갇히게 됩니다.

## 권고 사항

`getAsset()`에서 `convertToShares()`를 호출하지 않을 것을 권장합니다.

```
- uint256 obtainedDai = ISavingsDai(sDaiAddress).redeem(
-             ISavingsDai(sDaiAddress).convertToShares(collateralAmountIn), address(this), address(this)
+ uint256 obtainedDai = ISavingsDai(sDaiAddress).redeem(collateralAmountIn, address(this), address(this));
```

# [M-01] sDaiStrategy.sol에서 수수료가 부적절하게 계산되어 후속 예치자가 예치금 전액을 인출할 수 없음

## 심각도

**영향:** 중간 (Medium), 일부 사용자는 예치금을 전액 돌려받지 못할 수 있음.

**가능성:** 높음 (High), `_withdraw()`가 호출될 때마다 발생함.

## 설명

sDaiStrategy.sol의 수수료는 Dai를 sDai에 예치하여 발생하는 수익을 대상으로 합니다.

```
> (uint256 fees, uint256 accumulatedTokens) = _computePendingFees(_totalActiveDeposits, maxWithdraw); // Compute pending fees
            if (fees > 0) {
                feesPending += fees; // Update pending fees
            }
```

프로토콜이 x sDai에 대해 1000 Dai를 예치하고 1년 후 x sDai에 대해 1010 Dai를 돌려받는다면 수익은 10 Dai이고 수수료가 10%로 설정되면 프로토콜은 수수료로 1 Dai를 얻습니다.
문제는 sDaiStrategy에 많은 예치자가 있지만 sDaiStrategy만 sDai 계약과 상호 작용하기 때문에 공제할 수수료를 계산하는 것이 매우 어렵다는 것입니다. 이 문제는 예제를 통해 더 잘 설명됩니다. 수수료가 10%로 설정되었다고 가정합니다.

예치자 A와 예치자 B 두 명이 있습니다. 예치자 A는 sDaiStrategy를 통해 9990 Dai를 예치하고 예치자 B는 sDaiStrategy를 통해 10 Dai를 예치합니다. 그런 다음 sDaiStrategy는 10000 Dai를 예치하고 x개의 sDai 지분을 돌려받습니다.

1년 후 수익률이 5%라고 가정하면 x개의 sDai 지분은 이제 10500 Dai로 상환될 수 있습니다. 예치자 A의 경우 10,489.5 Dai를 돌려받을 수 있고 예치자 B의 경우 10.5 Dai를 돌려받을 수 있습니다. 예치자 A는 ~10,489.5(5% 수익률) Dai를 인출하려고 합니다. sDaiStrategy는 수수료를 계산하며 수수료가 10%이므로 `feesPending` 변수는 50 Dai가 됩니다.

예치자 A가 금액을 인출하면 sDaiStrategy에는 10.5 Dai가 남습니다. 수수료를 인출하기에 충분한 Dai가 없으며 소유자가 수수료를 인출할 것이기 때문에 후속 예치자는 인출금을 돌려받을 수 없습니다.

예치자 A에게 수수료를 지불하게 하는 대신 예치자 A는 전액 + 수익을 인출하여 후속 예치자가 그를 대신해 수수료를 지불하게 할 수 있습니다.

요약하면 500 Dai 수익률로 발생해야 하는 일은 다음과 같습니다:

- 예치자 A는 10439.55 Dai를 돌려받습니다 (49.95 Dai는 수수료로 이동)
- 예치자 B는 10.45 Dai를 돌려받습니다 (0.05 Dai는 수수료로 이동)
- 누적 수수료 = 50
- 계약에 남은 토큰 = 0

대신 발생하는 일은 다음과 같습니다:

- 예치자 A는 10,489.5 Dai를 돌려받습니다 (0 Dai는 수수료로 이동)
- 예치자 B는 아마도 10.5 Dai를 돌려받습니다 (50 Dai는 수수료로 이동하지만 수수료를 먼저 가져가면 예치자 B는 아무것도 받지 못합니다)
- 누적 수수료 = 50
- 계약에 남은 토큰: 10.5

## 권고 사항

sDaiStrategy가 자체 회계 시스템을 갖추도록 하여 사용자가 예치금 + 수익을 인출할 때 전역 `maxWithdraw`를 사용하는 대신 수익에 직접 세금을 부과할 수 있도록 권장합니다.

# [M-02] `shouldBuyGLP`가 false인 경우 `GlpStrategy` 인출 실패 가능

## 심각도

**영향:** 중간 (Medium), `shouldBuyGLP`가 true로 설정될 때까지 인출이 실패할 수 있으므로

**가능성:** 중간 (Medium), `shouldBuyGLP`는 소유자가 false로 설정해야 하므로

## 설명

사용자가 YieldBox에 예치한 자산을 인출하고자 할 때 YieldBox 계약에서 다음 로직이 사용됩니다:

```solidity
        uint256 totalAmount = _tokenBalanceOf(asset);
        if (share == 0) {
            // value of the share paid could be lower than the amount paid due to rounding, in that case, add a share (Always round up)
            share = amount._toShares(totalSupply[assetId], totalAmount, true);
        } else {
            // amount may be lower than the value of share due to rounding, that's ok
            amount = share._toAmount(totalSupply[assetId], totalAmount, false);
        }

        _burn(from, assetId, share);

        // Interactions
        asset.strategy.withdraw(to, amount);

```

여기서 사용자가 소유한 지분을 전략 내 자산의 지분으로 매핑한 다음, 지분을 소각하고 전략에서 해당 자산 수를 인출합니다. `_tokenBalanceOf` 메서드는 전략을 호출하는데, `GlpStrategy`의 경우 다음과 같습니다:

```solidity
function _currentBalance() internal view override returns (uint256 amount) {
        amount = sGLP.balanceOf(address(this));
        amount += pendingRewards();
    }

```

따라서 전략의 자산 잔액은 전략의 GLP 잔액과 전략의 대기 중인 보상의 합계이며, 대기 중인 WETH 보상은 현재 환율을 기준으로 GLP로 교환됩니다.

이제 전략의 인출 로직에서 보상이 청구되고 GLP는 인출자를 위해 tOFT로 랩핑됩니다:

```solidity
function _withdraw(address to, uint256 amount) internal override {
        if (amount == 0) revert NotValid();
        _claimRewards(); // Claim rewards before withdrawing
        if (shouldBuyGLP) {
            _buyGlp(); // Buy GLP with WETH rewards
        }
        sGLP.safeApprove(contractAddress, amount);
        ITapiocaOFTBase(contractAddress).wrap(address(this), to, amount); // wrap the sGLP to tsGLP to `to`, as a transfer
        sGLP.safeApprove(contractAddress, 0);
    }

```

그러나 여기서 문제는 `shouldBuyGLP`가 true일 때만 WETH 보상이 더 많은 GLP를 구매하는 데 사용된다는 것입니다.

따라서 이것은 문제입니다. 계약에 GLP 잔액 대비 대기 중인 보상 금액이 많은 경우 전략의 마지막 인출자들은 전혀 인출할 수 없을 수 있습니다. YieldBox의 지분에서 자산으로의 변환에 따라 계약이 보유한 것보다 더 많은 GLP를 청구하려고 하기 때문입니다.

## 권고 사항

`shouldBuyGLP`가 false여야 하는 이유를 알 수 없습니다. WETH를 인출하는 방법(누적 수수료 제외)이 없으므로 우리는 항상 WETH 보상이 더 많은 GLP를 구매하는 데 사용되기를 원합니다.

`shouldBuyGLP`는 항상 true여야 하거나(이 변수를 모두 제거할 수 있음), WETH 잔액을 회계 처리해야 하며 계약에 인출을 전적으로 GLP로 처리하기에 충분한 GLP가 남아 있지 않은 경우를 처리하는 특별한 로직이 있어야 합니다. 따라서 인출은 부분적으로 GLP, 부분적으로 WETH가 되어야 합니다. 이 후자의 옵션은 꽤 많은 복잡성을 추가하므로 전자가 더 타당할 수 있습니다.

# [M-03] `GlpStrategy`의 슬리피지(Slippage)는 다른 사용자의 지분 가치에 영향을 미침

## 심각도

**영향:** 중간 (Medium), 시장 상황과 인출 순서에 따라 지분 가치가 변경되므로

**가능성:** 중간 (Medium), 이 동작은 모든 인출/수확 시 유도되지만 영향은 현재 슬리피지에 따라 달라지므로

## 설명

사용자가 `GlpStrategy`에서 자산을 인출하고 싶을 때 지분 가치는 지분 비율과 전략의 현재 잔액을 기반으로 계산됩니다:

```solidity
function _currentBalance() internal view override returns (uint256 amount) {
        amount = sGLP.balanceOf(address(this));
        amount += pendingRewards();
    }
```

`pendingRewards` 메서드는 WETH의 계약 잔액으로 구매할 수 있는 GLP의 **추정치**를 반환합니다:

```solidity
function pendingRewards() public view returns (uint256 amount) {
        uint256 wethAmount = weth.balanceOf(address(this));
        uint256 _feesPending = feesPending;
        if (wethAmount > _feesPending) {
            wethAmount -= _feesPending;
            uint256 fee = (wethAmount * FEE_BPS) / 10_000;
            wethAmount -= fee;

            uint256 glpPrice;
            (, glpPrice) = wethGlpOracle.peek(wethGlpOracleData);

            uint256 amountInGlp = (wethAmount * glpPrice) / 1e18;
            amount = amountInGlp - (amountInGlp * _slippage) / 10_000; //0.5%
        }
    }
```

슬리피지가 0.5%(실제 `mintAndStakeGlp` 호출에서 허용할 최대 슬리피지이므로)로 설정되어 있기 때문에 이것은 **추정치**입니다. 그러나 이 로직의 문제는 슬리피지가 변경되기 쉽고 시장 상황의 영향을 받으며 GMX와 상호 작용하는 다른 사용자 및 프로토콜의 영향을 받을 수도 있다는 것입니다.

예를 들어 전략에서 지분을 인출하려는 사용자는 0.5%의 슬리피지를 기준으로 견적을 받지만 시장 상황에 따라 실제 슬리피지가 0.1%라고 하면 사용자는 여전히 동일한 수의 자산을 받지만 이제 풀에 남은 모든 지분의 비례 가치가 증가했습니다. 슬리피지가 낮은 조건에서는 가장 먼저 인출하는 사용자가 가장 큰 영향을 받습니다.

## 권고 사항

틀림없이 이것은 슬리피지가 낮은 시장 상황에서 사용자가 전략에 스테이킹 상태를 유지하도록 장려하기 때문에 프로토콜에 의해 의도된 동작일 수 있습니다. 그러나 이것이 일부 최종 사용자에게 불공평하다는 사실은 프로토콜의 윤리를 위반합니다.

잠재적인 해결책은 수수료 수령자 보상을 포함하여 모든 회계(및 보상 청구)를 sGLP로 유지하는 것입니다. WETH로 스왑하는 부담(필요한 경우)은 수수료 수령자에게 이전될 수 있으며 여기에는 2가지 이점이 있습니다:

1. 스왑 수행 시기를 선택할 수 있어 슬리피지가 낮을 때 수행하여 프로토콜/전략에 유지되는 가치를 극대화할 수 있습니다.
2. 전략에서 인출하는 사용자는 항상 지분에서 공정한 자산 변환을 받습니다.

# [M-04] `sDaiStrategy`는 단일 공격자에 의해 벽돌(brick)이 될 수 있으며 해결하려면 수동 개입이 필요함

## 심각도

**영향:** 중간 (Medium), 계약이 벽돌이 될 수 있으며 이는 최소한의 자금이 위험에 처했을 때 가장 쉽게 달성되기 때문입니다

**가능성:** 중간 (Medium), 단일 공격자가 최소한의 지분으로 또는 정상 작동 중 부주의하게 쉽게 달성할 수 있기 때문입니다

## 설명

예치 및 인출 상호 작용 모두에 대해 YieldBox에서 `_currentBalance` 메서드가 호출되어 자산을 지분으로 또는 그 반대로 교환합니다. 이 메서드가 되돌려지면 모든 예치 및 인출이 실패합니다. 되돌림을 유발할 수 있는 잠재적인 시나리오 중 하나는 다음 줄에서 `feesPending`이 그 앞의 합계보다 클 경우입니다(언더플로우로 되돌려지므로):

```solidity
return queued + maxWithdraw - feesPending;
```

따라서 관리자가 인출할 보류 중인 수수료는 전략에 대한 예치금보다 커야 합니다. 이는 정상적인 운영 중에는 특히 가능성이 높지 않지만 단일 공격자에 의해 유도될 수 있습니다.

공격자가 전략에 가장 먼저 예치하는 경우 가장 쉽게 달성할 수 있습니다. 이 시점의 자산과 지분 간의 환율은 1:1이므로 공격자가 100 자산을 예치하면 100 지분을 받습니다. 공격자는 자산이 실제로 sDai에 예치되고 수익이 발생하도록 최소 `depositThreshold` 자산을 전략에 예치합니다. 이제 짧은 시간이 지난 후 sDai는 수익을 발생시켰고 공격자는 이제 자산을 인출할 수 있습니다. 그들은 지분의 100%를 소유하고 있으므로 다음과 같은 상황에서 모든 자산을 인출할 수 있습니다:

```solidity
queued + maxWithdraw - feesPending = 0 + assetsDeposited - 0 = assetsDeposited
```

대기 중인 자산이 없고 인출이 발생하지 않았으므로 아직 포착된 수수료가 없기 때문입니다. 따라서 이제 인출 중에 수수료는 `feesPending`을 증가시켜 회계 처리되지만 사용자는 여전히 전략에 예치된 모든 자산을 인출합니다.

이 인출 후 전략에는 자산이 0이지만 `feesPending` 변수는 >0입니다. 결과적으로 `_currentBalance`에 대한 모든 호출은 언더플로우로 인해 되돌려집니다. 이는 관리자가 전략에 직접 자산을 보내는 수동 상호 작용으로 해결할 수 있지만 이 시나리오는 애초에 발생해서는 안 됩니다.

## 권고 사항

언더플로우 가능성을 피하기 위해 `_currentBalance`의 마지막 줄을 변경해야 합니다. 제안된 수정 사항은 다음과 같습니다:

```solidity
return feesPending > queued + maxWithdraw ? 0 : queued + maxWithdraw - feesPending;
```

보류 중인 수수료도 다른 문제에서 제안된 대로 적절하게 회계 처리되어야 합니다.

# [M-05] `sDaiStrategy`가 일시 중지되었을 때 사용자가 인출할 수 없음

## 심각도

**영향:** 중간 (Medium), 전략 지분과 통합할 때 되돌려짐(revert)

**가능성:** 중간 (Medium), 이는 `sDaiStrategy`가 일시 중지되었을 때만 적용되며, 이는 너무 자주 발생하지 않아야 하고 일시적이어야 하므로

## 설명

언제든지 `sDaiStrategy`의 소유자는 전략을 일시 중지하거나 비상 인출(일시 중지 활성화)을 할 수 있습니다. 전략이 일시 중지된 것으로 표시되면 예치와 인출 모두 실행할 수 없습니다.

그러나 이 로직의 문제는 언제든지 YieldBox 전략에서 인출을 차단하는 것은 절대 안 된다는 것입니다. 악용 시나리오가 발생한 경우에도 사용자는 자신의 자산 몫을 인출할 수 있어야 합니다. 지분의 상대적 가치가 가능했던 것보다 낮더라도 사용자는 다른 곳에서 차입금을 갚거나 다른 약속을 이행하기 위해 그 희생을 감수하기를 원할 수 있습니다.

## 권고 사항

`_withdraw` 메서드에서 다음 줄을 제거해야 합니다:

```solidity
if (paused) revert Paused();
```

# [M-06] 외부 호출과 함께 허가(permit)를 사용하면 그리핑(griefing) 공격이 가능함

## 심각도

**영향:** 낮음 (Low), 함수 호출자가 프런트러닝되지만 자금 손실은 없음

**가능성:** 높음 (High), 누구나 허가를 그리핑할 수 있음.

## 설명

허가를 호출할 때 허가 데이터가 블록체인에 기록되며 누구나 TX 인수를 복제하여 허가를 프런트러닝할 수 있습니다. EIP에 따르면 허가 생성자가 다른 허가를 생성하면 되므로 이는 문제가 되지 않습니다.

그러나 허가가 `transfer()` 호출과 같은 외부 함수 호출과 함께 사용되는 경우 허가를 프런트러닝하면 함수가 그리핑됩니다.

참조: https://www.trust-security.xyz/post/permission-denied

## 권고 사항

`_checkBatchPermitData()`를 호출할 때 토큰 허용량(allowance)을 여전히 사용할 수 있는지 확인하십시오. `transfer()`를 호출하기 전에 사용자에게 여전히 적절한 허용량이 있는지 확인하십시오.

# [L-01] 최소 슬리피지 설정

glpStrategy에서 슬리피지 허용량을 낮은 값으로 설정하는 것은 위험합니다. 극단적인 시나리오는 0으로 설정하는 것인데, 이는 되돌림 위험을 증가시킵니다. 항상 어느 정도의 슬리피지 허용되어야 합니다.

`_MIN_SLIPPAGE`를 도입하고 `_slippage`가 이 값 아래로 내려가지 않도록 하는 것을 고려하십시오.

# [L-02] AssetTotsDaiLeverageExecutor 및 AssetToSGLPLeverageExecutor 계약의 위험한 payable 함수

두 계약 모두 payable 수정자가 있는 `getCollateral()` 함수를 가지고 있습니다. 사용자가 실수로 자산과 함께 네이티브 토큰을 보내면 네이티브 토큰이 계약에 갇히게 됩니다.

```
 function getCollateral(address assetAddress, address collateralAddress, uint256 assetAmountIn, bytes calldata data)
        external
>       payable
        override
        returns (uint256 collateralAmountOut)
    {
```

payable 함수를 제거하거나 계약에 갇힌 네이티브 토큰을 인출할 수 있는 방법을 마련하는 것을 고려하십시오.

# [L-03] feeCollector.sol의 FEE_BPS를 임의의 값으로 설정할 수 있음

FEE_BPS는 생성자에서 설정될 때 확인되지 않습니다.

```
contract FeeCollector {
>   uint256 public immutable FEE_BPS;
    uint256 internal constant FEE_PRECISION = 10_000;
    address public feeRecipient;
    uint256 public feesPending;


    constructor(address _feeRecipient, uint256 feeBps) {
        feeRecipient = _feeRecipient;
>       FEE_BPS = feeBps;
    }
```

FEE_PRECISION보다 높은 값으로 설정할 수 있습니다.

생성자에 제한을 두어 수수료를 100%보다 크게 설정할 수 없도록 하십시오.

```
    constructor(address _feeRecipient, uint256 feeBps) {
        feeRecipient = _feeRecipient;
+       require(feeBps < 1000, "Fee cannot be more than 10%);
        FEE_BPS = feeBps;
    }
```

# [L-04] PermitC의 permitTransferFromERC20은 permitAmount로 uint256을 사용하지만 최대 승인은 uint200으로만 설정됨

PermitC에서 permitTransferFromERC20은 데이터 유형으로 uint256을 사용합니다.

```
    function permitTransferFromERC20(
        address token,
        uint256 nonce,
        uint256 permitAmount,
        uint256 expiration,
        address owner,
        address to,
 >      uint256 transferAmount,
```

그러나 승인할 때는 uint200만 사용합니다.

```
    function approve(
        address token,
        uint256 id,
        address operator,
>       uint200 amount,
        uint48 expiration) external {
        _storeApproval(token, id, amount, expiration, msg.sender, operator);
```

permit2가 승인 및 전송에 uint160만 사용하는 것과 같이 데이터 유형이 동일한지 확인하십시오.
# [L-05] msg.sender가 허가(permit)에서 해시되지 않음

PermitC의 PermitHash.sol에서 hashSingleUsePermit은 nonce 및 master nonce와 함께 msg.sender를 해시합니다.

```
    function hashSingleUsePermit(
        address token,
        uint256 id,
        uint256 amount,
        uint256 nonce,
        uint256 expiration,
        uint256 masterNonce
    ) internal view returns (bytes32) {
        return keccak256(
            abi.encode(
                SINGLE_USE_PERMIT_TYPEHASH,
                token,
                id,
                amount,
                nonce,
                msg.sender,
                expiration,
                masterNonce
            )
        );
```

PearlmitHash.sol에서 개별 허가에는 nonce와 master nonce가 없지만 전체 배치 해시에는 nonce와 master nonce가 있습니다. 그러나 전체 배치 해시는 msg.sender가 함께 해시되지 않습니다.

```
    function hashBatchTransferFrom(
        IPearlmit.SignatureApproval[] memory approvals,
        uint256 nonce,
        uint48 sigDeadline,
        uint256 masterNonce
    ) internal pure returns (bytes32) {
        uint256 numPermits = approvals.length;
        bytes32[] memory permitHashes = new bytes32[](numPermits);
        for (uint256 i = 0; i < numPermits; ++i) {
            permitHashes[i] = _hashPermitSignatureApproval(approvals[i]);
        }


        return keccak256(
            abi.encode(
                _PERMIT_BATCH_TRANSFER_FROM_TYPEHASH,
                keccak256(abi.encodePacked(permitHashes)),
>               nonce,
                sigDeadline,
>               masterNonce
               //@audit - no msg.sender
            )
        );
    }
```

보안을 강화하고 서명 재생(replay) 공격의 가능성을 줄이기 위해 배치 해시에서도 msg.sender를 해시하는 것을 고려하십시오.

# [L-06] 승인(approval)을 0으로 설정하면 PermitC에서 현재 블록의 맥락으로만 유효성이 설정되지 않음

PermitC에서 승인할 때 125행에 다음과 같이 명시되어 있습니다:

> 만료가 0인 경우 승인은 현재 블록의 맥락에서만 유효합니다.

그러나 `_storeApproval()`은 0이 만료로 입력되면 만료를 현재 블록의 맥락으로 설정하지 않습니다.

```
    function _storeApproval(
        address token,
        uint256 id,
        uint200 amount,
        uint48 expiration,
        address owner,
        address operator
    ) private {
        PackedApproval storage allowed = _getPackedApprovalPtr(owner, token, id, ZERO_BYTES32, operator);
>       allowed.expiration = expiration;
        allowed.amount = amount;
```

Permit2.sol에서 `approve()`가 호출되어 `updateAmountAndExpiration()`을 호출할 때 만료로 0이 입력되면 만료가 block.timestamp로 설정됩니다.

```
    function approve(address token, address spender, uint160 amount, uint48 expiration) external {
        PackedAllowance storage allowed = allowance[msg.sender][token][spender];
>        allowed.updateAmountAndExpiration(amount, expiration);
        emit Approval(msg.sender, token, spender, amount, expiration);
    }
```

```
    function updateAmountAndExpiration(
        IAllowanceTransfer.PackedAllowance storage allowed,
        uint160 amount,
        uint48 expiration
    ) internal {
        // If the inputted expiration is 0, the allowance only lasts the duration of the block.
>       allowed.expiration = expiration == 0 ? uint48(block.timestamp) : expiration;
        allowed.amount = amount;
    }
```

Permit2가 하는 것처럼 입력 만료가 0인 경우 만료를 block.timestamp로 설정하십시오.

# [L-07] PearImit.sol의 permitBatchTransferFrom()이 오류를 처리하지 않음

## 심각도

**영향:** 낮음 (Low), 트랜잭션이 조용히 실패하고 사용자는 배치에서 어떤 승인이 실패했는지 알 수 없으므로. 자금 손실은 없습니다.

**가능성:** 낮음 (Low), `permitBatchTransferFrom()` 호출 실패 시 발생.

## 설명

PearImit.sol의 `permitBatchTransferFrom()`은 먼저 승인을 확인한 다음 `_transferFromERC20()`과 같은 전송 중 하나를 호출합니다.

```
    function permitBatchTransferFrom(IPearlmit.PermitBatchTransferFrom calldata batch) external {
>       _checkPermitBatchApproval(batch);


        uint256 numPermits = batch.approvals.length;
        for (uint256 i = 0; i < numPermits; ++i) {
            IPearlmit.SignatureApproval calldata approval = batch.approvals[i];
            if (approval.tokenType == uint8(IPearlmit.TokenType.ERC20)) {
>              _transferFromERC20(approval.token, batch.owner, approval.operator, 0, approval.amount);
            } else if (approval.tokenType == uint8(IPearlmit.TokenType.ERC721)) {
>               _transferFromERC721(batch.owner, approval.operator, approval.token, approval.id);
            } else if (approval.tokenType == uint8(IPearlmit.TokenType.ERC1155)) {
>              _transferFromERC1155(approval.token, batch.owner, approval.operator, approval.id, approval.amount);
            }
        }
```

`transferFrom()` 함수는 PermitC에서 호출되며 토큰 전송을 처리합니다.

```
    function _transferFromERC20(
        address token,
        address owner,
        address to,
        uint256 /*id*/,
        uint256 amount
      ) internal returns (bool isError) {
        isError = _beforeTransferFrom(token, owner, to, ZERO, amount);


        if (!isError) {
            (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.transferFrom.selector, owner, to, amount));
```

이것이 PermitC가 단일 전송을 수행하는 방식입니다. `_transferFromERC20()`은 부울 값을 반환하고 `transferFromERC20()`은 오류를 반환합니다.

```
     function transferFromERC20(
        address owner,
        address to,
        address token,
        uint256 amount
    ) external returns (bool isError) {
        _checkAndUpdateApproval(owner, token, ZERO, amount, false);
>       isError = _transferFromERC20(token, owner, to, ZERO, amount);
    }
```

PearImit.sol에서는 오류가 확인되지 않고 반환되지 않습니다. 함수 실행이 실패하면 사용자는 배치에서 어떤 승인이 실패했는지 알 수 없습니다.

## 권고 사항

`permitBatchTransferFrom()`을 호출할 때 모든 오류를 포착하고 배열에 기록하여 사용자가 배치에서 어떤 승인이 실패했는지 알 수 있도록 하는 것이 좋습니다.

# [L-08] `Pearlmit` 배치 승인 기한이 잘못 사용됨

`permitBatchApprove`에서 자산 배치의 승인 기한은 서명의 기한으로 설정됩니다:

```solidity
function permitBatchApprove(IPearlmit.PermitBatchTransferFrom calldata batch) external {
        _checkPermitBatchApproval(batch);

        uint256 numPermits = batch.approvals.length;
        for (uint256 i = 0; i < numPermits; ++i) {
            IPearlmit.SignatureApproval calldata approval = batch.approvals[i];
            __storeApproval(
                approval.token, approval.id, approval.amount, batch.sigDeadline, batch.owner, approval.operator
            );
        }
    }
```

`Pearlmit.sol`의 독스트링(docstring)은 `SignatureApproval` 구조체에 `approvalExpiration` 필드가 있음을 나타냅니다:

```solidity
// batch.approvals.approvalExpiration - expiration of the approval.
```

그러나 구조체에는 실제로 이 필드가 포함되어 있지 않습니다. 아래 "권장 사항" 섹션에서 제안한 대로 포함되어야 한다고 생각합니다.

현재 로직의 문제는 자산 승인 만료 시간이 대부분의 경우 미래의 중요한 시간(예: 1개월/1년 등)이라는 것입니다. 그렇지 않으면 승인 메커니즘의 가치가 제한적이기 때문입니다. 그러나 이는 이제 서명 기한도 미래의 중요한 시간이어야 함을 의미합니다. 만료 시간이 긴 허가(permit)에 서명하는 것은 허가가 유출/도난당하여 악의적으로 사용될 수 있는 기간을 늘리기 때문에 위험합니다. 이 경우 허가는 토큰 접근을 직접 제어하므로 도난당할 경우 영향이 상당히 큽니다.

기한이 긴 댕글링(dangling) 허가는 유출/도난 시 허가가 악의적으로 사용될 위험을 높입니다. 이는 모든 배치 승인에 영향을 미치지만 서명된 허가의 도난/유출이 필요합니다.

`SignatureApproval` 구조체는 배치의 각 자산에 고유한 `approvalExpiration` 필드를 포함하도록 확장되어야 하며 이 만료는 `__storeApproval` 호출에서 사용되어야 합니다.

`permitBatchTransferFrom` 메서드는 또한 각 자산에 대한 `approvalExpiration`이 미래인지 확인해야 하지만 현실적으로 서명 기한 확인이 먼저 발생하며 서명 기한은 일반적으로 배치의 자산 승인 만료보다 짧습니다.


