# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**coffeexcoin/endless-clouds** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Endless Clouds 소개 (About Endless Clouds)

EndlessCloudsClaimer 스마트 계약은 적격 수령인이 미리 정의된 가득(vesting) 일정에 따라 토큰을 청구할 수 있도록 하며, 진위 확인을 위해 Merkle 증명을 통해 검증됩니다.

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

**_검토 커밋 해시_ - [e9ef869920abb7c3c7372d08b39d2c5fb3b0f9a1](https://github.com/coffeexcoin/endless-clouds/tree/e9ef869920abb7c3c7372d08b39d2c5fb3b0f9a1)**

**_수정 검토 커밋 해시_ - [be156b94b42facebe18c7ac11716799cff524321](https://github.com/coffeexcoin/endless-clouds/tree/be156b94b42facebe18c7ac11716799cff524321)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `EndlessCloudsClaimer`
- `EndlessClouds`

# 발견 사항 (Findings)

# [L-01] 청구가 일시 중지되었을 때의 일관성 없는 동작

EndlessCloudsClaimer 계약은 청구가 일시 중지되었을 때(`claimEnabled` = false) 일관성 없는 동작을 보입니다. 문제는 `claimEnabled`가 거짓일 때 0을 반환하는 `_computeClaimableAmount` 함수에서 비롯됩니다:

```solidity
function _computeClaimableAmount(ClaimData calldata claimData) internal view returns (uint256) {
    if (!claimEnabled) {
        return 0;
    }
    // ... rest of the function
}
```

이 설계는 청구 함수에서 두 가지 뚜렷한 시나리오를 생성합니다:

- 토큰을 청구하지 않은 사용자의 경우 청구 트랜잭션이 성공하지만 토큰은 전송되지 않습니다(claimAmount = 0).
- 토큰을 부분적으로 청구한 사용자의 경우 아래 계산의 언더플로우로 인해 청구 트랜잭션이 되돌려집니다. `totalClaimableVested`가 0이고 `claimedAmount` > 0이면 이 작업은 언더플로우됩니다.

        uint256 claimAmount = totalClaimableVested - claimedAmount;

```solidity
    function claim(ClaimData calldata claimData, address recipient, bytes32[] calldata proof) external {
        ...
        uint256 totalClaimableVested = _computeClaimableAmount(claimData);
        uint256 claimAmount = totalClaimableVested - claimedAmount; // @audit when claimEnabled = false, totalClaimableVested = 0 and claim tx reverts

        ...
    }
```

청구 tx 동작의 불일치는 사용자에게 혼란을 줄 수 있습니다. 청구가 일시 중지된 경우 되돌려서(reverting) 오류 메시지를 반환하는 것이 좋습니다.

```diff
    function _computeClaimableAmount(ClaimData calldata claimData) internal view returns (uint256) {
        if (!claimEnabled) {
-           return 0;
+           revert ClaimUnabled();
        }
```

# [L-02] 기간 종료 확인 누락

`withdrawTokens()` 및 `enableClaim()`은 소유자가 계약에서 토큰을 인출하거나 청구 기간 종료 후 EndlessClouds 토큰을 인출할 수 있도록 허용합니다. 그러나 코드에는 이 조건에 대한 실제 강제가 없습니다.

```solidity
    /// @notice Withdraw tokens from the contract. To be used in case of accidental deposits,
    /// or to withdraw any remaining tokens if the claim period is ended.
    /// @param token The token to withdraw
>>  function withdrawTokens(address token) external onlyOwner {
        SafeTransferLib.safeTransferAll(token, msg.sender);
    }

    /// @notice Enable the token claim functionality
>>  function enableClaim(bool enabled) external onlyOwner {
        claimEnabled = enabled;
    }
```

소유자가 토큰을 인출하도록 허용하기 전에 토큰이 EndlessClouds인지, 청구 기간이 종료되었는지 확인하는 것이 좋습니다.

# [L-03] `getClaimableAmount` 함수가 잘못된 정보를 반환할 수 있음

`getClaimableAmount` 뷰 함수는 주어진 `claimData` 및 `recipient`를 가진 리프(leaf)의 존재를 확인하지 않습니다. 사용자는 잘못된 데이터에 대해서도 답변을 받을 수 있습니다. 리프 존재 여부를 확인하는 것을 고려하십시오.

```solidity
    function getClaimableAmount(ClaimData calldata claimData, address recipient) external view returns (uint256) {
        return _computeClaimableAmount(claimData) - totalClaimed[keccak256(abi.encode(recipient, claimData))];
    }
<...>
    function _computeClaimableAmount(ClaimData calldata claimData) internal view returns (uint256) {
        if (!claimEnabled) {
            return 0;
        }

        uint256 periodsElapsed;
        if (block.timestamp >= claimData.vestStart) {
            uint256 elapsedTime = block.timestamp - claimData.vestStart;
            periodsElapsed = elapsedTime / VESTING_PERIOD_LENGTH;

            if (periodsElapsed >= claimData.vestingPeriods) {
                periodsElapsed = claimData.vestingPeriods;
            }
        }

        return claimData.immediateClaim + periodsElapsed * claimData.amountPerVestingPeriod;
    }
```

# [L-04] 다중 서명 지갑으로 계약을 배포할 경우 소유자가 잘못 설정됨

EndlessCloudsClaimer의 소유자는 모든 토큰을 인출하고, 새 Merkle 루트를 설정하고, 청구 프로세스를 활성화/비활성화할 수 있는 상당한 권한을 가집니다. 따라서 소유자가 다중 서명 지갑일 것으로 예상하는 것이 합리적입니다.

그러나 EndlessClouds가 다중 서명 지갑(예: Gnosis Safe)을 통해 배포되는 경우 소유자는 의도한 다중 서명 주소가 아니라 트랜잭션을 시작한 EOA(`tx.origin`)가 됩니다. 이를 통해 `tx.orgin` 주소가 모든 토큰을 빼낼 수 있습니다.

```solidity
    constructor(address _endlessClouds, bytes32 _merkleRoot) {
>>>     _initializeOwner(tx.origin);
        ENDLESS_CLOUDS = _endlessClouds;
        merkleRoot = _merkleRoot;
    }
```

생성자에서 매개변수로 계약의 소유자를 지정하십시오.

```diff
-       constructor(address _endlessClouds, bytes32 _merkleRoot) {
+       constructor(address _endlessClouds, bytes32 _merkleRoot, address _owner) {
-           _initializeOwner(tx.origin);
+           _initializeOwner(_owner);
            ENDLESS_CLOUDS = _endlessClouds;
            merkleRoot = _merkleRoot;
        }
```
