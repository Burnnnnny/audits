# 정보 (About)

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**DyadStablecoin/contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# DYAD 소개 (About DYAD)

DYAD 프로토콜은 사용자가 ETH와 같은 담보를 예치하여 최소 100%의 낮은 담보 비율로 무이자 스테이블 코인을 발행할 수 있도록 합니다. 사용자는 생태계에 참여하기 위해 Note라는 DYAD 전용 NFT를 소유해야 하며, 이는 사용자 활동을 추적하고 ERC-4626 금고의 주식 토큰을 통해 발행, 이자 농사(yield farming) 및 담보 포지션 전송을 용이하게 합니다.

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

_검토 커밋 해시_ - [973cb961198890449e0a80b4be4065dccff0abc0](https://github.com/DyadStablecoin/contracts/tree/973cb961198890449e0a80b4be4065dccff0abc0)

_수정 검토 커밋 해시_ - [fe10994f7ba9477ca464c5ec286f99eaea1dc18c](https://github.com/DyadStablecoin/contracts/tree/fe10994f7ba9477ca464c5ec286f99eaea1dc18c)

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `VaultManagerV5`
- `DyadXPv2`
- `UniswapV3Staking`

# 발견 사항 (Findings)

# [C-01] 보상을 청구하여 전체 등유(kerosene) 공급을 고갈시킬 수 있음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

Note가 스테이킹될 때 보상을 계산하는 데 사용되는 현재 공식은 다음과 같습니다:

```solidity
	function _calculateRewards(uint256 noteId, StakeInfo storage stakeInfo) internal view returns (uint256) {
	    uint256 timeDiff = block.timestamp - stakeInfo.lastRewardTime;

	    uint256 xp = dyadXP.balanceOfNote(noteId);

->	    return timeDiff * rewardsRate * stakeInfo.liquidity * xp;
	}
```

문제는 XP 값이 정말 크기 때문에 보상이 매우 높을 수 있다는 것입니다.

등유의 총공급량은 `1_000_000_000 1e18`이지만 대부분의 Note는 너무 많은 XP를 가지고 있습니다.

최상의 시나리오에서는 XP가 총공급량보다 커서 아무것도 스테이킹할 수 없습니다.

최악의 시나리오에서는 사용자가 1 wei만큼 작은 최소 유동성 투자로 총공급량의 대부분을 청구할 수 있습니다.

## 권장 사항

보상 계산에서 XP를 축소해야 합니다.
`rewardsRate`와 `stakeInfo.liquidity`를 함께 곱한 후 `1e18`로 나누는 것이 좋습니다. `xp` 값의 크기에 따라 `xp`를 곱할 때도 동일한 작업을 수행해야 합니다.

# [C-02] UniV3 유동성 포지션의 기본 토큰이 확인되지 않음

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명

`UniswapV3Staking.stake()` 내에서 스테이킹된 UniV3 유동성 포지션의 기본 토큰이 확인되지 않습니다. 이를 통해 악의적인 행위자가 자신의 임의 ERC20 토큰으로 새로운 UniV3 풀을 생성하고 대량의 토큰을 스스로 발행할 수 있습니다. 이로 인해 이 풀에서 해당 포지션의 `liquidity`가 매우 높아져 이 포지션을 스테이킹하여 엄청난 양의 보상을 얻을 수 있습니다.

## 권장 사항

`stake()` 내에서 포지션의 기본 토큰이 USDC와 DYAD인지 확인하십시오.

# [H-01] 사용자는 의도된 발생률(accrual rate)의 1/100을 받게 됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

문서에 설명된 대로 기본 발생률은 `1e9`이어야 합니다.

그러나 `DyadXPv2._computeXP`에서는 `1e7`로 설정되어 있습니다:

```solidity
    uint256 adjustedAccrualRate = accrualRateModifier * 1e7;
```

따라서 사용자는 예치금에 대해 빈약한 수익률(의도한 값보다 2배 낮은 자릿수)을 받게 됩니다.

## 권장 사항

다음 변경 사항을 고려하십시오:

```diff
-    uint256 adjustedAccrualRate = accrualRateModifier * 1e7;
+    uint256 adjustedAccrualRate = accrualRateModifier * 1e9;
```

# [H-02] 기존 사용자에 대해 `totalXP`가 잘못 계산됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`src/staking/DyadXPv2.sol`에서 계약은 `initialize` 함수를 통해 초기화되어야 하며 각 사용자의 `totalXP`도 여기에서 계산됩니다:

```solidity
    function initialize(address owner) public reinitializer(2) {
        __UUPSUpgradeable_init();
        __Ownable_init(msg.sender);

        uint256 dnftSupply = DNFT.totalSupply();

        for (uint256 i = 0; i < dnftSupply; ++i) {
            noteData[i] = NoteXPData({
                lastAction: uint40(block.timestamp),
                keroseneDeposited: uint96(KEROSENE_VAULT.id2asset(i)),
                lastXP: noteData[i].lastXP,
->              totalXP: noteData[i].lastXP,
                dyadMinted: DYAD.mintedDyad(i)
            });
        }
    }

```

그러나 `totalXP`는 업그레이드 중 실제 합계(보너스 포함)가 아닌 `lastXP`일 뿐이므로 마지막으로 발생한 보너스를 고려하지 않습니다.

이는 업그레이드와 마지막 작업 사이의 `elapsed` 시간이 0이 아닐 가능성이 매우 높기 때문입니다:

```solidity
    function _computeXP(NoteXPData memory lastUpdate) internal view returns (uint256) {
->      uint256 elapsed = block.timestamp - lastUpdate.lastAction;
        uint256 deposited = lastUpdate.keroseneDeposited;
        uint256 dyadMinted = lastUpdate.dyadMinted;
        uint256 totalXP = lastUpdate.totalXP;

        uint256 accrualRateModifier = totalXP > 0 ? 1e18 / totalXP.log10() : 1e18;

        uint256 adjustedAccrualRate = accrualRateModifier * 1e7;

        // bonus = deposited + deposited * (dyadMinted / (dyadMinted + deposited))
        uint256 bonus = deposited;

        if (dyadMinted + deposited != 0) {
            bonus += deposited.mulWadDown(dyadMinted.divWadDown(dyadMinted + deposited));
        }

        return uint256(lastUpdate.lastXP + (elapsed * adjustedAccrualRate * bonus) / 1e18);
    }
```

수치 예시:

```
lastXP: 1e18

_computeXP:

elapsed: 10_000
deposited: 1e18
dyadMinted: 0
totalXP: 0

accrualRateModifier: 1e18

adjustedAccrualRate: 1e18 * 1e7 = 1e25

bonus: 1e18

0 + 1e18 != 0
    bonus = 1e18 + (1e18 * (0 / (0 + 1e18)) = 1e18

-> 1e18 + (10_000 * 1e25 * bonus) / 1e18 = 1e18 + 10_000 * 1e25

```

이 예에서 사용자는 현재 코드로 `totalXP = 1e18`을 갖지만 대신 `totalXP = 1e18 + 1e29`를 가져야 합니다.

`totalXP`가 의도한 것보다 낮아지므로 다음에 함수가 호출될 때(예: Uniswap 포지션의 보상 계산) `accrualRateModifier`에 영향을 미칩니다.

## 권장 사항

다음 수정을 적용하는 것을 고려하십시오:

```diff
    function initialize(address owner) public reinitializer(2) {
        __UUPSUpgradeable_init();
        __Ownable_init(msg.sender);

        uint256 dnftSupply = DNFT.totalSupply();

        for (uint256 i = 0; i < dnftSupply; ++i) {
+       uint256 totalXP = _computeXP(noteData[i]);
            noteData[i] = NoteXPData({
                lastAction: uint40(block.timestamp),
                keroseneDeposited: uint96(KEROSENE_VAULT.id2asset(i)),
                lastXP: noteData[i].lastXP,
-               totalXP: noteData[i].lastXP,
+               totalXP: totalXP,
                dyadMinted: DYAD.mintedDyad(i)
            });
        }
    }

```

# [H-03] 청산 중 `updateXP`가 생략될 수 있음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`liquidate` 함수는 `dyad.burn`을 호출하지만 청산된 note에 `KEROSENE_VAULT`가 없거나 `depositAmount == 0`인 경우 `updateXP`가 생략될 수 있습니다. 이렇게 되면 청산된 note의 `balanceOfNote`가 올바르지 않게 됩니다.

```solidity
    function liquidate(uint256 id, uint256 to, uint256 amount)
        external
        isValidDNft(id)
        isValidDNft(to)
        returns (address[] memory, uint256[] memory)
    {
        uint256 cr = collatRatio(id);
        if (cr >= MIN_COLLAT_RATIO) revert CrTooHigh();
        uint256 debt = dyad.mintedDyad(id);
>>      dyad.burn(id, msg.sender, amount); // changes `debt` and `cr`

        lastDeposit[to] = block.number; // `move` acts like a deposit

        uint256 numberOfVaults = vaults[id].length();
        address[] memory vaultAddresses = new address[](numberOfVaults);
        uint256[] memory vaultAmounts = new uint256[](numberOfVaults);

        uint256 totalValue = getTotalValue(id);
        if (totalValue == 0) return (vaultAddresses, vaultAmounts);

        for (uint256 i = 0; i < numberOfVaults; i++) {
            Vault vault = Vault(vaults[id].at(i));
            vaultAddresses[i] = address(vault);
            if (vaultLicenser.isLicensed(address(vault))) {
                uint256 depositAmount = vault.id2asset(id);
>>              if (depositAmount == 0) continue;
                uint256 value = vault.getUsdValue(id);
                uint256 asset;
                if (cr < LIQUIDATION_REWARD + 1e18 && debt != amount) {
                    uint256 cappedCr = cr < 1e18 ? 1e18 : cr;
                    uint256 liquidationEquityShare = (cappedCr - 1e18).mulWadDown(LIQUIDATION_REWARD);
                    uint256 liquidationAssetShare = (liquidationEquityShare + 1e18).divWadDown(cappedCr);
                    uint256 allAsset = depositAmount.mulWadUp(liquidationAssetShare);
                    asset = allAsset.mulWadDown(amount).divWadDown(debt);
                } else {
                    uint256 share = value.divWadDown(totalValue);
                    uint256 amountShare = share.mulWadUp(amount);
                    uint256 reward_rate = amount.divWadDown(debt).mulWadDown(LIQUIDATION_REWARD);
                    uint256 valueToMove = amountShare + amountShare.mulWadUp(reward_rate);
                    uint256 cappedValue = valueToMove > value ? value : valueToMove;
                    asset = cappedValue * (10 ** (vault.oracle().decimals() + vault.asset().decimals()))
                        / vault.assetPrice() / 1e18;
                }
                vaultAmounts[i] = asset;

                vault.move(id, to, asset);
>>              if (address(vault) == KEROSENE_VAULT) {
                    dyadXP.updateXP(id);
                    dyadXP.updateXP(to);
                }
            }
        }

        emit Liquidate(id, msg.sender, to, amount);

        return (vaultAddresses, vaultAmounts);
    }
```

## 권장 사항

부채 변경 후 청산된 note의 XP를 업데이트하는 것을 고려하십시오.

# [H-04] DyadXPv2 초기화 불가능

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`DyadXPv2.initialize()`는 `DNFT` 공급에 대해 반복합니다:

```solidity
for (uint256 i = 0; i < dnftSupply; ++i) {
            noteData[i] = NoteXPData({
                lastAction: uint40(block.timestamp),
                keroseneDeposited: uint96(KEROSENE_VAULT.id2asset(i)),
                lastXP: noteData[i].lastXP,
                totalXP: noteData[i].lastXP,
                dyadMinted: DYAD.mintedDyad(i)
            });
        }
```

현재 `DNFT`의 총공급량은 882입니다.

88회 반복에 사용된 가스는 3.130682e6입니다.

이는 루프의 총비용이 최소 3.130682e7임을 의미하며, 이는 3e7(30M) 가스 제한을 초과합니다.

**개념 증명 (Proof of Concept)**

foundry 제한으로 인해 882회 반복 전체에 대해 PoC를 실행할 수는 없습니다.

88회 반복(전체의 10%)에 사용된 가스를 계산하려면 `DyadXPv2.initialize()`를 다음과 같이 변경하십시오:

```diff

            noteData[i] = NoteXPData({
                lastAction: uint40(block.timestamp),
                keroseneDeposited: uint96(KEROSENE_VAULT.id2asset(i)),
                lastXP: noteData[i].lastXP,
                totalXP: noteData[i].lastXP,
                dyadMinted: DYAD.mintedDyad(i)
            });

+         if (i == dnftSupply / 10 - 1) {
+              console.log("gas used in %s iterations is %e", i+1, gas - gasleft());
+               return;
+           }
```

그런 다음 이 PoC 파일을 `test/`에 추가하고 `test_InitCost`를 실행하십시오:

**코드화된 PoC**

```solidity
import {Test, console} from "forge-std/Test.sol";
import {DyadXPv2} from "../../src/staking/DyadXPv2.sol";

import {Kerosine} from "../../src/staking/Kerosine.sol";
import {DNft} from "../../src/core/DNft.sol";
import {KeroseneVault} from "../../src/core/VaultKerosene.sol";
import {Dyad} from "../../src/core/Dyad.sol";
import {UUPSUpgradeable} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

contract StakingTest is Test {
    address VAULT_MANAGER = 0xB62bdb1A6AC97A9B70957DD35357311e8859f0d7;
    address dNFT_owner = 0xDeD796De6a14E255487191963dEe436c45995813;

    DyadXPv2 dyadXP_v2;

    Kerosine kerosine;
    DNft dnft;
    Dyad dyad;
    KeroseneVault keroseneVault;

    function setUp() external {
        string memory RPC_URL = "https://eth-mainnet.g.alchemy.com/v2/vDqr_aMYwqkkAUs-sM3S07j92pkny3yE";
        vm.createSelectFork(RPC_URL);

        dyad = Dyad(0xFd03723a9A3AbE0562451496a9a394D2C4bad4ab);
        dnft = DNft(0xDc400bBe0B8B79C07A962EA99a642F5819e3b712);
        kerosine = Kerosine(0xf3768D6e78E65FC64b8F12ffc824452130BD5394);
        keroseneVault = KeroseneVault(0x4808e4CC6a2Ba764778A0351E1Be198494aF0b43);
    }

    function test_InitCost() public {
        // Deploy DyadXPv2 implementation
        DyadXPv2 impl = new DyadXPv2(
            VAULT_MANAGER,
            address(keroseneVault),
            address(dnft),
            address(dyad)
        );

        address xp_owner = 0xDeD796De6a14E255487191963dEe436c45995813;

        // Upgrade DyadXP->DyadXPv2
        vm.prank(xp_owner);
        UUPSUpgradeable xp_proxy = UUPSUpgradeable(payable(0xeF443646E52d1C28bd757F570D18F4Db30dB70F4));
        xp_proxy.upgradeToAndCall(address(impl), abi.encodeWithSignature("initialize(address)", address(this)));
        dyadXP_v2 = DyadXPv2(address(xp_proxy));
    }
}
```

**콘솔 출력:**

```bash
Ran 1 test for test/CantInit.t.sol:StakingTest
[PASS] test_InitCost() (gas: 4767974)
Logs:
  gas used in 89 iterations is 3.18022e6
```

## 권장 사항

초기화 시 전체 dnftSupply에 대해 반복하지 않는 것을 고려하십시오.

# [H-05] 사용자가 `UniV3Staking`에 스테이킹할 수 없음

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

`UniswapV3Staking.stake()`는 다음과 같은 방식으로 사용자로부터 NFT를 가져옵니다:

```solidity
        positionManager.safeTransferFrom(msg.sender, address(this), tokenId);
```

그러나 이렇게 하면 `UniswapV3Staking` 계약에서 `onERC721Received()` 함수가 호출되고 반환 값을 기대하게 됩니다. 그러나 해당 함수가 이 계약에 구현되어 있지 않으므로 ERC721 전송이 실패하여 `stake()` 실행이 되돌려집니다.

## 권장 사항

`safeTransferFrom()`을 통해 ERC721을 수신할 수 있도록 다음 함수를 구현하십시오:

```solidity
function onERC721Received(address, address, uint256, bytes calldata) public pure returns (bytes4) {
        return msg.sig;
}
```

# [H-06] 스테이킹 보상은 `balanceOfNote`가 변경되기 전에 매번 청구되어야 함

## 심각도

**영향:** 중간 (Medium)

**가능성:** 높음 (High)

## 설명

스테이킹 보상은 `balanceOfNote`에 의존하므로 `balanceOfNote`가 변경될 때마다 `claimRewards` 함수가 호출되어야 합니다. 그러나 이 기능은 구현되지 않았습니다. 이렇게 하면 보상이 잘못 계산되어 충분한 자산 손실이 발생할 수 있습니다.

```solidity
    function _calculateRewards(uint256 noteId, StakeInfo storage stakeInfo) internal view returns (uint256) {
        uint256 timeDiff = block.timestamp - stakeInfo.lastRewardTime;

>>      uint256 xp = dyadXP.balanceOfNote(noteId);

>>      return timeDiff * rewardsRate * stakeInfo.liquidity * xp;
    }
```

## 권장 사항

`balanceOfNote`가 변경(`deposit`, `withdraw`, `mintDyad`, `burnDyad`, `liquidate` (`id` 및 `to` 노트 모두))되기 전에 `stakes[noteId].isStaked == true`인 노트에 대해 보상을 청구하는 것을 고려하십시오.

# [M-01] 사용자가 유동성을 추가할 때 일부 보상을 놓치게 됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`UniswapV3Staking.stake`에서 노트가 스테이킹될 때 현재 유동성이 저장됩니다:

```solidity
    function stake(uint256 noteId, uint256 tokenId) external {
        require(dnft.ownerOf(noteId) == msg.sender, "You are not the Note owner");

        StakeInfo storage stakeInfo = stakes[noteId];
        require(!stakeInfo.isStaked, "Note already used for staking");

        (,,,,,,, uint128 liquidity,,,,) = positionManager.positions(tokenId);
        require(liquidity > 0, "No liquidity");

        positionManager.safeTransferFrom(msg.sender, address(this), tokenId);

        stakes[noteId] = StakeInfo({
->        liquidity: liquidity,
          lastRewardTime: block.timestamp,
          tokenId: tokenId,
          isStaked: true
        });

        emit Staked(msg.sender, noteId, tokenId, liquidity);
    }
```

이 값은 보상을 청구할 때 사용됩니다:

```solidity
    function _calculateRewards(uint256 noteId, StakeInfo storage stakeInfo) internal view returns (uint256) {
        uint256 timeDiff = block.timestamp - stakeInfo.lastRewardTime;

        uint256 xp = dyadXP.balanceOfNote(noteId);

->      return timeDiff * rewardsRate * stakeInfo.liquidity * xp;
    }
```

그러나 사용자가 `NonfungiblePositionManager.increaseLiquidity`를 사용하여 자신의 포지션에 유동성을 계속 추가하면 유동성이 오래되어(stale) 원래 값(스테이킹했을 때)을 사용하므로 의도한 것보다 적은 보상을 받게 됩니다.

## 권장 사항

유동성을 저장하는 대신 최신 유동성을 사용하는 것을 고려하십시오:

```diff
    function _calculateRewards(uint256 noteId, StakeInfo storage stakeInfo) internal view returns (uint256) {
        uint256 timeDiff = block.timestamp - stakeInfo.lastRewardTime;

        uint256 xp = dyadXP.balanceOfNote(noteId);

-       return timeDiff * rewardsRate * stakeInfo.liquidity * xp;
+       (,,,,,,, uint128 liquidity,,,,) = positionManager.positions(stakeInfo.tokenId);
+       return timeDiff * rewardsRate * liquidity * xp;
    }
```

# [M-02] 보상이 전송되지 않아도 보상 시간이 업데이트됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`UniswapV3Staking`에서 사용자는 `claimRewards` 함수를 호출하여 보상을 청구할 수 있습니다. 그러나 `lastRewardTime`은 보상을 0으로 받더라도 항상 업데이트됩니다:

```solidity
    function _claimRewards(uint256 noteId, StakeInfo storage stakeInfo, address recipient) internal {
        require(dnft.ownerOf(noteId) == msg.sender, "You are not the Note owner");
        require(stakeInfo.isStaked, "Note not staked");
        uint256 rewards = _calculateRewards(noteId, stakeInfo);
->      stakeInfo.lastRewardTime = block.timestamp;

        if (rewards > 0) {
            rewardsToken.transferFrom(rewardsTokenHolder, recipient, rewards);
            emit RewardClaimed(recipient, rewards);
        }
    }
```

`rewardsRate`를 0으로 설정하여 보상이 일시적으로 비활성화된 시나리오를 가정해 보십시오. 이 기간 동안 `claimRewards`를 호출하는 사용자는 아무것도 받지 못하더라도 `lastRewardTime`이 업데이트됩니다. 다음에 보상이 다시 활성화되면 그들은 더 적은 보상을 받게 됩니다.

## 권장 사항

다음 변경 사항을 적용하는 것을 고려하십시오:

```diff
    function _claimRewards(uint256 noteId, StakeInfo storage stakeInfo, address recipient) internal {
        require(dnft.ownerOf(noteId) == msg.sender, "You are not the Note owner");
        require(stakeInfo.isStaked, "Note not staked");
        uint256 rewards = _calculateRewards(noteId, stakeInfo);
-       stakeInfo.lastRewardTime = block.timestamp;

        if (rewards > 0) {
+           stakeInfo.lastRewardTime = block.timestamp;
            rewardsToken.transferFrom(rewardsTokenHolder, recipient, rewards);
            emit RewardClaimed(recipient, rewards);
        }
    }
```

# [M-03] 잘못된 XP 업데이트로 인해 청산된 사용자가 슬래싱으로부터 잘못 보호됨

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

기본적으로 금고를 청산하는 것은 `withdraw` 및 `deposit` 기능으로 모두 작동하며 등유 금고에서 인출할 때 Note의 XP가 삭감됩니다. 그러나 청산 중에는 `beforeKeroseneWithdrawn` 대신 `updateXP`가 사용되므로 청산된 Note에 대해 이 작업이 수행되지 않습니다. 결과적으로 모의 인출 중에도 Note XP는 영향을 받지 않습니다.

```solidity
                vault.move(id, to, asset);
                if (address(vault) == KEROSENE_VAULT) {
                    dyadXP.updateXP(id);
                    dyadXP.updateXP(to);
                }
            }
```

## 권장 사항

이를 수정하려면 리팩토링이 필요할 수 있습니다. 아래와 같은 방법이 효과적일 것입니다.

```diff
                vaultAmounts[i] = asset;

-                vault.move(id, to, asset);
-                if (address(vault) == KEROSENE_VAULT) {
-                    dyadXP.updateXP(id);
-                    dyadXP.updateXP(to);
-                }
-            }
+              if (address(vault) == KEROSENE_VAULT) {
+                   dyadXP.beforeKeroseneWithdrawn(id);
+                   vault.move(id, to, asset);//This subtracts from id first and adds to to
+                   dyadXP.updateXP(to);
+               } else {
+               vault.move(id, to, asset);
+           }
```

# [L-01] 초기화 중 소유자가 무시됨

`DyadXPv2` 및 `UniswapV3Staking`에서 `initialize` 함수에는 `owner` 인수가 있습니다. 그러나 `msg.sender`가 대신 소유자로 설정되므로 이 인수는 소유권 초기화 중에 무시됩니다:

```solidity
    function initialize(
->      address _owner,  //@audit ignored
        IERC20 _rewardsToken,
        INonfungiblePositionManager _positionManager,
        IDyadXP _dyadXP,
        DNft _dnft,
        uint256 _rewardsRate,
        address _rewardsTokenHolder
    ) public initializer {
        __UUPSUpgradeable_init();
        __Ownable_init(msg.sender);

        rewardsToken = _rewardsToken;
        positionManager = _positionManager;
        dyadXP = _dyadXP;
        dnft = _dnft;
        rewardsRate = _rewardsRate;
        rewardsTokenHolder = _rewardsTokenHolder;
    }
```

이로 인해 `msg.sender`가 `_owner`와 다른 경우 잘못된 소유자가 설정될 수 있습니다. `initialize`에서 `owner` 인수를 제거하거나 `__Ownable_init`에 전달하십시오.

# [L-02] totalXP가 10 미만인 경우 사용자 작업의 DOS

`_computeXP`는 totalXP가 0보다 큰 경우 발생률 수정자(accrual rate modifier)를 `1e18/log10(totalXP)`로 계산합니다. 문제는 10보다 작은 숫자의 상용로그(base10 log)는 정의되지 않음, 0에서 "0.소수점" 사이의 범위이며, 솔리디티에서는 결국 0으로 내림된다는 것입니다. 결과적으로 `accrualRateModifier`를 계산하려는 시도는 되돌려져 `_computeXP`를 DOS시킵니다.

```solidity
    function _computeXP(NoteXPData memory lastUpdate) internal view returns (uint256) {
        uint256 elapsed = block.timestamp - lastUpdate.lastAction;
        uint256 deposited = lastUpdate.keroseneDeposited;
        uint256 dyadMinted = lastUpdate.dyadMinted;
        uint256 totalXP = lastUpdate.totalXP;

        uint256 accrualRateModifier = totalXP > 0 ? 1e18 / totalXP.log10() : 1e18; //@note

        uint256 adjustedAccrualRate = accrualRateModifier * 1e7;//@note
//...
```

코드베이스에서 볼 수 있듯이 `_computeXP`는 `updateXP`(볼트 매니저에서 dyad가 발행/소각될 때, 토큰이 등유 금고에 예치될 때, 청산될 때 호출됨), `beforeKeroseneWithdrawn`(토큰이 등유 금고에서 인출될 때 호출됨) 및 DyadXPv2 정보를 쿼리하는 기타 인스턴스에서 광범위하게 사용됩니다.

이 발생 확률은 등유 인출 후 누적된 totalXP가 적어야 하므로 다소 낮습니다.

totalXP가 10 미만이 되지 않도록 보장하는 것이 좋습니다. 10 - 99 값의 로그는 1이고 1e18/1은 여전히 1e18이므로 확인을 > 100으로 리팩토링할 수 있습니다.

```diff
    function _computeXP(NoteXPData memory lastUpdate) internal view returns (uint256) {
        uint256 elapsed = block.timestamp - lastUpdate.lastAction;
        uint256 deposited = lastUpdate.keroseneDeposited;
        uint256 dyadMinted = lastUpdate.dyadMinted;
        uint256 totalXP = lastUpdate.totalXP;

-       uint256 accrualRateModifier = totalXP > 0 ? 1e18 / totalXP.log10() : 1e18;
+       uint256 accrualRateModifier = totalXP > 99 ? 1e18 / totalXP.log10() : 1e18;

        uint256 adjustedAccrualRate = accrualRateModifier * 1e7;
//...
```

# [L-03] 사용되지 않는 `isDNftOwner` 제거 가능

`isDNftOwner` 수정자가 `_authorizeCall` 확인으로 대체되었으므로 사용되지 않는 이 수정자를 제거할 수 있습니다.

# [L-04] unstake() 함수 재진입 가능

작업 순서는 다음과 같습니다:

```solidity
_claimRewards(noteId, stakeInfo, recipient);

positionManager.safeTransferFrom(address(this), msg.sender, stakeInfo.tokenId);

delete stakes[noteId];
```

`msg.sender`가 계약인 경우 `safeTransferFrom()`은 `msg.sender`에서 `onERC721Received()` 함수를 호출합니다.
이 시점에서 `stakes[noteId]`가 아직 삭제되지 않았으므로 사용자는 NFT를 `UniswapV3Staking` 계약으로 다시 전송한 다음 `unstake()`를 다시 입력할 수 있으며 되돌려지지 않습니다.

`_claimRewards()`가 `stakeInfo.lastRewardTime`을 `block.timestamp`로 업데이트하므로 재진입 시 얻은 보상은 `0`이 되어 재진입으로 인한 자금 손실은 발생하지 않습니다. 그러나 보상 계산의 향후 변경이 공격자가 unstake()를 재진입하여 보상을 고갈시키는 치명적인 취약점으로 이어질 수 있으므로 위험합니다.

현재 유일한 영향은 이 재진입을 통해 `Unstaked` 이벤트를 무제한으로 스팸할 수 있다는 것입니다.

권장 사항은 다음과 같이 작업 순서를 변경하는 것입니다:

```diff
_claimRewards(noteId, stakeInfo, recipient);

-positionManager.safeTransferFrom(address(this), msg.sender, stakeInfo.tokenId);

delete stakes[noteId];

+positionManager.safeTransferFrom(address(this), msg.sender, stakeInfo.tokenId);

```
