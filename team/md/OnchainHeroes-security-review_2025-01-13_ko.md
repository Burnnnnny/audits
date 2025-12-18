# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**onchain-heroes/och-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Onchain Heroes 정보

Onchain Heroes는 플레이어가 NFT 영웅을 던전으로 보내 토큰을 얻는 게임입니다. 성공하면 보상을 받지만 실패하면 시즌이 끝날 때까지 영웅을 잃게 됩니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [42d0262d32aba8e06cad0f6e2def2ec9e0e711f0](https://github.com/onchain-heroes/och-contracts/tree/42d0262d32aba8e06cad0f6e2def2ec9e0e711f0)

_수정 검토 커밋 해시_ - [beb094fca6d90dccb810ecc5ad0eafc42a3cec5d](https://github.com/onchain-heroes/och-contracts/tree/beb094fca6d90dccb810ecc5ad0eafc42a3cec5d)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `GachaToken`
- `Endgame`
- `HeroERC721A`

# 발견 사항

# [C-01] Gotcha 토큰 소각에 대한 무단 액세스

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

두 가지 함수가 영향을 받습니다: `burnFrom` 및 `brun`

- `burnFrom`: `GachaToken::burnFrom`에는 `address by, address from, uint256 id, uint256 amount`의 네 가지 입력이 있습니다. 다음은 `address by`에 대한 설명입니다.

```
        // - If `by` is not the zero address, it must be either `from`,
        //   or approved to manage the tokens of `from`.
```

공격자는 `by`를 0 또는 승인된 주소로 설정하고 토큰을 소각할 수 있습니다.

- `burn`: 이 함수는 `_burn` 함수를 호출합니다. `_burn`에서:

```solidity
    function _burn(address from, uint256 id, uint256 amount) internal virtual {
        _burn(address(0), from, id, amount);
    }
```

`by`를 0 주소로 설정합니다(위의 공격 경로와 동일).

다음은 `by`를 0 주소로 설정하기 위한 POC입니다.

**POC:**

```solidity
    function testBurn_gotcha_token() public {
        vm.prank(address(endgame));
        gachaToken.mint(owner, 1, 100);
        assertEq(gachaToken.balanceOf(owner, 1), 100);

        vm.prank(user1);
        // gachaToken.burnFrom(by, from, id, amount);
        //@audit by setting `by` as 0, it should be able to burn from any address
        gachaToken.burnFrom(address(0), owner, 1, 100);
        assertEq(gachaToken.balanceOf(owner, 1), 0);
    }
```

## 권장 사항

1. `burnFrom`을 삭제하십시오.
2. 다음과 같이 `burn`을 수정하십시오.

```solidity
    function burn(address from, uint256 id, uint256 amount) external {
        _burn(msg.sender, from, id, amount);
    }
```

# [C-02] 악의적인 사용자가 RING당 보장된 민트(Guaranteed Mints)를 두 배로 발행할 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`HeroERC721A.sol`은 NFT 계약이며, 첫 번째 단계 **OG Phase**에서 화이트리스트 주소가 다음을 받을 것을 보장합니다.

- 1 무료 민트
- **RING**당 2개의 보장된 민트 (RING은 NFT이므로 보유자에 대한 스냅샷을 찍습니다)

`HeroERC721A.sol#ogMint()` 함수는 사용자가 OG 화이트리스트 주소에 대한 토큰을 발행하는 방법입니다.
두 가지 유형의 OG 민트에 대한 자격이 있는 사용자는 `ogMint()`를 여러 번 호출할 수 있는 유연성이 있습니다.
따라서 두 개의 다른 거래에서 무료 민트와 보장된 민트를 받을 수 있습니다.

악의적인 사용자는 이 권한을 이용하여 보장된 민트 토큰 수를 두 배로 늘릴 수 있습니다.

- 1. `ogMint()`를 호출하고 `freeMint`를 false로 설정하고 `paidMintQty = 10`으로 설정하면 `numOfRing = 5`가 됩니다.
     10개의 NFT를 발행하고 `aux`는 다음과 같습니다.

```
 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000
 1010 0000 0000 0000 0000 0000 0000
```

- 2. `ogMint()`를 호출하고 `freeMint`를 true로 설정하고 `paidMintQty = 0`으로 설정합니다.
     1개의 NFT(무료)를 발행하고 `aux`는 다음과 같습니다.

```
 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000
 0000 0000 0000 0000 0000 0000 1000
```

[24-63]이 이제 비어 있음을 알 수 있습니다. `_ogMint()`가 이 단계에서 이전 값을 삭제했기 때문입니다.

```solidity
 uint64 aux = (_getAux(to) >> 3) & 0x0fffff;
```

- 3. `ogMint()`를 호출하고 `freeMint`를 false로 설정하고 `paidMintQty = 10`으로 설정하면 `numOfRing = 5`가 됩니다.
     10개의 NFT를 발행하고 `aux`는 다음과 같습니다.

```
 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000 0000
 1010 0000 0000 0000 0000 0000 1000
```

따라서 이 악의적인 사용자는 5개의 RING만 가지고 있을 때 OG 유료 민트에서 20개를 발행했습니다.

참고:
Aux 비트 레이아웃
aux는 다음 비트 레이아웃을 가진 64비트 숫자입니다.

[0-1]: `publicMint` 상태 추적
[2-2]: 영웅 목록 발행 추적
[3-23]: OG 무료 민트 수 추적 (용량 : 2\***\*20 -1)
[24-63]: OG 유료 민트 수 추적 (용량 : 2\*\***40 -1)

**POC:**

```solidity
    function testMinting() public {
        // og mint
        vm.warp(uint256(hero721.OG_MINT_TIMESTAMP()));
        bytes32[] memory b = new bytes32[](3);
        b[0] = bytes32(0xf6cad13a027fce5304a21819a989779362a06a4fbd07521b18209cd6ddc4e41c);
        b[1] = bytes32(0xc41bc74587e0ba059b462f9e0d8a6abd430876040e77e050ca8ec3f11d8bab38);
        b[2] = bytes32(0x31d93cf3542a493f93d6d5304dc473819fb4d0bf018b05c910f1802ea619ab5d);

        hero721.ogMint{value: 4e17}(ALICE, false, 4, 2, b);

        hero721.ogMint{value: 0}(ALICE, true, 0, 2, b);

        hero721.ogMint{value: 4e17}(ALICE, false, 4, 2, b);
        assertEq(hero721.balanceOf(ALICE), 10);
    }

```

## 권장 사항

```diff
    function _ogMint(address to, uint64 numOfRings) internal {
+        uint64 _aux = _getAux(to);
+        uint64 aux = (_aux >> 3) & 0x0fffff;
-        uint64 aux = (_getAux(to) >> 3) & 0x0fffff;
        if (aux == numOfRings) revert AlreadyMinted();
-        _setAux(to, uint64(aux | (numOfRings << 3))); // Set OG free mint flag
+        _setAux(to, uint64(_aux | (numOfRings << 3))); // Set OG free mint flag
        _safeMint(to, numOfRings);
    }
```
