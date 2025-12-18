# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**avierauy/beam-nodes** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Beam Nodes 소개

Beam Nodes는 BEAM 체인에서 직접 운영되는 NFT 주조 계약이며, 다른 체인의 사용자는 입금을 통해 NFT를 예약하고 나중에 외부 백엔드에서 일괄 주조됩니다. 다양한 결제 방법을 지원하며 추천 보상 및 할인 인센티브를 포함합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [df88d614b119cee8a1908c958b6027746c49c453](https://github.com/avierauy/beam-nodes/tree/df88d614b119cee8a1908c958b6027746c49c453)

_수정 검토 커밋 해시_ - [cdff871eba00a53c66b6d4fc4670aa0a09915cda](https://github.com/avierauy/beam-nodes/tree/cdff871eba00a53c66b6d4fc4670aa0a09915cda)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `BeamNodes`

# 발견 사항

# [M-01] 특정 tokenId에 대한 로열티 설정 함수 누락

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`resetDefaultRoyalty` 함수는 특정 tokenId에 대해 설정된 로열티 정보를 지우는 데 사용됩니다.

```solidity
    function resetDefaultRoyalty(uint256 _tokenId) external onlyRole(ADMIN_ROLE) {
        _resetTokenRoyalty(_tokenId);
        emit ResetDefaultRoyalty(_tokenId);
    }
```

그러나 특정 tokenId에 대한 로열티 정보를 설정하는 함수가 부족합니다.

## 권장 사항

특정 tokenId에 대한 로열티 정보를 설정하는 함수를 추가하십시오.

```diff
+   function setRoyaltyForId(uint256 _tokenId, address _receiver, uint96 _feeNumerator) external onlyRole(ADMIN_ROLE) {
+       _setTokenRoyalty(_tokenId, _receiver, _feeNumerator);
+       emit ResetDefaultRoyalty(_tokenId);
+   }
```

# [L-01] `batchMint()` 및 `claim()`에서 chainId 확인 누락

`batchMint` 및 `claim` 함수는 BEAM 체인에서만 호출되도록 의도되었지만 함수는 chainId 확인을 수행하지 않습니다.

```solidity
    function batchMint(address[] memory _recipients, uint256[] memory _quantities) external onlyRole(MINTER_ROLE) {
        require(_recipients.length == _quantities.length, InvalidValues());
        for (uint256 i = 0; i < _recipients.length; i++) {
            _safeMint(_recipients[i], _quantities[i]);
            emit BatchMint(_recipients[i], _quantities[i]);
        }
    }

    function claim(uint256 _quantity, bytes32[] calldata _proof) external whenNotPaused nonReentrant {
        if (hasClaimed[msg.sender]) revert AlreadyClaimed();

        bool isValidLeaf = _verifyProof(msg.sender, _quantity, _proof);

        if (!isValidLeaf) revert NotInMerkle();

        _safeMint(msg.sender, _quantity);

        hasClaimed[msg.sender] = true;

        emit Claim(msg.sender, _quantity);
    }
```

`block.chainid`를 가져와서 `BEAM_CHAIN_ID`와 비교하는 것이 좋습니다.

# [L-02] revokeRole 및 renounceRole 재정의 고려

AccessControl.sol에는 호출자의 역할을 제거하는 두 가지 함수인 `revokeRole()`과 `renounceRole()`이 있습니다.

```
  function revokeRole(bytes32 role, address account) public virtual onlyRole(getRoleAdmin(role)) {
        _revokeRole(role, account);
    }

    function renounceRole(bytes32 role, address callerConfirmation) public virtual {
        if (callerConfirmation != _msgSender()) {
            revert AccessControlBadConfirmation();
        }

        _revokeRole(role, callerConfirmation);
    }
```

프로토콜이 상당히 중앙 집중화되어 있으므로 BeamNodes.sol에서 이러한 함수를 재정의하고 우발적인 역할 취소를 방지하기 위해 되돌리는 것을 고려하십시오.

# [L-03] 큰 가격 변동으로 인해 고정 가격 차익 거래 가능

BeamNode에서 사용자는 네이티브 토큰, USDC 또는 BEAM 토큰을 입금하여 NFT를 주조하도록 선택할 수 있습니다. 관리자는 다른 입금 토큰에 대해 다른 기본 가격을 설정할 수 있습니다. 이렇게 하면 다른 입금 토큰과 비슷한 입금 가격을 갖도록 할 수 있습니다.

관리자는 실시간 토큰 가격에 따라 이러한 기본 가격을 업데이트할 수 있지만 토큰 가격을 항상 동기화하는 것은 불가능합니다. 네이티브 토큰이나 BEAM 토큰 가격이 많이 변하면 사용자는 예상보다 훨씬 저렴하게 NFT 토큰을 주조할 수 있습니다.

```solidity
    function getPrice(TokenType _tokenType) public view returns (uint256) {
        if (_tokenType == TokenType.NATIVE) {
            return nativeBasePrice;
        } else if (_tokenType == TokenType.USDC) {
            return usdcBasePrice;
        } else {
            return beamBasePrice;
        }
    }
```

실시간 토큰 가격을 계산하기 위해 하나의 체인링크 오라클을 사용하는 것이 좋습니다.

# [L-04] 시빌 공격을 통해 추천 시스템 공격 가능

BeamNode에는 하나의 추천 시스템이 있습니다. 사용자가 처음으로 추천인과 함께 입금하면 할인된 가격으로 하나의 NFT를 구매할 수 있습니다. 사용자가 두 번째로 추천인과 함께 입금하려는 경우 할인을 제공하지 않습니다.

문제는 이 설계가 우회하기 쉽다는 것입니다. 사용자가 두 번째로 추천인과 함께 입금하려는 경우 자금을 다른 주소로 이체하고 입금하여 할인을 받을 수 있습니다.

```solidity
    function _setReferrer(address _user, address _referrer) internal {
        if (users[_user].referrer == address(0) && _isValidReferrer(_user, _referrer)) {
            users[_user].referrer = _referrer;
        } else {
            require(_referrer == address(0), InvalidReferral());
        }
    }
```

이 설계가 필요한 경우 이 기능에 대해 하나의 예금자 화이트리스트를 추가해야 합니다.

