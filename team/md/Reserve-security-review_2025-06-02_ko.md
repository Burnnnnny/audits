# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**reserve-protocol/reserve-index-dtf** 및 **reserve-protocol/trusted-fillers** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Reserve Folio & Trusted Fillers 정보

Reserve Folio는 ERC20 토큰 포트폴리오를 생성하고 관리하기 위한 인덱스 바스켓 프로토콜로, 모듈식 및 거버넌스 주도 재조정 시스템을 통해 자산 배분을 정밀하게 제어할 수 있습니다. 이는 거버넌스가 정의한 매개변수 내에서 포트폴리오를 재조정하기 위해 역할 기반 경매 프로세스를 사용하여 토큰 가중치와 가격의 제어된 변경을 허용합니다.

또한 범위에는 Trusted Fillers가 포함되었는데, 이를 통해 Folio는 승인된 외부 당사자와 비동기적으로 거래를 결제할 수 있어 CoW Swap과 같은 프로토콜과 통합하면서 EIP-1271 기반 검증을 통해 거래 규칙 및 제한을 강제할 수 있습니다.

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

_검토 커밋 해시:_

- [a48f777e3f1cffecb3c71cbb5a88af73eb4f7626](https://github.com/reserve-protocol/reserve-index-dtf/tree/a48f777e3f1cffecb3c71cbb5a88af73eb4f7626)
- [2e3eae8fd3d352240aa48a2e2b3c0168e8229e71](https://github.com/reserve-protocol/trusted-fillers/tree/2e3eae8fd3d352240aa48a2e2b3c0168e8229e71)

_수정 검토 커밋 해시:_

- [79bcf277648f3a424dea46d2d8e3045e03c43c62](https://github.com/reserve-protocol/reserve-index-dtf/tree/79bcf277648f3a424dea46d2d8e3045e03c43c62)
- [2bc2e40955d27e786e97e93aeeb4f68d96870c83](https://github.com/reserve-protocol/trusted-fillers/tree/a9169ec805e2e02957caa18403d6b65c7f3f52c0)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `TrustedFillerRegistry`
- `CowSwapFiller`
- `Constants`
- `Folio`
- `MathLib`
- `RebalancingLib`
- `Versioned`
- `StakingVault`
- `UnstakingManager`
- `FolioDeployer`
- `GovernanceDeployer`
- `IBidderCallee`
- `IFolio`
- `IFolioDAOFeeRegistry`
- `IFolioDeployer`
- `IFolioVersionRegistry`
- `IGovernanceDeployer`
- `IRoleRegistry`

# 발견 사항

# [M-01] Filler 상태 조작으로 인한 Folio 작업 전반의 서비스 거부

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`swapActive()`가 `true`를 반환하면 되돌리는 `sync()` 수정자가 `Folio` 및 `FolioDAOFeeRegistry`의 많은 중요한 함수에서 사용되므로, 이를 악용하여 다음을 포함한 프로토콜 작업에 대한 DoS를 유발할 수 있습니다.

1. `mint()` 및 `redeem()` 함수, 사용자가 포지션에 진입하거나 나가는 것을 차단합니다.
2. 경매 함수, 프로토콜이 거래를 실행하는 것을 방지합니다.
3. 수수료 분배 함수, 프로토콜의 경제 모델을 방해합니다.
4. 매개변수 업데이트: `removeFromBasket()` 및 `FolioDAOFeeRegistry()` folio의 수수료 설정 함수.

`{Folio.createTrustedFiller(); filler.rescueToken();}`을 사용하여 해당 작업에 프론트러닝 공격을 수행함으로써 가능합니다.

공격은 다음 시퀀스로 대상 트랜잭션을 프론트러닝하여 실행할 수 있습니다.

1. `Folio.createTrustedFill()` 다음에 `filler.rescueToken()`을 호출하는 계약을 배포하여 내부 토큰 잔액을 조작합니다.
2. 이로 인해 `swapActive()`가 `true`를 반환하게 되어 필러가 닫히는 것을 방지하고 후속 `sync()`를 유발합니다.

## 권장 사항

가능한 옵션:

- `rescueToken()`을 `internal`로 제한하고 `closeFiller()`를 통해서만 호출할 수 있도록 합니다.
- `rescueToken()`을 제한하기 위해 액세스 제어를 추가합니다.
- `swapActive()`가 `false`일 때만 `rescueToken()`을 호출할 수 있도록 제한합니다. 이를 통해 필러가 무기한 가짜 활성 상태에 잠기는 것을 방지하고 프로토콜 전반의 서비스 거부 시나리오를 피할 수 있습니다.
- 계약이 초기화된 블록 동안 rescueToken이 호출되는 것을 허용하지 않는 확인을 추가합니다(block.number == blockInitialized).

# [L-01] `getBid()`가 과거나 미래의 타임스탬프에 대해 잘못된 결과를 반환함

`Folio.getBid()` 함수는 지정된 `timestamp`에 진행 중인 경매에 대한 경매 입찰 매개변수(sellAmount, bidAmount 및 price)를 검색하는 데 사용됩니다. 그러나 경매 가격은 제공된 타임스탬프를 기반으로 올바르게 계산되지만 `sellAmount` 및 `bidAmount` 계산은 현재 `totalSupply()`에 의존합니다.
`totalSupply()`는 시간이 지남에 따라(민팅, 수수료 인플레이션 등으로 인해) 변경될 수 있으므로 **요청된 타임스탬프가 현재 블록이 아닐 때 현재 `totalSupply()`를 사용하면** 잘못되고 일관성 없는 입찰 매개변수가 생성됩니다. 이로 인해 오프체인 시뮬레이션이나 과거나 미래의 입찰 추정치가 필요한 사용 사례에서 `getBid()`에 의존하는 것이 안전하지 않습니다.

```solidity
    function getBid(
        uint256 auctionId,
        IERC20 sellToken,
        IERC20 buyToken,
        uint256 timestamp,
        uint256 maxSellAmount
    ) external view returns (uint256 sellAmount, uint256 bidAmount, uint256 price) {
        return
            _getBid(
                auctions[auctionId],
                sellToken,
                buyToken,
                totalSupply(), <@ should be at the snapshot supply at timestamp
                timestamp != 0 ? timestamp : block.timestamp,
                0,
                maxSellAmount,
                type(uint256).max
            );
    }
```

**권장 사항:**

시간에 따른 `totalSupply`를 스냅샷하거나 추적하는 메커니즘을 도입하고 `sellAmount` 및 `bidAmount`를 계산할 때 요청된 `timestamp`에 해당하는 값을 사용하십시오.
또는 잘못된 가정을 피하기 위해 `getBid()`는 `timestamp == block.timestamp`일 때만 사용되어야 함을 명확하게 문서화하십시오.

# [L-02] 필러가 진행 중일 때 `toAssets()`가 오해의 소지가 있는 결과를 반환할 수 있음

`FoliotoAssets()` 함수는 `activeTrustedFill` 계약의 잔액을 포함합니다. 경매 스왑이 진행 중인 경우 스왑이 부분적으로 완료되었는지 완전히 완료되었는지에 따라 매수 토큰 및/또는 매도 토큰이 모두 포함될 수 있습니다. 결과적으로 주어진 지분 금액에 대해 반환된 자산 금액이 부정확해지고 활성 필러의 현재 상태에 따라 변동될 수 있습니다. 이는 정확한 정보를 위해 이 **뷰 함수**에 의존하는 소비 프로토콜이나 사용자를 오도할 수 있습니다.

**권장 사항:**

활성 필러가 없을 때만 `toAssets()`를 호출할 수 있도록 메커니즘을 구현하거나(예: `require(address(activeTrustedFill) == address(0))`), 미정산 필러 잔액 포함으로 인해 필러가 활성 상태일 때 자산 금액이 부정확할 수 있음을 함수 주석에 명확하게 문서화하는 것을 고려하십시오.

# [L-03] `isValidSignature()`의 검증이 좋은 가격에 부분 체결을 방지함

`CowSwapFiller` 계약은 주문의 매도 금액이 필러의 현재 매도 금액보다 작거나 같아야 한다는 엄격한 요구 사항으로 주문을 검증합니다.

```solidity
function isValidSignature(bytes32 orderHash, bytes calldata signature) external view returns (bytes4) {
    --- SNIPPED ---

    uint256 orderPrice = Math.mulDiv(order.buyAmount, D27, order.sellAmount, Math.Rounding.Floor);
@>  require(order.sellAmount <= sellAmount && orderPrice >= price, CowSwapFiller__OrderCheckFailed(100));

    --- SNIPPED ---
}
```

이로 인해 주문이 생성된 시간(CoW 프로토콜 API를 통해 오프체인)과 온체인에서 결제되는 시간 사이에 타이밍 문제가 발생합니다.

- 봇이 CoW 프로토콜 주문을 제출할 때 일반적으로 `Folio.getBid()`를 사용하여 현재 `sellAmount` 및 `buyAmount`를 가져옵니다.
- 주문 생성과 결제 사이에 사용 가능한 `sellAmount`가 변경될 수 있습니다(예: 다른 입찰 또는 상환으로 인해).
- `sellAmount`가 감소하면 주문 가격이 여전히 유리하고 부분적으로 채워질 수 있더라도 결제가 실패합니다.

**다음 시나리오를 고려하십시오.**

1. `AUCTION_LAUNCHER`가 바스켓을 1000 ETH에서 500 ETH로 재조정하고 1,500,000 USDT를 구매하기 위해 경매를 시작합니다(시작 가격: `3000 USDT/ETH`).
2. 봇은 경매 이벤트를 수신한 다음 `Folio.getBid()`의 현재 값을 사용하여 `500 ETH → 1,500,000 USDT`에 대한 주문을 CoW 프로토콜 API를 통해 제출합니다.
3. 결제 전에 다른 입찰자가 사용 가능한 ETH의 일부를 소비합니다. 필러는 이제 `400 ETH`만 사용할 수 있습니다.
4. 온체인 결제 중:

   - 필러 계약은 `sellAmount = 400 ETH` 및 `buyAmount = 1,199,600 USDT`(감쇄된 경매 가격)로 `pre-hook`을 통해 생성됩니다.
   - 따라서 필러의 가격은 `2999 USDT/ETH`로 설정됩니다.
   - 결제는 `order.sellAmount = 500`으로 원래 주문을 검증하려고 시도합니다.
   - 주문 가격(`3000`)이 현재 가격(`2999`)보다 좋음에도 불구하고 `order.sellAmount: 500 <= sellAmount: 400`이 `false`이므로 서명 검증이 실패합니다.

5. 해결사가 해당 블록에서 주문을 부분적으로 채울 수 있더라도(예: 요청된 가격으로 `~600,000 USDT`에 `200 ETH`), 이는 더 나은 가격에 순 긍정적인 실행이지만 위의 검증에 의해 차단됩니다.

아래는 주문이 단일 결제로 완전히 충족되지 않고 부분적으로 채워진 예입니다.
https://explorer.cow.fi/orders/0x358c3cf03715224c26f8043c94502c4649e8c59e94d43f16a90e2f8c72e3ad41a53a13a80d72a855481de5211e7654fabdfe3526685b2543/?tab=fills

**권장 사항:**

승인을 통한 `sellToken` 풀(pull)이 제한적이고 `orderPrice`가 이미 허용 가능한 가격을 강제하고 있음을 감안할 때(`order.buyAmount` 및 `order.sellAmount` 모두 고려), 검증을 위해 `orderPrice`에만 의존하고 `order.sellAmount <= sellAmount` 확인을 제거하는 것이 적절할 것입니다.

# [L-04] `swapActive()`가 잘못된 상태를 반환하도록 조작될 수 있음

`swapActive()`는 중요한 흐름에 의해 호출됩니다.

1. `stateChangeActive()` --> `swapActive()`
2. `sync()` --> `_poke()` --> `_closeTrustedFill()` --> `closeFiller()` --> `swapActive()`

**사례 1:**
외부 통합 프로토콜은 `stateChangeActive()`를 호출하고 Folio 상태를 신뢰하기 전에 `(false, false)`를 보기를 원합니다. 가정:

- `sellAmount`는 `100`입니다.
- `sellTokenBalance`는 `98`입니다. 즉, 스왑은 여전히 활성 상태이며 부분 매도만 발생했습니다.
- 이상적으로 외부 프로토콜은 `(false, true)`를 보아야 합니다.
- 공격 시나리오: 공격자가 `stateChangeActive()` 호출을 앞질러 `2` sellToken을 기부합니다.
- 이제 `swapActive()` 함수 내부에서 `if (sellTokenBalance >= sellAmount)`가 `true`로 평가되고 스왑이 완료된 것으로 보고됩니다. 즉 `false`가 반환됩니다.
- 외부 프로토콜은 일관성 없는 상태 및 잔액을 신뢰합니다.

buyToken을 기부하여 `minimumExpectedIn > buyToken.balanceOf(address(this))`가 `true` 대신 `false`를 반환하도록 하는 유사한 공격이 마운트될 수 있음에 유의하십시오.

**사례 2:**
이상적으로 모든 `sync()` 수정자 보호 함수는 스왑이 활성 상태인 경우 되돌려야 합니다. `closeFiller()`가 다음을 확인하기 때문입니다.

```js
require(!swapActive(), BaseTrustedFiller__SwapActive());
```

`mint()`, `redeem()`, `distributeFees()` 등과 같은 거의 모든 중요한 Folio 함수는 `sync()`로 보호됩니다. 그러나 위와 동일한 공격 경로로 인해 프로토콜은 스왑이 종료되었다고 믿도록 속을 수 있습니다. 이를 통해 함수 호출이 진행되고 잠재적으로 일관성 없는 상태가 될 수 있습니다.

**권장 사항:**

`swapActive()` 내부에서 `balanceOf()`를 사용하는 대신 회계 변수를 통해 내부적으로 잔액을 추적하십시오.

# [L-05] `Folio.bid()`의 엄격한 매도 금액으로 인한 DoS 및 경매 실행 손실

`Folio.bid()` 함수는 입찰이 `minSellAmount == sellAmount == maxSellAmount`가 되도록 엄격한 `sellAmount`를 지정하도록 강제합니다.

```solidity
function bid(
    //...
    uint256 sellAmount,
    //...
) external nonReentrant notDeprecated sync returns (uint256 boughtAmt) {
    Auction storage auction = auctions[auctionId];

    // checks auction is ongoing and that boughtAmt is below maxBuyAmount
    (, boughtAmt, ) = _getBid(
        --- SNIPPED ---
        sellAmount,             //> minSellAmount
        sellAmount,             //> maxSellAmount
        maxBuyAmount
    );

    --- SNIPPED ---
}
```

사용 가능한 매도 잔액은 `RebalanceLib::getBid()`의 실행 시간에 확인됩니다.

```solidity
require(sellAvailable >= params.minSellAmount, IFolio.Folio__InsufficientSellAvailable());
```

이로 인해 입찰 생성과 실행 사이에 프로토콜 잔액 상태가 변경되면 입찰이 되돌려질 수 있는 시나리오가 생성되며, 이는 다음을 통해 발생할 수 있습니다.

1. 다른 입찰: 성공적인 입찰마다 `sellToken` 잔액이 줄어들 수 있습니다.
2. 상환: 이는 아래와 같이 계산된 `sellToken` 잔액과 `sellLimitBal` 계산 모두에 영향을 미칩니다.
   ```solidity
    uint256 sellLimitBal = Math.mulDiv(sellLimit, params.totalSupply, D27, Math.Rounding.Ceil);
    uint256 sellAvailable = params.sellBal > sellLimitBal ? params.sellBal - sellLimitBal : 0;
   ```

이 문제는 프론트러닝을 통해 악용되거나 정상적인 프로토콜 사용을 통해 자연스럽게 발생하여 입찰 실행을 차단할 수 있습니다.
경매 가격은 시간이 지남에 따라 하락하므로 프로토콜은 유리한 실행 기회를 놓칠 수 있습니다.

**다음 시나리오를 고려하십시오**:

#### 초기 상태

0. `totalSupply()` 1000주, 1000 ETH에서 500 ETH로 줄이고 1,500,000 USDT를 구매하는 재조정 목표:

   - 1 BU는 현재 1 ETH 0 USDT로 구성됩니다.
   - 1 BU 목표는 0.5 ETH, 1500 USDT로 구성됩니다.

1. 사용자 A는 전체 입찰에 대한 트랜잭션을 제출합니다: 시작 가격(가장 유리한 가격)에 500 ETH → 1,500,000 USDT.
2. 사용자 B는 최소 입찰로 프론트러닝합니다: 0.0003333 ETH → 1 USDT.
3. 매도 토큰 잔액이 999.9996667 ETH로 감소합니다.
4. `sellAvailable`은 499.9996667 ETH(999.9996667 - 500 ETH)가 됩니다.
5. 사용자 A의 트랜잭션이 실행될 때 `499.9996667 >= 500` 확인이 실패합니다.
6. 사용자 A의 입찰이 되돌려지고 프로토콜은 유리한 가격에 대규모 입찰을 실행할 기회를 잃습니다.

**권장 사항**

`Folio.bid()`를 통해 다른 `minSellAmount` 및 `maxSellAmount` 값을 가진 입찰을 수락하여 `sellAmount` 범위를 허용하고, 실행 시간에 범위 내에서 가능한 만큼 채우도록 허용하십시오.

또는 활성 경매 중에 `redeem()`을 동결하거나 경매 중에 상환 수수료를 도입하여 이러한 공격을 억제함으로써 그리핑(griefing)을 방지하십시오.
