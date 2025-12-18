# 정보

**Pashov Audit Group**은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**nume-p2p-contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# nume-p2p-contracts 정보

Nume 프로토콜은 토큰 전송 및 NFT 발행/거래를 지원하는 Polygon zkEVM의 Layer-3입니다. 계약은 Nume 프로토콜의 검증자가 결정하고 오프체인 상태 업데이트 알고리즘을 실행하는 트러스티드 실행 환경(Trusted Execution Environment)에서만 알려진 개인 키에 해당하는 특정 사전 정의된 공개 키에 의해 증명될 상태 업데이트를 찾습니다. 범위 내의 스마트 계약은 zkEVM에서 다이아몬드 패턴을 사용하여 구축되었으며 NFT 및 지원되는 ERC20 토큰 입금, 출금 요청 제출 및 이러한 보류 중인 요청 처리를 허용합니다.

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

**_검토 커밋 해시_ - [50d6bd5a29bc0fcce8852baadd5f63603670dcee](https://github.com/nume-crypto/nume-p2p-contracts/tree/50d6bd5a29bc0fcce8852baadd5f63603670dcee)**

**_수정 검토 커밋 해시_ - []()**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `NumeP2P`
- `libraries/LibDiamond`
- `libraries/AppStorage`
- `utils/NumeNFTFactory`
- `utils/MerkleUtils`
- `utils/PaymentUtils`
- `utils/VerifyUserUtils`
- `utils/QueueUtils`
- `utils/ECDSAUtils`
- `utils/MerkleUtils`
- `facets/nume/NFTDepositsFacet`
- `facets/nume/DepositsFacet`
- `facets/nume/WithdrawalsFacet`
- `facets/nume/RegistrationFacet`
- `facets/nume/ConfigFacet`
- `facets/nume/SettlementsFacet`
- `facets/diamond/OwnershipFacet`

# 발견 사항

# [C-01] 프로젝트를 강제로 exodusMode로 전환하는 공격

## 심각도

**영향:** 높음, 핵심 프로젝트 기능이 영구적으로 비활성화됨

**가능성:** 높음, 누구나 쉽게 실행 가능

## 설명

공격 흐름:

1. 악의적인 스마트 계약 A에서 ETH를 입금합니다. 이 계약은 ETH를 받을 때 되돌리기(revert)를 수행합니다.
2. Nume 네트워크에 대한 입금 정산을 기다립니다.
3. `submitWithdrawalRequest()`를 호출합니다.
4. 소유자는 `notarizeSettlement()`를 통해 14일 동안 인출할 수 있습니다.
5. 소유자는 인출을 정산하려고 시도하지만 실패합니다. 악의적인 스마트 계약 A가 ETH를 받지 않으려고 의도적으로 되돌리기 때문입니다.
6. 공격자는 14일 동안 기다립니다.
7. 공격자가 `challengeUserWithdrawal()`을 호출합니다. => `ns.isInExodusMode = true;`

결과적으로 `enforceExodusMode` 확인은 핵심 Nume 함수에서 통과하지 못합니다. 즉, 프로젝트에 대한 대량 탈출 시나리오(Mass exit scenario)가 활성화되어 새로운 입금이 없고 강제 인출이 활성화됩니다(Nume에 대한 인출 요청 없음).
또한 `isInExodusMode`를 다시 false로 설정할 방법이 없습니다.
(사실 존재합니다. 새 코드로 새 패싯을 긴급하게 개발하고 배포하고 수정 사항을 실행하십시오.)
 `USDC`와 같은 블랙리스트가 있는 ERC20 토큰에서도 동일한 문제가 발생할 수 있습니다.

동일한 원칙을 가진 또 다른 공격자 벡터 - 입금이 무효화되는 것을 방지합니다.
사용자의 ETH 입금이 무효로 태그된 경우(`notarizeSettlement` 동안) Nume은 보증금을 환불합니다.
그러한 악의적인 입금이 유효한 입금 중 하나라면 제거할 방법이 없으므로 유효한 입금을 처리하는 것이 문제가 될 것입니다.
따라서 소유자는 그러한 입금을 유효한 것으로 취급해야 합니다.

## 권장 사항

별도의 함수에서 사용자가 스스로 ETH를 인출하도록 하는 것이 좋습니다.
자금을 직접 이체하는 대신 함수는 `user=>token=>amount`와 같은 매핑을 증가시킬 수 있습니다. 그런 다음 사용자는 이 "잔액"을 인출하기 위해 함수를 호출해야 합니다.
또한 `exoduceMode`를 비활성화하는 도구를 도입하는 것을 고려하십시오.

# [C-02] 공격자가 Exodus 모드를 켤 수 있음

## 심각도

**영향:** 높음. 프로토콜 작동이 중단됩니다.

**가능성:** 높음. 이 공격을 수행하는 것을 막을 수 있는 것은 없습니다.

## 설명

공격자는 정산 공증 시 `Witdrawal_Stake`가 반환될 때 Nume에 재진입하고 `fallback()` 호출 `challengeUserWithdrawal()`에서 Exodus 모드를 활성화할 수 있습니다.

`notarizeSettlement()`는 온체인에 제출된 인출을 처리합니다. 이러한 온체인 제출에는 `Withdrawal_stake`가 필요하며, 이는 성공적인 인출 시 반환됩니다. 먼저 해당 `withdrawalRequest`의 타임스탬프를 삭제하지만 모든 인출 후 가장 마지막에 `ns.lastFinalizedWithdrawalsQueueIndex`를 업데이트합니다.

```solidity
            for (uint256 i; i < numContractWithdrawals; ) {
                ...
                delete ns.withdrawalRequests[
                    _args.contractWithdrawalAddresses[i]
                ][_args.contractWithdrawalTokenAddresses[i]];
                delete ns.withdrawalRequestTimestamps[
@>                  ns.lastFinalizedWithdrawalsQueueIndex + i + 1
                ];
                if (_args.contractWithdrawalAmounts[i] > 0) {
                    PaymentUtils.pay(
                        _args.contractWithdrawalAddresses[i],
                        _args.contractWithdrawalTokenAddresses[i],
                        _args.contractWithdrawalAmounts[i]
                    );
                    PaymentUtils.pay(
                        _args.contractWithdrawalAddresses[i],
                        0x1111111111111111111111111111111111111111,
                        ns.WITHDRAWAL_STAKE
                    );
                }
                ...
            }
            ...
@>          ns.lastFinalizedWithdrawalsQueueIndex = _args
                .handledWithdrawalsQueueIndex;
```

공격 벡터를 소개합니다.

1. 공격자는 `fallback()`에서 `WithdrawalsFacet.challengeUserWithdrawal()`을 호출하는 악성 계약을 생성합니다.
2. 공격자는 해당 악성 계약에 대해 온체인 인출을 제출합니다.
3. 운영자가 `notarizeSettlement()`를 호출하고 인출을 처리합니다.
4. `Withdrawal_Stake`가 해당 악성 계약으로 전송되고 `challengeUserWithdrawal()` 함수가 호출됩니다.

이제 `challengeUserWithdrawal()`을 살펴보겠습니다. 1) 기억하다시피, `ns.lastFinalizedWithdrawalsQueueIndex`는 가장 마지막에 업데이트되므로 현재 `queueIndex`는 완료되지 않았습니다. 2) 자산을 전송하기 전에 타임스탬프가 지워졌습니다. 결과적으로 모든 요구 사항(require)이 통과되고 프로토콜이 Exodus 모드로 들어갑니다.

