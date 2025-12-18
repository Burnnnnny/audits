# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Pashov Audit Group**은 **klaster-smart-contracts** 저장소에 대한 시간 제한 보안 검토를 수행했으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Klaster 정보

Klaster 프로토콜 계약은 체인 간 트랜잭션(iTx)을 가능하게 하는 체인 추상화 프로토콜을 지원합니다. 주요 계약인 KlasterPaymaster와 KlasterEcdsaOwnershipModule은 사용자 작업 유효성 검사, 가스 비용 관리 및 다양한 블록체인 네트워크에서의 트랜잭션 번들 실행을 처리합니다.

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

_검토 커밋 해시_ - [a819679b17716bb0b01e787fbb7fc54d7c70127e](https://github.com/0xPolycode/klaster-smart-contracts/tree/a819679b17716bb0b01e787fbb7fc54d7c70127e)

_수정 검토 커밋 해시_ - [ff6a0931d88f30e747345c043e1b1dce53bb0c61](https://github.com/0xPolycode/klaster-smart-contracts/tree/ff6a0931d88f30e747345c043e1b1dce53bb0c61)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `KlasterEcdsaOwnershipModule`
- `KlasterPaymaster`
- `DeterministicDeployFactory`
- `BaseAuthorizationModule`
- `Dependencies`

# 발견 사항

# [M-01] 유효 기간이 짧을 경우 금전적 손실 가능성

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

시뮬레이션은 성공할 수 있지만 실제 온체인 실행에서는 실패할 수 있습니다. 이는 번들러(노드)에게 금전적 손실을 초래합니다. 따라서 시뮬레이션과 실제 온체인 제출 결과 간의 일관성을 유지하는 것이 매우 중요합니다.

예를 들어, 블록의 타임스탬프 값이 2000 미만인 경우에만 통과하는 검증 단계의 UserOp를 고려해 보십시오. 시뮬레이션 중에는 이와 같은 조건이 충족될 수 있지만, 블록에 포함될 때 트랜잭션의 타임스탬프가 2000보다 높으면 트랜잭션이 온체인에서 실패할 수 있습니다. 결과적으로 노드의 자금 손실이 발생합니다.

## 권장 사항

시뮬레이션과 실제 실행 사이의 시간을 확인하는 것을 고려하십시오. 최소 유효 기간이 있는 UserOp를 수락하십시오.

## Klaster 의견

이것은 백엔드 측의 노드에서 처리됩니다. 번들의 userOps 중 하나가 마감 기한이 매우 촉박한 경우(마감 기한이 너무 짧거나 실행이 먼 미래에 시작되는 경우(가스 스파이크 위험)) 노드는 iTx 번들 실행을 수락하지 않습니다.

다시 말씀드리지만, 우리는 계약을 가능한 한 가볍게 유지하고 금전적 손실 위험을 포함하여 iTx 번들 실행으로 수락할 위험 수준을 노드가 스스로 결정하게 하고 싶습니다.

# [M-02] 동일한 주소 케이스에 대한 여러 작업

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

ops 배열에 동일한 주소에 대한 여러 UserOperation이 있는 경우 ops 배열에 대한 확인이 없으므로 모든 UserOperation[]에 대해 [handleOps](https://github.com/0xPolycode/klaster-smart-contracts/blob/a819679b17716bb0b01e787fbb7fc54d7c70127e/contracts/KlasterPaymaster.sol#L13) 메서드에서 수행된 트랜잭션이 실패할 가능성이 있습니다.

```
function handleOps(UserOperation[] calldata ops) public payable {
        entryPoint.depositTo{value: msg.value}(address(this));
        entryPoint.handleOps(ops, payable(msg.sender));
        entryPoint.withdrawTo(payable(msg.sender), entryPoint.getDepositInfo(address(this)).deposit);
    }
```

https://www.alchemy.com/blog/account-abstraction 에 따르면 다음이 매우 중요합니다.

> 번들에 동일한 지갑에 대한 여러 작업이 포함되지 않는 한, 위에서 논의한 스토리지 제한으로 인해 실제로 이를 무료로 얻을 수 있습니다. 두 작업의 검증이 동일한 스토리지에 닿지 않으면 서로 간섭할 수 없습니다. 이를 활용하기 위해 실행자는 번들에 주어진 지갑의 작업이 최대 하나만 포함되도록 합니다.

동일한 지갑 주소에 대한 두 개의 OP는 문제를 일으킬 수 있습니다. 간단한 예가 다음 시나리오에 설명되어 있습니다.

- 사용자가 번들러에게 OP를 보냅니다.
- ops 배열에는 Bob의 동일한 지갑 주소에 대한 2개의 OP가 있습니다.
- 번들러가 시작되고 simulateValidation 테스트에 따라 해당 OP를 정렬하여 선택합니다.
- Bob의 계정에 1000USDC가 있다고 가정합니다.
- Bob의 첫 번째 OP에 대한 simulateValidation에서 1000 USDC를 다른 지갑으로 전송할 수 있는지 확인하고 `simulateValidation`을 통해 성공적으로 전달됩니다.
- 500 USDC를 이체하는 Bob의 두 번째 OP(동일한 지갑 주소)는 simulateValidation을 거치고 계정에 1000 USDC가 있으므로 역시 성공했습니다.
- 번들러는 handleOps를 호출하여 ops 배열을 실행할 준비가 되었습니다.
- Bob의 첫 번째 OP가 성공적으로 실행됩니다.
- Bob의 두 번째 OP 실행은 USDC가 충분하지 않아 실패했습니다.

## 권장 사항

동일한 지갑 주소에 대한 여러 작업이 없는지 확인하기 위해 배열에 확인을 추가하십시오.

## Klaster 의견

확인은 매우 필요하지만 시스템의 노드/백엔드 측에서 처리됩니다. 4337 인프라에서 번들러가 제어하는 ​​방식과 동일합니다(스마트 계약이 아님).

우리는 스마트 계약을 가능한 한 가볍고 일반적인 상태로 유지하고 오프체인에서 할 수 있는 모든 것을 하고 싶습니다.

# [M-03] `isSmartContract` 확인 부족

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`KlasterEcdsaModule` 계약의 `initForSmartAccount` 함수는 스마트 계정에 대한 모듈을 초기화하고 EOA 소유자를 설정하는 역할을 합니다. 그러나 제공된 `eoaOwner` 주소가 스마트 계약이 아닌지 확인하는 검사가 포함되어 있지 않습니다.

```solidity
function initForSmartAccount(address eoaOwner) external returns (address) {
    if (_smartAccountOwners[msg.sender] != address(0)) {
        revert AlreadyInitedForSmartAccount(msg.sender);
    }
    if (eoaOwner == address(0)) revert ZeroAddressNotAllowedAsOwner();
    _smartAccountOwners[msg.sender] = eoaOwner;
    return address(this);
}
```

계약에는 주소가 스마트 계약인지 확인하는 `_isSmartContract` 함수가 포함되어 있지만 `initForSmartAccount` 함수에서는 활용되지 않습니다. 즉, 초기화 중에 스마트 계약을 스마트 계정의 소유자로 설정할 수 있습니다.

제공된 정보에 따르면 유효한 EOA 서명 요구 사항으로 인해 보안이 손상되지는 않는다고 제안하지만, 일관성을 위해 `isSmartContract` 확인을 추가하고 EOA 소유자만 허용하는 의도된 동작을 시행하는 것을 고려할 가치가 있습니다.

## 권장 사항

의도된 동작의 명확성과 시행 가능성을 개선하기 위해 `initForSmartAccount` 함수에 `isSmartContract` 확인을 추가하는 것이 좋습니다. 이렇게 하면 스마트 계정 초기화 중에 EOA 주소만 소유자로 설정할 수 있습니다.

다음은 `isSmartContract` 확인이 포함된 업데이트된 코드입니다.

```diff
    function initForSmartAccount(address eoaOwner) external returns (address) {
        if (_smartAccountOwners[msg.sender] != address(0)) {
            revert AlreadyInitedForSmartAccount(msg.sender);
        }
        if (eoaOwner == address(0)) revert ZeroAddressNotAllowedAsOwner();
+       if (_isSmartContract(eoaOwner)) revert NotEOA(owner);
        _smartAccountOwners[msg.sender] = eoaOwner;
        return address(this);
    }
```

`if (_isSmartContract(eoaOwner)) revert NotEOA(eoaOwner);` 라인을 추가하면 제공된 `eoaOwner` 주소가 스마트 계약인 경우 함수가 되돌아가므로 EOA 주소만 소유자로 설정할 수 있습니다.

# [L-01] 이행되지 않은 트랜잭션 상환

노드 계정 이외의 계정이 `KlasterPaymaster.sol`을 사용하려고 하면 예치된 ETH가 없기 때문에 되돌려집니다. 페이마스터는 Klaster 노드가 userOps를 진입점으로 보낼 때만 선불됩니다.

```solidity
    function handleOps(UserOperation[] calldata ops) public payable {
>>      entryPoint.depositTo{value: msg.value}(address(this));
        entryPoint.handleOps(ops, payable(msg.sender));
>>      entryPoint.withdrawTo(payable(msg.sender), entryPoint.getDepositInfo(address(this)).deposit);
    }
```

이 문제를 해결하려면 페이마스터의 자금을 확인해야 합니다.

```diff
    function _validatePaymasterUserOp(UserOperation calldata userOp, bytes32 userOpHash, uint256 maxCost)
        internal
        virtual
        override
        returns (bytes memory context, uint256 validationData)
    {
+       if (address(this).balance < maxCost)  revert InsufficientBalance();
        (uint256 maxGasLimit, uint256 nodeOperatorPremium) =
            abi.decode(userOp.paymasterAndData[20:], (uint256, uint256));
        return (abi.encode(userOp.sender, userOp.maxFeePerGas, maxGasLimit, nodeOperatorPremium), 0);
    }
```

# [L-02] PreVerificationGas 처리

사용자는 UserOp를 제출할 때 세 가지 가스 제한(PreVerificationGas, VerificationGasLimit, CallGasLimit)을 설정합니다. PreVerificationGas는 여러 UserOp를 번들로 묶어 Entrypoint로 보내는 과정에서 소비되는 가스 수수료를 말합니다. PreVerificationGas는 미리 계산되지 않으므로 설정 시 신중한 고려가 필요합니다. 너무 높게 설정하면 사용자가 Bundler에게 과도한 가스 수수료를 지불하게 되고, 너무 낮게 설정하면 Bundler가 가스 수수료를 충당하지 못해 거래가 되돌려지고 수수료가 낭비될 수 있습니다.

이 분석 [링크](https://www.stackup.sh/blog/an-analysis-of-preverificationgas)를 확인하세요.

> 간단한 영어로, 각 userOp가 지불하는 PreVerificationGas는 다음의 합과 같습니다.

> 1. (batch_fixed / batch_size): 배치 오버헤드의 고정 부분의 몫.
> 2. batch_variable: 배치에 해당 크기의 userOp를 추가하는 데 필요한 배치 오버헤드의 델타.
> 3. per_userop_overhead: 검증 및 실행 루프를 통해 해당 크기의 userOp를 실행하는 userOp당 오버헤드.

## Klaster 의견

알겠습니다! 그리고 당신 말이 맞습니다. 그렇기 때문에 초기 버전에서는 사용자가 외부에서 제공하는 대신 노드가 이 세 가지 가스 매개변수를 추정하게 할 것입니다. 그렇게 하면 노드는 UserOp가 실제로 오류 없이 실행 가능하도록 구축되었는지 확인할 수 있습니다.

사용자는 여전히 iTx 해시에 서명하여 추정치에 동의해야 하므로, 사용자가 프로토콜과 상호 작용하는 프런트엔드 부분에서 시뮬레이션을 실행하고 숫자가 괜찮은지 확인할 수 있습니다!

# [L-03] EntryPoint v6 사용

Klaster 프로토콜은 EntryPoint v6를 사용하고 있으며 이는 EntryPoint v7의 업데이트에 따르면 향후 프로토콜에 부정적인 영향을 미칠 수 있습니다.

- EntryPoint v6을 사용하면 EntryPoint가 중복 호출을 발생시켜 프로세스를 복잡하게 만들 수 있습니다.

  > v0.7.0 업데이트: 중복 postOp 호출이 제거되었습니다. 이제 사후 작업 로직이 더 간단해졌으며, 트랜잭션 후 필요한 작업을 처리하는 단일 호출로 실행 흐름을 단순화하고 잠재적인 오류를 줄였습니다.

- 현재 버전 v6은 트랜잭션 실행에 적절한 가스 추정이 중요하며, 가스가 부족하지 않고 작업이 완료되도록 하고 특정 유형의 공격을 방지하여 보안을 강화합니다.

> v0.7.0 업데이트: validatePaymasterUserOp 및 postOp를 포함한 가스 제한에 대한 새로운 사양은 개발자에게 더 명확한 지침을 제공합니다. 이는 더 나은 가스 추정에 도움이 되고 트랜잭션 주변의 보안 프레임워크를 강화합니다.

EntryPoint v0.7.0과의 통합을 고려하십시오.

## Klaster 의견

개발을 시작하기 전에도 v7로 전환하는 것을 고려했지만 Biconomy 스마트 계정 인프라에서 작업하므로 스마트 계정 팩토리 측에서 지원하는 것으로 제한되며 현재는 EntryPoint v6입니다.

Biconomy가 새로운 스마트 계정 스택을 출시하면 아마도 v7로 업그레이드할 것입니다.

# [L-04] 중앙 집중식 노드 일괄 처리 및 잠재적 MEV 악용

현재 시스템의 중앙 집중식 단계에서는 단일 노드가 여러 사용자의 UserOp를 일괄 처리하고 처리하는 책임이 있습니다. 이러한 중앙 집중식 접근 방식은 초기 데모 단계에서는 허용되지만 Bundler MEV 악용과 관련하여 이전 문제 제기된 우려와 유사하게 채굴자 추출 가능 가치(MEV) 악용의 잠재적 위험을 초래합니다.
UserOp를 일괄 처리하고 처리하는 유일한 주체 역할을 하는 중앙 집중식 노드는 잠재적으로 배치의 트랜잭션을 재정렬하거나 조작하여 자신의 이익을 우선시하거나 MEV 전략을 통해 추가 가치를 추출할 수 있습니다. 트랜잭션 순서 및 실행에 대한 이러한 중앙 집중식 제어는 사용자 트랜잭션의 불공정한 처리, 잠재적인 검열 및 시스템 신뢰의 침식으로 이어질 수 있습니다.

중앙 집중식 노드는 자신의 이익을 우선시하기 위해 배치 내에서 트랜잭션을 재정렬하여 잠재적으로 사용자의 트랜잭션이 불리한 요율로 실행되거나 실패하게 할 수 있습니다.

시스템이 발전함에 따라 UserOp를 일괄 처리하고 처리하는 분산형 노드 네트워크를 도입하십시오. 이러한 분산화는 트랜잭션 순서 및 실행에 대한 제어를 분산시켜 단일 중앙 집중식 노드와 관련된 위험을 완화합니다.

## Klaster 의견

공정한 평가입니다. Klaster Protocol은 자체 공개 노드를 호스팅하며, 구현은 누구나 노드 자체의 내부 작동을 살펴보고 확인할 수 있도록 공개적으로 제공됩니다.

프로토콜의 초기 버전은 공개 Klaster 노드 간에 p2p 네트워킹이 구현된다는 의미에서 분산형이 아니지만, 누구나 자신의 목적을 위해 자신의 개인 Klaster 노드를 실행하도록 선택할 수 있으며, 그런 의미에서 MEV 추출과 관련된 우려를 제거하는 자신의 개인 멤풀을 가질 수 있습니다.

출시 파트너 및 기타 통합자는 완전한 분산화 단계가 시작될 때까지 초기에 프로토콜을 더욱 탄력적으로 만들기 위해 자체 노드를 실행하도록 권장됩니다.

# [L-05] 지불 가능(payable) 정의 누락

`DeterministicDeployFactory` 계약의 `deploy` 함수는 `create2` opcode를 사용하여 새 계약을 배포하는 역할을 합니다. 그러나 현재 구현에는 `payable` 수정자가 포함되어 있지 않으므로 함수가 네이티브 토큰을 받을 수 없습니다. 배포된 계약을 구성하는 동안 전송해야 하는 네이티브 토큰이 필요한 경우 현재 구현은 실패합니다. `create2` opcode는 배포 비용과 생성자에서 네이티브 토큰이 필요한 추가 로직을 충당하기 위해 배포되는 계약에 충분한 양의 네이티브 토큰을 제공해야 합니다.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

contract DeterministicDeployFactory {
    event Deploy(address addr);

    function deploy(bytes memory bytecode, uint256 _salt) external {
        address addr;
        assembly {
            addr := create2(0, add(bytecode, 0x20), mload(bytecode), _salt)
            if iszero(extcodesize(addr)) { revert(0, 0) }
        }

        emit Deploy(addr);
    }
}
```

이 문제를 해결하려면 `deploy` 함수에 `payable` 수정자를 추가하여 호출자로부터 네이티브 토큰을 받을 수 있도록 합니다.

# [L-06] 크기 및 가스 소비에 대한 확인 없음

계약의 handleOps() 함수는 UserOperation 배열의 크기에 대한 확인을 수행하지 않으며 모든 작업을 처리하는 데 필요한 가스 소비를 추정하지 않습니다. 이러한 간과로 인해 작업에 대한 반복 루프가 과도한 양의 가스를 소비하여 트랜잭션이 되돌려지고 트랜잭션에 할당된 모든 가스가 낭비되는 상황이 발생할 수 있습니다.

ops의 길이로 모든 루프를 실행하기 전에 추정을 하거나 여러 배치로 분할하고 루프 전에 사용자가 모든 가스를 소비하는 것을 방지하는 방법을 찾으십시오.

## Klaster 의견

이것은 노드가 모든 UserOp paymasterAndData 필드에서 maxGasLimit를 읽고 배치를 실행할 때 주어진 최대 가스 제한에 UserOp를 바인딩하므로 노드에서 처리됩니다.

노드는 UserOp의 시뮬레이션이 주어진 maxGasLimit 내에 잘 맞는지 확인하거나 그렇지 않으면 전체 iTx 번들을 거부합니다.

# [L-07] 상하이 하드포크와 호환되지 않음

Solidity 0.8.20용 컴파일러는 기본 대상 EVM 버전을 상하이로 전환하며, 이는 생성된 바이트코드에 PUSH0 opcode가 포함됨을 의미합니다. PUSH0을 지원하지 않을 수 있는 L2 체인과 같이 메인넷 이외의 체인에 배포하려는 경우 적절한 EVM 버전을 선택해야 합니다. 그렇지 않으면 계약 배포가 실패합니다.

contracts/deployer/DeterministicDeployFactory.sol은 유동적 프라그마를 사용합니다.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

contract DeterministicDeployFactory {
    event Deploy(address addr);

    function deploy(bytes memory bytecode, uint256 _salt) external {
        address addr;
        assembly {
            addr := create2(0, add(bytecode, 0x20), mload(bytecode), _salt)
            if iszero(extcodesize(addr)) { revert(0, 0) }
        }

        emit Deploy(addr);
    }
}
```

Solidity 컴파일러 버전을 0.8.19로 변경하거나 프로토콜에서 지원하려는 모든 체인에서 호환되는 evm 버전을 정의하십시오(참조: https://book.getfoundry.sh/reference/config/solidity-compiler?highlight=evm_vers#evm_version).

# [L-08] 0이 아닌 `msg.value`에 대한 확인 누락

`KlasterPaymaster` 계약에는 Ether를 수락하는 세 가지 함수인 `handleOps`, `simulateHandleOp`, `simulateValidation`이 있습니다. 그러나 이러한 함수는 `entryPoint.depositTo{value: msg.value}(address(this))`를 사용하여 `entryPoint` 계약에 자금을 예치하기 전에 수신된 `msg.value`가 0보다 큰지 확인하지 않습니다.

`msg.value`가 0이면 예치 작업은 여전히 ​​실행되지만 계약의 잔액에는 아무런 영향을 미치지 않습니다.

자금을 예치하기 전에 `msg.value`가 0보다 큰지 확인하려면 영향을 받는 각 함수의 시작 부분에 확인을 추가하십시오. `msg.value`가 0이면 함수는 적절한 오류 메시지와 함께 되돌려야 합니다.

# [L-09] `upperBoundTimestamp`에 대한 유효성 검사 부족

`KlasterEcdsaModule` 계약의 `validateUserOp` 함수에는 `upperBoundTimestamp` 매개변수에 대한 적절한 유효성 검사가 부족합니다. 이 함수는 확인이나 유효성 검사를 수행하지 않고 디코딩된 서명에서 수신한 `upperBoundTimestamp` 값을 직접 사용합니다.

```solidity
    function validateUserOp(UserOperation calldata userOp, bytes32 userOpHash)
        external
        view
        virtual
        returns (uint256)
    {
        (bytes memory sigBytes,) = abi.decode(userOp.signature, (bytes, address));

        (
            bytes32 iTxHash,
            bytes32[] memory proof,
            uint48 lowerBoundTimestamp,
            uint48 upperBoundTimestamp,
            bytes memory userEcdsaSignature
        ) = abi.decode(sigBytes, (bytes32, bytes32[], uint48, uint48, bytes));

        bytes32 calculatedUserOpHash = getUserOpHash(userOp, lowerBoundTimestamp, upperBoundTimestamp);
        if (!_validateUserOpHash(calculatedUserOpHash, iTxHash, proof)) {
            return SIG_VALIDATION_FAILED;
        }

        if (!_verifySignature(iTxHash, userEcdsaSignature, userOp.sender)) {
            return SIG_VALIDATION_FAILED;
        }

        return _packValidationData(false, upperBoundTimestamp, lowerBoundTimestamp);
    }
```

upperBoundTimestamp가 0과 같으면 uint48 max로 설정하는 것이 좋습니다.

## Klaster 의견

이것은 ERC-4337 EntryPoint가 작동하는 방식입니다. 검증 모듈은 하한/상한 타임스탬프(서명 유효 기간)를 이 정보를 요청하는 사람에게 다시 보고합니다. 이 정보 요청자(EntryPoint)는 수신된 정보로 무엇을 할지 선택합니다.

우리의 경우 ERC4337 진입점은 타임스탬프를 다시 받고 타임스탬프가 경계를 벗어난 UserOp를 거부합니다.
[링크](https://github.com/eth-infinitism/account-abstraction/blob/fa61290d37d079e928d92d53a122efcc63822214/contracts/core/EntryPoint.sol#L471)

또한 상한이 0인 경우 상한을 UINT48_MAX로 설정합니다.
[링크](https://github.com/eth-infinitism/account-abstraction/blob/fa61290d37d079e928d92d53a122efcc63822214/contracts/core/Helpers.sol#L26)
