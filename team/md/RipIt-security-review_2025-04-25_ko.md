# Pashov Audit Group 정보

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 입증된 실력을 갖추고 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 여러 번 챔피언이 되었거나, 우리 그룹과의 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300개 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위해 우리 팀이 최선을 다할 것임을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**ripdotfun/rip-it-core** 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며, **0x37, Arnie, Said, jesjupyter, km, 0xTheBlackPanther, dobrevaleri**가 **Rip It**을 검토하는 데 참여했습니다. 총 **54**개의 문제가 발견되었습니다.

# Rip It 정보

Rip It은 사용자가 NFT 팩을 구매, 개방 및 거래하는 트레이딩 카드 플랫폼으로, 마켓플레이스와 스핀-투-윈(spin-to-win) 메커니즘이 내장되어 있습니다. 무작위 카드 분배 시스템, 거래를 위한 USDC, 그리고 발행, 보상 및 마켓플레이스 운영을 관리하기 위한 역할 기반 액세스 모델을 사용합니다.

# 보안 평가 요약

**검토 커밋 해시:**<br>• [a8f2ee2544ef7ae42397f8d75915416bfeb7cb81](https://github.com/ripdotfun/rip-it-core/tree/a8f2ee2544ef7ae42397f8d75915416bfeb7cb81)<br>&nbsp;&nbsp;(ripdotfun/rip-it-core)

**수정 검토 커밋 해시:**<br>• [2c22ac3f742b3f3a27161d39e762fa4449c4c1cd](https://github.com/ripdotfun/rip-it-core/tree/2c22ac3f742b3f3a27161d39e762fa4449c4c1cd)<br>&nbsp;&nbsp;(ripdotfun/rip-it-core)

# 범위

- `Card`
- `CardStorage`
- `CardAllocationPool`
- `CardAllocationPoolStorage`
- `Marketplace`
- `MarketplaceAdmin`
- `MarketplaceStorage`
- `Packet`
- `PacketStorage`
- `PacketStore`
- `RoleBasedAccessControl`
- `RoleBasedAccessControlConsumer`
- `SpinLottery`
- `SpinLotteryStorage`
- `WhitelistRegistry`
- `WhitelistRegistryStorage`
- `IMarketplace.sol`
- `IRandomAllocationPool.sol`

# 발견 사항

# [H-01] 구독 자금이 고갈될 수 있음

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명
SpinLottery에서 사용자는 스핀 활동에 참여하여 하나의 상금 카드를 얻을 수 있습니다. 사용자는 당첨 가능성을 선택할 수 있습니다. 사용자가 더 높은 당첨 가능성을 선택하면, 이 스핀에 대해 더 높은 지불을 해야 합니다.

사용자가 스핀을 트리거하면 Chainlink VRF에 무작위성을 요청합니다. RIP에서는 구독 모드를 선택합니다. Chainlink VRF는 구독 잔액에서 관련 수수료를 청구합니다. 매번 스핀을 트리거할 때마다 Chainlink VRF는 구독 잔액에서 관련 수수료를 청구합니다.

Chainlink VRF 청구 [공식](https://docs.chain.link/vrf/v2-5/billing)에 따르면, 하나의 무작위 비용은 `gasPrice * (callback gas used + Verification gas used) * ((100 + premium percentage)/100)`입니다.

여기서 문제는 일부 악의적인 사용자가 그리프(grief) 공격을 시작할 수 있다는 것입니다. 해커는 `spin` 트리거를 반복하여 구독 잔액을 소모할 수 있습니다. 일반적인 시나리오에서 한 번의 스핀 비용은 `gas used + spin cost`여야 합니다. 그러나 해커는 `_totalSlots`를 매우 큰 값으로 늘려 스핀 비용을 0으로 내림할 수 있습니다. 이 공격 벡터에 대한 유일한 비용은 `gas used`입니다.

이로 인해 Chainlink VRF는 구독 계정의 모든 잔액을 소모하게 됩니다.
```solidity
    function spin(uint256 _totalSlots, uint256 _prizeCount) external whenNotPaused returns (uint256) {
        uint256 spinCost = calculateSpinCost(_totalSlots, _prizeCount);
        
        // Process payment
        if (!usdcToken.transferFrom(msg.sender, address(this), spinCost)) {
            revert USDCTransferFailed();
        }
        uint256 requestId = requestRandomness();
        spinRequests[requestId] = SpinRequest({
            player: msg.sender, 
            totalSlots: _totalSlots, 
            prizeCount: _prizeCount
        });
    }
    function calculateSpinCost(uint256 _totalSlots, uint256 _prizeCount) public view returns (uint256) {
        return (avgBasePrice * _prizeCount * 1000) / (_totalSlots * 1000);
    }
```

## 권장 사항

최소 스핀 비용을 추가하십시오.

# [H-02] `instantOpenPacket()`의 불충분한 제한으로 인한 DOS 위험

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`Packet.sol`에서 사용자는 `BurnType.INSTANT_OPEN_PACKET`으로 설정된 `params.BurnType`으로 `initiateBurn`을 호출할 수 있습니다.
```solidity
        if (params.burnType == BurnType.INSTANT_OPEN_PACKET) {
            randomAllocationPool.instantOpenPacket(params.packetId, _packetTypeIds[params.packetId], msg.sender);
        }
```
그러면 이 함수는 `CardAllocationPool` 계약에서 `instantOpenPacket`을 호출합니다.

```solidity
    function instantOpenPacket(uint256 packetId, uint256 packetType, address owner) external {
        if (msg.sender != packetNFTAddress) revert UnauthorizedCaller();

        if (packetTypeToCardBundles[packetType].length == 0) revert InsufficientCardBundles();

        // Request randomness from Chainlink VRF
        uint256 requestId = requestRandomWords(packetType);

        requestIdToPacketOpen[requestId] =
            PacketOpenRequest({packetId: packetId, packetType: packetType, owner: owner, fulfilled: false});
```
이 함수는 먼저 `msg.sender`가 실제로 `packetNFTAdress`인지 확인하고, 그 다음 로직은 요청을 이행하기에 충분한 특정 유형의 cardBundles가 있는지 확인합니다. 그렇지 않으면 `insufficientCardBundles`로 되돌립니다.
마지막으로 `requestRandomWords`에 대한 호출이 이루어지고 `requestId`가 할당됩니다.

chainlink VRF가 마침내 무작위 값을 전달하면 무작위 값을 사용하여 `selectRandomCards`를 호출합니다.
```solidity
    function fulfillRandomWords(uint256 requestId, uint256[] memory randomWords) internal {
        PacketOpenRequest storage request = requestIdToPacketOpen[requestId];
        if (request.fulfilled) revert("Already fulfilled");

        // fetch the available card bundles
        CardBundle[] storage cardBundles = packetTypeToCardBundles[request.packetType];

        // Select random cards using the provided randomness
        uint256[] memory selectedCards = selectRandomCards(cardBundles, randomWords[0]);
```

여기서 문제가 시작됩니다...

```solidity
    function selectRandomCards(CardBundle[] storage availableCardBundles, uint256 randomSeed)
        private
        returns (uint256[] memory selectedCardBundle)
    {
        if (availableCardBundles.length == 0) {
            revert NoAvailableCardBundles();
        }
```
`availableCardBundles.length` == 0이면 되돌립니다. 이것이 문제입니다. 아래에서 설명하겠습니다.

1. 2명의 사용자가 동일한 블록에서 `BurnType` == `INSTANT_OPEN_PACKET`으로 `Packet.sol`에서 `initiateBurn`을 호출하며, 두 요청 모두 동일한 `packetType`입니다.
2. 특정 `packetType`의 번들이 1개만 남았다고 가정합니다. 특정 `packetType`에 대해 번들이 1개만 남아 있어도 두 사용자 모두에 대해 `instantOpenPacket`의 확인이 통과됩니다.
3. 다음으로 두 사용자 모두 `requestId`를 할당받고 chainlink가 `fulfillRandomWords`를 수행하기를 기다립니다.
4. `fulfillRandomWords`가 호출되면 `selectRandomCards`에 대한 내부 호출이 발생합니다. 이 함수는 `availableCardBundles`에서 유일한 값을 팝(pop)하므로 사용자 1은 의도한 대로 카드를 받습니다.
5. 사용자 2에 대해 `fulfillRandomWords`가 호출되면 `availableCardsBundles.length`가 0이 되므로 `selectRandomCards`에 대한 하위 호출에서 `fulfillRandomWords`가 되돌려지고 전체 tx가 되돌려집니다.
6. `fulfillRandomWords`가 되돌려지므로 chainlink 문서에 띠르면 요청이 DOS됩니다.
> fulfillRandomWords() 구현이 되돌려지면, VRF 서비스는 두 번째 호출을 시도하지 않습니다. 계약 로직이 되돌려지지 않도록 하십시오. 단순히 무작위성을 저장하고 귀하, 사용자 또는 자동화 노드가 만든 별도의 계약 호출에서 더 복잡한 후속 조치를 취하는 것을 고려하십시오.

## 권장 사항

특정 `packetType`에 대해 사용할 수 있는 `cardBundles`의 양보다 더 많은 요청을 할 수 없도록 `instantOpenPacket`에 확인을 추가하는 것을 고려하십시오.

# [H-03] 가중치 비율과 일치하지 않는 상금 잠금 메커니즘

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

SpinLottery 계약에서 사용자는 희귀도(rarity)의 가중치 분포에 따라 `spin`에 대한 비용을 지불하며, 이는 참여 비용을 결정합니다.

```solidity
    function calculateSpinCost(uint256 _totalSlots, uint256 _prizeCount) public view returns (uint256) {
        if (_totalSlots == 0 || _prizeCount == 0 || _prizeCount > _totalSlots) {
            revert InvalidSlotConfiguration();
        }

        uint256 totalWeightedPrice = 0;
        
        // Calculate weighted average price across all active rarities
        for (uint8 i = 1; i <= maxRarityId; i++) {
            RarityConfig memory config = rarityConfigs[i];
            if (config.active) {
                totalWeightedPrice += uint256(config.basePrice) * config.weight;
            }
        }
        
        if (totalRarityWeight == 0) revert InvalidWeightConfiguration();
        uint256 avgBasePrice = totalWeightedPrice / totalRarityWeight;
        
        return (avgBasePrice * _prizeCount * 1000) / (_totalSlots * 1000);
    }
```

그러나 실제 상금 잠금은 이 가중치 분포를 따르지 않습니다.
**예를 들어, 가중치가 `80%`, `10%`, `10%`로 분포된 시나리오에서 `prizeCount`가 2로 설정된 경우, 상금은 의도한 가중치 분포를 반영하는 대신 `1:1:1` 비율로 잠길 수 있습니다. 반대로 `prizeCount`가 `1`로 설정된 경우 가중치를 완전히 무시하고 `1:0:0`의 잠금 초래할 수 있습니다.**

특히 `1:0:0`이 잠겨 있을 때 상금은 여전히 `더 높은 희귀도`에 속할 수 있습니다. `잠금 메커니즘`의 보호 없이는 `fulfillRandomness`가 예기치 않게 실패할 수 있습니다.

```solidity
    function determineRarity(uint256 randomValue) public view returns (uint8) {
        uint256 value = randomValue % totalRarityWeight;
        uint256 cumulativeWeight = 0;
        
        // Iterate through rarities to find where the random value lands
        for (uint8 i = 1; i <= maxRarityId; i++) {
            RarityConfig memory config = rarityConfigs[i];
            if (config.active) {
                cumulativeWeight += config.weight; // <= could still fall into higher rarity
                if (value < cumulativeWeight) { 
                    return i;
                }
            }
        }
        
        // Fallback to highest rarity (should not happen unless weights are misconfigured)
        return maxRarityId;
    }
```

이러한 불일치는 상금 할당이 기여도와 일치하지 않으므로 불공정한 결과로 이어질 수 있습니다.

## 권장 사항

잠금 메커니즘의 로직을 수정하십시오.

# [H-04] 보류 중인 상금 잠금 메커니즘으로 인한 DoS 위험

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`SpinLottery` 계약의 `calculatePendingPrizesForRarity` 함수는 `_prizeCount`가 `1`보다 크고 `weight`가 `0`보다 큰 경우 `pendingCount`를 보수적으로 1로 설정하는 최소 보장 메커니즘을 구현합니다.

```solidity
        // Ensure at least some prizes are allocated if weights allow it
        if (_prizeCount > 1 && pendingCount == 0 && weight > 0) {
            pendingCount = 1;
        }
```
`spin` 함수에서 `pendingCounts`가 잠기고 사용 가능한 총 상금이 부족하면 `InsufficientPrizes` 오류가 발생하여 서비스 거부(DoS)가 발생할 수 있습니다.

```solidity
        for (uint8 i = 1; i <= maxRarityId; i++) {
            if (rarityConfigs[i].active) {
                pendingCounts[i] = calculatePendingPrizesForRarity(_prizeCount, i);
                totalPending += pendingCounts[i];
            }
        }
        ...
        // Check if enough prizes are available considering pending ones
        for (uint8 i = 1; i <= maxRarityId; i++) {
            if (pendingCounts[i] > 0) {
                uint256 available = getAvailablePrizes(i);
                uint256 pending = prizePools[i].pendingCount;
                
                if (available - pending < pendingCounts[i]) {
                    revert InsufficientPrizes();
                }
            }
        }
```

이 상황은 **가치 높은 상금(`rarity`)의 `available` 상금 수가 적을 때** 악화됩니다.

공격자나 일반 사용자는 `_prizeCount = 2`로 여러 `spin` 트랜잭션을 시작하여 제한된 상금을 효과적으로 잠그고 다른 사용자가 일시적으로 복권에 참여하지 못하게 하여 일정 기간 동안 빈번한 DoS 시나리오를 유발하여 사용자 경험을 악화시킬 수 있습니다.

## 권장 사항

이 문제를 해결하려면 관련 잠금 로직을 개선해야 합니다.

# [H-05] 리스팅 및 제안 요청에서 통화 검증 누락

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

 `Marketplace` 계약은 `ListingRequest` 및 `OfferRequest` 모두에서 통화를 정의합니다.

```solidity
    struct ListingRequest {
        address tokenContractAddress;
        uint256 tokenId;
        uint256 price;
        **address acceptedCurrency;**
        uint256 deadline;
        address owner;
        uint256 chainId;
    }

    struct OfferRequest {
        address tokenContractAddress;
        uint256 tokenId;
        uint256 price;
        **address offerCurrency;**
        uint256 deadline;
        address requester;
        uint256 chainId;
    }
```

그러나 관련 확인인 `isValidListing` 및 `isValidBid`에는 이러한 요청에 지정된 통화가 계약에 저장된 `acceptedCurrency`와 일치하는지 확인하는 검증이 없습니다.

```solidity
    function isValidListing(ListingParams calldata listing) public view returns (bool) {
        if (listing.request.price == 0) revert InvalidPrice();
        if (block.timestamp > listing.request.deadline) revert ListingExpired();
        if (listing.request.chainId != block.chainid) revert InvalidChainId();
        bytes32 listingHash = generateListingSignatureHash(listing.request);
        if (usedListings[listingHash]) revert ListingAlreadyUsed();
        if (!IWhitelistRegistry(whitelistRegistry).isWhitelisted(listing.request.tokenContractAddress)) {
            revert NFTNotWhitelisted();
        }
        if (IERC721(listing.request.tokenContractAddress).ownerOf(listing.request.tokenId) != listing.request.owner) {
            revert NotOwner();
        }

        if (!SignatureChecker.isValidSignatureNow(listing.request.owner, listingHash, listing.sig)) {
            revert InvalidSignature();
        }

        if (msg.sender != listing.receiver && !SignatureChecker.isValidSignatureNow(listing.receiver, listingHash, listing.receiverSig)) {
            revert InvalidSignature();
        }

        return true;
    }
   ....
```
이러한 간과로 인해 심각한 문제가 발생할 수 있습니다.

- `buy` 함수에서 함수는 지불을 위해 `acceptedCurrency`를 직접 사용합니다. 서명 시점과 실행 시점 사이에 `acceptedCurrency`가 변경된 경우 잘못된 비용 청구(수취인이 충분한 허용량을 승인한 경우) 또는 DOS가 발생할 수 있습니다.

```solidity
            // External calls after state changes
            IERC20(acceptedCurrency).transferFrom(payee, listings[i].request.owner, sellerAmount);
            IERC20(acceptedCurrency).transferFrom(payee, address(this), feeAmount);
```

- 더 중요한 것은, `acceptOffer` 함수에서는 `odds[i].request.offerCurrency`를 사용하여 지불한다는 것입니다. 이는 `acceptedCurrency` 확인을 우회하여 `totalPendingFees`의 잘못된 계산으로 이어질 수 있으며 실제 수수료를 검색할 수 없게 만들 수 있습니다.

```solidity
            // Update state before external calls
            totalPendingFees += feeAmount;
            // External calls after state changes
            IERC20(offers[i].request.offerCurrency).transferFrom(offers[i].request.requester, offers[i].receiver, sellerAmount);
            IERC20(offers[i].request.offerCurrency).transferFrom(offers[i].request.requester, address(this), feeAmount);
```

- 또한 `emergencyShutdown` 중에 `acceptedCurrency`가 0으로 설정된 경우에도 `acceptOffer`의 트랜잭션이 완료될 수 있어 비상 종료 메커니즘의 목적이 훼손될 수 있습니다.

```solidity
    function emergencyShutdown() external {
        require(msg.sender == marketplaceAdmin, "Only marketplaceAdmin can call");
        // First collect any pending fees
        uint256 amount = totalPendingFees;
        if (amount > 0 && acceptedCurrency != address(0)) {
            totalPendingFees = 0;
            IERC20(acceptedCurrency).transfer(feeReceiver, amount);
        }
        // Set currency to address(0) to prevent any new trades
        acceptedCurrency = address(0);
    }
```

## 권장 사항

계약의 무결성을 높이고 이러한 문제를 방지하려면 트랜잭션을 실행하기 전에 `ListingRequest` 및 `OfferRequest` 모두에서 사용되는 통화가 `acceptedCurrency`와 일치하는지 확인하는 검증 확인을 구현하십시오.

# [H-06] 잘못된 `pendingCounts` 계산

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

SpinLottery 계약에서 사용자는 스핀을 통해 하나의 상금 NFT를 얻을 수 있습니다. 스핀 결과는 Chainlink VRF의 무작위성을 기반으로 합니다. 스핀 결과는 비동기식입니다. 사용자가 `spin` 함수를 실행하면 `pendingCounts`를 계산합니다. 이전 `스핀` 트랜잭션이 있기 때문에 이러한 트랜잭션의 결과는 알 수 없으며, 이러한 `스핀` 트랜잭션이 승리하여 계약에서 일부 상금 NFT를 청구할 수 있습니다. 따라서 `스핀`을 실행할 때 풀의 현재 상금과 잠재적인 청구 상금을 확인해야 합니다.

`pendingCounts`는 이 `스핀` 이전에 잠재적으로 청구된 상금 NFT를 기록하는 데 사용됩니다.

현재 구현에 따르면 사용자가 한 번의 스핀에서 승리하면 하나의 상금 NFT를 얻습니다. 여기서 문제는 `calculatePendingPrizesForRarity` 함수에서 `pendingCount`를 계산할 때 `pendingCount`가 `_prizeCount`, `weight`, `totalRarityWeight`와 관련이 있다는 것입니다. `_prizeCount`는 사용자가 제어할 수 있습니다. 사용자가 큰 `_prizeCount`로 `spin`을 실행하면 큰 `pendingCount`를 얻게 됩니다. `pendingCount`는 `prizePools[i].pendingCount`로 업데이트됩니다.

이로 인해 나중의 `spin`이 차단될 수 있습니다.

`calculatePendingPrizesForRarity` 함수에서 매개변수 `_prizeCount`는 주석에 따라 배포할 상금 수여야 합니다. 그러나 이것은 올바르지 않습니다. `_prizeCount`는 사용자가 제어할 수 있으며 `_prizeCount`가 어떤 값이든 한 번의 스핀에 대한 배포 상금 수는 항상 `1`입니다. 이로 인해 `spin`의 `_prizeCount`와 관련된 일부 논리가 올바르지 않게 됩니다.

```solidity
    function spin(uint256 _totalSlots, uint256 _prizeCount) external whenNotPaused returns (uint256) {
        for (uint8 i = 1; i <= maxRarityId; i++) {
            if (rarityConfigs[i].active) {
                pendingCounts[i] = calculatePendingPrizesForRarity(_prizeCount, i);
                totalPending += pendingCounts[i];
            }
        }
        if (totalPending == 0) {
            for (uint8 i = 1; i <= maxRarityId; i++) {
                if (rarityConfigs[i].active) {
                    pendingCounts[i] = 1;
                    totalPending = 1;
                    break;
                }
            }
        }
        for (uint8 i = 1; i <= maxRarityId; i++) {
            if (pendingCounts[i] > 0) {
                uint256 available = getAvailablePrizes(i);
                uint256 pending = prizePools[i].pendingCount;
                if (available - pending < pendingCounts[i]) {
                    revert InsufficientPrizes();
                }
            }
        }
        for (uint8 i = 1; i <= maxRarityId; i++) {
            if (pendingCounts[i] > 0) {
                prizePools[i].pendingCount += uint96(pendingCounts[i]);
            }
        }
    }
    function calculatePendingPrizesForRarity(uint256 _prizeCount, uint8 rarity) internal view returns (uint256) {
        if (_prizeCount == 1) {
            // For single prizes, prioritize first active rarity
            return rarity == 1 && rarityConfigs[1].active ? 1 : 0;
        }
        
        if (!rarityConfigs[rarity].active) return 0;
        
        uint256 weight = rarityConfigs[rarity].weight;
@>        uint256 pendingCount = (_prizeCount * weight) / totalRarityWeight;
        
        if (_prizeCount > 1 && pendingCount == 0 && weight > 0) {
            pendingCount = 1;
        }
        
        return pendingCount;
    }
```
## 권장 사항

`pendingCount`의 계산을 다시 방문하십시오.

# [H-07] 첫 번째 `prizeId`가 선택되면 `SpinLottery`가 중단됨

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`_claimPrize` 로직 내부에서 무작위로 선택된 `prizeId`가 `firstPrizeId`와 같으면 `firstPrizeId`가 증가합니다. 그러나 `nextPrizeId`도 감소합니다.

```solidity
    function _claimPrize(uint8 rarity, uint256 randomValue) internal returns (address nftAddress, uint88 tokenId) {
        PrizePool storage pool = prizePools[rarity];
        if (pool.nextPrizeId <= pool.firstPrizeId) revert NoPrizesAvailable();
        
        // Calculate prize count and random index
        uint256 prizeCount = pool.nextPrizeId - pool.firstPrizeId;
        uint256 index = randomValue % prizeCount;
        uint256 prizeId = pool.firstPrizeId + index;
        
        // Get prize data
        uint256 packed = pool.prizeData[prizeId];
        
        // Remove prize using unordered removal pattern - more gas efficient than array management
        // When a prize is removed, we move the last prize to its place if it's not already the last
        if (prizeId != pool.nextPrizeId - 1) {
            uint256 lastPrizeId = pool.nextPrizeId - 1;
            pool.prizeData[prizeId] = pool.prizeData[lastPrizeId];
            delete pool.prizeData[lastPrizeId];
        } else {
            delete pool.prizeData[prizeId];
        }
        
        // If we've removed the first prize, increment firstPrizeId
        if (prizeId == pool.firstPrizeId) {
>>>         pool.firstPrizeId++;
        }
        
        // Decrement nextPrizeId
>>>     pool.nextPrizeId--;
        
        uint8 unpackedRarity;
        (nftAddress, tokenId, unpackedRarity) = unpackPrize(packed);
        assert(unpackedRarity == rarity);
    }
```

이로 인해 해당 희귀도에 대한 상금 선택 기능이 중단됩니다. 다음 시나리오를 고려해 보십시오.

- `pool.firstPrizeId` = 1 및 `pool.nextPrizeId` = 3, 즉 가능한 상금이 2개 있음을 의미합니다.

- 사용자가 스핀하고 첫 번째 상금(`prizeId` = 1)을 받습니다.

- `pool.firstPrizeId++` 및 `pool.nextPrizeId--`가 수행되어 `pool.firstPrizeId` = 2 및 `pool.nextPrizeId` = 2가 됩니다. 즉, 다른 상금은 손실되어 사용자가 더 이상 플레이하고 청구할 수 없습니다.

## 권장 사항

`lastPrizeId`가 첫 번째 위치로 이동하므로 불필요한 `pool.firstPrizeId++` 로직을 제거하십시오.

# [M-01] `addCardBundlesToPacketPool`에서 누락된 온전성 검사

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

CardAllocationPool 계약에서 즉시 상환을 위해 Chainlink VRF를 통해 하나의 무작위성을 생성합니다. initialize() 함수에서 `callbackGasLimit`를 100_000으로 초기화합니다. 이 가스 제한은 Chainlink VRF 콜백 가스 제한에 사용됩니다.

콜백 함수에는 선택한 카드를 전송하기 위한 하나의 루프가 있습니다. 파운드리 테스트 후 이 번들에 10장의 카드가 있으면 가스 제한이 충분하지 않습니다. 이로 인해 콜백 기능이 실패하고 사용자는 카드 NFT를 얻을 수 없습니다.

`addCardBundlesToPacketPool` 함수는 하나의 카드 번들을 추가하는 데 사용됩니다. 여기서 문제는 카드 번들의 길이에 대한 온전성 검사를 추가하지 않는다는 것입니다.
```solidity
    function initialize(
        address packetAddress,
        address cardAddress,
        address _rbac,
        uint256 _redeemManagerRoleId,
        address vrfCoordinator,
        uint256 subscriptionId,
        bytes32 _keyHash
    ) external initializer {
        callbackGasLimit = 100000;
    }
    function fulfillRandomWords(uint256 requestId, uint256[] memory randomWords) internal {
        PacketOpenRequest storage request = requestIdToPacketOpen[requestId];
        if (request.fulfilled) revert("Already fulfilled");

        CardBundle[] storage cardBundles = packetTypeToCardBundles[request.packetType];

        uint256[] memory selectedCards = selectRandomCards(cardBundles, randomWords[0]);

@>        for (uint256 i = 0; i < selectedCards.length; i++) {
            IERC721(cardNFTAddress).transferFrom(address(this), request.owner, selectedCards[i]);
        }

        request.fulfilled = true;

        RipFunPacks(packetNFTAddress).finalizeOpen(request.packetId, "");

        emit PacketOpenFulfilled(requestId, request.packetId, selectedCards);
    }
    function addCardBundlesToPacketPool(uint256 packetType, uint256[] memory cardBundles, bytes32 bundleProvenance)
        external
        onlyRole(redeemManagerRoleId)
    {
        packetTypeToCardBundles[packetType].push(CardBundle({cardIds: cardBundles, bundleProvenance: bundleProvenance}));
        for (uint256 i = 0; i < cardBundles.length; i++) {
            IERC721(cardNFTAddress).transferFrom(msg.sender, address(this), cardBundles[i]);
        }

        emit CardsAddedToPool(packetType, cardBundles, bundleProvenance);
    }
```

## 권장 사항

카드 번들의 길이에 대한 온전성 검사를 추가하십시오.

# [M-02] 서명 해시 생성 시 논스(nonce) 메커니즘 부족

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

Marketplace 계약의 `generateListingSignatureHash` 함수는 논스 메커니즘을 포함하지 않는 리스팅 요청에 대한 해시를 생성합니다.

```solidity
    function generateListingSignatureHash(ListingRequest calldata listing) public view returns (bytes32) {
        return _hashTypedDataV4(
            keccak256(
                abi.encode(
                    LISTING_TYPEHASH,
                    listing.tokenContractAddress,
                    listing.tokenId,
                    listing.price,
                    listing.acceptedCurrency,
                    listing.deadline,
                    listing.owner,
                    listing.chainId
                )
            )
        );
    }
```

이 누락은 소유자가 리스팅에 서명하면 소유자가 나중에 가격을 수정하더라도 서명이 유효하다는 것을 의미합니다( `deadline`까지). **그리고 서명이 서명되면 취소할 방법이 없습니다.**

이로 인해 악의적인 공격자가 `deadline`까지 소유자의 서명을 보류할 수 있는 취약점이 생성됩니다. 자산 가격이 상승하면(변동성 자산에서 흔히 발생) 공격자는 원래 서명된 가격을 사용하여 구매를 실행하여 리스팅 프로세스의 무결성을 훼손하고 판매자에게 잠재적인 금전적 손실을 초래할 수 있습니다.

```solidity
        bytes32 listingHash = generateListingSignatureHash(listing.request);
        if (usedListings[listingHash]) revert ListingAlreadyUsed();
        if (!IWhitelistRegistry(whitelistRegistry).isWhitelisted(listing.request.tokenContractAddress)) {
            revert NFTNotWhitelisted();
        }
        if (IERC721(listing.request.tokenContractAddress).ownerOf(listing.request.tokenId) != listing.request.owner) {
            revert NotOwner();
        }

        if (!SignatureChecker.isValidSignatureNow(listing.request.owner, listingHash, listing.sig)) {
            revert InvalidSignature();
        }
```

`isValidTrade` 및 `isValidBid`의 다른 서명 생성에 대해서도 동일합니다.

## 권장 사항

리스팅 프로세스의 보안을 강화하려면 서명 해시 생성에 포함된 논스 메커니즘을 구현하십시오. 이 논스는 각 리스팅에 대해 고유해야 하며 소유자의 새 리스팅 요청마다 증가해야 합니다. **더 구체적으로, 소유자는 서명이 유효하지 않게 되면 서명을 비활성화할 수 있어야 합니다.**

# [M-03] `tradeFee` 및 `acceptedCurrency` 모드에서의 일관되지 않은 거래 수수료

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명
Marketplace 계약에서 `tradeFee`는 `acceptedCurrency`로 청구되며, 이는 수수료 자체와 독립적으로 수정될 수 있습니다.

```solidity
            // Update state before external calls
            totalPendingFees += tradeFee;

            // External calls after state changes
            // take fee from requester
            IERC20(acceptedCurrency).transferFrom(trades[i].request.requester, address(this), tradeFee);
```

```solidity
    /**
     * @notice Updates the trade fee (called by MarketplaceAdmin)
     * @dev Only callable by the MarketplaceAdmin contract
     * @param newTradeFee New trade fee amount
     */
    function setTradeFee(uint256 newTradeFee) external {
        require(msg.sender == marketplaceAdmin, "Only marketplaceAdmin can call");
        tradeFee = newTradeFee;
    }


    /**
     * @notice Updates the accepted currency (called by MarketplaceAdmin)
     * @dev Only callable by the MarketplaceAdmin contract
     * @param newERC20TokenAddress Address of the new ERC20 token to be accepted
     */
    function setAcceptedCurrency(address newERC20TokenAddress) external {
        require(msg.sender == marketplaceAdmin, "Only marketplaceAdmin can call");
        if (newERC20TokenAddress == address(0)) revert InvalidCurrencyAddress();
        acceptedCurrency = newERC20TokenAddress;
    }
```

이로 인해 `tradeFee`와 `acceptedCurrency`가 별도의 트랜잭션에서 변경될 때 잠재적인 위험이 발생합니다. 사용자가 이 두 수정 사이에 지불을 시작하면 과다 지불 또는 과소 지불과 같은 의도하지 않은 결과가 발생할 수 있습니다.

예를 들어 `acceptedCurrency`의 소수가 `6`( `USDC`)에서 `18`( `DAI`)로 변경되면 1e6의 `tradeFee`는 사실상 무시할 수 있게 되어 마켓플레이스의 수익이 크게 손실됩니다.

## 권장 사항
`acceptedCurrency`에 대한 변경 사항은 동일한 트랜잭션에서 `acceptedCurrency`도 변경하도록 요구하여 두 값이 동시에 업데이트되도록 하십시오.

# [M-04] `Marketplace`의 EIP-712 준수 문제

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

Marketplace 계약의 `TRADE_TYPEHASH`는 `TradeGive` 및 `TradeTake`를 참조하는 구조체를 사용하여 계산됩니다. 그러나 현재 구현에는 참조된 구조체에 대한 정의나 유형 해시가 포함되어 있지 않습니다.

```solidity
    /**
     * @notice The EIP-712 type hash for trade requests.
     */
    bytes32 public constant TRADE_TYPEHASH = keccak256(
        "TradeRequest(TradeGive[] give,TradeTake[] take,uint256 deadline,address requester,uint256 chainId)"
    );
```

[EIP-712](https://eips.ethereum.org/EIPS/eip-712#definition-of-encodetype)에 따르면:
> 구조체 유형이 다른 구조체 유형을 참조하는 경우(그리고 이것이 차례로 더 많은 구조체 유형을 참조하는 경우), 참조된 구조체 유형 세트가 수집되어 이름별로 정렬되고 인코딩에 추가됩니다. 인코딩 예제는 `Transaction(Person from,Person to,Asset tx)Asset(address token,uint256 amount)Person(address wallet,string name)`입니다.


또한 `generateTradeSignatureHash`를 계산할 때 `trade.give` 및 `trade.take`는 동적 유형이므로 `hashed` 값이어야 합니다.

```solidity
    function generateTradeSignatureHash(TradeRequest calldata trade) public view returns (bytes32) {
        return _hashTypedDataV4(
            keccak256(
                abi.encode(
                    TRADE_TYPEHASH,
                    trade.give,
                    trade.take,
                    trade.deadline,
                    trade.requester,
                    trade.chainId
                )
            )
        );
    }
```

이 누락은 `EIP-712` 사양을 위반하여 요청의 서명 및 확인 프로세스에서 잠재적인 취약점이 잘못 인코딩될 수 있습니다.

## 권장 사항

`EIP-712` 표준을 준수하는 것이 좋습니다.

# [M-05] `removeCardBundlesFromPacketPool`의 잠재적 경계 이탈(out-of-bounds)

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`removeCardBundlesFromPacketPool` 함수는 사용자가 `burnFromInventory`를 호출하는 대신 카드 번들을 "회수"하는 데 사용할 수 있습니다.

그러나 구현에는 문제가 있습니다.

```solidity
    function removeCardBundlesFromPacketPool(
        uint256 packetType,
        uint256[] memory bundleIndices
    ) external onlyRole(redeemManagerRoleId) {
        CardBundle[] storage bundles = packetTypeToCardBundles[packetType];
        
        // Sort indices in descending order to prevent index shifting issues
        // Simple bubble sort is fine here as we expect small arrays of indices
        for (uint i = 0; i < bundleIndices.length; i++) {
            for (uint j = 0; j < bundleIndices.length - 1; j++) {
                if (bundleIndices[j] < bundleIndices[j + 1]) {
                    uint256 temp = bundleIndices[j];
                    bundleIndices[j] = bundleIndices[j + 1];
                    bundleIndices[j + 1] = temp;
                }
            }
        }

        for (uint256 i = 0; i < bundleIndices.length; i++) {
            uint256 index = bundleIndices[i];
            if (index >= bundles.length) revert InvalidBundleIndex();

            CardBundle memory bundle = bundles[index];
            
            // Return cards to caller
            for (uint256 j = 0; j < bundle.cardIds.length; j++) {
                IERC721(cardNFTAddress).transferFrom(address(this), msg.sender, bundle.cardIds[j]);
            }

            // Remove bundle by swapping with last element and popping
            bundles[index] = bundles[bundles.length - 1];
            bundles.pop();
            
            emit CardBundleRemoved(packetType, index);
        }
    }
```

"제거" 작업은 마지막 요소와 교체하고 팝하여 수행됩니다. 배열의 길이는 매번 1씩 감소합니다. 그러나 `bundleIndices`에 따라 유효한 인덱스 검사가 이루어집니다. 따라서 정렬은 내림차순이어야 합니다(큰 것에서 작은 것으로). `bundleIndices[j]`가 `bundleIndices[j+1]`보다 작을 때 교환하면 `bundleIndices`는 마지막에 오름차순으로 정렬됩니다(작은 것에서 큰 것으로).

따라서 인덱스의 유효성 검사가 실패하거나 원치 않는 번들이 제거될 수 있습니다.

## 권장 사항

정렬을 올바르게 구현하십시오.
