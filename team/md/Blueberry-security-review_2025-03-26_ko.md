# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Blueberryfi/blueberry-v2-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Blueberry 소개

Blueberry는 사용자가 최대 25배의 레버리지로 자산을 대출하고 차입할 수 있게 하여 DeFi의 주요 중개업체 역할을 하여 다양한 거래 전략을 실행할 수 있도록 하는 레버리지 대출 프로토콜입니다. Blueberry의 두 번째 버전은 디자인 개선 및 분산형 레버리지 거래를 더 쉽게 접근할 수 있도록 확장성에 중점을 둔 완전한 재작성입니다. 이 감사는 Blueberry 프로토콜의 V2에 중점을 두었습니다.

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

_검토 커밋 해시_ - [216e59ab4f4f8e46bd337cc8fc934dce86ceed40](https://github.com/Blueberryfi/blueberry-v2-contracts/tree/216e59ab4f4f8e46bd337cc8fc934dce86ceed40)

_수정 검토 커밋 해시_ - [1844c2bfbc787628eb8d858b9d764ca3483d571d](https://github.com/Blueberryfi/blueberry-v2-contracts/tree/1844c2bfbc787628eb8d858b9d764ca3483d571d)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `HyperEvmVault` 
- `VaultEscrow` 

# 발견 사항

# [C-01] `requestSum.assets`의 이중 차감으로 인한 잘못된 수수료

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`HyperEvmVault` 계약의 `_calculateFee` 함수는 `_totalEscrowValue`에서 업스트림으로 이미 차감되었음에도 불구하고 `grossAssets`에서 `$.requestSum.assets`를 잘못 차감합니다. 이로 인해 `eligibleForFeeTake`가 과소 평가되어 잘못된 수수료 계산이 발생합니다.

```solidity
    function _calculateFee(V1Storage storage $, uint256 grossAssets) internal view returns (uint256 feeAmount_) {
        if (grossAssets == 0 || block.timestamp <= $.lastFeeCollectionTimestamp) {
            return 0;
        }

        // Calculate time elapsed since last fee collection
        uint256 timeElapsed = block.timestamp - $.lastFeeCollectionTimestamp;

        // We subtract the pending redemption requests from the total asset value to avoid taking more fees than needed from
        //    users who do not have any pending redemption requests
@>        uint256 eligibleForFeeTake = grossAssets - $.requestSum.assets;
        // Calculate the pro-rated management fee based on time elapsed
        feeAmount_ = eligibleForFeeTake * $.managementFeeBps * timeElapsed / BPS_DENOMINATOR / ONE_YEAR;

        return feeAmount_;
    }
```
그러나 `grossAssets`는 이미 차감을 수행하는 `_totalEscrowValue`에서 직접 전달됩니다.
```solidity
    function _totalEscrowValue(V1Storage storage $) internal view returns (uint256 assets_) {
        uint256 escrowLength = $.escrows.length;
        for (uint256 i = 0; i < escrowLength; ++i) {
            VaultEscrow escrow = VaultEscrow($.escrows[i]);
            assets_ += escrow.tvl();
        }

        if ($.lastL1Block == l1Block()) {
            assets_ += $.currentBlockDeposits;
        }

@>        return assets_ - $.requestSum.assets;
    }
```
따라서 `$.requestSum.assets`를 다시 차감하면 수수료가 이중으로 감소된 값에 대해 계산되거나 언더플로 및 되돌리기가 발생할 수 있습니다.

## 권장 사항

`_calculateFee`를 업데이트하고 `$.requestSum.assets`를 다시 차감하지 않도록 하십시오.

# [H-01] 호출자가 주식 소유자와 다를 때 결함이 있는 인출 로직

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

HyperEvmVault의 인출은 사용자 지정 동작을 합니다. 사용자는 먼저 `requestRedeem()`을 호출하여 자금을 L1에서 EVM 측으로 다시 이동해야 합니다. 그 후 인출 가능한 자금은 `redeemRequests` 매핑에 저장됩니다.

이러한 메커니즘은 `previewWithdraw()` 및 `previewRedeem()` 함수의 특수화로 이어집니다. 글로벌 공급 또는 TVL 값으로 작동하는 대신 이러한 함수는 `redeemRequests` 매핑을 사용하여 구현됩니다.

```solidity
function previewWithdraw(uint256 assets_) public view override(ERC4626Upgradeable, IERC4626) returns (uint256) {
    V1Storage storage $ = _getV1Storage();
    RedeemRequest memory request = $.redeemRequests[msg.sender];
    return assets_.mulDivUp(request.shares, request.assets);
}

function previewRedeem(uint256 shares_) public view override(ERC4626Upgradeable, IERC4626) returns (uint256) {
    V1Storage storage $ = _getV1Storage();
    RedeemRequest memory request = $.redeemRequests[msg.sender];
    return shares_.mulDivDown(request.assets, request.shares);
}
```

이것은 구현이 `msg.sender`로 선택된 계정에 대해 서술하도록 강제합니다.

ERC4626의 인출은 호출자가 ERC20 승인을 활용하여 주식 소유자와 다를 수 있는 흐름도 지원합니다. 이것은 `_withdraw()`의 재정의된 버전에서 올바르게 구현되었지만 구현은 여전히 `msg.sender`에서 작동하는 미리보기 함수를 사용합니다. 즉, 변환은 호출자 잔액을 사용하여 계산되지만 잔액은 소유자 계정에 대해 수정되어 회계 문제 및 잠재적 서비스 거부로 이어집니다.

## 권장 사항

`previewWithdraw()` 및 `previewRedeem()` 작업이 소유자에 대해 변환을 실행하도록(즉, `$.redeemRequests[owner]`를 사용하여) `withdraw()` 및 `redeem()`의 구현을 재정의하십시오.

# [H-02] 기부된 토큰이 볼트에 예치되지 않음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`HyperEvmVault` 계약은 시스템 계약에서 받은 토큰을 처리하기 위해 주식 가격 계산에 `VaultEscrow`의 자산 잔액을 사용해야 합니다. 일반적으로 잔액은 `$.requestSum.assets` 값을 초과해서는 안 됩니다. 문제는 볼트에 대한 기부의 경우 초과 잔액이 주식 가격을 증가시키지만 분배될 수 없다는 것입니다. 즉, 볼트에 잠겨 있습니다. 이런 식으로 사용자는 모든 주식을 상환할 수 없습니다. 또한 초과 금액은 관리 수수료를 증가시킵니다.
공격자가 100개의 토큰을 예치하고 다른 100개의 토큰을 기부했다고 가정합니다. 그러면 공격자는 100개의 주식을 가지고 있고 `$.requestSum.assets`가 0이므로 볼트에 의해 잠긴 총 자산 양은 200과 같습니다.
HyperEvmVault.sol
```solidity
    function _totalEscrowValue(V1Storage storage $) internal view returns (uint256 assets_) {
        uint256 escrowLength = $.escrows.length;
        for (uint256 i = 0; i < escrowLength; ++i) {
            VaultEscrow escrow = VaultEscrow($.escrows[i]);
>>          assets_ += escrow.tvl();
        }

        if ($.lastL1Block == l1Block()) {
            assets_ += $.currentBlockDeposits;
        }

        return assets_ - $.requestSum.assets;
    }
```
VaultEscrow.sol
```solidity
    function tvl() public view returns (uint256) {
        uint256 assetBalance = ERC20Upgradeable(_asset).balanceOf(address(this));
        (uint64 vaultEquity_,) = _vaultEquity();
        return uint256(vaultEquity_) + assetBalance;
    }
```
그런 다음 무고한 사용자가 100개의 주식을 발행합니다. 이제 총 공급량은 200이고 총 자산 양은 400입니다.
그런 다음 공격자는 100개의 주식 상환을 요청합니다. 이제 `$.requestSum.assets`가 200이므로 총 자산 양은 200입니다.
그런 다음 사용자는 100개의 주식 상환을 요청하려고 시도하지만 볼트 자본에 기부된 토큰이 포함되어 있지 않기 때문에 항상 되돌려집니다.
```solidity
    function withdraw(uint64 assets_) external override onlyVaultWrapper {
>>      (uint64 vaultEquity_, uint64 lockedUntilTimestamp_) = _vaultEquity();
        require(block.timestamp > lockedUntilTimestamp_, Errors.L1_VAULT_LOCKED());

        // Update the withdraw state for the current L1 block
        L1WithdrawState storage l1WithdrawState_ = _getV1Storage().l1WithdrawState;
        _updateL1WithdrawState(l1WithdrawState_);
>>      l1WithdrawState_.lastWithdraws += assets_;

        // Ensure we havent exceeded requests for the current L1 block
>>      require(vaultEquity_ >= l1WithdrawState_.lastWithdraws, Errors.INSUFFICIENT_VAULT_EQUITY());

        // Withdraw from L1 vault
        _withdrawFromL1Vault(assets_);
    }    
<..>
    function _vaultEquity() internal view returns (uint64, uint64) {
        (bool success, bytes memory result) =
            VAULT_EQUITY_PRECOMPILE_ADDRESS.staticcall(abi.encode(address(this), _vault));
        require(success, "VaultEquity precompile call failed");

        UserVaultEquity memory userVaultEquity = abi.decode(result, (UserVaultEquity));
        uint256 equityInSpot = _scaleToSpotDecimals(userVaultEquity.equity);

        return (uint64(equityInSpot), userVaultEquity.lockedUntilTimestamp);
    }
``` 

## 권장 사항

초과 금액을 L1 볼트에 예치하는 것을 고려하십시오.

# [H-03] `_withdrawFromL1Vault()`의 정밀도 손실로 인한 자산 전송 되돌리기 가능성

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`VaultEscrow::_withdrawFromL1Vault()` 함수에서 L1 볼트의 인출 금액은 EVM 현물로 다시 전송된 금액보다 **작을 수 있습니다**(둘 다 부동 소수점 숫자로 변환될 때). 이러한 불일치로 인해 자산을 EVM 현물로 다시 전송하는 Hyperliquid L1의 트랜잭션이 되돌려질 수 있는 반면 상환 요청을 위한 Hyper EVM의 트랜잭션(`HyperEvmVault::requestRedeem()`)은 이미 성공했습니다.

결과적으로 상환 요청은 이미 성공했지만 자산이 `VaultEscrow`로 전송되지 않았기 때문에 사용자는 자산을 인출할 수 없습니다.

`VaultEscrow::_withdrawFromL1Vault()` 함수:
```solidity
function _withdrawFromL1Vault(uint64 assets_) internal {
    uint256 amountPerp = _scaleToPerpDecimals(assets_);
    // Withdraws assets from L1 vault
    L1_WRITE_PRECOMPILE.sendVaultTransfer(_vault, false, uint64(amountPerp));
    // Transfer assets to L1 spot
    L1_WRITE_PRECOMPILE.sendUsdClassTransfer(uint64(amountPerp), false);
    // Bridges assets back to escrow's EVM account
=>  L1_WRITE_PRECOMPILE.sendSpot(assetSystemAddr(), _assetIndex, assets_);
}
```

구체적으로 L1 볼트의 인출 금액은 `amountPerp`이고 EVM 현물로 다시 전송된 금액은 `assets_`이며 변환은 `amountPerp = _scaleToPerpDecimals(assets_)`로 수행됩니다. `_perpDecimals`가 `_evmSpotDecimals`보다 작으면 정밀도 손실이 발생하여 자산을 EVM 현물로 다시 전송할 때 되돌려질 수 있습니다. 예시 사례:

가정:

- `_perpDecimals = 6`
- `_evmSpotDecimals = 8`
- `assets_ = 100001111`

그러면:

- `amountPerp = assets_ / 10^2 = 1000011`
- `amountPerp`가 다시 현물 소수점으로 변환되면 변환된 값은 `100001100`이며 이는 `100001111`보다 작습니다.
- 결과적으로 자산을 EVM 현물로 다시 전송하려는 Hyperliquid L1의 트랜잭션이 되돌려집니다.

## 권장 사항

`VaultEscrow::_withdrawFromL1Vault()` 함수를 수정하여 현물 소수점으로 다시 변환된 `amountPerp`가 `assets_`와 같은지 확인하거나 변환된 현물 소수점 금액만 다시 전송하십시오.

```diff
function _withdrawFromL1Vault(uint64 assets_) internal {
    uint256 amountPerp = _scaleToPerpDecimals(assets_);
    // Withdraws assets from L1 vault
    L1_WRITE_PRECOMPILE.sendVaultTransfer(_vault, false, uint64(amountPerp));
    // Transfer assets to L1 spot
    L1_WRITE_PRECOMPILE.sendUsdClassTransfer(uint64(amountPerp), false);
    // Bridges assets back to escrow's EVM account
-   L1_WRITE_PRECOMPILE.sendSpot(assetSystemAddr(), _assetIndex, assets_);
+   L1_WRITE_PRECOMPILE.sendSpot(assetSystemAddr(), _assetIndex, _scaleToSpotDecimals(amountPerp));
}
```

# [L-01] 잘못된 최대 인출 가능 자산 금액으로 인한 예기치 않은 되돌리기

`HyperEvmVault.maxWithdrawableAssets` 함수는 인출 요청할 수 있는 최대 자산 금액, 즉 볼트에 의해 제어되는 금액을 반환합니다. 사용자가 `requestRedeem` 함수를 호출하면 현재 블록의 요청 금액 합계가 증가합니다. 합계는 다음 블록에서만 업데이트되는 볼트 자본을 초과해서는 안 됩니다.

```solidity
    function withdraw(uint64 assets_) external override onlyVaultWrapper {
        (uint64 vaultEquity_, uint64 lockedUntilTimestamp_) = _vaultEquity();
        require(block.timestamp > lockedUntilTimestamp_, Errors.L1_VAULT_LOCKED());

        // Update the withdraw state for the current L1 block
        L1WithdrawState storage l1WithdrawState_ = _getV1Storage().l1WithdrawState;
>>      _updateL1WithdrawState(l1WithdrawState_);
        l1WithdrawState_.lastWithdraws += assets_;

        // Ensure we havent exceeded requests for the current L1 block
        require(vaultEquity_ >= l1WithdrawState_.lastWithdraws, Errors.INSUFFICIENT_VAULT_EQUITY());

        // Withdraw from L1 vault
        _withdrawFromL1Vault(assets_);
    }
<..>
    function _updateL1WithdrawState(L1WithdrawState storage l1WithdrawState_) internal {
        uint64 currentL1Block = l1Block();
        if (currentL1Block > l1WithdrawState_.lastWithdrawBlock) {
            l1WithdrawState_.lastWithdrawBlock = currentL1Block;
            l1WithdrawState_.lastWithdraws = 0;
        }
    }
```
사용자는 `maxWithdrawableAssets` 함수를 사용하여 `requestRedeem` 호출에 대한 매개변수를 제어할 수 있습니다. 그러나 `currentL1Block == l1WithdrawState_.lastWithdrawBlock`일 때 `l1WithdrawState_.lastWithdraws` 값만큼 반환 값이 감소하지 않습니다.

```solidity
    function maxWithdrawableAssets() public view returns (uint256) {
        V1Storage storage $ = _getV1Storage();
        VaultEscrow escrowToRedeem = VaultEscrow($.escrows[redeemEscrowIndex()]);
        return escrowToRedeem.vaultEquity();
    }
```
이런 식으로 사용자는 볼트에서 상환할 수 있는 것보다 더 많은 자산을 상환 요청하고 예기치 않은 오류에 직면할 수 있습니다.

사용 가능한 자산 금액 계산을 위해 `currentL1Block == l1WithdrawState_.lastWithdrawBlock`일 때 `l1WithdrawState_.lastWithdraws` 값을 고려하는 것을 고려하십시오.

# [L-02] `HyperEvmVault`에서 첫 번째 예금자 공격 가능

`HyperEvmVault`는 볼트의 공급이 적거나 없을 때 주식 대 자산 비율이 초기화되는 방식으로 인해 고전적인 첫 번째 예금자 공격에 취약합니다.

단계:
1. 피해자가 10e8 USDC를 예치할 준비를 하고 있습니다.
2. 공격자가 프론트런하여 10e8 USDC를 예치합니다. 이것이 첫 번째 예금이므로 그는 10e8 주식을 받습니다. 이제 `shares == totalAssets == 10e18`입니다.
3. 공격자는 1 wei를 제외한 모든 주식을 인출합니다. 볼트에 1 주식을 남겨두고 이제 1 wei USDC를 보유합니다. 보시다시피 비동기적이더라도 `totalAssets` 및 `totalSupply`는 이 상환을 반영합니다. 이제 `shares == totalAssets == 1`입니다.
```solidity 
    function totalSupply() public view override(ERC20Upgradeable, IERC20) returns (uint256) {
        V1Storage storage $ = _getV1Storage();
@>        return super.totalSupply() - $.requestSum.shares;
    }

    function _totalEscrowValue(V1Storage storage $) internal view returns (uint256 assets_) {
        uint256 escrowLength = $.escrows.length;
        for (uint256 i = 0; i < escrowLength; ++i) {
            VaultEscrow escrow = VaultEscrow($.escrows[i]);
            assets_ += escrow.tvl();
        }

        if ($.lastL1Block == l1Block()) {
            assets_ += $.currentBlockDeposits;
        }

@>        return assets_ - $.requestSum.assets;
    }
```
4. 공격자는 에스크로에 5e8 USDC를 직접 전송합니다. 이제 `shares = 1, totalAssets = 5e8 + 1`입니다.
5. 첫 번째 예금자 피해자는 지금 10e8 USDC를 예치하고 `10e8 * 1 /(5e8+1) = 1.99 = 1 share`가 발행됩니다. 이제 `shares = 2, totalAssets = 15e8 + 1`입니다.
6. 공격자는 자신의 1 주식을 인출하고 7.5e8 USDC를 가져가 `7.5e8 - 5e8 = 2.5e8` USDC 이익을 얻습니다.

이 6단계에서 피해자는 공격자가 이익을 챙긴 `2.5e8 USDC`를 잃었고 인플레이션 공격이 성공적으로 발생했습니다.

직접 첫 번째 예금을 하거나 다른 검증된 [솔루션](https://blog.openzeppelin.com/a-novel-defense-against-erc4626-inflation-attacks)을 따르는 것을 고려하십시오.

# [L-03] 그리핑 공격으로 사용자의 상환 요청 차단 가능

인출 요청할 수 있는 일일 최대 자산 금액은 솔로 에스크로 계약의 자본에 의해 제한됩니다.

```solidity
    function requestRedeem(uint256 shares_) external nonReentrant {
<..>
>>      VaultEscrow escrowToRedeem = VaultEscrow($.escrows[redeemEscrowIndex()]);
        escrowToRedeem.withdraw(uint64(assetsToRedeem));
    }
<..>
    function redeemEscrowIndex() public view returns (uint256) {
        uint256 len = _getV1Storage().escrows.length;
        if (len == 1) {
            return 0;
        }

        uint256 depositIndex = depositEscrowIndex();
>>      return (depositIndex + 1) % len;
    }
```
즉, 누군가가 `escrowToRedeem` 계약의 전체 자본을 요청한 경우 다음 요청은 다음 날에만 실행될 수 있습니다.

프로토콜에 자본 가치가 비슷한 7개의 에스크로가 있다고 가정합니다. 그러면 악의적인 사용자는 볼트에 의해 잠긴 총 자산 양보다 7배 적은 토큰 금액을 사용하여 다른 사용자가 상환을 요청하는 것을 방지할 수 있습니다.
이를 위해 공격자는 하루에 한 번 전체 일일 자본을 요청하고 다음 블록에서 해당 금액을 다시 예치할 수 있습니다.
공격의 또 다른 방법은 프로토콜에 재사용 대기 시간이 없다는 점을 악용하는 것입니다. 사용자는 `escrowToRedeem` 계약에 충분한 자본이 있다는 조건하에 예금 직후 상환을 요청할 수 있습니다.

이런 식으로 공격자는 가스 비용만으로 요청 기능을 제어할 수 있습니다.

요청 및 전송에 대한 재사용 대기 시간을 구현하는 것을 고려하십시오.

# [L-04] `constructor()`의 잘못된 주소 확인

`VaultEscrow::constructor()` 함수에서 여러 주소가 `address(0)`이 아님을 확인할 때 논리 OR(`||`) 연산자가 잘못 사용됩니다. 현재 논리로 생성자는 세 주소 중 **적어도 하나**가 0이 아니어야 한다고 요구하는데 이는 올바르지 않습니다.

`VaultEscrow::constructor()` 함수:
```solidity
constructor(address wrapper_, address vault_, address asset_, uint64 assetIndex_, uint8 assetPerpDecimals_) {
=>  require(wrapper_ != address(0) || vault_ != address(0) || asset_ != address(0), Errors.ADDRESS_ZERO());
    ...
}
```

대신 논리 AND(`&&`) 연산자를 사용하도록 조건을 수정하십시오.

```diff
constructor(address wrapper_, address vault_, address asset_, uint64 assetIndex_, uint8 assetPerpDecimals_) {
-   require(wrapper_ != address(0) || vault_ != address(0) || asset_ != address(0), Errors.ADDRESS_ZERO());
+   require(wrapper_ != address(0) && vault_ != address(0) && asset_ != address(0), Errors.ADDRESS_ZERO());
    ...
}
```

