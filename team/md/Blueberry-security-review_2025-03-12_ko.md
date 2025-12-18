# 소개

**Pashov Audit Group**은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 경험을 제공하기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 귀하의 블록체인 프로토콜을 위해 경험 많은 연구원들의 최선의 노력을 보장합니다. 이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락주십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 절대 확인할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로 가능한 한 많은 취약점을 찾으려고 시도하는 것입니다. 검토 후 100% 보안을 보장할 수 없으며, 검토가 스마트 계약의 문제를 발견할 것이라고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

**Blueberryfi/blueberry-v2-contracts** 저장소에 대한 시간 제한 보안 검토가 **Pashov Audit Group**에 의해 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# Blueberry 소개

Blueberry는 사용자가 최대 25배의 레버리지로 자산을 대출하고 차입할 수 있게 하여 DeFi의 주요 중개업체 역할을 하여 다양한 거래 전략을 실행할 수 있도록 하는 레버리지 대출 프로토콜입니다. Blueberry의 두 번째 버전은 디자인 개선 및 분산형 레버리지 거래를 더 쉽게 접근할 수 있도록 확장성에 중점을 둔 완전한 재작성입니다. 이 감사는 Blueberry 프로토콜의 V2에 중점을 두었습니다.

# 위험 분류

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| --- | --- | --- | --- |
| **가능성: 높음** | 치명적 | 높음 | 중간 |
| **가능성: 중간** | 높음 | 중간 | 낮음 |
| **가능성: 낮음** | 중간 | 낮음 | 낮음 |

## 영향

- 높음 - 프로토콜의 자산에 중대한 물질적 손실을 초래하거나 사용자 그룹에 심각한 해를 끼칩니다.
- 중간 - 프로토콜의 자산에 중간 정도의 물질적 손실을 초래하거나 사용자 그룹에 어느 정도 해를 끼칩니다.
- 낮음 - 프로토콜의 자산에 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 해를 끼칩니다.

## 가능성

- 높음 - 온체인 조건을 모방한 합리적인 가정 하에 공격 경로가 가능하며, 훔치거나 손실될 수 있는 자금의 양에 비해 공격 비용이 상대적으로 낮습니다.
- 중간 - 조건부로 인센티브가 주어지는 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.
- 낮음 - 가정이 너무 많거나 너무 가능성이 낮거나 공격자가 인센티브가 거의 없거나 전혀 없는 상태에서 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치

- 치명적 - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)
- 높음 - 수정해야 함 (아직 배포되지 않은 경우 배포 전)
- 중간 - 수정해야 함
- 낮음 - 수정할 수 있음

# 보안 평가 요약

