# 03 · Algorithmic SKU Substitution Engine

**Category:** Logistics Automation · Platform Infrastructure

**Company:** Truemeds — Series C, India's leading online pharmacy

**Role:** Associate Product Manager

**Timeline:** 2024

---

## Outcomes

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Delivery Fulfillment Rate | Baseline | — | **+3.0%** |
| User Order Cancellations | Baseline | — | **−2.6pp** |
| Operational Turnaround Time (TAT) | High Lag | Optimized | **Adherence ↑** |
| Customer Support Tickets | Baseline | — | **↓ Decreased** |

---

## The Hook

> *"Our on-ground warehouse leads were essentially running high-volume fulfillment using a static Excel sheet. When an item went out of stock, operators manually altered orders by completely deleting and re-adding medicines, which decoupled data mappings, spiked transaction prices, and left customers so blindsided by arbitrary changes that they simply cancelled the delivery."*

---

## 01 · Context

Stockouts and inventory unavailability are standard hurdles across e-commerce marketplaces, but at Truemeds, an out-of-stock (OOS) event at the fulfillment center threatened core user retention. To meet strict delivery timelines, the operational workflow relied on pack-size substitutions (e.g., swapping a missing pack of 10 for two packs of 5) or variant swaps (e.g., substituting Eno Lemon with Eno Orange). 

However, there was no software infrastructure to intelligently compute or suggest the next-best equivalent SKU. Instead, warehouse floor leads manually cross-referenced an Excel spreadsheet to find matching variants. 

Worse, because of rigid legacy limitations in our fulfillment portals, operators could not execute a true 1-to-1 "replacement." They had to physically delete the original medicine from the order invoice and add a new medicine as a separate line item. This administrative workaround broke financial transparency, caused systemic pricing mismatch complexities, and failed to communicate changes to the customer. I stepped in to architect a system-defined, automated logic layer that removed manual file dependencies and systematically eliminated operational friction.

---

## 02 · The Problem

The underlying fulfillment breakdown scaled across three distinct architectural bottlenecks:

* **De-coupled Ledger Logic:** Because the portal processed substitutions as a raw "delete-and-add" sequence rather than a linked item replacement, the pricing infrastructure perceived it as a new customer purchase. The system could not recognize that the change stemmed from our own operational stockout, meaning it failed to absorb the cost delta. As a result, the customer's total bill arbitrarily increased.
* **Blind Inventory Allocations:** The manual spreadsheet maintained by the catalog team completely ignored live, real-time warehouse inventory levels. Leads would routinely assign alternative pack sizes or variants based on the sheet, only for the picking team to discover that the substitute SKU was also entirely out of stock. 
* **Operational Lag & Missed Trucks:** The system offered zero upfront visibility regarding whether an order could be fulfilled successfully. An order would hit the warehouse floor, pass to a picker who searched the physical racks, transfer to a checker who flagged the missing SKUs, and finally reach the desk of a warehouse lead to review the manual Excel mapping. This tedious chain routinely pushed orders past tight courier truck cut-off windows, somtimes delaying shipments by a full 24 hours, spiking order delivery Turnaround Time (TAT), and driving heavy Return-To-Origin (RTO) rates.

If an order contained only a single SKU and that item suffered a warehouse stockout, the lack of an automated alternative workflow meant the platform had no choice but to trigger an immediate, hard downstream order cancellation.

---

## 03 · What Made It Hard

The primary challenge lay in bringing multiple fragmented software systems together while rectifying a heavily compromised historical database. Before building automated logic into the Catalog Management System (CMS), we discovered that our master catalog contained extensive errors and mistagged items. We had to pause and execute an exhaustive data-cleaning initiative to correctly index more than 70,000 SKUs based on exact pharmaceutical salt compositions, brand names, product variants, and dosage forms.

On the financial side, our checkout rules stated that the price locked at order placement must remain the absolute maximum a customer pays. If our own operational inefficiency forced a substitution, Truemeds had to absorb the cost delta as a discount or process an automated refund. We had to construct an intricate database logic layer that could instantly isolate and classify adjustments:

$$\text{Price Adjustment} = \begin{cases} \text{Update Customer Bill}, & \text{if Customer-Initiated Change} \\ \text{Absorb Delta (Discount/Refund)}, & \text{if Truemeds-Driven Substitution} \end{cases}$$

Engineering this multi-system calculation path to update invoices in real time—without causing latency bottlenecks in the core checkout API or cart state engines—demanded precise state-machine routing.

---

## 04 · The Decision

**The key call: Intercept the order lifecycle immediately at the warehouse portal layer using a live-inventory suggestion engine.**

Rather than pushing the inventory decision to the frontend user profile—which stakeholders argued would protect warehouse margins—I deliberately chose to shield the customer from the stockout friction. Showing threshold warnings on the order summary page would force users to manually toggle variants, driving prominent drop-offs as prices fluctuated at checkout. By shifting the automation entirely to the backend warehouse portal, Truemeds absorbed the price variance internally, preserving consumer trust and protecting core conversion funnels.

