# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Nexus-2023/NativeYield** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Nexus 정보

Nexus는 사용자가 선택한 L2에서 네이티브 수익을 입금하고 얻을 수 있도록 하며, Nexus는 수익을 얻을 수 있는 최상의 전략을 찾습니다.

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

_검토 커밋 해시_ - [a2c729ef6523d622510b17d2f87533d66cd70090](https://github.com/Nexus-2023/NativeYield/tree/a2c729ef6523d622510b17d2f87533d66cd70090)

_수정 검토 커밋 해시_ - [df7a1d651891904c0dde5fdbd0982a148ad75e6e](https://github.com/Nexus-2023/NativeYield/tree/df7a1d651891904c0dde5fdbd0982a148ad75e6e)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `DepositETHBera`
- `DepositUSDBera`
- `MessagingBera`
- `nETH`
- `nUSD`
- `AddressLibrary`
- `BytesArrayLibrary`
- `DSRStrategy`
- `LiDoStrategy`
- `WETHStrategy`
- `DepositETH`
- `DepositUSD`
- `Messaging`
- `StrategyManager`

# 발견 사항

# [C-01] `executeWithdrawal()`의 잘못된 토큰 전송

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`DepositUSD` 계약의 `executeWithdrawal` 함수는 사용자가 원래 예금한 USD 토큰(예: DAI, sDAI) 대신 ETH를 전송하도록 잘못 구현되었습니다.

계약은 안정적인 코인을 처리하도록 설계되었음에도 불구하고 `call{value : amount}`를 사용하여 ETH를 보내려고 시도합니다.

```solidity
    function executeWithdrawal(address _receiver, bytes32 _hash) external {
        if(msg.sender!=strategyExecutor) revert IncorrectStrategyExecutor(msg.sender);
        (bool success,bytes memory returndata) = _receiver.call{
            value:withdrawals[_receiver][_hash].amount,gas:10000
            }(""); // @audit send eth instead of DAI
        if(!success){
            if (returndata.length == 0) revert CallFailed();
            assembly {
                revert(add(32, returndata), mload(returndata))
            }
        }
        withdrawals[_receiver][_hash].withdrawalExecutionTime = block.timestamp;
        emit WithdrawalCompleted(_receiver, _hash);
    }
```

영향은 다음과 같습니다.

- 계약에는 ETH가 없으므로 전송은 항상 실패합니다.
- 사용자 자금은 사실상 계약에 잠기게 됩니다.
- DAI 또는 sDAI 토큰과 같은 의도된 토큰은 계약에 잠겨 있습니다.

## 권장 사항

사용자에게 올바른 토큰을 보내도록 `executeWithdrawal` 함수를 다시 구현하십시오.

# [C-02] 십진수 처리로 인한 잘못된 보상 계산

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

DSRStrategy 계약의 `getRewards` 함수에는 자산 잔액 및 보상을 계산할 때 십진수 처리 오류가 있습니다. 이 함수는 십진수를 고려하지 않고 토큰 잔액에 전환율을 곱하여 보상 계산이 심각하게 부풀려집니다.

```solidity
    function getRewards(address receiver,IStrategyManager.StrategyStruct memory _strategyBalance) public override view returns(uint256){
        uint256 balance_strategy = _strategyBalance.valueDeposited
            + _strategyBalance.rewardsEarned -_strategyBalance.valueWithdrawn;
        uint256 assetBalance = IERC20(TOKEN_ADDRESS).balanceOf(receiver)
            * sDAI(TOKEN_ADDRESS).convertToAssets(1e18);
        if (assetBalance>balance_strategy){
            return assetBalance - balance_strategy;
        }
        else{
            return 0;
        }
    }
```

영향은 보상 금액이 1e18 배로 부풀려지는 것입니다. 이로 인해 입금 계약에서 잘못된 주가 계산이 발생합니다. 그리고 신규 예금자는 인위적으로 높은 주가로 인해 받아야 할 것보다 적은 주식을 받습니다. 초기 예금자는 후기 예금자의 희생으로 불공정한 이점을 얻습니다.

## 권장 사항

십진수를 적절하게 처리하도록 `getRewards` 함수를 수정하십시오.

````diff
- uint256 assetBalance = IERC20(TOKEN_ADDRESS).balanceOf(receiver)*sDAI(TOKEN_ADDRESS).convertToAssets(1e18);
+ uint256 assetBalance = IERC20(TOKEN_ADDRESS).balanceOf(receiver)*sDAI(TOKEN_ADDRESS).convertToAssets(1e18) / 1e18;
````

# [C-03] 출금 추적 부족으로 인한 부정확한 주가

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`DepositETH` 및 `DepositUSD` 계약은 인출을 처리할 때 인출 카운터(`ethWithdrawn`/`stableWithdrawn`) 및 주식(`nETHMinted`, `nUSDMinted`)을 업데이트하지 못합니다.

이로 인해 전체 잔액에 인출된 자금의 일부가 포함되어 주가가 잘못 계산되기 때문에 회계가 올바르지 않게 됩니다.

주가는 총 잔액을 기준으로 계산됩니다.
```solidity
function _updateSharePrice(uint256 _rewardsEarned) internal {
    rewardsClaimed+=_rewardsEarned;
    uint256 totalEthBalance = (ethDeposited-ethWithdrawn+rewardsClaimed);
    if(nETHMinted==0){
        sharePrice = BASE_POINT;
    } else {
        sharePrice = (nETHMinted)*BASE_POINT/(ethDeposited-ethWithdrawn+rewardsClaimed);
    }
    emit SharePriceUpdated(sharePrice);
}
````

이로 인해 주가 계산이 잘못되어 주식에 이전 인출자 계정의 인출된 자금이 포함되어 신규 예금자가 더 적은 보상을 받을 수 있습니다.

다음 시나리오를 고려해 보겠습니다.

1. 초기 상태:

   - `sharePrice` = `1e18`
   - 모든 잔액 = `0` ETH

2. Alice가 `1` ETH 예금:

   - `1e18` 주식 획득
   - `ethDeposited` = `1e18`
   - `nETHMinted` = `1e18`

3. 시스템이 `0.05` ETH 보상 획득:

   - `rewardsClaimed` = `5e16` (0.05 ETH)
   - `totalEthBalance` = `1.05` ETH
   - `sharePrice` 업데이트 = `1e18 * 1e18 / 1.05 ETH = ~0.952e18`

4. Alice가 모든 것을 인출:

   - `~1.05` ETH 인출 (원금 + 보상)
   - **그러나 주식 및 ethWithdrawn은 업데이트되지 않음**
   - `sharePrice` 계산에 여전히 인출된 금액이 포함됨

5. Carol이 `1` ETH 예금:

   - `0.952e18` 주식 획득: `nETHShares` = `1e18 * 0.952e18 / 1e18` = `0.952e18`
   - `nETHMinted` += `0.952e18` = `1.952e18`
   - `ethDeposited` += `1e18` = `2e18`
   - `CAROL_SHARES` = `0.952e18`

6. 시스템이 또 다른 `0.05` ETH 보상 획득:

   - `rewardEarned` = `5e16` (0.05 ETH)
   - `rewardClaimed` += `5e16` = `1e17`
   - `totalEthBalance` = `ethDeposited - ethWithdrawn + rewardClaimed`
   - `totalEthBalance` = `2e18 - 0 + 1e17` = `2.1e18`
   - 새로운 `sharePrice` = `1.952e18 * 1e18 / 2.1e18` = `0.929e18`

7. Carol이 인출 청구:
   - `withdrawAmount` = `0.952e18 * 1e18 / 0.929e18` = `1.024e18`
   - Carol은 `1` ETH 예금에 대해 `1.024` ETH를 받습니다.
   - 그녀가 사용 가능한 모든 주식을 보유했기 때문에 모든 보상을 받아야 했습니다.

## 권장 사항

자금이 인출될 때 인출 및 주식에 대한 적절한 추적을 구현하십시오.

```diff
-function _withdrawFunds(address _receiver, uint256 _value) internal {
+function _withdrawFunds(address _receiver, uint256 _value, uint256 _shares) internal {
    bytes32 withdrawalHash = keccak256(abi.encodePacked(_receiver,_value,block.timestamp));
    WithdrawalStruct memory newWithdrawal;
    newWithdrawal.amount = _value;  //@audit withdraw amount
    newWithdrawal.shares = _shares;  //@audit withdraw shares
    newWithdrawal.withdrawalCreationTime = block.timestamp;
    withdrawals[_receiver][withdrawalHash] = newWithdrawal;
    emit WithdrawalRequestCreated(_receiver,withdrawalHash,newWithdrawal);
}
```

```diff
function executeWithdrawal(address _receiver, bytes32 _hash) external {
    uint256 amount = withdrawals[_receiver][_hash].amount;
    uitn256 sharesToBurn = withdrawals[_receiver][_hash].shares;

    // Update state before transfer
    ethWithdrawn += amount;
    nETHMinted -= sharesToBurn;
---
}
```

또한 주식을 자산으로 전환할 때 `_value`는 정수 나눗셈으로 인한 정밀도 손실을 겪을 수 있으며, 주의해서 처리하지 않으면 nETHMinted에 먼지(dust)가 남을 수 있습니다.

# [H-01] `onlyDeposit` 수정자의 잘못된 접근 제어 로직

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

Messaging 및 MessagingBera 계약 모두의 `onlyDeposit` 수정자에는 완전한 서비스 거부로 이어지는 접근 제어 구현에 논리적 오류가 포함되어 있습니다.

```solidity
    modifier onlyDeposit(){
        if(msg.sender!=depositETHBera
            || msg.sender!=depositUSDBera) revert NotDepositContract(msg.sender);
        _;
    }
```

```solidity
    modifier onlyDeposit(){
        if(msg.sender!=depositETH
            || msg.sender!=depositUSD) revert NotDepositContract(msg.sender);
        _;
    }
```

논리적 문제는 `msg.sender != A || msg.sender != B` 조건이 모든 msg.sender 값에 대해 항상 true로 평가된다는 것입니다. 왜냐하면 모든 주소는 두 개의 예금 주소 중 적어도 하나와 달라야 하기 때문입니다. 이것은 다음을 의미합니다.

- msg.sender가 `depositETH`와 같으면 `depositUSD`와 같지 않아야 합니다.
- msg.sender가 `depositUSD`와 같으면 `depositETH`와 같지 않아야 합니다.
- msg.sender가 둘 중 어느 것과도 같지 않으면 두 조건 모두 true입니다.

따라서 승인된 예금 계약의 합법적인 호출을 포함하여 모든 호출에 대해 되돌리기(revert)가 트리거되어 `sendMessage` 함수를 완전히 사용할 수 없게 됩니다.

## 권장 사항

OR(||) 연산자를 AND(&&)로 교체하여 의도한 접근 제어를 구현하십시오.

```diff
- if(msg.sender!=depositETHBera
    || msg.sender!=depositUSDBera) revert NotDepositContract(msg.sender);
+ if(msg.sender!=depositETHBera
    && msg.sender!=depositUSDBera) revert NotDepositContract(msg.sender);
```

# [H-02] DSR 계약에 DAI를 입금하기 전 승인 부족

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

DSRStrategy 계약의 `deposit` 함수는 DAI 토큰을 DSR(Dai Savings Rate) 계약에 예치하여 sDAI를 받으려고 시도하지만 필수 ERC20 승인 단계를 구현하지 못합니다. ERC20 토큰은 계약이 소유자를 대신하여 토큰을 전송하기 전에 승인이 필요하므로 입금 트랜잭션이 되돌려집니다.

```solidity
    function deposit(uint256 _value) public returns(uint256){
        sDAI(DSR_DEPOSIT_ADDRESS).deposit(_value,address(this));
        return 100;
    }
```

## 권장 사항

입금을 시도하기 전에 필요한 승인 단계를 추가하십시오.

```diff
    function deposit(uint256 _value) public returns(uint256){
+       IERC20(DAI_ADDRESS).approve(DSR_DEPOSIT_ADDRESS, _value);
        sDAI(DSR_DEPOSIT_ADDRESS).deposit(_value,address(this));
        return 100;
    }

```

# [H-03] 전략 계약의 출금 기능 부족

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`LidoStrategy` 및 `DSRStrategy` 계약은 수익을 얻기 위해 각각 `Lido` 및 `sDAI`에 `ETH` 및 `DAI` 예금을 관리할 책임이 있습니다.

이러한 전략은 `DepositETH` 및 `DepositUSD` 계약과 통합되어 사용자 예금을 처리하고 `Lido` 및 `sDAI`와의 상호 작용을 촉진합니다.

```solidity
function executeStrategies(StrategyExecution[] memory _executionData) external override {
    if(msg.sender!=strategyExecutor) revert IncorrectStrategyExecutor(msg.sender);
    unchecked{
        for(uint i=0;i<_executionData.length;i++){
            if(!IStrategyManager(strategyManager)
                .strategyExists(_executionData[i].strategy)) revert IncorrectStrategy(_executionData[i].strategy);
@>          (bool success, bytes memory returndata) =
                _executionData[i].strategy.delegatecall(_executionData[i].executionData);
            if(!success){
                if (returndata.length == 0) revert CallFailed();
                assembly {
                    revert(add(32, returndata), mload(returndata))
                }
            }
---
}
```

그러나 `LidoStrategy` 및 `DSRStrategy` 계약에는 출금 기능이 없습니다. 결과적으로 `DepositETH` 및 `DepositUSD` 계약은 누적된 보상과 함께 예치된 토큰을 검색할 수 없습니다.

이러한 제한으로 인해 프로토콜은 필요할 때 자산을 상환할 수 없으므로 사용자의 주식 상환을 이행하는 데 필요한 준비금을 유지할 수 없으며 결국 사용자가 자금에 대한 액세스 권한을 잃게 됩니다.

## 권장 사항

`LidoStrategy` 및 `DSRStrategy` 계약에 출금 기능을 구현하여 프로토콜이 누적된 보상과 함께 예치된 토큰을 검색할 수 있도록 하십시오.

또한 `Lido` 전략의 경우 Lido(1차 시장)에서의 인출은 요청 및 청구 프로세스가 인출 대기열과 통합되어야 하므로 프로토콜은 인출 요청 및 청구 프로세스를 처리하거나 2차 시장과의 인출을 통합하도록 선택해야 합니다.

# [H-04] 자금 손실 및 불공정 분배로 이어지는 오래된 주가 사용

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

주식 전환은 `DepositETH` 및 `DepositUSD` 계약에서 중요한 작업이며, 여기서 예금 및 인출은 현재 주가를 기준으로 합니다. 주가는 각각 `Lido` 및 `sDAI`에서 받은 수익으로 인해 시간이 지남에 따라 변동될 수 있습니다.

그러나 `DepositETH` 및 `DepositUSD` 계약은 사용자가 예금, 인출 또는 LayerZero 수수료 견적을 수행할 때 주가를 적절하게 업데이트하지 않습니다. 주가를 업데이트하지 못하면 오래된 주가를 사용하여 자금 손실 및 사용자에게 불공정한 주식 분배가 발생할 수 있습니다.

```solidity
function _depositETH(string memory _receiver, uint256 _value, uint32 _destID, uint256 _lzFee) internal {
    if(msg.value==_value+_lzFee){
@>      uint256 nETHShares= (sharePrice*_value)/BASE_POINT;
        bytes memory data = abi.encode(ASSET_ID,1, _receiver,nETHShares);
        ethDeposited+=_value;
        nETHMinted+=nETHShares;
        IMessaging(messageApp).sendMessage{value:_lzFee}(data, _destID,_lzFee);
    }else{
        revert IncorrectValue();
    }
}
```

```solidity
function messageReceivedL2(uint256 _id, address _receiver, uint256 _value) external override onlyMessageApp {
    if(_id==3){
@>      uint256 _valueUSDCWithdraw = _value*BASE_POINT/sharePrice;
        _withdrawFunds(_receiver, _valueUSDCWithdraw);
    }
    else {
        revert IncorrectTypeID(_id,msg.sender);
    }
}
```

예를 들어, 인출 중에 인출 요청과 실행 사이에 보상이 발생하면(따라서 주가 변경) 사용자는 새 보상을 고려하지 않는 오래된 주가를 사용하여 인출 금액을 계산합니다. 이로 인해 사용자는 자신이 받을 자격이 있는 것보다 적은 자금을 인출할 수 있습니다.

## 권장 사항

예금, 인출 및 LayerZero 수수료 견적과 같이 주식 계산과 관련된 함수를 업데이트하여 각 중요한 작업 중에 주가가 다시 계산되고 업데이트되도록 하는 것을 고려하십시오.

# [H-05] 총 잔액 계산 시 산술 언더플로

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`DepositETH`의 `_updateSharePrice()`는 [연산 우선순위](https://docs.soliditylang.org/en/v0.8.24/types.html#order)로 인해 언더플로가 발생할 수 있는 산술 표현식을 사용하여 총 ETH 잔액을 계산합니다.

```solidity
function _updateSharePrice(uint256 _rewardsEarned) internal {
    rewardsClaimed+=_rewardsEarned;
@>  uint256 totalEthBalance = (ethDeposited-ethWithdrawn+rewardsClaimed);
    if(nETHMinted==0){
        sharePrice = BASE_POINT;
    }
    else{
@>      sharePrice = (nETHMinted)*BASE_POINT/(ethDeposited-ethWithdrawn+rewardsClaimed);
    }
    emit SharePriceUpdated(sharePrice);
}
```

표현식

```solidity
ethDeposited-ethWithdrawn+rewardsClaimed
```

은 왼쪽에서 오른쪽으로 연산을 수행하므로 `rewardsClaimed`가 총 잔액을 양수로 만들더라도 `ethWithdrawn`이 `ethDeposited`보다 크면 언더플로가 발생할 수 있습니다.

이것은 정상적인 프로토콜 운영에서 `ethWithdrawn`이 자연스럽게 `ethDeposited`보다 커지기 때문에 특히 문제가 됩니다. 왜냐하면 인출 추적 금액에는 원금과 획득한 보상이 모두 포함되기 때문입니다.

`DepositUSD._updateSharePrice()`도 동일한 문제를 공유합니다.

## 권장 사항

언더플로를 방지하기 위해 산술 연산 순서를 변경하십시오.

```diff
function _updateSharePrice(uint256 _rewardsEarned) internal {
    rewardsClaimed += _rewardsEarned;
-    uint256 totalEthBalance = (ethDeposited-ethWithdrawn+rewardsClaimed);
+    uint256 totalEthBalance = ethDeposited + rewardsClaimed - ethWithdrawn;

    if(nETHMinted == 0) {
        sharePrice = BASE_POINT;
    } else {
-      sharePrice = (nETHMinted)*BASE_POINT/(ethDeposited-ethWithdrawn+rewardsClaimed);
+      sharePrice = (nETHMinted * BASE_POINT) / totalEthBalance;
    }
    emit SharePriceUpdated(sharePrice);
}
```

# [H-06] 주가 조작

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

프로토콜은 사용자 예금의 영구적인 손실을 초래할 수 있는 주가 조작 공격에 취약합니다. 공격자는 보상의 작은 "기부"를 통해 초기 상태를 조작하여 ETH 및 USD 볼트 모두에서 주가 계산 메커니즘을 악용할 수 있습니다.

공격은 다음 단계를 통해 수행될 수 있습니다.

1. 볼트가 새로 배포되었을 때(`nETHMinted` = 0), 공격자는 `DepositETH` 계약에 최소 금액(예: 100 wei)의 stETH를 입금합니다.
2. 공격자는 `updateRewards`를 트리거하여 `LidoStrategy`의 취약한 `getRewards` 함수를 호출합니다.

```solidity
    function getRewards(address receiver,IStrategyManager.StrategyStruct memory _strategyBalance) public override view returns(uint256){
        uint256 balance_strategy = _strategyBalance.valueDeposited +_strategyBalance.rewardsEarned -_strategyBalance.valueWithdrawn;
        if (IERC20(TOKEN_ADDRESS).balanceOf(receiver)>balance_strategy){
            return IERC20(TOKEN_ADDRESS).balanceOf(receiver) - balance_strategy; // @audit donate so rewards > 0
        }
        else{
            return 0;
        }
    }
```

3. 이로 인해 `rewardsClaimed` > 0이 되어 다음과 같은 결과가 발생합니다.

- `nETHMinted` = 0인 동안 `totalEthBalance` > 0
- `sharePrice` = (0 \* BASE_POINT) / (totalEthBalance) = 0

```solidity
    function _updateSharePrice(uint256 _rewardsEarned) internal {
        rewardsClaimed+=_rewardsEarned;
        uint256 totalEthBalance = (ethDeposited-ethWithdrawn+rewardsClaimed);
        if (totalEthBalance==0){
            sharePrice = BASE_POINT;
        }
        else{
            sharePrice = (nETHMinted)*BASE_POINT/(ethDeposited-ethWithdrawn+rewardsClaimed); // @audit nETHMinted = 0 leads to sharePrice = 0
        }
        emit SharePriceUpdated(sharePrice);
    }
```

다음으로 합법적인 사용자가 예를 들어 100 ETH를 입금할 때 `sharePrice`가 0이므로 해당 사용자가 받을 `nETHShares`도 0입니다. 즉, 이후의 모든 사용자 예금은 0 주식을 받습니다.

```solidity
    function _depositETH(string memory _receiver, uint256 _value, uint32 _destID, uint256 _lzFee) internal {
        if(msg.value==_value+_lzFee){
            uint256 nETHShares= (sharePrice*_value)/BASE_POINT; // @audit number of shares minted = 0
            bytes memory data = abi.encode(ASSET_ID,1, _receiver,nETHShares);
            ...
    }

    function _depositERC(address _tokenAddress, string memory _receiver, uint256 _value, uint32 _destID,uint256 _lzFee) internal onlyWhitelisted(_tokenAddress){
        ...
        uint256 nETHShares= (sharePrice*assetValue)/BASE_POINT; // @audit number of shares minted = 0
        bytes memory data = abi.encode(ASSET_ID,1, _receiver,nETHShares);
        ...
    }
```

DSRStrategy를 사용할 때 USD 볼트에도 동일한 취약점이 존재합니다.

이로 인해 사용자는 예치된 자금의 100%를 잃게 됩니다.

## 권장 사항

관리자는 기부 공격을 피하기 위해 초기 주식 금액을 발행합니다.

# [M-01] LayerZero 수수료 환불을 처리할 수 없음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`DepositUSD`, `DepositETHBera` 및 `DepositUSDBera` 계약은 네이티브 가스 토큰으로 수수료를 지불하는 책임이 있는 메시징 프로토콜인 LayerZero의 환불 주소 역할을 하도록 의도되었습니다. 그러나 이러한 계약은 이러한 환불을 받을 수 없으므로 초과 수수료가 전송될 때 되돌리기(revert)가 발생합니다.

`DepositUSD`, `DepositETHBera` 및 `DepositUSDBera` 계약은 구현 계약 역할을 하지만 환불은 프록시 계약으로 전송됩니다. 표준 [ERC1697Proxy](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/ERC1967/ERC1967Proxy.sol)는 모든 호출을 전달하는 폴백(fallback) 함수를 구현하여 구현 계약에 대한 위임 호출(delegate call)을 수행합니다.

따라서 구현 계약은 네이티브 가스 전송을 수신하는 기능을 제공해야 합니다.

## 권장 사항

이러한 계약이 사용자의 초과 환불 수수료를 받도록 의도된 경우 `DepositUSD`, `DepositETHBera` 및 `DepositUSDBera` 계약에 `receive()` 함수를 추가하는 것을 고려하십시오.

# [M-02] 인출 요청 생성 시 잠재적인 해시 충돌

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`DepositETH` 및 `DepositUSD` 계약 모두의 `_withdrawFunds` 함수는 동일한 사용자가 동일한 블록에서 여러 번 인출 요청을 하는 경우 충돌할 수 있는 인출 해시를 생성합니다. 해시는 수신자 주소, 인출 금액 및 블록 타임스탬프만 사용하여 생성됩니다.

```solidity
    function _withdrawFunds(address _receiver, uint256 _value) internal {
        bytes32 withdrawalHash = keccak256(abi.encodePacked(_receiver,_value,block.timestamp)); // @audit same hash if two withdrawals are made at the same time
        ...
        emit WithdrawalRequestCreated(_receiver,withdrawalHash,newWithdrawal);
    }
```

사용자가 동일한 블록에서 여러 인출 요청을 제출하는 경우:

- 서로 다른 인출 요청에 대해 동일한 해시가 생성됩니다.
- 두 번째 인출은 `withdrawals` 매핑에서 첫 번째 인출을 덮어씁니다.
- `executeWithdrawal`이 이 해시를 사용하여 `withdrawalExecutionTime`을 설정하므로 인출 중 하나가 손실됩니다.
- 시스템이 여러 의도된 인출 중 하나만 처리하면 사용자가 자금을 잃을 수 있습니다.

## 권장 사항

전역적으로 증가하는 인출 ID 사용:

```solidity
    uint256 withdrawalId = ++withdrawalCounter;
    bytes32 withdrawalHash = keccak256(abi.encodePacked(
        _receiver,
        _value,
        block.timestamp,
        withdrawalId
    ));
```

# [M-03] lido 슬래싱 이벤트로 인한 잠재적인 프로토콜 파산

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`DepositETH`는 슬래싱 이벤트로 인한 Lido stETH 잔액의 잠재적 감소를 적절하게 처리하지 못합니다. 이로 인해 주가가 부풀려져 사용자가 프로토콜의 실제 자금보다 더 많이 인출할 수 있게 되어 파산 위험이 있습니다.

문제를 입증하기 위해 `DepositETH.updateRewards()`는 현재 긍정적인 보상만 기록하지만 슬래싱이나 기타 부정적인 이벤트로 인해 `stETH` 잔액이 감소할 수 있는 시나리오는 처리하지 않습니다.

즉, 보상은 결코 줄어들지 않으며 주가는 부풀려진 보상 가치를 기준으로 계산됩니다.

`LidoStrategy.getRewards()`는 긍정적인 보상만 고려하며, 슬래싱이나 기타 부정적인 이벤트로 인해 stETH 잔액이 감소하는 상황은 무시합니다.

```solidity
// LidoStrategy.sol
function getRewards(address receiver,IStrategyManager.StrategyStruct memory _strategyBalance) public override view returns(uint256){
    uint256 balance_strategy = _strategyBalance.valueDeposited +_strategyBalance.rewardsEarned -_strategyBalance.valueWithdrawn;
@>    if (IERC20(TOKEN_ADDRESS).balanceOf(receiver)>balance_strategy){
        return IERC20(TOKEN_ADDRESS).balanceOf(receiver) - balance_strategy;
    }
    else{
        return 0;
    }
}
```

주가는 `rewardsClaimed`를 기준으로 계산되는데, 이는 부정적인 보상(예: 슬래싱)의 경우 감소하지 않습니다. 이로 인해 주가가 부풀려져 사용자가 실제로 가능한 것보다 더 많은 자금을 인출할 수 있습니다.

```solidity
function _updateSharePrice(uint256 _rewardsEarned) internal {
@>    rewardsClaimed+=_rewardsEarned;
    uint256 totalEthBalance = (ethDeposited-ethWithdrawn+rewardsClaimed);
    if (totalEthBalance==0){
        sharePrice = BASE_POINT;
    }
    else{
@>      sharePrice = (nETHMinted)*BASE_POINT/(ethDeposited-ethWithdrawn+rewardsClaimed);
    }
    emit SharePriceUpdated(sharePrice);
}
```

```solidity
function updateRewards(address[] memory _strategy) external{
    uint256 totalRewardsEarned;
    unchecked {
        for(uint i=0;i<_strategy.length;i++){
            if(!IStrategyManager(strategyManager).strategyExists(_strategy[i])) revert IncorrectStrategy(_strategy[i]);
            IStrategyManager.StrategyStruct memory tokenBalance = IStrategyManager(strategyManager).fetchStrategyDeposit(_strategy[i]);
@>          uint256 rewardsEarned = IStrategy(_strategy[i]).getRewards(address(this), tokenBalance);
            if(rewardsEarned>0){
                totalRewardsEarned+=rewardsEarned;
                IStrategyManager.StrategyStruct memory change;
                change.rewardsEarned=rewardsEarned;
                IStrategyManager(strategyManager).updateStrategy(_strategy[i], change);
            }
        }
    }
    _updateSharePrice(totalRewardsEarned);
    emit RewardsCollected(totalRewardsEarned);
}
```

문제를 설명하기 위해 다음 시나리오를 고려하십시오. 0. Alice는 `DepositETH` 계약에 1 stETH를 예치하고 1 ETH 주식을 발행합니다.

1. 프로토콜은 예금 시점에 1 ETH 가치의 1 stETH를 보유합니다.
2. `t1`에서 0.1 ETH의 보상이 발생하여 총 잔액이 1.1 ETH가 됩니다.
3. `DepositETH.updateRewards()`는 `LidoStrategy`에 대해 +0.1 ETH를 `rewardEarned`로 기록하고 `DepositETH`에 대한 `rewardsClaimed`를 업데이트합니다.
4. Lido 슬래싱 이벤트가 발생하여 stETH 잔액이 1.05 ETH로 감소합니다. (-0.05 ETH)
5. `DepositETH.updateRewards()`는 현재 잔액인 1.05 ETH가 이전 잔액인 1.1 ETH보다 작기 때문에 업데이트를 등록하지 않습니다.

```solidity
// LidoStrategy.sol
function getRewards(address receiver,IStrategyManager.StrategyStruct memory _strategyBalance) public override view returns(uint256){
    uint256 balance_strategy = _strategyBalance.valueDeposited +_strategyBalance.rewardsEarned -_strategyBalance.valueWithdrawn;
   if (IERC20(TOKEN_ADDRESS).balanceOf(receiver)>balance_strategy){ //@audit check 1.05 ETH > 1.10 ETH
        return IERC20(TOKEN_ADDRESS).balanceOf(receiver) - balance_strategy;
    }
    else{
@>      return 0; //@audit false: execute the else case
    }
}
```

6. 프로토콜은 모든 새로운 예금 및 들어오는 인출에 대해 원래 1.1 ETH를 주가에 계속 반영합니다.
7. Alice가 1 ETH 주식을 상환하면 실제 stETH 잔액은 1.05 ETH인데 Alice의 인출 요청은 1.1 ETH 금액이 인출될 것으로 기록됩니다.

## 권장 사항

stETH 잔액의 감소를 반영하여 Lido 슬래싱 이벤트를 설명하도록 주가 계산을 업데이트하는 것을 고려하십시오.

# [M-04] 예금 계약으로 잘못 전달된 LayerZero 수수료 환불

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

예금 및 인출에 대한 크로스 체인 메시징 비용을 커버하기 위한 LayerZero 수수료는 Ethereum 메인넷 또는 Berachain을 통해 트랜잭션을 시작한 발신자에게 환불되지 않습니다.

대신 발신자가 크로스 체인 메시징 비용을 부담하고 있음에도 불구하고 이 수수료는 예금 계약 자체(`DepositETH`, `DepositUSD`, `DepositETHBera` 및 `DepositUSDBera`)로 전달됩니다.

이로 인해 초과 수수료에 대한 보상을 받지 못하고 크로스 체인 트랜잭션을 시작하는 데 드는 전체 비용을 부담하는 발신자에게 불공정한 부담이 발생할 수 있습니다.

```solidity
@> function sendMessage(bytes memory _data, uint32 _destId, uint256 _lzFee) external override payable onlyDeposit{
    if(!destIdAvailable(_destId)) revert NotWhitelisted(_destId);
    MessagingReceipt memory receipt = _lzSend(
        _destId,
        _data,
        optionsDestId[_destId],
        MessagingFee(_lzFee, 0),
@>        payable(msg.sender)
    );
    emit MessageSent(_destId,_data,receipt);
}
```

`Messaging` 계약을 예로 들면 `sendMessage()`는 `DepositETH` 또는 `DepositUSD`에서만 호출할 수 있습니다. 결과적으로 환불 주소로 전달된 `msg.sender`는 원래 예금을 시작한 사용자가 아니라 `DepositETH` 또는 `DepositUSD` 계약입니다.

`DepositETHBera` 및 `DepositUSDBera` 계약에서 사용되는 `MessageBera.sendMessage()`도 동일한 문제를 공유합니다.

## 권장 사항

수수료가 사용자에게 적절하게 환불되도록 작업을 시작한 사용자 주소를 환불 주소로 전달하는 것을 고려하십시오.

```diff
- function sendMessage(bytes memory _data, uint32 _destId, uint256 _lzFee) external override payable onlyDeposit{
+ function sendMessage(bytes memory _data, uint32 _destId, uint256 _lzFee, address refundAddress) external override payable onlyDeposit{
    if(!destIdAvailable(_destId)) revert NotWhitelisted(_destId);
    MessagingReceipt memory receipt = _lzSend(
        _destId,
        _data,
        optionsDestId[_destId],
        MessagingFee(_lzFee, 0),
-        payable(msg.sender)
+       payable(refundAddress)
    );
    emit MessageSent(_destId,_data,receipt);
}
```

# [M-05] 잘못된 `TOKEN_ADDRESS` 및 `DSR_DEPOSIT_ADDRESS`

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`DSRStrategy`는 `DAI` 토큰을 입금하고 수익을 얻기 위한 `sDAI` 계약을 나타내기 위한 `TOKEN_ADDRESS` 및 `DSR_DEPOSIT_ADDRESS`에 대해 잘못된 계약 주소를 선언합니다.

```solidity
//mainnet
address public constant TOKEN_ADDRESS=0x3F1c547b21f65e10480dE3ad8E19fAAC46C95034;

address public constant DSR_DEPOSIT_ADDRESS=0x3F1c547b21f65e10480dE3ad8E19fAAC46C95034;
```

`DSRStrategy`는 주로 Ethereum 메인넷에서 DepositUSD 계약의 구현 전략으로 사용됩니다. 그러나 현재 주소는 코드가 없는 [계정](https://etherscan.io/address/0x3F1c547b21f65e10480dE3ad8E19fAAC46C95034)을 가리킵니다.

이로 인해 의도된 주소와의 모든 작업 및 상호 작용이 방지됩니다.

## 권장 사항

`TOKEN_ADDRESS` 및 `DSR_DEPOSIT_ADDRESS`를 올바른 주소로 업데이트하십시오.
