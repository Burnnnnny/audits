
# Pashov Audit Group 소개

Pashov Audit Group은 40명 이상의 프리랜서 보안 연구원으로 구성되어 있으며, 이들은 해당 분야에서 입증된 인력들입니다. 대부분은 공개 콘테스트 보상으로 10만 달러 이상을 받았거나, 다회 우승자이거나, 우리와의 감사에서 탁월한 성과를 거두었습니다. 우리는 검증되고 의욕적인 인재와만 협력합니다.

300건 이상의 보안 감사를 완료하고 수천 개의 취약점을 발견 및 패치하도록 지원한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장하는 것은 불가능하지만, 프로젝트를 위한 우리 팀의 최선을 다할 것을 보장합니다.

이전 작업은 [여기](https://github.com/pashov/audits)에서 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 문의하십시오.

# 면책 조항

스마트 계약 보안 검토는 취약점의 완전한 부재를 보장할 수 없습니다. 이는 시간, 자원 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장할 수 없으며, 검토를 통해 스마트 계약의 모든 문제를 발견한다고 보장할 수도 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개

<p><strong>Ion-Protocol/nucleus-boring-vault</strong> 저장소에 대한 시간 제한 보안 검토는 Pashov Audit Group에 의해 수행되었으며 이 기간 동안 Pashov Audit Group은 <strong>Nucleus</strong> 검토에 참여했습니다. 총 <strong>3</strong>개의 문제가 발견되었습니다.</p>

# Nucleus 정보

<p>Nucleus WHLP는 CoreWriter 프리컴파일을 통해 Hyperliquid HLP Vault 자금 관리를 자동화하는 확장 가능한 스마트 계약 시스템으로, 수동 다중 서명(multisig) 작업을 제거하고 자본 효율성을 개선합니다. 결정론적 계정 생성, 현물/무기한 선물(perp) 전송 및 금고(vault) 입출금을 조율하는 동시에 자금이 제어된 vault–account–HLP 경로를 벗어나지 않도록 보장합니다.</p>

# 보안 평가 요약

**검토 커밋 해시:**<br>• [c4fc29b6663296f07823766f5b864cb5a66431ab](https://github.com/Ion-Protocol/nucleus-boring-vault/tree/c4fc29b6663296f07823766f5b864cb5a66431ab)<br>&nbsp;&nbsp;(Ion-Protocol/nucleus-boring-vault)

**수정 검토 커밋 해시:**<br>• [2ff06ca5e138701c711a4d62ca5ec4e291c06176](https://github.com/Ion-Protocol/nucleus-boring-vault/tree/2ff06ca5e138701c711a4d62ca5ec4e291c06176)<br>&nbsp;&nbsp;(Ion-Protocol/nucleus-boring-vault)

# 범위

- `CoreWriterDecoderAndSanitizer.sol`
- `WHLPDecoderAndSanitizer.sol`
- `HLPAccount.sol`
- `HLPController.sol`

# 발견 사항

# [L-01] 디코더가 알 수 없는 `actionID`를 조용히 수락함

_해결됨_

SendRawAction 함수는 마지막 else 되돌리기(revert) 없이 끝에 도달하여 인식할 수 없는 actionID가 제공될 때 빈 바이트를 반환합니다.
그러면 머클 트리는 빈 페이로드를 해싱하여 의도하지 않은 CoreWriter 작업을 허용할 가능성이 있습니다.

actionID가 지원되는 분기와 일치하지 않을 때 명시적인 되돌리기 InvalidActionID()(또는 동등한 사용자 정의 오류)를 추가하여 알 수 없거나 미래의 작업이 실패하도록 하는 것이 좋습니다.

# [L-02] 소금(salt) 카운터가 고갈될 수 있음

_해결됨_

컨트롤러는 새 계정이 배포될 때마다 사용되는 소금(salt) 값을 1단위씩 증가시킵니다. 일정 횟수의 배포 후 증가는 address(this)를 인코딩하는 상위 바이트로 래핑되어 오버플로를 일으키고 다음 호출마다 되돌릴 수 있는 소금을 생성합니다.

이러한 이벤트가 발생할 가능성은 낮지만 오버플로가 트랜잭션을 되돌리지 않고 래핑될 수 있도록 `unchecked` 코드 블록 내에서 소금 값을 증가시키는 것이 좋습니다.

# [L-03] `CoreWriterDecoderAndSanitizer`의 잘못된 함수 이름이 금고(vault)에 영향을 미침

_해결됨_

`CoreWriterDecoderAndSanitizer` 계약은 `sendRawAction(bytes)`을 통해 이루어진 호출을 디코딩하고 삭제하여 Hyperliquid `coreWriter` 프리컴파일과의 머클 검증 상호 작용을 지원하도록 의도되었습니다. 이 메커니즘은 머클 검증 시스템을 통해 BoringVault에서 발생하는 안전하고 허가된 상호 작용에 매우 중요합니다.

그러나 디코더 함수의 이름이 올바르지 않습니다.

```solidity
function SendRawAction(bytes calldata data) external view returns (bytes memory);
```

이는 머클 호출 라우팅 시스템에서 예상하는 **함수 서명과 일치하지 않습니다**. 머클 호출 라우팅 시스템은 호출되는 대상 함수와 정확히 일치해야 하는 디코더 함수로 동적으로 디스패치합니다.

즉, 디코더가 예상 인터페이스를 구현하지 않으므로 **금고를 통해 `sendRawAction(bytes)`을 호출하려는 모든 시도는 머클 검증에 실패**합니다. 시스템은 `BaseDecoderAndSanitizer__FunctionNotImplemented`로 되돌아갑니다.

이로 인해 WHLP 출금 경로에 필수적인 `SpotSend` 및 `LimitOrder` 작업을 실행하는 데 필요한 작업을 포함하여 금고가 **CoreWriter 상호 작용**을 실행하는 기능이 중단됩니다.

> “그런 다음 금고는 USDHL에 대한 매수 주문을 하고 출금 서비스를 제공하기 위해 EVM으로 다시 연결합니다.”

디코더가 기본 `sendRawAction(...)` 호출을 인식하고 허용하지 못하므로 **금고에서 시작된 출금은 완전히 실패**하여 자산이 묶이게 됩니다.

**개념 증명(Proof of Concept):**
```solidity
function _buildCoreWriterCall(
    bytes memory encodedAction,
    bytes1 actionID
) internal returns (bytes memory) {
    bytes memory data = new bytes(4 + encodedAction.length);
    data[0] = 0x01;
    data[1] = 0x00;
    data[2] = 0x00;
    data[3] = actionID;
    for (uint256 i = 0; i < encodedAction.length; i++) {
        data[4 + i] = encodedAction[i];
    }
    return abi.encodeWithSignature("sendRawAction(bytes)", data);
}

function test_sendRawAction_decodeAndSanitize() public {
    WHLPDecoderAndSanitizer decoderAndSanitizer = new WHLPDecoderAndSanitizer(
            address(0),
            address(0)
        );

    bytes memory decodeAndSanitizeCallData = _buildCoreWriterCall(
        // We don't really care about the values here, just the structure
        abi.encode(
            uint32(0),
            true,
            uint64(0),
            uint64(0),
            true,
            uint8(0),
            uint128(0)
        ),
        0x01
    );

    vm.expectRevert(
        abi.encodeWithSelector(
            BaseDecoderAndSanitizer
                .BaseDecoderAndSanitizer__FunctionNotImplemented
                .selector,
            decodeAndSanitizeCallData
        )
    );
    // demos the call made in `ManagerWithMerkleVerification::_verifyCallData`
    bytes memory packedArgumentAddresses = abi.decode(
        address(decoderAndSanitizer).functionStaticCall(
            decodeAndSanitizeCallData
        ),
        (bytes)
    );
}
```

**권장 사항**

`coreWriter`에서 사용하는 정확한 인터페이스 이름과 일치하도록 디코더 함수의 이름을 바꿉니다.

```solidity
function sendRawAction(
    bytes calldata data
) external view override returns (bytes memory addressesFound) {
    ...
}
```

이 변경을 통해 머클 호출 유효성 검사 시스템이 디코더를 성공적으로 확인하고 금고를 통한 `coreWriter` 상호 작용을 적절하게 승인할 수 있습니다.
