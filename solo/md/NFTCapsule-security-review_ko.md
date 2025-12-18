# 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**NFT Capsule** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# NFT Capsule 소개

이 프로토콜은 사용자가 자신의 NFT(지원되는 컬렉션만 가능)를 예치하고 적극적으로 관리되는 전략을 통해 실제 수익률(ETH)을 얻을 수 있도록 합니다. 주요 목표는 NFT의 비유동성 문제를 해결하고 토큰 보유자의 전반적인 경험을 개선하는 것입니다. 계약은 이더리움 블록체인에 배포될 예정입니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼침.

- 중간 (Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼침.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼침.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방하는 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮음.

- 중간 (Medium) - 조건부로 동기 부여된 공격 벡터일 뿐이지만 여전히 상대적으로 가능성이 높음.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 너무 낮거나 공격자가 인센티브가 거의 또는 전혀 없이 상당한 지분을 필요로 함.

## 심각도 수준별 조치 필요성

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정해야 함 (Should fix)

- 낮음 (Low) - 수정할 수 있음 (Could fix)

# 보안 평가 요약

**_검토 커밋 해시_ - [9f3021c05ad94eb2a2616c63ca23e7f97f82f0c1](https://github.com/0xMedici/protecvault/tree/9f3021c05ad94eb2a2616c63ca23e7f97f82f0c1)**

**_수정 검토 커밋 해시_ - [19c9b6dcd3b43fc28d3a9a117edd16a0826ee5c1](https://github.com/0xMedici/protecvault/tree/19c9b6dcd3b43fc28d3a9a117edd16a0826ee5c1)**

# 발견 사항

# [C-01] 샌드위치 공격을 통해 프로토콜 자산이 도난당할 수 있음

## 심각도

**영향:**
높음, 프로토콜 자산이 손실되므로

**가능성:**
높음, 샌드위치 공격은 매우 일반적이고 실행하기 쉽기 때문

## 설명

`Capsule` 계약은 현재 여러 곳에서 샌드위치 공격에 취약합니다. 스왑을 수행하거나 유동성을 제공/제거하는 모든 곳이 취약한 방식으로 수행됩니다. 코드에 슬리피지(slippage, 가격 미끄러짐) 확인 기능이 있지만 결함이 있습니다. `tradeCRVtoWETH` 메서드를 예로 들어 스왑이 수행되는 방식을 살펴보겠습니다:

```solidity
uint256 expectedAmount = curvePoolWETHCRV.get_dy(2, 1, crvBal); // Estimate WETH received for CRV:WETH exchange
// Exchange CRV for WETH within the slippage limit
curvePoolWETHCRV.exchange(2, 1, crvBal, expectedAmount * LPslippageNum / LPslippageDen);
```

문제는 공격자가 당신이 `tradeCRVtoWETH`를 호출하는 것을 보면 매우 큰 프런트런(front-run) 트랜잭션으로 풀의 균형을 무너뜨려 `expectedAmount`를 훨씬 작게 만들 수 있다는 것입니다. 그런 다음 당신이 스왑을 한 후에 그는 가격을 정상으로 되돌리는 백런(back-run) 트랜잭션을 실행하여 본질적으로 당신의 나쁜 거래를 샌드위치하고 당신의 손실로 이익을 얻습니다. 이는 `add_liquidity`, `remove_liquidity_one_coin`, `calc_withdraw_one_coin` 및 `calc_token_amount`에 대한 기본 호출이 있는 모든 메서드에서 문제입니다.

## 권장 사항

온체인에서 동일한 트랜잭션 계산을 수행하여 슬리피지 허용 오차를 계산하는 대신, 슬리피지 계산을 사용하는 모든 메서드에 "minAmountReceived"라는 인수를 추가하십시오. 이 값은 온체인 조작에 취약하지 않도록 오프체인에서 계산해야 합니다.

# [H-01] 잘못된 `IPETHNFTVault::borrow` 가정으로 인해 프로토콜이 망가짐

## 심각도

**영향:**
중간, 프로토콜을 다시 배포해야 하므로

**가능성:**
높음, 확실히 발생할 것이므로

## 설명

`Capsule`의 `depositNft` 메서드는 `_borrowAmount` 인수를 사용하여 `IPETHNFTVault::borrow`를 호출합니다. 나중에 코드는 실제로 `_borrowAmount` 값을 Curve 풀에 유동성으로 제공할 `PETH` 금액으로 사용하려 합니다. 문제는 해당 볼트의 `borrow` 메서드가 항상 수수료를 받기 때문에 `Capsule`은 `PETH`의 `_borrowAmount`보다 적은 금액을 받게 된다는 것입니다. `IPETHNFTVault::borrow`의 NatSpec에서 인용:

> /// @param \_amount The amount of PUSD to be borrowed. Note that the user will receive less than the amount requested,
> /// the borrow fee and insurance automatically get removed from the amount borrowed

이것은 불충분한 `PETH` 잔액으로 인해 유동성 제공이 항상 실패하여 프로토콜을 사용할 수 없게 됨을 의미합니다.

수수료 값이 현재 0이라 하더라도 변경될 수 있으며 그럴 경우 프로토콜이 망가질 것입니다.

## 권장 사항

유동성을 제공하고 `depositNft`의 `newPosition.amountBorrowed` 값에 대해 차용에서 받은 `PETH`만 사용하십시오. 이 문제는 `increaseBorrowAmount`에도 존재하므로 거기서도 해결해야 합니다.

# [M-01] 가짜 볼트를 제공하는 사용자가 비탄(grief) 공격으로 프로토콜 자산을 훔칠 수 있음

## 심각도

**영향:**
높음, 악의적인 행위자가 프로토콜 자산을 탈취할 수 있으므로

**가능성:**
낮음, 일반적인 공격 벡터이고 전제 조건이 필요하지 않으므로

## 설명

`repay` 메서드(및 다른 모든 메서드)의 `_vault` 인수에는 입력 검증이 없습니다. 즉, 사용자가 자신이 만든 가짜 볼트를 제공할 수 있습니다. 자신이 소유한 NFT 차용 포지션에 대해 가짜 `_vault` 인수로 `repay`를 호출하여 `amountBorrowed > repayment`일 때 다음 코드를 통과하게 됩니다:

```solidity
PETH.approve(address(_vault), tokensWithdrawn); // Approve JPEGD to take PETH
_vault.repay(_id, tokensWithdrawn); // Repay desired amount of loan based on desired repayment size
ePosition.lpSize -= LPcostToRepay; // No need to adjust profit index here, previous check requires they either withdraw or forfeit profits beforehande the users amount borrowed
ePosition.amountBorrowed -= tokensWithdrawn; // Decrease the users amount borrowed
emit DecreaseBorrowAmount(owner, _nft, _id, tokensWithdrawn);
```

이렇게 하면 프로토콜의 `PETH`가 사용자의 가짜 볼트로 전송되어, 비록 그의 NFT는 더 이상 인출할 수 없게 되더라도 프로토콜 자산을 훔치는 것이 됩니다.

## 권장 사항

계약의 모든 메서드에 있는 `_vault` 인수에 화이트리스트 접근 방식을 사용하여 사용자가 가짜 볼트를 제공할 수 없도록 하십시오.

# [L-01] Convex 추가 보상 토큰이 처리되지 않음

`claimCVXReward` 메서드는 Convex 보상 풀 계약에서 `getReward`를 호출합니다. `getReward` 메서드는 실제로 호출자에게 다른 토큰 보상도 보낼 수 있지만 `CRV` 보상만 예상되므로 `Capsule` 계약에서는 처리되지 않습니다. 잠재적인 수익을 놓치지 않도록 다른 토큰 보상을 처리할 방법을 마련하십시오.
