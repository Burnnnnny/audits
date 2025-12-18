# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**resolv-im/resolv-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# wstUSR 정보

`WstUSR`은 스테이킹된 USR 토큰(`stUSR`)의 래퍼(wrapper)로, 사용자가 기초 토큰과 래핑된 형태 간에 변환할 수 있도록 합니다. `stUSR`은 `USR`과 동일한 가치를 가지며 그 양은 스테이킹 보상에 따라 시간이 지남에 따라 증가합니다. `USR`은 ETH로 뒷받침되고 미달러에 페그된 스테이블코인으로, 헤징 및 보험 유동성 풀을 통해 안정성을 보장합니다.

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

_검토 커밋 해시_ - [4f5498fc548eff4db9e5463f9385d27df8a23296](https://github.com/resolv-im/resolv-contracts/tree/4f5498fc548eff4db9e5463f9385d27df8a23296)

_수정 검토 커밋 해시_ - [c131133ae1413bcafeb217d6eac46ac0b94de53f](https://github.com/resolv-im/resolv-contracts-public/tree/c131133ae1413bcafeb217d6eac46ac0b94de53f)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `WstUSR`

# 발견 사항

# [M-01] `unwrap` 작업에서 반환된 `stUSRAmount`가 반올림으로 인해 부정확할 수 있음

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

사용자가 wstUSR을 `unwrap`할 때 기본 `stUSRAmount`가 작업에서 반환됩니다.

```solidity
    function unwrap(uint256 _wstUSRAmount, address _receiver) public returns (uint256 stUSRAmount) {
        _assertNonZero(_wstUSRAmount);

        IERC20Rebasing stUSR = IERC20Rebasing(stUSRAddress);

>>>     stUSRAmount = stUSR.convertToUnderlyingToken(_wstUSRAmount);
        _burn(msg.sender, _wstUSRAmount);
        IERC20(stUSRAddress).safeTransfer(_receiver, stUSRAmount);

        emit Unwrap(msg.sender, _receiver, stUSRAmount, _wstUSRAmount);

>>>     return stUSRAmount;
    }
```

그러나 반환된 `stUSRAmount` 값은 부정확합니다. 이 문제는 `wstUSR` 계약에서 `stUSR`을 전송할 때와 전송된 지분을 기반으로 사용자의 기본 잔액을 계산할 때 발생하는 추가적인 내림(rounding down)으로 인해 발생합니다.

이제 사용자가 반환된 값을 사용하여 `stUSR.withdraw`를 호출하면 잔액 부족으로 인해 호출이 되돌려집니다.

```solidity
    function withdraw(uint256 _usrAmount, address _receiver) public {
        // @audit - if providing the returned stUSRAmount, the call will revert due to insufficient balance
        uint256 shares = previewWithdraw(_usrAmount);
>>>     super._burn(msg.sender, shares);

        IERC20Metadata usr = super.underlyingToken();
        usr.safeTransfer(_receiver, _usrAmount);
        emit Withdraw(msg.sender, _receiver, _usrAmount, shares);
    }
```

PoC :

```javascript
it("PoC - unwrap and withdraw revert", async () => {
  // given
  const { stUSR, wstUSR, USR, otherAccount2 } = await loadFixture(
    deployFixture
  );
  // Initial deposit
  await USR["mint(address,uint256)"](otherAccount2.getAddress(), parseUSR(100));
  const wstUSRFromAnotherSigner2 = wstUSR.connect(otherAccount2);
  const usrFromAnotherSigner2 = USR.connect(otherAccount2);
  const stUSRFromAnotherSigner2 = stUSR.connect(otherAccount2);
  await usrFromAnotherSigner2.approve(wstUSR.getAddress(), parseUSR(100));
  await wstUSRFromAnotherSigner2["deposit(uint256)"](parseUSR(100));
  // Mint USR to StUSR as a reward
  await USR["mint(address,uint256)"](stUSR.getAddress(), parseUSR(100));

  const withdrawAmount = parseUSR(100);
  const expectedWstUSR = await wstUSR.previewWithdraw(withdrawAmount);
  const expectedUsrInShares = await stUSR.convertToUnderlyingToken(
    expectedWstUSR
  );
  const balancewstBefore = await wstUSR.balanceOf(otherAccount2);
  console.log("balance wstUSR before withdraw : ");
  console.log(balancewstBefore);
  const sharestUSRBefore = await stUSR.sharesOf(otherAccount2);
  console.log("share stUSR before unwrap : ");
  console.log(sharestUSRBefore);
  // when
  const tx = await wstUSRFromAnotherSigner2["unwrap(uint256,address)"](
    expectedWstUSR,
    otherAccount2
  );
  const sharestUSRAfter = await stUSR.sharesOf(otherAccount2);
  console.log("share stUSR after unwrap : ");
  console.log(sharestUSRAfter);
  const tx2 = stUSRFromAnotherSigner2["withdraw(uint256)"](expectedUsrInShares);
  // then
  await expect(tx2)
    .to.revertedWithCustomError(stUSR, "ERC20InsufficientBalance")
    .withArgs(otherAccount2, expectedWstUSR - BigInt(1), expectedWstUSR);
});
```

## 권장 사항

`stUSR`에서 사용자의 잔액을 조회하여 `stUSRAmount`를 업데이트하십시오.

```diff
    function unwrap(uint256 _wstUSRAmount, address _receiver) public returns (uint256 stUSRAmount) {
        _assertNonZero(_wstUSRAmount);

        IERC20Rebasing stUSR = IERC20Rebasing(stUSRAddress);

        stUSRAmount = stUSR.convertToUnderlyingToken(_wstUSRAmount);
+      uint256 balanceBefore = stUSR.balanceOf(_receiver);
        _burn(msg.sender, _wstUSRAmount);
        IERC20(stUSRAddress).safeTransfer(_receiver, stUSRAmount);

+       stUSRAmount = stUSR.balanceOf(_receiver) - balanceBefore;

        emit Unwrap(msg.sender, _receiver, stUSRAmount, _wstUSRAmount);

        return stUSRAmount;
    }
```

# [M-02] `mintWithPermit`이 `usrPermit.permit`에 잘못된 금액을 제공함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`WstUSR`은 사용자가 필요한 USR 금액을 제공하여 계약에서 직접 wstUSR을 민팅할 수 있도록 합니다. 사용자가 permit을 사용하여 민팅하려면 `mintWithPermit` 함수를 활용할 수 있습니다.

```solidity
    function mintWithPermit(
        uint256 _wstUSRAmount,
        address _receiver,
        uint256 _deadline,
        uint8 _v,
        bytes32 _r,
        bytes32 _s
    ) external returns (uint256 usrAmount) {
        IERC20Permit usrPermit = IERC20Permit(usrAddress);
        // the use of `try/catch` allows the permit to fail and makes the code tolerant to frontrunning.
        // solhint-disable-next-line no-empty-blocks
        // @audit - this should provide usrAmount
        try usrPermit.permit(msg.sender, address(this), _wstUSRAmount, _deadline, _v, _r, _s) {} catch {}
        usrAmount = previewMint(_wstUSRAmount);
        _deposit(msg.sender, _receiver, usrAmount, _wstUSRAmount);

        return usrAmount;
    }
```

그러나 필요한 `usrAmount` 대신 `_wstUSRAmount`를 `usrPermit.permit`에 제공합니다. permit에 서명하고 `previewMint` 결과를 사용하여 필요한 USR 금액을 제공하는 사용자 작업은 항상 되돌려집니다.

## 권장 사항

`_wstUSRAmount` 대신 `usrAmount`를 제공하십시오 :

```diff
    function mintWithPermit(
        uint256 _wstUSRAmount,
        address _receiver,
        uint256 _deadline,
        uint8 _v,
        bytes32 _r,
        bytes32 _s
    ) external returns (uint256 usrAmount) {
        IERC20Permit usrPermit = IERC20Permit(usrAddress);
        // the use of `try/catch` allows the permit to fail and makes the code tolerant to frontrunning.
        // solhint-disable-next-line no-empty-blocks
-        try usrPermit.permit(msg.sender, address(this), _wstUSRAmount, _deadline, _v, _r, _s) {} catch {}
        usrAmount = previewMint(_wstUSRAmount);
+        try usrPermit.permit(msg.sender, address(this), usrAmount, _deadline, _v, _r, _s) {} catch {}
        _deposit(msg.sender, _receiver, usrAmount, _wstUSRAmount);

        return usrAmount;
    }
```

# [M-03] previewWithdraw 함수의 잘못된 반올림으로 잔여 wstUSR 잔액 발생

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`WstUSR` 계약의 `previewWithdraw` 함수는 특정 금액의 USR을 인출하는 데 필요한 wstUSR 금액을 계산할 때 잘못된 반올림을 사용합니다. 함수는 올림 대신 내림을 사용하여 프로토콜보다 사용자에게 유리하게 합니다. 이로 인해 사용자가 전체 USR 잔액을 인출하면서도 인출할 수 없는 소량의 잔여 wstUSR 잔액을 남길 수 있습니다.

```solidity
    function previewWithdraw(uint256 _usrAmount) public view returns (uint256 wstUSRAmount) {
        return IStUSR(stUSRAddress).previewWithdraw(_usrAmount) / ST_USR_SHARES_OFFSET; // @audit round down number of wstUSR required
    }
```

POC: [링크](https://gist.github.com/thangtranth/04a3a34989df22d12a03e1791e06656b)

이 테스트 케이스는 1e18 USR을 예치한 후 가능한 최대 금액을 인출한 후에도 계정에 잔여 wstUSR 잔액이 남아 있음을 보여줍니다.

- hardhat에 foundry 추가: [링크](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-and-foundry)
- 파일을 `test/RoundingErrorPOC.t.sol`에 넣고
- `forge test -vvvvv --match-path test/RoundingErrorPOC.t.t.sol --match-test testPOC` 실행

## 권장 사항

필요한 wstUSR 금액을 올림하도록 previewWithdraw 함수를 수정하십시오.

```diff
    function previewWithdraw(uint256 _usrAmount) public view returns (uint256 wstUSRAmount) {
-       return IStUSR(stUSRAddress).previewWithdraw(_usrAmount) / ST_USR_SHARES_OFFSET;
+       return IStUSR(stUSRAddress).previewWithdraw(_usrAmount).ceilDiv(ST_USR_SHARES_OFFSET);
    }
```

# [M-04] `wstUSR.previewWithdraw`가 0을 반환하여 무료 인출이 발생할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`previewWithdraw`는 `_usrAmount`를 받아 `_usrAmount`를 인출하기 위해 소각해야 하는 필수 `wstUSRAmount`를 반환하는 함수입니다. 이 계산은 일반적으로 볼트 문제를 방지하기 위해 필수 지분 금액을 올림하여 볼트에 유리하게 구현됩니다. `stUSR.previewWithdraw`는 이미 가상 지분을 구현하고 계산을 올림하지만 `wstUSR.previewWithdraw`는 다음과 같은 구현을 가지고 있습니다.

```solidity
    function previewWithdraw(uint256 _usrAmount) public view returns (uint256 wstUSRAmount) {
        return IStUSR(stUSRAddress).previewWithdraw(_usrAmount) / ST_USR_SHARES_OFFSET;
    }
```

`IStUSR(stUSRAddress).previewWithdraw(_usrAmount)`의 반환 값이 `ST_USR_SHARES_OFFSET`보다 작으면 필수 `wstUSRAmount`는 0이 됩니다. 이 상황은 공격자나 그리퍼(griefer)가 `stUSR` 볼트 주가를 조작하기 위해 `stUSR`에 `USR`을 기부하는 경우 발생할 수 있습니다. 수익을 내기는 어렵지만, 적은 `withdraw` 작업에 대한 필수 기부금 및 가스 비용으로 인해 수익이 더 줄어들 수 있습니다. 그러나 `stUSR` 볼트 내부의 `USR`의 자연적 성장은 공격을 위한 원하는 주가에 더 빨리 도달하게 할 수 있습니다. 전반적으로 이러한 요소들의 조합은 공격을 더 저렴하고 수익성 있게 만들 수 있습니다.

PoC :

```javascript
it("PoC - previewWithdraw return 0", async () => {
  // given
  const { otherAccount, otherAccount1, USR, stUSR, wstUSR } = await loadFixture(
    deployFixture
  );
  const wstUSRFromAnotherSigner = wstUSR.connect(otherAccount);

  await USR["mint(address,uint256)"](otherAccount, parseUSR(3000));
  const usrFromAnotherSigner = USR.connect(otherAccount);
  await usrFromAnotherSigner.approve(wstUSR, parseUSR(3000));

  // when
  const tx = await wstUSRFromAnotherSigner["deposit(uint256)"](parseUSR(3000));
  const balancewstAfter = await wstUSR.balanceOf(otherAccount);
  console.log("balance wstUSR after deposit : ");
  console.log(balancewstAfter);
  // test
  const totalSupplyBefore = await stUSR.totalSupply();
  console.log("total supply before : ");
  console.log(totalSupplyBefore);
  const totalShares = await stUSR.totalShares();
  console.log("total shares : ");
  console.log(totalShares);
  await USR["mint(address,uint256)"](otherAccount1, parseUSR(300000));
  const usrFromAnotherSigner1 = USR.connect(otherAccount1);
  await usrFromAnotherSigner1.transfer(stUSR, parseUSR(300000));
  const previewWIthdraw = await wstUSR.previewWithdraw(100);
  console.log("previewWithdrawResult : ");
  console.log(previewWIthdraw);
});
```

로그 출력 :

```shell
balance wstUSR after deposit :
3000000000000000000000n
total supply before :
3000000000000000000000n
total shares :
3000000000000000000000000n
previewWithdrawResult :
0n
```

권장 사항 :

`wstUSR.previewWithdraw`의 결과가 0이면 `withdraw` 작업을 되돌리십시오.

```diff
    function withdraw(uint256 _usrAmount, address _receiver, address _owner) public returns (uint256 wstUSRAmount) {
        uint256 maxUsrAmount = maxWithdraw(_owner);
        if (_usrAmount > maxUsrAmount) revert ExceededMaxWithdraw(_owner, _usrAmount, maxUsrAmount);
        wstUSRAmount = previewWithdraw(_usrAmount);
+      _assertNonZero(wstUSRAmount);
        _withdraw(msg.sender, _receiver, _owner, _usrAmount, wstUSRAmount);

        return wstUSRAmount;
    }
```

# [L-01] 고래가 보상 분배를 프론트러닝할 수 있음

WstUSR 볼트는 사용자가 동일한 블록 내에서 예금과 인출을 할 수 있도록 합니다. 이를 통해 악의적인 사용자가 멤풀을 모니터링하고 대규모 예금을 하여 보상 분배를 프론트러닝한 후 동일한 블록 내에서 인출하여 보상의 일부를 빼돌릴 수 있습니다. 이 취약점을 완화하려면 예금과 인출 사이에 지연을 시행하는 것을 고려하십시오.

# [L-02] WstUSR이 EIP-4626을 준수하지 않음

WstUSR 구현은 `stUSRAddress.totalSupply()`를 `totalManagedUsrAmount`로 반환합니다.

```solidity
    function totalAssets() external view returns (uint256 totalManagedUsrAmount) {
        return IERC20Rebasing(stUSRAddress).totalSupply();
    }
```

EIP-4626에 따르면:

> totalAssets: 볼트가 "관리"하는 기본 자산의 총량.

- `totalAssets()` 함수의 목적은 볼트에 현재 보관된 기본 자산의 총량을 제공하는 것입니다. 이는 계약이 이 계약을 통해서만 예치(또는 전송)된 자산을 추적해야 함을 의미합니다.

- 그러나 `WstUSR.totalAssets()`는 `stUSR.totalSupply()`를 반환하므로 모든 `stUSR` 기본 자산이 `WstUSR`에 의해 관리되는 것은 아니기 때문에 EIP-4626 표준과 호환되지 않습니다.

- 이 구현은 향후 예상치 못한 동작 및 통합 문제로 이어질 수 있습니다.

EIP-4626 표준을 따르거나 이 동작을 적절하게 문서화하는 것을 고려하십시오.

# [L-03] 반올림으로 인해 계약에 소량의 stUSR이 축적될 가능성

WstUSR 계약의 wrap 및 unwrap 함수는 stUSR와 wstUSR 간의 변환 과정에서 발생하는 반올림으로 인해 시간이 지남에 따라 소량의 stUSR(더스트)이 계약에 축적될 수 있습니다.

`wrap` 함수에서 사용자는 `_stUSRAmount`의 stUSR을 전송하지만 `ST_USR_SHARES_OFFSET`(1000) 비율만큼 감소된 `wstUSRAmount`의 wstUSR을 받습니다.

```solidity
    function wrap(uint256 _stUSRAmount, address _receiver) public returns (uint256 wstUSRAmount) {
        ...

        wstUSRAmount = stUSR.convertToShares(_stUSRAmount) / ST_USR_SHARES_OFFSET;

        IERC20(stUSRAddress).safeTransferFrom(msg.sender, address(this), _stUSRAmount);
        ...
    }
```

반대로 `unwrap` 함수에서 사용자는 `_wstUSRAmount * ST_USR_SHARES_OFFSET` 양의 stUSR 지분을 받습니다.

```solidity
    function unwrap(uint256 _wstUSRAmount, address _receiver) public returns (uint256 stUSRAmount) {
        _assertNonZero(_wstUSRAmount);

        IERC20Rebasing stUSR = IERC20Rebasing(stUSRAddress);

        uint256 stUSRSharesAmount = _wstUSRAmount * ST_USR_SHARES_OFFSET;
        stUSRAmount = stUSR.convertToUnderlyingToken(stUSRSharesAmount);
        _burn(msg.sender, _wstUSRAmount);
        // slither-disable-next-line unused-return
        stUSR.transferShares(_receiver, stUSRSharesAmount);
        ...
    }
```

이러한 작업의 조합은 `_stUSRAmount / ST_USR_SHARES_OFFSET * ST_USR_SHARES_OFFSET`으로 나타낼 수 있습니다. 정수 나눗셈으로 인해 이 값은 원래 `_stUSRAmount`보다 약간 작은 값이 되어 계약에 소량의 stUSR이 남을 수 있습니다.
