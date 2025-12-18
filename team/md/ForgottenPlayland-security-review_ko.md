# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**forgotten-playland-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Forgotten Playground 소개 (About Forgotten Playground)

Forgotten Playland ToyBox는 판매를 통해 ERC1155 ToyBox를 배포하는 스마트 계약 세트이며, 이를 무작위로 ERC1155 Cosmetics로 교환할 수 있습니다. 새 토큰이 발행될 때마다 서버에서 무작위성을 가져오고 계약이 이를 소비하여 ID를 생성합니다.

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

**_검토 커밋 해시_ - [b3b733ed5d7f33ea4bc794ca912bab2c356f471b](https://github.com/Merit-Circle/forgotten-playland-contracts/tree/b3b733ed5d7f33ea4bc794ca912bab2c356f471b)**

**_수정 검토 커밋 해시_ - [ad99fbe727ebd61b59d012ecacdcfd3ca1903081](https://github.com/Merit-Circle/forgotten-playland-contracts/tree/ad99fbe727ebd61b59d012ecacdcfd3ca1903081)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `ToyBox`
- `Cosmetics`
- `ZkRandMint`
- `TokenSaver`
- `CustomSale`
- `AbstractERC1155`

# 발견 사항 (Findings)

# [C-01] 더 낮은 가격으로 구매하기 위한 유니스왑(Uniswap) 오라클 조작

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`uniswapV2Router.getAmountsIn()`은 `referenceToken`의 금액에 필요한 `paymentToken`의 양을 계산하는 데 사용됩니다.
이 피드(feed)는 유니스왑 페어(pair)에서 대규모 스왑에 의해 쉽게 조작됩니다.
따라서 공격자는 한 트랜잭션 내에서 다음을 수행할 수 있습니다:

1. `referenceToken` 플래시론(Flashloan)
2. 이 `referenceToken`을 유니스왑 페어에서 판매하여 `paymentToken` 구매. `referenceToken`의 가격이 거의 0까지 감소함.
3. `paymentToken`을 사용하여 TokenBox 발행 비용 지불. 조작된 가격은 `referenceToken`으로 가격이 책정된 TokenBox를 구매하는 데 매우 적은 양의 `paymentToken`을 지출하는 데 도움이 됨.
4. 플래시론된 `referenceToken` 반환.

## 권장 사항

TWAP는 Uniswap V2 페어에서 가격을 읽는 데 권장되는 방법입니다. 하지만 유동성이 낮은 페어의 경우 조작될 수도 있습니다.
체인링크(Chainlink)와 같은 중앙 집중식 오라클 사용을 고려하십시오. 예를 들어, 토큰을 paymentToken으로 허용할 때 Chainlink 피드를 제공할 수 있습니다.

# [C-02] `customSaleWithPermit()`에 기본(Primary) 가격을 사용하는 실수

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

ToyBox는 비전형(non-primary) 판매에 `customSale()` 및 `customSaleWithPermit()`을 사용하며, 이러한 함수는 1차 판매(primary sales)의 데이터를 사용해서는 안 됩니다.
그러나 `customSaleWithPermit()`은 1차 판매 가격을 사용하는데, 이는 의도된 동작이 아닐 가능성이 큽니다.

```
    function customSaleWithPermit(uint256 _amount, PermitSignature calldata _permitSignature, bytes32[] calldata _proof)
        external
        nonReentrant
        customSaleChecks(_permitSignature.owner, _permitSignature.token, _amount, _proof)
    {
        ...
        _collectWithPermit(
            _amount, getFullCustomPrice(price, _permitSignature.token), saleStruct.referenceToken, _permitSignature
        );
        ...
    }
```

결과적으로 허가(permit)를 통해 승인을 서명한 사용자는 다른 가격을 받게 됩니다.

## 권장 사항

`customSale()`에서와 같이 `price`를 `saleStruct.price`로 바꾸십시오.

# [H-01] `saleUserCap` 및 화이트리스트 우회

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`ToyBox.customSaleChecks()`는 많은 확인을 수행하지만 사용자에 대한 이 확인은 잘못되었을 가능성이 매우 높습니다:

```solidity
    modifier customSaleChecks(address _receiver, address _paymentToken, uint256 _amount, bytes32[] calldata _proof) {
        ...
        // If the merkleRoot is set, check if the user is in the list
        if (saleStruct.merkleRoot != bytes32(0)) {
            bytes32 leaf = keccak256(abi.encode(msg.sender));
            if (!MerkleProof.verify(_proof, saleStruct.merkleRoot, leaf)) {
                revert InvalidProof();
            }
        }
        ...
```

문제는 여기서 "사용자"가 `_receiver`일 때 `msg.sender`가 확인된다는 것입니다. 둘이 같으면 올바르게 작동하지만 다른 모든 경우에는 잘못됩니다.
코드의 뒷부분에서 `_receiver`는 `msg.sender`가 아닌 `saleUserCap` 확인의 대상입니다. 이를 통해 다른 수신자에게 발행하여 `saleUserCap`을 우회할 수 있습니다.
또한 `msg.sender`는 `customSaleWithPermit()`을 호출할 때 확인되는데, 이 또한 잘못되었을 수 있습니다.
게다가 이러한 수신자는 화이트리스트에 있는지 확인되지 않습니다.

## 권장 사항

`customSaleChecks()`에서 `msg.sender`를 `_receiver`로 바꾸십시오.

# [H-02] 계약 승인을 설정한 사람에게 강제로 ToyBox 구매

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

ToyBox 계약과 `primarySaleWithPermit()` 및 `customSaleWithPermit()` 코드에서는 `msg.sender`가 `permitSignature.owner`와 동일한지 확인하지 않습니다. 또한 `trustlessPermit()`에서 유효한 권한 서명이 강제되지 않으므로 누군가 ToyBox 계약에 대한 승인을 설정했다면, 스푸핑된 권한 서명으로 해당 함수를 호출하여 권한 없이 ToyBox 토큰을 구매할 수 있습니다. 공격자는 지출 허용(allowance)을 제공한 모든 사용자의 토큰을 소비하고 가격이 공정하지 않을 때 ToyBox를 구매할 수 있습니다.

## 권장 사항

코드는 `msg.sender`가 `permitSignature.owner`와 동일한지 확인해야 합니다.

# [M-01] 블록 번호 대신 블록 타임스탬프를 잘못 사용함

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

코드가 마지막 블록 해시를 얻으려고 할 때 `blockhash(block.timestamp - 1)`을 사용합니다.

```solidity
        bytes32 pseudoRandomness =
            keccak256(abi.encode(blockhash(block.timestamp - 1), msg.sender, nonces[msg.sender])) >> 3;
```

하지만 Solidity 문서에 따르면:

```
blockhash(uint blockNumber) returns (bytes32): hash of the given block - only works for 256 most recent blocks
```

결과적으로 `blockhash(block.timestamp - 1)`은 존재하지 않는 블록에 대해 계산되어 항상 0이 되므로 `pesudoRandomness`는 더 예측 가능하고 문서에 따르지 않게 됩니다.

## 권장 사항

코드를 `blockhash(block.number - 1)`로 변경하십시오.

# [M-02] `ToyBox.sol` 계약에 `discountTokens` 설정자가 없음

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

사용자가 판매에서 ToyBox 토큰을 구매하면 가격에 할인이 적용됩니다.

```solidity
    function getFullPrice(uint256 _price, address _paymentToken) internal view returns (uint256) {
>>      uint256 _discount = discountTokens[_paymentToken];
        if (_discount > 0) {
            _price = _price - (_price * _discount / 10000);
        }
        return _price;
    }

    function getFullCustomPrice(uint256 _price, address _paymentToken) internal view returns (uint256) {
>>      uint256 _discount = saleTokenDiscounts[customSaleActive][_paymentToken];
        if (_discount > 0) {
            _price = _price - (_price * _discount / 10000);
        }
        return _price;
    }
```

`discountTokens` 및 `saleTokenDiscounts` 매핑은 각각 1차 판매 및 맞춤형 판매에 대한 할인율을 저장합니다. 그러나 판매 관리자는 `setSaleTokenDiscounts/setSaleTokenDiscount` 함수를 통해 관리되는 맞춤형 판매 할인과 달리 설정자(setter) 함수가 없기 때문에 `discountTokens`를 사용하여 1차 판매에 대한 할인을 사용자 정의할 수 없습니다.

## 권장 사항

`discountTokens[_paymentToken]`을 설정하는 함수를 추가하거나 `primarySale()` 및 `primarySaleWithPermit()`에서 1차 판매에 대한 할인을 계산하는 단계를 제거하는 것을 고려하십시오.

# [L-01] `openingTime` 이후에 `burnId`를 설정하면 다른 ToyBox 토큰을 소각하여 Cosmetic 토큰을 발행할 수 있음

`burnId`의 기본값은 0이며 관리자가 `openingTime` 이후에 값을 설정하면 ToyBox 토큰 `id=0`도 소각하여 Cosmetic 토큰을 발행할 수 있습니다.

# [L-02] 마지막 드롭률(drop rate)은 100이어야 하며 그렇지 않으면 발행이 항상 되돌려짐(revert)

`setRarityDistribution()`에는 마지막 드롭률이 100인지 확인하는 확인이 없으며 마지막 드롭률이 100이 아니면 발행이 되돌려집니다.

# [L-03] 관리자가 무작위 발행 프로세스를 조작할 수 있으므로 중앙 집중화 위험 발생

관리자는 발행 프로세스 중에 다음과 같은 다양한 문제를 일으킬 수 있습니다:

1. 울트라 레어(Ultra Rare) 토큰을 받기 위해 약속(commitment) 변경.
2. 일부 사용자에 대한 증명(proof)을 생성하지 않음.

# [L-04] 설정 시 `openingTime` 및 `closingTime` 확인 안 함

`ToyBox.setTimes()`, `ToyBox.setCustomSale()` 및 `ZKRandMint.setTimes()`에서 `openingTime < closingTime`인지 `openingTime` 및 `closingTime` 설정에 대한 추가 확인을 고려하십시오.

# [L-05] maxMintPerTx를 우회하기 위해 다중 호출(Multiple calls)

`maxMintPerTx` 이름과 주석은 변수가 트랜잭션당 구매 금액을 제한해야 함을 암시합니다.

```solidity
        // Can't mint more per tx than allowed
        if (_amount > maxMintPerTx) {
            revert ExceedsMaxMintPerTx();
        }
```

실제로는 호출당 제한입니다. 결과적으로 제한을 우회하는 가장 간단한 전략은 트랜잭션당 여러 번 호출하는 것입니다.

이 동작이 의도적인지 확인하십시오. 그렇지 않은 경우 트랜잭션 제한을 올바르게 설정하는 로직을 추가하십시오.
