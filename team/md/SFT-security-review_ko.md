# 소개

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의해 주세요.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**CypherLabz/SFT418V2** 저장소에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# SFT418 소개

SFT는 ERC20과 ERC721을 원자적으로 작동하는 컨트랙트 쌍으로 결합한 래퍼 표준이 있는 세미 대체 불가능 토큰(Semi-Fungible Token) 설계입니다. 이를 통해 새로운 세미 대체 불가능 토큰을 생성할 수 있을 뿐만 아니라 기존 ERC721(NFT)을 SFT로 래핑하여 NFT에 대한 DeFi 유동성을 확보할 수 있습니다.

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

_첫 번째 검토 커밋 해시_ - [805714c57a7370baff9cf57b8662abad531846c6](https://github.com/CypherLabz/SFT418V2/tree/805714c57a7370baff9cf57b8662abad531846c6)

_두 번째 검토 커밋 해시_ - [7be0d9b0cb08e4c78414e4b8fc3bfbfdeaf0e155](https://github.com/CypherLabz/SFT_Audit/commit/7be0d9b0cb08e4c78414e4b8fc3bfbfdeaf0e155)

_수정 검토 커밋 해시_ - [07329b7c13ae80c833b657aee31544da4fb75ff7](https://github.com/CypherLabz/SFT418V2/tree/07329b7c13ae80c833b657aee31544da4fb75ff7)

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `SFT418`
- `SFT418Pair`
- `SFT418Wrapper`
- `SFT418WrapperPair`
- `ChunkProcessable`
- `ChunkProcessor`
- `ChunkProcessorWithSelector`
- `Interfaces`
- `SFT20`
- `SFT418Core`
- `SFT418CoreSequential`
- `SFT418PairCore`
- `SFT721`
- `SFTInterfaceHandler`
- `SFTMetadata`

# 발견 사항

# [C-01] 잘못된 승인 검증으로 인한 NFT 탈취 (프로젝트 자체 주입 익스플로잇)

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`approve()`를 호출할 때, 승인 권한을 검증하기 위해 `_FB721Approve()`를 통해 내부적으로 일부 확인이 수행됩니다.

`_isApprovedForAll`에 대한 확인이 잘못되었습니다. 호출자가 이전에 지출자(spender)를 승인했다면 어떤 `id`에 대해서도 이를 우회할 수 있습니다.

```solidity
    function _FB721Approve(address spender_, uint256 id_, address msgSender_) internal virtual {
        address _owner = _chunkToOwners[id_].owner;

        require(
            _owner == msgSender_ || // the owner of the token is the msg.sender
@>          _isApprovedForAll[msgSender_][spender_], // the msg.sender is an approved operator
            "NOT_AUTHORIZED"
        );

        _getApproved[id_] = spender_;
        require(NFT.emitApproval(_owner, spender_, id_));
    }
```

### 개념 증명 (Proof of Concept)

이 테스트는 어떻게 누구나 어떤 사용자의 토큰이라도 훔칠 수 있는지 보여줍니다.

- `test/SFT418V2_ERC721.t.sol`에 테스트 추가
- `forge test --mt "testApproveVulnerability"` 실행

```solidity
function testApproveVulnerability() public {
    address victim = address(0xABCD);
    address attacker = address(666);

    pToken.mint(victim, 1337);
    assertEq(pToken.ownerOf(1337), victim);

    hevm.startPrank(attacker);
    pToken.setApprovalForAll(attacker, true);
    pToken.approve(attacker, 1337); // @audit vulnerable function
    pToken.transferFrom(victim, attacker, 1337);
    hevm.stopPrank();

    assertEq(pToken.ownerOf(1337), attacker);
}
```

## 권고 사항

`_isApprovedForAll` 확인을 다음과 같이 변경하세요. 이제 `owner`가 호출자를 운영자로 승인했는지 확인하는 방식에 주목하세요.

```diff
    function _FB721Approve(address spender_, uint256 id_, address msgSender_) internal virtual {
        address _owner = _chunkToOwners[id_].owner;

        require(
            _owner == msgSender_ || // the owner of the token is the msg.sender
-           _isApprovedForAll[msgSender_][spender_], // the msg.sender is an approved operator
+           _isApprovedForAll[_owner][msgSender_], // the msg.sender is an approved operator
            "NOT_AUTHORIZED"
        );

        _getApproved[id_] = spender_;
        require(NFT.emitApproval(_owner, spender_, id_));
    }
```

# [C-02] 공격자가 어떤 사용자의 NFT라도 청구 가능 (프로젝트 자체 주입 익스플로잇)

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`SFT418Core`의 `_claim()` 함수는 토큰 풀에서만 허용하려는 의도에도 불구하고 사용자가 어떤 NFT라도 청구할 수 있게 합니다:

```solidity
    /**
@>   * @notice claims an existing chunk in the token pool if the target has enough eligible chunks
     * @param target_ - the claimer
     * @param id_ - the ID to claim
     */
    function _claim(address target_, uint256 id_) internal virtual {
        uint256 _eligibleChunks = _getEligibleChunks(target_);
        require(_eligibleChunks > 0, "NOT_ENOUGH_ELIGIBLE");

        if (_chunkToOwners[id_].owner == TOKEN_POOL) {
            _ERC721Transfer(TOKEN_POOL, target_, id_);
        }

        else {
@>          _ERC721Transfer(_chunkToOwners[id_].owner, target_, id_);
        }
    }
```

따라서 사용자가 NFT를 래핑하면, 추가적인 상호 작용 없이도 공격자가 이를 "청구(claim)"하고, 언래핑하여 사용자로부터 훔칠 수 있습니다.

피해자는 그 후 `_claim()`을 호출하여 다른 NFT를 얻을 수 있지만, 동일한 NFT는 아닙니다.

`_swap()`과 같은 다른 유사한 함수가 `require(TOKEN_POOL == _chunkToOwners[targetId_].owner)` 확인을 통해 이 공격을 방지하는 방식과 비교해 보세요.

### 개념 증명 (Proof of Concept)

- `test/demo/SFT418WPairDemoV2.sol`에 청구 함수 추가:

```solidity
function claim(uint256 id_) public virtual {
    _claim(id_);
}
```

- `test/SFT418WV2.t.sol`에 테스트 추가
- `forge test --mt "testClaimVulnerability"` 실행

```solidity
function testClaimVulnerability() public {
    // Set-up

    address victim = address(0x71C714);
    address attacker1 = address(0x666);
    address attacker2 = address(0x667);

    iToken.mint(victim, 1337);
    iToken.mint(attacker1, 666);
    iToken.mint(attacker2, 667);

    hevm.startPrank(victim);
    iToken.approve(address(token), 1337);
    pToken.wrap(1337);
    hevm.stopPrank();

    hevm.startPrank(attacker1);
    iToken.approve(address(token), 666);
    pToken.wrap(666);
    hevm.stopPrank();

    hevm.startPrank(attacker2);
    iToken.approve(address(token), 667);
    pToken.wrap(667);
    hevm.stopPrank();

    // Attack

    hevm.prank(attacker1);
    token.transfer(attacker2, 1);

    hevm.prank(attacker2);
    token.transfer(attacker1, 1);

    assertEq(pToken.ownerOf(1337), victim);

    hevm.prank(attacker1);
    pToken.claim(1337);

    assertEq(pToken.ownerOf(1337), attacker1);

    hevm.prank(attacker1);
    pToken.unwrap(1337);

    assertEq(iToken.ownerOf(1337), attacker1);
}
```

## 권고 사항

토큰 풀에서만 청구를 허용하세요:

```diff
    function _claim(address target_, uint256 id_) internal virtual {
        uint256 _eligibleChunks = _getEligibleChunks(target_);
        require(_eligibleChunks > 0, "NOT_ENOUGH_ELIGIBLE");

-       if (_chunkToOwners[id_].owner == TOKEN_POOL) {
-           _ERC721Transfer(TOKEN_POOL, target_, id_);
-       }
-
-       else {
-           _ERC721Transfer(_chunkToOwners[id_].owner, target_, id_);
-       }

+       require(TOKEN_POOL == _chunkToOwners[id_].owner, "INVALID_ID");
+       _ERC721Transfer(TOKEN_POOL, target_, id_);
    }
```

# [M-01] 중복된 청크 ID

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`ChunkProcessor`에서 `pushChunk()`와 같은 청크 조작 함수는 청크 ID와 인덱스를 `uint256`에서 `uint32`로 다운캐스트(downcast)를 수행합니다. 이는 청크의 최대 수를 효과적으로 `type(uint32).max`로 줄입니다. 청크는 쌍으로 연결된/래핑된 ERC721을 나타내므로, 이는 SFT418을 기반으로 하는 NFT 공급도 유사하게 제한됨을 의미합니다.

문제는 NFT 공급을 32비트로 제한하는 것이 EIP721을 준수하지 않는다는 것입니다. EIP721은 최대 `type(uint256).max`까지의 NFT를 허용합니다. 이는 `balanceOf()`에서 계정 잔액을 나타내기 위해 `uint256`을 사용하는 것에서 분명합니다.

이 문제의 의미는 SFT418이 NFT 수가 `type(uint32).max`를 초과하는 경우에 사용될 때 제대로 작동하지 않는다는 것입니다.

이로 인해 `type(uint32).max`보다 큰 청크 ID가 32비트로 잘리면서 대체 불가능(non-fungible) 속성을 위반하여 청크 ID 중복이 발생하게 됩니다.

```Solidity
    function _pushChunk(address to_, uint256 id_) internal virtual {
        require(to_ != address(0), "INVALID_TO");

        require(_chunkToOwners[id_].owner == address(0), "CHUNK_EXISTS");

        uint256 _nextIndex = _ownerToChunkIndexes[to_].length;

        _chunkToOwners[id_] = ChunkInfo(
            to_,
            //@audit index is downcasted to uint32
            uint32(_nextIndex)
        );

         //@audit chunk ID is also downcasted to uint32
        _ownerToChunkIndexes[to_].push(uint32(id_));
    }
```

## 권고 사항

최대 `uint256` 청크 ID/인덱스를 지원하거나, 청크 ID와 총 ERC721 공급량이 `uint32`를 초과하지 않도록 보장하세요.

# [M-02] 모든 토큰이 민팅된 후 리롤(Reroll) 기능 작동 불능

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

SFT418 네이티브의 `_processChunksOnTransfer` 함수는 ERC20 토큰의 잔액 변경에 따라 주소 간에 청크를 전송하는 로직을 처리하도록 설계되었습니다. 이는 잔액 상태에 따라 토큰을 풀로 전송할지 또는 새로운 토큰을 민팅/상환할지를 결정합니다. 또한, `_reroll` 함수는 특정 조건(`maxChunks > mintedChunks`)이 충족될 경우 50%의 확률로 청크를 새로 민팅된 청크로 리롤할 수 있도록 합니다.

```solidity
function _reroll(address from_, uint256 id_) internal virtual override(SFT418Core) {
    require(from_ == _chunkToOwners[id_].owner, "INVALID_FROM");

    if (maxChunks > mintedChunks) {
        uint256 _mintRng = _getRNG(1);

        if ((_mintRng % 100) > 49) { // 0-49, 50-99
            _ERC721Transfer(from_, TOKEN_POOL, id_);
            _ERC721MintSequential(from_);
        }
        else {
            uint256 _rng = _getRNG(uint160(from_));
            uint256 _poolOwnedLength =  _ownerToChunkIndexes[TOKEN_POOL].length;
            require(_poolOwnedLength > 0, "NO_ITEMS_TO_REROLL");

            uint256 _rngIndex = _rng % _poolOwnedLength;
            uint256 _rngId = _ownerToChunkIndexes[TOKEN_POOL][_rngIndex];

            _ERC721Swap(from_, TOKEN_POOL, id_, _rngId);
        }
    }
}
```

**POC 출력**

```python
Running 1 test for test/SFTNativeExampleTest.t.sol:SFTMintTest
[PASS] testDOSReroll() (gas: 162416398)
Logs:
  mintedChunks = 1337

Test result: ok. 1 passed; 0 failed; 0 skipped; finished in 107.20ms
```

**POC 코드**

```solidity
function testDOSReroll() public {
    address a = address(this);
    uint256 tokenAmount = 1e18;
    hevm.prank(a);
    token.toggleChunkProcessing();
    hevm.prank(a);
    token.mint(a, tokenAmount);

    for (uint256 i = 0; i < 1336; i++) {
        hevm.prank(a);
        token.transfer(b, tokenAmount);
        hevm.prank(b);
        token.transfer(a, tokenAmount);
    }
    console.log("mintedChunks = %s", pToken.mintedChunks());
}
```

## 권고 사항

이 문제를 완화하기 위해 리롤 함수를 다음과 같이 조정하는 것을 고려하세요:

```diff
function _reroll(address from_, uint256 id_) internal virtual override(SFT418Core) {
    require(from_ == _chunkToOwners[id_].owner, "INVALID_FROM");
+    uint256 _mintRng = _getRNG(1);
-    if (maxChunks > mintedChunks) {
+    if ((maxChunks > mintedChunks) && (_mintRng % 100) > 49)) {
-        uint256 _mintRng = _getRNG(1);

-        if ((_mintRng % 100) > 49) { // 0-49, 50-99
            _ERC721Transfer(from_, TOKEN_POOL, id_);
            _ERC721MintSequential(from_);
        }
        else {
            uint256 _rng = _getRNG(uint160(from_));
            uint256 _poolOwnedLength =  _ownerToChunkIndexes[TOKEN_POOL].length;
            require(_poolOwnedLength > 0, "NO_ITEMS_TO_REROLL");

            uint256 _rngIndex = _rng % _poolOwnedLength;
            uint256 _rngId = _ownerToChunkIndexes[TOKEN_POOL][_rngIndex];

            _ERC721Swap(from_, TOKEN_POOL, id_, _rngId);
-        }
    }
}
```

# [L-01] `_reorderChunk`의 잘못된 확인

컨트랙트의 `_reorderChunk` 함수는 청크를 한 인덱스에서 다른 인덱스로 재정렬하도록 설계되었습니다. 그러나 문제가 있는 조건 확인으로 인해, 지정된 `indexTo_`가 사용자 청크 컬렉션의 마지막 인덱스인 경우, 함수는 재정렬을 수행하지 않고 조기에 종료됩니다.

조건문 `if (_lastIndex == indexTo_) return;`은 청크가 이미 원하는 위치에 있는지 확인하여 조취가 필요 없는 경우를 위한 것입니다. 그러나 현재는 대상 인덱스가 배열의 마지막 인덱스인지 확인하고 있으며, 이는 재정렬을 위한 유효한 위치입니다. 이로 인해 사용자가 청크를 마지막 위치로 이동하려고 할 때, 그것이 합법적인 작업임에도 불구하고 함수가 재정렬을 실행하지 않게 됩니다.

이 문제를 해결하려면, 청크의 현재 인덱스(`_currentIndex`)를 `indexTo_`와 비교하도록 조건을 수정해야 합니다. 이것들이 같다면, 청크가 이미 원하는 위치에 있으므로 함수가 반환되어야 합니다. 이 변경을 통해 사용자는 마지막 위치를 포함하여 유효한 위치로 청크를 재정렬할 수 있습니다.

```diff
    function _reorderChunk(address from_, uint256 id_, uint256 indexTo_) internal virtual {
        // from_ must be the owner of id_
        require(from_ == _chunkToOwners[id_].owner, "INVALID_OWNER");

        // The index to be reordered to must be in-bounds
        uint256 _lastIndex = _ownerToChunkIndexes[from_].length - 1;
        require(_lastIndex >= indexTo_, "OOB");

-        // If the _lastIndex and indexTo_ is the same, simply return
-        if (_lastIndex == indexTo_) return;

        // Otherwise, reorder the indexes
        uint32 _currentIndex = _chunkToOwners[id_].index;
        uint32 _idAtIndexTo = _ownerToChunkIndexes[from_][indexTo_];

+        if (_lastIndex == _currentIndex ) return;

        _ownerToChunkIndexes[from_][_currentIndex] = _idAtIndexTo;
        _ownerToChunkIndexes[from_][indexTo_] = uint32(id_);

        // Then, save the new indexes on both chunks
        _chunkToOwners[id_].index = uint32(indexTo_);
        _chunkToOwners[_idAtIndexTo].index = uint32(_currentIndex);

        // Emit a ChunkReordered Event
        emit ChunkReordered(from_, id_, _currentIndex, indexTo_);
    }
```

# [L-02] 누락된 토큰 id 존재 여부 확인

[EIP-721 사양](https://eips.ethereum.org/EIPS/eip-721#specification)은 "모든 ERC-721 준수 컨트랙트는 ERC721 인터페이스를 구현해야 한다(아래의 주의 사항에 따름)"고 명시합니다.

`getApproved()` 함수의 경우, "`_tokenId`가 유효한 NFT가 아니면 던진다(Throws)"고 명시되어 있습니다:

```solidity
    /// @notice Get the approved address for a single NFT
@>  /// @dev Throws if `_tokenId` is not a valid NFT.
    /// @param _tokenId The NFT to find the approved address for
    /// @return The approved address for this NFT, or the zero address if there is none
    function getApproved(uint256 _tokenId) external view returns (address);
```

이는 ERC-721 표준 및 이를 따를 것으로 예상되는 통합과의 호환성을 깨뜨립니다.

토큰 id가 유효한 NFT가 아닌 경우 되돌리(revert)는 것을 고려하세요:

```diff
    function _FB721GetApproved(uint256 id_) internal virtual view returns (address) {
+       require(_chunkToOwners[id_].owner != address(0), "INVALID_ID");
        return _getApproved[id_];
    }
```
