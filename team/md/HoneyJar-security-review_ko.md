# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **0xHoneyJar/honeyjar-contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# HoneyJar 정보 (About HoneyJar)

이 게임은 플레이어가 ERC20 토큰이나 ETH를 사용하여 꿀단지(honey jars)를 발행할 수 있는 스마트 계약으로, 마일스톤에 도달하면 특별한 발효 단지(fermented jars)가 무작위로 수여됩니다. 플레이어는 Slumber Party에 참여할 수 있으며, 특정 체크포인트에 도달하면 난수에 따라 어떤 꿀단지가 특별한 발효 단지가 될지 결정되어 흥미와 보상을 더해줍니다.

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

_검토 커밋 해시_ - [fabf0c542528fdb3b2026e961aa7494ad9221248](https://github.com/0xHoneyJar/honeyjar-contracts/tree/fabf0c542528fdb3b2026e961aa7494ad9221248)

_수정 검토 커밋 해시_ - [0b3b94e40f41cab26094925f4cab795e97b440e4](https://github.com/0xHoneyJar/honeyjar-contracts/tree/0b3b94e40f41cab26094925f4cab795e97b440e4)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `Gatekeeper`
- `HoneyJarONFT`
- `GameRegistryConsumer`
- `Den`
- `Constants`
- `MultisigOwnable`
- `HoneyJar`
- `CrossChainTHJ`
- `Beekeeper`
- `HoneyJarPortal`
- `GameRegistry`
- `TokenMinter`
- `BearPouch`

# 발견 사항 (Findings)

# [C-01] `HoneyJarONFT`의 통신 채널이 차단될 수 있음 (The communication channel for `HoneyJarONFT` can be blocked)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`HoneyJarONFT`는 LayerZero의 `NonblockingLzApp`을 상속하여 크로스 체인 NFT 전송을 관리합니다.

크로스 체인 메시지를 수신하는 흐름이 어떻게 작동하는지 분석해 보겠습니다:

1. 진입점은 `NonblockingLzApp`에 의해 상속된 `LzApp` 계약의 `lzReceive` 함수이며, 이는 내부 함수 `_blockingLzReceive`를 실행합니다.

2. `_blockingLzReceive`(`NonblockingLzApp`에 의해 재정의됨)는 저수준 호출(low-level call)을 통해 `nonblockingLzReceive`를 실행합니다. 호출이 실패하면 메시지가 나중에 처리되도록 저장됩니다.

```solidity
File: NonblockingLzApp.sol

    function _blockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) internal virtual override {
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

3. `nonblockingLzReceive`는 메시지 발신자가 자신의 계약인지 확인하고 내부 함수 `_nonblockingLzReceive`를 실행하며, 이는 `HoneyJarONFT`에 의해 재정의됩니다.

4. `_nonblockingLzReceive`는 `_creditTill`을 호출하여 모든 토큰을 반복하고 수신자에게 크레딧을 제공합니다. 각 반복에서 토큰 처리를 계속할 가스가 충분한지 확인합니다. 그렇지 않으면 처리할 다음 토큰의 인덱스를 반환하고 `_blockingLzReceive`로 돌아가 메시지가 나중에 처리되도록 저장됩니다.

이 마지막 단계의 구현에 문제가 있습니다. `_creditTill`이 실행된 후 이벤트에서 방출될 tokenIds 배열을 구축하기 위해 토큰을 반복하는 추가 루프가 있기 때문입니다. 토큰 수에 따라 이 루프는 남은 모든 가스를 소비하여 트랜잭션이 되돌려질(revert) 수 있습니다.

이 오류는 `_blockingLzReceive` 함수에 의해 포착되지 않으므로 사실상 메시지 채널을 차단하고 계약이 더 이상 메시지를 수신하지 못하게 합니다. 또한 메시지가 소스 체인에서 전송될 때 NFT가 소각되므로 사용자는 NFT를 잃게 된다는 점에 유의하는 것이 중요합니다.

```solidity
File: HoneyJarONFT.sol

    function _nonblockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64, /*_nonce*/
        bytes memory _payload
    ) internal override {
        // decode and load the toAddress
        (address toAddress, TokenPayload[] memory tokens) = _decodeSendNFT(_payload);

        uint256 nextIndex = _creditTill(_srcChainId, toAddress, 0, tokens);
        if (nextIndex < tokens.length) {
            // not enough gas to complete transfers, store to be cleared in another tx
            bytes32 hashedPayload = keccak256(_payload);
            storedCredits[hashedPayload] = StoredCredit(_srcChainId, toAddress, nextIndex, true);
            emit CreditStored(hashedPayload, _payload);
        }

        uint256[] memory tokenIds = new uint256[](tokens.length);
@>      for (uint256 i = 0; i < tokens.length; i++) {
            tokenIds[i] = tokens[i].tokenId;
        }

        emit ReceiveFromChain(_srcChainId, _srcAddress, toAddress, tokenIds);
    }

(...)

    function _creditTill(uint16 _srcChainId, address _toAddress, uint256 _startIndex, TokenPayload[] memory tokens)
        internal
        returns (uint256)
    {
        uint256 i = _startIndex;
        while (i < tokens.length) {
            // if not enough gas to process, store this index for the next loop
            if (gasleft() < minGasToTransferAndStore) break;

            _creditTo(_srcChainId, _toAddress, tokens[i].tokenId, tokens[i].isFermented);
            i++;
        }

        // indicates the next index to send of tokenIds,
        // if i == tokenIds.length, we are finished
        return i;
    }
```

## 권장 사항

```diff
    function _nonblockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64, /*_nonce*/
        bytes memory _payload
    ) internal override {
        // decode and load the toAddress
        (address toAddress, TokenPayload[] memory tokens) = _decodeSendNFT(_payload);

-       uint256 nextIndex = _creditTill(_srcChainId, toAddress, 0, tokens);
+       (uint256 nextIndex, uint256[] memory tokenIds) = _creditTill(_srcChainId, toAddress, 0, tokens);
        if (nextIndex < tokens.length) {
            // not enough gas to complete transfers, store to be cleared in another tx
            bytes32 hashedPayload = keccak256(_payload);
            storedCredits[hashedPayload] = StoredCredit(_srcChainId, toAddress, nextIndex, true);
            emit CreditStored(hashedPayload, _payload);
        }

-       uint256[] memory tokenIds = new uint256[](tokens.length);
-       for (uint256 i = 0; i < tokens.length; i++) {
-           tokenIds[i] = tokens[i].tokenId;
-       }
-
        emit ReceiveFromChain(_srcChainId, _srcAddress, toAddress, tokenIds);
    }

