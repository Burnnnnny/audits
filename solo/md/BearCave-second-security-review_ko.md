# 소개

**Bear Cave** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Bear Cave** 소개

Bear Cave는 온체인에서 여러 NFT 게임을 호스팅 할 수 있는 프로토콜입니다. 프로토콜은 게임을 교차 체인(cross-chain)으로 만들기 위해 LayerZero 기술을 통합했습니다. 게임의 NFT는 이더리움 메인넷에서 민팅되지만 실제 게임은 아비트럼(Arbitrum) 메인넷에서 시작됩니다. Honey Jar NFT는 무료 청구를 하거나 ETH 또는 지불 토큰(예상: $OHM)으로 민팅됩니다. 일정 수의 Honey Jar NFT가 민팅되면 Chainlink의 VRF 기술을 사용하여 게임 승자가 선정되며 특별한 ERC721/ERC1155 토큰을 상품으로 청구할 수 있습니다.

작업 흐름은 다음과 같습니다:

1. (이더리움 메인넷) 게임 관리자가 `addBundle`을 호출하여 게임의 우승 상품 ERC721/ERC1155 토큰을 구성합니다.
2. (이더리움 메인넷) 게임 관리자가 `puffPuffPassOut`을 호출하여 토큰을 `HibernationDen` 계약으로 실제로 전송하고 아비트럼 메인넷으로 교차 체인 메시지를 보내어 거기서 게임을 시작합니다(`startGame` 호출).
3. (아비트럼 메인넷) 이제 게임이 실행 중이므로 사용자는 `claim`, `claimAll`, `earlyMekHoneyJarWithERC20`, `earlyMekHoneyJarWithEth`, `mekHoneyJarWithERC20` 및 `mekHoneyJarWithETH`를 통해 HoneyJar NFT를 민팅할 수 있습니다.
4. (아비트럼 메인넷) Honey Jar 민트의 각 체크포인트에서 Chainlink의 VRF가 호출됩니다.
5. (아비트럼 메인넷) VRF 무작위성이 이행되어 L2에서 발효된 항아리(Fermented jars, 우승 NFT)를 설정합니다.
6. (아비트럼 메인넷) 누구나 수동으로 `sendFermentedJars`를 호출하여 교차 체인 메시지를 보낼 수 있으며, 이 메시지는 우승 NFT(발효된 항아리)와 관련된 L2 상태를 이더리움 메인넷에 다시 적용합니다(`setCrossChainFermentedJars` 호출).
7. (아비트럼 메인넷) 우승한 HoneyJar NFT 소유자는 이를 이더리움 메인넷으로 브리지 해야 합니다.
8. (이더리움 메인넷) 우승한 HoneyJar NFT 소유자는 `wakeSleeper`를 호출하여 우승 상품 ERC721/ERC1155 토큰을 청구할 수 있습니다.

선택적 기능은 `HibernationDen::addToParty`로, 이미 실행 중인 게임에 더 많은 당첨 상품 ERC721/ERC1155 토큰을 추가할 수 있습니다.

## 특권 역할 및 행위자

- 게임 관리자(Game admin) - 프로토콜의 대부분을 제어하고 구성할 수 있습니다. Bear Cave 팀의 다중 서명(multi-sig)에 부여됩니다.
- 게임 인스턴스(Game instance) - `Gatekeeper`의 내부 청구된 `HoneyJar` 토큰 회계를 업데이트하고 토큰에 대한 게이트(gates)를 시작합니다. `HibernationDen` 계약에 부여됩니다.
- 포털(Portal) - L2에서 `startGame` 및 `setCrossChainFermentedJars`를 호출할 수 있습니다.
- 민터(Minter) - `HoneyJar` 토큰을 민팅할 수 있습니다. `HoneyBox` 및 `HoneyJarPortal` 계약 모두에 부여됩니다.
- 버너(Burner) - 소유자와 관계없이 모든 `HoneyJar` 토큰을 소각할 수 있습니다. 현재 누구에게도 부여되지 않았으며 `HibernationDen` 계약에 부여될 것으로 예상됩니다.
- 양봉가(Beekeeper) - `HoneyJar` NFT 민트 수수료의 일부를 받습니다.
- Jani - `HoneyJar` NFT 민트 수수료의 일부를 받습니다.
- 플레이어(Player) - 자격에 따라 무료 `HoneyJar` 토큰을 청구하거나 ERC20 토큰 또는 ETH로 `HoneyJar` 토큰을 민팅할 수 있습니다.
- Chainlink VRF - "특별한 Honey Jar" NFT가 선택되도록 무작위성을 제공합니다.

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

