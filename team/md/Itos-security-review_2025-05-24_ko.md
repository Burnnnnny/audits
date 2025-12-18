# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**itos-finance/Commons** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Itos 소개

Itos Finance는 사용자 정의 가능한 금융 수익(payoffs)을 생성하기 위해 온체인 파생 상품 엔진을 사용하는 플랫폼으로, 교차 마진(cross-margin) 거래, 사일로화된(siloed) 포트폴리오, 성과 추적을 가능하게 하는 포트폴리오 관리 시스템과 결합되어 있으며, 여러 프로토콜이 유동성을 공유할 수 있도록 합니다. 이번 범위는 RFT 계약에 중점을 두었으며, 이는 ERC165 호환 RFT 지불자(payer) 지원을 포함하여 비재진입(non-reentrant) 및 재진입(reentrant) 결제 기능을 포함한 계약 간의 토큰 요청 및 지불을 처리하는 유틸리티를 제공합니다.

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

_검토 커밋 해시_ - [cba52b0e2fbd57746869d15ee48bbd83667dc38a](https://github.com/itos-finance/Commons/tree/cba52b0e2fbd57746869d15ee48bbd83667dc38a)

_수정 검토 커밋 해시_ - [bb73627474b3fdb8e1e341132a146171b603778e](https://github.com/itos-finance/Commons/tree/bb73627474b3fdb8e1e341132a146171b603778e)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `RFT`

# 발견 사항

# [H-01] 지불자가 자가 전송으로 지불을 우회하기 위해 `reentrantSettle`을 악용

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

RFTLib의 `reentrantSettle` 함수에는 악성 계약이 `IRFTPayer` 인터페이스를 구현하여 지불 의무를 완전히 피할 수 있는 취약점이 포함되어 있습니다. 이 취약점은 함수가 중첩된 호출에서 누적 잔액 변경 사항(`transact.delta`)을 추적하는 방식에서 비롯됩니다.

계약이 `reentrantSettle`을 사용하여 토큰을 요청할 때 함수는 다음을 수행합니다:
- `transact.delta[token]`에 예상 잔액 변경을 기록합니다.
- 지불자가 `IRFTPayer`를 구현하는 경우 지불자의 `tokenRequestCB`를 호출합니다.
- 누적 델타에 대해 최종 잔액을 검증합니다.

그러나 악성 지불자는 `tokenRequestCB` 중에 요청 계약으로 다시 호출하여 `payer = 요청자 주소` 및 음수 금액으로 `reentrantSettle`을 다시 호출하게 함으로써 이를 악용할 수 있습니다. 결과적으로 다음과 같은 자가 전송(self-transfer)이 발생합니다:

- 계약의 실제 토큰 잔액을 변경하지 않습니다.
- 추적된 델타를 0으로 만듭니다(원래 요청에서 `+X`, 콜백에서 `-X`).
- 토큰을 받지 못했음에도 불구하고 최종 잔액 검증을 통과합니다.

```solidity
    function reentrantSettle(
>>>     address payer, // @audit payer is set to requester
        address[] memory tokens,
        int256[] memory balanceChanges,
        bytes memory data
    ) internal returns (bytes memory cbData) {
         ...
            // Handle and track all balance changes.
            int256 change = balanceChanges[i];
            if (change < 0) {
>>>             TransferHelper.safeTransfer(token, payer, uint256(-change)); // @audit if payer == address(this), this is a self-transfer
            }
            // If we want tokens we transfer from when it is not an RFTPayer. Otherwise we wait to request at the end.
            if (change > 0 && !isRFTPayer) {
                TransferHelper.safeTransferFrom(token, payer, address(this), uint256(change));
            }

            // Handle bookkeeping.
>>>         transact.delta[token] += change; // @audit delta is reduced because change is negative
        }
         ...
        }
    }
```

다음 시나리오를 고려해 보십시오:

- VictimContract가 `reentrantSettle(MaliciousPayer, +1 ETH)`을 호출합니다.
   -> transact.delta[token] = +1 ETH
   
- `MaliciousPayer.tokenRequestCB`가 실행됩니다:
   -> VictimContract를 호출하여 `reentrantSettle(VictimContract, -1 ETH)`를 트리거 합니다.
   
- VictimContract는 1 ETH를 자가 전송(잔액 변경 없음)합니다.
   -> transact.delta[token] = +1 ETH - 1 ETH = 0
   
- 최종 검증: 예상 델타 (0) == 실제 변경 (0)
   -> 공격 성공, 악성 지불자는 아무것도 지불하지 않음

VictimContract에 자금 손실이 발생합니다.

## 권장 사항

RFTLib에서 자가 전송 방지: `payer != address(this)`인지 확인하는 검사를 추가하십시오:

```diff
function reentrantSettle(...) internal returns (bytes memory cbData) {
+   require(payer != address(this), "Self-transfer not allowed");
    ...
}
```

# [M-01] 악성 지불자에 의한 잠재적인 가스 그리핑(griefing) 공격

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`RFTLib`은 호출자가 [`IRFTPayer(payer).tokenRequestCB()`](https://github.com/itos-finance/Commons/blob/cba52b0e2fbd57746869d15ee48bbd83667dc38a/src/Util/RFT.sol#L117)를 호출하여 RFT 지불자에게 토큰 지불을 요청할 수 있는 일련의 함수를 제공합니다.

[`RFTLib#settle()`](https://github.com/itos-finance/Commons/blob/cba52b0e2fbd57746869d15ee48bbd83667dc38a/src/Util/RFT.sol#L82-L133) 또는 [`RFTLib#reentrantSettle()`](https://github.com/itos-finance/Commons/blob/cba52b0e2fbd57746869d15ee48bbd83667dc38a/src/Util/RFT.sol#L148-L240)을 실행할 때 실제 잔액 변경이 예상 금액과 일치하는지 확인하여 지불자가 지불을 회피하는 것을 방지합니다.

그러나 호출자는 `payer`가 신뢰할 수 있는지 알 수 없습니다. `IRFTPayer(payer).tokenRequestCB()` 호출에 가스 상한선이 없으므로 공격자는 악성 지불자 계약을 만들 수 있으며 호출자는 가스 그리핑의 대상이 될 수 있습니다.

## 권장 사항

호출자가 `payer#tokenRequestCB()` 호출에 대한 가스 상한선을 설정할 수 있도록 하십시오. 게다가 `payer#supportsInterface()` 호출에는 고정된 가스 제한이 있어야 합니다.

# [L-01] RFTLib과 Fee-on-Transfer 토큰의 비호환성

RFT 라이브러리가 RFT 지불자가 아닌 주소(EOA 또는 비 RFTPayer 계약)에서 토큰을 요청할 때 `safeTransferFrom`을 사용하여 정확한 요청 금액을 가져옵니다. 그러나 `fee-on-transfer` 토큰의 경우 전송 수수료가 공제되어 실제로 수신된 금액이 요청 금액보다 적습니다.

이 문제는 `settle` 및 `reentrantSettle` 함수 모두에서 발생합니다:

```solidity
    function settle(
        address payer,
        address[] memory tokens,
        int256[] memory balanceChanges,
        bytes memory data
    ) internal returns (int256[] memory actualDeltas, bytes memory cbData) {
         ...
            // If we want tokens we transfer from when it is not an RFTPayer. Otherwise we wait to request at the end.
            if (change > 0 && !isRFTPayer) {
                TransferHelper.safeTransferFrom(token, payer, address(this), uint256(change));
            }
        
         ...

            // Validate our balances.
            uint256 finalBalance = IERC20(token).balanceOf(address(this));
            actualDeltas[i] = U256Ops.sub(finalBalance, startBalances[i]);
            if (actualDeltas[i] < balanceChanges[i]) {
                revert InsufficientReceive(token, balanceChanges[i], actualDeltas[i]);
            }
        

        transact.status = ReentrancyStatus.Idle;
    }
```

```solidity
    function reentrantSettle(
        address payer,
        address[] memory tokens,
        int256[] memory balanceChanges,
        bytes memory data
    ) internal returns (bytes memory cbData) {
       ...
            // If we want tokens we transfer from when it is not an RFTPayer. Otherwise we wait to request at the end.
            if (change > 0 && !isRFTPayer) {
                TransferHelper.safeTransferFrom(token, payer, address(this), uint256(change));
            }

            // Handle bookkeeping.
            transact.delta[token] += change;
        }
        ...
    }
```

실제 수신된 금액을 계산하는 것이 좋습니다: 전체 금액이 수신되었다고 가정하는 대신 전송 전후의 실제 잔액 차이를 계산하십시오.

# [L-02] 재진입 보호 우회로 인한 승인되지 않은 재진입 호출 허용

RFTLib은 `settle()`과 `reentrantSettle()` 함수 간의 재진입 보호를 구현하지만 `request()` 및 `requestOrTransfer()` 함수를 통한 재진입 보호에는 실패합니다.

`settle()` 및 `reentrantSettle()`은 `ReentrancyStatus`를 적절하게 확인하고 업데이트하지만 `request()` 및 `requestOrTransfer()` 함수는 재진입 보호 없이 `IRFTPayer(payer).tokenRequestCB()` 및 `isSupported()`에 대한 외부 호출을 수행합니다.
이를 통해 악성 지불자 계약이 다시 진입하여 `settle()` 또는 `reentrantSettle()`을 호출할 수 있어 의도한 트랜잭션 흐름 및 잔액 검증 로직을 우회할 가능성이 있습니다.

취약점은 다음과 같은 이유로 발생합니다:
- `request()` 및 `requestOrTransfer()`는 `transact.status`를 확인하거나 수정하지 않습니다.
- 이러한 함수의 외부 호출은 보호된 함수로 다시 진입하는 콜백을 트리거 할 수 있습니다.
- 이는 `settle()`/`reentrantSettle()`의 잔액 추적이 원자적이라는 가정을 깨뜨립니다.

이 문제를 해결하려면 `ReentrancyStatus`를 `Locked`로 업데이트하여 `request()` 및 `requestOrTransfer()` 함수에 재진입 보호를 추가하십시오.

# [L-03] `settle` 및 `reentrantSettle`의 조작으로 지불자가 검증을 우회

RFTLib의 `settle` 및 `reentrantSettle` 함수 모두 실행 중 토큰 잔액 변경에 대한 독점적 제어를 가정하는 잔액 회계 메커니즘을 사용합니다. 두 함수 모두 각 토큰에 대한 시작 잔액과 예상 델타를 추적한 다음 이 값에 대해 최종 잔액을 검증합니다. 그러나 `tokenRequestCB` 콜백 동안 지불자는 전체 실행 제어권을 가지며 토큰 잔액에 영향을 미치는 요청자 계약의 별도 관련 없는 공개 함수를 호출하거나 요청자가 상호 작용하는 다른 프로토콜의 콜백을 트리거 하여 요청자의 토큰 잔액을 임의로 수정할 수 있습니다.

예를 들어 요청한 것보다 적게 전송한 다음 다른 메커니즘을 사용하여 요청자의 잔액을 늘립니다(예: 타사 계약 전송 트리거, 보상 청구, 토큰 잔액 리베이스).

```solidity
   transact.startBalance[token] = IERC20(token).balanceOf(address(this));

   ...

   // Payer callback - can execute arbitrary code
   cbData = IRFTPayer(payer).tokenRequestCB(tokens, balanceChanges, data); // @audit Payer callback - can execute arbitrary code
   
   ...

   uint256 expectedBalance = U256Ops.add(transact.startBalance[token], expectedDelta);
   uint256 finalBalance = IERC20(token).balanceOf(address(this)); // @audit Final validation assumes only tracked changes occurred
   if (finalBalance < expectedBalance) {
      revert InsufficientReceive(token, expectedDelta, actualDelta);
   }
```

이는 요청자의 자금 손실로 이어질 수 있습니다.

**권장 사항**

- `settle` 및 `reentrantSettle` 호출을 신뢰할 수 있는 지불자로 제한하십시오.
- 신뢰할 수 없는 지불자의 경우 `transferFrom` 흐름을 사용하십시오.

# [L-04] `reentrantSettle()`의 경계 없는 루프로 인한 가스 부족 오류 위험

`reentrantSettle()` 함수에는 여러 경계 없는 루프가 포함되어 있어 가스 부족(out-of-gas) 오류가 발생할 수 있습니다. 특히 이 함수는 동일한 트랜잭션 내의 중첩/재진입 시나리오에서 여러 번 호출되도록 설계되었기 때문에 문제가 됩니다. 가스 소비는 중첩된 호출에 걸쳐 복합적으로 발생하여 함수가 가스 제한 실패에 취약하게 만듭니다.

`reentrantSettle()` 함수에는 토큰 수와 재진입 깊이에 따라 확장되는 여러 가스 집약적 작업이 포함되어 있습니다:
- **기본 처리 루프** - 전송 및 부기를 위해 모든 토큰을 반복합니다:
```solidity
for (uint256 i = 0; i < tokens.length; ++i) {
    address token = tokens[i];
    // ... token processing including potential transfers and storage updates
}
```
- **외부 컨텍스트의 정리 루프** - 누적된 모든 토큰을 반복합니다:
```solidity
if (outerContext) {
    uint256 lastIdx = transact.tokens.length - 1;
    for (uint256 i = 0; i <= lastIdx; ++i) {
        // ... balance validation and cleanup operations
    }
}
```
- **외부 콜백** - 토큰 배열을 처리해야 하는 IRFTPayer.tokenRequestCB()를 호출합니다:
```solidity
if (isRFTPayer) {
    cbData = IRFTPayer(payer).tokenRequestCB(tokens, balanceChanges, data);
}
```
이로 인해 발생할 수 있는 문제:
- 가스 제한 초과로 인한 트랜잭션 실패.
- 비용이 많이 드는 작업을 강제하여 그리핑 공격의 가능성.
- `reentrantSettle()`에 의존하는 프로토콜에 대한 서비스 거부.

**권장 사항**

과도한 가스 소비를 방지하기 위해 토큰 배열에 대한 최대 경계를 추가하십시오.
