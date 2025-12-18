# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Azuro-protocol/Azuro-v2** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Azuro 소개

BetExpress 계약은 단일 베팅에서 다양한 Azuro 조건의 결과를 결합하는 베팅 엔진입니다. BetExpress 계약을 통해 사용자는 콤보 베팅을 할 수 있습니다.

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

_검토 커밋 해시_ - [5d36c9cfc98afb1910692df106275cae81750652](https://github.com/Azuro-protocol/Azuro-v2/tree/5d36c9cfc98afb1910692df106275cae81750652)

_수정 검토 커밋 해시_ - [a4ec8c7edaceae35c0f44b19eeb58a13d1929c9d](https://github.com/Azuro-protocol/Azuro-v2/tree/a4ec8c7edaceae35c0f44b19eeb58a13d1929c9d)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `BetExpress`

# 발견 사항

# [H-01] 베팅에 결과가 두 개 이상인 조건이 있는 경우 BetExpress가 깨짐

## 심각도

**영향:** 높음, 두 개 이상의 승리 결과가 있는 조건이 있는 경우 BetExpress의 기능이 작동하지 않습니다.

**가능성:** 중간, 조건에 두 개 이상의 승리 결과가 있는 경우 발생합니다.

## 설명

두 개 이상의 결과를 지원하는 조건에 대해 입금하는 시나리오에서, BetExpress의 구성 로직은 여러 결과가 가능함에도 불구하고 `condition.payouts` 배열에 하나의 `subBetPayout`만 푸시하여 `condition.payouts` 배열을 부적절하게 처리합니다.

```solidity
        if (outcomeOrdinal == 0) {
            condition.payouts.push(subBetPayout);
```

변수 `winningOutcomesCount`는 조건에 대한 승리 결과를 계산하도록 지정되었습니다. 조건이 두 개 이상의 결과로 구성된 경우 이 수는 2를 초과하여 여러 승리 결과를 나타냅니다.

```solidity
        if (condition.winningOutcomesCount == 0)
            condition.winningOutcomesCount = winningOutcomesCounts[
                i
            ];
```

유동성 예약 계산 중에 `Math.maxSum` 함수가 `condition.payouts` 및 `condition.winningOutcomesCount`를 인수로 사용하여 호출되어 충분한 유동성이 예약되도록 합니다.

```solidity
        function _calcReserve(
            Condition storage condition
        ) internal view returns (uint128) {
            return
                uint128(
                    Math.diffOrZero(
                        Math.maxSum(
                            condition.payouts,
                            condition.winningOutcomesCount
                        ),
                        condition.fund
                    )
                );
        }
```

그러나 이 로직은 여러 결과가 있는 조건에 대해 실패합니다. `payouts` 배열에는 단일 요소만 포함되어 있지만 `n >= 2`이기 때문입니다.

```solidity
        /**
        * @notice Get the sum of `n` max items of `a`.
        */
        function maxSum(
            uint128[] memory a,
            uint256 n
        ) internal pure returns (uint256 sum_) {
            if (n < 2) return max(a);

            uint256 length = a.length;

            uint128[] memory sorted = new uint128[](length);
            for (uint256 i = 0; i < length; ++i) {
                sorted[i] = a[i];
            }
            sort(sorted, 0, length - 1);

            for (uint256 i = 0; i < n; ++i) {
                sum_ += sorted[length - 1 - i];
            }
        }
```

이 작업은 `length = 1` 및 `i = 1`일 때 `length - 1 - i`가 음수가 되는 합산 루프의 언더플로우로 인해 되돌리기를 유발합니다(`n = 2`이므로).

```solidity
            for (uint256 i = 0; i < n; ++i) {
                sum_ += sorted[length - 1 - i];
            }
```

## 권장 사항

승리 결과가 두 개 이상인 경우를 처리하십시오.

# [M-01] 패배한 베팅에 대해 지급이 해결되지 않음

## 심각도

**영향:** 중간, 핵심 기능인 resolvePayout에 영향을 미쳐 베팅 해결이 지연되고 결과적으로 자금의 효율적인 사용에 영향을 미칩니다.

**가능성:** 중간, 시퀀스의 첫 번째가 아닌 하위 베팅이 승리 결과가 아니면서 먼저 해결되는 일반적인 시나리오가 있습니다.

## 설명

익스프레스 베팅의 하위 베팅 중 하나라도 패배하면 전체 베팅은 패배한 것으로 간주되어 그에 따라 해결되어야 합니다. `condition.state` = RESOLVED이면 지급액이 0을 반환하는 것을 볼 수 있습니다.

```solidity
        function _calcPayout(Bet storage bet) internal view returns (uint128) {
            ...
            for (uint256 i = 0; i < length; ++i) {
                ICoreBase.CoreBetData storage subBet = subBets[i];
                ICondition.Condition memory condition = core.getCondition(
                    subBet.conditionId
                );

                if (condition.state == IConditionState.ConditionState.RESOLVED) {
                    if (core.isOutcomeWinning(subBet.conditionId, subBet.outcomeId))
                        winningOdds = winningOdds.mul(bet.conditionOdds[i]);
                    else return 0;
                } else if (
                    !(condition.state == IConditionState.ConditionState.CANCELED ||
                        lp.isGameCanceled(condition.gameId))
                ) {
                    revert ConditionNotFinished(subBet.conditionId);
                }
            ...
        }
```

문제는 subBets가 첫 번째 요소부터 순서대로 확인되기 때문에 발생합니다. 패배한 하위 베팅 이전에 해결되지 않은 하위 베팅이 있는 경우 전체 익스프레스 베팅은 해결되지 않은 상태로 유지됩니다. 예를 들어 다음 조건이 있는 세 가지 하위 베팅으로 구성된 익스프레스 베팅을 고려해 보십시오.

- 1: ConditionState.CREATED
- 2: ConditionState.RESOLVED (패배)
- 3: ConditionState.RESOLVED (패배)
  이 베팅은 해결될 수 없습니다.

이 시나리오는 익스프레스 베팅이 해결되는 것을 방지하여 자금이 불필요하게 잠기게 하고 DAO 및 오라클에 대한 지급을 지연시킵니다. 극단적인 경우 해결은 최종 하위 베팅의 결과를 기다립니다. 각 예측 엔진에는 잠긴 유동성 제한 수준이 있으며, 해결되지 않은 베팅은 수요가 많은 경우 다른 베터가 엔진을 사용할 수 없게 만들 수 있습니다.

## 권장 사항

모든 하위 베팅을 반복하여 패배한 베팅을 즉시 식별하고 0을 반환하여 모든 하위 베팅이 완료될 때까지 기다리지 않고 익스프레스 베팅을 해결할 수 있도록 합니다.

# [M-02] 신중하게 타이밍을 맞춘 익스프레스 베팅이 전체 유동성 풀을 잠글 수 있음

## 심각도

**영향:** 높음, 다른 사용자가 다른 베팅을 할 수 없으며 익스프레스 베팅이 성공하면 전체 유동성 풀이 소진됩니다.

**가능성:** 낮음, 공격은 짧은 기간 동안만 수행할 수 있으며 자본 손실 위험이 큰 상당한 초기 자본이 필요합니다.

## 설명

문서에 자세히 설명되어 있고 코드에 구현된 대로 다양한 수준에서 최대 익스프레스 베팅에 대한 제한이 있습니다.

- 익스프레스 베팅에 대한 최대 배당률이 있습니다.
- 익스프레스 베팅의 각 조건에는 강화 제한(해당 조건에 대해 풀에서 예약할 수 있는 최대 금액)이 있습니다.
- BetExpress 계약(코어)이 유동성 볼트에서 잠글 수 있는 유동성 양에 제한이 있습니다.

처음 두 가지 보호 장치는 적절하지만 BetExpress 계약에 대한 `reinforcementAbility` 제한은 충분히 엄격하지 않습니다. Factory 계약을 통해 새 BetExpress 계약이 코어에 연결되면 `addCore` 메서드가 호출되어 코어의 `reinforcementAbility`를 기본적으로 1로 설정합니다.

```solidity
function addCore(address core) external override onlyFactory {
        CoreData storage coreData = _getCore(core);
        coreData.minBet = 1;
        coreData.reinforcementAbility = uint64(FixedMath.ONE);
        coreData.state = CoreState.ACTIVE;

        emit CoreSettingsUpdated(
            core,
            CoreState.ACTIVE,
            uint64(FixedMath.ONE),
            1
        );
    }
```

기본적으로 이는 코어가 LP의 모든 유동성을 잠글 수 있도록 허용합니다. 이것은 `changeLockedLiquidity` 메서드에서 볼 수 있습니다.

```solidity
function changeLockedLiquidity(
        uint256 gameId,
        int128 deltaReserve
    ) external override isActive(msg.sender) {
        if (deltaReserve > 0) {
            uint128 _deltaReserve = uint128(deltaReserve);
            if (gameId > 0) {
                games[gameId].lockedLiquidity += _deltaReserve;
            }

            CoreData storage coreData = _getCore(msg.sender);
            coreData.lockedLiquidity += _deltaReserve;

            vault.lockLiquidity(_deltaReserve);

            if (coreData.lockedLiquidity > getLockedLiquidityLimit(msg.sender))
                revert LockedLiquidityLimitReached();
        } else
            _reduceLockedLiquidity(msg.sender, gameId, uint128(-deltaReserve));
    }
```

이 메서드는 잠재적인 성공적인 베팅 지급을 위해 LP의 유동성을 예약하기 위해 새로운 익스프레스 베팅이 이루어질 때 BetExpress에서 호출되며 내부적으로 `getLockedLiquidityLimit`를 호출합니다.

```solidity
function getLockedLiquidityLimit(
        address core
    ) public view returns (uint128) {
        return
            uint128(
                _getCore(core).reinforcementAbility.mul(vault.getReserve())
            );
    }
```

따라서 기본적으로 코어는 LP 소유자가 `updateCoreSettings`를 호출하여 코어의 설정을 명시적으로 업데이트할 때까지 유동성 풀의 100%를 잠글 수 있습니다.

이는 배당률이 기본적으로 최대 1000까지 복리될 수 있기 때문에 익스프레스 베팅에서 특히 중요합니다. 따라서 악의적인 사용자는 BetExpress 계약이 기존 코어에 연결될 때까지 기다렸다가 하위 베팅 조건에 대한 강화 제한에 도달하지 않고 배당률을 1000에 가깝게 최대화하는 하위 베팅 선택으로 즉시 익스프레스 베팅을 할 수 있습니다. 최대의 피해를 입히려는 공격자는 이러한 조건을 가능한 한 미래에 해결되도록 선택하여 유동성이 잠겨 있고 다른 베터가 계약을 사용할 수 없는 시간을 최대화합니다.

이 시점에서 관리자는 다음 중 하나를 수행할 수 있습니다.

1. 조건이 해결되기를 기다리고 그 중 하나가 베팅에 실패하기를 바랍니다(그렇지 않으면 전체 풀이 고갈됩니다).
2. 하위 베팅의 모든 조건에서 모든 조건을 취소하여 베팅 ID가 지급되고 유동성이 LP로 다시 릴리스되도록 합니다. 이는 기존(익스프레스가 아닌) 베팅에서 동일한 조건을 사용하는 다른 계약의 다른 베터에게 부정적인 영향을 미칠 수 있습니다.

공격자는 자본을 위험에 빠뜨려야 하지만 충분한 자금을 가진 공격자는 전체 풀을 포획할 가능성이 있고 LP 소유자가 다른 고객의 다운타임을 줄이기 위해 위의 옵션 2를 선택할 가능성이 높기 때문에(공격자가 자금을 돌려받음을 의미) 위험을 감수할 수 있습니다.

## 권장 사항

새로운 `BetExpress` 계약이 유동성 풀에 연결될 때 팩토리 `plugExpress` 메서드는 LP 소유자가 계약의 `reinforcementAbility`를 지정할 수 있도록 허용해야 합니다. 등록 중에 이를 지정함으로써 등록 후 `updateCoreSettings` 호출 전까지 BetExpress 계약이 유동성 풀을 잠글 수 있는 창이 없습니다.

# [L-01] 안전하지 않은 숫자 캐스팅 관행

uint256에서 uint128 로의 캐스팅 인스턴스 중 일부는 안전하지 않습니다. 오버플로우가 발생하여 BetExpress의 잘못된 회계로 이어질 가능성이 있습니다.

uint128()이 uint256 숫자를 변환하는 데 사용되는 경우가 있습니다.

```solidity
        subBetAmount = uint128(amount_.mul(subBetReserveShare));
        subBetPayout = uint128(payout.mul(subBetReserveShare));
```

uint128 숫자의 최댓값은 e39입니다. 따라서 18 소수점의 금액에 대한 가능성은 매우 낮습니다. 그러나 시스템이 미래에 더 높은 소수점을 가진 다른 토큰을 사용하는 경우 오버플로우가 발생할 수 있습니다. 토큰은 유동성 풀 생성자가 선택한 모든 토큰이 될 수 있습니다.

안전한 캐스팅을 일관되게 사용하는 것이 좋습니다: `amount_.mul(subBetReserveShare).toUint128()`