```solidity
    function challengeUserWithdrawal(
        uint256 _queueIndex,
        bool _isNft
    ) external {
        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();

        if (_isNft) {
            ... // Skipped because scenario describes ERC20 withdrawal
        } else {
            require(
                _queueIndex <= ns.currWithdrawalsQueueIndex &&
@>                  _queueIndex > ns.lastFinalizedWithdrawalsQueueIndex,
                "WithdrawalsFacet: Invalid queue index"
            );
            require(
                block.timestamp >
@>                  ns.withdrawalRequestTimestamps[_queueIndex] +
                        ns.WITHDRAWAL_REQUEST_TIMEOUT,
                "WithdrawalsFacet: Withdrawal request has not expired yet"
            );
        }

        ns.isInExodusMode = true;
        emit ExodusModeEntered(_queueIndex, _isNft);
    }
```

## 권장 사항

Nume의 모든 외부 함수에 `nonReentrant` 수정자를 추가하십시오.

# [H-01] 사용자가 `withdrawExodus()` 호출을 반복하여 모든 토큰을 훔칠 수 있음

## 심각도

**영향:** 높음. 모든 사용자가 자금을 훔칠 수 있습니다.

**가능성:** 중간. Exodus 모드가 활성화되어 있어야 합니다.

## 설명

