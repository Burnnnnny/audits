# 정보
Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항
스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.
# 소개
**Level-Money/contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.
# Level V2 정보
Level은 리스테이크된 달러 토큰으로 뒷받침되는 스테이블 코인인 Level USD (lvlUSD)의 배후에 있는 프로토콜입니다. 사용자는 lvlUSD를 발행하고 스테이킹하여 리스테이킹 프로토콜의 AVS와 우량 온체인 대출 프로토콜에서 수익을 올릴 수 있습니다. lvlUSD는 USDC와 USDT로 완전히 뒷받침되는 스테이블 코인으로, 무허가로 발행할 수 있으며 대출 프로토콜을 통해 수익을 창출합니다. 사용자는 lvlUSD를 스테이킹하여 slvlUSD를 받을 수 있으며, 수익이 분배됨에 따라 가치가 상승하고 두 토큰 모두 통합된 DeFi 플랫폼에서 자유롭게 사용할 수 있습니다.

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

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약
_검토 커밋 해시_ - [08ffd7e9d81f7017498524e646bf3abba47426a4](https://github.com/Level-Money/contracts/tree/08ffd7e9d81f7017498524e646bf3abba47426a4)

_수정 검토 커밋 해시_ - [5065d156f72b878db301907509eadb49760275d2](https://github.com/Level-Money/contracts/tree/5065d156f72b878db301907509eadb49760275d2)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `DeployLevel` 
- `DeploymentUtils` 
- `LevelMintingV2` 
- `LevelMintingV2Storage` 
- `AuthUpgradeable` 
- `StrictRolesAuthority` 
- `PauserGuard` 
- `PauserGuarded` 
- `MathLib` 
- `OracleLib` 
- `StrategyLib` 
- `VaultLib` 
- `interfaces/` 
- `LevelReserveLens` 
- `AaveTokenOracle` 
- `ERC4626DelayedOracle` 
- `ERC4626Oracle` 
- `ERC4626OracleFactory` 
- `BoringVault` 
- `RewardsManager` 
- `RewardsManagerStorage` 
- `Silo` 
- `VaultManager` 
- `VaultManagerStorage` 

# 발견 사항
# [H-01] 0의 `heartBeat`는 보상 청구 실패를 일으킬 수 있음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`OracleLib` 및 `RewardsManager` 계약은 함께 작동하여 자산 가격을 기반으로 보상 청구를 가능하게 합니다. 이 프로세스의 핵심 부분은 볼트가 보유한 총 자산을 계산하는 `StrategyLib`의 `getAssets()` 함수입니다. 이 함수는 `OracleLib.getPriceAndDecimals()`를 통해 가져온 가격 데이터에 의존합니다.

그러나 현재 구현은 `getPriceAndDecimals()`를 호출할 때 `heartBeat` 값으로 0을 전달합니다.

```solidity
        (int256 assetsForOneShare, uint256 decimals) = OracleLib.getPriceAndDecimals(address(config.oracle), 0);
```

`heartBeat` 매개변수는 가격 데이터가 여전히 최신인지 확인하는 데 사용됩니다. 0을 전달한다는 것은 사실상 오래된 데이터에 대한 "허용 오차가 없음"을 의미합니다. 결과적으로 현재 `block.timestamp`가 가격의 `updatedAt` 타임스탬프보다 조금이라도 크면 함수가 되돌려져(revert) 보상 청구가 실패합니다.

### 개념 증명

- 사용자가 `RewardsManager` 계약에서 `reward()` 함수를 호출합니다.
- `reward()`는 `getAccruedYield()`를 트리거하고 내부적으로 `StrategyLib`에서 `getAssets()`를 호출합니다.
- `getAssets()`는 0의 `heartbeat`로 `OracleLib.getPriceAndDecimals()`를 호출합니다.
- `block.timestamp > updatedAt + 0`이면 호출이 되돌려져 사용자가 보상을 청구할 수 없습니다.
- 이것은 현재 설정이 사용자가 보상을 받는 것을 어떻게 차단할 수 있는지 명확하게 보여주며, 특히 가격 피드가 자주 업데이트되지 않을 때 그렇습니다.

## 권장 사항

`getAssets()`의 `getPriceAndDecimals()` 호출을 업데이트하여 0이 아닌 `heartbeat`를 사용하십시오. 최소 3600초(1시간) 값이 권장됩니다.

# [M-01] `reward`는 페그 미만(under-peg) 시나리오를 설명하지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`reward`가 호출되면 볼트 및 전략 전반의 총 자산 잔고에서 볼트에 발행된 주식을 뺀 값으로 누적 수익을 계산합니다.

```solidity
    function getAccruedYield(address[] calldata assets) public view returns (uint256 accrued) {
        uint256 total;

        for (uint256 i = 0; i < assets.length; i++) {
            address asset = assets[i];

            StrategyConfig[] memory strategies = allStrategies[asset];

            uint256 totalForAsset = vault._getTotalAssets(strategies, asset);
 >>>        total += totalForAsset.convertDecimalsDown(ERC20(asset).decimals(), vault.decimals());
        }
        uint256 vaultShares = vault.balanceOf(address(vault));
        accrued = total - vaultShares;

        return accrued;
    }
```

그러나 기본 자산이 현재 페그 미만(가격 < $1)인 시나리오는 고려하지 않습니다. 자산이 페그 미만인 경우 실제 가치는 계산된 자산 금액보다 낮습니다. 이로 인해 트레저리에 할당된 누적 수익이 잘못 계산되어 급격한 가격 하락 시 lvlUSD가 불충분하게 담보될 수 있습니다.

## 권장 사항

총 가치를 계산할 때 각 자산 가격을 고려하십시오.

# [M-02] `initiateRedeem` 및 `reward` 중에 불필요한 인출이 발생할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

사용자가 `initiateRedeem`을 트리거하면 작업은 항상 기본 전략에서 담보를 인출하려고 시도하며, 보링 볼트(boring vault)의 현재 담보 잔고가 상환 요청을 충당하기에 충분한지 먼저 확인하지 않습니다.

```solidity
    function initiateRedeem(address asset, uint256 lvlUsdAmount, uint256 expectedAmount)
        external
        requiresAuth
        notPaused
        returns (uint256, uint256)
    {
        if (!redeemableAssets[asset]) revert UnsupportedAsset();
        if (!isBaseCollateral[asset]) revert RedemptionAssetMustBeBaseCollateral();
        if (lvlUsdAmount == 0) revert InvalidAmount();

        uint256 collateralAmount = computeRedeem(asset, lvlUsdAmount);
        if (collateralAmount < expectedAmount) revert MinimumCollateralAmountNotMet();

        pendingRedemption[msg.sender][asset] += collateralAmount;
        userCooldown[msg.sender][asset] = block.timestamp;

        // note preventing amounts that would fail by definition at complete redeem due to max per block
        if (pendingRedemption[msg.sender][asset] > maxRedeemPerBlock) revert ExceedsMaxBlockLimit();

        lvlusd.burnFrom(msg.sender, lvlUsdAmount);

        // Don't block redemptions if withdraw default fails
>>>     try vaultManager.withdrawDefault(asset, collateralAmount) {
            emit WithdrawDefaultSucceeded(msg.sender, asset, collateralAmount);
        } catch {
            emit WithdrawDefaultFailed(msg.sender, asset, collateralAmount);
        }

        vaultManager.vault().exit(
            address(silo), ERC20(asset), collateralAmount, address(vaultManager.vault()), lvlUsdAmount
        );

        emit RedeemInitiated(msg.sender, asset, collateralAmount, lvlUsdAmount);

        return (lvlUsdAmount, collateralAmount);
    }
```

이로 인해 볼트에 상환 요청을 충당할 만큼 충분한 담보가 있더라도 전략에서 불필요한 인출이 발생할 수 있습니다. 이는 최적이 아닌 수익 및 자산 관리로 이어집니다.

`RewardsManager.reward`가 호출될 때도 동일한 경우가 적용됩니다.

## 권장 사항

볼트의 담보 잔고가 상환 및 `reward` 요청을 충당하기에 불충분할 때만 `vaultManager.withdrawDefault`를 트리거하십시오.

# [M-03] 잘못된 `maxRedeemPerBlock` 확인

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`maxRedeemPerBlock`은 블록당 최대 상환을 제한하기 위한 것입니다. 그러나 현재 `pendingRedemption`에 대해 확인하고 있습니다.

```solidity
    function initiateRedeem(address asset, uint256 lvlUsdAmount, uint256 expectedAmount)
        external
        requiresAuth
        notPaused
        returns (uint256, uint256)
    {
        if (!redeemableAssets[asset]) revert UnsupportedAsset();
        if (!isBaseCollateral[asset]) revert RedemptionAssetMustBeBaseCollateral();
        if (lvlUsdAmount == 0) revert InvalidAmount();

        uint256 collateralAmount = computeRedeem(asset, lvlUsdAmount);
        if (collateralAmount < expectedAmount) revert MinimumCollateralAmountNotMet();

        pendingRedemption[msg.sender][asset] += collateralAmount;
        userCooldown[msg.sender][asset] = block.timestamp;

        // note preventing amounts that would fail by definition at complete redeem due to max per block
>>>     if (pendingRedemption[msg.sender][asset] > maxRedeemPerBlock) revert ExceedsMaxBlockLimit();

        lvlusd.burnFrom(msg.sender, lvlUsdAmount);

        // Don't block redemptions if withdraw default fails
        try vaultManager.withdrawDefault(asset, collateralAmount) {
            emit WithdrawDefaultSucceeded(msg.sender, asset, collateralAmount);
        } catch {
            emit WithdrawDefaultFailed(msg.sender, asset, collateralAmount);
        }

        vaultManager.vault().exit(
            address(silo), ERC20(asset), collateralAmount, address(vaultManager.vault()), lvlUsdAmount
        );

        emit RedeemInitiated(msg.sender, asset, collateralAmount, lvlUsdAmount);

        return (lvlUsdAmount, collateralAmount);
    }
```

이로 인해 확인이 의도한 대로 작동하지 않습니다. 사용자는 lvlUSD를 다른 계정으로 전송하고 상환하여 이를 쉽게 우회할 수 있습니다.

## 권장 사항

`redeemedPerBlock`이 증가하는 `completeRedeem`으로 `maxRedeemPerBlock` 확인을 이동하거나, `redeemedPerBlock` 증분을 `initiateRedeem`으로 이동하고 거기서 `maxRedeemPerBlock`에 대해 확인을 수행하십시오.

```diff
    function initiateRedeem(address asset, uint256 lvlUsdAmount, uint256 expectedAmount)
        external
        requiresAuth
        notPaused
        returns (uint256, uint256)
    {
        if (!redeemableAssets[asset]) revert UnsupportedAsset();
        if (!isBaseCollateral[asset]) revert RedemptionAssetMustBeBaseCollateral();
        if (lvlUsdAmount == 0) revert InvalidAmount();

        uint256 collateralAmount = computeRedeem(asset, lvlUsdAmount);
        if (collateralAmount < expectedAmount) revert MinimumCollateralAmountNotMet();

        pendingRedemption[msg.sender][asset] += collateralAmount;
        userCooldown[msg.sender][asset] = block.timestamp;

+      redeemedPerBlock[block.number] += collateralAmount;

        // note preventing amounts that would fail by definition at complete redeem due to max per block
-        if (pendingRedemption[msg.sender][asset] > maxRedeemPerBlock) revert ExceedsMaxBlockLimit();
+        if (redeemedPerBlock[block.number] > maxRedeemPerBlock) revert ExceedsMaxBlockLimit();

        // ...
    }
```

# [M-04] 최대 `redeemPerBlock` 제한이 올바르게 구현되지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`LevelMintingV2` 계약은 `maxRedeemPerBlock` 매개변수를 사용하여 단일 블록에서 상환할 수 있는 총 자산 양을 제어하도록 설계되었습니다. 이 메커니즘은 시스템 안정성을 유지하고 유동성 부족을 방지하는 것을 목표로 합니다. 그러나 현재 구현은 이 제한을 올바르게 시행하지 못합니다.

문제는 `initiateRedeem()` 및 `completeRedeem()` 함수가 상환을 처리하는 방식에 있습니다. `initiateRedeem()`은 사용자의 요청된 상환이 `maxRedeemPerBlock`을 초과하는지 확인하지만, 이 확인은 사용자별로 적용됩니다. 결과적으로 각 사용자는 다른 사용자가 무엇을 상환하는지에 관계없이 쿨다운 기간 동안 허용된 최대 금액까지 상환할 수 있습니다. 이는 사실상 여러 사용자가 동일한 블록에서 전체 제한을 동시에 상환할 수 있음을 의미합니다.

다음 예를 고려해 보십시오.

1. `maxRedeemPerBlock`이 250,000으로 설정됩니다.
2. 사용자 A가 250,000의 금액으로 `initiateRedeem()`을 호출합니다.
3. 사용자 B도 동일하게 수행하여 250,000을 요청합니다.
4. 두 트랜잭션 모두 동일한 블록에서 채굴됩니다.
5. 블록의 총 상환액 = 500,000, 이는 의도한 제한의 두 배입니다.

## 권장 사항

발행(minting) 중에 사용되는 것과 동일한 메커니즘을 사용하십시오.

# [M-05] `RewardsManager`에서 누락된 오라클 업데이트

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`RewardsManager.getAccruedYield()` 함수는 오라클 가격을 기반으로 보상을 계산하지만 가격을 가져오기 전에 `_tryUpdateOracle()`을 호출하지 못합니다. 이 동작은 가격을 사용하기 전에 오라클을 명시적으로 업데이트하는 `LevelMintingV2`와 다릅니다.
```solidity
    function getAccruedYield(address[] calldata assets) public view returns (uint256 accrued) {
        uint256 total;

        for (uint256 i = 0; i < assets.length; i++) {
            address asset = assets[i];
            StrategyConfig[] memory strategies = allStrategies[asset];
@>            uint256 totalForAsset = vault._getTotalAssets(strategies, asset);

            // ...
        }

        // ...
    }
```
위와 같이 `getAccruedYield`는 `USDC` 및 `USDT`와 같은 자산에 대해 `_getTotalAssets`를 호출하여 `BoringVault`가 제어하는 총 자산을 계산합니다. 이 계산은 대출 전략의 모든 볼트 주식을 기본 자산으로 가상으로 변환합니다.
```solidity
    function _getTotalAssets(BoringVault vault, StrategyConfig[] memory strategies, address asset)
        internal
        view
        returns (uint256 total)
    {
        // Initialize to undeployed
        uint256 totalForAsset = ERC20(asset).balanceOf(address(vault));

        for (uint256 j = 0; j < strategies.length; j++) {
            StrategyConfig memory config = strategies[j];
@>            totalForAsset += StrategyLib.getAssets(config, address(vault));
        }

        return totalForAsset;
    }
```
이 변환을 수행하기 위해 `StrategyLib.getAssets`가 사용됩니다. 각 전략에 보유된 주식 수를 확인하고 오라클 가격을 사용하여 기본 자산으로 변환합니다. 그러나 아래와 같이 오라클 업데이트 호출(steakUSDC 또는 `ERC4626DelayedOracle`을 사용하는 기타 주식 토큰에 필요함)이 누락되었습니다.
```solidity
    function getAssets(StrategyConfig memory config, address vault) internal view returns (uint256 assets_) {
        // ...

        uint256 shares = receiptToken.balanceOf(vault);

        uint256 sharesToAssetDecimals =
            shares.mulDivDown(10 ** ERC20(address(config.baseCollateral)).decimals(), 10 ** receiptToken.decimals());

@>        (int256 assetsForOneShare, uint256 decimals) = OracleLib.getPriceAndDecimals(address(config.oracle), 0);
        assets_ = uint256(assetsForOneShare).mulDivDown(sharesToAssetDecimals, 10 ** decimals);
        return assets_;
    }
```
이 취약점의 영향은 각 보상 계산 중에 오래된 가격이 사용될 수 있으며, 업데이트된 가격을 할당하기 위해 `ERC4626DelayedOracle::update`가 언제 다음에 호출될지 불분명하다는 것입니다.

## 권장 사항

이 문제를 해결하려면 `LevelMintingV2.sol`에서 사용된 접근 방식과 유사하게 가격 검색 전에 오라클이 업데이트되었는지 확인하십시오.

# [L-01] `initialize()`에 자산 및 오라클 길이 확인이 없음

initialize()에서 for-loop를 통해 자산에 대한 오라클이 추가됩니다. 그러나 asset.length가 oracles.length와 같아야 하는지 확인/요구하지 않습니다.
```solidity
    function initialize(
        address[] memory _assets,
        address[] memory _oracles,
....
    ) external initializer {
....
        for (uint256 i = 0; i < _assets.length; i++) {
            addOracle(_assets[i], _oracles[i], false);
        }
....

    }
```

자산과 오라클의 길이가 동일한지 확인하십시오. 그렇지 않으면 out-of-bond 오류가 발생합니다.

# [L-02] 잘못된 보링 볼트 설정

`VaultManager`는 업그레이드 가능한 프록시 뒤에 생성되도록 의도된 계약입니다. 그러나 `VaultManagerStorage`에서는 보링 볼트가 `VaultManager`의 초기화 함수(initializer) 대신 생성자(constructor)에서 설정됩니다. 이 로직을 `VaultManagerStorage` 생성자에서 `VaultManager`의 `initialize` 함수로 이동하는 것을 고려하십시오.

# [L-03] 가격 조작 위험에 처한 Morpho 저유동성 볼트

Morpho 볼트 또는 모든 ERC-4626 볼트는 본질적으로 특히 신규 또는 저유동성 볼트의 경우 가격 조작에 취약합니다. 예를 들어 보호 메커니즘이 없는 빈 볼트의 경우:

- 공격자가 최소 금액(예: 토큰 1 wei)을 입금하고 1주를 받습니다.
- 공격자는 대량의 금액을 볼트로 직접 전송합니다(예: 1억 토큰).
- 이로 인해 1주 ≈ 1억 토큰인 환율이 생성됩니다.
- 1 토큰을 입금하는 사용자는 부풀려진 환율로 인해 0주를 받습니다.

총 가치를 계산하거나 입금/출금 작업 중에 주당 가격을 확인하지 않으면 문제가 발생할 수 있습니다. 일반적으로 예상 반환 자산 또는 주식에 대한 슬리피지 확인은 ERC-4626 가격이 조작될 때 손실이 발생하지 않도록 하는 데 사용됩니다.

## Level 팀 의견:

인지했습니다. 볼트에 입금할 때 다음 정책을 채택할 계획입니다.

- 최소 $100k의 초기 기본 입금
- 볼트 크기의 25%를 초과할 수 없음

# [L-04] Morpho의 테스트 볼트가 배포 스크립트에서 아직 제거되지 않음

`DeployLevel.s.sol` 내부에는 테스트 목적으로 전략 목록을 설정하고 등록하는 `_setupMorphoVaultsForTests` 함수가 있지만 현재 스크립트 내부에서 여전히 호출되고 있습니다. `run` 함수 내부의 `_setupMorphoVaultsForTests` 호출을 제거하는 것을 고려하십시오.

# [L-05] `aUSDC`, `aUSDT` 및 steakhouse USDC 발행 불가

문서에 따르면 사용자는 aUSDC, aUSDT 및 Steakhouse USDC로 발행할 수 있다고 명시되어 있습니다. 그러나 현재 `DeployLevel.s.sol`에서 이러한 자산은 발행 가능한 자산으로 구성되지 않았습니다.

# [L-06] `LevelReserveLens`가 잘못된 준비금을 반환함

`LevelReserveLens` 계약의 `_getReserves()` 함수는 현재 v1 볼트의 준비금만 포함하고 v2 볼트에 보유된 담보를 완전히 무시합니다. 결과적으로 lvlUSD를 뒷받침하는 총 준비금을 과소평가하여 v1Reserves + 0을 반환하며, v2 담보는 사실상 0으로 하드코딩됩니다.

```solidity
    function _getReserves(IERC20Metadata collateral, address waCollateralAddress, address symbioticVault)
        internal
        view
        override
        returns (uint256)
    {
        uint256 v1Reserves = super._getReserves(collateral, waCollateralAddress, symbioticVault);

        uint256 boringVaultValue = 0;

        return v1Reserves + boringVaultValue;
    }
```

이러한 잘못된 표현은 이 함수에 의존하는 모든 소비자에게 부정확한 준비금 데이터로 이어져 잠재적으로 재정적 결정에 영향을 미칠 수 있습니다.

주어진 담보 토큰에 대해 보유된 총 자산을 반환하는 공개 함수를 `VaultManager` 계약에 추가하십시오. 그런 다음 LevelReserveLens의 `_getReserves()`를 업데이트하여 v2에 대해 하드코딩된 0 대신 이 새 함수를 사용하십시오.

# [L-07] `BoringVault`는 ETH를 받을 수 있지만 인출할 수는 없음

`BoringVault` 계약에는 `payable`인 `receive` 함수가 있습니다.

```solidity
    receive() external payable {}
```

누군가 `msg.value != 0`으로 트랜잭션을 보내면 ETH는 누군가 인출할 방법 없이 계약에 영원히 갇히게 됩니다.

어차피 계약에서 ETH 잔고를 사용하지 않으므로 `receive` 함수를 제거하십시오.

# [L-08] 전략 제거에 대한 역할 할당 누락

`VaultManager`의 `removeAssetStrategy` 함수는 인증이 필요하지만 호출할 수 있는 권한이 부여된 역할이 없습니다. 이로 인해 비상 시 문제가 있는 전략을 제거하지 못할 수 있습니다. 역할 기능을 추가하는 것을 고려하십시오.
```solidity
_setRoleCapabilityIfNotExists(
    STRATEGIST_ROLE,
    address(config.levelContracts.vaultManager),
    bytes4(abi.encodeWithSignature("removeAssetStrategy(address,address)"))
);
```

# [L-09] `beforeTransferHook`에 대한 역할 권한 부여 누락

BoringVault의 `setBeforeTransferHook` 함수는 인증이 필요하지만 호출할 수 있는 권한이 부여된 역할이 없습니다. 즉, 아무도 설정할 수 없으므로 훅 기능이 사실상 비활성화됩니다. 필요한 역할 기능을 추가하는 것을 고려하십시오.
```solidity
        _setRoleCapabilityIfNotExists(
            VAULT_MANAGER_ROLE,
            address(config.levelContracts.boringVault),
            bytes4(abi.encodeWithSignature("setBeforeTransferHook(address)"))
        );
```

# [L-10] 불필요하고 위험한 토큰 승인

`VaultLib` 라이브러리에는 `_withdrawFromAave` 및 `_withdrawFromMorpho` 함수 모두에 불필요한 토큰 승인이 포함되어 있습니다.
```solidity
    function _withdrawFromAave(BoringVault vault, StrategyConfig memory _config, uint256 amount)
        internal
        returns (uint256 withdrawn)
    {
        address aaveV3 = _getAaveV3Pool();

@>        vault.increaseAllowance(address(_config.receiptToken), aaveV3, amount);

        // ...
     }

    function _withdrawFromMorpho(BoringVault vault, StrategyConfig memory _config, uint256 amount)
        internal
        returns (uint256 withdrawn)
    {
        IERC4626 morphoVault = IERC4626(_config.withdrawContract);

        uint256 sharesToRedeem = morphoVault.previewWithdraw(amount);

        if (sharesToRedeem == 0) {
            revert("VaultManager: amount must be greater than 0");
        }

@>        vault.increaseAllowance(address(_config.receiptToken), _config.withdrawContract, sharesToRedeem);

        // ...
    }
```
다음과 같은 이유로 이러한 승인은 불필요합니다.
- Aave V3의 `withdraw` 함수는 `aTokens`의 승인을 요구하지 않습니다.
- Morpho의 `withdraw` 함수는 승인을 요구하지 않고 호출자의 잔고에서 직접 주식을 태웁니다(burn).

두 인출 함수 모두에서 이러한 불필요한 토큰 승인을 제거하는 것을 고려하십시오.

# [L-11] 스테이블 코인 간 `maxRedeemPerBlock`의 일관성 없는 가치 추적

`maxRedeemPerBlock` 제한은 상환되는 실제 `lvlUSD` 가치가 아닌 담보 금액(USDC/USDT)을 추적합니다. 스테이블 코인은 페그에서 벗어날 수 있으므로(예: USDC는 $0.99, USDT는 $1.01), 제한되는 실제 `lvlUSD` 가치에 불일치가 발생합니다. USDT가 페그 위에 있을 때 상환하는 사용자는 USDC가 페그 아래에 있을 때 사용하는 사람에 비해 1-2% 더 많은 `lvlUSD` 가치를 상환할 수 있지만 둘 다 `maxRedeemPerBlock` 제한에 대해 동일하게 계산됩니다.

```solidity
        uint256 collateralAmount = computeRedeem(asset, lvlUsdAmount);
        if (collateralAmount < expectedAmount) revert MinimumCollateralAmountNotMet();

@>        pendingRedemption[msg.sender][asset] += collateralAmount;
```

다른 담보 유형 간에 일관된 가치 제한을 보장하기 위해 원시 담보 금액 대신 상환되는 `lvlUSD` 가치를 추적하고 제한하는 것을 고려하십시오.

# [L-12] 자산이 제거되면 담보가 사일로(silo)에 갇힐 수 있음

사용자가 completeRedeem()을 수행할 때 자산이 `redeemableAssets`이어야 합니다. 그러나 관리자는 `removeRedeemableAsset()`을 통해 redeemableAssets 매핑에서 모든 자산을 제거할 수 있습니다. 이로 인해 completeRedeem()이 되돌려지고 담보가 사일로 계약에 `갇히게(stuck)` 됩니다.
```solidity
  function completeRedeem(address asset, address beneficiary) external notPaused returns (uint256 collateralAmount) {
        if (!redeemableAssets[asset]) revert UnsupportedAsset();
....

    }
```

1. 사용자가 USDC/T에 대해 100e18 lvlUSD의 상환을 시작했다고 가정해 보겠습니다. USDC/T는 baseCollateral이자 redeemableAssets입니다. 이것은 담보를 boringVault에서 사일로 계약으로 전송합니다.
2. 사용자가 상환을 완료하기 전에 관리자가 redeemableAssets에서 자산을 제거했습니다.
3. 사용자가 completeRedeem()을 호출하면 해당 자산이 더 이상 유효한 redeemableAssets가 아니기 때문에 되돌려집니다.
4. 결과적으로 사용자의 담보는 사일로 계약에 영원히 갇히게 됩니다.

권장 사항:

담보가 이미 boringVault에서 제거되었으므로 completeRedeem()에서 redeemableAssets 요구 사항을 제거하십시오.

# [L-13] Aave 인센티브가 잠길 것임

Aave는 프로토콜에 자산을 공급하는 사용자에게 **인센티브**(예: 스테이킹 보상 또는 유동성 채굴 보상, 여기 참조: <https://aave.com/docs/primitives/incentives>)를 제공합니다. 이러한 인센티브는 일반적으로 추가 토큰(예: AAVE 또는 기타 거버넌스 토큰) 형태로 분배되며 Aave의 인센티브 메커니즘과 상호 작용하는 사용자가 청구할 수 있습니다.

그러나 `LevelMintingV2, RewardManager, VaultManager` 계약의 현재 구현에는 이러한 인센티브를 청구할 수 있는 기능이 없습니다. 이것은 levelV2가 Aave에 자산을 공급하는 이점을 최대한 누리지 못하게 할 수 있는 **누락된 기능**입니다.

`vaultManager` 계약은 Aave에 담보 토큰을 공급합니다.
```solidity
    function _depositToAave(BoringVault vault, StrategyConfig memory _config, uint256 amount)
        internal
        returns (uint256 deposited)
    {
        address aaveV3 = _getAaveV3Pool();
        vault.increaseAllowance(address(_config.baseCollateral), aaveV3, amount);

        uint256 balanceBefore = ERC20(_config.baseCollateral).balanceOf(address(vault));
        vault.manage(
            address(aaveV3),
            abi.encodeWithSignature(
                "supply(address,uint256,address,uint16)", address(_config.baseCollateral), amount, address(vault), 0
            ),
            0
        );
        uint256 balanceAfter = ERC20(_config.baseCollateral).balanceOf(address(vault));

        uint256 deposited_ = balanceBefore - balanceAfter;
        emit DepositToAave(address(vault), address(_config.baseCollateral), amount, deposited_);

        return deposited_;
    }
```

그러나 rewardManger/운영자가 공급자에게 Aave가 분배하는 인센티브를 청구할 수 있는 방법을 제공하지 않습니다. Eth 메인넷에서 aave 보상 계약은 다음과 같습니다: <https://etherscan.io/address/0x8164Cc65827dcFe994AB23944CBC90e0aa80bFcb>

현재 이 계약은 보상 청구에 여전히 사용할 수 있습니다.

결과적으로 levelV2는 Aave에서 제공하는 인센티브를 청구할 수 없어 보상이 손실됩니다.

권장 사항:

운영자/rewardManager가 Aave에서 인센티브를 청구할 수 있는 기능을 추가하십시오. 여기에는 Aave의 **Incentives Controller** 또는 **Rewards Distributor** 계약과의 상호 작용이 포함됩니다.

## Level 팀 의견

인지했습니다. BoringVault의 소유자가 manage를 호출할 수 있으므로 보상이 영원히 손실되지 않습니다. Aave는 USDC/T에 대해 보상을 거의 발행하지 않는 것 같고 Morpho의 보상 청구 메커니즘은 계약 배포자가 구성할 수 있으므로 보상을 청구하는 기능을 다음 RewardsManager 업데이트로 미루는 것으로 괜찮습니다.

# [L-14] `RewardsManager`는 여러 자산에 걸친 수익 분할을 처리할 수 없음

`RewardsManager::reward` 함수는 총 누적 수익이 여러 자산(`USDC` 및 `USDT`) 간에 분할되어 단일 자산 전략의 잔고를 초과하는 경우를 처리할 수 없습니다. 이로 인해 프로토콜에 수익이 존재하더라도 보상을 청구할 수 없는 상황이 발생합니다.

```solidity
    function reward(address[] calldata assets) external requiresAuth {
        uint256 accrued = getAccruedYield(assets);
        address redemptionAsset = assets[0];

        if (accrued == 0) {
            revert NotEnoughYield();
        }

        uint256 accruedAssets = accrued.convertDecimalsDown(vault.decimals(), ERC20(redemptionAsset).decimals());

        vault._withdrawBatch(allStrategies[redemptionAsset], accruedAssets);

@>        vault.exit(treasury, ERC20(redemptionAsset), accruedAssets, address(vault), 0);

        emit Rewarded(redemptionAsset, treasury, accruedAssets);
    }
```

예를 들어:
1. BoringVault에 10주가 있습니다.
2. USDC 전략에 11 USDC가 있습니다.
3. USDT 전략에 11 USDT가 있습니다.
4. 총 누적 수익은 (11+11) - 10 = 12입니다.
5. 보상을 청구하려고 할 때 다음 이유로 함수가 되돌려집니다.
- USDC 전략에서 12를 인출하려고 시도합니다(11만 있음).
- 또는 USDT 전략에서 12를 인출하려고 시도합니다(11만 있음).
- 어느 전략도 전체 수익 금액을 충당할 만큼 충분하지 않습니다.

즉, 프로토콜에 사용할 수 있는 수익이 있더라도 보상 함수가 여러 자산에 걸쳐 인출을 분할하는 것을 지원하지 않기 때문에 청구할 수 없습니다. 결과적으로 `reward()`에 대한 모든 호출은 항상 되돌려집니다.

권장 사항:

`reward()`는 여러 자산에서 비례적으로 수익을 인출하는 것을 지원하도록 수정되어야 합니다. 이는 각 자산의 수익 지분을 계산하고 각 자산의 전략에서 비례 인출을 수행하여 구현할 수 있습니다. 또는 인출할 자산과 금액을 지정할 수도 있습니다.

## Level 팀 의견

인지했습니다. 적어도 일주일에 한 번 `reward`를 호출할 것으로 예상하므로 공유할 수 있을 것으로 예상되는 수익은 대략 `(연간 수익 APR / 52) * 적립금의 총 USD 가치`입니다. 몇 가지 경우를 예로 들어 보겠습니다.

평균 수익률 4% 가정: `4% / 52` = `0.08%`
높은 수익률 20% 가정: `20% / 52` = `0.40%`
매우 높은 수익률 100% 가정: `100% / 52` = `1.92%`

따라서 적립금의 최소 2%가 가장 유동적인 기본 담보에 저장되어 있다고 가정하면 매우 높은 수익률의 경우를 충당할 수 있어야 합니다. 그리고 기본 담보가 2개뿐이므로 가장 유동적인 기본 담보가 수익률이 매우 높거나 `reward`를 덜 자주 호출하는 경우를 충당할 수 있다고 가정하는 것이 편안합니다.

# [L-15] `LVLUSD` 소각으로 영구적인 담보 회계 불일치 발생

`LVLUSD` 토큰이 직접 소각되면(상환 프로세스를 통하지 않음) `BoringVault`의 해당 볼트 주식은 소각되지 않습니다. 이로 인해 `LVLUSD` 토큰의 총 공급량과 이를 뒷받침하는 담보를 나타내는 볼트 주식 간에 불일치가 발생합니다.

이 문제는 `total - vault.balanceOf(address(vault))`를 기반으로 하는 `RewardsManager`의 수익 계산에 영향을 미칩니다. 볼트 주식이 소각되지 않으므로 이 계산이 정확하지 않게 되어 가정하는 만큼 `LVLUSD`가 많지 않기 때문에 잘못된 보상 분배로 이어집니다.

```solidity
contract lvlUSD is ERC20Burnable, ERC20Permit, IlvlUSDDefinitions, SingleAdminAccessControl {
         // code
}
```

예를 들어:
1. 사용자가 100 `USDC`를 입금하고 100 `LVLUSD`를 받습니다.
2. `BoringVault`는 100 볼트 주식을 발행합니다.
3. 사용자가 50 `LVLUSD`를 직접 소각합니다.
4. 결과:
- `LVLUSD` 공급: 50
- 볼트 주식: 100
- 볼트 내 `USDC`: 100

```solidity
    function getAccruedYield(address[] calldata assets) public view returns (uint256 accrued) {
        uint256 total;

        for (uint256 i = 0; i < assets.length; i++) {
            // code
        }

        uint256 vaultShares = vault.balanceOf(address(vault));
        accrued = total - vaultShares;

        return accrued;
    }
```

`LVLUSD` 토큰을 소각할 직접적인 금전적 인센티브는 없지만 `LVLUSD`가 소각될 수 있는 합법적인 사용 사례(예: 프로토콜 업그레이드, 토큰 마이그레이션)가 있습니다. 볼트 주식과 `LVLUSD` 공급 간의 불일치는 프로토콜의 회계 및 보상 분배 메커니즘에 영향을 미칩니다.

권장 사항:

최적의 솔루션은 `LVLUSD` 토큰이 소각될 때마다 `BoringVault`를 호출하여 해당 볼트 주식을 소각하는 훅을 `LVLUSD` 계약에 구현해야 합니다.

## Level 팀 의견

인지했습니다. lvlUSD는 불변 계약이므로 로직에 영향을 미칠 수 없습니다. 또한 관리자 타임락(timelock)이 `vault.exit`를 호출하여 소각된 `lvlUSD`를 뒷받침하는 데 사용된 담보를 복구할 수 있으므로 복구 경로가 있습니다.

# [L-16] 볼트 입금 프론트런

볼트는 여러 전략(예: Aave 및 Morpho)을 지원합니다. 그러나 사용자가 LVLUSD를 발행하면 자금은 초기에 기본 전략(예: Aave)에만 입금됩니다. 전략 재조정(예: 자금을 Aave에서 Morpho로 이동)은 다음 단계를 사용하여 관리자가 수동으로 수행해야 합니다.

1. **인출**: Aave 전략에서 토큰을 인출합니다.
   관리자는 먼저 `withdraw()` 함수를 호출하여 Aave에서 aTokens를 가져와 USDC로 다시 변환하고 토큰을 볼트로 이동합니다.

2. **입금**: USDC를 Morpho 전략에 입금합니다.
   USDC 토큰이 볼트에 돌아오면 관리자는 `deposit()` 함수를 사용하여 해당 토큰을 Morpho 전략으로 이동할 수 있습니다.

Aave 전략에서 토큰을 인출하는 함수는 입금 함수와 동일하지 않습니다. 관리자는 먼저 토큰을 **인출**하며, 이는 재조정하거나 다른 전략에 입금할 수 있게 됩니다.

예를 들어:

```solidity
// Admin withdraws aTokens from Aave and converts them to USDC in the vault
function withdraw(address asset, uint256 amount) external requiresAuth notPaused {
    // Withdraw logic...
}

// Admin then deposits the USDC into Morpho strategy
function deposit(address asset, address strategy, uint256 amount) external requiresAuth notPaused returns (uint256 deposited) {
    return _deposit(asset, strategy, amount);
}
```

이러한 방식으로 관리자는 먼저 Aave에서 인출한 다음 Morpho에 입금하여 Aave와 Morpho 전략 간에 자금을 재할당할 수 있습니다.

### 문제: 프론트런으로 인한 DoS

관리자의 입금 트랜잭션이 채굴되기 직전에 사용자가 LVLUSD를 상환하면 볼트는 담보(예: USDC)를 `Silo` 계약으로 전송하여 **볼트의 토큰 잔고를 줄입니다**.

#### 예:

- 볼트에 **정확히 20,000 USDC**가 있습니다.
- 관리자가 Aave에서 Morpho로 **20,000 USDC**를 이동하려고 시도합니다.
- 사용자가 **1 LVLUSD**를 상환하여 볼트가 `Silo`로 **1 USDC**를 보내게 합니다 → 볼트에는 이제 **19,999 USDC**가 있습니다.
- 자금 부족으로 인해 입금 트랜잭션이 실패합니다.
- 1 USDC가 여전히 프로토콜(Silo)에 있지만 현재 볼트에서는 액세스할 수 없습니다.

이를 통해 모든 사용자가 프론트런하여 **프로토콜을 방해(grief)**하고 합법적인 전략 재할당을 방지할 수 있습니다.

### 확장된 DoS: 대량 보유자 악용

관리자가 **20,000 중 10,000**과 같이 **부분 금액**만 재할당하려고 시도하더라도 **남은 잔고보다 많은 금액(예: 10,001 LVLUSD)**을 보유한 악의적인 사용자는 **동일한 DoS를 강제**할 수 있습니다.

- 볼트에 20,000 USDC가 있습니다.
- 관리자가 10,000의 입금을 시작합니다.
- 악의적인 사용자가 10,001 LVLUSD를 상환합니다 → 볼트가 10,001 USDC를 Silo로 보냅니다.
- 볼트에는 이제 9,999 USDC만 있습니다.
- 관리자의 10,000 USDC 입금은 잠시 전에는 충분한 유동성이 있었음에도 불구하고 **실패**합니다.

이로 인해 서비스 거부(DoS) 시나리오가 생성되어 프론트런 공격이나 시스템을 악용하는 악의적인 사용자로 인해 볼트가 자금을 적절하게 재할당할 수 없습니다.

권장 사항:

자금을 이동하는 기능을 사용하여 이 작업을 원자적으로 수행하십시오.
