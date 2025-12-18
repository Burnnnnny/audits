# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 잘 입증된 40명 이상의 프리랜서 보안 연구원으로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 챔피언이거나, 우리와 함께 감사에서 뛰어난 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하여 수천 개의 취약점을 발견하고 패치하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**acc8-labs/funnel-vault** 및 **acc8-labs/v3-core** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, **Shaka, 0xTheBlackPanther, imsrybr0**가 **Funnel** 검토에 참여했습니다. 총 **14**개의 문제가 발견되었습니다.

# Funnel 소개 (About Funnel)

Funnel은 Hyperliquid에서 Uniswap V3 스타일 유동성 풀을 배포하고 관리하기 위한 시스템으로, 수수료 수집, 전환 및 HyperEVM과 HyperCore 간의 브리징을 지원합니다. 구성 가능한 수수료 관리가 있는 금고(vault) 기반 아키텍처를 통해 HIP-1 티커 획득, 토큰 소각 및 생성자 수수료 청구를 자동화합니다.

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**
• [95019239bb194f7af3bf27fd91b1238efe955af4](https://github.com/acc8-labs/funnel-vault/tree/95019239bb194f7af3bf27fd91b1238efe955af4)
  (acc8-labs/funnel-vault)
• [2c14fb74cc761f51a33971403ace3453abe2b554](https://github.com/acc8-labs/v3-core/tree/2c14fb74cc761f51a33971403ace3453abe2b554)
  (acc8-labs/v3-core)

**수정 검토 커밋 해시:**
• [14774a70ffeaad0108d8a08ea3a7018cfa5178bf](https://github.com/acc8-labs/funnel-vault/tree/14774a70ffeaad0108d8a08ea3a7018cfa5178bf)
  (acc8-labs/funnel-vault)

### 범위 (Scope)

- `UniswapV3Factory.sol`
- `UniswapV3Pool.sol`
- `IUniswapV3Factory.sol`
- `FunnelVaultUpgradeable.sol`

# 발견 사항 (Findings)

# [H-01] `FunnelVaultUpgradeable`이 HyperCore로 트랜잭션을 보낼 수 없음

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`sendNativeTokenToLayer1()`은 HyperEVM에서 HyperCore로 `HYPE`를 브리징합니다. 이 자금은 HyperCore의 금고 계약 주소로 입금되므로, 계약이 HyperEVM에서 HyperCore로 트랜잭션을 보내는 로직을 구현해야 합니다.

`FunnelVaultUpgradeable`이 HyperCore로 트랜잭션을 보내는 메커니즘을 구현하지 않으므로, 필요한 로직을 추가하여 계약이 업그레이드될 때까지 자금이 HyperCore에 잠기게 됩니다.

## 권장 사항

[Hyperliquid 문서](https://hyperliquid.gitbook.io/hyperliquid-docs/for-developers/hyperevm/interacting-with-hypercore#corewriter-contract)에 따라 HyperEVM에서 HyperCore로 트랜잭션을 보내는 로직을 구현하십시오.

# [H-02] 출력 토큰이 기본(native) 토큰일 때 swapTokens가 SwapX 라우터 수수료를 고려하지 않음

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

출력 토큰이 기본 토큰인 경우, `SwapX` 라우터는 `swapV2ExactIn` 및 `swapV3ExactIn` 모두에서 스왑 출력 금액에 대해 수수료를 부과하고 나머지를 수신자에게 보내지만 여전히 호출자에게 원래 출력 금액을 반환합니다.

[SwapX@takeFee](https://github.com/acc8-labs/swapx-router-contracts/blob/main/src/SwapX.sol#L149-L165)
```solidity
    function takeFee(address tokenIn, uint256 amountIn) internal returns (uint256){
        if (feeExcludeList[msg.sender])
            return 0;

        uint256 fee = amountIn.mul(feeRate).div(feeDenominator);

        if ( tokenIn == address(0) || tokenIn == WETH ) {
            require(address(this).balance > fee, "insufficient funds");
            (bool success, ) = address(feeCollector).call{ value: fee }("");
            require(success, "SwapX: take fee error");
        } else
            IERC20Upgradeable(tokenIn).safeTransferFrom(msg.sender, feeCollector, fee);

        emit FeeCollected(tokenIn, msg.sender, fee, amountIn, block.timestamp);

        return fee; 
    }
```

[SwapX@swapV2ExactIn](https://github.com/acc8-labs/swapx-router-contracts/blob/main/src/SwapX.sol#L168-L236)
```solidity
    function swapV2ExactIn(
        address tokenIn, 
        address tokenOut, 
        uint256 amountIn, 
        uint256 amountOutMin, 
        address poolAddress
    ) payable public nonReentrant whenNotPaused returns (uint amountOut){
        // ...

        bool nativeOut = false;
        if (tokenOut == address(0))
            nativeOut = true; <@

        // ...

        if (nativeOut) {
            amountOut = IERC20Upgradeable(WETH).balanceOf(address(this)).sub(balanceBefore); <@
            IWETH(WETH).withdraw(amountOut);
            uint256 fee = takeFee(address(0), amountOut); <@
            (bool success, ) = address(msg.sender).call{value: amountOut-fee}(""); <@
            require(success, "SwapX: send ETH out error");
        } else {
            amountOut = IERC20Upgradeable(tokenOut).balanceOf(msg.sender).sub(balanceBefore);
        }
        require(
            amountOut >= amountOutMin,
            'SwapX: insufficient output amount'
        );
    }
```

[SwapX@swapV3ExactIn](https://github.com/acc8-labs/swapx-router-contracts/blob/main/src/SwapX.sol#L499-L540)
```solidity
    function swapV3ExactIn (
            ExactInputSingleParams memory params
    ) external payable nonReentrant whenNotPaused checkDeadline(params.deadline) returns (uint256 amountOut) {
        // ...

        bool nativeOut = false;
        if (params.tokenOut == WETH) 
            nativeOut = true;

        // ...

        amountOut = exactInputInternal( <@
            params.amountIn,
            nativeOut ? address(0) : params.recipient,
            params.sqrtPriceLimitX96,
            SwapCallbackData({path: abi.encodePacked(params.tokenIn, params.fee, params.tokenOut), payer: msg.sender, payerOrigin: msg.sender})
        );

        require(amountOut >= params.amountOutMinimum, "SwapX: insufficient out amount");

        if (nativeOut) {
            IWETH(WETH).withdraw(amountOut);
            uint256 fee = takeFee(address(0), amountOut); <@
            (bool success, ) = address(params.recipient).call{value: amountOut-fee}(""); <@
            require(success, "SwapX: send ETH out error");
        }
    }
```

이로 인해 `FunnelVault`는 스왑에서 실제로 받은 것보다 더 많은 토큰을 회계 처리하게 됩니다.

[FunnelVaultUpgradeable@swapTokens](https://github.com/acc8-labs/funnel-vault/blob/main/src/FunnelVaultUpgradeable.sol#L317C14-L398)
```solidity
    function swapTokens(SwapParams calldata swapParams, address swapRouter, bool isBurn) external onlyRole(EXECUTER_ROLE) nonReentrant returns (uint256 amountOut) {
        // ...

        if (swapParams.useV3) {
            ISwapX.ExactInputSingleParams memory params = ISwapX.ExactInputSingleParams({
                tokenIn: swapParams.tokenIn,
                tokenOut: swapParams.tokenOut,
                fee: swapParams.fee,
                recipient: address(this),
                deadline: swapParams.deadline,
                amountIn: swapParams.amountIn,
                amountOutMinimum: swapParams.amountOutMin,
                sqrtPriceLimitX96: 0
            });

            if (params.tokenIn == address(0)) {
                amountOut = ISwapX(swapRouter).swapV3ExactIn{value: params.amountIn}(params);
            } else {
                amountOut = ISwapX(swapRouter).swapV3ExactIn(params); <@
            }
        } else {
            if (swapParams.poolAddress == address(0)) {
                revert ZeroAddress();
            }

            if (swapParams.tokenIn == address(0)) {
                amountOut = ISwapX(swapRouter).swapV2ExactIn{value: swapParams.amountIn}(
                    swapParams.tokenIn,
                    swapParams.tokenOut,
                    swapParams.amountIn,
                    swapParams.amountOutMin,
                    swapParams.poolAddress
                );
            } else {
                amountOut = ISwapX(swapRouter).swapV2ExactIn( <@
                    swapParams.tokenIn,
                    swapParams.tokenOut,
                    swapParams.amountIn,
                    swapParams.amountOutMin,
                    swapParams.poolAddress
                );
            }
        }

        if (amountOut == 0) {
            revert SwapFailed();
        }

        if (swapParams.tokenOut == WETH && swapParams.useV3) {
            if(isBurn){
                addToPoolBurn(address(0), swapParams.payingPool, amountOut); <@
            } else {
                addToPoolHIP(address(0), swapParams.payingPool, amountOut); <@
            }
        } else {
            if(isBurn){
                addToPoolBurn(swapParams.tokenOut, swapParams.payingPool, amountOut); <@
            } else {
                addToPoolHIP(swapParams.tokenOut, swapParams.payingPool, amountOut); <@
            }
        }

        emit TokenSwapped(swapParams.tokenIn, swapParams.tokenOut, swapParams.amountIn, amountOut, swapRouter);
    }
```

## 권장 사항
* Funnel이 제어하는 `SwapX` 라우터에서 `FunnelVault`를 `feeExcludeList`에 추가하거나 `feeRate`를 0으로 설정하십시오.
* 제어되는 `SwapX` 라우터와 제어되지 않는 `SwapX` 라우터를 모두 처리하려면 반환된 출력 금액에 의존하지 말고 대신 스왑 전후의 잔액을 비교하십시오(전송 수수료 토큰과 유사).

# [L-01] V2를 통한 스왑 시 데드라인(deadline) 확인 안 함

_해결됨 (Resolved)_

`swapTokens()` 함수에서 `swapParams.useV3`가 false일 때 `ISwapX.swapV2ExactIn()`이 호출됩니다. 이 함수는 `swapParams.deadline`이 지났는지 확인하지 않아 스왑이 예상보다 늦게 더 나쁜 가격으로 실행될 수 있습니다.

스왑이 V2를 사용할 때 `swapTokens()` 함수에 데드라인 확인을 추가하는 것이 좋습니다.

```diff
        } else {
            if (swapParams.poolAddress == address(0)) {
                revert ZeroAddress();
            }

+			if (block.timestamp > swapParams.deadline) {
+				revert TransactionTooOld();
+			}
```

# [L-02] 비상 출금이 `HYPE`를 복구할 수 없음

_해결됨 (Resolved)_

`emergencyWithdrawToken()`은 비상시 계약에서 토큰을 인출하기 위해 호출되는 함수입니다. 그러나 기본 토큰을 복구하는 로직은 구현되어 있지 않습니다.

`emergencyWithdrawToken()` 함수에 기본 토큰을 복구하는 메커니즘을 구현하는 것이 좋습니다.

# [L-03] `tokenIn`이 `WETH`일 때 스왑이 되돌려짐

_해결됨 (Resolved)_

`tokenIn`이 `WETH`일 때 `SwapX.swapV3ExactIn()`은 기본 토큰을 받을 것으로 예상합니다.

```solidity
File: SwapX.sol

        if (params.tokenIn == WETH || params.tokenIn == address(0)) {
            params.tokenIn = WETH;
            require(msg.value >= params.amountIn, "SwapX: amount in and value mismatch");
```

그러나 `FunnelVaultUpgradeable.swapTokens()`에서는 `tokenIn`이 `WETH`일 때 `swapV3ExactIn()` 호출 시 기본 토큰이 전송되지 않고 대신 `WETH`가 `SwapX`에 대해 승인되어 트랜잭션이 되돌려짐(revert)을 유발합니다.

`withdrawNative()`를 호출하고 `tokenIn`으로 `address(0)`를 사용하여 이 제한을 우회할 수 있지만, 이것이 예상된 동작인지는 불분명합니다. 그렇다면 `tokenIn`이 `WETH`일 때 명시적인 오류 메시지와 함께 조기에 되돌리는 것을 고려하십시오. 그렇지 않으면 다음 변경 사항을 적용하는 것을 고려하십시오:

```diff
        if (swapParams.tokenIn == address(0)) {
            if (swapParams.amountIn > address(this).balance) {
                revert InsufficientBalance();
            }
        } else {
            if (swapParams.amountIn > IERC20(swapParams.tokenIn).balanceOf(address(this))) {
                revert InsufficientTokenBalance();
            }
-           IERC20(swapParams.tokenIn).forceApprove(swapRouter, swapParams.amountIn);
+           if (swapParams.tokenIn == WETH) {
+				IWETH(WETH).withdraw(swapParams.amountIn);
+			} else {
+				IERC20(swapParams.tokenIn).forceApprove(swapRouter, swapParams.amountIn);
+			}
        }

(...)

-           if (params.tokenIn == address(0)) {
+			if (params.tokenIn == address(0) || params.tokenIn == WETH) {
                amountOut = ISwapX(swapRouter).swapV3ExactIn{value: params.amountIn}(params);
```

# [L-04] 반올림 오류로 인한 미분배 수수료 먼지(dust)

_해결됨 (Resolved)_

`splitFeesCollected()`는 구성된 수수료 비율에 따라 수수료를 프로토콜 및 유틸리티 수수료로 나눕니다. 결과적인 수수료는 수수료 계산의 반올림 오류로 인해 미분배 먼지 금액을 남길 수 있습니다.

토큰의 가치 평가와 소수점 정밀도에 따라 이러한 먼지 금액의 축적은 시간이 지남에 따라 중요할 수 있습니다.

두 수수료의 비율이 항상 합해서 100%가 되도록 보장되므로 모든 수수료의 분배를 보장하기 위해 다음 변경 사항을 적용하는 것이 좋습니다:

```diff
    function splitFeesCollected(uint256 amount, FeeSplit memory feeSplit) internal view returns (uint256 protocolFee, uint256 utilityFee) {
        protocolFee = (feeSplit.protocolFeePercentage * amount) / FEE_PERCENTAGE_PRECISION;
-       utilityFee = (feeSplit.utilityFeePercentage * amount) / FEE_PERCENTAGE_PRECISION;
+       utilityFee = amount - protocolFee;
    }
```

# [L-05] 생성자에서 `_disableInitializers()` 호출 누락

_해결됨 (Resolved)_

`FunnelVaultUpgradeable`의 생성자는 [`_disableInitializers()`](https://docs.openzeppelin.com/upgrades-plugins/writing-upgradeable#initializing_the_implementation_contract)를 호출하지 않아 구현 계약의 의도치 않은 초기화를 허용할 수 있습니다.

생성자에서 `_disableInitializers()`를 호출하는 것이 좋습니다.

# [L-06] 잘못된 `to` 주소

_해결됨 (Resolved)_

`sendNativeTokenToLayer1` 함수에 의해 방출된 `NativeTokenSentToLayer1` 이벤트에서 `to` 매개변수는 시스템 주소 `0x2222222222222222222222222222222222222222`로 설정됩니다. 그러나 L1에서 자금의 수신자는 `address(this)`입니다.

실제 수신자 주소를 반영하도록 이벤트를 업데이트하거나, 항상 동일한 값을 가지므로 `to` 매개변수를 제거하는 것을 고려하십시오.

# [L-07] 수수료 처리 시 비효과적인 토큰 소각 메커니즘

_해결됨 (Resolved)_

이 버그는 총 공급량을 적절하게 줄이는 대신 `address(0)`로 토큰을 전송하여 "소각"하려고 할 때 발생합니다. 이는 진정한 소각을 달성하지 못하고 보고된 총 공급량을 부풀린 채로 유지하면서 접근할 수 없는 토큰을 단순히 재배치하는 것입니다.

ERC20 토큰을 `address(0)`(또는 데드 주소)로 전송하면 적절한 `burn` 함수를 호출하지 않고 접근할 수 없는 잔액으로 이동하므로, `totalSupply`는 (900으로) 줄어드는 진정한 소각과 달리 (100을 "소각"한 후에도 총 1,000이 유지되는 등) 변경되지 않고 유지됩니다. 이것은 실제 소각이 아닙니다. 토큰은 공급 지표에 포함되지만 갇혀 있습니다.

문제는 내부 `addToClaimer` 함수, 특히 `claimProtocolFees`에서 호출되는 `if(claimer == address(0))` 조건부 블록 내의 `IERC20(token).safeTransfer(address(0), amount);` 줄에 있습니다.

이는 `protocolFeeClaimer` 또는 풀의 `claimer`가 `address(0)`로 구성된 경우(향후 업그레이드, 관리자 설정자 또는 로직 변경을 통해 가능), 수수료를 분할할 때 `claimProtocolFees` 중에 트리거되어 소각 대신 부적절한 전송을 유발합니다.

한 가지 분명한 영향은 `address(0)`에서 잠재적인 의도치 않은 복구를 가능하게 하는 반면, 기본 ETH(`token == address(0)`)는 소각되거나 되돌려지지 않고 조용히 실패하여 자금이 설명되지 않은 채로 남게 된다는 것입니다.

`burnToken`의 0xdead 주소 처리와의 이러한 불일치는 토큰경제학을 약화시키고 수수료 관리에서 공급 무결성을 위험에 빠뜨립니다.

권장 사항은 제로 청구자(zero-claimer) 분기를 리팩토링하여 의사 소각(pseudo-burns)을 위해 일관된 데드 주소(예: 0x000...dEaD)를 사용하고, 기본 ETH에 대한 명시적 되돌림(`if (token == address(0)) revert;`)을 추가하고, 유효성 검사 또는 불변 설정자를 통해 제로 청구자를 방지하는 것입니다. 토큰이 지원하는 경우 진정한 공급 감소를 위해 `burn(amount)`를 호출하는 것을 선호합니다.

# [L-08] `protocolFeeClaimer`에 대한 설정자 누락

_해결됨 (Resolved)_

funnel vault 계약에서 `protocolFeeClaimer`는 초기화 시 관리자로만 설정되며 설정자 함수가 없습니다. 관리자 주소가 변경되거나(예: 역할 취소/새 주소로 부여) 청구자 키가 분실/손상된 경우, 이전 청구자만 claimFees를 호출할 수 있으므로 addToClaimer를 통해 추가된 프로토콜 수수료는 영원히 청구할 수 없게 됩니다.

관리자는 비상 출금을 할 수 있지만, 이는 회계를 포기하여(알려진 문제) 초과 청구 또는 상태 손상을 초래할 위험이 있습니다.

이로 인해 청구자가 변경되면 프로토콜 수수료가 영구적으로 잠기게 되어 프로토콜 지속 가능성에 영향을 미칩니다.

발생 경위는 다음과 같습니다:

- Init가 청구자=관리자로 설정합니다.
- 초기 관리자로부터 DEFAULT_ADMIN_ROLE을 취소하고 새 관리자에게 부여합니다.
- 프로토콜 수수료 발생 -> 이전 청구자에게 추가됨. 새 관리자는 청구할 수 없으며 수수료가 잠깁니다.

권장 사항은 `newClaimer != address(0)` 확인과 함께 `DEFAULT_ADMIN_ROLE`로 제한된 `setProtocolFeeClaimer(address newClaimer)`를 추가하는 것입니다.

# [L-09] 초기화되지 않은 수수료 분할로 인한 수수료 회계 손실

_해결됨 (Resolved)_

수수료 분할 구성 `poolFeeSplitsBeforeMigration` 및 `poolFeeSplitsAfterMigration`은 `initialize()` 함수에서 초기화되지 않아 기본값 (0,0)이 됩니다. `claimProtocolFees()`가 실행될 때 이 0 비율로 `splitFeesCollected()`를 호출합니다:

```solidity
(uint256 protocolFee, uint256 utilityFee) = splitFeesCollected(amount0, poolFeeSplitsBeforeMigration);
// With (0,0) split: protocolFee = (0 * amount0) / 10000 = 0
//                   utilityFee = (0 * amount0) / 10000 = 0
```

토큰은 `collectProtocol()`을 통해 uniswap 풀에서 성공적으로 수집되지만 `addToPoolHIP()` 및 `addToClaimer()` 호출 모두 0 금액을 받아 수집된 수수료가 금고의 잔액에서 추적되지 않은 상태로 남습니다.

이는 관리자가 `setPoolFeeSplits()`를 통해 수수료 분할을 수동으로 설정하기 전에 `claimProtocolFees()`가 호출될 때마다 발생합니다. 계약은 새 수수료 분할의 합계가 10,000이 되도록 강제하지만 기본값 (0,0) 사용을 방지하지는 않습니다.

이런 식으로 **수집된 모든 프로토콜 수수료는 금고 회계에서 영구적으로 추적되지 않게 되어 수동 복구 전까지 프로토콜의 수수료 분배 시스템에서 효과적으로 사라집니다.**

**권장 사항**

계약 초기화 중에 합리적인 기본값으로 수수료 분할을 초기화하십시오.

```solidity
function initialize(address admin, address executer) external initializer {
    // ... existing code ...
    
    // Initialize fee splits per comments: 25%/75% before, 50%/50% after
    poolFeeSplitsBeforeMigration = FeeSplit(2500, 7500);
    poolFeeSplitsAfterMigration = FeeSplit(5000, 5000);
}
```

이렇게 하면 별도의 구성 트랜잭션 없이 배포부터 수수료 회계가 올바르게 작동합니다.

# [L-10] burnToken이 토큰 전송 성공을 확인하지 않음

_해결됨 (Resolved)_

`FunnelVaultUpgradeable`의 `burnToken()` 함수는 소각 주소로의 직접적인 ERC-20 전송을 사용하지만 반환 값을 확인하지 않습니다. 토큰이 ERC-20 표준을 엄격하게 따르지 않는 경우(예: 실패 시 false를 반환하는 비준수 토큰), 금고는 실패하더라도 소각이 성공했다고 가정합니다.

이러한 경우 매핑은 감소하고 이벤트가 방출되지만 토큰은 계약에 남아 있습니다. 이로 인해 토큰 자금이 금고에 갇히고(_금고는 소각되었다고 생각하므로_) 잠재적인 회계 불일치가 발생합니다. 예를 들어, USDT(실패 시 되돌리지 않음)와 같은 토큰은 전송에 조용히 실패하여 금고에 burnFees에서 더 이상 추적되지 않는 토큰을 남길 수 있습니다.

권장 사항은 전송이 성공했는지 확인하고 실패하면 trx를 되돌리는 확인을 추가하는 것입니다.

# [L-11] FunnelVault가 모든 기부/프로토콜 수수료를 지출할 수 없음

_인정됨 (Acknowledged)_

`FunnelVault`가 기부/프로토콜 수수료를 스왑해야 할 때마다 새로운 프로토콜 수수료가 다시 생성되며, 금고는 이를 청구하고 결국 스왑해야 합니다.

`FunnelVault`가 스왑 수수료 지불을 면제받을 수 있는 방법을 추가하는 것을 고려하십시오.

# [L-12] `withdrawNative` 및 `depositNative`를 WHYPE/HYPE에 사용할 수 없음

_해결됨 (Resolved)_

소각(Burn) 풀에서 토큰을 래핑/언래핑할 수 없습니다.

이를 허용하기 위해 해당 함수에 `isBurn` 매개변수를 추가하는 것을 고려하십시오.
