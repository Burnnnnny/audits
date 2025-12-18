# 소개

**IPNFT** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **IPNFT** 소개

IPNFT는 ERC721 NFT 표준을 사용하여 블록체인에서 지적 재산(IP)을 캡처합니다. IPNFT를 ERC20 토큰 공급으로 분할하는 분할 메커니즘을 구현하며, IPNFT의 원래 소유자가 온디맨드로 발행하거나 제한(cap)할 수 있습니다. 또한 이 프로토콜은 일반 크라우드 세일, 베스팅(vesting)이 있는 크라우드 세일, 베스팅 + 스테이킹이 있는 크라우드 세일과 같은 다양한 유형의 크라우드 세일을 구현합니다. 이들은 고정 가격으로 거버넌스 지분(분할된 토큰)을 모금하는 데 사용됩니다.

크라우드 세일은 다음 순서를 따릅니다:

1. 크라우드 세일 구성 및 시작, 경매 토큰(auction tokens) 입금
2. 판매가 마감될 때까지 입찰 토큰(bidding tokens) 입찰 수락
3. 판매 정산(Settling) - 판매 수혜자(beneficiary)에게 자금 회계 및 이체
4. 입찰자를 위한 경매 토큰 청구

## 관찰 사항

`Fractionalizer` 스마트 계약은 비수탁형(non-custodial)입니다. 즉, 주소가 주어진 IPNFT에 대해 `fractionalizeIpnft`를 호출한 다음, 해당 주소가 IPNFT를 전송/판매할 수 있으며 새 소유자도 이에 대해 `fractionalizeIpnft`를 호출할 수 있습니다.

`IPNFT` 계약은 업그레이드 가능하고 일시 중지 가능하며 소유자는 필요에 따라 `mintAuthorizer` 주소를 변경할 수 있습니다. 이는 프로토콜에 약간의 중앙 집중화 위험을 초래합니다.

크라우드 세일 로직의 경우, 18 소수점이 아닌 경매 및 스테이킹 토큰을 제외한 모든 유형의 ERC20이 예상됩니다. 입찰 토큰만 18 소수점이 아닐 수 있습니다.

크라우드 세일 아키텍처는 `StakedVestedCrowdSale`이 `VestedCrowdSale`을 상속하고 `VestedCrowdSale`이 `CrowdSale`을 상속하는 구조입니다. 각 계약을 별도로 배포할 수 있지만 팀은 `StakedVestedCrowdSale` 계약만 직접 배포하고 지원할 것입니다.

크라우드 세일은 초과 판매(oversold)될 수 있으며, 입찰은 자금 목표에 도달할 때까지가 아니라 판매가 마감될 때까지 수락됩니다.

`VestedCrowdSale` 또는 `StakedVestedCrowdSale`에 대해 `closingTime + cliff` 타임스탬프 이전에 경매 토큰을 청구하면 가스 오버헤드가 발생하고 토큰이 `TimelockedToken` 스마트 계약에 잠깁니다.

현재 크라우드 세일의 `auctionToken`이 공급 제한이 없는 `FractionalizedToken`일 수 있으며, 이는 원래 IPNFT 소유자가 원하는 만큼 새로운 분수(fractions)를 발행하여 기존 보유자의 가치를 희석시킬 수 있음을 의미합니다.

# 위협 모델

## 특권 역할 및 행위자

- IPNFT 원래 소유자(original owner) - 토큰 분수를 발행하고 기존 토큰 보유자를 희석시킬 수 있는 주체입니다. 또한 새 분수의 발행을 제한(cap)할 수도 있습니다.
- FractionalizedToken 소유자(owner) - 항상 `Fractionalizer` 계약이며, 새 분수를 발행하거나 공급을 제한할 수 있습니다.
- FractionalizedToken 발행자(issuer) - 분할된 IPNFT의 원래 소유자이며, 새 분수를 발행하거나 공급을 제한할 수 있습니다. `fractionalizeIpnft`를 호출하는 사용자와 동일합니다.
- Fractionalizer 소유자(owner) - 수수료 수신자 주소와 수수료 비율을 설정할 수 있으며 `Fractionalizer` 계약에 대한 업그레이드를 승인할 수도 있습니다.
- IPNFT 소유자(owner) - `IPNFT` 계약을 일시 중지-일시 중지 해제하고, `mintAuthorizer` 주소를 설정하고, 수수료를 인출하거나, 업그레이드를 승인할 수 있습니다.
- 민트 승인자(Mint authorizer) - IPNFT에 대한 예약 및 민트를 승인합니다.
- 크라우드 세일 시작자(CrowdSale starter) - 크라우드 세일을 시작하고 구성할 수 있습니다.
- 크라우드 세일 입찰자(CrowdSale bidder) - `auctionToken`을 얻기 위해 크라우드 세일에 `biddingToken`을 입금할 수 있습니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

