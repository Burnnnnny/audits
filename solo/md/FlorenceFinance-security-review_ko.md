# 소개

**Florence Finance** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Florence Finance** 소개

Florence Finance는 사용자가 암호화 자산으로 실제 대출에 자금을 지원할 수 있는 프로토콜입니다. 사용자가 스테이블코인을 프로토콜에 입금하면 영수증 토큰($FLR 또는 Loan Vault 토큰)을 받는 방식으로 작동합니다. 그런 다음 프로토콜은 실제 대출 플랫폼에 명목 화폐(유로)를 제공하고, 대출 플랫폼은 다시 기업에 명목 화폐를 대출합니다. 대출이 상환되면 이자와 원금이 프로토콜로 다시 유입되어 사용자에게 분배됩니다. 이 프로토콜은 유동성을 보장하지 않으며 P2P 대출과 유사합니다.

Florence Finance 사용자는 두 가지 방법으로 플랫폼을 사용할 수 있습니다:

1. 실제 대출에서 수익을 얻기 위해 Loan Vault에서 스테이블코인을 사용합니다.
2. $FLR을 스테이킹하고 Medici 토큰($MDC)을 얻습니다.

두 가지 방법 모두 사용자는 $FLR을 가지고 있어야 하는데, 이는 DEX에서 스테이블코인을 $FLR로 스왑하여 얻을 수 있으며, $FLR은 실제 대출의 원금에 의해 1:1로 EUR에 의해 뒷받침됩니다. 그런 다음 Loan Vault에 입금하고 Loan Vault 토큰(볼트는 ERC4626을 따름)을 받아 즉시 이자를 받기 시작할 수 있으며, 나중에 Loan Vault 토큰을 소각하여 $FLR을 인출하고 원금과 획득한 수익을 받을 수 있습니다. 사용자가 대신 $MDC 보상을 받기로 선택하면 스테이킹 풀에 $FLR을 스테이킹한 다음 언제든지 인출하거나 보상을 청구할 수 있습니다.

