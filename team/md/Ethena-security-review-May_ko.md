# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**ethena-labs/ethena-mint-contract-audit** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Ethena 소개 (About Ethena)

Ethena Mint V2는 가격 발산(divergence)에 대한 델타 제한, 별도의 발행/상환 제한, 온체인 신원 화이트리스트 및 EIP-1271 서명 확인을 도입하며, 모두 Ethena 다중 서명을 통해 구성할 수 있습니다.

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

**_검토 커밋 해시_ - [b60b7193636d499ce7f89c4f5afe3b99cf31a2b6](https://github.com/ethena-labs/ethena-mint-contract-audit/tree/b60b7193636d499ce7f89c4f5afe3b99cf31a2b6)**

**_수정 검토 커밋 해시_ - [9cd4ad7b46acc35f6b3340c808200279fbe75de0](https://github.com/ethena-labs/ethena-mint-contract-audit/tree/9cd4ad7b46acc35f6b3340c808200279fbe75de0)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `EthenaMinting`
- `SingleAdminAccessControl`

# 발견 사항 (Findings)

# [M-01] 일부 주문이 여러 번 실행될 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`_deduplicateOrder()` 및 `verifyNonce()` 함수는 주문 중복 제거와 동일한 논스가 두 번 사용되지 않도록 하는 역할을 합니다. 문제는 `verifyNonce()` 코드에서 `invalidatorBit`를 `uint128`로 안전하지 않은 캐스트(unsafe cast)를 통해 변환하고, `invalidatorBit` 값이 0이 되는 오버플로가 발생할 수 있다는 점입니다. 이 경우 `invalidator`가 1로 설정되지 않으며 해당 주문은 발행자(minter) 및 상환자(redeemer) 역할에 의해 여러 번 실행될 수 있습니다. 이 문제의 영향은 사용자의 동의 없이 자금을 조작하여 손실을 입힐 수 있다는 것입니다.

이 문제는 `uint8(nonce) > 128`일 때마다 발생합니다:

```solidity
 uint128 invalidatorSlot = uint64(nonce) >> 8;
 uint128 invalidatorBit = uint128(1 << uint8(nonce));
 uint128 invalidator = _orderBitmaps[sender][invalidatorSlot];
 if (invalidator & invalidatorBit != 0) revert InvalidNonce();
```

## 권장 사항

`invalidatorBit`를 `uint128`로 캐스트하거나 `uint7(nonce)`를 사용하지 마십시오.

# [L-01] `tokenConfig` 설정 시 건전성 검사 누락

`addSupportedAsset()` 함수는 관리자가 토큰 유효성을 보장하기 위해 기본 제공 건전성 검사를 통해 새 토큰을 추가할 수 있도록 허용합니다:

```solidity
    if (tokenConfig[asset].isActive || asset == address(0) || asset == address(usde)) {
      revert InvalidAssetAddress();
    }
```

그러나 배포 중에 새 토큰은 내부 `_setTokenConfig()` 함수를 사용하여 추가되는데, 이 함수에는 이러한 검증이 없습니다:

```solidity
    for (uint128 k = 0; k < _tokenConfig.length;) {
      _setTokenConfig(_assets[k], _tokenConfig[k]);
      unchecked {
        ++k;
      }
    }
```

배포 중 오류를 방지하려면 유사한 확인을 추가하는 것을 고려하십시오:

```solidity
    for (uint128 k = 0; k < _tokenConfig.length;) {
      if (_assets[k] == address(0) || _assets[k] == address(usde)) revert InvalidAssetAddress();
      _setTokenConfig(_assets[k], _tokenConfig[k]);
      unchecked {
        ++k;
      }
    }
```

# [L-02] ETH 및 WETH 상환 제한 결합 가능

`EthenaMinting.sol` 계약은 전역 제한 외에도 발행 및 상환에 대한 자산 기반 제한을 도입했습니다.

```solidity
  modifier belowMaxMintPerBlock(uint128 mintAmount, address asset) {
    TokenConfig memory _config = tokenConfig[asset];
    if (!_config.isActive) revert UnsupportedAsset();
 >> if (totalPerBlockPerAsset[block.number][asset].mintedPerBlock + mintAmount > _config.maxMintPerBlock) {
      revert MaxMintPerBlockExceeded();
    }
    _;
  }
  modifier belowMaxRedeemPerBlock(uint128 redeemAmount, address asset) {
    TokenConfig memory _config = tokenConfig[asset];
    if (!_config.isActive) revert UnsupportedAsset();
>>  if (totalPerBlockPerAsset[block.number][asset].redeemedPerBlock + redeemAmount > _config.maxRedeemPerBlock) {
      revert MaxRedeemPerBlockExceeded();
    }
    _;
  }
```

한 가지 경우에서 이러한 제한을 우회할 수 있습니다. USDe를 ETH 및 WETH 토큰으로 상환할 때, 사용자가 기본적으로 동일한 ETH 자산을 받더라도(WETH는 쉽게 래핑 해제될 수 있음) 서로 다른 `totalPerBlockPerAsset[block.number]` 누산기가 증가합니다. 이를 통해 사용자는 USDe를 블록 제한 이상으로 상환할 수 있습니다.

```solidity
  function redeem(Order calldata order, Signature calldata signature)
    external
    override
    nonReentrant
    onlyRole(REDEEMER_ROLE)
>>  belowMaxRedeemPerBlock(order.usde_amount, order.collateral_asset)
    belowGlobalMaxRedeemPerBlock(order.usde_amount)
  {
    ---SNIP---
>>  totalPerBlockPerAsset[block.number][order.collateral_asset].redeemedPerBlock += order.usde_amount;
```

자산이 WETH 또는 ETH인 경우 두 누산기를 모두 증가시키는 것이 좋습니다.
