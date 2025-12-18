# 정보 (About Pashov Audit Group)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**avierauy/fundraiser** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Fundraiser 소개 (About Fundraiser)

Fundraiser 스마트 계약은 사용자가 할인 및 추천 보상을 적용하면서 기본 통화(Native currency), USDC 또는 BEAM 토큰을 사용하여 노드를 구매할 수 있는 모금 시스템을 관리합니다. 이 시스템은 입금을 추적하고, 가격 책정 규칙을 적용하며, 자금을 재무부(treasury)에 할당하여 소유자가 각 토큰 유형에 대한 할인, 추천 보너스 및 가격을 구성할 수 있도록 합니다.

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

**_검토 커밋 해시_ - [9973f0ce627034d5e45baabd82ad0acd3dc5189e](https://github.com/avierauy/fundraiser/tree/9973f0ce627034d5e45baabd82ad0acd3dc5189e)**

**_수정 검토 커밋 해시_ - [4283c94614bfbebaf8c4fa1549d171cce870f729](https://github.com/avierauy/fundraiser/tree/4283c94614bfbebaf8c4fa1549d171cce870f729)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Fundraiser`

# 발견 사항 (Findings)

# [M-01] 악의적인 사용자가 추천 시스템을 악용하여 항상 추가 할인을 받을 수 있음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`_processDepositNative()`, `_processDepositUSDC()` 및 `_processDepositBEAM()` 함수는 각 토큰으로 사용자 입금을 처리합니다. 아래의 `_processDepositBEAM()` 함수를 살펴보겠습니다:

- 253행은 구매하는 노드 수에 맞는 정확한 토큰 양을 보냈는지 확인합니다.
- 257행은 `_setReferrer()` 함수를 호출합니다. 사용자 A가 추천인으로 B를 전달한다고 가정해 보겠습니다. 이로 인해 사용자 A의 추천인이 영구적으로 B로 설정됩니다.
- 260행은 `_isValidReferrer()` 함수를 호출하는데, 이는 추천인이 0이 아닌 주소이고 사용자 A 자신이 아닌지 확인합니다. if 블록에서 원래 금액에 추천 보너스와 할인을 적용합니다.
- 264행은 추천 보너스를 사용자 B에게 보내고 270행은 할인된 금액을 재무부로 보냅니다.

```solidity
File: Fundraiser.sol
252:     function _processDepositBEAM(uint256 quantity, uint256 amount, address referrer) internal {
253:         require(amount == (quantity * getPrice(TokenType.BEAM)), InvalidAmount());
254:
255:         uint256 discountedAmount = amount;
256:
257:         _setReferrer(msg.sender, referrer);
258:
259:
260:         if (_isValidReferrer(msg.sender, referrer)) {
261:             uint256 referralReward = _getReferralReward(amount);
262:             uint256 discountReward = _getDiscount(amount);
263:             discountedAmount = amount - (discountReward + referralReward);
264:             beam.safeTransferFrom(msg.sender, referrer, referralReward);
265:             referrals[referrer].totalBEAMRewarded += referralReward;
266:             referrals[referrer].totalDepositsReferred++;
267:             emit ReferralReward(TokenType.BEAM, referrer, referralReward);
268:         }
269:
270:         beam.safeTransferFrom(msg.sender, treasury, discountedAmount);
271:         _processDeposit(TokenType.BEAM, msg.sender, discountedAmount, quantity);
272:         totalBEAMDeposited += discountedAmount;
273:     }
```

아시다시피 추천은 일반적으로 특정 사용자(이 경우 B)에 대한 일회성 보상을 의도한 것입니다. 문제는 사용자 A가 모든 입금 시 주소(B)를 추천인으로 계속 제공할 수 있다는 것입니다. 이 경우 주소(B)는 단순히 사용자 A의 별칭 주소일 수 있습니다. 이는 `_setReferrer()` 함수의 else 블록과 `_isValidReferrer()`에서 필요한 확인을 우회하기 위해 수행됩니다.

```solidity
function _setReferrer(address user, address referrer) internal {
        if (users[user].referrer == address(0) && _isValidReferrer(user, referrer)) {
            users[user].referrer = referrer;
        } else {
            require(users[user].referrer == referrer || referrer == address(0), InvalidReferral());
        }
    }

    function _isValidReferrer(address user, address referrer) internal pure returns (bool) {
        return referrer != address(0) && referrer != user;
    }
```

사용자가 동일한 추천인(자신의 별칭 주소일 수 있음)을 계속 제공하여 할인을 받고 추천 보상을 항상 돌려받을 수 있으므로, 이는 노드 구매에 대해 예상되는 금액을 받지 못하기 때문에 프로토콜 팀에 간접적으로 손실을 의미합니다.

또한 이 문제에는 두 번째 영향이 있습니다. 즉, 사용자 A가 반복해서 입금하더라도 추천인 B의 `totalDepositsReferred` 변수가 증가한다는 것입니다. 이 `totalDepositsReferred` 문제를 보는 또 다른 방법은, 사용자 A가 추천인 B의 주소를 추천에 사용한다고 가정해 보겠습니다. 사용자 A는 10개의 노드를 구매하기로 결정합니다. 사용자 A는 10개의 노드를 한 번에 구매하는 대신 하나씩 구매하고 매번 동일한 추천인 B를 전달합니다. 즉, 추천인에 대한 `totalDepositsReferred`는 1이 아니라 10이 됩니다. 사용자 A가 10개의 노드를 한 번에 구매했다면 `totalDepositsReferred`가 1이었을 것이므로 이 로직은 분명히 결함이 있습니다. 따라서 지적된 근본 원인에서 발생하는 동작에 명백한 불일치가 있습니다.

### 코드로 작성된 POC

이 POC를 사용하는 방법:

- [여기](https://hardhat.org/hardhat-runner/docs/advanced/hardhat-and-foundry)의 안내에 따라 foundry를 통합하십시오.
- `forge test --mt testReferralExploit` 및 `forge test --mt testTotalDepositsReferredIssue`를 사용하여 두 테스트를 모두 실행하십시오. `-vvvvv` 플래그를 사용하여 터미널에서 추적을 볼 수 있습니다.
- 첫 번째 테스트는 사용자 A가 항상 자신의 별칭 주소를 사용하여 추천 보너스 금액을 돌려받고 추가 할인을 받을 수 있는 방법을 증명합니다.
- 두 번째 테스트는 사용자 A가 호출하는 방식에 따라 사용자 B(추천인)의 `totalDepositsReferred` 값이 어떻게 다른지 증명합니다.
- 위의 두 영향 모두 동일한 근본 원인에서 발생합니다. 해결책은 `권장 사항` 아래에 제공되었습니다.

```solidity
// SPDX-License-Identifier: BUSL-1.1

pragma solidity ^0.8.28;

import {Test, console2} from "forge-std/Test.sol";
import {Fundraiser} from "../contracts/Fundraiser.sol";

contract TestReferralExploit is Test {

    Fundraiser fundRaiser;

    address treasury = makeAddr("treasury");

    address userA = makeAddr("userA");
    address userA_Alias = makeAddr("userA_Alias");
    address userB = makeAddr("userB");

    function setUp() public {

        // Deploy fund raiser with 1e18 as ETH price (i.e. 1 node costs 1 ETH).
        fundRaiser = new Fundraiser(treasury, address(0), address(0), 1e18, 0, 0);
    }

    function testReferralExploit() public {
        fundRaiser.start();

        // Set ETH balance of User A as 10 ETH
        vm.deal(userA, 10e18);

        // User A's first 10 ETH deposit with referrer as user A's alias address
        vm.prank(userA);
        fundRaiser.deposit{value: 10e18}(Fundraiser.TokenType.NATIVE, 10, 10e18, userA_Alias);

        // Assert:
        // 1. User A's alias address received 5% of 10e18
        // 2. User A still holds 5% of 10e18 (discounted amount)
        // 3. Treasury received 90% of 10e18
        assertEq(userA_Alias.balance, 500/10000 * 10e18);
        assertEq(userA.balance, 500/10000 * 10e18);
        assertEq(treasury.balance, 90/100 * 10e18);

        // Mint 10 ETH to User A (existing balance + mint 10 ETH due to how vm.deal works)
        vm.deal(userA, userA.balance + 10e18);

        // User A's second 10 ETH deposit with same referrer being passed as parameter
        vm.prank(userA);
        fundRaiser.deposit{value: 10e18}(Fundraiser.TokenType.NATIVE, 10, 10e18, userA_Alias);

        // Assert:
        // 1. User A's alias address received 5% of 10e18 + previous first deposit amount
        // 2. User A still holds 5% of 10e18 (discounted amount) + previous first deposit amount
        // 3. Treasury received 90% of 10e18 + previous first deposit amount
        // Note - We just multiply by 2 to consider first deposit amount as well.
        assertEq(userA_Alias.balance, (500/10000 * 10e18) * 2);
        assertEq(userA.balance, (500/10000 * 10e18) * 2);
        assertEq(treasury.balance, (90/100 * 10e18) * 2);
    }

    function testTotalDepositsReferredIssue() public {
        fundRaiser.start();

        vm.deal(userA, 10e18);

        // Buying 10 nodes at one time
        vm.prank(userA);
        fundRaiser.deposit{value: 10e18}(Fundraiser.TokenType.NATIVE, 10, 10e18, userB);

        (, , , uint256 totalDepositsReferred) = fundRaiser.referrals(userB);
        assertEq(totalDepositsReferred, 1);

        vm.deal(userA, 10e18);

        // Buying 1 node at a time upto 10 nodes
        for(uint256 i; i < 10; i++) {
            vm.prank(userA);
            fundRaiser.deposit{value: 1e18}(Fundraiser.TokenType.NATIVE, 1, 1e18, userB);
        }

        (, , , totalDepositsReferred) = fundRaiser.referrals(userB);
        assertEq(totalDepositsReferred, 10 + 1); // Adding one from previous 10 nodes at one time deposit
    }
}
```

## 권장 사항

이에 대한 두 가지 해결책이 필요합니다:

1. `_setReferrer()` 함수의 else 블록에서 첫 번째 조건을 제거하십시오. 이렇게 하면 사용자가 별칭 주소로 추천 메커니즘을 악용하여 추가 할인을 받을 수 없습니다.

```diff
function _setReferrer(address user, address referrer) internal {
        if (users[user].referrer == address(0) && _isValidReferrer(user, referrer)) {
            users[user].referrer = referrer;
        } else {
-           require(users[user].referrer == referrer || referrer == address(0), InvalidReferral());
+           require(referrer == address(0), InvalidReferral());
        }
    }
```

2. 별칭 주소 사용을 방지하는 것은 시빌(sybil) 문제이므로 계약 수준에서 해결할 수 없습니다. 이를 해결하려면 사용 중인 추천인이 항상 프로토콜 서명자에 의해 검증되도록 하는 서명 검증 기반 메커니즘을 계약 수준에서 사용하는 것을 고려하십시오. 프론트엔드는 시빌 방지 메커니즘을 구현해야 합니다. [Guild](https://guild.xyz/explorer?order=FEATURED) 및/또는 [Zealy](https://zealy.io/)와 같은 예를 사용하여 시빌 보호를 강화할 수 있습니다.

# [M-02] 노드를 매우 낮은 가격에 구매할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

아래의 코드 스니펫은 `deploy-multichain.js` 파일에서 가져온 것입니다. 보시다시피 배포 중에 `finalUSDCBasePrice`(파싱된 ether 값) 변수 대신 `usdcBasePrice`(파싱된 mwei 값)를 사용합니다. USDC는 BSC 메인넷에서 18자리 소수를 가지지만 다른 체인에서는 6자리입니다. 가격이 18자리 대신 6자리로 배포되므로 노드 구매가 BSC 메인넷에서 매우 저렴해져(거의 0에 가까움) 출시 중 프로토콜에 손실을 초래할 것입니다.

```solidity
    if (networkName === "bscMainnet") {
      finalUSDCBasePrice = ethers.parseUnits(usdcBasePrice, "ether");
    }

    const contract = await Fundraiser.deploy(treasury, usdc, beam, nativeBasePrice, usdcBasePrice, beamBasePrice);
```

`deploy-multichain.js`는 이 검토 범위를 벗어나지만 수정해야 할 중요한 버그를 포함하고 있습니다.

## 권장 사항

아래 수정을 구현하십시오:

```diff
+ const contract = await Fundraiser.deploy(treasury, usdc, beam, nativeBasePrice, finalUSDCBasePrice, beamBasePrice);
```

# [L-01] `lastPriceUpdate`는 생성자에서 업데이트되어야 함

계약이 생성되면 모든 기본 가격이 설정되고 계약은 `paused` 모드로 설정됩니다. 소유자만이 `start()`를 호출하여 계약의 일시 중지를 해제할 수 있습니다.

`start()`는 `lastPriceUpdate = block.timestamp`를 설정합니다.

계약 생성과 `start()` 사이의 기간 동안 사용자는 가격에 관심이 있을 수 있으며 기다리는 동안 `getPrice()`를 호출할 수 있습니다. `start()`가 호출되기 전에 호출하면 `lastPriceUpdate`가 여전히 0이기 때문에 `priceMultiplier`가 계산되어 반환된 가격이 잘못됩니다.

```
 function getPrice(TokenType tokenType) public view returns (uint256) {
        bool weekElapsed = block.timestamp > (lastPriceUpdate + 7 days);

        if (weekElapsed) {
            if (tokenType == TokenType.NATIVE) {
                return nativeBasePrice * priceMultiplier;
            } else if (tokenType == TokenType.USDC) {
                return usdcBasePrice * priceMultiplier;
            } else {
                return beamBasePrice * priceMultiplier;
            }
```

1. 프로토콜이 계약을 생성하고 Beam 가격을 $0.5로 설정합니다. 프로토콜은 계약 생성 후 2일 후에 특정 시간에 시작할 계획입니다.
2. 사용자는 Beam 가격을 조회하고 싶어 공개 `beamBasePrice` 변수에 직접 액세스하는 대신 `getPrice()`를 호출합니다.
3. `lastPriceUpdate`가 0이므로 `weekElapsed`는 true이고 가격은 0.5 * 3 = $1.5가 됩니다.

생성자에서 `lastPriceUpdate`를 `block.timestamp`로 설정하십시오. `lastPriceUpdate`는 `start()`가 호출될 때 다시 업데이트됩니다.

# [L-02] 비원어민(non-native) 입금에 대한 msg.value 확인 누락

deposit() 함수를 통해 사용자는 기본 토큰, USDC 또는 BEAM을 입금하여 노드를 구매할 수 있습니다. 이 함수는 payable로 표시되어 있어 누구나 항상 호출과 함께 msg.value(즉, 기본 토큰)를 제공할 수 있습니다. 문제는 두 가지 else-if 케이스(즉, USDC 및 BEAM)에 대해 msg.value가 전송되지 않았는지 확인하지 않는다는 것입니다.

이로 인해 deposit() 호출과 함께 전송된 모든 기본 토큰은 영구적으로 계약에 잠기게 됩니다. 이 문제의 심각도는 사용자 실수에 의존하므로 낮습니다. 그럼에도 불구하고 계약은 가능한 한 사용자 실수로부터 보호해야 합니다.

```solidity
    function deposit(TokenType assetType, uint256 quantity, uint256 amount, address referrer)
        external
        payable
        whenNotPaused
    {
        require(amount > 0, InvalidAmount());
        require(quantity > 0, InvalidQuantity());
        if (assetType == TokenType.NATIVE) {
            require(amount == msg.value, InvalidAmount());
            _processDepositNative(quantity, amount, referrer);
        } else if (assetType == TokenType.USDC) {
            _processDepositUSDC(quantity, amount, referrer);
        } else if (assetType == TokenType.BEAM) {
            require(block.chainid == ETHEREUM_MAINNET_CHAIN_ID, InvalidChain());
            _processDepositBEAM(quantity, amount, referrer);
        } else {
            revert InvalidToken();
        }
    }
```

**권장 완화 단계**

```diff
    function deposit(TokenType assetType, uint256 quantity, uint256 amount, address referrer)
        external
        payable
        whenNotPaused
    {
        require(amount > 0, InvalidAmount());
        require(quantity > 0, InvalidQuantity());
        if (assetType == TokenType.NATIVE) {
            require(amount == msg.value, InvalidAmount());
            _processDepositNative(quantity, amount, referrer);
        } else if (assetType == TokenType.USDC) {
+           require(msg.value == 0, InvalidAmount());
            _processDepositUSDC(quantity, amount, referrer);
        } else if (assetType == TokenType.BEAM) {
            require(block.chainid == ETHEREUM_MAINNET_CHAIN_ID, InvalidChain());
+          require(msg.value == 0, InvalidAmount());
            _processDepositBEAM(quantity, amount, referrer);
        } else {
            revert InvalidToken();
        }
    }
```

# [L-03] 퍼센트 합계가 10000 미만인지 확인하는 것을 고려하십시오

추천 보너스 및 할인 퍼센트는 입금 시 적용됩니다. 문제는 이 합계가 10000보다 크거나 같지 않은지 확인하는 대신 개별적으로 10000보다 작거나 같은지 확인한다는 것입니다.

```solidity
    function setDiscountPercent(uint256 _discountPercent) external onlyOwner {
        require(_discountPercent <= PERCENT_DENOMINATOR, InvalidPercent());
        discountPercent = _discountPercent;
        emit PercentUpdated(PercentType.DISCOUNT, _discountPercent);
    }

    function setReferralBonusPercent(uint256 _referralBonusPercent) external onlyOwner {
        require(_referralBonusPercent <= PERCENT_DENOMINATOR, InvalidPercent());
        referralBonusPercent = _referralBonusPercent;
        emit PercentUpdated(PercentType.REFERRAL_BONUS, _referralBonusPercent);
    }
```

이로 인해 입금을 처리하는 동안 `discountReward + referralReward` 합이 금액보다 클 수 있으므로 뺄셈(아래 코드 스니펫)에서 언더플로가 발생할 수 있습니다.

```solidity
            uint256 referralReward = _getReferralReward(amount);
            uint256 discountReward = _getDiscount(amount);
            discountedAmount = amount - (discountReward + referralReward);
```

**권장 완화 단계**

이 문제는 간접적으로 관리자 오설정에 달려 있지만, 설정자에서 어쨌든 확인이 구현되고 있다면 이러한 버그가 발생하지 않도록 올바르게 구현하는 것을 고려하십시오.

```diff
    function setDiscountPercent(uint256 _discountPercent, uint256 _referralBonusPercent) external onlyOwner {
+        require(_discountPercent + _referralBonusPercent <= PERCENT_DENOMINATOR, InvalidPercent());

        discountPercent = _discountPercent;
        emit PercentUpdated(PercentType.DISCOUNT, _discountPercent);

+        referralBonusPercent = _referralBonusPercent;
+       emit PercentUpdated(PercentType.REFERRAL_BONUS, _referralBonusPercent);
    }
```

# [L-04] 프로토콜이 일시 중지되면 `lastPriceUpdate`가 재설정됨

start() 함수를 통해 소유자는 계약의 일시 중지를 해제할 수 있습니다(배포 시 처음에는 일시 중지됨). 계약의 일시 중지를 해제하면 사용자가 기본 토큰, USDC 또는 BEAM을 입금하여 노드를 구매할 수 있습니다. 노드는 소유자가 계약에서 설정한 가격으로 구매됩니다. 출시 첫 7일 후 노드 가격에는 `priceMultiplier`가 곱해집니다.

```solidity
File: Fundraiser.sol
118:     function start() external onlyOwner {
119:         _unpause();
120:
122:         lastPriceUpdate = block.timestamp;
123:         emit Start(block.timestamp);
124:     }
```

문제는 프로토콜이 다시 일시 중지/해제되면 위에서 본 것처럼 start() 함수에서 `lastPriceUpdate`가 현재 block.timestamp로 재설정된다는 것입니다. 이로 인해 사용자가 노드를 구매할 때 getPrice() 함수는 승수가 적용되지 않은 기본 가격을 반환합니다. 이는 프로토콜 팀에 간접적인 손실을 의미합니다.

```solidity
function getPrice(TokenType tokenType) public view returns (uint256) {
        bool weekElapsed = block.timestamp > (lastPriceUpdate + 7 days);

        if (weekElapsed) {
            if (tokenType == TokenType.NATIVE) {
                return nativeBasePrice * priceMultiplier;
            } else if (tokenType == TokenType.USDC) {
                return usdcBasePrice * priceMultiplier;
            } else {
                return beamBasePrice * priceMultiplier;
            }
        } else {
            if (tokenType == TokenType.NATIVE) {
                return nativeBasePrice;
            } else if (tokenType == TokenType.USDC) {
                return usdcBasePrice;
            } else {
                return beamBasePrice;
            }
        }
    }
```

이 문제의 심각도는 다음과 같은 이유로 낮습니다:

1. 프로토콜은 여전히 기본 가격만큼의 토큰을 받습니다.
2. 팀은 언제든지 승수를 반영하도록 기본 가격을 직접 구성할 수 있습니다(이 버그를 인지했다고 가정).

**권장 완화 단계**
아래 해결책은 `lastPriceUpdate`가 초기 경우에만 업데이트되도록 합니다. 향후 일시 중지/해제의 경우 변경되지 않은 상태로 유지됩니다.

```diff
function start() external onlyOwner {
        _unpause();

+        if (lastPriceUpdate == 0) lastPriceUpdate = block.timestamp;

        emit Start(block.timestamp);
    }
```

# [L-05] 추천인을 변경할 수 없음

사용자가 토큰을 입금할 때 추천인도 설정합니다. 추천인을 통해 사용자는 할인을 받을 수 있으며 추천인은 한 번만 설정할 수 있습니다.

```
function _setReferrer(address user, address referrer) internal {
        if (users[user].referrer == address(0) && _isValidReferrer(user, referrer)) {
            users[user].referrer = referrer;
        } else {
            require(users[user].referrer == referrer || referrer == address(0), InvalidReferral());
        }
    }
```

그러나 추천인은 USDC에서 블랙리스트에 오르거나 계약인 경우 저수준 호출(low-level call)을 되돌려(revert) 사용자에 대한 트랜잭션 실패를 초래할 수 있습니다. 사용자는 추천인을 `address(0)`로 설정하여 할인 보상을 포기하는 것 외에는 추천인 주소를 변경할 수 없습니다.

잠재적 시나리오:

- 앨리스는 이 프로토콜을 대규모 사용자로부터 알게 되어 그의 주소를 추천인 주소로 사용합니다.
- 프로토콜은 대규모 세일을 진행하여 25% 할인을 제공하므로 앨리스는 혜택을 받기 위해 소액을 지속적으로 구매합니다.
- 추천인이 갑자기 USDC 블랙리스트에 올라 USDC를 그에게 전송할 수 없게 됩니다.
- 앨리스는 더 이상 추천인의 주소를 사용할 수 없으며 `address(0)`만 사용할 수 있습니다. 즉, 그녀는 더 이상 할인을 받을 수 없습니다.

입금자가 언제든지 추천인 주소를 변경할 수 있도록 하는 것이 좋습니다.

# [L-06] 고정 가격은 큰 가격 변동에 의해 차익 거래될 수 있음

프로토콜은 NATIVE, USDC 및 BEAM에 대해 고정 가격을 설정합니다.

```solidity
    function setNativeBasePrice(uint256 _nativeBasePrice) external onlyOwner {
        nativeBasePrice = _nativeBasePrice;
        emit BasePriceUpdated(TokenType.NATIVE, _nativeBasePrice);
    }
```

플래시 크래시가 발생할 경우 가격은 소유자가 업데이트할 수 있는 것보다 훨씬 빠르게 변경됩니다. 가격이 예상보다 높아지므로 입금자는 오라클 업데이트를 프론트런하여 이익을 얻을 수 있습니다.

- 노드 하나가 10 USD라고 가정해 보겠습니다. 즉, 기본 가격은 10 USDC 또는 500 BEAM입니다(BEAM은 토큰당 $0.02).
- BEAM이 갑자기 토큰당 $0.005로 폭락했습니다.
- 오라클이 업데이트되기 전에 사용자는 노드를 구매하기 위해 여전히 500 BEAM이 필요하며, 이제 노드는 BEAM으로 $2.50의 가치가 있습니다.
- 오라클은 노드당 2000 BEAM으로 업데이트되지만 일부 사람들은 이미 $2.50에 노드를 구매했습니다.

기본 토큰 가격이 하락하고 BEAM 가격이 하락(USDC도 디페깅되어 가격이 하락할 수 있음)하는 경우, 소유자는 가격을 하나씩 업데이트해야 하며 입금자는 가장 낮은 비용으로 노드를 얻을 수 있는 최고의 차익 거래 기회를 찾을 수 있습니다.

사용자는 더 저렴한 가격에 노드를 구매하게 됩니다.

가격을 조회하기 위해 오라클을 사용하는 것이 좋습니다.

그렇지 않으면 샌드위치 공격을 방지하기 위해 모든 가격이 하나의 원자적 트랜잭션으로 변경되도록 하는 것이 모범 사례입니다.

```
function setAllPrices(uint256 _nativeBasePrice, uint256 _usdcBasePrice, uint256 _beamBasePrice) external onlyOwner {
        nativeBasePrice = _nativeBasePrice;
        usdcBasePrice = _usdcBasePrice;
        beamBasePrice = _beamBasePrice;
        emit BasePriceUpdated(TokenType.NATIVE, _nativeBasePrice);
        emit BasePriceUpdated(TokenType.USDC, _usdcBasePrice);
        emit BasePriceUpdated(TokenType.BEAM, _beamBasePrice);
    }
```
