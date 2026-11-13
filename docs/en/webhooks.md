# Webhooks and payment confirmation

[Back to overview](../../README.md) · [简体中文](../zh-CN/webhooks.md)

Configure a public callback URL and PEM RSA public key in Console → Developer settings → Callback configuration. Keep the paired private key on the merchant server.

## Current envelope

Requests use application/json with these identifiers:

```text
X-JangoPay-Event: payment.completed
X-JangoPay-Encryption-Alg: AES-256-GCM
X-JangoPay-Key-Encryption: RSA-OAEP-SHA256
```

```json
{
  "event_type": "payment.completed",
  "algorithm": "AES-256-GCM",
  "key_algorithm": "RSA-OAEP-SHA256",
  "encrypted_key": "<base64>",
  "nonce": "<base64>",
  "ciphertext": "<base64>"
}
```

Decrypt encrypted_key using the RSA private key with OAEP-SHA256 to obtain a 32-byte AES key. Base64-decode nonce and ciphertext: the final 16 bytes of the decoded ciphertext are the GCM authentication tag, and AAD is empty. Decrypt the remaining ciphertext with AES-256-GCM. This is not an MD5 or HMAC signature envelope.

Successful decryption alone is not proof of payment. A `payment.completed` event can leave the invoice `partial_paid`. Reconcile invoice_id, order_id, txn_id, tx_hash, payment asset and amounts against the merchant order mapping and server-side invoice/transaction records; fulfill only when `invoice.status` is `paid` and the corresponding `transaction.status` is `settled`.

## Processing sequence

1. Validate the event type and algorithms; accept only relevant events.
2. Decrypt, authenticate and validate business fields.
3. Find the local order/invoice mapping and reconcile JangoPay records.
4. Persist `payment.completed` with a unique constraint on event type + `txn_id`, or `payout.completed` on event type + `payout_id`.
5. Use transactions or durable jobs to fulfill each order once. Mixed payments can include multiple transaction IDs; event deduplication does not replace order-level deduplication.
6. Return 2xx after successful processing. Return non-2xx on failure and retain recoverable processing state.

The current payload has no standalone event_id. The official guide documents up to five retries on failure; no retry intervals are assumed here.

## Business workflows

| Workflow | Action after confirmed completion |
| :--- | :--- |
| Digital content | Grant the purchased access |
| SaaS / API credits | Grant entitlements or credits once |
| Service renewal | Extend the service term once |
| Payout completion | Reconcile payout_id and update the payout record |

## Integration testing

With separate test configuration, cover duplicate delivery, invalid ciphertext, unknown orders, inconsistent records and temporary failure recovery. Log redacted errors and record identifiers, not secrets or complete sensitive payloads.

See the [official Webhook guide](https://doc.jangopay.org/integration-guide?section=production) for full event examples and key generation. Checked on 2026-11-13.