(...)

    function _creditTill(uint16 _srcChainId, address _toAddress, uint256 _startIndex, TokenPayload[] memory tokens)
        internal
-       returns (uint256)
+       returns (uint256, uint256[] memory)
    {
        uint256 i = _startIndex;
+       uint256[] memory tokenIds = new uint256[](tokens.length);
        while (i < tokens.length) {
            // if not enough gas to process, store this index for next loop
            if (gasleft() < minGasToTransferAndStore) break;

            _creditTo(_srcChainId, _toAddress, tokens[i].tokenId, tokens[i].isFermented);
+           tokenIds[i] = tokens[i].tokenId;
            i++;
        }

        // indicates the next index to send of tokenIds,
        // if i == tokenIds.length, we are finished
-       return i;
+       return (i, tokenIds);
    }
```

이 변경 사항으로 `minGasToTransferAndStore`는 `tokenIds` 배열의 각 개별 요소를 설정하는 데 필요한 추가 가스를 고려해야 합니다.

# [H-01] 반올림으로 인해 자금 분배가 실패할 수 있음 (Funds distribution can fail due to the rounding up)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

NFT가 판매되면 `Beekeeper`는 `BearPouch.distribute`를 호출하여 받은 자금을 지분 가치에 따라 구성된 수령인에게 분배합니다.
각 수령인에게 전송할 금액 계산은 `FixedPointMathLib.mulWadUp`을 사용하여 결과를 가장 가까운 정수로 반올림합니다.

```solidity
File: BearPouch.sol

61            if (xferERC20) {
62                paymentToken.safeTransferFrom(
63  @>                 msg.sender, distributions[i].recipient, amountERC20.mulWadUp(distributions[i].share)
64                );
65            }
66
67            // xfer the ETH
68            if (xferETH) {
69                SafeTransferLib.safeTransferETH(
70   @>                distributions[i].recipient, (msg.value).mulWadUp(distributions[i].share)
71                );
72            }
```

전송할 금액을 반올림하면 총 전송 금액이 총 수신 금액보다 커지는 상황이 발생하여 트랜잭션이 되돌려지고 NFT 판매가 완료되지 않을 수 있습니다.

## 개념 증명 (Proof of concept)

```solidity
pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import "solady/src/utils/FixedPointMathLib.sol";
import "src/GameRegistry.sol";
import "src/BearPouch.sol";
import "src/Constants.sol";

contract PoC is Test {
    GameRegistry gameRegistry;
    BearPouch bearPouch;

    function setUp() public {
        gameRegistry = new GameRegistry();
        gameRegistry.grantRole(Constants.GAME_INSTANCE, address(this));

        IBearPouch.DistributionConfig[] memory distributions = new IBearPouch.DistributionConfig[](2);
        uint256 shareAlice = FixedPointMathLib.WAD / 3;
        uint256 shareBob = FixedPointMathLib.WAD - shareAlice;
        distributions[0] = IBearPouch.DistributionConfig({recipient: makeAddr("Alice"), share: shareAlice});
        distributions[1] = IBearPouch.DistributionConfig({recipient: makeAddr("Bob"), share: shareBob});
        bearPouch = new BearPouch(address(gameRegistry), address(0), distributions);
    }

    function test_distributeReverts() public {
        vm.expectRevert(SafeTransferLib.ETHTransferFailed.selector);
        bearPouch.distribute{value: 0.099 ether}(0);
        // Alice's share = 0.033 ether
        // Bob's share = 0.066 ether + 1 wei
        // Total = 0.099 ether + 1 wei
    }
}
```

## 권장 사항

`mulWadUp` 대신 `mulWad`를 사용하십시오.

# [H-02] 발행된 꿀단지 한도를 초과할 수 있음 (The limit for jars minted can be exceeded)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

꿀단지를 발행하는 모든 `Beekeeper` 외부 함수는 내부 `_canMintHoneyJar` 함수에 구현된 몇 가지 확인을 수행합니다. 무엇보다도 이 함수는 발행된 꿀단지의 합계와 발행할 새 금액이 체크포인트에 정의된 한도를 초과하지 않는지 확인합니다.

```solidity
File: Beekeeper.sol

168        if (progress.mintedJars.length + amount_ > progress.checkpoints[progress.checkpoints.length - 1]) {
169            revert MekingTooManyHoneyJars();
170        }
```

모든 확인이 통과되면 `_mintHoneyJar`가 호출되어 꿀단지를 발행합니다. 이 함수에서 `progress.mintedJars`는 `honeyJar.batchMint`가 호출된 후에만 업데이트된다는 점에 유의하는 것이 중요합니다.

```solidity
File: Beekeeper.sol

