# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**g8keep/audit** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# g8keep 소개 (About g8keep)

g8keep 프로토콜은 스마트 계약을 배포하는 시스템으로, 고정된 구매 및 판매 수수료, 잠긴 유동성, 그리고 초기 구매자가 토큰을 부당하게 획득하는 것을 방지하는 메커니즘을 갖춘 Uniswap V2 풀을 생성합니다. 이는 공정한 토큰 분배와 안정성을 보장하기 위해 수수료 징수, 유동성 공급, 스나이핑 방지에 대한 특정 규칙을 시행합니다.

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

**_검토 커밋 해시_ - [9a105c5b0758cf2421c3378c6884962d6b5708f0](https://github.com/g8keep/audit/tree/9a105c5b0758cf2421c3378c6884962d6b5708f0)**

**_수정 검토 커밋 해시_ - [bc6d948a68b37767e1e5a0db03768c52eff58263](https://github.com/g8keep/audit/tree/bc6d948a68b37767e1e5a0db03768c52eff58263)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `g8keepToken`
- `g8keepVester`
- `g8keepFactory`

# 발견 사항 (Findings)

# [H-01] 베스팅 기간이 끝날 때까지 토큰 청구 방지

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

토큰 베스팅 일정을 관리하는 `g8keepVester` 계약에는 전체 베스팅 기간이 경과할 때까지 사용자가 토큰을 청구하지 못하게 하는 구현 오류가 포함되어 있습니다.

이 문제는 `deploymentVest` 함수가 새로운 베스팅 일정을 생성할 때 `lastClaim` 타임스탬프를 초기화하지 않는 데서 비롯됩니다:

```solidity
    function deploymentVest(address _deployer, uint256 _tokensToDeployer, uint256 _vestTime)
        external
        returns (uint256 vestingId)
    {
        ...
        DeploymentVesting storage deploymentVesting = deploymentVestings[vestingId];
        deploymentVesting.recipient = _deployer;
        deploymentVesting.token = msg.sender;
        deploymentVesting.amount = _tokensToDeployer;
        deploymentVesting.vestingStart = uint40(block.timestamp);
        deploymentVesting.vestingEnd = uint40(block.timestamp + _vestTime); // @audit lastClaim is not set
        ...
    }
```

이 누락으로 인해 `_vested` 함수는 부풀려진 `vestedAmount`를 계산하게 됩니다:

```solidity
    function _vested(uint256 _id) internal view returns (DeploymentVesting storage vesting, uint256 vestedAmount) {
        vesting = deploymentVestings[_id];

        uint256 vestingStart = vesting.vestingStart;
        if (block.timestamp < vestingStart) return (vesting, 0);

        uint256 vestingEnd = vesting.vestingEnd;
        uint256 vestingAmount = vesting.amount;
        uint256 vestingClaimed = vesting.claimed;
        if (block.timestamp >= vestingEnd) return (vesting, (vestingAmount - vestingClaimed));

        uint256 timeSinceLastClaim = block.timestamp - vesting.lastClaim; // @audit inflated since lastClaim is 0
        uint256 vestingPeriod = vestingEnd - vestingStart;
        vestedAmount = (vestingAmount * timeSinceLastClaim) / vestingPeriod; // @audit vestedAmount > total vesting amount, claim will revert
    }
```

결과적으로 사용자가 `claim` 함수를 통해 토큰을 청구하려고 하면 계산된 `vestedAmount`가 총 베스팅 금액을 초과하여 잔액 부족으로 인해 트랜잭션이 되돌려집니다(revert).

## 권장 사항

`deploymentVest` 함수에서 `lastClaim` 타임스탬프를 초기화하십시오:

```diff
    function deploymentVest(address _deployer, uint256 _tokensToDeployer, uint256 _vestTime)
        external
        returns (uint256 vestingId)
    {
        ...
        DeploymentVesting storage deploymentVesting = deploymentVestings[vestingId];
        ...
+       deploymentVesting.lastClaim = uint40(block.timestamp);
        ...
    }
```

# [M-01] 배포자 수수료는 255 베이시스 포인트보다 높을 수 없음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`g8keepFactory.deployToken` 함수는 배포자가 토큰에 대한 매수 및 매도 수수료를 설정할 수 있도록 합니다. 처음에는 이 수수료의 최대값이 500 베이시스 포인트입니다. 그러나 함수 서명은 이 값들에 대해 `uint8`을 사용하므로 설정할 수 있는 최대값은 255 베이시스 포인트입니다. 즉, 배포자는 255 베이시스 포인트보다 높은 수수료를 설정할 수 없습니다.

```solidity
    function deployToken(
        uint256 _initialLiquidity,
        string memory _name,
        string memory _symbol,
        uint256 _totalSupply,
        address _treasuryWallet,
@>      uint8 _buyFee,
@>      uint8 _sellFee,
```

## 권장 사항

```diff
    function deployToken(
        uint256 _initialLiquidity,
        string memory _name,
        string memory _symbol,
        uint256 _totalSupply,
        address _treasuryWallet,
-       uint8 _buyFee,
+       uint16 _buyFee,
-       uint8 _sellFee,
+       uint16 _sellFee,
```

# [M-02] vestingEnd 오버플로로 인한 `minimumDeployVestTime` 우회

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`g8keepFactory` 계약은 `minimumDeployVestTime` 매개변수를 통해 토큰 배포자에 대한 최소 베스팅 기간을 구현합니다. 그러나 `g8keepVester` 계약의 오류로 인해 잠재적인 타임스탬프 오버플로로 이 확인을 우회할 수 있습니다.

`g8keepFactory`의 `deployToken` 함수에는 베스팅 시간이 최소 요구 사항을 충족하는지 확인하는 검사가 있습니다:

```solidity
    function deployToken(
        uint256 _initialLiquidity,
        string memory _name,
        string memory _symbol,
        uint256 _totalSupply,
        address _treasuryWallet,
        uint8 _buyFee,
        uint8 _sellFee,
        address _pairedToken,
        uint256 _deployReserve,
        uint256 _deployVestTime,
        uint256 _snipeProtectionSeconds,
        bytes32 _tokenSalt
    ) external payable returns (address _tokenAddress) {
        ...
        if (
            _buyFee > maxBuyFee || _sellFee > maxSellFee || _deployVestTime < minimumDeployVestTime
            ...
        ) {
            revert InvalidDeploymentParameters();
        }
        ...
    }
```

그러나 `g8keepVester` 계약에서 `deploymentVest` 함수는 베스팅 종료 시간을 `uint40`으로 저장하므로 오버플로가 발생할 수 있습니다. 이 오버플로는 의도한 것보다 훨씬 짧은 베스팅 기간을 초래하여 잠재적으로 즉시 토큰 청구를 허용할 수 있습니다.

```solidity
    function deploymentVest(address _deployer, uint256 _tokensToDeployer, uint256 _vestTime)
        external
        returns (uint256 vestingId)
    {
        ...
        DeploymentVesting storage deploymentVesting = deploymentVestings[vestingId];
        deploymentVesting.recipient = _deployer;
        deploymentVesting.token = msg.sender;
        deploymentVesting.amount = _tokensToDeployer;
        deploymentVesting.vestingStart = uint40(block.timestamp);
        deploymentVesting.vestingEnd = uint40(block.timestamp + _vestTime); // @audit vestingEnd can overflow
        ...
    }
```

이 취약점은 악의적인 배포자가 g8keep에서 설정한 최소 베스팅 기간을 우회할 수 있게 합니다. 그들은 오버플로를 유발하는 매우 큰 `_deployVestTime`을 설정하여 베스팅 종료 시간이 거의 즉시가 되게 할 수 있습니다. 이는 의도된 토큰 분배 모델을 약화시키고 예상치 못한 토큰 덤핑으로 이어질 수 있습니다.

## 권장 사항

`block.timestamp + _vestTime`이 `type(uint40).max`보다 큰지 확인하고, 그렇다면 되돌리도록(revert) 하십시오.

# [M-03] Uniswap 위젯으로 시스템을 완전히 사용할 수 없음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

프로토콜의 요구 사항 중 하나는 `시스템이 Uniswap 위젯으로 완전히 사용 가능해야 한다`는 것입니다. 그러나 다음과 같은 호환되지 않는 기능이 발견되었습니다:

1. 스나이프 보호 기간 동안 g8keep 토큰 구매 시 견적이 올바른 금액을 반환하지 않을 수 있으며, `maxSnipeProtectionBuyWithoutPenalty`를 사용하여 페널티 없이 구매할 수 있는 최대 토큰 양을 계산해야 합니다. 이 흐름은 Uniswap 위젯에서 지원되지 않습니다.

2. [문서](https://docs.uniswap.org/sdk/swap-widget/overview)에는 fee-on-transfer(전송 시 수수료 부과) 토큰 지원에 대한 언급이 없으며, Uniswap 위젯의 [소스 코드](https://github.com/Uniswap/widgets)를 검토한 결과 개발자가 이러한 토큰을 지원할 수 있는 가능성이 발견되지 않았습니다.

Uniswap SDK에서 이러한 지원의 유일한 예는 [v2-sdk router](https://github.com/Uniswap/sdks/blob/main/sdks/v2-sdk/src/router.ts)에서 발견되었습니다.

```ts
    /**
    * Whether any of the tokens in the path are fee on transfer tokens, which should be handled with special methods
    */
    feeOnTransfer?: boolean

@>  const useFeeOnTransfer = Boolean(options.feeOnTransfer)

    let methodName: string
    let args: (string | string[])[]
    let value: string
    switch (trade.tradeType) {
      case TradeType.EXACT_INPUT:
        if (etherIn) {
@>        methodName = useFeeOnTransfer ? 'swapExactETHForTokensSupportingFeeOnTransferTokens' : 'swapExactETHForTokens'
```

그러나 Uniswap 위젯에서 이 기능을 사용하는 방법은 발견되지 않았습니다.

따라서 Uniswap 위젯을 사용하여 g8keep 토큰을 구매하거나 판매하는 것이 예상대로 작동하지 않을 수 있습니다.

## 권장 사항

스나이핑 보호 및 전송 시 수수료는 프로토콜의 필수 기능이므로 Uniswap 위젯보다 유연한 대안을 사용하거나 Uniswap의 `v2-sdk`를 사용하여 사용자 지정 위젯을 구축하는 것이 좋습니다.

# [M-04] 비-ERC20 토큰은 페어링된 토큰으로 지원되지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

배포자로부터 팩토리로 자금을 이체하기 위해 `g8keepFactory.deployToken()` 함수는 ERC20 `transferFrom()` 함수를 사용합니다.

```solidity
IERC20(_pairedToken).transferFrom(msg.sender, address(this), _initialLiquidity);
```

비-ERC20 토큰을 페어링된 토큰으로 추가해야 하는 경우 이러한 호출은 되돌려지므로, 팩토리는 그러한 토큰을 지원하지 않습니다.

코드에서 ERC20 함수가 사용되는 다른 곳은 `g8keepFactory.withdrawToken` 및 `g8keepToken.withdrawToken` 함수입니다. 이러한 함수 역시 비-ERC20 토큰에서는 작동하지 않습니다.

## 권장 사항

[SafeERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol) 또는 유사한 라이브러리를 사용하십시오.

# [M-05] 0이 아닌 허용(allowance)이 필요한 토큰은 비활성화 할 수 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`g8keepFactory` 계약에서 `setPairedTokenSettings` 함수는 소유자가 토큰을 유효한 페어링된 토큰으로 활성화하거나 비활성화할 수 있도록 합니다. 함수 끝에서 Uniswap 라우터는 토큰의 무제한 금액을 사용하도록 승인됩니다.

```solidity
    function setPairedTokenSettings(address pairedToken, bool allowed, uint256 minimumLiquidity) external onlyOwner {
        if (lockedTokens[pairedToken]) revert TokenLocked();

        allowedPairs[pairedToken] = allowed;
        pairedTokenMinimumLiquidity[pairedToken] = minimumLiquidity;
@>      IERC20(pairedToken).approve(UNISWAP_V2_ROUTER, type(uint256).max);
    }
```

[메인넷의 USDT](https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7#code)와 같은 일부 토큰은 현재 및 새 허용량이 모두 0이 아닐 때 되돌려지므로 트랜잭션이 되돌려져 토큰을 페어링된 토큰으로 비활성화할 수 없게 됩니다.

```solidity
    function approve(address _spender, uint _value) public onlyPayloadSize(2 * 32) {

        // To change the approve amount you first have to reduce the addresses`
        //  allowance to zero by calling `approve(_spender, 0)` if it is not
        //  already 0 to mitigate the race condition described here:
        //  https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
@>      require(!((_value != 0) && (allowed[msg.sender][_spender] != 0)));

        allowed[msg.sender][_spender] = _value;
        Approval(msg.sender, _spender, _value);
    }
```

## 권장 사항

```diff
    function setPairedTokenSettings(address pairedToken, bool allowed, uint256 minimumLiquidity) external onlyOwner {
        if (lockedTokens[pairedToken]) revert TokenLocked();

        allowedPairs[pairedToken] = allowed;
        pairedTokenMinimumLiquidity[pairedToken] = minimumLiquidity;
+       if (allowed) {
            IERC20(pairedToken).approve(UNISWAP_V2_ROUTER, type(uint256).max);
+       } else {
+           IERC20(pairedToken).approve(UNISWAP_V2_ROUTER, 0);
+       }
    }
```

# [L-01] `msg.value`가 `_initialLiquidity`와 같은지 확인하지 않음

페어링된 토큰이 WETH일 때 `g8keepFactory.deployToken`에서 `msg.value`는 WETH로 래핑됩니다.

```solidity
        if (_pairedToken == WETH) {
@>          (bool success,) = WETH.call{value: msg.value}("");
            if (!success) revert FailedToDepositWETH();
        } else if (msg.value > 0) {
            revert ValueNotAllowedForNonWETHPairs();
```

그러나 `msg.value`가 `_initialLiquidity`와 같은지 확인하지 않으므로 사용자가 `_initialLiquidity`보다 더 많이 보내면 초과분은 손실됩니다. 다른 사용자가 `_initialLiquidity`보다 낮은 `msg.value`로 `deployToken`을 호출하여 초과분을 가져갈 수 있습니다.

## 권장 사항

```diff
        if (_pairedToken == WETH) {
++          if (msg.value != _initialLiquidity) revert ValueSentNotValid();
            (bool success,) = WETH.call{value: msg.value}("");
            if (!success) revert FailedToDepositWETH();
        } else if (msg.value > 0) {
            revert ValueNotAllowedForNonWETHPairs();
```

# [L-02] 데드라인이 현재 블록 타임스탬프로 설정됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`g8keepFactory.sellTokens`는 공장 소유자가 수수료로 받은 토큰을 판매하기 위해 호출할 수 있습니다. 이 함수는 `swapExactTokensForTokensSupportingFeeOnTransferTokens`를 사용하여 토큰을 스왑하고 `block.timestamp`를 데드라인으로 사용합니다. 결과적으로 트랜잭션은 토큰 가격이 다를 수 있는 미래의 언제든지 실행될 수 있습니다.

```solidity
            try IUniswapV2Router02(UNISWAP_V2_ROUTER).swapExactTokensForTokensSupportingFeeOnTransferTokens(
@>              sale.amountToSell, sale.amountOutMin, path, sale.recipient, block.timestamp
            ) {} catch {}
```

## 권장 사항

데드라인을 매개변수로 받아 스왑 함수에서 사용하는 것이 좋습니다.

# [L-03] 유동성 관리 중 수수료가 부과됨

`g8keepToken`은 사용자가 유니스왑 페어 계약에서 판매/구매할 때 수수료를 부과합니다. 이 기능은 `g8keepToken` 계약의 재정의된 `_transfer` 함수에 구현되어 있습니다.

```solidity
function _transfer(address from, address to, uint256 amount) private {
    ...
    if (!(from == G8KEEP || to == G8KEEP)) {
        if (to == UNISWAP_V2_PAIR) {
            toAmount = _applyFees(from, amount, SELL_FEE);
        } else if (from == UNISWAP_V2_PAIR) {
            toAmount = _applyFees(from, amount, BUY_FEE);
        }
    }

    ...
}
```

그러나 사용자가 페어 계약에 유동성을 추가하거나 소각할 때도 동일한 함수가 사용되므로 수수료가 부과됩니다.

# [L-04] fee-on-transfer 토큰과의 비호환성

g8keepFactory 계약의 deployToken 함수는 전송 시 수수료 부과(fee-on-transfer) 토큰을 처리하도록 설계되지 않았습니다. 이 문제는 함수가 페어링된 토큰을 전송할 때 `_initialLiquidity` 토큰의 정확한 양이 수신될 것이라고 가정하기 때문에 발생합니다.

그러나 `_pairedToken`이 fee-on-transfer 토큰인 경우, `deployToken` 함수는 수수료로 인해 `_initialLiquidity` 양보다 적게 받습니다. 나중에 Uniswap 풀에 유동성을 추가할 때, 계약은 `_initialLiquidity - valueToG8keep` 대신 `_initialLiquidity - valueToG8keep - fee`를 가지고 있으므로 잔액 부족으로 인해 `addLiquidity` 함수가 되돌려집니다.

```solidity
    function deployToken(
        ...
    ) external payable returns (address _tokenAddress) {
        ...
        } else {
            IERC20(_pairedToken).transferFrom(msg.sender, address(this), _initialLiquidity); // @audit will receive less than _initialLiquidity if _pairedToken is fee-on-transfer one
        }
        ...

        // add liquidity to LP
        IUniswapV2Router02(UNISWAP_V2_ROUTER).addLiquidity(
            address(token),
            _pairedToken,
            token.balanceOf(address(this)),
            _initialLiquidity - valueToG8keep, // @audit addLiquidity will revert because of insufficient balance
            0,
            0,
            address(this),
            block.timestamp
        );
        ...
    }
```

전송 전후의 잔액을 뺀 값을 `actualReceived` 양으로 설정하여 실제 토큰 전송을 확인하십시오.

```diff
    function deployToken(
        ...
    ) external payable returns (address _tokenAddress) {
        ...
        } else {
+           uint256 balanceBefore = IERC20(_pairedToken).balanceOf(address(this));
            IERC20(_pairedToken).transferFrom(msg.sender, address(this), _initialLiquidity);
+           uint256 balanceAfter = IERC20(_pairedToken).balanceOf(address(this));
+           uint256 actualReceived = balanceAfter - balanceBefore;
        }
        ...

        // add liquidity to LP
        IUniswapV2Router02(UNISWAP_V2_ROUTER).addLiquidity(
            address(token),
            _pairedToken,
            token.balanceOf(address(this)),
-           _initialLiquidity - valueToG8keep,
+           actualReceived - valueToG8keep,
            0,
            0,
            address(this),
            block.timestamp
        );
        ...
    }
```

# [L-05] g8keepFactory 토큰 배포에 대한 DOS 공격

g8keepFactory 계약은 새로운 `g8keepToken` 인스턴스를 배포하기 위해 `CREATE2`를 사용하므로 서비스 거부(DOS) 공격에 취약합니다. 공격자는 배포될 토큰의 주소를 예측하고 `deployToken` 트랜잭션을 프론트런하여 Uniswap V2 풀을 미리 생성하여 배포를 실패하게 만들 수 있습니다. 원래 `deployToken` 트랜잭션은 `UniswapV2: PAIR_EXISTS` 오류와 함께 되돌려집니다.

토큰 배포는 지속적으로 차단될 수 있으며, 실패한 배포 트랜잭션에 소비된 가스비 측면에서 사용자에게 금전적 손실을 초래할 수 있습니다. 공격 비용은 Uniswap V2 풀을 미리 생성하는 가스비입니다.

# [L-06] `_treasuryWallet`을 `address(0)`으로 설정 가능

`updateTreasuryWallet`이 호출되면 `newAddress`가 address(0)과 다른지 확인합니다. 이는 `treasuryWallet`이 `address(0)`이 될 수 없음을 암시합니다. 그러나 `g8keepFactory` 계약은 토큰 배포 중에 `treasuryWallet`을 `address(0)`으로 설정하는 것을 허용합니다.

`deployToken` 함수에 0 주소 확인을 추가하는 것이 좋습니다.

# [L-07] 사용자가 더 적은 g8keep 토큰을 받을 수 있음

스나이프 보호 기간 동안 사용자가 잔액을 예상 잔액 미만으로 줄이는 g8keep 토큰 양을 구매하려고 할 때, 구매자가 더 많은 페어링된 토큰을 보낼수록 더 적은 g8keep 토큰을 받습니다.

예를 들어:

- 시간 `t`에 `maxSnipeProtectionBuyWithoutPenalty`를 호출하면 1 WETH로 책정된 100 g8keep 토큰을 반환합니다.
- 구매자가 1 WETH를 보내면 100 g8keep 토큰(수수료 제외)을 받습니다.
- 구매자가 2 WETH를 보내면 70 g8keep 토큰(수수료 제외)을 받습니다.

구매자는 `amountOutMin` 매개변수로 미끄러짐(slippage)으로부터 자신을 보호해야 하지만, 위에서 설명한 동작은 직관에 어긋나며 의도한 것인지 명확하지 않습니다.

문서는 다음을 설명합니다:

> 잔액을 예상 잔액 미만으로 줄이는 구매는 산출량을 줄임으로써 기하급수적으로 페널티를 받습니다.

그러나 페널티가 초과 금액에 적용되어야 하는지 아니면 전체 운영 금액에 적용되어야 하는지 명확하지 않습니다.

또한 다음 요구 사항은

> 배포자나 초기 구매자가 적은 비용으로 공급의 많은 부분을 스나이핑하도록 허용해서는 안 됩니다.

초기 단계에서 많은 양의 토큰을 구매할 수 있지만 높은 비용이 든다는 것을 암시하는 것 같습니다.
