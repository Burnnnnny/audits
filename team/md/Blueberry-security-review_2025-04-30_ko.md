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

_검토 커밋 해시_ - [bcb1a056864226e433705f8e25926a126d21d38b](https://github.com/Blueberryfi/blueberry-v2-contracts/tree/bcb1a056864226e433705f8e25926a126d21d38b)

_수정 검토 커밋 해시_ - [d299166b968039bec4978fdd8a4c782d113dae9b](https://github.com/Blueberryfi/blueberry-v2-contracts/tree/d299166b968039bec4978fdd8a4c782d113dae9b)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `DeployRouterTestnet` 
- `UpgradeEscrows`
- `UpgradeRouter`
- `EscrowAssetStorage`
- `HyperVaultRouter`
- `HyperliquidEscrow`
- `L1EscrowActions`
- `WrappedVaultShare`

# 발견 사항

# [C-01] 상환 중 USD에서 자산으로의 잘못된 변환

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

deposit() 함수에서 자산 금액에서 USD 가치로의 변환은 현물 환율을 곱하여 수행됩니다.
`        uint256 usdValue = escrow.getRate(details.spotMarket).mulWadDown(amount * scaler);
`
redeem() 함수에서 USD에서 다시 자산으로의 변환이 잘못되게 곱셈을 통해 수행됩니다.
`amount = escrow.getRate(details.spotMarket).mulWadDown(usdAmount);
`

이로 인해 사용자는 원래 예치한 것보다 더 많은 토큰을 받게 되어 가치 인플레이션이 발생합니다. 예를 들어 환율이 2 * 1e18(즉, 1 자산 = 2 USD)이고 사용자가 1e18 상당의 자산(USD 가치 = 2e18)을 예치하는 경우 상환 로직은 다음을 반환합니다.
`amount = 2e18 * 2e18 / 1e18 = 4e18`

이로 인해 예상되는 1배 대신 자산 금액이 4배 증가합니다. 이 오류는 사용자가 예치한 것보다 더 많은 토큰을 발행하도록 악용할 수 있으므로 프로토콜에 심각한 재정적 손실을 초래할 수 있습니다.

## 권장 사항

redeem() 함수의 곱셈을 나눗셈으로 대체하여 USD에서 다시 자산으로 올바르게 변환하십시오.

`amount = usdAmount.divWadDown(escrow.getRate(details.spotMarket));
`

# [C-02] 자산 인덱스 확인 누락으로 모든 토큰이 주식 발행 가능

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

HyperVaultRouter 계약의 deposit 함수는 `$.assetIndexes[asset]`에서 검색된 자산 인덱스를 사용하여 `_isAssetSupported` 메서드를 통해 예치된 토큰이 지원되는지 확인합니다. 그러나 이 확인은 다음 로직으로 인해 결함이 있습니다.

```solidity
        uint64 assetIndex_ = $.assetIndexes[asset];
        require(_isAssetSupported($, assetIndex_), Errors.COLLATERAL_NOT_SUPPORTED());

````
토큰이 등록되지 않은 경우(즉, addAsset을 통해 추가되지 않음) `$.assetIndexes[asset]` 매핑은 기본값 0을 반환합니다. USDC는 인덱스 0(USDC_SPOT_INDEX = 0)에 하드코딩되어 있고 USDC는 유효하고 지원되는 자산이므로 주소가 `assetIndexes` 매핑에 없는 지원되지 않는 ERC20 토큰에 대해 이 확인이 거짓으로 통과됩니다.

결과적으로:
- 모든 사용자가 무작위의 잠재적으로 가치 없는 ERC20 토큰으로 `deposit()`을 호출할 수 있습니다.
- 프로토콜은 토큰을 USDC로 잘못 처리합니다.
- USDC 가치를 기준으로 주식이 발행됩니다.

POC:
https://gist.github.com/tevrat-aksoy/da734f144cd0176c41a3161ca74ab791

```solidity
    function testAnyTokenCanDeposited() public {
        //  First add the asset to the router
        router.addAsset(address(asset), 0, 0);
        router.setWithdrawAsset(address(asset));

        //  Then mint tokens to Alice
        asset.mint(alice, 100e8);

        vm.startPrank(alice);
        asset.approve(address(router), 100e8);
        router.deposit(usdcADDRESS, 100e8);

        assertEq(shareToken.balanceOf(address(alice)), 100e18, "alice should have 100e8 shares");

        assertEq(router.tvl(), 100e18, "alice should have 100e8 shares");

        // Create random token and deposit
        MockERC20 randomERC = new MockERC20("Random", "RND", 8);
        randomERC.mint(bob, 100e8);
        vm.startPrank(bob);

        randomERC.approve(address(router), 100e8);
        router.deposit(address(randomERC), 100e8);

        assertEq(shareToken.balanceOf(address(bob)), 100e18, "bob should have 100e8 shares");
        // TVL didnt change but share token supply increased
        assertEq(router.tvl(), 100e18, "alice should have 100e8 shares");
    }
```

## 권장 사항

자산이 USDC인 것으로 알려지지 않는 한 `assetIndex_ == 0`을 허용하지 마십시오.
인덱스가 0일 때 자산이 USDC인지 확인하는 특별 사례 확인을 추가하십시오.

# [H-01] `getRate()`가 `szDecimals`를 무시함

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

getRate() 함수는 사전 컴파일에서 현물 가격을 검색하고 고정 `USDC_SPOT_DECIMALS = 8`을 가정하여 스케일링합니다.

```solidity
uint8 public constant USDC_SPOT_DECIMALS = 8;
uint256 public constant USDC_SPOT_SCALING = 10 ** (18 - USDC_SPOT_DECIMALS);

function getRate(uint32 spotMarket) public view override returns (uint256) {
    (bool success, bytes memory result) = SPOT_PX_PRECOMPILE_ADDRESS.staticcall(abi.encode(spotMarket));
    require(success, Errors.PRECOMPILE_CALL_FAILED());
    uint256 scaledRate = uint256(abi.decode(result, (uint64))) * USDC_SPOT_SCALING;
    return scaledRate;
}
```

그러나 Hyperliquid 프로토콜에서 사전 컴파일이 반환하는 원시 현물 가격이 항상 8 소수점을 준수하는 것은 아닙니다. 대신 의미 있는 가격 소수점 수는 관련 자산의 `szDecimals` 값에 의해 동적으로 제한됩니다.

`szDecimals = 0`인 현물 토큰의 경우 가격은 최대 8 소수점까지 올라갈 수 있습니다(예: 0.108 → 10845000).

`szDecimals = 2`의 경우 가격은 최대 6 소수점(즉, 8 - szDecimals)을 가질 수 있습니다.

예시:
토큰: HFUN (szDecimals = 2)
가격: $37.07
사전 컴파일 반환: 37073000
8 소수점을 가정하면 0.37073000으로 해석됨 — 이는 올바르지 않음
실제로는 6 소수점만 있는 37.073000으로 처리되어야 함

`cast call 0x0000000000000000000000000000000000000808 0x0000000000000000000000000000000000000000000000000000000000000001 --rpc-url https://rpc.hyperliquid.xyz/evm`
--> 결과: 37073000

## 권장 사항

`szDecimal`을 사전 컴파일 결과에 적용해야 합니다.

```solidity
    function getRate(uint32 spotMarket, uint64 assetIndex ) public view override returns (uint256) {

AssetDetails memory details = $.assetDetails[assetIndex];

        (bool success, bytes memory result) = SPOT_PX_PRECOMPILE_ADDRESS.staticcall(abi.encode(spotMarket));
        require(success, Errors.PRECOMPILE_CALL_FAILED());
        uint256 scaledRate = uint256(abi.decode(result, (uint64))) * USDC_SPOT_SCALING* 10 ** (details.szDecimals) ;
        return scaledRate;

}
```

# [H-02] USDC 현물 시장에 대한 사용자 정의 로직 누락

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

addAsset()을 사용하여 지원되는 자산을 추가할 때 spotMarket ID를 제공해야 합니다. 이 시장 ID는 나중에 getRate()에서 사전 컴파일 호출을 통해 예치된 자산의 USD 가치를 가져오는 데 사용됩니다. 그러나 USDC의 경우 가격을 가져올 수 있는 유효한 현물 시장이 없습니다.

```
Universe: [
  {'tokens': [1, 0], 'name': 'PURR/USDC', 'index': 0, 'isCanonical': True},
  {'tokens': [2, 0], 'name': '@1', 'index': 1, 'isCanonical': False},
  {'tokens': [3, 0], 'name': '@2', 'index': 2, 'isCanonical': False}
]
```
나열된 모든 토큰은 USDC에 대해 거래되며, 이는 USDC가 견적 자산임을 의미합니다. 따라서 이미 USD로 표시되어 있으므로 USD로 가격을 검색하는 유효한 시장이 있을 수 없습니다.

## 권장 사항

getRate() 함수(또는 자산 추가 중 업스트림)에 특별 사례를 구현하여 USDC에 대해 1e18의 상수 값을 반환하여 1:1 USD 가치를 나타내도록 하십시오.

# [H-03] 사용자가 EVM에서 L1 전송 중 TVL을 악용하여 주식 인플레이션 유발 가능

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

**사용자가 EVM에서 L1 전송 중 TVL 계산을 악용하여 주식 인플레이션을 유발할 수 있습니다.**

`HyperliquidEscrow` 계약은 자산을 보유하고 EVM 및 L1(코어) 시스템 모두와 상호 작용하는 볼트에 대한 총 잠금 가치(TVL)를 계산하는 역할을 합니다.

TVL은 주식 발행 및 상환의 기준으로 사용되며 현물 및 영구 잔액을 포함하여 EVM 및 L1 소스의 잔액을 합산하여 `tvl()` 함수에서 계산됩니다.

이 설계의 중요한 측면은 자산을 EVM에서 L1 시스템으로 전송하는 것입니다. [Hyperliquid Precompiles에 대한 결정적이지 않은 가이드](https://github.com/PashovAuditGroup/Level_April25_MERGED/issues/32)에 따르면:

> 토큰이 EVM에서 L1으로 전송될 때 토큰이 EVM을 떠났지만 아직 L1 현물 잔액에 입금되지 않은 "사전 입금(pre-crediting)" 기간이 있습니다.

이 기간 동안 `tvl()`의 TVL 계산은 토큰이 EVM이나 L1 잔액에 반영되지 않으므로 관리 중인 자산의 실제 가치를 과소 보고합니다. 이는 사용자가 일시적으로 감소된 TVL을 악용할 수 있는 기회의 창을 만듭니다.

```solidity
    function tvl() external view override returns (uint256 tvl_) {
        // Get the equity, spot, and perp USD balances
        tvl_ = vaultEquity(); // scaled to 1e18
        tvl_ += usdSpotBalance(); // scaled to 1e18
        tvl_ += usdPerpsBalance(); // scaled to 1e18

        V1AssetStorage storage $ = _getV1AssetStorage();

        // Iterate through all supported assets calculate their contract and spot value
        //     and add them to the tvl
        for (uint256 i = 0; i < $.supportedAssets.length(); i++) {
            uint64 assetIndex = uint64($.supportedAssets.at(i));
            AssetDetails memory details = $.assetDetails[assetIndex];
            address assetAddr = details.evmContract;

            uint256 evmScaling = 10 ** (18 - details.evmDecimals);

            if (assetIndex == USDC_SPOT_INDEX) {
                // If the asset is USDC we only need to get the contract balance since we already queried the spot balance
                tvl_ += IERC20(assetAddr).balanceOf(address(this)) * evmScaling;
            } else {
                uint256 rate = getRate(details.spotMarket);
                uint256 balance = IERC20(assetAddr).balanceOf(address(this)) * evmScaling;
                balance += _spotAssetBalance(uint64(assetIndex));
                tvl_ += balance.mulWadDown(rate);
            }
        }
    }
```

### 개념 증명

1. 시스템에는 1000 USDC와 1000 주식이 발행되어 있습니다.
2. `LIQUIDITY_ADMIN_ROLE`이 EVM에서 L1 시스템으로 500 USDC 전송을 시작합니다.
   - EVM 잔액은 즉시 500 USDC 감소합니다.
   - L1 현물 잔액은 아직 입금되지 않았습니다(사전 입금 상태).
   - `tvl()` 함수는 이제 500 USDC만 보고합니다.
3. 사용자가 이 사전 입금 기간 동안 100 USDC를 입금합니다.
   - 주가는 500 USDC / 1000 주 = 0.5 USDC/주로 계산됩니다.
   - 사용자는 100 USDC 예치금에 대해 200 주를 받습니다.
4. 몇 블록 후 L1은 500 USDC를 입금합니다.
   - 실제 TVL은 이제 1100 USDC(1000 원본 + 100 예치금)입니다.
   - 1200 주식이 발행되어 있습니다.
   - 사용자는 200 주식을 183 USDC(200/1200 * 1100)로 상환하여 83 USDC의 이익을 실현할 수 있습니다.
5. 프로토콜과 다른 사용자는 희석되고 공격자는 그들의 비용으로 이익을 얻습니다.

## 권장 사항

이 악용을 방지하기 위해 두 가지 주요 접근 방식이 있습니다.

1. **사전 입금 잔액을 내부적으로 추적:** 전송 중인 자산의 내부 기록을 유지하고 L1에 입금될 때까지 TVL 계산에 포함합니다.
2. **사전 입금 중 입금/상환 일시 중지:** 입금이 확인될 때까지 EVM에서 L1 전송을 시작한 후 몇 블록 동안 입금 및 상환 기능을 일시적으로 비활성화합니다.

# [H-04] 오래된 `inFlightBridgeAmounts`로 인한 잘못된 `tvl` 계산

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`L1EscrowActions` 계약의 `inFlightBridgeAmounts[assetIndex]` 매핑은 자산이 L1에서 결제된 후 지워지지 않습니다. 이로 인해 오래되고 관련 없는 기내 금액이 매핑에 남아 `tvl` 계산에 잘못 포함됩니다.

**재현 단계**
1. **블록 1**: `assetA`에 대해 `bridgeToL1`을 실행합니다. `inFlightBridgeAmounts[assetA]` 매핑이 해당 브리지 금액으로 업데이트됩니다.
2. **블록 2**: 자산 A가 L1에서 결제됩니다. 그러나 `inFlightBridgeAmounts[assetA]` 매핑은 지워지지 않은 상태로 유지됩니다.
3. **블록 3**: `assetB`에 대해 `bridgeToL1`을 실행합니다. `inFlightBridgeAmounts[assetB]` 매핑이 새로운 브리지 금액으로 업데이트됩니다.
4. **블록 3**: `tvl`을 계산합니다. 계산은 활성 `inFlightBridgeAmounts[assetB]`와 함께 오래된 `inFlightBridgeAmounts[assetA]`를 잘못 고려합니다.

이 동작은 부풀려진 `tvl` 보고를 초래하며 프로토콜의 주식 회계에 부차적인 영향을 미칩니다.

## 권장 사항

이 문제를 해결하려면 자산 인덱스와 블록 번호별로 기내 금액을 추적하는 2D 매핑 구조를 구현하는 것을 고려하십시오.
```solidity
mapping(uint64 assetIndex => mapping(uint256 blockNumber => uint256 amount)) inFlightBridgeAmounts;
```

# [M-01] `_validateSpotMarket`이 현물 시장을 잘못 검증함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

_validateSpotMarket() 함수는 시장에 자산과 USDC가 모두 포함되어 있는지 확인하여(시스템은 모든 자산이 USDC에 대해 가격이 책정된다고 가정하기 때문에) 주어진 spotMarket이 제공된 assetIndex_에 대해 유효한지 확인하기 위한 것입니다. 그러나 현재 로직은 OR 조건을 사용합니다.

```solidity
if (spotInfo.tokens[i] == USDC_SPOT_INDEX || spotInfo.tokens[i] == assetIndex_) {
    return true;
}
```
모든 유효한 현물 시장은 USDC에 대해 자산을 쌍으로 구성하므로 USDC_SPOT_INDEX(0)는 항상 토큰 중 하나에 나타납니다. 이로 인해 시장이 주어진 자산과 전혀 관련이 없더라도 함수가 true를 반환합니다.

예시:

assetIndex_ = 5 (예: 일부 토큰)

spotInfo.tokens = [2, 0] (USDC에 대한 토큰 2 시장)

5가 시장에 전혀 관여하지 않더라도 tokens[1] == USDC_SPOT_INDEX이므로 확인은 true를 반환합니다.

## 권장 사항

토큰 중 하나가 USDC_SPOT_INDEX이고 다른 하나가 assetIndex_인지 확인하도록 유효성 검사 로직을 변경하십시오.

```solidity
function _validateSpotMarket(uint64 assetIndex_, uint32 spotMarket) internal view returns (bool) {
    (bool success, bytes memory result) = SPOT_INFO_PRECOMPILE_ADDRESS.staticcall(abi.encode(spotMarket));
    require(success, Errors.PRECOMPILE_CALL_FAILED());
    SpotInfo memory spotInfo = abi.decode(result, (SpotInfo));

    return (
        (spotInfo.tokens[0] == assetIndex_ && spotInfo.tokens[1] == USDC_SPOT_INDEX) ||
        (spotInfo.tokens[1] == assetIndex_ && spotInfo.tokens[0] == USDC_SPOT_INDEX)
    );
}
```

# [M-02] L1으로 브리지할 때 소액 소각

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

bridgeToL1() 함수는 에스크로에서 EVM의 시스템 주소로 토큰을 전송하여 L1으로의 브리지를 시작합니다. 그러나 EVM 측 토큰에 추가 십진수 정밀도(즉, 예상되는 extraEvmWeiDecimals보다 많음)가 있는 경우 둥글지 않은 값(10**extraEvmWeiDecimals로 깔끔하게 나누어지지 않음)은 토큰 소각으로 이어집니다.
이 동작은 프로토콜의 주의 사항에 언급되어 있으며 로그가 구문 분석되는 방식과 L1 입금이 작동하는 방식에 체계적입니다. 그러나 브리지 로직은 이러한 금액을 미리 조정하거나 정리하지 않습니다. 이로 인해 영구적인 토큰 손실이 발생합니다.

## 권장 사항

전송하기 전에 다음과 같이 금액을 내림하십시오.
```solidity
uint256 factor = 10 ** details.extraEvmWeiDecimals;
amounts[i] = amounts[i] - (amounts[i] % factor); IERC20(details.evmContract).transfer(_assetSystemAddr(assetIndexes[i]), amounts[i]);

```

# [M-03] 입금/상환 함수에 슬리피지 보호 누락

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`HyperVaultRouter` 계약은 사용자가 다양한 자산을 예치하고 그 대가로 주식 토큰을 받을 수 있는 볼트 시스템을 구현합니다. `deposit()` 함수의 주식 계산은 예치된 자산의 USD 가치에 의존하며, 이는 에스크로 계약에서 요금을 쿼리하여 결정됩니다. 그러나 이 기능에는 슬리피지 보호 기능이 없어 사용자는 샌드위치 공격 및 불리한 요금 조작에 취약합니다.

```solidity
    function deposit(address asset, uint256 amount) external override nonReentrant returns (uint256 shares) {
    function redeem(uint256 shares) external override nonReentrant returns (uint256 amount) {
```

주식에 대한 최소 출력 매개변수가 없으므로 사용자는 자신이 받을 최소 주식 수에 대한 제어 권한이 없습니다. 이는 특히 다음과 같은 이유로 문제가 됩니다.

1. 요금은 거래 제출과 실행 사이에 변동될 수 있습니다.
2. MEV 봇은 트랜잭션을 샌드위치하여 요금을 조작할 수 있습니다.

## 권장 사항

`deposit()` 및 `redeem()` 함수에 `minOut` 매개변수를 추가하여 사용자가 받을 것으로 예상되는 최소 주식/자산 수를 지정할 수 있도록 하십시오.

# [M-04] `maxRedeemable` 계산이 `withdrawAsset` 비율을 놓침

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`maxRedeemable` 함수는 다음 공식을 사용하여 최대 상환 가능 금액을 계산합니다.

```solidity
return maxWithdraw.mulDivDown(_shareSupply(), tvl());
```

이 계산은 `withdrawAsset` 값이 TVL에 정비례한다고 가정합니다. 그러나 `withdrawAsset`의 비율(예: USD 가치)과 소수점은 고려하지 않습니다. `withdrawAsset`의 비율이 다른 경우 계산 결과가 잘못되어 `maxRedeemable`에 의존하는 사람에게 잠재적인 오기 및 통합 문제가 발생할 수 있습니다.

## 권장 사항

상환 가능 금액을 계산할 때 `withdrawAsset` 비율(`getRate` 참조)과 올바른 소수점 스케일링을 고려하는 것이 좋습니다.

# [L-01] 인덱스 충돌로 지원되지 않는 자산이 인출 자산으로 설정될 수 있음

setWithdrawAsset() 함수는 모든 ERC20 토큰을 인출 가능한 자산으로 설정할 수 있도록 허용하지만 $.assetIndexes[asset]에서 반환된 인덱스를 사용하여 지원 여부를 확인합니다. 주어진 자산이 지원되지 않는 경우 assetIndexes[asset]은 USDC 인덱스에 해당하는 기본값 0을 반환합니다. 이로 인해 _isAssetSupported()가 잘못된 true를 반환하여 지원되지 않는 토큰을 설정할 수 있습니다. 결과적으로 redeem() 함수는 요금을 계산하거나 상환 가능한 자산을 전송하려고 할 때 실패하여 인출이 발생합니다.

```solidity
    function setWithdrawAsset(address asset) external onlyOwner {
        V1Storage storage $ = _getV1Storage();
        uint64 assetIndex_ = $.assetIndexes[asset];
        require(_isAssetSupported($, assetIndex_), Errors.COLLATERAL_NOT_SUPPORTED());
```
assetIndex_가 0인 경우 자산 주소가 USDC여야 한다는 확인을 추가하십시오.

# [L-02] 자산 제거 후 `withdrawAsset`이 `address(0)`으로 설정될 위험

`HyperVaultRouter` 계약의 `removeAsset()` 함수는 제거되는 자산이 현재 `withdrawAsset`인 경우 `withdrawAsset`을 `address(0)`으로 설정합니다.

```solidity
if (asset == $.withdrawAsset) $.withdrawAsset = address(0);
```
이는 소유자가 `withdrawAsset`을 제거하기 위해 `removeAsset`을 호출한 다음 `renounceOwnership()` 함수를 호출할 수 있는 위험을 생성합니다. 새 `withdrawAsset`을 할당하지 않고 소유권이 포기되면 다음 확인으로 인해 `redeem()` 함수를 영구적으로 사용할 수 없게 됩니다.
```solidity
require($.withdrawAsset != address(0), Errors.ADDRESS_ZERO());
```
이 문제는 다음과 같이 해결할 수 있습니다.
`withdrawAsset`이 `address(0)`으로 설정된 상태에서 소유권 포기를 방지하거나 제거하기 전에 새 withdrawAsset을 설정하십시오.

# [L-03] 중복 조건 확인

`HyperliquidEscrow`의 `_scaleToSpotDecimals()` 함수에는 영구 시장과 현물 시장 간의 소수점 스케일링에 대한 중복 조건 확인이 포함되어 있습니다. 함수는 `USDC_PERP_DECIMALS > USDC_SPOT_DECIMALS`인지 확인하지만 계약 상수에 `USDC_PERP_DECIMALS`는 6으로, `USDC_SPOT_DECIMALS`는 8로 정의되어 있으므로 이 조건은 절대 true가 될 수 없습니다.

```solidity
    function _scaleToSpotDecimals(uint64 amount_) internal pure returns (uint64) {
        return (USDC_PERP_DECIMALS > USDC_SPOT_DECIMALS)
            ? uint64(amount_ / (10 ** (USDC_PERP_DECIMALS - USDC_SPOT_DECIMALS)))
            : uint64(amount_ * (10 ** (USDC_SPOT_DECIMALS - USDC_PERP_DECIMALS)));
    }
```

권장 사항: 조건부 로직을 제거하고 스케일링 작업을 직접 구현하여 함수를 단순화하십시오.

```solidity
function _scaleToSpotDecimals(uint64 amount_) internal pure returns (uint64) {
    return uint64(amount_ * (10 ** (USDC_SPOT_DECIMALS - USDC_PERP_DECIMALS)));
}
```

# [L-04] OpenZeppelin `ProxyAdmin` 계약에서 `upgrade` 메서드를 사용할 수 없음

`UpgradeRouterScript` 스크립트는 `ProxyAdmin` 계약에서 `upgrade` 메서드를 호출하려고 시도합니다.

```solidity
proxyAdmin.upgrade(ROUTER_PROXY_ADDRESS, address(router));
```

그러나 활용된 OpenZeppelin `ProxyAdmin` 계약(v5.2.0)에는 `upgrade` 메서드가 포함되어 있지 않습니다. 대신 추가 `bytes memory data` 매개변수가 필요한 `upgradeAndCall` 메서드를 제공합니다.

`upgrade` 메서드 호출을 `upgradeAndCall`로 대체하고 빈 `data` 매개변수를 전달하는 것이 좋습니다.

# [L-05] 배포 스크립트에 `deployer == OWNER` 일관성 확인 부족

배포 스크립트(`DeployRouterTestnet.s.sol`)는 계약 배포 중에 `deployer`와 `OWNER`를 소유자 및 프록시 관리자로 상호 교환적으로 사용합니다. 그러나 `deployer == OWNER`인지 확인하지 않습니다. 이러한 불일치는 특히 라우터 및 에스크로 업그레이드 스크립트 실행 중에 권한 문제로 이어질 수 있습니다. 이러한 스크립트는 올바른 소유자 및 프록시 관리자 권한에 의존하기 때문입니다.

스크립트 시작 부분에 `deployer == OWNER`인지 확인하는 검사를 추가하는 것이 좋습니다.
```solidity
require(deployer == OWNER, "Deployer must match OWNER");
```

# [L-06] 배포 스크립트에서 `BURNER_ROLE`이 할당되지 않음

배포 스크립트(`DeployRouterTestnet.s.sol`)는 `WrappedVaultShare` 토큰에서 `HyperVaultRouter`에 대해 `MINTER_ROLE`을 설정하지만 `BURNER_ROLE`을 할당하는 것을 무시합니다. `BURNER_ROLE`이 없으면 라우터는 인출 중에 적절한 기능에 중요한 주식 토큰을 소각할 수 없습니다.

라우터에 `BURNER_ROLE`을 할당하려면 배포 스크립트에 다음 줄을 추가하는 것이 좋습니다.
```solidity
shareToken.grantRole(shareToken.BURNER_ROLE(), address(router));
```

# [L-07] 에스크로 초기화 중 `LIQUIDITY_ADMIN_ROLE`이 할당되지 않음

중요한 유동성 관리 기능(예: `bridgeToL1`, `trade`)에 필요한 `LIQUIDITY_ADMIN_ROLE`은 에스크로 계약 초기화 중에 부여되지 않습니다. 이로 인해 이러한 작업을 수행할 수 있는 권한이 부여된 계정이 없어 계약의 유동성 작업을 사용할 수 없게 됩니다.

계약 초기화 중 또는 배포 스크립트에서 관리자 주소에 `LIQUIDITY_ADMIN_ROLE`을 부여하는 것이 좋습니다.

# [L-08] `WrappedVaultShare` 생성자가 ROUTER를 잘못 할당함

`WrappedVaultShare`는 생성자 인수를 불변 상태 변수 대신 자신에게 잘못 할당합니다.

```solidity
 constructor(string memory name, string memory symbol, address router, address admin)
        MintableToken(name, symbol, 18, admin)
    {
        router = ROUTER;
    }
```
결과적으로 코드가 컴파일되지 않습니다.

권장 사항:

다음과 같이 변경하십시오.
`ROUTER = router;`