`withdrawExodus()` 함수는 프로토콜이 Exodus 모드로 들어갈 때, 즉 운영자가 Nume에서 Polygon으로 브리징을 수행하지 않을 때 Nume에 예치된 자금을 인출하는 데 사용됩니다.

이 함수는 사용자가 마지막 정산 전에 토큰 잔액을 가지고 있었는지만 확인합니다. 그렇다면 요청된 토큰을 사용자에게 전송합니다. 그러나 사용자가 `withdrawExodus()` 호출을 재생(replay)하는 것을 막을 수는 없습니다.

```solidity
    function withdrawExodus(
        WithdrawalRequestArgs calldata _args
    ) external nonReentrant {
        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();
        if (!ns.isInExodusMode) {
            revert AppStorage.NotInExodusMode();
        }
        require(
            ECDSAUtils.recoverSigner(
                abi.encodePacked(_args.user, ns.currBlockNumber),
                _args.signature
            ) == _args.user,
            "WithdrawExodus: Invalid user signature"
        );

        VerifyUserUtils.verifyUser(...);

        if (_args.isNft) {
            PaymentUtils.payNft(
                _args.user,
                _args.tokenAddress,
                _args.balanceOrTokenId,
                _args.mintedNft
            );
        } else {
            require(
                _args.balanceOrTokenId > 0,
                "WithdrawalsFacet: Invalid balance"
            );
            PaymentUtils.pay(
                _args.user,
                _args.tokenAddress,
                _args.balanceOrTokenId
            );
        }
        ...
    }
```

다음 시나리오를 가정해 보겠습니다.

1. 사용자가 300 USDC 입금
2. Nume이 Exodus 모드로 진입
3. 이제 사용자는 자신의 300 USDC를 무한히 인출할 수 있음

## 권장 사항

`withdrawExodus()`에서 인출된 금액을 추적하십시오. 예를 들어 내부 `mapping (address token => uint256 withdrawnAmount)`를 도입하십시오.

# [H-02] 성공적인 인출 후 NFT 인출 요청이 삭제되지 않음

## 심각도

**영향:** 높음. 공격자가 사용자의 nft를 훔칠 수 있습니다.

**가능성:** 중간. NFT 재입금이 필요합니다.

## 설명

인출 수행 후 인출 백엔드 nft 요청이 잘못 삭제되었습니다.

여기서 저장소(storage) 대신 메모리 배열에서 삭제가 수행되는 것을 볼 수 있습니다.

```solidity
    function withdrawNFT(
        address _user,
        address _nftContractAddress,
        uint256 _tokenId,
        bool _mintedNft,
        uint256 _queueIndex,
        bool _isContractWithdrawal
    ) external nonReentrant {
        AppStorage.enforceExodusMode();

        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();

        bytes32 queueItem = keccak256(
            abi.encodePacked(_user, _nftContractAddress, _tokenId, _mintedNft)
        );

        if (_isContractWithdrawal) {
            ...
        } else {
@>          bytes[] memory userBackendNftWithdrawalRequests = ns
                .userBackendNftWithdrawalRequests[_user];
            require(
                _queueIndex <= userBackendNftWithdrawalRequests.length,
                "NFTWithdrawalsFacet: Invalid queue index"
            );
            (
                address user,
                address nftContractAddress,
                uint256 tokenId,
                bool mintedNft
            ) = abi.decode(
                    userBackendNftWithdrawalRequests[_queueIndex - 1],
                    (address, address, uint256, bool)
                );
            PaymentUtils.payNft(user, nftContractAddress, tokenId, mintedNft);
@>          delete userBackendNftWithdrawalRequests[_queueIndex - 1];
        }

        emit NFTWithdrawn(_user, _nftContractAddress, _tokenId, _mintedNft);
    }
```

