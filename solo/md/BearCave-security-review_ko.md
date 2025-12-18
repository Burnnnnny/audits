# 소개

**Bear Cave** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Bear Cave** 소개

Bear Cave는 온체인에서 여러 게임을 호스팅 하는 NFT 프로토콜입니다. 각각의 다른 곰(곰 컬렉션의 NFT 토큰)은 프로토콜에 들어갈 수 있는 일련의 "게이트(gates)"를 가지고 있습니다.

다음은 프로토콜의 핵심 계약입니다:

- `Gatekeeper` - 다양한 게이트를 관리합니다.
- `GameRegistry` - 게임 단계와 권한을 관리합니다.
- `HoneyJar` - 간단한 ERC721 호환 NFT 계약입니다.
- `HoneyBox` - `HoneyJar` NFT를 민팅하고 번들(ERC721/ERC1155 컬렉션 그룹)을 관리합니다.
- `HoneyJarPortal` - `HoneyJar` NFT와의 교차 체인 상호 작용을 허용합니다.

[더 많은 문서](https://docs.google.com/document/d/1xqh47Q9cW6qZqp2euInVsxH4MHmmZcKx3JsC1O1QnYI)

## 관찰 사항

플레이어가 `HoneyBox`를 통해 `HoneyJar` 토큰을 민팅할 수 있는 5가지 메커니즘이 있습니다:

1. 무료 민트 화이트리스트에 포함된 경우 무료로 청구
2. 조기 유료 민트 화이트리스트에 포함된 경우 `paymentToken`으로 조기 유료 민트
3. 조기 유료 민트 화이트리스트에 포함된 경우 ETH로 조기 유료 민트
4. `paymentToken`으로 공개 유료 민트
5. ETH로 공개 유료 민트

`mintConfig.maxHoneyJar` 금액이 민팅된 후 `_findHoneyJar` 메서드가 호출되어 VRF 요청을 수행하고 "특별한 Honey Jar"를 선택합니다. 해당 소유자는 특정 번들의 `sleepoor` NFT를 청구할 수 있습니다.

# 위협 모델

## 특권 역할 및 행위자

### 특권 역할

- 게임 관리자(Game admin) - 프로토콜의 대부분을 제어하고 구성할 수 있습니다. Bear Cave 팀의 다중 서명(multi-sig)에 부여됩니다.
- 게임 인스턴스(Game instance) - `Gatekeeper`의 내부 청구된 `HoneyJar` 토큰 회계를 업데이트하고 토큰에 대한 게이트를 시작합니다. `HoneyBox` 계약에 부여됩니다.
- 민터(Minter) - `HoneyJar` 토큰을 민팅할 수 있습니다. `HoneyBox` 및 `HoneyJarPortal` 계약 모두에 부여됩니다.
- 버너(Burner) - 소유자와 관계없이 모든 `HoneyJar` 토큰을 소각할 수 있습니다. 현재 누구에게도 부여되지 않았으며 `HoneyBox` 계약에 부여될 것으로 예상됩니다.

게임 관리자는 모든 주소에 `GAME_INSTANCE` 및 `MINTER` 역할을 부여할 수 있습니다.

### 행위자

- 양봉가(Beekeeper) - `HoneyJar` NFT 민트 수수료의 일부를 받습니다.
- Jani - `HoneyJar` NFT 민트 수수료의 일부를 받습니다.
- 플레이어(Player) - 자격에 따라 무료 `HoneyJar` 토큰을 청구하거나 ERC20 토큰 또는 ETH로 `HoneyJar` 토큰을 민팅할 수 있습니다.
- VRF - "특별한 Honey Jar" NFT가 선택되도록 무작위성을 제공합니다.

## 보안 인터뷰

**Q:** 프로토콜에서 시장 가치가 있는 것은 무엇인가요?

**A:** `HoneyBox` 계약에 예치된 NFT입니다. 또한 "특별한" `HoneyJar` NFT는 적어도 `HoneyBox` 계약에 저장된 모든 NFT의 가치가 있습니다. `HoneyJar` NFT는 민팅하는 데 ERC20 토큰이나 ETH가 들기 때문에 가치가 있습니다.

**Q:** 프로토콜/사용자가 돈을 잃을 수 있는 경우는 언제인가요?

**A:** 게임이 끝나지 않거나 공격자가 민팅 역학(mechanics) 또는 "특별한" `HoneyJar` VRF 로직을 악용하는 경우입니다.

**Q:** 공격자가 목표를 달성할 수 있는 방법에는 어떤 것이 있나요?

**A:** 언더플로/오버플로에 의해 메서드의 로직을 되돌리거나(revert) 외부 호출을 강제로 되돌리게 만드는 것입니다. 또한 VRF 통합이나 민팅 역학을 악용합니다.

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

**_검토 커밋 해시_ - [6c098a53649c2cf08afc806be37f9d50835a5252](https://github.com/0xHoneyJar/bear-cave/tree/6c098a53649c2cf08afc806be37f9d50835a5252)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Constants`
- `GameRegistry`
- `GameRegistryConsumer`
- `GateKeeper`
- `HoneyBox`
- `HoneyJar`
- `HoneyJarPortal`
- `IHoneyJar`

심각도 별로 분류된 다음 수의 문제가 발견되었습니다:

- 치명적 & 높음: 6개 문제
- 중간: 2개 문제
- 낮음: 4개 문제
- 정보성: 6개 문제

---

# 발견 사항 요약

| ID | 제목 | 심각도 |
| --- | --- | --- |
| [C-01] | 누구든지 `HoneyJarPortal`에서 모든 `HoneyJar` NFT를 훔치고 승인을 악용할 수 있음 | 치명적 |
| [C-02] | 재진입(Reentrancy)을 통해 하나의 무료 `HoneyJar` 민트가 허용된 사용자라도 무료로 최대 공급량을 민팅할 수 있음 | 치명적 |
| [C-03] | 공개 민트가 시작되기도 전에 누구든지 공개 민트를 통해 모든 NFT를 민팅할 수 있음 | 치명적 |
| [C-04] | VRF에서 무작위성을 다시 요청하는 것은 보안 안티 패턴임 | 치명적 |
| [H-01] | 민트 함수 `mekHoneyJarWithETH`는 매번 되돌려짐(revert) | 높음 |
| [H-02] | 관리자 계정은 프로토콜에서 많은 권한을 가지고 있으며 사용자의 보상을 거부/훔칠 수 있는 여러 방법이 있음 | 높음 |
| [M-01] | 잠재적인 오버플로로 인해 `HoneyBox`의 로직이 깨질 수 있음 | 중간 |
| [M-02] | `Gatekeeper`의 게이트 재설정 로직에 여러 결함이 있음 | 중간 |
| [L-01] | `BURNER` 역할과 `burn` 메서드는 사용할 수 없음 | 낮음 |
| [L-02] | 구현과 문서 간의 불일치 | 낮음 |
| [L-03] | Checks-Effects-Interactions 패턴을 따르지 않음 | 낮음 |
| [L-04] | 여러 `Gatekeeper` 메서드의 불충분한 검증 | 낮음 |
| [I-01] | 불완전한 NatSpecs | 정보성 |
| [I-02] | 사용되지 않거나 불필요한 코드를 제거할 수 있음 | 정보성 |
| [I-03] | 코드의 오타 및 문법 오류 | 정보성 |
| [I-04] | 유형(types)의 전체 이름 사용 | 정보성 |
| [I-05] | `override` 키워드 누락 | 정보성 |
| [I-06] | 상태 변경 메서드에서 이벤트 방출 누락 | 정보성 |

# 상세 발견 사항

# [C-01] 누구든지 `HoneyJarPortal`에서 모든 `HoneyJar` NFT를 훔치고 승인을 악용할 수 있음

## 심각도

**영향:**
높음, 사용자에게 가치 손실이 발생하기 때문

**가능성:**
높음, 일반적인 취약점이며 전제 조건이 필요하지 않기 때문

## 설명

`HoneyJarPortal`의 `_debitFrom` 함수는 다음과 같이 생겼으므로 악용될 수 있습니다:

```solidity
function _debitFrom(address _from, uint16, bytes memory, uint _tokenId) internal override {
    honeyJar.safeTransferFrom(_from, address(this), _tokenId); // Performs the owner & approval checks
}
```

`_from` 인수에 대한 확인이 없으므로 누구나 함수를 호출하고( `ONFT721Core`의 `sendFrom` 메서드를 통해) `HoneyJarPortal`의 주소를 `_from` 인수로 전달하고 자신의 주소를 `sendFrom` 메서드의 `_toAddress` 인수로 전달하여 본질적으로 `HoneyJarPortal`이 소유한 모든 NFT를 훔칠 수 있습니다. 또한 `safeTransferFrom` 메서드가 성공적으로 완료되기 때문에 `HoneyJarPortal`이 소유하지는 않지만 승인된 지출자(approved spender)인 NFT를 훔칠 수도 있습니다.

## 권장 사항

`_debitFrom`에서 `_tokenId` NFT의 소유자가 `msg.sender`인지 확인하십시오.

# [C-02] 재진입(Reentrancy)을 통해 하나의 무료 `HoneyJar` 민트가 허용된 사용자라도 무료로 최대 공급량을 민팅할 수 있음

## 심각도

**영향:**
높음, 사용자가 아무것도 지불하지 않고 모든 `HoneyJar` NFT를 훔칠 것이기 때문

**가능성:**
높음, 재진입은 매우 일반적인 공격 벡터이며 쉽게 악용될 수 있기 때문

## 설명

`HoneyBox`의 `claim` 메서드는 (NatSpec에 따르면) "플레이어가 자격에 따라 무료 HoneyJar를 청구할 수 있도록 허용"합니다. 코드의 이 부분을 살펴보겠습니다:

```solidity
_canMintHoneyJar(bundleId_, numClaim); // Validating here because numClaims can change

// If for some reason this fails, GG no honeyJar for you
_mintHoneyJarForBear(msg.sender, bundleId_, numClaim);

claimed[bundleId_] += numClaim;
// Can be combined with "claim" call above, but keeping separate to separate view + modification on gatekeeper
gatekeeper.addClaimed(bundleId_, gateId, numClaim, proof);
```

여기서 실제로 민팅을 수행한 후 `Gatekeeper` 계약에서 `claimed` 매핑을 업데이트하고 청구에 대한 회계 처리를 합니다. 문제는 `_mintHoneyJarForBear` 메서드가 `honeyJar::batchMint`를 호출하고, 이는 `safeMint`를 사용하는데, 이것이 민트 수신자에게 안전하지 않은 외부 호출을 수행한다는 것입니다. 이 호출은 `claimed` 회계 처리가 아직 완료되지 않은 상태에서 `claim` 메서드로 재진입할 수 있으며 실제로 `mintConfig.maxHoneyJar`에 도달할 때까지 모든 `HoneyJar` NFT를 청구할 수 있습니다. 이렇게 되면 그가 게임의 승자가 될 가능성이 매우 높으므로 게임의 모든 NFT도 무료로 얻게 됩니다.

설상가상으로 `claim` 메서드는 `gateId` 인수를 허용하고 게이트 자체에는 `maxClaimable` 속성이 있기 때문에 보호 기능이 있지만, `gatekeeper::addClaimed` 호출도 안전하지 않은 외부 호출 이후에 수행되므로 여기에서도 여러 불변성이 깨집니다.

## 권장 사항

`claim` 메서드가 Checks-Effects-Interactions 패턴을 따르도록 하거나 `nonReentrant` 수정자를 추가하십시오.

# [C-03] 공개 민트가 시작되기도 전에 누구든지 공개 민트를 통해 모든 NFT를 민팅할 수 있음

## 심각도

**영향:**
높음, 중요한 프로토콜 불변성과 프로토콜이 전반적으로 작동하는 방식을 깨뜨리기 때문

**가능성:**
높음, 전제 조건이 필요하지 않으며 `HoneyBox` 배포 시 쉽게 실행할 수 있기 때문

## 설명

`mekHoneyJarWithERC20` 및 `mekHoneyJarWithETH` 메서드는 모두 플레이어가 `HoneyJar` NFT를 민팅할 수 있는 방법이지만, 두 메서드 모두에 존재하는 이 확인에서 알 수 있듯이 일반 민트가 열려 있을 때만 작동해야 합니다:

```solidity
if (slumberParties[bundleId_].publicMintTime > block.timestamp) revert GeneralMintNotOpen(bundleId_);
```

문제는 첫 번째 번들이 추가되기 전에 누구나 언제든지 두 메서드를 호출할 수 있다는 것입니다. 번들이 없는 경우 사용자가 두 메서드 중 하나에 `bundleId_ == 0`을 제공하면 `slumberParties[bundleId_]` 매핑의 모든 값이 기본값을 갖게 되어 메서드 및 `_canMintHoneyJar` 메서드의 모든 확인을 통과합니다. 이는 본질적으로 누구나 게임을 프런트 러닝하고 `mintConfig`에 구성된 최대 사용 가능한 `HoneyJar`를 민팅할 수 있음을 의미합니다.

## 권장 사항

`_canMintHoneyJar`에서 `slumberParties[bundleId_].publicMintTime == 0`인 경우 되돌리십시오(revert). 이는 이 번들이 아직 초기화되지 않았음을 의미합니다. 이것은 이 공격 벡터와 다른 `bundleId == 0` 공격도 다룰 것입니다.

# [C-04] VRF에서 무작위성을 다시 요청하는 것은 보안 안티 패턴임

## 심각도

**영향:**
높음, VRF 서비스 제공자가 누가 게임에서 이길지 제어할 수 있기 때문

**가능성:**
높음, VRF 제공자가 이를 악용할 유인이 있고 그들 입장에서 하기 어렵지 않기 때문

## 설명

`forceHoneyJarSearch` 메서드는 NatSpec에서 언급했듯이 "또 다른 VRF 요청을 시작"하는 데 사용됩니다. 이는 [문서](https://docs.chain.link/vrf/v2/security#do-not-re-request-randomness)에 명시된 대로 VRF 사용 보안 표준에 위배됩니다:

```
Re-requesting randomness is easily detectable on-chain and should be avoided for use cases that want to be considered as using VRFv2 correctly.
```

기본적으로 서비스 제공자는 자신에게 유리한 새로운 요청이 올 때까지 VRF 이행을 보류할 수 있습니다.

## 권장 사항

악용될 수 있으므로 `forceHoneyJarSearch` 메서드를 제거하십시오.

# [H-01] 민트 함수 `mekHoneyJarWithETH`는 매번 되돌려짐(revert)

## 심각도

**영향:**
중간, ERC20 토큰으로 민팅할 수 있는 옵션도 있기 때문

**가능성:**
높음, 함수가 매번 되돌려질 것이기 때문

## 설명

`HoneyBox` 계약은 `mekHoneyJarWithETH` 메서드를 통해 공개 판매에서 `ETH`로 `HoneyJar` NFT를 민팅할 수 있는 방법을 사용자에게 제공합니다. 문제는 이름에서 알 수 있듯이 ETH로 지불했을 것으로 예상되는 가격을 계산하기 위해 `msg.value`를 사용하지만, 메서드에 `payable` 키워드가 누락되었다는 것입니다. `msg.value != 0`인 모든 호출은 되돌려집니다.

## 권장 사항

```diff
-    function mekHoneyJarWithETH(uint8 bundleId_, uint256 amount_) external returns (uint256) {
+    function mekHoneyJarWithETH(uint8 bundleId_, uint256 amount_) external payable returns (uint256) {
```

# [H-02] 관리자 계정은 프로토콜에서 많은 권한을 가지고 있으며 사용자의 보상을 거부/훔칠 수 있는 여러 방법이 있음

## 심각도

**영향:**
높음, 관리자가 사용자(플레이어)의 자금을 훔칠 수 있기 때문

**가능성:**
중간, 악의적이거나 침해된 관리자가 필요하지만 인센티브가 높기 때문

## 설명

프로토콜에 여러 중앙 집중화 결함 및 공격 벡터가 있습니다:

- 게임 관리자는 `forceHoneyJarSearch` 호출로 프런트 러닝하여 `openHotBox`를 되돌리게(revert) 만들어 본질적으로 게임 승자를 "러그(rug)"할 수 있습니다.
- 게임 관리자는 `setVRFConfig`를 호출하고 개인적으로 제어되는 VRF 인터페이스 호환 계약을 사용하고 무작위가 아닌 숫자를 사용하여 본질적으로 게임의 결과를 결정할 수 있습니다.
- `setMaxhoneyJar` 메서드는 게임이 절대 끝나지 않도록 하는 데 사용될 수 있습니다. `isEnabled` 확인이 있고 `인질을 잡는 것을 방지하기 위해 게임이 진행되는 동안 호출해서는 안 된다`는 주석이 있지만 이는 사실이 아닙니다. `setMaxhoneyJar`를 호출할 수 있는 동일한 주소(`GAME_ADMIN` 역할)가 `enabled` 플래그를 설정하는 `startGame` 및 `stopGame`을 호출할 수 있으므로, 관리자는 최대값을 엄청난 숫자로 설정하여 본질적으로 게임을 DoS 상태로 만들 수 있습니다.
- 게임 관리자는 게임의 시작 또는 중지 시점을 제어하므로 `setHoneyJarPrice_ERC20` 호출로 사용자를 프런트 러닝하고 무제한 승인을 한 경우 더 많은 비용을 지불하게 하여 본질적으로 토큰을 훔칠 수도 있습니다.

## 권장 사항

러그 풀(rug pulls)로 사용될 수 있는 모든 메서드를 재설계하고 가능하면 프로토콜의 관리자를 Timelock 계약으로 만드십시오.

# [M-01] 잠재적인 오버플로로 인해 `HoneyBox`의 로직이 깨질 수 있음

## 심각도

**영향:**
높음, 번들 저장소 변수가 덮어씌워질 것이기 때문

**가능성:**
낮음, 255개 이상의 번들을 추가할 것으로 예상되지 않기 때문

## 설명

`HoneyBox::addBundle`에는 다음 코드가 있습니다:

```solidity
        uint8 bundleId = uint8(slumberPartyList.length); // Will fail if we have >255 bundles
```

이 주석은 틀렸는데, 캐스트가 안전하고 `slumberPartyList.length > 255`의 경우 되돌릴 것이라고 가정하지만, 실제로는 오버플로가 발생하기 때문입니다. 이는 `slumberParties` 매핑에서 이미 존재하는 `bundleId` 값을 덮어쓰게 되어 계약의 로직이 깨지므로 큰 문제가 될 것입니다.

## 권장 사항

`SafeCast` 라이브러리를 사용하거나 `slumberPartyList.length > 255`인 경우 되돌리십시오(revert).

# [M-02] `Gatekeeper`의 게이트 재설정 로직에 여러 결함이 있음

## 심각도

**영향:**
중간, 가치가 손실되지는 않지만 계약 상태가 올바르지 않기 때문

**가능성:**
중간, 매번 발생할 것으로 예상되지는 않지만 여기에 여러 공격 경로가 있기 때문

## 설명

`resetAllGates` 메서드는 바운드되지 않은(unbounded) 배열을 반복합니다. `tokenToGates[tokenId]` 및 `consumedProofsList[gateId]` 배열 모두 바운드되지 않았습니다. 배열을 반복하는 데 너무 많은 가스(블록 가스 제한 초과)가 소요될 수 있으므로 `resetAllGates` 메서드에 대해 DoS 상태가 발생할 수 있습니다.

메서드의 더 큰 문제는 `tokenToGates[tokenId]`에 대해 `delete`를 수행하지 않는다는 것입니다. `claimedCount`를 0으로 설정하더라도 예를 들어 `claimed`를 `false`로 설정하지 않으므로, 이를 확인하는 메서드는 여전히 `claimed == true`라고 생각할 것입니다(예: `validateProof`가 이를 확인합니다).

## 권장 사항

`addGate` 및 `addClaimed` 메서드에서 각각 `tokenToGates[tokenId]` 및 `consumedProofsList[gateId]` 배열 크기에 대한 상한을 추가하십시오. `resetAllGates`의 첫 번째 `for` 루프에서 `tokenGates[i]`에 대해 `delete`를 호출하고 `resetAllGates` 메서드에서 각 재설정 게이트에 대해 `GateReset` 이벤트를 방출하십시오.

# [L-01] `BURNER` 역할과 `burn` 메서드는 사용할 수 없음

`burn` 메서드는 어디에서도 호출되지 않으며, 현재 누구에게도 `BURNER` 역할을 부여할 수 있는 옵션이 없습니다. 팀과 논의한 바와 같이, 이는 가까운 미래에 코드가 변경될 것임을 의미합니다. 이는 위험을 내포하고 있으며 `BURNER` 역할 및 `burn` 기능을 제거하거나 포함된 새 코드로 새 감사를 받는 것을 권장합니다.

# [L-02] 구현과 문서 간의 불일치

`registerGame`의 NatSpec 문서는 메서드가 게임을 "활성화(enables)"한다고 말하지만 `games` 매핑은 해당 게임에 대해 실제로 `true`로 설정되지 않습니다(이는 `startGame`에서 수행됨). 이는 오해의 소지가 있으며 오류로 이어질 수 있습니다. 그에 따라 구현 또는 NatSpec을 업데이트하십시오.

# [L-03] Checks-Effects-Interactions 패턴을 따르지 않음

`HoneyBox`의 `puffPuffPassOut` 및 `openHotBox` 메서드는 CEI 패턴을 따르지 않습니다. 현재 재진입 취약점은 없지만 시간이 지남에 따라 코드가 변경될 수 있으므로 보안을 위해 CEI 패턴을 따르는 것이 좋습니다.

# [L-04] 여러 `Gatekeeper` 메서드의 불충분한 검증

`addClaimed` 메서드에는 `validateProof`에 존재하는 `gate.enabled` 및 `gate.activeAt` 확인이 누락되어 있으므로 추가해야 합니다. 또한 완전한 정확성을 위해 `consumedProofs` 매핑이 이미 `true`로 설정되어 있으면 되돌려야 합니다.

`addGate` 메서드는 `tokenId`가 존재하는지 확인해야 하며 `maxClaimable_` 인수에는 상한이 있어야 합니다.

`startGatesForToken`은 게이트가 이미 활성화되어 있는지 확인하고 활성화된 경우 되돌려야 합니다. 현재 구현은 `tokenId`에 대한 게이트의 "재시작"을 허용하기 때문입니다.

# [I-01] 불완전한 NatSpecs

코드베이스의 대부분의 `external` 메서드에는 NatSpec 문서가 있지만 `@param` 및 `@return` 설명이 거의 항상 부족합니다. 간단한 예는 `HoneyBoxx::addBundle`입니다. 매개변수나 반환 변수 모두 NatSpec에 나열되어 있지 않습니다. 모든 메서드를 살펴보고 완전하고 적절한 문서가 있는지 확인하십시오.

# [I-02] 사용되지 않거나 불필요한 코드를 제거할 수 있음

`Constants`의 `GATEKEEPER` 상수는 어디에서도 사용되지 않으므로 제거해야 합니다.

`HoneyBox`의 `initialize` 메서드는 업그레이드 가능하지 않고 프록시 기능을 구현하지 않으므로 필요하지 않습니다. 로직을 계약의 생성자로 이동하십시오. 또한 `NotInitialized` 및 `AlreadyInitialized` 오류, `Initialized` 이벤트 및 `initialized` 저장소 변수를 제거하십시오.

`Gatekeeper`의 다음 import는 사용되지 않으므로 제거해야 합니다:

- `import {ERC1155} from "solmate/tokens/ERC1155.sol";`
- `import {ERC20} from "solmate/tokens/ERC20.sol";`
- `import {ERC721} from "solmate/tokens/ERC721.sol";`

`BEEKEEPER` & `JANI` 역할과 해당 설정자 및 상수는 사용되지 않으며 필요하지 않으므로 제거할 수 있습니다.

`Gatekeeper`의 `games` 저장소 매핑은 어디에서도 사용되지 않으므로 제거해야 합니다.

`// Switching to OZ for LZ compatibility` 주석은 필요하지 않습니다. 어차피 이전 코드는 버전 제어 시스템에 보관되기 때문입니다.

`earlyMekHoneyJarWithERC20`의 `if (mintAmount == 0) revert ZeroMint();` 확인은 제거할 수 있습니다. `earlyMekHoneyJarWithERC20`에 의해 호출되는 `_canMintHoneyJar`에 확인이 존재하기 때문입니다.

`HoneyBox`의 `honeyJarToParty` 매핑은 기록만 되고 온체인에서 읽히지 않습니다. 이는 가스를 낭비하므로 제거해야 합니다.

불필요한 주석 - `// Save to mapping`. 가치를 더하지 않으므로 제거할 수 있습니다.

`HoneyBox::claimAll`의 `if (proof[i].length == 0) continue; // Don't nomad yourself` 확인은 제거하십시오. 그 후에 `claim`을 호출하고 `claim`에 `if (proof.length == 0) revert Claim_InvalidProof();`가 있기 때문입니다.

# [I-03] 코드의 오타 및 문법 오류

코드베이스 전체에 여러 오타와 문법 오류가 있습니다.

- `elegibility` -> `eligibility`

- `logic is affects` -> `logic is affected`

- `you a player wants to claim` -> `a player wants to claim`

- `mintor` -> `minter`

- `All game contracts should use extend` -> `All game contracts should extend`

- `all free cams` -> `all free claims`

# [I-04] 유형(types)의 전체 이름 사용

`HoneyJarPortal`에서 볼 수 있듯이 코드베이스의 모든 곳에서 `uint`를 `uint256`으로 바꾸십시오. `uint`만 사용하면 미묘한 버그가 발생할 수 있습니다. 예를 들어 메서드의 서명을 해싱하고 매개변수 유형에 `uint256` 대신 `uint`를 사용하는 경우입니다.

# [I-05] `override` 키워드 누락

`HoneyJar` 계약은 `IHoneyJar` 인터페이스를 구현하지만 여러 곳에서 `override` 키워드가 부족합니다. 키워드의 컴파일러 확인을 사용하려면 `mintTokenId`, `batchMint` 및 `burn` 메서드와 `nextTokenId` 저장소 변수에 추가하십시오.

# [I-06] 상태 변경 메서드에서 이벤트 방출 누락

오프체인 모니터링을 위해 모든 상태 변경 메서드에서 이벤트를 방출하는 것이 모범 사례입니다. `HoneyJar`의 `setBaseURI` 및 `setGenerated` 메서드에는 이벤트 방출이 누락되어 있으므로 추가해야 합니다.
