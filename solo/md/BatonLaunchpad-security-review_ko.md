# 소개

**Baton Launchpad** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Baton Launchpad** 소개

Baton Launchpad는 NFT 런치패드 프로토콜입니다. NFT 크리에이터가 토큰의 유동성을 극대화할 수 있도록 하는 여러 기능이 있습니다. 환불, 시차를 둔 민팅(minting), 이자 농사(yield farming), 베스팅(vesting) 및 유동성 잠금(locking liquidity)을 구성할 수 있습니다.

주어진 시간까지 토큰이 모두 민팅되지 않으면 사용자는 NFT를 소각하여 환불을 받을 수 있습니다. 민트는 화이트리스트나 다른 민트 가격이 필요할 수 있는 다양한 범주에 속하도록 구성할 수도 있습니다. 또 다른 점은 팀, 투자자 등에게 할당된 NFT는 시간이 지남에 따라 베스팅될 수 있으며 사용자는 민트 후 2차 시장에 즉시 덤핑되지 않을 것이라고 확신할 수 있습니다.

## 관찰 사항

NFT가 모두 민팅되지 않고 사람들이 토큰을 소각하여 환불을 받더라도 토큰 크리에이터는 여전히 베스팅이 가능합니다.

베스팅 할 토큰이 있고 컬렉션이 모두 민팅된 경우 `maxMintSupply`는 `totalSupply`보다 작습니다. 베스팅 된 토큰과 이자 농사 또는 잠긴 LP로 사용되는 토큰은 `maxMintSupply`에 포함되지 않습니다.

