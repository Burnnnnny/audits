
# Pashov Audit Group 소개 (About Pashov Audit Group)

Pashov Audit Group은 업계에서 입증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트에서 10만 달러 이상의 상금을 획득했거나, 다회 우승자이거나, 우리와의 감사에서 진정으로 뛰어난 성과를 보여주었습니다. 우리는 검증되고 동기 부여된 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견 및 수정하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

<p>Pashov Audit Group에 의해 <strong>hytopiagg/hybux-staking-audit</strong> 저장소에 대한 시간 제한 보안 검토가 수행되었으며, <strong>h2134, 0xl33, 0xbepresent, imsrybr0</strong>이 참여하여 <strong>HYBUX</strong>를 검토했습니다. 총 <strong>12</strong>개의 문제가 발견되었습니다.</p>

# HYBUX 소개 (About HYBUX)

<p>Hybux는 NFT 기반 스테이킹, 풀링된 방출(pooled emissions) 및 토큰화된 보상 흐름을 관리하도록 설계된 스테이킹 및 보상 분배 시스템입니다.</p>

# 보안 평가 요약 (Security Assessment Summary)

**검토 커밋 해시:**<br>• [735e5a99d4797482b4a85dc355dc8817ea2184ca](https://github.com/hytopiagg/hybux-staking-audit/tree/735e5a99d4797482b4a85dc355dc8817ea2184ca)<br>&nbsp;&nbsp;(hytopiagg/hybux-staking-audit)

**수정 검토 커밋 해시:**<br>• [3834eff5b7ca36c1af577d9470d06ee65731b6bc](https://github.com/hytopiagg/hybux-staking-audit/tree/3834eff5b7ca36c1af577d9470d06ee65731b6bc)<br>&nbsp;&nbsp;(hytopiagg/hybux-staking-audit)

# 범위 (Scope)

- `HYBUX.sol`
- `IHYBUX.sol`
- `NFTStaking.sol`
- `NFTStakingRouter.sol`
- `NodeKeyPool.sol`

# 발견 사항 (Findings)

# [C-01] 잘못된 보상 계산

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`_unstakeNFTs` 내부 함수는 언스테이킹 작업을 처리할 때 `_claimRewards(_sender)` 대신 `claimRewards()`를 호출합니다. 이는 직접 언스테이킹(`msg.sender`가 사용자인 경우)의 경우에는 올바르게 작동하지만, 라우터를 통한 언스테이킹의 경우에는 `msg.sender`가 라우터 계약 주소가 되기 때문에 실패합니다.

```solidity
File: NFTStaking.sol
82:     function unstakeNFTsRouter(address _sender, uint256[] calldata _tokenIds) external {
83:         require(msg.sender == stakingRouterAddress, "Only router");
84: 
85:@>       _unstakeNFTs(_sender, _tokenIds);
86:     }
...
88:     function claimRewards() public {
89:@>       _claimRewards(msg.sender);
90:     }
...
182:     function _unstakeNFTs(address _sender, uint256[] calldata _tokenIds) internal {
183:@>     claimRewards();
```

사용자가 `unstakeNFTsRouter`를 통해 언스테이킹할 때, 함수는 사용자의 주소를 `_sender`로 `_unstakeNFTs`에 올바르게 전달합니다. 그러나 `_unstakeNFTs`는 `claimRewards()`를 호출하고, 이는 내부적으로 `_claimRewards(msg.sender)`를 호출합니다. `msg.sender`는 라우터 계약이므로 보상은 사용자 대신 라우터에 대해 계산되고 전송됩니다.

스테이킹 구현은 라우터 사용을 올바르게 예상하고 모든 스토리지 작업에 `_sender`를 일관되게 사용했습니다. 다음 시나리오를 고려해 보십시오:

1. 사용자 Alice가 7일 동안 NFT를 스테이킹하여 1000 HYBUX 보상을 축적했습니다.
2. Alice가 자신의 NFT 토큰 ID로 `StakingRouter.unstakeNFTs()`를 호출합니다.
3. 라우터가 `NFTStaking.unstakeNFTsRouter(aliceAddress, tokenIds)`를 호출합니다.
4. `unstakeNFTsRouter`는 호출자가 라우터인지 확인한 다음 `_unstakeNFTs(aliceAddress, tokenIds)`를 호출합니다.
5. `_unstakeNFTs`가 `claimRewards()`를 호출하지만 `msg.sender`는 라우터 계약입니다.
6. `_claimRewards(routerAddress)`는 Alice 대신 라우터에 대한 보상을 계산합니다.
7. Alice는 1000 HYBUX 보상을 잃습니다.

## 권장 사항

언스테이킹이 직접 발생하든 라우터를 통해 발생하든 관계없이 보상이 항상 올바른 사용자 주소에 대해 계산되도록 `NFTStaking._unstakeNFTs();`를 `_claimRewards(_sender);`로 변경하십시오.

# [H-01] 교차 계약 서명 재생(Replay)으로 인한 보상 부풀리기

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`NFTStaking._stakeNFTs()`의 서명 검증은 해시에 계약 주소를 포함하지 않아, 세 가지 다른 NFTStaking 계약(WORLDS_STAKING, GRAYBOYS_STAKING, AVATARS_STAKING) 간에 서명을 재생할 수 있습니다.

```solidity
bytes32 hash = keccak256(abi.encode(_sender, _tokenIds, _rarityWeightIndexes));
```

다른 NFT 컬렉션에서 동일한 토큰 ID를 소유한 공격자는 한 컬렉션에서 높은 희귀도 NFT에 대한 서명을 얻은 다음, 다른 컬렉션에서 동일한 토큰 ID를 가진 낮은 희귀도 NFT를 스테이킹할 때 해당 서명을 재생할 수 있습니다. 이로 인해 낮은 희귀도 NFT가 마치 높은 희귀도인 것처럼 보상을 받게 됩니다.

공격 예시:
1. 사용자가 Worlds(예: 전설, 가중치 100)와 Grayboys(예: 일반, 가중치 1) 컬렉션 모두에서 토큰 #100을 소유합니다.
2. 사용자가 유효한 서명을 얻어 Worlds 계약에 스테이킹합니다(합법적).
3. 사용자가 일반 NFT로 Grayboys 계약에서 동일한 서명을 재생합니다.
4. 일반 NFT가 이제 전설 배율로 보상을 받습니다.

## 권장 사항

교차 계약 재생을 방지하기 위해 서명 해시에 계약 주소를 포함하십시오:

```solidity
bytes32 hash = keccak256(abi.encode(_sender, _tokenIds, _rarityWeightIndexes, address(this)));
```

# [M-01] 함수 서명 불일치로 인한 DoS

_해결됨 (Resolved)_

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`NFTStakingRouter`의 `INFTStaking` 인터페이스는 단일 매개변수가 있는 `calculateRewards()`를 정의하지만, `NFTStaking`의 실제 구현은 다릅니다:

인터페이스:
```solidity
function calculateRewards(address user) external returns (uint256)
```

구현:
```solidity
function calculateRewards(address _user, bool _includePendingRewards) public view returns (uint256)
```

`NFTStakingRouter.claimRewardsRouter()`가 `calculateRewards()`를 호출할 때 함수 선택자(selector)가 일치하지 않아 호출이 되돌려집니다(revert). 이로 인해 사용자가 라우터를 통해 보상을 청구하려는 시도가 완전히 차단됩니다(DoS).

## 권장 사항

인터페이스와 라우터 호출을 구현과 일치하도록 업데이트하십시오:

```solidity
// In INFTStaking interface:
function calculateRewards(address user, bool includePendingRewards) public view returns (uint256);

// In NFTStakingRouter.claimRewardsRouter():
uint256 amount = INFTStaking(stakingAddr).calculateRewards(msg.sender, true);
```

실제 청구된 금액과 일치하도록 `_includePendingRewards`가 `true`로 설정되었는지 확인하십시오(방출된 이벤트와 관련됨).

# [M-02] `setRarityWeights()`의 티어 제거로 인한 활성 스테이크 무효화 가능성

_해결됨 (Resolved)_

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`setRarityWeights()` 함수는 계약 소유자가 `rarityWeights` 배열을 업데이트할 수 있도록 합니다. 그러나 이 함수에는 치명적인 결함이 있습니다. 새롭고 더 짧은 배열이 현재 활성 NFT 스테이크가 있는 희귀도 티어를 제거하는지 확인하지 않습니다.

[NFTStaking.sol#L100-L104](https://github.com/hytopiagg/hybux-staking-audit/blob/735e5a99d4797482b4a85dc355dc8817ea2184ca/src/staking/NFTStaking.sol#L100-L104):
```solidity
    function setRarityWeights(
        uint256[] calldata _rarityWeights
    ) public onlyOwner {
        rarityWeights = _rarityWeights;
    }
```

소유자가 `rarityWeights` 배열을 줄이는 트랜잭션을 제출하면, 이제 제거된 인덱스에 스테이킹된 모든 NFT는 트랜잭션이 채굴되는 시점과 언스테이킹되는 시점 사이에 사실상 동결됩니다. 새롭고 더 짧은 배열의 길이만큼만 반복하는 `getRarityWeightMultiplier()` 함수는 보상 계산에서 이러한 NFT를 제외합니다.

이는 해당 사용자가 NFT가 유효하지 않은 인덱스에 스테이킹되어 있는 동안 영구적인 보상 손실을 초래합니다. 소유자가 브로드캐스팅 전에 상태를 확인하고 제거될 티어에 스테이킹된 NFT가 없음을 확인하더라도, 사용자는 소유자의 트랜잭션이 브로드캐스트된 후 채굴되기 전에 완전히 정상적이고 합법적인 스테이킹 트랜잭션을 제출할 수 있습니다. 소유자의 트랜잭션이 실행되면 티어가 제거되어 새로 스테이킹된 NFT가 즉시 무효화됩니다.

## 권장 사항

제거될 티어에 스테이킹된 NFT가 있는 경우 `rarityWeights` 배열 길이를 줄이는 것을 방지하기 위해 `setRarityWeights()`에 온체인 검사를 추가하십시오.

# [L-01] 서명에 데드라인 및 논스 누락

_해결됨 (Resolved)_

`NFTStaking._stakeNFTs()`의 서명 해시에는 데드라인이나 논스 매개변수가 포함되어 있지 않습니다. 이는 몇 가지 문제를 야기합니다:

- NFT의 희귀도가 하향 조정되는 경우(오프체인 로직 변경으로 인해 동일한 NFT가 다른 희귀도 가중치 인덱스에 해당하는 경우), 사용자는 더 높은 희귀도 인덱스를 가진 이전 서명을 계속 사용할 수 있습니다. 예를 들어, 메타데이터 업데이트나 희귀도 재조정으로 인해 NFT #100이 전설(인덱스 3)에서 일반(인덱스 0)으로 변경되는 경우, 이전 서명을 보유한 사용자는 여전히 인덱스 3으로 스테이킹하여 전설 배율로 무기한 보상을 받을 수 있습니다.

- 서명은 영원히 유효합니다. 서명 권한자의 개인 키가 손상되거나 의도적으로 새 키로 교체되는 경우, 이전에 발행된 모든 서명은 계속 작동합니다. 소유자는 계약을 업그레이드하지 않고는 오래된 서명을 무효화할 방법이 없습니다.

- 논스 추적 없이 서명은 언스테이킹 후 재사용될 수 있습니다. 이는 의도적인 설계일 수 있지만, 서명 권한자가 잘못된 희귀도 값으로 실수로 발행된 서명을 수정하는 것을 방지합니다.

**권장 사항:** 만료 및 일회성 사용을 가능하게 하도록 서명 해시에 데드라인 타임스탬프와 논스를 포함하십시오. 재사용을 방지하기 위해 사용자별 논스 추적을 구현하고, 합리적인 기간 후 서명이 만료되도록 데드라인 검증을 추가하십시오.

# [L-02] ERC721 수신자 지원이 없는 스마트 계약 사용자의 NFT가 갇힐 수 있음

_인정됨 (Acknowledged)_

`NFTStaking`의 `_unstakeNFTs()` 함수는 NFT를 사용자에게 반환하기 위해 `safeTransferFrom()`을 사용합니다. 사용자가 `onERC721Received()` 함수를 구현하지 않는 스마트 계약인 경우, `ERC721Utils`의 `checkOnERC721Received()` 안전 검사로 인해 전송이 되돌려집니다. 이로 인해 언스테이킹 트랜잭션이 실패하여 계약이 업그레이드되지 않는 한 NFT가 스테이킹 계약에 영구적으로 갇히게 됩니다.

**권장 사항:** 언스테이킹 중 사용자에게 NFT를 반환할 때 안전 버전(safe version) 대신 일반 전송(transfer)을 사용하십시오. 또는 스테이킹 허용 전에 스마트 계약 사용자(`code.length > 0`)가 ERC721 수신자 인터페이스를 지원하는지 확인하는 검사를 구현하십시오.

# [L-03] `NFTStaking`의 스토리지 갭 누락으로 인한 업그레이드 시 스토리지 손상 가능성

_해결됨 (Resolved)_

`NFTStaking`의 `getStakedNFTs()` 함수는 `virtual`로 표시되어 있어 계약이 상속을 위해 설계되었을 수 있음을 나타냅니다. 그러나 계약에는 스토리지 갭(`uint256[50] private __gap`)이 없습니다. `NFTStaking`이 다른 계약에 의해 상속되고 나중에 새로운 상태 변수를 추가하도록 업그레이드되는 경우, 해당 변수는 자식 계약이 사용하는 스토리지 슬롯을 덮어써 스토리지 손상을 일으킬 수 있습니다.

**권장 사항:** 기존 상태 변수 뒤에 스토리지 갭 변수(`uint256[50] private __gap`)를 추가하거나, 상속이 의도되지 않은 경우 `getStakedNFTs()`에서 `virtual` 키워드를 제거하십시오.

# [L-04] 글로벌 보상 풀 설계로 인한 경쟁 상태(Race Condition)

_인정됨 (Acknowledged)_

`NodeKeyPool`은 단일 글로벌 `lastClaimTimestamp`를 사용하며 누적된 모든 보상을 `burnAndClaim()`을 호출하는 첫 번째 사용자에게 분배합니다. 이는 다음과 같은 승자 독식 경쟁 상태를 만듭니다:

- 보상은 시간이 지남에 따라 단일 풀에 전역적으로 축적됩니다.
- 첫 번째 호출자가 누적된 모든 보상을 받습니다.
- 후속 호출자는 마지막 청구 이후 누적된 보상만 받습니다.
- 사용자 기여도나 소유권에 따른 비례 배분이 없습니다.

예시 시나리오:
- `secondsPassed` = 100,000
- `rate` = 1e18 per second
- `accumulated` = 100,000 * 1e18 = 100,000e18 tokens
- 2명의 사용자가 각각 1개의 NodeKey NFT 소유

사용자 A가 `burnAndClaim()` 호출:
- `calculateAccumulated()` = 100,000e18 tokens
- 100,000e18 토큰 획득
- `lastClaimTimestamp` = `block.timestamp`

사용자 B가 10초 후 `burnAndClaim()` 호출:
- `calculateAccumulated()` = 10 * 1e18 = 10e18 tokens
- 10e18 토큰만 획득

이 설계는 능력 기반 시스템보다는 타이밍이 보상을 결정하는 불공정한 분배를 만듭니다.

**권장 사항:** 사용자별 추적 또는 비례 배분을 사용하도록 보상 메커니즘을 재설계하십시오. 개별 사용자 기여를 추적하거나 첫 번째 청구자를 선호하지 않는 공정한 분배 메커니즘을 구현하는 것을 고려하십시오.

# [L-05] `tokenToRarityIndex` 매핑의 오래된 데이터

_인정됨 (Acknowledged)_

NFT가 언스테이킹될 때 `tokenToRarityIndex` 매핑이 지워지지 않아, 이 공개 매핑에 외부 관찰자를 오도할 수 있는 오래된 데이터가 남습니다.

매핑은 스테이킹된 토큰에 대한 희귀도 인덱스 할당을 추적하지만, 토큰이 언스테이킹될 때 토큰이 더 이상 스테이킹되지 않음에도 불구하고 매핑은 이전 값을 유지합니다.

```solidity
File: src/staking/NFTStaking.sol
30:    mapping(uint256 => uint256) public tokenToRarityIndex;
```

공개 매핑은 언스테이킹 후 오래된 데이터를 포함하며, `nftStaked`와 `tokenToRarityIndex` 매핑 간에 상태 불일치가 발생합니다.

NFT 언스테이킹 시 `tokenToRarityIndex` 매핑을 지우십시오.

# [L-06] 중요한 NFTStaking 함수에서 이벤트 방출 부족

_해결됨 (Resolved)_

NFTStaking 계약의 여러 중요한 함수에 이벤트 방출이 없어 계약의 투명성과 오프체인 관찰 가능성이 크게 감소합니다. `_stakeNFTs()`, `_unstakeNFTs()`, `_claimRewards()`, `setRarityWeights()`, `setRate()`와 같은 주요 상태 변경 작업은 해당 이벤트를 방출하지 않고 조용히 실행됩니다. 이 누락으로 인해 외부 시스템(예: dApp 프론트엔드, 인덱서, 모니터링 서비스)이 계약 활동 및 상태 변경을 안정적으로 추적하기 어렵습니다.

이벤트의 부재로 인해 오프체인 시스템은 사용자 작업 및 관리 변경 사항을 감지하기 위해 폴링이나 트랜잭션 영수증 파싱과 같은 비효율적이고 잠재적으로 신뢰할 수 없는 방법에 의존해야 합니다. 이는 업데이트를 지연시켜 사용자 경험을 저하시키고 신뢰할 수 있는 모니터링 도구 구축의 복잡성을 증가시킵니다. `setRarityWeights()` 및 `setRate()`와 같은 관리 함수의 경우 이벤트 부족으로 인해 커뮤니티가 거버넌스 조치를 투명하게 감사하기 어려워져 잠재적으로 프로토콜 관리에 대한 신뢰를 떨어뜨릴 수 있습니다.

**권장 사항:** 모든 중요한 상태 변경 작업에 대해 의미 있는 이벤트를 방출하십시오.

# [L-07] 계약에 충분한 HYBUX 잔액이 없으면 언스테이킹이 실패하여 NFT가 갇힘

_해결됨 (Resolved)_

`_unstakeNFTs` 함수는 NFT 인출 전에 보상 청구를 강제하는데, 계약에 누적된 보상을 지불할 충분한 HYBUX 토큰이 없는 경우 NFT가 영구적으로 갇힐 수 있습니다.

```solidity
File: src/staking/NFTStaking.sol
182:    function _unstakeNFTs(address _sender, uint256[] calldata _tokenIds) internal {
183:@>     claimRewards();  // Forces reward claiming before unstaking
```

`claimRewards()` 함수는 `_claimRewards()`를 호출하며, 이는 HYBUX 토큰 전송을 시도합니다:

```solidity
File: src/staking/NFTStaking.sol
196:    function _claimRewards(
197:        address _sender
198:    ) internal {
199:        uint256 rewards = calculateRewards(_sender, true);
200:        if (rewards > 0) {
201:            pendingRewards[_sender] = 0;
202:            lastUpdateTimestamp[_sender] = block.timestamp;
203:@>         hybux.safeTransfer(_sender, rewards);  // Reverts if insufficient balance
204:        }
```

영향:

- 계약이 지불 불능 상태가 되면 사용자가 NFT를 언스테이킹할 수 없음
- NFT가 계약에 영구적으로 갇힘
- 유동성이 낮은 기간 동안 사용자가 스테이킹된 자산에 접근할 수 없음

**권장 사항**

사용자가 항상 NFT를 회수할 수 있도록 언스테이킹을 보상 청구와 분리하십시오.

# [L-08] 스테이커가 불공정하게 보상을 최대화하기 위해 비율 변경을 적극적으로 악용할 수 있음

_인정됨 (Acknowledged)_

글로벌 `rate` 변수는 소유자가 변경할 수 있지만, 스테이커는 비율 변경을 전후하여 보상 청구 타이밍을 전략적으로 잡아 이를 적극적으로 악용할 수 있습니다. 보상을 계산할 때 현재 비율이 사용자의 마지막 업데이트 이후 전체 스테이킹 기간에 적용되어 조작 기회를 만듭니다.

```solidity
File: NFTStaking.sol
21:@>   uint256 public rate;
...
106:   function setRate(
107:       uint256 _rate
108:   ) public onlyOwner {
109:@>     rate = _rate;
110:   }
...
128:   function calculateRewards(address _user, bool _includePendingRewards) public view returns (uint256) {
...
133:@>     uint256 timePassed = block.timestamp - lastUpdateTimestamp[_user];
134:       uint256 totalWeight = getRarityWeightMultiplier(_user);
135:@>     uint256 accumulated = timePassed * rate * totalWeight;
...
```

다음 시나리오를 고려해 보십시오:

**비율 감소 악용:**
1. 소유자가 비율이 하루 100 토큰에서 50 토큰으로 감소할 것이라고 발표합니다.
2. 정교한 스테이커가 즉시 하루 100 토큰 비율로 모든 보상을 청구합니다.
3. 비율이 하루 50 토큰으로 감소합니다.
4. 변경 후 청구하는 다른 스테이커는 줄어든 보상을 받습니다.
5. 청구 타이밍을 정확하게 맞춘 악용자들은 최대 보상을 받습니다.

**비율 증가 악용:**
1. 비율이 하루 50 토큰에서 100 토큰으로 증가합니다.
2. 낮은 비율 기간 동안 스테이킹해 온 스테이커들이 청구를 기다립니다.
3. 증가 후 청구할 때, 그들은 전체 낮은 비율 스테이킹 기간에 대해 하루 100 토큰 보상을 받습니다.
4. 이는 스테이킹 중에 약속된 보상의 두 배를 제공합니다.

**권장 사항**

각 NFT가 스테이킹될 때 활성 상태였던 비율을 기준으로 보상을 받도록 토큰별 비율 저장을 구현하십시오. 이는 청구의 전략적 타이밍을 방지하고 실제 스테이킹 기간 동안 활성 상태였던 비율을 기반으로 공정한 보상 분배를 보장합니다.
