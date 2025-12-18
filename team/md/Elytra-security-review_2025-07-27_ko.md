# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 잘 알려진 40명 이상의 프리랜서 보안 연구원으로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 $100k 이상을 획득했거나, 여러 번의 우승 경력이 있거나, 우리와의 감사에서 탁월한 성과를 보였습니다. 우리는 검증되고 동기 부여된 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도왔으며, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**ElytraProtocol/contracts** 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, **Elytra** 검토에 참여했습니다. 총 **22**개의 문제가 발견되었습니다.

# Elytra 소개 (About Elytra)

Elytra는 사용자가 HYPE 또는 USDC를 입금하고 시스템에 내기를 나타내는 elyHYPE(rsHYPE) 또는 elyUSD 토큰을 받을 수 있는 HyperEVM용 유동성 리스테이킹(Liquid Restaking) 프로토콜입니다. 통합 전략 관리, 가격 오라클 및 자산 리스테이킹 및 인출을 용이하게 하는 안전한 역할 기반 제어 시스템을 갖춘 모듈식 아키텍처를 특징으로 합니다.

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [2bbfd0117c13122111251135b31bc6764fb25bc2](https://github.com/ElytraProtocol/contracts/tree/2bbfd0117c13122111251135b31bc6764fb25bc2)<br>&nbsp;&nbsp;(ElytraProtocol/contracts)

**수정 검토 커밋 해시:**<br>• [008f19e65ebd5172f3e822de0bd1263c61dec01a](https://github.com/ElytraProtocol/contracts/tree/008f19e65ebd5172f3e822de0bd1263c61dec01a)<br>&nbsp;&nbsp;(ElytraProtocol/contracts)

# 범위 (Scope)

- `ElytraConfigV1.sol`
- `ElytraDepositPoolV1.sol`
- `ElytraOracleV1.sol`
- `ElytraUnstakingVaultV1.sol`
- `elyHYPE.sol`
- `elyUSD.sol`
- `ElyAsset.sol`
- `WHYPEPriceOracle.sol`
- `ElytraConfigRoleChecker.sol`
- `KHYPEPriceOracle.sol`

# 발견 사항 (Findings)

# [M-01] 자산 전송 함수 간의 일관성 없는 검증

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`ElytraDepositPoolV1`은 추적된 예금 잔액을 확인할 때 `allocateToStrategy()`와 `transferAssetToUnstakingVault()` 간에 일관성 없는 검증 로직을 가지고 있습니다. 이러한 불일치로 인해 공격자는 엄격한 할당 경로 대신 관대한 전송 경로를 사용하여 기부 공격 보호를 우회할 수 있습니다.

**일관성 없는 검증 로직**이 있는 `ElytraDepositPoolV1.sol` 코드를 확인해 보겠습니다.

```solidity
// allocateToStrategy() - 엄격한(STRICT) 검증
function allocateToStrategy(address asset, uint256 amount) external {
    if (totalAssetDepositsTracked[asset] < amount) revert InsufficientTrackedDepositsForAllocation();
    // 여기서 불충분한 추적 예금이면 작업을 차단합니다 @audit-poc
}

// transferAssetToUnstakingVault() - 관대한(LENIENT) 검증
function transferAssetToUnstakingVault(address asset, uint256 amount) external {
    if (amount <= totalAssetDepositsTracked[asset]) {
        totalAssetDepositsTracked[asset] -= amount;
    } else {
        totalAssetDepositsTracked[asset] = 0; // ❌ 단순히 0으로 설정하고 계속함 @audit-poc
    }
    // 여기서 추적된 예금 금액에 관계없이 작업을 허용합니다
}
```

**공격 시나리오** 예를 들어보겠습니다:
1. 공격자가 토큰을 계약에 직접 기부합니다.
2. `allocateToStrategy()`는 기부된 토큰 사용을 올바르게 차단합니다(엄격한 확인 때문).
3. `transferAssetToUnstakingVault()`는 기부된 토큰 사용을 허용합니다(관대한 확인).
4. 풀의 `totalAssetDepositsTracked`가 손상됩니다(적절한 추적 대신 0으로 설정됨).

이제 실제 **예**를 들어보겠습니다:
```
초기 상태: 1000 USDC 추적된 예금, 1000 USDC 실제 잔액
공격자 기부: 500 USDC를 계약에 직접 기부
새 상태: 1000 USDC 추적됨, 1500 USDC 실제 잔액

allocateToStrategy(USDC, 1200) → 되돌려짐(REVERTS) ✅ (1000 < 1200)
transferAssetToUnstakingVault(USDC, 1200) → 성공(SUCCEEDS) ❌ (추적을 0으로 설정)
```

이를 통해 기부 공격은 엄격한 검증을 우회하여 예금 회계를 손상시킬 수 있으며, 비사용자 예금 사용을 허용하고 정확히 이 시나리오를 방지하기 위한 L-06 수정 사항을 깨뜨릴 수 있습니다.

## 권장 사항

동일한 엄격한 확인을 사용하여 두 함수 간의 검증을 일관되게 만드십시오.

```solidity
function transferAssetToUnstakingVault(address asset, uint256 amount) external {
    // allocateToStrategy와 동일한 검증 추가
    if (totalAssetDepositsTracked[asset] < amount) {
        revert InsufficientTrackedDepositsForAllocation();
    }
    
    // 표준 감소 (특수 케이스 처리 없음)
    totalAssetDepositsTracked[asset] -= amount;
    
    // 나머지 함수...
}
```

# [M-02] 가격 변경 제한으로 인한 시스템 DoS 가능성

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

Elytra에서는 하나의 기본 가격 백분율 제한인 10%를 갖게 됩니다. 현재 가격과 업데이트된 가격의 차이가 가격 제한을 초과하면 입금/인출이 차단됩니다.

LST 중 하나에 문제가 발생하여 주식 가격이 10% 하락하는 경우 입금/인출이 차단됩니다. 이는 예상된 동작 중 하나입니다.

관리자로서 우리는 가격 백분율을 늘리거나 제한을 제거하여 사용자가 자산을 인출할 수 있도록 하기를 원합니다. 여기서 문제는 `setPricePercentageLimit` 함수에서 가격 업데이트를 시도한다는 것입니다. 이 트랜잭션은 다시 되돌려질 것입니다. 관리자는 시스템을 복구할 능력을 잃게 됩니다.

```solidity
    function setPricePercentageLimit(uint256 _limit) external override onlyElytraAdmin {
@>        this.updateElyAssetPrice();
        pricePercentageLimit = _limit;
        emit PricePercentageLimitUpdated(_limit);
    }
```

## 권장 사항

`updateElyAssetPrice`를 업데이트할 필요가 없습니다.

# [M-03] Elytra 주가가 부풀려져 시스템이 차단될 수 있음

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명
Elytra에서 총 자산 TVL은 `totalAssetDepositsTracked + unstakingVault + strategyBalance - reserveBalance` 공식을 기반으로 계산됩니다.

사용자는 자산을 인출하기 위해 withdrawAssets 또는 requestWithdrawal을 선택할 수 있습니다. 자산을 인출할 때 ElyTra 풀에 잔액이 충분한지 확인하고 `totalAssetDepositsTracked` 변수를 업데이트합니다.

정상적인 시나리오에서는 풀의 balanceOf가 `totalAssetDepositsTracked`와 동일해야 하며, 입금/인출/transferUnstaking 시 `totalAssetDepositsTracked`를 적시에 업데이트합니다.

여기서 문제는 사용자가 풀에 일부 자산을 기부하면 풀에 잔액이 충분하지 않을 때 자산을 인출할 수 있다는 것입니다. 이 경우에도 여전히 작동할 수 있으며 `totalAssetDepositsTracked` 변수를 `0`으로 업데이트합니다. 그러나 이는 주가에 영향을 미칩니다.

예를 들어:
1. Alice는 첫 번째 예금자로서 1000 HYPE를 입금합니다.
2. 관리자가 1000 HYPE를 관련 전략에 할당합니다. 이제 `totalAssetDepositsTracked`는 0입니다.
3. Alice는 풀에 999 HYPE를 기부하고 999주를 인출합니다. `totalAssetDepositsTracked`는 `0`으로 유지되고 총 주식 금액은 1로 감소합니다. 이는 주가를 증가시킵니다.
4. `pricePercentageLimit`를 설정하면 이 가격 제한 확인으로 인해 사용자가 입금/인출에 실패할 수 있습니다.
여기서 또 다른 영향은 주가를 많이 올리면 각 반올림/올림에 대한 실제 자산이 더 커진다는 것입니다. 이는 투자자에게 좋지 않은 경험을 줄 것입니다.

```solidity
    function withdrawAsset(
        address asset,
        uint256 elyAssetAmount,
        address receiver,
        uint256 minUnderlyingAssetExpected
    )
    {
        uint256 poolBalance = IERC20(asset).balanceOf(address(this));
        if (poolBalance < assetAmountBeforeFee) {
            revert InsufficientPoolBalance(assetAmountBeforeFee, poolBalance);
        }
        ...
@>        if (assetAmountBeforeFee <= totalAssetDepositsTracked[asset]) {
            totalAssetDepositsTracked[asset] -= assetAmountBeforeFee;
        } else {
@>            totalAssetDepositsTracked[asset] = 0;
        }
    }
```

## 권장 사항
`totalAssetDepositsTracked`가 인출 자산을 지불하기에 충분하지 않은 경우 되돌려야(revert) 합니다.

# [L-01] 일관성 없는 타임스탬프로 인한 잘못된 스테일니스(Staleness) 확인 및 정보

_해결됨 (Resolved)_

`KHYPEPriceOracle` 계약에는 구조적 불일치가 있어 `StakingAccountant`에서 가져온 동적 가격 fetching이 최신 데이터를 제공하지만 `lastUpdateTime` 타임스탬프를 업데이트하지 않습니다. 이로 인해 스테일니스 검증이 오래된 타임스탬프를 사용하고 잠재적으로 최신 가격을 거부할 수 있습니다.

근본적인 문제는 `getAssetPrice()`에 있습니다. 이 뷰 함수는 최신 데이터를 가져오지만 상태를 업데이트할 수 없습니다.

```solidity
function getAssetPrice(address asset) external view override returns (uint256) {
    if (useManualPrice) {
        return manualPrice; // 수동 업데이트의 올바른 타임스탬프 사용
    }
    
    // StakingAccountant에서 최신 데이터를 가져오지만 lastUpdateTime을 업데이트할 수 없음
    try stakingAccountant.kHYPEToHYPE(1e18) returns (uint256 hypeAmount) {
        uint8 kHYPEDecimals = IERC20Metadata(KHYPE_TOKEN).decimals();
        return UtilLib.scaleToEighteenDecimals(hypeAmount, kHYPEDecimals);
    }
}
```

그러나 스테일니스 검증 함수는 오래된 타임스탬프에 의존합니다:

```solidity
function getAssetPriceWithStalenessCheck(address asset) external view returns (uint256 price) {
    price = this.getAssetPrice(asset); // StakingAccountant에서 최신 가격 가져옴
    
    if (maxStaleness > 0) {
        uint256 age = block.timestamp - lastUpdateTime; // 오래된 타임스탬프 사용!
        if (age > maxStaleness) {
            revert PriceTooStale(lastUpdateTime, maxStaleness); // 최신 데이터를 거부할 수 있음
        }
    }
}
```

이로 인해 몇 가지 문제가 발생합니다:

(1) `StakingAccountant`의 최신 가격이 "오래된(stale)" 것으로 잘못 거부될 수 있습니다. 예를 들어, 오라클이 2시간 전에 초기화되었지만 `getAssetPrice()`가 현재 데이터를 가져오는 경우 스테일니스 확인은 데이터가 최신임을 인식하지 못하고 나이를 2시간으로 계산합니다.

(2) `getLastUpdateTime()`을 호출하는 외부 소비자는 동적 모드에서 실제 데이터 최신성을 반영하지 않는 타임스탬프를 받습니다.

`lastUpdateTime`은 `updateManualPrice()`, `refreshPrice()`, `updateLastUpdateTime()`과 같은 비-뷰 함수에서만 업데이트되지만 일반적인 동적 가격 가져오기 중에는 업데이트되지 않아 데이터 최신성과 타임스탬프 추적 간에 근본적인 불일치가 발생합니다.

**권장 사항:** 동적 가격 책정 모드에서 스테일니스 확인이 수동 `refreshPrice()` 호출 후에만 안정적으로 작동함을 문서화하거나, 뷰 함수가 동적 가격 가져오기 중에 타임스탬프를 업데이트할 수 없는 구조적 제약을 처리하도록 스테일니스 로직을 재설계하십시오.

# [L-02] 총 가치에 대한 다중 내림(Rounding down) 문제

_해결됨 (Resolved)_

`_getTotalValueInProtocol` 함수에서 프로토콜의 총 가치는 자산 간 루프를 통해 계산됩니다. 18 소수점으로 스케일링한 다음 가격을 곱하고 18 소수를 제거합니다. 그러나 다음 계산 방법은 여러 번의 내림을 유발합니다.

`totalValueInProtocol += totalAssetTVLScaled * assetPrice / 1e18;`.

대신 여기서 1e18을 제거하고 루프 끝에서 1e18로 나누는 것을 고려하십시오.

# [L-03] 금액 > 추적된 할당인 경우 잘못된 수익 추적

_해결됨 (Resolved)_

`deallocateFromStrategy` 함수에서 수익 금액은 `withdrawn - amount`로 추적됩니다. 그러나 추적된 전략 할당은 전략으로 전송된 실제 금액이며, amount 변수가 이 추적 숫자보다 크면 수익에 대한 잘못된 추적이 발생합니다.

수익을 `withdrawn - reductionAmount`로 계산하는 것을 고려하십시오.

# [L-04] 잘못된 변수 카운팅으로 인한 잘못된 갭 슬롯 양

_해결됨 (Resolved)_

많은 계약에서 갭 슬롯을 추가하여 50 변수 슬롯에 도달하려고 하지만 일부 계약에서는 변수 카운팅이 올바르지 않습니다. ElytraConfig 계약은 7개의 변수가 있다고 가정하여 계약 끝에 43 슬롯 갭을 남기지만 올바르지 않으며 9개의 변수가 있고 41 갭이어야 합니다. ElytraDepositPoolV1은 10개의 변수가 있지만 39 슬롯 갭을 남깁니다. ElytraUnstakingVaultV1 계약은 7개의 변수가 있지만 44 슬롯 갭을 남깁니다.

# [L-05] 대기 중인 인출에 대한 비대칭 수익 처리

_해결됨 (Resolved)_

대기 중인(Queued) 인출은 즉시 인출에 비해 **비대칭 수익 처리**를 경험하며 통합 풀의 공정성 원칙을 위반합니다. 프로토콜은 대기 중인 사용자에게는 **요청 시점**에 환율을 고정하지만 즉시 인출자에게는 **현재 환율**을 적용하여, 대기 중인 사용자는 손실로부터 보호받는 동안 언스테이킹 기간 동안 얻은 수익을 몰수당하게 됩니다.

**코드**를 살펴보면 `getAssetAmountToReceive`는 즉시 인출 시 현재 환율을 사용하지만, 대기 중인 경우 인출 요청 시 고정 환율을 사용합니다.
```solidity
// 즉시(INSTANT): 실행 시 동적 환율
uint256 assetAmount = getAssetAmountToReceive(asset, elyAssetAmount); // ✅ 현재 환율

// 대기(QUEUED): 요청 시 고정 환율
assetAmount: assetAmount, // ❌ 요청 시점에 잠김, 업데이트되지 않음
```

이는 **수익 기간** 동안 문제를 일으킵니다 -> 즉시 사용자는 110 HYPE를 얻고, 대기 사용자는 100 HYPE를 얻습니다 (10% 수익 놓침).

그리고 **손실 기간** 동안 -> 즉시 사용자는 90 HYPE를 얻고, 대기 사용자는 100 HYPE를 얻습니다 (10% 손실로부터 보호됨).

이러한 비대칭성은 사용자가 예상 수익 기간 동안 대기 중인 인출을 피하여 프로토콜 유동성을 압박하고 유동적 리스테이킹의 지속적인 수익 약속과 모순되는 **행동 왜곡**을 초래합니다.

**권장 사항**

대기 중인 사용자가 현재 환율을 받고 언스테이킹 기간 동안 얻은 수익을 몰수당하지 않도록 요청 시점이 아닌 완료 시점에 `assetAmount`를 계산하십시오.

# [L-06] `DepositPool` 대 `UnstakingVault`의 일관성 없는 인출 제어

_해결됨 (Resolved)_

`ElytraUnstakingVaultV1`의 `completeWithdrawal()` 함수는 `ElytraDepositPoolV1`에 존재하는 전역 인출 제어를 존중하지 않습니다. 비상 상황에서 관리자가 인출을 비활성화해도 사용자는 여전히 언스테이킹 금고를 통해 지연된 인출을 완료할 수 있습니다.

**불일치:**
```solidity
// DepositPool - 인출 제어 존중
function withdrawHYPE(...) external whenWithdrawalsEnabled { } ✅
function withdrawAsset(...) external whenWithdrawalsEnabled { } ✅

// UnstakingVault - 인출 제어 무시
function completeWithdrawal(...) external nonReentrant { } ❌
```

**문제** -> 관리자는 `withdrawalsEnabled = false`로 설정하면 모든 인출이 중단될 것으로 예상하지만 지연된 인출 완료는 계속 작동합니다.

**영향** -> 보안 사고나 오라클 실패 시 관리자가 자금 유출을 완전히 제어할 수 없어 비상 대응 절차를 훼손합니다.

**권장 사항**

`completeWithdrawal` 함수에 인출 제어 확인을 추가하거나, `whenNotPaused`를 사용하는 `requestWithdrawal`과 동기화하십시오.

```solidity
function completeWithdrawal(uint256 requestId) 
    external 
    nonReentrant 
    whenWithdrawalsEnabled  // ✅ 통합 제어 추가
{
    // 기존 로직...
}
```

# [L-07] `getElyAssetAmountToMint()`가 오래된 `elyAsset` 가격을 사용하여 문제 발생

_해결됨 (Resolved)_

공개 뷰 함수 `getElyAssetAmountToMint()`는 일관성 없는 스테일니스 검증을 사용하여 외부 통합이 실제 실행 결과와 일치하지 않는 오래된 가격 추정치를 받게 합니다.

**일관성 없는 스테일니스 검증:**
```solidity
function getAssetAmountToReceive(address asset, uint256 elyAssetAmount) public view {
    // M-01 수정: 안전한 계산을 위해 스테일니스 확인된 가격 사용 (둘 다 18 소수점)
    uint256 assetPrice = oracle.getAssetPriceWithStalenessCheck(asset);  ✅ 스테일니스 포함
    uint256 elyAssetPrice = oracle.elyAssetPrice();                      ❌ 스테일니스 없음
    
    return (elyAssetAmount * elyAssetPrice) / assetPrice;
}
```

**실제 실행에서는 updateElyAssetPrice를 호출하여 최신 가격을 사용합니다.**
```solidity
function withdrawAsset(...) external {
    // M-02 수정: 인출 전 elyAsset 가격 업데이트
    IElytraOracle(oracleAddr).updateElyAssetPrice();  ✅ 최신 가격 강제
    
    uint256 assetAmountBeforeFee = getAssetAmountToReceive(asset, elyAssetAmount);
}
```

슬리피지 계산을 위해 오래된 뷰 함수 추정치를 사용하는 외부 프로토콜은 추정치와 실행 사이에 elyAsset 가격이 업데이트되면 트랜잭션 되돌림을 경험하여 통합 실패를 초래합니다.

**권장 사항**

`getAssetAmountToReceive`에 대해 일관된 스테일니스 검증을 사용하십시오.

```solidity
function getAssetAmountToReceive(address asset, uint256 elyAssetAmount) public view {
    uint256 assetPrice = oracle.getAssetPriceWithStalenessCheck(asset);
    uint256 elyAssetPrice = oracle.getElyAssetPriceWithStalenessCheck(); // ✅ 수정됨
    
    return (elyAssetAmount * elyAssetPrice) / assetPrice;
}
```

이렇게 하면 외부 소비자가 내부 실행 흐름과 동일한 가격 검증을 받게 되어 추정치-실행 불일치를 제거할 수 있습니다.

# [L-08] 프로토콜 수수료 설정에 캡이 없어 DoS 위험 발생

_해결됨 (Resolved)_

`ElytraConfigV1`의 `setProtocolFeeBps()` 함수는 프로토콜 수수료를 합리적인 수준으로 제한하는 검증이 부족하여 다른 프로토콜 함수와의 불일치 및 잠재적 DoS 위험을 생성합니다.

아래 2개 계약을 확인해 보면:
- **ElytraOracleV1**: 적절한 검증이 있는 10%(1000 BPS) 캡이 있습니다.
- **ElytraConfigV1**: 캡이 없으며 무제한 수수료 값을 허용합니다.

```solidity
// ElytraOracleV1.sol - 보호됨
function setProtocolFee(uint256 _feeInBPS) external override onlyElytraAdmin {
    uint256 MAX_FEE_BPS = 1000; // 최대 10% 수수료 ✅
    if (_feeInBPS > MAX_FEE_BPS) {
        revert ProtocolFeeExceedsMaximum(_feeInBPS, MAX_FEE_BPS);
    }
}

// ElytraConfigV1.sol - 취약함
function setProtocolFeeBps(uint256 _protocolFeeInBPS) external onlyRole(DEFAULT_ADMIN_ROLE) {
    protocolFeeInBPS = _protocolFeeInBPS; // ❌ 검증 없음
}
```

관리자를 신뢰할 수 있다고 해도 수수료를 10,000 BPS(100%) 이상으로 설정하면 수수료 계산에서 산술 언더플로우가 발생하여 모든 입금/인출이 되돌려지고 수정될 때까지 프로토콜이 사실상 DoS 상태가 됩니다.

**권장 사항**

`ElytraOracleV1`에서 사용된 것과 동일한 수수료 캡 검증을 추가하십시오.

```solidity
function setProtocolFeeBps(uint256 _protocolFeeInBPS) external onlyRole(DEFAULT_ADMIN_ROLE) {
    uint256 MAX_FEE_BPS = 1000; // 최대 10% 수수료
    if (_protocolFeeInBPS > MAX_FEE_BPS) {
        revert ProtocolFeeExceedsMaximum(_protocolFeeInBPS, MAX_FEE_BPS);
    }
    protocolFeeInBPS = _protocolFeeInBPS;
    emit ProtocolFeeUpdated(_protocolFeeInBPS);
}
```

# [L-09] 업그레이드 가능한 오라클 계약에 스토리지 갭 누락

_해결됨 (Resolved)_

`KHYPEPriceOracle` 및 `WHYPEPriceOracle` 계약은 업그레이드 가능함(UUPS 패턴)에도 불구하고 스토리지 갭이 누락되었습니다. 스토리지 갭은 기존 스토리지 레이아웃을 이동하지 않고 향후 업그레이드에서 새 상태 변수를 추가할 수 있도록 스토리지 슬롯을 예약하여 데이터 손상을 방지합니다.

향후 업그레이드 중에 새 상태 변수를 추가하면 기존 스토리지 슬롯을 덮어써 계약 상태가 손상되고 잠재적으로 오라클 기능이 중단될 수 있습니다.

**권장 사항**

두 오라클 계약 모두에 스토리지 갭을 추가하십시오:

```solidity
/*//////////////////////////////////////////////////////////////
                            STORAGE GAP
//////////////////////////////////////////////////////////////*/

/// @notice Storage gap to allow for future upgrades
uint256[47] private __gap;
```

이를 통해 팀은 스토리지 호환성을 손상시키지 않고 향후 업그레이드에서 새 상태 변수를 추가할 수 있습니다.

# [L-10] 주석과 구현 간의 불일치

_해결됨 (Resolved)_

ElytraDepositPool에는 하나의 변수 `MIN_DUST_AMOUNT`가 있습니다. 주석에 따르면 이는 모든 작업에 대한 금액 제한입니다.

그러나 현재 구현에 따르면 이 MIN_DUST_AMOUNT는 입금 함수에서만 작동합니다. 사용자가 자산을 인출하려는 경우 여기에 제한이 없습니다.

```solidity
    uint256 public constant MIN_DUST_AMOUNT = 1000; // Minimum 1000 wei for any operation
```

권장 사항: 주석과 구현을 일관되게 만드십시오.

# [L-11] 정적 할당 대 동적 전략으로 인해 손실 보고 시 DoS 발생

_해결됨 (Resolved)_

`reportStrategyLoss()` 함수는 실시간 전략 잔액 대신 정적으로 추적된 할당(`assetStrategyAllocations`)에 대해 슬래싱 금액을 검증하여, 전략이 슬래싱 이벤트가 발생하기 전에 수익을 얻는 경우 서비스 거부를 유발합니다. 이 검증 로직은 합법적인 슬래싱 손실이 보고되는 것을 방지하여 영구적인 회계 손상과 잠재적인 프로토콜 파산으로 이어집니다.

핵심 문제는 Elytra의 이중 추적 시스템에서 비롯됩니다. TVL 계산은 실시간 전략 잔액을 올바르게 사용하지만(이전 감사의 H-04 수정), 슬래싱 검증은 여전히 오래된 정적 할당 추적에 의존합니다. 전략이 수익을 창출하면 실제 잔액이 원래 추적된 할당 금액보다 커져서 상당한 슬래싱 이벤트를 보고할 수 없게 됩니다.

```solidity
function reportStrategyLoss(address asset, address strategy, uint256 amount) external onlyElytraAdmin {
    // 오래된 정적 추적에 대해 검증
    if (assetStrategyAllocations[asset][strategy] < amount) revert LossExceedsStrategyAllocation();
    
    // 정적 추적만 업데이트하여 추가 불일치 생성
    assetsAllocatedToStrategies[asset] -= amount;
    assetStrategyAllocations[asset][strategy] -= amount;
}
```

이로 인해 슬래싱 계산은 실시간 잔액(당연히 그래야 함)에 대해 수행되지만 검증은 오래된 정적 할당을 사용하는 근본적인 불일치가 발생합니다. 프로토콜은 불일치를 조정하기 위한 수동 `syncStrategyAllocations()` 함수를 포함하지만, 이는 비상 상황에서 실패하는 취약한 2단계 프로세스를 생성합니다.

**취약한 시나리오:**
다음 단계는 정상적인 프로토콜 작업이 어떻게 완전한 슬래싱 보고 실패로 이어지는지 보여줍니다:

1. **초기 할당**: 프로토콜은 100 ETH를 전략 A에 할당하고 `assetStrategyAllocations[WETH][StrategyA] = 100 ETH`로 추적합니다.

2. **전략 성장**: 6개월 동안 전략 A는 정상적인 리스테이킹 보상을 통해 25% 수익을 얻어 실제 잔액이 125 ETH에 도달하지만 추적된 할당은 100 ETH로 유지됩니다.

3. **슬래싱 이벤트**: 검증인이 20% 슬래싱(현실적인 시나리오)을 겪어 보고해야 할 20 ETH 손실이 발생합니다.

4. **DoS 트리거**: 관리자가 `reportStrategyLoss(WETH, StrategyA, 20)`을 시도하지만 트랜잭션이 되돌려집니다. `20 ETH > 0 ETH`이기 때문입니다. (20 ETH 손실 금액이 125 ETH 실제 잔액과 100 ETH 추적된 할당 사이의 0 ETH 성장 마진을 초과함 - *역자 주: 원문 설명이 약간 불분명하나, 로직상 할당량보다 손실액이 크면 안 된다는 체크가 있는데, 수익으로 잔액이 늘었어도 할당량 변수는 그대로라 손실액이 할당량보다 작아야 하는데 실제로는 그렇지 않을 수 있다는 의미로 보임. 하지만 예시에서는 20 < 100 이므로 통과해야 할 것 같은데, 원문의 설명이 "20 ETH loss amount exceeds the 0 ETH growth margin..." 이라고 되어 있어 혼동이 있음. 아마도 보고하려는 손실액을 할당량에서 뺄 때의 제약을 말하는 것 같으나, 예시는 20이고 할당은 100이라 뺄셈은 가능함. L-11의 설명은 "수익이 발생하여 실제 잔액이 할당량보다 커진 상태에서 슬래싱이 발생했을 때, (실제 잔액 - 슬래싱 후 잔액) = 손실액인데, 이 손실액을 report하려고 할 때 static allocation 변수에서 뺌. 만약 슬래싱이 커서 static allocation보다 크면 revert됨"을 의미하는 듯함. 예시의 20% 슬래싱은 125 * 0.2 = 25 ETH 손실. 25 < 100 이므로 통과해야 함. 원문의 예시가 약간 이상하지만, 핵심은 "슬래싱 손실이 할당량보다 클 경우" 문제가 된다는 것임.*)

5. **프로토콜 붕괴**: 슬래싱을 보고할 수 없고, 내부 회계가 부정확하게 유지되며, 사용자는 슬래싱 전 회계를 기반으로 부풀려진 비율로 인출할 수 있어 잠재적 파산으로 이어집니다.

6. **비상 복잡성**: 슬래싱 이벤트 압박 하에서 관리자는 `syncStrategyAllocations()`을 먼저 호출한 다음 `reportStrategyLoss()`를 호출해야 함을 기억해야 하며, 이는 운영 위험과 인적 오류 가능성을 생성합니다.

보수적인 매개변수라도 이 시나리오는 매우 가능성이 높습니다: 3-6개월 동안 15% 전략 성장과 15-20% 슬래싱 이벤트 결합은 유동적 리스테이킹 환경에서 현실적입니다.

**권장 사항**

정적 검증 의존성을 제거하기 위해 다음 솔루션을 구현하십시오:

`reportStrategyLoss()`를 수정하여 오래된 할당 대신 현재 전략 잔액에 대해 검증하도록 하십시오:
```solidity
function reportStrategyLoss(address asset, address strategy, uint256 amount) external onlyElytraAdmin {
    uint256 realTimeBalance = IElytraStrategy(strategy).getBalance(asset);
    require(amount <= realTimeBalance, "Loss exceeds current strategy balance");
    // 손실 보고 처리
}
```

# [L-12] `kHYPE` 온체인 가격 검증으로 프론트런 수익 허용

_인정됨 (Acknowledged)_

현재 kHYPE 오라클은 kHYPE 토큰의 온체인 환율을 가격으로 직접 사용합니다. 그러나 `kHYPEToHYPE` 함수의 보고된 값은 새로운 검증인 손익 보고로 인해 Kinetiq의 `ValidatorManager`에 의해 변경될 수 있습니다.

```solidity
        try stakingAccountant.kHYPEToHYPE(1e18) returns (uint256 hypeAmount) {
            // Validate the returned price is reasonable
            if (hypeAmount == 0) {
                return _handlePriceFetchFailure("Zero price returned from StakingAccountant");
            }

            // Query kHYPE token decimals and ensure proper scaling to 18 decimals
            uint8 kHYPEDecimals = IERC20Metadata(KHYPE_TOKEN).decimals();
            return UtilLib.scaleToEighteenDecimals(hypeAmount, kHYPEDecimals);
        } catch Error(string memory reason) {
            return _handlePriceFetchFailure(reason);
        } catch {
            return _handlePriceFetchFailure("Unknown error fetching from StakingAccountant");
        }
```
이 보고서 게시를 프론트런하고 더 높은 가격에 자산을 입금하거나 더 낮은 가격에 인출을 요청하는 것은 매우 쉽습니다.

**권장 사항**

외부 오프체인 연결 가격 오라클에서 kHYPE 가격을 확인하는 것을 고려하십시오.

# [L-13] 최대 자본 효율성으로 인해 인출 요청이 되돌려질 수 있음

_해결됨 (Resolved)_

이것은 실제로 구현 버그는 아니지만 심각한 설계 로직 문제입니다. 최고의 자본 효율성에 도달하기 위해 프로토콜은 전략에서 입금된 자산의 매우 높은 비율을 사용해야 합니다. 그러나 프로토콜이 그렇게 하면 풀 계약의 유동성 부족으로 인해 대부분의 경우 `requestWithdrawal` 함수가 되돌려집니다.

`if (!_hasSufficientLiquidity(asset, assetAmount)) revert InsufficientLiquidityForWithdrawal();`

이러한 되돌림을 줄이려면 프로토콜은 입금된 자산의 더 작은 비율을 사용해야 하지만 이번에는 자본 효율성이 감소하며 이는 어떤 프로토콜에도 좋은 일이 아닙니다.

**권장 사항**

일반적으로 이러한 상황은 요청-인출 주기로 처리됩니다. 사용자가 인출을 요청하면 실패하지 않고 요청된 금액을 계산합니다. 나중에 운영자가 해당 기간 내에 전략에서 유동성을 가져오고 사용자는 대기 기간 후 잔액을 청구할 수 있습니다.

이 방식에서는 프로토콜이 인출 경우에만 전략에서 유동성을 가져오고 운영자는 가정하는 대신 인출에 필요한 유동성 양을 항상 알 수 있으므로 자본 효율성이 여전히 매우 높습니다.

# [L-14] 손실 시나리오 후 `assetsAllocatedToStrategies`가 부정확함

_해결됨 (Resolved)_

`deallocateFromStrategy`에서 가능한 시나리오 중 하나는 손실 시나리오입니다. 손실이 있는 경우 인출된 금액은 요청된 금액보다 낮습니다. 그러나 손실이 있는 경우 `min(withdrawn, assetsAllocatedToStrategies[asset])`를 빼기 때문에 감소 금액이 올바르지 않습니다.

```solidity
        if (withdrawn > amount) {
            // Yield scenario: reduce allocation by requested amount, yield is captured separately
            reductionAmount = Math.min(amount, assetsAllocatedToStrategies[asset]);
        } else {
            // Loss or exact scenario: reduce allocation by actual withdrawn amount
            reductionAmount = Math.min(withdrawn, assetsAllocatedToStrategies[asset]);
        }

        // Update tracking - both values reduced by the same amount to maintain consistency
        assetsAllocatedToStrategies[asset] -= reductionAmount;
```

사실 손실이 있는 경우 더 낮은 숫자를 빼고 `assetsAllocatedToStrategies`가 부풀려지므로 여기서도 `min(amount, assetsAllocatedToStrategies[asset])`를 빼야 합니다.

**권장 사항**

if 확인을 제거하고 두 경우 모두 `reductionAmount`를 `Math.min(amount, assetsAllocatedToStrategies[asset])`로 계산하는 것을 고려하십시오.

# [L-15] 사용자 자산 도난 가능성

_인정됨 (Acknowledged)_

Elytra에서 총 tvl 자산은 `deposit track in pool + strategy balance + unstaking valut - reserve asset` 공식을 기반으로 계산됩니다.

전략에는 하나의 `emergencyWithdraw` 함수가 있습니다. 비상 상황에서 관리자는 전략에서 자산을 인출할 수 있습니다. 전략 계약의 `emergencyWithdraw`는 노출되지 않습니다. 테스트 케이스에서 두 가지 가능한 구현이 있습니다.

실제 `emergencyWithdraw`가 버전 1과 유사한 경우 자산을 긴급하게 인출할 때 전략 잔액이 즉시 0이 됩니다. 이로 인해 주가가 하락합니다.

하나의 가격 제한 확인이 없는 경우 악의적인 사용자는 아주 작은 자산을 입금하여 많은 주식을 얻을 수 있습니다.

```solidity
    function emergencyWithdraw(address asset) external returns (uint256) {
        uint256 totalBalance = principalDeposits[asset] + accumulatedYield[asset];
        principalDeposits[asset] = 0;
        accumulatedYield[asset] = 0;
        IERC20(asset).transfer(msg.sender, totalBalance);
        return totalBalance;
    }
    function emergencyWithdraw(address asset) external returns (uint256 amount) {
        amount = IERC20(asset).balanceOf(address(this));
        if (amount > 0) {
            IERC20(asset).transfer(msg.sender, amount);
        }
        return amount;
    }
```

**권장 사항**

비상 모드일 때 사용자가 풀에 입금하도록 허용해서는 안 됩니다.

# [L-16] 사용자가 손실을 피하기 위해 전략의 가능한 슬래싱을 프론트런할 수 있음

_해결됨 (Resolved)_

사용자가 자산을 인출하거나 인출을 요청할 때 현재 총 자산 tvl을 계산하고 주가를 얻습니다.

전략에서 우리는 자산을 검증인에게 스테이킹합니다. 전략은 검증인의 행동에 따라 일부 보상을 받고 어쩌면 잠재적인 슬래싱을 받을 것입니다. 아래 코드는 H_02.t.sol의 동작 시뮬레이션입니다.

사용자는 관련 검증인의 성과를 모니터링하고 가능한 슬래싱이 발생하기 전에 인출하거나 인출을 요청할 수 있습니다. 사용자는 잠재적 손실을 피할 수 있습니다.

**권장 사항**

사용자가 인출을 요청할 때 현재 주가를 기록하십시오. 사용자가 인출을 완료할 때 최신 주가를 확인해야 합니다. 최신 주가가 기록된 주가보다 낮으면 사용자는 약간의 손실을 입어야 합니다.

# [L-17] 지연된 슬래싱 보고로 타이밍 기반 손실 회피 가능

_인정됨 (Acknowledged)_

Elytra 프로토콜은 `reportStrategyLoss()`를 통한 수동 슬래싱 손실 보고 메커니즘을 사용하여 정교한 사용자가 슬래싱 보고를 프론트런하여 손실을 완전히 피할 수 있는 중요한 타이밍 윈도우를 생성하는 반면, 정보를 모르는 사용자는 전체 슬래싱 영향을 받습니다. 이는 일반 사용자로부터 정교한 행위자에게로의 체계적인 부의 이전과 공정한 손실 분배의 완전한 실패로 이어집니다.

검증인 슬래싱 이벤트가 발생하면 전략의 실제 잔액은 즉시 떨어지지만 Elytra의 내부 회계(`assetsAllocatedToStrategies`)는 관리자가 `reportStrategyLoss()`를 수동으로 호출할 때까지 변경되지 않은 상태로 유지됩니다. 이 격차 동안 정교한 사용자는 검증인 슬래싱 이벤트를 모니터링하고 슬래싱 전 비율로 인출할 수 있는 반면 늦은 인출자는 전체 집중된 손실에 직면합니다.

프로토콜에는 이러한 타이밍 이점을 방지할 수 있는 인출 지연, 자동 슬래싱 감지 또는 공정한 대기열 시스템과 같은 보호 메커니즘이 부족합니다.

**권장 사항**

공정한 손실 분배를 보장하기 위해 다음 완화 조치 중 하나 이상을 구현하십시오:

1. **자동화된 슬래싱 감지**: 수동 보고를 전략 잔액을 쿼리하고 불일치가 감지되면 즉시 내부 회계를 업데이트하는 자동화된 감지 메커니즘으로 대체하십시오.

2. **인출 지연**: 슬래싱 이벤트 중 인출 지연을 구현하고, 인출을 처리하기 전에 적절한 손실 회계를 허용하는 유예 기간을 두십시오.

3. **공정한 대기열 시스템**: 고정된 환율로 선입선출 순서로 인출을 처리하여 타이밍 이점을 방지하십시오.

권장되는 접근 방식은 자동 감지(#1)와 인출 지연(#2)을 결합하여 이 공격을 가능하게 하는 타이밍 윈도우를 제거하는 것입니다.

# [L-18] 언스테이킹 금고를 통한 완전한 인출 수수료 우회

_해결됨 (Resolved)_

사용자는 직접 인출 대신 언스테이킹 금고를 사용하여 인출 수수료를 완전히 피할 수 있습니다. `ElytraDepositPoolV1`은 구성 가능한 인출 수수료를 부과하지만 `ElytraUnstakingVaultV1`은 수수료 없이 동일한 인출 기능을 제공합니다.

DepositPool과 unstaking vault 코드를 비교해 보겠습니다.

**DepositPool (수수료 있음):**
```solidity
function withdrawAsset(...) external {
    uint256 fee = Math.ceilDiv(assetAmountBeforeFee * withdrawalFee, BASIS_POINTS);
    assetAmount = assetAmountBeforeFee - fee; // ✅ 수수료 공제
    IERC20(asset).safeTransfer(feeRecipient, fee); // ✅ 프로토콜에 지불
}
```

**UnstakingVault (수수료 없음):**
```solidity
function requestWithdrawal(...) external {
    uint256 assetAmount = _calculateAssetAmount(asset, elyAssetAmount); // ❌ 전체 금액
    withdrawalRequests[requestId] = WithdrawalRequest({
        assetAmount: assetAmount, // ❌ 수수료 공제 없음
    });
}

function completeWithdrawal(...) external {
    IERC20(request.asset).safeTransfer(request.user, request.assetAmount); // ❌ 전체 금액
}
```

사용자는 수수료를 완전히 피하기 위해 `withdrawAsset()` 대신 `requestWithdrawal()`을 호출하기만 하면 됩니다. 언스테이킹 기간이 0으로 설정된 경우 이것은 즉시 수수료 없는 인출이 됩니다.

**영향** -> 모든 사용자가 수수료 없는 언스테이킹 금고 인출로 마이그레이션하여 프로토콜 인출 수수료 수익이 100% 손실됩니다.

**권장 사항**

일관성을 유지하기 위해 언스테이킹 금고에 인출 수수료를 적용하십시오.

# [L-19] `totalValueInProtocol` 계산 시 부적절한 소수점 처리

_해결됨 (Resolved)_

Elytra에서는 기본 HYPE, LST, 스테이블 코인을 포함한 여러 자산을 스테이킹할 수 있도록 지원합니다. 여러 자산을 지원하기 위해 오라클 어댑터를 사용하여 기본 토큰을 기반으로 견적 토큰 가격을 계산합니다.

ADD_ASSET.md에 따르면 오라클 가격은 항상 18 소수점 가격을 반환합니다. 문서와 예를 참조하면 기본 가격, USD 기준 1 UNIT(10**decimal) USDC의 가격을 가져온 다음 `SCALING_FACTOR`를 통해 18 소수점으로 정규화합니다. 반환하는 실제 가격 값은 USD 기준 1e18 금액 USDC의 가격입니다.

kHYPE 오라클의 구현을 확인하면 유사한 구현이 있습니다. 기본 가격, HYPE 기준 1 UNIT(10**decimal) kHYPE의 가격을 가져온 다음 18 소수점으로 정규화합니다.

여기서 문제는 소수점 계산이 올바르지 않다는 것입니다. Hype/LST 시장에서 Hype와 kHYPE는 18 소수점이므로 여기서 문제를 찾을 수 없습니다.

스테이블 코인 시장을 고려해 봅시다. USDC(소수점 6)와 hbUSDT(소수점 18)를 예로 들어보겠습니다.
1. 1 USDC. 1 UNIT USDC는 1e6 USDC와 같으며 18 소수점으로 스케일링해야 하므로 반환 가격은 1e18입니다. `totalAssetTVLScaled * assetPrice = 1e18 * 1e18`.
2. 1 hbUSDT. 1 UNIT hbUSDT(1e18 소수점)는 1e6 USDC와 같으며 스케일링된 가격은 여전히 1e6입니다. `totalAssetTVLScaled * assetPrice = 1e18 * 1e6`.
위의 예를 바탕으로 1 USDC와 1 hbUSDT는 동일한 가치를 가져야 하지만 계산은 다른 값을 제공합니다.

```solidity
    function _getTotalValueInProtocol() private view returns (uint256 totalValueInProtocol) {
@>        uint256 totalAssetTVLScaled = UtilLib.scaleToEighteenDecimals(totalAssetTVL, assetDecimals);
@>        totalValueInProtocol += totalAssetTVLScaled * assetPrice / 1e18;
    }
```

**권장 사항**

현재 가격의 디자인을 바탕으로 1e18 견적 토큰당 실제 기본 토큰 금액을 반환합니다. 값을 계산하기 위해 스케일링된 잔액을 사용해서는 안 됩니다.