**A:** IPNFT의 IP 권리와 크라우드 세일의 `auctionToken`, `biddingToken` 및 `stakingToken`입니다.

**Q:** 프로토콜/사용자가 돈을 잃을 수 있는 경우는 언제인가요?

**A:** `CrowdSale`에서 토큰을 청구할 수 없거나 `IPNFT`가 잘못된 IP를 가지고 있는 경우입니다.

**Q:** 공격자가 목표를 달성할 수 있는 방법에는 어떤 것이 있나요?

**A:** `CrowdSale`에서 자신이 받아야 할 것보다 더 많은 `auctionToken`을 청구하거나 다른 사용자의 청구를 되돌리게(revert) 만드는 것입니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

**영향 (Impact)** - 성공적인 공격으로 인한 기술적, 경제적, 평판적 손해

**가능성 (Likelihood)** - 특정 취약점이 발견되고 악용될 확률

**심각도 (Severity)** - 위험의 전반적인 중요성

# 보안 평가 요약

**_검토 커밋 해시_ - [c99cbf42f10c28ca5912c8f44f1ebf48660826a7](https://github.com/moleculeprotocol/IPNFT/tree/c99cbf42f10c28ca5912c8f44f1ebf48660826a7)**

**_수정 검토 커밋 해시_ - [a56b9302c74d73949b586f50cc1ed17a2f2bff49](https://github.com/moleculeprotocol/IPNFT/tree/a56b9302c74d73949b586f50cc1ed17a2f2bff49)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `crowdsale/CrowdSale`
- `crowdsale/StakedVestedCrowdSale`
- `crowdsale/VestedCrowdSale`
- `Fractionalizer`
- `FractionalizedToken`
- `TimelockedToken`
- `IPNFT`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 1개 문제
- 중간: 3개 문제
- 낮음: 5개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [H-01] | 판매 생성자가 입찰자의 청구 가능한 토큰을 훔칠 수 있음 | 높음 |
| [M-01] | 승인 경쟁 보호 기능이 있거나 `approve`에서 `bool`을 반환하지 않는 경매 토큰은 `VestedCrowdSale`과 호환되지 않음 | 중간 |
| [M-02] | `CrowdSale` 구성의 입력 검증 불충분 | 중간 |
| [M-03] | 전송 수수료(fee-on-transfer) 메커니즘이 있는 토큰은 프로토콜을 손상시킴 | 중간 |
| [L-01] | 토큰 청구 시 올림 대신 내림 사용 | 낮음 |
| [L-02] | `CrowdSale::settle`에서 푸시보다 풀(Pull over push) 패턴 사용 | 낮음 |
| [L-03] | `address payable`의 `send` 메서드 사용은 권장되지 않음 | 낮음 |
| [L-04] | 백분율 설정자(setter)는 범위가 제한되어야 함 | 낮음 |
| [L-05] | `CrowdSale::claim`에서 올바른 상태가 강제되지 않음 | 낮음 |

# 상세 발견 사항

# [H-01] 판매 생성자가 입찰자의 청구 가능한 토큰을 훔칠 수 있음

## 심각도

**영향:**
높음, 입찰자의 자금 손실을 초래하기 때문

**가능성:**
중간, 클리프 만료 전에 청구해야 하기 때문

## 설명

`StakedVestedCrowdSale`에서 판매 생성자는 자신의 `TokenVesting` 및 `TimelockedToken` 계약 주소를 제공할 수 있습니다. `VestedCrowdSale`에서도 마찬가지지만 `TimelockedToken`에 대해서만 가능합니다. 이제 판매 생성자가 악성인 경우 `TokenVesting` 또는 `TimelockedToken`을 상속하지만 자금이 잠겨 있는 동안에도 필요에 따라 자금을 인출할 수 있는 기능을 추가한 자체 배포 계약의 주소를 제공할 수 있습니다. `VestedCrowdSale`의 토큰 잠금 로직과 관련하여 판매 정산 시 `TimelockedToken` 계약이 청구되어야 하는 모든 `auctionToken`을 지출하도록 승인되므로 기능이 있다면 필요에 따라 자금을 빼내 계약 밖으로 이체할 수 있기 때문에 이는 더 나쁩니다. 이로 인해 입찰자는 토큰을 청구할 수 없으며 가치가 100% 손실됩니다.

## 권장 사항

`TokenVesting` 및 `TimelockedToken` 계약이 미리 정의된 바이트코드/구현에서 내부적으로만 배포되도록 강제하고 사용자가 제공한 계약을 수락하지 마십시오.

# [M-01] 승인 경쟁 보호 기능이 있거나 `approve`에서 `bool`을 반환하지 않는 경매 토큰은 `VestedCrowdSale`과 호환되지 않음

## 심각도

**영향:**
높음, 판매 정산이 불가능하기 때문

**가능성:**
낮음, 그러한 토큰은 드물지만 존재하기 때문

## 설명

[USDT](https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7)(경매 토큰은 소수점 18자리여야 하므로 좋은 예는 아님) 및 [KNC](https://etherscan.io/token/0xdd974d5c2e2928dea5f71b9825b8b646686bd200#code)(소수점 18자리)와 같은 일부 토큰에는 승인 경쟁 보호 메커니즘이 있으며 업데이트 시 허용량이 0이거나 `uint256.max`여야 합니다. 문제는 `VestedCrowdSale`에 다음 코드가 존재한다는 것입니다:

```solidity
uint256 currentAllowance = sale.auctionToken.allowance(address(this), address(salesVesting[saleId].vestingContract));

//the poor man's `increaseAllowance`
sale.auctionToken.approve(address(salesVesting[saleId].vestingContract), currentAllowance + sale.salesAmount);
```

이것은 그러한 메커니즘을 가진 토큰에서는 작동하지 않으며 `currentAllowance`가 0이 아닐 때 되돌려집니다(revert).

또 다른 문제는 `approve` 호출 시 `bool`을 반환하지 않는 ERC20 표준을 따르지 않는 토큰(예: `USDT`)이 있다는 것입니다. 이러한 토큰은 프로토콜과 호환되지 않습니다. Solidity가 반환 데이터 크기를 확인하는데 0이 되어 되돌려지기(revert) 때문입니다.

## 권장 사항

토큰이 잠기기 직전에 `VestedCrowdSale::_claimAuctionTokens`에서만 승인을 수행하거나 먼저 0으로 승인하십시오. `SafeERC20`의 `forceApprove`를 사용하십시오.

# [M-02] `CrowdSale` 구성의 입력 검증 불충분

## 심각도

**영향:**
높음, 귀중한 토큰을 거의 영구적으로 잠글 수 있기 때문

**가능성:**
낮음, 팻 핑거(fat-finger) 또는 큰 구성 오류가 필요하기 때문

## 설명

새로운 `CrowdSale`의 구성 검증에 두 가지 결함이 있습니다. 현재 `closingTime` 필드에 최대값 확인이 없으므로 끝나지 않는 `Sale`을 만드는 것이 가능합니다. 또한 `StakedVestedCrowdSale::startSale`처럼 `startSale`의 `cliff` 인수가 검증되지 않으므로 `VestedCrowdSale`에서 끝나지 않는 잠금 또는 0 기간 잠금이 사용될 수 있습니다. 둘 다 토큰이 거의 영구적으로 잠겨 사용자에게 가치 손실을 초래할 수 있습니다.

## 권장 사항

`closingTime` 및 `cliff` 매개변수 모두에 적절한 최소 및 최대 값 범위를 추가하십시오.

# [M-03] 전송 수수료(fee-on-transfer) 메커니즘이 있는 토큰은 프로토콜을 손상시킴

## 심각도

**영향:**
높음, 일부 사용자가 가치를 잃을 것이기 때문

**가능성:**
낮음, 그러한 토큰은 흔하지 않기 때문

## 설명

모든 크라우드 세일 계약 및 `TimelockedToken`의 ERC20 로직은 전송 수수료 메커니즘이 있는 토큰과 호환되지 않습니다. 예를 들어 `PAXG`가 그러한 토큰이며, `USDT`에는 현재 꺼져 있는 내장 전송 수수료 메커니즘이 있습니다. 이에 대한 한 가지 예는 `CrowdSale::startSale`입니다:

```solidity
sale.auctionToken.safeTransferFrom(msg.sender, address(this), sale.salesAmount);
```

토큰에 전송 수수료 메커니즘이 있는 경우 `sale.salesAmount`를 예상 잔액으로 캐시(cache) 하지만 실제로는 `sale.salesAmount - fee` 잔액을 갖게 되므로 잘못 작동합니다. 이로 인해 계약에서 `auctionTokens`를 전송하는 마지막 사람이 되돌려지는(revert) 결과가 발생합니다. `TimelockedToken::lock`과 같이 토큰을 프로토콜로 전송하는 다른 `transferFrom` 호출에도 동일하게 적용됩니다.

## 권장 사항

계약으로 `transferFrom`하기 전에 잔액을 캐시 한 다음 전송 후 확인하고 그 차이를 새로 추가된 잔액으로 사용해야 합니다. ERC777 토큰이 이를 악용할 수 있으므로 `nonReentrant` 수정자도 필요합니다. 또 다른 해결책은 전송 수수료 메커니즘이 있는 토큰을 지원하지 않는다고 문서화하고 공지하는 것입니다.

# [L-01] 토큰 청구 시 올림 대신 내림 사용

현재 `CrowdSale` 계약의 모든 `wad` 수학은 올림을 수행합니다. 일부 사용자가 자신이 받아야 할 것보다 1 wei 더 많은 토큰을 청구하면 최종 사용자가 청구할 때 되돌려질(revert) 수 있으므로 이는 토큰 청구에 좋지 않습니다. 토큰 청구 시 올림 대신 내림을 사용하도록 하십시오.

# [L-02] `CrowdSale::settle`에서 푸시보다 풀(Pull over push) 패턴 사용

`CrowdSale`의 `settle` 메서드는 `sale.beneficiary`에게 자금을 직접 전송합니다. `SaleState.FAILED` 경우 `auctionToken`을, `SaleState.SETTLED` 경우 `biddingToken`을 전송합니다. 이러한 토큰 중 하나에 블랙리스트 메커니즘이 있고 수혜자가 블랙리스트에 오른 경우 판매를 `settle`할 방법이 없으므로 판매의 모든 `auctionToken` 및 `biddingToken`이 갇히게 됩니다. 푸시보다 풀 패턴을 사용하여 이 문제를 해결할 수 있습니다. 예를 들어 `sale.beneficiary`에게 자금을 강제 전송하지 않고 그가 청구해야 하는 메서드를 추가하는 것입니다.

# [L-03] `address payable`의 `send` 메서드 사용은 권장되지 않음

피호출자가 2300 가스 이상을 소비하는 `receive` 또는 `fallback` 함수를 가지고 있는 경우 `send` 메서드가 되돌려지므로 `address payable`의 `send` 메서드는 더 이상 사용되지 않습니다(deprecated). 네이티브 자산 수신 시 2300 가스 이상을 소비하는 스마트 지갑이나 다중 서명 지갑이 많으므로 대신 `call`을 값과 함께 사용하는 것이 좋습니다.

```diff
-require(payable(_msgSender()).send(address(this).balance), "transfer failed");
+msgSender().call{value: address(this).balance}("");
```

# [L-04] 백분율 설정자(setter)는 범위가 제한되어야 함

`Fractionalizer::setReceiverPercentage` 메서드는 경계가 없는 백분율 값을 설정합니다. 즉, 100% 이상일 수 있습니다. 이 기능은 아직 사용되지 않지만 위험해 보입니다. 사용될 때까지(`setFeeReceiver`와 함께) 제거하거나 `MAX_VALUE` 상수를 추가하여 가능한 백분율 값을 제한하는 것이 좋습니다.

# [L-05] `CrowdSale::claim`에서 올바른 상태가 강제되지 않음

`CrowdSale`의 `claim` 메서드는 판매가 여전히 `RUNNING` 상태이면 되돌려지고(revert) `FAILED` 상태이면 `claimFailed` 경로를 통과합니다. 문제는 첫 번째 확인 후 상태가 `SETTLED`가 아닌 경우 되돌리기(revert)를 강제하지 않는다는 것입니다. 현재는 문제가 없지만 미래에 `FAILED`도 `SETTLED`도 아닌 판매에 대해 청구하는 것과 같은 미묘한 버그로 이어질 수 있습니다.
