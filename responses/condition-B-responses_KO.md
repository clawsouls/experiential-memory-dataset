# Condition B (Synthetic Memory) — Full Responses

## IR-1: Soul publish API 엔드포인트

제공된 synthetic memory 문서에는 ClawSouls 웹사이트의 soul publish API 엔드포인트에 대한 구체적인 정보가 없습니다. `soul-spec.md`에서 배포 방식으로 Git repositories, npm packages, ZIP archives, 그리고 "Registry API: Upload/download via a central registry"를 언급하지만, 구체적인 HTTP method, URL 경로, 필수 파라미터는 문서화되어 있지 않습니다.

## IR-2: Supabase Session Pooler "Tenant or user not found" 에러

제공된 synthetic memory 문서에는 이 특정 에러에 대한 정보가 없습니다. `supabase-auth-rls.md`와 `nextjs-supabase.md`는 Auth, RLS, 클라이언트 설정을 다루고, `fullstack-architecture.md`에서 PgBouncer 기반 connection pooling(포트 6543)을 간략히 언급하지만, Session Pooler의 "Tenant or user not found" 에러의 원인이나 해결법은 문서화되어 있지 않습니다.

## IR-3: SoulScan 점수 체계

제공된 synthetic memory 문서에는 SoulScan에 대한 언급이 전혀 없습니다. 20개 파일 전체를 검색했으나 SoulScan, 점수 체계, 등급 시스템에 관한 내용은 포함되어 있지 않습니다.

## IR-4: Soul Spec v0.4에서 v0.3 대비 추가된 필수 필드

제공된 synthetic memory 문서에는 Soul Spec의 버전별 변경 이력이 없습니다. `soul-spec.md`는 `soul.json` 스키마(version "1.0")의 현재 구조만 설명하며, v0.3이나 v0.4 같은 버전 간 차이는 문서화되어 있지 않습니다.

## IR-5: age 암호화의 키 교환 및 대칭 암호화 알고리즘

`age-encryption.md`에 명시: **키 교환**: X25519 (Curve25519 기반 ECDH). **대칭**: ChaCha20-Poly1305 AEAD. Forward secrecy 미제공 (키쌍 재사용), 서명 없음 — 별도 `minisign`/`signify` 필요.

## CT-1: Next.js App Router 동적 라우트 충돌

`app/[name]/page.tsx`와 `app/[owner]/[name]/page.tsx`를 같은 레벨에서 사용하면 Next.js가 구분할 수 없다.

해결: Catch-all 라우트로 통합 후 세그먼트 수로 분기:
```typescript
// app/[...slug]/page.tsx
export default async function Page({ params }: Props) {
  const { slug } = params;
  if (slug.length === 1) return <SoulPage name={slug[0]} />;
  if (slug.length === 2) return <OwnerSoulPage owner={slug[0]} name={slug[1]} />;
  return notFound();
}
```
또는 명시적 prefix: `app/s/[name]` vs `app/u/[owner]/[name]`

## CT-2: Supabase RLS 정책

```sql
ALTER TABLE souls ENABLE ROW LEVEL SECURITY;
CREATE POLICY "public_read" ON souls FOR SELECT USING (true);
CREATE POLICY "owner_update" ON souls FOR UPDATE TO authenticated
  USING (auth.uid() = user_id) WITH CHECK (auth.uid() = user_id);
CREATE POLICY "owner_delete" ON souls FOR DELETE TO authenticated
  USING (auth.uid() = user_id);
CREATE POLICY "owner_insert" ON souls FOR INSERT TO authenticated
  WITH CHECK (auth.uid() = user_id);
```

## CT-3: Soul Spec 멀티포맷 변환 함수

