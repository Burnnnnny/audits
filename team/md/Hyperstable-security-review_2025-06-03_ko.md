# 소개 (About)
 Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)
 스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)
 **Pashov Audit Group**에 의해 **hyperstable/contracts** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Hyperstable 정보 (About Hyperstable)
 
Hyperstable은 사용자가 지원되는 담보를 예치하여 $USDH를 발행하는 1 USD로 거래되도록 설계된 과담보 스테이블코인입니다. 거버넌스 및 유동성 인센티브는 투표 및 게이지를 통해 관리되므로 vePEG 보유자가 PEG 배출을 지시하고 보상을 받고 유동성 분배에 영향을 미칠 수 있습니다.

# 위험 분류 (Risk Classification)
 
| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)
 
- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)
 
- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)
 
- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)
 _검토 커밋 해시_ - [4404ccb3b82b329a02c01f05e71fd3b588df7e46](https://github.com/hyperstable/contracts/tree/4404ccb3b82b329a02c01f05e71fd3b588df7e46)

_수정 검토 커밋 해시_ - [4190206ce93d4a275777a6b98742a45fb15b9b2a](https://github.com/hyperstable/contracts/tree/4190206ce93d4a275777a6b98742a45fb15b9b2a)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `InterestRateStrategyV3` 
- `PositionManagerV2` 
- `LiquidationManagerV2` 
- `GaugeV2` 
- `GaugeProviderV2` 
- `CurveGaugeFactory` 
- `HyperSwapGaugeFactory`

# 발견 사항 (Findings)

 # [M-01] 잘못된 변수 할당 (Incorrect variable assignment)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`GaugeV2.sol`의 생성자에는 할당 중에 내부 및 외부 뇌물 계약 주소가 잘못 교체되는 심각한 로직 오류가 포함되어 있습니다. 이로 인해 내부 뇌물 계약에 외부 뇌물 주소가 할당되고 그 반대의 경우도 마찬가지입니다.

취약한 코드:
```solidity
    constructor(
        address _stake,
        address _internalBribe,
        address _externalBribe,
      /***/
    ) {
        stake = _stake;
        internal_bribe = _externalBribe;
        external_bribe = _internalBribe;
```

## 권장 사항

이 문제는 다음과 같이 수정할 수 있습니다:

```diff
    constructor(
        /***/
    ) {
        stake = _stake;
-        internal_bribe = _externalBribe;
-        external_bribe = _internalBribe;
+        internal_bribe = _internalBribe;
+        external_bribe = _externalBribe;
```

# [M-02] 재진입 잠금이 보상 분배를 방해함 (Reentrancy lock hinders reward distribution)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`GaugeV2`와 `Voter` 계약 간의 보상 분배 흐름에 재진입 잠금 충돌이 존재합니다.

1. 사용자가 `GaugeV2.getReward()`를 호출합니다. `getReward`가 호출되면 첫 번째 `nonReentrant` 잠금을 얻습니다:

```solidity
function getReward(address account, address[] memory tokens) external nonReentrant {
    require(msg.sender == account || msg.sender == voter);
    IVoter(voter).distribute(address(this));
    // ... rest of the function
}
```

2. 이것은 `Voter.distribute()`를 호출하고, 그 다음 GaugeV2를 다시 호출합니다:

```solidity
IGauge(_gauge).notifyRewardAmount(base, _claimable);
emit DistributeReward(msg.sender, _gauge, _claimable);
```

3. 재진입 잠금이 아직 활성화되어 있기 때문에 notifyRewardAmount 호출은 되돌려집니다:

```solidity
function notifyRewardAmount(address token, uint256 amount) external nonReentrant {
    require(token != stake, "invalid reward");
    // ... rest of the function
}
```

이것은 보상 분배 메커니즘을 효과적으로 중단시킵니다.

## 권장 사항

`Gauge`를 따르십시오:

```solidity
        _unlocked = 1;
        IVoter(voter).distribute(address(this));
        _unlocked = 2;
```

# [L-01] 공격자가 화이트리스트 토큰으로 `Gauge` 배열을 채울 수 있음 (Attacker can fill `Gauge` array with whitelisted tokens)

Gauge/GaugeV2에서 notifyRewardAmount는 아직 추가되지 않은 경우 보상 토큰 배열에 토큰을 추가합니다. 토큰이 Voter 계약에서 화이트리스트에 있어야 한다는 것 외에는 다른 검증이 없습니다.

화이트리스트 토큰이 16개 이상이고 게이지에 보상 토큰이 16개 미만인 경우, 공격자는 `notifyRewardAmount`를 통해 게이지가 수명 종료 시까지 분배할 수 있는 토큰을 선택할 수 있으며(이 16개 토큰 외에는 다른 토큰을 분배할 수 없음), 이를 통해 다른 토큰의 분배를 막을 수 있습니다.

개념 증명 (Proof of Concept)

1. 50개의 화이트리스트 토큰이 있습니다.

2. 게이지는 3개의 보상 토큰으로 생성됩니다.

3. 공격자는 13개의 "최악의" 화이트리스트 보상 토큰으로 Gauge#notifyRewardAmount를 호출하여, 보상으로 "더 나은" 토큰을 입금할 수 없게 하므로 LP가 Gauge에 입금하는 것을 낙담시킵니다.

권장 사항:
`rewards` 배열에서 보상 토큰을 팝(pop)하는 방법을 도입하십시오.

# [L-02] `UUPSUpgradeable` 및 `OwnableUpgradeable`의 이중 상속 (Double inheritance of `UUPSUpgradeable` and `OwnableUpgradeable`)

`PositionManagerV2.sol`은 `UUPSUpgradeable` 및 `OwnableUpgradeable`을 상속합니다.
또한 `UUPSUpgradeable` 및 `OwnableUpgradeable`을 모두 상속 트리에 가지고 있는 `OperableUpgradeable`을 상속합니다:
```solidity
File: PositionManagerV2.sol
contract PositionManagerV2 is
    UUPSUpgradeable,
    OwnableUpgradeable,
    AccessControlUpgradeable,
    OperableUpgradeable,
    IPositionManager
{

File: OperableUpgradeable.sol
abstract contract OperableUpgradeable is UUPSUpgradeable, OwnableUpgradeable {
```
따라서 `PositionManagerV2.sol` 계약 상속 구조는 다음과 같습니다: 
```
UUPSUpgradeable    OwnableUpgradeable
           \                  /
            \                /
           OperableUpgradeable (abstract)
                   |
            PositionManagerV2
           /                \
    UUPSUpgradeable    OwnableUpgradeable
```
직접 상속이 이미 필요한 것을 제공하므로 추상 계약에서 상속을 제거하여 해결할 수 있습니다.

# [L-03] `NewRewardsBps`에 금고 인덱스가 누락되어 보상 추적을 방해함 (Vault index missing in `NewRewardsBps` impedes reward tracking)

`LiquidationManagerV2`의 `NewRewardsBps` 이벤트에 금고 인덱스 매개변수가 누락되어 특정 금고에 대한 보상 구성 변경 사항을 추적할 수 없습니다.

```solidity
emit NewRewardsBps(
  config.liquidatorRewardBps, config.bufferRewardsBps, 
  _newLiquidatorRewardBps, _newBufferRewardsBps
  );
```

이 문제를 완화하려면 `NewRewardsBps` 이벤트를 업데이트하여 금고 인덱스를 인덱싱된 매개변수로 포함하십시오.

# [L-04] 대리 작업에서 토큰 전송 수신자가 잘못됨 (Token transfer recipient incorrect in on-behalf operations)

PositionManagerV2 계약은 `onlyUserOrOperator` 수정자를 통해 `on-behalf-of` 기능을 구현하여 운영자가 사용자를 위해 작업을 수행할 수 있도록 합니다.

그러나 `onlyUserOrOperator`에서 두 가지 종류의 운영자가 있음을 알 수 있습니다:

```solidity
$.isSystemOperator[msg.sender] || $.isAllowedOperator[_user][msg.sender]
```

그러나 몇 가지 중요한 함수에서 계약은 토큰 작업에 `_user`(의도된 수신자) 대신 `msg.sender`(`operator`일 수 있음)를 잘못 사용합니다:

```solidity
...
DEBT_TOKEN.mint(msg.sender, _amountToBorrow);
...
DEBT_TOKEN.burn(msg.sender, _amountToRepay);
```
문제는 의도된 사용자 대신 운영자가 자산/토큰을 받게 된다는 것입니다. 이는 `on-behalf-of` 작업의 기본 신뢰 모델을 깨뜨립니다.

**`HypeOperator`가 사람들이 `Native_TOKEN`을 처리하는 데 도움이 되는 것은 사실이지만, `allowOperator`를 통해 임명된 운영자의 경우 동일한 경우가 아니며 토큰을 이러한 운영자에게 전송하는 것은 잘못된 것입니다.**

권장 사항:
`msg.sender`의 모든 인스턴스를 `_user`로 교체하십시오.

# [L-05] `LiquidationManager` 업그레이드 시 누락된 상태 마이그레이션 (State migration missing in `LiquidationManager` upgrade)

`LiquidationManager`에서 `LiquidationManagerV2`로 업그레이드하는 동안 전역 청산 보상 매개변수(`liquidatorRewardBps` 및 `bufferRewardsBps`)가 대체되었습니다.

```solidity
uint256 public liquidatorRewardBps = 0.25e18; // 25%
uint256 public bufferRewardsBps = 0.25e18; // 25%
```

LiquidationManagerV2에서는 이것들이 금고별 매핑으로 대체되었습니다:
```solidity
mapping(uint8 => LiquidationConfiguration) public liquidationConfiguration;
```

중요한 문제는 V2로 업그레이드할 때 기존 금고에 대한 `liquidationConfiguration` 값을 설정하는 상태 마이그레이션 프로세스가 없었다는 것입니다.

즉, 업그레이드 후 모든 금고는 `liquidationConfiguration`에서 `liquidatorRewardBps` 및 `bufferRewardsBps` 모두에 대해 0 값을 갖게 됩니다(나중에 `setRewardsBps`가 호출되지 않는 한).

문제는 V2의 `_liquidate` 함수가 `liquidationConfiguration[_index]`를 사용하여 보상 분배를 결정한다는 것입니다:

```solidity
        (IPositionManager.VaultData memory vaultData, IPositionManager.PositionData memory positionData) =
            manager.initLiquidation(_index, _positionOwner);

        LiquidationValues memory values =
            _getLiquidationValues(vaultData, positionData, liquidationConfiguration[_index], _debtToRepay);

        if (values.CR >= vaultData.MCR) {
            revert NothingToLiquidate();
        }
```

보상 매개변수가 0이면 의도하지 않은 결과가 발생할 수 있습니다.

권장 사항:
업그레이드 프로세스에서 기존 모든 금고에 대한 `liquidationConfiguration` 값을 이전 전역 매개변수와 일치하도록 설정하는 마이그레이션 함수를 구현하십시오.

`interestRateStrategyV3`를 참조할 수 있습니다:

```solidity
    function initialize(bytes memory) external reinitializer(3) {
        uint256 currentRate = ManualIRM.interestRate();

        uint256 vaultCount = IPositionManager(positionManagerAddress).lastVaultIndex();

        for (uint8 i; i < vaultCount; i++) {
            ManualIRMV2.setInterestRate(i, currentRate);
        }
    }
```
