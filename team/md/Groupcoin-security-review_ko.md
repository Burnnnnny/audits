# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **ControlCplusControlV/groupcoin** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Groupcoin 정보 (About Groupcoin)

Groupcoin 팩토리는 텔레그램 그룹을 위해 설계된 커스텀 ERC20 토큰인 Groupcoin을 생성하고 관리하며, 통합된 Uniswap V3 유동성 관리 및 본딩 커브 가격 모델을 갖추고 있습니다. LiquidityLocker는 Uniswap V3 유동성 포지션을 보유하여 토큰 생성자와 프로토콜에 수수료를 수집하고 분배할 수 있도록 합니다.

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

_검토 커밋 해시_ - [ebcf64a54fc9a028ae115425c770fa325c27138d](https://github.com/ControlCplusControlV/groupcoin/tree/ebcf64a54fc9a028ae115425c770fa325c27138d)

_수정 검토 커밋 해시_ - [5b2a6cc9198f3af50fffa082e64225d5ce91db0d](https://github.com/ControlCplusControlV/groupcoin/tree/5b2a6cc9198f3af50fffa082e64225d5ce91db0d)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `LiquidityLocker`
- `GroupcoinFactory`

# 발견 사항 (Findings)

# [C-01] 충분한 ETH를 모으지 않고도 Uniswap v3 출시를 활성화할 수 있음 (Uniswap v3 launch can be enabled without raising enough ETH)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`enableUniswapV3Launch`는 `unitPrice >= config.thresholdPrice`인 경우에만 실행할 수 있으며, 이는 주어진 토큰을 구매하기 위해 최소한 `config.token1Amount` ETH가 지불되었음을 강제하기 위한 것입니다. 충분한 유동성을 공급하기에 충분한 ETH가 지불되었는지 확인하는 것이 중요합니다. 그러나 사용자는 `unitPrice`가 `thresholdPrice`를 충족하기 위해 약 8,200,000개의 토큰만 구매하면 되는데, 이는 충분한 ETH를 확보하기 위해 판매해야 하는 양보다 약 155,000개 적습니다. 우리는 다음 테스트로 이를 증명할 수 있습니다:

```solidity
function testLaunchToUniswapV3() public {
    bytes32 message = keccak256(abi.encodePacked(address(this), "Telegram WIF Coin", "WIF"));
    (uint8 v, bytes32 r, bytes32 s) = vm.sign(0xbeef, message.toEthSignedMessageHash());
    bytes memory signature = abi.encodePacked(r, s, v);

    (bytes32 salt,) = factory.generateSalt(address(this), "Telegram WIF Coin", "WIF");
    uint256 newToken = factory.launchGroupCoin("Telegram WIF Coin", "WIF", signature, salt, 0);
    uint256 price = factory.getBuyPriceAfterFee(newToken, 8_357_142);

    vm.deal(User01, price);
    vm.startPrank(User01);

    factory.buy{ value: price }(newToken, 8_200_000);

    factory.enableUniswapV3Launch(newToken);
}
```

출시가 확정된 후에는 더 이상 토큰을 `buy`할 수 없기 때문에, 이와 같이 조기 확정된 후 Uniswap v3를 성공적으로 출시하는 유일한 방법은 사용 가능한 경우 다른 토큰에 의해 모금된 계약 내의 추가 ETH를 사용하는 것이며, 이는 계약을 지급 불능 상태로 만듭니다.

## 권장 사항

이에 대한 가장 좋은 해결책은 토큰을 구매하기 위해 충분한 ETH가 지불되었음을 확신할 수 있도록 토큰의 `totalSupply`에 따라 `enableUniswapV3Launch`를 제어하는 것입니다.

# [C-02] 언제든지 Uniswap v3에 토큰을 출시하여 ETH를 고갈시킬 수 있음 (Tokens can be launched to Uniswap v3 anytime, allowing to drain ETH)

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`GroupcoinFactory`에서 우리는 2단계 접근 방식을 통해 Uniswap v3 출시를 제어하려고 합니다. 먼저 사용자는 `enableUniswapV3Launch`를 호출하는데, 이는 하나의 토큰에 대한 현재 구매 가격이 최소 `config.thresholdPrice`인 경우에만 성공합니다. 그런 다음 이전 단계를 성공적으로 실행한 경우에만 `launchGroupCoinToUniswapV3`를 실행할 수 있어야 합니다. 그러나 논리적 오류로 인해 `enableUniswapV3Launch`를 먼저 실행하지 않고도 `launchGroupCoinToUniswapV3`를 실행할 수 있습니다.

`launchGroupCoinToUniswapV3`에서 다음 확인은 이전 블록에서 출시를 활성화한 경우에만 통과하도록 의도되었습니다:

```solidity
if (block.number <= commitLaunch[tokenId]) {
    revert CantLaunchPoolYet();
}
```

그러나 `commitLaunch[tokenId]`의 설정되지 않은 값은 0이므로, 활성화되지 않은 `tokenId`에 대해서도 이것이 통과됩니다. 우리는 다음 테스트로 이를 증명할 수 있습니다:

```solidity
function testLaunchToUniswapV3() public {
    bytes32 message = keccak256(abi.encodePacked(address(this), "Telegram WIF Coin", "WIF"));
    (uint8 v, bytes32 r, bytes32 s) = vm.sign(0xbeef, message.toEthSignedMessageHash());
    bytes memory signature = abi.encodePacked(r, s, v);

    (bytes32 salt,) = factory.generateSalt(address(this), "Telegram WIF Coin", "WIF");
    uint256 newToken = factory.launchGroupCoin("Telegram WIF Coin", "WIF", signature, salt, 0);

    // @audit deal ETH so we can add as liquidity
    vm.deal(address(factory), 2 ether);

    factory.launchGroupCoinToUniswapV3(newToken);
}
```

위에서 볼 수 있듯이 먼저 활성화하지 않고도 Uniswap v3에 토큰을 출시할 수 있으며, 이는 출시되기 위한 의도된 기준을 충족할 필요가 없음을 의미합니다. 이것의 영향은 공격자가 본딩 커브의 시작 부분에서 저렴하게 토큰을 원자적으로 구매한 다음 Uniswap v3에 토큰을 출시하여 다른 토큰의 유동성 포지션에 할당될 계약의 ETH를 가져와서 마지막으로 부풀려진 가격에 저렴한 토큰을 판매할 수 있다는 것입니다.

## 권장 사항

`commitLaunch[tokenId]`가 설정되지 않은 상태가 아니도록 해야 합니다. 예:

```solidity
if (block.number <= commitLaunch[tokenId] || commitLaunch[tokenId] == 0) {
    revert CantLaunchPoolYet();
}
```

# [H-01] 동일한 Groupcoin을 두 번 이상 출시할 수 있음 (The same Groupcoin can be launched more than once)

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

`launchGroupCoinToUniswapV3`에는 Groupcoin이 이미 출시되지 않았다는 검증이 없습니다. 정상적인 상황에서는 동일한 토큰 쌍과 수수료로 여러 풀을 생성할 수 없기 때문에 이것은 문제가 되지 않습니다. 그러나 소유자는 `setUniswapConfig`를 통해 언제든지 수수료를 변경할 수 있습니다. 결과적으로 소유자가 수수료를 변경하면 이전 수수료로 이전에 출시된 모든 Groupcoin을 다시 출시할 수 있습니다.

Groupcoin을 다시 출시하면 추가 토큰이 발행되어 `totalSupply`가 의도한 1,000만 토큰 최대치를 초과하게 됩니다. 또한 ETH도 유동성 포지션에 제공되고 Groupcoin을 처음 판매하여 받은 ETH는 이미 이전 출시에 사용되었으므로, 이는 다른 Groupcoin 판매에서 받은 ETH를 가져와 다른 Groupcoin이 출시되는 것을 방지합니다.

## 권장 사항

`launched[tokenId] = true`인 경우 `launchGroupCoinToUniswapV3` 상단에서 되돌리기(revert)를 트리거하십시오.

# [H-02] ETH가 `GroupcoinFactory` 계약에 잠김 (ETH gets locked in the `GroupcoinFactory` contract)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

Groupcoin을 Uniswap v3에 출시할 때 판매로 받은 모든 ETH를 새로 생성된 Uniswap v3 풀에 유동성으로 제공하려고 합니다. 그러나 아래에서 볼 수 있듯이 일부 ETH는 래핑되어 유동성 포지션에 추가되지 않아 `GroupcoinFactory` 계약에 영구적으로 잠길 수 있습니다.

`launchGroupCoinToUniswapV3`에서 우리는 `config.token1Amount`에 의해 결정된 고정된 양의 ETH를 래핑하며, 이는 나중에 생성된 Uniswap v3 풀에 유동성으로 예치됩니다:

```solidity
IWETH9(WETH).deposit{ value: config.token1Amount }();
```

구매가 허용된 토큰의 임계값을 초과하기 전에 `config.thresholdPrice`에 도달합니다. 우리는 출시 전에 더 적은 토큰을 구매하도록 `testLaunchToUniswapV3` 함수를 수정하여 이를 증명할 수 있습니다. 이는 2000개 이상의 더 적은 토큰으로 작동합니다:

```solidity
function testLaunchToUniswapV3() public {
    bytes32 message = keccak256(abi.encodePacked(address(this), "Telegram WIF Coin", "WIF"));
    (uint8 v, bytes32 r, bytes32 s) = vm.sign(0xbeef, message.toEthSignedMessageHash());
    bytes memory signature = abi.encodePacked(r, s, v);

    (bytes32 salt,) = factory.generateSalt(address(this), "Telegram WIF Coin", "WIF");
    uint256 newToken = factory.launchGroupCoin("Telegram WIF Coin", "WIF", signature, salt, 0);
    uint256 price = factory.getBuyPriceAfterFee(newToken, 8_357_142);

    vm.deal(User01, price);
    vm.startPrank(User01);

    // @audit we modify the buy amount to be 2110 tokens less and launch still succeeds
    factory.buy{ value: price }(newToken, 8_355_000);

    factory.enableUniswapV3Launch(newToken);
    vm.roll(block.number + 1);
    factory.launchGroupCoinToUniswapV3(newToken);
}
```

이것은 또한 우리가 래핑하는 고정된 양의 ETH가 더 많은 토큰을 구매하여 받을 수 있는 최대 ETH 양보다 적다는 것을 증명합니다. 그렇지 않으면 출시할 수 있는 충분한 ETH가 없기 때문입니다.

풀에 래핑되어 예치된 ETH의 양이 더 많은 토큰을 구매하여 받을 수 있는 최대 ETH 양보다 적기 때문에 일부 ETH가 계약에 남을 수 있습니다. 계약에서 초과 ETH를 인출할 방법이 없기 때문에 이 초과 ETH는 `GroupcoinFactory`에 영구적으로 잠깁니다. 우리는 `testLaunchToUniswapV3`의 다음 수정된 버전으로 이를 증명할 수 있습니다:

```solidity
function testLaunchToUniswapV3() public {
    bytes32 message = keccak256(abi.encodePacked(address(this), "Telegram WIF Coin", "WIF"));
    (uint8 v, bytes32 r, bytes32 s) = vm.sign(0xbeef, message.toEthSignedMessageHash());
    bytes memory signature = abi.encodePacked(r, s, v);

    (bytes32 salt,) = factory.generateSalt(address(this), "Telegram WIF Coin", "WIF");
    uint256 newToken = factory.launchGroupCoin("Telegram WIF Coin", "WIF", signature, salt, 0);
    uint256 price = factory.getBuyPriceAfterFee(newToken, 8_357_142);

    vm.deal(User01, price);
    vm.startPrank(User01);

    factory.buy{ value: price }(newToken, 8_357_110);

    factory.enableUniswapV3Launch(newToken);

    vm.roll(block.number + 1);

    factory.launchGroupCoinToUniswapV3(newToken);

    uint256 ethBalance = address(factory).balance;
    assertEq(ethBalance, 0);
}
```

위에서 볼 수 있듯이 8,357,110개의 토큰을 구매하면 `GroupcoinFactory` 계약에 약 0.001616692 ETH가 남게 됩니다.

## 권장 사항

계약에 남은 ETH를 인출하는 `onlyOwner` 함수를 포함하십시오. 이는 `GroupcoinFactory`의 `owner`가 신뢰할 수 있는 행위자라고 가정합니다.

# [M-01] Uniswap v3 출시 후 예상되는 1,000만 토큰의 `totalSupply`에 도달하지 않음 (Expected `totalSupply` of 10M tokens not reached after launching to Uniswap v3)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

Uniswap v3에 출시한 후 Groupcoin의 예상 `totalSupply`는 10,000,000입니다. 그러나 테스트를 통해 실제로 10,000,000에 도달하지 못한다는 것을 확인할 수 있습니다. 아래 테스트 결과에서 최대 토큰 양을 구매하더라도 여전히 1,000만 미만의 `totalSupply`를 갖게 됨을 알 수 있습니다:

```solidity
function testLaunchToUniswapV3() public {
    bytes32 message = keccak256(abi.encodePacked(address(this), "Telegram WIF Coin", "WIF"));
    (uint8 v, bytes32 r, bytes32 s) = vm.sign(0xbeef, message.toEthSignedMessageHash());
    bytes memory signature = abi.encodePacked(r, s, v);

    (bytes32 salt,) = factory.generateSalt(address(this), "Telegram WIF Coin", "WIF");
    uint256 newToken = factory.launchGroupCoin("Telegram WIF Coin", "WIF", signature, salt, 0);
    uint256 price = factory.getBuyPriceAfterFee(newToken, 8_357_142);

    vm.deal(User01, price);
    vm.startPrank(User01);

    factory.buy{ value: price }(newToken, 8_357_111);

    factory.enableUniswapV3Launch(newToken);

    vm.roll(block.number + 1);

    factory.launchGroupCoinToUniswapV3(newToken);

    Groupcoin coin = factory.addressOf(newToken);
    assertEq(coin.totalSupply(), 10_000_000 ether);
}
```

또한 그 전에 `config.thresholdPrice`에 도달하여 상관없이 Uniswap v3를 출시할 수 있으므로 최대 금액보다 2000개 이상의 적은 토큰을 구매할 수 있습니다:

```solidity
function testLaunchToUniswapV3() public {
    bytes32 message = keccak256(abi.encodePacked(address(this), "Telegram WIF Coin", "WIF"));
    (uint8 v, bytes32 r, bytes32 s) = vm.sign(0xbeef, message.toEthSignedMessageHash());
    bytes memory signature = abi.encodePacked(r, s, v);

    (bytes32 salt,) = factory.generateSalt(address(this), "Telegram WIF Coin", "WIF");
    uint256 newToken = factory.launchGroupCoin("Telegram WIF Coin", "WIF", signature, salt, 0);
    uint256 price = factory.getBuyPriceAfterFee(newToken, 8_357_142);

    vm.deal(User01, price);
    vm.startPrank(User01);

    // @audit we buy less tokens and yet we can still launch
    factory.buy{ value: price }(newToken, 8_355_000);

    factory.enableUniswapV3Launch(newToken);

    vm.roll(block.number + 1);

    factory.launchGroupCoinToUniswapV3(newToken);

    Groupcoin coin = factory.addressOf(newToken);
    assertEq(coin.totalSupply(), 10_000_000 ether);
}
```

위의 테스트에서 우리는 약 9,997,888 Groupcoins의 `totalSupply`로 끝납니다.

## 권장 사항

`launchGroupCoinToUniswapV3`에서 하드코딩된 양의 Groupcoin을 발행하는 대신 `10_000_000 ether - token.totalSupply()`를 발행해야 합니다. 예:

```solidity
token.mint(address(this), 10_000_000 ether - token.totalSupply());
```

# [M-02] 사용자는 동일한 이름과 티커로 여러 Groupcoin을 출시할 수 있음 (Users can launch multiple Groupcoins under the same name and ticker)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`launchGroupCoin`에서 `msg.sender`, `name` 및 `ticker`의 해시를 가져와 서명된 메시지 해시로 변환한 다음 제공된 `signature`와 비교하여 복구된 주소가 `signer`와 일치하지 않으면 되돌립니다. 이는 사용자가 `signer`가 주어진 `name`과 `ticker`로 출시하도록 허용한 경우에만 Groupcoin을 출시할 수 있도록 하기 위한 것입니다. 그러나 서명 로직에는 논스(nonce)가 포함되어 있지 않으므로 사용자는 다른 유효한 `salt`를 제공하여 허용된 `name` 및 `ticker`로 원하는 만큼 많은 Groupcoin을 출시할 수 있습니다.

## 권장 사항

서명된 메시지 해시에 `nonce`를 포함시켜 서명을 사용한 후 증가하는 상태 변수를 참조하도록 하십시오. 또한 체인 분할 시 Groupcoin이 의도한 체인에서만 출시될 수 있도록 메시지 해시에 `chainid`도 포함해야 합니다.

# [M-03] 코인 구매 시 샌드위치 공격 가능성 (Possible sandwich attack when buying a coin)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

GroupcoinFactory.sol의 `buy()`를 통해 코인을 구매할 때 구매자는 특정 수의 코인을 구매하기 위해 함수에 약간의 `msg.value`를 첨부합니다. `buy()`에는 슬리피지 보호가 없으므로 악의적인 사용자가 구매할 수 있습니다.

예를 들어,

- 앨리스는 1000 Acoin을 사고 싶어합니다. 그녀는 첫 번째 구매자이며 구매하는 데 약 3434501 wei의 ETH가 필요하다고 계산합니다(`getPrice()` 함수에서 계산된 금액). 앨리스는 프로토콜이 보낸 초과 ETH를 환불해 줄 것이라는 것을 알고 있으므로 1e18 상당의 ETH를 전달합니다.
- 밥은 그 트랜잭션을 보고 약 3429360425241769 wei에 1,000,000 Acoin을 구매하여 그녀를 프론트런합니다. 이제 앨리스의 트랜잭션이 진행되고 Acoin의 `totalSupply()`는 0 대신 100만 개가 됩니다.
- 앨리스의 1000 Acoin은 이제 10298367632031 wei의 ETH이며, 그녀가 1e18 상당의 ETH를 첨부했기 때문에 전송이 진행됩니다.

## 권장 사항

구매자를 보호하기 위해 구매자가 최대 가격에 도달할 때까지 x 금액의 토큰을 지불할 의사가 있는지 확인하는 또 다른 매개변수 `maxPrice`를 추가하십시오. 앨리스의 경우 최대 가격은 4000000 wei이므로 코인이 그 이상의 가격이면 트랜잭션이 되돌려집니다.

샘플 예:

```
    function buy(uint256 tokenId, uint256 amount, uint256 maxPrice) public payable {
        ...
        if (msg.value < price + protocolFeeTaken) {
            revert NotEnough();
        }
        if(maxPrice != 0){
        require(maxPrice > price + protocolFeeTaken, "Price too expensive")
        }
```

maxPrice가 설정되지 않은 경우 구매자가 슬리피지 보호가 없음을 수락한다고 가정합니다.

# [M-04] ETH 전송에 "transfer" opcode의 다양한 사용 (Various use of "transfer" opcode to send ETH)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

GroupcoinFactory.sol에서 토큰을 사고팔 때 `.transfer` opcode를 사용하여 ETH 전송을 처리하며, 이는 2300 가스의 고정 금액을 전달하여 수행합니다. 이는 두 가지 이유로 위험합니다:

1. EVM 명령어의 가스 비용은 하드 포크 중에 크게 변경될 수 있으며, 이전에는 고정 가스 비용을 가정했을 수 있습니다. 예를 들어 EIP 1884는 SLOAD 명령어의 비용 증가로 인해 여러 기존 스마트 계약을 중단시켰습니다.
2. 수신자가 계약이거나 `receive`/`fallback` 함수에 2300 가스 이상이 필요한 멀티시그 세이프(예: `receive`/`fallback` 함수에서 추가 로직을 실행하는 세이프)인 경우 가스 부족 오류로 인해 전송 함수가 항상 실패합니다.

`buy` 함수에서 `feeTo` 주소 또는 `msg.sender`로 ETH를 보내는 것은 위의 조건 중 하나라도 해당되면 실패하며, 따라서 사용자는 토큰을 구매할 수 없거나 환불을 받을 수 없습니다.

```solidity
        payable(feeTo).transfer(protocolFeeTaken);

        uint256 owed = price + protocolFeeTaken;
        if (msg.value > owed) {
            payable(msg.sender).transfer(msg.value - owed);
        }
```

`sell` 함수에서 `feeTo` 주소 또는 `msg.sender`로 ETH를 보내는 것은 위의 조건 중 하나라도 해당되면 실패하며, 따라서 사용자는 토큰을 판매할 수 없습니다.

```solidity
        payable(feeTo).transfer(protocolFeeTaken);
        payable(msg.sender).transfer(price);
```

## 권장 사항

대신 ".call" opcode를 사용하고 `buy` 및 `sell` 함수에 non reentrant 수정자를 추가하십시오.

# [M-05] 수수료 수집가 주소를 설정할 수 없음 (Fee collector address cannot be set)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

수수료를 청구하기 위해 생성자나 수집가는 `claimFees` 함수를 호출할 수 있습니다.

```solidity
    function claimFees(uint256 id) external {
        address creator = creatorOf[id];
        if (!canCollectFees[msg.sender] && msg.sender != creator) {
            revert NoAuthorization();
        }
```

수수료 수집가를 설정하려면 확인에서 볼 수 있듯이 GroupcoinFactory에서 호출해야 합니다. 그러나 GroupcoinFactory 계약에는 이 함수가 누락되어 있습니다. 결과적으로 수수료 수집가를 설정할 수 없어 수수료를 청구할 수 없습니다.

```solidity
    function delegateFeeCollection(address collector, bool isDelegated) public {
        if (msg.sender != factory) {
            revert NoAuthorization();
        }

        canCollectFees[collector] = isDelegated;
    }
```

## 권장 사항

생성자가 수수료 수집가를 설정할 수 있도록 GroupcoinFactory.sol에 함수를 도입하십시오.

# [M-06] 풀을 먼저 생성하여 UniswapV3 출시를 방해함 (Griefing the launch to UniswapV3 by creating the pool first)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

x 양의 코인을 구매하고 코인 가치가 특정 임계값에 도달하면 `launchGroupCoinToUniswapV3`를 호출할 수 있습니다. 이렇게 하면 코인 거래가 다른 본딩 커브로 전환됩니다.

`launchGroupCoinToUniswapV3()`에서 함수는 풀의 존재를 확인하지 않고 `uniswapV3Factory.createPool`을 호출합니다.

```
        address pool = uniswapV3Factory.createPool(address(token), WETH, config.fee);
        IUniswapV3Pool(pool).initialize(config.sqrtPriceX96);
```

UniswapV3Factory.createPool에서는 풀을 한 번만 생성할 수 있습니다:

```
 function createPool(
        address tokenA,
        address tokenB,
        uint24 fee
    ) external override noDelegateCall returns (address pool) {
        require(tokenA != tokenB);
        (address token0, address token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);
        require(token0 != address(0));
        int24 tickSpacing = feeAmountTickSpacing[fee];
        require(tickSpacing != 0);
 >      require(getPool[token0][token1][fee] == address(0));
        pool = deploy(address(this), token0, token1, fee, tickSpacing);
 >      getPool[token0][token1][fee] = pool;
        // populate mapping in the reverse direction, deliberate choice to avoid the cost of comparing addresses
        getPool[token1][token0][fee] = pool;
        emit PoolCreated(token0, token1, fee, tickSpacing, pool);
    }
```

악의적인 사용자는 일부 토큰을 구매하고 tokenA를 코인으로, tokenB를 WETH로, 수수료를 프로토콜의 구성에 따라 사용하여 UniswapV3Factory에서 직접 `createPool`을 호출할 수 있습니다.

그런 다음 `launchGroupCoinToUniswapV3()`가 호출되면 풀이 이미 생성되었으므로 함수가 되돌려집니다.

## 권장 사항

풀이 이미 생성되었는지 확인하고 그렇다면 단순히 initialize를 호출하는 다른 프로토콜의 함수가 있습니다.

잠재적인 해결책은 다음과 같습니다:

```
 function _initUniV3PoolIfNecessary(PoolAddress.PoolKey memory poolKey, uint160 sqrtPriceX96) internal returns (address pool) {
        pool = IUniswapV3Factory(UNIV3_FACTORY).getPool(poolKey.token0, poolKey.token1, poolKey.fee);
        if (pool == address(0)) {
            pool = IUniswapV3Factory(UNIV3_FACTORY).createPool(poolKey.token0, poolKey.token1, poolKey.fee);
            IUniswapV3Pool(pool).initialize(sqrtPriceX96);
        } else {
            (uint160 sqrtPriceX96Existing, , , , , , ) = IUniswapV3Pool(pool).slot0();
            if (sqrtPriceX96Existing == 0) {
                IUniswapV3Pool(pool).initialize(sqrtPriceX96);
            } else {
                require(sqrtPriceX96Existing == sqrtPriceX96, "UV3P");
            }
        }
    }
```

# [L-01] `tokenIdOf`는 초기 `tokenId`에 대해 신뢰할 수 없음 (`tokenIdOf` will be unreliable for the initial `tokenId`)

`launchGroupCoin`은 `tokenId`를 `maxGroupcoin`의 현재 값으로 설정하므로 `GroupcoinFactory`의 초기 `tokenId`는 0입니다. 초기 `tokenId`에 대한 `tokenIdOf` 매핑 값을 설정할 때 `tokenIdOf[initialCoin] = 0`이 설정됩니다. 설정되지 않은 매핑 키의 기본값이 0이기 때문에 이는 많은 혼란을 야기할 수 있으며, 단순히 `tokenIdOf`를 참조하는 것만으로는 참조 코인이 실제로 존재하는지 확인하는 것이 불가능합니다.

`maxGroupcoin`을 1로 초기화하여 이를 해결할 수 있습니다. 또한 0에서 0이 아닌 값으로 `maxGroupcoin`을 증가시키는 것이 0이 아닌 값에서 0이 아닌 값으로 증가시키는 것보다 훨씬 비용이 많이 들기 때문에 첫 번째 코인 출시 시 가스를 절약할 수 있습니다.

# [L-02] 제공된 틱이 모든 풀 수수료와 작동하지 않음 (Provided ticks will not work with all pool fees)

Uniswap v3 풀은 사용된 수수료에 따라 다른 틱 간격 세트를 갖습니다. `GroupcoinFactory`에서 사용되는 초기 값인 `10000` 수수료의 경우 틱 간격이 200이므로 사용된 제공된 틱은 200의 배수여야 합니다. `launchGroupCoinToUniswapV3`에서 유동성 포지션을 `mint`할 때 우리는 `[-887_200, 887_200]`의 하드코딩된 틱 범위를 제공합니다. 이 틱들은 200의 배수이므로 사용할 수 있습니다. 그러나 `3000`의 수수료를 사용하면 틱 간격은 60이 되며, 이는 우리의 틱으로 균등하게 나눌 수 없습니다. 결과적으로 `config.fee = 3000`인 경우 `launchGroupCoinToUniswapV3`는 항상 되돌려집니다.

이 문제를 해결하기 위해 대략 동일한 범위를 제공하면서 모든 수수료의 틱 간격과 작동해야 하는 `[-887_400, 887_400]`의 틱 범위를 대신 사용할 수 있습니다.

# [L-03] 먼지를 판매하여 출시 활성화를 방해할 수 있음 (Enabling launch can be dossed by selling dust)

토큰이 출시를 위해 활성화되기 전에 악의적인 사용자는 소량의 토큰을 판매하여 가격을 지속적으로 임계값 아래로 낮출 수 있습니다. 극단적인 경우 `enableUniswapV3Launch` 함수를 프론트런하여 가격을 임계값 아래로 유지하기에 충분한 소량의 토큰을 판매하고(판매/수수료에서 상당한 양을 잃지 않으면서) 토큰 출시를 방해할 수 있습니다(다른 사용자가 소량을 구매하는 것은 다소 불편할 것입니다).

```solidity
    function enableUniswapV3Launch(uint256 tokenId) public {
        uint256 unitPrice = getBuyPriceAfterFee(tokenId, 1);

        if (unitPrice < config.thresholdPrice) {
            revert NotAtThreshold();
        }

        commitLaunch[tokenId] = block.number;

        emit UniswapLaunchStaged(tokenId, block.number);
    }
```

판매할 수 있는 최소 토큰 양을 도입하는 것이 좋습니다.

# [L-04] 중앙 집중화로 인한 위험 (Risks due to centralization)

관리자 보호 함수는 관리자가 침해당할 경우 사용자에게 부정적인 영향을 미칠 가능성이 있습니다.

1. GroupcoinFactory.sol에서 `setProtocolFee`는 구매 및 판매 시 사용자에게 부정적인 영향을 주지 않도록 상한선이 있어야 합니다. 매우 높게 설정하면 잠재적으로 토큰 판매가 불가능해져 잠재적인 허니팟이 생성될 수 있습니다.

```solidity
    function setProtocolFee(uint256 newProtocolFee) public onlyOwner {
        protocolFee = newProtocolFee;

        emit ProtocolFeeChanged(newProtocolFee);
    }
```

2. LiquidityLocker.sol에서 생성자가 청구할 때 풀 수수료를 잃지 않도록 동일한 상한선을 구현해야 합니다.

```solidity
    function setProtocolFee(uint256 newProtocolFee) public onlyOwner {
        protocolFee = newProtocolFee;
    }
```

# [L-05] 서명이 만료되지 않고 취소할 수 없음 (Signatures do not expire and cannot be canceled)

제공된 서명에는 마감일 매개변수나 논스가 없으므로 서명은 무기한 유지될 수 있으며 적어도 `signer` 주소가 변경될 때까지 취소할 수 없습니다. 소유자가 특정 사용자가 더 이상 서명을 받을 자격이 없다고 결정하면(사용자가 악의적이거나 하는 등), 해당 사용자의 배포를 막는 유일한 방법은 새로운 `signer`를 설정하는 것뿐이며, 이는 다른 정직한 사용자에게 불편을 끼치고 이전 서명자가 복원되면 서명이 유효해집니다.

```solidity
    function launchGroupCoin(
        string calldata name,
        string calldata ticker,
        bytes calldata signature,
        bytes32 salt,
        uint256 amount
    )
        public
        payable
        returns (uint256 tokenId)
    {
        bytes32 hash = keccak256(abi.encodePacked(msg.sender, name, ticker));
        bytes32 signedHash = hash.toEthSignedMessageHash();
        address approved = ECDSA.recover(signedHash, signature);
        if (approved != signer) {
            revert NotApproved();
        }
```

마감일 매개변수를 도입하고 만료 시간을 확인하는 것이 좋습니다. 논스 매개변수와 논스를 사용하는 소유자 보호 함수도 서명을 취소하는 데 도움이 됩니다.

# [L-06] `protocolFee` 수정으로 인해 Uniswap v3 출시에 영향을 미칠 수 있음 (Uniswap v3 launches can be affected by modifying the `protocolFee`)

`enableUniswapV3Launch`에서 프로토콜 수수료를 포함한 구매 가격이 `config.thresholdPrice`에 도달했는지 여부에 따라 주어진 토큰이 Uniswap v3에 출시될 준비가 되었는지 확인합니다:

```solidity
uint256 unitPrice = getBuyPriceAfterFee(tokenId, 1);

if (unitPrice < config.thresholdPrice) {
    revert NotAtThreshold();
}
```

```solidity
function getBuyPriceAfterFee(uint256 tokenId, uint256 amount) public view returns (uint256 price) {
    price = getBuyPrice(tokenId, amount);
    price += price * protocolFee / 1 ether;
}
```

이 로직에 `protocolFee`를 포함하고 있고 `owner`는 언제든지 `protocolFee`를 변경할 수 있으므로 `protocolFee`를 수정하면 예상치 못한 효과가 발생할 수 있습니다. `protocolFee`가 증가하는 경우 반환된 `unitPrice`가 원래보다 일찍 `config.thresholdPrice`를 초과하여 유동성 포지션에 대해 충분한 ETH가 모금되기 전에 Uniswap v3 출시가 확정되어 시스템이 지급 불능 상태가 될 수 있습니다.

`protocolFee`를 포함하지 않는 단위 구매 가격을 참조하고 비교하는 `thresholdPrice`를 사용하십시오.
