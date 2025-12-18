# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Reya-Labs/reya-network** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Reya Network 정보

Reya Network는 무기한 선물(perpetuals)을 위해 거래 최적화된 모듈형 L2입니다. 체인 계층은 Arbitrum Orbit에 의해 구동되며 가스 요금이 없고 트랜잭션은 FIFO 기준으로 주문됩니다. 프로토콜 계층은 PnL 결제, 증거금 요구 사항, 청산과 같은 거래를 지원하기 위해 체인을 모듈형 구성 요소로 분해하여 DeFi 애플리케이션의 수직적 통합을 직접 해결합니다.

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

_검토 커밋 해시_ - [fb521866f00eb2fd7021a763a12aaf7d727e83f0](https://github.com/Reya-Labs/reya-network/tree/fb521866f00eb2fd7021a763a12aaf7d727e83f0)

_수정 검토 커밋 해시_ - [634058385163d38f1da033daf941c6fbf94884c6](https://github.com/Reya-Labs/reya-network/tree/634058385163d38f1da033daf941c6fbf94884c6)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `IAccountModule`
- `ICollateralPoolModule`
- `AccountExposure`
- `AccountModule`
- `CollateralPoolModule`
- `CollateralPool`
- `Market`
- `IDepositsModule`
- `IWithdrawalsModule`
- `Deposits`
- `Withdrawals`
- `DepositsModule`
- `WithdrawalsModule`
- `IAutoRebalanceModule`
- `IConfigurationModule`
- `ISharesModule`
- `DataTypes`
- `Errors`
- `Events`
- `FeatureFlagSupport`
- `AutoRebalanceModule`
- `ConfigurationModule`
- `SharesModule`
- `AllocationConfiguration`
- `GlobalConfiguration`
- `Pool`
- `ShareBalances`

# 발견 사항

# [C-01] `Pool.removeLiquidityV2()`가 잘못된 토큰을 사용하여 전송함

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`Pool.sol`은 quoteToken으로 rUSD를 포함하고 담보 지원으로 deUSD, sdeUSD를 포함합니다. 업데이트는 입금 및 출금 함수의 v2 버전을 도입합니다. 이를 통해 rUSD, deUSD, sdeUSD와 같은 토큰의 입금/출금이 가능합니다.

문제는 실수로 `Pool.removeLiquidityV2()`가 출금 토큰(withdrawing token) 대신 항상 quoteToken을 전송한다는 것입니다. 결과적으로 deUSD와 sdeUSD는 출금될 수 없습니다.

## 권장 사항

```diff
    function removeLiquidityV2(
        Data storage self,
        address owner,
        RemoveLiquidityV2Input memory input
    )
        internal
        returns (uint256)
    {
        ...

        // withdraw from the core to the passive pool
        coreWithdrawal(self.accountId, input.token, tokenAmount);

-       // transfer quote token amount to the receiver
+       // transfer collateral token amount to the receiver
        // note, tokens are transferred to the receiver rather than the owner!
-       self.quoteToken.safeTransfer(input.receiver, tokenAmount);
+       input.token.safeTransfer(input.receiver, tokenAmount);

        return tokenAmount;
    }
```

# [M-01] `removeLiquidityBySigV2`가 EIP-712를 준수하기 위해 내용을 올바르게 해시하지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`removeLiquidityBySigV2`는 아래와 같이 서명을 해시하지만 EIP-712를 준수하기 위해 완전히 해시하지는 않습니다.

```solidity
        Signature.validateRecoveredAddress(
            Signature.calculateDigest(
                keccak256(
                    abi.encode(
                        REMOVE_LIQUIDITY_V2_TYPEHASH,
                        block.chainid,
                        msg.sender,
                        owner,
                        poolId,
>>>                     abi.encode(
                            REMOVE_LIQUIDITY_V2_INPUT_TYPEHASH,
                            input.token,
                            input.sharesAmount,
                            input.receiver,
                            input.minOut
                        ),
                        Signature.incrementSigNonce(owner),
                        sig.deadline,
                        keccak256(extraSignatureData)
                    )
                )
            ),
            owner,
            sig
        );
```

`RemoveLiquidityV2Input` 구조체는 [표준](https://eips.ethereum.org/EIPS/eip-712#definition-of-encodedata)에서 요구하는 대로 해시되지 않고 인코딩만 됩니다.

> 구조체 값은 hashStruct(value)로 재귀적으로 인코딩됩니다.

결과적으로 EIP 준수 서명자는 `removeLiquidityBySigV2` 함수를 사용하려고 할 때 문제가 발생합니다.

## 권장 사항

`RemoveLiquidityV2Input` 구조체의 내용을 해시하십시오.

```diff
        Signature.validateRecoveredAddress(
            Signature.calculateDigest(
                keccak256(
                    abi.encode(
                        REMOVE_LIQUIDITY_V2_TYPEHASH,
                        block.chainid,
                        msg.sender,
                        owner,
                        poolId,
+                       keccak256(
                            abi.encode(
                               REMOVE_LIQUIDITY_V2_INPUT_TYPEHASH,
                               input.token,
                               input.sharesAmount,
                               input.receiver,
                               input.minOut
                           )
+                       ),
                        Signature.incrementSigNonce(owner),
                        sig.deadline,
                        keccak256(extraSignatureData)
                    )
                )
            ),
            owner,
            sig
        );
```
