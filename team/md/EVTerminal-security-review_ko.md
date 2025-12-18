# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Sage-Contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# EV Terminal 소개 (About EV Terminal)

EV Terminal은 ERC-314 토큰을 구현하는 DEX 플랫폼입니다. ERC314는 ERC20의 파생물로, 토큰에 유동성 풀을 통합하여 네이티브 스왑을 가능하게 하고 가스 소비를 줄이는 것을 목표로 합니다.

EV 플랫폼을 통해 사용자는 토큰 간의 스왑, 유동성 추가, 거래 수수료 획득을 할 수 있습니다.

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

_검토 커밋 해시_ - [136a71d9a862f556966544d9e167744740e6afd0](https://github.com/Sageterminal/Sage-Contracts/tree/136a71d9a862f556966544d9e167744740e6afd0)

_수정 검토 커밋 해시_ - [86e1bfa16ff1e4844850645c504ad1dc70f09061](https://github.com/Sageterminal/Sage-Contracts/tree/86e1bfa16ff1e4844850645c504ad1dc70f09061)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `EvFactoryProxyOptFee`

# 발견 사항 (Findings)

# [M-01] "팀"이 다른 "팀"이 자금을 받는 것을 차단할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`ERC314Factory`를 사용한 토큰 배포는 두 팀에 대해 수수료를 발생시킵니다:

1. `evFeeCollector`
2. `dzhvFeeCollector`

```solidity
    function claimFees() external {
        uint256 totalAccruedAmount = accruedFeeAmount;
        if (totalAccruedAmount > address(this).balance) {
            totalAccruedAmount = address(this).balance;
        }

        uint256 evTeamShare = (totalAccruedAmount * split) / 10000;
        uint256 dzhvTeamShare = totalAccruedAmount - evTeamShare;

        accruedFeeAmount = 0;

        (bool successEvFeeCollect, ) = payable(evFeeCollector).call{value: evTeamShare}("");
        require(successEvFeeCollect, "Transfer of EV team share failed");

        (bool successDzhvFeeCollect, ) = payable(dzhvFeeCollector).call{value: dzhvTeamShare}("");
        require(successDzhvFeeCollect, "Transfer of DZHV team share failed");
    }
```

팀 중 하나가 계약이고 다음과 같은 상태일 수 있습니다:

1. 손상됨(Compromised)
2. 악의적임(Malicious)
3. 폐업 / 제재됨

위의 상태에서 팀은 전송을 되돌려(revert) 다른 팀이 수수료를 받는 것을 막을 수 있습니다.

## 권장 사항

실패 시 되돌리지 않는 것을 고려하십시오.

# [M-02] 수수료 검색 불일치

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`EVToken` 계약을 사용하여 `ERC314 token`을 생성할 때 `buy()/sell()` 함수에서 청구할 수수료 금액을 지정할 수 있습니다. 나중에 이 수수료는 `constructor`에서 계약을 초기화할 때 메모리에 직접 저장됩니다:

```solidity
File: EvFactoryProxyOptFee.sol
555:
556: contract EVToken is ERC314Implementation {
557:     constructor(
558:         address implementation,
559:         address factory,
560:         address owner,
561:         uint totalSupply,
562:         uint fee,
563:         string memory name,
564:         string memory symbol
565:     ) {
566:         require(fee <= 500, "max 5% fee");
...
...
610:
611:         uint8 feeBytesNeeded = bytesNeeded(fee);
612:         assembly { mstore8(sizeCursor, feeBytesNeeded) }
613:         sizeCursor += 0x1;
614:         assembly { mstore(byteCursor, shl(mul(0x8, sub(0x20, feeBytesNeeded)), fee)) }
615:         byteCursor += feeBytesNeeded;
616:         assembly { mstore(0x40, add(mload(0x40), add(0x1, feeBytesNeeded))) }
...
...
634: }
```

그런 다음, 예를 들어 `ERC314Implementation::buy()` 함수에서 `_opt.fee` 변수(`EvFactoryProxyOptFee#L446`)로부터 수수료를 검색하여 청구할 금액을 계산합니다:

```solidity
File: EvFactoryProxyOptFee.sol
443:     function buy(uint256 amountOutMin) public payable {
...
...
446:         uint256 feeAmount = (msg.value * _opt.fee) / 10000;
447:
448:         uint256 ETHafterFee;
449:         unchecked {
450:             ETHafterFee = msg.value - feeAmount;
451:         }
...
...
473:     }
```

문제는 수수료로 `EVToken` 계약을 초기화할 때 이 수수료가 메모리에 저장되지만 `_opt.fee` 변수는 비어 있다는 것입니다. 한편으로는 `ERC314Implementation::fee()` 함수가 `EVToken` 계약 초기화 중에 지정된 `수수료` 금액을 검색하지만 다른 한편으로는 `_opt.fee` 변수가 비어 있기 때문에 불일치가 발생합니다.

보시다시피 `fee()` 함수는 먼저 `_opt.fee` 변수에서 값을 검색하고 비어 있으면 메모리에서 검색합니다.

```solidity
File: EvFactoryProxyOptFee.sol
112:     function fee() public view virtual returns (uint16) {
113:         if (_opt.feeDisable) return 0;
114:         if (_opt.fee != 0) return _opt.fee;
115:         assembly {
116:             extcodecopy(address(), 0x20, 0x55, 0x4)
117:             let lengths := mload(0x20)
118:             let offset := add(0x55, 0x4)
119:             offset := add(offset, byte(0x0, lengths))
120:             let length := byte(0x1, lengths)
121:             extcodecopy(address(), sub(0x20, length), offset, length)
122:             return(0x0, 0x20)
123:         }
124:     }
```

다음 테스트는 `EVToken`이 `fee=100`으로 초기화된 다음 `fee()` 함수가 `100` 값을 갖는지 확인하는 방법을 보여줍니다. 나중에 3단계에서 Alice가 구매할 때 `accruedFeeAmount`는 0으로 유지됩니다.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import "forge-std/Test.sol";
import {BlankERC314, EVToken} from "../Sage-Contracts/EvFactoryProxyOptFee.sol";

contract EvToken is Test {

    EVToken public evToken;
    address public owner = address(this);
    address public alice = address(0x41123);

    function setUp() public {
        BlankERC314 implementation = new BlankERC314();
        evToken = new EVToken(
            address(implementation),
            address(this),
            owner,
            100e18, // totalSupply
            100, // fee
            "TestEvToken",
            "TEV"
        );
        evToken.initialize(10);  // 10% percent to deployer
    }

    function test_evTokenFeeDiscrepancy() public {
        //
        // 1. Assert fees are set to `100`
        assertEq(evToken.fee(), 100);
        //
        // 2. Owner add liquidity in order to open trading
        vm.deal(owner, 100 ether);
        uint32 timeTillUnlockLiquidity = uint32(block.timestamp + 12);
        evToken.addLiquidity{value: 10 ether}(timeTillUnlockLiquidity);
        //
        // 3. Alice buys `evTokens`. `accruedFeeAmount` should be incremented but the
        // fees are zero
        vm.deal(alice, 100 ether);
        vm.prank(alice);
        evToken.buy{value: 1 ether}(0);
        assertEq(evToken.accruedFeeAmount(), 0);
    }
}
```

이것은 `0이 아닌 수수료 값`으로 `EVToken`을 초기화할 때 즉시 청구되지 않음을 증명합니다. `_opt.fee`를 업데이트하고 `buy()/sell()` 함수에서 청구하려면 새 `fee amount`로 `ERC314Implementation::setTradingFee` 함수를 수동으로 호출해야 하며 이는 중복입니다.

```solidity
File: EvFactoryProxyOptFee.sol
296:     function setTradingFee(uint16 _fee) external onlyOwner {
297:         require(_fee <= 500, "max 5% fee");
298:         _opt.fee = _fee;
299:         if (_fee == 0) _opt.feeDisable = true;
300:         if (_fee != 0) _opt.feeDisable = false;
301:     }
```

## 권장 사항

권장 사항은 `ERC314Implementation::initialize`에서 `_opt.fee`를 초기화하는 것입니다.

# [M-03] `buy()` 또는 `sell()` 함수에서 발생한 수수료가 손실될 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`feeCollector`는 `ERC314Implementation::setFeeCollector` 함수를 사용하여 수수료를 포기할 수 있으며 변수 `_feeCollector`를 `address(0)`로 설정합니다:

```solidity
File: EvFactoryProxyOptFee.sol
328:     function setFeeCollector(address _newFeeCollector) external onlyOwner {
329:         _feeCollector = _newFeeCollector;
330:         if (_newFeeCollector == address(0)) _opt.feeCollectorRenounced = true;
331:         if (_newFeeCollector != address(0)) _opt.feeCollectorRenounced = false;
332:     }
```

문제는 `feeCollector`가 포기한 후에도 `buy()` 또는 `sell()` 함수에서 생성된 수수료가 계속 누적되기 때문에 발생합니다. 누군가 `claimFees()` 함수(공개적으로 액세스 가능)를 호출하면 누적된 수수료가 `address(0)`(`EvFactoryProxyOptFee#L492`)로 전송되어 손실됩니다:

```solidity
File: EvFactoryProxyOptFee.sol
476:     function claimFees() external {
477:         uint256 totalAccruedAmount = accruedFeeAmount;
478:         if (totalAccruedAmount > address(this).balance) {
479:             totalAccruedAmount = address(this).balance;
480:         }
481:
482:         uint256 factoryShare = (totalAccruedAmount * 10) / 100; // 10% to factory owner
483:         uint256 ownerShare = totalAccruedAmount - factoryShare;
484:
485:         accruedFeeAmount = 0;
486:
487:         if(factoryShare > 0) {
488:             (bool successFactory, ) = payable(IFactory(this.factory()).getOwner()).call{value: factoryShare}("");
489:             require(successFactory, "Transfer of factory share failed");
490:         }
491:
492:         (bool successOwner, ) = payable(this.feeCollector()).call{value: ownerShare}("");
493:         require(successOwner, "Transfer of owner share failed");
494:     }
```

다음 테스트는 `feeCollector`가 수수료를 포기(`feeCollector==address(0)`)하고, `Alice`가 `buy()` 함수를 실행할 때 수수료가 계속 누적되지만, `악의적인 사용자`가 `claimFees()` 함수를 호출하여 `accruedFeeAmount` 변수에 누적된 `ETH`가 `address(0)`로 전송되어 해당 수수료가 손실되는 것을 보여줍니다:

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import "forge-std/Test.sol";
import {BlankERC314, EVToken} from "../Sage-Contracts/EvFactoryProxyOptFee.sol";

contract EvToken is Test {

    EVToken public evToken;
    address public owner = address(0x02335);
    address public alice = address(0x41123);

    function setUp() public {
        BlankERC314 implementation = new BlankERC314();
        evToken = new EVToken(
            address(implementation),
            address(this), // factory
            owner,
            100e18, // totalSupply
            100, // fee
            "TestEvToken",
            "TEV"
        );
        evToken.initialize(10);  // 10% percent to deployer
    }

    // address(this) is the `factory`, so it needs the `getOwner` function
    function getOwner() external pure returns(address) { return address(0x03435); }

    function test_lostFeesWhenFeeCollectorRenounces() public {
        vm.prank(owner);
        evToken.setTradingFee(100);
        //
        // 1. Owner add liquidity in order to open trading
        vm.deal(owner, 100 ether);
        vm.deal(alice, 100 ether);
        uint32 timeTillUnlockLiquidity = uint32(block.timestamp + 12);
        vm.prank(owner);
        evToken.addLiquidity{value: 10 ether}(timeTillUnlockLiquidity);
        //
        // 2. Alice buys `evTokens` in order to increment `accruedFeeAmount`
        vm.prank(alice);
        evToken.buy{value: 1 ether}(0);
        uint256 accruedFeesFirstBuy = evToken.accruedFeeAmount();
        assertGt(accruedFeesFirstBuy, 0);
        //
        // 3. Owner renounce fees and feeCollector is address(0)
        vm.prank(owner);
        evToken.setFeeCollector(address(0));
        assertEq(evToken.feeCollector(), address(0));
        assertGt(evToken.accruedFeeAmount(), 0); // accruedFeeAmount still positive
        //
        // 4. Alice buys more and the fees are accrued even more (`accruedFeeAmount` is incremented)
        evToken.buy{value: 1 ether}(0);
        assertGt(evToken.accruedFeeAmount(), accruedFeesFirstBuy);  // new accruedFeeAmount > old accruedFeeAmound
        //
        // 5. Attacker `claimFees` but those fees will be sent to address zero because feeCollector() == address(0)
        uint256 ethBalanceBefore = address(evToken).balance;
        evToken.claimFees();
        assertGt(ethBalanceBefore, address(evToken).balance); // ethBalanceBefore > currentEthBalance
    }
}
```

## 권장 사항

`claimFees()`의 사용을 `feeCollector` 또는 `factory.owner()`로만 제한하십시오.

# [M-04] `owner`와 `feeCollector` 모두 포기할 경우 `ETH` 손실

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`feeCollector`는 `setFeeCollector(address(0))` 함수를 사용하여 포기할 수 있습니다.

```solidity
File: EvFactoryProxyOptFee.sol
328:     function setFeeCollector(address _newFeeCollector) external onlyOwner {
329:         _feeCollector = _newFeeCollector;
330:         if (_newFeeCollector == address(0)) _opt.feeCollectorRenounced = true;
331:         if (_newFeeCollector != address(0)) _opt.feeCollectorRenounced = false;
332:     }
```

마찬가지로 `owner`도 다음 함수로 포기할 수 있습니다.

```solidity
File: EvFactoryProxyOptFee.sol
338:     function renounceOwnership() external onlyOwner {
339:         if (!_opt.feeCollectorRenounced && _feeCollector == address(0)) _feeCollector = this.owner();
340:         if (!_opt.liquidityProviderRenounced && _liquidityProvider == address(0)) _liquidityProvider = this.owner();
341:         _opt.ownerRenounced = true;
342:     }
```

그러나 이 동작은 몇 가지 문제로 이어질 수 있습니다. 두 역할 모두 포기하더라도 `buy()` 및 `sell()` 함수 내에서 수수료가 계속 징수되지만 `claimFees()` 함수 내에서는 소유자 또한 포기함에 따라 `feeCollector`가 `address(0)`로 영구적으로 설정되어 있음에도 불구하고 `feeCollector`에 대한 비율이 계속 계산되기 때문입니다.

```solidity
File: EvFactoryProxyOptFee.sol
476:     function claimFees() external {
...
...
482:         uint256 factoryShare = (totalAccruedAmount * 10) / 100; // 10% to factory owner
483:         uint256 ownerShare = totalAccruedAmount - factoryShare;
...
...
494:     }
```

다음 테스트는 `feeCollector`와 `owner` 모두 자신의 역할을 포기한 경우에도 수수료가 계속 발생하여 `claimFees()` 함수가 `address(0)`로 영구적으로 설정된 `feeCollector`에 대한 비율을 계산하여 해당 비율이 손실되는 것을 보여줍니다.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import "forge-std/Test.sol";
import {BlankERC314, EVToken} from "../Sage-Contracts/EvFactoryProxyOptFee.sol";

contract EvToken is Test {

    EVToken public evToken;
    address public owner = address(0x02335);
    address public alice = address(0x41123);

    function setUp() public {
        BlankERC314 implementation = new BlankERC314();
        evToken = new EVToken(
            address(implementation),
            address(this), // factory
            owner,
            100e18, // totalSupply
            100, // fee
            "TestEvToken",
            "TEV"
        );
        evToken.initialize(10);  // 10% percent to deployer
    }

    // this is the `factory`, so it needs the getOwner function
    function getOwner() external pure returns(address) { return address(0x03435); }

    function test_feesAreAccruedEvenWhenOwnerRenounces() public {
        vm.prank(owner);
        evToken.setTradingFee(100);
        //
        // 1. Owner add liquidity in order to open trading
        vm.deal(owner, 100 ether);
        vm.deal(alice, 100 ether);
        uint32 timeTillUnlockLiquidity = uint32(block.timestamp + 12);
        vm.prank(owner);
        evToken.addLiquidity{value: 10 ether}(timeTillUnlockLiquidity);
        //
        // 2. Alice buys `evTokens` in order to increment `accruedFeeAmount`
        vm.prank(alice);
        evToken.buy{value: 1 ether}(0);
        uint256 accruedFeesFirstBuy = evToken.accruedFeeAmount();
        assertGt(accruedFeesFirstBuy, 0);
        //
        // 3. Owner renounces to everything. feeCollector is address(0) and owner() is zero
        vm.startPrank(owner);
        evToken.setFeeCollector(address(0));
        evToken.renounceOwnership();
        assertEq(evToken.feeCollector(), address(0));
        assertEq(evToken.owner(), address(0));
        assertGt(evToken.accruedFeeAmount(), 0); // accruedFeeAmount still positive
        vm.stopPrank();
        //
        // 4. Alice buys more and the fees are accrued even more
        vm.prank(alice);
        evToken.buy{value: 1 ether}(0);
        assertGt(evToken.accruedFeeAmount(), accruedFeesFirstBuy);  // new accruedFeeAmount > old accruedFeeAmound
        //
        // 5. Anyone `claimFees` but those fees will be sent to address zero 90% (feeCollector) and 10% (factory.owner)
        uint256 ethBalanceBefore = address(evToken).balance;
        evToken.claimFees();
        assertEq(evToken.accruedFeeAmount(), 0);
    }
}
```

## 권장 사항

`feeCollector`와 `owner` 모두 역할을 포기한 경우 발생한 수수료의 100%가 `Factory.owner()`에게 돌아가도록 권장합니다.

# [M-05] 유동성 공급자는 유동성을 제거한 후 다시 추가할 수 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`removeLiquidity`를 사용하여 유동성을 제거할 때 - `_opt.liquidityAdded`는 `false`로 설정되지 않습니다.
따라서 `require(_opt.liquidityAdded == false, "Liquidity already added");` 확인으로 인해 `addLiquidity`를 다시 호출할 수 없습니다.

```solidity
    function addLiquidity(
        uint32 _timeTillUnlockLiquidity
    ) public payable onlyLiquidityProvider {
        require(_opt.liquidityAdded == false, "Liquidity already added");

        _opt.liquidityAdded = true;
-----------
    }

    function removeLiquidity() public onlyLiquidityProvider {
        require(block.timestamp > _opt.timeTillUnlockLiquidity, "Liquidity locked");
        _opt.tradingEnable = false;

        (uint256 reserveETH, ) = getReserves();

        (bool success, ) = payable(msg.sender).call{value: reserveETH}("");
        if (!success) {
            revert("Could not remove liquidity");
        }
        emit RemoveLiquidity(address(this).balance);
    }
```

따라서 `LiquidityProvider`가 유동성을 제거하면 다시 추가할 수 없습니다.

소유자가 포기하고 거래를 비활성화하고 계약의 유동성을 일시적으로 제거해야 하는 시나리오를 고려해 보십시오. 이러한 경우 자금을 제거한 후 다시 추가하고 거래를 활성화할 방법이 없습니다.

## 권장 사항

`removeLiquidity`에 `_opt.liquidityAdded = false;`를 추가하십시오.

# [M-06] 팩토리 소유자가 수수료 수집가의 수수료 청구를 방해할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

`claimFees` 함수는 시간이 지남에 따라 발생하는 수수료를 청구하는 데 사용됩니다.
수수료의 10%는 팩토리 소유자에게 돌아갑니다.

팩토리 소유자가 손상되거나 악의적인 계약인 경우 단순히 트랜잭션을 되돌려 **모든** 배포된 토큰이 수수료를 청구하는 것을 DOS(서비스 거부)할 수 있습니다.

```solidity
    function claimFees() external {
--------------------
        if(factoryShare > 0) {
            (bool successFactory, ) = payable(IFactory(this.factory()).getOwner()).call{value: factoryShare}(""); // @audit malicious factory owner can prevent all underlying token owners from gaining fees
            require(successFactory, "Transfer of factory share failed");
        }

        (bool successOwner, ) = payable(this.feeCollector()).call{value: ownerShare}("");
        require(successOwner, "Transfer of owner share failed");
    }
```

## 권장 사항

`successFactory` 값이 false인 경우 되돌리지 않는 것을 고려하십시오.
대신 푸시 실패 시 팩토리에 대해 별도의 회계 및 푸시 대신 풀(pull) 메커니즘을 유지하는 것을 고려하십시오.

# [M-07] `maxWallet` 변수를 우회할 수 있음

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

`maxWallet` 변수는 사용자가 구매할 수 있는 `ERC` 토큰의 최대 수를 결정하는 데 도움이 됩니다. `ERC314Implementation::buy()` 함수에서는 사용자가 `maxWallet`을 초과하지 않는지 확인합니다.

```solidity
File: EvFactoryProxyOptFee.sol
443:     function buy(uint256 amountOutMin) public payable {
...
...
461:         if (_opt.maxWalletEnable) {
462:             require(
463:                 tokenAmount + _balances[msg.sender] <= maxWallet,
464:                 "Max wallet exceeded"
465:             );
466:         }
...
...
473:     }
```

그러나 이 제한은 우회될 수 있습니다. 다음 테스트는 결국 `Alice2` 지갑이 `maxWallet`을 초과하는 잔액을 갖는 방법을 보여줍니다.

1. `ERC314`에 대해 `10e18`의 `maxWallet`이 구성되어 있습니다.
2. Alice는 `buy()` 함수를 사용하여 일정량의 토큰을 구매합니다.
3. Alice는 더 많이 구매하려고 시도합니다. 그러나 `Max wallet exceeded` 오류로 인해 거래가 되돌려집니다.
4. Alice는 잔액을 다른 제어된 사용자 `Alice2`에게 전송하기로 결정합니다.
5. Alice는 이제 더 많은 `ERC314` 토큰을 구매한 다음 `Alice2`에게 전송할 수 있습니다.

결국 `Alice2`는 `maxWallet` 제한을 초과하는 잔액을 갖게 됩니다.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import "forge-std/Test.sol";
import {BlankERC314, EVToken} from "../Sage-Contracts/EvFactoryProxyOptFee.sol";

contract EvToken is Test {

    EVToken public evToken;
    address public owner = address(this);
    address public alice = address(0x41123);

    function setUp() public {
        BlankERC314 implementation = new BlankERC314();
        evToken = new EVToken(
            address(implementation),
            address(this),
            owner,
            100e18, // totalSupply
            100, // fee
            "TestEvToken",
            "TEV"
        );
        evToken.initialize(10);  // 10% percent to deployer
    }

    function test_maxWalletBypass() public {
        assertEq(evToken.maxWallet(), 0);
        assertEq(evToken.maxWalletEnable(), false);
        //
        // 1. Activate maxWallet
        evToken.setMaxWallet(10e18);
        evToken.enableMaxWallet(true);
        assertEq(evToken.maxWallet(), 10e18);
        assertEq(evToken.maxWalletEnable(), true);
        // Owner add liquidity
        vm.deal(owner, 100 ether);
        uint32 timeTillUnlockLiquidity = uint32(block.timestamp + 12);
        evToken.addLiquidity{value: 10 ether}(timeTillUnlockLiquidity);
        //
        // 2. Alice buys `evTokens`
        vm.deal(alice, 100 ether);
        vm.startPrank(alice);
        evToken.buy{value: 1 ether}(0);
        //
        // 3. Alice buy more tokens but the transaction is reverted by wallet exceeded error
        vm.expectRevert("Max wallet exceeded");
        evToken.buy{value: 1 ether}(0);
        //
        // 4. Alice transfer this balance to another controlled address
        address alice2 = address(0x411232);
        evToken.transfer(alice2, evToken.balanceOf(alice));
        //
        // 5. Alice now can buy more `evTokens`. She transfers again to alice2
        // Now `alice2` has more tokens than the `maxWallet`
        evToken.buy{value: 1 ether}(0);
        evToken.transfer(alice2, evToken.balanceOf(alice));
        assertGt(evToken.balanceOf(alice2), evToken.maxWallet());  // alice2.balance > maxWallet
    }
}
```

## 권장 사항

전송 기능에서 `maxWallet` 제한을 확인하는 것이 좋습니다.

## EV Terminal 응답

_우리의 의도는 사람들이 1 tx로 모든 공급을 한 번에 구매하는 것을 방지하는 것입니다. 여러 거래/다중 지갑을 통해 구매하는 것은 괜찮습니다._

# [M-08] 스왑기능에 마감 기한 누락

## 심각도

**영향:** 낮음 (Low)

**가능성:** 높음 (High)

## 설명

스왑 함수에는 마감 기한(deadline) 매개변수가 없습니다. 이 매개변수는 사용자에게 대기 중인 트랜잭션의 실행을 제한하는 옵션을 제공할 수 있습니다. 마감 기한 매개변수가 없으면 시장 상황이 불리할 때 예상치 못한 시간에 사용자의 거래가 실행될 수 있습니다.

그러나 이 경우에는 슬리피지 보호 기능이 있기 때문에 큰 문제는 아닙니다. 사용자는 설정한 만큼은 최소한 얻게 되지만, 트랜잭션이 블록에 포함될 때 환율이 유리해지는 경우 긍정적인 슬리피지를 놓칠 수 있습니다.

## 권장 사항

`buy()` 및 `sell()`에 마감 기한 매개변수를 도입하십시오.

## EV Terminal 코멘트

_마감 기한 논리를 추가하면 가스가 더 많이 발생합니다. 프로토콜의 목적은 이더리움에서 스왑 가스 수수료를 줄이고 계약을 단순화하는 것입니다. 우리는 문제를 인지하고 있으며 보호를 위해 슬리피지만 사용하는 것을 고수하고 싶습니다._

# [M-09] 악의적인 유동성 공급자가 사용자를 러그풀(Rug-pull)할 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

악의적인 유동성 공급자가 사용자를 러그풀할 수 있는 몇 가지 방법이 있습니다:

#### `addLiquidity`를 이전에 호출하지 않고 `removeLiquidity` 호출

토큰 소유자는 `enableTrading`을 호출하여 유동성 없이 거래를 허용할 수 있습니다.
얼마 후 자금이 계약에 축적되면 악의적인 유동성 공급자가 `removeLiquidity`를 호출하여 모든 자금을 자신에게 보낼 수 있습니다.

#### 낮은 `timeTillUnlockLiquidity` 설정

최소 `timeTillUnlockLiquidity`에 대한 제한이 없습니다. 따라서 악의적인 유동성 공급자는 `timeTillUnlockLiquidity`를 낮은 값으로 설정할 수 있습니다. 토큰은 거래가 가능한 것처럼 보이고 `_opt.liquidityAdded == true`이므로 이미 자금이 지원된 것처럼 보이지만 유동성 공급자는 `removeLiquidity`를 호출하여 자금을 제거할 수 있습니다.

#### `timeTillUnlockLiquidity`를 연장하지 않음

유동성 공급자가 장기간(24개월) 자금을 잠근 토큰을 고려하십시오. 24개월이 지나면 유동성 공급자는 자신이 제공한 유동성뿐만 아니라 계약의 **_모든_** 자금을 가져갈 수 있습니다.

## 권장 사항

1. 거래 시 `_opt.liquidityAdded == true`도 확인하십시오.
2. `initialize` 함수에 최소 `timeTillUnlockLiquidity` 값을 추가하십시오. 이는 `addLiquidity`에서 시행되어야 합니다.
3. 유동성 공급자가 계약의 전체 ETH 준비금이 아닌 초기에 제공된 유동성만 제거하도록 허용하십시오.

## EV Terminal 코멘트

_다른 dex도 동일한 디자인을 가지고 있으며 우리는 그들에게 유연성을 제공하고 싶습니다. 많은 프로젝트가 잠재적인 마이그레이션의 경우를 대비하여 유동성을 잠그지 않고 다중 서명(multisig)에 보관하거나 잠금 해제 상태로 둡니다._

# [M-10] 소유자는 유동성 공급자를 변경할 수 있어야 함

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

유동성 공급자는 준비금을 제거할 수 있는 능력이 있어 계약 잔액을 고갈시킬 수 있습니다.
신뢰할 수 있는 유동성 공급자가 있는 토큰만 활성화될 가능성이 높습니다. 유동성 공급자 계정이 손상된 경우:

- 개인 키 유출
- 압수

이러한 경우 `소유자`는 유동성 잠금이 도달하기 전에 유동성 공급자를 교체할 수 있어야 합니다. 그러나 `setLiquidityProvider`는 유동성 공급자만 호출할 수 있으므로 `소유자`는 유동성 공급자를 교체할 수 없습니다.

```solidity
    function setLiquidityProvider(
        address _newLiquidityProvider
    ) external onlyLiquidityProvider {
        _liquidityProvider = _newLiquidityProvider;
        if (_newLiquidityProvider == address(0)) _opt.liquidityProviderRenounced = true;
        if (_newLiquidityProvider != address(0)) _opt.liquidityProviderRenounced = false;
    }
```

## 권장 사항

유동성 공급자와 소유자의 호출을 허용하도록 `onlyLiquidityProvider`를 변경하는 것을 고려하십시오.

## EV Terminal 코멘트

_우리는 소유자가 포기한 경우에도 필요할 때 유동성을 연장/철회할 수 있도록 디자인을 유지하고 싶습니다._

# [L-01] 함수 `disableFee()`가 `fee()`의 반환 값을 손상시킬 수 있음

어떤 이유로 `disableFee()`는 `_opt.feeDisable = true`로 설정할 수 있습니다:

```solidity
    function disableFee(bool _disable) external onlyOwner {
        _opt.feeDisable = _disable;
        _opt.fee = 0;
    }
```

`_opt.fee == 0`이고 `_opt.feeDisable == true`이기 때문에 `fee()`에서 0 대신 기본값이 반환됩니다:

```solidity
    function fee() public view virtual returns (uint16) {
        if (_opt.feeDisable) return 0;
        if (_opt.fee != 0) return _opt.fee;
        assembly {
            extcodecopy(address(), 0x20, 0x55, 0x4)
            let lengths := mload(0x20)
            let offset := add(0x55, 0x4)
            offset := add(offset, byte(0x0, lengths))
            let length := byte(0x1, lengths)
            extcodecopy(address(), sub(0x20, length), offset, length)
            return(0x0, 0x20)
        }
    }
```

_`setTradingFee()`와 동일한 로직을 포함하므로 `disableFee()` 함수를 제거하는 것을 고려하십시오._

# [L-02] `RemoveLiquidity()` 이벤트에서 방출된 잘못된 값

현재 계약의 ETH 수수료 금액을 방출하므로 올바른 값이 방출되는지 확인하십시오.

```solidity
    function removeLiquidity() public onlyLiquidityProvider {
        require(block.timestamp > _opt.timeTillUnlockLiquidity, "Liquidity locked");

        _opt.tradingEnable = false;

        (uint256 reserveETH, ) = getReserves();

        (bool success, ) = payable(msg.sender).call{value: reserveETH}("");
        if (!success) {
            revert("Could not remove liquidity");
        }

@>      emit RemoveLiquidity(address(this).balance);
    }
```

# [L-03] 사용되지 않는 변수 `lastTransaction`

계약 `ERC314Implementation`에는 전혀 사용되지 않는 저장 변수 `lastTransaction`이 포함되어 있습니다.
제거하거나 누락된 기능을 추가하는 것을 고려하십시오.

# [L-04] `ERC314Implementation`의 승인 경쟁 조건(Approval race conditions)

## 설명

`ERC314Implementation::approve`의 "승인 경쟁 조건"은 허용량을 0이 아닌 값에서 다른 값으로 변경할 때 발생하는 취약점입니다. 소유자가 먼저 지출자에게 일정량의 토큰을 승인한 다음 이 승인을 변경하기로 결정한 경우, 지출자가 새 승인 트랜잭션의 제출과 확인 사이의 기간 동안 원래 승인된 금액을 이동하기 위해 `transferFrom`을 실행할 위험이 있습니다. 이를 통해 지출자는 소유자가 의도한 것보다 더 많은 토큰을 전송할 수 있습니다.

다음 테스트는 취약점을 보여줍니다:

1. 소유자는 Alice에게 토큰 `5e18`을 지출하도록 승인합니다.
2. 소유자는 Alice의 허용량을 `2e18` 토큰으로 줄이기로 결정하고 이 새 승인 트랜잭션을 보냅니다.
3. 소유자의 새 승인 트랜잭션이 확인되기 전에 Alice는 대기 중인 트랜잭션을 발견하고 `5e18` 토큰을 빠르게 전송합니다.
4. 허용량을 `2e18` 토큰으로 줄이려는 소유자의 트랜잭션이 확인됩니다. Alice는 이 새 허용량을 전송할 수 있습니다. 마지막으로 Alice는 소유자가 의도한 것보다 더 많은 토큰을 얻습니다.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.20;

import "forge-std/Test.sol";
import {BlankERC314, EVToken} from "../Sage-Contracts/EvFactoryProxyOptFee.sol";

contract EvToken is Test {

    EVToken public evToken;
    address public owner = address(this);
    address public alice = address(0x41123);

    function setUp() public {
        BlankERC314 implementation = new BlankERC314();
        evToken = new EVToken(
            address(implementation),
            address(this),
            owner,
            100e18, // totalSupply
            100, // fee
            "TestEvToken",
            "TEV"
        );
        evToken.initialize(10);  // 10% percent to deployer
    }

    function test_approveRace() public {
        //
        // 1. Owner approves 5e18 to Alice. Assert alice's allowance
        evToken.approve(alice, 5e18);
        assertEq(evToken.allowance(owner, alice), 5e18);
        //
        // 2. Alice transfer to herself the tokens before Owner modifies the allowance. (frontrun)
        vm.prank(alice);
        evToken.transferFrom(owner, alice, 5e18);
        assertEq(evToken.allowance(owner, alice), 0);
        //
        // 3. Owner update alice's allowance is executed to 2e18
        evToken.approve(alice, 2e18);
        assertEq(evToken.allowance(owner, alice), 2e18);
        //
        // 4. Alice transfer again and she owns 7e18 tokens.
        vm.prank(alice);
        evToken.transferFrom(owner, alice, 2e18);
        assertEq(evToken.balanceOf(alice), 7e18);
    }
}
```

이 문제에 대한 확실한 해결책은 없지만 다음 제안을 고려할 수 있습니다:

- **`increaseAllowance` 및 `decreaseAllowance` 메서드 사용**: 이 함수를 사용하면 값을 완전히 새로 설정하는 대신 현재 허용량을 더하거나 빼는 방식으로 허용량을 변경할 수 있어 이 취약점을 방지하는 데 도움이 될 수 있습니다.

- **새 값으로 설정하기 전에 허용량을 0으로 설정**: 허용량 변경이 필요한 경우 소유자는 먼저 허용량을 0으로 설정한 다음 두 개의 별도 트랜잭션으로 원하는 새 값으로 설정해야 합니다. 이 2단계 프로세스는 경쟁 조건을 악용할 수 있는 창을 닫습니다.

# [L-05] 수수료가 `buy()`와 비교하여 `sell()`에서 비대칭적으로 적용됨

수수료는 항상 ETH로 부과되므로 `buy()`에서는 입력 금액에서 수수료를 먼저 뺀 다음 스왑을 수행합니다:

```solidity
    function buy(uint256 amountOutMin) public payable {
        require(_opt.tradingEnable, "Trading not enable");
        uint256 feeAmount = (msg.value * _opt.fee) / 10000;

        uint256 ETHafterFee;
        unchecked {
@>          ETHafterFee = msg.value - feeAmount;
        }

        unchecked {
            accruedFeeAmount += feeAmount;
        }
        (uint256 reserveETH, uint256 reserveToken) = getReserves();

@>      uint256 tokenAmount = (ETHafterFee * reserveToken) / reserveETH;
        ...
    }
```

그러나 `sell()`에서는 출력 금액에 수수료가 부과됩니다. 즉, `sell()`에서는 수수료가 슬리피지 후에 부과되므로 `buy()` 및 `sell()` 함수에서 동일한 금액이라도 수수료 크기가 다르다는 것을 의미합니다. 따라서 기본적으로 구매 시 슬리피지가 수수료에 적용되지 않으므로 수수료는 항상 `sell()`보다 `buy()`에서 더 큽니다.

## EV Terminal 코멘트:

_계산의 단순성을 위해 우리는 이 방식으로 디자인을 유지하고 슬리피지가 수수료 불일치에 영향을 미친다는 것을 받아들일 것입니다._

# [L-06] `WETH()` 및 `token0()`이 하드코딩됨

`WETH()` 및 `token0()`은 옵티미즘 체인의 WETH 주소인 `0x4200000000000000000000000000000000000006`을 가리키도록 하드코딩되어 있습니다.

```solidity
    function WETH() public pure virtual returns (address) {
        return address(0x4200000000000000000000000000000000000006);
    }

    function token0() public pure virtual returns (address) {
        return address(0x4200000000000000000000000000000000000006);
    }
```

그러나 이 계약이 다른 체인에 배포되도록 의도된 경우 예기치 않은/원치 않는 동작이 발생할 수 있습니다.
`initialize` 함수의 일부로 이 값을 업데이트하는 것을 고려하십시오.

## EV Terminal 코멘트

_우리는 ETH에만 배포할 것이며 계약을 단순하게 유지하기 위해 하드코딩을 원합니다._

# [L-07] 악의적인 행위자가 토큰 가격을 부풀릴 수 있음

거래 시 구매/판매 시 받을 토큰의 양은 `XY=K` 공식을 따르며 계약의 `ETH` 준비금과 `token` 준비금 간의 비율과 관련이 있습니다.

```solidity
    function getAmountOut(
        uint256 value,
        bool _buyBool
    ) public view returns (uint256) {
        (uint256 reserveETH, uint256 reserveToken) = getReserves();

        if (_buyBool) {
            uint256 valueAfterFee = (value * (10000 - _opt.fee)) / 10000;
            return ((valueAfterFee * reserveToken)) / (reserveETH + valueAfterFee);
        } else {
            uint256 ethValue = ((value * reserveETH)) / (reserveToken + value);
            ethValue = (ethValue * (10000 - _opt.fee)) / 10000;
            return ethValue;
        }
    }
```

그러나 `getReserves()`는 유동성 공급자가 제공한 유동성과 `buy` 및 `sell`을 통해 발생한 자금뿐만 아니라 계약의 `ETH` **_잔액(BALANCE)_**을 반환합니다:

```solidity
    function getReserves() public view returns (uint256, uint256) {
        return (
            (address(this).balance - accruedFeeAmount),
            _balances[address(this)]
        );
    }
```

일반적인 `buy` 작업에서 `reserveToken`은 감소하고 `reserveETH`는 비례적으로 증가하며 `XY=K`에 따릅니다.

악의적인 행위자는 `ETH`를 기부할 수 있으며, 이는 `reserveETH`를 **_만_** 증가시켜 공식의 균형을 깨뜨립니다. 이것은 인위적으로 토큰 가격을 부풀립니다(싸게 만듭니다).

`sell`을 원하는 사용자는 예상보다 적은 ETH를 받게 됩니다.

`getReserves`에서 - 계약의 `ETH` 잔액을 사용하는 대신 `reserveETH`에 대해 다음을 반환하도록 고려하십시오:

- 잠긴 유동성
- "buy"를 통해 얻은 유동성에서 `sell`을 통해 판매된 유동성을 뺀 값

이것은 "통제된" 예상 공식을 약속할 것입니다.

## EV Terminal 코멘트

_그렇게 할 인센티브가 거의 없고 영향이 제한적이므로 우리는 코드를 유지하고 이를 인지할 것입니다._
