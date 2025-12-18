# 정보

Pashov Audit Group은 이 분야에서 최고의 스마트 계약 보안 연구원 팀으로 구성되어 있습니다. 총 1000건 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**omo-protocol/omo-mvp** 저장소에 대한 시간 제한 보안 검토는 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현 보안 측면에 중점을 두었습니다.

# Omo 정보

Omo는 ERC4626 준수 금고(vault)를 기반으로 구축된 유동성 풀 시스템으로, 자산 관리, 스테이킹 및 권한 제어를 위해 OmoVault, AgentSetter, OmoStaking, OmoVaultFactory 및 OmoRouter와 같은 모듈식 구성 요소를 특징으로 합니다. 이 아키텍처는 모듈성, 가스 최적화 및 유연한 구성을 강조하여 독립적인 금고 운영과 간소화된 업그레이드를 가능하게 합니다.

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

_검토 커밋 해시_ - [c52b6d39ec34f640533c4fd30310e1fba31f5f39](https://github.com/omo-protocol/omo-mvp/tree/c52b6d39ec34f640533c4fd30310e1fba31f5f39)

_수정 검토 커밋 해시_ - [d2e1c2fb65d30591b41fd83771218f1445d1a8ef](https://github.com/omo-protocol/omo-mvp/tree/d2e1c2fb65d30591b41fd83771218f1445d1a8ef)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `AgentSetter`
- `OmoRouter`
- `OmoVault`
- `OmoVaultFactory`
- `OmoOracle`
- `DynamicAccount`
- `DynamicAccountFactory`
- `OmoAgent`

# 발견 사항

# [C-01] 사용자가 금고에서 첫 번째 입금을 중복할 수 있음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`OmoVault.sol` 계약은 자산에 대한 공유(share)의 비동기 상환이라는 ERC7540 표준의 일부 원칙을 구현합니다.
그러나 사용자는 여전히 `ERC4626.sol#withdraw()`를 사용하여 직접 인출할 수 있습니다.

또한 `DynamicAccount.sol` 계약의 `_validateSignature()` 함수는 Entry Point를 통해 사용자 지갑을 서명자로 수락합니다.
따라서 사용자는 에이전트와 동일한 권한을 갖습니다.

악의적인 사용자는 금고에서 `withdraw()`를 직접 호출하여 자금을 받을 수 있습니다.
다음으로 그는 Dynamic Account에서 모든 자금과 UniswapV3 포지션을 전송합니다.

결과적으로 그는 금고에 있는 첫 번째 입금을 중복하게 됩니다.

## 권장 사항

1- `OmoVault.sol`에서 `withdraw()` 함수를 재정의(override)하고 되돌리도록(revert) 만드십시오.
2- `_validateSignature()`에서 서명자가 소유자인 경우 어떤 함수 호출이 가능한지 확인해야 합니다.

# [C-02] `_burn()` 함수에 잘못된 값 전달

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

에이전트는 `OmoVault.sol` 계약의 `topOff()` 함수를 호출하여 상환 요청을 한 사용자에게 자산을 전송합니다.
사용자 공유(shares)는 아직 소각되지 않았습니다. `topOff()`는 여기에서 소각합니다.

```solidity
File: OmoVault.sol

453:         _totalAssets -= agentBalance;
454:         _burn(address(this), agentBalance);

```

그러나 잘못된 양의 공유를 소각합니다. `agentBalance`는 사용자에게 전송되는 자산의 양이기 때문입니다.

## 권장 사항

```diff
File: OmoVault.sol

453:         _totalAssets -= agentBalance;
-454:         _burn(address(this), agentBalance);
+454:         _burn(address(this), record.shares);

```

# [C-03] `_totalAssets`의 악의적인 조작으로 인해 사용자가 자금을 상환할 수 없음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`OmoVault.sol#topOff()` 함수는 에이전트만 사용합니다.
에이전트에서 `record.receiver` 주소로 전체 자산 잔액을 전송합니다.

```solidity
File: OmoVault.sol

449:         asset.safeTransferFrom(msg.sender, address(this), agentBalance);
450:         asset.safeTransfer(record.receiver, agentBalance);

```

에이전트가 잔액을 정확한 값으로 설정할 것이라고 신뢰해야 한다는 사실에 관계없이.
악의적인 사용자는 자산을 에이전트 주소로 직접 전송하여(다시 돌려받음) `agentBalance` 값을 조작할 수 있습니다.
그러나 중요한 값은 잘못된 값 `_totalAssets`만큼 감소합니다.

이 시나리오를 고려해보십시오.
밥과 앨리스라는 두 명의 사용자가 있는 경우,

- 둘 다 20$를 입금했습니다.
- 밥은 20$를 상환합니다.
- 에이전트가 `topOff()` 함수를 호출하기 전에 밥은 에이전트에게 20$를 전송합니다.
- 에이전트가 `topOff()` 함수를 호출합니다.
- 밥은 최소 40$를 받게 되고 `_totalAssets`는 0이 됩니다.
  이제 앨리스는 `_totalAssets -= agentBalance;`가 되돌려지기(revert) 때문에 20$를 상환할 수 없습니다.

## 권장 사항

```diff
File: OmoVault.sol

-449:         asset.safeTransferFrom(msg.sender, address(this), agentBalance);
+449:         asset.safeTransferFrom(msg.sender, address(this), record.assets);
-450:         asset.safeTransfer(record.receiver, agentBalance);
+450:         asset.safeTransfer(record.receiver, record.assets);

```

# [C-04] 자금이 금고에 잠김

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

다음 경우:

- 사용자 X가 자산에 대한 공유 상환을 요청하고 `receiver`를 Y로 설정합니다.
- 사용자 X의 Dynamic Account에 등록된 에이전트 Z는 Uniswap V3에서 유동성을 제거합니다.
- 에이전트 Z는 `OmoVault.sol#topOff()` 함수를 호출합니다.

그러나 현재 구현은 위의 경우 되돌려집니다.
`topOff()`는 에이전트가 `record.receiver` Dynamic Account에서 승인되었는지 확인하기 때문입니다.

```solidity
File: OmoVault.sol

427:         address userAccount = userToAccount[record.receiver];
428:         require(userAccount != address(0), "NO_REGISTERED_ACCOUNT");
429:
430:         // Check if agent is registered for this account
431:         OmoAgent agent = OmoAgent(payable(userAccount));
432:         require(agent.agents(0) == msg.sender, "UNAUTHORIZED_AGENT");

```

`userAccount`가 0 주소이거나 등록되지 않았을 가능성이 높습니다.
결과적으로 자금은 `record.receiver`가 Dynamic Account를 생성하고 해당 에이전트를 추가할 때까지 금고에 잠깁니다.

## 권장 사항

```diff
File: OmoVault.sol

-427:         address userAccount = userToAccount[record.receiver];
+427:         address userAccount = userToAccount[record.owner];
428:         require(userAccount != address(0), "NO_REGISTERED_ACCOUNT");


```

# [C-05] 사용자가 자금을 상환할 수 없음

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

Dynamic Accounts는 여러 에이전트를 가질 수 있으며, 이는 `OmoAgent.sol`에서 라이브러리 `OmoAgentStorage`로 `agents[]` 매핑 아래에 저장됩니다.

```solidity
File: OmoAgent.sol

18:     struct Data {
19:         address manager;
20:         mapping(uint256 => address) agents;

```

각 에이전트는 고유한 ID를 갖습니다.
그러나 `OmoVault.sol` 계약의 `topOff()` 함수에서는 `agents[]`에 있는 에이전트만 호출할 수 있습니다.

```solidity
File: OmoVault.sol

430:         // Check if agent is registered for this account
431:         OmoAgent agent = OmoAgent(payable(userAccount));
432:         require(agent.agents(0) == msg.sender, "UNAUTHORIZED_AGENT");
```

그러나 ID 0만 확인합니다(아마도 존재하지 않을 것입니다).
현재 구현으로는 어떤 에이전트도 `topOff()`를 호출하여 사용자 자금을 상환할 수 없습니다.

결과적으로 사용자는 자금을 상환할 수 없습니다.

## 권장 사항

0 값이 아닌 에이전트 ID를 전달해야 합니다.

# [C-06] `borrow()`는 `totalAssets` 값을 감소시켜야 함

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

`OmoVault.sol` 계약에는 에이전트만 사용하는 `borrow()`가 있어 자산을 에이전트에게 전송합니다.
이러한 부채에 대한 추적 시스템이 없습니다(오프체인 보안에 의존함).
또한 `_totalAssets` 변수를 업데이트하지 않습니다.

```solidity
File: OmoVault.sol

403:     function borrow(uint256 assets, address receiver) external nonReentrant onlyAgent {
407:         address msgSender = msg.sender;
409:         require(assets != 0, "ZERO_ASSETS");
411:         asset.safeTransfer(receiver, assets);
414:     }

```

이는 `totalAssets()` 계산에 영향을 미칠 것입니다. 현재 구현은 에이전트가 빌린 자산을 두 번 계산하기 때문입니다.
그러나 `totalAssets()`는 금고의 모든 자산과 DynamicAccounts의 모든 Uni V3 포지션 가치의 합이어야 합니다.

## 권장 사항

```diff
File: OmoVault.sol

403:     function borrow(uint256 assets, address receiver) external nonReentrant onlyAgent {
407:         address msgSender = msg.sender;
409:         require(assets != 0, "ZERO_ASSETS");
+410:      _totalAssets =  _totalAssets - assets;
411:         asset.safeTransfer(receiver, assets);
414:     }

```

# [C-07] 제한 없는 `OmoAgent.onERC721Received()`로 인한 영구적인 DoS 및 자금 묶임 허용

## 심각도

**영향:** 높음

**가능성:** 높음

## 설명

- `OmoAgent.onERC721Received()` 함수는 `safeTransferFrom()` 메서드를 사용하여 NFT가 계약으로 전송될 때 호출되도록 의도되었지만, `depositPosition()` 함수는 `transferFrom()`을 사용하여 NFT를 전송하므로 합법적인 입금 중에는 이 훅이 트리거되지 않습니다.

```javascript
   function onERC721Received(
        address,
        address,
        uint256 tokenId,
        bytes calldata
    ) external returns (bytes4) {
        // if (msg.sender == OmoAgentStorage.data().positionManager) {
        _addPositionId(tokenId);
        // }
        return this.onERC721Received.selector;
    }
```

- **그럼에도 불구하고**, `onERC721Received()`는 **모든** 주소에서 호출 가능하므로 다음과 같은 문제가 발생합니다.

1. **`depositPosition()`의 영구적인 DoS**:

   - 모든 공격자는 임의의 토큰 ID로 `onERC721Received()`를 호출할 수 있습니다.
   - 이러한 토큰 ID는 `ownedPositions` 및 `_positionIds`에 추가됩니다.
   - 에이전트가 유효한 포지션 ID를 입금하려고 하면 트랜잭션이 `"Position already tracked"`로 되돌려집니다.

2. **저장소 팽창(storage bloat)으로 인한 가스 부족(OOG)**:

   - 공격자는 지속적으로 토큰 ID를 추가하여 `_positionIds` 및 `ownedPositions` 매핑을 팽창시킬 수 있습니다.
   - 이로 인해 `topOffToAgent()` 및 `getPositionValue()`에서 `_positionIds`를 반복할 때 **OOG 오류**가 발생합니다.

3. **프로토콜 기능의 완전한 붕괴**:
   - **자금 묶임**: `topOffToAgent()` 실패로 인해 에이전트는 예치된 포지션을 회수할 수 없습니다.
   - **금고 중단**: 이 Dynamic Account가 `OmoVault`에 등록된 경우 `OmoVault.getAssets()` 호출이 실패하여 `OmoVault` 계약에서 악의적이거나 손상된 에이전트를 제거할 기능이 없으므로 금고가 영구적으로 비활성화됩니다.

## 권장 사항

NFT 포지션 추가 로직은 `depositPosition()` 함수에서 처리하므로 `OmoAgent.onERC721Received()`에서 `depositPosition()`을 제거하십시오.

# [H-01] 누구나 사용자의 자금을 상환하고 자금을 가져갈 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`OmoRouter.sol` 계약의 `redeem()` 함수는 사용자가 동등한 자산에 대한 공유 상환을 요청하기 위해 트리거합니다.
`owner`는 라우터 주소가 공유를 전송하도록 승인을 설정해야 합니다.

```solidity
File: OmoRouter.sol
     function redeem(uint256 vaultId,uint256 shares, address receiver,address owner)
     /*code*/
         // Transfer shares from owner to router first
         IOmoVault(vault).transferFrom(owner, address(this), shares);

```

공격자는 다른 사용자로부터 라우터 주소에 대한 모든 허용량(allowance)을 금고 공유로 소진할 수 있으며(체인이 프런트런 가능성이 있는 경우 MEV 봇이 사용자 `redeem()` 트랜잭션을 프런트런할 수 있음) 에이전트가 나머지를 수행(자금 전송)할 때까지 기다릴 수 있습니다.

## 권장 사항

소유자는 `msg.sender`여야 합니다.

```diff
File: OmoRouter.sol
-     function redeem(uint256 vaultId,uint256 shares, address receiver,address owner)
+     function redeem(uint256 vaultId,uint256 shares, address receiver)
     /*code*/
         // Transfer shares from owner to router first
-         IOmoVault(vault).transferFrom(msg.sender, address(this), shares);
+         IOmoVault(vault).transferFrom(owner, address(this), shares);

```

# [H-02] `OmoRouter.sol`의 `transferFrom()`에 승인 취약점 존재

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명

`OmoRouter.sol` 계약의 `transferFrom()` 함수는 발신자 `from`에서 수신자 주소 `to`로 일정량의 공유를 전송합니다.

```solidity
File: OmoRouter.sol

File: OmoRouter.sol
     function transferFrom(uint256 vaultId,address from,address to,uint256 amount) external returns (bool) {
         address vault = vaultFactory.getVault(vaultId);
         return IOmoVault(vault).transferFrom(from, to, amount);
     }
```

`OmoVault.sol#transferFrom()`을 직접 하위 호출합니다.
사용자는 라우터가 `msg.sender`를 대신하여 지출하고 수신자 주소로 보낼 수 있도록 금고에서 `Approve()`를 호출해야 합니다.

악의적인 사용자는 다른 사용자로부터 라우터 주소에 대한 `allowance[][]`의 금액을 훔치거나 `transferFrom()#OmoRouter.sol`을 호출하는 사용자 거래를 프런트런하여 사용자가 승인한 모든 가치를 자신의 주소로 전송할 수 있습니다.

## 권장 사항

```diff
File: OmoRouter.sol

-     function transferFrom(uint256 vaultId,address from,address to,uint256 amount) external returns (bool) {
+     function transferFrom(uint256 vaultId,address to,uint256 amount) external returns (bool) {
         address vault = vaultFactory.getVault(vaultId);

-         return IOmoVault(vault).transferFrom(from, to, amount);
+         return IOmoVault(vault).transferFrom(msg.sender, to, amount);
     }
```

# [H-03] `OmoVault` 악성 계정은 금고를 영구적으로 비활성화할 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

- `OmoVault` 계약은 화이트리스트에 있는 사용자가 금고 소유자나 관리자의 승인 없이 계정(`OmoAgent`)을 등록할 수 있도록 허용합니다.
  악의적인 행위자는 가격 검색을 위해 유효하지 않은 오라클로 구성된 `OmoAgent` 계정을 등록하여 이를 악용할 수 있습니다. 이로 인해 `OmoVault.totalAssets()` 함수가 호출될 때마다 되돌려져 **금고에 대한 영구적인 서비스 거부(DoS)**가 발생합니다.

```javascript
function totalAssets() public view virtual override returns (uint256) {
        //...
        // Sum up values from all registered accounts
        for (uint256 i = 0; i < accountList.length; i++) {
            address account = accountList[i];
            if (registeredAccounts[account]) {
                IDynamicAccount dynamicAcc = IDynamicAccount(account);
                accountHoldings += dynamicAcc.getPositionValue(address(asset));
            }
        }
        //...
    }
```

- `totalAssets()`는 `deposit()`, `mint()` 및 `redeem()` 함수에서 공유 가치를 계산하는 데 사용되므로 시스템에서 **악의적이거나 손상된 계정(`OmoAgent`)을 제거하는 구현된 방법이 없기 때문에** 금고를 사용할 수 없게 되어 이러한 공격으로부터 복구할 수 없습니다.

## 권장 사항

- 이 문제에 대한 가능한 완화 조치:

1. 금고에 계정을 등록하는 기능을 **금고 소유자나 관리자로만** 제한하십시오.
2. 금고 소유자/관리자가 시스템에서 **악의적이거나 손상된 계정을 제거**할 수 있는 기능을 구현하십시오.
3. 단일 실패 오라클이 전체 함수를 되돌리지 않도록 **오라클 실패를 우아하게 처리**하도록 `totalAssets()`를 수정하십시오.

# [H-04] 교차 금고 오귀속(misattribution)으로 인한 잘못된 `OmoVaults` 평가

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

- `OmoAgent` 에이전트는 여러 `OmoVault` 금고와 상호 작용하여 자산을 빌리고 유동성 포지션을 dynamic account(`OmoAgent`)에 다시 예치하지만, `OmoVault.totalAssets()` 함수는 이러한 포지션이 해당 특정 금고에서 빌린 자산에서 생성되었는지 여부를 추적하지 않고 `OmoAgent`에 예치된 **모든** 유동성 포지션을 계상합니다.

```javascript
function totalAssets() public view virtual override returns (uint256) {
        uint256 vaultHoldings = _totalAssets;
        uint256 accountHoldings;

        // Sum up values from all registered accounts
        for (uint256 i = 0; i < accountList.length; i++) {
            address account = accountList[i];
            if (registeredAccounts[account]) {
                IDynamicAccount dynamicAcc = IDynamicAccount(account);
                accountHoldings += dynamicAcc.getPositionValue(address(asset));
            }
        }

        return vaultHoldings + accountHoldings;
    }
```

- 이로 인해 `totalAssets()`에 **다른 금고**의 차입 자산에서 비롯된 유동성 포지션의 가치가 포함되어 주가가 부풀려지고 금고 보유량이 부정확하게 평가되므로 금고 공유의 **과대평가**가 발생합니다.

## 권장 사항

1. `OmoAgent` 계약에 추적 메커니즘을 구현하여 예치된 유동성 포지션을 **해당 금고(`vaultId`)**와 연관시켜 각 포지션이 해당 자산을 여는 데 사용된 자산을 빌린 금고와 연결되도록 하십시오.
2. `OmoAgent`의 모든 포지션을 합산하는 대신 **해당 특정 금고에서 빌린 자산에서 생성된 포지션만 계상**하도록 `OmoVault.totalAssets()`를 수정하십시오.

# [H-05] `OmoAgent.topOffToAgent` 잘못된 포지션 검색 로직

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

- `OmoAgent.topOffToAgent()` 함수를 통해 에이전트는 예치된 포지션 NFT를 검색할 수 있지만 현재 구현에는 여러 가지 문제가 있습니다.

```javascript
function topOffToAgent(uint256 _agentId, address asset) external onlyAgent(_agentId) {
        INonfungiblePositionManager nftManager = INonfungiblePositionManager(OmoAgentStorage.data().positionManager);
        for (uint256 i = 0; i < OmoAgentStorage.data()._positionIds.length; i++) {
            uint256 tokenId = OmoAgentStorage.data()._positionIds[i];
            require(nftManager.getApproved(tokenId) == OmoAgentStorage.data().agents[_agentId], "Not approved");

            nftManager.transferFrom(address(this), OmoAgentStorage.data().agents[_agentId], tokenId);
            _removePositionId(tokenId);
        }

        uint256 amount = IERC20(asset).balanceOf(address(this));
        if(amount > 0) {
            IERC20(asset).transfer(OmoAgentStorage.data().agents[_agentId], amount);
        }

        emit TopOffToAgent(_agentId);
    }
```

1. **잘못된 루프 기반 검증:**

   - 이 함수는 **계약이 소유한 모든 포지션**을 반복하고 호출자(에이전트)가 소유한 각 포지션에 대해 승인되었는지 확인합니다.
   - 에이전트가 그중 하나라도 승인되지 않은 경우 **호출이 되돌려집니다**.
   - **여러 에이전트가 `DynamicAccount`와 상호 작용**하고 각 예치된 포지션을 예치한 에이전트와 연결하는 **추적 메커니즘이 없기 때문에** 이 가정은 부정확합니다.

2. **불필요한 승인 확인:**
   - 이 함수는 다시 전송하기 전에 **에이전트가 포지션 ID에 대해 승인되었는지** 확인하지만, **DynamicAccount가 특정 에이전트에게 NFT 승인을 허용하도록 외부 호출을 수행할 수 있는 구현된 함수가 계약에 없습니다**.
   - 이 확인은 **제대로 시행될 수 없으며** **에이전트가 예치된 포지션을 검색하는 것을 방지**합니다.

## 권장 사항

- **각 포지션 ID를 예치한 에이전트에 매핑**하여 각 예치된 포지션에 대한 추적을 구현하십시오.
- 포지션이 **원래 예치자에게 다시 전송**될 때는 불필요하므로 `nftManager.getApproved(tokenId) == OmoAgentStorage.data().agents[_agentId]` 확인을 제거하십시오.

# [H-06] OmoOracle `getUSDValue` 및 `convertUSDToToken`에서 잘못된 토큰 소수점 처리

## **심각도**

**영향:** 높음

**가능성:** 중간

## **설명**

OmoOracle 계약의 `getUSDValue()` 함수는 금액을 변환할 때 토큰 소수점을 고려하지 않습니다. 모든 토큰의 소수점 자릿수(1e18)가 동일하다고 가정하는데, 이는 잘못된 변환 및 금액 계산으로 이어질 수 있습니다. 이 함수는 다음 접근 방식을 사용합니다.

```solidity
    function getUSDValue(address token, uint256 amount) public view returns (uint256) {
        --snip--
        uint8 decimals = priceFeed.decimals();
        uint256 normalizedPrice = uint256(price) * 1e18 / 10**decimals;

        return (amount * normalizedPrice) / 1e18;
    }
```

## 권장 사항

소수점을 고려하여 자산 금액 변환을 올바르게 처리하는 방법은 변환에 관련된 각 토큰의 해당 소수점을 조정하는 것입니다. 공식은 다음과 같아야 합니다.

```
amountUSD = (amountToken * priceTokenToUSD * decimalsUSD) / (decimalsToken * 1e18);
```

# [H-07] OmoOracle 풀 주소를 가져오기 위한 Uniswap 인터페이스의 잘못된 사용

## **심각도**

**영향:** 높음

**가능성:** 중간

## **설명**

`getLiquidityAmounts()` 함수는 Uniswap 인터페이스를 잘못 사용하여 풀 주소를 얻습니다.

```solidity
    function getLiquidityAmounts(
        address positionManager,
        uint256 tokenId,
        uint128 liquidity
    ) internal view returns (uint256 amount0, uint256 amount1) {
        if (liquidity == 0) return (0, 0); // Handle zero liquidity case
        INonfungiblePositionManager nftManager = INonfungiblePositionManager(positionManager);

        --snip--

        IUniswapV3Pool pool = IUniswapV3Pool(nftManager.factory()); //Wrong pool address
        (uint160 sqrtPriceX96,,,,,,) = pool.slot0();

        // Calculate amounts using UniswapV3 math
        (amount0, amount1) = LiquidityAmounts.getAmountsForLiquidity(
            sqrtPriceX96,
            TickMath.getSqrtRatioAtTick(tickLower),
            TickMath.getSqrtRatioAtTick(tickUpper),
            liquidity
        );
    }
```

## 권장 사항

올바른 인터페이스를 사용하여 풀 주소 가져오기

이 문제를 해결하려면 `tokenId`에 속한 풀을 가져오고 `nftManager.positions(tokenId);` 호출에서 `token0`, `token1` 및 `fee`를 캐치해야 합니다.
그리고 다음과 같이 사용하십시오.

```diff
File: OmoOracle.sol#getLiquidityAmounts()

         INonfungiblePositionManager nftManager = INonfungiblePositionManager(positionManager);
         /*code*/

-        IUniswapV3Pool pool = IUniswapV3Pool(nftManager.factory());
+        address poolAddress = IUniswapV3Factory(nftManager.factory()).getPool(token0, token1, fee);
+        IUniswapV3Pool pool = IUniswapV3Pool(poolAddress);
        (uint160 sqrtPriceX96,,,,,,) = pool.slot0();
```

# [H-08] OmoOracle `getLiquidityAmounts()`는 스팟 가격을 사용하여 조작 가능하게 만듦

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`OmoOracle`의 `getPositionValue()` / `getLiquidityAmounts()`는 Uniswap의 스팟 가격에 의존하여 포지션의 토큰 금액을 계산합니다. 이로 인해 시스템은 **가격 조작**에 취약해집니다.

```solidity
    function getLiquidityAmounts(
        address positionManager,
        uint256 tokenId,
        uint128 liquidity
    ) internal view returns (uint256 amount0, uint256 amount1) {

--snip--

        IUniswapV3Pool pool = IUniswapV3Pool(nftManager.factory());
        (uint160 sqrtPriceX96,,,,,,) = pool.slot0();

        // Calculate amounts using UniswapV3 math
        (amount0, amount1) = LiquidityAmounts.getAmountsForLiquidity(
            sqrtPriceX96,
            TickMath.getSqrtRatioAtTick(tickLower),
            TickMath.getSqrtRatioAtTick(tickUpper),
            liquidity
        );
    }
```

## 권장 사항

대신 시간 가중 평균 가격(TWAP) 사용

# [H-09] OmoAgent: `getPositionValue()`는 예치된 자산을 고려하지 않음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`totalAssets()` 함수는 총 자산을 계산하기 위해 모든 dynamic accounts에 대해 `getPositionValue()`를 호출합니다. `OmoAgent` 계약에서 `getPositionValue()` 함수는 포지션 가치를 계산할 때 예치된 자산을 고려하지 않습니다. 이로 인해 `OmoVault`에서 `totalAssets()`가 잘못 계산되어 부정확한 자산 추적으로 인해 사용자의 자금 손실이 발생할 수 있습니다.
**참고**: 에이전트는 `depositAssets()` 기능을 통해 `OmoAgent`에 자산(포지션뿐만 아니라)을 예치할 수 있습니다.

## 권장 사항

총 포지션 가치를 계산할 때 예치된 자산의 가치를 포함하도록 `getPositionValue()`를 수정하십시오.

# [H-10] `_validateSignature()`가 `address(0)`을 제대로 처리하지 않음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`_validateSignature()`의 현재 구현은 `ECDSA.recover()`를 사용하여 서명자 주소를 추출하는데, 유효하지 않은 서명에 대해 `address(0)`을 반환할 수 있습니다.

```solidity
    function _validateSignature(
        UserOperation calldata userOp,
        bytes32 userOpHash
    ) internal virtual override returns (uint256 validationData) {
        bytes32 hash = ECDSA.toEthSignedMessageHash(userOpHash);
        address signer = ECDSA.recover(hash, userOp.signature);

        // Check if signer is admin (user wallet)
        if (isAdmin(signer)) return 0;

        // Check if signer is an authorized agent
        if (OmoAgent(payable(address(this))).agents(0) == signer) {
            return 0;
        }
        return SIG_VALIDATION_FAILED;
    }
```

그러나 `agent(0)`이 설정되지 않은 경우(예: `address(0)`), 함수는 서명이 유효하지 않더라도 서명 유효성 검사를 잘못 통과할 수 있습니다. 복구된 주소가 `address(0)`일 수 있다는 가능성을 고려하지 않고 실수로 유효한 서명자로 취급하기 때문입니다. 이로 인해 유효하지 않은 서명이 허용되는 보안 문제가 발생할 수 있습니다.

## 권장 사항

복구된 주소에서 `address(0)`을 명시적으로 확인하고 유효하지 않은 서명이 허용되지 않도록 하십시오. 복구된 주소가 `address(0)`이면 서명은 유효하지 않은 것으로 간주되어야 합니다.

복구된 서명자가 `address(0)`이 아닌지 확인하도록 `DynamicAccount._validateSignature()` 함수를 업데이트하십시오.

```diff
function _validateSignature(
       UserOperation calldata userOp,
       bytes32 userOpHash
   ) internal virtual override returns (uint256 validationData) {
       bytes32 hash = ECDSA.toEthSignedMessageHash(userOpHash);
       address signer = ECDSA.recover(hash, userOp.signature);
+       if (signer == address(0)) return SIG_VALIDATION_FAILED;
       //...
   }
```

# [H-11] `redeem()`의 보류 중인 인출 토큰이 주가에 영향을 미침

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`redeem()` 함수의 보류 중인 인출 토큰은 여전히 주가 계산에 고려됩니다.
`totalSupply` 및 `_totalAssets`는 보류 중인 인출을 반영하도록 업데이트되지 않기 때문입니다.

```solidity
        // Transfer shares from owner to vault
        balanceOf[owner] -= shares;
        balanceOf[address(this)] += shares;
```

주가는 나중에 발생하는 이익/손실에 대해 부정확할 것입니다. 보류 중인 인출이 완료되면(`topOff` 호출) 주가는 이전에 고려되지 않은 인출을 설명하도록 조정됩니다. 이로 인해 공유 가치 평가의 불일치가 발생하여 시간이 지남에 따라 사용자 간에 잘못된 자금 분배가 발생할 수 있습니다.

## 권장 사항

완료될 때까지 보류 중인 인출 토큰을 제외하도록 주가 계산을 업데이트하십시오. 이를 통해 `totalSupply` 및 `totalAssets`가 금고의 현재 상태를 정확하게 반영하고 정확한 주가를 제공할 수 있습니다.

# [H-12] `getPositionValue()`는 누적 수수료를 고려하지 않음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

`getPositionValue()` 함수는 `getLiquidityAmounts()`를 사용하여 유동성 토큰 금액만 검색하고 이를 견적(quote) 토큰 가치로 변환합니다. 그러나 누적 수수료는 계상하지 않습니다.

```solidity

    function getPositionValue(
        address positionManager,
        uint256 tokenId,
        address quoteToken
    ) external view override returns (uint256) {
        // Get position details
        (,, address token0, address token1,,,, uint128 liquidity,,,,) = INonfungiblePositionManager(positionManager).positions(tokenId);

        // Get amounts
        (uint256 amount0, uint256 amount1) = getLiquidityAmounts(positionManager, tokenId, liquidity);

        // Convert to quote token value
        uint256 value0 = convertTokenValue(token0, amount0, quoteToken);
