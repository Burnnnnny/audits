# 소개

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만드는 것을 목표로 합니다. 100% 보안은 결코 보장될 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**spectra-core** 저장소에 대한 시간 제한이 있는 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 컨트랙트 구현의 보안 측면에 중점을 두었습니다.

# Spectra 소개

Spectra는 이자 발생 토큰(Interest Bearing Token, IBT)에서 발생한 수익을 원금 자산에서 분리할 수 있게 해줍니다. IBT는 프로토콜에 예치되고 사용자는 그 대가로 원금 토큰(Principal Tokens, PT)과 수익 토큰(Yield Tokens, YT)을 받습니다. PT는 원금 자산을 나타내고 YT는 IBT에서 발생한 수익을 나타냅니다. 특정 IBT에 대한 수익 토큰 보유자는 YT를 보유하는 동안 해당 예치된 IBT에서 발생한 수익을 청구할 수 있습니다.

# 리스크 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향 (Impact)

- 높음 - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 적당한 피해를 줍니다.

- 낮음 - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소수의 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 훔치거나 잃을 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 - 조건부로 인센티브가 있는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 - 가정이 너무 많거나 너무 비현실적이거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (배포 전이라면 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [fec59dc6720fb4861b07b30845ef2c1a42f947bf](https://github.com/perspectivefi/spectra-core/commit/fec59dc6720fb4861b07b30845ef2c1a42f947bf)

_수정 검토 커밋 해시_ - [afaf6925be7713c89745cac17cc264b549591a06](https://github.com/perspectivefi/core-v2/pull/668/commits/afaf6925be7713c89745cac17cc264b549591a06)

### 범위

다음 스마트 컨트랙트들이 감사 범위에 포함되었습니다:

- `Registry`
- `factory/Factory`
- `router/Commands`
- `router/Dispatcher`
- `router/Router`
- `router/Constants`
- `router/util/RouterUtil`
- `libraries/CurvePoolUtil`

# 발견 사항

# [M-01] `TRANSFER_FROM_WITH_PERMIT` 커맨드는 프런트러닝을 통한 DOS에 취약함

## 심각도

**영향:** 중간, 정당한 트랜잭션 실행을 방해할 수 있는 서비스 거부(DOS) 공격을 허용하기 때문입니다.

**가능성:** 중간, 멤풀에서 트랜잭션을 관찰하고 프런트러닝하는 등 특정 조건이 충족되어야 하기 때문입니다.

## 설명

`TRANSFER_FROM_WITH_PERMIT` 커맨드는 오프체인 서명 승인을 온체인에서 사용할 수 있도록 하여 가스를 절약하고 사용자 경험을 개선하기 위한 것입니다.

그러나 `execute()` 호출에 이 커맨드를 포함하면 트랜잭션이 프런트러닝을 통한 DOS에 취약해집니다. 공격자는 멤풀에서 트랜잭션을 관찰하고, 캘데이터(calldata)에서 permit 서명 및 값을 추출하여 원본 트랜잭션이 처리되기 전에 permit을 실행할 수 있습니다. 이렇게 되면 사용자 permit과 관련된 nonce가 소모되어 원본 트랜잭션은 이제 유효하지 않은 nonce로 인해 실패합니다.

이 공격 벡터는 이전에 [Permission Denied - The Story of an EIP that Sinned](https://www.trust-security.xyz/post/permission-denied)에서 설명되었습니다.

다음 테스트는 `attacker=address(1337)`가 `owner` 트랜잭션을 프런트러닝하고 `permit`을 호출하여 커맨드 실행이 되돌려지는(revert) 것을 보여줍니다:

```solidity
    // File: test/Router/RouterTest.t.sol:ContractRouterTest
    function testPermitFrontRun() public {
        uint256 amount = 10e18;
        uint256 privateKey = 0xABCD;
        address owner = vm.addr(privateKey);
        underlying.mint(owner, amount);
        //
        // 1. Owner signs to router
        vm.startPrank(owner);
        underlying.approve(address(principalToken), amount);
        uint256 shares = principalToken.deposit(amount, owner);

        bytes32 PERMIT_TYPEHASH = keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );
        uint256 deadline = block.timestamp + 10000;
        (uint8 v, bytes32 r, bytes32 s) = vm.sign(
            privateKey,
            keccak256(
                abi.encodePacked(
                    "\x19\x01",
                    principalToken.DOMAIN_SEPARATOR(),
                    keccak256(
                        abi.encode(PERMIT_TYPEHASH, owner, address(router), shares, 0, deadline)
                    )
                )
            )
        );
        vm.stopPrank();
        //
        // 2. Attacker frontrun the caller, the nounce is incremented.
        vm.prank(address(1337));
        principalToken.permit(owner, address(router), shares, deadline, v, r, s);
        //
        // 3. Router executes the permit and it will be reverted
        vm.startPrank(owner);
        bytes memory commands = abi.encodePacked(bytes1(uint8(Commands.TRANSFER_FROM_WITH_PERMIT)));
        bytes[] memory inputs = new bytes[](1);
        inputs[0] = abi.encode(address(principalToken), shares, deadline, v, r, s);
        vm.expectRevert();
        router.execute(commands, inputs);
        vm.stopPrank();
    }
```

사용자의 실행은 되돌려지지만, 사용자는 라우터를 다시 호출하여 라우터를 재호출하고 커맨드를 공식화하도록 강요하여 다른 트랜잭션에서 소비하게 할 수 있습니다.

## 권고 사항

`permit()` 호출이 실패할 경우, 원하는 금액에 대한 승인이 존재하는지 확인하는 메커니즘을 구현하는 것을 고려하십시오. 잠재적인 코드 조정은 다음과 같을 수 있습니다:

```solidity
try IERC20Permit(token).permit(msgSender, address(this), value, deadline, v, r, s) {
    // Permit executed successfully, proceed
} catch {
    // Check allowance to see if permit was already executed
    uint256 currentAllowance = IERC20(token).allowance(msgSender, address(this));
    if(currentAllowance < value) {
        revert("Permit failed and allowance insufficient");
    }
    // Otherwise, proceed as if permit was successful
}
```

이 접근 방식은 프런트러닝 또는 기타 문제로 인해 permit 호출 자체가 실패하더라도 종료 상태가 예상대로인 경우 트랜잭션을 진행할 수 있도록 보장합니다.

# [M-02] 수수료가 있는 토큰(fee-on-transfer tokens)에서 코드가 작동하지 않음

## 심각도

**영향:** 높음, 핵심 기능이 고장날 수 있습니다.

**가능성:** 낮음, 수수료가 있는 토큰을 갖는 것은 가능한 시나리오입니다.

## 설명

코드의 일부 위치에서는 ERC20 전송 함수가 지정된 `amount`를 전송할 것이라고 가정하지만, 이는 수수료가 있는 토큰의 경우 사실이 아닙니다. 이로 인해 해당 경우 잘못된 계산 결과가 발생합니다. 이 문제가 발생하는 몇 가지 위치는 다음과 같습니다:

1. `_addInitialLiquidity()` 코드에서 `_initialLiquidityInIBT` 토큰을 전송한다고 가정하고 이를 Curve 풀로 전송하려고 시도합니다.
2. `_dispatchPreviewRate()` 코드에서 사용자로부터 `value`를 전송할 것이라고 가정합니다.

## 권고 사항

상당한 코드 수정(실제 잔액 관리)을 고려하거나 코드 실행 중에 수수료가 있는 토큰이 나타나지 않도록 방지하거나 이러한 토큰은 항상 잘못된 계산을 갖게 될 것임을 인정하십시오.

# [M-03] execute() 중간의 공격 표면

## 심각도

**영향:** 높음, 사용자는 승인된 토큰을 잃게 됩니다.

**가능성:** 낮음, 실행 흐름이 도달하기 어려운 악성 주소에 도달해야 합니다.

## 설명

사용자는 Router 컨트랙트의 `execute()` 함수를 통해 여러 명령을 실행할 수 있습니다. 이러한 명령을 통해 첫 번째 `execute()` 호출에서 `msgSender`로 설정된 `msg.sender`의 자금을 이체할 수 있습니다. 문제는 `execute()` 실행 중에 실행이 악성 주소에 도달하면 공격자가 자금 도난으로 이어지는 다양한 작업을 수행할 수 있다는 것입니다.

"실행 흐름이 악성 주소에 도달한다"는 조건은 다양한 방식으로 발생할 수 있습니다. 예를 들어 일부 ERC20 토큰(ERC777, ERC677 등)이 가진 훅 기능을 사용하면 실행이 명령에 정의된 `recipient` 주소에 도달합니다.

1. 주소가 Router 컨트랙트에 재진입하여 초기 트랜잭션 전송자의 자금을 이체하는 것과 같은 임의의 명령을 실행할 수 있습니다.

```solidity
   User1 -> Router.execute() -> .... -> malicious address -> Router.execute() -> USDT.transferFrom(User1, malicious address, )
```

2. `onFlashLoan()` 함수는 호출자와 운영자(operator)를 확인하지 않으므로 임의의 데이터로 임의의 주소에서 호출할 수 있습니다. 사용자는 Router 컨트랙트의 `execute()` 함수를 통해 여러 명령을 실행할 수 있습니다. 악성 주소는 `onFlashLoan()` 함수를 통해 Router 컨트랙트에 재진입하여 원래 `msgSender` 자금을 훔칠 수 있습니다.

```solidity
   User1 -> Router.execute() -> .... -> malicious address -> Router.onFlashLoan() -> Router.execute() -> USDT.transferFrom(User1, malicious address, )
```

3. 사용자는 `execute()` 함수의 명령에 임의의 주소를 설정하여(예: `DEPOSIT_ASSET_IN_IBT` 명령을 사용하고 악성 주소를 `ibt` 값으로 설정) 악성 주소에 대한 Router 토큰 승인을 설정할 수 있습니다. 악성 주소는 Router 컨트랙트의 잔액을 이체하고 원래 `msgSender` 자금을 훔칠 수 있습니다.

```solidity
A.  Malicious_address -> Router.execute() -> USDT.approve(malicious address, uint256.max)
B.  User1 -> Router.execute() -> .... -> Malicious_address -> USDT.transferFrom(Router, malicious address, )
```

## 권고 사항

각 공격에는 별도의 방어가 필요합니다:

1. 코드에는 화이트리스트 주소가 있어야 하며 `execute()` 함수에 재진입하는 것만 허용해야 합니다. (Router 및 `msgSender` 주소)
2. `onFlashLoan()` 함수는 `msg.sender()`를 확인하고 `operator`도 확인하여 합법적인 값을 가지고 있는지 확인해야 합니다.
3. Router 및 Dispatcher 컨트랙트 코드는 명령 실행 후 허용량(allowance)을 0으로 설정하거나 코드는 신뢰할 수 있는 주소(Curve 풀 및 PT 풀)에 대해서만 승인을 설정해야 합니다.

# [L-01] 배포 중 `Factory.registry`가 수정될 수 있음

`Registry.sol` 컨트랙트는 프로토콜 내에서 사용되는 유효한 주소를 저장하는 데 사용됩니다. `Factory::deployPT` 함수를 사용하여 `principalToken`이 생성되면 새 `principalToken`이 `Registry.sol`에 등록됩니다(코드 라인 `Factory#L80`):

```solidity
File: Factory.sol
65:     function deployPT(address _ibt, uint256 _duration) public override returns (address pt) {
...
...
78:         pt = address(new BeaconProxy(ptBeacon, _data));
79:         emit PTDeployed(pt, msg.sender);
80:         IRegistry(registry).addPT(pt);
...
...
```

`principalToken`이 만기에 도달하지 않았고 `Registry.sol`에 등록된 `PT`인 한(코드 라인 `Factory#L104-L109`), 해당 `principalToken`과 연관된 `curvePool`은 `Factory::deployCurvePool` 함수를 사용하여 생성할 수 있습니다:

```solidity
File: Factory.sol
096:     function deployCurvePool(
097:         address _pt,
098:         CurvePoolParams calldata _curvePoolParams,
099:         uint256 _initialLiquidityInIBT
100:     ) public returns (address curvePool) {
101:         if (curveFactory == address(0)) {
102:             revert CurveFactoryNotSet();
103:         }
104:         if (!IRegistry(registry).isRegisteredPT(_pt)) {
105:             revert UnregisteredPT();
106:         }
107:         if (IPrincipalToken(_pt).maturity() < block.timestamp) {
108:             revert ExpiredPT();
109:         }
...
...
```

또한 관리자는 `Factory::setRegistry` 함수를 사용하여 `Factory.registry`를 변경할 수 있는 기능이 있습니다. 문제는 `Factory.registry`가 변경될 때 발생하며, 이러한 변경으로 인해 여전히 `principalToken`에 대한 `curvePool`을 생성할 수 있는 사용자가 `curvePool`을 배포하지 못하게 됩니다. 이는 `principalToken`이 `Registry`에 등록되지 않은 경우 `curvePool` 배포를 금지하는 `Factory::deployCurvePool` 내의 유효성 검사 때문입니다. 다음 시나리오를 고려해 보십시오:

1. 사용자가 만기가 1년인 `principalToken`을 생성하지만 관련 `curvePool`을 즉시 생성하지는 않습니다. 이 새로운 `PT`는 `Registry.sol` 내에 저장됩니다.
2. 10일 후, 어떤 이유로 관리자가 `Factory::setRegistry` 함수를 사용하여 `registry`를 변경하기로 결정합니다.
3. 나중에 사용자가 `principalToken`과 연관된 `curvePool`을 생성하기로 결정합니다. 그러나 `PT`가 새 `Registry` 내에 없으므로 다음 유효성 검사로 인해 트랜잭션이 되돌려집니다:

```solidity
File: Factory.sol
096:     function deployCurvePool(
097:         address _pt,
098:         CurvePoolParams calldata _curvePoolParams,
099:         uint256 _initialLiquidityInIBT
100:     ) public returns (address curvePool) {
...
104:         if (!IRegistry(registry).isRegisteredPT(_pt)) {
105:             revert UnregisteredPT();
106:         }
...
```

이는 특히 자산이 이미 `PT`에 예치된 경우 `PT`를 배포한 사용자에게 영향을 미칩니다. `curvePool` 배포를 용이하게 하기 위해 새 `PT`를 재배포하는 것은 불가능하므로 `PT`에 유동성을 제공할 풀이 없게 됩니다.

권장 사항은 `Factory::setRegistry`에서 등록된 `PT`들을 `새 레지스트리`로 마이그레이션하는 것입니다.

# [L-02] `Dispatcher::_dispatchPreviewRate`가 `rate`를 잘못 계산할 수 있음

`Dispatcher::_dispatchPreviewRate` 함수는 다양한 명령의 실행을 시뮬레이션하고 비율(rate)을 얻도록 설계되었습니다. 비율은 계약에 전송된 토큰 단위당 라우터가 실행한 일련의 작업 끝에 반환된 토큰 양으로 정의됩니다.

문제는 배치 중간에 `TRANSFER_FROM` 또는 `TRANSFER_FROM_WITH_PERMIT` 명령이 사용되어 `previousAmount`가 재설정되고 후속 트랜잭션에서 이것이 잘못 사용되는 경우 발생합니다. 다음 시나리오를 고려해 보십시오:

1. 사용자가 `Dispatcher::_dispatchPreviewRate`에서 `TRANSFER_FROM` 명령을 실행하여 `IBT`를 전송하고 `_previewRate = (1 exchangeRate, 1e18 commandAmount)` 상태가 됩니다(Router::\_previewRate#L103-L126).
2. 사용자가 `DEPOSIT_IBT_IN_PT` 명령을 실행하고 특정 비율로 `PT`를 획득하여 비율이 `_previewRate = (0.9 exchangeRate, 0.9e18 commandAmount)`가 됩니다.
3. 사용자가 `TRANSFER_FROM` 명령을 다시 실행하여 일부 `PT`를 전송하면 `_previewRate = (0.9 exchangeRate, 1e18 commandAmount)`로 재설정됩니다. `commandAmount` 증가에 유의하십시오.
4. 사용자가 `CURVE_SWAP` 명령을 실행하면 `3단계`의 재설정으로 인해 잘못된 `previous amount`를 사용하므로 새로운 교환(exchange)이 증가할 수 있습니다.

```solidity
File: Dispatcher.sol
...
...
316:             } else {
317:                 amountIn = _resolvePreviewTokenValue(amountIn, _previousAmount);
318:                 exchangeRate = ICurvePool(pool).get_dy(i, j, amountIn).mulDiv(
319:                     RayMath.RAY_UNIT,
320:                     amountIn
21:                 );
322:             }
323:             return (exchangeRate, 0);
```

사용자가 `TRANSFER_FROM`을 부적절하게 사용하면 `rate` 계산이 부정확해질 수 있습니다.

모든 `TRANSFER_FROM` 명령이 명령 목록의 시작 부분에 배치되도록 하는 것이 좋습니다.

# [L-03] 여러 파일의 사용되지 않는 import

범위 내의 여러 파일에 사용되지 않는 import 문이 포함되어 있습니다. 이러한 불필요한 import는 혼란을 야기하고 계약의 바이트코드 크기를 불필요하게 늘리며 배포 가스 비용에 영향을 줄 수 있습니다.

다음 import는 사용되지 않으며 제거할 수 있습니다:
[Factory.sol#L14](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/factory/Factory.sol#L14)
[RouterUtil.sol#L8](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/router/util/RouterUtil.sol#L8)
[RouterUtil.sol#L12-L14](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/router/util/RouterUtil.sol#L12-L14)
[RouterUtil.sol#L16](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/router/util/RouterUtil.sol#L16)
[CurvePoolUtil.sol#L5](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/libraries/CurvePoolUtil.sol#L5)

# [L-04] `getDx()`의 최대 반복 횟수에서 1차이(Off-by-one) 오류

`CurvePoolUtil` 라이브러리의 `getDx()` 함수에는 루프 종료 조건 확인 방식으로 인해 잠재적인 1차이(off-by-one) 오류가 있습니다. 루프는 `MAX_ITERATIONS_BINSEARCH`로 정의된 최대 반복 횟수만큼 실행되도록 의도되었습니다. 그러나 `if (loops > MAX_ITERATIONS_BINSEARCH)` 조건은 루프가 의도한 것보다 한 번 더 반복되도록 허용합니다.

조건을 `if (loops >= MAX_ITERATIONS_BINSEARCH)`로 조정하는 것을 고려하십시오. 이 조정은 루프가 의도한 로직에 맞춰 최대 정확히 `MAX_ITERATIONS_BINSEARCH` 반복만큼 실행되도록 보장합니다.

# [L-05] 중복된 `_resolveTokenValue` 로직이 인라인됨

[`Dispatcher.sol#L87`](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/router/Dispatcher.sol#L87)에서 `TRANSFER` 명령에 대한 토큰 값 해결 방식에 불일치가 있습니다. `Constants.CONTRACT_BALANCE` 특수 사례를 처리하기 위해 `_resolveTokenValue` 메서드를 활용하는 다른 명령과 달리 `TRANSFER` 명령에 대한 로직은 인라인되어 값을 직접 확인하고 대체합니다. 이러한 접근 방식은 로직을 복제할 뿐만 아니라 값 해결을 위해 도우미 메서드를 사용하는 확립된 패턴에서 벗어나 잠재적으로 유지 관리 문제와 다양한 명령 간의 토큰 값 처리 방식의 불일치를 초래할 수 있습니다.

권장 사항: `_dispatch` 함수 내의 `TRANSFER` 명령 실행 로직을 리팩터링하여 토큰 값 해결을 위해 `_resolveTokenValue` 메서드를 활용하도록 하십시오.

# [L-06] `Router`에서 불필요한 `Ownable2StepUpgradeable` 상속

`Router` 계약은 `Ownable2StepUpgradeable`과 `AccessManagedUpgradeable`(Dispatcher를 통해)을 모두 상속합니다. 원래 `Ownable2StepUpgradeable`은 소유권 및 액세스 제어를 관리하는 데 사용되었을 수 있습니다. 그러나 계약의 현재 구현은 소유자에게 어떠한 특별한 권한도 부여하지 않으므로 `Ownable2StepUpgradeable` 상속 및 관련 초기화가 불필요합니다.

이러한 중복은 계약 코드베이스에 혼란과 불필요한 복잡성을 야기하여 향후 개발이나 유지 관리 시 오류 발생 위험을 높일 수 있습니다. 계약을 간소화하고 명확성을 높이려면 `Ownable2StepUpgradeable`에서 상속을 제거하고 생성자에서 관련 초기화 로직을 제거하십시오.

# [L-07] 비효율적인 ERC20 허용량(allowance) 처리

현재 값이 필요한 것보다 낮은 경우 무제한 허용량을 설정하기 위해 아래 패턴이 여러 인스턴스에서 사용됩니다:

```solidity
uint256 allowance = IERC20(_token).allowance(address(this), _spender);
if (allowance < _value) {
	IERC20(_token).safeIncreaseAllowance(_spender, type(uint256).max - allowance);
}
```

이 패턴의 발생은 다음에서 찾을 수 있습니다:
[Dispatcher.sol#L272](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/router/Dispatcher.sol#L272)
[Router.sol#L165](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/router/Router.sol#L165)
[Factory.sol#L263](https://github.com/perspectivefi/spectra-core/blob/fec59dc6720fb4861b07b30845ef2c1a42f947bf/src/factory/Factory.sol#L263)

그러나 `safeIncreaseAllowance()`는 현재 허용량을 다시 폴링(poll)하고 `forceApprove()`를 호출하기 전에 인수에 전달된 값에 다시 추가합니다:

```solidity
function safeIncreaseAllowance(IERC20 token, address spender, uint256 value) internal {
	uint256 oldAllowance = token.allowance(address(this), spender);
	forceApprove(token, spender, oldAllowance + value);
}
```

`forceApprove()`를 직접 호출하는 것이 더 명확하고 가스 효율적일 것입니다:

```solidity
uint256 allowance = IERC20(_token).allowance(address(this), _spender);
if (allowance < _value) {
	IERC20(_token).forceApprove(_spender, type(uint256).max);
}
```

# [L-08] `Router.execute()`는 payable일 필요가 없음

`Router` 계약의 `execute` 메서드는 `payable`로 표시되어 있습니다. 그러나 어떤 명령도 이더(ether) 전송을 포함하지 않습니다. 이로 인해 사용자가 실수로 트랜잭션과 함께 ETH를 보내 잃어버리거나 메서드의 목적에 대해 혼란스러워할 수 있습니다.

실제 사용 사례를 반영하고 불필요한 이더가 계약에 묶이는 것을 방지하기 위해 payable 수정자를 제거하십시오.

# [L-09] 명령 실행 중 `Router.msgSender`가 0으로 재설정될 수 있음

`execute()`의 명령이 원래 `msg.sender`에 대한 호출을 수행하고 이것이 다시 `execute()`에 재진입하는 경우, `msgSender` 변수는 재진입 호출 시작 시 이미 설정되어 있지만 끝날 때 재설정됩니다. 즉, 이후 명령은 `msgSender`가 0으로 설정된 상태에서 실행될 수 있습니다.

이것이 악의적인 방식으로 악용될 수 있는 것은 아닌 것으로 보이지만, 잠재적인 공격 벡터를 최소화하기 위해 의도한 대로 `msgSender` 변수가 항상 원래 `msg.sender`를 나타내도록 하는 것이 좋습니다. 가능한 완화 방법은 다음과 같습니다:

```diff
diff --git a/src/router/Router.sol b/src/router/Router.sol
index c364804..d72a63d 100644
--- a/src/router/Router.sol
+++ b/src/router/Router.sol
@@ -59,8 +59,10 @@ contract Router is Dispatcher, IRouter, Ownable2StepUpgradeable, IERC3156FlashBo
 
          // Relying on msg.sender is problematic as it changes during a flash loan.
          // Thus, it's necessary to track who initiated the original Router execution.
+        bool topLevel;
          if (msgSender == address(0)) {
              msgSender = msg.sender;
+            topLevel = true;
          }
          // loop through all given commands, execute them and pass along outputs as defined
          for (uint256 commandIndex; commandIndex < numCommands; ) {
@@ -73,7 +75,7 @@ contract Router is Dispatcher, IRouter, Ownable2StepUpgradeable, IERC3156FlashBo
                  commandIndex++;
              }
          }
-        if (msgSender == msg.sender)
+        if (msgSender == msg.sender && topLevel)
              // top-level reset
              msgSender = address(0);
      }
```

# [L-10] `msgSender`가 `address(this)`일 때 `Router.onFlashLoan()` 호출

`onFlashLoan()`은 직접 호출될 수 있으며, 이를 통해 `msgSender` 변수가 `address(this)`로 설정된 상태에서 명령을 실행할 수 있습니다.

이것이 악의적인 방식으로 악용될 수 있는 것은 아닌 것으로 보이지만, 잠재적인 공격 벡터를 최소화하기 위해 `msgSender` 변수는 원래 호출자로만 설정될 수 있도록 하는 것이 좋습니다.

**권장 사항**: `onFlashLoan()`에서 `msgSender != address(0)`인지 확인하십시오.

# [L-11] `deployAll()`의 불완전한 권한 설정이 보상 청구에 영향을 미침

`Factory.deployAll()`에서 `deployPT()` 함수에 설정된 모든 권한이 배포된 PT에 적용되는 것은 아닙니다. 구체적으로 보상 수확(harvesting) 및 보상 프록시 설정과 관련된 권한이 누락되었습니다. 이러한 간과로 인해 PT 배포 및 Curve 풀 배포가 예상대로 진행되지만 배포 시점에 잠재적인 보상을 청구할 수 있는 기능이 제대로 구성되지 않습니다.

이러한 권한은 전역 관리자가 사후에 설정할 수 있지만 필수 권한에 대해 배포 후 조정에 의존하면 불필요한 운영 복잡성이 발생합니다.

**권장 사항**:

`ensure` 함수를 사용하여 `deployAll` 함수 내에 보상 수확 및 보상 프록시 설정을 포함한 모든 관련 권한이 적용되어 `deployPT` 함수에 설정된 권한과 일치하도록 하십시오.
예: `deployAll()`에서 코드는 PT 계약의 `SetRewardsProxy()` 및 `ClaimRewards()`에 대한 대상 함수 역할을 설정할 수 있습니다.

# [L-12] 불필요한 approve 사용

Dispatcher 코드의 `Commands.CURVE_REMOVE_LIQUIDITY` 및 `Commands.CURVE_REMOVE_LIQUIDITY_ONE_COIN` 명령에서 `_ensureApproved(lpToken, pool, lps)`를 호출하는데 이는 불필요합니다.

# [L-13] `spotExchangeRate()` 함수에서 `CURVE_DECIMALS`를 사용해야 함

코드의 주석에 따르면 환율은 18 소수점이어야 하므로 코드는 가격 소수점을 `CURVE_DECIMALS`에서 `UNIT`(18임)으로 변환해야 합니다. 현재 구현 코드는 이 두 값이 동일하다고 가정하고 소수점을 완전히 변환하지 않으며 `CURVE_DECIMALS` 대신 `UNIT`을 사용합니다.

```solidity
        if (_i == 0 && _j == 1) {
            return Constants.UNIT.mulDiv(Constants.UNIT, ICurvePool(_curvePool).last_prices());
        } else if (_i == 1 && _j == 0) {
            return ICurvePool(_curvePool).last_prices();
        }
```

# [L-14] `_dispatch()`는 `_resolveTokenValue()`가 0을 반환할 때 명령을 무시해야 함

일부 ERC20 토큰이나 볼트 또는 풀은 0 금액 상호 작용을 허용하지 않습니다. `_dispatch()` 함수는 명령을 수행하며 `execute()` 함수에 의해 호출됩니다. 문제는 `execute()`에 여러 명령이 있을 수 있고 각 명령은 이전 명령의 결과와 라우터의 잔액을 사용할 수 있으므로 일부 명령의 값이 0이 될 수 있으며 코드가 0일 때 명령을 무시하는 확인이 코드에 없다는 것입니다. 따라서 전송, 예치, 인출 및 유동성 추가와 같은 작업에서 코드가 0 금액으로 계약과 상호 작용하려고 시도하고 코드가 되돌려질(revert) 것입니다. 값이 0일 때 코드가 명령을 무시하고 되돌림을 피하려고 시도하는 것이 가장 좋습니다.

# [L-15] `_addInitialLiquidity()` 함수에 슬리피지(slippage) 보호가 없음

사용자는 `deployCurvePool()`을 호출하고 PT 풀에 대한 Curve 풀을 배포할 수 있으며 코드는 풀을 생성하고 사용자가 지정한 가격에 따라 Curve 풀에 유동성을 추가합니다. 문제는 트랜잭션이 멤풀에 있을 때와 트랜잭션이 블록체인에 포함될 때 실제 토큰 가격(ibt-PT)이 변경될 수 있다는 것입니다. 코드는 실제 가격과 다른 가격의 풀을 생성할 수 있으며(사용자 자금은 잘못된 가격으로 유동성으로 추가됨) 공격자는 백러닝(back-running) 및 스왑을 수행하여 사용자 자금을 훔칠 수 있습니다.

코드에는 이 문제를 방지하기 위한 슬리피지 보호가 없습니다. 사용자가 최대 슬리피지를 지정할 수 있도록 한 다음 사용자 지정 가격을 실제 온체인 가격과 확인하여 슬리피지 범위 내에 있는지 확인하는 것을 고려하십시오.

