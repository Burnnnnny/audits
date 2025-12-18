# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적인 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 절대 보장할 수 없지만, 경험 많은 연구원들의 블록체인 프로토콜에 대한 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**bgd-labs/aave-v3-origin-pashov** 리포지토리에 대해 **Pashov Audit Group**이 시간 제한 보안 검토를 수행했으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Aave V3.2 소개

Aave 프로토콜은 사용자가 유동성을 공급하여 이자를 얻거나, 차입한 것보다 더 많은 담보로 자산을 빌리거나, 청산에 참여할 수 있는 탈중앙화 시스템입니다. Aave v3는 사용자가 ETH와 WETH와 같은 관련 자산을 그룹화하여 더 높은 위험 설정으로 사용할 수 있는 eMode를 도입했지만, 각 자산은 하나의 eMode에만 속할 수 있어 유연성이 제한되었습니다. Aave v3.2 업데이트는 자산이 더 세부적인 설정으로 여러 eMode의 일부가 될 수 있도록 하는 유동적 eMode(liquid eMode)를 도입합니다. 예를 들어 자산이 eMode 내에서 차입되거나 담보로 사용될 수 있는지 여부와 새로운 구성 옵션은 다양한 eMode에서 자산 사용에 대한 더 세분화된 제어를 제공합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향(Impact)

