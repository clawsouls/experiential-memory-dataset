# Condition D (Baseline / No Memory) — Full Responses

## IR-1: Soul Publish API Endpoint

ClawSouls is an internal project, so the exact API endpoint cannot be determined from public knowledge alone. Based on common marketplace patterns, one might expect an endpoint like `POST /api/souls/publish` or `POST /api/v1/souls`, with required parameters such as soul name, description, spec, and pricing/license information. **An accurate answer cannot be provided without referencing internal project documentation.**

## IR-2: Supabase Session Pooler "Tenant or user not found" Error

- **Cause 1**: Incorrect connection string — Session pooler uses port 6543. Verify `*.pooler.supabase.com`.
- **Cause 2**: DB password mismatch — Verify/reset in dashboard.
- **Cause 3**: Project paused — Free tier auto-pause. Resume required.
- **Cause 4**: Tenant ID mapping failure — Verify username is in `postgres.{project-ref}` format.
- **Solution**: Copy directly from Dashboard > Settings > Database > Connection string (Session mode).

## IR-3: SoulScan Scoring System

SoulScan is an internal ClawSouls tool, and the exact scoring system cannot be determined from public knowledge. **This is project-specific information.**

## IR-4: Soul Spec v0.4 vs v0.3

Soul Spec is an internal ClawSouls specification, so version differences cannot be determined. **This is project-specific information.**

## IR-5: age Encryption Algorithms

- **Key exchange**: **X25519** (Curve25519-based ECDH). Public key `age1...`, secret key `AGE-SECRET-KEY-1...`.
- **Symmetric**: **ChaCha20-Poly1305** AEAD. File key derived via HKDF-SHA-256.
- Passphrase-based mode uses **scrypt** KDF. 4KiB chunk STREAM construction.

## CT-1: Next.js App Router Dynamic Route Conflict

Solution 1: Catch-all segment
```typescript
// app/[...slug]/page.tsx
export default async function SoulPage({ params }: Props) {
  const { slug } = await params;
  if (slug.length === 1) return <SoulByName name={slug[0]} />;
  if (slug.length === 2) return <SoulByOwner owner={slug[0]} name={slug[1]} />;
  return notFound();
}
```

Solution 2: Namespace separation with prefix (recommended): `app/s/[name]` vs `app/[owner]/[name]`

Solution 3: Middleware-based rewrite — internal separation while presenting clean URLs to users.

## CT-2: Supabase RLS Policy

```sql
ALTER TABLE souls ENABLE ROW LEVEL SECURITY;
CREATE POLICY "souls_select_all" ON souls FOR SELECT USING (true);
CREATE POLICY "souls_update_own" ON souls FOR UPDATE
  USING (auth.uid() = owner_id) WITH CHECK (auth.uid() = owner_id);
CREATE POLICY "souls_delete_own" ON souls FOR DELETE USING (auth.uid() = owner_id);
CREATE POLICY "souls_insert_own" ON souls FOR INSERT WITH CHECK (auth.uid() = owner_id);
```

## CT-3: Soul Spec Multi-Format Conversion

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

## AD-1: Payment → Key Distribution Architecture

**Option A: Stripe Checkout + Instant Download** — Simple implementation, instant delivery. Downside: redistribution complex if key is leaked.
**Option B: Stripe + Gated API Access** — Fine-grained access control, automatic update delivery. Downside: requires API server operation.
**Option C: Stripe + GitHub Private Repo Invite** — Built-in version control. Downside: GitHub required, revoke automation needed.
Recommendation: Option B (token-based API).

## AD-2: Documentation Site

**Docusaurus recommended.** Built-in versioning, Algolia search, MDX (React), free GitHub Pages hosting. GitBook/MkDocs eliminated.

## AD-3: CLI Sync Architecture

AES-256-GCM (passphrase derived via Argon2id). S3/R2 + Supabase metadata. Explicit push/pull direction. `.conflict` file generation. Passphrase → Argon2id → AES key. Zero-knowledge design.

## AD-4: Monaco Editor Validation

**Both** (inline + Problems panel). VS Code pattern. `setModelMarkers()` + `jsonDefaults.setDiagnosticsOptions()`.

## AD-5: npm Package Naming

`@clawsouls` scope. Register npm org (free). Pre-register key unscoped names. Also pre-register similar name variants.

## CD-1: Committing at 2 PM on a Weekday

Just commit. Working on code during business hours is a natural activity. Checking company policy is recommended.

## CD-2: CDO Bug Issue Comment

Acknowledge → request reproduction info (browser/OS, JSON example, console errors) → share investigation direction. Concise and action-oriented.

## CD-3: Competitor Blog Post Tone

"Validation" framing. Emphasize differentiators (user-owned, tradeable). Technical comparison with facts only.
Example title: "Agent Memory Is Having Its Moment — Here's What We've Learned Building It"

## CD-4: Paper Commercial Implications

Omit or minimize. Pre-departure IP conflict, strategy exposure, Zenodo is permanent. Only hint at "potential applications" in "Future Work."

## CD-5: Reddit Comment with Real-Name Account

Go ahead. "Disclosure: I work on this project." Humble tone. No exaggeration. Real-name traceability → actually adds authenticity. Clarify as personal opinion to avoid being mistaken for official company position.
