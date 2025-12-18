# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**overload-labs/farm** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Overload 정보

`Farm.sol`은 롤아웃 파밍 계약으로, 토큰 주소가 uint256으로 변환되고 ERC-6909에 의해 설명되는 ERC-20 토큰의 최소 래퍼입니다. `Router.sol`은 주변 계약이며 업데이트 및 재배포될 수 있습니다.

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

_검토 커밋 해시_ - [937907e02072601bf38bfafdfe152f507c036dcd](https://github.com/overload-labs/farm/tree/937907e02072601bf38bfafdfe152f507c036dcd)

_수정 검토 커밋 해시_ - [457af3301e9d226df7ebaea13ab3ea9cc6fbd6d0](https://github.com/overload-labs/farm/tree/457af3301e9d226df7ebaea13ab3ea9cc6fbd6d0)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Farm`
- `Router`
- `ERC6909`
- `Lock`
- `Payment`
- `TokenId`
- `TransferHelper`

# 발견 사항

# [M-01] 비표준 토큰이 제대로 설명되지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

농장에 예치할 때 예금자에게 주조되는 증서(shares)는 지정된 `amount`와 동일합니다. 그러나 전송 수수료(fee-on-transfer) 토큰의 경우 계약에 예치되는 실제 토큰 금액은 전송에 지정된 금액보다 적습니다(수수료가 아직 공제되지 않았기 때문). 따라서 예금자는 마땅히 받아야 할 것보다 더 많은 증서를 받게 됩니다.

프로토콜이 전송 수수료 토큰을 지원하려면 `transferFrom` 호출 전후에 전송된 토큰의 잔액을 확인하고 해당 수의 증서를 주조해야 합니다(`amountAfter - amountBefore`).

리베이스(rebasing) 토큰은 시간이 지남에 따라 증가 또는 감소를 통해 사용자 잔액을 동적으로 조정합니다. 사용자가 리베이스 토큰을 예치하고 동일한 양의 ERC6909 토큰을 받는 Farm 계약 내에서, 리베이스 토큰 잔액의 가변적인 특성으로 인해 문제가 발생합니다. 인출하기 전에 사용자의 리베이스 토큰 잔액이 변경되면 주조된 ERC6909 토큰과 계약에서 사용할 수 있는 실제 토큰 수량 간에 불일치가 발생합니다.

또 다른 이국적인 예는 최대 전송 처리가 있는 토큰입니다. Compound V3의 [cUSDCV3](https://etherscan.io/address/.0x9743591b23b83ed41e6abcc84016a4c7702c706e#code)와 같은 일부 토큰은 `type(uint256).max`를 문자 그대로의 최대 `uint256` 값이 아니라 발신자의 전체 잔액을 전송하라는 지시로 해석합니다. 이 동작을 악용하여 계약이 실제로 받은 토큰 수보다 더 많은 `shares`를 주조하여 자금을 유출할 수 있습니다.

## 권장 사항

- 문서화: 이러한 특수 전송 특성이 있는 토큰과 상호 작용하는 사용자의 잠재적인 오용이나 오해를 방지하기 위해 계약 코드 내에 이 동작을 명확하게 문서화하십시오.

# [M-02] 안전하지 않은 ERC20 작업 문제

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

Farm 계약은 현재 안전하지 않은 버전의 ERC20 상호 작용을 활용하고 있어 ERC20 표준을 엄격하게 준수하지 않는 토큰과 상호 작용할 때 상당한 위험을 초래할 수 있습니다. 구체적으로, [EURS](https://etherscan.io/address/0x25d772b21b0e5197f2dc8169e3aa976b16be04ac#code)와 같은 토큰에서 관찰된 것처럼 일부 토큰은 전송이 실패할 때 트랜잭션을 되돌리는(revert) 대신 false를 반환할 수 있습니다.

이 문제는 자금이 충분하지 않음에도 불구하고 성공한 것처럼 보이는 예금을 시작하여 잘못된 증서를 주조할 수 있는 악의적인 사용자에 의해 악용될 수 있습니다. 그 후 이러한 사용자는 이 증서를 소각하여 계약에서 실제 자금을 인출하여 정직한 사용자의 자산을 효과적으로 유출할 수 있습니다.

**그러나 "안전한" 방법만 사용하는 것만으로는 충분하지 않습니다.**

`TransferHelper` 라이브러리에 구현된 `safeTransferFrom` 및 `safeTransfer` 함수는 토큰 주소에 대한 저수준 호출을 수행하고 성공적인 실행을 확인합니다. 그러나 이러한 함수는 현재 토큰 주소가 실제 계약인지 확인하지 못합니다. 이러한 간과로 인해 공격자가 비계약 주소와 상호 작용하여 시스템을 악용할 수 있는 심각한 보안 취약점이 발생할 수 있습니다. 토큰 주소에 실행 코드가 없으면 저수준 호출이 되돌려지지 않아 무단 토큰 획득 또는 자금 유출이 가능할 수 있습니다.

```solidity
    function safeTransferFrom(
        address token,
        address from,
        address to,
        uint256 value
    ) internal {
        (bool success, bytes memory data) =
            token.call(abi.encodeWithSelector(IERC20.transferFrom.selector, from, to, value));
        require(success && (data.length == 0 || abi.decode(data, (bool))), "STF"); // @audit do not check if target is a contract
    }

    /// @notice Transfers tokens from msg.sender to a recipient
    /// @dev Errors with ST if transfer fails
    /// @param token The contract address of the token which will be transferred
    /// @param to The recipient of the transfer
    /// @param value The value of the transfer
    function safeTransfer(
        address token,
        address to,
        uint256 value
    ) internal {
        (bool success, bytes memory data) = token.call(abi.encodeWithSelector(IERC20.transfer.selector, to, value));
        require(success && (data.length == 0 || abi.decode(data, (bool))), "ST"); // @audit do not check if target is a contract
    }
```

다음 시나리오를 고려해 보겠습니다.

- Farm 계약은 USDT를 지원하기로 선택하여 `transferFrom` 함수를 `TransferHelper` 라이브러리의 `safeTransferFrom` 함수로 대체합니다.
- 공격자는 존재하지 않는 토큰 주소(토큰 배포 트랜잭션을 예측하거나 CREATE2 예측 가능성을 악용하거나 토큰이 멀티체인인 경우 다른 체인에서 동일한 토큰 주소를 사용)를 예측하여 예치 함수와 상호 작용하는 데 사용합니다.
- 이 상호 작용은 주소의 비계약적 특성으로 인해 되돌려지지 않아 공격자가 ERC6909 토큰을 잘못 수령할 수 있습니다.
- 이후 합법적인 사용자가 실제 토큰을 Farm 계약에 `deposit`하면 공격자는 이전에 획득한 ERC6909 토큰을 사용하여 자금을 유출할 수 있습니다.

POC:

- Alice는 미래의 토큰 주소를 예측하고 이를 사용하여 예금 함수와 상호 작용합니다.
- Alice는 실제 토큰을 예치한 것처럼 ERC6909 토큰을 받습니다.
- 토큰이 합법적으로 배포되고 Bob이 동일한 계약에 예치하면 Alice는 자신의 토큰을 사용하여 자금을 유출하여 Bob이 인출할 수 없게 만듭니다.

설정:

- 파일 생성: test/ExploitSafeTransferFrom.t.sol
- 테스트 실행: forge test -vvvvv --match-path test/ExploitSafeTransferFrom.t.sol --match-test testPOC

테스트: [링크](https://gist.github.com/thangtranth/116020d555b7a0545f44945978312d41)

## 권장 사항

1. `safeTransferFrom`, `safeTransfer` 및 `forceApprove` 함수를 구현하십시오.
2. 토큰 주소가 실제로 계약인지 확인하는 추가 검증 단계를 구현하십시오. 이는 OpenZeppelin의 `safeTransfer` 및 `safeTransferFrom` 구현 방식과 유사한 방식을 채택하여 달성할 수 있습니다.

```solidity
    function verifyCallResultFromTarget(
        address target,
        bool success,
        bytes memory returndata
    ) internal view returns (bytes memory) {
        if (!success) {
            _revert(returndata);
        } else {
            // only check if target is a contract if the call was successful and the return data is empty
            // otherwise we already know that it was a contract
            if (returndata.length == 0 && target.code.length == 0) { // @audit verify target code length
                revert AddressEmptyCode(target);
            }
            return returndata;
        }
    }
```

[링크](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol#L106-L121)

# [M-03] 예치금에서 초과 ETH 발생

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

사용자는 `msg.value > 0` 및 `token = WETH`로 `Router.sol`에서 `deposit`을 호출하여 팜에 WETH를 예치할 수 있습니다. 호출과 함께 제공된 이더는 WETH로 래핑됩니다. 래핑되어 팜에 예치되는 금액은 사용자 입력 `amount`에 의해 제어됩니다.

그러나 사용자가 제공한 이더가 실제로 예금을 제어하는 데 사용되는 `amount` 인수와 동일한지 확인하는 유효성 검사가 없습니다. 결과적으로 사용자가 `amount`보다 더 많은 이더를 제공할 수 있지만 `amount`의 WETH만 팜에 예치됩니다.

초과 ETH는 다음 호출자가 `amount = [excess ETH]`로 `deposit`을 호출하여 훔칠 수 있습니다. 호출자는 이전 호출자가 제공한 초과 ETH를 사용하여 자신의 주소로 팜에 예치합니다.

마찬가지로 사용자가 다른 토큰을 예치하려고 할 때 `msg.value > 0`으로 이 메서드를 호출하면 제공된 ETH가 기꺼이 수락되어 다음 사용자가 훔칠 수 있습니다.

## 권장 사항

`Router.sol:deposit` 함수에 대한 `token` 인수가 WETH인 경우 `amount == msg.value`인지 확인하십시오. 그렇지 않으면 `msg.value == 0`을 강제하십시오.

# [M-04] `Router.sol`의 `withdraw` 메서드는 지불 가능(payable)해서는 안 됨

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`Router.sol`의 `withdraw` 메서드는 지불 가능(payable)하므로 호출자가 함수 호출과 함께 ETH를 제공할 수 있습니다. 그러나 제공된 ETH는 계약에 의해 기꺼이 수락되고 사용자에게 반환되지 않으므로 이 메서드가 지불 가능할 이유가 없습니다.

다른 사용자는 `amount == [excess ETH] && token == WETH`로 `deposit`을 호출하여 실수로 제공된 ETH를 훔칠 수 있습니다.

## 권장 사항

`withdraw`에서 `payable` 수정자를 제거하십시오.
