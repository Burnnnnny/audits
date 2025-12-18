# pashov의 Arcana 프로토콜 보안 검토

***검토 커밋 해시* - [51fc65fdd6474c9632975294c560ddee24135f2f](https://github.com/Prominence-Games/arcana-foundry-erc721a/tree/51fc65fdd6474c9632975294c560ddee24135f2f)**

**범위: `ArcanaPrime.sol`**

---

# [H-01] 민터(minter)가 지불한 ETH를 인출할 방법이 없음

## 개념 증명 (Proof of Concept)

현재 계약 배포자/소유자가 채굴자가 지불한 ETH를 인출할 수 있는 방법이 없습니다. 즉, 그 가치는 영원히 갇혀 손실될 것입니다.
이는 이 프로젝트가 실제로 확장하는 `ERC721A` 표준의 경우에도 마찬가지이지만, 개발자와의 대화에서 `ArcanaPrime` 계약은 상속/확장의 필요성 없이 그대로 사용될 것으로 예상됨을 확인했습니다.

## 영향 (Impact)

이는 수십만 달러(`MAX_SUPPLY = 10_000`이고 `MINT_PRICE = 0.08 ether`이므로)를 복구할 수 없음을 의미하며, 본질적으로 NFT 프로젝트의 자금줄(runway)을 말라버리게 하므로 "높음(High)" 심각도입니다.

## 권장 사항

계약의 가치를 인출하는 메서드를 추가하십시오. 예를 들어:

```solidity
  function withdrawBalance() external onlyOwner {
    (bool success, ) = msg.sender.call{value: address(this).balance}("");
    require(success);
  }
```

# [M-01] 주소가 ERC721 토큰을 처리할 수 없는 스마트 계약인 경우 화이트리스트 민팅 후 토큰이 갇힘

## 개념 증명 (Proof of Concept)

`mintPublic` 메서드에는 EOA만 호출할 수 있도록 하는 확인 기능이 있습니다.
```solidity
if (tx.origin != msg.sender) revert ContractsNotAllowed();
```
하지만 화이트리스트 민팅 메서드(`mintArcanaList`, `mintAspirantList`, `mintAllianceList`)에는 이것이 누락되었습니다. 즉, 화이트리스트에 있는 주소가 계약이고 해당 함수를 호출하지만 ERC721 토큰을 올바르게 처리할 수 없는 경우 토큰이 갇히게 됩니다. 이 문제는 일반적으로 `_mint` 대신 `_safeMint`를 사용하여 처리되지만 `ArcanaPrime`의 모든 `mint` 기능은 `_mint`를 사용합니다.

## 영향 (Impact)

이로 인해 사용자가 새로 발행된 토큰을 영구적으로 잃을 수 있으며, 이는 잠재적인 가치 손실입니다. 사용자가 ERC721을 제대로 처리하지 않는 스마트 계약을 사용하고 있어야 하므로 "중간(Medium)" 심각도입니다.

## 권장 사항

`mintArcanaList`, `mintAspirantList` 및 `mintAllianceList`에서 `_mint` 호출을 `_safeMint`로 변경하십시오. 이는 재진입 가능성을 추가하므로 `nonReentrant` 수정자를 추가하는 것이 가장 좋습니다.


# [L-01] `ecrecover` 사용은 OpenZeppelin의 `ECDSA` 라이브러리 사용으로 대체되어야 함

[서명 가변성(Signature malleability)](https://swcregistry.io/docs/SWC-117)은 ecrecover의 잠재적 문제 중 하나입니다. 현재 구현에 위협이 되지는 않지만 최고의 보안 표준을 사용하는 것이 항상 좋습니다. `ECDSA`는 이미 가져왔지만 실제로는 사용되지 않습니다. `ecrecover` 사용을 `ECDSA.recover` 기능으로 대체하십시오.

# 가스 최적화 보고서

## [G-01] 유용하지 않으므로 일시 중지 기능 제거

일시 중지 기능(`isNotPaused` 수정자 및 `togglePause` 메서드)은 `setCurrentPhase` 메서드를 `Phases.Closed`와 함께 사용하는 것과 동일하게 작동합니다. 후자만 사용하면 많은 스토리지 읽기를 절약할 수 있습니다.

## [G-02] `constant` 변수에서 `public` 가시성 제거

`constant` 변수는 계약에 맞게 사용자 정의되며 온체인에서 읽을 필요가 없습니다. 누구나 소스 코드에서 그 값을 볼 수 있으며 필요한 경우 다른 계약에 하드코딩할 수 있습니다. `public` 가시성을 제거하면 계약의 바이트코드에 자동으로 생성된 getter가 존재하지 않으므로 배포 비용이 최적화됩니다.

## [G-03] 내부적으로 호출되지 않는 함수에 `public` 대신 `external` 사용

`external`인 함수는 인수를 `memory`에 복사할 필요 없이 항상 `calldata`에서 직접 읽으므로 가스 절감이 발생합니다. 다음 메서드에 대해 이 작업을 수행하십시오:
- `transferFrom`
- `safeTransferFrom` (둘 다 재정의)
- `setOperatorFilteringEnabled`
- `approve`
- `setApprovalForAll`
- `repeatRegistration`
- `registerCustomBlacklist`

## [G-04] 필수가 아니므로 `nextStartTime` 스토리지 변수 및 세터 제거

`nextStartTime` 스토리지 변수와 `setNextStartTime` 함수를 모두 제거하십시오. 이 기능이 여전히 필요한 경우 오프체인으로 이동하십시오.