_검토 커밋 해시_ - [8947ad027c5456fb968d30e0148c773402d43458](https://github.com/Blueberryfi/blueberry-v2-contracts/tree/8947ad027c5456fb968d30e0148c773402d43458)

_수정 검토 커밋 해시_ - [03dc79ddbacac2a4a1b194a06a7f9dfca6ad2fcb](https://github.com/Blueberryfi/blueberry-v2-contracts/tree/03dc79ddbacac2a4a1b194a06a7f9dfca6ad2fcb)

### 범위

다음 스마트 계약이 감사 범위에 포함되었습니다.

- `HyperEvmVault` 
- `VaultEscrow` 

# 발견 사항

# [H-01] 승인되지 않은 요청이 총 자산에서 차감되지 않음

## 심각도

**영향:** 중간

**가능성:** 높음

## 설명
`HyperEvmVault` 계약에서 `requestRedeem()` 함수는 요청된 자산과 주식을 기록하지만 총 자산(`tvl()`)과 총 공급(`totalSupply()`)에서 이러한 금액을 차감하지 않습니다. 이는 인출이 완료될 때까지 잘못된 주가 계산으로 이어집니다.

```solidity
function requestRedeem(uint256 shares_) external nonReentrant {
        V1Storage storage $ = _getV1Storage();
        uint256 balance = this.balanceOf(msg.sender);
        // Determine if the user withdrawal request is valid
        require(shares_ <= balance, Errors.INSUFFICIENT_BALANCE());

        RedeemRequest storage request = $.redeemRequests[msg.sender];
        request.shares += shares_;
        require(request.shares <= balance, Errors.INSUFFICIENT_BALANCE());

        // User will redeem assets at the current share price
        uint256 tvl_ = _totalEscrowValue($);
        _takeFee($, tvl_);
        uint256 assetsToRedeem = shares_.mulDivDown(tvl_, totalSupply());

        request.assets += uint64(assetsToRedeem);
        $.totalRedeemRequests += uint64(assetsToRedeem);
        --snip--
    }
```

**예시:**
다음은 두 가지 시나리오(조정 포함 및 미포함)의 비교입니다.

1. **초기 상태**:  
   - 총계 조정 없음: 총 고정 가치(TVL)는 1,000,010 자산이고, 총 공급량은 1,000,010 주입니다.  
   - 총계 조정 포함: TVL은 1,000,010 자산이고, 총 공급량은 1,000,010 주입니다.  

2. **사용자 상환 요청**:  
   - 총계 조정 없음: 사용자가 1,000,000 주와 1,000,000 자산을 상환하도록 요청합니다.  
   - 총계 조정 포함: 사용자가 1,000,000 주와 1,000,000 자산을 상환하도록 요청합니다.  

3. **요청 후**:  
   - 총계 조정 없음: TVL과 총 공급량은 각각 1,000,010 자산과 1,000,010 주로 변경되지 않습니다.  
   - 총계 조정 포함: TVL은 10 자산으로 조정되고, 총 공급량은 10 주로 조정됩니다.  

4. **볼트 5 자산 손실**:  
   - 총계 조정 없음: TVL은 1,000,005 자산으로 감소하지만 총 공급량은 1,000,010 주를 유지합니다.  
   - 총계 조정 포함: TVL은 5 자산으로 감소하고, 총 공급량은 10 주를 유지합니다.  

5. **주가 계산**:  
   - 총계 조정 없음: 주가는 약 1(1,000,005 / 1,000,010)로 계산됩니다.  
   - 총계 조정 포함: 주가는 0.5(5 / 10)로 계산됩니다.  

6. **다른 사용자에게 미치는 영향**:  
   - 총계 조정 없음: 다른 사용자는 잘못된 주가(~1)로 입금하거나 인출할 수 있습니다.  
   - 총계 조정 포함: 다른 사용자는 올바른 주가(0.5)로 입금하거나 인출할 수 있습니다.

## 권장 사항
상환 요청이 이루어지면 요청된 상환 금액을 총 자산(`tvl()`) 및 총 공급량에서 차감하십시오.

# [H-02] 인출 확인 우회 가능

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

VaultEscrow에서 withdraw() 함수를 통해 코어 체인의 Vault에서 자산을 인출합니다. withdraw() 함수에는 하나의 보안 온전성 검사가 있습니다. 이것은 우리가 볼트에 입금한 것보다 더 많은 자산을 인출하지 않도록 합니다.

그러나 이 보안 검사는 우회될 수 있습니다. 우리는 `VAULT_EQUITY_PRECOMPILE_ADDRESS`를 기반으로 볼트의 자본을 얻습니다.

[HyperLiquid 문서](https://hyperliquid.gitbook.io/hyperliquid-docs/for-developers/hyperevm/interacting-with-hypercore)에 따르면 테스트넷 EVM은 HyperCore 정보를 쿼리할 수 있는 사전 컴파일을 제공합니다. 사전 컴파일 주소는 0x0000000000000000000000000000000000000800에서 시작하며 perps 포지션, 현물 잔액, 볼트 자본, 스테이킹 위임, 오라클 가격 및 L1 블록 번호를 쿼리하는 메서드가 있습니다.

값은 EVM 블록이 구성될 때 최신 HyperCore 상태와 일치하도록 보장됩니다.

따라서 새로운 EVM 블록을 생성하기 시작할 때 Core Chain의 최신 상태를 `VAULT_EQUITY_PRECOMPILE_ADDRESS`를 포함한 EVM 체인의 사전 컴파일 계약에 동기화합니다. 동일한 EVM 블록에서 Core Chain은 트랜잭션당 데이터를 동기화하지 않습니다. 따라서 `VAULT_EQUITY_PRECOMPILE_ADDRESS`에서 읽은 데이터가 오래되었을 가능성이 큽니다.

예를 들어:
1. Alice는 vault escrow 1을 통해 1000 USDC를 입금합니다.
2. Bob은 vault escrow 2를 통해 1000 USDC를 입금합니다.
3. 시스템이 EVM 블록 100을 구성할 때 볼트의 자본을 `VAULT_EQUITY_PRECOMPILE_ADDRESS`, escrow 1(1000), escrow 2(1000)에 동기화합니다.
4. Bob은 블록 1000에서 vault escrow 2에서 인출을 요청합니다.
5. Alice는 블록 1000에서 vault escrow 2에서 인출을 요청합니다. escrow 2의 볼트 자본이 1000을 유지하므로 이 보안 검사가 통과됩니다. Alice의 EVM 트랜잭션은 통과할 수 있습니다. 그러나 이 작업은 L1 체인에서 실패합니다.
6. Alice는 1000 USDC를 받기 위해 인출합니다.
7. Bob은 인출을 시도하지만 escrow 2에 자산이 충분하지 않기 때문에 Bob의 트랜잭션은 되돌려집니다.

```solidity
    address public constant VAULT_EQUITY_PRECOMPILE_ADDRESS = 0x0000000000000000000000000000000000000802;

    function withdraw(uint64 assets_) external override onlyVaultWrapper {
@>        require(assets_ <= _vaultEquity(), Errors.INSUFFICIENT_VAULT_EQUITY());
        uint256 amountPerp = (_perpDecimals > _evmSpotDecimals)
            ? assets_ * (10 ** (_perpDecimals - _evmSpotDecimals))
            : assets_ / (10 ** (_evmSpotDecimals - _perpDecimals));

        L1_WRITE_PRECOMPILE.sendVaultTransfer(_vault, false, uint64(amountPerp));
        L1_WRITE_PRECOMPILE.sendUsdClassTransfer(uint64(amountPerp), false);
        L1_WRITE_PRECOMPILE.sendSpot(HYPERLIQUID_SPOT_BRIDGE, _assetIndex, assets_);
    }
```
```solidity
    function _vaultEquity() internal view returns (uint256) {
        (bool success, bytes memory result) =
            VAULT_EQUITY_PRECOMPILE_ADDRESS.staticcall(abi.encode(address(this), _vault));
        require(success, "VaultEquity precompile call failed");

        UserVaultEquity memory userVaultEquity = abi.decode(result, (UserVaultEquity));

        uint256 equityInSpot = (_perpDecimals > _evmSpotDecimals)
            ? userVaultEquity.equity / (10 ** (_perpDecimals - _evmSpotDecimals))
            : userVaultEquity.equity * (10 ** (_evmSpotDecimals - _perpDecimals));

        return equityInSpot;
    }
```

## 권장 사항

최신 볼트 자본을 기록하기 위해 하나의 내부 회계를 추가해야 합니다. 이 블록의 첫 번째 트랜잭션인 경우 사전 컴파일 계약에서 읽고 볼트 자본을 저장합니다. 이 블록의 다음 트랜잭션인 경우 내부 회계에서 볼트 자본을 읽습니다.

# [H-03] evm과 core 체인 간의 잘못된 `erc20` 전송 구현

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

VaultEscrow에서 사용자의 예치금을 EVM Chain에서 Core Chain으로 전송하고 관련 볼트에 예치합니다.

deposit() 함수에서 USDC 토큰을 사전 컴파일된 시스템 주소 `0x2222222222222222222222222222222222222222`로 전송합니다. 시스템 주소는 EVM 체인에서 Core 체인으로 USDC 토큰을 전송하는 데 도움이 됩니다.

여기서 문제는 [이것](https://discord.com/channels/1029781241702129716/1208476333089497189/1348533264629104660)에 따라 HyperLiquid가 전송 방법을 변경한다는 것입니다.

[최신 문서](https://hyperliquid.gitbook.io/hyperliquid-docs/for-developers/hyperevm/hypercore-less-than-greater-than-hyperevm-transfers)에 따르면 모든 토큰에는 Core에 특정 시스템 주소가 있습니다. 이 주소는 첫 번째 바이트가 0x20이고 나머지 바이트는 모두 0이며 토큰 인덱스는 big-endian 형식으로 인코딩됩니다. 예를 들어 토큰 인덱스 200의 경우 시스템 주소는 0x20000000000000000000000000000000000000c8입니다.

```solidity
    /// @notice The address of the hyperliquid spot bridge, used for sending tokens to L1.
    address public constant HYPERLIQUID_SPOT_BRIDGE = 0x2222222222222222222222222222222222222222;

    function deposit(uint64 amount) external onlyVaultWrapper {
        ERC20Upgradeable(_asset).safeTransfer(HYPERLIQUID_SPOT_BRIDGE, amount);
    }
```
따라서 이 레거시 시스템 주소 `0x2222222222222222222222222222222222222222`를 사용해서는 안 됩니다.

## 권장 사항

최신 문서를 기반으로 구현을 리팩터링하십시오. 관련 토큰을 관련 시스템 주소로 전송하십시오.

# [H-04] 악의적인 사용자가 다른 인출을 차단할 수 있음

## 심각도

**영향:** 높음

**가능성:** 중간

## 설명

HyperEvmVault에서 사용자는 상환 작업을 완료하기 위해 2단계를 수행해야 합니다.
1단계: 상환 요청. 이 단계에서는 현재 주가를 기준으로 상환 금액을 계산합니다.
2단계: 상환. `$.redeemRequests[msg.sender]`에서 실제 상환 금액을 차감합니다.

여기서 문제는 `$.redeemRequests[msg.sender]`에서 자산, 주식을 차감할 때 하나의 메모리 변수를 사용하고 업데이트된 값을 스토리지에 저장하지 못한다는 것입니다. `$.redeemRequests[msg.sender]`에서 자산과 주식을 차감하지 못합니다.

이 취약점에는 몇 가지 영향이 있습니다.
1. 전송 확인에서 사용자가 여전히 상환 요청이 있다고 생각하기 때문에 사용자가 주식을 전송하지 못합니다.
2. 일반 사용자의 인출이 악의적인 사용자에 의해 차단될 수 있습니다.

예를 들어:
1. 주가가 1:1이라고 가정해 봅시다.
2. Alice는 2000 USDC를 입금합니다.
3. Bob은 1000 USDC를 입금합니다.
4. Bob은 1000 USDC 상환을 요청합니다.
5. Alice는 1000 USDC 상환을 요청합니다.
6. Alice는 처음에 1000 USDC를 받기 위해 인출합니다.
7. Alice는 두 번째로 1000 USDC를 받기 위해 인출합니다.
8. Bob이 인출하려고 하면 Escrow 볼트에 자금이 없기 때문에 인출이 되돌려집니다.

```solidity
    function _beforeWithdraw(uint256 assets_, uint256 shares_) internal {
        V1Storage storage $ = _getV1Storage();
@>        RedeemRequest memory request = $.redeemRequests[msg.sender];
        require(request.assets >= assets_, Errors.WITHDRAW_TOO_LARGE());
        require(request.shares >= shares_, Errors.WITHDRAW_TOO_LARGE());
        request.assets -= uint64(assets_);
        request.shares -= shares_;
        $.totalRedeemRequests -= uint64(assets_);
        _fetchAssets(assets_);
    }

```
```solidity
    function _beforeTransfer(address from_, address, /*to_*/ uint256 amount_) internal {
        V1Storage storage $ = _getV1Storage();
        uint256 balance = this.balanceOf(from_);
        RedeemRequest memory request = $.redeemRequests[from_];

        _takeFee($, _totalEscrowValue($));

        if (request.shares > 0) {
            require(balance - amount_ >= request.shares, Errors.TRANSFER_BLOCKED());
        }
    }
```

## 권장 사항

```diff
         request.assets -= uint64(assets_);
         request.shares -= shares_;
+        $.redeemRequests[msg.sender] = request;
```

# [H-05] 사용자가 승인된 인출을 통해 에스크로를 고갈시킬 수 있음

## 심각도

**영향:** 높음  

**가능성:** 중간  

## 설명

사용자가 인출을 원할 때마다 `HyperEvmVault::requestRedeem` 함수를 통해 요청을 생성해야 합니다.

```solidity
function requestRedeem(uint256 shares_) external nonReentrant {
    V1Storage storage $ = _getV1Storage();
    uint256 balance = this.balanceOf(msg.sender);
    // Determine if the user withdrawal request is valid
    require(shares_ <= balance, Errors.INSUFFICIENT_BALANCE());

    RedeemRequest storage request = $.redeemRequests[msg.sender];
    
    request.shares += shares_;
    require(request.shares <= balance, Errors.INSUFFICIENT_BALANCE());

    uint256 tvl_ = _totalEscrowValue($);
    _takeFee($, tvl_);
    uint256 assetsToRedeem = shares_.mulDivDown(tvl_, totalSupply());

    request.assets += uint64(assetsToRedeem);
    $.totalRedeemRequests += uint64(assetsToRedeem);
    emit RedeemRequested(msg.sender, shares_, assetsToRedeem);

    VaultEscrow escrowToRedeem = VaultEscrow($.escrows[redeemEscrowIndex()]);
    escrowToRedeem.withdraw(uint64(assetsToRedeem));
}
```

이 함수는 자산/주식 비율을 계산하여 사용자 요청에 저장합니다. 인출 시 `_beforeWithdraw` 함수가 호출됩니다.

```solidity
function _beforeWithdraw(uint256 assets_, uint256 shares_) internal {
    V1Storage storage $ = _getV1Storage();

    RedeemRequest memory request = $.redeemRequests[msg.sender];
    require(request.assets >= assets_, Errors.WITHDRAW_TOO_LARGE());
    require(request.shares >= shares_, Errors.WITHDRAW_TOO_LARGE());

    request.assets -= uint64(assets_);
    request.shares -= shares_;
    $.totalRedeemRequests -= uint64(assets_);

    _fetchAssets(assets_);
}
```

이 함수는 `msg.sender`의 요청에서 차감하고 에스크로에서 자산을 가져옵니다. 그러나 `ERC4626::withdraw`는 승인도 지원하므로 악의적인 행위자가 이를 악용하여 에스크로의 볼트 자본을 고갈시킬 수 있습니다.

### 개념 증명 (PoC)

전제 조건:
- **상환 에스크로의 볼트 자본:** **2000 USDC**
- **입금 에스크로의 볼트 자본:** **0**
- **자산/주식 비율:** **1:1**

공격 단계:

1. **Alice는 두 개의 주소(Address1 및 Address2)를 생성하고 각 주소에 400주를 발행합니다.**
2. **Alice는 Address1에 Address2의 주식에 대한 무제한(`type(uint256).max`) 승인을 부여합니다.**
3. **Alice는 두 주소에서 상환 요청을 시작합니다:**
   - **상환 에스크로 볼트 자본:** **1200 USDC**
   - **입금 에스크로 볼트 자본:** **800 USDC**
4. **Alice는 Address1을 사용하여 Address2를 대신하여 인출을 실행합니다:**
   - `_beforeWithdraw`는 Address1의 요청에서 자산을 차감하지만 Address2의 주식을 소각합니다.
5. **Alice는 또 다른 상환 요청을 생성하여 상환 에스크로에서 400 USDC를 추가로 추출합니다:**
   - **상환 에스크로 자본:** **800 USDC**
   - **입금 에스크로 자본: 800 USDC**
6. **Address2의 건드리지 않은 요청으로 인해 400 USDC가 영구적으로 잠깁니다.**
  
Alice는 800 USDC만 예치함으로써 상환 에스크로에서 1200 USDC를 추출하여 에스크로 계약에 400 USDC를 영구적으로 잠글 수 있었습니다.

영향:

Alice는 이 공격을 지속적으로 반복하여 에스크로에서 자산을 빼돌리고 자금의 일부를 영구적으로 잠글 수 있습니다. 이는 영향을 받는 사용자가 전체 권한을 인출할 수 없는 불균형을 초래합니다.

## 권장 사항

`_beforeWithdraw`를 수정하여 `owner`를 인수로 명시적으로 전달하고 `msg.sender` 대신 요청에서 차감하여 다른 사용자를 대신한 무단 인출을 방지하는 것을 고려하십시오.

# [M-01] `setManagementFeeBps()`가 수수료 상태를 업데이트하지 못함

## 심각도

**영향:** 중간  

**가능성:** 중간  

## 설명

`HyperEvmVault` 계약의 `setManagementFeeBps()` 함수를 사용하면 소유자가 관리 수수료율을 업데이트할 수 있습니다. 그러나 수수료율이 변경될 때 수수료 상태(예: `lastFeeCollectionTimestamp`)를 업데이트하지 않습니다. 이는 특히 덜 활발한 볼트에서 잘못된 수수료 계산으로 이어질 수 있습니다.

```solidity
    function setManagementFeeBps(uint64 newManagementFeeBps_) external onlyOwner {
        require(newManagementFeeBps_ <= BPS_DENOMINATOR, Errors.FEE_TOO_HIGH());
        _getV1Storage().managementFeeBps = newManagementFeeBps_;
    }
```

수수료율이 장기간(예: 사용자를 유치하기 위해 1년) 0으로 설정된 경우 `lastFeeCollectionTimestamp`는 오래전으로 설정될 수 있습니다. 수수료율을 높인 후(예: 0.01%로) 계약은 오래된 `lastFeeCollectionTimestamp` 이후 경과된 전체 시간을 기준으로 수수료를 잘못 계산하여 과도한 수수료 징수로 이어질 수 있습니다.

## 권장 사항

`setManagementFeeBps()` 함수를 업데이트하여 수수료율이 변경될 때 수수료를 받고 `lastFeeCollectionTimestamp`를 재설정하십시오.

# [M-02] `mint()`의 잘못된 라운딩

## 심각도

**영향:** 중간

**가능성:** 중간

## 설명
`HyperEvmVault` 계약에서 `mint()` 함수는 내림을 통해 자산 수를 계산합니다.

```solidity
    function mint(uint256 shares, address receiver) public override(ERC4626Upgradeable, IERC4626) nonReentrant returns (uint256 assets) {
        V1Storage storage $ = _getV1Storage();

        if (totalSupply() == 0) {
            // If the vault is empty then we need to initialize last fee collection timestamp
            assets = shares;
            $.lastFeeCollectionTimestamp = uint64(block.timestamp);
        } else {
            uint256 tvl_ = _totalEscrowValue($);
            _takeFee($, tvl_);
            assets = shares.mulDivDown(tvl_, totalSupply());
        }
       --snip--
    }
```
이 구현에는 두 가지 중요한 문제가 있습니다.

1. ERC4626 표준 위반: ERC4626 표준은 `mint` 함수에 대해 올림을 예상합니다.

2. 악용 위험: 악의적인 사용자는 이 내림 메커니즘을 악용하여 최소한의 자산을 입금하면서 더 많은 주식을 발행할 수 있습니다. 이를 통해 공격자는 다른 사용자로부터 자금을 훔칠 수 있습니다.

## 권장 사항

`mint()` 함수에서 자산 금액을 계산하기 위해 올림을 수행하십시오.

# [M-03] `_takeFee()`에서 잘못된 수수료 주식 계산

## 심각도

**영향:** 중간  

**가능성:** 중간  

## 설명
`HyperEvmVault` 계약에서 `_takeFee()` 함수는 볼트에서 관리 수수료를 계산하고 징수하는 데 사용됩니다. 이 함수는 수수료 금액을 계산하고 **예금자**로부터 수수료를 공제하기 위해 `feeRecipient`에게 해당 주식을 발행합니다.

```solidity
function _takeFee(V1Storage storage $, uint256 grossAssets) private returns (uint256) {
    uint256 feeTake_ = _calculateFee($, grossAssets);

    // Only update state if there's a fee to take
    if (feeTake_ > 0) {
        $.lastFeeCollectionTimestamp = uint64(block.timestamp);
        uint256 sharesToMint = _convertToShares(feeTake_, Math.Rounding.Floor);
        _mint($.feeRecipient, sharesToMint);
    }
    return feeTake_;
}

function _convertToShares(uint256 assets, Math.Rounding /*rounding*/ ) internal view override returns (uint256) {
    return assets.mulDivDown(totalSupply(), tvl());
}

```
문제는 수수료 주식을 계산하기 위해 `_convertToShares()` 함수를 사용하는 데서 발생합니다. 현재 수수료 자산을 포함하는 총 자산(`tvl()`)을 사용합니다. 즉, 이미 징수된 수수료 자산에서도 수수료를 가져옵니다. 결과적으로 징수된 수수료는 의도한 금액보다 적습니다.

## 권장 사항
발행할 새 수수료 주식을 계산하려면 총 수수료 금액과 총 수수료 주식을 고려하십시오. 코드는 수수료 주식을 계산할 때 총 자산으로 `tvl() - feeTake_`를 사용해야 합니다.

# [M-04] 프로토콜 수수료 라운딩은 최소한의 수익을 위해 무기화될 수 있음

## 심각도

**영향:** 높음

**가능성:** 낮음

## 설명

`_previewFeeShares` 및 `_takeFee`에서 프로토콜이 받을 자격이 있는 수수료 자산을 계산한 후 `_convertToShares` 함수를 통해 자산을 수수료로 변환하는 것을 볼 수 있습니다. 지정되었지만 사용되지 않는 라운딩 방향을 관찰하십시오.

```solidity
    function _previewFeeShares(V1Storage storage $, uint256 tvl_) internal view returns (uint256) {
        uint256 expectedFee = _calculateFee($, tvl_);
@>1     return _convertToShares(expectedFee, Math.Rounding.Floor);
    }

    /**
     * @notice Takes the management fee from the vault
     * @dev There is a 0.015% annual management fee on the vault's total assets.
     * @param grossAssets The total value of the vault
     * @return The amount of fees to take in underlying assets
     */
    function _takeFee(V1Storage storage $, uint256 grossAssets) private returns (uint256) {
        uint256 feeTake_ = _calculateFee($, grossAssets);

        // Only update state if there's a fee to take
        if (feeTake_ > 0) {
            $.lastFeeCollectionTimestamp = uint64(block.timestamp);
@>2         uint256 sharesToMint = _convertToShares(feeTake_, Math.Rounding.Floor);
            _mint($.feeRecipient, sharesToMint);
        }
        return feeTake_;
    }

```

`_convertToShares`는 반환된 수수료 자산에 대해 `mulDivDown` 작업을 수행하여 프로토콜이 받을 자격이 있는 주식을 반환합니다.

```solidity
    function _convertToShares(uint256 assets, Math.Rounding /*rounding*/ ) internal view override returns (uint256) {
@>3     return assets.mulDivDown(totalSupply(), tvl());
    }
```

그리고 이 수수료 자산은 아래와 같이 `_calculateFee`에서 시간 기준으로 계산됩니다.

```solidity
    function _calculateFee(V1Storage storage $, uint256 grossAssets) internal view returns (uint256 feeAmount_) {
        if (grossAssets == 0 || block.timestamp <= $.lastFeeCollectionTimestamp) {
            return 0;
        }

        // Calculate time elapsed since last fee collection
@>4     uint256 timeElapsed = block.timestamp - $.lastFeeCollectionTimestamp;

        // We subtract the pending redemption requests from the total asset value to avoid taking more fees than needed from
        //    users who do not have any pending redemption requests
        uint256 eligibleForFeeTake = grossAssets - $.totalRedeemRequests;
        // Calculate the pro-rated management fee based on time elapsed
@>5     feeAmount_ = eligibleForFeeTake * $.managementFeeBps * timeElapsed / BPS_DENOMINATOR / ONE_YEAR;

        return feeAmount_;
    }
```

따라서 `_takeFee`(예: `transfer`)를 트리거하는 함수가 `lastFeeCollectionTimestamp`로부터 특정 `timeElapsed`(예: 10초)마다 반복적으로 호출되는 전용 공격(예: 스크립트 통함)에서 `feeAmount_`의 반환 값이 0보다 크지만 여전히 주식으로 변환(즉, `totalSupply()`를 곱함)하면 `tvl()`보다 작고 `mulDivDown`으로 인해 0으로 내림될 만큼 작습니다. 수수료 주식이 발행되지 않더라도 `lastFeeCollectionTimestamp`는 계속 업데이트되어 공격 반복이 가능합니다. 이 `timeElapsed` 사이에 발생하는 합법적인 트랜잭션도 이 상황을 트리거합니다.

```solidity
    function _takeFee(V1Storage storage $, uint256 grossAssets) private returns (uint256) {
        uint256 feeTake_ = _calculateFee($, grossAssets);

        // Only update state if there's a fee to take
        if (feeTake_ > 0) {
@>6         $.lastFeeCollectionTimestamp = uint64(block.timestamp);
            uint256 sharesToMint = _convertToShares(feeTake_, Math.Rounding.Floor);
            _mint($.feeRecipient, sharesToMint);
        }
        return feeTake_;
    }

```

## 권장 사항

1. DeFi의 규칙으로 프로토콜 수수료는 항상 유리하게 올림해야 합니다.
2. 위의 권장 사항을 구현하지 않는 경우 `sharesToMint` > 0인 경우에만 `lastFeeCollectionTimestamp`를 업데이트할 수 있습니다.

# [L-01] 0 반환 주식에 대한 확인 추가

입금 함수의 내림으로 인해 사용자가 대규모 예금자나 고래에게 선점당하거나 예금 자산이 `minDepositAmount`보다 크지만 `totalSupply`와의 곱이 tvl보다 작으면 사용자가 예금 자산에 대해 0주를 받게 되어 자금 손실로 이어질 수 있습니다.

```solidity
        } else {
            uint256 tvl_ = _totalEscrowValue($);
            _takeFee($, tvl_);
@>1         shares = assets.mulDivDown(totalSupply(), tvl_);
        }
```

# [L-02] `maxwithdraw()`는 보류 중인 상환을 무시함

`HyperEvmVault` 계약은 `ERC4626Upgradeable`을 상속하고 `maxWithdraw()` 함수를 구현하여 사용자가 인출할 수 있는 최대 자산 금액을 계산합니다. 그러나 이 함수는 보류 중인 상환 요청을 고려하지 않아 인출 가능한 자산을 과대평가하게 됩니다.

```solidity
    function maxWithdraw(address owner) public view virtual returns (uint256) {
        return _convertToAssets(balanceOf(owner), Math.Rounding.Floor);
    }

    function _convertToAssets(uint256 shares, Math.Rounding /*rounding*/ ) internal view override returns (uint256) {
        return shares.mulDivDown(tvl(), totalSupply());
    }
```

이 문제를 해결하려면 `maxWithdraw()`를 계산할 때 총 주식 잔액에서 보류 중인 상환 요청의 사용자 주식을 뺍니다. 이렇게 하면 진정으로 사용 가능한 주식만 인출에 고려됩니다.

# [L-03] 미리보기 함수는 supply=0일 때 실패

`HyperEvmVault` 계약에서 `previewDeposit` 및 `previewMint` 함수는 supply=0(잘못된 숫자 반환 또는 되돌리기)을 처리하지 않습니다.

```solidity
    function previewDeposit(uint256 assets_) public view override(ERC4626Upgradeable, IERC4626) returns (uint256) {
        V1Storage storage $ = _getV1Storage();
        uint256 tvl_ = _totalEscrowValue($);
        uint256 feeShares = _previewFeeShares($, tvl_);
        uint256 adjustedSupply = totalSupply() + feeShares;
        return assets_.mulDivDown(adjustedSupply, tvl_);
    }

    function previewMint(uint256 shares_) public view override(ERC4626Upgradeable, IERC4626) returns (uint256) {
        V1Storage storage $ = _getV1Storage();
        uint256 tvl_ = _totalEscrowValue($);
        uint256 feeShares = _previewFeeShares($, tvl_);
        uint256 adjustedSupply = totalSupply() + feeShares;
        return shares_.mulDivDown(tvl_, adjustedSupply);
    }
```
ERC4626에 따르면 이 함수는 현재 블록에서 입금/발행의 효과를 시뮬레이션해야 합니다.
`deposit` 함수에서 볼 수 있듯이 `supply = 0`일 때 자산 금액을 반환해야 합니다.

```solidity
function deposit(uint256 assets, address receiver) public override(ERC4626Upgradeable, IERC4626) nonReentrant returns (uint256 shares) {
        V1Storage storage $ = _getV1Storage();
        require(assets >= $.minDepositAmount, Errors.MIN_DEPOSIT_AMOUNT());

        if (totalSupply() == 0) {
            // If the vault is empty then we need to initialize last fee collection timestamp
@>          shares = assets;
            $.lastFeeCollectionTimestamp = uint64(block.timestamp);
        } else {
            uint256 tvl_ = _totalEscrowValue($);
            _takeFee($, tvl_);
            shares = assets.mulDivDown(totalSupply(), tvl_);
        }
        --snip--
}
```

미리보기 함수에서 `supply=0`을 처리하는 조건을 추가하십시오.

# [L-04] 프로토콜이 예상보다 많은 수수료를 부과할 수 있음

HyperEvmVault에서 프로토콜은 관리하는 총 자산을 기준으로 일부 프로토콜 수수료를 부과합니다. 계산된 수수료 금액이 0이면 수수료 프로세스를 건너뜁니다. 여기서 문제는 이 경우 `lastFeeCollectionTimestamp`를 업데이트하지 않는다는 것입니다. 이로 인해 다음에 프로토콜 수수료를 계산할 때 잘못된 시간 슬롯을 사용하게 됩니다.

예를 들어:
1. Alice는 타임스탬프 X에 1000 USDC를 입금합니다.
2. Alice는 타임스탬프 X + 100에 1000 USDC 인출을 요청합니다. X와 X + 100 사이에 일부 수집 수수료가 있습니다.
3. Alice는 타임스탬프 X + 200에 1000 USDC를 입금합니다. X + 100과 X + 200 사이의 수수료를 계산할 때 수수료는 0입니다. 타임스탬프를 업데이트하지 않습니다. 현재 `lastFeeCollectionTimestamp`는 타임스탬프 X + 100입니다.
4. Bob은 타임스탬프 X + 300에 1000 USDC를 입금합니다. 수수료를 계산할 때 자산 금액 1000을 기준으로 X + 100과 X + 300 사이의 수수료를 계산합니다. 예상보다 많은 수수료를 부과하게 됩니다.

```solidity
    function _takeFee(V1Storage storage $, uint256 grossAssets) private returns (uint256) {
        uint256 feeTake_ = _calculateFee($, grossAssets);

        if (feeTake_ > 0) {
            $.lastFeeCollectionTimestamp = uint64(block.timestamp);
            uint256 sharesToMint = _convertToShares(feeTake_, Math.Rounding.Floor);
            _mint($.feeRecipient, sharesToMint);
        }
        return feeTake_;
    }
```

수수료를 계산하고 받을 때 `lastFeeCollectionTimestamp`를 적시에 업데이트하십시오.

# [L-05] `Currentblockdeposits()`가 잘못된 값을 반환할 수 있음

L1 블록이 변경되는 경우 `HyperEvmVault::currentBlockDeposits` 함수는 스토리지 `currentBlockDeposits` 값이 업데이트되기 전에 호출되면 이전 블록의 입금액을 반환할 수 있습니다.

```solidity
function currentBlockDeposits() external view override returns (uint64) {
        return _getV1Storage().currentBlockDeposits;
    }
```

현재 L1 블록이 lastL1Block과 다른 경우 0을 반환하는 것을 고려하십시오.

```diff
function currentBlockDeposits() external view override returns (uint64) {
+       return _getV1Storage().lastL1Block == l1Block() ? _getV1Storage().currentBlockDeposits : 0;
-       return _getV1Storage().currentBlockDeposits;
    }
```

