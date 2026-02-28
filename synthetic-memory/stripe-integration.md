# Stripe Checkout + Webhooks

## Setup

```bash
npm install stripe @stripe/stripe-js
```

Environment variables:
```
STRIPE_SECRET_KEY=sk_live_...       # server only
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_live_...  # client OK
STRIPE_WEBHOOK_SECRET=whsec_...     # for webhook verification
```

## Checkout Session (Server)

```typescript
import Stripe from 'stripe';
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

// Create a checkout session
const session = await stripe.checkout.sessions.create({
  mode: 'subscription',  // or 'payment' for one-time
  line_items: [{
    price: 'price_xxxxx',  // Price ID from Stripe Dashboard
    quantity: 1,
  }],
  success_url: 'https://example.com/success?session_id={CHECKOUT_SESSION_ID}',
  cancel_url: 'https://example.com/cancel',
  customer_email: user.email,
  metadata: { userId: user.id },
});

// Redirect client to session.url
```

## Client Redirect

```typescript
import { loadStripe } from '@stripe/stripe-js';
const stripe = await loadStripe(process.env.NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY!);
await stripe.redirectToCheckout({ sessionId });
```

Or simply redirect to `session.url` server-side.

## Webhooks

Stripe sends events to your endpoint. **Always verify the signature.**

```typescript
// app/api/webhooks/stripe/route.ts (Next.js App Router)
import { NextRequest, NextResponse } from 'next/server';
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

export async function POST(req: NextRequest) {
  const body = await req.text();
  const sig = req.headers.get('stripe-signature')!;

  let event: Stripe.Event;
  try {
    event = stripe.webhooks.constructEvent(body, sig, process.env.STRIPE_WEBHOOK_SECRET!);
  } catch {
    return NextResponse.json({ error: 'Invalid signature' }, { status: 400 });
  }

  switch (event.type) {
    case 'checkout.session.completed':
      const session = event.data.object as Stripe.Checkout.Session;
      // Provision access, update database
      break;
    case 'customer.subscription.updated':
      // Handle plan changes
      break;
    case 'customer.subscription.deleted':
      // Revoke access
      break;
    case 'invoice.payment_failed':
      // Notify user, retry logic
      break;
  }

  return NextResponse.json({ received: true });
}
```

**Important**: Use `req.text()` not `req.json()` — signature verification needs the raw body.

## Key Webhook Events

| Event | When |
|-------|------|
| `checkout.session.completed` | Customer completes payment |
| `customer.subscription.created` | New subscription starts |
| `customer.subscription.updated` | Plan change, renewal |
| `customer.subscription.deleted` | Cancellation takes effect |
| `invoice.payment_succeeded` | Recurring payment succeeds |
| `invoice.payment_failed` | Payment fails |

## Testing

```bash
stripe listen --forward-to localhost:3000/api/webhooks/stripe
stripe trigger checkout.session.completed
```

Use `stripe-cli` for local webhook testing. Test mode keys (`sk_test_...`) for development.

## Best Practices

- Store Stripe `customer_id` in your database, linked to user accounts.
- Use `metadata` on sessions/subscriptions to store your internal IDs.
- Implement idempotency — webhooks may be delivered multiple times.
- Handle events asynchronously; return 200 quickly to avoid retries.
- Use the Customer Portal for self-service subscription management.
