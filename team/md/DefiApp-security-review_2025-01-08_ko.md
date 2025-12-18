# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**defi-app/defi-app-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Defi App 소개 (About Defi App)

DefiApp은 기본 계정 추상화 및 가스 없는 거래와 같은 기능을 제공하여 체인 간에 DeFi 자산을 관리하는 플랫폼입니다.
계약 범위는 PublicSale 및 VestingManager 계약에 집중되었습니다.
PublicSale 스마트 계약은 사용자가 USDC를 입금하여 다양한 계층, 사용자 정의 가능한 판매 매개변수 및 단계에서 토큰을 구매할 수 있도록 하여 토큰 판매를 용이하게 합니다.
VestingManager 스마트 계약은 토큰 베스팅 일정의 생성, 관리 및 취소를 가능하게 하며, 각 일정을 수혜자를 위한 사용자 정의 가능한 릴리스 매개변수가 있는 양도 가능한 ERC721 NFT로 나타냅니다.

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

_검토 커밋 해시_ - [b64eb41a74dc02655eb3e10ef2b6fe34b4ff51c6](https://github.com/defi-app/defi-app-contracts/tree/b64eb41a74dc02655eb3e10ef2b6fe34b4ff51c6)

_수정 검토 커밋 해시_ - [2a453af8d8c08335ce4af068267fe81999816004](https://github.com/defi-app/defi-app-contracts/tree/2a453af8d8c08335ce4af068267fe81999816004)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `PublicSale`
- `VestingManager`

# 발견 사항 (Findings)

# [H-01] 판매 토큰 금액이 소수점에 따라 조정되지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

USDC를 예치하는 동안 `_computeTokens` 함수가 호출되어(`depositUSDC -> _purchase -> _calculateTokensToTransfer -> _computeTokens`) 사용자가 예치된 금액에 대해 받을 토큰 수를 계산합니다:

```solidity
File: PublicSale.sol
519:     function _computeTokens(uint256 _amountUSDC, uint256 _price) private pure returns (uint256) {
520:         // _price = price * 10^18 --> precision scaling
521:         // _amount = (input_amount * 10^6 (USDC/T)) * 10^18 (_price)
522:         // (_amount * 1e18) / _price = (10^6 * 10^18) / 10^18 = 10^6 precision
523:         // 10^6 * 10^12 = 10^18 --> scale for future token's decimals
524:         return ((_amountUSDC * 1e18) / _price); <=@
525:     }
```

문제는 L523의 주석에도 불구하고 결과가 토큰의 소수점에 대해 조정되지 않았으며 사용자는 나중에 이 잘못된 금액으로 베스팅을 받게 된다는 것입니다.

즉, 토큰에 18개의 소수점이 있는 경우 결과는 실제 토큰 양보다 10^12배 적습니다. 예를 들어, 가격이 `0.01e18`이고 사용자가 1000 USDC(1000 _ 10^6)를 예치하면 결과는 `1000 _ 1e6 \* 1e18 / 0.01e18 = 1e11` 토큰이 되며, 이는 18개의 소수점이 있는 토큰의 경우 먼지(dust) 금액입니다.

## 권장 사항

`_computeTokens` 함수의 결과에 `tokenDecimals - 10^6`을 곱하여 토큰의 소수점에 맞게 조정하는 것을 고려하십시오. 이를 달성하려면 `PublicSale` 계약 생성 중에 토큰 주소를 설정하는 것도 고려하십시오. 이렇게 하면 USDC를 예치하는 동안 계약 주소의 `decimals()` 함수에 접근할 수 있으며, 사용자는 어떤 토큰을 받을지 확신할 수 있어 전반적인 투명성이 향상됩니다.

# [M-01] `maxTotalFunds()`가 호출될 때마다 팽창됨

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`setTiers` 함수는 판매를 위한 새로운 계층을 설정할 수 있게 합니다. 내부적으로 계층의 값을 업데이트하고 `maxTotalFunds`를 새 계층의 `cap` 값의 합계만큼 증가시키는 `_setTiers` 함수를 호출합니다(L483):

```solidity
File: PublicSale.sol
479:     function _setTiers(Tier[MAX_TIERS] memory _tiers) private {
480:         for (uint256 i = 0; i < MAX_TIERS; i++) {
481:             _checkTierVestDuration(_tiers[i]);
482:             tiers[i] = _tiers[i];
483:             maxTotalFunds += _tiers[i].cap; <=@
484:         }
485:
486:         emit TiersUpdate(msg.sender, _tiers);
487:         emit MaxTotalFundsUpdate(msg.sender, maxTotalFunds);
488:     }
```

문제는 새 계층이 설정될 때 `maxTotalFunds`가 0으로 재설정되지 않는다는 것입니다. 즉, 계약 생성 후(초기에 `_setTiers`가 호출될 때) `setTiers`가 호출되면 `maxTotalFunds`는 모든 계층의 `cap` 값 합계만큼 팽창됩니다. 이로 인해 `_getRemainingCap` 함수와 같이 `maxTotalFunds`에 의존하는 모든 검사가 올바르지 않게 됩니다:

```solidity
    function _getRemainingCap() private view returns (uint256) {
        return maxTotalFunds - totalFundsCollected;
    }
```

## 권장 사항

`_setTiers` 함수에서 새 계층이 설정될 때 `maxTotalFunds`를 0으로 재설정하는 것을 고려하십시오.

# [M-02] `saleTokens`를 베스팅하고 청구할 수 없음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`PublicSale::claimAndStartVesting` 함수는 이미 입금한 사용자에 대한 베스팅 프로세스를 시작하는 역할을 합니다. 이 함수는 `VestingManager::createVesting` 함수를 호출합니다:

```solidity
File: PublicSale.sol
583:     function _setVestingHook(address _user, uint256 _amount, uint256 _vesting, uint32 _start) private {
584:         saleToken.safeTransferFrom(treasury, address(this), _amount);
585:         saleToken.forceApprove(vestingContract, _amount);
586:         uint32 numberOfSteps = uint32(_vesting) / DEFAULT_STEP_DURATION;
587:         numberOfSteps = numberOfSteps > 0 ? numberOfSteps : 1;
588:         uint128 stepPercentage =
589:             numberOfSteps > 0 ? uint128(PERCENTAGE_PRECISION / numberOfSteps) : uint128(PERCENTAGE_PRECISION);
590:         uint32 stepDuration = numberOfSteps > 1 ? DEFAULT_STEP_DURATION : 1;
591:@>       IVestingManager(vestingContract).createVesting(
592:             VestParams({
593:                 recipient: _user,
594:                 start: _start,
595:                 cliffDuration: 0,
596:                 stepDuration: stepDuration,
597:                 steps: numberOfSteps,
598:                 stepPercentage: stepPercentage,
599:                 amount: uint128(_amount),
600:                 tokenURI: ""
601:             })
602:         );
603:     }
```

문제는 현재 블록의 타임스탬프와 관련하여 `Vest.start` 시간이 과거인 경우 함수가 되돌려진다는(revert) 것입니다.

```solidity
File: VestingManager.sol
63:         if (vestParams.start < block.timestamp) revert InvalidStart();
```

이러한 반전으로 인해 사용자는 베스팅 프로세스를 시작하고 `saleTokens`를 청구할 수 없습니다. 또한 `PublicSale::setVesting` 함수는 `Completed` 단계에서만 사용할 수 있는 반면 이 문제는 `ClaimAndVest` 단계에서 발생하므로 해결 방법으로 사용할 수 없습니다.

```solidity
File: PublicSale.sol
337:     function setVesting(address _user, uint256 _amount, uint256 _vestingTime, uint32 _start)
338:         external
339:@>       atStage(Stages.Completed)
340:         onlyOwner
```

다음 시나리오를 고려하십시오:

1. `sale`이 시작되고 계약이 `TokenPurchase` 상태가 됩니다.
2. 펀드 수집이 완료되고 소유자가 베스팅 프로세스를 시작합니다.
3. `saleTokens`를 구매한 사용자가 `PublicSale::claimAndStartVesting` 함수를 사용하여 베스팅 프로세스를 시작하는 것을 잊거나 재무부 자금 부족으로 인해 `PublicSale::_setVestingHook` 함수가 일시적으로 되돌려지는 문제가 발생합니다. 이로 인해 베스팅이 시작되지 않아 `vest.start`가 `block.timestamp`보다 작아집니다.
4. 시간이 지나고 이제 `VestingManager#L63`의 다음 유효성 검사:
   `if (vestParams.start < block.timestamp) revert InvalidStart();`
   가 트랜잭션을 되돌립니다.
5. 사용자는 USDC 또는 `saleTokens`를 청구할 수 없습니다.

## 권장 사항

`ClaimAndVest` 단계에서도 `PublicSale::setVesting` 기능을 사용할 수 있도록 허용하는 것을 고려하십시오. 이것은 관리자가 `Vest.start` 기간을 놓친 사용자에 대해 수동으로 베스팅을 설정할 수 있는 대체 메커니즘을 제공할 것입니다.

또는 `vestingStart`가 과거인 경우 `_setVestingHook` 함수에서 현재 타임스탬프로 베스팅을 생성하는 것을 고려하십시오.

# [L-01] `vestSummary`는 계산에 절벽(cliff) 주식을 포함하지 않음

`vestSummary` 함수는 `initiallyVested` 금액을 `stepShares * steps`(L143)로 계산하지만, 여기에 `cliffShares`도 추가해야 합니다:

```solidity
File: VestingManager.sol
141:     function vestSummary(uint256 vestId) external view returns (uint256 remainingVested, uint256 canClaim) {
142:         Vest memory vest = vests[vestId];
143:         uint256 initiallyVested = vest.stepShares * vest.steps; <=@
144:         remainingVested = initiallyVested - vest.claimed;
145:         canClaim = _balanceOf(vest) >= vest.claimed ? _balanceOf(vest) - vest.claimed : 0;
146:     }
```

`cliffShares`를 `initiallyVested` 계산에 추가하여 올바른 `remainingVested` 값을 반환하도록 고려하십시오.

# [L-02] `createVesting()`의 `safeMint()`는 제로(zero) 주소로 호출될 수 있음

`createVesting` 함수는 `_mint` 함수를 호출하지만 `_safeMint` 함수를 사용하는 것이 모범 사례입니다. 수신자가 ERC721 토큰을 처리할 수 없는 계약인 경우 토큰이 갇힐 수 있습니다.

`createVesting` 함수에서 `_safeMint` 함수를 사용하는 것을 고려하십시오.

# [L-03] `PublicSale` 계약에 의해 생성된 모든 베스팅에 대해 `tokenURI` 함수가 되돌려짐

`PublicSale` 계약에 의해 생성된 모든 베스팅은 빈 `tokenURI` 필드를 가지므로 이러한 모든 베스팅에 대해 `tokenURI` 함수가 되돌려집니다:

```solidity
File: VestingManager.sol
42:
43:     function tokenURI(uint256 vestId) public view override returns (string memory) {
44:         string memory uri = vests[vestId].tokenURI;
45:         if (bytes(uri).length > 0) {
46:             return uri;
47:         } else {
48:             revert NoTokenURI(); <=@
49:         }
50:     }
```

EIP는 `tokenURI`가 존재하지 않는 토큰에 대해 되돌려야 한다고 명시하고 있지만, 이는 `PublicSale` 계약에 의해 생성된 베스팅이 유효하지 않은 토큰으로 취급될 수 있음을 의미합니다. 사용자가 보조 시장에서 판매하려고 할 때 문제가 발생할 수 있습니다.

`tokenURI` 함수가 되돌려지는 것을 방지하기 위해 비어 있지 않은 `tokenURI` 필드로 베스팅을 생성하는 것을 고려하십시오.

# [L-04] 기간이 60일 미만인 모든 베스팅은 즉시로 생성됨

`_setVestingHook` 함수는 `numberOfSteps`를 `_vesting / DEFAULT_STEP_DURATION`(L586)으로 계산합니다. `_vesting`이 60일(2 \* DEFAULT_STEP_DURATION) 미만인 경우 `numberOfSteps`는 항상 1이고 `stepDuration`은 1이 되므로 기간이 60일 미만인 모든 베스팅은 즉시로 생성됩니다:

```solidity
File: PublicSale.sol
583:     function _setVestingHook(address _user, uint256 _amount, uint256 _vesting, uint32 _start) private {
584:         saleToken.safeTransferFrom(treasury, address(this), _amount);
585:         saleToken.forceApprove(vestingContract, _amount);
586:         uint32 numberOfSteps = uint32(_vesting) / DEFAULT_STEP_DURATION; <=@
587:         numberOfSteps = numberOfSteps > 0 ? numberOfSteps : 1;
588:         uint128 stepPercentage =
589:             numberOfSteps > 0 ? uint128(PERCENTAGE_PRECISION / numberOfSteps) : uint128(PERCENTAGE_PRECISION);
590:         uint32 stepDuration = numberOfSteps > 1 ? DEFAULT_STEP_DURATION : 1; <=@
591:         IVestingManager(vestingContract).createVesting(
592:             VestParams({
593:                 recipient: _user,
594:                 start: _start,
595:                 cliffDuration: 0,
596:                 stepDuration: stepDuration,
597:                 steps: numberOfSteps,
598:                 stepPercentage: stepPercentage,
599:                 amount: uint128(_amount),
600:                 tokenURI: ""
601:             })
602:         );
603:     }
```

`_vesting`이 30일보다 큰 경우 `stepDuration`이 `DEFAULT_STEP_DURATION`과 같아지도록 확인을 추가하는 것을 고려하십시오.

# [L-05] 금액 > 3.4e38로 베스팅을 생성하면 토큰이 갇힐 수 있음

`_setVestingHook` 함수는 `amount` 매개변수를 `uint256` 유형으로 허용하지만, `createVesting` 호출 중에는 `uint128` 유형으로 변환됩니다(L599). 즉, `amount`가 `3.4e38`보다 크면 `amount`가 묵시적으로 다운캐스팅되어 토큰의 일부가 `PublicSale` 계약에 갇히게 됩니다.

```solidity
File: PublicSale.sol
//...
598:                 stepPercentage: stepPercentage,
599:                 amount: uint128(_amount), <=@
600:                 tokenURI: ""
601:             })
602:         );
603:     }
```

`amount`가 `type(uint128).max`보다 작은지 확인하는 검사를 추가하거나 `amount` 유형을 `uint128`로 변경하여 `3.4e38`보다 큰 금액으로 `_setVestingHook`을 호출할 수 없도록 하는 것을 고려하십시오.

# [L-06] `setTiers` 함수에 빈 계층에 대한 잘못된 검사가 있음

`setTiers` 함수에는 빈 계층에 대한 검사가 있지만 `keccak256(bytes.concat(new bytes(256)))`을 사용하여 256바이트 길이의 빈 바이트 해시를 얻는 반면 계층 배열은 288바이트 길이(3개 계층 _ 3개 필드 _ 각 32바이트)입니다:

```solidity
File: PublicSale.sol
275:     function setTiers(Tier[3] calldata _tiers) public atStage(Stages.ComingSoon) onlyOwner {
276:         bytes32 tiersHash_ = keccak256(bytes.concat(msg.data[4:]));
277:         bytes32 zeroBytesHash_ = keccak256(bytes.concat(new bytes(256))); <=@
278:         require(tiersHash_ != zeroBytesHash_);
279:
280:         _setTiers(_tiers);
281:     }
```

즉, 256바이트는 288바이트와 다른 해시를 가지므로 빈 계층 배열에 대해 검사가 되돌려지지 않습니다. 빈 바이트의 길이를 288바이트로 변경하는 것을 고려하십시오.

# [L-07] `VestingManager`에서 `setVestTokenURI` 기능을 활용할 수 없음

`PublicSale::claimAndStartVesting` 함수는 ERC721 토큰으로 표시되는 베스팅 일정을 생성하도록 설계되었습니다. 그러나 `VestingManager`의 로직으로 인해 새로 생성된 ERC721 토큰의 소유자는 `PublicSale` 계약 자체로 기본 설정됩니다. 이는 베스팅 생성 중 소유권 할당에 `msg.sender`가 사용되기 때문입니다.

```solidity
File: VestingManager.sol
78:         vests[vestId] = Vest({
79:@>           owner: msg.sender,
```

`PublicSale` 계약에는 `VestingManager::setVestTokenURI`를 호출하는 구현된 메서드가 없으므로 이 프로세스를 통해 생성된 토큰은 메타데이터를 업데이트할 수 없습니다.

```solidity
File: VestingManager.sol
52:     function setVestTokenURI(uint256 vestId, string calldata _tokenURI) external {
53:         Vest storage vest = vests[vestId];
54:         if (vest.owner != msg.sender) revert NotOwner();
55:         vest.tokenURI = _tokenURI;
56:     }
```

`PublicSale` 계약 내에 메서드를 구현하여 생성하는 베스팅에 대해 토큰 URI(`VestingManager::setVestTokenURI`) 설정을 용이하게 하십시오.

# [L-08] 일관성 없는 금액 확인으로 인한 트랜잭션 되돌림

스마트 계약의 입금 확인 로직 내에 불일치가 존재하며, 계산된 `_remainingAmount`가 `10e6` 미만일 수 있어 트랜잭션이 되돌려집니다.

```solidity
File: PublicSale.sol
453:     function _verifyDepositConditions(uint256 _amount, uint256 _amountDeposited) private view {
454:         if (_amount < 10e6) {
455:             revert InvalidPurchaseInputHandler(msg.sig, bytes32("_amount"), bytes32("at least"), 10e6);
456:         }
457:
458:         SaleParameters memory _saleParameters = saleParameters;
459:
460:         if ((_amount + _amountDeposited) < _saleParameters.minDepositAmount) {
461:             revert InvalidPurchaseInputHandler(
462:                 msg.sig, bytes32("_amount"), bytes32("below minDepositAmount"), _saleParameters.minDepositAmount
463:             );
464:         }
465:
466:         uint256 _remainingAmount = _saleParameters.maxDepositAmount - _amountDeposited;
467:         if (_amount > _remainingAmount) {
468:@>           revert InvalidPurchaseInputHandler(
469:                 msg.sig, bytes32("_amount"), bytes32("exceeds maxDepositAmount"), _remainingAmount
470:             );
471:         }
472:     }
```

이는 `_amount`가 `10e6`보다 커야 한다는 454행의 확인과 모순됩니다. 공격자는 이 조건이 충족되도록 토큰을 입금하여 트랜잭션을 강제로 되돌리게 함으로써 이 불일치를 악용할 수 있습니다. 이는 토큰 구매 프로세스를 효과적으로 차단할 수 있습니다.

이로 인해 계약은 `Completed` 상태로 전환될 수 없으며 `end` 시간을 기준으로 `TokenPurchase` 기간이 종료됩니다.

```solidity
File: PublicSale.sol
536:     function _getCurrentStage() private view returns (Stages) {
537:         if (saleSchedule.start == 0 && saleSchedule.end == 0) return Stages.ComingSoon;
538:         if (vestingStart != 0) return Stages.ClaimAndVest;
539:@>       if (totalFundsCollected >= maxTotalFunds) return Stages.Completed;
540:
541:         if (block.timestamp < saleSchedule.start) return Stages.ComingSoon;
542:@>       if (block.timestamp < saleSchedule.end) return Stages.TokenPurchase;
543:
544:@>       return Stages.Completed;
545:     }
```

`PublicSale::_verifyDepositConditions`에 `_remainingAmount`가 `10e6` 미만인 경우를 별도로 처리하는 조건을 추가하고 `_remainingAmount`가 `10e6` 미만일 때 `sale`이 `Completed`로 표시되도록 하는 것을 고려하십시오.

# [L-09] 최소 구매 보호 부족

PublicSale 계약의 `depositUSDC()` 함수에는 최소 구매 보장 메커니즘이 없습니다. 사용자가 토큰을 구매하려고 시도하고 남은 계층 한도(cap)가 원하는 금액보다 작으면 계약은 사용자 동의 없이 부분 구매를 처리합니다.

```solidity
function _calculateTokensToTransfer(uint256 _amount, uint256 _tierIndex) private view returns (uint256, uint256) {
    Tier memory _tier = tiers[_tierIndex];
    uint256 _remainingTierCap = _tier.cap - tiersDeposited[_tierIndex];

    if (_remainingTierCap == 0) {
        revert InvalidPurchaseInput(this.depositUSDC.selector, "_tierIndex", "tier cap reached");
    }

    if (_amount <= _remainingTierCap) {
        return (_computeTokens(_amount, _tier.price), 0);
    } else {
        uint256 _remainingAmount = _amount - _remainingTierCap;
        return (_computeTokens(_remainingTierCap, _tier.price), _remainingAmount);
    }
}
```

[src/token/PublicSale.sol#L499](https://github.com/defi-app/defi-app-contracts/blob/b64eb41a74dc02655eb3e10ef2b6fe34b4ff51c6/src/token/PublicSale.sol#L499)

계층에 용량이 불충분한 경우 함수는 다음과 같이 작동합니다:

- 남은 한도 금액까지만 처리
- 초과 USDC 반환
- 감액된 금액에 대한 사용자 동의 없이 거래 완료

예를 들어:

- 계층의 한도에 1500 USDC가 남아 있습니다.
- 앨리스와 밥이 각각 1000 USDC 상당의 토큰을 구매하기 위해 거래를 제출합니다.
- 앨리스의 거래가 먼저 처리되어 1000 USDC 상당의 토큰을 받습니다.
- 밥의 거래는 두 번째로 처리되지만 계층에 남은 것이 전부이므로 동의 없이 500 USDC 상당의 토큰만 받습니다.
- 밥의 구매는 거래가 되돌려지는 것을 선호했을 수 있음에도 불구하고 부분 채결로 처리됩니다.

`depositUSDC` 함수에 `minTokensOut` 매개변수를 추가하십시오.

# [L-10] `createVesting()` 액세스 제어 부족

`VestingManager` 계약의 `createVesting` 함수에는 액세스 제어 메커니즘이 없어 충분한 토큰이 있고 계약을 승인하는 한 모든 주소가 베스팅 일정을 생성할 수 있습니다.

```solidity
function createVesting(VestParams calldata vestParams)
    external  // No access control modifier
    override
    returns (uint256 depositedShares, uint256 vestId, uint128 stepShares, uint128 cliffShares)
{
    // Only technical validations, no authorization checks
    if (vestParams.start < block.timestamp) revert InvalidStart();
    depositedShares = _depositToken(vestingAsset, msg.sender, vestParams.amount);
    // ...
}
```

[src/token/VestingManager.sol#L58](https://github.com/defi-app/defi-app-contracts/blob/b64eb41a74dc02655eb3e10ef2b6fe34b4ff51c6/src/token/VestingManager.sol#L58)

`PublicSale` 계약은 특정 규칙(즉, 단계, stepDuration 등)으로 베스팅 일정을 생성하기 위한 기본 인터페이스로 설계되었지만, `VestingManager`의 액세스 제어 부족은 사용자가 다음과 같은 방법으로 `PublicSale` 계약을 완전히 우회할 수 있음을 의미합니다:

- `VestingManager`와 직접 상호 작용
- 의도한 베스팅 일정과 일치하지 않을 수 있는 사용자 정의 매개변수로 베스팅 생성

`createVesting` 함수에 액세스 제어를 추가하십시오. 승인된 주소만 이 함수를 호출할 수 있도록 하십시오.
