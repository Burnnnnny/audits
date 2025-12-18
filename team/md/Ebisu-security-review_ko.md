# 정보 (About)

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**ebisu-vault** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Ebisu Vault 소개 (About ebisu-vault)

이 프로토콜은 wETH를 위한 ERC4626 포인트 금고입니다. 각 예금자가 금고에서 보낸 시간은 오프체인에서 계산되며 각 개별 예금자에게 발생한 포인트를 결정하는 데 사용됩니다.

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

**_검토 커밋 해시_ - [39db13fe1e74602693c63a4d716da581e2597319](https://github.com/ebisufinance/ebisu-vault/tree/39db13fe1e74602693c63a4d716da581e2597319)**

**_수정 검토 커밋 해시_ - [55ed087b07ec1bef0ce6236c0a6f89b348330b51](https://github.com/ebisufinance/ebisu-vault/tree/55ed087b07ec1bef0ce6236c0a6f89b348330b51)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Vault`
- `VaultCap`
- `VaultShare`
- `interfaces/**`

# 발견 사항 (Findings)

# [M-01] `maxTotalDeposits`가 `_beforeDeposit` 내부에서 제대로 확인되지 않음

## 심각도

**영향:** 중간 (Medium), 사용자가 `maxTotalDeposits`보다 더 많이 입금할 수 있기 때문입니다.

**가능성:** 중간 (Medium), 특정 시나리오가 필요하지 않으며 `deposit`/`mint` 작업이 어느 시점에 `maxTotalDeposits`보다 더 많이 입금할 수 있기 때문입니다.

## 설명

Ebisu vault는 사용자가 `deposit`/`mint`하기 전에 `_beforeDeposit` 확인을 구현하여 구성된 `maxPerDeposit` 및 `maxTotalDeposits`를 초과하지 않는지 확인합니다.

```solidity
    function deposit(uint256 assets, address receiver) public virtual override returns (uint256) {
        // require(assets <= maxDeposit(receiver), "ERC4626: deposit more than max");
>>      _beforeDeposit(assets);

        uint256 shares = previewDeposit(assets);
        _deposit(_msgSender(), receiver, assets, shares);

        return shares;
    }
```

그러나 현재 `_beforeDeposit` 구현에서 `maxTotalDeposits`에 대한 확인은 금고 내부의 토큰 잔액만 확인하고 사용자가 입금할 `assets`는 고려하지 않습니다.

```solidity
    function _beforeDeposit(uint256 assets) internal virtual {
        require(assets <= maxPerDeposit, "Vault: deposit amount exceeds per-deposit cap");
>>      require(_tokenBalance() <= maxTotalDeposits, "Vault: deposit amount exceeds total cap");
    }
```

이로 인해 사용자가 `maxTotalDeposits` 값을 초과하는 자산을 입금할 수 있습니다.

## 권장 사항

`maxTotalDeposits`를 확인할 때 사용자가 입금할 `assets`를 고려하십시오.

```diff
    function _beforeDeposit(uint256 assets) internal virtual {
        require(assets <= maxPerDeposit, "Vault: deposit amount exceeds per-deposit cap");
-        require(_tokenBalance() <= maxTotalDeposits, "Vault: deposit amount exceeds total cap");
+        require(_tokenBalance() + assets <= maxTotalDeposits, "Vault: deposit amount exceeds total cap");
    }
```

# [M-02] `setTVLLimits`가 `_newMaxTotalDeposits`를 `maxTotalDeposits` 대신 `maxPerDeposit`에 잘못 할당함

## 심각도

**영향:** 중간 (Medium), 캡 레이저(cap raiser)가 `maxTotalDeposits`를 업데이트할 수 없고 `_newMaxTotalDeposits`로 `maxPerDeposit`을 잘못 업데이트하기 때문입니다.

**가능성:** 중간 (Medium), 캡 레이저가 TVL 한도를 업데이트하려고 할 때마다 발생하기 때문입니다.

## 설명

현재 `setTVLLimits` 구현은 `_newMaxTotalDeposits`를 `maxTotalDeposits` 대신 `maxPerDeposit`에 잘못 할당합니다.

```solidity
    function setTVLLimits(uint256 _newMaxPerDeposit, uint256 _newMaxTotalDeposits) external onlyCapRaiser() {
        require(_newMaxPerDeposit<=_newMaxTotalDeposits, "newMaxPerDeposit exceeds newMaxTotalDeposits");

        maxPerDeposit = _newMaxPerDeposit;
>>      maxPerDeposit = _newMaxTotalDeposits;

        emit MaxPerDepositUpdated(maxPerDeposit, _newMaxPerDeposit);
        emit MaxTotalDepositsUpdated(maxTotalDeposits, _newMaxTotalDeposits);
    }
```

## 권장 사항

`_newMaxTotalDeposits`를 `maxPerDeposit` 대신 `maxTotalDeposits`에 할당하십시오.

```diff
    function setTVLLimits(uint256 _newMaxPerDeposit, uint256 _newMaxTotalDeposits) external onlyCapRaiser() {
        require(_newMaxPerDeposit<=_newMaxTotalDeposits, "newMaxPerDeposit exceeds newMaxTotalDeposits");

        maxPerDeposit = _newMaxPerDeposit;
-       maxPerDeposit = _newMaxTotalDeposits;
+       maxTotalDeposits = _newMaxTotalDeposits;

        emit MaxPerDepositUpdated(maxPerDeposit, _newMaxPerDeposit);
        emit MaxTotalDepositsUpdated(maxTotalDeposits, _newMaxTotalDeposits);
    }
```

# [M-03] 초기 그리핑(Initial Griefing) 공격 가능

## 심각도

**영향:** 높음 (High), 피해자가 자금을 잃기 때문입니다.

**가능성:** 낮음 (Low), 공격자에게 비용이 발생하기 때문입니다.

## 설명

유명한 초기 입금 공격은 자산/주식 변환에서 수행되는 `+1`에 의해 대부분 완화됩니다. 그러나 이 공격을 수행하면 사용자에게 불편을 주고(공격자에게 높은 비용 발생) 금고를 이상한 상태로 만들 수 있는 이상한 동작이 발생할 수 있습니다.

다음은 영향을 보여주는 PoC이며 `Deposit.t.sol`에 추가할 수 있습니다:

```solidity
    Vault vault;
    MockERC20 asset;

    address bob = makeAddr('bob');

    function setUp() public {
        asset = new MockERC20();
        vault = new Vault(100e18,100e18,asset);

        asset.mint(address(this), 10e18 + 9);
        asset.mint(bob,1e18);
    }

    function test_initialSupplyManipulation() public {
        // mint a small number of shares
        // (9 + 1 = 10) makes math simpler
        asset.approve(address(vault),9);
        vault.deposit(9, address(this));

        // do a large donation
        asset.transfer(address(vault), 10e18);

        // shares per assets is now manipulated
        assertEq(1e18+1,vault.convertToAssets(1));

        // victim stakes in vault
        vm.startPrank(bob);
        asset.approve(address(vault), 1e18);
        vault.deposit(1e18, bob);
        vm.stopPrank();

        // due to manipulation they receive 0 shares
        assertEq(0,vault.balanceOf(bob));

        // attacker redeems their shares
        vault.redeem(vault.balanceOf(address(this)), address(this), address(this));

        // even though the attacker loses 0.1 tokens
        assertEq(9.9e18 + 9,asset.balanceOf(address(this)));
        // the vicims tokens are lost and locked in the contract
        assertEq(1.1e18,asset.balanceOf(address(vault)));
    }
```

보시다시피 공격자는 공격을 위해 `0.1e18`의 자산을 지불해야 합니다. 그러나 그들은 피해자의 `1e18` 토큰을 계약에 효과적으로 잠갔습니다.

이것은 공격자에게 수익성이 없지만 금고를 이상한 상태로 남겨두고 피해자는 여전히 토큰을 잃게 됩니다.

## 권장 사항

소량의 초기 입금으로 이를 완화하는 것을 고려하십시오. 이것은 상당한 양인 한 이 공격을 너무 비용이 많이 들게 하기 때문에 이것이 불가능하도록 하는 가장 일반적이고 쉬운 방법입니다.

# [L-01] `getTVLLimits`는 변수 섀도잉(Variable Shadowing)으로 인해 잘못된 값을 반환함

`getTVLLimits` 함수의 로컬 상태 변수 (`maxPerDeposit` 및 `maxTotalDeposits`)는 전역 상태 변수와 동일한 이름을 사용하고 있습니다. 이 변수 섀도잉으로 인해 반환된 값은 `maxPerDeposit` 및 `maxTotalDeposits`에 대한 예상 전역 상태 값 대신 0이 됩니다. 함수가 예상 값을 반환하도록 함수를 업데이트하고 로컬 상태 변수 선언을 제거하십시오.

```diff
-    function getTVLLimits() external view returns (uint256 maxPerDeposit, uint256 maxTotalDeposits) {
+    function getTVLLimits() external view returns (uint256, uint256) {
        return (maxPerDeposit, maxTotalDeposits);
    }
```

# [L-02] `maxMint`는 `maxPerDeposit`에 해당하는 주식을 반환해야 함

`maxMint`는 현재 `type(uint256).max`를 반환하는데 이는 올바르지 않습니다. 사용자가 금고에 입금하면 입금된 `assets`가 `maxPerDeposit`을 초과하지 않는지 확인합니다. `type(uint256).max`를 반환하는 대신 `maxMint`는 `maxPerDeposit`에 해당하는 주식을 반환해야 합니다.

```diff
    function maxMint(address) public view virtual override returns (uint256) {
-        return type(uint256).max;
+        _convertToShares(maxPerDeposit, Math.Rounding.Down);
    }
```

# [L-03] `deposit`/`mint` 및 `withdraw`/`redeem`에 대한 슬리피지 보호 부족

`deposit`/`withdraw`의 계산된 주식과 `mint`/`redeem`의 계산된 사용자에 대한 자산은 Ebisu 금고 내부의 현재 총 자산 및 총 공급에 크게 의존하며, 이는 가치가 변하여 주식/자산 계산 결과에 영향을 미칠 수 있습니다. 이러한 함수를 래핑하여 슬리피지 확인을 제공하는 라우터/헬퍼 계약 또는 함수를 만드는 것을 고려하십시오.
