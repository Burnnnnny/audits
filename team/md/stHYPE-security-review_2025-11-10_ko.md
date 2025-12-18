
# Pashov Audit Group 소개

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 입증된 인재들입니다. 대부분은 공개 대회 보상으로 10만 달러 이상을 획득했거나, 다회 우승자이거나, 저희와의 감사에서 진정으로 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도우면서, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

<p><strong>ValantisLabs/sthype-contracts</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, <strong>Shurikenzer, 0xAlix2, 0xunforgiven</strong>이 <strong>stHYPE</strong> 검토에 참여했습니다. 총 <strong>4</strong>개의 이슈가 발견되었습니다.</p>

# stHYPE 소개

<p>stHYPE는 Hyperliquid HyperEVM을 위해 구축된 유동성 스테이킹 프로토콜(Liquid Staking Protocol)로, 사용자가 네이티브 HYPE 토큰을 스테이킹하고 stHYPE를 받을 수 있게 합니다. stHYPE는 거래 가능한 상태를 유지하면서 보상을 축적하는 유동성 스테이킹 파생 상품입니다. 이는 Overseer 계약을 통한 모듈식 스테이킹, 리베이스(rebasing) stHYPE 토큰, 그리고 DeFi 통합을 위한 랩핑된 비리베이스(wrapped non-rebasing) wstHYPE 버전을 특징으로 합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [0661f1972e3d02fcd923d1584022db5fa4ad621f](https://github.com/ValantisLabs/sthype-contracts/tree/0661f1972e3d02fcd923d1584022db5fa4ad621f)<br>&nbsp;&nbsp;(ValantisLabs/sthype-contracts)

**수정 검토 커밋 해시:**<br>• [0478fc5a8a62da4f8e46ca0ef0d4030e4723abbe](https://github.com/ValantisLabs/sthype-contracts/tree/0478fc5a8a62da4f8e46ca0ef0d4030e4723abbe)<br>&nbsp;&nbsp;(ValantisLabs/sthype-contracts)

# 범위

- `StakingModuleExternalManagement.sol`

# 발견 사항

# [L-01] `StakingModuleExternalManagement`가 `depositCap` 및 `amountDeposited`를 노출하지 않음 (`StakingModuleExternalManagement` does not expose `depositCap` and `amountDeposited`)

_해결됨_

`StakingModuleExternalManagement`는 내부 저장소 변수 `depositCap` 및 `amountDeposited`를 정의하지만 이에 대한 공개 또는 외부 getter 함수를 제공하지 않습니다.

**권장 사항:**
두 필드를 모두 노출하는 view 함수를 추가하는 것을 고려하십시오.

# [L-02] ExternalManagement 모듈이 HyperCore StakingModule과 동일한 저장소 슬롯을 사용함 (ExternalManagement module uses the same storage slot as HyperCore StakingModule)

_해결됨_

`StakingModuleExternalManagement`는 `HyperCoreStakingModule`에서도 사용하는 동일한 ERC-7201 네임스페이스(`"stHYPE.storage.StakingModule"`) 아래에 저장소를 정의합니다.
이는 각 네임스페이스가 다른 네임스페이스와의 충돌을 피해야 한다는 EIP-7201의 요구 사항을 위반합니다.

**권장 사항:**

적절한 저장소 격리를 유지하기 위해 `StakingModuleExternalManagement`에 대해 고유한 네임스페이스를 사용하는 것을 고려하십시오.

# [L-03] 리베이스에 급격한 잔액 하락에 대한 보호 기능이 없음 (Rebase does not have protection for sudden balance drops)

_인정됨_

스테이크 계정(stake account)은 신뢰되지만 때로는 `rebase()` 호출이 지연되어 계정 잔액이 최신 상태가 아닐 때 실행될 수 있습니다.
이로 인해 시스템은 잔액이 실제로 그렇지 않은데도 많이(예: N%) 떨어졌다고 생각하게 되어 리베이스 계산을 망칠 수 있습니다.
`Overseer.sol`에서:
```solidity
function rebase() external onlyRole(REBASER) {
    ...
    uint256 newSupply = getNewSupply();
    ...
}
```

`getNewSupply()`는 모든 스테이킹 모듈의 잔액을 집계합니다:
```solidity
    function getNewSupply() public view returns (uint256) {
        ...
            totalBalance += IStakingModule(stakingModule).getTotalBalance();
        ...
    }
```

그리고 `StakingModuleExternalManagement.sol`에서 `getTotalBalance()`는 외부 `stakeAccount`에 의존하는데, 이는 항상 올바른 현재 잔액을 반영하지 않을 수 있습니다:
```solidity
    function getTotalBalance() external view override returns (uint256) {
        address stakeAccount = _getStakingModuleStorage().stakeAccount;
        // WARNING: This will be temporarily incorrect if either:
        // - stake account has not deposited the received HYPE into its staking balance (see `deposit()`)
        // - the stake account's stake balance has been unstaked into spot balance,
        //   but not yet sent to this contract's HyperCore spot balance (see `requestWithdraw()`)
        // It is important that `manager` (Overseer) does not call `rebase()` in these scenarios,
        // so that stHYPE supply does not get adjusted incorrectly.
        return address(this).getHypeSpotBalance() * E10 + address(this).balance
            + stakeAccount.getHypeStakingBalance() * E10;
    }
```
권장 사항:
관리자가 특수 플래그를 사용하여 수동으로 허용하지 않는 한, 잔액이 갑자기 너무 많이 떨어지면 리베이스를 중지하는 확인을 추가하십시오.

# [L-04] `amountDeposited`가 감소하지 않아 새 입금을 차단함 (`amountDeposited` is never decremented, blocking new deposits)

_인정됨_

`deposit()`에서 모듈은 예치된 금액만큼 `_getStakingModuleStorage().amountDeposited`를 증가시키지만 자금이 인출될 때 이를 감소시키지 않습니다. 입금 한도 확인은 누적 총계와 비교하므로 한도에 도달하면 관리자가 자금을 인출했더라도 모든 향후 입금이 되돌려집니다.

```solidity
if (_getStakingModuleStorage().amountDeposited + amountEvm > _getStakingModuleStorage().depositCap) {
    revert StakingModuleExternalManagement__deposit_ExceedsDepositCap();
}

// Update the amount deposited (measured in EVM decimals)
_getStakingModuleStorage().amountDeposited += amountEvm;
```

이 동작은 용량을 다시 사용할 수 있더라도 한도에 도달한 후 모듈이 새 입금을 수락하지 못하도록 효과적으로 잠급니다.

**권장 사항**

인출이 이행될 때마다 `amountDeposited`를 감소시키는 것을 고려하십시오.
