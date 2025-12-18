# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**dinaricrypto/usdplus-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Dinari 소개 (About Dinari)

Dinari는 사용자가 스테이블 코인을 사용하여 미국 증권으로 담보된 토큰을 거래할 수 있도록 합니다. USD+는 단기 미국 국채로 담보되는 스테이블 코인으로, 안정적인 가치와 월 1회 수익 분배를 제공합니다. 이 과정은 USDC로 USD+를 주조하고 스마트 계약을 통해 상환하는 것을 포함하며, 유동성은 최대 3일 이내에 상환이 완료되도록 보장합니다.

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

_검토 커밋 해시_ - [1ed01b15d1f69828d8320c5384175c30cdd14377](https://github.com/dinaricrypto/usdplus-contracts/tree/1ed01b15d1f69828d8320c5384175c30cdd14377)

_수정 검토 커밋 해시_ - [1de4ba609a7ef187c75b0e5be53da3fa73479feb](https://github.com/dinaricrypto/usdplus-contracts/tree/1de4ba609a7ef187c75b0e5be53da3fa73479feb)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `ERC7281Min`
- `SelfPermit`
- `TransferRestrictor`
- `UsdPlus`
- `UsdPlusMinter`
- `UsdPlusRedeemer`
- `WrappedUsdPlus`

# 발견 사항 (Findings)

# [H-01] 토큰 소수점에 대한 잘못된 처리

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`previewDeposit()`, `previewMint()`, `previewWithdraw()` 및 `previewRedeem()`은 `paymentToken`의 소수점을 고려하지 않고 반환된 금액을 계산합니다.

이로 인해 작업 중 정밀도 불일치가 발생하여 잘못된 금액이 처리될 수 있습니다.

다음 시나리오를 가정해 보십시오:

- `paymentToken`의 소수점은 `18`입니다 (예: `DAI`).
- `USD`+ 토큰의 소수점은 `6`입니다.

각 기능 사용에 미치는 영향:

- `previewDeposit()`:

주조할 `usdPlusAmount`는 `paymentToken` 소수점(18)을 사용하여 계산되므로 사용자의 USD+ 금액이 과대평가됩니다 (`1e12`만큼 확대).

- `previewMint()`

주조에 필요한 `paymentTokenAmount`는 USD+ 소수점(6)을 사용하여 계산되므로 사용자가 의도한 것보다 적게 지불하고 USD+를 주조하게 됩니다.

- `previewWithdraw()`

반환된 `usdPlusAmount`가 18 소수점으로 계산되어 비현실적으로 큰 금액을 나타내므로 자금 부족으로 인해 트랜잭션이 되돌려질 수 있습니다.

- `previewRedeem()`

`paymentTokenAmount`는 더 작은 USD+ 소수점(6)으로 추적되므로 사용자는 상환이 완료될 때 예상보다 적은 가치를 받게 됩니다.

## 권장 사항

모든 계산에서 허용되는 결제 토큰 소수점을 처리하도록 고려하십시오.

# [M-01] `rebaseMul()`이 의도한 대로 작동하지 않음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`rebaseMul()` 함수는 주당 가치 잔액을 일정 비율만큼 증가시킵니다. 문제는 코드가 정수 비율만 허용하므로 주당 잔액을 작은 비율로 늘리는 것이 불가능하다는 것입니다.

```solidity
    function rebaseMul(uint128 factor) external onlyRole(OPERATOR_ROLE) {
        uint128 _balancePerShare = balancePerShare() * factor;
        UsdPlusStorage storage $ = _getUsdPlusStorage();
        $._balancePerShare = _balancePerShare;
        emit BalancePerShareSet(_balancePerShare);
    }
```

## 권장 사항

분모와 분자를 사용하여 증가 비율을 지정하십시오.

# [M-02] 공격자가 `privateMint()`를 서비스 거부(DOS)할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`privateMint()` 함수는 토큰 전송 전에 허용량(allowance)을 설정하기 위해 `permit()`을 호출합니다:

```solidity
        IERC20Permit(address(paymentToken)).permit(permit.owner, address(this), permit.value, permit.deadline, v, r, s);
        usdPlusAmount = permit.value;

        _issue(paymentToken, permit.value, permit.value, permit.owner, permit.owner);
```

문제는 공격자가 멤풀(mempool)을 감시하고 서명을 사용하여 permit을 호출할 수 있으며, 이로 인해 사용된 nonce로 인해 원래 트랜잭션이 되돌려질 수 있다는 것입니다. 결과적으로 공격자는 `privateMint()` 함수에 대한 호출을 DOS할 수 있습니다. `selfPermit()` 함수에도 동일한 문제가 존재합니다.

## 권장 사항

permit을 호출할 때 try/catch를 사용하고 현재 허용량이 지출 금액보다 큰 경우 로직이 실행되도록 허용하십시오.
[링크](https://www.trust-security.xyz/post/permission-denied)

# [M-03] 사용되지 않는 `privateMint()`는 많은 문제에 취약함

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`privateMint()`는 사용되지 않는(deprecated) 함수로 표시되어 있습니다. 그러나 이 함수는 여러 문제에 취약합니다:

1. `privateMint()`에만 사용되는 `splitSignature()`는 짧은 서명(64길이 서명)에서는 작동하지 않습니다:
   [링크](https://eips.ethereum.org/EIPS/eip-2098)

2. USD+ 주조를 위한 직접 가치(`usdPlusAmount`)는 특히 paymentToken이 USD+가 아닌 경우(예: permit 기능도 있는 USDC) 실제 금액을 반영하기 위해 오라클 변환을 수행하지 않고 사용됩니다. 이는 주조 시점에 USD 토큰의 가격이 과대 또는 과소 책정될 수 있습니다.

## 권장 사항

프로토콜이 올바르게 작동하고 공격 벡터를 줄이기 위해 사용되지 않는 함수를 제거하거나 작업을 올바르게 처리하도록 함수를 업데이트하는 것을 고려하십시오:

1. `splitSignature()`의 경우 짧은 서명 처리에 대해 다음을 참조하십시오:
   [링크](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v5.1.0/contracts/utils/cryptography/ECDSA.sol)

2. `paymentToken`의 현재 오라클 가격과 함께 주조 가치를 계산하기 위해 `previewDeposit()`을 사용하는 것을 고려하십시오.

# [M-04] 오라클 가격 피드에 대한 데이터 검증 부족

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`USDPlusMinter` 및 `USDPlusRedeemer`(Chainlink의 `latestRoundData()`를 사용할 때)의 가격 피드 데이터 검증에 몇 가지 문제가 있습니다:

- 가격 > 0에 대한 검증 누락:
  가격이 0보다 큰지 확인하는 검사가 없어 잘못된 가격 데이터가 허용될 수 있습니다.

- 가격의 오래됨(Staleness) 확인 안 됨:
  계약은 가격 피드 데이터가 최신인지 확인하지 않아 오래되거나 구식인 가격에 의존할 위험이 있습니다.

- 시퀀서 다운타임 처리:
  시퀀서 다운타임 후의 `GRACE_PERIOD`가 시행되지 않습니다.

## 권장 사항

가격이 유효하고 최신인지 확인하기 위한 유효성 검사를 구현하십시오. 각 토큰의 오라클은 다른 하트비트(heartbeat)를 가질 수 있으므로 최신성 검증은 이러한 차이를 개별적으로 고려해야 합니다.
[링크](https://docs.chain.link/data-feeds/price-feeds/historical-data)

시퀀서 다운타임 처리의 경우 문서를 따르십시오:
[링크](https://docs.chain.link/data-feeds/l2-sequencer-feeds#example-code)

# [M-05] 사용자가 리베이스 함수를 프론트런(Frontrun)할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

UsdPlus 계약에는 `OPERATOR_ROLE`을 가진 주소가 리베이스를 수행할 수 있도록 하는 `rebaseAdd`, `rebaseMul`, `rebaseSub` 함수가 포함되어 있습니다. 이 함수들은 실행된 작업에 따라 `$._balancePerShare`의 값을 조정하여 늘리거나 줄입니다.

```solidity
    function rebaseAdd(uint128 value) external onlyRole(OPERATOR_ROLE) {
        uint256 _supply = totalSupply();
        uint128 _balancePerShare = uint128(FixedPointMathLib.fullMulDiv(balancePerShare(), _supply + value, _supply));
        UsdPlusStorage storage $ = _getUsdPlusStorage();
        $._balancePerShare = _balancePerShare;
        emit BalancePerShareSet(_balancePerShare);
    }
```

이 기능은 다음과 같은 방식으로 악용될 수 있습니다:

사용자는 긍정적인 리베이스를 프론트런하여 더 낮은 비율로 토큰을 획득할 수 있습니다. 리베이스가 실행되면 인출 요청을 제출하여 더 높은 비율을 고정할 수 있습니다. 상환 프로세스는 최대 3일이 소요될 수 있지만 `request` 함수는 사용자 요청 티켓의 비율을 고정하여 수익을 확보할 수 있도록 합니다.

사용자는 리베이스가 실행되기 전에 인출 요청을 제출하여 부정적인 리베이스를 프론트런하여 업데이트 전의 비율을 고정할 수도 있습니다. 이를 통해 부정적인 리베이스와 관련된 손실을 피하고 리베이스 조정의 의도된 영향을 효과적으로 우회할 수 있습니다.

## 권장 사항

이러한 악용 시도를 덜 바람직하게 만들기 위해 소액의 인출 수수료를 구현할 수 있습니다. 또한 입금과 인출 요청 사이에 지연을 도입할 수도 있습니다.

# [L-01] 가격이 0에 도달하면 코드가 예기치 않게 동작할 수 있음

운영자는 `rebaseSub()`를 호출하여 주가 가치를 업데이트할 수 있습니다. 어떤 이유로 프로토콜이 모든 예치금을 잃는다면(예: 초기 배포 시) 운영자가 `rebaseSub()`를 호출한 후 일부 사용자가 양의 잔액을 가지고 있는 동안 `_balancePerShare` 값은 0이 됩니다. 그런 다음 누군가가 입금을 하고 USD+ 토큰을 주조하면 코드는 `_INITIAL_BALANCE_PER_SHARE`를 가격으로 사용합니다:

```solidity
    function balancePerShare() public view override returns (uint128) {
        // Override with default if not set due to upgrade
        if (_balancePerShare == 0) return _INITIAL_BALANCE_PER_SHARE;
        return _balancePerShare;
    }
```

결과적으로 예금자의 잔액은 예치한 금액보다 적어지고 잔액이 0이었던 기존 예금자들은 더 높은 잔액 가치를 얻게 됩니다. 문제는 코드가 `_balancePerShare`가 0이면 코드가 시작 상태에 있다고 가정한다는 것입니다. 이 문제를 피하려면 `rebaseSub()`를 호출할 때 주가가 항상 0보다 높게 유지되도록 하는 것이 좋습니다.

# [L-02] 재정의 함수에 "override" 지정자 누락

`mintingMaxLimitOf()`, `burningMaxLimitOf()`, `mintingCurrentLimitOf()`, `burningCurrentLimitOf()` 함수는 `IERC7281Min` 인터페이스에서 상속되었음에도 불구하고 override 지정자를 포함하지 않습니다.

# [L-03] 최대 한도에서 한도를 줄이면 보충하는 데 전체 기간이 소요됨

`_changeMintingLimit()` 및 `_changeBurningLimit()`은 새 한도를 완전히 사용할 수 있기 전에 전체 보충 기간을 잘못 기다립니다.

한도가 `type(uint256).max`에서 새 한도로 줄어들면 함수는 `type(uint256).max`를 `oldMaxLimit`로 사용하여 현재 및 새 한도를 계산합니다. 이 접근 방식은 한도를 사용할 수 있기 전에 1일 보충 기간 동안 새 현재 한도가 0으로 반환되는 결과를 초래합니다.

한도 `type(uint256).max`는 검사를 우회하고 한도를 즉시 사용할 수 있도록 하는 특수한 경우입니다. 반면 한도가 처음으로 다른 값으로 설정되면 전체 1일 보충 기간을 기다리지 않고 즉시 사용할 수 있습니다.

```solidity
function _calculateNewCurrentLimit(uint256 newMaxLimit, uint256 oldMaxLimit, uint256 currentLimit)
    internal
    pure
    returns (uint256)
{
    if (newMaxLimit > oldMaxLimit) {
        return currentLimit + (newMaxLimit - oldMaxLimit);
    }
    uint256 difference = oldMaxLimit - newMaxLimit;
@>  return currentLimit > difference ? currentLimit - difference : 0;
}
```

## 권장 사항

`type(uint256).max`에서 다른 한도로 변경될 때 `oldMaxLimit`를 별도로 처리하는 것을 고려하십시오. 예를 들어, 새로운 한도가 생성되는 것처럼 처리하십시오.

# [L-04] 함수에 슬리피지(Slippage) 보호가 없음

주조 및 인출 과정에서 사용자는 토큰 또는 USD+ 금액을 지정하고 코드는 오라클 가격을 기준으로 수신 또는 발송 금액을 계산합니다. 문제는 트랜잭션이 멤풀에 있는 동안 입금/인출 자산 가격 또는 USD+ 주가가 변경될 수 있으며, 사용자가 가격 변동에 대비하여 슬리피지를 지정할 방법이 없어 결과적으로 사용자가 불공정한 교환을 받을 수 있다는 점입니다.

```solidity
 function mint(IERC20 paymentToken, uint256 usdPlusAmount, address receiver)
 function requestRedeem(IERC20 paymentToken, uint256 usdplusAmount, address receiver, address owner)
```

입금/인출 과정에서 사용자가 최소/최대 금액을 지정할 수 있도록 허용하십시오.

# [L-05] `burn()`은 주식 금액을 계산할 때 올림(round up)해야 함

UsdPlus 계약의 `burn()` 함수는 호출자로부터 지정된 USD+ 토큰을 소각합니다. 인출 과정에서 사용되었습니다.

```solidity
    /// @notice burn USD+ from msg.sender
    function burn(uint256 value) external {
        address from = _msgSender();
        _useBurningLimits(from, value);
        _burn(from, value);
    }
```

USD+는 리베이스 토큰이기 때문에 주식 소각 금액을 계산하기 위해 코드는 `_balancePerShare`를 사용하고 USD+ 금액을 이 값으로 나눕니다.

```solidity
    function balanceToShares(uint256 balance) public view returns (uint256) {
        return mulDiv(balance, _INITIAL_BALANCE_PER_SHARE, balancePerShare()); // floor
    }
```

문제는 이렇게 하면 주식 금액이 내림(round down)되어 결과적으로 코드가 호출자가 지정한 것보다 적게 소각된다는 것입니다. 이로 인해 호출자에게 작은 이익이 발생하고 어떤 상황에서는 악의적인 사용자가 이를 이용해 가치를 추출할 수 있습니다.

`burn()`에서 주식 금액을 계산할 때 계약에 유리하도록 올림(round up)하십시오.
