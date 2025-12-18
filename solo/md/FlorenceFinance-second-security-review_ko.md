# 소개

**Florence Finance** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Florence Finance** 소개

**첫 번째 보안 검토에서 복사됨**

Florence Finance는 사용자가 암호화 자산으로 실제 대출에 자금을 지원할 수 있는 프로토콜입니다. 사용자가 스테이블코인을 프로토콜에 입금하면 영수증 토큰($FLR 또는 Loan Vault 토큰)을 받는 방식으로 작동합니다. 그런 다음 프로토콜은 실제 대출 플랫폼에 명목 화폐(유로)를 제공하고, 대출 플랫폼은 다시 기업에 명목 화폐를 대출합니다. 대출이 상환되면 이자와 원금이 프로토콜로 다시 유입되어 사용자에게 분배됩니다. 이 프로토콜은 유동성을 보장하지 않으며 P2P 대출과 유사합니다.

Florence Finance 사용자는 두 가지 방법으로 플랫폼을 사용할 수 있습니다:

1. 실제 대출에서 수익을 얻기 위해 Loan Vault에서 스테이블코인을 사용합니다.
2. $FLR을 스테이킹하고 Medici 토큰($MDC)을 얻습니다.

두 가지 방법 모두 사용자는 $FLR을 가지고 있어야 하는데, 이는 DEX에서 스테이블코인을 $FLR로 스왑하여 얻을 수 있으며, $FLR은 실제 대출의 원금에 의해 1:1로 EUR에 의해 뒷받침됩니다. 그런 다음 Loan Vault에 입금하고 Loan Vault 토큰(볼트는 ERC4626을 따름)을 받아 즉시 이자를 받기 시작할 수 있으며, 나중에 Loan Vault 토큰을 소각하여 $FLR을 인출하고 원금과 획득한 수익을 받을 수 있습니다. 사용자가 대신 $MDC 보상을 받기로 선택하면 스테이킹 풀에 $FLR을 스테이킹한 다음 언제든지 인출하거나 보상을 청구할 수 있습니다.

