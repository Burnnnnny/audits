# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**sushi-labs/tron-contracts** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# SushiSwap에 대하여

Tron의 SushiSwap은 구매자와 판매자를 연결하기 위한 전통적인 오더북 없이 사용자가 자산을 직접 거래할 수 있는 자동화된 시장 조성자(AMM)이자 탈중앙화 거래소(DEX)입니다.

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

_검토 커밋 해시_ - [f4863c95490b87229579bdc5354b0c558eb13fbc](https://github.com/sushi-labs/tron-contracts/tree/f4863c95490b87229579bdc5354b0c558eb13fbc)

_수정 검토 커밋 해시_ - [86530e9592c1605e5c51e4c18291d1d68473a197](https://github.com/sushi-labs/tron-contracts/tree/86530e9592c1605e5c51e4c18291d1d68473a197)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `TransferHelper`
- `Math`
- `SushiSwapV2Library`
- `Ownable`
- `SushiSwapV2ERC20`
- `SushiSwapV2Factory`
- `SushiSwapV2Migrator`
- `SushiSwapV2Pair`
- `SushiSwapV2Router02`

# 발견 사항

# [M-01] permit EIP712 도메인 구분자(separator)에 잘못된 `chainId` 사용

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`SushiSwapV2ERC20`은 `permit()`에 사용되는 EIP-712 `DOMAIN_SEPARATOR`를 구현합니다.

```Solidity
    constructor() public {
        uint chainId;
        assembly {
        //@audit this should not be hardcoded
>>>         chainId := 1
        }
        DOMAIN_SEPARATOR = keccak256(
            abi.encode(
                keccak256(
                    "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
                ),
                keccak256(bytes(name)),
                keccak256(bytes("1")),
                chainId,
                address(this)
            )
        );
    }
```

그러나 `chainId := 1`로 잘못 설정했는데, 이는 TRON 네트워크가 아닌 이더리움 메인넷을 위한 것입니다.

[EIP-712](https://eips.ethereum.org/EIPS/eip-712)에 따르면 chainId가 활성 체인과 일치하지 않으면 사용자 에이전트(브라우저/지갑)가 서명을 수행해서는 안 됩니다. 이로 인해 특정 사용자 에이전트가 가이드라인을 따르고 서명을 거부하므로 `permit()`이 작동하지 않게 됩니다.

> uint256 chainId [EIP-155](https://eips.ethereum.org/EIPS/eip-155) 체인 ID입니다. 사용자 에이전트는 현재 활성 체인과 일치하지 않는 경우 서명을 거부해야 합니다.

## 권고 사항

```diff
    constructor() public {
        uint chainId;
        assembly {
-            chainId := 1
+            chainId := chainid
        }
```

# [M-02] 합법적인 트랜잭션을 차단하기 위한 permit 기능 남용

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`SushiSwapV2Router02` 계약의 `removeLiquidityWithPermit` 함수를 사용하면 사용자가 `permit` 기능을 활용하여 유동성 토큰 전송을 승인함으로써 유동성 풀에서 유동성을 제거할 수 있습니다.

그러나 이 메커니즘은 잠재적인 프론트 러닝 취약점을 도입합니다. 공격자는 멤풀(mempool)에서 `permit` 호출이 포함된 트랜잭션을 관찰하고, 합법적인 트랜잭션이 채굴되기 전에 동일한 `permit` 매개변수로 다른 함수 호출을 포함하는 자신의 트랜잭션을 발행할 수 있습니다. 이로 인해 변경된 상태로 인해 합법적인 트랜잭션이 실패할 수 있습니다. 마찬가지로 서명을 생성한 사용자는 사용되지 않은 상태로 `SushiSwapV2Router02`에 토큰을 승인하게 되어, 서명자가 트랜잭션을 수동으로 실행해야 합니다.

```solidity
    function removeLiquidityWithPermit(
        address tokenA,
        address tokenB,
        uint liquidity,
        uint amountAMin,
        uint amountBMin,
        address to,
        uint deadline,
        bool approveMax,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external virtual override returns (uint amountA, uint amountB) {
        address pair = SushiSwapV2Library.pairFor(factory, tokenA, tokenB);
        uint value = approveMax ? uint(-1) : liquidity;
>>>     ISushiSwapV2ERC20(pair).permit(
            msg.sender,
            address(this),
            value,
            deadline,
            v,
            r,
            s
        );
>>>     (amountA, amountB) = removeLiquidity(
            tokenA,
            tokenB,
            liquidity,
            amountAMin,
            amountBMin,
            to,
            deadline
        );
    }
```

공격자가 `permit`을 실행하면 사용자의 nonce가 증가하여 합법적인 `permit` 호출이 "INVALID_SIGNATURE" 되돌림(revert) 오류와 함께 실패하게 됩니다. 다음 시나리오를 고려해 보십시오:

1. `UserA`가 특정 매개변수로 `SushiSwapV2Router02::removeLiquidityWithPermit`을 호출하려고 합니다.
2. 공격자는 `UserA`의 보류 중인 트랜잭션을 관찰하고 `UserA` 트랜잭션과 동일한 매개변수로 `permit` 함수를 호출하는 트랜잭션을 보냅니다.
3. permit에 대한 `nonce`가 이제 사용됩니다.
4. `UserA` 트랜잭션이 실행될 때 유효하지 않은 nonce로 인해 되돌려집니다.

또한 동일한 문제가 `SushiSwapV2Router02::removeLiquidityETHWithPermit` 및 `SushiSwapV2Router02::removeLiquidityETHWithPermitSupportingFeeOnTransferTokens`에서도 발생합니다.

## 권고 사항

[OpenZeppelin](https://docs.openzeppelin.com/contracts/4.x/api/token/erc20#security_considerations)에서 권장하는 대로 `permit` 실행 실패를 처리하는 것이 좋습니다.

```solidity
function doThingWithPermit(..., uint256 value, uint256 deadline, uint8 v, bytes32 r, bytes32 s) public {
    try token.permit(msg.sender, address(this), value, deadline, v, r, s) {} catch {}
    doThing(..., value);
}
```

# [L-01] `SushiSwapV2Pair`의 미사용 `SELECTOR` 상수

`SushiSwapV2Pair`에는 안전한 전송을 위해 `TransferHelper`에 의존하고 있으므로 더 이상 사용되지 않는 `SELECTOR` 상수가 있습니다. `SELECTOR`를 제거하여 이 문제를 해결할 수 있습니다.

이렇게 하면 바이트코드가 변경되므로 `SushiSwapV2Library.pairFor()`에서 생성 코드 해시를 업데이트해야 합니다.

```diff
contract SushiSwapV2Pair is ISushiSwapV2Pair, SushiSwapV2ERC20 {
    using SafeMath for uint;
    using UQ112x112 for uint224;

    uint public constant override MINIMUM_LIQUIDITY = 10 ** 3;

-    bytes4 private constant SELECTOR =
-        bytes4(keccak256(bytes("transfer(address,uint256)")));
```

# [L-02] 페어 토큰의 잘못된 `symbol` 이름

V2 Pair의 ERC20 토큰에 대한 심볼 이름이 잘못되었습니다. 심볼 이름이 프론트엔드나 탐색기에 표시될 때 사용자가 혼란스러워할 수 있습니다.

```diff
contract SushiSwapV2ERC20 is ISushiSwapV2ERC20 {
    using SafeMath for uint;

    string public constant override name = "SushiSwap V2";
-    string public constant override symbol = "SUHSI-V2";
+    string public constant override symbol = "SUSHI-V2";
    uint8 public constant override decimals = 18;
    uint public override totalSupply;
```

# [L-03] Solidity 0.7.0 이상과의 호환성 문제

TransferHelper 라이브러리와 Sushiswap 계약은 Solidity 버전 범위를 0.6.12에서 0.8.0 미만으로 지정합니다.

```solidity
pragma solidity >=0.6.12 <0.8.0;
```

그러나 safeTransferETH 함수의 구현은 Solidity 버전 0.7.0 이상과 호환되지 않는 구문을 사용합니다. 구체적으로, 외부 호출에서 ETH를 보내는 방법이 Solidity 0.7.0에서 변경되었습니다. 현재 구현:

```solidity
    function safeTransferETH(address to, uint value) internal {
        (bool success, ) = to.call.value(value)(new bytes(0)); // @audit cannot compile with solc >= 0.7
        require(success, "TransferHelper: ETH_TRANSFER_FAILED");
    }
```

ETH 전송에 값을 지정하는 이 구문(call.value())은 새로운 중괄호 구문을 위해 Solidity 0.7.0에서 더 이상 사용되지 않습니다.

Solidity 문서에서:

        외부 함수 및 계약 생성 호출에서 이더와 가스는 이제 새로운 구문을 사용하여 지정됩니다: x.f{gas: 10000, value: 2 ether}(arg1, arg2). 이전 구문 – x.f.gas(10000).value(2 ether)(arg1, arg2) – 은 오류를 발생시킵니다.

https://docs.soliditylang.org/en/v0.7.2/070-breaking-changes.html#changes-to-the-syntax

safeTransferETH 함수를 Solidity 0.7.0 이상과 호환되는 새로운 구문을 사용하도록 업데이트하는 것이 좋습니다.

```diff
    function safeTransferETH(address to, uint256 value) internal {
-       (bool success, ) = to.call.value(value)(new bytes(0));
+       (bool success, ) = to.call{value: value}(new bytes(0));
        require(success, 'TransferHelper::safeTransferETH: ETH transfer failed');
    }
```
