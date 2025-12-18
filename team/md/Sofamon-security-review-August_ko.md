# 소개

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**Sofamon/sofamon-v2-contracts** 저장소에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# Sofamon V2 소개

Sofamon V2는 커밋-공개(commit-reveal) 방식을 통해 무작위 민팅을 허용하고 웨어러블을 NFT로 도입한 혁신적인 Gacha NFT 민팅 시스템입니다. SupraRNG 및 ChainlinkRNG와 같은 모듈 식 IRNG 컨트랙트를 사용하여 무작위성을 생성하고 예측할 수 없는 민팅 결과를 보장합니다.

# 리스크 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 훔치거나 잃을 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 있는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 너무 비현실적이거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (배포 전이라면 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [44f64fb0a0920cd11387d18e19e83fe6303dd89d](https://github.com/Sofamon/sofamon-v2-contracts/tree/44f64fb0a0920cd11387d18e19e83fe6303dd89d)

_수정 검토 커밋 해시_ - [355d37a537290225d81f6c11ff28a77e685f19a5](https://github.com/Sofamon/sofamon-v2-contracts/tree/355d37a537290225d81f6c11ff28a77e685f19a5)

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `SofamonWearable`
- `SofamonExchange`
- `GelatoRNG`
- `SofamonWearableFactory`

# 발견 사항

# [H-01] 다른 주소를 사용하여 서명을 재생(replay)할 수 있음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

컨트랙트의 `commitToMint` 함수는 `signer`가 서명한 서명을 다른 주소를 사용하여 재생(replay)할 수 있도록 허용합니다. 누구나 온체인에서 서명을 보고 동일한 nonce를 가진 다른 계정으로 해당 서명을 재생할 수 있습니다. 공격자는 사용자가 제출한 이전 서명을 nonce 0부터 현재까지 입력하기 시작할 수 있습니다.

```solidity
/// @param _collectionId The Id of the collection you wish to roll for
/// @param spins The amount of spins you wish to roll
/// @param signature A signature commit to the price, nonce, and minter from the authority address
///
/// @return ticketNonce The nonce to use to claim your mint
function commitToMint(uint256 _collectionId, uint256 spins, address minter, bytes memory signature) public payable returns (uint256 ticketNonce) {
    if (msg.sender != commitController) {
        uint256 nonce = userNonce[msg.sender];
        bytes32 hash = keccak256(abi.encodePacked(_collectionId, spins, nonce, msg.value, minter));
        bytes32 signedHash = hash.toEthSignedMessageHash();
        address approved = ECDSA.recover(signedHash, signature);

        if (approved != signer) {
            revert NotApproved();
        }
    }

    if (msg.value != 0) {
        payable(protocolFeeTo).transfer(msg.value);
    }

    ticketNonce = rng.rng();

    NonceData memory data = NonceData({ owner: minter, collectionId: uint128(_collectionId), spins: uint128(spins) });

    uint256 _userNonce = userNonce[msg.sender];
    userNonce[msg.sender] = _userNonce + 1;

    dataOf[ticketNonce] = data;

    emit MintCommited(msg.sender, minter, spins, msg.value, _userNonce + 1, ticketNonce);
}
```

## 권고 사항

이 문제를 완화하려면 서명된 해시에 실행자(executor)의 주소를 포함하는 것이 좋습니다. 이렇게 하면 서명이 특정 주소에 바인딩되어 다른 사람이 재생할 수 없습니다. 가능한 수정 사항은 다음과 같습니다:

```solidity
bytes32 hash = keccak256(abi.encodePacked(_collectionId, spins, nonce, msg.value, minter, msg.sender));
```

이 추가 사항은 `msg.sender` 주소가 해시에 포함되도록 하여 다른 주소에 대해 서명이 유효하지 않도록 합니다.

# [H-02] 루프 내부에서 `msg.value` 사용

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`batchCommitToMint` 함수는 루프 내에서 `commitToMint` 함수를 여러 번 호출하려고 시도하며, 매번 `batchCommitToMint`가 호출될 때 제공된 것과 동일한 `msg.value`를 사용합니다. `commitToMint` 함수 자체는 올바르게 작동하지만, 루프 내에서 호출될 때 동일한 `msg.value`가 여러 번 사용되기 때문에 실패합니다. 첫 번째 반복에서는 `msg.value`가 전송되지만, 후속 반복에서는 `msg.value`가 이미 전송되었기 때문에 자금 부족으로 인해 트랜잭션이 되돌려집니다(revert).

```solidity
/// @dev Note, this function should only be called by the commitController, otherwise it will fail with no signature
/// @param mintData An array of all the spins to spin for
/// @return ticketNonces The nonces for each spin so that the user can claim their rewards
function batchCommitToMint(MintData[] calldata mintData) public payable returns (uint256[] memory ticketNonces) {
    ticketNonces = new uint256[](mintData.length);

    for (uint256 i; i < mintData.length;) {
        MintData memory data = mintData[i];

        uint256 nonce = commitToMint(data.collectionId, data.spins, data.minter, "");
        ticketNonces[i] = nonce;

        unchecked {
            ++i;
        }
    }
}

/// @param _collectionId The Id of the collection you wish to roll for
/// @param spins The amount of spins you wish to roll
/// @param signature A signature commit to the price, nonce, and minter from the authority address
///
/// @return ticketNonce The nonce to use to claim your mint
function commitToMint(uint256 _collectionId, uint256 spins, address minter, bytes memory signature) public payable returns (uint256 ticketNonce) {
    ...
    ...
    ...
    if (msg.value != 0) {
        payable(protocolFeeTo).transfer(msg.value);
    }
    ...
    ...
    ...
}
```

## 권고 사항

이 문제를 완화하려면 각 반복마다 `msg.value`를 중복으로 계정하지 않도록 두 함수를 조정하는 것을 고려하십시오.

# [H-03] `order.amount`가 채우기(filling) 시 계산되지 않음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`fillAskOrder()` 및 `fillBidOrder()`는 `order.price`와 동일한 값으로 작동합니다.
예:

```solidity
    function fillAskOrder(FulfillOrder calldata order) public payable {
        uint256[] memory amounts = new uint256[](1);
        amounts[0] = order.amount;

        order.orderPool.swapTokenForSpecificNFTs{ value: order.price }(amounts, order.price, payable(msg.sender), false, address(0));

        emit AskFilled(order.orderPool, msg.sender, order.nftId, order.amount, order.price);
    }
```

이 경우 `order.amount`는 항상 1이어야 합니다.

여러 주문을 실행하는 또 다른 함수 `fulfillMultipleOrders()`가 있으며, 거기서는 계산이 다릅니다.

```solidity
                order.orderPool.swapTokenForSpecificNFTs{ value: order.price * order.amount }(
                    amounts, order.price * order.amount, payable(msg.sender), false, address(0)
                );
```

이것은 `order.price * order.amount`를 보냄으로써 하나 이상의 토큰과 거래할 수 있도록 하는 올바른 접근 방식입니다.

## 권고 사항

필요한 채우기(fill) 함수에서 `order.amount`를 고려하십시오.

# [M-01] `safeBatchTransferFrom` 및 승인 리디렉션을 사용하여 블랙리스트 우회

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

컨트랙트에는 특정 주소가 토큰을 전송하지 못하도록 하는 블랙리스트 기능이 포함되어 있습니다. `safeTransferFrom` 함수는 발신자와 수신자 모두 블랙리스트에 있는지 확인하지만, `safeBatchTransferFrom` 함수는 수신자만 확인합니다. 이를 통해 블랙리스트에 포함된 사용자는 `safeBatchTransferFrom`을 사용하여 제한을 우회하고 자금을 이체할 수 있습니다. 또한 승인 함수에서 `msg.sender`에 대한 블랙리스트 확인이 없기 때문에 블랙리스트에 포함된 사용자는 자신의 승인을 다른 주소로 리디렉션할 수 있습니다.

### 취약한 코드

```solidity
function safeTransferFrom(address from, address to, uint256 id, uint256 amount, bytes calldata data) public override NoBlacklist(to) NoBlacklist(from) {
    super.safeTransferFrom(from, to, id, amount, data);
}

function safeBatchTransferFrom(
    address from,
    address to,
    uint256[] calldata ids,
    uint256[] calldata amounts,
    bytes calldata data
)
    public
    override
    NoBlacklist(to)
{
    super.safeBatchTransferFrom(from, to, ids, amounts, data);
}
```

## 권고 사항

이 문제를 해결하려면 `safeBatchTransferFrom` 함수에서 발신자와 수신자 모두 블랙리스트에 있는지 확인하십시오. 또한 승인 함수에 대한 블랙리스트 확인을 구현하여 블랙리스트에 포함된 사용자가 승인을 리디렉션하지 못하도록 하십시오.

```diff
-    function safeTransferFrom(address from, address to, uint256 id, uint256 amount, bytes calldata data) public override NoBlacklist(to) NoBlacklist(from) {
+    function safeTransferFrom(address from, address to, uint256 id, uint256 amount, bytes calldata data) public override NoBlacklist(to) NoBlacklist(from) NoBlacklist(msg.sender) {
        super.safeTransferFrom(from, to, id, amount, data);
    }

    function safeBatchTransferFrom(
        address from,
        address to,
        uint256[] calldata ids,
        uint256[] calldata amounts,
        bytes calldata data
    )
        public
        override
        NoBlacklist(to)
+       NoBlacklist(from)
+       NoBlacklist(msg.sender)
    {
        super.safeBatchTransferFrom(from, to, ids, amounts, data);
    }
```

# [M-02] protocolFeeTo 업데이트

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

SofamonWearable에는 수수료 수신자를 업데이트하는 다음과 같은 함수가 있습니다.

```solidity
    function updateRoyaltyFeeTo() public {
        royaltyFeeTo = ISofamonWearableFactory(machine).protocolFeeTo();

        emit NewRoyaltyFeeTo(royaltyFeeTo);
    }
```

따라서 `royaltyFeeTo`는 항상 수동으로 업데이트해야 합니다.

몇 가지 나쁜 시나리오가 가능합니다:

1. 함수가 호출되지 않은 경우 기본적으로 `address(0)`로 설정됨 => 로열티가 0 주소로 전송됨
2. `machine`이 업데이트될 수 있음 => 로열티가 더 이상 사용되지 않는 주소로 전송됨
3. `machine`에서 `protocolFeeTo`가 업데이트될 수 있음 => 로열티가 이전 `protocolFeeTo` 주소로 전송됨

## 권고 사항

`SofamonWearable`에 주소를 저장하지 말고 항상 `ISofamonWearableFactory(machine).protocolFeeTo()`를 읽는 것을 고려하십시오. `updateRoyaltyFeeTo()`는 제거할 수 있습니다.

# [M-03] Sudoswap 래퍼가 환불을 고려하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

SofamonExchange 컨트랙트에는 Sudoswap과 상호 작용하기 위한 `fillAskOrder` 및 `fulfillMultipleOrders`와 같은 래퍼 함수가 포함되어 있습니다. 이 함수들은 Sudoswap 풀 컨트랙트의 `swapTokenForSpecificNFTs` 함수를 호출하여 NFT 구매를 용이하게 합니다. 그러나 현재 구현은 `swapTokenForSpecificNFTs`가 필요 이상의 자금이 전송된 경우 환불을 반환할 수 있다는 가능성을 고려하지 않습니다. 이러한 누락은 호출자의 자금 손실로 이어집니다.

참조: [링크](https://github.com/sudoswap/lssvm2/blob/1b18945b6c8f3e74052ffae0385bd2640d167e81/src/erc1155/LSSVMPairERC1155.sol#L80)

## 권고 사항

`swapTokenForSpecificNFTs` 함수의 잠재적 환불을 고려하도록 래퍼 함수를 수정하십시오.

# [L-01] 난수 생성기 컨트랙트를 변경하면 정직한 커밋에 대해 DoS가 발생할 수 있음

`SofamonGachaMachine` 컨트랙트에는 커밋 단계와 후속 공개(reveal) 단계를 포함하는, 웨어러블 NFT 민팅을 위한 2단계 프로세스가 포함되어 있습니다. 커밋 단계에서는 rng(난수 생성기) 컨트랙트를 사용하여 무작위성 요청이 이루어지며, 공개 단계에서는 이전 요청과 관련된 임의의 값이 rng 컨트랙트에서 검색됩니다.

`SofamonGachaMachine` 컨트랙트에는 컨트랙트 소유자가 rng 컨트랙트를 변경할 수 있는 `setRNG` 함수가 있습니다. 그러나 대기 중인 커밋(즉, 아직 공개되지 않은 커밋)이 있는 동안 이 함수가 호출되면 해당 커밋은 공개 단계를 완료할 수 없게 되어 해당 커밋을 수행한 정직한 사용자에게 사실상 서비스 거부(DoS)를 유발합니다.

rng 컨트랙트 변경을 허용하기 전에 아직 공개되지 않은 대기 중인 커밋이 있는지 확인하십시오. 대기 중인 커밋이 있는 경우 `setRNG` 함수는 트랜잭션을 되돌리고(revert) 모든 대기 중인 커밋이 공개될 때까지 rng 컨트랙트 변경을 방지해야 합니다.

# [L-02] ERC2981에 대한 잘못된 ERC165 식별자

SofamonWearable 컨트랙트는 스마트 컨트랙트에서 인터페이스 지원을 감지하는 데 사용되는 ERC165 표준을 구현합니다. 그러나 이 컨트랙트는 ERC-2981 표준의 식별자를 `0x0e89341c`로 잘못 하드코딩하고 있으며, EIP-2981에 지정된 올바른 식별자는 `0x2a55205a`입니다.

참조: [링크](https://eips.ethereum.org/EIPS/eip-2981)

ERC165 구현에서 ERC-2981에 대해 잘못된 식별자 `0x0e89341c`를 올바른 식별자 `0x2a55205a`로 대체하도록 컨트랙트 코드를 수정하십시오.

# [L-03] Solady의 ECDSA 구현이 서명 가변성(malleability)을 확인하지 않음

Solady의 ECDSA 구현에는 서명 가변성에 대한 확인이 포함되어 있지 않습니다. 현재 컨트랙트의 nonce 구현으로 인해 이를 악용할 수는 없지만, 사용자가 제출된 서명에서 또 다른 유효한 서명을 생성하는 것을 방지하기 위해 신중하게 처리하는 것이 좋습니다.

서명의 `s` 값에 하한을 적용하여 서명이 가변적이지 않도록 확인을 추가하십시오. 이는 `s`가 `secp256k1n/2`보다 작거나 같은지 확인하여 수행할 수 있습니다. 여기서 `secp256k1n`은 secp256k1 곡선의 차수(order)입니다.

예:

```solidity
    // Signature malleability check.
    if (uint256(s) > 0x7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff) {
        return false;
    }
```

이 확인을 컨트랙트에 통합하면 서명을 조작하여 원본 서명에서 또 다른 유효한 서명을 생성할 수 없도록 보장할 수 있습니다.

# [L-04] `call` 대신 `transfer` 사용

컨트랙트는 Ether를 전송하기 위해 `transfer`를 사용하는데, 이는 `transfer`가 2300의 가스 제한을 부과하기 때문에 문제가 발생할 수 있습니다. 수신자가 폴백(fallback) 또는 수신(receive) 함수에 복잡한 로직이 있는 컨트랙트인 경우 이 가스 제한이 충분하지 않을 수 있습니다. 이로 인해 트랜잭션이 예기치 않게 실패할 수 있습니다.

```solidity
if (msg.value != 0) {
    payable(protocolFeeTo).transfer(msg.value);
}
```

Ether를 전송하려면 `transfer`를 `call`로 대체하십시오. 이를 통해 수신자는 더 많은 가스를 받을 수 있으며 예기치 않은 실패를 방지할 수 있습니다.

```solidity
if (msg.value != 0) {
    (bool success, ) = payable(protocolFeeTo).call{value: msg.value}("");
    require(success, "Transfer failed");
}
```

`call`을 사용하면 컨트랙트가 더 견고해지고 복잡한 폴백 또는 수신 함수가 있는 컨트랙트로의 전송을 처리할 수 있습니다. 또한 `call`의 반환 값을 확인하여 실패한 전송을 더 효과적으로 감지하고 처리할 수 있습니다.

# [L-05] 블랙리스트에 포함된 운영자의 승인을 취소할 수 없음

`SofamonWearable.setApprovalForAll()`은 주어진 운영자가 블랙리스트에 포함된 경우 승인을 수정하는 것을 허용하지 않습니다.

```solidity
    function setApprovalForAll(address operator, bool approved) public override NoBlacklist(operator) {
        super.setApprovalForAll(operator, approved);
    }
```

따라서 블랙리스트에 포함된 운영자에 대한 승인을 취소하는 것은 불가능합니다.

`setApprovalForAll()`에 대해 `NoBlacklist(operator)` 수정자(modifier)를 제거하는 것을 고려하십시오.

# [L-06] rarities.length에 대한 확인 없음

`launchNewCollection()`은 `_wearables[]` 목록에 대한 `rarities[]`를 설정합니다.
그러나 그것들의 길이가 같은지 확인하지 않습니다.

나중에 `mintRandomNFT()`는 동일한 인덱스를 사용하여 rarities와 wearables를 모두 반복합니다:

```solidity
    function mintRandomNFT(Collection memory collection, address to, bytes32 entropy, uint256 ticketNonce) internal {
        uint16 random = uint16(uint256(entropy)) % 10_000;

        for (uint256 i; i < collection.rarities.length;) {
            if (random < collection.rarities[i]) {
                uint256 wearableId = collection.wearables[i];
                sofamonWearables.mint(to, wearableId);

                emit NftMinted(ticketNonce, wearableId, to);
                return;
            }

            unchecked {
                ++i;
            }
        }
    }
```

`launchNewCollection()`에서 `_wearables.length == _rarities.length`인지 확인하는 것을 고려하십시오.

# [L-07] rarities에 대한 온전성 검사(sanity checks) 없음

`launchNewCollection()`은 `_rarities` 값을 확인하지 않습니다. 실제로 두 가지 조건이 예상됩니다:

1. 각 `_rarities` 값은 10,000보다 작거나 같아야 합니다.
2. 일관된 확률을 위해 `i` 요소의 값은 `i-1` 요소의 값보다 높아야 합니다.

위의 확인을 추가하는 것을 고려하십시오.

# [L-08] Gelato의 응답이 없으면 자금이 묶임

투자된 커밋 `msg.value`로부터 웨어러블을 민팅하기 위해 항상 Gelato로부터 무작위성을 받을 것으로 예상됩니다.

Gelato가 응답하지 않는 경우, 주어진 커밋 nonce에 대한 자금은 관리자가 VRF 공급자를 변경하거나 자체 무작위성을 제공할 때까지 무작위성 제공을 기다리는 상태로 묶이게 됩니다.

무작위성이 의미 있는 시간 동안 제공되지 않은 경우 사용자가 커밋된 `msg.value`를 인출할 수 있도록 허용하는 것을 고려하십시오.

# [L-09] 운영자가 블랙리스트에 포함되지 않음

`SofamonWearable.setBlacklist()`는 모든 주소를 블랙리스트에 추가할 수 있습니다.
`setApprovalForAll()`은 블랙리스트에 포함된 운영자에 대한 승인을 허용하지 않습니다. 이는 블랙리스트에 포함된 운영자가 허용되어서는 안 된다는 것을 의미합니다.

그러나 블랙리스트에 추가되기 전에 승인이 부여된 경우, 이 운영자는 블랙리스트에 있더라도 자금을 보낼 수 있습니다.
문제는 `safeTransferFrom()` 및 `safeBatchTransferFrom()`이 `msg.sender`(운영자)가 차단 목록에 있는지 확인하지 않는다는 것입니다.

자금을 이체하는 함수에 대해 `NoBlacklist(msg.sender)` 수정자를 추가하는 것을 고려하십시오.

# [L-10] `onERC1155Received`를 통한 트롤링(Griefing) 공격

`revealMintResult` 함수는 컬렉션의 `mintOdds`에 정의된 확률에 따라 NFT를 민팅하는 데 사용됩니다. 민팅 함수는 수신자가 컨트랙트인 경우 수신자의 `onERC1155Received` 함수를 호출합니다. 그러나 사용자는 `onERC1155Received` 함수 내에서 되돌리기(revert)를 통해 이를 악용하여 민팅 프로세스를 방해할 수 있습니다. 이는 한 번에 여러 사용자에 대해 NFT를 민팅하는 `revealMintResults` 함수 중에 다른 사용자를 괴롭히는 데 악용될 수 있습니다. NFT를 받기로 예정된 악의적인 사용자는 전체 트랜잭션을 실패하게 만들어 다른 사용자가 NFT를 받지 못하게 할 수 있습니다.

```solidity
// https://github.com/transmissions11/solmate/blob/97bdb2003b70382996a79a406813f76417b1cf90/src/tokens/ERC1155.sol#L152-L169
function _mint(
    address to,
    uint256 id,
    uint256 amount,
    bytes memory data
) internal virtual {
    ...
    require(
        to.code.length == 0
            ? to != address(0)
            : ERC1155TokenReceiver(to).onERC1155Received(msg.sender, address(0), id, amount, data) ==
                ERC1155TokenReceiver.onERC1155Received.selector,
        "UNSAFE_RECIPIENT"
    );
}
```

```solidity
// Function in the contract
function revealMintResults(uint256[] calldata nonces) external {
    for (uint256 i; i < nonces.length;) {
        revealMintResult(nonces[i]);

        unchecked {
            ++i;
        }
    }
}
```

```solidity
function revealMintResult(uint256 ticketNonce) public {
     ...
        if (_result < collection.mintOdds) {
            mintRandomNFT(collection, data.owner, keccak256(abi.encode(specificRandomness)), ticketNonce);
        } else if (_result < collection.mintOdds + collection.otherRewardOdds) {
            emit OtherResult(ticketNonce, data.owner, _result);
        } else {
            emit NoReward(ticketNonce, data.owner);
        }
     ...
}
```

이 문제를 완화하려면 민팅 프로세스를 개별 트랜잭션으로 분리하는 것을 고려하십시오. 이렇게 하면 한 사용자의 민팅 실패가 다른 사용자에게 영향을 미치지 않습니다. 또 다른 접근 방식은 `call`을 사용하여 `onERC1155Received`를 호출하여 트랜잭션 실패를 방지하는 것입니다.

# [L-11] EIP-712 표준 미준수

해싱 및 메시지 서명에 대한 현재 구현은 유형화된 구조적 데이터의 해싱 및 서명을 위한 EIP-712 표준을 준수하지 않습니다. 이러한 미준수는 다음과 같은 여러 문제를 초래합니다:

- EIP-712를 지원하는 지갑에서 메시지가 올바르게 표시되지 않습니다.
- 서명된 데이터에 체인 ID, 컨트랙트 주소 및 함수 선택자(function selector)가 없기 때문에 서명이 다른 컨트랙트, 체인 및 함수에서 재생(replay)될 수 있습니다.

```solidity
if (msg.sender != commitController) {
    uint256 nonce = userNonce[msg.sender];
    bytes32 hash = keccak256(abi.encodePacked(_collectionId, spins, nonce, msg.value, minter));
    bytes32 signedHash = hash.toEthSignedMessageHash();
    address approved = ECDSA.recover(signedHash, signature);

    if (approved != signer) {
        revert NotApproved();
    }
}
```

EIP-712 표준을 준수하도록 구현을 조정하고 식별된 문제를 완화하십시오.