즉, 인출 후 실제로 사용자의 인출 요청이 삭제되지 않습니다. 이를 통해 사용자는 동일한 nft를 여러 번 인출할 수 있습니다.
다음 시나리오를 가정해 보겠습니다.

1. User1이 NFT 입금.
2. User1이 `withdrawNFT()`를 통해 NFT 인출. 그의 요청은 여전히 존재함.
3. User1이 User2에게 NFT 판매.
4. User2가 NFT 입금. 그러나 이제 User1이 이를 인출할 수 있는 권한을 가짐.

## 권장 사항

```diff
    function withdrawNFT(
        address _user,
        address _nftContractAddress,
        uint256 _tokenId,
        bool _mintedNft,
        uint256 _queueIndex,
        bool _isContractWithdrawal
    ) external nonReentrant {
        ...

        if (_isContractWithdrawal) {
            ...
        } else {
            bytes[] memory userBackendNftWithdrawalRequests = ns
                .userBackendNftWithdrawalRequests[_user];
            require(
                _queueIndex <= userBackendNftWithdrawalRequests.length,
                "NFTWithdrawalsFacet: Invalid queue index"
            );
            ...
            PaymentUtils.payNft(user, nftContractAddress, tokenId, mintedNft);
-           delete userBackendNftWithdrawalRequests[_queueIndex - 1];
+           delete ns.userBackendNftWithdrawalRequests[_user][_queueIndex - 1];
        }

        emit NFTWithdrawn(_user, _nftContractAddress, _tokenId, _mintedNft);
    }
```

# [H-03] 다른 msg.value 및 \_user로 입금 한도 위반

## 심각도

**영향:** 중간, 일일 한도 우회됨 (불변성 깨짐)

**가능성:** 높음, 쉽게 이용 가능한 동작

## 설명

Nume은 `NFTDepositsFacet` 및 `DepositsFacet`의 두 계약에서 사용자의 하루 입금 횟수를 제한하려고 합니다.
입금 중 다음과 같은 코드가 있습니다(간단하게 다시 작성됨).

```
...
uint256 currentTime = block.timestamp;
if (currentTime - ns.userDepositTimestamp[_user] >= 1 days) {
            delete ns.userDepositCount[_user];
            ns.userDepositTimestamp[_user] = currentTime;
        }
require ( ns.userDepositCount[msg.sender] < ns.depositsLimit )
...
ns.userDepositCount[msg.sender]++;
_deposit(_user, 0x1111111111111111111111111111111111111111, msg.value);
```

보시다시피 함수는 `_user`에 대한 카운터를 매일 삭제합니다.

```
delete ns.userDepositCount[_user];
```

그러나 나중에 함수는 `msg.sender`에 대한 카운터를 확인하고 `msg.sender`에 대해 `++`를 수행합니다.

```
require ( ns.userDepositCount[msg.sender] < ns.depositsLimit )
...
ns.userDepositCount[msg.sender]++;
```

`userDepositCount[_user]`는 `ns.depositsLimit` 미만인지 확인되지 않으며 증가되지도 않습니다.
결과적으로 msg.senders를 변경하여 `ns.depositsLimit`를 쉽게 우회할 수 있습니다.

게다가 ns.userDepositCount[msg.sender]는 항상 증가하며 매일 삭제/무효화되지 않습니다.

## 권장 사항

두 입금 기능인 `depositERC20()` 및 `deposit()`에서 `ns.userDepositCount[msg.sender]`를 `ns.userDepositCount[_user]`로 바꾸십시오.
`msg.sender`가 토큰 발신자로만 사용되는지 확인하십시오.
이 문제는 `NFTDepositsFacet` 및 `DepositsFacet`의 두 입금 계약 모두에 존재합니다.
**또는**
정확히 `msg.sender`를 제한하려는 의도였을 수 있습니다. 스팸을 방지하기 위해서입니다.
그렇다면 `delete ns.userDepositCount[_user];`는 `delete ns.userDepositCount[msg.sender];`로 대체되어야 합니다. msg.sender에 대한 카운터가 무효화되지 않기 때문입니다.

