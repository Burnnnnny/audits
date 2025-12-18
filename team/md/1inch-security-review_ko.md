# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적인 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 절대 보장할 수 없지만, 경험 많은 연구원들의 블록체인 프로토콜에 대한 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Pashov Audit Group**이 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두고 **limit-order-protocol** 및 **limit-order-settlement** 리포지토리에 대한 시간 제한 보안 검토를 수행했습니다.

# 1inch 소개

1inch 지정가 주문 프로토콜을 사용하면 거래자는 기존의 지정가 주문뿐만 아니라 비 ERC20 토큰 교환, 동적 환율, 주문 체결 조건 확인, 임의 코드 실행을 포함한 다양한 사용자 정의 주문을 생성하고 실행할 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향(Impact)

- 높음(High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간(Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음(Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성(Likelihood)

- 높음(High) - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 도난당하거나 분실될 수 있는 자금의 양에 비해 공격 비용이 비교적 낮습니다.

- 중간(Medium) - 조건부 인센티브가 있는 공격 벡터일 뿐이지만 여전히 발생할 가능성이 비교적 높습니다.

- 낮음(Low) - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적(Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음(High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간(Medium) - 수정해야 함

- 낮음(Low) - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_:

- [198a6d719aef8b6cd0b29e18a128f4abcf978046](https://github.com/1inch/limit-order-protocol/tree/198a6d719aef8b6cd0b29e18a128f4abcf978046)
- [d6f86ed124b38e2af114cd821a6bf3beed94193b](https://github.com/1inch/limit-order-settlement/tree/d6f86ed124b38e2af114cd821a6bf3beed94193b)

_수정 검토 커밋 해시_:

- [8c19fb95792c8fec76ed8f11554300dc0056416d](https://github.com/1inch/limit-order-protocol/tree/8c19fb95792c8fec76ed8f11554300dc0056416d)
- [9cd284fb6d9856cf297c5662805efbd85227a6a8](https://github.com/1inch/limit-order-settlement/tree/9cd284fb6d9856cf297c5662805efbd85227a6a8)

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

`limit-order-protocol`의 경우:

- `FeeTaker`

`limit-order-settlement`의 경우:

- `Settlemen`
- `SimpleSettlement`
- `extensions/BaseExtension`
- `extensions/ExtensionLib`
- `extensions/ResolverFeeExtension`
- `extensions/WhitelistExtension`
- `extensions/IntegratorFeeExtension`

# 결과

# [M-01] FeeTaker가 `unwrapWeth` 기능을 고려하지 않음

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

`FeeTaker`는 현재 `unwrapWeth` 기능이 활성화된 시나리오를 고려하지 않습니다. 이러한 간과는 `takerAsset`이 `WETH`이고 `unwrapWeth`가 활성화되어 지정가 주문 프로토콜이 `WETH`를 언래핑하고 수령 금액을 `ETH`로 메이커 리스너(`FeeTaker`)에게 직접 보낼 때 명백해집니다. 그러나 `postInteraction` 함수는 ERC20 토큰만 처리하고 ETH는 처리하지 않도록 설계되었습니다. 결과적으로 이러한 불일치는 계약이 `WETH`를 `receiver`와 `feeRecipient`에게 전송하려고 시도할 때 이러한 전송을 이행할 충분한 `WETH`가 없기 때문에 트랜잭션 실패로 이어집니다. 또한 `FeeTaker`에는 `ETH`를 받을 수 있는 receive 메서드가 없습니다. 동일한 문제가 `IntegratorFeeExtension` 계약에도 적용됩니다.

```solidity
// Taker => Maker
if (order.takerAsset.get() == address(_WETH) && msg.value > 0) {
    if (msg.value < takingAmount) revert Errors.InvalidMsgValue();
    if (msg.value > takingAmount) {
        unchecked {
            // solhint-disable-next-line avoid-low-level-calls
            (bool success, ) = msg.sender.call{value: msg.value - takingAmount}("");
            if (!success) revert Errors.ETHTransferFailed();
        }
    }

    if (order.makerTraits.unwrapWeth()) {
        // solhint-disable-next-line avoid-low-level-calls
        (bool success, ) = order.getReceiver().call{value: takingAmount}("");
        if (!success) revert Errors.ETHTransferFailed();
    } else {
        _WETH.safeDeposit(takingAmount);
        _WETH.safeTransfer(order.getReceiver(), takingAmount);
    }
```

```solidity
function postInteraction(
    IOrderMixin.Order calldata order,
    bytes calldata /* extension */,
    bytes32 /* orderHash */,
    address /* taker */,
    uint256 /* makingAmount */,
    uint256 takingAmount,
    uint256 /* remainingMakingAmount */,
    bytes calldata extraData
) external {
    uint256 fee = takingAmount * uint256(uint24(bytes3(extraData))) / _FEE_BASE;
    address feeRecipient = address(bytes20(extraData[3:23]));

    address receiver = order.maker.get();
    if (extraData.length > 23) {
        receiver = address(bytes20(extraData[23:43]));
    }

    if (fee > 0) {
        IERC20(order.takerAsset.get()).safeTransfer(feeRecipient, fee);
    }

    unchecked {
        IERC20(order.takerAsset.get()).safeTransfer(receiver, takingAmount - fee);
    }
}
```

```solidity
function _postInteraction(
    IOrderMixin.Order calldata order,
    bytes calldata extension,
    bytes32 orderHash,
    address taker,
    uint256 makingAmount,
    uint256 takingAmount,
    uint256 remainingMakingAmount,
    bytes calldata extraData
) internal virtual override {
    if (extraData.integratorFeeEnabled()) {
        address integrator = address(bytes20(extraData[:20]));
        uint256 fee = takingAmount * uint256(uint32(bytes4(extraData[20:24]))) / _TAKING_FEE_BASE;
        if (fee > 0) {
            IERC20(order.takerAsset.get()).safeTransferFrom(taker, integrator, fee);
        }
        extraData = extraData[24:];
    }
    super._postInteraction(order, extension, orderHash, taker, makingAmount, takingAmount, remainingMakingAmount, extraData);
}
```

## 권장 사항

이 문제를 해결하려면 `postInteraction` 함수 내에 트랜잭션이 `ETH`로 언래핑 된 `WETH`를 포함하는지 확인하는 추가 검사를 구현하는 것이 좋습니다. 트랜잭션이 `WETH`를 `ETH`로 언래핑 하는 것을 포함하는 경우, 계약은 `WETH`를 ERC20 토큰으로 전송하려고 시도하는 대신 `ETH`를 `receiver`와 `feeRecipient`에게 직접 전송하는 것을 처리할 수 있어야 합니다. 또한 계약에는 `ETH`를 수락하는 receive 메서드가 있어야 합니다.

# [M-02] FeeTaker가 수수료 부과(fee-on-transfer) 토큰과 호환되지 않음

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

수수료 부과 토큰이 테이커 자산으로 사용될 때 `FeeTaker.sol` 계약은 `takingAmount - tokenFee`를 받습니다. 이는 전체 `takingAmount`가 잔액에 있다고 가정하기 때문에 계약이 되돌아가게(revert) 할 수 있습니다.

```solidity
    function postInteraction(
        IOrderMixin.Order calldata order,
        bytes calldata /* extension */,
        bytes32 /* orderHash */,
        address /* taker */,
        uint256 /* makingAmount */,
        uint256 takingAmount,
        uint256 /* remainingMakingAmount */,
        bytes calldata extraData
    ) external {
        ---SNIP---
        unchecked {
>>          IERC20(order.takerAsset.get()).safeTransfer(receiver, takingAmount - fee);
        }
    }
```

## 권장 사항

수령인에게 전송하기 전에 잔액을 확인하십시오.

```diff
    function postInteraction(
        IOrderMixin.Order calldata order,
        bytes calldata /* extension */,
        bytes32 /* orderHash */,
        address /* taker */,
        uint256 /* makingAmount */,
        uint256 takingAmount,
        uint256 /* remainingMakingAmount */,
        bytes calldata extraData
    ) external {
+      uint256 balance = IERC20(order.takerAsset.get()).balanceOf(address(this));
+      if (balance < takingAmount) takingAmount = balance;

         ---SNIP---
    }
```

# [L-01] 검증되지 않은 integratorFee가 합리적인 범위 내에 있는지 확인 필요

`IntegratorFeeExtension`은 `integratorFee`가 미리 정의된 임계값(`_TAKING_FEE_BASE`) 미만인지 또는 일반적으로 허용되는 범위 내에 있는지 확인하는 검증 프로세스를 시행하지 않습니다. 이러한 검증 부족은 `integratorFee`가 예상 값을 초과하는 시나리오로 이어질 수 있습니다. 수수료를 합리적인 한도 내로 유지하는 것은 사용자의 신뢰와 계약 무결성을 유지하는 데 중요합니다.

# [L-02] 16비트 인코딩으로 인한 `timeDelta`의 약 18시간 제한

`timeDelta` 매개변수는 현재 2바이트(16비트) 인코딩으로 제한되어 있어 최대 기간이 18시간 12분 15초로 제한됩니다. 이 제한은 일반적으로 스왑 작업 및 경매 조정의 신속한 특성에는 적합하지만 사용자 유연성에 제약을 줍니다. 빈번한 단기 스왑과의 연계를 위해 선택된 기간이 확장된 범위의 이점을 누릴 수 있음이 관찰되었습니다. 인코딩을 3바이트로 늘리면 계약이 사용자에게 더 다양한 타이밍 옵션을 제공할 수 있습니다.

```solidity
function _getAuctionBump(uint256 auctionStartTime, uint256 auctionFinishTime, uint256 initialRateBump, bytes calldata pointsAndTimeDeltas) private view returns (uint256) {
      ...
      while (pointsAndTimeDeltas.length > 0) {
          uint256 nextRateBump = uint24(bytes3(pointsAndTimeDeltas[:3]));
          uint256 nextPointTime = currentPointTime + uint16(bytes2(pointsAndTimeDeltas[3:5]));
      ...
}
```

# [L-03] 검증되지 않은 timeDelta로 인한 트랜잭션 패닉

`timeDelta` 매개변수는 나눗셈을 진행하기 전에 0과 다른지 제대로 확인되지 않습니다. 이러한 간과는 트랜잭션이 예기치 않게 패닉 상태가 되는 시나리오로 이어질 수 있습니다. `timeDelta`가 0과 다른지 확인하는 검증 검사를 구현하는 것이 좋습니다.

```solidity
function _getAuctionBump(uint256 auctionStartTime, uint256 auctionFinishTime, uint256 initialRateBump, bytes calldata pointsAndTimeDeltas) private view returns (uint256) {
      ...
      while (pointsAndTimeDeltas.length > 0) {
          uint256 nextRateBump = uint24(bytes3(pointsAndTimeDeltas[:3]));
          uint256 nextPointTime = currentPointTime + uint16(bytes2(pointsAndTimeDeltas[3:5]));
          if (block.timestamp <= nextPointTime) {
              return ((block.timestamp - currentPointTime) * nextRateBump + (nextPointTime - block.timestamp) * currentRateBump) / (nextPointTime - currentPointTime); // @audit (nextPointTime - currentPointTime) is equal to uint16(bytes2(pointsAndTimeDeltas[3:5])) which is timeDelta
          }
      ...
}
```

# [L-04] Whitelist Extension이 화이트리스트가 없는 경우를 처리할 수 없음

`WhitelistExtension` 계약은 특정 리졸버만 메이커 주문을 채울 수 있도록 설계되었습니다. 그러나 화이트리스트가 없을 수 있으며, 이는 모든 리졸버가 메이커 주문을 채울 수 있음을 의미합니다. 리졸버 수가 0으로 설정된 경우 `WhitelistExtension` 계약의 `_postInteraction` 함수는 항상 되돌아갑니다(revert).

권장 사항:

- 리졸버 수가 0인 경우 `_isWhitelisted` 확인을 건너뛰도록 `WhitelistExtension` 계약의 `_postInteraction` 함수를 수정하십시오.
- 경매가 시작되었는지 확인하는 것을 화이트리스트 확인 로직에서 분리하십시오.

