# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**sleekcore/gacha** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Gacha 소개 (About Gacha)

Gacha는 온체인 티켓팅 시스템을 사용하여 무작위 보상을 구매하고 청구하는 게임으로, 사용자가 티켓을 구매하고 엔트로피 기반 메커니즘을 통해 당첨자를 결정합니다. 토큰 스왑을 위해 Uniswap을 통합하고, 추천 보상을 지원하며, 자동화된 지불 계산 및 청구 검증을 실행합니다.

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

**_검토 커밋 해시_ - [3e352cadd3780cfaef3c65fbdb81e709a9a85d91](https://github.com/sleekcore/gacha/tree/3e352cadd3780cfaef3c65fbdb81e709a9a85d91)**

**_수정 검토 커밋 해시_ - [fae67e9381765e7774ad14921579354846b5e444](https://github.com/sleekcore/gacha/tree/fae67e9381765e7774ad14921579354846b5e444)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Gacha`
- `errors/Gacha`
- `events/Gacha`
- `GachaConfig`
- `GachaPools`
- `GachaStorage`
- `GachaTickets`
- `Pick`
- `Config`
- `Pool`
- `Referral`
- `Ticket`
- `GachaStorage`

# 발견 사항 (Findings)

# [C-01] `_swap()`에서 잘못된 준비금(reserve) 순서

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`GachaTickets::_swap` 함수에서 준비금은 다음과 같이 UniswapV2Pair 계약에서 검색됩니다:

```solidity
address pair = factory.getPair($.paymentToken, token);
(uint256 wethReserve, uint256 tokenReserve, ) = IUniswapV2Pair(pair)
    .getReserves();
if (wethReserve == 0 || tokenReserve == 0) revert InvalidPair();
```

그러나 `wethReserve`와 `tokenReserve`가 실제 토큰 쌍 순서에 따라 올바르게 할당되었는지 확인하는 `유효성 검사가 없습니다`.

Uniswap V2 준비금은 `일관된 순서로 검색되어야 합니다`:

```solidity
(token0, token1) = (pair.token0(), pair.token1())
```

여기서 `getReserves()`는 항상 `(reserve0, reserve1)`을 반환하며, `token0은 항상 더 작은 주소입니다`.

위 문제로 인해 다음과 같은 결과가 발생할 수 있습니다:

1. 잘못된 스왑 계산:

   - 다음에서 잘못된 준비금을 사용하는 경우:

   ```solidity
   uint256 maxTokens = uni.getAmountOut(cost, wethReserve, tokenReserve);
   ```

   - 함수가 `잘못된 actualTokens 값`을 반환하여 잘못된 토큰 금액을 수령하게 될 수 있습니다.

2. 잠재적인 서비스 거부(DoS):

   - `minTokens` 계산이 잘못되면(매우 높은 경우), 스왑이 `항상 되돌려져(revert)` 구매가 불가능할 수 있습니다.

3. 자금의 조용한 손실:
   - `minToken`(슬리피지 매개변수)이 극히 적은 경우(올바른 값보다), 프로토콜은 스왑에서 `예상보다 적은 풀 토큰`을 받을 수 있어 `복권 보상 풀 크기`가 줄어듭니다.

## 권장 사항

`minTokens` 계산에 준비금을 사용하기 전에 토큰 쌍 순서를 검색하고 유효성을 검사하는 것이 좋습니다.

`_swap`을 수정하여 `항상 올바른 순서로 준비금을 가져오도록` 하십시오:

```solidity
(address token0, ) = IUniswapV2Pair(pair).token0();
(uint256 reserve0, uint256 reserve1, ) = IUniswapV2Pair(pair).getReserves();

 uint256 wethReserve = token0 == $.paymentToken ? reserve0 : reserve1;
 uint256 tokenReserve = token0 == $.paymentToken ? reserve1 : reserve0;
```

권장 사항을 구현하면 계약은 `잘못된 토큰 스왑을 방지하고 잠재적인 DoS 취약점을 피할` 것입니다.

# [H-01] `_swap()`은 샌드위치 공격에 취약함

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

누구나 지정된 풀에서 티켓을 구매할 수 있으며, 특정 결제 토큰을 사용하여 밈 토큰으로 교환됩니다. 스왑 프로세스는 다음과 같이 구현됩니다:

```solidity
    function _swap(
        address token,
        uint256 cost
    ) private returns (uint256 actualTokens) {
        Storage storage $ = _getOwnStorage();
        IUniswapV2Router01 uni = IUniswapV2Router01($.uniswapRouter);
        IUniswapV2Factory factory = IUniswapV2Factory($.uniswapFactory);

        address pair = factory.getPair($.paymentToken, token);
        (uint256 wethReserve, uint256 tokenReserve, ) = IUniswapV2Pair(pair)
            .getReserves();
        if (wethReserve == 0 || tokenReserve == 0) revert InvalidPair();

@>      uint256 maxTokens = uni.getAmountOut(cost, wethReserve, tokenReserve); // includes 0.3%
@>      uint256 minTokens = Math.mulDiv(maxTokens, 95, 100); // 5% slippage

        address[] memory path = new address[](2);
        path[0] = $.paymentToken;
        path[1] = token;

        IERC20($.paymentToken).approve($.uniswapRouter, cost);
        uint256[] memory amounts = uni.swapExactTokensForTokens(
            cost,
@>          minTokens,
            path,
            address(this),
            block.timestamp + 1
        );
        actualTokens = amounts[amounts.length - 1];
    }
```

`uni.swapExactTokensForTokens()`를 사용하여 밈 토큰을 스왑하기 전에 `minTokens` 금액이 계산됩니다. 이 값은 슬리피지 보호 조치 역할을 합니다. 그러나 이것이 동적으로 계산되기 때문에 공격자는 밈 토큰 구매를 프론트런(front-run)하여 티켓 구매 전에 토큰을 구매하고 직후에 판매함으로써 가격 변동으로 이익을 취하여 가격을 조작할 수 있습니다.

## 권장 사항

`minTokens`는 프론트엔드에서 계산되어 `GachaTickets#purchase()`에 입력 매개변수로 전달되어야 합니다.

# [M-01] ERC20 `transfer` 반환 값의 부적절한 처리

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`GachaTickets::entropyCallback` 함수에서 계약은 다음과 같이 당첨금을 수령인에게 전송합니다:

```solidity
if (token == pool.token) pool.tokenBalance -= amount;
if (amount > 0) IERC20(token).transfer(receiver, amount);
```

이 함수 호출에는 `두 가지 주요 문제`가 있습니다:

1. 비준수 ERC20 토큰의 트랜잭션 되돌림(revert):

계약은 모든 ERC20 토큰이 다음 서명을 사용하여 표준 `transfer` 함수를 구현한다고 가정합니다:

```solidity
function transfer(address to, uint256 value) external returns (bool);
```

그러나 `일부 토큰은 bool 값을 반환하지 않으며`, 이는 다음을 의미합니다:

- 전송된 토큰이 `transfer 함수`에서 `boolean 값을 반환하지 않는` 경우, `IERC20`은 `boolean 반환` 값을 기대하므로 `transfer` 함수 호출이 `예기치 않게 되돌려져` 잠재적인 DoS 위험을 초래합니다.

2. 실패 시 `false`를 반환하는 토큰이 무시됨:

특정 ERC20 토큰(예: `USDT`)은 실패 시 되돌리지 않고 대신 `false를 반환`합니다.
`transfer`의 반환 값이 `확인되지 않으므로` 전송이 실패할 경우:

- `풀 토큰 잔액은 업데이트`되지만 사용자는 `당첨금을 받지 못합니다`.
- 함수는 전송이 성공했다고 가정하고 `실행을 계속합니다`.
- 값 `pool.totalRedeemed += 1;`이 잘못 업데이트되어 사용자의 `자금 손실`로 이어집니다.

동일한 문제가 `approve` 및 `transferFrom`과 같은 `IERC20` 함수가 `반환 값을 확인하지 않는` 여러 곳에서 발생하여 잠재적인 보안 위험을 초래합니다.

## 권장 사항

따라서 전송을 위해 OpenZeppelin의 SafeERC20을 사용하는 것이 좋습니다.
`모든 직접적인 ERC20 호출`을 OpenZeppelin의 `SafeERC20` 함수로 대체하십시오:

```solidity
import {SafeERC20} from "oz/token/ERC20/utils/SafeERC20.sol";
using SafeERC20 for IERC20;
```

그런 다음 전송 로직을 수정하십시오:

```solidity
if (amount > 0) IERC20(token).safeTransfer(receiver, amount);
```

`OpenZeppelin`의 SafeERC20 함수는 `반환 값 확인`과 `비표준 준수 토큰`을 올바르게 처리합니다.

# [L-01] 초과 수수료가 사용자에게 반환되지 않음

```solidity
function claim(uint256 poolId) external payable nonReentrant {
        Storage storage $ = _getOwnStorage();

        Ticket storage ticket = $.tickets[poolId][msg.sender];
        if (ticket.purchased <= ticket.claimed) revert InsufficientBalance();
        IEntropy entropy = IEntropy($.entropy);
        address provider = entropy.getDefaultProvider();
        uint256 fee = entropy.getFee(provider);
@>        uint64 seqNo = entropy.requestWithCallback{value: fee}(
            provider,
            bytes32(poolId)
        );

        $.callbackReceiver[seqNo] = msg.sender;
        $.callbackPool[seqNo] = poolId;
        ticket.claimed += 1;

        emit GachaLib.ClaimStarted(poolId, msg.sender, seqNo);
    }
```

계약은 사용자가 쿼리할 수 있는 정확한 수수료를 제공하지 않습니다. 결과적으로 `claim()` 함수 중에 사용자가 보낸 msg.value가 실제 수수료를 초과할 수 있습니다. 이 초과 금액은 사용자에게 환불되어야 합니다.

# [L-02] `swapExactTokensForTokens()`의 `deadline`이 작동하지 않음

밈 토큰을 스왑할 때 `uni.swapExactTokensForTokens()`의 데드라인으로 `block.timestamp+1`이 사용됩니다:

```solidity
        uint256[] memory amounts = uni.swapExactTokensForTokens(
            cost,
            minTokens,
            path,
            address(this),
            block.timestamp + 1
        );
```

[`UniswapV2Router01`](https://github.com/Uniswap/v2-periphery/blob/master/contracts/UniswapV2Router01.sol#L15-L18)의 데드라인 검증이 다음과 같기 때문에 이것은 항상 참입니다:

```solidity
    require(deadline >= block.timestamp, 'UniswapV2Router: EXPIRED');
```

`deadline`은 입력 매개변수로 전달되어야 합니다.

# [L-03] 모듈로 연산자가 `oddsBPS 비율` 선택 중 편향을 도입함

`GachaTickets::getPayout` 함수는 `수신된 난수`와 함께 `모듈로` 연산을 사용하여 복권 추첨을 위한 무작위성을 생성합니다. 그러나 `pyth 문서`는 모듈로 연산 사용과 관련하여 다음과 같이 명시합니다:

> 모듈로 연산자를 사용하면 2의 거듭제곱이 아닌 경우 난수의 분포가 왜곡될 수 있습니다. 이는 작거나 중간 범위에서는 무시할 수 있지만 큰 범위에서는 눈에 띌 수 있습니다. 예를 들어 1에서 52 사이의 난수를 생성하려는 경우 5의 값을 가질 확률은 극소인 50의 값을 가질 확률보다 약 10^-77 더 높습니다.

문서에 따르면 5를 가질 확률은 50을 가질 확률과 큰 차이가 있습니다. 따라서 `pool.oddsBPS.pick("pt", entropy)`가 호출되어 `oddsBPS 비율`을 무작위로 선택할 때, 불균등한 선택 확률 분포로 인해 `oddsBPS 비율`이 특정 값으로 편향될 수 있습니다. 또한 `oddsBPS 배열` 범위가 충분히 커서 `모듈로` 연산이 특정 `oddsBPS 비율`로 `편향`되게 할 수 있습니다.

따라서 이는 당첨자에 대한 `지급` 계산의 무작위성에 영향을 미칠 수 있습니다.

```solidity
  function pick(
    uint16[] memory self,
    string memory prefix,
    bytes32 entropy
  )
    internal
    pure
    returns (uint16)
  {
    return self[uint256(keccak256(abi.encode(prefix, entropy))) % self.length];
  }
```

따라서 `oddsBPS 비율`을 무작위로 결정할 때 `모듈로 연산`으로 인한 편향을 완화하기 위해 다른 방법을 사용하는 것이 좋습니다.

# [L-04] `pool.oddsBPS.indexOf(ratio)`에 대한 중복 호출

`GachaTickets::getPayout` 함수는 `pool.oddsBPS.pick` 함수를 호출하여 `oddsBPS 비율`을 무작위로 선택합니다. 구현은 다음과 같습니다:

```solidity
  function pick(
    uint16[] memory self,
    string memory prefix,
    bytes32 entropy
  )
    internal
    pure
    returns (uint16)
  {
    return self[uint256(keccak256(abi.encode(prefix, entropy))) % self.length];
  }
```

`oddsBPS 비율`이 선택되면 `getPayout` 함수는 `pool.oddsBPS.indexOf(ratio)`를 호출하여 `oddsBPS 배열`에서 `비율 값`의 인덱스를 가져옵니다.

```solidity
  function indexOf(uint16[] memory self, uint16 value) internal pure returns (uint16) {
    for (uint16 i = 0; i < self.length; i++) {
      if (self[i] == value) return i;
    }
    revert("not found");
  }
```

위에서 보는 바와 같이 `oddsBPS 배열`의 첫 번째 요소부터 시작하여 `비율`이 배열의 특정 요소와 일치할 때까지 반복한 다음 인덱스를 `티어(tier)`로 반환합니다. 그러나 이 구현의 문제는 `oddsBPS 배열`에 동일한 `비율`을 가진 두 개의 요소(인덱스 2와 3이라고 가정)가 있는 경우 `indexOf()` 함수는 항상 첫 번째 발생인 인덱스 2를 반환하는 반면, `uint256(keccak256(abi.encode(prefix, entropy))) % self.length` 계산은 인덱스 3을 반환했을 것이라는 점입니다. 따라서 이 시나리오에서는 잘못된 티어가 반환됩니다.

그러므로 `pool.oddsBPS.pick` 함수 호출 중에 동일한 인덱스를 검색하기 위해 `pool.oddsBPS.indexOf(ratio)` 함수를 중복 호출하는 대신 `uint256(keccak256(abi.encode(prefix, entropy))) % self.length` 값을 `티어`로 반환하는 것이 좋습니다.

# [L-05] 누락된 `referralClaimThreshold` 검증

사용자는 `GachaTickets::claimReferralFees` 함수를 호출하여 계약에 누적된 `추천 보상`을 청구할 수 있습니다:

```solidity
function claimReferralFees() external nonReentrant {
    Storage storage $ = _getOwnStorage();
    Referral storage ref = $.referrals[msg.sender];

    uint256 amount = ref.awardedAmount - ref.claimedAmount;
    if (amount == 0) revert InsufficientBalance();

    ref.claimedAmount += amount;

    IERC20($.paymentToken).transferFrom($.feeWallet, msg.sender, amount);

    emit GachaLib.ClaimReferral(msg.sender, amount);
}
```

그러나 `문서화된 동작은` `추천인이 referralClaimThreshold에 도달한 후에만 보상을 청구할 수 있어야 한다`고 명시합니다.

그럼에도 불구하고 `referralClaimThreshold` 매개변수는 `GachaConfig::setConfig` 함수에서 설정되지만, `추천 보상` 인출 중 `GachaTickets::claimReferralFees` 함수에서는 `시행되지 않습니다`.

위의 누락된 임계값 구현으로 인해 다음과 같은 결과가 발생할 수 있습니다:

1. 사용자가 의도한 임계값 미만의 추천 수수료를 청구할 수 있음

   - 사용자가 소액을 `반복적으로` 청구하여 `높은 거래 비용과 비효율성`을 초래할 수 있습니다.

2. 온체인 활동 및 가스 비용 증가:

   - 빈번한 소액 청구로 인해 `프로토콜에 상대적으로 높은 가스 비용`이 발생할 수 있습니다.

3. 문서화된 동작과의 편차:
   - `계약이 의도하고 문서화된 기능과 일치하지 않아` `사용자 혼란이나 잘못된 행동`으로 이어질 수 있습니다.

청구를 처리하기 전에 `referralClaimThreshold`를 시행하는 것이 좋습니다.

`claimReferralFees` 함수를 수정하여 `금액이 referralClaimThreshold를 초과하는 경우에만 청구를 허용`하십시오:

```solidity
function claimReferralFees() external nonReentrant {
    Storage storage $ = _getOwnStorage();
    Referral storage ref = $.referrals[msg.sender];

    uint256 amount = ref.awardedAmount - ref.claimedAmount;
    if (amount == 0) revert InsufficientBalance();

    // Enforce referral claim threshold
    require(amount >= $.referralClaimThreshold, "Claim amount below threshold");

    ref.claimedAmount += amount;

    IERC20($.paymentToken).transferFrom($.feeWallet, msg.sender, amount);

    emit GachaLib.ClaimReferral(msg.sender, amount);
}
```

# [L-06] 추천 수수료 추적 부족

`GachaTickets::_purchase` 함수에서 `referralFee > 0`인 경우 `추천 수수료`가 할당됩니다:

```solidity
if (referralFee > 0) {
    Referral storage ref = $.referrals[referral];
    ref.tickets += amount;
    ref.awardedAmount += referralFee;
}
```

동시에 아래와 같이 `모든 징수된 수수료(추천 수수료 포함)`가 `재무 지갑(feeWallet)`으로 전송됩니다:

```solidity
IERC20($.paymentToken).transferFrom(
    msg.sender,
    address($.feeWallet),
    totalFees
);
```

그러나 이 구현에는 다음과 같은 문제가 있습니다:

1. 총 추천 수수료가 추적되지 않음:

   - 계약은 `총 추천 수수료 금액을 프로토콜 수수료와 별도로 저장하지 않습니다`.
   - 이는 `feeWallet` 내부에서 `추천 수수료와 프로토콜 수수료를 구별할 방법이 없음`을 의미합니다.

2. 거버넌스가 프로토콜 수수료를 정확하게 인출할 수 없음:

   - `추천 수수료`는 `referrals 매핑`에 `주소별`로 저장되지만 `총액으로는 저장되지 않으므로` 거버넌스는 재무부의 실제 `프로토콜 수수료 잔액`을 결정할 수 없습니다.
   - 거버넌스가 `모든 자금을 인출`하면 추천 보상을 위해 예약해야 할 `추천 수수료를 실수로 인출`할 수 있습니다.

3. 추천 주소 추적 부족:
   - 추천 주소는 `매핑`에 저장되므로 `반복(iterate)하는 것이 불가능`합니다.
   - 거버넌스가 `총 추천 지급액을 계산`하려 해도 추천 청구가 있는 주소 목록을 검색할 수 없습니다.

권장 사항:

따라서 `totalReferralFees` 상태 변수를 도입하는 것이 좋습니다. 그리고 재무부에 누적된 `총 추천 수수료를 추적`하도록 계약을 수정하십시오.

```solidity
uint256 public totalReferralFees;
```

`추천 수수료가 추가`될 때마다 `totalReferralFees`를 업데이트하십시오:

```solidity
if (referralFee > 0) {
    Referral storage ref = $.referrals[referral];
    ref.tickets += amount;
    ref.awardedAmount += referralFee;

    totalReferralFees += referralFee; // Track total referral fees separately
}
```

# [L-07] 티켓 구매 시 프로토콜 수수료 감소

사용자는 티켓 구매 시 프로토콜 수수료를 지불해야 합니다. 프로토콜 수수료는 `feeWallet`으로 전송됩니다:

```solidity
        IERC20($.paymentToken).transferFrom(
            msg.sender,
            address($.feeWallet),
            totalFees
        );
```

추천 수수료는 다음과 같이 계산됩니다:

```solidity
        uint256 referralFee = referral == address(0)
            ? 0
            : Math.mulDiv(totalFees, $.referralBPS, BPS);
```

추천인이 `address(0)`이 아닌 경우 보상으로 추천인에게 분배됩니다:

```solidity
        if (referralFee > 0) {
            Referral storage ref = $.referrals[referral];
            ref.tickets += amount;
            ref.awardedAmount += referralFee;
        }
```

추천인은 나중에 `claimReferralFees()`를 호출하여 보상을 청구할 수 있습니다:

```solidity
    function claimReferralFees() external nonReentrant {
        Storage storage $ = _getOwnStorage();
        Referral storage ref = $.referrals[msg.sender];

        uint256 amount = ref.awardedAmount - ref.claimedAmount;
        if (amount == 0) revert InsufficientBalance();

        ref.claimedAmount += amount;

        IERC20($.paymentToken).transferFrom($.feeWallet, msg.sender, amount);

        emit GachaLib.ClaimReferral(msg.sender, amount);
    }
```

보시다시피, 지정된 풀에서 티켓을 구매할 때 동일한 금액의 프로토콜 수수료를 지불해야 합니다. 그러나 수수료의 일부는 추천인이 `address(0)`이 아닐 때 보상으로 추천인에게 분배됩니다.

구매자는 추천 주소를 자신의 주소(`msg.sender`) 또는 자신이 제어하는 계정으로 설정하여 결과적인 추천 보상을 청구할 수 있습니다.

권장 사항:

추천 수수료 메커니즘을 재설계하십시오. 예: 추천 수수료는 프로토콜 수수료에서 공제되는 대신 구매자가 직접 지불해야 합니다:

```diff
    function _purchase(
        uint256 poolId,
        uint16 amount,
        address referral
    ) private returns (uint256 actualTokens) {
        Storage storage $ = _getOwnStorage();
        Pool storage pool = $.pools[poolId];

        uint256 totalCost = amount * pool.ticketPrice;
        uint256 tokenCost = Math.mulDiv(totalCost, pool.memeRatioBPS, BPS);
        uint256 totalFees = Math.mulDiv(totalCost, $.feeBPS, BPS);
        uint256 referralFee = referral == address(0)
            ? 0
            : Math.mulDiv(totalFees, $.referralBPS, BPS);

        IERC20($.paymentToken).transferFrom(
            msg.sender,
            address(this),
            totalCost
        );
        IERC20($.paymentToken).transferFrom(
            msg.sender,
            address($.feeWallet),
-           totalFees
+           totalFees + referralFee
        );

        if (referralFee > 0) {
            Referral storage ref = $.referrals[referral];
            ref.tickets += amount;
            ref.awardedAmount += referralFee;
        }
```

# [L-08] paymentToken은 한 번만 설정할 수 있어야 함

```solidity
function setConfig(Config calldata config) public {
        Storage storage $ = _getOwnStorage();
        if (msg.sender != $.owner) revert Unauthorized();

        if (config.uniswapRouter != address(0))
            $.uniswapRouter = config.uniswapRouter;
        if (config.uniswapFactory != address(0))
            $.uniswapFactory = config.uniswapFactory;
        if (config.owner != address(0)) $.owner = config.owner;
        if (config.feeWallet != address(0)) $.feeWallet = config.feeWallet;
        if (config.feeBPS != 0) $.feeBPS = config.feeBPS;
        if (config.paymentToken != address(0))
@>            $.paymentToken = config.paymentToken;
        if (config.entropy != address(0)) $.entropy = config.entropy;
        if (config.referralBPS != 0) $.referralBPS = config.referralBPS;
        if (config.referralClaimThreshold != 0) {
            $.referralClaimThreshold = config.referralClaimThreshold;
        }
}
```

paymentToken을 다른 토큰으로 설정할 수 있도록 허용하면 금전적 손실이 발생할 수 있습니다. 예를 들어, 이전 paymentToken이 WETH였는데 나중에 USDC로 변경되면 풀의 WETH는 회수할 수 없게 됩니다.

미청구 추천 금액이 1e18인 시나리오를 고려해 보십시오. 처음에는 사용자가 1 WETH를 청구할 수 있었습니다. 그러나 변경 후 사용자는 대신 1e12 USDC를 청구할 수 있습니다. 이러한 불일치는 토큰의 가치가 동등하지 않기 때문에 발생하며, paymentToken의 변경은 청구의 실제 가치에 상당한 영향을 미칠 수 있습니다.

권장 사항:

```diff
- if (config.paymentToken != address(0))
+ if (config.paymentToken != address(0)&&$.paymentToken==address(0))
            $.paymentToken = config.paymentToken;
```
