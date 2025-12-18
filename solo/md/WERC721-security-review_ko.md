# 소개

**pashov**가 **WERC721** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. 그의 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **WERC721** 소개

WERC721은 다음 기능/최적화를 허용하는 ERC721 래퍼입니다.

- 네이티브 호출 일괄 처리(call-batching)
- 상당한 `transfer` 가스 비용 절감
- 메타 트랜잭션 (승인된 전송)

이 프로토콜은 비허가형(permissionless)이고 업그레이드 불가능하며 무료입니다. 내장 수수료가 없습니다. WERC721은 ERC721 표준의 부분 준수 구현이며 일부 메서드(`balanceOf`, `safeTransferFrom`, `approve`, `getApproved`)가 누락되어 있습니다. 이 프로토콜은 Ethereum Mainnet, Optimism, Arbitrum, Arbitrum Nova, Polygon 및 zkEVM에 배포될 예정입니다.

## 관찰 사항

`WERC721Factory` 계약은 새로운 `WERC721` 인스턴스를 배포하기 위해 CloneWithImmutableArgs 접근 방식을 사용합니다.

`WERC721` 계약은 `wrap` 호출 전에 사용자의 NFT를 래핑 되도록 이동할 수 있는 권한을 갖습니다.

애플리케이션에서 사용되는 서명 논스는 순차적인 `uint256` 논스와 달리 `bytes32` 유형입니다. 이는 [EIP3009](https://eips.ethereum.org/EIPS/eip-3009)를 따릅니다.

## 특권 역할 및 행위자

- Authorized WERC721 spender - 토큰 소유자가 서명한 페이로드를 사용하여 WERC721 토큰을 전송할 수 있습니다.
- ERC721 token holder - `WERC721::wrap`을 호출하여 자신의 NFT 토큰을 래핑 할 수 있습니다.
- Any user - `WERC721Factory::create`를 호출하여 모든 ERC721 컬렉션 계약에 대한 WERC721 래퍼 계약을 배포할 수 있습니다.

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

**_검토 커밋 해시_ - [615d8633d8928e5a13af98ac098cf3631d092d49](https://github.com/jpvge/WERC721/tree/615d8633d8928e5a13af98ac098cf3631d092d49)**

**_수정 검토 커밋 해시_ - [2422c33588f104704e554ca14e4ab4c9a4878755](https://github.com/jpvge/WERC721/tree/2422c33588f104704e554ca14e4ab4c9a4878755)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `WERC721`
- `WERC721Factory`

또한 프로젝트 종속성의 다음 메서드들이 감사 범위에 포함되었습니다:

- [LibClone::clone](https://github.com/Vectorized/solady/blob/2cfa231273fea6872c7cb70acfa134d2199aa7ea/src/utils/LibClone.sol#L303)
- [Clone::\_getArgAddress](https://github.com/Vectorized/solady/blob/2cfa231273fea6872c7cb70acfa134d2199aa7ea/src/utils/Clone.sol#L28)
- [Multicallable::multicall](https://github.com/Vectorized/solady/blob/2cfa231273fea6872c7cb70acfa134d2199aa7ea/src/utils/Multicallable.sol#L19)

---

# 결과 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [L-01] | 프록시에서 ETH를 꺼낼 방법이 없음 | 낮음 | 인지함 |
| [L-02] | 일부 계약은 `WERC721` 토큰을 처리하지 못할 수 있음 | 낮음 | 인지함 |
| [L-03] | 바이트코드가 모든 EVM 기반 체인과 호환되지 않을 수 있음 | 낮음 | 수정됨 |
| [L-04] | 타임스탬프 비교에 논리적 오류가 있음 | 낮음 | 수정됨 |

# 상세 결과

# [L-01] 프록시에서 ETH를 꺼낼 방법이 없음

사용된 프록시 패턴은 `receive() payable` 메서드를 구현하지만([여기](https://github.com/Vectorized/solady/blob/2cfa231273fea6872c7cb70acfa134d2199aa7ea/src/utils/LibClone.sol#L337-L349)에서 볼 수 있음), 프록시에서 받은 ETH를 꺼낼 방법이 없습니다. `delegatecall`하는 구현 계약(`WERC721`)에 ETH를 인출하는 메서드가 없기 때문입니다. 프록시가 ETH를 받을 것으로 예상되지 않으므로 높은 영향(High Impact) 문제는 아니지만 `withdrawETH` 메서드를 추가할 가치는 여전히 있습니다.

# [L-02] 일부 계약은 `WERC721` 토큰을 처리하지 못할 수 있음

`ERC721` 표준은 `safeTransferFrom` 기능을 추가했는데, 이는 그러한 토큰을 받을 것으로 예상하지 않으며 처리(이동)할 수 없는 스마트 계약 계정이 있을 수 있기 때문입니다. 스마트 계약이 `transfer` 또는 `unwrap`을 호출할 수 없는 경우 `WERC721`에도 동일한 문제가 존재합니다. 가능한 해결책은 `ERC721`과 동일한 메커니즘입니다.

# [L-03] 바이트코드가 모든 EVM 기반 체인과 호환되지 않을 수 있음

프로토콜은 여러 EVM 기반 체인(Optimism, Arbitrum, Polygon 등)에 배포될 예정이지만 `pragma` 문은 Solidity 컴파일러의 0.8.21 버전을 사용하는 것을 보여줍니다. 이 버전(및 0.8.19 이후의 모든 버전)은 `PUSH0` 연산 코드를 사용하는데, 이는 Arbitrum과 같은 일부 EVM 기반 체인에서 아직 지원되지 않습니다. 동일한 결정적 바이트코드를 모든 체인에 배포할 수 있도록 버전 0.8.19를 사용하는 것을 고려하십시오.

# [L-04] 타임스탬프 비교에 논리적 오류가 있음

`WERC721::transferFromWithAuthorization`에서 다음 코드를 봅니다:

```solidity
if (block.timestamp < validAfter) revert InvalidTransferAuthorization();
if (block.timestamp > validBefore)
    revert InvalidTransferAuthorization();
```

여기에 논리적 오류가 있습니다. 변수 이름이 `validAfter`임에도 불구하고 변수 값이 전송을 위한 유효한 타임스탬프로 계속 작동하기 때문입니다. 예를 들어 코드가 "validAfter = 오후 12:00:00"라고 하고 오후 12:00:00을 전달하면 "이후"에만 유효해야 하므로 실패해야 하지만 현재 코드로는 작동합니다. `<`를 `<=`로, `>`를 `>=`로 변경하십시오.

