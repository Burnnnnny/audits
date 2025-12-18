# 소개

**pashov**가 **Pump** 프로토콜에 대해 시간 제한이 있는 보안 검토를 수행했으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# **pashov** 소개

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. 그의 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# **Pump** 소개

Pump는 ERC20 토큰 출시 플랫폼입니다. 토큰 판매 및 구매는 플랫폼을 통해 이루어질 수 있으며, 플랫폼 자체는 오프체인 오더북과 온체인 정산을 사용합니다. 새 토큰 공급량 중 5%는 관리자가 제어하는 주소로 이동하고 95%는 제작자에게 1년에 걸쳐 베스팅(vesting)됩니다.

## 관찰 사항

플랫폼에서 생성된 토큰은 `transferFrom`의 호출자가 `PumpV1` 계약인 경우 지갑에서 전송되기 위해 허용(allowance)이 필요하지 않습니다.

## 특권 역할 및 행위자

- Token Factory - 허용 없이 지갑에서 프로젝트 출시 토큰을 전송할 수 있으며, `PumpV1` 계약이 보유해야 합니다.
- Pump Owner - 각 주문 이행 시 수수료를 받으며, `marketMaker` 주소, 오더북의 활성 상태, `feeRate` 및 `vestingDuration`을 변경할 수 있습니다.
- Market Maker - 생성된 각 새 토큰 공급량의 5%를 받습니다.
- Token Creator - `vest` 메서드를 통해 시간이 지남에 따라 토큰 공급량의 95%를 베스팅받으며, 토큰 이름과 심볼도 그의 주소를 기반으로 합니다.
- Order maker - `Order` 페이로드에 서명하고, 주문 이행 시 토큰이 전송되며, 주문을 취소할 수 있습니다.
- Order recipient - 주문 이행 시 토큰을 받습니다.
- Order taker - 매수/매도 주문을 실행하기 위해 `fulfill`을 호출합니다.

# 심각도 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

**영향(Impact)** - 성공적인 공격의 기술적, 경제적, 평판적 손해
**가능성(Likelihood)** - 특정 취약점이 발견되고 악용될 확률
**심각도(Severity)** - 위험의 전반적인 중요도

# 보안 평가 요약

