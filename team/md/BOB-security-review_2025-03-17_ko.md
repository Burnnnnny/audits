# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**bob-collective/bob-gateway** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# BOB Gateway 소개

BOB은 비트코인과 이더리움을 기반으로 하는 하이브리드 Layer-2입니다. 비트코인 사용자가 이전에 이더리움 자산을 보유하지 않고도 BOB L2에 쉽게 온보딩할 수 있도록 설계되었습니다. 사용자는 신뢰할 수 있는 릴레이어와 조정하여 사용 가능한 유동성 중 일부를 예약하고, 비트코인 메인넷에서 BTC를 전송한 다음, 릴레이어는 BOB에서 ERC20 토큰에 대한 스왑을 실행하기 위한 머클 증명을 제공할 수 있습니다.

BOB Gateway는 비트코인 사용자가 기존 ETH 보유 없이 신뢰할 수 있는 릴레이어 및 스마트 계약을 통해 BTC를 이더리움 기반 자산(wBTC 또는 tBTC 등)으로 교환하여 BOB L2에 원활하게 온보딩할 수 있도록 하는 브리지 솔루션입니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [b6686882f1bb5ea7837c7a38b2b5a0e93755d55b](https://github.com/bob-collective/bob-gateway/tree/b6686882f1bb5ea7837c7a38b2b5a0e93755d55b)

_수정 검토 커밋 해시_ - [5e6f4571933593e07423ea310a7ae7edf0393bfb](https://github.com/bob-collective/bob-gateway/tree/5e6f4571933593e07423ea310a7ae7edf0393bfb)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `CommonStructs` 
- `OfframpRegistry` 

# 발견 사항

# [M-01] `OfframpRegistry`의 오래된 `cached receiver address`로 토큰 전송됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`OfframpRegistry` 계약에서 오프램프 소유자가 주문을 수락하면 현재 수신자 주소가 주문 세부 정보에 캐시됩니다.

```solidity
    function offrampAcceptOrder(uint256 orderId) external {
        ...
        offrampOrder.receiver = offrampDetails.receiver; // update receiver so even if offramp gets deregister current valid orders are process
        ...
    }
```

이는 오프램프가 등록 취소되더라도 주문이 계속 처리될 수 있도록 하기 위해 수행됩니다. 그러나 수신자 주소가 보류 중인 주문이 처리되기 전에 업데이트되면 취약점이 발생합니다.

오프램프 소유자가 `updateOfframp`를 통해 수신자 주소를 업데이트할 때. 새 수신자 주소는 오프램프 세부 정보에 저장되지만 이전에 수락된 주문의 수신자 주소는 업데이트하지 않습니다.

```solidity
    function updateOfframp(address token, address newReceiver, uint256 newMinFeeAmount) external {
        ...
        offrampDetails.receiver = newReceiver;
        ...
    }
```

나중에 BTC 전송이 확인되고 자금이 해제될 때:

```solidity
    function verifyAndReleaseBtcTransfer(OfframpProveBtcTransferArgs calldata proveBtcTransferArgs) external {
        ...
        IERC20(offrampOrder.token).safeTransfer(offrampOrder.receiver, amountToUnlock);
        ...
    }
```

토큰은 오프램프 세부 정보의 업데이트된 주소가 아니라 캐시된 수신자 주소(`offrampOrder.receiver`)로 전송됩니다.

보안 문제(예: 개인 키 유출) 또는 운영 변경으로 인해 수신자 주소를 업데이트해야 하는 경우 이전에 수락된 주문은 여전히 오래되고 잠재적으로 손상된 주소로 토큰을 보냅니다. 이로 인해 오프램프 소유자가 진행 중인 거래에 대한 수신 주소를 업데이트하지 못해 토큰 손실이 발생할 수 있습니다.

## 권장 사항

`verifyAndReleaseBtcTransfer`에서 가능한 경우 현재 수신자를 사용하고 그렇지 않으면 캐시된 수신자로 대체합니다.

```diff
    function verifyAndReleaseBtcTransfer(OfframpProveBtcTransferArgs calldata proveBtcTransferArgs) external {
        ...

+       if (offramps[offrampOwner][offrampOrder.token].receiver != address(0)) {
+            receiver = offramps[offrampOwner][offrampOrder.token].receiver;
+        } else {
+            receiver = offrampOrder.receiver;
+        }

-        IERC20(offrampOrder.token).safeTransfer(offrampOrder.receiver, amountToUnlock);
+        IERC20(offrampOrder.token).safeTransfer(receiver, amountToUnlock);
        emit OrderProcessed(proveBtcTransferArgs.orderId, amountToUnlock, resultInfo.value, offrampOrder.token);
    }
```

# [L-01] 오프램프 제공자는 BTC를 보내지 않고도 ERC20 토큰을 받을 수 있음

OfframpRegistry 계약의 `verifyAndReleaseBtcTransfer` 함수에는 비트코인 트랜잭션을 확인하는 방법에 문제가 있습니다. 두 가지 기준만 검증합니다.

- 비트코인 트랜잭션의 출력 스크립트가 사용자가 지정한 비트코인 주소(`outputScript`)와 일치함
- 트랜잭션 값이 최소 요구 금액(`amountLocked - feesMax`) 이상임

그러나 계약은 트랜잭션이 이 특정 오프램프 주문을 이행하기 위해 특별히 이루어졌는지 확인하지 못합니다. 이로 인해 오프램프 제공자가 이 주문을 위해 특별히 BTC를 보내지 않고도 기존의 관련 없는 비트코인 트랜잭션을 악용하여 ERC20 토큰을 청구할 수 있는 취약점이 생성됩니다.

오프램프 제공자는 다음을 수행할 수 있습니다.
- `offrampAcceptOrder`를 통해 사용자의 주문을 수락함
- 주문을 이행하기 위해 새 BTC를 보내는 대신 기존 비트코인 트랜잭션(아마도 비즈니스 지불이나 환불과 같이 완전히 다른 이유로 이전에 이루어진 트랜잭션)을 제출합니다. 단, 다음 조건을 충족하는 경우:
    - 동일한 사용자 주소(`outputScript`)로 BTC를 보냄.
    - 값이 `amountLocked - feesMax` 이상임.
- 이 주문을 위해 특별히 BTC를 보내지 않고도 사용자의 잠긴 ERC20 토큰을 받음

이를 통해 이중 청구(동일한 BTC 트랜잭션을 사용하여 여러 주문 이행)가 가능합니다.

오프램프 제공자가 릴레이어 제어(오프램프에 커밋)하는 특정 주소로 UTXO를 보내고 해당 주소가 사용자에게 BTC를 전달하도록 하는 것이 좋습니다.

# [L-02] 제출자가 비활성 상태이면 오프램프는 BTC 트랜잭션을 제출할 수 없음

`verifyAndReleaseBtcTransfer` 함수에는 승인된 행위자(제출자)만 EVM 측에서 BTC tx를 확인할 수 있도록 하는 액세스 제어 검사가 있습니다. 이로 인해 오프램프 행위자는 제출자에게 의존하게 되므로 제출자가 비활성 상태이거나 소유자가 손상되어 모든 제출자의 권한을 박탈한 경우 오프램프는 BTC tx를 완료할 수 없습니다. 동시에 사용자는 EVM 측에서 주문을 환불할 수 있으므로 오프램프는 손실에 직면하게 됩니다.

시간이 `CLAIM_DELAY` 타임스탬프에 가까운 경우 오프램프가 주문에 대해서만 BTC tx를 확인할 수 있도록 하는 "비상 확인" 기능을 추가하는 것을 고려하십시오. 이렇게 하면 오프램프는 거래가 성공적으로 완료되고 BTC가 이미 전송된 동안 사용자가 주문을 환불할 수 없음을 확신할 수 있습니다.

# [L-03] createOrder()에 deadline 매개변수가 있을 수 있음

`createOrder`에는 deadline 매개변수가 없습니다. 이러한 기능은 주문 생성 트랜잭션이 멤풀에 갇히거나 주문 생성을 잊어버린 경우 사용자에게 유용할 수 있습니다.

`offrampOrderArgs` 구조체에 선택적 `deadline` 매개변수를 추가하고 `offrampAcceptOrder` 호출 중에 해당 값을 확인하는 것을 고려하십시오.

# [L-04] `txProofDifficultyFactor`에 상한이 있어야 함

`setTxProofDifficultyFactor` 함수에는 새 `txProofDifficultyFactor`에 대한 최소 제한 검사가 있지만 상한 검사는 누락되었습니다. 이로 인해 소유자가 실수로/악의적으로 `txProofDifficultyFactor`를 큰 값으로 설정하여 BTC 트랜잭션 확인 호출을 방지하고 사용자가 EVM 측에서 자금을 잠금 해제하도록 허용하는 동시에 BTC 측에서도 자산을 받을 수 있는 시나리오가 발생할 수 있습니다.

`txProofDifficultyFactor`에 대한 상한 검사를 합리적인 값(BTC 블록 시간 측면에서 `CLAIM_DELAY`보다 작음)으로 설정하는 것을 고려하십시오.

# [L-05] 오프 램프 소유자가 `BOB Gateway`에서 자산을 회수하지 못할 수 있음

Bob의 문서(https://docs.gobob.xyz/learn/introduction/roadmap/)에 따르면, `BOB은 하이브리드 L2(Layer 2)로, 비트코인 보안을 상속하면서 여러 L1에 직접 신뢰 최소화 브리지를 제공하는 새로운 종류의 롤업입니다.

현재 BOB은 로드맵의 1단계를 완료했습니다. 네트워크는 이더리움에서 낙관적 롤업으로 라이브 상태이며 DeFi 생태계에서 비트코인 유동성 스테이킹 토큰(LST)의 TVL이 증가하고 있습니다. 온체인 BTC 라이트 클라이언트는 BOB Gateway를 통해 신뢰 최소화 크로스 체인 BTC 인텐트를 강화합니다.

현재 단계에서 Bob 블록체인은 이더리움의 롤업 L2 중 하나입니다. OP/ARB와 마찬가지로 현재 단계의 솔루션을 기반으로 이 Bob 블록체인에서 리오그(re-org)가 발생할 수 있습니다.

리오그가 발생하면 악의적인 사용자가 이 이벤트를 모니터링하고 하나의 생성 주문 트랜잭션을 삽입하려고 시도할 수 있습니다. 이로 인해 오프 램프 소유자가 하나의 잘못된 오프 램프 주문을 수락하고 처리할 수 있습니다.

예를 들어:
1. Alice는 1 WBTC에 대한 하나의 주문을 생성하고 주문 ID는 1입니다.
2. 오프 램프 소유자 Bob은 이 주문을 모니터링하고 이 주문을 수락합니다.
3. Bob은 관련 전송 트랜잭션을 비트코인 블록체인으로 보내고 API addBtcTxForOrder/를 통해 이 BTC 트랜잭션을 추가합니다.
4. 리오그가 발생하면 Alice는 1 wei WBTC에 대해 하나의 주문을 삽입할 수 있으며 주문 ID는 1입니다.
5. Alice의 계정에 잔액이 충분하지 않으면 Alice의 이전 트랜잭션이 실패할 수 있습니다.
6. Bob의 evm 트랜잭션의 경우 여전히 주문 ID 1과 일치합니다.
7. 경우에 따라 Alice는 BTC 블록체인에서 0.99 BTC를 얻지만 Bob은 BTC를 잃고 예상되는 WBTC를 얻을 수 없습니다.

권장 사항:

하나의 오프 램프 주문을 식별할 때 이 주문의 모든 매개변수와 관련된 하나의 해시를 사용해야 합니다. 오프 램프 소유자가 하나의 주문을 수락하면 관련 주문의 해시 값을 입력합니다.

# [L-06] LP는 비상 오프램프 자금 인출을 방지할 수 없음

`offrampAcceptOrder` 함수에는 `whenNotPaused` 수정자가 없어 취약점이 발생합니다.

```solidity
    function offrampAcceptOrder(uint256 orderId) external {
        ...
    }
```

시스템이 일시 중지된 경우(비상 시 가능성 높음) 사용자는 `unlockFunds` 함수를 사용하여 잠긴 자금을 인출할 수 있어야 합니다. 그러나 현재 구현에서는 시스템 일시 중지 중에도 악의적이거나 손상된 LP가 주문을 수락할 수 있습니다.

주문이 Active 상태에서 Accepted 상태로 이동하면 사용자가 인출하기 전에 7일의 `CLAIM_DELAY`가 트리거됩니다.

```solidity
if (offrampOrder.status == OfframpOrderStatus.Accepted) {
    require(
        offrampOrder.timestamp + CLAIM_DELAY < block.timestamp,
        "Funds are still locked and cannot be claimed yet"
    );
}
```

이로 인해 다음과 같은 시나리오가 생성됩니다.

- 비상 상황으로 인해 시스템이 일시 중지됨
- 사용자가 `unlockFunds`를 통해 자금을 인출하려고 시도함
- 손상된 LP가 보류 중인 모든 주문을 빠르게 수락함
- 사용자는 인출하기 전에 7일(`CLAIM_DELAY`에 따라)을 기다려야 함
- 이는 사실상 시스템에서 자산을 인출할 기회를 거부합니다. 자금 손실로 이어질 수 있습니다.

`whenNotPaused` 수정자를 offrampAcceptOrder 함수에 추가하십시오.

```diff
- function offrampAcceptOrder(uint256 orderId) external {
+ function offrampAcceptOrder(uint256 orderId) external whenNotPaused {
```

# [L-07] `renounceOwnership()` 구현은 위험함

`OfframpRegistry.sol`은 `Ownable2Step`을 상속하며 함수가 재정의되지 않았으므로 `renounceOwnership()`을 통한 소유권 포기가 가능합니다. 소유자 없이 계약을 떠나면 오프램프를 추가/제거하거나 제출자를 승인/승인 취소할 수 없게 되어 수많은 바람직하지 않은 결과를 초래할 수 있습니다.

- 악의적/손상된 오프램프는 BTC를 전송하여 처리할 의도 없이 모든 주문을 수락하여 주문을 생성할 때마다 7일 동안 사용자 자금을 잠그는 방식으로 사용자를 괴롭힐 수 있습니다.
- `verifyAndReleaseBtcTransfer()` 호출을 담당하는 릴레이 계약이 손상되거나 버그가 발생하여 가동 중지 시간이 발생하면 결함이 있는 계약의 승인을 취소하고 새 계약을 승인할 수 없으므로 계약이 벽돌화됩니다.
- `OfframpRegistry.sol`은 또한 `Pausable`을 상속하므로 계약이 일시 중지된 동안 소유권을 포기할 수 있어 `createOrder()`를 영구적으로 호출할 수 없게 되어 계약이 벽돌화됩니다.

`OfframpRegistry.sol`에 다음 코드를 배치하여 호출 시 되돌리도록 `renounceOwnership()`을 재정의하는 것을 고려하십시오.

```solidity
    function renounceOwnership() public override onlyOwner {
        revert("renouncing ownership not allowed");
    }
```

