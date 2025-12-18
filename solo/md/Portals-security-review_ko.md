# 소개

**pashov**가 **Portals** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **Portals** 소개

Portals는 최적의 경로 스왑, 스테이킹, LP 등 메커니즘을 허용하기 위해 여러 호출을 집계(aggregate)하는 플랫폼입니다. 최고의 USD 출력 + 최소 가스 소비를 위해 최적화합니다. 사용자는 입력 및 출력 토큰, 입력 금액 및 발신자/수신자를 선택하여 Portals API에서 견적을 요청합니다. 스마트 주문 라우팅 알고리즘이 스왑 또는 작업의 최적 경로를 찾는 데 사용됩니다. 사용 사례의 예로는 Curve 풀에 유동성을 추가하거나(기본 토큰으로 스왑하고 `add_liquidity` 호출) 폴딩 전략(대출, 스왑, 대출 등)을 실행하는 것이 있습니다. 프로토콜에는 슬리피지 허용 오차 메커니즘이 구현되어 있습니다.

[추가 문서](https://docs.portals.fi/)

## 관찰 사항

`PortalsRouter`는 모든 승인/허용(approvals/allowances)을 처리하는 계약입니다. 계약은 서명된 승인 및 ERC20의 허가(permits)를 많이 사용합니다.

프로토콜은 수익 공유를 수행하며, 수수료의 50%는 온체인 주문이 발생한 프론트엔드 또는 `partner`에게 돌아갑니다. `partner`는 이벤트에만 기록된 다음 수익 공유가 오프체인에서 실행되므로 신뢰할 수 없는 방식(not trustless)입니다.

이 프로토콜은 비수탁형(non-custodial)으로, 트랜잭션 호출 사이에 자금을 보유하지 않습니다. 자금/수수료를 보유하는 유일한 시간은 트랜잭션 내(intra-transaction)입니다. 이는 공격 표면을 크게 줄여줍니다.

`PortalsMulticall` 계약은 누구든지 임의의 코드 실행을 허용합니다.

## 특권 역할 및 행위자

- Router owner - `PortalsRouter` 계약을 일시 중지/해제할 수 있고, `Multicall` 계약 주소를 설정하며, 라우터에 갇힌 토큰을 복구받을 수 있습니다.
- Multicall fees receiver - 백엔드에서 오프체인으로 설정되며(`aggregate`의 `calls` 배열에 추가됨), 일부 수수료를 받습니다.
- Portal broadcaster - `PortalsRouter`를 호출하여 `SignedOrder`를 실행합니다.
- Portal user - `PortalsRouter`를 호출하여 `Order`를 실행하며, `PortalsRouter`에 지출 허용을 제공합니다.

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

**_검토 커밋 해시_ - [d8be537304c8cd0bd3433b3aca5770d9375fc2ce](https://github.com/portals-fi/portals-sc/tree/d8be537304c8cd0bd3433b3aca5770d9375fc2ce)**

**_수정 검토 커밋 해시_ - [c1c223697b1a796623162148e1fd821e109c4107](https://github.com/portals-fi/portals-sc/tree/c1c223697b1a796623162148e1fd821e109c4107)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `portals/multicall/interface/**`
- `portals/multicall/PortalsMulticall`
- `portals/router/interface/**`
- `portals/router/RouterBase`
- `portals/router/PortalsRouter`

다음과 같은 수의 문제가 발견되었으며 심각도별로 분류되었습니다:

- 치명적 & 높음: 0개 문제
- 중간: 0개 문제
- 낮음: 3개 문제

---

# 결과 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [L-01] | 수수료 수령자가 슬리피지 허용 오차를 차익 거래할 수 있음 | 낮음 |
| [L-02] | `chainId`가 캐시되지만 변경될 수 있음 | 낮음 |
| [L-03] | 프로토콜이 취약한 라이브러리를 사용 중임 | 낮음 |

# 상세 결과

# [L-01] 수수료 수령자가 슬리피지 허용 오차를 차익 거래할 수 있음

`PortalsMulticall::aggregate`의 `calls` 배열은 항상 수수료 지불 트랜잭션을 포함할 것으로 예상되며, 여기서 스왑 수수료가 프로토콜 제어 계정으로 전송됩니다. 또한 때때로 배열 끝에 "sweep ETH" 호출이 포함되어 남은 모든 ETH가 원래 호출자에게 다시 스윕되도록 할 것으로 예상됩니다. 문제는 이 특정 시나리오에서 스윕 전에 수수료가 전송될 때, 수수료 수령자가 `transferEth`를 호출하여 계약에 재진입(reenter)하고 호출자 잔액이 슬리피지 허용 오차 수준에 머무를 만큼만 ETH를 전송할 수 있다는 것입니다.

이 공격은 여러 조건(낮은 가능성)이 필요하며 슬리피지 허용 오차 값까지로 제한되므로(낮음에서 중간 영향), 심각도는 낮음(Low)이지만 여전히 가능한 공격 벡터입니다. 한 가지 가능한 해결책은 `transferEth` 함수에 `nonReentrant` 수정자를 추가하는 것이지만, 이는 `aggregate` 호출의 일부로 호출하는 것을 허용하지 않게 됩니다.

## 논의

**pashov:** 인지함(Acknowledged).

# [L-02] `chainId`가 캐시되지만 변경될 수 있음

`chainId` 값(`RouterBase`의 생성자)을 캐시하는 것은 하드 포크가 네트워크의 chainId를 변경할 수 있으므로 좋은 관행이 아닙니다. 더 나은 해결책은 항상 현재 `block.chainid`가 캐시된 것과 같은지 확인하고 그렇지 않은 경우 업데이트하는 것입니다. [OpenZeppelin의 EIP712 구현](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/2271e2c58d007894c5fe23c4f03a95f645ac9175/contracts/utils/cryptography/EIP712.sol#L81-L87)의 접근 방식을 따르거나 OpenZeppelin의 EIP712 계약을 상속받으십시오.

## 논의

**pashov:** 수정됨(Fixed).

# [L-03] 프로토콜이 취약한 라이브러리를 사용 중임

OpenZeppelin 라이브러리의 4.8.0 버전에는 [여기](https://github.com/OpenZeppelin/openzeppelin-contracts/security)에 나열된 보안 취약점이 있습니다. 현재 취약한 코드가 코드베이스에서 사용되지 않지만, 최신 보안 패치를 포함하도록 라이브러리를 업데이트해야 합니다.

## 논의

**pashov:** 수정됨(Fixed).