더 많은 [문서](https://docs.florence.finance/)

**계속**

이 프로토콜은 처음에 이더리움 메인넷에 배포되었으며 최근에는 (가스비 비용 때문에) Arbitrum에 배포되었습니다. L2 체인으로의 TVL 마이그레이션은 이미 거의 완료되었으며, `FlorinToken`을 브리지 가능하게 만드는 방식(업그레이드 가능)으로 작동합니다. `FlorenceFinanceMediciToken`에 대해서도 동일한 작업이 수행됩니다. `FlorenceFinanceMediciToken` 계약은 `MediciToken`(사용 중단될 예정)의 새 버전입니다. 사용자는 `MediciConverter` 계약을 사용하여 잔액을 마이그레이션 할 수 있습니다.

## 관찰 사항

검토 대상 계약은 감사 시점에 Arbitrum에서 라이브 되었습니다.

모든 계약은 업그레이드 가능한 프록시 뒤에 있으므로 프로토콜의 중앙 집중화 요소가 높습니다.

## 특권 역할 및 행위자

- `MediciConverter` 소유자(owner) - 변환을 일시 중지/일시 중지 해제할 수 있습니다.
- `FlorinToken` 소유자(owner) - 토큰을 자유롭게 민팅할 수 있을 뿐만 아니라 `registerTokenOnL2` 및 `initializeArbitrumBridging`을 호출할 수 있습니다. Arbitrum 버전의 토큰도 마찬가지이지만 `registerTokenOnL2` 메서드 권한은 없습니다.
- `FlorenceFinanceMediciToken` 소유자(owner) - 토큰을 자유롭게 민팅할 수 있을 뿐만 아니라 `registerTokenOnL2` 및 `initializeArbitrumBridging`을 호출할 수 있습니다. Arbitrum 버전의 토큰도 마찬가지이지만 `registerTokenOnL2` 메서드 권한은 없습니다.

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

**_검토 커밋 해시_ - [f21fe8e3264a401a9732174c8ec6a2367102c563](https://github.com/florence-finance/florence-finance-protocol-v3/tree/f21fe8e3264a401a9732174c8ec6a2367102c563)**

**수정 사항이 구현되지 않았습니다.**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `FlorenceFinanceMediciToken`
- `FlorenceFinanceMediciTokenArbitrum`
- `FlorinToken`
- `FlorinTokenArbitrum`
- `MediciConverter`
- `FlorinTreasury` (`f21fe8e3264a401a9732174c8ec6a2367102c563` 및 `616e9d4ba18eef293dc76fb95144bd11fb29549b` 커밋 간의 차이)
- `LoanVault` (`f21fe8e3264a401a9732174c8ec6a2367102c563` 및 `616e9d4ba18eef293dc76fb95144bd11fb29549b` 커밋 간의 차이)

---

# 발견 사항 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [M-01] | 소각된 토큰을 `totalSupply`로 다시 민팅할 수 있음 | 중간 | 인지됨 |
| [L-01] | Arbitrum 시퀀서 가용성 확인 누락 | 낮음 | 인지됨 |
| [L-02] | 업그레이드 가능성 모범 사례를 따르지 않음 | 낮음 | 인지됨 |
| [L-03] | 초기화 메서드(initializer methods)를 한 번만 호출할 수 있도록 강제 | 낮음 | 인지됨 |

# 상세 발견 사항

# [M-01] 소각된 토큰을 `totalSupply`로 다시 민팅할 수 있음

## 심각도

**영향:**
낮음, 자금 손실로 이어지지는 않지만 프로토콜 불변성/가정을 깨뜨리기 때문

**가능성:**
높음, 누군가가 토큰을 소각할 때마다 문제가 되기 때문

## 설명

`FlorenceFinanceMediciToken` 계약은 `ERC20CappedUpgradeable`을 상속하며 최대 공급 한도가 "1_000_000_000 \* 10 \*\* 18" 토큰 단위입니다. 문제는 계약이 `ERC20BurnableUpgradeable` 계약도 상속한다는 것입니다. 즉, 사용자가 `burn` 메서드를 호출하면 `totalSupply`에서 차감됩니다. 예를 들어 10개의 토큰이 존재하고 모두 소각된 상태에서 10개의 새 토큰이 민팅되면, 이제 `totalSupply = 10`이 되는데, 이는 민팅된 토큰의 총공급량이 최대 "1_000_000_000 \* 10 \*\* 18"일 수 있다는 프로토콜의 가정과 맞지 않습니다.

## 권장 사항

`totalSupply`에서 차감하여 토큰을 소각하는 것이 불가능하도록 `FlorenceFinanceMediciToken`에서 `ERC20BurnableUpgradeable`의 상속을 제거하십시오.

# [L-01] Arbitrum 시퀀서 가용성 확인 누락

`LoanVault`의 `getFundingTokenExchangeRate` 메서드는 `latestRoundData` 메서드를 호출하여 Chainlink 가격 피드를 사용합니다. 가격 피드 응답에 대한 충분한 검증이 있지만 프로토콜이 이더리움에서 Arbitrum으로 이동하고 있으므로 반드시 있어야 할 L2 시퀀서 가용성에 대한 확인이 누락되었습니다. L2 시퀀서를 사용할 수 없는 경우 프로토콜은 오래된 가격으로 작동하며, 시퀀서가 다시 백업되면 대기 중인 모든 트랜잭션이 새로운 트랜잭션이 수행되기 전에 Arbitrum에서 실행됩니다. 이로 인해 `LoanVault::getFundingTokenExchangeRate`가 오래된 가격을 사용하게 되어 사용자가 `LoanVault`에서 받아야 할 것보다 더 많거나 적은 지분을 받을 수 있습니다. 하지만 모든 자금 조달 요청은 오프체인에서 먼저 승인되므로 이러한 문제가 발생할 확률은 훨씬 낮습니다. 수정을 위해 [Chainlink 문서](https://docs.chain.link/data-feeds/l2-sequencer-feeds)를 따르며 시퀀서 가용성에 대한 확인을 추가할 수 있습니다.

# [L-02] 업그레이드 가능성 모범 사례를 따르지 않음

`LoanVault`에서 저장소 레이아웃이 업그레이드 가능성에 안전하지 않은 방식으로 변경되었습니다. 커밋 `616e9d4ba18eef293dc76fb95144bd11fb29549b`와 `audit` 브랜치의 현재 HEAD 사이에서 `fundingFee` 저장소 변수의 위치가 변경되었고 화이트리스팅 저장소 변수도 제거된 것으로 보입니다. 이더리움 계약에서 잠재적인 업그레이드가 수행되면 저장소 레이아웃이 엉망이 되어 계약이 벽돌(bricking)이 될 수 있으므로 이는 매우 위험합니다. 하지만 프로토콜이 이를 방지하는 OpenZeppelin의 업그레이드 플러그인을 사용하고 있으므로 이러한 일이 발생할 가능성은 매우 낮습니다. 그럼에도 불구하고 업그레이드 가능성 모범 사례는 항상 적용되어야 합니다. 저장소 변수를 재정렬하지 말고 이전 변수를 제거하는 대신 더 이상 사용되지 않음(deprecate)으로 처리하십시오.

# [L-03] 초기화 메서드(initializer methods)를 한 번만 호출할 수 있도록 강제

`LoanVault`의 `setLoansOutstanding` 메서드는 NatSpec에서 "이것은 Arbitrum의 초기 배포에 사용됩니다."라고 말합니다. 즉, 메서드는 한 번만 호출할 수 있어야 합니다. 코드베이스의 토큰 계약에 있는 `initializeArbitrumBridging` 메서드에도 동일한 참고 사항이 유효합니다.
