# Pashov Audit Group 정보

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 입증된 실력을 갖추고 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 여러 번 챔피언이 되었거나, 우리 그룹과의 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300개 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치하는 데 도움을 준 이 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위해 우리 팀이 최선을 다할 것임을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**ripdotfun/rip-it-core** 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며, **Hals, merlinboii, Ch_301, saksham, IvanFitro, afriauditor, Opeyemi**가 **Rip It**을 검토하는 데 참여했습니다. 총 **27**개의 문제가 발견되었습니다.

# Rip It 정보

Rip It은 사용자가 NFT 팩을 구매, 개방 및 거래하는 트레이딩 카드 플랫폼으로, 마켓플레이스와 스핀-투-윈(spin-to-win) 메커니즘이 내장되어 있습니다. 무작위 카드 분배 시스템, 거래를 위한 USDC, 그리고 발행, 보상 및 마켓플레이스 운영을 관리하기 위한 역할 기반 액세스 모델을 사용합니다.

# 보안 평가 요약

**검토 커밋 해시:**<br>• [2c22ac3f742b3f3a27161d39e762fa4449c4c1cd](https://github.com/ripdotfun/rip-it-core/tree/2c22ac3f742b3f3a27161d39e762fa4449c4c1cd)<br>&nbsp;&nbsp;(ripdotfun/rip-it-core)

**수정 검토 커밋 해시:**<br>• [6e3f3fa04e18275071ffd896288d8dbf2d7f7f70](https://github.com/ripdotfun/rip-it-core/tree/6e3f3fa04e18275071ffd896288d8dbf2d7f7f70)<br>&nbsp;&nbsp;(ripdotfun/rip-it-core)

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

# [C-01] `authorizedOpenPacket()`의 재진입(Reentrancy) 공격으로 인한 중복 허용

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

**OPEN_PACKET** burn 유형을 처리할 때 `CardAllocationPool.sol`과 `Packet.sol` 간의 상호 작용에 중요한 재진입 취약점이 있습니다. 이 취약점을 통해 공격자는 단일 패킷에 대해 여러 세트의 NFT 카드를 받을 수 있습니다.

공격 시나리오는 다음 순서를 따릅니다.

- 공격자는 자신이 소유한 패킷에 대해 `Packet.sol`의 `initiateBurn()`을 사용하여 **OPEN_PACKET** 유형으로 소각(burn)을 시작합니다.
- 관리자는 `CardAllocationPool.sol`의 `authorizedOpenPacket()`을 호출하여 해당 패킷을 특정 카드로 엽니다.
- `authorizedOpenPacket()` 함수는 `safeTransferFrom()`을 사용하여 카드를 공격자에게 전송하며, 이는 공격자의 계약에서 `onERC721Received()` 후크를 트리거합니다.
- 이 콜백 내에서 공격자는 다음을 수행할 수 있습니다.
   1) `cancelBurn()`을 호출하여 패킷의 소각 상태를 재설정합니다.
   2) 즉시 동일한 패킷에 대해 **INSTANT_OPEN_PACKET** 유형으로 `initiateBurn()`을 다시 호출합니다.
- `CardAllocationPool.sol`에서 `packetTypeToCardBundles[packetType].length == 1`인 경우 `instantOpenPacket()` 함수는 다음을 수행합니다.
   1) NFT 카드 세트를 공격자에게 전송합니다.
   2) `Packet.sol`의 `finalizeOpen()`을 호출하여 패킷을 인벤토리로 전송합니다.
- 제어가 원래 `authorizedOpenPacket()` 함수로 돌아오면 `finalizeOpen()`을 호출하여 패킷을 소각하지만, 이 시점에서 패킷은 이미 인벤토리에 있으므로 상태 불일치가 발생합니다.

이 공격은 다음과 같은 결과를 초래합니다.

a. 공격자는 단일 패킷에 대해 카드를 두 번 받습니다.

b. 패킷은 재판매를 위해 인벤토리에 추가되지만 소각되기도 하여, 나중에 `PacketStore.sol` 계약을 호출하여 패킷을 전송하려고 할 때 실패하게 됩니다.

근본 원인은 `authorizedOpenPacket()` 함수가 재진입을 방지하지 않으며 burn 유형이 **NONE**으로 재설정되지 않아 `safeTransferFrom()` 작업의 콜백 중에 패킷의 상태가 조작될 수 있다는 것입니다.

## 권장 사항

재진입 보호를 구현하고 확인-효과-상호작용(checks-effects-interactions) 패턴을 사용하여 `Packet.sol` 및 `CardAllocationPool.sol` 함수에서 이 공격을 방지하십시오.

