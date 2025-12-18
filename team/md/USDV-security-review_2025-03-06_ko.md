# 정보

Pashov Audit Group은 업계 최고의 스마트 컨트랙트 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 경험을 바탕으로, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안은 보장할 수 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선의 노력을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

**0xtakii/usd-fun** 저장소에 대해 애플리케이션의 스마트 컨트랙트 구현 보안 측면에 중점을 둔 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었습니다.

# USDV 소개

USDV는 자산의 조합으로 뒷받침되며 안정성을 유지하기 위해 리베이스(rebase) 메커니즘을 활용하는 스테이블코인입니다. 프로토콜은 랩핑된 토큰(wrapped tokens), 가격 오라클 및 보상 분배 시스템을 특징으로 하며, 사용자가 관리되는 화이트리스트 인터페이스를 통해 토큰을 발행(mint) 및 소각(burn)할 수 있도록 합니다.

# 위험 분류

| 심각도 (Severity) | 영향: 높음 (High) | 영향: 중간 (Medium) | 영향: 낮음 (Low) |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음 (High)** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간 (Medium)** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음 (Low)** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 도난당하거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 인센티브가 주어지는 공격 벡터이지만, 여전히 비교적 가능성이 높습니다.

- 낮음 (Low) - 너무 많거나 비현실적인 가정을 필요로 하거나, 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 투자해야 합니다.

## 심각도 수준별 요구 조치

- 치명적 (Critical) - 가능한 한 빨리 수정해야 합니다 (이미 배포된 경우).

- 높음 (High) - 수정해야 합니다 (이미 배포되지 않은 경우 배포 전에).

- 중간 (Medium) - 수정하는 것이 좋습니다.

- 낮음 (Low) - 수정할 수 있습니다.

# 보안 평가 요약

