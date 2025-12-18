# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**dumontgg/dumont-contract-v1** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Dumont 소개

Dumont는 싱글 플레이어가 초기에 해시 되어 계약에 저장된 숨겨진 카드의 숫자를 추측할 수 있는 블록체인 게임입니다. 이 게임은 커밋-리빌(commit-reveal) 메커니즘을 사용하여 플레이어가 추측에 배팅하면, 서버(또는 '공개자')가 카드 번호를 공개하여 승자를 결정합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간 - 프로토콜 자산의 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터일 뿐이지만 여전히 발생 가능성이 상대적으로 높습니다.

- 낮음 - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 또는 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정해야 함 (Should fix)

- 낮음 - 수정할 수 있음 (Could fix)

# 보안 평가 요약

_검토 커밋 해시_ - [3165eff301b2fe23a6ca38d785075e507d9ae879](https://github.com/dumontgg/dumont-contract-v1/tree/3165eff301b2fe23a6ca38d785075e507d9ae879)

_수정 검토 커밋 해시_ - [8bb7d876d36267ab05604c0e3df71826573e83b9](https://github.com/dumontgg/dumont-contract-v1/tree/8bb7d876d36267ab05604c0e3df71826573e83b9)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Vault`
- `Revealer`
- `MontRewardManager`
- `MONT`
- `GameFactory`
- `Game`
- `Burner`
- `Initializable`
- `interfaces/`

# 발견 사항

# [C-01] 하우스 수수료 상한 메커니즘의 결함

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`transferPlayerRewards()` 함수는 게임 결과와 유동성에 접근할 수 있고, 플레이어로 참여하여 위험 없이 배팅하고 보상 풀을 고갈시킬 수 있는 운영자의 남용을 방지하기 위한 보호 메커니즘을 구현하려고 시도합니다.

문제는 이 메커니즘에 결함이 있어 그러한 공격으로부터 프로토콜을 보호하지 못할 뿐만 아니라 사용자 보상을 상당히 제한한다는 것입니다.

`reward`가 `_totalAmount`보다 클 때 `_totalAmount`로 설정되는 것에 주목하십시오. 오류는 `reward`가 MONT 토큰으로 측정되는 반면 `_totalAmount`는 USDT 토큰을 나타내므로 가치가 달라 직접 비교할 수 없다는 것입니다.

게다가 대부분의 체인에서 USDT는 6자리 정밀도를 가지는 반면 MONT는 18자리를 가지므로 보상이 상당히 제한되어 영향이 훨씬 더 큽니다:

```solidity
    function transferPlayerRewards(...) external returns (uint256 reward) {
        uint256 houseFee = calculateHouseFee(_betAmount, _houseEdgeAmount, _isPlayerWinner);

        uint256 price = getMontPrice();

        uint256 houseFeeFixedPoint = houseFee * 1e18;

        reward = ((houseFeeFixedPoint * 8) / 10) / price;

@>      if (reward > _totalAmount) {
@>          reward = _totalAmount;
        }

@>      balances[_player] += reward;
```

## 개념 증명 (Proof of Concept)

`MontRewardManager.sol`에 다음 로그를 추가하고 `test_revealCardWithTheRightData` 테스트를 실행하십시오:

```diff
+       console.log("USDT bet amount:        ", _betAmount);
+       console.log("USDT total won amount:  ", _totalAmount);
+       console.log("MONT expected reward:   ", reward);

        if (reward > _totalAmount) {
                reward = _totalAmount;
        }

+       console.log("MONT actual reward:     ", reward);
```

결과:

```
Ran 1 test for test/integration/revealer/revealCard.t.sol:RevealCardTest
[PASS] test_revealCardWithTheRightData() (gas: 292421)
Logs:
  USDT bet amount:         1000000
  USDT total won amount:   11800000
  MONT expected reward:    5000000000000000000000
  MONT actual reward:      11800000
```

보시다시피 사용자는 훨씬 적은 보상을 받습니다. MONT 정밀도는 18자리이므로 보상은 예상보다 몇 자릿수 더 낮습니다.

## 권장 사항

1. 현재 코드대로라면 USDT 하우스 수수료의 80%는 나중에 소각하기 위해 Vault를 통해 버너(burner)로 보내지고, 이에 상응하는 MONT 토큰은 예상되는 분배인 MONT/USDT 풀의 가격에 따라 플레이어와 추천인에게 보상됩니다.

따라서 상한선을 제거해야 합니다:

```diff
-    if (reward > _totalAmount) {
-        reward = _totalAmount;
-    }
```

오동작하는 운영자는 승리할 때마다 하우스 수수료의 20%에 해당하는 손실을 입게 됩니다(금고에 보관되므로). 반면에 그들은 큰 추측 비율 승수로 계속 승리하여 금고를 고갈시킬 수 있습니다.

따라서 신뢰할 수 없는 운영자의 경우 공정한 사용자에게 불이익을 주지 않으면서 언급된 문제를 고려하여 보다 강력한 예방/처벌 메커니즘을 구축해야 합니다.

2. 또한 보상과 비교하기 전에 `_totalAmount`를 18자리로 스케일링하는 것을 고려하십시오.

# [H-01] 패배한 게임에서 수수료가 소각되지 않음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

소각 메커니즘에 따르면 모든 배팅의 승리 또는 **패배** 금액에 10% 수수료가 적용되어야 합니다. 해당 USDT 수수료의 80%는 매월 말에 교환되어 MONT 토큰을 구매하고 소각합니다.

문제는 플레이어가 승자일 때만 Vault가 자산을 버너로 보낸다는 것입니다:

```solidity
    function transferPlayerRewards(...) external {
        IGameFactory.GameDetails memory game = gameFactory.games(_gameId);

        ...

        uint256 burnAmount = (houseEdge * 8) / 10;

        if (_isPlayerWinner) {
            usdt.safeTransfer(game.player, _totalAmount);
@>          usdt.safeTransfer(address(burner), burnAmount);

            emit PlayerRewardsTransferred(game.player, _totalAmount);
        }
```

`MontRewardManager`는 패배한 게임에서도 배팅 금액의 10%에 해당하는 MONT 보상을 계속 분배합니다.

이 문제점은 매도 압력이 토큰 소각으로 예상되는 해당 매수 압력으로 보상되지 않아 MONT 가격에 영향을 미친다는 것입니다.

## 권장 사항

```diff
    uint256 burnAmount = (houseEdge * 8) / 10;
+   usdt.safeTransfer(address(burner), burnAmount);

    if (_isPlayerWinner) {
        usdt.safeTransfer(game.player, _totalAmount);
-       usdt.safeTransfer(address(burner), burnAmount);

        emit PlayerRewardsTransferred(game.player, _totalAmount);
    }
```

# [H-02] 더 많은 보상을 얻기 위해 Mont 가격 조작 가능

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`MontRewardManager` 계약은 Uniswap Quoter의 `quoteExactInputSingle()` 함수를 사용하여 MONT 1단위의 USDT 가격을 가져옵니다.

문제는 계산에 현재 풀 가격을 사용하기 때문에 이 가격을 쉽게 조작할 수 있다는 것입니다.

```solidity
    function transferPlayerRewards(...) external returns (uint256 reward) {
        uint256 price = getMontPrice();
        ...

        reward = ((houseFeeFixedPoint * 8) / 10) / price;

        balances[_player] += reward;
    }

    function getMontPrice() private returns (uint256 price) {
        price = quoter.quoteExactInputSingle(address(mont), address(usdt), poolFee, 1e18, 0);
    }
```

플레이어는 운영자가 보낸 `revealCard()` 트랜잭션을 샌드위치 공격할 수 있습니다.

운영자가 플레이어가 `guessCard()`를 호출한 후 예상 시간 내에 카드 공개 트랜잭션을 호출할 가능성이 높기 때문에 이 공격의 타이밍을 맞출 수 있습니다.

공격 진행 방법은 다음과 같습니다:

1. `revealCard()` 트랜잭션이 언제 전송될지 추정하거나 가능한 경우 프론트런(frontrun) 합니다.
2. 스왑을 통해 MONT 가격이 내려가도록 조작합니다.
3. 운영자는 `revealCard()`를 호출하고 보상 전송을 트리거 합니다.
4. MONT 가격이 낮으므로 부당하게 많은 양의 보상이 플레이어에게 추가됩니다.
5. `claim()`을 통해 MONT 보상을 청구합니다.
6. 스왑을 통해 가격을 초기 값으로 되돌리거나 그대로 둡니다.
7. 공격자는 추가적인 MONT 토큰 금액으로 이익을 얻습니다.

또한 반대로 공개 전에 가격을 올려 다른 플레이어가 더 적은 보상을 받도록 수행할 수도 있습니다.

## 권장 사항

MONT 가격을 얻으려면 오라클이나 TWAP를 사용하십시오.

# [M-01] 게임 간 동일한 해시 사용

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

한 게임에서 해시가 공개되면 해당 매개변수(`_number` 및 `_salt`)가 공개됩니다. 이 해시는 다른 게임에서 사용해서는 안 됩니다. 그렇지 않으면 게임을 악용할 위험이 있습니다(최대 가용 금액으로 레버리지된 보장된 승리).

## 권장 사항

해시가 이 게임에서만 사용될 수 있도록 `verifySalt()`에서 `address(this)`를 추가로 인코딩하는 것을 고려하십시오.

# [M-02] MONT 가격이 낮을 때 예상보다 많은 보상

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

MONT 보상은 다음 공식을 사용하여 계산됩니다:

```solidity
function transferPlayerRewards(...) external returns (uint256 reward) {
    ...
    uint256 price = getMontPrice();
    uint256 houseFeeFixedPoint = houseFee * 1e18;
    reward = ((houseFeeFixedPoint * 8) / 10) / price;
    ...
}

function getMontPrice() private returns (uint256 price) {
    price = quoter.quoteExactInputSingle(address(mont), address(usdt), poolFee, 1e18, 0);
}
```

문제는 MONT 토큰의 정밀도는 18자리인 반면 대부분의 체인에서 USDT의 정밀도는 6자리라는 점입니다.

MONT 1 전체 단위(1e18 토큰)의 가격을 요청하면 반환된 가격에서 정밀도 손실이 발생하여 계산된 보상에 영향을 미칩니다.

USDT 가격을 $1, MONT 가격을 $0.0000159라고 가정하면 `getMontPrice()` 함수는 `15`를 반환하며, 이는 약 6%의 정밀도 손실을 발생시킵니다.

즉, 이 경우 사용자는 원래 받아야 할 것보다 약 6% 더 많은 MONT 보상을 받게 됩니다. MONT 가격이 낮을수록 보상은 더 커집니다.

## 권장 사항

USDT가 6자리 정밀도를 갖는 것을 고려할 때, 한 가지 가능한 방법은 역가격(USDT 1e6 단위당 몇 개의 MONT 토큰인지)을 인용하고 보상 공식을 리팩토링하여 정밀도 손실을 무시할 수 있게 하고 플레이어가 받아야 할 보상을 받도록 하는 것입니다.

# [M-03] 남은 모든 카드를 선택하여 무료 카드 공개

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`getGuessRate()` 함수를 사용하면 플레이어가 남은 모든 카드를 `_guessedNumbers`로 선택할 수 있습니다. 즉, 항상 승리하게 됩니다:

```solidity
function getGuessRate(uint256 _numbers) public view returns (UD60x18 rate) {
    uint256 remainingCards = 52 - cardsRevealed;
    uint256 remainingSelectedCard = 0;

    for (uint256 i = 0; i < 13; ++i) {
        if ((_numbers & (1 << i)) > 0) {
            remainingSelectedCard += 4 - revealedCardNumbersCount[i];
        }
    }

    rate = ud(remainingCards).div(ud(remainingSelectedCard));
}
```

총 승리 금액은 1배가 됩니다:

```solidity
uint256 totalWinningBetAmount = getGuessRate(_guessedNumbers).mul(ud(_betAmount)).unwrap();
```

그들은 보상으로 동일한 배팅 금액을 받게 됩니다:

```solidity
uint256 reward = totalWinningBetAmount - ((totalWinningBetAmount - _betAmount) / 10);
```

하우스 엣지(house edge)는 0이 됩니다:

```solidity
_card.houseEdgeAmount = totalWinningBetAmount - reward;
```

즉, 플레이어는 `requestFreeRevealCard()`에 구현된 `maxFreeReveals` 제한을 우회하여 무료로 카드를 추측할 수 있으며, 운영자가 의도한 것보다 더 많은 무료 공개를 처리하게 만들 수도 있습니다.

## 권장 사항

항상 플레이어로부터 가져갈 하우스 엣지 금액이 있도록 `getGuessRate()`에 이 확인을 추가하는 것을 고려하십시오.

```diff
+   require(remainingCards != remainingSelectedCard);
```

# [M-04] 운영자가 비활성 상태일 때 사용자에게 불이익

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

운영자가 비활성 상태이고 적시에 `revealCard()`를 호출하지 않으면 사용자는 `claimWin()`을 통해 승리를 청구할 수 있습니다.

이것은 플레이어가 보상 토큰을 받지 않아야 한다는 조건으로 `transferPlayerRewards()`를 트리거 합니다:

```solidity
function claimWin(uint256 _index) external onlyPlayer onlyInitialized {
    ...

    vault.transferPlayerRewards(
        gameId, _card.betAmount, _card.totalAmount, _card.houseEdgeAmount, true, !SHOULD_RECEIVE_REWARDS
    );
}
```

문제는 하우스 엣지 수수료가 플레이어로부터 이미 공제되었다는 것입니다. 보상이 발행되지 않았음에도 불구하고 80%는 소각되고 나머지 20%는 Vault에 보관됩니다.

따라서 사용자는 아무런 잘못을 하지 않았지만 운영자에게 잘못이 있는 이 시나리오에서 불이익을 받습니다.

```solidity
    function transferPlayerRewards(...) external {
        ...

        uint256 houseEdge = _betAmount / 10;

        if (_isPlayerWinner) {
            houseEdge = _houseEdgeAmount;
        }

        uint256 burnAmount = (houseEdge * 8) / 10;

        if (_isPlayerWinner) {
            usdt.safeTransfer(game.player, _totalAmount);
            usdt.safeTransfer(address(burner), burnAmount);
        }

@>      if (_receiveMontReward) {
@>          montRewardManager.transferPlayerRewards(_betAmount, _totalAmount, houseEdge, game.player, _isPlayerWinner);
        }
    }
```

## 권장 사항

MONT 보상이 발행되지 않는 시나리오에서는 하우스 엣지 수수료 또는 그 일부를 플레이어에게 다시 전송(버너로 보내지 않음)하여 플레이어에게 보상하는 것을 고려하십시오.

# [M-05] 예상 보상의 일부가 분배되지 않음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`transferPlayerRewards()` 함수는 플레이어와 추천인에게 분배할 보상을 다시 계산합니다.

추천인이 없는 경우 80%만 분배될 것으로 예상됩니다.

추천인이 있는 경우 모든 보상이 분배될 것으로 예상되지만 계산 방식 때문에 99%만 분배됩니다.

100,000 토큰에 대한 예: 플레이어에게 90,000 + 추천인에게 9,000 + 분배되지 않음 1,000

```solidity
    (bool isReferrerSet, address referrer) = checkReferrer(_player);

    if (!isReferrerSet) {
        reward = (reward * 8) / 10;
    } else if (isReferrerSet) {
        reward = (reward * 9) / 10;
        balances[referrer] += reward / 10;
    }

    balances[_player] += reward;
```

## 권장 사항

다음은 두 가지 제안된 대체 분배입니다.

옵션 1: 플레이어는 원래 보상의 90%를 받고, 추천인은 원래 보상의 10%를 받습니다. 추천인은 플레이어가 받은 승리의 9%를 받게 됩니다.

100,000 토큰에 대한 예: 플레이어에게 90,000 + 추천인에게 10,000

```diff
    (bool isReferrerSet, address referrer) = checkReferrer(_player);

    if (!isReferrerSet) {
        reward = (reward * 8) / 10;
    } else if (isReferrerSet) {
-       reward = (reward * 9) / 10;
        balances[referrer] += reward / 10;
+       reward = (reward * 9) / 10;
    }

    balances[_player] += reward;
```

옵션 2: 추천인이 플레이어가 받는 보상의 10%를 받도록 금액을 계산합니다. 이는 플레이어에게 원래 보상의 ~90.9% + 추천인에게 원래 보상의 ~9.1%를 의미합니다.

100,000 토큰에 대한 예: 플레이어에게 90,909 + 추천인에게 9,090 + 분배되지 않음 1 wei

```diff
    (bool isReferrerSet, address referrer) = checkReferrer(_player);

    if (!isReferrerSet) {
        reward = (reward * 8) / 10;
    } else if (isReferrerSet) {
-       reward = (reward * 9) / 10;
+       reward = (reward * 10) / 11;
        balances[referrer] += reward / 10;
    }

    balances[_player] += reward;
```

# [M-06] 중앙화 위험

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

악의적이거나 침해된 소유자가 있는 경우 사용자 자산은 다음 시나리오에서 위험할 수 있습니다.

- 진행 중인 게임에 대한 보상을 지불하기 위한 USDT는 언제든지 `withdraw()`를 통해 Vault에서 인출될 수 있습니다.
- 운영자가 직접 플레이하고 큰 추측 비율 승수로 항상 승리하게 하여 금고를 고갈시킬 수도 있습니다.
- Vault의 `gameFactory`는 `setGameFactory()`를 통해 언제든지 변경되어 나중에 속이는 게임 정보로 `transferPlayerRewards()`를 호출하여 보상을 얼마든지 청구할 수 있습니다.
- Vault의 `montRewardManager`는 `setMontRewardManager()`를 통해 언제든지 변경되어 `transferPlayerRewards()` 실행을 방지하고 플레이어 승리 시 되돌릴(revert) 수 있습니다.
- `minimumBetAmount` 및 `maximimBetRate` 값은 플레이어가 카드를 추측하지 못하도록 Vault에서 언제든지 수정할 수 있습니다.
- GameFactory의 `gameCreationFee`는 게임이 생성되기 직전에 변경되어 `createGame()`을 호출할 때 플레이어로부터 승인된 모든 USDT를 가져갈 수 있습니다. 플레이어가 동의하지 않을 수 있는 다른 게임 설정은 `createGame()`을 호출하기 직전에 언제든지 변경될 수 있습니다.

## 권장 사항

중앙화 위험을 완화하기 위한 몇 가지 제안:

- `guessCard()`를 통해 배팅이 이루어지면 `revealCard()` 또는 `claimWin()`이 실행될 때까지 Vault에 USDT 보상을 잠급니다(Lock). 그 후에 잠금을 해제합니다. 이렇게 하면 진행 중인 배팅을 지불할 수 있는 충분한 유동성이 확보됩니다.
- 타임락(timelock) 계약을 생성하고 일정 시간이 지난 후에 Vault 및 GameFactory 설정에 변경 사항을 적용하여 사용자가 이를 인지할 수 있도록 하십시오. 이렇게 하면 새로운 외부 계약으로 시스템의 기본 로직을 변경하거나 불공정한 값을 설정하는 것을 방지할 수 있습니다.

# [M-07] 추천인 보너스를 자신/통제된 주소로 악용

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

Dumont는 초대받은 사람이 획득한 게임 내 $MONT 보상의 10%에 해당하는 보너스 보상을 제공하는 온체인 추천 프로그램을 구현했습니다. 예를 들어, 초대받은 사람이 보상으로 20,000 $MONT를 획득하면 추천 보상으로 2,000 $MONT를 추가로 받습니다. 또한 추천 링크를 통해 게임에 가입한 사용자는 그렇지 않은 사용자보다 10% 더 많은 수익을 얻습니다.

추천인이 있는 경우 총 보상은 추천인이 없을 때보다 항상 높기 때문에 추천인을 두는 것이 항상 수익성이 높습니다.

```solidity
        if (!isReferrerSet) {
            reward = (reward * 8) / 10;
        } else if (isReferrerSet) {
            reward = (reward * 9) / 10;

            balances[referrer] += reward / 10;
        }

        balances[_player] += reward;
```

결과적으로 사용자는 이 추가 보너스 혜택을 받기 위해 제어되는 EOA를 추천인으로 지정할 수 있습니다.

## 권장 사항

추천인이 최종 총 보상 합계에 영향을 미치지 않는 선택적 매개변수라면 공정할 것입니다.
추천인 보상을 추가 보너스가 아닌 사용자 보상에서 수수료로 가져가는 것을 고려하십시오.

또는 추천 프로그램을 오프체인으로 이동하는 것을 고려하십시오.

# [L-01] `_lockedAmount`에 대한 온전성 검사 누락

TaxBasedLocker 계약의 initialize() 함수에서 계약은 잠글 지정된 토큰 양으로 초기화됩니다. NatSpec 요구 사항에 따르면 "지정된 토큰 금액은 0보다 커야 합니다"라고 명시되어 있습니다. 그러나 현재 구현에는 아래와 같이 `_lockedAmount`가 실제로 0보다 큰지 확인하는 검증이 부족합니다:

```solidity
    function initialize(uint256 _lockedAmount) external onlyOwner {
        if (lockedAmount > 0) {
            revert AlreadyInitialized();
        }
        lockedAmount = _lockedAmount;
        uint256 balance = token.balanceOf(address(this));
        if (balance < lockedAmount) {
            token.safeTransferFrom(owner(), address(this), lockedAmount - balance);
        }
        startTime = block.timestamp;
        emit Initialized();
    }
```

NatSpec 요구 사항에 맞게 지정된 토큰 금액이 0보다 큰지 확인하는 검사를 추가하십시오.

# [L-02] GameFactory 수수료에 대한 상한선 누락

`setGameCreationFee()` 함수를 사용하면 소유자가 주어진 인스턴스에 대해 새로운 `gameCreationFee` 백분율을 설정할 수 있습니다. 그러나 `_gameCreationFee` 매개변수에 대해 정의된 상한선이나 한도가 없습니다:

```solidity
    function setGameCreationFee(uint256 _gameCreationFee) external onlyOwner {
        emit GameFeeChanged(gameCreationFee, _gameCreationFee);

        gameCreationFee = _gameCreationFee;
    }
```

이로 인해 과도하게 높은 수수료가 설정될 수 있으며, 이는 사용자가 계약과 상호 작용하는 것을 방해할 수 있습니다. `gameCreationFee`에 대한 최대 제한을 도입하여 과도하게 높은 값으로 설정될 수 없도록 하십시오.

# [L-03] TaxBasedLocker를 다시 초기화할 수 있음

`initialize()` 함수는 초기화하기 위해 `lockedAmount > 0`만 확인하지만, `withdraw()`에서 해당 값이 재설정되어 계약을 다시 초기화할 수 있습니다:

```solidity
    function initialize(uint256 _lockedAmount) external onlyOwner {
        if (lockedAmount > 0) {
            revert AlreadyInitialized();
        }
        ...
    }

    function withdraw() external onlyOwner {
        ...
        lockedAmount = 0;
    }
```

검사를 위해 적절한 초기화 변수를 설정하는 것을 고려하십시오.

# [L-04] TaxBasedLocker로 전송된 추가 토큰

TaxBasedLocker에 잠긴 토큰은 먼저 계약으로 전송되거나 초기화할 때 직접 전송될 수 있습니다.

잔액이 잠긴 금액보다 낮으면 나머지 자감이 전송됩니다.

문제는 `lockedAmount` 값보다 더 많은 토큰이 사전에 전송된 경우 해당 토큰은 절대 잠금 해제될 수 없으며 계약에 갇힌 채로 남게 된다는 것입니다.

```solidity
    function initialize(uint256 lockedAmount) external onlyOwner {
        ...
        uint256 balance = token.balanceOf(address(this));

        if (balance < lockedAmount) {
            token.safeTransferFrom(owner(), address(this), lockedAmount - balance);
        }
    }
```

`balance > _lockedAmount`일 때 되돌리는 것을 고려하십시오(두 값이 동일할 수 있도록 확인).

# [L-05] 동일한 추천인을 사용하는 경우 게임 생성을 되돌리지 않아야 함

`createGame()` 함수는 동일한 사용자가 이전에 사용한 것과 동일한 추천인을 사용할 때 되돌립니다.

이로 인해 사용자가 새 게임에 대해 동일한 유효한 추천 코드를 입력할 수 있지만 트랜잭션이 되돌려지므로 사용자 경험(UX)이 좋지 않습니다.

```solidity
    function setReferrer(address _referee, address _referrer) private {
        ...
        if (referrals[_referee] != address(0)) {
            revert ReferralAlreadySet(_referee, referrals[_referee]);
        }

        referrals[_referee] = _referrer;
    }
```

동일한 추천인을 사용할 때 추가 검사를 피하는 것을 고려하십시오:

```diff
    function setReferrer(address _referee, address _referrer) private {
-       if (_referrer == address(0)) {
+       if (_referrer == address(0) || referrals[_referee] == _referrer) {
            return;
        }
```

# [L-06] getGuessRate에서 0으로 나누기

사용자가 이미 공개된 카드를 선택하면 `remainingSelectedCard`가 0이 될 수 있습니다. 예를 들어 모든 에이스가 공개된 경우 에이스를 선택하는 경우입니다. 이로 인해 `rate`를 계산할 때 0으로 나누기가 발생합니다:

```solidity
    function getGuessRate(uint256 _numbers) public view returns (UD60x18 rate) {
        uint256 remainingCards = 52 - cardsRevealed;
        uint256 remainingSelectedCard = 0;

        for (uint256 i = 0; i < 13; ++i) {
            if ((_numbers & (1 << i)) > 0) {
                remainingSelectedCard += 4 - revealedCardNumbersCount[i];
            }
        }

        rate = ud(remainingCards).div(ud(remainingSelectedCard));
    }
```

`prb` 라이브러리가 그러하듯이 트랜잭션은 여전히 되돌려지지만, 그럼에도 불구하고 다음과 같은 검사로 0으로 나누기를 방지하는 것이 좋습니다.

```diff
+   require(remainingSelectedCard > 0);
```

# [L-07] 존재하지 않는 카드 인덱스에 대한 무료 카드 공개 요청

카드 인덱스 범위는 0에서 51까지이지만 51보다 큰 값에 대해 카드 공개를 요청할 수 있습니다.

`CardStatus.SECRETED`에 해당하는 `uint8`은 0이므로 존재하지 않는 카드의 `_card.status`는 항상 0이 되어 `CardIsNotSecret` 오류가 트리거 되지 않습니다.

이로 인해 운영자가 처리를 시도할 `RevealFreeCardRequested`가 방출되어 오프체인 오류나 불필요한 계산이 발생할 수 있습니다.

```solidity
    function requestFreeRevealCard(uint256 _index) external onlyPlayer onlyInitialized notExpired {
        Card storage _card = _cards[_index];

        if (cardsFreeRevealedRequests == maxFreeReveals) {
            revert MaximumFreeRevealsRequested();
        }

@>      if (_card.status != CardStatus.SECRETED) {
            revert CardIsNotSecret(_index);
        }

        ++cardsFreeRevealedRequests;
        _card.requestedAt = block.timestamp;
        _card.status = CardStatus.FREE_REVEAL_REQUESTED;

        emit RevealFreeCardRequested(_index, block.timestamp);
    }
```

인덱스가 예상 범위 내에 있는지 확인하는 것을 고려하십시오:

```diff
+   if (_index > 51) {
+       revert InvalidGameIndex();
+   }
```

# [L-08] `gameCreatedAt`은 초기화 중에 설정되어야 함

각 게임 계약에는 지정된 기간이 있습니다. 예를 들어 기간이 12시간으로 설정된 경우 해당 기간 후에는 게임을 플레이할 수 없습니다. 이 제약 조건은 다음과 같이 시행됩니다:

```solidity
    modifier notExpired() {
        if (gameDuration + gameCreatedAt < block.timestamp) {
            revert GameExpired();
        }

        _;
    }
```

- `gameDuration`은 게임의 길이를 나타냅니다.
- `gameCreatedAt`은 게임이 배포된 타임스탬프입니다.

게임은 배포 직후에 플레이할 수 없습니다. 플레이어는 운영자가 게임을 초기화할 때까지 기다려야 합니다. 문제는 운영자가 EOA이며 생성된 모든 게임을 초기화하는 데 시간이 걸릴 수 있다는 것입니다. 예를 들어 밥이 오전 6시에 게임을 생성하면 gameCreatedAt은 해당 현재 타임스탬프로 설정됩니다. 운영자가 밥의 게임을 초기화하는 데 2시간이 걸리는 경우(운영자에 대한 인센티브가 없으므로 가능성 있음), 밥의 게임에 대한 실제 플레이 가능 기간은 의도한 12시간 대신 10시간이 됩니다.

배포 시 대신 초기화 중에 gameCreatedAt을 설정하는 것을 고려하십시오.

# [L-09] `claimableAfter`가 지난 후 `revealCard()` 호출

플레이어가 카드의 값을 추측하면 운영자는 카드의 번호와 솔트(salt)를 게임 계약으로 보낼 책임이 있습니다. 운영자는 `revealCard()`를 호출할 수 있는 6시간의 기간을 갖습니다. 운영자가 이를 수행하지 못하면 승자는 `claimWin()` 함수를 사용하여 배팅을 청구할 수 있습니다:

```solidity
    function claimWin(uint256 _index) external onlyPlayer onlyInitialized {
        Card storage _card = _cards[_index];
        if (_card.status != CardStatus.GUESSED) {
            revert CardIsNotGuessed(_index);
        }
        if (_card.requestedAt + claimableAfter > block.timestamp) {
            revert NotYetTimeToClaim(_index);
        }
        _card.status = CardStatus.CLAIMED;
        vault.transferPlayerRewards(
            gameId, _card.betAmount, _card.totalAmount, _card.houseEdgeAmount, true, !SHOULD_RECEIVE_REWARDS
        );
        emit CardClaimed(_index, block.timestamp);
    }

```

보시다시피 승자는 `claimableAfter` 기간이 경과한 후에만 이 함수를 호출할 수 있습니다. 그러나 운영자가 `claimableAfter`가 지난 후 `revealCard()`를 호출하는 것을 방지하는 안전장치가 없으며 이는 문서와 모순됩니다:

> 보시다시피 플레이어는 조작될 위험이 없으며 보안 및 무결성에 대한 모든 책임은 운영자의 어깨에 있습니다.

예를 들어 운영자가 `claimableAfter` 이전에 솔트와 카드 번호를 제출하지 못하고 승자가 `claimableAfter` 직후에 승리를 청구할 수 없는 경우, 운영자는 여전히 `revealCard()`를 호출할 수 있습니다.

이 문제를 해결하려면 `revealCard()` 함수에 다음 확인을 추가하는 것을 고려하십시오:

```solidity
if (_card.requestedAt + claimableAfter <= block.timestamp) revert();
```

# [L-10] `Game.initialize()` 고유 해시 확인

`Game.initialize()`를 통해 제공된 해시는 고유할 것으로 예상됩니다. 그러나 확인되지 않습니다.
동일한 해시를 가진 두 장의 카드가 있어서는 안 됩니다. 이는 공개자가 동일한 번호로 두 장의 카드를 등록했음을 의미합니다. 이는 게임에서 사용되는 중요한 확률 가정을 깨뜨릴 것입니다. 예: 해시가 한 번 공개되면 숫자를 완벽하게 추측할 수 있습니다.

권장 사항을 여기에 설명하십시오

# [L-11] 데드라인을 현재 타임스탬프로 설정

`exactInputSingle()` 스왑 작업의 `deadline` 매개변수는 [장기 대기 트랜잭션 및 가격의 급격한 변동으로부터 보호](https://docs.uniswap.org/contracts/v3/guides/swaps/single-swaps#swap-input-parameters)하는 데 사용됩니다.

데드라인이 `block.timestamp`로 설정되면 노드에서 트랜잭션이 실행되는 시간은 항상 `block.timestamp`가 되므로 작업은 항상 성공합니다.

장기 대기 트랜잭션의 경우 `amountOut`이 가장 유리한 결과를 가져오지 않을 수 있습니다. 이는 `Burner` 계약이 `burnTokens()`를 통해 소각할 MONT를 위해 USDT를 스왑 할 때 영향을 미칩니다:

```solidity
    function _swap(uint256 _amountIn, uint256 _amountOutMinimum) private returns (uint256 amountOut) {
        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter.ExactInputSingleParams({
            fee: uniswapPoolFee,
            amountIn: _amountIn,
            tokenIn: address(usdt),
            tokenOut: address(mont),
            recipient: address(this),
@>          deadline: block.timestamp,
            amountOutMinimum: _amountOutMinimum,
            sqrtPriceLimitX96: 0
        });

        amountOut = swapRouter.exactInputSingle(params);
    }
```

호출자가 설정한 `_deadline` 매개변수를 `Burner::burnTokens()`에 추가하고 이를 `_swap()` 함수에 전달하십시오:

```diff
-   function _swap(uint256 _amountIn, uint256 _amountOutMinimum) private returns (uint256 amountOut) {
+   function _swap(uint256 _amountIn, uint256 _amountOutMinimum, uint256 _deadline) private returns (uint256 amountOut) {
        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter.ExactInputSingleParams({
            fee: uniswapPoolFee,
            amountIn: _amountIn,
            tokenIn: address(usdt),
            tokenOut: address(mont),
            recipient: address(this),
-           deadline: block.timestamp,
+           deadline: _deadline,
            amountOutMinimum: _amountOutMinimum,
            sqrtPriceLimitX96: 0
        });

        amountOut = swapRouter.exactInputSingle(params);
    }
```

# [L-12] MONT 보상이 잘못 곱해짐

`transferPlayerRewards()` 함수는 플레이어에게 보상을 분배하기 위한 것입니다. 처음에는 비 추천 프로그램 사용자에 대한 보상을 계산하기 위해 보상에 0.8을 곱합니다:

```solidity
reward = ((houseFeeFixedPoint * 8) / 10) / price;
```

이 단계에서 `reward` 변수는 이미 20% 감소했습니다. 그러나 `transferPlayerRewards()` 함수 내에서 사용자가 추천 프로그램의 일부가 아닌 경우 이 보상은 20% 더 감소합니다:

```solidity
        if (!isReferrerSet) {
            reward = (reward * 8) / 10;
        } else if (isReferrerSet) {
            reward = (reward * 9) / 10;

            balances[referrer] += reward / 10;
        }
```

결과적으로 비 추천 및 추천 사용자 모두에 대한 보상이 고갈되고 있습니다. 다음 예를 고려하십시오(단순화를 위해 가격 제외):

- `houseFeeFixedPoint = 200e18`
- `reward = (200e18 * 8) / 10 = 160e18`
- 비 추천 사용자의 경우:
  - `reward = (reward * 8) / 10 = 128e18`
- 추천 사용자의 경우:
  - `reward = (reward * 9) / 10 = 144e18`

위에 표시된 대로 비 추천 사용자는 초기 보상의 64%(0.64)를 받고 추천 사용자는 각각 예상되는 80%(0.8) 및 90%(0.9) 대신 72%(0.72)를 받습니다.

이 문제를 해결하려면 보상의 다중 감소를 방지하도록 `transferPlayerRewards()` 함수를 업데이트하십시오:

```solidity
    function transferPlayerRewards(
        uint256 _betAmount,
        uint256 _totalAmount,
        uint256 _houseEdgeAmount,
        address _player,
        bool _isPlayerWinner
    ) external returns (uint256 reward) {
        if (msg.sender != vault) {
            revert Unauthorized();
        }
        uint256 houseFee = calculateHouseFee(_betAmount, _houseEdgeAmount, _isPlayerWinner);
        uint256 price = getMontPrice();
        reward = (houseFee * 1e18) / price;
        if (reward > _totalAmount) {
            reward = _totalAmount;
        }
        (bool isReferrerSet, address referrer) = checkReferrer(_player);
        if (!isReferrerSet) {
            reward = (reward * 8) / 10;
        } else if (isReferrerSet) {
            reward = (reward * 9) / 10;
            balances[referrer] += reward / 10;
        }
        balances[_player] += reward;
        emit MontRewardAssigned(_player, reward);
    }
```
