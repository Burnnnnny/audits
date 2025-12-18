# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **de-swarm/contracts.hyperbloom.xyz** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Hyperbloom 정보 (About Hyperbloom)

Hyperbloom은 유동성 풀, AMM 및 이자 농사 기회에서 보상을 자동으로 극대화하여 사용자가 토큰에 대한 복리 이자를 얻을 수 있도록 돕는 수익 최적화 도구(Yield Optimizer)입니다. Vault를 통해 Hyperbloom은 팜 보상을 예치된 자산으로 다시 복리화하여 언제든지 자금을 인출할 수 있도록 합니다. Hyperbloom은 Beefy Finance의 포크입니다.

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

_검토 커밋 해시_ - [7dba168e001e46188b0d83c2783daf741c7e1d2a](https://github.com/de-swarm/contracts.hyperbloom.xyz/tree/7dba168e001e46188b0d83c2783daf741c7e1d2a)

_수정 검토 커밋 해시_ - [ab2c86305e6baecb6c9d6f4d0883b773fd91d759](https://github.com/de-swarm/contracts.hyperbloom.xyz/tree/ab2c86305e6baecb6c9d6f4d0883b773fd91d759)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `contracts/BIFI/infra/` 
- `contracts/BIFI/interfaces/` 
- `contracts/BIFI/strategies/` 
- `contracts/BIFI/utils/` 
- `contracts/BIFI/vaults/` 
- `contracts/BIFI/zaps/` 

# 발견 사항 (Findings)

# [M-01] `withdraw` 작업은 샌드위치 공격에 취약함 (`withdraw` operation is prone to sandwich attacks)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`StrategyPassiveManagerHyperswap` 및 `StrategyPassiveManagerKittenswap`의 `withdraw` 작업 내부에서 `_onlyCalmPeriods`는 `calmAction`이 거짓이고 `lastDeposit`이 `block.timestamp`와 같을 때만 확인됩니다.

```solidity
    function withdraw(uint256 _amount0, uint256 _amount1) external {
        _onlyVault();

>>>     if (block.timestamp == lastDeposit && !calmAction) _onlyCalmPeriods();

        if (_amount0 > 0) IERC20Metadata(lpToken0).safeTransfer(vault, _amount0);
        if (_amount1 > 0) IERC20Metadata(lpToken1).safeTransfer(vault, _amount1);

        if (!_isPaused()) _addLiquidity();
    
        (uint256 bal0, uint256 bal1) = balances();
        calmAction = false;

        emit TVL(bal0, bal1);
    }
```

공격자는 소량(dust amount)을 입금하고 다음 블록을 기다린 다음 가격을 조작하기 위해 스왑을 수행하여 이를 악용할 수 있습니다. 그 후 `withdraw`를 호출하여 `_removeLiquidity` 및 `_addLiquidity`를 트리거하여 전략이 조작된 가격을 사용하여 유동성을 추가하도록 할 수 있습니다.

```solidity
    function _addLiquidity() private {
        _whenStrategyNotPaused();

        (uint256 bal0, uint256 bal1) = balancesOfThis();

        uint160 sqrtprice = sqrtPrice();
        uint128 liquidity = LiquidityAmounts.getLiquidityForAmounts(
            sqrtprice,
            TickMath.getSqrtRatioAtTick(positionMain.tickLower),
            TickMath.getSqrtRatioAtTick(positionMain.tickUpper),
            bal0,
            bal1
        );

        bool amountsOk = _checkAmounts(liquidity, positionMain.tickLower, positionMain.tickUpper);

>>>     if (liquidity > 0 && amountsOk) {
            minting = true;
            IKittenswapV3Pool(pool).mint(address(this), positionMain.tickLower, positionMain.tickUpper, liquidity, "Beefy Main");
        } else {
            if(!calmAction) _onlyCalmPeriods();
        }

        (bal0, bal1) = balancesOfThis();

        liquidity = LiquidityAmounts.getLiquidityForAmounts(
            sqrtprice,
            TickMath.getSqrtRatioAtTick(positionAlt.tickLower),
            TickMath.getSqrtRatioAtTick(positionAlt.tickUpper),
            bal0,
            bal1
        );

        // Flip minting to true and call the pool to mint the liquidity.
        if (liquidity > 0) {
            minting = true;
            IKittenswapV3Pool(pool).mint(address(this), positionAlt.tickLower, positionAlt.tickUpper, liquidity, "Beefy Alt");
        }
    }
```

이것은 `_addLiquidity` 내부에서 가격이 `positionMain` 틱 내에 유지되는 한 `_onlyCalmPeriods` 확인을 건너뛰기 때문에 발생할 수 있습니다.

## 권장 사항

`_addLiquidity`에 `onlyCalmPeriods` 수정자를 추가하십시오.

# [L-01] 금액이 0인지 확인하지 않고 전송 (Sending without checking if the amount is zero)

`StrategyPassiveManagerKittenswap._chargeFees`에서 `callFeeAmount`, `strategistFeeAmount`, `beefyFeeAmount` 값이 0인지 확인하지 않고 `safeTransfer`를 호출합니다. 기본 토큰(WHYPE)을 전송하므로 0을 전송해도 트랜잭션이 실패하지는 않지만 불필요하게 가스를 낭비하게 됩니다.

# [L-02] `shortTwap`에 대한 구성 가능한 시간 간격 부재 (Lack of configurable time interval for `shortTwap`)

현재 `shortTwap`의 시간 간격은 3초로 하드코딩되어 있습니다.

```solidity
    function shortTwap() public view returns (int56 twapTick) {
        uint32[] memory secondsAgo = new uint32[](2);
        secondsAgo[0] = uint32(3);
        secondsAgo[1] = 0;

        (int56[] memory tickCuml,) = IUniswapV3Pool(pool).observe(secondsAgo);
        twapTick = (tickCuml[1] - tickCuml[0]) / int32(3);
    }
```

`twap`에 대한 `twapInterval`이 3보다 작게 설정되면 `shortTwap`이 의도한 대로 작동하지 않습니다. `shortTwap`에 대한 twap 간격을 구성할 수 있도록 허용하는 것을 고려하십시오.

# [L-03] TWAP 계산 시 0으로 나누기 (Division by zero in TWAP calculation)

`StrategyPassiveManagerHyperswap` 계약에는 `twap()` 함수에 잠재적인 0으로 나누기 취약점이 포함되어 있습니다. 소유자는 `setTwapInterval()`을 사용하여 `twapInterval`을 0으로 설정할 수 있으며, 이로 인해 TWAP 계산이 되돌려지게 됩니다.

```solidity
function setTwapInterval(uint32 _interval) external onlyOwner {
    emit SetTwapInterval(twapInterval, _interval);
    twapInterval = _interval; // No validation - can be set to 0
}

function twap() public view returns (int56 twapTick) {
    uint32[] memory secondsAgo = new uint32[](2);
    secondsAgo[0] = uint32(twapInterval);
    secondsAgo[1] = 0;
    (int56[] memory tickCuml,) = IUniswapV3Pool(pool).observe(secondsAgo);
    twapTick = (tickCuml[1] - tickCuml[0]) / int32(twapInterval); // Division by zero if twapInterval = 0
}
```

`twapInterval`이 0으로 설정되면 `twap()`의 0으로 나누기로 인해 다음 중요 함수들이 되돌려집니다.

deposit(), withdraw(), harvest(), moveTicks(), setPositionWidth(), unpause()

이것은 `twapInterval`이 0이 아닌 값으로 설정될 때까지 전략의 핵심 기능을 사실상 중단시킵니다.

`setTwapInterval()`에 유효성 검사를 추가하여 간격을 0으로 설정하는 것을 방지함으로써 이 문제를 해결할 수 있습니다.