**_검토 커밋 해시_ - [3f6454471a27ccde967e9b73a60006ce8af7267f](https://github.com/pump-fun/pump-v1/tree/3f6454471a27ccde967e9b73a60006ce8af7267f)**

**_수정 검토 커밋 해시_ - [93753080ad8e9dded5d741413e1de4a1e7105fc7](https://github.com/pump-fun/pump-v1/tree/93753080ad8e9dded5d741413e1de4a1e7105fc7)**

### 범위

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `Token`
- `PumpV1`

---

# 결과 요약

| ID | 제목 | 심각도 | 상태 |
| --- | --- | --- | --- |
| [M-01] | 프로토콜이 일반적으로 사용되는 ERC20 토큰과 호환되지 않음 | 중간 | 수정됨 |
| [M-02] | 주문 이행 전에 수수료를 100%로 설정할 수 있음 | 중간 | 수정됨 |
| [L-01] | 베스팅 기간 설정자에 대한 입력 검증 없음 | 낮음 | 수정됨 |
| [L-02] | 취소가 프론트러닝 될 수 있음 | 낮음 | 인지함 |

# 상세 결과

# [M-01] 프로토콜이 일반적으로 사용되는 ERC20 토큰과 호환되지 않음

## 심각도

**영향:**
중간, 가치 손실을 초래할 가능성은 낮지만 프로토콜 사용성을 제한함

**가능성:**
중간, 일반적으로 사용되는 일부 토큰과는 작동하지 않지만 다른 토큰과는 작동함

## 설명

다음은 `PumpV1::fulfill`의 코드 스니펫입니다:

```solidity
if (order.isBuy) {
    ERC20(order.baseToken).transferFrom(order.maker, msg.sender, baseTokenAmount - fee);
    ERC20(order.baseToken).transferFrom(order.maker, owner(), fee);
    Token(order.assetToken).transferFrom(msg.sender, order.recipient, amount);
} else {
    ERC20(order.baseToken).transferFrom(msg.sender, order.recipient, baseTokenAmount - fee);
    ERC20(order.baseToken).transferFrom(msg.sender, owner(), fee);
    Token(order.assetToken).transferFrom(order.maker, msg.sender, amount);
}
```

코드는 `ERC20` 구현(`solady` 종속성에서 가져온 `ERC20`)의 `transferFrom` 메서드를 호출하고 있으며, 이는 `bool` 반환 값을 기대합니다. `USDT`, `BNB` 등과 같은 토큰은 `transfer` 및 `transferFrom`에서 반환 값이 누락되어 있어 애플리케이션과의 통합을 깨뜨릴 수 있습니다. 또한 `EURS`와 같이 전송 실패 시 되돌아가지(revert) 않고 `false` 부울 값을 반환하는 토큰도 있습니다. 이러한 토큰에 대한 자세한 내용은 [여기](https://github.com/d-xo/weird-erc20)에서 읽을 수 있습니다.

또 다른 문제는 `PumpV1`의 `fulfill` 메서드에서 `baseTokenAmount`와 `fee`를 계산하는 수학이 `baseTokenAmount`와 `fee`를 계산할 때 wad 기반 수학을 사용하기 때문에 `baseToken` 소수점(decimals)이 정확히 18이어야 한다는 점에 의존한다는 것입니다. 일반적으로 사용되는 많은 토큰은 18 미만의 소수점을 가지며(`USDC`와 `USDT`는 6 소수점), 일부 토큰은 18 이상의 소수점을 가집니다(`YAM-V2`는 24 소수점). 낮은 소수점 토큰 트랜잭션은 기껏해야 되돌아가겠지만, 높은 소수점 토큰 트랜잭션은 사용자가 의도한 것보다 더 많은 토큰을 소비하게 할 수 있습니다.

## 권장 사항

OpenZeppelin의 `SafeERC20` 라이브러리와 `safeTransferFrom` 메서드를 사용하여 이러한 토큰을 처리하십시오. 또한 모든 토큰 금액을 18 소수점으로 조정하거나 `fulfill`에서 명시적 검사를 통해 18 소수점이 아닌 토큰 사용을 금지하십시오.

# [M-02] 주문 이행 전에 수수료를 100%로 설정할 수 있음

## 심각도

**영향:**
높음, 주문 수령자가 토큰 대가로 아무것도 받지 못할 수 있음

**가능성:**
낮음, 악의적이거나 타협된 관리자가 필요함

## 설명

`PumpV1`의 `setFeeRate` 메서드는 현재 `_feeRate` 매개변수에 대한 입력 검증이 없습니다. 주어진 값이 `1e18`이면 수수료가 100%로 설정됩니다. 관리자는 블록체인의 대기 트랜잭션 풀을 모니터링하여 `fulfill` 호출을 볼 수 있으며, 수수료를 100%로 설정하여 프론트러닝함으로써 주문 수령자가 받아야 할 모든 토큰을 본질적으로 훔칠 수 있습니다.

## 권장 사항

수수료율에 최대 값(예: 3%)을 설정하여 제한하십시오.

# [L-01] 베스팅 기간 설정자에 대한 입력 검증 없음

`PumpV1`의 `setVestingDuration` 메서드에는 입력 검증이 없으므로 프로토콜 소유자가 `vestingDuration`으로 임의의 값을 설정할 수 있습니다. 여기에는 두 가지 잠재적인 문제가 있습니다. 0 값을 사용하면 0으로 나누기가 트랜잭션을 되돌리므로 프로토콜의 DoS로 이어질 수 있으며, 너무 큰 값을 사용하면 사용자의 베스팅 비율이 0이 될 수 있습니다. `vestingDuration` 값에 대한 하한 및 상한을 구현하십시오.

# [L-02] 취소가 프론트러닝 될 수 있음

`PumpV1` 계약은 주문 작성자가 자신의 주문을 취소할 수 있는 `cancel` 메서드를 구현합니다. 문제는 누구나 `cancel` 호출을 볼 수 있고 `fulfill` 호출로 트랜잭션을 프론트러닝하여 본질적으로 주문을 실행하고 주문 작성자가 취소하지 못하게 할 수 있다는 것입니다. 문서에 이를 추가하거나 주문 취소를 위한 다른 접근 방식을 허용하는 것을 고려하십시오.