# [C-02] `finalizeOpen()`에서 패킷 ID 누락으로 인한 NFT 손실

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`Packet.sol` 계약에서 `finalizeOpen()` 함수에는 `burnType`이 **INSTANT_OPEN_PACKET**일 때 모든 트랜잭션이 실패하게 만드는 치명적인 결함이 있습니다.
**INSTANT_OPEN_PACKET** burn 유형을 처리할 때 함수는 `packetStore.addPacketsToInventory(packetIds)`를 호출하여 패킷을 인벤토리에 추가하려고 시도합니다. 그러나 패킷 ID를 보유할 새 배열을 생성하지만 실제로 패킷 ID를 배열에 할당하지는 않습니다.

```solidity
if (burnType == BurnType.INSTANT_OPEN_PACKET) {
    _transfer(ownerOf(packetId), address(this), packetId);
    this.approve(address(packetStore), packetId);

    uint256[] memory packetIds = new uint256[](1);
    // Missing: packetIds[0] = packetId;
    packetStore.addPacketsToInventory(packetIds);
}
```
결과적으로 `packetIds` 배열에는 실제 패킷 ID가 아닌 기본값(0)만 포함되므로 `addPacketsToInventory()`가 유효하지 않은 패킷 ID를 처리하려고 시도합니다. 이는 0 패킷 ID를 검증하려고 할 때 `addPacketsToInventory()`에서 되돌림(revert)으로 이어집니다.

```solidity
if (!_validatePacketTypeId(packetTypeId)) revert InvalidPacketType(packetTypeId);
```

이 문제로 인해 사용자는 패킷 NFT를 잃게 되지만(계약으로 전송됨) `packetStore.addPacketsToInventory()` 호출 중에 트랜잭션이 실패하므로 대가로 아무것도 받지 못합니다.

### 개념 증명 (Proof of Concept)

다음 POC를 `CardAllocationPoolTest.t.sol`에 복사하십시오.

```solidity
function testPOC() public {
        uint256 packetType = 1;
        uint256 packetId = 1;
        uint256[] memory cardIds = _mintCardsToAdmin(3);

        // Add bundle to pool
        vm.startPrank(admin);
        bytes32 bundleProvenance = keccak256(abi.encode(cardIds));
        pool.addCardBundlesToPacketPool(packetType, cardIds, cardIds, bundleProvenance);
        vm.stopPrank();

        // Setup packet
        vm.startPrank(admin);
        packet.registerPacketType(
            PacketStorage.PacketTypeParams({packetTypeName: "Test Pack", packetTypeMetadata: "Test Metadata"})
        );

        packet.mintTo(
            PacketStorage.PacketMintParams({
                packetTypeId: packetType,
                packetMetadata: "Test Packet",
                packetSerialNumber: "TEST-001"
            }),user
        );

        vm.stopPrank(); 

        // User initiates burn which will trigger instantOpenPacket
        
        vm.startPrank(user);
        vm.expectRevert(RipFunStore.InvalidPacketType.selector);
        packet.initiateBurn(
            PacketStorage.PacketBurnParams({packetId: packetId, burnType: PacketStorage.BurnType.INSTANT_OPEN_PACKET})
        );
        vm.stopPrank();

        // Verify request was made
        //assertTrue(coordinator.lastRequestId() == 0);
    }
```
## 권장 사항

`addPacketsToInventory()`를 호출하기 전에 패킷 ID를 배열에 저장하도록 누락된 할당을 추가하십시오.

```diff
 function finalizeOpen(uint256 packetId, uint256[] memory selectedBundle, string memory openMetadata)
        external
        onlyRole(ALLOCATION_MANAGER_ROLE)
    {
       //...
        if (burnType == BurnType.INSTANT_OPEN_PACKET) {
            _transfer(ownerOf(packetId), address(this), packetId);
            this.approve(address(packetStore), packetId);

            uint256[] memory packetIds = new uint256[](1);
+           packetIds[0] = packetId;
            packetStore.addPacketsToInventory(packetIds);
        } else {
          //...
        }
        //...
    }
```
이렇게 하면 올바른 패킷 ID가 `addPacketsToInventory()` 함수에 전달되어 트랜잭션이 성공적으로 완료될 수 있습니다.

# [H-01] `burnPacketFromInventory()`에서 인덱스 업데이트 누락으로 인한 실패

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`PacketStore`에서 `_packetInventoryIdx` 매핑은 각 패킷의 위치를 해당 유형의 인벤토리 배열 `_packetInventory`에서 추적합니다. 패킷을 소각할 때 함수는 마지막 패킷을 이동하여 소각된 패킷을 대체하지만 **패킷의 `_packetInventoryIdx` 매핑을 업데이트하지 않습니다**.

```solidity
function addPacketsToInventory(uint256[] calldata packetIds) external onlyRole(INVENTORY_MANAGER_ROLE) {
    uint256 length = packetIds.length;
    for (uint256 i = 0; i < length; i++) {
        --- SNIPPED ---

@>      _packetInventoryIdx[packetId] = _packetInventory[packetTypeId].length;
@>      _packetInventory[packetTypeId].push(packetId);

        emit PacketAddedToInventory(packetId, packetTypeId);
    }
}

function burnPacketFromInventory(uint256 packetId) external onlyRole(INVENTORY_MANAGER_ROLE) {
    --- SNIPPED ---

    uint256 idx = _packetInventoryIdx[packetId];

    _packetInventory[packetTypeId][idx] = _packetInventory[packetTypeId][_packetInventory[packetTypeId].length - 1];
    _packetInventory[packetTypeId].pop();
}
```