251    function _mintHoneyJar(address to, uint256 amount_) internal returns (uint256) {
252        uint256 tokenId = honeyJar.nextTokenId();
253        honeyJar.batchMint(to, amount_);
254
255        for (uint256 i = 0; i < amount_; ++i) {
256            progress.mintedJars.push(tokenId);
257            ++tokenId;
258        }
(...)
```

`HoneyJar`는 내부 `_safeMint` 함수를 사용하여 꿀단지를 발행합니다. 이 함수는 수신자 계약이 존재하는 경우 `onERC721Received` 함수를 호출합니다. 이것은 `progress.mintedJars` 배열이 업데이트되기 전에 `Beekeeper` 계약에 다시 진입(re-enter)하여 더 많은 꿀단지를 발행하는 데 사용될 수 있으며, 이를 통해 발행 한도를 초과할 수 있습니다. 이 공격의 또 다른 결과는 `progress.mintedJars` 배열에 저장된 토큰 ID가 올바르지 않게 된다는 것입니다.

## 개념 증명 (Proof of concept)

`Beekeeper.t.sol` 파일에 다음 계약을 추가하십시오.

```solidity
contract Attacker {
    Beekeeper public beekeeper;
    uint256 public mintEthPrice;
    uint256 public reentranceAmount;

    constructor(Beekeeper _beekeeper, uint256 _mintEthPrice) {
        beekeeper = _beekeeper;
        mintEthPrice = _mintEthPrice;
    }

    function mek(uint256 amount, uint256 _reentranceAmount) public payable {
        reentranceAmount = _reentranceAmount;
        beekeeper.mekHoneyJarWithETH{value: amount * mintEthPrice}(amount);
    }

    function onERC721Received(address, address, uint256, bytes memory) public virtual returns (bytes4) {
        uint256 amount = reentranceAmount;
        if (amount > 0) {
            reentranceAmount = 0;
            beekeeper.mekHoneyJarWithETH{value: amount * mintEthPrice}(amount);
        }
        return this.onERC721Received.selector;
    }
}
```

또한 동일한 파일의 `BeekeeperTest` 계약 내에 다음 함수를 추가하고 `forge test --mt testMintOverLimit -vv`를 실행하십시오.

```solidity
function testMintOverLimit() public {
    // Setup
    uint256 MAX_MEK_JARS = 4;
    uint256[] memory checkpoints = new uint256[](2);
    checkpoints[0] = 2;
    checkpoints[1] = MAX_MEK_JARS;
    beekeeper.setCheckpoints(0, checkpoints);
    gameRegistry.startGame(address(beekeeper));
    beekeeper.startGame(block.chainid, numSleepers);

    // Attack
    uint256 amountOverLimit = 2;
    Attacker attacker = new Attacker(beekeeper, MINT_PRICE_ETH);
    attacker.mek{value: (MAX_MEK_JARS + amountOverLimit) * MINT_PRICE_ETH}(MAX_MEK_JARS, amountOverLimit);

    assertEq(honeyJar.balanceOf(address(attacker)), MAX_MEK_JARS + amountOverLimit);
    Beekeeper.Apiculture memory progress = beekeeper.getProgress();
    for (uint256 i = 0; i < progress.mintedJars.length; i++) {
        console2.log("mintedJars[%s] = %s", i, progress.mintedJars[i]);
    }
}
```

콘솔 출력:

```js
Logs: mintedJars[0] = 1;
mintedJars[1] = 2;
mintedJars[2] = 0;
mintedJars[3] = 1;
mintedJars[4] = 2;
mintedJars[5] = 3;
```

## 권장 사항

꿀단지를 발행하는 모든 외부 함수, 즉 `earlyMekHoneyJarWithERC20`, `earlyMekHoneyJarWithETH`, `mekHoneyJarWithETH`, `mekHoneyJarWithERC20`, `claim` 및 `adminMint`에 재진입 방지(reentrancy guard)를 추가하십시오.

# [M-01] 다음 비발효 꿀단지 검색 시 엣지 케이스 (Edge case in the next non fermented jar search)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

무작위 생성기(randomizer)가 이미 발효된 꿀단지를 선택하면 충돌이 발생합니다.

```solidity
    function _setFermentedJars(uint256[] memory randomNumbers) internal {
        ---SNIP---
        for (uint256 i = 0; i < numFermentedJars; i++) {
            fermentedIndex = randomNumbers[i] % numHoneyJars;
            uint256 fermentedJarId = honeyJarIds[fermentedIndex];
            if (honeyJar.isFermented(fermentedJarId)) {
                // Special case in the event of a collision -- not optimized for gas
                // 충돌 발생 시 특수 케이스 -- 가스 최적화되지 않음
>>              fermentedJarId = _findNextJar(fermentedJarId);
            }
```

이 경우 `Beekeeper` 계약은 발효되지 않은 다른 꿀단지를 찾으려고 시도합니다:

```solidity
    function _findNextJar(uint256 jarId) internal returns (uint256) {
        uint256 supplyCap = honeyJar.supplyCap();

>>      for (uint256 i = jarId + 1; i < supplyCap; i++) {
            if (honeyJar.isFermented(i)) continue;
            return i;
        }
        return type(uint256).max; // if you can't find any.
    }
```

현재 선택된 ID부터 공급 상한(supply cap)까지 검색합니다. 조건과 일치하는 꿀단지가 없으면 type(uint256).max가 반환됩니다. 이 알고리즘은 루프가 첫 번째 tokenId부터 시작하지 않기 때문에 완전히 정확하지 않습니다.

다음 시나리오를 고려하십시오. 공급 상한이 10이고 10개의 토큰이 발행되었으며 tokenId = 10은 이미 발효되었습니다. 무작위 생성기가 다시 tokenId = 10을 선택하고 `_findNextJar`가 호출되면 10번째가 마지막 토큰이므로 ID 1-9인 토큰이 발효되지 않았음에도 불구하고 `type(uint256).max`를 반환합니다.

이제 `checkpoints[progress.checkpoints.length - 1] < supplyCap`인 동일한 시나리오를 살펴보겠습니다. `_findNextJar`를 호출하면 존재하지 않는 꿀단지가 발효됩니다. 게임이 종료되었고 새 꿀단지를 발행할 수 없으므로 하나의 슬리퍼(sleeper) 보상은 획득되지 않은 상태로 남습니다.

## 권장 사항

```diff
+      for (uint256 i = 0; i < supplyCap; i++) {
```

# [M-02] `wakeSleepers()`에 대한 슬리피지 보호 (Slippage protection for `wakeSleepers()`)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

모든 꿀단지는 가치 측면에서 동일하며 슬리퍼(Sleepers)에 잠긴 토큰을 받는 데 사용할 수 있습니다.
사용자는 정확한 슬리퍼를 선택할 수 없으며 항상 다음으로 사용 가능한 슬리퍼가 사용됩니다.
이것은 게임 메커니즘으로 간주됩니다.
문제는 사용자 앨리스가 슬리퍼 2를 받을 것으로 예상하고 트랜잭션에 서명할 수 있지만, 트랜잭션 실행 중에 누군가가 사용자를 프론트런하고 이제 앨리스는 슬리퍼 3을 받기 위해 꿀단지를 소비한다는 것입니다.

## 권장 사항

사용자가 트랜잭션에 서명할 때 원하는 슬리퍼 인덱스를 표시하도록 허용하십시오.

# [M-03] 온체인 무작위성이 악용될 수 있음 (On-chain randomness can be exploited)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

특정 수의 HoneyJar NFT가 발행되면 일정량의 토큰이 발효된 꿀단지로 전환되도록 선택됩니다. 이 발효된 꿀단지는 나중에 `Den` 계약에서 슬리퍼로부터 보상을 받는 데 사용할 수 있습니다.

```solidity
    function _mintHoneyJar(address to, uint256 amount_) internal returns (uint256) {
        uint256 tokenId = honeyJar.nextTokenId();
        honeyJar.batchMint(to, amount_);

        for (uint256 i = 0; i < amount_; ++i) {
            progress.mintedJars.push(tokenId);
            ++tokenId;
        }

        // Find the special honeyJar when a checkpoint is passed.
        uint256 numMinted = progress.mintedJars.length;
        if (numMinted >= progress.checkpoints[progress.checkpointIndex]) {
>>          _fermentJars();
        }

        return tokenId - 1; // returns the lastID created
    }
```

발효될 토큰의 ID는 다음 알고리즘을 사용하여 계산됩니다(하나의 꿀단지 및 충돌 없음에 대한 단순화된 알고리즘):

- 난수 가져오기 `randomBytes = uint256(keccak256(abi.encodePacked(block.timestamp, block.difficulty, msg.sender)))`
- 발효된 꿀단지의 인덱스 가져오기 `id = randomBytes % num of minted jars`
- 발행된 꿀단지 배열에서 ID 가져오기

발효는 발행과 동일한 트랜잭션에서 이루어집니다. 이를 통해 정통한 사용자는 발행된 꿀단지를 수신하고 발효된 꿀단지가 수신될 때까지 트랜잭션을 되돌리는 계약을 생성할 수 있습니다.

민터(minter) 계약의 예:

```solidity
contract Reroll {
    Beekeeper beekeeper;
    uint256 offset;

    constructor(Beekeeper _beekeeper) {
        beekeeper = _beekeeper;
    }

    function predict() internal returns(uint256 id){
        uint256 rand = uint256(keccak256(abi.encodePacked(
            block.timestamp,
            block.difficulty,
            address(this)
        )));
        Beekeeper.Apiculture memory progress = beekeeper.getProgress();
        id = rand % (progress.mintedJars.length + offset);
    }

    function setOffset(uint256 _offset) external {
        offset = _offset;
    }

    function onERC721Received(address,address,uint256 id,bytes calldata) external returns(bytes4) {
        uint256 gibmedat = predict();
        if(id != gibmedat) revert("YIKES");
        return this.onERC721Received.selector;
    }
}
```

`Beekeeper.t.sol`에 대한 코딩된 POC:

```solidity
    function testReroll() public {
        address alice = address(0xa11cE);
        Reroll reroll = new Reroll(beekeeper);

        vm.deal(alice, 100 ether);
        vm.deal(address(reroll), 100 ether);

        _puffPuffPassOut();
        uint256 newSleepers = 10;
        uint256 mintAmount = 100;
        uint256[] memory checkpoints = new uint256[](3);
        checkpoints[0] = 20;
        checkpoints[1] = 40;
        checkpoints[2] = mintAmount;

        // Set the game
        gameRegistry.stopGame(address(beekeeper));
        beekeeper.setCheckpoints(0, checkpoints);
        beekeeper.setNumSleepers(newSleepers);
        gameRegistry.startGame(address(beekeeper));

        // First minter
        vm.prank(alice);
        beekeeper.mekHoneyJarWithETH{value: MINT_PRICE_ETH * 19}(19);
        // Second minter will get the fermented jar even though Alice has 19 jars
        vm.warp(1);
        vm.startPrank(address(reroll));
        reroll.setOffset(1);
        vm.expectRevert("YIKES");
        beekeeper.mekHoneyJarWithETH{value: MINT_PRICE_ETH}(1);
        // Try again
        vm.warp(2);
        uint256 minted = beekeeper.mekHoneyJarWithETH{value: MINT_PRICE_ETH}(1);
        assertEq(honeyJar.isFermented(minted), true);
    }
```

## 권장 사항

Chainlink VRF와 같은 서비스를 사용하거나 자체 내부 서버를 사용하여 오프체인에서 난수를 생성하는 것을 고려하십시오.

# [M-04] 사용자가 일시적으로 `HoneyJar` 계약이 LZ 메시지를 수신하는 것을 차단할 수 있음 (The user can temporarily block the `HoneyJar` contract from receiving LZ messages)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`HoneyJar` 토큰은 Layer-Zero 프로토콜을 통한 크로스 체인 통신을 위해 `ONFT721.sol` 및 `NonblockingLzApp.sol` 계약을 사용합니다. 엔드포인트에서 메시지를 수신하면 `NonblockingLzApp`은 `_blockingLzReceive` 함수를 재정의합니다:

```solidity
// LzApp.sol
    function lzReceive(
        uint16 _srcChainId,
        bytes calldata _srcAddress,
        uint64 _nonce,
        bytes calldata _payload
    ) public virtual override {
        // lzReceive must be called by the endpoint for security
        require(_msgSender() == address(lzEndpoint), "LzApp: invalid endpoint caller");
        ---SNIP---
>>      _blockingLzReceive(_srcChainId, _srcAddress, _nonce, _payload);
    }

// NonblockingLzApp.sol
    function _blockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) internal virtual override {
>>      (bool success, bytes memory reason) = address(this).excessivelySafeCall(
            gasleft(),
            150,
            abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload)
        );
        if (!success) {
>>          _storeFailedMessage(_srcChainId, _srcAddress, _nonce, _payload, reason);
        }
    }
```

거기서 `nonBlockingReceive` 함수를 호출하여 결국 지정된 ID를 가진 토큰을 수신자에게 발행(mint)합니다. `nonBlockingReceive`가 되돌려지면 페이로드는 나중에 다시 시도하기 위해 저장됩니다:

```solidity
    function _creditTo(uint16, address _toAddress, uint256 _tokenId, bool _isFermented) internal override {
        require(
            !_exists(_tokenId) || (_exists(_tokenId) && ERC721.ownerOf(_tokenId) == address(this)), "invalid token ID"
        );

        // Token shouldn't exist
>>      _safeMint(_toAddress, _tokenId);
        if (_isFermented) fermented.set(_tokenId);
    }
```

`_safeMint` 함수는 수신 계약에 대한 콜백을 시작하므로, 해당 콜백 내에서 모든 트랜잭션 가스가 소비되는 문제가 발생할 수 있습니다. 이 경우 페이로드를 저장할 가스가 남지 않아 `Endpoint.sol`의 catch 블록으로 끝나게 됩니다:

```solidity
    function receivePayload(uint16 _srcChainId, bytes calldata _srcAddress, address _dstAddress, uint64 _nonce, uint _gasLimit, bytes calldata _payload) external override receiveNonReentrant {
        ---SNIP---
        // block if any message blocking
        StoredPayload storage sp = storedPayload[_srcChainId][_srcAddress];
>>      require(sp.payloadHash == bytes32(0), "LayerZero: in message blocking");

        try ILayerZeroReceiver(_dstAddress).lzReceive{gas: _gasLimit}(_srcChainId, _srcAddress, _nonce, _payload) {
            // success, do nothing, end of the message delivery
        } catch (bytes memory reason) {
            // revert nonce if any uncaught errors/exceptions if the ua chooses the blocking mode
>>          storedPayload[_srcChainId][_srcAddress] = StoredPayload(uint64(_payload.length), _dstAddress, keccak256(_payload));
            emit PayloadStored(_srcChainId, _srcAddress, _dstAddress, _nonce, _payload, reason);
        }
```

그러면 `HoneyJar`는 소유자가 `forceResumeReceive`를 호출하고 페이로드를 제거할 때까지 소스 체인에서 메시지를 받을 수 없습니다.

이 취약점에 대한 자세한 설명은 이 문서(https://www.trust-security.xyz/post/learning-by-breaking-a-layerzero-case-study-part-3)를 참조하십시오.

## 권장 사항

`catch` 블록에 페이로드를 성공적으로 저장하기 위해 최소 30k 가스를 남겨두는 것을 고려하십시오:

```diff
    function _blockingLzReceive(
        uint16 _srcChainId,
        bytes memory _srcAddress,
        uint64 _nonce,
        bytes memory _payload
    ) internal virtual override {
      (bool success, bytes memory reason) = address(this).excessivelySafeCall(
-           gasleft() - 30000, // in theory gasLeft() is always greater than 30000 because of checks in the send
            150,
            abi.encodeWithSelector(this.nonblockingLzReceive.selector, _srcChainId, _srcAddress, _nonce, _payload)
        );
        if (!success) {
          _storeFailedMessage(_srcChainId, _srcAddress, _nonce, _payload, reason);
        }
    }
```

# [M-05] `NATIVE_TYPE` 유형의 슬리퍼를 청구한 첫 번째 사용자가 모든 보상을 받음 (First user to claim a sleeper of type `NATIVE_TYPE` gets all rewards)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`Den` 계약이 보유한 슬리퍼(Sleepers)는 다양한 유형일 수 있습니다. `NATIVE_TYPE` 유형은 발효된 꿀단지 보유자에게 체인의 기본 토큰(예: ether)을 보상합니다.

파티에는 동일한 유형의 슬리퍼가 여러 개 있을 수 있으며, 각각 다른 `amount`의 보상을 가집니다. 그러나 `NATIVE_TYPE` 유형의 슬리퍼가 전송될 때 전송되는 금액은 특정 슬리퍼의 금액이 아니라 계약의 전체 잔액입니다.

즉, `NATIVE_TYPE` 유형의 슬리퍼를 청구한 첫 번째 사용자가 해당 유형의 모든 슬리퍼의 보상을 받게 되며 후속 사용자는 아무것도 받지 못합니다.

## 권장 사항

```diff
        } else if (NATIVE_TYPE == tokenType) {
-           SafeTransferLib.safeTransferAllETH(to); // winner gets all eth in the contract
+           SafeTransferLib.safeTransferETH(to, sleeper_.amount);
        } else {
```

# [L-01] `HoneyJarPortal` 계약의 잘못된 가스 추정 (Incorrect gas estimation in the `HoneyJarPortal` contract)

`sendStartGame` 함수는 Layer Zero 프로토콜을 통해 메시지를 보내기 전에 가스 추정에 `1000 * numSleepers`의 추가 가스 비용을 포함합니다.

```solidity
    function sendStartGame(address payable refundAddress_, uint256 destChainId_, uint256 numSleepers_)
        external
        payable
        override
        onlyRole(Constants.GAME_INSTANCE)
    {
        uint16 lzDestId = lzChainId[destChainId_];
        if (lzDestId == 0) revert LzMappingMissing(destChainId_);

        bytes memory adapterParams = msgAdapterParams[MessageTypes.START_GAME];

        // Will check adapterParams against minDstGas
        _checkGasLimit(
            lzDestId,
            uint16(MessageTypes.START_GAME),
            adapterParams,
>>          1000 * numSleepers_ // Padding for each NFT being stored
        );
```

그러나 대상 체인에서 `Beekeeper.startGame`은 많은 가스를 사용하지 않으며 계산에 `numSleepers`를 포함하지 않습니다.

```solidity
    function startGame(uint256 srcChainId_, uint256 numSleepers_) external override onlyRole(Constants.PORTAL) {
        if (progress.checkpoints.length == 0) revert ProgressNotInitialized();
        if (progress.checkpoints.length > numSleepers_) revert InvalidInput("startGame::checkpoints");
        party.assetChainId = srcChainId_;
        party.mintChainId = getChainId(); // On the destination chain you MUST be able to mint.
        party.numSleepers = numSleepers_;
        canMakeJars = true;
        emit SlumberPartyStarted();
        return;
    }
```

결과적으로 호출자는 대상 체인에서 `startGame`을 실행하는 데 필요한 것보다 더 많은 가스를 지불해야 합니다.

# [L-02] `isSource` 플래그에 대한 설정자 없음 (No setter for the `isSource` flag)

`isSource` 매개변수는 Beekeeper 계약이 Den 계약과 동일한 체인에서 인스턴스화되는 것을 확인하고 방지하는 데 사용됩니다.

```solidity
    function setBeekeeper(address beekeeperAddress_) external onlyRole(Constants.GAME_ADMIN) {
>>      if (isSource) {
            revert SourceChain();
        }
        beekeeper = IBeekeeper(beekeeperAddress_);

        emit BeekeeperSet(beekeeperAddress_);
    }
```

불행히도 `isSource`에 대한 설정자가 없으므로 거짓(false)일 수만 있습니다. 생성자에서 `isSource`를 불변(immutable)으로 지정하는 것을 고려하십시오.

# [L-03] 2단계 소유권 이전 사용 (Use two step ownership transfer)

2단계 소유권 이전은 소유자의 권한을 받는 사람이 자체 계약 호출을 통해 적극적으로 수락하도록 요구함으로써 계약 소유권이 실수로 처리할 수 없는 주소(예: 주소의 오타)로 이전되는 것을 방지합니다.
MultisigOwnable.sol 1단계 소유권 이전:

```solidity
    function transferRealOwnership(address newRealOwner) public onlyRealOwner {
>>      realOwner = newRealOwner;
    }
```

대신 2단계 소유권 이전을 사용하십시오:

```diff
+   address private _pendingOwner;
<...>
    function transferRealOwnership(address newRealOwner) public onlyRealOwner {
-       realOwner = newRealOwner;
+       _pendingOwner = newRealOwner;
    }

+   function acceptOwnership() public {
+       address sender = msg.sender;
+       require (pendingOwner() == sender, "MultisigOwnable: caller is not the new real owner");
+       realOwner = sender;
+       delete _pendingOwner;
+   }
```

# [L-04] `GAME_ADMIN`이 체크포인트 완료를 강제할 수 있음 (`GAME_ADMIN` can force checkpoint finishing)

`Beekeeper.setAdminMint`는 게임이 활성화된 경우에도 `GAME_ADMIN` 역할이 `adminMintAmount` 변수를 증가시킬 수 있도록 합니다. Hibernation Games에 대한 프로토콜 문서에 언급된 대로 "Honeycomb이 매진되지 않으면 OG Bong Bear는 Honey Jar Cave에서 영원히 잠을 잡니다. 계약, 따라서 동굴은 불변입니다"(https://0xhoneyjar.mirror.xyz/soVN56Jla_Y9x2USB9UO2Pw3T0ALiHwAbI0oxC5AA0M). 그러나 이 불변식은 `GAME_ADMIN` 역할에 의해 깨질 수 있습니다.

```solidity
    function setAdminMint(uint256 adminMintAmount_) external onlyRole(Constants.GAME_ADMIN) {
        adminMintAmount = adminMintAmount_;

        emit AdminMintAmount(adminMintAmount_);
    }
```

게임 상태를 확인하고 게임이 활성화된 경우 되돌리는 것을 고려하십시오.

# [L-05] 슬리퍼가 Den으로 전송되었는지 확인되지 않음 (It is not ensured that sleepers have been transferred to the Den)

`Den` 설정에는 파티에 추가할 슬리퍼 배열이 필요합니다. 이 작업은 `setSlumberParty` 또는 `addToParty` 함수 호출을 통해 관리자가 수행합니다.

두 함수 모두 호출자가 슬리퍼를 전송할지 여부를 결정하는 데 사용되는 부울 매개변수를 받습니다. 이 값이 `false`로 설정되면 슬리퍼가 이미 `Den` 계약으로 전송되었거나 나중에 전송될 것으로 예상됩니다.

그러나 `startGame` 함수에는 슬리퍼가 `Den` 계약으로 전송되었는지 확인하는 검사가 없습니다. 따라서 슬리퍼가 `Den` 계약으로 전송되지 않은 상태에서 게임이 시작될 수 있으며, 이는 `wakeSleeper` 함수가 실패하도록 유발할 수 있습니다.

# [L-06] `Den`이 `ERC20` 토큰에 대해 `safe` 메서드를 사용하지 않음 (`Den` does not use `safe` methods for `ERC20` tokens)

일부 ERC20 토큰은 전송 시 조용히 실패하여 자금 손실로 이어질 수 있습니다. `SafeERC20` 라이브러리가 `IERC20` 인터페이스에 할당되어 있지만, 계약은 이를 방지하기 위해 `safeTransfer` 및 `safeTransferFrom` 함수를 사용하지 않습니다.

```solidity
File: Den.sol

196         if (from == address(this)) {
197             // transferFrom does an allowance check which is not needed for the contract owned assets.
198             IERC20(sleeper_.tokenAddress).transfer(to, sleeper_.amount);
199         } else {
200             IERC20(sleeper_.tokenAddress).transferFrom(from, to, sleeper_.amount);
```

# [L-07] LayerZero 수수료를 ZRO 토큰으로 지불할 수 없음 (LayerZero fees cannot be paid in ZRO token)

`_zroPaymentAddress` 필드를 address(0)으로 하드코딩하면 프로토콜이 수수료 지불 옵션으로 ZRO 토큰을 사용할 수 없습니다. 수수료 지불에 유연성을 허용하기 위해 `_zroPaymentAddress` 필드를 입력 매개변수로 전달하는 것을 고려하십시오.

이는 LayerZero [통합 체크리스트](https://docs.layerzero.network/v1/developers/evm/troubleshooting/integration-checklist)에 명시되어 있습니다.

> 수수료를 추정하고 메시지를 보낼 때 주소 0(address(0))을 zroPaymentAddress로 하드코딩하지 마십시오. 대신 매개변수로 전달하십시오.

```solidity
File: HoneyJarPortal.sol

155        _lzSend(lzDestId, payload, refundAddress_, address(0x0), adapterParams, msg.value);
```

# [L-08] 256개 이상의 꿀단지를 발효하면 무작위성이 제거됨 (Fermenting more than 256 jars eliminates randomness)

`Beekeeper._getRandomWords`에서 `numWords_`가 256보다 크면 모든 시드(seed)가 0이 되어 모든 종류의 무작위성이 제거되고 처음 발행된 꿀단지에 이익이 됩니다.

```solidity
File: Beekeeper.sol

        for (uint256 i = 0; i < numWords_; i++) {
            uint256 mask = (1 << (256 / numWords_)) - 1; // Generate mask for splitting the bytes
            seeds[i] = randomBytes >> (256 - (i + 1) * (256 / numWords_)) & mask; // Extract and apply mask
        }
```

단일 트랜잭션에서 발효할 꿀단지 수를 제한하는 것을 고려하십시오.

# [L-09] 꿀단지 발효로 인한 가스 고갈 가능성 (The fermenting of jars can cause gas exhaustion)

`Beekeeper._setFermentedJars`는 발효할 다음 꿀단지를 찾기 위해 `numFermentedJars`를 반복합니다. 선택한 `fermentedJarId`가 이미 발효된 꿀단지에 해당하는 경우 발효되지 않은 꿀단지를 찾을 때까지 `_findNextJar`에서 새로운 반복이 발생합니다.

발효할 꿀단지 수와 이미 발효된 꿀단지 수에 따라 반복 횟수가 많아져 결국 가스가 고갈될 수 있으며, 이로 인해 새 꿀단지 발행 및 발행된 꿀단지 발효가 방지될 수 있습니다.

단일 트랜잭션에서 발효할 꿀단지 수를 제한하고 발효할 다음 꿀단지를 찾는 보다 효율적인 방법을 구현하는 것을 고려하십시오. 예를 들어 발행되었지만 아직 발효되지 않은 모든 꿀단지를 목록에서 추적하여 수행할 수 있습니다.

# [L-10] 단일 발행에서 여러 체크포인트에 도달하면 발효가 지연될 수 있음 (Reaching multiple checkpoints in a single mint can delay fermentation)

`Beekeeper`에서 토큰이 발행될 때 둘 이상의 체크포인트에 도달할 가능성이 있습니다. 이렇게 하면 체크포인트가 효과적으로 건너뛰어지고 마지막 체크포인트에 도달할 때까지 꿀단지 발효가 지연됩니다.

이는 `_mintHoneyJar` 함수의 natspec에서 인정하고 있습니다:

```solidity
/// @dev if the amount_ is > than multiple checkpoints, accounting WILL mess up.
```

`IBeekeeper` 인터페이스의 `Apiculture` 구조체 natspec에는 이 문제에 대한 의도된 해결책에 대한 언급도 있습니다:

```solidity
/// @dev the gap between checkpoints MUST be big enough so that a user can't mint through multiple checkpoints.
```

그러나 이 솔루션은 구현하기 어려울 수 있습니다. 충분히 큰 간격을 만들려면 매우 큰 공급량이 필요하고 잠재적으로 새 체크포인트에 도달하지 못하게 될 수 있기 때문입니다.

잠재적인 해결책은 `_mintHoneyJar` 함수에 확인을 추가하여 발행된 토큰 양이 단일 체크포인트에서 발행될 수 있는 토큰 수를 초과하지 않도록 하는 것입니다. 그러나 이렇게 하려면 사용자가 체크포인트 한도를 초과하지 않고 모든 토큰을 청구할 수 있도록 부분 청구를 수행할 수 있는 기능도 필요합니다.

# [L-11] EOA 소유자가 `HoneyJar.sol`에서 LZ 설정을 변경할 수 있음 (EOA owner can change LZ settings in the `HoneyJar.sol`)

`HoneyJar.sol`은 `multisigOwnable.sol`을 상속하므로 두 명의 소유자가 있습니다. 토큰의 관리자 기능을 제어하는 멀티시그인 `realOwner`와 OpenSea 컬렉션 관리에 사용되는 EOA인 `owner`입니다.

아래 스니펫에서 `realOwner`가 관리자 기능에 사용되는 것을 볼 수 있습니다:

```solidity
    function setBaseURI(string calldata baseURI_) external onlyRealOwner {

    function setGenerated(bool generated_) external onlyRealOwner {
```

상속된 계약 `HoneyJarONFT` 및 `LzApp`을 확인하면 중요한 관리자 기능이 하위 `owner`에 의해 제어되는 것을 볼 수 있으며, 이는 EOA이므로 보안이 취약한 것으로 간주됩니다:

HoneyJarONFT.sol

```solidity
    function setMinGasToTransferAndStore(uint256 _minGasToTransferAndStore) external onlyOwner {

    // ensures enough gas in adapter params to handle batch transfer gas amounts on the dst
    function setDstChainIdToTransferGas(uint16 _dstChainId, uint256 _dstChainIdToTransferGas) external onlyOwner {

    // limit on src the amount of tokens to batch send
    function setDstChainIdToBatchLimit(uint16 _dstChainId, uint256 _dstChainIdToBatchLimit) external onlyOwner {
```

LzApp.sol

```solidity
    function setSendVersion(uint16 _version) external override onlyOwner {

    function setReceiveVersion(uint16 _version) external override onlyOwner {

    function forceResumeReceive(uint16 _srcChainId, bytes calldata _srcAddress) external override onlyOwner {

    // _path = abi.encodePacked(remoteAddress, localAddress)
    // this function set the trusted path for the cross-chain communication
    function setTrustedRemote(uint16 _remoteChainId, bytes calldata _path) external onlyOwner {

    function setTrustedRemoteAddress(uint16 _remoteChainId, bytes calldata _remoteAddress) external onlyOwner {

    function setPrecrime(address _precrime) external onlyOwner {

    function setMinDstGas(
        uint16 _dstChainId,
        uint16 _packetType,
        uint _minGas
    ) external onlyOwner {

    // if the size is 0, it means default size limit
    function setPayloadSizeLimit(uint16 _dstChainId, uint _size) external onlyOwner {
```

위의 함수를 재정의하여 `realOwner`만 호출할 수 있도록 하는 것을 고려하십시오.

# [L-12] ETH 분배 시 되돌리기 (Reverts on ETH distribution)

BearPounch.distribute()는 지분에 따라 자금을 분배합니다. ETH 분배는 `SafeTransferLib.safeTransferETH()`를 통해서도 허용됩니다. 수신자가 되돌리면(revert) 이 함수는 여전히 되돌려질 수 있습니다.

```solidity
    // The regular variants:
    // - Forwards all remaining gas to the target.
    // - Reverts if the target reverts.
    // - Reverts if the current contract has insufficient balance.
```

수신자는 되돌리기를 사용하여 일부 구매자를 차단할 수 있습니다.

ETH가 전송되지 않을 때 "청구(claim)" 방식을 사용하고 계약이 수신자가 청구할 수 있는 내부 잔액을 늘리는 것을 고려하십시오.

# [L-13] `batchMint()`를 차단하는 `mintTokenId()` (`mintTokenId()` to block `batchMint()`)

`HoneyJar.mintTokenId()`는 제공된 정확한 `tokenId`를 발행할 수 있도록 합니다.
이는 `Beekeeper`가 현재 금액에서 N개의 토큰을 발행하는 `batchMint()`를 사용하기 때문에 위험한 접근 방식입니다. `HoneyJar.mintTokenId()`에서 발행된 토큰이 이 일괄 발행 중에 만나면 트랜잭션이 되돌려지고 `Beekeeper`에서 새 토큰을 발행할 방법이 없습니다.

`tokenId`를 안전하게 발행하는 규칙을 구현하거나 함수를 제거하는 것을 고려하십시오.
