# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**TokenLogic-com-au/asset-manager** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# Arbitrum Strategy Manager 소개

Arbitrum Strategy Manager는 Aave V3에서 Arbitrum 재단의 wstETH 보유를 관리하는 스마트 컨트랙트로, 예치, 인출 및 보상 청구를 통해 수익 창출을 가능하게 합니다. 여기에는 Aave의 유동성 조건에 따라 노출을 조정하기 위한 자동 위치 확장 및 비상 기능이 포함됩니다.

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

_검토 커밋 해시_ - [bf3b2f80c56de0bacf9bfee54527f8dce1883576](https://github.com/TokenLogic-com-au/asset-manager/tree/bf3b2f80c56de0bacf9bfee54527f8dce1883576)

_수정 검토 커밋 해시_ - [af9e8308b1a036a97cc6faf5de81a159831454a1](https://github.com/TokenLogic-com-au/asset-manager/tree/af9e8308b1a036a97cc6faf5de81a159831454a1)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `ArbitrumStrategyManager`

# 발견 사항

# [L-01] `depositIntoAaveV3()`의 임계값 확인 누락으로 인한 축소(scale-down) 발생 가능성

`depositIntoAaveV3()` 함수는 `CONFIGURATOR_ROLE`을 가진 주소가 Aave V3 풀에 arbitrary 양의 `wstETH`를 예치할 수 있도록 허용합니다. 그러나 결과 포지션이 `scaleDown()` 함수에서 비상 축소를 트리거하는 상한인 `_maxPositionThreshold + BPS_BUFFER`를 초과하는지 확인하지 않습니다.

이 누락으로 인해 `CONFIGURATOR_ROLE`이 실수로 임계값 제한을 즉시 준수하지 않게 만드는 금액을 예치할 수 있습니다. 결과적으로 예치 직후 자동 축소가 트리거되어 불필요한 가스 사용과 운영 오버헤드가 증가할 수 있습니다.

```solidity
    function depositIntoAaveV3(
        uint256 amount
    ) external onlyRole(CONFIGURATOR_ROLE) {
        require(amount > 0, InvalidZeroAmount());

        IERC20(WST_ETH).forceApprove(_aaveV3Pool, amount);
        IPool(_aaveV3Pool).supply(WST_ETH, amount, address(this), 0);

        emit DepositIntoAaveV3(amount);
    }
```

권장 사항:
`depositIntoAaveV3()`에 결과 포지션 비율이 `_maxPositionThreshold + BPS_BUFFER` 아래로 유지되도록 확인하는 로직을 추가하십시오.

# [L-02] `updateMaxPositionThreshold` 이후 `scaleDown`에 대한 자동 확인 부재

`ArbitrumStrategyManager` 계약의 `updateMaxPositionThreshold` 함수는 구성자(configurator) 역할이 최대 포지션 임계값을 업데이트할 수 있도록 허용합니다.

```solidity
        uint256 old = _maxPositionThreshold;
        _maxPositionThreshold = newThreshold;
```

그러나 **임계값이 업데이트된 후 `scaleDown` 함수에 대한 확인을 자동으로 트리거하는 메커니즘이 현재 마련되어 있지 않습니다.** 이러한 간과는 포지션 비율이 새로 설정된 임계값을 초과하는 상황으로 이어질 수 있으며, 필요한 조정이 제때 이루어지지 않아 계약이 위험에 노출될 수 있습니다.

# [L-03] 하드코딩된 Aave V3 풀 주소가 향후 업그레이드와 일치하지 않을 수 있음

`ArbitrumStrategyManager` 계약에서 `_aaveV3Pool` 변수는 `immutable` 주소로 정의되며 배포 중에 하드코딩된 값 `0x794a61358D6845594F94dc1DB02A252b5b4814aD`로 설정됩니다.

```solidity
    /// @dev Address of the Aave V3 Pool
    address internal immutable _aaveV3Pool;
```

```solidity
    address public constant AAVE_V3_POOL =
        0x794a61358D6845594F94dc1DB02A252b5b4814aD;
    address public constant MERKL_DISTRIBUTOR =
        0x3Ef3D8bA38EBe18DB133cEc108f4D14CE00Dd9Ae;

    function run() public {
        vm.startBroadcast();

        manager = new ArbitrumStrategyManager(
            ADMIN,
            AAVE_V3_POOL,
            TREASURY,
            MERKL_DISTRIBUTOR,
            HYPERNATIVE
        );

        vm.stopBroadcast();
    }
```

그러나 Aave V3 풀 주소를 하드코딩하는 것은 위험을 초래합니다:

`AaveV3 pool` 주소는 일반적으로 `Aave Addresses Provider`를 통해 관리되며, 필요한 경우 풀 주소를 업데이트할 수 있습니다.

[링크](https://arbiscan.io/address/0xa97684ead0e402dc232d5a977953df7ecbab3cdb#code)
[링크](https://aave.com/docs/developers/smart-contracts/pool-addresses-provider)

> `setAddress`는 주어진 id에 저장된 프로토콜 계약의 주소를 설정하여 주소 맵에 저장된 주소를 대체합니다.

```solidity
  /// @inheritdoc IPoolAddressesProvider
  function setAddress(bytes32 id, address newAddress) external override onlyOwner {
    address oldAddress = _addresses[id];
    _addresses[id] = newAddress;
    emit AddressSet(id, oldAddress, newAddress);
  }
```

```solidity
  /// @inheritdoc IPoolAddressesProvider
  function getPool() external view override returns (address) {
    return getAddress(POOL);
  }
```

`setAddress`는 신중하게 호출되어야 하지만, 호출되어 주소 맵의 현재 풀 주소를 강제로 교체할 가능성은 여전히 존재합니다.

현재 `pool` 주소는 하드코딩되어 있어 주소 업그레이드를 따라가지 못할 수 있습니다.

주소를 하드코딩하면 계약이 오래된 풀과 상호 작용하여 기능 및 보안에 잠재적인 문제가 발생할 수 있습니다.

`hardcoding` 대신 `IPoolAddressesProvider(addressProviderAddress).getPool()`을 사용하여 `pool`을 참조하는 접근 방식을 권장합니다.

# [L-04] 최적화되지 않은 역할 ID 구현

`ArbitrumStrategyManager` 계약은 `CONFIGURATOR_ROLE` 및 `EMERGENCY_ACTION_ROLE`에 대해 문자열 리터럴을 사용하여 역할 식별자를 공개 상수로 정의합니다.

```solidity
    /// @notice Returns the identifier of the Configurator Role
    /// @return The bytes32 id of the Configurator role
    bytes32 public constant CONFIGURATOR_ROLE = "CONFIGURATOR";

    /// @notice Returns the identifier of the Emergency Action Role
    /// @return The bytes32 id of the Emergency Action role
    bytes32 public constant EMERGENCY_ACTION_ROLE = "EMERGENCY_ACTION";
```

`AccessControl`에 대한 `OpenZeppelin` [문서](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9393147db73ae6261b67cb03003370e9a7fa2448/contracts/access/AccessControl.sol#L17C1-L23C7)에 따르면, 역할 식별자는 고유성과 보안을 보장하기 위해 `keccak256` 해시 함수를 사용하여 정의되어야 합니다. 현재 구현은 이 모범 사례를 따르지 않습니다.

````
 * Roles are referred to by their `bytes32` identifier. These should be exposed
 * in the external API and be unique. The best way to achieve this is by
 * using `public constant` hash digests:
 *
 * ```solidity
 * bytes32 public constant MY_ROLE = keccak256("MY_ROLE");
 * ```
````

# [L-05] Aave 인센티브 청구 누락으로 인한 미청구 보상 발생

`ArbitrumStrategyManager` 계약은 수익을 얻기 위해 `wstETH`를 Aave V3 풀에 공급합니다. 그러나 **Aave의 `RewardsController`**로부터 인센티브 보상을 청구하는 기능을 구현하지 않습니다. 결과적으로 Aave에서 발생한 LP 인센티브는 청구되지 않은 상태로 Aave 풀에 묶여 있으며 전혀 활용되지 않습니다.

이는 프로젝트 문서에 명시된 의도된 설계와 모순되는데, 문서는 자금(보상 포함)이 `_arbFoundation` 주소로 전송되어야 한다고 나타냅니다. 명시적인 청구 메커니즘이 없으면 Arbitrum 재단은 Aave의 인센티브 시스템이 제공하는 수익 기회를 잃게 됩니다.

**권장 사항**

Aave의 `RewardsController`로부터 보상을 청구할 수 있는 기능을 도입하십시오.

# [L-06] 포지션 확장 시 고정된 `BPS_BUFFER` 값으로 인한 비효율성

`BPS_BUFFER` 상수는 현재 `500`으로 설정되어 있으며, 이는 `ArbitrumStrategyManager` 계약에서 포지션을 축소할 때 5% 버퍼를 나타냅니다.

```solidity
    /// @dev Buffer used when scaling down a position to not be close to threshold
    uint256 public constant BPS_BUFFER = 500;
```

이 고정된 값은 특히 포지션 비율(`positionPct`)이 최대 포지션 임계값(`_maxPositionThreshold`)에 근접할 때 **유동성 관리의 비효율성**을 초래할 수 있습니다.

```solidity
        if (positionPct >= _maxPositionThreshold) {
            uint256 bpsToReduce = positionPct + BPS_BUFFER - _maxPositionThreshold;
            uint256 excessAmount = (availableLiquidity * bpsToReduce) / MAX_BPS;

            /// this happens when positionPct and _maxPositionThreshold
            /// have lower values compared to BPS_BUFFER
            /// for example: if positionPct is 2 bps and _maxPositionThreshold is 1 bps
            /// due to BPS_BUFFER being 500 bps, the amount needed to be withdrawn
            /// (excessAmount) will be bigger than current position.
            /// aave only allows to have a withdraw amount value above
            /// the current position amount, if type(uint256).max is used
            if (excessAmount > suppliedAmount) {
                excessAmount = suppliedAmount;
            }
```

예를 들어, `positionPct`와 `_maxPositionThreshold`가 모두 `400`(4%)인 경우, `excessAmount` 계산은 포지션이 정확히 임계값에 있음에도 불구하고 모든 유동성이 인출되는 상황으로 이어질 수 있습니다. 이는 절벽(cliff) 현상을 만듭니다:

1. `positionPct`가 `_maxPositionThreshold`보다 작으면 아무런 조치도 취해지지 않습니다.
2. `positionPct`가 `_maxPositionThreshold`와 같으면 사용 가능한 모든 유동성이 갑자기 인출됩니다.

이 동작은 **포지션이 임계값에 있을 때 어떠한 버퍼나 유연성도 허용하지 않으므로** 의도하지 않은 결과를 초래할 수 있습니다.

이 동작은 예상되는 기능과 모순되며, 버퍼의 존재로 인해 때때로 불필요한 유동성 제거가 발생하여 궁극적으로 자금 활용의 비효율성을 초래할 수 있습니다.

**또한 이러한 상황에서는 일반적으로 높은 가동률(utilization)로 인해 이자율이 상승합니다. 예치금을 전액 인출하면 효과적인 수익 창출이 저해될 수도 있습니다.**

**권장 사항**

코드의 주석은 이 잠재적인 문제를 인정하지만, 정적 버퍼 값에 의존하면 변화하는 시장 상황이나 포지션의 특정 맥락에 따른 조정이 불가능합니다. `_maxPositionThreshold`가 변수이므로, 현재 포지션 역학에 더 잘 맞도록 `BPS_BUFFER`도 조정 가능하게 하는 것이 현명할 것입니다.
