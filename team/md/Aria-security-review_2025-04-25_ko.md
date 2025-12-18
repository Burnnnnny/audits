# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**AriaProtocol/main-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Aria Protocol 소개

Aria Protocol은 크라우드소싱 자금 조달을 통해 지적 재산(IP)의 부분 소유권을 활성화하고, IP 자산을 ERC20 토큰으로 토큰화하며, 이해 관계자에게 로열티를 분배하는 플랫폼입니다. Vault와 분배 계약을 사용하여 사용자가 IP에 투자하고, 부분 토큰을 청구하고, 보상을 얻을 수 있도록 합니다.

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

_검토 커밋 해시_ - [17c554e901f6c741003d74ae72db4fb2ef9662df](https://github.com/AriaProtocol/main-contracts/tree/17c554e901f6c741003d74ae72db4fb2ef9662df)

_수정 검토 커밋 해시_ - [d6060ae83ca2c4a25bc277f7a4a8ccbdfc87c7f8](https://github.com/AriaProtocol/main-contracts/tree/d6060ae83ca2c4a25bc277f7a4a8ccbdfc87c7f8)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `AriaIPVault`
- `AriaIPVaultStorage`
- `IAriaIPVault`
- `VaultAssetRegistryAdmin`
- `VaultFundraiseAdmin`
- `VaultWhitelistAdmin`
- `IVaultAdmin`
- `IVaultAdmin`
- `IVaultFundraiseAdmin`
- `VaultAdmin`
- `AriaIPVaultFactory`
- `IAriaIPVaultFactory`
- `IVaultFundraiseUser`
- `VaultFundraise`
- `VaultFundraiseStorage`
- `VaultStateChecker`
- `IVaultView`
- `VaultView`
- `Whitelist`
- `WhitelistStorage`

# 발견 사항

# [M-01] 잘못된 통합

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

`_registerIpAndAttachTermsAndCollectRoyaltyTokens()` 함수는 Story Protocol의 업데이트된 `mintAndRegisterIpAndAttachPILTermsAndDistributeRoyaltyTokens()` 함수와 잘못 통합되어 있습니다.

[링크](https://github.com/storyprotocol/protocol-periphery-v1/blob/main/contracts/workflows/RoyaltyTokenDistributionWorkflows.sol#L115-L122)

`mintAndRegisterIpAndAttachPILTermsAndDistributeRoyaltyTokens()`의 업데이트된 버전은 이제 `WorkflowStructs.LicenseTermsData[]` 배열을 입력으로 요구하고 `uint256[] memory licenseTermsIds`를 반환합니다. 그러나 현재 통합은 단일 `WorkflowStructs.LicenseTermsData`와 단일 `uint256 licenseTermsId`를 예상하므로 유형 불일치가 발생합니다.

구체적으로:
- `licenseTermsData`는 배열(`LicenseTermsData[]`)이어야 하지만 단일 구조체로 전달됩니다.
- `licenseTermsId`는 배열(`uint256[]`)이어야 하지만 반환 시 단일 `uint256`으로 처리됩니다.

이 불일치로 인해 DoS가 발생하고 IP를 등록하고 부분화할 수 없습니다.

## 권장 사항

- `_registerIpAndAttachTermsAndCollectRoyaltyTokens()`를 업데이트하여 `mintAndRegisterIpAndAttachPILTermsAndDistributeRoyaltyTokens()` 함수에 `LicenseTermsData[]` 배열을 전달하십시오.
- `licenseTermsId`의 반환 유형을 배열(`uint256[]`)을 처리하도록 조정하여 업데이트된 함수의 반환 값과 일치시키십시오.

## 수정

[PR 20: story protocol integration](https://github.com/AriaProtocol/main-contracts/pull/20/files)

# [L-01] 일관성 없는 스토리지 위치

`VaultFundraiseStorage` 및 `WhitelistStorage` 계약은 네임스페이스 기반 스토리지 위치(ERC-7201)를 계산하기 위한 올바른 규칙을 따르지 않아 계약 간에 불일치가 발생합니다.

- `VaultFundraiseStorage`에서는 공식이 따르지 않고 `bytes32` 값이 미리 계산되지 않습니다.
- 마찬가지로 `WhitelistStorage`에서는 스토리지 위치가 예상 공식으로 계산되지 않습니다.

이를 해결하려면 `VaultFundraiseStorage` 및 `WhitelistStorage`에서 네임스페이스 기반 스토리지 위치를 계산하기 위한 올바른 공식이 일관되게 따라야 합니다.

**수정:**

[PR 21: erc7201 storage location for Fundraise & Whitelist ](https://github.com/AriaProtocol/main-contracts/pull/21/files)

# [L-02] 불필요한 UUPS 업그레이드 가능 로직

`AriaIPVault` 계약은 `UUPSUpgradeable`을 상속하고 `_authorizeUpgrade`를 구현하지만, `AriaIPVaultFactory` 계약을 통해 배포된 `BeaconProxy`를 사용하며 `UpgradeableBeacon`을 가리키므로 이는 불필요합니다.

이 아키텍처에서 `BeaconProxy`는 비콘을 가리키며 업그레이드는 `UUPS` 업그레이드 기능이 아니라 비콘의 `upgradeTo` 메서드를 통해 처리되어야 합니다. `UUPSUpgradeable`의 존재는 불필요한 복잡성을 추가하고 업그레이드 관리 방식에 혼란을 줄 수 있습니다.

`BeaconProxy`는 자체 구현을 관리하지 않고 단순히 비콘에 위임하므로 `_authorizeUpgrade` 함수는 구현에 **아무런 영향을 미치지 않습니다**. 업그레이드 로직은 프록시가 아닌 비콘에 의해 제어됩니다.

이를 해결하려면 `AriaIPVault`에서 `UUPSUpgradeable` 상속 및 `_authorizeUpgrade` 메서드를 제거하고 초기화를 올바르게 처리하기 위해 `Initializable`만 사용하는 것을 고려하십시오.

**수정:**

[PR 32: use UUPS instead Beacon for AriaIPVault](https://github.com/AriaProtocol/main-contracts/pull/32/files)

# [L-03] 악의적인 관리자가 사용자의 토큰 인출을 차단할 수 있음

```solidity
    function updateFractionalTokenTotalSupply(uint256 newTotalSupply)
        external
        override
        onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE)
    {
        AriaIPVaultStorage.VaultLayout storage $ = AriaIPVaultStorage.load();
        VaultFundraiseStorage.FundraiseLayout storage $$ = VaultFundraiseStorage.load();

        if ($.vaultType == VaultType.Fundraise) {
            if (newTotalSupply < $$.totalDeposits[$$.setup.usdcContractAddress]) {
                revert Errors.AriaIPVault__FractionalTokenSupplyLessThanTotalDeposits(
                    newTotalSupply, $$.totalDeposits[$$.setup.usdcContractAddress]
                );
            }
        }
        if ($.fractionalToken != address(0)) {
            revert Errors.AriaIPVault__FractionalTokenAlreadyDeployed($.fractionalToken);
        }

        uint256 previousSupply = $.tokenDetails.fractionalTokenTotalSupply;
        $.tokenDetails.fractionalTokenTotalSupply = newTotalSupply;

        emit FractionalTokenTotalSupplyUpdated({previousTotalSupply: previousSupply, newTotalSupply: newTotalSupply});
    }
```

자금 조달이 완료된 후 악의적인 관리자가 `FractionalTokenTotalSupply`를 매우 큰 값으로 늘릴 수 있도록 허용하면 사용자가 토큰을 청구하려고 할 때 오버플로우가 발생할 수 있습니다. 이 오버플로우는 잘못된 계산이나 토큰 손실을 초래하여 잠재적으로 전체 토큰 분배 시스템을 중단시킬 수 있습니다.

전체 증가 제한을 설정하는 것이 좋습니다.

**수정:**

[PR 24: overflow when fractionalTokenTotalSupply is updated](https://github.com/AriaProtocol/main-contracts/pull/24/files)

# [L-04] 1 wei 입금으로 `updateUsdcContractAddress()`에 대한 DoS

`VaultFundraiseAdmin.updateUsdcContractAddress()`는 아직 입금이 이루어지지 않은 경우에만 관리자가 공개된 자금 조달에 대한 USDC 계약 주소를 업데이트할 수 있도록 허용합니다.

입금이 0일 때 이 함수를 호출하면 1 wei 입금으로 쉽게 프론트런하여 함수를 되돌릴 수 있습니다.

이를 방지하기 위해 다음 솔루션 중 하나를 구현하는 것이 좋습니다.
- 최소 입금액을 요구합니다.
- 되돌리기 조건을 제거합니다. `totalDeposits` 변수는 이전 및 새 USDC 계약 모두에 대한 입금을 설명하기 때문입니다. 이 경우 `claimRefund()` 및 `_fundraiseCalculateClaim()` 함수도 서로 다른 USDC 계약 주소를 설명하도록 조정해야 합니다.

**수정:**

[PR 30: account for many USDC used of fundraise lifetime](https://github.com/AriaProtocol/main-contracts/pull/30/commits)

# [L-05] 볼트에 토큰이 여러 개 있는 경우 `VaultFundraiseAdmin.withdraw()`가 되돌림

`VaultFundraiseAdmin.withdraw()`는 자금 조달이 끝난 후 볼트에 입금된 모든 USDC를 인출하기 위해 관리자가 호출할 수 있습니다. 이 함수는 볼트에 등록된 모든 USDC 주소를 반복하고 잔액을 자금 수령인에게 전송합니다.

```solidity
	uint256 length = $.tokensInVault.length();

	tokens = new address[](length);
	withdrawnAmounts = new uint256[](length);
	for (uint256 i = 0; i < length; i++) {
		tokens[i] = $.tokensInVault.at(i);
		withdrawnAmounts[i] = IERC20(tokens[i]).balanceOf(address(this));
@>		$.tokensInVault.remove(tokens[i]);
@>		IERC20(tokens[i]).safeTransfer($.setup.fundReceiver, withdrawnAmounts[i]);
	}
```

이 구현에는 두 가지 문제가 있습니다.

1) 각 반복에서 현재 토큰이 `tokensInVault` 열거형 집합에서 제거되어 다른 토큰의 인덱스가 변경되고 "배열 경계 초과 액세스" 오류가 발생하여 볼트의 모든 토큰이 잠기게 됩니다. 예를 들어 볼트에 두 개의 토큰이 있는 경우 인덱스 0의 토큰이 제거되어 집합에 하나의 토큰만 남습니다. 다음 반복에서는 더 이상 존재하지 않는 인덱스 1의 토큰에 액세스하려고 시도합니다.

2) 각 반복에서 토큰이 자금 수령인에게 전송되므로 전송이 실패하면 나머지 토큰을 인출할 수 없습니다.

현재 구현에서는 `tokensInVault` 집합에 하나 이상의 토큰을 추가할 수 없습니다. 그러나 계약이 업그레이드 가능하므로 향후 변경될 수 있습니다.

다음 중 하나를 수행하는 것이 좋습니다.

- `tokensInVault` 변수를 제거하고 항상 `setup.usdcContractAddress` 변수를 대신 사용합니다. 또는
- 향후 볼트에 여러 토큰이 있을 것으로 예상되는 경우 `withdraw()` 함수에서 토큰 주소를 매개변수로 받아 각 토큰을 별도로 인출할 수 있도록 합니다. 이 경우 `claimRefund()` 및 `_fundraiseCalculateClaim()` 함수도 서로 다른 USDC 계약 주소를 설명하도록 조정해야 합니다.

**수정:**

[PR 30: account for many USDC used of fundraise lifetime](https://github.com/AriaProtocol/main-contracts/pull/30/commits)

# [L-06] VaultFundraise 사용자는 입금을 취소할 수 없음

```solidity
    function deposit(address erc20, uint256 amount) external virtual override nonReentrant {
        VaultFundraiseStorage.FundraiseLayout storage $ = VaultFundraiseStorage.load();

        $._checkAndUpdateState();
        if (amount == 0) revert Errors.AriaIPVault__ZeroDepositAmount(msg.sender, erc20);
        if (erc20 != $.setup.usdcContractAddress) revert Errors.AriaIPVault__InvalidUSDCAddress();
        if ($.state != FundraiseState.Open) revert Errors.AriaIPVault__VaultNotOpen($.state);

        $.tokensInVault.add(erc20);
        $.deposits[msg.sender][erc20] += amount;
        $.totalDeposits[erc20] += amount;

        IERC20(erc20).safeTransferFrom(msg.sender, address(this), amount);

        emit DepositReceived({depositor: msg.sender, token: erc20, amount: amount});
    }
```

VaultFundraise 시스템에서 사용자가 자금을 입금하면 관리자가 볼트를 닫거나 취소할 때까지 기다려야만 자금을 인출할 수 있습니다. 그러나 자금 조달 과정에서 총 토큰 공급과 같은 볼트 매개변수가 변경될 수 있습니다. 사용자는 이러한 변경 사항에 따라 입금을 취소할 수 없으며, 캠페인이 끝나기 전에 자금을 인출하고 싶거나 자금 조달 매개변수에 예기치 않은 변경이 있는 경우 문제가 발생할 수 있습니다.

이러한 유연성 부족은 특히 사용자가 볼트의 조건 변경으로 인한 잠재적 손실을 피하려는 경우 문제가 될 수 있습니다.

권장 사항:

자금 조달 기간 동안 사용자가 입금을 취소할 수 있도록 입금 취소 기능을 구현하는 것이 좋습니다.

# [L-07] 사용자는 자금 조달에서 부분 IP에 대해 관리자를 신뢰해야 함

자금 조달의 경우 IP 등록 및 부분화는 자금 조달이 완료된 후에만 관리자에 의해 실행됩니다. 즉, 자금 조달 참여자는 관리자가 IP를 등록하고 부분화하며 올바른 데이터로 수행할 것을 신뢰해야 합니다. 등록이 발생하지 않더라도 입금된 자금을 회수할 수 있는 가능성이 없기 때문입니다.

**권장 사항:**

볼트 생성이 허가 없이 이루어지므로 자금 조달 생성 시 관리자가 IP를 등록하고 부분화하도록 요구하는 것이 좋습니다. 그렇지 않으면 사용자가 위험을 인지하도록 관리자를 신뢰하지 않는 경우 자금 조달에 자금을 입금하지 말아야 한다고 문서에 명시적으로 기재하는 것이 좋습니다.

또한 관리자가 자금을 인출하도록 허용하기 전에 FractionalToken을 올바르게 설정할 수 있습니다.

```diff
    function withdraw()
        external
        override
        onlyRole(AccessControlStorage.DEFAULT_ADMIN_ROLE)
        returns (address[] memory tokens, uint256[] memory withdrawnAmounts)
    {
        VaultFundraiseStorage.FundraiseLayout storage $ = VaultFundraiseStorage.load();
+       AriaIPVaultStorage.VaultLayout storage $ = AriaIPVaultStorage.load();
+       if ($$.fractionalToken == address(0)) revert Errors.AriaIPVault__FractionalTokenNotSet();

```

**수정:**

[PR 28: users trust for IP and factory admin only](https://github.com/AriaProtocol/main-contracts/pull/28/commits)

# [L-08] 부분 토큰 인출을 위한 결함 있는 메커니즘

`VaultAdmin`은 관리자에게 볼트에서 부분 토큰을 인출하기 위한 2단계 메커니즘을 제공합니다. 이는 일정 기간 후 청구되지 않은 부분 토큰과 부분 토큰 계산의 반올림 오류로 인한 먼지 잔여물을 인출하는 데 사용될 수 있습니다.

그러나 `initFractionalTokenWithdrawal()` 및 `execFractionalTokenWithdrawal()` 함수는 부분 토큰이 볼트 소유여야 한다고 가정하지만 그렇지 않습니다. 볼트는 부분 토큰을 소유하지 않고 대신 부분 토큰의 주조자입니다. 즉, 부분 토큰을 회수하려면 볼트의 잔액을 전송하는 대신 토큰을 주조해야 합니다.

**권장 사항:**

볼트의 잔액에서 전송하는 대신 부분 토큰을 주조하도록 `initFractionalTokenWithdrawal()` 및 `execFractionalTokenWithdrawal()` 함수의 구현을 변경하십시오.

**수정:**

[PR 25: flawed mechanism for admin fractional token withdrawal/mint](https://github.com/AriaProtocol/main-contracts/pull/25/commits/bae1fa3aa4acba5e4ba2d197e103bfb3b37cc80f)

# [L-09] 자금 조달 관리자가 모든 예금자에게 부분 토큰을 거부할 수 있는 권한을 가짐

자금 조달 관리자는 부분화와 같은 중요한 볼트 작업을 실행하는 임무를 맡은 시스템에서 신뢰할 수 있는 유일한 역할입니다. 또한 부과된 코드 제한에 따라 자금 조달 상태, 주소, 타임락 등과 같은 구성을 변경할 수 있습니다. 그러나 그가 허용되어서는 안 되는 것은 부분 토큰을 기대하는 예금자에게 피해를 주는 것입니다.
관리자가 만료 전에 `closeRaise()`를 통해 자금 조달을 원활하게 닫을 수 있는 권한으로 인해 다음 시나리오가 가능합니다.

1. 현재 입금 토큰에 대해 충분히 큰 플래시 대출을 받습니다.
2. 자신의 이름으로 거액의 입금을 하여 총 입금액을 부풀립니다.
3. `closeRaise()`를 통해 자금 조달을 닫습니다.
4. 소수점에 따라 대부분 또는 모든 부분 토큰을 청구하여 예금자에게 아무것도 남기지 않습니다.
5. `VaultFindraiseAdmin#withdraw()`를 통해 자신의 입금과 정직한 사용자의 입금을 회수하고 플래시 대출을 상환합니다.

이 모든 것은 단일 트랜잭션으로 실행될 수 있습니다. 관리자는 사용자의 보상을 변조할 수 없어야 합니다.
가장 간단한 수정은 공격을 비원자적으로 만들고 플래시 대출을 방해하기 위해 `closeRaise()` 및 `cancelRaise()` 함수에도 짧은 타임락을 도입하는 것입니다.

# [L-10] 공개적으로 주조 가능한 SPG NFT에 대해 함수가 되돌려질 수 있음

볼트 관리자는 레지스트리를 통해 모든 SPG NFT를 부분화하고 IP(지적 재산)로 등록하고 조건을 첨부하고 최대 로열티를 징수하여 나중에 분배할 수 있습니다. 이 부분화된 NFT는 자금 조달 중에 입금하거나 화이트리스트의 일부가 되어 사용자가 상환하는 토큰입니다.
`_registerIpAndAttachTermsAndCollectRoyaltyTokens()` 함수는 내부적으로 Story의 배포 워크플로우에서 `mintAndRegisterIpAndAttachPILTermsAndDistributeRoyaltyTokens`를 호출하고 `allowDuplicates`를 false로 설정합니다. 그런 다음 배포 워크플로우는 `_mintAndRegisterIp`를 호출하여 갓 주조된 NFT 등록을 시도합니다.
일부 SPG NFT는 공개적으로 주조 가능하므로 문제가 될 수 있습니다.

```solidity
    function mint(
        address to,
        string calldata nftMetadataURI,
        bytes32 nftMetadataHash,
        bool allowDuplicates
    ) public virtual returns (uint256 tokenId) {
        //@audit if public minting is enabled, we can mint any metadata hash
        if (!_getSPGNFTStorage()._publicMinting && !hasRole(SPGNFTLib.MINTER_ROLE, msg.sender)) {
            revert Errors.SPGNFT__MintingDenied();
        }
        tokenId = _mintToken({
            to: to,
            payer: msg.sender,
            nftMetadataURI: nftMetadataURI,
            nftMetadataHash: nftMetadataHash,
            allowDuplicates: allowDuplicates
        });
    }
```

이것은 Story의 멤풀이 공개되어 있다는 사실과 결합되어 임의의 사용자가 동일한 메타데이터 해시로 SPG를 직접 주조하여 허용되지 않는 중복으로 인해 되돌리기를 유발할 수 있습니다.

```solidity
        tokenId = $._nftMetadataHashToTokenId[nftMetadataHash];
        if (!allowDuplicates && tokenId != 0) {
            revert Errors.SPGNFT__DuplicatedNFTMetadataHash({
                spgNftContract: address(this),
                tokenId: tokenId,
                nftMetadataHash: nftMetadataHash
            });
        }


        if ($._mintFeeToken != address(0) && $._mintFee > 0) {
            IERC20($._mintFeeToken).safeTransferFrom(payer, address(this), $._mintFee);
        }


        tokenId = ++$._totalSupply;
        if ($._nftMetadataHashToTokenId[nftMetadataHash] == 0) {
            // only store the token ID if the metadata hash is not used
            $._nftMetadataHashToTokenId[nftMetadataHash] = tokenId;
        }
```

권장 사항:

분명히 `VaultAssetRegistryAdmin`에 유효한 주조자 역할이 없는 한 상호 작용할 수 있는 유일한 SPG는 공개적으로 주조 가능한 것입니다.

```solidity
    modifier onlyMintAuthorized(address spgNftContract) {
        if (
            !ISPGNFT(spgNftContract).hasRole(SPGNFTLib.MINTER_ROLE, msg.sender) &&
            !ISPGNFT(spgNftContract).publicMinting()
        ) revert Errors.Workflow__CallerNotAuthorizedToMint();
        _;
    }
}
```

따라서 여기서 가장 좋은 해결책은 중복을 허용하는 것입니다: `allowDuplicates: true`

# [L-11] `fractionalTokenTotalSupply`가 `totalDeposits`보다 작을 수 있음

Fundraise Vault 내에 부분 토큰이 배포되면 `fractionalTokenTotalSupply`가 `totalDeposits`보다 큰지 확인합니다.

```solidity
function _deployFractionalToken(
        address ipId,
        address fractionalTokenTemplate
    ) internal returns (address fractionalToken) {
        AriaIPVaultStorage.VaultLayout storage $ = AriaIPVaultStorage.load();
        VaultFundraiseStorage.FundraiseLayout storage $$ = VaultFundraiseStorage.load();

        if ($.vaultType == VaultType.Fundraise) {
@>          if ($.tokenDetails.fractionalTokenTotalSupply < $$.totalDeposits[$$.setup.usdcContractAddress]) {
                revert Errors.AriaIPVault__FractionalTokenSupplyLessThanTotalDeposits(
                    $.tokenDetails.fractionalTokenTotalSupply,
                    $$.totalDeposits[$$.setup.usdcContractAddress]
                );
            }
        }
        
      ///code...
    }
```

문제는 다른 토큰 소수점의 가능성을 설명하지 않는다는 것입니다. 예를 들어, 부분 토큰은 18 소수점(표준 ERC20 토큰이므로)을 갖지만 USDC는 6 소수점만 갖습니다.

소수점이 적절하게 조정되지 않으므로 `fractionalTokenTotalSupply`가 `totalDeposits`보다 작을 수 있습니다. 이 불일치로 인해 사용자가 의도한 것보다 적은 부분 토큰을 받게 되어 사용자에게 자금 손실이 발생할 수 있습니다.

문제를 더 잘 이해하기 위해 예를 들어보겠습니다. Fundraise Vault가 완료되었고 입금이 USDC이며 `totalDeposits = 10_000e6`이고 `fractionalTokenTotalSupply = 1e18`이라고 가정합니다.

1. 부분 토큰이 배포됩니다.
2. `$.tokenDetails.fractionalTokenTotalSupply < $$.totalDeposits[$$.setup.usdcContractAddress]` 확인이 수행되고 `fractionalTokenTotalSupply > totalDeposits`, 즉 `1e18 > 10_000e6`이므로 통과합니다. 그러나 이는 소수점이 다른 토큰을 비교하므로 잘못된 것입니다.

올바른 접근 방식은 USDC 금액을 조정하는 것입니다. 이 경우 `10_000e6 * 1e12`(18 - 6)는 `10_000e18`이 됩니다. 이 조정을 사용하면 `10_000e18 > 1e18`이므로 확인이 실패합니다.

**권장 사항:**

이 문제를 해결하려면 올바른 비교를 보장하기 위해 토큰 소수점을 조정하십시오.

**수정:**

[PR 26: fractional supply and total deposits compared on same decimals](https://github.com/AriaProtocol/main-contracts/pull/26/commits/034ba7b5788067031b670e1b65f90f877469774b)

