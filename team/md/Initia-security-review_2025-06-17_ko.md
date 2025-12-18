# 정보 (About)
Pashov Audit Group은 업계 최고의 스마트 계약 보안 연구원들로 구성된 여러 팀으로 이루어져 있습니다. 총 1000개 이상의 보안 취약점을 보고한 이 그룹은 절대적으로 최고의 감사 여정을 만들기 위해 노력합니다. 100% 보안을 보장할 수는 없지만, 경험 많은 연구원들이 귀하의 블록체인 프로토콜을 위해 최선을 다할 것을 보장합니다. [여기](https://github.com/pashov/audits)에서 이전 작업을 확인하거나 Twitter [@pashovkrum](https://twitter.com/pashovkrum)으로 연락해 주세요.

# 면책 조항 (Disclaimer)
스마트 계약 보안 검토는 취약점의 완벽한 부재를 보장할 수 없습니다. 이는 시간, 리소스 및 전문 지식이 제한된 노력으로, 가능한 한 많은 취약점을 찾으려고 노력합니다. 검토 후 100% 보안을 보장하거나 검토가 스마트 계약의 모든 문제를 발견할 것이라고 보장할 수 없습니다. 후속 보안 검토, 버그 바운티 프로그램 및 온체인 모니터링을 강력히 권장합니다.

# 소개 (Introduction)
**Pashov Audit Group**에 의해 **initia-labs/widget** 저장소에 대한 시간 제한 보안 검토가 수행되었으며, 애플리케이션의 스마트 계약 구현의 보안 측면에 중점을 두었습니다.

# 위젯 및 라우터 API 소개 (About Widget and Router API)
Initia Widget은 블록체인 지갑 연결, 브리징 및 트랜잭션 서명을 웹 애플리케이션에 원활하게 통합할 수 있도록 바로 사용할 수 있는 훅(hooks)과 컴포넌트를 제공하는 React SDK입니다. 범위에는 Initia 에코시스템에 대한 추가 지원이 포함된 Skip API의 프록시인 Router API도 포함되었습니다.

# 위험 분류 (Risk Classification)

| 심각도 | 영향: 높음 | 영향: 중간 | 영향: 낮음 |
| ---------------------- | ------------ | -------------- | ----------- |
| **가능성: 높음** | 치명적 (Critical) | 높음 (High) | 중간 (Medium) |
| **가능성: 중간** | 높음 (High) | 중간 (Medium) | 낮음 (Low) |
| **가능성: 낮음** | 중간 (Medium) | 낮음 (Low) | 낮음 (Low) |

## 영향 (Impact)

- 높음 (High) - 프로토콜 자산의 상당한 물질적 손실을 초래하거나 사용자 그룹에 심각한 피해를 줍니다.

- 중간 (Medium) - 프로토콜 자산의 적당한 물질적 손실을 초래하거나 사용자 그룹에 중간 정도의 피해를 줍니다.

- 낮음 (Low) - 프로토콜 자산의 경미한 물질적 손실을 초래하거나 소규모 사용자 그룹에 피해를 줍니다.

## 가능성 (Likelihood)

- 높음 (High) - 온체인 조건을 모방한 합리적인 가정하에 공격 경로가 가능하며, 공격 비용이 훔치거나 잃을 수 있는 자금의 양에 비해 상대적으로 낮습니다.

- 중간 (Medium) - 조건부로 동기가 부여된 공격 벡터이지만 여전히 상대적으로 가능성이 높습니다.

- 낮음 (Low) - 가정이 너무 많거나 희박하거나 인센티브가 거의 없거나 전혀 없는 공격자의 상당한 지분을 필요로 합니다.

## 심각도 수준에 따른 조치 (Action required for severity levels)

- 치명적 (Critical) - 가능한 한 빨리 수정해야 함 (이미 배포된 경우)

- 높음 (High) - 수정해야 함 (아직 배포되지 않은 경우 배포 전)

- 중간 (Medium) - 수정하는 것이 좋음

- 낮음 (Low) - 수정할 수 있음

# 보안 평가 요약 (Security Assessment Summary)

**_검토 커밋 해시_ - [c7c7fc23a5d65cafbd8d748711a607abf695052a](https://github.com/initia-labs/widget/tree/c7c7fc23a5d65cafbd8d748711a607abf695052a)**

**_수정 검토 커밋 해시_ - [de9d3602dc1afcb40312cadf12c6dbf1fb1a1169](https://github.com/initia-labs/widget/tree/de9d3602dc1afcb40312cadf12c6dbf1fb1a1169)**

### 범위 (Scope)

다음 스마트 계약들이 감사 범위에 포함되었습니다:

- `src/components/`
- `src/data/`
- `src/lib/router/`
- `src/pages/`
- `src/public/`
- `src/styles/`
- `console`
- `index`
- `src/app/`
- `src/shared/`
- `src/types/`
- `src/utils/`
- `constants`
- `env`
- `main`
- `sentry.ts`

# 발견 사항 (Findings)

# [M-01] 외부 서비스에 대한 원시 요청 체인 (Raw request chain to external service)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

취약한 코드:
- router-api/src/app/transfer/transfer.controller.ts.
- router-api/src/app/transfer/transfer.service.ts.
- router-api/src/app/transfer/external/skip.service.ts.

이 코드는 필터링되지 않은 전체 원시 HTTP 요청 객체를 애플리케이션 계층을 통해 전달하고 최종적으로 Skip API (SKIP_GO_API_URL)를 대상으로 하는 아웃바운드 axios.request(...) 호출로 전달하는 두 개의 개방형 프록시 스타일 엔드포인트(/api/rest/* 및 /api/rpc/*)를 정의합니다.

다음과 같은 항목이 없습니다:

- 입력 유효성 검사.
- 허용된 경로 필터링.
- 헤더 또는 메서드 제한.
- 본문 위생 처리(Sanitation).
- 인증 또는 접근 제어.

그 결과 클라이언트가 사용자 지정 페이로드로 임의의 호출을 실행할 수 있는 외부 API에 대한 일반적인 비인증 통과 인터페이스가 생성됩니다.

> **참고**: 이 취약점은 개별 발견 사항인 "외부 Skip API에 대한 응답 유효성 검사 없음" 및 "msgsDirect 및 txTrack을 통한 검증되지 않은 원시 요청 주입"에 자세히 설명되어 있습니다. 이 섹션에서는 이러한 취약점이 상호 작용하여 각 문제가 격리되었을 때 나타나는 것보다 잠재적 영향을 증폭시키는 복합적인 보안 위험을 생성하는 방식을 강조합니다.

```typescript
// transfer.controller.ts:
@All("/api/rest/*path")
apiRest(@Req() req: Request) {
  return this.transferService.api(req);
}

```
```typescript
// transfer.service.ts:
api(req: Request) {
  return this.skipService.api(req);
}

```
```typescript
// skip.service.ts:
api(req: Request) {
  const url = new URL(req.url, SKIP_GO_API_URL);
  return this._requestSkip(url, req.body, req.method);

}

```

영향:
- SKIP_GO_API_URL이 잘못 구성된 경우 공격자는 내부 리소스를 표적으로 삼을 수 있습니다.
- Host, Authorization, Origin과 같은 헤더를 스푸핑할 수 있는 능력.
- 공격자는 백엔드를 통해 임의의 요청을 전달할 수 있습니다.

## 권장 사항

이와 관련된 독립적인 발견 사항에 대한 권장 사항이 이미 마련되어 있습니다. 추가적으로:
- 헤더를 제거하고(Strip) 살균(Sanitize)하십시오.
- GET과 같은 안전한 메서드나 명시적으로 정의된 POST 작업만 허용하십시오.
- 본문을 사용하는 경우 DTO 유효성 검사를 적용하십시오.

# [M-02] NFT 메타데이터의 검증되지 않은 외부 이미지 URL (Unvalidated External Image URLs from NFT Metadata)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

여러 컴포넌트(`CollectionDetails.tsx`, `NftDetails.tsx` 등)에서 온체인 메타데이터 또는 타사 API에서 가져온 NFT 이미지 URL이 유효성 검사이나 살균(sanitization) 없이 `<NftThumbnail />`과 같은 렌더링 컴포넌트로 직접 전달됩니다.

취약한 코드 예시:

```tsx
<NftThumbnail src={nft.image} />
```

이는 NFT 이미지가 다음 출처에서 비롯될 수 있으므로 **신뢰 경계 위반(trust boundary violation)**을 생성합니다:

- 신뢰할 수 없는 도메인 또는 IPFS 게이트웨이.
- 인라인 `data:` URI (예: `data:image/svg+xml`).
- 형식이 잘못되었거나 악의적인 페이로드 (예: SVG에 포함된 `<script>`).

이러한 이미지는 궁극적으로 `Image.tsx`와 같은 공유 컴포넌트에 도달하며, 엄격한 URL 유효성 검사를 시행하지 않을 수 있습니다.

#### 영향
공격자는 다음을 수행할 수 있습니다:
- 악성 SVG 또는 `data:` URI를 통해 **XSS** 실행.
- 공격자가 제어하는 이미지로 **브랜딩** 또는 UI 스푸핑.
- **불쾌하거나 피싱** 콘텐츠 로드.
- **캐시 중독(cache poisoning)** 또는 예기치 않은 프록시 동작 악용.

#### 코드 위치

[link](https://github.com/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/pages/wallet/tabs/nft/CollectionItem.tsx#L24)

## 권장 사항

**외부 이미지를 렌더링하기 전에 엄격한 유효성 검사를 시행하십시오:**

- `https:` URL 또는 신뢰할 수 있는 `ipfs://` 스킴만 허용하십시오.
- `data:`, `javascript:`, 알 수 없는 프로토콜을 차단하십시오.
- 일관된 유효성 검사를 위해 공유 유틸리티(예: `isSafeImageUrl`)를 사용하십시오.

**렌더링 컴포넌트 강화 (`NftThumbnail.tsx`, `Image.tsx`):**

- 도메인 허용 목록(allowlists) 또는 신뢰할 수 있는 게이트웨이 필터를 추가하십시오.
- 인라인 SVG 및 `image/svg+xml` MIME 유형을 거부하거나 살균하십시오.
- 프록시를 통해 가져오는 경우 콘텐츠 유형(content-type) 확인을 적용하십시오.

**메타데이터 소싱 강화:**

- 신뢰할 수 있는 NFT 레지스트리(예: `nft.storage`, `mintscan`)를 사용하십시오.
- 서명 유효성 검사 또는 알려진 크리에이터 허용을 고려하십시오.

# [M-03] 계정 변경 시 오래된 `SigningStargateClient` (Stale `SigningStargateClient` when changing accounts)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`clientCache`는 chainId로만 키를 지정합니다. 사용자가 다른 지갑 주소로 전환하면 이전 `OfflineSigner` 인스턴스에 연결된 캐시된 클라이언트가 재사용되어 이후의 모든 트랜잭션이 이전 계정으로 서명되는 원인이 됩니다.

a) 클라이언트 캐시 구현:
```typescript
// packages/widget-react/src/data/signer.ts
const clientCache = new Map<string, SigningStargateClient>(); // 문제: 체인만 캐시됨

export function useCreateSigningStargateClient() {
  const findChain = useFindChain();
  const registry = useRegistry();
  const aminoTypes = useAminoTypes();
  const offlineSigner = useOfflineSigner();

  return async (chainId: string) => {
    if (clientCache.has(chainId)) { // @audit chainId만 확인
      return clientCache.get(chainId)!;
    }

    const { rpcUrl } = findChain(chainId);
    const client = await SigningStargateClient.createWithSigner(
      /* ... */
    );

    clientCache.set(chainId, client); // @audit 주소 컨텍스트 없이 저장
    return client;
  };
}
```
b) `OfflineSigner` 초기화:
```typescript
// 동일 파일, signer.ts
export function useOfflineSigner() {
  const address = useInitiaAddress(); // 지갑 전환 시 변경됨
  const { signMessageAsync } = useSignMessage();

  return new OfflineSigner(address, (message) => signMessageAsync({ message }));
}
```
### 근본 원인 분석
- 캐시 키 설계 결함:
  캐시는 chainId만 키로 사용합니다(Map<string, SigningStargateClient>).
  현재 연결된 지갑 주소(useInitiaAddress())를 무시합니다.

- 상태 오염 순서:
  사용자 -> 앱: 지갑 A 연결
  앱 -> 캐시: 클라이언트 저장 (chainId:123 + 지갑 A 서명자)
  사용자 -> 앱: 지갑 B로 전환
  앱 -> 캐시: 동일한 클라이언트 검색 (chainId:123)
  앱 -> 블록체인: 지갑 A로 TX 서명 (잘못된 계정!)

- 조용한 실패 모드:
  오류가 발생하지 않음
  UI는 현재 지갑(B)을 표시하지만 실제로는 이전 지갑(A)으로 서명 중임

### 코드 위치

https://github.com/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/data/signer.ts#L181

## 권장 사항
캐시 키를 chainId와 주소 모두로 지정하거나 서명자 변경 시 캐시를 지우십시오. 예:
```typescript
const clientCache = new Map<string, SigningStargateClient>();

function cacheKey(chainId: string, address: string) {
  return `${chainId}:${address}`;
}
…
const key = cacheKey(chainId, signerAddress);
if (clientCache.has(key)) return clientCache.get(key)!;
…
clientCache.set(key, client);
```

# [M-04] 링크 살균 시 부적절한 URL 프로토콜 화이트리스트 (Inadequate URL protocol whitelist in link sanitization)

## 심각도

**영향:** 중간 (Medium)

**가능성:** 중간 (Medium)

## 설명

`ExplorerLink.tsx` 및 `ManageChainsItem.tsx`에서 사용자가 제어하는 URL(예: txPage 템플릿 또는 체인의 웹사이트 필드)은 xss()를 통해 직접 전달되어 URL 스킴을 검증하지 않고 `<a href="…">`로 렌더링됩니다. xss 라이브러리는 위험한 문자를 이스케이프하지만 기본적으로 프로토콜 화이트리스트를 시행하지는 않습니다.

링크 템플릿을 조작할 수 있는 공격자는 javascript: URI(예: `"javascript:alert(document.cookie)"`)를 제공하여 사용자가 링크를 클릭할 때 DOM 기반 XSS를 초래할 수 있습니다.

```tsx
// ExplorerLink.tsx
<a
  href={xss(txPage.replace(/\$\{txHash\}/g, txHash))}
  target="_blank"
  rel="noopener noreferrer"
>
  {text}
</a>

// ManageChainsItem.tsx
{website && (
  <a
    href={xss(website)}
    target="_blank"
    rel="noopener noreferrer"
  >
    {website}
  </a>
)}
```

### 코드 위치

[ExplorerLink.tsx#L30-L31](https://github.com/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/components/ExplorerLink.tsx#L30-L31)

## 권장 사항

- 렌더링하기 전에 엄격한 URL 프로토콜 화이트리스트를 시행하십시오. 예를 들어, 각 URL을 다음과 같은 헬퍼로 감싸십시오:

1. `new URL(url, window.location.origin)`을 통해 파싱합니다.

2. 프로토콜이 `=== 'https:' || protocol === 'http:'`인지 확인합니다.

그런 다음 xss로 전달하거나(더 나은 방법은 xss를 생략하고 React의 안전한 속성 인코딩에 의존하는 것입니다). 예를 들어:
```typescript
// utils/safeLink.ts
export function sanitizeLink(href: string): string {
  try {
    const url = new URL(href, window.location.href)
    if (!['http:', 'https:'].includes(url.protocol)) {
      throw new Error('Invalid protocol')
    }
    return url.toString()
  } catch {
    return '#'
  }
}

// In ExplorerLink.tsx & ManageChainsItem.tsx
import { sanitizeLink } from '@/utils/safeLink'

…
<a
  href={sanitizeLink(txPage.replace(/\$\{txHash\}/g, txHash))}
  target="_blank"
>
  …
</a>

```

3. 대안으로, 비 HTTP(S) 프로토콜을 거부하도록 사용자 지정 safeAttrValue 로직으로 xss를 구성하십시오.
사용자가 제어하는 URL을 렌더링하기 전에 엄격한 HTTP/HTTPS 프로토콜 화이트리스트를 시행합니다. 두 가지 가능한 접근 방식:

헬퍼 기반 살균:
```typescript
// utils/sanitizeLink.ts
export function sanitizeLink(href: string): string {
  try {
    const url = new URL(href, window.location.href);
    if (!['http:', 'https:'].includes(url.protocol)) {
      throw new Error('Invalid protocol');
    }
    return url.toString();
  } catch {
    return '#';
  }
}

// In ExplorerLink.tsx & ManageChainsItem.tsx
<a
  href={sanitizeLink(txPage.replace(/\$\{txHash\}/g, txHash))}
  target="_blank"
  rel="noopener noreferrer"
>
  {text}
</a>
```

사용자 지정 xss 구성:
```typescript
import xss from 'xss';

const urlXssOptions = {
  whiteList: {},
  stripIgnoreTag: true,
  stripIgnoreTagBody: ['script'],
  safeAttrValue: (tag, name, value) => {
    try {
      const url = new URL(value, window.location.href);
      if (!['http:', 'https:'].includes(url.protocol)) {
        return '#';
      }
      return xss.safeAttrValue(tag, name, value);
    } catch {
      return '#';
    }
  }
};

<a href={ xss(txPageUrl, urlXssOptions) } …>
  {text}
</a>
```

# [M-05] `img.initia.xyz` 프록시 엔드포인트의 필터링되지 않은 `src`를 통한 XSS 가능성 (XSS possible via unfiltered `src` in `img.initia.xyz` proxy endpoint)

## 심각도

**영향:** 높음 (High)

**가능성:** 낮음 (Low)

## 설명

이미지 프록시 서비스(`img.initia.xyz`)로 전달되는 검증되지 않고 필터링되지 않은 URL 사용으로 인해 XSS 취약점이 존재합니다. 이는 잠재적으로 신뢰할 수 없는 입력의 `src` 속성을 직접 바인딩하는 `Image.tsx` 컴포넌트 내의 이미지 렌더링 로직에 영향을 미칩니다.

`getProxyImage` 함수는 제공된 URL이 `data:image/` 접두사로 시작하지 않는 한 `https://img.initia.xyz/?url=` 앞에 추가합니다. 그러나 이를 통해 공격자는 `data:text/html;base64,...` 스킴을 통해 base64로 인코딩된 HTML/JavaScript 콘텐츠를 사용하여 악성 페이로드를 주입할 수 있습니다.

작동하는 XSS 페이로드 예:

```
https://img.initia.xyz/?url=data:text/html;
base64,PHNjcmlwdD5hbGVydCgiWFNTIGhlcmUiKTwvc2NyaXB0Pg==
```

위젯은 `getProxyImage`의 `src`를 사용하여 이미지를 직접 렌더링하므로 브라우저는 URL을 유효한 것으로 처리하고 포함된 스크립트를 실행합니다.

또한 컴포넌트에서 들어오는 `src` 값에 대한 유효성 검사 또는 살균이 없습니다. 코드 경로:

```tsx
<Img
  ...
  src={getProxyImage(src)}
  ...
/>
```

는 형식이 잘못되었거나 악의적인 입력조차도 프록시되어 렌더링됨을 보여줍니다.

코드 위치 : [link](https://github.com/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/components/Image.tsx#L31-L32)

## 권장 사항

* 이미지 태그에 바인딩하기 전에 `src` 매개변수에 대한 **화이트리스트 또는 도메인 유효성 검사**를 추가하십시오.
* `data:image/`로 시작하지 않는 모든 `data:` URI를 살균하거나 거부하십시오.
* 안전한 CDN 또는 HTML/JS 페이로드를 방지하는 더 엄격한 프록시 계층을 통해 이미지를 렌더링하는 것을 고려하십시오.
* 입력 유효성 검사를 포함하고 `data:text/html`과 같은 안전하지 않은 스킴을 거부하도록 `Image.tsx` 컴포넌트를 업데이트하십시오.

# [L-01] ERC20 승인 실패 처리 (ERC20 approval failure handling)

빠른 실패 동작(fail-fast behavior)을 사용하는 순차적 승인 처리는 부분적 실패가 발생할 때 일관되지 않은 온체인 상태를 생성합니다.

[link](https://github.com/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/pages/bridge/FooterWithErc20Approval.tsx#L27-L28)

```typescript
for (const approval of tx.evm_tx.required_erc20_approvals) {
  const response = await tokenContract.approve(spender, amount)
  await response.wait() // 실패 시 throw 발생, 루프 종료
}
```
ERC20 승인은 롤백할 수 없는 개별 온체인 트랜잭션입니다. 루프에는 트랜잭션 수준의 오류 격리가 부족하여 다음 문제가 발생합니다:

1. **상태 불일치:** 이전 승인은 성공하지만 나중 승인은 실패합니다.
2. **가스 낭비:** 실패한 승인은 브리지 작업을 완료하지 않고 가스를 소비합니다.
3. **사용자 혼란:** 부분 승인 상태는 수동 개입이 필요합니다.

**권장 사항**
순차적 승인 루프를 트랜잭션 독립성을 유지하면서 개별 실패를 우아하게 처리하는 병렬 처리 접근 방식으로 교체하십시오.

# [L-02] 계산되었지만 강제되지 않는 수수료 유효성 검사로 인한 트랜잭션 실패 허용 (Fee validation calculated but not enforced allows transaction failure)

코드는 사용자가 트랜잭션 수수료에 대한 잔액이 충분한지 계산하지만 수수료가 부족할 때 제출을 실제로 막지는 않습니다. 유효성 검사 로직은 존재하지만 주석 처리되어 있습니다.

```typescript
// BridgeFields.tsx lines 140-145
const feeErrorMessage = useMemo(() => {
  for (const fee of route?.estimated_fees ?? []) {
    const balance = balances?.[fee.origin_asset.denom]?.amount
    if (!balance || BigNumber(balance).lt(fee.amount ?? 0)) {
      return `Insufficient ${fee.origin_asset.symbol} for fees`
    }
  }
}, [balances, route])

const disabledMessage = useMemo(() => {
  // ... other validations
  if (feeErrorMessage) return // feeErrorMessage  <-- 이슈: 주석 처리됨!
},
```

**권장 사항**

코드에서 수수료 유효성 검사를 활성화하는 것을 고려하십시오.

# [L-03] Docker 이미지가 루트 사용자로 실행됨 (Docker image runs as root user)

이 프로젝트의 [Dockerfile](https://github.com/initia-labs/router-api/blob/5b0be2fcb76cb7189874888725a87b616d070ee7/Dockerfile)은 기본 루트 사용자를 사용하여 애플리케이션을 실행합니다.

컨테이너 내에서 루트로 실행하면 다음과 같은 문제가 발생할 수 있습니다:

* 컨테이너가 침해될 경우 **권한 상승(privilege escalation)** 허용.
* **모범 사례** 및 일부 Kubernetes 보안 정책(예: PodSecurityAdmission) 위반.
* 공유 볼륨 사용 시 **일관되지 않은 파일 권한** 초래.
* 커널 취약점이 존재할 경우 **추가 위험** 도입.

**권장 사항**

루트가 아닌 사용자를 생성하고 전환하여 루트 권한을 제거하도록 Dockerfile을 업데이트하십시오. 다음은 제안된 변경 사항입니다:

```dockerfile
# Add to the base image section
RUN useradd --create-home --shell /bin/bash appuser

# In the app image section
RUN chown -R appuser:appuser /app

# Drop root privileges before running the app
USER appuser
```

# [L-04] 누락된 Kubernetes 보안 제어로 인한 컨테이너 탈출 위험 (Missing Kubernetes security controls risk container escape)

`charts/templates/deployment.yaml`의 Kubernetes 배포 구성에는 필수 보안 제어가 부족하여 컨테이너 탈출, 클러스터 내 횡적 이동(lateral movement), 리소스 남용을 가능하게 하는 여러 공격 벡터가 생성됩니다. 민감한 금융 작업을 처리함에도 불구하고 배포는 최소한의 보안 강화를 사용합니다.

식별된 구체적인 문제:

1. **네트워크 정책 없음**: 포드는 클러스터의 다른 포드/서비스와 제한 없이 통신할 수 있음
2. **불충분한 리소스 제한**: 고갈 공격에 취약한 고정된 1GB 메모리 제한
3. **누락된 보안 컨텍스트 제어**: 권한 상승 방지 및 기능(capability) 삭제 부족
4. **포드 보안 표준 없음**: 컨테이너 보안 검증을 위한 승인 제어 누락
5. **암호화되지 않은 내부 트래픽**: 서비스 간 통신을 위한 서비스 메시 또는 mTLS 없음
6. **부적절한 모니터링**: 기본 상태 확인만 있고 보안 이벤트 모니터링 없음

코드 위치: [link](https://github.com/initia-labs/router-api/blob/499bd1d1737172674fd8a5fee7d3413cd9748628/charts/templates/deployment.yaml#L4-L5)

**권장 사항**

Kubernetes 보안 정책 구현을 고려하십시오.

# [L-05] `LayerZero`의 고정 가스 한도로 인한 트랜잭션 및 자금 잠금 (Fixed gas limits in `LayerZero` cause transaction and fund locks)

LayerZero 통합은 페이로드 복잡성이나 네트워크 조건에 적응하지 않는 하드코딩된 가스 한도(lzReceive의 경우 500,000, compose 작업의 경우 800,000)를 사용합니다. 이는 불충분한 가스로 인해 복잡한 작업이 실패하여 잠재적으로 브리지 계약에 자금이 잠기는 치명적 취약점을 생성합니다.

[link](https://github.com/initia-labs/router-api/blob/499bd1d1737172674fd8a5fee7d3413cd9748628/src/app/transfer/external/_utils/lz/evm.ts#L21-L22)

```typescript
export function createLzQuoteSendEvm(
  amountIn: string,
  targetChainLzId: number,
) {
  return [
    targetChainLzId,
    toDestinationAddressDataEvm(DUMMY_HEX_ADDRESS),
    BigInt(amountIn),
    BigInt(amountIn),
    Options.newOptions()
      .addExecutorLzReceiveOption(500000)
      .addExecutorComposeOption(0, 800_000)
      .toHex(),
    "0x", // composeData doesn't affect the fees
    "0x",
  ];
}
```

**권장 사항**
가스 한도를 환경 구성을 통해 이동시키는 것을 고려하십시오.

# [L-06] 콘솔 로깅을 통한 정보 노출 (Information disclosure through console logging)

애플리케이션은 코드베이스 전체의 다양한 console.log, console.warn 및 console.error 문을 통해 트랜잭션 세부 정보, 오류 메시지 및 잠재적으로 사용자 데이터를 포함한 정보를 브라우저 콘솔에 기록합니다.

**권장 사항**

조건부 로깅을 구현하고 민감한 데이터를 살균하십시오:

```typescript
const isDevelopment = process.env.NODE_ENV === 'development'

function secureLog(message: string, data?: any) {
  if (isDevelopment) {
    console.log(message, sanitizeData(data))
  }
}
```

# [L-07] 외부 링크의 탭내빙 취약점 (Tabnabbing vulnerability in external links)

`target="_blank"`를 사용하는 외부 링크에 `rel="noopener noreferrer"` 속성이 포함되어 있지 않아 `window.opener` 참조를 통해 악성 사이트가 부모 창을 조작할 수 있는 역 탭내빙(reverse tabnabbing) 공격으로 이어질 수 있습니다.

문제 예시 : https://hackerone.com/reports/1145563

코드 위치:

https://github.com/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/components/ExplorerLink.tsx#L33.

https://github.com/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/components/ExplorerLink.tsx#L33.

https://github.com/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/pages/wallet/tabs/ManageChainsItem.tsx#L43.

**권장 사항**

모든 외부 링크에 `rel="noopener noreferrer"`를 추가하십시오.

# [L-08] 누락된 보안 헤더 (Missing security headers)

애플리케이션에 일반적인 웹 취약점으로부터 보호하는 필수 보안 헤더가 없습니다.

1. Content Security Policy (CSP) - CSP 헤더가 구현되지 않음.

2. HTTP Strict Transport Security (HSTS) - HSTS 시행 없음.

3. X-Frame-Options - 클릭재킹 보호 없음.

4. X-Content-Type-Options - MIME 유형 스니핑 보호 없음.

5. X-XSS-Protection - XSS 필터링 지시문 없음.

6. Referrer-Policy - 리퍼러 정보 제어 없음.

7. Permissions-Policy - 기능 정책 제한 없음.

8. Cross-Origin-Embedder-Policy (COEP) - 교차 출처 격리 없음.

9. Cross-Origin-Opener-Policy (COOP) - 교차 출처 창 격리 없음.

10. Cross-Origin-Resource-Policy (CORP) - 교차 출처 리소스 보호 없음.

**권장 사항**

헤더 구현을 고려하십시오.

# [L-09] 지나치게 관대한 CORS 구성 (Overly permissive CORS configuration)

Router API는 모든 출처의 요청을 허용하는 지나치게 관대한 CORS(Cross-Origin Resource Sharing) 정책으로 구성되어 있습니다.

```typescript
const app = await NestFactory.create<NestExpressApplication>(AppModule, {
  cors: true,  // 모든 출처, 메서드 및 헤더 허용
  // ... other configuration
});

```

HTTP 응답 예시 :

```
HTTP/1.1 200 OK
X-Powered-By: Express
Access-Control-Allow-Origin: *
Content-Type: application/json; charset=utf-8
Content-Length: 196
ETag: W/"c4-kJDgytQyX2ET4r6NyTo7CdMjllo"
Date: Sat, 21 Jun 2025 19:44:51 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{"environment":"dev",
"l1_nft_transfer_hook_address":
"0x42cd8467b1c86e59bf319e5664a09b6b5840bb3fac64f5ce690b5041c530565a",
"network_type":"mainnet",
"version":{"commit_sha":"commit_sha","tag":"tag"}}

```
**권장 사항**
출처 허용 목록(origin allowlisting) 구현을 고려하십시오.

# [L-10] X-Powered-By: express 헤더가 기술 스택 유출 (X-Powered-By: express header leaks technology stack)

애플리케이션은 X-Powered-By: Express HTTP 헤더로 응답하여 Express.js 프레임워크로 구축되었음을 드러냅니다. 이 정보 유출은 공격자가 Express의 특정 버전 또는 관련 미들웨어의 알려진 취약점이나 잘못된 구성을 기반으로 표적형 공격을 만드는 데 도움이 될 수 있습니다.

응답 예시

```
HTTP/1.1 404 Not Found
X-Powered-By: Express
Access-Control-Allow-Origin: *
Content-Type: application/json; charset=utf-8
Content-Length: 63
ETag: W/"3f-BunLb98SCK6azHy0RO08GDnFBek"
Date: Wed, 18 Jun 2025 17:52:36 GMT
Connection: keep-alive
Keep-Alive: timeout=5
```

**권장 사항**

다음 미들웨어를 추가하여 Express에서 X-Powered-By 헤더를 억제하십시오:

```javascript
const express = require('express');
const app = express();

app.disable('x-powered-by');
```

# [L-11] ORDERED IBC 채널의 과도한 크기 패킷으로 인한 채널 폐쇄 발생 가능 (Oversized packet in ORDERED IBC channel can cause channel closure)

[ICS-20](https://github.com/cosmos/ibc/blob/main/spec/app/ics-020-fungible-token-transfer/README.md)과 [ICS-721](https://github.com/cosmos/ibc/blob/main/spec/app/ics-721-nft-transfer/README.md) 사양 모두에서 다음과 같은 진술이 있습니다:

> "체인은 패킷이 DOS 벡터가 되지 않도록 전체 패킷 데이터에 길이를 제한해야 합니다. 그러나 이는 프로토콜 정의 제한일 필요는 없습니다. 길이 제한으로 인해 수신자가 패킷을 수락할 수 없는 경우, 이는 전송자 측에서 타임아웃을 초래할 것입니다."

즉, 패킷이 수신 체인의 허용 크기를 초과하여 거부되면 전송자 측에서 타임아웃이 발생합니다. ORDERED 채널의 경우, [IBC 사양](https://docs.cosmos.network/v0.45/ibc/overview.html#receipts-and-timeouts)에 명시된 대로 이러한 타임아웃은 채널 폐쇄로 이어질 수 있습니다:

> "ORDERED 채널에서 채널 내 단일 패킷의 타임아웃은 채널을 폐쇄합니다."

따라서 ORDERED 채널로 과도한 크기의 패킷을 보내면 전체 채널이 닫혀 해당 채널을 통한 두 체인 간의 모든 향후 통신이 중단될 수 있습니다. 이러한 이유로 `/nft` API는 반환된 `AminoMsg` 메시지에 크기 제한을 적용하여 타임아웃으로 이어질 수 있는 과도한 크기의 패킷 전송을 방지해야 합니다.

# [L-12] `Erc20Service`에서 `await` 누락으로 인한 적절한 오류 처리 방해 (Missing `await` prevents proper error handling in `Erc20Service`)

`Erc20Service::_getRemoteTokenContract()` 및 `Erc20Service::_getTokenDenom()` 함수는 `await`를 사용하지 않고 비동기 호출에서 예외를 잡으려고 잘못 시도합니다. 이로 인해 `try-catch` 블록이 비동기 함수에서 발생하는 오류를 잡지 못하여 처리되지 않은 프라미스 거부(unhandled promise rejections)가 발생합니다.

```typescript
private async _getRemoteTokenContract(...) {
  try {
=>  return fetchRemoteTokenContract(wrapperContract, localContract, rest);
  } catch (error) {
    ...
  }
}

private async _getTokenDenom(tokenContract: string, rest: string) {
  try {
=>  return fetchTokenDenom(tokenContract, rest);
  } catch (error) {
    ...
  }
}
```

여기서 `fetchRemoteTokenContract()` 및 `fetchTokenDenom()` 함수는 비동기입니다. await되지 않았기 때문에 이들의 실행 중 발생하는 예외는 `try-catch` 블록에서 잡히지 않습니다.

**권장 사항**

비동기 함수를 호출할 때 `await`를 추가하여 예외가 올바르게 잡히도록 하십시오:

```typescript
private async _getRemoteTokenContract(...) {
  try {
    const res = await fetchRemoteTokenContract(wrapperContract, localContract, rest);
    return res;
  } catch (error) {
    ...
  }
}
```

# [L-13] OP 브리지 확인에서 부적절한 예외 유형 및 입력 유효성 검사 부족 (Improper exception type and lack of input validation in OP bridge check)

취약한 코드: [router-api/src/app/transfer/external/op.service.ts]().

`_isOpDenomValid` 메서드는 제공된 l2Denom이 bridgeId 및 l1Denom에서 파생된 예상 해시 형식과 일치하는지 확인하기 위해 유효성 검사를 수행합니다. bridgeId가 십진수 문자열이 아닌 경우 메서드는 `InternalServerErrorException`을 발생시키는데, 이는 잘못된 클라이언트 입력이 아닌 애플리케이션 버그를 위한 서버 측 오류입니다.

관련 코드:

```typescript
if (!/^\d+$/.test(bridgeId)) {
  throw new InternalServerErrorException(
    `Invalid bridgeId format: expected a decimal string, got "${bridgeId}"`,
  );
}

```

영향:
로직은 내부적이며 영향은 제한적이지만, 약한 입력 유효성 검사 경계와 HTTP 의미론의 잘못된 사용을 반영합니다:

- 예외 유형의 오용으로 인해 단순한 클라이언트 실수에 대해서도 의도하지 않은 500 내부 서버 오류가 발생할 수 있습니다.
- bridgeId가 사용자 제공이고 유효하지 않은 경우 처리되지 않은 오류 또는 불명확한 API 응답이 발생할 수 있습니다.

**권장 사항**
적절한 예외로 교체하십시오:

```typescript
throw new BadRequestException(`Invalid bridgeId format: expected decimal string`);
```

# [L-14] `WithNormalizedNft`에서 NFT 메타데이터에 대한 유효성 검사 부족 (Lack of validation on NFT metadata in `WithNormalizedNft`)

취약한 코드: [packages/widget-react/src/pages/wallet/tabs/nft/WithNormalizedNft.tsx]().

이 컴포넌트는 UI 전반에 걸쳐 렌더링하기 위해 NFT 메타데이터를 가져오고 정규화하는 데 사용됩니다. 다음과 같이 수행합니다:

- 온체인 nftResponse에서 제공한 URI에서 메타데이터를 가져옵니다.
- 결과 NormalizedNft 객체를 자식 컴포넌트(이름, 이미지, 설명 등과 같은 필드를 렌더링하는 경우가 많음)에 전달합니다.

주요 우려 사항: 가져온 메타데이터는 완전히 검증되지 않았고 살균되지 않았으며 신뢰할 수 없거나 살균되지 않은 외부 소스에서 비롯될 수 있습니다.
```typescript
const { data: metadata = {} } = useNftMetataQuery(nftResponse.nft.uri)
const nft = normalizeNft(nftResponse, metadata)
return children(nft)

```

영향:
이로 인해 애플리케이션은 다음 위험에 노출됩니다:

- 보안: `data:image/svg+xml,...` 페이로드는 악성 또는 기만적인 콘텐츠를 포함할 수 있습니다.
- 레이아웃 남용: 제한되지 않은 이름 또는 설명 필드는 UI 정렬을 깨뜨릴 수 있습니다.

**권장 사항**
React는 JSX에서 원시 HTML을 이스케이프하지만, 악성 이미지 소스, 지나치게 긴 문자열 또는 스푸핑된 이름을 차단하지는 않습니다.
`normalizeNft()` 강화

이미지 및 이름과 같은 필드가 명시적으로 다음과 같은지 확인하십시오:
- 트림(Trimmed)됨.
- 길게 제한됨(Length-limited).
- 소스 확인됨(Source-verified).

# [L-15] 외부 skip API에 대한 응답 유효성 검사 없음 (No response validation for external skip API)

취약한 코드: [router-api/src/app/transfer/external/skip.service.ts]().

이것이 알려진 문제임을 인지하고 있습니다. Router API는 검증 없이 Skip의 응답을 프록시하며 현재 무결성 확인이나 계약 허용 목록이 시행되지 않고 있습니다.
모든 계약 주소를 동기화하는 것은 복잡할 수 있지만, 잘못된 형식이나 변조된 응답을 조기에 감지하기 위해 런타임 스키마 유효성 검사(예: zod 사용)를 구현하는 것을 여전히 권장합니다. 이 가벼운 조치는 전체 계약 검증 없이도 위험을 크게 줄입니다.

**권장 사항**

zod를 사용한 예:

```typescript
const SkipRouteSchema = z.object({
  routes: z.array(
    z.object({
      source_chain_id: z.string(),
      dest_chain_id: z.string(),
      tx: z.any(), // Placeholder for the actual tx structure (to be defined strictly later)
    })
  )
});
```
이 코드의 역할:
유효한 Skip 경로 응답이 routes 필드가 있는 객체여야 함을 선언합니다.
경로는 각 객체가 다음을 갖는 배열이어야 합니다:

- source_chain_id: 문자열.
- dest_chain_id: 문자열.
- tx: 임의의 데이터(이는 자리 표시자이며 이상적으로는 더 엄격하게 유형을 정의해야 함).

```typescript
const parsed = SkipRouteSchema.safeParse(data);
```
Skip API에서 원시 데이터를 가져와 스키마를 사용하여 파싱을 시도합니다.

- safeParse는 throw하지 않습니다: { success: true, data } 또는 { success: false, error }를 반환합니다.

```typescript
if (!parsed.success) {
  throw new BadRequestException("Invalid response from Skip");
}
return parsed.data;
```

응답이 예상 구조와 일치하지 않으면 예외가 발생하여 안전하지 않은 처리를 방지합니다.
유효한 경우 유형이 지정되고 안전한 parsed.data가 추가 사용을 위해 반환됩니다.

메서드(체인, 자산, 경로, 메시지 등)당 하나의 스키마를 정의하십시오.
예기치 않거나 잘못된 형식의 필드를 거부하십시오.

# [L-16] 공용 엔드포인트에서 활성 상태인 안전하지 않은 TLS (1.0 & 1.1) (Insecure TLS (1.0 & 1.1) active on public endpoints)

Initia 스택의 여러 공용 서비스는 여전히 레거시 TLS 버전 1.0 및 1.1을 허용하며, 이는 여러 암호화 공격(BEAST, POODLE 등)에 취약한 것으로 알려져 있고 최신 암호 모음(cipher suites)이 부족합니다. 이는 다음 엔드포인트에 대해 전송 중인 모든 데이터의 기밀성과 무결성에 영향을 미칩니다:

- Router API URL (예: https://api.initia.xyz).
- Registry API (https://registry.initia.xyz).
- Scan Endpoint 등.

TLS 1.0/1.1을 허용하면 보안 통신이 약화되어 사용자의 지갑 상호 작용, 트랜잭션 데이터 및 교차 체인 메시지가 노출됩니다.

**권장 사항**
- 이러한 도메인을 제공하는 모든 웹 서버, 로드 밸런서 및 API 게이트웨이에서 TLS 1.0 및 1.1을 비활성화하십시오.
- 강력한 암호 모음(예: ECDHE_A* 암호, AES-GCM 또는 ChaCha20-Poly1305)과 함께 TLS 1.2+를 시행하십시오.
- 긴 max-age 및 includeSubDomains를 포함하여 HTTP Strict Transport Security (HSTS)를 구현하십시오.

# [L-17] SKIP API의 axios 클라이언트에서 누락된 HTTP 요청 타임아웃 (Missing HTTP request timeouts in axios clients on SKIP APIs)

SkipService._requestSkip()에서 Skip의 HTTP API에 대한 모든 호출은 타임아웃 없이 axios.request(...)를 사용합니다. 느리거나 응답하지 않는 Skip 백엔드는 Router API의 이벤트 루프 스레드를 차단하여 요청 핸들러가 무기한 중단되고 궁극적으로 리소스 고갈(서비스 거부)로 이어질 수 있습니다.
```typescript
// app/transfer/external/skip.service.ts
private async _requestSkip<T = unknown>(
  url: URL,
  body?: BodyInit,
  method?: string,
) {
  // …
  const { data } = await axios.request<T>({
    data: body,
    headers,
    method,
    url: url.toString(),    // ← no timeout configured
  });
  return data;
}
```
기타 영향을 받는 파일
- `shared/account/account.service.ts`
`AccountService._l2AccountExists()`
 `AccountService._createL2Account()`

- `shared/registry/registry.service.ts`
 `RegistryService.getChains()` (`axios.get("/chains.json")` 호출)

**권장 사항**
axios 클라이언트에 합리적인 요청당 타임아웃(예: 5,000ms)을 구성하십시오. 예를 들어:
```typescript
// at top of skip.service.ts
const skipHttp = axios.create({
  baseURL: SKIP_API_URL,
  timeout: 5_000,        // 5 seconds
});

// then inside _requestSkip:
const { data } = await skipHttp.request<T>({
  url: url.pathname + url.search,
  method,
  headers,
  data: body,
});
```
이렇게 하면 Skip이 5초 이내에 응답하지 않을 경우 영원히 중단되는 대신 호출 오류를 재시도하거나 클라이언트에 표면화할 수 있습니다.

# [L-18] 위젯의 모듈 전반에 걸쳐 누락된 HTTP 요청 타임아웃 (Missing HTTP request timeouts across modules in widgets)

`src/pages/bridge/data/`의 여러 훅과 헬퍼는 타임아웃을 지정하지 않고 ky 클라이언트를 시작합니다. 응답하지 않거나 악의적으로 리디렉션된 엔드포인트는 이러한 요청을 무기한(또는 매우 길 수 있는 Ky의 내부 기본값까지) 중단시켜 React Suspense 경계를 차단하고 사실상 UI에서 서비스를 거부할 수 있습니다. 이는 아래 나열된 다른 모듈에도 영향을 미칩니다.
영향을 받는 파일
`account.ts` – `waitForAccountCreation()`
`assets.ts` – `useSkipAssets()`
`balance.ts` – `useSkipBalancesQuery()`
`chains.ts` – `useSkipChains()`
`simulate.ts` – `useRouteQuery()`
`skip.ts` – 전역 `useSkip()` 클라이언트 팩토리
`tx.ts` – `useBridgeTx`, `useTrackTxQuery`, `useTxStatusQuery`의 모든 `skip.get(…) / skip.post(…)` 호출

`packages/widget-react/src/pages/bridge/op/data.ts`:
`useLayer1RestClient()` → `ky.create({ prefixUrl: restUrl })` 생성
 `useWithdrawals()` → `ky.create({ prefixUrl: executorUrl })`
 `useLatestOutput()` & `useOutput()` → 둘 다 `useLayer1RestClient()`의 `restClient` 사용
 `useOutputResponse()` →` ky.create({ prefixUrl: executorUrl })`

트랜잭션 모듈
`src/pages/tx/TxRequest.tsx` (가스 가격 가져오기)

지갑 활동
`src/pages/wallet/tabs/activity/data.ts` (`useTxs`)
`wallet/tabs/nft/queries.ts` (모든 `ky.create` 호출)

타임아웃 없이 `ky.create({ prefixUrl })`을 수행하는 기타 모든 모듈

샘플 스니펫
```typescript
// src/pages/bridge/data/skip.ts
export function useSkip() {
  const { routerApiUrl } = useConfig()
  // no timeout configured here
  return useMemo(() => ky.create({ prefixUrl: routerApiUrl }), [routerApiUrl])
}
```

```typescript
// src/pages/bridge/op/data.ts

// 1) Global Layer-1 client without timeout
function useLayer1RestClient() {
  const { restUrl } = useLayer1()
  return useMemo(() => ky.create({ prefixUrl: restUrl }), [restUrl])
}

// 2) Withdrawals query without timeout
await ky
  .create({ prefixUrl: executorUrl })
  .get(`withdrawals/${address}`, { searchParams })
  .json<WithdrawalTxListResponse>()

// 3) Output-response query without timeout
ky.create({ prefixUrl: executorUrl })
  .get(`withdrawal/${sequence}`)
  .json<OutputResponse>()
```

**권장 사항**
모든 `Ky` 클라이언트에 합리적인 요청당 타임아웃(예: 5,000ms)과 선택적 재시도 정책을 구성하십시오. 가장 간단하고 통합된 수정 사항은 전역 `useSkip()` 및 `waitForAccountCreation` 클라이언트를 업데이트하는 것입니다.

# [L-19] `quantitySuperRefine()`의 처리되지 않은 예외 (Unhandled exception in `quantitySuperRefine()`)

Zod 개선(refinement) 내에서 `BigNumber(quantity)`는 유효하지 않은 문자열에 대한 보호 없이 호출됩니다. 수량이 유효한 숫자 문자열(NaN)이 아닌 경우 `BigNumber`는 throw를 발생시켜, 잡히지 않은 예외를 버블링하고 잠재적으로 양식을 중단시킬 수 있습니다.
```typescript
// src/data/form.ts
if (BigNumber(quantity).isZero()) {
  …
}
```

**권장 사항**
`BigNumber`를 생성하기 전에 `quantity`를 숫자 문자열로 사전 검증하거나 try/catch로 감싸십시오:
```typescript
if (!/^\d+(\.\d+)?$/.test(quantity)) { // 더 나은 정규식을 도입할 수 있습니다
  ctx.addIssue({ /* invalid format message */ })
  return
}
```

# [L-20] `encodeEthSecp256k1Pubkey`에서 누락된 곡선(curve) 유효성 검사 (Missing curve validation in `encodeEthSecp256k1Pubkey`)

packages/widget-react/src/data/patches/encoding.ts에서 `encodeEthSecp256k1Pubkey` 함수는 기본 구조 유효성 검사(33바이트 길이 및 `0x02/0x03` 접두사)를 수행하지만 공개 키가 secp256k1 곡선의 유효한 지점을 나타내는지는 암호화적으로 확인하지 않습니다. 이는 현재 구현에서 즉각적인 보안 위험을 초래하지는 않지만 다음과 같은 결과를 초래할 수 있습니다:

- 다운스트림 처리 문제: 일부 라이브러리는 유효하지 않은 곡선 지점이 주어질 때 예측할 수 없게 동작할 수 있습니다.
- UI 혼란: 지갑 인터페이스가 형식이 잘못된 키에 대해 잘못된 파생 주소를 표시할 수 있습니다.

```typescript
// packages/widget-react/src/data/patches/encoding.ts
export function encodeEthSecp256k1Pubkey(pubkey: Uint8Array): EthSecp256k1Pubkey {
  if (pubkey.length !== 33 || (pubkey[0] !== 0x02 && pubkey[0] !== 0x03)) {
    throw new Error(
      "Public key must be compressed secp256k1, i.e. 33 bytes starting with 0x02 or 0x03",
    )
  }
  return {
    type: pubkeyTypeInitia.ethsecp256k1,
    value: toBase64(pubkey),
  }
}
```
위험 맥락
이는 치명적인 수정보다는 주로 방어적인 개선입니다.
현재 구현은 다음과 같은 경우 안전합니다:
키가 신뢰할 수 있는 출처(지갑/키 생성기)에서 오는 경우.
다운스트림 시스템이 키를 적절하게 검증하는 경우.

주요 이점은 다음과 같습니다:
형식이 잘못된 키의 조기 감지.
다양한 클라이언트 구현 전반에 걸쳐 보다 예측 가능한 동작.

**권장 사항**
심층 방어를 위해 `@noble/secp256k1`을 사용하여 암호화 유효성 검사를 추가하는 것을 고려하십시오:
```typescript
import { Point } from "@noble/secp256k1";
import { toBase64 } from "@cosmjs/encoding";

export function encodeEthSecp256k1Pubkey(pubkey: Uint8Array): EthSecp256k1Pubkey {
  // 1. Structural checks
  if (pubkey.length !== 33 || (pubkey[0] !== 0x02 && pubkey[0] !== 0x03)) {
    throw new Error("Invalid compressed secp256k1 key format");
  }

  // 2. Cryptographic validation
  try {
    // Throws if:
    // - X-coordinate is >= curve field size
    // - Point is not on the curve
    // - Point is at infinity
    Point.fromHex(pubkey);
  } catch (err) {
    throw new Error(`Invalid secp256k1 public key: ${err.message}`);
  }

  return {
    type: pubkeyTypeInitia.ethsecp256k1,
    value: toBase64(pubkey),
  };
}
```

# [L-21] 재구성(reorg) 보호를 위한 불충분한 확인 깊이 (Insufficient confirmation depth for reorg protection)

```194:225:packages/widget-react/src/data/tx.ts```의 `waitForTxConfirmationWithClient` 함수는 추가 확인을 요구하지 않고 트랜잭션이 단일 블록에 나타날 때까지만 기다립니다. 이로 인해 블록체인 재구성이 "확인된" 트랜잭션을 되돌릴 수 있는 문제가 발생합니다.

코드 위치 : [tx.ts#L210-L211](https://github.dev/initia-labs/widget/blob/c7c7fc23a5d65cafbd8d748711a607abf695052a/packages/widget-react/src/data/tx.ts#L210-L211)

현재 구현은 어떤 블록에서든 트랜잭션을 찾으면 즉시 반환합니다:

```typescript
const tx = await client.getTx(txHash)
if (tx) {
  if (tx.code !== 0) throw new Error(tx.rawLog)
  return tx  // ❌ 확인 깊이 검사 없음
}
```

- **단일 블록 최종성(Finality)**: 모든 블록 포함을 최종 확인으로 처리합니다.
- **재구성 감지 없음**: 포크 감지 메커니즘이 누락되었습니다.

**권장 사항**

확인 깊이 요구 사항 구현을 고려하십시오.

# [L-22] `CollectionDetails.tsx`의 이스케이프되지 않은 NFT 메타데이터 (Unescaped NFT metadata in `CollectionDetails.tsx `)

취약한 코드: [widget-react/src/pages/wallet/tabs/nft/CollectionDetails.tsx](widget-react/src/pages/wallet/tabs/nft/CollectionDetails.tsx)

이 컴포넌트는 collection.name 및 nft.name을 JSX로 직접 렌더링합니다. 이러한 필드에 악성 또는 잘못된 형식의 콘텐츠(예: SVG 페이로드 또는 동형 문자(homoglyph))가 포함된 경우 다음이 발생할 수 있습니다:

- UI의 시각적 스푸핑.
- 렌더링 결함.

문자열이 나중에 React의 이스케이핑을 우회하는 컨텍스트에서 사용되는 경우 DOM 주입
```typescript
<Page title={collection.name}>         // Unescaped collection name
<div className={styles.name}>{nft.name}</div>  // NFT name rendered directly
```

영향:
이로 인해 애플리케이션은 다음 위험에 노출됩니다:

- SVG 또는 데이터 URI가 렌더링될 경우 사이트 간 스크립팅(XSS).
- 깨진 UI/UX: 예기치 않은 문자 또는 잘못된 형식의 문자열로 인해 렌더링이 손상될 수 있습니다.

## 권장 사항

React는 기본적으로 특정 HTML(예: <, >)을 이스케이프하지만 다음은 수행하지 않습니다:

- 콘텐츠 출처 또는 구조 확인.
- 유니코드 스푸핑 또는 복잡한 렌더링 문제 방지.
- 개발자가 나중에 dangerouslySetInnerHTML로 전환하는 경우 안전 보장.

따라서 신뢰할 수 없는 메타데이터를 직접 렌더링하는 것은 안전하지 않으며, 특히 NFT 또는 교차 체인 메타데이터로 작업하는 앱의 경우 더욱 그렇습니다.

메타데이터 이스케이프 또는 살균
일반 텍스트의 경우:
```typescript
const safeName = escapeHtml(nft.name);  // Custom util or use `he` lib
```
서식 있는 텍스트의 경우(필요한 경우):
```typescript
import DOMPurify from "dompurify";
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(nft.description) }} />
```

# [L-23] 검증되지 않은 지갑 이미지 소스 (Unvalidated Wallet Image Source)

취약한 코드: [widget-react/src/pages/bridge/BridgeAccount.tsx]()

이 이미지 소스(connected.image)는 useCosmosWallets()를 통해 사용자가 선택한 지갑 메타데이터에서 동적으로 로드됩니다. 공유된 `<Image />` 컴포넌트를 사용하여 렌더링하기 전에 이에 대한 유효성 검사 또는 살균이 없습니다.
```typescript
{type === "src" && connected && <Image src={connected.image} width={18} height={18} />}
```

영향:
- SVG 기반 XSS.
- 지갑 아이콘의 시각적 스푸핑(예: MetaMask 모방).
- 필터링되지 않은 CDN을 통해 렌더링되는 경우 프록시 악용.

개념 증명 (PoC):
공격자는 다음과 같은 지갑 메타데이터(예: 커뮤니티 지갑 구성)를 제공합니다:
```json
"image": "data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg'><script>alert('XSS')</script></svg>"
```

**권장 사항**

렌더링하기 전에 이미지를 검증하십시오:

- 신뢰할 수 있는 도메인의 https://만 허용하십시오.
- data:, javascript: 또는 알 수 없는 스킴을 차단하십시오.
- 지갑 메타데이터의 서버 측 유효성 검사를 고려하십시오.
