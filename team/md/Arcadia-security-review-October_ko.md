# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**arcadia-finance/accounts-v2** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Arcadia 소개

Arcadia는 개인, DAO 및 프로토콜이 마진 계정을 통해 자산을 담보로 관리할 수 있도록 하는 온체인 시스템입니다. 자산 관리 및 유동성 재조정을 단순화하여 사용자를 슬리피지로부터 보호하고 금융 프로토콜 전반에 걸친 부채를 지원합니다.

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

_검토 커밋 해시:_

- [08bc2a06dcc73ec13d326fe812179d865e3c67c5](https://github.com/arcadia-finance/accounts-v2/tree/08bc2a06dcc73ec13d326fe812179d865e3c67c5)
- [56a5cbaaeb1fe4999c4ae52fd2cc1460af7ff544](https://github.com/arcadia-finance/asset-managers/tree/56a5cbaaeb1fe4999c4ae52fd2cc1460af7ff544)

_수정 검토 커밋 해시:_

- [58f43ffe29ff435fcc25417f3199e52beadc0907](https://github.com/arcadia-finance/accounts-v2/tree/58f43ffe29ff435fcc25417f3199e52beadc0907)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Rebalancer`
- `StakedSlipstreamLogic`
- `RebalanceLogic`
- `UniswapV3Logic`
- `SwapLogic`
- `FeeLogic`
- `BurnLogic`
- `SlipstreamLogic`
- `MintLogic`
- `RebalanceOptimizationMath`
- `AccountSpot`
- `DefaultUniswapV4AM`
- `UniswapV4HooksRegistry`

# 발견 사항

# [M-01] 인출된 ETH가 `to` 주소 대신 `msg.sender`로 전송됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`AccountSpot` 계약에서 `_withdraw` 함수는 ETH 인출의 경우 인출된 자금의 수령자로 `to` 주소를 사용하지 않고 대신 `msg.sender`를 수령자로 사용합니다. 이는 인출자가 자금을 받을 다른 주소를 지정할 수 있는 기능을 제거하며, NatSpec 주석에서 `to` 주소가 인출된 자금의 수령자임을 명시적으로 나타내므로 호출자가 자금이 `to` 주소로 전송될 것으로 예상하는 경우 자금 손실로 이어질 수 있습니다.

```solidity
@>   * @param to The address to withdraw to.
     */
    function _withdraw(
        address[] memory assetAddresses,
        uint256[] memory assetIds,
        uint256[] memory assetAmounts,
        uint256[] memory assetTypes,
@>      address to
    ) internal {
        for (uint256 i; i < assetAddresses.length; ++i) {
            // Skip if amount is 0 to prevent transferring addresses that have 0 balance.
            if (assetAmounts[i] == 0) continue;

            if (assetAddresses[i] == address(0)) {
@>              (bool success, bytes memory result) = payable(msg.sender).call{ value: assetAmounts[i] }("");
                require(success, string(result));
```

## 권장 사항

ETH 인출의 경우 `to` 주소를 인출된 자금의 수령자로 사용하십시오.
자금이 호출자의 주소로만 인출될 수 있는 것이 예상되는 동작인 경우 `to == msg.sender`인지 확인하는 조건을 추가하고 그렇지 않은 경우 되돌리십시오(revert).

# [L-01] `getAssetModule`의 잘못된 반환 값

`UniswapV4HooksRegistry` 계약의 `public` `getAssetModule` 함수는 제공된 `assetId`의 존재를 확인하지 않습니다. 이러한 간과로 인해 빈 `poolKey`가 생성되고 `assetId`가 존재하지 않는 경우에도 함수가 기본 자산 모듈(`DEFAULT_UNISWAP_V4_AM`)을 잘못 반환할 수 있습니다.

```solidity
    function getAssetModule(uint256 assetId) public view returns (address assetModule) {
>>>     (PoolKey memory poolKey,) = POSITION_MANAGER.getPoolAndPositionInfo(assetId);

        // Check if we can use the default Uniswap V4 AM.
>>>     if (
            Hooks.hasPermission(uint160(address(poolKey.hooks)), Hooks.BEFORE_REMOVE_LIQUIDITY_FLAG)
                || Hooks.hasPermission(uint160(address(poolKey.hooks)), Hooks.AFTER_REMOVE_LIQUIDITY_FLAG)
        ) {
            // If not a specific Uniswap V4 AM must have been set.
            // Returns the zero address if no Asset Module is set.
            assetModule = hooksToAssetModule[address(poolKey.hooks)];
        } else {
            // If BEFORE_REMOVE_LIQUIDITY_FLAG and AFTER_REMOVE_LIQUIDITY_FLAG are not set,
            //Then we use the default Uniswap V4 AM.
            // The NoOP hook "AFTER_REMOVE_LIQUIDITY_RETURNS_DELTA_FLAG" is by default not allowed,
            // as it can only be accessed if "AFTER_REMOVE_LIQUIDITY_FLAG" is implemented.
            assetModule = DEFAULT_UNISWAP_V4_AM;
        }
    }
```

확인 부재로 인해 공개 함수에서 오해의 소지가 있는 정보가 반환될 수 있습니다. 테스트:

```solidity
    function testFuzz_Success_getAssetModule_InvalidTokenId(uint96 tokenId)
        public
    {
        // Calling getAssetModule()
        address assetModule = v4HooksRegistry.getAssetModule(tokenId);

        // It returns DefaultUniswapV4AM. It should returns `address(0)` or revert() since tokenId does not exist.
        assertEq(assetModule, address(uniswapV4AM));
    }
```

`poolKey`의 존재를 확인하는 검사를 구현하십시오. `poolKey`가 비어 있으면 `getAssetModule` 함수 호출이 되돌려지거나(revert) 존재하지 않음을 명확하게 나타내기 위해 제로 주소를 반환해야 합니다.

# [L-02] `getRiskFactors()`가 항상 되돌림

DefaultUniswapV4AM의 `getRiskFactors()` 함수는 토큰 위험 요소를 얻기 위해 `IRegistry(REGISTRY).getRiskFactors(address, address[], uint256[])`를 호출합니다. DefaultUniswapV4AM의 REGISTRY 값은 자산 관리자와 기본 레지스트리 계약 사이의 중간 계약 역할을 하는 UniswapV4HooksRegistry입니다. 문제는 UniswapV4HooksRegistry에 해당 함수가 없으며 `getRiskFactors(address creditor, address asset, uint256 assetId)`만 구현한다는 것입니다. 결과적으로 `getRiskFactors()` 함수는 DefaultUniswapV4AM 및 UniswapV4HooksRegistry 계약에 대해 되돌려집니다.
영향은 Uniswap V4 자산에 대한 위험 요소를 계산할 수 없으며 이 정보에 의존하는 다른 스마트 계약에서 사용할 수 없다는 것입니다.

UniswapV4HooksRegistry에 `getRiskFactors(address, address[], uint256[])` 함수를 추가하십시오.

