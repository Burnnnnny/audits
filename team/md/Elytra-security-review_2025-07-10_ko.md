# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 잘 알려진 40명 이상의 프리랜서 보안 연구원으로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 $100k 이상을 획득했거나, 여러 번의 우승 경력이 있거나, 우리와의 감사에서 탁월한 성과를 보였습니다. 우리는 검증되고 동기 부여된 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도왔으며, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**ElytraProtocol/contracts** 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, **zark, unforgiven, JCN, btk**가 **Elytra** 검토에 참여했습니다. 총 **35**개의 문제가 발견되었습니다.

# Elytra 소개 (About Elytra)

Elytra는 사용자가 HYPE 또는 USDC를 입금하고 시스템에 내기를 나타내는 elyHYPE(rsHYPE) 또는 elyUSD 토큰을 받을 수 있는 HyperEVM용 유동성 리스테이킹(Liquid Restaking) 프로토콜입니다. 통합 전략 관리, 가격 오라클 및 자산 리스테이킹 및 인출을 용이하게 하는 안전한 역할 기반 제어 시스템을 갖춘 모듈식 아키텍처를 특징으로 합니다.

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [f3fd3314e9cff7d74bc1edeb2e0a5b90762fbb4a](https://github.com/ElytraProtocol/contracts/tree/f3fd3314e9cff7d74bc1edeb2e0a5b90762fbb4a)<br>&nbsp;&nbsp;(ElytraProtocol/contracts)

**수정 검토 커밋 해시:**<br>• [0722d6f68c96a23d077ee278c4c5324643ad40ee](https://github.com/ElytraProtocol/contracts/tree/0722d6f68c96a23d077ee278c4c5324643ad40ee)<br>&nbsp;&nbsp;(ElytraProtocol/contracts)

# 범위 (Scope)

- `ElytraConfigV1.sol`
- `ElytraDepositPoolV1.sol`
- `ElytraOracleV1.sol`
- `ElytraUnstakingVaultV1.sol`
- `elyHYPE.sol`
- `elyUSD.sol`
- `ElyAsset.sol`
- `WHYPEPriceOracle.sol`
- `ElytraConfigRoleChecker.sol`

# 발견 사항 (Findings)

# [C-01] `receiveFromDepositPool()`이 전송된 자산을 추적하지 않음

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`ElytraUnstakingVaultV1`의 `receiveFromDepositPool()` 함수는 전송 작업 전후의 금고 잔액을 비교하여 수신된 자산을 계산하려고 시도하는 결함이 있습니다. 그러나 이 접근 방식은 모든 토큰에 대해 완전히 실패합니다. 잔액 비교가 항상 동일한 값을 초래하여 함수가 0의 수신 금액을 계산하기 때문입니다.

```solidity
    function receiveFromDepositPool(address asset) external onlyDepositPool {
        // All assets are now ERC20 tokens (including WHYPE)
        uint256 balanceBefore = IERC20(asset).balanceOf(address(this));
        // Assets should be transferred before calling this function
        uint256 balanceAfter = IERC20(asset).balanceOf(address(this));
        uint256 received = balanceAfter - balanceBefore;
        claimableAssets[asset] += received;
        emit AssetsReceivedFromDepositPool(asset, received);
    }
```

잔액 확인이 동일한 트랜잭션에서 수행되므로 두 값이 동일하여 문제가 발생합니다. `received = balanceAfter - balanceBefore` 계산은 항상 0이 됩니다.

이로 인해 다음과 같은 문제가 발생합니다:

1. 사용자가 예금 풀을 통해 인출을 요청합니다.
2. 자산이 `transferAssetToUnstakingVault()`를 통해 언스테이킹 금고로 전송됩니다.
3. 금고는 수신된 자산을 추적하기 위해 `receiveFromDepositPool()`을 호출합니다.
4. 함수는 결함이 있는 잔액 비교로 인해 0 수신 자산을 계산합니다.
5. `claimableAssets`가 변경되지 않으므로 사용자는 인출을 청구할 수 없습니다.

## 권장 사항

`receiveFromDepositPool()` 함수를 수정하여 예금 풀에서 명시적인 `amount` 매개변수를 수락한 다음 이 금액을 `claimableAssets`에 직접 추가하도록 하십시오. 이렇게 하면 신뢰할 수 없는 잔액 비교 방법을 완전히 우회할 수 있습니다.

```solidity
function receiveFromDepositPool(address asset, uint256 amount) external onlyDepositPool {
    claimableAssets[asset] += amount;
}
```

# [C-02] 예금 풀을 통한 인출 요청이 영구적으로 손실됨

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`ElytraDepositPoolV1`의 `requestWithdrawal()` 함수는 잘못된 사용자 주소 추적으로 인해 영구적으로 손실될 인출 요청을 생성합니다. 사용자가 `requestWithdrawal()`을 호출하면 예금 풀은 사용자의 elyAsset 토큰을 자신에게 전송한 다음 언스테이킹 금고의 `requestWithdrawal()` 함수를 호출합니다:

```solidity
    function requestWithdrawal(
        address asset,
        uint256 elyAssetAmount
    )
        external
        nonReentrant
        whenNotPaused
        onlySupportedAsset(asset)
        returns (uint256 requestId)
    {
        address unstakingVault = elytraConfig.getContract(ElytraConstants.ELYTRA_UNSTAKING_VAULT);

        // Transfer elyAsset tokens to this contract for burning
        address elyAssetToken = elytraConfig.getElyAsset();
        IERC20(elyAssetToken).safeTransferFrom(msg.sender, address(this), elyAssetAmount);

        // Approve unstaking vault to burn tokens
        IERC20(elyAssetToken).approve(unstakingVault, elyAssetAmount);

        // Request withdrawal through unstaking vault
        requestId = IElytraUnstakingVault(unstakingVault).requestWithdrawal(asset, elyAssetAmount);
    }
```

그러나 언스테이킹 금고는 원래 사용자 대신 `msg.sender`(예금 풀)를 요청 소유자로 기록합니다.

```solidity
        withdrawalRequests[requestId] = WithdrawalRequest({
            user: msg.sender,
            asset: asset,
            elyAssetAmount: elyAssetAmount,
            assetAmount: assetAmount,
            requestTime: block.timestamp,
            completed: false
        });
```

이로 인해 다음과 같은 문제가 발생합니다:

1. 사용자가 자신의 elyAsset 토큰으로 `ElytraDepositPoolV1.requestWithdrawal()`을 호출합니다.
2. 예금 풀은 토큰을 자신에게 전송하고 `UnstakingVault.requestWithdrawal()`을 호출합니다.
3. 언스테이킹 금고는 예금 풀 주소를 요청 소유자로 기록합니다.
4. 사용자가 `UnstakingVault.completeWithdrawal()`을 통해 인출을 완료하려고 시도합니다.
5. `msg.sender`(사용자) != `request.user`(예금 풀 주소)이므로 트랜잭션이 되돌려집니다.

사용자의 토큰은 요청 생성 중에 효과적으로 소각되지만 인출 요청은 사용자가 아닌 예금 풀 계약이 소유하므로 자산을 청구할 수 없습니다.

## 권장 사항

언스테이킹 금고에 별도의 `requestWithdrawalForUser()` 함수를 만드십시오:
- 예금 풀에 의해서만 호출 가능합니다.
- 실제 요청 소유자를 지정하기 위해 추가 `user` 매개변수를 사용합니다.
- `msg.sender` 대신 제공된 사용자 주소를 요청 소유자로 기록합니다.

# [C-03] 보류 중인 인출 자산을 포함하여 발생하는 TVL 오류

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`getTotalAssetTVL()` 함수는 TVL 계산에 **보류 중인 인출 요청**을 지원하는 자산을 잘못 포함합니다. 이로 인해 `elyAsset` 가격이 부풀려져 익스플로잇 가능한 가격 오류가 발생합니다.

**근본 문제**

사용자가 `requestWithdrawal()`을 통해 인출을 시작하면 `elyAssets`가 **즉시 소각**되어 총 공급량이 감소합니다. 그러나 해당 자산은 **언스테이킹 금고**로 이동되며 **TVL 계산에서 제거되지 않습니다**. 이 자산은 사용자가 인출을 완료할 때까지 계산에 남아 있습니다.

가격은 다음과 같이 계산되므로:

```
price = totalTVL / totalSupply
```

불일치가 발생합니다:

* 공급 감소 (소각된 `elyAssets`로 인해).
* TVL 동일 유지 (조정되지 않은 자산 회계로 인해).

이로 인해 `elyAsset` 가격이 **인위적으로 급등**하여 사용자가 시스템을 악용할 수 있습니다:

* 토큰 소각.
* 가격 부풀리기.
* 부당하게 높은 비율로 인출.

**기술적 세부 사항**

`ElytraDepositPoolV1`에서 TVL은 다음과 같이 계산됩니다:

```solidity
function getTotalAssetTVL(address asset) public view returns (uint256 totalTVL) {
    uint256 poolBalance = IERC20(asset).balanceOf(address(this));
    uint256 strategyAllocated = assetsAllocatedToStrategies[asset];
    uint256 unstakingVaultBalance = _getUnstakingVaultBalance(asset);

    return poolBalance + strategyAllocated + unstakingVaultBalance;
}
```

문제는 `unstakingVaultBalance`가 다음을 통해 검색된다는 것입니다:

```solidity
function _getUnstakingVaultBalance(address asset) internal view returns (uint256 balance) {
    address unstakingVault = elytraConfig.getContract(ElytraConstants.ELYTRA_UNSTAKING_VAULT);
    if (unstakingVault == address(0)) {
        return 0;
    }

    try IElytraUnstakingVault(unstakingVault).getClaimableAssets(asset) returns (uint256 claimableAmount) {
        return claimableAmount;
    } catch {
        return 0;
    }
}
```

이 값은 해당 주식이 이미 소각되었음에도 불구하고 보류 중인 인출 요청에 할당된 자산을 포함합니다.

**악용 예시**

다음을 고려해 보십시오:

1. 초기 상태:

* 풀에 15e18 HYPE.
* 10e18 elyHYPE 총 공급.
* 가격 = 1.5 HYPE.

2. 사용자 A가 9e18 HYPE가 지원하는 6e18 elyHYPE 인출을 요청합니다.

* `elyAssets` 소각됨.
* 9e18 HYPE가 언스테이킹 금고로 이동됨.
* TVL은 15e18로 유지됨.
* 새 공급 = 4e18.
* 새 가격 = 3.75 HYPE (부풀려짐).

3. 사용자 B가 가격을 업데이트한 다음, 4e18 elyHYPE를 인출하고 **15e18 HYPE**를 받아 불로소득을 얻습니다.

여러 사용자가 이 동작을 반복하여 초과 가치를 빼낼 수 있습니다.

## 권장 사항

이 문제를 해결하는 데는 두 가지 주요 접근 방식이 있습니다:

**옵션 A: TVL 계산 조정**

보류 중인 인출을 명시적으로 추적하고 `getTotalAssetTVL()` 결과에서 자산 지원을 뺍니다. 이렇게 하면 가격이 활성 elyAsset을 지원하는 자산만 정확하게 반영합니다.

**옵션 B: 토큰 소각 지연**

인출이 실제로 완료될 때까지(즉, 사용자가 `completeWithdrawal()`을 호출할 때까지) `elyAssets`의 소각을 연기하여 인출 수명 주기 동안 TVL과 공급이 일치하도록 합니다.

두 접근 방식 모두 자산 대 공급의 일관된 회계를 보장하여 가격 조작을 방지하는 것을 목표로 합니다.

# [H-01] 전략에서 금고로 반환된 자산을 이중으로 계산하는 TVL

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

자산이 전략에 할당되면 `ElytraDepositPoolV1.assetsAllocatedToStrategies[asset]`이 증가합니다. 나중에 전략은 다음을 통해 언스테이킹 금고로 자금을 반환할 수 있습니다:

```solidity
    /// @notice Receives assets from strategy for withdrawal processing
    /// @param asset Asset address
    /// @param amount Amount received
    function receiveFromStrategy(address asset, uint256 amount) external onlyStrategy {
        claimableAssets[asset] += amount;
        emit AssetsReceivedFromStrategy(asset, amount);
    }
```

그러나 동일한 토큰이 두 번 계산되므로 TVL 계산에 이중 계산 문제가 발생합니다:
1. 예금 풀의 전략 할당(`assetsAllocatedToStrategies[asset]`).
2. 금고의 청구 가능한 자산(`claimableAssets[asset]`).

`getTotalAssetTVL`은 두 값을 모두 합산하므로 반환된 전략 자산은 TVL을 부적절하게 부풀립니다.

## 권장 사항

`receiveFromStrategy`가 트리거될 때 `ElytraDepositPoolV1`의 `assetsAllocatedToStrategies` 및 `assetStrategyAllocations`를 줄이는 것을 고려하십시오.

# [H-02] `elyAsset` 가격 계산 오류로 인한 언더플로우 복귀(Reverts)

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`updateElyAssetPrice()` 함수에는 `protocolFeeInHYPE`가 `totalValueInProtocol`에서 차감되기 전에 1e18과 잘못 곱해지는 수학적 오류가 포함되어 있습니다. 두 값 모두 이미 18 소수점 정밀도이므로 이 불필요한 곱셈은 뺄셈이 언더플로우되도록 하는 엄청난 스케일링 불일치를 생성합니다.

```solidity
uint256 newElyAssetPrice = (totalValueInProtocol - (protocolFeeInHYPE * 1e18)) * 1e18 / elyAssetSupply;
```

문제는 다음과 같은 이유로 발생합니다:

2. `protocolFeeInHYPE`는 이미 18 소수점입니다.
3. 코드는 `protocolFeeInHYPE`에 1e18을 곱하여 e36으로 만듭니다.

#### 간단한 예를 들어 보겠습니다:

`elyAssetSupply` - 1,000e18
`oldElyAssetPrice` - 1.00e18
`totalValueInProtocol` - 1,050e18
`protocolFeeInBPS` - 1,000 (10%)

```solidity
tempElyAssetPrice = totalValueInProtocol / elyAssetSupply
                  = 1050e18 / 1000e18
                  = 1.05e18 // I scaled this to fix C-03

increaseInElyAssetPrice = tempElyAssetPrice - oldElyAssetPrice
                        = 1.05e18 - 1.00e18
                        = 0.05e18

rewardInHYPE = (increaseInElyAssetPrice * elyAssetSupply) / 1e18
             = (0.05e18 * 1000e18) / 1e18
             = 50e18

protocolFeeInUSD = (rewardInUSD * protocolFeeInBPS) / 10_000
                 = (50e18 * 1000) / 10_000
                 = 5e18

newElyAssetPrice = (1050e18 - 5e18 * 1e18) * 1e18 / 1000e18
                 = (1050e18 - 5e36) * 1e18 / 1000e18
                 = underflow ❌
```

## 권장 사항

불필요한 1e18 곱셈을 제거하여 계산을 수정하십시오:
```solidity
newElyAssetPrice = (totalValueInProtocol - protocolFeeInHYPE) * 1e18 / elyAssetSupply;
```

# [H-03] `elyAsset` 가격 계산의 스케일링 오류로 인한 수수료 손실

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`ElytraOracleV1`의 `updateElyAssetPrice()` 함수는 적절한 소수점 스케일링 없이 elyAsset 가격이 계산되는 계산 오류를 포함합니다. `totalValueInProtocol / elyAssetSupply` 공식은 가격 계산에 예상되는 18 소수점 정밀도를 고려하지 못합니다.

```solidity
        {
            uint256 tempElyAssetPrice = totalValueInProtocol / elyAssetSupply;
            if (tempElyAssetPrice > oldElyAssetPrice) {
                uint256 increaseInElyAssetPrice = tempElyAssetPrice - oldElyAssetPrice;
                uint256 rewardInHYPE = (increaseInElyAssetPrice * elyAssetSupply) / 1e18;
                protocolFeeInHYPE = (rewardInHYPE * protocolFeeInBPS) / 10_000;
            }
        }
```

이로 인해 다음과 같은 문제가 발생합니다:

1. `totalValueInProtocol`과 `elyAssetSupply`는 모두 18 소수점입니다(예: 2000e18 및 1000e18).
2. 나누기 결과는 2e18 대신 2가 됩니다.
3. 이로 인해 elyAsset 가격이 1e18 배만큼 인위적으로 수축됩니다.
4. `tempElyAssetPrice`는 항상 `oldElyAssetPrice`보다 작으므로 프로토콜은 영구적으로 가치를 잃습니다.

## 권장 사항

누락된 1e18 스케일링 요소를 추가하여 가격 계산을 수정하십시오:

```solidity
tempElyAssetPrice = (totalValueInProtocol * 1e18) / elyAssetSupply
```

# [H-04] TVL 계산에 영향을 미치는 전략 할당 추적 오류

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

Elytra 프로토콜은 자산 할당 및 할당 해제 중에 수동으로 업데이트되는 정적 변수 `assetsAllocatedToStrategies[asset]`을 사용하여 전략 할당을 추적합니다. 그러나 이 추적 메커니즘은 다음을 고려하지 못합니다:

1. **전략 내부의 실시간 수익 성장**.
2. **할당 해제 중 실현된 이익 또는 손실**.
3. **리스테이킹 작업으로 인해 발생한 슬래싱(Slashing) 이벤트**.

결과적으로 `getTotalAssetTVL()` 함수는 전략의 실제 보유량을 과소 또는 과대 평가하여 다음을 초래합니다:

* 잘못된 가격의 `elyAsset` 토큰.
* 악의적인 사용자를 위한 프론트 러닝 기회.
* 슬래싱 관련 손실 반영 불가.
* 사용자가 프로토콜이 실제로 소유한 것보다 더 많이 인출하는 경우 잠재적 파산.

**메커니즘 분석**

TVL은 다음과 같이 계산됩니다:

```solidity
function getTotalAssetTVL(address asset) public view returns (uint256 totalTVL) {
    uint256 poolBalance = IERC20(asset).balanceOf(address(this));
    uint256 strategyAllocated = assetsAllocatedToStrategies[asset];
    uint256 unstakingVaultBalance = _getUnstakingVaultBalance(asset);

    return poolBalance + strategyAllocated + unstakingVaultBalance;
}
```

문제는 다음에서 파생된 `strategyAllocated`에 있습니다:

```solidity
// Called during allocation
assetsAllocatedToStrategies[asset] += amount;

// Called during deallocation
uint256 withdrawn = IElytraStrategy(strategy).withdraw(asset, amount);
if (withdrawn <= assetsAllocatedToStrategies[asset]) {
    assetsAllocatedToStrategies[asset] -= withdrawn;
} else {
    assetsAllocatedToStrategies[asset] = 0;
}
```

이것은 세 가지 중요한 문제를 생성합니다:

**할당 수명 주기 동안 수익이 무시됨**

전략이 수익을 생성하면 실제 잔액이 증가하지만(예: 10 → 12 토큰), `assetsAllocatedToStrategies`는 원래 10으로 고정된 상태로 유지됩니다. 이는 TVL을 과소 보고하고 `elyAsset` 가격을 **수축된** 상태로 유지합니다.

**공격 시나리오:**

* 사용자는 전략 수익이 설명되지 않은 가치를 축적했음을 관찰합니다.
* 그들은 낮은 가격에 elyAssets를 입금합니다.
* 관리자는 자금을 할당 해제합니다(갑자기 수익 실현), 이로 인해 TVL과 가격이 급등합니다.
* 공격자는 즉시 인출하여 정당하게 그들의 것이 아닌 이익을 포착합니다.

**할당 해제 시 잘못된 손익 처리**

`deallocateFromStrategy()` 함수는 인출된 금액이 할당 해제되는 부분과 동일하다고 가정합니다. 그러나 전략의 가치가 변경된 경우:

* **이익이 있는 경우:** 남은 할당이 과장됩니다.
* **손실이 있는 경우:** 부족분이 기록되지 않으므로 할당이 과소 평가되거나 **영구적으로 부풀려집니다**.

이로 인해 TVL이 시간이 지남에 따라 실제 보유량에서 벗어납니다.

**예:**

* 전략에 100 USDC 할당 → TVL은 100 추적
* 전략이 120 USDC로 성장
* 60 인출 → 할당은 40이 되지만 전략에는 60이 남음
  → TVL = 40 + 60 명시되지 않음 = 100 (부정확함; 60이어야 함)

**슬래싱 손실이 반영되지 않음**

리스테이킹 시나리오(예: EigenLayer)에서 검증인에게 할당된 자금은 오작동으로 인해 슬래싱될 수 있습니다. 그러나 현재 모델에는 실제로 자산을 인출하지 않고 `assetsAllocatedToStrategies`를 **줄이는** 메커니즘이 없습니다. 실제로는 슬래싱 후 자산이 더 이상 존재하지 않을 수 있습니다.

따라서 검증인이 자금을 잃더라도 프로토콜은 계속해서 더 높은 TVL과 부풀려진 `elyAsset` 가격을 보고합니다.

## 권장 사항

몇 가지 보완적인 개선 사항이 권장됩니다:

**옵션 A: 실시간 잔액 확인**

`assetsAllocatedToStrategies`를 다음과 같은 **동적 온체인 뷰**로 대체하거나 보완하십시오:

```solidity
IElytraStrategy(strategy).balanceOf(asset)
```

이렇게 하면 `getTotalAssetTVL()`이 수익 및 손실을 포함한 자산의 실제 가치를 반영합니다.

**옵션 B: 할당 해제 시 정확한 손익 회계**

원래 할당된 전략 주식 또는 가치를 추적하고 실제 인출 결과와 비교하여 할당을 올바르게 업데이트하십시오.

다음을 구현할 수 있습니다:

* 수익 인식 회계를 위한 `strategyReportedValue()` 함수.
* 차이를 주기적으로 조정하기 위한 `syncStrategyAllocations()`.

**옵션 C: 수동 슬래싱 조정**

슬래싱 발생 시 `assetsAllocatedToStrategies[asset]`을 수동으로 **줄이는** 메커니즘을 도입하십시오. 예를 들어:

```solidity
function reportStrategyLoss(address asset, uint256 amount) external onlyAdmin {
    require(assetsAllocatedToStrategies[asset] >= amount, "Exceeds tracked allocation");
    assetsAllocatedToStrategies[asset] -= amount;
}
```

# [M-01] `_getAtomicPrices()`는 검증 없이 오래된 오라클 가격을 사용함

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`ElytraDepositPoolV1`의 `_getAtomicPrices()` 함수는 가격 데이터가 오래되었는지 확인하지 않고 `oracle.getAssetPrice()`를 호출합니다. 시스템의 일부 오라클은 마지막 업데이트 시간을 추적하지만 `getAssetPrice()` 함수는 이 타임스탬프를 검증하지 않아 프로토콜이 중요한 계산에 오래된 가격을 사용할 수 있도록 합니다.

```solidity
    function getAssetPrice(address asset) public view onlySupportedAsset(asset) returns (uint256) {
        address oracle = assetPriceOracle[asset];
        if (oracle == address(0)) {
            revert AssetOracleNotSupported();
        }

        // Price fetchers are responsible for returning 18-decimal normalized prices
        uint256 price = IPriceFetcher(oracle).getAssetPrice(asset);

        // Apply safety checks if configured
        if (minPrice > 0 && price < minPrice) {
            revert PriceOutOfRange(price, minPrice, maxPrice);
        }

        if (maxPrice > 0 && price > maxPrice) {
            revert PriceOutOfRange(price, minPrice, maxPrice);
        }

        return price; // Already normalized to 18 decimals by price fetcher
    }
```

문제는 다음과 같은 이유로 발생합니다:

1. `_getAtomicPrices()` 함수는 `oracle.getAssetPrice(asset)`을 직접 호출합니다.
2. 오라클에 최근에 업데이트되지 않은 오래된 가격 데이터가 있을 수 있습니다.
3. 함수는 `lastUpdateTime`을 확인하거나 `getAssetPriceWithStalenessCheck()`를 사용하지 않습니다.
4. 오래된 가격은 `_beforeDeposit()`에서 잘못된 elyAsset 발행 계산으로 이어져 가치 추출 기회를 생성합니다.

동일한 취약점이 `getAssetAmountToReceive()` 및 `_getAtomicPrices()`에 의존하는 기타 함수에 영향을 미칩니다.

## 권장 사항

`getAssetPrice()`를 사용하는 모든 함수에서 `oracle.getAssetPrice()` 호출을 `oracle.getAssetPriceWithStalenessCheck()`로 교체하십시오.

# [M-02] 입금 및 인출이 오래된 `elyAsset` 가격을 사용하여 추출로 이어짐

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`depositAsset()` 및 `withdrawAsset()` 함수는 최신 상태인지 확인하지 않고 오라클의 `elyAssetPrice`에 의존합니다. `updateElyAssetPrice()` 함수는 공개적으로 호출 가능하지만 가격 의존 작업 전에 호출되도록 보장하는 메커니즘이 없어 사용자가 가치 추출을 위해 오래된 가격을 악용할 수 있습니다.

문제는 다음과 같은 이유로 발생합니다:

1. `getElyAssetAmountToMint()` 및 `getAssetAmountToReceive()` 함수는 `oracle.elyAssetPrice()`를 직접 호출합니다.
2. `updateElyAssetPrice()` 함수는 공개이지만 자동으로 트리거되지 않습니다.
3. 시장 상황이 크게 변하면 elyAsset 가격이 오래될 수 있습니다.
4. 사용자는 유리한 오래된 가격을 활용하기 위해 입금/인출 시간을 정할 수 있습니다.

동일한 취약점이 반대로도 존재합니다. 오래된 데이터로 인해 elyAsset 가격이 인위적으로 부풀려지면 사용자는 평소보다 더 많은 자산을 인출할 수 있습니다.

## 권장 사항

`depositAsset()` 및 `withdrawAsset()` 함수 시작 부분에 `updateElyAssetPrice()`를 호출하여 자동 가격 업데이트를 구현하십시오.

# [M-03] 입금 수수료가 `ElytraDepositPoolV1`의 수수료 수신자에게 전송되지 않음

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`ElytraDepositPoolV1` 계약에는 입금과 인출 간에 수수료 처리에 불일치가 있습니다. 인출 수수료는 `feeRecipient`에게 올바르게 전송되지만 입금 수수료는 계산되어 사용자 예금에서 차감되지만 실제로 수수료 수신자에게 전송되지는 않습니다.

`_beforeDeposit` 함수에서:

```solidity
// Calculate fee
uint256 fee = (depositAmount * depositFee) / BASIS_POINTS;
uint256 amountAfterFee = depositAmount - fee;
```
수수료는 계산되어 입금 금액에서 차감되지만 인출 함수와 달리 이 수수료는 `feeRecipient`에게 전송되지 않습니다.

## 권장 사항

일관된 수수료 처리를 위해 입금 수수료를 `feeRecipient`에게 전송하는 것을 고려하십시오. 이는 자산을 `feeRecipient`에게 직접 전송하거나 해당 수수료 `elyAssets`를 `feeRecipient`에게 발행하여 수행할 수 있습니다.

# [M-04] 요청별로 언스테이킹 기간이 스냅샷되지 않아 불일치 발생

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

금고는 모든 인출 요청에 적용되는 단일 `unstakingPeriods[asset]`을 저장합니다. 관리자가 사용자의 요청 후 기간을 업데이트하면 해당 변경 사항이 보류 중인 요청과 향후 요청 모두에 소급 적용됩니다. 예를 들어, 10일 언스테이킹을 요청한 사용자는 기간이 늘어나면 20일을 기다려야 할 수 있으며, 줄어들면 일찍 인출할 수 있어 일관성 없고 불공정한 잠금 해제 타이밍이 발생합니다.

```solidity
    function completeWithdrawal(uint256 requestId) external nonReentrant {
        WithdrawalRequest storage request = withdrawalRequests[requestId];

         // ...

        uint256 unstakingPeriod = unstakingPeriods[request.asset];
        if (block.timestamp < request.requestTime + unstakingPeriod) {
            revert UnstakingPeriodNotComplete();
        }

        // ...
    }

    /// @notice Sets the unstaking period for an asset
    /// @param asset Asset address
    /// @param period Unstaking period in seconds (0 = instant withdrawal)
    function setUnstakingPeriod(address asset, uint256 period) external onlyElytraAdmin {
        uint256 maxPeriod = maxUnstakingPeriods[asset];
        if (maxPeriod > 0) {
            require(period <= maxPeriod, "Period exceeds maximum");
        }
        unstakingPeriods[asset] = period;
        emit UnstakingPeriodUpdated(asset, period);
    }
```

## 권장 사항

각 `requestWithdrawal`에서 현재 언스테이킹 기간을 요청 구조체(예: `request.unstakingPeriod = unstakingPeriods[asset];`)에 캡처하고 `block.timestamp`를 `request.requestTime + request.unstakingPeriod`와 비교하십시오.

# [M-05] 잘못된 입금 한도 확인 순서

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`depositHYPE()` 함수는 입금 금액이 현재 입금 한도를 초과하는지 확인하기 전에 기본 `HYPE`를 `WHYPE`로 래핑하여 풀에 저장합니다. 즉, 후속 확인:

```solidity
// Check deposit limit
if (_checkIfDepositAmountExceedsCurrentLimit(asset, depositAmount)) {
    revert MaximumDepositLimitReached();
}
```
이미 새로 입금된 토큰이 포함된 `balanceOf()`에서 작동하므로 한도 확인이 효과적이지 않습니다. 반면 `depositAsset()`은 전송 전에 한도 확인을 올바르게 수행합니다. 이 불일치는 통과해야 할 사용자의 예금이 되돌려지는 결과를 초래합니다.

문제를 더 잘 이해하기 위해 다음 예를 고려하십시오:
- 예금 풀 한도: 100 WHYPE.
- 현재 잔액: 80 WHYPE.
- 사용자가 15 WHYPE를 입금합니다. 실제 한도는 100 - 80 = 20이므로 통과해야 합니다.
- 그러나 확인은 “입금 예정” 15 WHYPE가 이미 잔액에 포함되어 있으므로 100 - 95 = 5로 잘못 수행하고 한도가 5 WHYPE에 불과하다고 가정합니다.

## 권장 사항

`depositAsset()`에서처럼 기본 `HYPE`를 래핑하기 전에 `_beforeDeposit()`이 호출되도록 `depositHYPE()`의 로직 순서를 변경하십시오. 이렇게 하면 입금 한도 로직이 일관되고 정확하게 유지됩니다.

# [M-06] `withdrawHYPE`의 교차 계약 재진입 버그

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

사용자는 다음과 같이 `withdrawHYPE`를 호출하여 `ElytraDepositPoolV1`에서 기본 HYPE를 인출할 수 있습니다:

```solidity
    function withdrawHYPE(
        uint256 elyAssetAmount,
        address receiver
    )
        // ...
    {
        // ...

        // Burn elyAsset tokens
        address elyAssetToken = elytraConfig.getElyAsset();
        IElyERC20(elyAssetToken).burnFrom(msg.sender, elyAssetAmount);

        // Unwrap WHYPE to native HYPE and transfer to receiver
        IWHYPE(WHYPE_TOKEN).withdraw(whypeAmount);
        (bool success,) = payable(receiver).call{ value: hypeAmount }("");
        require(success, "HYPE transfer failed");

        // Transfer fee to recipient if applicable (in WHYPE)
        if (fee > 0) {
            IERC20(WHYPE_TOKEN).safeTransfer(feeRecipient, fee);
        }

        emit AssetWithdrawal(msg.sender, WHYPE_TOKEN, elyAssetAmount, hypeAmount);
    }
```

그러나 기본 HYPE 금액이 수수료가 `feeRecipient`에게 전송되기 **전에** 사용자에게 전송되기 때문에 악의적인 사용자는 트랜잭션 흐름을 조작하고 오라클을 통해 Ely 시스템에 다시 진입하여 `ElytraOracleV1::updateElyAssetPrice()`를 호출할 수 있습니다. 잘못된 TVL 회계(elyAssets는 소각되었지만 모든 HYPE가 전송되지 않았기 때문)로 인해 아직 전송되지 않은 수수료 금액은 보상으로 간주됩니다. 결과적으로 새로운 elyAssets가 프로토콜 수수료로 발행될 수 있으며 업데이트된 가격은 이 인출에 대해 동일하게 유지되는 대신 감소합니다.

예를 들어, 총 공급량이 10e18이고 TVL이 13e18(가격 = 1.3, 인출 수수료 = 10%)일 때 사용자가 1 elyHYPE를 소각하면 시스템은 일시적으로 9e18 공급과 ~11.83e18 TVL을 봅니다. 이로 인해 오라클은 가격을 ~1.314로 업데이트합니다. 0.13e18 수수료는 보상으로 해석되고 프로토콜 수수료가 발행됩니다. 그러나 수수료가 최종적으로 전송되면 TVL이 떨어지고 보상이 발생했다고 생각하여 재무부에 초과 `elyHYPE`를 발행했기 때문에 가격이 ~1.29로 잘못 떨어집니다. 올바른 가격은 1.3으로 유지되어야 했습니다. 따라서 잘못된 양의 elyAssets가 프로토콜 수수료로 발행되고 가격이 일관되지 않게 됩니다.

## 권장 사항

CEI 패턴을 따르고 `feeRecipient`에게 수수료를 전송한 **후** 기본 HYPE 금액을 사용자에게 전송하는 것이 좋습니다.

# [L-01] 특정 조건에서 예금 풀이 인플레이션 공격에 직면할 수 있음

_해결됨 (Resolved)_

이 보고서는 상당히 **낮은 가능성**이지만 악용될 경우 **잠재적으로 높은 영향**을 미치는 엣지 케이스를 설명합니다.

참고: *공격자는 첫 번째 예금자로서 `elyAssetPrice`를 크게 부풀릴 수 있습니다. 가격 편차 제한이 설정된 경우 주가 업데이트가 차단됩니다. 그러나 주가 업데이트는 사용자 작업과 분리되어 있으므로 추가 입금이 수행된 후 이 문제는 자체적으로 완화된다는 점에 유의하는 것이 중요합니다. 이 경우 `elyAsset`의 총 공급량은 정상 금액으로 돌아가고 주가를 다시 업데이트할 수 있습니다.*

이 보고서의 나머지 부분은 `pricePercentageLimit`가 `0`으로 설정된 엣지 케이스를 고려합니다.

공격자는 필요한 최소 금액만 입금하고 1 wei를 제외한 모든 주식을 인출하여 주가를 부풀릴 수 있습니다. 이 시점에서 `elyAsset`의 총 공급량은 `1`이 되고 풀의 총 자산은 공격자의 초기 입금에서 가져온 입금 수수료와 같게 됩니다. 입금 수수료가 구성되지 않은 경우 공격자는 자산을 직접 기부하여 `totalAssets`를 늘릴 수 있습니다.

`ElytraOracleV1::updateElyAssetPrice` 함수가 호출되면 `newElyAssetPrice`는 `totalValueInProtocol * 1e18 / elyAssetSupply`로 계산됩니다:

```solidity
        uint256 newElyAssetPrice = (totalValueInProtocol - (protocolFeeInHYPE * 1e18)) * 1e18 / elyAssetSupply;
```

`totalValueInProtocol`은 18 소수점으로 표시되고 `elyAssetSupply == 1`이므로 결과적으로 새 주가는 `1e18` 요소만큼 부풀려집니다.

이 단계에서 공격자는 주가를 부풀려 예금자가 입금에서 `0`주를 받도록 해야 합니다.

**권장 사항**
앞서 언급했듯이 이 부풀려진 주가는 후속 입금이 수행된 후 자체적으로 완화됩니다. 그러나 프로토콜의 현재 구현은 기술적으로 이 공격을 엣지 케이스로 나타낼 수 있으므로 이 문제에 대한 가능한 완화책은 초기화 중에 "데드 쉐어(dead shares)"를 발행하는 것입니다.

# [L-02] 인출 시 슬리피지 보호 누락

_해결됨 (Resolved)_

예금 풀 계약의 입금 함수는 슬리피지 보호 역할을 하는 `minElyAssetExpected` 값을 받습니다. 그러나 인출 함수에는 이러한 보호 기능이 포함되어 있지 않습니다

결과적으로 사용자는 급격한 가격 변동 및 환율 업데이트로 인해 인출에 대해 불리한 요율을 받을 수 있습니다.

인출 함수에 `minUnderlyingAssetExpected` 매개변수를 추가하고 사용자에게 전송되거나 인출 보류 중인 자산 금액이 `>= minElyAssetExpected`인지 검증하는 것이 좋습니다.

# [L-03] `ElytraUnstakingVaultV1`에서 `withdrawalRequests` 배열의 무제한 증가

_해결됨 (Resolved)_

`ElytraUnstakingVaultV1` 계약의 `withdrawalRequests` 배열은 인출이 처리된 후 크기가 줄어들거나 지워지지 않습니다. 결과적으로 배열은 시간이 지남에 따라 무한히 커질 수 있습니다. 이러한 무제한 증가는 배열을 반복하는 함수(예: `getPendingWithdrawals()`)의 가스 비용 증가와 같은 문제로 이어질 수 있으며, 결국 가스 부족 오류나 호출 실패를 일으킬 수 있습니다. 특히 보류 중인 인출을 검색하는 데 의존하는 오프체인 통합이나 프론트엔드의 경우 더욱 그렇습니다.

**권장 사항**
`withdrawalRequests` 배열에서 완료된 요청을 삭제하십시오.

# [L-04] `setPricePercentageLimit()`에서 제한 전에 `elyAsset` 가격 업데이트 실패

_해결됨 (Resolved)_

`ElytraOracleV1`의 `setPricePercentageLimit()` 함수는 현재 가격이 최신인지 확인하기 위해 `updateElyAssetPrice()`를 먼저 호출하지 않고 `pricePercentageLimit`를 수정합니다.

```solidity
    function setPricePercentageLimit(uint256 _limit) external override onlyElytraAdmin {
        pricePercentageLimit = _limit;
        emit PricePercentageLimitUpdated(_limit);
    }

    function setProtocolFee(uint256 _feeInBPS) external override onlyElytraAdmin {
        uint256 MAX_FEE_BPS = 1000; // Max 10% fee
        if (_feeInBPS > MAX_FEE_BPS) {
            revert ProtocolFeeExceedsMaximum(_feeInBPS, MAX_FEE_BPS);
        }
        protocolFeeInBPS = _feeInBPS;
        emit ProtocolFeeUpdated(_feeInBPS);
    }
```

**권장 사항**
구성 변경 사항을 적용하기 전에 현재 가격이 최신인지 확인하기 위해 `setPricePercentageLimit()` 및 `setProtocolFee()` 함수 시작 부분에 `updateElyAssetPrice()`를 호출하십시오.

# [L-05] `totalSupply`가 0일 때 `updateElyAssetPrice()`에서 타임스탬프 업데이트 실패

_해결됨 (Resolved)_

`ElytraOracleV1`의 `updateElyAssetPrice()` 함수는 총 공급량이 0이 되면 `elyAssetPrice`를 `1e18`로 설정하지만 `lastPriceUpdateTimestamp`를 업데이트하지 못하는 설계가 포함되어 있습니다. 이로 인해 타임스탬프를 업데이트하지 않고 가격이 수정되는 불일치가 발생하여 `getElyAssetPriceWithStalenessCheck()`에 의존하는 외부 통합이 잘못된 복귀(revert)를 하게 됩니다.

```solidity
        if (elyAssetSupply == 0) {
            elyAssetPrice = 1 ether;
            emit ElyAssetPriceUpdated(elyAssetPrice);
            return;
        }
```

이 문제는 다음과 같은 이유로 발생합니다:

1. `elyAssetSupply`가 0이 되면 함수는 `elyAssetPrice = 1e18`을 설정합니다.
2. 그러나 `lastPriceUpdateTimestamp`를 현재 블록 타임스탬프로 업데이트하지 않습니다.
3. `getElyAssetPriceWithStalenessCheck()`를 호출하는 외부 시스템은 오래된 타임스탬프를 보고 가격이 오래된 것으로 잘못 판단하여 트랜잭션을 되돌립니다.

**권장 사항**
총 공급이 0이라서 `1e18`로 설정되는 경우를 포함하여 `elyAssetPrice`가 수정될 때마다 `lastPriceUpdateTimestamp`를 업데이트하십시오:

```solidity
elyAssetPrice = 1e18;
lastPriceUpdateTimestamp = block.timestamp;
```

# [L-06] `getTotalAssetDeposits()`의 잔액 조작을 통한 그리핑(Griefing) 공격 가능

_해결됨 (Resolved)_

`getTotalAssetDeposits()` 함수는 `IERC20(asset).balanceOf(address(this))`를 사용하여 계약의 현재 잔액을 읽어 총 자산 예금을 계산합니다. 이는 악의적인 사용자가 직접 토큰 전송을 통해 합법적인 예금자에게 피해를 줄 수 있는 취약점을 생성합니다.

```solidity
    function getTotalAssetDeposits(address asset) public view override returns (uint256 totalAssetDeposit) {
        // All assets are now ERC20 tokens (including WHYPE)
        return IERC20(asset).balanceOf(address(this));
    }
```

예를 들어:
- 현재 총 예금: 900 토큰.
- 예금 한도: 1000 토큰.
- Alice가 100 토큰을 입금하려고 합니다(허용되어야 함).
- Bob이 계약에 1 wei를 전송합니다.
- Alice의 100 토큰 예금은 거부됩니다.

**권장 사항**
총 자산 예금을 별도의 저장 변수에 저장하십시오.

# [L-07] 제로 할당 확인 없이 자산 전략 변경

_해결됨 (Resolved)_

`updateAssetStrategy` 함수는 기존 전략에 대한 현재 할당이 0인지 확인하지 않고 자산의 전략을 업데이트할 수 있도록 허용합니다. 이로 인해 자산 할당 추적의 불일치와 잠재적인 자금 손실 또는 잘못된 관리가 발생할 수 있습니다.

전략 업데이트를 허용하기 전에 현재 전략에 대한 자산 할당이 0인지 확인하는 체크를 추가하는 것이 좋습니다. 또는 일관성을 유지하고 오류를 방지하기 위해 전략을 변경하기 전에 자산의 할당 해제를 요구하십시오.

# [L-08] 수수료 계산 시 내림(Rounding down)으로 사용자가 수수료를 우회할 수 있음

_해결됨 (Resolved)_

`withdrawAsset()`, `withdrawHYPE()`, `_beforeDeposit()`과 같은 함수에서 수수료 계산은 정수 나눗셈을 사용하여 수행되며, 이는 결과를 내림합니다. 결과적으로 계산된 수수료가 매우 작으면 내림 후 0이 될 수 있어 사용자가 수수료를 전혀 지불하지 않을 수 있습니다.

이 동작은 공격자가 의도된 프로토콜 수수료를 우회하기 위해 소규모 트랜잭션을 반복적으로 수행함으로써 악용될 수 있습니다.

**권장 사항**

수수료 계산을 내림 대신 올림(rounding up)하여 아주 작은 작업이라도 최소 수수료가 발생하도록 하고 수수료 회피를 방지하십시오. 이는 프로토콜 수익을 보호하고 모든 사용자에게 공정성을 보장하는 데 도움이 됩니다.

# [L-09] `deallocateFromStrategy()`의 일관성 없는 전략 할당 추적

_해결됨 (Resolved)_

`deallocateFromStrategy()` 함수는 `assetsAllocatedToStrategies[asset]`과 `assetStrategyAllocations[asset][strategy]`의 합계 간에 불일치를 초래할 수 있습니다.
두 조건 중 하나가 값을 0으로 설정하는 경우(즉, `withdrawn > tracked amount`) 값이 일관성이 없고 동기화되지 않습니다.

이러한 불일치로 인해 부정확한 TVL 보고 또는 결함 있는 내부 회계가 발생하여 향후 할당 또는 할당 해제 결정에 영향을 미칠 수 있습니다.

```solidity
    function deallocateFromStrategy(
        address asset,
        uint256 amount
    )
        external
        nonReentrant
        onlyElytraOperator
        onlySupportedAsset(asset)
    {
        address strategy = elytraConfig.assetStrategy(asset);
        require(strategy != address(0), "No strategy set for asset");

        // Withdraw from strategy
        uint256 withdrawn = IElytraStrategy(strategy).withdraw(asset, amount);

        // Update tracking (ensure we don't underflow)
        if (withdrawn <= assetsAllocatedToStrategies[asset]) {
            assetsAllocatedToStrategies[asset] -= withdrawn;
        } else {
@>          assetsAllocatedToStrategies[asset] = 0;
        }

        if (withdrawn <= assetStrategyAllocations[asset][strategy]) {
            assetStrategyAllocations[asset][strategy] -= withdrawn;
        } else {
@>          assetStrategyAllocations[asset][strategy] = 0;
        }

        emit AssetDeallocatedFromStrategy(asset, strategy, withdrawn);
    }
```

# [L-10] `burnFrom` 함수가 무제한 허용량을 잘못 감소시킴

_해결됨 (Resolved)_

`ElyAsset.sol`의 `burnFrom` 함수는 허용량이 `type(uint256).max`로 설정된 경우에도 허용량을 감소시켜 표준 ERC20 동작에서 벗어납니다. `type(uint256).max`는 관례적으로 무제한 승인을 나타냅니다.

표준 ERC20 구현(OpenZeppelin 포함)에서 허용량이 `type(uint256).max`로 설정되면 무제한 승인을 나타내며 전송 또는 소각 중에 감소해서는 안 됩니다. ERC20 표준을 올바르게 따르려면 허용량이 `type(uint256).max`인 경우 감소하지 않는 것을 고려하십시오.

# [L-11] `elyHYPE`의 `grantRole` 및 `revokeRole`에 관리자 권한 누락

_해결됨 (Resolved)_

`elyHYPE` 계약의 `grantRole` 및 `revokeRole` 함수는 `elytraConfig` 계약에 대한 역할 관리 호출을 수행하도록 설계되었지만 근본적인 액세스 제어 문제로 인해 항상 실패합니다.

문제는 다음과 같은 이유로 발생합니다:
- `onlyElytraAdmin` 수정자는 호출자가 `elytraConfig`에서 관리자 권한을 가지고 있는지 확인합니다.
- 그런 다음 함수는 `msg.sender`가 `elyHYPE` 계약 주소가 되는 `elytraConfig.grantRole()`을 호출합니다.
- `elytraConfig.grantRole()` 함수(OpenZeppelin의 `AccessControl` 사용)는 호출자에게 관리자 권한이 있어야 합니다.
- 그러나 `elyHYPE` 계약 자체에는 `elytraConfig`의 관리자 역할이 부여되지 않았습니다.

이로 인해 이러한 함수에 대한 모든 호출이 되돌려져 완전히 기능하지 않는 코드가 됩니다. 이를 해결하려면 제거하거나, 대안으로 더 위험하지만 모든 `elyAssets`에 관리자 권한을 부여할 수 있습니다.

# [L-12] 업그레이드 가능한 스마트 계약에 스토리지 갭이 포함되지 않음

_해결됨 (Resolved)_

각 계약은 OpenZeppelin의 `UUPSUpgradeable`을 상속하지만 향후 업그레이드를 위해 추가 스토리지 슬롯을 예약하지 않고 자체 상태 변수도 선언합니다. 구현 계약 끝에 `uint256[50] private __gap;`이 없으면 나중에 새 변수를 추가하면 기존 스토리지 레이아웃이 이동하고 상태가 손상될 수 있습니다.

스토리지 갭 구현을 고려하십시오:

```solidity
uint256[49] private __gap;
```

# [L-13] 전략 할당을 고려하지 않아 예금 한도 우회

_해결됨 (Resolved)_

`ElytraDepositPoolV1` 계약의 `getAssetCurrentLimit()` 함수는 새 예금에 대한 남은 용량을 반환하여 각 자산에 대한 최대 예금 한도를 적용하기 위한 것입니다. 그러나 현재 구현은 풀이 직접 보유한 자산 잔액(`IERC20(asset).balanceOf(address(this))`)만 고려하고 `assetsAllocatedToStrategies`를 통해 전략에 할당된 자산은 포함하지 않습니다. 결과적으로 프로토콜은 의도된 한도를 초과하는 예금을 허용할 수 있습니다.

**권장 사항**

`getAssetCurrentLimit()`을 업데이트하여 총 자산 예금을 계산할 때 풀 잔액과 전략에 할당된 금액(즉, `assetsAllocatedToStrategies[asset]`)을 모두 포함하도록 하십시오.

# [L-14] 자산 비활성화 프론트 러닝으로 인한 TVL 하락 및 보유자 손실

_해결됨 (Resolved)_

프로토콜은 `ElytraConfigV1` 계약의 `updateAssetSupport()`를 통해 관리자가 특정 자산을 비활성화할 수 있도록 허용하며, 이는 TVL 계산에서 자산을 제거합니다. 그러나 이 작업은 관리자 트랜잭션이 채굴되기 직전에 곧 비활성화될 자산을 입금하는 공격자에 의해 프론트 러닝될 수 있습니다.

관리자의 작업이 실행되면 자산은 더 이상 TVL에 포함되지 않지만 총 공급은 증가하여 elyAsset 가격이 하락합니다.
결과적으로 elyAsset 토큰을 보유한 다른 모든 사용자는 공격자의 프론트 러닝으로 인한 희석 및 가격 억제로 인해 손실을 입습니다.

**권장 사항**

`updateAssetSupport()`를 동일한 블록 또는 그 이전에 대상 자산의 추가 예금을 방지하는 로직을 포함하는 원자적(atomic) 작업으로 만드십시오. 또는 지연이나 대기열을 도입하십시오.

# [L-15] 빈번한 `updateElyAssetPrice` 호출로 라운딩으로 인한 수수료 우회

_해결됨 (Resolved)_

`ElytraDepositPoolV1` 계약의 `updateElyAssetPrice()` 함수는 자산 가치 증가에 따라 프로토콜 수수료를 계산합니다. 이 수수료는 먼저 HYPE로 계산된 다음 재무부에 발행될 elyAsset 토큰으로 변환됩니다.

그러나 변환 과정에서의 내림으로 인해 계산된 HYPE 수수료가 매우 작으면 결과 elyAsset 수수료가 0이 될 수 있습니다. 공격자는 자산 가치를 조금씩 증가시키며 `updateElyAssetPrice()`를 자주 호출하여 이를 악용할 수 있으며, 사실상 프로토콜 수수료 발행을 피할 수 있습니다.

이로 인해 사용자는 시간이 지남에 따라 의도된 프로토콜 수수료 메커니즘을 우회할 수 있어 수수료 징수가 저조하고 프로토콜의 수익 모델을 약화시킬 수 있습니다.

**권장 사항**

HYPE에서 elyAsset 토큰으로 변환하는 동안 수수료 계산을 올림하여 해당되는 경우 프로토콜이 항상 최소 수수료를 징수하도록 하십시오. 이렇게 하면 작은 가격 변동으로 인해 수수료가 0으로 내림되는 것을 방지할 수 있습니다.

# [L-16] `requestWithdrawal()`이 금액을 계산하지만 토큰을 잠그지 않음

_해결됨 (Resolved)_

`ElytraUnstakingVaultV1`에서 `requestWithdrawal()` 함수는 요청 시점의 elyAsset 현재 가격을 기준으로 사용자에게 빚진 기본 자산의 양을 계산합니다. 그러나 해당 토큰을 잠그거나 예약하지 않습니다.

결과적으로 요청과 확정 시간 사이에 elyAsset의 가격(또는 TVL)이 하락하면 인출을 이행하기에 충분한 토큰이 없을 수 있습니다.
사용자가 `elyAsset` 토큰의 100%를 인출하도록 요청하는 최악의 시나리오를 고려해 보십시오. 언스테이킹 기간 동안 TVL이나 가격이 조금이라도 하락하면 금고가 약속된 금액에 미치지 못하여 인출을 확정할 수 없게 될 수 있습니다.

이로 인해 신뢰성 문제가 발생하고 가격 변동성이나 프로토콜 잔액 변화로 인해 사용자가 자금을 회수할 수 없는 상태에 갇힐 수 있습니다.

**권장 사항**

확정 시 가용성을 보장하기 위해 인출 요청 시 기본 토큰 금액을 잠그거나 예약하는 메커니즘을 도입하십시오. 대안으로 인출 확정 시 자산 금액을 계산하십시오.

# [L-17] `updateAssetStrategy`가 전략 할당이 0인지 확인하지 않음

_해결됨 (Resolved)_

`ElytraConfigV1` 계약의 `updateAssetStrategy` 함수는 프로토콜이 자산에 새 전략을 할당할 수 있도록 허용합니다. 그러나 이 변경을 수행하기 전에 현재(이전) 전략의 자산 할당이 0인지 확인하지 않습니다.

전환 시점에 이전 전략에 할당된 자금이 아직 남아 있는 경우 해당 자금에 접근할 수 없게 될 수 있습니다. `deallocateFromStrategy` 함수는 **현재** 전략에서만 작동하기 때문입니다. 결과적으로 업데이트 후 관리자는 표준 프로토콜 메커니즘을 통해 이전 전략에서 자금을 할당 해제하거나 인출할 수 있는 능력을 잃게 됩니다.

이로 인해 자금이 이전 전략에 잠겨 회계 문제, 자본 비효율성 또는 비상 또는 수동 복구 방법이 없는 경우 영구적인 자금 손실이 발생할 수 있습니다.

**권장 사항**

`updateAssetStrategy`에서 전략 변경을 허용하기 전에 자산에 대해 현재 전략의 할당이 0인지 확인하도록 강제하십시오. 대안으로 락인(lock-in)을 방지하고 운영 무결성을 유지하기 위해 이전 전략에서 잔여 자금을 마이그레이션하거나 복구하는 명확한 메커니즘을 제공하십시오.

# [L-18] `updatePriceOracleForValidated`의 검증 부족

_해결됨 (Resolved)_

`ElytraOracleV1`의 `updatePriceOracleForValidated`의 목표는 지원되는 자산에 대한 오라클을 업데이트하는 것이지만, 이에 대한 검증 확인도 수행합니다.

그러나 하드코딩된 범위(1e17–1e19)가 프로토콜의 구성된 `minPrice`/`maxPrice` 대신 사용되고 `maxAssetPriceAge`에 대해 오라클의 마지막 업데이트 타임스탬프를 확인하지 않으므로 확인이 올바르지 않고 부족합니다. 결과적으로 악의적이거나 오래된 가격 피드가 수락되어 잘못된 가격의 입금 또는 인출로 이어질 수 있습니다.

**권장 사항**

고정 상수 대신 계약의 `minPrice`/`maxPrice` 매개변수에 대해 검증하고, 새 오라클 주소를 수락하기 전에 가져온 가격의 나이(`getLastUpdateTime`을 통해)가 `maxAssetPriceAge`를 초과하지 않는지 요구하는 것을 고려하십시오.

# [L-19] `setUnstakingPeriod`에서 한도 확인을 우회하는 제로 `maxUnstakingPeriods`

_해결됨 (Resolved)_

`ElytraUnstakingVaultV1`에서 관리자가 즉시 인출 완료를 강제하려는 경우 `unstakingPeriods[asset] = 0`을 설정합니다.

또한 관리자가 허용된 범위에 캡(`maxUnstakingPeriods[asset]`)을 설정할 수 있는 로직이 있습니다. 그러나 해당 캡이 0이면 즉시 전용으로 제한하는 대신 확인을 완전히 건너뛰어 모든 기간을 허용하는 등 모든 한도를 제거합니다. 올바른 로직은 `maxUnstakingPeriods[asset] == 0`인 경우 `period == 0`만 허용되는 것입니다.

**권장 사항**

`maxUnstakingPeriods[asset] == 0`일 때 `period == 0`을 요구하여 0을 유효한 "즉시 전용" 설정으로 취급하는 것을 고려하십시오.

# [L-20] `_beforeDeposit()`의 수수료 후 `depositAmount` 검증

_해결됨 (Resolved)_

`_beforeDeposit()`에서 계약은 입금 수수료를 공제하기 전에 `minAmountToDeposit`, `DUST_AMOUNT` 및 0 금액 확인에 대해 원시 `depositAmount`를 검증합니다. 결과적으로 사용자는 수수료 전 임계값을 통과할 수 있지만 수수료 공제 후 의도한 최소 또는 먼지(dust) 한도 미만의 유효 입금으로 끝날 수 있습니다.

**권장 사항**

수수료를 먼저 뺀 다음 다음을 적용하도록 검증 단계의 순서를 변경하십시오:
1. `depositAmountAfterFee > 0`.
2. `depositAmountAfterFee >= minAmountToDeposit`.
3. `depositAmountAfterFee >= MIN_DUST_AMOUNT`.

총 유입을 제한하기 위한 것이므로 수수료 전 금액에 대해 `_checkIfDepositAmountExceedsCurrentLimit` 확인을 남겨두십시오.

# [L-21] 예금 풀로 직접 전송된 기본 `HYPE`는 영구적으로 잠김

_해결됨 (Resolved)_

계약에는 기본 `HYPE`를 수락할 수 있는 `receive()` 함수가 포함되어 있습니다. 그러나 누가 풀에 기본 `HYPE`를 보낼 수 있는지에 대한 제한이 없습니다. 이로 인해 의도된 `withdrawHYPE()` 흐름 외부에서도 모든 주소가 예금 풀에 기본 `HYPE`를 전송할 수 있는 시나리오가 생성됩니다.

계약은 입금/인출 로직 동안 `IWHYPE`로 래핑/언래핑을 통해서만 기본 `HYPE`를 처리하므로 `receive()`를 통해 계약으로 직접 전송된 모든 기본 `HYPE`는 추적, 환불 또는 청구되지 않으며 사실상 해당 토큰을 영구적으로 잠그게 됩니다.

**권장 사항**

`WHYPE` 계약에서만 기본 `HYPE`를 수락하도록 `receive()` 함수를 제한하십시오. 이렇게 하면 합법적인 언래핑 작업만 기본 HYPE 전송을 트리거하고 다른 주소의 우발적 또는 악의적인 전송은 되돌려집니다.

# [L-22] 18이 아닌 소수점 토큰으로 인한 잘못된 가격 계산

_해결됨 (Resolved)_

`ElytraDepositPoolV1` 계약은 서로 다른 소수 자릿수를 가진 토큰을 처리하도록 설계되었지만 18이 아닌 소수점 토큰을 적절하게 확장하는 메커니즘이 부족합니다. 이로 인해 소수점이 더 적은 토큰(예: 6 소수점의 USDC)이 18 소수점을 가진 것처럼 취급되어 엄청난 가격 계산 오류가 발생합니다.

문제는 다음과 같은 이유로 발생합니다:

1. `depositAsset()` 함수는 토큰의 기본 소수로 `depositAmount`를 수락합니다.
2. `_beforeDeposit()` 함수는 소수점 스케일링 없이 이 금액을 처리합니다.
3. `getElyAssetAmountToMint()`의 가격 계산은 모든 금액이 18 소수점이라고 가정합니다.
4. USDC와 같은 6 소수점 토큰의 경우 1,000,000 USDC(1M 토큰) 입금은 1,000,000e18이 아닌 1e12로 취급됩니다.

이로 인해 심각한 가치 추출 시나리오가 생성됩니다:

1. 사용자가 1,000,000 USDC(6 소수점을 가진 1M 토큰)를 입금합니다.
2. 시스템은 이를 1,000,000e18 대신 1e12로 취급합니다.
3. 사용자는 원래 받아야 할 것보다 1e12배 적은 elyAsset 토큰을 받습니다.

동일한 문제가 `getAssetAmountToReceive()`, `getTotalAssetTVL()`을 포함한 시스템 전체의 모든 가격 계산에 영향을 미칩니다.

**권장 사항**

다음과 같이 소수점 스케일링을 구현하십시오:

1. 토큰 소수점을 가져오는 함수 추가: `IERC20(asset).decimals()`.
2. `_beforeDeposit()`에서 `depositAmount`를 18 소수점으로 스케일링: `depositAmount * (10 ** (18 - tokenDecimals))`.
3. 모든 가격 계산 함수에 동일한 스케일링 로직을 적용하십시오.
