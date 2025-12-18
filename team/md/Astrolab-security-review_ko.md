# 정보

**Pashov Audit Group**은 이 분야 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 결코 보장할 수 없지만, 경험이 풍부한 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락하세요.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 최대한 많은 취약점을 찾으려고 노력하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**strats** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Astrolab 소개

Astrolab DAO는 규모에 따른 우수한 수익률을 제공하여 수익 집계(yield aggregation)를 재정의하는 것을 목표로 합니다. 이를 수행하는 방법은 온체인 최고 수준의 유동성 수집기(aggregator)와 알고리즘 실행을 활용하여 교차 체인 다각화 및 자본 효율성을 극대화하는 것입니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜의 자산에 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼침.

- 중간 (Medium) - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼침.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼침.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 공격 비용이 도난당하거나 손실될 수 있는 자금의 양에 비해 상대적으로 낮음.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 있음.

- 낮음 (Low) - 너무 많거나 너무 가능성이 낮은 가정을 포함하거나 인센티브가 거의 없거나 없는 상태에서 공격자가 상당한 지분을 필요로 함.

## 심각도 수준에 따른 필요 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약

**_검토 커밋 해시_ - [afe24b344f9e25104cbdd240d5d4448f01b06e07](https://github.com/AstrolabDAO/strats/tree/afe24b344f9e25104cbdd240d5d4448f01b06e07)**

**_수정 검토 커밋 해시_ - [f34c311f3c5fe6adac4522a9f2a9ead75b8d639a](https://github.com/AstrolabDAO/strats/tree/f34c311f3c5fe6adac4522a9f2a9ead75b8d639a)**

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다:

- `abstract/AsAccessControl`
- `abstract/AsRescuable`
- `abstract/StrategyV5Abstract`
- `abstract/As4626`
- `abstract/StrategyV5`
- `abstract/StrategyV5Chainlink`
- `abstract/AsManageable`
- `abstract/StrategyV5Agent`
- `abstract/StrategyV5Pyth`
- `abstract/As4626Abstract`
- `libs/AsAccounting`

# 발견 사항

# [C-01] 상환 요청 부풀리기 및 볼트 내 서비스 거부(DoS)

## 심각도

**영향:** 높음, 볼트의 모든 주요 기능이 중단되어 서비스 거부로 이어질 수 있으므로

**가능성:** 높음, 모든 볼트 지분 소유자가 쉽게 악용할 수 있으므로

## 설명

`requestRedeem`에서 함수는 상환 요청된 지분이 소유자의 지분 잔액보다 크지 않은지 확인합니다. 그러나 operator 매개변수는 검증되지 않습니다. 이를 통해 사용자는 서로 다른 operator 주소로 상환 요청을 반복적으로 제출하여 `req.totalRedemption` 값을 부풀릴 수 있습니다. `req.totalRedemption`은 `totalAssets`보다 커지도록 부풀려질 수 있습니다.

        function requestRedeem(
            uint256 shares,
            address operator,
            address owner
        ) public nonReentrant {
            if (owner != msg.sender || shares == 0 || balanceOf(owner) < shares)
                revert Unauthorized();
 
 `totalRedemption`의 이러한 팽창은 이후 키퍼(keeper)에 의한 liquidate 함수 호출 동안 `pendingRedemption`의 증가로 이어지며, 이는 다시 `req.totalClaimableRedemption`을 부풀립니다.

        uint256 pendingRedemption = totalPendingRedemptionRequest();

        req.totalClaimableRedemption += pendingRedemption;

부풀려진 `req.totalClaimableRedemption`은 `sharePrice()` 함수를 방해하여 지속적으로 되돌아가게(revert) 만듭니다. 예금, 발행, 인출 및 상환과 같은 중요한 작업이 `sharePrice` 함수에 의존하므로 이는 문제가 됩니다.

        function sharePrice() public view virtual returns (uint256) {
            uint256 supply = totalAccountedSupply();
            return
                supply == 0
                    ? weiPerShare
                    : totalAccountedAssets().mulDiv( // eg. e6
                        weiPerShare ** 2, // 1e8*2
                        supply * weiPerAsset
                    ); // eg. (1e6+1e8+1e8)-(1e8+1e6)
        }

`totalAssets`가 부풀려진 `req.totalClaimableRedemption`보다 작기 때문에 `totalAccountedAssets` 계산이 음수 값을 반환하므로 sharePrice 함수는 되돌아갑니다. 이 계산 오류로 인해 전체 프로토콜에 대한 DoS가 발생합니다.

        function totalAccountedAssets() public view returns (uint256) {
            return
                totalAssets() -
                req.totalClaimableRedemption.mulDiv(
                    last.sharePrice * weiPerAsset,
                    weiPerShare ** 2
                ); // eg. (1e8+1e8+1e6)-(1e8+1e8) = 1e6
        }

## 권장 사항

`requestRedeem` 함수에서 `operator`를 검증하고 소유자가 자신의 지분을 사용하여 한 번만 상환을 요청할 수 있도록 하십시오.

# [C-02] `cancelRedeemRequest`에서 매핑 대상의 잘못된 사용

## 심각도

**영향:** 높음, 다른 사람의 토큰을 소각할 수 있음

**가능성:** 높음, 무허가(permissionless)로 수행될 수 있음

## 설명

사용자는 `cancelRedeemRequest()`를 호출하여 상환 요청을 취소할 수 있으며, 코드는 유휴 자금으로 인해 발생한 손실(기회 비용)에서 초과 지분을 소각합니다. 문제는 코드가 operator가 소유자의 자금에 대한 허용량을 가지고 있는지 확인하지 않으며, 누구나 이 함수를 호출하여 다른 사람의 토큰을 소각할 수 있다는 것입니다.

또한 두 번째 실수는 코드가 `req.byOperator[owner]` 대신 `req.byOperator[operator]`를 사용한다는 것입니다.

POC는 다음과 같습니다:

1. 공격자는 계약의 가격이 1.5일 때 자신의 ADD1 주소와 AMOUNT1에 대해 상환 요청을 생성합니다.
2. 시간이 지나 계약의 가격이 2가 되면 공격자는 자신의 ADD1 주소로 `cancelRedeemRequest(owner=target_user, operator=ADD1)`를 호출할 수 있습니다.
3. 이제 `msg.sender == operator`이므로 코드는 호출을 허용하며 소유자와 운영자에 대한 허용량 확인도 없습니다.
4. 결국 가격이 1.5에서 2로 상승했기 때문에 코드는 소유자의 지분 일부를 소각합니다.

```solidity
    function cancelRedeemRequest(
        address operator,
        address owner
    ) external nonReentrant {

        if (owner != msg.sender && operator != msg.sender)
            revert Unauthorized();

        Erc7540Request storage request = req.byOperator[operator];
        uint256 shares = request.shares;

        if (shares == 0) revert AmountTooLow(0);

        last.sharePrice = sharePrice();

        if (last.sharePrice > request.sharePrice) {
            // burn the excess shares from the loss incurred while not farming
            // with the idle funds (opportunity cost)
            uint256 opportunityCost = shares.mulDiv(
                last.sharePrice - request.sharePrice,
                weiPerShare
            ); // eg. 1e8+1e8-1e8 = 1e8
            _burn(owner, opportunityCost);
        }

```

`requestRedeem()` 및 `_withdraw()`에도 주의가 필요한 유사한 버그가 있습니다.

## 권장 사항

`cancelRedeemRequest()`에서 `req.byOperator[owner]`를 사용하고 operator가 소유자 토큰에 대한 허용량을 가지고 있는지 확인하십시오.
`requestRedeem()`에서 코드는 `req.byOperator[owner]`에 정보를 저장하고 operator가 소유자 자금에 대한 허용량을 가지고 있는지 확인해야 합니다.
`_withdraw()`에서 `req.byOperator[_owner]`를 사용하십시오.

# [H-01] 역할 취소 시 계정이 역할에서 제대로 제거되지 않음

## 심각도

**영향:** 높음 - 역할에서 제거된 계정, 특히 기본 관리자와 같은 중요한 역할의 계정이 해당 역할의 권한에 대한 액세스 권한을 유지합니다. 이는 심각한 보안 위험을 초래합니다.

**가능성:** 중간 - `revokeRole` 함수가 호출될 때마다 지속적으로 문제가 발생합니다.

## 설명

계정이 역할에서 취소되면 `_revokeRole` 함수는 멤버 세트에서 계정을 제거합니다.

        function _revokeRole(bytes32 role, address account) internal virtual {
            if (hasRole(role, account)) {
                _roles[role].members.remove(account.toBytes32());
                emit RoleRevoked(role, account, msg.sender);
            }
        }

`AsSequentialSet.sol` 라이브러리에서 역할은 `data` 배열과 bytes32에서 uint32로의 매핑 `index`를 포함하는 `Set` 구조체에 저장됩니다.

        struct Set {
            bytes32[] data;
            mapping(bytes32 => uint32) index;
        }

라이브러리의 `remove` 함수는 요소의 제거를 처리해야 하지만 `Set.data` 배열에서만 계정을 제거하고 `Set.index`를 재설정하지 않는 `removeAt`을 호출합니다.

        function remove(Set storage q, bytes32 o) internal {
            uint32 i = q.index[o];
            require(i > 0, "Element not found");
            removeAt(q, i - 1);
        }

        function removeAt(Set storage q, uint256 i) internal {
            require(i < q.data.length, "Index out of bounds");
            if (i < q.data.length - 1) {
                delete q.data[i];
                q.data[i] = q.data[q.data.length - 1];
            }
            q.data.pop();
        }

따라서 hasRole 함수가 `has` 함수를 사용하여 사용자가 역할을 가지고 있는지 확인할 때.

        function hasRole(bytes32 role, address account) public view virtual returns (bool) {
            return _roles[role].members.has(account.toBytes32());
        }

그리고 `has` 함수는 해당 계정의 인덱스만 확인하며 인덱스는 여전히 존재합니다. 이는 사용자가 역할에서 제거된 후에도 여전히 역할을 가지고 있음을 의미합니다.

        function has(Set storage q, bytes32 o) internal view returns (bool) {
            return q.index[o] > 0 && q.index[o] <= q.data.length;
        }

## POC

test/POC.t.sol 파일에 넣으십시오.

https://gist.github.com/thangtranth/685dd8fa7faae141cdd2b1d0061b16f5

## 권장 사항

`AsSequentialSet.sol`의 `remove` 함수는 제거된 계정의 `index`를 재설정하도록 수정되어야 합니다.

```diff
    function remove(Set storage q, bytes32 o) internal {
        uint32 i = q.index[o];
+       q.index[o] = 0;
        require(i > 0, "Element not found");
        removeAt(q, i - 1);
    }
```

# [H-02] 플래시 론 잔액 확인 오류

## 심각도

**영향:** 중간, 플래시 론 기능이 영향을 받기 때문

**가능성:** 높음, 플래시 론이 사용될 때마다 되돌아가기 때문

## 설명

`balanceBefore`는 플래시 론 금액이 전송되기 전에 기록됩니다. 결과적으로 714행에서 `balanceAfter`는 필요한 것보다 더 많아야 합니다. 사용자는 플래시 론을 사용하기 위해 동일한 금액으로 추가 지불해야 합니다.

```solidity
File: src\abstract\As4626.sol
696:     function flashLoanSimple() external nonReentrant {
275: 
705:         uint256 fee = exemptionList[msg.sender] ? 0 : amount.bp(fees.flash);
706:         uint256 toRepay = amount + fee;
707:
708:         uint256 balanceBefore = asset.balanceOf(address(this));
709:         totalLent += amount;
710:
711:         asset.safeTransferFrom(address(this), address(receiver), amount);
712:         receiver.executeOperation(address(asset), amount, fee, msg.sender, params);
713:
714:         if ((asset.balanceOf(address(this)) - balanceBefore) < toRepay)
715:             revert FlashLoanDefault(msg.sender, amount);
287: 
718:     }
```

## 권장 사항

`toRepay` 변수를 삭제하거나 `safeTransferFrom` 이후에 `balanceBefore`를 기록하십시오:

```diff
File: src\abstract\As4626.sol
696:     function flashLoanSimple() external nonReentrant {
298: 
705:         uint256 fee = exemptionList[msg.sender] ? 0 : amount.bp(fees.flash);
300: - 706:         uint256 toRepay = amount + fee;
707:
708:         uint256 balanceBefore = asset.balanceOf(address(this));
709:         totalLent += amount;
710:
711:         asset.safeTransferFrom(address(this), address(receiver), amount);
712:         receiver.executeOperation(address(asset), amount, fee, msg.sender, params);
713:
308: - 714:         if ((asset.balanceOf(address(this)) - balanceBefore) < toRepay)
309: + 714:         if ((asset.balanceOf(address(this)) - balanceBefore) < fee)
715:             revert FlashLoanDefault(msg.sender, amount);
311: 
718:     }
313: 
```

# [H-03] transferFrom 문제로 인해 플래시 론이 작동하지 않음

## 심각도

**영향:** 중간 - 플래시 론 기능이 작동하지 않지만 자금 손실 위험은 없습니다.

**가능성:** 높음 - 플래시 론 함수가 의도한 대로 실행되지 않습니다.

## 설명

사용자가 `flashLoanSimple` 함수를 사용하려고 할 때 문제가 발생합니다:

```solidity
        function flashLoanSimple(
            IFlashLoanReceiver receiver,
            uint256 amount,
            bytes calldata params
        ) external nonReentrant {

            uint256 available = availableBorrowable();
            if (amount > available || amount > maxLoan) revert AmountTooHigh(amount);

            uint256 fee = exemptionList[msg.sender] ? 0 : amount.bp(fees.flash);
            uint256 toRepay = amount + fee;

            uint256 balanceBefore = asset.balanceOf(address(this));
            totalLent += amount;

@>          asset.safeTransferFrom(address(this), address(receiver), amount);
            receiver.executeOperation(address(asset), amount, fee, msg.sender, params);

            if ((asset.balanceOf(address(this)) - balanceBefore) < toRepay)
                revert FlashLoanDefault(msg.sender, amount);

            emit FlashLoan(msg.sender, amount, fee);
        }
```

사용자에게 자금을 이체하기 위해 `asset.safeTransferFrom(address(this), address(receiver), amount);`를 사용합니다.

이 줄은 사용자에게 자금을 이체하려고 합니다. 그러나 `safeTransferFrom`은 계약이 자신을 대신하여 "지출"할 수 있는 충분한 `allowance`를 가지고 있어야 하므로 실패합니다. USDC와 같은 ERC20 토큰의 맥락에서 transferFrom 함수는 중요한 검사를 포함합니다: `value <= allowed[from][msg.sender]`

그러나 계약이 아직 스스로를 승인하지 않았기 때문에 허용량이 0으로 유지되어 `transferFrom` 호출이 되돌아갑니다.

        function transferFrom(
            address from,
            address to,
            uint256 value
        )
            external
            override
            whenNotPaused
            notBlacklisted(msg.sender)
            notBlacklisted(from)
            notBlacklisted(to)
            returns (bool)
        {
            require(
                value <= allowed[from][msg.sender],
                "ERC20: transfer amount exceeds allowance"
            );
            _transfer(from, to, value);
            allowed[from][msg.sender] = allowed[from][msg.sender].sub(value);
            return true;
        }

USDC - FiatTokenV1.sol: https://arbiscan.io/address/0xaf88d065e77c8cc2239327c5edb3a432268e5831

`transfer`를 사용하면 추가 승인이 필요하지 않습니다.

## 권장 사항

`safeTransferFrom` 함수를 `safeTransfer`로 교체하십시오:

```diff
        function flashLoanSimple(
            IFlashLoanReceiver receiver,
            uint256 amount,
            bytes calldata params
        ) external nonReentrant {

            uint256 available = availableBorrowable();
            if (amount > available || amount > maxLoan) revert AmountTooHigh(amount);

            uint256 fee = exemptionList[msg.sender] ? 0 : amount.bp(fees.flash);
            uint256 toRepay = amount + fee;

            uint256 balanceBefore = asset.balanceOf(address(this));
            totalLent += amount;

-           asset.safeTransferFrom(address(this), address(receiver), amount);
+           asset.safeTransfer(address(receiver), amount);
            receiver.executeOperation(address(asset), amount, fee, msg.sender, params);

            if ((asset.balanceOf(address(this)) - balanceBefore) < toRepay)
                revert FlashLoanDefault(msg.sender, amount);

            emit FlashLoan(msg.sender, amount, fee);
        }
```

# [H-04] `withdraw()` 최악의 가격은 `sharePrice()`를 왜곡시킬 것임

## 심각도

**영향:** 높음, 부정확한 가격은 장기적인 가치 유출을 유발하며 수정할 수 없으므로

**가능성:** 중간, 기존 요청으로 `withdraw()`할 때마다 시스템에 오류가 발생하므로

## 설명

`_withdraw()`에서 최악의 가격이 사용됩니다. 즉, 실제 인출 가격은 `last.sharePrice`와 다릅니다.

```solidity
File: src\abstract\As4626.sol
149:     function _withdraw() internal nonReentrant returns (uint256) {
159:         last.sharePrice = sharePrice();

161:         uint256 price = (claimable >= _shares)
162:             ? AsMaths.min(last.sharePrice, request.sharePrice) // worst of if pre-existing request
163:             : last.sharePrice; // current price
```

`last.sharePrice`는 다음 경우에만 업데이트되며 실제 인출 가격을 고려하지 않습니다.

```solidity
File: src\abstract\As4626.sol
84:     function _deposit() internal nonReentrant returns (uint256) {
93:         last.sharePrice = sharePrice();

149:     function _withdraw() internal nonReentrant returns (uint256) {
159:         last.sharePrice = sharePrice();

512:     function requestRedeem() public nonReentrant {
523:         last.sharePrice = sharePrice();

577:     function cancelRedeemRequest() external nonReentrant {
590:         last.sharePrice = sharePrice();
```

`sharePrice()`가 어떻게 계산되는지 살펴보겠습니다:
먼저 `totalAccountedAssets()`를 호출하고, `totalAccountedAssets()`에서 `last.sharePrice`가 직접 사용됩니다. 가정은: `last.sharePrice`가 각 `deposit()/withdraw()`마다 일정하게 유지된다는 것 같습니다. 그러나 최악의 가격 사용으로 인해 유효하지 않습니다.

```solidity
File: src\abstract\As4626Abstract.sol
175:     function sharePrice() public view virtual returns (uint256) {
176:         uint256 supply = totalAccountedSupply();
177:         return
178:             supply == 0
179:                 ? weiPerShare
180:  @>>>>          : totalAccountedAssets().mulDiv( // eg. e6
181:                     weiPerShare ** 2, // 1e8*2
182:                     supply * weiPerAsset
183:                 ); // eg. (1e6+1e8+1e8)-(1e8+1e6)
184:     }

154:     function totalAccountedAssets() public view returns (uint256) {
155:         return
156:             totalAssets() -
157:             req.totalClaimableRedemption.mulDiv(
158:  @>>>>          last.sharePrice * weiPerAsset,
159:                 weiPerShare ** 2
160:             ); // eg. (1e8+1e8+1e6)-(1e8+1e8) = 1e6
161:     }
```

요약하면, `request.sharePrice < sharePrice()`일 때, 인출은 현재보다 낮은 가격으로 처리되며, 이 경우 불일치로 인해 유효 `sharePrice` 증가가 발생하여 `last.sharePrice`에 반영되지 않습니다.

숫자 예시를 살펴보겠습니다:

- 처음에 총 지분은 100, 총 자산은 500, sharePrice는 5입니다.
- 20 지분을 가진 어떤 사용자가 가격 5에 인출을 요청합니다. totalClaimableRedemption은 40입니다(총 20% 보류 중).
- 오랜 시간 후, 총 자산은 1000이 되고, sharePrice는 10이며, 유동성은 충분합니다.
- Alice는 가격 5에 withdraw()를 호출합니다(`_withdraw()`의 최악의 가격으로 인해), 총 자산은 80이 됩니다(totalClaimableRedemption에서 60 + 20).

이제 총 자산은 1000 - 20 \* 5 = 900입니다. 그러나 `last.sharePrice`는 As4626.sol#159 (last.sharePrice = sharePrice())로 인해 10으로 유지됩니다. 실제 가격은 900 / 80 = 11.25여야 합니다.

다음 번에 `totalAccountedAssets()`는 900 - 20 \* 10 = 700을 반환하고, `sharePrice()`는 700 / 60 = 11.67을 반환하여 약 3.7% 차이(11.67~11.25)가 발생합니다.

따라서 어떤 사용자가 소량을 예치하려고 하면 3.7%의 슬리피지가 손실됩니다.

```solidity
File: src\abstract\As4626Abstract.sol
154:     function totalAccountedAssets() public view returns (uint256) {
155:         return
156:             totalAssets() -
157:             req.totalClaimableRedemption.mulDiv(
158:                 last.sharePrice * weiPerAsset,
159:                 weiPerShare ** 2
160:             ); // eg. (1e8+1e8+1e6)-(1e8+1e8) = 1e6
161:     }
```

일반적인 상황에서는 last.sharePrice와 request.sharePrice가 상대적으로 가깝다면 sharePrice는 약간만 다를 것이라고 생각합니다.

## 권장 사항

가능한 완화책 중 하나는 `deposit()/withdraw()` 후에 `last.sharePrice`를 업데이트하는 것입니다.

# [H-05] `mint`, `deposit`, `redeem` 및 `withdraw`에서의 수수료 계산 불일치

## 심각도

**영향:** 중간, 수수료가 조금 더 높거나 낮을 것임

**가능성:** 높음, mint 및 deposit 함수 호출마다 발생하므로

## 설명

사용자가 `mint(shares)`를 호출하면 코드는 `_deposit(previewMint(_shares), _shares`를 호출하고 `previewMint(shares) = convertToAssets(shares).addBp()`입니다.
사용자가 `deposit(amount)`를 호출하면 코드는 `_deposit(_amount, previewDeposit(_amount)`를 호출하고 `previewDeposit(amount) = convertToShares(amount).subBp`입니다.

가격이 1:1이고 수수료가 10%라고 가정하고 두 가지 경우를 확인해 보겠습니다:

1. 사용자가 100 지분을 발행(mint)하려면 `mint(100)`을 호출하고 코드는 `amount = previewMint(100) = convertToAssets(100).addBp(10%) = 110`을 계산합니다. 따라서 결국 사용자는 110 자산을 지불하고 100 지분을 받으며 10 자산은 수수료가 됩니다.
2. 사용자가 110 자산을 예치(deposit)하려면 `deposit(110)`을 호출하고 코드는 `share = previewDeposit(110) = convertToShare(110).subBp(10%) = 99`를 계산합니다. 따라서 결국 사용자는 11 자산을 지불하고 99 지분을 받으며 11 자산은 수수료가 됩니다.

보시다시피 `deposit()` 호출은 사용자에게 과다 청구합니다. 그 이유는 코드가 `subBp()`를 사용하여 사용자 지정 금액을 기준으로 수수료를 계산하지만 사용자 지정 금액은 `amount + fee`여야 하므로 수수료 계산은 `.... * base / (base +fee)`여야 하기 때문입니다.

---

사용자가 `redeem(shares)`를 호출하면 코드는 `_withdraw(previewRedeem(_shares), _shares)`를 호출하고 `previewRdeem(shares) = convertToAssets(_shares).subBp()`입니다.

사용자가 `withdraw()`를 호출하면 코드는 `_withdraw(_amount, previewWithdraw(_amount))`를 호출하고 `previewWithdraw(_amount) = convertToShares(_assets).addBp()`입니다.

자산 대 지분 가격이 1:1이고 수수료가 10%라고 가정하고 두 가지 경우를 확인해 보겠습니다:

1. 사용자가 110 지분을 상환(redeem)하려면 `redeem(110)`을 호출하고 코드는 `amount = previewRdeem(110) = convertToAssets(110).subBp(10%) = 99`를 계산합니다. 따라서 결국 사용자는 110 지분을 소각하고 99 자산을 받으며 11 자산은 수수료가 됩니다.
2. 사용자가 100 자산을 인출(withdraw)하려면 `withdraw(100)`을 호출하고 코드는 `shares = previewWithdraw(100) = convertToShares(100).addBp(10%) = 110`을 계산합니다. 따라서 결국 사용자는 110 지분을 소각하고 100 자산을 받으며 10 자산은 수수료가 됩니다.

보시다시피 `redeem()`은 사용자에게 과다 청구합니다. 그 이유는 코드가 `subBp()`를 사용하여 사용자 제공 지분을 기준으로 수수료를 계산하지만 제공된 금액은 총 금액(burnAmount + fee)이며 계산은 `..... * base / (base + fee)`여야 하기 때문입니다.

## 권장 사항

`deposit()`에 대한 수수료를 `convertToShare(amount) * base / (base +fee)`로 계산하십시오.
`previewRedeem()`에 대한 수수료를 `convertToAssets(shares) * base / (base + fee)`로 계산하십시오.

# [H-06] `deposit`, `mint`, `withdraw`, `redeem` 및 `preview***` 메서드에서의 수수료 대상 불일치

## 심각도

**영향:** 높음, 이중 지출을 유발하고 풀 계산을 방해할 수 있으므로

**가능성:** 중간, 예외 수신자가 일반 주소에 대해 관리자에 의해 설정되기 때문

## 설명

사용자가 토큰을 예치하려고 하면 코드는 `previewDeposit()` 및 `previewMint()`에서 수수료를 계산하고 금액/지분에서 더하거나 뺍니다. 보시다시피 코드는 `msg.sender`를 기반으로 진입 수수료를 확인합니다.

```solidity
    function previewDeposit(
        uint256 _amount
    ) public view returns (uint256 shares) {
        return convertToShares(_amount).subBp(exemptionList[msg.sender] ? 0 : fees.entry);
    }

    function previewMint(uint256 _shares) public view returns (uint256) {
        return convertToAssets(_shares).addBp(exemptionList[msg.sender] ? 0 : fees.entry);
    }
```

`_deposit()`에서 코드는 수수료를 회계 처리하고 추적하려고 하며 다음 작업을 수행합니다:

```solidity
        if (!exemptionList[_receiver])
            claimableAssetFees += _amount.revBp(fees.entry);
```

보시다시피 호출자가 제어할 수 있는 `_receiver` 변수를 사용합니다.

따라서 두 개의 주소를 가진 공격자: `exemptionList[]`로 설정된 RECV1 주소와 `exemptionList[]`로 설정되지 않은 ADDERSS1은 ADDRESS1로 mint 및 deposit을 호출하고 수신자를 RECV1로 설정할 수 있습니다. preview 함수에서 코드는 사용자 예치금에 대한 수수료를 추가하지 않으며(또는 지분에서 빼지 않음) 코드는 사용자가 수수료를 지불하지 않았다고 가정하지만, `_deposit()` 함수에서 코드는 RECV1 주소를 확인하고 누적 수수료에 계산된 수수료를 추가합니다. 결국 사용자는 수수료를 지불하지 않았지만 코드는 수수료를 추가했고 이중 지출이 발생합니다.

공격자는 다른 시나리오를 사용하여 이 문제를 수행할 수도 있습니다. (코드는 수수료를 계산하고 호출자는 지불하지만 코드는 `claimableAssetFees`에 추가하지 않음)

---

사용자가 토큰을 인출하려고 하면 코드는 수수료를 부과하고 금액/지분에서 수수료를 더하거나/빼는 방식으로 preview 함수에서 수행됩니다. 보시다시피 코드는 `msg.sender`를 사용하여 `exemptionList[]`를 기반으로 종료 수수료를 확인합니다.

```solidity
    function previewWithdraw(uint256 _assets) public view returns (uint256) {
        return convertToShares(_assets).addBp(exemptionList[msg.sender] ? 0 : fees.exit);
    }

    function previewRedeem(uint256 _shares) public view returns (uint256) {
        return convertToAssets(_shares).subBp(exemptionList[msg.sender] ? 0 : fees.exit);
    }
```

그러나 `_withdraw()` 함수에서 코드가 수수료를 계산하고 누적하려고 할 때 `_owner`를 사용합니다.

```solidity
        if (!exemptionList[_owner])
            claimableAssetFees += _amount.revBp(fees.exit);
```

`owner`는 호출자(`msg.sender`)와 다를 수 있으며 코드는 호출자가 `owner`의 자금에 대해 승인을 받았는지 확인합니다.
따라서 공격자는 두 주소: `exemptionList[]`로 설정된 OWNER1과 `exemptionList[]`로 설정되지 않은 OPERATOR1로 이를 악용할 수 있습니다. 공격자가 OWNER1 토큰에 대한 승인을 OPERATOR1에게 부여하고 OPERATOR1 주소로 `withdraw(OWNER1)`을 호출하면 이중 지출이 발생합니다. 코드는 부과된 수수료 없이 자금을 완전히 반환하지만 `claimableAssetFees`에도 수수료를 추가합니다.

이것은 다른 시나리오에서도 악용될 수 있습니다. 일반적으로 이러한 불일치는 회계 오류를 유발합니다.

## 권장 사항

`_deposit()` 함수에서 `msg.sender`를 기반으로 수수료를 계산하십시오.
`_withdraw()` 함수에서 `msg.sender`를 기반으로 수수료를 계산하고 마지막으로 `preview` 함수를 수정하십시오.

# [H-07] 기초 자산 변경 후 회계 문제

## 심각도

**영향:** 높음, 여러 시나리오에서 회계가 잘못될 것이므로

**가능성:** 중간, 관리자가 `changeAsset()`을 호출할 때 발생하므로

## 설명

일반적으로 기초 자산을 업데이트하는 것은 풀에서 매우 위험한 움직임입니다.
캐시된 모든 가격이 잘못될 것입니다.

현재 코드에는 두 가지 캐시된 가격이 있습니다(제가 아는 한):
requestRedeem() 코드에서 요청에 대한 풀 가격을 캐시합니다. 코드는 나중에 인출 및 취소 요청에서 이를 사용합니다. (가격은 인출 가격에 영향을 미치고 취소 요청 시 토큰 소각에도 영향을 미칩니다)

수수료 계산 시 코드는 풀 가격을 캐시하고 나중에 수수료를 계산하는 데 사용합니다.

(풀 가격이 캐시되는 다른 곳이 있을 수 있습니다)

———
자산 금액이 캐시되는 또 다른 곳은 `claimableAssetFees`입니다. `updateAsset()`은 `_collectFees()`를 호출하여 `claimableAssetFees`를 처리하고 0으로 설정하지만 `_collectFees()`의 이 줄 때문에:
`If (profit == 0) return;`
`claimableAssetFees`(이전 자산의 금액을 표시함)는 자산 업데이트 후에도 0이 아닌 상태로 유지될 수 있습니다.

## 권장 사항

자산 변경 후 캐시된 가격을 재설정하십시오.

# [M-01] 볼트 일시 중지 시 상환 함수 활성

## 심각도

**영향:** 높음 - 볼트가 일시 중지된 중요한 상황에서 자산의 무단 인출을 허용합니다.

**가능성:** 낮음 - 이 문제는 볼트가 일시 중지된 상태일 때만 발생합니다.

## 설명

볼트의 예금, 발행 및 인출 기능은 일시 중지되면 중단됩니다. 이는 다음 함수에서 `whenNotPaused` 수정자를 통해 구현됩니다:

        function deposit(
            uint256 _amount,
            address _receiver
        ) public whenNotPaused returns (uint256 shares) {

        function safeDeposit(
            uint256 _amount,
            uint256 _minShareAmount,
            address _receiver
        ) public whenNotPaused returns (uint256 shares) {

        function withdraw(
            uint256 _amount,
            address _receiver,
            address _owner
        ) external whenNotPaused returns (uint256) {

        function safeWithdraw(
            uint256 _amount,
            uint256 _minAmount,
            address _receiver,
            address _owner
        ) public whenNotPaused returns (uint256 amount) {

그러나 `whenNotPaused` 수정자가 적용되지 않았기 때문에 상환 기능은 일시 중지할 수 없습니다. 이러한 부재로 인해 사용자는 볼트에 있어서는 안 될 때 자산을 인출할 수 있습니다.

        function redeem(
            uint256 _shares,
            address _receiver,
            address _owner
        ) external returns (uint256 assets) {

        function safeRedeem(
            uint256 _shares,
            uint256 _minAmountOut,
            address _receiver,
            address _owner
        ) external returns (uint256 assets) {

## 권장 사항

상환 및 safeRedeem 함수 모두에 whenNotPaused 수정자를 추가하십시오.

```diff
    function redeem(
        uint256 _shares,
        address _receiver,
        address _owner
-   ) external returns (uint256 assets) {
+   ) external whenNotPaused returns (uint256 assets) {
        return _withdraw(previewRedeem(_shares), _shares, _receiver, _owner);
    }

    function safeRedeem(
        uint256 _shares,
        uint256 _minAmountOut,
        address _receiver,
        address _owner
-    ) external returns (uint256 assets) {
+    ) external whenNotPaused returns (uint256 assets) {
        assets = _withdraw(
            previewRedeem(_shares),
            _shares, // _shares
            _receiver, // _receiver
            _owner // _owner
        );
        if (assets < _minAmountOut) revert AmountTooLow(assets);
    }
```

# [M-02] previewWithdraw()의 잘못된 반올림 방향

## 심각도

**영향:** 낮음, mint()에서 되돌아가고 EIP4626을 위반하므로

**가능성:** 높음, 각 tx에서 나눗셈이 발생하므로

## 설명

[EIP4626](https://eips.ethereum.org/EIPS/eip-4626)에 따르면 `previewWithdraw`는 나눗셈을 수행할 때 반올림(round up)해야 합니다.

> 마지막으로, EIP-4626 볼트 구현자는 다른 가변 및 뷰 메서드 전반에 걸쳐 특정하고 반대되는 반올림 방향이 필요하다는 것을 알고 있어야 합니다. 이는 사용자보다 계산 중에 볼트 자체를 선호하는 것이 가장 안전한 것으로 간주되기 때문입니다.
>
> 만약 (1) 사용자가 제공하는 기본 토큰의 특정 금액에 대해 사용자에게 발행할 지분의 수를 계산하거나 (2) 특정 양의 지분을 반환하기 위해 사용자에게 전송할 기본 토큰의 금액을 결정하는 경우 내림(round down)해야 합니다.
>
> 만약 (1) 주어진 양의 기본 토큰을 받기 위해 사용자가 제공해야 하는 지분의 양을 계산하거나 (2) 특정 양의 지분을 받기 위해 사용자가 제공해야 하는 기본 토큰의 양을 계산하는 경우 올림(round up)해야 합니다.

그러나 현재 구현에서 코드는 내림하고 계약 대신 호출자를 선호합니다.

`withdraw()` 함수는 `previewWithdraw()`를 사용하여 지분을 계산하고 `_withdraw()`를 호출합니다. `_withdraw()`에는 사용자가 받은 가격이 현재 가격보다 좋지 않은지 확인하는 가격 확인이 있으므로 반올림 오류가 발생하면 해당 확인이 실패하고 되돌아갑니다. (계산된 `_shares`는 더 작아지고 조건의 오른쪽은 더 작아짐)

```solidity
        // amount/shares cannot be higher than the share price (dictated by the inline convertToAssets below)
        if (_amount >= _shares.mulDiv(price * weiPerAsset, weiPerShare ** 2))
            revert AmountTooHigh(_amount);
```

## 권장 사항

지분을 계산할 때 올림하도록 `previewWithdraw`를 변경하십시오.

# [M-03] previewMint()의 잘못된 반올림 방향

## 심각도

**영향:** 낮음, EIP를 위반하고 mint()에서 되돌아가게 하므로

**가능성:** 높음, 모든 mint() 호출에서 나눗셈이 발생하므로

## 설명

EIP4626에 따르면 `previewMint()` 함수는 자산을 계산할 때 올림해야 합니다.

> 마지막으로, EIP-4626 볼트 구현자는 다른 가변 및 뷰 메서드 전반에 걸쳐 특정하고 반대되는 반올림 방향이 필요하다는 것을 알고 있어야 합니다. 이는 사용자보다 계산 중에 볼트 자체를 선호하는 것이 가장 안전한 것으로 간주되기 때문입니다.
>
> 만약 (1) 사용자가 제공하는 기본 토큰의 특정 금액에 대해 사용자에게 발행할 지분의 수를 계산하거나 (2) 특정 양의 지분을 반환하기 위해 사용자에게 전송할 기본 토큰의 금액을 결정하는 경우 내림(round down)해야 합니다.
>
> 만약 (1) 주어진 양의 기본 토큰을 받기 위해 사용자가 제공해야 하는 지분의 양을 계산하거나 (2) 특정 양의 지분을 받기 위해 사용자가 제공해야 하는 기본 토큰의 양을 계산하는 경우 올림(round up)해야 합니다.

현재 구현에서 코드는 내림합니다. 이는 계산이 계약 대신 호출자에게 유리하도록 할 것입니다.
`mint()` 함수에서 코드는 `previewMint()`를 사용하고 `_deposit()`을 호출합니다. `previewMint()`는 `_amount`에 대해 더 적은 금액을 계산하므로 사용자가 현재 가격보다 더 나은 가격을 받지 않도록 하는 `_deposit()` 내부 확인이 실패하고 호출이 되돌아갑니다: (`_amount`는 약간 더 낮아지고 조건의 오른쪽이 더 작아짐)

```solidity
        if (_shares > _amount.mulDiv(weiPerShare ** 2, last.sharePrice * weiPerAsset))
            revert AmountTooHigh(_amount);
```

## 권장 사항

지분을 계산할 때 올림하도록 `previewMint`를 변경하십시오.

# [M-04] 수수료 계산 시 deposit()에서 revBp()의 잘못된 사용

## 심각도

**영향:** 낮음, 잘못된 수수료 회계로 인해

**가능성:** 높음, deposit/mint 호출마다 발생하므로

## 설명

`_deposit()` 함수에서 코드는 다음과 같이 수수료를 계산합니다.

```solidity
        // slice the fee from the amount (gas optimized)
        if (!exemptionList[_receiver])
            claimableAssetFees += _amount.revBp(fees.entry);
```

그리고 `revBp()` 로직은 다음과 같습니다:

```solidity
    function revBp(
        uint256 amount,
        uint256 basisPoints
    ) internal pure returns (uint256) {
        return mulDiv(amount, basisPoints, BP_BASIS - basisPoints);
    }
```

예금의 `amount`는 분할되지 않으며 `deposit + fee`이므로 수수료 계산이 잘못되었습니다.

## 권장 사항

수수료 계산은 다음과 같아야 합니다:

```solidity
             claimableAssetFees  += _amount * fees.entry / (BP_BASIS  + fees.entry)
```

# [M-05] Chainlink에서 더 이상 사용되지 않는 함수 사용

## 심각도

**영향:** 높음 - 오래된 가격을 사용하면 총 자산 가치 및 지분 가격 계산이 부정확해집니다.

**가능성:** 낮음 - 검증 없이는 반환 가격이 잘못되었거나 오래된 것일 수 있습니다.

## 설명

USD에서 입력 토큰 금액으로 변환하기 위해 `StrategyV5Chainlink`는 `IChainlinkAggregatorV3.latestAnswer`를 사용합니다. 그러나 `latestAnswer` 함수는 Chainlink에서 더 이상 사용되지 않습니다. 이 더 이상 사용되지 않는 함수 사용은 `ChainlinkUtils`와 같은 다른 라이브러리에서도 관찰됩니다.

    function _usdToInput(uint256 _amount, uint8 _index) internal view returns (uint256) {
        return _amount.mulDiv(10**uint256(inputFeedDecimals[_index]) * inputDecimals[_index],
            uint256(inputPriceFeeds[_index].latestAnswer()) * 1e6); // eg. (1e6+1e8+1e6)-(1e8+1e6) = 1e6
    }

참고: https://docs.chain.link/data-feeds/api-reference#latestanswer

대신 `IChainlinkAggregatorV3.latestRoundData`를 사용해야 합니다.

## 권장 사항

Chainlink의 `latestRoundData`를 사용하도록 함수를 업데이트하십시오. 이 메서드는 타임스탬프를 포함하여 최신 가격 라운드에 대한 포괄적인 데이터를 제공하여 가격의 신선도와 관련성을 보장합니다.

예시 구현:

    uint256 private constant GRACE_PERIOD_TIME = 3600; // how long till we consider the price as stale

    function getChainlinkPrice (AggregatorV2V3Interface feed) internal {
        (uint80 roundId, int256 price, uint startedAt, uint updatedAt, uint80 answeredInRound) = feed.latestRoundData();
        require(price > 0, "invalid price");
        require(block.timestamp <= updatedAt + GRACE_PERIOD_TIME, "Stale price");
        return price;
    }

Arbitrum에 배포할 때 Arbitrum 시퀀서의 상태를 확인하는 검사를 포함하십시오. 가격 피드의 신뢰성에 영향을 줄 수 있기 때문입니다.

예시: https://docs.chain.link/data-feeds/l2-sequencer-feeds#example-code

# [M-06] 수수료 포함 전송(Fee on Transfer) 토큰이 회계를 망가뜨릴 것임

## 심각도

**영향:** 높음, 회계가 부정확해지고 sharePrice가 영향을 받으므로

**가능성:** 낮음, 수수료 포함 전송 토큰은 일반적으로 사용되지 않으므로

## 설명

`mint()/deposit()`은 전송 및 회계에 `amount`를 사용하고 있습니다. 그러나 수수료 포함 전송 토큰은 실제 받은 토큰이 금액보다 적기 때문에 회계를 망가뜨릴 수 있습니다. 결과적으로 `sharePrice`는 매번 약간의 오류가 발생합니다.

```solidity
File: src\abstract\As4626.sol
69:     function mint(
70:         uint256 _shares,
71:         address _receiver
72:     ) public returns (uint256 assets) {
73:         return _deposit(previewMint(_shares), _shares, _receiver);
74:     }

117:     function deposit(
118:         uint256 _amount,
119:         address _receiver
120:     ) public whenNotPaused returns (uint256 shares) {
121:         return _deposit(_amount, previewDeposit(_amount), _receiver);
122:     }

84:     function _deposit(
85:         uint256 _amount,
86:         uint256 _shares,
87:         address _receiver
88:     ) internal nonReentrant returns (uint256) {

98:         asset.safeTransferFrom(msg.sender, address(this), _amount);

105:         _mint(_receiver, _shares);
```

USDT는 잠재적으로 수수료 포함 전송 기능을 켤 수 있지만 아직은 아닙니다.

## 권장 사항

수신된 실제 금액을 정확하게 반영하기 위해 전후 잔액을 사용하고 그에 따라 지분 가격을 업데이트하십시오.

# [M-07] Pyth Network에서 오래된 가격 사용

## 심각도

**영향:** 높음 - 오래된 가격을 사용하면 총 자산 가치 및 지분 가격 계산이 부정확해집니다.

**가능성:** 낮음 - 업데이트가 없으면 가격이 자주 오래될 수 있습니다.

## 설명

`StrategyV5Pyth`는 `pyth.getPriceUnsafe`를 사용하여 Pyth 오라클 가격 피드를 얻어 자산/입력 환율을 계산합니다.

        function assetExchangeRate(uint8 inputId) public view returns (uint256) {
            if (inputPythIds[inputId] == assetPythId)
                return weiPerShare; // == weiPerUnit of asset == 1:1
            PythStructs.Price memory inputPrice = pyth.getPriceUnsafe(inputPythIds[inputId]);
            PythStructs.Price memory assetPrice = pyth.getPriceUnsafe(assetPythId);
            ...
        }

그러나 Pyth 문서에 따르면 `getPriceUnsafe`를 사용하면 가격이 업데이트되지 않은 경우 오래된 가격을 반환할 수 있습니다.

        /// @notice Returns the price of a price feed without any sanity checks.
        /// @dev This function returns the most recent price update in this contract without any recency checks.
        /// This function is unsafe as the returned price update may be arbitrarily far in the past.
        ///
        /// Users of this function should check the `publishTime` in the price to ensure that the returned price is
        /// sufficiently recent for their application. If you are considering using this function, it may be
        /// safer / easier to use either `getPrice` or `getPriceNoOlderThan`.
        /// @return price - please read the documentation of PythStructs.Price to understand how to use this safely.
        function getPriceUnsafe(
            bytes32 id
        ) external view returns (PythStructs.Price memory price);

`assetExchangeRate` 함수는 `Price.publishTime`을 확인하지 않아 잠재적으로 오래된 환율, 잘못된 투자 계산 및 왜곡된 총 자산 가치로 이어질 수 있습니다.

## 권장 사항

가격 업데이트를 위해 `pyth.updatePriceFeeds`를 사용하고 검색을 위해 `pyth.getPrice`를 사용하십시오.
예시: https://github.com/pyth-network/pyth-sdk-solidity/blob/main/README.md#example-usage

# [M-08] `ERC20::approve`는 USDT와 같은 일부 비표준 토큰에 대해 되돌아감

## 심각도

**영향**: 중간, 기능이 작동하지 않을 것이므로

**가능성**: 중간, USDT는 일반적인 토큰이므로

## 설명

코드는 `setSwapperAllowance`에서 ERC20 토큰에 대한 허용량을 설정하기 위해 `approve` 메서드를 사용합니다. 대상 ERC20이 `approve()` 함수에 대해 다른 함수 서명을 가진 비표준 토큰인 경우 되돌아갑니다. USDT와 같은 토큰은 이 함수에 대해 되돌아가므로 보상 토큰, 입력 토큰 및 기초 자산으로 사용할 수 없습니다.

```solidity
    function setSwapperAllowance(uint256 _amount) public onlyAdmin {
        address swapperAddress = address(swapper);

        for (uint256 i = 0; i < rewardLength; i++) {
            if (rewardTokens[i] == address(0)) break;
            IERC20Metadata(rewardTokens[i]).approve(swapperAddress, _amount);
        }
        for (uint256 i = 0; i < inputLength; i++) {
            if (address(inputs[i]) == address(0)) break;
            inputs[i].approve(swapperAddress, _amount);
        }
        asset.approve(swapperAddress, _amount);
    }
```

## 권장 사항

모든 ERC20 토큰을 지원하려면 대신 `SafeERC20`의 `forceApprove` 메서드를 사용하십시오.

# [L-01] setInputs에 대한 액세스 제어의 불일치

`StrategyV5Chainlink` 및 `StrategyV5Pyth` 모두에서 `setInputs` 함수는 `admin` 역할로 제한됩니다.

        function setInputs(address[] calldata _inputs, uint16[] calldata _weights, address[] calldata _priceFeeds) external onlyAdmin {
            ...
        }

        function setInputs(address[] calldata _inputs, uint16[] calldata _weights, bytes32[] calldata _pythIds) external onlyAdmin {
            ...
        }

반면 `StrategyV5Agent`(부모 계약)에서는 `setInputs`가 `manager` 역할에 의해 액세스 가능하도록 설계되었습니다.

        function setInputs(
            address[] calldata _inputs,
            uint16[] calldata _weights
        ) public onlyManager {
            ...
        }

계정이 admin 또는 manager 역할만 보유한 경우 모든 전략에서 `setInputs` 함수를 실행할 수 없습니다. 그리고 혼란을 초래할 수 있습니다.

이 문제를 해결하려면 `setInputs` 메서드의 액세스 제어에서 일관성을 유지하십시오.

# [L-02] StrategyV5Agent에서 rescueRequests 추가로 인한 스토리지 슬롯 충돌

전략의 스토리지 레이아웃은 `StrategyV5Abstract` 및 `As4626Abstract`에 의해 정의됩니다. 전략 계약은 스토리지 변수 정의를 위해 이 두 계약을 상속합니다.

StrategyV5Chainlink는 위의 2개 계약의 스토리지를 상속하고 4개의 다른 스토리지 변수를 추가합니다.

    IChainlinkAggregatorV3 internal assetPriceFeed; // Aggregator contract of the asset asset
    IChainlinkAggregatorV3[8] internal inputPriceFeeds; // Aggregator contract of the inputs
    uint8 internal assetFeedDecimals; // Decimals of the asset asset
    uint8[8] internal inputFeedDecimals; // Decimals of the input asset

`StrategyV5Chainlink`의 스토리지 레이아웃을 검사하면 슬롯 55에 `inputPriceFeeds` 상태 변수를 저장합니다.

그러나 구현 계약 `StrategyV5Agent`에서는 하나의 스토리지 슬롯 `mapping(address => RescueRequest) private rescueRequests`를 추가합니다. 또한 슬롯 `55`를 사용하고 `StrategyV5Chainlink`의 `inputPriceFeeds`와 충돌합니다.

이 경우 `rescueRequests`는 매핑이므로 매핑의 실제 데이터는 `slot = keccak256([key, mappingSlot])`에 저장되므로 데이터가 손상되지 않습니다. 그러나 나중에 `StrategyV5Agent`에 다른 데이터 유형이 추가되면 예측 불가능성을 유발할 수 있으므로 피해야 합니다. 계약 안정성과 예측 가능성을 보장하기 위해 명확하고 충돌 없는 스토리지 레이아웃을 유지하는 것이 중요합니다.
