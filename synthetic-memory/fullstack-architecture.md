# Full-Stack Architecture Decisions

## Stack Selection Criteria

When choosing a full-stack architecture, consider:
- **Team size**: Solo/small team → convention-over-configuration frameworks. Large team → explicit, typed contracts.
- **Time to market**: BaaS (Supabase, Firebase) accelerates MVP. Self-hosted for long-term control.
- **Scale profile**: Read-heavy → CDN + ISR. Write-heavy → serverless may hit cold start/cost limits.
- **Compliance**: Data residency, encryption requirements, audit logging.

## Recommended Stack: Next.js + Supabase + Vercel

### Why This Combination
- **Next.js**: React with SSR/SSG/ISR, API routes, middleware. Industry standard.
- **Supabase**: PostgreSQL with Auth, RLS, Realtime, Storage. Open-source Firebase alternative.
- **Vercel**: Zero-config deployment, edge functions, preview deploys.

### Architecture Layers

```
┌─────────────────────────────────┐
│        Client (Browser)         │
│   Next.js React (App Router)   │
├─────────────────────────────────┤
│       Vercel Edge Network       │
│   CDN, Edge Middleware, ISR     │
├─────────────────────────────────┤
│     Next.js Server Components   │
│   API Routes / Server Actions   │
├─────────────────────────────────┤
│         Supabase Cloud          │
│  PostgreSQL + Auth + Storage    │
│        + Realtime + RLS         │
└─────────────────────────────────┘
```

## API Design Patterns

### Server Actions (Preferred for Mutations)
```typescript
'use server'
async function createPost(formData: FormData) {
  const supabase = createClient();
  await supabase.from('posts').insert({ title: formData.get('title') });
  revalidatePath('/posts');
}
```

### API Routes (For External Consumers / Webhooks)
Use `app/api/` routes for:
- Webhook receivers (Stripe, GitHub).
- Public API endpoints.
- Long-running operations.

### Direct Client Queries (For Real-time)
Use Supabase client directly for:
- Real-time subscriptions.
- Simple reads where RLS provides sufficient authorization.

## Database Design

- **UUID primary keys**: `gen_random_uuid()`. Better for distributed systems than auto-increment.
- **Timestamps**: Always include `created_at` and `updated_at` with defaults.
- **Soft deletes**: Add `deleted_at` column instead of hard deletes for auditability.
- **RLS from day one**: Enable on every table immediately. Harder to retrofit.

## Authentication Architecture

1. Supabase Auth handles identity (email, OAuth, magic link).
2. JWT stored in HTTP-only cookies (via `@supabase/ssr`).
3. Middleware refreshes session on every request.
4. Server Components call `getUser()` for auth checks.
5. RLS policies enforce data access at the database level.

## Monorepo vs Polyrepo

For projects with web + CLI + docs:
- **Monorepo** (Turborepo/Nx): Shared types, atomic commits, unified CI.
- **Polyrepo**: Independent deploy cycles, clearer ownership boundaries.

Recommendation: Monorepo for small teams (<5 devs), polyrepo when teams diverge.

## Environment Strategy

| Environment | Branch | Database | Purpose |
|-------------|--------|----------|---------|
| Local | any | Local Supabase (Docker) | Development |
| Preview | PR branches | Staging DB | PR review |
| Staging | `develop` | Staging DB | Integration testing |
| Production | `main` | Production DB | Live users |

## Cost Optimization

- Use Supabase free tier for MVP (500MB DB, 1GB storage, 50K auth users).
- Vercel Hobby tier for personal projects; Pro ($20/mo) for teams.
- Cache aggressively: ISR for content pages, SWR for client data.
- Use Postgres connection pooling (Supabase provides via PgBouncer on port 6543).
