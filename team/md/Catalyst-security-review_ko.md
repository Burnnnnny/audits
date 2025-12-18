# 정보 (About)

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**desci-ecosystem** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Catalyst 소개 (About Catalyst)

이 프로토콜을 통해 사용자는 새로운 ERC1155 토큰을 출시하여 "프로젝트"를 생성할 수 있습니다. 각 프로젝트마다 고유하게 구성할 수 있는 가격 본딩 커브(bonding curve)에서 다양한 "프로젝트"를 구매하고 판매할 수 있습니다. 각 커브 거래에는 거래 수수료가 발생합니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
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

**_검토 커밋 해시_ - [e7980268004251020b47ba450c3c684dc0f38247](https://github.com/moleculeprotocol/desci-ecosystem/tree/e7980268004251020b47ba450c3c684dc0f38247)**

**_수정 검토 커밋 해시_ - [0dc2ee33e1e60ba812aa9e3a6da874728c374e4b](https://github.com/moleculeprotocol/desci-ecosystem/tree/0dc2ee33e1e60ba812aa9e3a6da874728c374e4b)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `IPSeed`
- `curves/AlgebraicSigmoidCurve`

# 발견 사항 (Findings)

# [C-01] 공격자는 `tokenId` 재구성으로 인해 IPSeed의 모든 ETH를 유출시킬 수 있음

## 심각도

영향: 높음 (High). 공격자는 IPSeed의 모든 ETH를 유출시킬 수 있습니다.

가능성: 높음 (High). 악용을 막을 수 있는 것은 없습니다.

## 설명

`tokenId`의 메타데이터를 부분적으로 구성할 수 있습니다. `projectId`가 빈 문자열인 동안 생성자는 언제든지 토큰 매개변수를 변경할 수 있습니다.

```solidity
  function spawn(
    uint256 tokenId,
    string calldata name,
    string calldata symbol,
    string calldata projectId,
    IIPSeedCurve curve,
    bytes32 curveParameters,
    address sourcer
  ) public {
    if (tokenId != computeTokenId(_msgSender(), projectId)) {
      revert InvalidTokenId();
    }

    // ERC1155's `exists` function checks for totalSupply > 0, which is not what we want here
    if (bytes(tokenMeta[tokenId].projectId).length > 0) {
      revert TokenAlreadyExists();
    }

    Metadata memory newMetadata =
      Metadata(sourcer, sourcer, name, symbol, projectId, curve, curveParameters);
    tokenMeta[tokenId] = newMetadata;

    emit Spawned(tokenId, sourcer, newMetadata);
  }
```

이 동작은 다음과 같은 공격을 도입합니다:

1. 공격자는 특정 `tokenId`에 대해 Curve 주소만 구성합니다.
2. Curve 매개변수가 0이므로 가격이 0이어서 공격자는 해당 `tokenId`를 임의의 양만큼 발행(mint)합니다.
3. 공격자는 해당 `tokenId`를 다시 구성하지만 이제는 실제 값으로 구성합니다.
4. 공격자는 발행된 모든 토큰을 판매하여 IPSeed의 전체 잔액을 유출시킵니다.

PoC 링크는 다음과 같습니다: https://gist.github.com/T1MOH593/4c28ede6cdc6d183927bb7e14352ea73

## 권장 사항

`tokenId`의 재구성을 허용하지 마십시오. 예를 들어, 비어 있지 않은 `string projectId`를 전달하도록 요구하십시오.

# [C-02] 악의적인 `IIPSeedCurve` 구현으로 인해 공격자는 IPSeed의 모든 ETH를 유출시킬 수 있음

## 심각도

영향: 높음 (High). 공격자는 모든 ETH를 유출시킬 수 있습니다.

가능성: 높음 (High). 악용을 막을 수 있는 것은 없습니다.

## 설명

현재 사용자는 `IIPSeedCurve`의 임의 구현을 지정할 수 있습니다.

```solidity
  function spawn(
    uint256 tokenId,
    string calldata name,
    string calldata symbol,
    string calldata projectId,
    IIPSeedCurve curve,
    bytes32 curveParameters,
    address sourcer
  ) public {
    ...

    Metadata memory newMetadata =
@>    Metadata(sourcer, sourcer, name, symbol, projectId, curve, curveParameters);
    tokenMeta[tokenId] = newMetadata;

    emit Spawned(tokenId, sourcer, newMetadata);
  }
```

그러나 Curve 구현은 악의적일 수 있습니다. 예를 들어 구매 시 0 가격을 반환하고 판매 시 임의의 가격을 반환할 수 있습니다. IPSeed 소각 및 발행 시 Curve 구현에서 가격을 인용합니다.

```solidity
  function getBuyPrice(uint256 tokenId, uint256 want)
    public
    view
    returns (uint256 gross, uint256 net, uint256 protocolFee, uint256 sourcerFee)
  {
    net = tokenMeta[tokenId].priceCurve.getBuyPrice(
      totalSupply(tokenId), want, tokenMeta[tokenId].curveParameters
    );
    (protocolFee, sourcerFee) = computeFees(net);
    gross = net + protocolFee + sourcerFee;
  }

  function getSellPrice(uint256 tokenId, uint256 sell)
    public
    view
    returns (uint256 gross, uint256 net, uint256 protocolFee, uint256 sourcerFee)
  {
    gross = tokenMeta[tokenId].priceCurve.getSellPrice(
      totalSupply(tokenId), sell, tokenMeta[tokenId].curveParameters
    );
    (protocolFee, sourcerFee) = computeFees(gross);
    net = gross - protocolFee - sourcerFee;
  }
```

악의적인 Curve 구현은 토큰 가격을 잘못 책정하여 판매 시 ETH를 유출시킬 수 있습니다.

## 권장 사항

화이트리스트에 등록된 `IIPSeedCurve` 구현만 사용하도록 허용하십시오.
# [M-01] `burn()` 함수에 슬리피지 제어 기능이 없음

## 심각도

영향: 중간 (Medium). 사용자가 예상보다 적은 담보를 받습니다.

가능성: 중간 (Medium). 멤풀(mempool)에 트랜잭션을 제출한 후 가격이 하락해야 합니다.

## 설명

현재 사용자가 토큰 판매 시 허용되는 가격을 지정할 수 있는 메커니즘이 없습니다.

따라서 다음과 같은 시나리오가 가능합니다:

1. 사용자가 판매 트랜잭션을 제출합니다.
2. 가격이 하락합니다.
   결과적으로 사용자는 판매에 대해 예상보다 적은 담보를 받습니다. 그리고 예상 금액을 설정할 수 있는 메커니즘이 없습니다.

## 권장 사항

`minOutputAmount`와 같은 인수를 도입하십시오:

```diff
- function burn(address account, uint256 tokenId, uint256 amount)
+ function burn(address account, uint256 tokenId, uint256 amount, uint256 minOutputAmount)
    public
    virtual
    override
    nonReentrant
  {
    ...

    //when selling, gross < net
    (uint256 gross, uint256 net, uint256 protocolFee, uint256 sourcerFee) =
      getSellPrice(tokenId, amount);
+   require(net >= minOutputAmount);
    ...
  }
```

# [L-01] 사용자가 판매 대신 실수로 토큰을 소각할 수 있음

`IPSeed.sol`은 `ERC1155BurnableUpgradeable.sol`을 상속합니다. `burn()`은 판매를 실행하도록 재정의되었지만 `burnBatch()`는 그렇지 않아 여전히 토큰을 소각합니다:

```solidity
    function burnBatch(address account, uint256[] memory ids, uint256[] memory values) public virtual {
        if (account != _msgSender() && !isApprovedForAll(account, _msgSender())) {
            revert ERC1155MissingApprovalForAll(_msgSender(), account);
        }

        _burnBatch(account, ids, values);
    }
```

# [L-02] 악의적인 사용자가 `uri()`에 JSON 주입을 제출할 수 있음

`uri()` 함수는 현재 tokenId 메타데이터에서 JSON을 빌드합니다. 그러나 공격자는 메타데이터를 렌더링하는 웹사이트에 JSON 주입이 포함된 악의적인 메타데이터를 제출할 수 있습니다.

프론트엔드에서 예방 조치를 구현하는 것을 고려하십시오.

# [L-03] 저장 변수를 설정할 때 온전성 검사(sanity checks) 추가

1. `setProtocolFeeBps()` 및 `setSourcerFeeBps()` 함수에서 수수료가 허용 범위 내에 있는지 확인하십시오.
2. `spawn()`에서 메타데이터 매개변수의 유효성 검사를 추가하십시오.

# [L-04] SQRT 오버플로

실험적인(extreme) 매개변수가 있는 일부 엣지 케이스입니다. 조건은 다음과 같습니다:

- "b" 매개변수가 매우 높은 값으로 설정됨
- totalSupply도 높음

이러한 상황에서는 sqrt 함수가 오버플로될 수 있습니다. mint 함수는 getBuyPrice() DoS로 인해 중단됩니다. 29행에서 매우 클 수 있으므로 sqrt 입력 매개변수가 너무 커져 오버플로가 발생하기 때문입니다.

발생할 수 있는 일은 프로젝트가 처음에는 정상적으로 작동하지만 totalSupply가 충분히 커진 후에 자금 조달 프로세스가 중단되고 추가 mint()가 DoS된다는 것입니다.

```solidity
File: packages\desci-contracts\src\curves\AlgebraicSigmoidCurve.sol
27: function collateral(UD60x18 x, UD60x18 a, UD60x18 b, UD60x18 c) internal pure returns (uint256) {
28: UD60x18 b2plusc = (b.mul(b)).add(c);
29: UD60x18 inner = sqrt(((x.mul(x)).add(b2plusc)).sub(ud(2e18).mul(b).mul(x)));
30: UD60x18 result = (x.add(inner)).sub(sqrt(b2plusc)).mul(a);
31:
32: return unwrap(result);
33: }
```

다음은 "a"가 2, "b"가 1e28, "c"가 1e8일 때의 테스트입니다. 핵심은 "b"가 거대한 값으로 설정되면 수식에서 꽤 커질 수 있다는 것입니다.

```solidity
function testExtremeCurve() public {

    AlgebraicSigmoidCurve curve = new AlgebraicSigmoidCurve();

    bytes32 curveParams = bytes32(abi.encodePacked(uint64(2), uint96(1e28), uint96(1e8)));

    console.log("extreme case: %s", curve.getBuyPrice(4e38, 1 ether, curveParams));
}
```

```solidity
Running 1 test for test/Curves.t.sol:CurveTest
[FAIL. Reason: PRBMath_UD60x18_Sqrt_Overflow(152100000000000000000000000000000000000000100000000000000000 [1.521e59])] testExtremeCurve() (gas: 340594)
```

일부 프로젝트의 경우 곡선이 "일정" 가파름을 갖고 상한선이 가능한 한 높을 것으로 예상될 수 있습니다. 따라서 "b" 매개변수는 여기서 가능한 한 높게 설정될 것입니다.

이를 해결하려면 합리적인 값 범위에 대해 a, b, c에 대한 확인을 추가하십시오.

# [L-05] MINIMUM_TRADE_SIZE 값 유출

"b"와 "c"가 크고 "a"가 작고 MINIMUM_TRADE_SIZE가 금액인 경우 `getBuyPrice()`는 0을 반환할 수 있으므로 무료로 발행할 수 있습니다.

```solidity
File: packages\desci-contracts\src\curves\AlgebraicSigmoidCurve.sol
27:   function collateral(UD60x18 x, UD60x18 a, UD60x18 b, UD60x18 c) internal pure returns (uint256) {
28:     UD60x18 b2plusc = (b.mul(b)).add(c);
29:     UD60x18 inner = sqrt(((x.mul(x)).add(b2plusc)).sub(ud(2e18).mul(b).mul(x)));
30:     UD60x18 result = (x.add(inner)).sub(sqrt(b2plusc)).mul(a);
31:
32:     return unwrap(result);
33:   }
```

다음은 테스트입니다.

```solidity
  function testMinimum() public {

    AlgebraicSigmoidCurve curve = new AlgebraicSigmoidCurve();

	// a = 200, b = 2e18, c = 2e18
    bytes32 curveParams = bytes32(abi.encodePacked(uint64(200), uint96(2e18), uint96(2e18)));

    console.log("first token price: %s", curve.getBuyPrice(0, 0.00001 ether, curveParams));
  }
```

```
[PASS] testMinimum() (gas: 349645)
Logs:
  first token price: 0
```

이를 해결하려면 `getBuyPrice()`가 긍정적인 결과를 반환하도록 강제하십시오:

```diff
File: packages\desci-contracts\src\curves\AlgebraicSigmoidCurve.sol
66:   function getBuyPrice(uint256 supply, uint256 want, bytes32 curveParameters)
72:   {
73:     (UD60x18 a, UD60x18 b, UD60x18 c) = decodeParameters(curveParameters);
74:     uint256 startPrice = collateral(ud(supply), a, b, c);
75:     uint256 endPrice = collateral(ud(supply + want), a, b, c);
76: +       require(endPrice > startPrice);
77:     return endPrice - startPrice;
78:   }
```
