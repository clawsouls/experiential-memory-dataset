# Condition D (Baseline / No Memory) — Full Responses

## IR-1: Soul publish API 엔드포인트

ClawSouls는 내부 프로젝트이므로 공개된 일반 지식으로는 정확한 API 엔드포인트를 알 수 없습니다. 일반적인 마켓플레이스 패턴을 기반으로 추론하면 `POST /api/souls/publish` 또는 `POST /api/v1/souls`와 같은 엔드포인트가 예상되며, 필수 파라미터로는 soul name, description, spec, 가격/라이선스 정보 등이 있을 것입니다. **프로젝트 내부 문서를 참조하지 않고는 정확한 답변을 제공할 수 없습니다.**

## IR-2: Supabase Session Pooler "Tenant or user not found" 에러

- **원인 1**: 잘못된 connection string — Session pooler는 포트 6543 사용. `*.pooler.supabase.com` 확인.
- **원인 2**: DB 비밀번호 불일치 — 대시보드에서 재확인/재설정.
- **원인 3**: 프로젝트 paused — Free tier 자동 일시정지. Resume 필요.
- **원인 4**: tenant ID 매핑 실패 — username이 `postgres.{project-ref}` 형식인지 확인.
- **해결법**: 대시보드 > Settings > Database > Connection string (Session mode)에서 직접 복사.

## IR-3: SoulScan 점수 체계

SoulScan은 ClawSouls 내부 도구이며, 공개된 일반 지식으로는 정확한 점수 체계를 알 수 없습니다. **프로젝트 고유 정보입니다.**

## IR-4: Soul Spec v0.4 vs v0.3

Soul Spec은 ClawSouls 내부 스펙이므로 버전 간 차이를 알 수 없습니다. **프로젝트 고유 정보입니다.**

## IR-5: age 암호화 알고리즘

- **키 교환**: **X25519** (Curve25519 기반 ECDH). 공개키 `age1...`, 비밀키 `AGE-SECRET-KEY-1...`.
- **대칭**: **ChaCha20-Poly1305** AEAD. HKDF-SHA-256으로 파일 키 유도.
- 패스프레이즈 기반 시 **scrypt** KDF 사용. 4KiB 청크 단위 STREAM construction.

## CT-1: Next.js App Router 동적 라우트 충돌

해결 방법 1: Catch-all 세그먼트
```typescript
// app/[...slug]/page.tsx
export default async function SoulPage({ params }: Props) {
  const { slug } = await params;
  if (slug.length === 1) return <SoulByName name={slug[0]} />;
  if (slug.length === 2) return <SoulByOwner owner={slug[0]} name={slug[1]} />;
  return notFound();
}
```

해결 방법 2: 접두사로 네임스페이스 분리 (권장): `app/s/[name]` vs `app/[owner]/[name]`

해결 방법 3: Middleware 기반 rewrite — 내부적으로 분리하되 사용자에겐 깔끔한 URL.

## CT-2: Supabase RLS 정책

```sql
ALTER TABLE souls ENABLE ROW LEVEL SECURITY;
CREATE POLICY "souls_select_all" ON souls FOR SELECT USING (true);
CREATE POLICY "souls_update_own" ON souls FOR UPDATE
  USING (auth.uid() = owner_id) WITH CHECK (auth.uid() = owner_id);
CREATE POLICY "souls_delete_own" ON souls FOR DELETE USING (auth.uid() = owner_id);
CREATE POLICY "souls_insert_own" ON souls FOR INSERT WITH CHECK (auth.uid() = owner_id);
```

## CT-3: Soul Spec 멀티포맷 변환

