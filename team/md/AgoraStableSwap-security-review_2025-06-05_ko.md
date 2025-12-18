# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**amphora-atlas/stable-swap-dev** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Agora StableSwap 소개

Agora StableSwap은 오라클 대신 이자율 조정을 통한 고정 가격 책정을 사용하여 여러 EVM 체인에서 자산을 지원하는 스테이블코인 및 실물 자산(RWA)을 위한 DEX입니다. KYC 제한 액세스, Uniswap 라우터 호환성 및 서브그래프 기반 관리가 특징입니다.

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
- [1dedf62430e2fcf164a807f95c80c12615bad135](https://github.com/amphora-atlas/stable-swap-dev/tree/1dedf62430e2fcf164a807f95c80c12615bad135)

_수정 검토 커밋 해시:_
- [0b424f359bc22a80f681a92440cf5746e5b7dcf8](https://github.com/amphora-atlas/stable-swap-dev/tree/0b424f359bc22a80f681a92440cf5746e5b7dcf8)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `AgoraStableSwapAccessControl`
- `AgoraStableSwapFactory`
- `AgoraStableSwapPair`
- `AgoraStableSwapPairConfiguration`
- `AgoraStableSwapPairCore`

# 발견 사항

# [M-01] 역 기준(inverse base) 및 마이너스 이자율을 사용할 때 잘못된 가격 계산

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

가치가 증가하는 토큰에 대해 `token0OverToken1`을 계산하기 위해 `swapStorage.perSecondInterestRate`를 사용하여 자산의 기준 가격을 조정합니다. 토큰의 순서에 따라 이 값은 양수 또는 음수가 될 수 있습니다.

그러나 이 접근 방식은 결함이 있습니다. 역 가격에 마이너스 이자율을 적용하면 올바른 결과가 산출되지 않기 때문입니다.

다음 예를 고려해 보십시오.
- XYZ는 token0입니다(6 소수점).
- AUSD는 token1입니다(6 소수점).
- XYZ 가격은 $2이고 연간 이자율은 50%입니다.
- token0OverToken1은 0.5e18(1/2 * 1e18)로 설정되고 annualizedInterestRate는 -0.5e18(마이너스 50% 이자율)로 설정됩니다.
- 1년 후 XYZ 가격은 $3이므로 token0OverToken1은 ~0.33e18(1/3 * 1e18)이어야 합니다.
- token0OverToken1은 0.5e18 * (1 - 0.5) = 0.25e18로 계산되는데, 이는 올바르지 않습니다.

## 권장 사항

항상 비 AUSD 토큰의 가격을 기준 가격으로 사용하고 역 가격을 사용하지 마십시오. 이렇게 하면 선형 이자 계산이 깨집니다. 이를 위해서는 AUSD가 항상 쌍의 token0이어야 합니다. AUSD 주소 앞에 8개의 0이 있다는 점을 감안할 때 토큰 순서를 반전하면 대부분의 경우 AUSD가 token0이 되지만 이것이 보장되지는 않습니다. 따라서 코드에서 이를 명시적으로 강제해야 합니다.

# [L-01] 프론트런 `sync()`로 인해 공격자가 준비금 업데이트 전에 토큰을 훔칠 수 있음

준비금을 늘리려면 관리자가 먼저 토큰을 쌍 계약으로 전송한 다음 `sync()`를 호출하여 준비금을 업데이트해야 합니다.

```solidity
    function _sync(
        uint256 _token0Balance,
        uint256 _token1Balance,
        uint256 _token0FeesAccumulated,
        uint256 _token1FeesAccumulated
    ) internal {
        uint112 token0Reserves = (_token0Balance - _token0FeesAccumulated).toUint112();
        uint112 token1Reserves = (_token1Balance - _token1FeesAccumulated).toUint112();

        _getPointerToStorage().swapStorage.reserve0 = token0Reserves;
        _getPointerToStorage().swapStorage.reserve1 = token1Reserves;
        _getPointerToStorage().swapStorage.token0FeesAccumulated = _token0FeesAccumulated.toUint128();
        _getPointerToStorage().swapStorage.token1FeesAccumulated = _token1FeesAccumulated.toUint128();

        // emit event
        emit Sync({ reserve0: token0Reserves, reserve1: token1Reserves });
    }
```

그러나 이러한 작업이 별도의 트랜잭션에서 수행되는 경우 공격자는 준비금이 업데이트되기 전에 스왑을 트리거하여 `sync()` 호출을 프론트런할 수 있습니다. 이로 인해 계약이 사용 가능한 준비금을 잘못 계산하여 공격자가 입력 토큰을 제공하지 않고 입금된 토큰을 훔칠 수 있습니다.

# [L-02] `initialAdmin`이 `msg.sender`인 경우 `initialize`가 관리자를 취소할 수 있음

`Pair` 및 `PairFactory` 계약 모두에서 `initialize` 함수는 `_initialAdminAddress`를 `ACCESS_CONTROL_MANAGER_ROLE`의 관리자로 설정한 다음 일시적으로 `msg.sender`를 `ACCESS_CONTROL_MANAGER_ROLE`에 할당하고 마지막으로 모든 초기화 작업 후 `msg.sender`에서 이 역할을 취소합니다.

```solidity
    function initialize(InitializeParams memory _params) external initializer {
        // Set the admin role
@1>     _initializeAgoraAccessControl({ _initialAdminAddress: _params.initialFactoryAccessControlManager });

        // `proxyAdminAddress` is the proxy admin for the proxy contracts generated with this factory
        _getPointerToFactoryStorage().proxyAdminAddress = _params.initialStableSwapProxyAdminAddress;

        // We temporarily set the manager role to the sender to set the defaultValues on initialization
@2>     _assignRole({ _role: ACCESS_CONTROL_MANAGER_ROLE, _newAddress: msg.sender, _addRole: true });
       --Snipped--
@3>    _assignRole({ _role: ACCESS_CONTROL_MANAGER_ROLE, _newAddress: msg.sender, _addRole: false });
    }
```

`_initialAdminAddress == msg.sender`인 경우 역할이 먼저 할당된 다음 동일한 주소에서 취소됩니다. 이로 인해 `ACCESS_CONTROL_MANAGER_ROLE`을 가진 계정이 없는 상황이 발생합니다.

참고:
동일한 문제가 `AgoraStableSwapPair.initialize()`에서도 발생합니다.

# [L-03] 잘못된 제로 주소 확인이 `token1` 대신 `token0`만 검증함

`computePairDeploymentAddress` 함수에서 제로 주소 확인은 정렬된 두 토큰 주소 중 더 큰 `_token0`에만 적용됩니다. 그러나 `_token1`은 정렬된 쌍에서 더 낮은 주소임이 보장되므로 대신 `_token1 != address(0)`인지 확인해야 합니다. 그렇게 하지 않으면 `address(0)`이 토큰 중 하나로 조용히 통과되어 잠재적으로 잘못된 동작이나 배포 문제가 발생할 수 있습니다.

```solidity
 function computePairDeploymentAddress(
        address _tokenA,
        address _tokenB
    ) public view returns (address _pairDeploymentAddress) {
        // Checks: tokens in swap should be different
        if (_tokenA == _tokenB) revert IdenticalAddresses();
        // Sort the tokens
        (address _token0, address _token1) = sortTokens(_tokenA, _tokenB);

        // Checks: None of the tokens should be zero address
@>      if (_token0 == address(0)) revert ZeroAddress();

        bytes32 _salt = _generateSalt(_token0, _token1);
        // `this` is the factory proxy
        bytes32 _guardedSalt = keccak256(abi.encodePacked(bytes32(uint256(uint160(address(this)))), _salt));
        _pairDeploymentAddress = ICreateX(CREATEX_DEPLOYER).computeCreate3Address({ salt: _guardedSalt });
    }
```

# [L-04] 풀이 Uniswap V2 라우터와 호환되지 않음

`swap()` 함수의 인터페이스는 Uniswap V2 라우터와 일치하지만 이 라우터는 프로토콜의 풀과 함께 사용할 수 없습니다. 여기에는 몇 가지 이유가 있습니다.

- Uniswap의 라우터는 자체 팩토리 계약을 사용하여 쌍을 찾습니다.
- 쌍 주소는 Uniswap의 풀 초기화 코드 해시를 기반으로 계산됩니다.
- 들어오고 나가는 금액의 계산을 위해 쌍의 준비금이 사용됩니다.

# [L-05] 전송 수수료 토큰에 대한 예상치 못한 동작

`swapExactTokensForTokens()` 및 `swapTokensForExactTokens()` 함수는 전송 수수료 토큰(fee on transfer tokens)과 올바르게 작동하지 않습니다. 계산 시 들어오고 나가는 금액이 보내고 받은 금액과 동일하다고 가정하는데, 전송 수수료 토큰의 경우는 그렇지 않기 때문입니다.

전송 수수료 토큰이 들어오는 토큰일 때 트랜잭션이 실패하고, 전송 수수료 토큰이 나가는 토큰일 때 사용자는 예상보다 적은 토큰을 받을 수 있습니다.

지원하도록 의도된 경우 전송 수수료 토큰을 처리하는 특정 함수를 추가하거나 프로토콜이 이를 지원하지 않음을 문서화하는 것을 고려하십시오.

# [L-06] `setFeeBounds` 변경 사항이 적용되지 않아 구매 수수료가 오래됨

**AgoraStableSwapPairConfiguration.sol**의 `setFeeBounds` 함수는 향후 수수료 설정에 대한 허용 범위(`minToken0PurchaseFee`, `maxToken0PurchaseFee` 등)를 업데이트하지만 **현재** 수수료 변수(`swapStorage.token0PurchaseFee`, `swapStorage.token1PurchaseFee`)에 대해 이러한 범위를 소급 적용하지 **않습니다**. 결과적으로 관리자가 처음에 높은 수수료(예: 2%)를 구성하고 현재 수수료를 이 상한선으로 설정한 다음 나중에 범위를 더 낮은 값(예: 1%)으로 강화하면 기존 수수료는 2%로 유지됩니다. 이는 `tokenPurchaseFee`가 항상 구성된 범위 내에 있어야 한다는 의도된 불변성을 위반하여 잠재적으로 과다 청구로 이어질 수 있습니다.

# [L-07] `claimFor`를 통해 공격자가 플래시 스왑을 통해 보상을 훔칠 수 있음

이 프로토콜은 이자 수익 토큰을 지원하도록 설계되었으며, 그 중 일부는 보상을 청구하기 위해 명시적인 호출(예: `claim()` 또는 `harvest()`)이 필요합니다. 그러나 `Pair` 계약에는 이러한 보상을 청구하는 기능이 포함되어 있지 않습니다.
또한 보상 플랫폼에 `claimFor` 기능이 있는 경우 공격자가 이를 악용하여 보상을 훔칠 수 있습니다.
공격자는 `swap()`을 시작하고 `uniswapV2Call()` 콜백 내에서 토큰의 `claimFor(address)` 메서드를 호출하여 보상 토큰을 쌍 계약으로 전송할 수 있습니다. 쌍 계약은 잔액 델타를 기반으로 토큰 입력 금액을 계산하므로 갓 청구된 보상을 공격자의 입력으로 잘못 해석합니다. 그런 다음 공격자는 실제로 입력 토큰을 전송하지 않고 출력 토큰을 받아 사실상 프로토콜의 보상 토큰을 훔칩니다.

```solidity
 function swap(uint256 _amount0Out, uint256 _amount1Out, address _to, bytes memory _data) public nonReentrant {
      --Snipped--

      if (_data.length > 0) {
@1>         IUniswapV2Callee(_to).uniswapV2Call({
                sender: msg.sender,
                amount0: _amount0Out,
                amount1: _amount1Out,
                data: _data
            });
        }
       --Snipped--

            uint256 _finalToken0Balance = IERC20(_swapStorage.token0).balanceOf({ account: address(this) });
            uint256 _finalToken1Balance = IERC20(_swapStorage.token1).balanceOf({ account: address(this) });

            uint256 _previousToken0Balance = _swapStorage.reserve0 + _swapStorage.token0FeesAccumulated;
            uint256 _previousToken1Balance = _swapStorage.reserve1 + _swapStorage.token1FeesAccumulated;

            // Calculate how many tokens were transferred
            _token0In = _finalToken0Balance > (_previousToken0Balance - _amount0Out)
 @2>            ? _finalToken0Balance - (_previousToken0Balance - _amount0Out)
                : 0;
            --Snipped--
  }
```

권장 사항:
- 보상 토큰을 적절하게 처리하기 위해 계약에 명시적 보상 청구 로직을 통합하십시오.
- `swap()`과 같은 민감한 작업 중 외부 보상 청구를 차단하거나 `claimFor` 액세스를 신뢰할 수 있는 역할이나 사용자로 제한하십시오.

# [L-08] `calculatePrice` 선형 모델로 인한 수익률 토큰 가격 오류

`calculatePrice` 함수는 기준 가격, 초당 이자율 및 경과 시간을 기반으로 선형 이자 모델을 사용하여 현재 스왑 가격을 결정합니다.

```solidity
    function calculatePrice(
        uint256 _priceLastUpdated,
        uint256 _timestamp,
        int256 _perSecondInterestRate,
        uint256 _basePrice
    ) public pure returns (uint256 _price) {
        // Calculate the time elapsed since the last price update
        uint256 timeElapsed = _timestamp - _priceLastUpdated;

        // Calculate the price
        _price = _perSecondInterestRate >= 0
            ? ((_basePrice * (PRICE_PRECISION + uint256(_perSecondInterestRate) * timeElapsed)) / PRICE_PRECISION)
            : ((_basePrice * (PRICE_PRECISION - (uint256(-_perSecondInterestRate) * timeElapsed))) / PRICE_PRECISION);
    }
```

그러나 이 접근 방식은 USTB와 같은 수익률 제공 토큰의 이자 복리 특성을 반영하지 않습니다. 연간 이자율을 365로 나누고 시간에 따라 선형적으로 적용함으로써 이 모델은 실제 가격 상승을 과대평가하는데, 이는 복리 효과로 인해 기하급수적이어야 합니다.

이 불일치로 인해 복리 수익률 메커니즘에 의존하는 토큰에 대해 잘못된 가격이 책정되고 빈번한 가격 조정이 필요합니다.

권장 사항:
예를 들어 고정 소수점 연산을 사용하는 지수 성장 공식을 구현하여 복리 이자를 지원하도록 가격 모델을 업데이트하십시오.

# [L-09] 가격이 수수료를 초과하면 `configureOraclePrice`가 악용될 수 있음

`configureOraclePrice` 함수는 거래 쌍에 대한 새 기준 가격 및 연간 이자율을 설정하는 데 사용됩니다.

```solidity
   function configureOraclePrice(uint256 _basePrice, int256 _annualizedInterestRate) public {
        --Snipped--
        _getPointerToStorage().swapStorage.priceLastUpdated = (block.timestamp).toUint40();
        // Effects: Convert yearly APR to per second APR
        _getPointerToStorage().swapStorage.perSecondInterestRate = (_annualizedInterestRate / 365 days).toInt72();
        // Effects: Set the price of the asset
        _getPointerToStorage().swapStorage.basePrice = _basePrice;

        emit ConfigureOraclePrice(_basePrice, _annualizedInterestRate);
    }
```

이 기준 가격은 `calculatePrice` 함수에서 이자율과 마지막 업데이트 이후 경과 시간을 고려하여 시간 경과에 따른 토큰의 유효 스왑 가격을 결정하는 데 사용됩니다.

```solidity
    function calculatePrice(
        uint256 _priceLastUpdated,
        uint256 _timestamp,
        int256 _perSecondInterestRate,
        uint256 _basePrice
    ) public pure returns (uint256 _price) {
        // Calculate the time elapsed since the last price update
        uint256 timeElapsed = _timestamp - _priceLastUpdated;


        // Calculate the price
        _price = _perSecondInterestRate >= 0
            ? ((_basePrice * (PRICE_PRECISION + uint256(_perSecondInterestRate) * timeElapsed)) / PRICE_PRECISION)
            : ((_basePrice * (PRICE_PRECISION - (uint256(-_perSecondInterestRate) * timeElapsed))) / PRICE_PRECISION);
    }
```

공격자는 가격 상승이 예상되는 자산을 구매하여 `configureOraclePrice`를 프론트런하고 가격 업데이트가 적용되도록 한 다음 새로운 더 높은 가격에 매도하여 백런할 수 있습니다. 가격 변동이 총 트랜잭션 및 토큰 스왑 수수료보다 큰 경우 악용은 수익성이 있습니다. 이 취약점은 가격 상승과 하락 모두에 적용됩니다.

권장 사항:
악용 위험을 줄이려면 기준 가격을 즉시 적용하는 대신 여러 블록에 걸쳐 점진적으로 업데이트하는 것을 고려하십시오.

# [L-10] `configureOraclePrice()` 타임스탬프 누락으로 인한 가격 오류

`AgoraStableSwapPairConfiguration` 계약의 `configureOraclePrice()` 함수는 업데이트 시간으로 `block.timestamp`를 사용하여 쌍에 대한 기준 가격과 연간 이자율을 설정합니다.

```solidity
    function configureOraclePrice(uint256 _basePrice, int256 _annualizedInterestRate) public {
        // Checks: Only the price setter can configure the price
        _requireSenderIsRole({ _role: PRICE_SETTER_ROLE });

        ConfigStorage memory _configStorage = _getPointerToStorage().configStorage;

        // Checks: price is within bounds
        if (_basePrice < _configStorage.minBasePrice || _basePrice > _configStorage.maxBasePrice) {
            revert BasePriceOutOfBounds();
        }
        if (
            _annualizedInterestRate < _configStorage.minAnnualizedInterestRate ||
            _annualizedInterestRate > _configStorage.maxAnnualizedInterestRate
        ) revert AnnualizedInterestRateOutOfBounds();

        // Effects: Set the time of the last price update
@>      _getPointerToStorage().swapStorage.priceLastUpdated = (block.timestamp).toUint40();
        // Effects: Convert yearly APR to per second APR
        _getPointerToStorage().swapStorage.perSecondInterestRate = (_annualizedInterestRate / 365 days).toInt72();
        // Effects: Set the price of the asset
        _getPointerToStorage().swapStorage.basePrice = _basePrice;

        // emit event
        emit ConfigureOraclePrice(_basePrice, _annualizedInterestRate);
    }

```

그러나 블록체인 트랜잭션 처리의 비결정적 특성으로 인해 이 접근 방식은 부정확성을 초래합니다.

서명과 실행 사이에 트랜잭션이 상당히 지연될 수 있습니다. 결과적으로 기록된 타임스탬프가 의도한 구성 시간을 반영하지 않아 잘못된 가격 계산으로 이어질 수 있습니다.

또한 이 함수에는 **`deadline`** 메커니즘이 없습니다. 이것이 없으면 오래된 트랜잭션이 훨씬 나중에 실행되어 가격 책정 로직의 정확성과 무결성을 더욱 손상시킬 수 있습니다.

권장 사항:
호출자가 기준 가격 업데이트의 의도된 시간을 지정할 수 있도록 `timestamp` 매개변수를 추가하고, 특정 시간 전에 채굴된 경우에만 트랜잭션이 유효하도록 `deadline` 매개변수를 추가하십시오.

# [L-11] `getAmountsIn`이 나가는 토큰 수수료에 대한 충분한 준비금을 검증하지 못함

`getAmountsIn` 함수는 풀 준비금이 사용자가 요청한 `_amountOut` 값을 충족할 수 있는지 여부만 확인합니다. 그러나 실제 스왑 중에 준비금에서 공제되는 수수료로 징수될 토큰의 추가 부분(`tokenFeesAccumulated`)은 고려하지 않습니다.

```solidity
    function getAmountsIn(uint256 _amountOut, address[] memory _path) public view returns (uint256[] memory _amounts) {
        SwapStorage memory _swapStorage = _getPointerToStorage().swapStorage;
        uint256 _token0OverToken1Price = getPrice();

        // Checks: path length is 2 && path must contain token0 and token1 only
        requireValidPath({ _path: _path, _token0: _swapStorage.token0, _token1: _swapStorage.token1 });
@>        if (_path[1] == _swapStorage.token0 && _amountOut > _swapStorage.reserve0) revert InsufficientLiquidity();
@>        if (_path[1] == _swapStorage.token1 && _amountOut > _swapStorage.reserve1) revert InsufficientLiquidity();

        // Checks: amountOut is greater than 0
        if (_amountOut == 0) revert InsufficientOutputAmount();

        // instantiate return variables
        _amounts = new uint256[](2);
        // set the amountOut
        _amounts[1] = _amountOut;

        // ...
    }
```

이 문제로 인해 `getAmountsIn`에서 잘못된 반환 값이 발생할 수 있으며, DEX이므로 통합자(라우터 또는 애그리게이터 등)에 대한 스왑이 예기치 않게 되돌려질 것으로 예상됩니다.

권장 사항:
준비금이 충분한지 확인하기 위해 `AgoraStableSwapPairCore` 함수가 반환하는 토큰 금액 수수료를 사용하고 이를 `amountOut`에 추가하는 것을 고려하십시오.

# [L-12] 토큰 주소 검증 누락으로 대체 경로를 통한 수수료 유출 허용

`removeTokens` 함수는 제공된 `_tokenAddress`가 `token0` 또는 `token1`과 정확히 일치하는지 확인하지 않습니다. 토큰이 두 개의 주소(예: 프록시 및 보조 진입점 계약)를 통해 상호 작용할 수 있는 설정에서 해당 주소 중 하나는 저장된 `token0`/`token1` 값과 일치하지 않습니다. 비록 동일한 기본 잔액을 제어하더라도 말이죠.

```solidity
File: AgoraStableSwapPairConfiguration.sol
141:     function removeTokens(address _tokenAddress, uint256 _amount) external {
...
152:@>       if (_tokenAddress == _swapStorage.token0 && _amount > _token0Balance - _swapStorage.token0FeesAccumulated) {
153:             revert InsufficientTokens();
154:         }
155:@>       if (_tokenAddress == _swapStorage.token1 && _amount > _token1Balance - _swapStorage.token1FeesAccumulated) {
156:             revert InsufficientTokens();
157:         }
...
159:         // Interactions: transfer tokens from the pair to the token receiver
160:@>       IERC20(_tokenAddress).safeTransfer({ to: _configStorage.tokenReceiverAddress, value: _amount });
```

결과적으로 "별칭" 주소를 사용하는 호출은 수수료 공제 확인을 모두 우회하고 전체 잔액(누적된 수수료 포함)을 빼낼 수 있습니다.

권장 사항:
엄격한 토큰 주소 일치를 강제하십시오.

```solidity
if (_tokenAddress != _swapStorage.token0 && 
    _tokenAddress != _swapStorage.token1) {
    revert InvalidTokenAddress();
}
```

# [L-13] 마이너스 이자율 계산의 언더플로우로 인한 서비스 거부

`calculatePrice` 함수는 `PRICE_PRECISION`에서 누적된 이자율 조정을 빼서 마이너스 초당 이자율을 적용하려고 시도합니다. 그러나 다음 코드가

```solidity
File: AgoraStableSwapPairCore.sol
520:     function calculatePrice(
521:         uint256 _priceLastUpdated,
522:         uint256 _timestamp,
523:         int256 _perSecondInterestRate,
524:         uint256 _basePrice
525:     ) public pure returns (uint256 _price) {
526:         // Calculate the time elapsed since the last price update
527:         uint256 timeElapsed = _timestamp - _priceLastUpdated;
528: 
529:         // Calculate the price
530:         _price = _perSecondInterestRate >= 0
531:             ? ((_basePrice * (PRICE_PRECISION + uint256(_perSecondInterestRate) * timeElapsed)) / PRICE_PRECISION)
532:@>           : ((_basePrice * (PRICE_PRECISION - (uint256(-_perSecondInterestRate) * timeElapsed))) / PRICE_PRECISION);
533:     }
```

음수 값으로 평가되면 uint256 언더플로우가 발생하고 즉시 되돌려집니다. 이것은 사실상 서비스 거부 벡터가 됩니다. `timeElapsed * |_perSecondInterestRate| > PRICE_PRECISION`이 되면 `calculatePrice`에 대한 **모든** 호출이 되돌려져 스왑 또는 종속 로직이 차단됩니다. 다음 시나리오를 고려해 보십시오.

1. **매개변수 설정:**
   * `PRICE_PRECISION = 1e18`
   * `_perSecondInterestRate = -1e15` (초당 -0.000001)
   * `_priceLastUpdated = 0`
   * `timeElapsed = 2000`초
   * `_basePrice = 1e18`

2. **감소 계산:**
   ```
   decay = uint256(-(-1e15)) * 2000 = 1e15 * 2000 = 2e18
   ```
3. **뺄셈 시도:**
   ```
   PRICE_PRECISION - decay = 1e18 - 2e18 → underflow → revert
   ```
4. **결과:** `calculatePrice()`에 대한 모든 호출이 되돌려져 다운스트림 로직이 중단됩니다.

테스트:

```solidity
// SPDX-License-Identifier: ISC
pragma solidity ^0.8.28;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "src/test/BaseTest.sol";

/* solhint-disable func-name-mixedcase */
contract TestOverFlow is BaseTest {
    /// FEATURE: Advanced fee collection scenarios

    address payable public alice = labelAndDeal("alice");
    address payable public bob = labelAndDeal("bob");

    address token0Address;
    address token1Address;

    IERC20 token0;
    IERC20 token1;

    function setUp() public {
        /// BACKGROUND:
        _defaultFactorySetup();
        pairAddress = factory.createPair(
            AgoraStableSwapFactory.CreatePairArgs({
                token0: Constants.Mainnet.USDC_ERC20,
                token1: Constants.Mainnet.AUSD_ERC20,
                minToken0PurchaseFee: 0,
                maxToken0PurchaseFee: 1e18,
                minToken1PurchaseFee: 0,
                maxToken1PurchaseFee: 1e18,
                token0Decimals: 6,
                token1Decimals: 6,
                token0PurchaseFee: 5e14,
                token1PurchaseFee: 1e14,
                minBasePrice: 1e18,
                maxBasePrice: 1e18,
                basePrice: 1e18,
                minAnnualizedInterestRate: -50e18,
                maxAnnualizedInterestRate: 1e18,
                annualizedInterestRate: -31e18
            })
        );
        pair = AgoraStableSwapPair(pairAddress);
        token0Address = pair.token0();
        token1Address = pair.token1();
        token0 = IERC20(token0Address);
        token1 = IERC20(token1Address);   
        _seedErc20({ _tokenAddress: token0Address, _to: pairAddress, _amount: 1e6 * 1e6 });
        _seedErc20({ _tokenAddress: token1Address, _to: pairAddress, _amount: 1e6 * 1e6 });
        pair.sync();
        _seedErc20({ _tokenAddress: pair.token0(), _to: alice, _amount: 100 * (10 ** pair.token0Decimals()) });
        _seedErc20({ _tokenAddress: pair.token1(), _to: alice, _amount: 100 * (10 ** pair.token1Decimals()) });
        _setApprovedSwapperAsWhitelister({ _pair: pair, _newSwapper: alice });
        assertTrue(pair.hasRole(TOKEN_REMOVER_ROLE, tokenRemoverAddress));
        hoax(feeSetterAddress);
        pair.setTokenPurchaseFees(1e16, 1e16); // 1% fee for both tokens
        vm.startPrank(alice);
        uint256 _amount0In = 10 * (10 ** pair.token0Decimals());
        (uint256 _amount1Out,) = pair.getAmount1Out(
            _amount0In,
            pair.getPrice(),
            pair.token1PurchaseFee()
        );
        uint256 _amount1In = _amount1Out;
        (uint256 _amount0Out,) = pair.getAmount0Out(
            _amount1In,
            pair.getPrice(),
            pair.token0PurchaseFee()
        );
        token0.approve(address(pair), type(uint256).max);
        token1.approve(address(pair), type(uint256).max);
        IERC20(pair.token0()).transfer(address(pair), _amount0In);
        pair.swap(0, _amount1Out, alice, "");
        IERC20(pair.token1()).transfer(address(pair), _amount1In);
        pair.swap(_amount0Out, 0, alice, "");
        vm.stopPrank();
        address[] memory toApprove = new address[](1);
        toApprove[0] = address(this);
        hoax(whitelisterAddress);
        pair.setApprovedSwappers(toApprove, true);
        _seedErc20({ _tokenAddress: token0Address, _to: address(this), _amount: 1e18 * 1e6 });
        _seedErc20({ _tokenAddress: token1Address, _to: address(this), _amount: 1e18 * 1e6 });
    }

    function test_OverFlowRevertNegative() public {
        PairStateSnapshot memory _initialPairSnapshot = pairStateSnapshot(pairAddress);
        uint256 initialToken1Balance = token1.balanceOf(address(pair));
        uint256 currentToken1FeesAccumulated = pair.token1FeesAccumulated();
        //
        // 1. calls calculatePrice() which will overflow
        vm.expectRevert();
        pair.getPrice(block.timestamp + 20 days);
    }
}

```

권장 사항:
마이너스 금리 감소가 정밀도 상수를 언더플로우하도록 허용해서는 안 되며, 적절한 범위 검사 또는 캡을 도입해야 합니다. 또한 마이너스 가격은 유효하지 않으므로, 특히 `getPrice`와 같은 공개 함수가 이 동작에 의존하기 때문에 되돌리고 특정 `error()`를 방출해야 합니다.

# [L-14] 고정 쌍 가격 사용 시 차익 거래 허용

이자 수익 자산의 경우 쌍 가격은 고정되며 선형 이자로 조정됩니다. AUSD는 USD에 페깅되도록 되어 있으므로 쌍의 가격이 자산의 실제 가치를 반드시 나타내지는 않을 수 있습니다. 스테이블코인은 1 USD로 평가되는 경향이 있지만 시장 변동으로 인해 이 가치에서 일시적으로 벗어날 수 있습니다. 또한 USTB 및 USYC와 같은 이자 수익 자산은 가격 변동이 이산적일 수 있지만([예: 하루에 한 번](https://usyc.docs.hashnote.com/overview/token-price#price-reporting)), 쌍의 가격은 선형적으로 조정됩니다. 또한 프로토콜은 쌍 가격의 수동 업데이트가 필요하므로 자산의 이자율 변화를 실시간으로 반영하지 못할 수 있습니다.

스왑에 슬리피지가 없고 스왑할 수 있는 토큰 양에 제한이 없다는 점을 감안할 때 사용자는 쌍과 자산의 실제 시장 가격 간의 가격 차이를 잠재적으로 차익 거래하여 과대평가된 자산의 모든 유동성을 인출하고 풀에 단일 토큰만 남겨 프로토콜에 자금 손실을 초래하고 잠재적으로 스테이블코인의 가치를 떨어뜨릴 수 있습니다.

차익 거래 전략은 가격 차이가 스왑 수수료보다 큰 경우에만 수익성이 있으므로 스왑 수수료를 늘리면 차익 거래 공격의 위험을 완화할 수 있습니다. 그러나 여기에는 몇 가지 단점이 있습니다. 1) 이것은 차익 거래 공격의 가능성을 줄일 뿐 제거하지는 않으며, 2) 사용자가 스왑에 대해 더 높은 수수료를 지불해야 하므로 프로토콜 사용을 억제합니다.

권장 사항:
외부 가격 오라클을 사용하여 쌍에 있는 자산의 실제 시장 가격을 결정하십시오.

# [L-15] 오래된 수수료로 인한 준비금 오산, 자산 유출 가능

악의적인 콜백이 `collectFees()`(코드 라인 280)를 통해 온체인 수수료 누적 상태를 변경할 수 있음에도 불구하고 계약은 **메모리** 구조체 `_swapStorage`(코드 라인 334-337)의 수수료 누적기와 준비금을 모두 업데이트합니다. `_swapStorage`는 콜백 *전*(코드 라인 265)에 로드되었으므로 오래된 상태로 유지되며, 라인 334는 오래된 값에 새 구매 수수료를 맹목적으로 추가합니다. 결과적으로 라인 336은 잘못된 수수료 오프셋을 사용하여 `_swapStorage.reserve1`을 계산하여 불변성을 깨고 도난을 가능하게 합니다.

```solidity
File: AgoraStableSwapPairCore.sol
255:     /// @param _data The data to send to the callback
256:     function swap(uint256 _amount0Out, uint256 _amount1Out, address _to, bytes memory _data) public nonReentrant {
... 
264:         // Cache information about the pair for gas savings
265:@>       SwapStorage memory _swapStorage = _getPointerToStorage().swapStorage;
...
283:         // Execute the callback (if relevant)
279:         if (_data.length > 0) {
280:@>           IUniswapV2Callee(_to).uniswapV2Call({
281:                 sender: msg.sender,
282:                 amount0: _amount0Out,
283:                 amount1: _amount1Out,
284:                 data: _data
285:             });
286:         }
287: 
288:         uint256 _token0In;
289:         uint256 _token1In;
290:         {
291:             // Create local scope
292:             // Take snapshot of balances
293:             uint256 _finalToken0Balance = IERC20(_swapStorage.token0).balanceOf({ account: address(this) });
294:             uint256 _finalToken1Balance = IERC20(_swapStorage.token1).balanceOf({ account: address(this) });
295: 
296:@>           uint256 _previousToken0Balance = _swapStorage.reserve0 + _swapStorage.token0FeesAccumulated;
297:@>           uint256 _previousToken1Balance = _swapStorage.reserve1 + _swapStorage.token1FeesAccumulated;
...
333:             // Calculate new fees + reserves in memory struct
334:@>           _swapStorage.token0FeesAccumulated += _token0PurchaseFee.toUint128();
335:@>           _swapStorage.token1FeesAccumulated += _token1PurchaseFee.toUint128();
336:@>           _swapStorage.reserve0 = (_finalToken0Balance - _swapStorage.token0FeesAccumulated).toUint112();
337:@>           _swapStorage.reserve1 = (_finalToken1Balance - _swapStorage.token1FeesAccumulated).toUint112();
338:         }
```

다음 테스트는 공격자가 조작된 `reserve1`(있어야 할 것보다 적은 토큰 포함)을 남길 수 있는 방법을 보여줍니다. 이는 콜백 중에 악의적인 사용자가 `collectFees()`를 호출하지만 해당 변경 사항이 `AgoraStableSwapPairCore#L334-337`의 `swap()` 함수 내에 반영되지 않아 공격자가 준비금 값을 조작할 수 있기 때문입니다. 결국 악의적인 수수료 징수자는 준비금을 빼낼 수 있습니다.

```solidity
// SPDX-License-Identifier: ISC
pragma solidity ^0.8.28;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "src/test/BaseTest.sol";

/* solhint-disable func-name-mixedcase */
contract TestSwapTokensMaliciousCallback is BaseTest {
    /// FEATURE: Advanced fee collection scenarios

    address payable public alice = labelAndDeal("alice");
    address payable public bob = labelAndDeal("bob");

    address token0Address;
    address token1Address;

    IERC20 token0;
    IERC20 token1;

    MaliciousFeeCollectorCallback maliciousContract;

    function setUp() public {
        /// BACKGROUND:
        _defaultFactorySetup();
        pairAddress = factory.createPair(
            AgoraStableSwapFactory.CreatePairArgs({
                token0: Constants.Mainnet.USDC_ERC20,
                token1: Constants.Mainnet.AUSD_ERC20,
                minToken0PurchaseFee: 0,
                maxToken0PurchaseFee: 1e18,
                minToken1PurchaseFee: 0,
                maxToken1PurchaseFee: 1e18,
                token0Decimals: 6,
                token1Decimals: 6,
                token0PurchaseFee: 5e14,
                token1PurchaseFee: 1e14,
                minBasePrice: 1e18,
                maxBasePrice: 1e18,
                basePrice: 1e18,
                minAnnualizedInterestRate: 0,
                maxAnnualizedInterestRate: 1e18,
                annualizedInterestRate: 0
            })
        );
        pair = AgoraStableSwapPair(pairAddress);
        token0Address = pair.token0();
        token1Address = pair.token1();
        token0 = IERC20(token0Address);
        token1 = IERC20(token1Address);   
        _seedErc20({ _tokenAddress: token0Address, _to: pairAddress, _amount: 1e6 * 1e6 });
        _seedErc20({ _tokenAddress: token1Address, _to: pairAddress, _amount: 1e6 * 1e6 });
        pair.sync();
        //
        // 1. Generate some swaps to accumulate fees
        _seedErc20({ _tokenAddress: pair.token0(), _to: alice, _amount: 100 * (10 ** pair.token0Decimals()) });
        _seedErc20({ _tokenAddress: pair.token1(), _to: alice, _amount: 100 * (10 ** pair.token1Decimals()) });
        _setApprovedSwapperAsWhitelister({ _pair: pair, _newSwapper: alice });
        assertTrue(pair.hasRole(TOKEN_REMOVER_ROLE, tokenRemoverAddress));
        hoax(feeSetterAddress);
        pair.setTokenPurchaseFees(1e16, 1e16); // 1% fee for both tokens

        vm.startPrank(alice);
        uint256 _amount0In = 10 * (10 ** pair.token0Decimals());
        (uint256 _amount1Out,) = pair.getAmount1Out(
            _amount0In,
            pair.getPrice(),
            pair.token1PurchaseFee()
        );
        uint256 _amount1In = _amount1Out;
        (uint256 _amount0Out,) = pair.getAmount0Out(
            _amount1In,
            pair.getPrice(),
            pair.token0PurchaseFee()
        );
        token0.approve(address(pair), type(uint256).max);
        token1.approve(address(pair), type(uint256).max);
        IERC20(pair.token0()).transfer(address(pair), _amount0In);
        pair.swap(0, _amount1Out, alice, "");
        IERC20(pair.token1()).transfer(address(pair), _amount1In);
        pair.swap(_amount0Out, 0, alice, "");
        vm.stopPrank();
        //
        // 2. Create a malicious contract that will receive the swap (it is used by malicious fees collector)
        // The malicious collector has permission to call collectFees()
        maliciousContract = new MaliciousFeeCollectorCallback(address(pair));
        hoax(adminAddress);
        pair.assignRole(TOKEN_REMOVER_ROLE, address(maliciousContract), true);
        //
        // 3. Set up this contract as approved swapper
        address[] memory toApprove = new address[](1);
        toApprove[0] = address(this);
        hoax(whitelisterAddress);
        pair.setApprovedSwappers(toApprove, true);
        _seedErc20({ _tokenAddress: token0Address, _to: address(this), _amount: 1e18 * 1e6 });
        _seedErc20({ _tokenAddress: token1Address, _to: address(this), _amount: 1e18 * 1e6 });
    }

    function test_MaliciousCallbackCanManipulateSwap() public {
        PairStateSnapshot memory _initialPairSnapshot = pairStateSnapshot(pairAddress);
        uint256 initialToken1Balance = token1.balanceOf(address(pair));
        uint256 currentToken1FeesAccumulated = pair.token1FeesAccumulated();
        //
        // 5. Perform a swap that will trigger the malicious callback (malicious fee collector)
        uint256 _amount1Out = 10 * (10 ** pair.token1Decimals());
        //
        (uint256 _amount0In, uint256 _token1PurchaseFeeAmount) = pair.getAmount0In(
            _amount1Out,
            pair.getPrice(),
            pair.token1PurchaseFee()
        );
        IERC20(pair.token0()).transfer(address(pair), _amount0In);
        bytes memory data = abi.encode("malicious");
        pair.swap(0, _amount1Out, address(maliciousContract), data);
        //
        // 6. The reserve1 is manipulated by the malicious callback. 100000 are removed from reserve1 which is incorrect
        assertEq(pair.reserve1(), initialToken1Balance - _amount1Out - currentToken1FeesAccumulated - _token1PurchaseFeeAmount - 100000, "Reserve1 should be manipulated by the malicious callback");
        //
        // 7. Malicious feeCollector now is able to collect the fees accumulated from previous swap (step 5) plus the fees subtracted from the reserve1 (via MaliciousFeeCollectorCallback) allowing the drain of tokens
        vm.startPrank(address(maliciousContract));
        pair.collectFees(pair.token1(), _token1PurchaseFeeAmount + 100000);
    }
}


contract MaliciousFeeCollectorCallback {
    address public pair;
    
    constructor(address _pair) {
        pair = _pair;
    }

    // This function will be called during the swap
    function uniswapV2Call(
        address sender,
        uint256 amount0,
        uint256 amount1,
        bytes calldata data
    ) external {
        // Try to manipulate the state by calling removeTokens or collectFees
        // This will affect the swap calculations
        // AgoraStableSwapPair(pair).removeTokens(AgoraStableSwapPair(pair).token1(), 1337);
        AgoraStableSwapPair(pair).collectFees(AgoraStableSwapPair(pair).token1(), 100000);
    }
}
```

권장 사항:
`uniswapV2Call`이 실행되면 `swap()`에서 `_swapStorage`를 다시 로드하십시오.