- 높음(High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간(Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음(Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성(Likelihood)

- 높음(High) - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 도난당하거나 분실될 수 있는 자금의 양에 비해 공격 비용이 비교적 낮습니다.

- 중간(Medium) - 조건부 인센티브가 있는 공격 벡터일 뿐이지만 여전히 발생할 가능성이 비교적 높습니다.

- 낮음(Low) - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적(Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음(High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간(Medium) - 수정해야 함

- 낮음(Low) - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [a4849111a0ce57e3af1ca5cd9a9b8c6a8cdad1e0](https://github.com/bgd-labs/aave-v3-origin-pashov/tree/a4849111a0ce57e3af1ca5cd9a9b8c6a8cdad1e0)

_수정 검토 커밋 해시_ - [dd0bbecb90a53628fe15c076217eac3a7275182f](https://github.com/bgd-labs/aave-v3-origin-pashov/tree/dd0bbecb90a53628fe15c076217eac3a7275182f)

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `AaveProtocolDataProvider`
- `IPoolConfigurator`
- `IPoolDataProvider`
- `EModeConfiguration`
- `ReserveConfiguration`
- `Errors`
- `ConfiguratorLogic`
- `EModeLogic`
- `GenericLogic`
- `LiquidationLogic`
- `ValidationLogic`
- `DataTypes`
- `PoolConfigurator`

### 다룬 공격 벡터

#### 1. 차선의 LTV 및 청산 임계값

##### 설명

공격자는 E-Mode에서 차선의 이율을 활용하기 위해 자산 선택을 조작하여 의도한 것보다 더 유리한 조건을 얻으려고 시도합니다. 새로운 EMode가 예상치 못하게 기본 모드보다 낮은 LTV 또는 청산 임계값을 초래할 수 있습니까? 또는 사용자가 EMode를 활성화할 때 새로운 가정으로 인해 차선의 LTV 및 청산 임계값을 얻습니까?

##### 보호

시스템은 항상 각 자산에 대해 E-Mode와 일반 모드 간에 가장 보수적인(가장 안전한) 매개변수를 사용하여 사용자가 불일치를 악용하여 불공정한 이점을 얻지 못하도록 합니다. 모든 자산에 대해 EMode LT가 항상 비 EMode LT보다 높은 것은 제약 조건이 아니므로 문제가 되지 않습니다. 차선의 LTV 및 청산 임계값의 경우 문서에 명시되어 있지 않지만 의도된 동작입니다.

#### 2. E-Mode 매개변수의 지속성

##### 설명

공격자는 유리한 LTV 및 청산 임계값 매개변수를 고정하기 위해 E-Mode에서 자산 제거를 프론트러닝 하려고 시도합니다.

##### 보호

E-Mode 매개변수는 건강 요소 계산 중에 동적으로 적용됩니다. 자산이 E-Mode에서 제거되더라도 시스템은 해당 자산에 대해 일반 모드 매개변수를 사용하여 오래된 E-Mode 설정의 악용을 방지합니다.

#### 3. 담보 제거 시 즉시 청산

##### 설명

공격자는 담보가 E-Mode에서 제거될 때 전환 기간을 악용하여 잠재적으로 불공정한 청산을 트리거하려고 시도합니다.

##### 보호

시스템은 E-Mode를 전환하거나 E-Mode 매개변수가 변경될 때 건강 요소가 1 이상으로 유지되도록 요구합니다. 이를 통해 전환 중에 포지션이 건전하게 유지되어 즉시 청산을 방지합니다.

#### 4. 차입과 담보의 비대칭적 처리

##### 설명

공격자는 악용 가능한 포지션을 만들기 위해 E-Mode에서 차입된 자산과 담보 간의 처리 차이를 활용하려고 시도합니다.

##### 보호

시스템은 E-Mode 내에서 차입과 담보 모두에 대해 일관된 규칙을 적용합니다. 자산은 E-Mode에서 두 기능 모두에 대해 명시적으로 활성화되어야 하며 가장 보수적인 매개변수가 항상 사용되어 비대칭의 악용을 방지합니다.

#### 5. 새로운 변수로 인한 스토리지 손상 위험

##### 설명

EModeCategory 구조체의 변경, 특히 `isCollateralBitmap` 및 `isBorrowableBitmap`과 같은 새로운 변수의 도입으로 인해 스토리지 손상 위험이 있습니다.

##### 보호

새로운 변수는 이전 스토리지 레이아웃과 완전히 호환되며 데이터 손상이 발생하지 않습니다. 시스템은 Aave가 `EModeCategory` 내의 `priceSource` 필드에 0이 아닌 주소를 설정하지 않았다고 가정하고 이것이 사실로 유지되도록 보장합니다.

#### 6. 관리자 유발 데이터 손상

##### 설명

관리자가 eMode에 대한 담보 인정 비율(LTV), 청산 임계값(LT) 또는 청산 보너스(LB)를 변경할 때 데이터 구조를 무의식적으로 손상시킬 수 있습니다.

##### 보호

관리자는 `isCollateralBitmap` 및 `isBorrowableBitmap` 데이터 필드에 손상을 주지 않고 LTV, LT 또는 LB를 안전하게 조정할 수 있습니다. 시스템 아키텍처는 관리 변경 중에 이러한 필드의 무결성을 보장합니다.

#### 7. E-Mode 카테고리 비활성화

##### 설명

공격자는 쉽게 비활성화할 수 없는 활성화되었지만 원하지 않는 E-Mode 카테고리를 악용하여 잠재적으로 시스템을 자신에게 유리하게 조작하려고 시도합니다.

##### 보호

시스템은 활성 E-Mode 카테고리가 있더라도 안전하게 작동하도록 설계되었습니다. E-Mode 카테고리를 비활성화하는 것은 기존 포지션을 신중하게 고려해야 하는 민감한 작업이므로 의도하지 않은 결과를 방지하기 위해 의도적으로 어렵게 만들었습니다.

#### 8. 위험 관리의 제한된 세분성

##### 설명

공격자는 카테고리 내의 모든 자산에 대한 E-Mode 매개변수의 광범위한 적용을 악용하여 위험이 과소평가되는 에지 케이스를 찾으려고 시도합니다.

##### 보호

E-Mode 매개변수는 카테고리에 광범위하게 적용되지만 시스템은 여전히 개별 자산 매개변수를 고려합니다. E-Mode와 개별 자산 매개변수 사이의 더 보수적인 옵션을 사용하여 특정 자산에 대해 위험이 과소평가되지 않도록 합니다.

#### 9. E-Mode 매개변수 우선순위

##### 설명

공격자는 글로벌 자산 매개변수와 E-Mode 매개변수 간의 상호 작용을 조작하여 의도한 것보다 더 유리한 포지션을 만들려고 시도합니다.

##### 보호

시스템은 글로벌 매개변수와 E-Mode 매개변수 사이의 더 보수적인 옵션을 항상 사용하는 명확한 우선순위 규칙을 가지고 있습니다. 이를 통해 더 엄격한 위험 관리 접근 방식이 항상 적용되어 매개변수 상호 작용의 악용을 방지합니다.

#### 10. EMode 구성 변경 순서

##### 설명

이 현재 업데이트를 고려하지 않는 EMode 구성 변경, 사용자 작업 및 사용자 상태의 모든 가능한 순서를 찾습니다. (예: 이미 활성화된 EMode, EMode 비활성화 원함, 차입 원함, 이미 차입됨, 담보 제공 원함, 이미 담보 제공됨, 청산 원함)

##### 보호

모든 작업이 올바른지 확인하기 위해 이미 검사 및 안전장치가 마련되어 있습니다. 모든 작업은 `ValidationLogic` 내의 동일한 검증에 의존하므로 한 곳에서 안전장치를 업데이트하면 모든 작업이 올바르게 유지됩니다.

#### 11. 차입 중 EMode 변경으로 인한 의도하지 않은 결과

##### 설명

사용자가 자산을 차입하는 동안 EMode를 변경할 경우 의도하지 않은 결과가 있습니까? (예: 즉시 청산 가능, 청산 불가능, 담보가 계산되지 않음 등)

##### 보호

`GenericLogic` 내의 `ValidationLogic` 및 `calculateUserAccountData`가 이미 올바르게 업데이트되어 의도하지 않은 결과가 없습니다.

#### 12. EMode에서 차입된 자산 제거의 영향

##### 설명

`calculateUserAccountData`가 차입된 자산이 EMode에 등록되어 있는지 확인하지 않으므로 차입된 자산이 EMode에서 제거되면 사용자가 여전히 EMode의 혜택을 받을 수 있습니까?

##### 보호

가능하지만 개발자는 이것이 의도된 것이라고 명시합니다. "차입 비활성화는 eMode 내부 및 외부에서 매우 비침해적인 작업입니다.
기존 포지션은 그대로 유지되며 사람들은 증가된 차입을 통해 노출을 늘릴 수 없습니다."

#### 13. 플래시론으로 EMode 제한 우회

##### 설명

사용자가 구성된 EMode 내에 등록되지 않은 자산을 차입하는 등 플래시론을 활용하여 EMode 제한을 우회할 수 있습니까?

##### 보호

사용자가 플래시론을 사용하고 차입된 자산을 상환하지 않기로 선택하면 EMode 검증을 포함하여 차입된 자산이 유효한지 확인하기 위해 `BorrowLogic.executeBorrow`가 이미 마련되어 있습니다.

#### 14. EMode 청산 보너스 조작

##### 설명

EMode에는 별도의 청산 임계값과 청산 보너스가 있습니다. 두 EMode 값을 모두 사용하지 않으면 프로토콜에 심각한 문제가 발생할 수 있습니다. 차입자가 EMode 청산 보너스를 조작하거나 변경할 수 있습니까?

##### 보호

`LiquidationLogic`은 사용자가 `GenericLogic.calculateUserAccountData`와 동일한 EMode 청산 보너스를 사용하는지 여부를 결정할 때 정확히 동일한 상태 확인을 갖습니다. 따라서 둘 다(청산 보너스 및 청산 임계값) 항상 EMode 또는 비 EMode 값을 사용합니다.

#### 15. EMode 업데이트로 인한 범위 내 로직 영향

##### 설명

이 새로운 동작을 고려해야 하지만 현재는 그렇지 않은 기존 작업이 있습니까? 커밋 내에 차이점이 없는 모든 범위 내 로직을 확인합니다.

##### 보호

커밋 내에 차이점이 없는 모든 범위 내 로직은 EMode 구성과 상호 작용하거나 의존하지 않는 경우 이미 `ValidationLogic`을 사용하고 있습니다. 따라서 안전하며 변경이 필요하지 않습니다.

#### 16. EMode가 다른 기능에 미치는 영향

##### 설명

새로운 EMode가 다른 기능(예: 격리된 차입, 격리 모드 등)에 예상치 못한 영향을 미칩니까? 예를 들어 EMode를 활성화하면 실수로 다른 기능이나 모드가 비활성화됩니까?

##### 보호

각 기능에 대한 안전장치 및 검증은 각각의 라이브러리 및 `ValidationLogic` 내에서 독립적이므로 영향이 없습니다.

#### 17. eMode = 0 케이스 처리

##### 설명

eMode = 0인 모든 케이스가 올바르게 처리됩니까? eMode = 0은 기본 케이스이므로 실수로 eMode = 0에 액세스 하여 사용자의 EMode LTV 및 청산 임계값을 사용하는 함수나 로직이 없는지 확인하십시오. 또한 관리자가 eMode = 0을 구성할 수 없는지 확인하십시오.

##### 보호

`ValidationLogic` 및 `GenericLogic` 내의 함수는 이미 eMode = 0을 제외하고(`params.userEModeCategory != 0`을 보장함으로써), `configureEModeCategory`는 이미 eMode = 0에 대한 구성 변경이 제한되도록 보장합니다(`id != 0`을 보장함으로써).

#### 18. 동시 관리자 함수 호출

##### 설명

두 명의 관리자가 동일한 인수로 `setCollateral` 및 `setBorrowable` 함수를 동시에 호출하여 잠재적으로 최종 상태를 손상시킬 수 있습니다.

##### 보호

`setCollateral`과 `setBorrowable`은 모두 멱등 함수입니다. 즉, 동일한 매개변수로 여러 번 호출해도 최종 상태가 변경되지 않습니다. 이는 다른 관리자의 반복적인 트랜잭션으로 인한 의도하지 않은 상태 변경을 방지합니다.

#### 19. EMode 전환으로 인한 건강 요소 감소

##### 설명

사용자가 eMode를 전환하려고 시도하여 자신도 모르게 HF를 감소시켜 즉시 청산을 초래할 수 있습니다.

##### 보호

시스템은 사용자가 eMode를 변경하도록 허용하기 전에 건강 요소(HF)를 확인합니다. 변경 결과로 건강 요소가 1 미만으로 떨어지면 작업이 차단되어 사용자가 이를 수행할 수 없도록 합니다.

#### 20. eMode 마스크 제거 및 데이터 손상 위험

##### 설명

자산 구성 비트맵 내에서 eMode 마스크를 제거하면 데이터 손상이 발생할 수 있습니다.

##### 보호

eMode 마스크는 비트맵에 저장된 나머지 데이터에 영향을 주지 않고 깨끗하게 제거되어 데이터 손상이나 의도하지 않은 부작용이 없도록 보장합니다.

#### 21. EMode 업데이트가 기존 사용자에게 미치는 영향

##### 설명

현재 특정 eMode에 있는 사용자는 업데이트로 인한 문제를 겪거나 변경 사항을 악용할 수 있습니다.

##### 보호

eMode의 사용자는 업데이트의 영향을 받지 않습니다. 사용자 eMode를 추적하는 매핑(`_usersEModeCategory`)은 변경되지 않습니다. 또한 LT 및 LTV 값이 유지되는 한 `calculateUserAccountData` 및 `executeLiquidationCall` 함수는 업데이트 전과 동일하게 작동합니다.

#### 22. `setCollateral()`의 오버플로 위험

##### 설명

`bit` 변수를 설정할 때 `setCollateral()` 함수에서 오버플로가 발생합니다.

##### 보호

이 문제는 reserveIndex가 항상 128 미만인지 확인하여 완화됩니다.

#### 23. `DataTypes` 라이브러리의 스토리지 슬롯 충돌 위험

##### 설명

DataTypes 라이브러리의 스토리지 슬롯 충돌.

##### 보호

스토리지 슬롯은 충돌을 방지하기 위해 적절하게 관리됩니다.

# 결과

# [L-01] `setUserEMode`는 불필요한 검증을 피하기 위해 `categoryId`가 동일한지 확인해야 함

사용자가 `setUserEmode`를 호출할 때 실행은 제공된 `categoryId`가 사용자의 이전 구성과 동일한지 확인하지 않습니다. 이를 위해서는 사용자가 불필요한 건강 요소 검증을 수행하고 추가 가스를 소비해야 합니다. 사용자가 `setUserUseReserveAsCollateral`을 호출하여 준비금을 담보로 구성할 때와 비교하면, 제공된 플래그가 기존 플래그와 동일하면 조기에 반환됩니다.

```solidity
  function executeUseReserveAsCollateral(
    mapping(address => DataTypes.ReserveData) storage reservesData,
    mapping(uint256 => address) storage reservesList,
    mapping(uint8 => DataTypes.EModeCategory) storage eModeCategories,
    DataTypes.UserConfigurationMap storage userConfig,
    address asset,
    bool useAsCollateral,
    uint256 reservesCount,
    address priceOracle,
    uint8 userEModeCategory
  ) external {
    DataTypes.ReserveData storage reserve = reservesData[asset];
    DataTypes.ReserveCache memory reserveCache = reserve.cache();

    uint256 userBalance = IERC20(reserveCache.aTokenAddress).balanceOf(msg.sender);

    ValidationLogic.validateSetUseReserveAsCollateral(reserveCache, userBalance);

>>> if (useAsCollateral == userConfig.isUsingAsCollateral(reserve.id)) return;
    // ...
}
```

`setUserEmode` / `executeSetUserEMode` 내부에서 동일한 패턴을 사용하는 것을 고려하십시오. 제공된 카테고리가 기존 카테고리와 동일하면 조기에 반환하여 불필요한 검증을 피하십시오.

```diff
  function executeSetUserEMode(
    mapping(address => DataTypes.ReserveData) storage reservesData,
    mapping(uint256 => address) storage reservesList,
    mapping(uint8 => DataTypes.EModeCategory) storage eModeCategories,
    mapping(address => uint8) storage usersEModeCategory,
    DataTypes.UserConfigurationMap storage userConfig,
    DataTypes.ExecuteSetUserEModeParams memory params
  ) external {
    ValidationLogic.validateSetUserEMode(
      eModeCategories,
      userConfig,
      params.reservesCount,
      params.categoryId
    );

+   if (usersEModeCategory[msg.sender] == params.categoryId) return;
    usersEModeCategory[msg.sender] = params.categoryId;
    ValidationLogic.validateHealthFactor(
      reservesData,
      reservesList,
      eModeCategories,
      userConfig,
      msg.sender,
      params.categoryId,
      params.reservesCount,
      params.oracle
    );
    emit UserEModeSet(msg.sender, params.categoryId);
  }
```

