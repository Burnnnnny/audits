# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**play-turbo/coinflip-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Coinflip 소개 (About Coinflip)

Coinflip은 스테이킹 풀과 결합된 탈중앙화 동전 던지기 게임으로, 사용자가 게임에 참여하고 스테이킹을 통해 보상을 얻을 수 있도록 합니다. 이 프로젝트는 무작위성을 위해 VRF(Verifiable Random Function)를 사용하며 여러 체인에 배포할 계획입니다.

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

_검토 커밋 해시_ - [e8bc317e5cb0a7b27c91d23fc47c83b4524bfa8e](https://github.com/play-turbo/coinflip-contracts/tree/e8bc317e5cb0a7b27c91d23fc47c83b4524bfa8e)

_수정 검토 커밋 해시_ - [a3b2f1e75eed8c76aa3c4e03211540eb9b2aca99](https://github.com/play-turbo/coinflip-contracts/tree/a3b2f1e75eed8c76aa3c4e03211540eb9b2aca99)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Flip`
- `Staking`

# 발견 사항 (Findings)

# [C-01] `flip()`에서 `Staking` 유동성 확인이 다른 게임 계약을 고려하지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`Flip.flip()`은 승리 시 플레이어의 보상을 커버할 수 있는 충분한 자금이 `Staking` 계약에 있는지 확인합니다. 그러나 다른 게임 계약들도 `Staking` 계약에 자금을 잠글 수 있다는 점이 고려되지 않았습니다.

결과적으로 모든 게임 계약에 걸쳐 잠긴 총 유동성이 `Staking` 계약의 총 잔액을 초과할 수 있으며, 이는 `Staking` 계약이 승리 시 모든 보상을 커버할 수 없게 만들 수 있습니다.

## 권장 사항

`flip()` 함수에서 `Staking` 계약 잔액이 현재 게임 계약의 잠긴 유동성뿐만 아니라 모든 게임 계약의 잠긴 유동성을 커버하기에 충분한지 확인하십시오.

# [C-02] 플레이어가 승리할 때 베팅 금액이 `Flip` 계약에 잠긴 상태로 유지됨

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

게임이 완료되고 플레이어가 승리하면 베팅 금액(수수료)의 일부가 관리자 및/또는 스테이킹 계약으로 전송됩니다. 그러나 베팅 금액의 나머지는 영원히 잠긴 상태로 유지됩니다.

이는 게임에서 이길 때마다 프로토콜이 `netPayout` 토큰을 잃는 반면, 게임에서 질 때마다 프로토콜이 `betAmount` 토큰을 얻는다는 것을 의미합니다.

매개변수가 프로토콜에 최대의 수익성을 제공하도록 설정된 시나리오를 고려하면 다음과 같습니다:

- feePercentage = 10%
- managerWinFeePercentage = 0%
- liquidityEdge = 0%

플레이어가 1번의 뒤집기 중 1번의 앞면에 대해 100 토큰을 베팅하므로 승리 확률은 50%입니다.

- 플레이어가 승리하면 Staking 계약은 플레이어에게 190 토큰을 보내고 10 토큰(수수료)은 Flip에서 Staking 계약으로 이체되며 90 토큰은 Flip 계약에 잠깁니다. 프로토콜의 결과는 180 토큰의 순손실입니다.
- 플레이어가 패배하면 프로토콜은 플레이어 베팅에서 100 토큰을 얻습니다.

보시다시피 프로토콜은 장기적으로 지속 가능하지 않습니다.

## 권장 사항

```diff
        uint256 managerFee = (fee * managerWinFeePercentage) / 10000;
-       uint256 stakingFee = fee - managerFee;
+       uint256 stakingAmount = game.betAmount - managerFee;

        if (managerFee > 0) {
            game.token.safeTransfer(manager, managerFee);
        }
-       if (stakingFee > 0) {
-           game.token.safeTransfer(address(stakingContract), stakingFee);
-       }
+       game.token.safeTransfer(address(stakingContract), stakingAmount);
```

# [H-01] Staking.earned가 보상을 잘못 계산함

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`Staking.giveReward()`는 소유자나 관리자가 사용자에게 보상을 분배하기 위해 호출합니다. 이 함수는 사용자가 적자인 경우에도 사용자에 대한 보상을 계산하는 `earned()`를 호출합니다. 계산은 다음과 같습니다:

```
 function earned(address user, address token) public view returns (uint256) {
        require(acceptedTokens[token], "Token not supported");
        TokenInfo storage info = tokenInfo[token];
        if (info.totalStaked == 0) {
            return 0;
        }
        uint256 totalTokenBalance = IERC20(token).balanceOf(address(this));

        // Multiply by 1e18 first to maintain precision
>       uint256 userShare = (stakedBalances[token][user] * 1e18 / info.totalStaked) * totalTokenBalance / 1e18;

        // If contract has lost balance, return user share of remaining balance
        if (totalTokenBalance < info.totalStaked) {
            return userShare;
        }

        // Otherwise return the difference between user's share and their staked balance
        return userShare > stakedBalances[token][user] ? userShare - stakedBalances[token][user] : 0;
    }
```

`earned()`는 totalToken 잔액, 사용자의 스테이킹 잔액 및 totalStaked를 사용하여 보상을 계산합니다.

계약에 100개의 토큰이 있고 100개가 스테이킹되어 있다고 가정해 보겠습니다. 앨리스는 10개의 토큰을 스테이킹합니다. 6자리 소수점 토큰을 가정합니다.

여기서 두 가지 시나리오가 있습니다. 첫째, 스테이킹 계약이 토큰을 얻습니다(사용자가 게임에서 지고 베팅 금액이 스테이킹 계약으로 유입됨).

이제 계약에는 120개의 토큰이 있고 totalStaked는 100개입니다.

앨리스의 userShare는 이제 12e6입니다.

```
userShare = (stakedBalances[token][user] * 1e18 / info.totalStaked) * totalTokenBalance / 1e18;
userShare = 10e6 * 1e18 / 100e18 * 120e18 / 1e18 = 12e6
```

`totalTokenBalance (120) > totalStaked (100)`이므로 `earned()`는 2e6을 반환합니다. 이 시나리오에서 앨리스의 보상은 2e6입니다.

```
return userShare > stakedBalances[token][user] ? userShare - stakedBalances[token][user] : 0;
return 12e6 > 10e6 ? 12 - 10 = 2
```

이제 시나리오가 뒤집혀서 계약에 80개의 토큰이 있고 totalStaked가 100개라고 가정해 보겠습니다.

앨리스의 userShare는 이제 8e6입니다.

```
userShare = (stakedBalances[token][user] * 1e18 / info.totalStaked) * totalTokenBalance / 1e18;
userShare = 10e6 * 1e18 / 100e18 * 80e18 / 1e18 = 8e6
```

`totalTokenBalance (80) < totalStaked (100)`이므로 `userShare`인 8e6을 반환합니다.

스테이킹 계약은 토큰을 잃었지만 앨리스의 보상은 이제 8e6입니다.

## 권장 사항

`giveReward()`가 정확히 무엇을 하는지 또는 액세스가 제어되기 때문에 언제 호출되는지 확실하지 않지만 `giveReward()`가 호출되면 스테이킹 계약이 돈을 잃었을 때도 사용자에게 보상합니다.

`totalTokenBalance < info.totalStaked`인 경우 0을 반환하는 것을 고려하십시오.

```
        // If contract has lost balance, return user share of remaining balance
        if (totalTokenBalance < info.totalStaked) {
            return 0;
        }
```

# [H-02] `giveReward()`가 스테이킹 잔액을 업데이트하지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`Staking` 계약에서 `giveReward()` 함수는 소유자나 관리자가 스테이커의 이익을 분배하기 위해 호출할 수 있습니다. 그러나 이 함수는 스테이커의 스테이킹 잔액과 총 스테이킹 금액을 업데이트하지 않습니다. 결과적으로 다른 스테이커 보상의 일부가 보상이 주어지는 스테이커에게 분배됩니다.

## 권장 사항

`giveReward()` 함수에서 스테이커의 스테이킹 잔액과 총 스테이킹 금액을 업데이트하십시오.

# [H-03] 사용자가 잠재적으로 다른 사용자의 스테이킹 보상을 훔칠 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

사용자가 `Staking.stake()`를 호출할 때 사용자의 `stakedBalance`는 예치된 금액과 같습니다.

```
function stake(address token, uint256 amount) external nonReentrant {
        require(acceptedTokens[token], "Token not supported");
        require(amount > 0, "Cannot stake 0");

        TokenInfo storage info = tokenInfo[token];
        info.totalStaked += amount;
>       stakedBalances[token][msg.sender] += amount;
        lastStakeTime[token][msg.sender] = block.timestamp;
>       require(IERC20(token).transferFrom(msg.sender, address(this), amount), "Stake transfer failed");
        emit Staked(msg.sender, token, amount);
    }
```

사용자가 `unstake()`할 때 `userShares`를 계산하여 `stakedBalance`를 뺍니다.

```
 uint256 totalTokenBalance = IERC20(token).balanceOf(address(this));

>       uint256 userShare = (userStaked * totalTokenBalance) / info.totalStaked;
        require(amount <= userShare, "Cannot unstake more than share");
        uint256 fraction = (amount * 1e18) / userShare;

        // Decrease user's staked balance proportionally
>       uint256 sharesToBurn = (userStaked * fraction) / 1e18;
>       stakedBalances[token][msg.sender] -= sharesToBurn;
>       info.totalStaked -= sharesToBurn;

        // Transfer amount to user
        require(IERC20(token).transfer(msg.sender, amount), "Unstake transfer failed");
```

예를 들어 앨리스가 100 USDC를 예금하면 그녀의 `stakedBalance`는 100이 되고 총 계약 잔액은 100이 됩니다. USDC의 총 계약 잔액이 200으로 증가한다고 가정하면 앨리스가 `unstake()`를 호출할 때 그녀의 `userShare`가 200e6이 되므로 200 USDC를 돌려받게 됩니다.

```
Alice calculation
userShare = 100e6 * 200e6 / 100e6 = 200e6
amount = 200e6
fraction = 200e6 * 1e18 / 200e6 = 1e18
sharesToBurn = 100e6 * 1e18 / 1e18 = 100e6
stakedBalance = 100e6 - 100e6 = 0
totalStaked = 100e6 - 100e6 = 0

amount transferred = 200e6
```

그러나 앨리스가 언스테이킹하지 않기로 결정하고 계약 잔액이 200으로 증가했을 때 누군가 `stake()`를 호출하면 앨리스의 보상을 훔칠 수 있습니다.

totalStaked가 현재 100e6이고 총 USDC 잔액이 현재 200e6이라고 가정해 보겠습니다. 밥은 400 USDC를 스테이킹하기로 결정합니다. 앨리스가 `unstake()`를 호출하지 않거나 언스테이킹 쿨타임이 짧다고 가정하면 밥이 언스테이킹을 호출할 때 앨리스의 보상을 받을 수 있습니다. 밥이 400 USDC를 스테이킹할 때 밥의 stakedBalance = 400e6, totalStaked = 500e6, 총 USDC 잔액은 600e6입니다.

```
Bob's calculation
userShare = 400e6 * 600e6 / 500e6 = 480e6
amount = 480e6
fraction = 1e18
sharesToBurn = 400e6 * 1e18 / 1e18 = 400e6
stakedBalance = 400e6 - 400e6 = 0
totalStaked = 500e6 - 400e6 = 100e6

amount transferred = 480e6
```

밥은 400e6 스테이킹에서 480e6을 얻고 앨리스 스테이킹은 이제 200e6 대신 120e6의 가치가 있습니다.

## 권장 사항

스테이킹할 때 나중의 스테이커가 이전 스테이커의 보상을 얻지 못하도록 `userShare`도 계산하는 것을 고려하십시오.

한 가지 예는 밥이 400e6 USDC를 스테이킹할 때 비율이 2:1(totalTokenBalance / totalStakedBalance)이므로 밥은 200 stakedBalance만 받아야 한다는 것입니다.

밥이 언스테이킹할 때 그의 `userShare`는 `200e6 * 600e6 / 300e6 = 400e6`이 됩니다.

# [M-01] 지연된 트랜잭션 실행으로 인한 수수료 증가 가능성

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Flip.sol` 계약에서 `PendingGame` 구조체는 아래와 같이 뒤집기(flip) 함수 호출 시점에 `gameStartTime`이 `block.timestamp`로 설정되어 초기화됩니다.

```solidity
        pendingGames[gameId] = PendingGame({
            player: msg.sender,
            betAmount: betAmount,
            numberOfCoins: numberOfCoins,
            headsRequired: headsRequired,
            netPayout: netPayout,
            token: token,
            gameStartTime: block.timestamp // Record game start time
        });
```

이로 인해 `flip transaction`이 멤풀에 갇혀 있다가 나중에, 잠재적으로 소유자가 `feePercentage`를 인상한 후에 실행될 수 있는 취약점이 생성됩니다. 결과적으로 플레이어는 당첨금에 대해 초기에 예상했던 것보다 `더 높은 수수료`를 부담하게 되어 순지급액에 영향을 미칠 수 있습니다. 이 상황은 플레이어의 통제 범위를 벗어나며, 수수료가 더 높을 것이라는 것을 알았다면 게임에 참여하지 않았을 시나리오로 이어질 수 있습니다.

당첨금 및 수수료의 실제 계산은 `calculatePayout` 함수에서 발생합니다. 수수료는 `feePercentage`를 사용하여 계산되며 아래와 같이 `grossPayout`에서 차감되어 `netPayout`을 계산합니다.

```solidity
        fee = (betAmount * feePercentage) / 10000;
        netPayout = grossPayout - fee;
```

## 권장 사항

`flip function`에 `deadline parameter`를 도입하십시오. 이 매개변수는 트랜잭션이 실행될 수 있는 가장 늦은 허용 가능한 `block timestamp`를 지정해야 합니다. `이 마감일 이후에 트랜잭션이 실행되면` 되돌려야(revert) 합니다. 이를 통해 플레이어는 게임 플레이를 통제하고 트랜잭션을 시작할 때의 계약 상태에 따른 위험과 결과를 수용할 수 있으며 예상치 못한 수수료 인상 및 기타 상태 변경을 방지할 수 있습니다.
# [M-02] 게임 시작 후 수수료 비율 변경으로 지급이 차단될 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`Flip.sol`에서 `flip()` 함수는 `netPayout`을 계산하고 계약이 승자에게 지급할 충분한 자금을 확보하기 위해 금액을 잠급니다.

그러나 게임이 완료되었을 때 플레이어가 승리하면 `netPayout`이 다시 계산됩니다.

```solidity
230:         if (won) {
231:             (uint256 grossPayout, uint256 netPayout, uint256 fee) =
232:                 calculatePayout(game.betAmount, game.numberOfCoins, game.headsRequired);
```

`feePercentage`는 `netPayout` 계산에 사용되므로 게임이 시작된 후 소유자가 수수료 비율을 변경하면 `netPayout`이 새로운 수수료 비율로 계산되어 새로운 `netPayout`이 `flip()` 함수에서 계산된 금액과 다를 수 있습니다.

`feePercentage`가 증가하면 사용자는 예상보다 적은 지급액을 받게 됩니다.
`feePercentage`가 감소하면 새로운 `netPayout`이 잠긴 유동성을 초과하여 트랜잭션이 되돌려질(revert) 수 있습니다.

## 권장 사항

`flip()` 함수의 `PendingGame` 구조체에 `fee`를 추가하고 `_completeGame()` 함수에서 다시 계산하는 대신 `netPayout` 및 `fee`의 저장된 값을 사용하십시오.

# [M-03] `PythRandomnessProvider`는 항상 기본 공급자(default provider)를 사용함

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`PythRandomnessProvider` 계약을 통해 소유자는 엔트로피에 대한 특정 공급자를 설정할 수 있습니다.

```solidity
    function setProvider(address _provider) external onlyOwner {
        require(_provider != address(0), "Zero address not allowed");
        provider = _provider;
        emit ProviderUpdated(_provider);
    }
```

그러나 `provider` 변수는 무시되며 `entropy` 계약과의 상호 작용에서 항상 기본 공급자가 사용됩니다. 결과적으로 소유자는 엔트로피를 생성하는 데 사용되는 공급자를 변경할 수 없습니다.

## 권장 사항

`entropy.getDefaultProvider()` 대신 `provider` 변수의 값이 address(0)과 다를 때 `provider` 변수를 사용하십시오.
또한 `sequenceNumberToInfo` 매핑을 새로운 `providerToSequenceNumberToInfo` 매핑으로 교체하십시오.

# [M-04] 유동성이 당첨금보다 적으면 플레이어가 돈을 받지 못할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`flip()`이 호출되면 함수는 게임의 잠재적 당첨금을 지불할 수 있는 유동성이 있는지 확인합니다.

```
        // require staked amount > maximum payout using calculatePayout
        (uint256 grossPayout, uint256 netPayout,) = calculatePayout(betAmount, numberOfCoins, headsRequired); // TODO check use grossPayout?

        uint256 stakingBalance = IERC20(token).balanceOf(address(stakingContract));
        uint256 availableLiquidity = (stakingBalance * maxLiquidityPercentage) / 10000;
        require(lockedLiquidity + netPayout <= availableLiquidity, "Not enough liquidity for new bet");

        lockedLiquidity += netPayout;
```

예를 들어 remix에서 테스트했을 때 1 USD에 대한 20 코인 20 앞면 게임은 1,048,575.98 (1백만) USDC의 지급금을 산출합니다. 함수는 `availableLiquidity`가 있는지 확인한 다음 총 `lockedLiquidity`를 확인하고 스테이킹 계약이 해당 금액을 지불할 수 있는지 확인합니다.

고래가 스테이킹 계약에 110만 USDC를 예금한다고 가정해 보겠습니다. 2일 후(언스테이킹 쿨타임 기간), 플레이어가 `flip(1e6,20,20,USDC)`를 호출합니다. `flip()` 호출이 실행되고 플레이어는 pyth 엔트로피 콜백을 기다립니다.

안타깝게도 고래는 정확히 이 순간에 출금하기로 결정하고 엔트로피 콜백은 출금 후에 이루어집니다. 플레이어는 실제로 게임에서 이겨 1백만 이상의 USDC를 얻었지만 스테이킹 계약에 더 이상 유동성이 충분하지 않아 출금할 수 없습니다.

## 권장 사항

스테이킹 계약은 `lockedLiquidity`를 고려해야 합니다. 사용자가 언스테이킹하려고 할 때 스테이킹 계약의 총 잔액이 `lockedLiquidity` 아래로 떨어지면 사용자는 언스테이킹할 수 없어야 합니다.

# [M-05] 스테이커들이 게임 완료를 프론트러닝(front-run)할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

프로토콜의 스테이커들은 플레이어가 내기에서 지면 이익을 얻고 플레이어가 이기면 손실을 입습니다.

사용자는 잃은 게임의 완료를 프론트러닝하여 토큰을 스테이킹함으로써 미실현 이익을 얻을 수 있습니다. 스테이커는 언스테이킹하기 위해 쿨타임 기간을 기다려야 하고 그 기간 동안 미실현 이익을 잃을 수 있지만 장기적으로 이 과정을 반복하는 것은 통계적으로 수익성이 있습니다.

다음 시나리오를 고려하십시오:

- `Staking` 계약에 100 토큰이 예금되어 있음

- 앨리스가 동전 던지기에 50 토큰을 

- 다음 블록에서 밥은 멤풀에서 `PythRandomnessProvider.entropyCallback`이 호출되는 트랜잭션을 보고 난수가 앨리스를 지게 할 것임을 확인

- 밥은 트랜잭션을 프론트러닝하여 10,000 토큰을 스테이킹

- 게임이 완료되고 앨리스의 베팅 금액이 `Staking` 계약으로 이체됨

- 밥의 스테이킹은 이제 10,049 토큰의 가치가 있음

- 밥은 지는 게임을 감지할 때마다 이 과정을 반복

같은 방식으로 쿨타임 기간이 지난 스테이커는 멤풀을 추적하고 플레이어가 이기는 게임이 완료되기 전에 언스테이킹하여 손실을 피할 수 있습니다.

## 권장 사항

이 문제에 대한 간단한 해결책은 없습니다. 가능한 완화 조치는 사용자가 토큰을 스테이킹한 후 `n` 블록이 지난 후에만 새로운 스테이킹 금액이 `info.totalStaked` 및 `stakedBalances[token][msg.sender]`에 추가되도록 하는 것입니다. 언스테이킹의 경우 요청과 실제 언스테이킹 프로세스 사이에 지연을 추가해야 합니다. 그러나 이러한 구현은 스테이커에 대한 서비스 거부를 피하기 위해 특별한 주의가 필요합니다.

# [L-01] `requestRandomness` 호출 후 초과 `msg.value`가 플레이어에게 전송되지 않음

`pythRandomnessProvider::requestRandomness` 함수는 플레이어가 게임에서 이겼는지 계산하기 위해 `난수`를 검색하기 위해 `Flip::flip` 계약에 의해 호출됩니다. `requestRandomness` 함수는 아래와 같이 `기본 공급자에 대한 각각의 수수료 금액`으로 `entropy::requestWithCallback` 함수를 호출합니다.

```solidity

        uint64 sequenceNumber = entropy.requestWithCallback{value: msg.value}(entropyProvider, userRandomNumber);

```

`feeAmount`는 아래와 같이 `Flip::flip`을 통해 이 호출로 전달됩니다.

```solidity

            uint256 fee = randomnessProvider.getFee();

            require(msg.value >= fee, “Insufficient fee for randomness”);

```

여기서 문제는 `randomnessProvider.getFee()`에 의해 반환된 수수료 금액이 pyth 문서에 언급된 대로 온체인 가스 가격을 기준으로 변경된다는 것입니다.

> 다음 표는 기본 공급자를 사용할 때 지불해야 하는 총 수수료를 보여줍니다. 아래 표시된 수수료는 각 체인의 일반적인 가스 가격에 따라 시간에 따라 달라질 수 있습니다.

https://docs.pyth.network/entropy/current-fees

따라서 `flip transaction이 실행될 때` `기본 공급자 수수료가 감소`하면 초과 `msg.value`가 `Flip.sol` 계약에 갇히게 됩니다.

따라서 `randomnessProvider.requestRandomness` 호출이 이루어진 후 `초과 수수료 금액(msg.value - fee)`을 `플레이어(msg.sender)`에게 다시 전송하는 것이 좋습니다.

# [L-02] `fee-on-transfer` 토큰으로 Coinflip 게임이 작동하지 않음

`Coinflip 게임`의 `Flip.sol` 계약에는 게임 플레이의 핵심 로직이 있습니다. `Flip.sol` 계약에서 `acceptedTokens` 매핑은 `owner`가 베팅을 위해 토큰을 화이트리스트에 추가하는 데 사용됩니다.

사용자가 게임을 플레이할 때 `Flip::flip` 함수를 호출하여 아래와 같이 `msg.sender`에서 `AcceptedTokens`의 `betAmount`를 `Flip.sol` 계약으로 전송합니다.

```solidity
        token.safeTransferFrom(msg.sender, address(this), betAmount);
```

그리고 이 동일한 `betAmount`는 전송 수수료(사용된 토큰이 fee-on-transfer 토큰인 경우)를 고려하지 않고 `pendingGame`을 생성하는 데 사용됩니다. 그리고 동일한 `betAmount`가 `randomnessProvider.requestRandomness` 호출이 실패한 경우 사용자에게 환불하는 데 사용됩니다. 위의 두 로직은 아래와 같습니다.

```solidity
        pendingGames[gameId] = PendingGame({
            player: msg.sender,
            betAmount: betAmount,
            numberOfCoins: numberOfCoins,
            headsRequired: headsRequired,
            netPayout: netPayout,
            token: token,
            gameStartTime: block.timestamp // Record game start time
        });
```

```solidity
                token.safeTransfer(msg.sender, betAmount);
                revert(string.concat("Randomness request failed: ", reason));
```

토큰이 `fee-on-transfer`인 경우 계약으로 전송된 실제 금액은 `betAmount`보다 적으며 결과적으로 `pendingGame 생성` 및 `환불`에 `정확한 betAmount 사용`은 잘못된 것이며 이는 `자금 부족으로 인한 DoS` 또는 다른 사용자의 스테이킹 금액에서 `자금을 훔칠` 수 있습니다.

따라서 `Flip::flip` 함수에서 `betAmount` 전송 로직을 업데이트하여 계약으로 전송된 토큰의 올바른 잔액을 계산하는 것이 좋습니다. 이는 `토큰 전송` 중 `전후 잔액`을 가져와서 수행할 수 있습니다. `fee-on-transfer` 토큰이 `coinflip 게임에서 허용되지 않는 경우` 올바르게 문서화하는 것이 좋습니다.

# [L-03] `Staking`에서 승인된 토큰을 제거할 수 없음

`Staking` 계약의 소유자는 스테이킹을 위해 승인된 토큰을 추가할 수 있습니다. 그러나 제거할 방법은 없습니다. `Flip` 계약에서 승인된 토큰이 제거되면 `Staking` 계약은 새로운 보상을 생성하지 않더라도 토큰 스테이킹을 계속 수락합니다.

스테이킹을 위해 승인된 토큰을 제거하는 방법을 추가하는 것을 고려하십시오.

# [L-04] `earned()`에서 사용자 지분 계산 시 정밀도 손실

`Staking.earned()` 함수에서 사용자의 지분을 계산하기 위해 사용자의 스테이킹 잔액을 총 스테이킹 잔액으로 나눈 다음 총 토큰 잔액을 곱합니다. 이로 인해 계산 시 정밀도 손실이 발생할 수 있습니다.

```solidity
101:         // Multiply by 1e18 first to maintain precision
102:         uint256 userShare = (stakedBalances[token][user] * 1e18 / info.totalStaked) * totalTokenBalance / 1e18;
```

다음 예를 고려하십시오:

- 사용자의 스테이킹 잔액: 1e18
- 총 스테이킹 잔액: 2e18 + 1
- 총 토큰 잔액: 2e18 + 1
- stakedBalances[token][user] _ 1e18 / info.totalStaked = 1e18 _ 1e18 / (2e18 + 1) = 499999999999999999
- userShare = 499999999999999999 \* (2e18 + 1) / 1e18 = 999999999999999998

정밀도 손실로 인해 사용자 지분은 1e18 대신 999999999999999998로 계산됩니다.

정밀도 손실을 피하기 위해 사용자 지분 계산은 `unstake()` 함수에서 수행되는 것과 동일한 방식으로 수행되어야 합니다.

```diff
-  uint256 userShare = (stakedBalances[token][user] * 1e18 / info.totalStaked) * totalTokenBalance / 1e18;
+  uint256 userShare = stakedBalances[token][user] * totalTokenBalance / info.totalStaked;
```

# [L-05] `Flip`으로 전송된 네이티브 토큰이 계약에 갇힘

`Flip` 계약은 누구나 네이티브 토큰을 계약으로 보낼 수 있는 `receive()` 함수를 구현합니다. 그러나 계약에는 이러한 토큰을 인출하는 메커니즘이 없으므로 영원히 계약에 갇히게 됩니다.

이러한 네이티브 토큰을 사용하여 난수 공급자의 수수료를 지불하거나 `receive()` 함수를 제거하는 것을 고려하십시오.

# [L-06] `Stake.timeUntilUnstake()`는 사용자가 토큰을 스테이킹했는지 확인하지 않음

`Stake.timeUntilUnstake()`는 사용자가 토큰을 언스테이킹하기 전에 기다려야 하는 시간을 반환합니다. 사용자가 즉시 토큰을 언스테이킹할 수 있으면 함수는 0을 반환합니다. 그러나 사용자가 토큰을 스테이킹했는지 확인하지 않습니다. 사용자가 토큰을 스테이킹하지 않은 경우 `lastStakeTime`은 0이 되므로 쿨타임 기간이 지난 것으로 간주되어 함수는 0을 반환합니다.

사용자가 토큰을 스테이킹하지 않은 경우 트랜잭션을 되돌리는 것을 고려하십시오.

# [L-07] 다른 pyth entropy 요청에 대해 동일한 `userRandomNumber`가 사용될 수 있음

게임 ID는 `Flip.flip()`에서 다음과 같이 계산됩니다:

```solidity
    bytes32 gameIdBytes =
        keccak256(abi.encodePacked(msg.sender, block.timestamp, numberOfCoins, headsRequired, nonce++));
    string memory gameId = string(abi.encodePacked(gameIdBytes));
```

이 값은 `PythRandomnessProvider.requestRandomness()`에서 `entropy.requestWithCallback()` 호출로 전송될 사용자 난수를 계산하는 데 사용됩니다:

```solidity
    bytes32 userRandomNumber = keccak256(abi.encodePacked(gameId, block.timestamp));

    uint64 sequenceNumber = entropy.requestWithCallback{value: msg.value}(entropyProvider, userRandomNumber); // CHANGED FROM GETFEE
```

사용자가 동일한 블록에서 동일한 매개변수를 사용하여 다른 계약에서 여러 게임을 시작하는 경우 다른 요청에 대해 동일한 `userRandomNumber`가 사용될 가능성이 있습니다. 이 경우 악의적인 엔트로피 공급자가 난수 요청의 결과를 조작하기 더 쉽습니다.

이 문제를 방지하기 위해 `userRandomNumber` 계산에 게임 계약의 주소를 포함하는 것이 좋습니다.

```diff
-    bytes32 userRandomNumber = keccak256(abi.encodePacked(gameId, block.timestamp));
+    bytes32 userRandomNumber = keccak256(abi.encodePacked(gameId, block.timestamp, msg.sender));
```

# [L-08] `transfer` 및 `transferFrom` 사용으로 트랜잭션이 되돌려질(revert) 수 있음

`staking.sol` 계약에는 `IERC20::transferFrom` 및 `IERC20::transfer` 함수를 사용하여 `msg.sender`와 계약 자체 간에 `토큰`을 전송하는 여러 경우가 있습니다.

그러나 문제는 전송되는 `토큰`이 `bool`을 반환하지 않으면 `IERC20` 구현이 반환 값으로 `bool` 값을 기대하기 때문에 위의 전송 트랜잭션이 실패한다는 것입니다.

`동전 던지기` 게임은 소유자가 화이트리스트에 올린 여러 토큰으로 작동할 것으로 예상되므로 문제가 될 수 있습니다.

따라서 이러한 토큰이 게임에서 사용될 때 게임의 핵심 기능이 중단됩니다.

따라서 openzeppelin `SafeERC20` 라이브러리의 `safeTransfer` 및 `safeTransferFrom`을 사용하는 것이 좋습니다.

# [L-09] `Staking.unstake()`에서 소각된 지분이 과소평가될 수 있음

`Staking.unstake()` 함수에서 사용자를 위해 소각될 지분의 계산은 다음과 같이 수행됩니다:

```solidity
79:         uint256 userShare = (userStaked * totalTokenBalance) / info.totalStaked;
80:         require(amount <= userShare, "Cannot unstake more than share");
81:         uint256 fraction = (amount * 1e18) / userShare;
82:
83:         // Decrease user's staked balance proportionally
84:         uint256 sharesToBurn = (userStaked * fraction) / 1e18;
```

81행과 84행의 나눗셈 연산 결과는 나눗셈 연산의 잘라냄(truncation)으로 인해 내림되므로 사용자가 소각한 지분이 과소평가될 수 있습니다.

다음 예를 고려해 보겠습니다:

- amount = 1
- userStaked = 10
- totalStaked = 100
- totalTokenBalance = 110
- userShare = 10 \* 110 / 100 = 11
- fraction = 1 \* 1e18 / 11 = 90909090909090909
- sharesToBurn = 10 \* 90909090909090909 / 1e18 = 0

이 경우 사용자는 1 토큰을 인출할 수 있지만 소각된 지분은 0입니다.

토큰의 최소 단위는 일반적으로 무시할 수 있는 양이지만 소수점 자릿수가 적고 가치가 높은 토큰의 경우 그 양이 상당할 수 있습니다. 예를 들어 WBTC(8자리)의 최소 단위는 약 0.001 USD(현재 가격 기준)이고 GUSD(2자리)의 최소 단위는 0.01 USD입니다. 소수점 자릿수가 적고 가치가 높은 토큰을 사용하는 극단적인 경우 이 공격 벡터를 악용하여 스테이킹 풀을 고갈시킬 수 있습니다.

`fraction` 및 `sharesToBurn` 계산을 올림하고 최종 값을 사용자의 스테이킹 잔액으로 제한하십시오.

# [L-10] 블랙리스트 전송 엣지 케이스에서 entropyCallback이 실패할 수 있음

Pyth 엔트로피 [문서](https://docs.pyth.network/entropy/generate-random-numbers/evm)는 다음과 같이 명시합니다:

> entropyCallback 함수는 오류를 반환해서는 안 됩니다. 오류를 반환하면 키퍼가 콜백을 호출할 수 없습니다.

사용자가 보상을 받지 못하도록 블랙리스트에 올랐기 때문에 `entropyCallback` 함수가 되돌려지는 불행한 경우가 있을 수 있습니다.

RandomnessProvider 계약에서 `entropyCallback()`이 호출되면 `IGame(info.gameContract).completeGame(info.gameId, random);`을 호출하여 플레이어가 승리하면 토큰을 플레이어에게 전송합니다.

```
Flip.sol

            if (managerFee > 0) {
                game.token.safeTransfer(manager, managerFee);
            }
            if (stakingFee > 0) {
                game.token.safeTransfer(address(stakingContract), stakingFee);
            }

>           stakingContract.transferPayout(game.token, game.player, netPayout);

Staking.sol

function transferPayout(address token, address recipient, uint256 amount) external nonReentrant returns (bool) {
        require(authorizedGames[msg.sender] || msg.sender == owner(), "Caller not authorized");
        require(acceptedTokens[token], "Token not supported");
        require(amount > 0, "Cannot transfer 0");
>       require(IERC20(token).transfer(recipient, amount), "Payout transfer failed");
        return true;
    }
```

엣지 케이스에서 콜백을 기다리는 동안 사용자가 토큰에 의해 블랙리스트에 오르면 전송이 실패하므로 콜백이 실패하고 재시도할 수 없습니다.

예를 들어 플레이어가 USDC를 토큰으로 사용하여 `flip()`을 호출하고 즉시 블랙리스트에 오릅니다. `entropyCallback()` 동안 자금을 플레이어에게 전송할 수 없으며 `entropyCallback()`이 되돌려집니다.

이 경우 플레이어가 이겼으므로 최대한의 공정성을 보장하기 위해 재시도 메커니즘을 갖추는 것이 좋습니다. 플레이어가 블랙리스트가 해제된 후 `cancelGame()`을 호출하여 토큰을 회수할 수 있지만 플레이어에게는 공정하지 않을 것입니다.