**_검토 커밋 해시_ - [406157655fdd94f90b4606773d3eacf1b9c4c725](https://github.com/0xHoneyJar/bear-cave/tree/406157655fdd94f90b4606773d3eacf1b9c4c725)**

**_수정 검토 커밋 해시_ - [90ea448845b743ad7dcd863a28008671530c64d5](https://github.com/0xHoneyJar/bear-cave/tree/90ea448845b743ad7dcd863a28008671530c64d5)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `HibernationDen`
- `HoneyJarPortal`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 3개 문제
- 중간: 3개 문제
- 낮음: 1개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | L2에서 상태가 올바르게 업데이트되지 않아 당첨 NFT가 묶임 | 치명적 |
| [C-02] | 누구나 `HoneyJarPortal`에서 모든 NFT를 소각할 수 있음 | 치명적 |
| [H-01] | 무작위성 요청 후 입력 수락은 악용될 수 있음 | 높음 |
| [M-01] | `fulfillRandomWords` 메서드는 가스 부족 오류로 되돌려질(revert) 수 있음 | 중간 |
| [M-02] | `HibernationDen` 계약은 ETH를 받을 수 있지만 인출할 수는 없음 | 중간 |
| [M-03] | 중앙 집중화 공격 벡터가 코드에 존재함 | 중간 |
| [L-01] | 강제되지 않은 체크포인트 간격 크기 | 낮음 |

# 상세 발견 사항

# [C-01] L2에서 상태가 올바르게 업데이트되지 않아 당첨 NFT가 묶임

## 심각도

**영향:**
높음, 우승 상품 NFT를 청구할 수 없기 때문

**가능성:**
높음, 기능이 올바르게 작동하지 않기 때문

## 설명

`HibernationDen`의 `addToParty` 메서드는 침목(sleepers, 우승 상품)을 L1의 `party.sleepoors` 배열로 푸시 하더라도 교차 체인 메시지를 보내지 않습니다. L2에서 배열이 업데이트되지 않으므로 `party.sleepoors.length` 값을 기반으로 수행되는 모든 계산 및 검증이 올바르지 않게 됩니다. 이는 본질적으로 `wakeSleeper`에 다음 확인이 있으므로 침목 NFT가 묶이게 됨을 의미합니다:

```solidity
if (party.numUsed == party.sleepoors.length) revert PartyAlreadyWoke(bundleId_);
```

즉, 실제로 침목이 10개(원래 7개였는데 3개가 추가됨)가 있더라도 `numUsed`가 7이면 더 이상 침목을 청구할 수 없습니다. `wakeSleeper` 호출은 `PartyAlreadyWoke`로 되돌려질 것입니다.

## 권장 사항

L2의 `party.sleeepoors` 배열에 침목을 추가하기 위해 교차 체인 메시지를 보내야 합니다.

# [C-02] 누구나 `HoneyJarPortal`에서 모든 NFT를 소각할 수 있음

## 심각도

**영향:**
높음, NFT를 더 이상 포털에서 검색할 수 없기 때문

**가능성:**
높음, 악용하기 위한 전제 조건이 필요하지 않기 때문

## 설명

`HoneyJarPortal`의 `_debitFrom` 메서드는 ID가 주어지면 모든 `HoneyJar` NFT를 소각할 수 있도록 합니다. 이 메서드는 `_send`를 호출하는 `ONFT721Core`의 `sendFrom` 메서드를 통해 자유롭게 호출할 수 있으며, `_send`는 액세스 제어 확인 없이 `_debitFrom` 메서드를 호출합니다. 결과적으로 누가 소유하고 있든 상관없이 누구나 모든 HoneyJar NFT를 소각할 수 있습니다. 메서드에 다음 확인이 있습니다:

```solidity
if (_from != _msgSender()) revert OwnerNotCaller();
```

이는 액세스 제어 의도를 보여주지만 소각 과정에서 `_from` 인수가 사용되지 않으므로 실제로는 불필요합니다.

## 권장 사항

코드는 호출자가 실제로 소각하려는 NFT를 소유하고 있는지 확인해야 합니다. 현재 확인은 이를 수행하지 않습니다. 그에 따라 업데이트하십시오.

# [H-01] 무작위성 요청 후 입력 수락은 악용될 수 있음

## 심각도

**영향:**
높음, 예상되는 게임 승자에 대한 그리핑(griefing) 공격을 초래할 수 있기 때문

**가능성:**
중간, 새로운 HoneyJar NFT를 민팅해야 하기 때문

## 설명

[VRF 보안 고려 사항 문서](https://docs.chain.link/vrf/v2/security#dont-accept-bidsbetsinputs-after-you-have-made-a-randomness-request)는 계약의 결과가 사용자 제공 입력(이 경우 HoneyJar NFT 민팅)과 무작위성에 의존하는 경우, 무작위성 요청을 제출한 후에는 추가 사용자 제공 입력을 수락하지 않아야 한다고 명시적으로 언급합니다. 여기서 문제는 `fulfillRandomWords`에서 `_setFermentedJars` 메서드가 호출되는데, 여기서 번들에 대해 민팅된 HoneyJar NFT의 수가 우승 NFT를 선택하는 과정에 사용된다는 것입니다. 즉, `fulfillRandomWords` 트랜잭션은 HoneyJar NFT 민트로 프런트 러닝(front-run)될 수 있으며 승자가 변경될 수 있습니다. 이는 게임의 예상 승자에 대한 그리핑 공격을 초래할 수 있습니다.

## 권장 사항

무작위성 요청과 사용자 입력을 서로 분리하십시오. 무작위성 요청 전에 제출된 사용자 입력만 사용하십시오.

# [M-01] `fulfillRandomWords` 메서드는 가스 부족 오류로 되돌려질(revert) 수 있음

## 심각도

**영향:**
높음, 무작위성이 이행되지 않기 때문

**가능성:**
낮음, 가스 잘못된 구성이 필요하기 때문

## 설명

`HibernationDen`의 `fulfillRandomWords` 메서드는 `fermentedJars` 배열을 반복하고 외부 호출도 수행하는 내부 `_setFermentedJars` 메서드를 호출합니다. 이 코드는 많은 가스를 필요로 하고 `fulfillRandomWords` 메서드를 되돌릴 수 있으므로 잠재적인 문제입니다. 이는 VRF 통합에 문제가 됩니다 ([VRF 보안 고려 사항 문서](https://docs.chain.link/vrf/v2/security#fulfillrandomwords-must-not-revert)에 나열됨).

메서드의 또 다른 문제는 다음 코드입니다:

```solidity
if (party.assetChainId != getChainId() && address(honeyJarPortal) != address(0) && address(this).balance != 0) {
    uint256 sendAmount = address(this).balance / party.checkpoints.length;
    honeyJarPortal.sendFermentedJars{value: sendAmount}(
        address(this), party.assetChainId, party.bundleId, fermentedJars
    );
}
```

문제는 `party.assetChainId != getChainId() && address(honeyJarPortal) != address(0)`가 참일 때 `if` 문으로 들어가는 유일한 조건이 `address(this).balance != 0`이라는 것입니다. 누구나 계약에 1 wei의 ETH를 보낼 수 있어 표현식을 `true`로 평가되게 만들 수 있으므로 쉽게 악용될 수 있습니다. 이는 더 많은 로직이 있는 외부 호출을 실행하므로 추가 가스 비용 오버헤드를 추가하고, 또한 `sendAmount`가 0으로 내림될 수 있으므로(`address(this).balance`는 1이지만 `party.checkpoints.length`가 1보다 큰 경우) 교차 체인 호출이 거의 확실하게 실패할 것입니다.

## 권장 사항

수신된 무작위성을 캐싱한 다음, 예를 들어 외부 소유 계정(EOA)이 실제로 `_setFermentedJars` 호출을 하여 올바른 가스를 설정할 수 있도록 하는 것을 고려하십시오. 또한 잔액이 0이 아닌지 확인하는 것뿐만 아니라 `sendFermentedJars` 호출을 수행하기에 충분한지 확인하십시오.

# [M-02] `HibernationDen` 계약은 ETH를 받을 수 있지만 인출할 수는 없음

## 심각도

**영향:**
중간, 자금이 묶이게 되지만 가스 환불 가치만큼 일 것이기 때문

**가능성:**
중간, 교차 체인 호출에서 환불이 있을 때 발생할 것이기 때문

## 설명

`HibernationDen` 계약에는 `receive` 메서드가 있습니다. 메서드 위의 주석에서 알 수 있듯이 이는 주로 `LayerZero` 환불에 사용될 것으로 예상됩니다. 문제는 계약에 ETH 인출 메서드가 없기 때문에 이 가스 환불 ETH를 인출할 수 없다는 것입니다. 또 다른 문제는 누구나 실수로 ETH를 `HibernationDen`으로 보낼 수 있으며 거기에 묶이게 된다는 것입니다.

## 권장 사항

`HibernationDen` 계약에서 ETH를 인출할 수 있는 메서드를 추가하십시오.

# [M-03] 중앙 집중화 공격 벡터가 코드에 존재함

## 심각도

**영향:**
높음, 일부 계정이 게임을 망가뜨릴 수 있기 때문

**가능성:**
낮음, 악의적이거나 침해된 소유자/관리자 계정이 필요하기 때문

## 설명

계약에 여러 중앙 집중화 공격 벡터가 존재합니다. 예:

- `HibernationDen::setVRFConfig` - 임의의 값으로 업데이트하면 게임이 중단될 수 있음
- `HoneyJarPortal::setHibernationDen` - 임의의 주소로 업데이트하면 게임에 DoS 발생
- `HoneyJarPortal::setAdapterParams` - 너무 낮은 `gasLimit` 값을 사용하면 가스 부족으로 되돌아가게 됨

## 권장 사항

메서드를 한 번만 호출할 수 있게 하거나 생성자/초기화 메서드에 추가하십시오.

# [L-01] 강제되지 않은 체크포인트 간격 크기

`SlumberParty` 구조체의 `checkpoints` 필드에 대해 다음 주석이 있습니다:

> /// @dev the gap between checkpoints MUST be big enough so that a user can't mint through multiple checkpoints.

`checkpoints` 값이 설정되는 곳(`HibernationDen::addBundle`)에서 실제로 이를 강제하지 않으므로 이는 단지 가정일 뿐입니다. 이것이 실제로 심각한 문제로 이어질 수는 없지만, 주석을 제거하고 실제 확인 및 검증을 코드에 구현하여 설정된 체크포인트 간격이 너무 작지 않도록 하는 것이 가장 좋습니다.
