# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**open-dollar/od-relayer** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Open Dollar 정보

Open Dollar는 Arbitrum을 기반으로 구축된 스테이블코인 프로토콜입니다. 유동성 스테이킹 토큰 및 기타 Arbitrum 네이티브 자산을 Vault에 예치하여 저이자 대출로 OD 스테이블코인을 빌리고 레버리지 포지션을 생성할 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [48224ade8621a8697bb849d7853bd29d3cb7237b](https://github.com/open-dollar/od-relayer/tree/48224ade8621a8697bb849d7853bd29d3cb7237b)

_수정 검토 커밋 해시_ - [453222dff7437edae894df2845600ec97a2baf9d](https://github.com/open-dollar/od-relayer/tree/453222dff7437edae894df2845600ec97a2baf9d)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `CamelotRelayerChild`
- `CamelotRelayerFactory`
- `ChainlinkRelayerChild`
- `ChainlinkRelayerFactory`
- `DenominatedOracleChild`
- `DenominatedOracleFactory`
- `FactoryChild`
- `CamelotRelayer`
- `ChainlinkRelayer`
- `ChainlinkRelayerWithL2Validity`
- `DataConsumerSequencerCheck`
- `DenominatedOracle`
- `Authorizable`
- `Math`

# 발견 사항

# [M-01] `_abs` 함수의 잘못된 절댓값 계산으로 인해 18자리 이상의 소수점을 가진 토큰에 대한 DoS 발생

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`_abs(int256 x)` 함수는 입력 `x`의 절대값을 반환하도록 설계되었습니다. 그러나 `x >= 0 ? x : -x` 삼항 연산이 처리되는 방식의 오류로 인해 결과가 `x`에 다시 할당되지 않습니다. 결과적으로 함수는 부호를 변경하지 않고 단순히 `x`를 `uint256`으로 캐스팅합니다. 이는 `x`가 음수일 때 잘못된 동작으로 이어집니다. 음수 `int256`을 `uint256`으로 캐스팅하면 매우 큰 숫자가 됩니다. 예를 들어 `uint256(-1)`은 `uint256`의 최대값을 반환합니다.

```solidity
  // @notice Return the absolute value of a signed integer as an unsigned integer
  function _abs(int256 x) internal pure returns (uint256) {
    x >= 0 ? x : -x;
    return uint256(x);
  }
```

이 문제는 `ChainlinkRelayer` 및 `CamelotRelayer` 계약 모두에서 사용되는 `_parseResult` 함수에 영향을 미칩니다. `MULTIPLIER`가 음수(18보다 큰 소수를 의미함)일 때 잘못된 `_abs(MULTIPLIER)`는 큰 숫자를 반환하여 10의 거듭제곱으로 올릴 때 오버플로가 발생하고 0으로 나누기 오류가 발생하여 서비스 거부가 발생할 수 있습니다. 이 시나리오는 일반적인 소수 값(8 또는 18)으로 인해 `ChainlinkRelayer`에서는 가능성이 낮지만, quote 토큰의 소수 값을 동적으로 사용하는 `CamelotRelayer`에서는 더 큰 위험을 초래합니다.

- ChainlinkRelayer.sol

```solidity
  function _parseResult(int256 _chainlinkResult) internal view returns (uint256 _result) {
    if (MULTIPLIER == 0) {
      return uint256(_chainlinkResult);
    } else if (MULTIPLIER > 0) {
      return uint256(_chainlinkResult) * (10 ** uint256(MULTIPLIER));
    } else {
      return uint256(_chainlinkResult) / (10 ** _abs(MULTIPLIER));
    }
  }
```

- CamelotRelayer.sol

```solidity
  function _parseResult(uint256 _quoteResult) internal view returns (uint256 _result) {
    if (MULTIPLIER == 0) {
      return _quoteResult;
    } else if (MULTIPLIER > 0) {
      return _quoteResult * (10 ** uint256(MULTIPLIER));
    } else {
      return _quoteResult / (10 ** _abs(MULTIPLIER));
    }
  }
```

## 권장 사항

1. 삼항 연산의 결과가 `x`에 다시 할당되도록 하여 절대값을 올바르게 계산하도록 `_abs` 함수를 수정하십시오. 함수는 다음과 같이 수정해야 합니다.

```diff
function _abs(int256 x) internal pure returns (uint256) {
-   x >= 0 ? x : -x;
+   x = x >= 0 ? x : -x;
    return uint256(x);
}
```

## Open Dollar 의견

_초기 프로토콜 출시를 위한 담보 유형 중 어느 것도 비표준 소수점 금액이나 18자리 이상의 소수점을 가지고 있지 않습니다. 우리는 새로운 담보 유형을 추가할 때 이러한 조사 결과를 고려할 것이며 필요한 경우 비표준 소수점 금액과 호환되는 새로운 릴레이어 또는 토큰 래퍼를 만들 것입니다._

# [M-02] `ChainlinkRelayer`의 `read` 함수에서 오해의 소지가 있는 유효성 검사

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`ChainlinkRelayer` 계약의 `read` 함수는 Chainlink 피드에서 최신 가격을 가져와 반환하고 가격이 유효하지 않은 것으로 간주되면 되돌려지도록(revert) 의도되었습니다. 그러나 현재 구현에서는 유효성 검사에서 논리 연산자를 오용하여 잠재적으로 잘못된 동작을 허용합니다. 함수는 OR(`||`) 조건을 사용하여 가격 `_aggregatorResult`가 0이 아니거나 데이터가 최신인지 확인합니다. 이를 통해 함수는 데이터가 오래되지 않은 경우 0 가격을 반환하고 반대로 0이 아닌 경우 오래된 가격을 반환할 수 있습니다. 두 시나리오 모두 의도하지 않을 가능성이 높으며 잘못된 결과가 유효한 것으로 간주될 수 있습니다.

자매 함수 `getResultWithValidity`는 유사한 검사에 AND(`&&`) 조건을 사용하는데, 이는 데이터가 유효한 것으로 간주되려면 두 조건(0이 아님 및 최신)이 모두 참이어야 함을 시사합니다.

## 권장 사항

`read` 함수의 유효성 검사를 OR(`||`) 대신 AND(`&&`) 연산자를 사용하도록 수정하여 `getResultWithValidity`에서 사용된 논리적 조건과 일치시키십시오. 이 변경은 0이 아니고 최신인 가격만 유효한 것으로 간주되도록 합니다. 함수의 수정된 부분은 다음과 같아야 합니다.

```diff
  function read() public view virtual returns (uint256 _result) {
    // Fetch values from Chainlink
    (, int256 _aggregatorResult,, uint256 _aggregatorTimestamp,) = chainlinkFeed.latestRoundData();

    // Revert if price is invalid
-    require(_aggregatorResult != 0 || _isValidFeed(_aggregatorTimestamp), 'InvalidPriceFeed');
+    require(_aggregatorResult != 0 && _isValidFeed(_aggregatorTimestamp), 'InvalidPriceFeed');
    // Parse the quote into 18 decimals format
    _result = _parseResult(_aggregatorResult);
  }
```

## Open Dollar 의견

_권장되는 변경 사항을 구현했습니다._

# [L-01] 승인된 사용자가 승인에서 자신을 제거할 수 있음

Authorizable.sol에는 승인된 사용자가 승인된 계정의 승인을 제거할 수 있는 `removeAuthorization()` 함수가 있습니다.

```
  function removeAuthorization(address _account) external virtual isAuthorized {
    _removeAuthorization(_account);
  }
```

승인된 사용자가 자신을 제거할 수 없도록 확인하는 절차가 없습니다. 승인된 사용자가 실수로 자신을 제거하고 승인된 사용자가 더 이상 없으면 `addAuthorization()`을 호출할 권한이 있는 사람이 필요하므로 승인을 다시 받을 수 있는 방법이 없습니다.

`isAuthorized` 수정자 아래에 있으므로 릴레이어 배포에 영향을 미칩니다.

```
  function deployChainlinkRelayer(
    address _aggregator,
    uint256 _staleThreshold
> ) external isAuthorized returns (IBaseOracle _chainlinkRelayer) {
    _chainlinkRelayer = IBaseOracle(address(new ChainlinkRelayerChild(_aggregator, _staleThreshold)));
    relayerId++;
```

승인된 사용자가 자신의 승인을 철회할 수 없고 항상 승인된 사람이 있는지 확인하십시오.

## Open Dollar 의견

_승인된 계정이 제거될 수 있고 추가 수정이 방지될 수 있음을 알고 있습니다. 승인이 손실되면 언제든지 새 팩토리를 배포할 수 있으므로 프로토콜에 위험은 없습니다._

# [L-02] FactoryChild에서 `onlyFactory` 수정자가 사용되지 않음

RelayerFactory에서 자식 계약을 생성할 때 자식 계약은 FactoryChild를 상속합니다.

```
contract ChainlinkRelayerChild is ChainlinkRelayer, FactoryChild {
  // --- Init ---

  /**
   * @param  _aggregator The address of the aggregator to relay
   * @param  _staleThreshold The threshold in seconds to consider the aggregator stale
   */
  constructor(address _aggregator, uint256 _staleThreshold) ChainlinkRelayer(_aggregator, _staleThreshold) {}
}
```

이 FactoryChild는 onlyFactory() 수정자를 가진 추상 계약이지만 프로토콜에서는 사용되지 않습니다.

```
  /// @dev Verifies that the contract is being deployed by a contract address
  constructor() {
    factory = msg.sender;
  }

  // --- Modifiers ---

  ///@dev Verifies that the caller is the factory
  modifier onlyFactory() {
    require(msg.sender == factory, 'CallerNotFactory');
    _;
  }
```

새 AlgebraPool을 배포할 때 AlgebraPoolDeployer에서 수행되는 방식과 같이 수정자를 사용하는 것을 고려하십시오.

```
 /// @inheritdoc IAlgebraPoolDeployer
  function deploy(
    address dataStorage,
    address _factory,
    address token0,
    address token1
> ) external override onlyFactory returns (address pool) {
    parameters = Parameters({dataStorage: dataStorage, factory: _factory, token0: token0, token1: token1});
    pool = address(new AlgebraPool{salt: keccak256(abi.encode(token0, token1))}());
  }
}
```

## Open Dollar 의견

_새 팩토리 자식을 배포하는 것은 보호된 기능이 아니기 때문에 여기에서 onlyFactory 수정자를 구현해야 한다고 생각하지 않습니다._

# [L-03] CamelotRelayer.read()는 새로 생성된 풀에 대해 되돌려짐(revert)

CamelotRelayer.sol에서 `getResultWithValidity()`는 `DataStorageLibrary.consult`를 호출하고 `QUOTE_PERIOD`를 제공하여 토큰 가격을 얻습니다.

```
function getResultWithValidity() external view returns (uint256 _result, bool _validity) {
    // TODO: add catch if the pool doesn't have enough history - return false

    // Consult the query with a TWAP period of QUOTE_PERIOD
    int24 _arithmeticMeanTick = DataStorageLibrary.consult(algebraPool, QUOTE_PERIOD);
```

이 `QUOTE_PERIOD`는 프로토콜이 가격 조작 공격을 방지하기 위해 사용하려는 TWAP 기간(예: 15분(900))입니다.

풀이 새로 생성된 경우 `QUOTE_PERIOD`가 작동하지 않고 `getResultWithValidity()`가 되돌려집니다. TODO 주석에서 알 수 있듯이 함수의 의도는 되돌리는 대신 false를 반환하는 것으로 보입니다.

새로 생성된 풀에 대해 `false` 유효성을 반환하도록 try/catch로 함수를 래핑하는 것을 고려하십시오.

## Open Dollar 의견

_우리는 새로운 오라클이 프로토콜에 추가되거나 어떤 방식으로든 프로덕션에 사용되기 전에 유효한 가격 피드를 반환하도록 할 것입니다. 유효한 가격 피드가 없는 새로 생성된 풀은 프로토콜에서 사용할 의도가 없습니다._

# [L-04] 소수점이 높은 토큰을 사용하는 `consult` 함수에서 되돌리기(Revert)

`DataStorageLibrary`의 `consult` 함수는 `AlgebraPool`과 상호 작용하여 지정된 기간을 기반으로 시간 가중 평균 틱을 검색합니다. `secondAgos` 배열을 구성하여 과거 틱 누적을 가져옵니다. 그러나 이 함수는 18개 이상의 소수점을 가진 토큰과 상호 작용할 때 오류(EvmError: 수학 오류에 대해 주로 반환되는 InvalidFEOpcode)와 함께 되돌릴 수 있습니다. 되돌리기는 대수 풀(algebra pool)의 `getTimepoints` 함수가 비정상적으로 높은 소수점 카운트를 가진 토큰에 필요한 정밀도를 처리하지 못할 수 있기 때문에 발생합니다. 특히 특정 기간 동안의 틱 값 차이를 계산할 때 그렇습니다.

대수 풀(algebra pool)과 관련된 작업을 시도하기 전에 토큰의 소수점 카운트가 18 이하인지 확인하십시오.

## Open Dollar 의견

_초기 프로토콜 출시를 위한 담보 유형 중 어느 것도 비표준 소수점 금액이나 18자리 이상의 소수점을 가지고 있지 않습니다. 우리는 새로운 담보 유형을 추가할 때 이러한 조사 결과를 고려할 것이며 필요한 경우 비표준 소수점 금액과 호환되는 새로운 릴레이어 또는 토큰 래퍼를 만들 것입니다._

# [L-05] `_parseResult`에서 음수 가격의 안전하지 않은 캐스팅

`_parseResult` 함수는 Chainlink 피드 결과에 대해 `int256`에서 `uint256`으로의 변환을 수행합니다. Chainlink 가격 피드는 특정 조건에서 음수 가격을 반환할 수 있습니다. 현재 이 함수는 `uint256`으로 변환하기 전에 가격의 부호를 확인하지 않습니다. 음수 `int256`을 `uint256`으로 캐스팅하면 `uint256`의 최대값에 가까운 매우 큰 숫자가 되어 시스템에서 예기치 않은 동작이나 재정적 부정확성을 초래할 수 있으므로 잘못된 결과가 발생할 수 있습니다.

잘못된 데이터 처리 및 오버플로와 관련된 잠재적 취약성을 방지하려면 캐스팅하기 전에 음수 값 확인을 포함하도록 `_parseResult` 함수를 수정하는 것이 좋습니다. 음수 가격이 감지되면 함수가 되돌리거나(revert) 적절하게 처리해야 합니다. 제안된 구현은 다음과 같습니다.

```diff
function _parseResult(int256 _chainlinkResult) internal view returns (uint256 _result) {
+    require(_chainlinkResult >= 0, "Negative price value not allowed");

    if (MULTIPLIER == 0) {
        return uint256(_chainlinkResult);
    } else if (MULTIPLIER > 0) {
        return uint256(_chainlinkResult) * (10 ** uint256(MULTIPLIER));
    } else {
        return uint256(_chainlinkResult) / (10 ** _abs(MULTIPLIER));
    }
}
```

## Open Dollar 의견

_권장되는 변경 사항을 구현했습니다._

# [L-06] 연산 순서로 인한 `DenominatedOracle`의 정밀도 손실

`DenominatedOracle`의 `getResultWithValidity` 및 `read` 함수는 반전된(inverted) 가격 소스를 처리할 때 곱셈 전에 나눗셈을 수행합니다. 이로 인해 결과 값의 정밀도가 손실됩니다. 구체적으로, 가격 피드가 직접 USD/ETHT를 제공할 때 ETH/USDT의 가치를 계산하는 것과 같이 반전이 필요한 시나리오에서 문제가 발생합니다. 곱하기 전에 나누면 후속 곱셈으로 상쇄되기 전에 정수 나눗셈이 소수 부분을 잘라내므로 계산의 정밀도가 손실됩니다.

예를 들어 약 3000\*1e18의 ETH/USDT 가격의 경우 반전된 가격에 대한 이상적인 계산은 가능한 한 많은 정밀도를 보존해야 합니다.
10^18 x 10^18/ (3000 x 10^18) = `333 333 333 333 333.333333...`
그러나 현재 구현은 먼저 나눗셈을 수행하여 결과를 자르고 소수 정밀도 손실을 초래합니다.
따라서 대신 `333 333 333 333 333`을 사용합니다.

`INVERTED`가 참일 때 `_priceSourceValue`로 나누기 전에 `_denominationPriceSourceValue`를 곱하도록 `getResultWithValidity` 함수를 수정하십시오. 이 조정은 결과 크기를 잠재적으로 증가시킬 수 있는 곱셈이 나눗셈 이전에 수행되도록 하여 더 많은 정밀도를 보존합니다. 함수의 수정된 부분은 다음과 같습니다.

```solidity
function getResultWithValidity() external view returns (uint256 _result, bool _validity) {
    (uint256 _priceSourceValue, bool _priceSourceValidity) = priceSource.getResultWithValidity();
    (uint256 _denominationPriceSourceValue, bool _denominationPriceSourceValidity) =
      denominationPriceSource.getResultWithValidity();

    if (INVERTED) {
      if (_priceSourceValue == 0) return (0, false);
      _result = WAD.wmul(_denominationPriceSourceValue).wdiv(_priceSourceValue);
    } else {
      _result = _priceSourceValue.wmul(_denominationPriceSourceValue);
    }

    _validity = _priceSourceValidity && _denominationPriceSourceValidity;
}
```

`read` 함수도 마찬가지입니다.

## Open Dollar 의견

_권장되는 변경 사항을 구현했습니다._

# [L-07] Solidity 0.7.6으로 인한 `wmul` 함수의 잠재적 정수 오버플로

두 값을 곱한 다음 미리 정의된 상수(`WAD`)로 나누는 데 사용되는 계약의 `wmul` 함수는 정수 오버플로에 취약합니다. 이는 주로 계약이 버전 0.8 이상처럼 오버플로 및 언더플로를 자동으로 확인하지 않는 Solidity 버전 0.7.6으로 컴파일되기 때문입니다. 이 함수에서 `_x`와 `_y`가 모두 충분히 큰 경우 `_x * _y` 곱셈이 `uint256`의 최대값을 초과하여 포착되지 않은 오버플로가 발생할 수 있습니다.

Solidity 버전 0.8.0 이상으로 업그레이드하는 것을 고려하십시오. 이러한 버전에는 자동 오버플로 및 언더플로 검사가 포함되어 있어 수동 검사로 인한 추가 가스 비용 없이 이 문제를 근본적으로 방지할 수 있습니다. SafeMath 라이브러리를 사용하여 문제를 예방하는 것도 가능합니다.

## Open Dollar 의견

_우리는 Open Zeppelin 라이브러리 SafeMath의 권장 접근 방식을 사용하여 오버플로를 방지하는 솔루션을 구현했습니다.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.3/contracts/math/SafeMath.sol_

_모든 wmul 함수 호출은 이제 오버플로를 확인하는 Math 라이브러리를 사용합니다._

# [L-08] ChainlinkRelayer는 getResultWithValidity 및 read 함수에 대해 다른 조건을 가짐

`ChainlinkRelayer.getResultWithValidity` 함수는 양수 가격만 허용합니다: `_validity = _aggregatorResult > 0 && _isValidFeed(_aggregatorTimestamp);`.

`ChainlinkRelayer.read` 함수는 음수 가격을 허용합니다: `require(_aggregatorResult != 0 || _isValidFeed(_aggregatorTimestamp), 'InvalidPriceFeed');`.

이 두 함수는 동일하므로 검사가 동일해야 하며 두 경우 모두 음수 가격이 허용되어서는 안 됩니다.

## Open Dollar 의견

_변경이 필요하지 않습니다. 음수 값은 \_parseResult 중에 확인되므로 반환된 가격은 음수일 수 없습니다._

# [L-09] ChainlinkRelayerFactory는 ChainlinkRelayerWithL2Validity를 배포할 수 없음

ChainlinkRelayerWithL2Validity는 ChainlinkRelayer를 확장하고 L2 시퀀서 검사를 제공합니다. 현재 ChainlinkRelayerFactory로는 배포할 수 없으며 ChainlinkRelayerFactory는 ChainlinkRelayerChild(즉, ChainlinkRelayer)만 배포할 수 있으므로 별도의 팩토리가 없습니다.

```solidity
  function deployChainlinkRelayer(
    address _aggregator,
    uint256 _staleThreshold
  ) external isAuthorized returns (IBaseOracle _chainlinkRelayer) {
    _chainlinkRelayer = IBaseOracle(address(new ChainlinkRelayerChild(_aggregator, _staleThreshold)));
    relayerId++;
    relayerById[relayerId] = address(_chainlinkRelayer);
    emit NewChainlinkRelayer(address(_chainlinkRelayer), _aggregator, _staleThreshold);
  }
```

## Open Dollar 의견

_권장되는 변경 사항을 구현했습니다._

# [L-10] ChainlinkRelayerWithL2Validity.getResultWithValidity는 시퀀서가 다운되었을 때 되돌리면 안 됨

ChainlinkRelayerWithL2Validity.getResultWithValidity 함수는 결과가 유효하지 않을 때 되돌리지 않도록 설계되었으며 유효성으로 `false`를 반환해야 합니다. 그러나 시퀀서가 다운된 경우 함수가 되돌려져 통합을 되돌릴 수 있습니다.

```solidity
function getResultWithValidity() public view override returns (uint256 _result, bool _validity) {
    require(getSequencerFeedValidation(), 'SequencerDown');
    (_result, _validity) = super.getResultWithValidity();
  }
```

## Open Dollar 의견

_권장되는 변경 사항을 구현했습니다._
