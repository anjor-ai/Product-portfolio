# 01 · Price Lock and the Checkout Rebuild

**Category:** Payments · 0-to-1  
**Company:** Truemeds — Series C, India's leading online pharmacy  
**Role:** Associate Product Manager  
**Timeline:** 2024

[← Back to Portfolio](../case-studies)

---

## Outcomes

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Prepaid order-to-delivery | 77.2% | 81.4% | **+3.2pp** |
| RTO rate | Baseline | — | **−1.6pp** |
| Order cancellations | Baseline | — | **−2.3pp** |
| Daily orders delivered | — | ~26,000 | — |

---

## The Hook

> *"Customers placed an order, selected online payment, and then waited hours for a payment link that may never come — or got cancelled without warning if their courier couldn't handle cash on delivery."*

---

## 01 · Context

Truemeds had no standard ecommerce checkout experience. Payments were not collected at order placement — instead, the system held the order until the box was packed at the warehouse, roughly 3–4 hours later, and only then sent the customer a payment link.

If the customer missed that window, the order was held for a full day before shipping. If the courier partner couldn't handle COD, the order was auto-cancelled. The problem was known across the team. I was the first to take ownership of solving it.

The project involved designers, engineers, and a data analyst throughout — cross-functional from day one.

---

## 02 · The Problem

The root cause had three layers: a broken payment flow, auto-cancellations from courier constraints, and price mismatches at the warehouse that made collecting payment at checkout technically unreliable.

The price mismatch problem alone had five distinct causes:

| Cause | What happened |
|-------|--------------|
| JIT procurement | Out-of-stock items procured just-in-time at a different price than shown at placement |
| Batch change | Medicine batch changes between order and fulfilment changed the applied price |
| Pricing error | Incorrect prices in the system, corrected only when the box was packed |
| Pack size substitution | Pack of 10 replaced with 2×5 — same quantity, different unit pricing |
| SKU substitution | Eno Lemon replaced with Eno Orange — nearest equivalent, different SKU and price |

Order cancellations due to missed payments, auto-cancellations due to courier partners unable to handle COD, and price mismatches causing customer dissatisfaction were the data signals. Product intuition also played a role — this simply wasn't a standard ecommerce checkout experience.

---

## 03 · What Made It Hard

Everyone agreed on the direction. The hard part was building the operational systems to make it work without creating a cost problem for the business.

Every one of the five price mismatch causes needed its own fix — a portal-level change that tracked each substitution or correction and ensured the customer-facing price didn't shift. The core technical challenge was to enumerate every possible scenario that could cause a price change at the warehouse and build a mitigation for each one.

One of the core changes I made was to build a suggestion engine within the order processing portals that surfaced the next-best item with the smallest price delta — reducing the cost absorbed by Truemeds on substitutions.

---

## 04 · The Decision

**The key call: lock the price at order placement.**

Rather than trying to prevent price changes at the warehouse — which was operationally complex and would take months — the decision was to lock the customer's price at the moment of order placement:

- Any price **increase** at the warehouse → absorbed by Truemeds as a discount on the bill
- Any price **decrease** → refunded to the customer
- The customer's payable amount → never changes from what they agreed to at checkout

**What we chose not to do:** Try to fix every upstream pricing and inventory system first. That would have taken far longer and delayed a fix customers needed immediately.

**Validation:** Two-track — floor testing with warehouse ops teams for the order processing portal changes, and a parity benchmark against standard ecommerce checkout flows for the consumer-facing redesign.

---

## 05 · What Was Built

### Price Lock Mechanism

Freezes the customer's price at order placement. All warehouse-side changes — JIT procurement, batch change, pack substitution, SKU swap — are tracked through updated order processing portals. Discounts added to bill for price increases; refunds issued for decreases. Customer's payable amount is always what they agreed to at checkout.

### Checkout Redesign

Moved payment collection to order placement — matching standard ecommerce. Redesigned the entire payment UI/UX to:

- Surface payment options clearly at checkout
- Handle payment failures seamlessly without triggering order cancellation
- Eliminate the 3–4 hour payment window causing missed payments and auto-cancellations
- Support edge cases like payment retries and partial failures

---

## 06 · Outcomes

Validated via a phased A/B rollout released in milestones over one month:

- **Prepaid delivery +3.2pp** — from 77.2% to 81.4% across App and Web
- **RTO −1.6pp** — fewer orders returning due to missed payments and courier-ineligible CODs
- **Cancellations −2.3pp** — auto-cancellations from missed payment windows eliminated
- **Support tickets reduced** — "When will my payment activate?" and "Where is my order?" queries dropped significantly
- **Foundation unlocked** — first time Truemeds had a standard ecommerce payments stack, enabling the full engineering revamp of the payments infrastructure

---

## 07 · What I'd Do Differently

I jumped straight into building the ideal ecommerce checkout experience without first doing a proper evaluation of the payments stack at a technical level.

A better approach would have been to first audit:

- Payment partners and orchestrators available
- Discount structures to incentivise online payment adoption
- Existing UX flow issues that needed fixing independently of the larger checkout redesign

Doing that audit first would have made the build more targeted and potentially faster — and would have set up the payments engineering revamp more cleanly.

---

[← Back to Portfolio](../README.md)

---

*Anjor Tank · [anjort2000@gmail.com](mailto:anjort2000@gmail.com) · [linkedin.com/in/anjor-tank](https://linkedin.com/in/anjor-tank)*
