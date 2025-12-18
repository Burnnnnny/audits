# 소개 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **0xHoneyJar/interpol** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Interpol 정보 (About Interpol)

Berachain의 InterPol은 사용자가 보상을 계속 얻으면서 유동성을 잠글 수 있도록 합니다. HoneyLocker 계약을 통해 사용자는 지정된 기간 동안 LP 토큰을 예치 및 잠그고 보안 어댑터를 통해 금고에 스테이킹할 수 있습니다. HoneyQueen은 승인된 어댑터를 관리하고 보상 토큰을 추적하며 수수료를 계산합니다. 이 구조는 유동성 공급자가 스테이킹 보상에 참여하는 동안 자산에 대한 완전한 통제권을 유지하도록 돕습니다.

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

_검토 커밋 해시_ - [1a7dd55a13f4a1c5d604f8f5ad2b7a63202243a6](https://github.com/0xHoneyJar/interpol/tree/1a7dd55a13f4a1c5d604f8f5ad2b7a63202243a6)

_수정 검토 커밋 해시_ - [c1d91a0be07aff5e997bc9fea368b952bfafc109](https://github.com/0xHoneyJar/interpol/tree/c1d91a0be07aff5e997bc9fea368b952bfafc109)

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `LockerFactory`
- `HoneyQueen`
- `HoneyLocker`
- `Beekeeper`
- `AdapterFactory`
- `TokenReceiver`
- `BGTStationAdapter`
- `BaseVaultAdapter`
- `BeradromeAdapter`
- `InfraredAdapter`
- `KodiakAdapter`

# 발견 사항 (Findings)

# [H-01] 보상 토큰을 수수료 없이 인출할 수 있음 (Reward tokens can be withdrawn without fee)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

InterPol은 보상 토큰을 인출할 때 프로토콜에 잠긴 토큰의 스테이킹에서 발생한 보상의 일부를 가져갑니다. 보상은 `withdrawERC20` 함수로 인출할 수 있으며, 이 함수는 프로토콜에 수수료도 보냅니다. 그러나 사용자는 `withdrawLPToken` 함수를 사용하여 수수료를 우회할 수 있습니다. 이는 아래 스니펫에서 볼 수 있듯이 인출된 토큰이 보상 토큰인지 확인하는 줄이 주석 처리되어 있기 때문에 발생합니다:

```Solidity
    function withdrawLPToken(address _LPToken, uint256 _amountOrId) external onlyUnblockedTokens(_LPToken) onlyOwnerOrOperator {
        // @audit-issue These lines should not be commented
        // if (HONEY_QUEEN.isRewardToken(_LPToken)) revert HasToBeLPToken();
        //if (expirations[_LPToken] == 0) revert HoneyLocker__HasToBeLPToken();
        if (block.timestamp < expirations[_LPToken])
            revert HoneyLocker__NotExpiredYet();

        // self approval only needed for ERC20, try/catch in case it's an ERC721
        try ERC721(_LPToken).approve(address(this), _amountOrId) {} catch {}
        ERC721(_LPToken).transferFrom(address(this), recipient(), _amountOrId);
        emit HoneyLocker__Withdrawn(_LPToken, _amountOrId);
    }
```

## 권장 사항

인출되는 토큰이 보상 토큰인지 확인하는 두 줄의 주석 처리를 제거하는 것을 고려하십시오.

현재 잠금 해제된 로커에 예치하는 경우 만료가 0으로 설정됩니다. 즉, 두 번째 줄(`//if (expirations[_LPToken] == 0) revert HoneyLocker__HasToBeLPToken();`)의 주석 처리를 제거하면 수수료 없이 잠금 해제된 로커에 예치된 토큰을 인출하는 것이 불가능합니다.

그러나 토큰이 보상 토큰으로 등록되지 않은 경우 사용자가 토큰 1 wei를 잠가 수수료 지불을 피할 수 있으므로 이 줄은 불필요해 보입니다.

# [M-01] 보상이 Kodiak 어댑터에 일시적으로 갇힐 수 있음 (Rewards might be temporarily stuck in Kodiak adapter)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

Kodiak에서 토큰을 언스테이킹할 때 아래 코드 스니펫에서 관찰할 수 있듯이 보상이 청구됩니다:

```Solidity
    function withdrawLocked(bytes32 kek_id) nonReentrant withdrawalsNotPaused public {
        _withdrawLocked(msg.sender, msg.sender, kek_id, true);
    }

    function _withdrawLocked(address staker_address, address destination_address, bytes32 kek_id, bool collectRewards) internal  {
        // Collect rewards first and then update the balances
        if(collectRewards) {
            _getReward(staker_address, destination_address);
        }

        //... snippet ...

    }
```

즉, 언스테이킹 시 보상이 어댑터 계약으로 전송됩니다. 언스테이크 함수는 이를 전송하지 않으므로 특별한 경우를 제외하고 어댑터가 자금을 보유해서는 안 된다는 프로토콜 불변성이 깨집니다. 보상이 청구되어 어댑터에서 전송될 때까지 잠시 동안 불변성이 깨질 수 있습니다. 그러나 때로는 보상 수집이 일시 중지되어 `getReward` 함수 호출이 되돌려질 수 있습니다:

```Solidity
    function getReward() external nonReentrant returns (uint256[] memory) {
        require(rewardsCollectionPaused == false,"Rewards collection paused");
        return _getReward(msg.sender, msg.sender);
    }
```

즉, Kodiak 어댑터에서 `claim`을 호출하면 되돌려지므로 Kodiak에서 보상 수집이 다시 시작될 때까지 보상이 어댑터에 갇히게 됩니다. 보상 수집이 일시 중지된 경우 Kodiak에서 청구가 작동하지 않는 것은 이해할 수 있지만, 이로 인해 어댑터 계약에 이미 존재하는 보상이 로커 계약에서 상환되는 것이 방지됩니다.

사용자의 보상이 어댑터 계약에 일시적으로 잠기고 프로토콜 불변성이 깨지기 때문에 영향은 중간으로 간주됩니다. 보상 수집 일시 중지는 다양한 이유로 언제든지 활성화될 수 있는 Kodiak의 합법적인 기능이므로 가능성은 중간입니다.

## 권장 사항

`getReward` 함수를 try-catch 블록으로 래핑하는 것을 고려하십시오. 이렇게 하면 호출이 항상 계약에서 보상을 보낼 수 있습니다.

```diff
    function claim(address vault) external override onlyLocker isVaultValid(vault) returns (address[] memory, uint256[] memory) {
        IKodiakFarm kodiakFarm = IKodiakFarm(vault);

        address[] memory rewardTokens = kodiakFarm.getAllRewardTokens();
        uint256[] memory amounts = new uint256[](rewardTokens.length);
-       kodiakFarm.getReward();
+       try kodiakFarm.getReward() {} catch {}
        for (uint256 i; i < rewardTokens.length; i++) {
            amounts[i] = ERC20(rewardTokens[i]).balanceOf(address(this));
            /*
                we skip the transfer, to not block any other rewards
                it can always be retrieved later because we use the balanceOf() function
            */
            try ERC20(rewardTokens[i]).transfer(locker, amounts[i]) {} catch {
                emit Adapter__FailedTransfer(locker, rewardTokens[i], amounts[i]);
                amounts[i] = 0;
            }
        }
        return (rewardTokens, amounts);
    }
```

고려해야 할 또 다른 사항은 언스테이킹 시 로커에게 보상을 보내 어댑터가 자산을 보유하지 않아야 한다는 불변성이 항상 유지되도록 하는 것입니다.

# [M-02] 공격자가 Kodiak에서 언스테이킹하는 로커를 DoS할 수 있음 (Attacker can DoS lockers from unstaking from Kodiak)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`HoneyLocker` 계약에 스테이킹할 때 `totalLPStaked` 및 `vaultLPStaked` 필드는 스테이킹되는 토큰 양만큼 증가합니다. 언스테이킹할 때 이 필드는 언스테이킹되는 토큰 양만큼 감소합니다. 그러나 공격자는 어댑터에 토큰 1 wei를 기부하여 Kodiak 어댑터에서 언스테이킹할 때 산술 언더플로우를 유발함으로써 이 메커니즘을 악용할 수 있습니다. 이로 인해 언스테이크 함수가 항상 실패하고 스테이킹된 토큰을 인출할 수 없게 됩니다. 다음 단위 테스트는 문제를 증명합니다:

```Solidity
    function test_unstakeDoS(
        bool _useOperator
    ) external prankAsTHJ(_useOperator) {
        // too low amount results in the withdrawal failing because of how Kodiak works
        uint256 amountToDeposit = 1000e18;

        StdCheats.deal(address(LP_TOKEN), address(locker), amountToDeposit);

        bytes32 expectedKekId = keccak256(
            abi.encodePacked(
                address(lockerAdapter),
                block.timestamp,
                amountToDeposit,
                GAUGE.lockedLiquidityOf(address(lockerAdapter))
            )
        );

        locker.stake(address(GAUGE), amountToDeposit);

        vm.warp(block.timestamp + 30 days);
        GAUGE.sync();

        StdCheats.deal(address(LP_TOKEN), address(lockerAdapter), 1);

        vm.expectRevert();
        locker.unstake(address(GAUGE), uint256(expectedKekId));
    }
```

테스트 실행 후 어댑터 계약에 `LP_TOKEN` 1 wei를 기부한 후 `unstake` 호출이 되돌려졌음을 의미하는 통과가 됩니다.

라이브 계약에서 악용되는 경우 어댑터나 로커를 새 버전으로 업그레이드하여 문제를 해결하여 자금에 액세스할 수 있도록 할 수 있으므로 중간 영향 등급이 정당화됩니다. 그러나 이는 여전히 일시적인 동결을 의미합니다.

로커가 기부된 금액을 스테이킹하여 해결할 수도 있습니다. 이는 로커에게 자금 손실을 의미하지만 공격자는 이러한 기부에 자금을 지출하도록 인센티브를 받지 않습니다. 이러한 요인들의 조합으로 인해 가능성은 중간으로 간주되어야 합니다.

## 권장 사항

이 문제는 Kodiak의 고유한 스테이킹 메커니즘(다른 어댑터와 비교)으로 인해 발생합니다. 가장 좋은 방법은 Kodiak 어댑터 계약 내부에서 스테이크 금액을 내부적으로 추적하는 것입니다. 제안된 수정 사항은 다음과 같을 수 있습니다:

```diff
+   mapping(bytes32 kekId => uint256 amount) amounts;

    function stake(address vault, uint256 amount) external override onlyLocker isVaultValid(vault) returns (uint256) {
        IKodiakFarm kodiakFarm = IKodiakFarm(vault);
        address token = kodiakFarm.stakingToken();

        ERC20(token).transferFrom(locker, address(this), amount);
        ERC20(token).approve(address(kodiakFarm), amount);
+       bytes32 expectedKekId = keccak256(
+           abi.encodePacked(
+               address(this),
+               block.timestamp,
+               amount,
+               kodiakFarm.lockedLiquidityOf(address(this))
+           )
+       );
        kodiakFarm.stakeLocked(amount, kodiakFarm.lock_time_for_max_multiplier());
+       amounts[expectedKekId] = amount;
        return amount;
    }

    function unstake(address vault, uint256 kekIdAsUint) external override onlyLocker isVaultValid(vault) returns (uint256) {
        IKodiakFarm kodiakFarm = IKodiakFarm(vault);
        address token = kodiakFarm.stakingToken();

        kodiakFarm.withdrawLocked(bytes32(kekIdAsUint));
+       uint256 unstakeAmount = amounts[bytes32(kekIdAsUint)];
        uint256 amount = ERC20(token).balanceOf(address(this));
        ERC20(token).transfer(locker, amount);
+       delete amounts[bytes32(kekIdAsUint)];
-       return amount;
+       return unstakeAmount;
    }
```

그러나 어댑터의 업그레이드 가능한 특성으로 인해 제안된 수정 사항은 `BaseVaultAdapter` 계약에 `gap` 필드를 추가하는 것과 결합되어야 합니다([여기서 자세히 보기](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#storage-gaps)).

# [M-03] 반환 값이 없는 ERC20 토큰은 보상 청구를 DoS함 (ERC20 tokens without return value will DoS reward claiming)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

애플리케이션 전체의 여러 함수가 `transfer` 함수를 사용하여 ERC20 토큰을 전송합니다. 그러나 일부 토큰은 전송 시 `bool`을 반환하지 않으며 ERC20 인터페이스는 `bool` 반환 값을 예상하므로 `bool`을 반환하지 않는 토큰에 대해 `transfer`를 호출하면 되돌려집니다. 다음 함수는 이 문제의 영향을 받습니다:

- `HoneyLocker`의 `withdrawERC20`
- `BeradromeAdapter`의 `unstake`
- `BGTStationAdapter`의 `unstake`
- `InfraredAdapter`의 `unstake`
- `KodiakAdapter`의 `unstake`

다음 함수들도 ERC20에서 `transfer`를 호출하지만 호출은 try-catch 블록으로 래핑됩니다. 그러나 이러한 호출은 여전히 되돌려집니다:

- `BeradromeAdapter`의 `claim`
- `InfraredAdapter`의 `claim`
- `KodiakAdapter`의 `claim`

이 문제는 계약 업그레이드를 통해 해결될 수 있지만 사용자는 수정 사항이 배포될 때까지 자산이 일시적으로 동결될 수 있으므로 영향은 중간으로 평가됩니다. 생태계의 수많은 널리 사용되는 토큰이 반환 값과 관련하여 ERC20 표준을 엄격하게 따르지 않는다는 점을 고려할 때 가능성은 높습니다.

## 권장 사항

ERC20 토큰 전송에 `SafeTransfer` 라이브러리를 사용하는 것을 고려하십시오. 이로 인해 일부 전송이 여전히 전체 트랜잭션을 되돌릴 수 있으므로 토큰을 별도로 빼내는 함수를 추가하는 것을 고려하십시오.

대안으로, 어댑터에서 안전한 전송 기능을 직접 구현하고 실패 시 되돌리지 않도록 하여 나중에 전송을 다시 시도할 수 있도록 하는 것을 고려하십시오.

# [L-01] 로커 소유자는 모든 수수료를 추천인에게 리디렉션할 수 있음 (The locker owner can redirect all fees to the referrer)

`Beekeeper` 계약은 보상 토큰의 수수료를 프로토콜과 추천인(있는 경우)에게 분배합니다. 그러나 추천인의 수수료 공유는 위로 반올림하는 `mulDivUp` 함수로 계산됩니다. 스니펫을 참조하십시오:

```Solidity
    function distributeFees(address _referrer, address _token, uint256 _amount) external payable {
        // snippet //

        uint256 referrerFee = FPML.mulDivUp(_amount, referrerFeeShareInBps, 10000);

        // snippet //
    }
```

즉, 사용자는 총 수수료가 1 wei가 되도록 의도적으로 보상을 인출할 수 있으며, 위로 반올림으로 인해 추천인의 수수료는 1 wei가 되어 프로토콜은 삭감을 받지 못합니다.

수수료를 과다 지불할 수 있으므로 사용자가 이런 식으로 행동할 유인은 없지만 1 wei 당 높은 가치로 인해 WBTC 보상과 같이 이것이 의미가 있을 수 있는 경우가 있을 수 있지만, 악의적인 사용자가 추천인이 아닌 한 아무것도 얻지 못합니다. 추천인인 경우 수수료를 절약할 수 있습니다. 이 시나리오는 가능성이 매우 낮습니다.

프로토콜에 유리하게 반올림하는 것이 좋지만 이는 추천인이 수수료 공유를 받지 못하는 반대 영향을 미칠 수 있습니다. 따라서 가장 좋은 권장 사항은 청구된 최소 보상 금액에 대한 확인을 추가하는 것입니다.

# [L-02] 재정의 추천인이 존재할 때 수수료 공유 업데이트 (Updating fee share when an overriding referrer exists)

BeeKeeper 함수에서 추천인 수수료가 할당되는 방법은 다음과 같습니다 ->

```solidity
function setReferrerFeeShare(address _referrer, uint256 _shareOfFeeInBps) external onlyOwner {
        if (!isReferrer[_referrer]) revert Beekeeper__NotAReferrer();
        _referrerFeeShare[_referrer] = _shareOfFeeInBps;
    }
```

그러나 위 추천인에게 재정의 추천인이 있을 가능성이 있으며, 이 경우 재정의 추천인에 대한 수수료 공유도 업데이트되어야 합니다.

`referrerOverrides[_referrer]`가 0이 아니면 재정의 추천인 수수료 공유를 업데이트하십시오.

# [L-03] 로커에 `onERC721Received` 후크가 없음 (Locker does not have `onERC721Received` hook)

HoneyLocker.sol 계약은 NFT(ERC1155 포함) 토큰을 수신하도록 의도되었지만, 일반적으로 외부 통합이 로커와 통합되고 `safeTransferFrom` / `safeTransfer`를 활용하여 NFT 토큰을 전송하는 경우 로커에 onERC721Received(및 ERC1155 후크) 후크가 없으므로 전송이 항상 되돌려집니다.

이를 해결하기 위해 이러한 후크를 포함하는 것이 좋습니다.

# [L-04] 잠금 해제되지 않은 토큰에 대해 `expirations`가 0으로 설정됨 (`expirations` is set to 0 for non-unlocked tokens)

잠금 해제된 HoneyLocker의 경우 예치된 LP 토큰의 `expirations`가 0으로 설정됩니다:

```solidity
    function depositAndLock(address _LPToken, uint256 _amountOrId, uint256 _expiration) external onlyOwnerOrOperator {

        if (!unlocked && expirations[_LPToken] != 0 && _expiration < expirations[_LPToken]) {
            revert HoneyLocker__ExpirationNotMatching();
        }

        expirations[_LPToken] = unlocked ? 0 : _expiration; <=@
```

이는 사용자가 `withdrawERC20()` 함수를 사용하여 LP를 인출하고 안 내야 할 때 LP 토큰에 대한 수수료를 지불할 수 있게 하므로 사용자에게 잠재적인 위험이 될 수 있습니다.

잠금 해제된 HoneyLocker의 경우 `expirations`를 1로 설정하도록 업데이트하는 것을 고려하십시오.

# [L-05] Kodiak 금고는 언스테이킹/청구 호출을 되돌릴 수 있음 (Kodiak vault can revert unstaking/claiming calls)

일부 엣지 케이스에서 `Kodiak` 금고는 더스트 보상이 발생하거나 소량 반올림으로 인해 더스트 LP가 스테이킹된 경우 언스테이킹/청구 호출을 되돌릴 수 있습니다. 예를 들어 33 wei만 언스테이킹하는 경우 되돌려집니다:

```solidity
    function test_dustStake() external prankAsTHJ(true) {
        uint256 amountToDeposit = 33;

        StdCheats.deal(address(LP_TOKEN), address(locker), amountToDeposit);

        //====== Stake

        bytes32 expectedKekId1 = keccak256(
            abi.encodePacked(
                address(lockerAdapter), block.timestamp, amountToDeposit, GAUGE.lockedLiquidityOf(address(lockerAdapter))
            )
        );

        locker.stake(address(GAUGE), amountToDeposit);

        vm.warp(block.timestamp + 30 days);

        //====== Unstake

        locker.unstake(address(GAUGE), uint256(expectedKekId1));
    }
```

이 문제에 대한 수정 사항은 Interpol 자체 내에서 사용할 수 없지만, 보상률이 업데이트될 때 미래에 잠재적인 문제로 이어질 수 있고 더스트가 아닌 양에서 되돌림이 발생할 수 있으므로 기본 프로토콜의 이러한 동작을 고려해야 합니다.

# [L-06] `_hasSetOperator`가 true로 설정되지 않음 (`_hasSetOperator` is not set to true)

`BGTStationAdapter` 계약은 `_hasSetOperator` 매핑을 사용하여 `locker`가 이미 운영자를 설정했는지 추적합니다. 그 값은 `BGTStationAdapter#stake()` 함수에서 true로 설정됩니다. 그러나 `BGTStationAdapter#wildcard()` 함수에서는 true로 설정되지 않습니다. `locker`가 `BGTStationAdapter`에 대한 운영자를 여러 번 설정하는 것을 방지하기 위해 이를 업데이트하는 것을 고려하십시오.

# [L-07] `Initializable` 상속 및 초기화자 비활성화 (Inheriting `Initializable` and disabling initializers)

모든 어댑터 계약과 `HoneyLocker` 계약은 프록시이므로 `Initializable` 계약을 상속해야 합니다. `initialize()` 함수에는 `initializer` 수정자가 있어야 합니다. 또한 이러한 계약의 `constructor` 내부에서 `_disableInitializers()`를 호출해야 합니다. 이렇게 하면 악의적인 사용자가 구현 계약에서 `initialize()` 함수를 호출하는 것을 방지할 수 있습니다.

# [L-08] 차단된 토큰을 예치할 수 있지만 인출할 수는 없음 (Blocked tokens can be deposited but cannot be withdrawn)

`HoneyLocker.depositAndLock` 함수는 차단된 토큰 예치를 방지하지 않습니다. 따라서 사용자는 이러한 토큰을 예치할 수 있지만 인출할 수는 없습니다. 토큰은 계약에서 차단됩니다.

```solidity
>>  function depositAndLock(address _LPToken, uint256 _amountOrId, uint256 _expiration) external onlyOwnerOrOperator {
<...>
    function withdrawLPToken(address _LPToken, uint256 _amountOrId) external
    onlyUnblockedTokens(_LPToken)
    onlyOwnerOrOperator
```

`depositAndLock` 함수에 `onlyUnblockedTokens` 수정자를 추가하는 것을 고려하십시오.

# [L-09] `expirations`는 항상 `block.timestamp`보다 작을 수 있음 (The `expirations` can always be less than `block.timestamp`)

`HoneyLocker.depositAndLock` 함수는 `_expiration`이 현재 타임스탬프보다 작은지 확인하지 않습니다. 이 방식대로 사용자는 즉시 `withdrawLPToken` 실행을 허용하는 `expirations`를 항상 설정할 수 있습니다.

```solidity
    function depositAndLock(address _LPToken, uint256 _amountOrId, uint256 _expiration) external onlyOwnerOrOperator {

>>      if (!unlocked && expirations[_LPToken] != 0 && _expiration < expirations[_LPToken]) {
            revert HoneyLocker__ExpirationNotMatching();
        }

        expirations[_LPToken] = unlocked ? 0 : _expiration;

        // using transferFrom from ERC721 because same signature for ERC20
        // with the difference that ERC721 doesn't expect a return value
        ERC721(_LPToken).transferFrom(msg.sender, address(this), _amountOrId);

        emit HoneyLocker__Deposited(_LPToken, _amountOrId);
        emit HoneyLocker__LockedUntil(_LPToken, _expiration);
    }
```

`_expiration` 매개변수가 `block.timestamp` 값을 초과하는지 확인하는 것을 고려하십시오.

# [L-10] 일부 어댑터는 ERC721 토큰을 스테이킹하지 못할 수 있음 (Some adapters might be unable to stake ERC721 tokens)

프로토콜은 LP 토큰이 ERC721 토큰일 것으로 예상합니다. 프로토콜의 핵심 기능은 잠긴 토큰의 스테이킹이므로 ERC721 토큰에도 적용되어야 합니다. 그러나 일부 어댑터는 일치하지 않는 `approve` 함수 반환 값으로 인해 ERC721 토큰을 스테이킹하지 못할 수 있습니다.

`ERC721::approve`의 서명은 다음과 같습니다:

```Solidity
function approve(address to, uint256 tokenId) external;
```

반면 `ERC20::approve`의 서명은 다음과 같습니다:

```Solidity
function approve(address spender, uint256 value) external returns (bool);
```

즉, ERC721 토큰에서 ERC20 인터페이스를 통해 `approve`를 호출하면 되돌려집니다. ERC20 approve는 반환 값을 예상하기 때문입니다. 이로 인해 `BeradromeAdapter` 및 `KodiakAdapter`의 `stake` 기능을 ERC721 토큰에 사용할 수 없게 됩니다.

승인을 위해 `SafeTransferLib`의 `safeApprove` 함수를 사용하는 것을 고려하십시오. 이 접근 방식은 ERC721 및 ERC20 토큰 모두에서 작동합니다.

# [L-11] Kodiak 스테이킹 시간을 조정할 수 없음 (Kodiak staking time is not adjustable)

Kodiak에 토큰을 스테이킹할 때 스테이커는 토큰을 스테이킹할 기간을 선택하고 스테이킹 배율은 이 기간을 기준으로 합니다. Kodiak 어댑터에서 `stake` 함수를 호출할 때 스테이킹 시간은 가능한 최대 시간인 `lock_time_for_max_multiplier()`로 설정됩니다.

이 값은 현재 `2592000`초(30일)이므로 문제가 될 수 있습니다. LP 토큰은 일반적으로 30일보다 오래 잠겨 있지만, 특히 토큰이 `unlocked` 로커에 잠겨 있는 경우 이 가정을 해서는 안 됩니다.

이 값이 나중에 변경되는 경우(예: 1년) 또 다른 문제가 발생할 수 있습니다. 사용자가 한 달 동안 토큰을 잠그려면 스테이크 시간이 잠금 시간보다 길기 때문에 스테이킹할 수 없습니다.

예를 들어 와일드카드 함수를 활용하여 사용자에게 잠금 시간을 선택할 수 있는 옵션을 제공하는 것을 고려하십시오. 아래 코드 스니펫은 이것이 어떻게 작동할 수 있는지 보여줍니다:

```diff
+   uint256 lockTime;

    function stake(address vault, uint256 amount) external override onlyLocker isVaultValid(vault) returns (uint256) {
        IKodiakFarm kodiakFarm = IKodiakFarm(vault);
        address token = kodiakFarm.stakingToken();

        ERC20(token).transferFrom(locker, address(this), amount);
        ERC20(token).approve(address(kodiakFarm), amount);
-       kodiakFarm.stakeLocked(amount, kodiakFarm.lock_time_for_max_multiplier());
+       kodiakFarm.stakeLocked(amount, lockTime);
        return amount;
    }

    function wildcard(
        address vault,
        uint8 func,
        bytes calldata args
    ) external override onlyLocker isVaultValid(vault) {
        IKodiakFarm kodiakFarm = IKodiakFarm(vault);
        XKDK xKdk = XKDK(kodiakFarm.xKdk());
        if (func == 0) {
            (uint256 amount, uint256 duration) = abi.decode(
                args,
                (uint256, uint256)
            );
            xKdk.redeem(amount, duration);
        } else if (func == 1) {
            uint256 index = abi.decode(args, (uint256));
            xKdk.finalizeRedeem(index);
            ERC20(kodiakFarm.kdk()).transfer(locker, ERC20(kodiakFarm.kdk()).balanceOf(address(this)));
        } else if (func == 2) {
            lockTime = abi.decode(args, (uint256));
        }
    }
```
