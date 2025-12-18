# 소개

Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100%의 보안을 보장할 수는 없지만, 숙련된 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾기 위해 시도합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**bob-collective/optimism** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# BOB USDC Bridge 소개

BOB은 비트코인과 이더리움을 기반으로 하는 하이브리드 레이어-2입니다. 이 디자인은 비트코인 사용자가 이더리움 자산을 미리 보유하지 않고도 BOB L2에 쉽게 온보딩할 수 있도록 합니다. 사용자는 신뢰할 수 있는 릴레이어(relayer)와 협력하여 사용 가능한 유동성의 일부를 예약하고, 비트코인 메인넷에서 BTC를 보낸 다음, 릴레이어가 머클 증명을 제공하여 BOB에서 ERC20 토큰으로 스왑을 실행할 수 있습니다. 유동성 제공자(LP)는 먼저 Onramp.sol에 해당 토큰과 사용자가 BOB에서 스왑을 수행할 수 있도록 소량의 ETH를 잠급니다. LP는 지정된 주소로 비트코인을 받고, 이를 래핑된 토큰으로 변환하여 다시 예치함으로써 재조정할 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 심각한 물적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물적 손실을 초래하거나 사용자 그룹에 적당한 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 공격 경로가 온체인 조건을 모방하는 합리적인 가정 하에 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 있습니다.
- 낮음 - 가정이 너무 많거나 희박하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (배포 전, 이미 배포되지 않은 경우)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [4c27e88204aaa8dc531b3ff1fdd5b4e8ec85d056](https://github.com/bob-collective/optimism/tree/4c27e88204aaa8dc531b3ff1fdd5b4e8ec85d056)

_수정 검토 커밋 해시_ - [c9648bed367881438e782bf9e7de9dc70fa50a29](https://github.com/bob-collective/optimism/tree/c9648bed367881438e782bf9e7de9dc70fa50a29)

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `IPartialUsdc`
- `L1UsdcBridge`
- `L2UsdcBridge`
- `UsdcBridge`
- `Pausable`
- `UsdcManager`

# 발견 사항

# [L-01] 브릿지된 USDC 표준이 완전히 준수되지 않음

[문서 링크](https://github.com/circlefin/stablecoin-evm/blob/master/doc/bridged_USDC_standard.md#2-ability-to-burn-locked-usdc)

브릿지된 USDC 표준은 브릿지가 잠긴 토큰을 소각할 수 있는 기능을 보유해야 한다고 명시합니다.

> 공급 잠금에 의해 설정된 브릿지된 USDC의 유통 총 공급량에 정확히 해당하는 브릿지가 보유한 USDC 양을 소각하십시오.

그러나 현재 구현은 브릿지되지 않은 토큰(예: 실수로 전송됨)을 포함하여 L1 브릿지가 보유한 모든 토큰을 소각합니다. 이러한 불일치는 L1과 L2의 공급량 차이를 초래할 수 있습니다.

```solidity
    function burnLockedUSDC() external {
        require(msg.sender == burner, "Not whitelisted");

        IPartialUsdc token = IPartialUsdc(l1Usdc);
>>      uint256 balance = token.balanceOf(address(this));
        token.burn(balance);
    }
```

잠긴 토큰만 소각하는 것을 고려하십시오:

```diff
+       uint256 balance = deposits[l1Usdc][l2Usdc];
        token.burn(balance);
```

