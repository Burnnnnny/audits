# 소개 (About)

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **system-contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Hytopia 정보 (About Hytopia)

HYCHAIN은 블록체인(Ethereum L2 롤업)입니다. HYCHAIN Node Key는 HYCHAIN 네트워크에서 가디언 노드(Guardian Node)를 운영하는 데 필수적인 대체 불가능한 토큰(NFT)입니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [ec45c8357aca526d4a83005f2e9b120abbba08ae](https://github.com/HYCHAIN/system-contracts/tree/ec45c8357aca526d4a83005f2e9b120abbba08ae)**

**_수정 검토 커밋 해시_ - [3d342858ad9022f44de2df66e64b2d1c921b2fe3](https://github.com/HYCHAIN/system-contracts/tree/3d342858ad9022f44de2df66e64b2d1c921b2fe3)**

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `HychainNodeKeyPricing`
- `HychainNodeKeyPricingStorage`
- `HychainNodeKey`
- `HychainNodeKeyStorage`
- `HychainNodeKeyTransferManager`
- `HychainNodeKeyTransferManagerStorage`
- `HychainNodeKeyWhitelist`
- `HychainNodeKeyWhitelistStorage`

# 발견 사항 (Findings)

# [H-01] 하나 이상의 가격 등급을 뛰어넘는 수량에 대해 발행 가격이 부정확할 수 있음 (Mint price will be inaccurate for quantities that jumped more than one price tier)

## 심각도

**영향:** 높음 (High), 중간 가격 등급을 고려하지 않아 가격이 크게 달라질 수 있습니다.

**가능성:** 중간 (Medium), 사용자가 대량으로 토큰을 구매하거나 현재 토큰 ID가 현재 가격 등급의 끝에 가까울 때 발생할 수 있습니다.

## 설명

사용자가 `HychainNodeKey`를 발행할 때, `getPriceForQuantity`를 호출하여 현재 토큰 ID와 사용자가 발행하려는 토큰 수량을 기준으로 사용자가 지불해야 할 가격을 계산합니다.

```solidity
  function getPriceForQuantity(uint256 _startingId, uint256 _qty) public pure returns (uint256) {
      if (_qty == 1) {
          return getCurrentPrice(_startingId);
      }
      uint256 _startingPrice = getCurrentPrice(_startingId);
      uint256 _endingPrice = getCurrentPrice(_startingId + _qty - 1);
      if (_endingPrice == _startingPrice) {
          return _startingPrice * _qty;
      }
      // find the quantity in starting price and ending price (ex qty = 5, starting at id #3182 means 2 are at starting price, 3 are at ending price
      uint256 _lastTierId = getLastIdForTier(_startingId);
      // @audit - this will not work properly if jump more than 1 tier
      uint256 _startingPriceQty = _lastTierId - _startingId + 1;
      return (_startingPrice * _startingPriceQty) + (_endingPrice * (_qty - _startingPriceQty));
  }
```

그러나 관찰할 수 있듯이 제공된 시작 토큰 ID와 수량의 `_startingPrice`와 `_endingPrice`만 고려합니다. 제공된 수량이 여러 가격 등급을 통과하는 경우, 첫 번째와 마지막 가격 등급만 고려하고 중간 가격 등급의 정보는 무시합니다.

PoC 시나리오:

- `_startingId`는 3183(가격 등급 1의 끝)이므로 `_startingPrice`는 등급 1 가격을 사용합니다.
- 제공된 `_qty`는 3026이며 토큰 ID가 6208(가격 등급 3의 시작)이 될 때까지 토큰을 발행합니다. 결과적으로 `_endingPrice`는 등급 3 가격을 사용합니다.
- 가격 계산은 등급 1 토큰 ID를 초과한 토큰에 대해 `_endingPrice`(등급 3)를 사용합니다. 즉, 등급 2 가격을 사용했어야 하는 토큰을 포함한 모든 토큰에 등급 3 가격이 적용되어 잘못된 가격 계산이 발생합니다.

코드화된 PoC:

```solidity
  function test_mint_three_tiers() public {
        _warpPublicMint();

        uint256 _lastIdForTier1 = _getLastIdForTier();

        vm.deal(leet, 10000 ether);

        vm.prank(leet);
        hychainNodeKey.mint{ value: 1000 ether }(address(this), _lastIdForTier1 - 1);

        // second tier
        uint256 _lastIdForTier2 = hychainNodeKey.getLastIdForTier(_lastIdForTier1 + 1);

        uint256 _qty3 = _lastIdForTier2 + 1 - (hychainNodeKey.totalSupply() + 1) + 1;

        // price if we mint in 1 call (one call with jump from 1 -> 3)
        uint256 price_ = hychainNodeKey.getPriceForQuantity(hychainNodeKey.totalSupply() + 1, _qty3);

        console.log("total price in 1 call : ");
        console.log(price_);
        console.log("quantity : ");
        console.log(_qty3);

        // now, we compare with price with multiple calls (one with jump from 1 -> 2, one call with jump from 2 -> 3)

        uint256 stepQty1 = _lastIdForTier2 - (hychainNodeKey.totalSupply() + 1) + 1;
        uint256 stepQty2 = _qty3 - stepQty1;

        uint256 price_1 =  hychainNodeKey.getPriceForQuantity(hychainNodeKey.totalSupply() + 1, stepQty1);
        uint256 price_2 =  hychainNodeKey.getPriceForQuantity(hychainNodeKey.totalSupply() + 1 + stepQty1, stepQty2);

        uint256 totalPrice = price_1 + price_2;
        console.log("total price in 2 calls : ");
        console.log(totalPrice);
        console.log("step qty 1 : ");
        console.log(stepQty1);
        console.log("step qty 2 : ");
        console.log(stepQty2);
    }
```

로그 출력:

```shell
Logs:
  total price in 1 call :
  400156250000000000000
  quantity :
  3026

  total price in 2 calls :
  347992250000000000000
  step qty 1 :
  3025
  step qty 2 :
  1
```

## 권장 사항

여러 가격 등급 변경을 고려하도록 가격 책정을 수정하거나, 한 번 이상의 가격 등급 변경이 발생하지 않도록 수량을 제한하는 것을 고려하십시오.

# [M-01] L2로 토큰을 브리징할 때 잘못된 `excessFeeRefundAddress` 및 `callValueRefundAddress`가 제공됨 (Incorrect `excessFeeRefundAddress` and `callValueRefundAddress` are provided when bridging tokens to the L2)

## 심각도

**영향:** 중간 (Medium), `createRetryableTicket`을 호출할 때 `excessFeeRefundAddress` 및 `callValueRefundAddress` 매개변수로 `address(this)`가 제공되어 초과 수수료 손실 및 L1 -> L2 티켓 취소 기능 상실로 이어질 수 있습니다.

**가능성:** 중간 (Medium), `_mintAndBridge`가 트리거될 때 방지해야 할 잠재적으로 악의적인 동작이 있어 티켓을 취소하는 기능이 필요할 수 있습니다.

## 설명

HYCHAIN의 블록체인은 Arbitrum Nova(L2)를 구동하는 것과 동일한 기술을 사용합니다. 사용 가능하고 사용되는 기능 중 하나는 발행된 노드 키 토큰에 대한 L1 -> L2 브리징입니다. 문서에 따르면 `excessFeeRefundAddress`는 초과 수수료가 적립되는 L2 주소이고 `callValueRefundAddress`는 필요한 경우 브리징 티켓을 취소할 수 있는 기능을 가진 주소입니다.

```solidity
    function _mintAndBridge(address _to, uint256 _qty) internal {
        uint256 _startingTokenId = _nextTokenId();
        _mint(_to, _qty);
        HychainNodeKeyStorage.Layout storage $ = HychainNodeKeyStorage.layout();
        // require enough nativeToken to bridge
        if ($._topia != address(0)) {
            // TODO: figure out the exact amount
            require(IERC20($._topia).balanceOf(address(this)) >= $._transferCost, "Not enough $TOPIA to mint");
        }
        // approve inbox to transfer token
        IERC20($._topia).approve($._inbox, $._transferCost);
        // register ownership via retryable ticket
        uint256 ticketID = IERC20Inbox($._inbox).createRetryableTicket(
            $._l2NodeKeyAddress, // to
            0, // l2CallValue
            $._maxSubmissionCost, // maxSubmissionCost
>>>         address(this), // excessFeeRefundAddress
>>>         address(this), // callValueRefundAddress
            $._l2GasLimit, // gasLimit
            $._l2GasPrice, // maxGasPrice
            4e15, // tokenTotalFeeAmount
            abi.encodeWithSignature("mint(address,uint256,uint256)", msg.sender, _startingTokenId, _qty)
        );

        emit InboxTicketCreated(msg.sender, ticketID, _startingTokenId, _qty);
    }
```

그러나 이 두 매개변수에 대해 `address(this)`가 제공되는데, 초과 수수료가 0이 아니거나 예기치 않은 동작을 방지하기 위해 취소 작업이 필요한 경우 문제가 될 수 있습니다.

## 권장 사항

`excessFeeRefundAddress` 및 `callValueRefundAddress`에 대해 구성 가능한 L2 주소를 넣는 것을 고려하십시오.

# [M-02] L2에서 노드 키 토큰을 브리징하고 발행할 때 수신자로 잘못된 주소가 제공됨 (Incorrect address provided as the receiver when bridging and minting the Node Key token on the L2)

## 심각도

**영향:** 중간 (Medium), 사용자는 제공된 `_to` 주소 대신 L2에서 `msg.sender`가 토큰을 받을 것이라고 예상하지 못할 수 있습니다.

**가능성:** 중간 (Medium), 사용자가 노드 키 토큰을 발행하고 `_mintAndBridge`가 트리거되고 `msg.sender`가 제공된 `_to` 주소와 같지 않을 때 항상 발생합니다.

## 설명

사용자가 노드 키 토큰을 발행하면 결국 `_mintAndBridge`가 트리거되어 제공된 `_to` 주소로 토큰을 발행하고 발행 정보를 L2로 브리징합니다. 그러나 L2에 제공된 calldata 내부에서 사용자가 제공한 `_to` 매개변수 대신 `msg.sender`를 제공합니다.

```solidity
    function _mintAndBridge(address _to, uint256 _qty) internal {
        uint256 _startingTokenId = _nextTokenId();
        _mint(_to, _qty);
        HychainNodeKeyStorage.Layout storage $ = HychainNodeKeyStorage.layout();
        // require enough nativeToken to bridge
        if ($._topia != address(0)) {
            // TODO: figure out the exact amount
            require(IERC20($._topia).balanceOf(address(this)) >= $._transferCost, "Not enough $TOPIA to mint");
        }
        // approve inbox to transfer token
        IERC20($._topia).approve($._inbox, $._transferCost);
        // register ownership via retryable ticket
        uint256 ticketID = IERC20Inbox($._inbox).createRetryableTicket(
            $._l2NodeKeyAddress, // to
            0, // l2CallValue
            $._maxSubmissionCost, // maxSubmissionCost
            address(this), // excessFeeRefundAddress
            address(this), // callValueRefundAddress
            $._l2GasLimit, // gasLimit
            $._l2GasPrice, // maxGasPrice
            4e15, // tokenTotalFeeAmount
>>>         abi.encodeWithSignature("mint(address,uint256,uint256)", msg.sender, _startingTokenId, _qty)
        );

        emit InboxTicketCreated(msg.sender, ticketID, _startingTokenId, _qty);
    }
```

사용자는 제공된 `_to` 매개변수가 L2의 수신자가 될 것이라고 예상할 수 있습니다. `msg.sender`가 L2에서 사용할 수 없거나 발행된 토큰을 처리할 수 없는 계약인 경우 예기치 않은 동작이 발생할 수 있습니다.

## 권장 사항

L2에 전달된 calldata에 `msg.sender` 대신 `_to`를 제공하십시오.

# [L-01] 미리 계산된 수량 및 가격으로 인해 특정 형태의 구매가 불허됨 (Precomputed quantity and price disallow certain forms of buying)

NFT를 구매하려면 사용자는 수량을 지정하고 호출과 함께 예상 비용을 전달해야 합니다. 이는 사용자가 구매할 때 고려할 수 있는 특정 시나리오를 불허합니다.

1. 판매의 마지막 단계에서 가능한 한 많은 NFT를 얻으려고 시도하면 다른 사용자의 구매로 인해 계산된 수량이 상한을 초과하여 되돌려질(revert) 수 있습니다.
2. 가능한 가격 변경으로 인해 사용자는 구매력을 극대화할 수 없습니다. 사용자에게 100 Eth가 있는 경우 사용자는 100 Eth에 대해 가능한 최대 금액의 NFT 구매를 지정할 옵션이 없습니다. 사용자는 100 Eth에 대한 현재 가격으로 계산된 수량을 전달해야 하며, 이 경우 다른 사용자의 구매로 인한 가격 인상으로 인해 tx가 되돌려질 수 있습니다. 반면 사용자가 더 적은 수의 NFT를 전달하여 이를 해결하려고 시도하고 가격이 변경되지 않으면 사용자는 남은 금액을 놓치게 됩니다.

가능한 최대 양의 NFT를 구매하려는 의도를 지정하는 기능을 추가하면 이 문제를 해결할 수 있습니다.

# [L-02] 호출 대신 전송을 사용하면 일부 계약과의 상호 작용이 불허될 수 있음 (Using transfer instead of call may disallow interaction with some contracts)

초과 지불된 금액을 반환하기 위해 `mint` 함수는 `call` 메서드 대신 `transfer` 메서드를 사용합니다.

```solidity
    function mint(address _to, uint256 _qty) public payable {
        if (_to == address(0)) {
            revert InvalidRecipient();
        }
        if (getPointsCostPerNodeKey() != 0) {
            revert NotPublicMintPhase();
        }

        uint256 _price = getPriceForQuantity(_nextTokenId(), _qty);

        if (msg.value < _price) {
            revert InsufficientPaymentAmount(msg.value, _price);
        } else if (msg.value > _price) {
=>          payable(msg.sender).transfer(msg.value - _price);
        }
```

`transfer` 메서드는 2300 가스만 전달하므로 상호 작용하는 계약이 수신/폴백(fallback) 함수에서 더 많은 가스를 사용하는 경우 트랜잭션이 되돌려질 수 있습니다.

따라서 사전에 이를 알리거나 이 문제를 완화하기 위해 재진입 가드와 함께 call 메서드를 사용하십시오.

# [L-03] `_refBps` 값 검증 부족 (lack of `_refBps` value validation)

사용자가 `mintWithReferral` 또는 `mintWhitelist`를 통해 발행하고 유효한 추천 데이터를 제공하면 `(_price * _refBps) / 10000`으로 계산된 금액으로 기본 ETH를 `_refRecipient`로 전송합니다. 그러나 호출 내부에서 사용할 수 있는 `_refBps`에 대한 적절한 검증이 없습니다. 예기치 않은 동작을 방지하기 위해 제공된 `_refBps`를 확인하고 값이 구성된 상한보다 클 때 되돌리는 것을 고려하십시오.

# [L-04] 발행 함수 내부의 재진입 지점은 향후 문제가 될 수 있음 (Reentrancy point inside mint functions could become an issue in the future)

`HychainNodeKey` 내부의 mint 함수에는 `_mintAndBridge`가 트리거되기 전에 초과 기본 및 추천 지불금을 전송하기 때문에 잠재적인 재진입 지점이 있습니다. 향후 `.transfer` 대신 `.call`이 사용되는 경우 악의적인 사용자가 이 재진입 지점을 악용하여 실제 지불해야 하는 가격보다 낮은 가격 등급으로 노드 키 토큰을 구매하거나 최대 발행 한도(50000)를 초과할 수 있는 토큰을 발행할 수 있습니다. 이는 `.transfer`를 사용할 때 가스 제한으로 방지되지만 재진입 잠금 기능도 구현하는 것이 좋습니다.

# [L-05] `_refRecipient`는 사용자가 유효한 추천 데이터로부터 혜택을 받는 것을 방해할 수 있음 (`_refRecipient` can prevent users from getting benefit from valid referral data)

사용자가 노드 키를 발행하고 유효한 추천 데이터를 제공하면 결국 동일한 작업 내에서 추천 지불금을 `_refRecipient`로 전송합니다. `_refRecipient`가 의도적으로 전송 작업을 방지하는 계약인 경우 문제가 될 수 있으며, 사용자는 가격 할인을 위한 유효한 추천 데이터의 혜택을 받을 수 없습니다. push-over-pull 설계를 구현하고, `_refRecipient`의 추천 지불금을 추적하고, 지불금을 청구하기 위한 별도의 함수를 갖는 것을 고려하십시오.

# [L-06] 추천 데이터를 제공하지 않을 때 `mintWhitelist`는 항상 되돌려짐 (`mintWhitelist` will always revert when not providing referral data)

추천 데이터는 `mintWhitelist` 내부의 선택적 매개변수입니다. 제공된 `_refCode`가 `0x0`이면 추천 코드가 사용되지 않음을 나타냅니다. 그러나 함수 내부에서는 `refCode` 값에 관계없이 `_validateAndUseReferralSignature`를 호출하여 제공된 서명을 확인합니다. 이로 인해 추천 데이터가 선택 사항임에도 불구하고 추천 데이터가 제공되지 않으면 호출이 항상 되돌려집니다. `refCode`가 `0x0`이 아닌 경우에만 추천 서명을 확인하는 것을 고려하십시오.

# [L-07] 악의적인 사용자는 의도적으로 발행 작업을 분할하여 브리징을 위해 엄청난 양의 Topia 토큰을 소비할 수 있음 (Malicious user can intentionally split their mint operations to consume huge amounts of Topia tokens for bridging)

화이트리스트 단계가 끝나면 사용자는 0이 아닌 양의 노드 키 토큰을 `mint`할 수 있으며, 결국 모든 `mint` 작업에 대해 L2로 브리징됩니다. 이를 통해 악의적인 사용자는 `mint` 요청을 가능한 가장 작은 수량으로 분할할 수 있으며, 브리징 토큰 수수료는 `mint` 요청 기준이므로 엄청난 양의 Topia 토큰이 사용됩니다. 주소당 발행할 수 있는 노드 키 토큰의 최대 양을 추가하거나 `mint` 작업 간에 각 주소에 대한 시간 지연 제한을 구현하는 것을 고려하십시오.
