# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**ulti-org/ulti-protocol-contract** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# Ulti 소개

ULTI는 사용자가 이더리움 메인넷의 ETH와 같은 블록체인의 기본 통화를 예치하고 그 대가로 ULTI 토큰을 받을 수 있는 DeFi 프로토콜입니다. Ulti는 초기 토큰 분배를 처리하면서 Uniswap에 유동성 풀을 생성하여 토큰을 출시합니다. 사용자는 입력 토큰을 예치하여 ULTI 토큰을 얻을 수 있으며, 보상 청구 및 추천 관리 시스템이 마련되어 있습니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ------------ | -------------- | ----------- |
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

_검토 커밋 해시_ - [926e9bb4ba8a3d2ebb60c2f88ba645b31caad05c](https://github.com/ulti-org/ulti-protocol-contract/tree/926e9bb4ba8a3d2ebb60c2f88ba645b31caad05c)

_수정 검토 커밋 해시_ - [4ec79b2fd3862b3ad11c21601774428dcf1a3d02](https://github.com/ulti-org/ulti-protocol-contract/tree/4ec79b2fd3862b3ad11c21601774428dcf1a3d02)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `ULTI`
- `FullMath`
- `LiquidityAmounts`
- `Oracle`
- `PoolAddress`
- `TickMath`

# 발견 사항

# [M-01] 악의적인 행위자가 LP 기여를 방해(grief)할 수 있음 (Malicious actors can grief the LP contribution)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

ULTI 프로토콜에 ETH를 예치하는 것은 ULTI 토큰을 획득하고 생태계에 참여하기 위한 주요 메커니즘입니다. `deposit()` 함수에는 여러 내부 호출(\_allocateDeposit -> \_tryIncreaseLiquidity -> increaseLiquidity)을 통해 전달되는 `deadline` 매개변수가 포함되어 있습니다:

```solidity
    function deposit(
        uint256 inputTokenAmount,
        address referrer,
        uint256 minUltiToAllocate,
        uint256 deadline,
        bool autoClaim
    ) external nonReentrant unstoppable {
```

그러나 `deadline` 매개변수가 아직 만료되지 않았는지 확인하는 검증이 없습니다. 이러한 간과로 인해 악의적인 사용자는 의도적으로 오래된 기한을 제공하여 시스템을 악용할 수 있습니다. 이런 상황이 발생하면 try nfpm.increaseLiquidity 함수가 조용히 실패합니다:

```solidity
        try nonfungiblePositionManager.increaseLiquidity(increaseParams) {}
        catch {
            // Skip, failing to add liquidity should never block deposits
            // The input token dedicated to liquidity will instead be used to pump
        }
```

결과적으로 LP 기여금(inputTokenForLP 및 ultiForLP)이 향후 펌프를 위해 계약에 남아 있게 됩니다.

## 권장 사항

`block.timestamp > deadline`인 경우 되돌리도록(revert) 하십시오.

# [L-01] 이전 주기에 아무도 입금하지 않은 경우 `pump()`가 올바르지 않게 작동함 (`pump()` behaves incorrectly in case nobody deposited in the previous cycle)

이전 주기에 아무도 입금하지 않은 경우, `_performCycleMaintenance()` 함수는 현재 주기의 펌프에서 항상 실행됩니다.

```solidity
    function pump(uint256 maxInputTokenPerUlti, uint256 deadline)
        external
        nonReentrant
        unstoppable
        returns (uint256 inputTokenToSwap, uint256 ultiToBurn)
    {
        ...

        // 2. Perform cycle maintenance if needed
        uint32 cycle = getCurrentCycle();
        if (cycle > 1 && !isTopContributorsBonusAllocated[cycle - 1]) {
@>          _performCycleMaintenance(cycle - 1);
        }
        ...
```

그 이유는 `_allocateTopContributorsBonuses()`가 항상 일찍 반환되고 주기를 할당된 것으로 표시하지 않기 때문입니다:

```solidity
    function _performCycleMaintenance(uint32 cycle) private {
        if (cycle < 1) return;

        _allocateTopContributorsBonuses(cycle);

        _collectAndProcessLiquidityFees();

        _burnExcessUlti();
    }

    function _allocateTopContributorsBonuses(uint32 cycle) private {
        // 1. Get the total bonus amount allocated for this cycle
        uint256 topContributorsBonusAmount = topContributorsBonuses[cycle];

        // Skip if no bonuses amount were allocated for this cycle
        if (topContributorsBonusAmount == 0) {
            return;
        }
        ...
```

결과적으로:
1) 현재 주기의 펌프는 항상 수수료를 청구하기 위해 추가 가스를 필요로 합니다. 2) 입력 토큰 잔액이 증가하여 펌프가 예상보다 많은 토큰을 교환합니다.

권장 사항: 조기 반환 전에 할당된 것으로 설정하십시오.

```diff
    function _allocateTopContributorsBonuses(uint32 cycle) private {
        // 1. Get the total bonus amount allocated for this cycle
        uint256 topContributorsBonusAmount = topContributorsBonuses[cycle];

        // Skip if no bonuses amount were allocated for this cycle
        if (topContributorsBonusAmount == 0) {
+           isTopContributorsBonusAllocated[cycle] = true;
            return;
        }
        ...
```

# [L-02] `_getLiquidityAmounts()`가 포지션 내의 잘못된 토큰 양을 반환함 (`_getLiquidityAmounts()` returns a wrong amount of tokens in position)

`_getLiquidityAmounts()`가 호출되면 포지션의 입력 토큰 양과 `ULTI` 토큰 양을 반환합니다:

> @return inputTokenAmountInPosition The amount of input token in the current liquidity position

      @return ultiAmountInPosition The amount of ULTI in the current liquidity position

그러나 이를 수행하는 코드는 올바르지 않으며 다른 누군가가 풀에 유동성을 제공한 경우 잘못된 값을 반환합니다. 위에서 논의된 값은 다음과 같이 계산됩니다:

```solidity
uint128 liquidity = liquidityPool.liquidity();
...
(uint256 amount0, uint256 amount1) = LiquidityAmounts.getAmountsForLiquidity(sqrtPriceX96, sqrtRatioAX96, sqrtRatioBX96, liquidity);
...
```

우리는 풀의 유동성을 기반으로 `amount0`과 `amount1`을 계산합니다. 우리 포지션이 풀의 유일한 포지션이 아닐 수 있으므로 이는 잘못된 것입니다. 다른 누군가가 풀에 유동성을 제공했다면 반환 값에는 그들의 금액도 포함되어 올바르지 않게 됩니다.

금액을 올바르게 계산하려면 풀의 총 유동성 대신 우리 포지션의 유동성을 가져와 `getAmountsForLiquidity()` 함수 입력에 사용하십시오.
