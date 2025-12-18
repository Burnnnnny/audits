
# Pashov Audit Group 소개

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 입증된 인재들입니다. 대부분은 공개 대회 보상으로 10만 달러 이상을 획득했거나, 다회 우승자이거나, 저희와의 감사에서 진정으로 탁월한 성과를 거두었습니다. 우리는 검증되고 동기 부여된 인재들과만 일합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견하여 패치를 도우면서, 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 귀하의 프로젝트를 위한 우리 팀의 최선의 노력을 보장합니다.

[여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 컨트랙트 보안 검토는 취약점의 완전한 부재를 검증할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 우리는 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 컨트랙트의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 서론

<p><strong>Sofamon/wishwish-contracts</strong> 저장소에 대한 시간 제한 보안 검토가 Pashov Audit Group에 의해 수행되었으며, <strong>0x37, JCN, 0xbepresent, Hunter</strong>가 <strong>WishWish</strong> 검토에 참여했습니다. 총 <strong>12</strong>개의 이슈가 발견되었습니다.</p>

# WishWish 소개

<p>WishWish는 창작자가 상자(boxes)와 수집품(collectables)의 이중 계약 시스템을 통해 NFT 컬렉션을 출시할 수 있도록 하는 NFT 플랫폼으로, 사용자가 상자를 발행하고 수집품으로 공개(reveal)하며 창작자를 위한 로열티를 생성할 수 있도록 합니다. 이는 지불, 수수료 분배 및 상환 가능한 창작자 보상을 위해 WISH ERC20 토큰을 통합하며, 계약 배포 및 발행 권한을 처리하는 팩토리 및 관리자 아키텍처를 갖추고 있습니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [ce7b00f10fb26e207a785331ffaa1d13f44db766](https://github.com/Sofamon/wishwish-contracts/tree/ce7b00f10fb26e207a785331ffaa1d13f44db766)<br>&nbsp;&nbsp;(Sofamon/wishwish-contracts)

**수정 검토 커밋 해시:**<br>• [58b0c86fc7ea13795b05ed9c08ab2e558af61a52](https://github.com/Sofamon/wishwish-contracts/tree/58b0c86fc7ea13795b05ed9c08ab2e558af61a52)<br>&nbsp;&nbsp;(Sofamon/wishwish-contracts)

# 범위

- `RoyaltyManager.sol`
- `SwapImpl.sol`
- `SwapProxy.solWishWishBox.sol`
- `WishWishCollectable.sol`
- `WishWishFactory.sol`
- `WishWishManager.sol`
- `WishWishManagerProxy.sol`
- `WishWishToken.sol`
- `WishWishTokenProxy.sol`
- `IWETH9.sol`

# 발견 사항

# [C-01] 누구나 `swapProxy`를 재초기화할 수 있음 (Anyone can re-initialize the `swapProxy`)

_해결됨_

## 심각도

**영향:** 높음 (High)

**가능성:** 높음 (High)

## 설명
`SwapImpl::initialize` 함수는 접근 제어가 부족하여 두 번 이상 성공적으로 호출될 수 있어 프록시의 임의 재초기화를 허용합니다. 이를 통해 공격자는 `router` 및 `permit2` 주소와 같은 중요한 상태 변수를 사용자 지정 악성 계약으로 업데이트할 수 있습니다. 이렇게 하면 공격자가 `RoyaltyManager`로 전송되는 모든 `ETH` 및 `WETH`를 탈취할 수 있습니다.

`SwapImpl` 및 `SwapProxy`가 배포된 후 공격자는 `SwapProxy::initialize`를 다시 호출하고 사용자 지정 계약을 `router` 및 `permit2`로 지정할 수 있습니다.

```solidity
    function initialize(
        address _universalRouter,
        address _permit2,
        address _weth,
        address _usdc,
        address _feedETH,
        address _feedUSDC,
        uint24 _feeTier,
        uint16 _slippageBps,
        uint32 _maxAgeSec
    ) external {
    ...
        $.router = IUniversalRouter(_universalRouter);
        $.permit2 = _permit2;
        $.WETH = IWETH9(_weth);
        $.USDC = IERC20(_usdc);
        $.FEED_ETH = AggregatorV3Interface(_feedETH);
        $.FEED_USDC = AggregatorV3Interface(_feedUSDC);
        $.FEE_TIER = _feeTier;
        $.SLIPPAGE_BPS = _slippageBps;
        $.MAX_AGE_SEC = _maxAgeSec;
    ...
        $.WETH.safeApprove(_permit2, type(uint256).max);
        IPermit2(_permit2).approve(address($.WETH), _universalRouter, type(uint160).max, type(uint48).max);
    }
```

위에서 볼 수 있듯이 이 재초기화 후 `SwapProxy` 계약은 공격자의 악성 계약이 `WETH` 토큰을 처리하도록 승인합니다.

로열티(`ETH`, `WETH` 또는 `USDC`로 표시될 수 있음)가 `RoyaltyManager`로 전송된 후, 어떤 엔터티든 `RoyaltyManager::distribute`를 호출하여 사용 가능한 모든 `ETH`/`WETH`를 `USDC`로 교환(swap)할 수 있습니다.

```solidity
    function distribute() external nonReentrant {
        // 1. Swap ETH to USDC if any ETH balance
        uint256 ethBalance = address(this).balance;
        if (ethBalance > 0) {
            swapProxy.swapETHToUSDC{ value: ethBalance }();
        }

        // 2. Swap WETH to USDC if any WETH balance
        uint256 wethBalance = WETH.balanceOf(address(this));
        if (wethBalance > 0) {
            swapProxy.swapWETHToUSDC(wethBalance);
        }

        // 3. Distribute USDC if any balance
        uint256 usdcBalance = USDC.balanceOf(address(this));
        if (usdcBalance >= 2e6) {
            USDC.approve(address(manager), usdcBalance);
            manager.distributeRoyalty(usdcBalance);
        }
    }
```

그러면 `RoyaltyManager`는 `swapETHToUSDC` 함수 호출을 통해 전체 `ETH` 잔액을 `SwapProxy`로 전송하고, 이 함수는 차례로 `router::execute`를 통해 모든 `ETH`를 공격자의 악성 계약으로 전송합니다.

```solidity
    function swapETHToUSDC() external payable nonReentrant returns (uint256 amountOut) {
        SwapImplStateStorage storage $ = _getStorage();

        uint256 amountIn = msg.value;
        if (amountIn == 0) revert AmountZero();
...
        $.router.execute{ value: amountIn }(commands, inputs, block.timestamp);
...
    }
```

마찬가지로 `RoyaltyManager`는 `swapWETHToUSDC` 함수를 호출하여 `SwapProxy`가 관리자의 전체 `WETH` 잔액을 가져오도록 한 다음, 함수는 공격자의 악성 `router` 계약을 호출합니다.

```solidity
    function swapWETHToUSDC(uint256 amountIn) external nonReentrant returns (uint256 amountOut) {
        SwapImplStateStorage storage $ = _getStorage();

        if (amountIn == 0) revert AmountZero();

        $.WETH.safeTransferFrom(msg.sender, address(this), amountIn);
...
        $.router.execute(commands, inputs, block.timestamp);
...
    }
```

재초기화로 인해 `SwapProxy`가 악성 계약이 모든 `WETH` 토큰을 처리하도록 승인했으므로, 계약은 이제 `SwapProxy`에서 모든 `WETH` 토큰을 간단히 가져올 수 있습니다.

## 권장 사항
`initialize` 함수가 두 번 이상 호출되지 않도록 방지하십시오. 이는 `WishWishToken`과 같은 다른 프로토콜 계약에서 볼 수 있는 것과 동일한 패턴을 따라 달성할 수 있습니다.

```solidity
    function init(address _usdc, address _usdcReceiver, address _redemptionSigner, address _owner) external {
        if (owner() != address(0)) revert AlreadyInitialized();
    ...
        _initializeOwner(_owner);
    }
```

위의 코드와 유사하게 `SwapImpl::initialize` 함수는 소유자가 이미 초기화된 경우 되돌리고(revert) 함수 호출 끝에 소유자를 초기화해야 합니다.

# [M-01] `RoyaltyManager` 계약의 오래된 `swapProxy` 참조 (Stale `swapProxy` reference in `RoyaltyManager` contracts)

_해결됨_

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`WishWishManager` 계약을 통해 소유자는 `setSwapProxy` 함수를 통해 `swapProxy` 주소를 업데이트할 수 있습니다. 그러나 `RoyaltyManager` 계약은 `getRoyaltyManager` 함수에서 창작자당 한 번 배포되며 생성자에서 `swapProxy`를 불변(immutable) 변수로 저장합니다. 창작자가 첫 번째 컬렉션을 출시하면 후속 컬렉션은 동일한 `RoyaltyManager` 인스턴스를 재사용하며, 이는 업데이트 후에도 이전 프록시 주소를 유지합니다. 다음 시나리오를 고려해 보십시오.

1. **CreatorA가 첫 번째 컬렉션 출시**: `launchNewCollection`을 호출하면 `getRoyaltyManager(msg.sender)`가 호출됩니다(코드 라인 256). CreatorA에 대한 `RoyaltyManager`가 존재하지 않으므로 현재 `swapProxy`(예: `0x123`)로 새 관리자가 배포됩니다(코드 라인 406). 이 인스턴스는 `royaltyMap[CreatorA]`에 저장됩니다.

```solidity
File: WishWishManager.sol
256:         address royaltyManager = getRoyaltyManager(msg.sender);
...
405:         if ($.royaltyMap[creator] == address(0)) {
406:             RoyaltyManager rm = new RoyaltyManager(address(this), $.swapProxy);
```

2. **관리자가 스왑 프록시 업데이트**: 소유자가 `WishWishManager.setSwapProxy(0x456)`을 호출하여 새로운 프록시 구현으로 업데이트합니다(예: 버그 수정 또는 최적화).

3. **CreatorA가 두 번째 컬렉션 출시**: `launchNewCollection`을 다시 호출하고, 이는 `getRoyaltyManager(msg.sender)`를 호출합니다. `royaltyMap[CreatorA]`에 이미 주소가 포함되어 있으므로 새 `RoyaltyManager`가 배포되지 않고 기존 관리자(이전 프록시 `0x123` 포함)가 재사용됩니다.

```solidity
File: WishWishManager.sol
403:     function getRoyaltyManager(address creator) public returns (address) {
404:         WishWishManagerStateStorage storage $ = _getStorage();
405:         if ($.royaltyMap[creator] == address(0)) {
406:                 ...
411:         }
412:@>       return $.royaltyMap[creator];
413:     }
```

4. **로열티 분배 실패**: `RoyaltyManager`에서 `distribute()`가 호출되면 오래된 프록시 주소 `0x123`을 사용하여 스왑을 시도합니다. 이 프록시가 더 이상 사용되지 않거나, 손상되었거나, 새 스왑 로직과 호환되지 않는 경우 트랜잭션이 되돌려져 로열티 분배가 방지될 수 있습니다.

새로운 창작자는 `new proxy`가 있는 `RoyaltyManager`를 받게 된다는 점에 유의하십시오. 문제는 이미 RoyaltyManager가 생성된 창작자에게 있습니다.

## 권장 사항

새 컬렉션은 `RoyaltyManager` 인스턴스에서 `new proxy`를 사용해야 합니다. 이를 달성하려면 창작자의 기존 인스턴스가 배포된 이후 프록시가 변경된 경우 `getRoyaltyManager`가 RoyaltyManager 계약을 재배포하도록 수정하십시오.

기존 `RoyaltyManager` 계약의 `swapProxy` 참조를 업데이트하는 관리자 함수를 추가하는 것을 고려하십시오.

# [L-01] L2 시퀀서 상태 확인 제안 (Suggeset verifying L2 sequencer's status)

_해결됨_

Wish 계약은 Base Chain에 배포됩니다. Base chain은 L2 체인으로서, 모범 사례 중 하나로 L2 네트워크에서 애플리케이션을 실행할 때 L2 시퀀서 피드를 사용하여 시퀀서의 상태를 확인해야 합니다.

```solidity
    function _minOutFromFeeds(uint256 amountInWETH) internal view returns (uint256) {
        SwapImplStateStorage storage $ = _getStorage();
        // ethPxE18 = 4000e18
        (uint256 ethPxE18, uint256 ethAge) = _readE18($.FEED_ETH);
        (uint256 usdcPxE18, ) = _readE18($.FEED_USDC);
        // check stale price.
        if (ethAge > $.MAX_AGE_SEC) revert StalePrice();
        // usdcPxE18 = 1e18, rateE18 = 4000e18
        uint256 rateE18 = (ethPxE18 * 1e18) / usdcPxE18;
        // 1e18 * 4000 e18 / 1e18 = 4000 e18
        uint256 expectedOutE18 = (amountInWETH * rateE18) / 1e18;
        // USDC_DECIMALS = 6
        // 4000e6
        uint256 expectedOut = expectedOutE18 / (10 ** (18 - $.USDC_DECIMALS));
        // add one slippage here.
        return (expectedOut * (10_000 - $.SLIPPAGE_BPS)) / 10_000;
    }
```

권장 사항: L2 시퀀서 상태를 확인하십시오. https://docs.chain.link/data-feeds/l2-sequencer-feeds 를 참조하십시오.

# [L-02] `SwapImpl`에서 더 이상 사용되지 않는 `answeredInRound` 사용 (Deprecated `answeredInRound` is used in `SwapImpl`)

_해결됨_

SwapImpl에서 슬리피지를 계산하기 위해 현재 토큰 가격을 가져옵니다. `_readE18` 함수에서 반환 값의 유효성을 확인하기 위해 `answeredInRound`를 사용합니다.

여기서 문제는 https://docs.chain.link/data-feeds/api-reference#functions-in-aggregatorv3interface 에 따르면 `answeredInRound`가 더 이상 사용되지 않는다는(deprecated) 것입니다. 이전에는 답변을 계산하는 데 여러 라운드가 걸릴 수 있을 때 사용되었습니다.

```solidity
    function _readE18(AggregatorV3Interface feed) internal view returns (uint256 pxE18, uint256 age) {
        // https://docs.chain.link/data-feeds/api-reference#functions-in-aggregatorv3interface
        (, int256 ans, , uint256 updatedAt, uint80 answeredInRound) = feed.latestRoundData();

        if (answeredInRound == 0 || ans <= 0) revert BadFeed();
    }
```

권장 사항: 반환 값의 유효성을 확인하기 위해 더 이상 사용되지 않는 `answeredInRound`를 사용하지 마십시오.

# [L-03] 업그레이드 가능한 설계를 나타내는 프록시 패턴임에도 불구하고 `SwapImpl`에 업그레이드 기능이 부족함 (`SwapImpl` lacks upgradeability despite proxy pattern indicating upgradeable design)

_해결됨_

`SwapImpl` 계약은 `UUPSUpgradeable`을 상속하지 않으므로, 프록시 계약이 업그레이드 가능한 구현에 위임하는 코드베이스 전체에서 사용되는 확립된 프록시 패턴을 깨뜨립니다.

`WishWishManagerProxy` 및 `WishWishTokenProxy`와 유사하게 `SwapProxy`는 업그레이드 가능한 구현에 위임해야 하는 EIP-1967 준수 프록시로 설계되었습니다. 그러나 `SwapImpl`은 `UUPSUpgradeable`을 상속하지 않아 스왑 로직의 제자리(in-place) 업그레이드를 방지합니다.

```solidity
File: SwapProxy.sol
4: /// @dev This contract is a proxy contract for SwapImpl.
5: contract SwapProxy {
```

이것은 코드베이스의 다른 프록시와 동일한 패턴을 따릅니다:

```solidity
File: WishWishManagerProxy.sol
4: /// @dev This contract is a proxy contract for WishWishManager.
5: contract WishWishManagerProxy {
```

여기서 `WishWishManager`는 `UUPSUpgradeable`을 적절하게 상속합니다:

```solidity
File: WishWishManager.sol
20: contract WishWishManager is Ownable, EIP712, ReentrancyGuard, UUPSUpgradeable {
```

하지만 `SwapImpl`에는 이 상속이 없습니다:

```solidity
File: SwapImpl.sol
15: contract SwapImpl is ReentrancyGuard, Ownable {
```

이러한 아키텍처 불일치는 다른 핵심 프로토콜 계약과 달리 버그가 발견되거나 새로운 기능이 필요한 경우 스왑 로직의 업그레이드를 불가능하게 합니다.

- 업그레이드를 활성화하려면 `SwapImpl`이 `UUPSUpgradeable`을 상속하도록 만드십시오.
- 향후 업그레이드 안전성을 위해 `SwapImplStateStorage`에 스토리지 간격(storage gap)을 추가하십시오.
- 적절한 소유자 승인이 있는 `_authorizeUpgrade` 함수를 구현하십시오.
- 업그레이드 가능성이 다른 핵심 계약에 사용되는 확립된 프록시 패턴과 일치하는지 확인하십시오.

# [L-04] 업그레이드 가능한 계약에서 스토리지 간격 누락 (Missing storage gaps in upgradeable contracts)

_해결됨_

`WishWishToken` 및 `WishWishManager` 계약은 UUPS 프록시 패턴을 통해 업그레이드 가능하도록 설계되었지만 스토리지 레이아웃 끝에 예약된 스토리지 간격이 포함되어 있지 않습니다.

두 계약 모두 사용자 정의 스토리지 구조체(`WishWishTokenStateStorage` 및 `WishWishManagerStateStorage`)에 상태를 저장하므로 향후 버전에서 기본 계약에 새 변수가 도입될 수 있습니다. 예약된 간격이 없으면 이러한 추가는 향후 업그레이드에서 스토리지 레이아웃 충돌로 이어질 수 있습니다.

`WishWishTokenStateStorage` 및 `WishWishManagerStateStorage`에 예약된 스토리지 간격을 추가하십시오.

# [L-05] 관리자 교체가 기존 컬렉션 기능을 중단시킴 (Manager rotation breaks legacy collection functionality)

_해결됨_

`WishWishManager`는 프록시 주소가 불변으로 유지되는 반면 구현은 업그레이드될 수 있는 프록시 패턴을 사용합니다. 그러나 `WishWishToken` 및 `WishWishFactory`는 독립적인 관리자 교체를 허용하는 `setManager` 함수를 노출하여 관리자가 프록시 주소라는 근본적인 가정을 깨뜨립니다.

이것은 아키텍처의 혼란을 야기합니다. 프록시는 관리자 역할에 대한 업그레이드 가능성을 제공하지만 개별 계약은 관리자 참조를 독립적으로 변경하여 이를 재정의할 수 있습니다. 프록시의 불변성은 관리자 업그레이드가 별도의 관리자 필드가 아닌 구현 업그레이드를 통해서만 발생해야 함을 시사합니다.

다음 시나리오를 고려해 보십시오.

1. 소유자가 고정 주소 0x123이 있는 프록시 뒤에 WishWishManager를 배포합니다.
2. 소유자가 `WishWishToken`에서 `setManager`를 호출하여 관리자를 0x123에서 0x456으로 변경합니다.
3. 기존 컬렉션이 상자 발행을 시도합니다. WishWishManager가 `token.creditCreator()`를 호출합니다.

```solidity
File: WishWishManager.sol
348:             WishWishToken(address($.wishToken)).creditCreator(c.creatorAddress, creatorCut);
```

4. 호출자(0x123) ≠ 현재 관리자(0x456)이므로 WishWishToken이 `NotManager`로 `되돌려집니다(reverts)`.

```solidity
File: WishWishToken.sol
135:     function creditCreator(address creator, uint256 amount) external onlyManager {
```

5. 기존 및 새 컬렉션을 사용할 수 없게 됩니다.

또한 `revealBox` 및 `distributeRoyalty` 함수도 영향을 받을 수 있습니다.

- 관리자는 항상 프록시 주소여야 하므로 `WishWishToken` 및 `WishWishFactory`에서 `setManager` 함수를 제거하십시오.
- 관리자 교체가 필요한 경우 독립적인 관리자 필드 대신 프록시 업그레이드를 통해 구현하십시오.

# [L-06] 개별 토큰 로열티 기능이 더 이상 지원되지 않음 (Individual token royalty functionality no longer supported)

_해결됨_

`WishWishManager`는 `WishWishManager::launchNewCollection` 동안 배포된 모든 `WishWishBox` 및 `WishWishCollectable` 계약의 소유자입니다. `WishWishBox` 및 `WishWishCollectable` 계약은 `ERC2981`을 상속하므로 로열티 정보와 관련된 상태를 보유할 수 있습니다. 이러한 계약은 계약에서 파생된 모든 `tokenIds`에 적용되는 기본 로열티를 저장하거나 지정된 `tokenId`에만 적용되는 특정 토큰 로열티를 저장할 수 있습니다.

이러한 로열티 값은 `WishWishManager` 계약에 의해서만 설정될 수 있습니다.

```solidity
    function setGlobalRoyalty(uint96 _feeNumerator) external onlyOwner {
        royaltyFeeNumerator = _feeNumerator;
        _setDefaultRoyalty(royaltyReceiver, _feeNumerator);
    }

    /**
     * @notice Sets per-token royalty information.
     */
    function setTokenRoyalty(uint256 _tokenId, address _receiver, uint96 _feeNumerator) external onlyOwner {
        _setTokenRoyalty(_tokenId, _receiver, _feeNumerator);
    }
```

그러나 `WishWishManager`는 `setGlobalRoyalty` 함수만 노출하므로 `WishWishBox` 및 `WishWishColectable` 계약에서 토큰별 로열티를 더 이상 설정할 수 없습니다. 이것이 의도된 경우 `setTokenRoyalty` 함수를 완전히 제거하고 `WishWishBox::burnBox` 및 `WishWishBox::batchBurnBox` 함수에서 `_resetTokenRoyalty` 호출을 제거하는 것을 고려하십시오.

계약이 토큰별 로열티를 지원할 것으로 예상되는 경우 `WishWishManager` 계약에서 `setTokenRoyalty` 함수를 노출하십시오.

# [L-07] USDC에 대한 오래된 피드 확인 누락 (Missing stale feed check for USDC)

_인정됨_

`SwapImpl::_minOutFromFeeds` 함수는 ETH 및 USDC 오라클 피드에서 얻은 가격을 고려하여 스왑에 허용되는 최소 금액을 계산하기 위해 스왑 작업 중에 호출됩니다. 이 호출 중에 코드는 ETH 가격의 나이(age)를 검증하지만 USDC 가격의 나이는 검증하지 않습니다. USDC의 가격은 ETH만큼 수시로 변동하지 않을 것으로 예상되지만 예기치 않은 시장 이벤트로부터 보호하기 위해 가격 나이를 검증하는 것이 좋습니다.

```solidity
// SwapImpl
    function _minOutFromFeeds(uint256 amountInWETH) internal view returns (uint256) {
        SwapImplStateStorage storage $ = _getStorage();
        (uint256 ethPxE18, uint256 ethAge) = _readE18($.FEED_ETH);
        (uint256 usdcPxE18, ) = _readE18($.FEED_USDC);

        if (ethAge > $.MAX_AGE_SEC) revert StalePrice();

        uint256 rateE18 = (ethPxE18 * 1e18) / usdcPxE18;

        uint256 expectedOutE18 = (amountInWETH * rateE18) / 1e18;
        uint256 expectedOut = expectedOutE18 / (10 ** (18 - $.USDC_DECIMALS));

        return (expectedOut * (10_000 - $.SLIPPAGE_BPS)) / 10_000;
    }
```

USDC 가격의 나이도 검증하는 것을 고려하십시오.

# [L-08] `_mintBox` 중 `protocolCut`이 실제로 소각되지 않음 (`protocolCut` not actually burned during `_mintBox`)

_해결됨_

코드 주석은 `WishWishManager::_mintBox` 함수가 사용자가 상자를 발행할 때 소각될 `protocolCut`을 계산한다고 제안합니다. 그러나 소각 함수를 호출하는 대신 코드는 단순히 토큰을 `address(0)`으로 전송하며, 이는 `totalSupply`를 줄이지 않습니다. 결과적으로 토큰은 유통 상태로 유지됩니다.

```solidity
// WishWishManager.sol
    function _mintBox(MintData calldata mintData, bytes calldata signature) internal {
...
        if (mintData.fee != 0) {
            uint256 protocolCut = (mintData.fee * $.MINT_FEE_PERCENT) / 1 ether;
            creatorCut = mintData.fee - protocolCut;
            $.wishToken.transferFrom(msg.sender, address(0), protocolCut); // burn protocol fee
            $.wishToken.transferFrom(msg.sender, c.creatorAddress, creatorCut);
            WishWishToken(address($.wishToken)).creditCreator(c.creatorAddress, creatorCut);
        }
...
```

**`$.manager` 상태 변수가 업데이트될 수 있고 `ERC20::_transfer` 함수(Solady에서 상속됨)가 `address(0)`으로 또는 `address(0)`으로부터 전송하는 것에 대한 제한이 없기 때문에 프로토콜이 `address(0)`으로 전송된 `protocolCut` 토큰을 복구하는 것이 여전히 가능하다는 점에 유의하십시오.**

```solidity
// WishWishToken.sol
    function transferFrom(address from, address to, uint256 amount) public override returns (bool) {
        WishWishTokenStateStorage storage $ = _getStorage();
        if (msg.sender != $.manager) revert TransfersRestricted();
        _transfer(from, to, amount);
        return true;
    }
```

```solidity
// ERC20.sol
    /// @dev Moves `amount` of tokens from `from` to `to`.
    function _transfer(address from, address to, uint256 amount) internal virtual {
        _beforeTokenTransfer(from, to, amount);
        /// @solidity memory-safe-assembly
        assembly {
            let from_ := shl(96, from)
            // Compute the balance slot and load its value.
            mstore(0x0c, or(from_, _BALANCE_SLOT_SEED))
            let fromBalanceSlot := keccak256(0x0c, 0x20)
            let fromBalance := sload(fromBalanceSlot)
            // Revert if insufficient balance.
            if gt(amount, fromBalance) {
                mstore(0x00, 0xf4d678b8) // `InsufficientBalance()`.
                revert(0x1c, 0x04)
            }
            // Subtract and store the updated balance.
            sstore(fromBalanceSlot, sub(fromBalance, amount))
            // Compute the balance slot of `to`.
            mstore(0x00, to)
            let toBalanceSlot := keccak256(0x0c, 0x20)
            // Add and store the updated balance of `to`.
            // Will not overflow because the sum of all user balances
            // cannot exceed the maximum uint256 value.
            sstore(toBalanceSlot, add(sload(toBalanceSlot), amount))
            // Emit the {Transfer} event.
            mstore(0x20, amount)
            log3(0x20, 0x20, _TRANSFER_EVENT_SIGNATURE, shr(96, from_), shr(96, mload(0x0c)))
        }
        _afterTokenTransfer(from, to, amount);
    }
```

의도가 실제로 `protocolCut`을 소각하는 것이라면, `WishWishToken.sol`에 관리자만 호출할 수 있는 `burnFrom` 함수를 도입하여 내부적으로 `ERC20::_burn()`을 호출하여 토큰을 적절히 파괴하고 총 공급을 감소시키는 것을 고려하십시오.

# [L-09] 스왑 기한으로 `block.timestamp`를 사용하여 MEV 보호 제거 (Using `block.timestamp` as swap deadline removes MEV protection)

_인정됨_

`SwapImpl` 계약은 `swapETHToUSDC`(107행) 및 `swapWETHToUSDC`(130행) 모두에서 Uniswap Universal Router의 `execute` 함수를 호출할 때 `block.timestamp`를 deadline 매개변수로 사용합니다. 이는 멤풀에 얼마나 오래 머물렀는지에 관계없이 `block.timestamp`가 항상 트랜잭션이 채굴되는 현재 블록 시간을 나타내므로 기한 보호를 효과적으로 비활성화합니다.

```solidity
File: SwapImpl.sol
106:        uint256 balBefore = $.USDC.balanceOf(msg.sender);
107:        $.router.execute{ value: amountIn }(commands, inputs, block.timestamp);
108:        uint256 balAfter = $.USDC.balanceOf(msg.sender);
...
...
129:        uint256 balBefore = $.USDC.balanceOf(msg.sender);
130:        $.router.execute(commands, inputs, block.timestamp);
131:        uint256 balAfter = $.USDC.balanceOf(msg.sender);
```

다음 시나리오를 고려해 보십시오.

1. 공격자는 스왑할 10 ETH가 포함된 보류 중인 `distribute()` 호출을 관찰합니다.
2. 오라클 기반 `minOut`은 ETH = $2000일 때 계산되어 ~$20,000 USDC(슬리피지 제외)를 예상했습니다.
3. 공격자는 ETH가 $1800로 떨어질 때까지 트랜잭션 실행을 지연시킵니다.
4. 트랜잭션은 $1800 비율로 실행되어 ~$18,000 USDC만 얻지만, 슬리피지 허용 오차가 백분율 기준이므로 $2000 가격의 `minOut`은 여전히 통과합니다.
5. 창작자는 로열티 가치에서 ~$2000를 잃습니다.

**권장 사항**

사용자가 제어하는 `deadline` 매개변수를 추가하십시오. 이를 통해 호출자는 합리적인 기한을 지정하여 스왑이 허용 가능한 시간 내에 실행되거나 되돌려지도록 하여 MEV 추출 및 오래된 가격 실행으로부터 보호할 수 있습니다.

# [L-10] 무허가 `distribute()`로 인해 이익을 위한 셀프 샌드위치 허용 (permissionless `distribute()` allows self sandwiching for profit)

_해결됨_

`RoyaltyManager::distribute()`가 무허가(permissionless)이므로 Base에는 공개 멤풀이 없기 때문에 MEV 봇을 통하거나 셀프 샌드위칭(self sandwiching)을 통해 창작자 자신이 슬리피지 %까지 가치를 추출할 수 있습니다.

공격은 다음과 같습니다:
- 공격자는 로열티 분배 이벤트를 수신 대기합니다.
- distribute()를 호출하고 풀을 조작하여 로열티의 슬리피지 금액까지 추출합니다.

따라서 기본적으로 슬리피지 금액을 잃는 것이 보장됩니다.

여기서 공격자는 창작자일 수도 있습니다. 왜냐하면 그가 받은 로열티에서 슬리피지 금액을 추출하면 공격 후 남은 금액에 적용될 `ROYALTY_FEE_PERCENT`를 덜 지불하게 되기 때문입니다.

30 usdc 분배와 10%의 슬리피지 및 `ROYALTY_FEE_PERCENT` = 5%의 경우 경제학은 다음과 같습니다:
- 추출된 슬리피지 금액 = 3$.
- 프로토콜 컷 = 1.35$.
- 손실된 프로토콜 컷 0.15$.
 avg txn fees: 0.015 -> 0.030$.

이것은 protocolCut 손실 측면이며, 사용자 손실의 경우 매번 슬리피지를 잃는 것이 보장됩니다.

따라서 프로토콜이 분배를 처리하기로 선택한 경우 공격자에게 경제적으로 이익이 되고 경제적으로 실행 가능합니다.

**권장 사항**

팀이 제어하는 오프체인 백엔드 봇 주소에 의해서만 분배가 이루어지도록 보호하십시오.
