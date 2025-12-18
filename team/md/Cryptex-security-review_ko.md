# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**cryptexfinance/tcapv2.0** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# TCAP 소개 (About TCAP)

Cryptex Finance의 Total Market Cap Token (TCAP)은 단일 합성 자산을 통해 전체 암호화폐 시장의 가치에 대한 노출을 투자자에게 제공하는 암호화 인덱스 토큰입니다.

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

_검토 커밋 해시_ - [f0168f3fe66c1fba4fd70ffdcdc87287e8f0cb6a](https://github.com/cryptexfinance/tcapv2.0/tree/f0168f3fe66c1fba4fd70ffdcdc87287e8f0cb6a)

_수정 검토 커밋 해시_ - [71ee4a810febe2cb5fcbf2102076b44525005289](https://github.com/cryptexfinance/tcapv2.0/tree/71ee4a810febe2cb5fcbf2102076b44525005289)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `TCAPV2`
- `Vault`
- `Constants`
- `FeeCalculatorLib`
- `LiquidationLib`
- `Multicall`
- `AggregatedChainlinkOracle`
- `BaseOracleUSD`
- `TCAPTargetOracle`
- `AaveV3Pocket`
- `BasePocket`

# 발견 사항 (Findings)

# [M-01] `takeFee`에 접근 제어 형식 추가

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`takeFee()` 함수는 모든 사람이 사용할 수 있습니다.
예금자 A, B, C가 각각 10 ETH를 예치하고 각각 10 주식을 보유하고 있으며 동일한 부채를 가지고 있다고 가정해 봅시다.
누구든지 A와 B에 대해 `takeFee()`를 호출하여 그들의 주식을 점진적으로 9.99로 줄일 수 있습니다.
결과적으로 예금자 C는 더 많은 주식을 갖게 됩니다.

```solidity
    function takeFee(address user, uint96 pocketId) external {
        IPocket pocket = _getVaultStorage().pockets[pocketId].pocket;
        if (address(pocket) == address(0)) revert InvalidValue(IVault.ErrorCode.INVALID_POCKET);
        _takeFee(pocket, user, pocketId);
    }
```

## 권장 사항

함수를 보호된 역할로 제한하거나 사용자가 자신에 대해서만 수수료를 가져갈 수 있도록 허용하는 것을 고려하십시오.

# [M-02] Chainlink의 응답을 처리할 때 회로 차단기(Circuit breakers)가 고려되지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

모든 Chainlink 피드에는 최소 및 최대 가격이 있습니다. 그러나 회로 차단기로 인해 자산 가격이 이러한 제한을 벗어나더라도 제공된 답변은 여전히 캡이 씌워진 상태로 유지됩니다.

이로 인해 실제 가격이 수집기(aggregator)의 `minAnswer` 아래로 떨어지면 Chainlink가 실제 값 대신 캡이 씌워진 값을 계속 제공하므로 가격이 올바르지 않게 될 수 있습니다.

Chainlink 문서는 "대부분의 데이터 피드에서 이러한 값은 더 이상 사용되지 않으며 애플리케이션이 최신 답변을 읽는 것을 방해하지 않습니다."라고 언급합니다. 그러나 Arbitrum의 경우 대부분의 데이터 피드(ETH 및 대부분의 스테이블코인 포함)에 대해 이러한 값이 실제로 사용되므로 사실이 아닙니다. 예를 들어 ETH/USD 수집기:
[링크](https://arbiscan.io/address/0x3607e46698d218B3a5Cae44bF381475C0a5e2ca7#readContract)

## 권장 사항

가격이 항상 수집기의 `minAnswer`와 `maxAnswer` 사이에 있는지 확인하는 것을 고려하십시오:

```diff
    function latestPrice(bool checkStaleness) public view virtual override returns (uint256) {
        ...
126: -       assert(answer > 0);
127: +       require(answer > MIN_ANSWER && answer < MAX_ANSWER);
        ...
    }
```

# [L-01] 가격 처리 시 Arbitrum Sequencer 확인 없음

계약은 Arbitrum에 배포되지만 `AggregatedChainlinkOracle.sol`은 Arbitrum Sequencer가 다운되었는지 확인하지 않습니다. 다운되면 오라클 데이터가 최신 상태로 유지되지 않고 오래된 데이터가 될 수 있습니다. 그러나 사용자는 L1 낙관적 롤업 계약을 통해 프로토콜과 직접 계속 상호 작용할 수 있습니다. 많은 프로토콜 작업이 실제로 오래된 가격을 허용하지만 TCAPV2 발행과 같은 일부 작업은 그렇지 않습니다.

결과적으로 시퀀서 다운타임 기간 동안 실제 가격에서 포지션이 비정상적일 수 있는 사용자가 오래된 가격이 사용되어 계속 발행할 수 있거나 그 반대의 경우가 발생할 수 있습니다.

시퀀서에 대한 확인을 설정하기 위해 Chainlink의 [예제 코드](https://docs.chain.link/data-feeds/l2-sequencer-feeds#example-code)를 따르는 것을 권장합니다.

# [L-02] 일반적인 부패(staleness) 시간 사용은 권장되지 않음

부패 여부는 마지막 업데이트 시간이 하루를 넘지 않았는지 확인하여 발행 중에 검사됩니다. 그러나 모든 토큰이 1일의 부패 기간을 갖는 것은 아닙니다. 일부는 1시간 정도로 짧습니다. 이 구현은 이러한 토큰에 대해 오래된 가격이 허용되도록 합니다.

```solidity
        if (checkStaleness) {
            if (updatedAt < block.timestamp - 1 days) {
                revert StaleOracle();
            }
        }
```

각 토큰의 하트비트 매개변수를 기록하여 부패 기간을 결정하는 매핑을 사용하는 것을 고려하십시오.

# [L-03] 수수료가 충분히 높으면 인출이 실패할 수 있음

사용자가 인출할 때 수수료가 먼저 공제됩니다:

```solidity
    function withdraw(uint96 pocketId, uint256 amount, address to) external {
        // ...
>>      _takeFee(pocket, msg.sender, pocketId);
>>      shares = pocket.withdraw(msg.sender, amount, to);
    }
```

보시다시피 `_takeFee`는 사용자의 주머니(pocket)에서 이자를 인출합니다.

```solidity
    function _takeFee(IPocket pocket, address user, uint96 pocketId) internal {
//...
            pocket.withdraw(user, interest, feeRecipient_);
//...
```

인출된 이자는 사용자가 보유한 총 주식 수를 줄입니다.

```solidity
    function withdraw(address user, uint256 amountUnderlying, address recipient) external onlyVault returns (uint256 shares) {
///...
        $.sharesOf[user] -= shares;
        $.totalShares -= shares;
//...
```

이 과정 후 사용자가 입력한 `amount`가 인출 시도됩니다.

```solidity
    function withdraw(uint96 pocketId, uint256 amount, address to) external {
        // ...
        shares = pocket.withdraw(msg.sender, amount, to);
    }
```

수수료가 충분히 높은 경우, 사용자가 입력한 금액에서 계산된 후속 주식은 이제 수수료 공제로 인해 줄어든 `shareOf`보다 많아져 인출이 실패하게 됩니다.

이것을 증명하기 위해 아래 테스트를 `Vault.t.sol`에 추가하고 `forge test --mt test_mayNotBeAbleToWithdraw -vvv`로 실행합니다.

```solidity
        function test_mayNotBeAbleToWithdraw(uint32 timestamp) public {
        address user = makeAddr("user");
        uint256 amount = 100 ether;
        deposit(user, amount);
        vm.prank(user);
        vault.mint(pocketId, amount * 1e10 / 10);
        timestamp = uint32(bound(timestamp, block.timestamp + 1, type(uint32).max));
        vm.warp(timestamp);
        uint256 outstandingInterest = vault.outstandingInterestOf(user, pocketId);
        assertGt(outstandingInterest, 0);
        vm.prank(user);
        vault.withdraw(pocketId, 90 ether, user);
```

아래 오류와 함께 실패해야 합니다.

```md
    │   │   │   ├─ [558] MockCollateral::balanceOf(BasePocket: [0x1d1499e622D69689cdf9004d05Ec547d650Ff211]) [staticcall]
    │   │   │   │   └─ ← [Return] 86381365946854388636 [8.638e19]
    │   │   │   └─ ← [Revert] InsufficientFunds()
    │   │   └─ ← [Revert] InsufficientFunds()
    │   └─ ← [Revert] InsufficientFunds()
    └─ ← [Revert] InsufficientFunds()
```

대신 사용자로부터 가져온 수수료를 뺀 금액을 인출하는 것을 권장합니다.

# [L-04] 수수료 수취인이 없는 경우 사용자는 수수료 지불을 완전히 건너뛸 수 있음

`_takeFee`는 사용자의 이자가 0이거나 수수료 수취인이 없는 경우 수수료를 받지 않습니다. 그리고 `updateFeeRecipient`에서 `feeRecipient_`가 어떤 이유로든 `address(0)`으로 설정될 수 있음을 알 수 있습니다.

```solidity
//...
        if (interest != 0 && feeRecipient_ != address(0)) {
            $.mintData.resetInterestOf(_toMintId(user, pocketId));
            pocket.withdraw(user, interest, feeRecipient_);
            emit FeeCollected(user, pocketId, feeRecipient_, interest);
        }
    }
```

`feeRecipient_`가 없는 기간 동안 사용자는 이 때 모든 토큰을 인출하여 수수료 지불을 성공적으로 우회할 수 있습니다. 인출은 수수료 부과 없이 성공적으로 진행됩니다. 사용자가 프로토콜과 다시 상호 작용하려면 새 주소로 그렇게 할 수 있습니다.

아래 테스트를 복사하여 `Vault.t.sol`에 추가하고 실행하십시오. 사용자가 예치한 100 ether가 해당 기간 동안 누적된 수수료에 관계없이 완전히 인출되는 것을 볼 수 있습니다.

```solidity
        function test_userMayPayNoFees(uint32 timestamp) public {
        address user = makeAddr("user");
        uint256 amount = 100 ether;
        uint256 mintAmount = amount * 1e10 / 10;
        uint256 burnAmount = mintAmount;
        deposit(user, amount);
        vm.prank(user);
        vault.mint(pocketId, mintAmount);
        timestamp = uint32(bound(timestamp, block.timestamp + 10000, type(uint32).max));
        vm.warp(timestamp);
        vm.prank(user);
        vault.burn(pocketId, burnAmount);
        // We set fee recipeint to 0
        vault.updateFeeRecipient(address(0));
        uint256 outstandingInterest = vault.outstandingInterestOf(user, pocketId);
        assertGt(outstandingInterest, 0);
        vm.prank(user);
        //User can safely withdraw all of his token
        vault.withdraw(pocketId, 100 ether, user);
    }
```

수수료 수취인이 없는 동안 인출을 방지하거나 사용자에게 예상되는 수수료를 뺀 금액을 인출하는 것이 좋습니다. 수수료는 나중에 수취인이 설정될 때 가져갈 수 있습니다.

# [L-05] 전체 청산이 길어지거나 일시적으로 방지될 수 있음

`liquidate`는 청산인의 `burnAmount`가 사용자의 `mintAmount`보다 많으면 되돌려집니다(revert). 결과적으로 사용자가 소량의 TCAPV2를 소각하여 `mintAmount`를 줄이는 데는 충분하지만 건강 요소를 높이는 데는 충분하지 않게 함으로써 전체 청산을 어렵게 만들 수 있습니다. 이는 청산인에게 불만스러울 수 있으며, 특히 부분 청산보다 전체 청산이 더 수익성 있는 경우 더욱 그렇습니다.

아래 테스트를 `Vault.t.sol`에 추가하고 실행하십시오. 사용자가 대출을 더 건강하게 만들지 않고 TCAPV2 1위(wei)를 소각하여 청산을 실패하게 만들 수 있음을 보여줍니다.

```solidity
    function test_frontrunLiquidation(address user, uint256 amount) public {
        vm.assume(user != address(0) && user != address(vaultProxyAdmin));
        uint256 depositAmount = deposit(user, amount, 1, 1e28);

        vm.startPrank(user);
        uint256 mintAmount = bound(amount, 1, depositAmount * 1e10);
        vault.mint(pocketId, mintAmount);
        vm.stopPrank();

        uint256 collateralValue = vault.collateralValueOfUser(user, pocketId);
        uint256 mintValue = vault.mintedValueOf(mintAmount);
        uint256 multiplier = mintValue * 10_000 / (collateralValue);
        vm.assume(multiplier > 1);
        feed.setMultiplier(multiplier - 1);

        vm.startPrank(user);
        vault.burn(pocketId, 1);
        vm.stopPrank();

        //We attempt to mint 1 wei to show that user's loan is still not healthy
        vm.expectRevert();
        vm.startPrank(user);
        vault.mint(pocketId, 1);
        vm.stopPrank();

        tCAPV2.mint(address(this), mintAmount);
        vault.liquidate(user, pocketId, mintAmount);
    }
```

테스트는 `INVALID_BURN_AMOUNT`에 해당하는 오류 코드 10으로 실패해야 합니다.

```md
    │   │   │   └─ ← [Return] 9584648100537527642735121263 [9.584e27]
    │   │   └─ ← [Revert] InvalidValue(10)
    │   └─ ← [Revert] InvalidValue(10)
    └─ ← [Revert] InvalidValue(10)
```

`burnAmount` > `mintAmount`인 경우 되돌리는 대신 `burnAmount`를 `mintAmount`로 설정하는 것이 좋습니다.

```diff
// ...
        uint256 mintAmount = mintedAmountOf(user, pocketId);
-       if (burnAmount > mintAmount) revert InvalidValue(IVault.ErrorCode.INVALID_BURN_AMOUNT);
+       if (burnAmount > mintAmount) {
+       burnAmount = mintAmount;
+      }
        uint256 tcapPrice = TCAPV2.latestPrice();
// ...
```

# [L-06] 0 주식을 발행한 사용자는 자금을 잃게 됨

사용자가 자금을 예치할 때 다음 함수가 호출됩니다:

```solidity
    function registerDeposit(address user, uint256 amountUnderlying) external onlyVault returns (uint256 shares) {
        uint256 amountOverlying = _onDeposit(amountUnderlying);
        uint256 totalShares_ = totalShares();
        if (totalShares_ > 0) {
            shares = (totalShares_ * amountOverlying) / (OVERLYING_TOKEN.balanceOf(address(this)) - amountOverlying);
        } else {
            shares = amountOverlying * Constants.DECIMAL_OFFSET;
        }
        BasePocketStorage storage $ = _getBasePocketStorage();
        $.totalShares += shares;
        $.sharesOf[user] += shares;
        // @audit should not happen, prevent overflow when calculating balance
        assert($.sharesOf[user] < 1e41);
        emit Deposit(user, amountUnderlying, amountOverlying, shares);
    }
```

`totalShares_ > 0`이고 분모가 분자보다 높으면 사용자는 자금을 예치하지만 그 대가로 0 주식을 받게 되므로 `shares == 0`일 때 되돌리는 것을 고려하십시오.

# [L-07] 청산인이 예상보다 적은 보상을 받을 수 있음

`Vault`에서 `liquidationReward`는 TCAP의 현재 가격에 따라 달라집니다. 청산인은 토큰의 `burnAmount`를 소각하지만 TCAP 가격이 현재 변동성이 큰 경우 예상보다 적은 보상을 받을 수 있습니다.

청산을 위한 `minRewardAmount`를 추가하고 보상이 이 값보다 작으면 트랜잭션을 되돌리는 것을 고려하십시오.

# [L-08] 청산을 유발하기 위한 과도한 대출

`Vault`에서 사용자는 청산 임계값까지 대출을 받을 수 있습니다:

```solidity
	if (_healthFactor(user, pocketId, checkStaleness) < liquidationParams().threshold) revert LoanNotHealthy();
```

청산은 건강 요소(health factor)가 이전 임계값과 적어도 같으면 트리거될 수 있습니다:

```solidity
    function liquidate(address user, uint96 pocketId, uint256 burnAmount) external returns (uint256 liquidationReward) {
        ...
        IVault.LiquidationParams memory liquidation = liquidationParams();
        uint256 healthFactor_ = LiquidationLib.healthFactor(mintAmount, tcapPrice, collateralAmount, collateralPrice, COLLATERAL_DECIMALS);
->      if (healthFactor_ >= liquidation.threshold) revert LoanHealthy();
        ...
    }
```

차이가 1위(wei)에 불과하여 최대화된 대출이 다음 블록에서 청산될 가능성이 매우 높기 때문에 문제가 됩니다.

이 시나리오를 피하기 위해 대출 금액을 늘릴 수 있는 임계값을 낮추는 것을 고려하십시오.

# [L-09] 주머니(Pocket) 인플레이션 공격

공격을 시작하기 위해 악의적인 사용자는 종종 가능한 가장 작은 양의 주식(예: 1 wei)을 발행한 다음 볼트에 상당한 자산을 기부하여 주당 자산 수를 부풀립니다. 결과적으로 다른 사용자가 예치할 때 반올림 오류가 발생합니다.
[링크](https://blog.openzeppelin.com/a-novel-defense-against-erc4626-inflation-attacks)

Pockets의 경우 공격자는 `OVERLYING_TOKEN`을 기부해야 합니다.

```solidity
    function registerDeposit(address user, uint256 amountUnderlying) external onlyVault returns (uint256 shares) {
        uint256 amountOverlying = _onDeposit(amountUnderlying);
        uint256 totalShares_ = totalShares();
        if (totalShares_ > 0) {
            shares = (totalShares_ * amountOverlying) / (OVERLYING_TOKEN.balanceOf(address(this)) - amountOverlying);
        } else {
            shares = amountOverlying * Constants.DECIMAL_OFFSET;
        }
        BasePocketStorage storage $ = _getBasePocketStorage();
        $.totalShares += shares;
        $.sharesOf[user] += shares;
        // @audit should not happen, prevent overflow when calculating balance
        assert($.sharesOf[user] < 1e41);
        emit Deposit(user, amountUnderlying, amountOverlying, shares);
    }
```

첫 번째 예금자는 `shares = amountOverlying * Constants.DECIMAL_OFFSET`을 받습니다.
그러나 이 예금자는 여전히 담보를 인출하여 주식 1위만 남길 수 있으므로 이 보호만으로는 충분하지 않습니다.

**공격 재현 테스트:**
[Gist](https://gist.github.com/ZanyBonzy/52b8a74582829f2dbe245fec2b29eeb7)

위 링크에서 설명된 대로 가상 주식을 사용하는 것을 고려하십시오.

# [L-10] AAVE 주머니를 사용하는 대출은 청산 불가능해질 수 있음

`AaveV3Pocket`에서 Aave에서 사용 가능한 공급이 전체 금액을 커버하기에 충분하지 않은 경우 인출 시 함수가 되돌려집니다:

```solidity
    function _onWithdraw(uint256 amountOverlying, address recipient) internal override returns (uint256 amountUnderlying) {
        if (amountOverlying == 0) return 0;
        uint256 amountWithdrawn = POOL.withdraw(address(UNDERLYING_TOKEN), amountOverlying, recipient);
        // https://github.com/code-423n4/2022-06-connext-findings/issues/181
->      assert(amountWithdrawn == amountOverlying);
        return amountOverlying;
    }
```

이는 Aave의 유동성 부족시 발생할 수 있으며 사용자가 담보를 인출할 때 보호하기 위한 것입니다.

문제는 이것이 청산 중에도 발생할 수 있으며 되돌리기를 유발할 수 있다는 것입니다. `Vault.liquidate`에서 `_takeFee`는 누적된 이자를 인출하며, 이는 거래를 차단하여 프로토콜에 악성 부채가 축적되는 결과를 초래할 수 있습니다:

```solidity
    function _takeFee(IPocket pocket, address user, uint96 pocketId) internal {
        uint256 interest = outstandingInterestOf(user, pocketId);
        uint256 collateral = _balanceOf(user, pocketId);
        if (interest > collateral) interest = collateral;
        VaultStorage storage $ = _getVaultStorage();
        address feeRecipient_ = $.feeRecipient;
        if (interest != 0 && feeRecipient_ != address(0)) {
            $.mintData.resetInterestOf(_toMintId(user, pocketId));
            pocket.withdraw(user, interest, feeRecipient_);
            emit FeeCollected(user, pocketId, feeRecipient_, interest);
        }
    }
```

기본(underlying)과 오버라잉(overlying) 간의 변환은 항상 1:1이므로 사용자에게 오버라잉 토큰을 보내면 항상 성공적인 청산이 보장됩니다.

대안 옵션:

`AaveV3Pocket`에서 `amountOverlying`이 `amountWithdrawn`과 같지 않을 때 되돌리기를 피하는 것을 고려하십시오. 대신 풀(pull) 접근 방식을 사용하여 차액을 저장하고 수신자가 인출할 수 있도록 하십시오.
