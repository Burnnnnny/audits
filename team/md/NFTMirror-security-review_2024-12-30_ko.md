# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **yuga-labs/NFTMIrror** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# NFTMirror 정보 (About NFTMirror)

NFTMirror는 사용자가 ApeChain에서 자신의 NFT 컬렉션을 미러링할 수 있게 하는 프로토콜입니다. 이는 이더리움 메인넷에 잠긴 NFT의 소유권을 반영하는 NFTShadow를 ApeChain에 배포하여 작동합니다. Beacon 계약을 통해 작동합니다. 사용자는 메인넷에 있는 원래 자산에 대한 통제권을 유지하면서 ApeChain에서 자신의 미러링된 NFT를 거래, 위임 또는 사용할 수 있습니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 희박하거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [763a1a9ddbd340f24babba19cc4c7c73c39aa4f4](https://github.com/yuga-labs/NFTMIrror/tree/763a1a9ddbd340f24babba19cc4c7c73c39aa4f4)**

**_수정 검토 커밋 해시_ - [4929802e666a375bbd95f8ceed976ff42ab4180b](https://github.com/yuga-labs/NFTMIrror/tree/4929802e666a375bbd95f8ceed976ff42ab4180b)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Beacon`
- `NFTShadow`
- `CollectionConfig`
- `MetadataReadRenderer`
- `SafeCall`
- `ShadowFactory`

# 발견 사항 (Findings)

# [C-01] 소유자에 의해 소각된 토큰을 누구나 다시 발행할 수 있음 (Anyone can re-mint a token that was burned by the owner)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

토큰의 발행은 `_beforeTokenTransfer` 훅에서 오직 비콘만이 잠긴 토큰을 전송할 수 있도록 강제함으로써 비콘 계약으로 제한됩니다. 토큰의 기본 상태는 잠김(locked)이므로 존재하지 않는 토큰은 잠긴 것으로 예상됩니다.

```solidity
    function _beforeTokenTransfer(address from, address to, uint256 tokenId) internal view override {
        if (msg.sender != BEACON_CONTRACT_ADDRESS) {
@>          if (tokenIsLocked(tokenId)) revert CallerNotBeacon();
        }
```

그러나 토큰이 잠기지 않았을 때 소유자에 의해 소각될 수 있으며, 소각된 후에도 잠금 해제 상태로 유지된다는 점이 고려되지 않았습니다. 이는 누구든지 해당 토큰을 다시 발행할 수 있게 합니다.

```solidity
    function burn(uint256 tokenId) external {
        if (tokenIsLocked(tokenId)) {
            _burn(tokenId);
        } else {
@>          _burn(msg.sender, tokenId);
        }
    }
```

#### 개념 증명 (Proof of concept)

```solidity
function testBurnAndMint() public {
    testUnlockTokens_ShadowCollection();

    vm.prank(baycShadow.ownerOf(tokenId));
    baycShadow.burn(tokenId);

    assertEq(baycShadow.tokenIsLocked(tokenId), false);
    baycShadow.mint(recipient, tokenId);
}
```

## 권장 사항

소유자에 의해 소각된 후 토큰을 잠그십시오.

```diff
    function burn(uint256 tokenId) external {
        if (tokenIsLocked(tokenId)) {
            _burn(tokenId);
        } else {
            _burn(msg.sender, tokenId);
+           _setExtraData(tokenId, LOCKED);
        }
    }
```

# [H-01] `executeCallback` 호출로 인해 기본 체인에서 소유권을 업데이트할 수 없음 (`executeCallback` call causes that ownership cannot be updated on the base chain)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`Beacon.triggerOwnershipUpdate()`가 호출되면, 대상 체인에서 토큰의 새로운 소유자를 읽어오고, 소스 체인에서 `Beacon._updateOwnership()`이 실행되어 소유권을 업데이트합니다.

이것이 NFT의 네이티브 체인에서 실행될 때, `shadowAddress`는 NFTShadow 계약이 아닌 NFT 계약의 주소입니다. 따라서 함수 끝에서 `_shadow.executeCallback(guid)`가 호출되면, NFT 계약에는 `executeCallback` 함수가 없기 때문에 트랜잭션이 되돌려집니다.

```solidity
564:    function _updateOwnership(bytes calldata _message, bytes32 guid) internal {
565:        (address shadowAddress, address[] memory staleOwners, address[] memory newOwners, uint256[] memory tokenIds) =
566:            abi.decode(_message, (address, address[], address[], uint256[]));
    (...)
575:        INFTShadow _shadow = INFTShadow(shadowAddress);
    (...)
595:        _shadow.executeCallback(guid);
596:    }
```

결과적으로 네이티브 체인에서 소유권을 업데이트할 수 없습니다.

## 권장 사항

```diff
-    _shadow.executeCallback(guid);
+    if (!isNative) _shadow.executeCallback(guid);
```

# [H-02] `releaseOnEid()`에 대한 `lzReceive()` 호출이 OOG 오류 발생 (`lzReceive()` call for `releaseOnEid()` results in OOG error)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

다른 체인에서의 섀도우 NFT 릴리스는 `NFTShadow.releaseOnEid()` 함수에서 시작됩니다. 이 함수는 다음과 같이 LayerZero 메시지에 대한 `options` 매개변수를 계산합니다:

```solidity
    uint128 private constant _BASE_OWNERSHIP_UPDATE_COST = 80_000;
    (...)
    uint128 private constant _INCREMENTAL_OWNERSHIP_UPDATE_COST = 20_000;
    (...)
    function getSendOptions(uint256[] calldata tokenIds) public pure returns (bytes memory) {
        uint128 totalGasRequired =
            _BASE_OWNERSHIP_UPDATE_COST + (_INCREMENTAL_OWNERSHIP_UPDATE_COST * uint128(tokenIds.length));

        return OptionsBuilder.newOptions().addExecutorLzReceiveOption(totalGasRequired, 0);
    }
```

문제는 대상 체인에서 `lzReceive()` 호출 실행에 사용되는 가스 제한이 불충분하다는 것입니다. 예를 들어, 대상 체인이 다른 섀도우 체인인 경우 섀도우 NFT를 발행하거나 전송해야 할 수 있습니다. 새로운 섀도우 NFT를 발행하는 데는 약 46,700 가스가 소요되고 섀도우 NFT를 전송하는 데는 약 27,300 가스가 소요됩니다. 전송 검증기 계약이 사용되는 경우 각 발행 또는 전송 전에 `validateTransfer()` 호출이 이루어지므로 그 양은 훨씬 더 높을 수 있습니다. 그러나 가스 제한 계산에서는 각 토큰 ID에 대해 20,000 가스만 추가됩니다.

이로 인해 `lzReceive()` 호출을 실행할 때 가스 초과(out-of-gas) 오류가 발생하며, 항상 더 높은 가스 제한으로 메시지 실행을 재시도해야 합니다.

## 개념 증명 (Proof of concept)

`NFTShadow.t.sol` 파일에 다음 테스트를 추가하고 `forge test --mt test_unlockShadowOOG -vvvv`를 실행하십시오.

```solidity
function test_unlockShadowOOG() public {
    uint256[] memory tokenIds = new uint256[](1);
    tokenIds[0] = tokenId;

    // Calculate gasLimit used on the lzReceive() call
    uint128 _BASE_OWNERSHIP_UPDATE_COST = 80_000;
    uint128 _INCREMENTAL_OWNERSHIP_UPDATE_COST = 20_000;
    uint128 totalGasRequired = _BASE_OWNERSHIP_UPDATE_COST + (_INCREMENTAL_OWNERSHIP_UPDATE_COST * uint128(tokenIds.length));

    vm.selectFork(apechainForkId);
    vm.startPrank(address(beacon.endpoint()));
    bytes memory transferMessage = abi.encode(bayc, recipient, tokenIds);
    Origin memory origin = Origin({srcEid: mainnetEid, sender: AddressCast.toBytes32(address(shadowLockbox)), nonce: 0});
    // DVN calls lzReceive() on the target chain
    beacon.lzReceive{ gas: totalGasRequired }(
        origin,
        bytes32(0),
        transferMessage,
        address(0),
        ""
    );
    vm.stopPrank();

    // Check that the message has been stored for retrial
    bytes32 payloadHash = beacon.payloadHashes(origin.srcEid, origin.sender, origin.nonce);
    assert(payloadHash != bytes32(0));
}
```

콘솔 로그 스니펫:

```js
    │   │   ├─ [27985] NFTShadow::unlockTokens([8903], 0x5C04911bA3a457De6FA0357b339220e4E034e8F7)
    │   │   │   └─ ← [OutOfGas] EvmError: OutOfGas
    │   │   └─ ← [Revert] EvmError: Revert
```

## 권장 사항

- `NFTShadow.getSendOptions()` 함수에서 `lzReceive()` 호출에 대한 가스 제한 계산에 사용되는 양을 늘리십시오.
- 사용자가 `NFTShadow.releaseOnEid()` 함수에서 `lzReceive()` 호출에 대한 가스 제한을 기본값 이상으로 늘릴 수 있도록 허용하십시오.

# [M-01] `NFTShadow.sol`에서 `triggerMetadataRead()`를 호출할 방법이 없음 (No way to call `triggerMetadataRead()` from `NFTShadow.sol`)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`NFTShadow.sol#tokenURI()` 함수는 `MetadataReadRenderer.sol`의 `tokenURI()`를 호출하여 토큰 ID에 대한 토큰 URI를 반환합니다.
그러나 `MetadataReadRenderer.sol` 계약에는 LayerZero 프로토콜의 **lzRead**를 사용하여 기본 컬렉션(원래 체인)에서 토큰 URI를 읽고 섀도우 컬렉션에 렌더링하는 `triggerMetadataRead()` 함수가 있습니다.

하지만 `baseCollectionAddress` 값을 결정하기 위해 `msg.sender`를 사용하기 때문입니다.

```solidity
        address baseCollectionAddress = IBeacon(beacon).shadowToBase(
            msg.sender
        );
```

`triggerMetadataRead()`는 `NFTShadow.sol`에서 호출되어야 하지만 아직 구현되지 않았습니다.
현재 구현으로는 `MetadataReadRenderer.sol`은 아무것도 할 수 없으며 `NFTShadow.sol#tokenURI()`는 기본 URI만 반환할 수 있습니다.

## 권장 사항

`MetadataReadRenderer.sol#tokenURI()`를 트리거하는 새로운 함수를 `NFTShadow.sol`에 생성하십시오.

# [M-02] 섀도우 팩토리가 작동하지 않음 (Shadow factory does not work)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

ShadowFactory의 `onlyOwner` 확인이 초기화되지 않아 소유자가 설정되지 않았습니다.

Solady의 `Ownable`은 배포 시 소유자를 자동으로 설정하지 않으며, 예를 들어 NFTShadow에서 수행되는 방식과 같이 `_initializeOwner()`를 호출해야 합니다. 결과적으로 `deployAndRegister()`를 호출하면 항상 `msg.sender`에 대해 소유자를 확인하는 부분에서 되돌려집니다.

따라서 배포 흐름이 중앙 집중화되지 않고(단일 소스를 거치지 않음), 소유자는 Shadow 인스턴스를 수동으로 배포해야 하며, 이는 `initiailize()` 프론트런닝에 취약할 수 있습니다(계약 생성과 동일한 트랜잭션에서 호출되지 않는 경우).

## 권장 사항

ShadowFactory에 생성자를 추가하고 `_initializeOwner(msg.sender)`를 호출하십시오.

# [M-03] `releaseOnEid` 호출 후 NFT가 잠길 수 있음 (NFTs can get locked after the call to `releaseOnEid`)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

사용자는 `Beacon.releaseOnEid()`를 호출하여 소스 체인에서 토큰을 잠그고 대상 체인에서 잠금 해제할 수 있습니다. 릴리스된 토큰의 양이 너무 많으면 토큰 잠금을 해제할 때 대상 체인에서 블록의 모든 가스가 소비될 수 있습니다. 이로 인해 토큰이 영구적으로 잠길 수 있습니다.

## 권장 사항

한 번에 릴리스할 수 있는 토큰의 수를 제한하십시오.

# [L-01] 확인 값에 대한 타임스탬프 0 (Zero timestamp for confirmation value)

`Beacon.sol#_buildCmd()` 함수는 확인 값을 0으로 설정합니다.

```solidity
EVMCallComputeV1 memory computeSettings = EVMCallComputeV1(
            _MAP_AND_REDUCE,
            THIS_CONTRACT_EID,
            false,
            uint64(block.timestamp),
            0,//confirmations
            address(this)
        );
```

이는 대상 체인에서 타임스탬프 최종성을 기다리는 데 필요한 확인 수입니다.
합리적인 숫자를 설정하여 이 문제를 해결할 수 있습니다.

# [L-02] `MessageCached` 이벤트가 항상 빈 이유를 방출함 (The `MessageCached` event, always emits an empty reason)

`Beacon.sol#_lzReceive()`의 `MessageCached` 이벤트는 `safeCall()` 실행이 실패한 경우 방출됩니다.

```solidity
            emit MessageCached(
                _origin.srcEid,
                _origin.sender,
                _origin.nonce,
                reason
            );
```

그러나 `safeCall()`에서 `maxCopy` 값을 0으로 설정하면 `reason`은 항상 비어 있게 됩니다.
적어도 처음 몇 바이트를 캐시하면 도움이 될 것입니다.

# [L-03] `releaseOnEid()`에서 사용자 입력 확인 (Check the user input in `releaseOnEid()`)

`Beacon.sol#releaseOnEid()`의 `msg.sender`가 섀도우 계약인 경우 `getSendOptions`를 사용하여 필요한 총 가스를 계산합니다.
그러나 컬렉션이 네이티브인 경우 호출자는 소유자여야 하며 스스로 `_options`를 구축해야 하는데, `releaseOnEid()` 함수에서 `_options` 값에 대한 확인이 없습니다. `msg.sender`는 필요한 총 가스를 잠재적으로 잘못된 값으로 설정할 수 있습니다.

이로 인해 트랜잭션이 `payloadHashes[][][]`에 실패한 메시지를 캐싱하기 전에 되돌려지는 경우, 특히 `_lzReceive()`의 이 줄에서 원본 NFT와 섀도우 NFT가 모두 영구적으로 잠길 위험이 있습니다:

```solidity
        // Calculate gas to forward, leaving some in reserve
        uint256 externalGas = gasleft() - GAS_RESERVE;
```

이 문제를 해결하려면 `NFTShadow.sol#getSendOptions()`의 유사한 로직을 사용하여 입력 값을 확인하십시오.
참고: 이는 사용자 실수로 분류되지만 영향은 무섭습니다.

# [L-04] `Beacon`의 가스 도우미 함수가 `lzReceive()`에 필요한 가스 제한을 과소평가할 수 있음 (`Beacon`'s gas helper functions can underestimate the gas limit required for `lzReceive()`)

`Beacon` 계약의 `getSendOptions()` 및 `getReadOptions()` 함수는 `lzReceive()` 호출에 대한 가스 제한을 추정하는 데 사용할 수 있는 도우미 함수입니다. 그러나 이러한 계산은 50,000으로 고정된 `GAS_RESERVE` 값을 고려하지 않습니다.

또한 `getReadOptions`는 `NFTShadow` 계약에서 수행되는 콜백 함수의 실행을 고려하지 않습니다.

이로 인해 `lzReceive()` 호출에 필요한 가스 제한이 과소평가될 수 있습니다.

# [L-05] `id`가 유효한 NFT가 아닌 경우 `tokenURI()`가 되돌리지 않음 (`tokenURI()` does not revert if the `id` is not a valid NFT)

[ERC-721](https://eips.ethereum.org/EIPS/eip-721) 명세에는 `tokenURI` 함수와 관련하여 다음과 같이 명시되어 있습니다: `_tokenId가 유효한 NFT가 아닌 경우 예외를 발생시킨다`.

그러나 `NFTShadows`의 `tokenURI()` 함수는 `id`가 유효한 NFT인지 확인하지 않으므로 유효하지 않은 `id`로 함수를 호출하고 응답을 받는 것이 가능합니다.

토큰 URI를 반환하기 전에 `id`가 유효한 NFT인지 확인하는 검사를 추가하는 것을 고려하십시오.

```diff
    function tokenURI(uint256 id) public view override returns (string memory) {
+       if (!_exists(id)) revert TokenDoesNotExist();
+
        if (metadataRenderer != address(0)) {
            return ERC721(metadataRenderer).tokenURI(id);
        }

        return LibString.concat(_baseTokenUri, LibString.toString(id));
    }
```

# [L-06] `multicallData`에 빈 데이터가 포함된 경우 `multicall()`이 되돌려짐 (`multicall()` will revert if empty data is included in the `multicallData`)

NFT의 기본 체인에서 소유권 업데이트가 트리거되면 `_updateDelegations()` 응답이 실행됩니다. 이 함수에서 `multicallData`는 이전 소유자와 새 소유자에 대한 토큰 위임을 업데이트하는 호출로 채워진 다음 `multicall()`이 호출됩니다.

```solidity
    function _updateDelegations(bytes calldata _message) internal {
        // message should now be an abi encoded array of abi encoded (address, address, address, uint256) tuples
        (address collectionAddress, address[] memory staleOwners, address[] memory newOwners, uint256[] memory tokenIds)
        = abi.decode(_message, (address, address[], address[], uint256[]));
@>      bytes[] memory multicallData = new bytes[](tokenIds.length * 2);

@>      for (uint256 i = 0; i < tokenIds.length; ++i) {
            address staleOwner = staleOwners[i];
            address newOwner = newOwners[i];
            uint256 tokenId = tokenIds[i];

            if (staleOwner != address(0)) {
                multicallData[i] = abi.encodeWithSelector(
                    IDelegateRegistry.delegateERC721.selector,
                    staleOwner,
                    collectionAddress,
                    tokenId,
                    _GLOBAL_RIGHTS_WITH_MAX_EXPIRY,
                    false
                );
            }

@>          if (IERC721(collectionAddress).ownerOf(tokenId) == address(this)) {
                multicallData[i + tokenIds.length] = abi.encodeWithSelector(
                    IDelegateRegistry.delegateERC721.selector,
                    newOwner,
                    collectionAddress,
                    tokenId,
                    _GLOBAL_RIGHTS_WITH_MAX_EXPIRY,
                    true
                );

                delegatedOwners[collectionAddress][tokenId] = newOwner;
            } else {
                // if token is not owned by this contract, there should be no delegation
                delegatedOwners[collectionAddress][tokenId] = address(0);
            }
        }

@>      IDelegateRegistry(DELEGATE_REGISTRY).multicall(multicallData);
    }
```

코드는 `multicallData` 배열에 포함할 호출을 결정하기 위해 조건문을 적용합니다. 여기서 주목해야 할 중요한 점은 호출 중 어느 것이라도 `multicallData` 배열에 채워지지 않으면 `multicall()`이 되돌려져 소유권 업데이트가 실패한다는 것입니다.

이런 일이 발생하려면 `staleOwner`(`delegatedOwners`에서 가져옴)가 `address(0)`이거나 `IERC721(collectionAddress).ownerOf(tokenId)`가 비콘 주소와 달라야 합니다. 기본 체인에서의 성공적인 소유권 업데이트는 섀도우 NFT가 잠금 해제되었음을 의미하고, 따라서 기본 체인의 NFT는 잠겨 있음을 의미하므로 이 두 가지 조건은 절대 발생해서는 안 됩니다.

따라서 조건문은 항상 참이므로 아무런 효과가 없습니다. 그러나 향후 코드 변경의 경우 `multicallData` 배열이 모든 호출로 채워지지 않으면 `multicall()`이 되돌려질 가능성을 고려해야 합니다. 미래의 어느 시점에 조건이 충족되지 않을 수 있다고 예상되는 경우, `multicallData` 배열의 크기를 채워진 호출만 포함하도록 줄여야 합니다.

# [L-07] 소유권 상태가 최신 상태임을 보장할 수 없음 (Ownership status cannot be guaranteed to be up-to-date)

프로토콜의 주요 목적은 계약이 NFT의 네이티브 체인과 다른 체인에서 NFT의 소유권을 읽을 수 있도록 허용하는 것입니다. 이는 토큰 소유자에게 다른 체인에서의 에어드랍, 거버넌스, 보상 또는 기타 혜택에 대한 액세스 권한을 부여하는 데 사용될 수 있습니다.

소유권 업데이트는 `triggerOwnershipUpdate()`를 호출하여 `Beacon` 계약에서 트리거됩니다. 그러나 크로스 체인 통신의 특성상 대상 체인에서 응답을 받을 때까지 항상 지연이 발생합니다. 이는 소유권 상태가 최신 상태임을 보장할 수 없다는 생각을 불러일으킵니다. 이로 인해 이전 소유자가 부당하게 NFT의 소유자로 간주되는 상황이 발생할 수 있습니다.

# [L-08] 소유자가 많은 수의 위임을 가진 경우 함수가 `address(0)`을 반환할 수 있음 (Function can return `address(0)` if the owner has a large number of delegations)

`Beacon` 계약에서 `unlockedExclusiveOwnerByRights()` 함수는 소유권 업데이트의 대상 체인에서 실행되며 위임 확인자(delegate resolver)로부터 토큰의 소유자를 가져옵니다.

```solidity
    try IExclusiveDelegateResolver(EXCLUSIVE_DELEGATE_RESOLVER_ADDRESS).exclusiveOwnerByRights(
        _collectionAddress, tokenId, SHADOW_TOKEN_RIGHTS
    ) returns (address owner) {
        return owner;
    } catch {
        return address(0);
    }
```

`exclusiveOwnerByRights()` 함수는 NFT에 대한 소유자의 모든 위임을 반복합니다. 소유자가 많은 수의 위임을 가지고 있으면 가스 고갈이 발생하여 `address(0)`이 반환될 수 있습니다. 이로 인해 소스 체인에서 섀도우 토큰이 소각됩니다.

`exclusiveOwnerByRights()` 호출이 되돌려지는 경우, 존재하는 경우 `IERC721(collectionAddress).ownerOf(tokenId)`를 반환하는 것을 고려하십시오. 또한 `exclusiveOwnerByRights()` 함수에 전달되는 가스 양을 제한하여 `unlockedExclusiveOwnerByRights()`가 항상 주소를 반환하도록 하십시오.

# [L-09] `initialize` 함수의 잘못된 함수 서명 (Incorrect function signature in `initialize` function)

`INFTShadow` 인터페이스의 `initialize` 함수 서명이 잘못되었습니다. 마지막 매개변수인 `address _metadataRenderer`가 누락되었습니다. 즉, 인터페이스를 사용하여 `NFTShadow` 계약의 `initialize` 함수를 호출할 수 없습니다.

