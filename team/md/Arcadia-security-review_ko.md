# 정보

**Pashov Audit Group**은 이 분야 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험이 풍부한 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**lending-v2** 및 **accounts-v2** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Arcadia 소개

Arcadia는 온체인 자산 관리를 개선하고 단순화하는 데 전념하는 비수탁형 플랫폼입니다. 프로토콜의 V2는 사용자가 단일 온체인 트랜잭션만으로 복잡한 포트폴리오를 구성하고 리밸런싱할 수 있도록 하여, 위험을 숨기지 않으면서 DeFi의 대부분의 복잡성을 제거합니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼침.

- 중간 (Medium) - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼침.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼침.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮음.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 있음.

- 낮음 (Low) - 너무 많거나 너무 가능성이 낮은 가정을 포함하거나 인센티브가 거의 없거나 없는 상태에서 공격자가 상당한 지분을 필요로 함.

## 심각도 수준에 따른 필요 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약

**_검토 커밋 해시_ - [239da7a6a1f1f395a506d7e4258b29dd9d32f6ce](https://github.com/arcadia-finance/lending-v2/commit/239da7a6a1f1f395a506d7e4258b29dd9d32f6ce) 및 [cd5db77bbcc561c7e8c0486311c95398b9e0e2bc](https://github.com/arcadia-finance/accounts-v2/commit/cd5db77bbcc561c7e8c0486311c95398b9e0e2bc)**

**_수정 검토 커밋 해시_ - [326587baba2f8f3ea166c9609e18171ec07b3996](https://github.com/arcadia-finance/lending-v2/commit/326587baba2f8f3ea166c9609e18171ec07b3996) 및 [33c11ae03ccc2066f4c80b5e078f247fdbbae92c](https://github.com/arcadia-finance/accounts-v2/commit/33c11ae03ccc2066f4c80b5e078f247fdbbae92c)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

`lending-v2`의 경우:

- `Liquidator`
- `DebtToken`
- `LendingPool`
- `Tranche`
- `guardians/LendingPoolGuardian`

`accounts-v2`의 경우:

- `guardians/FactoryGuardian`
- `guardians/BaseGuardian`
- `guardians/RegistryGuardian`
- `Factory`
- `abstracts/Creditor`
- `Registry`
- `Proxy`
- `accounts/AccountV1`
- `accounts/AccountStorageV1`
- `oracle-modules/ChainlinkOracleModule`
- `oracle-modules/AbstractOracleModule`
- `oracle-modules/AbstractAssetModule`
- `asset-modules/AbstractAssetModule`
- `asset-modules/UniswapV3/UniswapV3AssetModule`
- `asset-modules/AbstractDerivedAssetModule`
- `asset-modules/AbstractPrimaryAssetModule`
- `asset-modules/StandardERC20AssetModule`

# 발견 사항

# [H-01] 계정 양도 후 `approvedCreditor`가 재설정되지 않음

## 심각도

**영향**: 높음, 사용자가 청산된 계정에서 자금을 훔칠 수 있음

**가능성**: 중간, 부실 채권 계정이 필요하거나 L2가 일정 시간 동안 오프라인 상태여야 함

## 설명

Arcadia 계정은 `approvedCreditor` 저장 변수에 저장되는 보조 백업 채권자를 지원하며 소유자가 설정할 수 있습니다. 문제는 계정이 양도될 때 이 변수가 재설정되지 않아 이전 소유자가 본질적으로 시스템에 대한 백도어를 가질 수 있다는 것입니다.

`approvedCreditor`는 `flashActionByCreditor` 함수를 호출하고 계정의 현재 채권자를 변경할 수 있습니다. 이 함수는 또한 일부 `actionTarget`과 함께 `_withdraw` 함수를 호출하며, 이전 채권자로부터 미상환 대출이 없는 경우 계정의 모든 자산을 해당 대상으로 본질적으로 이체할 수 있습니다. 따라서 `approvedCreditor`는 기본적으로 새로운 채권자처럼 행동하고 대신 계정의 모든 자산을 비울 수 있습니다.

프로토콜은 `approvedCreditor` 설정과 계정 양도 사이에 최소 경과 시간을 요구함으로써 2차 시장의 포지션 구매자에게 이것이 강제되는 것을 방지하며, 구매자는 계정을 구매할 때 이를 알고 있어야 합니다. 그러나 대출 풀이 `auctionBoughtIn` 함수를 통해 부실 대출을 내부화할 때 청산 중에도 계정 양도가 발생합니다.

```solidity
function auctionBoughtIn(
    address recipient
) external onlyLiquidator nonReentrant {
    _transferOwnership(recipient);
}
```

이것은 계정을 lendingPool로 이체하며 나중에 수동으로 청산해야 합니다. 그러나 `approvedCreditor`를 통한 백도어로 인해 사용자는 이 계정에 있는 자산을 훔칠 수 있습니다.

`auctionBoughtIn` 함수는 청산 기간이 지났을 때 청산 중에 호출됩니다.

```solidity
else if (block.timestamp > auctionInformation_.cutoffTimeStamp) {
ILendingPool(creditor).settleLiquidationUnhappyFlow(
    account,
    startDebt,
    msg.sender
);
IAccount(account).auctionBoughtIn(
    creditorToAccountRecipient[creditor]
);
```

이는 청산인이 계정에 보관된 자산에 관심이 없거나 L2 시퀀서가 일정 시간 동안 다운되어 청산이 발생하지 않는 경우 발생할 수 있습니다. 대부분의 청산은 MEV 봇을 통해 발생하므로 트래픽이 많고 가스 요금이 높은 시기에는 MEV 봇이 소규모 계정 청산에 관심이 없을 수 있으며 타이머가 차단 타임스탬프에 도달하도록 둡니다.

이 경우 안전한 수동 청산 대신 계정의 백도어를 통해 자산을 도난당할 수 있습니다.

## 권장 사항

계정을 양도할 때 `approvedCreditor` 변수를 재설정하거나 `isAssetManager`에서 처리되는 것처럼 소유자 기반 `approvedCreditor`를 보유하십시오.

# [M-01] 계약에 우아한 은퇴(graceful retirement) 기능이 없음

## 심각도

**영향**: 중간, 사용자는 여전히 너무 안전하지 않거나 불안정한 것으로 간주되는 풀에서 자산을 빌릴 수 있음

**가능성**: 중간, 다른 플랫폼에서 풀의 은퇴는 일반적임

## 설명

일반적으로 대출 플랫폼에서 특정 토큰이나 대출 풀이 너무 위험하거나 해킹당한 것으로 간주되면 은퇴합니다. 즉, 풀에서의 모든 예금 및 대출은 중단되지만 풀은 사용자가 대출을 상환하고 인출할 수 있도록 시스템에 여전히 유지됩니다. 이것은 대출 플랫폼의 일반적인 기능이며 사용자가 자금에 접근하지 못하도록 막는 동시에 위험한 자산으로부터 시스템의 레버리지를 해소하는 좋은 방법입니다.

이것은 Aave에서 여러 번 발생했으며 아래의 다른 예시들도 있습니다:

- Polygon에서 GHST 대출 비활성화
- Polygon에서 agEUR 대출 비활성화
- BSC Venus 프로토콜에서 UST 비활성화
- BSC Venus 프로토콜에서 SXP 비활성화
- BSC Venus 프로토콜에서 TRXOld 비활성화

주된 아이디어는 프로토콜이 종종 토큰을 상장 폐지하며, 예금 및 대출을 비활성화하지만 사용자가 여전히 대출을 상환하고 인출할 수 있도록 함으로써 이를 수행한다는 것입니다.

문제는 이 기능이 이 프로토콜에 존재하지 않는다는 것입니다.

현재 프로토콜에서 대출, 예금, 상환 및 인출의 각 작업은 `repayPaused`, `withdrawPaused`, `borrowPaused`, `depositPaused` 변수에 의해 제어됩니다. 이들은 `LendingPoolGuardian.sol` 계약에서 정의되고 제어됩니다. 그러나 쿨다운 기간 후 트리거될 수 있는 자동 잠금 해제 기능이 있습니다.

```solidity
function unpause() external override afterCoolDownOf(30 days) {
    emit PauseFlagsUpdated(
        repayPaused = false,
        withdrawPaused = false,
        borrowPaused = false,
        depositPaused = false,
        liquidationPaused = false
    );
}
```

따라서 프로토콜은 인출 및 상환만 허용하도록 풀을 일시 중지할 수 있지만, 이는 30일 동안만 가능합니다. 그 후 풀은 자동으로 일시 중지 해제되고 사용자는 다시 풀에서 예금하고 빌릴 수 있습니다.

예금을 중단하는 유일한 다른 방법은 Tranche 계약의 `lock` 변수를 조작하는 것입니다. 그러나 이는 인출도 방지합니다.

따라서 프로토콜이 장기간 동안 풀에 대한 예금 및 대출만 선택적으로 비활성화할 수 있는 방법은 없습니다. 팀의 설계 결정인 경우 이 문제는 무시할 수 있지만, 다른 대출 프로토콜의 일반적인 기능이므로 여기에서 플래그가 지정됩니다.

## 권장 사항

대출 풀 가디언에 예금 및 대출만 비활성화하고 쿨다운 기간 후에도 잠금 해제할 수 없는 `retire` 옵션을 갖추십시오.

# [M-02] 트랜치(Tranche) 지분 비율은 청산을 통한 기부를 통해 조작될 수 있음

## 심각도

**영향**: 높음, ERC4626 볼트의 지분 비율 조작

**가능성**: 낮음, 정확한 타이밍 및 기타 조건 필요

## 설명

계약은 다양한 볼트에 대한 지분 비율 조작 공격을 방지하기 위해 solmate ERC4626 계약을 사용합니다.

현재 맥락에서 공격 벡터를 이해하는 데 유용한 지분 비율 조작 공격에 대한 간략한 요약은 다음과 같습니다:

1. Alice는 조작 가능한 볼트에 1 wei의 토큰을 예치하고 1 wei의 볼트 토큰을 발행받습니다.
2. Alice는 직접 이체를 통해 볼트에 1e18 토큰을 "기부"합니다.
3. Bob은 이제 볼트에 2e18 토큰을 예치합니다. 볼트는 Bob의 지분을 (2e18/(1e18+1) = 1) wei의 지분 토큰으로 계산합니다. 반올림 오류로 인해 Bob은 토큰의 절반에 대한 접근 권한을 잃습니다.

solmate 라이브러리는 토큰의 `기부`를 차단하여 이 공격을 방지하려고 시도합니다. 일반적으로 총 자산은 `ERC20.balanceOf` 함수를 통해 계산됩니다. 그러나 solmate에서는 저장 변수에서 추적합니다. 이는 토큰의 직접 이체가 총 자산을 업데이트하지 않으므로 `기부`를 더 어렵게 만듭니다.

그러나 여전히 토큰을 `기부`할 수 있는 방법, 즉 `totalShares`에 영향을 주지 않고 `totalAssets`를 펌핑할 수 있는 방법이 있다면 지분 비율은 여전히 조작될 수 있습니다.

계약의 청산 메커니즘은 잉여금이 있는 경우 계정 소유자에게 토큰을 적립합니다.

```solidity
unchecked {
    // Pay out any surplus to the current Account Owner.
    if (surplus > 0)
        realisedLiquidityOf[IAccount(account).owner()] += surplus;
    // Pay out the "terminationReward" to the "terminator".
    realisedLiquidityOf[terminator] += terminationReward;
}
```

간단한 이체를 통해 수행할 수 있는 트랜치를 이러한 계정의 소유자로 만들면 트랜치의 `realisedLiquidityOf`는 지분을 발행하지 않고도 펌핑될 수 있습니다. 트랜치는 또한 아래 함수를 통해 자체 자산을 추적합니다.

```solidity
function totalAssetsAndSync() public returns (uint256 assets) {
    assets = LENDING_POOL.liquidityOfAndSync(address(this));
}

//@audit inside lending pool
function liquidityOfAndSync(
    address owner_
) external returns (uint256 assets) {
    _syncInterests();
    assets = realisedLiquidityOf[owner_];
}
```

따라서 청산을 통해 트랜치의 `realisedLiquidityOf`를 펌핑하면 지분을 늘리지 않고 계정의 자산을 늘릴 수 있습니다. 이는 지분 비율을 조작하는 데 사용될 수 있습니다.

공격은 다음 단계로 수행될 수 있습니다. 우리는 트랜치가 비어 있다고 가정합니다.

1. Alice는 트랜치에 1 wei를 예치하고 1 wei의 지분 토큰을 발행받습니다.
2. Alice는 계정을 만들고 계정이 거의 청산 가능할 때까지 빌립니다.
3. Alice는 거의 청산 가능한 계정을 트랜치의 소유권으로 이전하고 가격이 변동하기를 기다렸다가 청산을 호출합니다.
4. 트랜치는 이제 계정의 `owner`임으로 인해 `1e18` 토큰의 `surplus`를 적립받습니다.
5. 이제 트랜치는 `totalShares` = 1 wei, `totalAssets` = 1e18+1 wei를 갖습니다.
6. Bob은 토큰을 예치하고 이전과 같이 손실을 입습니다.

공격자들은 배포 전에도 트랜치 계정을 준비할 수 있습니다. 공격자들은 미래에 동일한 프로토콜 지갑에 의해 트랜치가 배포될 가능성이 매우 높기 때문에 미래 트랜치 주소의 주소를 예측할 수 있습니다. 따라서 공격자들은 청산을 호출하고 이 잉여금으로 미래 트랜치 계정에 자금을 미리 조달할 수 있습니다. 그런 다음 트랜치가 마침내 추가되면 처음부터 매우 큰 지분 비율을 가질 수 있어 반올림 오류로 인해 소액 예금을 방지할 수 있습니다.

## 권장 사항

토큰을 `기부`할 수 있는 방법이 있으므로 지분 비율 조작 공격은 여전히 가능합니다. 이는 여러 가지 방법으로 해결할 수 있습니다:

1. 트랜치는 1e10 "유령(ghost)" 지분을 제로 주소나 팩토리 자체에 발행할 수 있습니다. 이는 이러한 지분 조작을 방지하며 Yieldbox 및 Openzeppelin의 최신 ERC4646 구현에서 채택한 방식입니다.
2. 또는 `기부` 기능을 차단하십시오. 이것도 여러 가지 방법으로 수행할 수 있습니다:
   1. 잉여 토큰을 `realisedLiquidityOf`에 저장하는 대신 계정 소유자에게 지급하십시오. 이렇게 하면 `totalAssets` 수가 펌핑되지 않아 `기부`가 방지됩니다.
   2. 또는 트랜치가 계정의 소유자가 되는 것을 방지하기 위해 일련의 블랙리스트를 넣으십시오. 이것은 미래 지향적이지 않으며 많은 코드 팽창을 초래할 것이므로 좋은 해결책이 아닙니다.

간단한 특성으로 인해 주요 권장 사항은 유령 지분을 발행하거나 처음 1e10 초기 발행 지분을 소각하는 것입니다.

참고: solmate 스타일 자산 추적의 지분 비율 악용 방지 무능력은 최근 Wise Lending 해킹에서 강조되었으며 원래 ERC4626 EIP의 기여자인 alcueca가 [여기](https://twitter.com/alcueca/status/1746837626491908135)에서 언급했습니다. 현재 권장 사항은 [여기](https://twitter.com/danielvf/status/1746306320553152615)에서 참조된 것처럼 초기 토큰 양을 소각하거나 효과적으로 동일한 작업을 수행하는 최신 openzeppelin 또는 yieldbox 구현을 따르는 것입니다.

# [M-03] 청산인은 플래시 예금을 트랜치에 입금하여 추가 수익을 얻을 수 있음

## 심각도

**영향**: 중간, 청산 수익이 유동성 제공자에게 거부될 수 있음

**가능성**: 중간, 현재 배포 구성에는 적용되지 않지만 일반적으로 유효함

## 설명

청산 인센티브의 `liquidationPenalty` 부분은 `_syncLiquidationFeeToLiquidityProviders` 함수를 통해 가중치에 따라 트랜치에 추가 수익으로 분배됩니다. 이는 각 트랜치 지분에 해당하는 금액을 증가시킵니다. 문제는 청산인이 플래시 예금을 통해 추가 비용 없이 이 추가 수익을 스나이핑할 수 있다는 것입니다.

청산 중에는 가장 주니어(junior-most) 트랜치만 잠기고 다른 트랜치는 열려 있어 예치할 수 있습니다. `liquidationPenalty`는 즉각적인 시점에 주어지므로 사용자는 이론적으로 이러한 트랜치 풀에 매우 많은 금액을 예치하고 수익을 모은 다음 모두 인출하여 플래시론을 상환할 수 있습니다.

그러나 청산인 자신은 비용 없이 이 작업을 수행할 수 있습니다. 따라서 청산인이 수익성 있는 청산 포지션을 보면 바로 동일한 거래 내에서 트랜치에 플래시 예금을 하고 청산을 수행하고 종료 수수료, 청산 페널티의 큰 부분 및 담보 가격 할인을 모을 수 있습니다. 이것은 매우 수익성 있는 공격이며 모든 청산인이 수행할 수 있습니다.

이것은 모든 잠금 해제된 트랜치, 즉 가장 주니어 트랜치를 제외한 모든 트랜치에 영향을 미칩니다. 이 공격은 프런트러닝이 필요 없으므로 트랜잭션의 가시성과 관계없이 모든 체인에서 실행될 수 있습니다. 청산 수익은 공격자에게 추가 비용 없이 유동성 제공자에게 거부될 수 있습니다.

## 권장 사항

동일한 트랜잭션 / 블록에서 트랜치에 대한 예금 및 인출을 금지하십시오.

# [M-04] 트레저리 가중치 업데이트 시 보류 중인 이자가 처리되지 않음

## 심각도

**영향**: 중간, 트레저리 또는 풀 참가자의 수익 손실로 이어질 수 있음

**가능성**: 중간, 가중치 업데이트와 다음 예금/인출 사이에만 손실 발생

## 설명

`setTreasuryWeights` 함수는 트레저리의 이자 가중치를 설정하고 전역 `totalInterestWeight`도 업데이트합니다.

```solidity
function setTreasuryWeights(
    uint16 interestWeightTreasury_,
    uint16 liquidationWeightTreasury_
) external onlyOwner {
    totalInterestWeight =
        totalInterestWeight -
        interestWeightTreasury +
        interestWeightTreasury_;
```

그러나 `processInterests` 수정자가 누락되었습니다. 즉, 다음에 `processInterests`가 호출될 때 업데이트된 트레저리 가중치가 가중치 업데이트 시점이 아닌 마지막 이자 계산 시점부터 적용됩니다.

## 권장 사항

`setTreasuryWeights` 함수에 `processInterests` 수정자를 추가하십시오.

# [M-05] 수수료 포함 전송(fee-on-transfer) 토큰 지원은 부실 채권으로 이어질 수 있음

## 심각도

**영향**: 높음, FOT 토큰은 부실 채권을 유발함

**가능성**: 낮음, FOT 토큰은 드묾

## 설명

프로토콜은 필요한 경우 수수료 포함 전송(FOT) 토큰을 지원할 수 있다고 주장합니다. 사전 감사 질문 목록에 따르면,

```
- 계정의 가치를 계산하기 위해 (계정에서 받은 금액보다 클) '전송된 금액'을 저장하므로 계정은 실제로 과대 평가될 것입니다.
- 활성화되면 담보 및 청산 요인(가치 할인)이 수수료로 인한 가치 손실을 수용하도록 조정됩니다.
```

문제는 청산 및 담보 요인을 변경하는 것만으로는 문제를 해결하기에 충분하지 않다는 것입니다. 이는 간단한 예로 설명할 수 있습니다.

토큰 A가 1% 수수료가 있는 FOT 토큰이라고 가정해 보겠습니다.

1. Alice는 토큰 A 100,000개를 예치합니다.
2. 계약은 99,000개의 토큰을 받지만 Alice의 계정에 전체 100,000개의 토큰을 적립합니다.
3. Alice는 이제 99,000개의 토큰을 인출합니다.
4. Alice는 수수료 후 89,100개의 토큰을 받습니다. 계약은 보유 자산을 모두 지급했으므로 0개의 토큰을 가지고 있지만, 계약은 여전히 (100,000 - 99,000)=1000개의 토큰을 보유하고 있다고 생각합니다.
5. Alice는 이제 계약이 보유하고 있다고 생각하는 1000개의 토큰에 대해 대출을 받을 수 있습니다. 이것은 전적으로 부실 채권입니다.

예금 및 연속적인 인출을 통해 FOT 토큰을 사용하여 매우 적은 비용으로 부실 채권 포지션을 생성할 수 있습니다. 이는 시스템의 건전성에 직접적인 영향을 미치므로 문제입니다.

## 권장 사항

FOT 토큰은 항상 전송 후 회계 로직과 함께 사용해야 합니다. 프로토콜에는 그것이 없으므로 질문 목록의 주장에도 불구하고 FOT 토큰을 전혀 지원하지 않는 것이 좋습니다.

# [M-06] 첫 번째 트랜치 예금자는 이전에 얻은 모든 이자를 인출할 수 있음

## 심각도

**영향:** 중간, 트랜치의 초기에 얻은 이자에만 영향을 미치므로.

**가능성:** 중간, 트랜치가 이자를 얻기 시작하는 시간과 첫 번째 예금 시간 사이에 더 큰 격차가 있는 경우에만 문제가 존재하므로.

## 설명

트랜치가 LendingPool에 추가되면 이자를 받을 자격이 있습니다:

```solidity
    /**
     * @notice Syncs interest payments to the liquidity providers and the treasury.
     * @param assets The total amount of underlying assets to be paid out as interests.
     * @dev The interest weight of each Tranche determines the relative share of yield (interest payments)
     * that goes to its liquidity providers.
     */
    function _syncInterestsToLiquidityProviders(uint256 assets) internal {
        uint256 remainingAssets = assets;

        uint256 trancheShare;
        uint24 totalInterestWeight_ = totalInterestWeight;
        uint256 trancheLength = tranches.length;
        for (uint256 i; i < trancheLength; ++i) {
            trancheShare = assets.mulDivDown(interestWeightTranches[i], totalInterestWeight_);
            unchecked {
                realisedLiquidityOf[tranches[i]] += trancheShare;
                remainingAssets -= trancheShare;
            }
        }
        unchecked {
            totalRealisedLiquidity = SafeCastLib.safeCastTo128(totalRealisedLiquidity + assets);

            // Add the remainingAssets to the treasury balance.
            realisedLiquidityOf[treasury] += remainingAssets;
        }
    }
```

첫 번째 사용자가 자금을 예치한 후, 트랜치의 `totalAssets`는 예금 + 이전에 얻은 이자가 됩니다.

```solidity
    /**
     * @notice Returns the total amount of underlying assets, to which liquidity providers have a claim.
     * @return assets The total amount of underlying assets.
     * @dev Modification of totalAssets() where interests are realised (state modification).
     */
    function totalAssetsAndSync() public returns (uint256 assets) {
        assets = LENDING_POOL.liquidityOfAndSync(address(this));
    }
```

예금자는 트랜치 지분을 모두 보유하고 있기 때문에 트랜치 지분을 소각하여 전액을 인출할 수 있습니다:

```solidity
    /**
     * @notice Modification of the standard ERC-4626 redeem implementation.
     * @param shares The amount of shares being redeemed.
     * @param receiver The address of the receiver of the underlying ERC20 tokens.
     * @param owner_ The address of the owner of the shares being redeemed.
     * @return assets The corresponding amount of assets withdrawn.
     */
    function redeem(uint256 shares, address receiver, address owner_)
        public
        override
        notLocked
        notDuringAuction
        returns (uint256 assets)
    {
        if (msg.sender != owner_) {
            // Saves gas for limited approvals.
            uint256 allowed = allowance[owner_][msg.sender];

            if (allowed != type(uint256).max) allowance[owner_][msg.sender] = allowed - shares;
        }

        // Check for rounding error since we round down in previewRedeem.
        if ((assets = previewRedeemAndSync(shares)) == 0) revert TrancheErrors.ZeroAssets();

        _burn(owner_, shares);

        LENDING_POOL.withdrawFromLendingPool(assets, receiver);

        emit Withdraw(msg.sender, receiver, owner_, assets, shares);
    }
```

## 권장 사항

트랜치가 채권자에게 유동성을 제공하지 않은 경우, 즉 `realisedLiquidityOf[tranche] != 0`인지 확인하여 이자를 얻지 않도록 해야 합니다.

```solidity
   /**
     * @notice Syncs interest payments to the liquidity providers and the treasury.
     * @param assets The total amount of underlying assets to be paid out as interests.
     * @dev The interest weight of each Tranche determines the relative share of yield (interest payments)
     * that goes to its liquidity providers.
     */
    function _syncInterestsToLiquidityProviders(uint256 assets) internal {
        uint256 remainingAssets = assets;

        uint256 trancheShare;
        uint24 totalInterestWeight_ = totalInterestWeight;
        uint256 trancheLength = tranches.length;
        for (uint256 i; i < trancheLength; ++i) {
            trancheShare = assets.mulDivDown(interestWeightTranches[i], totalInterestWeight_);
            unchecked {
                realisedLiquidityOf[tranches[i]] += trancheShare;
                remainingAssets -= trancheShare;
            }
        }
        unchecked {
            totalRealisedLiquidity = SafeCastLib.safeCastTo128(totalRealisedLiquidity + assets);

            // Add the remainingAssets to the treasury balance.
            realisedLiquidityOf[treasury] += remainingAssets;
        }
    }
```

# [M-07] 청산이 진행되는 동안 차용자가 부채에 대한 이자를 지불함

## 심각도

**영향:** 중간, 추가 이자가 경매 마감 시간에 의해 제한되기 때문입니다.

**가능성:** 중간, 사용자가 완전히 청산되는 경우에만 문제입니다.

## 설명

LendingPool은 사용자의 포지션이 청산되는 동안 이자 지급을 일시 중지하지 않습니다. 청산이 시작되면 사용자의 부채 포지션이 증가하여 개시 + 종료 보상 및 청산 페널티를 충당합니다.

```solidity
    function startLiquidation(address initiator, uint256 minimumMargin_)
        external
        override
        whenLiquidationNotPaused
        processInterests
        returns (uint256 startDebt)
    {
        // Only Accounts can have debt, and debtTokens are non-transferrable.
        // Hence by checking that the balance of the msg.sender is not 0,
        // we know that the sender is indeed an Account and has debt.
        startDebt = maxWithdraw(msg.sender);
        if (startDebt == 0) revert LendingPoolErrors.IsNotAnAccountWithDebt();

        // Calculate liquidation incentives which have to be paid by the Account owner and are minted
        // as extra debt to the Account.
        (uint256 initiationReward, uint256 terminationReward, uint256 liquidationPenalty) =
            _calculateRewards(startDebt, minimumMargin_);

        // Mint the liquidation incentives as extra debt towards the Account.
        _deposit(initiationReward + liquidationPenalty + terminationReward, msg.sender);

        // Increase the realised liquidity for the initiator.
        // The other incentives will only be added as realised liquidity for the respective actors
        // after the auction is finished.
        realisedLiquidityOf[initiator] += initiationReward;
        totalRealisedLiquidity = SafeCastLib.safeCastTo128(totalRealisedLiquidity + initiationReward);

        // If this is the sole ongoing auction, prevent any deposits and withdrawals in the most jr tranche
        if (auctionsInProgress == 0 && tranches.length > 0) {
            unchecked {
                ITranche(tranches[tranches.length - 1]).setAuctionInProgress(true);
            }
        }

        unchecked {
            ++auctionsInProgress;
        }

        // Emit event
        emit AuctionStarted(msg.sender, address(this), uint128(startDebt));
    }
```

사용자는 여전히 총 부채의 지분을 나타내는 대출 풀 지분을 보유합니다. 경매가 진행되는 동안 다음 수정자를 통해 모든 부채 토큰 보유자가 이자를 지불합니다:

```solidity
    /**
     * @notice Syncs interest to LPs and treasury and updates the interest rate.
     */
    modifier processInterests() {
        _syncInterests();
        _;
        // _updateInterestRate() modifies the state (effect), but can safely be called after interactions.
        // Cannot be exploited by re-entrancy attack.
        _updateInterestRate(realisedDebt, totalRealisedLiquidity);
    }
```

기껏해야 경매는 최대 4시간 동안 진행될 수 있으므로 사용자는 자신이 해야 할 것보다 최대 4시간 더 많은 이자를 지불할 뿐입니다. 예를 들어, MakerDAO는 청산을 덮기 위해 경매가 시작되자마자 차용자의 포지션에 대한 이자 축적을 중단합니다.

## 권장 사항

청산되는 포지션은 추가 이자를 축적하지 않아야 합니다.

# [L-01] PUSH0가 모든 체인에서 지원되지 않음

계약은 `0.8.22` 솔리디티 버전을 사용하며, 이는 컴파일러 설정에 지정된 evm 버전에 따라 `PUSH0` 연산 코드를 생성할 수 있습니다. 이 연산 코드는 모든 체인에서 지원되지 않으며 배포 시 계약이 실패할 수 있습니다.

foundry.toml 파일에 `evm_version`이 지정되어 있지 않으므로 계약은 기본 evm 버전인 `paris`로 컴파일되며, 이는 `PUSH0` 연산 코드를 생성하지 않으므로 지금은 괜찮습니다. 그러나 이것이 `shanghai` 이상으로 변경되면 `PUSH0`를 지원하지 않는 체인에서 계약 배포가 실패하기 시작할 수 있습니다.

솔리디티 버전을 롤백하거나 foundry.toml 파일에서 evm 버전을 `paris`로 하드코딩하여 구성의 예상치 못한 변경을 방지하는 것이 좋습니다.

# [L-02] 청산이 진행되는 동안 계정이 2차 시장에서 판매될 수 있음

계정은 `safeTransferFrom` 함수를 통해 양도될 수 있으며, 이는 계정에서 `transferOwnership`을 호출합니다. 이 `transferOwnership`에는 `notDuringAuction` 수정자가 없으므로 쿨다운 기간이 지나는 한 경매가 진행되는 동안 양도를 수행할 수 있습니다. 따라서 2차 시장의 계정 구매자는 사기를 당할 수 있습니다. 경매 중인 계정은 부분 청산 후 또는 가격 변동으로 인해 정상 상태로 전환될 수 있으므로 `isAccountUnhealthy` 확인만으로는 구매자가 인지하기에 충분하지 않기 때문입니다. 구매자는 양도 후 다른 청산인의 성공적인 입찰로 모든 담보 자산을 잃을 수 있습니다.

`transferOwnership` 함수에 `notDuringAuction` 수정자를 추가하는 것을 고려하십시오.

# [L-03] 모든 트랜치 가중치가 0이면 예금/인출이 중단됨

LendingPool.sol 계약의 `addTranche` 함수에는 0 값 확인이 없습니다. 그러나 추가된 모든 트랜치의 가중치가 0이고 따라서 `totalInterestWeight`가 0이 되면 `_syncInterestsToLiquidityProviders` 함수에서 0으로 나누기로 인해 이자 계산이 중단될 수 있습니다.

```solidity
trancheShare = assets.mulDivDown(
                interestWeightTranches[i],
                totalInterestWeight_
            );
```

`addTranche` 및 `setTrancheWeights` 함수 모두에서 `interestWeightTranches` 및 `liquidationWeightTranches` 변수에 대한 0 값 확인을 추가하는 것을 고려하십시오.

# [L-04] 계정은 진행 중인 경매 동안 더 많은 부채를 계속 질 수 있음

lendingPool 계약의 `borrow` 함수를 사용하여 계정의 담보에 대해 더 많은 부채를 질 수 있습니다. 이 함수는 계정에 부채 토큰을 발행한 다음 상태 확인을 수행합니다.

```solidity
// Mint debt tokens to the Account.
_deposit(amountWithFee, account);

// Add origination fee to the treasury.
unchecked {
    if (amountWithFee - amount > 0) {
        totalRealisedLiquidity = SafeCastLib.safeCastTo128(
            amountWithFee + totalRealisedLiquidity - amount
        );
        realisedLiquidityOf[treasury] += amountWithFee - amount;
    }
}

// UpdateOpenPosition checks that the Account indeed has opened a margin account for this Lending Pool and
// checks that it is still healthy after the debt is increased with amountWithFee.
// Reverts in Account if one of the checks fails.
uint256 accountVersion = IAccount(account).increaseOpenPosition(
    maxWithdraw(account)
);
```

상태 확인은 `Account` 계약의 `increaseOpenPosition` 함수에서 수행됩니다.

```solidity
function increaseOpenPosition(
    uint256 openPosition
)
    external
    onlyCreditor
    nonReentrant
    updateActionTimestamp
    returns (uint256 accountVersion)
{
    // If the open position is 0, the Account is always healthy.
    // An Account is unhealthy if the collateral value is smaller than the used margin.
    // The used margin equals the sum of the given open position and the minimum margin.
    if (
        openPosition > 0 &&
        getCollateralValue() < openPosition + minimumMargin
    ) {
        revert AccountErrors.AccountUnhealthy();
    }

    accountVersion = ACCOUNT_VERSION;
}
```

이 함수는 사용된 마진이 담보 가치보다 작은지 확인하여 상태 확인을 수행하지만, 함수는 진행 중인 경매가 있는지 확인하지 않습니다. 즉, 계정은 상태 확인을 충족하는 한 더 많은 부채를 질 수 있습니다.

문제는 청산 중에 부분 청산 후 계정이 정상 상태가 될 수 있다는 것입니다. 이는 청산이 높은 승수로 시작되고 일부 사용자가 자산 목록에서 소중한 ERC721을 확보하고 계정을 다시 정상 상태로 밀어넣기 위해 그 거래를 기꺼이 받아들일 수 있기 때문입니다. 그러나 그 후 계정 소유자는 더 많은 부채를 지고 시스템의 건전성 요소를 악화시킬 수 있습니다. 가격이 변동하면 시스템은 이 조치로 인해 부실 채권 상태로 밀려날 수 있습니다.

계정 소유자가 경매 중에 적극적으로 더 많은 부채를 질 수 있으므로 수정해야 합니다.

`Account` 계약의 `increaseOpenPosition` 함수에 `notDuringAuction` 수정자를 추가하는 것을 고려하십시오.
