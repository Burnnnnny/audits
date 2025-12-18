# 정보

Krum Pashov, 또는 **pashov**는 독립적인 스마트 계약 보안 연구원입니다. 다양한 프로토콜에서 수많은 보안 취약점을 발견했으며, 보안 연구 및 검토에 시간과 노력을 투자하여 블록체인 생태계와 프로토콜에 기여하기 위해 최선을 다하고 있습니다. 그의 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식에 제한이 있는 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 귀하의 스마트 계약에서 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**token-vesting-contracts**에 대해 시간 제한이 있는 보안 검토를 **pashov**가 수행했으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# yHair Vesting 소개

이 프로토콜은 [Molecule vesting protocol](https://github.com/moleculeprotocol/token-vesting-contract)의 포크입니다. 클리프 및 베스팅 기간이 있는 온체인 토큰 베스팅 구현입니다. 포크된 구현 외에도 베스팅 일정 구매 기능과 토큰 청구 비용이 추가되었습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향(Impact)

- 높음(High) - 프로토콜 자산의 상당한 물질적 손실로 이어지거나 사용자 그룹에 심각한 해를 끼칩니다.

- 중간(Medium) - 프로토콜 자산의 중간 정도의 물질적 손실로 이어지거나 사용자 그룹에 적당한 해를 끼칩니다.

- 낮음(Low) - 프로토콜 자산의 경미한 물질적 손실로 이어지거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성(Likelihood)

- 높음(High) - 온체인 조건을 모방하는 합리적인 가정으로 공격 경로가 가능하며, 도난당하거나 분실될 수 있는 자금의 양에 비해 공격 비용이 비교적 낮습니다.

- 중간(Medium) - 조건부 인센티브가 있는 공격 벡터일 뿐이지만 여전히 발생할 가능성이 비교적 높습니다.

- 낮음(Low) - 너무 많거나 너무 그럴듯하지 않은 가정이 있거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 필요

- 치명적(Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음(High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간(Medium) - 수정해야 함

- 낮음(Low) - 수정할 수 있음

# 보안 평가 요약

**_검토 커밋 해시_ - [39635f44b219d9fe95649b8208a98f86d8dd7ebd](https://github.com/HairDAO/token-vesting-contracts/tree/39635f44b219d9fe95649b8208a98f86d8dd7ebd)**

**_수정 검토 커밋 해시_ - [f14edf844e443ea227b70f20d4dc4f7da888052b](https://github.com/HairDAO/token-vesting-contracts/tree/f14edf844e443ea227b70f20d4dc4f7da888052b)**

# 결과

# [H-01] `createVestingSchedule`이 `ROLE_CREATE_SCHEDULE` 역할의 다른 보유자에 의해 프론트러닝 될 수 있음

## 심각도

**영향:**
높음, 베스팅 토큰 잔액을 도난당할 수 있기 때문

**가능성:**
중간, 프론트러닝이 필요하기 때문

## 설명

`TokenVestingV2`의 `createVestingSchedule` 메서드는 베스팅 일정을 초기화하기 전에 미리 전송된 잔액이 있을 것으로 예상합니다. 현재 계약 버전의 문제는 여러 계정이 `ROLE_CREATE_SCHEDULE` 역할을 보유할 수 있다는 것입니다. 베스팅 일정을 생성하는 데 두 개의 트랜잭션이 예상되므로(`TokenVestingV2` 계약으로 자금 전송 후 `createVestingSchedule` 호출), 그 사이에 다른 역할 보유자가 들어와 자신의 베스팅 일정(예: 자신을 수혜자로, 7일 기간의 취소 불가능)을 생성하여 다른 역할 보유자의 자금을 훔칠 수 있습니다.

## 권장 사항

`createVestingSchedule`이 호출자로부터 계약으로 베스팅 일정 토큰을 직접 전송하도록 변경하거나 단 1개의 주소에서만 호출할 수 있도록 하십시오.

# [L-01] 결함 있는 액세스 제어

`setVTokenCost` 및 `setTokenCost` 메서드는 `ROLE_CREATE_SCHEDULE` 역할 보유자만 호출할 수 있습니다. 메서드는 베스팅 일정 구매 비용을 결정합니다. 일정을 생성하는 역할이 가격을 결정해서는 안 되므로 이는 대신 `DEFAULT_ADMIN_ROLE`의 기능이어야 합니다.

# [L-02] 일시 중지가 올바르게 구현되지 않음

현재 `TokenVestingV2`의 `setPaused` 메서드는 NatSpec에 "토큰 릴리스 및 일정 청구를 일시 중지하거나 일시 중지 해제합니다"라고 말합니다. 문제는 계약의 어떤 메서드도 `whenNotPaused` 또는 `whenPaused` 수정자를 가지고 있지 않으므로 주석이 잘못되었다는 것입니다. `setPaused` 메서드를 완전히 제거하십시오.

### 논의

**Pashov Audit Group:** 클라이언트는 수정으로 `createVestingSchedule` 및 `purchaseVSchedule` 메서드에 대신 `whenNotPaused` 수정자를 넣었으며, 이는 일정 구매에 약간의 중앙화를 가져옵니다 - 소유자가 사용자가 그렇게 하는 것을 차단할 수 있습니다.

# [L-03] `getVestingSchedulesIds`의 DoS 가능성

`getVestingSchedulesIds` 메서드는 전체 `vestingSchedulesIds` 배열을 메모리로 복사하는데, 메모리 확장 비용으로 인해 막대한 양의 가스 비용이 발생할 수 있습니다. 배열에 대한 푸시는 제한이 없으므로 너무 커지면 메서드 호출에 필요한 가스가 블록 가스 제한보다 많거나 실행하기에 너무 비쌀 수 있습니다. 이러한 오류가 발생하지 않도록 배열 크기를 제한하십시오.

# [L-04] 토큰 가격 설정자에 입력 검증 누락

`setVTokenCost` 및 `setTokenCost` 메서드에는 하한 및 상한이 누락되어 있어 호출자가 예를 들어 엄청난 값을 설정하여 토큰을 실제로 구매할 수 없게 만들 수 있습니다. 해당 메서드에서 설정할 수 있는 값에 적절한 상한과 가능한 경우 하한을 설정하십시오.

# [L-05] `address payable`의 `transfer` 함수 사용은 권장되지 않음

`TokenVestingV2`의 `release`, `releaseAvailableTokensForHolder` 및 `purchaseVSchedule` 메서드는 기본 자산 자금을 주소로 전송하기 위해 `address payable`의 `transfer` 메서드를 사용합니다. `paymentReceiver` 주소가 2300 가스(`transfer` 제한) 이상을 차지하는 `receive` 또는 `fallback` 함수가 있는 스마트 계약인 경우 `paymentReceiver`가 변경될 때까지 메서드는 매번 되돌아갑니다. 예를 들어 일부 스마트 계약 지갑이나 다중 서명 지갑이 있으므로 `transfer` 사용은 권장되지 않습니다. 이를 해결하려면 `transfer` 대신 값이 있는 `call`을 사용하십시오. 세 가지 메서드 모두 `nonReentrant` 수정자를 사용하므로 재진입 위험도 없습니다.

# [L-06] 프로토콜이 취약한 라이브러리 버전을 사용 중임

리포지토리의 `.gitmodules` 파일에서 다음을 볼 수 있습니다:

```javascript
url = https://github.com/openzeppelin/openzeppelin-contracts
branch = release-v4.8
```

이 버전에는 [여기](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories)에서 볼 수 있듯이 여러 취약점이 포함되어 있습니다. 현재 코드베이스에는 문제가 없지만 v4.8 이후 발견된 모든 취약점에 대한 수정 사항이 있는 v4.9.5로 버전을 업그레이드하는 것이 강력히 권장됩니다.

# [L-07] 열거형에 `INVALID` 값 누락

`Status` 열거형의 현재 기본값은 `INITIALIZED`입니다. 존재하지 않는 베스팅 일정의 경우에도 일정이 매핑의 값일 때 기본값을 가지며 `Status`가 `INITIALIZED`가 되므로 오류가 발생하기 쉽습니다. 기본값으로 인한 미묘한 오류를 방지하기 위해 인덱스 0(기본값)인 `INVALID`를 Status 값으로 추가하십시오.

