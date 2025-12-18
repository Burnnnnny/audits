# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**timeless-fi/bunni-v2** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# Uniswap V4 Periphery 소개

Uniswap v4는 Uniswap v3의 자본 효율성 개선 사항을 유지하면서 후크(hooks)를 통한 유연성을 도입하고 전체 프로세스에서 가스 사용량을 최적화합니다. 주변 계약의 SVG 계약은 NFTSVG라는 라이브러리를 정의하며, 이는 Uniswap NFT에 사용되는 SVG 이미지를 생성하는 기능을 제공합니다. 토큰 ID, 가격 범위, 토큰 심볼과 같은 다양한 매개변수를 기반으로 곡선, 색상, 위치와 같은 사용자 지정 가능한 그래픽 요소를 결합합니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ------------ | -------------- | ----------- |
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

_검토 커밋 해시_ - [7faae4718eecda1b33dc3abd894431ed2d16c929](https://github.com/timeless-fi/bunni-v2/tree/7faae4718eecda1b33dc3abd894431ed2d16c929)

_수정 검토 커밋 해시_ - [1a21920085fc712ca745361bf397e8a7be25dc1c](https://github.com/timeless-fi/bunni-v2/tree/1a21920085fc712ca745361bf397e8a7be25dc1c)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `PositionDescriptor`
- `PositionManager`
- `ERC721Permit_v4`
- `SafeCurrencyMetadata`
- `AddressStringUtils`
- `HexStrings`
- `Descriptor`
- `SVG`
- `SafeCurrencyMetadata`

# 발견 사항

# [M-01] Hook 주소가 SVG에 올바르게 표시되지 않음 (Hook address is not correctly represented in the SVG)

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`SVG` 라이브러리의 `generateSVGPositionDataAndLocationCurve`는 포지션 데이터에 대한 SVG를 생성합니다. 이 데이터에는 후크(hook) 계약의 주소가 포함되는데, 전체가 표시되는 것이 아니라 중간에 줄임표(ellipsis)가 있는 처음과 마지막 글자만 표시됩니다.

후크 주소를 처리하기 위해 먼저 `toHexString` 함수를 호출하여 주소를 문자열로 변환하므로 `hookStr` 변수의 길이는 42자이고 바이트 인덱스 범위는 0에서 41까지입니다.

그런 다음 `hookStr` 변수의 처음 5글자, 줄임표, 마지막 3글자를 연결하여 슬라이스된 주소를 생성합니다. 그러나 `substring` 함수에 전달된 값이 마지막 문자에 대해 잘못되었습니다. `endIndex`는 40이 아니라 42여야 합니다.

```solidity
    function generateSVGPositionDataAndLocationCurve(
        string memory tokenId,
        address hook,
        int24 tickLower,
        int24 tickUpper
    ) private pure returns (string memory svg) {
@>      string memory hookStr = (uint256(uint160(hook))).toHexString(20);
        string memory tickLowerStr = tickToString(tickLower);
        string memory tickUpperStr = tickToString(tickUpper);
        uint256 str1length = bytes(tokenId).length + 4;
@>      string memory hookSlice = string(abi.encodePacked(substring(hookStr, 0, 5), "...", substring(hookStr, 37, 40)));
```

즉, 후크 주소가 `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48`인 경우 SVG에는 다음 텍스트가 표시됩니다:

```
Hook: 0xa0b...6eb
```

이로 인해 사용자가 후크 계약을 식별할 수 없게 됩니다.

## 권장 사항

```diff
-    string memory hookSlice = string(abi.encodePacked(substring(hookStr, 0, 5), "...", substring(hookStr, 37, 40)));
+    string memory hookSlice = string(abi.encodePacked(substring(hookStr, 0, 5), "...", substring(hookStr, 39, 42)));
```

# [L-01] Hook 주소가 설정되지 않을 수 있음 (Hook address could be unset)

`Descriptor.sol` 및 `SVG.sol`에서는 모든 포지션에 연관된 후크 계약이 있을 것으로 예상하는 것으로 보입니다. 그러나 [이 페이지](https://docs.uniswap.org/contracts/v4/concepts/hooks)에 명시된 바와 같이 후크는 유동성 풀에 대해 선택 사항이므로 포지션에 연관된 후크가 없을 수 있습니다.

예를 들어 `SVG.sol`에서 생성되는 SVG는 후크 계약 없이 유동성 풀에 포지션을 가진 사용자에게 혼란을 줄 수 있습니다. 현재 생성된 SVG에는 `Hook: 0x0000...0000`과 같은 섹션이 있습니다. 지식이 부족한 사용자에게는 SVG가 잘못 생성되었거나 후크가 실제로 표시된 주소에 있는 것처럼 보일 수 있습니다.

포지션에 연관된 후크가 없을 때를 위한 조건부 로직이 있다면 더 좋을 것입니다. 가능한 방법은 다음과 같습니다:

- 관련 유동성 풀이 후크 계약을 사용하지 않는 경우 후크 관련 섹션 없이 SVG(또는 설명자) 생성
- `0x0000...00000`을 표시하는 대신 `No hook`과 같은 문자열 표시

물론 특정하고 예상되는 형식을 준수해야 하는 URI와 같은 일부 구성 요소의 경우 0 주소를 고수하는 것이 더 쉽거나 나을 수 있습니다. 그러나 사용자에게 보이는 콘텐츠의 경우 더 투명하고 명시적인 것이 좋습니다.

# [L-02] permit 해시에 대한 호환성 깨짐 변경 (Breaking change to permit hashes)

v4가 아직 메인넷에 배포되지는 않았지만 테스트넷에 배포되었으므로 `PositionManager.sol`의 이름 문자열을 `V4`에서 `v4`로 변경하는 작은 변경 사항으로 인해 서명되었지만 아직 사용되지 않은 모든 기존 허가(permit)가 깨질 것이라는 점을 언급할 가치가 있습니다. 이는 v4로 테스트하는 모든 통합자(integrator)에게 영향을 줄 수 있습니다.

이 변경 사항이 이해되고 다운스트림 테스트넷 통합자에게 전달된다면 이 변경 사항을 유지할 수 있습니다.

# [L-03] 네이티브 토큰에 0 주소를 사용하면 최종 사용자에게 혼란을 줄 수 있음 (Using zero address for native token can be confusing for end users)

내부적으로 Uniswap v4는 체인의 네이티브 토큰을 나타내기 위해 0 주소를 사용합니다. 즉, 네이티브 토큰을 사용하는 풀의 NFT에 대한 `tokenURI`는 메타데이터의 "description" 필드에 다음 텍스트를 포함합니다:

```
ETH Address: 0x0000000000000000000000000000000000000000
```

마찬가지로 NFT의 SVG 이미지에는 이미지 테두리에 다음 텍스트가 포함됩니다:

```
0x0000000000000000000000000000000000000000 • ETH
```

최종 사용자가 0 주소를 네이티브 토큰의 실제 주소로 해석할 수 있으므로 혼란을 줄 수 있습니다.

통화가 네이티브 토큰인 특수한 경우를 관리하여 해당 경우 주소를 표시하지 않거나 "Native"라는 단어와 같이 다른 표현을 사용하는 것을 고려하십시오.

# [L-04] 이스케이프되지 않은 특수 문자가 유효하지 않은 JSON을 생성할 수 있음 (Special characters not escaped can produce invalid JSON)

`Descriptor.constructTokenURI`는 `PositionDescriptor.tokenURI` 함수가 반환할 Base64 인코딩된 JSON을 생성합니다. 이 함수는 입력 심볼을 완전히 삭제(sanitize)하지 않으며, 유효하지 않은 JSON을 생성할 수 있는 특수 문자가 포함될 수 있습니다.

다음 문자는 큰따옴표 문자에 대해 수행되는 것과 동일한 방식으로 이스케이프되어야 합니다:

- `\u000c` (폼 피드)
- `\n` (개행)
- `\r` (캐리지 리턴)
- `\t` (탭)

# [L-05] 긴 심볼 문자열이 `tokenURI`를 되돌리거나 SVG 이미지에 아티팩트를 유발할 수 있음 (Long symbol strings can cause `tokenURI` to revert or artifacts in the SVG image)

`SafeCurrencyMetadata` 라이브러리의 `currencySymbol` 함수는 토큰 계약에서 토큰 심볼을 추출하는 데 사용됩니다. 계약에서 반환된 값이 너무 길면 다음과 같은 문제가 발생할 수 있습니다:

1. 심볼의 길이가 255자를 초과하면 `symbolBytes.length`가 `uint8`에 맞지 않아 오버플로우 오류로 인해 `Descriptor.escapeQuotes` 함수가 되돌려집니다:

```solidity
    for (uint8 i = 0; i < symbolBytes.length; i++) {
        if (symbolBytes[i] == '"') {
            quotesCount++;
        }
    }
```

2. 길이는 255보다 작지만 여전히 긴 경우, SVG 이미지 테두리에 배치된 토큰 데이터가 있는 텍스트가 겹쳐서 출력을 읽을 수 없게 됩니다 ([예시 참조](https://raw.githubusercontent.com/gist/shaka0x/3261ae647d8ad1a004e6512d72a04dc5/raw/b7f9e9294d2c1a4710d7db59eab89081182e4b83/nft.svg)).

이러한 문제를 방지하기 위해 `currencySymbol` 함수의 출력을 적절한 길이로 자르는 것을 고려하십시오.

# [L-06] `currencyDecimals`가 반환된 값이 uint8인지 확인하지 않음 (`currencyDecimals` does not check if the value returned is uint8)

`SafeCurrencyMetadata`에서 `currencyDecimals` 함수의 NatSpec은 다음과 같이 명시합니다:

```solidity
/// @notice attempts to extract the token decimals, returns 0 if not implemented or not a uint8
```

그러나 함수는 토큰 계약에서 반환된 값이 uint8인지 확인하지 않으므로, 값이 255보다 큰 경우 디코딩 단계에서 함수가 되돌려집니다:

```solidity
    if (data.length == 32) {
        return abi.decode(data, (uint8));
    }
```

32바이트에서 77자리 이상의 소수점 자리를 나타내는 것은 불가능하므로 255보다 큰 소수점 값은 예상되지 않지만, 함수는 되돌려지는 것을 피하고 대신 대체 값인 0을 반환해야 합니다.

코드에 다음 변경 사항을 추가하는 것을 고려하십시오:

```diff
    if (data.length == 32) {
-       return abi.decode(data, (uint8));
+       uint256 decimals = abi.decode(data, (uint256));
+       if (decimals <= type(uint8).max) {
+           return uint8(decimals);
+       }
    }
    return (false, 0);
```