# [H-04] Exodus 모드 중 갇힌 토큰

## 심각도

**영향:** 높음, 토큰 갇힘

**가능성:** 중간, 보류 중인 인출이어야 하며 Exodus 모드가 활성화되어야 함

## 설명

Exodus 모드에서 사용자는 자신의 Nume 잔액을 증명하고 `withdrawExodus()`를 호출하여 아직 인출되지 않고 `notarizeSettlement()`에서 승인을 받지 못한 NFT를 인출할 수 있습니다.
또한 사용자는 이미 승인된 자금을 보유하고 있을 수 있습니다. 이 경우 사용자는 `withdrawNFT()`를 수동으로 호출해야 하는데, 이는 승인된 인출을 완료하는 마지막 단계입니다.
그러나 이 함수에는 다음 줄이 있습니다.

```
AppStorage.enforceExodusMode();
```

즉, 이러한 승인된 인출은 exodus 모드 중에 완료되지 않습니다.

- exodus 모드 중에는 허용되지 않으므로 `withdrawNFT()`는 실패합니다.
- `withdrawExodus()`는 사용자가 이러한 NFT를 가지고 있음을 증명하지 않습니다.

## 권장 사항

`withdrawNFT()`에서 `AppStorage.enforceExodusMode();`를 제거하십시오.

# [M-01] WITHDRAWAL_REQUEST_TIMEOUT은 너무 짧을 수 없음

## 심각도

**영향:** 높음, 프로토콜이 중지됨

**가능성:** 낮음, 변수에는 기본값이 있으며 수정될 가능성이 낮음

## 설명

`OwnershipFacet`에는 `WITHDRAWAL_REQUEST_TIMEOUT`을 설정하는 `setWithdrawalRequestTimeout()`이 있습니다.

`WITHDRAWAL_REQUEST_TIMEOUT`은 매우 위험한 매개변수입니다. 너무 낮으면 exodusMode로 빠질 가능성이 높아집니다. 즉, 프로토콜이 비활성화됩니다(exodusMode에서 돌아올 함수가 없으며 프로토콜은 새 Diamond를 배포하거나 새 패싯을 추가해야 합니다).

## 권장 사항

`WITHDRAWAL_REQUEST_TIMEOUT`에 대해 허용되는 최소값을 설정하거나 기본값 14일에서 수정을 비활성화하는 것이 좋습니다.

# [M-02] 동일한 서명이 인출 요청 및 구독 취소 모두에 사용됨

## 심각도

**영향:** 중간. 사용자는 서명을 제공할 때 어떤 작업을 수행할지 지정할 수 없습니다. 결과적으로 tx 발신자가 사용자를 대신하여 다른 작업을 수행할 수 있기 때문에 사용자는 무신뢰(trustless) 방식으로 서명을 사용할 수 없습니다.

**가능성:** 중간. 서명 사용에 영향을 미치지만 사용자는 문제를 피하기 위해 스스로 트랜잭션을 보낼 수 있습니다.

## 설명

여기서 두 메서드의 서명에 동일한 매개변수가 포함되어 있음을 알 수 있습니다.

```solidity
    function submitWithdrawalRequest(
        WithdrawalRequestArgs calldata _args
    ) external payable nonReentrant {
        AppStorage.enforceExodusMode();
        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();
        ...
        require(
            ECDSAUtils.recoverSigner(
@>              abi.encodePacked(_args.user, ns.currBlockNumber),
                _args.signature
            ) == _args.user,
            "SubmitWithdrawalRequest: Invalid user signature"
        );
       ...
    }

    function cancelSubscriptionRequest(
        CancelSubscriptionRequestArgs calldata _args
    ) external {
        AppStorage.enforceExodusMode();

        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();

        require(
            ECDSAUtils.recoverSigner(
@>              abi.encodePacked(_args.user, ns.currBlockNumber),
                _args.signature
            ) == _args.user,
            "SettlementsFacet: Invalid user signature"
        );
        ...
    }
```

