# 소개

**Ethena** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Ethena** 소개

Ethena 프로토콜은 이더리움에 배포되며 수익 창출 속성을 가진 합성 달러인 `USDe`를 구축하고 있습니다. 스테이블코인은 은행 시스템 내의 담보 없이 100% 담보화되며 USDC, stETH 및 기타 LSD를 담보로 사용합니다. 수익은 `stETH`와 차익 거래에서 발생할 것으로 예상됩니다. `USDe` 스마트 계약의 민팅 및 상환은 Ethena 팀에 의해 신뢰할 수 있는 방식으로 처리됩니다.

[더 많은 문서](https://ethena-labs.gitbook.io/ethena-labs/Fy1XpH0vy9LnSDCMdikd/)

## 관찰 사항

프로토콜은 높은 수준의 중앙 집중화를 가지고 있습니다:

- Ethena 팀은 `USDe` 토큰의 민팅 및 상환을 자유롭게 수행할 수 있습니다.
- `StakedUSDe` 토큰 계약의 소유자는 주소를 블랙리스트에 올린 다음 `redistributeLockedAmount`를 호출하여 잔액을 목적지 주소로 이동함으로써 누구의 잔액이든 조작할 수 있습니다.
- 사용자는 Ethena가 나중에 실행하는 트랜잭션에 서명만 하므로 `USDe`를 직접 민팅/상환할 수 없습니다. Ethena는 KYC, AML 화이트리스팅 등을 기반으로 사용자에 대해 상당한 양과 다양성의 오프체인 확인을 수행합니다.

위임된 서명자는 사용자(사용자가 `EthenaMinting` 계약이 자신의 `USDe` 토큰을 소각하도록 승인한 경우)를 대신하여 `redeem`을 호출할 수 있으며 담보 자산을 받을 `beneficiary` 주소를 제어할 수 있습니다.

## 특권 역할 및 행위자

- `USDe` 민터(minter) - 모든 주소에 `USDe` 토큰을 얼마든지 민팅할 수 있습니다. `EthenaMinting` 계약이 될 것으로 예상됩니다.
- `USDe` 소유자(owner) - 토큰 `minter`를 설정하고 소유권을 다른 주소로 이전할 수 있습니다.
- `USDe` 토큰 보유자(token holder) - 토큰을 전송할 수 있을 뿐만 아니라 소각하고 다른 사람이 잔액을 소비하도록 허가(permit)에 서명할 수 있습니다.
- `StakedUSDe` 관리자(admin) - 계약에서 토큰을 구출하고 완전히 제한된(fully restricted) 스테이커의 `stUSDe` 잔액을 재분배할 수 있으며, 다른 주소에 역할(예: `FULL_RESTRICTED_STAKER_ROLE` 역할)을 부여할 수도 있습니다.
- `StakedUSDeV2` 관리자(admin) - " `StakedUSDe` 관리자"의 모든 권한을 가지며 `setCooldownDuration` 메서드도 호출할 수 있습니다.
- `REWARDER_ROLE` - 향후 8시간 동안 베스팅(vesting)될 보상을 `StakedUSDe` 계약으로 전송할 수 있습니다.
- `BLACKLIST_MANAGER_ROLE` - `stUSDe` 보유자에 대한 완전 또는 소프트 제한을 수행/취소할 수 있습니다.
- `SOFT_RESTRICTED_STAKER_ROLE` - 이 역할을 가진 주소는 자신의 `USDe` 토큰을 스테이킹하거나 `stUSDe` 토큰을 민팅받을 수 없습니다.
- `FULL_RESTRICTED_STAKER_ROLE` - 이 역할을 가진 주소는 `USDe` 토큰을 언스테이킹하기 위해 `stUSDe` 토큰을 소각할 수 없으며 `stUSDe` 토큰을 전송할 수도 없습니다. 그의 잔액은 `StakedUSDe` 관리자가 조작할 수 있습니다.
- `MINTER_ROLE` - 실제로 `USDe` 토큰을 민팅하고 `EthenaMinting`의 토큰 또는 ETH 잔액을 관리인(custodian) 주소로 전송할 수도 있습니다.
- `REDEEMER_ROLE` - `USDe` 소각을 위해 담보 자산을 상환할 수 있습니다.
- `EthenaMinting` 관리자(admin) - 블록당 maxMint/maxRedeem 금액을 설정하고 지원되는 담보 자산 및 관리인 주소를 추가하거나 제거할 수 있습니다.
- `GATEKEEPER_ROLE` - `USDe`의 민팅/상환을 비활성화하고 승인된 계정에서 `MINTER_ROLE` 및 `REDEEMER_ROLE` 역할을 제거할 수 있습니다.

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

**_검토 커밋 해시_ - [1d0f746d5ebbc5b2254d5d311ac7399aab66ec17](https://github.com/ethena-labs/ethena/tree/1d0f746d5ebbc5b2254d5d311ac7399aab66ec17)**

**_수정 검토 커밋 해시_ - [deaa94b3df961d995e93e408b70b0d085ca1866c](https://github.com/ethena-labs/ethena/tree/deaa94b3df961d995e93e408b70b0d085ca1866c)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `EthenaMinting`
- `USDe`
- `StakedUSDe`
- `StakedUSDeV2`
- `SingleAdminAccessControl`

---

# 발견 사항 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [L-01] | 악의적인 행위자가 `FULL_RESTRICTED_STAKER_ROLE` 역할을 피할 수 있음 | 낮음 | 수정됨 |
| [L-02] | 확인되지 않은 메서드 반환 값은 오류로 이어질 수 있음 | 낮음 | 인지됨 |
| [L-03] | `SOFT_RESTRICTED_STAKER_ROLE`은 잘못된 보안 감각을 줌 | 낮음 | 인지됨 |
| [L-04] | EIP712가 `Route` 구조체에 대해 올바르게 구현되지 않았음 | 낮음 | 수정됨 |

# 상세 발견 사항

# [L-01] 악의적인 행위자가 `FULL_RESTRICTED_STAKER_ROLE` 역할을 피할 수 있음

프로토콜은 `StakedUSDe` 소유자가 주소를 블랙리스트에 올리고 잔액을 조작할 수 있는 권한을 갖도록 `FULL_RESTRICTED_STAKER_ROLE` 역할을 구현했습니다. 이 메커니즘은 결함이 있으며 공격자가 이를 우회할 수 있습니다. 예를 살펴보겠습니다:

1. 사용자가 악의적인 행동을 하거나 나쁜 행위자로 파악됩니다. `StakedUSDe` 소유자는 그의 주소를 블랙리스트에 올리기로 결정합니다.
2. `StakedUSDe` 소유자는 악의적인 사용자의 주소에 대해 `addToBlacklist` 호출이 포함된 트랜잭션을 보냅니다.
3. 사용자는 이더리움의 멤풀을 모니터링하고 있으며, 이 트랜잭션을 프런트 러닝하여 자신의 `stUSDe` 잔액 전부를 자신이 제어하는 다른 주소로 보냅니다.
4. 이제 그의 이전 주소는 `FULL_RESTRICTED_STAKER_ROLE` 역할을 가지게 되어 블랙리스트에 올라갔지만, 그의 모든 토큰은 새 주소에 있으며 정상적으로 사용할 수 있습니다.

현재 최선의 해결책은 모든 관리자 작업에 대해 비공개 멤풀 서비스 사용을 강제하는 것입니다.

## 논의

**Ethena:** 수정됨. 트랜잭션은 항상 Flashbots를 통해서만 제출됩니다.

# [L-02] 확인되지 않은 메서드 반환 값은 오류로 이어질 수 있음

`StakedUSDe` 계약에는 각각 `_grantRole` 및 `_revokeRole`을 호출하는 `addToBlacklist` 및 `removeFromBlacklist` 메서드가 있습니다. 두 경우 모두 후자의 반환 값을 확인해야 역할을 부여할 때 `target`이 이미 이 역할을 가지고 있지 않았는지, 역할을 취소할 때 `target`이 실제로 이 역할을 가지고 있었는지 확인할 수 있습니다.

## 논의

**Ethena:** 인지됨. 가스 비용 오버헤드가 추가되므로 수정하지 않을 예정입니다.

# [L-03] `SOFT_RESTRICTED_STAKER_ROLE`은 잘못된 보안 감각을 줌

이 역할은 보유자가 토큰을 `StakedUSDe`에 예치하거나 그로부터 민팅된 지분을 받는 것을 금지합니다. 이 보호는 계정이 자신의 토큰을 역할을 가지지 않은 다른 주소로 전송하여 우회할 수 있습니다. 초기 계정이 해당 주소를 제어하며 `StakedUSDe`에서 토큰을 예치하거나 지분을 성공적으로 민팅할 수 있습니다. 역할을 제거하고 `FULL_RESTRICTED_STAKER_ROLE`만 사용하는 것을 고려하십시오.

## 논의

**Ethena:** 인지됨. 이것은 디자인의 일부입니다. Ethena는 특정 국가의 사용자에게 수익을 지불하지 않도록 법적으로 요구받고 있으며, 온체인 제한을 포함하여 최선의 노력을 다할 것입니다. 이는 법적으로 우리를 보호하지만 정교한 사용자는 오픈 마켓에서 stUSDe를 사고/팔거나 자금을 다른 주소로 이체하여 스테이킹/언스테이킹하는 등 다양한 방법으로 이 제한을 우회할 수 있습니다.

# [L-04] EIP712가 `Route` 구조체에 대해 올바르게 구현되지 않았음

[EIP712](https://eips.ethereum.org/EIPS/eip-712#definition-of-encodedata) 표준에 명시된 바와 같이 - "배열 값은 내용의 연결된 encodeData의 keccak256 해시로 인코딩됩니다". 이는 `Route` 구조체 배열 필드에 대해 이를 수행하지 않으므로 `EthenaMinting::encodeRoute`에서 올바르게 따르지 않습니다. 이는 일반적으로 더 심각한 문제이지만, 메서드가 프로토콜에서 호출되지 않으므로 제거할 수 있습니다.

## 논의

**Ethena:** 수정됨. `encodeRoute` 메서드가 제거되었습니다.
