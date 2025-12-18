
# Pashov Audit Group 소개 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 입증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트에서 10만 달러 이상의 상금을 획득했거나, 다회 우승자이거나, 우리와의 감사에서 진정으로 뛰어난 성과를 보여주었습니다. 우리는 검증되고 동기 부여된 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견 및 수정하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

<p>Pashov Audit Group에 의해 <strong>hyperlendx/hyperlend-utils</strong> 및 <strong>hyperlendx/hyperevm-oracle</strong> 저장소에 대한 시간 제한 보안 검토가 수행되었으며, <strong>t.aksoy, DemoreXTess, Drynooo</strong>가 참여하여 <strong>Hyperlend Utils</strong>를 검토했습니다. 총 <strong>6</strong>개의 문제가 발견되었습니다.</p>

# Hyperlend Utils 소개 (About Hyperlend Utils)

<p>Hyperlend Utils 계약은 자동 청산 보호, 담보 스왑, 일괄 대출/상환을 포함하여 Hyperlend에서 사용자 포지션을 관리하기 위한 도구 세트를 제공하며, 상태 요인(Health Factor) 임계값, TWAP 확인 및 토큰별 한도와 같은 안전 장치를 통합합니다. 읽기 전용 마진 추적, 원자적 작업, 외부 CoreWriter 작업에 크게 의존하지 않는 구성 가능한 사용자 보호를 제공하여 HyperLend 및 HyperCore와의 상호 작용을 단순화하도록 설계되었습니다.</p>

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [835056165de225941e949beea66747e9df9d2164](https://github.com/hyperlendx/hyperlend-utils/tree/835056165de225941e949beea66747e9df9d2164)<br>&nbsp;&nbsp;(hyperlendx/hyperlend-utils)<br>• [e352f15eba26dd24748617664ca044572c821a75](https://github.com/hyperlendx/hyperevm-oracle/tree/e352f15eba26dd24748617664ca044572c821a75)<br>&nbsp;&nbsp;(hyperlendx/hyperevm-oracle)

**수정 검토 커밋 해시:**<br>• [5b58ed59e39e407789e3bc54013f6f466f991b7c](https://github.com/hyperlendx/hyperlend-utils/tree/5b58ed59e39e407789e3bc54013f6f466f991b7c)<br>&nbsp;&nbsp;(hyperlendx/hyperlend-utils)<br>• [ebb2fd00ce6737d984c3a94585244ad7984bef1c](https://github.com/hyperlendx/hyperevm-oracle/tree/ebb2fd00ce6737d984c3a94585244ad7984bef1c)<br>&nbsp;&nbsp;(hyperlendx/hyperevm-oracle)

# 범위 (Scope)

- `UtaHelper.sol`
- `DualFallbackOracle.sol`

# 발견 사항 (Findings)

# [H-01] 더 이상 사용되지 않는 `safeApprove()` 사용이 풀에 대한 담보 승인을 차단함

_해결됨 (Resolved)_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

프로젝트는 `safeApprove()`를 더 이상 사용하지 않는(deprecated) Openzeppelin@v4.9.6을 사용합니다. 담보에 대한 승인이 0이 아닌 경우 모든 승인을 차단하기 때문에 여기서 안전한 승인 사용은 안전하지 않습니다:

```solidity
//    function executeOperation()

        for (uint256 i = 0; i < _collateralActions.length; ++i){
            uint256 amount = _collateralActions[i].amount;
            IERC20 token = _collateralActions[i].token;

            //transfer tokens from the caller & approve pool contract to spend them
            token.safeTransferFrom(msg.sender, address(this), amount);
@>          token.safeApprove(address(pool), type(uint256).max);

            //supply tokens on behalf of the msg.sender
            pool.supply(address(token), amount, msg.sender, 0);
        }
```

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/dc44c9f1a4c3b10af99492eed84f83ed244203f6/contracts/token/ERC20/utils/SafeERC20.sol#L45-L54

```solidity
    function safeApprove(IERC20 token, address spender, uint256 value) internal {
        // safeApprove should only be called when setting an initial allowance,
        // or when resetting it to zero. To increase and decrease it, use
        // 'safeIncreaseAllowance' and 'safeDecreaseAllowance'
        require(
            (value == 0) || (token.allowance(address(this), spender) == 0),
            "SafeERC20: approve from non-zero to non-zero allowance"
        );
        _callOptionalReturn(token, abi.encodeWithSelector(token.approve.selector, spender, value));
    }
```

담보에 대한 첫 번째 승인 후, `safeApprove()`의 `require` 줄 때문에 다른 모든 호출은 실패합니다.

## 권장 사항

`safeApprove()` 대신 `forceApprove()`를 사용하는 것을 고려하십시오.

# [L-01] Core 구조(rescue)에 대한 경쟁 상태(Race condition)

_해결됨 (Resolved)_

사용자가 대출을 실행하는 동안 소유자가 USDC 토큰에 대해 rescueHyperCore()를 호출하면, 사용자의 전송이 HyperCore에서 실행되기 전에 구조(rescue)가 자금을 소진하여 자금이 조용히 손실될 수 있습니다. HyperEVM 사양에 따르면 CoreWriter 작업은 USDC 전송이 도착한 후 실행됩니다. 소유자의 구조와 사용자의 전송이 동일한 블록에서 발생하는 경우 사용자 전송이 조용히 실패할 수 있습니다.

블록 실행 순서 (HyperEVM 사양에 따름)
```
   Step 1: L1 block is built
   Step 2: EVM block is built (both transactions execute here)
   Step 3: EVM -> Core transfers are processed
   Step 4: CoreWriter actions are processed
```

USDC 구조와 사용자 대출은 동일한 블록에서 허용되어서는 안 됩니다. 허용되는 경우 USDC 구조를 신중하게 처리해야 합니다.

# [L-02] 폴백(Fallback) 로직이 더 최신 오라클을 선택하지 못함

_해결됨 (Resolved)_

기본 오라클이 비정상일 때 계약은 폴백 오라클을 확인합니다.
폴백도 비정상인 경우 → 폴백이 덜 오래되었음에도 불구하고 계약은 기본 오라클 데이터를 반환합니다. 시스템이 더 오래되거나 부정확한 데이터를 반환할 수 있습니다.

둘 다 비정상인 경우 최신 가격을 선택하는 것이 더 나은 로직일 수 있습니다.

# [L-03] 오라클에 비상 가격 검증이 없음

_해결됨 (Resolved)_

`isEmergencyOracleEnabled = true`일 때, 계약은 데이터 최신성이나 가격 정확성에 관계없이 비상 오라클의 latestRoundData()를 즉시 반환합니다. 가격이 0이거나 유효하지 않은 데이터를 반환하더라도 마찬가지입니다.

비상 오라클에 대한 가격 확인을 추가하고, 유효하지 않은 경우 다른 오라클 가격을 반환해야 합니다.

# [L-04] 오라클 호출에 try/catch 누락으로 폴백 비활성화

_해결됨 (Resolved)_

`DualFallbackOracle` 계약은 기본 소스가 비정상일 때 폴백 오라클을 활용하도록 설계되었습니다. 그러나 계약은 `PRIMARY_SOURCE.latestRoundData()` 및 `FALLBACK_SOURCE.latestRoundData()`에 대해 직접 외부 호출을 수행합니다.

기본 오라클이 되돌려지면(예: 기본 집계기가 일시 중지되거나 내부 오류가 발생하는 경우) 전체 트랜잭션이 되돌려집니다. 실행 흐름은 `_isPrimaryHealthy`를 확인하거나 폴백 소스를 쿼리하려는 로직에 도달하지 않아 이중화 메커니즘을 사실상 쓸모없게 만듭니다.

```solidity
// hyperevm-oracle/contracts/adapters/DualFallbackOracle.sol

(
    uint80 _roundId,
    int256 _answer,
    uint256 _startedAt,
    uint256 _updatedAt,
    uint80 _answeredInRound
) = PRIMARY_SOURCE.latestRoundData(); // @audit If this reverts, the whole tx fails
```

**권장 사항**

`latestRoundData`에 대한 호출을 `try/catch` 블록으로 감싸십시오. 기본 소스 호출이 실패하면 catch 블록이 폴백 소스에서 데이터를 가져오려고 시도하여 오류를 처리해야 합니다.

# [L-05] 선택적 `Emergency_Source`가 address(0)인 경우 배포 실패

_해결됨 (Resolved)_

`getData`의 로직은 사용하기 전에 주소가 0이 아닌지 명시적으로 확인하므로 `EMERGENCY_SOURCE`가 선택 사항임을 암시합니다. 그러나 생성자는 검증을 위해 `EMERGENCY_SOURCE`에서 무조건 `decimals()`를 호출합니다.

배포자가 `_emergencySource`를 `address(0)`으로 초기화하려고 시도하면(나중에 설정하거나 비활성화 상태로 유지하려는 의도), 0 주소에서 `decimals()`를 호출하는 것이 실패하기 때문에 배포가 되돌려집니다.

```solidity
        if (PRIMARY_SOURCE.decimals() != FALLBACK_SOURCE.decimals()) revert InvalidDecimals();
        // @audit Reverts if EMERGENCY_SOURCE is address(0)
        if (PRIMARY_SOURCE.decimals() != EMERGENCY_SOURCE.decimals()) revert InvalidDecimals();
        decimals = PRIMARY_SOURCE.decimals();
```

**권장 사항**

생성자에서 소수점을 검증하기 전에 `EMERGENCY_SOURCE`가 `address(0)`이 아닌지 확인하는 검사를 추가하십시오.