이로 인해 두 가지 문제가 발생합니다.

1. 이동된 패킷의 인덱스 매핑은 여전히 이전 위치(마지막 인덱스)를 가리킵니다.

2. 소각된 패킷의 인덱스 매핑이 남아 있습니다.

결과적으로 다음과 같은 손상된 상태가 발생합니다.

- 이동된 패킷에 대한 향후 작업은 잘못된 배열 위치에 액세스합니다.
    1) 인벤토리에 새 패킷이 추가되지 않으면 범위를 벗어난 인덱스를 가리켜 이동된 패킷의 후속 소각이 되돌려집니다.
    2) 그렇지 않으면 2개의 패킷이 동일한 인덱스를 가리킵니다.

- 소각된 패킷의 인덱스 매핑은 댕글링 포인터(dangling pointer)가 됩니다.

다음 예시 순서를 고려하십시오.

초기 상태:
```bash
_packetInventory[typeId] = [P_1, P_2, P_3]
_packetInventoryIdx = {
  P_1 => 0,
  P_2 => 1,
  P_3 => 2
}
```

패킷 `P_1` 소각:
```solidity
function burnPacketFromInventory(uint256 packetId) external {
    uint256 idx = _packetInventoryIdx[packetId]; // idx = 0
    // Move P_3 to index 0
    _packetInventory[packetTypeId][idx] = _packetInventory[packetTypeId][2];
    _packetInventory[packetTypeId].pop();
}
```

결과적으로 손상된 상태:
```bash
_packetInventory[typeId] = [P_3, P_2] // length =2 
_packetInventoryIdx = {
  P_1 => 0,  // Dangling
  P_2 => 1,
  P_3 => 2   // OOB, array length is 2 and actual position is at index 0
}
```

`P_3`를 소각하려고 하면 인덱스가 범위를 벗어나거나 새 패킷을 추가하면 인덱스 충돌이 발생하기 때문에 되돌려집니다.
```bash
_packetInventory[typeId] = [P_3, P_2, P_4] // length = 2 
_packetInventoryIdx = {
  P_1 => 0,  
  P_2 => 1,
  P_3 => 2  // Collision
  P_4 => 2  // Collision
}
```

## 권장 사항

이동된 패킷에 대한 인덱스 매핑을 업데이트하고 소각된 패킷의 매핑을 삭제하십시오.

```diff
function burnPacketFromInventory(uint256 packetId) external {
    uint256 idx = _packetInventoryIdx[packetId];

    uint256 lastPacketId = _packetInventory[packetTypeId][_packetInventory[packetTypeId].length - 1];
    _packetInventory[packetTypeId][idx] = lastPacketId;

+   _packetInventoryIdx[lastPacketId] = idx;
+   delete _packetInventoryIdx[packetId];
    _packetInventory[packetTypeId].pop();
}
```

# [H-02] `finalizeOpen()`에서 패킷 소각 상태가 재설정되지 않아 실패 발생

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`Packet.sol` 계약에서 사용자가 **INSTANT_OPEN_PACKET** 유형으로 소각을 시작하면 실행 흐름에서 트랜잭션 실패로 이어지는 문제가 발생합니다.
순서는 다음과 같습니다.
- 사용자가 `Packet.sol`에서 **INSTANT_OPEN_PACKET** burn 유형으로 `initiateBurn()`을 호출합니다.
- 패킷의 소각 상태를 **INSTANT_OPEN_PACKET**으로 설정하고 `CardAllocationPool.sol`에서 `instantOpenPacket()`을 호출합니다.
- 카드 번들을 하나만 사용할 수 있는 경우 `CardAllocationPool`은 NFT를 직접 전송하고 `finalizeOpen()`을 호출합니다(계약이 Chainlink VRF에서 무작위성을 요청하는 경우에도 문제가 여전히 존재합니다).
- `Packet.sol`의 `finalizeOpen()` 함수는 `_transfer()` 함수를 직접 사용하여 패킷을 계약으로 전송한 다음 PacketStore.sol에서 `addPacketsToInventory()`를 호출하려고 시도합니다.
- `addPacketsToInventory()` 함수는 `safeTransferFrom()`을 사용하여 패킷을 전송하려고 시도합니다.
- 그러나 패킷의 burn 유형이 여전히 **INSTANT_OPEN_PACKET**이므로 이 전송이 실패합니다.
핵심 문제는 다음과 같은 확인이 있는 `transferFrom()` 함수에 있습니다.
```solidity
function transferFrom(address from, address to, uint256 tokenId) public virtual override {
    if (_packetBurnType[tokenId] != BurnType.NONE) revert PacketFrozen();
    super.transferFrom(from, to, tokenId);
}
```
이 함수는 burn 유형이 **NONE**이 아닐 때 `PacketFrozen()` 오류와 함께 되돌려지지만, **INSTANT_OPEN_PACKET** burn 유형에 대한 `finalizeOpen()` 프로세스 중에 burn 유형이 **NONE**으로 재설정되지 않습니다.

