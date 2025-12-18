# 정보
Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항
스마트 컨트랙트 보안 분선은 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론
**moondance-labs/tanssi-symbiotic** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# Tanssi에 대하여
Tanssi는 Symbiotic에 존재하는 지분(stakes)을 통해 보안을 보장하면서 기하급수적으로 substrate/EVM 기반 블록체인을 생성할 수 있는 인프라를 제공합니다.

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

_검토 커밋 해시_
- [5e9cf50106f4624cf4e975097e9360ce61a12e20](https://github.com/moondance-labs/tanssi-symbiotic/tree/5e9cf50106f4624cf4e975097e9360ce61a12e20)
- [f39bcaa2957642174e7201ed00df27e8e57f4b6d](https://github.com/moondance-labs/tanssi-bridge-relayer/tree/f39bcaa2957642174e7201ed00df27e8e57f4b6d)

_수정 검토 커밋 해시_
- [8ec5ade1155212bcb3e714ca3b8c9298d193c73e](https://github.com/moondance-labs/tanssi-symbiotic/tree/8ec5ade1155212bcb3e714ca3b8c9298d193c73e)


### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `OSharedVaults` 
- `QuickSort` 
- `Middleware` 
- `MiddlewareProxy` 
- `MiddlewareStorage` 
- `OBaseMiddlewareReader` 
- `ODefaultOperatorRewards` 
- `ODefaultStakerRewards` 
- `ODefaultStakerRewardsFactory` 
- `BeefyClient` 
- `Gateway` 
- `Operators` 
- `Types` 
- `OSubstrateTypes` 
- `GatewayCoreStorage` 

# 발견 사항

 # [H-01] 무허가(Permissionless) `sendCurrentOperatorsKeys()`

## 심각도

**영향:** 높음 (High)

**가능성:** 중간 (Medium)

## 설명

### 첫 번째 영향

`Middleware.sendCurrentOperatorsKeys()` 함수는 무허가이므로 외부 행위자가 누구나 호출할 수 있습니다. 이 함수는 브리지 이더리움 측에서 `Gateway.sendOperatorsData()` 함수를 트리거합니다:
- OutboundMessageAccepted 이벤트를 방출합니다.
- 채널의 outboundNonce를 증가시킵니다.
- 브리지 인프라의 아웃바운드 채널에 메시지를 제출합니다.

```solidity
// Middleware.sol
    function sendCurrentOperatorsKeys() external returns (bytes32[] memory sortedKeys) {
        address gateway = getGateway();
        if (gateway == address(0)) {
            revert Middleware__GatewayNotSet();
        }

        uint48 epoch = getCurrentEpoch();
        sortedKeys = IOBaseMiddlewareReader(address(this)).sortOperatorsByPower(epoch);
        IOGateway(gateway).sendOperatorsData(sortedKeys, epoch);
    }
```
이제 `Gateway::sendOperatorsData()`가 어떻게 작동하는지 보겠습니다:
```solidity
    function sendOperatorsData(bytes32[] calldata data, uint48 epoch) external onlyMiddleware {
        Ticket memory ticket = Operators.encodeOperatorsData(data, epoch);
        _submitOutboundToChannel(PRIMARY_GOVERNANCE_CHANNEL_ID, ticket.payload);
    }
```
이 아웃바운드에 대한 티켓 생성은 비용이 0이므로, 이 아웃바운드 메시지를 보내는 것은 수수료가 없고 무료입니다. `Operators` 라이브러리에서 볼 수 있듯이:
```solidity
        // TODO: This is a type from Snowbridge, do we want our own simplified Ticket type?
        ticket.dest = ParaID.wrap(0);
        // TODO For now mock it to 0
@>        ticket.costs = Costs(0, 0);

        ticket.payload = OSubstrateTypes.EncodedOperatorsData(operatorsKeys, uint32(validatorsKeysLength), epoch);
```
보시다시피 누구나 비용 없이 무허가로 브리지의 인프라 노드에 스팸을 보내 DoS 공격을 할 수 있습니다. 이는 그리핑(griefing), 자원 고갈 또는 경쟁 조건(race conditions)과 같은 브리지 운영에 심각한 문제를 일으킬 것으로 예상됩니다.

### 두 번째 영향

`Middleware.sendCurrentOperatorsKeys()`는 사용자가 현재 검증인(validator) 세트를 Tanssi 합의 계층으로 보낼 수 있도록 합니다. 이 함수가 `epochStartTs`에 정확히 호출되면 같은 블록 내에서 다른 결과를 반환할 수 있습니다. 예를 들어, 사용자가 지분을 `예치(deposit)`하면 대기열에 새 검증인이 합류할 수 있고, 사용자가 지분을 `인출(withdraw)`하면 검증인이 대기열에서 떠날 수 있습니다.

새로운 검증인 세트가 `SymbioticMessageProcessor`의 `process_message` 함수를 통해 처리되면 [`pallet_external_validators::Pallet::<T>::set_external_validators_inner`](https://github.com/moondance-labs/tanssi/blob/3e0b626af0b234e768c6a31c8b0fcb0963b62224/pallets/external-validators/src/lib.rs#L386)를 호출하여 새 검증인 세트를 설정합니다.

새로운 검증인 세트가 수신될 때마다 이전에 저장된 검증인 세트 정보를 덮어쓰고, 다음 시대(era)가 되어야 검증인 세트에 공식적으로 기록됩니다.

사용자가 한 블록 내에서 제출한 첫 번째 `OperatorsKeys`가 Tanssi 체인의 이전 시대에 포함되고, 두 번째 또는 후속 OperatorsKeys가 다음 시대에 포함되는 엣지 케이스가 있습니다. 이로 인해 잘못된 에폭(epoch)이 발생할 수 있습니다. 에폭의 첫 번째 시대는 잘못된 오퍼레이터 세트(이더리움 측과 일치하지 않음)를 사용하고 나머지 시대는 올바른 오퍼레이터 세트를 사용합니다. 이더리움 측의 슬래싱(slashing) 및 보상 로직은 후자 시대의 올바른 검증인 세트에 전적으로 의존하므로 첫 번째 시대는 슬래싱 및 보상에 잘못된 값을 사용하게 됩니다.

## 권고 사항

특정 역할(예: `FORWARDER_ROLE`)을 요구하여 `sendCurrentOperatorsKeys()`에 대한 접근을 제한하거나, 에폭 타이밍에 기반한 속도 제한 메커니즘을 도입하거나, 소액의 수수료를 도입하십시오.

# [M-01] 특정 경우 동일한 보상을 여러 번 청구할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`ODefaultOperatorRewards` 계약은 활성 지분을 기준으로 오퍼레이터에게 보상을 분배합니다. 중복 청구를 방지하기 위해 계약은 다음을 통해 청구된 금액을 추적합니다:
```solidity
claimed[eraIndex][recipient]
```
그러나 수신자(recipient)는 다음을 사용하여 `operatorKey`에서 파생됩니다:
```solidity
address recipient = IOBaseMiddlewareReader(middlewareAddress).operatorByKey(abi.encode(input.operatorKey));
```
문제는 미들웨어에 의해 오퍼레이터 키가 시간이 지남에 따라 재할당될 수 있다는 것입니다. 이전에 한 오퍼레이터와 연관되었던 키가 나중에 다른 오퍼레이터 주소에 재할당되는 경우, 해당 새로운 오퍼레이터는 동일한 보상을 다시 청구할 수 있습니다. 수신자 주소가 다르기 때문에 `claimed` 매핑은 중복 청구를 감지하지 못합니다.

## 권고 사항

수신자 주소 대신 `operatorKey`로 청구된 보상을 추적하십시오: `claimed[eraIndex][key]`.

# [M-02] `setCollateralToOracle()`로 담보를 비활성화하면 보상 청구가 차단됨

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`Middleware`에서 `setCollateralToOracle(collateral, address(0))`을 호출하면 관련 오라클을 제거하여 담보를 비활성화합니다. 이는 치명적인 문제를 야기합니다: **이 담보를 사용한 볼트는 오퍼레이터가 보상을 청구하는 것을 방지합니다.**
```solidity
    function claimRewards(
        ClaimRewardsInput calldata input
    ) external nonReentrant returns (uint256 amount) {
        --Snipped--
        _distributeRewardsToStakers(
            eraRoot_.epoch, input.eraIndex, stakerAmount, recipient, middlewareAddress, tokenAddress, input.data
        );
    }

   function _distributeRewardsToStakers(
        uint48 epoch,
        uint48 eraIndex,
        uint256 stakerAmount,
        address operator,
        address middlewareAddress,
        address tokenAddress,
        bytes calldata data
    ) private {
        --Snipped--
        uint256[] memory amountPerVault = _getRewardsAmountPerVault(
            operatorVaults, totalVaults, epochStartTs, operator, middlewareAddress, stakerAmount
        );

        _distributeRewardsPerVault(epoch, eraIndex, tokenAddress, totalVaults, operatorVaults, amountPerVault, data);
    }
```
문제는 `claimRewards()` 실행 중에 발생합니다. 이 함수는 `_distributeRewardsToStakers()`를 호출하는데, 이는 볼트 파워를 계산하기 위해 `stakeToPower()`에 의존합니다. `stakeToPower()`는 `collateralToOracle()`을 통해 오라클 주소를 가져옵니다. 매핑이 `address(0)`을 반환하면 함수가 되돌려져(revert) 전체 보상 청구 프로세스를 차단합니다.

## 권고 사항

- 과거 볼트에 대한 보상을 안전하게 처리하는 메커니즘이 마련되지 않는 한 **`collateralToOracle`을 `address(0)`으로 설정하는 것을 금지하십시오**.
- **담보에 대한 "은퇴(retired)" 상태**를 도입하여 새로운 볼트가 비활성화된 담보를 사용하는 것을 방지하면서 과거 보상을 청구할 수 있도록 하십시오.
- 과거 계산을 위해 실시간 가격 피드에 의존하지 않도록 **에폭당 오라클 가격을 캐싱**하는 것을 고려하십시오.

추가 참고 사항

이 문제는 과거 회계에 대해서도 실시간 오라클 데이터에 대한 체계적인 의존성을 반영합니다. `collateralToOracle`에 영향을 미치는 관리 또는 거버넌스 작업은 보상 계산을 위한 하위 호환성을 보장해야 합니다.

# [M-03] 오퍼레이터 키 변경으로 인해 미청구 보상에 대한 접근 권한을 잃을 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`ODefaultOperatorRewards` 계약의 `claimRewards()` 함수는 제공된 `operatorKey`에서 오퍼레이터의 주소를 확인하기 위해 `operatorByKey()`에 의존합니다. 그러나 이 접근 방식은 오퍼레이터 키가 업데이트될 때 취약합니다.
```solidity
function claimRewards(
        ClaimRewardsInput calldata input
    ) external nonReentrant returns (uint256 amount) {
        OperatorRewardsStorage storage $ = _getOperatorRewardsStorage();
        EraRoot memory eraRoot_ = $.eraRoot[input.eraIndex];
        address tokenAddress = eraRoot_.tokenAddress;
        if (eraRoot_.root == bytes32(0)) {
            revert ODefaultOperatorRewards__RootNotSet();
        }
        --Snipped--
        // Starlight sends back only the operator key, thus we need to get back the operator address
@>      address recipient = IOBaseMiddlewareReader(middlewareAddress).operatorByKey(abi.encode(input.operatorKey));
         --Snipped--
}
```
`Middleware` 관리자는 `updateOperatorKey()`, `registerOperator()` 또는 `unregisterOperator()`를 호출하여 오퍼레이터의 키를 변경할 수 있습니다. 내부적으로 이러한 함수는 [`KeyManager256._updateKey()`](https://github.com/symbioticfi/middleware-sdk/blob/a65b247c0f468cabac9e05712119d5dc2292a46a/src/extensions/managers/keys/KeyManager256.sol#L88)를 호출하여 키와 주소 매핑을 업데이트합니다. 이 함수는 이전 키에 대한 데이터를 삭제하고 사실상 해제(release)합니다.
키가 해제되면 `operatorByKey()`는 다음 중 하나를 수행할 수 있습니다:
- `address(0)` 반환 또는.
- 재할당 후 다른 오퍼레이터 주소 반환.

결과적으로 미청구 보상(삭제된 키와 연관됨)이 있는 오퍼레이터는 더 이상 `claimRewards()`를 통해 이를 청구할 수 없으며 사실상 해당 보상에 대한 접근 권한을 잃게 됩니다.

## 권고 사항

오퍼레이터 키를 업데이트하고 키 데이터를 삭제하기 전에 오퍼레이터 보상이 청구되었는지 확인하십시오.

# [M-04] 볼트 큐레이터가 정밀도(precision) 문제로 `operator.claimReward()`를 강제로 되돌릴(revert) 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

볼트 큐레이터는 오퍼레이터에게 **매우 적은 양**의 지분을 할당할 수 있습니다. `_getRewardsAmountPerVault()`의 정밀도 손실(예: 반올림 오류)로 인해 볼트는 **0의 보상 금액**을 갖게 될 수 있습니다.
```solidity
    function _getRewardsAmountPerVault(
        address[] memory operatorVaults,
        uint256 totalVaults,
        uint48 epochStartTs,
        address operator,
        address middlewareAddress,
        uint256 stakerAmount
    ) private view returns (uint256[] memory amountPerVault) {
        --Snipped--
        for (uint256 i; i < totalVaults;) {
            uint256 amountForVault;
            // Last vault gets the remaining amount
            if (i == totalVaults - 1) {
                amountForVault = stakerAmount - distributedAmount;
            } else {
83: @>              amountForVault = vaultPowers[i].mulDiv(stakerAmount, totalPower);
            }
            amountPerVault[i] = amountForVault;
            unchecked {
                distributedAmount += amountForVault;
                ++i;
            }
        }
    }
```
나중에 오퍼레이터가 `claimReward()`를 호출하면 각 볼트에 대해 `vault.distributeRewards()`와 `_transferAndCheckAmount()`가 트리거됩니다. 볼트에 대해 계산된 보상 금액이 0이면 `_transferAndCheckAmount()` 함수가 **되돌려져(revert)** **전체 청구 트랜잭션이 실패**할 수 있습니다.

결과적으로:
- 오퍼레이터는 **자신의 보상을 청구할 수 없습니다**.
- 오퍼레이터와 관련된 다른 볼트들도 보상 몫을 받지 못하도록 차단됩니다.

## 권고 사항
다음과 같은 보호 조치를 구현하십시오:
- 배포 중 보상 금액이 0인 **볼트를 건너뛰십시오**.
- 정밀도 기반 악용을 피하기 위해 **최소 지분 임계값을 설정하십시오**.

# [M-05] `getOperatorPowerAt`이 과거 지분 계산에 현재 가격을 사용함

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`OBaseMiddlewareReader::getOperatorPowerAt()`은 모든 활성 볼트의 지분을 기반으로 특정 오퍼레이터가 과거 타임스탬프에 가졌던 지분 파워(stake power)를 계산하려고 합니다. 이 과거 지분 파워는 `sortOperatorsByPower`에서 다른 오퍼레이터와 정렬하고, `ODefaultOperatorRewards::_getRewardsAmountPerVault`에서 `stakerAmount`가 활성 공유 볼트 간에 어떻게 분배될지 결정하는 데 사용됩니다.
```solidity
    function _getRewardsAmountPerVault(
        address[] memory operatorVaults,
        uint256 totalVaults,
        uint48 epochStartTs,
        address operator,
        address middlewareAddress,
        uint256 stakerAmount
    ) private view returns (uint256[] memory amountPerVault) {
        // ...

        uint256[] memory vaultPowers = new uint256[](totalVaults);
        uint256 totalPower;
        for (uint256 i; i < totalVaults;) {
@>            vaultPowers[i] = reader.getOperatorPowerAt(epochStartTs, operator, operatorVaults[i], subnetwork);
            totalPower += vaultPowers[i];
            unchecked {
                ++i;
            }
        }

        // ...
    }
```
그러나 `getOperatorPowerAt`은 현재 토큰 가격을 사용하기 때문에 과거 타임스탬프에서 오퍼레이터의 올바른 지분 파워를 계산하지 못합니다. `getOperatorPowerAt`의 작동 방식은 다음과 같습니다.
```solidity
// VaultManager.sol
    function _getOperatorPowerAt(
        uint48 timestamp,
        address operator,
        address vault,
        uint96 subnetwork
    ) internal view returns (uint256) {
        uint256 stake = _getOperatorStakeAt(timestamp, operator, vault, subnetwork);
        return stakeToPower(vault, stake);
    }

    function _getOperatorStakeAt(
        uint48 timestamp,
        address operator,
        address vault,
        uint96 subnetwork
    ) private view returns (uint256) {
        bytes32 subnetworkId = _NETWORK().subnetwork(subnetwork);
@>        return IBaseDelegator(IVault(vault).delegator()).stakeAt(subnetworkId, operator, timestamp, "");
    }
```
`_getOperatorStakeAt`에서 볼 수 있듯이 지정된 과거 타임스탬프의 지분을 올바르게 검색합니다. 그러나 `stakeToPower`에서 지분을 파워로 변환하는 동안 Chainlink 피드의 담보 토큰 현재 가격을 사용합니다:
```solidity
    function stakeToPower(address vault, uint256 stake) public view override returns (uint256 power) {
        address collateral = vaultToCollateral(vault);
        address oracle = collateralToOracle(collateral);

        if (oracle == address(0)) {
            revert Middleware__NotSupportedCollateral(collateral);
        }
        (, int256 price,,,) = AggregatorV3Interface(oracle).latestRoundData();
        uint8 priceDecimals = AggregatorV3Interface(oracle).decimals();
        power = stake.mulDiv(uint256(price), 10 ** priceDecimals);
        // Normalize power to 18 decimals
        uint8 collateralDecimals = IERC20Metadata(collateral).decimals();
        if (collateralDecimals != DEFAULT_DECIMALS) {
            power = power.mulDiv(10 ** DEFAULT_DECIMALS, 10 ** collateralDecimals);
        }
    }
```
결과적으로 계산된 파워는 주어진 과거 타임스탬프에서 오퍼레이터의 파워를 올바르게 반영하지 않습니다. 이는 `ODefaultOperatorRewards`에서 잘못된 보상 분배로 이어지는데, 보상 청구가 최근 가격 변동의 영향을 받기 때문입니다. 이는 분배 시점(즉, `ODefaultOperatorRewards::distributeRewards`가 호출되었을 때)의 가격이 다를 것이기 때문에 부정확합니다.

## 권고 사항

보상 분배 또는 지분 스냅샷 시점의 각 담보 토큰에 대한 과거 가격 데이터를 저장하고 참조하십시오. 이를 위해 Open Zeppelin [Checkpoints](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/Checkpoints.sol) 라이브러리를 사용하십시오.

# [M-06] `_getRewardsAmountPerVault`가 오래된 볼트 파워를 사용할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

Symbiotic에서 현재 에폭 내에 볼트가 슬래싱되면 `activeStake`가 즉시 감소합니다:
```solidity
// https://github.com/symbioticfi/core/blob/main/src/contracts/vault/Vault.sol#L237
            if (slashedAmount > 0) {
                uint256 activeSlashed = slashedAmount.mulDiv(activeStake_, slashableStake);
                uint256 nextWithdrawalsSlashed = slashedAmount - activeSlashed;

                _activeStake.push(Time.timestamp(), activeStake_ - activeSlashed);
                withdrawals[captureEpoch + 1] = nextWithdrawals - nextWithdrawalsSlashed;
            }
```

그러나 현재 Tanssi 구현에서는 한 에폭 내에 `_distributeRewardsToStakers`가 몇 번 호출되든 관계없이 다른 볼트의 파워는 여전히 에폭 시작 시의 파워 가중치를 기준으로 계산됩니다:

```solidity
//ODefaultOperatorRewards.sol::L242
        uint256[] memory amountPerVault = _getRewardsAmountPerVault(
            operatorVaults, totalVaults, epochStartTs, operator, middlewareAddress, stakerAmount
        );
```

즉, 에폭 중에 볼트가 슬래싱되면 Tanssi 메인넷의 합의 계층에서는 받아야 할 보상이 줄어들어야 하지만 Symbiotic에서는 보상이 변경되지 않습니다.

## 권고 사항

현재 eraIndex부터 시작하는 타임스탬프를 사용하여 파워를 계산하십시오.

# [L-01] 타임스탬프가 에폭 시작과 일치할 때 `getEpochAtTs()`가 부정확함

`OBaseMiddlewareReader.getEpochAtTs()` 함수는 입력 타임스탬프가 해당 에폭의 `epochStart`와 같을 때 잘못된 에폭 번호를 반환합니다.
```solidity
    function getEpochAtTs(
        uint48 timestamp
    ) public view returns (uint48 epoch) {
        EpochCaptureStorage storage $ = _getEpochCaptureStorage();
        return (timestamp - $.startTimestamp) / $.epochDuration;
    }
```
symbiotic 에폭 처리 함수([`getCurrentEpoch()`](https://github.com/symbioticfi/middleware-sdk/blob/a65b247c0f468cabac9e05712119d5dc2292a46a/src/extensions/managers/capture-timestamps/EpochCapture.sol#L63) 등)에 구현된 로직에 따르면 올바른 접근 방식은 나눗셈을 수행하기 전에 타임스탬프에서 1을 빼는 것입니다:
```solidity
    function getCurrentEpoch() public view returns (uint48) {
        EpochCaptureStorage storage $ = _getEpochCaptureStorage();
        if (_now() == $.startTimestamp) {
            return 0;
        }

        return (_now() - $.startTimestamp - 1) / $.epochDuration;
    }
```

현재 중요 경로에서 이 함수의 사용이 식별되지는 않았지만, 이 잘못된 동작은 정확한 에폭 식별에 의존하는 다른 프로토콜이나 오프체인 도구에 미묘한 버그나 불일치를 일으킬 수 있습니다.

# [L-02] `operatorVaultPairs` 배열 길이가 조정되지 않음

오퍼레이터 및 볼트 데이터를 가져올 때 `getOperatorVaultPairs` 함수는 `operatorVaultPairs` 배열을 총 오퍼레이터 수와 동일한 길이로 할당합니다:
```solidity
    function getOperatorVaultPairs(
        uint48 epoch
    ) external view returns (IMiddleware.OperatorVaultPair[] memory operatorVaultPairs) {
        uint48 epochStartTs = getEpochStart(epoch);
        address[] memory operators = _activeOperatorsAt(epochStartTs);

>>      operatorVaultPairs = new IMiddleware.OperatorVaultPair[](operators.length);

        uint256 valIdx = 0;
        uint256 operatorsLength_ = operators.length;
        for (uint256 i; i < operatorsLength_;) {
            address operator = operators[i];
            (uint256 vaultIdx, address[] memory _vaults) = getOperatorVaults(operator, epochStartTs);

            if (vaultIdx != 0) {
                operatorVaultPairs[valIdx++] = IMiddleware.OperatorVaultPair(operator, _vaults);
            }
            unchecked {
                ++i;
            }
        }
    }
```
그러나 모든 오퍼레이터가 연결된 볼트를 가지고 있는 것은 아닙니다. 데이터는 오퍼레이터에게 하나 이상의 볼트가 있는 경우에만 채워집니다. 결과적으로 초기화되지 않은(빈) 요소가 배열에 남아 호출자에게 반환될 때 불완전하거나 오해의 소지가 있는 결과로 이어질 수 있습니다.

다음과 같이 배열 길이를 조정하는 것을 고려하십시오:

```solidity
        assembly ("memory-safe") {
            mstore(operatorVaultPairs, valIdx)
        }
```

# [L-03] `ClaimAdminFee()` 이벤트에서 `epoch` 누락

`claimAdminFee()`에 의해 방출되는 `ClaimAdminFee` 이벤트에는 `epoch` 매개변수가 없습니다. 이로 인해 청구된 관리 수수료가 어느 에폭에 해당하는지 추적하기 어려우며, 특히 여러 에폭이나 토큰에 걸쳐 여러 청구가 발생하는 경우 더욱 그렇습니다.

```solidity
emit ClaimAdminFee(recipient, tokenAddress, claimableAdminFee_);
```

청구의 중요한 매개변수이므로 `epoch` 값을 포함하도록 `ClaimAdminFee` 이벤트를 업데이트하는 것을 고려하십시오.

# [L-04] 볼트 에폭 종료 근처에서 `Middleware::slash`가 `VetoSlasher`로 인해 되돌려질(revert) 수 있음

볼트 에폭 종료 근처에서 `Middleware::slash`가 호출되면 슬래싱 요청을 `VetoSlasher`로 전달할 때 되돌려질 수 있습니다. 이는 `VetoSlasher.requestSlash`에 `captureTimestamp`가 전체 `vetoDuration`을 수용할 수 있을 만큼 충분히 앞서 있는지 확인하는 엄격한 검사가 포함되어 있기 때문입니다:
```solidity
if (
    captureTimestamp < Time.timestamp() + vetoDuration - IVault(vault).epochDuration() ||
    captureTimestamp >= Time.timestamp()
) {
    revert InvalidCaptureTimestamp();
}
```
`Middleware::slash`는 `Gateway`에 의해 트리거되므로 이 실패는 다시 전파되어 조용한 실패를 일으킵니다. 베토(veto) 슬래싱 창이 유효하지 않은 경우 슬래싱 시도를 건너뛰도록 `Middleware`에 사전 확인을 추가하는 것을 고려하십시오.

# [L-05] `ODefaultOperatorRewards`의 생성자에 `disableInitializers()` 호출 누락

`ODefaultOperatorRewards` 계약은 프록시를 통한 업그레이드 가능한 배포를 위해 설계된 `UUPSUpgradeable`을 상속합니다. 그러나 이 계약의 생성자는 `_disableInitializers()`를 호출하지 않습니다. 이는 `initialize()` 함수를 누구나 호출할 수 있게 하므로 위험할 수 있으며 OZ의 권장 사항에 위배됩니다.

이 패턴은 생성자에서 `_disableInitializers()`가 올바르게 호출되는 `Middleware`와 같은 유사한 업그레이드 가능한 계약에서 해결되었습니다.

```solidity
    constructor(
        address network,
        address networkMiddlewareService
    ) notZeroAddress(network) notZeroAddress(networkMiddlewareService) {
        i_network = network;
        i_networkMiddlewareService = networkMiddlewareService;
    }
```

`ODefaultOperatorRewards::constructor`에 `_disableInitializers()`를 추가하는 것을 고려하십시오.

# [L-06] `_beforeRegisterOperator`가 유효하지 않은 검증인 등록을 허용함

현재 프로토콜은 사용자가 0이 아닌 모든 검증인 키를 등록할 수 있도록 허용하며, 이는 Substrate 체인으로 그대로 전달되어 검증인 세트에 저장됩니다. 그러나 이더리움 측에서 프로토콜은 이 Substrate 주소가 유효한지 또는 확인할 수 있는지를 검증하지 않습니다. 예:

- 잘못된 SS58 체크섬.
- 잘못된 길이.
- 유효하지 않은 Base58 문자.
- 잘못된 네트워크 접두사.
- Ed25519 곡선에 있지 않은 점.

합의 계층이 이러한 잘못된 키를 적절하게 처리할 수 있는지 확인하십시오. 합의 계층이 이러한 키를 처리할 수 없는 경우 체인 중단으로 이어질 수 있습니다. 이더리움과 같은 다른 많은 잘 알려진 프로토콜도 유사한 문제에 직면했습니다: https://github.com/ethereum/go-ethereum/security/advisories/GHSA-q26p-9cq4-7fc2.

# [L-07] `adminFeeAmount`가 항상 내림(round down)되어 관리자 수익 감소 초래

ODefaultStakerRewards.sol은 관리 수수료를 다음과 같이 계산합니다:

```solidity
//ODefaultStakerRewards.sol::L244
    function _updateAdminFeeAndRewards(uint256 amount, uint256 adminFee_, uint48 epoch, address tokenAddress) private {
        // Take out the admin fee from the rewards
        uint256 adminFeeAmount = amount.mulDiv(adminFee_, ADMIN_FEE_BASE);
        // And distribute the rest to the stakers
        uint256 distributeAmount = amount - adminFeeAmount;
```

이는 각 보상 분배 중에 `ADMIN_FEE_BASE` 미만인 부분은 직접 버려진다는 것을 의미합니다. 기대값을 고려하면 관리자는 호출당 평균 `ADMIN_FEE_BASE / 2`의 수익을 잃습니다.

- $USDT 및 $USDC의 경우 5000 / 1e6 = 0.005 USD입니다.
- $WBTC의 경우 5000 / 1e8 * 1e5 = 5 USD입니다.
- $STAR의 경우 5000 / 1e12 = 5e-9 $STAR입니다.

# [L-08] `distributeRewards()` 되돌림(revert)

보상 분배가 우연히 에폭의 첫 번째 블록(즉, `block.timestamp == epochTs`)에서 발생하고, 사용자가 동일한 블록 내에서 `ODefaultOperatorRewards.claimRewards()`를 호출하면 계약이 보상 분배를 미래 블록에 있는 것처럼 처리하는 엣지 케이스가 현재 존재합니다. 이로 인해 성공해야 할 유효한 호출이 실패하게 됩니다.
```solidity
//ODefaultStakerRewards.sol::L191
        // If the epoch is in the future, revert
        if (epochTs >= Time.timestamp()) {
            revert ODefaultStakerRewards__InvalidRewardTimestamp();
        }
```

코드 주석에 언급된 대로 이 함수는 현재 에폭이 아니라 미래 에폭을 차단해야 합니다.

# [L-09] 베토(veto) 슬래싱을 위한 `requestSlash()` 후 `executeSlash()` 누락

`Middleware` 계약의 `_slashVault()` 함수는 슬래셔 유형에 따라 슬래싱을 처리합니다. `SlasherType.VETO`의 경우 `VetoSlasher.requestSlash()`를 올바르게 호출하여 슬래싱을 요청합니다. 그런 다음 베토 단계 이후 `vetoSlasher.executeSlashe()`를 호출하여 슬래싱 작업을 확정해야 합니다([문서](https://docs.symbiotic.fi/modules/vault/slashing/#1-vetoslasher)).
```solidity
    function _slashVault(
        uint48 timestamp,
        address vault,
        bytes32 subnetwork,
        address operator,
        uint256 amount
    ) private {
        address slasher = IVault(vault).slasher();

        if (slasher == address(0) || amount == 0) {
            return;
        }
        uint256 slasherType = IEntity(slasher).TYPE();

        uint256 response;
        if (slasherType == uint256(SlasherType.INSTANT)) {
            response = ISlasher(slasher).slash(subnetwork, operator, amount, timestamp, new bytes(0));
            emit VaultManager.InstantSlash(vault, subnetwork, response);
        } else if (slasherType == uint256(SlasherType.VETO)) {
            response = IVetoSlasher(slasher).requestSlash(subnetwork, operator, amount, timestamp, new bytes(0));
            emit VaultManager.VetoSlash(vault, subnetwork, response);
        } else {
            revert VaultManager.UnknownSlasherType();
        }
    }
```
현재 구현에서 `executeSlash()`는 `Middleware` 내에서 구현되거나 호출되지 않아 슬래싱이 불완전한 상태로 남습니다. 이러한 공백으로 인해 베토 기반 슬래싱이 요청되지만 실행되지 않을 수 있습니다.

**참고**: 테스트 케이스에서는 `executeSlash()` 함수가 미들웨어를 대신하여 직접 호출됩니다.

권고 사항:
`Gateway` 및 `Middleware` 내에 `executeSlash()` 함수를 구현하고 통합하여 베토 슬래싱 프로세스를 완료하십시오.

# [L-10] 미들웨어가 `bytes32(0)`을 검증인 키로 보낼 수 있음

미들웨어 계약은 주기적으로 검증인 데이터를 게이트웨이로 보냅니다:
```solidity
            // Decode the sorted keys and the epoch from performData
            (bytes32[] memory sortedKeys, uint48 epoch) = abi.decode(performData, (bytes32[], uint48));
            IOGateway(gateway).sendOperatorsData(sortedKeys, epoch);
```
검증인 키는 `getValidatorSet()`을 통해 수집되며, 이는 `getOperatorKeyAt()`에 의존하여 검증인의 키를 반환합니다:
```solidity
    function getValidatorSet(
        uint48 epoch
    ) public view returns (IMiddleware.ValidatorData[] memory validatorSet) {
        uint48 epochStartTs = getEpochStart(epoch);
        address[] memory operators = _activeOperatorsAt(epochStartTs);
        validatorSet = new IMiddleware.ValidatorData[](operators.length);

        uint256 len = 0;
        uint256 operatorsLength_ = operators.length;
        for (uint256 i; i < operatorsLength_;) {
            address operator = operators[i];
            unchecked {
                ++i;
            }
>>          bytes32 key = abi.decode(getOperatorKeyAt(operator, epochStartTs), (bytes32));
            uint256 power = _getOperatorPowerAt(epochStartTs, operator);
            validatorSet[len++] = IMiddleware.ValidatorData(power, key);
        }

        // shrink array to skip unused slots
        assembly ("memory-safe") {
            mstore(validatorSet, len)
        }
    }
```
그러나 `getOperatorKeyAt()`은 특정 엣지 케이스(예: `Operators.updateOperatorKey()`를 사용하여 오퍼레이터의 키가 비활성화되고 다른 활성 키가 남아 있지 않은 경우)에서 `bytes32(0)`을 반환할 수 있습니다. 이로 인해 0 키가 인코딩되어 게이트웨이로 전송되는 검증인 세트에 포함되어 잠재적으로 다운스트림 가정을 위반할 수 있습니다. 최악의 시나리오: 일부 보상이 0 키 검증인에게 할당되어 청구할 수 없게 됩니다.

```solidity
    function getOperatorKeyAt(address operator, uint48 timestamp) public view returns (bytes memory) {
        KeyManager256Storage storage $ = _getKeyManager256Storage();
        bytes32 key = $._key[operator];
        if (key != bytes32(0) && $._keyData[key].status.wasActiveAt(timestamp)) {
            return abi.encode(key);
        }
        key = $._prevKey[operator];
        if (key != bytes32(0) && $._keyData[key].status.wasActiveAt(timestamp)) {
            return abi.encode(key);
        }
>>      return abi.encode(bytes32(0));
    }
```

권고 사항:

검증인 세트에 포함하기 전에 0 값 키를 필터링하십시오:
```diff
    function getValidatorSet(
        uint48 epoch
    ) public view returns (IMiddleware.ValidatorData[] memory validatorSet) {
        uint48 epochStartTs = getEpochStart(epoch);
        address[] memory operators = _activeOperatorsAt(epochStartTs);
        validatorSet = new IMiddleware.ValidatorData[](operators.length);

        uint256 len = 0;
        uint256 operatorsLength_ = operators.length;
        for (uint256 i; i < operatorsLength_;) {
            address operator = operators[i];
            unchecked {
                ++i;
            }
            bytes32 key = abi.decode(getOperatorKeyAt(operator, epochStartTs), (bytes32));
            uint256 power = _getOperatorPowerAt(epochStartTs, operator);
+           if(key != bytes32(0) {
                validatorSet[len++] = IMiddleware.ValidatorData(power, key);
+           }
        }

        // shrink array to skip unused slots
        assembly ("memory-safe") {
            mstore(validatorSet, len)
        }
    }
```

# [L-11] `ODefaultStakerRewards` 없이 볼트 보상 분배 실패

`ODefaultOperatorRewards` 계약의 `_distributeRewardsPerVault` 함수에서 로직은 `operatorVaults`에 나열된 모든 볼트가 `vaultToStakerRewardsContract` 매핑에 해당 스테이커 보상 계약이 설정되어 있다고 가정합니다. 그러나 이 가정은 `Operators::registerOperatorVault`를 통해 등록된 오퍼레이터 특정 볼트에 대해서는 부정확합니다:

```solidity
    function _distributeRewardsPerVault(
         // ...
    ) private {
        OperatorRewardsStorage storage $ = _getOperatorRewardsStorage();
        for (uint256 i; i < totalVaults;) {
@>            address stakerRewardsForVault = $.vaultToStakerRewardsContract[operatorVaults[i]];
            IERC20(tokenAddress).approve(stakerRewardsForVault, amountPerVault[i]);
            IODefaultStakerRewards(stakerRewardsForVault).distributeRewards(
                epoch, eraIndex, amountPerVault[i], tokenAddress, data
            );

            // ...
        }
    }
```

이는 `_distributeRewardsPerVault`에 전달된 `operatorVaults` 배열이 다음을 사용하여 검색되기 때문에 발생합니다:
```solidity
IOBaseMiddlewareReader(middlewareAddress).getOperatorVaults(operator, epochStartTs);
```
이는 오퍼레이터의 모든 볼트(공유 볼트 및 특정 볼트 모두)를 반환합니다. 그러나 특정 볼트에는 `vaultToStakerRewardsContract`가 없습니다. 따라서 스폰서 메시지 "**오퍼레이터 볼트 등록 가능성**이 있을 수 있지만 현재는 이에 대한 목표가 없습니다."에 따르더라도, 오퍼레이터 볼트 등록이 `ODefaultOperatorRewards::claimRewards` 호출을 완전히 DoS할 것이라고 고려하는 것이 좋습니다.

권고 사항:
오퍼레이터로부터 활성 공유 볼트만 가져오고 실제로 `vaultToStakerRewardsContract` 계약이 있는 이러한 볼트에 대해서만 보상 분배를 수행하는 것을 고려하십시오.

# [L-12] `Middleware::stakeToPower`에서 오래되거나 유효하지 않은 가격 데이터에 대한 검증 누락

`stakeToPower` 함수는 `AggregatorV3Interface(oracle).latestRoundData()`를 통해 `Chainlink` 오라클에서 최신 가격을 가져오지만, 반환된 가격 데이터가 최신이고 유효하며 오래되지 않았는지 확인하는 검증을 수행하지 않습니다. Chainlink 피드는 오라클 네트워크 지연이나 실패와 같은 특정 조건에서 오래되거나 불완전한 데이터를 반환할 수 있으며, 이로 인해 부정확하거나 조작된 파워 계산이 발생할 수 있습니다.
```solidity
    function stakeToPower(address vault, uint256 stake) public view override returns (uint256 power) {
        address collateral = vaultToCollateral(vault);
        address oracle = collateralToOracle(collateral);

        if (oracle == address(0)) {
            revert Middleware__NotSupportedCollateral(collateral);
        }
@>        (, int256 price,,,) = AggregatorV3Interface(oracle).latestRoundData();
        uint8 priceDecimals = AggregatorV3Interface(oracle).decimals();
        power = stake.mulDiv(uint256(price), 10 ** priceDecimals);
        // Normalize power to 18 decimals
        uint8 collateralDecimals = IERC20Metadata(collateral).decimals();
        if (collateralDecimals != DEFAULT_DECIMALS) {
            power = power.mulDiv(10 ** DEFAULT_DECIMALS, 10 ** collateralDecimals);
        }
    }
```

권고 사항:
다음과 같은 표준 Chainlink 보안 검증을 구현하십시오:
```solidity
(, int256 price, , uint256 updatedAt,) = AggregatorV3Interface(oracle).latestRoundData();

require(price > 0, "Invalid oracle price");
require(updatedAt >= block.timestamp - MAX_STALE_TIME, "Stale price data");
```

# [L-13] 미들웨어가 매우 낮은 활성 파워를 가진 검증인을 유효한 것으로 취급함

`middleware`가 `performUpkeep` 또는 `sendCurrentOperatorsKeys`를 통해 Tanssi 합의 계층에 메시지를 보낼 때마다 활성 지분 파워가 있는 모든 검증인을 반복하고 정렬합니다.

Tanssi는 각 검증인의 정확한 파워에는 신경 쓰지 않고 오직 상대적인 순서만 신경 씁니다. 문제는 현재 검증인의 활성 파워가 충분한지 확인하는 메커니즘이 없다는 것입니다. 검증인이 매우 낮은 파워를 가지고 목록 하단에 나타나더라도 Substrate 체인에서는 여전히 정상적으로 기능할 수 있습니다. 이는 네트워크의 일부 검증인이 제대로 스테이킹되지 않을 수 있으며, 그들에게 적용되는 모든 슬래싱도 최소화될 것임을 의미합니다. 이는 경제적 관점에서 네트워크의 보안을 약화시킵니다.

권고 사항:
`_quickSort`에 최소 임계값을 추가하고 지분이 낮은 검증인을 버리십시오.

# [L-14] 보류 중인 슬래시(Pending slashing)가 고려되지 않음

`Middleware.sol`은 최대 슬래싱 가능 금액을 결정하기 위해 `IBaseDelegator(IVault(vault).delegator()).stakeAt()`을 사용합니다:

```solidity
        uint256 vaultStake = IBaseDelegator(IVault(vault).delegator()).stakeAt(
            subnetwork, params.operator, params.epochStartTs, new bytes(0)
        );
```

VetoSlasher를 사용할 때 보류 중인 슬래시는 `stake()`/`stakeAt()` 호출에 반영되지 않습니다. 이는 [Symbiotic 문서](https://docs.symbiotic.fi/manuals/network-manual#:~:text=You%20are%20aware,slashings%E2%80%99%20processing%20logic.)에 명시적으로 언급되어 있습니다:

> "NetworkRestakeDelegator.stakeAt()/FullRestakeDelegator.stakeAt() (네트워크에서 오퍼레이터의 지분을 반환하는 함수)은 기존 자금 전체를 실제 지분으로 계산한다는 점을 인식하고 있습니다. 즉, 귀하 또는 귀하의 '이웃' 네트워크로부터의 보류 중인 슬래싱을 고려하지 않습니다. 따라서 볼트의 유형과 슬래싱 처리 로직에 따라 미들웨어에서 이를 다루어야 합니다."

이로 인해 몇 가지 문제가 발생할 수 있습니다:

- 슬래싱은 백분율로 적용되므로, 검증인이 단일 에폭 내에서 여러 번 슬래싱되는 경우(Tanssi 체인이 각 시대(era) 시작 시 이전 시대의 검증인 최대 슬래시(75%로 제한됨)를 실행하고 에폭에 여러 시대가 포함되기 때문에 가능함), 두 슬래시 모두 결국 동일한 지분 금액을 사용하게 될 수 있습니다. vetoSlasher.requestSlash와 vetoSlasher.executeSlash 사이의 시간이 한 시대 이상 걸리는 경우 두 슬래시 모두 첫 번째 슬래시의 지분을 기반으로 실행될 수 있습니다. 결합된 슬래시 백분율이 50%를 초과하면 두 번째 슬래시가 실패할 수 있습니다.
- 보류 중인 슬래싱이 지분 계산에 포함되지 않기 때문에 검증인이 옵트인 상태여서 슬래싱 가능해야 하지 않아야 할 때 슬래싱 가능한 것으로 간주될 수 있습니다.

권고 사항:

외부 네트워크의 보류 중인 슬래시를 추적하는 것은 불가능하거나 실행 가능하지 않습니다. 이것은 단순히 symbiotic 프로토콜의 약점입니다.

# [L-15] 볼트가 슬래싱을 악의적으로 피할 수 있음

[버너 라우터(burner routers)](https://docs.symbiotic.fi/modules/extensions/burners/#burner-router)를 사용하는 볼트의 경우 다음 악용 시나리오가 가능합니다: 볼트는 사용자 지정 네트워크를 생성하고 해당 네트워크에 대해 자체 수신자를 지정하여 자금을 자신이 제어하는 주소로 전송할 수 있습니다. 이 볼트가 검증인 역할도 한다면 악의적으로 행동할 수 있습니다. Tanssi 네트워크 내에서 자신의 오작동을 감지/확인하면 볼트는 즉시 사용자 지정 네트워크 내에서 자신을 슬래싱하여 Tanssi 네트워크가 자체 슬래싱을 시행할 기회를 갖기 전에 담보를 다른 곳으로 전송할 수 있습니다. 따라서 Tanssi로부터의 페널티를 피할 수 있습니다.

이 때문에 Tanssi 네트워크는 `middleware`와 통합된 모든 볼트를 완전히 신뢰해야 합니다. 모든 볼트는 슬래시를 피할 수 있는 능력이 있지만 반드시 그렇게 할 동기가 있는 것은 아닙니다.

권고 사항:

이 문제를 완화하기 위해 실행 가능한 솔루션은 최소 버너 라우터 지연을 강제하여 오프체인 모니터링 서비스가 볼트 상태의 보류 중인 변경 사항을 관찰하고 악의적인 행동을 감지하려고 시도할 시간을 허용하는 것입니다. 실패한 슬래싱 시도도 오프체인에서 모니터링해야 하며, 볼트가 악의적으로 슬래싱을 피하는 것으로 밝혀지면 여러 검증인과 함께 악의적인 행동을 실행하기 전에 즉시 등록 취소해야 합니다.

# [L-16] 재귀적 QuickSort는 일부 최악의 시나리오에서 항상 실패함

```solidity
File: contracts/libraries/QuickSort.sol
34:     function _quickSort(IMiddleware.ValidatorData[] memory arr, int256 left, int256 right) public pure {
[...]
48:         if (left < j) {
49:             _quickSort(arr, left, j);
50:         }
51:         if (i < right) {
52:             _quickSort(arr, i, right);
53:         }
```

이 프로토콜은 QuickSort 알고리즘의 재귀 버전을 구현합니다. 평균적인 경우 재귀 깊이는 `log(n)`으로 추정되지만, 일부 엣지 케이스의 경우 깊이는 `n`에 가깝습니다. 이러한 경우는 피벗(pivot)이 항상 가장 작거나 가장 큰 요소일 때 발생합니다(예: 배열이 이미 정렬되어 있거나 역순으로 정렬된 경우).
Solidity 언어는 최대 스택 깊이 1024로 제한됩니다. 즉, 1024개 이상의 요소가 있는 일부 목록에서 `_quickSort()` 함수를 호출하는 것이 불가능합니다.

설명된 문제를 피하고 여전히 `O(n*log(n))` 시간 복잡도를 유지하려면 반복적(iterative) QuickSort 알고리즘으로 전환하는 것이 좋습니다.

이 문제는 1024개 이상의 요소가 있는 배열을 정렬하는 동안 가스 제한에 도달하지 않을 가능성이 매우 낮기 때문에 '낮음(Low)'으로 평가되었습니다. 이 함수를 활용한 코드:

```solidity
 /**
     * @dev Sorts operators by their total power in descending order, after 500 it will be almost impossible to be used on-chain since 500 ≈ 36M gas
[...]
        validatorSet = validatorSet.quickSort(0, int256(validatorSet.length - 1));
```

이는 개발자가 이러한 제한 사항을 인지하고 있음을 시사합니다. 그러나 가스 부족과 최대 스택 깊이 제한은 동일한 근본 원인이 아니므로 이 문제는 기록을 위해 별도로 보고되었습니다.

# [L-17] `performUpkeep()`이 Chainlink의 `maxPerformDataSize`를 초과할 수 있음

Chainlink [문서](https://docs.chain.link/chainlink-automation/overview/supported-networks#ethereum)에 따르면 `performUpkeep` 함수로 보낼 수 있는 최대 크기(바이트)는 `2000`바이트입니다.

`performUpkeep()` 구현 내에서 `performData`에는 정렬된 키와 `performData`의 에폭이 포함됩니다.

```solidity
File: contracts/middleware/Middleware.sol
316:     function performUpkeep(
317:         bytes calldata performData
318:     ) external override checkAccess {
319:         StorageMiddleware storage $ = _getMiddlewareStorage();
320:         address gateway = $.gateway;
321:         if (gateway == address(0)) {
322:             revert Middleware__GatewayNotSet();
323:         }
324: 
325:         uint48 currentTimestamp = Time.timestamp();
326:         if ((currentTimestamp - $.lastTimestamp) > $.interval) {
327:             $.lastTimestamp = currentTimestamp;
328: 
329:             // Decode the sorted keys and the epoch from performData
330:             (bytes32[] memory sortedKeys, uint48 epoch) = abi.decode(performData, (bytes32[], uint48));
331: 
332:             IOGateway(gateway).sendOperatorsData(sortedKeys, epoch);
333:         }
334:     }
```

Chainlink Keepers는 또한 엄격한 최대 가스 사용량에 의해 제한되며, 이는 `sortOperatorsByPower()`와 같은 고비용 작업을 실행하는 능력에 영향을 미칩니다. 개발팀은 코드 주석에 언급된 대로 이 함수와 관련된 상당한 가스 소비를 알고 있습니다:

```solidity
File: contracts/middleware/OBaseMiddlewareReader.sol
521:     /**
522:      * @dev Sorts operators by their total power in descending order, after 500 it will be almost impossible to be used on-chain since 500 ≈ 36M gas
523:      * @param epoch The epoch number
524:      * @return sortedKeys Array of sorted operators keys based on their power
525:      */
526: 
```

그러나 간과된 것으로 보이는 추가적인 제약이 있습니다. Chainlink는 `maxPerformDataSize` 제한을 강제하며, 이는 처리할 수 있는 오퍼레이터 수에 더 엄격한 상한선을 부과합니다. 각 오퍼레이터 키는 `bytes32` 값으로 표시되므로 포함할 수 있는 총 키 수는 2000 / 32 = 62개 키로 제한됩니다.

이로 인해 실질적인 상한선은 62명의 오퍼레이터가 되며, 이는 주석에서 참조된 500명 오퍼레이터 추정치보다 훨씬 낮습니다.

미들웨어는 오퍼레이터 수를 제한하는 메커니즘을 구현하지 않았으므로, 해당 수가 너무 커지면(오퍼레이터가 너무 많으면) Chainlink의 `maxPerformDataSize`에 도달하게 됩니다.

**권고 사항**

오퍼레이터 수를 제한하는 메커니즘을 구현하십시오.


