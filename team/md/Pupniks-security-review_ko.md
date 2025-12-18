# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**pupniks-contract** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Pupniks 정보

Pupniks는 ERC721 계약(NFT)입니다. 사용자는 ETH로 Pupniks NFT를 민팅하고 NFT를 상환하여 ETH를 다시 받을 수 있습니다.

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

- 낮음 - 가정이 너무 많거나 희박하거나, 인센티브가 거의 또는 전혀 없이 공격자가 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정하는 것이 좋음

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [b2c9fc80fecfbc6b789246aae05b46110464e712](https://github.com/mitche50/pupniks-contract/tree/b2c9fc80fecfbc6b789246aae05b46110464e712)

_수정 검토 커밋 해시_ - [175ddaa3e1c0194e360e9c12fe0b57989f115708](https://github.com/mitche50/pupniks-contract/tree/175ddaa3e1c0194e360e9c12fe0b57989f115708)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Pupniks`

# 발견 사항

# [H-01] NFT 상환으로 인해 민팅이 벽돌(bricked)이 될 수 있음

## 심각도

**영향:** 높음, 후속 Pupnik 판매 방지

**가능성:** 중간, 악의적인 당사자가 쉽게 수행할 수 있음

## 설명

Pupnik NFT는 민팅된 후 언제든지 상환할 수 있습니다. 판매가 진행되는 동안 ID가 `amountMinted`보다 엄격히 작은 Pupnik을 상환하면 판매가 `amountMinted`에 의존하기 때문에 후속 판매가 되돌려집니다. 상환은 `amountMinted`를 감소시킵니다.

감소는 `amountMinted`가 감소되기 전에 기존 `amountMinted` ID를 민팅하려고 시도합니다.

```solidity
uint256 currentAmount = amountMinted;

for (uint256 i = 1; i <= quantity;) {
  _mint(msg.sender, currentAmount + i);
  ++i;
}
```

## POC

```solidity
function test_dos_sale(uint256 amount) public {
  // setup: mint 2-5 NFTs (exclude 1 because we need to redeem NFT id < lastMinted)
  amount = bound(amount, 2, 5);
  _deploy();

  changePrank(owner);
  pupniks.setSignerAddress(signer);
  pupniks.toggleSaleStatus();
  changePrank(user);

  uint256 nonce = 0;

  (bytes32 hash, uint8 v, bytes32 r, bytes32 s) = getSignature(user, nonce, amount, signerPkey);

  pupniks.mintPupnik{value: 0.5 ether * amount}(hash, abi.encodePacked(r, s, v), nonce, amount);

  // redeem 1 NFT
  pupniks.redeemPupnik(1);

  // then try to mint more, but it reverts with TokenAlreadyExists()
  nonce = 1;
  (hash, v, r, s) = getSignature(user, nonce, amount, signerPkey);
  vm.expectRevert(ERC721.TokenAlreadyExists.selector);
  pupniks.mintPupnik{value: 0.5 ether * amount}(hash, abi.encodePacked(r, s, v), nonce, amount);
}
```

## 권장 사항

민팅된 Pupnik만 추적하고 절대 감소하지 않는 별도의 변수를 갖거나, 판매가 완료된 것으로 간주될 때까지 일정 기간 동안 Pupnik 상환을 지연시키십시오.

# [L-01] 베스팅 기간으로 인해 가스 보상을 잃을 수 있음

Blast에서 계약에 의해 생성된 가스 보상은 Blast 계약의 `claimAllGas()` 함수를 호출하여 수집할 수 있습니다. 그러나 이로 인해 일부 보상을 잃게 됩니다.

계약에서 소비한 가스 수수료는 베스팅 기간 후에 동일한 계약에서 수집할 수 있습니다. 초기에는 가스 수수료의 50%만 수집할 수 있으며, 30일에 걸쳐 선형적으로 100%까지 증가합니다.

`claimAllGas()`가 호출되면 현재 사용 가능한 모든 가스 보상을 청구하고 미베스팅된 모든 보상을 제거합니다. 이로 인해 아직 베스팅 과정 중인 일부 가스 수수료가 손실될 수 있습니다. 이는 완전한 베스팅을 가정하는 `claimableGas` 함수의 결과와 다를 수 있습니다.

특정 베스팅 임계값 이상의 가스 수수료만 청구하는 `claimGasAtMinClaimRate()` 함수를 추가하는 것을 고려하십시오.

Blast 문서 [여기](https://docs.blast.io/building/guides/gas-fees#claiming-gas-fees)를 참조하십시오.

# [L-02] 잘못된 이벤트 이름이 ERC7572 준수를 깸

ERC-7572는 오프체인 인덱서가 계약을 쿼리할 수 있도록 계약 메타데이터 업데이트 시 `ContractURIUpdated()` 이벤트를 방출해야 한다고 명시합니다.

그러나 이벤트 이름이 `ContractURISet()`으로 잘못 지정되었으며 인수도 잘못 포함되어 있습니다.

```diff
- event ContractURISet(string URI);
+ event ContractURIUpdated();
```