즉, 트랜잭션을 보내는 전송자(transmitter)는 사용자가 구독 취소를 수행하기 위해 서명했음에도 불구하고 사용자의 자금을 인출할 수 있으며 그 반대의 경우도 마찬가지입니다. 데이터 변경 가능성은 서명의 무신뢰 사용 가능성을 깨뜨립니다.

## 권장 사항

서명 인수에 고유한 소금(salt)(예: 메서드 이름)을 추가하십시오.

```diff
    function submitWithdrawalRequest(
        WithdrawalRequestArgs calldata _args
    ) external payable nonReentrant {
        AppStorage.enforceExodusMode();
        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();
        ...
        require(
            ECDSAUtils.recoverSigner(
-               abi.encodePacked(_args.user, ns.currBlockNumber),
+               abi.encodePacked(_args.user, ns.currBlockNumber, "submitWithdrawalRequest"),
                _args.signature
            ) == _args.user,
            "SubmitWithdrawalRequest: Invalid user signature"
        );
       ...
    }

    function cancelSubscriptionRequest(
        CancelSubscriptionRequestArgs calldata _args
    ) external {
        AppStorage.enforceExodusMode();

        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();

        require(
            ECDSAUtils.recoverSigner(
-               abi.encodePacked(_args.user, ns.currBlockNumber),
+               abi.encodePacked(_args.user, ns.currBlockNumber, "cancelSubscriptionRequest"),
                _args.signature
            ) == _args.user,
            "SettlementsFacet: Invalid user signature"
        );
        ...
    }

    function withdrawExodus(
        WithdrawalRequestArgs calldata _args
    ) external nonReentrant {
        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();
        if (!ns.isInExodusMode) {
            revert AppStorage.NotInExodusMode();
        }
        require(
            ECDSAUtils.recoverSigner(
-               abi.encodePacked(_args.user, ns.currBlockNumber),
+               abi.encodePacked(_args.user, ns.currBlockNumber, "withdrawExodus"),
                _args.signature
            ) == _args.user,
            "WithdrawExodus: Invalid user signature"
        );
        ...
}
```

# [M-03] 입금 한도를 0으로 설정하면 모든 신규 입금이 중단됨

## 심각도

**영향:** 높음, 신규 입금이 중단됨 (DoS)

**가능성:** 낮음, 실수로 0을 입력하거나 입금 한도를 취소/비활성화하려는 의도로 입력

## 설명

입금 한도는 `setDepositsLimit()` 및 `setNftDepositsLimit()` 함수로 관리됩니다.
그들은 0 입력 확인 없이 `depositsLimit` 및 `nftDepositsLimit`를 설정합니다.
실수로 0을 입력하거나 한도를 비활성화하려는 의도로 0을 입력할 수 있습니다.

## 권장 사항

새 값이 0이 아니도록 요구하는 것이 좋습니다.

# [M-04] `Withdrawal_Stake` 및 보류 중인 입금이 잘못된 주소로 반환됨

## 심각도

**영향:** 낮음. 인출 개시자는 인출당 0.01 ETH를 잃습니다.

**가능성:** 높음. 유일한 전제 조건은 다른 사용자를 대신하여 인출을 수행하는 것이며, 이는 예상되는 동작입니다.

## 설명

현재 `Withdrawal_Stake`는 인출 요청을 제출하고 스테이킹한 실제 발신자가 아니라 인출이 처리되는 사용자에게 반환됩니다.

여기서 `Withdrawal_Stake`가 인출 개시자 대신 인출 수신자에게 반환되는 것을 볼 수 있습니다.

