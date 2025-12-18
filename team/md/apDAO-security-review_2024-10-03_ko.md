# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**0xHoneyJar/apdao-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# apDAO 소개

Apiology DAO는 시트 토큰(seat tokens)을 위한 특수 경매장과 함께 맞춤형 ERC20DAO 계약을 구현하여 사용자가 탈중앙화된 구조 내에서 경매하고 입찰할 수 있도록 합니다. 통합에는 경매 기능과 일치하는 거버넌스 및 시트 토큰이 포함되어 있으며, 맞춤형 주조 및 소각 조건을 통해 규제되는 안전하고 양도 불가능한 소유권을 보장합니다.

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

_검토 커밋 해시_ - [225065a80c717e1bd6129ae7fd6652e922270b2d](https://github.com/0xHoneyJar/apdao-contracts/tree/225065a80c717e1bd6129ae7fd6652e922270b2d)

_수정 검토 커밋 해시_ - [a1d5172b8fe2d80be6db01bd65b2394259a8b00d](https://github.com/0xHoneyJar/apdao-contracts/tree/a1d5172b8fe2d80be6db01bd65b2394259a8b00d)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Deployer`
- `emitter`
- `erc20dao`
- `factory`
- `helper`
- `proxy`
- `ApiologyDAOAuctionHouse`
- `ApiologyDAOToken`

# 발견 사항

# [C-01] 경매 생성 및 정산을 프론트러닝하여 상장 가격을 낮추거나 NFT 소유자에게 피해를 입힘

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

경매 생성 시 다음 라인이 있습니다.

```solidity
uint256 currentRFV = treasury.realFloorValue();
```

이것은 경매된 NFT가 상장되는 가격입니다(버퍼 역할을 하는 백분율만큼 증가). 이 값은 다음과 같이 계산됩니다.

```solidity
value_ = (backing_ + backingLoanedOut_) / float_;
```

`float_` 변수는 트레저리가 소유하지 않은 유통되는 NFT의 공급량입니다. 계산을 보면 `float_` 값이 높을수록 결과가 낮아진다는 것을 분명히 알 수 있습니다. 따라서 모든 사용자(또는 많은 사용자)는 경매 생성을 프론트러닝하고 `ApiologyDAOToken::claim()`을 호출하여 자신에게 NFT를 주조함으로써 더 높은 공급량 또는 더 높은 `float_` 값을 초래할 수 있습니다.

```solidity
_mint(msg.sender, remainingClaimable);
```

이로 인해 경매의 시작 가격이 예상보다 낮아집니다.

NFT에 대한 입찰자가 없고 경매가 정산될 때 유사한 상황이 발생할 수 있습니다(값을 계산하는 코드는 `Treasury::redeemItem()`에 있음).

```solidity
                uint256 redeemedValue = treasury.redeemItem(_auction.apdaoId);

                // Convert WETH to ETH
                IWETH(weth).withdraw(redeemedValue);

                // Transfer the exact redeemed ETH value to the original owner
                _safeTransferETHWithFallback(originalOwner, redeemedValue);
```

이로 인해 NFT를 경매에 올린 NFT 소유자는 예상보다 훨씬 적은 자금을 받게 되며 실제 예약 가격보다 낮을 수 있습니다.

## 권장 사항

가능한 수정 사항은 `_createAuction()` 호출과 청구 정산 사이의 시간 델타에서 청구를 허용하지 않는 것이지만, 경매 대기열이 비어 있으면 난수를 먼저 요청하지 않고 경매가 직접 생성되므로 해당 벡터는 여전히 가능합니다.

# [C-02] 입찰 승자가 경매장을 마비시킬 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

경매가 끝나면 `_settleAuction()`이 호출됩니다. 여기에는 경매에 입찰자가 없는 경우와 경매에 입찰자가 있는 경우의 2가지 주요 흐름이 있습니다. 후자에 초점을 맞출 것입니다. 해당 흐름에 대한 라인 중 하나는 다음과 같습니다.

```solidity
apiologyToken.safeTransferFrom(address(this), _auction.bidder, _auction.apdaoId);
```

`safeTransferFrom()`을 사용하여 NFT를 계약에서 입찰자에게 전송합니다. 따라서 입찰자가 악의적으로 또는 우발적으로 `onERC721Received()` 함수를 적절하게 구현하지 않은 계약인 경우 되돌리기로 이어져 경매장이 마비됩니다. NFT는 입찰자가 지불한 자금과 함께 갇히게 됩니다.

## 권장 사항

`safeTransferFrom()` 대신 `transferFrom()`을 사용하는 것을 고려하십시오.

# [H-01] 사용자가 경매를 위해 선택된 NFT를 조작할 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

경매를 생성하기 위해 `auctionQueue`에서 경매될 NFT의 인덱스를 결정하는 데 사용되는 난수가 생성됩니다. 난수가 경매 대기열의 길이와 같거나 경매 대기열의 길이보다 1 작으면 사용자는 자신에게 유리하게 선택된 NFT를 조작할 수 있습니다.

먼저 첫 번째 시나리오를 상상해 보겠습니다.

- 난수 5로 경매가 생성되고 경매 대기열에 5개의 NFT가 있습니다.
- 아래 라인으로 인해 인덱스가 0인 NFT를 경매합니다.

```solidity
uint256 randomIndex = uint256(randomNumber) % auctionQueue.length;
```

- 그러나 사용자가 경매 생성을 프론트러닝하고 자신의 NFT를 경매 대기열에 넣어 NFT의 인덱스가 대신 `5 % 6 = 5`가 되게 합니다. 이는 자신의 NFT입니다.

이제 다른 시나리오를 상상해 보겠습니다.

- 난수 5로 경매가 생성되고 경매 대기열에 6개의 NFT가 있습니다.
- 위 라인으로 인해 경매될 NFT의 인덱스는 `5 % 6 = 5`가 됩니다. 즉, 마지막 NFT가 경매됩니다.
- 그러나 대기열에 이미 자신의 NFT를 넣은 사용자가 경매 생성을 프론트러닝하고 다음을 수행합니다.
  - 대기열에서 자신의 NFT를 꺼냅니다.
  - 다시 넣습니다.
- 이제 NFT가 대기열의 `x` 슬롯에서 마지막 지점으로 이동했으므로 경매될 NFT의 인덱스는 여전히 동일하며, 이는 위의 시퀀스를 수행한 사용자가 경매된 NFT의 소유자가 되는 결과를 낳습니다.

## 권장 사항

난수의 요청과 실제 수신은 별도의 트랜잭션에 있으므로 `_createAuction()`이 호출될 때마다 경매 대기열에서 추가 및 제거를 허용하지 않는 것을 고려하십시오.

# [H-02] 공격자는 난수를 요청한 후 대기열을 나갈 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`ApiologyAuctionHouse` 계약이 경매를 생성하는 두 가지 상황이 있습니다.

1. `auctionQueue`에 시트가 있을 때 Pyth에 난수를 요청합니다. Pyth가 난수를 생성한 후 경매를 생성하기 위해 `entropyCallback` 함수를 다시 호출합니다. 구체적인 프로세스는 다음과 같습니다.
   1. (tx1) 사용자가 `ApiologyAuctionHouse.settleCurrentAndCreateNewAuction`을 호출합니다.
   2. (tx1) `ApiologyAuctionHouse`가 Pyth에 난수를 요청하고 Pyth에 일부 수수료를 지불합니다.
   3. (off-chain) Pyth가 난수를 생성합니다.
   4. (tx2) Pyth가 `ApiologyAuctionHouse.entropyCallback`을 호출하여 경매를 생성합니다.
2. `auctionQueue`에 시트가 없으면 경매가 직접 생성됩니다.

문제는 첫 번째 시나리오에 존재합니다. 공격자는 NFT를 대기열에 먼저 넣은 다음 Pyth가 난수를 생성하기 전에 대기열에서 NFT를 제거할 수 있습니다. 이로 인해 `settleCurrentAndCreateNewAuction`이 호출될 때마다 Pyth에 난수를 요청하여 계약의 자금을 낭비하고 빈 경매를 생성하게 됩니다.

공격자는 NFT를 사용하여 이 버그를 반복적으로 트리거하고 계약의 자금을 소비할 수 있습니다.

## 권장 사항

대기열을 나가는 사용자가 수수료를 지불하거나 난수를 받기 전에 사용자가 대기열을 나가지 못하도록 하는 것이 좋습니다.

# [H-03] 반복적 기본 토큰 분배에서 `msg.value`의 잘못된 사용

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`factory::createERC20DAO` 함수에서 `_depositChainIds` 배열에 하나 이상의 요소가 있는 경우 함수는 체인 ID를 반복하여 `commLayer::sendMsg` 함수를 사용하여 메시지를 보냅니다. 기본 토큰 값(`msg.value`)의 일부는 `(msg.value - fees) / (_depositChainIds.length - (i + 1))` 공식을 사용하여 각 반복에서 계산되고 전송됩니다. 그러나 계산은 각 반복에서 수수료가 공제됨에 따라 줄어드는 `msg.value`를 고려하지 않습니다. 이로 인해 메시지 전송에 대한 수수료 분배가 잘못될 수 있으며 후속 메시지 전송을 위한 자금이 부족할 수 있습니다.

```solidity
File: factory.sol
243:         if (_depositChainIds.length > 1) {
244:             for (uint256 i; i < _depositChainIds.length - 1;) {
245:                 require(_depositTokenAddress[i + 1] != NATIVE_TOKEN_ADDRESS, "Native not supported");
246:                 bytes memory _payload = abi.encode(
247:                     _commLayerId,
248:                     _distributionAmount,
249:                     amountToSD(_depositTokenAddress[0], _pricePerToken),
250:                     amountToSD(_depositTokenAddress[0], _minDepositPerUser),
251:                     amountToSD(_depositTokenAddress[0], _maxDepositPerUser),
252:                     _ownerFeePerDepositPercent,
253:                     _depositTime,
254:                     _quorumPercent,
255:                     _thresholdPercent,
256:                     _safeThreshold,
257:                     _depositChainIds,
258:                     _daoAddress,
259:                     _depositTokenAddress[i + 1],
260:                     _admins,
261:                     _onlyAllowWhitelist,
262:                     _merkleRoot,
263:                     0,
264:                     msg.sender
265:                 );
266:>>>              ICommLayer(commLayer).sendMsg{value: (msg.value - fees) / (_depositChainIds.length - (i + 1))}(
267:>>>                  commLayer, _payload, abi.encode(_depositChainIds[i + 1], msg.sender)
268:                 );
269:                 unchecked {
270:                     ++i;
271:                 }
272:             }
273:
274:             IEmitter(emitterAddress).createCCDao(_daoAddress, _depositChainIds);
275:         }
```

`sendMsg`에 대한 각 호출은 이전 반복에서 이미 분배된 `msg.value`의 부분을 고려해야 합니다. 그렇게 하지 않으면 각 후속 메시지에 대해 불충분한 자금이 전송되어 실패하거나 불완전한 작업이 발생할 수 있습니다.

## 권장 사항

이전 반복에서 이미 할당된 자금을 고려하도록 계산을 업데이트하십시오. 이렇게 하면 각 후속 메시지에 대해 남은 자금이 올바르게 분배됩니다.

```diff
+           uint256 valueToDistribute = msg.value - fees;
            for (uint256 i; i < _depositChainIds.length - 1;) {
                require(_depositTokenAddress[i + 1] != NATIVE_TOKEN_ADDRESS, "Native not supported");
                bytes memory _payload = abi.encode(
                    _commLayerId,
                    _distributionAmount,
                    amountToSD(_depositTokenAddress[0], _pricePerToken),
                    amountToSD(_depositTokenAddress[0], _minDepositPerUser),
                    amountToSD(_depositTokenAddress[0], _maxDepositPerUser),
                    _ownerFeePerDepositPercent,
                    _depositTime,
                    _quorumPercent,
                    _thresholdPercent,
                    _safeThreshold,
                    _depositChainIds,
                    _daoAddress,
                    _depositTokenAddress[i + 1],
                    _admins,
                    _onlyAllowWhitelist,
                    _merkleRoot,
                    0,
                    msg.sender
                );
-               ICommLayer(commLayer).sendMsg{value: (msg.value - fees) / (_depositChainIds.length - (i + 1))}(
+               uint256 calculatedDeposit = valueToDistribute / (_depositChainIds.length - (i + 1));
+               ICommLayer(commLayer).sendMsg{value: calculatedDeposit}(
                    commLayer, _payload, abi.encode(_depositChainIds[i + 1], msg.sender)
                );
+               valueToDistribute = valueToDistribute - calculatedDeposit;
                unchecked {
                    ++i;
                }
            }
```

# [H-04] 불충분한 거버넌스 토큰으로 인한 `ApiologyDAOToken` 전송 되돌리기

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`ApiologyDAOToken` 계약은 `ApiologyDAOToken::changeGovTokensPerNFT` 함수를 통해 각 NFT와 관련된 거버넌스 토큰의 수를 동적으로 조정할 수 있도록 합니다.

```solidity
File: ApiologyDAOToken.sol
333:     function changeGovTokensPerNFT(uint256 _newAmount) external onlyOwner {
334:         require(_newAmount > 0, "Amount must be greater than 0");
335:>>>      GOVERNANCE_TOKENS_PER_NFT = _newAmount;
336:     }
```

이 함수는 일반 주소에서 유동성 보증 트레저리 또는 경매장으로 토큰을 전송하는 동안 호출되는 `ApiologyDAOToken::_burnGovernanceTokens` 함수에 직접적인 영향을 미칩니다. `GOVERNANCE_TOKENS_PER_NFT`가 증가하면 사용자는 소각에 필요한 양을 충당할 충분한 거버넌스 토큰을 보유하지 못할 수 있으며(ApiologyDAOToken.sol#L318), 이로 인해 전송 트랜잭션이 되돌려질 수 있습니다.

```solidity
File: ApiologyDAOToken.sol
185:     function transferFrom(address from, address to, uint256 tokenId) public payable override(ERC721A) whenNotPaused {
...
191:         // Burn governance tokens if transferring from a regular address to LBT or Auction House
192:         if (
193:             (to == liquidBackingTreasury || to == auctionHouse)
194:                 && (from != liquidBackingTreasury && from != auctionHouse)
195:         ) {
196:>>>          _burnGovernanceTokens(from, 1);
197:         }
198:
199:         super.transferFrom(from, to, tokenId);
200:
201:         // Mint governance tokens if transferring from LBT or Auction House to a regular address
202:         if (
203:             (from == liquidBackingTreasury || from == auctionHouse)
204:                 && (to != liquidBackingTreasury && to != auctionHouse)
205:         ) {
206:             _mintGovernanceTokens(to, 1);
207:         }
...
...
316:     function _burnGovernanceTokens(address from, uint256 amount) internal {
317:         for (uint256 i = 0; i < stationXTokens.length; i++) {
318:>>>          IERC20DAO(stationXTokens[i]).burn(from, amount * GOVERNANCE_TOKENS_PER_NFT * 1 ether);
319:         }
320:     }
```

이로 인해 사용자는 `ApiologyAuctionHouse::addToAuctionQueue` 함수를 사용하여 `auctionQueue`에 토큰을 추가할 수 없게 되는데, 사용자에서 `auctionHouse` 계약으로의 토큰 전송이 되돌려지기 때문입니다. 이는 토큰을 판매하려는 사용자에게 손실을 초래할 수 있습니다. 경매가 유일한 판매 방법이기 때문입니다. NFT를 다른 위치로 전송하는 것은 허용되지 않으므로 경매 메커니즘이 유일한 판매 경로입니다.

반면에 `GOVERNANCE_TOKENS_PER_NFT`의 값이 감소하면 사용자에서 `auctionHouse`로의 토큰 전송으로 인해 일부 거버넌스 토큰만 소각되고 나머지 거버넌스 토큰은 사용자에게 남게 됩니다. 이는 소각 프로세스에 불일치를 초래하여 사용자가 전송 중에 완전히 소각되어야 했던 거버넌스 토큰을 보유할 수 있게 합니다.

## 권장 사항

`GOVERNANCE_TOKENS_PER_NFT` 값을 수정할 필요성을 고려하십시오. 조정이 필요한 경우 `GOVERNANCE_TOKENS_PER_NFT`가 증가하거나 감소할 때마다 올바른 수의 토큰을 소각하도록 `_burnGovernanceTokens` 함수가 업데이트되었는지 확인하십시오.

마찬가지로 `GOVERNANCE_TOKENS_PER_NFT`가 변경되면 사용자의 거버넌스 토큰을 업데이트하는 메커니즘이 있어야 합니다.

# [H-05] 경매 스토리지 덮어쓰기로 인한 경매 토큰 손실 가능성

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`ApiologyDAOAuctionHouse` 계약은 대기 중인 토큰에 대한 경매를 생성하기 위해 Pyth Entropy 계약에 의해 생성된 난수를 활용합니다. 난수를 사용할 준비가 되면 `ApiologyDAOAuctionHouse::entropyCallback` 함수가 호출되어 응답을 처리하고 경매 생성 프로세스를 시작합니다.

```solidity
File: ApiologyDAOAuctionHouse.sol
358:     function entropyCallback(uint64 sequenceNumber, address _providerAddress, bytes32 randomNumber) internal override {
359:         emit RandomNumberReceived(sequenceNumber, randomNumber);
360:>>       _createAuctionWithRandomNumber(randomNumber);
361:     }
```

문제는 진행 중인 경매가 있는 동안 `ApiologyDAOAuctionHouse::entropyCallback` 함수가 호출될 때 발생합니다. 이 동작으로 인해 `auctionStorage`를 덮어쓰게 되어 이전에 등록된 경매 데이터가 손실될 수 있습니다.

```solidity
File: ApiologyDAOAuctionHouse.sol
378:     function _createAuctionWithRandomNumber(bytes32 randomNumber) internal {
...
414:
415:>>       auctionStorage = AuctionV2({
416:             apdaoId: uint96(apdaoId),
417:             clientId: 0,
418:             amount: 0,
419:             startTime: startTime,
420:             endTime: endTime,
421:             bidder: payable(0),
422:             settled: false
423:         });
...
426:     }
```

다음 시나리오를 고려해 보십시오.

1. 소유자가 일시 중지 함수를 호출하고 현재 경매를 닫기 위해 정산합니다.
2. 소유자가 일시 중지 해제 함수를 호출하여 `ApiologyDAOAuctionHouse::requestRandomNumber`를 트리거하고, 이는 `Entropy::requestWithCallback`을 호출합니다.
3. 소유자가 다시 한 번 일시 중지 및 일시 중지 해제를 호출하여 `ApiologyDAOAuctionHouse::requestRandomNumber`를 다시 트리거하고, 이는 `Entropy::requestWithCallback`을 호출합니다.
4. 마지막으로 entropy가 `ApiologyDAOAuctionHouse::entropyCallback`을 두 번 호출하여 `auctionStorage`를 덮어쓰게 됩니다.

[문서](https://docs.pyth.network/entropy/generate-random-numbers/evm#3-implement-callback-for-entropy)에 따르면 `ApiologyDAOAuctionHouse::entropyCallback` 함수에 대한 호출은 별도의 트랜잭션에서 발생한다는 점을 강조하는 것이 중요합니다. _최종 난수가 사용 준비가 되면 Entropy 계약에 의해 entropyCallback 함수가 호출됩니다. 이는 요청한 공급자에 의해 제출된 별도의 트랜잭션에서 발생합니다._ 반면에 `entropy`가 콜백을 여러 번 호출하지 않을 것이라고 가정해서는 안 됩니다.

이로 인해 `auctionStorage`를 덮어쓰게 되어 현재 경매 중인 토큰이 관련 입찰 금액을 포함하여 손실되는 상황이 발생합니다.

## 권장 사항

새 경매를 생성하기 전에 `auctionStorage`의 현재 상태를 확인하여 진행 중인 경매가 없는지 확인하십시오.

```diff
   function entropyCallback(uint64 sequenceNumber, address _providerAddress, bytes32 randomNumber) internal override {
       emit RandomNumberReceived(sequenceNumber, randomNumber);
-       _createAuctionWithRandomNumber(randomNumber);
+       if (auctionStorage.settled) { _createAuctionWithRandomNumber(randomNumber); };
   }
```

# [M-01] 트레저리 지원이 없을 때 생성된 경매

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

경매가 생성될 때마다 다음과 같이 예약 가격을 설정합니다.

```solidity
uint256 reservePriceWithBuffer = currentRFV + (currentRFV * reservePriceBuffer) / 100;

_setReservePrice(uint192(reservePriceWithBuffer));
```

`currentRFV`는 `Treasury::realFloorValue()`의 결과입니다. 지원이 없으면(그리고 대여된 지원이 없으면) 값은 0이 됩니다(또는 float 값이 지원보다 높은 경우). 그런 일이 발생할 가능성은 희박하지만 영구적으로 갇힌 NFT로 이어질 수 있으므로 영향은 매우 심각합니다.

예약 가격이 0이면 누군가가 `msg.value`를 0으로 사용하여 경매에 입찰할 수 있음을 의미합니다. 이로 인해 다음 2개의 변수가 변경됩니다.

```solidity
auctionStorage.amount = uint128(msg.value);
auctionStorage.bidder = payable(msg.sender);
```

입찰자는 `msg.sender`가 되고 금액은 0이 됩니다. 이제 시간이 지나고 경매가 여전히 0인 금액으로 정산되면(지원이 0이면 사용자가 실제 돈으로 입찰하는 데 관심이 없기 때문에 이런 일이 발생할 가능성이 높음), NFT가 갇히게 됩니다. 입찰자가 있었을 때를 위한 `_settleAuction`의 **else** 블록에 들어가게 되기 때문입니다. 거기서 전체 블록은 다음 확인 내부에 있습니다.

```solidity
if (_auction.amount > 0)
```

금액이 0이므로 이 확인에 들어가지 않으므로 해당 블록에 있는 상태 변경 및 전송이 발생하지 않습니다. 이제 NFT는 계약에 유지되므로 완전히 갇히게 됩니다. `nftOwners` 매핑은 여전히 사용자를 NFT 소유자로 가지고 있지만 경매 생성 시 경매 대기열에서 해당 NFT를 팝 아웃하므로, 사용자는 이미 제거되었기 때문에 NFT를 되찾기 위해 경매 대기열에서 NFT를 제거할 수 없습니다.

## 권장 사항

금액이 0일 때 다음 **else** 블록을 구현하십시오.

```solidity
if (_auction.amount > 0) {
       ...
} else {
     auctionQueue.push(_auction.apdaoId);
}
```

이렇게 하면 금액이 0이고 입찰자가 있는 경우 사용자가 되돌려받을 수 있도록 NFT를 대기열에 다시 넣습니다.

# [M-02] `mintToken()`은 주조 대상 사용자가 관리자가 아니라고 가정함

## 심각도

**영향:** 낮음

**가능성:** 중간

## 설명

`ERC20DAO::mintToken()`이 호출되면 다음 코드가 있습니다.

```solidity
        Emitter(emitterContractAddress).newUser(
            address(this),
            to,
            Factory(factoryAddress).getDAOdetails(address(this)).depositTokenAddress,
            0,
            block.timestamp,
            amount,
            false
        );
```

마지막 변수는 사용자가 관리자인지 여부입니다. 보시다시피 사용자가 관리자가 아니라고 항상 가정합니다. 그러나 사용자가 관리자일 수 있는 여러 시나리오가 있으므로 이는 잘못된 가정입니다. 먼저 NFT를 전송할 때 다음 라인이 있습니다.

```solidity
        if ((from == liquidBackingTreasury || from == auctionHouse) && (to != liquidBackingTreasury && to != auctionHouse)) {
            _mintGovernanceTokens(to, 1);
        }
```

`from`이 경매장이고 `to`가 아닌 경우 거버넌스 토큰을 주조하여 처음에 논의된 함수를 호출합니다. 이 시나리오는 누군가가 경매 대기열에서 NFT를 제거할 때 발생하며 이는 관리자가 수행할 것으로 예상되는 작업입니다. 이 함수는 누군가가 `ApiologyDAOToken::claim()`을 호출할 때도 호출되며, 이는 다시 관리자가 호출할 수 있는 것입니다.

## 권장 사항

`false`를 `ERC20DAO::mintGTToAddress()`에서 사용되는 코드로 변경하십시오.

# [M-03] `setPrices()`가 잘못된 값으로 정산 내역에 액세스함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`setPrices()` 함수를 살펴보겠습니다.

```solidity
function setPrices(SettlementNoClientId[] memory settlements) external onlyOwner {
        for (uint256 i = 0; i < settlements.length; ++i) {
            SettlementState storage settlementState = settlementHistory[settlements[i].apdaoId];
            settlementState.blockTimestamp = settlements[i].blockTimestamp;
            settlementState.amount = ethPriceToUint64(settlements[i].amount);
            settlementState.winner = settlements[i].winner;
            settlementState.apdaoId = uint96(settlements[i].apdaoId);
        }
    }
```

보시다시피 NFT ID로 정산 내역에 액세스합니다. 그러나 이것은 코드의 다른 모든 곳과 동기화되지 않습니다. 예를 들어 경매가 정산될 때 정산 내역이 어떻게 설정되는지 살펴보겠습니다.

```solidity
uint256 settlementId = currentSettlementId++;
SettlementState storage settlementState = settlementHistory[settlementId];
```

현재 정산 ID를 기반으로 정산 내역에 선형적으로 액세스합니다. 그러나 위의 경우 선형성을 따르지 않는 NFT ID로 액세스하며 이 2개의 값은 완전히 관련이 없습니다. 일부 함수는 `setPrices()`의 작동 방식으로 인해 잘못될 수 있는 정산이 순서대로 되어 있다고 가정합니다.

## 권장 사항

두 번째 입력인 정산 ID 배열을 포함하고 이를 사용하여 정산 내역에 액세스하십시오. 또한 온전성 목적으로 2개 배열에 대한 길이 확인을 포함하십시오.

# [M-04] 엔트로피 콜백으로 인해 일시 중지 상태 동안 경매 생성

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`ApiologyAuctionHouse` 계약에는 새로운 경매가 생성되는 것을 방지하기 위해 작업을 일시 중지하는 메커니즘이 있습니다. 경매의 생성은 `ApiologyAuctionHouse::unpause` 및 `ApiologyAuctionHouse::settleCurrentAndCreateNewAuction` 함수를 통해서만 발생할 수 있으며, 이 두 함수는 계약이 일시 중지되지 않은 경우에만 실행될 수 있습니다. 그러나 `entropyCallback` 함수는 계약이 일시 중지되었는지 확인하지 않고 새 경매를 생성할 수 있습니다. 이는 엔트로피 콜백이 난수 요청이 이루어진 후 비동기적으로 트리거될 수 있기 때문에 발생하며, 잠재적으로 계약이 일시 중지된 시간에 발생할 수 있습니다. 이 동작은 계약이 비활성 상태여야 할 때에도 경매 생성을 허용하여 계약의 일시 중지 상태 제어를 위반합니다.

```solidity
File: ApiologyDAOAuctionHouse.sol
358:     function entropyCallback(uint64 sequenceNumber, address _providerAddress, bytes32 randomNumber) internal override {
359:         emit RandomNumberReceived(sequenceNumber, randomNumber);
360:         _createAuctionWithRandomNumber(randomNumber);
361:     }
```

이 함수는 난수 요청에 응답하여 엔트로피 공급자에 의해 호출되며, 일시 중지 상태를 확인하지 않고 잠재적으로 새 경매를 생성하는 `_createAuctionWithRandomNumber`를 직접 호출합니다.

다음 시나리오를 고려해 보십시오.

1. 여러 이유로 계약이 일시 중지될 예정이며, 그 전에 난수 요청이 이루어집니다. 요청 후 계약이 일시 중지됩니다.
2. 다른 트랜잭션에서 `entropy`가 `entropyCallback`을 호출하여 계약이 일시 중지되었음에도 불구하고 새 경매를 생성합니다.

## 권장 사항

`ApiologyAuctionHouse::entropyCallback` 함수 내에서 경매 생성을 진행하기 전에 계약이 일시 중지되었는지 확인하는 검사를 구현하십시오.

# [M-05] 엔트로피 소스 콜백 실패

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`ApiologyAuctionHouse` 계약은 경매 생성에 필요한 난수를 제공하기 위해 `Pyth Entropy` 계약을 통해 구현된 외부 엔트로피 소스에 의존합니다. 난수 응답 처리를 담당하는 주요 함수는 `ApiologyAuctionHouse::entropyCallback`이며, 이는 엔트로피 소스에 의해 호출되어야 합니다. 이 콜백 함수가 호출되지 않으면 계약은 무작위성을 필요로 하는 새 경매 생성을 진행할 수 없습니다.

```solidity
File: ApiologyDAOAuctionHouse.sol
358:     function entropyCallback(uint64 sequenceNumber, address _providerAddress, bytes32 randomNumber) internal override {
359:         emit RandomNumberReceived(sequenceNumber, randomNumber);
360:>>>      _createAuctionWithRandomNumber(randomNumber);
361:     }
```

콜백이 없으면 경매 생성 프로세스가 중단되어 새 경매가 시작되지 않습니다. 자동으로 진행할 수 없으므로 수동 개입이 필요합니다. 여기서 소유자는 경매 생성 메커니즘을 재설정하기 위해 계약을 일시 중지하고 일시 중지 해제해야 하며, 이는 중앙 집중식 실패 지점과 소유자가 사용할 수 없거나 악의적으로 행동할 경우 잠재적인 오용을 초래합니다.

## 권장 사항

특정 기간 내에 콜백이 수신되지 않으면 난수를 다시 요청하는 폴백 메커니즘을 구현하십시오. 이는 타임아웃 또는 재시도 카운터를 사용하여 수행할 수 있습니다.

# [M-06] 함수의 처리되지 않은 예외

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`ApiologyAuctionHouse::_createAuctionWithRandomNumber` 함수에는 `Error(string memory)`만 잡는 try/catch 블록이 있습니다.

```solidity
File: ApiologyDAOAuctionHouse.sol
402:         } else {
403:             try apiologyToken.mint() returns (uint256 mintedapDAOId) {
404:                 apdaoId = mintedapDAOId;
405:                 nftOwners[apdaoId] = address(0);
406:             } catch Error(string memory) {
407:                 _pause();
408:                 return;
409:             }
410:         }
```

이 접근 방식은 `apiologyToken::mint` 함수가 일시 중지되어 현재 try/catch 구현에 의해 [캡처되지 않는](https://docs.soliditylang.org/en/latest/control-structures.html#error-handling-assert-require-revert-and-exceptions) [`revert EnforcedPause();`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/8b591baef460523e5ca1c53712c464bcc1a1c467/contracts/utils/Pausable.sol#L83)를 트리거할 수 있기 때문에 불충분합니다.

> catch Error(string memory reason) { ... }: 이 catch 절은 오류가 revert("reasonString") 또는 require(false, "reasonString")(또는 이러한 예외를 유발하는 내부 오류)에 의해 발생한 경우 실행됩니다.

결과적으로 토큰 주조가 일시 중지되면 경매 생성 프로세스가 실패하고 일시 중지된 상태를 확인하지 않고 새로운 난수 요청이 시작됩니다. 이러한 간과로 인해 사용자는 난수 요청이 호출될 때마다 비용이 발생하므로 불필요한 엔트로피 수수료를 지출하게 됩니다.

## 권장 사항

일시 중지 상태와 관련된 예외를 처리하도록 try/catch 블록을 강화하십시오.

```diff
        } else {
            try apiologyToken.mint() returns (uint256 mintedapDAOId) {
                apdaoId = mintedapDAOId;
                nftOwners[apdaoId] = address(0);
-           } catch Error(string memory) {
+           } catch {
                _pause();
                return;
            }
        }
```

# [M-07] `_depositChainIds`의 길이가 1보다 클 때 `createERC20DAO()`가 항상 되돌림

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`createERC20DAO()`를 호출할 때마다 다음 **if** 검사가 있습니다.

```solidity
if (_depositChainIds.length > 1)
```

여기에 도달하면 항상 되돌립니다. 이는 다음 라인 때문입니다.

```solidity
IEmitter(emitterAddress).createCCDao(_daoAddress, _depositChainIds);
```

이것은 `Emitter` 계약에서 `createCCDao()`를 호출하지만 해당 함수는 그곳에 구현되어 있지 않습니다. 이는 `CTRL + Shift + F`를 사용하여 해당 함수 이름을 검색하여 쉽게 확인할 수 있으며 모든 파일에서 해당 이름의 유일한 함수는 `IEmitter` 인터페이스에 있습니다.

## 권장 사항

`createCCDao()` 기능을 구현하십시오.

# [M-08] 모듈로 연산자로 인한 무작위 토큰 선택 편향

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`_createAuctionWithRandomNumber` 함수는 모듈로 연산을 사용하여 `auctionQueue`에서 임의의 인덱스를 선택합니다.

```solidity
File: ApiologyDAOAuctionHouse.sol
378:     function _createAuctionWithRandomNumber(bytes32 randomNumber) internal {
...
390:         if (auctionQueue.length > 0) {
391:>>>          uint256 randomIndex = uint256(randomNumber) % auctionQueue.length;
392:             apdaoId = auctionQueue[randomIndex];
393:
394:             // Remove the selected token from the queue
395:             auctionQueue[randomIndex] = auctionQueue[auctionQueue.length - 1];
396:             auctionQueue.pop();
397:
398:             // Emit the removal event
399:             uint256[] memory removedTokens = new uint256[](1);
400:             removedTokens[0] = apdaoId;
401:             emit TokensRemovedFromAuctionQueue(removedTokens, nftOwners[apdaoId]);
402:         } else {
...
```

[pyth 문서](https://docs.pyth.network/entropy/best-practices)에 따르면 이로 인해 대기열에 있는 토큰 전체에 선택 확률이 불균등하게 분포될 수 있습니다.

> 모듈로 연산자를 사용하면 난수 분포가 2의 거듭제곱이 아닌 경우 왜곡될 수 있습니다. 작고 중간 범위의 경우 무시할 수 있지만 큰 범위의 경우 눈에 띄게 나타날 수 있습니다. 예를 들어 1에서 52 사이의 난수를 생성하려는 경우 값이 5일 확률은 값이 50일 확률보다 약 10^-77 더 높으며 이는 미미합니다.

이 경우 `auctionQueue.length`가 `randomNumber`의 범위를 균등하게 나누지 않으면 일부 인덱스가 다른 인덱스보다 선택될 확률이 더 높을 수 있습니다. 이로 인해 불균등한 선택 프로세스가 발생하여 잠재적으로 경매 대기열에서 특정 NFT를 다른 NFT보다 선호할 수 있습니다. 이것은 큰 대기열에서 특히 우려됩니다.

## 권장 사항

모듈로 연산으로 인한 편향을 완화하려면 임의의 인덱스를 선택하는 다른 방법을 사용하는 것을 고려하십시오.

# [L-01] 스토리지 변수 검사 간의 불일치

경매장의 `initialize()` 함수에서 다음 변수를 설정합니다.

```solidity
duration = _duration;
reservePrice = _reservePrice;
timeBuffer = _timeBuffer;
minBidIncrementPercentage = _minBidIncrementPercentage;
feePercentage = 0;
```

이 모든 변수에 대해 설정자 함수가 있으며 예약 가격을 제외한 모든 변수에 대해 온전성 검사가 있습니다. 예:

```solidity
    function setAuctionDuration(uint256 _duration) external onlyOwner {
        require(_duration > 0, "Duration must be greater than zero");
        duration = _duration;
        emit AuctionDurationUpdated(_duration);
    }
```

보시다시피 `initialize()` 함수에는 이러한 검사가 없습니다. 불일치가 있습니다. 잘못된 값을 설정하지 않을 자신이 있다면 설정자 함수의 검사를 제거하여 계약을 최적화할 수 있습니다. 그러나 계약을 최대한 안전하게 유지하려면 초기화 함수에서도 이러한 온전성 검사를 구현하는 것을 고려하십시오.

# [L-02] 방출된 이벤트 간의 차이

`ERC20DAO::mintToken()`과 `ERC20DAO::mintGTToAddress()`는 목적 측면에서 매우 동일합니다. 첫 번째는 지정된 주소로 거버넌스 토큰을 주조하고 후자는 여러 주소로 거버넌스 토큰을 주조합니다. 따라서 이 함수들은 동일한 이벤트를 방출할 것으로 예상되며, 그 중 하나는 일괄 주조 또는 기타 추가 정보를 표시하기 위해 추가 이벤트를 방출할 수 있습니다. 그러나 후자는 이 이벤트 방출을 완전히 놓칩니다.

```solidity
        Emitter(emitterContractAddress).deposited(...);
```

대신 이것도 추가하는 것을 고려하십시오.

# [L-03] ERC20 DAO 생성 시 완료 여부 미확인

ERC20 DAO 생성 시 다음 확인이 있습니다.

```solidity
        if (((_distributionAmount * _pricePerToken) / 1e18) < _maxDepositPerUser) {
            revert RaiseAmountInvalid(((_distributionAmount * _pricePerToken) / 1e18), _maxDepositPerUser);
        }
```

그런 다음 분배 금액과 토큰당 가격을 업데이트하려는 `updateTotalRaiseAmount()`에서 먼저 다음 확인을 수행합니다.

```solidity
        if (_distributionAmount != _newDistributionAmount) {
            if (_distributionAmount > _newDistributionAmount) {
                revert AmountInvalid("_newDistributionAmount", _newDistributionAmount);
            }
         ...
        }
```

분배 금액이 더 낮은 값으로 업데이트되면 되돌릴 것임을 알 수 있습니다. 이는 이전에 `_maxDepositPerUser` 위에 있었다면 분배 금액이 올라가면 `_maxDepositPerUser` 변수 아래로 내려갈 방법이 없으므로 처음에 표시된 초기 확인이 여전히 통과할 것임을 암시적으로 확인합니다. 그런 다음 다음 코드가 있습니다.

```solidity
        if (daoDetails[_daoAddress].pricePerToken != _newPricePerToken) {
            daoDetails[_daoAddress].pricePerToken = _newPricePerToken;
            IEmitter(emitterAddress).updatePricePerToken(_daoAddress, _newPricePerToken);
        }
```

여기에는 토큰당 가격에 대한 확인이 없으므로 예를 들어 토큰당 가격이 0이면 초기 확인이 더 이상 유효하지 않게 됩니다.

# [L-04] `updateDepositTime()`으로 입금 마감 시간을 과거로 설정 가능

`updateDepositTime()`에는 다음 입력이 있습니다.

> /// @param \_depositTime New deposit close time

입력이 입금 마감 시간임을 알 수 있습니다. 해당 함수를 호출할 때 해당 입력에 대해 다음 확인만 있습니다.

```solidity
if (_depositTime == 0) revert AmountInvalid("_days", _depositTime);
```

따라서 입금 마감 시간은 `block.timestamp`에 대해 검증되지 않으므로 실제로 과거로 설정될 수 있습니다. 대신 위 확인을 다음과 같이 변경하십시오.

```solidity
if (_depositTime <= block.timestamp) revert AmountInvalid("_days", _depositTime);
```

# [L-05] 최소 및 최대 입금 간의 불일치

ERC20 DAO를 생성할 때마다 최소 및 최대 사용자 입금에 대해 다음 라인이 있습니다.

```solidity
        if (_maxDepositPerUser <= _minDepositPerUser) {
            revert DepositAmountInvalid(_maxDepositPerUser, _minDepositPerUser);
        }
```

최대 및 최소 입금이 모두 10000이라고 가정해 보겠습니다. 같으므로 되돌립니다. 그러나 업데이트를 위한 다음 함수도 있습니다.

```solidity
    function updateMinMaxDeposit(uint256 _minDepositPerUser, uint256 _maxDepositPerUser, address _daoAddress)
        external
        payable
        onlyGnosisOrDao(address(this), _daoAddress)
    {
        ...

        validateDepositAmounts(_minDepositPerUser, _maxDepositPerUser);
        ...
    }
```

검증을 담당하는 함수를 살펴보면 다음과 같습니다.

```solidity
if (_min == 0 || _min > _max) revert DepositAmountInvalid(_min, _max);
```

위에서 사용한 것과 동일한 값을 사용해 보겠습니다. 10000은 10000보다 크지 않으므로 되돌리지 않습니다. 보시다시피 이 2개의 함수는 다르게 검증합니다. 대신 의도한 방식을 결정하고 두 함수 모두에서 이를 고수하십시오.

# [L-06] 정산 상태가 비어 있다고 잘못 가정하는 여러 인스턴스

`ApiologyDAOAuctionHouse::getSettlements()`와 같은 몇 가지 함수에는 다음과 유사한 코드가 있습니다.

```solidity
            settlementState = settlementHistory[id];

            if (skipEmptyValues && settlementState.winner == address(0)) continue;

            settlements[actualCount] = Settlement({
                blockTimestamp: settlementState.blockTimestamp,
                amount: uint64PriceToUint256(settlementState.amount),
                winner: settlementState.winner,
                apdaoId: settlementState.apdaoId,
                clientId: settlementState.clientId,
                settlementId: id
            });
```

이 라인에 주목해 보겠습니다.

```solidity
if (skipEmptyValues && settlementState.winner == address(0)) continue;
```

이 라인의 목표는 비어 있는 정산을 건너뛰는 것입니다. 그러나 `address(0)` 승자가 정확히 빈 정산은 아닙니다. 경매에 입찰자가 없을 때 승자는 `address(0)`일 수 있습니다. 그러나 그것은 여전히 `settlementState` 스토리지에 기록됩니다.

```solidity
        SettlementState storage settlementState = settlementHistory[settlementId];
        settlementState.blockTimestamp = uint32(block.timestamp);
        settlementState.amount = ethPriceToUint64(_auction.amount);
        settlementState.winner = _auction.bidder;
        settlementState.apdaoId = _auction.apdaoId;
```

위에서 변경된 값은 타임스탬프와 NFT ID이며, 이는 `ApiologyDAOAuctionHouse::getSettlements()`와 같은 함수를 호출하는 사용자에게 유용할 수 있는 값이지만 비어 있는 것으로 가정하므로 사용할 수 없습니다.

# [L-07] `factory.emitSignerChanged` 함수가 모든 `dao` 주소를 허용함

```solidity
/// @file: contracts/stationX/factory.sol
    function emitSignerChanged(address _dao, address _signer, bool _isAdded)
        external
        onlyGnosisOrDao(address(this), _dao)
    {
        IEmitter(emitterAddress).changedSigners(_dao, _signer, _isAdded);
    }
```

호출자는 자신이 배포한 `_dao`의 주소를 전달하여 `onlyGnosisOrDao(address(this), _dao)` 수정자를 우회하여 `changedSigner` 이벤트를 방출할 수 있습니다.

# [L-08] `factory.createERC20DAO` 함수가 빈 Gnosis Safe 계약을 배포할 수 있음

`factory.createERC20DAO` 함수는 Safe 계약을 배포하기 위해 `IDeployer(deployer).deploySAFE`를 호출합니다. `deploySAFE` 함수 코드는 아래와 같습니다.

```solidity
/// @file: contracts/stationX/Deployer.sol
    function deploySAFE(address[] calldata _admins, uint256 _safeThreshold, address _daoAddress)
        external
        returns (address SAFE)
    {
        bytes memory _initializer = abi.encodeWithSignature(
            "setup(address[],uint256,address,bytes,address,address,uint256,address)",
            _admins,
            _safeThreshold,
            0x0000000000000000000000000000000000000000,
            "",
            safeFallback,
            0x0000000000000000000000000000000000000000,
            0,
            0x0000000000000000000000000000000000000000
        );

        uint256 nonce = getNonce(_daoAddress);
A>      try ISafe(safe).createProxyWithNonce(singleton, _initializer, nonce) returns (address _deployedSafe) {
            SAFE = _deployedSafe;
        } catch {}
    }
```

코드 A에 표시된 대로 `deploySAFE` 함수는 Safe 계약 배포 실패를 허용합니다. 실패하면 빈 주소가 반환됩니다.

# [L-09] 팩토리는 생성 수수료가 성공적으로 지불되었는지 확인하지 않음

`factory.createERC20DAO` 함수는 호출자가 생성 수수료를 지불하도록 요구합니다. 코드는 다음과 같습니다.

```solidity
/// @file: contracts/stationX/factory.sol
    function checkCreateFeesSent(uint256[] calldata _depositChainIds) internal returns (uint256) {
        require(_depositChainIds.length > 0, "Deposit Chains not provided");
        uint256 fees = createFees * _depositChainIds.length;
        require(msg.value >= fees, "Insufficient fees");
A>      payable(_owner).call{value: fees}("");
        return fees;
    }
```

그러나 코드 A에서는 `call`이 성공했는지 확인하지 않습니다. `call`이 실패하면 `_owner`는 생성 수수료를 받지 못합니다.

# [L-10] ETH 전송 실패 시 계약이 WETH 사용을 강제하여 자금 손실

`ApiologyAuctionHouse` 계약은 일반적으로 ETH를 지불 토큰으로 사용합니다. 그러나 ETH 전송이 실패하면 WETH 토큰 전송을 강제로 사용합니다. 코드는 다음과 같습니다.

```solidity
/// @file: contracts/ApiologyDAOAuctionHouse.sol
    function _safeTransferETHWithFallback(address to, uint256 amount) internal {
        if (!_safeTransferETH(to, amount)) {
            IWETH(weth).deposit{value: amount}();
            IERC20(weth).transfer(to, amount);
        }
    }
```

즉, 사용자가 ERC20을 지원하지 않는 계약을 가지고 있다면 자금이 손실됩니다.

# [L-11] 중요 함수에서 이벤트 방출 부재

`ApiologyDAOAuctionHouse::setPrices`, `ApiologyDAOAuctionHouse::setEntropy`, `erc20dao::updateGovernanceActive`, `Deployer::transferOwnerShip`, `Deployer::defineContracts`, `Factory::defineTokenContracts`를 포함한 여러 중요 함수는 실행 시 이벤트를 방출하지 않습니다. 이벤트 방출이 없으면 스마트 계약 생태계 내에서 중요한 상태 변경의 투명성과 추적성이 저하될 수 있습니다.

식별된 함수에 대한 이벤트 방출을 구현하십시오. 각 함수는 관련된 변경 사항과 데이터를 기록하는 이벤트를 방출해야 합니다.

# [L-12] 중복 역할 할당

`Emitter` 계약 내의 `createDaoErc20` 함수에는 `msg.sender`에게 `EMITTER` 역할을 부여하는 라인이 있습니다.

```solidity
File: emitter.sol
161:     function createDaoErc20(
162:         address _deployerAddress,
163:         address _proxy,
164:         string memory _name,
165:         string memory _symbol,
166:         uint256 _distributionAmount,
167:         uint256 _pricePerToken,
168:         uint256 _minDeposit,
169:         uint256 _maxDeposit,
170:         uint256 _ownerFee,
171:         uint256 _totalDays,
172:         uint256 _quorum,
173:         uint256 _threshold,
174:         address _depositTokenAddress,
175:         address _emitter,
176:         address _gnosisAddress,
177:         address lzImpl,
178:         bool _isGovernanceActive,
179:         bool isTransferable,
180:         bool assetsStoredOnGnosis
181:     ) external payable onlyRole(FACTORY) {
182:         _grantRole(EMITTER, _proxy);
183:>>>      _grantRole(EMITTER, msg.sender);
```

이 함수는 `onlyRole(FACTORY)` 수정자에 의해 제한되므로 `msg.sender`는 항상 `FACTORY` 계약입니다. `FACTORY` 계약에는 이미 `EMITTER` 역할이 있으므로 이 작업은 중복됩니다. `createDaoErc20`을 호출할 때마다 이 라인을 계속 실행하면 불필요한 가스 소비가 발생합니다.

`createDaoErc20` 함수 내에서 `msg.sender`에게 `EMITTER` 역할을 부여하는 라인을 제거하십시오. 이렇게 하면 불필요한 가스 사용량이 줄어들고 역할 관리 로직이 단순화됩니다.

# [L-13] `payable` 수정자의 불필요한 사용

`emitter.sol` 계약의 `defineContracts` 및 `createDaoErc20` 함수는 `payable` 수정자로 표시됩니다. 그러나 `msg.value`를 처리하지 않으므로 이더 트랜잭션을 받기 위한 의도가 아닙니다. 이러한 함수에 `payable` 수정자가 있으면 불필요하며 계약의 기능에 대한 혼란이나 오해를 초래할 수 있습니다.

```solidity
File: emitter.sol
151:     function defineContracts(
152:         address ERC20ImplementationAddress,
153:         address ERC721ImplementationAddress,
154:         address emitterImplementationAddress
155:>>   ) external payable onlyRole(FACTORY) {
...
...
161:     function createDaoErc20(
...
181:>>   ) external payable onlyRole(FACTORY) {

```

`payable` 수정자를 제거하도록 함수 정의를 업데이트하여 이더를 받으려는 함수만 받을 수 있도록 하여 잠재적인 혼란을 줄이십시오.

# [L-14] 대기열에 단일 NFT가 있는 불필요한 난수 요청

`ApiologyAuctionsHouse::_createAuction` 함수는 `auctionQueue`에 NFT가 하나만 있는 경우에도 난수를 요청합니다. 하나의 항목만 사용할 수 있는 경우 무작위 선택이 필요하지 않으므로 비효율적입니다. 이로 인해 난수 요청과 관련된 수수료로 인해 사용자에게 불필요한 추가 비용이 발생합니다.

```solidity
File: ApiologyDAOAuctionHouse.sol
366:     function _createAuction() internal {
367:         if (auctionQueue.length > 0) {
368:             requestRandomNumber();
369:         } else {
370:             _createAuctionWithRandomNumber(0);
371:         }
372:     }
```

`auctionQueue`에 NFT가 하나만 있는지 확인하는 조건부 검사를 도입하십시오. 그렇다면 난수 요청을 우회하고 경매 생성으로 직접 진행하십시오.

```diff
    function _createAuction() internal {
-       if (auctionQueue.length > 0) {
+       if (auctionQueue.length > 1) {
            requestRandomNumber();
        } else {
            _createAuctionWithRandomNumber(0);
        }
    }
```

# [L-15] `factory::createERC20DAO`의 중복 gnosis safe 배포

`factory::createERC20DAO` 함수에는 새로운 Gnosis Safe를 배포해야 하는지 여부를 결정하는 조건부 검사가 있습니다. 구체적으로 `_depositChainIds` 배열에 두 개 이상의 요소가 포함된 경우 함수는 사용자가 제공한 기존 Gnosis Safe 주소를 재정의하고 새 주소를 배포합니다. 이 로직으로 인해 사용자가 사용하려는 기존 Gnosis Safe가 있더라도 불필요하게 새 Gnosis Safe를 배포하게 될 수 있습니다.

```solidity
File: factory.sol
185:         if (_gnosisAddress == address(0) || _depositChainIds.length > 1) {
186:>>>          _safe = IDeployer(deployer).deploySAFE(_admins, _safeThreshold, _daoAddress);
187:         } else {
188:             if (!isGnosisSafe(_gnosisAddress)) {
189:                 revert InvalidGnosisSafe("Invalid Gnosis Safe", _gnosisAddress);
190:             }
191:
192:             _safe = _gnosisAddress;
193:         }
```

`_depositChainIds`의 길이에 관계없이 사용자가 제공한 Gnosis Safe 주소를 존중하도록 로직을 수정하는 것이 좋습니다.

# [L-16] `maxDepositPerUser` 및 `pricePerToken`의 불충분한 검증

`updateMinMaxDeposit` 함수에는 `maxDepositPerUser`가 `(_distributionAmount * _pricePerToken) / 1e18`에서 파생된 계산 가능한 입금 한도를 초과하지 않도록 하는 유효성 검사가 부족합니다.

```solidity
File: factory.sol
355:     function updateMinMaxDeposit(uint256 _minDepositPerUser, uint256 _maxDepositPerUser, address _daoAddress)
356:         external
357:         payable
358:         onlyGnosisOrDao(address(this), _daoAddress)
359:     {
360:         validateDaoAddress(_daoAddress);
361:
362:         validateDepositAmounts(_minDepositPerUser, _maxDepositPerUser);
363:
364:         daoDetails[_daoAddress].minDepositPerUser = _minDepositPerUser;
365:         daoDetails[_daoAddress].maxDepositPerUser = _maxDepositPerUser;
366:
367:         IEmitter(emitterAddress).updateMinMaxDeposit(_daoAddress, _minDepositPerUser, _maxDepositPerUser);
368:     }
```

이 검증은 `erc20dao`가 처음 생성될 때 수행되지만 `maxDeposit`이 업데이트될 때는 검증이 실행되지 않습니다.

```solidity
File: factory.sol
331:         if (((_distributionAmount * _pricePerToken) / 1e18) < _maxDepositPerUser) {
332:             revert RaiseAmountInvalid(((_distributionAmount * _pricePerToken) / 1e18), _maxDepositPerUser);
333:         }
```

`factory::updateMinMaxDeposit` 함수에 유효성 검사를 추가하여 `maxDepositPerUser`가 계산된 분배 값 `((_distributionAmount * _pricePerToken) / 1e18)`을 초과하지 않도록 하십시오.

# [L-17] 요청 수수료에 대한 payable 수정자 부재

`ApiologyDAOAuctionHouse::requestRandomNumber` 함수는 `entropy::requestWithCallback` 함수를 호출할 때 ETH를 `requestFee`로 보냅니다.

```solidity
File: ApiologyDAOAuctionHouse.sol
344:     function requestRandomNumber() internal {
345:         bytes32 userRandomNumber = keccak256(abi.encodePacked(block.timestamp, msg.sender));
346:         address provider = entropy.getDefaultProvider();
347:         uint128 requestFee = entropy.getFee(provider);
348:>>>      uint64 sequenceNumber = entropy.requestWithCallback{value: requestFee}(provider, userRandomNumber);
349:         emit RandomNumberRequested(sequenceNumber);
350:     }
```

이 함수는 `ApiologyDAOAuctionHouse::_createAuction`에서 호출되며, 이는 `ApiologyDAOAuctionHouse::unpause()` 및 `ApiologyDAOAuctionHouse::settleCurrentAndCreateNewAuction()` 메서드에 의해 트리거될 수 있습니다. 이러한 메서드 중 어느 것도 `payable` 수정자가 없으므로 계약에 기존 잔액이 없는 한 수수료를 충당할 충분한 ETH가 없을 수 있습니다.

## 권장 사항

`unpause()` 및 `settleCurrentAndCreateNewAuction()` 함수에 `payable` 수정자를 추가하고 `Entropy`의 난수가 필요한 경우 사용자가 올바른 `requestFee` 금액을 보내도록 하십시오. 이렇게 하면 난수 요청에 필요한 자금이 제공됩니다.

# [L-18] `createERC20DAO()`가 입금 시간 입력을 잘못된 방식으로 사용함

`createERC20DAO()`에는 다음 입력이 있습니다.

> /// @param \_depositTime Deposit period duration

보시다시피 입력은 입금 기간입니다. 그러나 사용법을 보면:

```solidity
daoDetails[_daoAddress] = DAODetails(
            _pricePerToken,
            _distributionAmount,
            _minDepositPerUser,
            _maxDepositPerUser,
            _ownerFeePerDepositPercent,
            _depositTime, <@
            _depositTokenAddress,
            _gnosisAddress,
            _merkleRoot,
            true,
            false,
            _assetsStoredOnGnosis
        );
```

구조체를 기준으로 이 변수는 다음과 같습니다.

```solidity
uint256 depositCloseTime;
```

기간을 마감 시간과 혼동합니다. 따라서 100분의 기간은 마감 시간을 초 단위로 100분으로 설정하게 되는데, 이는 아주 먼 과거입니다.

주석이 정확하다면 해당 변수를 `block.timestamp`에 추가하십시오. 주석이 틀렸다면 변경하십시오.

# [L-19] 악의적인 입찰자가 사용자의 입찰을 억제할 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

누군가가 입찰하면 이전 입찰자가 있는 경우 아래 함수를 사용하여 입찰한 금액을 환불합니다.

```solidity
    function _safeTransferETHWithFallback(address to, uint256 amount) internal {
        if (!_safeTransferETH(to, amount)) {
            IWETH(weth).deposit{value: amount}();
            IERC20(weth).transfer(to, amount);
        }
    }
```

먼저 `_safeTransferETH()`를 호출하고 실패하면 해당 양의 ETH를 WETH로 변환하여 사용자에게 전송합니다. 이는 입찰자가 `receive()` 함수를 되돌려 전송을 차단하는 것을 허용하지 않기 위해 수행됩니다. `_safeTransferETH()`를 살펴보겠습니다.

```solidity
    function _safeTransferETH(address to, uint256 value) internal returns (bool) {
        bool success;
        assembly {
            success := call(70000, to, value, 0, 0, 0, 0)
        }
        return success;
    }
```

어셈블리를 사용하여 가스를 70000으로 제한하여 자금을 사용자에게 전송합니다. 이렇게 하면 수신자가 모든 가스를 사용하여 새 입찰자의 트랜잭션을 되돌리고 사용자가 입찰하지 못하게 하는 것을 방지할 수 있습니다. 그러나 사용자는 여전히 70000 가스를 사용하고 함수를 되돌릴 수 있습니다. 되돌리기가 포착되어 WETH가 그에게 전송되므로 함수가 차단되지는 않지만 최근 평균 가스 가격을 살펴보면 27 gwei를 볼 수 있습니다. 그 비용을 계산하려면 $27 * 70000$을 곱하여 1890000 gwei가 되며 현재 가격으로 약 4.50$입니다. 따라서 모든 입찰자는 입찰 비용을 4.50$만큼 늘릴 수 있으며 이는 적은 금액이 아닙니다(이는 총 가스 비용이 아니라 악의적인 입찰자로 인한 증가분일 뿐입니다). 가스 가격이 그보다 훨씬 높을 수 있다는 사실(작년에 하루 평균 가스 가격이 가장 높았던 것이 100이었으므로 증가분이 4배 더 큼)과 ETH 가격이 크게 오를 수 있다는 사실을 결합하면 이는 우려 사항입니다. 사용자는 수수료에 대해 더 많은 비용을 청구받게 되며 해당 증가로 인해 입찰 가치가 없어져 입찰을 꺼리게 될 수 있습니다.

## 권장 사항

가스를 더 제한하는 것도 옵션이지만 실패가 더 자주 발생할 수 있습니다. push over pull 패턴을 유지하려면 ETH를 전혀 보내지 않고 금액을 WETH로 직접 변환하여 대신 전송하는 것이 좋습니다. 이렇게 하면 push over pull 패턴의 훌륭한 UX를 유지하면서 입찰자에 의한 DoS 벡터뿐만 아니라 그러한 시나리오가 불가능해집니다.

# [L-20] 공격자는 환불 가스를 프론트런하고 늘려 입찰을 DoS할 수 있음

UserA가 새 입찰을 제출하면 이전 입찰의 자금은 이전 입찰자(UserB라고 가정)에게 반환됩니다. 문제는 UserB가 환불 가스를 늘리는 것을 프론트러닝하여 UserA의 입찰을 DoS할 수 있다는 것입니다. 이는 환불 메커니즘에 의존하며 코드는 다음과 같습니다.

```solidity
/// @file: contracts/ApiologyDAOAuctionHouse.sol
    function _safeTransferETHWithFallback(address to, uint256 amount) internal {
        if (!_safeTransferETH(to, amount)) {
            IWETH(weth).deposit{value: amount}();
            IERC20(weth).transfer(to, amount);
        }
    }
--snip--
    function _safeTransferETH(address to, uint256 value) internal returns (bool) {
        bool success;
        assembly {
            success := call(70000, to, value, 0, 0, 0, 0)
        }
        return success;
    }
```

`_safeTransferETHWithFallback`은 ETH를 전송하기 위해 `call(70000, to, value, 0, 0, 0, 0)`을 수행합니다. `to`는 UserB가 완전히 제어할 수 있는 계약입니다. 공격 단계는 다음과 같습니다.

1. UserA가 `createBid`를 호출하기 전에 일반적으로 실행을 시뮬레이션하고 가스 소비량을 얻습니다. 100,000이라고 가정합니다.
2. UserA는 중복성을 위해 가스를 20% 늘리므로 `createBid`를 실행할 때 120,000의 gaslimit를 제공합니다.
3. UserB는 `to.receive`의 가스 소비량을 늘리는 것을 프론트러닝합니다.
4. UserA의 tx는 실행 시 실제로 170,000이 필요하므로 실행이 실패합니다.

환불 메커니즘을 최적화하는 것, 즉 환불을 계약에 저장하고 사용자가 청구할 수 있는 방법을 제공하는 것이 좋습니다.