## 권장 사항

패킷을 전송하기 전에 소각 상태를 **NONE**으로 재설정하도록 `Packet.sol` 계약의 `finalizeOpen()` 함수를 수정하십시오.
```solidity
if (burnType == BurnType.INSTANT_OPEN_PACKET) {
        // Reset burn state to NONE before transfer
        _packetBurnType[packetId] = BurnType.NONE;
        
        _transfer(ownerOf(packetId), address(this), packetId);
        this.approve(address(packetStore), packetId);

        uint256[] memory packetIds = new uint256[](1);
        packetStore.addPacketsToInventory(packetIds);
    } else {
```

# [M-01] `sendNFTs()` 실패로 인해 NFT가 고착되고 자산 손실 발생

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`CardAllocationPool` 계약에서 무작위 카드 NFT가 소유자(`packetId`의 소유자)에게 할당될 때 `sendNFTs` 함수가 선택한 카드를 전송하지 못하면 **이 카드는 계약에 영구적으로 갇히게 됩니다**.

```solidity
 function fulfillRandomWords(uint256 requestId, uint256[] memory randomWords) internal {
        //...

        // Select random cards using the provided randomness
        uint256[] memory selectedCards = selectRandomCards(cardBundles, randomWords[0]);

        // Transfer cards to owner
        try this.sendNFTS(selectedCards, request.owner) {
            RipFunPacks(packetNFTAddress).finalizeOpen(request.packetId, selectedCards, "");
            emit PacketOpenFulfilled(requestId, request.packetId, selectedCards);
        } catch {}

        //...
    }
```

`sendNFTS()` 호출은 `try/catch` 블록 내에서 발생하며, catch는 상태 변경을 되돌리거나 전송 실패한 `selectedCards`를 재할당하는 기능을 구현하지 않습니다. 결과적으로 카드는 **계약에 영구적으로 갇히게 되며** 향후 무작위 선택에서 다시 활용될 수 없습니다. 또한 상환 관리자가 이러한 카드 NFT를 복구할 수 있는 메커니즘이 없습니다.

`cardBundles`가 `packetTypeToCardBundles` 배열에서 제거되므로 이러한 카드 NFT와 관련된 실제 항목은 **영구적으로 상환할 수 없게 됩니다**.

`_distributePrize()`가 실패할 때 `SpinLottery.fulfillRandomness()` 함수에도 유사한 문제가 있습니다.

## 권장 사항

전송 실패한 카드 NFT를 `packetTypeToCardBundles` 배열에 다시 추가하는 메커니즘을 구현하거나 상환 관리자가 이러한 카드 NFT를 복구할 수 있는 메커니즘을 도입하십시오.

# [M-02] `LISTING_TYPEHASH`의 논스(nonce) 누락은 EIP-712 및 서명에 영향을 미침

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Marketplace.generateListingSignatureHash()` 함수에서 리스팅 요청에 대한 해시가 생성될 때 `nonce` 매개변수가 포함되지만, 이 논스는 `LISTING_TYPEHASH`에 도입되지 않아 `EIP-712` 준수를 위반하여 서명 유효성 검사가 실패하고 리스팅 프로세스가 규정을 준수하지 않게 되므로 리스팅이 올바르게 확인되고 처리되는 것을 차단합니다.

```solidity
    /**
     * @notice The EIP-712 type hash for listing requests.
     */
    bytes32 public constant LISTING_TYPEHASH = keccak256(
        "ListingRequest(address tokenContractAddress,uint256 tokenId,uint256 price,address acceptedCurrency,uint256 deadline,address owner,uint256 chainId)"
    );
```

```solidity
  function generateListingSignatureHash(ListingRequest calldata listing) public view returns (bytes32) {
         return _hashTypedDataV4(
             keccak256(
                 abi.encode(
                     LISTING_TYPEHASH,
                     listing.tokenContractAddress,
                     listing.tokenId,
                     listing.price,
                     listing.nonce,// @audit : not introduced in LISTING_TYPEHASH
                     acceptedCurrency,
                     listing.deadline,
                     listing.owner,
                     listing.chainId
                 )
             )
         );
     }