**What we chose not to do:** We consciously chose not to build consumer-facing configuration paths or dynamic front-end cart validation rules during the initial rollout. This kept our engineering footprint tightly localized to our internal logistics tools and catalog engines.

---

## 05 · What Was Built

### 1. Algorithmic CMS Variant Mapping & Suggestion Engine
We built an automated tagging interface directly into the Catalog Management System. The engine reads a master parameters table (salt composition, product line, dosage format, mother brand) and auto-generates an intelligent dropdown of verified substitute SKUs whenever a catalog manager edits or inputs a new medicine. 
* If the operator accepts the suggestion, the 1-to-1 link is locked into the master database. 
* If they map a custom alternative, that sequence is written back to the master table to continuously train future suggestions. This data structure also pipes clean variant choices directly to our front-end product display pages.

### 2. Live Inventory Order-Routing Tables
We completely overhauled the warehouse order stream. Before an order is ever assigned to a physical warehouse picker, the system passes the invoice through a live-inventory calculation script. If an item is flagged as out of stock, the order is routed to a specialized Warehouse Lead Portal table. The interface explicitly alerts the supervisor to the exact shortage and pre-populates the screen with the highest-matching, currently available variant or pack-size substitute.

### 3. Integrated 1-to-1 Replacement Architecture
We permanently retired the legacy "delete-and-add" mechanism. The portal was re-engineered with a native "Replace SKU" command. When clicked, the API disables the original product within that specific order record and binds the incoming variant directly alongside it in a strict 1-to-1 state mapping. The system mandates that the operator select a predefined reason code (e.g., *Inventory Unavailable*, *JIT Procurement Failure*) before finalizing the adjustment, creating a clean audit trail for our finance and procurement models.

### 4. Automated Tracking & Metabase Alerts
To maintain day-on-day operational velocity and ensure no orders lingered in the lead review queue, I built custom Metabase tracking dashboards coupled with automated email alert intervals. Every few hours, the system parses the database and pings warehouse operation leads with the exact count of pending actions, preventing fulfillment bottlenecks from breaking courier cut-off times.

---

## 06 · Outcomes

Due to intense on-ground operational complexities, cross-system edge cases, and continuous bug-fixing phases, the platform required two full months of careful scaling to reach complete system stability. Once finalized, the rollout secured major operational wins:

* **Fulfillment Rate Increased by +3.0%:** Orders that previously suffered automatic down-funnel cancellations due to single-item stockouts were saved via automated matching.
* **User Cancellations Decreased by 2.6 percentage points:** Eliminating post-purchase pricing surprises and unexpected invoice changes dramatically preserved customer trust.
* **Warehouse Picking Velocity Surged:** On-ground leads possessed the direct system ammunition to resolve order anomalies instantly, bypassing the need for manual spreadsheet lookups and sending pre-verified, accurate invoices straight to pickers.
* **Procurement Demand Planning Unlocked:** Integrating live inventory tracking directly into our substitution flows provided our central procurement and supply chain teams with clean data to optimize just-in-time forecasting models.
* **Support Ticket Volume Plunged:** Customer queries regarding missing items, surprise charges, or unannounced order cancellations dropped significantly.

---

## 07 · What I'd Do Differently

### 1. Avoid a Big-Bang GTM Release
In my excitement to deploy such a high-impact infrastructure upgrade, I launched the feature live across all national fulfillment centers simultaneously. This was a critical mistake. The sudden influx of multi-warehouse scale exposed undetected database gaps, breaking active order paths and forcing us into an emergency war-room scenario. I had to quickly roll back the release and redeploy it strictly to our lowest-volume warehouse. Testing the feature incrementally in a low-risk environment allowed us to iron out P0 issues safely before scaling gradually to larger fulfillment hubs.

### 2. Launch an MVP Shadow Operation
Instead of locking the operational teams out of the solution for two full months while engineering built out the perfect software interface, I should have had a data analyst run a simple database query against our live inventory and sync it to a dynamic Google Sheet. Letting the warehouse leads run a manual pilot from that sheet for a week or two would have given us immediate on-ground product feedback and accelerated operational efficiency much sooner.

### 3. Test Upsell Experimentation at Checkout
I regret not running a parallel A/B test that exposed pack-size alternatives directly to users on the order summary page. If architected with the right incentives, we could have offered customers the option to swap their selection for a larger pack size at a minor discount right before checkout. This could have avoided fulfillment delays entirely while actively driving up Average Order Value (AOV).

---

---

*Anjor Tank · [anjort2000@gmail.com](mailto:anjort2000@gmail.com) · [linkedin.com/in/anjor-tank](https://linkedin.com/in/anjor-tank)*
