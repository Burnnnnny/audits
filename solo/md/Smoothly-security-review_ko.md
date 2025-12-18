# 소개

**pashov**가 **Smoothly** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. 그의 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **Smoothly** 소개

이 프로토콜은 이더리움 검증자들이 블록 제안에서 팁과 MEV를 함께 풀링할 수 있는 "MEV Smoothing Pool"입니다. 제안된 블록의 팁과 MEV 크기는 주로 네트워크 활동에 따라 많이 다르다는 아이디어입니다. 개별 검증자가 팁을 함께 풀링하면 특히 짧은(<10년) 기간 내에 전체적으로 더 많은 보상을 받을 가능성이 높습니다.

작동 방식은 등록된 검증자가 0.65 ETH를 예치하고(악의적으로 행동할 경우 슬래싱을 위해) 제안된 블록의 수수료 수령자 주소를 `SmoothlyPool` 계약으로 설정하는 것입니다. 그런 다음 운영자 그룹이 프로토콜 거버넌스를 제어하고, 머클 루트로 온체인에 투표를 제출하여 보상을 나누거나 검증자를 슬래싱하는 방법을 결정합니다.

## 관찰 사항

이벤트는 프로토콜의 핵심 구성 요소이며, 중요한 작업을 위해 오프체인에서 사용됩니다. 이는 많은 일이 오프체인에서 발생하고 이더리움 네트워크 및 스마트 계약에 의해 제어되지 않으므로 높은 중앙화를 허용합니다.

운영자 및 검증자는 새로운 에포크가 시작될 때만 추가/등록되어야 에포크 보상을 공정하게 받을 수 있습니다.

## 특권 역할 및 행위자

- Smoothly Pool owner - `withdrawals`, `exists` 및 `state` 머클 트리 루트를 설정하고 풀에서 `fee` 금액을 인출할 수 있으며, `PoolGovernance` 계약이 이 역할을 맡습니다.
- Pool Governance owner - 풀 운영자를 관리하고 풀 소유권을 이전할 수 있습니다.
- Pool Governance operator - 새로운 `withdrawals`, `exits` 및 `state` 머클 트리 루트와 모든 운영자 간에 분배될 `fee`를 제안할 수 있습니다.
- Registered validator - 풀 보상을 청구하거나 풀을 나갈 수 있습니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

**영향(Impact)** - 성공적인 공격의 기술적, 경제적, 평판적 손해
**가능성(Likelihood)** - 특정 취약점이 발견되고 악용될 확률
**심각도(Severity)** - 위험의 전반적인 중요도

# 보안 평가 요약