```

## 권장 사항

논스를 포함하도록 `LISTING_TYPEHASH`를 업데이트하십시오.

```diff
-    bytes32 public constant LISTING_TYPEHASH = keccak256("ListingRequest(address tokenContractAddress,uint256 tokenId,uint256 price,address acceptedCurrency,uint256 deadline,address owner,uint256 chainId)");
+    bytes32 public constant LISTING_TYPEHASH = keccak256("ListingRequest(address tokenContractAddress,uint256 tokenId,uint256 price,uint256 nonce,address acceptedCurrency,uint256 deadline,address owner,uint256 chainId)");

```

# [L-01] `burnFromInventory()`에서 `packetToBundle[packetId]`에 대한 정리 누락

_해결됨_

할당 관리자가 패킷 저장소에서 패킷을 제거하기 위해 `Packet.burnFromInventory()` 함수를 호출합니다. 그러나 패킷이 소각될 때 관련된 `packetToBundle[packetId]`를 삭제하지 않습니다.

```solidity
function burnFromInventory(uint256 packetId) external onlyRole(ALLOCATION_MANAGER_ROLE) {
    if (_ownerOf(packetId) == address(packetStore)) {
        packetStore.burnPacketFromInventory(packetId);
        emit PacketBurnedFromInventory(packetId);
        _burn(packetId);
    }
}
```

권장 사항:
`burnFromInventory()` 함수가 호출될 때 `packetToBundle[packetId]`가 삭제되는지 확인하십시오.

```diff
function burnFromInventory(uint256 packetId) external onlyRole(ALLOCATION_MANAGER_ROLE) {
    if (_ownerOf(packetId) == address(packetStore)) {
        packetStore.burnPacketFromInventory(packetId);
        emit PacketBurnedFromInventory(packetId);
        _burn(packetId);
+       delete packetToBundle[packetId];
    }
}
```

# [L-02] `paymentToken` 변경 시 인출 없이 `PacketStore`에 수수료가 고착됨

_해결됨_

`PacketStore.setPaymentToken()` 함수에서 아직 전송되지 않은 누적 수수료를 먼저 인출하지 않고 허용된 지불 토큰이 변경되면, 이전 지불 토큰의 수수료가 계약에 고착됩니다.

```solidity
  function setPaymentToken(
        address newTokenAddress
    ) external onlyRole(SUPER_ADMIN_ROLE) {
        if (newTokenAddress == address(0)) revert InvalidAddress();
        paymentToken = IERC20(newTokenAddress);
        emit PaymentTokenUpdated(newTokenAddress);
    }
```

권장 사항:
`paymentToken`을 변경하기 전에 `withdrawFunds()` 함수를 통해 누적된 수수료가 수집되도록 하십시오.

# [L-03] `addCardBundlesToPacketPool()`에서 소각된 `packetIds`에 대한 검증 누락

_해결됨_

상환 관리자가 특정 패킷 유형의 풀에 카드를 번들로 추가하기 위해 호출하도록 의도된 `CardAllocationPool.addCardBundlesToPacketPool()` 함수에는 제공된 `packetIds`가 추가되는 것과 동일한 `packetTypeId`에 속하는지 확인하는 검증이 없습니다.

이 검증 부족으로 인해 일관성이 없거나 잘못된 패킷 할당이 발생할 수 있습니다. 또한 악의적인 상환 관리자가 이 허점을 이용하여 `PacketStore`에 나열된 모든 패킷을 소각하여 의도하지 않은 패킷을 제거하고 시스템에 중단을 초래할 수 있는 잠재적인 보안 위험을 초래합니다.

```solidity
  function addCardBundlesToPacketPool(
        uint256 packetType,
        uint256[] memory cardBundles,
        uint256[] memory packetIds,
        bytes32 bundleProvenance
    ) external noPendingRandomness onlyRole(redeemManagerRoleId) {
        //...

        packetTypeToCardBundles[packetType].push(CardBundle({cardIds: cardBundles, bundleProvenance: bundleProvenance}));
        for (uint256 i = 0; i < cardBundles.length; i++) {
            RipFunPacks(packetNFTAddress).burnFromInventory(packetIds[i]);
            IERC721(cardNFTAddress).safeTransferFrom(msg.sender, address(this), cardBundles[i]);
        }

        //...
    }
```

권장 사항:
제공된 모든 `packetIds`가 제공된 `packetTypeId`와 연관되어 있는지 확인하는 검증 단계를 구현하십시오.

# [L-04] `addCardBundlesToPacketPool()`에서 패킷 유형 검증 누락

_해결됨_

`CardAllocationPool.addCardBundlesToPacketPool()` 함수는 상환 관리자가 특정 패킷 유형의 풀에 카드를 번들로 추가하기 위해 호출하도록 설계되었습니다. 그러나 `cardBundles`를 할당하기 전에 `packetType`이 등록된 유형인지 확인하지 않습니다. 이로 인해 등록되지 않았거나 유효하지 않은 패킷 유형에 대해 잘못된 데이터가 풀에 추가되어 일관성 없는 동작이나 잠재적인 취약점이 발생할 수 있습니다.

```solidity
 function addCardBundlesToPacketPool(
        uint256 packetType,
        uint256[] memory cardBundles,
        uint256[] memory packetIds,
        bytes32 bundleProvenance
    ) external noPendingRandomness onlyRole(redeemManagerRoleId) {
        if (cardBundles.length > maxBundleSize) revert CannotExceedMaxBundleSize();
        if (cardBundles.length != packetIds.length) revert IncorrectLength();

        packetTypeToCardBundles[packetType].push(CardBundle({cardIds: cardBundles, bundleProvenance: bundleProvenance}));
        //....
    }
