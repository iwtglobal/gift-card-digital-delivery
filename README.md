# Gift Card Digital Delivery

An educational guide to **digital gift card delivery**, **e-gift card email and SMS delivery**, and gift card fulfillment pipelines. Written for retailers, telecom operators, and payment teams evaluating platforms in the [EVD System](https://evdsystem.com/) and [MoboGage](https://evdsystem.com/about-mobogage/) family.

---

## What Is Digital Gift Card Delivery?

**Digital gift card delivery** is the process of getting a purchased e-gift card, meaning its code, PIN, or redemption link, to the right recipient securely and on time. Delivery can happen by email, SMS, WhatsApp or other messaging, in-app wallet, printed receipt at a POS, or through a partner's API.

Delivery is where gift card programs win or lose trust. The customer has already paid; if the code arrives late, lands in spam, goes to a mistyped address, or is intercepted, the brand absorbs the cost and the complaint. A good fulfillment pipeline treats delivery as a tracked state machine with retries, resend controls, and fraud checks, not as a fire-and-forget email.

### Why delivery design matters

- **Customer experience** — instant, branded delivery is the main reason people choose e-gift cards  
- **Fraud exposure** — delivered codes are bearer value; interception equals loss  
- **Support cost** — "I never received my code" is the most common gift card ticket  
- **Scheduling** — gifts are often sent for a specific date and time  

---

## Architecture Overview: Order → Delivered Code

```
Order paid (web / app / POS / corporate bulk)
        │
        ▼
Fraud screen (velocity, payment risk, recipient patterns)
        │
        ▼
Code issuance (activate from inventory or generate)
        │
        ▼
Delivery scheduler (send now or at a chosen date/time)
        │
        ▼
Channel adapters (email / SMS / messaging / in-app / API)
        │
        ▼
Delivery status tracking ──► Resend with controls ──► Redemption
```

### Core layers

| Layer | Responsibility |
|-------|----------------|
| **Order intake** | Captures buyer, recipient, message, design, and send time |
| **Fraud screening** | Holds risky orders before value is released |
| **Issuance** | Activates a code from stock or generates one, bound to the order |
| **Scheduler** | Queues future-dated gifts and handles time zones |
| **Channel adapters** | Provider-specific email, SMS, and messaging integrations |
| **Status tracking** | Sent, delivered, bounced, opened, redeemed |
| **Resend and reissue** | Controlled resend; reissue invalidates the old code first |

See [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) for a component view.

---

## How E-Gift Card Delivery Works, Step by Step

### 1. Capture recipient details carefully

Validate email format and phone number structure at checkout and show the recipient back to the buyer before payment. Many delivery failures are typos.

### 2. Screen before releasing value

Hold orders that match fraud patterns, such as many high-value cards to new addresses or stolen-card signals, until they are reviewed. Once a code is out, it is very hard to recover.

### 3. Issue and bind the code

Activate the code only after payment and screening succeed, and bind it to the order ID so support can trace it.

### 4. Deliver through the chosen channel

Prefer redemption links or masked codes where possible, so the full code is not sitting in plain text in an inbox. Track provider callbacks for delivery and bounce events.

### 5. Handle resends safely

Resend to the original recipient freely. Changing the recipient should require buyer verification, and reissuing a code should cancel the original first.

---

## Delivery Channel Comparison

| Channel | Strengths | Watch-outs |
|---------|-----------|------------|
| **Email** | Rich branding, personal message, cheap | Spam filtering, typos, forwarding |
| **SMS** | Fast, high open rate, works on basic phones | Length limits, sender ID rules, cost |
| **Messaging apps** | Familiar, supports images | Template approval, opt-in rules |
| **In-app wallet** | Secure, no exposed code | Recipient must have the app |
| **Partner API** | Scales to marketplaces and corporates | Partner security and reconciliation |
| **POS receipt** | Works offline and in store | Paper loss, shoulder surfing |

Platforms such as EVD System / MoboGage handle electronic voucher and gift card distribution across POS, apps, and APIs, so the same inventory and audit trail cover in-store, online, and partner deliveries.

---

## Implementation Considerations

- **Idempotent delivery jobs** — retries must not issue a second code  
- **Masking and links** — reduce exposure of full codes in messages and logs  
- **Time-zone aware scheduling** — deliver at the recipient's intended local time  
- **Sender reputation** — authenticated email domains and registered SMS sender IDs  
- **Recipient-change controls** — treat as a high-risk action  
- **Reconciliation** — issued, delivered, and redeemed counts must tie back to orders and payments  

---

## FAQ

**What is digital gift card delivery?**  
Sending a purchased e-gift card's code or redemption link to the recipient through email, SMS, messaging, an app, or a partner API, with tracking and resend controls.

**How fast should an e-gift card arrive?**  
Customers expect seconds to a few minutes for instant orders. Orders held for fraud review should tell the buyer that a short review is underway.

**What if the recipient never receives the code?**  
Check delivery status first, resend to the verified recipient, and only reissue (cancelling the original code) after confirming the original is unredeemed.

**Is it safe to send gift card codes by email?**  
It is common, but safer designs send a redemption link or a partially masked code and keep the full code behind a click or app login.

**How does this relate to MoboGage / EVD System?**  
EVD System is MoboGage's platform family for electronic voucher, gift card, and airtime distribution. See the [EVD System home](https://evdsystem.com/) and [About MoboGage](https://evdsystem.com/about-mobogage/) pages when evaluating product fit.

---

## Further Reading / Related Industry Resources

- [EVD System home](https://evdsystem.com/) — platform overview for digital value distribution  
- [About MoboGage](https://evdsystem.com/about-mobogage/) — company and product family context  

---

## License

Documentation in this repository is provided under the MIT License. See [LICENSE](./LICENSE).

*Educational material only. Not a substitute for vendor due diligence or legal advice. MoboGage and EVD System refer to offerings on evdsystem.com.*
