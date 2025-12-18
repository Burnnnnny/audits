# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **Ion-Protocol/ion-boring-vault** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Ion 정보 (About Ion)

Ion Protocol은 스테이킹 및 리스테이킹된 자산을 위해 구축된 탈중앙화 머니 마켓입니다. 차용자는 수익 창출 스테이킹 자산을 담보로 WETH를 빌릴 수 있으며, 대출자는 차용자 담보에 의해 생성된 부스팅된 스테이킹 수익에 노출될 수 있습니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

_검토 커밋 해시:_

- [d29fee34dcc0e6188688273df9ad0888003a589f](https://github.com/Ion-Protocol/ion-boring-vault/tree/d29fee34dcc0e6188688273df9ad0888003a589f)
- [65678ddca45e33ebe1a49e368f12342e66788221](https://github.com/Ion-Protocol/ion-boring-vault/tree/65678ddca45e33ebe1a49e368f12342e66788221)

_수정 검토 커밋 해시_ - [5a6e990d1dac7c4c0eda445e25ff7aabec8afbc0](https://github.com/Ion-Protocol/ion-boring-vault/tree/5a6e990d1dac7c4c0eda445e25ff7aabec8afbc0)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `CrossChainLayerZeroTellerWithMultiAssetSupport`
- `CrossChainTellerBase`
- `OAppAuth`
- `OAppAuthCore`
- `OAppAuthReceiver`
- `OAppAuthSender`
- `TellerWithMultiAssetSupport`
- `BoringVaultCrossChainDepositor`
- `BoringVaultL2OFT`
- `BoringVaultOFTAdapter`

# 발견 사항 (Findings)

# [H-01] 사용자가 `shareUnlockTime`을 우회할 수 있음 (Users can bypass `shareUnlockTime`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

사용자가 `depositAndBridge`를 호출하면 즉시 토큰을 금고로 전송한 다음 사용자는 발행된 지분을 받고 목적지 체인으로 브리징합니다.

```solidity
    function depositAndBridge(ERC20 depositAsset, uint256 depositAmount, uint256 minimumMint, BridgeData calldata data) external payable{
        uint shareAmount = _erc20Deposit(depositAsset, depositAmount, minimumMint, msg.sender);
        bridge(shareAmount, data);
    }
```

목적지 체인에서는 목적지 수신자에게 지분을 발행합니다.

```solidity
    function _lzReceive(
        Origin calldata _origin,
        bytes32 _guid,
        bytes calldata payload,
        address,  // Executor address as specified by the OApp.
        bytes calldata  // Any extra data or options to trigger on receipt.
    ) internal override {
        // @audit - should call _afterPublicDeposit if it from depositAndBridge
        // Decode the payload to get the message
        (uint256 shareAmount, address receiver) = abi.decode(payload, (uint256,address));
>>>     vault.enter(address(0), ERC20(address(0)), 0, receiver, shareAmount);
    }
```

```solidity
    function receiveBridgeMessage(address receiver, uint256 shareMintAmount) external{
        if(msg.sender != address(messenger)){
            revert CrossChainOPTellerWithMultiAssetSupport_OnlyMessenger();
        }

        if(messenger.xDomainMessageSender() != peer){
            revert CrossChainOPTellerWithMultiAssetSupport_OnlyPeerAsSender();
        }

>>>     vault.enter(address(0), ERC20(address(0)), 0, receiver, shareMintAmount);
    }
```

브리징 시간(소스에서 목적지까지)이 `shareLockPeriod`보다 짧은 경우 이 작업을 악용할 수 있습니다. 사용자는 `shareLockPeriod`보다 빨리 자신의 지분을 양도할 수 있게 됩니다.

## 권장 사항

크로스체인 작업에 추가 데이터 또는 플래그를 전달하고, `depositAndBridge`에서 호출된 경우 목적지 체인의 `receiver`에 대해 `_afterPublicDeposit`을 호출하는 것을 고려하십시오.

# [M-01] 지원되는 자산에 대한 확인 누락 (Missing check for supported asset)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`depositAndBridge` 함수에는 현재 예치 자산이 금고에서 지원되는지 확인하는 데 필요한 유효성 검사가 없습니다. 이러한 누락으로 인해 공격자는 사용자 지정 토큰을 예치 자산으로 사용하여 금고가 승인된 자산으로 뒷받침되지 않는 일부 지분을 발행하도록 오도할 수 있습니다.

```solidity
function depositAndBridge(ERC20 depositAsset, uint256 depositAmount, uint256 minimumMint, BridgeData calldata data) external payable{
    uint shareAmount = _erc20Deposit(depositAsset, depositAmount, minimumMint, msg.sender);
    bridge(shareAmount, data);
}
```

현재 이 공격은 `AccountantWithRateProviders` 계약의 일부 관리 변수에 의존하기 때문에 악용되지 않을 수 있습니다. 그러나 이 버그는 프로토콜이 지원되는 자산을 더 이상 사용하지 않지만 `AccountantWithRateProviders`에서 가격 피드를 제거하는 것을 잊은 경우와 같은 일부 시나리오에서 우려될 수 있습니다.

## 권장 사항

이 문제를 해결하려면 `depositAndBridge` 함수에 예치 자산이 실제로 금고에서 지원되는지 확인하는 검사를 통합하는 것이 좋습니다:

```diff
    function depositAndBridge(ERC20 depositAsset, uint256 depositAmount, uint256 minimumMint, BridgeData calldata data) external payable{
+       require(isSupported[depositAsset]);
        uint shareAmount = _erc20Deposit(depositAsset, depositAmount, minimumMint, msg.sender);
        bridge(shareAmount, data);
    }
```

# [M-02] 목적지 체인에서 `allowMessagesFrom`을 확인하지 않음 (Not checking `allowMessagesFrom` on the destination chain)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

크로스체인 텔러 계약에는 지원되는 각 체인에 대한 구성이 있습니다. 메시지 수신만 허용(`allowMessagesFrom`), 메시지 전송만 허용(`allowMessagesTo`), 또는 둘 다 true 또는 false로 구성하도록 별도로 구성할 수 있습니다.

```solidity
    function addChain(
        uint32 chainSelector,
        bool allowMessagesFrom,
        bool allowMessagesTo,
        address targetTeller,
        uint64 messageGasLimit
    ) external requiresAuth {
        if (allowMessagesTo && messageGasLimit == 0) {
            revert CrossChainTellerBase_ZeroMessageGasLimit();
        }
        selectorToChains[chainSelector] = Chain(allowMessagesFrom, allowMessagesTo, targetTeller, messageGasLimit);

        emit ChainAdded(chainSelector, allowMessagesFrom, allowMessagesTo, targetTeller, messageGasLimit);
    }
```

그러나 크로스체인 메시지를 수신할 때 `allowMessagesFrom` 플래그를 고려하지 않습니다.

```solidity
    function _lzReceive(
        Origin calldata _origin,
        bytes32 _guid,
        bytes calldata payload,
        address,  // Executor address as specified by the OApp.
        bytes calldata  // Any extra data or options to trigger on receipt.
    ) internal override {
        // @audit - not checking "allowMessagesFrom"
        // Decode the payload to get the message
        (uint256 shareAmount, address receiver) = abi.decode(payload, (uint256,address));
        vault.enter(address(0), ERC20(address(0)), 0, receiver, shareAmount);
    }
```

```solidity
    function receiveBridgeMessage(address receiver, uint256 shareMintAmount) external{
        // @audit - not checking receive chain from?
        if(msg.sender != address(messenger)){
            revert CrossChainOPTellerWithMultiAssetSupport_OnlyMessenger();
        }

        if(messenger.xDomainMessageSender() != peer){
            revert CrossChainOPTellerWithMultiAssetSupport_OnlyPeerAsSender();
        }

        vault.enter(address(0), ERC20(address(0)), 0, receiver, shareMintAmount);
    }
```

이로 인해 잠재적으로 문제가 발생할 수 있습니다. 예를 들어 지원되는 체인 구성이 목적지 체인으로의 메시지 전송만 허용하고(`allowMessagesTo`가 true) 해당 체인에서 메시지를 수신하는 것은 허용하지 않는 경우(`allowMessagesFrom`이 false로 설정됨)입니다. 또 다른 시나리오는 다른 체인에 악의적인 활동이 있어 해당 체인으로부터의 메시지 수신을 방지해야 하는 비상 상황의 경우입니다.

## 권장 사항

다른 체인에서 메시지를 수신할 때 `allowMessagesFrom`을 확인하십시오.

# [L-01] `shareUnlockTime`이 기존 지분에 영향을 미침 (`shareUnlockTime` will impact existing shares)

사용자가 Teller를 통해 예치하면 `shareUnlockTime`이 업데이트되어 `shareUnlockTime`이 지날 때까지 지분을 전송할 수 없습니다. 그러나 이 설계는 `shareUnlockTime`의 영향을 받지 않아야 하는 기존 지분에도 영향을 미쳐 잠재적으로 사용자 경험을 방해할 수 있습니다. 사용자의 모든 지분에 영향을 미치는 대신 예치된 지분에만 영향을 미치도록 잠금 해제 구현을 재설계하는 것을 고려하십시오.

# [L-02] `bridge`는 제공된 `msg.value`가 수수료로 충분한지 확인하지 않음 (`bridge` does not check if the provided `msg.value` is enough for the fee)

`CrossChainLayerZeroTellerWithMultiAssetSupport`에는 LayerZero의 모범 사례를 사용하여 작업에 필요한 수수료를 계산하는 데 사용할 수 있는 `_quote`가 있습니다. 그러나 `_bridge`가 호출될 때 제공된 `msg.value`가 `_quote` 추정치와 적어도 동일한지 확인하지 않습니다. 제공된 `msg.value`가 수수료를 충당하기에 충분한지 확인하는 추가 확인을 추가하는 것을 고려하십시오.

# [L-03] `requiresAuth` 수정자 부족 (Lack of `requiresAuth` modifier)

이 함수는 공개적으로 호출 가능하도록 의도되었지만 모든 함수에 여전히 `requiresAuth`가 있는 `TellerWithMultiAssetSupport` 내부의 예치 구현과 일치하지 않습니다. `bridge` 함수 내부에도 수정자를 추가하는 것을 고려하십시오.

# [L-04] 금고가 "이상한" 특성을 보이는 토큰과 호환되지 않음 (Vault is incompatible with tokens exhibiting "weird" traits)

ERC-20 표준을 준수하는 일부 토큰은 리베이스 또는 전송 시 수수료 메커니즘과 같은 특이한 특성을 보입니다. 이러한 토큰을 금고에 통합하면 회계 시스템을 방해하고 사용자에게 재정적 손실을 초래할 수 있습니다.

예를 들어 `stETH`와 같은 리베이스 토큰을 통합하면 자동 잔액 조정(`stETH` 잔액은 블록마다 증가)을 설명하지 않기 때문에 금고의 회계가 손상될 수 있습니다. 마찬가지로 `STA` 또는 `PAXG`와 같은 전송 시 수수료 토큰을 통합하면 금고가 `transferFrom` 호출 중에 요청한 것보다 적은 토큰을 받는 것을 예상하지 않기 때문에 불일치가 발생할 수 있습니다.

회계 문제를 방지하려면 이러한 복잡한 특성을 가진 토큰을 금고에 통합하지 않는 것이 좋습니다. 이 접근 방식은 금고 회계 시스템의 무결성과 정확성을 유지하는 데 도움이 됩니다.

# [L-05] `beforeTransfer`가 브리지 내부에서 확인됨 (`beforeTransfer` is checked inside the bridge)

사용자가 `depositAndBridge`를 호출하면 즉시 토큰을 금고에 예치하고 발행된 지분은 즉시 브리지 함수에 제공되어 지분을 목적지 체인으로 브리징합니다.

```solidity
    function depositAndBridge(ERC20 depositAsset, uint256 depositAmount, uint256 minimumMint, BridgeData calldata data) external payable{
        uint shareAmount = _erc20Deposit(depositAsset, depositAmount, minimumMint, msg.sender);
>>>     bridge(shareAmount, data);
    }
```

```solidity
    function bridge(uint256 shareAmount, BridgeData calldata data) public payable returns(bytes32 messageId){
        if(isPaused) revert TellerWithMultiAssetSupport__Paused();
        if(!selectorToChains[data.chainSelector].allowMessagesTo) revert CrossChainTellerBase_MessagesNotAllowedTo(data.chainSelector);

        if(data.messageGas > selectorToChains[data.chainSelector].messageGasLimit){
            revert CrossChainTellerBase_GasLimitExceeded();
        }

        // Since shares are directly burned, call `beforeTransfer` to enforce before transfer hooks.
        // @audit - should not be checked if called from deposit and bridge
>>>     beforeTransfer(msg.sender);

        // Burn shares from sender
        vault.exit(address(0), ERC20(address(0)), 0, msg.sender, shareAmount);

        messageId = _bridge(shareAmount, data);
        _afterBridge(shareAmount, data, messageId);
    }
```

그러나 브리지 함수는 직접 호출되든 `depositAndBridge`에서 호출되든 관계없이 항상 `beforeTransfer`를 확인합니다. `depositAndBridge` 내부의 예치 작업에서 지분을 브리징하는 것이므로 사용자의 `shareUnlockTime`을 확인해서는 안 됩니다.

```solidity
    function beforeTransfer(address from) public view {
        if (shareUnlockTime[from] >= block.timestamp) revert TellerWithMultiAssetSupport__SharesAreLocked();
    }
```

이 확인은 이전 사용자가 Teller를 통해 자산을 예치하고 아직 잠금 해제 시간을 통과하지 않은 경우 예기치 않은 되돌림을 유발할 수 있습니다.

`depositAndBridge`에서 호출되는 경우 `beforeTransfer` 확인을 건너뛰도록 브리지 함수를 업데이트하는 것을 고려하십시오.

# [L-06] 브리징 시 전송되는 최소 가스 부족으로 인한 지분 손실 (Lack of minimum gas sent on bridging leads to a loss of shares)

크로스체인 작업에서 목적지 체인에서 트랜잭션의 성공을 보장하기 위해 적절한 양의 가스를 보내는 것이 중요합니다. `CrossChainTellerBase` 계약은 현재 가스 양이 프로토콜 관리자가 설정한 최대값을 초과하지 않는지 확인합니다. 그러나 필요한 최소 가스에 대한 확인이 부족합니다.

```solidity
if(data.messageGas > selectorToChains[data.chainSelector].messageGasLimit){
    revert CrossChainTellerBase_GasLimitExceeded();
}
```

사용자가 부주의하게 너무 적은 `messageGas`로 크로스체인 트랜잭션을 보내면 가스 부족으로 인해 트랜잭션이 실패하여 사용자가 브리징 프로세스와 관련된 지분을 잃을 수 있습니다.

이 문제를 완화하려면 `Chain` 구조체 내에 minimumMessageGas 변수를 도입하는 것이 좋습니다. 이 변수는 다른 EVM 체인에서 트랜잭션이 성공하는 데 필요한 최소 가스를 정의합니다. 사용하지 않은 가스는 사용자에게 환불되므로 더 높은 최소값을 설정하는 것이 좋습니다. 이 추가 사항은 낮은 가스 한도로 인해 트랜잭션이 실패하는 것을 방지하는 데 도움이 됩니다.

```diff
 struct Chain{
      bool allowMessagesFrom;
      bool allowMessagesTo;
      address targetTeller;
      uint64 messageGasLimit;
+     uint64 minimumMessageGas
  }

    function bridge(uint256 shareAmount, BridgeData calldata data) public payable returns(bytes32 messageId){
        // ...
        if(data.messageGas > selectorToChains[data.chainSelector].messageGasLimit){
            revert CrossChainTellerBase_GasLimitExceeded();
        }

+       if(data.messageGas < selectorToChains[data.chainSelector].minimumMessageGas){
+           revert CrossChainTellerBase_GasTooLow();
+        }

        // ...
    }
```

또한 [LayerZero 문서](https://docs.layerzero.network/v2/developers/evm/oapp/overview#optional-enforced-options)에서는 소스에서 보낸 메시지가 목적지 체인에서 실행되기에 충분한 가스를 갖도록 보장하여 가스 관련 문제로 인한 실패를 효과적으로 방지하기 위해 "Enforced Options"를 설정할 것을 권장합니다.

# [L-07] `lzReceive` 가스 제한에 대한 추가 및 강제 옵션이 제대로 처리되지 않음 (Extra and enforced options for `lzReceive` gas limit not handled properly)

`BoringVaultCrossChainDepositor.deposit()`은 `gasLimit`를 매개변수로 받습니다. 이 값은 목적지 체인의 Executor가 `lzReceive` 호출에서 사용할 가스 양을 나타내며 `BoringVaultOFTAdapter.send`로 전송된 `SendParam` 구조체의 `extraOptions`에 전달됩니다.

```solidity
File: BoringVaultCrossChainDepositor.sol

    function deposit(
        ERC20 depositAsset,
        uint256 depositAmount,
        uint256 minimumMint,
        address bridgeRecipient,
 @>     uint128 gasLimit
    ) external payable returns (uint256 shares) {
 @>     bytes memory options = OptionsBuilder.newOptions().addExecutorLzReceiveOption(gasLimit, 0);

        depositAsset.safeTransferFrom(msg.sender, address(this), depositAmount);
        shares = teller.deposit(depositAsset, depositAmount, minimumMint);

        if (shares < minimumMint) revert BelowMinimumMint(shares, minimumMint);

        SendParam memory sendParam = SendParam({
            dstEid: dstEid,
            to: bytes32(uint256(uint160(bridgeRecipient))),
            amountLD: shares,
            minAmountLD: shares,
 @>         extraOptions: options,
            composeMsg: "",
            oftCmd: ""
        });

        MessagingFee memory messagingFee = MessagingFee({nativeFee: msg.value, lzTokenFee: 0});

        oftAdapter.send{value: msg.value}({_sendParam: sendParam, _fee: messagingFee, _refundAddress: msg.sender});
    }
```

반면, 목적지 체인의 실행자에 대한 가스 제한은 소유자가 [강제 옵션(enforce option)](https://docs.layerzero.network/v2/developers/evm/oft/quickstart#setting-enforced-options)으로 설정할 수도 있으며, 이는 [통합 체크리스트](https://docs.layerzero.network/v2/developers/evm/troubleshooting/integration-checklist)에서 권장됩니다.

> OApp 또는 OFT 사용자가 모든 트랜잭션에 대해 미리 결정된 양의 가스를 충당하도록 Enforced Options를 구현하고 설정하는 것이 좋습니다. 이 설정은 소스에서 보낸 메시지가 목적지 체인에서 실행되기에 충분한 가스를 갖도록 보장하여 가스 관련 문제로 인한 실패를 효과적으로 방지합니다. 자세한 내용은 Enforced Options를 참조하십시오.

두 가지 가능성이 있습니다:

**1. 프로토콜이 강제 옵션을 설정함**

이 경우 `deposit` 호출자는 가스 제한에 대해 두 번 청구될 수 있습니다. 문서에서 다음과 같은 경고를 볼 수 있습니다:

> 주의: enforcedOptions를 설정할 때 의도치 않게 중복된 \_options 인수를 extraOptions에 전달하지 않도록 하십시오. enforcedOptions와 extraOptions 모두에 동일한 \_options를 전달하면 LayerZero가 중복된 \_options를 가스에 대한 두 개의 별도 요청으로 해석하기 때문에 프로토콜이 소스 체인에서 호출자에게 두 번 청구하게 됩니다.

> 주의: 위에서 설명한 대로 enforcedOptions를 통해 애플리케이션 전체 옵션이 필요한지 아니면 extraOptions를 사용하여 호출별 옵션이 필요한지 결정하십시오. 트랜잭션이 구현한 정확한 설정을 반영하므로 두 매개변수에 대해 사용하는 \_options를 구체적으로 지정하십시오.

**2. 프로토콜이 강제 옵션을 설정하지 않음**

이 경우 `BoringVaultCrossChainDepositor.deposit` 또는 `BoringVaultOFTAdapter.send` 호출자는 가스 제한으로 실행자가 목적지 체인에서 트랜잭션을 실행하기에 충분하지 않은 값을 설정할 수 있으며, 따라서 소스 체인에서 이미 잠긴 토큰을 목적지 체인에서 받지 못할 수 있습니다.

배포 스크립트나 테스트 모두 강제 옵션을 설정하지 않기 때문에 이 시나리오가 더 가능성이 높아 보입니다.

`BoringVaultCrossChainDepositor.deposit`에서 `gasLimit` 매개변수를 제거하고 강제 옵션을 사용하여 목적지 체인의 실행자에 대한 가스 제한을 설정하십시오.

# [L-08] 지원되지 않는 자산에 대한 승인을 취소할 방법이 없음 (No way to revoke approval for unsupported assets)

`BoringVaultCrossChainDepositor`에서 토큰이 Teller에서 지원되는 경우 누구나 최대 승인을 설정할 수 있습니다:

```
    function maxApprove(ERC20 depositAsset) external {
        if (!teller.isSupported(depositAsset)) revert AssetNotSupported();
        depositAsset.approve(address(boringVault), type(uint256).max);
    }
```

그러나 Teller는 지원되는 자산을 추가하거나 제거할 수 있습니다. 제거된 자산의 경우 승인 취소를 고려하십시오.

# [L-09] 네이티브 자산으로 발행하는 경우 브리지가 작동하지 않음 (Bridge does not work if minting with native assets)

## 설명

텔러 계약은 금고 지분을 발행하기 위해 네이티브 자산(`msg.value`)을 허용합니다.

```solidity
        if (address(depositAsset) == NATIVE) {
            if (msg.value == 0) revert TellerWithMultiAssetSupport__ZeroAssets();
            nativeWrapper.deposit{value: msg.value}();
            depositAmount = msg.value;
            shares = depositAmount.mulDivDown(ONE_SHARE, accountant.getRateInQuoteSafe(nativeWrapper));
            if (shares < minimumMint) revert TellerWithMultiAssetSupport__MinimumMintNotMet();
            // `from` is address(this) since user already sent value.
            nativeWrapper.safeApprove(address(vault), depositAmount);
            vault.enter(address(this), nativeWrapper, depositAmount, msg.sender, shares);
```

그러나 크로스체인 브리지에서는 더 이상 이것이 불가능합니다. `teller.deposit()` 호출이 msg.value와 함께 전달되지 않기 때문입니다.

```solidity
shares = teller.deposit(depositAsset, depositAmount, minimumMint);
```

게다가 전달된 모든 네이티브 자산은 크로스체인 호출에 사용됩니다. 따라서 금고에서 지분을 발행하는 데 자산을 사용할 수 없습니다.

# 완화 권장 사항

네이티브 자산 기반 발행을 지원해야 하는 경우 추가 `mintWithAmount`를 전달하고 `teller.deposit{value: mintWithAmount}()` 호출에서 해당 금액만 사용하십시오. 다른 모든 곳에서는 `msg.value`를 eth의 남은 잔액으로 대체하십시오.
