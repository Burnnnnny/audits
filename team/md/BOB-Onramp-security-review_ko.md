# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**bob-collective/bob-onramp** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# BOB Onramp 소개

BOB은 비트코인과 이더리움을 기반으로 하는 하이브리드 Layer-2입니다. 비트코인 사용자가 이전에 이더리움 자산을 보유하지 않고도 BOB L2에 쉽게 온보딩할 수 있도록 설계되었습니다. 사용자는 신뢰할 수 있는 릴레이어와 조정하여 사용 가능한 유동성 중 일부를 예약하고, 비트코인 메인넷에서 BTC를 전송한 다음, 릴레이어는 BOB에서 ERC20 토큰에 대한 스왑을 실행하기 위한 머클 증명을 제공할 수 있습니다. 유동성 제공자(LP)는 먼저 해당 토큰을 Onramp.sol에 잠그고 사용자가 BOB에서 일부 스왑을 수행할 수 있도록 소량의 ETH도 잠급니다. LP는 지정된 주소로 비트코인을 받고 이를 래핑된 토큰으로 변환하고 다시 예치하여 재조정할 수 있습니다.

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

_검토 커밋 해시_ - [9bf1966f9469d2b13d426402da8adb6059a30096](https://github.com/bob-collective/bob-onramp/tree/9bf1966f9469d2b13d426402da8adb6059a30096)

_수정 검토 커밋 해시_ - [403389e4fae9d4a0dd59fa9e419cb50442cd33f3](https://github.com/bob-collective/bob-onramp/tree/403389e4fae9d4a0dd59fa9e419cb50442cd33f3)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `OnrampFactory`
- `Onramp`

# 발견 사항

# [M-01] `getTxOutputValue()`가 모든 값을 합산하지 않음

## 심각도

**영향:** 높음, 사용자의 브리지된 자금이 손실됩니다.

**가능성:** 낮음, 사용자가 `outputVector`에서 대상 주소를 여러 번 반복해야 합니다.

## 설명

브리지된 금액을 계산하기 위해 코드는 `getTxOutputValue()`를 호출하여 온램프 주소로 전송된 금액을 찾습니다. 해당 함수는 `outputVector` 목록에서 대상 주소의 첫 번째 값만 가져왔습니다. 문제는 `outputVector`에서 동일한 주소가 여러 번 반복될 수 있으며 코드는 대상 주소로 전송된 총 금액을 계산하기 위해 모든 값을 합산해야 한다는 것입니다.

## 권장 사항

`outputVector` 목록에서 `scriptPubKeyHash`에 대한 모든 값을 합산하십시오.

# [M-02] BTC 트랜잭션은 여러 입력 및 출력을 가질 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

BTC tx는 여러 입력 및 출력을 가질 수 있으며 릴레이어는 사용자가 BTC 트랜잭션을 수행한 후 BTC 트랜잭션을 기반으로 `reciever` 및 `onramp` 계약을 선택합니다. 문제는 토큰 수신자로 선택될 입력 주소와 온램프 계약으로 선택될 출력 주소가 명확하지 않다는 것입니다. 자금이 다른 수신자 주소로 이동하거나 릴레이어가 잘못된 온램프 계약을 사용할 수 있습니다. 릴레이어가 특정 BTC tx만 처리하는 경우 사용자에게 릴레이어 제한 사항에 대해 알려야 합니다.

## 권장 사항

릴레이어가 올바른 수신자 주소를 사용하는지 확인하십시오.

# [M-03] 브리지된 BTC에 대한 경쟁 조건

## 심각도

**영향:** 높음, 사용자는 브리지된 BTC를 잃게 됩니다.

**가능성:** 낮음, 특별한 조건이 필요합니다.

## 설명

BTC를 브리지하려면 사용자가 온램프 주소(`scriptPubKeyHash`)로 자금을 이체한 다음 릴레이어가 `proveBtcTransfer()`를 호출하고 대상 온램프가 브리지된 토큰을 사용자에게 전송합니다. 문제는 여러 사용자가 동시에 해당 주소로 BTC를 전송했을 수 있으며 온램프에 모든 주문을 처리할 충분한 토큰이 없을 수 있으므로 일부 사용자는 자금을 잃게 된다는 것입니다.

## 권장 사항

오프체인 릴레이어에 대한 큐 메커니즘을 추가하고 사용자를 하나씩 처리하십시오.
또는 사용자에게 BTC를 온램프 계약의 `scriptPubKeyHash`로 직접 전송하도록 요청하지 마십시오. 릴레이어 BTC 주소에 BTC를 모으고 BOB에서 브리지가 성공하면 온램프의 BTC를 전송하십시오.

# [M-04] 악의적인 수신자가 릴레이어에게 가스 그리핑(gas griefing)을 할 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

사용자가 비트코인 메인넷에서 BTC를 보내면 릴레이어는 온램프 계약을 통해 BoB 네트워크에서 스왑을 실행하려고 시도합니다.

```solidity
    function proveBtcTransfer(
        BitcoinTx.Info memory _txInfo,
        BitcoinTx.Proof memory _txProof,
        address payable _recipient,
        Onramp _onramp
    ) external onlyOwner {
        require(getOnramp[address(_onramp)], "Onramp does not exist");

        bytes32 txHash = relay.validateProof(txProofDifficultyFactor, _txInfo, _txProof);

        uint256 outputValueSat = BitcoinTx.getTxOutputValue(_onramp.scriptPubKeyHash(), _txInfo.outputVector);

        _onramp.executeSwap(txHash, outputValueSat, _recipient);
    }
```

온램프 계약은 1:1 양의 래핑된 BTC와 약간의 추가 ETH 토큰을 사례금으로 전송합니다.

```solidity
    function executeSwap(bytes32 _txHash, uint256 _outputValueSat, address payable _recipient) external onlyFactory {
         ---SNIP---

        uint256 amount = calculateAmount(_outputValueSat - feeSat);

        emit ExecuteSwap(_recipient, _outputValueSat, feeSat, amount, gratuity);

        // transfer token
        token.safeTransfer(_recipient, amount);

        // slither-disable-next-line arbitrary-send-eth,low-level-calls
>>      (bool sent,) = _recipient.call{value: gratuity}("");
        require(sent, "Could not transfer ETH");
    }
```

악의적인 수신자 계약은 모든 트랜잭션 가스를 소비하고 릴레이어를 괴롭히기 위해 함수를 되돌리는 `receive` 함수를 구현하여 이를 이용할 수 있습니다.
상대적으로 작을 수 있는 `dustThreshold`를 제외하고는 BTC 금액에 대한 최소 제한이 없으므로 이 공격은 소량의 토큰으로 반복적으로 실행되어 릴레이어의 가스를 낭비할 수 있습니다. 최악의 경우 이는 프로토콜의 서비스 거부(DoS)로 이어질 수 있습니다.

## 권장 사항

ETH를 보내는 대신 수신자가 청구할 수 있는 풀(pull) 메커니즘을 만드십시오.

# [M-05] 여러 온램프가 동일한 scriptPubKeyHash를 가질 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

각 온램프 계약에는 사용자가 BTC를 브리지하기 위해 입금하는 `scriptPubKeyHash` 주소가 있습니다. 문제는 코드가 다른 온램프 계약이 다른 `scriptPubKeyHash` 주소를 가지고 있는지 확인하지 않는다는 것입니다. 이는 오프체인 릴레이 구현에 따라 문제를 일으키고 여러 영향을 미칠 수 있습니다.

1. 사용자는 동일한 `scriptPubKeyHash`를 가진 온램프 계약에서 여러 번 입금하고 토큰을 받을 수 있습니다.
2. LP 공급자는 온램프 계약에 대해 중복 `scriptPubKeyHash`를 설정하고 다른 사용자의 BTC 전송을 선행 실행하여 자신의 토큰을 자체 브리지하고 온램프에서 자금을 받고 사용자에게 손실을 입힐 수 있습니다.
3. LP 공급자는 다른 온램프 계약에 대해 중복 `scriptPubKeyHash`를 설정하고 해당 온램프의 다른 수수료로 인해 사용자가 자금을 잃게 할 수 있습니다.

## 권장 사항

계약은 다른 온램프 계약이 동일한 `scriptPubKeyHash` 주소를 갖도록 허용해서는 안 됩니다.

# [L-01] 소수점 <8인 토큰 미지원

생성자의 언더플로로 인해 소수점 이하 자릿수가 8개 미만인 토큰에 대한 온램프 계약을 생성하는 것은 불가능합니다.

```solidity
    constructor(
        address _owner,
        ERC20 _token,
        bytes32 _scriptPubKeyHash,
        uint64 _feeDivisor,
        // should be a very small amount
        uint64 _gratuity
    ) Ownable() {
        transferOwnership(_owner);
        factory = msg.sender;
        token = _token;
>>      multiplier = 10 ** (token.decimals() - 8);
```

wBTC 및 tBTC 토큰의 경우 문제가 되지 않을 수 있지만 향후 8개 미만의 소수점을 가진 브리지된 BTC 토큰이 생성되면 문제가 될 수 있습니다. `calculateAmount` 함수를 리팩터링하는 것을 고려하십시오.

```diff
    function calculateAmount(uint256 _outputValueSat) public view virtual returns (uint256) {
+       uint8 decimals = token.decimals();
+       uint256 amountSubFee = decimals >= 8 ? _outputValueSat * 10 ** (decimals - 8) : _outputValueSat / 10 ** (8 - decimals);
-       uint256 amountSubFee = _outputValueSat * multiplier;
        return amountSubFee;
    }
```

# [L-02] 비트코인 트랜잭션은 큰 `outputVector`를 가질 수 있음

사용자가 토큰을 브리지하려고 하면 릴레이어는 `proveBtcTransfer()`를 호출하여 전송을 완료합니다. 문제는 코드가 `getTxOutputValue()`를 호출하여 브리지된 금액을 찾고 해당 함수가 비트코인 tx의 모든 `outputVector`를 반복한다는 것입니다. `getTxOutputValue`의 크기는 최대 400K 및 3000 출력일 수 있으므로 `proveBtcTransfer()` 호출에 대해 OOG가 발생하고 사용자의 브리지된 트랜잭션을 완료할 수 없을 수 있습니다.

# [L-03] `validateProof`는 `encodePacked`를 사용

`validateProof` 함수는 트랜잭션 해시를 계산합니다.

```solidity
txHash =
            abi.encodePacked(txInfo.version, txInfo.inputVector, txInfo.outputVector, txInfo.locktime).hash256View();
```

문제는 `txInfo.inputVector`와 `txInfo.outputVector`가 모두 가변 길이 바이트 객체라는 것입니다. 이는 `encodePacked`와 함께 사용할 때 안전하지 않습니다. `encodePacked`는 개별 길이를 고려하지 않고 단순히 합치기 때문에 다른 입력으로 해시 충돌을 일으킬 수 있습니다.

예를 들어 `encodePacked([a,b],[c])`의 해시는 `encodePacked([a],[b,c])`의 해시와 동일합니다. 이것은 또한 가변 길이인 바이트 객체까지 확장됩니다.

이것은 현재 코드베이스에서 악용할 수 없지만 여전히 권장되지 않습니다. 대신 `abi.encode()`를 사용하는 것을 고려하십시오.

자세한 정보는 여기에서 찾을 수 있습니다: https://swcregistry.io/docs/SWC-133/

# [L-04] 사용자가 대기하는 동안 LP 매개변수 변경됨

비트코인 네트워크의 블록 확인에는 최대 10분이 소요될 수 있습니다. 여러 확인의 경우 릴레이어가 BOB에서 토큰을 발행하기 위해 트랜잭션을 시작하기 전에 사용자가 20-30분을 기다릴 수 있습니다.

문제는 사용자가 전달할 수 있는 `deadline` 매개변수가 없다는 것입니다. 따라서 사용자는 오랜 시간을 기다려야 할 수 있으며 이 대기 시간 동안 LP의 상태가 변경될 수 있습니다.

유동성 공급자는 `startUpdate`를 호출하고 6시간을 기다린 후 매개변수를 변경할 수 있습니다. 이 기간 동안 사용자는 여전히 스왑을 수행하기 위해 해당 풀을 활용할 수 있습니다. 그러나 이 대기 기간이 곧 끝날 때 비트코인 네트워크에서 블록 확인을 기다리는 사용자가 이미 트랜잭션을 시작했을 수 있습니다. 그러면 풀 소유자가 매개변수를 변경하고 수수료를 업데이트할 수 있으며 트랜잭션이 발생하여 사용자에게 예상보다 많은 수수료가 부과됩니다.

주요 문제는 비트코인에서 브리징하는 데 오랜 시간이 걸릴 수 있으며 마지막 블록이 생성된 시점에 따라 10분의 분산이 있다는 것입니다. MEV가 있는 EVM 기반 체인에서 이 10분 동안 많은 일이 발생할 수 있습니다. 특히 이 시간 창이 풀 구성의 잠금 해제 시간과 일치하는 경우 더욱 그렇습니다.

풀은 사용자가 트랜잭션을 시작했을 때 가입하지 않은 극단적인 수수료(최대 50%)를 지불하도록 강요할 수 있습니다.

특정 양의 토큰을 강제로 배출하는 `slippage` 매개변수나 너무 늦은 경우 스왑이 진행되지 않도록 하는 `deadline` 매개변수를 추가하는 것을 고려하십시오.

# [L-05] 다른 LP에서 동일한 BTC 트랜잭션 재생

BTC 브리징 이벤트가 시작되면 릴레이어는 관련 트랜잭션 정보 및 증명을 수집하고 EVM 체인(BOB)에 제출하여 온램프에서 해당 토큰을 발행합니다. 시스템은 여러 온램프를 갖도록 설계되었으므로 사용자는 원하는 램프를 선택할 수 있습니다.

문제는 BTC 트랜잭션이 발행을 수행하는 데 사용되면 다시는 사용할 수 없도록 무효화되어야 한다는 것입니다. 이것은 모든 브리지의 매우 핵심적인 디자인이며 따라야 합니다.

여기서 문제는 이 트랜잭션 무효화가 현재 설계의 개별 램프 수준에서 발생한다는 것입니다.

```solidity
// Onramp.sol
spent[_txHash] = true;
```

다른 램프는 이 상태를 공유하지 않습니다. 따라서 트랜잭션이 램프 A에서 무효화되더라도 램프 B에서 BTC 입금 트랜잭션이 아직 무효화되지 않았기 때문에 램프 B에서 다시 트리거될 수 있습니다.

따라서 릴레이어는 단일 증명을 사용하여 모든 램프에서 자산을 발행할 수 있는 능력이 있습니다. 릴레이어는 신뢰할 수 있는 당사자에 의해 제어되지만 이미 소진된 트랜잭션을 사용하여 다시 발행할 권한이 없어야 합니다.

현재 상태의 계약은 본질적으로 릴레이어가 모든 램프에서 동일한 BTC 토큰을 이중 지출하도록 허용합니다.

트랜잭션 해시 무효화는 중앙 `OnrampFactory` 계약으로 이동해야 합니다. 이렇게 하면 동일한 txHash를 두 개의 다른 램프에서 사용할 수 없습니다.