_검토 커밋 해시_ - [0d171ad3f776e2e7ab2f7ceb81866057a2e92e69](https://github.com/0xtakii/usd-fun/tree/0d171ad3f776e2e7ab2f7ceb81866057a2e92e69)

_수정 검토 커밋 해시_ - [802ff9217d800675c48aa713918ebaad5d7728e0](https://github.com/0xtakii/usd-fun/tree/802ff9217d800675c48aa713918ebaad5d7728e0)

### 범위

다음 스마트 컨트랙트가 감사 범위에 포함되었습니다:

- `AddressesWhitelist`
- `ERC20RebasingPermitUpgradeable`
- `ERC20RebasingUpgradeable`
- `ExternalRequestsManager`
- `FlpPriceStorage`
- `RewardDistributor`
- `SimpleToken`
- `StUSF`
- `UsfPriceStorage`
- `WstUSF`
- `deploy`

# 발견 사항

# [L-01] 손실을 방지하기 위해 사용자의 소액 입금 및 랩핑 금지 (Prohibit users from making small deposits and wraps to avoid losses)

`deposit` 및 `wrap` 함수에서 `wstUSFAmount`가 0이 아닌지 확인하여, 사용자가 일부 `stUSF`를 입금했지만 0개의 점유율(shares)을 발행하여 손실을 입는 반올림 문제를 방지해야 합니다.

```diff
    function deposit(uint256 _usfAmount, address _receiver) public returns (uint256 wstUSFAmount) {
        wstUSFAmount = previewDeposit(_usfAmount);
+       _assertNonZero(wstUSFAmount);
        _deposit(msg.sender, _receiver, _usfAmount, wstUSFAmount);
        return wstUSFAmount;
    }

    function wrap(uint256 _stUSFAmount, address _receiver) public returns (uint256 wstUSFAmount) {
        _assertNonZero(_stUSFAmount);

        wstUSFAmount = convertToShares(_stUSFAmount);
        IERC20(stUSFAddress).safeTransferFrom(msg.sender, address(this), _stUSFAmount);
+       _assertNonZero(wstUSFAmount);
        _mint(_receiver, wstUSFAmount);
        emit Wrap(msg.sender, _receiver, _stUSFAmount, wstUSFAmount);

        return wstUSFAmount;
    }
```

# [L-02] 소유자가 `transferFrom`을 사용하여 토큰을 직접 전송할 수 없음 (The owner cannot directly transfer tokens using `transferFrom`)

`ERC20RebasingUpgradeable` 계약에서 소유자가 `transferFrom` 또는 `transferSharesFrom`을 사용하여 토큰을 전송할 때 여전히 허용량(allowance)을 소비해야 합니다. 즉, 소유자는 먼저 `approve` 함수를 호출하여 자신을 승인해야 합니다.
소유자가 허용량을 소비하지 않고 자신의 토큰을 전송할 수 있도록 하는 것이 좋습니다.

```diff
    function transferFrom(address _from, address _to, uint256 _underlyingTokenAmount) public returns (bool isSuccess) {
        address spender = _msgSender();
        uint256 shares = convertToShares(_underlyingTokenAmount);
        // To ensure accuracy in the `Transfer` event and allowance, utilize `convertToUnderlyingToken(shares)`
        // instead of using `_underlyingTokenAmount` as is.
        // This prevents discrepancies between the reported 'value' and the actual token amount transferred
        // due to floor rounding during the conversion to shares.
        uint256 underlyingTokenAmount = convertToUnderlyingToken(shares);
+       if (_from != msg.sender) {
+           _spendAllowance(_from, spender, underlyingTokenAmount);
+       }
-       _spendAllowance(_from, spender, underlyingTokenAmount);
        convertToUnderlyingToken(shares)
        _transfer(_from, _to, convertToUnderlyingToken(shares), shares);
        return true;
    }

    /**
     * @dev Moves a `_shares` amount from `_from` to `_to` using the
     * allowance mechanism. `_underlyingTokenAmount` is then deducted from the caller's
     * allowance.
     */
    function transferSharesFrom(address _from, address _to, uint256 _shares) public returns (bool isSuccess) {
        address spender = _msgSender();
        uint256 underlyingTokenAmount = convertToUnderlyingToken(_shares);
+       if (_from != msg.sender) {
+           _spendAllowance(_from, spender, underlyingTokenAmount);
+       }
-       _spendAllowance(_from, spender, underlyingTokenAmount);
        _transfer(_from, _to, underlyingTokenAmount, _shares);
        return true;
    }
```

# [L-03] `setReserves` 및 `setPrice`가 샌드위치 공격을 받을 수 있음 (`setReserves` and `setPrice` can be sandwiched)

`setReserves` 및 `setPrice`에 대한 호출은 MEV 기회를 제공합니다. 멤풀(mempool)을 모니터링하는 사용자가 선행 매매(frontrunning) 또는 후행 매매(backrunning)를 통해 함수 호출 전후에 트랜잭션을 수행하여 빠른 이익을 얻을 수 있기 때문입니다. 이 문제는 `setReserves`에서 더욱 악화되는데, 가격이 이동할 수 있는 엄격하게 정의된 상한선이 없기 때문입니다. 예를 들어, 가격은 1e6 정도의 낮은 수준에서 `PRICE_SCALING_FACTOR`인 1e18까지 이동할 수 있습니다.

UsfPriceStorage.sol에서:

```solidity
    function setReserves(bytes32 _key, uint256 usfSupply, uint256 reserves) external onlyRole(SERVICE_ROLE) {
        if (_key == bytes32(0)) revert InvalidKey();
        if (usfSupply == 0) revert InvalidUsfSupply();
        if (reserves == 0) revert InvalidReserves();
        if (prices[_key].timestamp != 0) revert PriceAlreadySet(_key);

        uint256 computedPrice = _calculatePrice(usfSupply, reserves);
        uint256 lastPriceValue = lastPrice.price;
        if (lastPriceValue != 0) {
            // assumes only possible at initialization
            uint256 lowerBound = lastPriceValue - (lastPriceValue * lowerBoundPercentage / BOUND_PERCENTAGE_DENOMINATOR);
            if (computedPrice < lowerBound) {
                revert InvalidPrice(computedPrice, lowerBound);
            }
        }

        uint256 currentTime = block.timestamp;
@>      Price memory priceData =
            Price({price: computedPrice, usfSupply: usfSupply, reserves: reserves, timestamp: currentTime});

        prices[_key] = priceData;
        lastPrice = priceData;

        emit PriceSet(_key, computedPrice, usfSupply, reserves, currentTime);
    }
```

FlpPriceStorage.sol에서:

```solidity
    function setPrice(bytes32 _key, uint256 _price) external onlyRole(SERVICE_ROLE) {
        if (_key == bytes32(0)) revert InvalidKey();
        if (_price == 0) revert InvalidPrice();
        if (prices[_key].timestamp != 0) revert PriceAlreadySet(_key);

        uint256 lastPriceValue = lastPrice.price;
        if (lastPriceValue != 0) {
            uint256 upperBound = lastPriceValue + (lastPriceValue * upperBoundPercentage / BOUND_PERCENTAGE_DENOMINATOR);
            uint256 lowerBound = lastPriceValue - (lastPriceValue * lowerBoundPercentage / BOUND_PERCENTAGE_DENOMINATOR);
            if (_price > upperBound || _price < lowerBound) {
                revert InvalidPriceRange(_price, lowerBound, upperBound);
            }
        }

        uint256 currentTime = block.timestamp;
@>      Price memory price = Price({price: _price, timestamp: currentTime});
        prices[_key] = price;
        lastPrice = price;

        emit PriceSet(_key, _price, currentTime);
    }
```

권장 사항은 프라이빗 RPC를 사용하여 선행 매매를 제거하고, `setPrice`에서 수행되는 것처럼 `setReserves`에도 상한 비율을 도입하는 것입니다. 경계값이 `PRICE_SCALING_FACTOR`를 초과하면 이를 최댓값으로 설정할 수 있습니다.

# [L-04] 토큰 permit 서명은 기한 전에 취소할 수 없음 (Token permit signatures cannot be cancelled before deadline)

WstUSF 및 SImpleToken 계약은 가스 없는 승인을 위해 `ERC20Permit` 기능을 구현합니다. stUSF는 ERC20RebasingPermitUpgradeable.sol을 통해서도 이를 수행합니다. 그러나 구현에는 사용자가 permit 서명이 만료되기 전에 취소할 수 있는 메커니즘이 부족합니다. 사용자가 permit에 서명하고 나면, 다른 보안상의 이유로 승인을 취소하고 싶더라도 기한이 지날 때까지 기다려야 무효화됩니다.

stUSF.sol에서:

```solidity
    function initialize(string memory _name, string memory _symbol, address _usfAddress) public initializer {
        _assertNonZero(_usfAddress);

        __ERC20Rebasing_init(_name, _symbol, _usfAddress);
@>      __ERC20RebasingPermit_init(_name);
    }
```

ERC20RebasingPermitUpgradeable.sol에서:

```solidity
        bytes32 structHash =
@>          keccak256(abi.encode(PERMIT_TYPEHASH, _owner, _spender, _value, _useNonce(_owner), _deadline));

        bytes32 hash = _hashTypedDataV4(structHash);
```

SImpleToken.sol에서:

```solidity
    function initialize(string memory _name, string memory _symbol) public initializer {
        __ERC20_init(_name, _symbol);
@>      __ERC20Permit_init(_name);

        __AccessControlDefaultAdminRules_init(1 days, msg.sender);
    }
```

WstUSF.sol에서:

```solidity
    function initialize(string memory _name, string memory _symbol, address _stUSFAddress) public initializer {
        __ERC20_init(_name, _symbol);
@>      __ERC20Permit_init(_name);

        _assertNonZero(_stUSFAddress);
        stUSFAddress = _stUSFAddress;

        usfAddress = address(IERC20Rebasing(_stUSFAddress).underlyingToken());
        _assertNonZero(usfAddress);
        IERC20(usfAddress).safeIncreaseAllowance(stUSFAddress, type(uint256).max);
    }
```

이것은 계약이 OpenZeppelin의 `ERC20PermitUpgradeable.sol`을 기반으로 하는데, 여기에는 nonce를 증가시키는 기능이 존재하지 않고 `Nonces` 내의 [\_useNonce](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/8cb7caa05dee53c21033cf852c9cd714ff839661/contracts/utils/NoncesUpgradeable.sol#L47-L55) 함수가 internal로 표시되어 있기 때문입니다. 즉, 현재 nonce 시스템은 permit이 실행될 때만 증가하므로, 사용자가 자신의 nonce를 진행시켜 보류 중인 서명을 능동적으로 무효화할 수 없습니다.

서명자가 nonce를 소비하여 서명을 취소하는 데 직접 사용할 수 있는 public 함수를 도입하는 것을 고려하십시오.

```solidity
    function useNonce() external returns (uint256) {
        return _useNonce(msg.sender);
    }
```

# [L-05] 불공정한 손실 사회화 또는 제재 우회 (Unfair loss socialization or sanctions bypass)

`ExternalRequestsManager.sol#requestBurn()`을 살펴보십시오:

```solidity
function requestBurn(uint256 _issueTokenAmount, address _withdrawalTokenAddress, uint256 _minWithdrawalAmount)
    public
    onlyAllowedProviders
|>   allowedToken(_withdrawalTokenAddress)
    whenNotPaused
{
    // Implementation
}
```

프로토콜은 사용자가 지원되는 다양한 자산(현재 USDC 및 USDT, _향후 더 추가될 가능성 있음_)을 입금하여 토큰을 발행(mint)할 수 있도록 허용한다는 점에 유의해야 합니다. 그러나 예치금을 되찾기 위해 토큰을 소각(burn)할 때, 사용자는 원래 예치한 것과 관계없이 지원되는 모든 토큰을 인출 토큰으로 지정할 수 있습니다.

이는 사용자가 예치한 것과 인출하는 것 사이의 적절한 관계를 유지하지 못하기 때문에 문제가 됩니다. 예를 들어:

1. 사용자 A가 1000 USDC를 입금하여 프로토콜 토큰을 발행합니다.
2. 사용자 B가 1000 USDT를 입금하여 프로토콜 토큰을 발행합니다.
3. 두 예치금 모두 재무부로 이체됩니다.
4. USDT에 블랙 스완 이벤트(디페깅, 해킹 등)가 발생합니다.
5. 사용자 A는 원래의 USDC 예치금 대신 USDT를 인출하도록 선택할 수 있습니다. (역주: 원문은 A가 USDT를 인출한다고 되어있으나 문맥상 A가 일찍 빠져나가기 위해 가치가 있는 자산을 인출해야 하므로 USDC를 인출하거나, B가 USDT 대신 USDC를 인출하는 상황이어야 맞을 것으로 보이나, 원문 그대로 번역하고 아래 설명을 따름. 아래 설명은 "First to exit" 시나리오이므로, 손상된 자산을 입금한 사용자가 건전한 자산을 인출해가는 상황을 의미함.)

이 설계는 시장 스트레스 이벤트 동안 "선착순 탈출(first to exit)" 시나리오를 만들어 초기 사용자가 나중 사용자에게 손실을 전가할 수 있도록 합니다. 지원되는 자산에 블랙 스완 이벤트가 발생하면:

1. 손상된 자산을 예치한 사용자는 다른 건전한 자산 풀에서 인출할 수 있습니다.
2. 이로 인해 재무부의 건전한 자산이 고갈됩니다.
3. 나중 사용자는 손상된 자산을 받아들이거나 전혀 인출할 수 없게 됩니다.

프로토콜에 더 많은 예치 자산이 추가될수록 문제는 악화됩니다. 각 사용자가 자신이 선택한 예치 자산의 위험을 부담하는 대신, 위험은 프로토콜의 모든 사용자에게 사회화됩니다(의도적으로 다른 사용자에게 손실을 위임하는 악의적인 사용자는 제외).

제목에서도 암시했듯이, 이 버그 케이스는 OFAC 제재 계정이 제재를 우회하고 여전히 자금에 접근할 수 있도록 허용합니다. 처음에 USDT로 입금하고 발행했는데 현재 USDT 블랙리스트에 오른 사용자가 재무부에서 자산을 인출할 때 인출 토큰으로 `USDC`를 지정하기만 하면 제재를 피할 수 있기 때문입니다.

**권장 사항**

각 발행 요청에 대해 원래 예치 토큰을 이미 추적하고 있으므로, 사용자가 처음에 예치한 것과 동일한 유형의 토큰만 인출할 수 있도록 강제해야 합니다.

# [L-06] 민팅 후 allowedToken을 제거하면 자금이 묶이고 DOS가 발생함 (Removing allowedToken after minting leads to stuck funds and DOS)

`ExternalRequestsManager` 계약은 allowedTokens 목록에서 토큰을 추가하고 제거하는 것을 허용합니다:

```solidity
function removeAllowedToken(address _allowedTokenAddress) external onlyRole(DEFAULT_ADMIN_ROLE) {
    _assertNonZero(_allowedTokenAddress);
    allowedTokens[_allowedTokenAddress] = false;
    emit AllowedTokenRemoved(_allowedTokenAddress);
}
```

사용자가 토큰 발행을 요청할 때 예치 토큰을 제공하며 이는 재무부로 이체됩니다:

```solidity
function requestMint(address _depositTokenAddress, uint256 _amount, uint256 _minMintAmount) {
    // ...
    IERC20(_depositTokenAddress).safeTransferFrom(msg.sender, address(this), _amount);
    // ...
}

function completeMint(bytes32 _idempotencyKey, uint256 _id, uint256 _mintAmount) {
    // ...
    depositToken.safeTransfer(treasuryAddress, request.amount);
    // ...
}
```

나중에 사용자가 토큰을 소각할 때, 현재 허용된 인출 토큰을 지정해야 합니다:

```solidity
function requestBurn(uint256 _issueTokenAmount, address _withdrawalTokenAddress, uint256 _minWithdrawalAmount)
    public
    onlyAllowedProviders
|>    allowedToken(_withdrawalTokenAddress)
    whenNotPaused
{
    // ...
}
```

그러나 이 흐름은 사용자가 해당 토큰을 이미 예치하고 발행한 후 관리자가 `allowedTokens` 목록에서 토큰을 제거할 때 아래 경로를 고려하지 않습니다. 예를 들어:

1. USDC와 USDT가 모두 allowedTokens입니다.
2. 사용자가 $100,000 상당의 USDC와 USDT를 예치하고, 이는 재무부로 이체됩니다.
3. 관리자가 allowedTokens에서 USDC를 제거합니다.
4. USDC를 예치한 사용자는 `allowedToken(_withdrawalTokenAddress)` 수정자(modifier)로 인해 인출 토큰으로 USDC를 지정할 수 없습니다.
5. 사용자는 USDT를 지정하거나(USDT 준비금 고갈) 자금을 인출할 수 없게 됩니다.

즉, 여전히 허용된 토큰을 예치한 사용자라도 원래 예치 토큰을 지정할 수 없는 다른 사용자에 의해 해당 토큰이 고갈되어, 늦은 사용자가 자금을 인출할 수 없는 뱅크런 시나리오를 만들 수 있습니다.

토큰을 허용하지 않게 되면(unallowing) 모든 사용자에게 영향을 미치는 시스템적 유동성 문제가 발생하므로 다중 토큰 시스템에서 영향이 증폭됩니다. 실제로 프로토콜이 USDC와 USDT를 지원하고 USDC가 허용되지 않게 되면, 보고서에 공유된 가상의 경우를 사용하면 약 50%의 사용자가 원래 예치금을 회수할 수 없게 됩니다.

`requestBurn` 함수에서 `allowedToken` 수정자를 제거하여 현재 allowedTokens 상태와 관계없이 사용자가 원래 예치한 것과 동일한 토큰 유형을 사용하여 인출할 수 있도록 하는 것을 고려하십시오.

# [L-07] 일부 제공자에 대해 취소가 차단될 수 있음 (Cancellations can be blocked for some providers)

```solidity
    function cancelMint(uint256 _id) external mintRequestExist(_id) {
// ..snip
        request.state = State.CANCELLED;
        IERC20 depositedToken = IERC20(request.token);
|>        depositedToken.safeTransfer(request.provider, request.amount);
        emit MintRequestCancelled(_id);
    }
        function cancelBurn(uint256 _id) external burnRequestExist(_id) {
// ..snip
        request.state = State.CANCELLED;
        IERC20 issueToken = IERC20(ISSUE_TOKEN_ADDRESS);
|>        issueToken.safeTransfer(request.provider, request.amount);

        emit BurnRequestCancelled(_id);
    }
```

보시다시피 취소 함수 `ExternalRequestsManager::cancelMint()` 및 `cancelBurn()`은 `safeTransfer`를 사용하여 자금을 `msg.sender`로 직접 전송합니다.

현재 usd-fun은 USDC/USDT(둘 다 블랙리스트 가능한 스테이블코인임)만 지원하며, 블랙리스트 주소로의 전송은 되돌려진다는 것을 알고 있습니다. 이는 다음과 같은 복구 불가능한 상태를 만듭니다:

1. 초기에 발행/소각 요청을 했던 블랙리스트 제공자가 `cancelMint()/cancelBurn()`을 호출하려고 시도합니다.
2. 토큰 계약의 블랙리스트 확인으로 인해 `safeTransfer`가 실패합니다.
3. 트랜잭션이 되돌려져 요청이 `CREATED` 상태로 유지됩니다.
4. 프로토콜은 사용자를 위한 취소를 처리할 수 없습니다.

취소 함수에 새로운 매개변수를 추가하여 제공자가 수령인을 전달할 수 있도록 허용하십시오:

```solidity
function cancelMint(uint256 _id, address _recipient) external {
    // ...
    depositedToken.safeTransfer(_recipient, request.amount);
}

function cancelBurn(uint256 _id, address _recipient) external {
    // ...
    issueToken.safeTransfer(_recipient, request.amount);
}
```

# [L-08] 현재 재무부 구현으로 인한 보안 결함 발생 (Current treasury implementation causes security deficiencies)

재무부는 단순한 주소로 구현되어 있으며, 계약일 수도 있고 아닐 수도 있습니다(하지만 이번 감사에서는 계약이 아닌 주소로 가정합니다).

```solidity
// ExternalRequestsManager.sol
address public treasuryAddress;

constructor(
    address _issueTokenAddress,
    address _treasuryAddress,
    address _providersWhitelistAddress,
    address[] memory _allowedTokens
) {
    treasuryAddress = _assertNonZero(_treasuryAddress);
    // ...
}
```

스크립트에서 이것은 "자금 관리 주소"로만 전달되었습니다.

`deploy.s.sol#L79`에서:

```solidity
    address public treasury; // fund management address
```

토큰이 이 주소로 직접 송수신된다는 점에 유의해야 합니다:

```solidity
// When completing mint
depositToken.safeTransfer(treasuryAddress, request.amount);

// When completing burn
IERC20(request.token).safeTransferFrom(treasuryAddress, request.provider, _withdrawalAmount);
```

대조적으로, usd-fun에 영감을 준 resolv-contracts는 다음과 같은 견고한 `Treasury` 계약을 구현합니다:

1. PausableUpgradeable을 통한 비상 일시 중지 기능
2. 세분화된 수령인/지출자 화이트리스트
3. 다양한 트랜잭션 유형에 대한 운영 제한
4. 중복 작업을 방지하기 위한 멱등성(Idempotency) 추적
5. 안전 메커니즘을 갖춘 프로토콜별 통합

현재 구현의 경우 이에 해당하지 않으므로 아래의 모든 경우가 열려 있음을 의미합니다:

- 외부 계약을 일시 중지하는 것 외에는 침해 발생 시 토큰 이동을 일시 중지할 수 있는 기능이 없지만, 재무부의 모든 자산이 노출될 수 있습니다.
- 상당한 자산 유출을 방지하기 위한 트랜잭션 규모 제한이 없습니다.
- 지정된 재무부 주소의 소유자는 모든 자금을 이체할 수 있습니다.
- 가장 중요한 것은 기술에 정통한 사용자는 발행 시 자금이 이체되는 검증 가능한 계약이 없고, 소각을 결정할 때 유효한 인스턴스에서만 토큰이 제거될 수 있다는 확신을 가질 수 없으므로 프로토콜을 면밀히 조사할 것입니다.

resolv-contracts 방식과 유사한 전용 Treasury 계약을 구현하십시오.

# [L-09] 타임락 부재로 인한 보상 스나이핑 가능 (Lack of timelock enables reward sniping)

먼저 다음을 참조하십시오:

```solidity
function deposit(uint256 _usfAmount, address _receiver) public returns (uint256 wstUSFAmount) {
    wstUSFAmount = previewDeposit(_usfAmount);
    _deposit(msg.sender, _receiver, _usfAmount, wstUSFAmount);
    return wstUSFAmount;
}
```

```solidity
function withdraw(uint256 _usfAmount, address _receiver, address _owner) public returns (uint256 wstUSFAmount) {
    uint256 maxusfAmount = maxWithdraw(_owner);
    if (_usfAmount > maxusfAmount) revert ExceededMaxWithdraw(_owner, _usfAmount, maxusfAmount);
    wstUSFAmount = previewWithdraw(_usfAmount);
    _withdraw(msg.sender, _receiver, _owner, _usfAmount, wstUSFAmount);
    return wstUSFAmount;
}
```

보시다시피 WstUSF 계약은 사용자가 타임락 메커니즘 없이 동일한 블록 내에서 입출금을 할 수 있도록 합니다. 이러한 원자적 상호 작용 기능은 정교한 사용자가 `RewardDistributor`의 보상 분배를 선행 매매(frontrun)할 수 있는 공격 벡터를 생성합니다.

보상이 기본 StUSF 계약(WstUSF가 랩핑함)에 분배되려고 할 때 악의적인 행위자가 다음을 수행할 수 있기 때문입니다:

1. 멤풀에서 들어오는 보상 분배 트랜잭션을 모니터링합니다.
2. WstUSF에 대규모 입금을 하여 이러한 트랜잭션을 선행 매매합니다.
3. 분배된 보상의 비례적으로 큰 몫을 받습니다.
4. 동일한 블록에서 즉시 포지션을 인출합니다.
5. 장기 포지션을 유지하지 않고 가치를 추출합니다.

이 공격은 특정 시점의 스냅샷을 기반으로 점유율 분배를 계산하는 ERC4626 스타일의 볼트 아키텍처 때문에 특히 효과적입니다. 이 경로를 통해 유출될 수 있는 가치가 그리 크지 않아 영향은 중간 정도이지만, 이는 여전히 진정한 장기 스테이커에 대한 보상을 줄이고 MEV 가능 행위자에게 유리한 불공정한 분배 메커니즘을 만듭니다.

**권장 사항**

입금과 인출 작업 사이에 최소 보유 기간을 강제하는 타임락 메커니즘을 구현하는 것을 고려하십시오.

# [L-10] 공유 화이트리스트 사용으로 인한 토큰별 주소 화이트리스트 지정 불가 (Shared whitelist usage prevents token-specific address whitelisting)

먼저 `deploy.s.sol`을 살펴보십시오:

```solidity
// deploying the whitelist contract
whitelist = IAddressesWhitelistExtended(address(new AddressesWhitelist()));

address[] memory whitelistedTokens = new address[](2);
whitelistedTokens[0] = usdcAddress;
whitelistedTokens[1] = usdtAddress;

// deploying the ExternalRequestsManager contract for FunLP tokens
externalRequestsManager = IExternalRequestsManagerExtended(
    address(new ExternalRequestsManager(address(funLpToken), treasury, address(whitelist), whitelistedTokens))
);

funLpToken.grantRole(SERVICE_ROLE, address(externalRequestsManager));

externalRequestsManager.grantRole(SERVICE_ROLE, service);

usfExternalRequestsManager = IExternalRequestsManagerExtended(
    address(new ExternalRequestsManager(address(funToken), treasury, address(whitelist), whitelistedTokens))
);

funToken.grantRole(SERVICE_ROLE, address(usfExternalRequestsManager)); // requires for mint and burn functions


// ..snip

whitelist.transferOwnership(admin);

```

스크립트에서 볼 수 있듯이 두 `ExternalRequestsManager` 인스턴스에 대해 동일한 `whitelist` 계약 인스턴스를 사용하며, 그런 다음 `funLpToken` 및 `funToken` 토큰에 대해 전달됩니다. 그러면 하나의 토큰에 대해 화이트리스트에 있는 모든 제공자가 자동으로 다른 토큰에 대해서도 화이트리스트에 포함되는 공유 화이트리스트 상황이 생성됩니다.

이제 두 ExternalRequestsManager 인스턴스 모두에 공유 화이트리스트 계약을 사용하는 것은 토큰별 제공자 접근 제어를 구현할 가능성을 제거합니다.

스크립트에 따르면 두 개의 분리된 `ExternalRequestsManager`를 보유하려는 의도가 있으므로 이는 적절한 경로가 아닌 것으로 해석될 수 있으며, 이는 `funLpToken` 및 `funToken` 토큰의 화이트리스트 지정을 위한 두 개의 다른 인스턴스로 해석되어야 합니다.

그러나 현재 설계 결정은 두 토큰에 대해 다른 위험 프로필을 구현할 수 없음을 의미합니다. 즉, 하나의 토큰과 상호 작용하도록 신뢰할 수 있는 제공자가 다른 토큰과 상호 작용하는 것을 제한할 수 없습니다.

여기서 규제 고려 사항을 암시할 수도 있습니다. 서로 다른 토큰은 수용할 수 없는 제공자 화이트리스트 지정에 대한 규제 요구 사항이 다를 수 있기 때문입니다. 예를 들어 OFAC 목록에 있는 사람들을 준수하고 차단해야 하는 반면 다른 하나는 그렇지 않은 경우인데, 이는 불가능합니다.

즉, 우리의 경우 `requestMint()`를 보면 일부 제공자는 복잡성으로 인해 유동성 토큰과 상호 작용할 수 있는 규제 승인을 받았지만 스테이블코인과는 상호 작용할 수 없다는 주장이 있을 수 있습니다.

**권장 사항**

두 개의 별도 `ExternalRequestsManager`가 있으므로 토큰별 접근 제어를 활성화하려면 두 개의 별도 `AddressesWhitelist`도 있어야 합니다:

```solidity
// Deploy separate whitelist contracts
IAddressesWhitelistExtended funLpWhitelist = IAddressesWhitelistExtended(address(new AddressesWhitelist()));
IAddressesWhitelistExtended usfWhitelist = IAddressesWhitelistExtended(address(new AddressesWhitelist()));

// Use token-specific whitelists for each manager
externalRequestsManager = IExternalRequestsManagerExtended(
    address(new ExternalRequestsManager(address(funLpToken), treasury, address(funLpWhitelist), whitelistedTokens))
);

usfExternalRequestsManager = IExternalRequestsManagerExtended(
    address(new ExternalRequestsManager(address(funToken), treasury, address(usfWhitelist), whitelistedTokens))
);
```

# [L-11] `mintWithPermit`의 잘못된 매개변수 순서 (Incorrect parameter order in `mintWithPermit`)

`WstUSF::mintWithPermit`의 현재 구현은 permit 검증에 계산된 `usfAmount`를 사용합니다:

```solidity
function mintWithPermit(
135:    uint256 _wstUSFAmount,
        address _receiver,
        uint256 _deadline,
        uint8 _v,
        bytes32 _r,
        bytes32 _s
) external returns (uint256 usfAmount) {
    IERC20Permit usfPermit = IERC20Permit(usfAddress);
    usfAmount = previewMint(_wstUSFAmount);
146:      try usfPermit.permit(msg.sender, address(this), usfAmount, _deadline, _v, _r, _s) {} catch {}
    _deposit(msg.sender, _receiver, usfAmount, _wstUSFAmount);
    return usfAmount;
}
```

검증 후 우리가 이체하는 것은 usfAmount 자체이므로(`WstUSF.sol#L250`, `WstUSF.sol#L146`), 랩핑된 토큰 금액(`WstUSF.sol#L135`) 대신 사용자가 지출하고자 하는 usf의 정확한 양을 지정하도록 함수 서명을 변경하는 것이 좋습니다.

```solidity

    function _deposit(address _caller, address _receiver, uint256 _usfAmount, uint256 _wstUSFAmount) internal {
        IStUSF stUSF = IStUSF(stUSFAddress);
        IERC20 usf = IERC20(usfAddress);

250:        usf.safeTransferFrom(_caller, address(this), _usfAmount);
        stUSF.deposit(_usfAmount);
        _mint(_receiver, _wstUSFAmount);

        emit Deposit(_caller, _receiver, _usfAmount, _wstUSFAmount);
    }
```

그러나 현재 방식은 사용자가 승인해야 하는 `usf`의 양을 확인하기 위해 오프체인에서 먼저 `previewMint`를 호출한 다음 이를 서명을 통해 허용하는 데 의존합니다. 하지만 어떤 이유로든 온체인에서 `mintWithPermit`이 호출되기 전에 환율이 변경되면, 약간 더 많은 `usf`가 필요한 경우 `safeTransferFrom` 중에 트랜잭션이 되돌려질 수 있습니다.

이 접근 방식은 일반적으로 permit의 아이디어와도 배치됩니다. 당연히 EIP-2612 permit 시스템은 사용자가 승인을 위해 온체인에 올 필요조차 없도록 하기 위해 사용되며, 이는 사용자의 온체인 존재를 가능한 한 줄이려고 한다는 것을 의미합니다. 따라서 먼저 미리보기를 하게 한 다음 서명 생성과 온체인 실행 사이에 환율이 변경되면 실패할 수 있는 것에 서명하게 하는 것은 이 접근 방식에 직관적이지 않습니다.

**권장 사항**

대신 `usfAmount`를 지정하고 permit 검증 후 변환되도록 해야 하며, 랩핑된 토큰은 수령인에게 발행되어야 합니다:

```solidity
function mintWithPermit(
    uint256 _usfAmount,  // Changed from _wstUSFAmount
    address _receiver,
    uint256 _deadline,
    uint8 _v,
    bytes32 _r,
    bytes32 _s
) external returns (uint256 wstUSFAmount) {
    IERC20Permit usfPermit = IERC20Permit(usfAddress);

    // Use the exact amount the user specified for the permit
    try usfPermit.permit(msg.sender, address(this), _usfAmount, _deadline, _v, _r, _s) {} catch {}

    // Calculate how many wrapped tokens this will yield
    wstUSFAmount = convertToShares(_usfAmount);

    _deposit(msg.sender, _receiver, _usfAmount, wstUSFAmount);
    return wstUSFAmount;
}
```

# [L-12] 서명 검증 누락으로 인한 스마트 계정의 permit 기능 제한 (Missing signature verification limits permit functionality for smart accounts)

`ERC20RebasingPermitUpgradeable.sol#permit()`을 살펴보십시오:

```solidity
function permit(address _owner, address _spender,
    uint256 _value, uint256 _deadline,
    uint8 _v, bytes32 _r, bytes32 _s
) public virtual {
// ..snip
    address signer = ECDSA.recover(hash, _v, _r, _s);
    if (signer != _owner) {
        revert ERC2612InvalidSigner(signer, _owner);
    }

    _approve(_owner, _spender, _value);
}
```

보시다시피, permit 구현은 서명 검증을 위해 ECDSA.recover에만 의존하므로 스마트 컨트랙트 지갑과 계정이 permit 기능을 사용할 수 없습니다. 많은 최신 지갑 구현(Gnosis Safe, Argent 등)은 이 구현에서 예상하는 형식(즉, `v, r, s`)으로 ECDSA 서명을 생성할 수 없어, 상당수의 사용자가 가스 효율적인 승인에서 배제됩니다. 프로토콜이 스테이블코인이라는 사실을 고려할 때 이러한 사용자는 통합될 것으로 예상됩니다.

스마트 컨트랙트 지갑이 permit 기능을 사용할 수 있도록 EIP-1271 서명 검증 지원을 구현하십시오.

추가로 아직 배포되지 않은 반사실적(counterfactual) 계약 지갑을 위한 EIP-6492 지원 구현을 고려할 수 있습니다.

