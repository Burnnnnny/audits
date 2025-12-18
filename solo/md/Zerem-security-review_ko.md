# pashov에 의한 Zerem 프로토콜 보안 검토

**_검토 커밋 해시_ - [667a41b577647f0d95591a5f9928a43b976b8e25](https://github.com/hananbeer/zerem/tree/667a41b577647f0d95591a5f9928a43b976b8e25)**

**범위: `Zerem.sol`**

### 코드는 총 10시간 동안 검토되었습니다.

---

# [H-01] `unlockExponent`가 1이 아닐 때 의도한 대로 작동하지 않음

## 개념 증명(Proof of Concept)

`README.md`의 문서와 차트는 `unlockExponent == 0`일 때 자금 `unlockDelaySec` 직후 사용자가 전체 잠긴 금액을 청구할 수 있어야 함을 보여줍니다. 이것은 실제로 의도한 대로 작동하지 않습니다. `_getWithdrawableAmount` 함수를 살펴보겠습니다:

1. 잠금 해제할 금액을 계산하기 위해 다음이 있습니다: `uint256 totalUnlockedAmount = (record.totalAmount * factor) / precision;`
2. `record.totalAmount`는 총 잠긴 금액이고, `precision`은 `1e8` 값을 가진 상수이며, `factor`는 다음으로 계산됩니다:

```solidity
uint256 factor = deltaTimeNormalized ** unlockExponent;

if (factor > precision) {
         factor = precision;
}
```

1. `unlockExponent == 0`이면 `factor`는 항상 1과 같으며, 이는 `1e8`보다 작으므로 `factor == 1`입니다.
2. 이제 총 잠금 해제 금액 수학으로 돌아가면 `uint256 totalUnlockedAmount = (record.totalAmount * factor) / precision;`이 되므로 `uint256 totalUnlockedAmount = record.totalAmount / precision`이 됩니다.

예상 잠금 해제 금액은 `record.totalAmount`와 같았지만 대신 올바르지 않은 `record.totalAmount / precision`을 얻었습니다. 이제 `_getWithdrawableAmount` 함수가 호출될 때마다 수학은 동일하며 코드는 기본적으로 새로 잠금 해제된 금액이 없다고 생각할 것입니다. 이는 Zerem에 자금을 잠근 사용자가 `totalLockedAmount / 1e8` 이상을 인출할 수 없으며 다른 모든 토큰은 갇히게 됨을 의미합니다.

`unlockExponent > 1`일 때도 문제가 있습니다. 계산된 `factor`가 쉽게 `>= precision`이 되어 100%의 자금이 너무 일찍 잠금 해제되는 결과를 초래할 수 있기 때문입니다.

다음은 중요한 수학입니다:

```solidity
uint256 deltaTimeNormalized = (deltaTimeDelayed * precision) / unlockPeriodSec;

uint256 factor = deltaTimeNormalized ** unlockExponent;

if (factor > precision) {
      factor = precision;
}
uint256 totalUnlockedAmount = (record.totalAmount * factor) / precision;
```

예시 시나리오를 살펴보겠습니다:

1. `unlockPeriodSec == 100 000`, `deltaTimeDelayed == 100` 및 `precision == 1e8`이므로 `deltaTimeNormalized == 1e5`
2. `unlockExponent > 1`인 경우(예: 2와 같을 때) `factor = 1e5 ** 2`가 되어 `1e10`이 되며, 이는 `1e8`인 `precision`보다 크므로(`>`) 이제 `factor = precision`입니다.
3. 이제 `totalUnlockedAmount = record.totalAmount * factor / factor`이며 이는 `record.totalAmount`입니다.
4. 잠금 해제 기간의 1/1000만 지났고 `unlockExponent`가 단지 `2`였음에도 불구하고 사용자는 이미 잠긴 토큰을 모두 청구할 수 있습니다.

## 영향

프로토콜은 핵심 기능에서 예상대로 작동하지 않으며 사용자의 토큰이 갇히거나(가치 손실) 토큰이 너무 일찍 잠금 해제될 수 있으므로 심각도는 높음(High)입니다.

## 권장 사항

`unlockExponent` 로직을 재설계하거나 항상 선형(값 1)이 되도록 하드코딩하십시오.

## 클라이언트 응답

`unlockExponent` 로직을 제거하여 수정됨

# [M-01] `ERC20::transfer`에 대한 안전하지 않은 호출로 인해 자금이 갇힐 수 있음

## 개념 증명

`_sendFunds` 메서드에는 ERC20 토큰 전송을 위한 다음 코드가 있습니다.

```solidity
IERC20(underlyingToken).transfer(receiver, amount);
```

문제는 ERC20의 `transfer` 함수가 전송 성공 여부를 나타내는 bool을 반환한다는 것입니다. 실패 시 되돌리지(revert) 않고 `false`를 반환하는 토큰이 있고(한 예로 [ZRX](https://etherscan.io/address/0xe41d2489571d322189246dafa5ebde1f4699f498#code)), Zerem은 그렇게 하는 프로토콜과 통합될 수 있으므로 모든 유형의 ERC20 토큰과 작동해야 하므로 반환 값을 확인하지 않으면 토큰이 갇힐 수 있습니다. 다음 시나리오를 살펴보겠습니다:

1. Alice가 Zerem과 통합된 프로토콜에서 일부 토큰을 청구하려고 시도하므로 그녀의 트랜잭션은 `Zerem::transferTo`를 호출합니다.
2. 청구할 금액이 `lockThreshold`보다 작으므로 코드는 `_sendFunds` 기능으로 직접 이동합니다.
3. 전송이 실패하고 토큰이 되돌리지 않고 `false`를 반환하므로 이는 Zerem 프로토콜에 의해 설명되지 않으며 트랜잭션이 성공적으로 완료됩니다.
4. 토큰은 이제 갇혀 있으며 Alice가 더 이상 청구할 수 없습니다.

## 영향

`ERC20::transfer` 호출이 실패하면 사용자의 자금이 갇히게 됩니다. 그러나 이는 특수 클래스의 ERC20 토큰에서만 발생하므로 심각도는 중간(Medium)입니다.

## 권장 사항

OpenZeppelin의 `SafeERC20` 라이브러리를 사용하고 `transfer`를 `safeTransfer`로 변경하십시오.

## 클라이언트 응답

`SafeERC20`을 추가하여 수정됨

# [M-02] 외부 호출에 대한 가스 지원금이 불충분하여 ETH가 갇힐 수 있음

## 개념 증명

Zerem 프로토콜이 ETH를 전송하는 방식은 다음과 같습니다:

```solidity
payable(receiver).call{gas: 3000, value: amount}(hex"")
```

보시다시피 3000의 가스 지원금이 있지만 일부 스마트 계약 수신자는 ETH를 받는 데 3000 가스 이상이 필요하므로 어떤 경우에는 충분하지 않을 수 있습니다.

문제가 있는 수신자의 예:

1. 수신자는 3000 가스 이상을 사용하는 지불 가능한 폴백 메서드가 있는 스마트 계약입니다.
2. 수신자는 3000 가스 미만이지만 프록시를 통해 호출되는 지불 가능한 폴백 기능이 있는 스마트 계약으로, 호출의 가스 사용량을 3000 이상으로 높입니다.

또한 일부 다중 서명 지갑의 경우 3000 가스 이상을 사용하는 것이 필수일 수 있습니다.

## 영향

일부 수신자는 Zerem과 통합된 프로토콜에서 청구 가능한 모든 ETH에 대한 액세스 권한을 잃게 됩니다. 이를 위해서는 특별한 유형의 수신자가 필요하므로 심각도는 중간(Medium)입니다.

## 권장 사항

최소한 가스 지원금을 두 배로 늘리면 대부분의 시나리오에서 도움이 되지만 동적 구성 옵션도 생각해보십시오.

## 클라이언트 응답

가스 지원금을 두 배로 늘려 수정됨

# [M-03] 안전하지 않은 외부 호출에서 가스 그리핑(Gas griefing)/도난 가능

## 개념 증명

`unlockFor` 메서드의 `// TODO: send relayer fees here` 주석과 그 설계는 `unlockFor`가 일반적으로 릴레이어에 의해 호출될 가능성이 있음을 보여줍니다. 이것은 계약에서 새로운 공격 벡터를 열고 그것은 ETH 전송에 대한 가스 그리핑입니다.

```solidity
(bool success,) = payable(receiver).call{gas: 3000, value: amount}(hex"");
```

이제 `(bool success, )`는 실제로 `(bool success, bytes memory data)`를 쓰는 것과 동일합니다. 즉, `data`가 생략되더라도 계약이 이를 처리하지 않는다는 의미는 아닙니다. 실제로 작동하는 방식은 `receiver`에서 반환된 `bytes data`가 메모리에 복사된다는 것입니다. 페이로드가 크면 메모리 할당 비용이 매우 많이 들므로 `receiver`가 거대한 페이로드를 반환하는 폴백 함수를 구현하면 트랜잭션의 `msg.sender`, 이 경우 릴레이어는 이 페이로드를 메모리에 복사하는 데 막대한 양의 가스를 지불해야 합니다.

## 영향

악의적인 행위자가 릴레이어에 대해 가스 그리핑 공격을 시작할 수 있습니다. 그리핑 공격은 공격자에게 경제적 인센티브가 없고 릴레이어도 필요하므로 심각도는 중간(Medium)이어야 합니다.

## 권장 사항

반환 데이터를 자동으로 메모리에 복사하지 않으므로 저수준 어셈블리 `call`을 사용하십시오.

```solidity
bool success;
assembly {
    success := call(3000, receiver, amount, 0, 0, 0, 0)
}
```

## 클라이언트 응답

저수준 어셈블리 `call`을 사용하여 수정됨

# [M-04] `liquidationResolver`가 잠긴 자금의 100%를 훔칠 수 있으므로 중앙화 위험

## 개념 증명

현재 `liquidationResolver`는 다음과 같은 방식으로 잠긴 자금의 100%를 훔칠 수 있는 권한을 가집니다:

1. 잠긴 잔액이 있는 모든 사용자에 대해 `freezeFunds`를 호출합니다.
2. 청산 지연이 지날 때까지 기다려 `liquidateFunds`의 `require` 문이 성공하도록 합니다.
3. `liquidateFunds`를 호출하고 모든 사용자의 잔액을 받습니다.

이것은 `liquidationResolver`가 악의적이 되거나 타협된 경우 발생할 수 있습니다.

## 영향

중앙화 취약점은 일반적으로 악의적이거나 타협된 계정을 필요로 하며 심각도는 중간(Medium)입니다.

## 권장 사항

자금 동결/청산이 프로토콜에 필수적인 메커니즘인지 재고하십시오.

## 클라이언트 응답

인지함

# [M-05] 구성 검증 및 제약 조건 누락으로 자금이 갇힐 수 있음

## 개념 증명

프로토콜이 Zerem과 통합되는 경우 지원하는 각 `underlyingToken`에 대해 서로 다른 `Zerem.sol` 인스턴스를 배포해야 합니다. 생성자에서 일부 구성이 설정되지만 입력은 전혀 검증되지 않습니다. 이제 배포자가 잘못 구성했거나, 배포 시 오타를 냈거나, 배포 스크립트가 잘못된 경우, 명백하지 않지만 모든 잠긴 자금이 영원히 갇히게 되는 방식으로 프로토콜을 잘못 구성할 수 있습니다.

다음 시나리오를 살펴보겠습니다:

1. 배포 스크립트의 실수로 `unlockDelaySec` 또는 `unlockPeriodSec`가 거대하거나 구체적인 타임스탬프로 설정됩니다.
2. 이제 `_getWithdrawableAmount`에서 `unlockDelaySec`가 너무 크면 다음으로 인해 항상 0 인출 가능 금액을 얻게 됩니다.

```
if (deltaTime < unlockDelaySec) {
     return 0;
}
```

3. 또한 `_getWithdrawableAmount`에서 `unlockPeriodSec`가 너무 크면 `uint256 deltaTimeNormalized = (deltaTimeDelayed * precision) / unlockPeriodSec;`이 0이 되므로 항상 0을 얻게 됩니다.

즉, 사용자는 자금을 잠금 해제하기 위해 엄청난 양(무한대일 수 있음)의 시간을 기다려야 하며 `liquidateFunds` 기능으로도 잠금 해제할 수 없습니다.

## 영향

이로 인해 사용자 자금이 Zerem에 갇힐 수 있지만 배포 시 구성 오류가 필요하므로 심각도는 중간(Medium)입니다.

## 권장 사항

`Zerem.sol`의 생성자에 `unlockDelaySec` 및 `unlockPeriodSec`의 유효한 값에 대한 합리적인 제약 조건을 추가하십시오.

## 클라이언트 응답

생성자에 제약 조건을 추가하여 수정됨

# [M-06] 수수료 부과(fee-on-transfer) 메커니즘이 있는 ERC20 토큰은 특별한 처리가 필요함

## 개념 증명

일부 토큰은 전송 수수료(`STA`, `PAXG`)를 받고 현재는 아니지만 미래에 그렇게 할 수 있는 토큰(`USDT`, `USDC`)이 있습니다. Zerem은 모든 유형의 ERC20 토큰과 작동하는 프로토콜과 통합될 수 있고 Zerem도 그래야 하므로 문제가 발생할 수 있습니다.

다음 시나리오를 살펴보겠습니다:

1. Alice는 Zerem과 통합된 프로토콜에서 수수료 부과 메커니즘이 있는 일부 토큰을 청구하려고 합니다.
2. 통합된 프로토콜은 `Zerem::transferTo` 메서드를 호출하지만 전달된 `amount` 인수는 수수료를 고려하지 않습니다.
3. `transferredAmount`가 수수료로 인해 `amount`보다 작으므로 `require(transferredAmount >= amount, "not enough tokens");` 확인은 항상 실패합니다.

이런 일이 발생하면 이러한 토큰의 모든 사용자 잔액을 청구할 수 없고 영원히 갇히게 됩니다.

## 영향

수수료 부과 메커니즘이 있는 토큰을 사용하고 통합 프로토콜과 Zerem 측 모두에서 적절하게 처리되지 않으면 사용자의 이 토큰 잔액이 100% 갇힐 수 있습니다. 이는 특수한 유형의 ERC20에서만 발생하므로 심각도는 중간(Medium)입니다.

## 권장 사항

이러한 토큰의 통합에는 통합 프로토콜 측(수수료를 미리 계산하여 전달된 `amount` 인수가 올바른 값을 갖도록 함)과 아마도 Zerem 측에서 특별한 처리가 필요합니다. 이에 대한 더 나은 문서를 고려하거나 통합 프로토콜에 Zerem을 통해 이러한 토큰을 전송하지 않도록 조언하십시오.

## 클라이언트 응답

코드에 경고 주석 추가됨

# [M-07] 프로토콜은 전송 외부에서 잔액 수정 메커니즘이 있는 ERC20 토큰과 작동하지 않음

## 개념 증명

일부 토큰은 전송 외부에서 임의의 잔액 수정을 수행할 수 있습니다. 한 가지 예는 Ampleforth 스타일의 리베이스 토큰이며 에어드롭 또는 민트/번 메커니즘이 있는 다른 토큰이 있습니다. Zerem 시스템은 사용자의 잠긴 잔액을 캐시하며 이러한 임의의 수정이 발생하면 프로토콜이 오래된 정보로 작동하고 있음을 의미할 수 있습니다. 다음 시나리오를 살펴보겠습니다:

1. Alice는 Zerem과 통합된 프로토콜에서 그러한 메커니즘이 있는 일부 토큰을 청구합니다.
2. 프로토콜은 `Zerem::transferTo` 메서드를 호출하지만 전송된 금액은 `lockThreshold` 이상이므로 자금이 잠깁니다.
3. `_lockFunds`에서 전송된 금액은 `record.totalAmount`, `record.remainingAmount` 및 `pendingTotalBalances[user]`에 캐시됩니다.
4. 이 일이 있은 후 얼마 지나지 않아 토큰 잔액의 리베이스가 발생하여 이제 실제로 Zerem이 더 적은 토큰을 보유하게 되었다고 가정해 보겠습니다.
5. 이제 `unlockPeriodSec`가 지나고 Alice가 토큰을 청구하려고 하면 캐시된 금액이 Zerem 프로토콜에 보관된 실제 금액보다 많기 때문에 불가능하므로 트랜잭션이 항상 되돌아가 모든 잠긴 자금이 갇히게 됩니다.

또한 토큰의 리베이스로 인해 실제로 프로토콜 잔액이 증가하면 해당 초과 토큰은 갇히게 됩니다.

## 영향

자금이 Zerem에 갇힐 수 있지만 특수 유형의 ERC20 토큰이 필요하므로 심각도는 중간(Medium)입니다.

## 권장 사항

자금의 부분 잠금 해제를 허용하거나 프로토콜이 이러한 토큰을 지원하지 않는다고 문서화하여 통합 프로토콜이 Zerem을 통해 토큰을 전송하지 않도록 하십시오. 또한 Zerem 프로토콜에서 초과 자금을 구출하는 기능을 추가할 수 있습니다.

## 클라이언트 응답

인지함

# QA 보고서 - 낮은 심각도 및 중요하지 않은 문제

## [QA-01] 안정적인 pragma 문과 함께 최신 Solidity 버전 사용

유동적 pragma `^0.8.13` 문을 사용하는 것은 코드가 다른 컴파일러 버전으로 다른 바이트코드로 컴파일될 수 있으므로 권장되지 않습니다. 결정적 바이트코드를 얻으려면 안정적인 pragma 문을 사용하십시오. 또한 모든 컴파일러 기능, 버그 수정 및 최적화를 얻으려면 최신 Solidity 버전을 사용하십시오.

## [QA-02] NatSpec 문서 추가

모든 공개 메서드 및 변수에 대한 NatSpec 문서는 개발자와 감사자가 코드를 더 잘 이해하는 데 필수적이며 강력히 권장됩니다.

## [QA-03] 코드가 제대로 포맷되지 않음

코드를 깨끗하고 깔끔하게 유지하려면 코드 포맷터를 사용하십시오. pre-commit 훅에 `forge fmt` 명령을 추가하는 것이 좋습니다.

## [QA-04] 작업이 발생하는 메서드로 이벤트 방출 이동

`TransferFulfilled` 이벤트 방출을 `_sendFunds()`로, `TransferLocked` 이벤트 방출을 `_lockFunds()`로 이동하십시오.

## [QA-05] 주석의 오타

`recive`를 `receive`로, `timeframe`을 `time frame`으로 변경하십시오.

## [QA-06] 생성자에서 0이 아닌 주소 확인 누락

`Zerem.sol`의 생성자에 있는 모든 `address` 유형 인수에 대해 0이 아닌 주소 확인을 추가하십시오.

## [QA-07] `_sendFunds`에서 `address payable`로 캐스팅할 필요 없음

`address`를 `address payable`로 캐스팅하는 것은 `send` 또는 `transfer`를 사용할 때만 필요하므로 여기서는 필요하지 않습니다.

## [QA-08] 일부 함수는 하나로 융합될 수 있음

`freezeFunds`와 `unfreezeFunds`를 `updateFundsFreezeStatus`로 병합하십시오. 이벤트도 마찬가지입니다. 두 개의 별도 메서드와 이벤트가 필요하지 않습니다.

## [QA-09] 코드에 열린 `TODO`

`unlockFor`에 열린 `TODO`가 있습니다. 이는 감사되지 않았을 수 있는 변경 사항을 의미합니다. 해결하거나 제거하십시오.

## [QA-10] 중복 코드 제거

코드 재사용, `_getRecord`가 `_getTransferId`를 호출하도록 만드십시오. `bytes32 transferId = keccak256(abi.encode(user, lockTimestamp));` 로직이 중복되기 때문입니다. `_unlockFor`도 마찬가지입니다.

## [QA-11] 외부 종속성을 최신 버전으로 업데이트

OpenZeppelin 라이브러리 종속성이 오래된 버전을 사용하고 있습니다. 최신 버전으로 업그레이드하여 모든 보안 패치, 기능 및 가스 최적화를 얻으십시오.

## [QA-12] `pendingTransfers` 매핑에서 다른 키 사용 고려

현재 `pendingTransfers` 매핑의 키는 다음과 같이 계산됩니다.

`bytes32 transferId = keccak256(abi.encode(user, lockTimestamp));`

각 `TransferRecord`에 대해 단순한 uint256 논스를 사용하는 것만으로도 충분할 것 같습니다. 또한 더 간단하고 가스 효율적일 것입니다.

# 가스 최적화 보고서

## [G-01] `pendingTotalBalances` 매핑은 아무 용도로도 사용되지 않으므로 제거

`pendingTransfers[transferId].remainingAmount`가 거의 동일한 기능을 하므로 매핑을 제거하십시오.

## [G-02] 미리 결정된 값에 대해 `immutable` 대신 `constant` 사용

`precision` 및 `NATIVE` 저장 변수는 모두 미리 결정된 값을 가집니다. `immutable`이 아닌 `constant`로 변경하십시오. 또한 가스를 절약하기 위해 비공개(private)로 설정하십시오.

## [G-03] 저장 변수 및 함수 매개변수에 `uint8` 대신 `uint256` 사용

스토리지에서 변수 패킹을 수행하지 않는 한 `uint256`을 사용하는 것이 가장 좋습니다. 다른 모든 `uint` 유형은 어차피 메모리에서 자동으로 `uint256`으로 패딩 되기 때문입니다. 함수 매개변수에도 동일하게 적용됩니다.

## [G-04] 되돌리기 문자열 대신 사용자 정의 오류 사용

Solidity 0.8.4에는 사용자 정의 오류 기능이 추가되었습니다. 되돌리기 문자열 대신 사용하면 오류 발생 시 가스를 크게 절약할 수 있습니다. 모든 revert 문을 사용자 정의 오류 문으로 바꾸십시오.

## [G-05] 모든 `public` 함수를 `external`로 변경 가능

함수가 계약 내에서 호출되지 않으므로 `public` 대신 `external`을 사용하면 모든 함수 인수가 메모리에 복사되는 대신 `calldata`에 있으므로 가스를 절약할 수 있습니다.

## [G-06] uint 유형에 대해 `x > 0` 대신 `x != 0` 사용

`!=` 연산자는 `>`보다 가스 비용이 적게 들며, uint 유형의 경우 0이 아닌 값을 확인하는 데 사용하여 가스를 절약할 수 있습니다.

## [G-07] `x = x - y`는 `x -= y`보다 가스 비용이 적게 들며, 덧셈도 마찬가지임

모든 `-=` 및 `+=` 발생을 교체하여 가스를 절약할 수 있습니다.

## [G-08] `totalUnlockedAmount < withdrawnAmount`를 `totalUnlockedAmount <= withdrawnAmount`로 변경

`_getWithdrawableAmount` 메서드에서 `if (totalUnlockedAmount < withdrawnAmount)`를 `if (totalUnlockedAmount <= withdrawnAmount)`로 변경하면 몇 가지 계산을 건너뛰어 가스를 절약할 수 있습니다.

# 부록 - 아키텍처 고려 사항

1. 현재 설계로는 프로젝트가 지원하는 각 ERC20 토큰에 대해 별도의 `Zerem` 계약을 배포해야 합니다. 이는 그다지 효율적이지 않을 수 있으며 통합 프로토콜이나 사용자에게 훨씬 더 큰 가스 비용을 초래할 수 있습니다.
2. `README` 파일에서 지적했듯이 Zerem 통합은 침습적인 접근 방식을 가지고 있습니다. 모든 전송이 Zerem을 통과하도록 통합 프로토콜의 아웃바운드 메서드에 추가 코드 로직을 추가해야 합니다. 이는 이미 존재하는 업그레이드 불가능한 프로토콜에서는 작동하지 않으며, 더 많은 코드는 더 많은 유지 관리와 프로토콜에 대한 더 큰 공격 벡터를 의미하므로 잘 받아들여지지 않을 수 있습니다.
3. 공격자는 항상 `lockThreshold - 1 wei` 금액을 보낼 수 있으며 항상 직접 전송됩니다. 단일 블록에 여러 트랜잭션을 분산시킬 수 있으므로 몇 블록 만에 매우 많은 양의 토큰을 훔칠 수 있습니다.

