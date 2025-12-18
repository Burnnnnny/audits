# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**lastdotnet/hypurrfi-deployments** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# HypurrFi 소개

HypurrFi는 Hyperliquid의 레버리지 대출 시장으로, HYPE 및 stHYPE와 같은 기본 자산에 대한 현물 포지션을 유지하면서 깨끗한 레버리지 루프를 가능하게 합니다. 스테이블 코인인 $USDXL은 프로토콜 수익과 증가하는 토큰화된 미국 재무부 채권 준비금으로 뒷받침됩니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간 - 프로토콜 자산의 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터일 뿐이지만 여전히 발생 가능성이 상대적으로 높습니다.

- 낮음 - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 또는 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정해야 함 (Should fix)

- 낮음 - 수정할 수 있음 (Could fix)

# 보안 평가 요약

_검토 커밋 해시:_

- [2509ece7be02e22c1db54e2238ba4c1715ca2bae](https://github.com/lastdotnet/hypurrfi-deployments/commit/2509ece7be02e22c1db54e2238ba4c1715ca2bae)
- [2490d3ca12a081a8c49981935c2b11eddcb5d519](https://github.com/lastdotnet/usdxl-core/commit/2490d3ca12a081a8c49981935c2b11eddcb5d519)

_수정 검토 커밋 해시:_

- [a049c7dcad5ce0c9af0f9f369b984023d324bd9b](https://github.com/lastdotnet/usdxl-core/commit/a049c7dcad5ce0c9af0f9f369b984023d324bd9b)
- [df0d50f3a37f3c199214b6c1e460e390a7a03e17](https://github.com/lastdotnet/hypurrfi-deployments/commit/df0d50f3a37f3c199214b6c1e460e390a7a03e17)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `ConfigurrHyFiReservesMainnet`
- `ConfigurrHyFiReservesTestnet`
- `DeployCapAutomator`
- `DeployHyFi`
- `DeployWHYPE`
- `SupplyHyFi`
- `TransferOwnership`
- `USDfSilo`
- `HyperTestnetReservesConfigs`
- `DeployHyFiUtils`
- `DeployUtils`
- `BorrowUsdxlHyperTestnet`
- `DeployUsdxlGsmHyperTestnet`
- `DeployUsdxlHyperTestnet`
- `RepayUsdxlHyperTestnet`
- `HyperTestnetReservesConfigs`
- `DeployUsdxlFileUtils`
- `DeployUsdxlUtils`

# 발견 사항

# [H-01] `DeployUsdxlUtils`가 usdxlToken의 소유권을 `admin`에게 양도하지 않음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`_deployUsdxl` 함수는 usdxlTokenProxy를 배포하고 `deployer`를 `usdxlToken`의 소유자로 설정합니다.

```solidity
    function _deployUsdxl(address proxyAdmin, IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry) internal {
        --snip--
        // 1. Deploy USDXL token implementation and proxy
        UpgradeableUsdxlToken usdxlTokenImpl = new UpgradeableUsdxlToken();

        bytes memory initParams = abi.encodeWithSignature("initialize(address)", deployer);

        usdxlTokenProxy = address(new TransparentUpgradeableProxy(address(usdxlTokenImpl), proxyAdmin, initParams));

        usdxlToken = IUsdxlToken(usdxlTokenProxy);

        --snip--
     }
```

그러나 소유권(관리자 권한)을 `deployer`에서 `admin`으로 양도하지 않습니다.

## 권장 사항

배포 및 구성 후 `usdxlToken`의 소유권을 관리자(admin)에게 양도하십시오.

# [H-02] 배포자가 `CapAutomator`의 소유권을 관리자에게 양도하지 않음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`DeployCapAutomator.run` 함수는 `CapAutomator`의 인스턴스를 배포하여 `msg.sender`(`deployer`)를 초기 소유자로 할당합니다. 그러나 소유권을 지정된 `admin`에게 양도하지 않습니다.

```solidity
    function run() external {
        --snip--
        poolAddressesProvider = IPoolAddressesProvider(deployedContracts.readAddress(".poolAddressesProvider"));

        vm.startBroadcast(vm.envUint("PRIVATE_KEY"));

        capAutomator = new CapAutomator(address(poolAddressesProvider));

        vm.stopBroadcast();
        --snip--
    }
```

```solidity
contract CapAutomator is ICapAutomator, Ownable {
    --snip--
    constructor(address poolAddressesProvider) Ownable(msg.sender) {
        pool             = IPool(IPoolAddressesProvider(poolAddressesProvider).getPool());
        poolConfigurator = IPoolConfigurator(IPoolAddressesProvider(poolAddressesProvider).getPoolConfigurator());
    }
```

## 권장 사항

배포 후 `capAutomator`의 소유권을 `admin`으로 양도하십시오.

# [H-03] 잘못된 프록시 주소 추적으로 USDXL 풀 토큰 구성 오류 발생

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`DeployUsdxlUtils._getUsdxlATokenProxy()` 및 `DeployUsdxlUtils._getUsdxlVariableDebtTokenProxy()` 함수는 프록시 주소 대신 구현 계약 주소를 잘못 반환합니다.

```solidity
//File: src/deployments/utils/DeployUsdxlUtils.sol

function _getUsdxlATokenProxy() internal view returns (address) {
    return address(usdxlAToken);    // Returns implementation instead of proxy
}

function _getUsdxlVariableDebtTokenProxy() internal view returns (address) {
    return address(usdxlVariableDebtToken); // Returns implementation instead of proxy
}
```

이로 인해 네 가지 주요 문제가 발생합니다:

1. `_initializeUsdxlReserve()` 함수의 배포 아티팩트에서 잘못된 계약 내보내기.
2. `_setUsdxlAddresses()` 함수에서 잘못된 토큰 구성으로 인해 USDXL 풀의 `AToken` 및 `VariableDebtToken`이 구성되지 않은 상태로 유지됨.
3. `_addUsdxlATokenAsEntity()` 함수에서 USDXL 토큰에 대한 잘못된 퍼실리테이터(facilitator) 구성.
4. `_setDiscountTokenAndStrategy()` 함수에서 잘못된 할인 토큰 및 전략 구성.

## 권장 사항

구현 주소를 사용하는 대신 USDXL 풀에 구성된 실제 프록시 주소를 추적하십시오. 이렇게 하면 토큰 구성이 풀이 상호 작용하는 올바른 계약 인스턴스에 적용됩니다.

이를 구현하려면:

1. 풀 초기화 후 풀의 준비금 데이터에서 프록시 주소를 가져와 추적하십시오:

```diff
function _initializeUsdxlReserve(
    address token,
    IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry
)
    internal
{
    --- SNIPPED ---
    // set reserves configs
    _getPoolConfigurator(deployRegistry).initReserves(inputs);

+   IPoolAddressesProvider poolAddressesProvider = _getPoolAddressesProvider(deployRegistry);
    //@audit DataTypes should be additional imported
+   DataTypes.ReserveData memory reserveData = IPool(poolAddressesProvider.getPool()).getReserveData(token);

    //@audit Introduce new two state variables to track proxy addresses
+   usdxlATokenProxy = UsdxlAToken(reserveData.aTokenAddress);
+   usdxlVariableDebtTokenProxy = UsdxlVariableDebtToken(reserveData.variableDebtTokenAddress);

    // export contract addresses
    DeployUsdxlFileUtils.exportContract(instanceId, "usdxlATokenProxy", _getUsdxlATokenProxy());
    DeployUsdxlFileUtils.exportContract(instanceId, "usdxlVariableDebtTokenProxy", _getUsdxlVariableDebtTokenProxy());
}
```

2. 프록시 주소를 반환하도록 getter 함수를 업데이트하십시오:

```diff
function _getUsdxlATokenProxy() internal view returns (address) {
-    return address(usdxlAToken);
+    return address(usdxlATokenProxy);
}

function _getUsdxlVariableDebtTokenProxy() internal view returns (address) {
-    return address(usdxlVariableDebtToken);
+    return address(usdxlVariableDebtTokenProxy);
}
```

3. 프록시를 사용하도록 자금(treasury) 구성을 업데이트하십시오:

```diff
function _setUsdxlAddresses(IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry)
    internal
{
-    usdxlAToken.updateUsdxlTreasury(deployRegistry.treasury);
+    UsdxlAToken(_getUsdxlATokenProxy()).updateUsdxlTreasury(deployRegistry.treasury);

    UsdxlAToken(_getUsdxlATokenProxy()).setVariableDebtToken(_getUsdxlVariableDebtTokenProxy());
    UsdxlVariableDebtToken(_getUsdxlVariableDebtTokenProxy()).setAToken(_getUsdxlATokenProxy());
}
```

# [M-01] DeployHyFiConfigEngine: `proxyAdmin`의 이중 배포

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`DeployHyFiConfigEngine.run`은 `transparentProxyFactory`를 사용하여 `ProxyAdmin`을 생성합니다:

```solidity
    function run() external {
        --snip--
        transparentProxyFactory = new TransparentProxyFactory();
        proxyAdmin = ProxyAdmin(transparentProxyFactory.createProxyAdmin(admin));

        (ratesFactory,) = DeployRatesFactoryLib._createAndSetupRatesFactory(
             poolAddressesProvider, address(transparentProxyFactory), address(proxyAdmin), reservesToSkip);
       --snip--
     }
```

그런 다음 `_createAndSetupRatesFactory`를 호출하고 `proxyAdmin`의 주소를 `ownerForFactory`로 전달합니다:

```solidity
    function _createAndSetupRatesFactory(
        IPoolAddressesProvider addressesProvider,
        address transparentProxyFactory,
        address ownerForFactory,
        address[] memory reservesToSkip
    ) internal returns (V3RateStrategyFactory, address[] memory) {
        --snip--
        V3RateStrategyFactory ratesFactory = V3RateStrategyFactory(
            ITransparentProxyFactory(transparentProxyFactory).create(
                address(new V3RateStrategyFactory(addressesProvider)),
                ownerForFactory,
                abi.encodeWithSelector(V3RateStrategyFactory.initialize.selector, uniqueStrategies)
            )
        );
       --snip--
}
```

`ITransparentProxyFactory(transparentProxyFactory).create`를 호출하고 `ownerForFactory`(이미 배포된 `proxyAdmin`)의 주소를 `initialOwner`로 전달합니다.
문제는 `create` 함수가 소유자의 주소를 기대하고 자체 `adminProxy`를 배포한다는 것입니다:

https://github.com/bgd-labs/solidity-utils/blob/90266e46868fe61ed0b54496c10458c247acdb51/src/contracts/transparent-proxy/TransparentProxyFactoryBase.sol#L29

```solidity
function create(
    address logic,
    address initialOwner,
    bytes calldata data
  ) external returns (address) {
    address proxy = address(new TransparentUpgradeableProxy(logic, initialOwner, data));
    _storeProxyInRegistry(proxy);

    emit ProxyCreated(proxy, logic, initialOwner);

    return proxy;
  }
```

따라서 패턴은 다음과 같습니다:
proxyAdmin(1) > proxyAdmin(2) > transparentProxy > Impl
결과적으로 관리자는 계약을 업그레이드할 수 없습니다.

참고:
`import {ITransparentProxyFactory} from "solidity-utils/contracts/transparent-proxy/interfaces/ITransparentProxyFactory.sol"; `
위 인터페이스에 대한 코드는 다음과 같습니다:
https://github.com/bgd-labs/solidity-utils/blob/main/src/contracts/transparent-proxy/interfaces/ITransparentProxyFactory.sol

## 권장 사항

별도의 `proxyAdmin`을 배포하지 말고 `admin` 주소를 `create` 함수에 전달하십시오.

# [M-02] `DeployUsdxlUtils`: 발행 한도에 대한 잘못된 설정

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`_addUsdxlATokenAsEntity()` 및 `_addUsdxlFlashMinterAsEntity()` 함수는 발행 한도를 1억(100mil) 대신 10억(1B)으로 설정합니다:

```solidity
    function _addUsdxlATokenAsEntity(IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry)
        internal
    {
        // pull aToken proxy from reserves config
        _getUsdxlToken().addFacilitator(
          address(_getUsdxlATokenProxy()),
          'HypurrFi Market Loans', // entity label
          1e27 // entity mint limit (100mil)
        );
    }

    function _addUsdxlFlashMinterAsEntity(IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry)
        internal
    {
      _getUsdxlToken().addFacilitator(
        address(flashMinter),
        'HypurrFi Market Flash Loans', // entity label
        1e27 // entity mint limit (100mil)
      );
    }
```

## 권장 사항

1억(100mil)으로 설정하려면 1e27 대신 1e26을 사용하십시오.

# [M-03] 풀 준비금은 동일한 트랜잭션에서 초기화되고 공급되어야 함

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

현재 **HyperEVM 테스트넷**의 경우 풀은 `ConfigurrHyFiReserves` 스크립트에서 준비금 토큰으로 초기화되고, 토큰은 다른 스크립트인 `SupplyHyFi`에서 공급됩니다. 이러한 접근 방식은 빈 준비금에 대한 첫 번째 예금자의 **인플레이션 공격**에 시스템을 취약하게 만듭니다.
이상적으로는 두 작업(준비금 초기화 및 공급)이 동일한 트랜잭션에서 발생하여 시스템이 올바르게 구성되고 유동성이 추가되기 전에 공격자가 풀을 조작할 수 없도록 해야 합니다.

인스턴스:

- **USDC** 및 **sUSDe** 토큰은 풀에 공급되지만 해당 준비금은 `ConfigurrHyFiReserves` 스크립트가 **KHYPE** 토큰 준비금만 초기화하므로 배포 스크립트에 의해 초기화되지 않습니다.
- **KHYPE 준비금**은 `ConfigurrHyFiReserves` 스크립트에 의해 초기화되지만 유동성이 공급되지 않습니다.

## 권장 사항

풀 준비금이 동일한 트랜잭션에서 초기화되고 최소 유동성(시드 금액)이 공급되도록 배포 프로세스를 업데이트하십시오.

# [M-04] 컴파일 불가능한 `DeployUsdxlHyperTestnet` 스크립트

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`DeployUsdxlHyperTestnet` 스크립트는 배포 구성에 `usdxlConfig`를 사용하려고 시도하지만 상태 변수로 선언하지 못합니다. 이로 인해 컴파일 실패가 발생하고 배포 스크립트를 사용할 수 없게 됩니다.

```solidity
//File: script/DeployUsdxlHyperTestnet.sol

function _deploy() internal {
    vm.setEnv('FOUNDRY_ROOT_CHAINID', vm.toString(block.chainid));
    instanceId = 'hypurrfi-testnet';

    config = DeployUsdxlFileUtils.readInput(instanceId);
@>  usdxlConfig = DeployUsdxlFileUtils.readUsdxlInput(instanceId);  // @audit usdxlConfig not declared
    --- SNIPPED ---

    _deployUsdxl(usdxlConfig.readAddress('.usdxlAdmin'), deployRegistry);  // Fails: usdxlConfig not declared
}
```

## 권장 사항

`DeployUsdxlHyperTestnet`에서 `usdxlConfig` 상태 변수를 선언하십시오.

# [L-01] `DeployUsdxlUtils`: `UsdxlInterestRateStrategy` 계약이 두 번 배포됨

`DeployUsdxlUtils`: `_deployUsdxl` 함수는 3단계에서 `UsdxlInterestRateStrategy`를 배포합니다:

```solidity

    function _deployUsdxl(address proxyAdmin, IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry) internal {
        --snip--
          // 3. Deploy USDXL Interest Rate Strategy
        usdxlInterestRateStrategy = new UsdxlInterestRateStrategy(
            deployRegistry.poolAddressesProvider,
            0.02e27 // 2% base rate
        );
        --snip--
    }
```

그러나 `_deployUsdxl`의 10단계에서 `_updateUsdxlInterestRateStrategy()`를 호출하여 `UsdxlInterestRateStrategy`를 두 번째로 배포합니다:

```solidity
    function _updateUsdxlInterestRateStrategy(IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry)
        internal
    {
        UsdxlInterestRateStrategy interestRateStrategy = new UsdxlInterestRateStrategy(
          address(deployRegistry.poolAddressesProvider),
          0.02e27
        );

        _getPoolConfigurator(deployRegistry).setReserveInterestRateStrategyAddress(address(_getUsdxlToken()), address(interestRateStrategy));
    }
```

`UsdxlInterestRateStrategy`를 다시 배포하는 대신 이미 배포된 계약 주소를 사용하십시오.

# [L-02] 배포에서 사용되지 않는 `Göerli testnet` 파일 제거

입력 폴더에는 **Göerli testnet** 배포를 위한 `primary.json`이 있는 폴더가 포함되어 있습니다. 그러나 Göerli 테스트넷은 더 이상 사용되지 않으며 더 이상 테스트 배포에 사용할 수 없습니다. 결과적으로 Göerli에 대한 `primary.json` 파일의 존재는 불필요하며 이 테스트넷에 배포를 시도할 때 혼란을 야기하거나 오류로 이어질 수 있습니다.

권장 사항: 문제를 피하고 지원되는 체인만 배포에 사용되도록 배포 프로세스에서 **Göerli testnet** 관련 항목, 파일 및 폴더를 제거하십시오.

# [L-03] `hyTokenImpl` 초기화 중 잘못된 tokenName 설정

`DeployHyFiUtils` 스크립트에서 `hyTokenImpl`이 초기화될 때 `aTokenName`은 `"SPTOKEN_IMPL"`로 설정되는데, 이는 SparkLend 프로토콜에 특정한 것이며, 대신 **HypurrFi** 프로토콜에 해당하는 특정 이름으로 설정되어야 합니다.

```solidity
   hyTokenImpl = new HyToken(pool);
        hyTokenImpl.initialize(
            pool, address(0), address(0), IHyFiIncentivesController(address(0)), 0, "SPTOKEN_IMPL", "SPTOKEN_IMPL", ""
        );
```

권장 사항: `DeployHyFiUtils` 스크립트를 업데이트하여 `hyTokenImpl` 초기화 중에 `aTokenName`을 **HypurrFi** 프로토콜에 적절한 이름으로 설정하십시오.

# [L-04] `_deployUsdxl()`이 `usdxlVariableDebtToken`을 초기화하지 않음

`_deployUsdxl()` 함수는 **usdxl 토큰**과 **usdx 준비금**을 초기화하는 데 필요한 계약을 배포하도록 설계되었지만, **`usdxlVariableDebtToken`**이 배포될 때 스크립트에서 초기화되지 않는 것으로 확인되었습니다. `usdxlVariableDebtToken.initialize()` 함수가 제한되지 않았으므로 악의적인 행위자가 의도하지 않거나 잘못되거나 관련 없는 매개변수로 초기화할 수 있습니다.

권장 사항: 배포 프로세스 중에 스크립트 내에서 **`usdxlVariableDebtToken`**이 제대로 초기화되도록 하십시오.

# [L-05] `_deployUsdxl()`이 `usdxlAToken`을 초기화하지 않음

`_deployUsdxl()` 함수는 **usdxl 토큰**과 **usdx 준비금**을 초기화하는 데 필요한 계약을 배포하도록 설계되었지만, **`usdxlAToken`**이 배포될 때 스크립트에서 초기화되지 않는 것으로 확인되었습니다. `usdxlAToken.initialize()` 함수가 제한되지 않았으므로 악의적인 행위자가 의도하지 않거나 잘못되거나 관련 없는 매개변수로 초기화할 수 있습니다.

권장 사항: 배포 프로세스 중에 스크립트 내에서 **`usdxlAToken`**이 제대로 초기화되도록 하십시오.

# [L-06] GSM 프록시 관리자 및 고유 이자율 전략 처리에 필요한 `TODO` 해결

다음 해결되지 않은 `TODO`는 배포 및 구성 로직에 중요한 문제를 도입합니다:

1. `DeployUsdxlUtils._deployGsm()`에서 `address(0)`을 프록시 관리자로 하드 코딩함. 현재 프록시 관리자는 `address(0)`으로 하드 코딩되어 있어 아무도 프록시의 업그레이드 또는 관리 기능을 관리할 수 없습니다.

```solidity
//File: (usdxl-core) src/deployments/utils/DeployUsdxlUtils.sol

function _deployGsm() internal returns (address) {
    AdminUpgradeabilityProxy proxy = new AdminUpgradeabilityProxy(
        address(gsmImpl),
        address(0), // TODO: set admin to timelock
        ""
    );
    --- SNIPPED ---
}
```

2. `DeployHyFiConfigEngine._getUniqueStrategiesOnPool()`의 중복 전략 계약.

```solidity
//File: (hypurrfi-deployment) script/DeployHyFiConfigEngine.s.sol

library DeployRatesFactoryLib {
@>   // TODO check also by param, potentially there could be different contracts, but with exactly same params
    function _getUniqueStrategiesOnPool(IPool pool, address[] memory reservesToSkip) {...}
```

이 함수는 현재 계약 주소로만 중복 전략을 확인하지만 실제 매개변수로는 확인하지 않습니다. 그러나 `V3RateStrategyFactory.initialize()`에서 전략은 매개변수의 해시를 사용하여 식별됩니다. 즉, 동일한 구성을 다른 계약으로 여러 번 등록하여 불필요한 중복을 초래할 수 있습니다.

```solidity
//File: (hypurrfi-deployment) lib/aave-helpers/src/v3-config-engine/V3RateStrategyFactory.sol

function initialize(IDefaultInterestRateStrategy[] memory liveStrategies) external initializer {
for (uint256 i = 0; i < liveStrategies.length; i++) {
    RateStrategyParams memory params = getStrategyData(liveStrategies[i]);

    bytes32 hashedParams = strategyHashFromParams(params);

@>  _strategyByParamsHash[hashedParams] = address(liveStrategies[i]);
@>  _strategies.push(address(liveStrategies[i]));

    emit RateStrategyCreated(address(liveStrategies[i]), hashedParams, params);
}
}
```

권장 사항

- `DeployUsdxlUtils._deployGsm()` 함수의 경우:
  프록시 관리자가 계약(예: 타임락 계약)이어야 하는 경우 스크립트의 일부로 배포하고 적절하게 할당하십시오. 그렇지 않으면 `address(0)`을 하드 코딩하는 대신 프록시 관리자 주소를 `_deployGsm()`에 매개변수로 전달하십시오.

- `DeployHyFiConfigEngine._getUniqueStrategiesOnPool()`의 경우:
  새로운 고유 전략을 추가하기 전에 동일한 매개변수를 가진 다른 전략이 이미 존재하는지 확인하십시오.

# [L-07] DeployUsdxlUtils: `_deployGsm()`은 구현 대신 `usdxlToken`의 프록시 주소를 사용해야 함

`DeployUsdxlUtils`: `_deployGsm()` 함수는 새 Gsm을 배포할 때 구현 대신 프록시 주소(`_getUsdxlToken()`)를 사용해야 합니다:

```solidity
   function _deployGsm(
        address token,
        address gsmOwner,
        uint256 maxCapacity,
        IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry
    ) internal returns (address gsmProxy) {
        --snip--

        // Deploy GSM implementation
        Gsm gsmImpl = new Gsm(address(usdxlToken), address(token), address(fixedPriceStrategy));

        --snip--
```

권장 사항:

`address(usdxlToken)` 대신 `_getUsdxlToken()`을 사용하십시오.

# [L-08] `DeployUsdxlUtils`: `usdxlAToken` 및 `usdxlVariableDebtToken` 계약이 두 번 배포됨

`_deployUsdxl` 함수는 4단계에서 `usdxlAToken` 및 `usdxlVariableDebtToken` 토큰 계약을 배포하고 다른 구성에서 해당 주소를 사용합니다:

```solidity

    function _deployUsdxl(address proxyAdmin, IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry) internal {
        --snip--

        // 4. Deploy USDXL AToken and Variable Debt Token
        usdxlAToken = new UsdxlAToken(IPool(IPoolAddressesProvider(deployRegistry.poolAddressesProvider).getPool()));

        usdxlVariableDebtToken =
            new UsdxlVariableDebtToken(IPool(IPoolAddressesProvider(deployRegistry.poolAddressesProvider).getPool()));

        // 5. Deploy Flash Minter
        flashMinter = new UsdxlFlashMinter(
            address(usdxlToken),
            deployRegistry.treasury,
            0, // no fee
            deployRegistry.poolAddressesProvider
        );

```

그러나 `_deployUsdxl`의 8단계에서 `_initializeUsdxlReserve()`를 호출하여 `usdxlAToken` 및 `usdxlVariableDebtToken` 토큰을 배포하고 해당 주소를 두 번째로 내보냅니다:

```solidity
    function _initializeUsdxlReserve(address token, IDeployConfigTypes.HypurrDeployRegistry memory deployRegistry) internal {
        ConfiguratorInputTypes.InitReserveInput[] memory inputs = new ConfiguratorInputTypes.InitReserveInput[](1);

        usdxlAToken = new UsdxlAToken(_getPoolInstance(deployRegistry));

        usdxlVariableDebtToken = new UsdxlVariableDebtToken(_getPoolInstance(deployRegistry));

        DeployUsdxlFileUtils.exportContract(instanceId, "usdxlATokenImpl", address(usdxlAToken));
        DeployUsdxlFileUtils.exportContract(instanceId, "usdxlVariableDebtTokenImpl", address(usdxlVariableDebtToken));

        --snip--
    }

```

권장 사항:

`usdxlAToken` 및 `usdxlVariableDebtToken` 계약을 다시 배포하는 대신 이미 배포된 계약 주소를 사용하십시오.
