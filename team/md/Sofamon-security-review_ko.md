# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 블록체인 프로토콜을 위해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락할 수 있습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**sofamon-pwa-contracts** 리포지토리에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Sofamon 소개

SofamonWearable은 본딩 커브(bonding curve) 메커니즘을 사용하여 웨어러블 컬렉션을 판매하는 Sofamon 스마트 계약입니다. 구매한 웨어러블마다 공식에 따라 가격이 상승합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간 - 프로토콜 자산의 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터일 뿐이지만 여전히 발생 가능성이 상대적으로 높습니다.

- 낮음 - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 또는 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정해야 함 (Should fix)

- 낮음 - 수정할 수 있음 (Could fix)

# 보안 평가 요약

_검토 커밋 해시_ - [4f05651a7a7eb10a3a2d4fc34234fb55f08ac51b](https://github.com/Sofamon/sofamon-pwa-contracts/tree/4f05651a7a7eb10a3a2d4fc34234fb55f08ac51b)

_수정 검토 커밋 해시_ - [01195301f92d054102c97e54c5c056f0db426fb1](https://github.com/Sofamon/sofamon-pwa-contracts/tree/01195301f92d054102c97e54c5c056f0db426fb1)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `IBlast`
- `SofamonWearables`

# 발견 사항

# [H-01] 가격 공식의 반올림 문제

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

현재 공식은 다음과 같습니다:

```solidity
        return (
            (totalSupply * curveFactor) / (totalSupply - x) - (totalSupply * curveFactor) / totalSupply
                - initialPriceFactor / 1000 * x
        ) * 1 ether;
```

몇 가지 문제가 있습니다:

1. 첫 번째 매개변수 `(totalSupply * curveFactor) / (totalSupply - x)`는 분자와 분모가 모두 1e18 숫자이기 때문에 내림(rounded down) 됩니다.
2. 두 번째 매개변수 `(totalSupply * curveFactor) / totalSupply`는 간단히 `curveFactor`로 줄일 수 있습니다.
3. 세 번째 매개변수 `initialPriceFactor / 1000 * x`는 `initialPriceFactor`가 [100, 1000] 범위에 있기 때문에 항상 0으로 내림됩니다. 또한 이 매개변수는 1e18 단위여야 하지만 이전 매개변수는 단순한 숫자입니다. 따라서 결국 수정 후 언더플로가 발생할 것입니다.

## 권장 사항

다음은 1번째 및 2번째 매개변수의 정밀도를 1e18로 높이고 3번째 매개변수의 연산 순서를 변경한 것입니다:

```solidity
        return (totalSupply * curveFactor * 1 ether) / (totalSupply - x) - (curveFactor * 1 ether) - initialPriceFactor * x / 1000;
```

# [M-01] 동일한 서명을 여러 번 사용

## 심각도

**영향:** 중간, createSigner는 주어진 판매에 대해 정확한 금액에 서명하지만 실제로는 결국 모든 금액이 될 수 있습니다.

**가능성:** 중간, 쉽게 사용 가능하며 비공개 판매(private sale)를 위해 서명을 한 번만 받으면 됩니다.

## 설명

비공개 판매는 주어진 `wearablesSubject+amount`에 대해 `createSigner`의 서명이 필요하며, 매수와 매도에 대한 서명이 다릅니다.
하지만 한 번 서명하면 서명을 여러 번 사용할 수 있습니다. 결과적으로 `createSigner`는 실제로 매수 및 매도 작업에 대한 금액을 제어할 권한이 없습니다. 심지어 하나의 서명만 사용하여 누구나 판매에 액세스할 수 있는 2차 시장을 마련하는 것도 가능하므로, 비공개 시장이 그다지 비공개적이지 않게 됩니다.

## 권장 사항

사용된 서명에 대해 별도의 매핑을 두거나 서명에 증가하는 논스(nonce) 로직을 적용하는 것을 고려하십시오.

# [M-02] 구매 시 초과 msg.value 손실

## 심각도

**영향:** 높음, 자금이 영원히 손실됨

**가능성:** 낮음, 사용자의 실수가 필요함

## 설명

`msg.value`는 이 방정식에서만 올바르지 않은 것으로 간주됩니다:

```
msg.value < price + protocolFee + creatorFee
```

결과적으로 가격+수수료를 초과하는 `msg.value`는 통과되지만, 반환되지 않고 손실됩니다.

사용자가 잘못된 `msg.value`를 제공하는 실수를 저지르는 경우 발생할 수 있습니다.

이 실수는 수수료 계산에서 비롯되거나 사용자보다 먼저 일부 매도 작업이 있어서 총가격이 예상보다 낮은 경우일 수 있습니다.

## 권장 사항

msg.value가 정확한 가격+수수료여야 한다는 엄격한 요구 사항을 갖거나(더 나음), 초과 자금을 사용자에게 반환하는 것(더 위험함)을 고려하십시오.

# [M-03] 잔액이 BASE_WEARABLE_UNIT 미만으로 떨어짐

## 심각도

**영향:** 중간, 일부 잔액은 판매 및 전송할 수 없으며, 작지만 curveAdjustmentFactor가 낮거나 공급량이 많은 경우 잠재적으로 충분히 가치가 있을 수 있습니다.

**가능성:** 중간, 0.001 ETH로 정확히 나누어떨어지지 않는 숫자를 보내기만 하면 됩니다.

## 설명

`transferWearables()`는 `BASE_WEARABLE_UNIT=0.001 ether` 이상의 금액을 전송해야 합니다.
그러나 사용자가 0.002 이더를 가지고 있을 때 0.0015 이더를 보내는 것을 허용합니다.
이 경우 0.0005 이더가 남게 되며, 이는 `BASE_WEARABLE_UNIT` 미만입니다.

웨어러블 판매도 마찬가지입니다.

```solidity
    function sellPrivateWearables(bytes32 wearablesSubject, uint256 amount, bytes calldata signature)
        external
        payable
    {
            // Check if amount is greater than base unit
            if (amount < BASE_WEARABLE_UNIT) revert InsufficientBaseUnit();
            ...
    }

    function transferWearables(bytes32 wearablesSubject, address from, address to, uint256 amount) external {
        ...

        // Check if amount is greater than base unit
        if (amount < BASE_WEARABLE_UNIT) revert InsufficientBaseUnit();
        ...
    }
```

이 새로운 금액은 `transferWearables()` 및 매도 작업의 최소 요구 사항을 통과하지 못합니다. 사용자를 위한 유일한 옵션은 한도를 통과하기 위해 더 많은 웨어러블을 구매하는 것입니다.

## 권장 사항

전송 및 매도 작업 후 사용자가 `BASE_WEARABLE_UNIT` 이상의 잔액을 가지고 있는지 확인하는 것을 고려하십시오.

# [M-04] 마지막 사용자가 자신의 모든 웨어러블을 판매할 수 없음

## 심각도

**영향:** 중간, 사용자가 1개의 Wearable 지분만 가지고 있는 경우 판매할 수 없을 수 있습니다.

**가능성:** 중간, 판매하는 마지막 사용자가 오류를 받게 됩니다.

## 설명

가격 계산은 매수와 매도 상황 모두에서 내림(rounds down) 됩니다. 따라서 내림차순으로 인해 하나의 구매를 여러 배치로 나누면 매수 가격이 매도 가격보다 낮을 수 있습니다.

`sell`에 비해 `buy`에서 더 낮은 가격이 지불되는 상황이 발생할 수 있습니다.
결과적으로, 이 테스트에서 볼 수 있듯이 계약은 매도 가격을 매수 가격보다 크게 계산할 수 있습니다:

```solidity
    function test_custom1() public {

// Setup wearable
// ------------------------------------------------------------
        bytes32 wearablesSubject = keccak256(abi.encode("test hoodie", "hoodie image url"));

        vm.startPrank(signer1);
        bytes32 digest = keccak256(
            abi.encodePacked(creator1, "test hoodie", "hoodie", "this is a test hoodie", "hoodie image url")
        ).toEthSignedMessageHash();
        (uint8 v, bytes32 r, bytes32 s) = vm.sign(signer1Privatekey, digest);
        bytes memory signature = abi.encodePacked(r, s, v);
        vm.stopPrank();

        vm.startPrank(creator1);
        sofa.createWearable(
            SofamonWearables.CreateWearableParams({
                name: "test hoodie",
                category: "hoodie",
                description: "this is a test hoodie",
                imageURI: "hoodie image url",
                isPublic: true,
                curveAdjustmentFactor: 50_000,
                signature: signature
            })
        );
        vm.stopPrank();
// ------------------------------------------------------------

        vm.startPrank(creator1);
        vm.deal(creator1, 1_000_000 ether);

        uint256 total = 0;
        // buy 10 batches of wearables
        for (uint256 i; i < 10; i++) {
            uint256 amount = 1e18 + 49_999;
            total += amount;
            uint256 buyPrice = sofa.getBuyPriceAfterFee(wearablesSubject, amount);
            sofa.buyWearables{value: buyPrice}(wearablesSubject, amount);
        }

        console.log("sellPrice                ", sofa.getSellPrice(wearablesSubject, total));
        console.log("SofamonWearables balance:", address(sofa).balance);

        // Sell all wearables
        //@note However it reverts with `error SendFundsFailed`, because not enough balance in contract
        sofa.sellWearables(wearablesSubject, total);

        console.log(creator1.balance);
    }
```

매도를 여러 배치로 나누면 문제를 피할 수 있지만, 사용자가 허용된 최소 금액인 `BASE_WEARABLE_UNIT = 0.001 ether`를 소유하고 있는 경우에는 불가능합니다.

## 권장 사항

매수 가격을 계산할 때 올림(Round up) 하십시오.

# [L-01] `abi.encodePacked`가 해시 계산에 사용됨

`abi.encodePacked`는 모든 문자열을 연결하기 때문에 동일한 서명을 사용하여 다른 매개변수로 여러 웨어러블을 배포하는 데 사용할 수 있습니다.

```solidity
    function createWearable(CreateWearableParams calldata params) external {
        // Validate signature
        {
            bytes32 hashVal = keccak256(
@>              abi.encodePacked(msg.sender, params.name, params.category, params.description, params.imageURI)
            );
            bytes32 signedHash = hashVal.toEthSignedMessageHash();
            if (signedHash.recover(params.signature) != createSigner) {
                revert InvalidSignature();
            }
        }
      ...
    }
```

간단한 테스트는 다음과 같습니다:

```solidity
    function test_custom2() public {
        bytes memory string1 = abi.encodePacked("aabb", "");
        bytes memory string2 = abi.encodePacked("aa", "bb");
        assertEq(string1, string2);
    }
```

권장 사항:

```diff
-              abi.encodePacked(msg.sender, params.name, params.category, params.description, params.imageURI)
+              abi.encode(msg.sender, params.name, params.category, params.description, params.imageURI)
```

# [L-02] 가스 청구 기능 제거 고려

생성자에서 Governor를 설정합니다. Blast 문서에 따르면 주소의 Governor가 설정되면 Governor 만 가스 및 수익(yield)을 청구할 수 있습니다.
따라서 다음 함수는 불필요합니다: `claimAllGas()`, `claimMaxGas()`, `claimGasAtMinClaimRate()`

거버넌스를 `SofamonWearables` 계약으로 반환하는 것을 고려하는 경우 액세스 제어 및 수익 청구를 구현해야 합니다.

# [L-03] 수수료 확인

SofamonWearables.sol은 수수료 설정을 확인하지 않으므로 어떤 숫자도 가능합니다. 그러나 나쁜 값들이 있습니다. 예를 들어 수수료 합계가 100%를 초과하는 경우 - 이 경우에는 웨어러블을 구매할 수는 있지만 판매는 되돌려질(revert) 것입니다.

수수료 설정 시 추가 확인을 고려하십시오:

- `protocolFeePercent`에 대한 제한 설정
- `creatorFeePercent`에 대한 제한 설정
- 두 수수료의 합계가 100%(또는 더 작은 X) 미만이어야 함

# [L-04] 최대 금액은 `2**256`이 아니라 `2**(256/3)`

곡선(Curve) 계산은 곱셈 후 나눗셈이라는 안전한 산술을 만듭니다.
그리고 첫 번째 곱셈은 `amount**3`입니다.

이는 양(amount)에 대해 `2**(256/3)`의 오버플로 제한을 설정합니다.
반면 README는 `amount`를 `웨어러블의 총공급량으로 0에서 uint256의 최대 제한까지 가능`하다고 설명합니다.

따라서 README에 이 제한을 명시하거나 더 큰 숫자를 허용하도록 가격 수학을 조정해야 합니다.

# [L-05] curveAdjustmentFactor에 대한 확인

`curveAdjustmentFactor` 설정에는 어떤 확인도 없습니다. 매우 높은 숫자의 경우 수수료 없이 무료 웨어러블 구매가 발생할 수 있습니다.

`curveAdjustmentFactor`에 대한 최소 및 최대 제한 설정을 고려하십시오.

# [L-06] 수수료 수령 시 크리에이터의 그리프(grief) 거래

`SofamonWearables`는 raw call을 사용하여 ETH 수수료를 분배합니다. 이것은 모든 수신자가 매수 및 매도 작업의 동작에 영향을 미칠 수 있는 기회입니다.

악의적인 행위자는 주어진 `wearablesSubject`의 크리에이터인 `wearables[wearablesSubject].creator`가 될 수 있습니다. 크리에이터는 주어진 수수료가 매수 또는 매도 작업에서 받은 것인지 추적하고( `wearablesSupply[]`의 증가 또는 감소 확인), 예를 들어 매도 작업에서 되돌릴(revert) 수 있습니다.

크리에이터가 수수료를 인출하기 위해 별도의 함수를 호출해야 하도록 보류 중인 ETH 수수료를 저장하는 별도의 매핑을 갖는 것이 좋습니다.
