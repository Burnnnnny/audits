# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**lucidlyfi/lucidly-core-v1** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Lucidly 정보

Lucidly는 다양한 토큰으로 구성된 유동성 풀을 생성할 수 있습니다. 이 풀은 또한 이러한 토큰에 대한 AMM 역할을 하여 토큰 간의 교환을 허용합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [2be4150ea1eecded436f3b5ee37c1cc57ff04d62](https://github.com/lucidlyfi/lucidly-core-v1/tree/2be4150ea1eecded436f3b5ee37c1cc57ff04d62)

_수정 검토 커밋 해시_ - [5bbf1fc080e47cec886240aa00ae7c4f93794a88](https://github.com/lucidlyfi/lucidly-core-v1/tree/5bbf1fc080e47cec886240aa00ae7c4f93794a88)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Pool`
- `PoolToken`
- `Staking`

# 발견 사항

# [C-01] 수수료는 항상 `address(0)`으로 전송됩니다.

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

Staking 계약에는 다음과 같이 구현된 수수료 메커니즘이 포함되어 있습니다.

```solidity
        uint256 _fee = (shares_ * depositFeeBps) / 1e4;
        _mint(to_, shares_ - _fee);
        _mint(protocolFeeAddress, _fee);
```

모든 수수료는 protocolFeeAddress로 전송됩니다. 그러나 계약은 이 주소를 초기화하지 않으며 업데이트할 수 있는 기능도 제공하지 않습니다. 결과적으로 `protocolFeeAddress`의 기본값이 address(0)이기 때문에 수수료는 영원히 손실됩니다.

## 권장 사항

Staking 계약에 다음 함수를 추가하는 것을 고려하십시오.

```solidity
    function setProtocolFeeAddress(address protocolFeeAddress_) external onlyOwner {
        if (protocolFeeAddress_ == address(0)) revert Staking__InvalidParams();
        protocolFeeAddress = protocolFeeAddress_;
    }
```

# [H-01] 잘못된 `_prevRatios` 인덱스를 사용할 수 있음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

초기 유동성이 이미 제공된 상태에서 `addLiquidity`가 호출될 때 호출자는 `amounts_`의 해당 값을 0으로 설정하여 나열된 토큰 중 하나를 제공하지 않고 건너뛸 수 있습니다. 제공된 금액이 0이면 함수는 토큰 및 풀 가상 잔액의 곱과 합계 업데이트를 건너뛰고 `_prevRatios` 저장도 건너뜁니다.

```solidity

    function addLiquidity(uint256[] calldata amounts_, uint256 minLpAmount_, address receiver_)
        external
        nonReentrant
        returns (uint256)
    {
        uint256 _numTokens = numTokens;
        if (amounts_.length != _numTokens) revert Pool__InvalidParams();

        // ...

        // update rates
        (_virtualBalanceProd, _virtualBalanceSum) = _updateRates(_tokens, _virtualBalanceProd, _virtualBalanceSum);
        uint256 _prevSupply = supply;

        uint256 _virtualBalanceProdFinal = _virtualBalanceProd;
        uint256 _virtualBalanceSumFinal = _virtualBalanceSum;
        uint256 _prevVirtualBalanceSum = _virtualBalanceSum;
        uint256[] memory _prevRatios = new uint256[](_numTokens);
        uint256 _virtualBalance;

        for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
            if (t == _numTokens) break;

            uint256 __amount = amounts_[t];

            if (__amount == 0) {
                if (!(_prevSupply > 0)) {
                    revert Pool__InitialDepositAmountMustBeNonZero();
                }
                continue;
            }

            // update stored virtual balance
            (_prevVirtualBalance, _rate, _packedWeight) = _unpackVirtualBalance(packedVirtualBalances[t]);
            uint256 _changeInVirtualBalance = (__amount * _rate) / PRECISION;
            _virtualBalance = _prevVirtualBalance + _changeInVirtualBalance;
            packedVirtualBalances[t] = _packVirtualBalance(_virtualBalance, _rate, _packedWeight);

            if (_prevSupply > 0) {
>>>             _prevRatios[t] = (_prevVirtualBalance * PRECISION) / _prevVirtualBalanceSum;
                uint256 _weightTimesN = _unpackWeightTimesN(_packedWeight, _numTokens);

                // update product and sum of virtual balances
                _virtualBalanceProdFinal = (
                    _virtualBalanceProdFinal
                        * _powUp((_prevVirtualBalance * PRECISION) / _virtualBalance, _weightTimesN)
                ) / PRECISION;

                // the `D^n` factor will be updated in `_calculateSupply()`
                _virtualBalanceSumFinal += _changeInVirtualBalance;

                // remove fees from balance and recalculate sum and product
                uint256 _fee = (
                    (_changeInVirtualBalance - (_prevVirtualBalance * _lowest) / PRECISION) * (swapFeeRate / 2)
                ) / PRECISION;
                _virtualBalanceProd = (
                    _virtualBalanceProd
                        * _powUp((_prevVirtualBalance * PRECISION) / (_virtualBalance - _fee), _weightTimesN)
                ) / PRECISION;
                _virtualBalanceSum += _changeInVirtualBalance - _fee;
            }

            SafeTransferLib.safeTransferFrom(tokens[t], msg.sender, address(this), __amount);
        }
        // ...
}
```

그러나 비율이 유효한 대역 내에서 변경되는지 확인할 때 잘못된 인덱스의 `prevRatios`를 제공합니다.

```solidity
// ...
        uint256 _supply = _prevSupply;
        if (_prevSupply == 0) {
            // initial deposit, calculate necessary variables
            (_virtualBalanceProd, _virtualBalanceSum) = _calculateVirtualBalanceProdSum();
            if (!(_virtualBalanceProd > 0)) revert Pool__AmountsMustBeNonZero();
            _supply = _virtualBalanceSum;
        } else {
            // check bands
            uint256 _j = 0;
            for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
                if (t == _numTokens) break;
                if (amounts_[t] == 0) continue;
                (_virtualBalance, _rate, _packedWeight) = _unpackVirtualBalance(packedVirtualBalances[t]);
>>>             _checkBands(_prevRatios[_j], (_virtualBalance * PRECISION) / _virtualBalanceSumFinal, _packedWeight);
                _j = FixedPointMathLib.rawAdd(_j, 1);
            }
        }
// ...
```

3개의 토큰(토큰 인덱스 0, 인덱스 1, 인덱스 2)이 있는 시나리오를 고려해 보십시오.

인덱스 1 토큰을 건너뛰고 인덱스 1의 `amounts_`를 0으로 설정하여 `addLiquidity`가 호출될 때.

인덱스 2의 토큰에 대해 `_checkBands`가 호출되면 1과 같은 인덱스 `_j`에서 `_prevRatios`를 제공합니다(토큰 인덱스 1이 건너뛰어져 `_j`가 증가하지 않음). 이는 `_prevRatios`를 저장할 때 건너뛴 토큰이 있는지 여부에 관계없이 실제 인덱스(`t`)가 사용되기 때문에 잘못된 것입니다.

이로 인해 잘못된 인덱스에서 `_prevRatios`를 사용하여 유효한 `addLiquidity` 작업이 되돌려질(revert) 수 있습니다.

## 권장 사항

`_j` 대신 `t` 인덱스를 사용하십시오.

```diff
// ...
        uint256 _supply = _prevSupply;
        if (_prevSupply == 0) {
            // initial deposit, calculate necessary variables
            (_virtualBalanceProd, _virtualBalanceSum) = _calculateVirtualBalanceProdSum();
            if (!(_virtualBalanceProd > 0)) revert Pool__AmountsMustBeNonZero();
            _supply = _virtualBalanceSum;
        } else {
            // check bands
-            uint256 _j = 0;
            for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
                if (t == _numTokens) break;
                if (amounts_[t] == 0) continue;
                (_virtualBalance, _rate, _packedWeight) = _unpackVirtualBalance(packedVirtualBalances[t]);
-                _checkBands(_prevRatios[_j], (_virtualBalance * PRECISION) / _virtualBalanceSumFinal, _packedWeight);
+                _checkBands(_prevRatios[t], (_virtualBalance * PRECISION) / _virtualBalanceSumFinal, _packedWeight);
-                _j = FixedPointMathLib.rawAdd(_j, 1);
            }
        }
// ...
```

# [H-02] 새로운 공급 계산에 잘못된 증폭 값이 사용됨

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

새 토큰을 풀에 추가하기 위해 `addToken`이 호출될 때 제공되는 매개변수 중 하나는 공급 및 가상 잔액 제품을 계산하기 위한 새로운 풀 증폭 계수(amplification factor)입니다. 그러나 `_calculateSupply`가 호출되면 새 `amplification_` 값 대신 이전 `amplification`을 제공합니다.

```solidity
    function addToken(
        address token_,
        address rateProvider_,
        uint256 weight_,
        uint256 lower_,
        uint256 upper_,
        uint256 amount_,
        uint256 amplification_,
        uint256 minLpAmount_,
        address receiver_
    ) external onlyOwner {
        // ...

        _virtualBalance = (amount_ * _rate) / PRECISION;
        _packedWeight = _packWeight(weight_, weight_, _lower, _upper);

        // set parameters for new token
        numTokens = _numTokens;
        tokens[_prevNumTokens] = token_;
        rateProviders[_prevNumTokens] = rateProvider_;
        packedVirtualBalances[_prevNumTokens] = _packVirtualBalance(_virtualBalance, _rate, _packedWeight);

        // recalculate variables
        (uint256 _virtualBalanceProd, uint256 _virtualBalanceSum) = _calculateVirtualBalanceProdSum();

        // update supply
        uint256 _prevSupply = supply;
        uint256 __supply;
        // @audit - should provide new amplification here
        (__supply, _virtualBalanceProd) = _calculateSupply(
>>>         _numTokens, _virtualBalanceSum, amplification, _virtualBalanceProd, _virtualBalanceSum, true
        );

        amplification = amplification_;
        supply = __supply;
        packedPoolVirtualBalance = _packPoolVirtualBalance(_virtualBalanceProd, _virtualBalanceSum);

        SafeTransferLib.safeTransferFrom(token_, msg.sender, address(this), amount_);
        if (__supply <= _prevSupply) revert Pool__InvalidParams();
        uint256 _lpAmount = FixedPointMathLib.rawSub(__supply, _prevSupply);
        if (_lpAmount < minLpAmount_) revert Pool__InvalidParams();
        PoolToken(tokenAddress).mint(receiver_, _lpAmount);
        emit AddToken(_prevNumTokens, token_, rateProvider_, _rate, weight_, amount_);
    }
```

이로 인해 풀의 공급 및 가상 잔액 제품의 계산이 올바르지 않게 되며, 제공된 증폭이 예상대로 공급 및 가상 잔액 제품에 즉시 영향을 미치지 않습니다.

## 권장 사항

`_calculateSupply`에 `amplification` 대신 `amplification_`을 제공하십시오.

```diff
// ...
        (__supply, _virtualBalanceProd) = _calculateSupply(
-           _numTokens, _virtualBalanceSum, amplification, _virtualBalanceProd, _virtualBalanceSum, true
+           _numTokens, _virtualBalanceSum, amplification_, _virtualBalanceProd, _virtualBalanceSum, true
        );
// ...
```

# [M-01] `Staking` 볼트가 초기 슬픔(grief) 공격에 취약함

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

초기 입금 공격은 가상 주식 사용으로 인해 대부분 완화되었습니다. 그러나 초기 예금자 슬픔 공격은 여전히 가능하여 첫 번째 예금자가 공격자에게 비교적 적은 비용으로 자산을 잃게 합니다.

```solidity
contract PoolSwap is Test {

    Staking staking;
    MockToken public token;
    address alice = makeAddr("alice");
    address bob = makeAddr("bob");

    function setUp() public {
        token = new MockToken("token", "t", 18);

        // deploy staking contract
        staking = new Staking(address(token), "XYZ Mastervault Token", "XYZ-MVT", true, alice);
    }

        function test_initial_deposit_grief() public {

        vm.startPrank(alice);
        token.mint(alice,11e18 + 10);

        uint256 initialAssetBalance = token.balanceOf(alice);
        console.log("attacker balance before : ");
        console.log(initialAssetBalance);

        token.approve(address(staking), 1e18);

        staking.mint(10, alice);

        token.transfer(address(staking), 11e18);

        vm.stopPrank();

        vm.startPrank(bob);

        token.mint(bob,10e18 + 10);
        token.approve(address(staking), 1e18);
        staking.deposit(1e18, bob);
        vm.stopPrank();

        uint256 bobShares = staking.balanceOf(bob);
        console.log("bob shares : ");
        console.log(bobShares);

        vm.stopPrank();

        vm.startPrank(alice);

        staking.redeem(staking.balanceOf(alice), alice, alice);
        uint256 afterAssetBalance = token.balanceOf(alice);
        console.log("attacker balance after : ");
        console.log(afterAssetBalance);
        vm.stopPrank();

    }
}
```

테스트 실행 :

```shell
forge test --match-test test_initial_deposit_grief -vvv
```

로그 출력 :

```shell
Logs:
  attacker balance before :
  11000000000000000010
  bob shares :
  0
  attacker balance after :
  10909090909090909100
```

앨리스가 `~ 0.1 ETH`의 비용으로 밥의 자산 `1 ETH`를 잠글 수 있음을 관찰할 수 있습니다.

## 권장 사항

소량의 초기 예치금으로 이를 완화하는 것을 고려하십시오.

# [M-02] 소유자가 특정 경우 풀 토큰을 `rescue()`할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

풀 계약에는 계약에서 비풀(non-pool) 토큰을 복구하도록 설계된 `rescue()` 함수가 포함되어 있습니다.

```solidity
    function rescue(address token_, address receiver_) external onlyOwner {
        uint256 _numTokens = numTokens;
        for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
            if (t == _numTokens) break;
            if (!(token_ != tokens[t])) revert Pool__CannotRescuePoolToken();
        }
        uint256 _amount = ERC20(token_).balanceOf(address(this));
        ERC20(token_).transfer(receiver_, _amount);
    }
```

소유자가 풀 토큰을 구하려고 시도하면 함수는 `Pool__CannotRescuePoolToken()` 오류를 발생시킵니다. 그러나 토큰에 진입점이 여러 개 있는 경우 소유자는 이 확인을 우회할 수 있습니다. 예를 들어, TUSD 토큰의 경우 보조 진입점이 여전히 활성 상태일 때 이러한 경우가 있었습니다. 여러 진입점이 있는 다른 토큰이 존재할 수 있으므로 이 취약점은 위험으로 남아 있습니다.

## 권장 사항

이 문제에 대한 직접적인 해결책은 없지만, 토큰에 여러 진입점이 있는 경우 소유자가 모든 토큰을 인출할 수 있다는 점을 문서화하는 것이 중요합니다.

# [M-03] Staking 계약이 EIP-4626을 준수하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

현재 스테이킹 구현은 아래와 같이 볼트에 예치된 자산에 대해 수수료를 부과합니다.

```solidity
    function _deposit(address by_, address to_, uint256 tokens_, uint256 shares_) internal virtual override {
        SafeTransferLib.safeTransferFrom(asset(), by_, address(this), tokens_);
        uint256 _fee = (shares_ * depositFeeBps) / 1e4;
        _mint(to_, shares_ - _fee);
        _mint(protocolFeeAddress, _fee);

        emit Deposit(by_, to_, tokens_, shares_);

        _afterDeposit(tokens_, shares_);
    }
```

[EIP4626](https://eips.ethereum.org/EIPS/eip-4626)에 따르면 `previewDeposit()` 및 `previewMint()`에는 보증금 수수료가 포함되어야 합니다.

> 보증금 수수료를 포함해야 합니다. 통합자는 보증금 수수료의 존재를 알고 있어야 합니다.

그러나 현재 스테이킹 구현(즉, OpenZeppelin impl)은 자산에서 주식으로 변환할 때 이러한 수수료를 고려하지 않습니다. 이러한 누락은 향후 예상치 못한 동작 및 통합 문제로 이어질 수 있습니다.

## 권장 사항

수수료를 포함하도록 `previewDeposit()` 및 `previewMint()` 함수를 재정의하는 것을 고려하십시오.

# [M-04] `SafeTransferLib`를 사용하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Pool` 내의 대부분의 토큰 전송 작업은 이미 `SafeTransferLib`를 사용합니다. 그러나 `removeLiquidity`, `rescue` 및 `skim`에 있는 작업을 포함하여 여러 작업은 여전히 안전하지 않은 전송 방법을 사용합니다. 이러한 전송 작업도 `SafeTransferLib`를 사용하도록 조정하는 것을 고려하십시오.

rescue 함수는 tokens 배열에 나열된 지원되는 토큰을 제외하고 계약으로 잘못 전송된 모든 토큰을 인출하도록 설계되었습니다. 그러나 전송 시 부울 값을 반환하지 않는 비표준 ERC20 토큰(예: USDT)의 경우 함수가 실패합니다. 이로 인해 계약이 되돌려져 해당 토큰을 구하지 못합니다.

```solidity
    function rescue(address token_, address receiver_) external onlyOwner {
        uint256 _numTokens = numTokens;
        for (uint256 t = 0; t < MAX_NUM_TOKENS; t++) {
            if (t == _numTokens) break;
            if (!(token_ != tokens[t])) revert Pool__CannotRescuePoolToken();
        }
        uint256 _amount = ERC20(token_).balanceOf(address(this));
        ERC20(token_).transfer(receiver_, _amount);
    }
```

또한 `Pool.removeLiquidity()`의 현재 구현은 성공적인 전송 시 부울을 반환하지 않는 토큰을 지원하지 않습니다. 문제는 다음 코드에 있습니다.

```solidity
if (!(ERC20(tokens[t]).transfer(receiver_, amount))) revert Pool__TransferFailed();
```

## 권장 사항

이러한 토큰을 처리하려면 `SafeTransferLib.safeTransfer()`를 사용하는 것을 고려하십시오.

# [M-05] 수수료율이 0일 때 스테이킹 주소로 의도하지 않은 토큰 발행

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

풀에 유동성을 추가할 때 계약은 `_calculateSupply`를 두 번 호출합니다. 두 결과의 차이는 발행 수수료로 스테이킹 주소에 발행됩니다. 그러나 수수료율이 0으로 설정된 경우에도 풀 토큰이 여전히 스테이킹 주소로 발행되어 유동성 공급자에게 예상치 못한 손실이 발생합니다.

이 문제는 `_calculateSupply`에 대한 첫 번째 호출은 내림(round down)을 수행하고 두 번째 호출은 올림(round up)을 수행하기 때문에 발생합니다. 결과적으로 `_prevSupply > 0` 및 `mint fee = 0`으로 `addLiquidity` 함수가 호출될 때마다 스테이킹 주소는 소량의 풀 토큰을 받습니다.

```solidity
    function addLiquidity(uint256[] calldata amounts_, uint256 minLpAmount_, address receiver_)
        external
        nonReentrant
        returns (uint256)
    {
        ...
        // mint LP tokens
        (_supply, _virtualBalanceProd) = _calculateSupply(
            _numTokens, _supply, amplification, _virtualBalanceProd, _virtualBalanceSum, _prevSupply == 0 // @audit first call - rounding down
        );
        uint256 _toMint = _supply - _prevSupply;

        if (!(_toMint > 0 && _toMint >= minLpAmount_)) {
            revert Pool__SlippageLimitExceeded();
        }
        PoolToken(tokenAddress).mint(receiver_, _toMint);
        emit AddLiquidity(msg.sender, receiver_, amounts_, _toMint);

        uint256 _supplyFinal = _supply;
        if (_prevSupply > 0) {
            // mint fees
            (_supplyFinal, _virtualBalanceProdFinal) = _calculateSupply(
                _numTokens, _prevSupply, amplification, _virtualBalanceProdFinal, _virtualBalanceSumFinal, true  // @audit second call - rouding up => mint fees even if fee rate is 0
            );
            PoolToken(tokenAddress).mint(stakingAddress, _supplyFinal - _supply);
        ...
```

`_calculateSupply` 함수에서 첫 번째 계산은 내림하여 `_sp - delta`를 산출합니다. 발행 수수료에 대한 두 번째 계산은 올림하여 `_sp + delta`를 산출합니다. 두 계산의 차이인 `2 * delta`는 수수료율이 0인 경우에도 발행 수수료로 스테이킹 주소에 발행됩니다.

```solidity
    function _calculateSupply(
        uint256 numTokens_,
        uint256 supply_,
        uint256 amplification_,
        uint256 virtualBalanceProd_,
        uint256 virtualBalanceSum_,
        bool up_
    ) internal pure returns (uint256, uint256) {
        ...
            if (FixedPointMathLib.rawDiv(FixedPointMathLib.rawMul(_delta, PRECISION), _s) <= MAX_POW_REL_ERR) {
                _delta = FixedPointMathLib.rawDiv(FixedPointMathLib.rawMul(_sp, MAX_POW_REL_ERR), PRECISION);
                if (up_) {
                    _sp += _delta;
                } else {
                    _sp -= _delta;
                }
                return (_sp, _r);
            }

            _s = _sp;
        }
        ...
    }
```

유동성 공급자는 제공된 유동성의 일부가 의도치 않게 스테이킹 주소로 전송됨에 따라 사소하지만 지속적인 가치 손실을 경험합니다. 시간이 지남에 따라 트랜잭션 양이 증가하면 스테이킹 주소에 상당한 양의 토큰이 축적되어 유동성 공급자의 전체 가치가 감소할 수 있습니다.

## 권장 사항

`swapFeeRate`가 0인지 확인하고 발행 수수료의 계산 및 발행을 건너뛰십시오.

# [M-06] 유동성 제거 중 비율 및 가중치가 업데이트되지 않음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

사용자가 유동성을 제거하면 받는 토큰의 양은 각 토큰의 `_rate`를 기준으로 계산됩니다. 또한 `virtualBalanceProd`는 각 토큰의 가중치를 사용하여 다시 계산됩니다.

```solidity
    function removeLiquidity(uint256 lpAmount_, uint256[] calldata minAmounts_, address receiver_)
        external
        nonReentrant
    {
        uint256 _numTokens = numTokens;

        if (minAmounts_.length != _numTokens || minAmounts_.length > MAX_NUM_TOKENS) revert Pool__InvalidParams();
        // update supply
        uint256 _prevSupply = supply;
        uint256 _supply = _prevSupply - lpAmount_;
        supply = _supply;
        PoolToken(tokenAddress).burn(msg.sender, lpAmount_);
        emit RemoveLiquidity(msg.sender, receiver_, lpAmount_);

        // update variables and transfer tokens
        uint256 _virtualBalanceProd = PRECISION;
        uint256 _virtualBalanceSum = 0;

        uint256 _prevVirtualBalance;
        uint256 _rate;
        uint256 _packedWeight;
        for (uint256 t = 0; t <= MAX_NUM_TOKENS; t++) {
            if (t == _numTokens) break;

            (_prevVirtualBalance, _rate, _packedWeight) = _unpackVirtualBalance(packedVirtualBalances[t]);

            uint256 __weight = _unpackWeightTimesN(_packedWeight, 1);

            uint256 dVb = (_prevVirtualBalance * lpAmount_) / _prevSupply;
            uint256 vb = _prevVirtualBalance - dVb;
            packedVirtualBalances[t] = _packVirtualBalance(vb, _rate, _packedWeight);
            // @audit - compare this calculation with other place
>>>         _virtualBalanceProd = FixedPointMathLib.rawDiv(
                FixedPointMathLib.rawMul(
                    _virtualBalanceProd,
                    _powDown(
                        FixedPointMathLib.rawDiv(FixedPointMathLib.rawMul(_supply, __weight), vb),
                        FixedPointMathLib.rawMul(__weight, _numTokens)
                    )
                ),
                PRECISION
            );
            _virtualBalanceSum = FixedPointMathLib.rawAdd(_virtualBalanceSum, vb);

>>>         uint256 amount = (dVb * PRECISION) / _rate;
            if (amount < minAmounts_[t]) revert Pool__SlippageLimitExceeded();
            if (!(ERC20(tokens[t]).transfer(receiver_, amount))) revert Pool__TransferFailed();
        }

        packedPoolVirtualBalance = _packPoolVirtualBalance(_virtualBalanceProd, _virtualBalanceSum);
    }
```

그러나 가중치와 비율이 먼저 업데이트되지 않는 것을 확인할 수 있습니다. 토큰 중 하나의 가치가 갑자기 떨어지는 시나리오에서 문제가 발생하여 비율을 재조정해야 할 수 있습니다. 결과적으로 사용자에게 전송된 토큰은 풀의 비율과 가중치의 현재 상태를 반영하지 않을 수 있습니다.

## 권장 사항

virtualBalanceProd를 계산하기 전에 유동성 제거 작업 내에서 비율과 가중치를 업데이트하는 것을 고려하십시오.

# [M-07] 특정 스테이블 코인을 풀에 추가할 수 없음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

프로토콜은 표준 토큰, 스테이블 코인 및 LRT와 작동하는 것을 목표로 하지만 생성자 및 `addToken` 함수에는 토큰 소수가 18과 같은지 확인하는 확인이 있습니다.

생성자에서:

```solidity
            if (ERC20(tokens_[t]).decimals() != 18) {
                revert Pool__InvalidDecimals();
            }
```

`addToken`에서:

```
        if (ERC20(token_).decimals() != 18) revert Pool__InvalidParams();
```

[USDC](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48) 및 [USDT](https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7)와 같은 가장 인기 있는 스테이블 코인은 18 소수가 아니라 6 소수이므로 풀에 추가할 수 없습니다.

## 권장 사항

소수가 18인지 확인하는 것을 제거할 것을 권장합니다.

# [L-01] `removeLiquidity`의 잘못된 루프 영역

`removeLiquidity` 함수는 < 대신 <= 연산자를 사용합니다. t가 32일 때 즉시 루프를 빠져나오기 때문에 심각한 영향은 없지만 추가 반복이 시도됩니다. 대신 < 연산자를 사용하는 것을 고려하십시오.

```solidity
        for (uint256 t = 0; t <= MAX_NUM_TOKENS; t++) {
            if (t == _numTokens) break;
```

# [L-02] 배포 시 `_nameHash`가 초기화되지 않음

배포 시 계약을 컴파일하는 데 사용되는 solc에 따라 다르기 때문에 지적할 가치가 있다고 생각합니다.

Foundry 구성 파일은 0.8.24를 사용하므로 여기서는 큰 문제가 아니며 허용된 기능은 현재 `nameHash`가 0인 경우 새 `nameHash`를 [생성](https://github.com/Vectorized/solady/blob/7659ab19f1224e398b406f591e53af68dd1dddcd/src/tokens/ERC20.sol#L314)합니다.

그러나 solc 버전 <= 0.8.20은 모든 불변 상태 변수의 초기화 없이 배포를 허용하지 않으며 `_nameHash`가 생성자에서 초기화되지 않으므로 PoolToken.sol은 컴파일할 수 없으므로 배포할 수 없습니다.

```solidity
...........
    bytes32 internal immutable _nameHash;
...........
    constructor(string memory name_, string memory symbol_, uint8 decimals_, address owner_) {
        _name = name_;
        _symbol = symbol_;
        _decimals = decimals_;
        _setOwner(owner_);
    }
```

permit 함수가 어차피 `nameHash`를 생성하므로 제거하는 것이 좋습니다. 프로토콜이 permit 함수에서 이를 대신 사용할 계획이라면 이를 선언하고 쿼리 시 반환하도록 [`_constantNameHash`](https://github.com/Vectorized/solady/blob/7659ab19f1224e398b406f591e53af68dd1dddcd/src/tokens/ERC20.sol#L285) 함수를 생성할 수 있습니다.

# [L-03] 중앙화로 인한 위험

많은 함수가 `onlyOwner` 수정자에 의해 보호되며 악의적이거나 손상된 관리자가 조치를 취할 수 있습니다. 그러나 일부 함수는 잠재적인 영향으로부터 사용자를 보호하기 위해 보호 장치를 마련하고 살펴볼 가치가 있습니다.

1. `setDepositFeeBps`는 사용자에게 슬픔(grief)을 주기 위해 매우 높게 설정될 수 있으며 100% 미만일 수 있습니다. 수수료 bps에 더 합리적인 상한선을 도입하십시오. 아마도 20%/25% 상한선일 것입니다.

```solidity
    function setDepositFeeBps(uint256 depositFeeBps_) external onlyOwner {
        if (depositFeeBps >= 1e4) revert Staking__InvalidParams();
        depositFeeBps = depositFeeBps_;
    }
```

2. `setPool`은 poolToken 발행/소각을 막기 위해 0 주소로 설정할 수 있습니다. 악의적인 소유자는 풀 주소를 외부에서 제어되는 주소로 설정하여 누구에게서나 임의로 토큰을 발행하고 소각할 수도 있습니다. 이에 대한 잠재적인 해결책은 풀 주소를 설정한 직후 소유권을 포기하고 0 주소 확인을 추가하는 것입니다.

```solidity
    function setPool(address poolAddress_) public onlyOwner {
        poolAddress = poolAddress_;

        emit PoolAddressSet(poolAddress);
    }
```

# [L-04] 첫 번째 유동성 공급자 세트의 수수료가 손실될 수 있음

사용자가 `addLiquidity` 함수를 호출하면 수수료가 `stakingAddress`에 발행됩니다. 스테이킹 주소는 다른 매개변수와 달리 생성자에서 설정되지 않으므로 배포와 관리자가 `setStaking` 함수를 호출하는 사이의 기간 내에 `addLiquidity` 함수를 호출하면 0 주소로 수수료가 발행될 위험이 있습니다.

```solidity
    function addLiquidity(uint256[] calldata amounts_, uint256 minLpAmount_, address receiver_)
        external
        nonReentrant
        returns (uint256)
    {
..........
        if (_prevSupply > 0) {
            // mint fees
            (_supplyFinal, _virtualBalanceProdFinal) = _calculateSupply(
                _numTokens, _prevSupply, amplification, _virtualBalanceProdFinal, _virtualBalanceSumFinal, true
            );
            PoolToken(tokenAddress).mint(stakingAddress, _supplyFinal - _supply);
..........
    }
```

권장 사항은 생성자에서 `setStaking` 함수를 호출하거나 stakingAddress가 0인 경우 발행을 건너뛰는 것입니다.

# [L-05] `tokenAddress_` 설정 시 온전성 확인 누락

Pool 계약의 생성자에는 제공된 tokenAddress\_가 유효한지 확인하는 온전성 확인이 없습니다. 참고로 Yearn Finance에는 다음과 같은 유효성 검사가 포함되어 있습니다.

```solidity
assert _token != empty(address)
```

배포 중 오류를 방지하려면 유사한 확인을 추가하는 것을 고려하십시오.

```solidity
        if (tokenAddress_ == address(0)) revert Pool__InvalidParams();
```

# [L-06] 사용자는 청크 단위로 입금하여 수수료를 우회할 수 있음

사용자가 Staking 계약에 토큰을 입금하면 수수료가 부과되어 `protocolFeeAddress`에 발행됩니다.

```solidity
        uint256 _fee = (shares_ * depositFeeBps) / 1e4;
        _mint(to_, shares_ - _fee);
        _mint(protocolFeeAddress, _fee);
```

사용자는 작고 조각난 예금을 만들어 수수료를 0으로 내림(round down)하여 이 계산을 악용할 수 있습니다. 이 문제는 [2 소수](https://etherscan.io/token/0x056Fd409E1d7A124BD7017459dFEa2F387b6d5Cd#readContract#F3)를 갖는 [GUSD](https://etherscan.io/token/0x056Fd409E1d7A124BD7017459dFEa2F387b6d5Cd)와 같이 소수점 정밀도가 낮은 토큰에 특히 문제가 됩니다. 다음과 같이 입금 함수를 업데이트하는 것을 고려하십시오.

```solidity
        uint256 feeBps = depositFeeBps;
        require((shares_ * feeBps) > 1e4);
        uint256 _fee = (shares_ * feeBps) / 1e4;
        _mint(to_, shares_ - _fee);
        _mint(protocolFeeAddress, _fee);
    }
```

# [L-07] 스왑 실행 시 마감일 매개변수 없음

스왑 실행 시 마감일 매개변수가 없어 채굴자가 트랜잭션을 보류하고 나중에 실행하여 사용자에게 불리한 거래를 할 수 있습니다.

# [L-08] 반올림 방향이 잘못될 수 있음

`_powUp/Down` 함수는 반올림 방향에 대해 예상되는 대로 작동하지 않을 수 있습니다.

```solidity
            // compute x^n
            if (n_ % 2 != 0) {
                // n is odd
                _output = FixedPointMathLib.rawDiv(FixedPointMathLib.rawMul(_output, x_), PRECISION);
            }
```

x의 거듭제곱을 계산할 때 중간 값은 항상 `rawDiv`를 사용하여 내림됩니다. 결과적으로 `_powUp`은 예상되는 상한보다 약간 작을 수 있습니다.
