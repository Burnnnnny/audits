# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**frontrun-market/contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Frontrun Market 소개 (About Frontrun Market)

포인트, 에어드랍 할당, 프리마켓 토큰 및 NFT 화이트리스트를 거래하기 위한 탈중앙화 P2P OTC 거래 플랫폼입니다. 사용자는 담보를 예치하여 포인트나 에어드랍 할당을 판매하거나 구매할 수 있으며, 주문 결제 시 판매자/구매자에게 반환됩니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
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

**_검토 커밋 해시_ - [4fe59a4a1c69a6accb99e32cbb044438077eeb2d](https://github.com/frontrun-market/contracts/tree/4fe59a4a1c69a6accb99e32cbb044438077eeb2d)**

**_수정 검토 커밋 해시_ - [35c836d5ea1c5ffd2aa68762a856d2837d8b4853](https://github.com/frontrun-market/contracts/tree/35c836d5ea1c5ffd2aa68762a856d2837d8b4853)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `FrontrunMarket`
- `FrontrunMarketBlast`

# 발견 사항 (Findings)

# [M-01] 오퍼(Offer) 생성 시 과도한 `msg.value` 손실

## 심각도

**영향:** 높음 (High), 자금 영구 손실

**가능성:** 낮음 (Low), 사용자의 실수가 필요함

## 설명

`msg.value`가 필요한 것보다 크거나 같으면 유효한 것으로 간주됩니다:

```solidity
  function newOfferETH(
    uint8 offerType,
    bytes32 tokenId,
    uint256 amount,
    uint256 value,
    uint256 minAmount
  ) external payable nonReentrant {
    ...

    // collateral
    uint256 collateral = (value * $.config.pledgeRate) / WEI6;

    uint256 _ethAmount = offerType == OFFER_BUY ? value : collateral;
@>  require(_ethAmount <= msg.value, 'Insufficient Funds');
    ...
  }
```

그러나 지불해야 할 금액은 정적이며 제출된 오퍼 매개변수에만 의존하므로, 과도한 `msg.value`는 사용자 측의 실수를 의미합니다. 예를 들어, 거래를 수행하는 소프트웨어에 오류가 포함되어 있는 경우입니다.

## 권장 사항

과도한 `msg.value`를 허용하지 마십시오:

```diff
-   require(_ethAmount <= msg.value, 'Insufficient Funds');
+   require(_ethAmount == msg.value, 'Insufficient Funds');
```

# [M-02] ETH 전송 방해(Greifing)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`forceCancelOrder()`는 ETH 환불 시 구매자와 판매자에게 ETH를 보내기 위해 직접 주소 호출(direct address call)을 사용합니다.

```
    if (offer.exToken == address(0)) {
      // refund ETH
      if (buyerRefundValue > 0 && buyer != address(0)) {
        (bool success,) = buyer.call{value: buyerRefundValue}('');
        require(success, 'Transfer Funds to Seller Fail');
      }
      if (sellerRefundValue > 0 && seller != address(0)) {
        (bool success,) = seller.call{value: sellerRefundValue}('');
        require(success, 'Transfer Funds to Seller Fail');
      }
```

구매자와 판매자 모두 이러한 호출을 되돌려(revert) 서로를 방해할 수 있으며, 양쪽 모두에 대한 환불 실행을 차단하고 환불을 완료하기 위한 적절한 시간/조건을 관리할 수 있습니다. 최악의 경우 협박 수단으로 사용될 수 있습니다.
또한 수신자가 환불 로직을 설계하지 않은(그리고 ETH를 받을 수 없는) 스마트 계약인 경우와 같이 의도하지 않은 되돌림일 수도 있습니다.

## 권장 사항

사용자가 호출 및 전송을 받지 못하고 계약이 보류 중인 출금을 저장하는 청구(claiming) 로직을 구현하는 것을 고려하십시오. 사용자는 별도의 기능을 사용하여 이러한 자금을 청구해야 합니다.
이 로직은 `settleFilled()`, `settle2Steps()` 및 `cancelOffer()`를 포함한 모든 ETH 전송에 대해 구현할 가치가 있습니다.

# [L-01] 두 주문 상태가 동일한 값을 공유함

`STATUS_ORDER_CANCELLED`와 `STATUS_ORDER_SETTLE_CANCELLED`는 모두 동일한 값 3을 갖습니다.

```
  uint8 private STATUS_ORDER_SETTLE_CANCELLED = 3;
  uint8 private STATUS_ORDER_CANCELLED = 3;
```

이는 취소 상태에 대한 getter가 이 두 상태를 구별할 수 없음을 의미합니다.

하나의 취소 상태만 남겨두거나 STATUS_ORDER_CANCELLED = 4로 설정하는 것을 고려하십시오.

# [L-02] 운영자(Operator) 역할이 사용되지 않음

주석에 따르면 다음 함수는 `OPERATOR_ROLE`을 확인할 것으로 예상되었습니다:
`forceCancelOrder()` - 현재 onlyOwner 확인
`settle2Step()` - 현재 onlyOwner 확인
`settleCalcelled()` - '구매자 또는 운영자 전용'이지만 현재 구매자와 onlyOwner 확인

결과적으로 운영자 역할은 사용되지 않지만 변수로는 존재합니다.

위의 함수에서 운영자를 확인하거나 역할을 제거하는 것을 고려하십시오.

# [L-03] 전송 수수료(Fee-on-transfer) 토큰이 지원되지 않음

`acceptedTokens`는 모든 토큰을 담보로 받을 수 있습니다. 사실, 모든 토큰을 사용할 수 있는 것은 아닙니다. 예를 들어, 계약이 transferFrom()에 제공된 것보다 항상 적은 잔액을 받게 되므로 fee-on-transfer 및 리베이스 가능 토큰이 갇힐 수 있으며, 이를 분배하는 데 문제가 발생할 수 있습니다.

환불 로직을 설계하지 않은(그리고 ETH를 받을 수 없는) 스마트 계약인 경우와 같이 의도하지 않은 되돌림일 수도 있습니다.

이러한 토큰이 허용되지 않도록 하십시오.

# [L-04] batchFillOffer()가 제공된 동일한 길이를 확인함

`settle2StepsBatch()`에서 수행되는 것과 동일한 방식으로 `batchFillOffer()`에서 `offerId.length == amount.length`를 확인하는 것을 고려하십시오.

# [L-05] pledgeRate 설정이 확인되지 않음

`pledgeRate` 설정은 `updateConfig()`에서 확인 없이 모든 숫자를 허용합니다.
설정에 대한 건전성 확인(sanity checks)을 추가하는 것을 고려하십시오.

# [L-06] 주문 상태를 확인하는 데 잘못된 변수가 사용됨

보시다시피 주문 상태는 오퍼(offer)의 변수와 비교하여 확인됩니다. 현재 그것들은 동일하지만, 실수는 미래에 영향을 미칠 수 있습니다.

```solidity
  // Status
  // Offer status
  uint8 private STATUS_OFFER_OPEN = 1;
  uint8 private STATUS_OFFER_FILLED = 2;
  uint8 private STATUS_OFFER_CANCELLED = 3;

  // Order Status
  uint8 private STATUS_ORDER_OPEN = 1;
  uint8 private STATUS_ORDER_SETTLE_FILLED = 2;
  uint8 private STATUS_ORDER_SETTLE_CANCELLED = 3;
  uint8 private STATUS_ORDER_CANCELLED = 3;

  function forceCancelOrder(uint256 orderId) public nonReentrant onlyOwner {
    MarketStorage storage $ = _getOwnStorage();
    Order storage order = $.orders[orderId];
    Offer storage offer = $.offers[order.offerId];

@>  require(order.status == STATUS_OFFER_OPEN, 'Invalid Order Status');
    ...
  }
```

# [L-07] 잘못된 `offerType`을 가진 오퍼가 판매 오퍼로 취급됨

오퍼 생성 중 `offerType`에 대한 확인이 없습니다. 따라서 사용자가 실수로 잘못된 `offerType`을 임의의 값으로 설정하면 판매 오퍼로 취급됩니다:

```solidity
  uint8 private OFFER_BUY = 1;
  uint8 private OFFER_SELL = 2;

  function newOffer(
    uint8 offerType,
    bytes32 tokenId,
    uint256 amount, //amount of asset
    uint256 value, // amount of collateral
    address exToken,
    uint256 minAmount
  ) external nonReentrant {
    ...

    // transfer offer value (offer buy) or collateral (offer sell)
@>  uint256 _transferAmount = offerType == OFFER_BUY ? value : collateral;
    iexToken.safeTransferFrom(msg.sender, address(this), _transferAmount);

    ...
  }
```

건전성 확인을 추가하는 것이 좋습니다:

```diff
require(token.status == STATUS_TOKEN_ACTIVE, 'Invalid Token');
    require(exToken != address(0) && $.acceptedTokens[exToken], 'Invalid Offer Token');
    require(amount > 0 && value > 0, 'Invalid Amount or Value');
    require(amount >= minAmount,'minamount to be filled cant be greater then amount');
    require(minAmount >= token.minAmount,'minamount should be greater then eual to market global minamount');
+   require(offerType == OFFER_BUY || offerType == OFFER_SELL);
```
