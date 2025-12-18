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

_검토 커밋 해시_ - [0dee13ca39660bcfae64184163d7cf84cf67c722](https://github.com/Reya-Labs/reya-network/tree/0dee13ca39660bcfae64184163d7cf84cf67c722)

_수정 검토 커밋 해시_ - [56d589baa31fdcdbf6f8bb15618327a1f731d37b](https://github.com/Reya-Labs/reya-network/tree/56d589baa31fdcdbf6f8bb15618327a1f731d37b)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `CollateralPoolPermissions`
- `SignatureHelpers`
- `AccountLiquidation`
- `AutoExchange`
- `AccountModule`
- `AutoExchangeConfigurationModule`
- `CollateralModule`
- `CollateralPoolModule`
- `ExecutionModule`
- `InsuranceFundConfigurationModule`
- `RiskConfigurationModule`
- `Account`
- `AccountRBAC`
- `ConfigurationModule`
- `PassivePerpInformationModule`
- `Market`
- `CoreAccountPermission`
- `Signature`
- `/orders-gateway`

# 발견 사항

# [H-01] 서명 논스(nonce)의 잘못된 증가

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`ExecutionModule.executeBySig()`는 각 서명이 한 번만 사용될 수 있도록 `accountOwner`에 대한 논스를 증가시키기 위해 `incrementSigNonce(accountOwner)`를 수행하여 재생 공격(replay attack)을 방지합니다.

그러나 서명자가 `accountOwner`가 아닌 경우에도 `accountOwner`에 대한 논스를 잘못 증가시킵니다.

이를 통해 누구나 `executeBySig()`를 호출하여 논스를 증가시킴으로써 `accountOwner` 또는 다른 유효한 서명자의 트랜잭션을 DoS(서비스 거부)할 수 있습니다. `accountOwner`에 대한 논스가 증가하면 이전 논스를 사용하여 생성된 서명이 있는 모든 트랜잭션이 거부됩니다.

Reya Network는 가스 요금이 필요하지 않으므로 DoS 공격은 `executeBySig()`를 지속적으로 스팸하여 쉽게 실행될 수 있습니다. 또한 이는 서명자에 대한 권한 확인을 우회하기 위해 0개의 명령(commands)으로 `executeBySig()`를 호출하여 누구나 수행할 수 있습니다.

최악의 경우 공격자는 인출을 DoS하여 자금이 계약 내에 잠길 수 있습니다.

동일한 문제가 `AccountModule.grantAccountPermissionBySig()` 및 `AccountModule.revokeAccountPermissionBySig()`에도 적용됩니다.

```Solidity
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
        Account.Data storage account = Account.exists(accountId);
        address accountOwner = AccountRBAC.load(account.id).owner;
        //@audit this should increment nonce for signer instead of accountOwner
>>>     uint256 incrementedNonce = Signature.incrementSigNonce(accountOwner);
        address recoveredAddress = Signature.generateRecoveredAddress(
            Signature.calculateDigest(
                hashExecuteBySig(accountId, commands, incrementedNonce, sig.deadline, extraSignatureData)
            ),
            sig
        );

        return _execute(accountId, commands, recoveredAddress);
    }
```

## 권장 사항

`accountOwner` 대신 서명자(`recoveredAddress`)의 서명 논스를 증가시키십시오.

# [M-01] 관리자 역할 회피 위험

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`AccountModule::revokeAccountPermission` 함수는 사용자로부터 권한을 제거하도록 설계되었습니다. 그러나 악의적인 관리자는 자신의 권한이 취소되기 전에 `AccountModule::grantAccountPermission`을 호출하여 다른 제어되는 계정에 관리자 권한을 할당함으로써 선제적으로 프론트 러닝을 실행할 수 있습니다. 다음 시나리오를 고려하십시오.

1. `AccountModule::revokeAccountPermission` 함수를 사용하여 관리자에 대한 권한이 취소되고 있습니다.
2. 악의적인 관리자는 프론트 러닝을 수행하여 `AccountModule::grantAccountPermission` 함수를 실행하여 다른 제어되는 사용자에게 관리자 권한을 할당합니다.
3. 1단계의 트랜잭션이 완료되지만 악의적인 관리자는 새로 권한을 부여받은 사용자를 통해 액세스 권한을 유지합니다.

## 권장 사항

관리자 역할을 할당하는 기능을 시스템 소유자에게만 제한하는 것이 좋습니다.

# [M-02] 서명된 명령을 취소할 수 없음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

현재 서명된 메시지의 `nonce`는 `ExecutionModule::executeBySig` 함수의 실행을 통해서만 증가시킬 수 있습니다.

```solidity
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
        Account.Data storage account = Account.exists(accountId);
        address accountOwner = AccountRBAC.load(account.id).owner;

>>      uint256 incrementedNonce = Signature.incrementSigNonce(accountOwner);
        address recoveredAddress = Signature.generateRecoveredAddress(
            Signature.calculateDigest(
                hashExecuteBySig(accountId, commands, incrementedNonce, sig.deadline, extraSignatureData)
            ),
            sig
        );

        return _execute(accountId, commands, recoveredAddress);
    }
```

이 설계는 소유자나 관리자가 명령을 취소할 수 있는 메커니즘을 제공하지 않으며, 이는 인출이나 계정 이전과 같은 주문의 긴급 취소 또는 가스 부족 오류 등과 같은 트랜잭션 되돌림 상황에서 문제가 될 수 있습니다.

## 권장 사항

이 문제를 해결하려면 소유자나 지정된 관리자가 수동으로 `nonce`를 증가시킬 수 있는 함수를 구현하는 것이 좋습니다.

# [M-03] 소유자 변경 시 `Nonce` 재설정 취약점

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`accountOwner`와 연결된 논스는 `AccountModule::notifyAccountTransfer` 함수를 사용하여 소유자가 변경될 때 0으로 재설정됩니다. 이 재설정은 이전에 처리된 서명된 메시지의 잠재적인 재생(replay)을 허용하여 심각한 보안 위험을 초래합니다.

```solidity
// ExecutionModule.sol
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
        Account.Data storage account = Account.exists(accountId);
        address accountOwner = AccountRBAC.load(account.id).owner;

>>      uint256 incrementedNonce = Signature.incrementSigNonce(accountOwner);
        address recoveredAddress = Signature.generateRecoveredAddress(
            Signature.calculateDigest(
                hashExecuteBySig(accountId, commands, incrementedNonce, sig.deadline, extraSignatureData)
            ),
            sig
        );

        return _execute(accountId, commands, recoveredAddress);
    }
```

## 권장 사항

계정 소유자가 변경될 때 `nonce`를 재설정하지 않는 것이 좋습니다.

# [L-01] `validatePermission()`을 우회할 수 있음

`executeBySig()`는 `executeCommand()` 내에서 서명자의 권한을 확인하며, 이는 `validatePermission()`을 트리거합니다.

그러나 서명자는 0개의 명령으로 `executeBySig()`를 호출하여 권한 확인을 우회할 수 있습니다. for 루프를 실행하지 않고 `executeCommand()`를 호출하지 않기 때문입니다.

현재 이를 통해 서명자는 `accountOwner`의 서명 논스를 증가시킬 수 있습니다(별도 문제로 보고됨).

```Solidity
    function _execute(
        uint128 accountId,
        Command[] calldata commands,
        address signer
    )
        internal
        returns (bytes[] memory outputs, MarginInfo memory usdNodeMarginInfo)
    {
        Account.Data storage account = Account.exists(accountId);
        account.ensureAccess();

        // execution
        outputs = new bytes[](commands.length);

       //@audit zero commands will allow bypass of permission validation in executeCommand()
>>>     for (uint256 i = 0; i < commands.length; i++) {
            outputs[i] = executeCommand(account, commands[i], signer);
        }

        // post-execution checks
        if (AccountCollateral.hasPool(account.id)) {
            usdNodeMarginInfo = account.getUsdNodeMarginInfo();
            AccountChecks.checkAboveIm(account.id, usdNodeMarginInfo);
        }
    }
```

이 문제는 `commands.length`가 0보다 커야 함을 요구하여 해결할 수 있습니다.

# [L-02] 서명 가변성(Malleability) 공격

`Signature.generateRecoveredAddress()`는 `ecrecover()`를 사용하여 서명에서 서명자 주소를 가져와 확인합니다.

그러나 `ecrecover()`에서 허용하는 고유하지 않은(가변적인) 서명을 거부하지 않으므로 서명 가변성 공격에 취약합니다.

즉, 공격자는 보류 중인 트랜잭션에서 서명을 얻고 v, r, s 값을 변경하여 해당 특정 해시에 대한 또 다른 유효한 서명을 생성할 수 있습니다. 그런 다음 공격으로 논스가 증가하므로 합법적인 트랜잭션을 DoS하기 위해 새 서명으로 프론트 러닝 공격을 수행할 수 있습니다.

다음 시나리오를 가정합니다.

1. 피해자가 서명이 있는 tx1을 제출합니다.
2. 공격자는 tx1에서 서명을 얻고 v, r, s 값을 변경하여 새 유효한 서명을 생성합니다.
3. 공격자는 새 서명을 사용하여 tx2로 tx1을 프론트 러닝합니다.
4. tx2가 실행되면 `Signature.incrementSigNonce()`를 통해 논스가 증가합니다.
5. 이제 피해자의 tx1이 실행되면 논스가 증가했기 때문에 실패합니다.

프론트 러닝 공격은 멤풀이 있는 체인에서만 가능하며 트랜잭션이 FIFO 기반으로 실행되는 Reya Network 체인에서는 불가능합니다.

```Solidity
    function generateRecoveredAddress(
        bytes32 digest,
        EIP712Signature memory sig
    ) internal view returns (address recoveredAddress) {
        if (sig.deadline < block.timestamp) revert SignatureExpired();
        //@audit this is susceptible to signature malleability attack
>>>     recoveredAddress = ecrecover(digest, sig.v, sig.r, sig.s);
        if (recoveredAddress == address(0)) revert SignatureInvalid();
    }
```

이 문제는 r 및 s 값을 확인하고 고유하지 않은(가변적인) 서명을 거부하는 OZ ECDSA 라이브러리를 사용하여 해결할 수 있습니다.

# [L-03] 논스를 무효화할 수 없음

사용자가 새 손절매 주문에 서명할 때 함께 사용될 논스를 인코딩합니다. 논스는 정렬되지 않은 방식으로 사용될 수 있으며 주문이 서명되면 누구나 서명자를 대신하여 실행할 수 있으며, 서명자가 주문을 무효화할 수 있는 기능은 없습니다. 서명자가 할 수 있는 유일한 일은 동일한 논스로 새 주문을 생성하고 실행하는 것뿐입니다. 그러나 이는 불편하거나 심지어 불가능할 수도 있습니다.

우리는 프로토콜이 계정에 대한 논스를 무효화할 수 있는 기능을 갖는 것이 유용할 것이라고 믿습니다.
