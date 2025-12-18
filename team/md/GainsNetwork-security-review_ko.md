# 소개 (About)

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **gTrade-contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# gTrade-contracts 정보 (About gTrade-contracts)

**첫 번째 보안 검토에서 복사됨**

`gTrade` 프로토콜은 Gains Network에서 개발한 레버리지 거래 플랫폼입니다. 암호화폐 토큰에서 최대 150배, 외환에서 1000배, 상품에서 250배까지 허용합니다. 아키텍처는 주로 `GNS` ERC20 토큰과 ERC721 유틸리티 토큰을 중심으로 회전하며, 두 토큰 모두 플랫폼 사용 시 스프레드 감소, 거버넌스 등 다양한 유틸리티를 제공합니다.

**계속됨**

이 프로토콜은 스테이킹의 새 버전을 구현하여 여러 보상 토큰을 허용합니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [5f31ff9bf7de2237569c195d81bbf54f48ac0be2](https://github.com/GainsNetwork-org/gTrade-contracts/tree/5f31ff9bf7de2237569c195d81bbf54f48ac0be2)**

**_수정 검토 커밋 해시_ - [a29fb9b5a475a2f516488f0e128fb96e2e1e78d8](https://github.com/GainsNetwork-org/gTrade-contracts/tree/a29fb9b5a475a2f516488f0e128fb96e2e1e78d8)**

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `GNSStaking`

# 발견 사항 (Findings)

# [M-01] 보상 토큰 USDC에 의해 블랙리스트에 오르면 사용자는 언스테이킹할 수 없음 (User can't unstake in case he gets blacklisted by reward token USDC)

## 심각도

영향: 높음 (High). 사용자는 자신의 Gns를 언스테이킹할 수 없습니다.

가능성: 낮음 (Low). USDC 블랙리스팅은 일반적인 작업이 아닙니다.

## 설명

USDC는 보상 토큰으로 사용되어야 하며, 블랙리스트 기능이 있음에 유의하십시오. 따라서 USDC 토큰은 블랙리스트에 오른 주소로/주소로부터 전송될 수 없습니다.

현재 수확된 토큰 보상은 베스팅된 토큰 청구, 스테이크, 언스테이크, 베스팅 취소 등 모든 상호 작용 시 스테이커에게 강제로 전송됩니다. 또한 `unstakeGns()`에서는 모든 보상이 처리됩니다. 따라서 토큰 전송 중 하나라도 되돌려지면(revert), 사용자는 수확할 수 없으므로 언스테이킹할 수 없습니다.

언스테이킹하는 동안 먼저 모든 토큰에 대한 보상을 처리합니다.

```solidity
    function unstakeGns(uint128 _amountGns) external {
        require(_amountGns > 0, "AMOUNT_ZERO");

        harvestDai();
@>      harvestTokens();

        ... // Main unstake logic
    }
```

그런 다음 USDC를 포함한 모든 보상 토큰의 수확된 금액을 전송합니다. 위에서 논의한 바와 같이 USDC 전송은 되돌려질 수 있습니다.

```solidity
    function _harvestToken(address _token, uint128 _stakedGns) private {
        ... // Main harvest logic

@>      IERC20(_token).transfer(msg.sender, uint256(pendingTokens));

        emit RewardHarvested(msg.sender, _token, pendingTokens);
    }
```

따라서 USDC에 의해 블랙리스트에 오른 사용자는 모든 토큰 보상을 수확할 수 없으므로 언스테이킹할 수 없습니다. 그의 지분은 영원히 존재하며 결코 청구되지 않을 보상의 일부를 받게 됩니다.

사용자가 베스팅된 금액을 청구하려고 할 때도 동일한 시나리오가 발생합니다.

## 권장 사항

청구 패턴을 사용하십시오. 토큰을 전송하는 대신 내부 잔액만 늘리십시오. 다음과 같이:

```solidity
mapping (address token => uint256 pendingBalance) pendingBalances;
```

또한 청구 방법을 도입하십시오.

# [L-01] 보상 토큰을 제거할 수 없음 (Reward tokens cannot be removed)

`GNSStaking`의 새 버전은 여러 보상 토큰을 허용합니다. 계약 소유자는 `addRewardToken`을 통해 새 보상 토큰을 추가합니다. 그러나 보상 토큰을 제거할 방법은 없습니다.

사용자의 모든 주요 상호 작용인 `stakeGns`, `unstakeGns` 및 `createUnlockSchedule`은 모든 보상 토큰을 반복하여 보상을 청구하고 최신 부채를 업데이트합니다. 보상 토큰의 양이 너무 많이 증가하면 가스 부족(out of gas)이 발생할 수 있습니다.

최대 보상 토큰 수를 추가하거나 보상 토큰을 제거/비활성화하는 방법을 추가하는 것을 고려하십시오.

# [L-02] `transfer`/`transferFrom`의 사용 (Use of `transfer`/`transferFrom`)

모든 ERC20 구현이 잘 작동하고 실패 시 되돌려지는(revert) 것은 아닙니다. 일부는 단순히 false를 반환하고 일부는 아무것도 반환하지 않을 수도 있습니다(메인넷의 USDT).

비정형적인 ERC20 토큰에 대해 가능한 한 안전하도록 OpenZeppelin `SafeERC20`의 `safeTransfer` 및 `safeTransferFrom`을 사용하는 것을 고려하십시오.
