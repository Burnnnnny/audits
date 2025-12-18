# 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Pirex** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Pirex 소개

Pirex를 사용하면 ETH 보유자가 풀링(pooling)을 통해 스테이킹 보상을 받을 수 있습니다. Pirex를 통해 스테이킹하면 블록 검증/증명 및 MEV 수익과 `pxETH`를 통한 복리 보상을 통해 이익을 얻습니다. `pxETH` 토큰은 Pirex가 래핑(wrapped)한 ETH 버전으로, 보유자는 `AutoPxETH`에 스테이킹하여 수익을 올리거나 토큰을 ETH로 상환할 수 있습니다.

[추가 문서](https://github.com/redacted-cartel/pirex-eth-contracts/blob/master/README.md)

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼침.

- 중간 (Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼침.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼침.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방하는 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮음.

- 중간 (Medium) - 조건부로 동기 부여된 공격 벡터일 뿐이지만 여전히 상대적으로 가능성이 높음.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 너무 낮거나 공격자가 인센티브가 거의 또는 전혀 없이 상당한 지분을 필요로 함.

## 심각도 수준별 조치 필요성

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정해야 함 (Should fix)

- 낮음 (Low) - 수정할 수 있음 (Could fix)

# 보안 평가 요약

**_검토 커밋 해시_ - [12b25265e17d141faf1c10fa84d9826838107e67](https://github.com/redacted-cartel/dinero-pirex-eth/tree/12b25265e17d141faf1c10fa84d9826838107e67)**

**_수정 검토 커밋 해시_ - [277ca47a598694a51261beac86dc21ec4937a699](https://github.com/redacted-cartel/dinero-pirex-eth/tree/277ca47a598694a51261beac86dc21ec4937a699)**

# 발견 사항

# [M-01] `emergencyWithdraw` 메서드는 `PirexEth`를 손상된 상태로 만들 수 있음

## 심각도

**영향:**
높음, `PirexEth`의 로직이 손상될 것이므로

**가능성:**
낮음, 비상 상황이 필요하고 그 후 계약을 사용해야 하므로

## 설명

`PirexEth`의 `emergencyWithdraw` 메서드는 ETH 인출을 허용합니다. 이 ETH는 아직 ETH 2.0 예금 계약에 예치되지 않은 `pendingDeposit` 잔액일 수 있으며 `emergencyWithdraw` 메서드에서 인출되면 계약은 손상된 상태가 됩니다. `pendingDeposit` 변수는 계약의 ETH 잔액보다 큰 값을 가지므로 `emergencyWithdraw` 호출 후에 사용되는 경우 예금 트랜잭션이 되돌려지게(revert) 됩니다.

## 권장 사항

`emergencyWithdraw` 메서드를 변경하여 `pendingDeposit` 없이 초과 잔액만 인출하거나, `pendingDeposit`을 사용할 때 전체 잔액을 인출하고 상태 변수를 0으로 만들도록 하십시오.

# [L-01] `withdrawalCredentials` 업데이트에 결함이 있음

`PirexEthValidators`의 `setContract` 메서드는 `RewardRecipient` 계약 주소가 업데이트될 때 `withdrawalCredentials` 상태 변수를 업데이트합니다. `_initializedValidators` 배열에 이미 검증인이 있는 경우 다른 `withdrawalCredentials`로 구성되었을 것입니다. 검증인이 1 ETH라도 예치한 경우 더 많이 예치할 때 예금 계약에서 `withdrawalCredentials` 인수가 사용되지 않고 무시되므로 이는 문제가 되지 않습니다. 그러나 추가적인 안전과 논리적 정확성을 위해 `_initializedValidators` 배열에 요소가 없을 때만 `withdrawalCredentials` 업데이트를 허용하도록 하십시오.

# [L-02] `previewRedeem`의 페널티 계산이 잘못된 방향으로 반올림함

`AutoPxEth`의 `previewRedeem` 메서드는 다음 코드로 페널티를 계산합니다:

```solidity
uint256 penalty = (_totalSupply == 0 || _totalSupply - shares == 0)
    ? 0
    : assets.mulDivDown(withdrawalPenalty, FEE_DENOMINATOR);
```

문제는 수학적으로 페널티를 내림(round down)한다는 것입니다. 하지만 ERC4626 표준에 따라 볼트에 대한 최상의 보안을 따라야 하며, 주식을 소각할 때 사용자가 받게 될 자산을 계산할 때 자산 금액은 내림되어야 합니다. 여기서는 페널티가 내림되지만 페널티가 `assets`에서 차감되므로 반대 효과가 발생합니다. `penalty` 계산에서 `mulDivDown` 대신 `mulDivUp`을 사용하십시오.

# [L-03] 상한 설정자 확인 누락

`PirexEth`에서 `setMaxFee` 메서드에 상한이 없습니다. 최대 수수료를 100% 이상으로 설정하면 수수료도 100% 이상으로 설정할 수 있는데, 이는 불가능해야 합니다. `setMaxFee` 메서드에 최대 상한(예: 50%)을 추가하십시오.

`PirexEthValidators`의 `setMaxProcessedValidatorCount` 메서드에서도 동일한 문제가 발생합니다. 매우 높은 값을 입력하면 예금에 대한 DoS가 발생할 수 있습니다. `_count` 인수에 최대값(예: 20)을 추가하십시오.

# [L-04] 허용량을 0으로 설정하는 것은 유효한 작업이어야 함

`PxEth`의 `operatorApprove` 메서드는 현재 허용량 금액이 0이면 되돌려지는데(revert) 이는 논리적 오류입니다. 허용량을 제거(또는 0으로 설정)하는 것은 유효한 작업이어야 합니다. `operatorApprove`에서 다음 줄을 제거하십시오:

```solidity
if (_amount == 0) revert Errors.ZeroAmount();
```
