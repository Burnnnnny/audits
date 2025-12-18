# Pashov Audit Group 정보

Pashov Audit Group은 업계에서 잘 증명된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 벌었거나, 다회 챔피언이거나, 우리와 함께한 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하고 패치하는 데 도움을 주면서, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**RegnumAurumAcquisitionCorp/fx-contracts** 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며, **rvierdiiev, merlinboii, Shaka**가 **RWf(x)** 검토에 참여했습니다. 총 **2**개의 문제가 발견되었습니다.

# RWf(x) 정보

RWf(x)는 부분 금(fGOLD)과 같은 RWA 기반 토큰을 담보로 사용하여 스테이블코인(fToken)과 레버리지 토큰(xToken)을 민팅하는 프로토콜입니다. 이를 통해 수익 창출 자산을 안정적인 수익 기반 코인(goldUSD)과 레버리지 자산(xGOLD)으로 분할하여 안정성과 변동성 노출의 균형을 맞출 수 있습니다.

# 보안 평가 요약

**검토 커밋 해시:**
• [f6e865df2dd46d67a49391d94e54b26e6a8af43c](https://github.com/RegnumAurumAcquisitionCorp/fx-contracts/tree/f6e865df2dd46d67a49391d94e54b26e6a8af43c)
  (RegnumAurumAcquisitionCorp/fx-contracts)

**수정 검토 커밋 해시:**
• [80c514e163f5f8effaa3ba0c4b25cf658a939434](https://github.com/RegnumAurumAcquisitionCorp/fx-contracts/tree/80c514e163f5f8effaa3ba0c4b25cf658a939434)
  (RegnumAurumAcquisitionCorp/fx-contracts)

# 범위

- `FxLowVolatilityMath.sol`
- `HarvestableTreasury.sol`
- `Market.sol`
- `Treasury.sol`
- `IFxMarket.sol`
- `IFxTreasury.sol`
- `IRWAVaultPriceOracle.sol`

# 발견 사항

# [H-01] 시스템이 담보 부족 상태인 경우 중요 함수가 되돌려짐(revert)

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

내부 함수 `Treasury._loadSwapState()`는 다음과 같이 `xNav`를 계산합니다.

```solidity
_state.xNav = _state.baseSupply.mul(_state.baseNav).sub(_state.fSupply.mul(_state.fNav)).div(_state.xSupply);
```

이는 다음과 같습니다.

```js
(baseSupplyNav - fSupplyNav) / xSupply
```

시스템이 담보 부족 상태(`baseSupplyNav < fSupplyNav`)인 경우 뺄셈 언더플로우로 인해 `xNav` 계산이 되돌려집니다. `_loadSwapState()` 실행을 포함하는 모든 트랜잭션이 실패하며, 여기에는 담보 비율을 높여 건전한 상태로 되돌리려는 모든 작업이 포함됩니다.

결과적으로 프로토콜은 담보 비율이 100% 미만으로 떨어지면 이를 회복할 메커니즘이 없습니다.

### 개념 증명

`Market.spec.ts` 테스트 파일에 다음 코드를 추가하십시오.

```ts
  context.only("audit", async () => {
    beforeEach(async () => {
      await oracle.setPrice(100000000000); // $1000 with 8 decimals
      await treasury.initializePrice();
      await weth.deposit({ value: ethers.parseEther("10") });
      await weth.approve(market.getAddress(), MaxUint256);
      await market.mint(ethers.parseEther("1"), deployer.address, 0, 0);
    });

    it("reverts when collateral ratio is below 100%", async () => {
      // The system becomes undercollateralized
      await oracle.setPrice(65000000000); // $650 with 8 decimals

      // Manager tries to raise collateral ratio, but transactions revert
      await expect(market.mintXToken(ethers.parseEther("1"), signer.address, 0))
        .to.revertedWith("SafeMath: subtraction overflow");
      await expect(market.addBaseToken(ethers.parseEther("1"), signer.address, 0))
        .to.revertedWith("SafeMath: subtraction overflow");
    });
  });
```

## 권장 사항

```diff
  function redeem(
(...)
-   _baseOut = _state.redeem(_fTokenIn, _xTokenIn);
+   if (_state.xNav == 0) {
+     require (_xTokenIn == 0, "Undercollateralalized");
+     // only redeem fToken proportionally when under collateral.
+     _baseOut = _fTokenIn.mul( _state.baseSupply).div(_state.fSupply);
+   } else {
+     _baseOut = _state.redeem(_fTokenIn, _xTokenIn);
+   }

(...)

    if (_state.xSupply == 0) {
        // no xToken, treat the nav of xToken as 1.0
        _state.xNav = PRECISION;
	} else {
-		_state.xNav = _state.baseSupply.mul(_state.baseNav).sub(_state.fSupply.mul(_state.fNav)).div(_state.xSupply);
+		uint256 baseSupplyNav = _state.baseSupply.mul(_state.baseNav);
+		uint256 fSupplyNav = _state.fSupply.mul(_state.fNav);
+		if (baseSupplyNav <= fSupplyNav) {
+			_state.xNav = 0;
+		} else {
+			_state.xNav = baseSupplyNav.sub(fSupplyNav).div(_state.xSupply);
+		}
	}
```

# [M-01] 안정성 모드 중 `fToken` 및 `xToken` 민팅 허용됨

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Market.mint()` 함수는 [현재 담보 비율에 따라](https://github.com/RegnumAurumAcquisitionCorp/fx-contracts/blob/main/contracts/f(x)/math/FxLowVolatilityMath.sol#L293-L307) fToken과 xToken을 모두 민팅합니다.
원래 Aladdin 구현에서는 이 함수를 한 번만 호출할 수 있었습니다. 그러나 RegnumFx는 [이 제한을 제거하여](https://github.com/RegnumAurumAcquisitionCorp/fx-contracts/compare/bbb461cba879349c24c02d87872e93ec0a1a1975...f6e865df2dd46d67a49391d94e54b26e6a8af43c#diff-2c8d19ba3d13b72d110c2a9536e5e9915118ad919b38848357200e91afb683faL252) 여러 번 호출할 수 있도록 했습니다.

시스템이 안정성 모드에 진입하면 담보 비율이 정의된 안전 임계값 아래로 떨어진 것입니다. 이는 비율을 건전한 상태로 되돌리기 위해 추가 기본 토큰을 예치해야 함을 나타냅니다.

안정성 모드 중에 `mint()`를 허용하면 문제가 악화됩니다. 새로운 민팅은 유통되는 fToken의 수를 증가시키고, 이는 다시 시스템을 건전한 상태로 되돌리는 데 필요한 기본 토큰의 양을 증가시킵니다. 결과적으로 회복이 더 어려워지고 시스템이 더 오랫동안 담보 부족 상태로 남을 수 있습니다.

화이트리스트에 등록된 관리자만이 이 함수를 사용할 수 있고, 그들은 스테이블코인 디페깅을 만드는 데 관심이 없는 신뢰할 수 있는 엔터티이기 때문에 이 문제의 심각도는 중간으로 선택되었습니다.

## 권장 사항

담보 희석을 방지하고 회복을 단순화하기 위해 시스템이 안정성 모드일 때 `mint()` 호출을 제한하십시오.
