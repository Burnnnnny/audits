# 소개

**Lizard Staking** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Lizard Staking** 소개

**Lizard Staking** 프로토콜은 `Ethlizards` 및 `Genesis Ethlizards` NFT 보유자가 `USDC` 형태의 보상을 받는 대가로 NFT를 스테이킹할 수 있도록 합니다. NFT를 스테이킹하면 `Locked Lizards` NFT가 발행되고 `(Genesis) Ethlizards` NFT는 90일 동안 계약에 잠겨 인출할 수 없지만, 스테이킹하는 동안 누적된 보상은 언제든지 청구할 수 있습니다. 이 프로토콜은 지분 기반 접근 방식을 사용하며, 보상은 "풀"에 있고 각 스테이커는 이 풀의 지분을 소유합니다. 또한 스테이킹 된 시간을 기반으로 하는 지분 리베이스(인플레이션) 메커니즘과 이러한 리베이스에 대한 재설정(reset) 메커니즘도 있습니다.

## 예상치 못한/흥미로운 설계 선택

`Locked Lizards` NFT에 대한 승인(Approvals)은 승인 가능한 계약에 대한 화이트리스트를 구현하는 `onlyApprovedContracts` 수정자(modifier)에 의해 제한됩니다.

스테이킹 된 Lizard NFT를 인출해도 `Locked Lizards`는 소각되지 않습니다. 스테이킹 계약에 남아 있으며 다시 스테이킹하면 다시 전송됩니다.

`retractLockedLizard` 기능이 있어 스테이커가 `Locked Lizards` NFT가 다른 주소로 전송된 경우 강제로 자신의 주소로 다시 전송할 수 있습니다.

사용자가 `withdrawStake`를 호출하기 전에 `claimReward`를 호출하지 않으면 누적된 보상은 스테이킹 계약에 영원히 잠기게 되며, 사용자나 프로토콜 소유자가 이를 청구하거나 복구할 방법이 없습니다.

