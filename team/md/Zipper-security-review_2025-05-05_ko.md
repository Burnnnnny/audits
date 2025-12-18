
# 소개

Pashov Audit Group은 해당 분야에서 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 귀하의 블록체인 프로토콜을 위한 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**fabric-dapps/zipper.contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# Zipper 소개

Zipper는 다양한 블록체인 전반에 걸쳐 예금, 인출 및 토큰 래핑을 처리하기 위한 모듈식 계약으로 구성된 크로스체인 자산 브리징 프로토콜입니다. 핵심 계약인 ZipperEndpoint는 요청 흐름, 검증인 승인, 실행 로직을 관리하고 일시 중지 메커니즘을 지원합니다. 체인, 키, 토큰, 수수료 및 저장소 팩토리와 통합되어 입력을 검증하고, 사용자 ID를 연결하고, 온체인 토큰 상호 작용을 조정합니다.

Zipper는 오프체인 요청 검증을 위해 역할 기반 액세스 제어 및 EIP-712 서명을 사용합니다. ZToken은 소스 자산을 나타내기 위해 생성되며, 발행 및 소각은 크로스체인 작업과 연결됩니다. 체인, 수수료, 사용자 키 및 저장소 매핑은 체인 ID별로 등록되어 네트워크 전반에 걸쳐 표준화된 상호 작용을 가능하게 합니다. 시스템은 팩토리 계약을 통해 동적 업데이트 및 업그레이드를 지원합니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| --- | --- | --- | --- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 해를 끼칩니다.

