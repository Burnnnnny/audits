# 소개

**Beam** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Beam** 소개

Beam은 ERC20 토큰(`BeamToken`), `TokenBurner` 계약, `Migrator` 계약 및 `BeamDAO` 계약(사용되지 않으며 범위 외)으로 구성된 프로토콜입니다. 토큰은 [MeritToken](https://etherscan.io/address/0x949d48eca67b17269629c7194f4b727d4ef9e5d6#code)의 포크입니다. 아이디어는 사용자가 `MeritToken` 토큰을 `BeamToken` 토큰으로 마이그레이션할 수 있도록 하는 것이며, 여기서 1 `MeritToken` = 100 `BeamToken`입니다.

## 관찰 사항

프로토콜 토큰의 민팅(minting) 및 소각(burning)은 관리자가 제어하는 역할에 의해 제어되므로 프로토콜에 중앙 집중화가 존재합니다.

## 특권 역할 및 행위자

- `BeamToken` 관리자 - 시스템의 다른 주소에 역할을 부여할 수 있습니다.
- `BEAM` 민터(minter) - `BeamToken::mint`를 호출하여 임의의 주소에 임의의 양의 토큰을 민팅할 수 있습니다.
- `BEAM` 버너(burner) - `BeamToken::burn`을 호출하여 임의의 주소에서 임의의 양의 토큰을 소각할 수 있습니다.

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

**_검토 커밋 해시_ - [a11ee9d60f6ab8413daf8fbb222335baaab95db1](https://github.com/Merit-Circle/beam-token/tree/a11ee9d60f6ab8413daf8fbb222335baaab95db1)**

**_수정 검토 커밋 해시_ - [b3749ec52abd9333638ecf6b1365c5a539fe1d57](https://github.com/Merit-Circle/beam-token/tree/b3749ec52abd9333638ecf6b1365c5a539fe1d57)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Migrator`
- `BeamToken`
- `TokenBurner`

---

# 발견 사항 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [M-01] | `BeamToken`의 민팅 및 소각은 중앙 집중화되어 있음 | 중간 | 인지됨 |
| [L-01] | 취약한 버전의 외부 라이브러리 사용 | 낮음 | 수정됨 |
| [L-02] | 오래된 Solidity 컴파일러 버전 사용 | 낮음 | 수정됨 |

# 상세 발견 사항

# [M-01] `BeamToken`의 민팅 및 소각은 중앙 집중화되어 있음

## 심각도

**영향:**
높음, 토큰 공급이 끝없이 팽창될 수 있고 사용자 토큰이 마음대로 소각될 수 있기 때문

**가능성:**
낮음, 악의적이거나 침해된 관리자/민터/버너가 필요하기 때문

## 설명

현재 `BeamToken`의 `mint` 및 `burn` 메서드는 각각 `MINTER_ROLE` 및 `BURNER_ROLE`에 의해 제어됩니다. 이러한 역할은 `BeamToken` 배포자에게 부여된 `DEFAULT_ADMIN_ROLE`에 의해 제어됩니다. 즉, 관리자, 민터 또는 버너 계정이 악의적이거나 침해된 경우 토큰 공급을 끝없이 팽창시키거나 사용자의 토큰 잔액을 소각하기로 결정할 수 있으며, 이는 사용자의 자금 손실로 이어질 수 있습니다.

## 권장 사항

사용자가 관리자/민터/버너의 트랜잭션에 동의하지 않는다고 판단할 경우 `BeamToken` 포지션을 종료할 수 있는 충분한 시간을 가질 수 있도록 Timelock 메커니즘이 있는 계약에만 해당 역할을 부여하십시오.

# [L-01] 취약한 버전의 외부 라이브러리 사용

`package.json`에서 사용되는 OpenZeppelin 라이브러리 버전이 `"@openzeppelin/contracts": "^4.3.1"`임을 볼 수 있습니다. OpenZeppelin의 [보안 권고](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories)에 따르면 이 버전에는 여러 가지 높음 및 중간 심각도의 취약점이 포함되어 있음을 알 수 있습니다. 범위 내 코드는 현재 라이브러리 코드의 취약한 부분을 사용하지 않지만, 브레이킹 체인지(breaking changes)가 없는 최신 안정 버전, 즉 4.9.3 버전으로 업데이트하는 것이 강력히 권장됩니다.

# [L-02] 오래된 Solidity 컴파일러 버전 사용

현재 프로토콜 계약은 `pragma solidity 0.8.6;`을 사용하는데, 이는 Solidity의 [알려진 버그 목록](https://docs.soliditylang.org/en/develop/bugs.html)에 따르면 일부 낮은 심각도의 문제를 포함하는 버전입니다. 버그 수정 및 최적화를 사용하기 위해 컴파일러 버전을 최신 버전으로 업데이트하는 것이 강력히 제안됩니다.
