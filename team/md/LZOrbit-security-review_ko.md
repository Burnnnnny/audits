# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Pashov Audit Group**은 **LayerZero-Labs/orbit-adapter** 저장소에 대한 시간 제한 보안 검토를 수행했으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Orbit 정보

Layer Zero Orbit은 Omnichain Fungible Tokens(OFT)를 Arbitrum의 네이티브 브리지에 통합하여 유동성 분리 문제 없이 Layer 3(L3)와 Arbitrum(L2) 네트워크 간에 원활한 자산 전송을 가능하게 합니다. 사용자는 Arbitrum 브리지 또는 OFT를 사용하여 자산을 원활하게 전송할 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약

_검토 커밋 해시_ - [baf2f49c3c4b53d7c733e49c111f39630609cda2](https://github.com/LayerZero-Labs/orbit-adapter/tree/baf2f49c3c4b53d7c733e49c111f39630609cda2)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `OrbitProxyOFT1_2.sol`
- `OrbitNativeOFT1_2.sol`

# 발견 사항

# [M-01] 비 EVM 체인에서 예상치 못한 토큰 분할 문제

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

BaseOFTV2를 상속받은 OrbitProxyOFT1_2 계약은 생성자에서 `_sharedDecimals` 매개변수를 사용합니다. 그러나 이 값은 특히 비 EVM 체인의 다양한 블록체인 네트워크에서 십진수 자릿수의 잠재적 차이를 수용하도록 명시적으로 설정되지 않았습니다.

```solidity
constructor(
    address _token,
    uint8 _sharedDecimals,
    address _lzEndpoint,
    IERC20Bridge _bridge
) BaseOFTV2(_sharedDecimals, _lzEndpoint) {
    // ... other initialization logic ...
    if (_sharedDecimals > decimals) {
        revert SharedDecimalsTooBig();
    }
    ld2sdRate = 10**(decimals - _sharedDecimals);
}
```

계약은 `_sharedDecimals`가 토큰의 소수점 자릿수보다 크지 않은지 확인하지만, 모든 잠재적 체인에서 최소 공통 분모가 사용되는지 확인하지 않습니다. OrbitProxyOFT1_2가 EVM(일반적으로 18자리)과 비 EVM 체인(예: 6자리가 있는 Aptos) 모두에 배포되는 경우 18을 `_sharedDecimals`로 사용하면 소수점 자릿수가 적은 체인에서 정밀도 손실 또는 오버플로 문제가 발생할 수 있습니다.

## 권장 사항

지원되는 모든 체인 중에서 가장 낮은 십진수 카운트를 결정하십시오.

# [M-02] 글로벌 아웃바운드 제한 부족

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

현재 구현에서는 개별 트랜잭션이 공유 소수점에서 uint64 최대값을 초과하지 않는지 확인합니다.

```solidity
    function _debitFrom(
        address _from,
        uint16,
        bytes32,
        uint256 _amount
    ) internal virtual override returns (uint) {
        ...

        uint256 cap = _sd2ld(type(uint64).max);
        if (amount > cap) {
            revert AmountOverflow();
        }

        return amount;
    }
```

아키텍처에서 가스 토큰은 L2와 L3뿐만 아니라 다른 체인 간에도 연결됩니다. 그러나 비 EVM 체인으로 브리지된 총액에 대한 전역 추적은 없습니다. 이로 인해 많은 트랜잭션에 걸친 누적 아웃바운드 금액이 공유 소수점에서 `uint64.max`를 초과할 수 있습니다. 많은 비 EVM 체인은 `uint64`를 사용하여 토큰 잔액을 나타냅니다. 총 브리지된 금액이 이 제한을 초과하면 해당 체인에서 잔액 오버플로가 발생하여 잠재적으로 토큰이 잠기거나 다른 예상치 못한 동작이 발생할 수 있습니다.

## 권장 사항

L3를 제외한 다른 체인의 총 `outboundAmount`를 추적하고 총액이 상한 `_sd2ld(type(uint64).max)`보다 큰 경우 트랜잭션을 되돌립니다.

# [L-01] 일시 중지 메커니즘 누락

OrbitNativeOFT1_2 계약에는 현재 일시 중지 메커니즘이 없습니다. 이 기능은 스마트 계약, 특히 교차 체인 토큰 전송 및 네이티브 통화 래핑/언래핑을 처리하는 계약에 매우 중요합니다. 일시 중지 기능을 사용하면 계약 소유자 또는 지정된 당사자가 발견된 취약점, 예상치 못한 동작 또는 업그레이드 중과 같은 비상 상황에서 계약 작업을 일시적으로 중단할 수 있습니다.

계약에서 Pausable 패턴을 구현하십시오.

# [L-02] 현재 체인에서 토큰의 부정확한 순환량

현재 체인에서 토큰의 순환량에는 브리지된 토큰이 포함됩니다.

```solidity
    function circulatingSupply() public view virtual override returns (uint) {
        return innerToken.totalSupply();
    }
```

브리지된 토큰에 대해 별도의 변수를 추적하여 `innerToken.totalSupply()`에서 빼는 것을 고려하십시오.

# [L-03] 반환 값 무시

`OrbitProxyOFT1_2`는 `IBridge.executeCall` 함수 반환을 무시합니다.

```solidity
    function executeCall(
        address to,
        uint256 value,
        bytes calldata data
>>  ) external returns (bool success, bytes memory returnData);
```

`ERC20Bridge` 계약의 현재 `IBridge` 구현에는 영향이 없지만 사용자 정의 구현에 자산 손실을 초래할 수 있습니다. `IBridge.executeCall` 호출의 반환 값을 확인하는 것을 고려하십시오. 문제의 두 가지 인스턴스가 있습니다.

```solidity
    function _debitFrom(
        address _from,
        uint16,
        bytes32,
        uint256 _amount
    ) internal virtual override returns (uint) {
        if (_from != _msgSender()) {
            revert OwnerNotSendCaller();
        }

        _amount = _transferFrom(_from, address(bridge), _amount);

        // _amount still may have dust if the token has transfer fee, then give the dust back to the sender
        (uint256 amount, uint256 dust) = _removeDust(_amount);
>>      if (dust > 0) bridge.executeCall(_from, dust, "");
<...>
    function _transferFrom(
        address _from,
        address _to,
        uint256 _amount
    ) internal virtual override returns (uint) {
        uint256 before = innerToken.balanceOf(_to);
        if (_from == address(bridge)) {
>>          bridge.executeCall(_to, _amount, "");
        } else if (_from == address(this)) {
            innerToken.safeTransfer(_to, _amount);
        } else {
            innerToken.safeTransferFrom(_from, _to, _amount);
        }
        return innerToken.balanceOf(_to) - before;
    }
```