외부 종속성은 [baton-contracts](https://github.com/baton-finance/baton-contracts)와 [Caviar](https://github.com/outdoteth/caviar)이며 둘 다 감사를 받았습니다.

## 특권 역할 및 행위자

- Baton Launchpad 소유자 - NFT 민트 수수료율과 복제할 `nftImplementation` 계약을 구성할 수 있으며 `Nft`에서 잠긴 LP 토큰의 마이그레이션을 실행할 수 있습니다.
- Nft 소유자 - 민트 지급금을 인출할 수 있으며 잠재적인 잠긴 LP 마이그레이션을 위한 대상 계약을 설정할 수도 있습니다.
- 베스팅 수령자 - 특정 시간 임계값 이후에 `vest`를 호출하여 베스팅 된 NFT 토큰을 청구할 수 있습니다.
- 화이트리스트에 있는 민터(Whitelisted Minter) - `proof`를 제출하여 화이트리스트가 필요한 범주에서 NFT를 민팅할 수 있습니다.
- 민터(Minter) - 화이트리스트가 필요 없는 범주에서 NFT를 민팅할 수 있습니다.

민터는 특정 시간 임계값까지 민트가 완료되지 않았고 환불이 활성화된 경우 ETH를 환불받을 수도 있습니다.

누구나 `lockLp` 및 `seedYieldFarm`을 호출하여 `Pair` 계약이나 `BatonFarm` 계약에 토큰을 민팅할 수 있습니다.

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

**_검토 커밋 해시_ - [d278f930ef3e358f61fa5c42a73d197059aa2dad](https://github.com/baton-finance/baton-launchpad/tree/d278f930ef3e358f61fa5c42a73d197059aa2dad)**

**_수정 검토 커밋 해시_ - [709e1df99d433266554dc5a551c4ab325bcdc2cb](https://github.com/baton-finance/baton-launchpad/tree/709e1df99d433266554dc5a551c4ab325bcdc2cb)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `BatonLaunchpad`
- `Nft`

---

# 발견 사항 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [C-01] | `BatonLaunchpad`에서 NFT 민트의 프로토콜 수수료를 청구할 수 없음 | 치명적 | 수정됨 |
| [H-01] | 사용자 입력 검증 누락으로 자금이 묶일 수 있음 | 높음 | 수정됨 |
| [M-01] | `BatonFarm`의 보상 마이그레이션을 실행할 수 없음 | 중간 | 수정됨 |
| [M-02] | NFT 생성에 대한 프런트 러닝(front-running) 그리핑(griefing) 공격 가능 | 중간 | 수정됨 |
| [M-03] | 프로토콜에 중앙 집중화 취약점이 존재함 | 중간 | 부분적으로 수정됨 |
| [L-01] | `Nft`의 `payable` 메서드로 인해 ETH가 묶일 수 있음 | 낮음 | 인지됨 |
| [L-02] | 환불 메커니즘은 승인이 있는 계정에서 사용할 수 있음 | 낮음 | 수정됨 |

# 상세 발견 사항

# [C-01] `BatonLaunchpad`에서 NFT 민트의 프로토콜 수수료를 청구할 수 없음

## 심각도

**영향:**
높음, 프로토콜의 가치 손실을 초래하기 때문

**가능성:**
높음, 확실히 발생하기 때문

## 설명

`Nft::mint`에서 예상되는 `msg.value`는 NFT 가격에 민팅할 NFT 양을 곱하고 프로토콜 수수료를 더한 값입니다. 이 프로토콜 수수료는 `mint` 메서드가 끝날 때 다음과 같이 `BatonLaunchpad` 계약으로 전송됩니다:

```solidity
if (protocolFee != 0) {
    address(batonLaunchpad).safeTransferETH(protocolFee);
}
```

`BatonLaunchpad`는 `payable`로 표시된 `receive` 메서드를 정의하며 이는 정확합니다. 문제는 `BatonLaunchpad`에 ETH 잔액을 꺼낼 방법이 없다는 것입니다. 어떤한 방법으로도 소비될 수 없어 계약에 영원히 묶이게 됩니다.

## 권장 사항

`BatonLaunchpad`에 계약의 `owner`가 ETH 잔액을 인출할 수 있는 메서드를 추가하십시오.

# [H-01] 사용자 입력 검증 누락으로 자금이 묶일 수 있음

## 심각도

**영향:**
높음, 모든 민트 수수료가 영원히 묶일 수 있기 때문

**가능성:**
중간, 사용자가 입력을 쉽게 잘못 구성할 수 있기 때문

## 설명

`Nft`의 `initialize` 메서드 인수의 입력 검증에는 여러 가지 불충분한 점이 있습니다:

1. 모든 `categories_`의 `supply` 합계가 `maxMintSupply_`보다 작을 수 있습니다. 이는 민트가 완료되지 않아 지금까지 민트로 인해 `Nft` 계약에 들어온 모든 ETH가 영원히 묶이게 됩니다.
2. `vestingParams_`의 `duration`에는 하한 및 상한이 있어야 합니다. 예를 들어 기간이 너무 길면 베스팅이 완료되지 않거나 나눗셈 반올림 오류가 발생할 수 있습니다.
3. `refundParams_`의 `mintEndTimestamp`는 미래 너무 멀리에 있으면 안 됩니다. 그렇지 않으면 환불 및 베스팅 메커니즘이 작동하지 않으며, 너무 가까우면 민트 메커니즘이 작동하지 않습니다.

## 권장 사항

모든 범주의 공급 합계가 `maxMintSupply`보다 크거나 같은지 확인하는 검증을 추가하십시오. 또한 베스팅 메커니즘의 `duration`과 환불 메커니즘의 `mintEndTimestamp`에 대해 합리적인 상한 및 하한을 추가하십시오.

# [M-01] `BatonFarm`의 보상 마이그레이션을 실행할 수 없음

## 심각도

**영향:**
높음, 보상이 묶일 수 있기 때문

**가능성:**
낮음, 마이그레이션이 필요할 가능성이 낮기 때문

## 설명

`Nft` 계약의 외부 종속성인 `BatonFarm` 계약(`BatonFarm`은 `seedYieldFarm`에 배포됨)에는 획득하지 않은 보상을 새 계약으로 이동하는 마이그레이션 메커니즘이 있습니다. 이 기능은 `BatonFarm` 소유자(이 경우 `Nft` 계약)가 `BatonFarm`의 `initiateMigration` 메서드를 호출하는 것에 의존하기 때문에 현재 차단되었습니다. 이에 대한 코드가 없으므로 이러한 호출이 불가능하여 현재 시스템에서 마이그레이션이 불가능합니다. 즉, 일부 `Nft` 계약에 의해 배포된 `BatonFarm` 계약에 보상이 남아 있으면 영원히 거기에 묶이게 됩니다.

## 권장 사항

`Nft` 관리자가 `initiateMigration` 호출을 실행할 수 있는 방법을 추가하십시오.

# [M-02] NFT 생성에 대한 프런트 러닝(front-running) 그리핑(griefing) 공격 가능

## 심각도

**영향:**
중간, 프로토콜 사용자에게 일시적인 DoS를 초래하기 때문

**가능성:**
중간, 실행하기 쉽지만 공격자에게는 큰 인센티브가 없기 때문

## 설명

`BatonLaunchpad`의 `create` 메서드는 `create2` opcode를 사용하는 `LibClone`의 `cloneDeterministically` 메서드를 호출합니다. `create` 메서드에는 `cloneDeterministically` 호출에 전달되는 `salt` 매개변수도 있습니다. 악의적인 행위자는 `create`에 대한 모든 호출을 프런트 러닝하고 동일한 `salt` 인수를 사용할 수 있습니다. `create2`가 배포하려는 주소에 이미 계약이 있으므로 모든 사용자 트랜잭션이 되돌려집니다.

## 권장 사항

`cloneDeterministically`에 전달된 `salt` 인수에 `msg.sender`를 추가하면 이 문제가 해결됩니다.

# [M-03] 프로토콜에 중앙 집중화 취약점이 존재함

## 심각도

**영향:**
높음, 러그 풀(rug pull)로 이어질 수 있기 때문

**가능성:**
낮음, 침해되거나 악의적인 소유자가 필요하기 때문

## 설명

`BatonLaunchpad`의 `owner`는 계약의 `nftImplementation` 및 `feeRate` 저장소 변수 값에 대한 전적인 통제 권한을 갖습니다. 이는 몇 가지 공격 벡터를 엽니다:

1. `BatonLaunchpad`의 `owner`는 `create` 호출을 프런트 러닝하여 `nftImplementation` 계약을 민트 수수료를 인출할 수 있는 메서드가 있는 계약으로 변경하여 "러그 풀"을 초래할 수 있습니다.
2. `BatonLaunchpad`의 `owner`는 수수료를 훨씬 더 높은 값으로 변경하여 `Nft` 민터가 막대한 수수료를 지불하도록 강요하거나 단순히 토큰을 민팅하고 싶지 않게 만들 수 있습니다.
3. `Caviar` 종속성의 `owner`는 `Pair` 계약에서 `close`를 호출할 수 있으며, 이는 `lockLp`의 `nftAdd` 호출과 `seedYieldFarm`의 `wrap` 호출이 되돌려짐을 의미합니다. 이는 LP 잠금 및 이자 농사 시딩이 완료될 수 없음을 의미할 수 있으며, 이는 `Nft` 계약의 `owner`가 `withdraw`를 호출할 수 없어 계약에 ETH가 묶이게 됨을 의미합니다.

## 권장 사항

`nftImplementation` 메서드를 한 번만 호출할 수 있도록 하여 처음에 설정한 후에는 값을 업데이트할 수 없도록 하십시오. `feeRate`의 경우 `MAX_FEE_RATE` 상수 값을 추가하고 새 값이 그보다 작거나 같은지 확인하십시오. `Caviar` 종속성 문제의 경우 `try-catch`로 호출하고 호출이 오류를 발생시키면 LP 잠금 또는 이자 농사 시딩을 완료하십시오.

# [L-01] `Nft`의 `payable` 메서드로 인해 ETH가 묶일 수 있음

`ERC721AUpgradeable`의 여러 메서드(예: 재정의된 `transferFrom`)에는 `payable` 키워드가 있어 ETH를 받을 수 있음을 의미합니다. 이는 가스 최적화이지만 `ERC721AUpgradeable`을 상속하므로 `Nft` 계약에 ETH가 묶일 수 있습니다. 이 문제로부터 보호하기 위해 `payable` 메서드를 재정의하고 `msg.value != 0`일 때 되돌릴(revert) 수 있습니다.

# [L-02] 환불 메커니즘은 승인이 있는 계정에서 사용할 수 있음

`refund` 메서드는 `_burn`을 호출하여 승인이 있는 경우 토큰을 소각할 수 있습니다. 이는 `msg.sender`가 `_accounts` 매핑에 `totalMinted` 및 `availableRefund` 값을 가져야 하므로 발생할 가능성이 매우 낮은 시나리오이지만 여전히 논리적 오류입니다. `tokenIds`의 소유자만 `refund`를 실행할 수 있도록 허용하십시오.
