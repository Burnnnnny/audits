# 정보

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. 그의 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**veTenet** 프로토콜에 대해 시간 제한이 있는 보안 검토를 **pashov**가 수행했으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# veTenet 소개

`veTenet`은 Tenet에 배포됩니다. Tenet은 네트워크의 검증자가 스테이킹하는 담보로 LSD(유동성 스테이킹 파생상품)를 사용하는 EVM 호환 레이어-1 블록체인입니다. 프로토콜 자체는 기본 `TENET` 자산 보유자에게 투표권을 부여하여 온체인 투표를 통해 다양한 게이지(gauges)의 토큰 인센티브 분배에 영향을 미칠 수 있도록 합니다. 이는 Curve DAO의 투표 및 보상 메커니즘에서 로직과 디자인을 차용했습니다. `veTenet`은 검증자 성능을 기반으로 하는 고유한 블록당 보상 분배 메커니즘을 사용합니다.

[추가 문서](https://nivalis.notion.site/Design-Documentation-veTENET-14f37c7cb5aa4368a41c089356f34150)

## 관찰 사항

`TENET` 토큰은 양도 가능하지만 `veTENET` 토큰은 잠금 기간(최대 2년)이 끝날 때까지 양도할 수 없습니다.

`veTENET` 토큰의 투표권은 시간이 지남에 따라 감소하며, 2년 잠금 시 최대 2.5배의 부스트를 받습니다.

`TENET` 토큰은 `RewardVault` 계약으로 직접 발행(mint)됩니다.

`AddressRegistry` 계약의 소유자는 프로토콜에 대한 완전한 통제권을 가집니다.

코드 커버리지가 100%와 거리가 멀기 때문에 프로토콜에서 더 많은 단위 테스트를 수행해야 합니다.

## 특권 역할 및 행위자

- Governance - 코드베이스의 대부분 메서드를 호출할 수 있습니다. 예를 들어 `RewardVault`에서 일일 보상률을 설정할 수 있습니다.
- TLSD Factory - `GaugeProxy`에서 새로운 `subGauge`를 생성하거나 `validatorFee` 또는 `validatorFeeRecipient`를 설정할 수 있습니다.
- Address Registry owner - `governance`를 포함하여 프로토콜에서 사용하는 모든 주소를 설정할 수 있습니다.
- Emergency - `RewardVault` 계약에서 `togglePause`를 호출할 수 있습니다.
- Vesting Beneficiary - 베스팅된 보상을 받을 것으로 예상됩니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향(Impact)

- 높음(High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간(Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음(Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성(Likelihood)

- 높음(High) - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 도난당하거나 분실될 수 있는 자금의 양에 비해 공격 비용이 비교적 낮습니다.

- 중간(Medium) - 조건부 인센티브가 있는 공격 벡터일 뿐이지만 여전히 발생할 가능성이 비교적 높습니다.

- 낮음(Low) - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적(Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음(High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간(Medium) - 수정해야 함

- 낮음(Low) - 수정할 수 있음

# 보안 평가 요약

**_검토 커밋 해시_ - [9771131e6e21a4dc804e729026228f6e2092415a](https://github.com/tenet-org/veTenet-contracts/commit/9771131e6e21a4dc804e729026228f6e2092415a)**

**_수정 검토 커밋 해시_ - [434bcc0ac716d2143dccd01eef74e16e97116cdb](https://github.com/tenet-org/veTenet-contracts/tree/434bcc0ac716d2143dccd01eef74e16e97116cdb)**

# 결과

# [C-01] `subGauge` 복제로 인해 초기화되지 않은 프록시가 발생할 수 있음

## 심각도

**영향:**
높음, `subGauge` 초기화가 프론트러닝 될 수 있음

**가능성:**
높음, 모든 새로운 `subGauge`가 취약할 것임

## 설명

`GaugeFactory::createSubGauge`에서 `Clones::cloneDeterministic` 메서드는 새 `subGauge`를 배포하는 데 사용됩니다. 문제는 `GaugeFactory::createGaugeProxyAndSubGauges`와 달리 `createSubGauge`에서는 복제 후 `subGauge`가 초기화되지 않아 프론트러닝 공격에 취약하다는 것입니다. 예시 시나리오:

1. Alice가 `GaugeFactory::createSubGauge`를 호출하여 새 `subGauge`를 배포합니다.
2. `GaugeProxy::addSubGauge`를 호출하여 `subGauge`가 `GaugeProxy`에 추가됩니다.
3. 이제 Alice는 `subGauge`에서 `initialize`를 호출하기 위해 트랜잭션을 보냅니다.
4. Bob은 Alice의 트랜잭션을 보고 프론트러닝 하여 자신이 제공한 인수(`stakingToken`, `governance` 등)로 초기화하므로 그가 제어하게 됩니다.

## 권장 사항

`GaugeFactory::createSubGauge`에서 새로 복제된 `subGauge`를 `GaugeFactory::createGaugeProxyAndSubGauges`에서 수행되는 것과 동일한 방식으로 초기화하십시오.

# [C-02] 코드베이스가 취약한 Vyper 컴파일러 버전을 사용 중임

## 심각도

**영향:**
높음, 금지된 메서드에서 재진입이 가능함

**가능성:**
높음, 해당 컴파일러 버전으로 컴파일될 때 바이트코드는 항상 취약할 것임

## 설명

한 달 전, [여기](https://www.halborn.com/blog/post/explained-the-vyper-bug-hack-july-2023)에 설명된 대로 Vyper 컴파일러의 버그로 인해 이더리움 생태계에서 주요 익스플로잇이 발생했습니다. Vyper 팀은 언어 자체에 `@nonreentrant` 주석(또는 함수 데코레이터)을 기본적으로 내장했지만, 버전 0.2.15에서 버그가 도입되어 메서드에 주석을 사용하더라도 본질적으로 재진입 보호가 없게 되었습니다. [여기](https://hackmd.io/@vyperlang/HJUgNMhs2#:~:text=All%20Vyper%20contracts%20that%20have,the%20malfunctioning%20re%2Dentrancy%20guard.)에 명시된 대로 "버전 v0.2.15, v0.2.16 및 v0.3.0으로 컴파일된 모든 Vyper 계약은 오작동하는 재진입 가드에 취약합니다.".

리포지토리의 Vyper 파일(`BoostDelegationV2` 제외)은 모두 컴파일러 버전 0.2.16을 가지고 있으며, 이는 `hardhat.config.ts`에도 구성되어 있습니다. `GaugeController`, `LiquidityGaugeV4` 및 `VotingEscrow`에는 `@nonreentrant`로 표시된 여러 메서드가 있습니다. 이러한 모든 메서드는 재진입 보호가 되지 않습니다.

## 권장 사항

모든 Vyper 계약의 컴파일러 버전을 버그가 수정된 최소 v0.3.1로 업그레이드하십시오.

# [C-03] 새로운 `subGauge`를 프로토콜에서 사용할 수 없음

## 심각도

**영향:**
높음, 중요한 프로토콜 기능이 작동하지 않음

**가능성:**
높음, 버그가 매번 발생할 것임

## 설명

`GaugeFactory`의 `createSubGauge` 메서드는 새 `subGauge`를 생성하지만 `createGaugeProxyAndSubGauges`에서 수행되는 것처럼 실제로 `subGauge`를 `GaugeProxy`에 추가하지는 않습니다. `GaugeProxy` 계약에 `addSubGauge` 메서드가 있지만 `gaugeFactory` 또는 `TLSDFactory`에서만 호출할 수 있습니다. 두 계약 모두 실제로 `addSubGauge` 메서드를 직접 호출할 수 있는 방법이 없으므로, 새 `subGauge`를 생성할 수는 있지만 실제로는 `GaugeProxy`에 추가할 방법이 없습니다.

## 권장 사항

`createSubGauge` 메서드를 변경하여 주어진 `_stakingToken` 인수와 함께 `createGaugeProxyAndSubGauges`에서와 동일한 `GaugeProxy(_gaugeProxy).addSubGauge(_stakingTokens[i], subGauge);` 호출을 실행하도록 하십시오.

# [H-01] 보상률을 변경하면 청구 불가능한 수익이 발생함

## 심각도

**영향:**
높음, 누적된 보상이 검증자에게 분배되지 않을 것임

**가능성:**
중간, 메서드가 특정 순서로 호출되어야 하기 때문

## 설명

`RewardVault`의 `setDailyRewardRate`는 `lastReward` 저장 변수를 현재 날짜로 재설정합니다. 이는 이제 `RewardVault::distributeRewards`가 호출될 때 최신 `setDailyRewardRate` 호출 이후의 기간과 현재 순간만 누적 보상을 계산하는 데 사용된다는 것을 의미합니다. 이 접근 방식의 문제는 `setDailyRewardRate`를 호출하기 전에 누적되었지만 청구되지 않은 보상이 있는 경우 해당 보상은 더 이상 청구할 수 없게 되며 검증자는 이를 잃게 된다는 것입니다.

## 권장 사항

`setDailyRewardRate`에서 `lastReward`를 업데이트하기 전에 `distributeRewards`를 호출하십시오. 이는 `lastReward`가 이미 설정된 경우에만 수행되어야 합니다.

# [M-01] 여러 메서드에서 입력이 불충분하게 검증됨

## 심각도

**영향:**
높음, 검증자의 누적 수익 손실 또는 프로토콜의 DoS로 이어질 수 있기 때문

**가능성:**
낮음, `Governance` 또는 `TLSDFactory`의 구성 오류 또는 악의적이거나 타협된 경우를 필요로 하기 때문

## 설명

코드베이스 전체의 여러 메서드에 대한 입력 검증이 불충분합니다:

- `RewardDistributor::setScanPeriod`에서 `_newScanPeriod` 인수가 너무 큰 숫자이면 `_distribute` 메서드에서 DoS가 발생할 수 있으며, 너무 작으면 검증자의 청구되지 않은 수익이 손실될 수 있습니다.
- `GaugeProxy::setValidatorFee`에서 `_fee` 인수가 `FEE_BASE`보다 크면 `distributeToken`에서 DoS가 발생하며, `FEE_BASE`와 같으면 전체 `rewardAmount`가 `validatorFeeRecipient` 주소로 전송됩니다.
- `TenetVesting::initialize`에서 `cliff`, `vestingPeriod` 및 `startTime` 입력이 제대로 검증되지 않았으며, `revocable == false`인 경우 계약에 자금이 갇힐 수 있는 너무 큰 값을 포함할 수 있습니다.

## 권장 사항

`scanPeriod`의 경우 너무 큰 값을 허용하지 않도록 하고 검증자가 청구되지 않은 수익을 사용하지 않도록 하십시오. `validatorFee`의 경우 적절한 값, 아마도 10%로 제한하십시오. 베스팅 매개변수의 경우 `cliff`가 너무 크지 않은지, `vestingPeriod`도 마찬가지인지 확인하고 `startTime`이 너무 먼 미래이거나 이미 지나지 않았는지 확인하십시오.

# [M-02] 비표준 ERC20 토큰이 계약에 갇힐 것임

## 심각도

**영향:**
높음, 자금 동결로 이어질 것이기 때문

**가능성:**
낮음, 실제로 사용되지 않을 수 있는 일부 토큰만 문제가 될 것이기 때문

## 설명

`USDT`와 같은 토큰은 `ERC20::transfer` 호출 시 `bool`을 반환하지 않는 반면, 다른 토큰은 실패한 전송 시 되돌리지(revert) 않고 그냥 `false`를 반환합니다. 이러한 유형의 토큰은 `VestRewardReceiver::sendTokens` 및 `TenetVesting::rescueFunds`에서 제대로 지원되지 않습니다. 해당 메서드의 코드는 이를 고려하지 않고 일반 `ERC20::transfer` 메서드를 사용하기 때문입니다. 이로 인해 예를 들어 `TenetVesting`에서 `USDT`에 대해 `rescueFunds`를 호출할 수 없게 되어 자금이 갇히게 됩니다.

## 권장 사항

대신 OpenZeppelin `SafeERC20` 라이브러리를 사용하고 `transfer` 호출을 `safeTransfer`로 변경하여 이러한 비표준 ERC20 토큰도 지원하십시오.

# [M-03] 수령인이 설정되지 않은 경우 검증자 수수료가 손실됨

## 심각도

**영향:**
높음, 검증자의 수익 손실로 이어질 것이기 때문

**가능성:**
낮음, 특정 시나리오가 필요하기 때문

## 설명

`GaugeProxy::distributeToken`에서 보상은 검증자 활동에 따라 게이지에 분배됩니다. 또한 `validatorFee`라고 하는 일부 수수료가 공제되어 각 검증자의 다른 수령인에게 전송됩니다. 문제는 코드가 `validatorFeeRecipient`가 설정되었는지 확인하지 않는다는 것입니다. 설정되지 않은 경우 `fee`는 소각됩니다(제로 주소로 전송됨).

## 권장 사항

`IERC20Metadata(tenet).transfer(validatorFeeRecipient[validators[i]], fee);`를 수행하기 전에 `validatorFeeRecipient[validators[i]] != address(0)`인지 확인하여 전송된 `fee`를 소각하지 않도록 하십시오.

# [M-04] `AddressRegistry`의 `owner`가 프로토콜을 장악할 수 있음

## 심각도

**영향:**
높음, 자금이 도난당할 수 있기 때문

**가능성:**
낮음, 악의적이거나 타협된 `AddressRegistry`의 `owner`가 필요하기 때문

## 설명

현재 대부분의 계약에는 `GOVERNANCE` 계정에서만 호출할 수 있는 중요한 메서드가 있습니다. 이 계정은 `AddressRegistry` 계약의 `owner`가 언제든지 설정하거나 업데이트할 수 있으며, 이를 통해 자신이 제어하는 주소를 설정한 다음 보호된 모든 메서드의 기능을 자신의 이익을 위해 악용할 수 있습니다(예: 자신을 프로토콜 보상의 유일한 수령인으로 만듦).

## 권장 사항

`AddressRegistry` 계약의 `owner`는 다중 서명 또는 `Governor` 스마트 계약에 의해 제어되는 `Timelock` 계약이어야 합니다. 그래야 실제 공격이 충분히 느리게 수행되어 사용자가 프로토콜 사용을 중단하거나 자금을 인출할 시간을 가질 수 있습니다.

# [L-01] 일시 중지된 `RewardVault`는 보상률 설정을 허용해서는 안 됨

`RewardVault` 계약에는 `distributeRewards`가 호출될 때 계약이 아무것도 하지 않도록 하는 `pause` 메커니즘이 있습니다. 이것의 문제는 계약이 일시 중지된 상태에서도 `setDailyRewardRate`를 여전히 호출할 수 있다는 것인데, 이래서는 안 됩니다. `RewardVault::setDailyRewardRate`에서도 `isPaused` 플래그를 확인하고 `true`와 같으면 되돌리도록(revert) 하십시오.

# [L-02] 죽은(killed) `TenetVesting`은 `withdrawable`이 0을 반환해야 함

`TenetVesting` 계약에는 베스팅 일정을 취소할 수 있는 `kill` 메커니즘이 있습니다. 거의 모든 메서드가 `isKilled` 플래그를 확인하고 `true`와 같으면 되돌아가지만 `withdrawable` 메서드는 예외입니다. 정확히 말하면, 메서드는 `isKilled == true`일 때 0을 반환해야 합니다.

# [L-03] 제한 없는 배열을 반복하면 DoS가 발생할 수 있음

`GaugeProxy` 계약에는 `gauges` 및 `stakingTokens` 배열에 새 요소를 푸시하는 `addSubGauge` 메서드가 있습니다. 이 두 배열의 크기는 모두 제한되어야 합니다. 너무 많은 항목이 추가되면 배열을 반복하는 데 가스 비용이 너무 많이 들 수 있고, 필요한 가스 양이 블록 가스 제한을 초과하면 이를 수행하는 메서드에 대해 DoS 상태가 발생할 수 있기 때문입니다. `removeSubGauge` 메서드가 있지만 이 또한 배열을 반복하므로 차선책입니다. 가장 좋은 접근 방식은 `MAX_GAUGES_COUNT` 및 `MAX_STAKING_TOKENS_COUNT` 상수 값을 도입하여 배열 크기를 제한하는 것입니다.

# [L-04] `subGauge` 복제 시 일관성 없는 `salt` 인수

`GaugeFactory`에는 `createGaugeProxyAndSubGauges`와 `createSubGauge` 모두에서 `subGauge` 복제가 있지만, 복제를 위한 `salt` 인수가 계산되는 방식이 다릅니다. 이로 인해 동일한 `stakingToken`에 대해 여러 `subGauge`가 생성될 수 있으며, 이는 바람직하지 않습니다. `salt`가 일관되게 동일한 방식으로 계산되는지 확인하십시오.

# [L-05] `RewardVault`의 비상시 자금 인출 방법 없음

`RewardVault` 계약에는 일시 중지 기능이 있으며, 이는 계약에서 가능한 취약점이 발견되는 등 어떤 종류의 비상 상황이 있을 때 호출될 것으로 예상됩니다. 일시 중지 후 계약에서 자금을 인출할 수 있는 방법을 추가해야 합니다. 그렇지 않으면 계약에 대한 일시 중지 해제 트랜잭션을 백러닝하여 자금을 훔칠 수 있습니다. 일시 중지 기능을 한 번만 설정하도록 변경하고 `true`로 설정된 경우 자금을 인출할 수 있는 별도의 메서드를 갖는 것을 고려하십시오.

# [L-06] 구현 계약이 초기화될 수 있음

`GaugeProxy` 계약은 프록시를 통해 사용될 것으로 예상되는 구현 계약입니다. 구현 계약은 사용되어서는 안 되므로 초기화를 허용하지 않는 것이 관례입니다. `GaugeProxy`, `TenetVesting` 및 `VestRewardReceiver`에서 `_disableInitializers()`를 호출하는 빈 생성자를 추가하는 것을 고려하십시오.

# [L-07] 2단계 `governance` 역할 전송 패턴 사용

`VestFactory::setGovernance`는 단일 단계 액세스 제어 전송 패턴을 사용합니다. 이는 현재 `governance` 계정이 잘못된 주소로 `setGovernance`를 호출하면 이 `governance` 역할이 그에 의존하는 모든 기능과 함께 영원히 손실된다는 것을 의미합니다. OpenZeppelin의 [Ownable2Step](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) 패턴을 따르고 작업에 대한 2단계 전송 패턴을 구현하십시오.