```solidity
    function notarizeSettlement(
        NotarizeSettlementArgs calldata _args
    ) external nonReentrant {
            ...
            for (uint256 i; i < numContractWithdrawals; ) {
                ...
                if (_args.contractWithdrawalAmounts[i] > 0) {
                    PaymentUtils.pay(
                        _args.contractWithdrawalAddresses[i],
                        _args.contractWithdrawalTokenAddresses[i],
                        _args.contractWithdrawalAmounts[i]
                    );
@>                  PaymentUtils.pay(
                        _args.contractWithdrawalAddresses[i],
                        0x1111111111111111111111111111111111111111,
                        ns.WITHDRAWAL_STAKE
                    );
                }
                ...
            }
            for (uint256 i; i < numContractWithdrawals; ) {
                ...
                if (_args.isContractNftWithdrawalValid[i]) {
@>                  PaymentUtils.pay(
                        _args.contractNftWithdrawalAddresses[i],
                        0x1111111111111111111111111111111111111111,
                        ns.WITHDRAWAL_STAKE
                    );
                } else {
                    ...
                }
                ...
            }
        ...
    }
```

## 권장 사항

인출 요청에 `sender` 매개변수를 도입하십시오. 인출 수신자 대신 요청의 `sender`에게 `Withdrawal_Stake`를 반환하십시오.

# [M-05] 스테이킹된 WITHDRAWAL_STAKE는 exodusMode 중에는 인출할 수 없음

## 심각도

**영향:** 중간, 일부 사용자 자금 손실

**가능성:** 중간, exodusMode는 시나리오이지만 가능성은 높지 않음

## 설명

`submitWithdrawalRequest()`는 대기 중인 인출을 갖기 위해 제공된 `msg.value`=`WITHDRAWAL_STAKE`가 필요합니다. `notarizeSettlement()`를 통해 인출 요청이 승인되면 반환되도록 설계되었습니다.
그러나 대량 탈출 시나리오 중에는 스테이킹된 `WITHDRAWAL_STAKE`를 반환할 방법이 없습니다. 보류 중인 입금 및 확인된 Nume 잔액만 인출할 수 있지만 `WITHDRAWAL_STAKE`는 인출할 수 없습니다.

## 권장 사항

# [M-06] WITHDRAWAL_STAKE가 변경된 경우 보류 중인 인출에 대한 예상치 못한 이익 및 손실

## 심각도

**영향:** 높음, 다른 사용자로부터 자금이 도난당할 가능성 및 서비스 거부(DoS)

**가능성:** 낮음, `Withdrawal_Stake`가 변경될 가능성이 낮고, 증가해야 하며, 보류 중인 인출이 있어야 함

## 설명

`submitWithdrawalRequest()`는 `msg.value`를 `WITHDRAWAL_STAKE`로 받습니다. 그러나 요청당 수신된 정확한 값을 저장하지 않습니다.
인출 요청이 `notarizeSettlement()`에서 진행될 때 현재 `WITHDRAWAL_STAKE`가 반환됩니다.
따라서 "submit"과 "notarize" 사이에( `setWithdrawStake()`를 통해) `WITHDRAWAL_STAKE`가 업데이트된 경우 새 업데이트된 값이 다시 전송되며 이는 초기에 스테이킹된 값과 다릅니다. 결과적으로 보류 중인 인출은 손실 또는 이익을 겪게 됩니다.
`WITHDRAWAL_STAKE`가 감소하면 사용자는 스테이킹한 것보다 적게 받습니다(손실).
`WITHDRAWAL_STAKE`가 증가하면 사용자는 스테이킹한 것보다 더 많이 받습니다(이익).

그러한 사용자의 이익은 전체 계약의 손실을 의미합니다. 대량 탈출 시나리오(DoS)의 경우 모든 인출을 완료할 자금이 부족합니다.

일부 터무니없는 시나리오에는 프런트런(frontrun) 공격이 포함됩니다.

1. (프런트런) 인출 요청, 더 작은 값 스테이킹
2. `WITHDRAWAL_STAKE` 증가
3. 요청이 진행될 때까지 대기, 증가된 값 수신

## 권장 사항

몇 가지 옵션이 있습니다.

1. 사용자당 또는 요청당 스테이킹된 값 저장
2. `setWithdrawStake()`를 호출할 때 보류 중인 인출이 없는지 확인
3. `WITHDRAWAL_STAKE` 변경 비활성화

