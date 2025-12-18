# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **0xgarnish/gigaverse-contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Gigaverse 정보 (About Gigaverse)

Gigaverse는 플레이어가 던전 탐험 RPG를 탐험하고 게임 데이터베이스에 저장된 아이템을 수집하고 소비하는 게임입니다. ImportExportSystem을 사용하여 온체인에서 아이템을 가져오거나 내보낼 수 있는 기능을 갖추고 있습니다. SERVER_JUDGE(게임 서버) 및 MANAGER(관리자)와 같은 역할이 다양한 상호 작용을 관리하며, 플레이어는 계정 생성을 통해 인증하고 결국 NFT 소유권 또는 ETH를 기반으로 계정을 발행합니다.
이 시스템은 스마트 계약, 인덱서 및 게임 서비스를 연결하여 온체인 및 오프체인 데이터를 기록하고 검색함으로써 사용자 계정, 게임 자산 및 상호 작용을 관리합니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

_검토 커밋 해시_ - [9ab59658213f842741f3451265659eee39138dcf](https://github.com/0xgarnish/gigaverse-contracts/tree/9ab59658213f842741f3451265659eee39138dcf)

_수정 검토 커밋 해시_ - [e9cf5c47deb344a0e3dc770208502db8d4a10e31](https://github.com/0xgarnish/gigaverse-contracts/tree/e9cf5c47deb344a0e3dc770208502db8d4a10e31)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `ImportExportSystem`
- `AccountSystem`
- `ColumnConstants`
- `RoleConstants`
- `GameRegistry`
- `GameRegistryConsumer`
- `ColumnInitializer`
- `DataStore`
- `DataTable`
- `DataTypes`
- `GameItems`
- `GameNFT`
- `GigaNameNFT`
- `GigaNameNFTBeforeUpdateHandler`
- `GigaNoobNFT`
- `GigaNoobNFTBeforeUpdateHandler`

# 발견 사항 (Findings)

# [H-01] `MINT_COUNT_CID` 및 `BURN_COUNT_CID` 미업데이트 (Not updating `MINT_COUNT_CID` and `BURN_COUNT_CID`)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`GameItems` 계약은 게임 아이템의 일괄 발행(mintBatch) 및 소각(burnBatch)을 위한 `mintBatch` 및 `burnBatch` 함수를 제공합니다.

```solidity
    function mintBatch(
        address to,
        uint256[] memory ids,
        uint256[] memory amounts,
        bytes memory data
    ) external onlyRole(MINTER_ROLE) whenNotPaused {
        _mintBatch(to, ids, amounts, data);
    }

    function mintBatch(
        address to,
        uint256[] memory ids,
        uint256[] memory amounts
    ) external onlyRole(MINTER_ROLE) whenNotPaused {
        _mintBatch(to, ids, amounts, "");
    }

    function burnBatch(
        address from,
        uint256[] memory ids,
        uint256[] memory amounts
    ) external onlyRole(GAME_LOGIC_CONTRACT_ROLE) whenNotPaused {
        _burnBatch(from, ids, amounts);
    }
```

그러나 일괄 처리 중에 `MINT_COUNT_CID` 및 `BURN_COUNT_CID`가 업데이트되지 않아 공급 계산이 잘못될 수 있습니다.

## 권장 사항

일괄 작업에서 `MINT_COUNT_CID` 및 `BURN_COUNT_CID`를 처리하는 로직을 추가해야 합니다.

# [H-02] `update()` 함수에 대한 무단 접근 (Unauthorized access to `update()` function)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`GigaNameNFTBeforeUpdateHandler` 및 `GigaNoobNFTBeforeUpdateHandler` 핸들러 계약은 **GigaNameNFT** 또는 **GigaNoobNFT** 토큰이 발행, 전송 또는 소각될 때 `GameNFT._update()` 함수에 의해 호출됩니다. 여기서 이러한 핸들러 계약은 새 소유자 데이터를 저장하고 데이터 스토어에서 이전 소유자 데이터를 제거하기 위해 `AccountSystem` 계약에 부여된 `GAME_LOGIC_CONTRACT_ROLE`에 의존합니다.
그러나 이러한 핸들러 계약의 `update()` 함수는 보호되지 않으며 누구나 액세스할 수 있습니다. 이를 통해 누구나 이미 발행된 토큰에 대해 `update()` 함수를 호출하여 데이터 스토어에서 원래 소유자의 기록을 제거하고 새 소유자를 설정할 수 있습니다. 이로 인해 새 소유자는 발행 프로세스가 **"User already has an account"** 오류와 함께 되돌려지므로 **AccountSystem.mintWithEth()**를 통해 **GigaNoobNFT**를 발행할 수 없게 됩니다.

```solidity
function update(
        address tokenContract,
        address to,
        uint256 tokenId,
        address //auth
    ) external override {
        //...
    }

```

## 권장 사항

`GigaNameNFTBeforeUpdateHandler.update()` 함수의 액세스를 `GigaNameNFT` 계약으로 제한하십시오.

# [H-03] 유효하지 않은 검사로 인해 소울바운드 토큰을 발행하거나 소각할 수 없음 (Soulbound tokens cannot be minted or burnt due to an invalid check)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`GameNFT` 계약은 `GigaNoobNFT` 및 `GigaNameNFT` 계약에 의해 상속되며, 토큰이 발행, 전송 또는 소각될 때 상속된 `_update()` 함수(`ERC721._update()`를 재정의)가 호출됩니다. 그러나 함수에는 토큰이 소울바운드인지 확인할 때 유효하지 않은 검사가 포함되어 있어 소울바운드 토큰이 발행될 때 검사가 발행/소각 프로세스를 완료하지 못하게 잘못 막습니다:

```solidity
function _update(
        address to,
        uint256 tokenId,
        address auth
    )
        internal
        virtual
        override(
            ERC721
        ) returns (address)
    {

         if (beforeUpdateHandler != address(0)) {
            IERC721UpdateHandler(
                    beforeUpdateHandler
                ).update(
                address(this),
                to,
                tokenId,
                auth
            );
        }

        address prevOwner = _ownerOf(tokenId);

        //...
        bool isSoulbound = getDocBoolValue(tokenId, IS_SOULBOUND_CID);
        require(!isSoulbound, "GameNFT: Token is soulbound");
        //...
    }

```

## 권장 사항

다음 검사를 추가하도록 `GameNFT._update()` 함수를 업데이트하십시오:

- 토큰 발행 시: 이전 소유자가 `address(0)`인지 확인하십시오(이전 소유자가 없음을 나타냄).
- 토큰 소각 시: 수신자 주소(to)가 `address(0)`인지 확인하십시오(토큰이 소각됨을 나타냄).

# [H-04] `GigaNameNFT` 계약에 묶인 ETH (Stuck ETH in `GigaNameNFT` contract)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`GigaNameNFT` 계약은 발행자 역할이 `mintUsername()`을 호출하여 플레이어를 위한 **GigaNameNFT**를 발행할 때 ETH를 받도록 되어 있습니다. 그러나 이 계약은 관리자가 수집된 ETH를 인출할 수 있는 메커니즘을 구현하지 않았으며(부모 `GameNFT` 계약도 마찬가지임), 이로 인해 수집된 ETH가 계약에 영구적으로 잠기게 됩니다.

## 권장 사항

수집된 ETH에 대한 인출 메커니즘을 구현하도록 `GigaNameNFT` 계약을 업데이트하십시오.

# [H-05] `mintUsername()`으로 발행 시 사용자 이름 가격이 0임 (Zero Username price when it is minted with `mintUsername()`)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`AccountSystem` 계약은 사용자가 `mintWithEth` 함수를 통해 이더를 지불하여 새 계정을 발행할 수 있도록 합니다. 이 함수는 `AccountSystem` 테이블의 `ETH_MINT_PRICE_CID` 열에 저장된 발행 가격을 확인하여 지불 요구 사항을 올바르게 시행합니다. 관리자는 AccountSystem 계약의 `setMintPrice` 함수를 사용하여 이 가격을 설정할 수 있습니다.

그러나 `GigaNameNFT` 계약의 `mintUsername` 함수를 호출하여 무료로 계정을 발행할 수 있는 대체 경로가 있습니다. 이 함수는 또한 `ETH_MINT_PRICE_CID` 값에 대한 발행 가격을 확인하지만, 기본적으로 값이 0으로 설정된 `GigaNameNFT` 테이블을 참조합니다. 또한 `GigaNameNFT` 계약에는 테이블에서 발행 가격을 설정하는 함수가 없어 영구적으로 0으로 유지됩니다.

```solidity
    function mintUsername(address to, string memory username) external payable {
        require(msg.value >= getTableUint256Value(ETH_MINT_PRICE_CID), "Insufficient payment");
        confirmMint(to, username);
    }
```

## 권장 사항

`AccountSystem` 및 `GigaNameNFT` 계약이 모두 AccountSystem 테이블의 동일한 가격을 참조하도록 하십시오.

# [M-01] NFT 소각 시 특정 등록 데이터 정리 누락 (Missing cleanup of certain registered data when burning an NFT)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`GigaNameNFT`의 경우 발행 시 `IS_GIGA_NAME_CID`, `NAME_CID` 및 `INITIALIZED_CID`가 설정됩니다. 그러나 이러한 데이터 항목은 NFT 소각 시 제거되지 않습니다.

```solidity
    function _initializeTraits(uint256 tokenId, string memory username) internal nonReentrant onlyRole(GAME_LOGIC_CONTRACT_ROLE) {
        require(validateUsername(username), "Invalid username");
        require(tokenId == uint256(keccak256(abi.encodePacked(username))), "Token ID does not match name");

        _setDocBoolValue(tokenId, IS_GIGA_NAME_CID, true);
        validateUsername(username);
        _setDocStringValue(tokenId, NAME_CID, username);
    }
```

```solidity
    function _safeMint(address to, uint256 tokenId, bytes memory data) internal override {
        ...
        // Conditionally initialize traits
        if (getDocBoolValue(tokenId, INITIALIZED_CID) == false) {
            _initializeTraits(tokenId);
            _setDocBoolValue(tokenId, INITIALIZED_CID, true);
        }
    }
```

`GigaNoobNFT`의 경우 발행 중 `IS_NOOB_CID`, `LEVEL_CID` 및 `INITIALIZED_CID`가 설정됩니다. 그러나 이러한 데이터 항목은 NFT 소각 시 제거되지 않습니다.

```solidity
    function _initializeTraits(uint256 tokenId) internal override {
        _setDocBoolValue(tokenId, IS_NOOB_CID, true);
        _setDocUint256Value(tokenId, LEVEL_CID, 1);
    }
```

## 권장 사항

파기 시 테이블에서 발행 중에 설정된 관련 데이터를 지우는 것이 좋습니다.

# [M-02] `mintUsername()`은 발행자 역할로만 호출할 수 없음 (`mintUsername()` cannot be called by the minter role only)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`GigaNameNFT.mintUsername()` 함수는 플레이어를 위한 `GigaNameNFT`를 발행하기 위해 `MINTER_ROLE`(`confirmMint()`에서 확인된 대로)에 의해 호출되도록 의도되었습니다. 그러나 호출자가 `GAME_LOGIC_CONTRACT_ROLE`을 보유하지 않으면 `_initializeTraits()`는 해당 역할이 있는 주소에서만 액세스할 수 있으므로 호출이 실패합니다. `GAME_LOGIC_CONTRACT_ROLE`은 시스템 계약에만 부여된다는 것을 알면; 이는 `mintUsername()` 함수가 EOA에 의해 호출됨을 의미합니다:

```javascript
  function mintUsername(address to, string memory username) external payable {
        require(
            msg.value >= getTableUint256Value(ETH_MINT_PRICE_CID),
            "Insufficient payment"
        );
        confirmMint(to, username);
    }
```

```javascript
  function confirmMint(
        address to,
        string memory username
    ) public onlyRole(MINTER_ROLE) returns (uint256) {
        uint256 tokenId = uint256(keccak256(abi.encodePacked(username)));
        _initializeTraits(tokenId, username);
        //...
    }
```

```javascript
  function _initializeTraits(
        uint256 tokenId,
        string memory username
    ) internal nonReentrant onlyRole(GAME_LOGIC_CONTRACT_ROLE) {
        //...
    }
```

## 권장 사항

`AccountSystem` 계약이 `MINTER_ROLE` 및 `GAME_LOGIC_CONTRACT_ROLE`을 모두 보유하므로; 플레이어가 자신의 **GigaNameNFT**를 발행할 수 있는 `mintUsername()` 함수를 구현하도록 `AccountSystem`을 업데이트하십시오.

# [M-03] 최대 공급 시행 문제 (Max supply enforcement issues)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`GigaNameNFT` 계약은 현재 두 가지 주요 문제로 인해 무제한 발행을 허용합니다:

1. `confirmMint`에 `maxSupply` 확인이 없음: 이 함수는 총 발행된 토큰 수를 공급 제한과 대조하여 확인하지 않아 무한 토큰 생성을 허용합니다.

2. `mint` 함수는 `GameNFT._safeMint`를 호출하는데, 이는 토큰 ID가 순차적이고 총 공급량을 나타낸다고 가정하지만, 이 GigaNameNFT는 사용자 이름을 해싱하여 생성된 비순차적 ID를 사용합니다.

```solidity
    function _safeMint(address to, uint256 tokenId, bytes memory data) internal override {
        uint256 _maxSupply = maxSupply();
        if (_maxSupply != 0 && tokenId > _maxSupply) {
            revert TokenIdExceedsMaxSupply();
        }
```

3. `GameNFT._safeMint`는 총 공급량을 계산할 때 소각된 토큰을 고려하지 않습니다.

`maxSupply`가 현재 0(무한 공급)으로 설정되어 있지만, 이는 유한한 `maxSupply`가 도입될 수 있는 향후 버전에 잠재적인 문제입니다. 해결되지 않으면 이러한 문제로 인해 과잉 발행이 발생하거나 공급 제한이 잘못 시행될 수 있습니다.

## 권장 사항

`totalSupply` 카운터를 도입하여 발행된 토큰을 정확하게 추적하고 순차적 토큰 ID를 가정하지 않고 소각된 토큰을 고려하여 `confirmMint` 및 `_safeMint` 모두에서 `maxSupply`를 시행하십시오.

# [M-04] 업데이트 함수의 부적절한 주소 유효성 검사 (Improper address validation in update function)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`GigaNameNFTBeforeUpdateHandler` 계약에서 `update` 함수는 `to` 주소가 `address(0)`인지 제대로 확인하지 않으며, 이는 NFT가 소각되는 경우를 처리하는 데 중요합니다.
현재 코드는 to가 `address(0)`인지 확인하지 않고 to에 대한 사용자 이름을 설정하려고 시도합니다.
이로 인해 `AccountSystem`에서 일관성이 없거나 유효하지 않은 상태가 발생할 수 있습니다.

## 권장 사항

```solidity
function update(
    address tokenContract,
    address to,
    uint256 tokenId,
    address //auth
) external override {
    IAccountSystem accountSystem = IAccountSystem(_gameRegistry.getSystem(ACCOUNT_SYSTEM_ID));
    IGigaNameNFT gigaNameNFT = IGigaNameNFT(tokenContract);

    if (gigaNameNFT.exists(tokenId)) {
        address prevOwner = gigaNameNFT.ownerOf(tokenId);
        if (prevOwner != address(0) && accountSystem.getPlayerUsernameId(prevOwner) == tokenId) {
            accountSystem.removeUsername(prevOwner);
        }
    }

    // Add a check to ensure `to` is not the zero address
    if (to != address(0)) {
        if (accountSystem.getPlayerUsernameId(to) == 0) {
            accountSystem.setUsername(to, tokenId);
        }
    }
}
```

# [M-05] `mintWithEth()` 및 `mintUsername()`이 추가 ETH를 반환하지 않음 (`mintWithEth()` and `mintUsername()` does not return extra ETH)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`AccountSystem` 계약의 `mintWithEth` 함수는 사용자가 이더를 지불하여 발행할 수 있도록 합니다. 그러나 사용자가 필요한 `mintPrice`보다 더 많은 이더를 보내면 계약은 초과 금액을 환불하지 않습니다. 이로 인해 사용자는 의도치 않게 추가 이더를 잃게 됩니다.

```solidity
    function mintWithEth(string memory _username) override external payable whenNotPaused {
        uint256 _mintPrice = mintPrice();
        require(_mintPrice > 0, "Mint price not set");
        require(msg.value >= _mintPrice, "Insufficient payment");
        _mint(msg.sender, _username);
    }
```

`GigaNameNFT.mintUsername()` 함수에서도 마찬가지입니다.

## 권장 사항

두 함수 모두에서 사용자에게 초과 이더를 환불하십시오.
예를 들어 `mintWithEth`에서:

```solidity
function mintWithEth(string memory _username) external payable whenNotPaused {
    uint256 _mintPrice = mintPrice();
    require(_mintPrice > 0, "Mint price not set");
    require(msg.value >= _mintPrice, "Insufficient payment");

    _mint(msg.sender, _username);

    uint256 excess = msg.value - _mintPrice; // Calculate excess Ether
    if (excess > 0) {
        payable(msg.sender).transfer(excess); // Refund excess Ether
    }
}
```

또는 엄격한 `msg.value` 금액을 요구하십시오:

```diff
    function mintWithEth(string memory _username) override external payable whenNotPaused {
        uint256 _mintPrice = mintPrice();
        require(_mintPrice > 0, "Mint price not set");
-       require(msg.value >= _mintPrice, "Insufficient payment");
+       require(msg.value == _mintPrice, "Insufficient payment");
        _mint(msg.sender, _username);
    }
```

```diff
    function mintUsername(address to, string memory username) external payable {
        require(
-           msg.value >= getTableUint256Value(ETH_MINT_PRICE_CID),
+           msg.value == getTableUint256Value(ETH_MINT_PRICE_CID),
            "Insufficient payment"
        );
        confirmMint(to, username);
    }
```

# [M-06] `maxSupply` 확인을 `mintBatch`로 우회할 수 있음 (`maxSupply` check can be bypassed by `mintBatch`)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`mint` 함수는 내부적으로 `_safeMint`를 호출하여 총 발행 금액이 토큰 유형에 대해 정의된 공급 제한을 초과하지 않도록 `maxSupply` 확인을 수행합니다. 그러나 mintBatch 함수는 유사한 `maxSupply` 확인을 수행하지 않고 `_mintBatch`를 직접 호출합니다. 이를 통해 사용자는 `mintBatch` 함수를 사용하여 토큰을 발행함으로써 `maxSupply` 제한을 우회할 수 있으며, 이는 토큰 유형의 잠재적인 과잉 발행으로 이어질 수 있습니다.

## 권장 사항

`mintBatch` 함수를 수정하여 `_safeMint`의 로직과 유사하게 ids 배열의 각 토큰 유형에 대한 `maxSupply` 확인을 포함하십시오.

# [L-01] `confirmMint()`가 `INITIALIZED_CID`를 초기화하지 않음 (`confirmMint()` does not initialize the `INITIALIZED_CID`)

`confirmMint` 함수는 `_mint`를 호출하여 NameNFT를 생성합니다.

```solidity
    function confirmMint(address to, string memory username) public onlyRole(MINTER_ROLE) returns (uint256) {
        uint256 tokenId = uint256(keccak256(abi.encodePacked(username)));
        _initializeTraits(tokenId, username);
        _mint(to, uint256(keccak256(abi.encodePacked(username))));
        return tokenId;
    }
```

그러나 `INITIALIZED_CID`는 `_safeMint` 함수가 호출될 때만 초기화됩니다.
이를 설정하려면 `MANAGER_ROLE`이 `setTraitsInitialized`를 별도로 호출해야 합니다.

# [L-02] 게임 NFT를 소각하면 `address(0)`의 잔액이 증가함 (Burning Game NFTs will increase the balance of the `address(0)`)

GameNFT 계약은 게임 내 NFT의 기본 추상 계약이며, 재정의된 `_update` 함수는 Soulbound NFT 로직을 처리합니다.

```solidity
    function _update(
        address to,
        uint256 tokenId,
        address auth
    )
        internal
        virtual
        override(
            ERC721
        ) returns (address)
    {
        ...
        address result = super._update(to, tokenId, auth);
        _incrementAmount(getAccountKey(to), BALANCE_CID, 1);
        ...
    }
```

그러나 NFT를 소각할 때 `_incrementAmount` 호출은 예상치 못하게 `dataStore`에서 address(0)의 잔액을 증가시킵니다.
`to` 매개변수가 `address(0)`인 경우 앞서 언급한 로직이 실행되지 않도록 하는 것이 좋습니다.

# [L-03] `burn()` 함수가 시스템 내에서 액세스할 수 없음 (`burn()` functions are not accessible within the system)

`GigaNameNFT.burn()` 및 `GigaNoobNFT.burn()` 함수는 `GAME_LOGIC_CONTRACT_ROLE`이 플레이어의 이름 및 초보자 NFT를 소각하기 위해 호출하도록 의도되었지만, **이 역할은 시스템 계약에 의해서만 보유/할당될 수 있으므로**; 시스템 계약 중 어느 것도 사용자가 호출할 수 있도록 이러한 함수를 구현하지 않은 것으로 확인되었습니다:

```solidity
//GigaNoobNFT
    function burn(
        uint256 id
    ) external onlyRole(GAME_LOGIC_CONTRACT_ROLE) whenNotPaused {
        _burn(id);
    }
```

```solidity
//GigaNameNFT
    function burn(
        uint256 id
    ) external onlyRole(GAME_LOGIC_CONTRACT_ROLE) whenNotPaused {
        _burn(id);
    }
```

권장 사항: 사용자가 자신의 NFT를 소각할 수 있도록 `AccountSystem` 계약에 함수를 추가하십시오.

# [L-04] `_safeMint()` 대신 `_mint()` 사용 (Using `_mint()` instead of `_safeMint()`)

`GigaNameNFT.confirmMint()` 함수는 `_safeMint()` 대신 `_mint()` 함수를 사용하는데, `_mint()`는 ERC721 수신자 준수를 확인하지 않으므로 플레이어가 계약을 통해 게임과 상호 작용하는 경우 문제가 발생할 수 있습니다:

```solidity
 function confirmMint(
        address to,
        string memory username
    ) public onlyRole(MINTER_ROLE) returns (uint256) {
        uint256 tokenId = uint256(keccak256(abi.encodePacked(username)));
        _initializeTraits(tokenId, username);
        _mint(to, uint256(keccak256(abi.encodePacked(username))));
        return tokenId;
    }
```

권장 사항: 특히 수신자가 계약일 때 ERC721 표준과의 호환성 및 토큰 전송의 적절한 처리를 보장하기 위해 `_mint()` 호출을 `_safeMint()`로 대체하십시오.

# [L-05] 사용자 이름 또는 계정 생성 DOS (DOS username or account creation)

악의적인 사용자가 `mintWithEth` 및 `mintWithGameItem` 함수를 프론트런하여 사용자 이름을 차지함으로써 합법적인 사용자가 해당 사용자 이름으로 계정을 등록하는 것을 방지할 수 있습니다.

**참고:** 사용자 이름은 토큰 ID로 해시되며 ERC721 토큰은 동일한 토큰 ID를 다시 발행할 수 없습니다.

# [L-06] `mint()`가 `username` 규칙에 대해 `tokenId`를 검증하지 않음 (`mint()` does not validate `tokenId` against `username` rules)

`GigaNameNFT.mint()` 함수는 `MINTER_ROLE` 보유자가 사용자 이름을 기반으로 미리 계산된 tokenId로 `GigaNameNFT`를 발행할 수 있도록 허용하지만, `username`에서 파생된 `tokenId`가 `validateUsername()` 함수에 의해 시행되는 제약 조건을 준수하는지 검증하지 않아 잠재적으로 유효하지 않은 사용자 이름이 발행될 수 있습니다:

```solidity
    function mint(
        address to,
        uint256 id
    ) external onlyRole(MINTER_ROLE) whenNotPaused {
        _safeMint(to, id);
    }
```

`username`에서 파생된 `tokenId`가 `validateUsername()` 함수를 통해 필요한 `username` 제약 조건을 충족하는지 검증하도록 `GigaNameNFT.mint()` 함수를 업데이트하십시오.
