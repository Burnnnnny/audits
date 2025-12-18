# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**bob-collective/bob-gateway** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# BOB 소개

BOB은 비트코인과 이더리움을 기반으로 하는 하이브리드 Layer-2입니다. 비트코인 사용자가 이전에 이더리움 자산을 보유하지 않고도 BOB L2에 쉽게 온보딩할 수 있도록 설계되었습니다. 사용자는 신뢰할 수 있는 릴레이어와 조정하여 사용 가능한 유동성 중 일부를 예약하고, 비트코인 메인넷에서 BTC를 전송한 다음, 릴레이어는 BOB에서 ERC20 토큰에 대한 스왑을 실행하기 위한 머클 증명을 제공할 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [1511179bfc908b73020e8c3b668957c7857a8c61](https://github.com/bob-collective/bob-gateway/tree/1511179bfc908b73020e8c3b668957c7857a8c61)

_수정 검토 커밋 해시_ - [36b5dec7446538c9e54e94291a755ace4f0cf920](https://github.com/bob-collective/bob-gateway/tree/36b5dec7446538c9e54e94291a755ace4f0cf920)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `OnrampV1`
- `OnrampFactoryV1`
- `Gateway`
- `GatewayRegistry`
- `ERC20Mintable`
- `Constants`
- `CommonStructs`
- `IERC20Ext`
- `IGateway`
- `VelodromeSwapper`
- `TestnetSwapper`
- `ISwapper`
- `IRouter`
- `IWETH`

# 발견 사항

# [L-01] 불필요한 onlyOwner 제한

Gateway 계약의 `depositERC20` 함수는 `onlyOwner`로 제한되어 계약 소유자만 계약에 토큰을 예치할 수 있습니다. 그러나 누구나 토큰을 계약 주소로 직접 전송하여 이 함수를 완전히 우회할 수 있으므로 이 제한은 대체로 효과가 없습니다.

```solidity
    /**
     * @dev Deposit tokens in the contract.
     * Can only be called by the current owner
     */
    function depositERC20(uint256 amount) external onlyOwner { // @audit restrict to owner but anyone can transfer token to the contract
        emit DepositERC20(amount);
        IERC20(token).safeTransferFrom(_msgSender(), address(this), amount);
    }
```

Gateway의 유동성을 얻을 때 릴레이어는 `availableLiquidity()`를 직접 호출하여 계약의 토큰 잔액을 조회합니다.

# [L-02] Deadline이 block.timestamp로 설정됨

`swapExactTokensForNative` 함수에서 라우터를 호출하여 토큰을 ETH로 스왑할 때 deadline 매개변수가 block.timestamp로 설정됩니다. 즉, deadline은 트랜잭션이 제출된 시점이 아니라 검증자가 트랜잭션을 포함할 때의 현재 블록 타임스탬프로 설정됩니다. deadline 매개변수가 없으면 트랜잭션이 멤풀에 머물러 훨씬 나중에 실행되어 사용자에게 더 나쁜 가격을 초래할 수 있습니다. 그러나 amountIn이 소량의 ETH이므로 영향은 적습니다.

릴레이어가 적절한 미래 타임스탬프를 설정할 수 있도록 `swapExactTokensForNative` 함수에 `deadline` 매개변수를 추가하는 것을 고려하십시오.

```solidity
    function swapExactTokensForNative(
        IERC20 _token,
        uint256 _amountIn,
        uint256 _amountOutMin,
        address payable _to,
        bytes memory
    ) external override returns (uint256 outAmount) {
        ...

        outAmount = router.swapExactTokensForETH(_amountIn, _amountOutMin, routes, _to, block.timestamp)[1]; // @audit deadline is set to block.timestamp
    }
```

# [L-03] Velodrome 풀 일시 중지로 BTC 브리징 중단 가능성

게이트웨이 프로세스에서 토큰을 스왑하는 데 사용되는 Velodrome 풀은 풀 팩토리 관리자가 일시 중지할 수 있습니다. 풀이 일시 중지되면 토큰 스왑 ETH 함수가 되돌려져 여러 번의 재시도 시도에도 불구하고 BTC가 Bob L2 체인으로 브리지되는 것을 방지합니다.

```solidity
    function swap(uint256 amount0Out, uint256 amount1Out, address to, bytes calldata data) external nonReentrant {
        if (IPoolFactory(factory).isPaused()) revert IsPaused();
```

풀이 일시 중지되었는지 확인한 다음 사용자에 대한 ETH 스왑을 건너뛰는 것이 좋습니다.

# [L-04] 게이트웨이 소유자가 손실을 초래할 수 있는 악성 스와퍼를 설정할 수 있음

`setSwapper` 함수를 사용하면 `Gateway owner`가 모든 계약을 스와퍼로 설정할 수 있습니다. 소유자가 악성 계약을 스와퍼로 설정하면 악성 계약은 스왑 프로세스 중에 자금을 훔치도록 프로그래밍될 수 있습니다.

```solidity
...
    function _setSwapper(ISwapper _swapper) internal {
        emit UpdateSwapper(address(_swapper));
        swapper = _swapper;
    }
...
...
    function releaseFundsAndInvest(
        bytes32 _txHash,
        uint256 _outputValueSat,
        address payable _recipient,
        bool _sendEthToUser,
        uint256 _ethTransferGasLimit,
        bytes memory /* any extra data that is needed for this specific gateway contract to release the funds and invest) */
    ) external override onlyRegistry {
        ...
        ...
            receivedEthFromSwap =
                swapper.swapExactTokensForNative(token, cappedAmountToSwap, 0, payable(this), new bytes(0));
        ...
        ...
    }
...
...
```

`Gateway` 계약 생성 시 초기에만 스왑 계약을 설정하는 것이 좋습니다. 이 프로세스는 비용을 지불하는 프로토콜 자체에서 감사합니다(허가됨). 그렇지 않으면 악의적인 소유자가 스와퍼 계약을 변경할 수 있으며 악의적인 스와퍼를 사용하는 조건에 동의하는 사용자가 있을 수 있습니다.

# [L-05] 수수료 범위 길이 제한 부족

`Gateway.sol` 계약에서 `setFeeRanges` 함수는 설정할 수 있는 수수료 범위의 수에 대한 제한이 없습니다. 프로토콜이 `Gateway.sol` 배포에 대해 [비용을 지불](https://github.com/bob-collective/bob-gateway?tab=readme-ov-file#lp-flow)하므로

> LP는 릴레이어에게 게이트웨이 계약을 배포하도록 요청합니다(수수료를 지불하므로 허가됨).

제한 없는 수수료 범위 수는 과도한 가스 소비로 이어져 프로토콜의 운영 비용이 높아질 수 있습니다.

```solidity
    constructor(GatewayConstructorArgs memory gatewayConstructorArgs) Ownable2Step() {
        require(gatewayConstructorArgs.gatewayOwner != address(0), "Owner is the zero address");
        _transferOwnership(gatewayConstructorArgs.gatewayOwner);

        registry = gatewayConstructorArgs.gatewayRegistry;
        token = gatewayConstructorArgs.token;
        require(token.decimals() >= 8, "Invalid token");
        multiplier = 10 ** (token.decimals() - 8);

        _setOutputScript(gatewayConstructorArgs.outputScript);
        _setDustThreshold(_DEFAULT_DUST_THRESHOLD);
        _setConvertedBtcToBeSwappedToEth(gatewayConstructorArgs.btcToBeSwappedToEth);
>>>     _setFeeRanges(gatewayConstructorArgs.feeRanges);
        _setSwapper(gatewayConstructorArgs.swapper);
    }
...
...
   function _setFeeRanges(FeeRange[] memory _feeRanges) internal {
        uint256 feeRangesLength = _feeRanges.length;
        require(_feeRanges[0].amountLowerRange == 0, "First range lower boundary has to be 0");

>>>     for (uint256 i; i < feeRangesLength - 1; ++i) {
            require(
                _feeRanges[i].amountLowerRange < _feeRanges[i + 1].amountLowerRange,
                "Amount lower ranges need to be sorted ascending"
            );
            require(
                _feeRanges[i].scaledFeePercent > _feeRanges[i + 1].scaledFeePercent,
                "Scaled fee percent need to be sorted descending"
            );
            require(_feeRanges[i].scaledFeePercent <= SCALED_MAX_FEE, "Fee must be <= maximum fee for all ranges");
        }
        require(
            _feeRanges[feeRangesLength - 1].scaledFeePercent <= SCALED_MAX_FEE,
            "Fee must be <= maximum fee for all ranges"
        );

        emit UpdateFeeRanges(_feeRanges);

        // Solc compiler: Copying of type struct FeeRange memory[] memory to storage not yet supported.
        delete feeRanges;
        for (uint256 i; i < feeRangesLength; ++i) {
            feeRanges.push(_feeRanges[i]);
        }
    }
...
...
```

또한 릴레이어가 `releaseFundsAndInvest` 함수를 호출할 때마다 수수료가 계산됩니다. 이 가스 지출은 릴레이어가 지불하므로 프로토콜의 운영 비용이 증가합니다.

```solidity
   function releaseFundsAndInvest(
        bytes32 _txHash,
        uint256 _outputValueSat,
        address payable _recipient,
        bool _sendEthToUser,
        uint256 _ethTransferGasLimit,
        bytes memory /* any extra data that is needed for this specific gateway contract to release the funds and invest) */
    ) external override onlyRegistry {
        ...
        ...
        // Passing feeRanges to calculateFee even though this incurs a small gas penalty compared to directly using it inside the method.
        // Passing it to calculateFee will make the method pure and make testing easier.
>>>     uint256 feeSat = calculateFee(_outputValueSat, feeRanges);
        ...
        ...
    }
...
...
   function calculateFee(uint256 _amount, FeeRange[] memory _feeRanges) public view virtual returns (uint256 fee) {
        uint256 feeRangesLength = _feeRanges.length;

        uint256 feeRangeIndex = 1;
>>>     for (; feeRangeIndex < feeRangesLength; ++feeRangeIndex) {
            if (_feeRanges[feeRangeIndex].amountLowerRange > _amount) {
                break;
            }
        }
        --feeRangeIndex;

        fee = (_amount * _feeRanges[feeRangeIndex].scaledFeePercent) / Constants.FEE_SCALER;
    }
```

## 권장 사항

설정할 수 있는 수수료 범위 수에 합리적인 제한을 두는 것이 좋습니다.

# [L-06] 높은 `convertedBtcAmountToBeSwappedToEth` 값 설정 허용됨

`Gateway` 계약에서 소유자는 `Gateway::setConvertedBtcToBeSwappedToEth` 함수를 통해 `wETH`로 스왑될 변환된 BTC의 양을 설정할 수 있습니다. 스왑된 `wETH`는 수신자에게 전송됩니다. 스왑 함수의 `amountOut` 매개변수는 0으로 설정되어 있습니다. 이는 코드 주석에 언급된 대로 변환된 양이 적으므로 스왑에 대해 `amountOut`이 0으로 설정되어 경제적으로 가치가 없기 때문에 받은 ETH 양에 관계없이 스왑이 진행됨을 의미합니다. 이 설정은 샌드위치 공격을 방지하기 위한 것이지만 특정 조건에서는 작동하지 않습니다.

```solidity
   function releaseFundsAndInvest(
        bytes32 _txHash,
        uint256 _outputValueSat,
        address payable _recipient,
        bool _sendEthToUser,
        uint256 _ethTransferGasLimit,
        bytes memory /* any extra data that is needed for this specific gateway contract to release the funds and invest) */
    ) external override onlyRegistry {
        // slither-disable-next-line timestamp
        require(updateStart == 0 || block.timestamp <= updateStart + UPDATE_DELAY, "Not allowed to execute");

        require(_outputValueSat >= dustThreshold, "Amount too small");

        require(!spent[_txHash], "Transaction already spent");
        spent[_txHash] = true;

        // Passing feeRanges to calculateFee even though this incurs a small gas penalty compared to directly using it inside the method.
        // Passing it to calculateFee will make the method pure and make testing easier.
        uint256 feeSat = calculateFee(_outputValueSat, feeRanges);

        // scale the Btc so the decimal count of Btc will correspond to converted Btc
        uint256 scaledAmount = (_outputValueSat - feeSat) * multiplier;

        // cap at the amount of Btc sent by the user, meaning if the user sends less Btc
        // then the conversion amount everything is swapped to Eth
>>>     uint256 cappedAmountToSwap = Math.min(convertedBtcAmountToBeSwappedToEth, scaledAmount);

        uint256 receivedEthFromSwap = 0;
        if (_sendEthToUser) {
            // now subtract the amount to be swapped
            scaledAmount = scaledAmount - cappedAmountToSwap;

            IERC20(token).safeIncreaseAllowance(address(swapper), cappedAmountToSwap);
            // 1.
            // For extra safety against contract level reentrancy, the ETH is transferred back to the
            // contract instead of directly transferred to the user supplied address using the AMM's (optionally present) feature.
            // After transferred back to the contract, the gas will be limited to 2300 by the transfer function before forwarding it to the
            // user supplied address. Doing the transfer directly from the AMM might not limit the gas which could cause reentrancy attack.

>>>         // 2.
>>>         // The amountOut for the swap is set to 0, as the small amount of converted Btc will make sandwich attacks
>>>         // not worthy economically to carry out.
>>>         // Denial of service attacks are still possible where an attacker would make sandwich attacks to decrase the amount of
>>>         // ETH the user receives to a near 0 amount. It would be costly though for the attacker.
>>>         receivedEthFromSwap =
>>>             swapper.swapExactTokensForNative(token, cappedAmountToSwap, 0, payable(this), new bytes(0));

            (bool sent,) = _recipient.call{value: receivedEthFromSwap, gas: _ethTransferGasLimit}("");
            require(sent, "Could not transfer ETH");
        }
        emit ExecuteSwap(_recipient, _txHash, _outputValueSat, feeSat, scaledAmount, receivedEthFromSwap);

        // transfer converted Btc token
        IERC20(token).safeTransfer(_recipient, scaledAmount);
    }
```

악의적인 소유자는 `convertedBtcAmountToBeSwappedToEth`를 높은 값으로 설정할 수 있습니다. `amountOut`이 0으로 설정되어 있으므로 스왑은 계속 진행되지만 수신자는 무시할 수 있는 양의 ETH를 받거나 토큰을 전혀 받지 못할 수 있습니다. 다음 시나리오를 고려하십시오.

1. `Gateway's owner`는 `convertedBtcAmountToBeSwappedToEth=4`로 설정합니다. 이는 `4 ERC20 btc tokens`이 `weth`로 변환됨을 의미합니다.
2. 사용자는 조건에 동의하고 `10 btc`를 `Gateway.owner` (scriptPubKeyHash)에게 전송합니다.
3. 릴레이어는 `Gateway::releaseFundsAndInvest` 함수를 호출하고 `4 ERC20 btc tokens` (convertedBtcAmountToBeSwappedToEth)이 `weth`로 스왑되도록 할당됩니다. 그러나 스왑 함수에서 `amountOut`이 0으로 설정되어 있으므로 `Gateway owner`는 이를 악용하여 샌드위치 공격을 수행하여 수신자에게 `weth`가 전송되지 않도록 합니다.
4. 수신자는 전송한 `10 BTC` 중 `6 ERC20 btc tokens`만 받습니다. `4 ERC20 btc tokens`는 스왑 샌드위치 공격을 통해 소유자에 의해 도난당했습니다.

`convertedBtcAmountToBeSwappedToEth`를 설정할 수 있는 최대값에 대한 상한을 구현하십시오. 이렇게 하면 `owner`가 악용될 수 있는 지나치게 높은 값을 설정하는 것을 방지할 수 있습니다.

# [L-07] 토큰 일시 중지 메커니즘으로 인한 릴레이어 작업 중단 가능성

`Gateway` 계약은 소유자가 `startUpdate()`를 호출하여 `relayer`가 `releaseFundsAndInvest`를 실행할 수 있는 6시간 창을 시작하도록 허용합니다. 그러나 토큰 계약이 토큰 전송을 방지하는 일시 중지 메커니즘을 구현하는 경우 릴레이어는 허용된 기간 내에 이 기능을 실행할 수 없습니다. 6시간 창이 지나면 소유자는 상태 업데이트를 진행할 수 있으며 릴레이어는 필요한 토큰 전송을 수행할 기회가 전혀 없을 수 있습니다. 다음 시나리오를 고려하십시오.

1. 사용자는 조건에 동의하고 `btc`를 `gateway owner's` 계정(scriptPubKeyHash)으로 전송합니다.
2. 게이트웨이 소유자는 `Gateway::startUpdate`를 시작하고 릴레이어는 1단계에서 생성된 주문을 실행하는 데 6시간만 있습니다.
3. 어떤 이유로 ERC20 `Gateway.token`이 일시 중지 메커니즘을 활성화하고 전송이 되돌려집니다.
4. 릴레이어는 `GatewayRegistry::proveBtcTransfer`를 호출하여 차례로 `Gateway::releaseFundsAndInvest`를 호출합니다. 그러나 토큰이 일시 중지되어(트랜잭션이 되돌려짐) 토큰 전송이 불가능합니다.

```solidity
    function releaseFundsAndInvest(
        bytes32 _txHash,
        uint256 _outputValueSat,
        address payable _recipient,
        bool _sendEthToUser,
        uint256 _ethTransferGasLimit,
        bytes memory /* any extra data that is needed for this specific gateway contract to release the funds and invest) */
    ) external override onlyRegistry {
        ...
        ...
        // transfer converted Btc token
>>>     IERC20(token).safeTransfer(_recipient, scaledAmount);
    }
```

5. 6시간이 지나고 이제 `Gateway.owner`는 계약 상태를 변경할 수 있어 `Gateway.owner`가 ERC20 토큰을 인출할 수 있습니다. 그동안 토큰은 일시 중지 해제됩니다.
6. 릴레이어는 `releaseFundsAndInvest`를 다시 실행하려고 시도하지만 `Gateway` 계약에는 더 이상 사용 가능한 토큰이 없습니다.

[WBTC](https://etherscan.io/token/0x2260fac5e5542a773aa44fbcfedf7c193bc2c599#code#L650)는 일시 중지 메커니즘을 포함하는 토큰의 예입니다. 이 기능을 사용하면 토큰 관리자가 전송을 일시적으로 중단할 수 있으며 업그레이드 중이나 보안 위협에 대응하여 활용할 수 있습니다.

```solidity
contract WBTC is StandardToken, DetailedERC20("Wrapped BTC", "WBTC", 8),
    MintableToken, BurnableToken, PausableToken, OwnableContract {
    ...
    ...
```

ERC20 토큰이 일시 중지 해제되면 수신자가 해당 토큰을 얻을 수 있도록 풀 메커니즘을 구현하는 것이 좋습니다. 또한 `Gateway.owner`가 이미 청구 가능한 `ERC20` 토큰을 인출하지 못하도록 잔액을 따로 설정해야 합니다.

1. 토큰이 일시 중지됩니다.
2. 릴레이어는 `Gateway::releaseFundsAndInvest`를 호출하고, 이때 ERC20 토큰이 게이트웨이 잔액에서 차감됩니다. 이 조치는 ERC20 토큰 계약이 일시 중지 해제되는 즉시 수신자가 검색할 수 있도록 지정되므로 `Gateway.owner`가 ERC20 토큰을 인출할 수 없도록 합니다.
3. 시간이 지나고 토큰이 일시 중지 해제되면 수신자는 예약된 토큰을 가져올 수 있습니다.

