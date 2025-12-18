# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**resolv-im/resolv-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Resolv 정보

Resolv는 ETH로 뒷받침되는 스테이블코인 USR을 발행하고 숏 선물 포지션으로 ETH 가격 위험을 헤지하여 미달러 대비 가치를 안정적으로 유지하는 프로토콜입니다. 또한 보험 풀인 RLP를 유지하여 USR이 초과 담보 상태를 유지하도록 하고 사용자가 예치된 담보로 이러한 토큰을 민팅하고 상환할 수 있도록 합니다.

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

_검토 커밋 해시_ - [ea4bdfae5bce7ee52d139610214d35da4c77ab4d](https://github.com/resolv-im/resolv-contracts/tree/ea4bdfae5bce7ee52d139610214d35da4c77ab4d)

_수정 검토 커밋 해시_ - [c5166ea4c016e1a3f8e3a5185818f4864a8d4317](https://github.com/resolv-im/resolv-contracts/tree/c5166ea4c016e1a3f8e3a5185818f4864a8d4317)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `DineroTreasuryConnector`
- `Treasury`

# 발견 사항

# [M-01] `instantRedeemWithPxEth` 지원 부족

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

upxETH를 사용하는 Pirex ETH의 상환 프로세스(via `initiateRedemption` 및 `redeemWithUpxEth`)는 시간이 필요하며 여러 요인에 따라 달라집니다.

- 인출하려는 사용자 수: `pendingWithdrawal`이 `DEPOSIT_SIZE`에 도달하지 않은 경우 `pendingWithdrawal`이 `DEPOSIT_SIZE`에 도달할 때까지 더 많은 사용자가 인출해야 합니다.
- 검증인의 전체 잔액을 인출하려면 먼저 완전히 종료해야 합니다. 종료 프로세스는 동시에 종료하는 다른 사람의 수에 따라 가변적인 시간이 걸립니다. 종료가 많을수록 검증인이 기다려야 하는 시간이 길어집니다.
- 비콘 체인의 종료 대기열에 의존하는 것 외에도 인출 확정 시간에 불확실성을 추가할 수 있는 오프체인 프로세스를 활용하는 Pirex ETH의 `OracleAdapter`에도 의존합니다.

Resolv가 재무(Treasury)에서 자산을 인출해야 하는 비상 상황의 경우, 프로세스가 즉각적이지 않기 때문에 `Dinero`의 자산은 필요를 충족할 수 없습니다.

## 권장 사항

`DineroTreasuryConnector` 내에서 옵션으로 `instantRedeemWithPxEth`를 구현하는 것을 고려하십시오. 이는 사용 가능한 `buffer`에 따라 달라지며 더 높은 수수료가 발생하지만 비상시 즉시 인출을 지원합니다.

# [M-02] `IERC1155Receiver` 인터페이스 ID 미지원

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`DineroTreasuryConnector`는 `IERC1155Receiver` 인터페이스 ID를 지원하기 위해 ERC-165를 구현하는 `ERC1155HolderUpgradeable`을 상속합니다. 또한 `IAccessControlDefaultAdminRules` 인터페이스 ID를 지원하기 위해 ERC-165를 구현하는 `AccessControlDefaultAdminRulesUpgradeable`도 상속합니다.

그러나 `DineroTreasuryConnector` 내의 현재 `supportsInterface` 구현은 ERC-165 검사기에서 `IERC1155Receiver` 인터페이스 ID를 제거합니다.

```solidity
contract DineroTreasuryConnector is IDineroTreasuryConnector, ERC1155HolderUpgradeable, AccessControlDefaultAdminRulesUpgradeable {

    // ...

    function initialize() public initializer {
        __AccessControlDefaultAdminRules_init(1 days, msg.sender);
        __ERC1155Holder_init();
    }

    function supportsInterface(
        bytes4 _interfaceId
    ) public view virtual override(ERC1155HolderUpgradeable, AccessControlDefaultAdminRulesUpgradeable) returns (bool isSupports) {
        return super.supportsInterface(_interfaceId);
    }
// ...
}
```

이유는 다중 상속 때문입니다. `supportsInterface`는 `DineroTreasuryConnector`가 상속하는 `ERC1155HolderUpgradeable` 및 `AccessControlDefaultAdminRulesUpgradeable` 모두에 존재합니다. Solidity 상속은 오른쪽에서 왼쪽으로 [선형화](https://docs.soliditylang.org/en/develop/contracts.html#multiple-inheritance-and-linearization)되므로 `super` 호출은 가장 오른쪽 계약인 `AccessControlDefaultAdminRulesUpgradeable`을 트리거합니다.

결과적으로 어떤 계약이 ERC-165를 사용하여 `DineroTreasuryConnector`가 `IERC1155Receiver`를 지원하는지 확인하면 false를 반환합니다.

PoC :

```typescript
describe("Check ERC165 interface", async () => {
  it("check interface id of ERC1155Receiver and AccessControlDefaultAdminRules", async () => {
    // given
    const { owner, treasury } = await loadFixture(deployOrGetFixture);

    // when
    // ERC1155 interfaceId : 0x4e2312e0
    const result = await treasury.supportsInterface("0x4e2312e0");

    // AdminRules interfaceId : 0x31498786
    const result2 = await treasury.supportsInterface("0x31498786");

    // then
    expect(result).is.false;
    expect(result2).is.true;
  });
});
```

## 권장 사항

`DineroTreasuryConnector`의 `supportsInterface`를 다음과 같이 수정하십시오.

```diff
    function supportsInterface(
        bytes4 _interfaceId
    ) public view virtual override(ERC1155HolderUpgradeable, AccessControlDefaultAdminRulesUpgradeable) returns (bool isSupports) {
-        return super.supportsInterface(_interfaceId);
+        return interfaceId == type(IERC1155Receiver).interfaceId || super.supportsInterface(interfaceId);
    }
```

# [L-01] `dineroRedeem`이 잘못된 작업 유형을 사용함

`dineroRedeem`은 `DineroRedeem` 대신 `DineroInitiateRedemption` 작업 유형을 사용합니다. 이는 잘못된 키가 잘못된 작업 유형에 매핑되므로 ETH를 상환할 때 `idempotent` 수정자에서 예기치 않은 동작을 초래할 수 있습니다.

적절한 작업 유형을 사용하는 것을 권장합니다.

```diff
//...
-   ) external onlyRole(SERVICE_ROLE) idempotent(OperationType.DineroInitiateRedemption, _idempotencyKey) whenNotPaused {
+   ) external onlyRole(SERVICE_ROLE) idempotent(OperationType.DineroRedeem, _idempotencyKey) whenNotPaused {
//...
    }
```

# [L-02] `apxETH`는 재무(Treasury)에 직접 예치되어야 함

`deposit`은 `pxETH`를 `apxETH`로 교환하는데, 이는 재무로 전송되기 전에 먼저 커넥터에 예치됩니다. `apxETH.deposit`은 수신자 지정이 가능하므로 수신자를 `address(this)` 대신 재무(즉, `msg.sender`)로 지정할 수 있습니다. 이렇게 하면 재무로의 추가 토큰 전송이 필요하지 않습니다.

```diff
        PX_ETH.safeIncreaseAllowance(address(apxETH), pxETHPostFeeAmount);
-       apxETHAmount = apxETH.deposit(pxETHPostFeeAmount, address(this));
-       IERC20(apxETH).safeTransfer(msg.sender, apxETHAmount);
+       apxETHAmount = apxETH.deposit(pxETHPostFeeAmount, msg.sender);
    }
```

# [L-03] `emergencyWithdrawETH`는 필요하지 않음

DineroTreasuryConnector.sol에는 지불 가능한 `deposit` 함수를 통해서만 ETH를 받을 수 있는 한 가지 방법이 있습니다. 이 함수에서 재무로부터 전송된 모든 ETH는 환불 처리 없이 `PX_ETH`로 변환됩니다. 따라서 계약이 ETH를 보유할 방법이 없습니다. 그러므로 계약에서 ETH를 검색하는 기능을 갖는 것은 불필요합니다.

ETH 검색이 필요한 경우 함수를 제거하거나 `receive` 함수를 도입하는 것을 권장합니다.
