# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**FydeTreasury/veFyde** 및 **FydeTreasury/liquid-vault** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Fyde 소개 (About Fyde)

Fyde의 Fyde Liquid Vaults는 사용자가 다양한 토큰을 입금하여 소유권을 유지하면서 향상된 성능, 수익률 및 유동성을 얻을 수 있도록 하며, 입금된 자산의 가치를 나타내는 유동성 금고 토큰인 $TRSY를 받습니다. veFyde는 자산의 화이트리스트를 관리하고, 가중치와 배출량을 추적하며, 소유자가 에포크(epoch)를 업데이트하고 완료하여 투표 잔액에 따라 보상을 분배할 수 있도록 합니다.

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

**_검토 커밋 해시_:**

- [9148db3e070235465257268bcb0bc333c1d4313a](https://github.com/FydeTreasury/veFyde/tree/9148db3e070235465257268bcb0bc333c1d4313a)
- [6bf1be3dec4b6c9e33929a51bf07539a4ced74f2](https://github.com/FydeTreasury/liquid-vault/tree/6bf1be3dec4b6c9e33929a51bf07539a4ced74f2)

**_수정 검토 커밋 해시_:**

- [5c90a9c06866d1f3572c5463eed919b751aed21a](https://github.com/FydeTreasury/veFyde/tree/5c90a9c06866d1f3572c5463eed919b751aed21a)
- [4b169e850b889e82523662a6dcd76cc02a4000ae](https://github.com/FydeTreasury/liquid-vault/tree/4b169e850b889e82523662a6dcd76cc02a4000ae)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

`veFyde` 내:

- `VoteEscrow`
- `Fyde`
- `FeeDistributor`
- `AssetGuage`

`liquid-vault` 내:

- `RewardsDistributor`
- `YieldManager`
- `YieldToken`
- `YieldTokenFactory`
- `YieldStrategy`
- `sTRSY`
- `OracleModule`
- `TaxModule`

# 발견 사항 (Findings)

# [H-01] `RewardsDistributor` 계산이 제대로 작동하지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`RewardsDistributor` 계약의 소유자는 `setBoostPeriods`를 호출하여 더 높은 보상 승수를 위한 부스트 기간을 설정할 수 있습니다. 이 기간은 현재 보상률과 그에 따른 토큰당 보상을 계산할 때 `rewardRate` 로직에서 사용됩니다.

스폰서의 예에 따르면, 이러한 부스트 기간에 대한 일반적인 구성은 일련의 부스트 기간을 통해 시간이 지남에 따라 승수를 줄이는 것입니다. 이에 대한 아이디어는 초기 참여자에게 보상하기 위해 더 높은 부스트로 시작하고 시간이 지남에 따라 부스트 승수를 줄이는 것입니다.

그러나 현재 `rewardRate` 로직은 예상대로 작동하지 않습니다. 예를 들어, 타임스탬프 10에서 승수 5로 시작하는 부스트 기간과 타임스탬프 20에서 승수 1로 시작하는 또 다른 부스트 기간이 있다고 가정해 보겠습니다. 또한 마지막 업데이트 시간이 0에서 시작하고 현재 시간이 15라고 가정해 보겠습니다.

지금 보상률이 계산되면 루프의 첫 번째 반복에서 다음과 같은 결과가 발생합니다:

```
currTime = 15
nextTimestamp = 15
lastUpdate = 10
timeInPeriod = 15 - 10 = 5
totalTime = 5
rateMultiplier = 0 + 5*5 = 25
```

`(lastUpdateTime > boostPeriods[i].timeStamp) == false`이므로 루프에서 다음 부스트 기간으로 계속 진행합니다:

```
currTime = 15 - 5 = 10
nextTimestamp = 20
lastUpdate = 20
timeInPeriod = 20 - 20 = 0
...
```

이 모든 것은 의도한 대로 작동하며 보상률이 올바르게 계산됩니다. `lastUpdateTime` 변수는 이제 15(블록 타임스탬프)로 설정됩니다.

이제 누군가 이 함수를 다음에 호출할 때 블록 타임스탬프가 25라고 가정해 보겠습니다. 따라서 우리는 1x 승수 기간에 있어야 합니다. 루프의 첫 번째 반복에서는 다음과 같습니다:

```
currTime = 25
nextTimestamp = 25
lastUpdate = 15
timeInPeriod = 25 - 15 = 10
totalTime = 10
rateMultiplier = 0 + 10*5 = 50
```

기간 내 시간이 잘못 계산되었을 뿐만 아니라(우리는 10초가 아니라 5초 동안만 기간에 있었음), `(lastUpdateTime > boostPeriods[i].timeStamp) == true`이므로 루프를 중단합니다.

이제 우리는 두 번째 부스트 기간에 들어갔으므로 1x로 보상해야 하지만 5x로 모두에게 보상하고 있습니다. 이 점은 여러 부스트 기간이 있는 모든 구성에 적용됩니다. 첫 번째 부스트 기간을 시작하면 항상 일찍 종료하므로 루프에서 첫 번째 부스트 기간을 지나지 못합니다.

일반적으로 이 함수는 여러 축에 걸쳐 의도한 대로 수행되지 않는 것 같습니다.

## 권장 사항

`rewardRate` 로직은 여러 부스트 기간을 적절하게 처리하고 일반적으로 기간 경계에 걸쳐 올바르게 계산하도록 업데이트되어야 합니다. 또한 `setBoostPeriods`에서 부스트 기간의 시간순 정렬을 강제할 것을 제안합니다.

여기에는 관련 테스트 모음도 있어야 합니다. 퍼즈(fuzz) 테스팅이 좋은 옵션일 수 있습니다.

# [H-02] 함수를 호출할 수 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`YieldToken` 구현은 `YieldTokenFactory` 생성자 내에서 생성됩니다:

```solidity
  constructor(address _fyde, address _relayer) {
    TOKEN_IMPLEMENTATION = address(new YieldToken(address(this), _fyde, _relayer));
  }
```

`YieldManager` 계약은 `YieldTokenFactory` 계약을 상속합니다:

```solidity
  constructor() YieldTokenFactory(_fyde, _relayer) Ownable(msg.sender) ERC1967Proxy(_stratgies, "") {}
```

따라서 `YieldManager`는 `msg.sender`이자 구현의 `owner`이므로 `YieldToken` 구현의 소유자가 됩니다:

```solidity
  constructor(address _yieldManager, address _fyde, address _relayer) Ownable(msg.sender) {
    yieldManager = _yieldManager;
    fyde = _fyde;
    relayer = _relayer;
    baseYieldToken = IYieldToken(address(this));
  }
```

문제는 `YieldToken` 계약에 `owner`(즉, `YieldManager`)만 호출할 수 있는 두 개의 함수가 포함되어 있다는 것입니다:

```solidity
  function setYieldManager(address _yieldManager) public onlyOwner {
    yieldManager = _yieldManager;
  }

  function setRelayer(address _relayer) public onlyOwner {
    relayer = _relayer;
  }
```

그러나 `YieldManager`는 그렇게 할 수 있는 메커니즘을 제공하지 않으므로 이러한 함수를 호출할 수 없습니다.

## 권장 사항

`YieldToken` 구현의 소유권을 `YieldManager.owner()`로 이전하는 것을 고려하십시오.

# [M-01] EtherFi에서 출금할 방법이 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`YieldStrategy.sol` 계약에는 호출자가 EtherFi에 WETH를 입금하여 WEETH를 얻을 수 있는 `depositWETHInEtherfi` 메서드가 있습니다.

그러나 그 반대 작업을 수행할 수 있는 방법은 현재 없습니다... WEETH를 상환하여 기본 WETH를 인출하는 것입니다. 따라서 원래 WETH를 되찾는 유일한 방법은 1INCH를 통해 스왑하는 것이지만, 이는 WEETH에서 WETH로의 경로에 필요한 관련 풀의 가용 유동성에 전적으로 의존합니다. sDAI 및 Pendle에 대해 가지고 있는 것과 같은 `withdraw` 등가물을 왜 원하지 않는지 확실하지 않습니다.

## 권장 사항

`withdrawWETHInEtherfi` 메서드를 추가하십시오.

# [M-02] Pendle에 입금할 때 슬리피지(slippage) 확인 없음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`YieldStrategy.depositToPendle()`을 호출할 때 `router.swapExactTokenForPt()`가 호출되면 슬리피지가 0으로 설정됩니다.

```
function depositToPendle(address _underlying, address _market, uint256 _amount)
    external
    returns (uint256)
  {
    (uint256 netPtOut,,) = router.swapExactTokenForPt(
      address(this),
      _market,
>     0,
      defaultApprox(),
      createTokenInputStruct(_underlying, _amount),
      emptyLimit()
    );
    return netPtOut;
  }
```

이것은 Pendle IPActionSwapPTV3 인터페이스의 `swapExactTokenForPt`입니다:

```
function swapExactTokenForPt(
        address receiver,
        address market,
        uint256 minPtOut,
        ApproxParams calldata guessPtOut,
        TokenInput calldata input,
        LimitOrderData calldata limit
    ) external payable returns (uint256 netPtOut, uint256 netSyFee, uint256 netSyInterm);
```

`minPtOut`은 항상 0으로 설정됩니다.

## 권장 사항

`depositToPendle()`을 호출할 때 `minPtOut`을 매개변수로 설정하십시오.

# [M-03] `swapOn1INCH`에서 저수준 호출(Low-level call)이 확인되지 않음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`YieldStrategy.swapOn1INCH()`를 호출할 때 저수준 성공 호출이 확인되지 않습니다. 스왑이 실패하더라도 호출자가 눈치채지 못할 수 있습니다. 영향은 `_minAmountOut`에 의해 완화되지만 `_minAmountOut`이 0으로 설정되면 확인되지 않은 반환 호출 값으로 인해 부당한 문제가 발생할 수 있습니다.

```
  function swapOn1INCH(
    address _assetIn,
    uint256 _amountIn,
    address _assetOut,
    uint256 _minAmountOut,
    bytes calldata _swapData
  ) external {
    IERC20(_assetIn).approve(ONEINCH_AGGREGATION_ROUTER, _amountIn);
>   (bool success, bytes memory data) = ONEINCH_AGGREGATION_ROUTER.call(_swapData);
    require(IERC20(_assetOut).balanceOf(address(this)) >= _minAmountOut, "Slippage Exceeded");
  }
```

## 권장 사항

스왑 함수에 확인을 추가하십시오.

```
  function swapOn1INCH(
    address _assetIn,
    uint256 _amountIn,
    address _assetOut,
    uint256 _minAmountOut,
    bytes calldata _swapData
  ) external {
    IERC20(_assetIn).approve(ONEINCH_AGGREGATION_ROUTER, _amountIn);
    (bool success, bytes memory data) = ONEINCH_AGGREGATION_ROUTER.call(_swapData);
+   require(success, "Swap Failed");
    require(IERC20(_assetOut).balanceOf(address(this)) >= _minAmountOut, "Slippage Exceeded");
  }
```

# [M-04] `depositWETHInEtherfi()`는 1e18만 입금하도록 제한됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

YieldManager 소유자는 `depositWETHInEtherfi()` 함수를 사용하여 WETH를 `eETHliquidityPool`에 입금할 수 있습니다:

```solidity
  function depositWETHInEtherfi(uint256 _amount) external {
    WETH.withdraw(_amount);
    eETHliquidityPool.deposit{value: 1e18}();
    uint256 eethBal = eETH.balanceOf(address(this));
    eETH.approve(address(weeth), eethBal);
    weeth.wrap(eethBal);
  }
```

- 현재 이 함수는 WETH 계약에서 ETH를 인출하고 유동성 풀에 1e18의 고정 가치를 입금합니다. 남은 ETH는 계약에 무기한 잠깁니다.
  - 예를 들어, 소유자가 2e18의 금액으로 `depositWETHInEtherfi()`를 호출하면 계약은 WETH 계약에서 2e18 ETH를 인출하지만(1 WETH = 1 ETH이므로) 유동성 풀에는 1e18만 입금합니다.
  - 남은 1e18 ETH는 계약에 갇히게 됩니다.

## 권장 사항

이 문제를 해결하려면 전체 지정된 금액 `_amount`를 유동성 풀에 입금하도록 `depositWETHInEtherfi()` 함수를 업데이트하십시오:

```solidity
  function depositWETHInEtherfi(uint256 _amount) external {
    WETH.withdraw(_amount);
    eETHliquidityPool.deposit{value: _amount}();
    uint256 eethBal = eETH.balanceOf(address(this));
    eETH.approve(address(weeth), eethBal);
    weeth.wrap(eethBal);
  }
```

# [M-05] `swapOn1INCH()` 슬리피지가 효과가 없을 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`swapOn1INCH()` 함수는 다음과 같이 슬리피지를 확인하려고 시도합니다:

```solidity
  function swapOn1INCH(
    address _assetIn,
    uint256 _amountIn,
    address _assetOut,
    uint256 _minAmountOut,
    bytes calldata _swapData
  ) external {
    IERC20(_assetIn).approve(ONEINCH_AGGREGATION_ROUTER, _amountIn);
    (bool success, bytes memory data) = ONEINCH_AGGREGATION_ROUTER.call(_swapData);
    require(IERC20(_assetOut).balanceOf(address(this)) >= _minAmountOut, "Slippage Exceeded");
  }
```

이 슬리피지 확인은 계약에 이미 `_assetOut` 토큰이 있는 경우 효과가 없을 수 있습니다. 문제를 설명하기 위한 간단한 예는 다음과 같습니다:

- 소유자가 token1 1000개를 token2로 교환하려고 합니다.
  - 계약에 이미 token2 500개가 있다고 가정합니다.
- 소유자는 `_minAmountOut`을 995로 설정합니다.
- 스왑은 token1 1000개에 대해 token2 500개를 반환합니다.
- 총 token2 잔액(1000)이 `_minAmountOut`(995)을 초과하므로 슬리피지 확인이 통과됩니다.

## 권장 사항

슬리피지 확인이 효과적이도록 `swapOn1INCH()` 함수를 다음과 같이 업데이트하는 것을 고려하십시오:

```solidity
  function swapOn1INCH(
    address _assetIn,
    uint256 _amountIn,
    address _assetOut,
    uint256 _minAmountOut,
    bytes calldata _swapData
  ) external {
    uint256 balBefore = IERC20(_assetOut).balanceOf(address(this));
    IERC20(_assetIn).approve(ONEINCH_AGGREGATION_ROUTER, _amountIn);
    (bool success, bytes memory data) = ONEINCH_AGGREGATION_ROUTER.call(_swapData);
    uint256 balAfter = IERC20(_assetOut).balanceOf(address(this));
    require((balAfter - balBefore) >= _minAmountOut, "Slippage Exceeded");
  }
```

# [M-06] YieldManager가 슬리피지를 0으로 하드코딩함

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

YieldManager 함수` moveAssetToYieldManager()`, `moveAssetToLiquidVault()`, 및 `depositYieldIntoLiquidVault()`의 현재 구현은 슬리피지 제어를 0으로 하드코딩합니다. 즉, 슬리피지 제어를 위한 매개변수가 없으므로 모든 함수 실행이 상당한 손실을 입을 수 있습니다. 슬리피지를 0으로 설정하면 이러한 함수는 중계자(relayer)가 스왑이나 입금에 대해 반환하는 금액을 그대로 수락하므로 불리한 결과가 발생할 수 있습니다.

`YieldStrategy.depositToPendle()` 함수에도 동일한 문제가 존재합니다.

## 권장 사항

슬리피지 제어를 보장하기 위해 영향을 받는 모든 함수에 `amountOutMinimum` 매개변수를 도입하십시오.

# [M-07] 비상시 자산 복구가 회계에 오류를 일으킬 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`recoverAsset` 함수는 실수로 계약에 전송된 ERC20을 인출하는 데 사용됩니다. 그러나 이 함수는 모든 주소를 허용합니다. 관리자가 **TRSY** 주소를 전달하면 계약에서 **TRSY**를 추출할 수 있는데, 이는 발생해서는 안 됩니다.

또한 비상 상황이 발생하여 관리자가 **TRSY**를 원한다고 가정해 보겠습니다. 인출된 금액이 차감되지 않으므로 `cumulativeFees`는 잘못된 값을 갖게 됩니다. 따라서 전체 프로토콜에 회계 오류가 발생합니다.

## 권장 사항

**TRSY**인 경우 `recoverAsset`에서 `cumulativeFees`를 업데이트하십시오.

# [M-08] 부스트 기간을 업데이트하면 보상이 손실될 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`claimFydeEmissions`는 사용자의 보상을 청구하는 데 사용됩니다. 사용자 보상을 계산하기 위해 `earned` 뷰 함수가 사용됩니다.

```solidity
  /// @notice Withdraws all earned rewards
  function claimFydeEmissions() external {
		.
		.
		.
    uint256 reward = earned(msg.sender, accountBalance, rewardPerToken_, rewards[msg.sender]);
```

[RewardsDistributor.sol:L133](https://github.com/FydeTreasury/liquid-vault/blob/6bf1be3dec4b6c9e33929a51bf07539a4ced74f2/src/yield/RewardsDistributor.sol#L133)

`earned` 함수는 `_rewardPerToken`을 기반으로 만료된 보상을 계산합니다.

```solidity
      _userReward + _userBalance * (_rewardPerToken - userRewardPerTokenPaid[_user]) / PRECISION;
```

[RewardsDistributor.sol:L171](https://github.com/FydeTreasury/liquid-vault/blob/6bf1be3dec4b6c9e33929a51bf07539a4ced74f2/src/yield/RewardsDistributor.sol#L171)

`_rewardPerToken`은 부스트 기간(설정된 경우)을 기반으로 합니다.

```solidity
  function rewardPerToken() public view returns (uint256) {
    return totalSupply == 0
      ? rewardPerTokenStored
      : rewardPerTokenStored
        + (PRECISION * (block.timestamp - lastUpdateTime) * rewardRate() / totalSupply);
  }
```

[RewardsDistributor.sol#L158-L163](https://github.com/FydeTreasury/liquid-vault/blob/6bf1be3dec4b6c9e33929a51bf07539a4ced74f2/src/yield/RewardsDistributor.sol#L158-L163)

관리자가 부스트 기간을 업데이트하면 기한이 지난 보상을 청구하지 않은 사용자는 이를 잃을 수 있습니다.

## 권장 사항

현재의 부스트 기간 로직 설계를 변경해야 합니다. 예를 들어, 부스트 기간이 변경될 예정인 경우 보상 청구를 비활성화하십시오. 변경 후에도 사용자가 여전히 보상을 청구할 수 있도록 이전 부스트 기간을 독립적으로 추적하십시오.

또 다른 제안은 부스트 기간을 업데이트하기 전에 사용자가 보상을 청구할 수 있는 유예 기간을 설정하는 것입니다.

# [M-09] boostPeriods 설계로 인한 보상 손실

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`rewardRate()`는 다음과 같이 boostPeriods를 기반으로 계산됩니다:

```solidity
    for (uint256 i; i < boostPeriods.length; ++i) {
      currTime = currTime - timeInPeriod;
      uint256 nextTimeStamp = // if boostPeriods[i].timeStamp in the past, take the current time
        currTime > boostPeriods[i].timeStamp ? currTime : boostPeriods[i].timeStamp;
      uint256 lastUpdate =
        boostPeriods[i].timeStamp > lastUpdateTime ? boostPeriods[i].timeStamp : lastUpdateTime;
      timeInPeriod = nextTimeStamp - lastUpdate;
      totalTime += timeInPeriod;

      rateMultiplier += timeInPeriod * boostPeriods[i].multiplier;
      if (lastUpdateTime > boostPeriods[i].timeStamp) break;
    }
```

[RewardsDistributor.sol:L178](https://github.com/FydeTreasury/liquid-vault/blob/6bf1be3dec4b6c9e33929a51bf07539a4ced74f2/src/yield/RewardsDistributor.sol#L178)

boostPeriod가 과거인 경우 만료되었음을 의미하므로 대신 현재 시간을 사용합니다. 그러나 사용자가 부스트 기간 동안 청구하지 않은 경우 사용자는 부스트된 보상을 잃고 일반 요율(currentTime - lastUpdateTime)에 따라서만 보상을 받게 됩니다.

사용자가 부스트 기간 동안 보상을 청구하더라도 항상 약간의 손실이 발생합니다. 이는 사용자가 **boostPeriods[i].timeStamp**가 만료되기 직전에 보상을 청구한다고 보장할 수 없기 때문입니다. 이 문제는 사용자에게 보상 분배 측면에서 불공정을 초래합니다.

## 권장 사항

부스트된 보상 계산은 사용자별 일반 보상과 독립적으로 추적되어야 합니다.

# [L-01] `unsafeTransfer`로 인해 cumulativeFees가 증가함

**FeeDistributor**의 `Deposit` 함수는 다음과 같이 TRSY 전송을 수행합니다:

```solidity
function deposit(address from, uint256 amount) external onlyOwner {
    ERC20(TRSY).transferFrom(from, address(this), amount);
    cumulativeFees += amount;
  }
```

[FeeDistributor.sol:L81-L84](https://github.com/FydeTreasury/veFyde/blob/9148db3e070235465257268bcb0bc333c1d4313a/src/FeeDistributor.sol#L81-L84)

[TRSY](https://github.com/FydeTreasury/liquid-vault/blob/6bf1be3dec4b6c9e33929a51bf07539a4ced74f2/src/core/TRSY.sol#L8)의 구현을 보면 Solmate의 표준 ERC20 구현을 사용합니다.

따라서 transferFrom은 되돌리지 않으며 성공/실패에 따라 true/false만 반환합니다. 그러나 반환된 값에 대한 확인이 없습니다.

결과적으로 조용한 전송 실패가 가능하며 계약으로 실제 전송된 금액 없이 cumulativeFees가 증가합니다.

반환된 값을 확인하고 false이면 되돌리십시오. 그렇지 않으면 SafeTransferLib의 `safeTransferFrom`을 사용하십시오.

코드베이스의 다른 인스턴스에도 동일한 수정을 적용하십시오.

# [L-02] `boostPeriods`가 미래 타임스탬프로 설정되면 `rewardRate`가 0을 반환함

`rewardRate`가 항상 0을 반환할 가능성이 있습니다. 이는 관리자가 boostPeriods를 미래 시간으로 설정하는 경우입니다. 따라서 block.timestamp(현재 시간)보다 커져 timeInPeriod가 항상 0이 되고, 결과적으로 totalTime도 0이 되어 rewardRate가 0을 반환하게 됩니다.

```solidity
      uint256 nextTimeStamp = // if boostPeriods[i].timeStamp in the past, take the current time
        currTime > boostPeriods[i].timeStamp ? currTime : boostPeriods[i].timeStamp;
      uint256 lastUpdate =
        boostPeriods[i].timeStamp > lastUpdateTime ? boostPeriods[i].timeStamp : lastUpdateTime;
      timeInPeriod = nextTimeStamp - lastUpdate;
      totalTime += timeInPeriod;
```

이는 부스트 기간이 꺼져 있을 때 일반 요율 계산(현재 시간-lastUpdateTime 기반)이 여전히 작동해야 하기 때문에 문제가 됩니다. 그러나 그렇지 않습니다.

모든 boostPeriods가 미래에 설정되어 있는지 확인하고 일반 요율 계산으로 대체(fallback)하십시오.

# [L-03] 부스트 기간 설정 시 순서 유효성 검사 부족

부스트 기간 설정 시 순서 유효성 검사가 부족합니다. 순서가 잘못 설정되면 다음 조건으로 인해 `rewardRate()`가 잘못된 결과를 제공할 수 있습니다:

```solidity
      if (lastUpdateTime > boostPeriods[i].timeStamp) break;
```

[RewardsDistributor.sol:L193](https://github.com/FydeTreasury/liquid-vault/blob/6bf1be3dec4b6c9e33929a51bf07539a4ced74f2/src/yield/RewardsDistributor.sol#L193)