# [L-01] `getUserDepositsLeft()` 및 `getUserNftDepositsLeft()`는 새 한도를 설정한 후 되돌릴(revert) 수 있음

사용자의 남은 입금에 대한 getter는 현재 에포크(epoch) 동안 한도가 설정되었고 사용자의 입금 횟수가 새 한도보다 크면 `NFTDepositsFacet` 및 `DepositsFacet`에서 되돌릴 수 있습니다.

```solidity
    function setDepositsLimit(uint256 _limit) external {
        AppStorage.enforceIsOwner();
        AppStorage.numeStorage().depositsLimit = _limit;
        emit DepositLimit(_limit);
    }

    function getUserDepositsLeft(
        address _user
    ) external view returns (uint256) {
        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();
        if (block.timestamp - ns.userDepositTimestamp[_user] >= 1 days) {
            return ns.depositsLimit;
        }
@>      return ns.depositsLimit - ns.userDepositCount[_user];
    }
```

```solidity
    function setNftDepositsLimit(uint256 _limit) external {
        AppStorage.enforceIsOwner();
        AppStorage.numeStorage().nftDepositsLimit = _limit;
        emit NFTDepositLimit(_limit);
    }

    function getUserNftDepositsLeft(
        address _user
    ) external view returns (uint256) {
        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();
        if (block.timestamp - ns.userNftDepositTimestamp[_user] >= 1 days) {
            return ns.nftDepositsLimit;
        }
@>      return ns.nftDepositsLimit - ns.userNftDepositCount[_user];
    }
```

권장 사항: 현재 한도가 사용자의 수보다 큰 경우를 처리하고 대신 0을 반환합니다.

# [L-02] currBlockNumber 명명 - block.number와 관련 없음

`currBlockNumber`는 `notarizeSettlement()`가 호출될 때마다 1씩 증가합니다.
또한 `Returns the current block number.`라는 주석이 달린 getter getCurrBlockNumber()도 있습니다.
그러나 이 변수는 실제 `block.number`와 비교되지 않습니다.
이 변수의 사용 사례는 논스(nonce)에 가깝습니다.
`block.number`와 관련이 없도록 변수 이름을 바꾸는 것을 고려하십시오.

# [L-03] `totalTokens`는 네이티브 코인을 계산하지 않음

네이티브 코인도 포함하는 것을 고려하십시오.

[여기에서 테스트 파일을 찾을 수 있습니다](https://gist.github.com/T1MOH593/b6f988b6e9902d97ab8f8c07779cb28f)

권장 사항:

```diff
contract NumeP2P {
    constructor(
        address _contractOwner,
        address _diamondCutFacet,
        bytes memory _data
    ) payable {
        ...

+       ++ns.totalTokens;
        ns.supportedTokens[0x1111111111111111111111111111111111111111] = true;
        ...
    }
...
}
```

# [L-04] 악성 ERC721로 `nftDepositsLimit` 우회 가능

여기에서는 CEI 패턴을 따르지 않으며 외부 호출 전에 효과를 수행합니다. ERC721은 악의적일 수 있으며 한도를 우회하여 `depositNFT()` 함수에 다시 들어갈(reenter) 수 있습니다. `ns.userNftDepositCount`는 가장 마지막에 업데이트되기 때문입니다.

```solidity
    function depositNFT(
        address _user,
        address _nftContractAddress,
        uint256 _tokenId
    ) external {
        AppStorage.enforceExodusMode();
        AppStorage.NumeStorage storage ns = AppStorage.numeStorage();

@>      if (!_checkNftDepositStatus(_user)) {
            revert AppStorage.ExceededMaximumDailyCalls(_user);
        }

@>      IERC721(_nftContractAddress).safeTransferFrom(
            msg.sender,
            address(this),
            _tokenId
        );
@>      ns.userNftDepositCount[msg.sender]++;
        _depositNFT(_user, _nftContractAddress, _tokenId);
    }
```

권장 사항:
가장 마지막에 외부 호출을 수행하십시오.

```diff
    function depositNFT(
        address _user,
        address _nftContractAddress,
        uint256 _tokenId
    ) external {
        AppStorage.enforceExodusMode();
