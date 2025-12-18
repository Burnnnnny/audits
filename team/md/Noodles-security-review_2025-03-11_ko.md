# 정보
Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항
스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개
**noodles-fun/contracts** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Noodles 정보

Noodles.Fun은 본딩 커브(bonding curve)를 사용하여 Twitter (X) 계정을 거래 가능한 토큰으로 전환하여 사용자가 이를 구매, 판매하고 샤아웃(shoutout) 또는 고정 트윗과 같은 프로모션에 사용하여 핵심 오피니언 리더(KOL)와 더 깊이 참여할 수 있도록 합니다. 각 계정의 토큰은 자체 본딩 커브에서 작동하여 유동성 풀이나 오더북 없이 즉시 거래가 가능하며, 크리에이터는 토큰을 환매하고 소각하여 ETH를 얻고 토큰 가치를 높일 수 있습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 상당한 피해를 줍니다.

- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 가능성이 희박하거나 공격자가 인센티브가 거의 없거나 전혀 없이 상당한 지분을 투자해야 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적 - 가능한 한 빨리 수정해야 함(이미 배포된 경우).

- 높음 - 수정해야 함(아직 배포되지 않은 경우 배포 전).

- 중간 - 수정해야 함.

- 낮음 - 수정할 수 있음.

# 보안 평가 요약
_검토 커밋 해시_ - [f276d0ddf8a21ce02fa8641a2e46ff8fc3c6db74](https://github.com/noodles-fun/contracts/tree/f276d0ddf8a21ce02fa8641a2e46ff8fc3c6db74)

_수정 검토 커밋 해시_ - [980ad2f02c22599e966a940c9c47ff04e03af6f1](https://github.com/noodles-fun/contracts/tree/980ad2f02c22599e966a940c9c47ff04e03af6f1)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `VisibilityCredits` 
- `VisibilityServices` 

# 발견 사항
# [M-01] 파트너 및 추천인에 대한 잘못된 보너스 비율

## 심각도

**영향:** 낮음

**가능성:** 높음

## 설명

`VisibilityCredits` 계약에서 `PARTNER_FEE` 및 `PARTNER_REFERRER_BONUS`는 250ppm(0.025%)으로 잘못 설정되어 있습니다. 그러나 계약의 주석에 따르면 이러한 값은 0.25%로 설정되어야 합니다. 이 잘못된 구성으로 인해 파트너와 추천인은 구매한 크레딧의 총 비용에서 의도한 것보다 적은 보너스를 받게 됩니다.

```solidity
uint16 public constant PARTNER_FEE = 250; 
// 0.25% bonus for the partner/marketing agency 
// if linked to a referrer (deduced from protocol fee)
uint16 public constant PARTNER_REFERRER_BONUS = 250; 
// 0.25% bonus for the referrer 
// if linked to a partner (deduced from protocol fee)
```

## 권장 사항

의도한 값과 일치하도록 `PARTNER_FEE` 및 `PARTNER_REFERRER_BONUS` 값을 2500ppm(0.25%)으로 업데이트하십시오.

# [M-02] sellCredits()에 대한 슬리피지 보호 누락

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명

크레딧 토큰을 거래하는 사용자는 봇에 의한 슬리피지에 취약합니다. VisibilityCredits.sol은 본딩 커브를 사용하므로 총 공급량이 증가하고 감소함에 따라 크레딧 토큰의 가격이 증가하고 감소합니다.

**판매 시 슬리피지:**
 - Alice가 sellCredits() 트랜잭션을 제출합니다.
 - 트랜잭션이 한동안 멤풀에 남아 있습니다.
 - 이 기간 동안 다른 sellCredits() 트랜잭션이 실행되어(다른 사용자가 의도치 않게 또는 봇이 의도적으로), 거래 비용이 감소합니다.
 - Alice의 트랜잭션이 진행되지만 의도한 것보다 적은 네이티브 토큰을 받습니다.

## 권장 사항

사용자에게 판매 시 `minAmountOut` 매개변수를 전달할 수 있는 옵션을 제공하십시오. 거래 비용을 이 매개변수와 확인하여 사용자가 의도한 대로 받을 수 있도록 하십시오.

# [L-01] 요청 수명 주기 동안 요청 데이터 크기에 대한 제한 누락

`requestServiceExecution`, `addInformationForServiceExecution`, `acceptServiceExecution`, `cancelServiceExecution` 및 `disputeServiceExecution` 함수는 사용자가 임의의 길이의 문자열 데이터를 입력할 수 있도록 합니다.
```solidity
    function requestServiceExecution(
        uint256 serviceNonce,
        string calldata requestData 
    ) external payable {

    function addInformationForServiceExecution(
        uint256 serviceNonce,
        uint256 executionNonce,
        string calldata informationData
    ) external {

    function acceptServiceExecution(
        uint256 serviceNonce,
        uint256 executionNonce,
        string calldata responseData 
    ) external {

    function cancelServiceExecution(
        uint256 serviceNonce,
        uint256 executionNonce,
        string calldata cancelData
    ) external {

    function disputeServiceExecution(
        uint256 serviceNonce,
        uint256 executionNonce,
        string calldata disputeData
    ) {
```
최대 데이터 크기 제한이 없으면 악의적인 공격자가 대량의 유효하지 않은 데이터가 포함된 요청을 보낼 수 있습니다. 이러한 데이터는 결국 프런트 엔드에 나타나 사용자 경험을 저하시킵니다.

# [L-02] 불충분한 유효성 검사로 인해 유효하지 않은 실행 논스(nonce)에 대한 데이터 미리 채우기 허용

문서에 따르면 addInformationForServiceExecution() 함수는 주로 크리에이터가 특정 제안(즉, executionNonce)에 대해 생성된 X 게시물에 대한 정보 데이터를 업로드하는 데 사용하도록 의도되었습니다. 여기서 문제는 함수가 executionNonce가 전역 `executionNonces` 값보다 작은지 확인하지 않는다는 것입니다. 이를 통해 크리에이터 또는 분쟁 해결자가 아직 존재하지 않는 미래의 논스에 대한 정보 데이터를 미리 채울 수 있습니다. 현재 온체인 위협이 되지는 않지만 함수는 `ServiceExecutionInformation`을 방출하므로 오프체인 프로세스에서 다르게 소비될 수 있습니다. 이러한 동작은 의도된 흐름의 일부가 아니므로 허용해서는 안 됩니다.
```solidity
    function addInformationForServiceExecution(
        uint256 serviceNonce,
        uint256 executionNonce,
        string calldata informationData
    ) external {
        VisibilityServicesStorage storage $ = _getVisibilityServicesStorage();

        Service storage service = $.services[serviceNonce];
        Execution storage execution = service.executions[executionNonce];
```

# [L-03] 불충분한 유효성 검사로 인해 가시성 크레딧 결제 시 ETH 전송 허용

지불 가능한(payable) 함수 requestServiceExecution()에서 지불 유형이 크레딧 토큰인 경우 msg.value가 0인지 확인하지 않습니다. 이로 인해 호출과 함께 ETH가 전송되면 계약에 잠긴 상태로 유지됩니다. 온전성 확인(sanity check) `if (msg.value != 0) revert();`를 추가하여 서비스 요청 중에 발생할 수 있는 사용자 실수를 방지해야 합니다.
```solidity
if (paymentType == PaymentType.VISIBILITY_CREDITS) {
            /// @dev it reverts if not enough credits
            $.visibilityCredits.transferCredits(
                service.visibilityId,
                msg.sender,
                address(this),
                service.creditsCostAmount
            );
```
