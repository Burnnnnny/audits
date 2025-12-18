# Pashov Audit Group 정보

Pashov Audit Group은 이 분야에서 검증된 40명 이상의 프리랜서 보안 연구원들로 구성되어 있습니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 획득했거나, 여러 차례 우승했거나, 우리와의 감사에서 뛰어난 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 함께 일합니다.

300건 이상의 보안 감사를 완료하고 수천 건의 취약점을 발견하여 패치를 도왔으며, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 우리는 귀하의 프로젝트를 위해 우리 팀의 최선의 노력을 보장합니다.

이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

<p>Pashov Audit Group에 의해 <strong>Kittenswap/contracts-v2</strong> 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 그 동안 Pashov Audit Group은 <strong>KittenSwap</strong> 검토에 참여했습니다. 총 <strong>27</strong>개의 문제가 발견되었습니다.</p>

# KittenSwap 정보

<p>KittenSwap은 LayerZero 및 hyperlane 래핑 브리지 토큰을 지원하는 HyperEVM에 배포된 맞춤형 게이지 및 팩토리 수정 사항이 있는 DEX입니다. 감사는 투표 에스크로 토큰 잠금 시스템, 투표자 제어 게이지 가중치 분배, 외부 뇌물 보상 메커니즘 및 집중 유동성 풀 팩토리 관리에 중점을 두었습니다. 이번 감사의 범위는 투표 에스크로 로직의 업데이트와 Algebra와의 통합에 중점을 두었습니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [29a49b17a2494c389861b7e18b184b515992c465](https://github.com/Kittenswap/contracts-v2/tree/29a49b17a2494c389861b7e18b184b515992c465)<br>&nbsp;&nbsp;(Kittenswap/contracts-v2)

**수정 검토 커밋 해시:**<br>• [e9a0af4bf8d92ef7a0c5f61db8934e94adef9fe0](https://github.com/Kittenswap/contracts-v2/tree/e9a0af4bf8d92ef7a0c5f61db8934e94adef9fe0)<br>&nbsp;&nbsp;(Kittenswap/contracts-v2)

# 범위

- `VotingEscrow.sol`
- `Voter.sol`
- `AlgebraGaugeFactory.sol`
- `AlgebraGauge.sol`
- `AlgebraVaultFactory.sol`

# 발견 사항

# [C-01] `carryVoteForward()`로 이중 투표 가능

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`Voter.carryVoteForward()` 함수를 사용하면 투표자가 다가오는 기간 동안 이전 기간의 투표(풀 및 가중치)를 재사용할 수 있습니다. 그러나 이 함수는 토큰을 다음 기간에 투표한 것으로 표시하지 않습니다(`period[nextPeriod].voted[_tokenId] = true`를 설정하지 않음).

```solidity
function carryVoteForward(uint256 _tokenId, uint256 _fromPeriod) public {
        IVoter.Period storage ps = period[_fromPeriod];
        uint256 nextPeriod = getCurrentPeriod() + 1;

        // fetch weights from previous period
        address[] memory _poolList = ps.tokenIdVotedList[_tokenId].values();
        uint256[] memory _weightList = new uint256[](_poolList.length);

        for (uint256 i; i < _poolList.length; i++) {
            address _gauge = gauge[_poolList[i]].gauge;
            _weightList[i] = ps.tokenIdVotes[_gauge][_tokenId];
        }

        // deposit votes to next period
        _vote(nextPeriod, _tokenId, _poolList, _weightList);
    }
```

결과적으로 `vote()` 함수를 통해 투표하는 데 사용되지 않은 토큰과 함께 `carryVoteForward()`를 사용하면 `checkPeriodVoted(nextPeriod)`가 `false`를 반환하여 `VotingEscrow` 계약의 `notVoted` 수정자를 우회할 수 있습니다. 이 수정자는 `merge()`, `split()`, `withdraw()`와 같은 중요한 함수와 ERC721 재정의된 `_update()` 함수에서 사용되어 투표에 참여하는 토큰이 투표 도중에 조작되거나 전송될 수 없도록 합니다.

```solidity
modifier notVoted(uint256 _tokenId) {
        if (
            IVoter(voter).checkPeriodVoted(
                PeriodLibrary.getPeriod(block.timestamp) + 1,
                _tokenId
            ) == true
        ) revert Voted();
        _;
    }
```

```solidity
    function _update(
        address _to,
        uint256 _tokenId,
        address _auth
    ) internal override notVoted(_tokenId) returns (address) {
        return ERC721EnumerableUpgradeable._update(_to, _tokenId, _auth);
    }
```
예를 들어: 악의적인 행위자는 자신의 NFT를 사용하여 `carryVoteForward()`를 사용하여 투표한 다음, 이 NFT를 분할하고(최대 잠금 기간 동안 잠기므로 더 큰 투표권으로 여러 NFT를 얻음), 같은 기간에 이러한 새 NFT로 다시 투표할 수 있습니다. (사용자가 원래 NFT를 사용하여 이미 투표한 경우).

## 권장 사항

`period[nextPeriod].voted[_tokenId] = true`를 설정하도록 `carryVoteForward()`를 업데이트하십시오.

```diff
function carryVoteForward(uint256 _tokenId, uint256 _fromPeriod) public {
        IVoter.Period storage ps = period[_fromPeriod];
        uint256 nextPeriod = getCurrentPeriod() + 1;

        // fetch weights from previous period
        address[] memory _poolList = ps.tokenIdVotedList[_tokenId].values();
        uint256[] memory _weightList = new uint256[](_poolList.length);

        for (uint256 i; i < _poolList.length; i++) {
            address _gauge = gauge[_poolList[i]].gauge;
            _weightList[i] = ps.tokenIdVotes[_gauge][_tokenId];
        }

+       period[nextPeriod].voted[_tokenId] = true;
        // deposit votes to next period
        _vote(nextPeriod, _tokenId, _poolList, _weightList);
    }
```

# [H-01] 종료된 게이지에 대한 반복적인 분배로 유효한 분배 차단 가능

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

**참고: 종료된 (표준) 게이지에 대한 분배는 현재 실패하므로 이 보고서는 언급된 종료된 게이지가 AlgebraGauge라고 가정합니다.**

게이지가 종료되면 해당 게이지에 대한 현재 기간의 보류 중인 방출량이 `minter` 계약으로 다시 라우팅됩니다. 또한 `es.distributed` 플래그는 `true`로 설정되지 *않습니다*. 이는 게이지가 부활했을 때 놓친 방출량을 재분배할 수 있도록 하기 위함입니다.

```solidity
    function _distribute(uint256 _period, address _gauge) internal {
...
        IVoter.Emissions storage es = ps.gaugeEmissions[_gauge];
        if (es.distributed) revert EmissionsAlreadyDistributedForPeriod();

        uint256 emissions = (ps.totalEmissions * ps.gaugeTotalVotes[_gauge]) /
            ps.globalTotalVotes;
...
        if (gauge[_pool].isAlive == false) {
            kitten.transfer(address(minter), emissions);
            emissions = 0;
        } else {
            es.amount = emissions;
            es.distributed = true;
        }

        if (gauge[_pool].isAlgebra) _claimAndDistributeAlgebraPoolFees(_pool);
        kitten.approve(_gauge, emissions);
        IGauge(_gauge).notifyRewardAmount(emissions);
...
    }
```

이 때문에 공격자는 종료된 게이지에 대해 반복적으로 분배를 트리거하고 다른 게이지를 위한 방출량을 `minter` 계약으로 효과적으로 다시 전송할 수 있습니다. 그러면 Voter 계약에 실제로 배포할 `KITTEN` 토큰이 충분하지 않기 때문에 다른 게이지에 대한 유효한 분배 호출이 실패하게 됩니다.

이 버그로 인해 두 가지 뚜렷한 영향이 있다는 점에 유의하는 것이 중요합니다.

1. 유효한 방출 분배가 일시적으로 차단됨 (표준 및 대수 게이지 모두에 영향):

- 공격자는 종료된 게이지에 대해 반복적으로 분배를 트리거하여 Voter의 KITTEN 잔액을 채굴자 계약으로 다시 빼낼 수 있습니다. 이는 현재 기간의 다른 게이지에 대한 유효한 분배가 성공하는 것을 방지합니다. 이러한 누락된 방출은 나중에 소급하여 분배해야 합니다.

2. 투표자에 대한 보상 손실 (`AlgebraGauges`에만 영향):

- 분배가 차단된 영향을 받는 게이지 중 하나라도 `AlgebraGauges`인 경우, 해당 기간 동안 누적된 풀 수수료는 해당 기간 동안 투표한 투표자가 청구할 수 없습니다. 이는 `AlgebraGauges`의 풀 수수료가 **기간별(period-specific)**이기 때문입니다. 분배가 발생하면 수수료는 게이지 `votingReward` 계약에 투표 보상으로 적립되지만 현재 기간의 투표자에게만 해당됩니다. (악용으로 인해) 해당 기간의 배포가 실패하면 보류 중인 수수료는 게이지의 커뮤니티 저장소에 남아 배포가 성공할 때 나중 기간의 투표 보상으로 대신 적립됩니다. 이러한 투표 보상은 원래 기간의 투표자에게 소급하여 적립될 수 없습니다. 결과적으로 영향을 받는 기간에 `AlgebraGauge`에 투표한 투표자는 게이지가 최종적으로 성공적인 배포를 받을 때까지 후속 기간 동안 동일한 게이지에 계속 투표하지 않는 한 해당 보상을 영구적으로 잃게 됩니다.

### 공격 시나리오

다음 시나리오를 고려하십시오.

우리는 `period_1`에 있고 3개의 게이지 `gauge_a`, `gauge_b`, `gauge_c`가 있습니다. `period_2`에 투표할 때 Alice는 `gauge_a`에 45표, Bob은 `gauge_b`에 45표, Carol은 `gauge_c`에 10표를 할당합니다. `gauge_c`가 이제 `period_1` 중에 종료된다고 가정합니다. 이제 `period_2`가 시작되고 게이지에 대한 방출량으로 배포될 `100` KITTEN 토큰이 있다고 가정합니다.

```
# current state before distributions

minter_contract_balance = 100
voter_contract_balance = 0

# gauge_a_expected_emissions = 45
# gauge_b_expected_emissions = 45
# gauge_c_expected_emissions = 10 (killed gauge)
```

`period_2`가 시작될 때 공격자가 `distribute(gauge_c)`를 호출하면 `100` KITTEN이 채굴자 계약에서 투표자 계약으로 전송되고, `gauge_c`가 종료되었으므로 10 KITTEN 방출량이 채굴자 계약으로 다시 라우팅됩니다. 이 시점의 상태는 다음과 같습니다.

```
# current state after distribution for gauge_c

minter_contract_balance = 10
voter_contract_balance = 90
```

이제 공격자가 `distribute(gauge_c)`를 다시 호출한다고 가정하면, 투표자 계약에서 `minter` 계약으로 또 다른 `10` KITTEN 토큰이 전송됩니다. 새 상태:

```
# current state after second distribution call for gauge_c

minter_contract_balance = 20
voter_contract_balance = 80
```

보시다시피 공격자는 `distribute(gauge_c)`를 8번 더 호출하여 Voter의 전체 KITTEN 잔액을 `minter` 계약으로 다시 전송할 수 있습니다. 이런 일이 발생하면 Voter 계약에 배포할 KITTEN 토큰이 충분하지 않기 때문에 `gauge_a` 및 `gauge_b`에 대한 방출 분배 호출이 실패합니다.

공격자는 향후 배포에 대해서도 이 악용을 수행할 수 있으며 잠재적으로 향후 배포의 대다수를 차단할 수 있습니다. 그러나 다음과 같은 경우에만 향후 배포에 대해 발생할 수 있습니다.

1. 종료된 대수 게이지가 부활하지 않았습니다.
2. 종료된 게이지와 관련된 대수 풀의 `algebraFee`가 `0`으로 설정되지 *않았습니다*.
3. 종료된 게이지에 대한 영구 파밍 가상 풀 인센티브가 비활성화되지 *않았습니다*.

종료된 대수 게이지 비활성화는 취소할 수 없으므로 게이지가 부활할 것으로 예상되는 경우 이는 발생하지 않을 것입니다. `algebraFee`가 `0`으로 설정되면 이는 `algebraFeeReceiver`의 손실을 나타내지만 이는 가능합니다. 그러나 다음 기간에 `algebraFee`가 `0`으로 설정되더라도 현재 기간 동안 영향을 받는 대수 게이지의 보류 중인 수수료는 다음 기간으로 리디렉션되어 현재 기간 동안 해당 게이지에 투표한 사용자에게 손실이 발생합니다.

**악용에 대한 기술적 세부 사항:** 공격자는 다른 게이지에 배포하기 위한 다른 호출 *전에* 현재 기간의 맨 위에 `distribute(killed_algebra_gauge)` 호출이 포함되도록 해야 합니다.

## 권장 사항

종료된 게이지에 대해 별도의 플래그(예: `es.killedDistributed`)를 도입합니다. 종료된 게이지에 대해 배포가 호출될 때:

- 주어진 `period`에 대해 `es.killedDistributed`가 이미 `true`로 설정된 경우 `minter` 계약으로 방출량을 다시 전송하지 않고 조기에 반환합니다.

- 그렇지 않은 경우 종료된 게이지에 대해 단일 배포를 수행한 다음 `es.killedDistributed = true`로 설정합니다.

이렇게 하면 종료된 게이지가 유효한 기간당 **한 번**의 배포만 트리거하여 반복적인 배포를 방지할 수 있습니다. 이렇게 하면 게이지가 부활할 때 적절한 방출량을 소급하여 배포할 수 있습니다.

# [H-02] 보상률이 0으로 재설정되고 투표자로부터 미래 보상을 훔칠 수 있음

_해결됨_

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`Voter.distribute(uint256 _period, address _gauge)` 함수를 사용하면 호출자가 현재 기간에 게이지에 방출량을 배포할 때 유효한 과거 기간을 지정할 수 있습니다. 이는 게이지에 대한 누락된 배포를 현재 기간에 배포할 수 있도록 하기 위한 것입니다.

그러나 `_distribute` 함수는 제공된 `gauge`가 제공된 기간 동안 존재하지 않아 `ps.gaugeTotalVotes[_gauge] == 0`인 경우를 고려하지 않습니다. 이 경우 제공된 기간이 투표가 있었던 유효한 과거 기간이므로(`ps.globalTotalVotes > 0`) `emissions`은 `0`으로 계산됩니다.

```solidity
        uint256 emissions = (ps.totalEmissions * ps.gaugeTotalVotes[_gauge]) / // @audit: gauge could have 0 votes for past period
            ps.globalTotalVotes;
        address _pool = gaugeToPool[_gauge];

        // transfer emissions to minter if gauge is killed
        if (gauge[_pool].isAlive == false) {
            kitten.transfer(address(minter), emissions);
            emissions = 0;
        } else {
            es.amount = emissions;
            es.distributed = true;
        }

        if (gauge[_pool].isAlgebra) _claimAndDistributeAlgebraPoolFees(_pool);
        kitten.approve(_gauge, emissions);
        IGauge(_gauge).notifyRewardAmount(emissions);
```

이를 통해 현재 기간에 대한 실제 방출량이 이미 실행된 *후에* `_claimAndDistributeAlgebraPoolFees` 및 `gauge::notifyRewardAmount(0)`이 트리거될 수 있습니다. 문제가 되는 게이지가 `AlgebraGauge`인 경우 두 가지 공격 벡터가 열립니다.

**보상률을 0으로 재설정**

`algebraGauge::notifyRewardAmount(0)`이 호출되면 관련 영구 파밍 가상 풀에 대한 보상률이 `0`으로 재설정됩니다.

```solidity
// AlgebraGauge.sol

    function _notifyRewardAmount(
        uint256 _rewardAmount,
        uint256 _bonusRewardAmount
    ) internal {
        uint256 duration = PeriodLibrary.periodNext(block.timestamp) -
            block.timestamp;
...
        algebraGaugeFactory.setRates(
            key,
            SafeCast.toUint128(_rewardAmount / duration), // @audit: resets rates to `0` when _rewardAmount == 0
            SafeCast.toUint128(_bonusRewardAmount / duration)
        );
    }
```

```solidity
// EternalVirtualPool.sol

  function setRates(uint128 rate0, uint128 rate1) external override onlyFromFarming {
    _distributeRewards(); // @audit: distribute rewards based on previous rates
    (rewardRate0, rewardRate1) = (rate0, rate1); // @audit: update rates to 0
  }
```

공격자는 이를 이용하여 게이지의 영구 파밍 가상 풀에 대한 보상 분배를 차단할 수 있습니다.

현재 `period_5`에 있고 새로운 `AlgebraGauge`가 추가되었다고 가정합니다(보상률은 `0`으로 초기화됨).

```
# gauge eternal virtual pool state

reward_rate = 0
reward_reserves = 1 (1 wei of rewards added during initialization)
```

사용자는 이제 다음 기간인 `period_6` 동안 이 게이지에 투표할 수 있습니다. 이제 `period_6`에 들어가고 이 게이지에 방출량이 배포되고 보상률이 업데이트됩니다.

```
# gauge eternal virtual pool state

reward_rate = x
reward_reserves = 1 + y (y is reward amount added in period_6)
```

공격자는 즉시 `distribute(4, algebraGauge)`를 호출할 수 있으며, 이는 궁극적으로 `algebraGauge::notifyRewardAmount(0)`을 트리거하고 보상률을 다시 `0`으로 재설정합니다.

```
# gauge eternal virtual pool state

reward_rate = 0
reward_reserves = 1 + y
```

다음 기간에 `algebraGauge.notifyRewardAmount`가 다시 호출되면 현재 비율이 `0`으로 재설정되었으므로 가상 풀은 보류 중인 보상을 분배하지 않습니다.

공격자는 `n`번 분배 기간 동안 이 악용을 반복할 수 있습니다. 여기서 `n`은 문제의 대수 게이지가 생성되기 전에 존재했던 유효 기간의 수입니다.

**보류 중인 보상을 현재 기간으로 리디렉션**

또한 배포가 실행되면 관련 커뮤니티 저장소에 보관된 보류 중인 대수 풀 수수료가 해당 게이지에 대한 `votingReward` 계약으로 전송되고 현재 기간에 할당됩니다.

```solidity
// Voter::_distribute

        if (gauge[_pool].isAlgebra) _claimAndDistributeAlgebraPoolFees(_pool);
```

```solidity
// Voter::_claimAndDistributeAlgebraPoolFees

    function _claimAndDistributeAlgebraPoolFees(address _pool) internal {
...
        votingReward.notifyRewardAmount(address(token0), token0Claimed);
...
        votingReward.notifyRewardAmount(address(token1), token1Claimed);
        emit DistributeAlgebraFees(_pool, token0Claimed, token1Claimed);
    }
```

```solidity
// Reward.sol

    function notifyRewardAmount(
        address _token,
        uint256 _amount
    ) public virtual nonReentrant onlyRole(NOTIFY_ROLE) {
        uint256 currentPeriod = getCurrentPeriod();
        _addReward(currentPeriod, _token, _amount);
```

따라서 이러한 보상은 이전 기간에 투표한 사용자를 위한 것입니다. 공격자는 이전에 언급한 버그를 악용하여 해당 기간의 모든 게이지에 대해 이미 배포가 이루어진 *후* 현재 기간의 *끝*에 `_claimAndDistributeAlgebraPoolFees`를 트리거할 수 있습니다. 이로 인해 다음 기간을 위한 보상의 대다수가 현재 기간에 배포됩니다.

`period_5`에 있다고 가정합니다. 새로운 `algebraGauge`가 생성되고 사용자는 `period_6` 동안 투표하기 시작합니다. `period_5` 동안 풀 수수료가 게이지의 커뮤니티 저장소에 누적되어 사용자가 이 게이지에 투표하도록 더욱 유도합니다. `period_6`이 시작되면 `algebraGauge`에 대한 배포가 이루어지고 모든 보류 중인 보상은 `period_6`에 대한 게이지의 `votingReward` 계약으로 배포됩니다. 지금까지는 예상대로 작동하고 있습니다.

이제 `period_6` 동안 풀 수수료가 다시 저장소에 누적되기 시작하여 사용자가 `period_7`에 대해 이 게이지에 다시 투표하도록 유도합니다. 그러나 `period_6`의 끝에서 공격자는 `distribute(4, algebraGauge)`를 호출하여 `period_6`에 대해 `_claimAndDistributeAlgebraPoolFees`를 두 번째로 트리거합니다. 그런 다음 `period_7`이 시작되고 배포가 이루어지면 저장소에서 더 적은 풀 수수료가 징수되고 `period_7`의 투표자는 예상 투표 보상을 받지 못합니다. 따라서 `period_7`을 위한 보상은 `period_6`으로 성공적으로 리디렉션되었습니다.

첫 번째 공격 벡터와 유사하게 이 악용은 `n`번 실행될 수 있습니다. 여기서 `n`은 문제의 대수 게이지가 생성되기 전에 발생한 유효한 과거 기간의 수입니다.

## 권장 사항

게이지에 배포할 때 해당 기간 동안 게이지에 표가 없는 경우 풀 수수료를 한 기간에 두 번 실수로 배포하고 가상 풀의 보상률을 `0`으로 잘못 재설정하는 것을 방지하기 위해 조기에 반환하십시오.

# [H-03] `Voter::carryVoteForward()`를 통한 무허가 투표

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`Voter.sol`의 `carryVoteForward` 함수에는 필수 액세스 제어가 부족하여 모든 사용자가 소유자의 승인 없이 모든 NFT에 대한 투표를 조작할 수 있습니다.

```solidity
    /// @notice Carry forward votes from past period
    function carryVoteForward(uint256 _tokenId, uint256 _fromPeriod) public {
        IVoter.Period storage ps = period[_fromPeriod];
        uint256 nextPeriod = getCurrentPeriod() + 1;
```

이 취약점의 결과는 누구나 `veKITTEN`에 대해 `carryVoteForward()`를 호출하고 다음 기간에 대해 투표할 수 있으며(`veKITTEN`의 이전 투표를 복사하여) 원래 소유자의 승인 없이 투표할 수 있다는 것입니다.

## 권장 사항

다음과 같이 `carryVoteForward()`에 대한 액세스 제어를 구현하십시오.

```solidity
        if (veKitten.isApprovedOrOwner(msg.sender, _tokenId) == false)
            revert NotApprovedOrOwner();
```

# [M-01] `claimAndDistributeAlgebraPoolFees()`에서 투표자가 없으면 커뮤니티 수수료 낭비됨

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Voter` 계약에서 `_claimAndDistributeAlgebraPoolFees()` 함수는 Algebra 게이지에 대한 `_distribute()` 흐름 중에 호출됩니다.

1. Algebra 풀에서 커뮤니티 수수료를 수집하고 인출합니다.
2. **현재 기간**의 게이지 투표자에게 분배하기 위해 `votingReward.notifyRewardAmount()`를 통해 관련 `VotingReward` 계약으로 이러한 수수료를 보냅니다.

```solidity
 function _distribute(uint256 _period, address _gauge) internal {
        //...

        uint256 emissions = (ps.totalEmissions * ps.gaugeTotalVotes[_gauge]) /
            ps.globalTotalVotes;
        //...

        // transfer emissions to minter if gauge is killed
        if (gauge[_pool].isAlive == false) {
            kitten.transfer(address(minter), emissions);
            emissions = 0;
        } else {
            es.amount = emissions;
            es.distributed = true;
        }

        if (gauge[_pool].isAlgebra) _claimAndDistributeAlgebraPoolFees(_pool); // @audit : if emissions = 0 (no voters) this will still be called to add voting rewards for a period that doesn't have voters
        //...
    }
```

```solidity
 function _claimAndDistributeAlgebraPoolFees(address _pool) internal {
        //...
        IAlgebraCommunityVault vault = IAlgebraCommunityVault(
            algebraPool.communityVault()
        );

        //...
        vault.withdraw(address(token0), token0BalVault);
        uint256 token0Claimed = token0.balanceOf(address(this)) -
            token0BalBefore;
        token0.approve(address(votingReward), token0Claimed);
        votingReward.notifyRewardAmount(address(token0), token0Claimed);

        //...
    }
```

```solidity
// @note : VotingReward.notifyRewardAmount()
    function notifyRewardAmount(
        address _token,
        uint256 _amount
    ) public virtual nonReentrant onlyRole(NOTIFY_ROLE) {
        uint256 currentPeriod = getCurrentPeriod();
        _addReward(currentPeriod, _token, _amount);

        emit NotifyReward(currentPeriod, msg.sender, _token, _amount);
    }
```

그러나 게이지가 현재 기간 동안 **투표를 받지 못한 경우** 인출된 커뮤니티 수수료는 여전히 현재 기간에 대해 방출되며, 현재 기간에 게이지에 투표한 유권자가 없으므로 아무도 이 보상을 청구할 자격이 없습니다. 결과적으로 이러한 커뮤니티 수수료(투표 보상)는 나중 기간의 투표자에게 분배되는 대신 손실됩니다(참고: `VotingReward` 계약에는 갇힌 토큰을 구하기 위한 `transferERC20()` 함수가 포함되어 있습니다. 그러나 여기서 문제는 토큰 복구가 아니라 커뮤니티 수수료의 **불공정한 할당**에 관한 것입니다).

## 권장 사항

`_claimAndDistributeAlgebraPoolFees(_pool)`를 호출하기 전에 다음을 확인하여 게이지에 현재 기간에 투표자가 있는지 확인하십시오.

```diff
 function _distribute(uint256 _period, address _gauge) internal {
        //...

        uint256 emissions = (ps.totalEmissions * ps.gaugeTotalVotes[_gauge]) /
            ps.globalTotalVotes;
        //...

        // transfer emissions to minter if gauge is killed
        if (gauge[_pool].isAlive == false) {
            kitten.transfer(address(minter), emissions);
            emissions = 0;
        } else {
            es.amount = emissions;
            es.distributed = true;
        }

-       if (gauge[_pool].isAlgebra) _claimAndDistributeAlgebraPoolFees(_pool);
+       if (emissions > 0 && gauge[_pool].isAlgebra) {
+           _claimAndDistributeAlgebraPoolFees(_pool);
+       }
    }
```

# [M-02] `carryVoteForward()`는 투표 불변성을 위반함

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Voter.carryVoteForward()` 함수를 사용하면 사용자가 다음 기간의 이전 기간에서 투표 기본 설정(풀 및 가중치)을 재사용할 수 있습니다. 이는 사용자의 재투표를 단순화하기 위한 것입니다.

```solidity
function carryVoteForward(uint256 _tokenId, uint256 _fromPeriod) public {
        IVoter.Period storage ps = period[_fromPeriod];
        uint256 nextPeriod = getCurrentPeriod() + 1;
//...
        // deposit votes to next period
        _vote(nextPeriod, _tokenId, _poolList, _weightList);
    }
```

그러나 프로토콜은 주어진 토큰 ID에 대한 투표가 기간당 한 번만 사용될 수 있음을 강제합니다. 이 불변성은 `_checkPeriodVoted()`를 통해 토큰이 대상 기간에 이미 투표했는지 확인하여 `vote()` 함수에서 준수됩니다.

```solidity
function vote(
        uint256 _tokenId,
        address[] memory _poolList,
        uint256[] memory weightList
    ) public {
        uint256 nextPeriod = getCurrentPeriod() + 1;

        // do all the checks
        if (_checkPeriodVoted(nextPeriod, _tokenId))
            revert AlreadyVotedInPeriod();
        //...
    }
```

그러나 `carryVoteForward()` 함수에는 이러한 확인이 없습니다. 토큰이 다음 기간에 이미 투표한 경우 `carryVoteForward()`를 호출하면 기존 투표를 덮어쓰게 되어 투표 변경이 기간당 한 번만 발생할 수 있다는 불변성이 깨집니다.

## 권장 사항

`vote()` 함수와 동일한 논리를 구현하여 토큰이 다음 기간에 아직 투표하지 않았는지 확인하기 위해 `carryVoteForward()`에 확인을 추가하십시오.

# [M-03] 투표자 없이 `Voter.notifyRewardAmount()`가 호출되면 방출량 갇힘

_해결됨_

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`Voter` 계약에서 `notifyRewardAmount()` 함수는 누구나 현재 기간 동안 `KITTEN` 토큰을 기부할 수 있도록 허용하며, 이는 총 투표 점유율에 따라 등록된 게이지에 분배됩니다. 이 함수는 `Minter.updatePeriod()`에 의해서도 호출되어 `Voter.start()` 호출 후 다음 에포크 기간부터 방출량을 발행하고 할당합니다.

그러나 이 함수는 현재 기간에 활성 투표자가 있는지 확인하지 않습니다. `period[_period].globalTotalVotes > 0`인지 확인하지 않습니다. 결과적으로 투표가 없는 기간 동안 함수가 호출되면 기부된 방출량은 계약에 갇혀 어떤 게이지에도 분배되지 않습니다.

```solidity
  function notifyRewardAmount(uint256 _amount) public {
        uint256 currentPeriod = getCurrentPeriod();

        kitten.safeTransferFrom(msg.sender, address(this), _amount);
        period[currentPeriod].totalEmissions += _amount;
    }
```

```solidity
function _distribute(uint256 _period, address _gauge) internal {
        //...
        IVoter.Period storage ps = period[_period];
        IVoter.Emissions storage es = ps.gaugeEmissions[_gauge];
        if (es.distributed) revert EmissionsAlreadyDistributedForPeriod();

        uint256 emissions = (ps.totalEmissions * ps.gaugeTotalVotes[_gauge]) /
            ps.globalTotalVotes;
       //...
    }
```

## 권장 사항

`period[_period].globalTotalVotes == 0`을 확인하여 현재 기간에 투표자가 없는 경우 `Voter.notifyRewardAmount()` 호출을 허용하지 않거나, 분배되지 않은 보상을 추적하여 다음 기간으로 이월하십시오.

# [L-01] 일부 계약이 UUPS 모범 사례를 따르지 않음

_해결됨_

`AlgebraVaultFactory` 및 `AlgebraGauge`는 둘 다 UUPS 프록시 뒤에 배포되지만 `constructors` 내부에서 `_disableInitializers`를 호출하지 않습니다. 즉, 누구나 구현 계약에서 직접 `initialize`를 호출하여 구현의 소유자가 될 수 있습니다. 이는 구현에서 `delegatecall` 기능을 노출하여 `selfdestruct`를 트리거할 수 있는 주요 공격 벡터에서 오는 것이므로 현재 위험은 없어 보입니다. 그러나 구현에는 `delegatecall` 기능이 없으며 `selfdestruct`는 더 이상 계약의 코드를 삭제하지 않습니다.

그렇긴 하지만 최상의 보안 관행을 따르는 정신으로 계약의 생성자 내부에서 `_disableInitializers`를 호출하는 것을 고려하십시오.

# [L-02] 죽은 게이지에 대해 수수료 청구 및 보상 분배가 계속됨

_해결됨_

`_distribute()` 함수는 죽은 게이지에 대한 수수료 청구를 처리합니다. 게이지가 종료된 경우에도(`isAlive`가 거짓임을 의미) 분배 로직은 Algebra 풀에서 수수료 청구를 실행합니다.

```solidity
        // transfer emissions to minter if gauge is killed
        if (gauge[_pool].isAlive == false) {
            kitten.transfer(address(minter), emissions);
            emissions = 0;
        } else {
            es.amount = emissions;
            es.distributed = true;
        }

        if (gauge[_pool].isAlgebra) _claimAndDistributeAlgebraPoolFees(_pool); // <<<------
```

결과적으로 Algebra 풀 수수료는 죽은 게이지에 대해 청구되고 처리되는 반면, `isAlive`가 거짓이므로 kitten 방출량은 분배되지 않습니다.

게이지가 종료된 경우 veKITTEN 게이지 투표자에게 algebra 풀 수수료를 청구하지 않는 것을 고려하십시오.

# [L-03] `split()` 함수의 내림으로 인해 먼지(dust) 토큰 손실 발생

_해결됨_

`split()` 함수는 정수 나눗셈을 사용하는데, 이는 내림을 수행하여 잠재적으로 소량의 토큰 금액이 영구적으로 손실될 수 있습니다.

```solidity
        for (uint256 i; i < _weightList.length; i++) {
            // calc amount based on weight
            uint256 newAmount = (lockedAmount * _weightList[i]) / totalWeight;
```

그 결과 소량의 토큰이 반올림으로 인해 영구적으로 손실되어 여러 분할 작업에 걸쳐 시간이 지남에 따라 누적됩니다.

먼지 손실을 방지하기 위해 남은 금액을 마지막 NFT에 할당하는 것을 고려하십시오.

# [L-04] `start()` 함수에 다중 호출에 대한 보호 부족

_인정됨_

`start()` 함수는 Voter의 실제 시작 후 호출되지 않도록 하는 보호 없이 여러 번 호출될 수 있습니다.

```solidity
    /// @notice Start ve33 voting and epoch 0
    function start() external onlyOwnerOrAuthorized {
        epoch0Period = getCurrentPeriod();
        minter.start();
    }
```

`minter.start` 호출이 `lastMintedPeriod = getCurrentPeriod();`를 업데이트하므로 이는 심각한 문제를 일으킬 수 있습니다. 따라서 `start()`가 호출되고 `Voter`의 현재 기간에 대해 KITTEN 발행이 아직 완료되지 않은 경우 이는 사실상 전체 기간에 대한 KITTEN 방출량이 분배되지 않는 결과를 초래합니다.

`start()`에 대한 다중 호출을 방지하기 위해 이 안전 확인을 추가하는 것을 고려하십시오.

```solidity
require(epoch0Period == 0, "Already started");
```

# [L-05] 업그레이드 가능한 계약인 `Voter`에 스토리지 `__gap` 누락

_해결됨_

`Voter` 계약은 여러 OpenZeppelin 업그레이드 가능한 계약을 상속하지만 향후 업그레이드를 위한 슬롯을 예약하기 위한 스토리지 갭(`uint256[50] private __gap;`)이 없어 잠재적으로 스토리지 레이아웃 충돌이 발생할 수 있습니다.

```solidity
contract Voter is
    IVoter,
    UUPSUpgradeable,
    Ownable2StepUpgradeable,
    AccessControlUpgradeable,
    ReentrancyGuardUpgradeable
{
    using SafeERC20 for IERC20;
    using EnumerableSet for EnumerableSet.AddressSet;
```

계약 끝에 스토리지 갭을 추가하십시오.

```solidity
uint256[50] private __gap;
```

# [L-06] `AlgebraVaultFactory` 초기화에서 `__UUPSUpgradeable_init()` 호출 누락

_인정됨_

`AlgebraVaultFactory.initialize()` 함수는 `UUPSUpgradeable`을 상속하지만 초기화 중에 `__UUPSUpgradeable_init()`을 호출하지 않습니다.

```solidity
    function initialize(
        address _algebraFactory,
        address _voter,
        address _initialOwner
    ) public initializer {
        __Ownable2Step_init();
        __Ownable_init(_initialOwner);

        algebraFactory = _algebraFactory;
        voter = _voter;
    }
```

최신 버전의 `UUPSUpgradeable`은 명시적 초기화가 필요하지 않을 수 있지만 프로토콜의 다른 계약에 있는 다른 초기화 호출과 비교할 때 일관되지 않은 패턴은 잠재적 혼란을 야기합니다.

누락된 호출을 추가하는 것을 고려하십시오.

```solidity
__UUPSUpgradeable_init();
```

# [L-07] `_createGauge()`로 기존 게이지 덮어쓰기 허용

_해결됨_

`Voter` 계약에서 `_createGauge()` 함수는 계약 소유자 또는 권한 있는 주소가 호출하여 주어진 풀에 대한 새로운 Algebra 게이지 또는 표준 게이지를 생성할 수 있습니다.

배포할 게이지가 표준 유형(Algebra 아님)인 경우 동일한 풀에 대한 기존 게이지를 덮어쓰지 않도록 하는 확인이 없습니다.

```solidity
 function _createGauge(
        address _poolAddress,
        bool _isAlgebra
    ) internal returns (address _gauge, address _votingReward) {
        Gauge storage gs = gauge[_poolAddress];

        //...
        gs.votingReward = votingRewardFactory.createVotingReward();
        //...

        if (gs.isAlgebra) {
            // create new AlgebraCommunityVault for gauge and set to pool
            gs.vault = algebraVaultFactory.createVaultForPool(
                _poolAddress,
                msg.sender,
                address(0),
                _token0,
                _token1
            );

            //...
            gs.gauge = algebraGaugeFactory.createGauge(
                _poolAddress,
                seedAmount
            );
        } else {
            gs.gauge = gaugeFactory.createGauge(_poolAddress, gs.votingReward);
        }
       //...
    }
```

권장 사항:
`_createGauge()`가 진행되도록 허용하기 전에 주어진 풀에 대한 게이지가 아직 생성되지 않았는지 확인하는 확인을 추가하십시오.
# [L-08] 만료된 잠금이 `split()`을 통해 만료 제한을 우회하여 투표권을 되찾을 수 있음

_해결됨_

`VotingEscrow` 계약에서 `increase_unlock_time()` 함수는 잠금 소유자가 잠금 기간을 연장하여 투표권을 늘릴 수 있도록 설계되었습니다. 그러나 잠금의 현재 잠금 해제 시간이 `block.timestamp`보다 커야 하므로 만료된 잠금이 잠금 해제 시간을 연장하는 것을 허용하지 않는 중요한 확인이 포함되어 있습니다.

```solidity
 function increase_unlock_time(
        uint256 _tokenId,
        uint256 _lock_duration
    ) external nonReentrant onlyAuthorized(_tokenId) {
        LockedBalance memory _locked = locked[_tokenId];
        //...

        if (block.timestamp >= _locked.end) revert LockExpired();
        //...
    }
```

그럼에도 불구하고 이 제한은 `split()` 함수를 통해 우회될 수 있습니다. 여기서 만료된 잠금은 사용자 정의 가중치를 사용하여 여러 개의 새 잠금(NFT)으로 분할될 수 있으며, 새로 생성된 각 잠금은 **원래 잠금의 만료 여부에 관계없이** 자동으로 최대 잠금 기간(2년)을 부여받습니다.

`split()`은 원래 잠금이 만료되었는지 확인하지 않으므로 사용자가 만료된 잠금의 기간을 효과적으로 재설정하고 투표권을 복원할 수 있어 시간 기반 투표 감쇠의 의도된 논리를 위반하고 불공정한 투표권이 발생할 수 있습니다.

권장 사항:
만료된 잠금에서 호출될 수 없도록 `split()` 함수에 만료 확인을 도입하십시오.

# [L-09] 0 전송 실패 시 `claimAndDistributeAlgebraPoolFees()`가 되돌려질 수 있음

_해결됨_

`Voter` 계약에서 `_claimAndDistributeAlgebraPoolFees()` 함수는 Algebra 게이지에 대한 `_distribute()` 중에 호출됩니다. 다음을 수행합니다.

1. Algebra 풀의 커뮤니티 저장소에서 누적된 수수료(`token0`, `token1`)를 수집하고 인출합니다.
2. 이 토큰을 `VotingReward.notifyRewardAmount()`를 통해 `VotingReward` 계약으로 전송하고, `VotingReward`는 `Voter`에서 토큰을 가져옵니다.

```solidity
 function _claimAndDistributeAlgebraPoolFees(address _pool) internal {
        IAlgebraPool algebraPool = IAlgebraPool(_pool);
        IVotingReward votingReward = IVotingReward(gauge[_pool].votingReward);
        IERC20 token0 = IERC20(algebraPool.token0());
        IERC20 token1 = IERC20(algebraPool.token1());
        IAlgebraCommunityVault vault = IAlgebraCommunityVault(
            algebraPool.communityVault()
        );

        uint256 token0BalVault = token0.balanceOf(address(vault));
        uint256 token0BalBefore = token0.balanceOf(address(this));
        vault.withdraw(address(token0), token0BalVault);
        uint256 token0Claimed = token0.balanceOf(address(this)) -
            token0BalBefore;
        token0.approve(address(votingReward), token0Claimed);
        votingReward.notifyRewardAmount(address(token0), token0Claimed);

       //...
    }
```

수집된 토큰 중 하나(예: BNB)가 **0 가치 전송**( `VotingReward` 계약이 보상 금액을 가져오려고 할 때)에서 되돌려지는 경우, 잔액이 0이면 `VotingReward.notifyRewardAmount()` 호출이 되돌려집니다. 이로 인해 `_distribute()`가 실패하여 해당 게이지에 대한 방출량이 갇히게 됩니다.

이 시나리오는 풀에 대한 거래 활동이 아직 발생하지 않았고 커뮤니티 저장소에 수수료가 누적되지 않아 잔액이 0인 경우 발생할 수 있습니다.

권장 사항:
보상 분배를 진행하기 전에 청구된 수수료 금액(`token0Claimed`, `token1Claimed`)이 0보다 큰지 확인하는 확인을 추가하십시오.

# [L-10] `_distribute()`의 반올림으로 인해 방출량 먼지(dust)가 갇힘

_해결됨_

`Voter` 계약에서 `_distribute()` 함수는 주어진 기간의 총 투표에 대한 득표 비율을 기준으로 각 등록된 게이지에 대한 방출 점유율(보상)을 계산합니다.

그러나 보상 계산은 정수 나눗셈으로 인해 내림됩니다. 할당되지 않은 이러한 먼지 방출량은 `Voter` 계약에 누적되어 청구하거나 인출할 수 없으므로 영구적으로 갇히게 됩니다.

```solidity
 function _distribute(uint256 _period, address _gauge) internal {
        //...
        uint256 emissions = (ps.totalEmissions * ps.gaugeTotalVotes[_gauge]) /
            ps.globalTotalVotes;
        //...
    }
```

권장 사항:
할당되지 않은 먼지 방출량을 추적하여 향후 기간에 할당하십시오.

# [L-11] `updatePeriod()` 전에 채굴자를 변경하면 보상을 놓침

_인정됨_

`Voter.setMinter()` 함수를 사용하면 권한 있는 역할이 `minter` 주소를 업데이트할 수 있습니다. 이 채굴자는 `notifyRewardAmount()`를 호출하여 각 기간에 대해 보상을 방출하는 역할을 합니다.

```solidity
  function setMinter(address _minter) external onlyOwnerOrAuthorized {
        minter = IMinter(_minter);
    }
```

그러나 이전 채굴자가 `updatePeriod()`를 호출하여 현재 기간의 방출량을 확정하고 방출하기 **전에** `minter`가 변경되면 해당 방출량을 놓칠 수 있습니다. 이는 새 채굴자가 **다음 기간**(즉, `현재 기간 + 1`)부터 보상 방출을 시작하기 때문입니다. 결과적으로 분배가 발생하지 않은 경우 현재(또는 이전) 기간에 대한 보상이 손실될 수 있습니다.

```solidity
    function updatePeriod() external returns (bool) {
        uint256 currentPeriod = getCurrentPeriod();
        if (currentPeriod > lastMintedPeriod) {
           //...
            kitten.approve(address(voter), emissions);
            voter.notifyRewardAmount(emissions);
            //...
        }
        return false;
    }
```

권장 사항:
`setMinter()`를 통해 채굴자 주소를 변경하기 전에 `updatePeriod()`가 호출되었는지 확인하는 확인 또는 완화 메커니즘을 추가하십시오.

# [L-12] 악의적인 행위자와 0 방출량으로 인해 `distributeAll()`에서 DoS 발생

_해결됨_

`Voter` 계약에는 현재 기간 동안 등록된 게이지에 `KITTEN` 보상을 분배하는 여러 함수가 있습니다.

- `distributeAll()`: 수수료를 징수하고 등록된 모든 게이지에 보상을 분배합니다.
- `distributeRange()`: 등록된 게이지의 하위 집합에 보상을 분배합니다.
- `distribute(address _gauge)`: 특정 게이지에 보상을 분배합니다.

이 모든 함수는 보상 계산을 `_distribute()`에 위임하는데, 이 함수는 이중 분배를 방지하기 위해 `gaugeEmissions[_gauge].distributed == false`인지 확인합니다. 보상이 이미 분배된 경우 `_distribute()`는 `EmissionsAlreadyDistributedForPeriod`로 되돌립니다.

```solidity
function _distribute(uint256 _period, address _gauge) internal {
        // check to see if trying to distribute for future period
        if (_period > getCurrentPeriod()) revert InvalidPeriod();

        IVoter.Period storage ps = period[_period];
        IVoter.Emissions storage es = ps.gaugeEmissions[_gauge];
        if (es.distributed) revert EmissionsAlreadyDistributedForPeriod(); //! @audit
        //...
        kitten.approve(_gauge, emissions);
        IGauge(_gauge).notifyRewardAmount(emissions); //! @audit
        //...
    }
```

이로 인해 악의적인 행위자가 `distributeAll()` 또는 `distributeRange()` 호출에 앞서 특정 게이지에 대해 `distribute(_gauge)`를 호출할 수 있는 문이 열립니다. 이러한 호출에는 반복에 동일한 게이지가 포함되고 `_distribute()`는 이미 배포된 게이지에 대해 되돌리기 때문에 전체 `distributeAll()` 또는 `distributeRange()` 트랜잭션이 되돌려집니다.

또한 일반(비 Algebra) 게이지에 대해 계산된 `emissions`이 0인 경우 `IGauge(_gauge).notifyRewardAmount(emissions)` 호출은 0의 `rewardRate`를 초래하여 호출이 되돌려집니다.

```solidity
 function notifyRewardAmount(
        uint256 _amount
    ) external onlyVoterOrAuthorized updateReward(address(0)) {
        _claimFees();

        kitten.safeTransferFrom(msg.sender, address(this), _amount);

        if (block.timestamp >= finishAt) {
            rewardRate = (_amount * PRECISION) / DURATION; //! @audit : if emissions = 0, reardRate will be zero
        } else {
            uint256 remainingRewards = (finishAt - block.timestamp) *
                rewardRate;
            if (_amount * PRECISION <= remainingRewards)
                revert NotifyLessThanEqualToLeft();
            rewardRate = ((_amount * PRECISION) + remainingRewards) / DURATION;
        }
        if (rewardRate == 0) revert ZeroRewardRate();
        //...
    }
```

권장 사항:
`distributeAll()` 및 `distributeRange()` 함수 모두에서 `gaugeEmissions[_gauge].distributed == true`를 확인하여 이미 보상을 받은 게이지에 대해 `_distribute()` 호출을 건너뛰고, `IGauge(_gauge).notifyRewardAmount(emissions)`를 호출하기 전에 `emissions > 0`인지 확인하도록 `_distribute()` 함수를 업데이트하십시오.

```diff
function _distribute(uint256 _period, address _gauge) internal {
        //...
+       if (emissiosn > 0){
        kitten.approve(_gauge, emissions);
        IGauge(_gauge).notifyRewardAmount(emissions);
+       }
        //...
    }
```

# [L-13] 게이지 생성 중 이전 커뮤니티 저장소의 수수료 손실

_인정됨_

`createAlgebraGauge()`가 새 커뮤니티 저장소를 생성하고 풀을 사용하도록 전환하면 이전 저장소에 누적된 수수료는 영구적으로 갇혀 액세스할 수 없게 됩니다.

```solidity
// replace old with new vault
algebraPool.setCommunityVault(gs.vault);
```

이 취약점의 영향은 `AlgebraPool`의 이전 커뮤니티 저장소가 새 저장소로 대체되지만 이전 저장소에 누적된 수수료는 처리되거나 복구되지 않는다는 것입니다.

저장소를 전환하기 전에 수수료 복구를 추가하는 것이 좋습니다.

```solidity
// Check and migrate any existing fees
if (oldVault.balanceOf(token0) > 0 || oldVault.balanceOf(token1) > 0) {
    // Withdraw existing fees from old vault
    oldVault.withdraw(token0, oldVault.balanceOf(token0));
    oldVault.withdraw(token1, oldVault.balanceOf(token1));
    // Transfer to new vault or distribute immediately
}
```

# [L-14] `claimVotingRewardBatch()`가 조기 청구를 허용하여 보상을 놓침

_해결됨_

`Voter` 계약에서 `claimVotingRewardBatch()` 함수를 사용하면 투표자가 투표한 게이지에 대한 관련 `VotingReward` 계약에서 누적된 투표 보상을 청구할 수 있습니다.

```solidity
 function claimVotingRewardBatch(
        address[] memory _votingRewardList,
        uint256 _tokenId
    ) external {
        if (veKitten.isApprovedOrOwner(msg.sender, _tokenId) == false)
            revert NotApprovedOrOwner();

        for (uint256 i; i < _votingRewardList.length; i++)
            IVotingReward(_votingRewardList[i]).getRewardForOwner(_tokenId);
    }
```

그러나 이 함수는 다음에도 불구하고 사용자가 **현재 기간을 포함**하여 보상을 청구할 수 있도록 합니다.

- `notifyRewardAmount()` 호출을 통해 현재 기간에 대한 보상이 **여전히 누적될 수 있습니다**.
- 사용자가 현재 기간에 대한 보상을 청구하면 `VotingReward` 계약은 해당 기간을 해당 사용자에 대해 **청구됨으로 표시**합니다.

```solidity
 function getRewardForOwner(uint256 _tokenId) external virtual nonReentrant {
        if (msg.sender != address(voter)) revert NotVoter();

        _getRewardForTokenId(_tokenId, veKitten.ownerOf(_tokenId));
    }
```

```solidity
 function _getRewardForTokenId(
        uint256 _tokenId,
        address _to
    ) internal virtual {
        uint256 len = rewardTokenList.length();
        address[] memory tokenList = rewardTokenList.values();
        uint256 currentPeriod = getCurrentPeriod();
        for (uint256 i; i < len; ) {
            uint256 period = fullClaimedPeriod[_tokenId] > periodInit
                ? fullClaimedPeriod[_tokenId]
                : periodInit;
            for (; period <= currentPeriod; ) { //! @audit : allows claiming for the current period, where rewards are not finalized
                _getReward(period, _tokenId, tokenList[i], _to);
                unchecked {
                    ++period;
                }
            }
            unchecked {
                ++i;
            }
        }
        fullClaimedPeriod[_tokenId] = currentPeriod; //! @audit : this marks the period as claimed, so the user can't claim again for that period
    }
```

이것은 다음을 의미합니다.

- **사용자가 청구한 후** 게이지의 `VotingReward` 계약으로 전송된 추가 보상은 합법적인 투표 가중치가 있더라도 사용자가 **청구할 수 없습니다**.
- 이로 인해 기간 초기에 청구하는 사용자에게 **보상 누락**이 발생합니다.
- 또한 사용자가 공정한 분배의 목적을 무효화하고 청구할 최적의 순간을 예측하려고 시도하는 **전략적 청구를 장려**합니다.

**권장 사항**

`claimVotingRewardBatch()`가 `currentPeriod - 1`까지만 청구를 허용하도록 제한하여 사용자가 현재 기간 동안 아직 수집 중인 보상을 놓치지 않도록 하십시오.

# [L-15] 과거 분배에 대해 현재 투표자에게 수수료가 잘못 지급됨

_인정됨_

`Voter` 계약에서 `_claimAndDistributeAlgebraPoolFees()` 함수는 다음 용도로 사용됩니다.

1. `AlgebraCommunityVault.collectFees()`를 통해 Algebra 풀에서 커뮤니티 수수료를 수집합니다.
2. 이 수수료를 `votingReward.notifyRewardAmount()`를 통해 `VotingReward` 계약으로 전달하면 `Voter`에서 토큰을 가져와 **현재 기간**에 대한 해당 게이지의 투표자에게 분배합니다.

```solidity
   function _distribute(uint256 _period, address _gauge) internal {
        // check to see if trying to distribute for future period
        if (_period > getCurrentPeriod()) revert InvalidPeriod();
        //...
        if (es.distributed) revert EmissionsAlreadyDistributedForPeriod();
        //...
        if (gauge[_pool].isAlgebra) _claimAndDistributeAlgebraPoolFees(_pool);
        //...
    }
```

```solidity
 function _claimAndDistributeAlgebraPoolFees(address _pool) internal {
        //...
        IAlgebraCommunityVault vault = IAlgebraCommunityVault(
            algebraPool.communityVault()
        );

        //...
        vault.withdraw(address(token0), token0BalVault);
        uint256 token0Claimed = token0.balanceOf(address(this)) -
            token0BalBefore;
        token0.approve(address(votingReward), token0Claimed);
        votingReward.notifyRewardAmount(address(token0), token0Claimed);

        //...
    }
```

```solidity
// @note : VotingReward.notifyRewardAmount()
    function notifyRewardAmount(
        address _token,
        uint256 _amount
    ) public virtual nonReentrant onlyRole(NOTIFY_ROLE) {
        uint256 currentPeriod = getCurrentPeriod();
        _addReward(currentPeriod, _token, _amount);

        emit NotifyReward(currentPeriod, msg.sender, _token, _amount);
    }
```

이 로직은 `_distribute()`가 현재 기간에 호출될 때 올바르게 작동합니다. 그러나 **초기 기간에 대한 보상이 아직 분배되지 않은 경우** 미묘하지만 영향력 있는 문제가 있습니다.

`distribute(uint256 _period, address _gauge)`는 현재 기간까지의 과거 기간에 대한 보상의 소급 분배를 허용하므로 **이전 기간**의 보상을 분배하라는 호출은 여전히 다음을 수행합니다.

- 커뮤니티 저장소에서 **현재 시간**까지 누적된 **모든** 수수료를 가져옵니다.
- 대상 `_period`가 아닌 **현재 기간**의 투표자에게 해당 수수료를 분배합니다.

결과적으로 **이전 기간**(즉, `_period < current period`)에 투표한 투표자는 투표 기간 동안 발생한 커뮤니티 수수료의 **어떤 부분도 받지 못하고**, 대신 **현재 기간**의 투표자가 모든 커뮤니티 수수료를 받게 되어 보상의 잘못된 귀속 및 불공정한 분배가 발생합니다.

**권장 사항**

기간별로 커뮤니티 수수료 발생을 분리하거나 기간별로 수수료 잔액을 추적하는 메커니즘을 구현하여 해당 기간의 투표자에게만 정확한 금액의 커뮤니티 수수료가 귀속되고 분배되도록 하십시오.

# [L-16] 조기에 배포된 Algebra 수수료로 인한 투표 보상 손실

_인정됨_

`Voter` 계약에서 `_claimAndDistributeAlgebraPoolFees()` 함수는 Algebra 풀에서 누적된 커뮤니티 수수료를 수집하여 `VotingReward` 계약을 통해 투표자에게 분배하는 역할을 합니다. 이 함수는 특정 기간의 특정 게이지에 대한 보상 분배 프로세스를 처리하는 `_distribute()` 함수 내에서 호출됩니다.

그러나 미묘한 문제가 있습니다. `_distribute()`는 **현재 기간**(7일 기간) 동안 언제든지 호출될 수 있으며, **기간당 게이지당 한 번만** 호출될 수 있습니다. `_claimAndDistributeAlgebraPoolFees()`가 기간 초기에 트리거되면 **그 시점까지 수집된 커뮤니티 수수료**만 `AlgebraCommunityVault`에서 가져와서 `VotingReward`로 전달됩니다. **동일한 기간**에 나중에 누적된 **추가 수수료**는 해당 기간 동안 동일한 게이지에 대해 두 번째 `_distribute()` 호출이 허용되지 않으므로 **현재 기간 동안 분배되지 않은 상태로 남습니다**.

이로 인해 커뮤니티 수수료의 일부가 투표한 기간 동안 투표자에게 분배되지 않기 때문에 **투표자에 대한 보상 손실**이 발생합니다.

**권장 사항**

이전 기간에 누적된 모든 커뮤니티 수수료가 확정되어 게이지 투표자에게 분배되도록 `_distribute()` 호출을 **다음 기간 시작**까지 지연시키십시오.

# [L-17] `split()` 함수는 `supply`에서 잠금 금액을 이중 계산함

_해결됨_

`VotingEscrow` 계약에서 `split()` 함수를 사용하면 NFT 잠금 소유자가 사용자 지정 가중치 분포에 따라 잠긴 금액을 여러 NFT로 분할할 수 있습니다. 각 새로 생성된 NFT 잠금은 최대 잠금 기간(2년)을 받으며, 이 함수는 기존 잠금 금액의 재할당으로 작동합니다.

그러나 이 함수는 **`supply`를 잘못 부풀립니다**. 분할 과정에서 원래 잠금의 금액은 `supply`에서 차감되지 않지만, `deposit_for()` 호출을 통해 새 잠금의 잠긴 금액이 다시 **추가**됩니다. 결과적으로 원래 잠금 금액이 `supply`에서 **두 번 계산**됩니다.

```solidity
 function split(
        uint256 _from,
        uint256[] memory _weightList
    )
        external
        nonReentrant
        onlyAuthorized(_from)
        notVoted(_from)
        returns (uint256[] memory tokenIdList)
    {
        //...
        for (uint256 i; i < _weightList.length; i++) {
            //...
            _deposit_for(
                tokenId,
                newAmount,
                maxUnlockTime,
                locked[tokenId],
                DepositType.MERGE_TYPE
            );
        }
        //...
    }
```

```solidity
function _deposit_for(
        uint256 _tokenId,
        uint256 _value,
        uint256 unlock_time,
        LockedBalance memory locked_balance,
        DepositType deposit_type
    ) internal {
        LockedBalance memory _locked = locked_balance;
        uint256 supply_before = supply;

        supply = supply_before + _value;
       //...
    }
```

이로 인해 `supply` 값이 잘못되어 이벤트에서 방출되고 온체인에서 관찰 가능하게 되며, 이는 다음과 같은 결과를 초래할 수 있습니다.

- 계산을 위해 `supply`에 의존하는 외부 통합자를 오도합니다.
- 이 계약과 상호 작용하는 다른 계약 또는 오프체인 시스템에서 잘못된 동작을 유발합니다.
- 총 잠긴 토큰에 대한 잘못된 부풀려진 지표를 초래합니다.

**권장 사항**

`split()`의 `deposit_for()`를 통해 잠긴 금액을 재분배하기 전에 `supply`에서 원래 잠금 금액을 뺍니다.

# [L-18] `_distribute()`로 보상을 조기에 배포할 수 있음

_해결됨_

`Voter` 계약에서 `notifyRewardAmount()` 함수는 **누구나 `KITTEN` 토큰을 기부**하여 현재 기간의 총 투표 점유율에 따라 등록된 게이지에 분배할 수 있도록 합니다. 이 함수는 `Minter.updatePeriod()`에 의해서도 호출되어 `Voter.start()` 후 다음 에포크부터 방출량을 발행하고 할당합니다.

```solidity
  function notifyRewardAmount(uint256 _amount) public {
        uint256 currentPeriod = getCurrentPeriod();

        kitten.safeTransferFrom(msg.sender, address(this), _amount);
        period[currentPeriod].totalEmissions += _amount;
    }
```

그러나 **보상이 통보된 동일한 기간에 보상 분배가 허용되므로**, 같은 기간에 `notifyRewardAmount()`가 두 번 이상 호출되면 문제가 발생합니다.

- 첫 번째 호출은 `distributeAll()`이 방출량을 분배하도록 허용하며, 그 후 처리된 모든 게이지에 대해 `gaugeEmissions[_gauge].distributed`가 `true`로 설정됩니다.
- 동일한 기간 내에 `notifyRewardAmount()`에 대한 두 번째 호출이 이루어지면 새로 기부된 보상은 `distributed` 플래그가 이미 설정된 게이지에 대해 `_distribute()`가 되돌리기 때문에 계약에 갇힌 상태로 유지됩니다.

**권장 사항**

현재 기간에 대한 보상 분배를 거부하고, 보상이 통보된 후 다음 기간부터만 보상을 분배할 수 있도록 강제하기 위해 `_distribute()` 함수를 업데이트하십시오.

```diff
function _distribute(uint256 _period, address _gauge) internal {
-       // check to see if trying to distribute for future period
-       if (_period > getCurrentPeriod()) revert InvalidPeriod();

+       // check to see if trying to distribute for future or current period
+       if (_period >= getCurrentPeriod()) revert InvalidPeriod();

        IVoter.Period storage ps = period[_period];
        IVoter.Emissions storage es = ps.gaugeEmissions[_gauge];
        if (es.distributed) revert EmissionsAlreadyDistributedForPeriod();

        uint256 emissions = (ps.totalEmissions * ps.gaugeTotalVotes[_gauge]) /
            ps.globalTotalVotes;
       //...
    }
```

# [L-19] 첫 번째 투표 에포크 기간 전에 추가된 방출량은 할당되지 않은 상태로 유지됨

_인정됨_

`Voter.notifyRewardAmount()` 함수는 누구나 `KITTEN` 토큰을 기부하여 현재 기간의 투표 점유율에 비례하여 게이지에 분배할 수 있도록 합니다. 이 동일한 함수는 `Minter.updatePeriod()`에서도 `epoch0Period + 1` 기간부터 `Voter` 계약으로 방출량을 발행하는 데 사용됩니다.

그러나 방출량이 갇힐 수 있는 시나리오가 있습니다.
`Voter.start()`가 아직 호출되지 않은 epoch0 기간(`epoch0Period == 0`) 내에 현재 기간이 있는 동안 `notifyRewardAmount()`가 호출되면, 기부된 방출량은 분배되지 않습니다. `vote()`는 다음 기간(epoch0 기간 + 1)에만 투표하도록 호출될 수 있기 때문입니다. 그런 다음 `distributeAll()`이 수신된 방출량을 게이지에 분배하기 위해 호출되면, **epoch0 기간에 게이지에 대해 기록된 투표가 없으므로** 되돌려집니다. 따라서 이러한 방출량은 `Voter` 계약에 갇혀 어떤 게이지에도 할당되지 않습니다.

**권장 사항**

소유자가 아직 `Voter.start()` 함수를 호출하지 않은 경우 `notifyRewardAmount()` 호출을 방지하거나, 분배되지 않은 보상을 추적하여 첫 번째 유효 기간으로 이월하십시오.

# [L-20] `split`은 자식 `NFT`에 최대 잠금 시간을 할당하여 잠재적으로 자금을 잠글 수 있음

_해결됨_

NFT를 분할할 때 코드는 부모 NFT의 잠금 해제 시간이 더 빠르더라도 각 자식 NFT의 잠금 해제 시간을 최대 잠금 시간으로 설정합니다.

```solidity
        uint256 maxUnlockTime = ((block.timestamp + MAXTIME) / WEEK) * WEEK;
        ...
            _mint(ownerOfFrom, tokenId);
            _deposit_for(
                tokenId,
                newAmount,
                maxUnlockTime,
```

이는 원래 잠금 기간과 관계없이 의도치 않게 사용자를 가능한 가장 긴 잠금 기간으로 강제할 수 있습니다. 사용자가 의지대로(`increase_unlock_time`을 통해) 수동으로 잠금 기간을 연장할 수 있는 시스템에서 `split` 중에 최대 잠금 해제 시간을 자동으로 할당하는 것은 불필요하며 사용자의 실제 의도에 반할 수 있습니다.

예를 들어:
- 사용자가 곧 잠금 해제할 의도로 **3개월** 동안 잠급니다.
- 그들은 게이지에 걸쳐 투표를 다양화하기 위해 NFT를 분할하기로 결정합니다.
- 결과 자식 NFT는 이제 **2년**의 최대 잠금 시간을 갖습니다.
- 사용자는 원래 NFT가 훨씬 더 빨리 잠금 해제되었을지라도 잠긴 토큰을 인출하기 위해 2년을 꼬박 기다려야 합니다.

**권장 사항**

분할 중에 자식 NFT가 부모 NFT의 잠금 해제 시간을 상속하도록 하는 것을 고려하십시오. 또한 부모 NFT의 잠금이 아직 만료되지 않았는지 확인하기 위해 잠금 해제 시간을 검증해야 합니다.
