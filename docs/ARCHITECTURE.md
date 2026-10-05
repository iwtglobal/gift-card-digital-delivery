# Architecture — Gift Card Digital Delivery

High-level reference for evaluating **digital gift card delivery**, e-gift card email and SMS delivery, and fulfillment pipelines. Educational only; vendor designs vary.

## Logical Components

```
┌──────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│ Checkout / POS / │────►│ Fraud screening  │────►│ Code issuance   │
│ corporate bulk   │     │ + manual review  │     │ (bind to order) │
└──────────────────┘     └──────────────────┘     └────────┬────────┘
                                                           │
                                                           ▼
┌──────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│ Support console  │◄────│ Status tracker   │◄────│ Scheduler +     │
│ resend / reissue │     │ (callbacks)      │     │ channel adapters│
└──────────────────┘     └──────────────────┘     └─────────────────┘
```

## Suggested Delivery Record Fields

| Field | Meaning |
|-------|---------|
| `delivery_id` | Unique, idempotent delivery job ID |
| `order_id` | Purchase the code belongs to |
| `code_ref` | Reference to the issued code (never the raw code in logs) |
| `channel` | `email` / `sms` / `messaging` / `in_app` / `api` / `print` |
| `recipient_hash` | Hashed recipient address for matching without exposure |
| `scheduled_for` | Requested send time with time zone |
| `status` | `queued` / `sent` / `delivered` / `bounced` / `failed` / `redeemed` |
| `attempts` | Count of send attempts and resends |

## Audit Events Worth Keeping

- Fraud decision and reviewer for held orders  
- Code issuance bound to order and delivery job  
- Each send attempt with provider response  
- Recipient changes, with who approved them  
- Reissues, with the cancelled original code reference  

## Failure Modes to Design For

- Duplicate code issuance on job retry  
- Raw codes written to application logs or analytics  
- Scheduled gifts sent at the wrong local time  
- Recipient changed by an attacker using a compromised buyer account  
- Reissue without cancelling the original, creating double value  

## Related Reading

Live product reading: [evdsystem.com](https://evdsystem.com/), [About MoboGage](https://evdsystem.com/about-mobogage/).
