# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**stake-dao/laposte** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# LaPoste 소개

LaPoste는 블록체인 간의 크로스 체인 통신 및 토큰 전송을 간소화하는 유틸리티 계약입니다. 네트워크 간의 상호 작용을 위해 Chainlink의 CCIP를 사용합니다.

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

_검토 커밋 해시_ - [0bf0be29ea9d78aa147cc7a924a4c10dba262669](https://github.com/stake-dao/laposte/tree/0bf0be29ea9d78aa147cc7a924a4c10dba262669)

_수정 검토 커밋 해시_ - [0c1065d364692b396aa4bc49dddf2d18d3975f98](https://github.com/stake-dao/laposte/tree/0c1065d364692b396aa4bc49dddf2d18d3975f98)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `TokenFactory`
- `Token`
- `LaPoste`
- `Adapter`
- `Client`
- `Chains`

# 발견 사항

# [M-01] 프로토콜이 ZkSync 및 Celo에서 작동하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

모든 체인에는 LaPoste.sol, Adapter.sol, TokenFactory.sol의 단일 버전이 포함됩니다.

메시지를 받으면 LaPoste.sol이 모든 체인에서 동일한 주소를 공유한다고 가정합니다:

```solidity
    /// @notice Receives a message from another chain
    /// @param message The received message
    function ccipReceive(Client.Any2EVMMessage calldata message) external override onlyRouter {
        address source = abi.decode(message.sender, (address));
@>      if (source == address(0) || source != laPoste) revert InvalidSender();

        ILaPoste(laPoste).receiveMessage({chainId: getChainId(message.sourceChainSelector), payload: message.data});
    }
```

그러나 [ZkSync는 다른 공식 파생](https://docs.zksync.io/build/developer-reference/ethereum-differences/evm-instructions#address-derivation)을 가지므로 동일한 주소에 배포하는 것은 불가능합니다.

Celo에서도 비슷한 문제가 있습니다: [시드 구문과 다른 주소 파생](https://docs.celo.org/developer/migrate/from-ethereum#things-to-watch-out-for)을 가지므로 동일한 tx 전송자를 Celo에서 재현할 수 없기 때문에 create3는 다른 주소를 초래할 것입니다.

그 반대도 마찬가지입니다. 현재 설계에서는 LaPoste가 해당 체인의 Adapter를 호출할 수 없습니다. 어댑터의 주소가 다르기 때문입니다.

## 권장 사항

ZkSync 및 Celo로/부터 메시지를 보낼 때 특정 주소를 사용하십시오.

# [M-02] 현재 논스(nonce) 구현으로 수동 실행이 작동하지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

CCIP를 통해 메시지/토큰을 보낼 때 대상 체인에서 트랜잭션이 실패하면 CCIP는 트랜잭션을 수동으로 다시 실행(재시도)할 수 있는 옵션을 제공합니다.

트랜잭션이 실패할 수 있는 한 가지 경우는 트리거 된 함수를 실행하기에 가스 제한이 충분하지 않아 대상 블록체인의 수신자 계약이 되돌려지는(revert) 경우입니다(참고: 가스 제한 값은 메시지의 [extraArgs](https://docs.chain.link/ccip/api-reference/client#evmextraargsv1) 매개변수에 설정됨) (출처: [Chainlink 문서](https://docs.chain.link/ccip/concepts/manual-execution#overview))

LaPoste.sol에서 `receiveMessage`가 성공하면 `receivedNonce[chainId]`가 `message.nonce`만큼 증가합니다. 이 함수는 또한 `message.nonce <= receivedNonces[chainId]`를 확인합니다.

```
    function receiveMessage(uint256 chainId, bytes calldata payload) external onlyAdapter {
        ILaPoste.Message memory message = abi.decode(payload, (ILaPoste.Message));

        // Check if the message has already been processed
        if (message.nonce <= receivedNonces[chainId]) revert MessageAlreadyProcessed();
```

이 논스 값은 메시지를 보낼 때 설정되며 매번 1씩 증가합니다. 다음 시나리오를 고려하십시오:

- 메시지 1(소스 체인에 잠긴 토큰 포함)이 대상 체인으로 전송되고 `message.nonce = 1`, `receivedNonces[chainId]= 0`입니다. 확인이 통과하고 발신자는 발행된 토큰을 받으며 `receivedNonces[chainId]`는 1이 됩니다.
- 메시지 2(소스 체인에 잠긴 토큰 포함)가 대상 체인으로 전송됩니다. `message.nonce = 2`, `receivedNonces[chainId]= 1`입니다. 가스 제한에 문제가 발생하여 이 트랜잭션이 되돌려집니다.
- 메시지 3(소스 체인에 잠긴 토큰 포함)이 대상 체인으로 전송됩니다. `message.nonce = 3`, `receivedNonces[chainId]= 1`입니다. 트랜잭션이 통과하고 `receivedNonces[chainId]= 3`이 됩니다. 발신자는 발행된 토큰을 받습니다.
- 메시지 2 발신자가 수동 실행을 통해 메시지를 다시 시도하고 싶어 하지만 `message.nonce = 2`, `receivedNonces[chainId]= 3`이므로 함수는 항상 되돌려집니다. 발신자의 토큰은 소스 체인에 잠겨 있습니다.

## 권장 사항

[문서](https://docs.chain.link/ccip/best-practices#best-practices)의 권장 사항 중 하나는 메시징 순서가 순차적이 되도록 `allowOutOfOrderExecution`을 false로 설정하여 `EVMExtraArgsV2`를 사용하는 것입니다.

# [M-03] 가스가 부족하면 `receiveMessage()`가 되돌려짐

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

프로토콜은 엄격한 방식으로 CCIP 메시징을 사용합니다. 즉, 이전 메시지의 실행이 성공할 때까지 레인(lane) 당 추가 메시지를 처리할 수 없습니다. `LaPoste.receiveMessage()`에서 어떤 작업이라도 되돌릴 수 있게 되면 추가 메시지를 처리할 수 없습니다.

공격자가 자신의 메시지를 되돌리게 만드는 방법입니다. 공격자 계약에 대한 호출을 실행하기 위해 `try catch`를 사용합니다:

```solidity
        /// 2. Execute the message.
        bool success;
        if (message.payload.length > 0) {
@>          try IMessageReceiver(message.to).receiveMessage(chainId, message.sender, message.payload) {
                success = true;
            } catch {
                success = false;
            }
        }
```

문제는 `try catch`가 가스 부족(out-of-gas) 오류를 처리하지 않는다는 것입니다. 따라서 기본적으로 공격자는 이 외부 호출에서 모든 가스를 낭비하여 `LaPoste.receiveMessage()`가 항상 되돌려지게 할 수 있습니다.

공격자는 모든 CCIP 레인에서 이러한 공격을 수행하여 LaPoste 프로토콜의 모든 통신이 DOS 될 수 있습니다. 결과적으로 메인넷의 잠긴 토큰은 영원히 잠기게 되어 사용자가 돈을 잃게 됩니다.

## 권장 사항

ILaposte.Message 구조체에 `executionGasLimit` 필드를 추가하십시오. 그리고 여기 외부 호출에서 이 gasLimit를 지정하십시오:

```diff
        /// 2. Execute the message.
        bool success;
        if (message.payload.length > 0) {
-           try IMessageReceiver(message.to).receiveMessage(chainId, message.sender, message.payload) {
+           try IMessageReceiver(message.to).{gas: message.executionGasLimit}receiveMessage(chainId, message.sender, message.payload) {
                success = true;
            } catch {
                success = false;
            }
        }
```

# [L-01] Adapter.sol이 일부 chainId를 처리하지 않음

라이브러리 `Chains`에는 17개의 체인이 포함되어 있습니다. 그러나 Adapter.sol은 `getChainId()` 및 `getBridgeChainId()` 함수에서 13개의 체인만 처리합니다. 결과적으로 WEMIX, METIS, MODE, KROMA 체인은 프로토콜에서 사용할 수 없습니다.

# [L-02] 대체(fallback) 함수 누락

Chainlink의 [문서](https://docs.chain.link/ccip/best-practices#decoupling-ccip-message-reception-and-business-logic)에 따르면 CCIP 수신자 계약에서 예기치 않은 예외를 원활하게 처리하기 위해 대체 메커니즘을 구현하는 것이 모범 사례로 간주됩니다. 그러나 LaPoste 계약은 이 지침을 따르지 않으므로 특정 시나리오에서 프로토콜이 새 계약을 배포해야 할 수도 있습니다. 대체 함수 구현을 고려하십시오.

# [L-03] 페이로드가 없는 경우 MessageReceived 이벤트가 혼동을 줄 수 있음

LaPoste.sol의 receiveMessage() 함수는 success 변수를 생성하고 `message.payload.length > 0`인 경우 값을 변경한 다음 `MessageReceived` 이벤트를 방출합니다.

```
 bool success;
        if (message.payload.length > 0) {
            try IMessageReceiver(message.to).receiveMessage(chainId, message.sender, message.payload) {
                success = true;
            } catch {
                success = false;
            }
        }
        emit MessageReceived(chainId, message.nonce, message.sender, message.to, message, success);
```

혼동은 `message.payload.length`가 0이고 `success`가 자동으로 false로 설정되지만 트랜잭션이 성공한 경우에 발생합니다.

대상 체인의 이 [트랜잭션](https://app.blocksec.com/explorer/tx/arbitrum/0x11a9a5be3b871d41c88b987483094bb6eb0e010df8c2f14aa256aab0a0ef7212)에서 20번째 줄의 페이로드는 비어 있고 성공 값은 false로 설정되어 있지만 트랜잭션은 성공입니다.

두 가지 유형의 이벤트를 갖고 `message.payload.length`가 0보다 크거나 0인 경우 그에 따라 이벤트를 방출하십시오.

```
if (message.payload.length > 0) {
        emit MessageReceived(chainId, message.nonce, message.sender, message.to, message, success);
} else {
        success = true;
        emit MessageReceivedNoPayload(chainId, message.nonce, message.sender, message.to, message, success);
}
```

# [L-04] EVMExtraArgsV2가 사용되지 않음

CCIP를 사용하여 메시지를 보낼 때 `EVMExtraArgsV1`만 사용되는 것 같습니다.

```
   Client.EVMExtraArgsV1 memory evmExtraArgs = Client.EVMExtraArgsV1({gasLimit: totalGasLimit});
        bytes memory extraArgs = Client._argsToBytes(evmExtraArgs);

        Client.EVM2AnyMessage memory ccipMessage = Client.EVM2AnyMessage({
            receiver: abi.encode(to),
            data: message,
            tokenAmounts: new Client.EVMTokenAmount[](0),
            feeToken: address(0),
            extraArgs: extraArgs
        });
```

Client 계약에는 `EVMExtraArgsV2`가 있지만 사용되지 않습니다.

```
  struct EVMExtraArgsV2 {
        uint256 gasLimit;
        bool allowOutOfOrderExecution;
    }

    function _argsToBytes(EVMExtraArgsV2 memory extraArgs) internal pure returns (bytes memory bts) {
        return abi.encodeWithSelector(EVM_EXTRA_ARGS_V2_TAG, extraArgs);
    }
```

사용하지 않는 코드를 제거하는 것을 고려하십시오.

# [L-05] 초과 msg.value는 사용자에게 반환되어야 함

프로토콜은 CCIP 수수료를 지불하기 위해 LINK 토큰 대신 기본 토큰을 사용합니다. Adapter.sendMessage() 함수는 `msg.value < fee`를 확인하지만 사용자가 실수로 추가 `msg.value`를 보내는 경우 계약에 갇히게 됩니다.

```solidity
  uint256 fee = router.getFee(chainSelector, ccipMessage);

        if (fee == 0) revert InvalidMessage();
        if (msg.value < fee) revert NotEnoughFee();
        return router.ccipSend{value: fee}(chainSelector, ccipMessage);
```

초과된 `msg.value`를 호출자에게 반환하십시오.

# [L-06] Fee-on-Transfer 및 리베이스(Rebasing) 토큰이 제대로 작동하지 않음

이 프로토콜은 크로스 체인 상호 작용을 위한 단일 진입점을 제공하고 안전하고 신뢰할 수 있는 메시지 및 토큰 전송을 위해 Chainlink의 CCIP를 활용합니다.

허용되는 토큰에 대한 언급이 없으므로 FoT(Fee-on-Transfer)/리베이스 토큰도 사용할 수 있다고 가정합니다.

```
>   ITokenFactory(tokenFactory).burn(messageParams.token.tokenAddress, msg.sender, messageParams.token.amount);

            (message.tokenMetadata.name, message.tokenMetadata.symbol, message.tokenMetadata.decimals) =
                ITokenFactory(tokenFactory).getTokenMetadata(messageParams.token.tokenAddress);
```

사용자는 프로토콜과 함께 해당 토큰을 사용할 때 문제에 직면하게 됩니다.

리베이스 토큰의 경우:

사용자는 소스 체인에서 대상 체인으로 다시 동일한 금액만 가져올 수 있으므로 리베이스 보상은 계약에 갇히게 됩니다.

Fee-on-Transfer 토큰의 경우:

대상 체인에서 발행할 때 수수료가 고려되지 않습니다.

토큰을 화이트리스트에 올리는 것이 권장되지만, 프로토콜이 모든 유형의 토큰을 수락하도록 의도한 경우 사용자가 일반적이지 않은 ERC20 토큰을 사용할 때 직면하는 문제를 알고 있는지 확인하십시오.
