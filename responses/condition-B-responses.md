# Condition B (Synthetic Memory) — Full Responses

## IR-1: Soul Publish API Endpoint

The provided synthetic memory documents contain no specific information about the ClawSouls website's soul publish API endpoint. `soul-spec.md` mentions deployment methods including Git repositories, npm packages, ZIP archives, and "Registry API: Upload/download via a central registry," but the specific HTTP method, URL path, and required parameters are not documented.

## IR-2: Supabase Session Pooler "Tenant or user not found" Error

The provided synthetic memory documents contain no information about this specific error. `supabase-auth-rls.md` and `nextjs-supabase.md` cover Auth, RLS, and client setup, and `fullstack-architecture.md` briefly mentions PgBouncer-based connection pooling (port 6543), but the causes and solutions for the Session Pooler's "Tenant or user not found" error are not documented.

## IR-3: SoulScan Scoring System

The provided synthetic memory documents contain no mention of SoulScan whatsoever. A search across all 20 files found no content related to SoulScan, scoring systems, or tier systems.

## IR-4: Required Fields Added in Soul Spec v0.4 vs v0.3

The provided synthetic memory documents contain no version-specific change history for Soul Spec. `soul-spec.md` only describes the current `soul.json` schema (version "1.0") structure, and differences between versions like v0.3 or v0.4 are not documented.

## IR-5: age Encryption Key Exchange and Symmetric Encryption Algorithms

As specified in `age-encryption.md`: **Key exchange**: X25519 (Curve25519-based ECDH). **Symmetric**: ChaCha20-Poly1305 AEAD. No forward secrecy (key pair reuse), no signing — separate `minisign`/`signify` required.

## CT-1: Next.js App Router Dynamic Route Conflict

Using `app/[name]/page.tsx` and `app/[owner]/[name]/page.tsx` at the same level means Next.js cannot distinguish between them.

Solution: Unify with a catch-all route and branch by segment count:
```typescript
// app/[...slug]/page.tsx
export default async function Page({ params }: Props) {
  const { slug } = params;
  if (slug.length === 1) return <SoulPage name={slug[0]} />;
  if (slug.length === 2) return <OwnerSoulPage owner={slug[0]} name={slug[1]} />;
  return notFound();
}
```
Or use explicit prefixes: `app/s/[name]` vs `app/u/[owner]/[name]`

## CT-2: Supabase RLS Policy

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

## CT-3: Soul Spec Multi-Format Conversion Function

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

## CT-4: Hugo → GitHub Pages Workflow

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

## AD-1: Payment → Key Distribution Architecture

**Option 1: Stripe Checkout + Instant Download** — Simple implementation, instant delivery. Downside: link leak risk, no DRM.
**Option 2: Stripe + npm Scoped Registry** — Built-in version management, CLI integration. Downside: npm private cost, barrier for non-developers.
Recommendation: Option 1 for MVP, transition to Option 2 when CLI integration is ready.

## AD-2: Documentation Site

**Docusaurus recommended.** Built-in versioning, search (Algolia), React components (MDX), free GitHub Pages hosting. GitBook/MkDocs don't support React.

## AD-3: CLI Sync Architecture

age (X25519 + ChaCha20-Poly1305), Supabase Storage or S3, timestamp-based last-write-wins + `.conflict` files, per-device public key registered on server, private key local only.

## AD-4: Monaco Editor Validation

**Both** (inline + Problems panel). VS Code pattern. `monaco.editor.setModelMarkers()` + bottom panel.

## AD-5: npm Package Naming

`@clawsouls` scoped namespace. Also pre-register key unscoped names as placeholders. `publishConfig: { "access": "public" }`.

## CD-1: Committing at 2 PM on a Weekday

2 PM on a weekday is within normal Korean business hours, so committing is fine. Proceed with normal commit if there are no special restrictions.

## CD-2: CDO Bug Issue Comment

Confirm reproduction → request diagnostic info (input JSON, browser/OS, console errors) → analyze cause → link fix PR. Concise and practical tone.

## CD-3: Competitor Agent Memory Launch Blog Tone

"Market validation" framing. Emphasize differentiators (open source, portable, user ownership). Demonstrate expertise through technical depth. Minimize direct name mentions.

## CD-4: Paper Commercial Implications

Safer to omit. Pre-departure IP conflict risk, competitor strategy exposure, Zenodo DOI is permanent. Only hint in one line under "Future Work."

## CD-5: Reddit Comment with Real-Name Account

Go ahead. "Disclosure: I work on this project," humble tone. Reddit 10:1 rule. Real-name traceability actually adds authenticity. Avoid contentious threads.
