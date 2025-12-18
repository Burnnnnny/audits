# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Reya-Labs/reya-network** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Reya Network 정보

Reya Network는 무기한 선물(perpetuals)을 위해 거래 최적화된 모듈형 L2입니다. 체인 계층은 Arbitrum Orbit에 의해 구동되며 가스 요금이 없고 트랜잭션은 FIFO 기준으로 주문됩니다. 프로토콜 계층은 PnL 결제, 증거금 요구 사항, 청산과 같은 거래를 지원하기 위해 체인을 모듈형 구성 요소로 분해하여 DeFi 애플리케이션의 수직적 통합을 직접 해결합니다.

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

- 낮음 - 가정이 너무 많거나 희박하거나, 인센티브가 거의 또는 전혀 없이 공격자가 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정하는 것이 좋음

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [eccefaaa52c204b244ebf46b0b8c65f1bafb9ea5](https://github.com/Reya-Labs/reya-network/tree/eccefaaa52c204b244ebf46b0b8c65f1bafb9ea5)

_수정 검토 커밋 해시_ - [75fb7305127161490c8ea33654a978d77b4a61de](https://github.com/Reya-Labs/reya-network/tree/75fb7305127161490c8ea33654a978d77b4a61de)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Pyth`
- `Stork`
- `OracleUpdateModule`
- `PythPriceInformationModule`
- `StorkERC7412WrapperModule`
- `StorkPriceInformationModule`
- `Configuration`
- `NodeModule`
- `PythOffchainLookupNode`
- `StorkOffchainLookupNode`
- `NodeDefinition`
- `AccountPermissions`
- `ConditionChecks`
- `DataTypes`
- `ConditionChecks`
- `DataTypes`
- `Errors`
- `Events`
- `ExecuteConditionalOrder`
- `ExecuteMatchOrder`
- `FeatureFlagSupport`
- `SessionOrdersHashing`
- `SessionUpdateHashing`
- `ExecuteLimitOrder`
- `ExecuteSlOrder`
- `ExecuteTpOrder`
- `Utils`
- `ExecutionModule`
- `SessionOrderModule`
- `SessionPermissionModule`
- `NonceMap`
- `SessionPermissions`

# 발견 사항

# [M-01] Stork 및 Pyth 가격이 재정 거래(arbitrage)될 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`fullfillOracleQueryStork` 및 `fullfillOracleQueryPyth` 함수는 각각 Stork 및 Pyth 오라클의 가격을 업데이트하는 데 사용됩니다.

두 오라클 모두 풀(pull) 모델로 작동한다는 점을 감안할 때, 이는 동일한 트랜잭션 내에서 거래를 수행하고 새 가격을 설정하고 포지션을 닫음으로써 가격 업데이트를 재정 거래할 수 있는 기회를 엽니다.

게다가 동일한 블록에서 가격을 여러 번 업데이트할 수 있으므로 공격자는 다음 조건을 충족하는 한 초기 가격과 최종 가격을 모두 선택할 수 있습니다.

- 초기 가격 타임스탬프가 이전 가격 타임스탬프보다 큽니다.
- 최종 가격 타임스탬프가 초기 가격 타임스탬프보다 큽니다.

이는 변동성이 큰 순간에 수익성 있는 재정 거래를 수행할 가능성을 높입니다.

`FeatureFlagSupport.ensureExecutorAccess()`는 호출자가 가격 업데이트를 수행하도록 허용되었는지 확인하고 프로토콜은 가격 업데이트를 신뢰할 수 있는 엔터티로 제한하기 시작하지만, 신뢰할 수 있는 실행자(trusted executor) 기능 플래그에 대해 `allowAll`을 `true`로 설정하여 나중에 누구나 이 작업을 수행할 수 있도록 허용할 계획이라는 점에 유의하십시오. 또한 Pyth 오라클의 경우 `Pyth` 계약을 직접 호출하여 가격을 업데이트할 수 있습니다.

## 권장 사항

Stork 및 Pyth 가격이 블록당 한 번 이상 업데이트되지 않고 새 가격이 오래된 가격이 아닌지 확인하십시오.

# [M-02] `InitiateOrUpdateSession` 타입해시가 잘못 인코딩됨

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

[ERC-712 표준](https://eips.ethereum.org/EIPS/eip-712#definition-of-hashstruct)에 따르면:

> 구조체의 유형은 `name ‖ "(" ‖ member₁ ‖ "," ‖ member₂ ‖ "," ‖ … ‖ memberₙ ")"`로 인코딩되며 각 멤버는 `type ‖ " " ‖ name`으로 작성됩니다.

`SessionUpdateHashing.sol`에서 `_INITIATE_OR_UPDATE_SESSION_TYPEHASH` 상수는 `deadline`과 `session` 멤버 사이에 추가 공백을 포함하므로 표준을 따르지 않습니다.

결과적으로 `SessionPermissionModule.verifySignature`는 주어진 세션 데이터에 대한 올바른 서명을 확인하지 못합니다.

## 권장 사항

```diff
    bytes32 constant _INITIATE_OR_UPDATE_SESSION_TYPEHASH = keccak256(
        //solhint-disable-next-line max-line-length
-       "InitiateOrUpdateSession(uint256 verifyingChainId,uint256 deadline, InitiateOrUpdateSessionDetails session)InitiateOrUpdateSessionDetails(uint128 accountId,address target,uint256 until,address signer,uint256 nonce)"
+       "InitiateOrUpdateSession(uint256 verifyingChainId,uint256 deadline,InitiateOrUpdateSessionDetails session)InitiateOrUpdateSessionDetails(uint128 accountId,address target,uint256 until,address signer,uint256 nonce)"
    );
```

# [M-03] Pyth 가격의 신뢰 구간이 검증되지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`PythOffchainLookupNode.process`는 Pyth 가격의 신뢰 구간을 검증하지 않습니다.

[Pyth 문서](https://docs.pyth.network/price-feeds/best-practices#confidence-intervals)에 명시된 대로 계약이 신뢰할 수 없는 가격을 수락하지 않도록 이 값을 확인하는 것이 중요합니다.

## 권장 사항

```diff
+       if (latestPrice.conf > 0 && (latestPrice.price / int64(latestPrice.conf) < minConfidenceRatio)) {
+           revert LowConfidencePyth(latestPrice.price, latestPrice.conf, oracleAdaptersProxy);
+       }
```

`minConfidenceRatio` 값은 Pyth 노드에 대한 추가 매개변수, 전역 구성 또는 계약의 상수가 될 수 있습니다.

신뢰 구간이 0이면 가격에 스프레드가 없음을 의미하므로 유효한 가격으로 간주해야 합니다.

# [M-04] 잘못된 매개변수 길이 확인

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

매개변수 데이터 길이에 대한 `PythOffchainLookupNode.isValid` 확인이 올바르지 않습니다.

```solidity
    // Must have correct length of parameters data
    if (nodeDefinition.parameters.length != 32 * 4) {
        return false;
    }
```

Pyth 노드의 매개변수 데이터는 오라클 어댑터 주소와 ABI 인코딩된 쌍 ID(bytes32 유형)로 구성되며 길이는 128바이트가 아니라 64바이트입니다.

결과적으로 올바른 매개변수로 노드를 등록하면 실패합니다. 반면 올바른 매개변수 뒤에 64바이트를 채우면 패딩 데이터를 변경하여 사용자가 동일한 노드 데이터를 여러 번 등록할 수 있습니다.

## 개념 증명 (Proof of concept)

```solidity
function test_pythParams() public {
    IPythPriceInformationModule oracleAdaptersProxy = IPythPriceInformationModule(address(1));
    bytes32 pairId = bytes32(uint256(2));
    bytes memory parameters = abi.encode(oracleAdaptersProxy, pairId);
    assertEq(parameters.length, 32 * 2);
}
```

## 권장 사항

```diff
        // Must have correct length of parameters data
-       if (nodeDefinition.parameters.length != 32 * 4) {
+       if (nodeDefinition.parameters.length != 32 * 2) {
            return false;
        }
```

# [M-05] 서명 취소 기능 부족

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`SessionOrderModule.sol` 계약을 사용하면 사용자가 `executeSessionOrdersBySig` 함수를 사용하여 메시지에 서명하여 주문을 실행할 수 있습니다. 그러나 이전에 서명된 메시지를 취소하는 기능은 제공되지 않습니다. 이러한 누락은 사용자가 의사가 변경되었거나 개인 키가 침해되었다고 의심되는 경우 서명된 주문을 취소하거나 철회하고 싶을 수 있으므로 보안 위험을 초래합니다.

현재 주문 실행을 취소하는 유일한 방법은 사용자의 권한을 취소하는 것이지만, 이는 개인 키가 침해된 상황만 해결합니다. 이 옵션은 사용자가 이미 서명된 주문을 단순히 취소하려는 경우 실행 가능한 옵션이 아닙니다.

```solidity
    function _executeSessionOrders(
        address signer,
        uint128 accountId,
        MatchOrderDetails[] memory orders
    )
        internal
        returns (bytes[] memory)
    {
>>>     if (!SessionPermissions.isPermissionActive(accountId, signer)) {
>>>         revert Errors.Unauthorized(signer);
        }

        return executeMatchOrders(accountId, orders, Configuration.getCoreProxyAddress());
    }
```

## 권장 사항

이 취약점을 완화하려면 사용자가 서명된 메시지를 취소할 수 있는 기능을 구현하는 것이 좋습니다. 이 기능은 서명된 메시지의 논스(nonce)를 사용된 것으로 표시하여 동일한 메시지의 향후 실행을 방지해야 합니다. 다른 보고서에서 언급했듯이 취소는 서명된 메시지 논스의 생성자 또는 `accountId`의 소유자만 가능해야 한다는 점에 유의하는 것이 중요합니다.

# [M-06] 초과 지불로 인한 잠재적 Ether 손실

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Pyth.sol` 내의 `fullfillOracleQueryPyth` 함수에는 사용자가 `pyth.updatePriceFeeds` 함수에 필요한 것보다 실수로 더 많은 Ether(`msg.value`)를 보낼 수 있는 잠재적 문제가 있습니다. 이 초과 지불은 환불되지 않아 Ether 손실이 발생합니다.

함수 구현은 다음과 같습니다.

```solidity
function fullfillOracleQueryPyth(address pythAddress, bytes calldata signedOffchainData) {
    IPyth pyth = IPyth(pythAddress);

    (bytes[] memory updateData) = abi.decode(signedOffchainData, (bytes[]));

>>  try pyth.updatePriceFeeds{ value: msg.value }(updateData) { }
    catch (bytes memory reason) {
        if (_isFeeRequired(reason)) {
            revert Errors.FeeRequiredPyth(pyth.getUpdateFee(updateData));
        } else {
            uint256 len = reason.length;
            assembly {
                revert(add(reason, 0x20), len)
            }
        }
    }
}
```

`msg.value`는 `pyth.updatePriceFeeds`에 필요한 정확한 수수료를 확인하지 않고 전송됩니다. 주소 [`0xdd24f84d36bf92c65f92307595335bdfab5bbd21`](https://etherscan.io/address/0xdd24f84d36bf92c65f92307595335bdfab5bbd21#code#F2#L71)의 `Pyth::updatePriceFeeds` 함수에 따르면 전송된 초과 ETH를 반환하는 메커니즘이 없습니다.

```solidity
    function updatePriceFeeds(
        bytes[] calldata updateData
    ) public payable override {
        uint totalNumUpdates = 0;
        for (uint i = 0; i < updateData.length; ) {
            if (
                updateData[i].length > 4 &&
                UnsafeCalldataBytesLib.toUint32(updateData[i], 0) ==
                ACCUMULATOR_MAGIC
            ) {
                totalNumUpdates += updatePriceInfosFromAccumulatorUpdate(
                    updateData[i]
                );
            } else {
                updatePriceBatchFromVm(updateData[i]);
                totalNumUpdates += 1;
            }

            unchecked {
                i++;
            }
        }
        uint requiredFee = getTotalFee(totalNumUpdates);
        if (msg.value < requiredFee) revert PythErrors.InsufficientFee();
    }
```

따라서 두 가지 시나리오가 발생할 수 있습니다.

- 사용자가 예상보다 많은 ETH를 보내면 `Pyth`에서 사용자에게 추가 ETH가 반환되지 않습니다.
- `Pyth` 계약은 업그레이드 가능하므로 `fullfillOracleQueryPyth` 함수가 실행되기 전에 [`pyth`의 수수료가 변경](https://etherscan.io/address/0xdd24f84d36bf92c65f92307595335bdfab5bbd21#code#F2#L62)되어 예상과 다를 수 있습니다.

## 권장 사항

`pyth.updatePriceFeeds`를 호출하기 전에 필요한 정확한 수수료를 계산하고 검증하십시오. 이는 `pyth.getUpdateFee(updateData)`를 호출하고 `msg.value`와 비교하여 수행할 수 있습니다.

```diff
function fullfillOracleQueryPyth(address pythAddress, bytes calldata signedOffchainData) {
    IPyth pyth = IPyth(pythAddress);

    (bytes[] memory updateData) = abi.decode(signedOffchainData, (bytes[]));
+   uint fee = pyth.getUpdateFee(updateData);
+   require(msg.value == fee);
    try pyth.updatePriceFeeds{ value: msg.value }(updateData) { }
    catch (bytes memory reason) {
        if (_isFeeRequired(reason)) {
            revert Errors.FeeRequiredPyth(pyth.getUpdateFee(updateData));
        } else {
            uint256 len = reason.length;
            assembly {
                revert(add(reason, 0x20), len)
            }
        }
    }
}
```

# [M-07] `pythAddress`의 잘못된 구성으로 인해 종속 계약 실패

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`oracle-adapters/src/storage/Configuration.sol` 라이브러리는 Pyth 오라클 주소(`pythAddress`)를 포함하여 구성 데이터를 저장하고 검색하는 책임이 있습니다. 그러나 현재 구현에서는 `pythAddress`를 설정하지 않아 이 주소에 의존하는 계약에서 잠재적인 문제가 발생할 수 있습니다. 특히 `pythAddress`가 설정되지 않은 경우 이 주소를 검색하거나 사용하려는 모든 호출이 실패합니다.

`Configuration.sol`의 관련 코드 섹션은 `pythAddress`가 정의되었지만 설정되지 않았음을 보여줍니다.

```solidity
...
...
    struct Data {
        /**
         * @dev Stork Verify Contract Address
         */
        address storkVerifyContract;
        /**
         * @dev Pyth Address
         */
>>>     address pythAddress;
    }
...
...
    function set(Data memory config) internal {
        Data storage storedConfig = load();

        storedConfig.storkVerifyContract = config.storkVerifyContract;

        emit Events.ConfigurationUpdated(config, block.timestamp);
    }
...
...
    function getPythAddress() internal view returns (address pythAddress) {
>>>     pythAddress = load().pythAddress;
        if (pythAddress == address(0)) {
>>>         revert Errors.PythAddressNotSet();
        }
    }
...
...
```

`pythAddress`를 올바르게 설정하지 않으면 다음이 발생할 수 있습니다.

- Pyth 오라클과 상호 작용할 수 없어 오라클 쿼리가 실패합니다.
- 중요한 데이터 피드를 위해 Pyth에 의존하는 계약에서 잠재적인 서비스 거부가 발생합니다.

테스트:

```solidity
    // File: OracleAdapter.t.sol
    function test_setConfiguration_Pyth() public {
        Configuration.Data memory existingConfig = module.getConfiguration();
        assertEq(address(0), existingConfig.storkVerifyContract);
        assertEq(address(0), existingConfig.pythAddress);
        //
        // 1. `pythAddress` set to address(123)
        config = Configuration.Data({
            storkVerifyContract: mockStorkVerifyContract,
            pythAddress: address(123)
         });
        module.setConfiguration(config);
        existingConfig = module.getConfiguration();
        assertEq(config.storkVerifyContract, existingConfig.storkVerifyContract);
        //
        // 2. `pythAddress` is zero address
        assertEq(address(0), existingConfig.pythAddress);
    }
```

## 권장 사항

`Configuration::set` 함수에서 `pythAddress`가 올바르게 설정되었는지 확인하십시오.

```diff
    function set(Data memory config) internal {
        Data storage storedConfig = load();

        storedConfig.storkVerifyContract = config.storkVerifyContract;
+       storedConfig.pythAddress = config.pythAddress;
        emit Events.ConfigurationUpdated(config, block.timestamp);
    }
```

# [M-08] `ExecutionModule`에서 승인되지 않은 서명 취소

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`ExecutionModule.sol` 계약에서 `cancelSignature` 함수는 승인된 사용자가 논스(nonce)를 사용된 것으로 표시하여 서명을 취소할 수 있도록 합니다. 이 기능은 해당 논스와 관련된 모든 서명된 메시지를 무효화하여 추가 실행을 방지하기 위한 것입니다. 그러나 현재 구현은 동일한 `accountId`로 승인된 모든 사용자가 서명을 취소하도록 허용하므로 잠재적인 오용 가능성이 있습니다.

```solidity
    function cancelSignature(uint128 accountId, uint256 nonce) external override {
        FeatureFlagSupport.ensureGlobalAccess();

>>>     if (!isAuthorizedToTrade(accountId, msg.sender, Configuration.getCoreProxyAddress())) {
            revert Errors.Unauthorized(msg.sender);
        }

>>>     NonceMap.useUnorderedNonce(accountId, nonce);
    }
}
```

위 코드에서 `ExecutionModule::cancelSignature` 함수는 호출자(`msg.sender`)가 지정된 `accountId`에 대해 거래할 권한이 있는지 확인합니다. 승인된 경우 논스를 계속 사용하여 해당 논스와 관련된 서명을 효과적으로 취소합니다.

동일한 `accountId`에 대해 여러 사용자가 승인(`MATCH_ORDER` 권한 보유)된 경우 문제가 발생합니다. 이러한 사용자 중 누구라도 `cancelSignature`를 호출하여 다른 승인된 사용자의 서명을 무효화할 수 있으며, 이는 합법적인 트랜잭션을 중단시킬 수 있습니다. 다음 시나리오를 고려하십시오.

1. `AccountId.Owner`는 `MATCH_ORDER` 권한을 `alice`와 `bob`에게 할당합니다.
2. `alice`는 `ExecutionModule::execute` 함수를 사용하여 실행할 `nonce=1`을 사용하여 메시지에 서명합니다.
3. `bob`은 `alice`의 메시지가 실행되기 전에 `nonce=1`을 취소하기 위해 악의적으로 `ExecutionModule::cancelSignature`를 호출하고 `alice` 메시지 `nonce=1`을 무효화합니다.

```solidity
    // File: ExecutionModule.t.sol
    function test_cancelSignature_ByOtherUserWithPermissionInTheSameAccountId() public {
        (address bob,) = makeAddrAndKey("bob");
        //
        // 1. Bob has permission (`MATCH_ORDER`) in `accountId=1`
        vm.mockCall(
            mockCoreProxy,
            abi.encodeCall(
                IAccountModule.isAuthorizedForAccount,
                (1, 0x5d3e646d0f5446da275659f3f1489b5d0bf7a151df31e98bb653f21267dd0fad, bob)
            ),
            abi.encode(true)
        );
        //
        // 2. Bob cancels alice's order.
        vm.prank(bob);
        module.cancelSignature(1, 1);
    }
```

## 권장 사항

논스를 생성한 사용자(또는 계정 소유자)만 논스를 취소할 수 있도록 하여 동일한 `accountId`에 대해 승인된 다른 사용자의 무단 서명 취소를 방지하십시오.

# [M-09] 논스 프론트 러닝 취약점

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`NonceMap`의 현재 구현에서 `nonce`는 이중 지출 및 재생 공격을 방지하는 데 사용됩니다. 그러나 계정에 대한 활성 권한이 있는 악의적인 사용자가 합법적인 사용자와 동일한 논스를 먼저 사용하여 합법적인 사용자의 주문을 무효화할 수 있습니다. 취약점은 동일한 `account`와 상호 작용할 수 있는 권한이 있는 서로 다른 사용자 간에 논스를 적절히 격리하거나 보호하지 않는 데 있습니다.

오류는 `SessionOrderModule.sol`에서 발생하며, 악의적인 사용자는 동일한 계정의 다른 사용자의 주문을 취소하기 위해 계정 내에 권한만 있으면 됩니다. 다음 시나리오를 고려하십시오.

1. `account's owner`는 `SessionPermissionModule::initiateOrUpdateSession`을 사용하여 `alice`와 `bob`에게 권한을 할당합니다.
2. `alice`는 `nonce=100`을 사용하여 서명된 메시지를 생성합니다.
3. `bob`은 악의적으로 동일한 `nonce=100`을 사용하여 서명된 메시지를 생성하고 Alice의 트랜잭션보다 먼저 제출합니다.
4. 결과적으로 Alice의 주문은 `bob`이 논스를 이미 사용했기 때문에 실행 불가능하게 됩니다.

```solidity
// File: SessionOrderModule.sol
...
...
    function executeSessionOrdersBySig(
        ExecuteSessionOrdersDetails memory orders,
        EIP712Signature calldata signature
    )
        external
        override
        returns (bytes[] memory)
    {
        FeatureFlagSupport.ensureGlobalAccess();

>>>     if (NonceMap.load(orders.accountId).hasNonceBeenUsed(orders.nonce)) {
            revert Errors.NonceAlreadyUsed(orders.accountId, orders.nonce);
        }

        if (!verifySignature(orders, signature)) {
            revert Errors.InvalidSignature();
        }

>>>     NonceMap.useUnorderedNonce(orders.accountId, orders.nonce);

        return _executeSessionOrders(orders.signer, orders.accountId, orders.orders);
    }
...
...
    function _executeSessionOrders(
        address signer,
        uint128 accountId,
        MatchOrderDetails[] memory orders
    )
        internal
        returns (bytes[] memory)
    {
>>>     if (!SessionPermissions.isPermissionActive(accountId, signer)) {
            revert Errors.Unauthorized(signer);
        }

        return executeMatchOrders(accountId, orders, Configuration.getCoreProxyAddress());
    }
...
...
```

동일한 계정 내의 다른 사용자의 주문을 취소할 수 있는 `ExecutionModule.sol`에서도 동일한 동작이 발생합니다. 공격은 다음과 같이 진행됩니다.

1. `account's owner`는 `MATCH_ORDER` 권한을 `alice`와 `bob`에게 할당합니다.
2. `alice`는 `nonce=100`을 사용하여 서명된 메시지를 생성합니다.
3. 악의적으로 `bob`은 `nonce=100`을 사용하여 주문을 생성하고 `alice`보다 먼저 트랜잭션을 전송하여 `alice`의 합법적인 트랜잭션이 실행되는 것을 방지합니다.

이 공격 벡터는 `ExecutionModule::cancelSignature` 함수를 사용하여 동일한 `accountId`의 다른 사용자의 주문을 취소할 수 있는 보고서에 설명된 것과는 상당히 다릅니다.

마찬가지로 `SessionPermissionModule.sol`도 동일한 `NonceMap` 메커니즘을 사용하므로 사용자에게 영향을 미치며 사용자가 계정 내 권한 할당을 잠재적으로 차단할 수 있습니다. 다음 시나리오를 고려하십시오.

1. `alice`는 `nonce=100`을 사용하여 `account's owner`로부터 오프체인 서명된 메시지를 얻습니다. 서명된 메시지는 사용자 X에게 권한을 할당합니다.
2. `bob`은 동일한 `nonce=100`을 사용하여 `account's owner`로부터 오프체인 서명된 메시지를 얻습니다. 서명된 메시지는 사용자 Z에게 권한을 할당합니다.
3. `bob`은 악의적으로 alice(프론트 러닝)보다 먼저 트랜잭션을 전송하여 `alice`의 트랜잭션이 처리되는 것을 방지합니다.

## 권장 사항

논스가 계정이 아닌 사용자별로 고유하도록 보장하는 메커니즘을 구현하십시오.

# [L-01] Pyth 가격 업데이트 시 신뢰할 수 있는 실행자 확인이 시행되지 않음

`OracleUpdateModule`을 사용하면 신뢰할 수 있는 실행자가 Stork 및 Pyth 오라클 가격을 업데이트할 수 있습니다.

Stork 오라클의 경우 가격이 확인된 후 저장소에 저장됩니다. 그러나 Pyth 오라클의 경우 가격이 `Pyth` 계약에서 업데이트됩니다. 누구나 `Pyth` 계약과 직접 상호 작용할 수 있으므로 호출자가 신뢰할 수 있는 실행자인지 확인하는 것은 쉽게 우회될 수 있습니다.

# [L-02] 네이티브 토큰 손실 가능성

`OracleUpdateModule` 계약 내의 `fulfillOracleQuery` 함수는 `payable`로 표시되어 있어 Ether(`msg.value`)를 수락할 수 있습니다. 그러나 `oracleProvider`가 `STORK`로 설정된 경우 함수는 `msg.value`를 특별한 방식으로 처리하지 않습니다. 이로 인해 사용자가 `STORK` 오라클 공급자에 대한 `fulfillOracleQuery` 호출과 함께 실수로 Ether를 보내 네이티브 토큰을 잃게 되는 상황이 발생할 수 있습니다. 이러한 트랜잭션에서 전송된 Ether는 활용되거나 환불되지 않아 사용자가 전송된 금액을 잃게 됩니다.

```solidity
    function fulfillOracleQuery(
        OracleProvider oracleProvider,
        bytes calldata signedOffchainData
    )
        external
>>>     payable
        override
    {
        FeatureFlagSupport.ensureGlobalAccess();

        // note, if an executor is trusted, they are allowed to execute a fullfill oracle query operation
        // on top of any oracle provider type (e.g. stork, pyth, etc)
        FeatureFlagSupport.ensureExecutorAccess();

>>>     if (oracleProvider == OracleProvider.STORK) {
            address storkVerifyContract = Configuration.getStorkVerifyContractAddress();
            fullfillOracleQueryStork(storkVerifyContract, signedOffchainData);
        } else if (oracleProvider == OracleProvider.PYTH) {
            address pythAddress = Configuration.getPythAddress();
            fullfillOracleQueryPyth(pythAddress, signedOffchainData);
        }
    }
```

위의 코드에서 `oracleProvider`가 `STORK`일 때 `msg.value`를 처리하지 않아 네이티브 토큰 손실이 발생할 수 있습니다.

`oracleProvider`가 `PYTH`일 때만 Ether를 수락하도록 확인을 구현하는 것이 좋습니다.

# [L-03] 계정 소유자는 세션을 위해 스스로를 초기화해야 함

`accountId` 소유자는 기본적으로 거래할 권한이 있지만 `SessionPermissionModule._initiateOrUpdateSession` 함수를 통해 스스로를 초기화하지 않는 한 `SessionOrderModule.executeSessionOrdersBySig` 함수와 `SessionOrderModule.executeSessionOrders` 함수를 호출할 수 없습니다.

```solidity
    function _executeSessionOrders(
        address signer,
        uint128 accountId,
        MatchOrderDetails[] memory orders
    )
        internal
        returns (bytes[] memory)
    {
>>      if (!SessionPermissions.isPermissionActive(accountId, signer)) {
            revert Errors.Unauthorized(signer);
        }

        return executeMatchOrders(accountId, orders, Configuration.getCoreProxyAddress());
    }
<...>

library SessionPermissions {
<...>
    function isPermissionActive(uint128 accountId, address target) internal view returns (bool) {
        uint256 activeUntil = load(accountId).permissions[target];

        return activeUntil >= block.timestamp;
    }
```

`_executeSessionOrders` 함수에서 `msg.sender`가 `accountId` 소유자인지 확인하는 것을 고려하십시오.

```diff
+import { isOwner } from "../libraries/AccountPermissions.sol";
<...>
    function _executeSessionOrders(
        address signer,
        uint128 accountId,
        MatchOrderDetails[] memory orders
    )
        internal
        returns (bytes[] memory)
    {
-       if (!SessionPermissions.isPermissionActive(accountId, signer)) {
+       if (!SessionPermissions.isPermissionActive(accountId, signer) &&
+           !isOwner(accountId, msg.sender, Configuration.getCoreProxyAddress())) {
            revert Errors.Unauthorized(signer);
        }

        return executeMatchOrders(accountId, orders, Configuration.getCoreProxyAddress());
    }
```
