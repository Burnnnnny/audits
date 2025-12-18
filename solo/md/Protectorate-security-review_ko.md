# 소개

**pashov**가 **Protectorate** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **Protectorate** 소개

Protectorate 프로토콜은 이더리움 메인넷의 NFT/NFTfi 프로토콜을 타겟으로 하는 전략을 사용하는 수익률 집계(yield aggregation) 플랫폼입니다. 사용자는 유동성을 제공하고 플랫폼은 생태계 전체에서 가능한 가장 높은 수익률을 위해 유동성을 배포할 위치를 결정합니다. 프로토콜의 기본 토큰인 `$PRTC`는 초기에 수익 공유에 사용되며 결국 거버넌스에 사용될 것입니다.

[프로토콜 라이트페이퍼](https://protectorate.xyz/litepaper)

## 관찰 사항

문서에 따르면 수익률의 80%는 예금자에게 돌아가고 20%는 "성과 수수료"가 되며, 여기서 10%는 `xPTRC` 스테이커에게, 10%는 트레저리(treasury)로 갑니다. 이 20% 분할은 `Staking::distribute`에서 발생합니다.

투자자와 트레저리 계정은 베스팅(vesting)과 관련하여 클리프(cliff)가 없습니다.

`DutchAuction` 계약은 `PRTC` 토큰으로 사전 자금 조달될 것이라고 신뢰합니다.

`LendingVault` 계약은 ERC4626 표준을 사용하고 있으며, 이와 관련된 일반적인 결함이 있습니다.

## 특권 역할 및 행위자

- Envoy (유동성 제공자) - 개별 캡슐(NFT, ETH, 스테이블코인 등)에 자산을 예치합니다.
- Elder (xPRTC 스테이커) - 전략의 수익률에서 수익을 얻습니다.
- Vesting owner - 베스팅 일정을 생성하고 `Vesting`에서 초과 자금을 인출할 수 있습니다.
- Vesting beneficiary - 구성된 일정에 따라 시간이 지남에 따라 토큰을 베스팅받습니다.
- Staker - `WETH` 보상을 받을 것을 기대하며 `PRTC`를 스테이킹합니다.
- Staking `WETH` rewards distributor - `Staking` 계약에 `WETH`를 예치합니다.
- Treasury - 스테이킹 분배된 `WETH`의 절반을 받습니다.
- Vault depositor - 전략 수익을 기대하며 볼트에 자산을 예치합니다.
- Bend Dao strategy owner - 전략 계약에서 자산에 대한 보상을 예치, 인출 및 스왑할 수 있습니다.
- DutchAuction participant - `PRTC` 토큰을 구매하기 위해 `ETH`를 커밋합니다.
- DutchAuction beneficiary - 계약에 `PRTC` 토큰을 잔액으로 보내고 경매 완료 시 성공하면 `ETH`를, 실패하면 초기 보증금을 돌려받습니다.
- PRTC token recipient - `PRTC` 배포 시 전체 총 공급 잔액을 받습니다.

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

**_검토 커밋 해시_ - [de93a8aff725b69f9f525858f6fdaefe92c8f966](https://github.com/protecc-labs/protectorate-contracts/commit/de93a8aff725b69f9f525858f6fdaefe92c8f966)**

**_수정 검토 커밋 해시_ - [0888811ffcadff0c1385823b27897f02f04c2a2d](https://github.com/protecc-labs/protectorate-contracts/tree/0888811ffcadff0c1385823b27897f02f04c2a2d)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `interfaces/**`
- `strategies/BendDaoLendingStrategy`
- `utils/Constants`
- `vaults/LendingVault`
- `DutchAuction`
- `ETHRouter`
- `PRTC`
- `StakingContract`
- `Vesting`

다음과 같은 수의 문제가 발견되었으며 심각도별로 분류되었습니다:

- 치명적 & 높음: 3개 문제
- 중간: 2개 문제
- 낮음: 3개 문제

---

# 결과 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | 전략이 돈을 잃고 있을 때 볼트에서 청구할 마지막 사람이 모든 손실을 떠안음 | 치명적 |
| [H-01] | 수혜자의 베스팅 일정을 덮어쓸 수 있음 | 높음 |
| [H-02] | 볼트 예치자가 프론트러닝 당하여 자금을 잃을 수 있음 | 높음 |
| [M-01] | 일부 베스팅 수령인이 일시적으로 청구할 수 없음 | 중간 |
| [M-02] | 불충분한 입력 검증으로 인해 자금 손실이 발생할 수 있음 | 중간 |
| [L-01] | 전략 계약이 비표준 ERC20 토큰과 호환되지 않음 | 낮음 |
| [L-02] | 곱셈 전 나눗셈으로 인해 반올림 오류가 발생할 수 있음 | 낮음 |
| [L-03] | `DutchAuction`의 신뢰 가정 | 낮음 |

# 상세 결과

# [C-01] 전략이 돈을 잃고 있을 때 볼트에서 청구할 마지막 사람이 모든 손실을 떠안음

## 심각도

**영향:**
높음, 일부 사용자가 상당한 가치 손실을 입을 수 있음

**가능성:**
높음, 특정 시점에 전략이 돈을 잃고 있을 가능성이 있음

## 설명

현재 `LendingVault`가 설계된 방식은 볼트의 자금이 선택된 전략으로 이체되는 것입니다. 자금 중 일부가 전략에 대출된 상태에서도 사용자가 볼트 잔액에서 자금을 인출할 수 있다는 사실 때문에 다음과 같은 시나리오가 발생할 수 있습니다:

1. Alice가 볼트에 100 ETH를 예치합니다.
2. Bob이 볼트에 100 ETH를 예치합니다.
3. 전략이 볼트에서 200 ETH를 요청합니다 - 이제 볼트 잔액은 0, 전략 잔액은 200입니다.
4. Chris가 볼트에 100 ETH를 예치합니다.
5. 전략이 성과가 좋지 않아 100 ETH 잔액만 남습니다.
6. Bob이 이를 보고 자신의 몫인 100 ETH를 인출합니다.
7. 이제 전략은 손실을 보고 있지만 자금은 반환되어, 볼트 잔액은 100 ETH, 전략 잔액은 0이 됩니다.
8. 이제 Alice와 Chris는 전략의 모든 손실을 떠안게 되며, Bob은 손실에도 불구하고 초기 예치금의 100%를 확보했습니다.

## 권장 사항

자금이 전략에 대출되어 있는 동안 인출을 금지하거나 볼트-전략 대출에 대한 다른 설계를 고려하십시오.

# [H-01] 수혜자의 베스팅 일정을 덮어쓸 수 있음

## 심각도

**영향:**
높음, 베스팅되어야 할 남은 금액이 계약에 영원히 갇힐 것임

**가능성:**
중간, 동일한 수혜자에 대해 1개 이상의 베스팅 일정이 필요함

## 설명

`Vesting`의 베스팅 일정은 `schedules` 매핑에 저장되며, `_beneficiary` 주소를 키로 사용합니다. 문제는 수혜자가 이미 예정된 베스팅을 가지고 있는 경우, 두 번째 일정이 설정되면 첫 번째 일정은 덮어쓰여지지만 `schedulesTotalAmount`는 여전히 첫 번째 예정된 베스팅 자금을 보유하게 됩니다. 즉, 이 자금들은 `Vesting` 계약에 영원히 갇히게 됩니다.

## 권장 사항

가능한 해결책은 `beneficiary` 주소 대신 베스팅 ID를 `schedules` 매핑의 키로 사용하거나, 동일한 `beneficiary`에 대해 여러 일정이 설정되는 것을 허용하지 않는 것입니다.

# [H-02] 볼트 예치자가 프론트러닝 당하여 자금을 잃을 수 있음

## 심각도

**영향:**
높음, 사용자 자산 도난 가능성 있음

**가능성:**
중간, 공격자가 첫 번째 볼트 예치자인 경우에만 작동함

## 설명

다음과 같은 공격이 가능합니다:

1. `LendingVault` 계약이 방금 배포되었으며 `assets`와 `shares`가 0 예치/발행되었습니다.
2. Alice가 `10e18` 가치의 `_assets`를 예치하기 위해 트랜잭션을 보냅니다.
3. Bob은 악의적인 사용자/봇이며 멤풀에서 트랜잭션을 보고 1 wei 가치의 자산을 예치하여 1 wei의 주식을 받음으로써 이를 프론트러닝합니다.
4. Bob은 또한 Alice의 트랜잭션을 `LendingVault` 계약으로 10e18 가치의 자산을 직접 ERC20::transfer하는 것으로 프론트러닝합니다.
5. 이제 Alice의 트랜잭션에서 코드는 Alice의 주식을 `shares = assets.mulDiv(totalSupply(), totalAssets(), Math.Rounding.Down)`으로 계산하는데, 여기서 `totalAssets`는 `_asset.balanceOf(address(this))`를 반환하여 주식이 0으로 버림(round down) 됩니다.
6. Alice는 10e18 가치의 자산을 예치했음에도 불구하고 0 주식을 발행받습니다.
7. 이제 Bob은 `withdraw` 호출로 Alice의 트랜잭션을 백러닝(back-run)합니다. 여기서 `assets`는 계약의 자산 잔액이므로, 그는 자신의 1 주식을 소각하고 자신의 예치금 + Alice의 전체 예치금을 인출할 수 있습니다.

이것은 예치자들이 문제를 알아차릴 때까지 여러 번 반복될 수 있습니다.

## 권장 사항

먼저, 모든 예치가 Flashbots를 통해 이루어지도록 하여 트랜잭션이 샌드위치/프론트러닝되지 않도록 하십시오.

그런 다음 UniswapV2가 두 가지 유형의 보호로 이를 어떻게 해결했는지 살펴볼 수 있습니다:

[첫째](https://github.com/Uniswap/v2-core/blob/master/contracts/UniswapV2Pair.sol#L119-L121), 첫 번째 `mint` 시 실제로 처음 1000 주식을 제로 주소(zero-address)로 발행합니다.

[둘째](https://github.com/Uniswap/v2-core/blob/ee547b17853e71ed4e0101ccfd52e70d5acded58/contracts/UniswapV2Pair.sol#L125), 발행된 주식이 0이 아니어야 합니다.

이러한 솔루션을 모두 구현하면 이 취약점이 해결됩니다.

# [M-01] 일부 베스팅 수령인이 일시적으로 청구할 수 없음

## 심각도

**영향:**
중간, 자금이 30일 동안 잠길 것임

**가능성:**
중간, 클리프가 30일 미만일 때만 발생함

## 설명

`Vesting`의 `SLICE_PERIOD` 상수는 30일로 설정되어 있습니다. `_computeReleasableAmount`의 다음 수학으로 인해:

```solidity
uint256 vestedSeconds = (timeFromStart / SLICE_PERIOD) * SLICE_PERIOD;
```

`timeFromStart`가 30일 미만이면 이는 0으로 버림 되며, 이는 30일이 지날 때까지 청구할 금액이 항상 0임을 의미합니다. 이는 특히 `Investors`와 `Treasury`에 대해 예상되는 `cliff`가 없는(0인) 베스팅 일정에 적용됩니다.

## 권장 사항

`SLICE_PERIOD`를 작게 만들거나, 이 계산을 사용하지 않는 클리프 없는 베스팅 일정을 처리하기 위한 다른 설계를 구현하십시오.

# [M-02] 불충분한 입력 검증으로 인해 자금 손실이 발생할 수 있음

## 심각도

**영향:**
높음, 자금이 계약에 영원히 갇힐 수 있음

**가능성:**
낮음, 관리자의 구성 오류가 필요함

## 설명

코드베이스의 여러 곳에 자금 동결로 이어질 수 있는 불충분한 입력 검증이 있습니다.

1. `LendingVault` 생성자의 `performanceFee`는 `adjustPerformanceFee` 설정자(setter)에서와 같이 검증되지 않습니다.
2. `Vesting::createSchedule`의 `_duration` 매개변수는 검증되지 않아 너무 크거나 너무 작을 수 있습니다.
3. `DutchAuction` 생성자에서 설정된 `AuctionDetails`에는 너무 크거나 너무 작은 타임스탬프와 가격이 있을 수 있습니다.

`Vesting`의 `_duration`이 너무 크거나 `DutchAuction`의 `startTime`과 `endTime` 사이의 차이가 너무 크면 자금이 계약에 영원히 갇힐 수 있습니다.

## 권장 사항

`LendingVault`의 생성자에서 `adjustPerformanceFee` 메서드를 호출하여 입력 검증을 사용하십시오. `createSchedule`의 `_duration` 매개변수와 관련해서는 최소 7일, 최대 2년과 같은 제한을 사용하십시오.

`AuctionDetails`의 경우 여러 가지를 확인해야 합니다:

1. `startTime`과 `endTime`이 이미 지나지 않았는지
2. `endTime`이 `startTime` 이후인지
3. `endTime`이 너무 먼 미래가 아닌지 - 아마도 최대 15일

`startPrice`와 `minimumPrice`에 대해서도 마찬가지입니다:

1. `minimumPrice`가 `startPrice`보다 작은지 확인
2. 그들의 값이 너무 크지 않은지 확인

# [L-01] 전략 계약이 비표준 ERC20 토큰과 호환되지 않음

일부 토큰(예: `USDT`)은 ERC20 표준을 올바르게 따르지 않으며 `approve` 호출 시 `bool`을 반환하지 않습니다. 이러한 토큰은 `deposit` 및 `withdraw`의 `approve` 호출이 반환 데이터가 0이므로 항상 되돌아가기(revert) 때문에 `BendDaoLendingStrategy` 계약과 호환되지 않습니다. 대신 `SafeERC20`의 `forceApprove`를 사용하십시오.

# [L-02] 곱셈 전 나눗셈으로 인해 반올림 오류가 발생할 수 있음

`DutchAuction::priceFunction`에서 곱셈 전에 나눗셈이 있습니다:

```solidity
uint256 priceDeclinePerSecond =
            (auctionDetails.startPrice - auctionDetails.minimumPrice) / auctionDuration;

return auctionDetails.startPrice - (timeSinceAuctionStart *
priceDeclinePerSecond);
```

이는 반올림 오류로 이어질 수 있지만 애플리케이션에 심각한 영향을 미치지는 않습니다. 여전히 Solidity에서는 항상 나눗셈보다 곱셈을 먼저 수행해야 합니다.

# [L-03] `DutchAuction`의 신뢰 가정

`DutchAuction` 계약은 올바르게 작동하기 위해 `auctionDetails.totalTokens` 만큼의 `PRTC` 잔액을 보유하고 있을 것으로 예상하지만, 이는 어떤 방식으로도 검증되지 않습니다. 또한 경매 실패 시 `auctionDetails.totalTokens`는 수혜자에게 전송되는 `PRTC`의 양이며, 금액이 1 wei라도 적으면 되돌아갑니다(revert). 대신 `prtc.balanceOf(address(this))`를 사용하고, 신뢰가 필요 없도록(trustless) 생성자에서 `PRTC` 토큰을 계약으로 전송하는 것을 고려할 수 있습니다.

