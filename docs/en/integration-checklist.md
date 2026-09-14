[Documentation home](../../README.md) · [简体中文](../zh-CN/integration-checklist.md)

# Integration checklist

This is an engineering planning checklist, not an API contract.

## Before implementation

- Obtain the current official endpoint, authentication and request/response documentation.
- Confirm supported asset/network combinations, amount precision, fees and expiration behavior.
- Confirm the authoritative payment states and which state permits fulfillment.
- Confirm notification authenticity checks, retry behavior and status-query options.
- Establish whether a sandbox/test workflow is available; do not assume one.

## Order integrity

- Keep your business order identifier separate from the provider payment identifier.
- Verify order association, amount and asset/network context before fulfillment.
- Make fulfillment idempotent: duplicate notifications must not duplicate credits or delivery.
- Handle delayed and out-of-order updates without regressing a completed order.
- Use server-side verified data; do not trust a browser redirect alone.

## Reliability & privacy

- Keep credentials server-side and outside version control.
- Store only required customer and payment information; redact sensitive logs.
- Persist payment and fulfillment state before returning a successful notification response.
- Use the officially documented response/retry behavior.
- Reconcile pending or exceptional payments through supported product channels.

## Launch review

Review expired, short, excess, duplicated, delayed and unsupported-payment scenarios. Confirm a support escalation path and validate reconciliation between the provider record and your business ledger.

Never publish private keys, access tokens, customer records or sensitive payment details in this repository.