```

권장 사항:
`cardBundles`를 풀에 추가하기 전에 `packetType`이 등록되어 있는지 확인하는 검증을 구현하십시오.

```diff
  function addCardBundlesToPacketPool(
         uint256 packetType,
         uint256[] memory cardBundles,
         uint256[] memory packetIds,
         bytes32 bundleProvenance
     ) external noPendingRandomness onlyRole(redeemManagerRoleId) {
         if (cardBundles.length > maxBundleSize) revert CannotExceedMaxBundleSize();
         if (cardBundles.length != packetIds.length) revert IncorrectLength();
+        packetNFTAddress.getPacketTypeInfo(packetType); // @note: it will revert if the packetType is not registerd
         packetTypeToCardBundles[packetType].push(CardBundle({cardIds: cardBundles, bundleProvenance: bundleProvenance}));
         //....
     }
```

# [L-05] 소각된 패킷에 대한 상태 정리 누락

_해결됨_

`Packet` 계약에서 NFT(Packet)가 소각될 때 상태 변수 `_packetUris[packetId]` 및 `_packetTypeIds[packetId]`는 삭제되지 않습니다. `_packetUris[packetId]` 매개변수에 대한 `tokenURI()` getter 함수는 소각되거나 존재하지 않는 NFT에 대해 되돌려지지만, **이러한 상태는 공개되어** 여전히 직접 액세스할 수 있으며, 소각되거나 존재하지 않는 NFT에 대해 유효하지 않은 데이터가 반환됩니다.

이 문제는 `finalizeOpen()`, `finalizeRedeem()`, `burnFromInventory()` 함수에 존재하며, 소각된 NFT와 관련된 상태가 올바르게 정리되지 않습니다.
```solidity
 /// @notice Maps token IDs to their packet type IDs
 /// @dev Used to determine which type a packet belongs to
 mapping(uint256 => uint256) public _packetTypeIds;

 /// @notice Maps token IDs to their packet metadata
 /// @dev Used to store packet metadata for each token
 mapping(uint256 => string) public _packetUris;
```
권장 사항:
NFT(Packet)가 소각될 때마다 `_packetUris[packetId]` 및 `_packetTypeIds[packetId]` 상태를 삭제하십시오.
```diff
 _burn(packetId);
+ delete _packetUris[packetId];
+ delete _packetTypeIds[packetId];
```

# [L-06] `PacketStore`와 `WhitelistRegistry` 계약 간의 역할 ID 충돌

_해결됨_

`PacketStore` 및 `WhitelistRegistry` 계약 모두 관련 없는 목적으로 역할 ID `6`을 정의합니다.

```solidity
//File: src/PacketStore.sol
uint256 private constant PRICE_MANAGER_ROLE = 6;

