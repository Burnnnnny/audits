# 소개

**Parcel Payroll** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다.

# 프로토콜 개요

Parcel Payroll은 UX 중심의 급여 관리 인프라를 블록체인에 도입하는 프로토콜입니다. Gnosis Safe 다중 서명 지갑 추상화와 Gnosis Safe 다중 서명 내의 Spending Limit 모듈과 통합되어 지출 승인자의 허용량을 설정합니다.

범위 내 공개 상태 변경 메서드:

- `PayrollManager::executePayroll` - 서명된 메시지를 검증하고 Gnosis Safe 다중 서명 지갑에서 토큰을 가져오고 이체(지불)를 실행합니다.

[추가 문서](https://parcelhq.notion.site/Parcel-Payroll-SC-Documentation-539f9a1a75a541a79b8f5809b66b62e1)

# 위협 모델

### 시스템 행위자

- 급여 승인자 (Payroll approver) - 토큰 지출을 승인하기 위해 오프체인에서 메시지에 서명할 수 있습니다.
- 급여 실행자 (Payroll executor) - 급여 트랜잭션을 실행합니다.

Q: 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

A: 프로토콜의 Gnosis Safe 허용량입니다.

Q: 프로토콜에 일어날 수 있는 최악의 일은 무엇인가요?

1. 악의적인 사용자가 Gnosis Safe 다중 서명 지갑의 프로토콜 허용량을 고갈시킴
2. 서명 및 승인에 대한 재전송 공격
3. 토큰/기본 자산 기능과 관련된 `executePayroll`에 대한 DoS
4. `PayrollManager` 계약에 영원히 갇힌 토큰

### 흥미롭거나 예상치 못한 설계 선택:

프로토콜은 이체하는 만큼의 가치를 정확히 받도록 의도되었으며, 그렇지 않으면 토큰이 계약에 갇히게 됩니다.

프로토콜은 Gnosis Safe 다중 서명 서명자의 승인 서명을 오프체인에 저장하기 위해 머클 트리를 사용합니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

# 보안 평가 요약

**_검토 커밋 해시_ - [37d25a4f4fb2b72f47ccd4dad2146b07640feb20](https://github.com/ParcelHQ/parcel-payroll/tree/37d25a4f4fb2b72f47ccd4dad2146b07640feb20)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `payroll/PayrollManager`
- `payroll/Validators`
- `payroll/Modifiers`
- `payroll/Storage`
- `signature/Signature`
- `interfaces/index`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 2개 문제
- 중간: 3개 문제
- 낮음: 3개 문제
- 정보성: 8개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | 계약에 `payable` 함수가 누락되어 기본 자산으로 작업할 수 없음 | 치명적 |
| [C-02] | 계약이 제로 잔액 유지 불변성(zero balance left invariant)을 위해 `address(0)`을 기본 자산으로 처리하지 못함 | 치명적 |
| [M-01] | `address payable`의 `transfer` 함수 사용은 권장되지 않음 | 중간 |
| [M-02] | 비표준 ERC20 토큰의 사용은 자금 동결로 이어질 수 있음 | 중간 |
| [M-03] | 계약이 `Pausable`을 상속하지만 일시 중지/일시 중지 해제 기능을 노출하지 않음 | 중간 |
| [L-01] | EIP-712 도메인 구분자가 올바르게 구현되지 않음 | 낮음 |
| [L-02] | `PayrollManager` 계약의 코드 커버리지가 5% 미만임 | 낮음 |
| [L-03] | 배열 인수 길이 확인 누락 | 낮음 |
| [I-01] | 코드베이스 전체에 중복되거나 사용되지 않는 인터페이스, 열거형, 변수, 수정자 및 주석 존재 | 정보성 |
| [I-02] | 계약 및 파일 이름이 일치하지 않음 | 정보성 |
| [I-03] | 문자열이 있는 `require` 문보다 Solidity 사용자 지정 오류 선호 | 정보성 |
| [I-04] | 파일 헤더 주석의 불일치 | 정보성 |
| [I-05] | 잘못된 NatSpec | 정보성 |
| [I-06] | 코드에 열려 있는 `TODO` 존재 | 정보성 |
| [I-07] | 코드의 오타 | 정보성 |
| [I-08] | 일관성 없고 안전하지 않은 pragma 문 사용됨 | 정보성 |

# 상세 발견 사항

# [C-01] 계약에 `payable` 함수가 누락되어 기본 자산으로 작업할 수 없음

## 심각도

**영향:**
높음, 기본 자산을 사용하는 모든 트랜잭션이 되돌려지므로(revert)

**가능성:**
높음, 기본 자산이 `paymentToken`으로 자주 사용될 것으로 예상되므로

## 설명

`PayrollManager`에는 `payable`로 표시된 `receive` 함수나 `payable` 함수가 전혀 없습니다. 이것은 Gnosis Safe 다중 서명에서 그에게로의 모든 이체가 되돌려질 것이기 때문에 계약이 기본 자산으로 작업하는 것을 불가능하게 만들 것입니다.

## 권장 사항

기본 자산 이체를 허용하려면 `PayrollManager`에 `payable` `fallback` 또는 `receive` 함수를 추가하십시오.

# [C-02] 계약이 제로 잔액 유지 불변성(zero balance left invariant)을 위해 `address(0)`을 기본 자산으로 처리하지 못함

## 심각도

**영향:**
높음, 기본 자산을 사용하는 모든 트랜잭션이 되돌려지므로(revert)

**가능성:**
높음, 기본 자산이 `paymentToken`으로 자주 사용될 것으로 예상되므로

## 설명

`executePayroll` 메서드의 끝에는 다음과 같은 코드가 있습니다:

```solidity
// Check if the contract has any tokens left
for (uint256 i = 0; i < paymentTokens.length; i++) {
    IERC20 erc20 = IERC20(paymentTokens[i]);
    if (erc20.balanceOf(address(this)) > 0) {
        // Revert if the contract has any tokens left
        revert("CS018");
    }
}
```

몇 줄 위의 코드는 다음과 같습니다:

```solidity
if (tokenAddress[i] == address(0)) {
    // Transfer ether
    payable(to[i]).transfer(amount[i]);
    packPayoutNonce(true, payoutNonce[i]);
}
```

이는 `address(0)`이 기본 자산 이체를 처리하는 데 사용됨을 보여줍니다. 문제는 앞서 언급한 코드가 이 `address(0)` 토큰을 올바르게 처리하지 않으므로 `paymentTokens` 배열에 포함되어 있으면 전체 트랜잭션이 되돌려진다는 것입니다.

## 권장 사항

`executePayroll`이 끝날 때 계약의 기본 자산 잔액을 별도로 확인하고 `paymentTokens` 배열 값에 대해 `ERC20::balanceOf`를 호출할 때 `address(0)`을 무시하십시오.

# [M-01] `address payable`의 `transfer` 함수 사용은 권장되지 않음

## 심각도

**영향:**
중간, 급여가 되돌려지고 머클 트리 및 승인을 다시 수행해야 하므로

**가능성:**
중간, 수신자가 스마트 계약이거나 2300 가스 이상을 사용하는 수신(receive) 함수가 있는 다중 서명 지갑일 때마다 발생하기 때문

## 설명

`executePayroll` 함수는 `address payable`의 `transfer` 메서드를 사용하여 기본 자산 자금을 수신자 주소로 전송합니다. 이 주소는 호출자가 설정하지만 오프체인에서 생성된 머클 트리의 리프에도 인코딩됩니다. 이 수신자는 `transfer`의 한계인 2300 가스 이상을 차지하는 `receive` 또는 `fallback` 함수가 있는 스마트 계약일 수 있습니다. 예를 들면 일부 스마트 계약 지갑이나 다중 서명 지갑이 있으므로 `transfer` 사용은 권장되지 않습니다.

## 권장 사항

`transfer` 대신 값을 포함한 `call`을 사용하십시오. 함수에는 이미 `nonReentrant` 수정자가 있으므로 여기서는 재진입이 문제가 되지 않습니다.

# [M-02] 비표준 ERC20 토큰의 사용은 자금 동결로 이어질 수 있음

## 심각도

**영향:**
높음, 토큰이 `PayrollManager`에 영원히 갇히게 되므로

**가능성:**
낮음, 그러한 ERC20 토큰이 많지 않으므로

## 설명

`executePayroll` 메서드는 `ERC20`의 `transfer` 메서드를 사용하지만 반환된 `bool` 값이 `true`인지 확인하지 않습니다. 블록체인에는 실패 시 되돌리지(revert) 않고 대신 `false`를 반환하는 토큰이 있기 때문에 이는 문제가 됩니다(예: `ZRX`). 이러한 토큰을 사용하고 전송이 실패하면 토큰은 `PayrollManager` 스마트 계약에 영원히 갇히게 됩니다.

## 권장 사항

`OpenZeppelin`의 `SafeERC20` 라이브러리를 사용하고 `transfer` 호출을 대신 `safeTransfer` 호출로 변경하십시오.

# [M-03] 계약이 `Pausable`을 상속하지만 일시 중지/일시 중지 해제 기능을 노출하지 않음

## 심각도

**영향:**
낮음, 메서드에 `whenNotPaused` 수정자가 없으므로

**가능성:**
높음, 계약을 전혀 일시 중지할 수 없는 것이 확실하므로

## 설명

`Organizer` 스마트 계약은 OpenZeppelin의 `Pausable` 계약을 상속하지만 `_pause` 및 `_unpause` 메서드는 외부에서 호출할 수 있도록 노출되지 않으며 실제로 `whenNotPaused` 수정자를 사용하는 메서드도 없습니다. 이는 `Pausable`이 잘못 사용되었음을 보여주며 실제로는 계약을 전혀 일시 중지할 수 없는데도 잘못된 보안 감각을 줄 수 있습니다.

## 권장 사항

계약에서 `Pausable`을 제거하거나 더 안전하게 만들고 싶은 메서드에 `whenNotPaused` 수정자를 추가하고 액세스 제어를 통해 `_pause` 및 `_unpause` 메서드를 외부로 노출하십시오.

# [L-01] EIP-712 도메인 구분자가 올바르게 구현되지 않음

`Signature`의 도메인 구분자에는 EIP-712에 정의된 `name`, `version` 및 `salt` 필드가 누락되었습니다. 표준에서는 모든 필드가 의무 사항은 아니라고 명시하고 있지만, 이를 추가하면 프로토콜에 대한 오프체인 서명 메시지 사용에 또 다른 보안 계층이 추가됩니다. [EIP712 문서](https://eips.ethereum.org/EIPS/eip-712)를 참조하고 누락된 모든 도메인 구분자 필드를 추가하십시오.

# [L-02] `PayrollManager` 계약의 코드 커버리지가 5% 미만임

일부 보안 취약점은 전체 라인별 수동 감사보다 일반적인 단위 테스트 시나리오에서 더 쉽게 잡힙니다. 배포하려는 모든 코드에 대해 100%에 가까운 코드 커버리지를 위해 노력하여 계약에 손쉬운 버그가 남지 않도록 하는 것아 좋습니다.

# [L-03] 배열 인수 길이 확인 누락

`PayrollManager::executePayroll`에는 대부분의 배열 길이가 동일하다는 확인이 있지만 `paymentTokens.length == payoutAmounts.length` 확인이 누락되어 있으므로 추가해야 합니다.

# [I-01] 코드베이스 전체에 중복되거나 사용되지 않는 인터페이스, 열거형, 변수, 수정자 및 주석 존재

`index`의 `GnosisSafe` 인터페이스는 `executeAllowanceTransfer` 메서드에서 매개변수 유형으로만 사용되지만 `address` 유형을 대신 사용할 수 있으므로 실제로는 필요하지 않습니다. 이 방법으로 코드베이스 어디에서도 사용되지 않는 유일한 메서드 `execTransactionFromModule`이 있는 `GnosisSafe` 인터페이스를 제거할 수 있습니다.

`AllowanceModule`의 `Allowance` 구조체는 어디에서도 사용되지 않으므로 제거할 수 있습니다.

`AllowanceModule` 인터페이스의 `getTokenAllowance` 메서드는 어디에서도 사용되지 않으므로 제거할 수 있습니다.

`Storage`의 `Operation` 열거형은 어디에서도 사용되지 않으므로 제거할 수 있습니다.

`Storage`의 `MASTER_OPERATOR` 저장 변수는 쓰기만 하고 읽지 않으며 `internal`이므로 getter가 없습니다. 제거할 수 있습니다.

`Storage`의 `SENTINEL_UINT` 상수는 어디에서도 사용되지 않으므로 제거할 수 있습니다.

`Validators::isApprover`에서 `_addressToCheck != address(0)`를 확인할 필요가 없습니다. `Organizer::onboard`에 `approver` 주소가 `address(0)`가 아니도록 강제하는 `require` 문이 있기 때문입니다. 즉, `_isApprover`는 어차피 `false`를 반환하므로 확인은 중복이며 제거할 수 있습니다.

`Validators`의 `isOrgOnboarded`에 있는 `require(_addressToCheck != address(0), "CS003");` 확인은 실제로 필요하지 않습니다. `Organizer::onboard`는 `orgs` 매핑의 키로 `msg.sender`만 사용하므로 `address(0)`일 수 없기 때문입니다. 이 확인은 중복이고 가스를 낭비하므로 제거할 수 있습니다.

실제로 `Validators::isApprover`에서 `isOrgOnboarded` 확인을 수행할 필요가 없습니다. 생략하더라도 Org가 온보딩되지 않은 경우 `_isApprover` 메서드는 `false`를 반환하기 때문입니다. 여기서 내 권장 사항은 `Validators` 스마트 계약을 제거하고 다음을 사용하는 것입니다.

```solidity
require(_addressToCheck != SENTINEL_ADDRESS);
orgs[_safeAddress].approvers[_addressToCheck] != address(0);
```

주소가 Gnosis Safe 지갑의 승인자인지 확인합니다.

`Modifiers`의 `onlyMultisig` 수정자는 함수 인수가 `msg.sender` 값을 갖도록 강제할 뿐이므로 중복입니다. 수정자와 확인된 인수를 제거하고 대신 `msg.sender`를 직접 사용하십시오.

코드가 사용되지 않으므로 `Organizer`에서 주석 처리된 import를 제거하십시오. 오래된 코드는 이전 git 커밋에 보관되므로 다시 필요한 경우 거기에서 가져올 수 있지만 이렇게 코드를 주석 처리하여 유지할 필요는 없습니다.

`Signature`의 `EIP712Domain` 구조체 정의는 실제로 사용되지 않으며 `EIP712_DOMAIN_TYPEHASH` 해시 계산에서 문자열로 하드코딩되므로 필요하지 않습니다. 구조체 정의는 중복이므로 제거할 수 있습니다.

`Signature`의 `PayrollTx` 구조체 정의는 하나의 필드만 포함하므로 필요하지 않습니다. 구조체와 `validatePayrollTxHashes`에서의 사용을 제거하고 대신 `rootHash` 필드를 직접 사용하십시오. `// hash = encodeTransactionData(recipient, tokenAddress, amount, nonce)` 주석을 `validatePayrollTxHashes`의 `rootHash`에 대한 NatSpec `@param` 정의로 이동하십시오.

`validatePayrollTxHashes`의 `signer` 변수를 제거하고 그 값을 반환하십시오. 한 번만 사용되는 경우 변수를 추출할 필요가 없습니다.

`PayrollManager::encodeTransactionData`의 `encodedHash` 변수를 제거하고 한 번만 사용되므로 그 값을 직접 반환하십시오.

`erc20` 변수를 제거하고 해당 범위에서 한 번만 사용되므로 `PayrollManager::executePayroll`에서 직접 값을 사용하십시오.

`allowance` 및 `to` 변수를 제거하고 한 번만 사용되므로 `PayrollManager::execTransactionFromGnosis`에서 직접 값을 사용하십시오.

값이 항상 `bytes("")`이므로 `PayrollManager::execTransactionFromGnosis`에서 `signature` 매개변수를 제거하십시오. 대신 이 값을 `executeAllowanceTransfer` 호출에 직접 전달하십시오.

`PayrollManager::executePayroll`의 로컬 배열 변수 `validatedRoots`를 제거하십시오. 모든 값이 어차피 `true`이므로 중복입니다.

`packPayoutNonce`의 `flag` 매개변수는 항상 `true`이므로 제거할 수 있으며, `false`일 때 실행되는 코드도 실행되지 않으므로(죽은 코드) 제거할 수 있습니다.

# [I-02] 계약 및 파일 이름이 일치하지 않음

`Signature.sol` 파일에는 `SignatureEIP712` 스마트 계약이 포함되어 있습니다. 두 곳 모두에 동일한 이름(예: `Signature`만 사용)을 사용하십시오.

# [I-03] 문자열이 있는 `require` 문보다 Solidity 사용자 지정 오류 선호

Solidity 사용자 지정 오류(Custom Errors)를 사용하면 되돌려진 트랜잭션에서 소비되는 가스가 줄어들고, 프로토콜 클라이언트가 온체인에서 오류를 쉽게 잡을 수 있어 프로토콜의 상호 운용성이 향상될 뿐만 아니라, 더 큰 바이트코드나 트랜잭션 가스 소비 없이 오류에 설명적인 이름을 지정할 수 있어 UX도 향상됩니다. 모든 `require` 문을 제거하고 대신 사용자 지정 오류를 사용하십시오.

# [I-04] 파일 헤더 주석의 불일치

코드베이스의 대부분 파일에는 다음과 같은 주석이 파일 첫 줄에 있습니다.

```solidity
//contracts/Organizer.sol
```

문제는 일부 파일에는 있고 다른 파일에는 없어 일관성이 없다는 것입니다. 이러한 주석은 어쨌든 필요하지 않으므로 코드베이스에서 모두 제거하는 것이 가장 좋으며, 그렇지 않은 경우 각 스마트 계약 파일에 올바른 주석을 추가하십시오.

# [I-05] 잘못된 NatSpec

`Storage`에 있는 `ORG` 구조체의 NatSpec은 `claimbles` 및 `autoClaim` 매개변수나 구조체 필드가 있다고 말하지만 그러한 필드가 없으므로 NatSpec 문서에서 제거해야 합니다. 또한 `approvers`와 `approvalsRequired`의 순서를 바꿔야 합니다.

# [I-06] 코드에 열려 있는 `TODO` 존재

`Organizer::onboard`에는 코드가 프로덕션 준비가 되지 않았음을 보여주는 열려 있는 `TODO`가 있습니다. `TODO`의 주석을 구현하거나 제거하십시오.

# [I-07] 코드의 오타

코드의 다양한 오타를 수정해야 합니다:

`payrill` -> `payroll`

`claimbles` -> `claimables`

`AlowanceModule` -> `AllowanceModule`

# [I-08] 일관성 없고 안전하지 않은 pragma 문 사용됨

컴파일러 버전을 잠그고 각 컴파일에서 동일한 바이트코드를 결정론적으로 얻을 수 있도록 안정적인 pragma 문을 사용하는 것이 모범 사례입니다. 또한 코드베이스 전체에서 동일한 Solidity 버전을 사용하는 것이 권장되지만 현재는 그렇지 않습니다. 모든 pragma 문을 동일한 버전을 사용하고 pragma를 잠그도록 변경하십시오.
