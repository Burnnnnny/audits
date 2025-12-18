# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **hyperstable/contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Hyperstable 정보 (About Hyperstable)

Hyperstable은 사용자가 지원되는 담보를 예치하여 $USDH를 발행하는 1 USD로 거래되도록 설계된 과담보 스테이블코인입니다. 거버넌스 및 유동성 인센티브는 투표 및 게이지를 통해 관리되므로 vePEG 보유자가 PEG 배출을 지시하고 보상을 받고 유동성 분배에 영향을 미칠 수 있습니다.

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

_검토 커밋 해시_ - [00dedbdb52474ae34caa3e2571c4b29f34b14c00](https://github.com/hyperstable/contracts/tree/00dedbdb52474ae34caa3e2571c4b29f34b14c00)

_수정 검토 커밋 해시_ - [cb30a683971974f6205c453a0d172a5431daa935](https://github.com/hyperstable/contracts/tree/cb30a683971974f6205c453a0d172a5431daa935)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `DebtToken`
- `InterestRateStrategyV1`
- `LiquidationBuffer`
- `LiquidationManager`
- `PositionManager`
- `Vault`
- `BribeFactory`
- `EmissionScheduler`
- `ExternalBribe`
- `Gauge`
- `GaugeFactory`
- `InternalBribe`
- `Minter`
- `Peg`
- `PegAirdrop`
- `RewardsDistributor`
- `VeArtProxy`
- `Voter`
- `vePeg`

# 발견 사항 (Findings)

# [H-01] 공격자가 자신의 `vePeg` NFT를 찌를 수 없게 만들 수 있음 (Attacker can make his `vePeg` NFT unpokeable)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`vePeg` NFT 잔액은 시간이 지남에 따라 선형적으로 감소하므로 업데이트되지 않으면 투표가 오래될 수 있습니다. 이것이 시스템에 `poke` 메커니즘을 둔 이유입니다.
이를 통해 관리자는 `vePeg` NFT 투표 가중치를 업데이트하여 현재 잔액을 반영할 수 있습니다. 이는 투표 시스템의 공정성과 정확성을 보장합니다.

사용자는 "더스트 투표(dust vote)" 전략을 악용하여 의도적으로 자신의 `vePeg` NFT를 찌를 수 없게(unpokeable) 만들 수 있습니다. 방법은 다음과 같습니다:

현재 `vePeg` NFT 잔액이 `10e18`이라고 가정합니다.
사용자는 두 개의 풀에 투표합니다: 선호하는 풀에 `10e18 - 1` 가중치를 할당합니다. 임의의 풀에 1 가중치("더스트 투표")를 할당합니다.
1초 후 `vePeg` NFT의 잔액은 10e18보다 약간 작아집니다.
이 시점에서 `vePeg` NFT를 찌르려는 시도는 다음과 같은 이유로 실패합니다: 더스트 투표에 대한 `1 * veWeight / 10e18` 계산은 감소된 가중치로 인해 0으로 내림됩니다.

`require(_poolWeight != 0)` 확인은 트랜잭션을 되돌려 `vePeg` NFT를 효과적으로 찌를 수 없게 만듭니다.

```solidity
File: Voter.sol#_vote()

175:             if (isGauge[_gauge]) {
176:                 uint256 _poolWeight = _weights[i] * _weight / _totalVoteWeight;
177:                 require(votes[_tokenId][_pool] == 0);
178:                 require(_poolWeight != 0);

```

사용자는 의도적으로 자신의 `vePeg` NFT를 찌를 수 없게 만들어 투표 가중치가 현재 잔액을 반영하도록 업데이트되는 것을 방지할 수 있습니다. `vePeg` NFT는 오래된 잔액을 기반으로 계속 투표에 기여하여 투표 시스템을 왜곡하고 잠재적으로 다른 사용자에게 불이익을 줄 수 있습니다.

## 권장 사항

`_vote()` 함수는 0 투표 시 되돌리지 말고 대신 루프를 계속해야 합니다.

# [M-01] 잘못된 `startTime` 확인으로 인해 `EmissionScheduler` 및 `Minter` 계약을 초기화할 수 없음 (`EmissionScheduler` and `Minter` contracts can't be initialized due to an incorrect `startTime` check)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`EmissionScheduler` 계약에서 `initialize()` 함수는 계약을 초기화하기 위한 것이며 `Minter.initialize()` 함수를 통해 호출됩니다. 그러나 함수는 `startTime`이 0이면 잘못되게 되돌려집니다.
먼저 계약을 초기화하려면 `startTime`이 0이어야 하므로; **재초기화를 방지하기 위해 수행된 확인은 계약이 초기화되는 것을 잘못 방지**하며, 결과적으로 `Minter` 계약의 초기화도 차단합니다:

```solidity
    function initialize(uint256 _startTime) external {
        _onlyMinter();
        if (startTime == 0) {
            revert Errors.AlreadyInitialized();
        }
        startTime = _startTime;
    }
```

## 권장 사항

```diff
    function initialize(uint256 _startTime) external {
        _onlyMinter();
-       if (startTime == 0) {
+       if (startTime != 0) {
            revert Errors.AlreadyInitialized();
        }
        startTime = _startTime;
    }
```

# [M-02] 죽은 게이지가 `Voter._vote()` 함수에서 올바르게 처리되지 않음 (Dead gauges are not handled correctly in `Voter._vote()` function)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Voter` 계약에서 `_vote()` 함수는 사용자가 `vote()`를 통해 투표하거나 `poke()`를 사용하여 투표를 업데이트할 때 호출됩니다. 그러나 함수에는 현재 게이지가 살아있는지 여부를 확인하는 체크가 포함되어 있지 않습니다.

이 누락된 확인은 다음을 초래합니다:

1. 보상 갇힘: 함수가 죽은 게이지에 대한 투표를 처리할 때, `_updateFor()` 함수를 통해 죽은 게이지의 누적 보상(지분)은 계약에 **갇히게** 됩니다. 왜냐하면 `updateFor()` 함수에는 게이지 활성 확인이 있어 보상이 죽은 게이지에 할당되지 않기 때문이며, 이는 계약 내에 잠긴 상태로 남는 보상의 축적으로 이어집니다.
2. 뇌물(bribe) 계약에 잘못된 입금.
3. 부풀려진 `totalWeight`: 죽은 게이지를 고려하지 않으면 `totalWeight`가 잘못 증가하고, 이 값은 `notifyRewards()`를 통한 보상 알림과 연결된 `index` 계산에 영향을 미칩니다. 결과적으로 죽은 게이지를 포함하면 `totalWeight`가 부풀려져 다른 게이지에 대한 보상 할당이 감소합니다.

```solidity
function _vote(uint256 _tokenId, address[] memory _poolVote, uint256[] memory _weights) internal {
        _reset(_tokenId);
        //...

        for (uint256 i = 0; i < _poolCnt; i++) {
            _totalVoteWeight += _weights[i];
        }

        for (uint256 i = 0; i < _poolCnt; i++) {
            address _pool = _poolVote[i];
            address _gauge = gauges[_pool];

            if (isGauge[_gauge]) {
                //...
                _updateFor(_gauge);

                //...
                IBribe(internal_bribes[_gauge])._deposit(uint256(_poolWeight), _tokenId);
                IBribe(external_bribes[_gauge])._deposit(uint256(_poolWeight), _tokenId);
                _usedWeight += _poolWeight;
                _totalWeight += _poolWeight;
                emit Voted(msg.sender, _tokenId, _poolWeight);
            }
        }
        if (_usedWeight > 0) IVotingEscrow(_ve).voting(_tokenId);
        totalWeight += uint256(_totalWeight);
        usedWeights[_tokenId] = uint256(_usedWeight);
    }
```

```solidity
function _updateFor(address _gauge) internal {
        //...
        if (_supplied > 0) {
            //...
            if (_delta > 0) {
                uint256 _share = uint256(_supplied) * _delta / 1e18; // add accrued difference for each supplied token
                if (isAlive[_gauge]) {
                    claimable[_gauge] += _share;
                }
            }
        } else {
            supplyIndex[_gauge] = index; // new users are set to the default global state
        }
    }
```

## 권장 사항

게이지가 살아있는지 확인하고 되돌리지 않고 죽은 게이지를 건너뛰어 함수를 계속하도록 `_vote()` 함수를 업데이트하는 것을 고려하십시오.

# [M-03] 사용자 입금이 샌드위치 공격에 취약할 수 있음 (User deposits may be vulnerable to sandwich attacks)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`PositionManager` 계약에서 `deposit()` 함수는 사용자가 지정된 금고의 기본 자산을 입금하고 담보로 잠글 수 있도록 합니다. 그러나 이 함수는 `minAmountOut`(사용자가 받으려는 최소 지분 양)을 도입하지 않습니다.

이 매개변수의 부재는 예금자를 **샌드위치 공격**에 노출시키며, 여기서 악의적인 행위자가 입금 트랜잭션을 프런트런하여 주가를 조작할 수 있으며, 결과적으로 예금자는 예상보다 적은 지분을 받아 유효 담보가 줄어듭니다.

사용자가 받을 수 있는 상환 자산의 최소 금액이 없는 `withdraw()` 함수에서도 동일한 문제가 발생합니다.

## 권장 사항

사용자가 입금(및 인출)에 대해 최소한의 지분만 수락할 의향이 있음을 보장하기 위해 `minAmountOut` 매개변수를 도입하고, 최종 지분 금액이 `minAmountOut`보다 작으면 트랜잭션을 되돌려야 합니다.

# [M-04] MCR 기반의 고정 청산인 보상은 프로토콜 손실을 초래할 수 있음 (Fixed liquidator reward based on MCR may cause protocol losses)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

청산 메커니즘은 금고의 MCR 값(`@1>`)을 기준으로 청산인 보상을 계산하므로 포지션이 여전히 지급 능력이 있더라도(담보가 부채를 커버할 수 있음) 불필요한 프로토콜 손실을 초래할 수 있습니다.

이 문제는 보상 계산 과정에서 청산 중에도 포지션이 MCR 수준의 담보를 유지해야 한다고 가정하여 과도한 보상을 생성하여 프로토콜 버퍼를 고갈시키거나 채무자에게 손실을 분배할 수 있기 때문에 발생합니다.

```solidity
function _getLiquidationValues(
    ...
) internal view returns (LiquidationValues memory) {
    --- SNIPPED ---

    uint256 equivalentCollateral = values.debtToRepay.divWad(values.sharePrice);
@1>  uint256 overcollateralization = equivalentCollateral.mulWad(_vaultData.MCR - 1e18);
@1>  uint256 requiredCollateral = equivalentCollateral + overcollateralization.mulWad(liquidatorRewardBps);
    uint256 liquidatedCollateral =
        _positionData.collateralSnapshot.mulDiv(values.debtToRepay, _positionData.debtSnapshot);

    if (liquidatedCollateral < requiredCollateral) {
        // the amount of shares that are needed to cover the required collateral
@2>     values.missingShares = requiredCollateral - liquidatedCollateral;
        // the shares that are going to be redeemed
        values.sharesToRedeem = liquidatedCollateral;
    } else {
    --- SNIPPED ---
}
```

현재 공식은 보상을 다음과 같이 계산합니다: `Debt_Equivalent * (MCR-100)/100 * liquidatorRewardBps (fixed 25%)`

이것은 다음을 의미합니다:

- MCR = 120%인 경우: 청산인은 부채 가치의 5%를 받습니다 (20% \* 25%)
- MCR = 150%인 경우: 청산인은 부채 가치의 12.5%를 받습니다 (50% \* 25%)
- MCR = 200%인 경우: 청산인은 부채 가치의 25%를 받습니다 (100% \* 25%)

그러나 `collateral > debt`이지만 `< debt * (1 + reward rate)`인 포지션은 다음과 같이 계산되어 불필요한 손실을 발생시킵니다: 0. 이자율 0, MCR = 120%라고 가정합니다.

1. 담보 가치는 `1200 USDH`이고 부채는 `1000 USDH`입니다.

2. 플래시 크래시 시장에서 가격이 하락하고 담보 가치가 `1020 USDH`가 됩니다(청산 가능 상태).

3. 포지션을 청산할 때 청산인은 다음을 지불합니다:
   : `1000 USDH` (부채)
   : 그리고 (`1000 * 20%) * 25% = 50 USDH`를 얻습니다,
   청산인에게 지불하기 위해 `1050 USDH`에 해당하는 담보가 필요합니다.

4. 담보 가치가 이제 `1020 USDH`에 불과하므로 `missingShares`(`@2>`)를 가져가는 경우에 해당합니다. 이 상태에서 담보는 여전히 부채(`1000 USDH`)를 커버할 수 있지만 필요한 보상(`50 USDH`)은 커버할 수 없습니다.

5. 따라서 프로세스는 (`@3>`) **`liquidationBuffer`**(일반적으로 정상적인 청산 포지션 수수료의 자금을 보유함)에서 `30 USDH`에 해당하는 담보를 추가로 가져가거나, **금고의 모든 담보 지분 보유자에게 손실을 분배**하거나, 더 나아가 **청산 불가능**하게 됩니다.

6. 이 메커니즘은 부분 청산 중에 포지션의 건전성을 감소시키는 것을 관찰할 수 있습니다. 또한 MCR이 높으면 이 시나리오의 임계값도 증가합니다.

```solidity
function _liquidate(...)
    internal
    returns (LiquidationValues memory)
{
    --- SNIPPED ---

    // take from buffer
@3>  if (missingShares > 0) {
        ILiquidationBuffer buffer = liquidationBuffer;
        --- SNIPPED ---
        rewards += buffer.redeem(vaultData.addr, _liquidator, toRedeem);
        }
    }

    // take from vault
@3>  if (missingShares > 0) {
            --- SNIPPED ---
            IVault(vaultData.addr).take(_liquidator, assetsToTake);

            rewards += assetsToTake;
            missingShares -= sharesToTake;
        }
    }

@3>  if (missingShares > 0) {
        revert NotEnoughRewards();
    }

    --- SNIPPED ---
}
```

## 권장 사항

언급된 경우에 대해 포지션의 CR은 `100% < CR < ((MR-100%) * 25%)` (지급 능력 있지만 청산 가능) 정도일 것입니다. 이 경우 프로세스는 인센티브를 사용 가능한 담보의 양으로 제한해야 합니다.

# [M-05] 낮은 건강 요소 시 손실 감소를 돕는 자가 청산 (Self-liquidation can help reduce losses when health factor is low)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

사용자의 담보가 청산 보상을 커버하기에 불충분한 경우, 청산인에게 지불하기 위해 LiquidationBuffer에서 토큰이 인출됩니다. LiquidationBuffer에도 청산 보상을 커버할 충분한 토큰이 없으면 Vault에서 토큰을 인출하여 청산인에게 지불합니다.

사용자의 담보가 청산 보상을 지불하기에 불충분한 경우, 사용자는 자가 청산을 선택할 수 있으며, 이를 통해 일반 상환 방식에 비해 프로토콜이 손실의 일부를 흡수하도록 할 수 있습니다.

예를 들어, 사용자 담보의 현재 가치가 100이고 전체 부채 청산에 110(청산인 보상 포함) 상당의 담보가 필요한 경우, 사용자는 자가 청산을 선택하여 담보 가치 하락으로 인한 손실을 줄일 수 있습니다.

## 권장 사항

자가 청산을 방지하거나 재배포 상태에서 부채 청산을 특권 주소로만 제한하는 것이 좋습니다.

# [M-06] 위임 제거에 대한 DOS (DOS for removal of delegates)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`vePeg.sol`의 위임 시스템은 `MAX_DELEGATES`가 하드코딩된 값이라는 특징을 가지고 있습니다.

반면, `_create_lock()`에는 `require(_value > 0);` 확인만 있기 때문에 사용자는 1 wei LP만으로 잠금을 생성할 수 있습니다.

다음 시나리오를 고려해 보십시오:

Alice는 vePeg_NFT_01(큰 가중치 포함)을 소유하고 있으며 이를 Bob에게 위임하기로 결정합니다.

악의적인 사용자가 Alice의 트랜잭션을 백런(back-run)하고 Alice에게 `MAX_DELEGATES` vePeg_NFT(단 1 wei LP)를 위임합니다.

일정 기간 후 Alice는 Bob에게서 vePeg_NFT_01의 위임자를 이동하려고 시도하지만 이 요구 사항으로 인해 실패합니다.

```solidity

File: vePeg.sol#_moveAllDelegates

                require(dstRepOld.length + ownerTokenCount <= MAX_DELEGATES, “dstRep would have too many tokenIds”);

```

사용자는 52 에포크 동안 위임자에서 자신의 vePeg_NFT를 제거할 수 없습니다.

누구나 이 공격으로 사용자가 어떤 위임도 받지 못하게 할 수 있습니다.

## 권장 사항

```diff

File: VotingEscrow.sol

                require(

-                      dstRepOld.length + ownerTokenCount <= MAX_DELEGATES,

+                      dstRepOld.length <= MAX_DELEGATES,

                     “dstRep would have too many tokenIds”

                 );

```

# [M-07] `checkpoint_total_supply()`에서 `ve_supply`의 잘못된 업데이트 (Incorrect update of `ve_supply` in `checkpoint_total_supply()`)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`RewardsDistributor.sol` 계약에서 `checkpoint_total_supply()` 함수는 주어진 시간 `t`의 총 공급량을 `ve_supply[t]` 매핑에 저장하는 역할을 합니다. 이 값은 향후 보상 분배 계산에 사용됩니다. 관련 코드는 다음과 같습니다:

```solidity

File: RewardsDistributor.sol
158:                 ve_supply[t] = FixedPointMathLib.max(uint256(int256(pt.bias - pt.slope * dt)), 0);
```

`ve_supply[t]` 값은 시간 `t`에 해당하는 주가 끝났을 때, 즉 `t + 1 weeks <= block.timestamp`일 때만 업데이트되어야 합니다. 그러나 현재 구현은 `block.timestamp % 1` weeks가 0일 때 `ve_supply[t]`가 잘못 업데이트되도록 허용합니다.

이로 인해 `ve_supply[t]`에서 새로 생성된 `vePeg` NFT(`checkpoint_total_supply()`가 호출된 직후 생성됨)의 잔액이 고려되지 않는 취약점이 생성됩니다.
악의적인 사용자는 이 결함을 악용하여 보상 계산을 조작하고 향후 분배 보상을 훔칠 수 있습니다.

이는 보상 조작으로 이어집니다. 악의적인 행위자는 특정 시간에 `vePeg` NFT를 생성하여 `ve_supply[t]`에서 잔액을 제외시켜 잘못된 보상 분배를 유도할 수 있습니다.

## 권장 사항

이 문제를 완화하려면 시간 `t`에 해당하는 주가 완전히 경과한 후에만 `ve_supply[t]`가 업데이트되도록 하십시오. 이는 다음과 같이 달성할 수 있습니다:

```diff
File: RewardsDistributor.sol

         for (uint256 i = 0; i < 20; i++) {
-             if (t > rounded_timestamp) {
+             if (t >= rounded_timestamp)
```

# [M-08] 최소 예치 금액 부족으로 인한 악성 부채 발생 가능성 (Lack of minimum deposited amount can result in bad debt)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`PositionManager` 계약에서 `deposit()` 함수는 사용자가 최소 예치 규모 없이 지원되는 모든 금고에 자산을 예치할 수 있도록 허용합니다. 이를 통해 "더스트 예금(dust deposits)"이라고 하는 매우 작은 예금 포지션을 생성할 수 있으며, 이 작은 예금은 담보로 사용하여 대출을 받을 수 있습니다.

그러나 이러한 작은 포지션의 **건강 요소(health factor)**가 **최소 담보 비율(MCR)** 임계값 아래로 떨어지면 담보의 낮은 가치로 인해 **청산인이 이를 청산할 인센티브가 없습니다**. 결과적으로 이러한 더스트 예금 포지션은 열린 상태로 유지되어 프로토콜에 **악성 부채**가 축적됩니다.

## 권장 사항

사용자가 매우 적은 양의 자산으로 포지션을 개설할 수 없도록 담보에 대한 최소 예치 규모를 도입하십시오.

# [M-09] 잘못된 소수점 처리 (Incorrect decimal handling)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`PositionManager` 및 `LiquidationManager` 계약은 다양한 프로세스에서 계산할 때 소수점을 잘못 처리합니다. 문제는 금고 지분이 `Vault.decimals()`를 통해 기본 토큰의 소수점을 상속하지만 계산을 수행할 때 모든 값이 18 소수점이라고 가정하는 데서 비롯됩니다.

따라서 소수점이 18이 아닌 금고 지원 자산이나 소수점 오프셋 > 0인 경우, 현재 프로세스는 `colalteralSnapshots`가 금고 소수점(`@1>`)으로 저장됨에도 불구하고 지분 금액을 18 소수점으로 취급합니다.

```solidity

//File: src/core/PositionManager.sol

function deposit(uint8 _index, uint256 _amountToDeposit) external {

    --- SNIPPED ---

    asset.transferFrom(msg.sender, address(this), _amountToDeposit);

@1>  uint256 shares = vault.deposit(_amountToDeposit, address(this));

    _accruePositionDebt(_index, vaultData, positionData);

@1>  vaultData.collateralSnapshot += shares;

@1>  positionData.collateralSnapshot += shares;

    emit Deposit(vaultData.addr, msg.sender, _amountToDeposit);

}

```

이로 인해 다음과 같은 문제가 발생합니다:

1. `PositionManager` 및 `LiquidationManager` 계약 전반에서 포지션에 대한 `CR`을 계산하는 값이 잘못됨:

- `PositionManager._checkCR()`

- `PositionManager.accountCr()`

- `LiquidationManager._getLiquidationValues()`

- `LiquidationManager._calculateCr()`

이로 인해 `CR` 확인을 우회하거나(> 18 소수점의 경우) `CR`이 항상 `MCR`보다 작은 것으로 표시되어(< 18 소수점의 경우) 포지션이 항상 청산 가능한 것으로 표시될 수 있습니다.

2. `LiquidationManager._getLiquidationValues()`에서 `liquidatedCollateral`(`_positionData.collateralSnapshot`)과 비교하고 처리할 때 항상 18 소수점으로 표시되므로 `requiredCollateral`을 계산하는 값이 잘못됩니다.

문서에 따르면 `8` 소수점 값을 가진 `WBTC` 자산이 잠재적으로 사용될 예정입니다.

## 권장 사항

계산, 비교 및 검증 프로세스 모두에서 해당 값을 처리할 때 18 소수점이 아닌 금고에 대해 `PositionManager` 및 `LiquidationManager` 계약에서 소수점을 올바르게 정규화하십시오.
# [L-01] `RewardsDistributor` : 잠금 만료 시간이 시간과 같을 때 보상 청구 실패 (`RewardsDistributor` : reward claim fails when time equals lock expiration time)

`RewardsDistributor` 계약에서 `claim()` 및 `claim_many()` 함수는 잠금 소유자에게 보상을 분배합니다. 잠금이 만료된 경우 보상은 잠금 소유자에게 직접 전송됩니다. 잠금이 만료되지 않은 경우 보상은 `ve.deposit_for()`를 통해 소유자를 위해 투표 에스크로에 예치됩니다.

```solidity
function claim(uint256 _tokenId) external returns (uint256) {
        //...
        if (amount != 0) {
            IVotingEscrow ve = IVotingEscrow(veAddress);
            if (block.timestamp > ve.locked(_tokenId).end) {
                address owner = ve.ownerOf(_tokenId);
                token.safeTransfer(owner, amount);
            } else {
                ve.deposit_for(_tokenId, amount);
            }
            token_last_balance -= amount;
        }
        //...
    }
```

그러나 투표 에스크로 계약(`vePeg`)의 로직에 따르면 `locked.end <= block.timestamp`이면 잠금은 만료된 것으로 간주됩니다. 이는 `block.timestamp == locked.end`일 때 문제가 발생하는데, `ve.deposit_for()` 함수가 보상을 예치하려고 시도하지만 바로 그 순간 잠금이 만료된 것으로 간주되어 실패하기 때문입니다.
이로 인해 현재 시간이 잠금 종료 시간과 정확히 같을 때 사용자가 `claim()` 또는 `claim_many()` 함수를 호출할 수 없게 됩니다.

```solidity
// vePeg contract:
    function deposit_for(uint256 _tokenId, uint256 _value) external nonreentrant {
        //...
        require(_locked.end > block.timestamp, "Cannot add to expired lock. Withdraw");
        //...
    }
```

권장 사항: 잠금 종료 시간이 현재 타임스탬프와 같은 경우를 처리하도록 고려하십시오.

# [L-02] `Voter.killGauge()` 함수에서 토큰 승인 취소 누락 (Missing revocation of token approval in `Voter.killGauge()` function)

`Voter` 계약에서 `killGauge()` 함수는 `emergencyCouncil`에 의해 호출될 때 게이지를 제거하는 역할을 합니다. 함수는 게이지의 `isAlive[_gauge]` 상태를 `false`로 설정하고 청구 가능한 보상을 0으로 재설정하지만, `createGauge()` 함수를 통해 게이지가 등록될 때 게이지에 부여된 **토큰 승인을 취소하지 않습니다.**
따라서 제거된 게이지가 손상되거나 악의적으로 제어되는 경우, 승인 취소 부족으로 인해 여전히 보유하고 있는 권한을 오용하여 `Voter` 계약 자금을 고갈시킬 수 있습니다.

권장 사항: 생성 중에 게이지에 부여된 토큰 승인을 취소하도록 `killGauge()` 함수를 수정하십시오.

# [L-03] `bufferSurplus > 0`일 때 `Liquidation` 이벤트 방출 누락 (Missing `Liquidation` event emission when `bufferSurplus > 0`)

`LiquidationManager` 계약에서 `liquidate()` 함수는 `bufferSurplus`가 0보다 클 때 잉여 담보가 있으면 이벤트가 방출되기 전에 함수가 반환되므로 `Liquidation` 이벤트를 방출하지 않습니다.

```solidity
  function _liquidate(uint8 _index, address _positionOwner, address _liquidator, uint256 _debtToRepay)
        internal
        returns (LiquidationValues memory)
    {
        //...

        if (values.bufferSurplus > 0) {
            ILiquidationBuffer buffer = liquidationBuffer;
            IVault(vaultData.addr).transferFrom(address(manager), address(buffer), values.bufferSurplus);
            buffer.notifyDeposit(vaultData.addr, values.bufferSurplus);

            assert(values.missingShares == 0);

            // if there is a buffer surplus then there are no missing shares
            return values;
        }

        //...
    }
```

권장 사항: `bufferSurplus`가 0보다 클 때 `Liquidation` 이벤트가 방출되도록 보장하십시오.

# [L-04] 거버넌스 계약에서 잠재적으로 오래되고 보안 문제가 발행된 코드 (Potential outdated and security-issued code in governance contracts)

거버넌스 계약에 대한 코드의 차등 검토 중, 최근 수정에도 불구하고 **일부 코드 경로가 오래된 것으로 보입니다.**

또한 **코드베이스의 일부 부분이 이전에 감사되었으며 과거 검토에서 보안 문제로 플래그가 지정되었음**을 확인했습니다.

거버넌스 모듈의 무결성과 보안을 보장하기 위해 팀이 기존 코드를 과거 보안 보고서와 교차 확인하는 것이 좋습니다. 특히:

- https://github.com/spearbit/portfolio/blob/master/pdfs/Velodrome-Spearbit-Security-Review.pdf
- 해결되지 않은 취약점을 해결하기 위해 필요한 보안 수정 사항을 평가하고 적용하십시오.

# [L-05] 소유권 관리 부족으로 인한 관리 제어의 영구적 손실 (Lack of ownership management leads to permanent loss of administrative control)

`Peg` 계약에서 `minter` 역할이 `Minter` 계약으로 이전되면 `setMerkleClaim` 주소를 다시 업데이트할 방법이 없습니다.

이 문제는 다음과 같은 사실에서 비롯됩니다:

1. `minter` 역할은 생성자에서 초기에 `msg.sender`로 설정됩니다.
2. 계약은 현재 채굴자만 호출할 수 있는 `setMinter()` 함수를 제공합니다.
3. `Minter` 계약으로 이전되면 `Minter` 계약에는 다른 관리 기능을 제어하는 기능이 없습니다: `setMerkleClaim()`

이로 인해 관리 제어가 영구적으로 잠겨 필요한 경우 새 머클 청구 주소를 설정하는 기능을 포함하여 계약 매개변수에 대한 향후 업데이트가 방지됩니다.

`Peg` 계약에 `Minter`와 별도로 다른 소유권 관리 기능을 도입하는 것을 고려하십시오.

# [L-06] `stHYPE`와의 잠재적 비호환성으로 인한 잘못된 자산 잔액 계산 (Potential incompatibility with `stHYPE` leads to incorrect asset balance calculation)

`Vault` 계약은 `totalAssets()`를 결정하기 위해 `balanceOf()`를 사용하는 OpenZeppelin의 `ERC4626` 구현을 상속합니다.

그러나 `stHYPE` 토큰의 경우 통합 가이드를 위한 현재 `sthype-docs`에 따라 `balanceOf()` 대신 `assetsOf()` 함수를 통해 스테이킹 보상을 포함한 실제 잔액을 얻어야 합니다.

이러한 비호환성은 금고가 스테이킹 보상을 계산하지 않아 `stHYPE` 담보를 과소평가하게 되며 이는 다음으로 이어집니다:

1. 포지션 건강 확인에서 잘못된 담보 평가
2. 입금/인출에 대한 잘못된 주가
3. 부정확한 청산 임계값

`stHYPE` 자산은 프로토콜 문서에 따라 잠재적으로 사용될 예정이지만 stHYPE 구현 코드는 아직 확인되지 않았습니다.

# [L-07] `InterestRateStrategyV1`은 구현의 무단 초기화를 허용함 (`InterestRateStrategyV1` allows unauthorized initialization of implementation)

`InterestRateStrategyV1` 계약은 초기화를 비활성화하지 않습니다. 이는 구현의 소유권을 가져갈 수 있게 합니다.

```solidity
contract InterestRateStrategyV1 is OwnableUpgradeable, UUPSUpgradeable, IInterestRateStrategy {
    --- SNIPPED ---
    function initialize(address _owner, address _newPositionManagerAddress, address _usdhPriceFeed)
        external
        initializer
    {
        _transferOwnership(_owner);
        positionManagerAddress = _newPositionManagerAddress;
        PegIRM.setPriceFeed(_usdhPriceFeed);
    }
    --- SNIPPED ---
}
```

구현에 대한 초기화 프로그램을 비활성화하기 위해 생성자를 추가하는 것을 고려하십시오.

```solidity
/// @custom:oz-upgrades-unsafe-allow constructor
constructor() {
    _disableInitializers();
}
```

# [L-08] 금고 등록 확인 누락으로 중복 금고 등록 가능 (Missing vault registration check enables duplicate vault registrations)

`PositionManager.registerVault()` 함수는 `vaults` 매핑에 추가하기 전에 금고가 이미 등록되었는지 확인하지 않습니다. 이를 통해 동일한 금고 주소가 잠재적으로 다른 MCR 값 및 이자 지수와 함께 여러 번 등록될 수 있습니다.

동일한 금고를 여러 번 추가하지 않도록 확인을 적용하는 것을 고려하십시오.

# [L-09] 청산 보상이 문서와 일치하지 않음 (The liquidation reward does not match the documentation)

문서에는 청산인이 청산된 자산 가치의 105%에 해당하는 담보 보상을 받는다고 명시되어 있습니다. 그러나 계약에서 청산 보상은 105%로 고정되지 않고 대신 금고의 MCR 및 `liquidatorRewardBps`에 따라 달라집니다.

```solidity
        values.debtToRepay = FixedPointMathLib.min(_debtToRepay, _positionData.debtSnapshot);
        uint256 equivalentCollateral = values.debtToRepay.divWad(values.sharePrice);
        uint256 overcollateralization = equivalentCollateral.mulWad(_vaultData.MCR - 1e18);
        uint256 requiredCollateral = equivalentCollateral + overcollateralization.mulWad(liquidatorRewardBps);
```

문서를 업데이트하거나 계약 로직을 조정하여 둘 간의 일관성을 보장하는 것이 좋습니다.

# [L-10] `registerVault`의 `_registerData`에서 MCR 확인 (Check MCR in `_registerData` in `registerVault`)

`registerVault` 작업은 두 개의 MCR 값을 받습니다. 하나는 변수로 명시적으로 전달되어 전역 `VaultData`에 기록되고, 다른 하나는 `_registerData`에 인코딩되어 초기 `interestRate`를 계산하는 데 사용됩니다. 이 두 MCR 값은 동일해야 합니다. 그렇지 않으면 초기 `interestRate` 계산이 잘못될 수 있습니다.

```diff
    function registerVault(address _vaultAddress, uint256 _mcr, bytes memory _registerData)
        external
        onlyOwner
        returns (uint8)
    {
        uint8 index = lastVaultIndex;
+       (uint256 mcr, ) = abi.decode(_registerData, (uint256, uint256));
+       require(mcr == _mcr, "mcr misMatch");
```

# [L-11] MERGE_TYPE 예금에 대해 `_deposit_for()`에서 잘못된 공급 증가 (Incorrect supply increase in `_deposit_for()` for MERGE_TYPE deposits)

`_deposit_for()` 함수에서 예금 유형이 MERGE_TYPE인 경우 공급이 잘못 증가합니다. 이는 예금 유형에 관계없이 공급이 무조건 업데이트되기 때문에 발생합니다. 그러나 `merge()` 함수에 의해 트리거되는 MERGE_TYPE 예금의 경우 두 포지션을 병합해도 시스템에 새로운 가치가 도입되지 않으므로 공급이 증가해서는 안 됩니다.

이 문제를 해결하려면 예금 유형이 MERGE_TYPE가 아닐 때만 `supply`를 증가시켜야 합니다.

```solidity
// Only increase supply if the deposit type is not MERGE_TYPE
    if (deposit_type != DepositType.MERGE_TYPE) {
        supply = supply_before + _value;
    }
```

# [L-12] `killgauge()` 함수에서 청구 가능한 보상 전송 누락 (Missing claimable reward transfer in `killgauge()` function)

새로운 `killGauge()` 함수는 게이지와 관련된 청구 가능한 보상을 채굴자에게 반환하지 못합니다. 구체적으로 남아 있는 `claimable[_gauge]` 보상을 채굴자에게 전송하고 청구 가능한 값을 재설정하는 로직이 누락되었습니다. 이 누락으로 인해 청구되지 않은 보상이 계약에 영구적으로 잠길 수 있습니다.

# [L-13] 재설정 후 일시적인 투표 중단 (Temporary voting disruption after reset)

사용자가 에포크 N 동안 `Voter.sol#reset()` 함수를 호출하면 동일한 에포크(에포크 N) 내에서 다시 투표할 수 없습니다. 이는 lastVoted[_tokenId] 타임스탬프가 현재 블록 타임스탬프로 업데이트되어 다음 에포크(에포크 N+1)까지 사용자가 효과적으로 투표할 수 없게 되기 때문입니다.

따라서 현재 에포크에서 투표를 재설정한 사용자는 다음 에포크가 시작될 때까지 투표에 참여할 수 없으며 이로 인해 보상을 잃게 됩니다.

이 문제를 해결하려면 에포크 N에서 `reset()`을 호출한 사용자가 동일한 에포크 내에서 여전히 투표할 수 있도록 로직을 수정하는 것을 고려하십시오.

# [L-14] 낮은 USDH 유통량으로 인한 초기 부채 상환 문제 (Initial debt repayment issue due to low USDH circulating supply)

`PositionManager.sol` 계약이 배포될 때 부채를 지는 첫 번째 사용자는 부채를 완전히 상환할 수 없는 문제에 직면할 수 있습니다. 이는 USDH의 유통량이 초기에 너무 낮아 사용자가 부채를 완전히 상환하기에 충분한 USDH를 확보할 수 없기 때문에 발생합니다. 결과적으로 이러한 사용자는 `PositionManager.sol` 계약에 일부 담보를 잠가야 합니다.
이로 인해 다음이 발생합니다:
USDH 유통량 부족으로 인해 초기 사용자가 부채를 완전히 상환할 수 없습니다.
사용자는 담보의 100%를 인출할 수 없게 되어 시스템의 잠재적인 불만과 비효율성을 초래합니다.

이 문제를 해결하려면 시장에서 청구된 이자를 판매하는 메커니즘을 구현하는 것을 고려하십시오. 이를 통해 부채가 있는 사용자는 부채를 완전히 상환하고 담보를 인출하는 데 필요한 USDH를 구매할 수 있습니다.

# [L-15] `Voter.totalWeight`가 0이면 `Minter.updatePeriod` 실패 (`Minter.updatePeriod` fails if `Voter.totalWeight` is zero)

`Minter` 계약에서 `updatePeriod()` 함수는 `RewardDistributor` 계약, `team` 및 `Voter` 계약에 보상을 분배하는 역할을 합니다.
함수는 보상을 `Voter` 계약에 보낸 후 `VOTER.notifyRewardAmount()`를 호출하지만, `Voter` 계약의 `totalWeight`가 0인 경우(사용자가 투표 및 재설정할 때 `totalWeight`가 변경됨을 알고 있음), 0으로 나누기가 발생하여 `updatePeriod()` 함수가 실패합니다:

```solidity
function updatePeriod() external returns (uint256) {

        //...
        if (toVoter > 0) {
            PEG.approve(address(VOTER), toVoter);
            VOTER.notifyRewardAmount(toVoter);
        }

        if (toLockers > 0) {
            address(PEG).safeTransfer(address(REWARDS_DISTRIBUTOR), toLockers);
            REWARDS_DISTRIBUTOR.checkpoint_token();
            REWARDS_DISTRIBUTOR.checkpoint_total_supply();
        }

        if (toTeam > 0) {
            address(PEG).safeTransfer(team, toTeam);
        }

      //...
    }
```

```solidity
// Voter contract:
  function notifyRewardAmount(uint256 amount) external {
        _safeTransferFrom(base, msg.sender, address(this), amount); // transfer the distro in

        // @audit : revert due div by zero
        uint256 _ratio = amount * 1e18 / totalWeight; // 1e18 adjustment is removed during claim
        if (_ratio > 0) {
            index += _ratio;
        }
        emit NotifyReward(msg.sender, base, amount);
    }
```

이로 인해 `Voter` 계약의 `totalWeight`가 0보다 커질 때까지 `RewardDistributor` 계약과 `team` 주소 모두에 보상이 분배되지 않습니다.

권장 사항:

1. `totalWeight`가 0일 때 되돌리는 것을 방지하도록 `VOTER.notifyRewardAmount()` 함수를 업데이트하십시오.
2. `totalWeight`가 0이더라도 보상이 `RewardDistributor` 및 `team`에 계속 분배되도록(그리고 `toVoter`를 환불하도록) `updatePeriod()` 함수를 수정하십시오.

# [L-16] 전송 누락으로 인해 죽은 게이지에 대한 `Voter` 보상이 갇힘 (`Voter` rewards for dead gauges become stuck due to missing transfer)

`Voter` 계약에서 `_updateFor()` 함수는 `claimable` 매핑에 기록된 투표 가중치를 기반으로 게이지의 보상을 업데이트하는 역할을 합니다. 함수는 보상을 할당하기 전에 게이지가 **살아있는지(alive)** 확인하여 활성 게이지 만이 자격 있는 보상을 받도록 합니다.

그러나 게이지가 죽었을 때 중대한 문제가 발생합니다. `index`는 `notifyRewardAmount()` 함수에서 업데이트되고 활성 및 죽은 게이지의 가중치를 모두 고려한 총 투표 가중치(`totalWeight`)에 따라 증가하지만, 죽은 게이지에 대한 보상은 `_updateFor()` 함수에서 적절하게 처리되지 않습니다.

따라서 게이지가 죽으면 계산된 보상(`shares`)은 게이지에 할당되지 않으며, 함수는 보상을 채굴자(또는 다른 승인된 주소)에게 다시 전송하지 않으므로, 결과적으로 죽은 게이지에 대한 이러한 자격 있는 보상은 분배되거나 전송되지 않고 **계약에 갇히게** 됩니다.

```solidity
function _updateFor(address _gauge) internal {
        //...
        if (_supplied > 0) {
            //...
            if (_delta > 0) {
                uint256 _share = uint256(_supplied) * _delta / 1e18; // add accrued difference for each supplied token
                if (isAlive[_gauge]) {
                    claimable[_gauge] += _share;
                }
            }
        } else {
            supplyIndex[_gauge] = index; // new users are set to the default global state
        }
    }
```

`_updateFor()` 함수는 `_reset()`, `vote()`, `updateFor()` 및 `updateForRanges()` 함수 모두에서 호출되므로 죽은 게이지는 여전히 이러한 함수에서 처리되지만 보상은 다른 승인된 주소로 전송되지 않습니다.

게이지가 죽었을 때 계산된 보상이 채굴자 또는 다른 승인된 주소로 전송되도록 `_updateFor()` 함수를 업데이트하십시오.

# [L-17] `liquidate()`에 `minAmountCollateral` 누락 (Missing `minAmountCollateral` in `liquidate()`)

`LiquidationManager` 계약에서 `liquidate()` 함수에는 `minAmountCollateral` 매개변수가 없어 담보 가격이 조작되거나 급격히 급등할 경우 청산인이 잠재적 손실에 노출될 수 있으며, 청산인은 상환한 자산에 대해 더 적은 담보를 받을 수 있어 청산인이 프로토콜에 참여하고 건전하지 않은 포지션을 청산하는 것을 덜 매력적으로 만듭니다.

청산인이 받을 것으로 예상하는 최소 담보 금액을 지정할 수 있도록 `minAmountCollateral` 매개변수를 포함하도록 `liquidate()` 함수를 업데이트하십시오.

# [L-18] 첫 번째 예금자에 의한 인플레이션 공격에 취약한 금고 (Vault is susceptible to inflation attack by first depositor)

`Vault` 계약은 첫 번째 사용자가 지분 평가를 조작하여 불균형한 양의 자산을 상환할 수 있는 인플레이션 공격에 취약합니다.

초기 지분 금액(죽은 지분 발행)을 죽은 주소(`address(0)`)로 발행하는 것을 고려하십시오:

```diff
    constructor(address _asset, address _priceFeed, uint256 _mcr) ERC4626(IERC20(_asset)) ERC20("", "") {
        _initializeOwner(msg.sender);
        priceFeed = IPriceFeed(_priceFeed);
        MCR = _mcr;
+      _mint(address(0),1e3);
    }
```

# [L-19] `Vault.sharePrice`는 모든 금고에 대해 18 소수를 잘못 가정함 (`Vault.sharePrice` incorrectly assumes 18 decimals for every vault)

`Vault.sharePrice()` 함수는 하드코딩된 `1e18` 값을 사용하여 주가를 계산할 때 18 소수를 잘못 가정합니다. 이 가정은 기본 자산에 다른 소수점(예: 8 소수가 있는 `WBTC` 또는 `_decimalsOffset()` > 0을 도입하는 금고)이 있을 때 깨져서 주가가 과대 평가되고 시스템 전체의 담보 평가에 영향을 미치는 잠재적으로 잘못된 주가 계산으로 이어집니다.

```solidity
//File: src/core/Vault.sol

function sharePrice() external view returns (uint256) {
    return (convertToAssets(1e18) * assetPrice()) / 1e18;
}
```

```solidity
//File: src/core/Vault.sol -> ERC4626.sol

function decimals() public view virtual override(IERC20Metadata, ERC20) returns (uint8) {
    return _underlyingDecimals + _decimalsOffset();
}
```

예를 들어 지분 금고가 자산과 1:1이 아닌 시나리오(예: 일부 인센티브 기부가 있음)에서 프로세스는 계산을 위해 1e18 지분을 가져가는데 이는 WBTC 금고의 경우 1e10 곱한 지분으로 볼 수 있으며 비율이 1:1에 비례하지 않으므로 잠재적으로 잘못된 정밀도가 있습니다.

그러나 다른 계산 프로세스와 결합하면 최소 정밀도는 수학적으로 잠재적으로 상쇄되지만 `sharePrice()` 자체는 여전히 과도한 가격 값을 나타냅니다.

```bash
(convertToAssets(1e18) * assetPrice()) / 1e18
vault8 share price: 92344394845405751500000

decimals: 8 + 0 = 8
(convertToAssets(1e8) * assetPrice()) / 1e8 (maintain the returned 18 decimals)
vault8 share price after: 92344393931150300000000
```

지분 소수를 기준으로 지분 금액을 조정하십시오. 이 접근 방식은 `assetPrice()`가 18 소수로 반환된다고 가정합니다:

```diff
function sharePrice() external view returns (uint256) {
-   return (convertToAssets(1e18) * assetPrice()) / 1e18;
+   uint256 oneShare = 10 ** decimals();
+   return (convertToAssets(oneShare) * assetPrice()) / oneShare;
}
```

# [L-20] `missingShares`로 인해 더 많은 부분 청산 가능 (The `missingShares` could lead to liquidate more portions)

`LiquidationManager.sol` 계약의 `liquidate()` 함수에는 필요한 담보를 커버하는 데 필요한 지분 수인 `missingShares` 개념이 있습니다.
프로토콜은 누락된 지분을 커버하기 위해 `LiquidationBuffer.sol`을 구축합니다. 그러나 누락된 자금을 모두 제공할 수 없는 경우 이 로직이 실행됩니다:

```solidity
 // take from vault
        if (missingShares > 0) {
            uint256 totalShares = IVault(vaultData.addr).totalSupply();
            if (totalShares > 0) {
                uint256 sharesToTake = totalShares > missingShares ? missingShares : totalShares;
                uint256 assetsToTake = IVault(vaultData.addr).convertToAssets(sharesToTake);
                IVault(vaultData.addr).take(_liquidator, assetsToTake);
```

이처럼 금고에서 자산을 가져가면 `Vault.sol#sharePrice()`의 가격 계산에 직접적인 영향을 미치기 때문에 다른 사용자의 담보에 영향을 미치며, 이는 그들 또한 청산되도록 할 수 있습니다.

버퍼 계약을 공급할 수 있는 재무부를 갖는 것이 더 낫습니다.
