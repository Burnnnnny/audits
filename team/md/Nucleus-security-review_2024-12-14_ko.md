# 정보
Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항
스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개
**Ion-Protocol/nucleus-boring-vault** 및 **Ion-Protocol/nucleus-queues** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Nucleus 정보
Ion은 스테이킹 및 리스테이킹된 자산을 위해 구축된 대출 프로토콜입니다. 차용인은 수익 창출 스테이킹 자산을 담보로 WETH를 빌릴 수 있으며, 대출자는 차용인 담보로 생성된 부스트 스테이킹 수익에 노출될 수 있습니다. 이번 감사의 범위는 두 가지 별도 부분으로 구성되었습니다. 하나는 크로스체인 입금 계약을 위해 Hyperlane을 통합하는 것이고, 다른 하나는 CoW 프로토콜과 유사한 솔버(solver) 기반 출금을 용이하게 하는 것입니다.

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
_검토 커밋 해시:_ 
- [fe84445ff7142d37ea2a88fa587a00c7515b4f13](https://github.com/Ion-Protocol/nucleus-boring-vault/tree/fe84445ff7142d37ea2a88fa587a00c7515b4f13)
- [fbe7b5e1489139574ac8c2c3d19dc25fd53f86d9](https://github.com/Ion-Protocol/nucleus-queues/tree/fbe7b5e1489139574ac8c2c3d19dc25fd53f86d9)

_수정 검토 커밋 해시:_ 
- [fee3bafbb38b4587cd349ce0aed665e278a57490](https://github.com/Ion-Protocol/nucleus-boring-vault/tree/fee3bafbb38b4587cd349ce0aed665e278a57490)
- [6728bddf38bbb81ee362f958a2d94e18960273e6](https://github.com/Ion-Protocol/nucleus-queues/tree/6728bddf38bbb81ee362f958a2d94e18960273e6)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `MultiChainHyperlaneTellerWithMultiAssetSupport` 
- `CrossChainTellerBase`
- `MultiChainTellerBase`
- `TellerWithMultiAssetSupport`
- `AtomicQueueUCP` 

# 발견 사항
# [M-01] 영구적으로 손실될 수 있는 브리지 메시지

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`MultiChainHyperlaneTellerWithMultiAssetSupport` 계약에는 `handle` 함수의 `_beforeReceive` 확인을 통해 모든 메시지 처리를 차단하는 일시 중지 메커니즘이 포함되어 있습니다.

```solidity
    function handle(uint32 origin, bytes32 sender, bytes calldata payload) external payable {
        _beforeReceive();
        ...
    }

    function _beforeReceive() internal virtual {
        if (isPaused) revert TellerWithMultiAssetSupport__Paused();
    }
```

이로 인해 크로스체인 브리지 흐름에 취약점이 생성됩니다.

- 사용자가 소스 체인에서 지분을 태우는 `bridge`를 호출하여 브리지 트랜잭션을 시작합니다.
- Hyperlane 릴레이어가 메시지를 선택하고 전달을 시도합니다.
- 릴레이 기간 동안 계약이 일시 중지되면 메시지 전달이 실패합니다.

Hyperlane 문서에 따르면 릴레이어는 무한 재시도를 보장하지 않습니다.

```
메시지의 재시도 횟수는 지수 백오프 전략에 따라 다음 전달 시도를 결정합니다.
현재 릴레이어가 메시지 처리를 중단하는 고정된 최대 재시도 횟수는 없습니다.
그러나 이것이 무기한 재시도를 보장하는 것은 아니며 운영자는 이를 서비스 수준 계약(SLA)으로 의존해서는 안 됩니다.
``` 
https://docs.hyperlane.xyz/docs/protocol/agents/relayer#the-submitter

결과는 다음과 같습니다.
- 소스 체인에서 사용자의 지분이 소각됨
- 대상 체인에서 발행된 지분이 없음
- 복구 메커니즘 없이 자금이 효과적으로 손실됨

## 권장 사항

일시 중지 상태로 인해 `handle` 트랜잭션이 실패하는 경우를 처리하십시오.

# [M-02] shareLockPeriod가 활성 상태일 때 `depositAndBridge()` 실패

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`depositAndBridge` 함수는 입금 후 즉시 브리지 작업을 수행하려고 시도하지만, 주식 잠금(share locking)이 활성화된 경우 모순되는 보안 확인으로 인해 이 시퀀스가 실패합니다.

- `depositAndBridge`는 먼저 `_afterPublicDeposit`을 호출하여 설정합니다: `shareUnlockTime[user] = block.timestamp + shareLockPeriod;`

```solidity
    function depositAndBridge(
        ERC20 depositAsset,
        uint256 depositAmount,
        uint256 minimumMint,
        BridgeData calldata data
    )
        external
        payable
        requiresAuth
        nonReentrant
    {
        ...
        _afterPublicDeposit(msg.sender, depositAsset, depositAmount, shareAmount, shareLockPeriod); // @audit set shareUnlockTime to block.timestamp + shareLockPeriod
        bridge(shareAmount, data);
    }
```

- 그런 다음 즉시 `bridge`를 호출하여 수행합니다: `beforeTransfer(msg.sender);`

```solidity
    function bridge(
        uint256 shareAmount,
        BridgeData calldata data
    )
        public
        payable
        requiresAuth
        returns (bytes32 messageId)
    {
        ...
        // Since shares are directly burned, call `beforeTransfer` to enforce before transfer hooks.
        beforeTransfer(msg.sender); // @audit revert because shareUnlockTime > block.timestamp
        ...
    }
```

- `beforeTransfer` 확인은 `shareUnlockTime > block.timestamp` 인 경우 되돌립니다(revert).

```solidity
    function beforeTransfer(address from) public view {
        if (shareUnlockTime[from] > block.timestamp) revert TellerWithMultiAssetSupport__SharesAreLocked();
    }
```

이로 인해 주식 잠금이 활성화된 경우 사용자는 원자적(atomic) `depositAndBridge` 기능을 사용할 수 없습니다. 대신 그들은 그 사이에 대기 기간을 두고 별도의 거래로 입금 및 브리지를 수행해야 합니다.

## 권장 사항

- `depositAndBridge` 동안 잠금 확인을 위한 우회 플래그를 추가하고 대상 체인에서 잠급니다.

# [M-03] `_bridge()` 및 `_quote()`에 적용되지 않는 사용자 지정 훅(hook)

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`MultiChainHyperlaneTellerWithMultiAssetSupport` 계약의 `_bridge()` 및 `_quote()`는 `setHook()`을 통해 설정된 사용자 정의 사후 디스패치 훅(post-dispatch hook)을 적용하지 못합니다. 대신 Mailbox 계약의 defaultHook을 사용하는 것으로 기본 설정되는 오버로드된 `Mailbox.dispatch()`에 의존합니다.

```solidity
function _bridge(uint256 shareAmount, BridgeData calldata data) internal override returns (bytes32 messageId) {
--- SNIPPED ---
    mailbox.dispatch{ value: msg.value }(
        data.chainSelector, // must be `destinationDomain` on hyperlane
        msgRecipient, // must be the teller address left-padded to bytes32
        _payload,
        StandardHookMetadata.overrideGasLimit(data.messageGas) // Sets the refund address to msg.sender, sets
            // `_msgValue`
            // to zero
    );
}
```

`Mailbox.dispatch()`에는 세 가지 오버로드된 변형이 있습니다. 이 경우 `_bridge()`는 사용자 정의 IPostDispatchHook을 지정하지 않고 버전을 호출하여 `Mailbox` 계약이 `defaultHook`을 자동으로 적용하도록 합니다.

```solidity
// hyperlane-monorepo/solidity/contracts/Mailbox.sol
function dispatch(
    uint32 destinationDomain,
    bytes32 recipientAddress,
    bytes calldata messageBody,
    bytes calldata hookMetadata
) external payable override returns (bytes32) {
    return
        dispatch(
            destinationDomain,
            recipientAddress,
            messageBody,
            hookMetadata,
@>          defaultHook
        );
}
```

결과적으로 `MultiChainHyperlaneTellerWithMultiAssetSupport`는 `Mailbox` 계약의 기본값 대신 사용자 정의 사후 디스패치 훅을 사용하도록 선택하지만 디스패치 프로세스 중에 사용자 정의 훅이 호출되지 않습니다. 이로 인해 사후 디스패치 처리가 잘못되거나 의도하지 않은 결과가 발생할 수 있습니다.

```solidity
// hyperlane-monorepo/solidity/contracts/Mailbox.sol
function dispatch(
    uint32 destinationDomain,
    bytes32 recipientAddress,
    bytes calldata messageBody,
    bytes calldata metadata,
    IPostDispatchHook hook
) public payable virtual returns (bytes32) {
    if (address(hook) == address(0)) {
        hook = defaultHook;
    }
--- SNIPPED ---
    requiredHook.postDispatch{value: requiredValue}(metadata, message);
@>  hook.postDispatch{value: msg.value - requiredValue}(metadata, message);

    return id;
}
```

이 문제는 잘못된 수수료 추정으로 이어질 수 있는 `_quote()`에서도 발생합니다.

## 권장 사항

사용자 정의 훅을 허용하는 `Mailbox` 계약의 오버로드된 `dispatch()` 메서드를 사용하는 것을 고려하십시오.

이 접근 방식은 프로토콜이 훅을 제로 주소로 남겨두기로 결정한 경우 `defaultHook`을 사용하는 것으로 기본 설정되므로 훅이 설정된 경우와 설정되지 않은 경우 모두 처리합니다.

사용자 정의 훅은 Hyperlane 표준 훅과 호환되어야 합니다.

```diff
function _bridge(uint256 shareAmount, BridgeData calldata data) internal override returns (bytes32 messageId) {
--- SNIPPED ---
    mailbox.dispatch{ value: msg.value }(
        data.chainSelector, // must be `destinationDomain` on hyperlane
        msgRecipient, // must be the teller address left-padded to bytes32
        _payload,
-       StandardHookMetadata.overrideGasLimit(data.messageGas) // Sets the refund address to msg.sender, sets
-            // `_msgValue`
-            // to zero
+       StandardHookMetadata.overrideGasLimit(data.messageGas),
+       hook        
    );
}
```

# [L-01] `isAtomicRequestValid` 확인이 `solve` 함수와 일치하지 않음

`isAtomicRequestValid`에 따르면 사용자의 `atomicPrice`가 0이면 잘못된 원자적(atomic) 요청으로 간주됩니다. 그러나 다른 매개변수(`deadline`, `offerAmount`)가 올바르게 구성된 경우 `atomicPrice`가 0이더라도 원자적 요청은 여전히 해결 가능할 수 있습니다. `solve`가 호출되고 사용자의 `atomicPrice`가 0일 때 작업을 되돌리거나(revert) `isAtomicRequestValid`에서 `atomicPrice`가 0인지 확인하는 것을 제거하는 것을 고려하십시오.

# [L-02] `targetTeller` 유효성 검사 부족

`MultiChainTellerBase` 내부에는 구성된 체인(`allowMessagesFrom` / `allowMessagesTo`)에서 메시지 수신을 허용하거나 중지하는 몇 가지 작업이 있습니다. 그러나 `allowMessagesFrom` / `allowMessagesTo`가 true로 설정된 경우 `targetTeller`가 비어 있지 않은 주소인지 확인하지 않습니다. 잘못 구성된 경우 `mailbox.dispatch`가 `msgRecipient`를 빈 주소로 설정하므로 문제가 발생할 수 있습니다. 추가 검증을 추가하는 것을 고려하십시오.

# [L-03] `_quote` 페이로드에 `messageId`가 포함되지 않음

`_quote`는 `shareAmount` 및 `destinationChainReceiver`로 구성된 `_payload`를 생성하고 `mailbox.quoteDispatch`에 전달합니다.

```solidity
    function _quote(uint256 shareAmount, BridgeData calldata data) internal view override returns (uint256) {
>>>     bytes memory _payload = abi.encode(shareAmount, data.destinationChainReceiver);
        bytes32 msgRecipient = _addressToBytes32(selectorToChains[data.chainSelector].targetTeller);

        return mailbox.quoteDispatch(
            data.chainSelector, msgRecipient, _payload, StandardHookMetadata.overrideGasLimit(data.messageGas)
        );
    }
```

그러나 실제 브리지가 수행될 때 페이로드는 `shareAmount`, `destinationChainReceiver` 및 `messageId`로 구성됩니다.

```solidity
    function _bridge(uint256 shareAmount, BridgeData calldata data) internal override returns (bytes32 messageId) {
        // ...

>>>     bytes memory _payload = abi.encode(shareAmount, data.destinationChainReceiver, messageId);

        // Unlike L0 that has a built in peer check, this contract must
        // constrain the message recipient itself. We do this by our own
        // configuration.
        bytes32 msgRecipient = _addressToBytes32(selectorToChains[data.chainSelector].targetTeller);
        mailbox.dispatch{ value: msg.value }(
            data.chainSelector, // must be `destinationDomain` on hyperlane
            msgRecipient, // must be the teller address left-padded to bytes32
            _payload,
            StandardHookMetadata.overrideGasLimit(data.messageGas) // Sets the refund address to msg.sender, sets
                // `_msgValue`
                // to zero
        );
    }
```

`_quote`의 페이로드 내부에도 `messageId`를 제공하는 것을 고려하십시오.

# [L-04] InterchainSecurityModule에 의존하는 위험

`interchainSecurityModule`을 `address(0)`으로 설정하면 프로토콜이 Hyperlane 팀이 언제든지 변경할 수 있는 Hyperlane의 기본 ISM을 사용하게 됩니다. 이는 프로토콜의 통제 없이 보안 위험을 초래합니다.

```solidity
    function setInterchainSecurityModule(IInterchainSecurityModule _interchainSecurityModule) external requiresAuth {
        interchainSecurityModule = _interchainSecurityModule;
        emit SetInterChainSecurityModule(address(_interchainSecurityModule));
    }
```

```solidity
    function setDefaultIsm(address _module) public onlyOwner {
        require(
            Address.isContract(_module),
            "Mailbox: default ISM not contract"
        );
        defaultIsm = IInterchainSecurityModule(_module);
        emit DefaultIsmSet(_module);
    }
```

예를 들어 Hyperlane 팀이 기본 ISM을 `NoopIsm`으로 설정하면 공격자는 `mailbox.process()`를 호출하여 승인되지 않은 공유를 발행할 수 있습니다. NoopIsm은 보안 검증을 수행하지 않기 때문입니다.

NoopIsm: [링크](https://github.com/hyperlane-xyz/hyperlane-monorepo/blob/main/solidity/contracts/isms/NoopIsm.sol)

가능성은 매우 낮지만 프로토콜은 ISM 사용을 모니터링하거나 안전한 ISM을 직접 설정해야 합니다.

# [L-05] `finishSolve()`에 전달된 `assetsForWant`의 과대평가 가능성

요청을 해결하는 동안 외부 `finishSolve()`로 전달된 `assetsForWant` 금액은 `solver`에서 가져온 토큰의 실제 금액보다 과대평가될 수 있습니다.

```solidity
function solve(
--- SNIPPED ---
)
    external
    nonReentrant
{
    if (!isApprovedSolveCaller[msg.sender]) revert AtomicQueue__UnapprovedSolveCaller(msg.sender);
    uint8 offerDecimals = offer.decimals();
    uint256 assetsToOffer = _handleFirstLoop(offer, want, users, clearingPrice, solver);
@>  uint256 assetsForWant = _calculateAssetAmount(assetsToOffer, clearingPrice, offerDecimals);

@>  IAtomicSolver(solver).finishSolve(runData, msg.sender, offer, want, assetsToOffer, assetsForWant);

    _handleSecondLoop(offer, want, users, clearingPrice, solver, offerDecimals);
}
```

```solidity
function _handleSecondLoop(
--- SNIPPED ---
)
    internal
{
    for (uint256 i = users.length; i > 0;) {
        --- SNIPPED ---

        uint256 assetsToUser = _calculateAssetAmount(request.offerAmount, clearingPrice, offerDecimals);    
@>      want.safeTransferFrom(solver, user, assetsToUser);
        --- SNIPPED ---
    }
}
```

`_handleSecondLoop()`에서 want 토큰은 `_calculateAssetAmount()`를 사용하여 처리되는 각 사용자의 `request.offerAmount`에서 파생된 금액을 사용하여 솔버에서 가져옵니다. 계산은 내림 곱셉-나눗셈(flooring multiplication-division) 연산을 사용하여 잠재적인 내림(rounding down)을 초래합니다.

```solidity
function _calculateAssetAmount(
--- SNIPPED ---
)
    internal
    pure
    returns (uint256)
{
    return clearingPrice.mulDivDown(offerAmount, 10 ** offerDecimals);
}
```

결과적으로 `assetsForWant`는 모든 사용자의 누적 `assetsToOffer` 값에서 파생되지만 실제 토큰 전송 금액은 각 사용자의 요청에 대해 내림되므로 `assetsForWant`가 `solver`에서 실제로 전송된 토큰보다 과대평가될 수 있습니다.

이러한 `assetsForWant`의 불일치는 `viewSolveMetaData()`에서도 발생합니다.

대신 각 사용자에 대해 가져올 실제 `want` 토큰의 합계를 사용하는 것을 고려하십시오.
