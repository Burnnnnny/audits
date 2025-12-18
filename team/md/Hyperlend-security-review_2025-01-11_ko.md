# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **hyperlendx/hyperlend-core, hyperlendx/hyperlend-isolated, hyperlendx/core-config-engine, hyperlendx/looping-contracts, hyperlendx/cross-chain-lending-deposits** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Hyperlend 정보 (About Hyperlend)

HyperLend는 Hyperliquid의 EVM 블록체인에 구축된 대출 플랫폼으로, 사용자가 유동성 풀에 자산을 공급하고 예치금에 대해 대출할 수 있도록 합니다. 공급과 수요에 기반한 알고리즘 이자율 모델을 활용하여 효율적인 자본 활용을 보장합니다. 이 플랫폼은 플래시론, P2P 대출, 격리 및 코어 풀을 지원합니다.

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

_검토 커밋 해시:_

- [4256249f94b8762a5ce41caa2283c0d989efc593](https://github.com/hyperlendx/hyperlend-core/tree/4256249f94b8762a5ce41caa2283c0d989efc593)
- [37c678450f37a923517e7a203f8353599e143b7e](https://github.com/hyperlendx/hyperlend-isolated/tree/37c678450f37a923517e7a203f8353599e143b7e)
- [0339f192bb98b55c856d1cc6f76a04bd6fe687ee](https://github.com/hyperlendx/core-config-engine/tree/0339f192bb98b55c856d1cc6f76a04bd6fe687ee)
- [0fdde7b1033263b7c7579c9ec505dfb635197f3c](https://github.com/hyperlendx/looping-contracts/tree/0fdde7b1033263b7c7579c9ec505dfb635197f3c)
- [2576caa13409d49bd886965d3c4b3fe61e40397e](https://github.com/hyperlendx/cross-chain-lending-deposits/tree/2576caa13409d49bd886965d3c4b3fe61e40397e)

_수정 검토 커밋 해시:_

- [c8ea750400e62ef089ed95ea2851afe7027646e5](https://github.com/hyperlendx/looping-contracts/tree/c8ea750400e62ef089ed95ea2851afe7027646e5)
- [a496d810a66df9b889306b7e6807ba354785d59e](https://github.com/hyperlendx/cross-chain-lending-deposits/tree/a496d810a66df9b889306b7e6807ba354785d59e)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `HyperlendPair`
- `HyperlendPairAccessControl`
- `HyperlendPairAccessControlErrors`
- `HyperlendPairDeployer`
- `HyperlendPairRegistry`
- `HyperlendWhitelist`
- `ReservesSetupHelper`
- `SeedAmountsHolder`
- `Oracle`
- `ProtocolDataProvider`
- `PriceOracleSentinel`
- `FlashLoanLogic`
- `DataTypes`
- `Pool`
- `AToken`
- `StableDebtToken`
- `ACLConfigEngine`
- `CapsConfigEngine`
- `ConfiguratorInputTypes`
- `DataTypes`
- `Context`
- `Ownable`
- `ListingConfigEngine`
- `ListingsConfigEngineFactory`
- `UiDataProvider`
- `Looping`
- `StrategyManager`
- `StrategyManagerFactory`
- `UniswapV3Swapper`

# 발견 사항 (Findings)

# [M-01] StrategyManager 함수들이 `payable`이 아님 (StrategyManager functions are not `payable`)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

사용자는 StrategyManager를 사용하여 한 번의 트랜잭션으로 전략을 수행할 수 있으며 코드는 트랜잭션에 ETH 전송을 허용합니다:

```solidity
    function executeCall(address target, uint256 value, bytes memory data, bool allowRevert) public onlyOwner() returns (bytes memory) {
        (bool success, bytes memory returnData) = target.call{value: value}(data);
        if (!allowRevert) require(success, 'execution reverted');
        return returnData;
    }
```

문제는 StrategyManager의 어떤 함수도 payable이 아니며 ETH를 StrategyManager 주소로 전송하고 ETH 전송이 필요한 트랜잭션을 수행할 수 있는 payable `fallback()` 또는 `receive()` 함수가 없다는 것입니다.

## 권장 사항

StrategyManager 함수를 `payable`로 표시하십시오.

# [M-02] `Looping.openPosition()` 불충분한 나눗셈 계수 (`Looping.openPosition()` insufficient division factor)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Looping.openPosition()` 함수에서 사용자가 `yieldAsset`으로 예상 플래시론 프리미엄 지불을 제공하면 `debtAsset`으로 스왑되는데, 여기서 스왑에서 예상되는 `minAmountOut`은 다음과 같이 계산됩니다:

```solidity
function openPosition(
        address _pool,
        address _swapper,
        address _debtAsset,
        address _yieldAsset,
        uint256 _initialAmount,
        uint256 _flashloanAmount,
        uint256 _minAmountOut,
        address[] memory _path,
        bool _startWithYield
    ) external nonReentrant {
        //...

        if (_startWithYield) {
         //...
            uint256 minAmountOut = (((_flashloanAmount * 1e8) / _minAmountOut) *
                _initialAmount) / 1e8;
            //...
        } else {
            //...
        }
        //...
    }
```

`1e8` 계수는 `_minAmountOut`으로 나눌 때 내림 문제를 고려하기 위해 도입되었습니다.

그러나 `yieldAsset decimals` > `debtAsset decimals + 8`인 경우, 도입된 계수는 내림에 대한 충분한 보호를 제공하지 않아 예상보다 작은 `minAmountOut` 값이 계산될 수 있습니다.

## 권장 사항

더 나은 정밀도 처리를 보장하기 위해 `1e8` 계수를 더 높은 값(예: `1e36`)으로 늘리는 것을 고려하십시오:

```diff
function openPosition(
        address _pool,
        address _swapper,
        address _debtAsset,
        address _yieldAsset,
        uint256 _initialAmount,
        uint256 _flashloanAmount,
        uint256 _minAmountOut,
        address[] memory _path,
        bool _startWithYield,
+       uint256 _minInitialAmountOut
    ) external nonReentrant {
        //...

        if (_startWithYield) {
            //...
-           uint256 minAmountOut = (((_flashloanAmount * 1e8) / _minAmountOut) *
-               _initialAmount) / 1e8;

+           uint256 minAmountOut = (((_flashloanAmount * 1e36) / _minAmountOut) *
+               _initialAmount) / 1e36;

        //...
    }
```

# [M-03] `_initialAmount`가 `_yieldAsset`일 때 `openPosition()` DoS (`openPosition()` DoS when `_initialAmount` is `_yieldAsset`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

- `Looping.openPosition()` 함수는 사용자가 `yieldAsset` 또는 `debtAsset`으로 플래시론 프리미엄 지불(`_initialAmount`)을 제공할 수 있도록 합니다:

```solidity
function openPosition(
        address _pool,
        address _swapper,
        address _debtAsset,
        address _yieldAsset,
        uint256 _initialAmount,
        uint256 _flashloanAmount,
        uint256 _minAmountOut,
        address[] memory _path,
        bool _startWithYield
    ) external nonReentrant {
        require(pools[_pool], "pool not allowed");

        if (_startWithYield) {
            //transfer initial _yieldAsset from user
            IERC20(_yieldAsset).transferFrom(
                msg.sender,
                address(this),
                _initialAmount
            );
            uint256 minAmountOut = (((_flashloanAmount * 1e8) / _minAmountOut) *
                _initialAmount) / 1e8;
            _initialAmount = _swap(
                _swapper,
                _reversePath(_path),
                _initialAmount,
                minAmountOut
            );
        } else {
            //...
        }
        //...
    }
```

- `_initialAmount`가 `yieldAsset`인 경우 `debtAsset`으로 `_initialAmount`를 얻기 위해 스왑이 수행되며, 여기서 스왑에서 예상되는 `minAmountOut`은 다음과 같이 계산됩니다:

```solidity
uint256 minAmountOut = (((_flashloanAmount * 1e8) / _minAmountOut) * _initialAmount) / 1e8;
```

이 경우:

- `_flashloanAmount`: 플래시론 후 `_yieldAsset`으로 스왑될 `_debtAsset`의 양
- `_minAmountOut`: `_debtAsset`을 스왑한 후 받을 것으로 예상되는 `_yieldAsset`의 최소 양
- `_initialAmount`: 사용자가 제공한 `_yieldAsset`의 초기 양

- 그러나 알 수 있듯이, `minAmountOut` 계산은 `_debtAsset`과 `_yieldAsset`이 동일한 가치를 갖는다고 가정하는데, 이는 부정확합니다. 예를 들어:

  - 사용자가 `100_000 USDC`를 플래시론하고, 받을 WBTC의 `_minimumAmountOut`이 `0.9995 WBTC`이며 0.05%의 허용 가능한 슬리피지가 있습니다(1WBTC 가격이 100_000USDC라고 가정).
  - 플래시론 프리미엄이 플래시론 금액의 0.1%로 설정되었다고 가정하면, 사용자는 `openPosition()`을 실행하기 위해 100USDC에 해당하는 WBTC, 즉 `0.001` WBTC를 제공합니다.
  - 따라서 계산된 `minAmountOut`은 **USDC 소수점으로 100.050025**가 되며 이는 제공된 `_initialAmount`의 값보다 큽니다:

  ```solidity
  minAmountOut = (((100_000*1e6 * 1e8) / 0.9995*1e8) * 0.001*1e8) / 1e8 = 100.050025 in USDC decimals
  ```

- 스왑 시 `minAmountOut` > `_initialAmount`를 계산하게 되어 트랜잭션이 되돌려집니다.

## 권장 사항

이 최소 금액을 인수로 받도록 `Looping.OpenPosition()` 함수를 업데이트하십시오:

```diff
function openPosition(
        address _pool,
        address _swapper,
        address _debtAsset,
        address _yieldAsset,
        uint256 _initialAmount,
        uint256 _flashloanAmount,
        uint256 _minAmountOut,
        address[] memory _path,
        bool _startWithYield,
+       uint256 _minInitialAmountOut
    ) external nonReentrant {
        require(pools[_pool], "pool not allowed");

        if (_startWithYield) {
            //transfer initial _yieldAsset from user
            IERC20(_yieldAsset).transferFrom(
                msg.sender,
                address(this),
                _initialAmount
            );
-           uint256 minAmountOut = (((_flashloanAmount * 1e8) / _minAmountOut) *
-               _initialAmount) / 1e8;
            _initialAmount = _swap(
                _swapper,
                _reversePath(_path),
                _initialAmount,
-               minAmountOut
+               _minInitialAmountOut
            );
        } else {
            //...
        }
        //...
    }
```

# [M-04] `UniswapV3Swapper.swap()`에서 취소되지 않은 승인으로 인한 스왑 실패 (Unrevoked approvals causing swap failures in `UniswapV3Swapper.swap()`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`UniswapV3Swapper.swap()` 함수는 스왑 후 라우터에서 토큰 승인을 취소하지 않는 문제가 있어 `USDT`와 같은 토큰에서 잠재적인 DoS가 발생할 수 있습니다. 이 문제는 스왑 후 승인이 재설정되지 않아 발생하며, `USDT`와 같은 토큰이 사용되는 경우 이전 스왑의 허용량이 라우터에 의해 완전히 소비되지 않으면 0이 아닌 값에서 다른 0이 아닌 값으로 허용량을 수정할 때 되돌려져 스왑 트랜잭션이 실패하게 됩니다.

`Looping._swap()` 함수에서도 다른 영향으로 유사한 문제가 발견되었습니다.

## 권장 사항

스왑 후 라우터에서 토큰 승인을 취소하도록 `UniswapV3Swapper.swap()` 함수를 업데이트하십시오:

```diff
    function swap(
        address tokenIn,
        address tokenOut,
        uint256 amountIn,
        uint256 minAmountOut,
        address to,
        address referrer, // Unused in this implementation
        uint256 deadline
    ) external override returns (uint256 amountOut) {
        IERC20(tokenIn).transferFrom(msg.sender, address(this), amountIn);
        IERC20(tokenIn).approve(address(router), amountIn);
        //...
+       IERC20(tokenIn).approve(address(router), 0);
    }
```

# [L-01] Looping 경로를 확인하지 않는 코드 (Code does not verify the path Looping)

사용자가 `openPosition()` 또는 `closePosition()`을 호출할 때마다 스왑을 수행하는 데 사용되는 `path` 변수를 전달합니다. 문제는 코드가 `path` 값을 확인하여 경로 목록의 첫 번째와 마지막 주소가 스왑될 토큰과 동일한지 확인하지 않는다는 것입니다. 경우에 따라 사용자가 경로의 마지막 주소에 잘못된 값을 지정하면 코드가 잘못된 토큰을 수신하고 해당 토큰은 계약 주소에 머물게 되어 사용자가 자금을 잃을 수 있습니다. 이러한 엣지 케이스를 피하기 위해 `path` 매개변수의 값을 확인하는 것이 좋습니다.

# [L-02] 사용자 포지션 종료를 위한 잘못된 확인 (Incorrect check to close the user's position)

`Looping._executeClosePosition()` 함수는 `repaymentAmount == type(uint256).max`인 경우 사용자 포지션을 종료하는 확인 기능이 있습니다. 그러나 `repaymentAmount`는 `params`에서 디코딩되며 이는 `flashloanAmount`를 나타내고, `flashloanAmount`는 `type(uint256).max`로 설정할 수 없으므로(달성할 수 없음) 이 확인은 불필요합니다:

```solidity
  function _executeClosePosition(
        bytes memory params,
        address debtAsset
    ) internal {
        (
            ,
            //action type
            address yieldAsset,
            address swapper,
            address[] memory path,
            uint256 repaymentAmount, //=flashloanAmount
            uint256 minAmountOut,
            address user,
            uint256 withdrawAmount
        ) = abi.decode(
                params,
                (
                    uint8,
                    address,
                    address,
                    address[],
                    uint256,
                    uint256,
                    address,
                    uint256
                )
            );

        //...

        if (repaymentAmount == type(uint256).max) {
            repaymentAmount = debtDebtToken.balanceOf(user);
            withdrawAmount = hYieldToken.balanceOf(user);
        }

        //...
    }
```

## 권장 사항

`repaymentAmount` 대신 `withdrawAmount`를 사용하여 확인하도록 업데이트하십시오. `withdrawAmount`는 `type(uint256).max`로 설정할 수 있습니다:

```diff
  function _executeClosePosition(
        bytes memory params,
        address debtAsset
    ) internal {
        (
            ,
            //action type
            address yieldAsset,
            address swapper,
            address[] memory path,
            uint256 repaymentAmount, //=flashloanAmount
            uint256 minAmountOut,
            address user,
            uint256 withdrawAmount
        ) = abi.decode(
                params,
                (
                    uint8,
                    address,
                    address,
                    address[],
                    uint256,
                    uint256,
                    address,
                    uint256
                )
            );

        //...

-       if (repaymentAmount == type(uint256).max) {
+       if (withdrawAmount == type(uint256).max) {
            repaymentAmount = debtDebtToken.balanceOf(user);
            withdrawAmount = hYieldToken.balanceOf(user);
        }

        //...
    }
```

# [L-03] `UniswapV3Swapper.swap()`에 스왑 수수료가 하드코딩됨 (Swap fee is hardcoded in `UniswapV3Swapper.swap()`)

`UniswapV3Swapper.swap()` 함수에서: `ExactInputSingleParams`를 구성할 때 0.3%(3000)의 고정 스왑 수수료가 하드코딩되어 있습니다. 그러나 Uniswap V3는 일반적으로 주어진 토큰 쌍에 대해 여러 풀을 제공하며 각각 다른 수수료율을 갖습니다.

예를 들어, `USDC`를 `WETH`로 스왑하는 것은 일반적으로 0.3%(3000) 수수료 풀에 비해 유동성이 더 좋고 슬리피지가 적은 0.05%(500) 수수료 풀이 더 효율적입니다.

최적의 풀을 사용할 수 없는 경우 스왑이 실패할 수 있습니다:

```solidity
   function swap(
        address tokenIn,
        address tokenOut,
        uint256 amountIn,
        uint256 minAmountOut,
        address to,
        address referrer, // Unused in this implementation
        uint256 deadline
    ) external override returns (uint256 amountOut) {
       //...
        uint256 balanceBefore = IERC20(tokenOut).balanceOf(to);
        IUniswapV3Router.ExactInputSingleParams memory params = IUniswapV3Router
            .ExactInputSingleParams({
                tokenIn: tokenIn,
                tokenOut: tokenOut,
                fee: 3000,
                recipient: to,
                deadline: deadline,
                amountIn: amountIn,
                amountOutMinimum: minAmountOut,
                sqrtPriceLimitX96: 0
            });
       //...
    }
```

권장 사항: 수수료를 매개변수로 전달하도록 `UniswapV3Swapper.swap()` 함수를 업데이트하여 각 스왑에 대해 최적의 풀을 동적으로 선택할 수 있도록 하십시오.

# [L-04] 사용자가 스왑 마감일을 지정할 수 없음 (Code does not allow users to specify a deadline for swaps)

사용자는 Looping 계약을 사용하여 레버리지 포지션을 열거나 닫을 수 있습니다. 코드는 작업 중에 스왑을 수행합니다.

```solidity
        ISwapper(swapper).swapExactTokensForTokensSupportingFeeOnTransferTokens(
            amountToSwap,
            minAmountOut,
            path,
            address(this),
            referralAddress,
            block.timestamp
        );
```

문제는 코드가 사용자의 토큰을 스왑할 때 스왑 마감일에 `block.timestamp`를 사용하고 사용자가 스왑 마감일을 지정할 수 없다는 것입니다. 시간이 지남에 따라 스왑 결과가 변경될 수 있으므로 손실을 방지하기 위해 사용자가 온체인 스왑 실행에 대한 더 많은 제어권을 갖도록 스왑 마감일을 지정할 수 있게 하는 것이 일반적인 관행입니다.

사용자가 마감일을 지정하고 스왑 호출 시 이를 사용하도록 허용하십시오.

# [L-05] `StrategyManager.executeCall()`의 잘못된 `allowRevert` 처리 (`StrategyManager.executeCall()` incorrect handling of `allowRevert`)

`StrategyManager.executeCall()` 함수는 소유자가 외부 호출을 실행하기 위해 호출하도록 의도되었으며, 주로 사용자가 HyperLend 포지션을 관리할 수 있도록 설계되었습니다:

```solidity
    function executeCall(
        address target,
        uint256 value,
        bytes memory data,
        bool allowRevert
    ) public onlyOwner returns (bytes memory) {
        (bool success, bytes memory returnData) = target.call{value: value}(
            data
        );
        if (!allowRevert) require(success, "execution reverted");
        return returnData;
    }
```

이 함수에는 `allowRevert` 매개변수가 있는데, `true`로 설정된 경우 실패한 호출이 **되돌려져야(revert)** 합니다. 그러나 현재 확인 구현은 `allowRevert`가 `false`로 설정된 경우(소유자가 호출이 되돌려지기를 원하지 않을 때) 호출을 되돌리므로 `allowRevert`의 의도된 사용과 모순됩니다.

결과적으로 소유자가 호출을 실행하고 `allowRevert = true`(실패 시 되돌려져야 함을 의미)를 설정하면 트랜잭션이 되돌려지지 않으며 호출에 의해 변경된 사항이 롤백되지 않습니다. 이는 특히 호출이 소유자가 실행한 일련의 호출의 일부인 경우 전송된 가치의 손실 및 원치 않는 결과 또는 상태 변경으로 이어질 수 있습니다.

```diff
    function executeCall(
        address target,
        uint256 value,
        bytes memory data,
        bool allowRevert
    ) public onlyOwner returns (bytes memory) {
        (bool success, bytes memory returnData) = target.call{value: value}(
            data
        );
-       if (!allowRevert) require(success, "execution reverted");
+       if (allowRevert) require(success, "execution reverted");

        return returnData;
    }
```
