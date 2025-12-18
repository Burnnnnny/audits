# 소개

**pashov**가 **Rolling Dutch Auction** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **Rolling Dutch Auction** 소개

Rolling Dutch Auction 프로토콜은 복합 감쇠(composite decay)가 있는 더치 경매 파생물입니다. 이는 경매가 높은 호가로 시작하여 시간이 지남에 따라 점차 감소함을 의미합니다. 이 프로토콜은 다른 ERC20 토큰으로 입찰할 수 있는 ERC20 토큰을 경매하는 방식으로 작동합니다. 경매와 입찰 구현 모두 수탁형(custodial)이며, 이는 프로토콜이 ERC20 자금을 스스로 전송하고 보유함을 의미합니다.

경매 방식은 현재 가격이 동결되는 기간인 '창(window)'을 갖는 것입니다. 창이 활성 상태인 동안 입찰이 제출되면 창의 지속 시간이 다시 시작됩니다. 즉, 영구적 지속 시간 메커니즘을 구현합니다. 창이 만료되면 현재 판매 가격은 경매 시작 시간이 아닌 마지막 창 이후 경과된 시간을 계산하여 참조됩니다.

## 예상치 못한/흥미로운 설계 선택

`withdraw` 기능은 누구나 호출할 수 있으며, 수익금과 남은 준비금을 `operatorAddress`로 전송합니다. `redeem`도 마찬가지로 누구나 호출할 수 있으며 환불 및 청구 금액을 `bidder` 인수로 전송합니다.

다른 입찰자보다 높은 입찰을 하려면 이전 최고 입찰보다 높은 `price`와 높은 `volume`을 모두 가진 입찰을 해야 합니다.

`scalarPrice` 메서드와 `commitBid`의 `price` 매개변수 모두 `reserve` 토큰 측면에서 가치가 평가됩니다.

# 위협 모델

## 역할 및 행위자

- Auction creator - `createAuction`을 호출하고 초기 준비금을 `RDA` 계약에 로드하는 계정
- Auction operator - `withdraw`가 호출될 때 수익금과 남은 준비금을 받을 계정
- Bidder - `commitBid` 기능을 통해 입찰을 제출하여 자신의 `purchaseToken` 자금을 `RDA` 계약으로 이동시키는 계정
- Unauthorized user - 가스비만 지불하면 누구나 `fulfillWindow`, `withdraw` 및 `redeem`을 호출할 수 있습니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇입니까?

**A:** 프로토콜은 수탁형이므로 보유하고 있는 구매 및 준비금 토큰 금액이 가치가 있습니다.

**Q:** 프로토콜에 일어날 수 있는 최악의 상황은 무엇입니까?

**A:** 보유한 자금이 영원히 갇히거나 사용자가 훔쳐 다른 사용자의 상환/인출이 되돌아가게(revert) 만드는 것입니다.

**Q:** 어떤 경우에 프로토콜/사용자가 돈을 잃을 수 있습니까?

**A:** 사용자가 입찰한 것보다 적은(또는 전혀 없는) 준비금 토큰을 받는 경우입니다. 프로토콜 운영자의 경우도 마찬가지로, 받아야 할 것보다 적은(또는 전혀 없는) 구매 토큰을 받는 경우입니다.

## 잠재적 공격자의 목표

- 프로토콜의 모든 메서드를 DoS 상태로 만듦
- 다른 사용자의 청구 가능한 준비금 토큰 훔치기
- 가격 계산의 버그 악용

## 공격자가 목표를 달성하는 잠재적 방법

- `require` 문이나 외부 호출을 강제로 실패하게 하여 `createAuction`, `commitBid`, `redeem` 및 `withdraw` 호출을 되돌림(revert)
- 개인적인 이익을 위해 가격 계산에서 나눗셈의 오류나 버림(rounding down)을 악용
- 경매가 완료되지 않도록 강제하여 아무도 토큰을 받지 못하게 함

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

