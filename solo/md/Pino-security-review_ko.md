# 소개

**Pino** 프로토콜에 대한 시간 제한 보안 검토가 **pashov**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견한 그는 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# **Pino** 소개

Pino 프로토콜은 사용자와 인기 있는 DeFi 프로토콜(Aave, Curve, Compound, Uniswap 등) 사이에서 프록시 역할을 하는 프로토콜입니다. 프로토콜과의 통합 위에 `Multicall`, `Permit` 및 기타 기능을 추가하여 사용자의 상호 작용을 단순화합니다.

[추가 문서](https://plausible-zebu-7fd.notion.site/Pino-Contract-wiki-5be0cdcfe5c94953b180231c6f396a76)

## 관찰 사항

누구나 프로토콜의 계약 중 하나가 보유한 토큰의 지출자로 자신을 승인하도록 할 수 있습니다.

누구나 모든 계약에서 모든 ERC20을 휩쓸(sweep) 수 있습니다.

프로토콜은 트랜잭션 간에 수수료로 받은 ETH 잔액(오직 `owner`만 인출할 수 있음)을 제외하고는 어떤 잔액도 보유해서는 안 됩니다.

## 특권 역할 및 행위자

여기서 유일한 특권 역할은 `BaseProtocolProxy` 소유자이며, 모든 계약에 대한 `owner` 역할을 보유하고 ETH 잔액을 인출하거나 `Aave` 및 `Swap` 계약의 주소를 업데이트할 수 있습니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

**영향 (Impact)** - 성공적인 공격으로 인한 기술적, 경제적, 평판적 손해

**가능성 (Likelihood)** - 특정 취약점이 발견되고 악용될 확률

**심각도 (Severity)** - 위험의 전반적인 중요성

# 보안 평가 요약

**_검토 커밋 해시_ - [e11214c8eb52fd967d496888999b0327a8f28a93](https://github.com/nitolabs/pino-contract/tree/e11214c8eb52fd967d496888999b0327a8f28a93)**

**_수정 검토 커밋 해시_ - [7596a1dab98f712c5fdf2499d091ddc2c8a6bed7](https://github.com/nitolabs/pino-contract/tree/7596a1dab98f712c5fdf2499d091ddc2c8a6bed7)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `base/BaseProtocolProxy`
- `base/Multicall`
- `base/Permit`
- `protocols/v2/Balancer`
- `protocols/v2/Curve`
- `protocols/v2/Uniswap`
- `protocols/Aave`
- `protocols/Compound`
- `protocols/Invest`
- `protocols/Swap`
- `interfaces/**`

---

# 발견 사항 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [C-01] | `Curve::withdraw`를 호출하면 사용자가 ETH를 잃을 가능성이 높음 | 치명적 | 수정됨 |
| [C-02] | 수수료 메커니즘이 강제되지 않음 | 치명적 | 인지됨 |
| [M-01] | `Curve`와의 통합에 결함이 있음 | 중간 | 인지됨 |
| [M-02] | `nonETHReuse` 수정자가 있는 메서드 호출이 강제로 되돌려질 수 있음 | 중간 | 인지됨 |
| [M-03] | 계약 소유자가 사용자에게 악의적으로 프런트 런(front-run)을 할 수 있음 | 중간 | 수정됨 |
| [L-01] | `Uniswap` 기능에 기한(deadline) 매개변수가 없음 | 낮음 | 수정됨 |
| [L-02] | 가짜 Curve 풀과 Compound 토큰을 사용할 수 있음 | 낮음 | 인지됨 |

# 상세 발견 사항

# [C-01] `Curve::withdraw`를 호출하면 사용자가 ETH를 잃을 가능성이 높음

## 심각도

**영향:**
높음, 사용자가 ETH 가치를 잃을 수 있으므로

**가능성:**
높음, ETH가 있는 75개 이상의 모든 Curve 풀이 문제가 되므로

## 설명

`Curve::withdraw` 메서드는 풀에서 유동성을 제거하여 LP 토큰을 소각하고 기본 자산을 수신한 후 사용자가 자신의 지갑으로 휩쓸 수(sweep) 있도록 합니다. 이 문제점은 일부 풀이 ETH를 기본 자산으로 사용하고 `remove_liquidity` 메서드가 호출자에게 ETH를 반환한다는 것입니다. 이런 경우 사용자가 계약의 ETH 잔액을 휩쓸 방법이 없기 때문에 ETH가 계약에 갇히게 됩니다(`owner`는 자신을 위해 인출할 수 있음). 이것은 `withdrawOneCoinI` 및 `withdrawOneCoinU`에서는 처리되지만 `withdraw`에서는 처리되지 않습니다.

다음은 실행 가능한 개념 증명(POC) 단위 테스트입니다. `curve_pool.test.ts` 파일의 `Remove Liquidity` 제품군에 마지막 테스트로 추가하여 실행하십시오:

```javascript
    it('User can not claim his ETH liquidity', async () => {
      const { contract, sign } = await loadFixture(deploy);

      const POOL = '0xdc24316b9ae028f1497c275eb9192a3ea0f67022';
      const POOL_TOKEN = '0x06325440d014e39736583c165c2963ba99faf14e';

      const proxyFee = 5n;
      const ethAmount = 1n * 10n ** 18n;

      const amounts = [ethAmount, 0]; // eth - steth

      const poolToken = await ethers.getContractAt(
        'IERC20',
        POOL_TOKEN,
      );

      const poolTokenBalanceBefore = await poolToken.balanceOf(
        account.address,
      );

      const addTx = await contract.populateTransaction.deposit(
        amounts,
        0,
        POOL,
        proxyFee,
      );
      const sweepTx = await contract.populateTransaction.sweepToken(
        POOL_TOKEN,
        account.address,
      );

      await contract.multicall([addTx.data, sweepTx.data], 0, {
        value: ethAmount + proxyFee,
      });

      const poolTokenBalanceAfter = await poolToken.balanceOf(
        account.address,
      );

      expect(poolTokenBalanceAfter).to.gt(poolTokenBalanceBefore);

      await poolToken.approve(PERMIT2_ADDRESS, constants.MaxUint256);

      const { permit, signature } = await sign(
        {
          amount: poolTokenBalanceAfter,
          token: POOL_TOKEN,
        },
        contract.address,
      );

      const approveTx =
        await contract.populateTransaction.approveToken(POOL_TOKEN, [
          POOL,
        ]);
      const permitTx =
        await contract.populateTransaction.permitTransferFrom(
          permit,
          signature,
        );

      const removeLiquidityTx =
        await contract.populateTransaction.withdraw(
          poolTokenBalanceAfter,
          [0, 0],
          POOL,
        );

      // remove liquidity then try with unwrapping, as if the contract wrapped the received ETH in WETH
      const unwrapTx = await contract.populateTransaction.unwrapWETH9(
        account.address,
      );

      let ethBalanceBefore = await account.getBalance();
      await contract.multicall(
        [
          approveTx.data,
          permitTx.data,
          removeLiquidityTx.data,
          unwrapTx.data,
        ],
        0,
      );

      let ethBalanceAfter = await account.getBalance();
      expect(ethBalanceBefore).to.gt(ethBalanceAfter); // balance before is actually more than current because of paying gas - it should have been ~1 ETH more, because of `ethAmount`
    });
  });
```

## 권장 사항

`withdrawOneCoinI` 및 `withdrawOneCoinU`에서의 처리 방식, 즉 ETH 잔액을 사용자가 자신의 지갑으로 휩쓸 수 있는 `WETH`로 래핑(wrapping)하는 방식을 사용하십시오.

# [C-02] 수수료 메커니즘이 강제되지 않음

## 심각도

**영향:**
높음, 프로토콜이 수수료 형태의 잠재적 수익을 잃을 수 있으므로

**가능성:**
높음, 사용자가 허가 없이(permissionless) 이러한 거래를 조작할 수 있으므로

## 설명

코드베이스는 사용자가 일부 기능을 사용하기 위해 수수료를 지불하는 수수료 메커니즘을 사용하고 있습니다. 다음은 `Compound::depositETHV2` 메서드에서 수행되는 예입니다:

```solidity
    function depositETHV2(address _recipient, uint256 _proxyFeeInWei) external payable nonETHReuse {
        address _cEther = address(cEther);

        ICEther(_cEther).mint{value: msg.value - _proxyFeeInWei}();
    ....
    ....
```

이 접근 방식의 문제점은 수수료 값이 `_proxyFeeInWei` 인수를 통해 사용자에 의해 제어된다는 것입니다. 즉, 사용자는 항상 0 값을 보내 수수료를 지불하지 않을 수 있습니다.

## 권장 사항

사용자에게 수수료를 강제할 수 있도록 수수료 접근 방식을 재설계하십시오. 예를 들어, 합리적인 관리자 설정 값을 사용하여 이를 수행할 수 있습니다.

# [M-01] `Curve`와의 통합에 결함이 있음

## 심각도

**영향:**
낮음, 사용자는 자금을 잃지 않지만 프로토콜 계약에 새로운 구현 및 재배포가 필요할 수 있으므로

**가능성:**
높음, 사용자가 Curve 풀의 큰 부분을 사용할 수 없으므로

## 설명

현재 `Curve` 메서드 `deposit` 및 `withdraw`는 Curve 풀의 기본 토큰 수를 정확히 2개로 하드코딩하고 있습니다. 일부 풀에는 3개 이상의 기본 토큰이 있으며 현재 구현으로는 사용자가 프록시 호출을 할 수 없으므로 프로토콜의 기능이 제한되기 때문에 이는 올바르지 않습니다.

## 권장 사항

`Curve`의 메서드를 변경하여 풀의 기본 토큰 수가 다를 수 있도록 작동하게 하고, 적절한 유효성 검사를 통해 이를 수행하십시오.

# [M-02] `nonETHReuse` 수정자가 있는 메서드 호출이 강제로 되돌려질 수 있음

## 심각도

**영향:**
중간, 사용자의 트랜잭션이 되돌려지지만 `Multicall` 호출을 통해 다시 실행할 수 있으므로

**가능성:**
중간, 앱을 사용하는 일반적인 방법이 아닌 해당 메서드에 대한 직접 호출이 있을 때만 발생할 수 있으므로

## 설명

`protocols/` 아래의 계약에서 상당수의 메서드에 `nonETHReuse` 수정자가 있는 것을 볼 수 있습니다. 수정자 코드는 다음 메서드를 호출합니다:

```solidity
    function _nonReuseBefore() private {
        // On the first call to nonETHReuse, _status will be NOT_ENTERED
        if (_status == ENTERED) {
            revert EtherReuseGuardCall();
        }

        // Any calls to nonETHReuse after this point will fail
        _status = ENTERED;
    }
```

이 코드는 수정자가 있는 메서드가 연속으로 두 번 호출되면 두 번째 호출이 되돌려짐을 의미합니다. 계약을 "잠금 해제"하는 유일한 방법은 `Multicall::multicall` 호출을 통해 `_status = NOT_ENTERED;`를 설정하는 것입니다. 이로 인해 다음과 같은 공격이 실행될 수 있습니다:

1. Alice가 `nonETHReuse` 수정자가 있는 메서드(예: `Compound::depositWETHV2`)를 직접(`Multicall`을 통하지 않고) 호출하려고 합니다.
2. Bob은 Alice의 트랜잭션을 보고 `nonETHReuse` 수정자가 있는 메서드(예: `Compound::depositETHV2`)에 대한 또 다른 직접 호출로 프런트 런(front-run)합니다.
3. Bob의 트랜잭션이 먼저 실행되었으므로 이제 `_status == ENTERED`가 되어 Alice의 트랜잭션이 되돌려집니다.

## 권장 사항

메서드에 대한 직접 호출을 금지하고 `Multicall`을 통해서만 수행되도록 강제하는 것을 고려하십시오.

# [M-03] 계약 소유자가 사용자에게 악의적으로 프런트 런(front-run)을 할 수 있음

## 심각도

**영향:**
높음, 사용자의 자금 손실을 초래할 수 있으므로

**가능성:**
낮음, 악의적이거나 침해된 소유자가 필요하므로

## 설명

`Swap` 및 `Aave` 계약에는 계약 `owner`만 호출할 수 있는 `setNewAddresses` 기능이 있습니다. 사용자가 `Multicall` 호출을 프로토콜에 보내고 `Swap` 또는 `Aave` 계약을 사용하는 경우 `owner`는 주소를 자신이 제어하는 악의적인 계약으로 업데이트하여 호출을 프런트 런(front-run)할 수 있습니다. 악의적인 계약은 사용자 자산을 수신하고 아무것도 돌려주지 않을 수 있습니다.

## 권장 사항

계약은 트랜잭션 간에 어떠한 가치나 허용량도 보유해서는 안 되므로 두 계약 모두에서 메서드를 제거하십시오. 주소를 업데이트하려면 새 `Swap` 또는 `Aave` 계약을 배포하고 프런트 엔드에서 호출을 전달하도록 하면 됩니다.

# [L-01] `Uniswap` 기능에 기한(deadline) 매개변수가 없음

다음 공격 벡터로 인해 `Uniswap` 프록시 계약은 트랜잭션 기한 메커니즘을 사용해야 합니다:

1. Alice가 스왑을 실행하려고 슬리피지를 10%로 설정하고 멤풀에 트랜잭션을 보내지만 가스 요금이 매우 낮습니다.
2. 채굴자/검증자는 트랜잭션을 보지만 수수료가 매력적이지 않아 트랜잭션이 오랫동안 부실(stale) 및 보류 상태가 됩니다.
3. 일주일 후(예를 들어) 평균 가스비가 채굴자/검증자가 트랜잭션을 실행할 만큼 충분히 낮아졌지만 자산 가격이 급격하게 변경되었습니다.
4. 이제 Alice가 받는 가치는 훨씬 낮으며 설정한 최대 슬리피지에 가까울 가능성이 큽니다.

이 문제를 방지하기 위해 `NonFungiblePositionManager` Uniswap 계약에 전달하는 `IUniswap.MintParams` 구조체에 `deadline` 속성을 추가하는 것이 가장 좋습니다.

# [L-02] 가짜 Curve 풀과 Compound 토큰을 사용할 수 있음

`Curve` 계약의 메서드는 `ICurvePool _pool` 매개변수를 사용합니다. 즉, 사용자가 실제 `Curve` 프로토콜이 아닌 자신이 배포한 계약 주소를 보낼 수 있습니다. `Compound` 계약의 `ICToken _cToken` 매개변수에서도 동일한 상황이 존재합니다. 악의적인 사용자는 이 취약점을 사용하여 두 프로토콜의 실제 사용이 아닌 이벤트를 스팸할 수 있습니다. 풀과 토큰의 미리 정의된 화이트리스트를 사용하여 이를 해결할 수 있습니다.
