# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**PKbubu/Cardex_Audit** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Cardex 소개 (About Cardex)

Cardex는 수집 가능한 TCG 카드를 토큰화하여 사용자가 본딩 커브(bonding curve)에 따라 지분을 구매, 판매 및 거래하는 동시에 이를 사용하여 덱을 구축하고 토너먼트에서 경쟁할 수 있는 게임입니다. CardexBeta 계약은 UUPS 업그레이드 가능 패턴을 따르며 핵심 거래 로직을 관리하고 가격 책정을 위해 포물선형 본딩 커브를 활용하여 사전 판매 및 공개 거래 단계를 지원합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 희박하거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약 (Security Assessment Summary)

_검토 커밋 해시_ - [2bb0e653e44038918e1f5894a1d3a4cf8848ac53](https://github.com/PKbubu/Cardex_Audit/tree/2bb0e653e44038918e1f5894a1d3a4cf8848ac53)

_수정 검토 커밋 해시_ - [5dbabb03124bce7bd412f0ce9e3fb6864ac1779f](https://github.com/PKbubu/Cardex_Audit/tree/5dbabb03124bce7bd412f0ce9e3fb6864ac1779f)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `CardexBeta`
- `ERC1967Proxy`

# 발견 사항 (Findings)

# [M-01] 사전 판매 구매자가 다른 화이트리스트 구매자의 구매를 차단할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

감사 중 스폰서가 제공한 정보에 따르면:

> Cardex에서는 각 카드에 1일의 사전 판매 단계가 있으며 그 후에는 공개로 변경됩니다.
> 사전 판매 단계는 자격이 있는 사용자를 위한 보상입니다. Cardex 백엔드가 자격이 있는 사용자를 결정합니다. 카드 소유자는 어떤 특권도 없습니다.

이제 사전 판매 메커니즘은 nonce 기반 서명 시스템을 사용하여 구매자를 승인합니다. preSaleSigner는 순차적인 nonce를 포함하는 화이트리스트 구매자를 위한 메시지에 서명합니다. 그러나 nonce는 성공적인 구매 후에만 증가하므로 구매자가 _(의도적으로/의도하지 않게)_ 구매를 실행하지 않음으로써 다른 사람을 차단할 수 있는 문제가 발생합니다.

```solidity
function buyPreSaleShares(
    uint256 id,
    uint256 amount,
    uint8 v,
    bytes32 r,
    bytes32 s
) external payable {
    require(
        cards[id].tradeStage == TradeStage.PreSale,
        "Presale only available in presale stage"
    );

    bytes32 messageHash = keccak256(
|>        abi.encode(msg.sender, id, amount, preSaleNonce)
    );
    bytes32 signedMessageHash = messageHash.toEthSignedMessageHash();
    require(
        signedMessageHash.recover(v, r, s) == preSaleSigner,
        "Invalid Signature"
    );

|>    preSaleNonce++;

    _buyShares(id, amount);
}
```

취약점은 다음과 같은 이유로 발생합니다.

1. preSaleSigner는 순차적인 nonce(예: nonce 1-10)를 사용하여 여러 구매자에 대한 메시지에 서명합니다.
2. 각 서명은 메시지의 특정 nonce 값에 연결됩니다: `abi.encode(msg.sender, id, amount, preSaleNonce)`
3. nonce는 성공적인 구매 후에만 증가합니다: `preSaleNonce++;`
4. nonce N에 대한 서명이 있는 구매자가 구매를 실행하지 않으면 nonce > N에 대한 서명이 있는 모든 구매자가 영구적으로 차단됩니다.

POC:

- 앨리스(Alice)는 nonce 1에 대한 서명을 받습니다.
- 밥(Bob)은 nonce 2에 대한 서명을 받습니다.
- 찰리(Charlie)는 nonce 3에 대한 서명을 받습니다.
- 앨리스가 구매를 실행하지 않으면 nonce는 1로 유지됩니다.
- 밥과 찰리는 nonce 2와 3으로 서명되었기 때문에 서명을 사용할 수 없습니다.

이를 통해 구매자는 단순히 구매를 실행하지 않음으로써 대기열에 있는 다른 사람들을 영구적으로 차단하여 다른 화이트리스트 구매자를 괴롭힐 수 있습니다.

## 권장 사항

현재 nonce 로직을 유지하려면 시간 기반 로직을 구현하여 시간이 지나면 다음 nonce에 대한 구매를 허용하고 사용자에게 제시간에 구매하도록 알리십시오.

## Cardex 팀 의견

오프체인 메커니즘(선착순, FCFS)을 통해 완화되었습니다.

# [M-02] 구매 및 판매에 슬리피지가 첨부되지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`buyShares` 및 `sellShares` 함수에는 슬리피지 보호 기능이 없습니다.

```solidity
function buyShares(uint256 id, uint256 amount) external payable {
    require(
        cards[id].tradeStage == TradeStage.Public,
        "Public sale only available in public stage"
    );
    _buyShares(id, amount);
}

function sellShares(uint256 id, uint256 amount) external payable {
    require(id <= cardID && id > 0, "Invalid card ID");
    require(amount > 0, "Cannot sell zero shares");
    require(
        boughtShares[id] >= amount,
        "Total shares cannot be less than initial owner shares"
    );
    _sellShares(id, amount);
}
```

이로 인해 사용자가 자금에 대해 불공정한 가치를 받게 될 수 있습니다. 이는 배포가 Abstract에 있어 선행 실행(front running)될 가능성이 낮더라도 트랜잭션 처리 순서만으로도 발생할 수 있습니다. 즉:

- 사용자가 **지분을 구매**하려고 할 때 사용자의 트랜잭션보다 먼저 처리되는 다른 구매 트랜잭션은 사용자의 가격을 상승시킵니다.
- 사용자가 **지분을 판매**하려고 할 때 사용자의 트랜잭션보다 먼저 처리되는 다른 판매 트랜잭션은 사용자의 가격을 하락시킵니다.

이는 불공정한 거래 조건과 사용자의 금전적 손실로 이어질 수 있습니다.

사용자는 `buyShares` 동안 합리적인 `msg.value`를 설정하여(초과 지불 방지) 영향을 제한할 수 있지만 `sellShares`의 경우에는 그렇지 않습니다. 판매 중에는 이전 트랜잭션으로 인해 가격이 크게 떨어질 수 있으며 사용자가 수락할 최소 가격을 지정할 수 없습니다.

## 권장 사항

사용자가 구매 및 판매 트랜잭션 모두에 대해 슬리피지 값을 전달할 수 있도록 허용하십시오.
# [M-03] `boughtShares`가 잘못 초기화됨

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

IPO 카드를 생성할 때 초기 지분은 초기 소유자에게 할당됩니다.

```solidity

            card.initialOwnerShares = _card.initialOwnerShares;
..snip
            sharesBalance[cardID][_card.cardInitialOwner] = _card
                .initialOwnerShares;

```

그러나 이 지분은 `boughtShares` 매핑에 포함되지 않습니다. 이는 회계 불일치를 생성하여 영구적인 자금 손실로 이어지며, 나중에 사용자가 지분을 판매하려고 할 때 계약이 다음을 확인하게 합니다.

```solidity
require(
    boughtShares[id] >= amount,
    "Total shares cannot be less than initial owner shares"
);
```

이로 인해 다음과 같은 시나리오가 발생합니다.

- 초기 소유자는 X 지분을 가집니다.
- 기록된 총 구매 지분은 Y입니다.
- 실제 유통되는 지분은 X + Y입니다.
- 초기 소유자가 X 지분을 판매하면 마지막 Y 지분은 영구적으로 잠깁니다.

예를 들어:

1. 초기 소유자가 100 지분을 얻는 카드가 생성됩니다.
2. 10명의 사용자가 500 지분을 구매합니다(`boughtShares[id] = 500`), _각 50 지분_
3. 초기 소유자가 100 지분을 판매합니다.
4. 400 지분만 다시 판매할 수 있습니다(마지막 100 지분은 잠김).
5. 판매를 시도하는 마지막 두 사용자는 자금을 영구적으로 잃게 됩니다.

## 코드화된 POC (Coded POC)

`forge test --match-test testPOC_ShareAccountingMismatch -vv --via-ir`로 실행

```solidity

    function testPOC_ShareAccountingMismatch() public {
        // 1. Create a card with initial owner getting 100 shares
        vm.startPrank(cardIpoSigner);
        CardexBeta.curveFactor memory factor = CardexBeta.curveFactor({
            a: 100000000000,
            b: 9850000000000000,
            c: 5000000000000000
        });

        ipoCard(
            "Card 1",
            "pokemon",
            CardexBeta.Rarity.Common,
            "URI1",
            "URI2",
            user1, // initial owner
            100, // initial owner gets 100 shares
            block.timestamp,
            factor,
            CardexBeta.TradeStage.Public
        );
        vm.stopPrank();

        // Verify initial state
        assertEq(cardex.sharesBalance(1, user1), 100, "Initial owner should have 100 shares");
        assertEq(cardex.boughtShares(1), 0, "boughtShares not counting initial owner shares");

        // 2. Have user2 buy 100 shares
        vm.startPrank(user2);
        uint256 buyValue = cardex.getBuyPriceAfterFee(1, 100);
        cardex.buyShares{value: buyValue}(1, 100);
        vm.stopPrank();

        // Verify state after user2 buys
        assertEq(cardex.sharesBalance(1, user2), 100, "Buyer should have 100 shares");
        assertEq(cardex.boughtShares(1), 100, "Total bought shares should be 100");
        assertEq(cardex.sharesBalance(1, user1), 100, "Initial owner should still have 100 shares");

        // 3. owner tries to sell all their shares - this should work
        vm.startPrank(user1);
        cardex.sellShares(1, 100);
        vm.stopPrank();

        // 4. Now user2 tries to sell their shares - this will fail
        vm.startPrank(user2);
        vm.expectRevert("Total shares cannot be less than initial owner shares");
        cardex.sellShares(1, 100);
        vm.stopPrank();

        // User2 is stuck with their shares because boughtShares is 0
        // but the check requires boughtShares[id] >= amount
        assertEq(cardex.boughtShares(1), 0, "boughtShares is 0");
        assertEq(cardex.sharesBalance(1, user2), 100, "User2 still has 100 shares but can't sell");
    }
```

## 권장 사항

1. `boughtShares`에서 초기 소유자 지분을 올바르게 회계 처리하십시오:

```solidity
function ipoCard(...) external {
    // ... existing code ...
    sharesBalance[cardID][cardInitialOwner] = initialOwnerShares;
    boughtShares[cardID] = initialOwnerShares;  // Add this line
    // ... rest of the function
}
```

## Cardex 팀 의견

이것은 설계 메커니즘이며 Cardex 문서에 표시될 것입니다.

# [L-01] 단계 전환에 대한 제한 없음

계약은 단계가 동일한 순서를 따른다고 보장하지 않습니다. 예: 카드가 공개 단계에서 사전 판매 단계로 되돌려질 수 있습니다.

```solidity
function setTradeStage(uint256 id, TradeStage tradeStage) public {
    require(
        msg.sender == cardIpoSigner,
        "Only cardIpoSigner can set trade stage"
    );
    require(id <= cardID && id > 0, "Invalid card ID");
    cards[id].tradeStage = tradeStage;  // No validation on stage transition

    emit StageUpdated(id, tradeStage);
}
```

권장 사항:

- 공개 단계에서 사전 판매 단계로 되돌리는 것을 방지하십시오.
- 적절한 단계 전환 유효성 검사를 추가하십시오.

# [L-02] 지갑은 사전 판매에 참여할 수 없음

Cardex에는 공개 및 사전 판매 거래를 모두 허용하는 로직이 포함되어 있으며, 공개 거래의 경우 누구나 액세스할 수 있으며 구매할 지분의 양에 대해 `msg.value`만 제공하면 됩니다.

그러나 사전 판매 중에는 `presaleSigner`가 서명했는지 확인하기 위해 구매자의 서명을 확인하고 검증해야 합니다.

[링크](https://github.com/PKbubu/Cardex_Audit/blob/2bb0e653e44038918e1f5894a1d3a4cf8848ac53/src/CardexBeta.sol#L366-L390)

```solidity
    function buyPreSaleShares(
..
    ) external payable {
// ..snip
        bytes32 messageHash = keccak256(
            abi.encode(msg.sender, id, amount, preSaleNonce)
        );
        bytes32 signedMessageHash = messageHash.toEthSignedMessageHash();
        require(
            signedMessageHash.recover(v, r, s) == preSaleSigner,
            "Invalid Signature"
        );

        preSaleNonce++;

        _buyShares(id, amount);
    }
```

문제는 messagerHash를 생성할 때 `msg.sender` 쿼리를 사용한다는 것입니다. 그러나 대부분의 지갑/계약은 메타 트랜잭션 로직을 구현하며, 이 경우 `msg.sender`를 쿼리하는 것은 잘못된 것입니다. 대신 트랜잭션의 실제 발신자에 액세스하려면 `_msg.sender()`를 쿼리해야 합니다. 이 경우 지갑이 유효한 서명을 받은 후 사전 판매에 참여하려고 시도하면 참여할 수 없습니다.

메타 트랜잭션을 지원하고 `_msg.sender()`를 쿼리하여 EOA인 경우 실제 `msg.sender()`를 반환하도록 하십시오.
