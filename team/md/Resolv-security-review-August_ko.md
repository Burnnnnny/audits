# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**resolv-im/resolv-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Resolv 정보

Resolv는 ETH로 뒷받침되는 스테이블코인 USR을 발행하고 숏 선물 포지션으로 ETH 가격 위험을 헤지하여 미달러 대비 가치를 안정적으로 유지하는 프로토콜입니다. 또한 보험 풀인 RLP를 유지하여 USR이 초과 담보 상태를 유지하도록 하고 사용자가 예치된 담보로 이러한 토큰을 민팅하고 상환할 수 있도록 합니다.

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

- 낮음 - 가정이 너무 많거나 희박하거나, 인센티브가 거의 또는 전혀 없이 공격자가 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 - 수정하는 것이 좋음

- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [2e24f0e76525a663e222530a88bdd968e5e818eb](https://github.com/resolv-im/resolv-contracts/tree/2e24f0e76525a663e222530a88bdd968e5e818eb)

_수정 검토 커밋 해시_ - [65f664227802dfe3fd112cee9fb307c2c9113f20](https://github.com/resolv-im/resolv-contracts/tree/65f664227802dfe3fd112cee9fb307c2c9113f20)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Treasury`
- `LidoTreasuryConnector`
- `AaveV3TreasuryConnector`
- `LPExternalRequestsManager`

# 발견 사항

# [M-01] 재무(Treasury)에 자금이 부족할 때 사용자 자금이 일시적으로 동결될 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

LPExternalRequestsManager 계약에서 사용자가 소각을 요청하면 요청이 먼저 처리된 다음 SERVICE_ROLE에 의해 확인됩니다.

```solidity
    function processBurns(
        bytes32 _idempotencyKey,
        uint256[] calldata _ids
    ) external onlyRole(SERVICE_ROLE) idempotentProcessBurn(_idempotencyKey) {

    function completeBurns(
        bytes32 _idempotencyKey,
        CompleteBurnItem[] calldata _items,
        address[] calldata _withdrawalTokens
    ) external onlyRole(SERVICE_ROLE) idempotentCompleteBurn(_idempotencyKey) {
```

사용자는 요청이 처리되기 전(즉, 생성 시) 또는 부분적으로 완료된 후에만 소각 요청을 취소할 수 있습니다.

```solidity
        if (BurnRequestState.CREATED != request.state && BurnRequestState.PARTIALLY_COMPLETED != request.state) {
            revert IllegalState(uint256(request.state));
        }
```

문제는 SERVICE_ROLE이 소각을 처리한 후 완료하려고 시도하지만 재무에 자금이 부족할 때 발생합니다.

```solidity
withdrawalToken.safeTransferFrom(treasuryAddress, address(this), totalWithdrawalCollateralAmounts[i]);
```

자금 부족으로 인해 소각을 완료할 수 없는 경우 사용자의 토큰은 사실상 계약에 동결됩니다. 소각이 처리되면 처리 취소할 수 없으므로 사용자는 요청을 취소할 수 없습니다. 이로 인해 소각 요청을 이행하기 위해 재무에 충분한 자금이 생길 때까지 사용자의 자금이 잠기는 부당한 상황이 발생합니다.

## 권장 사항

이행되지 않은 소각 요청을 처리 취소하는 기능을 추가하는 것을 고려하십시오.

# [M-02] 잘못된 스테이크 한도 확인

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`LidoTreasuryConnector.deposit()` 함수는 현재 스테이크 한도에 의해 제한되는 금액으로 Lido 풀에 ETH를 제출하도록 설계되었습니다.

```solidity
        if (lidoCurrentStakeLimit <= amount) {
            revert StakeLimit(lidoCurrentStakeLimit, amount);
        }
```

그러나 이 논리는 흠이 있습니다. 함수는 `lidoCurrentStakeLimit == amount`일 때 잘못 되돌립니다. 예를 들어 `lidoCurrentStakeLimit`가 1000이고 재무가 1000 ETH를 예치하려고 하면 예치가 허용되어야 함에도 불구하고 함수가 되돌려집니다.

## 권장 사항

조건을 다음과 같이 업데이트하십시오.

```solidity
        if (lidoCurrentStakeLimit < amount) {
            revert StakeLimit(lidoCurrentStakeLimit, amount);
        }
```

# [M-03] 격리 모드에 진입한 후 종료할 메커니즘이 없음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`AaveV3TreasuryConnector` 계약의 `supply()` 함수를 사용하면 사용자가 ETH 또는 ERC20 토큰을 담보로 공급할 수 있습니다. 함수는 자산의 담보 사용이 활성화되어 있는지 확인합니다. 그렇지 않으면 다음 코드를 사용하여 활성화합니다.

```solidity
            if (!_isUsageAsCollateralEnabled(_token)) {
                aavePool.setUserUseReserveAsCollateral(_token, true);
            }
```

- 문제는 Aave v3에서 사용자가 격리된 자산을 담보로 공급하면 격리 모드에서 허용된 자산만 빌릴 수 있다는 것입니다.

- 또한 격리 모드의 사용자는 다른 격리된 자산을 포함하여 다른 자산을 담보로 활성화할 수 없습니다.

- 결과적으로 첫 번째로 공급된 자산이 격리된 자산인 경우 `AaveV3TreasuryConnector`를 통해 다른 비격리 자산을 공급할 수 없습니다. `supply()` 함수가 새로 공급된 모든 자산을 담보로 활성화하려고 시도하는데, 이는 격리 모드와 호환되지 않기 때문입니다.

## 권장 사항

이 문제를 해결하려면 `setUserUseReserveAsCollateral()` 함수를 `supply()` 함수와 분리하는 것을 고려하십시오. 격리 모드를 종료하는 유일한 방법은 격리된 자산을 담보로 비활성화하는 것인데, 이는 기존 구현으로는 불가능합니다.

# [M-04] 담보 인출은 허용된 제공자로만 제한되어서는 안 됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`LPExternalRequestsManager` 계약에서 화이트리스트가 활성화된 경우 허용된 제공자만 민트 및 소각 요청을 생성할 수 있습니다.

```solidity
File: LPExternalRequestsManager.sol
40:     modifier onlyAllowedProviders() {
41:         if (isWhitelistEnabled && !providersWhitelist.isAllowedAccount(msg.sender)) {
42:             revert UnknownProvider(msg.sender);
43:         }
44:         _;
45:     }
...
144:     function requestMint(
145:         address _depositTokenAddress,
146:         uint256 _amount,
147:         uint256 _minMintAmount
148:     ) public onlyAllowedProviders allowedToken(_depositTokenAddress) whenNotPaused {
...
215:     function requestBurn(
216:         address _withdrawalTokenAddress,
217:         uint256 _amount
218:     ) public onlyAllowedProviders allowedToken(_withdrawalTokenAddress) whenNotPaused {
```

동시에 원래 요청 생성자가 화이트리스트에 있는지 여부에 관계없이 이러한 요청을 취소하는 것은 허용됩니다.

```solidity
File: LPExternalRequestsManager.sol
179:     function cancelMint(uint256 _id) external mintRequestExist(_id) {
180:         MintRequest storage request = mintRequests[_id];
181:         _assertAddress(request.provider, msg.sender);
211: ...
329:     function cancelBurn(uint256 _id) external burnRequestExist(_id) {
330:         BurnRequest storage request = burnRequests[_id];
331:         _assertAddress(request.provider, msg.sender);
```

그러나 완료된 요청에서 담보를 인출하는 것도 화이트리스트에 있는 제공자로만 제한됩니다.

```solidity
File: LPExternalRequestsManager.sol
346:     function withdrawAvailableCollateral(uint256 _id) external onlyAllowedProviders burnRequestExist(_id) {
```

이로 인해 제공자가 담보를 인출할 수 있어야 함에도 불구하고 인출하지 못하는 두 가지 시나리오가 발생할 수 있습니다.

- 소각 요청을 완료한 후 담보를 인출하기 전에 목록에서 제외된 경우
- 요청이 생성된 후 계약에서 화이트리스트가 활성화되었지만 제공자가 화이트리스트에 포함되지 않은 경우

## 권장 사항

`LPExternalRequestsManager#withdrawAvailableCollateral()` 함수에서 `onlyAllowedProviders` 수정자를 제거하는 것을 고려하십시오.

# [M-05] 많은 수의 소각 요청이 처리되는 경우 소각이 DoS될 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

소각 흐름의 성공적인 실행을 위해 소각 요청은 먼저 `LPExternalRequestsManager#processBurns()` 함수를 사용하여 처리된 다음 몇 개의 `for` 루프를 포함하는 `LPExternalRequestsManager#completeBurns()` 함수를 통해 완료되어야 합니다. "가장 무거운" 하나의 루프(290줄)의 반복 횟수는 이전에 처리된 소각 요청의 수(L283)에 전적으로 의존합니다.

```solidity
File: LPExternalRequestsManager.sol
275:     function completeBurns(
276:         bytes32 _idempotencyKey,
277:         CompleteBurnItem[] calldata _items,
278:         address[] calldata _withdrawalTokens
279:     ) external onlyRole(SERVICE_ROLE) idempotentCompleteBurn(_idempotencyKey) {
...
283:         if (burnEpoch.burnRequestIds.length != _items.length) {
284:             revert InvalidBurnRequestsLength(burnEpoch.burnRequestIds.length, _items.length);
285:         }
...
290:         for (uint256 i = 0; i < _items.length; i++) {
291:             _completeBurn(_idempotencyKey, _items[i], currentEpoch);
292:
293:             totalBurnAmount += _items[i].burnAmount;
294:
295:             bool withdrawalTokenFound = false;
296:             for (uint256 j = 0; j < _withdrawalTokens.length; j++) {
297:                 if (_withdrawalTokens[j] == _items[i].withdrawalToken) {
298:                     totalWithdrawalCollateralAmounts[j] += _items[i].withdrawalCollateralAmount;
299:                     withdrawalTokenFound = true;
300:                     break;
301:                 }
302:             }
303:             if (!withdrawalTokenFound) {
304:                 revert WithdrawalTokenNotFound(_items[i].withdrawalToken);
305:             }
306:         }
...
312:         for (uint256 i = 0; i < totalWithdrawalCollateralAmounts.length; i++) {
313:             if (totalWithdrawalCollateralAmounts[i] > 0) {
314:                 IERC20 withdrawalToken = IERC20(_withdrawalTokens[i]);
315:                 // slither-disable-next-line arbitrary-send-erc20
316:                 withdrawalToken.safeTransferFrom(treasuryAddress, address(this), totalWithdrawalCollateralAmounts[i]);
317:             }
318:         }
...
```

처리 함수와 달리, 완료 함수에는 예측할 수 없는 가스 소비를 가질 수 있는 변경 가능한 로직을 가진 외부 계약(토큰)에 대한 호출이 포함됩니다. 이로 인해 많은 수의 소각 요청이 처음에 성공적으로 처리되었지만 완료 호출이 있는 트랜잭션이 가스 제한 도달로 인해 되돌려지는 상황이 발생할 수 있습니다. 요청을 "처리 취소"할 방법이 없고 새 요청 처리는 이전 요청이 완료될 때만 가능하므로 전체 소각 흐름이 DoS됩니다.

## 권장 사항

한 에포크에서 처리될 수 있는 소각 요청의 수에 대한 상한 제한을 추가하는 것을 고려하십시오.

# [L-01] `withdrawAvailableCollateral`은 일시 중지할 수 없음

일반적으로 계약을 일시 중지 가능하게 만드는 목적은 사용자가 유발하는 자금 흐름 및 기타 상태 변경 기능을 중지하는 것입니다.
`LPExternalRequestsManager` 계약의 경우 허용된 제공자(사용자)가 호출할 수 있는 `requestMint`, `requestBurn` 및 `withdrawAvailableCollateral` 메서드가 있습니다. 이들 모두 자금 흐름을 포함하지만 앞의 두 가지만 일시 중지할 수 있습니다.

따라서 `withdrawAvailableCollateral` 메서드에도 `whenNotPaused` 수정자를 추가하는 것이 좋습니다.

# [L-02] RLP를 `LPExternalRequestsManager`에서 담보 토큰으로 추가 할 수 있음

`LPExternalRequestsManager` 계약의 `constructor`나 `addAllowedToken` 메서드는 동일한 RLP를 민팅/소각하기 위한 담보 토큰으로 `ISSUE_TOKEN_ADDRESS`(RLP)를 추가하려는 시도를 거부하지 않습니다.

이것이 문제가 되려면 관리자의 실수가 필요하지만 잠재적인 영향을 피하고 `constructor` 및 `addAllowedToken` 메서드에서 `ISSUE_TOKEN_ADDRESS`를 확인하는 것이 좋습니다.

# [L-03] Permit 프론트러닝 보호가 합법적인 오류를 침묵시킴

`LPExternalRequestsManager` 계약의 `requestMintWithPermit` 및 `requestBurnWithPermit` 메서드는 빈 절이 있는 try-catch 블록 내에서 ERC20 `permit` 메서드를 호출하여 알려진 permit 프론트러닝 공격 벡터를 완화합니다. 그러나 이 방식은 합법적인 기한 또는 잘못된 서명 오류조차도 침묵시키고 메서드가 요청을 진행합니다. 잠재적으로 존재하는 승인에 따라 요청의 후속 ERC20 전송이 성공하거나 성공하지 못할 수 있습니다. 이는 정의되지 않은 동작입니다.

이미 실행된 permit의 경우를 격리하고 다른 모든 시나리오에서는 각각의 오류와 함께 되돌리는 것이 좋습니다.

# [L-04] `getCurrentDebt`에서 레이트 모드(rate mode) 어설션 누락

`AaveV3TreasuryConnector` 계약의 `getCurrentDebt` 메서드는 `_rateMode != 1`인 경우 가변 부채를 반환하지만, 가변 부채는 `_rateMode == 2`인 경우에만 반환되어야 합니다. 이는 이 메서드에 의존하는 통합자에게 오해의 소지가 있을 수 있습니다.

우리는 계약의 다른 레이트 모드 종속 메서드인 `borrow` 및 `repay`의 동작과 일치하도록 `getCurrentDebt` 메서드에 `_assertRateMode` 호출을 추가할 것을 권장합니다.

# [L-05] 재무 커넥터 설정이 의도한 대로 작동하지 않을 수 있음

`Treasury` 계약의 `setLidoTreasuryConnector` 및 `setAaveTreasuryConnector` 메서드를 사용하면 관리자가 필요한 경우 언제든지 재무 커넥터 계약을 변경할 수 있습니다.
그러나 커넥터 계약은 순수한 커넥터가 아니라 그 자체로 재무이기 때문에 운영 중에는 거의 항상 실행 불가능합니다.

- `LidoTreasuryConnector` 계약은 요청된 인출의 `unstETH` NFT를 보유합니다.
- `AaveV3TreasuryConnector` 계약은 오픈 공급/차입 포지션의 `a/s/vTokens`를 보유합니다. 부채 토큰은 양도할 수 없으며 `emergencyWithdrawERC20`을 통해 다른 계약으로 마이그레이션할 수 없습니다.

결과적으로 모든 요청과 오픈 포지션을 정리하지 않고는 커넥터 계약을 변경하는 것이 불가능합니다.

우리는 `Treasury` 계약이 모든 종류의 커넥터 자산도 보유하고 커넥터 계약이 재무를 대신하여 행동하도록 할 것을 권장합니다.

# [L-06] `aaveBorrow`에서 불필요한 ERC20 승인

`Treasury.sol#L318-L321`

```solidity
} else {
    IERC20(_token).safeIncreaseAllowance(address(connector), _borrowAmount);
    connector.borrow(_token, _borrowAmount, _rateMode, aaveReferralCode);
}
```

`Treasury` 계약의 `aaveBorrow` 메서드에서 `AaveV3TreasuryConnector` 계약에 대한 ERC20 허용량이 증가합니다. 그러나 커넥터의 `borrow` 메서드는 재무에서 토큰을 가져오는 것이 아니라 재무로 전송합니다.
결과적으로 승인은 불필요하며 허용량 회계와 실제로 전송된 토큰 간의 불일치가 지속적으로 누적됩니다.

이 경우 `safeIncreaseAllowance` 호출을 제거하는 것이 좋습니다.

# [L-07] 악성 제공자가 SERVICE_ROLE을 괴롭힐 수 있음

LPExternalRequestsManager 계약에서 사용자는 다음 함수를 사용하여 민트 요청을 시작할 수 있습니다.

```solidity
    function requestMint(
        address _depositTokenAddress,
        uint256 _amount,
        uint256 _minMintAmount
    ) public onlyAllowedProviders allowedToken(_depositTokenAddress) whenNotPaused {
```

그 후 SERVICE_ROLE은 이러한 민트 요청을 승인하고 완료할 책임이 있습니다.

```solidity
    function completeMint(
        bytes32 _idempotencyKey,
        uint256 _id,
        uint256 _mintAmount
    ) external onlyRole(SERVICE_ROLE) mintRequestExist(_id) {
```

악성 제공자는 단일 대규모 요청 대신 여러 개의 작은 민트 요청을 제출하여 프로토콜을 괴롭힐 수 있습니다. 예를 들어 한 번에 100개의 토큰을 민트하도록 요청하는 대신 악성 제공자는 각각 10개의 토큰을 민트하도록 10개의 별도 요청을 제출할 수 있습니다. 이로 인해 SERVICE_ROLE에 불필요한 가스 비용이 부과됩니다. 이 위험을 완화하려면 최소 민트 금액을 시행하는 것을 고려하십시오.

# [L-08] 소각 흐름에서 타임락 구현 부족

문서에는 처리 단계가 소각 실행 전 48시간 동안 타임락되어야 한다고 명시되어 있지만, 코드 구현에는 타임락이없어 요청이 처리된 후 즉시 소각을 실행할 수 있습니다.

# [L-09] Lido 호출 전 건전성 검사 누락

`LidoTreasuryConnector#requestWithdrawals` 함수는 요청의 `totalAmount`가 `amouts` 배열의 모든 요소의 합계와 같은지, 그리고 모든 요소가 Lido의 최소 요청 크기보다 큰지 확인하지 못합니다.
`LidoTreasuryConnector#claimWithdrawals` 함수는 `requestIds` 배열이 Lido의 `claimWithdrawalsTo` 함수가 요구하는 대로 정렬되어 있는지 확인하지 못합니다.

# [L-10] 목록에서 제외된 지출자에 대한 허용량을 줄일 수 없음

`Treasury#decreaseAllowance` 함수에는 지출자가 화이트리스트에 있는지 확인하는 `onlyAllowedSpender` 수정자가 포함되어 있어 현재 목록에서 제외된 지출자에 대한 승인을 취소할 수 없습니다.

```solidity
File: Treasury.sol
207:     function decreaseAllowance(
208:         bytes32 _idempotencyKey,
209:         IERC20 _token,
210:         address _spender,
211:         uint256 _decreaseAmount
212:     ) external onlyRole(SERVICE_ROLE) idempotent(OperationType.DecreaseAllowance, _idempotencyKey) onlyAllowedSpender(_spender) whenNotPaused {
```

# [L-11] 자산 전송 중앙화 위험

프로토콜은 `DEFAULT_ADMIN_ROLE`이 손상되거나 악의적으로 행동하는 경우 위협이 되는 다음과 같은 자산 전송 위험에 노출되어 있습니다.

- 직접 전송 또는 허용량을 통한 `Treasury` 계약에서의 ETH 및 ERC20 임의 전송.
- `LidoTreasuryConnector` 계약에서의 ETH, ERC721 및 ERC20 임의 전송.
- `AaveV3TreasuryConnector` 계약에서의 ETH 및 ERC20 임의 전송.
- `LPExternalRequestsManager` 계약에서의 ERC20 임의 전송.

# [L-12] `requestBurn`에 최소 인출 금액 누락으로 제공자에게 손실 발생 가능

허용된 제공자가 `requestBurn` 메서드를 통해 주어진 담보 토큰에 대해 RLP를 소각하도록 요청할 때, 그들은 `completeBurns`를 호출하고 소각된 RLP에 대해 0이 아닌 `withdrawalCollateralAmount`를 임의로 결정할 수 있는 `SERVICE_ROLE`의 처분에 맡겨집니다.
결과적으로 제공자는 RLP에 대해 불충분한 담보를 받을 때 손실을 입으며 최소 금액을 시행할 방법이 없습니다.

`_minMintAmount` 매개변수가 있는 `requestMint`와 유사하게, 후속 소각 절차 중에 확인해야 할 `_minWithdrawAmount`를 `requestBurn` 메서드에 추가할 것을 제안합니다.

# [L-13] `processBurns`가 `cancelBurn`으로 프론트러닝되어 RLP 일괄 소각 실패 유발 가능

`LPExternalRequestsManager.sol#L409-L423`

```solidity
function _processBurn(
    bytes32 _idempotencyKey,
    uint256 _id,
    uint256 _epochId
) internal burnRequestExist(_id) {
    BurnRequest storage request = burnRequests[_id];
    if (BurnRequestState.CREATED != request.state
        && BurnRequestState.PARTIALLY_COMPLETED != request.state) {
        revert IllegalState(uint256(request.state));
    }

    request.state = BurnRequestState.PROCESSING;
    burnRequestEpochs[request.id][_epochId] = true;

    emit BurnRequestProcessing(_idempotencyKey, _id, _epochId);
}
```

`LPExternalRequestsManager.sol#L329-L344`

```solidity
function cancelBurn(uint256 _id) external burnRequestExist(_id) {
    BurnRequest storage request = burnRequests[_id];
    _assertAddress(request.provider, msg.sender);
    if (BurnRequestState.CREATED != request.state
        && BurnRequestState.PARTIALLY_COMPLETED != request.state) {
        revert IllegalState(uint256(request.state));
    }

    uint256 remainingAmount = request.requestedToBurnAmount
        - request.burnedAmount;
    _assertAmount(remainingAmount);

    request.state = BurnRequestState.CANCELLED;
    IERC20 issueToken = IERC20(ISSUE_TOKEN_ADDRESS);
    issueToken.safeTransfer(request.provider, remainingAmount);

    emit BurnRequestCancelled(_id);
}
```

에포크의 선택된 RLP 배치를 처리하여 소각하는 `LPExternalRequestsManager` 계약의 `processBurns` 메서드는 이후 `_processBurn` 메서드를 호출하는데, 이 메서드는 소각 요청 중 하나라도 잘못된 상태(예: `CANCELLED`)이면 되돌립니다.

그러나 소각 요청이 배치에 포함된 허용된 제공자는 `cancelBurn`을 통해 소각 요청을 취소하여 배치 처리를 악의적으로/우발적으로 프론트러닝할 수 있습니다.
결과적으로 보류 중인 `processBurns` 트랜잭션이 되돌려지며 취소된 소각 요청 없이 다시 시도해야 합니다.

`processBurns` 또는 `_processBurn` 메서드에서 취소된 소각 요청을 건너뛰고 `burnEpochs` 저장소 매핑에 `id`를 추가하지 않는 것이 좋습니다. `requestMintWithPermit` 및 `requestBurnWithPermit` 메서드와 유사하게 코드는 프론트러닝을 허용합니다.

# [L-14] 코드가 수수료 부과 토큰(fee on transfer tokens)과 작동하지 않음

코드는 현재 ERC20 전송 함수가 지정된 정확한 금액을 이동할 것이라고 가정합니다. 이 가정은 전송에 수수료를 부과하는 토큰의 경우 잘못된 것이며 부정확한 계산으로 이어집니다. 예를 들어 `requestMint()` 함수에서 `LPExternalRequestsManager`는 전송 수수료를 고려하지 않고 `MintRequest.amount`를 전송된 금액으로 기록합니다.

```solidity
        IERC20(_depositTokenAddress).safeTransferFrom(msg.sender, address(this), _amount);
        MintRequest memory request = _addMintRequest(_depositTokenAddress, _amount, _minMintAmount);
```

보시다시피 `LPExternalRequestsManager`는 사용자로부터 전체 `_amount`를 전송한다고 잘못 가정합니다. 결과적으로 `LPExternalRequestsManager`가 전송 수수료로 인해 예상 금액보다 적게 받았기 때문에 재무 주소로 원래 금액을 전송하려고 시도하면 되돌려지므로 민팅 프로세스가 실패합니다.

전송 전후의 잔액을 확인한 다음 그에 따라 회계를 업데이트하십시오. 이 접근 방식의 좋은 예는 [Uniswap Router02](https://etherscan.io/address/0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D)에 구현되어 있습니다.
