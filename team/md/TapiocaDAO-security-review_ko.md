# 정보

**Pashov Audit Group**은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**tapioca-periph** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# tapioca-periph에 대하여

`tapioca-periph` 저장소는 프로토콜의 핵심 기능으로 사용되지는 않지만 작동을 위해 여전히 필요한 외부 계약을 포함합니다. 여기에는 전송 도우미 계약, 트랜잭션 번들링 메커니즘, 화이트리스팅 메커니즘, 스와핑 및 오라클 로직이 포함됩니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| --- | --- | --- | --- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 비교적 가능성이 높습니다.

- 낮음 (Low) - 너무 많거나 비현실적인 가정을 필요로 하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준별 요구 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (이미 배포되지 않은 경우 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

**_검토 커밋 해시_ - [57ff863ae205a968d3ede03d9e04606b60603ee7](https://github.com/Tapioca-DAO/tapioca-periph/tree/57ff863ae205a968d3ede03d9e04606b60603ee7)**

**_수정 검토 커밋 해시_ - [70a86b7f7fbf97ea134e4049cfad444ebe2d26d6](https://github.com/Tapioca-DAO/tapioca-periph/tree/70a86b7f7fbf97ea134e4049cfad444ebe2d26d6)**

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `StargateLbpHelper`
- `Magnetar/modules/MagnetarMarketModule`
- `Magnetar/MagnetarHelper`
- `Magnetar/MagnetarV2`
- `Magnetar/MagnetarV2Storage`
- `Swapper/UniswapV2Swapper`
- `Swapper/UniswapV3Swapper`
- `Swapper/BaseSwapper`
- `oracle/Seer`
- `oracle/utils/SequencerCheck`
- `oracle/implementations/Arbitrum/GLPOracle`
- `oracle/implementations/Arbitrum/TapOracle`
- `oracle/Cluster/Cluster`

# 발견 사항

# [C-01] `read()` 함수가 적절하게 확장 가능하지 않아 잘못된 값을 반환함

## 심각도

**영향**: 높음 (High), 가격 데이터가 매우 부정확할 것이기 때문입니다.

**가능성**: 높음 (High), 항상 발생할 것이기 때문입니다.

## 설명

`OracleUniSolo.sol`에는 `read()` 함수가 있습니다:

```solidity
    function read() external view override returns (uint256) {
        return _readUniswapQuote(10 ** inBase);
    }
```

이 함수는 Uniswap에서 두 토큰 간의 현재 환율을 반환합니다. 가격 데이터를 제공하기 때문에 매우 중요합니다. 하지만 인수에 큰 실수가 있습니다.

`inBase` 변수가 어떤지 확인할 수 있습니다:

```solidity
inBase = 10 ** (inCur.decimals());
```

`read()` 함수의 올바른 버전은 `_readUniswapQuote()`에 대한 인수로 `inBase`를 직접 사용해야 합니다. `inBase`는 이미 `10 ** (inCur.decimals())`로 계산되었습니다.

`_readUniswapQuote()`에 잘못된 값이 전달되어 결과가 엄청날 수 있습니다. 어떤 경우에는 오버플로가 발생하여 함수가 전혀 작동하지 않을 수도 있고, 어떤 경우에는 완전히 잘못된 값을 반환할 수도 있습니다.

## 권장 사항

추가 확장 없이 `inBase`를 함수에 직접 전달하십시오:

```solidity
    function read() external view override returns (uint256) {
        return _readUniswapQuote(inBase);
    }
```

# [C-02] 누구나 클러스터 주소를 업데이트할 수 있음

**영향**: 높음 (High), 악의적인 사용자가 매우 중요한 기능에 접근할 수 있기 때문입니다.

**가능성**: 높음 (High), 접근 제어가 부족하여 이 기능을 악용하기가 매우 쉽습니다.

## 설명

`MagnetarV2.sol`에는 `setCluster()` 함수가 있습니다:

```solidity
    function setCluster(ICluster _cluster) external {
        if (address(_cluster) == address(0)) revert NotValid();
        emit ClusterSet(cluster, _cluster);
        cluster = _cluster;
    }
```

이 함수는 매우 중요한 클러스터 주소를 업데이트합니다. 하지만 이 함수에는 접근 제어가 없습니다. 소유자만 이 함수를 호출할 수 있어야 합니다. 하지만 현재는 누구나 `setCluster()`를 호출하여 클러스터 주소를 변경할 수 있으며, 이는 프로토콜에 큰 해를 끼칠 수 있습니다.

## 권장 사항

`setCluster()` 함수에 `onlyOwner` 수정자를 추가하십시오:

```solidity
    function setCluster(ICluster _cluster) external onlyOwner {
        if (address(_cluster) == address(0)) revert NotValid();
        emit ClusterSet(cluster, _cluster);
        cluster = _cluster;
    }
```

# [C-03] `_exitPositionAndRemoveCollateral`의 불충분한 검사로 인해 승인이 악용될 수 있음

## 심각도

**영향**: 높음 (High), 승인 악용으로 인한 제공된 유동성 토큰 손실

**가능성**: 높음 (High), 누구나 호출할 수 있음

## 설명

MagnetarMarketModule의 `_exitPositionAndRemoveCollateral` 함수는 여러 작업을 수행합니다. 잠긴 유동성 포지션을 잠금 해제한 다음 시장에서 자산을 제거합니다. 여기서 관심 있는 첫 두 작업은 `exit` 및 `unlock`입니다. 호출자는 이러한 작업이 실행될지 여부를 결정하는 함수 호출에 부울 값을 제공합니다. 이러한 부울 값은 `removeAndRepayData.exitData.exit` 및 `removeAndRepayData.unlockData.unlock`에 저장됩니다.

문제는 전달된 값의 특정 조합에 대해 사용자가 Magnetar 계약에 승인을 준 다른 사용자로부터 유동성 토큰을 가져올 수 있다는 것입니다. Magnetar 계약은 사용자 상호 작용을 위한 도우미 계약이므로 사용자가 이미 토큰 및 ERC721 포지션에 대한 허용량을 Magnetar에 부여했다고 가정합니다.

공격자가 `removeAndRepayData.exitData.exit`를 false로 설정하고 `removeAndRepayData.unlockData.unlock`을 true로 설정하는 시나리오를 살펴보겠습니다.

첫 번째 bool이 false이므로 함수는 `exit` 작업을 수행하지 않습니다.

```solidity
uint256 tOLPId = 0;
        if (removeAndRepayData.exitData.exit) { ... }
```

`unlock` 작업에서 잠금 해제해야 하는 tokenId가 설정됩니다. `exit` 작업을 건너뛰므로 `tOLPId`에 저장된 값은 위에 표시된 대로 여전히 0입니다.

```solidity
if (removeAndRepayData.unlockData.tokenId != 0) {
                if (tOLPId != 0) {
                    if (tOLPId != removeAndRepayData.unlockData.tokenId)
                        revert tOLPTokenMismatch();
                }
                tOLPId = removeAndRepayData.unlockData.tokenId;
            }
```

여기서 공격자는 희생자의 토큰 ID를 `removeAndRepayData.unlockData.tokenId` 필드에 설정합니다. 0이 아니므로 내부 if 절에 도달합니다. `exit` 작업을 건너뛰었기 때문에 `tOLPId`는 여전히 0이므로 if 절을 건너뛰고 `tOLPId`는 `removeAndRepayData.unlockData.tokenId`로 설정됩니다.

문제는 호출자가 실제로 이 tokenId를 소유하고 있는지 확인하는 절차가 없다는 것입니다! 다음 줄에서 이 tokenId에 대해 `unlock`이 호출되고 수익금은 공격자인 `user`에게 전송됩니다.

```solidity
 ITapiocaOptionLiquidityProvision(
                removeAndRepayData.unlockData.target
            ).unlock(tOLPId, externalData.singularity, user);
```

희생자가 과거에 자신의 유동성 토큰에 대한 승인을 Magnetar에 주었다면(Magnetar와 상호 작용한 적이 있는 경우 가능성이 높음), 토큰을 잃고 유동성이 잠금 해제되어 공격자에게 전송됩니다. 따라서 공격자는 누구의 유동성 토큰이든 가로챌 수 있습니다. `unlock` 함수에서 볼 수 있듯이 Magnetar가 승인을 가지고 있는 한 보유자의 지갑에서 토큰이 직접 소각되므로 Magnetar 계약은 이 ERC721 토큰을 소유할 필요조차 없습니다.

```solidity
require(
            _isApprovedOrOwner(msg.sender, _tokenId),
            "tOLP: not owner nor approved"
        );

        _burn(_tokenId);
```

## 권장 사항

`exit` 작업에 있는 것과 유사한 사용자 검사를 추가하십시오.

```solidity
address ownerOfTapTokenId = IERC721(oTapAddress).ownerOf(
                removeAndRepayData.unlockData.tokenId
            );

            if (ownerOfTapTokenId != user && ownerOfTapTokenId != address(this))
                revert NotValid();
```

# [H-01] `StargateLbpHelper.participate()`가 잘못된 주소로 토큰을 보냄

## 심각도

**영향:** 중간(Med), 잘못 전송된 토큰이 `msg.sender`에게 전송되면 호출자가 이에 대한 제어 권한이 없는 경우(호출자가 다중 서명 지갑 계약을 사용하는 경우) 복구할 수 없을 수 있으므로

**가능성:** 높음 (High), `StargateLbpHelper`가 잘못된 주소로 stargate 스왑을 수행하므로

## 설명

`StargateLbpHelper.participate()`는 Balancer Liquidity Bootstrapping Pool에 예치하기 위해 Stargate를 통해 호스트 체인의 `StargateLbpHelper`로 토큰을 보내기 위해 비 호스트 체인에서 호출됩니다.

이것은 `participate()`의 문제로, 호스트 체인의 `StargateLbpHelper` 계약 대신 대상 주소 `msg.sender`로 Stargate `router.swap()`을 수행합니다.

```Solidity
    function participate(
        StargateData calldata stargateData,
        ParticipateData calldata lbpData
    ) external payable nonReentrant {
        IERC20 erc20 = IERC20(stargateData.srcToken);

        // retrieve source token from sender
        erc20.safeTransferFrom(msg.sender, address(this), stargateData.amount);

        // compute min amount to be received on destination
        uint256 amountWithSlippage = stargateData.amount -
            ((stargateData.amount * stargateData.slippage) /
                SLIPPAGE_PRECISION);

        // approve token for Stargate router
        erc20.safeApprove(address(router), stargateData.amount);

        // send over to another layer using the Stargate router
        router.swap{value: msg.value}(
            stargateData.dstChainId,
            stargateData.srcPoolId,
            stargateData.dstPoolId,
            payable(msg.sender), //refund address
            stargateData.amount,
            amountWithSlippage,
            IStargateRouterBase.lzTxObj({
                dstGasForCall: 0,
                dstNativeAmount: 0,
                dstNativeAddr: "0x0"
            }),
            abi.encodePacked(msg.sender), //@audit this should be a parameter to `StargateLbpHelper` address on host chain
            abi.encode(lbpData, msg.sender)
        );
    }
```

## 권장 사항

`participate()`가 호스트 체인의 `StargateLbpHelper` 수신 주소를 전달하도록 허용하십시오.

`StargateLbpHelper`는 `TapiocaDeployer`를 통해 `CREATE2`를 사용하여 배포되므로 계약은 호스트 체인과 비 호스트 체인에서 동일한 주소를 갖지 않습니다. 이는 `StargateLbpHelper`의 생성자 매개변수가 서로 다르기 때문에 CREATE2가 생성자 매개변수를 포함하는 creationcode를 기반으로 주소를 미리 계산하기 때문에 배포된 주소가 다르게 됩니다. 따라서 대상 `StargateLbpHelper` 주소에 대해 매개변수를 사용하고 하드코딩하지 않는 것이 중요합니다.

# [H-02] `StargateLbpHelper.sgReceive()`가 영구적인 오류를 만나 수신된 토큰이 계약에 갇힐 수 있음

## 심각도

**영향:** 높음 (High), 토큰이 계약에 갇히게 되므로

**가능성:** 중간 (Medium), 영구적인 오류에 대해서만 발생하므로

## 설명

`StargateLbpHelper.sgReceive()`는 Stargate 스왑을 통해 체인 간에 전송된 토큰을 받아 LBP 풀을 통해 스왑합니다. `StargateLbpHelper`에는 소유자가 stargate 스왑 실패 시 실행을 재시도할 수 있도록 하는 `retryRevert()`가 있습니다.

그러나 실패가 영구적인 오류로 인한 경우 `retryRevert()`는 복구에 도움이 되지 않습니다. 그런 일이 발생하면 수신된 토큰은 `StargateLbpHelper` 계약에 갇혀 검색할 방법이 없습니다.

## 권장 사항

토큰 수령자가 토큰을 회수하도록 허용하거나 영구적인 오류가 발생할 때 수령자에게 전송하도록 하십시오.

# [H-03] `StargateLbpHelper.participate()`를 호출하는 다중 서명 지갑으로 인해 수신된 토큰이 도난당할 수 있음

## 심각도

**영향:** 높음 (High), 대상 체인에서 수신된 토큰이 손실되므로

**가능성:** 중간 (Medium), 호출자가 다중 서명 지갑 계약일 때 발생합니다.

## 설명

`StargateLbpHelper.participate()`는 호스트 체인으로의 크로스 체인 전송을 수행한 다음 Balancer Liquidity Bootstrapping Pool을 사용하여 스왑하는 데 사용됩니다.

문제는 대상 체인에서의 LBP 스왑 수신 주소가 `msg.sender`(`participate()`에 의해 전달됨)로 하드코딩되어 있다는 것입니다.

`msg.sender`가 다중 서명 지갑 계약인 경우 이는 문제가 됩니다. 지갑 소유자가 대상 체인에서 `msg.sender`와 동일한 주소에 대한 제어 권한이 없어 수신된 토큰이 손실될 수 있기 때문입니다(수령자가 자금을 반환할 의사가 없는 경우).

최악의 경우 토큰이 배포되지 않은 주소로 전송될 수 있으며 공격자는 대상 체인에서 `msg.sender` 주소를 제어하여 수신된 토큰을 훔칠 기회를 잡을 수 있습니다([Wintermute 해킹 기사](https://rekt.news/wintermute-rekt/) 참조).

```Solidity
    function participate(
        StargateData calldata stargateData,
        ParticipateData calldata lbpData
    ) external payable nonReentrant {
        ...
        router.swap{value: msg.value}(
            stargateData.dstChainId,
            stargateData.srcPoolId,
            stargateData.dstPoolId,
            payable(msg.sender),
            stargateData.amount,
            amountWithSlippage,
            IStargateRouterBase.lzTxObj({
                dstGasForCall: 0,
                dstNativeAmount: 0,
                dstNativeAddr: "0x0"
            }),
            abi.encodePacked(msg.sender),
            abi.encode(lbpData, msg.sender) //@audit receiver address on destination chain should be a parameter and not msg.sender
        );


    function sgReceive(
        uint16,
        bytes memory,
        uint256,
        address token,
        uint256 amountLD,
        bytes memory payload
    ) external {
        ...
        // decode payload
        (ParticipateData memory data, address receiver) = abi.decode( //@audit receiver is set to msg.sender
            payload,
            (ParticipateData, address)
        );
       ...
        IBalancerVault.FundManagement memory fundManagement = IBalancerVault
            .FundManagement({
                sender: address(this),
                recipient: payable(receiver), //@audit token is sent to receiver (which is msg.sender on src chain)
                fromInternalBalance: false,
                toInternalBalance: false
            });

        IERC20(data.assetIn).approve(address(lbpVault), 0);
        IERC20(data.assetIn).approve(address(lbpVault), amountLD);
        lbpVault.swap(
            singleSwap,
            fundManagement,
            data.minAmountOut,
            (data.deadline != 0 ? data.deadline : block.timestamp)
        );
    }

```

## 권장 사항

호출자가 LBP 스왑을 위해 `receiver` 주소를 전달하도록 허용하십시오.

# [H-04] `Seer`에서 더 높은 가격만 사용하면 가격 조작에 취약해짐

## 심각도

**영향:** 높음 (High), 가격 조작으로 인해 자금 손실 발생

**가능성:** 중간 (Medium), TWAP 기간이 너무 짧거나 유동성이 넓은 범위에 분산되어 있지 않을 때 Uniswap 풀이 가격 조작될 수 있음

## 설명

`Seer.sol`은 Chainlink와 Uniswap V3 TWAP 오라클의 조합인 Angle Protocol의 `OracleMulti.sol`을 사용합니다. `OracleMulti.sol`의 설계는 Chainlink와 Uniswap 가격을 모두 반환하고 프로토콜이 가장 유리한 가격을 선택하도록 하는 것입니다. 예를 들어 Angle은 담보를 사용하는 발행(mint) 트랜잭션에 대해 Chainlink와 Uniswap 중 더 낮은 가격을 사용합니다. ([Angle의 오라클 설계](https://docs.angle.money/deprecated/overview/other-aspects/oracles#angles-oracle-design) 참조)

문제는 `Seer.sol`이 `get()`, `peek()`, `peekSpot()`과 같은 가격 검색 함수에 더 높은 가격만 사용한다는 것입니다. 더 낮은 가격은 무시하고 전혀 반환하지 않습니다. 즉, 프로토콜에 불리한 가격을 사용할 수 있습니다.

```Solidity
    function get(
        bytes calldata
    ) external virtual nonReentrant returns (bool success, uint256 rate) {
        // Checking whether the sequencer is up
        _sequencerBeatCheck();

        //@audit only the higher price is used, while ignoring the lower (first returned variable)
        (, uint256 high) = _readAll(inBase);
        return (true, high);
    }
```

이로 인해 `Seer.sol`을 사용하는 계약은 가격 조작 공격에 취약해집니다.

예를 들어 oTAP 옵션 행사를 위한 지불 토큰의 가격을 검색할 때 더 낮은 가격을 사용하는 것이 더 유리합니다. 더 높은 가격을 사용함으로써 공격자는 Uniswap V3 풀의 가격을 조작하여 지불 토큰의 가격을 인위적으로 부풀릴 수 있습니다. 즉, 공격자는 훨씬 적은 양의 지불 토큰으로 옵션을 행사할 수 있게 됩니다.

## 권장 사항

`Seer.sol`의 가격 함수에 대해 더 높은 값과 더 낮은 값을 모두 반환하고 프로토콜에서 적절한 가격을 사용하십시오.

# [H-05] TOFT 차감에 대한 잘못된 주소

## 심각도

**영향**: 높음 (High), 트랜잭션이 되돌려지거나 잘못된 계정에서 차감됨

**가능성**: 중간 (Medium), 호출자가 화이트리스트된 릴레이어일 때만 발생

## 설명

Magnetar 계약은 `TOFT_SEND_FROM` 작업 ID를 처리하여 TOFT 토큰의 크로스 체인 전송을 트리거합니다. 먼저 사용자의 호출에서 매개변수를 추출한 다음 실제 호출이 수행됩니다.

```solidity
(
                    address from,
                    uint16 dstChainId,
                    bytes32 to,
                    uint256 amount,
                    ICommonOFT.LzCallParams memory lzCallParams
                ) = abi.decode(
                        _action.call[4:],
                        (
                            address,
                            uint16,
                            bytes32,
                            uint256,
                            (ICommonOFT.LzCallParams)
                        )
                    );
                _checkSender(from);

                ISendFrom(_action.target).sendFrom{value: _action.value}(
                    msg.sender,
                    dstChainId,
                    to,
                    amount,
                    lzCallParams
                );
```

스니펫에서 볼 수 있듯이 중요한 주소는 `msg.sender`와 `from` 두 가지입니다. `from`은 토큰이 차감되어야 하는 주소입니다. `msg.sender`는 특별한 권한을 가질 수 있으며 단순히 함수의 호출자 역할만 할 수 있는 주소입니다. 이는 `checkSender` 함수의 구현에 의해 더욱 뒷받침됩니다.

```solidity
function _checkSender(address _from) internal view {
        if (_from != msg.sender && !cluster.isWhitelisted(0, msg.sender))
            revert NotAuthorized();
    }
```

보시다시피 `msg.sender`와 `from`은 동일한 사람이거나 `msg.sender`는 특별한 권한이 있는 화이트리스트 주소입니다.

그러나 `sendFrom` 호출에서 토큰이 공제될 때 함수는 실수로 `from` 주소 대신 `msg.sender` 주소에서 토큰을 공제합니다. msg.sender가 화이트리스트 릴레이어일 때 문제가 되며 잘못된 계정에서 토큰이 차감됩니다. 이 계정에 토큰이 없으면 트랜잭션이 되돌려지고 화이트리스트 역할이 쓸모없게 됩니다.

이 문제는 여러 인스턴스에 존재합니다:

- [ ] action.id `TOFT_WRAP`
- [x] action.id `TOFT_SEND_FROM`
- [ ] action.id `YB_DEPOSIT_ASSET`
- [ ] action.id `MARKET_ADD_COLLATERAL`
- [ ] action.id `MARKET_BORROW`
- [ ] action.id `MARKET_LEND`
- [ ] action.id `MARKET_REPAY`
- [ ] action.id `TOFT_SEND_AND_BORROW`
- [ ] action.id `TOFT_SEND_AND_LEND`
- [ ] action.id `TOFT_DEPOSIT_TO_STRATEGY`
- [ ] action.id `TOFT_RETRIEVE_FROM_STRATEGY`

## 권장 사항

`msg.sender`는 단지 화이트리스트된 릴레이어일 수 있으므로 `msg.sender` 대신 `from` 계정에서 토큰을 차감하십시오.

# [H-06] Magnetar의 잘못된 레버러징 작업 호출

## 심각도

**영향**: 중간 (Medium), 고장 난 기능

**가능성**: 높음 (High), 고장 난 기능은 절대 작동하지 않음

## 설명

Magnetar 계약에는 Singularity 및 BigBang 시장과 상호 작용하는 도우미 함수가 있어 추가 기능을 위해 동시에 여러 복합 작업을 수행합니다. BigBang 및 Singularity 시장에서 지원하는 작업 중 하나는 담보 토큰을 사고파는 기능입니다. 이는 아래 함수 프로토타입과 같이 BigBang 및 Singularity 시장에서 지원됩니다.

```solidity
function buyCollateral(
        address from,
        uint256 borrowAmount,
        uint256 supplyAmount,
        bytes calldata data
    ){...}
function sellCollateral(
        address from,
        uint256 share,
        bytes calldata data
    ){...}
```

문제는 Magnetar 계약이 현재 버전의 계약과 작동하지 않는 이전 버전의 프로토콜 인터페이스를 사용한다는 것입니다. 이는 Magnetar 계약에서 보이는 호출에서 분명합니다.

```solidity
IMarket(_action.target).buyCollateral(
                    from,
                    borrowAmount,
                    supplyAmount,
                    minAmountOut,
                    swapper,
                    dexData
                );

IMarket(_action.target).sellCollateral(
                    from,
                    share,
                    minAmountOut,
                    swapper,
                    dexData
                );
```

함수 호출에서 볼 수 있듯이 호출자는 5개의 값을 전달하지만 함수는 실제로 함수 프로토타입에서 3개의 값만 예상하므로 기능이 고장 납니다.

`MagnetarV2.sol`에는 이 문제의 두 가지 인스턴스가 있습니다.

- [ ] action.id `MARKET_BUY_COLLATERAL`
- [ ] action.id `MARKET_SELL_COLLATERAL`

## 권장 사항

시장의 현재 버전을 준수하도록 함수 호출을 변경하십시오.
# [H-07] `StargateLbpHelper.participate()`에서 Stargate 수수료의 과소 지불/과다 지불 발생

## 심각도

**영향:** 중간 (Medium), Stargate 수수료를 과소/과다 지불하게 되므로

**가능성:** 높음 (High), 항상 발생함

## 설명

`StargateLbpHelper.participate()`는 LBP 스왑을 위해 토큰을 호스트 체인으로 보내기 위해 Stargate `router.swap()`을 사용합니다.

그러나 `_lzTxParams` 매개변수는 0으로 하드코딩되어 있어 Stargate가 크로스 체인 스왑 수수료를 과소/과다 청구하게 됩니다. 이는 Stargate가 크로스 체인 메시징에 LayerZero를 사용하기 때문에 발생하며, LayerZero는 `dstGasForCall`을 기반으로 기본 LZ 수수료를 계산합니다. `dstGasForCall == 0`일 때 대상 체인 실행에 대해 200,000 가스의 기본값을 기준으로 수수료를 청구합니다.

즉, 대상 체인 가스 소비가 200,000 가스 미만인 경우 `participate()`는 Stargate 수수료를 과다 지불하게 됩니다. 반면 대상 체인 가스 소비가 200,000 가스 초과인 경우 과소 지불하게 되고 OOG(Out Of Gas) 오류로 인해 대상 체인 실행이 실패하게 됩니다.

```Solidity
    function participate(
        StargateData calldata stargateData,
        ParticipateData calldata lbpData
    ) external payable nonReentrant {
        IERC20 erc20 = IERC20(stargateData.srcToken);

        // retrieve source token from sender
        erc20.safeTransferFrom(msg.sender, address(this), stargateData.amount);

        // compute min amount to be received on destination
        uint256 amountWithSlippage = stargateData.amount -
            ((stargateData.amount * stargateData.slippage) /
                SLIPPAGE_PRECISION);

        // approve token for Stargate router
        erc20.safeApprove(address(router), stargateData.amount);

        // send over to another layer using the Stargate router
        router.swap{value: msg.value}(
            stargateData.dstChainId,
            stargateData.srcPoolId,
            stargateData.dstPoolId,
            payable(msg.sender), //refund address
            stargateData.amount,
            amountWithSlippage,
            //@audit the _lzTxParams parameter should not be hardcoded to zero
            IStargateRouterBase.lzTxObj({
                dstGasForCall: 0,
                dstNativeAmount: 0,
                dstNativeAddr: "0x0"
            }),
            abi.encodePacked(msg.sender), // StargateLbpHelper.sol destination address
            abi.encode(lbpData, msg.sender)
        );
    }
```

## 권장 사항

`_lzTxParams` 매개변수를 하드코딩하는 대신 전달하십시오.

# [M-01] `TickMath` 및 `FullMath` 라이브러리에 `unchecked` 누락

## 심각도

**영향:** 중간 (Medium), 스왑이 되돌려지게(revert) 하므로.

**가능성:** 중간 (Medium), 팬텀 오버플로(phantom overflow)가 있을 때 발생하므로.

## 설명

`FullMath`와 `TickMath` 모두 `unchecked`가 누락되어 있어 팬텀 오버플로에서 잘못 되돌려집니다. 이 라이브러리들은 UniswapV3에 문서화된 대로 중간값이 256비트를 초과하는 경우에도 곱셈과 나눗셈을 허용하여 "팬텀 오버플로"를 처리하도록 되어 있습니다. 원래 UniswapV3 코드에서는 solidity 버전이 0.8.0 미만이므로 오버플로 시 되돌리지 않아 unchecked가 사용되지 않습니다.

`TickMath`는 `TickMath`를 활용하는 `OracleLibrary`를 사용하는 `UniswapV3Swapper`에 영향을 미칩니다. `FullMath`에 대해서도 동일한 문제가 있으며 이는 `Seer`에 영향을 미칩니다.

## 권장 사항

두 라이브러리 모두에 `unchecked`를 추가하십시오.

# [M-02] 제거된 자산/담보를 인출할 때 Magnetar `exitPositionAndRemoveCollateral()`이 실패할 수 있음

## 심각도

**영향:** 중간 (Medium), 함수가 되돌려지게 하므로
**가능성:** 중간 (Medium), 사용자가 YieldBox에서 기본 담보를 인출하려고 할 때 발생하므로

## 설명

`MagnetarMarketModule._exitPositionAndRemoveCollateral()`에서 사용자는 `removeAndRepayData.removeCollateralFromBB = true` 및 `removeAndRepayData.collateralWithdrawData.withdraw = true`를 설정하여 BigBang에서 담보 지분을 제거한 다음 해당 지분을 사용하여 YieldBox에서 기본 담보를 인출할 수 있습니다.

제거할 기본 담보의 양은 `removeAndRepayData.collateralAmount` 매개변수로 표시됩니다.

그러나 실제 인출되는 담보의 양은 `removeAndRepayData.collateralAmount` 매개변수가 제공하는 것보다 적을 수 있습니다. 이는 `bigBang.removeCollateral()`이 `collateralShare` 매개변수를 사용하기 때문에 `yieldBox.toShare()` 변환이 필요하기 때문입니다.

이러한 경우 사용자가 제공한 `removeAndRepayData.collateralAmount`가 BigBang에서 제거된 것보다 많기 때문에 `_withdraw()`가 실패합니다.

```Solidity
        if (removeAndRepayData.removeCollateralFromBB) {
            //@audit collateralShare could be rounded down here after toShare() conversion
            uint256 collateralShare = yieldBox.toShare(
                bigBang.collateralId(),
                removeAndRepayData.collateralAmount,
                false
            );
            address removeCollateralTo = removeAndRepayData
                .collateralWithdrawData
                .withdraw
                ? address(this)
                : user;

            //@audit if collateralShare is rounded down, amount removed will be less than
            //       user provided removeAndRepayData.collateralAmount
            bigBang.removeCollateral(user, removeCollateralTo, collateralShare);

            //withdraw
            if (removeAndRepayData.collateralWithdrawData.withdraw) {
                bytes memory withdrawCollateralBytes = abi.encode(
                    removeAndRepayData
                        .collateralWithdrawData
                        .withdrawOnOtherChain,
                    removeAndRepayData.collateralWithdrawData.withdrawLzChainId,
                    LzLib.addressToBytes32(user),
                    removeAndRepayData
                        .collateralWithdrawData
                        .withdrawAdapterParams
                );
                _withdraw(
                    address(this),
                    withdrawCollateralBytes,
                    singularity,
                    yieldBox,
                    //@audit when amount from removeCollateral() < removeAndRepayData.collateralAmount
                    //      _withdraw() will fail as it tries to withdraw more than what was removed above
                    removeAndRepayData.collateralAmount,
                    true,
                    valueAmount,
                    removeAndRepayData.collateralWithdrawData.unwrap
                );
            }
        }
        _revertYieldBoxApproval(address(bigBang), yieldBox);
    }
```

`removeAndRepayData.removeAssetFromSGL = true`일 때도 동일한 문제가 적용됩니다.
유사하게 `_depositRepayAndRemoveCollateralFromMarket()`에 대해서도 마찬가지입니다.

## 권장 사항

YieldBox에서 인출할 금액은 `yieldBox.toShare()`가 반환한 `collateralShare`에서 파생되어야 합니다.

# [M-03] `TapOracle`에 대한 시퀀서 가동 시간 확인 부족으로 인해 오래된 가격이 발생할 수 있음

## 심각도

**영향:** 중간 (Medium), 오라클 가격이 오래될 것임
**가능성:** 중간 (Medium), 시퀀서 다운타임 기간 동안 발생

## 설명

`TapOracle`은 최소 4시간(`FETCH_TIME` 기준) 간격으로 UniswapV3 풀에서 3개의 TWAP 가격 평균을 가져옵니다. 3개의 TWAP 가격은 `lastPrices[]`에 저장되고 TAP 가격을 검색하기 위해 `get()`이 호출될 때 업데이트됩니다.

문제는 L2 시퀀서가 장기간 다운되면 `get()`을 통한 오라클과의 상호 작용이 없어 `lastPrices[]`가 최신 가격으로 업데이트되지 않는다는 것입니다. 이로 인해 시퀀서가 복구될 때 `TapOracle`이 오래된 가격을 반환하게 됩니다.

## 권장 사항

`get()` 함수에 `_sequencerBeatCheck();`를 추가하십시오. 이는 시퀀서가 다운타임에서 복구될 때 `TapOracle`이 최신 가격으로 업데이트될 수 있도록 유예 기간을 제공하기 위함입니다.

시퀀서 복구 시 3개의 `lastPrices[]`가 모두 업데이트될 수 있는 충분한 시간을 허용하기 위해 `FETCH_TIME`은 유예 기간의 최대 1/3이어야 합니다.

# [M-04] `UniswapV3Swapper`는 모든 토큰 쌍에 대해 동일한 `poolFee`를 사용해서는 안 됨

## 심각도

**영향:** 중간 (Medium), 풀 수수료가 잘못되면 스왑 실패
**가능성:** 중간 (Medium), 풀 수수료가 3000(기본값)이 아닌 스왑에서만 발생

## 설명

UniswapV3Swapper는 모든 토큰 쌍에 대해 동일한 `poolFee`를 사용합니다.

문제는 아래 UniswapV3 `SwapRouter.getPool()`에 표시된 것처럼 Uniswap V3 풀 주소가 토큰 주소와 풀 수수료 모두에 연결되어 있다는 것입니다. 즉, 스왑할 토큰 쌍을 구성된 `poolFee`로 사용할 수 없는 경우 스왑이 실패할 수 있습니다.

```
    /// @dev Returns the pool for the given token pair and fee. The pool contract may or may not exist.
    function getPool(
        address tokenA,
        address tokenB,
        uint24 fee
    ) private view returns (IUniswapV3Pool) {
        return IUniswapV3Pool(PoolAddress.computeAddress(factory, PoolAddress.getPoolKey(tokenA, tokenB, fee)));
    }
```

## 권장 사항

`poolFee`를 매개변수로 전달하여 스왑에 올바른 풀이 사용되도록 허용하십시오.

# [M-05] 수신된 ETH를 YieldBox에 예치할 때 `UniswapV3Swapper` 및 `UniswapV2Swapper`가 실패함

## 심각도

**영향:** 중간 (Medium), 스왑 실패를 유발하므로

**가능성:** 중간 (Medium), YieldBox에 WETH를 예치할 때 발생하므로

## 설명

`UniswapV3Swapper.swap()`이 `depositToYb = true` 및 `tokenOut = address(0)`(WETH)로 호출되면 입력 토큰을 WETH로 교환한 다음 ETH를 YieldBox에 예치하기 전에 WETH를 언랩합니다.

그러나 `yieldBox.depositAsset()`이 언랩된 ETH를 예치하는 데 사용되는데, 이는 잘못된 것이며 기본 자산의 예치를 지원하지 않으므로 되돌려질(revert) 것입니다.

```Solidity
        amountOut = _swap(
            tokenOut,
            params,
            swapData.yieldBoxData.depositToYb,
            to
        );

        if (swapData.yieldBoxData.depositToYb) {
            if (tokenOut != address(0)) {
                _safeApprove(tokenOut, address(yieldBox), amountOut);
            }
            (, shareOut) = yieldBox.depositAsset(
                swapData.tokensData.tokenOutId,
                address(this),
                to,
                amountOut,
                0
            );
        }
```

동일한 문제가 `UniswapV2Swapper`에도 적용됩니다.

## 권장 사항

`depositToYb = true`일 때 WETH를 언랩하지 마십시오. 그러면 언랩 및 재랩핑 없이 WETH가 YieldBox에 직접 예치됩니다.
또는 `tokenOut`이 address(0)일 때 `yieldBox.depositETHAsset()`을 대신 사용하십시오. 하지만 이렇게 하면 언랩 및 재랩핑이 수행됩니다.

# [M-06] YieldBox에 예치/인출할 때 tokenIn/tokenOut과 함께 `buildSwapData()`를 사용하면 스왑이 실패함

## 심각도

**영향:** 중간 (Medium), `swap()`이 실패하므로

**가능성:** 중간 (Medium), yieldbox 예치와 함께 스왑을 사용할 때만 발생함.

## 설명

`buildSwapData(address tokenIn, address tokenOut, ...)`가 `SwapData`를 채우는 데 사용되면 `tokenInId`와 `tokenOutId`가 모두 0으로 설정됩니다.

그러나 `depositToYb = true`로 `swap()`을 사용하면 `tokenOutId`를 기반으로 YieldBox에 예치합니다. `tokenOutId`가 0이므로 실패합니다.

`withdrawToYb = true`의 경우에도 동일한 문제가 발생하며 `tokenInId`가 0이므로 실패합니다.

## 권장 사항

`buildSwapData(address tokenIn, address tokenOut, ...)`의 경우 `withdrawFromYb` 및 `depositToYb`를 `false`로 설정하고 지원되지 않으므로 이러한 매개변수를 제거하십시오.

# [M-07] `FETCH_TIME`이 너무 커서 가격이 오래될 수 있음

## 심각도

**영향**: 높음 (High), 오래된 가격을 반환할 수 있기 때문입니다

**가능성**: 낮음 (Low), 변동성이 큰 시장에서 발생할 수 있습니다

## 설명

`TapOracle.sol` 계약의 `FETCH_TIME` 값은 4시간으로 설정되어 있으며, 이는 오라클 가격 업데이트 간의 최소 간격을 결정합니다.

```solidity
uint32 public FETCH_TIME = 4 hours;
```

하지만 이 간격은 너무 길어 가격이 오래될 수 있습니다. 변동성이 큰 시장에서는 가격 데이터가 빠르게 오래될 수 있습니다. 4시간 간격은 중요한 시장 움직임을 포착하기에 충분하지 않을 수 있으며, 오라클이 오래되거나 부정확한 데이터를 제공할 가능성이 있습니다.

NatSpec 및 문서에 설명된 대로 정확히 1시간으로 변경하십시오.

## 권장 사항

`FETCH_TIME`을 1시간으로 변경하십시오:

```solidity
uint32 public FETCH_TIME = 1 hours;
```

# [M-08] StargateLbpHelper.sol에서 `quoteLayerZeroFee()` 구현 누락

## 심각도

**영향**: 낮음 (Low), 사용자가 이 구현 없이도 `participate()`를 계속 사용할 수 있기 때문입니다.

**가능성**: 높음 (High), 이 함수에 대한 구현이 없기 때문입니다.

## 설명

크로스 체인 전송을 원할 때 `participate()` 함수를 사용합니다. 이 함수는 Stargate 라우터를 사용하여 `swap` 메서드를 호출합니다:

```solidity
        router.swap{value: msg.value}(
            stargateData.dstChainId,
            stargateData.srcPoolId,
            stargateData.dstPoolId,
            payable(msg.sender), //refund address
            stargateData.amount,
            amountWithSlippage,
            IStargateRouterBase.lzTxObj({
                dstGasForCall: 0,
                dstNativeAmount: 0,
                dstNativeAddr: "0x0"
            }),
            abi.encodePacked(msg.sender), // StargateLbpHelper.sol destination address
            abi.encode(lbpData, msg.sender)
        );
```

`fee`를 지불하기 위해 `msg.value`를 보냅니다:

```solidity
swap{value:msg.value}
```

Stargate [문서](https://stargateprotocol.gitbook.io/stargate/developers/how-to-swap)에서 수수료를 계산하려면 `quoteLayerZero()`를 호출해야 함을 알 수 있습니다.

> `swap()`에 필요한 기본 가스 요금의 경우 Router.sol 계약에서 `quoteLayerZero()`를 호출하여 msg.value로 보내야 하는 금액을 가져와야 합니다.

하지만 해당 함수가 누락되었습니다. 이로 인해 전송이 성공하기 위해 얼마의 수수료를 보내야 하는지 알 수 없습니다. 필요 이상으로 보내면 환불되지만, 필요보다 적게 보내면 크로스 체인 전송이 실패합니다.

## 권장 사항

`quoteLayerZeroFee()` 함수를 구현하십시오. 자세한 내용은 [문서](https://stargateprotocol.gitbook.io/stargate/developers/cross-chain-swap-fee)를 참조하십시오.

# [M-09] `_withdrawToChain()`이 LZ 호출을 위한 초과 가스를 잘못된 주소로 환불함

## 심각도

**영향:** 중간 (Medium), 환불된 가스가 손실되므로

**가능성:** 중간 (Medium), 크로스 체인 인출을 수행할 때 특정 경우에 발생

## 설명

`MagnetarMarketModule._withdrawToChain()`은 다른 체인으로의 인출을 허용하므로 초과 가스가 소스 체인의 주소로 환불될 수 있도록 `refundAddress`를 지정해야 합니다. 그러나 `refundAddress`는 모든 경우를 처리하지 않기 때문에 잘못 설정되어 있습니다.

```Solidity
        // build LZ params
        bytes memory _adapterParams;
        ICommonOFT.LzCallParams memory callParams = ICommonOFT.LzCallParams({
            refundAddress: msg.value == gas ? refundAddress : payable(this),  //@audit this does not handle all cases
            zroPaymentAddress: address(0),
            adapterParams: ISendFrom(address(asset)).useCustomAdapterParams()
                ? adapterParams
                : _adapterParams
        });
```

`_withdrawToChain()`이 호출될 수 있는 4가지 다른 경우를 확인했으며 다음은 경우 2/3/4가 잘못되었음을 보여줍니다.

### 사례 1: 직접 호출

`gas == msg.value`이고 `refundAddress`는 매개변수에서 가져옵니다.
사용자 오류가 없다고 가정하면 이것은 정확합니다.

```
EOA -> MagnetarV2.withdrawToChain()
	-> MagnetarMarketModule._withdrawToChain() // delegatecall
```

### 사례 2: 크로스 체인 직접 호출

`gas == msg.value`이고 `refundAddress`는 BaseTOFTMarketDestinationModule.sol#L247로 인해 `payable(to)`가 됩니다.
`refundAddress`는 소스 체인에서 사용자가 제공한 주소여야 하므로 잘못된 것입니다.

```
LZ (dest) -> BaseTOFT._nonblockingLzReceive()
		-> BaseTOFTMarketDestinationModule.remove() //delegatecall
			-> MagnetarV2.withdrawToChain()
				-> MagnetarMarketModule._withdrawToChain() // delegatecall
```

### 사례 3: 크로스 체인 간접 호출

`gas == msg.value`이고 `refundAddress`는 MagnetarMarketModule.sol#L858로 인해 `payable(this)`가 됩니다.
`refundAddress`는 소스 체인에서 사용자가 제공한 주소여야 하므로 잘못된 것입니다.

```
LZ (dest) -> BaseTOFT._nonblockingLzReceive()
		-> BaseTOFTMarketDestinationModule.borrowInternal() //delegatecall
			-> MagnetarV2.depositAddCollateralAndBorrowFromMarket()
				-> MagnetarMarketModule.depositAddCollateralAndBorrowFromMarket() // delegatecall
					-> MagnetarMarketModule._withdraw()
						-> MagnetarMarketModule._withdrawToChain()
```

### 사례 4: burst()를 사용한 멀티콜

`gas == _action.value`이고 `refundAddress`는 MagnetarMarketModule.sol#L800으로 인해 `payable(this)`가 됩니다.
`refundAddress`는 `msg.sender`여야 하므로 잘못된 것입니다.

```

EOA -> MagnetarV2.burst()
	-> MagnetarV2.depositAddCollateralAndBorrowFromMarket()
		-> MagnetarMarketModule.depositAddCollateralAndBorrowFromMarket() // delegatecall
			-> MagnetarMarketModule._withdraw()
				-> MagnetarMarketModule._withdrawToChain()
```

## 권장 사항

`refundAddress`가 첫 번째 `MagnetarV2` 함수에서 매개변수로 전달되도록 허용하십시오. 언급했듯이 여러 경우가 있으므로 `gas` 또는 msg.value를 통해 추론하는 것보다 올바른 refundAddress를 결정하는 것이 더 쉽습니다.

크로스 체인 호출(사례 2 및 3)의 경우 `refundAddress`는 실제로 `payable(to)`가 아니라 소스 체인에서 사용자가 제공한 주소여야 합니다. 다른 체인으로 인출할 때 `payable(to)`는 대상 체인의 주소가 되며, 이는 소스 체인에서 사용자가 제어하는 주소가 아닐 수 있기 때문입니다(다중 서명 지갑의 경우).

# [M-10] Uniswap Oracl용 `TapOracle` TWAP 기간은 최소 30분이어야 함

## 심각도

**영향:** 높음 (High), oTAP 옵션 행사를 위한 자금 손실 비용 발생

**가능성:** 낮음 (Low), 유동성이 넓은 범위에 분산되어 있지 않을 때 발생할 수 있음(예: 프로토콜 출시 중)

## 설명

`TapOracle`은 TAP/USDC 풀에 대해 Uniswap V3 TWAP 오라클 기반의 단일 가격 피드를 사용합니다.

그러나 `TapOracle` 배포 스크립트는 Euler[1] 및 Uniswap[2]이 연구에서 사용하는 일반적인 1,800초(30분)보다 낮은 600초(10분)의 TWAP 기간을 나타냅니다. 이는 TAP/USDC 풀의 유동성이 넓은 범위에 분산되지 않은 경우 `TapOracle`이 가격 조작 위험을 더 높입니다. 이는 Uniswap 풀의 유동성이 제한된 프로토콜 출시 중에 발생할 수 있습니다.

혹자는 공격자가 가격을 한 블록 이상 조작된 상태로 유지하기 위해 (플래시 론 대신) 자신의 자본을 사용해야 하므로 차익 거래에 취약해져서 그러한 가격 조작이 공격자에게 위험하다고 주장할 수 있습니다. 그러나 공격자가 자본을 위험에 빠뜨리고 여러 블록을 기다린 Rari의 Fuse 해킹[3]에서 볼 수 있듯이 이것이 완전한 억제책은 아닙니다. 해당 해킹의 근본 원인은 TWAP 기간이 600초이고 Uniswap 풀이 넓은 범위에 걸쳐 유동성을 갖지 않은 Uniswap V3 TWAP 오라클의 가격 조작 때문이었습니다.

```Solidity
    const args: Parameters<Seer__factory['deploy']> = [
        'TAP/USDC', // Name
        'TAP/USDC', // Symbol
        18, // Decimals
        [
            ARGS_CONFIG[chainID].TAP_ORACLE.TAP_ADDRESS, // TAP
            ARGS_CONFIG[chainID].MISC.USDC_ADDRESS, // USDC
        ],
        [
            ARGS_CONFIG[chainID].TAP_ORACLE.TAP_USDC_LP_ADDRESS, /// LP TAP/USDC
        ],
        [1], // Multiply/divide Uni
        600, // @audit Uniswap V3 TWAP duration 600 seconds is lower than typical 30 mins
        10, // Observation length that each Uni pool should have
        0, // Whether we need to use the last Chainlink oracle to convert to another
        // CL path
        [],
        [], // Multiply/divide CL
        86400, // CL period before stale, 1 day
        [deployer.address], // Owner
        hre.ethers.utils.formatBytes32String('TAP/USDC'), // Description,
        ARGS_CONFIG[chainID].MISC.CL_SEQUENCER, // CL Sequencer
        deployer.address, // Owner
    ];
```

**참고 문헌**

[1] https://docs.euler.finance/euler-protocol/eulers-default-parameters#twap-length

[2] https://blog.uniswap.org/uniswap-v3-oracles

[3] https://cmichel.io/replaying-ethereum-hacks-rari-fuse-vusd-price-manipulation/

## 권장 사항

`TapOracle` Uniswap V3 오라클 TWAP 기간을 최소 30분으로 설정하십시오. 공격 비용을 높이기 위해 TAP/USDC 풀 유동성이 넓은 범위에 분산되도록 하는 것도 중요합니다.

# [M-11] 크로스 체인 호출에서 가스 전달 누락

## 심각도

**영향**: 중간 (Medium), 중요한 기능의 고장 난 기능

**가능성**: 중간 (Medium), `TOFT_REMOVE_AND_REPAY` 작업은 호출될 때 항상 되돌려짐

## 설명

`TOFT_REMOVE_AND_REPAY` 작업은 포지션을 종료한 다음 시장에서 담보를 제거하는 데 사용됩니다. 문제는 호출되는 함수가 LayerZero 호출을 보내지만 가스가 전달되지 않는다는 것입니다.

함수 호출은 아래와 같이 `TOFT_REMOVE_AND_REPAY` 작업 ID 아래의 `MagnetarV2.sol` 계약에서 찾을 수 있습니다.

```solidity
if (_action.id == TOFT_REMOVE_AND_REPAY) {
                HelperTOFTRemoveAndRepayAsset memory data = abi.decode(
                    _action.call[4:],
                    (HelperTOFTRemoveAndRepayAsset)
                );

                _checkSender(data.from);
                IUSDOBase(_action.target).removeAsset(
                    data.from,
                    data.to,
                    data.lzDstChainId,
                    data.zroPaymentAddress,
                    data.adapterParams,
                    data.externalData,
                    data.removeAndRepayData,
                    data.approvals,
                    data.revokes
                );
```

외부 호출로 가스가 전달되지 않기 때문에 외부 호출은 0의 `msg.value`를 갖습니다. 하지만 BaseUSDO.sol의 `removeAsset` 함수를 확인하면 USDOMarketModule을 통한 후속 layerzero 호출이 보입니다.

```solidity
bytes memory lzPayload = abi.encode(
            PT_MARKET_REMOVE_ASSET,
            to,
            externalData,
            removeAndRepayData,
            approvals,
            revokes,
            airdropAmount
        );

        _checkAdapterParams(
            lzDstChainId,
            PT_MARKET_REMOVE_ASSET,
            adapterParams,
            NO_EXTRA_GAS
        );

        _lzSend(
            lzDstChainId,
            lzPayload,
            payable(from),
            zroPaymentAddress,
            adapterParams,
            msg.value
        );
```

여기서 문제는 `msg.value`가 0이므로 layerzero 엔드포인트로 가스가 전송되지 않아 크로스 체인 호출이 실패한다는 것입니다.

## 권장 사항

가스를 전달하도록 magnetar의 호출을 수정하십시오.

```solidity
 IUSDOBase(_action.target).removeAsset{value: _action.value}(...)
```
# [M-12] `GlpOracle`에서 `Glp`를 최대화하지 않으면 유동성 추가 시 `_minGlp` 계산 오류 발생

## 심각도

영향: 중간 (Medium). 반올림이 잘못되어 가격에 영향을 미칩니다.

가능성: 높음 (High). False가 하드코딩되어 있으므로 발생할 가능성이 매우 높습니다.

## 설명

현재 `GlpOracle` 계약에는 Glp Manager에서 Glp 가격을 가져오는 함수가 있습니다:

```
 function _get() internal view returns (uint256) {
        return glpManager.getPrice(false);
    }
```

가격 최대화가 `glpManager.getPrice(false);`와 같이 false로 설정되어 있습니다. 이 가격은 Yieldbox 모듈의 `GlpStrategy`에서 가져옵니다. 이 경우 가격은 GMX에 유동성을 추가할 때 예상되는 amountOut 또는 `_minGlp`에 영향을 미칩니다.

```
 (success, glpPrice) = wethGlpOracle.get(wethGlpOracleData);
            if (!success) revert Failed();
            uint256 amountInGlp = (wethAmount * glpPrice) / 1e18;
            amountInGlp = amountInGlp - (amountInGlp * _slippage) / 10_000;
```

가격 최대화가 `false`로 설정되어 있으므로 약간 더 작은 가격을 반환하며, 이는 `maximize = true`로 설정된 경우보다 슬리피지를 더 낮게 설정합니다.

이로 인해 `GlpManager`는 `_minGlp`가 maximize가 `false`로 설정된 상태에서 reverted되지 않고 사용자에게 더 적은 Glp를 발행할 수 있습니다:

`require(mintAmount >= _minGlp, "GlpManager: insufficient GLP output");`

## 권장 사항

```diff
 function _get() internal view returns (uint256) {
-       return glpManager.getPrice(false);
+       return glpManager.getPrice(true);
    }
```

# [M-13] Magnetar의 모든 토큰 또는 ETH가 유출될 수 있음

## 심각도

**영향**: 높음 (High), 자금을 도난당할 수 있으며 승인이 무작위 주소로 제공될 수 있음

**가능성**: 낮음 (Low), Magnetar는 사용자 자금을 보유하도록 설계되지 않음

## 설명

Magnetar 계약은 YieldBox 계약 및 Singularity 및 BigBang 시장 계약과 같은 특정 계약에 토큰 전송을 수행하기 위한 승인을 발행합니다. 문제는 이러한 모든 주소를 사용자 입력으로 받아들인다는 것입니다. 따라서 악의적인 사용자는 이를 악용하여 악의적인 계약에 승인을 부여한 다음 Magnetar 계약에 있는 모든 토큰을 빼낼 수 있습니다.

Magnetar 계약 자체는 사용자 자금을 보유하도록 설계되지 않았지만 ETH에 대한 복구 기능이 있으며 LZ 호출을 위한 가스로 일부 eth를 보유할 수 있습니다. 이러한 자금도 도난당할 수 있습니다.

예를 들어 `MagnetarMarketModule.sol` 계약의 `withdrawToChain` 함수를 살펴보겠습니다.

```solidity
function withdrawToChain(
        IYieldBoxBase yieldBox,
        address from,
        uint256 assetId,
        uint16 dstChainId,
        bytes32 receiver,
        uint256 amount,
        bytes memory adapterParams,
        address payable refundAddress,
        uint256 gas,
        bool unwrap
    )
```

`yieldBox` 주소는 사용자로부터 입력으로 받아들여지며 확인되지 않습니다. 그런 다음 `_withdrawToChain` 함수에서 이 주소와 상호 작용합니다. 전달된 `dstChainId`가 0이 아니라고 가정해 보겠습니다. 그러면 계약은 이 악의적인 `yieldbox` 계약을 호출하여 다른 주소, asset을 가져옵니다.

```solidity
(, address asset, , ) = yieldBox.assets(assetId);
```

이 `asset` 주소도 악의적인 사용자 입력입니다. 그런 다음 `unwrap`이 `true`로 설정되면 함수는 다른 사용자 입력인 `gas`에 의해 결정된 금액의 eth로 이 `asset` 주소를 호출합니다.

```solidity
if (unwrap) {
            ICommonData.IApproval[]
                memory approvals = new ICommonData.IApproval[](0);
            ITapiocaOFT(address(asset)).triggerSendFromWithParams{value: gas}(
                address(this),
                dstChainId,
                receiver,
                amount,
                callParams,
                true,
                approvals,
                approvals
            );
```

`asset`도 악의적인 주소이므로 계약에서 이 무료 eth를 받고 이 계약 호출을 완료할 수 있습니다. 따라서 공격자는 계약에서 무료 eth를 얻었습니다.

마찬가지로 예를 들어 `_depositAddCollateralAndBorrowFromMarket` 함수에서 다른 ERC20 토큰도 유출될 수 있으며, 여기서 계약은 입력으로 사용자가 전달한 `market` 값에 대한 호출을 수행합니다.

```solidity
IYieldBoxBase yieldBox = IYieldBoxBase(market.yieldBox());

        uint256 collateralId = market.collateralId();
        (, address collateralAddress, , ) = yieldBox.assets(collateralId);
```

악의적인 `market` 계약은 더 많은 악의적인 주소를 반환할 수 있으며, 그런 다음 `extractTokens`가 호출되어 토큰 전송을 수행하면 공격자가 Magnetar에 저장된 토큰을 꺼내는 데 사용할 수 있습니다. 승인도 악의적인 주소일 수 있는 `yieldbox`에 부여됩니다.

```solidity
if (deposit) {
            // transfers tokens from sender or from the user to this contract
            collateralAmount = _extractTokens(
                extractFromSender ? msg.sender : user,
                collateralAddress,
                collateralAmount
);
IERC20(collateralAddress).approve(address(yieldBox), 0);
            IERC20(collateralAddress).approve(
                address(yieldBox),
                collateralAmount
            );
```

따라서 다양한 기능을 사용하여 Magnetar에 남은 자금을 모두 빼낼 수 있습니다.

## 권장 사항

Magnetar에 자금을 전혀 보관하지 않는다면 이를 무시해도 됩니다. 그렇지 않으면 `yieldbox` 및 `markets`에 대한 주소를 계약 화이트리스트와 대조하여 확인해야 합니다. 이러한 패턴은 `_mintFromBBAndLendOnSGL` 함수에서 `cluster` 변수를 사용하여 이미 존재합니다.

```solidity
if (externalContracts.bigBang != address(0)) {
            //@audit cluster check useless since passed in by the user
            if (
                !_cluster.isWhitelisted(
                    _cluster.lzChainId(),
                    externalContracts.bigBang
                )
            ) revert NotAuthorized();
        }
```

# [M-14] 반올림 오류로 인해 Magnetar `_depositRepayAndRemoveCollateralFromMarket`이 실패할 수 있음

## 심각도

**영향**: 중간 (Medium), 함수가 되돌려질 수 있음

**가능성**: 중간 (Medium), 사용자가 담보를 제거하고 yieldbox에서 인출하려고 할 때 발생

## 설명

`_depositRepayAndRemoveCollateralFromMarket` 함수를 호출하는 동안 `collateralAmount`가 0보다 크게 설정되면 다음 스니펫이 실행됩니다.

```solidity
if (collateralAmount > 0) {
            address collateralWithdrawReceiver = withdrawCollateralParams
                .withdraw
                ? address(this)
                : user;
            uint256 collateralShare = yieldBox.toShare(
                marketInterface.collateralId(),
                collateralAmount,
                false
            );
            marketInterface.removeCollateral(
                user,
                collateralWithdrawReceiver,
                collateralShare
            );

            //withdraw
            if (withdrawCollateralParams.withdraw) {
                _withdrawToChain(
                    yieldBox,
                    collateralWithdrawReceiver,
                    marketInterface.collateralId(),
                    withdrawCollateralParams.withdrawLzChainId,
                    LzLib.addressToBytes32(user),
                    collateralAmount,
                    withdrawCollateralParams.withdrawAdapterParams,
                    valueAmount > 0 ? payable(msg.sender) : payable(this),
                    valueAmount,
                    withdrawCollateralParams.unwrap
                );
            }
        }
```

첫 번째 비트에서 `collateralShare`는 yieldBox 리베이스 계산에서 계산됩니다. 마지막 매개변수가 false이므로 답은 내림됩니다. `collateralAmount`가 100으로 설정되고 계산된 `collateralShare`가 49에서 50 사이이며 내림으로 인해 49로 설정된다고 가정해 보겠습니다.

나중에 `_withdrawToChain` 호출에서 계약은 전체 `collateralAmount` 만큼의 토큰을 인출하려고 시도합니다. 여기서 yieldBox는 해당 토큰을 꺼내기 위해 다시 호출됩니다.

```solidity
yieldBox.withdraw(
                assetId,
                from,
                LzLib.bytes32ToAddress(receiver),
                amount,
                0
            );
```

그러나 이 호출에서는 인출이기 때문에 공제할 지분의 양이 올림됩니다. 따라서 이제 동일한 100단위의 `collateralAmount`를 인출하기 위해 소각할 지분의 양은 올림으로 인해 50으로 계산됩니다. 계약은 49개의 지분만 인출했지만 yieldBox는 50개를 소각하려고 하므로 이 함수는 되돌려집니다.

이는 다른 시나리오에서 되돌려지도록 강제하는 유사한 반올림 오류를 보여주는 M-02 보고서와 유사합니다.

## 권장 사항

올림하여 `collateralShare`를 계산하십시오. 이렇게 하면 인출 시 소각할 충분한 지분이 확보됩니다.

```solidity
uint256 collateralShare = yieldBox.toShare(
                marketInterface.collateralId(),
                collateralAmount,
                true
            );
```

# [M-15] DEX 스와퍼만 사용하는 것은 최적이지 않음

## 심각도

영향: 중간 (Medium), 애그리게이터 없는 스왑은 대량의 경우 높은 가격 영향을 받을 수 있습니다.

가능성: 중간 (Medium). 스왑되는 양에 따라 빈번한 문제가 될 수 있습니다.

## 설명

현재 Tapioca의 코드베이스 전체에서 토큰을 교환해야 하는 대부분의 작업은 스와퍼를 사용하며 현재 3개입니다.

각 스와퍼는 다른 DEX, UniV2, Univ3 및 Curve DeFi 풀을 사용합니다. 스왑할 때 스왑한 토큰에 대한 대가로 얻는 amountOut을 최대화하려고 하기 때문에 대부분의 경우 이상적인 시나리오가 아닙니다. 이를 달성하고 스왑에 대해 더 나은 비율을 얻으려면 스와퍼 목록에 하나 이상의 애그리게이터를 추가해야 합니다.

## 권장 사항

스와퍼 목록에 하나 이상의 애그리게이터를 추가하십시오. 저는 1inch를 선호하지만 0x를 사용할 수도 있습니다.

# [M-16] Stargate로 전송된 금액을 계산할 때 순서가 잘못되어 `participate()` 함수를 사용할 수 없음

## 심각도

영향: 중간 (Medium), 기능이 항상 되돌려지기 때문입니다.

가능성: 중간 (Medium), `participate`를 호출할 때만 발생하기 때문입니다.

## 설명

Stargate 도우미 계약의 participate() 함수가 작동하지 않습니다.

그 원인은 Tapioca가 Stargate가 반환하는 더스트(dust) 금액을 환불하기 위해 Stargate로 전송된 금액을 계산하는 방식 때문입니다:

```
uint256 transferred = balanceAfter - balanceBefore;
```

간단하게 설명하기 위해 소수점 없는 예를 들어보겠습니다.

- 제가 msg.sender로서 50개의 토큰을 전송합니다: `balanceBefore = 50`

- 그 50개의 토큰은 풀로 보내집니다. Stargate가 1개의 토큰을 더스트로 반환한다고 가정해 보겠습니다.

- 그러면 다음과 같습니다: `uint256 transferred = 1 - 50;`

따라서 계산은 항상 되돌려집니다.

## 권장 사항

```diff
uint256 balanceAfter = IERC20(stargateData.srcToken).balanceOf(
            address(this)
        );
+        uint256 transferred = balanceBefore  - balanceAfter;
-        uint256 transferred = balanceAfter - balanceBefore;
```

# [L-01] `StargateLbpHelper`의 사용(redeem) 함수 및 `retryRevert()`는 불필요함

`StargateLbpHelper`에는 stargate에서 유동성을 제거하는 사용(redeem) 함수가 있지만 유동성을 추가하는 함수가 없어 불필요합니다.

또한 `someone`이 `StargateLbpHelper`의 기존 `retryRevert()`를 직접 호출하여 stargate 스왑을 재시도할 수 있으므로 `retryRevert()`가 필요하지 않습니다.

이 문제를 해결하려면 사용(redeem) 함수와 `retryRevert()`를 제거하십시오.

# [L-02] `getOutputAmount()`는 상태 변경 함수에서 사용해서는 안 됨

`UniswapV2Swapper` 및 `UniswapV3Swapper` 모두에서 `getOutputAmount()`의 의도는 프론트엔드에서 스왑 매개변수를 구축하기 위한 뷰 함수라고 가정합니다. 현물 가격 또는 낮은 TWAP 기간 가격을 제공하기 때문입니다. 이 두 가지 모두 가격이 조작되어 상태 변경 함수에 영향을 미칠 수 있으므로 상태 변경 함수에서 사용해서는 안 됩니다.

그러나 가격 조작이 영향을 미칠 수 있는 `LiquidationQueue` 내의 상태 변경 함수 `activateBid()` 및 `removeBid()`에서 사용됩니다.

`LiquidationQueue`가 감사 범위를 벗어났고 `getOutputAmount()`가 뷰 함수 전용으로 설계되었다고 가정하므로 이를 낮음(Low)으로 설정합니다.

이 문제를 해결하려면 상태 변경 함수에서 `getOutputAmount()`를 사용하지 마십시오.

# [L-03] `_withdrawToChain()`에서 잘못된 `interfaceId` 확인

`MagnetarMarketModule._withdrawtoChain()`은 다음과 같이 `supportsInterface()`를 사용하여 자산이 크로스 체인 전송을 지원하는지 확인합니다:

```Solidity
        try
            IERC165(address(asset)).supportsInterface(
                type(ISendFrom).interfaceId
            )
        {} catch {
            yieldBox.withdraw(
                assetId,
                from,
                LzLib.bytes32ToAddress(receiver),
                amount,
                0
            );
            return;
        }
```

그러나 `sendFrom()`을 지원하는 LayerZero `BaseOFTV2sol`은 실제로 다음과 같이 `type(IOFTV2).interfaceId`를 반환합니다:

```Solidity
    function supportsInterface(bytes4 interfaceId) public view virtual override(ERC165, IERC165) returns (bool) {
        return interfaceId == type(IOFTV2).interfaceId || super.supportsInterface(interfaceId);
    }
```

LayerZero `OFTCore.sol`은 `type(IOFTCore).interfaceId`를 반환합니다.

```Solidity
    function supportsInterface(bytes4 interfaceId) public view virtual override(ERC165, IERC165) returns (bool) {
        return interfaceId == type(IOFTCore).interfaceId || super.supportsInterface(interfaceId);
    }
```

그리고 `TapiocaOFT`는 `triggerSendFromWithParams()` 함수의 interfaceId를 반환하지 않고 상속을 기반으로 OFTV2 `interfaceId`만 반환합니다.

검사에는 3가지 문제가 있습니다.

1. `supportInterface()` 검사는 잘못된 `interfaceId`를 사용하여 확인하므로 자산이 `sendFrom()`을 지원하지 않는 경우에도 실제로 통과합니다.
2. `supportInterface()` 검사는 `supportInterface()` 검사가 되돌려지지 않는 한 자산이 `sendFrom()`을 지원하지 않더라도 통과합니다. 이 경우 false를 반환하는 `supportInterface()`의 반환 값을 처리하지 않기 때문입니다.
3. `unwrap == true`일 때 자산이 `triggerSendFromWithParams()`를 지원하는 `TapiocaOFT`가 아니더라도 검사가 통과합니다.

크로스 체인 호출 중에 대상 체인에서 `_withdrawtoChain()`이 호출될 가능성이 있습니다. 이 경우 이 문제로 인해 다른 체인에서 인출할 수 없는 경우 토큰이 `Magnetar` 계약에 갇히게 됩니다. 그러나 이것은 지원되지 않는 자산을 다른 체인에서 인출하려고 할 때 사용자 오류 시에만 발생하므로 낮음(Low)으로 분류합니다.

이 문제는 다음과 같이 해결할 수 있습니다:

1. `sendFrom()` 사용의 경우 자산이 `type(IOFTCore).interfaceId` 또는 `type(IOFTV2).interfaceId`를 지원하는지 확인하십시오.
2. `triggerSendFromWithParams()` 사용의 경우 추가해야 하는 TapiocaOFT `interfaceId`를 자산이 지원하는지 확인하십시오.
3. 위의 경우 `supportInterface()`의 반환 값을 확인하고 false를 반환하면 로컬 체인 인출을 수행하십시오.
   참고: try/catch는 IERC165를 구현하지 않은 자산을 처리하기 위해 그대로 유지될 수 있습니다.

또는 더 쉬운 해결책은 `triggerSendFromWithParams()` 및 `sendFrom()`에 대해 try/catch를 수행하여 인출된 자산이 크로스 체인 전송을 지원하지 않는 시나리오를 처리하는 것입니다.

# [L-04] 프로토콜이 구 버전의 OpenZeppelin 계약을 사용함

현재 사용 중인 버전은 4.5.0으로 꽤 오래된 버전입니다. OpenZeppelin 4.9.3까지의 모든 버전에는 관련 버그가 있습니다. 범위는 4.5.0에 보고된 문제의 직접적인 영향을 받지 않지만 버그가 발견되지 않은 가장 초기 버전을 사용하는 것이 좋습니다. 현재 이는 버전 4.9.3입니다. 버전 4.5.0의 높은 심각도 위험 중 하나는 `ERC165Checker.supportsInterface`와 관련이 있습니다. 이 계약은 `ProxyONFT721` 및 `ProxyONFT1155`에서 상속됩니다. 이들은 범위에 포함되지 않으므로 범위 내 계약에 대한 이 발견의 심각도는 낮음(Low)입니다. 그러나 OZ v4.5.0에서 `ERC165Checker.supportsInterface`는 어떠한 상황에서도 되돌려지지 않고 항상 true를 반환합니다. 자세한 내용은 [여기](https://security.snyk.io/vuln/SNYK-JS-OPENZEPPELINCONTRACTS-2958047)를 참조하십시오. v4.9.3으로 업그레이드하는 것을 고려하십시오.

# [L-05] `answeredInRound`는 더 이상 사용되지 않음

`ChainlinkUtils.sol`에서 `_readChainlinkBase()` 함수는 `roundId`가 `answeredInRound`보다 큰지 확인할 때 사용 중단된 변수를 사용합니다.

Chainlink는 최근 `answeredInRound`가 이제 더 이상 사용되지 않는다고 추가했으므로 사용할 때 주의해야 합니다.

> - `answeredInRound`: 사용 중단됨 (Deprecated) - 이전에는 답변을 계산하는 데 여러 라운드가 걸릴 수 있을 때 사용되었습니다.

[참조](https://docs.chain.link/data-feeds/feed-registry/feed-registry-functions#latestrounddata)

# [L-06] `zroPaymentAddress`를 `address(0)`으로 하드코딩해서는 안 됨

[LayerZero 통합 체크리스트](https://layerzero.gitbook.io/docs/troubleshooting/layerzero-integration-checklist)에는 다음과 같이 명시되어 있습니다:

> "수수료를 추정하고 메시지를 보낼 때 `address(0)`을 `zroPaymentAddress`로 하드코딩하지 마십시오. 대신 매개변수로 전달하십시오."

하지만 [`MagnetarMarketModule.sol`](https://github.com/Tapioca-DAO/tapioca-periph/blob/d6cdac0d09c817f7b5c67e97f73defbae6c4aa01/contracts/Magnetar/modules/MagnetarMarketModule.sol#L801)에서 `zroPaymentAddress`는 `address(0)`으로 하드코딩되어 있습니다:

```solidity
        // build LZ params
        bytes memory _adapterParams;
        ICommonOFT.LzCallParams memory callParams = ICommonOFT.LzCallParams({
            refundAddress: msg.value == gas ? refundAddress : payable(this),
❌          zroPaymentAddress: address(0), //@audit should not be hardcoded
            adapterParams: ISendFrom(address(asset)).useCustomAdapterParams()
                ? adapterParams
                : _adapterParams
        });
```

하드코딩된 `address(0)`을 제거하고 대신 매개변수로 전달하십시오.

# [L-07] `StargateLbpHelper.participate()`가 계약에 더스트(dust) 금액을 남길 수 있음

`StargateLbpHelper.participate()`에서 `srcToken`의 `stargateData.amount`가 계약으로 전송된 다음 Stargate `router.swap()`을 통해 다른 체인으로 전송됩니다.

그러나 Stargate `router.swap()`은 계약에 더스트 금액을 남길 변환으로 인해 전체 `stargateData.amount`보다 적게 보낼 수 있습니다. 이는 체인마다 소수 점 자릿수가 다른 토큰의 경우 발생하며, Stargate 라우터는 로컬 소수 점(소스 체인)에서 공유 소수 점(모든 체인에 대해 정규화됨)으로 변환합니다.

예를 들어 Stargate LUSD 풀은 Optimism의 경우 로컬 소수 점 자릿수가 18이고 공유 소수 점 자릿수가 6입니다. 따라서 변환(로컬 소수 점에서 공유 소수 점으로, 그런 다음 다시 로컬 소수 점으로)은 로컬 소수 점보다 낮은 공유 소수 점으로 표시되지 않는 더스트 금액을 제거합니다.

```Solidity
    function participate(
        StargateData calldata stargateData,
        ParticipateData calldata lbpData
    ) external payable nonReentrant {
        IERC20 erc20 = IERC20(stargateData.srcToken);

        // retrieve source token from sender
        erc20.safeTransferFrom(msg.sender, address(this), stargateData.amount);

        // compute min amount to be received on destination
        uint256 amountWithSlippage = stargateData.amount -
            ((stargateData.amount * stargateData.slippage) /
                SLIPPAGE_PRECISION);

        // approve token for Stargate router
        erc20.safeApprove(address(router), stargateData.amount);

        //@audit this could leave dust amount due to a conversion within swap()
        // send over to another layer using the Stargate router
        router.swap{value: msg.value}(
            stargateData.dstChainId,
            stargateData.srcPoolId,
            stargateData.dstPoolId,
            payable(msg.sender), //refund address
            stargateData.amount,
            amountWithSlippage,
            IStargateRouterBase.lzTxObj({
                dstGasForCall: 0,
                dstNativeAmount: 0,
                dstNativeAddr: "0x0"
            }),
            abi.encodePacked(msg.sender), // StargateLbpHelper.sol destination address
            abi.encode(lbpData, msg.sender)
        );
    }
```

```Solidity
    function swap(
        uint16 _dstChainId,
        uint256 _srcPoolId,
        uint256 _dstPoolId,
        address payable _refundAddress,
        uint256 _amountLD,
        uint256 _minAmountLD,
        lzTxObj memory _lzTxParams,
        bytes calldata _to,
        bytes calldata _payload
    ) external payable override nonReentrant {
        require(_amountLD > 0, "Stargate: cannot swap 0");
        require(_refundAddress != address(0x0), "Stargate: _refundAddress cannot be 0x0");
        Pool.SwapObj memory s;
        Pool.CreditObj memory c;
        {
            Pool pool = _getPool(_srcPoolId);
            {
                uint256 convertRate = pool.convertRate();
                //@audit this converts from local decimals to shared decimals and then back to local decimals.
                //            it will remove dust amount that is not represented in shared decimals
                _amountLD = _amountLD.div(convertRate).mul(convertRate);
            }

            s = pool.swap(_dstChainId, _dstPoolId, msg.sender, _amountLD, _minAmountLD, true);
            _safeTransferFrom(pool.token(), msg.sender, address(pool), _amountLD);
            c = pool.sendCredits(_dstChainId, _dstPoolId);
        }
        bridge.swap{value: msg.value}(_dstChainId, _srcPoolId, _dstPoolId, _refundAddress, c, s, _lzTxParams, _to, _payload);
    }
```

`router.swap`에 의해 전송된 실제 금액을 확인하고 이를 사용하여 호출자에게 환불될 남은 더스트를 결정함으로써 이 문제를 해결할 수 있습니다.

# [L-08] `StargateLbpHelper`에 대해 사용되지 않는 `ParticipateData.poolId`

`StargateLbpHelper.participate()`는 `ParticipateData` 구조체 매개변수를 사용하며 여기에는 `poolId`가 있습니다.

그러나 `ParticipateData.poolId`는 대상 체인 `sgReceive()` 실행에 사용되지 않습니다. 이는 페이로드 크기를 기반으로 하므로 LayerZero 수수료를 약간 증가시킵니다.

```Solidity
    struct ParticipateData {
        address assetIn;
        address assetOut;
        uint256 poolId; //@audit this is not used for destination chain execution
        uint256 deadline;
        uint256 minAmountOut;
    }
```

`ParticipateData` 구조체에서 `poolId`를 제거하여 이 문제를 해결할 수 있습니다.

# [L-09] 쓸모없는 페이로드 확인

stargate로 전송되는 페이로드에는 다음 데이터가 포함됩니다: `abi.encode(lbpData, stargateData.receiver)`

하지만 `sgReceive()` 함수에는 다음과 같은 검사가 있습니다: `if (payload.length <= 40) return;`

이 검사는 stargate 문서에 있기 때문에 수행된다는 것을 알고 있지만, 그들은 2개의 주소 + 기타 데이터를 포함하는 다른 페이로드를 사용하고 있기 때문에 주소가 실제로 20바이트인지 확인하기 위해 40바이트 길이 검사가 존재합니다.

`ParticipateData` 구조체를 보내야 하고 페이로드의 구성 요소가 바이트 단위가 아니라는 점을 감안할 때 이 검사는 쓸모가 없습니다.

`sgReceive()` 함수에서 다음 검사를 제거하십시오.

```diff
- if (payload.length <= 40) return;
```

# [L-10] layerZero 수수료를 추정할 때 `_payInZRO`를 false로 하드코딩하지 마십시오

[LayerZero 통합 체크리스트](https://layerzero.gitbook.io/docs/troubleshooting/layerzero-integration-checklist)에는 다음과 같이 명시되어 있습니다:

> "수수료를 추정하고 메시지를 보낼 때 useZro를 false로 하드코딩하지 마십시오. 대신 매개변수로 전달하십시오."

하지만 [`StargateLbpHelper.sol`](https://github.com/Tapioca-DAO/tapioca-periph/blob/73313d5ff9241f7a584284eb6c006339b7f70314/contracts/StargateLbpHelper.sol#L111)에서 `_payInZRO`는 `false`로 하드코딩되어 있습니다:

```solidity
 endpoint.estimateFees(
                _dstChainId,
                address(bridge),
                payload,
                false,
                _txParamBuilder(_dstChainId, _functionType, _lzTxParams)
            );
```