//File: src/WhitelistRegistry.sol
uint256 public constant WHITELIST_MANAGER_ROLE = 6;
```

두 계약 모두 ERC1155 토큰을 사용하여 역할을 관리하는 `RoleBasedAccessControl`을 사용합니다. 동일한 역할 ID를 공유하므로 한 역할을 부여받은 주소는 액세스 제어 시스템이 구별할 수 없기 때문에 두 역할을 모두 갖게 됩니다.

이는 최소 권한의 원칙을 위반하며 역할 관리에 혼란을 초래할 수 있습니다.

시스템의 각 고유 역할에 대해 고유한 역할 ID를 할당하는 것을 고려하십시오.

# [L-07] `onERC721Received` 누락으로 안전한 NFT 전송 및 발행 차단

_해결됨_

`SpinLottery` 계약은 소유권 확인을 통해 확인된 것처럼 상금으로 사용하기 위해 NFT를 전송하거나 발행해야 합니다.

```solidity
function addPrize(address _nftAddress, uint256 _tokenId, uint8 _rarity) external onlyRole(lotteryManagerRoleId) {
    IERC721 nft = IERC721(_nftAddress);
@>  if (nft.ownerOf(_tokenId) != address(this)) revert NFTNotOwnedByContract();

    uint256 packed = packPrize(_nftAddress, uint88(_tokenId), _rarity);
    _addPrizeToPool(_rarity, packed);

    emit PrizeAdded(_nftAddress, _tokenId, _rarity);
}
```

그러나 계약에는 ERC721의 안전한 함수에 필요한 `onERC721Received` 구현이 부족합니다. 이로 인해 안전한 방법을 사용하는 외부 통합이 NFT를 계약으로 성공적으로 보내지 못합니다.

`SpinLottery` 계약에 `onERC721Received` 인터페이스를 구현하는 것을 고려하십시오.

# [L-08] Marketplace 계약에 제안 취소 메커니즘 누락

_해결됨_

`Marketplace.sol`에는 제안자가 마감일이 만료되기 전에 제안을 취소할 수 있는 메커니즘이 없습니다. 계약은 `revokeListing()` 함수를 통해 판매자 리스팅에 대한 `revokedListings`를 구현하지만 제안에 대한 동등한 기능은 없습니다. 이는 시장 상황이 빠르게 변하거나 실수를 한 경우 요청자가 제안을 취소할 수 없으므로 보안 문제를 야기하여 빠르게 변화하는 암호화폐 시장에서 불충분한 짧은 마감일에만 의존하도록 강요합니다.

# [L-09] 서명된 리스팅 데이터에 `chargeDelegate` 매개변수 누락

_해결됨_

`Marketplace.sol` 계약에서 `buy()` 함수는 구매 비용을 지불하는 사람(호출자 또는 수신자)을 결정하는 `chargeDelegate` 매개변수를 허용하지만, 이 매개변수는 서명된 `ListingRequest` 데이터 구조에 포함되지 않습니다.
```solidity
function buy(ListingParams[] calldata listings, bool chargeDelegate) external nonReentrant {
    // ...
    address payee = chargeDelegate ? address(msg.sender) : listings[i].receiver;
    // ...
    IERC20(acceptedCurrency).safeTransferFrom(payee, listings[i].request.owner, sellerAmount);
}
```

이로 인해 이전에 마켓플레이스에서 USDC를 지출 승인한 수신자가 악의적인 호출자가 `chargeDelegate`를 `false`로 설정한 경우 마켓플레이스로 돌아올 때 예기치 않게 구매 비용을 지불할 수 있는 보안 위험이 발생합니다. 이 매개변수는 서명된 데이터의 일부가 아니므로 서명을 무효화하지 않고 지불 약정을 조작할 수 있습니다.

이 문제를 해결하려면 `ListingRequest` 구조체에 `chargeDelegate` 매개변수를 포함하십시오.

# [L-10] 초기 이벤트로 인한 조용한 오류 삼킴(Swallowing)

_해결됨_

`MarketplaceAdmin.sol`에서 여러 관리 함수는 Marketplace 계약에서 함수를 호출할 때 try/catch 패턴을 사용하지만 발생하는 오류는 조용히 무시합니다. 더 우려스러운 점은 계약이 try/catch 블록에 들어가기 전에 성공 이벤트를 방출한다는 것입니다. 즉, 기본 작업이 실패하더라도 이벤트가 방출됩니다.
이는 다음을 포함한 여러 함수에서 발생합니다.
- updateAcceptedCurrency().
- updateFeeReceiver().
- updateFees().
- updateTradeFee().
- collectFees().
- updateWhitelistRegistryAddress().
- emergencyShutdown().

이 패턴은 작업이 실패하더라도 성공을 나타내는 이벤트가 방출되므로 문제가 발생하여 잘못된 이벤트 로그가 사용자, 프론트엔드 애플리케이션 및 모니터링 시스템을 오도할 수 있습니다. 관리 함수는 실제로 실패했을 때 성공한 것처럼 보입니다.

이 문제를 해결하려면 이벤트 방출을 try 블록 내부 또는 성공적인 실행 후로 이동하여 성공 시에만 이벤트가 방출되도록 하십시오.

# [L-11] `revertBurnRequest()`가 `INSTANT_OPEN_PACKET` burn 유형을 잘못 관리함

_해결됨_

`Packet.sol`의 `revertBurnRequest()` 함수는 **INSTANT_OPEN_PACKET**을 포함한 모든 burn 유형을 되돌릴 수 있습니다. 그러나 패킷의 burn 유형이 **INSTANT_OPEN_PACKET**인 경우 `randomAllocationPool.instantOpenPacket()`을 통해 패킷 개방 프로세스가 이미 시작되었으며, 이는 burn 유형을 NONE으로 변경하여 단순히 취소할 수 없는 비동기 VRF 프로세스를 시작합니다.
이로 인해 `Packet` 계약에서 burn 유형이 `NONE`으로 재설정되는 일관성 없는 상태가 생성됩니다.
그리고 VRF 프로세스는 `CardAllocationPool` 계약에서 계속됩니다.

이 문제를 해결하려면 `fulfilled`를 true로 설정하십시오.

# [L-12] 카드가 빈 `authorizedOpenPacket()`에서 패킷 손실 위험

_해결됨_

**OPEN_PACKET** burn 유형을 처리할 때 `CardAllocationPool.sol`과 `Packet.sol` 간의 상호 작용에 치명적인 취약점이 있습니다. 사용자가 **OPEN_PACKET** 유형으로 소각을 시작하면 관리자는 `CardAllocationPool.sol`에서 `authorizedOpenPacket()`을 호출하여 특정 카드로 패킷을 열 수 있습니다.
문제는 `uthorizedOpenPacket()` 함수가 카드 배열에 적어도 하나의 카드가 포함되어 있는지 확인하지 않는다는 것입니다.
관리자가 악의적으로 또는 실수로 빈 카드 배열로 이 함수를 호출하면 여전히 `Packet.sol`에서 `finalizeOpen()`을 호출합니다.
```solidity
if (burnType == BurnType.INSTANT_OPEN_PACKET) {
    // ... INSTANT_OPEN_PACKET handling ...
} else {
    _burn(packetId);
}
```
이로 인해 카드 배열이 비어 있었기 때문에 사용자가 대가로 카드를 받지 못한 채 패킷이 영구적으로 소각됩니다. 이는 소각 과정을 시작한 사용자에게 상당한 손실입니다.

이 문제를 해결하려면 `CardAllocationPool.sol`의 `authorizedOpenPacket()` 함수를 수정하여 비어 있지 않은 카드 배열을 요구하십시오.

# [L-13] `UpdateVRFConfig()`에서 Chainlink 최소 허용치 미만의 `requestConfirmations` 허용

_인지됨_

`CardAllocationPool.sol`에서 `updateVRFConfig()` 함수는 Chainlink의 최소 요구 사항을 강제하지 않고 `requestConfirmations` 매개변수를 설정할 수 있도록 합니다.
이 함수는 `_requestConfirmations`가 0이 아닌지만 확인하지만 네트워크별 최소값은 강제하지 않습니다. Polygon Mainnet의 경우 Chainlink는 공식 문서에 기록된 대로 최소 3개의 확인을 요구합니다.

# [L-14] `configureRarity`가 가스 부족(OOG)이 되는 것을 방지하기 위해 상금에 상한선 필요

_해결됨_

`addPrize()`를 사용하여 희귀도 풀에 상금을 추가할 수 있으며 현재 희귀도 풀에 추가할 수 있는 상금에는 상한선이 없습니다. 이로 인해 `configureRarity()` 함수가 희귀도 풀의 상금 NFT를 msg.sender에게 전송하려고 할 때 가스가 부족해질 수 있습니다. 즉, 필요할 때 희귀도 풀 구성이 실패하게 되며, 이 기능은 희귀도를 비활성화/활성화하고 실패한 전송으로 인해 계약에 갇힌 NFT를 전송하는 데 시스템에 매우 중요합니다.

권장 사항:
희귀도 풀에 추가할 수 있는 최대 NFT 상금 수에 대한 안전한 상한선을 두십시오.

# [L-15] `MarketPlace.buy()`에서 판매자가 보호 장치 없이 수수료 변동성에 직면함

_해결됨_

`MarketPlace.buy()` 함수에서 각 구매에 대해 요청 가격에서 백분율 수수료가 공제됩니다. 그러나 `fees` 백분율은 리스팅이 추가된 시점과 `buy()` 함수를 통해 이행된 시점 사이에 변경될 수 있습니다. 이로 인해 판매자는 수수료 변경으로 인해 원래 수락한 가격과 크게 벗어난 가격을 받게 되어 판매자에게 금전적 손실을 초래할 수 있는 위험이 발생합니다.

```solidity
 function buy(ListingParams[] calldata listings, bool chargeDelegate) external nonReentrant {
        for (uint256 i = 0; i < listings.length; i++) {
            //...
            uint256 feeAmount = Math.mulDiv(listings[i].request.price, fees, 10000, Math.Rounding.Ceil);
            uint256 sellerAmount = listings[i].request.price - feeAmount;
            //...
            if (feeAmount > 0) IERC20(acceptedCurrency).safeTransferFrom(payee, address(this), feeAmount);

            //...
        }
    }
```
`acceptOffer()` 함수에도 유사한 문제가 있습니다.

권장 사항:
판매자가 허용 가능한 가격 편차(`minPrice`)를 정의할 수 있는 메커니즘을 구현하십시오.

```diff
 function buy(ListingParams[] calldata listings, bool chargeDelegate) external nonReentrant {
        for (uint256 i = 0; i < listings.length; i++) {
            //...
            uint256 feeAmount = Math.mulDiv(listings[i].request.price, fees, 10000, Math.Rounding.Ceil);
            uint256 sellerAmount = listings[i].request.price - feeAmount;
+           require(sellerAmount > listings[i].request.minPrice)
            // External calls after state changes
            if (sellerAmount > 0) {
                IERC20(acceptedCurrency).safeTransferFrom(payee, listings[i].request.owner, sellerAmount);
            }

            if (feeAmount > 0) IERC20(acceptedCurrency).safeTransferFrom(payee, address(this), feeAmount);

            //...
        }
    }
```
