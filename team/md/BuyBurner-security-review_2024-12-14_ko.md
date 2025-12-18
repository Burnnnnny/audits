# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**avierauy/buyburner** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# BuyBurner 소개 (About BuyBurner)

BuyBurner 계약은 승인된 소각자(burner)가 USDC 또는 ETH를 사용하여 Uniswap에서 BEAM 토큰을 구매한 다음 소각하여 공급을 줄이고 디플레이션을 지원할 수 있도록 합니다. 관리자는 사용하지 않는 토큰이나 ETH를 인출할 수도 있습니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
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

_검토 커밋 해시_ - [bb4e8a812e6bda4838e84d0bae1b93a6896ffca3](https://github.com/avierauy/buyburner/tree/bb4e8a812e6bda4838e84d0bae1b93a6896ffca3)

_수정 검토 커밋 해시_ - [4f22ab9be3555200357273332112ff1429d9f3a8](https://github.com/avierauy/buyburner/tree/4f22ab9be3555200357273332112ff1429d9f3a8)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `BuyBurner`

# 발견 사항 (Findings)

# [H-01] 조작 가능한 오라클 및 손상된 슬리피지 보호

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

프로토콜의 BEAM 구매는 외부 당사자에 의해 영향을 받을 수 있습니다. 예상되는 BEAM 수령액은 `UNISWAP_V2_ROUTER_02.getAmountsOut(...)`으로 계산되며, 이는 관련 쌍(pair)의 [토큰 준비금(reserves)에 따라 달라집니다](https://github.com/Uniswap/v2-periphery/blob/master/contracts/libraries/UniswapV2Library.sol#L62-L70).

```solidity
    function _getTokenAmountPrice(uint256 tokenAmount, address token0, address token1)
        internal
        view
        returns (uint256)
    {
        ...

@>      uint256[] memory returnedParam = UNISWAP_V2_ROUTER_02.getAmountsOut(tokenAmount, path);

        return returnedParam[1];
    }
...
    function _getTokenAmountPrice3(uint256 tokenAmount, address token0, address token1, address token2)
        internal
        view
        returns (uint256)
    {
        ...

@>      uint256[] memory returnedParam = UNISWAP_V2_ROUTER_02.getAmountsOut(tokenAmount, path);

        return returnedParam[2];
    }
```

이러한 준비금은 쉽게 조작할 수 있습니다. 공격자는 거대한 스왑으로 선행 실행(frontrun)하여 쌍에서 거의 모든 가격을 설정할 수 있으며, 이에 따라 `_getAmountOutMin()`에 영향을 줄 수 있습니다.

이 계산에서 slippagePercent는 설명된 조작으로부터 보호를 제공하지 않는 고정 숫자일 뿐입니다.

결과적으로 각 스왑에서 프로토콜이 거의 0에 가까운 BEAM을 구매하게 만들기 쉽습니다.

그러나 실제로 공격자의 경우 수익을 극대화하기 위해 샌드위치 공격의 정확한 크기를 찾는 것이 더 수익성이 높을 것입니다(계산은 [여기](https://cmichel.io/de-fi-sandwich-attacks/)에서 찾을 수 있음) 각 거래에서 토큰을 훔칠 수 있습니다.

## 권장 사항

조작이 덜 가능한 오라클(동일한 쌍의 TWAP 또는 Chainlink)을 사용하는 것을 고려하거나 슬리피지 비율(percentage)이 아니라 `buy()`에서 예상되는 BEAM의 **양(amount)**을 입력하는 것을 고려하십시오.

# [L-01] buy() 함수에서 slippagePercent에 대한 안전 확인 추가

buy() 함수에서 slippagePercent에 대해 두 가지 안전 확인을 추가해야 합니다.

1. 10000보다 크지 않은지 확인해야 합니다.
2. `BUYER_ROLE`이 매개변수를 오용하여 슬리피지를 절대 최대값인 10000으로 설정할 수 없도록 해야 합니다. 이를 위해 `DEFAULT_ADMIN_ROLE`이 `BUYER_ROLE`이 초과할 수 없는 허용 한도를 설정할 수 있도록 허용하는 것을 고려하십시오.

```solidity
function buy(uint256 amountIn, uint256 slippagePercent, bool useUSDC) external onlyRole(BUYER_ROLE) {
```

# [L-02] `block.timestamp`를 스왑 마감 기한으로 사용하면 보호 기능이 제공되지 않음

`_swapETHForBEAM()` 및 `_swapUSDCforBEAM()`은 `deadline = block.timestamp()`로 스왑을 수행합니다. 트랜잭션이 결국 들어가는 블록은 `block.timestamp`가 되므로 이는 보호를 제공하지 않습니다.

권장 사항: 마감 기한(deadline)을 함수 인수로 전달하십시오.