**영향(Impact)** - 성공적인 공격의 기술적, 경제적, 평판적 손해
**가능성(Likelihood)** - 특정 취약점이 발견되고 악용될 확률
**심각도(Severity)** - 위험의 전반적인 중요도

# 보안 평가 요약

**_검토 커밋 해시_ - [cb27022597db95c4fd734356491bc0304e1e0721](https://github.com/deomaius/rolling-dutch-auction/tree/cb27022597db95c4fd734356491bc0304e1e0721)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `RDA`
- `interfaces/IRDA`

다음과 같은 수의 문제가 발견되었으며 심각도별로 분류되었습니다:

- 치명적 & 높음: 4개 문제
- 중간: 4개 문제
- 낮음: 3개 문제
- 정보성: 6개 문제

---

# 결과 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | 창이 만료된 후 누구나 새 입찰을 항상 되돌리게(revert) 만들 수 있음 | 치명적 |
| [C-02] | 성공적인 입찰자는 나눗셈 반올림으로 인해 상당한 가치를 잃을 수 있음 | 치명적 |
| [C-03] | `elapsedTime`의 로직에 결함이 있음 | 치명적 |
| [H-01] | `purchaseToken`이 낮은 소수점 토큰인 경우 사용자가 입찰을 잃을 가능성이 높음 | 높음 |
| [M-01] | `createAuction` 함수 매개변수에 대한 입력 검증 누락으로 가치 손실이 발생할 수 있음 | 중간 |
| [M-02] | `scalarPrice` 함수의 정밀도 손실 | 중간 |
| [M-03] | `transfer` 실패 시 되돌리지 않는 토큰과 프로토콜이 올바르게 작동하지 않음 | 중간 |
| [M-04] | 수수료 부과(fee-on-transfer) 및 리베이스(rebasing) 토큰과 함께 경매가 올바르게 작동하지 않음 | 중간 |
| [L-01] | `price == 0`인 경매가 다시 생성될 수 있음 | 낮음 |
| [L-02] | `commitBid` 메서드가 Checks-Effects-Interactions 패턴을 따르지 않음 | 낮음 |
| [L-03] | `scalarPrice` 메서드에 `activeAuction` 수정자가 있어야 함 | 낮음 |
| [I-01] | 오류 문자열 없이 `require` 문 사용 | 정보성 |
| [I-02] | 코드의 오타 | 정보성 |
| [I-03] | 라이선스 식별자 누락 | 정보성 |
| [I-04] | 함수 상태 가변성을 view로 제한할 수 있음 | 정보성 |
| [I-05] | 불완전한 NatSpec 문서 | 정보성 |
| [I-06] | `override` 키워드 누락 | 정보성 |

# 상세 결과

# [C-01] 창이 만료된 후 누구나 새 입찰을 항상 되돌리게(revert) 만들 수 있음

## 심각도

**영향:**
높음, 경매가 끝날 때까지 모든 새 입찰이 되돌려짐

**가능성:**
높음, 누구나 희귀한 전제 조건 없이 공격을 실행할 수 있음

## 설명

`fulfillWindow` 메서드는 내부적으로도 호출되는 `public` 메서드입니다. 이는 `window.processed`를 `true`로 설정하여 단일 `windowId`에 대해 한 번만 호출할 수 있도록 합니다. 문제는 `commitBid` 함수에 다음과 같은 로직이 있다는 것입니다:

```solidity
if (hasExpired) {
    window = _window[auctionId][windowExpiration(auctionId)];
}
```

여기서 `windowExpiration`은 내부적으로 최신 `windowId`로 `fulfillWindow`를 호출합니다. 만약 어떤 사용자가 외부적으로 `fulfillWindow`를 먼저 호출하면 `window.processed`가 `true`로 설정되어 `fulfillWindow`의 다음 확인이:

```solidity
if (window.processed) {
    revert WindowFulfilled();
}
```

이제부터 모든 `commitBid` 호출에서 되돌아가게(revert) 됩니다. 이로 인해 누구도 더 이상 입찰할 수 없게 되어, 경매 기간이 끝날 때까지 경매는 더 이상 아무것도 판매하지 않게 됩니다.

## 권장 사항

`fulfillWindow`를 `internal`로 만들고 내부적으로 호출하지만 `inactiveAuction` 수정자도 있는 새로운 공개 메서드를 추가하십시오. 이렇게 하면 아무도 `commitBid`를 호출할 수 없더라도 경매가 완료되었을 때 누구나 창을 완료할 수 있습니다.

# [C-02] 성공적인 입찰자는 나눗셈 반올림으로 인해 상당한 가치를 잃을 수 있음

## 심각도

**영향:**
높음, 아마도 상당한 가치가 손실될 수 있음

**가능성:**
높음, 대부분의 입찰에서 발생할 것임

## 설명

`fulfillWindow` 메서드는 창에서 성공적인 입찰 후 경매 준비금과 수익금을 계산합니다. 다음은 `auctions` 및 `claims` 저장소 매핑에서 이를 계산하는 방법입니다:

```solidity
_auctions[auctionId].reserves -= volume / price;
_auctions[auctionId].proceeds += volume;

_claims[bidder][auctionId] = abi.encode(refund - volume, claim + (volume / price));
```

문제는 `volume / price` 나눗셈과 Solidity의 작동 방식에 있습니다. 정수만 있기 때문에 나눗셈에서 결과는 항상 버림(round down) 됩니다. 이는 입찰자가 예상보다 적은 `claim` 토큰을 갖게 되는 반면, `_auctions[auctionId].reserves`는 가져야 할 것보다 더 많은 토큰을 유지하게 됨을 의미합니다. 다음 시나리오를 살펴보겠습니다:

1. `reserve` 토큰은 `WETH`(18 소수점)이고 구매 토큰은 `DAI` - 18 소수점입니다.
2. 창의 최고 입찰자가 1e18 `WETH` 가격으로 2 \* 1e18 - 1 `DAI`를 입찰했습니다.
3. 입찰로 1.99999 `WETH`를 구매했어야 하지만, 사용자는 1 `WETH`만 받고 여기서 환불을 받지 못합니다.
4. 사용자는 1e18을 입찰한 것과 같은 양의 `WETH`를 받았지만 두 배만큼 입찰했습니다(마이너스 1).

`volume / price` 나눗셈의 모든 나머지는 입찰자에게 손실을 초래합니다.

## 권장 사항

`volume / price` 나눗셈의 나머지가 입찰자에게 환불되도록 코드를 설계하십시오. 예를 들어 `refund` 값에 추가하십시오.

# [C-03] `elapsedTime`의 로직에 결함이 있음

## 심각도

**영향:**
높음, 메서드가 경매 가격을 계산하는 데 사용되지만 잘못된 결과를 제공할 것임

**가능성:**
높음, 문제는 경매 중 거의 항상 존재함

## 설명

`elapsedTime` 메서드에는 여러 결함이 있습니다:

1. 창이 0개인 경우 `windowIndex` 변수(창 카운트에 사용됨)가 1이 되는데, 이는 잘못된 것이며 `windowElapsedTime`이 0이어야 할 때 큰 값을 초래합니다.
2. `auctionElapsedTime == windowElapsedTime`인 경우 결과로 `auctionElapsedTime`을 얻지만, `auctionElapsedTime`에 1초만 더 있어도 `auctionElapsedTime - windowElapsedTime`을 얻게 되어 결과가 1이 되므로 완전히 다른 결과가 나옵니다.
3. 창이 활성화된 경우 `timestamp` 인수는 `auction.startTimestamp`와 동일한 값을 가지므로 이 경우 `auctionElapsedTime`은 항상 0이 됩니다.

이 메서드는 여러 결함이 있으며 행복한 경우(happy-case) 시나리오에서만 작동합니다.

## 권장 사항

메서드를 완전히 제거하거나 `timestamp` 매개변수를 제거하여 로직을 단순화하기 위해 두 개의 메서드로 추출하십시오. 또한 엣지 케이스 시나리오에 대해 생각하십시오.

# [H-01] `purchaseToken`이 낮은 소수점 토큰인 경우 사용자가 입찰을 잃을 가능성이 높음

## 심각도

**영향:**
높음, 사용자가 전체 입찰 금액을 잃을 수 있기 때문

**가능성:**
중간, `purchaseToken`이 낮은 소수점 토큰일 때 발생하지만, 이는 일반적으로 사용됨

## 설명

사용자가 `commitBid`를 호출할 때 입찰할 `purchaseToken`의 양인 `volume` 매개변수와 `reserveToken` 단위의 가격인 `price` 매개변수를 제공합니다. 그의 입찰은 캐시되고 창이 만료되면 `fulfillWindow` 메서드가 호출되며, 여기서 다음과 같은 로직이 있습니다:

```solidity
_auctions[auctionId].reserves -= volume / price;
_auctions[auctionId].proceeds += volume;

_claims[bidder][auctionId] = abi.encode(refund - volume, claim + (volume / price));
```

문제는 `volume / price` 계산에 있습니다. `reserveToken`이 18 소수점 토큰(가장 일반적임)이지만 `purchaseToken`의 소수점 수가 낮은 경우 - `USDC`, `USDT` 및 `WBTC`는 6~8 소수점을 가짐 - `volume / price` 계산 결과가 0으로 버림될 가능성이 매우 높습니다. 이는 경매 소유자가 여전히 전체 입찰 금액을 받지만, 입찰자는 청구할 `reserveToken`을 0개 얻게 되어 입찰의 전체 손실을 초래한다는 것을 의미합니다.

이 문제는 `reserve` 및 `purchase` 토큰에 대해 동일한 소수점 토큰을 사용하지만 입찰의 `volume`이 `price`보다 작은 경우에도 존재합니다. 다시 말하지만, 나눗셈은 0으로 버림 되어 입찰자에게 100% 손실을 초래합니다.

## 권장 사항

`commitBid`에서 `volume >= price`를 강제하고 `createAuction`에서 `reserveToken` 소수점이 `purchaseToken` 소수점과 동일하도록 강제하십시오.

# [M-01] `createAuction` 함수 매개변수에 대한 입력 검증 누락으로 가치 손실이 발생할 수 있음

## 심각도

**영향:**
높음, 자금 동결로 이어질 수 있음

**가능성:**
낮음, 사용자 오류/구성 오류가 필요함

## 설명

`createAuction`의 입력 검증에 몇 가지 문제가 있으며, 특히 타임스탬프 값과 관련이 있습니다.

1. `endTimestamp`가 `startTimestamp`와 같을 수 있으므로 `duration`이 0이 될 수 있습니다.
2. `endTimestamp`가 `startTimestamp`보다 훨씬 미래일 수 있으므로 `duration`이 거대한 숫자가 되고 경매가 절대 끝나지 않을 수 있습니다.
3. `startTimestamp`와 `endTimestamp` 모두 훨씬 미래일 수 있으므로 경매가 절대 시작되지 않을 수 있습니다.

이러한 가능성은 모두 완화되어야 하며, 초기 준비금 및/또는 입찰이 프로토콜에 영원히 갇힐 수 있습니다.

## 권장 사항

최소 `duration` 값(예: 1일)과 최대 값(예: 20일)을 사용하십시오. 경매가 생성된 후 X일 이상 지나서 시작되지 않도록 하십시오.

# [M-02] `scalarPrice` 함수의 정밀도 손실

## 심각도

**영향:**
중간, 가격이 예상 가격과 크게 다르지 않을 것이기 때문

**가능성:**
중간, 항상 큰 정밀도 손실을 초래하지는 않기 때문

## 설명

`scalarPrice`에 다음 코드가 있습니다:

```solidity
uint256 b_18 = 1e18;
uint256 t_mod = t % (t_r - t);
uint256 x = (t + t_mod) * b_18 / t_r;
uint256 y = !isInitialised ? state.price : window.price;

return y - (y * x) / b_18;
```

여기서 `x`를 계산할 때 나중에 `x`에 `y`를 곱하더라도 `t_r`로 나눕니다. 정밀도 손실을 최소화하려면 항상 나눗셈보다 곱셈을 먼저 수행해야 합니다. Solidity는 나눗셈 연산에 나머지가 있을 때 그냥 버림(round down)을 하기 때문입니다.

## 권장 사항

Solidity에서는 항상 나눗셈보다 곱셈을 먼저 수행하고, 전체 `scalarPrice` 메서드에서 이를 따르도록 하십시오.

# [M-03] `transfer` 실패 시 되돌리지 않는 토큰과 프로토콜이 올바르게 작동하지 않음

## 심각도

**영향:**
높음, 가치 손실로 이어질 수 있음

**가능성:**
낮음, 이러한 토큰은 흔하지 않음

## 설명

일부 토큰은 `transfer` 또는 `transferFrom` 실패 시 되돌리지(revert) 않고 대신 `false`를 반환합니다(예: [ZRX](https://etherscan.io/address/0xe41d2489571d322189246dafa5ebde1f4699f498#code)). 이러한 토큰은 기술적으로 표준을 준수하지만 `transfer`/`transferFrom` 호출의 반환 값을 확인하는 것을 잊는 것은 흔한 문제입니다. 현재 코드로 인해 이러한 호출이 실패했지만 되돌아가지 않으면 부정확한 계산이나 자금이 프로토콜에 갇히는 결과를 초래할 것입니다.

## 권장 사항

OpenZeppelin의 `SafeERC20` 라이브러리와 ERC20 전송을 위한 `safe` 메서드를 사용하십시오.

# [M-04] 수수료 부과(fee-on-transfer) 및 리베이스(rebasing) 토큰과 함께 경매가 올바르게 작동하지 않음

## 심각도

**영향:**
높음, 가치 손실로 이어질 수 있음

**가능성:**
낮음, 이러한 토큰은 흔하지 않음

## 설명

`createAuction`의 코드는 다음을 수행합니다:

```solidity
IERC20(reserveToken).transferFrom(msg.sender, address(this), reserveAmount);
...
...
state.reserves = reserveAmount;
```

따라서 기본적으로 예상 전송 금액을 캐시합니다. `reserveToken`에 수수료 부과(fee-on-transfer) 메커니즘이 있는 경우 수수료 때문에 실제 수신 금액이 적으므로 이 작업은 작동하지 않습니다. 또한 토큰이 리베이스(rebasing) 메커니즘을 사용하는 경우 문제가 됩니다. 이는 계약이 경매를 위해 `state.reserves`에 캐시한 것보다 적은 잔액을 보유하거나 더 많이 보유하게 되어 프로토콜에 갇힐 수 있음을 의미할 수 있습니다.

## 권장 사항

수수료 부과 또는 리베이스 메커니즘이 있는 토큰을 지원하지 않는다고 명시적으로 문서화하거나 다음을 수행할 수 있습니다:

1. 수수료 부과 토큰의 경우 전송 전후의 잔액을 확인하고 그 차이를 실제 수신 금액으로 사용하십시오.
2. 리베이스 토큰의 경우 가치가 내려갈 때 보유 잔액을 기준으로 캐시된 `reserves`를 그에 따라 업데이트하는 메서드가 있어야 합니다. 이것은 복잡한 솔루션입니다.
3. 리베이스 토큰의 경우 가치가 올라갈 때 초과 토큰을 프로토콜 밖으로 실제로 전송하는 메서드를 추가해야 합니다.

# [L-01] `price == 0`인 경매가 다시 생성될 수 있음

`createAuction` 메서드는 다음 코드로 경매가 존재하는지 확인합니다:

```solidity
Auction storage state = _auctions[auctionId];

if (state.price != 0) {
    revert AuctionExists();
}
```

그러나 이 메서드는 `startingOriginPrice` 인수의 값이 0인지 확인하지 않습니다. 만약 그렇다면 `state.price`는 다음 `createAuction` 호출에서 0이 될 것입니다. 이것이 일어날 것으로 예상되지는 않지만, 만약 그렇다면 다음 코드 라인이 두 번 실행될 수 있습니다:

```solidity
IERC20(reserveToken).transferFrom(msg.sender, address(this), reserveAmount);
```

이는 호출자에게 손실을 초래할 것입니다. `startingOriginPrice`의 값이 0이 아니어야 함을 요구(require)하도록 하십시오.

# [L-02] `commitBid` 메서드가 Checks-Effects-Interactions 패턴을 따르지 않음

가치 전송을 수행하는 메서드에서 CEI 패턴을 따르는 것이 모범 사례입니다. 여전히 항상 최상의 솔루션은 아닌데, ERC777 토큰은 CEI를 따르더라도 계약이 이상한 상태일 때 재진입(reenter)할 수 있기 때문입니다. `commitBid`에 `nonReentrant` 수정자를 추가하고 `transferFrom` 호출을 메서드 끝으로 이동하는 것을 권장합니다.

# [L-03] `scalarPrice` 메서드에 `activeAuction` 수정자가 있어야 함

경매가 비활성 상태이면 `scalarPrice` 메서드는 경매가 끝났으므로 반환해서는 안 되지만 여전히 가격을 반환합니다. `activeAuction` 수정자를 추가하십시오.

# [I-01] 오류 문자열 없이 `require` 문 사용

`activeAuction` 및 `inactiveAuction` 수정자는 오류 문자열 없이 `require` 문을 사용합니다. 더 나은 오류 케이스 UX를 위해 사용자 정의 오류와 함께 `if` 문을 대신 사용하십시오.

# [I-02] 코드의 오타

코드의 모든 오타를 수정하십시오:

`Ancoded` -> `Encoded`

`exipration` -> `expiration`

`fuflfillment` -> `fulfillment`

`operatorAdress` -> `operatorAddress`

`Uinx` -> `Unix`

`multipled` -> `multiplied`

# [I-03] 라이선스 식별자 누락

`RDA.sol` 파일의 첫 번째 줄에 라이선스 식별자가 누락되어 컴파일러 경고가 발생합니다. 컴파일러 경고를 제거하려면 최소한 `No License` 라이선스를 추가하십시오.

# [I-04] 함수 상태 가변성을 view로 제한할 수 있음

`scalarPriceUint` 메서드는 상태를 변경하지 않지만 `view`로 표시되지 않았습니다. 함수 서명에 `view` 키워드를 추가하십시오.

# [I-05] 불완전한 NatSpec 문서

메서드에 불완전한 NatSpec 문서가 있습니다. 예를 들어 `elapsedTime` 메서드는 NatSpec에 `@param timestamp`가 누락되어 있으며, `balancesOf` 및 `createAuction`과 같은 대부분의 메서드는 `@return` 매개변수가 누락되었습니다. 각 공개 메서드에 대해 완전하고 상세한 NatSpec 문서를 작성하십시오.

# [I-06] `override` 키워드 누락

`createAuction`, `withdraw` 및 `redeem` 메서드는 `IRDA` 인터페이스의 오버라이드 메서드임에도 불구하고 `override` 키워드가 누락되었습니다. 언급된 메서드에 추가하십시오.