```typescript
type Platform = 'claude-code' | 'cursor' | 'windsurf';
function convertSoulSpec(spec: SoulSpec, format: Platform): string {
  switch (format) {
    case 'claude-code':
      return { filename: 'CLAUDE.md', content: `# ${spec.name}\n\n${merged}` };
    case 'cursor':
      return { filename: '.cursorrules', content: merged };
    case 'windsurf':
      return { filename: '.windsurfrules', content: merged };
  }
}
```

## CT-4: Hugo → GitHub Pages

```yaml
name: Deploy Hugo to GitHub Pages
on: { push: { branches: [main] } }
permissions: { contents: read, pages: write, id-token: write }
concurrency: { group: pages, cancel-in-progress: true }
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with: { submodules: recursive, fetch-depth: 0 }
      - uses: peaceiris/actions-hugo@v3
        with: { hugo-version: 'latest', extended: true }
      - run: hugo --minify
      - uses: actions/upload-pages-artifact@v3
        with: { path: ./public }
  deploy:
    needs: build
    environment: { name: github-pages }
    steps:
      - uses: actions/deploy-pages@v4
```

## CT-5: Stripe Webhook Handler

```typescript
export default async function handler(req, res) {
  const buf = await buffer(req);
  const sig = req.headers['stripe-signature'];
  const event = stripe.webhooks.constructEvent(buf, sig, process.env.STRIPE_WEBHOOK_SECRET!);
  if (event.type === 'checkout.session.completed') {
    const session = event.data.object;
    await supabase.from('purchases').insert({
      stripe_session_id: session.id,
      user_id: session.metadata?.user_id,
      amount: session.amount_total,
      status: session.payment_status,
    });
  }
  res.status(200).json({ received: true });
}
export const config = { api: { bodyParser: false } };
```

## AD-1: 결제 → 키 배포 아키텍처

**Option A: Stripe Checkout + Instant Download** — 구현 단순, 즉시 배포. 단점: 키 유출 시 재배포 복잡.
**Option B: Stripe + Gated API Access** — 접근 제어 세밀, 업데이트 자동 반영. 단점: API 서버 운영 필요.
**Option C: Stripe + GitHub Private Repo Invite** — 버전 관리 내장. 단점: GitHub 필수, revoke 자동화 필요.
추천: Option B (토큰 기반 API).

## AD-2: 문서 사이트

**Docusaurus 추천.** 버전관리 내장, Algolia 검색, MDX (React), GitHub Pages 무료. GitBook/MkDocs 탈락.

## AD-3: CLI sync 아키텍처

AES-256-GCM (Argon2id로 패스프레이즈 유도). S3/R2 + Supabase metadata. push/pull 명시적 방향. `.conflict` 파일 생성. 패스프레이즈 → Argon2id → AES key. Zero-knowledge 설계.

## AD-4: Monaco Editor 유효성 검사

**둘 다** (인라인 + Problems 패널). VS Code 패턴. `setModelMarkers()` + `jsonDefaults.setDiagnosticsOptions()`.

## AD-5: npm 패키지 네이밍

`@clawsouls` 스코프. npm org 등록 (무료). 주요 unscoped 선점. 유사 이름 변형도 선점.

## CD-1: 평일 오후 2시 커밋

그냥 커밋하면 된다. 업무시간 중 코드 작업은 자연스러운 활동. 회사 정책 확인 권장.

## CD-2: CDO 버그 이슈 코멘트

확인 → 재현 정보 요청 (브라우저/OS, JSON 예시, 콘솔 에러) → 조사 방향 공유. 간결하고 액션 지향적.

## CD-3: 경쟁사 블로그 톤

"Validation" 프레이밍. 차별점 강조 (개인 소유, 거래 가능). 기술적 비교 팩트 기반만.
제목 예: "Agent Memory Is Having Its Moment — Here's What We've Learned Building It"

## CD-4: 논문 commercial implications

넣지 않거나 최소화. 퇴사 전 IP 충돌, 전략 노출, Zenodo 회수 불가. "Future Work"에 "potential applications" 암시만.

## CD-5: Reddit 실명 계정 댓글

달아도 된다. "Disclosure: I work on this project". 겸손한 톤. 과장 안 함. 실명 추적 가능 → 오히려 진정성. 회사 공식 입장 오해 방지 위해 개인 의견 명시.