[추가 문서](https://ethlizards.gitbook.io/ethlizards/)

[Ethlizards 컬렉션](https://opensea.io/collection/ethlizards) & [Genesis Ethlizards 컬렉션](https://opensea.io/collection/genesis-ethlizards-erc721)

# 위협 모델

## 역할 및 행위자

- 소유자(Owner) - 승인 허용된 계약, 재설정 지분 값, 최소 재설정 값, 보상 입금을 위한 위원회 주소를 제어할 수 있으며 입금을 켜고 끄고 `startTimestamp` 및 `lastGlobalUpdate`를 재설정할 수 있습니다.
- 스테이커(Staker) - `USDC` 보상을 받을 목적으로 `Ethlizard` 또는 `Genesis Ethlizard` NFT를 스테이킹하는 사용자입니다.
- 원래 `Locked Lizards` 소유자 - `Locked Lizards` NFT를 자신에게 강제로 다시 전송할 수 있습니다.
- 허용된 계약(Allowed contracts) - `Locked Lizards` NFT의 운영자로 설정할 수 있는 계약의 주소(EOA일 수도 있음)입니다.
- 위원회(Council) - 보상을 입금하고 새 풀 생성을 트리거 할 수 있습니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

**A:** 실제 스테이킹 된 Lizard NFT와 보상을 분배하는 데 사용되는 `USDC`입니다.

**Q:** 프로토콜에 일어날 수 있는 최악의 일은 무엇인가요?

**A:** 공격자가 계약의 `USDC` 잔액이나 스테이킹 된 NFT를 (부분적으로 또는 완전히) 훔치거나 계약을 DoS 상태로 만드는 것입니다.

**Q:** 프로토콜/사용자가 돈을 잃을 수 있는 경우는 언제인가요?

**A:** 스테이킹 된 NFT를 `withdraw`할 수 없거나 누적된 보상을 `claim`할 수 없는 경우입니다.

## 잠재적 공격자의 목표

- 각 `claimRewards` 또는 `withdrawStake` 트랜잭션을 되돌려(revert) 시스템을 DoS 상태로 만들고 스테이커에 대한 그리핑(griefing) 공격을 실행합니다.
- 풀의 `USDC` 보상을 훔칩니다.
- 풀에 스테이킹 된 NFT를 훔칩니다.
- 보상 계산 버그를 악용하여 예상보다 더 많은 보상을 받습니다.

## 공격자가 목표를 달성할 수 있는 잠재적 방법

- NFT를 스테이킹한 다음 특정 시간에 특정 `_poolNumber` 인수로 `claimRewards`를 호출합니다.
- 한 번 청구할 수 있어야 할 때 보상을 여러 번 청구합니다.
- 다른 스테이커의 NFT를 자신에게 전송하는 방식으로 `withdrawStake` 함수를 호출합니다.
- 스테이킹하지 않았는데 스테이킹한 것처럼 `depositStake` 메서드를 속입니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

**영향 (Impact)** - 성공적인 공격으로 인한 기술적, 경제적, 평판적 손해

**가능성 (Likelihood)** - 특정 취약점이 발견되고 악용될 확률

**심각도 (Severity)** - 위험의 전반적인 중요성

# 보안 평가 요약

**_검토 커밋 해시_ - [448b8d934a0591e10aea871d4a405e3fa7aa28c4](https://github.com/kmaox/ethlizardstaking/commit/448b8d934a0591e10aea871d4a405e3fa7aa28c4)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `LizardLounge`
- `interfaces/**`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 4개 문제
- 중간: 4개 문제
- 낮음: 6개 문제
- 정보성: 15개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | `claimReward`가 `USDC`를 절대 보내지 않으므로 사용자가 보상을 청구하는 것이 불가능함 | 치명적 |
| [C-02] | `owedAmount` 계산이 0으로 내림됨 | 치명적 |
| [H-01] | `claimReward`를 먼저 호출하기 전에 `withdrawStake`를 호출하면 사용자는 누적된 보상을 영원히 잃음 | 높음 |
| [H-02] | `claimCalculation`의 잘못된 확인으로 인해 사용자가 받는 보상이 줄어듦 | 높음 |
| [M-01] | 경계가 없는 배열에 대한 루핑은 DoS 상태를 초래할 수 있음 | 중간 |
| [M-02] | 백분율 값의 설정자(setter) 메서드에 제약 조건이 누락됨 | 중간 |
| [M-03] | 소유자는 사용자의 일일 보상 이자를 0으로 만들 수 있는 권한이 있음 | 중간 |
| [M-04] | 승인을 제한하는 것은 NFT 판매를 부분적으로만 제한함 | 중간 |
| [L-01] | 문서의 기능이 누락됨 | 낮음 |
| [L-02] | 구현이 ERC721의 `burn` 메서드를 사용하고 있지 않음 | 낮음 |
| [L-03] | 첫 번째 풀이 생성된 것과 같은 블록에서 사용자가 스테이킹할 경우 예상치 못한 동작 발생 | 낮음 |
| [L-04] | `calculateShareFromTime` 메서드에서 곱셈 전 나눗셈 | 낮음 |
| [L-05] | 2단계 소유권 이전 접근 방식 사용 | 낮음 |
| [L-06] | 코드 명명은 강제되지 않는 가정을 제공함 | 낮음 |
| [I-01] | 계약이 허용되지 않으므로 `safeTransfer`를 사용할 필요가 없음 | 정보성 |
| [I-02] | 지나치게 복잡한 수학 계산 | 정보성 |
| [I-03] | 메서드 및 저장소 변수를 제거할 수 있음 | 정보성 |
| [I-04] | 코드베이스의 열린 `TODO` | 정보성 |
| [I-05] | 파일 이름과 인터페이스 이름 불일치 | 정보성 |
| [I-06] | 계약이 사용자 지정 오류와 `require` 문을 모두 사용하고 있음 | 정보성 |
| [I-07] | NatSpec 문서 및 주석의 오타, 문법 오류, 중복 및 복잡성 | 정보성 |
| [I-08] | `LockedLizardMinted` 이벤트 방출은 mint 메서드에서 발생해야 함 | 정보성 |
| [I-09] | 변수의 기본값을 할당하는 대신 `delete` 키워드 사용 | 정보성 |
| [I-10] | 변수를 `immutable` 또는 `constant`로 변경할 수 있음 | 정보성 |
| [I-11] | 대부분의 상태 변경 메서드는 이벤트를 방출하지 않음 | 정보성 |
| [I-12] | 인터페이스가 필요하지 않음 | 정보성 |
| [I-13] | `isLizardWithdrawable`이 확인 결과를 직접 반환하도록 함 | 정보성 |
| [I-14] | `onlyApprovedContracts`의 명명 문제 | 정보성 |
| [I-15] | `IERC721` 인터페이스가 필요하지 않음 | 정보성 |

# 상세 발견 사항

# [C-01] `claimReward`가 `USDC`를 절대 보내지 않으므로 사용자가 보상을 청구하는 것이 불가능함

## 심각도

**영향:**
높음, 사용자가 계약에서 보상을 받을 수 없기 때문

**가능성:**
높음, 코드가 ERC20 API를 잘못 사용하고 있기 때문

## 설명

`claimReward` 메서드는 스테이커가 잠긴 NFT에 대한 `USDC` 보상을 받기 위해 사용해야 합니다. 보상 전송이 다음 코드로 구현되어 있기 때문에 이것은 절대 작동하지 않을 것입니다:

```solidity
USDc.transferFrom(msg.sender, address(this), claimableRewards);
```

이것은 스테이커에게서 보상을 받아 스테이킹 계약으로 전송하는 것과 같으므로 잘못되었습니다. 그 반대가 되어야 합니다.

## 권장 사항

코드를 다음과 같이 변경하십시오:

```diff
- USDc.transferFrom(msg.sender, address(this), claimableRewards);
+ USDc.transfer(msg.sender, claimableRewards);
```

항상 ERC20 API를 올바르게 사용하고 감사를 받기 전에 단위 테스트로 코드베이스를 완전히 커버하십시오.

# [C-02] `owedAmount` 계산이 0으로 내림됨

## 심각도

**영향:**
높음, 사용자가 보상을 청구할 수 있어야 할 때 청구 가능한 보상이 0이 되기 때문

**가능성:**
높음, 사용자의 지분이 풀의 캐시 된 글로벌 지분보다 작을 때마다 발생하며 이는 거의 항상 발생하기 때문

## 설명

`claimCalculation` 메서드는 다음 계산을 사용하여 사용자에게 `USDC` 보상 형태로 전송될 `owedAmount`를 계산합니다:

```solidity
owedAmount = (currentShareRaw / pool[_poolNumber].currentGlobalShare) * pool[_poolNumber].value;
```

이것은 `_poolNumber == 1`인 경우와 다른 값인 경우 모두 발생하지만 코드는 두 경우 모두 존재합니다. 문제는 곱셈보다 나눗셈을 먼저 수행한다는 것입니다. `pool[_poolNumber].currentGlobalShare` 값이 `currentShareRaw` 값보다 크면 나눗셈이 0으로 내림 되어 `owedAmount`가 0이 됩니다. 풀의 캐시 된 `currentGlobalShare`가 단일 사용자의 원시 지분보다 클 것으로 예상되므로 거의 항상 발생합니다. 즉, 사용자가 아무리 기다려도 이 풀에 대한 보상을 청구할 수 없으며 계약에 영원히 갇히게 됩니다.

이 문제는 개발자가 감사 중간에 부분적으로 인지하여 `owedAmount`가 계산되는 곳 중 하나를 수정했지만 다른 `owedAmount` 계산 오류는 발견되지 않았습니다.

## 권장 사항

코드를 다음과 같이 변경하십시오:

```diff
- owedAmount = (currentShareRaw / pool[_poolNumber].currentGlobalShare) * pool[_poolNumber].value;
+ owedAmount = currentShareRaw * pool[_poolNumber].value / pool[_poolNumber].currentGlobalShare;
```

이렇게 하면 나눗셈보다 곱셈을 먼저 수행하여 0으로 내림 되지 않습니다. 풀의 값은 6 소수점인 `USDC`에 있지만 지분은 18 소수점이기 때문입니다.

# [H-01] `claimReward`를 먼저 호출하기 전에 `withdrawStake`를 호출하면 사용자는 누적된 보상을 영원히 잃음

## 심각도

**영향:**
높음, 사용자에게 금전적 손실을 초래하기 때문

**가능성:**
중간, 프론트엔드가 올바른 호출 순서를 강제하지만 Gitbook 문서는 재스테이킹이 사용자의 보상 접근 권한을 다시 얻게 해준다고 잘못 주장하기 때문

## 설명

계약은 사용자가 각 보상 풀에 대해 `claimReward`를 먼저 호출하지 않고 `withdrawStake`를 호출하면 스테이커가 미청구 보상을 모두 영원히 잃게 되며 스테이킹 계약에 잠기도록 구현되어 있습니다. 프론트엔드는 올바른 호출 순서를 강제하지만 Gitbook 문서는 `언스테이킹 시 사용자는 보류 중인 모든 보상에 대한 액세스 권한을 잃고 향후 보상에 대한 액세스 권한을 잃습니다(재스테이킹하지 않는 한)`라고 명시하고 있어 재스테이킹하면 미청구 보상에 대한 액세스 권한을 다시 얻을 수 있다는 인상을 주지만 `withdrawStake` 메서드가 이전 보상 계산에 필요한 데이터를 제거하므로 사실이 아닙니다.

문서가 이 메커니즘의 작동 방식에 대해 오해의 소지가 있는 정보를 제공하고 사용자가 자신에게 나쁜 방식으로 스마트 계약과 직접 상호 작용할 수 있기 때문에(악의적이지 않은 경우) 사용자에게 금전적 가치 손실을 초래할 가능성이 더 높습니다.

## 권장 사항

가능한 해결책 중 하나는 미청구 보상이 있는 경우 되돌리기(reverting)를 통해 `withdrawStake` 호출 시 미청구 보상이 0이 되도록 강제하는 것입니다. 다른 하나는 `withdrawStake`에서 `claimReward`를 호출하는 것입니다.

# [H-02] `claimCalculation`의 잘못된 확인으로 인해 사용자가 받는 보상이 줄어듦

## 심각도

**영향:**
높음, 사용자에게 금전적 손실을 초래하기 때문

**가능성:**
중간, ID가 1인 풀에 대해서만 발생하기 때문

## 설명

`claimCalculation`의 첫 번째 `if` 문은 `if (_poolNumber == 1)`를 확인하고 해당 특정 풀에 대한 인플레이션을 고려하지 않습니다. 문제(`if` 문 위의 주석에서도 설명됨)는 풀이 1개만 있는 경우(또는 첫 번째 풀인 경우) 인플레이션 계산(더 높은 보상을 초래함)을 수행할 필요가 없다는 것을 확인하려는 의도였습니다. 그러나 `_poolNumber == 1`인 경우 배열은 인덱스 0부터 시작하므로 실제로는 최소 2개의 풀이 있음을 의미하며, 1은 실제로 `pool` 배열의 두 번째 풀입니다. 이로 인해 ID가 1인 풀에 스테이킹한 모든 보상 청구자는 인플레이션 보상을 놓치게 됩니다.

## 권장 사항

코드를 다음과 같이 변경하십시오:

```diff
- if (_poolNumber == 1) {
+ if (_poolNumber == 0) {
```

# [M-01] 경계가 없는 배열에 대한 루핑은 DoS 상태를 초래할 수 있음

## 심각도

**영향:**
높음, 계약이 DoS 상태가 되어 아무도 NFT를 인출하거나 보상을 청구할 수 없기 때문

**가능성:**
낮음, 많은 풀이 추가되거나 악의적인 소유자가 필요하기 때문

## 설명

`claimCalculation` 및 `getCurrentShareRaw` 메서드 모두 적절한 계산을 수행하기 위해 `pool` 배열을 반복(loop)합니다. 문제는 배열에서 요소를 꺼낼 방법이 없지만 배열의 길이에 대한 상한선이 없다는 것입니다. `currentRewards`가 `minResetValue` 이상일 때마다 `createPool` 메서드가 호출되어 `pool` 배열에 새 요소를 추가합니다. 어느 시점에서 풀이 많아지면 이를 반복하는 데 많은 비용이 들고 블록 가스 제한을 초과하는 가스 비용이 발생할 수 있습니다. 이는 트랜잭션을 더 이상 실행할 수 없음을 의미하며 계약의 주요 기능(스테이킹 된 NFT 인출 및 보상 청구)이 DoS 상태가 됩니다.

## 권장 사항

생성할 수 있는 풀의 수를 제한하십시오. 예를 들어 최대 25개 풀을 생성할 수 있습니다.

# [M-02] 백분율 값의 설정자(setter) 메서드에 제약 조건이 누락됨

## 심각도

**영향:**
높음, 잘못된 보상 계산을 초래하기 때문

**가능성:**
낮음, 악의적/침해된 소유자 또는 그 측의 큰 오류가 필요하기 때문

## 설명

`setResetShareValue`에는 `_newShareResetValue` 인수가 100%를 초과하지 않는지 확인하는 기능이 없습니다. 값이 백분율일 것으로 예상되므로 100보다 큰 값을 설정하면 계약의 중요한 계산(보상 청구 계산 중 하나)이 엉망이 됩니다. 더 큰 `resetShareValue`는 사용자에게 더 작은 보상과 같으므로 사용자가 얻은 것보다 더 작은 보상을 받게 할 수 있습니다.

## 권장 사항

`setResetShareValue`에 `_newShareResetValue` 인수가 100%를 초과하지 않는지 확인하는 기능을 추가하십시오.

# [M-03] 소유자는 사용자의 일일 보상 이자를 0으로 만들 수 있는 권한이 있음

## 심각도

**영향:**
높음, 사용자가 누적된 보상을 청구할 권리를 잃을 수 있기 때문

**가능성:**
낮음, 악의적/침해된 소유자가 필요하기 때문

## 설명

`setDepositsActive` 메서드는 `startTimestamp` 및 `lastGlobalUpdate`를 재설정합니다. 소유자는 각 `claimReward` 트랜잭션을 프런트런(front-run)하고 `startTimestamp`를 재설정하여 `calculateShareFromTime`에서 `requiredRebases`가 0이 되도록 하여 사용자가 일일 이자를 잃게 할 수 있습니다. 반면 `lastGlobalUpdate`를 재설정하면 `updateGlobalShares`가 리베이스를 수행하지 않아 `overallShare`를 팽창시키지 않으며 이 또한 가능해서는 안 됩니다.

## 권장 사항

`setDepositsActive` 메서드를 계약 배포 후 한 번만 호출할 수 있도록 만드십시오.

# [M-04] 승인을 제한하는 것은 NFT 판매를 부분적으로만 제한함

## 심각도

**영향:**
높음, 다른 게임/프로토콜과 통합할 때 사기 및 버그로 이어질 수 있기 때문

**가능성:**
낮음, 현재 그러한 판매나 통합이 일어날 것으로 예상되지 않고 이에 대한 정보가 문서에 있기 때문

## 설명

승인에 대한 제약(`onlyApprovedContracts` 수정자)이 추가되어 `Locked Lizards` NFT를 OpenSea, Blur 등과 같은 마켓플레이스에서 판매할 수 없습니다. 사용자가 항상 OTC 거래를 할 수 있기 때문에 이는 NFT 판매를 부분적으로만 제한합니다. 원래 NFT 소유자가 언제든지 `retractLockedLizard`를 호출하고 NFT 소유권을 다시 얻을 수 있기 때문에 이러한 거래는 사기가 될 것입니다. 판매뿐만 아니라 NFT 게임과의 통합과 같은 경우에도 문제가 될 것입니다. 게임은 철회될 수 있는 NFT와 제대로 작동하지 않을 것으로 예상되며 이는 여러 공격 벡터를 열어줍니다.

## 권장 사항

`onlyApprovedContracts` 수정자를 제거하고 `retractLockedLizard` 기능을 제거하여 판매 및 통합을 허용하거나, 문제가 발생할 수 있으므로 `approve` 및 `transfer` 기능을 모두 금지하십시오.

# [L-01] 문서의 기능이 누락됨

Gitbook에는 다음과 같이 나와 있습니다. `보상을 청구하려면 사용자는 적극적인 참여를 보장하기 위해 Ethlizards와 함께 하나 이상의 온체인 거버넌스 투표/조치에 참여해야 합니다.` 이 기능은 계약에 없으며 사용자/프로토콜 개발자의 잘못된 가정으로 이어질 수 있습니다. 문서에서 제거하거나 구현하여 코드를 업데이트하십시오.

# [L-02] 구현이 ERC721의 `burn` 메서드를 사용하고 있지 않음

현재 구현은 사용자가 지분을 인출한 후 LLZ NFT를 저장하고 후속 입금 시 다시 전송합니다. 인출 시 LLZ NFT를 소각하고 후속 입금 시 다시 발행하는 것이 현재 사용되는 방식보다 오류 발생 가능성이 적고 ERC721 NFT와 관련된 일반적인 모범 사례 패턴을 따르므로 더 좋습니다.

# [L-03] 첫 번째 풀이 생성된 것과 같은 블록에서 사용자가 스테이킹할 경우 예상치 못한 동작 발생

보상 풀이 1개뿐이고 사용자가 풀이 생성된 것과 정확히 같은 블록에서 스테이킹한 경우,

```solidity
(pool[pool.length - 1].time) <= timeLizardLocked[_tokenId]
```

그리고

```solidity
timeLizardLocked[_tokenId] <= pool[0].time
```

둘 다 `true`를 반환합니다. 문제는 첫 번째 확인은 `getCurrentShareRaw`의 `if`에 있고 두 번째 확인은 `else if`에 있으며 코드가 어떤 분기를 취하는지에 따라 지분 금액에 대해 다른 계산이 발생한다는 것입니다. 둘 중 하나에서 `=` 기호를 제거하면 문제가 해결되는데, 같은 블록에서 스테이킹한 사용자가 풀의 지분 인플레이션을 얻는 것이 공정하므로 `if` 문에서 제거하는 것이 더 정확하다고 생각합니다.

이와 매우 유사한 또 다른 문제가 있습니다. `getCurrentShareRaw`에서 사용자가 첫 번째 풀이 생성된 것과 같은 `block.timestamp`에 스테이킹하면 그의 지분은 마치 그 첫 번째 풀에 포함된 것처럼 계산됩니다. `claimCalculation` 함수의 경우는 그렇지 않습니다. 사용자가 첫 번째 풀이 생성된 것과 같은 블록(동일한 `block.timestamp`)에 스테이킹한 경우 그의 보상에는 첫 번째 블록의 보상이 포함되지 않습니다. 첫 번째 풀이 생성된 것과 같은 블록에서 스테이킹할 때 풀의 지분 인플레이션은 받지만 풀의 보상은 받지 못하는 것과 같이, 프로토콜이 이 동작을 의도했다고 문서화하지 않았으므로 이는 예상치 못한 일입니다.

제 권장 사항은 풀이 생성된 것과 같은 블록에서 스테이킹한 경우 사용자가 지분에 대한 인플레이션과 보상을 모두 받아야 한다는 것입니다. 이에 대해 깊이 생각해보십시오.

# [L-04] `calculateShareFromTime` 메서드에서 곱셈 전 나눗셈

`requiredRebases` 변수는 `1 days`로 나누어 계산됩니다. 예상치 못하게 0으로 내림 될 수 있지만 결과가 `calculateRebasePercentage` 메서드로 전달되어 "power of" 값으로 사용되므로 문제가 발생하지 않으며, `calculateRebasePercentage`는 인수로 0을 받아도 1을 반환합니다. 이것은 문서화가 잘 되어 있어야 하며 개발자와 감사자가 이해해야 합니다. 코드에 적절한 주석을 추가하십시오.

# [L-05] 2단계 소유권 이전 접근 방식 사용

`onlyOwner` 수정자가 있는 4개의 메서드가 있으며 이는 `owner` 역할이 중요하다는 것을 보여줍니다. 의도치 않게 제어할 수 없는 주소로 `owner` 역할을 보내지 않도록 보안을 제공하는 OpenZeppelin의 `Ownable2Step`을 `Ownable` 대신 사용하여 2단계 소유권 이전 접근 방식을 사용하십시오.

# [L-06] 코드 명명은 강제되지 않는 가정을 제공함

`allowedContracts` 매핑에는 계약뿐만 아니라 EOA도 포함될 수 있습니다. 다른 개발자는 계약만 `onlyApprovedContracts` 수정자가 있는 메서드를 호출할 수 있다고 기대할 수 있지만 실제로는 그렇지 않습니다. `setAllowedContracts`에서 설정할 때 주소의 코드 크기가 > 0인지 확인하는 기능을 추가하여 모든 허용된 주소가 계약 주소인지 확인하여 이 문제를 해결하십시오.

# [I-01] 계약이 허용되지 않으므로 `safeTransfer`를 사용할 필요가 없음

`depositStake` 메서드는 계약 호출을 허용하지 않으므로(코드가 주석 처리되어 있어도 주석이 해제될 것이라고 함) ERC721 `_safeTransfer` 기능을 사용할 필요가 없습니다. 항상 초기 입금자에게 수행되기 때문입니다. 대신 일반 `_transfer` 기능을 사용하십시오.

# [I-02] 지나치게 복잡한 수학 계산

`calculateShareFromTime`의 다음 코드는 지나치게 복잡하며 다음과 같이 단순화할 수 있습니다:

```diff
- uint256 requiredRebases = ((_currentTime - startTimestamp) - (_previousTime - startTimestamp)) / 1 days;
+ uint256 requiredRebases = (_currentTime - _previousTime) / 1 days;
```

# [I-03] 메서드 및 저장소 변수를 제거할 수 있음

단순성을 위해 `stakePoolClaims`를 `rewardsClaimed`로 이름을 바꾸고 `isRewardsClaimed`를 제거한 다음 자동으로 생성된 getter를 사용하십시오. 이렇게 하면 가스 최적화도 됩니다. 또한 `resetCounter` 및 `rebaseCounter` 저장소 변수는 온체인에서 읽히지 않으므로 `reset` 또는 `rebase` 시 이벤트를 방출하고 오프체인에서 계산을 수행할 수 있습니다.

# [I-04] 코드베이스의 열린 `TODO`

현재 코드에는 4개의 열린 `TODO`가 있으며 그 중 하나는 감사 후 코드/기능을 추가하려는 의도를 보여줍니다 - `// TODO: ADD A MERKLE SIGNATURE HERE ONCE FRONTEND IS FINALISED`. 모두 제거하거나 해결하십시오.

# [I-05] 파일 이름과 인터페이스 이름 불일치

`IEthLizards`와 `IGenesisEthLizards` 모두 파일 이름과 인터페이스 이름 사이에 불일치가 있습니다. 파일 이름은 대문자 `L`로 `Lizards`를 쓰지만 인터페이스는 소문자를 사용합니다. `IUSDC` 파일에 포함된 `IUSDc` 인터페이스에도 동일한 문제가 존재합니다. 미묘한 오류로 이어질 수 있으므로 명명에 일관성을 유지하십시오.

# [I-06] 계약이 사용자 지정 오류와 `require` 문을 모두 사용하고 있음

더 가스 효율적이고 프로토콜의 상호 운용성에 도움이 되므로 계약이 모든 곳에서 일관되게 사용자 지정 오류를 사용하는지 확인하십시오.

# [I-07] NatSpec 문서 및 주석의 오타, 문법 오류, 중복 및 복잡성

`LizardLounge` 계약의 NatSpec 문서와 주석에 여러 문제가 있습니다:

- `IUSDC` 파일에 `// Unit testing for the LizardLounge Contract` 주석이 있어 인터페이스가 테스트에만 사용된다는 것을 의미하지만 `LizardLounge` 계약에서도 사용됩니다.
- `isLizardWithdrawable`의 NatSpec은 도마뱀이 양도 가능한지(transferrable) 확인한다고 하지만 실제로는 인출 가능한지(withdrawable) 확인합니다.
- `Checks if the rewards of a lizard for a specific pool has been claimed` -> `Checks if the rewards of a lizard for a specific pool have been claimed`
- `calculateRebasePercentage`의 이상한 주석 - `// Do not times`, 제거하거나 업데이트해야 합니다.
- `calculateRebasePercentage`의 NatSpec 불완전한 문장 - `@notice We calculate the 1.005^_requiredRebases and`, 문맥 완성 필요
- `calculateRebasePercentage`의 NatSpec 및 주석은 일부 기술 문서를 언급하지만 링크가 없습니다 - 추가하십시오.
- `depositStake` NatSpec의 불완전한 문장 - 올바르게 업데이트하십시오.
- `depositStake`의 NatSpec은 `Allows user to deposit their regular Ethlizards for staking`라고 말하지만 `Genesis Ethlizards` 스테이킹도 허용하므로 업데이트하십시오.
- 오타: `firsts` -> `first`, `depositer` -> `depositor`, `CallerNotDepositer` -> `CallerNotDepositor`, `everytime` -> `every time`
- `updateGlobalShares`의 NatSpec은 메서드가 `Gets the current global share counter`라고 말하지만 올바르지 않으므로 올바르게 업데이트하십시오.
- 문법 오류:
  - `TokenId where share is being calculated` -> `TokenId for which share is being calculated`
  - `Transfer the user the USDC rewards` -> `Transfer the USDC rewards to the user`
  - `If the no pools have been created` -> `If no pools have been created`
  - `after the user is staked` -> `after the user has staked`
  - `// First time stakers mints their...` -> `// First time stakers mint their ...`
  - `A lizard is transferrable if it been over 90 days since it was deposited` -> `A lizard is transferrable if more than 90 days have passed since it was deposited`
  - `... user are protected ...` -> `... users are protected ...`

# [I-08] `LockedLizardMinted` 이벤트 방출은 mint 메서드에서 발생해야 함

`mintLLZ` 메서드가 호출될 때만 방출되므로 `LockedLizardMinted` 이벤트 방출을 `mintLLZ` 메서드로 이동하십시오.

# [I-09] 변수의 기본값을 할당하는 대신 `delete` 키워드 사용

`withdrawStake`에서 스테이킹 된 NFT에 대한 `timeLizardLocked` 및 `originalLockedLizardOwners`는 0 또는 `address(0)`을 할당하여 재설정됩니다. 대신 `delete` 키워드를 사용하는 것이 모범 사례이며, 유형의 기본값을 수동으로 할당할 필요가 없습니다.

# [I-10] 변수를 `immutable` 또는 `constant`로 변경할 수 있음

`nominator` 변수의 값은 컴파일 타임에 알려져 있으므로 `private constant`로 만들 수 있으며 계약 외부에서 호출될 것으로 예상되지 않습니다. `Ethlizards`, `GenesisLiz` 및 `USDc` 변수는 생성자에서만 설정되고 그 후에는 변경되지 않으므로 `immutable`로 만들 수 있습니다.

# [I-11] 대부분의 상태 변경 메서드는 이벤트를 방출하지 않음

이에 대한 예는 `setDepositsActive` 또는 `setCouncilAddress`입니다. 상태 변경 메서드는 오프체인 모니터링을 구현할 수 있도록 이벤트를 방출해야 합니다. 모범 사례를 따르기 위해 각 상태 변경 메서드에서 적절한 이벤트를 방출하십시오.

# [I-12] 인터페이스가 필요하지 않음

`IUSDc` 인터페이스는 코드베이스에 필요하지 않습니다. OpenZeppelin `IERC20` 인터페이스를 가져와서 대신 사용하십시오. 또한 `ABDKMath64x64`를 코드베이스에서 제거하고 외부 종속성으로 가져오십시오. 거기에 유지할 필요가 없습니다.

# [I-13] `isLizardWithdrawable`이 확인 결과를 직접 반환하도록 함

메서드에 if-else 문을 가질 필요가 없습니다. 대신 `isLizardWithdrawable`에서 다음과 같이 직접 수행하십시오:

```solidity
return block.timestamp - timeLizardLocked[_tokenId] >= 90 days;
```

# [I-14] `onlyApprovedContracts`의 명명 문제

`onlyApprovedContracts` 수정자는 같은 것을 위해 `approved`, `allowed`, `whitelisted`라는 세 가지 다른 단어를 사용합니다. 일관성을 유지하고 프로토콜의 맥락에서 한 가지 의미에 대해 한 단어만 사용하십시오(예: `whitelisted`).

# [I-15] `IERC721` 인터페이스가 필요하지 않음

`IEthlizards` 인터페이스는 `IERC721` 인터페이스를 가져오고 상속합니다. 메서드 중 어느 것도 사용되지 않으므로 필요하지 않습니다. `IERC721` 인터페이스와 해당 import를 제거하십시오.
