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

_검토 커밋 해시_ - [9849fa45d21a9b331164051805fa0d4e93dd85c6](https://github.com/play-turbo/coinflip-contracts/tree/9849fa45d21a9b331164051805fa0d4e93dd85c6)

_수정 검토 커밋 해시_ - [6e7bb0621911f07939ec0aeacacd6ac9b76190b4](https://github.com/play-turbo/coinflip-contracts/tree/6e7bb0621911f07939ec0aeacacd6ac9b76190b4)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Flip`
- `Staking`

# 발견 사항 (Findings)

# [C-01] 총 잔액에서 제외된 보류 중인 지급금으로 인해 잘못된 지분 계산 발생

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

각 게임이 완료된 후 프로토콜은 `Staking.transferPayout()`을 통해 승자의 지급금을 전송하려고 시도합니다. 이 전송이 실패하면 금액은 나중에 청구할 수 있도록 `pendingPayouts`에 저장됩니다. 그러나 계약은 잔액 계산에 이러한 보류 중인 지급금을 계속 포함하므로 계산에 사용되는 스테이킹 잔액이 과대평가됩니다.

```solidity
function transferPayout(address token, address recipient, uint256 amount) external {
    --- SNIPPED ---
    if (!callSucceeded) {
@<>     pendingPayouts[token][recipient] += amount;
        emit TransferFailed(token, recipient, amount);
        return false;
    }
    --- SNIPPED ---
}
```

문제는 계약이 총 잔액을 계산하는 방식에서 비롯됩니다. `pendingPayouts`를 빼지 않고 단순히 `IERC20(token).balanceOf(address(this))`를 사용합니다. 이 부풀려진 잔액은 `totalOwed()`, `lockLiquidity()`, `finalizeStake()` 및 `finalizeUnstake()`와 같은 여러 중요한 기능에 영향을 미칩니다.

다음 시나리오를 고려하십시오:

1. 초기 설정:

   - Alice는 1000 토큰(1000 지분)을 스테이킹했습니다.
   - 누군가 게임을 시작합니다:
     - `betAmount`: 100 토큰
     - `netPayout`: 200 토큰 (2:1 배당)
     - 프로토콜은 200 토큰을 잠급니다.

2. 게임이 종료되지만 승자의 지급금 200 토큰 전송이 실패합니다:
   - Staking 계약은 `pendingPayouts`에 200 토큰을 추가합니다.
   - 풀은 이제 1100 토큰(원래 1000 + `betAmount` 100)을 보유합니다.
   - 200 토큰은 잠긴 상태로 유지됩니다.

이제 Alice가 소유한 것으로 보이는 토큰을 분석해 보겠습니다:

- Alice의 지분은 1100 토큰(1000 지분 × 1100 / 1000)을 나타내지만 이는 발생한 손실을 고려하지 않습니다(Alice의 지분은 여전히 1:1의 가치가 있는 것으로 보임).
- 승자에게 200 토큰을 지불해야 합니다.
- 총 지불해야 할 토큰: 1300 토큰이지만 프로토콜에는 1100 토큰만 있습니다.

비교를 위해 지급이 정상적으로 이루어졌다면:

- 풀은 900 토큰(1000 - 200(`netPayout`) + 100(`betAmount`))을 갖게 됩니다.
- Alice의 지분은 최대 지급액에 대해 900 토큰(1000 지분 × 900 / 1000)을 정확하게 나타냅니다.

3. 이는 풀에 연쇄적인 영향을 미칩니다:
   - Alice는 유동성 잠금으로 인해 900 토큰을 인출하지만 잘못된 계산으로 인해 여전히 효과가 전파됩니다.
   - Alice는 900 지분을 소각하여 100 지분을 남기고 200 토큰 잔액(1100 - 900)을 초래합니다.
   - 승자는 자신의 200 토큰을 청구하여 토큰 잔액을 고갈시킵니다.
   - 결과적으로 나머지 100 지분은 더 이상 토큰 잔액으로 뒷받침되지 않습니다.

이로 인해 지분이 쓸모없어지고 새로운 예금이 즉시 평가 절하되므로 풀은 손실 상태가 됩니다. 또한 Alice는 남은 지분의 언스테이킹을 요청하고 새로운 예금을 기다려 본질적으로 그들의 스테이킹 일부를 훔칠 수 있습니다.

## 권장 사항

각 사용자의 총 보류 중인 지급금을 추적하는 변수를 도입하고 모든 잔액 계산 및 검증에서 보류 중인 지급금을 적절하게 설명하십시오.

또한 `lockedLiquidity`는 아직 손실이나 승리로 확정되지 않은 진행 중인 게임 준비금을 나타내므로 계산에 적절하게 사용되지 않습니다. 이는 게임이 빨리 확정될 것으로 예상되더라도 지분에 대한 과소평가를 초래할 수 있으므로 `lockedLiquidity`는 보류 중인 지급금의 합계와 동일한 것으로 취급될 수 있습니다. 진행 중인 게임 중에 언스테이크가 계산되는 경우도 있습니다.

# [C-02] 보류 중인 스테이크가 유동성 계산에 고려되지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`Staking` 계약은 `IERC20(token).balanceOf(address(this))`를 사용하여 지분 계산 및 유동성 검증을 위한 총 잔액을 결정합니다. 그러나 `Staking.requestStake()`에서 토큰은 보류 중인 스테이크를 추적하지 않고 스테이크 요청 시 즉시 계약으로 전송됩니다. 이는 지분 계산, 유동성 잠금 검증, 지불해야 할 금액 계산과 같은 많은 중요한 프로세스의 유동성에 포함됩니다.

```solidity
function requestStake(address token, uint256 amount) external nonReentrant {
    --- SNIPPED ---

    // Transfer the tokens from the user to this contract
@>  IERC20(token).safeTransferFrom(msg.sender, address(this), amount);

    --- SNIPPED ---
}
```

이는 계산 및 검증에 잘못된 토큰 잔액 사용을 유발하기 쉬워 보류 중인 스테이커의 자금 손실로 이어집니다.

아래의 가능한 시나리오 예를 고려하십시오:

0. 풀에 100 지분과 100 토큰의 풀 잔액을 가진 기존 스테이커가 한 명 있다고 가정합니다(이 사용자는 유동성의 100%를 소유합니다).

1. 이전 스테이커는 언스테이크를 요청하기로 결정했고 이미 쿨타임 기간을 지났습니다.

2. Alice가 100 토큰을 스테이킹하도록 요청하면 토큰이 스테이킹 계약으로 전송되고 지분 계산에서도 스테이크를 확정할 때 Alice에게 할당되어야 하는 요청된 금액이 그녀에게 사용 가능하다는 것을 고려하지 않고 토큰 계약 잔액을 계산에 사용합니다.

3. 이전 스테이커는 언스테이크 요청을 확정하고 200 토큰(100 + 100(Alice))을 돌려받습니다.

```solidity
function finalizeUnstake(address token) external nonReentrant {
    --- SNIPEPD ---

@>  uint256 totalBalance = IERC20(token).balanceOf(address(this));
    // The user’s pro-rata portion of the underlying tokens
@>  uint256 amountOwed = (sharesToRedeem * totalBalance) / totalShares;

    --- SNIPEPD ---
@>  IERC20(token).safeTransfer(msg.sender, amountOwed);

    emit Unstaked(msg.sender, token, amountOwed);
}
```

4. Alice는 100 지분을 갖고 확정하지만 0 토큰을 돌려받습니다.

스테이킹 풀에서 토큰 잔액의 과대평가를 사용하는 함수에는 다음과 같은 추가 경우도 있습니다: `finalizeStake()`, `finalizeUnstake()`, `totalOwed()` 및 `lockLiquidity()`.

## 권장 사항

보류 중인 스테이크를 추적하고 사용 가능한 유동성으로 처리되지 않도록 제외하십시오. 또한 스테이크가 확정될 때마다 보류 중인 스테이크를 업데이트하여 유동성 풀에 포함하고 보류 중인 상태에서 제거해야 합니다.

# [M-01] `Staking` 계약 변경 시 게임 환불 및 완료 문제

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Flip` 계약에서 `setStakingContract()` 함수를 통해 계약 소유자는 스테이킹 계약 주소를 변경할 수 있습니다. 그러나 보류 중인 게임이 있는 동안 스테이킹 계약을 변경하면 보류 중인 게임이 완료되지 않은 상태로 남게 됩니다.
또한 소유자는 `cancelGame()`을 호출하여 플레이어에게 환불할 수 없을 수도 있습니다. 새로운 스테이킹 계약에 불완전한 `game.netPayout`을 잠금 해제할 충분한 유동성이 없을 수 있기 때문입니다.

## 권장 사항

각 플레이어의 `PendingGame` 구조체에 게임 생성 시 스테이킹 계약 주소를 기록하고, 이 기록된 주소를 게임 완료 또는 취소 시 사용하는 것을 고려하십시오.

# [M-02] 동적 잔액 변경으로 인해 언스테이커가 불공정한 금액을 받음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Staking` 계약에서 사용자는 `finalizeUnstake()` 함수를 통해 언스테이크를 확정할 수 있으며, 여기서 사용 가능한 계약 잔액을 기준으로 `amountOwed`를 받게 됩니다. 그러나 게임 계약이 승자에게 지불하거나 게임 패자의 금액을 전송하기 위해 계약과 상호 작용할 때 사용 가능한 잔액이 동적으로 변경되기 때문에 문제가 발생합니다.
이로 인해 예상치 못한 양의 자산이 사용자에게 전송됩니다.
또한 이는 `unstakeDelay`를 통과한 사용자가 악용하여 `finalizeUnstake()` 트랜잭션을 프론트런하여 먼저 언스테이크를 확정함으로써 다른 언스테이커의 `amountOwed`를 줄일 수 있습니다.

```solidity
function finalizeUnstake(address token) external nonReentrant {
    //...
}
```

`finalizeStake()` 함수에서도 유사한 문제가 있습니다:

```solidity
function finalizeStake(address token) external nonReentrant {
    //...
}
```

## 권장 사항:

`finalizeUnstake()` 함수에 `minAmountOut` 매개변수(`finalizeStake()` 함수의 경우 `minShare`)를 추가하여 언스테이커가 허용 가능한 양의 자산을 받고 사용 가능한 계약 잔액의 동적 변경으로 인해 그들이 받아야 할 금액이 예상치 못하게 감소하는 것을 방지하십시오.

# [M-03] `depositDelay` 기간 동안 토큰이 제거되면 토큰이 잠김

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명:

`Staking.finalizeStake()` 함수는 **`depositDelay` 동안** 의도한 토큰이 `acceptedTokens` 목록에서 제거되더라도(`requestStake()`와 `finalizeStake()` 호출 사이에 의도한 토큰이 제거된 경우) 스테이킹을 완료할 수 있도록 합니다.
이로 인해 사용자가 승인되지 않은 토큰을 인출할 수 있을 때까지 최소 `depositDelay` + `unstakeDelay` 기간(현재 최소 총 **49시간** 동안 잠김) 동안 사용자의 토큰이 잠기게 됩니다.
또한 계약의 유동성이 `finalizeUnstake()`를 완료하기에 충분하지 않은 경우 사용자의 스테이크가 훨씬 더 오랜 기간 동안 잠길 수 있습니다.
이 시나리오는 사용자가 `Staking.finalizeStake()` 함수를 호출할 때 토큰이 `acceptedTokens`에서 제거되었는지 확인하지 않기 때문에 발생할 수 있습니다.

## 권장 사항:

`finalizeStake()` 함수를 업데이트하여 토큰이 `acceptedTokens` 목록에서 제거된 경우를 처리하고, 승인되지 않은 토큰을 사용자에게 환불하여 토큰이 더 이상 승인되지 않는 경우 사용자가 장기간 토큰에 잠기지 않도록 하십시오.

# [M-04] 게임 계약이 `Staking.authorizedGames`에서 제거될 때 유동성이 잠김

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`Staking.removeAuthorizedGame()` 함수는 게임 계약의 승인을 제거하여 더 이상 `Staking` 계약과 상호 작용할 수 없도록 하기 위한 것입니다. 그러나 계약이 `authorizedGames` 목록에서 제거되는 동안 게임 계약에 의해 잠긴 `lockedLiquidity`가 있는 경우 잠긴 유동성은 `Staking` 계약에 영구적으로 잠기며 스테이커에게 할당되지 않습니다.

```solidity
 function transferPayout(address token, address recipient, uint256 amount)
        external
        nonReentrant
        returns (bool)
    {
        require(authorizedGames[msg.sender] || msg.sender == owner(), "Caller not authorized");
        //...
            if (callSucceeded) {
            //...
            lockedLiquidity[token] -= amount;
        }
        //...
    }
```

## 권장 사항:

이 문제를 완화하기 위해 `Staking` 계약은 게임 계약당 `lockedLiquidity`를 추적하여 잠긴 유동성이 있는 경우 게임 계약이 `authorizedGames` 목록에서 제거되지 않도록 해야 합니다.

# [M-05] 사용자가 `completeGame`을 프론트러닝하여 스테이크 또는 언스테이크 요청을 확정할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

스테이킹 사용자는 `Flip.completeGame()`을 프론트러닝하고 스테이크/언스테이크 요청을 확정할 수 있습니다(게임 결과 손실 또는 승리에 따라):

```solidity
    function finalizeUnstake(address token) external nonReentrant {
        UnstakeRequest memory request = unstakeRequests[token][msg.sender];
        uint256 sharesToRedeem = request.shares;
        require(sharesToRedeem > 0, "No unstake request found");
        require(block.timestamp >= request.requestTime + unstakeDelay, "Unstake delay not passed");
        --snipp--
    }
```

```solidity

    function finalizeStake(address token) external nonReentrant {
        StakeRequest memory request = stakeRequests[token][msg.sender];
        require(request.amount > 0, "No stake request found");
        require(block.timestamp >= request.requestTime + depositDelay, "Stake delay not passed");
        --snipp--
    }
```

결과적으로 스테이킹 사용자는 게임에서 큰 손실을 피하거나 큰 승리에 참여할 수 있습니다.

## 권장 사항

이 공격이 발생할 확률을 줄이려면 다음을 고려하십시오:

1. **다른 사람이 확정하도록 허용**: 공격자의 불공정한 이득을 방지할 인센티브가 있으므로 다른 사용자가 사용자의 스테이크 또는 언스테이크 요청을 확정할 수 있도록 허용합니다.

2. **확정 시간 창**: 트랜잭션을 확정하기 위한 마감일을 정의합니다. 이 마감일 이후에는 보류 중인 스테이크 또는 언스테이크 요청을 취소할 수 있어야 합니다.

3. **최소 지분**: 요청 시점과 확정 시점 사이의 최소 주가를 사용하는 것을 고려하십시오.

# [M-06] 토큰 잔액 인플레이션을 통한 제로 지분 발행 (Zero share minting)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`Staking.finalizeStake()`에서 초기 예금이 아닌 경우(`@1>`) 지분을 계산할 때 정수 나눗셈을 사용하는데, 토큰 잔액이 크면 0으로 내림될 수 있습니다.

```solidity
function finalizeStake(address token) external nonReentrant {
    --- SNIPPED ---
    // Retrieve the pool's current balance and total shares
    uint256 totalBalance = IERC20(token).balanceOf(address(this));
    uint256 currentShares = tokenInfo[token].totalShares;

    // Use standard share-mint formula: deposit * totalShares / totalBalance
    uint256 mintedShares;
    if (currentShares == 0 || totalBalance == 0) {
        // If no one has staked yet (or balance was 0), do a 1:1 mint
        mintedShares = depositAmount;
    } else {
        // Standard: deposit's fraction of the pool => minted shares
@1>      mintedShares = (depositAmount * currentShares) / totalBalance;
    }

    --- SNIPPED ---
    emit Staked(msg.sender, token, depositAmount);
}
```

이것은 승인된 모든 토큰에 대해 공격자가 다음을 수행할 수 있는 인플레이션 공격을 가능하게 합니다:

1. `addAcceptedToken()`을 백런(backrun)하여 해당 토큰에 대해 적은 금액(즉, `1 wei`)으로 스테이킹을 요청하는 첫 번째 사람이 됩니다.
2. 첫 번째 요청 스테이커로서 다가오는 스테이커들은 결과적으로 그들 뒤에 확정할 수 있고 쿨타임 기간이 지나면 공격자는 공격을 위해 확정할 적절한 타이밍을 기다릴 수 있습니다.
3. Alice는 `N` 토큰으로 스테이크를 요청하고 확정할 타이밍을 기다립니다.
4. Alice는 스테이크를 확정하기 위해 tx를 보내고 공격자는 자신의 1 wei 스테이크를 확정하기 위해 프론트런하고 최소 `N+1` 금액을 기부합니다.
5. Alice의 tx가 실행되면 부풀려진 토큰 잔액에서 계산이 내림되므로 Alice는 0 지분을 받고 공격자는 Alice의 스테이크 중 해당 부분을 얻습니다.

이 공격은 새로 승인된 토큰이나 특정 토큰의 스테이킹 풀 유동성이 낮을 때 반복할 수 있습니다.

## 권장 사항

이 문제를 완화하기 위한 가능한 접근 방식은 다음과 같습니다:

- 각 스테이킹 풀에 대한 초기 예금(첫 번째 예금자 공격이 불가능하도록)을 데드 쉐어(dead shares)로 남겨 둡니다. 이는 승인된 토큰 제한 제거의 현재 로직에 대한 경우를 고려해야 합니다.
- 지분 계산에 가상 지분을 적용합니다.

또한 최소 스테이킹 금액 요구 사항을 적용하면 공격을 시작하기가 더 어려워질 수 있습니다.
# [L-01] Staking 계약이 토큰 소수점 자릿수를 고려하지 않음

`Staking` 계약은 발행된 지분을 계산할 때 토큰 및 지분 소수점 자릿수를 고려하지 않아 잠재적인 반올림 오류가 발생합니다. 사용자는 예상보다 적은 지분을 받을 수 있습니다.

```solidity
function finalizeStake(address token) external nonReentrant {
    --snip--
    if (currentShares == 0 || totalBalance == 0) {
        // If no one has staked yet (or balance was 0), do a 1:1 mint
        mintedShares = depositAmount;
    } else {
        // Standard: deposit's fraction of the pool => minted shares
        mintedShares = (depositAmount * currentShares) / totalBalance;
    }
    --snip--
}
```

# [L-02] `cancelGame()`에서 reqId 삭제 누락

`cancelGame()` 함수는 난수 공급자(RP)에서 `reqId`를 삭제하지 않습니다. 이로 인해 요청 ID가 남아서 잠재적으로 불일치가 발생할 수 있습니다.

```solidity
    function cancelGame(string calldata gameId) external onlyPlayerOrOwner(gameId) {
        require(isGamePending[gameId], "Game is not pending");
        PendingGame memory game = pendingGames[gameId];
        require(block.timestamp >= game.gameStartTime + gameTimeout, "Game timeout period not reached");

        delete pendingGames[gameId];
        delete isGamePending[gameId];

        _tryTokenTransfer(game.token, game.player, game.betAmount); // Refund bet amount

        stakingContract.unlockLiquidity(game.token, game.netPayout);

        emit GameCancelled(game.player, game.betAmount);
    }
```

# [L-03] `Staking.totalOwed()` 함수에서 잘못된 지불 금액 계산

`Staking` 계약에서 `totalOwed()` 함수는 현재 사용자의 지분이 나타내는 토큰 양을 추정하도록 설계되었습니다. 그러나 이 함수는 보류 중인 예금 잔액을 `totalBalance`에 포함하여 지분에 대해 지불해야 할 금액을 부풀리고 부정확한 계산을 초래합니다. 보류 중인 예금은 아직 확정되지 않았기 때문입니다(이러한 보류 중인 예금의 지분은 아직 발행되지 않았음).

```solidity
    function totalOwed(address user, address token) external view returns (uint256) {
       //...
        uint256 totalBalance = IERC20(token).balanceOf(address(this));
        return (userShares * totalBalance) / totalShares_;
    }
```

권장 사항: 보류 중인 예금 잔액을 추적하고 사용자에게 지불해야 할 금액을 계산할 때 `totalBalance`에서 뺄 것을 고려하십시오.

# [L-04] 스테이킹 토큰에 의해 블랙리스트에 오른 경우 언스테이커가 스테이킹된 금액을 받을 수 없음

`Staking` 계약에서 `finalizeUnstake()` 함수는 언스테이킹 된 금액을 스테이커의 주소(`msg.sender`)로 보내려고 시도합니다. 그러나 언스테이커의 주소가 스테이킹 토큰에 의해 블랙리스트에 오른 경우 블랙리스트로 인해 전송이 실패하여 스테이킹된 금액을 받을 수 없습니다.

```solidity
  function finalizeUnstake(address token) external nonReentrant {
    //...
    IERC20(token).safeTransfer(msg.sender, amountOwed);
    //...
  }
```

권장 사항: `receiver` 인수를 포함하도록 `finalizeUnstake()` 함수를 업데이트하는 것을 고려하십시오. 그러면 언스테이킹 된 금액을 다른 주소로 보낼 수 있습니다.

# [L-05] `claimPendingPayout` 및 `claimPendingWithdrawal`에서 블랙리스트 주소에 대한 청구 실패

`Staking.claimPendingPayout()` 함수는 `transferPayout()` 중에 전송이 실패한 경우 승자가 보상을 청구할 수 있도록 설계되었습니다. 실패 이유는 승자의 주소가 블랙리스트에 올라 전송이 실패했기 때문일 수 있습니다.
그러나 이 함수는 블랙리스트에 오른 주소에 대해 작동하지 않습니다. 첫 번째 시도에서 거부된 동일한 주소로 토큰을 전송하려고 시도하므로 전송이 다시 실패하여 승자가 보상을 청구할 수 없기 때문입니다.

```solidity
function claimPendingPayout(address token) external nonReentrant {
        uint256 amount = pendingPayouts[token][msg.sender];
        //...
        (bool success, bytes memory data) =
            token.call(abi.encodeWithSelector(IERC20.transfer.selector, msg.sender, amount));
        //...
    }
```

`Flip.claimPendingWithdrawal()` 함수에도 동일한 문제가 존재하며, 블랙리스트에 오른 주소는 전송 실패로 인해 보류 중인 인출을 청구할 수 없습니다.

권장 사항: 두 함수 모두 `receiver` 주소를 수락하도록 업데이트하여 지급 또는 인출이 블랙리스트에 없는 주소로 리디렉션되도록 하여 원래 주소가 블랙리스트에 있더라도 보상 또는 인출을 청구할 수 있도록 합니다.

```diff
-function claimPendingPayout(address token) external nonReentrant {
+function claimPendingPayout(address token,address receiver) external nonReentrant {
        uint256 amount = pendingPayouts[token][msg.sender];
        //...
        (bool success, bytes memory data) =
-           token.call(abi.encodeWithSelector(IERC20.transfer.selector, msg.sender, amount));
+           token.call(abi.encodeWithSelector(IERC20.transfer.selector, receiver, amount));
        //...
    }
```

# [L-06] 엄격한 0 지분 유효성 검사로 토큰 제거 차단

`Staking.removeAcceptedToken()` 함수는 토큰 제거를 허용하기 전에 `totalShares`가 정확히 0인지 확인합니다. 이 엄격한 등식 검사는 악의적인 사용자가 무시할 수 있는 비용으로 최소 지분 잔액을 유지함으로써 토큰 제거를 영구적으로 방지할 수 있도록 합니다.

```solidity
function removeAcceptedToken(address token) external onlyOwner {
@>  require(tokenInfo[token].totalShares == 0, "Token still has staked shares");
    acceptedTokens[token] = false;
    emit TokenRemoved(token);
}
```

엄격한 등식 대신 지분이 먼지(dust) 임계값 미만인 경우 제거를 허용하는 최소 임계값 접근 방식을 구현하는 것을 고려하십시오.

예외적인 경우 적법한 소유자는 지분 제한 없이 승인된 토큰을 제거할 수 있어야 합니다. 승인된 플래그가 해당 토큰에 대한 새로운 스테이킹을 차단할 수 있지만 여전히 언스테이킹을 허용하기 때문입니다. 그러나 토큰을 다시 승인하는 것은 남은 지분이 있는 경우 위험을 초래할 수 있으므로 더 주의해야 합니다.

# [L-07] `gameTimeout` 후에도 게임이 완료될 수 있음

`Flip` 계약에서 `gameTimeout` 후에도 게임이 완료될 수 있습니다:

```solidity
    function completeGame(string calldata gameId, uint256 randomNumber) external nonReentrant {
        require(isGamePending[gameId], "No pending game for this seed");
        require(useVRF, "VRF is not enabled");
        require(msg.sender == address(randomnessProvider), "Caller is not the randomness provider");

        _completeGame(gameId, randomNumber);
    }
```

```solidity
    function cancelGame(string calldata gameId) external onlyPlayerOrOwner(gameId) {
        require(isGamePending[gameId], "Game is not pending");
        PendingGame memory game = pendingGames[gameId];
        require(block.timestamp >= game.gameStartTime + gameTimeout, "Game timeout period not reached");
      --snip--
```

게임 타임아웃 기간 후 패배한 플레이어는 `completeGame()`을 프론트런하고 `cancelGame()`을 호출하여 베팅 금액 손실을 피할 수 있습니다.

권장 사항:

사용자가 `gameTimeout` 후에 `completeGame()`을 수행하도록 허용하지 마십시오.

# [L-08] `lockedLiquidity`를 확인하지 않고 토큰 제거 시 토큰 잠김 및 지급 실패

`Staking` 계약에서 `removeAcceptedToken()` 함수는 토큰에 `lockedLiquidity`가 있는지 확인하지 않고 소유자가 `acceptedTokens` 목록에서 토큰을 제거할 수 있도록 허용합니다. 이로 인해 게임 계약이 승자에게 토큰 지급을 전송하기 위해 호출할 때 `transferPayout()` 함수가 되돌려집니다. 결과적으로 승자는 당첨금을 잃고 토큰은 `Staking` 계약에 잠겨 승자가 지급금을 받지 못하게 됩니다.

```solidity
    function removeAcceptedToken(address token) external onlyOwner {
        require(tokenInfo[token].totalShares == 0, "Token still has staked shares");
        acceptedTokens[token] = false;
        emit TokenRemoved(token);
    }
```

```solidity
  function transferPayout(address token, address recipient, uint256 amount)
        external
        nonReentrant
        returns (bool)
    {
        //...
        require(acceptedTokens[token], "Token not supported");
        //...
    }
```

권장 사항:

`removeAcceptedToken()` 함수를 업데이트하여 토큰이 `acceptedTokens` 목록에서 제거되기 전에 `lockedLiquidity`가 있는지 확인하거나, 대안으로 지급 실패를 방지하기 위해 `transferPayout()` 함수에서 `acceptedTokens` 확인을 제거하는 것을 고려하십시오.

# [L-09] 스테이커 보상과 `lockedLiquidity` 혼합으로 인한 일관되지 않은 지급 처리

`Staking.transferPayout()` 함수는 **소유자**가 **스테이커**에게 보상을 분배할 수 있도록 합니다. 그러나 이 함수는 **게임 계약에 의해 잠긴** `lockedLiquidity`에서 보상을 차감합니다.
이는 `lockedLiquidity`가 게임 계약에 불충분해질 때 일관성이 없어지는 결과를 낳습니다. 소유자의 스테이커 보상 분배가 게임 계약에 의해서만 수정되어야 하는 유동성에 영향을 미치기 때문입니다.

```solidity
 function transferPayout(address token, address recipient, uint256 amount)
        external
        nonReentrant
        returns (bool)
    {
        require(authorizedGames[msg.sender] || msg.sender == owner(), "Caller not authorized");
        //...
            if (callSucceeded) {
             //...
            lockedLiquidity[token] -= amount;
        }
        //...
    }
```

권장 사항:

`lockedLiquidity`가 게임 계약에 의해 독점적으로 제어되도록 하기 위해 **스테이커**에게 분배된 보상을 `lockedLiquidity`와 별도로 추적하는 메커니즘을 도입하는 것을 고려하십시오.

# [L-10] 일부 조합에 대해 `netPayout`이 `betAmount`보다 적어 플레이어 손실 발생

`Flip` 계약의 `flip()` 함수에서 `netPayout` 계산 중에 사용자의 `netPayout`이 때때로 게임에 참여하기 위해 지불한 `betAmount`보다 적다는 것이 테스트 중에 발견되었습니다. 특정 동전/앞면 조합의 경우 사용자는 베팅한 금액보다 낮은 지급액을 받게 되어 승리하더라도 플레이어에게 손실이 발생하여 플레이어가 승리하더라도 보상이 되지 않습니다.

`CoinFlipTest` 테스트 파일은 10% `feePercentage`(`feePercentage` = 1000) 및 `betAmount` 100(베팅 토큰은 18 소수점 자릿수를 갖는 것으로 가정)으로 설정되어 계산된 `netPayout`(보상)이 게임 `betAmount`보다 적기 시작하는 `numberOfCoins` 대 `headsRequired` 조합을 관찰합니다.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;
import "forge-std/Test.sol";

contract CoinFlipTest is Test {
    uint256 public feePercentage = 1000;

    function calculatePayout(
        uint256 betAmount,
        uint256 numberOfCoins,
        uint256 headsRequired
    )
        public
        view
        returns (uint256 grossPayout, uint256 netPayout, uint256 fee)
    {
        require(numberOfCoins > 0, "Number of coins must be greater than 0");
        require(
            headsRequired > 0 && headsRequired <= numberOfCoins,
            "Invalid number of heads required"
        );

        // Calculate total number of possible outcomes (2^numberOfCoins)
        uint256 totalOutcomes = 2 ** numberOfCoins;

        // Calculate favorable outcomes (where headsRequired or more heads occur)
        uint256 favorableOutcomes = 0;

        for (uint256 i = headsRequired; i <= numberOfCoins; i++) {
            favorableOutcomes += binomialCoefficient(numberOfCoins, i);
        }

        uint256 odds = (totalOutcomes * 1e18) / favorableOutcomes;

        // Corrected gross payout calculation
        grossPayout = (betAmount * odds) / 1e18; // Removed betAmount addition

        // Fee based on original bet amount
        fee = (betAmount * feePercentage) / 10000;
        netPayout = grossPayout - fee;

        return (grossPayout, netPayout, fee);
    }

    function binomialCoefficient(
        uint256 n,
        uint256 r
    ) internal pure returns (uint256) {
        if (r > n) {
            return 0;
        }
        if (r == 0 || r == n) {
            return 1;
        }

        uint256 numerator = 1;
        uint256 denominator = 1;

        // Calculate nCr = n! / (r! * (n - r)!)
        for (uint256 i = 0; i < r; i++) {
            numerator = numerator * (n - i);
            denominator = denominator * (i + 1);
        }
        return numerator / denominator;
    }

    function test_PayoutCalculation(
        uint256 numberOfCoins,
        uint256 headsRequired
    ) public {
        // same checks implemented in `Flip.flip()`
        vm.assume(numberOfCoins >= 1 && numberOfCoins <= 20);
        vm.assume(headsRequired >= 1 && headsRequired <= numberOfCoins);
        uint256 betAmount = 100e18;

        (, uint256 netPayout, ) = calculatePayout(
            betAmount,
            numberOfCoins,
            headsRequired
        );
        console.log(
            "numberOfCoins:headsRequired :",
            numberOfCoins,
            headsRequired
        );
        console.log("betAmount:netPayout :", betAmount, netPayout);
        console.log("-------------------");

        assert(netPayout >= betAmount);
    }
}
```

아래 결과는 `numberOfCoins = 9 및 headsRequired = 3`일 때 테스트가 실패(`netPayout < betAmount`)하기 시작함을 보여줍니다.

```bash
$ forge test --mt test_PayoutCalculation -vvvvv

Ran 1 test for test/CoinFlipTest.t.sol:CoinFlipTest
[FAIL: panic: assertion failed (0x01); counterexample: calldata=0xe8ea7ea900000000000000000000000000000000000000000000000000000000000000090000000000000000000000000000000000000000000000000000000000000003 args=[9, 3]] test_PayoutCalculation(uint256,uint256) (runs: 1,
μ: 26196, ~: 26196)
Logs:
  numberOfCoins:headsRequired : 9 3
  betAmount:netPayout : 100000000000000000000 99871244635193133000
  -------------------

Traces:
  [49533] CoinFlipTest::test_PayoutCalculation(9, 3)
    ├─ [0] VM::assume(true) [staticcall]
    │   └─ ← [Return]
    ├─ [0] VM::assume(true) [staticcall]
    │   └─ ← [Return]
    ├─ [0] console::log("numberOfCoins:headsRequired :", 9, 3) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] console::log("betAmount:netPayout :", 100000000000000000000 [1e20], 99871244635193133000 [9.987e19]) [staticcall]
    │   └─ ← [Stop]
    ├─ [0] console::log("-------------------") [staticcall]
    │   └─ ← [Stop]
    └─ ← [Revert] panic: assertion failed (0x01)

Suite result: FAILED. 0 passed; 1 failed; 0 skipped; finished in 32.16ms (31.60ms CPU time)

Ran 1 test suite in 55.82ms (32.16ms CPU time): 0 tests passed, 1 failed, 0 skipped (1 total tests)

Failing tests:
Encountered 1 failing test in test/CoinFlipTest.t.sol:CoinFlipTest
[FAIL: panic: assertion failed (0x01); counterexample: calldata=0xe8ea7ea900000000000000000000000000000000000000000000000000000000000000090000000000000000000000000000000000000000000000000000000000000003 args=[9, 3]] test_PayoutCalculation(uint256,uint256) (runs: 1,
μ: 26196, ~: 26196)

Encountered a total of 1 failing tests, 0 tests succeeded
```

`numberOfCoins = 10 및 headsRequired = 3`에 대해 별도로 테스트한 경우

```solidity
    function test_testPayoutForASpecificValues() public {
        uint256 betAmount = 100e18;
        uint256 numberOfCoins = 10;
        uint256 headsRequired = 3;
        (, uint256 netPayout, ) = calculatePayout(
            betAmount,
            numberOfCoins,
            headsRequired
        );
        console.log("betAmount:netPayout :", betAmount/1e18, netPayout/1e18);
    }
```

결과는 다음과 같습니다(`betAmount = 100e18`인 동안 `netPayout = 95e18`):

```bash
$ forge test --mt test_testPayoutForASpecificValues -vvvvv

Ran 1 test for test/CoinFlipTest.t.sol:CoinFlipTest
[PASS] test_testPayoutForASpecificValues() (gas: 53257)
Logs:
  betAmount:netPayout : 100 95

Traces:
  [53257] CoinFlipTest::test_testPayoutForASpecificValues()
    ├─ [0] console::log("betAmount:netPayout :", 100, 95) [staticcall]
    │   └─ ← [Stop]
    └─ ← [Stop]

Suite result: ok. 1 passed; 0 failed; 0 skipped; finished in 12.12ms (11.56ms CPU time)

Ran 1 test suite in 34.93ms (12.12ms CPU time): 1 tests passed, 0 failed, 0 skipped (1 total tests)
```

권장 사항:

계산된 `netPayout`이 `betAmount`보다 작은 경우 트랜잭션을 되돌리도록 `flip()` 함수를 업데이트하는 것을 고려하십시오.
