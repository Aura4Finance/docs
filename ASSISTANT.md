# Orafi — AI Assistant Context

## About Orafi

Orafi is a crypto payment infrastructure for African businesses. It lets merchants accept payments in USDC on the Sui blockchain and settle in crypto or fiat. Everything is accessible through a REST API and a web dashboard.

## Key Facts

- **Base URL:** `https://api.orafi.app`
- **Auth:** API key via `x-api-key` header
- **Token:** USDC on the Sui blockchain
- **Environments:** `test` (sandbox) and `live` (production) — toggled via the Mode endpoint
- **API key prefixes:** `ora_test_` for test mode, `ora_live_` for live mode
- **Dashboard:** [dashboard.orafi.app](https://dashboard.orafi.app)

## Core Resources

| Resource | Description |
|----------|-------------|
| **Transaction** | Umbrella record for every financial event — payments, refunds, and payouts all create a transaction |
| **Payment** | A charge initiated by a customer through hosted checkout or a paylink |
| **Refund** | Returns funds from a completed payment to the customer's wallet |
| **Payout** | Withdrawal of settled funds to a crypto wallet or bank account |
| **Paylink** | Shareable payment URL for invoices, social commerce, or quick charges |
| **Customer** | A buyer who has interacted with the business (often auto-created during payment) |
| **Webhook** | Callback URL registered to receive event notifications (e.g. `payment.success`, `payment.failed`) |
| **Webhook Delivery** | A single delivery attempt of an event payload to the webhook endpoint |
| **Business Profile** | The merchant's identity — controls API keys, wallets, mode, and onboarding status |
| **Crypto Settlement Account** | A saved on-chain wallet address for payouts |
| **Fiat Settlement Account** | A saved bank account for fiat payouts (coming soon) |

## Common Flows

### Accept a Payment
1. `POST /transactions/payment/create` — returns a `checkoutUrl`
2. Redirect the customer to the checkout URL
3. Customer pays in USDC on-chain
4. `POST /transactions/payment/verify` — confirm status
5. Webhook fires `payment.success` or `payment.failed`

### Issue a Refund
1. `POST /transactions/refund` with `originalTxId`, destination `to` wallet address, and optional `reason`
2. Funds are returned on-chain; webhook fires with the result

### Withdraw Funds (Crypto Payout)
1. `GET /transactions/payout/balances` — check available balance
2. `POST /transactions/payout/crypto` — via a saved settlement account **or** a direct wallet (requires signing)

### Create a Paylink
1. `POST /paylink/create` with `title`, `description`, `amount`, and optional `images`
2. Share the returned URL; customers pay through the hosted page

### Register a Webhook
1. `POST /webhook/register` with the target `url`
2. `POST /webhook/subscribe` to add event types (e.g. `payment.success`)

## API Response Format

All responses follow this structure:

```json
{
  "success": true | false,
  "message": "Human-readable description",
  "data": { ... } | null
}
```

## Error Handling

Standard HTTP status codes are used:
- `400` — Bad request / validation error
- `401` — Missing or invalid API key
- `403` — Forbidden (wrong mode, unverified account, etc.)
- `404` — Resource not found
- `409` — Conflict (duplicate `txRef`, etc.)
- `500` — Internal server error

## Fees

- **Payment fee:** 1.5% of the transaction amount
- **Payout fee:** flat $0.01 USDC network gas
- **Refund fee:** free (no platform fee deducted)

## Support

- **Email:** support@orafi.app
- **Dashboard:** https://dashboard.orafi.app
- **Website:** https://orafi.app
