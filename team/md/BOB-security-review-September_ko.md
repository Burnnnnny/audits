# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100%의 보안을 보장할 수는 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾기 위해 시도합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**bob-collective/bob-gateway** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# BOB Gateway 소개

BOB은 비트코인과 이더리움을 기반으로 하는 하이브리드 레이어-2입니다. 이 디자인은 비트코인 사용자가 이더리움 자산을 미리 보유하지 않고도 BOB L2에 쉽게 온보딩할 수 있도록 합니다. 사용자는 신뢰할 수 있는 릴레이어(relayer)와 협력하여 사용 가능한 유동성의 일부를 예약하고, 비트코인 메인넷에서 BTC를 보낸 다음, 릴레이어가 머클 증명을 제공하여 BOB에서 ERC20 토큰으로 스왑을 실행할 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 심각한 물적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 공격 경로가 온체인 조건을 모방하는 합리적인 가정 하에 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 있습니다.
- 낮음 - 가정이 너무 많거나 희박하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (배포 전, 이미 배포되지 않은 경우)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [c26afe6b0c33e82c828de999dbb5b4279d065f8e](https://github.com/bob-collective/bob-gateway/tree/c26afe6b0c33e82c828de999dbb5b4279d065f8e)

_수정 검토 커밋 해시_ - [86c81ac6ecf3060f70f66979ec4a3cd8e0ad4f1d](https://github.com/bob-collective/bob-gateway/tree/86c81ac6ecf3060f70f66979ec4a3cd8e0ad4f1d)

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `CommonStructs`
- `Constants`
- `Gateway`
- `GatewayRegistry`
- `GatewayRegistryV2`
- `BedrockStrategy`
- `PellStrategy`
- `SegmentStrategy`
- `ShoebillStrategy`
- `SolvStrategy`

# 발견 사항

# [L-01] `PellStrategy#stakerStrategyShares` 함수는 `view`여야 함

현재 `PellStrategy#stakerStrategyShares`는 `IPellStrategyManager` 인터페이스의 외부 비-뷰(non-view) 함수 `stakerStrategyShares`를 호출하기 때문에 `view` 수정자가 없습니다:

```solidity
File: PellStrategy.sol
10: interface IPellStrategyManager {
...
14:     function stakerStrategyShares(address staker, IPellStrategy strategy) external returns (uint256);
15: }
...
19: contract PellStrategy is IStrategy, Context {
...
37:     function stakerStrategyShares(address recipient) external returns (uint256) {
38:         return pellStrategyManager.stakerStrategyShares(recipient, pellStrategy);
39:     }
40:```

그러나 현재 `pellStrategyManager` 구현에서 `stakerStrategyShares`는 `view` 함수입니다:
https://explorer.gobob.xyz/address/0x8f083EaFcbba2e126AD9757639c3A1E25a061A08?tab=contract

이는 `PellStrategy#stakerStrategyShares`를 안전하게 `view`로 제한할 수 있음을 의미하며, 이는 계약의 온체인 테스트를 단순화하고 데이터 수집기와 같은 외부 통합자가 트랜잭션을 보낼 필요 없이 계약 정보를 얻을 수 있게 합니다.

# [L-02] 전략에 대한 슬리피지(Slippage) 누락

전략들은 어떠한 슬리피지 확인도 구현하지 않습니다.

일부 전략은 브릿지 정보를 포함한 비트코인 트랜잭션을 보낼 때 사용자가 예상하는 것보다 적은 자산을 반환할 수 있습니다. 이는 브릿징 과정 중 시장 변화나 샌드위치 공격 때문일 수 있습니다. 이러한 시나리오 중 하나에서 사용자는 예상보다 적은 자산을 받을 수 있습니다.

참고: 특정 전략(예: Bedrock)은 항상 선형적으로 동등한 토큰을 반환할 수 있지만, 모든 경우에 그렇지는 않으며 일부는 프로토콜이나 다른 사용자의 외부 작업에 의존하여 결과에 영향을 미칠 수 있습니다.

전략에서 예상되는 반환 자산을 가져오고 필요할 때 적절한 슬리피지를 설정하는 것을 고려하십시오. 이 값은 비트코인 트랜잭션의 해시 데이터(예: `strategyExtraData` 내부)에 추가되어 나중에 디코딩되어 반환된 자산과 `minAmountOut` 비교로 적용되어야 합니다.

# [L-03] ETH 스왑 슬리피지 누락

`Gateway`의 `releaseFundsAndInvest()` 함수는 스왑 시 0 ETH의 `minAmountOut`을 제공합니다. 이는 사용자가 예상보다 적은 ETH를 받게 할 수 있습니다.

```solidity
swapper.swapExactTokensForNative(token, cappedAmountToSwap, 0, payable(this), new bytes(0))
```

참고: 이는 이전 감사에서 언급되었지만, 스왑할 금액이 매우 적다는 점을 감안할 때 수익성 없는 샌드위치 공격을 고려하여 인정되었습니다.

사용자가 많은 양을 스왑하는 것을 방지하는 온체인 확인이 없고, 브릿징 과정 중 ETH/BTC 가격 변동이 발생할 수 있으므로(샌드위치 공격이 없더라도), 사용자가 허용 가능한 ETH 금액을 받지 못할 수 있다는 점에 유의하는 것이 중요합니다.

ETH 스왑에 대한 슬리피지 매개변수를 추가하는 것을 고려하십시오. 그렇게 할 경우, 이는 `gatewayExtraData`에 포함되어야 하며, 이는 해시되어 BTC 트랜잭션에 포함되어야 합니다.

업그레이드 시 참고 사항: 새로운 해싱 형식과 호환되지 않을 경우 보류 중인 BTC 브릿지 트랜잭션이 릴레이되지 않을 수 있음에 유의하십시오.

# [L-04] 특정 수신자에 대해 트랜잭션이 중단될 수 있음

`Gateway` 계약은 트랜잭션을 릴레이할 때 토큰을 ETH로 스왑할 수 있습니다. 스워퍼(swapper)가 실패할 경우 트랜잭션이 중단되는 것을 방지하기 위해 try/catch 메커니즘을 사용합니다.

문제는 릴레이된 트랜잭션이 `.call` 상호작용에서 설정된 예상 가스가 2300이기 때문에 여전히 실패할 수 있다는 것입니다.

스마트 지갑과 같은 특정 수신자는 실행을 위해 2300 이상의 가스를 필요로 하는 폴백(fallback) 함수를 가질 수 있으며, 이 경우 트랜잭션은 항상 되돌려집니다.

후속 시도들도 실패할 것이며, 릴레이된 트랜잭션의 자금은 갇힌 상태로 남을 수 있습니다.

```solidity
    // After transferred back to the contract, the gas will be limited to 2300 by the transfer function before forwarding it to the
    // user supplied address. Doing the transfer directly from the AMM might not limit the gas which could cause reentrancy attack.
    ...

    try swapper.swapExactTokensForNative(token, cappedAmountToSwap, 0, payable(this), new bytes(0)) returns (
        uint256 outAmount
    ) {
        receivedEthFromSwap = outAmount;
        (bool sent,) = _proveBtcTransferArgs.recipient.call{
            value: receivedEthFromSwap,
            gas: _proveBtcTransferArgs.ethTransferGasLimit
        }("");
@>      require(sent, "Could not transfer ETH");

        // now subtract the amount that was swapped
        scaledAmountToSendToUser -= cappedAmountToSwap;
    } catch {
        emit EthSwapFailed();
        IERC20(token).safeDecreaseAllowance(address(swapper), cappedAmountToSwap);
    }
```

참고: ETH 스왑 함수는 EOA가 운영할 가스를 갖도록 소량의 BTC 토큰을 ETH로 스왑하는 것으로 예상됩니다. 그러나 사용자가 더 많은 양을 스왑하여 스마트 지갑이나 다른 계약으로 보내는 것을 방지하는 온체인 제한은 없습니다. 따라서 이러한 경우에 대한 완화 조치가 제안됩니다.

가능한 한 가지 해결책은 `try` 문 안의 함수가 토큰 스왑 + 예상 가스 한도로 전송을 모두 시도하는 것입니다.

이렇게 하면 작업이 실패하더라도 사용자는 수신자 주소로 BTC 토큰을 받게 되며 트랜잭션은 릴레이됩니다.

참고: 이는 사용자에게 ETH를 제공하지는 않지만(스왑이 실패한 경우와 동일한 완화를 제공함), 트랜잭션이 중단되는 것을 방지합니다.

# [L-05] ETH 전송 가스 한도 매개변수의 일관성 없는 사용

`ethTransferGasLimit` 매개변수는 소비되는 가스를 제한하고 가능한 재진입 공격을 방지하기 위해 `proveBtcTransferArgs`의 일부로 `Gateway`에 전달됩니다.

이 값은 릴레이어가 `GatewayRegistry`에서 `proveBtcTransfer()`를 호출함으로써 제공됩니다.

값이 너무 높으면 릴레이어는 가스에 더 많은 비용을 지출하게 되며 재진입 기회를 열어줍니다. 값이 너무 낮으면 ETH를 보낼 수 없으므로 트랜잭션이 되돌려집니다. 따라서 적절한 값을 설정하는 것이 중요합니다.

또한 릴레이될 사용자가 제공한 BTC 전송 인수에 무결성 확인이 추가되었지만, `ethTransferGasLimit` 값은 그 안에 포함되지 않았다는 점에 주목할 필요가 있습니다.

이전의 모든 사실을 고려할 때 두 가지 가능한 문제가 있습니다:

- `ethTransferGasLimit`이 릴레이어에 의해 임의로 설정되는 경우, 이는 BTC 트랜잭션에서 오는 해시 값의 일부가 아니므로 `proveBtcTransferArgs`에 속해서는 안 됩니다. 또한 사용자 트랜잭션을 차단하는 구성 오류의 가능성으로 이어질 수 있습니다.
- `ethTransferGasLimit`이 사용자에 의해 제공되고 릴레이어에 의해 맹목적으로 릴레이되는 경우, 이는 누구에게나 이전에 언급된 공격 벡터를 열어줍니다.

## 권장 사항

`ethTransferGasLimit` 값을 누가 설정할 것으로 예상되는지에 따라 두 가지 접근 방식을 제안할 수 있습니다.

값이 프로토콜에 의해 제공되고 고정될 것으로 예상되는 경우, 릴레이어 측의 오류(손상된 경우에도)를 방지하므로 상수 또는 구성 가능한 값을 권장합니다. 또한 `ethTransferGasLimit` 값을 `proveBtcTransferArgs` 구조체 외부로 이동하는 것이 좋지만, 현재 함수 인터페이스가 변경되므로 호환성 문제를 방지하기 위해 주석으로 충분할 것입니다.

값이 사용자에 의해 제공되고 나중에 릴레이될 것으로 예상되는 경우, 추가 조치를 취해야 합니다. 최소값과 최대값을 확인해야 합니다(수수료가 충분한 경우 릴레이어의 추가 비용 고려). 값은 또한 무결성 확인에 포함되어야 합니다(그리고 비트코인 트랜잭션 해시 데이터에 추가됨).

# [L-06] 제공된 추가 데이터를 통한 가스 폭탄

사용자는 자신의 트랜잭션이 브릿지될 수 있도록 릴레이될 `gatewayExtraData` 및 `strategyExtraData` 값을 제공할 수 있습니다. 데이터가 해시되므로 비어 있거나 거대하더라도 비용이 동일하므로 해당 값의 길이에 관계없이 비트코인 체인에서 추가 비용이 들지 않습니다.

문제는 릴레이어가 해당 값이 사용되지 않더라도 큰 calldata 값으로 트랜잭션을 릴레이하기 위해 추가 가스를 지불해야 한다는 것입니다.

공격자는 이를 악용하여 릴레이어에 대한 가스 폭탄을 생성할 수 있습니다.

최소 예상 수수료를 고려하여 `gatewayExtraData` 및 `strategyExtraData`의 길이가 예상 용도에 맞는 합리적인 한도 미만이어야 합니다.

이 값은 구성 가능하거나 상수일 수 있지만, 그렇지 않으면 사용자 트랜잭션을 차단하므로 가능한 합법적인 시나리오에 충분해야 합니다.

