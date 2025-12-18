# 소개
Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의해 주세요.

# 면책 조항
스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론
**spaceandtimelabs/sxt-token**, **spaceandtimelabs/sxt-node-op-contracts**, **spaceandtimelabs/sxt-zkpay-contracts** 저장소에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# SXT 소개
SXT는 머클 증명(Merkle proofs)을 기반으로 한 배포 메커니즘을 가진 토큰으로, 사용자가 가볍고 검증 가능한 증명을 통해 토큰을 청구할 수 있도록 합니다. 범위에는 지불(광범위한 가스 최적화를 통한 자금 이체 처리 엔진) 및 스테이킹(검증인 지명, 임계값 관리 및 크로스 체인 메시지 방출을 위해 SXT 토큰 스테이킹)을 위한 모듈도 포함됩니다.

# 리스크 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 훔치거나 잃을 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 있는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 너무 비현실적이거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (배포 전이라면 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_:
- [73462f93d03a0cae29129db62b75615f96639c93](https://github.com/spaceandtimelabs/sxt-token/tree/73462f93d03a0cae29129db62b75615f96639c93)
- [de770e8107d3ec7a8a93cd4fad08c5806f49d68d](https://github.com/spaceandtimelabs/sxt-node-op-contracts/tree/de770e8107d3ec7a8a93cd4fad08c5806f49d68d)
- [cc275ea67004ca5073be8613a20a548d06dc733d](https://github.com/spaceandtimelabs/sxt-zkpay-contracts/tree/cc275ea67004ca5073be8613a20a548d06dc733d)

_수정 검토 커밋 해시_:
- [ffa98911b034c8b976a3830ff93938c7710efce7](https://github.com/spaceandtimelabs/sxt-token/tree/ffa98911b034c8b976a3830ff93938c7710efce7)
- [2a4483339d59d138fce9504862899691db073aa3](https://github.com/spaceandtimelabs/sxt-node-op-contracts/tree/2a4483339d59d138fce9504862899691db073aa3)
- [cc275ea67004ca5073be8613a20a548d06dc733d](https://github.com/spaceandtimelabs/sxt-zkpay-contracts/tree/f7e73375298639303f162fdc055235c4c423ee6d)

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `SXTDeployer` 
- `SpaceAndTime` 
- `SXTDistributorWithDeadline` 
- `SXTTokenDistributor` 
- `TokenSplitter` 
- `SubstrateSignatureValidator` 
- `StakingPool` 
- `Staking` 
- `SXTChainMessaging` 
- `ZKPay` 
- `ZKPayStorage` 
- `AssetManagement` 
- `Constants` 
- `QueryLogic` 
- `Utils` 

# 발견 사항

# [C-01] 한 서명자로부터 중복 서명 수락

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`SubstrateSignatureValidator`의 `validateMessage` 함수는 메시지가 증명자(attestor) 임계값에 의해 서명되었는지 확인하도록 설계되었습니다. 그러나 이 함수는 동일한 증명자로부터의 중복 서명을 고려하지 않습니다. 단순히 서명이 알려진 증명자에 매핑되는 횟수만 계산하며, 서명자가 이미 계산되었는지 여부는 상관하지 않습니다. 함수에 중복 제거 로직이 없으므로, 동일한 유효한 서명을 여러 번 재생하여 유효 서명 수를 인위적으로 부풀릴 수 있습니다.

```solidity
        for (uint256 i = 0; i < signaturesLength; ++i) {
            address recoveredAddress = ecrecover(message, v[i], r[i], s[i]);

            while (attestorIndex < attestorsLength && _attestors[attestorIndex] < recoveredAddress) {
                ++attestorIndex;
            }

            if (attestorIndex < attestorsLength && _attestors[attestorIndex] == recoveredAddress) {
                ++validSignaturesCount;
            }

            if (validSignaturesCount == _threshold) return true;
        }
```

결과적으로 유효한 증명자의 서명이 여러 번 제출되면(즉, 동일한 v, r, s), 임계값을 충족하는 것으로 여러 번 계산되지만 실제로는 나머지 증명자들은 서명하지 않았을 수 있습니다.

간단한 PoC를 위해 `SubstrateSignatureValidatorTest`의 다음 함수를 수정하고 `forge test --mt testValidateMessage`를 실행해 보세요. 보시다시피 임계값은 `2`이지만 첫 번째 증명자만 서명했고 그의 서명이 임계값을 충족하기 위해 두 번 반복되었습니다.

```solidity
    function setUp() public {
        message = keccak256("test");

        attestors = new address[](2);
        attestors[0] = ecrecover(message, 27, bytes32(uint256(0x1)), bytes32(uint256(0x3)));
        attestors[1] = ecrecover(message, 27, bytes32(uint256(0x2)), bytes32(uint256(0x4)));

        validator = new SubstrateSignatureValidator(attestors, 2);
    }

    function testValidateMessage() public view {
        bytes32[] memory r = new bytes32[](2);
        r[0] = bytes32(uint256(0x1));
        r[1] = bytes32(uint256(0x1));

        bytes32[] memory s = new bytes32[](2);
        s[0] = bytes32(uint256(0x3));
        s[1] = bytes32(uint256(0x3));

        uint8[] memory v = new uint8[](2);
        v[0] = 27;
        v[1] = 27;

        assertEq(validator.validateMessage(message, r, s, v), true);
    }
```

## 권고 사항

`validateMessage`의 루프 내에 메모리 배열을 구현하고 복구된 서명자가 이미 확인되었는지 검사하는 것을 고려하세요.

# [C-02] `ZKPay.sol`에서 ETH 탈취

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

공격자는 ERC20 토큰과 함께 사용되어야 하는 `ZKPay::query()`를 호출하면서 `NATIVE_ADDRESS`를 전달하고 많은 양의 ETH를 지정할 수 있습니다. 그러면 `handleQueryPayment`는 이 금액이 `msg.value`에서 왔다고 가정하므로, 발신자가 실제로 해당 ETH를 컨트랙트로 보냈다고 생각하게 됩니다.

```solidity
    function query(address asset, uint248 amount, QueryLogic.QueryRequest calldata queryRequest)
        external
        returns (bytes32 queryHash)
    {
        return _query(asset, amount, queryRequest);
    }

    function handleQueryPayment(
        mapping(address asset => PaymentAsset) storage _assets,
        address assetAddress,
        uint248 tokenAmount
    ) internal returns (uint248 actualAmountReceived, uint248 amountInUSD) {
        if (!isSupported(_assets, assetAddress, PaymentType.Query)) {
            revert AssetIsNotSupportedForThisMethod();
        }

        if (assetAddress == NATIVE_ADDRESS) {
@>            actualAmountReceived = tokenAmount;
        } else {
            uint256 balanceBefore = IERC20(assetAddress).balanceOf(address(this));
            IERC20(assetAddress).safeTransferFrom(msg.sender, address(this), tokenAmount);
            uint256 balanceAfter = IERC20(assetAddress).balanceOf(address(this));

            actualAmountReceived = uint248(balanceAfter - balanceBefore);
        }

        amountInUSD = convertToUsd(_assets, assetAddress, actualAmountReceived);
    }
```
동일한 트랜잭션에서 공격자는 자신의 쿼리를 취소하고 전체 ETH 금액을 인출합니다. 그들은 단 1 wei도 입금하지 않았지만, 많은 ETH 금액에 대한 `QueryPayment`가 생성되었고 이제 이를 인출할 수 있게 됩니다.

## 권고 사항

간단한 `ZKPay::query` 함수가 `asset == NATIVE_ADDRESS`로 호출되면 되돌리는(revert) 것을 고려하세요.

```diff
    function query(address asset, uint248 amount, QueryLogic.QueryRequest calldata queryRequest)
        external
        returns (bytes32 queryHash)
    {
+       require(asset != NATIVE_ADDRESS);
        return _query(asset, amount, queryRequest);
    }
```

# [M-01] 이행자(Fulfillers)가 `EIP-150`을 악용하여 `zkPayCallback` 우회

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`fulfillQuery`는 실행이 끝날 무렵 `ZKPayClient`의 콜백 함수를 호출하며, 이는 try-catch 블록 내에서 수행됩니다:

```solidity
        try IZKPayClient(queryRequest.callbackClientContractAddress).zkPayCallback(
            queryHash, results, queryRequest.callbackData
        ) {
            emit CallbackSucceeded(queryHash, queryRequest.callbackClientContractAddress);
        } catch {
            emit CallbackFailed(queryHash, queryRequest.callbackClientContractAddress);
        }
```

[EIP150](https://eips.ethereum.org/EIPS/eip-150)에 따르면, 호출에는 가스의 63/64가 할당되며, 이 호출이 가스가 부족하면 가스의 전체 63/64가 소각됩니다. 이는 공격 벡터를 생성합니다: 사용자의 `zkPayCallback`이 상당한 양의 가스를 소비하는 경우, 이행자는 `zkPayCallback`이 가스 부족으로 실행되지 않도록 딱 충분한 가스만 지정하면서, 남은 1/64 가스가 `settleQueryPayment`를 완료하기에 충분하도록 할 수 있습니다. 이를 통해 악의적인 이행자는 `zkPayCallback`을 우회할 수 있습니다.

## 권고 사항

콜백이 적은 가스를 소비한다면 변경이 필요하지 않습니다. 콜백이 많은 가스를 소비한다면, 가능한 완화책은 사용자가 `zkPayCallback`을 실행하기 전에 최소 `gasleft()`를 지정할 수 있도록 하는 것입니다.

# [M-02] `fulfillQuery()`를 프론트러닝하여 쿼리 취소

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명
ZKPay에서 사용자는 하나의 쿼리 요청을 보내고, 관련 상인은 이 이벤트를 모니터링하며 관련 오라클이 `fulfillQuery`를 통해 이 요청을 이행하고 관련 수수료를 받습니다.

여기서 문제는 쿼리의 소유자가 언제든지 이 요청을 취소할 수 있다는 것입니다. 스폰서의 정보에 따르면, 이 컨트랙트는 이더리움에 배포될 예정입니다. 이더리움에서 악의적인 사용자는 `fulfillQuery` 트랜잭션을 모니터링하고 이 트랜잭션을 프론트러닝하여 이 쿼리를 취소할 수 있습니다. 이 프론트러닝 후, 악의적인 사용자는 쿼리에 대한 지불을 돌려받을 수 있으며 또한 온체인 데이터를 모니터링하여 쿼리 결과를 얻을 수도 있습니다.

```solidity
    function cancelQuery(bytes32 queryHash) external nonReentrant {
        QueryLogic.QueryPayment storage payment = _queryPayments[queryHash];
        if (payment.source != msg.sender) revert OnlyQuerySourceCanCancel();

        QueryLogic.cancelQuery(_queryPayments, _queryNonces, queryHash);
    }

    function fulfillQuery(bytes32 queryHash, QueryLogic.QueryRequest calldata queryRequest, bytes calldata queryResult)
        external
        nonReentrant
        returns (uint248 gasUsed)
    {
    }
```

## 권고 사항
상인이 이 쿼리를 모니터링하면, 상인은 이 쿼리를 수락하여 잠그는 트랜잭션을 트리거할 수 있습니다. 그러면 쿼리가 만료되기 전까지는 아무도 이를 취소할 수 없습니다.

# [M-03] 공격자가 이행자 계정으로의 환불 차단 가능

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

현재 `fulfillQuery`는 환불 가스를 쿼리 소스(source)에게 지불합니다. 환불이 실패하면 전체 흐름이 되돌려집니다. 이는 악의적인 사용자가 쿼리 완료를 항상 거부할 수 있음을 의미합니다:

```solidity
QueryLogic.sol::L243
            if (refundAmount > 0) {
                // slither-disable-next-line low-level-calls
                (bool refundSuccess,) = payment.source.call{value: refundAmount}("");
                if (!refundSuccess) revert NativeRefundFailed();
            }
```

공격자는 다음을 수행할 수 있습니다:

0. 공격자는 receive 함수가 되돌릴지 여부를 자유롭게 조정할 수 있는 컨트랙트를 미리 배포하고, 처음에는 되돌리지 않도록 설정합니다.

1. 공격자는 EVM ZKPay 컨트랙트에 0이 아닌 요청 수수료로 유효한 쿼리를 게시합니다.

2. 오프체인 리졸버는 유효한 요청을 찾고 쿼리를 채우려고 시도합니다.

3. 공격자는 컨트랙트를 전환하여 모든 `receive()`/`fallback()` 호출을 되돌리도록 합니다.

4. 리졸버의 트랜잭션은 실패합니다.

## 권고 사항

가능한 완화책은 원본 토큰을 래핑하여 사용자에게 보내는 것입니다.

# [M-04] `claimUnstake()` 후 `stakingDelayStartTime`이 초기화되지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`stakingDelayStartTime`은 `CollaborativeStaking::initiateUnstake()`가 호출될 때 초기화되지만, 해당 언스테이킹 프로세스가 `claimUnstake()`를 통해 완료될 때 절대 지워지거나 재설정되지 않습니다.

```solidity
    function claimUnstake() external onlyRole(STAKER_ROLE) {
        emit ClaimUnstake();

        IStaking(STAKING_ADDRESS).claimUnstake();
    }
```

결과적으로 사용자가 다음을 수행한 후에도:
1. 자금 스테이킹,
2. 언스테이킹 시작,
3. 언스테이킹된 토큰 청구 성공,

여전히 다시 스테이킹하기 전에 스테이킹 지연을 겪어야 합니다. 이는 `withStakingDelay` 수정자가 `stakingDelayStartTime`을 기반으로 지연을 강제하는데, 이 값이 설정된 상태로 남아 있기 때문입니다.

이 취약점은 `STAKING_DELAY_LENGTH`가 `Staking.sol`에 정의된 `UNSTAKING_UNBONDING_PERIOD`보다 길 때 존재합니다. 이 경우 사용자는 언스테이킹 프로세스가 완전히 완료된 후에도 추가적인 `STAKING_DELAY_LENGTH`를 기다려야 하며, 이는 불필요하고 스테이킹 워크플로를 방해하며 스테이킹 참여를 지연시킬 수 있습니다.

## 권고 사항

`claimUnstake()` 함수에서 `stakingDelayStartTime`을 `0`으로 재설정하는 것을 고려하세요.

# [L-01] 가스 제한 보호 장치가 없어 가스 고갈 가능

사용자는 여기서 가스 제한을 모두 소비할 수 있습니다.
```solidity
File: ZKPay.sol#fulfillQuery()

         try IZKPayClient(queryRequest.callbackClientContractAddress).zkPayCallback(
            queryHash, results, queryRequest.callbackData
        ) {

```
다시 말해, 오라클이 쿼리를 이행하면 오라클이 모든 가스 제한을 소비하는 것을 막을 확인 절차가 없습니다.
콜백 컨트랙트는 어떤 컨트랙트든 될 수 있습니다;
`ZKPay.sol#fulfillQuery()` 함수는 `QueryLogic.sol#settleQueryPayment()`의 확인이 지불된 금액과 실제 `gasUsed`가 다른 어떤 금액이라도 허용하기 때문에 되돌려지지 않습니다.

```solidity
File: QueryLogic.sol#settleQueryPayment()

            if (payoutAmount > payment.amount) {
            payoutAmount = payment.amount;
        }
```
이를 해결하기 위해 허용 가능한 차이를 추가하고 이를 확인할 수 있습니다.

# [L-02] 초기화되지 않은 값 사용

`SubstrateSignatureValidator` 컨트랙트의 `constructor`에서 `_updateAttestors(attestors)` 호출은 `_threshold` 값이 `_updateThreshold(threshold)`를 통해 설정되기 전에 이루어집니다. 그러나 `_updateAttestors`는 `attestors.length >= _threshold`를 확인하며, 이는 아직 초기화되지 않은 상태 변수에 의존합니다.

```solidity
    constructor(address[] memory attestors, uint16 threshold) Ownable(msg.sender) {
        _updateAttestors(attestors);
        _updateThreshold(threshold);
    }
```
보시다시피 `_updateAttestors`가 `_updateThreshold`보다 먼저 호출되고 있으며, `_updateAttestors`는 초기화되지 않은 `_threshold`를 사용하여 확인을 수행합니다.

```solidity
    function _updateAttestors(address[] memory attestors) internal {
        // ...
@>        if (attestors.length < _threshold) revert AttestorsLengthLessThanThreshold();

        // ...
    }
```
이 문제를 완화하기 위해 `_updateAttestors` 전에 `_updateThreshold`를 호출하는 것을 고려하세요.

# [L-03] 일관성 없는 타임아웃 확인

ZKPay에서 사용자는 하나의 타임아웃으로 일부 요청을 쿼리합니다. 타임아웃이 만료되기 전에 상인 오라클은 이 요청을 해결하고 `fulfillQuery`를 통해 이를 이행할 수 있습니다. 타임아웃이 만료되면 상인 오라클은 만료된 요청을 이행할 수 없습니다. 그리고 누구나 요청 소유자가 이 요청을 취소하도록 도울 수 있습니다.

여기서 문제는 `timeout`에 대한 명확한 정의가 없다는 것입니다. 이는 block.timestamp가 `queryRequest.timeout`과 같을 때 상인이 이 쿼리를 이행할 수 있게 합니다. 그러나 악의적인 사용자는 이 쿼리를 취소하기 위해 프론트러닝할 수 있습니다. 왜냐하면 block.timestamp가 `queryRequest.timeout`과 같을 때 누구든지 만료된 쿼리를 취소할 수 있기 때문입니다.

```solidity
    function cancelExpiredQuery(bytes32 queryHash, QueryLogic.QueryRequest calldata queryRequest)
        external
        nonReentrant
    {
        _validateQueryRequest(queryHash, queryRequest);
        if (block.timestamp < queryRequest.timeout || queryRequest.timeout == 0) {
            revert QueryHasNotExpired();
        }

        QueryLogic.cancelQuery(_queryPayments, _queryNonces, queryHash);
    }
    function fulfillQuery(bytes32 queryHash, QueryLogic.QueryRequest calldata queryRequest, bytes calldata queryResult)
        external
        nonReentrant
        returns (uint248 gasUsed)
    {
        if (block.timestamp > queryRequest.timeout && queryRequest.timeout != 0) {
            revert QueryTimeout();
        }
    }
```

`cancelExpiredQuery`와 `fulfillQuery`에 대해 엣지 케이스가 작동할 수 있는지 확인하세요. 함수 `fulfillQuery`와 `cancelExpiredQuery`는 동일한 블록에서 실행되어서는 안 됩니다.

# [L-04] Chainlink의 최대/최소 가격 확인 누락

`_validatePriceFeed` 함수에서 우리는 Chainlink 어그리게이터(aggregator)로부터 가격을 가져옵니다. Chainlink에는 최소/최대 답변이 있습니다. 일부 엣지 케이스에서는 반환된 값이 실제 가격이 아닐 수 있습니다. 예를 들어 실제 가격이 최소 답변보다 작을 경우 최소 답변을 반환합니다.

```solidity
    function _validatePriceFeed(PaymentAsset memory paymentAsset) internal view {
        if (paymentAsset.priceFeed == NATIVE_ADDRESS || !Utils.isContract(paymentAsset.priceFeed)) {
            revert InvalidPriceFeed();
        }
        (, int256 answer,, uint256 updatedAt,) = AggregatorV3Interface(paymentAsset.priceFeed).latestRoundData();

        if (answer == 0) {
            revert InvalidPriceFeed();
        }

        // slither-disable-next-line timestamp
        if (updatedAt + paymentAsset.stalePriceThresholdInSeconds < block.timestamp) {
            revert InvalidPriceFeed();
        }
    }
```

https://docs.chain.link/data-feeds#check-the-latest-answer-against-reasonable-limits 를 확인하여, 범위 내 토큰에 대해 합리적인 최소/최대 값을 설정해야 합니다. 반환 가격이 설정된 최소/최대 값에 도달하면 오래된 가격 사용을 피하기 위해 여기서 되돌려야 합니다.

# [L-05] 가격에 대한 L2 시퀀서 가동 시간 확인 누락

`_validatePriceFeed` 함수에서 우리는 Chainlink의 피드로부터 가격 피드를 가져옵니다. Layer2에서 가격을 가져오려고 할 때, 시퀀서의 가동 시간을 확인하고 유예 기간(grace period)을 지난 후 가격을 가져오는 것이 좋습니다. (https://docs.chain.link/data-feeds/l2-sequencer-feeds)

스폰서의 입력에 따르면, 컨트랙트는 Base 체인에 배포될 수 있습니다. 따라서 컨트랙트가 Base 체인에 배포된다면 시퀀서의 가동 시간을 확인하는 것을 고려해야 합니다.

```solidity
    function _validateSxtFulfillUnstake(
        address staker,
        uint248 amount,
        uint64 sxtBlockNumber,
        bytes32[] calldata proof,
        bytes32[] calldata r,
        bytes32[] calldata s,
        uint8[] calldata v
    ) internal view returns (bool isValid) {
        bytes32 leaf = keccak256(bytes.concat(keccak256(abi.encodePacked(uint256(uint160(staker)), amount))));
        bytes32 rootHash = MerkleProof.processProof(proof, leaf);
        bytes32 messageHash =
            keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n36", rootHash, sxtBlockNumber, block.chainid));

        isValid =
            ISubstrateSignatureValidator(SUBSTRATE_SIGNATURE_VALIDATOR_ADDRESS).validateMessage(messageHash, r, s, v);
    }

```

배포된 체인이 Base 체인인 경우 시퀀서의 가동 시간 확인을 추가하세요.

# [L-06] `EIP-191` 표준 미준수

`_validateSxtFulfillUnstake` 함수에서 우리는 ERC-191에 따라 메시지 해시를 생성합니다. https://eips.ethereum.org/EIPS/eip-191 에 따르면 제안된 형식은 다음과 같습니다:
```
"\x19Ethereum Signed Message:\n" + len(message).
```

`_validateSxtFulfillUnstake` 함수에서 우리는 36을 고정된 메시지 길이로 사용합니다. 그러나 `bytes32 rootHash`, `uint64 sxtBlockNumber`, `block.chainid(uint256)` 이 세 매개변수를 확인할 때 총 바이트 길이는 72여야 합니다.

이는 메시지 해시가 EIP-191 표준을 준수하지 않게 만듭니다.

```solidity
    function _validateSxtFulfillUnstake(
        address staker,
        uint248 amount,
        uint64 sxtBlockNumber,
        bytes32[] calldata proof,
        bytes32[] calldata r,
        bytes32[] calldata s,
        uint8[] calldata v
    ) internal view returns (bool isValid) {
        bytes32 leaf = keccak256(bytes.concat(keccak256(abi.encodePacked(uint256(uint160(staker)), amount))));
        bytes32 rootHash = MerkleProof.processProof(proof, leaf);
        bytes32 messageHash =
            keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n36", rootHash, sxtBlockNumber, block.chainid));

        isValid =
            ISubstrateSignatureValidator(SUBSTRATE_SIGNATURE_VALIDATOR_ADDRESS).validateMessage(messageHash, r, s, v);
    }

```

메시지 해시에 대한 올바른 바이트 길이를 계산하세요.

# [L-07] 사용자가 보상 청구에 실패할 수 있음

SXTDistributorWithDeadline에서 사용자는 머클 증명을 통해 SXT 토큰을 청구할 수 있습니다. 사용자가 마감일까지 보상을 청구하지 않으면 소유자는 이 컨트랙트에 남은 SXT 토큰을 다시 인출할 것입니다.

여기서 문제는 `claim`과 `withdraw` 함수에 시간 충돌이 있다는 것입니다. `claim` 함수에서 사용자는 block.timestamp가 `END_TIME`과 같을 때 예상 보상을 청구할 수 있습니다. 그러나 `withdraw` 함수에서 관리자는 block.timestamp가 `END_TIME`과 같을 때 남은 SXT 토큰을 직접 인출할 수 있습니다.

예를 들어:
1. END_TIME = 타임스탬프 X.
2. 타임스탬프 X에서 소유자는 `withdraw` 함수를 통해 남은 SXT 토큰을 인출합니다.
3. 타임스탬프 X에서 사용자가 보상을 청구합니다. 사용자의 청구는 `block.timestamp > END_TIME` 확인을 적용하지만, 컨트랙트에 남은 자금이 없기 때문에 사용자의 청구는 실패합니다.

```solidity
    function claim(uint256 index, address account, uint256 amount, bytes32[] calldata merkleProof) public override {
        if (block.timestamp > END_TIME) revert ClaimWindowFinished();
        super.claim(index, account, amount, merkleProof);
    }

    function withdraw() external onlyOwner {
        if (block.timestamp < END_TIME) revert NoWithdrawDuringClaim();

        address tokenAddress = this.token();

        uint256 balance = IERC20(tokenAddress).balanceOf(address(this));
        IERC20(tokenAddress).safeTransfer(msg.sender, balance);
    }
```

`END_TIME`에 대해 명확한 정의를 내리세요. 동일한 블록에서 `claim`과 `withdraw`를 트리거해서는 안 됩니다.

# [L-08] 이행자에 대한 높은 가스 가격 인센티브로 인한 가치 누출

`settleQueryPayment()`는 이행자가 사용한 가스 가격을 얻기 위해 `tx.gasprice`를 사용합니다. 이는 두 가지 가스 가격으로 구성됩니다:

- 시스템에 의해 설정된 기본 수수료(base fee).
- 채굴자에게 팁으로 주고 사용자가 정의한 우선순위 수수료(priority fee).

이 가스 가격은 사용자에게 환불할 `refundAmount`를 계산하는 데 사용됩니다:

```solidity
        uint248 usedGasInWei = uint248(gasUsed * tx.gasprice);
        uint248 usedGasInPaymentToken = AssetManagement.convertNativeToToken(_assets, payment.asset, usedGasInWei);

        (address payoutAddress, uint248 fee) = ICustomLogic(customLogicContractAddress).getPayoutAddressAndFee();
        uint248 feeInPaymentToken = AssetManagement.convertUsdToToken(_assets, payment.asset, fee);
        payoutAmount = usedGasInPaymentToken + feeInPaymentToken;

        if (payoutAmount > payment.amount) {
            payoutAmount = payment.amount;
        }

        refundAmount = payment.amount - payoutAmount;
```

현재 `fulfillQuery()`에는 이행자가 사용하는 가스 가격에 대한 제한이 없습니다. 작업 실행에 필요한 수수료가 가변적이고 남은 수수료가 많다면, 이행자는 항상 매우 높은 우선순위 수수료를 선택하여 트랜잭션을 더 빨리 포함시키는 동시에 환불 금액을 줄일 수 있습니다. 이는 사용자에게 반환되어야 할 환불을 크게 줄여 전체 시스템에 큰 가치 누출을 초래합니다.

가능한 해결책 중 하나는 사용자가 최대 가스 가격을 지정할 수 있도록 하는 것입니다.

# [L-09] `MIN_STAKING_AMOUNT`가 너무 낮음

`Staking::stake()` 호출 중, SXT 노드에 알리기 위해 `Staked` 이벤트가 방출됩니다. 스테이킹 컨트랙트는 사용자가 SXT 체인에 스팸을 보내기 위해 극도로 적은 스테이킹을 하는 것을 방지하기 위해 `MIN_STAKING_AMOUNT`를 정의합니다. 그러나 SXT 토큰은 18 소수점을 가지고 있지만, 현재 `MIN_STAKING_AMOUNT`는 100으로만 설정되어 있어 악의적인 사용자가 스팸을 보내는 것을 막기에 충분하지 않습니다.

`MIN_STAKING_AMOUNT`를 100e18과 같은 값으로 늘리세요.

# [L-10] `msg.data` 길이 확인 누락

ZKPay에서 상인 오라클은 `fulfillQuery`를 통해 쿼리를 완료하고 관련 수수료를 받습니다. 수수료에는 `getPayoutAddressAndFee`의 쿼리 해결 수수료와 관련 트랜잭션의 가스 수수료가 포함됩니다.

가스 계산은 `gasUsed = uint248(initialGas - gasleft() + (16 * msg.data.length) + ACCOUNTING_FIXED_GAS_OVERHEAD);` 계산을 기반으로 합니다. 일반적인 경우, `msg.data`에는 `bytes32 queryHash, QueryLogic.QueryRequest calldata queryRequest, bytes calldata queryResult`에 대한 예상 매개변수가 포함됩니다. 하나의 정상적인 `msg.data`의 길이는 특정 `bytes32 queryHash, QueryLogic.QueryRequest calldata queryRequest, bytes calldata queryResult`를 기반으로 평가될 수 있습니다.

여기서 문제는 악의적인 사용자가 calldata를 조작하여 0 값으로 패딩을 확장할 수 있다는 것입니다. 이로 인해 `msg.data`의 길이가 예상되는 `msg.data`의 길이보다 훨씬 커지게 됩니다.

`fulfillQuery` 함수는 사용자가 신뢰하는 상인 오라클에 의해 트리거되어야 합니다. 그러나 이더리움에서 사용자는 메모리 풀을 모니터링하고 이 `fulfillQuery`를 프론트러닝할 수 있습니다.

공격자가 `msg.data`를 0x00으로 확장하도록 조작하면, 공격자가 사용하는 가스 사용량도 증가합니다. 그러나 https://eips.ethereum.org/EIPS/eip-2028 에서 0이 아닌 바이트의 가스는 16이고 0 바이트 가스는 4라는 점에 유의해야 합니다. 따라서 공격자는 4 가스를 소비하지만, 쿼리 요청자는 확장된 부분에 대해 16 가스를 지불해야 합니다.

```solidity
    function fulfillQuery(bytes32 queryHash, QueryLogic.QueryRequest calldata queryRequest, bytes calldata queryResult)
        external
        nonReentrant
        returns (uint248 gasUsed)
    {
        gasUsed = uint248(initialGas - gasleft() + (16 * msg.data.length) + ACCOUNTING_FIXED_GAS_OVERHEAD);
    }
```

`zkPayCallback` 함수에 대한 관련 가스 제한을 추가하세요.

# [L-11] `fulfillmentTxGasLimit` 누락

ZKPay에서 사용자는 하나의 쿼리 요청을 보냅니다. 이 쿼리 요청에서 사용자는 콜백에 사용할 최대 가스 양인 `fulfillmentTxGasLimit`를 정의합니다. 이것은 하나의 중요한 매개변수입니다. 상인 오라클은 오라클이 소비해야 할 총 가스 소비량을 평가합니다. 오라클은 사용자의 쿼리 지불이 `fulfillQuery`에 대한 가스 소비 값과 실행 수수료보다 높을 것으로 예상합니다.

여기서 문제는 `fulfillQuery`를 트리거할 때 `zkPayCallback`에 대한 관련 가스 제한이 없다는 것입니다. 악의적인 사용자는 `fulfillmentTxGasLimit`보다 훨씬 많은 가스를 소비할 수 있습니다. 이는 상인의 이익을 크게 감소시킬 것입니다.

예를 들어:
1. 상인 A의 쿼리 수수료는 2U입니다.
2. 앨리스는 상인 A에게 하나의 쿼리를 요청하고 `fulfillmentTxGasLimit`는 1000입니다. 앨리스는 2.2U를 지불합니다. 2U는 쿼리 수수료이고 0.2U는 가스 수수료를 충당하는 데 사용됩니다.
3. 상인 A는 이 이벤트를 확인하고 `fulfillmentTxGasLimit`에 따라 가스 소비를 평가하며, `fulfillQuery`에 대한 총 가스가 0.2U로 충당될 것이라고 생각합니다. 상인 A의 오라클은 이 쿼리를 해결하고 이행하려고 시도합니다.
4. 앨리스의 `zkPayCallback`에서 우리는 많은 가스를 소비할 수 있습니다. 그러면 상인의 이익은 크게 떨어질 것입니다. 왜냐하면 우리는 0.2U가 가스를 충당할 것이라고 예상하기 때문입니다.

여기서 문제는 쿼리의 소유자가 언제든지 이 요청을 취소할 수 있다는 것입니다. 스폰서의 정보에 따르면, 이 컨트랙트는 이더리움에 배포될 예정입니다. 이더리움에서 악의적인 사용자는 `fulfillQuery` 트랜잭션을 모니터링하고 이 트랜잭션을 프론트러닝하여 이 쿼리를 취소할 수 있습니다. 이 프론트러닝 후, 악의적인 사용자는 쿼리에 대한 지불을 돌려받을 수 있으며 또한 온체인 데이터를 모니터링하여 쿼리 결과를 얻을 수도 있습니다.

```solidity
    function fulfillQuery(bytes32 queryHash, QueryLogic.QueryRequest calldata queryRequest, bytes calldata queryResult)
        external
        nonReentrant
        returns (uint248 gasUsed)
    {
        try IZKPayClient(queryRequest.callbackClientContractAddress).zkPayCallback(
            queryHash, results, queryRequest.callbackData
        ) {
            emit CallbackSucceeded(queryHash, queryRequest.callbackClientContractAddress);
        } catch {
            emit CallbackFailed(queryHash, queryRequest.callbackClientContractAddress);
        }
    }
```

`zkPayCallback` 함수에 대한 관련 가스 제한을 추가하세요.

# [L-12] `StakingPool.sol`에서의 재전송(Replay) 공격

`StakingPool.sol` 컨트랙트는 여러 `Staking.sol` 컨트랙트(예: SXT 및 USDC)를 처리하도록 설계되었습니다. 소유자가 `StakingPool.sol#addStakingContract()`를 사용할 수 있기 때문입니다.

사용자가 `Staking.sol#claimUnstake()`를 호출하면 SXT 체인은 `Staking.sol#sxtFulfillUnstake()`를 트리거하여 언스테이크 요청을 이행하기 위해 콜백을 수행합니다. 이는 `_validateSxtFulfillUnstake()`를 서브 호출(sub-calling)하여 서명을 검증합니다.

```solidity
        bytes32 leaf = keccak256(bytes.concat(keccak256(abi.encodePacked(uint256(uint160(staker)), amount))));
        bytes32 rootHash = MerkleProof.processProof(proof, leaf);
        bytes32 messageHash = keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n36", rootHash, sxtBlockNumber, block.chainid));

        isValid = ISubstrateSignatureValidator(SUBSTRATE_SIGNATURE_VALIDATOR_ADDRESS).validateMessage(messageHash, r, s, v);
```
여러 스테이킹 컨트랙트가 있는 경우 재전송 공격이 가능합니다. 공격자는 SXT 체인의 동일한 입력을 사용하여 두 번째 스테이킹 컨트랙트에서 언스테이크 요청을 이행할 수 있습니다.
참고: SXT 토큰은 18 소수점을 가지고 있지만 USDC 토큰은 6 소수점을 가집니다.
SXT 스테이킹 컨트랙트에서 이행된 금액이 1 SXT인 경우, 공격자는 1000000000000 USDC를 언스테이크(도난)할 수 있습니다.

이 문제를 해결하려면 `messageHash`를 쌓인 토큰에 고유하게 만들어야 합니다.

```diff
        bytes32 leaf = keccak256(bytes.concat(keccak256(abi.encodePacked(uint256(uint160(staker)), amount))));
        bytes32 rootHash = MerkleProof.processProof(proof, leaf);
-        bytes32 messageHash = keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n36", rootHash, sxtBlockNumber, block.chainid));
+        bytes32 messageHash = keccak256(abi.encodePacked("\x19Ethereum Signed Message:\n36", rootHash, sxtBlockNumber, block.chainid, TOKEN_ADDRESS));

        isValid = ISubstrateSignatureValidator(SUBSTRATE_SIGNATURE_VALIDATOR_ADDRESS).validateMessage(messageHash, r, s, v);
```

# [L-13] 공격자가 환불에 대한 제한 없는 가스 한도를 악용하여 이행자를 괴롭힐 수 있음

현재 환불 로직은 환불 주소에 대한 저수준 호출(low-level call)을 직접 수행합니다. 그러나 이 호출에 소비되는 가스는 사용자가 지불하지 않습니다. 따라서 악의적인 사용자는 항상 환불 콜백에 악성 로직을 추가하여 이행자의 가스를 낭비할 수 있습니다.

```solidity
        if (payment.asset == NATIVE_ADDRESS) {
            if (payoutAmount > 0) {
                // slither-disable-next-line low-level-calls
                (bool paid,) = payoutAddress.call{value: payoutAmount}("");
                if (!paid) revert NativePaymentFailed();
            }
```

한 가지 해결책은 항상 래핑된 원본 토큰을 사용자에게 보내는 것이며, 다른 해결책은 호출에 사용되는 가스를 제한하는 것입니다.

# [L-14] `fulfillQuery()`의 `gasUsed` 계산에 L1 롤업 수수료 누락

`fulfillQuery`의 `gasUsed`는 항상 전체 실행 가스를 소비하도록 설계되었습니다.

```solidity
//QueryFulfillment.t.sol::L206
    function testFulfillQueryGasAccounting() public {
        uint256 initialGas = gasleft();
        uint248 usedGas = zkpay.fulfillQuery(_queryHash, _queryRequest, "results");
        uint248 expectedGasUsed = uint248(initialGas - gasleft());

        int248 shouldBeZero = int248(expectedGasUsed) - int248(usedGas);
        assertEq(shouldBeZero, 0, "FIXED_GAS_OVERHEAD is off by");
    }
```

여기서 문제는 사용자가 지불하는 가스가 항상 실행 가스 수수료와 같다는 점이지만, ZKPay가 이더리움 L2에서 사용될 때 리졸버가 지불한 L1 데이터 수수료는 상환되지 않습니다.

예를 들어, OP 체인의 현재 L1 수수료는 다음과 같이 계산됩니다:

`l1FeeScaled = baseFeeScalar*l1BaseFee*16 + blobFeeScalar*l1BlobBaseFee`
`estimatedSizeScaled = max(minTransactionSize * 1e6, intercept + fastlzCoef*fastlzSize)`
`l1Cost = estimatedSizeScaled * l1FeeScaled / 1e12`

https://docs.optimism.io/app-developers/transactions/fees#fjord

권고 사항:

특정 배포 체인을 기반으로 추가 L1 가스를 계산하세요.

# [L-15] 사용자의 언스테이크 요청이 이행되지 않은 상태로 남을 수 있음

현재 `SubstrateSignatureValidator.sol`은 순간적인 증명자(Attestor) 세트만 기록합니다. 특정 순간에 사용자의 sxtFulfillUnstake가 실패하고(예: 풀에 잔액이 충분하지 않음) 그 후 증명자 세트가 변경되면, 사용자는 토큰을 인출할 수 없게 될 수 있습니다. SXT 체인은 인출이 성공했는지 여부를 확인할 수 없고 사용자는 언스테이크를 다시 신청할 수 없으므로 이러한 토큰은 다시 인출할 수 없습니다.

가능한 해결책은 SubstrateSignatureValidator.sol에 sxtBlockNumber와 증명 세트의 매핑을 기록하여 사용자가 요청 당시 블록의 매핑을 사용하여 인출할 수 있도록 하는 것입니다.

또 다른 해결책은 스테이킹 풀이 사용자의 정산되지 않은 토큰을 내부적으로 기록하도록 허용하는 것입니다.
