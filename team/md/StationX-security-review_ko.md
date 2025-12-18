# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**Pashov Audit Group**은 **StationX-Network/smartcontract** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토를 수행했습니다.

# StationX 소개

StationX는 커뮤니티가 온체인 멤버십을 생성 및 관리하고, 자본을 풀링하며, 안전한 다중 서명(multi-sig) 재무를 통해 운영을 실행할 수 있도록 지원하는 프로토콜입니다. 사용자는 스테이션 내에서 멤버십 조건을 설정하고, 일상적인 작업을 처리하며, 자금을 쉽게 모금할 수 있습니다.

# 위험 분류

| 심각도 (Severity)      | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음 (High)**   | 치명적 (Critical)     | 높음 (High)           | 중간 (Medium)      |
| **가능성: 중간 (Medium)** | 높음 (High)         | 중간 (Medium)         | 낮음 (Low)         |
| **가능성: 낮음 (Low)**    | 중간 (Medium)       | 낮음 (Low)            | 낮음 (Low)         |

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

_검토 커밋 해시_ - [de6dbb69fd6ec51570899d6e110030d0d5ac602f](https://github.com/StationX-Network/smartcontract/tree/de6dbb69fd6ec51570899d6e110030d0d5ac602f)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `Claims/`
- `LayerZero/`
- `Deployer`
- `emitter`
- `erc20dao`
- `erc721dao`
- `factory`
- `helper`
- `proxy`
- `zairdrop`
- `interfaces`

# 발견 사항

# [C-01] 크로스체인 매수 작업 시 LayerZero 구현 컨트랙트가 DAO 수수료 수신자가 됨

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`createCrossChainERC20DAO()` 또는 `createCrossChainERC721DAO()`를 통해 크로스체인 DAO가 생성될 때, `ownerAddress`를 `msg.sender`로 할당합니다. 문제는 `msg.sender`가 항상 LayerZero Deployer 컨트랙트가 된다는 것입니다:

```solidity
    function createCrossChainERC20DAO(...) {
@>      require(msg.sender == commLayer, "Caller not LZ Deployer");
        bytes memory _payload = abi.encode(_daoAddress, msg.sender, _numOfTokensToBuy, _tokenURI, _merkleProof);
        ccDetails[_daoAddress] =
            CrossChainDetails(
                _commLayerId,
                _depositChainIds,
                false,
@>              msg.sender,          // @audit ownerAddress
                _onlyAllowWhitelist
            );
    }
```

따라서 크로스체인 매수 작업이 수행될 때마다 수수료는 DAO 소유자 주소가 아닌 LayerZero Deployer로 전송되려고 시도합니다:

```solidity
    payable(
        ccDetails[_daoAddress].ownerAddress != address(0)
@>          ? ccDetails[_daoAddress].ownerAddress
            : IERC20DAO(_daoAddress).getERC20DAOdetails().ownerAddress
    ).call{value: ownerShare}("");
```

LayerZero Deployer에는 `receive() payable {}` 함수가 없기 때문에 수수료는 어디로도 전송되지 않으며, `call`의 결과는 확인되지 않습니다. 어쨌든 DAO 소유자의 주소는 해당 수수료를 받지 못합니다.

## 권고 사항

DAO 생성자가 각 크로스체인 DAO 배포에 대해 `ownerAddress`를 지정할 수 있도록 허용하십시오 (모든 체인에서 동일한 주소를 제어하지 못할 수 있으므로).

# [C-02] DAO의 네이티브 토큰이 묶임

## 심각도

**영향:** 높음 (High). 예치된 네이티브 토큰이 DAO로 전송되지만, 그곳에서 이동할 수 없습니다.

**가능성:** 높음 (High). 네이티브 토큰 예치금이 있는 DAO에서는 항상 발생합니다.

## 설명

`_buyGovernanceTokenERC20DAO()` 및 `_buyGovernanceTokenERC721DAO()` 모두 설정된 경우 네이티브 토큰 예치금을 DAO 주소로 보냅니다:

```solidity
    if (_daoDetails.depositTokenAddress == NATIVE_TOKEN_ADDRESS) {
        payable(_daoDetails.assetsStoredOnGnosis ? _daoDetails.gnosisAddress : _daoAddress).call{
            value: _totalAmount
        }("");
    }
```

문제는 이러한 네이티브 토큰을 출금, 사용 또는 전송할 수 있는 기능이 없다는 것입니다.

## 권고 사항

`ERC20DAO` 및 `ERC721DAO`의 `updateProposalAndExecution()` 함수에 `_value` 매개변수를 전달하여 네이티브 토큰을 전송할 수 있게 하십시오.

또한 이 함수를 `payable`로 만드는 것을 고려하십시오. 이는 DAO가 예치금을 사용하지 않고 `value`와 함께 외부 호출을 하는 데 유용할 수 있습니다.

```diff
-   function updateProposalAndExecution(address _contract, bytes memory _data)
+   function updateProposalAndExecution(address _contract, bytes memory _data, uint256 _value)
        external
        onlyGnosis(factoryAddress, address(this))
        nonReentrant
+       payable
    {
-       (bool success,) = _contract.call(_data);
+       (bool success,) = _contract.call{value: _value}(_data);
        require(success);
    }
```

# [C-03] Factory 컨트랙트에서 네이티브 토큰이 탈취될 수 있음

## 심각도

**영향:** 높음 (High). 자산 도난.

**가능성:** 높음 (High). 모든 사용자가 수행할 수 있습니다.

## 설명

공격자는 `crossChainBuy()` 함수의 오류를 악용하여 Factory 컨트랙트에서 모든 네이티브 토큰을 훔치는 공격을 수행할 수 있습니다.

오류의 단순화된 버전은 다음과 같습니다. `value: msg.value - fees` 계산에 존재합니다:

```solidity
    function crossChainBuy(...) {
        _buyGovernanceTokenERC20DAO(_daoAddress, _numOfTokensToBuy);

        fees = ((depositFees * platformFeeMultiplier) / 100);
@>      ICommLayer(_commLayer).sendMsg{value: msg.value - fees}(_commLayer, _payload, _extraParams);
    }
```

`sendMsg()`는 `value = msg.value - fees`로 LayerZero 메시지를 보내고 초과 금액을 `_extraParams` 내에서 사용자가 제공한 주소로 환불합니다.

예를 들어 `msg.value = 10 ETH`, `fees = 0.1 ETH`이고 LayerZero 메시지에 또 다른 0.1 ETH가 필요하다고 가정해 봅시다. 따라서 LayerZero는 9.9 ETH를 받고 사용되지 않은 금액을 환불합니다. 사용자의 최종 잔액은 9.8 ETH가 됩니다.

프로토콜 수수료가 Factory 컨트랙트에 남는다는 점에 유의하십시오. 이는 DAO 배포 시에도 발생합니다. 네이티브 토큰 수수료는 프로토콜 소유자가 청구할 때까지 컨트랙트에 남아 있습니다. 이것들이 도난당할 수 있는 토큰들입니다.

`_buyGovernanceTokenERC20DAO()` (및 ERC721 함수)에서 예치된 네이티브 토큰이 Safe/DAO로 보내지는 반면, 일부 수수료는 DAO 소유자에게 보내지는 것을 확인하십시오:

```solidity
    function _buyGovernanceTokenERC20DAO(...) {
        uint256 ownerShare = (_totalAmount * _daoDetails.ownerFeePerDepositPercent) / (FLOAT_HANDLER_TEN_4);

        if (_daoDetails.depositTokenAddress == NATIVE_TOKEN_ADDRESS) {
            checkDepositFeesSent(_daoAddress, _totalAmount + ownerShare);
@>          payable(_daoDetails.assetsStoredOnGnosis ? _daoDetails.gnosisAddress : _daoAddress).call{
                value: _totalAmount
            }("");

            payable(
                ccDetails[_daoAddress].ownerAddress != address(0)
                    ? ccDetails[_daoAddress].ownerAddress
                    : IERC20DAO(_daoAddress).getERC20DAOdetails().ownerAddress
@>          ).call{value: ownerShare}("");
        }
    }
```

이러한 자금은 `checkDepositFeesSent()`를 통해 제공된 `msg.value`가 그들을 모두 충당하기에 충분한지 검증하기 때문에 Factory에서 전송됩니다.

예를 들어, 예치된 금액이 9 ETH이고 수수료가 0.1 ETH라고 가정해 보겠습니다. 따라서 9.1 ETH가 DAO/Safe/DAO 소유자에게 전송됩니다. 공격자는 이를 받기 위해 가짜 DAO를 만들 수도 있습니다.

컨트랙트에 자금이 충분한 한, 앞서 언급한 `value = msg.value - fees`도 LayerZero로 보내고, 마지막으로 초과분을 사용자에게 환불합니다 (이 예에서는 9.8 ETH).

따라서 기본적으로 Factory 컨트랙트는 크로스체인 예치에 필요한 네이티브 토큰을 공급하고 있는 셈입니다.

## 권고 사항

LayerZero로 보낼 남은 `value`를 계산할 때, 예치 토큰이 네이티브 토큰인지 여부, 예치 금액, 소유자 몫, 예치 수수료 및 KYC 수수료를 고려하여 올바르게 계산하십시오.

# [H-01] ERC721 DAO에 대한 `maxTokensPerUser` 제한 우회

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

사용자가 `buyGovernanceTokenERC721DAO()`를 호출하면 코드는 최대 한도 확인을 수행한 다음 `mintToken()`을 호출합니다. 문제는 재진입 방지 장치(reentrancy guard)가 없고, `_safeMint()`를 사용하여 토큰을 하나씩 발행할 때 사용자가 ERC721의 훅(hook) 함수에서 다시 Factory 컨트랙트로 재진입하여 `buyGovernanceTokenERC721DAO()`를 호출하고 더 구매할 수 있다는 것입니다. 이때 `_tokenIdTracker`와 `balanceOf(_to)`가 완전히 업데이트되지 않았기 때문에 확인을 우회하게 됩니다. 이 영향은 화이트리스트가 있는 DAO에서 더 심각한데, 한 사용자가 허용된 것보다 더 많이 구매할 수 있기 때문입니다.

```solidity
    function mintToken(address _to, string calldata _tokenURI, uint256 _amount) public onlyFactory(factoryAddress) {
        if (balanceOf(_to) + _amount > erc721DaoDetails.maxTokensPerUser) {
            revert MaxTokensMintedForUser(_to);
        }

        if (!erc721DaoDetails.isNftTotalSupplyUnlimited) {
            require(
                Factory(factoryAddress).getDAOdetails(address(this)).distributionAmount >= _tokenIdTracker + _amount, "Max supply reached" );
        }
        for (uint256 i; i < _amount;) {
            _tokenIdTracker += 1;
            _safeMint(_to, _tokenIdTracker);
```

이것은 POC입니다:

1. 사용자1은 사용자당 최대 토큰이 15개인 상황에서 10개의 토큰을 구매하기 위해 `buyGovernanceTokenERC721DAO()`를 호출합니다.
2. 코드가 사용자를 위해 첫 번째 토큰을 발행하려고 `_safeMint()`를 호출하면 사용자1의 주소가 훅 함수에 의해 호출됩니다.
3. 사용자1은 다시 `buyGovernanceTokenERC721DAO()`를 호출하여 10개의 토큰을 더 구매합니다. 이번에는 이전 구매 수량이 `balanceOf(_to)`에 추가되지 않았기 때문에 확인을 통과합니다.
4. 결과적으로 사용자1은 사용자당 최대 한도가 15개인데도 20개의 토큰을 구매하게 됩니다.

## 권고 사항

재진입 방지 장치를 사용하거나 check-effect-interact 패턴을 사용하십시오.

# [H-02] DAO 세부 정보를 덮어쓰는 것이 가능함

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

코드는 Deployer 컨트랙트에서 `create`를 사용하여 DAO 토큰을 배포하므로, DAO 주소는 Deployer 컨트랙트 주소와 배포 논스(nonce)에만 의존합니다. 만약 두 체인이 동일한 Deployer 컨트랙트 주소를 가진다면, 해당 체인들에서 생성된 DAO는 동일한 주소를 갖게 됩니다. 이는 일부 DAO가 두 체인 모두에 배포될 때 충돌을 일으킬 수 있습니다. 공격자는 이를 사용하여 다른 체인에 있는 DAO의 세부 정보를 덮어쓸 수 있습니다. 따라서 DAO가 다중 체인 배포를 사용할 때 크로스체인에 동일한 DAO를 배포하는 것이 가능하며, 그 결과 Factory에 있는 실제 DAO의 정보가 덮어씌워질 수 있습니다.

이것은 POC입니다:

1. 사용자1은 chain1과 chain2에 자신의 DAO를 배포하고 싶어 합니다. 그는 chain1에 DAO를 배포하는 트랜잭션을 생성하고 chain2를 두 번째 체인으로 사용합니다. DAO 주소는 Deployer 컨트랙트 주소와 배포 `nonce`를 기반으로 하는 `create` opcode의 결과에 의해서만 결정됩니다.
2. Chain1과 Chain2가 동일한 Deployer 컨트랙트 주소를 가지고 있다면, 공격자는 chain2에서 동일한 주소를 갖는 DAO를 생성하고 chain1을 자신의 DAO의 사이드 체인으로 설정할 수 있습니다.
3. 결과적으로 chain2의 Factory 컨트랙트는 chain1에 메시지를 보내 공격자가 지정한 DAO를 사용자1의 DAO와 동일한 DAO 주소로 chain1에 배포하게 되고, 이는 사용자1의 DAO를 다시 쓰게(덮어쓰게) 됩니다.

## 권고 사항

DAO가 서로 다른 체인에서 다른 주소를 갖도록 하십시오.

# [H-03] 사용자가 LayerZero(lz) 통신을 중단시킬 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

LayerZeroImpl 컨트랙트는 LZ SDK의 NonBlockingLzApp을 사용하여 실패한 모든 메시지를 향후 재시도를 위해 저장합니다:

```solidity
    function _blockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64 _nonce, bytes memory _payload)
        internal
    {
        (bool success, bytes memory reason) = address(this).excessivelySafeCall(
            gasleft(),
            150,
            abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload)
        );

        if (!success) {
            _storeFailedMessage(_srcChainId, _srcAddress, _nonce, _payload, reason);
        }
    }
```

이 함수는 `excessivelySafeCall()`을 사용하여 최대 `gasleft()` 가스를 사용하고 최대 150바이트의 반환 데이터를 읽습니다. [63/64 규칙](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-150.md)으로 인해, `nonblockingLzReceive()`가 할당된 가스를 모두 사용하면 남은 가스의 1/64만이 실패한 메시지를 저장하는 데 남게 됩니다. LayerZeroImpl은 엔드포인트에 600k 가스를 전달합니다:

```solidity
        endpoint.send{value: msg.value}(
            _dstChainId,
            abi.encodePacked(dstCommLayer[uint16(_dstChainId)], address(this)),
            _payload,
            payable(refundAd),
            address(0x0),
            abi.encodePacked(uint16(1), uint256(600000)) // 600k
        );
```

63/64 가스를 모두 사용하면 약 9000 가스만 남게 되는데, 0에서 0이 아닌 값으로의 단일 SSTORE 비용이 22.1k 가스이므로 실패한 메시지를 저장하기에 불충분합니다. 이로 인해 Endpoint try/catch 처리에서 revert가 발생합니다.

```solidity
        try ILayerZeroReceiver(_dstAddress).lzReceive{gas: _gasLimit}(_srcChainId, _srcAddress, _nonce, _payload) {
            // success, do nothing, end of the message delivery
        } catch (bytes memory reason) {
            // revert nonce if any uncaught errors/exceptions if the ua chooses the blocking mode
            storedPayload[_srcChainId][_srcAddress] = StoredPayload(uint64(_payload.length), _dstAddress, keccak256(_payload));
            emit PayloadStored(_srcChainId, _srcAddress, _dstAddress, _nonce, _payload, reason);
        }
    }
```

이 부분은 페이로드를 저장하고 채널을 차단하는 부분입니다. 가스가 여기서 gasLimit으로 제한되므로 `storedPayload`에 실패를 저장할 가스가 부족할 위험은 없으며, Relayer는 `lzReceive()` 전후의 로직을 실행하기 위해 약간의 추가 버퍼를 제공할 것으로 예상됩니다.

악의적인 사용자는 `onERC721Received()`를 사용하여 할당된 모든 가스를 소모함으로써 이를 악용할 수 있습니다.

- Bob은 `onERC721Received()`에 할당된 모든 가스를 소모하는 악성 코드가 있는 스마트 컨트랙트입니다.
- Bob은 DAO 토큰을 구매하기 위해 `crossChainBuy()`를 호출하여 다음 호출 스택을 트리거합니다:
  - `commLayer.sendMsg()`
    - `endpoint.send()`
      - `commLayer.lzReceive()`
- `lzReceive()`는 `_nonblockingLzReceive()`를 호출하고, 이는 `factory.crossChainMint()`를 호출합니다.
- `crossChainMint()`는 몇 가지 온전성 검사를 수행한 다음 호출합니다:

  - `dao.mintToken()`
    - `_safeMint()`
      - `onERC721Received()`

- Bob의 `onERC721Received()` 훅은 할당된 모든 가스를 소모하여 `_blockingLzReceive()`가 실패하고 불충분한 가스로 실패한 메시지를 저장하려고 시도하게 합니다.
- revert는 Endpoint try/catch 처리로 전달되어 통로가 차단되는 결과를 초래합니다.

## 권고 사항

간단하고 효과적인 해결책은 safeMint 대신 mint를 사용하는 것입니다. 이를 명확하게 문서화하십시오:

> 만약 `to`가 스마트 컨트랙트라면, {IERC721Receiver-onERC721Received} 훅을 구현해야 합니다.

# [H-04] 악의적인 DAO 소유자가 lz 통신을 중단시킬 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

LayerZeroImpl 컨트랙트는 LZ SDK의 NonBlockingLzApp을 사용하여 실패한 모든 메시지를 향후 재시도를 위해 저장합니다:

```solidity
    function _blockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64 _nonce, bytes memory _payload)
        internal
    {
        (bool success, bytes memory reason) = address(this).excessivelySafeCall(
            gasleft(),
            150,
            abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload)
        );

        if (!success) {
            _storeFailedMessage(_srcChainId, _srcAddress, _nonce, _payload, reason);
        }
    }
```

이 함수는 `excessivelySafeCall()`을 사용하여 최대 `gasleft()` 가스를 사용하고 최대 150바이트의 반환 데이터를 읽습니다. [63/64 규칙](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-150.md)으로 인해, `nonblockingLzReceive()`가 할당된 가스를 모두 사용하면 남은 가스의 1/64만이 실패한 메시지를 저장하는 데 남게 됩니다. LayerZeroImpl은 엔드포인트에 600k 가스를 전달합니다:

```solidity
        endpoint.send{value: msg.value}(
            _dstChainId,
            abi.encodePacked(dstCommLayer[uint16(_dstChainId)], address(this)),
            _payload,
            payable(refundAd),
            address(0x0),
            abi.encodePacked(uint16(1), uint256(600000)) // 600k
        );
```

63/64 가스를 모두 사용하면 약 9000 가스만 남게 되는데, 0에서 0이 아닌 값으로의 단일 SSTORE 비용이 22.1k 가스이므로 실패한 메시지를 저장하기에 불충분합니다. 이로 인해 Endpoint try/catch 처리에서 revert가 발생합니다.

```solidity
        try ILayerZeroReceiver(_dstAddress).lzReceive{gas: _gasLimit}(_srcChainId, _srcAddress, _nonce, _payload) {
            // success, do nothing, end of the message delivery
        } catch (bytes memory reason) {
            // revert nonce if any uncaught errors/exceptions if the ua chooses the blocking mode
            storedPayload[_srcChainId][_srcAddress] = StoredPayload(uint64(_payload.length), _dstAddress, keccak256(_payload));
            emit PayloadStored(_srcChainId, _srcAddress, _dstAddress, _nonce, _payload, reason);
        }
    }
```

이 부분은 페이로드를 저장하고 채널을 차단하는 부분입니다. 가스가 여기서 gasLimit으로 제한되므로 `storedPayload`에 실패를 저장할 가스가 부족할 위험은 없으며, Relayer는 `lzReceive()` 전후의 로직을 실행하기 위해 약간의 추가 버퍼를 제공할 것으로 예상됩니다.

악의적인 DAO 소유자는 커뮤니티 입장을 제한(gate)하기 위해 토큰의 큰 배열을 사용하여 할당된 가스를 소모함으로써 이를 악용할 수 있습니다.

- ERC20 DAO 소유자인 Bob은 토큰 게이팅을 위해 과도하게 큰 토큰 목록을 설정합니다.

- Bob은 DAO 토큰을 구매하기 위해 `crossChainBuy()`를 호출하여 다음 호출 스택을 트리거합니다:
  - `commLayer.sendMsg()`
    - `endpoint.send()`
      - `commLayer.lzReceive()`
- `lzReceive()`는 `_nonblockingLzReceive()`를 호출하고, 이는 `factory.crossChainMint()`를 호출합니다.
- `crossChainMint()`는 모든 토큰을 반복하며, 큰 배열로 인해 "Out Of Gas" 오류가 발생합니다.
- `_blockingLzReceive()`가 실패하고 불충분한 가스로 실패한 메시지를 저장하려고 시도합니다.
- revert는 Endpoint try/catch 처리로 전달되어 통로가 차단되는 결과를 초래합니다.

## 권고 사항

예를 들어 10개와 같이 소유자가 추가할 수 있는 토큰의 수를 제한하십시오:

```solidity
    function setupTokenGating(
        address[] calldata _tokens,
        Operator _operator,
        uint256[] calldata _value,
        address payable _daoAddress
    ) external payable onlyAdmins(daoDetails[_daoAddress].gnosisAddress) {
        require(_value.length == _tokens.length, "Length mismatch");
        require(_tokens.length <= 10, "Large array");

        tokenGatingDetails[_daoAddress] = TokenGatingCondition(_tokens, _operator, _value);

        daoDetails[_daoAddress].isTokenGatingApplied = true;
    }
```

# [H-05] 잘못된 값이 commLayer로 전송됨

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`factory.createERC20DAO()` 함수는 LayerZero를 사용하여 크로스체인 DAO를 생성합니다. 사용자는 여러 체인에 분배될 msg.value를 지정할 수 있습니다. 예를 들어 5개의 체인이 있고 msg.value가 1000이라면 각 체인은 200을 받습니다. 이 값은 commLayer로 전달됩니다.

\_depositChainIds 배열은 체인 간의 분배를 관리하기 위한 것입니다. 예를 들어 사용자가 기본 체인과 2개의 보조 체인에 DAO를 생성하려는 경우:

- Bob은 기본 체인(Ethereum)과 2개의 보조 체인(Binance 및 Arbitrum One)에 DAO를 생성하고 싶어 합니다.
- Bob은 각 보조 체인에 250의 value를 전달하고 싶으므로 500을 전달해야 합니다.
- depositChainIds 배열은 다음과 같습니다:

```solidity
uint256[] memory depositChainIds = new uint256[](3);
        depositChainIds[0] = 1; // Ethereum
        depositChainIds[1] = 56; // Binance
        depositChainIds[2] = 42161; // Arbitrum One
```

하지만 두 가지 문제가 있습니다:

1. **잘못된 나눗셈:** 값 계산 시 2 대신 3으로 나누어 기본 체인을 잘못 포함합니다.
2. **감소하는 값:** \_depositChainIds.length는 일정하게 유지되는데 반복할 때마다 값은 줄어듭니다. 예를 들어:

- 첫 번째 반복:

```
i = 0 (i < _depositChainIds.length - 1)
value = msg.value / _depositChainIds.length = 500 / 3 = 166
```

- 두 번째 반복:

```
i = 1 (i < _depositChainIds.length - 1)
value = msg.value / _depositChainIds.length = 334 / 3 = 111
```

의도한 500 대신 277(166 + 111)만 전달됩니다. 나머지 223은 factory에 남게 되며 소유자만이 출금할 수 있습니다.

## 권고 사항

createERC20DAO() 및 createERC721DAO() 함수의 값 계산을 업데이트하십시오:

```solidity
                ICommLayer(commLayer).sendMsg{value: (msg.value - fees) / (_depositChainIds.length - (i + 1))}(
                    commLayer, _payload, abi.encode(_depositChainIds[i + 1], msg.sender)
                );
```

- 첫 번째 반복:

```
i = 0 (i < _depositChainIds.length - 1)
value = msg.value / (_depositChainIds.length - (i + 1)) = 500 / 2 = 250
```

- 두 번째 반복:

```
i = 1 (i < _depositChainIds.length - 1)
value = msg.value / (_depositChainIds.length - (i + 1)) = 250 / 1 = 250
```

# [H-06] 토큰 게이트 에어드랍이 ERC20 DAO에서 작동하지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`Claim` 컨트랙트에서 토큰을 `claim()`(청구)하는 함수는 먼저 `daoToken`이 `supportsInterface()`를 통해 인터페이스를 지원하는지 확인합니다:

```solidity
    if (claimSettingsMemory.permission == CLAIM_PERMISSION.TokenGated) {
@>      if (IERC20Extended(claimSettingsMemory.daoToken).supportsInterface(0xd9b67a26)) {
            if (
                IERC20Extended(claimSettingsMemory.daoToken).balanceOf(msg.sender, _tokenId)
                    < claimSettingsMemory.tokenGatingValue
            ) revert InsufficientBalance();
        } else {
            if (IERC20(claimSettingsMemory.daoToken).balanceOf(msg.sender) < claimSettingsMemory.tokenGatingValue) {
                revert InsufficientBalance();
            }
        }
```

문제는 `ERC20DAO`가 `supportsInterface()` 함수를 구현하지 않으므로 ERC20 DAO 토큰에 대해 호출될 때마다 되돌려진다(revert)는 것입니다.

`ERC721DAO`는 `supportsInterface()`를 구현하는 `ERC165Upgradeable`을 상속하는 `ERC721Upgradeable`을 상속하므로 이 문제가 없다는 점에 유의하십시오.

## 권고 사항

`의도가 DAO 토큰만 허용하는 것이라면` `ERC20DAO`가 `ERC165Upgradeable`을 상속하도록 만드는 것으로 충분합니다.

에어드랍 잔액을 확인하는 데 다른 ERC20 토큰도 사용될 것으로 예상되는 경우, 사용 시 되돌리지 않는 [`ERC165Checker::supportsERC165()`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/introspection/ERC165Checker.sol#L22)를 통해 인터페이스 지원을 확인하는 것이 좋습니다.

# [H-07] 예치 금액의 불정확한 검증

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

모든 민팅 작업 `buyGovernanceTokenERC20DAO()`, `buyGovernanceTokenERC721DAO()` 및 `crossChainMint()`에는 예치된 토큰이 총 모금액을 나타내는 특정 한도 미만인지 확인하는 검사가 있습니다:

```solidity
// For ERC20 DAOs
if (daoBalance + _totalAmount > (_daoDetails.pricePerToken * _daoDetails.distributionAmount) / 1e18) {
    revert AmountInvalid("daoBalance", daoBalance + _totalAmount);
}

For ERC721 DAOs
checkAmountValidity(daoBalance, _totalAmount, _daoDetails.pricePerToken,
_daoDetails.distributionAmount);

function checkAmountValidity(uint256 _daoBalance, uint256 _totalAmount,
uint256 _pricePerToken, uint256 _distributionAmount
  ) internal pure {
    if (_distributionAmount != 0) {
        uint256 _maxAllowedAmount = _pricePerToken * _distributionAmount;
        if (_daoBalance + _totalAmount > _maxAllowedAmount) {
            revert AmountInvalid("daoBalance", _daoBalance + _totalAmount);
        }
    }
}
```

이러한 모든 검사가 `daoBalance`에 의존하고 있음에 유의하십시오. 이는 DAO 계약이 가진 특정 순간의 잔액을 나타냅니다:

```solidity
    uint256 daoBalance;
    if (daoDetails[_daoAddress].depositTokenAddress == NATIVE_TOKEN_ADDRESS) {
        daoBalance = _daoAddress.balance;
    } else {
        daoBalance = IERC20(daoDetails[_daoAddress].depositTokenAddress).balanceOf(_daoAddress);
    }
```

DAO 계약의 토큰 잔액은 총 모금액을 계산하는 데 필요한 시간 경과에 따른 총 예치 토큰을 반영하지 않으므로 문제가 됩니다. 이 값은 DAO가 토큰을 받으면 올라갈 수도 있고, 토큰이 밖으로 이동하면 내려갈 수도 있습니다.

게다가 구성에 따라 예치된 토큰이 DAO가 아닌 Safe 지갑으로 갈 수도 있습니다. 그런 경우 DAO 잔액은 0일 수 있으며 한도가 전혀 적용되지 않을 것입니다.

## 권고 사항

DAO 계약의 임시 잔액을 추적하는 대신 총 예치 토큰을 저장 변수(storage variable)로 추적하고 이를 사용하여 검증을 수행하십시오.

# [H-08] 예치 토큰이 네이티브 토큰일 때 크로스체인 예치 실패

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`crossChainMint()` 함수는 `_daoBalance`를 다음과 같이 계산합니다:

```solidity
uint256 _daoBalance = IERC20(_daoDetails.depositTokenAddress).balanceOf(_daoAddress);
```

문제는 크로스체인 DAO가 네이티브 토큰(계약 내에서 주소: `0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE`로 정의됨)으로 배포될 수 있다는 점입니다.

따라서 사용자가 크로스체인 DAO 토큰을 구매하려고 할 때마다 토큰을 예치하겠지만, 대상 체인에서의 트랜잭션은 존재하지 않는 계약에 대해 `balanceOf()` 호출을 수행하려고 하므로 항상 되돌려질(revert) 것입니다.

## 권고 사항

크로스체인 DAO가 네이티브 토큰으로 배포된 경우 네이티브 토큰 잔액을 확인하십시오.

# [H-09] DAO 생성자가 생성 수수료 지불을 피할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`createERC20DAO()` 및 `createERC721DAO()` 모두 `checkCreateFeesSent()`를 통해 수수료로 충분한 네이티브 토큰이 전송되었는지 확인합니다:

```solidity
function checkCreateFeesSent(uint256[] calldata _depositChainIds) internal returns (uint256) {
    uint256 fees = createFees * _depositChainIds.length;
    require(msg.value >= fees, "Insufficient fees");
    return fees;
}
```

문제는 `_depositChainIds.length == 0`일 때 생성 수수료가 지불되지 않는다는 것입니다. 언급된 함수들에 이를 방지하는 확인이 없기 때문에 가능합니다.

게다가 트랜잭션을 되돌릴 수 있는 범위를 벗어난 오류(out-of-bounds error)를 방지하는 명시적인 `_depositChainIds.length != 0` 조건이 있습니다:

```solidity
if (_depositChainIds.length != 0) {
    for (uint256 i; i < _depositChainIds.length - 1;) {
        // ...
        ICommLayer(commLayer).sendMsg{value: (msg.value - fees) / _depositChainIds.length}(
            commLayer, _payload, abi.encode(_depositChainIds[i + 1], msg.sender)
        );
    // ...
}
```

## 권고 사항

`require(_depositChainIds.length > 0)`와 같은 확인을 통해 DAO가 소스 체인에만 배포될 때 생성 수수료가 지불되는지 검증하십시오.

# [H-10] 사용자 주소에 다른 체인에서 액세스하지 못할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

스마트 지갑이 하나의 체인에 배포되어 특정 주소를 가지고 있습니다. 하지만 다른 체인에서 동일한 주소로 스마트 지갑을 배포하는 것이 불가능할 수 있습니다. 예를 들어, 배포자(deployer)가 대상 체인에서 사용할 수 없거나, 동일한 솔트/논스를 설정할 수 없는 경우입니다. 또한 zkSync와 같은 체인에서는 `create` 작업이 동일한 배포 매개변수로도 항상 다른 주소로 이어집니다. 이러한 경우 사용자는 두 체인 모두에서 동일한 주소를 제어할 수 없게 됩니다.

일부 크로스체인 함수는 소스 체인의 동일한 주소가 대상 체인에서도 제어될 것이라고 가정합니다.

`crossChainBuy()`는 DAO 토큰의 수신자를 `msg.sender`로 설정합니다. 토큰은 그들이 제어하지 않는 주소로 전송될 수 있습니다.

`createERC20DAO()` 및 `createERC721DAO()`는 크로스체인 Safe에 대해 소스 체인과 동일한 `admins`를 설정합니다. 따라서 관리자는 Safe를 제어할 수 없으며, DAO 내의 잠긴 자금을 전송할 수 없게 됩니다(다른 관리 기능 중에서).

## 권고 사항

- `crossChainBuy()`의 경우: 간단한 완화 조치는 EOA(외부 소유 계정)에 대해서만 크로스체인 구매 작업을 허용하는 것입니다. 더 강력하지만 복잡한 완화 조치는 사용자가 대상 체인에서 특정 수신자를 설정하도록 허용하는 것입니다. 그러나 이는 다른 사용자를 대신하여 토큰을 발행할 가능성을 열어주므로, 이러한 리팩토링의 부작용을 고려해야 합니다(현재는 불가능함).
- `createERC20DAO()`의 경우: DAO 생성자가 배포된 각 크로스체인 DAO에 대해 특정 `admins`를 제공할 수 있도록 허용하십시오.

# [H-11] 실패한 크로스체인 트랜잭션에 대한 환불 및 재시도 메커니즘 없음

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

크로스체인 트랜잭션을 수행할 때, 특정 상황이나 특정 값에 대해 대상 체인에서 트랜잭션이 항상 되돌려질 수 있습니다.

사용자는 소스 체인에서 토큰을 예치하거나 수수료를 지불하지만, 대상 체인에서 이러한 되돌림이 발생하면 잃게 되며 복구할 수 없습니다.

크로스체인 매수 작업의 경우: 사용자는 `crossChainBuy()`를 통해 소스 체인에 토큰을 예치합니다. `crossChainMint()`가 되돌려지면 DAO 토큰이 발행되지 않습니다. 이는 여러 가지 이유로 발생할 수 있습니다:

- 금액이 유효하지 않은 경우 (DAO 잔액, 토큰 가격 및 DAO 체인의 분배 금액에 따라 다름)
- `maxTokensPerUser`를 초과하는 경우
- `distributionAmount`를 초과하는 경우
- 사용자의 총 예치금이 한도 미만이거나 초과인 경우
- 사용자가 화이트리스트에 없는 경우
- 토큰 게이팅이 적용된 경우
- 사용자 오류

이러한 조건의 대부분은 이동하는 값에 의존하며, 소스 체인 트랜잭션을 보낼 때의 오프체인 유효성 검사는 참일 수 있지만 크로스체인 트랜잭션이 실행될 때는 유효하지 않을 수 있다는 점에 유의하는 것이 중요합니다. 진실의 원천은 궁극적으로 DAO 계약이 배포된 체인에 있으므로 소스 체인에서의 유효성 검사만으로는 충분하지 않습니다.

또한 이론적으로 크로스체인 DAO 배포에서도 발생할 수 있지만 가능성은 낮습니다. `decimals()`를 조회할 수 없는 유효하지 않은 `_depositTokenAddress`에서 되돌림이 발생할 수 있습니다(물론 유효하지 않은 주소로 배포하는 것은 의미가 없지만). 다른 확인은 `_createERC20DAO()` 또는 `_createERC721DAO()`에서 수행되지만 소스 체인과 동일한 값을 사용하므로 원래 트랜잭션에서 되돌려졌을 것입니다. 대상 체인의 다른 문은 되돌려지면 안 되지만, 이 보고서에 설명된 시나리오로 이어질 수 있음을 명심하십시오.

## 권고 사항

예치금은 냈지만 DAO 토큰을 받지 못한 사용자를 위한 환불 메커니즘을 제공하십시오. 또한 일시적으로 되돌려질 수 있는 메시지를 재시도할 수 있도록 허용하는 것도 고려하십시오.

# [H-12] 공격자가 크로스체인 DAO의 상대측 배포를 차단할 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`Deployer`의 `deploySAFE()` 함수는 `SafeProxyFactory::createProxyWithNonce()`를 통해 DAO를 위한 Safe를 생성하려고 시도합니다. `try/catch` 패턴을 사용하지만, 해당 체인에서 동일한 `nonce`를 가진 다른 Safe가 미리 생성된 경우 항상 되돌려집니다(revert).

`catch` 블록 내부의 `revert`가 전체 트랜잭션을 되돌리게 만드는 방식에 유의하십시오:

```solidity
uint256 nonce = getNonce(_daoAddress);
do {
    try ISafe(safe).createProxyWithNonce(singleton, _initializer, nonce) returns (address _deployedSafe) {
        SAFE = _deployedSafe;
    } catch Error(string memory reason) {
        nonce = getNonce(_daoAddress);
        revert SafeProxyCreationFailed(reason);
    } catch {
        revert SafeProxyCreationFailed("Safe proxy creation failed");
    }
} while (SAFE == address(0));
```

공격자는 크로스체인 트랜잭션이 LayerZero에 의해 처리되는 동안 대상 체인에서 [SafeProxyFactory::createProxyWithNonce()](https://github.com/safe-global/safe-smart-account/blob/a9e3385bb38c29d45b3901ff7180b59fcee86ac9/contracts/proxies/SafeProxyFactory.sol#L56)를 직접 호출할 수 있습니다.

피해자 `_daoAddress()`에 대해 생성된 동일한 `nonce`와 동일한 `initialize` 매개변수를 사용하면 동일한 주소로 Safe가 생성될 것이 보장됩니다.

이는 `createCrossChainERC20DAO()`에서 `deploySAFE()`를 실행하려고 할 때 크로스체인 트랜잭션을 되돌리게 만들기 때문에 문제가 됩니다:

```solidity
function createCrossChainERC20DAO(...) {
    address _safe = IDeployer(deployer).deploySAFE(_admins, _safeThreshold, _daoAddress);

    _createERC20DAO(...);
    ccDetails[_daoAddress] = CrossChainDetails(_commLayerId, _depositChainIds, false, msg.sender, _onlyAllowWhitelist);
}
```

대상 체인에서 DAO 상대측의 생성을 완료하는 것이 불가능해지며, 거기서 매수 작업을 사용할 수 없게 됩니다.

참고: 이 발견에는 소스 체인에서의 트랜잭션을 프론트러닝하여 DAO 생성을 완전히 방지하는 이차적인, 덜 심각한 영향도 있습니다. 이 경우 어떤 체인에서도 DAO가 생성되지 않았으므로 위험은 없지만, 괴롭힘(griefing) 공격으로 간주될 수 있습니다. 권고 사항은 이 시나리오도 다룹니다.

## 권고 사항

가능한 해결책 중 하나는 `try/catch` 블록을 제거하고, Safe [프록시](https://github.com/safe-global/safe-smart-account/blob/a9e3385bb38c29d45b3901ff7180b59fcee86ac9/contracts/proxies/SafeProxyFactory.sol#L15)의 예상 주소를 [미리 계산(precompute)](https://solidity-by-example.org/app/create2/)하여 존재하는지(코드가 있는지) 확인하고, 없는 경우에만 생성하는 것입니다.

해당 주소에 대한 계약이 이미 배포된 경우, 해당 `daoAddress`에 대해 예상된 관리자 및 기타 매개변수가 초기화 프로그램에 설정된 상태로 생성되었어야 하므로 위험을 초래하지 않아야 합니다.

# [M-01] 크로스체인 메시지에 하드코딩된 가스 사용

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

LayerZeroImpl 및 LayerZeroDeployer 계약은 LayerZero 브리지를 통해 크로스체인 메시지를 보낼 때 하드코딩된 가스양을 사용합니다. 문제는 대상 체인마다 다른 가스양을 필요로 할 수 있고 메시지 자체도 다른 가스양을 유발할 수 있다는 것입니다. 사용자가 DAO를 생성할 때 여러 목록을 지정하며, 해당 목록의 길이는 크로스체인 메시지 가스 비용에 영향을 줄 수 있습니다. 결과적으로 일부 체인 및 일부 DAO에 대해 크로스체인 DAO 배포가 손실될 수 있습니다.

## 권고 사항

`baseGas`, `intrinsicGas` 및 `executionGas`를 고려하여 필요한 가스양을 추정하는 메커니즘을 갖추십시오.

# [M-02] Claim 계약에서 `hasAllowanceMechanism`이 설정된 경우 관리자가 청구 잔액을 늘릴 수 없음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

사용자가 DAO를 생성할 때 `claimBalance`를 지정하고 나중에 늘릴 수 있습니다. 문제는 `hasAllowanceMechanism`이 설정되면 `depositTokens()`가 이를 허용하지 않기 때문에 `claimBalance`를 늘릴 방법이 없다는 것입니다. 이로 인해 DAO 생성 중에 관리자가 설정한 `claimBalance`가 일정하게 유지되며 에어드랍 총액을 늘릴 수 없게 됩니다.

## 권고 사항

`hasAllowanceMechanism`이 설정된 경우 관리자가 `claimBalance`를 늘릴 수 있도록 허용하십시오.

# [M-03] ERC20DAO의 `mintGTToAddress()`가 `distributionAmount` 한도를 확인하지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

ERC721 및 Factory 계약의 `mintGTToAddress()`는 `distributionAmount` 한도를 확인하지만, ERC20DAO는 `distributionAmount` 한도를 확인하지 않아 관리자가 무제한으로 토큰을 발행할 수 있습니다.

## 권고 사항

ERC20DAO의 `mintGTToAddress()`에서 `distributionAmount`를 확인하십시오.

# [M-04] 거버넌스 구매 기능에서 소유자 몫은 총 지불액에서 공제되어야 함

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

사용자가 거버넌스 토큰을 구매하려고 할 때 코드는 총액을 계산한 다음 소유자 몫을 계산합니다. 문제는 코드가 사용자로부터 `totalAmount + ownerShare`를 전송한다는 것입니다. 따라서 사용자는 코드가 총액에서 소유자 몫을 뺴지 않기 때문에 토큰의 실제 가격보다 더 많이 지불하게 됩니다.

## 권고 사항

코드는 `totalAmount`를 계산하고 사용자로부터 해당 금액만 전송해야 합니다. `ownerShare`는 추가 금액이 아니라 총액의 일부여야 합니다.

# [M-05] 크로스체인 배포 실패

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

코드가 크로스체인 DAO 배포 메시지를 생성하려고 할 때, 총 ETH 금액을 크로스체인 메시지 간에 균등하게 나눕니다. 문제는 대상 체인마다 다른 크로스체인 수수료가 필요할 수 있다는 것입니다. 코드는 사용자가 각 크로스체인 메시지에 대해 브리지 수수료 금액을 지정할 수 있도록 허용해야 합니다. 현재 설계에서는 사용자가 최대 브리지 수수료 금액을 기준으로 지불해야 합니다.

## 권고 사항

사용자가 각 체인에 대한 크로스체인 브리지 수수료 금액을 지정할 수 있도록 허용하십시오.

# [M-06] 18자리 이상의 소수점을 가진 토큰은 지원되지 않음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`amountToSD()` 및 `amountToLD()` 함수의 현재 설계는 18자리 이하의 소수점을 가진 토큰으로 호환성을 제한합니다. 18자리 이상의 소수점을 가진 토큰을 `depositTokenAddress`로 사용하여 DAO를 생성하면 `convertRate` 계산 중 언더플로 문제로 인해 DAO 생성이 실패합니다. 이는 재정적 위험을 초래하지는 않지만, 더 넓은 DeFi 생태계 내에서 팩토리(Factory)의 적응성을 제한하여 해당 토큰과 함께 사용하는 것을 방지합니다.

예를 들어, [24자리 소수점을 가진 YAMv2](https://etherscan.io/token/0xaba8cac6866b83ae4eec97dd07ed254282f6ad8a)와 같은 토큰은 계산이 18 - 24를 시도하게 하여 언더플로 및 실패한 DAO 생성으로 이어집니다.

## 권고 사항

소수점이 18보다 큰지 확인하고 소수점 차이만금 나누어 값을 정규화하는 것을 고려하십시오. 구현 예시는 다음과 같습니다:

> https://github.com/code-423n4/2022-06-connext-findings/issues/204#issuecomment-1170453579

# [M-07] 크로스체인 및 KYC 사용자는 수수료의 일부만 지불하게 됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

팩토리 컨트랙트는 다음과 같이 크로스체인 DAO 및 KYC 사용자에 대한 수수료를 계산합니다:

```solidity
    function checkDepositFeesSent(address _daoAddress, uint256 _totalAmount) internal {
        if (ccDetails[_daoAddress].depositChainIds.length > 1 || isKycEnabled[_daoAddress]) {
            require(msg.value >= _totalAmount + ((depositFees * platformFeeMultiplier) / 100), "Insufficient fees");
        } else {
            require(msg.value >= _totalAmount + depositFees, "Insufficient fees");
        }
    }
```

예를 들어, `depositFees`가 100이고 `platformFeeMultiplier`가 125인 경우, 팩토리는 DAO가 크로스체인이거나 KYC된 경우 125를 수수료로 청구합니다. 스폰서는 KYC가 별도의 기능이며 KYC가 활성화된 경우 팩토리가 추가 수수료를 청구해야 한다고 지적했습니다. 그러나 현재는 그렇지 않습니다. DAO가 크로스체인이면서 KYC된 경우, 팩토리는 `platformFeeMultiplier`를 두 번이 아닌 한 번만 적용합니다.

## 권고 사항

함수를 다음과 같이 업데이트하는 것을 고려하십시오:

```solidity
    function checkDepositFeesSent(address _daoAddress, uint256 _totalAmount) internal {
        uint256 fees = (depositFees * platformFeeMultiplier) / 100;
        if (ccDetails[_daoAddress].depositChainIds.length > 1 && isKycEnabled[_daoAddress]) {
            require(msg.value >= _totalAmount + (fees * 2)), "Insufficient fees";
        } else if (ccDetails[_daoAddress].depositChainIds.length > 1 || isKycEnabled[_daoAddress]) {
            require(msg.value >= _totalAmount + fees, "Insufficient fees");
        } else {
            require(msg.value >= _totalAmount + depositFees, "Insufficient fees");
        }
    }
```

# [M-08] 토큰 구매 시 확인되지 않은 저수준 호출 (Unchecked Low-level Call)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`_buyGovernanceTokenERC20DAO()` 및 `_buyGovernanceTokenERC721DAO()` 함수에서 `gnosisAddress` 및 `ownerAddress`에 대한 저수준 호출의 성공 여부가 검증되지 않습니다. 이러한 감독 소홀은 이러한 호출이 실패할 때 실패가 눈에 띄지 않게 지나가며, 자금이 `gnosisAddress` 또는 `ownerAddress`에 분배되지 않고 팩토리에 남게 됨을 의미합니다. 그 결과 양측 모두 지불 몫을 받지 못합니다.

## 권고 사항

`_buyGovernanceTokenERC20DAO()` 및 `_buyGovernanceTokenERC721DAO()` 함수 내에서 저수준 호출의 반환 값을 확인하십시오. 호출이 실패하면 트랜잭션이 되돌려져야 합니다. 또한 가스 괴롭힘(gas griefing) 공격을 방지하기 위해 `ownerAddress`에 대한 호출의 가스 한도가 적절하게 설정되었는지 확인하십시오.

# [M-09] 사용자가 덩어리로 구매하여 소유자를 괴롭힐 수 있음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

팩토리 컨트랙트는 모든 ERC20 토큰을 지원하므로 DAO 생성자는 모든 토큰을 DAO의 지불 방법으로 추가할 수 있습니다. 사용자가 `buyGovernanceTokenERC20DAO()` 함수를 사용하여 토큰을 구매할 때, 소유자는 지불의 일부를 수수료로 받습니다:

```solidity
        uint256 _totalAmount = (_numOfTokensToBuy * _daoDetails.pricePerToken) / 1e18;

        if (_totalAmount == 0) {
            revert AmountInvalid("_numOfTokensToBuy", _totalAmount);
        }

        uint256 ownerShare = (_totalAmount * _daoDetails.ownerFeePerDepositPercent) / (FLOAT_HANDLER_TEN_4);
```

그러나 사용자는 소규모 분할 구매를 통해 `ownerShare`가 0으로 내림되도록 이 계산를 악용할 수 있습니다. 이 문제는 [2자리 소수점](https://etherscan.io/token/0x056Fd409E1d7A124BD7017459dFEa2F387b6d5Cd#readContract#F3)을 가진 [GUSD](https://etherscan.io/token/0x056Fd409E1d7A124BD7017459dFEa2F387b6d5Cd)와 같이 소수점 정밀도가 낮은 토큰에서 특히 문제가 됩니다. 다음 시나리오를 고려하십시오:

- DAO 설정:
  - `Dao.depositTokenAddress = GUSD`
  - `Dao.ownerFeePerDepositPercent = 100`
  - `pricePerToken = 10e2`
- 익스플로잇 실행:
  - 버그를 알고 있는 악의적인 사용자 Bob은 시스템을 악용하려고 합니다.
  - Bob은 `numOfTokensToBuy = 9.9e16`으로 `buyGovernanceTokenERC20DAO()`를 호출합니다.
  - 계산:
    - `_totalAmount = (9.9e16 * 10e2) / 1e18 = 99`
    - `ownerShare = (99 * 100) / 10000 = 0`
  - ownerShare는 0으로 내림되며, 이는 소유자가 9.9e16 토큰 구매마다 수수료를 받지 못함을 의미합니다.
  - 트릭은 `_totalAmount * _daoDetails.ownerFeePerDepositPercent`가 `FLOAT_HANDLER_TEN_4` (즉 10,000)보다 작아야 한다는 것입니다.

## 권고 사항

이 익스플로잇을 방지하려면 다음과 같이 확인을 업데이트하는 것을 고려하십시오:

```solidity
        if (_totalAmount == 0 || (_totalAmount * _daoDetails.ownerFeePerDepositPercent) < FLOAT_HANDLER_TEN_4) {
            revert AmountInvalid("_numOfTokensToBuy", _totalAmount);
        }
```

# [M-10] 초기화가 소유자를 설정하기 위해 프론트러닝될 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

초기화 함수를 사용하는 계약은 배포 중에 프론트러닝 공격에 취약합니다. 공격자는 배포 프로세스를 가로채서 자신을 계약 소유자로 할당할 수 있는데, 이는 소유권이 msg.sender에게 부여되기 때문입니다. StationX는 손상된 계약을 재배포할 수 있지만 이는 금전적 비용을 초래합니다. 또한 StationX가 공격자에 의해 미리 초기화되었다는 사실을 깨닫지 못하고 계약을 배포하면 사용자는 처음부터 손상된 시스템과 알지 못한 채 상호 작용할 수 있습니다.

영향을 받는 계약은 다음과 같습니다:

- Deployer
- Emitter
- Factory
- ClaimEmitter
- ClaimFactory
- LayerZeroDeployer
- LayerZeroImpl

## 권고 사항

이 위험을 완화하려면 생성자에서 소유자를 설정하고 나열된 계약에 대해 initialize() 함수를 소유자 전용으로 제한하십시오.

# [M-11] 되돌림(Revert)으로 인해 배포를 위한 LayerZero 채널이 차단될 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`LayerZeroDeployer` 컨트랙트는 기본적으로 차단(blocking) 특성을 가진 LayerZero v1을 사용하며, 수신자 함수는 비차단 패턴을 구현하지 않습니다. 크로스체인 실행 중 되돌림이 발생하면 전체 채널이 차단되어 프로토콜 관리자가 `forceResume()`을 통해 잠금을 해제할 때까지 향후 DAO 배포가 방지됩니다.

이론적으로 `createCrossChainERC20DAO()` 및 `createCrossChainERC721DAO()`와 같은 크로스체인 호출은 되돌려지면 안 되지만, 공격자는 검증을 통과하고 소스 체인에서는 성공하지만 대상 체인에서는 성공하지 못하는 트랜잭션을 만들 수 있습니다.

한 가지 방법은 크로스체인 트랜잭션에 큰 `_admins` 배열을 전달하는 것입니다. 어느 시점에서 함수는 루프 크기로 인해 "Out of Gas" 오류와 함께 되돌려질 것입니다:

```solidity
address _safe = IDeployer(deployer).deploySAFE(_admins, _safeThreshold, _daoAddress);

for (uint256 i; i < _admins.length;) {
    IEmitter(emitterAddress).newUserCC(
        _daoAddress, _admins[i], _depositTokenAddress, 0, block.timestamp, 0, true
    );

    unchecked {
        ++i;
    }
}
```

소스 체인에서 공격자는 원하는 만큼의 가스를 보낼 수 있으므로 꽤 큰 수의 `_admins`로도 트랜잭션이 성공할 수 있습니다. 문제는 대상 체인에서 가스가 `2_000_000`으로 제한된다는 점입니다:

```solidity
    endpoint.send{value: msg.value}(
        uint16(_dstChainId),
        abi.encodePacked(dstCommLayer[uint16(_dstChainId)], address(this)),
        _payload,
        payable(_refundAd),
        address(0x0),
@>      abi.encodePacked(uint16(1), uint256(2_000_000))
    );
```

따라서 트랜잭션을 소스 체인에서는 성공적으로 실행시키고 대상 체인에서는 되돌려지게 하여, LayerZero v1의 기본 동작으로 인해 모든 후속 크로스체인 트랜잭션을 차단하는 것이 가능합니다.

## 권고 사항

`createERC20DAO()` 및 `createERC721DAO()`를 호출할 때 소스 체인에서 `_admins` 배열이 최대 한도 미만인지 확인하십시오. 또한 크로스체인 배포에 비차단 패턴을 적용하는 것을 고려하십시오.

# [M-12] 비차단 LayerZero 크로스체인 매수 작업이 차단될 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

크로스체인 매수 작업이 수행되면 대상 체인에서 실행하기 위해 고정된 `600_000` 가스 제한으로 LayerZero 메시지가 전송됩니다:

```solidity
    endpoint.send{value: msg.value}(
        _dstChainId,
        abi.encodePacked(dstCommLayer[uint16(_dstChainId)], address(this)),
        _payload,
        payable(refundAd),
        address(0x0),
@>      abi.encodePacked(uint16(1), uint256(600_000))
    );
```

LayerZero v1은 기본적으로 차단 동작을 가지며, `LayerZeroImpl`은 코드를 통해 비차단 패턴을 구현하려고 시도합니다:

```solidity
    function _blockingLzReceive(uint16 _srcChainId, bytes memory _srcAddress, uint64 _nonce, bytes memory _payload) internal {
@>      (bool success, bytes memory reason) = address(this).excessivelySafeCall(
            gasleft(),
            150,
            abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload)
        );

        if (!success) {
@>          _storeFailedMessage(_srcChainId, _srcAddress, _nonce, _payload, reason);
        }
    }

    function _storeFailedMessage(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload,
        bytes memory _reason
    ) internal virtual {
@>      failedMessages[_srcChainId][_srcAddress][_nonce] = keccak256(_payload);
@>      emit MessageFailed(_srcChainId, _srcAddress, _nonce, _payload, _reason);
    }
```

이론적으로 이는 크로스체인 트랜잭션 실행을 시도하고 실패 시 나중에 처리하기 위해 실패한 메시지를 저장함으로써 예상대로 작동해야 합니다.

그러나 어떤 이유로 `failedMessages[_srcChainId][_srcAddress][_nonce] = keccak256(_payload);` 문에서 트랜잭션이 되돌려지면, LayerZero는 전체 크로스체인 트랜잭션을 실패한 것으로 간주하고 향후 메시지에 대해 채널을 차단합니다.

0에서 0이 아닌 값으로 값을 설정하는 데는 최소 20,000 가스가 듭니다. 따라서 그 시점까지 남은 가스가 이보다 적으면 트랜잭션이 되돌려지고 LayerZero가 채널을 차단합니다.

앞서 언급했듯이 매수 메시지는 고정된 600,000 가스 제한으로 실행됩니다. [EIP-150](https://eips.ethereum.org/EIPS/eip-150)에 따라 63/64 또는 약 590k가 외부 호출로 전달됩니다. `excessivelySafeCall()`에서 모든 가스가 사용되면 나머지 실행을 위해 약 9,000 가스가 남게 됩니다. 이는 `failedMessages` 저장 + `MessageFailed` 이벤트 방출을 커버하기에 충분하지 않으며 트랜잭션이 되돌려질 것입니다.

`excessivelySafeCall()`은 `crossChainMint()`를 호출하며, 정상적인 사용으로는 해당 함수가 590k 가스를 소비하는 것이 불가능해야 하지만, 공격자는 예를 들어 큰 루프에서 반복적으로 해싱하여 가스를 소비하는 충분히 큰 `_merkleProof` 배열을 제공할 수 있습니다. 또한 토큰을 발행할 때 저장하기에 너무 비싼 충분히 큰 `_tokenURI`를 보낼 수도 있습니다.

또한 `excessivelySafeCall()` 내부에서 모든 가스를 소비하지 않더라도, 큰 페이로드를 저장하고 큰 페이로드로 메시지를 방출하는 것은 20,000 가스보다 훨씬 더 많은 비용이 들며 트랜잭션을 되돌리게 만들 수 있습니다.

## 권고 사항

메시지의 페이로드가 사용 사례 및 제공된 가스 제한에 대해 합리적인 크기 미만인지 확인하십시오. 또한 크로스체인 트랜잭션을 보내기 전에 배열, 바이트 및 문자열의 최대 길이를 확인하는 것을 고려하십시오.

이 공격은 크로스체인 배포에도 영향을 미칠 수 있습니다. 거기에도 동일한 권고 사항을 적용하는 것이 좋습니다.

# [M-13] mintGTToAddress를 통한 민팅이 되돌려질 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`mintGTToAddress()`를 통해 ERC721 DAO 토큰을 발행할 때 사용자가 최대 한도보다 많이 갖게 되지 않는지 확인하는 검사가 수행됩니다:

```solidity
    uint256 length = _userAddress.length;
    for (uint256 i; i < length;) {
        for (uint256 j; j < _amountArray[i];) {
@>          if (balanceOf(_userAddress[i]) + _amountArray[i] > erc721DaoDetails.maxTokensPerUser) {
@>              revert MaxTokensMintedForUser(_userAddress[i]);
@>          }

            _tokenIdTracker += 1;
            _safeMint(_userAddress[i], _tokenIdTracker);
            _setTokenURI(_tokenIdTracker, _tokenURI[i]);
            unchecked {
                ++j;
            }
        }
    }
```

예를 들어 사용자가 NFT를 0개 가지고 있고 최대 한도가 10개이며 10개의 토큰이 발행되면 `0 (잔액) + 10 (수량) <= 10 (최대 한도)`이므로 성공해야 합니다.

문제는 이 확인이 각 민팅 후에 수행된다는 것입니다. 따라서 다음 반복에서 잔액은 1이 되고 `1 (잔액) + 10 (수량) <= 10 (최대 한도)`은 거짓이 됩니다. 이로 인해 트랜잭션이 되돌려지고 토큰이 발행되지 않습니다.

## 권고 사항

`if` 확인을 `j` 루프 외부로 이동하는 것을 고려하십시오:

```diff
    uint256 length = _userAddress.length;
    for (uint256 i; i < length;) {
+       if (balanceOf(_userAddress[i]) + _amountArray[i] > erc721DaoDetails.maxTokensPerUser) {
+           revert MaxTokensMintedForUser(_userAddress[i]);
+       }
        for (uint256 j; j < _amountArray[i];) {
-           if (balanceOf(_userAddress[i]) + _amountArray[i] > erc721DaoDetails.maxTokensPerUser) {
-               revert MaxTokensMintedForUser(_userAddress[i]);
-           }

            _tokenIdTracker += 1;
            _safeMint(_userAddress[i], _tokenIdTracker);
            _setTokenURI(_tokenIdTracker, _tokenURI[i]);
            unchecked {
                ++j;
            }
        }
    }
```

# [M-14] ERC721 사용자당 최대 토큰 한도가 항상 1만큼 우회될 수 있음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`ERC721DAO`의 `transferFrom()` 및 `safeTransferFrom()` 함수 모두 사용자가 가질 수 있는 최대 토큰 양을 계산할 때 off-by-one 오류가 있습니다:

```solidity
require(balanceOf(to) <= erc721DaoDetails.maxTokensPerUser);
```

이는 `mintToken()`에서 수행되는 제한 검증과도 일치하지 않으며, `mintToken()`에서는 올바르게 적용됩니다.

이로 인해 사용자는 허용된 것보다 더 많은 토큰을 가질 수 있게 됩니다. 예를 들어 사용자당 토큰을 하나로 제한하려는 DAO는 대신 두 개를 가질 수 있도록 허용하게 됩니다.

## 권고 사항

`transferFrom()` 및 `safeTransferFrom()` 모두에 이 변경을 적용하는 것을 고려하십시오:

```diff
-   require(balanceOf(to) <= erc721DaoDetails.maxTokensPerUser);
+   require(balanceOf(to) < erc721DaoDetails.maxTokensPerUser);
```

# [M-15] 공격자가 제한 없이 EMITTER 역할을 필요로 하는 모든 이벤트를 방출할 수 있음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`Emitter` 컨트랙트에는 DAO 프록시에 `EMITTER` 역할을 부여하는 두 가지 함수가 있습니다. 이 역할은 팩토리 컨트랙트에서 `createERC20DAO()` 또는 `createERC721DAO()`를 통해 생성된 모든 DAO에 부여됩니다.

```solidity
    function createDaoErc20(..., address _proxy, ...) {
        _grantRole(EMITTER, _proxy);
    }

    function createDaoErc721(..., address _proxy, ...) {
        _grantRole(EMITTER, _proxy);
    }
```

문제는 DAO에 Safe가 임의의 데이터로 임의의 컨트랙트에 대한 호출을 실행할 수 있도록 하는 함수가 있다는 것입니다:

```solidity
    function updateProposalAndExecution(address _contract, bytes memory _data) external onlyGnosis(factoryAddress, address(this)) {
        (bool success,) = _contract.call(_data);
        require(success);
    }
```

공격자는 이를 활용하여 `Emitter` 컨트랙트를 호출하고 일부 오프체인 로직을 악용할 수 있는 기만적인 이벤트를 방출할 수 있습니다. 다음은 영향을 받는 26개 함수의 예입니다:

```solidity
    function deposited(address _daoAddress, address _depositor,
        address _depositTokenAddress, uint256 _amount, ...
    ) external onlyRole(EMITTER) {
        emit Deposited(_daoAddress, _depositor, _depositTokenAddress, _amount,
            _timestamp, _ownerFee, _adminShare);
    }
```

## 권고 사항

간단한 접근 방식은 `ERC20DAO` 및 `ERC721DAO` 모두 `updateProposalAndExecution()`에서 `Emitter` 컨트랙트에 대한 호출을 비활성화하는 것입니다.

```diff
    function updateProposalAndExecution(address _contract, bytes memory _data) external onlyGnosis(factoryAddress, address(this)) {
+       require(_contract != emitterContractAddress);
        (bool success,) = _contract.call(_data);
        require(success);
    }
```

# [L-01] DAO 컨트랙트가 `isGovernanceActive` 변수를 사용하지 않음

`isGovernanceActive` 변수가 코드에서 사용되지 않았습니다. 코드는 `isGovernanceActive`가 `False`로 설정된 경우 거버넌스 작업을 허용해서는 안 됩니다.

# [L-02] `updateProposalAndExecution()`이 ETH 전송을 지원하지 않음

`updateProposalAndExecution()` 함수는 DAO 작업을 실행하기 위한 것이지만 문제는 ETH 전송을 지원하지 않아 결과적으로 해당 DAO에서 일부 기능을 실행할 수 없다는 것입니다.

# [L-03] DAO 체인 목록에서 중복 항목 확인

DAO 체인 배포 목록에 중복 항목이 있는지 확인하는 절차가 없으며, 중복 항목이 있는 경우 해당 DAO에 대한 설정이 사이드 체인에서 두 번 설정되고 해당 설정이 동일하지 않을 수 있으므로 사이드 체인에서 DAO가 예기치 않은 동작을 할 수 있습니다. 코드는 동일한 대상 체인에 대해 두 개의 크로스체인 메시지를 생성하고 해당 메시지는 비동기적으로 수신되어 DAO에 대한 구성 변경을 일으킬 것입니다. 예를 들어 DAO의 예치 토큰이 사이드 체인에서 변경될 수 있습니다.

# [L-04] createERC20DAO 및 createERC721DAO의 잘못된 확인

`createERC20DAO()` 및 `createERC721DAO()` 함수에서 루프는 길이가 0이 아닌 경우에만 사용자의 예치 체인 ID를 반복합니다:

```solidity
        if (_depositChainIds.length != 0) {
            for (uint256 i; i < _depositChainIds.length - 1;) {
```

그러나 이 접근 방식에는 결함이 있습니다. `_depositChainIds.length == 1`일 때도 루프가 시작되는데, 이는 크로스체인 생성이 없음을 의미합니다. 이는 크로스체인 생성이 발생하지 않을 때 `createCCDao()` 이벤트가 방출되는 결과를 초래합니다. 다음과 같이 확인을 업데이트하는 것을 고려하십시오:

```solidity
        if (_depositChainIds.length > 1) {
            for (uint256 i; i < _depositChainIds.length - 1;) {
```

# [L-05] 악의적인 walletAddress가 모든 사용자 자금을 잠글 수 있음

청구 컨트랙트에는 `hasAllowanceMechanism` 불리언이 포함되어 있어 청구 생성자가 사용자가 지정된 지갑에서 직접 청구할 수 있도록 허용합니다. 그러나 이 `walletAddress`가 손상되어 허용량을 취소하면 사용자 자금이 잠깁니다. 또한 청구 생성자는 현재 이 주소를 업데이트할 수 없습니다. 이 문제를 해결하려면 `walletAddress`를 업데이트할 수 있는 함수를 추가하는 것을 고려하십시오.

# [L-06] claim()에서 확인되지 않은 저수준 호출

claim() 함수에서 저수준 호출의 성공 여부가 검증되지 않습니다. 호출이 실패하면 팩토리는 claimFee를 받지 못하고 수수료는 청구 컨트랙트에 잠긴 상태로 남습니다:

```solidity
        if (claimAmount[msg.sender] == 0) {
            if (msg.value != IFactory(factory).claimFee()) {
                revert InvalidAmount();
            }
            payable(factory).call{value: msg.value}("");
        }
```

아래와 같이 반환 값을 확인하는 것을 고려하십시오:

```solidity
            (bool success, ) = payable(factory).call{value: msg.value}("");
            require(success, "Call failed");
```

# [L-07] transfer 대신 call 사용

solidity `transfer()` 함수가 `disburseNative()`에서 네이티브 ETH 전송에 사용됩니다. transfer 및 send 함수는 고정된 2300 가스를 전달합니다. 역사적으로 재진입 공격을 방지하기 위해 가치 전송에 이러한 함수를 사용하는 것이 권장되었습니다. 그러나 EVM 명령어의 가스 비용은 하드 포크 중에 크게 변경될 수 있으며, 이는 가스 비용에 대해 고정된 가정을 하는 이미 배포된 계약 시스템을 망가뜨릴 수 있습니다. 예를 들어. EIP 1884는 SLOAD 명령어의 비용 증가로 인해 여러 기존 스마트 컨트랙트를 망가뜨렸습니다.

주소에 대해 더 이상 사용되지 않는 transfer() 함수를 사용하면 다음과 같은 경우 트랜잭션이 필연적으로 실패합니다:

- 수신자 스마트 컨트랙트가 payable 함수를 구현하지 않는 경우.
- 수신자 스마트 컨트랙트가 2300 가스 유닛 이상을 사용하는 payable 폴백을 구현하는 경우.
- 수신자 스마트 컨트랙트가 2300 가스 유닛 미만을 필요로 하는 payable 폴백 함수를 구현하지만 프록시를 통해 호출되어 호출의 가스 사용량이 2300을 초과하는 경우.
- 또한 일부 다중 서명 지갑의 경우 2300보다 높은 가스를 사용하는 것이 필수적일 수 있습니다.

transfer 대신 call을 사용하고 각 호출에 가스 제한을 추가하십시오.

# [L-08] Safe 초기화 프로그램에 잘못된 데이터 전송

Safe 초기화 프로그램은 `"0x"` 문자열 리터럴로 선언됩니다. 이는 빈 `bytes` 값으로 설정하는 것과 다릅니다.

이 값은 모듈을 설정하는 데 `data`를 사용합니다. 현재 코드베이스에는 모듈이 설정되어 있지 않으므로 위험을 초래하지 않지만, 코드가 모듈을 포함하도록 업데이트될 경우 문제를 방지하기 위해 수정하는 것이 좋습니다.

```solidity
    bytes memory _initializer = abi.encodeWithSignature(
        "setup(address[],uint256,address,bytes,address,address,uint256,address)",
        _admins,
        _safeThreshold,
        0x0000000000000000000000000000000000000000,
@>      "0x",
        0xf48f2B2d2a534e402487b3ee7C18c33Aec0Fe5e4,
        0x0000000000000000000000000000000000000000,
        0,
        0x0000000000000000000000000000000000000000
    );
```

`"0x"`를 `""`로 변경하는 것을 고려하십시오.

# [L-09] 사용자가 잘못된 tokenURI를 설정하면 예치금을 잃을 수 있음

`crossChainBuy()`를 통해 토큰을 구매할 때 사용자가 ERC721 DAO에 대해 빈 `tokenURI`를 설정하거나 ERC20 DAO에 대해 비어 있지 않은 `tokenURI`를 설정하면, 소스 체인에서는 트랜잭션이 성공하지만 대상 체인에서는 잘못된 유형의 DAO에 대해 토큰을 발행하려고 시도하므로 되돌려집니다.

트랜잭션이 예치금 없이 되돌려지도록 `crossChainBuy()`에서 사용자가 토큰을 구매하려는 DAO의 유형에 대해 `tokenURI`가 유효한지 확인하는 것을 고려하십시오.

# [L-10] 불필요한 receivePayload 호출

`LayerZeroDeployer` 및 `LayerZeroImpl`의 `lzReceive()` 함수에는 Layer Zero가 [테스트 목적](https://github.com/LayerZero-Labs/LayerZero/blob/a1fb11a3b9c0ac449291816e71eacede4e36613e/contracts/mocks/OmniCounter.sol#L45-L49)으로 사용하는 불필요한 문이 있습니다.

```solidity
if (keccak256(abi.encodePacked((_payload))) == keccak256(abi.encodePacked((bytes10("ff"))))) {
    endpoint.receivePayload(1, bytes(""), address(0x0), 1, 1, bytes(""));
}
```

두 컨트랙트 모두에서 이를 제거하는 것을 고려하십시오.

# [L-11] 총 NFT 공급량이 무제한인지 확인

현재 함수는 `_distributionAmount != 0`을 확인하지만, 무제한 공급을 알리는 해당 속성은 `isNftTotalSupplyUnlimited`입니다.

`_distributionAmount != 0` 대신 `isNftTotalSupplyUnlimited == true`를 확인하는 것을 고려하십시오. 이렇게 하면 사용자 오류를 방지할 수 있습니다.

# [L-12] 크로스체인 예치 메시지가 동일한 체인으로 전송될 수 있음

`crossChainBuy()` 함수는 크로스체인 예치가 동일한 체인으로 전송되지 않는지 검증하지 않습니다. `ccDetails[_daoAddress]`가 소스 체인에 대해 비어 있으므로 크로스체인 예치 수수료 승수를 피하기 위해 이를 시도할 수 있습니다:

```solidity
if (ccDetails[_daoAddress].depositChainIds.length > 1 || isKycEnabled[_daoAddress]) {
    fees = ((depositFees * platformFeeMultiplier) / 100);crossChainMint?
}
```

메시지가 동일한 체인으로 전송되지 않는지 확인하거나, `crossChainBuy()`에 대해 항상 크로스체인 승수를 청구하는 것을 고려하십시오.

# [L-13] 일괄 발행된 ERC721 DAO 토큰은 항상 동일한 tokenURI를 가짐

`ERC721DAO`의 `mintToken()` 및 `mintGTToAddress()` 모두 한 사용자에 대해 토큰을 일괄 발행할 수 있습니다. 문제는 모두 동일한 `tokenURI`를 갖게 된다는 것입니다. `tokenURI`는 하나의 NFT를 다른 NFT와 구별하며, 그렇게 하는 것이 중요한 경우가 많습니다. 또한 이 속성은 나중에 사용자가 변경할 수 없다는 점을 언급할 가치가 있습니다.

일괄 발행된 각 토큰에 대해 서로 다른 토큰 URI를 전달하는 것을 고려하십시오.

# [L-14] 사용자가 자신의 tokenURI를 설정하게 하면 DAO에 해로울 수 있음

NFT의 `tokenURI`는 일반적으로 토큰의 고유한 속성을 포함하며 대체 불가능성을 나타냅니다.

사용자가 임의의 `tokenURI`를 설정하거나, 기만적인 정보를 제공하거나, UI에서 올바르게 렌더링되지 않을 수 있는 잘못된 값을 설정하거나, 다른 토큰의 값을 복사할 수 있게 함으로써 문제가 발생할 수 있습니다. 가격 책정 또한 서로를 구별할 고유한 속성이 없기 때문에 영향을 받습니다.

사용자 대신 DAO 관리자가 개별 `tokenURI` 값을 설정하도록 하거나, 이러한 옵션 중 하나를 선택할 수 있도록 하는 것을 고려하십시오.

# [L-15] 토큰 구매 시 DAO 주소가 검증되지 않음

`buyGovernanceTokenERC20DAO()`, `buyGovernanceTokenERC721DAO()` 또는 `crossChainBuy()` 함수 중 어느 것에서도 `_daoAddress` 매개변수가 검증되지 않습니다. 이는 함수 더 깊은 곳에서 예상치 못한 되돌림(revert)이 발생하기 때문에 현재 코드베이스에서는 위험을 초래하지 않습니다.

미래의 코드 변경에서 발생할 수 있는 문제를 방지하기 위해 `_daoAddress`가 팩토리에 의해 배포된 DAO인지 검증하는 것을 고려하십시오.

# [L-16] DAO 생성자가 대상 체인에서 다른 토큰을 설정할 수 없음

DAO 생성 함수 `createERC20DAO()` 및 `createERC721DAO()`는 토큰 예치 금액 또는 가격 책정과 관련된 값을 18자리 소수점으로 조정합니다:

```solidity
    if (_depositChainIds.length != 0) {
        for (uint256 i; i < _depositChainIds.length - 1;) {
            bytes memory _payload = abi.encode(
                _commLayerId,
                _distributionAmount,
@>              amountToSD(_depositTokenAddress[0], _pricePerToken),
@>              amountToSD(_depositTokenAddress[0], _minDepositPerUser),
@>              amountToSD(_depositTokenAddress[0], _maxDepositPerUser),
                // ...
@>              _depositTokenAddress[i + 1],
```

그리고 대상 체인에서 `createCrossChainERC20DAO()` 및 `createCrossChainERC721DAO()`를 통해 해당 예치 토큰에 해당하는 소수점으로 다시 조정합니다:

```solidity
    _createERC20DAO(
        _distributionAmount,
@>      amountToLD(_depositTokenAddress, _pricePerToken),
@>      amountToLD(_depositTokenAddress, _minDepositPerUser),
@>      amountToLD(_depositTokenAddress, _maxDepositPerUser),
        // ...
@>      _depositTokenAddress,
```

문제는 코드가 DAO 생성자가 모든 체인에서 "동일한" 토큰을 사용할 것이라고 가정한다는 점인데, 이는 작업을 제한합니다("동일한"이라는 의미는 Ethereum USDT <> Polygon USDT와 같이 대상 체인의 상대측을 의미함).

가장 중요한 것은 일부 토큰은 모든 체인에 존재하지 않으므로 DAO 생성자는 다른 토큰(다른 가격 및 최소/최대 예치 한도 포함)을 사용해야 한다는 것입니다.

DAO 생성자가 각 체인에서 각각의 가격 및 예치 한도 값과 함께 서로 다른 예치 토큰을 설정할 수 있도록 허용하는 것이 좋습니다.

# [L-17] DAO 생성자가 초기 생성 후 다른 체인에 DAO를 배포할 수 없음

DAO는 `createERC20DAO()` 및 `createERC721DAO()`를 통해 생성될 수 있습니다. 이는 또한 LayerZero를 통해 다른 체인에 DAO 상대측 생성을 트리거합니다. 문제는 초기 생성 후에는 새로운 체인에 DAO를 배포할 수 있는 방법이 없다는 것입니다.

초기 생성 후 DAO 관리자가 다른 체인에 DAO를 배포할 수 있도록 하는 함수를 추가하는 것이 제안됩니다.

# [L-18] - 토글 함수로 변경된 저장 변수는 public 가시성을 가져야 함

저장 변수 `isPaused` 및 `isKycEnabled`는 `private` 가시성을 갖습니다.

```solidity
bool private isPaused;
mapping(address => bool) private isKycEnabled;
```

문제는 오프체인이나 온체인의 다른 계약에서 현재 상태를 확인할 쉬운 방법이 없다는 것입니다. 상태가 토글 함수를 통해 변경되므로 이 역시 문제가 됩니다:

```solidity
function togglePaused() external {
    _onlyOwners();
    isPaused = !isPaused;
}

function toggleKYC(address _daoAddress) external payable onlyAdmins(daoDetails[_daoAddress].gnosisAddress) {
    isKycEnabled[_daoAddress] = !isKycEnabled[_daoAddress];
}
```

이러한 저장 변수를 `public`으로 만들거나 토글 함수 대신 설정자(setter) 함수를 정의하는 것이 좋습니다.

# [L-19] 중요한 변경 사항에 대한 이벤트 누락

일부 함수는 이벤트를 방출하지 않기 때문에 오프체인 도구로 추적하기 어려운 중요한 변경을 수행합니다.

다음에 이벤트를 추가하는 것을 고려하십시오:

- `Claim`: `claimAllPending()`
- `ClaimFactory`: `setEmitter()`, `changeClaimPrice()`, `changeClaimFee()`, `changeClaimImplementation()`, `withdrawFunds()`
- `LayerZeroDeployer`: `changeFactory()`, `changeEndpoint()`, `setDestination()`
- `LayerZeroImpl`: `changeFactory()`, `changeEndpoint()`, `setDestination()`
- `Deployer`: `defineContracts()`
- `ERC20DAO`: `toggleOnlyAllowWhitelist()`, `updateGovernanceActive()`
- `ERC721DAO`: `toggleOnlyAllowWhitelist()`, `updateGovernanceActive()`
- `Factory`: `changeOwner()`, `togglePaused()`, `defineTokenContracts()`, `setupTokenGating()`, `disableTokenGating()`, `updateFees()`, `toggleKYC()`

# [L-20] `totalClaimAmount`를 0이 아닌 값으로 초기화

`Claim` 컨트랙트의 `totalClaimAmount` 값은 내부 회계 정보를 유지하기 위한 것입니다. 그러나 관리자가 초기화 시 0이 아닌 값으로 설정하는 것을 방지하는 확인이 없으므로 0이 아닌 값으로 설정될 수 있습니다.

이는 부적절한 회계로 이어지며 이를 사용하는 함수에서 되돌림이나 원치 않는 효과를 초래할 수 있습니다.

컨트랙트 초기화 시 `_claimSettings.claimAmountDetails.totalClaimAmount == 0`인지 확인하는 것을 고려하십시오.

# [L-21] ERC20 전송에 safeTransfer 사용

일부 토큰은 `IERC20::transfer()` / `IERC20::transferFrom()` 인터페이스와 [호환](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#missing-return-values)되지 않아 트랜잭션이 되돌려질 수 있습니다.

`zairdrop`의 `airDropToken()` 함수는 `transferFrom()`을 사용합니다:

```solidity
IERC20(_airdropTokenAddress).transferFrom(msg.sender, _members[i], _airdropAmountArray[i]);
```

`factory`의 `rescueFunds()` 함수는 `transfer()`를 사용합니다:

```solidity
IERC20(tokenAddr).transfer(_owner, balance);
```

이러한 경우 `safeTransferFrom`을 사용하는 것을 고려하십시오.

# [L-22] 재진입 호출 시 토큰에 tokenURI가 할당되지 않을 수 있음

`ERC721DAO`의 `mintToken()` 함수는 Checks-Effects-Interactions 패턴을 준수하지 않습니다. 재진입 호출이 이루어지고 더 많은 토큰이 발행되면 새로 발행된 모든 토큰에 대해 `_tokenIdTracker += 1`이 먼저 증가하고, 그 후에 마지막 `_tokenIdTracker` 값으로 `_setTokenURI()`가 호출됩니다. 이는 마지막 토큰에 대해 `tokenURI`를 N번 설정하고 이전 토큰에 대해서는 설정하지 않음을 의미합니다:

```solidity
    for (uint256 i; i < _amount;) {
@>      _tokenIdTracker += 1;
@>      _safeMint(_to, _tokenIdTracker);
@>      _setTokenURI(_tokenIdTracker, _tokenURI);
        unchecked {
            ++i;
        }
    }
```

`mintToken()` 및 `mintGTToAddress()`에서 상호 작용 전에 저장소 변경을 수행하는 것을 고려하십시오:

```solidity
    for (uint256 i; i < _amount;) {
        _tokenIdTracker += 1;
+       _setTokenURI(_tokenIdTracker, _tokenURI);
        _safeMint(_to, _tokenIdTracker);
-       _setTokenURI(_tokenIdTracker, _tokenURI);
        unchecked {
            ++i;
        }
    }
```

# [L-23] 악의적인 사용자가 mintGTToAddress를 통한 토큰 발행을 막을 수 있음

`mintGTToAddress()` 함수는 `_safeMint()`를 통해 많은 사용자에게 토큰을 발행합니다. 이 함수는 각 사용자에 대해 `onERC721Received()` 콜백을 만듭니다. 이러한 사용자 중 누구라도 트랜잭션을 되돌리게 하거나, ERC721 수신자를 구현하지 않는 컨트랙트로 토큰을 전송하려고 시도하면 전체 트랜잭션이 되돌려지고 어떤 사용자에게도 토큰이 발행되지 않습니다.

충돌하는 사용자 없이 발행을 재시도할 수 있습니다. 그러나 공격을 완전히 방지하려면 각 사용자가 자신의 토큰을 청구하도록 하는 pull-over-push 패턴을 구현하는 것이 좋습니다.

# [L-24] DAO가 NFT를 받지 못할 수 있음

`ERC20DAO`나 `ERC721DAO` 모두 ERC721 또는 ERC1155 토큰에 대한 수신자를 구현하지 않습니다. 이는 `safeTransferFrom()`을 통해 전송된 NFT를 받는 것을 방지하는데, 해당 함수들은 `onERC721Received()`, `onERC1155Received()` 및 `onERC1155BatchReceived()` 함수가 구현되어 있을 것으로 기대하기 때문입니다.

두 DAO 계약 모두 이러한 수신자를 구현하거나 [ERC721Holder](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.9.6/contracts/token/ERC721/utils/ERC721Holder.sol#L14) 및 [`ERC1155Holder`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.9.6/contracts/token/ERC1155/utils/ERC1155Holder.sol#L16)를 상속받는 것이 좋습니다.

# [L-25] 모든 ERC1155 tokenId를 토큰 게이트 청구에 사용할 수 있음

`ERC1155` NFT의 경우, 다른 ID는 일반적으로 이를 나타내는 고유한 속성을 갖습니다. `Claim` 컨트랙트의 `claim()` 함수는 토큰 게이트 검증을 위해 어떤 `_tokenId` 값이라도 전달할 수 있도록 허용합니다:

```solidity
    function claim(..., uint256 _tokenId) external payable nonReentrant {
        if (IERC20Extended(claimSettingsMemory.daoToken).supportsInterface(0xd9b67a26)) {
            if (
@>              IERC20Extended(claimSettingsMemory.daoToken).balanceOf(msg.sender, _tokenId)
                    < claimSettingsMemory.tokenGatingValue
            ) revert InsufficientBalance();
        }
    }
```

`Claim` 컨트랙트의 관리자가 토큰 게이팅에 유효한 특정 `tokenId` 값을 설정할 수 있도록 허용하고, `claim()` 함수에서 이를 검증하는 것을 고려하십시오.

# [L-26] 모든 Safe가 다른 DAO를 대신하여 changedSigners 이벤트를 방출할 수 있음

`ERC20DAO` 및 `ERC721DAO`의 `emitSignerChanged()` 함수는 Safe 지갑이 다른 `_dao` 값을 전달하기만 하면 다른 DAO를 대신하여 이벤트를 방출할 수 있도록 허용합니다. 이는 공격자가 오프체인 인덱싱 값을 엉망으로 만드는 데 사용될 수 있습니다.

```solidity
    function emitSignerChanged(address _dao, address _signer, bool _isAdded) external onlyGnosis(factoryAddress, address(this)) {
        Emitter(emitterContractAddress).changedSigners(
@>          _dao,
            _signer,
            _isAdded);
    }
```

`changedSigners()` 호출에서 `_dao`를 `address(this)`로 대체하는 것을 고려하십시오.

# [L-27] 하드코딩된 Safe 폴백 핸들러가 일부 체인에 존재하지 않을 수 있음

Safe 초기화 프로그램은 폴백 핸들러를 하드코딩된 `0xf48f2B2d2a534e402487b3ee7C18c33Aec0Fe5e4` 주소로 설정하며, 이는 `CompatibilityFallbackHandler`에 해당합니다.

이 주소는 [Safe 문서](https://docs.safe.global/advanced/smart-account-supported-networks/v1.3.0)에서 볼 수 있듯이 모든 체인에서 동일하다고 보장되지 않습니다. 한 가지 예로 zkSync가 있는데, 해당 주소는 `0x2f870a80647BbC554F3a0EBD093f11B4d2a7492A`입니다.

각 체인에 맞는 올바른 값을 지정할 수 있는 설정자(setter) 함수를 추가하는 것을 고려하십시오.

# [L-28] 제공할 최종 가치를 계산하는 뷰(view) 함수 누락

토큰을 구매하거나 DAO를 배포하면 KYC, 크로스체인 트랜잭션 및 수수료 비율에 따라 많은 가변 수수료가 발생합니다.

사용자가 지불해야 할 비용을 쉽게 요청할 수 있는 뷰 함수가 없습니다.

이 데이터에 쉽게 액세스할 수 있는 방법이 없으면 UI에 매우 어려울 수 있습니다. 이러한 함수를 추가하는 것을 고려하십시오.

# [L-29] 소유자를 변경하거나 역할을 취소할 방법이 없음

일부 계약에는 소유자/관리자 기능을 가진 유일한 특권 사용자가 있습니다. 관리상의 이유와 위험 예방을 위해 소유권을 다른 주소로 이전하는 기능을 추가하는 것이 좋습니다.

이 문제는 `LayerZeroImpl`, `LayerZeroDeployer`, `Deployer`, `zairdrop`에 존재합니다.

`emitter` 컨트랙트의 경우 사용자 지정 `ADMIN` 역할을 할당하고 있습니다. 대신 OpenZeppelin의 `DEFAULT_ADMIN_ROLE` 역할을 할당하여 다른 역할을 부여하고 취소할 수 있도록 하는 것이 좋습니다.

비슷한 맥락에서 `erc20dao` 및 `erc721`은 `RefundModule` 역할을 부여할 수 있지만 제거할 방법이 없습니다. 이를 수행하는 함수를 추가하는 것을 고려하십시오.

# [L-30] 업그레이드 가능한 계약의 일관성 없는 사용

일부 업그레이드 가능한 계약은 [권장되는 대로](https://docs.openzeppelin.com/contracts/4.x/upgradeable) OpenZeppelin 계약의 업그레이드 가능한 버전을 사용하지 않습니다. 이러한 계약에는 제대로 작동하기 위한 초기화 프로그램이 포함되거나 향후 업그레이드를 위해 저장소에 공간(gaps)을 예약해야 할 수 있습니다.

다음에 대해 업그레이드 가능한 OpenZeppelin 계약 버전을 사용하는 것을 고려하십시오:

- `erc20dao`: `AccessControlUpgradeable`, `ReentrancyGuardUpgradeable`
- `erc721dao`: `AccessControlUpgradeable`, `ERC2981Upgradeable`
- `emitter`: `AccessControlUpgradeable`
- `ClaimFactory`: `AccessControlUpgradeable`
- `ClaimEmitter`: `AccessControlUpgradeable`
- `Claim`: `AccessControlUpgradeable`, `ReentrancyGuardUpgradeable`

현재 커밋이 `erc20dao` 및 `erc721dao`에서 `_contextSuffixLength()`에 대한 "누락된" 재정의 때문에 빌드되지 않는다는 점에 유의하십시오. 이 재정의는 제안된 계약 변경으로 필요하지 않습니다.

또한 `erc20dao` 및 `erc721dao`의 `_msgSender()` 및 `_msgData()` 재정의는 계약 충돌이 해결되었으므로 더 이상 필요하지 않습니다(추가 기능이 추가되지 않는 한). 이것들은 안전하게 제거할 수 있습니다.

또한 다음 계약에 대해 [권장되는 대로](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#initializing_the_implementation_contract) 업그레이드 가능한 구현에서 초기화 프로그램을 비활성화하는 것을 고려하십시오: `Claim`, `ClaimEmitter`, `ClaimFactory`, `LayerZeroDeployer`, `LayerZeroImpl`, `factory`, `Deployer`, 및 `emitter`.

마지막으로 각 계약의 해당 `initialize()` 함수에서 OpenZeppelin 초기화 프로그램이 호출되는지 확인하십시오:

- `Claim`: `__AccessControl_init()`, `__ReentrancyGuard_init()`
- `ClaimEmitter`: `__AccessControl_init()`
- `ClaimFactory`: `__AccessControl_init()`
- `emitter`: `__AccessControl_init()`
- `erc20dao`: `__AccessControl_init()`, `__ReentrancyGuard_init()`
- `erc721dao`: `__AccessControl_init()`, `__ERC2981_init()`

초기화되지 않은 구현 계약은 공격자가 초기화할 수 있으므로, 생성자에서 \_disableInitializers() 함수를 호출하여 공격자가 구현 계약을 사용하는 것을 방지하는 것이 좋습니다.



