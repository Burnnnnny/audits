# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적인 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 절대 보장할 수 없지만, 경험 많은 연구원들의 블록체인 프로토콜에 대한 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Project-Aegis-xyz/tokenFactory** 리포지토리에 대해 **Pashov Audit Group**이 시간 제한 보안 검토를 수행했으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Aburra 소개

TokenFactory 스마트 계약을 통해 사용자는 본딩 곡선(xy=k 가상 ETH 유동성 사용)에서 거래되는 코인을 생성할 수 있으며, 특정 기간 내에 가격이 특정 임계값에 도달하면 토큰을 Uniswap v3 풀로 마이그레이션할 수 있는 옵션이 있습니다. 그렇지 않으면 유동성은 Purple DAO의 재무고에 기부됩니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향(Impact)

- 높음(High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간(Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음(Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성(Likelihood)

- 높음(High) - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 도난당하거나 분실될 수 있는 자금의 양에 비해 공격 비용이 비교적 낮습니다.

- 중간(Medium) - 조건부 인센티브가 있는 공격 벡터일 뿐이지만 여전히 발생할 가능성이 비교적 높습니다.

- 낮음(Low) - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적(Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음(High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간(Medium) - 수정해야 함

- 낮음(Low) - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [c16e8f6355d80415de4ae75a86e6a88e33fe4038](https://github.com/Project-Aegis-xyz/tokenFactory/tree/c16e8f6355d80415de4ae75a86e6a88e33fe4038)

_수정 검토 커밋 해시_ - [b9d932c33a58adafcd2edb7b89d362d60dcdbf04](https://github.com/Project-Aegis-xyz/tokenFactory/tree/b9d932c33a58adafcd2edb7b89d362d60dcdbf04)

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `FixedSupplyERC20`
- `TokenFactory`
- `IWETH9`

# 결과

# [C-01] 악의적인 토큰 생성자가 토큰 `buy/sells`를 차단할 수 있음

## 심각도

**영향:** 높음(High)

**가능성:** 높음(High)

## 설명

토큰이 생성될 때 생성자는 `creatorFeeRecipient`를 지정할 수 있으며, 이는 해당 수수료를 보내는 데 사용됩니다. 그런 다음 `TokenFactory` 계약에서 `buyWithReferral`, `buyWithReferralWithExactEth` 및 `sellWithReferral` 함수는 `_sendFees` 함수를 사용하여 수수료 전송을 처리합니다:

```solidity
File: TokenFactory.sol
490:     function sellWithReferral(
491:         address erc20Address,
492:         uint256 tokenAmountToSell,
493:         uint256 minEthReceivedAfterFees,
494:         address referral
495:     ) external nonReentrant validToken(erc20Address) {
...
536:         // send fees
537:         _sendFees(erc20Address, protocolFee, creatorFee, referralFee, referral);
...
```

`_sendFees` 함수에는 `TokenFactory#L806-809`에 위치한 `revert` 문이 포함되어 있어 토큰 생성자가 트랜잭션을 의도적으로 실패하게 만들 수 있습니다.

```solidity
File: TokenFactory.sol
806: (bool creatorFeeSuccess,) = creatorFeeRecipients[erc20Address].call{value: creatorFee, gas: 100_000}("");
807: if (!creatorFeeSuccess) {
808:     revert TokenFactory_CreatorFeeTransferFailed();
809: }
```

이 문제는 토큰 생성자가 트랜잭션을 전략적으로 실패하게 만들어 누가 토큰을 사고 팔 수 있는지 제어할 수 있게 하여, 토큰 판매를 방지하고 자신이 생성한 토큰을 펌핑하여 `Uniswap pool`을 생성할 수 있도록 합니다. 다음 테스트는 악의적인 생성자가 `buyWithReferral` 호출을 되돌릴 수 있음을 보여줍니다:

```solidity
    // File: TokenFactoryForkTest_BuyAndSell.t.sol
    function test_buyMaliciousCreatorDOS() public {
        string memory name = "TestToken";
        string memory symbol = "TT";
        uint256 initialBuyerBalance = 10 ether;
        // Malicious creator contract
        MaliciousCreator customCreatorFeeRecipient = new MaliciousCreator();
        //
        // 1. Token creation using the `malicious creator` contract
        vm.startPrank(buyer);
        address newTokenAddress = factory.initializeToken(name, symbol, address(customCreatorFeeRecipient));
        vm.deal(buyer, initialBuyerBalance);
        //
        // 2. Malicious creator can block purchases
        vm.expectRevert(bytes4(keccak256("TokenFactory_CreatorFeeTransferFailed()")));
        factory.buyWithReferral{value: 10 ether}(newTokenAddress, 1e7 ether, address(0));
        vm.stopPrank();
    }
```

`MaliciousCreator` 계약:

```solidity
contract MaliciousCreator {
    receive() external payable {
        // custom revert
        revert();
    }
}
```

## 권장 사항

**옵션 1:**

`TokenFactory::_sendFees` 함수를 `creatorFee` 전송이 실패할 때 되돌리지(revert) 않도록 수정해야 합니다:

```solidity
    (bool creatorFeeSuccess,) = creatorFeeRecipients[erc20Address].call{value: creatorFee, gas: 100_000}("");
    if (!creatorFeeSuccess) {
        emit CreatorFeeTransferFailed(erc20Address, creatorFee);
    }
```

**옵션 2:**

각 생성자와 tokenAddress에 대한 매핑을 갖도록 하여 이러한 공격이 발생할 경우 프로토콜이 악의적인 생성자를 식별하고 다른 생성자에게 영향을 주지 않고 그의 creatorFee를 0으로 설정할 수 있도록 고려하십시오.

이미 creatorFeeRecipients[ERC20Token]에서 생성자로의 매핑이 있으므로, 매핑을 생성자와 생성자 수수료가 있는 구조체를 가리키도록 변경하는 것을 권장합니다.

# [M-01] 사용자가 수수료 지불을 회피할 수 있음

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

`TokenFactory`는 토큰 구매 및 판매 모두에 대해 수수료를 부과합니다.

```solidity
        // initialize fees
        protocolFeeBips = 150;
        creatorFeeBips = 250;

        protocolFeeWithReferralBips = 100;
        creatorFeeWithReferralBips = 250;
        referralFeeBips = 50;
```

여기서 문제는 사용자가 공개 시장(예: Dex, Uniswap 쌍 등)에서 토큰을 거래함으로써 두 수수료를 모두 지불하는 것을 회피할 수 있다는 것입니다.

## 권장 사항

`curve.isPaused`가 `false`일 때 모든 전송을 일시 중지하는 것을 고려하십시오.

# [M-02] ETH 기부로 인한 토큰 가치 하락

## 심각도

**영향:** 높음(High)

**가능성:** 낮음(Low)

## 설명

`TokenFactory.pauseAndWithdrawToPurple` 함수는 특정 기간 내에 가격 목표에 도달하지 못한 경우 토큰을 일시 중지하고 ETH를 Purple DAO에 기부합니다. 따라서 모든 토큰 보유자는 토큰을 다시 판매할 가능성을 잃습니다. 이는 토큰 가격을 0으로 낮추고 모든 투자를 잃는 것을 의미합니다.

```solidity
    /// @notice Pauses a token and donates its ETH to Purple DAO. This happens when a token is dead (e.g. not reaching price targe within certain period of time)
    /// @param erc20Address The address of a valid bonding curve token.
    function pauseAndWithdrawToPurple(address erc20Address) external onlyOwner nonReentrant validToken(erc20Address) {
        BondingCurveData storage curve = curves[erc20Address];
>>      curve.isPaused = true;
        uint256 ethBalance = curve.ethBalance;
        if (ethBalance > 0) {
>>          curve.ethBalance = 0;
            (bool ethSent,) = purpleAddress.call{value: ethBalance, gas: 100_000}("");
            if (!ethSent) revert TokenFactory_EthTransferFailed();
            emit EthWithdrawnToPurple(erc20Address, ethBalance);
        }
    }
```

## 권장 사항

이 함수는 `UNISWAP_V3_POSITION_MANAGER`에 의해 ETH가 환불된 경우 토큰 곡선에서 초과 ETH를 구출하는 데만 사용하고, 특정 시간이 지났을 때 곡선을 일시 중지하는 별도의 함수를 구현하는 것을 고려하십시오.

# [M-03] buyWithReferral에 슬리피지 보호 없음

## 심각도

**영향:** 높음(High)

**가능성:** 낮음(Low)

## 설명

사용자가 `buyWithReferral` 함수를 사용하여 토큰을 구매할 때 구매할 토큰 금액을 지정하고 해당 금액의 ETH를 보냅니다. 사용자가 지불할 최대 가격이 `tokenAmountToBuy/msg.value`라고 가정하여 사용자가 지불하는 가격을 제어할 수 있다고 가정하므로 슬리피지 보호가 없습니다.

그러나 사용자는 다음과 같은 이유로 예상보다 높은 가격(슬리피지)을 지불하게 될 수 있습니다:

- 남은 토큰 공급량이 `tokenAmountToBuy`보다 적은 경우 구매 가능한 토큰 수가 `tokenAmountToBuy`보다 적을 수 있습니다.
- 사용 가능한 토큰 공급량이 감소함에 따라 토큰 가격이 상승합니다.

사용자가 1 ETH로 10e18 토큰을 구매하려고 하지만(가격은 1e18 토큰당 0.1 ETH), 다른 사용자가 프론트러닝 하여 9e18 토큰을 먼저 구매하는 시나리오에서 초기 사용자는 1e18 토큰만 받고 0.1 ETH 이상을 지불하게 되며, 이는 의도한 가격인 1e18 토큰당 0.1 ETH보다 높습니다.

```solidity
    function buyWithReferral(address erc20Address, uint256 tokenAmountToBuy, address referral)
        public
        payable
        nonReentrant
        validToken(erc20Address)
    {
        ...
        uint256 tokenAmountLeft = getTokenAmountLeft(erc20Address);
        if (tokenAmountToBuy >= tokenAmountLeft) { // @audit amount to buy can be less than specified
            tokenAmountToBuy = tokenAmountLeft;
            // pause the curve when all available tokens have been bought
            curves[erc20Address].isPaused = true;
            emit BondingCurveCompleted(erc20Address);
        }
        ...
    }
```

## 권장 사항

슬리피지를 방지하기 위해 사용자가 받을 최소 토큰 금액을 지정할 수 있도록 허용하십시오.

# [M-04] LP 수수료 손실 가능성

## 심각도

**영향:** 높음(High)

**가능성:** 낮음(Low)

## 설명

토큰 판매가 완료되면 해당 `curvePool`이 `paused`로 설정됩니다. 그 후 소유자는 `TokenFactory::moveToAMM`을 호출하여 Uniswap에 풀을 생성하고 유동성을 추가할 수 있습니다(TokenFactory#L888-905). 이 작업은 Uniswap 포지션 NFT를 발행하고 나중에 수수료 징수를 위해 ID를 저장합니다(TokenFactory#L858). 그러나 Uniswap은 전체 금액이 유동성에 사용되지 않으면 ETH를 반환할 수 있으며, 이는 곡선의 ETH 잔액(`curve.ethBalance`)을 잘못 0이 아닌 상태로 남길 수 있습니다(TokenFactory#L917-931):

```solidity
File: TokenFactory.sol
861:     function _createPoolAndAddLiquidity(
862:         address erc20Address,
863:         uint256 ethBalance,
864:         uint256 tokenBalance,
865:         uint16 slippageBips
866:     ) internal returns (uint256 tokenId) {
...
...
916:         // Remove ERC20 allowance and update ETH balance (if refunded)
917:         if (amount0Added < amount0) {
918:             if (token0 == WETH9) {
919:                 curve.ethBalance = amount0 - amount0Added;
920:             } else {
921:                 TransferHelper.safeApprove(token0, address(UNISWAP_V3_POSITION_MANAGER), 0);
922:             }
923:         }
924:
925:         if (amount1Added < amount1) {
926:             if (token1 == WETH9) {
927:                 curve.ethBalance = amount1 - amount1Added;
928:             } else {
929:                 TransferHelper.safeApprove(token1, address(UNISWAP_V3_POSITION_MANAGER), 0);
930:             }
931:         }
932:     }
```

이 0이 아닌 잔액은 `TokenFactory::moveToAMM`이 되돌리지 않고 진행하기에 충분한 유동성이 있는지 확인하기 때문에 다시 호출될 수 있게 잘못 허용할 수 있습니다(TokenFactory#L854-856):

```solidity
File: TokenFactory.sol
840:     function moveToAMM(address erc20Address, uint16 slippageBips)
841:         external
842:         onlyOwner
843:         nonReentrant
844:         validToken(erc20Address)
845:     {
846:         if (!curves[erc20Address].isPaused) {
847:             revert TokenFactory_BondingCurveNotPaused();
848:         }
849:         BondingCurveData storage curve = curves[erc20Address];
850:
851:         uint256 ethBalance = curve.ethBalance;
852:         uint256 tokenBalance = IERC20(erc20Address).balanceOf(address(this));
853:
854:         if (ethBalance == 0 || tokenBalance == 0) {
855:             revert TokenFactory_InsufficientLiquidity();
856:         }
857:
858:         tokenIds[erc20Address] = _createPoolAndAddLiquidity(erc20Address, ethBalance, tokenBalance, slippageBips);
859:     }
```

다음 시나리오를 고려하십시오:

1. 토큰 풀이 완료되고 소유자가 처음에 `moveToAMM`을 호출합니다.
2. Uniswap 풀이 생성되고 포지션이 발행되지만 일부 ETH가 환불되어 `curve.ethBalance`가 0이 아닌 상태가 됩니다(TokenFactory#L919).
3. 소유자가 아마도 자동 스크립트나 오프체인 프로세스의 오해로 인해 `moveToAMM`을 다시 실수로 호출합니다. 공격자는 이를 악용하여 유동성 확인 `(TokenFactory#L854-856)`을 충족하기 위해 토큰을 보내어 다른 포지션 생성을 허용할 수 있습니다. 이 새로운 포지션 ID는 이전 포지션 ID를 덮어쓰고(TokenFactory#L858), 1단계에서 생성된 포지션에서 LP 수수료를 청구할 수 있는 기능을 잃게 됩니다.

궁극적으로 계약은 `3단계`에서 생성된 포지션에서만 `LP 수수료`를 청구할 수 있습니다. 이 포지션만 `tokenIds[erc20Address]`에 저장되기 때문입니다. 2단계에서 생성된 포지션은 더 이상 `LP 수수료`를 청구할 수 없습니다.

## 권장 사항

이 문제를 완화하려면 ETH 환불이 다른 `moveToAMM` 호출에 재사용되지 않도록 처리하는 것이 좋습니다.

# [M-05] 의도하지 않은 생성자 수수료를 0 주소로 전송

## 심각도

**영향:** 중간(Medium)

**가능성:** 중간(Medium)

## 설명

`tokenPool` 생성 중에 사용자는 `creatorFeeRecipient`를 지정할 수 있습니다(TokenFactory#L266):

```solidity
File: TokenFactory.sol
262:      */
263:     function initializeTokenAndBuy(
264:         string calldata name,
265:         string calldata symbol,
266:         address creatorFeeRecipient,
267:         uint256 initialBuyAmount
268:     ) external payable returns (address newTokenAddress) {
269:         newTokenAddress = initializeToken(name, symbol, creatorFeeRecipient);
...
...
```

문제는 토큰 생성자가 `creatorFeeRecipient`를 `address(0)`으로 지정하여 누적된 생성자 수수료가 `address(0)`으로 전송되어 사실상 해당 수수료가 소각될 때 발생합니다.

```solidity
File: TokenFactory.sol
794:     function _sendFees(
795:         address erc20Address,
796:         uint256 protocolFee,
797:         uint256 creatorFee,
798:         uint256 referralFee,
799:         address referralAddr
800:     ) internal {
...
806:         (bool creatorFeeSuccess,) = creatorFeeRecipients[erc20Address].call{value: creatorFee, gas: 100_000}("");
807:         if (!creatorFeeSuccess) {
808:             revert TokenFactory_CreatorFeeTransferFailed();
809:         }
...
818:     }
```

수수료를 받지 않기로 결정한 사용자(`creatorFeeRecipient=address(0)`)가 있을 수 있지만 시스템은 여전히 해당 수수료를 계산하여 `address(0)`으로 보내므로 문제가 될 수 있습니다. 다음 테스트는 사용자가 `address(0)`을 `creatorFeeRecipient`로 사용하여 토큰을 초기화한 다음 토큰 구매가 이루어지고 생성자를 위한 수수료가 `address(0)`으로 전송되는 방법을 보여줍니다:

```solidity
    function test_creatorAddressZero() public {
        string memory name = "TestToken";
        string memory symbol = "TT";
        uint256 initialBuyerBalance = 10 ether;
        //
        // 1. Token creation using the `malicious creator` contract
        vm.startPrank(buyer);
        address newTokenAddress = factory.initializeToken(name, symbol, address(0));
        vm.deal(buyer, initialBuyerBalance);
        //
        // 2. Buyer buys and the creator fees are sent to zero address
        factory.buyWithReferral{value: 10 ether}(newTokenAddress, 1e7 ether, address(0));
    }
```

## 권장 사항

토큰이 `creatorFeeRecipient=address(0)`으로 생성될 수 없음을 검증하는 것이 좋습니다. 또는 사용자가 토큰 생성에서 수수료를 받지 않기로 결정한 경우 이러한 `creatorFees`를 프로토콜로 보내야 하는지 평가할 수 있습니다.

# [M-06] 토큰 전송 시 경쟁 조건으로 인한 잠재적 자산 오용

## 심각도

**영향:** 높음(High)

**가능성:** 낮음(Low)

## 설명

토큰이 모든 토큰을 판매하면 토큰이 일시 중지되고 "완료됨"으로 표시됩니다(TokenFactory#L388-393). 이를 통해 `TokenFactory::moveToAMM` 함수를 사용하여 자산을 `Uniswap Pool`로 이동할 수 있습니다:

```solidity
File: TokenFactory.sol
371:     function buyWithReferral(address erc20Address, uint256 tokenAmountToBuy, address referral)
372:         public
373:         payable
374:         nonReentrant
375:         validToken(erc20Address)
376:     {
...
...
387:         uint256 tokenAmountLeft = getTokenAmountLeft(erc20Address);
388:         if (tokenAmountToBuy >= tokenAmountLeft) {
389:             tokenAmountToBuy = tokenAmountLeft;
390:             // pause the curve when all available tokens have been bought
391:             curves[erc20Address].isPaused = true;
392:             emit BondingCurveCompleted(erc20Address);
393:         }
```

반면에 `TokenFactory::pauseAndWithdrawToPurple` 함수는 토큰 풀을 일시 중지하고 ETH를 PurpleDAO에 기부하는 데 도움이 됩니다.

```solidity
File: TokenFactory.sol
936:     function pauseAndWithdrawToPurple(address erc20Address) external onlyOwner nonReentrant validToken(erc20Address) {
937:         BondingCurveData storage curve = curves[erc20Address];
938:         curve.isPaused = true;
939:         uint256 ethBalance = curve.ethBalance;
940:         if (ethBalance > 0) {
941:             curve.ethBalance = 0;
942:             (bool ethSent,) = purpleAddress.call{value: ethBalance, gas: 100_000}("");
943:             if (!ethSent) revert TokenFactory_EthTransferFailed();
944:             emit EthWithdrawnToPurple(erc20Address, ethBalance);
945:         }
946:     }
```

문제는 판매 가능한 토큰이 아직 있고 누군가 토큰 풀을 완료하는 동안 `pauseAndWithdrawToPurple` 함수가 호출될 때 발생합니다. 다음 이벤트 시퀀스는 자산 오용을 유발할 수 있습니다:

1. 토큰에 판매할 토큰이 아직 있습니다.
2. 소유자가 `pauseAndWithdrawToPurple` 함수를 호출하지만 트랜잭션이 멤풀에 머무릅니다.
3. 사용자가 나머지 모든 토큰을 구매하여 토큰을 "완료됨" 및 일시 중지됨 상태로 남겨 Uniswap 풀로 이동하기를 기다립니다.
4. `2단계`의 트랜잭션이 실행되어 모든 토큰을 `PurpleDAO`로 보냅니다.

Uniswap 풀이 생성되고 자산이 PurpleDAO가 아닌 Uniswap 풀로 전송되어야 하므로 이 이벤트 순서는 올바르지 않습니다.

다음 테스트는 `일시 중지된` 토큰 풀이 `pauseAndWithdrawToPurple` 함수를 사용하여 자산을 PurpleDAO로 보내는 방법을 보여줍니다:

```solidity
    function test_WithdrawToPurpleWhenCurveIsCompleted() public {
        //
        // 1. Create token and complete the bonding curve
        address tokenAddress = _initializeToken(buyer, 1e9 ether);
        //
        // 2. The Bonding curve is paused (completed)
        (uint256 initialBondingCurveEthBalance, bool isPaused) = factory.curves(tokenAddress);
        assertEq(isPaused, true);
        //
        // 3. Owner calls `pauseAndWithdrawToPurple`
        vm.startPrank(contractOwner);
        uint256 initialPurpleBalance = factory.purpleAddress().balance;
        factory.pauseAndWithdrawToPurple(tokenAddress);
        vm.stopPrank();
        //
        // 4. Balances are transferred to `purple`
        (uint256 finalBondingCurveEthBalance,) = factory.curves(tokenAddress);
        uint256 finalContractBalance = address(factory).balance;
        uint256 finalPurpleBalance = factory.purpleAddress().balance;
        // check balance and eth withdraw
        assertEq(finalContractBalance, 0);
        assertEq(finalBondingCurveEthBalance, 0);
        assertEq(finalPurpleBalance, initialPurpleBalance + initialBondingCurveEthBalance);
    }
```

또한 또 다른 잠재적 공격 벡터는 `TokenFactory#L902`의 `deadline=15 minutes` 매개변수로 인해 Uniswap 풀 생성이 불가능할 수 있다는 것입니다. 이로 인해 오프체인 자동 스크립트 실행에 의해 `pauseAndWithdrawToPurple()` 함수가 호출되기 쉬워지며, 이는 리소스를 `PurpleDAO`로 전환할 수 있습니다. 해당 리소스는 Uniswap 풀로 전송되어야 하므로 이는 올바르지 않습니다.

## 권장 사항

이 문제를 완화하려면 `pauseAndWithdrawToPurple` 함수에 토큰이 Uniswap 풀로 이동하기를 기다리는 상태가 아닌지 확인하는 검증 단계를 도입하는 것이 좋습니다:

```diff
    function pauseAndWithdrawToPurple(address erc20Address) external onlyOwner nonReentrant validToken(erc20Address) {
        BondingCurveData storage curve = curves[erc20Address];
++      if (curve.isPaused) revert TokenFactory_BondingCurvePaused();
        curve.isPaused = true;
        uint256 ethBalance = curve.ethBalance;
        if (ethBalance > 0) {
            curve.ethBalance = 0;
            (bool ethSent,) = purpleAddress.call{value: ethBalance, gas: 100_000}("");
            if (!ethSent) revert TokenFactory_EthTransferFailed();
            emit EthWithdrawnToPurple(erc20Address, ethBalance);
        }
    }
```

# [M-07] tradeTokenMinSupply는 0이 아니어야 함

## 심각도

**영향:** 높음(High)

**가능성:** 낮음(Low)

## 설명

새 토큰을 생성할 때 생성자는 초기 공급량과 최소 공급량을 설정해야 합니다. 최소 공급량은 0이 될 수 없으며, 그렇지 않으면 AMM 계산이 작동하지 않고(0으로 나누기) 토큰 구매에 사용된 모든 ETH가 `curves[erc20Address]`를 일시 중지할 수 없어 Uniswap 포지션으로 전송될 수 없으므로 계약에 ETH가 갇히게 됩니다.

```
        uint256 newX = x - tokenAmount;
        @audit - the newX will be zero, which will not work
>       uint256 newYScaled = (k * _PRECISION_MULTIPLIER) / newX;
```

## 권장 사항

`initializeCustomToken()`을 호출할 때 `tradeTokenMinSupply`가 0이 아닌지 확인하십시오.

```
    function initializeCustomToken(
        if (tradeTokenInitSupply <= tradeTokenMinSupply) {
            revert TokenFactory_InvalidParams();
        }
       require(tradeTokenMinSupply != 0, "Minimum supply cannot be zero");
```

# [L-01] `ethVirtualSupply` 매개변수 확인 부족

`TokenFactory.initializeCustomToken` 함수는 `ethVirtualSupply` 매개변수가 0인지 또는 너무 작은지 확인하지 않습니다. 이로 인해 가격 계산에서 예상치 못한 결과가 발생하고 토큰이 인플레이션 공격에 취약해질 수 있습니다.

```solidity
    function initializeCustomToken(
        string calldata name,
        string calldata symbol,
        address creatorFeeRecipient,
        uint256 tradeTokenInitSupply,
        uint256 tradeTokenMinSupply,
>>      uint256 ethVirtualSupply
    ) public nonReentrant returns (address newTokenAddress) {
        if (tradeTokenInitSupply <= tradeTokenMinSupply) {
            revert TokenFactory_InvalidParams();
        }

        IERC20 newToken = new FixedSupplyERC20(name, symbol, tradeTokenInitSupply);
        newTokenAddress = address(newToken);

        params[newTokenAddress] = BondingCurveParams({
            tradeTokenInitSupply: tradeTokenInitSupply,
            tradeTokenMinSupply: tradeTokenMinSupply,
>>          ethVirtualSupply: ethVirtualSupply
        });
```

`ethVirtualSupply` 값이 합리적인 범위(예: 0보다 큼)인지 확인하는 것을 고려하십시오.

# [L-02] 하드코딩된 추천 수수료

`protocolFeeWithReferralBips` 및 `creatorFeeWithReferralBips` 수수료는 변경할 수 있지만 `referralFeeBips`는 하드코딩되어 있습니다.

```solidity
    function initialize(
        address initialOwner,
        INonfungiblePositionManager _uniswapV3PositionManager,
        IUniswapV3Factory _uniswapV3Factory,
        address _protocolFeeRecipient,
        address _weth9,
        address _purple
    ) public initializer {
<...>
        protocolFeeWithReferralBips = 100;
        creatorFeeWithReferralBips = 250;
>>      referralFeeBips = 50;
```

`setProtocolFeeBips` 함수에서 `protocolFeeBips`와 `protocolFeeWithReferralBips`의 차이를 새로운 `referralFeeBips` 값으로 설정하는 것을 고려하십시오.

# [L-03] Ownable 대신 Ownable2Step 사용

`Ownable2Step` 및 `Ownable2StepUpgradeable`은 소유자 권한 수령인이 자신의 계약 호출을 통해 적극적으로 수락하도록 요구함으로써 계약 소유권이 실수로 처리할 수 없는 주소(예: 주소의 오타로 인해)로 전송되는 것을 방지합니다.

```solidity
contract TokenFactory is
    Initializable,
    UUPSUpgradeable,
    IERC721Receiver,
    ERC721HolderUpgradeable,
    ReentrancyGuardUpgradeable,
>>  OwnableUpgradeable
```

# [L-04] Purple DAO 주소가 설정되지 않은 경우에도 ETH를 전송할 수 있음

Purple DAO 재무고 주소는 `TokenFactory.initialize` 함수로 설정해야 하지만 주소를 `0`으로 남겨두고 나중에 `setPurpleAddress` 함수로 설정할 수 있습니다. 문제는 `pauseAndWithdrawToPurple` 함수가 Purple DAO 주소가 0이 아닌지 확인하지 않아 ETH가 손실될 수 있다는 것입니다.

```solidity
    function initialize(
        address initialOwner,
        INonfungiblePositionManager _uniswapV3PositionManager,
        IUniswapV3Factory _uniswapV3Factory,
        address _protocolFeeRecipient,
        address _weth9,
        address _purple
    ) public initializer {
        __Ownable_init(initialOwner);
        __UUPSUpgradeable_init();
        __ReentrancyGuard_init();
        __ERC721Holder_init();

        UNISWAP_V3_POSITION_MANAGER = _uniswapV3PositionManager;
        UNISWAP_V3_FACTORY = _uniswapV3Factory;
        protocolFeeRecipient = _protocolFeeRecipient;
        WETH9 = _weth9;
>>      purpleAddress = _purple;

<...>
    function pauseAndWithdrawToPurple(address erc20Address) external onlyOwner nonReentrant validToken(erc20Address) {
        BondingCurveData storage curve = curves[erc20Address];
        curve.isPaused = true;
        uint256 ethBalance = curve.ethBalance;
        if (ethBalance > 0) {
            curve.ethBalance = 0;
>>          (bool ethSent,) = purpleAddress.call{value: ethBalance, gas: 100_000}("");
            if (!ethSent) revert TokenFactory_EthTransferFailed();
            emit EthWithdrawnToPurple(erc20Address, ethBalance);
        }
    }
<...>
    function setPurpleAddress(address _purpleAddress) external onlyOwner {
        emit PurpleAddressUpdated(purpleAddress, _purpleAddress);
>>      purpleAddress = _purpleAddress;
    }
```

`pauseAndWithdrawToPurple` 함수에서 Purple DAO 주소가 0이 아닌지 확인하는 것을 고려하십시오.

# [L-05] 마감일이 장기 보류 트랜잭션을 보호하지 않음

Uniswap v3에서 `deadline` 매개변수는 트랜잭션이 실패할 유닉스 시간을 나타내며, 장기 보류 트랜잭션과 가격의 급격한 변동으로부터 보호하기 위한 것입니다. 그러나 `UNISWAP_V3_POSITION_MANAGER.mint`를 호출할 때 설정된 마감일은 `block.timestamp + 15 minutes`입니다. 즉, 블록 제안자가 트랜잭션을 블록에 포함하기로 결정할 때마다 `block.timestamp`가 현재 타임스탬프가 되므로 유효하게 되어 `deadline`이 효과가 없습니다.

```solidity
        UNISWAP_V3_POSITION_MANAGER.mint(
            MintParams({
                token0: token0,
                token1: token1,
                fee: poolFee,
                tickLower: tickLower,
                tickUpper: tickUpper,
                amount0Desired: amount0Desired,
                amount1Desired: amount1Desired,
                amount0Min: amount0Min,
                amount1Min: amount1Min,
                recipient: address(this),
                deadline: block.timestamp + 15 minutes // @audit deadline currently set to block timestamp
            })
        );
```

`deadline`을 함수에 매개변수로 전달하는 것이 좋습니다.

# [L-06] moveToAMM이 되돌아가거나 자금 손실로 이어질 수 있음

Uniswap V3 풀로 자금을 이동할 때 계약은 `sqrtPriceX96`을 계산하여 모든 ETH 잔액과 토큰 잔액을 사용하여 풀에 유동성을 제공할 수 있도록 합니다.

```solidity
    function _createPoolAndAddLiquidity(
        address erc20Address,
        uint256 ethBalance,
        uint256 tokenBalance,
        uint16 slippageBips
    ) internal returns (uint256 tokenId) {
        ...
        uint160 sqrtPriceX96 = uint160((Math.sqrt((amount1 * 1e18) / amount0) * _X96) / 1e9);

        // clear eth balance
        curve.ethBalance = 0;

        // Note that The ERC20 balance of this contract is automatically reduced by the transfer
        UNISWAP_V3_POSITION_MANAGER.createAndInitializePoolIfNecessary(token0, token1, poolFee, sqrtPriceX96); // @audit can exist before with different price.
        ...
```

그러나 풀 생성 및 초기화 트랜잭션은 모든 계정에 의해 프론트러닝 될 수 있으며, 이는 매우 높거나 낮은 `sqrtPriceX96` 값으로 풀을 초기화할 수 있습니다.

- 허용되는 최대 슬리피지(`slippageBips`)가 설정되면 `moveToAMM` 함수가 되돌아가 TokenFactory 계약에 자금이 갇히게 됩니다.
- `slippageBips`가 설정되지 않은 경우 가격 조작으로 인해 Uniswap V3 풀로 이동된 자금이 손실될 수 있습니다.

`moveToAMM`을 호출하기 전에 풀이 이미 존재하는지 확인하고 현재 `sqrtPriceX96`을 확인하고 스왑하여 가격을 수정하는 것이 좋습니다.

# [L-07] 총 수수료가 10000 이상일 수 있음

`protocolFee`와 `creatorFee`에 수수료가 10000이 아님을 확인하는 검사가 있지만 모두 합하면 10000 이상이 될 수 있습니다.

```solidity
    function setProtocolFeeBips(uint256 _protocolFeeBips, uint256 _protocolFeeWithReferralBips) external onlyOwner {
>>      if (_protocolFeeBips > 10000 || _protocolFeeWithReferralBips > 10000) {
            revert TokenFactory_InvalidFeeBips();
        }
        emit ProtocolFeeBipsUpdated(
            protocolFeeBips, _protocolFeeBips, protocolFeeWithReferralBips, _protocolFeeWithReferralBips
        );
        protocolFeeBips = _protocolFeeBips;
        protocolFeeWithReferralBips = _protocolFeeWithReferralBips;
    }

    function setCreatorFeeBips(uint256 _creatorFeeBips, uint256 _creatorFeeWithReferralBips) external onlyOwner {
>>      if (_creatorFeeBips > 10000 || _creatorFeeWithReferralBips > 10000) {
            revert TokenFactory_InvalidFeeBips();
        }
        emit CreatorFeeBipsUpdated(
            creatorFeeBips, _creatorFeeBips, creatorFeeWithReferralBips, _creatorFeeWithReferralBips
        );
        creatorFeeBips = _creatorFeeBips;
        creatorFeeWithReferralBips = _creatorFeeWithReferralBips;
    }
```

개별 수수료가 소액(예: 500(5%)) 미만인지 확인하거나 `getFeesFromPrice()`에서 세 가지 수수료가 모두 합해 10000 이상이 되지 않는지 확인하십시오.

또한 수수료를 그렇게 높게 설정해서는 안 되므로 아마도 2000(20%) 이상 합산되지 않아야 합니다.

```solidity
 function getFeesFromPrice(uint256 priceBeforeFees, bool hasReferral)
        public
        view
        returns (uint256 protocolFee, uint256 creatorFee, uint256 referralFee)
    {
        (uint256 _protocolBips, uint256 _creatorBips, uint256 _referralBips) = _getFeeBips(hasReferral);
>       require(_protocolBips + _creatorBips + _referralBips < 2000);
        protocolFee = (priceBeforeFees * _protocolBips) / BIPS_DIVISOR;
        creatorFee = (priceBeforeFees * _creatorBips) / BIPS_DIVISOR;
        referralFee = (priceBeforeFees * _referralBips) / BIPS_DIVISOR;
    }
```

# [L-08] `x * y = k` 불변성이 유지되지 않음

공식 `x * y = k`를 사용하는 상수 곱 AMM에서 주요 불변성은 k 값이 각 스왑 후에도 감소해서는 안 된다는 것입니다. 그러나 `getEthAmountWhenBuyWithExactTokenOutput`에서 지정된 토큰 금액을 구매하기 위한 ETH 금액을 계산할 때 반올림 오류로 인해 k 값이 감소하여 유동성 손실이 발생할 수 있습니다. 계산된 `ethAmount`는 내림됩니다.

```solidity
    function getEthAmountWhenBuyWithExactTokenOutput(address erc20Address, uint256 tokenAmount)
        public
        view
        returns (uint256 ethAmount)
    {
        (uint256 x, uint256 y, uint256 k) = _getCurveDetails(erc20Address);

        if (tokenAmount > x) {
            revert TokenFactory_InvalidTokenAmount();
        }

        uint256 newX = x - tokenAmount;
        uint256 newYScaled = (k * _PRECISION_MULTIPLIER) / newX;

        ethAmount = (newYScaled - y * _PRECISION_MULTIPLIER) / _PRECISION_MULTIPLIER; // @audit ETH amount is rounded down
    }
```

`buyWithReferral`이 호출될 때마다 k 값이 감소하여 판매된 동일한 토큰 양에 대해 더 적은 양의 ETH를 받게 됩니다.

ethAmount를 올림 하십시오:

```diff
    function getEthAmountWhenBuyWithExactTokenOutput(address erc20Address, uint256 tokenAmount)
        public
        view
        returns (uint256 ethAmount)
    {
        (uint256 x, uint256 y, uint256 k) = _getCurveDetails(erc20Address);

        if (tokenAmount > x) {
            revert TokenFactory_InvalidTokenAmount();
        }

        uint256 newX = x - tokenAmount;
        uint256 newYScaled = (k * _PRECISION_MULTIPLIER) / newX;

-       ethAmount = (newYScaled - y * _PRECISION_MULTIPLIER) / _PRECISION_MULTIPLIER;
+       ethAmount = (newYScaled - y * _PRECISION_MULTIPLIER) / _PRECISION_MULTIPLIER + 1;
    }
```

Uniswap V2 계산 참조:

        amountIn = (numerator / denominator).add(1);

[링크](https://github.com/Uniswap/v2-periphery/blob/master/contracts/libraries/UniswapV2Library.sol#L58)

# [L-09] 악의적인 생성자가 사용자를 가스-그리핑(gas-grief) 할 수 있음

토큰을 사고 팔 때 생성자 수수료가 있습니다. ETH로 된 이 수수료는 생성자 수수료 수령인에게 직접 전송됩니다. 가스 제한은 100,000입니다.

```
        (bool creatorFeeSuccess,) = creatorFeeRecipients[erc20Address].call{value: creatorFee, gas: 100_000}("");
        if (!creatorFeeSuccess) {
            revert TokenFactory_CreatorFeeTransferFailed();
        }
```

악의적인 생성자는 `creatorFeeRecipient`를 스마트 계약으로 설정하고 사용자가 의도한 것보다 더 많은 가스를 지불하도록 가스 그리핑을 하기 위해 무작위 작업(예: 아무것도 아닌 것에 대해 1000번 루프)을 호출할 수 있습니다.

악의적일 수 있고 많은 문제를 일으킬 수 있으므로 생성자에게 ETH를 직접 보내지 않는 것이 좋습니다. 대신 생성자가 스스로 수수료를 인출하도록 하십시오(획득한 모든 생성자 수수료를 추적하는 매핑을 만들고 생성자가 획득한 ETH를 인출할 수 있는 다른 함수를 추가하십시오).

