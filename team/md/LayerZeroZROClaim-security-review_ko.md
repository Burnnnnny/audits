# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Pashov Audit Group**은 **LayerZero-Labs/ZROClaim** 저장소에 대한 시간 제한 보안 검토를 수행했으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# ZRO Claim 정보

ClaimRemote 계약은 원격 ZROClaimHub 계약으로 메시지를 전송하여 크로스 체인 토큰 청구를 용이하게 하고, Merkle 증명을 기반으로 토큰 청구를 처리하며, 청구를 확인하기 전에 기부자의 기여를 보장합니다. 여기에는 속도 제한, 수수료 견적 및 구성 가능한 청구 매개변수가 포함되어 있으며, 속도 제한을 설정하고 메시지 무결성을 확인하려면 소유자 권한이 필요합니다.

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

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [36ea2473e9b67aa6681dc1ebb368c7db6dcd0cd1](https://github.com/LayerZero-Labs/ZROClaim/tree/36ea2473e9b67aa6681dc1ebb368c7db6dcd0cd1)

_수정 검토 커밋 해시_ - [f4871d4f330ddb6b3eacc89edbb4b3d25a7167e0](https://github.com/LayerZero-Labs/ZROClaim/tree/f4871d4f330ddb6b3eacc89edbb4b3d25a7167e0)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `ZROClaimCore`
- `ZROClaimHub`
- `ZROClaimSpoke`
- `IZROCLaim`

# 발견 사항

# [C-01] 분자가 하나 차이남

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`ClaimCore` 생성자는 ZRO 토큰을 청구하는 데 필요한 기부 금액을 계산하기 위한 분자 (`numeratorUsdc`, `numeratorUsdt`, `numeratorNative`)를 설정합니다.

그러나 초기화 중에 10진수 지수에서 잘못 1을 뺌으로써 분자가 10배 차이가 납니다. 이렇게 하면 필요한 기부금이 10배 줄어들어 누구나 예상보다 10배 더 많은 ZRO를 청구할 수 있습니다.

```Solidity
    constructor(
        bytes32 _merkleRoot,
        address _donateContract,
        address _stargateUsdc,
        address _stargateUsdt,
        address _stargateNative,
        uint256 _nativePrice,
        address _owner
    ) Ownable(_owner) {
        merkleRoot = _merkleRoot;
        donateContract = IDonate(_donateContract);

        // TODO needs tests
        if (_stargateUsdc != address(0)) {
            //@audit this is off by a factor of 10
>>>         numeratorUsdc = 1 * 10 ** (IERC20Metadata(IOFT(_stargateUsdc).token()).decimals() - 1);
        }

        if (_stargateUsdt != address(0)) {
            //@audit this is off by a factor of 10
>>>         numeratorUsdt = 1 * 10 ** (IERC20Metadata(IOFT(_stargateUsdt).token()).decimals() - 1);
        }

        // native is always denominated in 18 decimals
        if (_stargateNative != address(0) && _nativePrice > 0) {
            //@audit this is off by a factor of 10
>>>         numeratorNative = _nativePrice * 10 ** (18 - 1);
            // Validate this is an actual native pool, eg. NOT WETH
            if (IOFT(_stargateNative).token() != address(0)) {
                revert InvalidNativeStargate();
            }
        }
    }
```

## 권장 사항

분자 초기화에서 1을 빼는 것을 제거하십시오.

# [C-02] 원격 기부가 멈춤

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

사용자는 `RemoteDonate`와 상호 작용하여 비 이더리움 체인에서 기부합니다. 그리고 기부금은 Stargate 크로스 체인 전송을 통해 이더리움 체인의 `donationReceiver`로 원격으로 인출됩니다.

크로스 체인 전송으로 인해 `donationAmount`를 이더리움 체인으로 전송하는 동안 `msg.value`를 통해 LZ 수수료를 지불해야 합니다.

문제는 `donationAmount`에 `address(this).balance` 값이 할당된다는 것입니다. 여기에는 실제로 체인 간 전송을 위한 `msg.value`가 포함되어 있습니다.

이로 인해 `IOFT(stargate).send{ value: msg.value + donationAmountNative }`는 `address(this).balance`보다 더 많이 보내려고 하기 때문에 실패하게 됩니다.

이로 인한 영향은 원격 기부에 대한 인출이 항상 실패하여 계약 내에 갇히게 된다는 것입니다.

```Solidity
    function withdrawDonation(Currency _currency, uint256 _minAmount) external payable {
        address stargate;
        uint256 donationAmount;
        uint256 donationAmountNative;

        if (_currency == Currency.USDC && stargateUsdc != address(0)) {
            stargate = stargateUsdc;
            donationAmount = tokenUsdc.balanceOf(address(this));
        } else if (_currency == Currency.USDT && stargateUsdt != address(0)) {
            stargate = stargateUsdt;
            donationAmount = tokenUsdt.balanceOf(address(this));
        } else if (_currency == Currency.Native && stargateNative != address(0)) {
            stargate = stargateNative;
        //@audit this would have included the LZ fee (msg.value) as well
>>>         donationAmount = address(this).balance;
            donationAmountNative = donationAmount;
        } else {
            revert UnsupportedCurrency(_currency);
        }//ok

        // sends via taxi
        bytes memory emptyBytes = new bytes(0);
        SendParam memory sendParams = SendParam({
            dstEid: remoteEid, // only send to ethereum
            to: bytes32(uint256(uint160(donationReceiver))),
            amountLD: donationAmount,
            minAmountLD: _minAmount,
            extraOptions: emptyBytes,
            composeMsg: emptyBytes,
            oftCmd: emptyBytes // type taxi
        });//ok

        // combine the msg value in addition to the donation amount
        // in non native currencies this will just be 0
        // solhint-disable-next-line check-send-result

        //@audit this will always fail since it is sending more than address(this).balance
 >>>    IOFT(stargate).send{ value: msg.value + donationAmountNative }(
            sendParams,
            MessagingFee(msg.value, 0),
            msg.sender // refund any excess native to the sender
        );

        emit DonationWithdrawn(_currency, donationReceiver, donationAmount);
    }
```

## 권장 사항

전송을 위한 실제 기부 금액을 계산하도록 다음 변경 사항을 적용하십시오.

```diff
        } else if (_currency == Currency.Native && stargateNative != address(0)) {
            stargate = stargateNative;
-            donationAmount = address(this).balance;
+            donationAmount = address(this).balance - msg.value;
            donationAmountNative = donationAmount;
        } else {
```

# [H-01] WithdrawDonation에서 최대 슬리피지가 발생할 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`DonateRemote`의 `withdrawDonation` 함수는 누구나 지정된 기부 수령인에게 기부금을 전송할 수 있는 무허가(permissionless) 함수입니다. 슬리피지 보호 매개변수인 `minAmount`는 보낸 사람이 설정합니다. 즉, 모든 계정은 `_minAmount`를 0으로 설정할 수 있으므로 로컬 체인으로 전송된 자산에 최대 슬리피지 손실이 발생할 수 있습니다.

```solidity
    function withdrawDonation(Currency _currency, uint256 _minAmount) external payable {
        ...
        // sends via taxi
        bytes memory emptyBytes = new bytes(0);
        SendParam memory sendParams = SendParam({
            dstEid: remoteEid, // only send to ethereum
            to: bytes32(uint256(uint160(donationReceiver))),
            amountLD: donationAmount,
            minAmountLD: _minAmount, // @audit can be set to 0
            extraOptions: emptyBytes,
            composeMsg: emptyBytes,
            oftCmd: emptyBytes // type taxi
        });
        ...
    }
```

## 권장 사항

DonateRemote의 `withdrawDonation`은 `_minAmount` 매개변수를 안전한 값으로 설정할 수 있도록 허가된(permissioned) 기능이어야 합니다.

# [H-02] 수수료 환불이 있을 때 `_lzReceive` 되돌림 발생

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

ZROToken 잔액을 사용자에게 전송하기 위해 원격 `ZROToken`으로 메시지를 보낼 때 수수료가 필요한 금액을 초과하면 잉여금이 `address(this)`(`ClaimLocal` 계약)로 환불됩니다. 그러나 `ClaimLocal` 계약에는 `fallback` 또는 `receive` 함수가 없어 ETH를 받을 수 없습니다. 이로 인해 `_lzReceive` 함수가 항상 되돌려져 사용자가 원격 체인에서 토큰을 청구할 수 없게 됩니다.

```solidity
    function _lzReceive(
        Origin calldata _origin,
        bytes32 /*_guid*/,
        bytes calldata _payload,
        address /*_executor*/,
        bytes calldata /*_extraData*/
    ) internal override {
        (address user, uint256 zroAmount, address to) = abi.decode(_payload, (address, uint256, address));
        ...

        // solhint-disable-next-line check-send-result
        IOFT(zroToken).send{ value: msg.value }(sendParams, MessagingFee(msg.value, 0), address(this)); // @audit refund fee cannot be received by ClaimLocal contract

        emit ClaimRemote(user, availableToClaim, _origin.srcEid, to);
    }
```

LayerZero 엔드포인트 계약은 수수료를 반환하기 위해 낮은 수준의 호출에 `TransferLibrary`를 사용하는 SendLibrary를 사용합니다.

```solidity
library Transfer {
    ...

    function native(address _to, uint256 _value) internal {
        if (_to == ADDRESS_ZERO) revert Transfer_ToAddressIsZero();
        (bool success, ) = _to.call{ value: _value }("");
        if (!success) revert Transfer_NativeFailed(_to, _value);
    }

    ...
}
```

[링크](https://etherscan.io/address/0xbB2Ea70C9E858123480642Cf96acbcCE1372dCe1#code#F11#L16)

## 권장 사항

ClaimLocal 계약에 `receive` 함수를 추가하여 ETH 환불을 수락할 수 있도록 하십시오.

```diff
+    receive() external payable {}
```

# [M-01] `donateAndClaim()`은 원격 기부 및 청구에 대해 작동하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

사용자는 `donateAndClaim()`을 활용하여 `msg.value`를 통해 네이티브 토큰으로 기부한 다음 ZRO 청구를 수행할 수 있습니다.

그러나 원격 청구를 수행하기 위해 `claim()`의 `value` 매개변수에 LZ 수수료를 전달하지 않으므로 작동하지 않습니다.

```Solidity
    function donateAndClaim(
        Currency currency,
        uint256 amountToDonate,
        uint256 _zroAmount,
        bytes32[] calldata _proof,
        address _to,
        bytes calldata _extraBytes
    ) external payable returns (MessagingReceipt memory receipt) {
        // move tokens into the wrapper to forward to the donate contract
        if (currency == Currency.USDC && stargateUsdc != address(0)) {
            tokenUsdc.safeTransferFrom(msg.sender, address(this), amountToDonate);
        } else if (currency == Currency.USDT && stargateUsdt != address(0)) {
            tokenUsdt.safeTransferFrom(msg.sender, address(this), amountToDonate);
        } else if (currency == Currency.Native && stargateNative != address(0)) {
            // native do nothing
        } else {
            // sanity just in case somehow a different currency is somehow passed
            revert IDonate.UnsupportedCurrency(currency);
        }

        // donate
        IDonate(donateContract).donate{ value: msg.value }(currency, amountToDonate, msg.sender);

        // claim

        //@audit need to pass in the gas value for remote claim
>>>     return IClaim(claimContract).claim(msg.sender, currency, _zroAmount, _proof, _to, _extraBytes);
    }
```

```Solidity
    function _claim(
        address _user,
        Currency _currency,
        uint256 _zroAmount,
        bytes32[] calldata _proof,
        address _to,
        bytes calldata _extraOptions // will need enough to send 'msg.value' back to this chain via, quoteClaimCallback()
    ) internal returns (MessagingReceipt memory receipt) {
        // step 1: assert claimer has donated sufficient amount
        assertDonation(_currency, _user, _zroAmount);

        // step 2: validate the proof is correct
        if (!_verifyProof(_user, _zroAmount, _proof)) revert InvalidProof();

        // step 3: generate the message and options for the crosschain call
        bytes memory payload = abi.encode(_user, _zroAmount, _to);
        bytes memory options = combineOptions(remoteEid, MSG_TYPE_CLAIM, _extraOptions);

        // step 4: send the message cross chain
        // solhint-disable-next-line check-send-result

        //@audit this will use the donated native tokens for cross-chain messaging as well
>>>     receipt = endpoint.send{ value: msg.value }(
            MessagingParams(remoteEid, _getPeerOrRevert(remoteEid), payload, options, false),
            payable(_user)
        );

        emit ZRORequested(_user, _zroAmount, _to);
    }
```

## 권장 사항

원격 기부 및 네이티브 토큰 청구의 경우 사용자는 기부된 네이티브 통화 외에 LZ 전송 수수료도 제공해야 합니다.
이 시나리오에서 `DonateAndClaim()`은 `amountToDonate()`를 기부 계약에 전달하고 LZ 수수료는 청구 계약에 전달됩니다.

# [M-02] 스타게이트 토큰 중 하나라도 설정되지 않으면 `DonateRemote` 배포 실패

## 심각도

**영향:** 중간
**가능성:** 중간

## 설명

`DonateCore`와 `DonateLocal`은 `stargateUsdc`, `stargateUsdt`, `stargateNative` 주소 중 하나라도 설정되지 않은 경우에도 기부 계약이 작동하도록 구현되었습니다. 즉, 이러한 계약은 스타게이트 토큰의 모든 조합을 지원할 수 있는 유연성을 갖추고 있으며 모든 토큰을 활성화할 필요는 없습니다.

그러나 `DonateRemote` 생성자는 모든 스타게이트 토큰을 활성화해야 하도록 구현되었습니다. 그 중 하나라도 없으면 생성자가 되돌려지고 초기화에 실패합니다. 의도가 `DonateLocal`과 같은 유연성을 유지하는 것이라면 이것은 잘못된 것입니다.

또한 `IOFT(_stargateNative).token() != address(0))`은 `DonateCore`에서 수행되었으므로 중복됩니다.

```Solidity
contract DonateRemote is DonateCore {
    constructor(
        uint32 _remoteEid,
        address _donationReceiver,
        address _stargateUsdc,
        address _stargateUsdt,
        address _stargateNative
    ) DonateCore(_donationReceiver, _stargateUsdc, _stargateUsdt, _stargateNative) {
        remoteEid = _remoteEid;

        //@audit missing != address(0) checks for the following
>>>     IERC20(IOFT(_stargateUsdc).token()).forceApprove(_stargateUsdc, type(uint256).max);
>>>     IERC20(IOFT(_stargateUsdt).token()).forceApprove(_stargateUsdt, type(uint256).max);

        //@audit this is redundant as this is checked in DonateCore
>>>     if (IOFT(_stargateNative).token() != address(0)) {
            revert InvalidNativeStargate();
        }
    }
```

## 권장 사항

`DonateRemote` 생성자 내에서 다음을 수행하십시오.

- 스타게이트 토큰에 대한 각각의 호출에 대해 조건부 `!= address(0)` 검사를 추가합니다.
- `_stargateNative`에 대한 중복 검사를 제거합니다.

# [M-03] 프로토콜은 1 Stable = 1 USD로 가정합니다.

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

ClaimCore 계약 생성자에서 USDC 및 USDT 가격은 1 USD로 하드 코딩됩니다.

```solidity
        if (_stargateUsdc != address(0)) {
            numeratorUsdc = 1 * 10 ** (IERC20Metadata(IOFT(_stargateUsdc).token()).decimals() - 1);
        }

        if (_stargateUsdt != address(0)) {
            numeratorUsdt = 1 * 10 ** (IERC20Metadata(IOFT(_stargateUsdt).token()).decimals() - 1);
        }
```

이 가정은 스테이블 코인 변동성 이벤트 중에 문제를 일으킬 수 있습니다. 예를 들어 사용자가 1000 ZRO 대가로 1000 USDC(1000 USD에 해당)를 기부하는 경우 USDC가 0.8 USD로 떨어지는 디페깅 이벤트는 사용자가 동일한 1000 ZRO에 대해 1000 USDC(현재 800 USD 가치)를 기부할 수 있음을 의미합니다.

## 권장 사항

Chainlink 오라클을 사용하여 스테이블 가격을 가져오는 것을 고려하십시오.

# [L-01] `requiredDonation()`의 정밀도 손실

`requiredDonation()`은 ZRO의 정밀도(18 소수점)보다 낮은 USDC/USDT 정밀도(6 소수점)로 필요한 기부 횟수를 결정합니다. 이로 인해 정밀도 손실이 발생하여 기부에 필요한 USDC/USDT가 줄어들 수 있습니다.

필요한 기부 금액을 반올림하여 이 문제를 해결할 수 있습니다.

```Solidity
    function requiredDonation(uint256 _zroAmount) public view returns (Donation memory) {
        uint256 usdc = (_zroAmount * numeratorUsdc) / DENOMINATOR;
        uint256 usdt = (_zroAmount * numeratorUsdt) / DENOMINATOR;
        uint256 native = (_zroAmount * numeratorNative) / DENOMINATOR;
        return Donation(usdc, usdt, native);
    }
```

# [L-02] 네이티브 토큰으로 기부하면 계산되지 않을 수 있음

`donate` 함수를 사용하면 사용자가 `DonateCore` 계약에 토큰을 기부하고 전송할 수 있습니다. `_currency`가 네이티브 토큰 이외의 토큰으로 지정되면 토큰 금액을 `Donate` 계약으로 전송합니다. 그러나 이러한 경우 `msg.value == 0`인지 확인하지 않습니다. 기부자가 기부 트랜잭션에서 네이티브 토큰도 보내면 `else if (_currency == Currency.Native && stargateNative != address(0))` 분기가 실행되지 않으므로 네이티브 토큰 기부는 설명되지 않습니다. 이로 인해 네이티브 토큰이 무시됩니다.

`msg.value > 0`인지 확인하고 그에 따라 네이티브 토큰의 기부금을 늘리는 것이 좋습니다.

```solidity
    function donate(Currency _currency, uint256 _amount, address _beneficiary) external payable { // @audit accept native token if _currency != Currency.Native
        if (_currency == Currency.USDC && stargateUsdc != address(0)) {
            tokenUsdc.safeTransferFrom(msg.sender, address(this), _amount);
            donations[stargateUsdc][_beneficiary] += _amount;
        } else if (_currency == Currency.USDT && stargateUsdt != address(0)) {
            tokenUsdt.safeTransferFrom(msg.sender, address(this), _amount);
            donations[stargateUsdt][_beneficiary] += _amount;
        } else if (_currency == Currency.Native && stargateNative != address(0)) {
            if (msg.value != _amount) revert InsufficientMsgValue();
            donations[stargateNative][_beneficiary] += _amount;
        } else {
            // sanity just in case somehow a different currency is somehow passed
            revert UnsupportedCurrency(_currency);
        }
        ...
    }
```

# [L-03] `quoteClaimCallback`에 0 슬리피지 허용 오차가 있음

`quoteClaimCallback()` 함수는 현재 다음과 같이 체인 간에 ZRO를 전송하는 데 필요한 네이티브 금액을 인용합니다.

```solidity
    function quoteClaimCallback(uint32 _dstEid, uint256 _zroAmount) external view returns (MessagingFee memory msgFee) {
        bytes memory emptyBytes = new bytes(0);
        SendParam memory sendParams = SendParam({
            dstEid: _dstEid,
            to: addressToBytes32(msg.sender),
            amountLD: _zroAmount,
            minAmountLD: _zroAmount,
            extraOptions: emptyBytes,
            composeMsg: emptyBytes,
            oftCmd: emptyBytes
        });
        return IOFT(zroToken).quoteSend(sendParams, false);
    }
```

이 구현에서 `_zroAmount`는 `minAmountLD`로 직접 전달됩니다. 이는 콜백이 0.001만큼 작은 슬리피지에 대해서도 되돌려짐을 의미합니다. 콜백에 `minZroAmount` 매개변수를 추가하고 허용 오차를 허용하기 위해 `minAmountLD`로 전달하는 것을 고려하십시오. 동일한 문제가 `ClaimLocal._lzReceive()`에도 존재한다는 점에 유의하십시오.
