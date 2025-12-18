# 정보

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 제공하기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것임을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾기 위해 노력합니다. 우리는 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**rivus-comai-contracts** 및 **rivusDAO-contract** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Rivus 정보

Rivus DAO는 rsTAO를 통해 유동성 스테이킹(liquid staking) 솔루션을 제공하며, 사용자는 이더리움 네트워크에 머무르면서 스테이킹 보상을 얻을 수 있습니다. LST는 DeFi 활동에서 담보로 사용될 수 있으며 동시에 스테이킹 보장을 얻을 수 있습니다.

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

_검토 커밋 해시_:

- [fe259d9](https://github.com/cryptoLteam/rivus-comai-contracts)
- [15f8444](https://github.com/cryptoLteam/rivusDAO-contract)

_수정 검토 커밋 해시_ - [49967a9402ecfd761bbb94bb17886d1e291814b6](https://github.com/cryptoLteam/rivus-contract-audit/tree/49967a9402ecfd761bbb94bb17886d1e291814b6)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `rsCOMAI`
- `rsTAO`
- `RivusCOMAI`
- `RivusTAO`

# 발견 사항

# [C-01] 무제한 토큰 전송

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`getSharesByMintedRsTAO()` 함수는 RsTAO 금액에 해당하는 주식(shares)을 계산하는 데 사용되며 `_transfer()` 및 `_mintRsTAO()`와 같은 여러 함수에서 사용되었습니다. 문제는 총 발행량이 0일 때 `getSharesByMintedRsTAO()` 함수가 금액 자체를 반환해야 하지만 0을 반환한다는 것입니다. 이는 총 발행량이 0일 때 무제한 토큰을 전송하는 것과 같은 여러 영향을 미칩니다. 다음은 POC입니다.

1. RivusTAO가 최근에 배포되었거나 다른 이유(업그레이드 등)로 총 금액이 0이라고 가정합니다.
2. 이제 공격자는 DEX나 대출 플랫폼과 같은 제3자 계약으로 무제한 토큰을 전송하고 자신을 위해 토큰을 입금할 수 있습니다.
3. 이는 RivusTAO가 `_transfer()`에서 토큰을 전송하려고 할 때 `getSharesByMintedRsTAO()`를 호출하여 주식 금액을 계산하고 주식 금액이 0이 되므로 코드가 문제없이 0 주식을 전송하기 때문에 가능합니다.
4. 결국 제3자 계약은 실제로 토큰을 받지 않았음에도 불구하고 사용자 계정에 요금을 부과합니다. `transferFrom()` 호출은 true를 반환하고 되돌리지 않습니다.

다른 영향도 있습니다. `_mintRsTAO()` 함수도 `getSharesByMintedRsTAO()`를 사용하며 반환 금액이 0이면 코드는 `amount`를 사용하여 주식을 발행합니다. 문제는 반올림 오류(작은 `amount` 값)로 인해 `getSharesByMintedRsTAO()`의 반환 금액이 0이 될 수 있으며, 코드는 0보다 큰 주식을 발행해야 하는데 0주식을 발행해야 한다고 계산할 수 있다는 것입니다. 이는 주식 가격이 높을 때 소액 예금 계약에서 가치를 추출하는 데 사용할 수 있습니다.

이 문제는 rsCOMAI 및 RivusCOMAI 계약에도 존재합니다.

## 권장 사항

총 발행 토큰이 0인 경우 코드는 `getSharesByMintedRsTAO()`에서 `amount`를 반환해야 합니다. `getMintedRsTAOByShares()` 함수도 수정해야 합니다. 이러한 수정 사항은 COMAI 계약에도 적용되어야 합니다.

# [C-02] RivusTAO의 `rebase()` 함수가 잘못된 APR 적용

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`rebase()` 함수는 총 주식 금액을 줄여 주식 가격에 일일 APR을 적용하기 위한 것입니다. 문제는 코드가 `burnAmount`를 계산하기 위해 `totalSharesAmount * apr`을 사용하므로 소각된 금액이 원래보다 커진다는 것입니다. 다음은 POC입니다.

1. 100주와 100토큰이 있다고 가정합니다.
2. 관리자는 하루에 20% 인상을 적용하고 싶어 합니다.
3. 코드는 소각 금액을 `100 * 20% = 20`으로 계산하고 새 총 주식은 80이 됩니다.
4. 이제 토큰 대 주식 가격은 `100/80 = 1.25`가 되고 보시다시피 비율이 25% 증가합니다.
5. 이로 인해 보상률이 잘못되고 더 빨리 인출하는 사람들이 다른 사람들의 토큰을 훔치게 됩니다.

## 권장 사항

코드는 `burnAmount`를 계산하기 위해 `totalSharesAmount * apr / (1 + apr)`을 사용해야 합니다.

# [H-01] 리베이스 메커니즘에서 슬래싱(Slashing)이 지원되지 않음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`rebase()` 함수는 주식 가격을 높이고 스테이킹 보상을 적용하는 데 사용되었습니다. 문제는 스테이킹에는 고유한 위험이 있으며 스테이킹 금액이 삭감(slashed)될 수 있다는 것입니다(Commume 또는 Bittensor 네트워크에서). 현재 로직은 주식 가격 인하를 지원하지 않습니다. 또한 브리징 시 일부 토큰이 도난당하거나 손실될 수 있으므로 해당 이벤트에 따라 주식 가격을 조정하기 위해 주식 가격을 낮출 수 있는 기능이 필요합니다.

## 권장 사항

주식 가격을 낮추는 기능도 추가하십시오.

# [M-01] 수수료 계산이 일관되지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`RivusCOMAI::approveMultipleUnstakes` 함수는 `hasApproveWithdrawalRole` 역할을 가진 사용자가 `requestUnstake` 요청을 승인하고 요청된 `wCOMAI` 자산을 보낼 수 있도록 합니다. 그런 다음 사용자는 `RivusCOMAI::unstake` 함수를 호출하여 해당 `wCOMAI` 토큰을 받을 수 있습니다.

문제는 `requestUnstake` 승인과 사용자가 `unstake`를 수행할 때 사이에 수수료가 변경되어 필요한 실제 금액에 영향을 미칠 수 있기 때문에 발생합니다. 다음 시나리오를 고려해 보십시오.

1. `1 rsCOMAI`에 대한 `requestUnstake`가 `1 wCOMAI`와 교환하여 이루어집니다.
2. 관리자는 요청을 승인하고 `RivusCOMAI#L724`의 `getWCOMAIByrsCOMAIAfterFee` 함수를 사용하여 계약으로 보낼 `wCOMAI` 금액을 계산합니다.

```solidity
File: RivusCOMAI.sol
681:   function approveMultipleUnstakes(UserRequest[] calldata requests)
682:     public
683:     hasApproveWithdrawalRole
684:     nonReentrant
685:     checkPaused
686:   {
...
...
702:     // Loop through each request to unstake and check if the request is valid
703:     for (uint256 i = 0; i < requests.length; i++) {
...
...
724:       (uint256 wcomaiAmt,,uint256 unstakingFeeAmt) = getWCOMAIByrsCOMAIAfterFee(unstakeRequests[request.user][request.requestIndex].comaiAmt);
725:       totalRequiredComaiAmt = totalRequiredComaiAmt + wcomaiAmt + unstakingFeeAmt;
726:     }
...
...
741:     // Transfer the COMAI from the withdrawal manager to this contract
742:     require(
743:       IERC20(commonWrappedToken).transferFrom(
744:         msg.sender,
745:         address(this),
746:         totalRequiredComaiAmt
747:       ),
748:       "comaiAmt transfer failed"
749:     );
...
...
760:   }
```

```solidity
File: RivusCOMAI.sol
476:   function getWCOMAIByrsCOMAIAfterFee(uint256 rsCOMAIAmount)
477:     public
478:     view
479:     returns (uint256, uint256, uint256)
480:   {
481:     uint256 unstakingFeeAmt = rsCOMAIAmount * unstakingFee / 1000;
482:     uint256 bridgingFeeAmt = rsCOMAIAmount * bridgingFee / 1000;
483:     if(bridgingFeeAmt < 1 * (10 ** decimals())) bridgingFeeAmt = 1 * (10 ** decimals());
484:     uint256 unstakingAmt = rsCOMAIAmount - bridgingFeeAmt - unstakingFeeAmt;
485:     return (unstakingAmt, bridgingFeeAmt, unstakingFeeAmt);
486:   }
```

3. 그런 다음 `RivusCOMAI::setUnstakingFee` 및 `RivusCOMAI::setBridgingFee` 함수를 사용하여 수수료가 변경됩니다.
4. 승인된 `requestUnstake`를 가진 사용자가 `RivusCOMAI::unstake` 함수를 호출합니다. 이 함수는 `RivusCOMAI#L781`에서 `getWCOMAIByrsCOMAIAfterFee` 함수를 다시 호출하여 `단계 3`에서 변경된 사항으로 인해 다른 수수료가 발생합니다.

```solidity
File: RivusCOMAI.sol
770:   function unstake(uint256 requestIndex) public nonReentrant checkPaused {
771:
772:     require(
773:       requestIndex < unstakeRequests[msg.sender].length,
774:       "Invalid request index"
775:     );
776:     UnstakeRequest memory request = unstakeRequests[msg.sender][requestIndex];
777:     require(request.amount > 0, "No unstake request found");
778:     require(request.isReadyForUnstake, "Unstake not approved yet");
779:
780:     // Transfer wCOMAI tokens back to the user
781:     (uint256 amountToTransfer,,uint256 unstakingFeeAmt) = getWCOMAIByrsCOMAIAfterFee(request.comaiAmt);
782:     _transferToVault(address(this), unstakingFeeAmt);
783:
784:     // Update state to false
785:     delete unstakeRequests[msg.sender][requestIndex];
786:
787:     // Perform ERC20 transfer
788:     bool transferSuccessful = IERC20(request.wrappedToken).transfer(
789:       msg.sender,
790:       amountToTransfer
791:     );
792:     require(transferSuccessful, "wCOMAI transfer failed");
793:
794:     // Process the unstake event
795:     emit UserUnstake(msg.sender, requestIndex, block.timestamp);
796:   }
```

5. 사용자에게 전송된 금액은 수수료 변경으로 인해 올바르지 않을 수 있으며, `단계 2`에서 관리자가 예치한 `wCOMAI`가 부족할 수 있습니다.

이러한 불일치로 인해 `approveMultipleUnstakes()`를 통해 `RivusCOMAI` 계약에 올바른 금액의 `wCOMAI`가 예치되지 않아 일부 언스테이크 작업이 실패할 수 있습니다.

## 권장 사항

`RivusTAO::requestUnstake#L572`에서 수행되는 방식과 유사하게 `RivusCOMAI::requestUnstake` 함수 중에 수수료와 래핑된 토큰 금액을 계산하는 것이 좋습니다. 이렇게 하면 `approveMultipleUnstakes()` 및 `unstake()` 함수 모두에서 동일한 `wCOMAI` 및 `unstakingFees` 값이 일관되게 사용됩니다.

# [M-02] APR이 정확하게 적용되지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`rebase()` 함수는 원하는 APR을 적용하기 위해 주식 가격 인상을 처리합니다. 문제는 코드가 요금 인상을 계산할 때 `lastRebaseTime`을 사용하지 않고 지난번부터 24시간이 지났다고 가정한다는 것인데, 이는 사실이 아닐 수 있습니다. 이렇게 하면 일부 시간 동안 APR이 잘못됩니다. 예를 들어 `rebase()`가 1시간 더 빨리 또는 나중에 호출되면 해당 1시간 동안의 주가 비율은 틀린 것이 됩니다.

## 권장 사항

`duration = block.timestamp - lastRebaseTime`을 사용하여 마지막 리베이스 호출에서 발생한 실제 요금 인상을 계산하십시오.

# [L-01] `payable.send` 대신 `payable.call` 사용

`RivusCOMAI.sol`에서 `payable.send`는 serviceFee를 withdrawalManager에게 보낼 때 사용됩니다.

```
    // in the guard condition at start of function
    bool success = payable(withdrawalManager).send(serviceFee);
```

send()를 사용하는 스마트 계약은 2300이라는 고정된 양의 가스를 전달하여 가스 비용에 대한 하드 종속성을 갖습니다. 가스 비용이 변경될 수 있는 경우 스마트 계약은 특정 가스 비용에 의존할 수 없습니다.

대신 `payable(withdrawalManager){value: serviceFee}.call("")`을 사용하십시오.

# [L-02] 초과 `msg.value`가 사용자에게 반환되지 않음

사용자가 `requestUnstake()`를 호출할 때 `msg.value`가 serviceFee 지불의 일부로 필요합니다. 그러나 사용자가 필요한 것보다 더 많은 `msg.value`를 보내면 초과된 가치는 계약에 갇히게 됩니다.

```
    // Ensure that the fee amount is sufficient
    require(msg.value >= serviceFee, "Fee amount is not sufficient");
```

대신 엄격한 동등성 확인을 하는 것이 좋습니다.

# [L-03] 토큰을 rsToken으로 변환하고 다시 변환할 때 수수료 계산이 다름

wToken을 rsToken으로 변환할 때 수수료는 다음과 같이 계산됩니다.

```
 function calculateAmtAfterFee(uint256 wcomaiAmount)
    ...
    uint256 _bridgeFee = wcomaiAmount * bridgingFee / 1000;
    if(_bridgeFee < 1 * (10 ** decimals())) _bridgeFee = 1 * (10 ** decimals());
    uint256 amountAfterBridgingFee = wcomaiAmount - _bridgeFee;
    uint256 feeAmount = 0;
    if(stakingFee > 0) {
      feeAmount = (amountAfterBridgingFee * stakingFee) / 1000;
    }
    uint256 amountAfterTotalFees = amountAfterBridgingFee - feeAmount;
  }

```

총 금액에서 브리징 수수료를 공제한 다음 새 금액에서 스테이킹 수수료를 공제합니다.

언스테이킹할 때 브리징 수수료와 언스테이킹 수수료는 총 금액에 대해 계산됩니다.

```
  function getWCOMAIByrsCOMAIAfterFee(uint256 rsCOMAIAmount)
    public
    view
    returns (uint256, uint256, uint256)
  {
    uint256 unstakingFeeAmt = rsCOMAIAmount * unstakingFee / 1000;
    uint256 bridgingFeeAmt = rsCOMAIAmount * bridgingFee / 1000;
    if(bridgingFeeAmt < 1 * (10 ** decimals())) bridgingFeeAmt = 1 * (10 ** decimals());
    uint256 unstakingAmt = rsCOMAIAmount - bridgingFeeAmt - unstakingFeeAmt;
    return (unstakingAmt, bridgingFeeAmt, unstakingFeeAmt);
  }
```

즉, 스테이킹할 때와 언스테이킹할 때 수수료 계산이 다릅니다. 사용자는 스테이킹 및 언스테이킹 비율이 동일한 경우 스테이킹할 때 수수료를 약간 덜 지불합니다.

예를 들어 스테이킹과 언스테이킹이 모두 1%이고 브리지가 1%인 경우, 1000 토큰을 가진 사용자는 언스테이킹 시 10 토큰과 브리지 수수료 10 토큰을 지불하지만, 스테이킹 시에는 브리지 수수료 10 토큰과 9.9 토큰을 지불합니다.

수수료 계산 방식을 표준화하십시오.

# [L-04] `wrap()` 및 `requestUnstake()` 함수가 최소 예상 금액을 지정하지 않음

블록체인의 특성과 인센티브에 따른 트랜잭션 순서로 인해 `wrap()` 및 `requestUnstake()` 함수에 대한 트랜잭션이 실행되기 전에 `exchangeRate`, `stakingFees` 및 `bridgingFees`가 변경될 수 있습니다. 이로 인해 사용자는 예상보다 적은 자산을 받을 수 있습니다.

교환 시 최소 예상 금액을 지정하기 위해 `RivusTAO.sol` 및 `RivusCOMAI.sol` 계약의 `wrap()` 및 `requestUnstake()` 함수에 매개변수를 포함해야 합니다.

# [L-05] `minStakingAmt` 확인이 수수료가 공제되기 전에 수행됨

`RivusTAO::wrap` 함수는 수수료가 차감되기 전에 `wTAO` 금액이 `minStakingAmt`를 초과하는지 확인합니다.

```solidity
File: RivusTAO.sol
887:   function wrap(uint256 wtaoAmount) public nonReentrant checkPaused returns (uint256) {
...
...
918:     // Ensure that at least 0.125 TAO is being bridged
919:     // based on the smart contract
920:     require(wtaoAmount > minStakingAmt, "Does not meet minimum staking amount");
921:
922:
923:     // Ensure that the wrap amount after free is more than 0
924:     (uint256 wrapAmountAfterFee, uint256 feeAmt) = calculateAmtAfterFee(wtaoAmount);
925:
926:     uint256 rsTAOAmount = getRsTAObyWTAO(wrapAmountAfterFee);
...
...
939:   }
```

이 접근 방식은 실제로 스테이킹되는 수수료 공제 후 순액이 `minStakingAmt`를 충족하는지 확인해야 하므로 결함이 있습니다. 수수료 공제 전에 확인을 수행하면 이후에 차감되는 수수료로 인해 실제 스테이킹된 금액이 의도한 최소 금액보다 적은 시나리오가 발생할 수 있습니다.

수수료가 계산되고 `wTAO` 금액에서 공제된 후 `minStakingAmt`를 확인하도록 유효성 검사 프로세스를 조정하십시오. 이렇게 하면 효과적으로 스테이킹되는 금액이 여전히 프로토콜에서 규정한 최소 요구 사항을 충족하여 사용자가 수수료로 인해 최소 금액 미만으로 스테이킹하는 것을 방지할 수 있습니다.

```diff
  function wrap(uint256 wtaoAmount) public nonReentrant checkPaused returns (uint256) {
    ...
    ...

--  // Ensure that at least 0.125 TAO is being bridged
--  // based on the smart contract
--  require(wtaoAmount > minStakingAmt, "Does not meet minimum staking amount");

    // Ensure that the wrap amount after free is more than 0
    (uint256 wrapAmountAfterFee, uint256 feeAmt) = calculateAmtAfterFee(wtaoAmount);

++  // Ensure that at least 0.125 TAO is being bridged
++  // based on the smart contract
++  require(wrapAmountAfterFee > minStakingAmt, "Does not meet minimum staking amount");

    uint256 rsTAOAmount = getRsTAObyWTAO(wrapAmountAfterFee);
    ...
    ...
  }
```

# [L-06] `hasTokenSafePullRole` 사용자 역할이 `wrappedToken`을 인출할 수 있음

`safePullERC20()` 함수는 승인된 사용자가 계약에서 토큰을 인출할 수 있도록 설계되었습니다.

```solidity
File: RivusTAO.sol
941:
942:   function safePullERC20(
943:     address tokenAddress,
944:     address to,
945:     uint256 amount
946:   ) public hasTokenSafePullRole checkPaused {
947:     _requireNonZeroAddress(to, "Recipient address cannot be null address");
948:
949:     require(amount > 0, "Amount must be greater than 0");
950:
951:     IERC20 token = IERC20(tokenAddress);
952:     uint256 balance = token.balanceOf(address(this));
953:     require(balance >= amount, "Not enough tokens in contract");
954:
955:     // "to" have been checked to be a non zero address
956:     bool success = token.transfer(to, amount);
957:     require(success, "Token transfer failed");
958:     emit ERC20TokenPulled(tokenAddress, to, amount);
959:   }
```

그러나 이 함수는 `approveMultipleUnstakes()` 함수를 통해 예치된 자산이며 사용자가 `unstake()`를 실행할 때 사용할 수 있어야 하는 `wrappedTokens`(예: `wTAO` 또는 `wCOMAI`)의 인출도 허용합니다. `safePullERC20` 함수가 `wrappedTokens`를 인출하도록 허용하면 이러한 토큰은 사용자 `unstake` 요청을 이행하는 데 필수적이므로 프로토콜 자산의 오용으로 이어질 수 있습니다.

필수 자산의 무단 인출을 방지하려면 `safePullERC20` 함수가 `wrappedTokens`를 제외하도록 제한해야 합니다. 이렇게 하면 사용자 `unstake` 요청을 위한 토큰을 계속 사용할 수 있습니다. 또는 `unstake`에 대해 승인된 금액이 `safePullERC20` 함수를 통해 인출될 수 없도록 하여 사용자 작업에 필요한 자산의 무결성을 유지하십시오.

# [L-07] `totalRsTAOMinted`가 `cap`을 초과할 수 있음

`cap` 변수는 `rsTAO` 및 `rsCOMAI`의 총 공급량을 제한하기 위한 것이며, 총 발행된 금액이 이 한도를 초과하지 않도록 `wrap()` 함수에서 확인됩니다.

```solidity
File: RivusTAO.sol
887:   function wrap(uint256 wtaoAmount) public nonReentrant checkPaused returns (uint256) {
...
...
894:     require(
895:       cap >= totalRsTAOMinted,
896:       "Deposit amount exceeds maximum"
897:     );
...
...
```

그러나 현재 구현에서는 특정 조건에서 `cap`을 초과할 수 있습니다. 다음 시나리오를 고려해 보십시오.

1. `cap`은 `10`으로 설정되고 `totalRsTAOMinted`는 `9`입니다.
2. 사용자가 `30 rsTAO`를 발행하는 `wrap()` 작업을 수행하면 `totalRsTAOMinted`가 `39`가 되어 `cap`인 `10`을 초과하게 됩니다.

이는 `cap` 확인이 새로운 `rsTAO`가 발행되어 `totalRsTAOMinted`에 추가되기 전에 수행되기 때문에 발생할 수 있습니다.

한도 확인에 발행될 `rsTAO` 금액을 포함하도록 유효성 검사 로직을 조정하는 것이 좋습니다. 이는 `rsTAO` 발행 계산 후로 한도 확인을 이동하여 수행할 수 있습니다. 이렇게 하면 새 토큰이 발행된 후 한도가 초과되지 않습니다.

```diff
  function wrap(uint256 wtaoAmount) public nonReentrant checkPaused returns (uint256) {
    ...
    ...
--  require(
--    cap >= totalRsTAOMinted,
--    "Deposit amount exceeds maximum"
--  );
    ...
    ...
    uint256 rsTAOAmount = getRsTAObyWTAO(wrapAmountAfterFee);

    // Perform token transfers
    _mintRsTAO(msg.sender, rsTAOAmount);
++  require(
++    cap >= totalRsTAOMinted,
++    "Deposit amount exceeds maximum"
++  );
    ...
    ...
  }
```

# [L-08] `getWTAOByrsTAOAfterFee()` 함수의 잘못된 공식

`getWTAOByrsTAOAfterFee()` 함수 코드에서 wTAO 금액을 계산한 다음 `unstakingFee`를 빼야 합니다. 현재 코드에서는 비율이 1이기 때문에 문제가 발생하지 않지만 일반적으로 공식이 잘못되었습니다. 이 문제는 COMAI 계약에도 존재합니다.

# [L-09] `unstakeRequests` 재사용 시 `maxUnstakeRequests`가 검증되지 않음

`maxUnstakeRequests` 변수는 사용자가 가질 수 있는 `unstakeRequests`의 수를 제한하는 데 도움이 됩니다. 이 변수는 `RivusTAO#L607-609`의 `requestUnstake()` 함수에서 유효성이 검사됩니다.

```solidity
File: RivusTAO.sol
555:   function requestUnstake(uint256 rsTAOAmt) public payable nonReentrant checkPaused {
...
...
606:     if (!added) {
607:       require(
608:         unstakeRequests[msg.sender].length < maxUnstakeRequests,
609:         "Maximum unstake requests exceeded"
610:       );
611:       unstakeRequests[msg.sender].push(
612:         UnstakeRequest({
613:           amount: rsTAOAmt,
614:           taoAmt: outWTaoAmt,
615:           isReadyForUnstake: false,
616:           timestamp: block.timestamp,
617:           wrappedToken: wrappedToken
618:         })
619:       );
...
629:     }
...
...
638:   }
```

문제는 `maxUnstakeRequests`가 `setMaxUnstakeRequest()` 함수를 통해 수정될 수 있어 이 변수 유효성 검사가 예상대로 작동하지 않을 수 있다는 것입니다. 다음 시나리오를 고려해 보십시오.

1. 소유자가 `setMaxUnstakeRequest()`를 값 `2`로 호출합니다.
2. `Account1`이 `10e9 wTAO tokens`를 예치하여 `10e9 rsTAO tokens`를 얻습니다.
3. 어떤 이유로 `Account1`이 `1e9 rsTAO`의 `unstaking`과 `2e9 rsTAO`의 또 다른 `unstaking`을 수행하여 `3e9 wTAO - fees`를 얻습니다.
4. 소유자가 `setMaxUnstakeRequest()`를 수정하여 값을 `1`로 줄입니다.
5. `Account1`이 별도의 트랜잭션에서 `3e9 rsTAO` 및 `4e9 rsTAO`의 또 다른 `unstaking`을 수행하여 `Account1`이 2개의 `requestStaking`을 갖게 됩니다. **이는 올바르지 않습니다.** `maxUnstakeRequests`가 `단계 4`에서 감소되었으므로 `Account1`은 1개의 `requestUnstaking`으로 제한되어야 합니다.

다음 테스트는 이전 시나리오를 보여줍니다.

```js
it("maxUnstakeRequests is not used when user have already unstakeRequests", async function () {
  // setup
  const { owner, account1, account2, account3, wTAO, rsTAO, wrsTAO } =
    await loadFixture(deployContracts);
  await wTAO.setBridge(owner.address);
  const froms = ["from0", "from1", "from1", "from1"];
  const tos = [
    owner.address,
    account1.address,
    account2.address,
    account3.address,
  ];
  const amounts = [
    ethers.parseUnits("100", 9),
    ethers.parseUnits("10", 9),
    ethers.parseUnits("10", 9),
    ethers.parseUnits("10", 9),
  ];
  await wTAO.bridgedTo(froms, tos, amounts);
  console.log(
    "\nAccount1 wTAO balance: ",
    ethers.formatUnits(await wTAO.balanceOf(account1.address), 9)
  );
  console.log(
    "Account1 rsTAO balance: ",
    ethers.formatUnits(await rsTAO.balanceOf(account1.address), 9)
  );
  //
  // 1. Owner set max unstakeRequest to 2 just for the testing purposes
  await rsTAO.setMaxUnstakeRequest(2);
  //
  // 2. Account1 stakes wTAO
  let amountToStake = ethers.parseUnits("10", 9);
  console.log("\nStaking", ethers.formatUnits(amountToStake, 9), "wTAO...");
  await wTAO.connect(account1).approve(rsTAO.target, amountToStake);
  await rsTAO.connect(account1).wrap(amountToStake);
  console.log(
    "Account1 wTAO balance: ",
    ethers.formatUnits(await wTAO.balanceOf(account1.address), 9)
  );
  console.log(
    "Account1 rsTAO balance: ",
    ethers.formatUnits(await rsTAO.balanceOf(account1.address), 9)
  );
  //
  // 3. Account1 request unstake 1 wTAO
  console.log("\nRequest unstake 1 wTAO...");
  await rsTAO.connect(account1).requestUnstake(ethers.parseUnits("1", 9), {
    value: ethers.parseEther("0.003"),
  });
  //
  // 4. Account1 request unstake 2 wTAO
  console.log("\nRequest unstake 2 wTAO...");
  await rsTAO.connect(account1).requestUnstake(ethers.parseUnits("2", 9), {
    value: ethers.parseEther("0.003"),
  });
  //
  // 5. Account1 unstake both `requestUnstakes`
  console.log("\nOwner approves all the `Account1` requestUnstake");
  const userRequests = [
    { user: account1.address, requestIndex: 0 },
    { user: account1.address, requestIndex: 1 },
  ];
  await wTAO.approve(rsTAO.target, ethers.parseUnits("10", 9));
  await rsTAO.approveMultipleUnstakes(userRequests);
  console.log("\nAccount1 unstake both requests");
  await rsTAO.connect(account1).unstake(0);
  await rsTAO.connect(account1).unstake(1);
  console.log(
    "Account1 wTAO balance: ",
    ethers.formatUnits(await wTAO.balanceOf(account1.address), 9)
  );
  console.log(
    "Account1 rsTAO balance: ",
    ethers.formatUnits(await rsTAO.balanceOf(account1.address), 9)
  );
  //
  // 6. Owner set max unstakeRequest to 1 but the account1 can still use 2 request unstakes
  await rsTAO.setMaxUnstakeRequest(1);
  console.log("\nRequest unstake 3 wTAO...");
  await rsTAO.connect(account1).requestUnstake(ethers.parseUnits("1", 9), {
    value: ethers.parseEther("0.003"),
  });
  console.log("\nRequest unstake 4 wTAO...");
  await rsTAO.connect(account1).requestUnstake(ethers.parseUnits("2", 9), {
    value: ethers.parseEther("0.003"),
  });
  let getUserRequests = await rsTAO.getUnstakeRequestByUser(account1.address);
  console.log(getUserRequests.length);
});
```

`RivusTAO#L577-L602`의 빈 `unstakeRequests`가 재사용되는 섹션 내에서 `maxUnstakeRequests`가 초과되지 않도록 하는 것이 좋습니다.

```solidity
File: RivusTAO.sol
555:   function requestUnstake(uint256 rsTAOAmt) public payable nonReentrant checkPaused {
...
...
576:     // Loop throught the list of existing unstake requests
577:     for (uint256 i = 0; i < length; i++) {
578:       uint256 currAmt = unstakeRequests[msg.sender][i].amount;
579:       if (currAmt > 0) {
580:         continue;
581:       } else {
582:         // If the curr amt is zero, it means
583:         // we can add the unstake request in this index
584:         unstakeRequests[msg.sender][i] = UnstakeRequest({
585:           amount: rsTAOAmt,
586:           taoAmt: outWTaoAmt,
587:           isReadyForUnstake: false,
588:           timestamp: block.timestamp,
589:           wrappedToken: wrappedToken
590:         });
591:         added = true;
592:         emit UserUnstakeRequested(
593:           msg.sender,
594:           i,
595:           block.timestamp,
596:           rsTAOAmt,
597:           outWTaoAmt,
598:           wrappedToken
599:         );
600:         break;
601:       }
602:     }
...
...
```
