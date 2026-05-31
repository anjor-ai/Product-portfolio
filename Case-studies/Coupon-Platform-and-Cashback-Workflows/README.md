# 02 · The Core Coupon Platform and Cashback Workflows

**Category:** Retention & Growth · 0-to-1

**Company:** Truemeds — Series C, India's leading online pharmacy

**Role:** Associate Product Manager

**Timeline:** 2024

---

## Outcomes

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Coupon applicability | Baseline | — | **+5.0pp** |
| User retention rate | Baseline | — | **+1.9pp** |
| Search-to-order conversion | Baseline | — | **+2.6%** |
| Customer support tickets | Baseline | — | **↓ Decreased** |

---

## The Hook

> *"Our growth teams were effectively fighting a retention battle without ammunition. The marketing portal was so structurally rigid that we couldn't configure app/web-specific constructs, target custom cohorts, or offer native cashback—forcing engineers to be constantly pulled in for simple promo setups while consumers flew completely blind on potential savings."*

---

## 01 · Context

The legacy coupon management portal at Truemeds was rudimentary and structurally rigid. The platform had no options for creating app or web-specific coupons to intentionally migrate customer cohorts, nor did it possess any provisions to build cashback reward constructs. 

The marketing team was severely constrained in the types of promotions they could configure, and the underlying coupon system was completely unprepared to scale for high-velocity transaction growth. Furthermore, the user-facing UI/UX on our mobile app and website was entirely unevolved. Essential customer information—such as a clear breakdown of which coupon applied to which products or an upfront calculation of how much savings a user would receive—was totally absent from the interface. 

I took direct ownership of this project, partnering closely with the entire marketing team to define different promotional constructs and collaborating with our design pod to fundamentally overhaul the UI/UX across our internal admin portals and consumer-facing application funnels.

---

## 02 · The Problem

The root cause was a deep operational bottleneck and an absolute lack of promotional ammunition. The platform could not isolate specific customer cohorts, meaning the growth team could not run targeted experimentation. Every time a customer-specific campaign needed to be launched, engineers had to be manually pulled away from core tracks to hardcode configurations directly on the backend database. 

Additionally, because the old portal completely lacked an internal alert system, our growth teams had no automated visibility into expiring campaigns. On high-volume operational days when the marketing team was slumped with other work, top-performing coupons would quietly expire without notice, resulting in 3 to 4 days of lost orders before the team could manually prolong the expiry.

On the consumer side, coupon applicability was severely choked for four reasons:

| Cause | What happened |
|-------|--------------|
| Lack of Transparency | Customers never knew which specific coupons applied to which line-items in their cart. |
| Zero Visual Savings Estimates | The interface did not calculate or display estimated discount values before checkout. |
| Choked Platform Logic | The rule validator could not process custom product-type rules or cashback logic. |
| Absence of Gamification | There were no interactive prompts signaling how close a user was to unlocking a better tier. |

---

## 03 · What Made It Hard

Everyone across the business agreed on the necessity of the revamp, but executing it required separating our legacy workflows from a scalable architecture without creating calculation drag. The core technical challenge lay in expanding the rule validation framework to process up to 45+ stacking combinations and exclusion scenarios without spiking checkout API latency or blocking real-time cart updates. 

We had to design a robust, granular classification system capable of applying coupon rules based on highly specific product attributes:

* **Generic vs. Branded Alternatives**
* **OTC vs. Non-OTC Items**
* **Prescription Required (Rx) vs. General Wellness**
* **SKU-Specific Whitelists** (enabling marketing to bulk-upload specific lists of items)

To safeguard our unit economics, marketing kept a strict day-on-day eye on promotional burn rates. This continuous oversight prevented our cross-functional teams from clashing over margin preservation, keeping engineering and growth aligned throughout the process.

---

## 04 · The Decision

**The key call: Rebuild the coupon platform entirely from scratch.**

Rather than trying to stitch patches onto a broken legacy backend, I made the decision to execute a complete architectural revamp. This was a pivotal point for Truemeds' internal toolset—the core platform infrastructure had to be built cleanly once to support all future scale and business use cases, with micro-optimizations scheduled later down the line.

