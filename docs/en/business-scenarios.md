[Documentation home](../../README.md) · [简体中文](../zh-CN/business-scenarios.md)

# Business scenarios

Choose a payment workflow around what the customer buys and how your business fulfills the order.

| Business | Payment purpose | Fulfillment to connect |
| :--- | :--- | :--- |
| AI & SaaS | Membership, subscription access or usage credits | Entitlement or credit activation |
| Digital content | Source code, licenses, plugins or downloads | Controlled digital delivery |
| Cross-border commerce | Store checkout and individual trade orders | Order confirmation and shipping workflow |
| Hosting & network services | Purchase, renewal or plan upgrade | Service provisioning and renewal |
| Gaming & virtual goods | Top-up packages or virtual items | Account credit or item delivery |
| Top-ups & vouchers | Gift cards, telecom top-ups or e-vouchers | Code issuance or balance update |
| Booking | Hotel, tickets or event reservations | Reservation confirmation |
| Platform operations | Customer balance top-ups and merchant reconciliation | Platform ledger and settlement records |

## Picking an integration depth

- **Payment link:** use when staff can reconcile an order and manage delivery.
- **API/Webhook workflow:** use when verified payment status should trigger an automated business action.
- **Merchant operations:** define separate transfer, balance and reconciliation procedures when the business needs them.

A payment notification does not implement shipping, digital delivery, recurring billing or a marketplace ledger for you. Those are responsibilities of the merchant system unless explicitly supported by the formal product.

**Next:** [Integration recipes](https://github.com/jangopay/jangopay-examples)