- 중간 (Medium) - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 (Low) - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용은 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터일 뿐이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 너무 비현실적이거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 요구합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [39900b04c83bff9cdbb8802e04c7e694b1788f9e](https://gitlab.com/fabric-dapps/zipper.contracts/-/tree/39900b04c83bff9cdbb8802e04c7e694b1788f9e)

_수정 검토 커밋 해시_ - [c07ea47a7748db717d65aa618a103a2e597b0b83](https://gitlab.com/fabric-dapps/zipper.contracts/-/tree/c07ea47a7748db717d65aa618a103a2e597b0b83)

### 범위 (Scope)

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `ZipperEndpoint`
- `ZipperFactoryChain`
- `ZipperFactoryFee`
- `ZipperFactoryKey`
- `ZipperFactoryToken`
- `ZipperFactoryVault`

# 발견 사항

# [L-01] `createVault` 및 `changeVault`가 토큰 간 저장소 할당 확인을 누락함 (`createVault` and `changeVault` miss vault assignment checks across tokens)

각 저장소는 토큰에 대해 고유해야 합니다. 그러나 `createVault` 및 `changeVault` 함수는 저장소 키가 이미 다른 토큰에 할당되었는지 확인하지 않습니다. 이로 인해 저장소가 덮어씌워지거나 실수로 재할당되어 고유성 제약 조건을 위반할 수 있습니다.

완화 방법:
저장소가 이미 다른 토큰에서 사용 중이지 않은지 확인하기 위해 두 함수에 다음 확인을 추가하십시오:

```solidity
require(vaultToToken[chainId][vault].length == 0, "Vault already exists for another token");
```

# [L-02] `createKey` 및 `changeKey`가 사용자 키 소유권 충돌 확인을 실패함 (`createKey` and `changeKey` fail to check user key ownership conflicts)

각 키는 사용자에 대해 고유해야 합니다. 그러나 `createKey` 및 `changeKey` 함수는 제공된 키가 이미 다른 사용자에게 할당되었는지 확인하지 않습니다. 이로 인해 키가 덮어씌워지거나 실수로 재할당되어 고유성 제약 조건을 위반할 수 있습니다.

완화 방법:
키가 이미 다른 사용자에게 사용 중이지 않은지 확인하기 위해 두 함수에 다음 확인을 추가하십시오:

```solidity
require(keyToUser[chainId][key] == address(0), "Key already exists for another user");
```

# [L-03] `ZipperFactoryVault:changeVault`에서 `vaultToToken`을 정리하지 않음 (In `ZipperFactoryVault:changeVault` does not clean `vaultToToken`)

`ZipperFactoryVault:createVault`에서 `tokenToVault`와 `vaultToToken` 두 매핑 값이 할당됩니다. 문제는 변경자가 나중에 새 저장소 주소를 사용하여 이러한 값을 업데이트할 때 발생합니다. 그러나 `oldVault`에 대한 데이터는 삭제되지 않아 오래된 매핑으로 인한 잠재적인 문제가 발생합니다.

# [L-04] 편집자는 수수료 구성이 설정되지 않은 경우에도 업데이트할 수 있음 (Editor can update fee configuration even if it is unset)

`updateFeeToken`, `updateFeeAmount` 및 `updateFeeTo` 함수에서 수수료 구성이 사전에 적절히 설정되었는지 확인하지 않습니다. 결과적으로 편집자는 수수료 구성이 설정되지 않은 체인 ID에 대해 수수료 구성을 실수로 업데이트할 수 있습니다.

영향의 예: 편집자가 수수료 토큰을 업데이트하면 해당 체인 ID에 대해 수수료 구성을 설정할 수 없습니다.

# [L-05] 요청 실행 중 수수료 토큰 변경으로 인해 잘못된 수수료가 발생할 수 있음 (Fee token change during request execution may cause incorrect fees)

인출 요청이 `FEE_PROVIDER_ROLE`에 의해 서명되면 `extraFee` 금액은 `feeFactory.fees(request.chainId)`의 현재 수수료 토큰 값으로 고정됩니다.

그러나 `reqWithdrawZToken()` 또는 `reqWithdrawZTokenWithPermits()`를 통해 요청이 실행되기 전에 `ZipperFactoryFee.updateFeeToken()`을 통해 수수료 토큰이 변경되면, 동일한 `extraFee` 금액이 새 수수료 토큰의 소수점 단위로 해석되어 잠재적으로 잘못된 수수료 금액으로 이어질 수 있습니다.

```solidity
function reqWithdrawZToken(WithdrawRequest memory request) public nonReentrant nonRepeat {
  --- SNIPPED ---
  require(hasRole(FEE_PROVIDER_ROLE, _validateSignature(request)), "Invalid signature");

  // Charge fee
@> (address feeToken, uint256 feeAmount, address feeTo) = feeFactory.fees(request.chainId);
@> uint256 totalFee = feeAmount + request.extraFee;
  if (totalFee > 0) {
    require(feeToken != address(0), "Fee not initialized");
    IERC20(feeToken).safeTransferFrom(_msgSender(), feeTo, totalFee);
  }
  --- SNIPPED ---
}
```

다음 시나리오를 고려해 보십시오:
1. 서명 시점:
   - 수수료 토큰은 `WETH`입니다.
   - 기본 수수료는 0.000044 WETH (~$0.1)입니다.
   - 서명된 `extraFee`는 0.00001 WETH (~$0.023)입니다.
   - 예상 총 수수료: 0.000054 WETH (~$0.123).

2. 실행 전 어느 시점:
   - `EDITOR_ROLE`이 수수료 토큰을 `DAI`로 변경합니다.
   - 기본 수수료가 0.1 DAI (~$0.1)로 업데이트됩니다.

3. 사용자는 원래 `WETH` 기준으로 서명된 extraFee 금액을 사용하여 `DAI`로 더 낮은 수수료를 지불하고 요청을 실행할 기회를 잡습니다.
```solidity
totalFee = (0.1 * 10^18) + (0.00001 * 10^18)
         = 0.10001 * 10^18 // 0.10001 DAI ~= $0.10001
```

4. 수수료 차이는 USD 기준으로 약 $0.123 대 $0.10001입니다.

서명 시점과 실행 시점 간의 수수료 토큰 일관성을 보장하기 위해 서명된 인출 요청 데이터에 수수료 토큰 주소를 포함하는 것을 고려하십시오.

# [L-06] 검증인이 제거되면 투표가 거부 쿼럼에 도달할 수 있음 (Votes could reach rejected quorum if a validator is removed)

각 요청은 최소 1/3 (33%)이 거부에 투표하면 거부되거나 2/3 (66%)가 승인에 투표하면 수락됩니다.
요청 상태를 결정하기 위해 검증인 수가 계산에 고려됩니다:

```solidity
// Reject if 1/3 rejected
if (requestStatus.rejectCount * 3 >= validatorCount) {
  requestStatus.status = VALIDATION_REJECTED;
}
// Approve if 2/3 approved
else if (requestStatus.approveCount * 3 >= validatorCount * 2) {
  requestStatus.status = VALIDATION_APPROVED;
}
```

`removeValidator()`를 호출하여 검증인 수가 줄어들 수 있다는 사실 때문에, 기술적으로 거부되어야 하지만 거부되지 않는 상태에 도달할 수 있습니다.

시나리오:
- 4명의 검증인이 있습니다.
- 첫 번째 검증인이 요청을 거부합니다. 쿼럼은 이제 거 부에 대해 1/4 (25%)입니다.
- 두 번째 검증인이 동일한 요청을 수락합니다. 쿼럼은 이제 수락에 대해 1/4 (25%)입니다.
- 한 명의 검증인이 제거됩니다. 쿼럼은 이제 둘 다에 대해 1/3 (33%)이며, 이는 거부 규칙을 충족하지만 수락 규칙은 충족하지 않습니다.
- 다른 검증인이 요청을 수락하기 위해 투표하면, 이는 먼저 요청 상태를 거부됨으로 설정한 다음 수락됨으로 설정합니다.

위에서 언급한 대로 기술적으로 거부 쿼럼에 도달하는 상태가 문제가 되는 경우, 제거된 검증인의 투표를 모든 보류 중인 요청에서 제거하는 것을 고려하십시오.

# [L-07] `keyToUser` 맵을 변경하면 `reqDepositZToken`이 죽은 주소로 전송될 수 있음 (Changing `keyToUser` map may make `reqDepositZToken` sent to dead addresses)

`ZipperFactoryKey`는 `keyToUser`를 할당하는 데 사용되며, 여기서 새 사용자를 할당하여 이전 `keyToUser`를 업데이트할 수도 있습니다:

```solidity
  function changeKey(uint16 chainId, address user, bytes memory key) external onlyRole(CHANGE_KEY_ROLE) {
    require(chain.validateEncodedAddress(chainId, key), "Invalid key");

    bytes memory oldKey = userToKey[chainId][user];
    require(oldKey.length > 0, "Key does not exist for user");
    require(keccak256(oldKey) != keccak256(key), "Key is the same");

    userToKey[chainId][user] = key;
    keyToUser[chainId][key] = user;
```

여기서 발생하는 문제는 `keyToUser[chainId][oldKey]`의 `user`가 제거되지 않는다는 것입니다. 즉, `userToKey`를 새 키로 변경하고 `keyToUser`를 새 사용자로 변경한 후에도 `keyToUser[chainId][oldKey]` 맵에는 여전히 이전 `user`가 포함되어 있습니다.

이 맵은 예금을 요청할 때 사용됩니다:

```solidity
  function reqDepositZToken( ... ) external nonReentrant onlyRole(RELAYER_ROLE) {
    // ...

    address user = keyFactory.keyToUser(chainId, receiver);

    // ...

    Request memory request = Request({
      reqType: REQ_DEPOSIT_ZTOKEN,
      chainId: chainId,
      // NOTE srcHashTx are enought to be unique identifiers
      payload: abi.encode(zToken, sender, receiver, user, zAmount, srcHashTx, srcHashIdx)
    });
```

우리가 `executeRequest`를 수행할 때 이 사용자가 자금을 받는 사람이기 때문에 매우 위험합니다:

```solidity
  function executeRequest(uint256 _requestId) external nonReentrant onlyRole(EXECUTOR_ROLE) {
    // ...
    if (request.reqType == REQ_DEPOSIT_ZTOKEN) {
      (address zToken, , , address user, uint256 zAmount, , ) = abi.decode(
        request.payload,
        (address, bytes, bytes, address, uint256, bytes, uint256)
      );
      tokenFactory.mintZToken(request.chainId, zToken, user, zAmount);
```

요약하자면, 이전 키가 입력되면 토큰은 이전 사용자에게 전송되며, 이는 더 이상 사용되지 않는 주소이거나 버려진 계약일 수 있습니다.

`ZipperFactoryVault::changeVault`에서도 동일하지만 거기에는 영향이 없습니다.

권장 사항:

`keyToUser`에서 이전 키를 제거하십시오:
```diff
    userToKey[chainId][user] = key;
    keyToUser[chainId][key] = user;
+   delete keyToUser[chainId][oldKey];
```