더 많은 [문서](https://docs.florence.finance/)

## 관찰 사항

이 프로토콜은 ERC20 구현의 모범 사례를 따르지 않는 [EURS](https://etherscan.io/token/0xdb25f211ab05b1c97d595516f45794528a807ad8#code)와 함께 작동하도록 통합되었습니다.

프로토콜의 Loan Vault는 오류가 발생하기 쉬운 ERC4646 표준을 사용하고 있습니다. 또한 프로토콜의 주요 자금 조달을 위한 메커니즘이 있어 자금 제공자는 화이트리스트에 있어야 합니다(KYC 및 AML 관련 문서를 제공해야 함).

FLR은 온체인에서 1:1로 EUR에 페깅 되는 것이 보장되지 않습니다. 이는 실제 대출에 의해 뒷받침됩니다.

Vault의 펀딩 토큰은 화이트리스트에 있어야 합니다. 자금 제공자도 화이트리스트에 있어야 합니다.

# 위협 모델

## 특권 역할

- 대리인(Delegate) - 새 자금 요청(Funding Request)을 생성한 다음 대출을 관리할 수 있습니다(상환, 감액, 증액, 채무 불이행).
- 기본(화이트리스트) 자금 제공자(Primary Funder) - Vault에서 자금 요청을 이행하도록 요청하기 위해 자금 시도(Funding Attempt)를 생성할 수 있습니다.
- 자금 승인자(Fund Approver) - 자금 시도를 승인하고 동시에 실행할 수 있습니다.
- Vault 소유자(Vault Owner) - 볼트를 배포했으며 다른 모든 역할을 설정하고 볼트를 구성할 수 있습니다.
- Florin Treasury 소유자(Florin Treasury Owner) - 재무부 계약을 일시 중지/일시 중지 해제하고 $FLR 토큰 소유권을 이전할 수도 있습니다.
- Loan Vault Registry 소유자(Loan Vault Registry Owner) - 레지스트리에서 Loan Vault를 화이트리스트에 올릴 수 있습니다.

## 행위자

- Chainlink 가격 피드 - 온체인 자산 가격을 제공합니다.
- 스테이커(Staker) - $MDC 보상을 받기 위해 $FLR을 스테이킹합니다.
- Vault 예금자(Vault depositor) - 펀딩 토큰을 대출로 예치하고 볼트 지분을 받습니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

**A:** $FLR 토큰, `LoanVault` 지분 및 재무부의 `EURS` 잔액입니다.

**Q:** 프로토콜/사용자가 돈을 잃을 수 있는 경우는 언제인가요?

**A:** 대출이 채무 불이행 되거나 예금/스테이크를 상환할 수 없는 경우입니다.

**Q:** 공격자가 목표를 달성할 수 있는 방법에는 어떤 것이 있나요?

**A:** 종료 메서드를 강제로 되돌리게(revert) 하거나 스테이킹/볼트 지분 계산 수학을 악용하여 프로토콜/사용자로부터 토큰을 훔치는 것입니다.

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

**_검토 커밋 해시_ - [ef3f96a7c1c5948b1da20af3e1c161c029711484](https://github.com/florence-finance/florence-finance-protocol-v3/tree/ef3f96a7c1c5948b1da20af3e1c161c029711484)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Errors`
- `FlorinStaking`
- `FlorinToken`
- `FlorinTreasury`
- `LoanVault`
- `LoanVaultRegistry`
- `Util`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 2개 문제
- 중간: 6개 문제
- 낮음: 3개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | 사용자가 `FlorinTreasury`에 지출을 허용한 `EURS` 토큰을 누구나 옮길 수 있음 | 치명적 |
| [H-01] | 스테이커/볼트 예금자는 프런트 러닝을 당해 자금을 잃을 수 있음 | 높음 |
| [M-01] | Chainlink 가격 피드의 입력이 제대로 검증되지 않음 | 중간 |
| [M-02] | ERC4626 표준을 올바르게 따르지 않음 | 중간 |
| [M-03] | 사용자 종료/청구 메서드에는 `whenNotPaused` 수정자가 없어야 함 | 중간 |
| [M-04] | `apr` 및 `fundingFee` 백분율 값은 제한되지 않음 | 중간 |
| [M-05] | 프로토콜은 `_msgSender()`를 광범위하게 사용하지만 모든 곳에서 사용하지는 않음 | 중간 |
| [M-06] | 프로토콜에 여러 중앙 집중화 공격 벡터가 존재함 | 중간 |
| [L-01] | 너무 많은 펀딩 토큰이 화이트리스트에 있으면 제거가 불가능할 수 있음 | 낮음 |
| [L-02] | 등록된 대출 볼트의 수를 제한해야 함 | 낮음 |
| [L-03] | 허용량(Allowance) 확인은 잘못된 보안 감각을 줌 | 낮음 |

# 상세 발견 사항

# [C-01] 사용자가 `FlorinTreasury`에 지출을 허용한 `EURS` 토큰을 누구나 옮길 수 있음

## 심각도

**영향:**
높음, 사용자의 지갑에서 자금이 본의 아니게 이동될 것이기 때문

**가능성:**
높음, 전제 조건이 필요하지 않으며 일반적인 공격 벡터이기 때문

## 설명

`FlorinTreasury`의 `depositEUR` 메서드는 다음과 같습니다:

```solidity
function depositEUR(address from, uint256 eurTokens) external whenNotPaused {
    eurTokens = Util.convertDecimals(eurTokens, 18, Util.getERC20Decimals(eurToken));
    SafeERC20Upgradeable.safeTransferFrom(florinToken, from, address(this), eurTokens);
    emit DepositEUR(_msgSender(), from, eurTokens);
}
```

문제는 `from` 인수가 사용자 제어형이라서, 누구나 누가 `FlorinTreasury` 계약이 토큰을 지출하도록 허용했는지 확인한 다음 해당 주소를 메서드의 `from` 인수로 전달할 수 있다는 것입니다. 이렇게 하면 사용자가 직접 수행하지 않았음에도 불구하고 악용된 사용자에서 `FlorinTreasury` 계약으로 `eurTokens` 양만큼의 `EURS` 토큰이 이동합니다. `depositEUR` 메서드는 `LoanVault::repayLoan` 또는 `LoanVault::depositRewards`에 의해 호출될 것으로 예상되며, 여기서 사용자는 먼저 `FlorinTreasury` 계약이 자신의 `EURS` 토큰을 지출하도록 승인해야 합니다. 사용자가 계약의 허용량을 `type(uint256).max`로 설정한 경우 특히 문제가 되는데, 이 경우 그의 `EURS` 잔액 전체가 유출될 수 있기 때문입니다.

## 권장 사항

사용자가 제공한 `from` 인수 대신 `msg.sender`를 사용하여 호출자의 계정에서만 토큰을 이동할 수 있도록 하십시오.

## 논의

**pashov:** 수정됨.

# [H-01] 스테이커/볼트 예금자는 프런트 러닝을 당해 자금을 잃을 수 있음

## 심각도

**영향:**
높음, 사용자 자산의 도난을 초래하기 때문

**가능성:**
중간, 공격자가 첫 번째 스테이커인 경우에만 작동하기 때문

## 설명

다음 예를 살펴보겠습니다:

1. `FlorinStaking` 계약이 배포되고 일시 중지가 해제되었으며 0개의 스테이킹된 $FLR이 있습니다.
2. 앨리스는 `florinTokens == 10e18`로 `stake` 메서드를 호출하는 트랜잭션을 보냅니다.
3. 밥은 악의적인 사용자/봇이며 멤풀에서 트랜잭션을 보고 1 wei의 $FLR을 입금하고 1 wei의 지분을 받아 프런트 러닝합니다.
4. 밥은 또한 10e18 $FLR을 `FlorinStaking` 계약으로 직접 `ERC20::transfer` 하여 앨리스의 트랜잭션을 프런트 러닝합니다.
5. 이제 앨리스의 트랜잭션에서 코드는 `shares = florinTokens.mulDiv(totalShares_, getTotalStakedFlorinTokens(), MathUpgradeable.Rounding.Down);`으로 앨리스의 지분을 계산합니다. 여기서 `getTotalStakedFlorinTokens`는 `florinToken.balanceOf(address(this))`를 반환하므로 이제 `shares`는 0으로 내림됩니다.
6. 앨리스는 10e18 상당의 $FLR을 예치했지만 0 지분을 민팅받습니다.
7. 이제 밥은 `requestedFlorinTokens`가 계약의 $FLR 잔액인 `unstake` 호출로 앨리스의 트랜잭션을 백 러닝(back-runs)하여 자신의 1 지분을 소각하고 자신의 예금 + 앨리스의 전체 예금을 인출할 수 있게 합니다.

이것은 예금자가 문제를 알아차릴 때까지 여러 번 반복될 수 있습니다.

**참고:** 이 절대적으로 동일한 문제는 `LoanVault`의 ERC4626 로직에도 존재합니다. 이는 볼트 지분 계산과 관련된 일반적인 취약점이기 때문입니다. OpenZeppelin은 이 프로토콜에서 사용하는 버전인 4.8.0 버전에서 완화 방법을 도입했습니다.

## 권장 사항

UniswapV2는 두 가지 유형의 보호로 이를 해결했습니다:

[첫째](https://github.com/Uniswap/v2-core/blob/master/contracts/UniswapV2Pair.sol#L119-L121), 첫 번째 `mint`에서 실제로 처음 1000개의 지분을 0 주소(zero-address)로 민팅합니다.

[둘째](https://github.com/Uniswap/v2-core/blob/ee547b17853e71ed4e0101ccfd52e70d5acded58/contracts/UniswapV2Pair.sol#L125), 민팅된 지분이 0이 아니어야 합니다.

두 가지를 모두 구현하면 이 취약점이 해결됩니다.

## 논의

**pashov:** 수정됨.

# [M-01] Chainlink 가격 피드의 입력이 제대로 검증되지 않음

## 심각도

**영향:**
높음, 애플리케이션이 잘못된 자산 가격으로 작동할 수 있기 때문

**가능성:**
낮음, Chainlink 오라클은 대부분 신뢰할 수 있지만 이전에 이 문제의 발생 사례가 있었기 때문

## 설명

`LoanVault::getFundingTokenExchangeRate`의 코드는 다음과 같은 방식으로 Chainlink 가격 오라클을 사용합니다:

```solidity
(, int256 exchangeRate, , , ) = fundingTokenChainLinkFeeds[fundingToken].latestRoundData();

if (exchangeRate == 0) {
    revert Errors.ZeroExchangeRate();
}
```

이것은 일부 검증이 있지만 수신된 답변(또는 가격)이 실제로 오래된 것인지 확인하지 않습니다. 가격 피드 업데이트가 중지되는 이유는 [여기](https://ethereum.stackexchange.com/questions/133242/how-future-resilient-is-a-chainlink-price-feed/133843#133843)에 나열되어 있습니다. 애플리케이션에서 오래된 가격을 사용하면 볼트 지분 수학에서 잘못된 계산이 발생하여 나쁜 행위자가 악용할 수 있습니다.

## 권장 사항

코드를 다음과 같이 변경하십시오:

```diff
- (, int256 exchangeRate, , , ) = fundingTokenChainLinkFeeds[fundingToken].latestRoundData();
+ (, int256 exchangeRate, , uint256 updatedAt , ) = fundingTokenChainLinkFeeds[fundingToken].latestRoundData();

- if (exchangeRate == 0) {
+ if (exchangeRate <= 0) {
    revert Errors.ZeroExchangeRate();
}

+ if (updatedAt < block.timestamp - 60 * 60 /* 1 hour */) {
+   pause();
+}
```

이렇게 하면 음수 가격(`int256` 유형이므로)과 오래된 가격도 확인하게 됩니다. 제가 제안한 일시 중지 메커니즘을 구현하려면 몇 가지 다른 변경 사항도 필요합니다. 또 다른 옵션은 거기서 그냥 되돌리는(revert) 것입니다.

## 논의

**pashov:** 수정됨.

# [M-02] ERC4626 표준을 올바르게 따르지 않음

## 심각도

**영향:**
중간, 기능이 예상대로 작동하지 않지만 가치 손실은 없기 때문

**가능성:**
중간, 여러 메서드가 표준을 준수하지 않기 때문

## 설명

EIP-4626에 따라 `maxDeposit` 메서드는 "전역 및 사용자 별 제한을 모두 고려해야 합니다. 예를 들어 예금이 완전히 비활성화된 경우(일시적으로라도) 0을 반환해야 합니다(MUST return 0)." 현재 경우는 그렇지 않습니다. 계약이 일시 중지된 경우에도 `maxDeposit` 메서드는 여전히 평소와 같은 값을 반환합니다.

`decimals` 메서드와 관련하여 EIP는 다음과 같이 말합니다: "convertTo 함수를 사용하면 EIP-4626 Vault의 decimals 변수를 사용할 필요가 없지만, 혼란의 원인을 제거하고 프런트엔드 및 기타 오프체인 사용자 간의 통합을 단순화하기 위해 가능한 경우 기본 토큰의 소수점을 반영하는 것이 강력히 권장됩니다."

`LoanVault` 계약은 `decimals`가 호출될 때 반환할 값 18을 하드코딩했지만 이는 기본 토큰의 소수점이어야 합니다(일부 경우 18이 아닐 수도 있음).

## 권장 사항

[표준](https://eips.ethereum.org/EIPS/eip-4626)을 검토하고 상속된 ERC4626 구현에서 메서드를 `override`하는 모든 메서드에 대해 이를 따르십시오.

## 논의

**pashov:** 수정됨.

# [M-03] 사용자 종료/청구 메서드에는 `whenNotPaused` 수정자가 없어야 함

## 심각도

**영향:**
높음, 사용자 자금이 계약에 묶일 수 있기 때문

**가능성:**
낮음, 악의적이거나 침해된 소유자가 필요하기 때문

## 설명

`FlorinStaking`의 `unstake` 및 `claim` 메서드에는 `whenNotPaused` 수정자가 있으며 `LoanVault`의 `redeem` 및 `_withdraw` 메서드도 마찬가지입니다. 이는 프로토콜 소유자가 사용자가 자금을 인출/청구할 수 있는지 결정할 수 있는 공격 벡터를 엽니다. 또한 관리자가 계약을 일시 중지하고 소유권을 포기하여 자금이 영원히 계약에 묶일 가능성도 있습니다.

## 권장 사항

프로토콜의 사용자 종료/청구 메서드에서 `whenNotPaused` 수정자를 제거하거나 프로토콜의 `Pausable` 통합을 재고하십시오.

## 논의

**pashov:** 인지됨.

# [M-04] `apr` 및 `fundingFee` 백분율 값은 제한되지 않음

## 심각도

**영향:**
높음, 이로 인해 계약이 DoS 상태가 되거나 사용자에 대한 보상이 0이 될 수 있기 때문

**가능성:**
낮음, 악의적이거나 침해된 소유자가 필요하거나 소유자 측의 큰 실수가 필요하기 때문

## 설명

`setApr` 및 `setFundingFee` 메서드 모두 백분율 값 인수가 너무 크거나 작은지 확인하는 입력 검증이 없습니다. 악의적이거나 침해된 소유자, 또는 "팻 핑거(fat-finger)"를 한 소유자는 해당 메서드의 인수로 엄청난 숫자를 입력할 수 있으며, 이로 인해 계약이 DoS 상태가 됩니다. 또한 0 또는 100(백분율) 값도 유효하지만 유효해서는 안 됩니다. 이는 사용자에 대한 보상이 0이 되거나 높은 수수료(100% 수수료는 `approveFundingAttempt`의 슬리피지 확인 때문에 불가능함)를 초래할 것입니다.

## 권장 사항

`LoanVault`의 `setApr` 및 `setFundingFee` 메서드 모두에 최소 및 최대 값 확인을 추가하십시오.

## 논의

**pashov:** 수정됨.

# [M-05] 프로토콜은 `_msgSender()`를 광범위하게 사용하지만 모든 곳에서 사용하지는 않음

## 심각도

**영향:**
낮음, 프로토콜은 여전히 정상적으로 작동하지만 예상되는 원하는 유형의 트랜잭션이 작동하지 않기 때문

**가능성:**
높음, 코드가 있는 그대로 문제가 발생할 것이 확실하기 때문

## 설명

이 코드는 메타 트랜잭션을 허용하기 위해 의도된 OpenZeppelin의 `Context` 계약을 사용하고 있습니다. 이는 `msg.sender`를 직접 쿼리 하는 대신 `_msgSender()`를 호출하여 작동합니다. 해당 메서드가 이러한 특수 트랜잭션을 허용하기 때문입니다. 문제는 `LoanVault`의 `onlyDelegate` 및 `onlyFundApprover` 수정자가 `_msgSender()` 대신 `msg.sender`를 직접 사용한다는 것입니다. 이는 의도를 깨뜨리고 해당 수정자가 있는 메서드에서 메타 트랜잭션을 전혀 허용하지 않게 되며, 이는 `LoanVault` 계약에서 중요한 것들 중 하나입니다.

## 권장 사항

`onlyDelegate` 및 `onlyFundApprover` 수정자의 코드를 변경하여 `msg.sender` 대신 `_msgSender()`를 사용하십시오.

## 논의

**pashov:** 수정됨.

# [M-06] 프로토콜에 여러 중앙 집중화 공격 벡터가 존재함

## 심각도

**영향:**
높음, 프로토콜 소유자의 러그(rug)를 초래할 수 있기 때문

**가능성:**
낮음, 침해되거나 악의적인 소유자가 필요하기 때문

## 설명

프로토콜 소유자는 프로토콜의 자금이나 흐름을 제어할 수 있는 권한을 가집니다.

`FlorinToken`의 `mint` 함수는 `FlorinTreasury`인 계약 소유자가 호출할 수 있지만 `FlorinTreasury`에는 `transferFlorinTokenOwnership` 메서드가 있습니다. 이를 통해 `FlorinTreasury` 배포자가 원하는 만큼 `FlorinToken` 토큰을 자신에게 자유롭게 민팅할 수 있습니다.

`FlorinStaking`의 `withdraw` 메서드는 소유자가 스테이킹된 모든 `florinToken` 토큰을 자신을 포함한 모든 지갑으로 옮길 수 있도록 작동합니다.

`FlorinStaking`의 `setMDCperFLRperSecond` 메서드는 소유자가 언제든지 보상을 중지하거나 의도치 않게 즉시 배포할 수 있도록 작동합니다.

`setFundingTokenChainLinkFeed` 메서드는 소유자가 모든 주소를 새 Chainlink 피드로 설정할 수 있도록 허용하므로, 자신이 제어하고 결정한 규칙에 따라 다른 가격을 반환하는 주소를 사용할 수 있습니다.

## 권장 사항

일부 소유자 권한을 제거하거나 Timelock 계약 또는 거버넌스 뒤에 두는 것을 고려하십시오.

## 논의

**pashov:** 인지됨.

# [L-01] 너무 많은 펀딩 토큰이 화이트리스트에 있으면 제거가 불가능할 수 있음

`LoanVault`의 `setFundingToken` 메서드를 사용하면 소유자가 원하는 만큼 많은 펀딩 토큰을 화이트리스트에 추가하여 바운드되지 않은(unbounded) 배열로 푸시 할 수 있습니다. 문제는 그가 너무 많은 토큰을 화이트리스트에 추가하면 배열이 너무 커져서 배열에서 값(예: 마지막 값)을 제거하는 데 너무 많은 가스가 소요될 수 있으며, 심지어 블록 가스 제한보다 더 많아져 화이트리스트에서 일부 펀딩 토큰을 제거하는 것이 불가능해질 수 있다는 것입니다. 이 문제를 해결하려면 `_fundingTokens` 배열 크기에 상한을 두어야 합니다(예: 50).

## 논의

**pashov:** 수정됨.

# [L-02] 등록된 대출 볼트의 수를 제한해야 함

`LoanVaultRegistry`의 소유자가 `registerLoanVault`를 너무 많이 호출하여 `loanVaultIds` 배열이 매우 커질 수 있습니다. 온체인에서 `getLoanVaultIds`를 호출하면 호출에 필요한 가스가 너무 많으면(예: 블록 가스 제한보다 많음) 되돌려질(revert) 수 있습니다. 등록할 수 있는 대출 볼트의 수를 제한하는 것이 좋습니다(예: 최대 200개).

## 논의

**pashov:** 수정됨.

# [L-03] 허용량(Allowance) 확인은 잘못된 보안 감각을 줌

`LoanVault::createFundingAttempt` 메서드에는 다음 허용량 확인이 포함되어 있습니다:

```solidity
if (fundingToken.allowance(_msgSender(), address(this)) < fillableFundingTokenAmount) {
    revert Errors.InsufficientAllowance();
}
```

그러나 그 자체로 `fundingToken`에 대한 `transferFrom`을 수행하지 않습니다. 즉, 메서드 호출은 허용량 취소 트랜잭션으로 백 러닝(back-ran) 될 수 있습니다. 이렇게 하면 확인이 무효화되고 나중에 자금 시도가 실행될 때 되돌려지므로 확인을 제거하는 것이 좋습니다.

## 논의

**pashov:** 인지됨.