**_검토 커밋 해시_ - [400acaff945f8e41a0ede64e711deb69438d5a2c](https://github.com/Smoothly-Protocol/contracts/tree/400acaff945f8e41a0ede64e711deb69438d5a2c)**

**_수정 검토 커밋 해시_ - [028ba5e7eda683f60efd7867d18d1a32f6f16376](https://github.com/Smoothly-Protocol/contracts/tree/028ba5e7eda683f60efd7867d18d1a32f6f16376)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `PoolGovernance`
- `SmoothlyPool`

---

# 결과 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [C-01] | 운영자가 투표 과반수를 얻기 위해 추가 투표를 할 수 있음 | 치명적 | 수정됨 |
| [C-02] | 운영자가 거버넌스에서 제거된 후에도 보상을 청구할 수 있음 | 치명적 | 수정됨 |
| [H-01] | 큰 중앙화 공격 표면 | 높음 | 인지함 |
| [M-01] | 가스 제한으로 인해 운영자가 보상을 인출할 수 없을 수 있음 | 중간 | 수정됨 |
| [M-02] | 스테이크 수수료가 온체인에서 추적되지 않음 | 중간 | 인지함 |
| [L-01] | `operators.length == 1`일 때 에포크 제안 허용 안 함 | 낮음 | 수정됨 |
| [L-02] | `PoolGovernance`에 작은 ETH 더스트(dust) 금액이 남을 것임 | 낮음 | 인지함 |
| [L-03] | `epoch`는 `withdrawals` 및 `exits` 머클 트리의 리프의 일부여야 함 | 낮음 | 인지함 |
| [L-04] | 메서드가 Checks-Effects-Interactions 패턴을 따르지 않음 | 낮음 | 수정됨 |
| [L-05] | 상태 변경 메서드에 이벤트 방출이 누락됨 | 낮음 | 인지함 |

# 상세 결과

# [C-01] 운영자가 투표 과반수를 얻기 위해 추가 투표를 할 수 있음

## 심각도

**영향:**
높음, `votingRatio` 불변성을 깨기 때문

**가능성:**
높음, 운영자가 언제든지 추가 투표를 할 수 있기 때문

## 설명

`PoolGovernance::proposeEpoch`에서 운영자는 `votingRatio`에 도달하기 위해 필요한 경우 추가 투표를 할 수 있습니다. 이를 악용하면 3명 그룹의 단일 운영자가 결정한 제안을 실행할 수 있습니다. 또한 그룹에 더 많은 운영자가 있고 제안에 대해 한 번의 추가 투표가 필요한 경우, 이미 투표한 사람은 누구나 자신의 투표를 다시 전송하여 제안을 실행할 수 있습니다. 이는 이전 투표를 제거하는 메커니즘이 작동하는 방식 때문입니다:

```solidity
bytes32 prevVote = votes[epochNumber][msg.sender];
uint256 count = ++voteCounter[epochNumber][vote];
uint256 operatorsLen = operators.length;

votes[epochNumber][msg.sender] = vote;

if (prevVote != bytes32(0)) --voteCounter[epochNumber][prevVote];
```

`count`는 사용자가 이미 투표했는지 확인하지 않고 업데이트됩니다. 즉, 사용자가 이미 투표하고 동일한 투표를 한 경우 `count` 값은 1이 아니라 2가 됩니다.

다음과 같은 경우 단일 운영자가 제안을 직접 실행할 수 있습니다:

- `operators.length == 1` - 직접 실행 가능
- `operators.length == 2` - 직접 실행 가능
- `operators.length == 3` - 직접 실행 가능
- `operators.length == 4` - 이제 다른 운영자가 투표한 경우 단일 운영자는 그들의 투표 중 하나에 대해 이중 투표하고 직접 실행할 수 있습니다.
- `operators.length == 5` - `operators.length == 4`와 동일

`operators.length`가 더 크면 단일 운영자가 스스로 제안을 실행할 수 없지만 이중 투표를 사용하는 것은 항상 가능합니다.

PoC(개념 증명)를 실행하려면 이 테스트를 `PooGovernance.t.ts`에 추가하십시오.

```javascript
it("Operators can cast an extra vote to get voting majority", async () => {
  await governance.addOperators([
    operator1.address,
    operator2.address,
    operator3.address,
  ]);
  await time.increase(week);
  await governance
    .connect(operator1)
    .proposeEpoch([withdrawals.root, exits.root, state, fee]);

  expect(await governance.epochNumber()).to.equal(0);
  // operator1 casts a second vote to get 66% vote ratio
  await governance
    .connect(operator1)
    .proposeEpoch([withdrawals.root, exits.root, state, fee]);

  // validate that the epoch increased (vote passed)
  expect(await governance.epochNumber()).to.equal(1);
});
```

## 권장 사항

코드를 다음과 같이 변경하십시오:

```diff
bytes32 prevVote = votes[epochNumber][msg.sender];
- uint256 count = ++voteCounter[epochNumber][vote];
uint256 operatorsLen = operators.length;

votes[epochNumber][msg.sender] = vote;

if (prevVote != bytes32(0)) --voteCounter[epochNumber][prevVote];
+ uint256 count = ++voteCounter[epochNumber][vote];
```

# [C-02] 운영자가 거버넌스에서 제거된 후에도 보상을 청구할 수 있음

## 심각도

**영향:**
높음, 거버넌스에서 제거된 운영자에 대해 보상을 청구할 수 없어야 하기 때문

**가능성:**
높음, 이 기능이 사용되고 운영자가 청구되지 않은 보상을 가지고 있을 때마다 발생할 것이기 때문

## 설명

`deleteOperators` 메서드는 `PoolGovernance`에서 운영자 계정을 제거하지만 여전히 `operatorRewards` 매핑을 건드리지 않고 둡니다. 즉, 운영자가 악의적으로 행동하여 제거되더라도 누적된 보상을 여전히 청구할 수 있습니다. 이 기능은 운영자가 슬래싱 되어야 할 때 사용되므로 이래서는 안 됩니다. 또한 운영자가 비활성 상태가 되면 제거되더라도 현재 구현으로는 청구되지 않은 보상이 계약에 갇히게 됩니다.

## 권장 사항

운영자 제거 시 운영자 보상을 선택한 계정(예: `SmoothlyPool`)으로 전송하십시오.

# [H-01] 큰 중앙화 공격 표면

## 심각도

**영향:**
높음, 여러 승인된 행위자가 자금을 훔치기 위해 악의적으로 행동할 수 있음

**가능성:**
중간, 악의적이거나 타협된 행위자가 필요하지만 프로토콜 `owner`뿐만이 아님

## 설명

1. `PoolGovernance`의 `owner`는 많은 수의 운영자를 추가하여 `proposeEpoch`의 블록 가스 제한 DoS를 초래할 수 있습니다. 메서드가 모든 운영자를 반복하기 때문입니다.
2. 운영자는 투표를 그룹화하고 `SmoothlyPool` 잔액의 전부를 `fee`로 청구할 수 있습니다.
3. 운영자는 `withdrawals` 머클 트리 리프 중 하나에 추가하여 등록되지 않은 계정이 `SmoothlyPool`에서 보상을 청구하도록 허용할 수 있습니다.
4. `PoolGovernance`의 `owner`는 `SmoothlyPool`의 소유권을 자신이 제어하는 계정으로 이전하고 직접 `updateEpoch`를 호출하여 전체 계약 잔액을 `fee`로 인출할 수 있습니다.

## 권장 사항

`PoolGovernance`의 `owner`를 Timelock 계약 뒤의 다중 서명 지갑으로 만들어 사용자가 이 계정으로 실행될 트랜잭션을 모니터링하고 필요한 경우 조치를 취할 수 있도록 하십시오. 또한 최대 운영자 수에 대한 제한(예: 50)을 추가하십시오. 운영자의 경우 현재 쉽게 수정할 수 없으므로 중앙화를 보호하기 위해 추가 보안 메커니즘을 추가해야 할 수도 있습니다.

# [M-01] 가스 제한으로 인해 운영자가 보상을 인출할 수 없을 수 있음

## 심각도

**영향:**
높음, 운영자의 수익이 동결될 것이기 때문

**가능성:**
낮음, 운영자가 특별한 다중 서명 지갑 또는 계약이어야 하기 때문

## 설명

`PoolGovernance:withdrawRewards` 메서드는 운영자가 수익을 인출할 수 있도록 허용하며, 이는 다음 외부 호출로 발생합니다:

```solidity
(bool sent, ) = msg.sender.call{value: rewards, gas: 2300}("");
```

2300 가스 제한은 2300 가스 단위 이상을 사용하는 `receive` 또는 `fallback` 함수가 있는 스마트 계약 지갑에는 충분하지 않을 수 있으며, 이는 너무 낮습니다(이벤트 방출 외에는 할 수 있는 것이 거의 없음). 그럴 경우 운영자는 보상을 청구할 수 없으며 영원히 계약에 갇히게 됩니다.

## 권장 사항

외부 호출에서 가스 제한을 제거하십시오. `SmoothlyPool`의 동일한 로직에서도 제거할 수 있습니다.

# [M-02] 스테이크 수수료가 온체인에서 추적되지 않음

## 심각도

**영향:**
높음, `SmoothlyPool`이 보유한 ETH의 잘못된 회계로 이어질 수 있기 때문

**가능성:**
낮음, 오프체인 코드가 잘못되어야 하기 때문

## 설명

`SmoothlyPool`에 가입하는 모든 검증자는 `STAKE_FEE`(0.065 ETH 크기)를 계약에 지불하여 등록해야 합니다. 풀은 검증자가 얼마나 많은 스테이크 수수료 잔액을 가지고 있는지 추적하지 않으며, 이는 다음과 같은 이유로 문제가 됩니다:

1. 풀은 잔액에 적어도 `numValidators * STAKE_FEE` ETH를 보유하고 있다는 보장이 없습니다. ETH가 실수로 보상으로 분배되거나 운영자가 수수료로 청구했을 수 있습니다.
2. 검증자가 `SmoothlyPool::addStake`를 여러 번 호출하면 `STAKE_FEE`보다 더 많이 예치할 수 있습니다.
3. 슬래싱/처벌 메커니즘을 온체인에서 강제할 수 없습니다.

## 권장 사항

`SmoothlyPool`에서 검증자의 스테이크 수수료 잔액을 추적하는 매핑을 추가하십시오.

# [L-01] `operators.length == 1`일 때 에포크 제안 허용 안 함

`operators.length == 1`일 때 운영자는 모든 에포크 제안을 실행할 수 있으며, 이는 허용되어서는 안 됩니다. 이 시나리오를 방지하기 위해 `proposeEpoch`에서 `operators.length`가 최소 2인지 확인하십시오.

# [L-02] `PoolGovernance`에 작은 ETH 더스트(dust) 금액이 남을 것임

`PoolGovernance::proposeEpoch`에서 각 운영자가 `epoch.fee`에서 얻는 금액을 계산하는 수학은 `uint256 operatorShare = epoch.fee / operatorsLen;`입니다. Solidity와 EVM이 나눗셈을 실행하는 방식 때문에 나눗셈 후 일부 나머지가 있을 가능성이 매우 높으며, 이는 모든 운영자가 보상을 인출한 후 `PoolGovernance` 계약에 남을 ETH 금액(운영자 수를 곱한 값)입니다. 남은 더스트를 분배하는 메커니즘을 고려하십시오.

# [L-03] `epoch`는 `withdrawals` 및 `exits` 머클 트리의 리프의 일부여야 함

루트 재사용의 경우, 이전 에포크의 리프가 재사용될 수 없도록 `epoch` 번호를 머클 트리의 리프의 일부로 포함하는 것이 안전합니다. `SmoothlyPool`의 `withdrawRewards` 및 `withdrawStake` 모두에 이것을 추가하십시오.

# [L-04] 메서드가 Checks-Effects-Interactions 패턴을 따르지 않음

`PoolGovernance::proposeEpoch` 및 `SmoothlyPool::updateEpoch` 메서드는 CEI 패턴을 따르지 않습니다. 해당 메서드에서 외부 호출을 마지막에 수행하도록 하십시오.

# [L-05] 상태 변경 메서드에 이벤트 방출이 누락됨

`SmoothlyPool` 계약은 이벤트에 크게 의존하지만 `PoolGovernance`는 상태 변경 메서드에서 이벤트를 방출하지 않습니다. 또한 `SmoothlyPool::receive`는 이벤트를 방출하지 않습니다. 상태를 변경하는 코드베이스의 모든 메서드가 오프체인 모니터링을 위해 적절한 이벤트를 방출하는지 확인하십시오.