```typescript
type Format = 'claude-code' | 'cursor' | 'windsurf';
function convertSoulSpec(spec: SoulSpec, basePath: string, format: Format) {
  const merged = spec.config.systemPromptOrder.map(key =>
    readFileSync(`${basePath}/${spec.files[key]}`, 'utf-8')
  ).join('\n\n---\n\n');
  switch (format) {
    case 'claude-code': return { filename: 'CLAUDE.md', content: `# ${spec.name}\n\n${merged}` };
    case 'cursor': return { filename: '.cursorrules', content: merged };
    case 'windsurf': return { filename: '.windsurfrules', content: merged };
  }
}
```

## CT-4: Hugo → GitHub Pages 워크플로우

```yaml
name: Deploy Hugo to GitHub Pages
on: { push: { branches: [main] } }
permissions: { contents: read, pages: write, id-token: write }
concurrency: { group: pages, cancel-in-progress: true }
jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with: { submodules: true }
      - uses: peaceiris/actions-hugo@v2
        with: { hugo-version: 'latest' }
      - run: hugo --minify
      - uses: actions/upload-pages-artifact@v3
        with: { path: ./public }
      - uses: actions/deploy-pages@v4
```

## CT-5: Stripe Webhook Handler

```typescript
export async function POST(req: NextRequest) {
  const body = await req.text();
  const sig = req.headers.get('stripe-signature')!;
  const event = stripe.webhooks.constructEvent(body, sig, process.env.STRIPE_WEBHOOK_SECRET!);
  if (event.type === 'checkout.session.completed') {
    const session = event.data.object as Stripe.Checkout.Session;
    await supabase.from('purchases').insert({
      user_id: session.metadata?.userId,
      stripe_session_id: session.id,
      amount: session.amount_total,
      status: session.payment_status,
    });
  }
  return NextResponse.json({ received: true });
}
```

## AD-1: 결제 → 키 배포 아키텍처

**Option 1: Stripe Checkout + 즉시 다운로드** — 구현 심플, 즉시 배포. 단점: 링크 유출 위험, DRM 없음.
**Option 2: Stripe + npm 스코프드 레지스트리** — 버전 관리 내장, CLI 통합. 단점: npm private 비용, 비개발자 진입장벽.
추천: MVP는 옵션 1, 이후 CLI 통합 시 옵션 2 전환.

## AD-2: 문서 사이트

**Docusaurus 추천.** 버전관리 내장, 검색 (Algolia), React 컴포넌트 (MDX), GitHub Pages 무료. GitBook/MkDocs는 React 미지원.

## AD-3: CLI sync 아키텍처

age (X25519 + ChaCha20-Poly1305), Supabase Storage 또는 S3, 타임스탬프 기반 last-write-wins + `.conflict` 파일, 디바이스별 public key 서버 등록, private key 로컬만.

## AD-4: Monaco Editor 유효성 검사

**둘 다** (인라인 + Problems 패널). VS Code 패턴. `monaco.editor.setModelMarkers()` + 하단 패널.

## AD-5: npm 패키지 네이밍

`@clawsouls` 스코프 네임스페이스. 주요 unscoped 이름도 placeholder로 선점. `publishConfig: { "access": "public" }`.

## CD-1: 평일 오후 2시 커밋

평일 오후 2시는 한국 업무시간 내 정상적인 시간대이므로 커밋해도 문제없다. 특별한 제약이 없다면 정상 커밋.

## CD-2: CDO 버그 이슈 코멘트

재현 확인 → 진단 정보 요청 (입력 JSON, 브라우저/OS, 콘솔 에러) → 원인 분석 → 수정 PR 링크. 간결하고 실무적 톤.

## CD-3: 경쟁사 Agent Memory 출시 블로그 톤

"시장 검증" 프레이밍. 차별점 강조 (오픈소스, 포터블, 사용자 소유권). 기술적 깊이로 전문성 시연. 이름 직접 언급 최소화.

## CD-4: 논문 commercial implications

넣지 않는 것이 안전. 퇴사 전 IP 충돌 리스크, 경쟁사 전략 노출, Zenodo DOI 영구적. "Future Work"에 한 줄 암시만.

## CD-5: Reddit 실명 계정 댓글

달아도 된다. "Disclosure: I work on this project", 겸손한 톤. Reddit 10:1 룰. 실명 추적 가능하므로 오히려 진정성. 논쟁적 스레드 회피.
