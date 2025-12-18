# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜에 대해 경험이 풍부한 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 시도합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Kittenswap/contracts** 리포지토리에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# KittenSwap 정보

Kittenswap은 맞춤형 게이지 및 팩토리 수정 사항이 있는 DEX로, LayerZero 및 Hyperlane 래핑 브리지 토큰을 지원하며 HyperEVM에 배포됩니다.
이번 감사는 투표 에스크로 토큰 잠금 시스템, 유권자가 제어하는 게이지 가중치 분배, 외부 뇌물 보상 메커니즘 및 집중된 유동성 풀 팩토리 관리에 중점을 두었습니다.

# 위험 등급 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음 (High)**   | 치명적 (Critical)     | 높음 (High)           | 중간 (Medium)      |
| **가능성: 중간 (Medium)** | 높음 (High)         | 중간 (Medium)         | 낮음 (Low)         |
| **가능성: 낮음 (Low)**    | 중간 (Medium)       | 낮음 (Low)            | 낮음 (Low)         |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터일 뿐이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 너무 드물거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

_검토 커밋 해시_ - [b191141c1efa9eda997c75460dab383db878e2b1](https://github.com/Kittenswap/contracts/commit/b191141c1efa9eda997c75460dab383db878e2b1)

_수정 검토 커밋 해시_ - [bb528db2ab79290f64788cb5a65f1e58cd3aa182](https://github.com/Kittenswap/contracts/commit/bb528db2ab79290f64788cb5a65f1e58cd3aa182)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `Voter`
- `VotingEscrow`
- `Gauge`
- `CLFactory`
- `FactoryRegistry`
- `CLGauge`
- `CLGaugeFactory`
- `ExternalBribe`

# 발견 사항 (Findings)

# [C-01] `split`은 임의의 `amount`로 `locked` 데이터를 생성하는 데 악용될 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`split` 내부의 분할 금액에 대한 `check`가 없기 때문에 사용자는 함수에 임의의 `_amount`를 제공할 수 있습니다. `locked.amount`는 `int128`로 저장되므로, 이로 인해 분할된 토큰 중 하나가 음수 값을 가질 수 있으며, 다른 하나는 제공된 전체 `_amount`로 설정될 수 있습니다.

```solidity
    function split(
        uint _from,
        uint _amount
    ) external returns (uint _tokenId1, uint _tokenId2) {
        address msgSender = msg.sender;

        require(_isApprovedOrOwner(msgSender, _from));
        require(attachments[_from] == 0 && !voted[_from], "attached");
        require(_amount > 0, "Zero Split");

        // burn old NFT
        LockedBalance memory _locked = locked[_from];
        int128 value = _locked.amount;
        locked[_from] = LockedBalance(0, 0);
        _checkpoint(_from, _locked, LockedBalance(0, 0));
        _burn(_from);

        // set max lock on new NFTs
        _locked.end = block.timestamp + MAXTIME;

        int128 _splitAmount = int128(uint128(_amount));
        // @audit - underflow is possible
>>>     _locked.amount = value - _splitAmount; // already checks for underflow here in ^0.8.0
        _tokenId1 = _createSplitNFT(msgSender, _locked);

>>>     _locked.amount = _splitAmount;
        _tokenId2 = _createSplitNFT(msgSender, _locked);

        emit Split(
            _from,
            _tokenId1,
            _tokenId2,
            msgSender,
            uint(uint128(locked[_tokenId1].amount)),
            uint(uint128(_splitAmount)),
            _locked.end,
            block.timestamp
        );
    }
```

이것은 처음에 소량의 토큰을 입금한 다음 임의의 금액으로 `split`을 호출하여 큰 `locked` 데이터를 생성함으로써 악용될 수 있습니다. 그런 다음 잠금 기간이 끝날 때 철회할 수 있으며, 임의의 양의 투표권과 잔액을 부여하여 게이지 및 뇌물에서 보상을 청구하는 데 사용할 수 있습니다.

PoC :

```solidity
    function testSplitVeKittenUnderflow() public {
        testDistributeVeKitten();

        vm.startPrank(user1);

        uint veKittenId = veKitten.tokenOfOwnerByIndex(user1, 0);
        (int128 lockedAmount, uint endTime) = veKitten.locked(veKittenId);

        (uint t1, uint t2) = veKitten.split(veKittenId, uint256(uint128(lockedAmount)) * 100);

        (int128 lockedAmountSplit, ) = veKitten.locked(t2);

        console.log("initial token to split locked amount :");
        console.log(lockedAmount);
        console.log("splitted token locked amount :");
        console.log(lockedAmountSplit);

        vm.stopPrank();
    }
```

로그 출력 :

```shell
Logs:
  initial token to split locked amount :
  100000000000000000000000000
  splitted token locked amount :
  10000000000000000000000000000
```

## 권장 사항

제공된 `_amount`가 분할 토큰의 잠금 금액을 초과하지 않는지 확인하십시오.

# [C-02] CLGauge가 KITTEN 보상을 스테이커가 아닌 자신에게 보냄

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

사용자가 NFP 토큰을 스테이킹하면 이러한 NFT의 소유권은 CLGauge 계약 자체로 이전됩니다. 스테이커가 KITTEN 보상을 청구할 때 `_getReward` 함수는 이러한 보상을 보상을 받을 자격이 있는 원래 스테이커가 아니라 NFP의 현재 소유자(CLGauge 계약)에게 잘못 보냅니다.

아래의 `_getReward` 함수에서 보상이 NFP 소유자에게 전송되고 있음을 알 수 있습니다. 스테이킹 후 소유자는 CLGauge 계약 자체입니다.

```solidity
    function _getReward(uint256 nfpTokenId) internal {
        (, , , , , int24 _tickLower, int24 _tickUpper, , , , , ) = nfp
            .positions(nfpTokenId);

        _updateRewardForNfp(nfpTokenId, _tickLower, _tickUpper);

        uint256 reward = rewards[nfpTokenId];
        address owner = nfp.ownerOf(nfpTokenId); // @audit owner is CLGauge contract

        if (reward > 0) {
            delete rewards[nfpTokenId];
            _safeApprove(kitten, address(this), reward);
            _safeTransferFrom(kitten, address(this), owner, reward);
            emit ClaimRewards(owner, reward);
        }
    }
```

이로 인해 스테이킹된 사용자 보상의 100%가 손실되고 게이지 계약으로 전송된 보상은 복구할 수 없습니다.

## 권장 사항

NFP 소유권에서 파생되지 않고 실제 스테이커의 주소를 매개변수로 받도록 `_getReward` 함수를 수정하십시오.

```diff
-    function _getReward(uint256 nfpTokenId) internal {
+    function _getReward(uint256 nfpTokenId, address owner) internal {
        ...
-       address owner = nfp.ownerOf(nfpTokenId);

        if (reward > 0) {
            delete rewards[nfpTokenId];
-            _safeApprove(kitten, address(this), reward);
-            _safeTransferFrom(kitten, address(this), owner, reward);
-            emit ClaimRewards(owner, reward);
+            _safeTransfer(kitten, owner, reward);
+            emit ClaimRewards(owner, reward);
        }
    }
```

# [H-01] 잘못된 다음 에포크 공급 사용으로 보상 계산에 영향

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

컨텍스트: 이것은 수정되지 않은 포크된 리포지토리의 문제입니다. (https://github.com/spearbit/portfolio/blob/master/pdfs/Velodrome-Spearbit-Security-Review.pdf 5.1.1 Reward calculates earned incorrectly on each epoch boundary).

`ExternalBribe` 계약에서 다음 줄은 이전 에포크의 공급을 검색하는 데 사용됩니다.

```solidity
_prev._prevSupply = supplyCheckpoints[getPriorSupplyIndex(_nextEpochStart + DURATION)].supply;
```

여기서 `_nextEpochStart + DURATION`은 공급을 검색하기 위한 인덱스를 결정하는 데 사용됩니다. 즉, 계약은 실제로 이전 에포크가 아닌 다음 에포크의 공급에 액세스하고 있습니다.

## 권장 사항

`_nextEpochStart + DURATION-1`을 사용하십시오.

# [H-02] 게이지 비활성화 시 청구 가능한 보상 손실

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`Voter` 계약의 `killGauge` 및 `reviveGauge` 함수를 사용하면 `emergencyCouncil`이 게이지를 비활성화하고 다시 활성화할 수 있습니다. 그러나 게이지가 종료되면 관련 청구 가능한 보상이 0으로 설정되어 게이지가 나중에 부활하더라도 누적된 보상이 영구적으로 손실됩니다.

계약에서 다음 코드 스니펫은 문제를 보여줍니다.

```solidity
    function killGauge(address _gauge) external {
        require(msg.sender == emergencyCouncil, "not emergency council");
        require(isAlive[_gauge], "gauge already dead");
        isAlive[_gauge] = false;
        claimable[_gauge] = 0; // Claimable rewards are reset to zero
        emit GaugeKilled(_gauge);
    }
```

게이지가 종료되면 해당 게이지에 대한 `claimable` 보상이 0으로 설정됩니다. 즉, 게이지를 종료하기 전에 누적된 보상은 영구적으로 손실됩니다. 나중에 게이지가 부활하더라도 이전에 누적된 보상은 복구할 수 없습니다.

또한 동시에 다른 게이지의 보상은 여전히 `totalWeight`를 기반으로 하므로 적절하게 조정되지 않아 보상 손실이 발생합니다.

```solidity
            uint _delta = _index - _supplyIndex; // see if there is any difference that need to be accrued
            if (_delta > 0) {
                uint _share = (uint(_supplied) * _delta) / 1e18; // add accrued difference for each supplied token
                if (isAlive[_gauge]) {
                    claimable[_gauge] += _share;
                }
```

```solidity
    function notifyRewardAmount(uint amount) external {
        _safeTransferFrom(base, msg.sender, address(this), amount); // transfer the distro in
        uint256 _ratio = (amount * 1e18) / totalWeight; // 1e18 adjustment is removed during claim
        if (_ratio > 0) {
            index += _ratio;
        }
        emit NotifyReward(msg.sender, base, amount);
    }

```

## 권장 사항

이 문제를 해결하려면 게이지가 부활할 때 `claimable` 보상을 복구할 수 있는 메커니즘을 구현하는 것이 좋습니다.

# [H-03] `ExternalBribe.earned`는 마지막 `tokenId` 체크포인트 이전의 보상을 건너뜀

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`ExternalBribe.earned` 내부에서는 먼저 `_endIndex - 1`까지 루프를 돌며 이전 보상을 획득한 금액에 포함할지 여부를 결정합니다.

```solidity
    function earned(address token, uint tokenId) public view returns (uint) {
        uint _startTimestamp = lastEarn[token][tokenId];
        if (numCheckpoints[tokenId] == 0) {
            return 0;
        }

        uint _startIndex = getPriorBalanceIndex(tokenId, _startTimestamp);
        uint _endIndex = numCheckpoints[tokenId] - 1;

        uint reward = 0;
        // you only earn once per epoch (after it's over)
        RewardCheckpoint memory prevRewards;

        prevRewards.timestamp = _bribeStart(_startTimestamp);

        _prev._prevSupply = 1;

        console.log("END INDEX : ");
        console.log(_endIndex);

        if (_endIndex > 0) {
>>>         for (uint i = _startIndex; i <= _endIndex - 1; i++) {
                _prev._prevTs = checkpoints[tokenId][i].timestamp;
                _prev._prevBal = checkpoints[tokenId][i].balanceOf;
                uint _nextEpochStart = _bribeStart(_prev._prevTs);
                // check that you've earned it
                // this won't happen until a week has passed
>>>             if (_nextEpochStart > prevRewards.timestamp) {
                    console.log("REWARD ADDED : ");
                    console.log(prevRewards.balance);
                    reward += prevRewards.balance;
                }

                prevRewards.timestamp = _nextEpochStart;
                _prev._prevSupply = supplyCheckpoints[
                    getPriorSupplyIndex(_nextEpochStart + DURATION)
                ].supply;
                prevRewards.balance =
                    (_prev._prevBal *
                        tokenRewardsPerEpoch[token][_nextEpochStart]) /
                    _prev._prevSupply;
            }
        }

        Checkpoint memory _cp0 = checkpoints[tokenId][_endIndex];
        (_prev._prevTs, _prev._prevBal) = (_cp0.timestamp, _cp0.balanceOf);

        uint _lastEpochStart = _bribeStart(_prev._prevTs);
        uint _lastEpochEnd = _lastEpochStart + DURATION;

        if (
            block.timestamp > _lastEpochEnd && _startTimestamp < _lastEpochEnd
        ) {
            SupplyCheckpoint memory _scp0 = supplyCheckpoints[
                getPriorSupplyIndex(_lastEpochEnd)
            ];
            _prev._prevSupply = _scp0.supply;
            reward += (_prev._prevBal *
                    tokenRewardsPerEpoch[token][_lastEpochStart]) /
                _prev._prevSupply;
        }

        return reward;
    }
```

그러나 로직에는 `_endIndex`가 포함되어야 합니다. 그렇지 않으면 그 전의 체크포인트를 확인해야 하는데, 그렇지 않으면 항상 건너뛰고 보상으로 계산되지 않습니다.

다음 테스트 파일을 리포지토리에 추가하십시오: https://gist.github.com/said017/b96daba163bf2e1eb101589bc541ce06.

테스트 실행 :

```shell
forge test --match-test testEpochCalculationIssue -vvv
```

로그 출력 :

```shell
Logs:

  Earned at end of first epoch: 100000000000000000000

  Earned at end of second epoch: 0

  Earned at end of third epoch: 100000000000000000000
```

## 권장 사항

`_endIndex`를 포함하도록 계산 로직을 변경하십시오.

```diff
    function earned(address token, uint tokenId) public view returns (uint) {
        uint _startTimestamp = lastEarn[token][tokenId];
        if (numCheckpoints[tokenId] == 0) {
            return 0;
        }

        uint _startIndex = getPriorBalanceIndex(tokenId, _startTimestamp);
        uint _endIndex = numCheckpoints[tokenId] - 1;

        uint reward = 0;
        // you only earn once per epoch (after it's over)
        RewardCheckpoint memory prevRewards;

        prevRewards.timestamp = _bribeStart(_startTimestamp);

        _prev._prevSupply = 1;

        console.log("END INDEX : ");
        console.log(_endIndex);

        if (_endIndex > 0) {
-            for (uint i = _startIndex; i <= _endIndex - 1; i++) {
+            for (uint i = _startIndex; i <= _endIndex ; i++) {
                _prev._prevTs = checkpoints[tokenId][i].timestamp;
                _prev._prevBal = checkpoints[tokenId][i].balanceOf;
                uint _nextEpochStart = _bribeStart(_prev._prevTs);
                // check that you've earned it
                // this won't happen until a week has passed
                if (_nextEpochStart > prevRewards.timestamp) {
                    console.log("REWARD ADDED : ");
                    console.log(prevRewards.balance);
                    reward += prevRewards.balance;
                }
/ ...
}
```

# [H-04] 위임 목록의 중복 `tokenId`로 투표가 부풀려질 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

다음 문제는 대리인을 이동할 때 발생하며 [Velodrome](https://github.com/spearbit/portfolio/raw/master/pdfs/Velodrome-Spearbit-Security-Review.pdf)의 Spearbit 감사에서 이미 식별되었습니다.

`_moveAllDelegates` 및 `_moveTokenDelegates` 내부에서 위임된 `tokenIds`는 `srcRep`(이전 위임자)에서 제거되고 `dstRep`(새 위임자)에 추가됩니다.

```solidity
    function _moveAllDelegates(
        address owner,
        address srcRep,
        address dstRep
    ) internal {
        // You can only redelegate what you own
        if (srcRep != dstRep) {
            if (srcRep != address(0)) {
                uint32 srcRepNum = numCheckpoints[srcRep];
                uint[] storage srcRepOld = srcRepNum > 0
                    ? checkpoints[srcRep][srcRepNum - 1].tokenIds
                    : checkpoints[srcRep][0].tokenIds;
                uint32 nextSrcRepNum = _findWhatCheckpointToWrite(srcRep);
                uint[] storage srcRepNew = checkpoints[srcRep][nextSrcRepNum]
                    .tokenIds;
                // All the same except what owner owns
                for (uint i = 0; i < srcRepOld.length; i++) {
                    uint tId = srcRepOld[i];
>>>                if (idToOwner[tId] != owner) {
                        srcRepNew.push(tId);
                    }
                }

>>>             numCheckpoints[srcRep] = srcRepNum + 1;
            }

            if (dstRep != address(0)) {
                uint32 dstRepNum = numCheckpoints[dstRep];
                uint[] storage dstRepOld = dstRepNum > 0
                    ? checkpoints[dstRep][dstRepNum - 1].tokenIds
                    : checkpoints[dstRep][0].tokenIds;
                uint32 nextDstRepNum = _findWhatCheckpointToWrite(dstRep);
                uint[] storage dstRepNew = checkpoints[dstRep][nextDstRepNum]
                    .tokenIds;
                uint ownerTokenCount = ownerToNFTokenCount[owner];
                require(
                    dstRepOld.length + ownerTokenCount <= MAX_DELEGATES,
                    "dstRep would have too many tokenIds"
                );
                // All the same
                for (uint i = 0; i < dstRepOld.length; i++) {
                    uint tId = dstRepOld[i];
>>>                 dstRepNew.push(tId);
                }
                // Plus all that's owned
                for (uint i = 0; i < ownerTokenCount; i++) {
                    uint tId = ownerToNFTokenIdList[owner][i];
>>>                 dstRepNew.push(tId);
                }

>>>             numCheckpoints[dstRep] = dstRepNum + 1;
            }
        }
    }
```

`_findWhatCheckpointToWrite`는 목록을 추가/제거할 체크포인트를 결정하는 데 사용됩니다.

```solidity
    function _findWhatCheckpointToWrite(
        address account
    ) internal view returns (uint32) {
        uint _timestamp = block.timestamp;
        uint32 _nCheckPoints = numCheckpoints[account];

        if (
            _nCheckPoints > 0 &&
            checkpoints[account][_nCheckPoints - 1].timestamp == _timestamp
        ) {
            return _nCheckPoints - 1;
        } else {
            return _nCheckPoints;
        }
    }
```

동일한 블록 내에서 작업이 호출되면 동일한 체크포인트가 위임 이동 작업에 사용됩니다.

이로 인해 문제가 발생할 수 있습니다. 다음 시나리오를 고려해 보십시오 (모든 작업은 동일한 블록에서 수행됨).

**Alice**
Num checkpoint = 1
TokenIds (checkpoint 0) = 1 , 2

**Bob**
Num checkpoint = 1
TokenIds (checkpoint 0) = 3, 4

TokenId 1은 alice에서 bob으로 이동되어 다음과 같은 상태가 됩니다.

**Alice**
Num checkpoint = 2
TokenIds (checkpoint 0) = 1 , 2
TokenIds (checkpoint 1) = 2

**Bob**
Num checkpoint = 2
TokenIds (checkpoint 0) = 3, 4
TokenIds (checkpoint 1) = 3, 4, 1

TokenId 2는 alice에서 bob으로 이동했지만 동일한 블록에서 수행되었으므로 다음과 같은 상태가 됩니다.

**Alice**
Num checkpoint = 3
TokenIds (checkpoint 0) = 1 , 2
TokenIds (checkpoint 1) = 2

**Bob**
Num checkpoint = 3
TokenIds (checkpoint 0) = 3, 4
TokenIds (checkpoint 1) = 3, 4, 1, 3, 4, 1, 2

여기에는 몇 가지 문제가 있습니다. 동일한 체크포인트를 사용하는 경우에도 체크포인트 수가 계속 증가하고 tokenId 2가 Alice의 최신 체크포인트에서 제거되지 않습니다. 그리고 Bob의 최신 체크포인트에는 `dstRepNew`가 비어 있다고 항상 가정하기 때문에 여러 중복 항목이 포함되어 있습니다.

## 권장 사항

작업이 동일한 블록 내에서 수행되는 시나리오를 고려하여 `_moveAllDelegates` 및 `_moveTokenDelegates`를 조정하는 것을 고려하십시오.

# [H-05] `_checkpoint()`의 공유 메모리로 인한 잘못된 `last_point.blk` 계산

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

VotingEscrow 계약의 `_checkpoint` 함수에서는 `initial_last_point`가 독립적인 복사본을 생성하는 대신 `last_point`에 대한 참조로 할당되는 문제가 있습니다. 이로 인해 시스템 전체의 투표권 계산에 영향을 미치는 잘못된 블록 번호 외삽 계산이 발생합니다.

```solidity
    function _checkpoint(
        uint _tokenId,
        LockedBalance memory old_locked,
        LockedBalance memory new_locked
    ) internal {
        ...
        Point memory initial_last_point = last_point; // @audit initial_last_point is a pointer which points to last_point instead of a copy
        uint block_slope = 0; // dblock/dt
        if (block.timestamp > last_point.ts) {
            block_slope =
                (MULTIPLIER * (block.number - last_point.blk)) /
                (block.timestamp - last_point.ts);
        }

        {
            uint t_i = (last_checkpoint / WEEK) * WEEK;
            for (uint i = 0; i < 255; ++i) {
                // Hopefully it won't happen that this won't get used in 5 years!
                // If it does, users will be able to withdraw but vote weight will be broken
                t_i += WEEK;
                int128 d_slope = 0;
                if (t_i > block.timestamp) {
                    t_i = block.timestamp;
                } else {
                    d_slope = slope_changes[t_i];
                }
                last_point.bias -=
                    last_point.slope *
                    int128(int256(t_i - last_checkpoint));
                last_point.slope += d_slope;
                if (last_point.bias < 0) {
                    // This can happen
                    last_point.bias = 0;
                }
                if (last_point.slope < 0) {
                    // This cannot happen - just in case
                    last_point.slope = 0;
                }
                last_checkpoint = t_i;
                last_point.ts = t_i; // @audit last_point.ts is updated to t_i
                last_point.blk =
                    initial_last_point.blk +
                    (block_slope * (t_i - initial_last_point.ts)) / // @audit the initial_last_point.ts will also be updated to t_i.
                    MULTIPLIER;
                _epoch += 1;
                if (t_i == block.timestamp) {
                    last_point.blk = block.number;
                    break;
                } else {
                    point_history[_epoch] = last_point;
                }
            }
        }

        ...
    }
```

루프에서 `last_point.ts`가 `t_i`로 업데이트되면 동일한 메모리 위치를 참조하기 때문에 `initial_last_point.ts`도 동일한 값으로 업데이트됩니다. 이로 인해 `(t_i - initial_last_point.ts)`가 0으로 평가되어 루프 반복 내내 `last_point.blk`가 변경되지 않은 상태로 유지됩니다.

이 문제는 직접적으로 다음에 영향을 미칩니다.

- 과거 투표권 계산의 정확성: 총 투표권 및 NFT당 투표권.
- 정확한 투표 가중치에 의존하는 거버넌스 기능.

## 권장 사항

`last_point`의 복사본을 생성하십시오.

```diff
    function _checkpoint(
        uint _tokenId,
        LockedBalance memory old_locked,
        LockedBalance memory new_locked
    ) internal {
        ...
-       Point memory initial_last_point = last_point;
+       Point memory initial_last_point = Point({
+         bias: last_point.bias,
+         slope: last_point.slope,
+         ts: last_point.ts,
+         blk: last_point.blk
        ...
});

```

# [H-06] `CLGauge` 생성 시 액세스 제어 부족으로 승인되지 않은 `Pools` 허용

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`CLGaugeFactory.createGauge` 함수는 누구나 호출할 수 있어 선점(front-running) 취약점이 발생합니다. 공격자는 유효한 `Voter.createCLGauge` 트랜잭션을 멤풀에서 모니터링하고 이를 선점하여 투표자 계약의 트랜잭션이 처리되기 전에 동일한 풀 주소로 `CLGaugeFactory.createGauge`를 호출할 수 있습니다.

```solidity
    function createGauge(
        address _pool,
        address _internal_bribe,
        address _kitten,
        bool _isPool
    ) external returns (address) {
        CLGauge newGauge = CLGauge(
            address(
                new ERC1967Proxy(
                    implementation,
                    abi.encodeCall(
                        CLGauge.initialize,
                        (
                            _pool,
                            _internal_bribe,
                            _kitten,
                            ve,
                            voter,
                            nfp,
                            _isPool
                        )
                    )
                )
            )
        );

        ICLPool(_pool).setGaugeAndPositionManager(address(newGauge), nfp);

        ...
    }

```

이 함수는 `ICLPool(_pool).setGaugeAndPositionManager(address(newGauge), nfp)`를 호출하는데, CLPool.setGaugeAndPositionManager 함수의 다음 확인으로 인해 풀당 한 번만 호출할 수 있습니다.

```solidity
    function setGaugeAndPositionManager(
        address _gauge,
        address _nft
    ) external override lock onlyGaugeFactory {
        require(gauge == address(0)); // @audit Can only be called once
        gauge = _gauge;
        nft = _nft;
    }
```

문제는 합법적인 `Voter.createCLGauge` 함수가 나중에 호출될 때 동일한 팩토리를 통해 게이지를 생성하려고 시도할 때 발생합니다. 함수는 되돌아갑니다.

```solidity
    function createCLGauge(
        address _poolFactory,
        address _pool
    ) external returns (address) {
        // ...
        address _gauge = ICLGaugeFactory(gaugefactory).createGauge(
            _pool,
            _internal_bribe,
            base,
            isPool
        );
        // ...
    }
```

이로 인해 의도한 게이지 생성 프로세스에 대한 서비스 거부가 효과적으로 생성되어 프로토콜의 거버넌스 및 보상 배포 메커니즘이 중단됩니다.

## 권장 사항

`createGauge` 함수가 투표자 계약에서만 호출될 수 있도록 제한하는 액세스 제어를 추가하십시오.

```diff
    function createGauge(
        address _pool,
        address _internal_bribe,
        address _kitten,
        bool _isPool
    ) external returns (address) {
+       require(msg.sender == voter, "Only voter can create gauge");
        CLGauge newGauge = CLGauge(
            address(
                new ERC1967Proxy(
                    implementation,
                    abi.encodeCall(
                        CLGauge.initialize,
                        (
                            _pool,
                            _internal_bribe,
                            _kitten,
                            ve,
                            voter,
                            nfp,
                            _isPool
                        )
                    )
                )
            )
        );

        ...
    }
```

# [H-07] 잠금 시간의 일관성 없는 반올림으로 인한 투표권 오류

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

VotingEscrow 계약에서 잠금 종료 시간은 `split`을 제외한 모든 함수에서 주 경계(목요일 00:00 UTC)로 일관되게 내림됩니다. 이 불일치로 인해 체크포인트 시스템이 중단되고 잘못된 투표권 계산이 발생합니다.

`_create_lock` 및 `increase_unlock_time`에서는 잠금 종료 시간이 주 경계로 명시적으로 반올림됩니다. 그러나 `split` 함수에서는 이 반올림이 누락되었습니다.

```solidity
    function split(
        uint _from,
        uint _amount
    ) external returns (uint _tokenId1, uint _tokenId2) {
        ...
        // set max lock on new NFTs
        _locked.end = block.timestamp + MAXTIME; // @audit the _locked.end isn't rounded down to week
        ...
    }
```

이 불일치는 기울기 변경을 적용할 때 계약의 체크포인트 메커니즘이 주 정렬 타임스탬프에 의존하기 때문에 심각한 영향을 미칩니다.

```solidity
    uint t_i = (last_checkpoint / WEEK) * WEEK;
    for (uint i = 0; i < 255; ++i) {
        t_i += WEEK;
        int128 d_slope = 0;
        if (t_i > block.timestamp) {
            t_i = block.timestamp;
        } else {
            d_slope = slope_changes[t_i]; // @audit t_i is aways on Thursday
        }
        // ...voting power calculations continue...
}
```

잠금이 만료되면 투표권을 줄이기 위해 총 기울기에서 해당 기울기를 제거해야 합니다. 그러나 잠금 만료 시간이 주 경계와 정렬되지 않으면 체크포인트 시스템은 정확한 만료 시간에 기울기 변화를 처리하지 않습니다. 전역 기울기는 항상 잘못될 것입니다.

사용자나 공격자는 `split`을 호출하여 계약의 체크포인트 계산을 깨뜨릴 수 있습니다. 이로 인해 투표권이 부풀려지고 과장된 투표 가중치를 기반으로 잘못된 보상이 분배됩니다.

## 권장 사항

나머지 코드베이스와 일치하도록 잠금 종료 시간을 주 경계에 맞추도록 분할 함수를 수정하십시오.

```diff
    function split(
        uint _from,
        uint _amount
    ) external returns (uint _tokenId1, uint _tokenId2) {
        ...
        // set max lock on new NFTs
-        _locked.end = block.timestamp + MAXTIME;
+        _locked.end = ((block.timestamp + MAXTIME) / WEEK) * WEEK;
        ...
    }
```

# [M-01] `balanceOfNFT()` 메서드의 일관성 없는 `VotingEscrow` 구현

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

이것은 포크된 리포지토리의 수정되지 않은 문제입니다 ([링크](https://github.com/spearbit/portfolio/blob/master/pdfs/Velodrome-Spearbit-Security-Review.pdf), 5.3.9 Inconsistent between balanceOfNFT, balanceOfNFTAt and \_balanceOfNFT functions).

> `balanceOfNFT` 함수는 `ownershipChange[_tokenId] == block.number`인 경우 0의 투표 균형을 반환하는 플래시 대출 보호를 구현합니다. 그러나 이것은 `balanceOfNFTAt` 및 `_balanceOfNFT` 함수에 일관되게 적용되지 않았습니다.

## 권장 사항

일관성을 유지하십시오. 확인이 더 이상 사용되지 않는 것 같으면 코드베이스에서 제거하십시오.

# [M-02] 뇌물 보상에 대한 수수료 처리의 모호한 조건

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Gauge` 계약에서 `_fees0`/`_fee1`이 `IBribe(internal_bribe).left(_token0)`/`IBribe(internal_bribe).left(_token1)`에 의해 반환된 금액보다 커야 한다는 조건은 불분명하고 오해의 소지가 있습니다. 이 조건은 처리되는 수수료와 배포 가능한 나머지 보상 간의 필요한 관계를 설정하지 않습니다.

`Gauge` 계약에서 다음 코드 스니펫은 뇌물 계약에 남은 금액과 `_fees0` 간의 관계를 확인합니다.

```solidity
if (
    _fees0 > IBribe(internal_bribe).left(_token0) &&
    _fees0 / DURATION > 0
) {
    fees0 = 0;
    _safeApprove(_token0, internal_bribe, _fees0);
    IBribe(internal_bribe).notifyRewardAmount(_token0, _fees0);
}
```

\_fees0 > IBribe(internal_bribe).left(\_token0) 조건은 다음과 같은 이유로 문제가 됩니다.

- 모호성: `_fees0` 변수는 처리 중이며 `notifyRewardAmount`에 전달될 새로운 보상을 나타내는 반면, `left`는 아직 분배되지 않은 보상의 양을 나타냅니다. 이 두 값 사이에는 고유한 연결이 없으므로 조건이 불분명합니다.
- 논리적 단절: 이 요구 사항은 새로운 수수료가 남은 보상을 초과해야 함을 의미하며, 이는 뇌물 계약에 새로운 보상을 알리는 프로세스와 논리적으로 상관 관계가 없습니다.

참고:
또한 `_claimable/DURAITON` 확인은 `DURATION` 대신 `epochDurationLeft`를 사용하는 `CLGauge`에서 확인된 것과 비교할 때 너무 엄격합니다.

```solidity
        if (
            _claimable > IGauge(_gauge).left(base) && _claimable / DURATION > 0
        ) {
            claimable[_gauge] = 0;
            IGauge(_gauge).notifyRewardAmount(base, _claimable);
            emit DistributeReward(msg.sender, _gauge, _claimable);
        }
```

```solidity
        if (block.timestamp >= periodFinish) {
            rewardRate = amount / epochDurationLeft;
            pool.syncReward({
                rewardRate: rewardRate,
                rewardReserve: amount,
                periodFinish: nextPeriodFinish
            });
        } else {
            uint256 newAmount = amount + epochDurationLeft * rewardRate;
            rewardRate = newAmount / epochDurationLeft;
            pool.syncReward({
                rewardRate: rewardRate,
                rewardReserve: newAmount,
                periodFinish: nextPeriodFinish
            });
        }
```

## 권장 사항

명확성을 개선하고 올바른 기능을 보장하려면 의도한 논리를 더 잘 반영하도록 조건을 수정하는 것이 좋습니다.

# [M-03] `_vote()`에서 유효하지 않은 풀 투표로 인한 가중치 손실 가능성

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Voter` 계약의 `_vote` 함수에서 제공된 `_poolVote` 배열에 유효하지 않은 풀이 포함된 경우 `_totalVoteWeight` 계산은 여전히 모든 `weights`의 합계를 사용합니다. 이로 인해 비효율적인 가중치 할당과 유효한 게이지에 대한 잠재적 보상 손실이 발생할 수 있습니다.

`_vote` 함수 내에서 다음 코드 스니펫이 실행됩니다.

```solidity
uint256 _weight = IVotingEscrow(_ve).balanceOfNFT(_tokenId);
uint256 _totalVoteWeight = 0;

for (uint i = 0; i < _poolCnt; i++) {
    _totalVoteWeight += _weights[i];
}

for (uint i = 0; i < _poolCnt; i++) {
    address _pool = _poolVote[i];
    address _gauge = gauges[_pool];

    if (isGauge[_gauge]) {
        uint256 _poolWeight = (_weights[i] * _weight) / _totalVoteWeight;
        // ... additional logic ...
    }
}
```

`_poolVote`에 유효한 게이지가 아닌 풀(이전에 추가되지 않음)이 포함된 경우 함수는 되돌리지 않고 해당 풀에 대한 계산을 건너뜁니다. 결과적으로 **처리되는 유효한 게이지는 잠재적으로 부풀려진 `_totalVoteWeight`를 제수로 사용하여 `_poolWeight`를 잘못 계산하게 됩니다.** 이는 유효한 투표가 NFT의 실제 가중치를 활용하지 못하여 잠재적 보상 손실로 이어질 수 있음을 의미합니다.

이 문제는 투표 과정에서 상당한 비효율성을 초래할 수 있습니다. 사용자가 잘못된 풀 투표를 입력하면 투표 가중치의 상당 부분이 낭비되어 보상이 줄어들 수 있습니다. `vote` 함수는 `onlyNewEpoch` 수정자에 의해 보호되므로 사용자는 동일한 에포크 내에서 실수를 수정할 수 없어 잠재적 손실을 더욱 악화시킵니다.

다음 시나리오를 고려하십시오.

사용자 입력: 사용자는 다음 입력을 제공합니다.

- `_poolVote`: 사용자가 투표하려는 풀 배열: `["PoolA", "PoolB", "PoolC"]`.
- `_weights`: 각 풀에 대한 해당 가중치: `[50, 50, 50]`.
- 게이지 상태: `gauges["PoolA"]`는 유효한 게이지, `gauges["PoolB"]`는 유효한 게이지, `gauges["PoolC"]`는 유효한 게이지가 아닙니다.
- NFT 가중치: 사용자의 NFT 가중치는 다음과 같이 검색됩니다.
  `_weight = IVotingEscrow(_ve).balanceOfNFT(_tokenId);` (이것이 `150`을 반환한다고 가정).

결과적으로:

- 사용된 총 가중치: 사용자는 150인 NFT 가중치로 투표할 의도였습니다.
- 실제 활용된 가중치: 100(`PoolA` 및 `PoolB`에서)만 효과적으로 활용됩니다.
- 가중치 낭비: `PoolC`(유효하지 않은 게이지)와 관련된 가중치는 낭비됩니다.

## 권장 사항

이 문제를 완화하려면 가중치 계산을 진행하기 전에 `_poolVote` 배열에 대한 유효성 검사를 구현하는 것이 좋습니다. 풀이 유효하지 않은 것으로 판명되면 함수는 그에 따라 계산을 되돌리거나 조정해야 합니다.

# [M-04] `VotingEscrow` 내부의 `checkpoint.timestamp`가 사용되지 않음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`VotingEscrow`의 `checkpoint` 설계는 실행 시 위임자의 정보를 기록하는 것입니다. 이는 최적화와 주어진 시간에 특정 주소에 `tokenId`가 얼마나 위임되었는지 쿼리하는 데 필요합니다.

```solidity
    function getPastVotesIndex(
        address account,
        uint timestamp
    ) public view returns (uint32) {
        uint32 nCheckpoints = numCheckpoints[account];
        if (nCheckpoints == 0) {
            return 0;
        }
        // First check most recent balance
        if (checkpoints[account][nCheckpoints - 1].timestamp <= timestamp) {
            return (nCheckpoints - 1);
        }

        // Next check implicit zero balance
        if (checkpoints[account][0].timestamp > timestamp) {
            return 0;
        }

        uint32 lower = 0;
        uint32 upper = nCheckpoints - 1;
        while (upper > lower) {
            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
            Checkpoint storage cp = checkpoints[account][center];
            if (cp.timestamp == timestamp) {
                return center;
            } else if (cp.timestamp < timestamp) {
                lower = center;
            } else {
                upper = center - 1;
            }
        }
        return lower;
    }

    function getPastVotes(
        address account,
        uint timestamp
    ) public view returns (uint) {
        uint32 _checkIndex = getPastVotesIndex(account, timestamp);
        // Sum votes
        uint[] storage _tokenIds = checkpoints[account][_checkIndex].tokenIds;
        uint votes = 0;
        for (uint i = 0; i < _tokenIds.length; i++) {
            uint tId = _tokenIds[i];
            // Use the provided input timestamp here to get the right decay
            votes = votes + _balanceOfNFT(tId, timestamp);
        }
        return votes;
    }
```

그러나 `checkpoints.timestamp`는 사용되지 않고 초기화되지 않아 이 정보에 의존하는 모든 작업이 제대로 작동하지 않습니다.

## 권장 사항

위임 정보가 이동되면 `checkpoints.timestamp`를 초기화하고 설정하십시오.

# [M-05] `Gauge`, `CLGauge`, `Bribes`에 구조 보상 기능 누락

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`notifyRewardAmount`를 통해 `Gauge`, `CLGauge` 및 Bribes 계약에 보상이 분배될 때 해당 보상 기간/에포크 동안 총 공급량이 0이 되어 보상이 분배되지 않을 수 있습니다.

```solidity
    function notifyRewardAmount(address token, uint amount) external lock {
        require(amount > 0);
        if (!isReward[token]) {
            require(
                IVoter(voter).isWhitelisted(token),
                "bribe tokens must be whitelisted"
            );
            require(
                rewards.length < MAX_REWARD_TOKENS,
                "too many rewards tokens"
            );
        }
        // bribes kick in at the start of next bribe period
        uint adjustedTstamp = getEpochStart(block.timestamp);
        uint epochRewards = tokenRewardsPerEpoch[token][adjustedTstamp];

        _safeTransferFrom(token, msg.sender, address(this), amount);
        tokenRewardsPerEpoch[token][adjustedTstamp] = epochRewards + amount;

        periodFinish[token] = adjustedTstamp + DURATION;

        if (!isReward[token]) {
            isReward[token] = true;
            rewards.push(token);
        }

        emit NotifyReward(msg.sender, token, adjustedTstamp, amount);
    }
```

그러나 해당 계약에는 보상을 구조할 수 있는 기능이 없어 분배된 보상이 영구적으로 갇히게 됩니다.

## 권장 사항

보상을 구조하는 기능을 추가하십시오.

# [M-06] `VotingEscrow._burn`은 승인된 지출자가 `withdraw()`를 사용하는 것을 방지함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`withdraw`, `merge`, `split` 작업 내부에는 `_isApprovedOrOwner`를 사용하는 확인이 있어 `tokenId`의 지출자 또는 운영자가 해당 작업을 수행할 수 있습니다.

```solidity
    function withdraw(uint _tokenId) external nonreentrant {
>>>     assert(_isApprovedOrOwner(msg.sender, _tokenId));
        require(attachments[_tokenId] == 0 && !voted[_tokenId], "attached");

        LockedBalance memory _locked = locked[_tokenId];
        require(block.timestamp >= _locked.end, "The lock didn't expire");
        uint value = uint(int256(_locked.amount));

        locked[_tokenId] = LockedBalance(0, 0);
        uint supply_before = supply;
        supply = supply_before - value;

        // old_locked can have either expired <= timestamp or zero end
        // _locked has only 0 end
        // Both can have >= 0 amount
        _checkpoint(_tokenId, _locked, LockedBalance(0, 0));

        assert(IERC20(token).transfer(msg.sender, value));

        // Burn the NFT
>>>     _burn(_tokenId);

        emit Withdraw(msg.sender, _tokenId, value, block.timestamp);
        emit Supply(supply_before, supply_before - value);
    }
```

그러나 `_burn` 함수 내부에서는 실제 `owner` 대신 `msg.sender`에서 토큰을 제거하려고 시도합니다.

```solidity
    function _burn(uint _tokenId) internal {
        require(
            _isApprovedOrOwner(msg.sender, _tokenId),
            "caller is not owner nor approved"
        );

        address owner = ownerOf(_tokenId);

        // Clear approval
        approve(address(0), _tokenId);
        // checkpoint for gov
        _moveTokenDelegates(delegates(owner), address(0), _tokenId);
        // Remove token
>>>     _removeTokenFrom(msg.sender, _tokenId);
        emit Transfer(owner, address(0), _tokenId);
    }
```

```solidity
    function _removeTokenFrom(address _from, uint _tokenId) internal {
        // Throws if `_from` is not the current owner
>>>     assert(idToOwner[_tokenId] == _from);
        // Change the owner
        idToOwner[_tokenId] = address(0);
        // Update owner token index tracking
        _removeTokenFromOwnerList(_from, _tokenId);
        // Change count tracking
        ownerToNFTokenCount[_from] -= 1;
    }
```

이것은 `tokenId`의 지출자와 운영자가 소유자를 대신하여 해당 작업을 수행하는 것을 방지합니다.

## 권장 사항

`_burn` 내부의 `_removeTokenFrom` 작업에 `msg.sender` 대신 `owner`를 제공하십시오.

# [M-07] `VotingEscrow.withdraw`는 승인된 `tokenId` 지출자에 의해 호출될 수 없음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`VotingEscrow.withdraw` 내부에는 `_isApprovedOrOwner` 확인이 있어 승인된 `tokenId` 지출자가 이 작업을 트리거할 수 있습니다.

```solidity
    function withdraw(uint _tokenId) external nonreentrant {
>>>     assert(_isApprovedOrOwner(msg.sender, _tokenId));
        require(attachments[_tokenId] == 0 && !voted[_tokenId], "attached");

        LockedBalance memory _locked = locked[_tokenId];
        require(block.timestamp >= _locked.end, "The lock didn't expire");
        uint value = uint(int256(_locked.amount));

        locked[_tokenId] = LockedBalance(0, 0);
        uint supply_before = supply;
        supply = supply_before - value;

        // old_locked can have either expired <= timestamp or zero end
        // _locked has only 0 end
        // Both can have >= 0 amount
        _checkpoint(_tokenId, _locked, LockedBalance(0, 0));

        assert(IERC20(token).transfer(msg.sender, value));

        // Burn the NFT
        _burn(_tokenId);

        emit Withdraw(msg.sender, _tokenId, value, block.timestamp);
        emit Supply(supply_before, supply_before - value);
    }
```

```solidity
    function _isApprovedOrOwner(
        address _spender,
        uint _tokenId
    ) internal view returns (bool) {
        address owner = idToOwner[_tokenId];
        bool spenderIsOwner = owner == _spender;
>>>     bool spenderIsApproved = _spender == idToApprovals[_tokenId];
        bool spenderIsApprovedForAll = (ownerToOperators[owner])[_spender];
        return spenderIsOwner || spenderIsApproved || spenderIsApprovedForAll;
    }
```

그러나 `_burn` 내부에서 `approve`가 호출되어 `tokenId` 승인을 재설정할 때 승인된 지출자가 작업을 수행하는 것을 방지하는 확인이 있습니다.

```solidity
    function approve(address _approved, uint _tokenId) public {
        address owner = idToOwner[_tokenId];
        // Throws if `_tokenId` is not a valid NFT
        require(owner != address(0));
        // Throws if `_approved` is the current owner
        require(_approved != owner);
        // Check requirements
        bool senderIsOwner = (idToOwner[_tokenId] == msg.sender);
        bool senderIsApprovedForAll = (ownerToOperators[owner])[msg.sender];
>>>     require(senderIsOwner || senderIsApprovedForAll);
        // Set the approval
        idToApprovals[_tokenId] = _approved;
        emit Approval(owner, _approved, _tokenId);
    }
```

## 권장 사항

`approve`를 호출하는 대신 `_burn` 함수 내부에서 직접 `idToApprovals`를 재설정하는 것을 고려하십시오.

# [M-08] merge 함수의 잘못된 공급 회계

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`supply` 상태 변수는 VotingEscrow 계약에 잠긴 KITTEN의 총량을 보여줍니다.

VotingEscrow 계약에서 두 NFT를 병합할 때 `from` NFT의 토큰(`value0`)이 `to` NFT로 전송됩니다. 그러나 NFT가 소각되는 동안 `_deposit_for`를 호출하기 전에 `supply`가 줄어들지 않습니다. 그런 다음 `_deposit_for` 내부에서 `supply`가 `value0`만큼 다시 증가합니다.

병합 작업이 발생할 때마다 `supply` 변수가 `value0`만큼 인위적으로 부풀려집니다. 해당 감소가 없으므로 이 과장은 영구적입니다. 이는 계약 내부의 내부 회계 오류를 나타냅니다.

```solidity
    function merge(uint _from, uint _to) external {
        ...
        locked[_from] = LockedBalance(0, 0);
        _checkpoint(_from, _locked0, LockedBalance(0, 0));
        _burn(_from);
        _deposit_for(_to, value0, end, _locked1, DepositType.MERGE_TYPE); // @audit No supply reduction before this call
    }
```

`_deposit_for` 함수에서 공급은 항상 증가합니다.

```solidity
    function _deposit_for(
        uint _tokenId,
        uint _value,
        uint unlock_time,
        LockedBalance memory locked_balance,
        DepositType deposit_type
    ) internal {
        LockedBalance memory _locked = locked_balance;
        uint supply_before = supply;

        supply = supply_before + _value; // @audit original_total_supply_including_value0 + value0
        ...
    }
```

## 권장 사항

`_deposit_for`를 호출하기 전에 `merge` 함수에서 `supply`를 줄이십시오.

```diff
    function merge(uint _from, uint _to) external {
        ...
        locked[_from] = LockedBalance(0, 0);
        _checkpoint(_from, _locked0, LockedBalance(0, 0));
        _burn(_from);
+       supply -= value0;
        _deposit_for(_to, value0, end, _locked1, DepositType.MERGE_TYPE);
    }
```

# [M-09] 비동기화로 인해 배출 없이 뇌물을 받을 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

BribeVotingReward가 EPOCH_- END - 1의 투표권을 기반으로 뇌물을 수여한다는 사실 때문에, 그러나 Minter.update_period() 및 Voter.distribute()는 그 이후에 호출될 수 있으므로, 일부 유권자는 가중치가 배출에 영향을 미치기 전에 투표를 변경하여 유권자가 뇌물을 받게 하지만 뇌물 제공 프로토콜은 게이지가 배출을 받지 못하게 할 수 있습니다.

## 권장 사항

프로토콜이 배포를 트리거할 수 있도록 투표 창 이후 1시간을 허용합니다.

# [M-10] split()은 원래 소유자가 아닌 msg.sender에게 NFT를 발행함

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

VotingEscrow 구현에서 split() 함수는 msg.sender가 원래 소유자인지 아니면 승인된 운영자인지에 관계없이 msg.sender에게 새 NFT를 발행합니다. 이는 소유권을 보존하고 \_isApprovedOrOwner()에 의존하여 작업을 허용하지만 소유권은 변경하지 않는 merge(), increase_amount(), increase_unlock_time()과 같은 다른 함수의 동작과 일치하지 않습니다.

```solidity
    function split(
        uint _from,
        uint _amount
    ) external returns (uint _tokenId1, uint _tokenId2) {
        address msgSender = msg.sender;
        require(_isApprovedOrOwner(msgSender, _from));
        //

        int128 _splitAmount = int128(uint128(_amount));
        _locked.amount = value - _splitAmount; // already checks for underflow here in ^0.8.0
        _tokenId1 = _createSplitNFT(msgSender, _locked);

        _locked.amount = _splitAmount;
        _tokenId2 = _createSplitNFT(msgSender, _locked);
```

## 권장 사항

split() 함수를 수정하고 새로운 veNFT를 발행할 때 NFT의 원래 소유자를 사용하십시오.
# [M-11] 사용자는 poke()가 투표권을 업데이트하는 것을 막을 수 있음

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

poke() 함수는 누구나 현재 투표권에 따라 사용자의 투표 할당을 업데이트할 수 있도록 설계되었습니다. 이는 vote()를 통해 투표를 다시 하지 않는 사용자가 잔액이 줄어들지 않은 것처럼 계속 보상을 받을 수 있기 때문에 중요합니다.
그러나 원래 투표에 소량의 가중치를 도입하여 성공적인 poke를 방지하도록 시스템을 악용할 수 있습니다.

```solidity
    function _vote(
        uint _tokenId,
        address[] memory _poolVote,
        uint256[] memory _weights
    ) internal {
        _reset(_tokenId);
        uint _poolCnt = _poolVote.length;
        uint256 _weight = IVotingEscrow(_ve).balanceOfNFT(_tokenId);

        for (uint i = 0; i < _poolCnt; i++) {
            address _pool = _poolVote[i];
            address _gauge = gauges[_pool];
//
            if (isGauge[_gauge]) {
                uint256 _poolWeight = (_weights[i] * _weight) / _totalVoteWeight;
                require(_poolWeight != 0);
```

악의적인 사용자는 다음을 수행할 수 있습니다.

- 한 풀에 아주 작은 가중치(예: 1단위)를 받고 나머지는 다른 풀로 가는 초기 투표를 합니다.
- 시간이 지남에 따라 사용자의 투표권이 감소함에 따라(예: 잠금 만료 임박), 작은 가중치 풀에 대한 결과적인 `_poolWeight`는 0으로 내림됩니다.
- 나중에 poke()가 호출되면 동일한 투표 구성을 다시 적용하려고 시도합니다. 0 가중치 조건으로 인해 다음 확인에서 되돌아갑니다.

```solidity
uint256 _poolWeight = (_weights[i] * _weight) / _totalVoteWeight;
require(_poolWeight != 0,);
```

- 결과적으로 poke()가 실패하고 사용자의 사용된 투표 가중치는 오래된 상태로 유지되어 실제 감소된 투표권에 비해 초과 뇌물 보상을 받을 수 있습니다.

POC:

```solidity
    function testPokeFail()
        public
        returns (address pairGauge, address poolGauge)
    {
        (address pair, address pool) = testCreateGauge();

        (pairGauge, poolGauge) = (voter.gauges(pair), voter.gauges(pool));

        (uint128 gaugeFees0, uint128 gaugeFees1) = ICLPool(pool).gaugeFees();

        vm.startPrank(user1);

        // uint256 bal = veKitten.balanceOf(user1);
        uint256 veKittenId = veKitten.tokenOfOwnerByIndex(user1, 0);
        uint256 balanceWeight = veKitten.balanceOfNFT(veKittenId);

        address[] memory voteList = new address[](2);
        voteList[0] = pair;
        voteList[1] = pool;
        uint256[] memory weightList = new uint256[](2);
        weightList[0] = 1;
        weightList[1] = balanceWeight - 1;

        //@audit pool weight would be exactly 1
        // _poolWeight = (_weights[i] * _weight) / _totalVoteWeight;
        // 1= 1*balanceWeight /(balanceWeight - 1 + 1)
        voter.vote(veKittenId, voteList, weightList);

        vm.stopPrank();

        vm.warp(block.timestamp + 2 weeks);
        //@audit weight round down to 0 and fail
        vm.expectRevert();
        voter.poke(veKittenId);
    }
```

## 권장 사항

0 `_poolWeight`에서 되돌리는 대신 해당 풀을 건너뛰십시오.

`if (_poolWeight == 0) continue;
`

# [M-12] VotingEscrow를 병합하거나 철회하면 보상 없이 토큰이 소각됨

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

VotingEscrow의 merge(), split(), withdraw() 함수는 뇌물 시스템(getReward를 통해)에서 보류 중인 보상을 청구하지 않고 사용자의 NFT를 소각합니다. 뇌물 보상을 청구하려면 NFT의 소유권(isApprovedOrOwner 확인)이 필요하므로 NFT를 소각하면 나중에 청구되지 않은 보상에 액세스할 수 없습니다.

merge() 함수에서:

```
locked[_from] = LockedBalance(0, 0);
_checkpoint(_from, _locked0, LockedBalance(0, 0));
_burn(_from);
```

사용자가 뇌물 보상을 청구하기 위해 호출해야 하는 getReward() 함수는 소유권을 확인합니다.

`require(IVotingEscrow(_ve).isApprovedOrOwner(msg.sender, tokenId));
`
NFT를 소각하면 이 소유권이 제거되어 나중에 getReward를 호출할 수 없습니다.

## 권장 사항

nft를 직접 소각하는 대신 토큰 금액을 재설정하십시오.

# [M-13] 체크포인트 공급이 0일 때 earned()에서 잠재적 되돌리기

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`ExternalBribe.earned` 내부에서 보상은 `tokenId`의 체크포인트 잔액에 `tokenRewardsPerEpoch`를 곱한 다음 체크포인트의 총 공급량으로 나누어 계산됩니다.

```solidity
    function earned(address token, uint tokenId) public view returns (uint) {
        uint _startTimestamp = lastEarn[token][tokenId];
        if (numCheckpoints[tokenId] == 0) {
            return 0;
        }

        uint _startIndex = getPriorBalanceIndex(tokenId, _startTimestamp);
        uint _endIndex = numCheckpoints[tokenId] - 1;

        uint reward = 0;
        // you only earn once per epoch (after it's over)
        RewardCheckpoint memory prevRewards;
        prevRewards.timestamp = _bribeStart(_startTimestamp);

        PrevData memory _prev;
        _prev._prevSupply = 1;

        if (_endIndex > 0) {
            for (uint i = _startIndex; i <= _endIndex - 1; i++) {
                _prev._prevTs = checkpoints[tokenId][i].timestamp;
                _prev._prevBal = checkpoints[tokenId][i].balanceOf;
                uint _nextEpochStart = _bribeStart(_prev._prevTs);
                // check that you've earned it
                // this won't happen until a week has passed
                if (_nextEpochStart > prevRewards.timestamp) {
                    console.log("REWARD ADDED : ");
                    console.log(prevRewards.balance);
                    reward += prevRewards.balance;
                }

                prevRewards.timestamp = _nextEpochStart;
                _prev._prevSupply = supplyCheckpoints[
                    getPriorSupplyIndex(_nextEpochStart + DURATION)
                ].supply;
                prevRewards.balance =
                    (_prev._prevBal *
                        tokenRewardsPerEpoch[token][_nextEpochStart]) /
>>>                 _prev._prevSupply;
            }
        }

        Checkpoint memory _cp0 = checkpoints[tokenId][_endIndex];
        (_prev._prevTs, _prev._prevBal) = (_cp0.timestamp, _cp0.balanceOf);

        uint _lastEpochStart = _bribeStart(_prev._prevTs);
        uint _lastEpochEnd = _lastEpochStart + DURATION;

        if (
            block.timestamp > _lastEpochEnd && _startTimestamp < _lastEpochEnd
        ) {
            SupplyCheckpoint memory _scp0 = supplyCheckpoints[
                getPriorSupplyIndex(_lastEpochEnd)
            ];
            _prev._prevSupply = _scp0.supply;

            reward += (_prev._prevBal *
                    tokenRewardsPerEpoch[token][_lastEpochStart]) /
>>>             _prev._prevSupply;
        }

        return reward;
    }
```

그러나 계산된 에포크의 `supplyCheckpoints`가 0인 경우는 고려되지 않았습니다. 이는 poke가 호출되고 해당 체크포인트 내부의 모든 계산된 잔액이 0 공급이 되는 시나리오에서 가능합니다.

코드베이스에 다음 테스트 파일을 추가하십시오: https://gist.github.com/said017/f6b0674c8f992c07c69ec1c4cffa3630.

테스트 실행:

```shell
forge test --match-test testEpochDivZero -vvv
```

## 권장 사항

0인 경우 `_prev._prevSupply`를 1로 설정하십시오.

# [M-14] `CLGauge.claimFees` 내부의 잘못된 확인

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`CLGauge.claimFees` 내부에서는 `pool.collectFees`를 호출하고 반환된 `claimed0` 및 `claimed1`이 0보다 크면 수수료로 간주합니다.

```solidity
    function _claimFees() internal returns (uint claimed0, uint claimed1) {
        if (!isForPair) return (0, 0);
        (claimed0, claimed1) = pool.collectFees();
>>>     if (claimed0 > 0 || claimed1 > 0) {
            uint _fees0 = fees0 + claimed0;
            uint _fees1 = fees1 + claimed1;

            if (
                _fees0 > IBribe(internal_bribe).left(token0) &&
                _fees0 / ProtocolTimeLibrary.WEEK > 0
            ) {
                fees0 = 0;
                _safeApprove(token0, internal_bribe, _fees0);
                IBribe(internal_bribe).notifyRewardAmount(token0, _fees0);
            } else {
                fees0 = _fees0;
            }
            if (
                _fees1 > IBribe(internal_bribe).left(token1) &&
                _fees1 / ProtocolTimeLibrary.WEEK > 0
            ) {
                fees1 = 0;
                _safeApprove(token1, internal_bribe, _fees1);
                IBribe(internal_bribe).notifyRewardAmount(token1, _fees1);
            } else {
                fees1 = _fees1;
            }

            emit ClaimFees(msg.sender, claimed0, claimed1);
        }
    }
```

그러나 `pool` 내부에서는 비어 있는 경우 수수료 금액을 1로 설정합니다.

```solidity
    function collectFees()
        external
        override
        lock
        onlyGauge
        returns (uint128 amount0, uint128 amount1)
    {
        amount0 = gaugeFees.token0;
        amount1 = gaugeFees.token1;
        if (amount0 > 1) {
            // ensure that the slot is not cleared, for gas savings
>>>         gaugeFees.token0 = 1;
            TransferHelper.safeTransfer(token0, msg.sender, --amount0);
        }
        if (amount1 > 1) {
            // ensure that the slot is not cleared, for gas savings
>>>         gaugeFees.token1 = 1;
            TransferHelper.safeTransfer(token1, msg.sender, --amount1);
        }

        emit CollectFees(msg.sender, amount0, amount1);
    }
```

즉, 현재 수수료가 비어 있으면 `CLGauge`가 `fees0` 및 `fees1`에 수수료 1을 잘못 추가하여 결국 잔액 부족으로 인해 되돌리기가 발생합니다.

## 권장 사항

`CLPool` 내부의 다음 줄을 조정하십시오.

```diff
    function collectFees() external override lock onlyGauge returns (uint128 amount0, uint128 amount1) {
        amount0 = gaugeFees.token0;
        amount1 = gaugeFees.token1;
        if (amount0 > 1) {
            // ensure that the slot is not cleared, for gas savings
            gaugeFees.token0 = 1;
            TransferHelper.safeTransfer(token0, msg.sender, --amount0);
-        }
+        } else {
+           --amount0;
+      }
        if (amount1 > 1) {
            // ensure that the slot is not cleared, for gas savings
            gaugeFees.token1 = 1;
            TransferHelper.safeTransfer(token1, msg.sender, --amount1);
-        }
+        } else {
+           --amount1;
+      }

        emit CollectFees(msg.sender, amount0, amount1);
    }
```

# [M-15] MAX_DELEGATES = 1024에서 토큰 위임에 의한 DOS 공격

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

VotingEscrow 계약의 delegate 함수를 사용하면 모든 사용자가 투표권을 다른 주소로 위임할 수 있습니다. 그러나 위임된 토큰 정보는 MAX_DELEGATES = 1024로 제한된 크기의 배열에 저장됩니다. 이 제한은 transfer, withdraw 또는 burn과 같은 작업 중에 과도한 가스 사용을 방지하도록 설계되었습니다.

HyperEVM의 듀얼 블록 아키텍처에서 이 제약 조건은 더 심각한 영향을 미칩니다.

Fast blocks: 2초 블록 시간, 2M 가스 제한.

Slow blocks: 1분 블록 시간, 30M 가스 제한.
NFT가 1024개의 위임을 받은 사용자는 transfer 또는 withdrawal과 같은 특정 작업을 수행하는 데 ~23M 가스가 필요합니다. 빠른 블록에서 이러한 작업을 시도하면 빠른 블록의 가스 한도(2M)가 필요한 가스보다 훨씬 낮기 때문에 항상 실패합니다. 사용자가 트랜잭션을 느린 블록에서 처리되도록 명시적으로 구성하지 않는 한 작업은 확인되지 않은 상태로 유지되거나 무기한 되돌려집니다.
• 짧은 토큰 목록이 있는 주소에서 긴 토큰 목록이 있는 주소로 위임하는 것이 더 저렴합니다. =>
만약 누군가 새로운 주소, 새로운 잠금을 생성하고 피해자에게 위임하여 피해자의 주소를 공격하려고 한다면, 공격자가 가스 한도에 도달할 때쯤이면 피해자는 철회/전송/위임할 수 없습니다.

## 권장 사항

비대해진 위임 배열이 빠른 블록 제한을 초과하지 않도록 MAX_DELEGATES 값을 1024에서 줄이십시오.

# [M-16] `ExternalBribe`에서 이중 보상 청구

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`ExternalBribe` 계약에서 악의적인 사용자는 `ExternalBribe::earned()` 함수의 결함 있는 논리로 인해 동일한 에포크에서 동일한 `tokenId`에 대해 보상을 두 번 청구할 수 있습니다. 이 문제로 인해 사용자는 단일 에포크에서 여러 번 보상을 받을 수 있으며, 이로 인해 보상 고갈로 인해 다른 사용자가 정당한 보상을 청구할 수 없게 될 수 있습니다.

문제는 루프 내부의 `prevRewards.timestamp` 필드 업데이트에 있습니다. 계약은 `_nextEpochStart <= prevRewards.timestamp`인 경우에도 `prevRewards.timestamp`를 **무조건** 업데이트합니다. 이로 인해 보상 누적 로직이 깨져 이전에 청구된 에포크의 보상이 다시 포함될 수 있습니다.

`ExternalBribe::earned()` 함수:

```solidity
function earned(address token, uint tokenId) public view returns (uint) {
    ...
    uint _startIndex = getPriorBalanceIndex(tokenId, _startTimestamp);
    uint _endIndex = numCheckpoints[tokenId] - 1;

    uint reward = 0;
    // you only earn once per epoch (after it's over)
    RewardCheckpoint memory prevRewards;
    prevRewards.timestamp = _bribeStart(_startTimestamp);

    PrevData memory _prev;
    _prev._prevSupply = 1;

    if (_endIndex > 0) {
        for (uint i = _startIndex; i <= _endIndex - 1; i++) {
            _prev._prevTs = checkpoints[tokenId][i].timestamp;
            _prev._prevBal = checkpoints[tokenId][i].balanceOf;

            uint _nextEpochStart = _bribeStart(_prev._prevTs);
            if (_nextEpochStart > prevRewards.timestamp) {
                reward += prevRewards.balance;
            }

=>          prevRewards.timestamp = _nextEpochStart;
            _prev._prevSupply = supplyCheckpoints[getPriorSupplyIndex(_nextEpochStart + DURATION)].supply;

            prevRewards.balance =
                (_prev._prevBal * tokenRewardsPerEpoch[token][_nextEpochStart]) /
                _prev._prevSupply;
        }
    }
    ...
    return reward;
}
```

예시 시나리오:

`tokenId`를 "User A"라고 하겠습니다.

1. **Epoch 1**:

   - User A 입금 → Checkpoint 1 생성됨.

2. **Epoch 2**:

   - User A 보상 청구 → Epoch 1 보상 획득 → `lastEarn` 업데이트됨 (녹색 점).

3. **Epoch 2**:

   - User A 2회 추가 입금 → Checkpoint 2 및 3 생성됨.

4. User A 다시 청구:

   - `_startIndex`: Checkpoint 1.
   - `_endIndex`: Checkpoint 3.
   - `prevRewards.timestamp`: Epoch 2 시작.

- **Iteration 1** (Checkpoint 1):

  - `_nextEpochStart = Epoch 1 시작`.
  - `prevRewards.timestamp = Epoch 1 시작`.
  - `prevRewards.balance` 업데이트됨 (Epoch 1 보상).

- **Iteration 2** (Checkpoint 2):

  - `_nextEpochStart = Epoch 2 시작`.
  - `_nextEpochStart > prevRewards.timestamp`이므로, `reward += Epoch 1 보상`.
  - `prevRewards.timestamp = Epoch 2 시작`.

- **Checkpoint 3**은 건너뛰거나 현재 타임스탬프에 따라 사용됨.

5. 결과: User A가 이미 청구된 보상을 성공적으로 다시 청구함.

## 권장 사항

`_nextEpochStart > prevRewards.timestamp`일 때만 `prevRewards.timestamp`가 업데이트되도록 `ExternalBribe::earned()` 내부의 로직을 업데이트하십시오.

# [L-01] `ExternalBribe`의 `getEpochStart()`에 중복 로직

ExternalBribe 계약의 getEpochStart 함수에는 불필요한 복잡성을 초래할 수 있는 중복 로직이 포함되어 있습니다.

`ExternalBribe` 계약에서 `getEpochStart` 함수는 다음과 같이 정의됩니다.

```solidity
function getEpochStart(uint timestamp) public pure returns (uint) {
    uint bribeStart = _bribeStart(timestamp);
    uint bribeEnd = bribeStart + DURATION;
    return timestamp < bribeEnd ? bribeStart : bribeStart + 7 days;
}
```

이 문제는 타임스탬프가 현재 에포크 내에 있는 경우 `bribeStart + 7`일의 계산이 항상 `timestamp`보다 크기 때문에 발생합니다. 즉, 조건 확인 및 대체 반환 값은 중복됩니다.

# [L-02] 존재하지 않는 토큰 ID에 대해 `ownerOf()`에서 되돌리지 않음

`VotingEscrow` 계약의 `ownerOf` 함수는 존재하지 않는 토큰 ID로 쿼리할 때 되돌리지 않습니다. 이는 계약에서 오해의 소지가 있는 동작으로 이어질 수 있습니다.

```solidity
function ownerOf(uint _tokenId) public view returns (address) {
    return idToOwner[_tokenId];
}
```

이는 EIP-721과 일치하지 않습니다.

```solidity
    /// @notice Find the owner of an NFT
    /// @dev NFTs assigned to zero address are considered invalid, and queries
    ///  about them do throw.
    /// @param _tokenId The identifier for an NFT
    /// @return The address of the owner of the NFT
    function ownerOf(uint256 _tokenId) external view returns (address);
```

# [L-03] `ClGauge` 계약의 부적절한 보상 검증

`CLGauge` 계약의 보상 비율 검증은 kitten 토큰의 총 잔액을 적절하게 설명하지 못합니다. **여기에는 분배 가능한 보상과 청구되지 않은 보상이 모두 포함됩니다**.

```solidity
require(rewardRate > 0);
uint balance = IERC20(kitten).balanceOf(address(this));
require(
    rewardRate <= balance / epochDurationLeft,
    "Provided reward too high"
);
```

이 확인은 `rewardRate`가 사용 가능한 잔액을 남은 에포크 기간으로 나눈 값을 초과하지 않도록 보장하지만, 잔액에는 아직 분배되지 않은 보상과 아직 청구되지 않은 보상이 모두 포함된다는 점을 고려하지 않습니다.

# [L-04] `CLGauge`는 `ERC721Holder` 대신 `ERC721HolderUpgradeable`을 사용해야 함

`CLGauge` 계약은 업그레이드 가능하도록 설계되었지만 현재 `ERC721Holder`를 상속합니다. 업그레이드 가능한 패턴과의 호환성을 보장하려면 대신 `ERC721HolderUpgradeable`을 상속해야 합니다.

```solidity
contract CLGauge is
    ICLGauge,
    UUPSUpgradeable,
    Ownable2StepUpgradeable,
    ERC721Holder
{
```

`CLGauge`는 업그레이드 가능한 계약이므로 `ERC721HolderUpgradeable`인 `ERC721Holder`의 업그레이드 가능한 버전을 사용하는 것이 중요합니다.

# [L-05] 토큰의 되돌릴 수 없는 화이트리스팅

Voter 계약의 `_whitelist` 함수를 사용하면 토큰을 화이트리스트에 추가할 수 있지만, 일단 토큰이 화이트리스트에 추가되면 제거할 수 없습니다. 이 `단방향` 작업은 운영상의 불편함과 잠재적인 오용으로 이어질 수 있습니다.

```solidity
function _whitelist(address _token) internal {
    require(!isWhitelisted[_token]);
    isWhitelisted[_token] = true;
    emit Whitelisted(msg.sender, _token);
}
```

토큰이 화이트리스트에 추가되면 계약에서 이를 화이트리스트에서 제거할 수 있는 메커니즘이 제공되지 않습니다. 즉, 화이트리스트에 추가된 모든 토큰은 토큰의 상태나 시스템과의 관련성 변화에 관계없이 무기한 유지됩니다.

계약의 유연성과 보안을 강화하려면 화이트리스트에서 토큰을 제거할 수 있는 메커니즘을 구현하는 것이 좋습니다.

# [L-06] `_reset()` 함수의 `_votes`에 대한 중복 확인

`Voter` 계약의 `_reset` 함수에는 `uint256` 유형인 변수 `_votes`에 대한 중복 확인이 포함되어 있습니다.

```solidity
uint256 _votes = votes[_tokenId][_pool];
```

첫 번째 `if` 문은 `_votes`가 0과 같지 않은지 확인합니다. 그러나 `_votes`는 `uint256`이므로 음수가 될 수 없으며 0이 아니면 0보다 커야 합니다. 따라서 `_votes` > 0에 대한 두 번째 확인은 중복되며 `else` 블록에서 도달할 수 없는 코드로 이어집니다.

```solidity
if (_votes != 0) {
    _updateFor(gauges[_pool]);
    weights[_pool] -= _votes;
    votes[_tokenId][_pool] -= _votes;
    if (_votes > 0) {
        IBribe(internal_bribes[gauges[_pool]])._withdraw(
            uint256(_votes),
            _tokenId
        );
        IBribe(external_bribes[gauges[_pool]])._withdraw(
            uint256(_votes),
            _tokenId
        );
        _totalWeight += _votes;
    } else {
        _totalWeight -= _votes;
    }
}
```

`_votes > 0`에 대한 중복 확인을 제거하고 코드를 단순화하는 것이 좋습니다.

# [L-07] `ClFactory`의 `getSwapFee` 및 `getUnstakedFee`의 하드코딩된 가스로 인한 문제 발생 가능성

`getSwapFee` 및 `getUnstakedFee`에서 `unstakedFeeModule` 및 `swapFeeModule`에 대한 정적 호출을 수행하기 위해 제공된 가스는 하드코딩되어 있습니다.

```solidity
    /// @inheritdoc ICLFactory
    function getSwapFee(address pool) external view override returns (uint24) {
        if (swapFeeModule != address(0)) {
            (bool success, bytes memory data) = swapFeeModule
                .excessivelySafeStaticCall(
>>>                 200_000,
                    32,
                    abi.encodeWithSelector(IFeeModule.getFee.selector, pool)
                );
            if (success) {
                uint24 fee = abi.decode(data, (uint24));
                if (fee <= 100_000) {
                    return fee;
                }
            }
        }
        return tickSpacingToFee[CLPool(pool).tickSpacing()];
    }

    /// @inheritdoc ICLFactory
    function getUnstakedFee(
        address pool
    ) external view override returns (uint24) {
        address gauge = voter.gauges(pool);
        if (!voter.isAlive(gauge) || gauge == address(0)) {
            return 0;
        }
        if (unstakedFeeModule != address(0)) {
            (bool success, bytes memory data) = unstakedFeeModule
                .excessivelySafeStaticCall(
>>>                 200_000,
                    32,
                    abi.encodeWithSelector(IFeeModule.getFee.selector, pool)
                );
            if (success) {
                uint24 fee = abi.decode(data, (uint24));
                if (fee <= 1_000_000) {
                    return fee;
                }
            }
        }
        return defaultUnstakedFee;
    }
```

필요한 가스를 조정해야 하는 경우 향후 잠재적인 문제를 방지하기 위해 구성 가능하게 만드는 것을 고려하십시오.

# [L-08] `end` 값이 유효하지 않은 경우 `Voter.updateForRange`가 되돌릴 수 있음

`updateForRange` 내부에서 제공된 `end` 값은 유효한 `pools` 범위를 초과하는지 확인하지 않고 `pools`를 루프하는 데 사용됩니다.

```solidity
    function updateForRange(uint start, uint end) public {
        for (uint i = start; i < end; i++) {
            _updateFor(gauges[pools[i]]);
        }
    }
```

이로 인해 되돌리기가 발생할 수 있습니다. `end` 값을 확인하고 pools 길이를 초과하는 경우 pools 길이를 대신 사용하는 것을 고려하십시오.

```diff
    function updateForRange(uint start, uint end) public {
+         end = Math.min(end, pools.length);
        for (uint i = start; i < end; i++) {
            _updateFor(gauges[pools[i]]);
        }
    }
```

# [L-09] 잠재적 유동성 오버플로

CLGauge 계약에서 `deposit` 및 `withdraw` 함수 모두 풀의 스테이크 함수와 상호 작용할 때 `uint128`에서 `int128`로의 안전하지 않은 유형 캐스팅을 수행합니다. 유동성 값이 큰 경우(2^127-1 초과) 이 변환으로 인해 오버플로가 발생하여 풀에서 스테이킹되거나 인출되는 유동성 값이 잘못될 수 있습니다.

```solidity
    function deposit(
        uint256 nfpTokenId,
        uint256 tokenId
    ) public lock actionLock {
        ...
        // stake nfp liquidity in pool
        pool.stake(int128(_liquidity), _tickLower, _tickUpper, true); // @audit unsafe cast to int128 can lead to overflow
        ...
    }
```

```solidity
    function withdraw(uint nfpTokenId) public lock actionLock {
        ...
        if (_liquidity != 0)
            pool.stake(-int128(_liquidity), _tickLower, _tickUpper, true); // @audit unsafe cast to int128 can lead to overflow
        ...
    }
```

유동성을 int128로 캐스팅하려면 SafeCast 라이브러리를 사용하는 것이 좋습니다.

# [L-10] 이체 수수료 토큰 비호환성

프로토콜은 보상 분배 메커니즘에서 이체 수수료(fee-on-transfer) 토큰을 올바르게 처리하지 않습니다. 현재 구현은 이체 후 지정된 전체 금액을 받는다고 가정하므로 이체 수수료 토큰을 보상으로 사용할 때 회계 불일치가 발생합니다.

영향은 프로토콜이 실제 받은 금액이 아닌 수수료 전 금액을 기준으로 보상을 기록하고 계산한다는 것입니다. 이러한 불일치로 인해 보상 계산이 잘못됩니다.

EternalBribe.notifyRewardAmount:

```solidity
    function notifyRewardAmount(address token, uint amount) external lock {
        ...

        _safeTransferFrom(token, msg.sender, address(this), amount);
        tokenRewardsPerEpoch[token][adjustedTstamp] = epochRewards + amount; // @audit uses pre-fee amount
        ...
    }
```

InternalBribe.notifyRewardAmount:

```solidity
    function notifyRewardAmount(address token, uint amount) external lock {
        ...
        if (block.timestamp >= periodFinish[token]) {
            _safeTransferFrom(token, msg.sender, address(this), amount); // @audit uses pre-fee amount
            rewardRate[token] = amount / DURATION;
        } else {
            uint _remaining = periodFinish[token] - block.timestamp;
            uint _left = _remaining * rewardRate[token];
            require(amount > _left);
            _safeTransferFrom(token, msg.sender, address(this), amount); // @audit uses pre-fee amount
            rewardRate[token] = (amount + _left) / DURATION;
        }
        ...
    }
```

Gauge.notifyRewardAmount:

```solidity
    function notifyRewardAmount(address token, uint amount) external lock {
        ...
        if (block.timestamp >= periodFinish[token]) {
            _safeTransferFrom(token, msg.sender, address(this), amount);
            rewardRate[token] = amount / DURATION;  // @audit uses pre-fee amount
        } else {
            uint _remaining = periodFinish[token] - block.timestamp;
            uint _left = _remaining * rewardRate[token];
            require(amount > _left);
            _safeTransferFrom(token, msg.sender, address(this), amount);
            rewardRate[token] = (amount + _left) / DURATION; // @audit uses pre-fee amount
        }
        ...
    }
```

권장 사항: 이체 수수료 토큰을 올바르게 처리하려면 프로토콜은 이체 전후의 토큰 잔액을 비교하여 실제 수령 금액을 추적해야 합니다.

# [L-11] 잠재적인 산술 오버플로로 인한 보상 계산 되돌리기

CLGauge.sol의 `earned` 함수는 잠재적으로 오버플로될 수 있는 여러 산술 계산을 수행하여 함수가 되돌아가고 보상 청구에 대한 서비스 거부 조건이 발생합니다.

업데이트된 보상 성장 계산 시: `rewardGrowthGlobalX128 += (reward * FixedPoint128.Q128) / pool.stakedLiquidity()`
그리고 최종 획득 보상 금액 계산 시 `(rewardGrowthInsideDelta * _liquidity) / FixedPoint128.Q128;`

두 경우 모두 중간 곱셈이 uint256 최대값을 초과하여 되돌리기를 유발할 수 있습니다.
이로 인해 사용자는 보상을 청구할 수 없게 됩니다.

```solidity
    function earned(uint256 nfpTokenId) public view returns (uint) {
        ...
        if (timeDelta != 0 && rewardReserve > 0 && pool.stakedLiquidity() > 0) {
            uint256 reward = rewardRate * timeDelta;
            if (reward > rewardReserve) reward = rewardReserve;

            rewardGrowthGlobalX128 +=
                (reward * FixedPoint128.Q128) / // @audit revert due to overflow here
                pool.stakedLiquidity();
        }
        ...
        return (rewardGrowthInsideDelta * _liquidity) / FixedPoint128.Q128; // @audit revert due to overflow here
    }
```

중간 오버플로를 처리할 수 있는 OpenZeppelin Math.mulDiv를 사용하는 것이 좋습니다.

참조:
https://github.com/velodrome-finance/slipstream/blob/main/contracts/gauge/CLGauge.sol#L123.
https://github.com/velodrome-finance/slipstream/blob/main/contracts/gauge/CLGauge.sol#L132.

# [L-12] `ClFactory`의 Initializer 패턴 업그레이드 부족, 선점 위험

`CLFactory` 계약은 `initializer` 수정자가 있는 `initialize` 함수를 사용하지만 업그레이드 패턴(예: UUPSUpgradeable)을 구현하지 않습니다.

선점 초기화의 위험을 제거하기 위한 권장 사항:

- 계약을 업그레이드할 의도가 없다면 초기화 함수를 생성자로 교체하십시오.
- 업그레이드 가능성을 원한다면 코드베이스의 다른 계약과 마찬가지로 적절한 업그레이드 패턴(예: UUPSUpgradeable)을 구현하십시오.

# [L-13] 사용자는 split 함수를 통해 잠긴 금액이 0인 NFT를 생성할 수 있음

VotingEscrow는 사용자가 잠긴 금액이 0인 NFT를 생성하는 것을 허용하지 않습니다.

`split` 함수도 0으로 분할하는 것을 허용하지 않습니다. 그러나 결과 NFT 두 개 모두 0이 아닌 금액을 가지고 있는지 확인하지 못합니다. 사용자가 `_amount`를 소스 NFT의 전체 잠긴 잔액(`value`)과 동일하게 설정하면 첫 번째 NFT의 잠긴 금액이 0이 됩니다.

```solidity
    function split(
        uint _from,
        uint _amount
    ) external returns (uint _tokenId1, uint _tokenId2) {
        ...
        // split and mint new NFTs
        int128 _splitAmount = int128(uint128(_amount));
        _locked.amount = value - _splitAmount; // @audit Can be 0 here if value = _splitAmount
        _tokenId1 = _createSplitNFT(msgSender, _locked);

        _locked.amount = _splitAmount;
        _tokenId2 = _createSplitNFT(msgSender, _locked);
        ...
    }
```

이를 통해 사용자는 토큰을 반복적으로 분할하여 잠긴 금액이 0인 수많은 "좀비" NFT를 생성할 수 있으며, 이는 NFT에 잠긴 가치가 필요한 계약 설계와 모순됩니다.

`split` 함수에 두 결과 NFT 모두 0이 아닌 금액을 가지고 있는지 확인하는 확인을 추가하는 것이 좋습니다.

```diff
    function split(
        uint _from,
        uint _amount
    ) external returns (uint _tokenId1, uint _tokenId2) {
        ...
        int128 _splitAmount = int128(uint128(_amount));
+       require(_splitAmount < value, "Cannot split entire amount");
        _locked.amount = value - _splitAmount;
        _tokenId1 = _createSplitNFT(msgSender, _locked);
```

# [L-14] 게이지가 부활할 때 죽은 기간 동안 보상을 받을 수 있음

`reviveGauge` 함수를 사용하여 게이지를 부활시킬 때 `supplyIndex`가 현재 인덱스로 업데이트되지 않습니다. 이로 인해 부활한 게이지가 죽은 기간 동안의 보상을 계산하고 받을 수 있는 취약점이 생성됩니다.

```solidity
    function reviveGauge(address _gauge) external { // @audit index isn't updated
        require(msg.sender == emergencyCouncil, "not emergency council");
        require(!isAlive[_gauge], "gauge already alive");
        isAlive[_gauge] = true;
        emit GaugeRevived(_gauge);
    }
```

게이지의 `supplyIndex`를 현재 전역 인덱스로 업데이트하는 것이 좋습니다.

```diff
function reviveGauge(address _gauge) external {
    require(msg.sender == emergencyCouncil, "not emergency council");
    require(!isAlive[_gauge], "gauge already alive");
    isAlive[_gauge] = true;
+   supplyIndex[_gauge] = index; // Update to current index
    emit GaugeRevived(_gauge);
}
```

# [L-15] `Gauge` 또는 `Bribe` 입금이 0이 아닐 때까지 보상 손실

보상은 DURATION 동안 방출되므로 입금이 발생하지 않고 0이 아닌 값으로 notifyRewardAmount()가 호출되면 totalSupply가 0이 아닐 때까지(아무도 청구할 수 없으므로) 모든 보상이 몰수됩니다.

권장 사항: 최종 사용자에게 이 위험을 문서화하고 게이지에 투표하기 전에 입금하라고 알리십시오.
# [L-16] `notifyRewardAmount`의 먼지(Dust) 손실

## 심각도

**영향:** 낮음

**가능성:** 높음

**설명:**
이는 미미하지만 결코 대기열에 다시 추가되지 않는 먼지 손실을 발생시킵니다. 비공개 링크 code-423n4/2022-10-3xcalibur-findings#410을 참조하십시오.
먼지를 다시 대기열에 넣는 SNX 구현과 비교됩니다.
게이지에 예치된 총 공급량이 동일한 기간 동안 계속 증가하면 사용자는 DURATION 길이의 또 다른 에포크 기간의 남은 금액을 분배하여 희석될 수 있습니다. 반면에 사용자가 동일한 에포크 동안 게이지에서 자금을 인출하면 혜택을 볼 수도 있습니다.

# [L-17] 업그레이드 가능한 계약에 `_disableInitializers` 없음

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
구현 계약을 초기화하는 기능을 비활성화하는 것은 [모범 사례](https://docs.openzeppelin.com/upgrades-plugins/writing-upgradeable#initializing_the_implementation_contract)입니다. 다음 계약의 생성자에서 `_disableInitializers()` 호출이 누락되었습니다.

- Voter.
- VotingEscrow.
- CLFactory.
- FactoryRegistry.
- CLGauge.

모든 업그레이드 가능한 계약에서 OpenZeppelin `_disableInitializers`가 있는 생성자를 추가하는 것을 고려하십시오.

```solidity
constructor() {
    _disableInitializers();
}
```

# [L-18] 보상 분배 체크포인트 순회 시 가스부족 잠재적 위험

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
Gauge 계약은 각 사용자 작업에 대해 체크포인트를 생성하므로 보상 분배 중에 과도한 가스 소비가 발생할 수 있습니다. 사용자가 장기간 보상을 청구하지 않은 경우 수많은 체크포인트를 순회하면 가스 부족(OOG) 오류가 발생할 수 있습니다.

사용자가 보상을 청구할 때 계약은 마지막 체크포인트부터 시작하여 해당 사용자와 관련된 모든 체크포인트를 반복합니다. 사용자가 오랫동안 보상을 청구하지 않은 경우 체크포인트 수가 크게 증가하여 반복 중에 가스 비용이 증가할 수 있습니다.

```solidity
    function earned(address token, address account) public view returns (uint) {
        uint _startTimestamp = Math.max(
            lastEarn[token][account],
            rewardPerTokenCheckpoints[token][0].timestamp
        );
        if (numCheckpoints[account] == 0) {
            return 0;
        }

        uint _startIndex = getPriorBalanceIndex(account, _startTimestamp);
        uint _endIndex = numCheckpoints[account] - 1;

        uint reward = 0;

        if (_endIndex > 0) {
            for (uint i = _startIndex; i <= _endIndex - 1; i++) {
                Checkpoint memory cp0 = checkpoints[account][i];
                Checkpoint memory cp1 = checkpoints[account][i + 1];
                (uint _rewardPerTokenStored0, ) = getPriorRewardPerToken(
                    token,
                    cp0.timestamp
                );
                (uint _rewardPerTokenStored1, ) = getPriorRewardPerToken(
                    token,
                    cp1.timestamp
                );
                reward +=
                    (cp0.balanceOf *
                        (_rewardPerTokenStored1 - _rewardPerTokenStored0)) /
                    PRECISION;
            }
        }

        Checkpoint memory cp = checkpoints[account][_endIndex];
        (uint _rewardPerTokenStored, ) = getPriorRewardPerToken(
            token,
            cp.timestamp
        );
        reward +=
            (cp.balanceOf *
                (rewardPerToken(token) -
                    Math.max(
                        _rewardPerTokenStored,
                        userRewardPerTokenStored[token][account]
                    ))) /
            PRECISION;

        return reward;
    }
```

 이로 인해 트랜잭션의 가스가 부족하여 작업이 실패할 수 있습니다.

권장 사항:
사용자가 특별 체크포인트에 대한 보상을 청구할 수 있도록 허용하십시오.

# [L-19] `Voter` 계약 보상 분배 메커니즘의 부정확성

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`voter` 계약의 보상 분배 메커니즘은 `IVotingEscrow(_ve).balanceOfNFT(_tokenId)`에서 파생된 동적 가중치에 의존하기 때문에 부정확하고 동기화되지 않을 수 있습니다. 이로 인해 특히 사용자가 많은 시나리오에서 보상 계산에 불일치가 발생할 수 있습니다.

`Voter` 계약에서 보상 분배 비율은 총 투표 가중치를 기반으로 계산됩니다.

```solidity
uint256 _ratio = (amount * 1e18) / totalWeight; // 1e18 adjustment is removed during claim
```

총 가중치는 NFT의 잔액에 의해 영향을 받으며, 이는 `VotingEscrow` 계약의 다음 함수에 의해 결정됩니다.

```solidity
    function _vote(
        uint _tokenId,
        address[] memory _poolVote,
        uint256[] memory _weights
    ) internal {
        _reset(_tokenId);
        uint _poolCnt = _poolVote.length;
        uint256 _weight = IVotingEscrow(_ve).balanceOfNFT(_tokenId);
        uint256 _totalVoteWeight = 0;
        uint256 _totalWeight = 0;
        uint256 _usedWeight = 0;
```

이 가중치는 시간이 지남에 따라 변할 수 있습니다. 그러나 보상 분배 인덱스는 이러한 가중치를 기반으로 업데이트되며, `_updateFor` 함수는 현재 가중치를 기반으로 지분을 계산합니다.

```solidity
    function _balanceOfNFT(
        uint _tokenId,
        uint _t
    ) internal view returns (uint) {
        uint _epoch = user_point_epoch[_tokenId];
        if (_epoch == 0) {
            return 0;
        } else {
            Point memory last_point = user_point_history[_tokenId][_epoch];
            last_point.bias -=
                last_point.slope *
                int128(int256(_t) - int256(last_point.ts));
            if (last_point.bias < 0) {
                last_point.bias = 0;
            }
            return uint(int256(last_point.bias));
        }
    }
```

그러나 가중치는 사용자가 `poke` 또는 `vote`와 같은 함수를 호출할 때만 다시 계산되므로 시스템의 실제 상태와 가중치를 동기화 상태로 유지할 만큼 자주 발생하지 않을 수 있습니다.

결과적으로 많은 사용자가 참여하는 경우 보상 계산이 부정확해져 일부 사용자가 실제 기여에 비해 다소 많거나 적은 보상을 받을 수 있습니다.

이것은 포크된 리포지토리의 수정되지 않은 문제입니다(https://github.com/spearbit/portfolio/blob/master/pdfs/Velodrome-Spearbit-Security-Review.pdf 5.3.3 Bribe and fee token emissions can be gamed by users).

권장 사항:
원래 발견 사항의 수정 권장 사항을 따르십시오. 사용자 예치금을 자동으로 줄이기 위해 Voter 및 Bribe 계약을 재설계하는 것이 가치가 있을 수 있습니다.

# [L-20] 지원되지 않는 토큰에 대해 `notifyRewardAmount`를 호출하면 `gauge`를 슬프게(grief) 할 수 있음

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
공격자는 새로운 `gauge`에 대해 `notifyRewardAmount`를 반복적으로 호출하여 시스템을 악용하고 잠재적으로 `MAX_REWARD_TOKENS` 한도에 도달할 수 있습니다. 이로 인해 합법적인 토큰이 보상으로 추가되는 것을 막을 수 있습니다.

토큰이 화이트리스트에 추가되면 `swapOutRewardToken`이 호출되지 않는 한 제거할 수 없습니다. 즉, `voter`가 `MAX_REWARD_TOKENS`가 넘는 토큰을 화이트리스트에 올린 경우 공격자는 새로 생성된 게이지에 대해 슬픔(griefing) 공격을 수행하여 일시적인 DOS를 달성할 수 있습니다.

```solidity
    function notifyRewardAmount(address token, uint amount) external lock {
        require(token != stake);
        require(amount > 0);
        if (!isReward[token]) {
            require(
                IVoter(voter).isWhitelisted(token),
                "rewards tokens must be whitelisted"
            );
            require(
                rewards.length < MAX_REWARD_TOKENS,
                "too many rewards tokens"
            );
        }
        if (rewardRate[token] == 0)
            _writeRewardPerTokenCheckpoint(token, 0, block.timestamp);
        (
            rewardPerTokenStored[token],
            lastUpdateTime[token]
        ) = _updateRewardPerToken(token, type(uint).max, true);
        _claimFees();

        if (block.timestamp >= periodFinish[token]) {
            _safeTransferFrom(token, msg.sender, address(this), amount);
            rewardRate[token] = amount / DURATION;
        } else {
            uint _remaining = periodFinish[token] - block.timestamp;
            uint _left = _remaining * rewardRate[token];
            require(amount > _left);
            _safeTransferFrom(token, msg.sender, address(this), amount);
            rewardRate[token] = (amount + _left) / DURATION;
        }
        require(rewardRate[token] > 0);
        uint balance = IERC20(token).balanceOf(address(this));
        require(
            rewardRate[token] <= balance / DURATION,
            "Provided reward too high"
        );
        periodFinish[token] = block.timestamp + DURATION;
        if (!isReward[token]) {
            isReward[token] = true;
            rewards.push(token);
        }

        emit NotifyReward(msg.sender, token, amount);
    }
```

공격자는 `minimum` 토큰을 제공하고 더 이상 보상되지 않는(또는 게이지에서 지원하지 않는) 토큰으로 `notifyRewardAmount`를 호출할 수 있습니다. 토큰이 화이트리스트에 있으므로 `rewards.length`가 `1` 증가할 수 있습니다.

이로 인해 `rewards` 배열이 `MAX_REWARD_TOKENS`를 초과하여 합법적인(실제 의도된) 보상 토큰이 향후 보상으로 추가되는 것을 방지할 수 있습니다. `swapOutRewardToken`이 호출되지 않는 한 일시적인 DOS를 해결할 수 없습니다.

권장 사항:
`voter`에만 호출을 제한하십시오.

# [L-21] 잔액 검색 시 잘못된 논리 및 문서

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`Gauge` 계약의 `getPriorBalanceIndex` 함수에 있는 문서와 논리에는 부정확성이 포함되어 있습니다. 주석에는 **함수가 주어진 블록의 잔액을 반환한다**고 잘못 명시되어 있지만 **실제로는 block.timestamp를 기반으로 인덱스를 반환**합니다. 또한 0의 잔액을 반환하는 논리에 결함이 있어 잠재적인 오해를 불러일으킬 수 있습니다.

관련 문서 및 코드 조각은 다음과 같습니다.

```solidity
   /**
    * @notice Determine the prior balance for an account as of a block number
    * @dev Block number must be a finalized block or else this function will revert to prevent misinformation.
    * @param account The address of the account to check
    * @param timestamp The timestamp to get the balance at
    * @return The balance the account had as of the given block
    */
```

```solidity
   // Next check implicit zero balance
   if (checkpoints[account][0].timestamp > timestamp) {
       return 0;
   }
```

`checkpoints[account][0].timestamp > timestamp` 조건이 참이면 함수는 `0`(인덱스는 `0`)을 반환합니다. 여기서 0을 반환하는 것은 계정의 잔액이 0이어야 함을 의미하지만 실제로는 첫 번째 체크포인트의 인덱스를 나타내므로 오해의 소지가 있습니다.

다른 경우에는 `checkpoints[account][index]`를 직접 사용하여 해당 지점의 잔액을 가져올 수 있지만 위에서 논의한 경우 `0` 대신 `checkpoints[account][0]`을 잘못 반환할 수 있습니다.

이는 `checkpoints[account][0]`에 0이 아닌 데이터가 있을 수 있기 때문입니다.

```solidity
        if (
            _nCheckPoints > 0 &&
            checkpoints[account][_nCheckPoints - 1].timestamp == _timestamp
        ) {
            checkpoints[account][_nCheckPoints - 1].balanceOf = balance;
        } else {
            checkpoints[account][_nCheckPoints] = Checkpoint(
                _timestamp,
                balance
            );
            numCheckpoints[account] = _nCheckPoints + 1;
        }
```

또한 `VotingEscrow::getPastVotes`에서도 문제가 발생할 수 있습니다.

```solidity
    function getPastVotes(
        address account,
        uint timestamp
    ) public view returns (uint) {
        uint32 _checkIndex = getPastVotesIndex(account, timestamp);
        // Sum votes
        uint[] storage _tokenIds = checkpoints[account][_checkIndex].tokenIds;
        uint votes = 0;
        for (uint i = 0; i < _tokenIds.length; i++) {
            uint tId = _tokenIds[i];
            // Use the provided input timestamp here to get the right decay
            votes = votes + _balanceOfNFT(tId, timestamp);
        }
        return votes;
    }
```

`timestamp`가 `checkpoints[account][0].timestamp`보다 작으면 잘못된 투표가 계산됩니다.

```solidity
        // Next check implicit zero balance
        if (checkpoints[account][0].timestamp > timestamp) {
            return 0;
        }
```

**POC**

```solidity
//forge test -f https://rpc.hyperliquid.xyz/evm  --mt testBalanceAt -vvv
    function testBalanceAt() public {
        _setUp();
        vm.warp( 100 weeks);
        vm.startPrank(deployer);

        kitten.approve(address(veKitten), type(uint256).max);
        uint256 tokenId1=veKitten.create_lock_for(100_000_000 ether, 10 weeks , user1);

        vm.warp(block.timestamp + 10 weeks);
        uint256 tokenId2=veKitten.create_lock_for(100_000_000 ether, 10 weeks , user1);

        uint256 votingPower1= veKitten.balanceOfNFTAt(tokenId1, block.timestamp - 5 weeks);
        uint256 votingPower2= veKitten.balanceOfNFTAt(tokenId2, block.timestamp - 5 weeks);

        //@audit token2 is just created but have much more voting power at the past
        assertEq(votingPower1,4794520547945205478800000 );
        assertEq(votingPower2,14383561643835616436400000 );

    }
```

**권장 사항**

이 문제를 해결하려면 다음을 수행하는 것이 좋습니다.

- 문서 업데이트: 함수가 block.timestamp를 기반으로 인덱스를 반환한다는 것을 정확하게 반영하도록 주석을 수정하고 해당 잔액을 검색하는 방법을 명확히 하십시오.
- `0`을 정확하게 반영하기 위해 인덱스 `0`의 모든 `checkpoints`를 빈 상태로 설정하십시오.

# [L-22] `reset()`에서 초기화 후 투표 제한 가능

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`Voter` 계약의 `reset` 함수에서 다음 코드가 실행됩니다.

```solidity
function reset(uint _tokenId) external onlyNewEpoch(_tokenId) {
    require(IVotingEscrow(_ve).isApprovedOrOwner(msg.sender, _tokenId));
    lastVoted[_tokenId] = block.timestamp; // Updates lastVoted
    _reset(_tokenId);
    IVotingEscrow(_ve).abstain(_tokenId);
}
```

사용자가 `reset` 함수를 호출하면 `lastVoted` 타임스탬프가 현재 `block.timestamp`로 업데이트됩니다. 즉, `onlyNewEpoch` 수정자가 제한을 가하므로 사용자는 현재 에포크에서 `vote`할 수 없습니다. 이로 인해 투표에 계속 참여하려는 사용자가 의도치 않게 차단되는 상황이 발생할 수 있습니다.

권장 사항:
의도가 사용자가 현재 에포크에서 투표를 `기권`하도록 허용하는 것이라면 함수 이름을 `reset`에서 `abstain`과 같은 이름으로 변경해야 합니다. 이는 사용자가 현재 에포크에서 투표하지 않기로 선택했음을 나타내므로 수행 중인 작업을 더 잘 반영합니다.

그렇지 않으면 `lastVoted` 할당을 제거하십시오.

# [L-23] `VotingEscrow.totalSupplyAtT`가 제대로 작동하지 않음

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`VotingEscrow.totalSupplyAtT` 내부에서 항상 최신 마지막 에포크의 `point_history`를 `_supply_at`에 제공한다는 것을 알 수 있습니다.

```solidity
    function totalSupplyAtT(uint t) public view returns (uint) {
        uint _epoch = epoch;
        Point memory last_point = point_history[_epoch];
        return _supply_at(last_point, t);
    }
```

즉, 제공된 `t`가 과거인 경우 함수는 올바른 `last_point`를 사용하지 않아 총 공급량 계산이 잘못되고 작업이 되돌려집니다.

```solidity
    function _supply_at(
        Point memory point,
        uint t
    ) internal view returns (uint) {
        Point memory last_point = point;
        uint t_i = (last_point.ts / WEEK) * WEEK;
        for (uint i = 0; i < 255; ++i) {
            t_i += WEEK;
            int128 d_slope = 0;
            if (t_i > t) {
                t_i = t;
            } else {
                d_slope = slope_changes[t_i];
            }
            last_point.bias -=
                last_point.slope *
>>>             int128(int256(t_i - last_point.ts));
            if (t_i == t) {
                break;
            }
            last_point.slope += d_slope;
            last_point.ts = t_i;
        }

        if (last_point.bias < 0) {
            last_point.bias = 0;
        }
        return uint(uint128(last_point.bias));
    }
```

PoC :

```solidity
    function testSupplyInconsistent() public {
        _setUp();

        vm.startPrank(deployer);

        kitten.approve(address(veKitten), type(uint256).max);
        veKitten.create_lock_for(100_000_000 ether, 52 weeks * 2, user1);
        veKitten.create_lock_for(150_000_000 ether, 52 weeks * 2, user2);


        vm.warp(block.timestamp + 1 weeks);

        veKitten.checkpoint();

        uint256 cacheTime = block.timestamp;
        uint256 cachetBlock = block.number;

        console.log("total supply at current block : ");
        console.log(veKitten.totalSupply());
        console.log("total supply at current block using block time : ");
        console.log(veKitten.totalSupplyAt(cachetBlock));

        vm.warp(block.timestamp + 1 weeks);

        veKitten.checkpoint();

        console.log("total supply at cached block timestamp : ");
         // will revert
        console.log(veKitten.totalSupplyAtT(cacheTime));
        console.log("total supply at cached block using block time : ");
        console.log(veKitten.totalSupplyAt(cachetBlock));


        vm.stopPrank();
    }
```

**권장 사항**

`totalSupplyAt`과 동일한 검색 로직을 사용하여 먼저 올바른 대상 에포크를 결정한 다음 `dt`를 계산하고 해당 값을 `_supply_at`에 전달하십시오.

# [L-24] `increase_amount` 및 `deposit_for`에 대한 일관성 없는 제한

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`increase_amount` 및 `deposit_for`는 동일한 작업을 수행하지만 일관성 없는 제한이 있습니다. `increase_amount`는 승인된 지출자 또는 `tokenId` 소유자만 작업을 실행할 수 있도록 허용하는 반면, `deposit_for`는 누구나 호출할 수 있습니다.

```solidity
    function deposit_for(uint _tokenId, uint _value) external nonreentrant {
        LockedBalance memory _locked = locked[_tokenId];
        require(_value > 0); // dev: need non-zero value
        require(_locked.amount > 0, "No existing lock found");
        require(
            _locked.end > block.timestamp,
            "Cannot add to expired lock. Withdraw"
        );
        _deposit_for(
            _tokenId,
            _value,
            0,
            _locked,
            DepositType.DEPOSIT_FOR_TYPE
        );
    }
```

```solidity
    function increase_amount(uint _tokenId, uint _value) external nonreentrant {
>>>     assert(_isApprovedOrOwner(msg.sender, _tokenId));

        LockedBalance memory _locked = locked[_tokenId];

        assert(_value > 0); // dev: need non-zero value
        require(_locked.amount > 0, "No existing lock found");
        require(
            _locked.end > block.timestamp,
            "Cannot add to expired lock. Withdraw"
        );

        _deposit_for(
            _tokenId,
            _value,
            0,
            _locked,
            DepositType.INCREASE_LOCK_AMOUNT
        );
    }
```

권장 사항:
`increase_amount`에 대한 제한을 제거하거나 `deposit_for`에 제한을 추가하십시오.

# [L-25] `Voter.vote` 내부에 `_poolVote` 길이 확인이 없어 문제가 발생할 수 있음

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
사용자는 `Voter` 내부에 등록되어 있는 한 임의의 양의 풀에 `vote`할 수 있습니다.

```solidity
    function vote(
        uint tokenId,
        address[] calldata _poolVote,
        uint256[] calldata _weights
    ) external onlyNewEpoch(tokenId) {
        require(IVotingEscrow(_ve).isApprovedOrOwner(msg.sender, tokenId));
        require(_poolVote.length == _weights.length);
        lastVoted[tokenId] = block.timestamp;
        _vote(tokenId, _poolVote, _weights);
    }
```

이로 인해 문제가 발생할 수 있습니다. 최대 풀 길이 확인이 없으면 많은 작업이 풀 목록을 반복하고 뇌물 계약과 상호 작용하는 것과 같이 잠재적으로 비용이 많이 드는 작업을 수행하므로 가스 부족 오류가 발생할 수 있습니다.

권장 사항:
`vote` 작업 내부에 최대 풀 길이 확인을 추가하는 것을 고려하십시오.

# [L-26] 사용자가 에포크 종료 시 정확히 `getReward`를 호출하면 보상을 잃음

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`ExternalBribe.earned` 함수에서 사용자가 에포크 종료 시간에 정확히 `getReward`를 호출하면 에포크에 대한 보상을 영구적으로 잃을 수 있습니다.

사용자가 체크포인트 하나를 가지고 있고 `block.timestamp`가 `_lastEpochEnd`와 정확히 같을 때 `getReward`를 호출하는 시나리오를 고려해 보십시오.

- `block.timestamp > _lastEpochEnd` 조건은 `false`로 평가됩니다.
- 보상 계산을 건너뛰고 0을 반환합니다.
- `getReward` 함수는 `lastEarn[token][tokenId]`를 `block.timestamp`(`_lastEpochEnd`)로 업데이트합니다.
- 사용자가 나중에 다시 시도하면(예: `block.timestamp + 1`), `_startTimestamp`는 `lastEarn[token][tokenId]`( `_lastEpochEnd`와 같음)로 설정됩니다.
- `_startTimestamp < _lastEpochEnd` 조건은 이제 `false`로 평가됩니다.
- 사용자는 해당 에포크에 대한 보상을 영구적으로 잃습니다.

이것은 사용자가 보상을 잃는 타이밍 취약점을 생성합니다.

```solidity
    function earned(address token, uint tokenId) public view returns (uint) {
        uint _startTimestamp = lastEarn[token][tokenId];
        ...
        Checkpoint memory _cp0 = checkpoints[tokenId][_endIndex];
        (_prev._prevTs, _prev._prevBal) = (_cp0.timestamp, _cp0.balanceOf);

        uint _lastEpochStart = _bribeStart(_prev._prevTs);
        uint _lastEpochEnd = _lastEpochStart + DURATION;

        if (
            block.timestamp > _lastEpochEnd && _startTimestamp < _lastEpochEnd // @audit check will not pass if user calls getReward at _lastEpochEnd
        ) {
            SupplyCheckpoint memory _scp0 = supplyCheckpoints[
                getPriorSupplyIndex(_lastEpochEnd)
            ];
            _prev._prevSupply = _scp0.supply;

            reward +=
                (_prev._prevBal *
                    tokenRewardsPerEpoch[token][_lastEpochStart]) /
                _prev._prevSupply;
        }

        return reward;
    }
```

**권장 사항**

사용자가 에포크 종료 시 `getReward`를 호출할 때 보상을 잃지 않도록 되돌립니다.

# [L-27] 사용자는 죽은 CL 게이지에 NFT를 예치할 수 있음

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
Kittenswap 프로토콜은 게이지 시스템을 사용하여 보상을 분배합니다. 안전을 위해 프로토콜은 Voter 계약의 `killGauge` 함수를 통해 게이지를 "종료"할 수 있으며, 이는 `isAlive[_gauge] = false`를 설정하고 청구 가능한 보상을 0으로 만듭니다.

```solidity
    function deposit(uint256 nfpTokenId, uint256 tokenId) public lock actionLock {
        ...
        // attach ve
        if (tokenId > 0) {
            require(IVotingEscrow(_ve).ownerOf(tokenId) == msg.sender);
            if (tokenIds[msg.sender] == 0) {
                tokenIds[msg.sender] = tokenId;
                IVoter(voter).attachTokenToGauge(tokenId, msg.sender);
            }
            require(tokenIds[msg.sender] == tokenId);
        } else {
            tokenId = tokenIds[msg.sender];
        }

        emit Deposit(msg.sender, nfpTokenId, tokenId, _liquidity);
    }
```

그러나 사용자가 veKITTEN 토큰을 첨부하지 않고 NFP 토큰을 예치하면(`tokenId = 0`), 게이지가 살아 있는지 여부에 대한 확인이 우회됩니다. 이를 통해 사용자는 종료된 게이지에 예치할 수 있으며, 이는 스테이킹된 유동성과 스테이킹되지 않은 유동성 간의 CLPool 수수료 분배에서 예상치 못한 동작으로 이어집니다.

권장 사항:
게이지 상태를 확인하기 위해 투표자 계약으로 콜백하십시오.

```diff
    function deposit(
        uint256 nfpTokenId,
        uint256 tokenId
    ) public lock actionLock {
        ...
+       IVoter(voter).emitDeposit(tokenId, msg.sender, _liquidity);
        emit Deposit(msg.sender, nfpTokenId, tokenId, _liquidity);
    }
```

# [L-28] `ExternalBribe` 계약의 부정확한 보상 계산

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`ExternalBribe` 계약의 보상 계산은 이전 에포크 말의 공급을 사용하면서 해당 에포크의 마지막 기록된 사용자 잔액에 의존합니다. 이러한 불일치로 인해 부정확한 보상 분배가 발생하여 사용자가 에포크가 끝나기 직전에 많은 양을 입금하여 시스템을 악용할 수 있습니다.

```solidity
_prev._prevSupply = supplyCheckpoints[getPriorSupplyIndex(_nextEpochStart + DURATION)].supply;
_prev._prevBal = checkpoints[tokenId][i].balanceOf;
```

여기서 `_prev._prevSupply`는 이전 에포크 말의 공급을 검색하는 반면, `_prev._prevBal`은 사용자의 마지막 체크포인트 잔액을 검색합니다. 즉, 보상을 계산할 때 계약은 에포크 말의 공급을 사용하지만 마지막 체크포인트의 사용자 잔액을 사용하므로 에포크 동안 사용자의 기여도를 정확하게 반영하지 못할 수 있습니다.

이러한 설계 결함을 통해 사용자는 에포크가 끝나기 직전에 상당한 양의 토큰을 입금하여 에포크 동안의 실제 기여도가 미미하더라도 부풀려진 공급 수치를 기반으로 불균형적인 보상 몫을 받을 수 있습니다.

권장 사항:
이 문제를 완화하려면 전체 에포크 동안 사용자 기여도를 정확하게 반영하는 보다 강력한 보상 계산 메커니즘을 구현하는 것을 고려하십시오.

# [L-29] `CLGauge.earned`는 보상 성장 언더플로로 인해 되돌릴 수 있음

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`earned` 함수 내부에서 최신 `getRewardGrowthInside`를 쿼리한 다음 `rewardGrowthInsideDelta`를 계산합니다.

```solidity
    function earned(uint256 nfpTokenId) public view returns (uint) {
        uint256 timeDelta = block.timestamp - pool.lastUpdated();

        uint256 rewardGrowthGlobalX128 = pool.rewardGrowthGlobalX128();
        uint256 rewardReserve = pool.rewardReserve();
        if (timeDelta != 0 && rewardReserve > 0 && pool.stakedLiquidity() > 0) {
            uint256 reward = rewardRate * timeDelta;
            if (reward > rewardReserve) reward = rewardReserve;

            rewardGrowthGlobalX128 +=
                (reward * FixedPoint128.Q128) /
                pool.stakedLiquidity();
        }
        (
            ,
            ,
            ,
            ,
            ,
            int24 _tickLower,
            int24 _tickUpper,
            uint128 _liquidity,
            ,
            ,
            ,

        ) = nfp.positions(nfpTokenId);

        uint256 rewardGrowthInsideInitial = rewardGrowthInside[nfpTokenId];
        uint256 rewardGrowthInsideCurrent = pool.getRewardGrowthInside(
            _tickLower,
            _tickUpper,
            rewardGrowthGlobalX128
        );

>>>     uint256 rewardGrowthInsideDelta = rewardGrowthInsideCurrent -
            rewardGrowthInsideInitial;
        return (rewardGrowthInsideDelta * _liquidity) / FixedPoint128.Q128;
    }
```

보상 성장 및 델타 계산은 암시적으로 언더플로에 의존하지만 현재 `CLGauge`는 Solidity 버전 0.8을 사용하므로 올바르게 작동하지 않습니다.

근본 원인에 대한 자세한 정보: https://github.com/Uniswap/v3-core/issues/573.

권장 사항:
언더플로를 허용하려면 `rewardGrowthInsideDelta`를 `unchecked` 블록 안에 래핑하십시오.

# [L-30] 일관성 없는 `DOMAIN_TYPEHASH`

## 심각도

**영향:** 낮음

**가능성:** 낮음

**설명:**
`VotingEscrow::delegateBySig` 함수에서 `domainSeparator`는 다음과 같이 계산됩니다.

```solidity
bytes32 domainSeparator = keccak256(
    abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), keccak256(bytes(version)), block.chainid, address(this))
);
```

그러나 `DOMAIN_TYPEHASH` 상수는 다음과 같이 정의됩니다.

```solidity
bytes32 public constant DOMAIN_TYPEHASH = keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
```

여기서 `domainSeparator`에는 정의된 유형 해시의 일부가 아닌 추가 `version` 필드가 포함되어 있어 EIP-712 표준과 호환되지 않습니다.

아래와 같이 `version` 필드를 포함하도록 `DOMAIN_TYPEHASH` 정의를 업데이트하십시오.

```solidity
bytes32 public constant DOMAIN_TYPEHASH = keccak256('EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)');
```
