# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**reya-network** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Reya Network 소개

Reya Network는 거래에 최적화된 모듈식 L2입니다. 체인 레이어는 Arbitrum Orbit에 의해 구동되며 가스 비용이 없고 트랜잭션은 FIFO(선입선출) 방식으로 정렬됩니다. 프로토콜 레이어는 체인을 거래 지원을 위한 모듈식 구성 요소(예: PnL 정산, 증거금 요구 사항, 청산)로 분리하여 DeFi 애플리케이션의 수직적 통합을 직접 다룹니다.

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

_검토 커밋 해시_ - [39b31762f0fe5836397c87ba78fd6cd11f147a9f](https://github.com/Reya-Labs/reya-network/tree/39b31762f0fe5836397c87ba78fd6cd11f147a9f)

_수정 검토 커밋 해시_ - [29b5286569b08c63b2c94365b04434a5d24a7a03](https://github.com/Reya-Labs/reya-network/tree/29b5286569b08c63b2c94365b04434a5d24a7a03)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `IDepositModule`
- `IExecutionModule`
- `SignatureHelpers`
- `DepositModule`
- `ExecutionModule`
- `ISharesModule`
- `SharesModule`
- `IConfigurationModule`
- `ConfigurationModule`
- `NodeModule`
- `DivReducerNode`
- `NodeDefinition`
- `IConfigurationModule`
- `ITransfersModule`
- `ISocketControllerWithPayload`
- `ISocketExecutionHelper`
- `BridgingUtils`
- `CoreUtils`
- `DataTypes`
- `Deposits`
- `Errors`
- `Events`
- `Transfers`
- `Withdrawals`
- `ConfigurationModule`
- `DepositsFallbackModule`
- `TransfersModule`
- `Configuration`
- `TokenProxy`
- `IERC20TokenModule`
- `FeatureFlagSupport`
- `FeatureFlagModule`
- `ERC20TokenModule`
- `OwnerUpgradeModule`
- `IOrderModule`
- `OrderModule`

# 발견 사항

# [C-01] 사용자가 앱 체인에서 자금을 다시 브리징 할 수 없음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

프로토콜은 앱 체인에서 자금을 다시 브리징 하기 위해 네이티브 코인으로 수수료를 지불해야 합니다:

```solidity
        (uint256 tokenFees, uint256 nativeFees) =
            getFees(withdrawToken, socketController, socketConnector, socketMsgGasLimit, socketPayloadSize);
        if (tokenAmount > tokenFees) {
            uint256 tokensToWithdraw = tokenAmount - tokenFees;
@>          socketController.bridge{ value: nativeFees }({
                receiver_: receiver,
                amount_: tokensToWithdraw,
                msgGasLimit_: socketMsgGasLimit,
                connector_: socketConnector,
                execPayload_: abi.encode(),
                options_: abi.encode()
            });
```

Periphery는 브리지와 상호 작용하는 모듈입니다. 문제는 이러한 계약 중 어느 것도 ETH를 받을 수 있는 `payable` 함수가 없다는 것입니다.

```solidity
contract PeripheryRouter is
    ConfigurationModule,
    DepositsModule,
    DepositsFallbackModule,
    OrderModule,
    TransfersModule,
    WithdrawalsModule,
    OwnerUpgradeModule,
    ERC721ReceiverModule,
    FeatureFlagModule
{ }

contract PeripheryProxy is UUPSProxyWithOwner, PeripheryRouter {
    constructor(
        address firstImplementation,
        address initialOwner
    )
        UUPSProxyWithOwner(firstImplementation, initialOwner)
    { }
}
```

## 권장 사항

PeripheryRouter.sol이 `receive() payable` 함수가 있는 모듈을 상속받도록 하십시오.

# [C-02] 공격자가 큰 `socketPayloadSize`를 지정하여 Periphery를 고갈시킬 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

사용자가 프로토콜에서 자금을 인출할 때, 토큰은 `receiver` 주소로 다른 체인으로 브리징 됩니다. 브리징 비용을 지불하는 데 사용되는 수수료는 `gasLimit` 및 `payloadSize`를 기반으로 합니다:

```solidity
    function getFees(...)
        internal
        view
        returns (uint256 feeInToken, uint256 nativeFees)
    {
@>      nativeFees = controller.getMinFees(connector, gasLimit, payloadSize);
        feeInToken = Configuration.getStaticWithdrawFee(token, connector);
    }
```

사용자는 매우 높은 `payloadSize`를 설정하기만 하면 프로토콜이 높은 수수료를 지불하게 됩니다:

```solidity
    function executeBridging(...)
        internal
    {
        ISocketControllerWithPayload socketController =
            ISocketControllerWithPayload(Configuration.getController(withdrawToken));

        (uint256 tokenFees, uint256 nativeFees) =
            getFees(withdrawToken, socketController, socketConnector, socketMsgGasLimit, socketPayloadSize);
        if (tokenAmount > tokenFees) {
            uint256 tokensToWithdraw = tokenAmount - tokenFees;
@>          socketController.bridge{ value: nativeFees }({
                receiver_: receiver,
                amount_: tokensToWithdraw,
                msgGasLimit_: socketMsgGasLimit,
                connector_: socketConnector,
                execPayload_: abi.encode(),
                options_: abi.encode()
            });
            withdrawToken.safeTransfer(OwnableStorage.getOwner(), tokenFees);
        } else {
            revert Errors.NotEnoughFees(tokenAmount, tokenFees);
        }
    }
```

브리징에 사용되는 Socket은 초과 `msg.value`를 다시 보내지 않습니다. 초과된 `msg.value`를 `executionFee`로 처리합니다:
[링크](https://github.com/SocketDotTech/socket-DL/blob/1766c10c0e4dee08db1dc24f0ca8a0b469232a57/contracts/ExecutionManager.sol#L246-L250)

참고로 현재 `payloadSize`는 수수료 계산에 사용되지 않지만 향후 버전에서는 사용될 예정입니다:
[링크](https://github.com/SocketDotTech/socket-DL/blob/1766c10c0e4dee08db1dc24f0ca8a0b469232a57/contracts/ExecutionManager.sol#L275)

## 권장 사항

인수 `socketPayloadSize`를 제거하고 대신 0을 사용하십시오.

# [C-03] DivReducerNode에서 소수점이 잘못 처리됨

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

Node DivReducer는 Redstone 오라클인 2개의 상위를 갖고 2개의 가격을 결합해야 합니다. 예를 들어, ETH/USDC 가격을 책정하려면 2개의 가격을 가져와 `(ETH/USD) / (USDC/USD)`를 나눕니다.

문제는 Redstone 오라클의 기본 소수점이 8자리인데 코드는 1e18 산술을 사용한다는 것입니다:

```solidity
    function process(NodeOutput.Data[] memory parentNodeOutputs) internal pure returns (NodeOutput.Data memory) {
        if (parentNodeOutputs[1].price == 0) {
            revert InvalidPrice();
        }

@>      uint256 price = divUintUint(parentNodeOutputs[0].price, parentNodeOutputs[1].price).unwrap();
        uint256 timestamp = (parentNodeOutputs[0].timestamp + parentNodeOutputs[1].timestamp) / 2;

        return NodeOutput.Data({ price: price, timestamp: timestamp });
    }


function divUintUint(uint256 a, uint256 b) pure returns (UD60x18) {
    return UD60x18.wrap(a).div(UD60x18.wrap(b));
}
```

여기서 기본 소수점이 8임을 확인할 수 있습니다:
[링크](https://github.com/redstone-finance/redstone-oracles-monorepo/blob/9d10a48aad7a2ccb5f3f48396d970fd63761dbce/packages/on-chain-relayer/contracts/price-feeds/PriceFeedBase.sol#L46-L53)

## 권장 사항

RedstoneOracle의 가격을 해당 오라클의 소수점으로 정규화하십시오. 그 후에만 내부 계산에 사용하십시오.

# [H-01] 예치금 폴백(fallback) 브리징 중에 사용자가 토큰을 잃을 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

DepositsFallbackModule은 예치가 되돌려지는 상황을 처리하고 사용자 자금의 브리징을 다시 시작합니다. 소스 체인에서 자금을 다시 브리징 하기 위해 Reya 체인의 예치금 `receiver` 주소를 사용한다는 점에 유의하십시오:

```solidity
        try DepositsModule(address(this)).depositPassivePool(inputs) { }
        catch {
            BridgingUtils.executeBridging({
                withdrawToken: usdc,
                socketConnector: fallbackData.socketConnector,
                socketMsgGasLimit: fallbackData.socketMsgGasLimit,
                tokenAmount: inputs.amount,
@>              receiver: inputs.owner,
                socketPayloadSize: fallbackData.socketPayloadSize
            });
        }
```

이는 소스 체인의 `inputs.owner` 주소가 Reya 체인의 동일한 사람 소유라고 잘못 가정합니다. 가정이 보장되지 않는 2가지 경우가 있습니다:

1. 계정 추상화 지갑 구현
2. 구 버전의 Safe 다중 서명 (https://rekt.news/wintermute-rekt/)

## 권장 사항

FallbackData 구조체에 `receiver` 인수를 추가하고 DepositsFallbackModule.sol에서 `inputs.accountOwner` 대신 사용하십시오.

# [M-01] `tokenAmount` 검증 부족으로 인한 잠재적 미세 잔액 도난 (Dust Theft)

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

Periphery 기능을 사용하면 Core 및 Passive Pool의 예치, 인출 및 전송 기능과의 통합을 포함하여 소스 체인과 프로토콜 간의 자금을 브리징 할 수 있습니다. 대상 체인에서 예치 작업이 실패할 때 문제가 발생합니다. `DepositsFallbackModule`은 이 실패를 포착하고 Socket 브리지를 통해 소스 체인에서 사용자에게 환불하도록 설계되었습니다. 문제는 `tokenAmount`가 `tokenFees`(고정 수수료)보다 낮을 때 발생하며, 이로 인해 수수료 부족으로 트랜잭션이 되돌려지고 결과적으로 `tokenAmount`가 Periphery에 갇히게 됩니다. 이 시나리오는 `BridgingUtils::executeBridging` 함수에서 사용자 입력 `tokenAmount`와 `bridgeAmount` 간의 검증이 없기 때문에 악용 가능해집니다. 공격자는 Periphery의 잔액(이전 사용자의 미세 잔액에서 누적됨)과 동일한 `tokenAmount`와 다른 `bridgeAmount`로 `DepositsFallbackModule::depositPassivePool`을 호출하여 이를 악용할 수 있습니다. 이로 인해 `DepositsModule::depositPassivePool`에서 되돌리기가 발생하고 `BridgingUtils::executeBridging` 함수가 트리거 되어 Periphery의 잔액이 다른 체인의 공격자에게 다시 브리징 됩니다. 이 문제를 통해 공격자는 Periphery에서 누적된 미세 잔액을 빼돌릴 수 있습니다.

## 권장 사항

이 취약점을 완화하고 잠재적인 미세 잔액 도난으로부터 보호하려면 다음을 권장합니다:

1. 브리징 실패 시 사용자가 대상 체인에서 토큰을 회수할 수 있는 메커니즘을 구현하십시오.

2. `BridgingUtils::executeBridging` 함수 내의 검증을 강화하여 `tokenAmount`가 `bridgeAmount`와 정확히 일치하는지 확인하십시오. 이렇게 하면 공격이 발생하는 불일치를 방지할 수 있습니다.

# [M-02] 평균 타임스탬프 계산으로 인한 `DivReducer`의 오래된 가격 데이터

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

시스템 내의 `DivReducer` 함수는 두 입력 노드의 가격 몫을 계산하도록 설계되었으며, 일반적으로 직접 피드를 사용할 수 없을 때 대체 통화 기준으로 자산 가격을 도출하는 데 사용됩니다. 이 기능의 중요한 부분은 출력의 `updated_at` 타임스탬프를 계산하는 것인데, 현재 두 입력 노드의 타임스탬프 평균을 냅니다. 이 접근 방식은 상당한 위험을 초래합니다. 한 입력 노드가 매우 최근의 타임스탬프를 제공하고 다른 노드가 상당히 오래된 경우 평균 타임스탬프가 최신성 확인을 잘못 통과하여 출력이 실제보다 최신인 것처럼 표시될 수 있습니다. 이로 인해 중요한 재무 계산에 오래된 가격 데이터가 사용될 수 있으며, 시기적절한 의사 결정을 위해 이 피드의 정확성에 의존하는 모든 종속 시스템에 잠재적으로 영향을 미칠 수 있습니다.

## 권장 사항

오래된 데이터를 사용할 위험을 완화하고 `DivReducer` 노드 출력의 신뢰성을 높이려면 `DivReducerNode` 출력의 `updated_at` 타임스탬프를 결정하는 로직을 수정하십시오. 입력 노드의 타임스탬프를 평균 내는 대신 두 타임스탬프 중 최소값을 사용하십시오. 이 접근 방식은 데이터 최신성에 대한 가장 보수적인 추정치를 우선시하여 출력 타임스탬프가 데이터의 신선도를 정확하게 반영하도록 합니다.

# [M-03] 오라클 가격 데이터의 가격 신선도 검증 부족

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`getOraclePrice` 및 `getCollateralExchangeInfo` 함수는 가격 정보와 이 가격의 신선도를 나타내는 타임스탬프가 포함된 `NodeOutput.Data`를 검색합니다. 이러한 함수가 타임스탬프를 기반으로 데이터의 신선도를 확인하지 않고 가격 데이터를 직접 사용하는 문제가 확인되었습니다. 이러한 간과로 인해 중요한 재무 계산이나 의사 결정 과정에서 오래되거나 구식인 가격 데이터가 사용되는 시나리오가 발생할 수 있습니다.

## 권장 사항

이 취약점을 해결하고 시스템 전체에서 사용되는 가격 데이터의 신뢰성을 보장하기 위해 `getOraclePrice` 및 `getCollateralExchangeInfo` 함수 모두에 `NodeOutput.Data`의 타임스탬프를 미리 정의된 신선도 임계값과 비교하는 로직을 도입하십시오.

# [M-04] 잘못된 확인으로 인해 유효하지 않은 노드가 등록될 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

DivReducer 노드는 2개의 상위 노드를 가져야 합니다. 등록 중에 노드가 유효한지 확인하지만 상위가 유효하지 않은 경우 아무 작업도 수행하지 않습니다:

```solidity
    function _isValidNodeDefinition(NodeDefinition.Data memory nodeDefinition) internal view returns (bool valid) {
        if (nodeDefinition.nodeType == NodeDefinition.NodeType.DIV_REDUCER) {
            //check if parents are processable
@>          _hasValidParentNodeDefinitions(nodeDefinition);
        }

        ...
    }

    function _hasValidParentNodeDefinitions(NodeDefinition.Data memory nodeDefinition)
        internal
        view
        returns (bool valid)
    {
        for (uint256 i = 0; i < nodeDefinition.parents.length; i++) {
            NodeDefinition.Data memory nodeDef = _getNode(nodeDefinition.parents[i]);
            if (!_isValidNodeDefinition(nodeDef)) {
                return false;
            }
        }
        return true;
    }
```

## 권장 사항

```diff
    function _isValidNodeDefinition(NodeDefinition.Data memory nodeDefinition) internal view returns (bool valid) {
        if (nodeDefinition.nodeType == NodeDefinition.NodeType.DIV_REDUCER) {
            //check if parents are processable
-           _hasValidParentNodeDefinitions(nodeDefinition);
+          if( !_hasValidParentNodeDefinitions(nodeDefinition)) return false;
        }
        ...
    }
```

# [L-01] 서명 기능에서 EIP-712 사양 미준수

`calculateDigest` 및 `hashExecuteBySig` 함수는 현재 서명 검증을 위한 메시지의 인코딩 및 해싱에 관한 `EIP-712` 사양을 준수하지 않습니다. `EIP-712`는 이더리움으로 입력된 데이터 서명을 표준화하여 검증 가능하고 이해할 수 있는 메시지를 생성하는 안전하고 규정 준수 방법을 제공하는 것을 목표로 합니다. 사양에 따르면 올바른 인코딩 형식은 `"\x19\x01" ‖ domainSeparator ‖ hashStruct(message)`이며, `domainSeparator`는 `hashStruct(eip712Domain)`의 결과이고, `eip712Domain`은 `name`, `version`, `chainId`, `verifyingContract`, `salt`와 같은 필드를 포함하는 구조체입니다. 이러한 필드는 서명의 무결성과 도메인 특이성을 보장하여 특정 공격에 대한 보안을 강화하는 데 필수적입니다.

현재 구현에서 이 표준을 벗어나면 적절한 방식으로 인코딩을 수행하는 EIP712 호환 지갑 또는 도구와의 통합 실패가 발생할 수 있으며, 사용자는 확인할 수 있는 명확한 메시지 대신 무작위 바이트에 서명하도록 요청받게 됩니다.

EIP-712 표준을 완전히 준수하고 보안과 신뢰성으로 널리 인정받는 OpenZeppelin 라이브러리의 `EIP712.sol` 구현을 채택하는 것이 좋습니다. 이 변경은 입력된 데이터 서명 및 검증에 대한 이더리움의 모범 사례와의 일관성을 보장하여 기능에 미치는 영향을 최소화하면서 프로토콜의 전반적인 보안 태세를 강화할 것입니다.

# [L-02] ecrecover 프리컴파일 사용 시 서명 가변성

프로토콜의 현재 `ecrecover` 프리컴파일 사용은 서명 가변성으로 인한 보안 우려를 야기합니다. 구체적으로, 서명의 `s` 및 `v` 구성 요소를 변경하여 동일한 해시 및 서명자에 해당하는 다르지만 유효한 서명을 생성할 수 있는 가능성으로 인해 취약점이 발생합니다. 이 문제는 재생 공격의 위험을 완화하는 시스템의 서명 체계 내에 논스(nonce)가 구현되어 있기 때문에 현재 프로토콜 보안에 직접적인 위협이 되지는 않습니다.

그러나 이러한 형태의 서명 가변성을 해결하는 것은 시스템 내의 예기치 않은 상호 작용이나 변경 사항으로 인해 발생할 수 있는 잠재적인 미래의 취약점이나 악용에 대비하여 프로토콜을 강화하기 위한 모범 사례로 간주됩니다. OpenZeppelin의 ECDSA 라이브러리는 서명이 표준적이고 엄격하게 변경 불가능하도록 보장하는 이 문제에 대한 솔루션을 제공합니다.
