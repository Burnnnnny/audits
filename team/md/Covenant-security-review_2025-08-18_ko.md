# Pashov Audit Group 정보 (About Pashov Audit Group)

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원들로 구성되어 있으며, 이들은 이 분야에서 입증된 실력을 갖추고 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 획득했거나, 여러 번 우승했거나, 우리와 함께 감사에서 탁월한 성과를 거두었습니다. 우리는 증명되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도운 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Covenant-labs/covenant-core** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, **ast3ros, Tejas Warambhe, Said, Ch_301**이 **Covenant** 검토에 참여했습니다. 총 **25**개의 이슈가 발견되었습니다.

# Covenant 소개 (About Covenant)

Covenant는 기본 자산을 수익 코인(yield-coins)과 레버리지 코인(leverage-coins)이라는 두 가지 대체 가능하고 거래 가능한 구성 요소로 분할하여 위험을 변환하는 플랫폼입니다. 수익 코인은 이자를 축적하고 더 안전한 노출을 제공하며, 레버리지 코인은 증폭된 위험과 보상을 수반합니다. 영구 부채 메커니즘과 잠재적 스왑 불변량을 통해 Covenant 시장은 자본 효율적이고 완전히 담보화된 대출 거래소로 운영되어 자체 안정화되고 동적인 위험 및 수익 변환을 가능하게 합니다.

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**
• [2075992fd533af9da64fbdaf690d0b8627110261](https://github.com/covenant-labs/covenant-core/tree/2075992fd533af9da64fbdaf690d0b8627110261)
  (covenant-labs/covenant-core)

**수정 검토 커밋 해시:**
• [a4521cde950b712db14e7ed550af858fd1c73bd0](https://github.com/covenant-labs/covenant-core/tree/a4521cde950b712db14e7ed550af858fd1c73bd0)
  (covenant-labs/covenant-core)

# 범위 (Scope)

- `CovenantLiquid.sol`
- `LatentSwapLEX.sol`
- `FixedPoint96.sol`
- `LatentMath.sol`
- `SqrtPriceMath.sol`
- `DataTypes.sol`
- `Errors.sol`
- `NoDelegateCall.sol`
- `Utils.sol`
- `ExchangeLogic.sol`
- `StateLogic.sol`
- `TransferLogic.sol`
- `ValidationLogic.sol`
- `DebtMath.sol`
- `PercentageMath.sol`
- `WadRayMath.sol`
- `MetaProxyDeployer.sol`
- `ERC20.sol`
- `SynthToken.sol`
- `interfaces/`

# 발견 사항 (Findings)

# [H-01] 상태 업데이트 작업에서 `accruedProtocolFee`를 제외하지 않아 여러 문제 발생

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

Covenant 거버넌스는 프로토콜 수수료를 켤 수 있으며, 이 경우 여러 문제가 발생할 수 있습니다.

`_calculateMarketState`가 호출되면 `baseTokenSupply`를 기준으로 누적된 수수료를 계산합니다. 이 수수료는 `protocolFeeGrowth`에 추가되고 `baseTokenSupply`에서 차감됩니다. 그러나 수수료 적립 후의 여러 작업에서 여전히 수수료가 포함된 `baseTokenSupply` 값을 사용하므로 여러 문제가 발생합니다.

- `lastETWAPBaseSupply` 업데이트는 누적된 수수료가 `baseTokenSupply`를 감소시키기 때문에 잘못된 `baseTokenSupply`를 사용합니다. 이로 인해 `lastETWAPBaseSupply`를 업데이트하는 데 사용해야 하는 실제 `baseTokenSupply`가 과대평가됩니다.

```solidity
// ...
            // If baseTokenSupply has decreased since the last update, then decrease the ETWAPBaseSupply tracker
            if (marketState.baseTokenSupply < marketState.lexState.lastETWAPBaseSupply)
                marketState.lexState.lastETWAPBaseSupply =
                    marketState.lexState.lastETWAPBaseSupply.rayMul(updateFactor) +
                    marketState.baseTokenSupply.rayMul(WadRayMath.RAY - updateFactor); // eTWAP update if baseSupply decreased vs last update
        }

        // if baseTokenSupply has increased (even in same timeblock), update record
        if (marketState.baseTokenSupply >= marketState.lexState.lastETWAPBaseSupply)
            marketState.lexState.lastETWAPBaseSupply = marketState.baseTokenSupply; // immediatedly update if baseSupply increased vs last update
```

- `liquidity`로 변환된 `baseTokenSupply`는 `maxDebtValue`를 결정하는 데에도 사용됩니다. 이는 `maxDebtValue`를 과대평가하며, 이는 시장이 담보 부족 상태가 아니라고 잘못 나타낼 수 있으므로 매우 위험합니다.

```solidity
// ...
            marketState.liquidity = _synthToDex(marketState, baseTokenSupply, AssetType.BASE, Math.Rounding.Floor)
                .toUint160();

            // Calculate debt balanced value from zTokenSupply
            marketState.dexAmounts[0] = _synthToDex(
                marketState,
                marketState.supplyAmounts[0],
                AssetType.DEBT,
                Math.Rounding.Floor
            );

            // Check max value for debt, given availablie liquidity in market.
            uint256 maxDebtValue = SqrtPriceMath.getAmount0Delta(
                _edgeSqrtPriceX96_A,
                _edgeSqrtPriceX96_B,
                marketState.liquidity,
                Math.Rounding.Floor
            );
// ...
```

- 또한 `dexAmounts[1]` 및 `lastSqrtPriceX96`을 계산하는 데에도 사용됩니다.

- `lastSqrtPriceX96`, `dexAmounts` 및 `liquidity`는 `mint`, `redeem` 및 `swap` 작업 내부에서도 사용되어 이러한 작업이 잘못된 토큰 금액을 반환하게 합니다.

- 프로토콜 수수료 적립을 통한 전체 상환 언더플로/DoS, 전체 상환은 `amountOut = baseTokenSupply`를 설정하지만 Covenant는 `amountOut`과 `protocolFees`를 모두 빼서 수수료 > 0일 때 언더플로 및 되돌리기(revert)가 발생합니다.

## 권장 사항

후속 작업에서 사용하기 전에 계산된 수수료만큼 `baseTokenSupply`를 줄이십시오.

# [M-01] `SynthToken`이 잘못된 이름, 기호 및 소수점을 반환함

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

SynthToken은 상속된 ERC20 상태 변수를 적절한 재정의 대신 새로운 private 변수 `_name` 및 `_symbol`을 선언하여 재정의하려고 잘못 시도합니다. 이는 섀도잉 문제를 일으킵니다.

이로 인해 `name` 및 `symbol` 함수는 빈 문자열을 반환합니다. 이 함수들은 SynthToken의 새 변수가 아닌 ERC20의 변수(""로 설정됨)에 액세스합니다.

또한 계약의 `decimals`는 함수 재정의가 존재하지 않기 때문에 기본 자산의 소수점 대신 18을 반환합니다. 오라클 가격 계산과 결합할 때 가격 오류가 발생할 수 있습니다.

## 권장 사항

올바른 값을 반환하도록 getter 함수를 재정의하는 것이 좋습니다.

# [M-02] `baseTokenSupply`가 0이고 Synth 토큰 공급이 0이 아닐 때 시장 DoS

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

시장 상태가 업데이트되도록 하기 위해 모든 작업에서 호출되는 `_calculateMarketState` 함수 내부에는 시장이 나쁜 상태로 진입하지 않도록 하는 `baseTokenSupply == 0` 확인이 있습니다.

그러나 Synth 토큰 공급은 0이 아니지만 `baseTokenSupply`는 0인 이 상태는 특히 시장 초기 단계에서 발생할 수 있습니다.

시장에 입금하고 소량(dust)의 Synth 토큰을 남기면 스왑 수수료와 프로토콜 수수료가 결합되어 `baseTokenSupply`가 0이 되는 동안 소량의 Synth 토큰이 남을 수 있음을 관찰할 수 있습니다.

## 권장 사항

각 작업 후 상태를 확인하여 이 시나리오를 방지하거나, 첫 번째 예금자로부터 소량의 Synth 주식을 가져와서 죽은(dead) 주소로 발행하여 이 시나리오를 피하십시오.

# [M-03] ETWAP 로직이 상환 흐름에 대한 그리프(grief) 공격을 허용함

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

ETWAP 구현은 사용자가 1시간마다 약 25%를 상환할 수 있도록 하는 데 사용됩니다. `_calculateMarketState()`는 기본 토큰 공급이 감소할 때 `lastETWAPBaseSupply`를 논리적으로 업데이트하고, `baseTokenSupply`가 `lastETWAPBaseSupply`보다 크거나 같을 때 즉시 설정합니다.

이 `lastETWAPBaseSupply`는 `_checkRedeemCap()`으로 전달되고, `remainingBaseSupply`가 `eTWAPBaseTokenSupply`의 75%보다 작으면 되돌려집니다(revert).

그러나 여기에 사용된 로직은 다음 공격을 사용하여 악용될 수 있습니다:

1. 합법적인 사용자가 상환하고자 하는 일부 synth 토큰을 보유하고 있습니다.
2. 공격자는 플래시 대출을 활용하여 대량으로 `mint()` 함수를 호출하고 동일한 트랜잭션에서 즉시 상환함으로써 이러한 트랜잭션을 프론트런(front-run)할 수 있습니다.
3. 이 공격은 `eTWAPBaseTokenSupply`를 급증시키고, 올바르게 조정되면 `eTWAPBaseTokenSupply`가 다시 정상화될 때까지(몇 분 소요) 합법적인 사용자가 상환하지 못하게 할 수 있습니다.

이는 소규모 시장에서 쉽게 수행될 수 있으며 사용자에게 잠시 동안 피해를 줄 수 있으며, 대규모 시장의 변동성 동안 체계적으로 수행되면 많은 사용자가 상환하지 못하도록 DoS 공격을 할 수 있습니다.

## 권장 사항

이 문제는 eTWAP 로직이 기본 공급 급락만 고려하기 때문에 발생합니다. 양방향 모두에 동일하게 적용하는 것이 좋습니다.

# [M-04] `swap()`을 사용하여 상환 캡 우회

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

단기간에 기본 자산의 대량 인출을 제한하도록 설계된 `_checkRedeemCap()` 안전 메커니즘을 우회하기 위해 `swap()` 함수를 사용할 수 있는 치명적인 취약점이 존재합니다. 이를 통해 사용자는 `redeem()`이 시행하는 상환 캡 확인을 트리거하지 않고 `swap()`을 통해 baseToken을 얻을 수 있습니다.

`LatentSwapLEX.sol`의 `redeem()` 함수는 `_checkRedeemCap()`을 적절하게 호출하여 단기간에 인출할 수 있는 기본 자산의 양을 제한하여 시장의 안정성을 보호합니다.

그러나 `swap()` 함수는 synth 토큰(aToken 또는 zToken)을 `baseToken`으로 교환하는 데 사용될 때 내부적으로 `LatentMath.computeRedeem`을 호출하여 기능적으로 동일한 작업을 수행합니다. 결정적으로, `swap` 함수의 코드 경로에는 `_checkRedeemCap()` 호출이 **포함되어 있지 않습니다**.

상환 캡 우회 자체가 반드시 "뱅크런"을 가능하게 하는 것과 동일한 것은 아닙니다. 상환 캡은 정상적인 유동성 유출을 막기 위한 것이 아닙니다. 그러나 이 우회는 프로토콜이 제한하고자 했던 복잡한 원자적 시퀀스를 가능하게 할 수 있습니다. 특히, 단일 블록 내에서 실행되는 **대량 발행 → 스왑(baseToken 확보) → 상환**과 같은 시퀀스는 온체인 상태나 가격을 조작하는 데 사용될 수 있습니다. 알려진 정밀도 제한(예: `sqrtPriceX96`)을 감안할 때 이러한 원자적 시퀀스는 일시적인 가격 불일치, 조작 또는 시장이 예상치 못한 LTV 한도에 도달하게 할 위험을 증가시킵니다.

이 우회는 상환 캡이 제공하는 의도된 통제를 약화시키고 특정 조건에서 조작 또는 LTV 트리거 효과의 위험을 증가시킵니다.

## 권장 사항

이 취약점을 해결하려면 `assetOut`이 `baseToken`일 때마다 `_checkRedeemCap()` 확인을 `swap()` 함수의 실행 경로에 통합해야 합니다.

# [M-05] 부채 과다 발행으로 초기 시장이 DoS될 수 있음

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

시장이 아직 `baseTokenSupply`가 낮은 초기 단계에 있을 때, 공격자는 최대 부채 한도에 가깝게 zToken의 많은 비율을 발행할 수 있습니다. 이로 인해 시장은 가격 변동에 매우 민감해지며, 가격이 하락하면 시장은 담보 부족 상태가 되고 후속 발행 작업은 되돌려지며(revert), 정직한 사용자는 기본 유동성을 추가하고 시장을 부트스트랩하는 것이 효과적으로 차단(DoS)됩니다.

## 권장 사항

담보가 부족할 때 발행 작업(또는 관리자로 제한)을 허용하고, 상태를 악화시키는 z-side 상환 또는 스왑만 차단하십시오.

# [L-01] 이자 적립 시 산술 언더플로로 인한 프로토콜 DoS 가능성

_해결됨 (Resolved)_

`DebtMath.calculateApproxNotionalUpdate`에서 `_lnRate`가 음수일 때 함수는 다음을 계산합니다: `WadRayMath.RAY - rate1 + rate2 - rate3`.

`_timeDelta / _duration > 1 / |_lnRate|` (`rate1` > `WadRayMath.RAY`)인 경우 뺄셈 `WadRayMath.RAY - rate1`은 언더플로되고 되돌려집니다.

이는 다음 경우에 발생할 수 있습니다:
- 부채 가격 할인이 매우 커짐
- 마지막 업데이트 이후 큰 시간 델타가 지남 (풀이 현재 매력적이지 않음)
- 부채 기간이 상대적으로 작음

프로토콜은 `calculateApproxNotionalUpdate` 함수의 산술 언더플로로 인해 영구적으로 사용 불가능해질 수 있으며, 모든 시장 상태 업데이트를 방지하고 전체 시스템을 효과적으로 DoS할 수 있습니다.

권장 사항:
- `WadRayMath.RAY + rate2 - rate1 - rate3` 사용 또는
- 정확한 지수 계산을 사용하여 문제를 완전히 방지하십시오.

# [L-02] 전체 상환 시나리오에서 일관성 없는 가격 재설정 동작

_해결됨 (Resolved)_

`LatentMath.computeRedeem` 함수는 모든 토큰이 상환될 때 현재 시장 가격을 반환하는 반면, 상위 수준의 `LatentSwapLEX._calcRedeem` 함수는 전체 상환 시나리오에서 목표 가격으로 올바르게 재설정합니다. 이로 인해 가격 재설정 동작에 불일치가 발생합니다.

`LatentMath.computeRedeem`을 수정하여 전체 상환 시나리오에서 목표 가격으로 재설정하도록 권장합니다.

# [L-03] 스왑에서 비-기본 자산 출력에 대한 0 금액 검증 누락

_해결됨 (Resolved)_

ValidationLogic의 `checkSwapOutputs` 함수는 assetOut이 `AssetType.BASE`일 때만 0 출력 금액을 검증하지만, 출력 자산이 `AssetType.LEVERAGE` 또는 `AssetType.DEBT`일 때는 동일한 검증을 수행하지 못합니다. 이러한 불일치로 인해 0개의 합성 토큰이 발행되는 스왑이 허용되어 사용자 경험이 저하되고 입력 토큰이 손실될 수 있습니다.

모든 자산 유형에 대해 0 금액 확인을 추가하는 것이 좋습니다.

# [L-04] 시장에서 동일한 기본 및 견적 토큰에 대한 검증 누락

_확인됨 (Acknowledged)_

`checkMarketParams` 함수는 새 시장을 생성할 때 `baseToken`과 `quoteToken`이 다른 주소인지 검증하지 못합니다. 이를 통해 두 토큰이 동일한 시장을 생성할 수 있으며, 이는 무의미한 가격 계산 및 스왑 작업으로 인해 기능하지 않는 시장을 초래합니다.

`baseToken`과 `quoteToken`이 다른지 확인하는 검증 확인을 추가하는 것이 좋습니다.

# [L-05] `_dexToSynth`에서 0으로 나누기 가능성

_해결됨 (Resolved)_

`_dexToSynth` 내부에서 assetType이 `AssetType.LEVERAGE`일 때 한 분기가 `marketState.dexAmounts[1]` 대신 `marketState.supplyAmounts[1] == 0`을 잘못 확인합니다. 이로 인해 `marketState.dexAmounts[1]`이 0인 경우 0으로 나누기로 인해 작업이 되돌려질(revert) 수 있습니다.

# [L-06] ETWAP 검사는 마지막 사용자의 즉시 전체 인출을 차단함

_해결됨 (Resolved)_

`LatentSwapLEX`의 `redeem` 작업은 마지막 인출자가 호출한 경우에도 항상 ETWAP 상환 캡을 확인합니다.

 이로 인해 마지막 인출자는 전체 인출을 수행하기 위해 불필요하게 작업을 일괄 처리해야 합니다. 시장을 비우는 전체 인출인 경우 상환 캡 확인을 건너뛰는 것을 고려하십시오.

# [L-07] 미리보기(Preview) 함수들이 출력을 검증하지 못함

_해결됨 (Resolved)_

`previewMint()`, `previewRedeem()` 및 `previewSwap()` 함수를 통해 사용자는 트랜잭션 결과를 미리 볼 수 있어 온체인에 실제 트랜잭션을 제출하기 전에 출력을 확인할 수 있습니다.

그러나 이러한 함수들은 실제로 `ValidationLogic` 계약을 통해 출력을 검증하지 않으므로 트랜잭션을 실제로 모방하지 못합니다. 따라서 사용자는 자신의 트랜잭션이 통과될 것이라고 속을 수 있습니다.

각 미리보기 함수에 위 검증 확인을 추가하는 것이 좋습니다.

# [L-08] 잘못된 concat 사용으로 토큰 이름과 기호가 손상됨

_해결됨 (Resolved)_

SynthToken의 생성자는 토큰의 이름과 기호를 설정합니다. `z`와 `a`는 접두사로 추가되어야 하지만 현재 접미사로 추가되고 있습니다.

접두사에 추가하는 것이 좋습니다.

# [L-09] 시장 상태 손상: `mint()` 비활성화되었지만 스왑으로 부채 생성 허용

_해결됨 (Resolved)_

중요한 설계 결함으로 인해 프로토콜은 부채 토큰(zTokens)의 공급이 0이지만 레버리지 토큰(aTokens)이 여전히 존재하는 손상된 상태에 들어갈 수 있습니다. 이 상태에서 기본 유동성 제공 함수인 `mint()`는 영구적으로 비활성화되지만 `swap()` 함수는 여전히 새로운 부채를 생성하는 데 사용될 수 있어 일관성 없고 비논리적인 시장 동작을 초래합니다.

이는 사용자가 일련의 스왑(예: `DEBT` -> `LEVERAGE`)을 통해 이 상태에 도달할 수 있습니다. `zTokenSupply`가 0이 되면 시장의 내부 가격(`lastSqrtPriceX96`)은 절대 최소 경계(`_edgeSqrtPriceX96_A`)로 이동합니다.

**`mint()` 실패:** 사용자가 이후에 `mint()`를 호출하려고 시도하면 `LatentSwapLEX`의 `_calcMint()` 함수는 가격이 경계에 있기 때문에 0개의 zToken이 발행되어야 한다고 올바르게 계산합니다. 이 `zTokenAmountOut = 0`은 0 금액 발행을 금지하는 `ValidationLogic.sol::checkMintOutputs`에서 트랜잭션을 되돌리게(revert) 합니다. 시장은 이제 기본 유동성 메커니즘에 대해 효과적으로 "벽돌(bricked)" 상태가 됩니다.
**`swap()` 성공:** 이 동일한 손상된 상태에서 사용자는 `swap()`을 성공적으로 호출하여 `BASE` 토큰을 `DEBT` (zTokens)로 변환할 수 있습니다. 이렇게 하면 `mint()`를 다시 호출할 수 있게 됩니다.

이러한 불일치는 시장의 핵심 로직을 깨뜨립니다. 유동성 추가를 위한 의도된 메커니즘이 차단되어, 사용자가 시장을 기능적 상태로 복원하기 위해 덜 효율적인 단면 스왑을 수행하도록 강요합니다. 이는 기능하지 않는 상태에 쉽게 갇힐 수 있는 혼란스럽고 취약한 시스템을 만듭니다.

# [L-10] 일관성 없는 LTV는 담보 부족 시 zToken 상환을 차단함

_해결됨 (Resolved)_

redeem 함수에는 담보 부족 시 유익한 시장 개선을 방해하는 일관성 없는 로직이 포함되어 있습니다. 프로토콜은 담보가 부족할 때 zToken 전용 상환(부채 감소가 시장 건전성을 개선하므로)을 올바르게 허용하지만, 시장을 한도 내로 완전히 되돌릴 만큼 크지 않으면 상환을 차단할 수 있는 엄격한 LTV 경계를 시행합니다.

시장이 `currentPrice > _limHighSqrtPriceX96`으로 담보가 부족할 때:
- 작은 zToken 상환: LTV를 개선하지만 가격을 `_limHighSqrtPriceX96` 아래로 낮추지 않음 -> 되돌리기(REVERT).
- 큰 zToken 상환: LTV를 개선하고 가격을 `_limHighSqrtPriceX96` 아래로 낮춤 -> 성공(SUCCESS).

이는 유익한 조치가 차단되는 문제가 있는 임계값 효과를 생성합니다. 스왑 함수는 상황을 악화시키는 조치에 대해서만 LTV 확인을 시행하여 이를 올바르게 처리합니다.

**권장 사항**

담보가 부족할 때 zToken 전용 상환에 대한 LTV 경계 확인을 건너뛰십시오.

# [L-11] 일관성 없는 상환 캡으로 과도한 인출 허용

_해결됨 (Resolved)_

`LatentSwapLEX`의 `MAX_REDEEM_NO_CAP` 상수는 `10**9` 기본 단위의 고정 값으로 설정되어 있어 소수점이 다른 토큰에 대해 경제적 임계값이 크게 다릅니다. 이를 통해 임계값 미만일 때 비율 제한 없이 시장을 완전히 비울 수 있습니다.

WBTC(8 소수점)의 경우 이는 **10 BTC**(`10^9 / 10^8 = 10`)로 해석되며, 이는 ETWAP 공급이 10 BTC 이하인 모든 시장이 상환 캡 보호를 트리거하지 않고 단일 트랜잭션으로 완전히 상환될 수 있음을 의미합니다.

상환 캡 메커니즘은 활성 상태일 때 30분 창당 ETWAP 공급의 25%로 상환을 제한하도록 설계되었지만, 임계값 미만의 시장에 대해서는 이 보호가 완전히 우회됩니다. 이는 10 BTC가 비율 제한 없이 인출되어서는 안 되는 상당한 가치를 나타내는 WBTC와 같은 고가치, 저소수점 토큰에 중대한 취약점을 생성합니다.

**권장 사항**

모든 토큰에 대해 일관된 경제적 제한을 보장하기 위해 소수점 임계값 계산을 구현하십시오.

# [L-12] computeLiquidity 함수에서 잘못된 512비트 숫자 비교

_해결됨 (Resolved)_

`computeLiquidity` 함수에서 코드는 `q > beta^2`인지 확인하려고 시도하며, 두 값은 모두 (`low_bits`, `high_bits`) 쌍으로 표시되는 512비트 숫자입니다.

현재 비교는 `(beta2_low < q_low) AND (beta2_high <= q_high)`로 변환됩니다.
그러나 두 512비트 숫자 A > B를 비교하는 올바른 방법은 `(A_high > B_high) OR (A_high == B_high AND A_low > B_low)`입니다.

`q_high > beta2_high`이지만 `q_low <= beta2_low`인 경우를 놓치고 있습니다.

q > beta^2인 경우 잘못된 청산 계산으로 이어질 수 있습니다.

**권장 사항**

모든 경우를 다루도록 비교를 업데이트하는 것이 좋습니다.

# [L-13] 프로토콜 수수료 업데이트로 인해 적립된 수수료 손실

_해결됨 (Resolved)_

`Covenant.sol`의 `setMarketProtocolFee` 함수는 현재 요율에 따라 빚진 수수료를 먼저 적립하지 않고 시장의 프로토콜 수수료율을 업데이트합니다. 이로 인해 프로토콜은 마지막 업데이트와 수수료 변경 사이에 징수했어야 할 모든 수수료를 잃게 됩니다.

수수료율이 1%에서 0.5%로 변경되고 중간 거래 없이 7일이 지난 경우, 프로토콜은 1% 요율로 7일치 수수료를 잃습니다. 다음 업데이트에서는 대신 새로운 0.5% 요율로 전체 7일 기간을 잘못 계산합니다.

**권장 사항**

수수료율을 변경하기 전에 `updateState()`를 호출하여 보류 중인 수수료를 적립하십시오.

# [L-14] `redeem`은 계산을 위해 입력을 제한하지만 제한되지 않은 금액을 소각하여 되돌리기(revert) 발생

_해결됨 (Resolved)_

`LatentSwapLEX.redeem`에서 `_calcRedeem`은 수학 계산을 위해 `aTokenAmountIn` 및 `zTokenAmountIn`을 사용 가능한 공급으로 제한하지만, 함수는 나중에 원래의 제한되지 않은 `redeemParams` 금액을 소각합니다.

이 비동기화로 인해 호출자가 캡 이상의 금액을 제공할 때(예: 경쟁 조건) 상환이 되돌려집니다. 계약이 캡 값을 사용하여 유효한 `amountOut`을 계산했음에도 불구하고 그렇습니다. 이는 일관성 없는 회계 기대로 이어집니다.

**권장 사항**

`_calcRedeem`에서 캡이 적용된 값을 반환하고 그 금액을 대신 소각하십시오.

# [L-15] `swap` 작업으로 담보 부족 상태가 될 수 있음

_해결됨 (Resolved)_

`swap` 작업이 수행되면 스왑이 실행기 전에 담보 부족 확인이 수행됩니다. 그러나 실행 후 `dexAmounts[0]`이 `maxDebtValue`를 초과하여 시장이 담보 부족 상태가 될 수 있습니다.

테스트에서 스왑 수수료 및 프로토콜 수수료 공제를 통합하면 스왑 후 최대 LTV 확인은 담보 부족을 방지하기에 충분하지 않습니다.

**권장 사항**

`isUnderCollateralized` 확인을 다시 확인하거나 스왑 후로 이동하는 것을 고려하십시오.

# [L-16] 적립 중 소수점 고려 부족으로 인한 프로토콜 수수료 손실

_해결됨 (Resolved)_

프로토콜 수수료는 스왑, 상환 또는 발행과 같은 핵심 프로토콜 작업이 발생할 때마다 `_calculateMarketState()` 함수 내부에서 계산됩니다.

그러나 `calculateLinearAccrual()`을 면밀히 관찰하면 소수점이 낮은 토큰에 대한 수수료 적립이 고려되지 않았음을 알 수 있습니다.

WBTC(8 소수점)와 같은 토큰에 대해 상당한 영향이 관찰될 수 있습니다. 1% 수수료율에서도 작은 기본 토큰 공급의 경우 0으로 내림될 수 있습니다.

**권장 사항**

상태 업데이트 시 주기적으로 진행함에 따라 선형적으로 증가하는 RAY의 전역 고정밀 인덱스를 사용하는 것이 좋습니다. 이는 인덱스 델타에 비례하여 수수료 주식을 전송하는 데 사용할 수 있습니다.

# [L-17] 시장 일시 중지는 이자 및 수수료 적립을 중지하지 않음

_확인됨 (Acknowledged)_

Covenant.sol::setMarketPause()에 의해 제어되는 시장 일시 중지 기능은 비상 상황 시 시장을 동결하도록 의도되었습니다. 그러나 현재 구현은 새 트랜잭션(`mint()`, `redeem()`, `swap()`)만 차단하고 시간 기반의 이자 및 프로토콜 수수료 적립은 중지하지 못합니다.

`LatentSwapLEX.sol::_calculateMarketState()`의 이자 및 수수료 계산은 `lastUpdateTimestamp` 이후 경과된 시간을 기준으로 합니다. 시장이 장기간 일시 중지되었다가 일시 중지 해제되면 첫 번째 트랜잭션이 전체 일시 중지 기간에 대한 이자 및 수수료를 계산하고 적용합니다.

이는 두 가지 심각한 결과를 초래합니다:
- 불공정한 불이익: 시장이 기능하지 않아 포지션을 관리하거나 청산할 수 없었던 기간 동안 a/zToken 보유자에게 이자를 부과하는 것은 근본적으로 불공평합니다.
- 시장 충격: 일시 중지 해제 시 누적된 대규모 이자 지급이 갑자기 적용되면 시장의 내부 가격 책정 및 LTV가 급격히 변경될 수 있습니다. 이는 그렇지 않으면 안정적이었을 시장을 즉시 고위험 또는 담보 부족 상태로 밀어넣어 예상치 못한 청산을 유발하거나 사용자가 의도한 대로 상호 작용하는 것을 방지할 수 있습니다.

**권장 사항**

"일시 중지"는 단순히 사용자 상호 작용의 중단이 아니라 시장 재무 상태의 완전한 동결을 의미해야 합니다(시장이 비었을 때와 동일하게 수행되어야 함).

# [L-18] 마지막 인출자가 `redeem` 작업에서 수수료를 추출하기 위해 샌드위치 공격을 당할 수 있음

_확인됨 (Acknowledged)_

`redeem`이 요청되었고 마지막 인출자가 아닌 경우 수수료는 `amountOut`에서 가져와 나머지 유동성 제공자에게 분배됩니다.

이로 인해 마지막 인출자는 샌드위치 공격에 취약해집니다. 마지막 인출자가 전체 `redeem`을 수행할 때 공격자는 소량의 유동성을 예치하여 작업을 프론트런할 수 있습니다. 그런 다음 `redeem`이 실행되면 수수료가 추출되고 공격자는 `redeem`하여 이를 수집할 수 있습니다.

**권장 사항**

이 시나리오를 피하기 위해 `redeem` 수수료를 재설계하십시오. 나머지 유동성 제공자에게 분배하는 대신 프로토콜 수수료로 추가하는 것을 고려하십시오.

# [L-19] 정확한 계산 대신 exact_out에 대한 근사 스왑 수수료 사용

_해결됨 (Resolved)_

`LatentSwapLex::swap()`은 스왑을 용이하게 하는 데 사용되며, 스왑 종류가 exact-in인지 exact-out인지 결정하기 위해 `IsExactIn`을 전환할 수 있습니다.
이 함수는 맨 마지막에 스왑 수수료를 청구합니다.

exact-in의 경우 수수료를 올바르게 청구하고 있지만, exact-out 종류의 스왑의 경우 `PercentageMath.PERCENTAGE_FACTOR + _swapFee`를 사용하면 과소 청구하게 됩니다.
원하는 출력 금액을 지정할 때 필요한 입력을 결정하기 위해 역으로 작업해야 합니다.
따라서 `isExactIn`이 false로 설정된 경우 `calcAmount`에 `PercentageMath.PERCENTAGE_FACTOR / (PercentageMath.PERCENTAGE_FACTOR - _swapFee)`를 곱해야 합니다.

또한 현재 로직은 exact-out의 경우 반올림(round up)하지 못합니다. 사용자는 적어도 실제 필요한 금액을 지불해야 하기 때문입니다.

**권장 사항**

일관된 수수료 청구를 위해 업계 표준 수수료 공제 방법을 사용하는 것이 좋습니다.
