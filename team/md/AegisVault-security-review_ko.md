# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**labs-solo/aegis-vault** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Aegis Vault 소개

Aegis Vault는 고급 전략을 사용하여 한 유형의 토큰을 다른 유형으로 자동 변환합니다. Uniswap V3 풀과 통합하여 유동성을 최적화하고 사용자가 선호도에 따라 다양한 방식으로 자금을 인출할 수 있도록 합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [7f8badf33b62725acdd837ddac1d4819736b2ed1](https://github.com/labs-solo/aegis-vault/tree/7f8badf33b62725acdd837ddac1d4819736b2ed1)

_수정 검토 커밋 해시_ - [1171f4c4cf9578d4addc00905a5085bd7299a4ff](https://github.com/labs-solo/aegis-vault/tree/1171f4c4cf9578d4addc00905a5085bd7299a4ff)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `AegisVault`
- `AegisVaultERC20`
- `AegisVaultFactory`
- `AegisVaultCore`
- `Constants`
- `ERC20Initializable`
- `SymbolLib`
- `UV3Math`
- `PctMath`

# 발견 사항

# [M-01] 변동성 확인을 수행하는 작업에 대한 DoS

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`AegisVaultCore.sol`의 `_checkVolatility` 함수는 입금 및 대상 볼트의 보류 중인 보유 비율이 특정 임계값(초기 1%로 설정)을 초과하지 않도록 요구합니다.

```solidity
    require(
        _pendingHoldingsRatio(depositVault, depositSpotPrice) <= pendingThreshold
            && _pendingHoldingsRatio(targetVault, targetSpotPrice) <= pendingThreshold,
        "PDT"
    );
```

이 내부 함수는 입금, 재조정 및 `TargetOnly` 유형의 인출 시 실행됩니다.

악의적인 행위자는 트랜잭션 전에 ICHI 볼트 중 하나에 토큰을 입금하여 트랜잭션이 되돌려지게 한 다음, 트랜잭션이 되돌려진 후 토큰을 인출함으로써 이러한 작업에 대해 DoS를 수행할 수 있습니다.

또한 ICHI 볼트에서의 인출은 Uniswap 풀과 볼트의 미사용 자금에서 비례적으로 토큰을 인출합니다. 즉, 악의적인 행위자가 인출한 후에도 보류 중인 보유 비율이 여전히 임계값을 초과하여 추가 트랜잭션이 되돌려질 수 있습니다.

## 개념 증명

다음 코드를 `deposit.t.sol`에 복사하십시오.

```solidity
function test_DoS_Deposit() public {
    // 1. Initial setup
    bytes32 depositorRoleId = aegisVaultCore.DEPOSITOR_ROLE();
    aegisVaultCore.grantRole(depositorRoleId, users.alice);

    vm.startPrank({ msgSender: users.alice });

    uint32 one_period = defaults.AEGIS_TWAP_PERIOD();
    skip(one_period);
    int24 finalTick = _swapToCrossTicks(true, 30);

    uint256 deposit0 = isToken0Deposit ? 1e16 : 0;
    uint256 deposit1 = isToken0Deposit ? 0 : 1e16;
    address to = users.alice;

    depositVault.deposit(deposit0, deposit1, to);
    targetVault.deposit(deposit1, deposit0, to);

    int24 baseLower = getClosestLowTick(-TICKS_100_PCT, tickSpacing);
    int24 baseUpper = getClosestLowTick(finalTick, tickSpacing);
    int24 limitUpper = getClosestHighTick(baseUpper + TICKS_100_PCT, tickSpacing);

    vm.startPrank({ msgSender: users.owner });
    depositVault.rebalance(baseLower, baseUpper, baseUpper + tickSpacing, limitUpper, 0);
    targetVault.rebalance(baseLower, baseUpper, baseUpper + tickSpacing, limitUpper, 0);

    vm.startPrank({ msgSender: users.alice });
    IERC20(address(depositVault)).approve({ spender: address(aegisVaultCore), amount: MAX_UINT256 });
    uint256 notTooLittleDepositToken = 510;

    // 2. Bob front-runs Alice's deposit, increasing the pending holdings ratio in the deposit vault
    vm.startPrank({ msgSender: users.bob });
    uint256 ichiShares = depositVault.deposit(deposit0, deposit1, users.bob);

    // 3. Alice's deposit reverts
    vm.startPrank({ msgSender: users.alice });
    expectRevert("PDT");
    aegisVaultCore.deposit(notTooLittleDepositToken, 505, users.alice, true);

    // 4. Bob withdraws his deposit from the deposit vault
    vm.startPrank({ msgSender: users.bob });
    depositVault.withdraw(ichiShares, users.bob);

    // 5. Pending holdings ratio is still above the threshold, so deposits continue to revert
    vm.startPrank({ msgSender: users.alice });
    expectRevert("PDT");
    aegisVaultCore.deposit(notTooLittleDepositToken, 505, users.alice, true);
}
```

## 권장 사항

옵션:

1. 이 검사를 제거하는 것이 DoS 공격의 영향보다 낮은지 평가하고, 그렇다면 제거하거나 최소한 Aegis 볼트 소유자가 비활성화할 수 있도록 허용하는 것이 좋습니다.
2. 유동성이 낮은 볼트의 경우 `pendingThreshold`를 높여 이러한 공격에 더 많은 비용이 들게 하는 것을 고려하십시오. 예를 들어 볼트 가치가 1000e18 미만인 경우 `pendingThreshold`를 10%로 설정하는 것이 더 적절할 수 있습니다.
3. 입금에 대해 `_pendingHoldingsRatio` 확인을 건너뛰거나, 재조정 확인에 사용되는 `pendingThreshold`보다 덜 제한적인 별도의 `pendingThreshold`를 입금 확인에 사용하는 것을 고려하십시오.

## Solo labs 의견

_이러한 유형의 DoS 공격은 공격자에게 비용을 부과한다는 점에 유의하십시오. ICHI 볼트에 대한 입금은 비관적인 가격(현물 가격 또는 TWAP 중 덜 유리한 가격)을 사용하여 평가되므로 풀에 변동성이 있는 경우 공격자는 약간의 슬리피지를 겪게 됩니다. 이러한 고유한 보호 외에도 Aegis 소유자는 입금을 일시적으로 중단하고 재조정하기 전에 pendingThreshold를 높여 수동 재조정을 실행할 수도 있습니다._

# [M-02] Merkl distributor의 청구가 실패할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`AegisVaultCore.claim`을 통해 관리자는 Merkl distributor 계약에서 보상을 청구하고 청구된 토큰을 `recipient` 주소로 전송할 수 있습니다.

```solidity
735     IMerklDistributor(merkleDistributor).claim(users, tokens, amounts, proofs);
736
737     // Iterate over the list of token addresses and transfer the corresponding amounts to the recipient
738     for (uint256 i = 0; i < tokens.length; i++) {
739         IERC20(tokens[i]).safeTransfer(recipient, amounts[i]);
740     }
```

보시다시피 토큰에 대해 청구된 금액과 전송된 금액은 동일합니다. 그러나 [`Distributor.claim` 함수](https://github.com/AngleProtocol/merkl-contracts/blob/af7f5ae855acccf594b1f93be9cab4a06da22536/contracts/Distributor.sol#L194-L197)의 코드를 살펴보면 항상 그런 것은 아님을 알 수 있습니다.

```solidity
194         uint256 toSend = amount - claimed[user][token].amount;
195         claimed[user][token] = Claim(SafeCast.toUint208(amount), uint48(block.timestamp), getMerkleRoot());
196
197         IERC20(token).safeTransfer(user, toSend);
```

Merkle 트리의 리프는 사용자 주소, 토큰 주소 및 자격이 있는 금액의 keccak 해시로 구성되므로 동일한 토큰에 대해 동일한 사용자에게 여러 번 청구를 제공하기 위해 이전 청구 금액을 증가시키는 새 리프가 생성됩니다. 따라서 Aegis 볼트가 특정 토큰에 대해 보상을 청구하는 경우 후속 청구로 인해 트랜잭션이 되돌려집니다.

또한 수수료가 포함된 전송 토큰의 경우 수령된 금액이 Distributor 계약에 의해 전송된 금액보다 적을 수 있습니다. 따라서 이러한 종류의 토큰의 경우 첫 번째 청구에서도 트랜잭션이 되돌려질 것입니다.

## 개념 증명

- Aegis 볼트는 Merkle distributor로부터 100 XYZ 토큰을 청구할 자격이 있습니다.
- 관리자가 `claim` 함수를 호출하고 100 XYZ 토큰을 청구합니다.
- 50개의 추가 XYZ 토큰이 Aegis 볼트에 배포되므로 Merkle 트리의 새 리프는 `keccak256(abi.encode(aegisVaultAddress, xyzAddress, 150e18))`이 됩니다.
- 관리자가 `150e18` 금액으로 `claim` 함수를 다시 호출하고 50 XYZ 토큰이 Aegis 볼트로 전송됩니다.
- `IERC20(xyzAddress).safeTransfer(recipient, 150e18)`는 Aegis 볼트가 50 XYZ 토큰만 가지고 있기 때문에 되돌려집니다.

## 권장 사항

```diff
+       // We could just transfer all available balance of tokens after claim, but just in case reward token is depositVault,
+       // targetVault, depositToken or targetToken, we ensure that only the increase in balance is withdrawn.
+       uint256[] balancesBefore = new uint256[](tokens.length);
+       for (uint256 i = 0; i < tokens.length; i++) {
+           balancesBefore[i] = IERC20(tokens[i]).balanceOf(address(this));
+       }
+
        IMerklDistributor(merkleDistributor).claim(users, tokens, amounts, proofs);

        // Iterate over the list of token addresses and transfer the corresponding amounts to the recipient
        for (uint256 i = 0; i < tokens.length; i++) {
-           IERC20(tokens[i]).safeTransfer(recipient, amounts[i]);
+           uint256 balanceIncrease = IERC20(tokens[i]).balanceOf(address(this)).sub(balancesBefore[i]);
+           IERC20(tokens[i]).safeTransfer(recipient, balanceIncrease);
        }
```

# [M-03] `deposit0Max`/`deposit1Max`를 초과하는 금액으로 ICHI 볼트에 재입금 시 되돌리기

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`withdraw` 함수를 통해 사용자는 인출 유형으로 `TargetOnly`를 선택할 수 있습니다. 이 옵션을 선택하면 사용자는 대상 토큰만 받고 `_withdrawAssetMix` 작업에서 인출된 입금 토큰은 입금 볼트에 재입금됩니다.

```solidity
    function _withdraw(
        uint256 aegisShares,
        uint256 aegisTotalSupply,
        address to,
        WithdrawSlippageData memory minSlippage,
        WithdrawType withdrawType
    )
        internal
        returns (WithdrawSlippageData memory actualSlippage, uint256 aegisSharesWithdrawn)
    {
        // ...

        } else if (withdrawType == WithdrawType.TargetOnly) {
            // NOTE: despite the possibility of a re-deposit on WithdrawType.TargetOnly
            // we don't check _onlyDepositor as it's not a standard deposit
            // NOTE: we _checkVolatility before _withdrawAssetMix to avoid oracle updates due to burns&mints in underlying pools
            // which could result in the ICHIVault re-deposit(i.e. in _depositDepositToken) failing on the hysteresis check
            (uint256 depositSpotPrice, uint256 targetSpotPrice) = _checkVolatility();

            // the AegisVault initially custodies both tokens, then transfers the targetTokens to the recipient
            // and then re-deposits the depositTokens.
            // NOTE: the withdrawFee is charged identically across all WithdrawTypes
            // so in the worst case of a TargetOnly withdrawal(i.e. the user's position has no targetToken and only has depositToken)
            // the user would essentially be paying a withdrawFee for nothing in return
>>>         (actualSlippage.depositTokenAmount, actualSlippage.targetTokenAmount) =
                _withdrawAssetMix(aegisShares, address(this), aegisTotalSupply);

            targetToken.safeTransfer(to, actualSlippage.targetTokenAmount);

            // after _withdrawAssetMix we've temporarily burned all withdraw aegisShares
            // however we then calculate the deposit aegisShares that would normally be minted on the deposit below
            // NOTE: (deposit aegisShares <=  withdraw aegisShares) is necessarily logically true
            // so we effectively only need to burn the difference as calculated in aegisSharesWithdrawn
            uint256 newTotalSupply = aegisTotalSupply.sub(aegisShares);

>>>         actualSlippage.depositSharesAmount =
                _depositDepositToken(actualSlippage.depositTokenAmount, true, newTotalSupply, depositSpotPrice, targetSpotPrice);

            aegisSharesWithdrawn = aegisShares.sub(actualSlippage.depositSharesAmount);
        }
```

그러나 이것은 두 ICHI 볼트에서 반환된 `actualSlippage.depositTokenAmount`가 `deposit0Max`/`deposit1Max`를 초과하는 시나리오를 설명하지 않습니다. 이 경우 입금 ICHI 볼트의 `deposit0Max`/`deposit1Max`를 초과하는 금액을 재입금하려고 시도하여 사용자의 인출 작업이 예기치 않게 되돌려질 수 있습니다.

```solidity
    function deposit(
        uint256 deposit0,
        uint256 deposit1,
        address to
    ) external override nonReentrant returns (uint256 shares) {
        require(allowToken0 || deposit0 == 0, "IV.deposit: token0 not allowed");
        require(allowToken1 || deposit1 == 0, "IV.deposit: token1 not allowed");
        require(deposit0 > 0 || deposit1 > 0, "IV.deposit: deposits must be > 0");
>>>     require(deposit0 < deposit0Max && deposit1 < deposit1Max, "IV.deposit: deposits too large");
        require(to != NULL_ADDRESS && to != address(this), "IV.deposit: to");

        // ...
    }
```

## 권장 사항

재입금할 `actualSlippage.depositTokenAmount`가 `deposit0Max`/`deposit1Max`를 초과하는 경우 단일 입금 작업을 여러 트랜잭션으로 나누어 예기치 않은 되돌리기를 방지하십시오.

## Solo labs 의견

_depositMax0 및 depositMax1 설정은 볼트 소유자가 ICHI 볼트 수준에서 구성합니다. Aegis는 설정된 depositMax0 또는 depositMax1 제약 조건이 있는 ICHI 볼트에 입금을 시도하는 모든 트랜잭션을 되돌려 이러한 제한을 준수합니다. 이는 직접 토큰 입금과 TargetOnly 인출 중 재입금 모두에 적용됩니다. 그러나 Aegis 예금자는 다른 사용 가능한 인출 옵션을 사용하여 인출할 수 있으므로 자금이 잠기지 않습니다._

# [M-04] 0 금액을 재입금하려고 시도할 때 되돌리기

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`withdraw` 함수를 통해 사용자는 인출 유형으로 `TargetOnly`를 선택할 수 있습니다. 이 옵션을 선택하면 사용자는 대상 토큰만 받고 `_withdrawAssetMix` 작업에서 인출된 입금 토큰은 입금 볼트에 재입금됩니다.

```solidity
    function _withdraw(
        uint256 aegisShares,
        uint256 aegisTotalSupply,
        address to,
        WithdrawSlippageData memory minSlippage,
        WithdrawType withdrawType
    )
        internal
        returns (WithdrawSlippageData memory actualSlippage, uint256 aegisSharesWithdrawn)
    {
        // ...

        } else if (withdrawType == WithdrawType.TargetOnly) {
            // NOTE: despite the possibility of a re-deposit on WithdrawType.TargetOnly
            // we don't check _onlyDepositor as it's not a standard deposit
            // NOTE: we _checkVolatility before _withdrawAssetMix to avoid oracle updates due to burns&mints in underlying pools
            // which could result in the ICHIVault re-deposit(i.e. in _depositDepositToken) failing on the hysteresis check
            (uint256 depositSpotPrice, uint256 targetSpotPrice) = _checkVolatility();

            // the AegisVault initially custodies both tokens, then transfers the targetTokens to the recipient
            // and then re-deposits the depositTokens.
            // NOTE: the withdrawFee is charged identically across all WithdrawTypes
            // so in the worst case of a TargetOnly withdrawal(i.e. the user's position has no targetToken and only has depositToken)
            // the user would essentially be paying a withdrawFee for nothing in return
>>>         (actualSlippage.depositTokenAmount, actualSlippage.targetTokenAmount) =
                _withdrawAssetMix(aegisShares, address(this), aegisTotalSupply);

            targetToken.safeTransfer(to, actualSlippage.targetTokenAmount);

            // after _withdrawAssetMix we've temporarily burned all withdraw aegisShares
            // however we then calculate the deposit aegisShares that would normally be minted on the deposit below
            // NOTE: (deposit aegisShares <=  withdraw aegisShares) is necessarily logically true
            // so we effectively only need to burn the difference as calculated in aegisSharesWithdrawn
            uint256 newTotalSupply = aegisTotalSupply.sub(aegisShares);

>>>         actualSlippage.depositSharesAmount =
                _depositDepositToken(actualSlippage.depositTokenAmount, true, newTotalSupply, depositSpotPrice, targetSpotPrice);

            aegisSharesWithdrawn = aegisShares.sub(actualSlippage.depositSharesAmount);
        }
```

그러나 이것은 ICHI 볼트가 `actualSlippage.targetTokenAmount`만 반환하고 `actualSlippage.depositTokenAmount`(0과 동일)를 반환하지 않는 시나리오를 고려하지 않습니다. 이는 두 ICHI 볼트가 현재 활성 범위를 벗어난 UniV3 틱에 유동성을 제공할 때 발생할 수 있습니다. 이 경우 ICHI 볼트 입금 작업에서 두 토큰 모두에 대해 0 금액이 제공되기 때문에 작업이 되돌려집니다.

```solidity
    function deposit(
        uint256 deposit0,
        uint256 deposit1,
        address to
    ) external override nonReentrant returns (uint256 shares) {
        require(allowToken0 || deposit0 == 0, "IV.deposit: token0 not allowed");
        require(allowToken1 || deposit1 == 0, "IV.deposit: token1 not allowed");
>>>     require(deposit0 > 0 || deposit1 > 0, "IV.deposit: deposits must be > 0");
        require(deposit0 < deposit0Max && deposit1 < deposit1Max, "IV.deposit: deposits too large");
        require(to != NULL_ADDRESS && to != address(this), "IV.deposit: to");

        // ...
    }
```

## 권장 사항

조건을 추가하여 `actualSlippage.depositTokenAmount`가 0보다 큰 경우에만 입금 작업을 수행하도록 고려하십시오.

```diff
    function _withdraw(
        uint256 aegisShares,
        uint256 aegisTotalSupply,
        address to,
        WithdrawSlippageData memory minSlippage,
        WithdrawType withdrawType
    )
        internal
        returns (WithdrawSlippageData memory actualSlippage, uint256 aegisSharesWithdrawn)
    {
        // ...

        } else if (withdrawType == WithdrawType.TargetOnly) {
            // ...
            (uint256 depositSpotPrice, uint256 targetSpotPrice) = _checkVolatility();

            // ...
            (actualSlippage.depositTokenAmount, actualSlippage.targetTokenAmount) =
                _withdrawAssetMix(aegisShares, address(this), aegisTotalSupply);

            targetToken.safeTransfer(to, actualSlippage.targetTokenAmount);

            // ...
            uint256 newTotalSupply = aegisTotalSupply.sub(aegisShares);

+          if (actualSlippage.depositTokenAmount > 0) {
                  actualSlippage.depositSharesAmount =
                      _depositDepositToken(actualSlippage.depositTokenAmount, true, newTotalSupply, depositSpotPrice, targetSpotPrice);
+           }
            aegisSharesWithdrawn = aegisShares.sub(actualSlippage.depositSharesAmount);
        }
```

# [M-05] `performUpkeep`이 되돌려질 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`performUpkeep`이 호출되면 입금 볼트에서 지분의 일정 비율을 인출한 다음 인출된 토큰을 입금 및 대상 볼트에 따라 입금합니다.

```solidity
    function _rebalance(uint256 rebalancePct) private {
        require(rebalancePct > 0 && rebalancePct <= maxRebalanceThreshold, "IIN");

        // don't do rebalance during high volatility
        (uint256 depositSpotPrice,) = _checkVolatility();

        // don't rebalance if there are too few target tokens in the depositVault
        require(_targetTokenRatio(depositSpotPrice) >= excessTargetTokenThreshold, "ITT");

        // calculate how many shares to withdraw from the depositVault
        uint256 toWithdraw = PctMath.pctOf(depositVault.balanceOf(address(this)), rebalancePct);

        // withdraw shares from the depositVault
>>>     depositVault.withdraw(toWithdraw, address(this));

        // take a portion of targetToken as fees before depositing them to the targetVault
        uint256 fees = _distributeTargetTokenFees();

        // get pending token amounts
        uint256 depositTokenBalance = depositToken.balanceOf(address(this));
        uint256 targetTokenBalance = targetToken.balanceOf(address(this));

        // @audit - this could also revert when one of them is empty
        // deposit pending depositToken to the depositVault
>>>     depositVault.deposit(isInverted ? 0 : depositTokenBalance, isInverted ? depositTokenBalance : 0, address(this));

        // deposit pending targetToken to the targetVault
>>>     targetVault.deposit(isInverted ? targetTokenBalance : 0, isInverted ? 0 : targetTokenBalance, address(this));

        emit Rebalance(rebalancePct, toWithdraw, depositTokenBalance, targetTokenBalance, fees);
    }
```

그러나 이것은 ICHI 볼트에서 인출된 모든 자산이 `targetToken`으로만 구성된 시나리오를 설명하지 않습니다. 이는 두 ICHI 볼트가 현재 활성 범위를 벗어난 UniV3 틱에 유동성을 제공할 때 발생할 수 있습니다. 이 경우 ICHI 볼트의 `depositVault.deposit` 작업에서 두 토큰 모두에 대해 0 금액이 제공되기 때문에 작업이 되돌려집니다.

## 권장 사항

`depositTokenBalance`가 0이면 입금 작업을 건너뜁니다. `_targetTokenRatio` 확인이 0이 아닌 `targetTokenBalance`를 보장해야 하므로 `targetTokenBalance` 확인은 필요하지 않습니다.

# [M-06] `MIN_INITIAL_DEPOSIT` 우회

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

사용자가 Aegis 볼트에 처음 입금할 때(`aegisTotalSupply`가 0임) 작업은 주조된 `aegisShares`가 `MIN_INITIAL_DEPOSIT`보다 큰지 확인합니다. 이는 첫 번째 입금자의 공격을 방지하기 위해 설계되었습니다.

```solidity
    function __deposit(
        uint256 depositSharesAmount,
        uint256 aegisTotalSupply,
        uint256 depositSpotPrice,
        uint256 targetSpotPrice
    )
        private
        returns (uint256 aegisShares)
    {
        // ...
        if (aegisTotalSupply == 0) {
            // better to use userDepositValue than userValueContribution here, because userValueContribution could be very small
            aegisShares = ctx.userDepositValue;
            // simplified check for initial deposit
            // @audit - can this exploited by withdrawing immediately until aegisShares lower than minimum
>>>         require(aegisShares >= MIN_INITIAL_DEPOSIT, "VTS");
        } else {
            /// @dev invariant check for guaranteed safety, since at this point the AegisVault has received any ICHIVault shares
            /// either from a depositToken deposit or for a depositVault share transfer therefore given existing prior deposits
            /// the user's contribution to the whole must necessarily be less than the whole i.e. PRECISION i.e. 1e18
            require(ctx.userValueContribution < PRECISION, "VTS");
            aegisShares = _mulDiv(ctx.userValueContribution, aegisTotalSupply, PRECISION.sub(ctx.userValueContribution));
        }
    }
```

그러나 인출 작업에서 남은 지분이나 총 공급량이 `MIN_INITIAL_DEPOSIT`보다 큰지 확인하는 검사가 없으므로 첫 번째 입금자 공격을 수행할 수 있을 때까지 즉시 지분을 인출하여 이를 쉽게 우회할 수 있습니다.

## 권장 사항

`withdraw` 작업 후 새 총 공급량을 확인하십시오. 0이 아니고 `MIN_INITIAL_DEPOSIT`보다 낮으면 작업을 되돌립니다.

```diff
    function _withdraw(
        uint256 aegisShares,
        uint256 aegisTotalSupply,
        address to,
        WithdrawSlippageData memory minSlippage,
        WithdrawType withdrawType
    )
        internal
        returns (WithdrawSlippageData memory actualSlippage, uint256 aegisSharesWithdrawn)
    {
        // ...

+      uint256 newAegisTotalSupply = aegisTotalSupply - aegisSharesWithdrawn;
+      require(newAegisTotalSupply == 0  || newAegisTotalSupply >= MIN_INITIAL_DEPOSIT, "VTS");
        _checkWithdrawSlippage(actualSlippage, minSlippage);
        emit Withdraw(msg.sender, to, aegisShares, actualSlippage);
    }
```

# [L-01] 특정 경우 소유자가 토큰을 스윕할 수 있음

AegisVaultCore에는 계약에서 볼트가 아닌 토큰을 복구하도록 설계된 `sweepExtraTokens()` 함수가 포함되어 있습니다.

```solidity
    function sweepExtraTokens(address _token, address _recipient) external override {
        _onlyAdmin();
        require(
            _token != address(depositVault) && _token != address(targetVault) && _token != address(depositToken)
                && _token != address(targetToken),
            "WTK"
        );

        IERC20 token = IERC20(_token);
        uint256 tokenBalance = token.balanceOf(address(this));
        require(tokenBalance > 0, "ZBL");
        token.safeTransfer(_recipient, tokenBalance);

        emit SweepExtraTokens(msg.sender, _token, _recipient, tokenBalance);
    }
```

소유자가 볼트 토큰을 구조하려고 하면 함수가 "WTK" 오류를 발생시킵니다. 그러나 토큰에 여러 진입점이 있는 경우 소유자가 이 확인을 우회할 수 있습니다. 예를 들어, 보조 진입점이 여전히 활성 상태였을 때 TUSD 토큰의 경우가 그랬습니다. 여러 진입점이 있는 다른 토큰이 존재할 수 있으므로 이 취약점은 위험으로 남아 있습니다. 이 문제에 대한 직접적인 수정은 없지만 토큰에 여러 진입점이 있는 경우 소유자가 모든 토큰을 인출할 수 있음을 문서화하는 것이 중요합니다.

## Solo labs 의견

_Aegis 소유자는 표준 ERC20 토큰으로 구현된 경우 입금 및 대상 토큰을 복구할 수 없지만, 이러한 토큰에 여러 진입점이 있는 경우 복구가 잠재적으로 가능할 수 있습니다. 그러나 이러한 토큰은 드물고 일반적으로 ICHI 볼트에서 사용되지 않으므로 이 시나리오는 매우 드뭅니다. 이러한 토큰을 처리하는 Aegis 계약이 필요한 경우 사례별로 평가됩니다._

# [L-02] 후크가 있는 토큰 사용

Aegis 볼트에 입금할 때 예금자가 받은 지분 계산에 사용되는 현물 가격을 가져오는 변동성 확인이 있습니다. 이 확인은 입금이 이루어지기 전에 발생하며 [checks-effects-interactions 패턴](https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html)을 위반합니다.

입금 토큰이 전송 시 후크를 구현하는 경우 예금자는 Uniswap 풀의 현물 가격을 조작할 수 있지만 이전 가격은 여전히 받은 지분 계산에 사용됩니다.

동일한 원칙이 `TargetOnly` 유형의 인출에도 적용됩니다.

변동성 확인을 토큰이 전송된 후로 이동하거나 전송 시 후크를 구현하는 토큰은 프로토콜에서 사용해서는 안 된다는 점을 문서화하는 것을 고려하십시오.

## Solo labs 의견

_Aegis는 풀에서 가격 조작으로 이어질 수 있는 전송 후크가 있는 토큰과 함께 사용되지 않습니다._

# [L-03] AegisVaultFactory는 ICHI 볼트가 단면인지 확인하지 않음

예상 동작 설명에는 다음이 명시되어 있습니다.

> 볼트는 단면입니다. 즉, 입금 볼트는 입금 토큰만 허용하고 대상 볼트는 대상 토큰만 허용합니다.

그러나 `AegisVaultFactory.createAegisVault`는 ICHI 볼트가 단면인지 확인하기 위해 어떠한 검사도 수행하지 않습니다. 동일한 ICHI 볼트가 token0과 token1 모두에 대한 입금 볼트로 사용되는 경우 Aegis 볼트가 두 토큰 간에 지속적으로 재조정될 수 있으므로 예기치 않은 동작이 발생할 수 있습니다.

`AegisVaultFactory.sol`에 다음 변경 사항을 적용하는 것을 고려하십시오.

```diff
        require(
            createAegisVaultParams.depositToken == dToken0
-               ? IICHIVault(createAegisVaultParams.depositVault).allowToken0() && IICHIVault(createAegisVaultParams.targetVault).allowToken1()
+               ? IICHIVault(createAegisVaultParams.depositVault).allowToken0() && IICHIVault(createAegisVaultParams.targetVault).allowToken1() &&
+                 !IICHIVault(createAegisVaultParams.depositVault).allowToken1() && !IICHIVault(createAegisVaultParams.targetVault).allowToken0()
-               : IICHIVault(createAegisVaultParams.depositVault).allowToken1() && IICHIVault(createAegisVaultParams.targetVault).allowToken0(),
+               : IICHIVault(createAegisVaultParams.depositVault).allowToken1() && IICHIVault(createAegisVaultParams.targetVault).allowToken0() &&
+                 !IICHIVault(createAegisVaultParams.depositVault).allowToken0() && !IICHIVault(createAegisVaultParams.targetVault).allowToken1(),
            "WDT"
        )
```

# [L-04] 사용자가 대상 토큰 수수료 지불을 피할 수 있음

리밸런서 키퍼가 `AegisVaultCore.performUpKeep`을 호출하면 재조정되는 대상 토큰에 대해 수수료가 적용되고 수수료는 `feeRecipient` 주소로 전송됩니다.

사용자는 재조정이 트리거되기 전에 인출 함수를 호출하고 재조정이 완료된 후 토큰을 다시 입금하여 수수료 분담을 피할 수 있습니다.

전략의 수익성은 기본 수수료(초기 1%), 인출 수수료(초기 0), 재조정된 대상 토큰의 양, 총 공급량 대비 사용자가 소유한 지분 비율, 기본 풀의 토큰 가격, 가스 가격 등 여러 요인에 따라 달라진다는 점에 유의하는 것이 중요합니다.

이 문제를 완화하는 가장 간단한 방법은 기본 수수료가 인출 수수료보다 훨씬 높지 않은지 확인하는 것입니다.

## Solo labs 의견

_baseFee와 withdrawFee는 모두 기본적으로 0%로 설정됩니다. 특정 Aegis 계약에 대해 baseFee를 늘려야 하는 경우 Aegis 관리자는 이 점을 고려하여 withdrawFee를 그에 따라 조정할 수 있습니다._

# [L-05] `performUpkeep` 내부에서 주조된 지분 슬리피지 부족

`performUpkeep` 작업 내부에서는 입금 볼트에서 지분의 일정 비율을 인출하고(대상 비율이 충족되면) 토큰을 입금 및 대상 ICHI 볼트에 따라 입금합니다.

```solidity
    function _rebalance(uint256 rebalancePct) private {
        require(rebalancePct > 0 && rebalancePct <= maxRebalanceThreshold, "IIN");

        // don't do rebalance during high volatility
        (uint256 depositSpotPrice,) = _checkVolatility();

        // don't rebalance if there are too few target tokens in the depositVault
        require(_targetTokenRatio(depositSpotPrice) >= excessTargetTokenThreshold, "ITT");

        // calculate how many shares to withdraw from the depositVault
        uint256 toWithdraw = PctMath.pctOf(depositVault.balanceOf(address(this)), rebalancePct);

        // withdraw shares from the depositVault
        depositVault.withdraw(toWithdraw, address(this));

        // take a portion of targetToken as fees before depositing them to the targetVault
        uint256 fees = _distributeTargetTokenFees();

        // get pending token amounts
        uint256 depositTokenBalance = depositToken.balanceOf(address(this));
        uint256 targetTokenBalance = targetToken.balanceOf(address(this));

        // deposit pending depositToken to the depositVault
        depositVault.deposit(isInverted ? 0 : depositTokenBalance, isInverted ? depositTokenBalance : 0, address(this));

        // deposit pending targetToken to the targetVault
        targetVault.deposit(isInverted ? targetTokenBalance : 0, isInverted ? 0 : targetTokenBalance, address(this));

        emit Rebalance(rebalancePct, toWithdraw, depositTokenBalance, targetTokenBalance, fees);
    }
```

변동성 확인으로 인해 토큰 가격은 조작할 수 없지만 ICHI 볼트 지분의 가격은 여전히 조작될 수 있습니다(기부 또는 샌드위치 공격으로 인해). 재조정 작업으로 인해 Aegis 볼트가 예상보다 적은 지분을 주조할 수 있습니다.

`performUpkeep` 작업을 보호하기 위해 최소 주조 지분을 추가하십시오.

