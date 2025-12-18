# 소개 (About)

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원 팀 여러 개로 구성되어 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 블록체인 프로토콜을 위해 숙련된 연구원들의 최선의 노력을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)

스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)

**Pashov Audit Group**에 의해 **wallet** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# wallet 정보 (About wallet)

HYTOPIA 지갑은 블록체인에서 사용자 계정을 관리합니다. 다중 서명자 권한 모델, 가스 없는 거래(gasless transactions), 세션 승인과 같은 기능을 갖추고 있습니다. 지갑은 스마트 계약 서명을 위한 EIP-1271, 계정 추상화를 위한 EIP-4337, 비콘 프록시를 위한 EIP-1967 등 여러 표준을 따릅니다. 이를 통해 HYTOPIA는 사용자의 자산에 대한 완전한 소유권을 갖지 않고도 사용자 계정을 생성, 거래 및 관리할 수 있습니다.

# 위험 분류 (Risk Classification)

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ----------------- | ----------------- | ------------------- | ---------------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정으로 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 비교적 가능성이 있습니다.

- 낮음 (Low) - 가정이 너무 많거나 가능성이 낮거나 공격자가 거의 또는 전혀 인센티브 없이 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (아직 배포되지 않은 경우 배포 전).

- 중간 (Medium) - 수정해야 합니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [0d38699f148577f137e709ada64bc47fdd65924f](https://github.com/hytopiagg/wallet/tree/0d38699f148577f137e709ada64bc47fdd65924f)**

**_수정 검토 커밋 해시_ - [533d5fdb12385aa50f7ce7c60f0d17d33ea7d953](https://github.com/hytopiagg/wallet/tree/533d5fdb12385aa50f7ce7c60f0d17d33ea7d953)**

### 범위 (Scope)

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `BeaconProxyFactory`
- `WalletProxyFactory`
- `utils/Bytes`
- `utils/Signatures`
- `modules/Versioned/Versioned`
- `modules/SessionCalls/SessionCalls`
- `modules/SessionCalls/SessionCallsStorage`
- `modules/SessionCalls/SessionCallsStructs`
- `modules/PreauthorizedCalls/PreauthorizedCalls`
- `modules/PreauthorizedCalls/PreauthorizedCallsStorage`
- `modules/PreauthorizedCalls/PreauthorizedCallsStructs`
- `modules/Main/Main`
- `modules/Main/MainStorage`
- `modules/ERC1271/ERC1271`
- `modules/Controllers/Controllers`
- `modules/Controllers/ControllersStorage`
- `modules/Calls/Calls`
- `modules/Calls/CallsStructs`
- `interfaces/**`

# 발견 사항 (Findings)

# [H-01] 누락된 `payable` 함수 (Missing `payable` function)

## 심각도

**영향:** 중간, 지갑이 기본 토큰(native tokens)을 받지 못하지만 받을 것으로 예상됨

**가능성:** 높음, msg.value 사용 및 기본 토큰 전송은 빈번한 작업이기 때문

## 설명

`_call()`은 `.call{ value: _callRequest.value }`를 통해 기본 토큰을 보낼 수 있지만, Main.sol, PreauthorizedCalls.sol, SessionCalls.sol, Calls.sol, Controllers.sol을 포함하여 어떤 계약도 기본 토큰을 받을 수 없습니다.

```solidity
File: contracts\modules\Calls\Calls.sol
80:     function _call(CallsStructs.CallRequest calldata _callRequest) internal returns (bytes memory) {
81:         (bool success, bytes memory result) = _callRequest.target.call{ value: _callRequest.value }(_callRequest.data);
82:
```

## 권장 사항

- 관련 함수에 payable 수정자 추가
- `receive()` 함수 추가

# [H-02] 컨트롤러 제거 시 임계값 충돌 (Threshold conflicts with removing controllers)

## 심각도

**영향:** 높음, 지갑을 탈취할 수 있는 공격 벡터

**가능성:** 중간, 모든 컨트롤러 세트와 관련이 있는 것은 아님

## 설명

절대 가중치를 사용하는 데에는 단점이 있습니다. 때로는 컨트롤러를 제거하기 전에 임계값(Threshold)을 낮춰야 합니다. 임계값 감소는 강력한 투표 규칙 조정이며 모든 컨트롤러가 악의적으로 사용할 수 있습니다.

컨트롤러 세트 A=50, B=50이 있다고 상상해 보십시오. 총 가중치(TotalWeight)=100, 임계값(Threshold)=100입니다.
그런 다음 사용자 B가 나가기를 요청한다고 상상해 보십시오.

```
    function _removeController(address _controller) internal {
        ControllersStorage.layout().totalWeights -= ControllersStorage.layout().weights[_controller];
        if (
            ControllersStorage.layout().totalWeights == 0
                || ControllersStorage.layout().totalWeights < ControllersStorage.layout().threshold
        ) {
            revert ThresholdImpossible();
        }
        ControllersStorage.layout().weights[_controller] = 0;
    }
```

`_removeController()`는 되돌려집니다(revert). `totalWeight`는 50으로 업데이트되지만 `threshold=100`은 업데이트되지 않고 우리는 `totalWeight >= threshold`를 요구하기 때문입니다.

따라서 서명자는 두 가지 트랜잭션을 가져야 합니다:

1. `updateControlThreshold()`를 50으로 업데이트
2. B를 제거하는 `removeControllers()`

사용자 A가 TX 1에 서명하면 사용자 B가 지갑을 탈취할 수 있습니다.

1. 컨트롤러 B는 A로부터 서명을 받습니다.
2. 컨트롤러 B는 원자적 멀티콜(atomic multicall) = `updateControlThreshold()`를 50으로 업데이트 + **컨트롤러 A를 제거하는** `removeControllers()`를 수행합니다. 이 마지막 호출은 임계값이 50으로 변경되었으므로 하나의 서명만 필요합니다.

결과적으로 컨트롤러 B는 컨트롤러 A를 제거하고 지갑을 탈취하는 데 성공했습니다.

## 권장 사항

이 문제는 해결하기 어렵습니다. 이 시나리오를 처리하기 위해 몇 가지 새로운 규칙을 도입해야 합니다.
`totalWeight`가 현재 `threshold` 아래로 떨어지면 이 델타 투표력은 다른 컨트롤러 간에 어떻게든 분배될 수 있습니다. **또는**, `totalWeight`가 현재 `threshold`까지만 떨어지도록 허용될 수 있습니다.
다음 아이디어를 고려하십시오.
현재 임계값이 95일 때 100의 `totalWeight`를 20만큼 줄여야 한다고 상상해 보십시오.

1. 5/20는 분배 없이 감소될 수 있습니다. `totalWeight`는 95로 변경됩니다.
2. 15/20은 무시될 수 있습니다.
   결과적으로 `totalWeight`는 95이고 `threshold`는 95이며, 이는 100%의 합의가 필요함을 의미합니다.

또한 `updateControllerWeight()`는 특정 컨트롤러의 가중치를 줄이는 데 사용할 수 있고 거기에서도 `totalWeight`가 감소하므로 동일한 내용이 관련이 있다는 점에 유의해야 합니다.

# [H-03] 지갑 초기화 프론트런 (Wallet initialize frontrun)

## 심각도

**영향:** 높음, 다른 사용자의 새 지갑을 몰래 소유하기 위한 공격

**가능성:** 중간, 프론트런 필요

## 설명

배포 스크립트는 지갑을 배포하기 위해 2개의 트랜잭션을 수행합니다:

1. 계약 배포
2. initialize() 호출 (첫 번째 컨트롤러를 수락함)
   (foundry, 모든 호출은 트랜잭션임)

```
        address _newWalletAddr = _factory.createProxy(_testWalletSalt);
        Main(payable(_newWalletAddr)).initialize(deployer);
```

지갑 배포는 추적되고 대기될 수 있습니다. 공격자는 프론트런하여 악의적인 입력으로 `initialize()`를 호출할 수 있습니다.

아이디어 중 하나:

1. 공격자가 프론트런하여 `Controller=Attacker`로 설정합니다.
2. 공격자는 구현(`Main.upgradeToAndCall()`)을 악의적인 구현으로 업데이트합니다.
3. 다시 `controller=TargetUser`로 설정합니다.
4. 사용자는 악의적인 구현이 있는 이 지갑으로 작업을 계속합니다.
5. 구현에는 모든 토큰을 공격자에게 인출하는 악의적인 함수가 있습니다. 공격자는 지갑에 약간의 잔액이 생기기를 기다렸다가 공격 함수를 호출합니다.

## 권장 사항

지갑 배포와 동일한 트랜잭션에서 `initialize()`가 호출되도록 하십시오.

# [M-01] `RestrictedFunction`이 일부 경우를 놓칠 수 있음 (`RestrictedFunction` could miss come cases)

## 심각도

**영향:** 높음, 계약의 자금이 허용된 금액을 훨씬 초과하여 모두 도난당할 수 있기 때문입니다.

**가능성:** 낮음, MAGIC_CONTRACT_ALL_FUNCTION_SELECTORS가 사용되는 경우에만 적용되며 모든 토큰에 이 문제가 있는 것은 아니기 때문입니다.

## 설명

`approve` 및 `setApprovalForAll`만 제한된 함수로 간주되지만, 토큰 허용량(allowance)이 다른 함수에 의해 변경될 가능성이 있습니다:
예를 들어 [RNDR](https://etherscan.io/address/0x1a1fdf27c5e6784d1cebf256a8a5cc0877e73af0#code)에는 표준 ERC20 함수가 아닌 `increaseApproval()/decreaseApproval()` 함수가 있습니다.

```solidity
File: contracts\modules\SessionCalls\SessionCalls.sol
448:     function _isRestrictedFunction(bytes4 _functionSelector) private pure returns (bool isRestricted_) {
449:         if (
450:             _functionSelector == IERC20.approve.selector || _functionSelector == IERC721.approve.selector
451:                 || _functionSelector == IERC721.setApprovalForAll.selector
452:                 || _functionSelector == IERC1155.setApprovalForAll.selector
453:         ) {
454:             isRestricted_ = true;
455:         }
456:     }

// https://etherscan.io/address/0x1a1fdf27c5e6784d1cebf256a8a5cc0877e73af0#code
  function increaseApproval(address _spender, uint _addedValue) public returns (bool) {
    allowed[msg.sender][_spender] = allowed[msg.sender][_spender].add(_addedValue);
    emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]);
    return true;
  }

  function decreaseApproval(address _spender, uint _subtractedValue) public returns (bool) {
    uint oldValue = allowed[msg.sender][_spender];
    if (_subtractedValue > oldValue) {
      allowed[msg.sender][_spender] = 0;
    } else {
      allowed[msg.sender][_spender] = oldValue.sub(_subtractedValue);
    }
    emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]);
    return true;
  }
```

## 권장 사항

- `MAGIC_CONTRACT_ALL_FUNCTION_SELECTORS` 옵션을 제거하거나 권한 있는 역할로 제한하십시오.

# [M-02] 서명 작업 - 논스 및 만료 (Working with signatures - nonce and expiry)

## 심각도

**영향:** 높음, 일부 서명자가 개인적인 이익을 취할 수 있음

**가능성:** 낮음, 실수와 투표권의 고유한 동등성이 필요함

## 설명

보호되지 않는 몇 가지 공격 벡터가 있습니다.

- 컨트롤러가 두 개의 트랜잭션에 서명한 경우, msg.sender는 이 두 트랜잭션의 순서를 결정하여 어느 것을 우선순위로 둘지 결정할 수 있습니다.
- 일부 서명자가 서명을 거부했지만 다른 서명자가 서명한 경우 - 그들의 서명은 몇 달 후에 수정된 컨트롤러 세트에 대해 사용되거나, 거부한 서명자가 이익이 된다고 판단할 때 서명할 순간을 찾을 수 있습니다.

몇 가지 간단한 예:

1. 가중치가 각각 30, 30, 30인 3명의 컨트롤러 A, B, C가 있습니다.
2. 새 제안 - C의 가중치를 40으로 업데이트합니다.
3. A는 서명하고 비는 서명하고 C는 서명을 거부합니다. 컨트롤러 C는 A와 B의 서명을 저장합니다.
4. 1년 후 컨트롤러 C는 컨트롤러 C를 자신의 다른 지갑 D로 교체할 것을 요청합니다. 모든 컨트롤러는 여러 트랜잭션에 서명하여 결과적으로 3명의 컨트롤러 A, B, D가 있게 됩니다.
5. 컨트롤러 D와 C는 동일한 서명자의 소유입니다. 컨트롤러 D는 이전에 저장된 C의 가중치를 40으로 업데이트하는 트랜잭션에 서명합니다.
6. 이제 컨트롤러 C가 컨트롤러 목록에 나타납니다. 이제 가중치가 30, 30, 30, 40인 컨트롤러 세트 A, B, C, D가 됩니다.
7. 이것은 컨트롤러 A와 B가 서명한 것이 아닙니다.

이것이 가능한 두 가지 문제가 있습니다:

1. 현재 입력의 `nonce`는 솔트(salt)로 사용됩니다. 동일한 논스를 가진 여러 검증된 트랜잭션이 존재할 수 있습니다. 지금은 스토리지 논스 값과 비교되지 않습니다.
   모든 호출에서 1씩 증가하는 스토리지 값 논스여야 합니다. 입력된 논스 6이 있는 새 트랜잭션이 서명되면 스토리지의 활성 논스가 6일 때만 기록될 수 있음을 의미합니다.

2. 입력에 \_inputHash의 일부가 될 `Expiry` 인수가 없습니다. 이것은 현재 block.timestamp와 비교되는 미래의 타임스탬프입니다. 1일의 만료가 있는 입력이 일부 컨트롤러에 의해 서명되면 그들의 서명은 한 달 후에 사용되지 않습니다.

## 권장 사항

1. 스토리지(예: `ControllersStorage.layout()`)에 전역 `nonce`를 추가하고 모든 호출에서 증가하도록 만드십시오.
   모든 함수는 입력된 `_nonce`가 이 `ControllersStorage.layout().nonce`와 같은지 확인해야 합니다.
   `meetsControllersThreshold()`는 이 로직에 완벽한 장소입니다.
2. 입력에 `expiredAt` 인수를 추가하고 해시에 추가하십시오.

# [L-01] CallRequestPreauthorization 추가 검사 (CallRequestPreauthorization additional checks)

`PreauthorizedCalls.preauthorizeCall()`은 `CallRequestPreauthorization`을 입력으로 받습니다.
지금은 .maxCalls만 확인합니다.
`lastCallTimestamp != 0`인지 추가로 확인하는 것을 고려하십시오. 이는 `preauthorizedCall()`의 추가 사용을 위한 예상 조건이기 때문입니다.

# [L-02] addControllers()가 기존 컨트롤러를 확인하지 않음 (addControllers() does not check existing controllers)

`Controller.addControllers()`는 새 컨트롤러 목록을 받습니다.
입력에 두 가지 실수가 있을 수 있습니다:

- 새 컨트롤러 중 일부가 이전에 추가되었습니다.
- 새 컨트롤러 목록에 중복이 있습니다.

이 경우 `totalWeights`는 증가하지만 `weights[_controller]`는 마지막 값으로 다시 작성됩니다.
결과적으로 `totalWeights`는 모든 컨트롤러에 대한 weights[]의 합계를 초과하게 됩니다.

`_addController()`는 새 가중치가 기록되기 전에 새 컨트롤러의 현재 가중치가 0인지 확인해야 합니다.
