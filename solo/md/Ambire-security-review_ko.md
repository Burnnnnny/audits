# 소개

**Ambire** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# **Ambire** 소개

Ambire는 스마트 지갑 프로토콜입니다. 사용자는 자신 또는 "권한(privileges)"을 가진 다른 주소가 제어하는 지갑(계정)을 갖습니다. 사용자는 트랜잭션 번들을 오프체인에서 서명할 수 있으며 누구나 온체인에서 이를 실행할 수 있습니다. EIP712, Schnorr, Multisig 등 다양한 서명 체계가 허용됩니다. 이 프로토콜은 반사실적(counterfactual) 방식으로 작동하며, 이는 사용자 지갑이 첫 번째 트랜잭션에서만 배포됨을 의미합니다. 실제 배포는 지갑 스마트 계약에 대한 EIP1167 최소 프록시입니다.

## 관찰 사항

팩토리 계약은 지갑의 특권 계정을 초기화하기 위해 SSTORE 명령을 앞에 붙이는 사용자 지정 EIP1167 프록시를 배포(`CREATE2`를 통해)하는 데 사용됩니다. 이 바이트코드 생성은 [여기](https://github.com/AmbireTech/adex-protocol-eth/blob/master/js/IdentityProxyDeploy.js)에서 수행되며 애플리케이션의 백엔드 코드를 관리하는 Ambire 팀이 제어합니다. 사용자는 이 프록시를 통해 스마트 지갑과 상호 작용합니다. `AmbireAccount` 코드에는 `delegatecall` opcode가 있으며, 이는 `CREATE2` opcode와 결합하여 "변성 계약(metamorphic contract)" 사용 사례를 엽니다.

특별한 `allowedToDrain` 주소는 `AmbireAccountFactory`에서 임의의 호출을 실행할 수 있지만 계약에 상태가 없고 잔액을 보유해서는 안 되므로 안전합니다.

대부분의 메서드에 "벽돌 방지(non-bricking)" 기술이 구현되어 있어 사용자가 권한을 다운그레이드 하여 지갑이 "벽돌(brick)" 상태가 되는 것을 허용하지 않습니다.

프로토콜 아키텍처에는 지갑을 배포하거나 트랜잭션 번들을 실행할 수 있는 릴레이어(relayer)가 있습니다. 릴레이어는 가스 그리핑(gas griefing) 공격의 공격 벡터이며 온체인 가스 제한 다중 호출 계약을 사용할 수 있을 뿐만 아니라 오프체인에서도 트랜잭션 시뮬레이션을 수행해야 합니다.

프로토콜에 외부 종속성이 없습니다(OpenZeppelin/Solmate 가져오기 없음).

# 위협 모델

`AmbireAccount`에 관하여:

- 비특권(Unprivileged) 사용자는 `fallback` 함수만 호출할 수 있습니다.
- 특권(Privileged) 사용자는 `execute`, `executeMultiple` 및 `executeBySender`만 직접 호출할 수 있습니다.
- 계약의 외부 호출은 `setAddrPrivilege`, `tryCatch`, `tryCatchLimit` 및 `executeBySelf`를 호출할 수 있습니다.

## 특권 역할 및 행위자

- 릴레이어 (Relayer) - 사용자를 위해 번들을 실행하며, 일반적으로 자신에게 수수료 트랜잭션을 포함합니다. 복구 키를 보유할 수도 있습니다.
- AllowedToDrain - `AmbireAccountFactory` 배포 중에 주소에 부여되는 역할입니다. `AmbireAccountFactory`에서 임의의 호출을 실행할 수 있습니다.
- 특권 사용자 (Privileged user) - 지갑에서 임의의 호출을 실행할 수 있으며 완전한 제어 권한을 갖습니다.
- 폴백 핸들러 (Fallback handler) - `AmbireAccount` 기능을 확장할 수 있으며 `delegatecall`을 받습니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇입니까?

**A:** 사용자의 스마트 지갑에 보관된 자금입니다.

**Q:** 어떤 경우에 프로토콜/사용자가 돈을 잃을 수 있습니까?

**A:** 자금을 사용할 수 없거나 지갑에서 도난당하는 경우입니다.

**Q:** 공격자가 목표를 달성하는 방법에는 어떤 것이 있습니까?

**A:** 특별한 서명을 제공하여 임의의 호출을 실행할 권한이 있다고 지갑을 속이거나 재생 공격을 시작하는 것입니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

**영향 (Impact)** - 성공적인 공격의 기술적, 경제적, 평판적 손상

**가능성 (Likelihood)** - 특정 취약점이 발견되고 악용될 확률

**심각도 (Severity)** - 위험의 전반적인 임계도

# 보안 평가 요약

**_검토 커밋 해시_ - [3f17320132e3bc67fa9a505fafded7bb16fc3e96](https://github.com/AmbireTech/ambire-common/tree/3f17320132e3bc67fa9a505fafded7bb16fc3e96)**

**_수정 검토 커밋 해시_ - [5c54f8005e90ad481df8e34e85718f3d2bfa2ace](https://github.com/AmbireTech/ambire-common/tree/5c54f8005e90ad481df8e34e85718f3d2bfa2ace)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `libs/Bytes`
- `libs/SignatureValidator`
- `AmbireAccount`
- `AmbireAccountFactory`

다음과 같은 수의 문제가 발견되었으며 심각도별로 분류되었습니다:

- 치명적 & 높음: 1개 문제
- 중간: 1개 문제
- 낮음: 4개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | 누구나 다른 사용자의 예약된 복구를 취소할 수 있음 | 치명적 |
| [M-01] | `address(0)`에 0이 아닌 권한이 있는 경우 잘못된 서명 실행 가능 | 중간 |
| [L-01] | `ecrecover` 프리컴파일은 서명 가변성(malleability)에 취약함 | 낮음 |
| [L-02] | Schnorr 서명이 불충분하게 검증됨 | 낮음 |
| [L-03] | 모든 호출 경로가 벽돌 방지(anti-bricking) 보호되는 것은 아님 | 낮음 |
| [L-04] | 프로토콜에 서명 만료가 구현되지 않음 | 낮음 |

# 상세 발견 사항

# [C-01] 누구나 다른 사용자의 예약된 복구를 취소할 수 있음

## 심각도

**영향:**
높음, 중요한 프로토콜 기능이 피해 사용자에게 영구적으로 차단될 수 있기 때문입니다.

**가능성:**
높음, 전제 조건이 없으며 누구나 악용할 수 있기 때문입니다.

## 설명

Ambire의 지갑 복구 메커니즘을 사용하면 미리 설정된 계정(일반적으로 릴레이어)이 초기 서명자가 도난/분실된 후 새로운 기본 서명자를 설정할 수 있습니다. 이는 `SIGMODE_RECOVER` 서명으로 `execute`를 호출하여 작동합니다. 이렇게 하면 다음과 같이 요청이 계약에 저장되어 나중을 위해 예약됩니다:

```solidity
scheduledRecoveries[hash] = block.timestamp + recoveryInfo.timelock;
emit LogRecoveryScheduled(hash, recoveryInfoHash, recoveryKey, currentNonce, block.timestamp, txns);
```

또한 계약에서는 `SIGMODE_CANCEL` 서명을 사용하여 예약된 복구를 취소하는 옵션을 허용합니다. 문제는 예약된 복구로 저장되는 `hash`에 `isCancellation` 플래그(서명 모드가 `SIGMODE_CANCEL`인지 확인하는 플래그)가 포함되어 있지 않다는 것입니다. 플래그가 이 해시의 일부가 아니기 때문에 누구나 `SIGMODE_RECOVER`가 사용되었을 때와 동일한 `signature` 매개변수로 `execute`를 호출할 수 있지만 마지막 바이트만 변경하여 `SIGMODE_CANCEL`을 사용하도록 할 수 있습니다. 즉, 누구나 전제 조건 없이 다른 사용자의 예약된 복구를 취소할 수 있으며, 이는 지갑 복구에 대한 그리핑 공격 벡터로 이어집니다. 공격자는 예약된 복구가 통과되기 몇 분 전에 취소하는 스크립트를 작성하는 데까지 나아갈 수 있습니다.

## 권장 사항

`hash`에 `isCancellation` 플래그를 추가하여 초기 `SIGMODE_RECOVER` 서명이 `SIGMODE_CANCEL` 서명으로 변경되어 재사용되지 않도록 하십시오.

## 논의

**pashov:** 수정됨.

# [M-01] `address(0)`에 0이 아닌 권한이 있는 경우 잘못된 서명 실행 가능

## 심각도

**영향:**
높음, 누구나 특정 지갑에서 모든 자금을 훔칠 수 있기 때문입니다.

**가능성:**
낮음, `address(0)`에 0이 아닌 권한이 있어야 하기 때문입니다.

## 설명

이 문제는 이전 감사의 [이](https://github.com/code-423n4/2021-10-ambire-findings/issues/13) 문제와 유사합니다. `SignatureValidator::recoverAddrImpl`의 코드는 유효한 `Schnorr` 서명을 받았지만 주어진 `hash`(트랜잭션 해시)에 대한 것이 아닌 경우 `address(0)`을 반환합니다. 이제 결과는 다음과 같이 확인됩니다:

```solidity
signerKey = SignatureValidator.recoverAddrImpl(hash, signature, true);
require(privileges[signerKey] != bytes32(0), 'INSUFFICIENT_PRIVILEGE');
```

즉, `signerKey`는 `address(0)`이 되고 `privileges[address(0)]`의 값이 0이 아니면 누구나 이 지갑에 대해 트랜잭션을 실행할 수 있습니다(예: 모든 자금 훔치기).

또한 `SignatureMode == Multisig`인 서명을 제공한 다음 그 안의 서명을 `SignatureMode == Spoof` 유형으로 제공하면 `recoverAddrImpl`이 `address(0)`을 반환하도록 할 수 있습니다. `allowSpoofing` 인수가 `false`이므로 메서드 끝에서 `return address(0);` 코드로 이동하게 됩니다.

`recoverAddrImpl`이 `address(0)`을 반환하도록 하는 세 번째 방법은 `SignatureMode == Multisig`를 사용하고 빈 서명 배열을 제공하는 것입니다. 그러면 `address signer`의 기본값인 `address(0)`이 반환됩니다.

## 권장 사항

`recoverAddrImpl`이 `address(0)`을 반환하는 경로가 없도록 하고, 대신 트랜잭션을 되돌리(revert)십시오. 또한 `// should be impossible to get here` 주석은 거짓이므로 제거하십시오.

## 논의

**pashov:** 수정됨.

# [L-01] `ecrecover` 프리컴파일은 서명 가변성(malleability)에 취약함

`s`와 `v`를 뒤집으면 동일한 해시 및 서명자에 도달하는 다른 서명을 만들 수 있습니다. 이는 OpenZeppelin의 ECDSA 라이브러리에서 [이렇게](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/dfef6a68ee18dbd2e1f5a099061a3b8a0e404485/contracts/utils/cryptography/ECDSA.sol#L125-L136) 수정되었습니다. 시스템 서명에 논스(nonce)가 있기 때문에 이것이 문제가 되지는 않지만, 문제가 해결되는 것이 강력히 권장됩니다.

## 논의

**pashov:** 인지됨.

# [L-02] Schnorr 서명이 불충분하게 검증됨

[Chainlink의 Schnorr 서명 검증 구현](https://github.com/smartcontractkit/chainlink/blob/bb214c5d7ec172de400a72a1d8851ff639c979d2/evm/v0.5/contracts/dev/SchnorrSECP256K1.sol#L107-L109)에 따르면 Schnorr 서명이 `Q` 상수보다 강력하게 작은지 확인하고 공개 키의 X 좌표가 `Q/2`보다 작은지 확인하는 것이 좋습니다. 이는 서명에 논스가 있기 때문에 현재 시스템에서 문제가 되지 않는 서명 가변성 공격으로부터 보호합니다.

## 논의

**pashov:** 인지됨.

# [L-03] 모든 호출 경로가 벽돌 방지(anti-bricking) 보호되는 것은 아님

`execute` 및 `executeBySender` 호출 경로 모두 다음과 같은 벽돌 방지 보호 기능을 갖추고 있습니다:

```solidity
require(privileges[signerKey] != bytes32(0), 'PRIVILEGE_NOT_DOWNGRADED');
```

및

```solidity
require(privileges[msg.sender] != bytes32(0), 'PRIVILEGE_NOT_DOWNGRADED');
```

문제는 `fallback` 호출 경로에 이 보호 기능이 누락되어 있어 100%의 사례가 커버되지 않는다는 것입니다. 가능한 부분적인 해결책은 `delegatecall`에서 호출자의 권한이 다운그레이드 되었는지 확인하고 다운그레이드 된 경우 되돌리는 것입니다. 하지만 여기서는 모든 경우를 적절하게 처리할 수 없으므로 예상되는 폴백 핸들러 동작을 설명할 때 이를 잘 문서화하십시오.

## 논의

**pashov:** 인지됨.

# [L-04] 프로토콜에 서명 만료가 구현되지 않음

사용자가 트랜잭션 번들을 서명하고 릴레이어에게 팁을 포함하여 실행해 달라고 기대한다고 가정해 보겠습니다. 가스비가 매우 빠르게 급등하면 릴레이어가 번들을 실행하는 것이 경제적으로 타당하지 않게 될 수 있으므로 트랜잭션이 한동안 대기 상태로 유지될 수 있습니다. 가스비가 다시 떨어지면 시장 상황이 변경되어 예를 들어 스왑이 원래 받아야 할 가치보다 적은 가치를 받게 될 수 있습니다. 이를 방지하기 위해 서명 만료/마감일 속성을 추가하여 그 이후에는 번들 실행이 유효하지 않도록 하는 것이 유용합니다.

## 논의

**pashov:** 인지됨.
