# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**falconfinance/falcon-contracts-evm** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Falcon 소개 (About Falcon)

Falcon은 USDf(합성 달러 ERC20 토큰)와 sUSDf(이자를 얻기 위해 USDf를 스테이킹하는 ERC4626 금고)를 중심으로 한 스테이블코인 시스템이며, FalconPosition이라는 독특한 ERC721 NFT를 통해 시간 제한이 있는 부스트 수익 포지션을 제공합니다. 이 플랫폼에는 간소화된 거래를 위한 FalconBundler, USDf 발행을 위한 PreCollateralizedMinter, 수익 분배를 위한 StakingRewardsDistributor, 스테이킹 쿨다운 동안 USDf를 보관하는 USDfSilo와 같은 보조 계약도 포함되어 있습니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 희박하거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [9c34a242ae6c39e2054d5e3bb62e44328339aaa1](https://github.com/falconfinance/falcon-contracts-evm/tree/9c34a242ae6c39e2054d5e3bb62e44328339aaa1)**

**_수정 검토 커밋 해시_ - [ee3655c8f747718a2b95d99d78500d71c4841664](https://github.com/falconfinance/falcon-contracts-evm/tree/ee3655c8f747718a2b95d99d78500d71c4841664)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `ClassicMinterV1`
- `FalconBundler`
- `FalconPosition`
- `PreCollateralizedMinter`
- `StakedUSDf`
- `StakingRewardsDistributor`
- `USDf`
- `USDfSilo`

# 발견 사항 (Findings)

# [M-01] 마지막 출금에 대한 서비스 거부(DoS) 공격 가능

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`StakedUSDf.sol`에서 모든 출금은 `totalSupply()`가 0이 아닌 경우 `MIN_SHARES == 1e18`보다 커야 함을 강제하는 확인(`_checkMinShares`)을 수행합니다. 이 제한은 토큰을 인출하려는 마지막 사용자에 대한 DoS 취약점을 생성합니다.

공격자는 소액을 입금하여 마지막 출금 트랜잭션을 프론트런할 수 있으며, 이로 인해 `_checkMinShares`가 합법적인 출금을 되돌리게(revert) 합니다. DoS를 성공적으로 실행한 후, 공격자는 자신의 출금을 백런(backrun)하여 합법적인 사용자가 **비용 없이 무기한** 토큰을 인출할 수 없도록 할 수 있습니다.

```solidity
    function _withdraw(
        address caller,
        address receiver,
        address owner,
        uint256 assets,
        uint256 shares
    )
        internal
        override
    {
        _checkZeroAmount(assets);
        _checkZeroAmount(shares);
        _checkRestricted(caller);
        _checkRestricted(receiver);
        _checkRestricted(owner);

        super._withdraw(caller, receiver, owner, assets, shares);
        _checkMinShares(); // <@ audit
    }
```

이 취약점의 영향은 공격자가 소액 예금으로 프론트런하여 마지막 사용자가 자금을 인출하는 것을 영구적으로 방지할 수 있으며, 피해자의 자산을 비용 없이 원하는 기간 동안 금고에 효과적으로 잠글 수 있다는 것입니다.

이 문제를 PoC(개념 증명)하려면 `falcon-contracts-evm/test` 폴더에 새 파일을 만들고 이름을 `sUSDfLastWithdrawal.t.sol`로 지정한 다음 다음 스마트 계약을 붙여넣으십시오. 그런 다음 `forge test --mt testDoSLastWithdrawal -v`를 실행하여 시나리오를 복제하십시오:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.8.28;

import {MockERC20} from "./mocks/MockERC20.sol";
import {SigUtils} from "./utils/SigUtils.sol";
import {ClassicMinterV1} from "src/ClassicMinterV1.sol";
import {StakedUSDf} from "src/StakedUSDf.sol";

import {StakingRewardsDistributor} from "src/StakingRewardsDistributor.sol";
import {USDf} from "src/USDf.sol";
import {USDfSilo} from "src/USDfSilo.sol";
import {IStakedUSDf} from "src/interfaces/IStakedUSDf.sol";

import {TransparentUpgradeableProxy} from "@openzeppelin/contracts/proxy/transparent/TransparentUpgradeableProxy.sol";
import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import {Test, console2} from "forge-std/Test.sol";

contract sUSDfLastWithdrawal is Test {
    // State variables
    USDf public usdf;
    StakedUSDf public stakedUsdf;
    ClassicMinterV1 public minter;
    USDfSilo public silo;
    StakingRewardsDistributor public distributor;
    MockERC20 public token;

    // Test accounts
    address internal admin;
    address internal operator;
    address internal treasury;
    address internal user1;
    address internal attacker;
    address internal backendSigner;
    uint256 internal backendPrivateKey;

    // Constants
    bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
    bytes32 public constant REWARDER_ROLE = keccak256("REWARDER_ROLE");
    bytes32 public constant OPERATOR_ROLE = keccak256("OPERATOR_ROLE");

    // SigUtils
    SigUtils internal sigUtils;
    bytes32 internal classicMinterDomainSeparator;

    function setUp() public {
        // Setup test accounts
        admin = makeAddr("admin");
        operator = makeAddr("operator");
        treasury = makeAddr("treasury");
        user1 = makeAddr("user1");
        attacker = makeAddr("attacker");
        backendPrivateKey = 0x1234;
        backendSigner = vm.addr(backendPrivateKey);

        vm.startPrank(admin);

        // Deploy implementations
        USDf usdfImpl = new USDf();
        StakedUSDf stakedUsdfImpl = new StakedUSDf(treasury);

        // Initialize data for proxies
        bytes memory usdfData = abi.encodeWithSelector(USDf.initialize.selector, admin);

        // Deploy USDf proxy
        TransparentUpgradeableProxy usdfProxy = new TransparentUpgradeableProxy(address(usdfImpl), admin, usdfData);
        usdf = USDf(address(usdfProxy));

        silo = new USDfSilo(address(usdf));

        // Deploy mock token
        token = new MockERC20("Mock Token", "MTK");

        // Deploy StakedUSDf proxy
        bytes memory stakedUsdfData = abi.encodeWithSelector(
            StakedUSDf.initialize.selector,
            usdf,
            admin,
            silo,
            1 days, // vesting period
            1 days // 0 cooldown duration
        );
        TransparentUpgradeableProxy stakedUsdfProxy =
            new TransparentUpgradeableProxy(address(stakedUsdfImpl), admin, stakedUsdfData);
        stakedUsdf = StakedUSDf(address(stakedUsdfProxy));

        // Deploy StakingRewardsDistributor
        distributor = new StakingRewardsDistributor(stakedUsdf, IERC20(address(usdf)), admin, operator);

        // Deploy minter with treasury as fee recipient
        minter = new ClassicMinterV1(admin, address(usdf), treasury, backendSigner);

        // Setup basic roles
        usdf.grantRole(MINTER_ROLE, admin);
        usdf.grantRole(MINTER_ROLE, address(minter));

        // Grant REWARDER_ROLE to distributor
        stakedUsdf.grantRole(REWARDER_ROLE, address(distributor));

        stakedUsdf.setCooldownDuration(0); // NO COOLDOWN.

        // Mint `user1` and `attacker` some USDf.
        usdf.mint(address(distributor), 1000e18);
        usdf.mint(address(attacker), 2000e18);
        usdf.mint(address(user1), 2000e18);

        vm.stopPrank();
    }

    function testDoSLastWithdrawal() public {
        // User1 deposits into staking contract.
        vm.startPrank(user1);
        usdf.approve(address(stakedUsdf), 1000e18);
        console2.log("User1 deposits 1000e18 USDf.");
        stakedUsdf.deposit(1000e18, user1);
        vm.stopPrank();

        // At this point we can assume that times passes and only one depositor has remained on the vault, the last user who tries to withdraw fully out of the vault.
        // Attacker has seen the tx of user1 and will DoSed it with no cost.

        console2.log("User1 submits transaction to withdraw fully out of the vault now.");

        // Here, attacker frontruns user1's full withdrawal by depositing just enough so `MinSharesViolation` error to be triggered.
        vm.startPrank(attacker);
        console2.log("Attacker frontruns him and deposit 0.5e18 USDf.");
        usdf.approve(address(stakedUsdf), 0.5e18);
        stakedUsdf.deposit(0.5e18, attacker);
        vm.stopPrank();

        // User1 is the last withdrawer and wants to withdraw fully but his withdraw will revert.
        vm.startPrank(user1);
        uint256 allShares = stakedUsdf.balanceOf(user1);
        console2.log("User1 withdrawal is reverting due to `MinSharesViolation` error.");
        vm.expectRevert();
        stakedUsdf.redeem(allShares ,user1, user1);
        vm.stopPrank();

        // Attacker backruns the tx with his withdrawal and at no cost, he has effectivelly DoSed the withdraw of the last depositor of the vault with just 0.5e18.
        vm.startPrank(attacker);
        console2.log("Attacker withdraws his 0.5e18 USDf and effectivelly has DoSed the last withdrawal of the vault.");
        stakedUsdf.withdraw(0.5e18, attacker, attacker);
        vm.stopPrank();

        // The result is that the last user's withdrawal of the vault can be DoSed for ever and at no cost.
    }
}
```

## 권장 사항

출금 시 최소 주식을 확인하지 않는 것을 고려하십시오.

# [M-02] 예치 공격 시 주식을 발행할 수 없음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

사용자가 USDf를 stakedUSDf 계약에 예치하면 sUSDf를 받습니다. 계산은 다음과 같이 [ERC4626](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/8ab1f5acf958b937531cee87f99ae4c0242f0dee/contracts/token/ERC20/extensions/ERC4626Upgradeable.sol#L247)을 따릅니다:

```
  function _convertToShares(uint256 assets, Math.Rounding rounding) internal view virtual returns (uint256) {
        return assets.mulDiv(totalSupply() + 10 ** _decimalsOffset(), totalAssets() + 1, rounding);
    }
```

사용자가 처음 100e18 USDf를 예치하면 `assets * totalSupply() + 1 / totalAssets() + 1` = 1e18 \* (0+1) / (0+1) = 1e18 주식을 얻게 됩니다.

예치 중 주식이 1e18 미만이 될 수 없도록 하는 `MIN_SHARES` [확인](https://github.com/falconfinance/falcon-contracts-evm/blob/9c34a242ae6c39e2054d5e3bb62e44328339aaa1/src/StakedUSDf.sol#L219)이 있습니다.

```
function _checkMinShares() internal view {
        uint256 supply = totalSupply();
>       if (supply > 0 && supply < MIN_SHARES) {
            revert MinSharesViolation();
        }
    }

    function _deposit(address caller, address receiver, uint256 assets, uint256 shares) internal override {
        _checkZeroAmount(assets);
        _checkZeroAmount(shares);
        _checkRestricted(caller);
        _checkRestricted(receiver);

        super._deposit(caller, receiver, assets, shares);
        _checkMinShares();
    }
```

이 확인의 문제는 악의적인 사용자가 `deposit()`이 호출되기 전에 1e18 USDf를 stakedUSDf 계약에 직접 주입하여 `totalAssets() = 1e18` 및 `totalSupply() = 0`으로 만들 수 있다는 것입니다.

주식이 계산될 때, `assets * totalSupply() + 1 / totalAssets() + 1`, 1e18 \* 1 / (1e18 + 1) = 1이며, 처음으로 더 많은 자산(예: 1e20)이 예치되더라도 반환되는 주식은 1e18 미만이 되어 `ZeroAmount` 오류를 유발합니다.

아래 테스트는 직접 예치 공격을 설명하며, deposit.t.sol 아래에 추가하십시오.

```
    function test2_deposit() public {
        // User deposits USDf directly into the stakedUSDf contract, resulting in "ZeroAmount" issue

        uint mintAmount = 1e25;

        vm.startPrank(user1);
        deal(address(usdf), user1, mintAmount);
        usdf.approve(address(stakedUSDf), mintAmount);
        // Directly deposit 1e18 worth of USDf before any `deposit()` is called
        usdf.transfer(address(stakedUSDf), 1e18);
        console2.log("USDF BALANCE of user:", usdf.balanceOf(address(stakedUSDf)));
        console2.log("SUSDF TOTALSUPPLY of user", stakedUSDf.totalSupply());
        // If this number is changed from 1e18 -> 1e25 , all will fail because of "MIN_SHARE_VIOLATION"
        stakedUSDf.deposit(1e18, user1);
        vm.stopPrank();
        console2.log("USDF BALANCE TREASURY:", usdf.balanceOf(address(stakedUSDf.TREASURY())));
        console2.log("USDF BALANCE in contract:", usdf.balanceOf(address(stakedUSDf)));
        console2.log("SUSDF TOTALSUPPLY in contract", stakedUSDf.totalSupply());

        console2.log("USDF BALANCE of user:", usdf.balanceOf(address(user1)));
        console2.log("SUSDF TOTALSUPPLY of user", stakedUSDf.balanceOf(user1));
    }
```

## 권장 사항

이런 일이 발생하지 않도록 `totalSupply() == 0`인 경우 계약 내부의 모든 자산을 휩쓸어(sweep) 버리십시오. 예치 함수에서 `_deposit()`을 호출하기 전에 `previewDeposit()`을 계산하여 위의 공격에 대해 0을 반환하므로 `_deposit()` 대신 예치 함수를 재정의하십시오.

```
Append in stakedUSDf.sol:

     function deposit(uint256 assets, address receiver) public override returns (uint256) {
        if(IERC20(asset()).balanceOf(address(this)) != 0 && totalSupply() == 0) {
            SafeERC20.safeTransfer(IERC20(asset()), TREASURY,  IERC20(asset()).balanceOf(address(this)));
        }
        super.deposit(assets, receiver);
     }
```

# [L-01] `redistributeLockedAmount()`에서의 재분배가 충돌할 수 있음

`stakedUSDf.redistributeLockedAmount()`가 호출될 때 `burnShares`가 true이면 `from`의 주식을 소각하고 `_updateVestingAmount`를 호출하여 모든 주주에게 USDf를 분배합니다.

```
 function redistributeLockedAmount(address from, bool burnShares) external onlyRole(DEFAULT_ADMIN_ROLE) {
        // Redistribute only when `from` is restricted
        require(isRestricted[from], AddressNotRestricted(from));

        uint256 amountToDistribute = balanceOf(from);
        _checkZeroAmount(amountToDistribute);

        if (burnShares) {
            uint256 usdfToVest = previewRedeem(amountToDistribute);
>           _burn(from, amountToDistribute);
>           _updateVestingAmount(usdfToVest);
        } else {
            _transfer(from, TREASURY, amountToDistribute);
        }

        emit LockedAmountRedistributed(from, burnShares, amountToDistribute);
    }
```

문제는 `transferInRewards()`도 동일한 `updateVestingAmount()` 함수를 사용한다는 것입니다.

```
function transferInRewards(uint256 amount) external onlyRole(REWARDER_ROLE) {
        require(totalSupply() > 0, MinSharesViolation());

        _updateVestingAmount(amount);
        IERC20(asset()).safeTransferFrom(msg.sender, address(this), amount);
        emit RewardsReceived(amount);
    }
```

`vestingPeriod` > 0인 경우 `_updateVestingAmount()`가 작동하려면 `getUnvestedAmount()`가 0이어야 합니다.

```
 function _updateVestingAmount(uint256 newVestingAmount) internal {
        _checkZeroAmount(newVestingAmount);
        require(getUnvestedAmount() == 0, RewardsStillVesting());
        vestingAmount = newVestingAmount;
        lastDistributionTimestamp = uint40(block.timestamp);
    }
```

예를 들어 `vestingPeriod`가 1일이고 보상자가 방금 일부 보상을 전송한 경우, 관리자는 1일 후까지 소각된 sUSDf 주식을 재분배하기 위해 `redistributeLockedAmount()`를 호출할 수 없습니다. 또한 관리자는 가득 기간 내에 `redistributeLockedAmount()`를 1회 이상 호출할 수 없으므로 관리자가 1명 이상의 사용자 sUSDf 토큰을 소각하고 분배하려는 경우 문제가 됩니다.

권장 사항:

sUSDf 주식을 소각할 때 `_updateVestingAmount()` 함수를 제거하여 계약의 USDf 토큰이 주주들에게 즉시 분배되도록 하는 것을 고려하십시오.

```
        if (burnShares) {
            uint256 usdfToVest = previewRedeem(amountToDistribute);
            _burn(from, amountToDistribute);
-           _updateVestingAmount(usdfToVest);
        } else {
```

# [L-02] 잘못된 이벤트 방출

`FalconPosition::withdraw()`에서 함수는 NFT 스테이킹 포지션의 실제 소유자가 아닌 승인된 사용자에 의해 호출될 수 있습니다. 그러나 방출된 이벤트 `PositionClosed`는 `msg.sender`가 항상 스테이킹 포지션의 소유자라고 잘못 가정하여 `msg.sender`가 승인된 주소일 때 부정확한 이벤트 방출로 이어집니다.

```solidity
    event PositionClosed(uint256 indexed tokenId, address indexed owner, uint256 principal, uint256 duration);

    function withdraw(uint256 tokenId) external nonReentrant {
        _checkAuthorized(_ownerOf(tokenId), msg.sender, tokenId);

       // ...

        emit PositionClosed(tokenId, msg.sender, totalAmount - tokensOwed, position.duration); // <@ audit
        emit FeesCollected(tokenId, tokensOwed);
    }
```

스테이킹 포지션의 실제 소유자를 메모리에 저장하고 이벤트 방출 시 사용하는 것을 고려하십시오.

# [L-03] `cooldownAssets()` 호출 시 `CooldownEnd`가 더 길어짐

`StakedUSDf.sol`에서 쿨다운 메커니즘이 활성 상태일 때 사용자가 출금을 시작하면 자산에 액세스하기 전에 `cooldownDuration` 동안 기다려야 합니다. 그러나 쿨다운 기간이 만료되기 전에 사용자가 다시 `cooldownAssets`를 호출하면 이전에 쿨다운된 모든 자산이 새 쿨다운 기간 동안 다시 잠기며 `cooldownEnd`가 `block.timestamp` + `cooldownDuration`으로 재설정됩니다.

```solidity
    function cooldownAssets(uint256 assets, address owner) external ensureCooldownOn returns (uint256 shares) {
        cooldowns[owner].cooldownEnd = uint104(block.timestamp) + cooldownDuration;
        cooldowns[owner].underlyingAmount += uint152(assets);

        shares = super.withdraw(assets, address(silo), owner);
    }
```

이 취약점의 영향은 사용자가 `cooldownAssets`를 반복적으로 호출하는 경우 이전에 쿨다운된 자산이 새 요청마다 다시 잠기므로 자산을 인출하려는 사용자가 무기한 지연을 겪을 수 있다는 것입니다. 이전에 언스테이크된 자산에 대한 쿨다운 재설정을 방지하려면 새로 추가된 자산만 새 쿨다운을 트리거하도록 논리를 수정하고 이미 쿨다운 중인 자산에 대해서는 원래 쿨다운 기간을 유지하십시오.

# [L-04] 낮은 달러 가격 토큰은 `ClassicMinterV1`에서 담보로 사용할 수 없음

`ClassicMinterV1::_mintAmount()` 함수에서 `USDf` 토큰 발행 계산은 `10_000`의 `PRICE_SCALE`을 사용합니다. 이는 가격이 `$0.00009`보다 낮은 토큰은 4소수점 스케일의 가격이 `>1`이어야 하고 Solidity가 소수를 지원하지 않기 때문에 사용할 수 없음을 의미합니다. 예를 들어 가격이 `$0.00009`인 토큰은 4소수점으로 `0.00009e4`이며 이는 `0.9`와 같으므로 `params.price` 변수에 주어질 수 없습니다.

```solidity
    function _mintAmount(uint256 depositAmount, address token, uint256 price) internal view returns (uint256) {
        uint256 collateralScale = 10 ** IERC20Metadata(token).decimals();
        uint256 scaledAmount = (depositAmount * price * _usdfScale);
        uint256 scaledPriceAmount = scaledAmount / PRICE_SCALE;
        return scaledPriceAmount / collateralScale;
    }
```

실제로 [Shiba](https://coinmarketcap.com/currencies/shiba-inu/), [BitTorrent](https://coinmarketcap.com/currencies/bittorrent-new/) 등과 같이 가격이 매우 낮은 시가 총액이 높은 암호화폐가 많으므로 이 문제를 완화하려면 가격의 정밀도 스케일을 `1e4`에서 `1e8` 또는 `1e18`과 같은 더 큰 숫자로 늘리는 것을 고려하십시오.

# [L-05] `callerFundedMint()`가 fee-on-transfer 토큰과 작동하지 않음

`ClassicMinterV1::callerFundedMint` 함수는 프로토콜의 `BACKEND_SIGNER_ROLE address`가 서명한 `MintParams` 메시지의 서명을 확인한 후 담보 토큰을 입금하여 `USDf` 토큰을 `mint`하는 데 사용됩니다. `signature verification` 후 담보 금액은 아래와 같이 `treasury contract`로 전송됩니다:

```solidity
        // Transfer collateral to treasury
        IERC20(params.token).safeTransferFrom(params.caller, treasury, depositAmount);
```

하지만 여기서 문제는 `params.token (collateral)`이 전송 수수료(fee on transfer) 토큰인 경우 재무부(treasury)에 입금된 금액이 `depositAmount`보다 적다는 것입니다. 그러나 `USDf tokens`는 아래와 같이 `depositAmount` 자체에 대해 발행됩니다:

```solidity
        uint256 amountToMint = _mintAmount(depositAmount, params.token, params.price);
```

결과적으로 프로토콜은 `recipient`에게 발행된 `USDf` 양에 비해 재무부에 입금된 담보 토큰이 적으므로 자금을 잃게 됩니다.

따라서 `ClassicMinterV1` 계약으로 `실제 전송된 담보 금액`을 계산하고 해당 금액을 사용하여 `발행할 USDf 토큰 금액`을 계산하는 것이 좋습니다.

# [L-06] 제한된(Restricted) 사용자가 `StakedUSDf::unstake`를 호출할 수 있음

`StakedUSDf`에서 사용자가 `StakedUSDf::cooldownAssets`를 호출한 후 제한된 경우, `StakedUSDf::unstake`가 `msg.sender` 또는 `reciever`가 제한되었는지 확인하지 않기 때문에 쿨다운 기간이 끝난 후 보상을 언스테이크할 수 있습니다.

```solidity
    function unstake(address receiver) external {
        _checkZeroAddress(receiver);

        UserCooldown storage userCooldown = cooldowns[msg.sender];
        uint256 assets = userCooldown.underlyingAmount;

        require(block.timestamp >= userCooldown.cooldownEnd, CooldownNotEnded());

        userCooldown.cooldownEnd = 0;
        userCooldown.underlyingAmount = 0;

        silo.withdraw(receiver, assets);
    }
```

`StakedUSDf::unstake`는 `StakedUSDf` 계약의 맥락에서 호출되므로, 이를 통해 악의적인 사용자가 `restricted`로 태그된 후에도 자금을 언스테이크할 수 있습니다.

따라서 `StakedUSDf::unstake` 실행 시 `msg.sender`와 `receiver`가 제한되어 있는지 확인하고 사용자 중 한 명이 제한된 상태이면 트랜잭션을 되돌리는 것이 좋습니다.

# [L-07] `vestingPeriod` 변경 시 `totalAssets` 반환 값이 손상됨

`StakedUSDf.sol` 계약은 수익을 창출하기 위해 USDf를 스테이킹하는 ERC4626 금고이며, 보상 예치에 대해 선형 가득(linear vesting)을 사용합니다.
`StakingRewardsDistributor.sol`은 `StakedUSDf.sol`의 `transferInRewards()` 함수를 트리거하여 보상을 전송하고 가득 금액을 업데이트합니다.

관리자가 `setVestingPeriod()` 함수를 호출하여 가득 기간의 기간을 업데이트하고 `getUnvestedAmount()`가 여전히 0이 아닌 값을 반환하는 경우, 현재 보상 가득이 손상되거나 `totalAssets()` 함수가 `getUnvestedAmount()`의 반환 값을 사용하므로 현재 스테이커에게 손실/이익을 초래할 수 있습니다. `getUnvestedAmount()`는 `vestingPeriod` 업데이트의 영향을 직접 받습니다.

또한 `StakedUSDf.sol` 계약의 여러 함수(예: deposit, mint, withdraw, redeem...)를 차단하여 패닉 **산술 언더플로 또는 오버플로**로 되돌릴 수 있습니다.

권장 사항:

`getUnvestedAmount() > 0`인 경우 되돌릴 수 있습니다.

```diff
    function _setVestingPeriod(uint32 newPeriod) internal {
        uint32 oldVestingPeriod = vestingPeriod;
        require(newPeriod <= MAX_VESTING_PERIOD, DurationExceedsMax());
        require(oldVestingPeriod != newPeriod, DurationNotChanged());
        require(newPeriod > 0 || cooldownDuration > 0, ExpectedCooldownOn());
+       require(getUnvestedAmount() == 0, ExpectedCooldownOn());
```

# [L-08] 쿨다운 기간 단축 후 일관성 없는 `unstake` 동작

`StakedUSDf` 계약을 통해 사용자는 `cooldownAssets` 또는 `cooldownShares` 함수를 사용하여 스테이킹된 자산(주식 또는 기본 자산)에 대한 쿨다운 기간을 시작할 수 있습니다. 그런 다음 `unstake` 함수를 사용하여 쿨다운 기간이 끝난 후 자산을 인출합니다. `unstake` 함수에는 다음 확인이 포함됩니다:

```solidity
require(block.timestamp >= userCooldown.cooldownEnd, CooldownNotEnded());
```

이것은 `cooldownEnd timestamp` 이전의 출금을 올바르게 방지합니다. cooldownEnd는 `cooldownAssets` 또는 `cooldownShares`가 호출될 때 `block.timestamp + cooldownDuration`으로 계산됩니다.

`setCooldownDuration` 함수를 사용하면 `DEFAULT_ADMIN_ROLE`이 `cooldownDuration`을 변경할 수 있습니다. 사용자가 쿨다운을 시작한 후 `cooldownDuration이 0으로 설정`되면 사용자의 `cooldownEnd timestamp는 변경되지 않습니다`(여전히 미래임).

문제는 `unstake` 함수가 `0 쿨다운 기간`의 가능성을 고려하지 않는다는 것입니다. 전역 cooldownDuration이 0으로 설정되더라도 변경 전에 쿨다운을 시작한 사용자는 여전히 원래 cooldownEnd 타임스탬프까지 기다려야 합니다. 이는 일관성 없는 동작을 생성합니다:

`cooldownDuration이 0으로 설정`된 후 쿨다운을 시작하는 사용자는 효과적으로 `즉시 인출`할 수 있습니다.
`cooldownDuration`이 0으로 설정되기 전에 쿨다운을 시작한 사용자는 여전히 원래 쿨다운 기간의 적용을 받습니다.
이러한 불일치는 `cooldownDuration`이 0으로 설정되기 전에 쿨다운을 시작한 사용자에게 기회 비용을 발생시킵니다.

권장 사항:

unstake 함수는 0 cooldownDuration의 가능성을 고려하여 수정해야 합니다. 가장 간단하고 효과적인 해결책은 require문에 추가 확인을 추가하는 것입니다:

```solidity
require(block.timestamp >= userCooldown.cooldownEnd || (userCooldown.underlyingAmount> 0 && cooldownDuration == 0), "Cooldown not ended or duration is 0");
```

이 수정은 다음을 보장합니다:

cooldownDuration이 0보다 크면 원래 쿨다운 확인(block.timestamp >= userCooldown.cooldownEnd)이 강제됩니다.
cooldownDuration이 0이면 userCooldown.cooldownEnd 값에 관계없이 출금이 허용됩니다.

# [L-09] StakingRewardsDistributor를 일시 중지하면 보상 분배가 방지됨

`StakingRewardsDistributor` 계약에는 `PAUSER_ROLE`이 호출할 수 있는 `pause()` 함수가 있으며, 이는 `transferInRewards` 함수의 기능을 중단합니다. `StakingRewardsDistributor`의 `transferInRewards` 함수는 보상(USDf)을 `StakedUSDf` 계약으로 전송하는 역할을 합니다. `StakedUSDf` 계약의 `transferInRewards` 함수는 차례로 내부 회계를 업데이트하여 스테이커에게 해당 보상을 분배합니다.

```solidity
    function transferInRewards(uint256 _rewardsAmount) external whenNotPaused onlyRole(OPERATOR_ROLE) {
        USDF_TOKEN.approve(address(STAKING_VAULT), _rewardsAmount);
        STAKING_VAULT.transferInRewards(_rewardsAmount);
    }
```

`StakingRewardsDistributor`가 일시 중지되면 `transferInRewards` 함수를 호출할 수 없으므로 `StakedUSDf` 계약으로 새로운 보상을 보낼 수 없습니다. 그러나 사용자는 `StakingRewardsDistributor`가 일시 중지된 경우에도 `StakedUSDf` 계약에 USDf를 입금할 수 _있습니다_.

이로 인해 사용자는 스테이킹 보상을 받을 것으로 기대하고 USDf를 입금하지만 분배 메커니즘이 일시 중지되어 보상을 _받지 못하는_ 상황이 발생합니다. 그들의 자금은 의도된 혜택을 발생시키지 않고 `StakedUSDf` 계약에 효과적으로 잠깁니다(활성화된 경우 쿨다운 기간 적용). 이는 사용자에게 불공평하며 기회 비용의 손실을 나타냅니다. 사용자는 USDf를 단순히 보유하거나 다른 DeFi 프로토콜에서 사용하는 것보다 더 나쁜 상황에 처하게 됩니다.

이는 이미 `sUSDf`를 계약에 입금한 사용자에게도 영향을 미칩니다. 그들의 자금은 보상을 발생시키지 않더라도 `cooldownDuration`이 끝날 때까지 잠깁니다.

핵심 문제는 `StakedUSDf`의 입금 기능과 `StakingRewardsDistributor`의 보상 분배 메커니즘 간의 동기화 부족입니다. 입금은 항상 허용되지만 보상 분배는 일방적으로 일시 중지될 수 있습니다.

`FalconPosition::withdraw` 함수에서도 유사한 문제가 발견됩니다. 사용자가 해당 `기간`으로 발행한 후 `기간이 지원되지 않는` 경우, 해당 기간 동안 스테이커에게 보상을 발생시키기 위해 `depositRewards`를 호출할 수 없음에도 불구하고 `matureTimestamp에 도달할 때까지` 인출할 수 없습니다. 따라서 이는 `FalconPositoin minters`에게 기회 비용을 생성합니다. 그들은 특정 기간 동안 그 대가로 아무런 보상 없이 계약에 자금을 잠그고 있기 때문입니다.

권장 사항:

이 문제를 완화하기 위한 몇 가지 가능한 접근 방식이 있습니다:

1.  **`StakedUSDf`에서 입금 일시 중지:** 가장 직접적인 해결책은 `StakingRewardsDistributor`가 일시 중지될 때 `StakedUSDf` 계약의 입금도 일시 중지하는 것입니다.

2.  **사용자에게 알림:** 입금 일시 중지가 바람직하지 않은 경우, `StakingRewardsDistributor`가 일시 중지될 때마다 사용자 인터페이스(및 문서)에 명확한 경고를 표시하여 사용자가 이 기간 동안 보상을 받지 못할 것임을 알려야 합니다.

# [L-10] 제한된 사용자는 전송을 통해 자산 몰수를 우회할 수 있음

`StakedUSDf` 계약은 특정 계정이 특정 방식으로 계약과 상호 작용하는 것을 방지하기 위한 제한 메커니즘(`isRestricted` 매핑)을 구현합니다. `redistributeLockedAmount` 함수는 제한된 계정의 sUSDf 잔액을 몰수(소각하거나 재무부로 전송)하도록 설계되었습니다. 이 함수는 `DEFAULT_ADMIN_ROLE`이 호출하도록 되어 있습니다.

```solidity
    function redistributeLockedAmount(address from, bool burnShares) external onlyRole(DEFAULT_ADMIN_ROLE) {
        // Redistribute only when `from` is restricted
        require(isRestricted[from], AddressNotRestricted(from));

        uint256 amountToDistribute = balanceOf(from);
        _checkZeroAmount(amountToDistribute);

        if (burnShares) {
            uint256 usdfToVest = previewRedeem(amountToDistribute);
            _burn(from, amountToDistribute);
            _updateVestingAmount(usdfToVest);
        } else {
            _transfer(from, TREASURY, amountToDistribute);
        }

        emit LockedAmountRedistributed(from, burnShares, amountToDistribute);
    }
```

그러나 `StakedUSDf` 계약은 제한된 사용자가 자신의 sUSDf 토큰을 다른 계정으로 전송하는 것을 _막지 않습니다_. `ERC20Upgradeable`(`StakedUSDf`가 상속함)의 `_transfer` 함수는 `from`과 `to` 주소가 0 주소인지와 발신자의 잔액이 충분한지만 확인합니다. `isRestricted` 상태는 _확인하지 않습니다_.

이는 제한된 사용자가 관리자가 `redistributeLockedAmount` 함수를 호출하기 _전에_ sUSDf 토큰을 제한되지 않은 계정으로 간단히 전송할 수 있음을 의미합니다. 이는 제한을 효과적으로 우회하고 사용자가 자산에 대한 통제권을 유지할 수 있게 하여 `redistributeLockedAmount` 메커니즘의 목적을 무력화시킵니다.

`USDf` 계약은 유사한 제한 메커니즘을 _가지고 있으며_, 발신자나 수신자가 제한된 경우 전송을 _방지합니다_(`USDf::_update` 함수를 통해). `USDf`와 `StakedUSDf` 간의 이러한 불일치가 문제의 주요 원인입니다.

가장 효과적인 해결책은 제한된 사용자가 sUSDf 토큰을 전송하는 것을 방지하는 것입니다. 이는 `StakedUSDf`에서 `transfer` 및 `transferFrom` 함수를 재정의하여 `USDf.sol`에서 수행되는 방식과 유사하게 `from` 및 `to` 주소 모두의 `isRestricted` 상태에 대한 확인을 포함함으로써 달성할 수 있습니다.