**What we chose not to do:** We chose not to limit ourselves by legacy constraints or trim down the feature set to hit an immediate delivery target. When timelines threatened to stretch too far apart, I applied first-principles thinking to work backward from our business goals, breaking the massive system architecture into targeted milestones to satisfy immediate operational needs without compromising on the final vision.

---

## 05 · What Was Built

### 45+ Combination Rule Stacking Engine
An automated portal engine enabling the creation of complex, multi-tiered coupon structures across web and mobile platforms. Includes a clone functionality for marketing velocity and supports localized SKU whitelists, product-type restrictions (Rx, OTC, Generic, Branded), and automated internal alert systems for campaign expirations.

### Multi-Touchpoint Cashback Infrastructure
A secure cashback credit ledger that clearly communicates reward status across the user journey—specifically integrated into the cart screen, order summary screen, and order status screen. To eliminate consumer confusion and customer service (CSR) queries, it programmatically updates and handles edge cases dynamically:

* **Cart Screen:** Displays dynamic validation and highlights the value of cashback to be received.
* **Order Summary Screen:** Locks in expected cashback rewards before checkout confirmation.
* **Order Status Screen:** Provides post-purchase confirmation, clearly outlining exactly when the funds will hit the user's wallet.
* **Dynamic MOV Breach Handling:** Programmatically computes real-time adjustments if partial cancellations or returns drop the order value below the Minimum Order Value (MOV) criteria, clearly warning the user if cashback values are decreased or removed.
* **Protective Financial Rails:** Restricts wallet payouts to execute strictly post-delivery and post-return window to eliminate refund fraud. Enforces a 60-day expiry period (matching our 45–50 day repeat purchase cycle) and caps utilization at ₹100 or 10% of the total order value per transaction.
* **Protective Utilization Constraints:** Enforces a 60-day expiry period (matching our typical 45–50 day replenishment cycle) and limits redemption to a maximum of ₹100 or 10% of the total order value per transaction.

### Intelligent Visibility & Progress Bars
Overhauled consumer interface featuring intelligent coupon visibility that calculates exact savings values upfront. Integrated interactive coupon-unlock progress bars modeled after standard quick-commerce patterns, creating a gamified prompt ("Add items worth XX to unlock this coupon and save YY") to increase average order values.

### High-Margin Substitution Anchors
Designed targeted coupon structures paired with specialized consumer coachmarks to guide users toward choosing generic alternatives over high-cost branded options, driving sales velocity directly toward our highest-margin product inventory.

---

## 06 · Outcomes

Validated via a phased A/B rollout launched across test and control cohorts:

* **Coupon applicability increased by +5.0pp**, allowing users to understand and utilize rewards seamlessly.
* **User retention rate improved by +1.9pp**, driven by the 60-day native cashback loop.
* **Conversion to order placement grew securely by +2.6%** via upfront savings calculations and progress bars.
* **Support ticket volumes dropped sharply** within the first week of going live, eliminating legacy queries regarding unapplied coupons and ambiguous cashback timelines.
* **Marketing velocity surged** due to the addition of clone tools and automated backend controls, removing engineering dependencies completely.

---

## 07 · What I'd Do Differently

Our legacy systems were so broken and backward in functionality that our product pod spent an excessive amount of time planning the entire end-to-end system to achieve complete architectural perfection right out of the gate. Because no one on the product or business teams had managed a fully in-house coupon infrastructure of this scale before, we built our milestones linearly within a single pod. 

If I were to build this system again, I would change two things:

1. **Parallel Engineering Execution:** I would prioritize shipping frontend app releases, admin dashboard tools, and backend ledger updates concurrently across separate engineers rather than scheduling them linearly, which would have greatly reduced our overall time-to-market.
2. **Feedback-Backward Iteration:** I would focus on shipping lower-hanging fruits first to capture immediate feedback from internal stakeholders and users, building the system iteratively rather than delaying rollouts to complete the full scope. 

By dedicating our full engineering bandwidth to this massive build, we created a temporary backlog of requests from other teams, which caused subsequent micro-optimizations for this platform to get deprioritized later down the line.

---

---

*Anjor Tank · [anjort2000@gmail.com](mailto:anjort2000@gmail.com) · [linkedin.com/in/anjor-tank](https://linkedin.com/in/anjor-tank)*
