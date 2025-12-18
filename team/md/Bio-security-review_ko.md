# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**MTXstudio/bio-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Bio 소개

FairAuctionVesting 스마트 계약은 사용자가 DAO 토큰을 BIO 및 VBIO 토큰으로 교환할 수 있는 토큰 판매를 용이하게 하며, 후자는 베스팅 일정을 따릅니다. 여기에는 기부 처리, 토큰 청구, 베스팅 일정 및 비상 자금 인출을 위한 메커니즘이 포함됩니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [c28a41517d1f426d49e9b477e565432f1307ba58](https://github.com/MTXstudio/bio-contracts/tree/c28a41517d1f426d49e9b477e565432f1307ba58)

_수정 검토 커밋 해시_ - [70e2f84927752fd43446dee282df189993f55548](https://github.com/MTXstudio/bio-contracts/tree/70e2f84927752fd43446dee282df189993f55548)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `FairAuctionVesting`

# 발견 사항

# [M-01] `claim` 호출 시 사용자가 토큰 청구 권한을 잃을 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

비상 시 `DEFAULT_ADMIN_ROLE`은 계약에서 `BIO_TOKEN` 잔액을 인출할 수 있습니다. 동시에 사용자가 토큰을 `claim`하려고 시도하는 데 대한 제한은 없습니다.
`_safeClaimTransfer`는 실제 잔액을 전송할 수 있으므로 사용자는 0 `BIO_TOKEN`을 받고 다시 토큰을 청구할 권리를 잃게 됩니다. 또한 `VBIO_TOKEN` 계약도 0을 받지만 올바른 금액으로 베스팅 일정을 수락합니다. 따라서 모든 베스팅을 위한 `VBIO_TOKEN` 계약 잔액에 충분한 토큰이 없을 것입니다.

```solidity
    function claim() external nonReentrant isClaimable {
        UserInfo storage user = userInfo[msg.sender];

        if (totalRaised == 0 || user.contribution == 0) revert ZeroContribution();
>>      if (user.hasClaimed) revert AlreadyClaimed();
>>      user.hasClaimed = true;

        (uint256 bioAmount, uint256 vbioAmount) = getExpectedClaimAmount(msg.sender);

        emit Claim(msg.sender, bioAmount, vbioAmount);

>>      if (bioAmount > 0) {
            // send BIO_TOKEN contribution
>>          _safeClaimTransfer(BIO_TOKEN, msg.sender, bioAmount);
        }
>>      if (vbioAmount > 0) {
            // send VBIO_TOKEN contribution
>>          _safeClaimTransfer(BIO_TOKEN, address(VBIO_TOKEN), vbioAmount);
            // create vesting schedule
            VBIO_TOKEN.createVestingSchedule(
>>              msg.sender, vestingStart, vestingCliff, vestingDuration, vestingSlicePerSecond, vestingIsRevocable, vbioAmount
            );
        }
    }
<...>
    function _safeClaimTransfer(IERC20 token, address to, uint256 amount) internal {
>>      uint256 balance = token.balanceOf(address(this));
        bool transferSuccess = false;

        if (amount > balance) {
    >>      transferSuccess = token.transfer(to, balance);
        } else {
            transferSuccess = token.transfer(to, amount);
        }

        if (!transferSuccess) revert TransferFailed();
    }
```

## 권장 사항

라운딩 오류 보호를 위해 사용할 수 없지만 예상치 못한 동작과 자산 손실을 초래할 수 있으므로 `_safeClaimTransfer` 기능을 제외하는 것을 고려하십시오.

# [L-01] 판매되지 않은 Bio를 Treasury로 전송하는 것을 고려하십시오

스왑, 비상 인출 중 다른 모든 곳에서 Treasury는 토큰 수령인입니다. 그러나 어떤 이유로 Admin은 판매되지 않은 Bio를 청구합니다.

```solidity
    function withdrawUnsoldTokens() external onlyRole(DEFAULT_ADMIN_ROLE) {
        if (!hasEnded()) revert SaleNotEnded();
        if (unsoldTokensWithdrew) revert TokensAlreadyWithdrawn();

        uint256 totalBIOSold = bioToDistribute();
        uint256 totalVBIOSold = vbioToDistribute();

        unsoldTokensWithdrew = true;
        //because VBIO is BIO and is sent to VBIO contract before claiming
@>      BIO_TOKEN.transfer(msg.sender, MAX_BIO_TO_DISTRIBUTE + MAX_VBIO_TO_DISTRIBUTE - totalBIOSold - totalVBIOSold);
    }
```

# [L-02] `START_TIME` 및 `END_TIME`에 대한 합리적인 값

계약 생성자에서 `startTime_` 및 `endTime_` 매개변수의 입력 확인이 불충분합니다. 입력이 불변 값에 사용되므로 매개변수에 합리적인 값이 있는지 확인하는 것이 중요합니다. 적어도 `startTime_` 및 `endTime_`은 block.timestamp보다 작아서는 안 되며 그 사이의 간격이 너무 작거나 크지 않아야 합니다.

```solidity
    constructor(
        IERC20 bioToken_,
        TokenVesting vbioToken_,
        IERC20 daoToken_,
        uint256 startTime_,
        uint256 endTime_,
        address treasury_,
        uint256 maxToDistribute_,
        uint256 maxToDistribute2_,
        uint256 minToRaise_
    ) AccessControlDefaultAdminRules(0, msg.sender) {
>>      if (startTime_ >= endTime_) revert InvalidConstructorParameters();
        if (treasury_ == address(0)) revert InvalidConstructorParameters();
        if (address(bioToken_) == address(0) || address(vbioToken_) == address(0)) revert InvalidConstructorParameters();

        BIO_TOKEN = bioToken_;
        VBIO_TOKEN = vbioToken_;
        DAO_TOKEN = daoToken_;
>>      START_TIME = startTime_;
>>      END_TIME = endTime_;
        vestingStart = endTime_;
        treasury = treasury_;
        MAX_BIO_TO_DISTRIBUTE = maxToDistribute_;
        MAX_VBIO_TO_DISTRIBUTE = maxToDistribute2_;
        MIN_DAO_RAISED_FOR_MAX_BIO = minToRaise_;
    }
<...>
    function hasStarted() public view returns (bool) {
>>      return _currentBlockTimestamp() >= START_TIME;
    }
<...>
    function hasEnded() public view returns (bool) {
>>      return END_TIME <= _currentBlockTimestamp();
    }
```

생성자의 `start` 및 `endTime`은 과거로 설정될 수 있습니다.

```
        if (startTime_ >= endTime_) revert InvalidConstructorParameters();
```

`vestingStart` 시간도 과거로 설정될 수 있습니다.

```
 function setVestingStart(uint256 start_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        // _start should be no further away than 30 weeks
        if (start_ > block.timestamp + 30 weeks) revert InvalidScheduleParameter();
        vestingStart = start_;
    }
```

이 오류를 방지하려면 시작 시간이 `block.timestamp`보다 큰지 확인하십시오.

```
생성자의 경우:

+       if (startTime_ < block.timestamp) revert InvalidConstructorParameters();
        if (startTime_ >= endTime_) revert InvalidConstructorParameters();
```

```
setVestingStart의 경우:

 function setVestingStart(uint256 start_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        // _start should be no further away than 30 weeks
+       if (start_ < block.timestamp) revert InvalidScheduleParameter();
        if (start_ > block.timestamp + 30 weeks) revert InvalidScheduleParameter();
        vestingStart = start_;
    }
```

# [L-03] `DAO` 토큰 주소 확인 부족

`DAO` 토큰 주소는 배포 중에만 설정할 수 있습니다. `DAO` 주소 불변 값이 0인 경우 계약은 쓸모없게 됩니다.

```solidity
    constructor(
        IERC20 bioToken_,
        TokenVesting vbioToken_,
        IERC20 daoToken_,
        uint256 startTime_,
        uint256 endTime_,
        address treasury_,
        uint256 maxToDistribute_,
        uint256 maxToDistribute2_,
        uint256 minToRaise_
    ) AccessControlDefaultAdminRules(0, msg.sender) {
        if (startTime_ >= endTime_) revert InvalidConstructorParameters();
        if (treasury_ == address(0)) revert InvalidConstructorParameters();
>>      if (address(bioToken_) == address(0) || address(vbioToken_) == address(0)) revert InvalidConstructorParameters();

        BIO_TOKEN = bioToken_;
        VBIO_TOKEN = vbioToken_;
>>      DAO_TOKEN = daoToken_;
        START_TIME = startTime_;
        END_TIME = endTime_;
        vestingStart = endTime_;
        treasury = treasury_;
        MAX_BIO_TO_DISTRIBUTE = maxToDistribute_;
        MAX_VBIO_TO_DISTRIBUTE = maxToDistribute2_;
        MIN_DAO_RAISED_FOR_MAX_BIO = minToRaise_;
    }
```

# [L-04] 소유자가 모든 BIO 토큰을 인출할 수 있는 중앙화 위험

소유자는 비상 시 모든 bio 토큰을 인출할 수 있으며, 이는 사용자의 청구에 영향을 미칩니다.

```
 function emergencyWithdrawBIO() external onlyRole(DEFAULT_ADMIN_ROLE) {
        uint256 amount = BIO_TOKEN.balanceOf(address(this));
        BIO_TOKEN.safeTransfer(treasury, amount);

        emit EmergencyWithdraw(address(BIO_TOKEN), amount);
    }
```

또한 소유자는 `forceClaimable`을 true로 설정하지 않을 수 있으며, 그러면 사용자는 DAO 토큰을 treasury에 잃게 됩니다.

# [L-05] `swap` 및 `claim` 기능을 사용할 수 없음

`FairAuctionVesting`에 `VBIO_TOKEN.VESTING_CREATOR_ROLE` 역할이 없는 경우 `swap` 함수를 호출할 수 없습니다.

```solidity
    modifier isSaleActive() {
        if (!hasStarted() || hasEnded()) revert SaleInactive();
        if (BIO_TOKEN.balanceOf(address(this)) < (MAX_BIO_TO_DISTRIBUTE + MAX_VBIO_TO_DISTRIBUTE)) revert SaleNotFilled();
        // check if this contract has vesting creator role
>>      if (!VBIO_TOKEN.hasRole(VBIO_TOKEN.VESTING_CREATOR_ROLE(), address(this))) revert SaleNotFilled();
        _;
    }
<...>
>>  function swap(uint256 amount) external isSaleActive isNotPaused nonReentrant {
```

`claim` 함수는 동일한 조건에 대해 `TokenVesting.createVestingSchedule` 함수에서 확인하여 되돌려집니다.

```solidity
    function claim() external nonReentrant isClaimable {
        UserInfo storage user = userInfo[msg.sender];

        if (totalRaised == 0 || user.contribution == 0) revert ZeroContribution();
        if (user.hasClaimed) revert AlreadyClaimed();
        user.hasClaimed = true;

        (uint256 bioAmount, uint256 vbioAmount) = getExpectedClaimAmount(msg.sender);

        emit Claim(msg.sender, bioAmount, vbioAmount);

        if (bioAmount > 0) {
            // send BIO_TOKEN contribution
            _safeClaimTransfer(BIO_TOKEN, msg.sender, bioAmount);
        }
        if (vbioAmount > 0) {
            // send VBIO_TOKEN contribution
            _safeClaimTransfer(BIO_TOKEN, address(VBIO_TOKEN), vbioAmount);
            // create vesting schedule
>>          VBIO_TOKEN.createVestingSchedule(
                msg.sender, vestingStart, vestingCliff, vestingDuration, vestingSlicePerSecond, vestingIsRevocable, vbioAmount
            );
        }
    }
<...>
    function createVestingSchedule(
        address _beneficiary,
        uint256 _start,
        uint256 _cliff,
        uint256 _duration,
        uint256 _slicePeriodSeconds,
        bool _revokable,
        uint256 _amount
>>  ) external whenNotPaused onlyRole(VESTING_CREATOR_ROLE) {
        _createVestingSchedule(_beneficiary, _start, _cliff, _duration, _slicePeriodSeconds, _revokable, _amount);
    }
```

따라서 계약은 `VBIO_TOKEN` 소유자의 조치에 취약합니다.

`VBIO_TOKEN` 계약 관리자를 신뢰할 수 있지만 `FairAuctionVesting`에서 새 `VBIO_TOKEN` 계약을 배포하여 베스팅 토큰의 `DEFAULT_ADMIN_ROLE` 및 `VESTING_CREATOR_ROLE` 전체 제어 권한을 부여할 수도 있습니다.

# [L-06] 청구 시 예기치 않은 베스팅 일정

베스팅 일정은 경매 중 사용자에게 정말 중요할 수 있지만 계약 수명 기간 동안 변경되거나 전혀 설정되지 않을 수 있습니다.

```solidity
    function setVestingStart(uint256 start_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        // _start should be no further away than 30 weeks
        if (start_ > block.timestamp + 30 weeks) revert InvalidScheduleParameter();
        vestingStart = start_;
    }

    function setCliff(uint256 cliff_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        // _duration must be longer than _cliff
        if (vestingDuration < cliff_) revert InvalidScheduleParameter();
        vestingCliff = cliff_;
    }

    function setDuration(uint256 duration_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        // _duration should be at least 7 days and max 50 years
        if (duration_ < 7 days || duration_ > 50 * (365 days)) revert InvalidScheduleParameter();
        vestingDuration = duration_;
    }

    function setSlicePerSecond(uint256 slicePerSecond_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        // _slicePeriodSeconds should be between 1 and 60 seconds
        if (slicePerSecond_ == 0 || slicePerSecond_ > 60) revert InvalidScheduleParameter();
        vestingSlicePerSecond = slicePerSecond_;
    }

    function setIsRevocable(bool isRevocable_) external onlyRole(DEFAULT_ADMIN_ROLE) {
        vestingIsRevocable = isRevocable_;
    }
```

판매 시작 전에만 베스팅 일정을 설정하는 것을 고려하십시오.

