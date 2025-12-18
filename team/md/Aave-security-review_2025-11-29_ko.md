# Pashov Audit Group 소개

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 잘 입증된 인물들입니다. 대부분은 공개 콘테스트 보상으로 $100k 이상을 획득했거나, 다회 챔피언이거나, 우리와의 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치하는 데 도움을 준 이 그룹은 절대적인 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

<p>Pashov Audit Group이 <strong>aave-dao/aave-v3-origin</strong> 리포지토리에 대해 시간 제한 보안 검토를 수행했으며, <strong>0x37, unforgiven, 0xl33, Shaka, X0sauce</strong>가 참여하여 <strong>Aave v3.6.0</strong>을 검토했습니다. 총 <strong>1</strong>개의 문제가 발견되었습니다.</p>

# Aave v3.6.0 소개

<p>Aave v3.6.0은 분리된 eMode 구성을 도입하여 자산이 특정 eMode 내에서만 차입 가능하거나 담보로 사용될 수 있도록 허용하는 동시에, aToken, 격리된 담보 및 전송에 대한 자동 담보 활성화를 제거하여 가스 비용을 줄이고 코드를 단순화했습니다. 또한 사용하지 않는 허용량(allowance)을 포기하는 기능을 추가하고, 이벤트 방출을 OpenZeppelin 표준에 맞추고, 특정 레거시 기능을 더 이상 사용하지 않으며, 핵심 계약, 라이브러리 및 주변 로직을 업데이트하여 이러한 개선 사항과 더 세분화된 위험 관리를 지원합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [c4857cf9a78560cc9326c8dc7ee7dbd5b7aed8bc](https://github.com/aave-dao/aave-v3-origin/tree/c4857cf9a78560cc9326c8dc7ee7dbd5b7aed8bc)<br>&nbsp;&nbsp;(aave-dao/aave-v3-origin)

**수정 검토 커밋 해시:**<br>• [4df386b1c57381564a603fc78e0ad5663b567dae](https://github.com/aave-dao/aave-v3-origin/tree/4df386b1c57381564a603fc78e0ad5663b567dae)<br>&nbsp;&nbsp;(aave-dao/aave-v3-origin)

# 다룬 공격 벡터 (Attack vectors covered)

## **담보 검증, LTV0 규칙 및 담보 조작**

### eMode 담보 타이밍 및 LTV0 동작

**설명:**
사용자는 자산이 eMode 전용 LTV0 담보에서 0이 아닌 LTV로 전환되는 타이밍을 악용할 수 있습니다.
eMode 또는 글로벌 모드에서 LTV0 담보를 추가하는 것도 포함됩니다.

**보호:**
`validateUseAsCollateral()`, `getUserReserveLtv()`, `validateSetUserEMode()`는 허용되지 않는 한 대상 eMode에서 LTV0 담보를 활성화하는 것을 방지합니다.

### 비활성화되거나 LTV0인 준비금을 사용하여 건강 요소(HF) 조작

**설명:**
비활성화되거나 LTV0인 준비금을 사용하여 HF를 조작하려는 시도.

**보호:**
준비금은 잔액 > 0일 때만 활성화할 수 있습니다.
잔액 = 0일 때 자동으로 비활성화됩니다.
사용자 공급 = 0이 아닌 한 담보를 비활성화할 수 없습니다.

### LTV0 담보보다 먼저 비 LTV0 담보 인출/전송

**설명:**
LTV0 제약 조건을 피하기 위해 더 안전한 담보를 먼저 인출하거나 전송하려는 시도.

**보호:**
`validateHFAndLtvzero()`는 LTV0 담보가 존재할 때 모든 이동이 제한되도록 보장합니다.

### 유리한 차입 조건을 얻기 위해 LTV 우선순위 악용

**설명:**
eMode/글로벌 LTV 우선순위 불일치를 악용하려는 시도.

**보호:**
모든 경로는 효과적인 LTV 선택을 위해 일관되게 단일 함수를 사용합니다.

## **차입 가능성, 부채 생성, 플래시론 및 eMode 상호 작용**

### 대상 eMode에서 자산을 빌릴 수 없을 때 차입

**설명:**
공격자가 eMode에서 허용되지 않는 자산을 빌리려고 시도합니다.

**보호:**
`validateBorrow()` 및 `validateSetUserEMode()`는 두 모드 모두에 대해 차입 가능 비트맵을 강제합니다.

### eMode 제한의 플래시론 우회

**설명:**
플래시론을 사용하여 차입 가능성 또는 담보 규칙을 우회하려는 시도.

**보호:**
부채 생성 경로는 사용자 활성화 담보만 계산합니다. LTV0 검사는 우회를 방지합니다.

### 차입 중 eMode 변경

**설명:**
공격자가 의도하지 않은 동작을 얻기 위해 차입 중에 eMode 전환을 시도합니다.

**보호:**
모든 차입 자산은 대상 eMode에서 차입 가능한 상태로 유지되어야 합니다. 전환을 완료하기 전에 HF가 다시 계산됩니다.

### eMode 작업을 결합하여 예상치 못한 상태 도달

**설명:**
여러 eMode 전환을 결합하여 유효하지 않은 상태에 도달합니다.

**보호:**
eMode 전환 로직은 항상 대상 상태 일관성을 검증합니다.

## **건강 요소 조작, 청산 임계값, 차입 가능 비트맵**

### eMode LTV 변경 및 즉시 청산 위험

**설명:**
eMode LTV가 변경될 때(LTV>0 → LTV0) 사용자가 즉시 청산될 수 있는지 여부.

**보호:**
기존 포지션은 기득권(grandfathered)을 인정받습니다. 차입자는 변경 사항에 대한 거버넌스 통지를 받습니다.

### 사용자가 eMode를 전환하여 불건전한 HF를 남김

**설명:**
eMode를 변경하면 LT/LTV가 감소하고 HF가 1.0 미만으로 떨어질 수 있습니다.

**보호:**
대상 eMode 구성을 적용한 후 HF가 검증됩니다.
HF가 안전하지 않으면 전환이 실패합니다.

### 차입 가능 비트맵 변경 및 청산 노출 증가

**설명:**
eMode 전용 담보에 대한 차입 가능 비트맵을 비활성화하면 기존 포지션이 일반 모드 청산 임계값 및 LTV 값으로 돌아갈 수 있습니다. 사용자가 더 유리한 eMode 매개변수에 의존한 경우 청산 위험이 증가할 수 있습니다.

**보호:**
이 동작은 프로토콜 거버넌스에 의해 관리됩니다. 활성 차입자는 이러한 변경 사항에 대해 사전 통지를 받으며 부정적인 영향을 피하기 위해 업데이트 전에 포지션을 조정하거나 닫을 것으로 예상됩니다.

## **eMode 대 글로벌 모드 분리 (LT/LTV, 격리 로직)**

### 분리된 eMode/글로벌 LTV 및 LT 구성 불일치

**설명:**
분리로 인해 유효 매개변수가 일치하지 않을 위험이 있습니다.

**보호:**
유효 LTV에 대한 단일 진실 소스; 분리는 모드 간 누출을 방지합니다.

### 분리가 기존 사용자에게 미치는 영향

**설명:**
하위 호환성에 대한 우려.

**보호:**
포지션 및 계산은 업그레이드 후에도 유효하게 유지됩니다.

### 모든 작업에서 eMode 0 및 >0 분리

**설명:**
작업은 eMode 0 및 0이 아닌 eMode에 대해 별도의 LTV/LT를 존중해야 합니다.

**보호:**
`getLiquidationThreshold()` 및 `getLtv()`는 업데이트된 분리 로직을 사용합니다.

### eMode로 전환 시 실수로 글로벌 모드 비활성화 담보 활성화

**설명:**
모드 전환 시 한 모드에서만 허용되는 자산을 실수로 활성화해서는 안 됩니다.

**보호:**
eMode 전환은 대상 구성에 대해 모든 기존 담보를 검증합니다.

### 일반적인 글로벌 대 eMode LT/LTV 분리

**설명:**
분리는 하나의 업데이트가 다른 하나에 영향을 미치지 않도록 보장합니다.

**보호:**
독립적인 구성 경로는 격리를 보장합니다.

## **격리 모드 및 자동 담보 활성화**

### 격리 모드 담보 우회 위험

**설명:**
`ISOLATED_COLLATERAL_SUPPLIER_ROLE` 제거로 우회 우려가 제기됩니다.

**보호:**
`validateAutomaticUseAsCollateral()`은 격리 모드 자산에 대한 자동 활성화를 차단합니다.

### 전송/청산에서 자동 담보 활성화 비활성화로 인한 부작용

**설명:**
전송/청산이 자동 활성화 담보에 의존하는지 여부.

**보호:**
실행 경로는 필요할 때 명시적으로 담보를 설정합니다. 자동 동작에 의존하지 않습니다.

### 토큰 기부를 통한 DOS 또는 조작

**설명:**
기부는 담보 로직을 조작하거나 eMode 전환을 차단할 수 있습니다.

**보호:**
기부는 자동 담보 활성화를 트리거하지 않습니다. eMode 변경을 차단할 수 없습니다.

## **eMode 카테고리, 구성 및 카테고리-0 처리**

### `eModeCategories[0]` 잘못 사용

**설명:**
카테고리 0 인덱스를 사용하는 경우 의도하지 않은 동작의 위험.

**보호:**
검사는 카테고리 0 인덱싱 또는 설정을 방지합니다. 글로벌 모드 데이터는 `_reserves`를 통해 저장됩니다.

### `ltvzeroBitmap`이 eMode 제거에 미치는 영향

**설명:**
새 비트맵이 eMode에서 자산 제거를 차단할 수 있다는 우려.

**보호:**
동작 변경 없음; 비트맵은 기본적으로 꺼져 있음; 제거는 영향을 받지 않음.

## **스토리지 레이아웃, 업그레이드 안전성 및 구조체 변경**

### 새로운 eMode 변경 사항의 스토리지 레이아웃 안전성

**설명:**
구조체 필드를 추가/제거하면 스토리지 충돌이 발생할 수 있습니다.

**보호:**
메모리 전용 구조체는 영향을 받지 않음; `EModeCategory` 필드가 안전하게 추가됨.

### Aave v3 계약 업그레이드 시 스토리지 안전성

**설명:**
일반적인 업그레이드 우려 사항 (프록시 패턴).

**보호:**
`ltvZeroBitmap` 추가는 스토리지 슬롯을 이동시키지 않습니다.

## **이벤트 방출 정확성**

### 토큰에 대한 올바른 이벤트 동작

**설명:**
`transferFrom()`은 더 이상 승인 이벤트를 방출하지 않습니다.

**보호:**
올바른 이벤트 로직이 `approval()`에 유지됨; 의도한 대로 전송 경로에서 제거됨.

# 범위

- `GenericLogic.sol`
- `LiquidationLogic.sol`
- `SupplyLogic.sol`
- `ValidationLogic.sol`
- `DataTypes.sol`
- `Pool.sol`
- `PoolConfigurator.sol`
- `AToken.sol`
- `VariableDebtToken.sol`
- `DebtTokenBase.sol`
- `IncentivizedERC20.sol`

# 결과

# [L-01] 보류 중인 LTV 변경에 대한 이벤트 방출 누락

_해결됨_

`PoolConfigurator`는 `_setReserveLtvzero()` 및 `configureReserveAsCollateral()` 두 곳에서 `_pendingLtv` 매핑을 0으로 설정합니다. 첫 번째 경우에는 새로운 보류 중인 LTV 값(0)으로 `PendingLtvChanged` 이벤트가 방출됩니다.

```solidity
  function _setReserveLtvzero(
(...)
    if (ltvZero) {
      if (currentConfig.getLtv() == 0) return;
      newPendingLtv = currentConfig.getLtv();
      _pendingLtv[asset] = newPendingLtv;
      currentConfig.setLtv(0);
    } else {
      // ltvzero can only be removed on non frozen reserves
      require(!currentConfig.getFrozen(), Errors.ReserveFrozen());
      newLtv = _pendingLtv[asset];
      if (newLtv == 0 || currentConfig.getLtv() != 0) return;
      currentConfig.setLtv(newLtv);
      delete _pendingLtv[asset];
    }
@>  emit PendingLtvChanged(asset, newPendingLtv);
```

그러나 `configureReserveAsCollateral()` 함수는 보류 중인 LTV를 지울 때 이벤트 방출을 놓칩니다.

```solidity
  function configureReserveAsCollateral(
(...)
    if (currentConfig.getFrozen()) {
      _pendingLtv[asset] = ltv;
      newLtv = 0;

      emit PendingLtvChanged(asset, ltv);
    } else {
@>    if (_pendingLtv[asset] != 0) delete _pendingLtv[asset];
      currentConfig.setLtv(ltv);
    }
```

일관성을 유지하고 오프체인 모니터링 및 분석을 위한 정확한 이벤트 로그를 제공하기 위해 `configureReserveAsCollateral()`에서 보류 중인 LTV가 지워질 때 `PendingLtvChanged` 이벤트를 방출하는 것이 좋습니다.

