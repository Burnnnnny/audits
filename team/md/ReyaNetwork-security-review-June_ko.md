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

_검토 커밋 해시_ - [87ee315c30023bae6ef1af364193189f1e05e7f3](https://github.com/Reya-Labs/reya-network/tree/87ee315c30023bae6ef1af364193189f1e05e7f3)

_수정 검토 커밋 해시_ - [f522b5f203975d2bb9daaa397a52fe853d26fab5](https://github.com/Reya-Labs/reya-network/tree/f522b5f203975d2bb9daaa397a52fe853d26fab5)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Deposits`
- `DepositsModule`
- `Withdrawals`
- `WithdrawalsModule`
- `DataTypes`
- `BridgingUtils`
- `IDepositsModule`
- `IWithdrawalsModule`

# 발견 사항

# [L-01] 0 금액 확인 누락

프로토콜 로직은 계정에서의 인출 및 브리징 아웃 0 금액을 제한합니다.

```solidity
    function withdrawMA(WithdrawMAInputs memory inputs) internal {
        address rUSDProxy = GlobalConfiguration.getRUSDProxyAddress();

        bytes memory extraSignatureData = abi.encode(inputs.receiver, inputs.chainId, inputs.socketMsgGasLimit);

>>      CoreUtils.coreWithdraw(inputs.accountId, inputs.token, inputs.tokenAmount, inputs.sig, extraSignatureData);
<...>
    function coreWithdraw(
        uint128 accountId,
        address token,
        uint256 tokenAmount,
        EIP712Signature memory sig,
        bytes memory extraSignatureData
    )
        internal
    {
        IExecutionModule(GlobalConfiguration.getCoreProxyAddress()).executeBySig({
            accountId: accountId,
            commands: singleCoreCommand(CommandType.Withdraw, token, tokenAmount),
            sig: sig,
            extraSignatureData: extraSignatureData
        });
    }
<...>
```

```
<...>
    function executeBySig(
        uint128 accountId,
        Command[] calldata commands,
        EIP712Signature memory sig,
        bytes memory extraSignatureData
    )
        external
        override
        returns (bytes[] memory outputs, MarginInfo memory usdNodeMarginInfo)
    {
        // note, signature is only used for accountId check, if there are batch match orders then counterparty
        // permissions are checked directly

        Account.Data storage account = Account.exists(accountId);
        address accountOwner = AccountRBAC.load(account.id).owner;

        uint256 incrementedNonce = Signature.incrementSigNonce(accountOwner);

        Signature.validateRecoveredAddress(
            Signature.calculateDigest(
                hashExecuteBySig(accountId, commands, incrementedNonce, sig.deadline, extraSignatureData)
            ),
            accountOwner,
            sig
        );

>>      return _execute(account, commands);
<...>
```

```
<...>
    function _execute(
        Account.Data storage account,
        Command[] calldata commands
    )
        internal
        returns (bytes[] memory outputs, MarginInfo memory usdNodeMarginInfo)
    {
        account.ensureAccess();

        // execution
        outputs = new bytes[](commands.length);

        for (uint256 i = 0; i < commands.length; i++) {
>>         outputs[i] = executeCommand(account, commands[i]);
<...>
```

```
<...>
        if (command.commandType == CommandType.Withdraw) {
            (address collateral, uint256 collateralAmount) = abi.decode(command.inputs, (address, uint256));
>>          if (collateralAmount == 0) {
                revert Errors.ZeroWithdraw(account.id, collateral);
```

그러나 `Withdrawals.withdraw`에 0 금액 확인이 없기 때문에 `WithdrawalsModule.withdraw` 함수를 통해 주소에 대해 수행될 수 있습니다.

```solidity
    function withdraw(WithdrawInputs memory inputs) internal {
        address rUSDProxy = GlobalConfiguration.getRUSDProxyAddress();

        inputs.token.safeTransferFrom(msg.sender, address(this), inputs.tokenAmount);

        address withdrawToken = inputs.token;

        if (inputs.token == rUSDProxy) {
            IWrappedERC20(rUSDProxy).withdraw(inputs.tokenAmount);
            withdrawToken = IWrappedERC20(rUSDProxy).getUnderlyingAsset();
        }

        BridgingUtils.executeDirectBridging({
            withdrawToken: withdrawToken,
            chainId: inputs.chainId,
            socketMsgGasLimit: inputs.socketMsgGasLimit,
            tokenAmount: inputs.tokenAmount,
            receiver: inputs.receiver
        });
    }
```

`inputs.tokenAmount` 매개변수가 0보다 큰지 확인하는 것을 고려하십시오.

# [L-02] 브리징 비용에 대한 검증 부족

`executeDirectBridging` 함수는 인출에 사용되며 인출자가 브리징 비용을 부담할 것을 예상하여 `msg.value`에 의존합니다. 그러나 이 금액에 대한 검증은 없습니다. 제공된 금액이 필요한 것보다 낮으면 트랜잭션은 Socket 브리지 측에서 단순히 되돌려집니다. 그러나 금액이 훨씬 더 높으면 Socket 브리지는 필요한 금액을 초과하는 추가 수수료를 실행 수수료로 간주합니다.

**출처**: https://github.com/SocketDotTech/socket-DL/blob/42f1703c427246661d84d94e5fcb77e050c1a784/contracts/ExecutionManager.sol#L249-L250

```solidity
// any extra fee is considered as executionFee
executionFee = msgValue - transmissionFees - switchboardFees_;
```

이러한 제한 부족으로 인해 사용자는 과도한 브리징 비용으로 인해 인출 중에 과다 지출을 할 수 있습니다.

`msg.value`가 예상 브리징 비용에 대해 허용 가능한 범위 내에 있는지 확인하는 검증 메커니즘을 도입하십시오. 이는 최소 수수료를 계산하여(다른 `withdrawMA` 및 `withdrawPassivePool`과 마찬가지로) 브리징 작업을 진행하기 전에 `msg.value`가 필요한 금액보다 과도하게 높지 않은지 확인하여 수행할 수 있습니다. 이렇게 하면 사용자가 실수로 인출에 대해 초과 지불하는 것을 방지할 수 있습니다.
