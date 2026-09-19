# Track 4: Techno-Functional Lead
## Chapter 8: Top 50+ Techno-Functional Lead Interview Questions & Ideal Answers

Here is a curated collection of deep, scenario-based interview questions asked in Techno-Functional Lead, ERP Solution Architect, and Principal Consultant interviews.

---

### Section 1: Fit-Gap, Scoping & Implementation Methodology (Q1 – Q10)

#### Q1: What is the difference between a Business Requirement Document (BRD), a Functional Specification Document (FSD), and a Technical Specification Document (TSD)?
- **Answer**: 
  - **BRD**: Authored by business stakeholders; defines *what* the business problem is and the desired outcome in commercial language (e.g. "Sales reps should not offer unapproved discounts").
  - **FSD**: Authored by the Techno-Functional Lead; translates the BRD into functional ERP workflows, screens, permission rules, and field behavior (e.g. "If discount $> 15\%$, set state to `waiting_approval` and require Manager sign-off").
  - **TSD**: Authored by the Technical Lead; details database schema changes, ORM overrides, Python methods, API payloads, and performance indexes to implement the FSD.

#### Q2: How do you handle a situation where a client insists on customizing standard ERP functionality to mimic their legacy 20-year-old system?
- **Answer**: I guide the client through the **Fit-Gap Matrix**: 
  1. Demonstrate the standard ERP workflow during a Conference Room Pilot (CRP) to show how modern industry best practices solve their core business need without custom code.
  2. Articulate the total cost of ownership (TCO): customization increases implementation timeline, increases testing complexity, and creates recurring technical debt during future version upgrades.
  3. If the custom requirement does not provide a defensible competitive advantage, recommend **Process Change**. If it is a genuine unique differentiator, scope it as a controlled, cleanly decoupled custom module.

#### Q3: What is a Conference Room Pilot (CRP) and why is it critical?
- **Answer**: A CRP is an interactive workshop where real end-users and department leads walk through end-to-end business scenarios (e.g. Lead $\to$ SO $\to$ Delivery $\to$ Invoice) in the ERP using actual sample company data. It validates whether the configured solution meets operational realities early in the project life cycle, uncovering hidden process gaps months before formal User Acceptance Testing (UAT).

#### Q4: When is Agile appropriate for an ERP implementation, and when is Waterfall better?
- **Answer**: A **Hybrid Agile-Waterfall approach** is optimal:
  - **Waterfall** for project governance, budget milestones, data migration cutover, and final UAT sign-off.
  - **Agile / Scrum** during the Build Phase (iterative two-week sprints to configure modules, author custom extensions, demo progress to business champions, and incorporate rapid feedback).

#### Q5: What are the key criteria for a successful Go/No-Go Go-Live decision?
- **Answer**:
  1. 100% of P1 (Critical) and P2 (Major) UAT test cases passed with business sign-off.
  2. Zero open blocker defects.
  3. Trial Balance and Inventory Asset reconciliation matches legacy financial ledgers to the exact cent on a staging dry-run.
  4. 90%+ of staff passed end-user simulation training.
  5. A documented, rehearsed cutover sequence and rollback plan.

#### Q6: How do you prevent scope creep during an enterprise implementation?
- **Answer**: Baseline the Statement of Work (SOW) during Blueprinting. When a new request surfaces, evaluate whether it was explicitly included in the SOW. If outside scope, explain the trade-off to the stakeholder and channel the request through the formal **Change Control Board (CCB)**: assess additional hours, commercial cost, and schedule impact. Require executive sign-off before allocating development resources, or defer it to Phase 2.

#### Q7: How do you ensure high data quality during legacy ERP data migration?
- **Answer**: Implement an **ETL (Extract, Transform, Load) Pipeline**:
  1. Extract raw legacy tables.
  2. Cleanse and deduplicate master records (consolidate duplicate partners, standardize country codes, clean phone numbers).
  3. Map legacy foreign keys to new ERP relational IDs.
  4. Perform test load into staging and execute the **Golden Reconciliation Tests** (Trial balance, AR/AP aging balance, stock valuation).

#### Q8: What is the ADKAR change management framework, and how do you apply it to ERP?
- **Answer**: Prosci's ADKAR model addresses user adoption:
  - **Awareness**: Communicate why legacy systems are being replaced.
  - **Desire**: Highlight benefits to individual contributors (e.g. eliminating manual paper logs).
  - **Knowledge**: Role-specific interactive training sessions.
  - **Ability**: Sandbox practice under simulated business pressure.
  - **Reinforcement**: Post-go-live recognition, KPI tracking, and deprecating old spreadsheets.

#### Q9: What is the difference between master data, transactional data, and configuration data?
- **Answer**:
  - **Configuration Data**: Structural rules set once during implementation (Chart of Accounts, tax rates, warehouse routes, payment terms).
  - **Master Data**: Core business entities that persist across transactions (Customers, Vendors, Products, BOMs, Price lists).
  - **Transactional Data**: Daily operational records created during business activities (Sales Orders, Invoices, Delivery Slips, Stock Moves, Journal Entries).

#### Q10: How do you handle multi-phase rollouts across global subsidiaries?
- **Answer**: Establish a **Global Core Template** containing 80% of standard enterprise configurations (Chart of Accounts skeleton, standard sales/purchase workflows, security groups). For each subsidiary rollout, layer on local legal/tax localization rules (local VAT/GST, localized invoice PDF layouts, local banking integration) while preserving core corporate reporting rollup.

---

### Section 2: Order-to-Cash (O2C) & CRM (Q11 – Q18)

#### Q11: What is the difference between Invoicing Based on Ordered Quantities vs Delivered Quantities?
- **Answer**: Invoicing on Ordered Quantities generates the customer invoice immediately upon Sales Order confirmation, regardless of stock delivery (common for services, subscriptions, and prepayments). Invoicing on Delivered Quantities restricts billing to only the items physically validated on outgoing shipment delivery orders, preventing billing discrepancies and GAAP revenue recognition violations for physical goods.

#### Q12: How does an automated Credit Limit enforcement mechanism function?
- **Answer**: The system computes total exposure: $\text{Total Exposure} = \text{Open Invoices (Unpaid AR)} + \text{Pending Confirmed Uninvoiced Orders}$. If $\text{Total Exposure} > \text{Customer Credit Limit}$, the sales order is automatically placed on **Credit Hold**, blocking warehouse fulfillment and delivery order generation until a finance credit manager manually approves or the customer settles overdue balances.

#### Q13: How does Revenue Recognition under ASC 606 / IFRS 15 work for bundled contracts?
- **Answer**: Under the 5-step model, revenue cannot be recognized all at once for bundled offerings. The total transaction price must be allocated across distinct performance obligations based on their standalone selling prices (SSP). For example, in a $120,000 deal covering a Software License ($100k) and 1-Year Support ($20k): the $100k license is recognized immediately upon delivery, while the $20k support is booked as **Deferred Revenue (Liability)** and recognized ratably at $1,666.67 per month over 12 months.

#### Q14: How do price lists resolve tiered volume discounts and promotional formulas?
- **Answer**: Price lists evaluate rules in order of specificity (Product Variant $\to$ Product Template $\to$ Product Category $\to$ Global). Rules check quantity thresholds (e.g. minimum quantity $\ge 50$ units) and calculate price via fixed price override, percentage discount, or formula: $\text{New Price} = \text{Cost} + (\text{Markup \%}) - \text{Discount} + \text{Rounding}$.

#### Q15: What is a Down Payment / Deposit invoice, and how does it affect accounting?
- **Answer**: A down payment is an advance billing prior to delivery. It generates an invoice debiting Accounts Receivable and crediting a **Down Payment Customer Deposit Liability account** (not sales revenue, because performance obligations have not been fulfilled). When the final order is delivered and billed, the down payment line is deducted from the final invoice, relieving the liability and recognizing earned revenue.

#### Q16: How do you handle Return Merchandise Authorization (RMA) workflows?
- **Answer**: When a customer returns goods:
  1. Generate an incoming Return Picking to receive the physical item back into warehouse QA/Return location.
  2. Inspect the returned item: classify as restockable or scrap.
  3. Generate a **Credit Note (Refund Invoice)** to relieve the customer's AR balance or issue store credit.
  4. The inventory movement automatically reverses COGS and re-credits the Stock Valuation asset account.

#### Q17: What is the difference between Lead Qualification via BANT vs MEDDIC?
- **Answer**:
  - **BANT**: Budget, Authority, Need, Timeline. Ideal for high-velocity transactional sales.
  - **MEDDIC**: Metrics, Economic Buyer, Decision Criteria, Decision Process, Identify Pain, Champion. Used for enterprise B2B sales cycles with multi-stakeholder committees and complex procurement criteria.

#### Q18: How do you manage multi-currency quotations where exchange rates fluctuate wildly?
- **Answer**: Configure quotations with an explicit expiration date (e.g. valid for 7 days). Store the quotation currency and locked exchange rate on the quotation document itself. When the order is confirmed, lock the agreed rate for the entire transaction lifecycle so subsequent currency fluctuations do not alter customer billing amounts.

---

### Section 3: Procure-to-Pay (P2P) & Vendor Supply (Q19 – Q26)

#### Q19: Explain the 3-Way Matching process and how discrepancies are resolved.
- **Answer**: 3-Way Matching compares the **Purchase Order** (agreed price and quantity), **Goods Receipt Note** (physically received quantity), and **Vendor Bill** (invoiced price and quantity). If billed quantity exceeds received quantity or billed unit price exceeds PO price outside configured tolerance limits (e.g. $\pm 2\%$), the vendor bill is automatically placed on **Payment Hold** requiring buyer investigation or a vendor credit note.

#### Q20: What is Purchase Price Variance (PPV) and where does it post in the GL?
- **Answer**: PPV occurs under Standard Costing when the actual purchase price differs from the standard cost: $\text{PPV} = (\text{Actual Price} - \text{Standard Cost}) \times \text{Received Qty}$. It posts to the Profit & Loss statement as a variance expense (or income), isolating purchasing efficiency from operational manufacturing costs.

#### Q21: How do Landed Costs work, and why must they be capitalized into inventory?
- **Answer**: Landed costs represent inbound transportation, ocean freight, customs tariffs, and port handling. Capitalizing landed costs into inventory ensures that ending inventory on the balance sheet reflects true replacement cost and cost of goods sold (COGS) matches GAAP/IFRS matching principles when products are sold.

#### Q22: What are the different allocation methods for Landed Costs?
- **Answer**:
  - **By Current Value**: High-value items absorb proportionally more freight/customs costs (ideal for general merchandise).
  - **By Gross Weight**: Heavy items absorb more freight costs (ideal for metals, raw materials).
  - **By Volume (m³)**: Bulky items taking up container space absorb more shipping cost.
  - **Equally**: Flat split across all items.

#### Q23: What is a Blanket Purchase Agreement (Call-Off Order)?
- **Answer**: A long-term contractual agreement with a vendor for a negotiated volume (e.g. 50,000 units over 12 months at a guaranteed price of $10/unit). Instead of receiving everything at once, the procurement team releases individual **Call-Off Purchase Orders** throughout the year based on live operational demand while honoring the blanket contract pricing.

#### Q24: How do you design multi-tier approval workflows for purchase orders?
- **Answer**: Define approval matrices linked to monetary thresholds and department cost centers:
  - $< \$5,000$: Auto-approved.
  - $\$5,000 - \$25,000$: Requires Department Manager approval.
  - $\$25,000 - \$100,000$: Requires VP Procurement.
  - $> \$100,000$: Requires CFO sign-off.
  The PO remains in a `waiting_approval` state, blocking RFQ dispatch to the vendor until all required approvers complete electronic sign-offs.

#### Q25: How does Vendor Performance Evaluation function in an enterprise ERP?
- **Answer**: The ERP tracks historical vendor delivery metrics:
  - **On-Time Delivery Rate (OTD %)**: Comparing PO promised date vs actual receipt date.
  - **Quality Acceptance Rate %**: Scrapped or returned units vs total received units.
  - **Price Compliance**: Comparing invoiced price vs agreed contract price.
  These KPIs automatically generate vendor scorecards during annual contract renegotiations.

#### Q26: What is a Goods In Transit / Stock Interim account?
- **Answer**: An accounting clearing account on the balance sheet. When goods are received at the warehouse dock, the ERP debits `Stock Valuation (Asset)` and credits `Stock Interim Received (Liability)`. When the vendor bill arrives, the ERP debits `Stock Interim Received` and credits `Accounts Payable`. This allows the company to recognize inventory assets on day 1 even if the vendor bill arrives 30 days later.

---

### Section 4: Inventory, Warehouse & Valuation (Q27 – Q34)

#### Q27: What is the difference between Periodic and Perpetual (Real-Time) Stock Valuation?
- **Answer**:
  - **Periodic Valuation**: Inventory movements do not create financial journal entries. At month-end, physical counts determine ending inventory, and COGS is calculated retrospectively: $\text{COGS} = \text{Beginning Inventory} + \text{Purchases} - \text{Ending Inventory}$.
  - **Perpetual (Real-Time) Valuation**: Every single physical stock movement (receipt, internal transfer, delivery, scrap) automatically creates instant double-entry accounting journal entries in the General Ledger.

#### Q28: How does Average Costing (AVCO) recalculate unit cost upon receipt?
- **Answer**: Formula:
  $$\text{New Unit Cost} = \frac{(\text{Current Qty on Hand} \times \text{Current Unit Cost}) + (\text{Received Qty} \times \text{Received Unit Price})}{\text{Current Qty on Hand} + \text{Received Qty}}$$
  Outgoing sales deliveries consume inventory at this current AVCO cost without changing it.

#### Q29: What is Cross-Docking and when should you recommend it?
- **Answer**: Cross-docking routes incoming goods directly from the receiving dock to the outgoing shipping dock without ever putting them away into warehouse storage racks. Recommend it when dealing with high-demand fast-moving consumer goods (FMCG), backordered customer orders, or perishable items with tight expiration dates.

#### Q30: How does Dropshipping work from a logistics and accounting standpoint?
- **Answer**:
  - **Logistics**: The customer places an order with the company; the company issues a dropship PO to a third-party vendor with the customer's delivery address; the vendor ships directly to the customer.
  - **Accounting**: No internal stock location is touched. When the vendor validates shipping proof, the ERP records:
    Debit `Cost of Goods Sold (Expense)` | Credit `Stock Interim Received (Liability)`.
    When customer invoice is issued: Debit `Accounts Receivable` | Credit `Sales Revenue`.

#### Q31: What is the difference between a Push Rule and a Pull Rule in warehouse routing?
- **Answer**:
  - **Pull Rule**: Demand-driven. A need for goods at the destination location (e.g. Customer delivery) triggers a procurement or internal picking transfer to pull stock from the source location (e.g. Stock $\to$ Packing $\to$ Customer).
  - **Push Rule**: Supply-driven. The arrival of goods at a location automatically triggers an immediate subsequent movement pushing them to the next destination (e.g. Goods arriving at Inbound Dock are automatically pushed to Quality Inspection).

#### Q32: What is Cycle Counting and why is it superior to annual physical shutdowns?
- **Answer**: Cycle counting continuously audits a small subset of warehouse inventory every day or week based on ABC analysis (Category A: high-value items counted monthly; Category B: quarterly; Category C: annually). It eliminates costly annual plant shutdowns, detects inventory theft and shrinkage in real time, and maintains $> 98\%$ inventory accuracy year-round.

#### Q33: How do you handle scrap vs waste vs unbuild in inventory?
- **Answer**:
  - **Scrap**: Damaged or defective goods removed from stock. Debits `Scrap Expense` and credits `Stock Valuation Asset`.
  - **Waste / Shrinkage**: Unaccounted inventory losses resolved during cycle counting adjustments.
  - **Unbuild**: Disassembling a finished good back into raw components, transferring asset value from the finished SKU back to individual component asset accounts.

#### Q34: How does Min-Max automated replenishment work?
- **Answer**: The Techno-Functional Lead configures two thresholds per product per warehouse: **Minimum Quantity** (safety buffer triggering reorder) and **Maximum Quantity** (storage capacity ceiling). When available stock falls below Minimum, the scheduler generates a purchase order or manufacturing order for: $\text{Order Qty} = \text{Maximum Quantity} - \text{Current Available Quantity}$.

---

### Section 5: Accounting, Taxes & Ledgers (Q35 – Q42)

#### Q35: What happens to P&L accounts during Year-End Fiscal Closing?
- **Answer**: At fiscal year-end, all temporary Income Statement accounts (Revenues, Cost of Goods Sold, Operating Expenses) must be closed out to net zero balance. The resulting net profit or loss is transferred into the permanent Balance Sheet equity account: **Retained Earnings**. The fiscal period is then hard-locked to prevent retro-active changes.

#### Q36: How does Fiscal Position mapping work for International B2B vs Domestic B2C?
- **Answer**: The customer's fiscal position overrides default product taxes:
  - Domestic B2C: Retains standard local VAT (e.g. 20%).
  - Intra-EU B2B with valid VAT ID: Automatically maps 20% VAT to **0% Reverse Charge Intra-Community Supply**.
  - Non-EU International Export: Automatically maps 20% VAT to **0% Export Tax Exemption**.

#### Q37: What is the difference between Realized and Unrealized Foreign Exchange Gain/Loss?
- **Answer**:
  - **Unrealized Forex Gain/Loss**: Calculated at month-end closing by revaluing open, unpaid foreign currency invoices/bills against the latest closing exchange rate. Recognized on the Balance Sheet and automatically reversed on the first day of the subsequent month.
  - **Realized Forex Gain/Loss**: Permanently recognized in the Profit & Loss statement on the exact date an invoice is paid and reconciled, reflecting the actual cash difference between invoice booking rate and settlement bank rate.

#### Q38: How does bank statement reconciliation operate in modern ERPs?
- **Answer**: Bank feeds import daily statements via OFX, CAMT.053, or automated Open Banking APIs (Plaid). The ERP uses automated reconciliation rules to match statement transactions:
  1. Exact amount and customer invoice reference match $\to$ auto-reconcile.
  2. Direct debit / utility bill match $\to$ auto-post to designated expense account.
  3. Wire transfer fees $\to$ auto-write-off to bank fee expense account.

#### Q39: What is the difference between Accounts Receivable (AR) and Unearned / Deferred Revenue?
- **Answer**: Accounts Receivable is an **Asset** representing money owed by customers for goods/services already delivered. Deferred Revenue is a **Liability** representing cash collected from customers in advance for goods/services that have not yet been delivered or earned.

#### Q40: What are Analytic / Cost Centers and how do they differ from the General Ledger?
- **Answer**: General Ledger accounts answer *What* was spent (e.g. Account 610000: Cloud Hosting Expense). Analytic Cost Centers answer *Why* or *Who* spent it (e.g. Project Alpha, Marketing Department, North America Branch). Analytic distribution splits expenses across multiple projects or departments without bloating the statutory Chart of Accounts.

#### Q41: How do you handle Inter-Company reconciliations between parent and subsidiary entities?
- **Answer**: Subsidiary A records an Inter-Company Receivable from Subsidiary B, and Subsidiary B records a matching Inter-Company Payable to Subsidiary A. During monthly consolidation, these inter-company balances and transactions must be eliminated via **Elimination Journal Entries** so consolidated group financial statements do not double-count revenue or debt.

#### Q42: What is an Accounting Lock Date and what are its two levels?
- **Answer**:
  1. **Tax Lock Date**: Prevents altering journal entries in past tax return periods to ensure VAT/GST audit compliance.
  2. **Management / General Lock Date**: Completely locks the ledger for all non-advisor users, preventing retroactive adjustments to finalized fiscal quarters or audited historical years.

---

### Section 6: Manufacturing (MRP) & Production (Q43 – Q46)

#### Q43: What is the difference between a Kit BOM and a Manufacturing BOM?
- **Answer**: A Manufacturing BOM creates a separate physical production order to consume raw components and produce a new finished goods inventory item. A Kit BOM is purely commercial: components are stored as individual parts in the warehouse, but sold and billed under a single kit product SKU. When a delivery order is validated, the ERP automatically picks the individual kit components from shelves.

#### Q44: What is a Phantom BOM and why is it used?
- **Answer**: A Phantom BOM is an engineering sub-assembly that does not physically exist as standalone stocked inventory. During an MRP manufacturing explosion, the phantom parent item is bypassed, and its constituent raw components are pulled directly into the higher-level parent Manufacturing Order, simplifying inventory tracking.

#### Q45: How does the MRP engine schedule purchase orders backwards from customer delivery dates?
- **Answer**: Backward scheduling formula:
  $$\text{Promised Delivery Date} - \text{Customer Shipping Lead Time} - \text{Manufacturing Lead Time} - \text{Vendor Procurement Lead Time} = \text{PO Dispatch Date}$$
  If the calculated PO Dispatch Date falls in the past, the system flags the order with an exception alert, indicating that customer delivery must be rescheduled.

#### Q46: How is Work Center Costing calculated into finished goods valuation?
- **Answer**: Work centers have hourly cost rates ($\text{Cost/Hour} = \text{Machine Depreciation} + \text{Electricity} + \text{Direct Labor}$). When an operation finishes, the ERP records:
  $$\text{Operational Cost} = \text{Actual Duration (Hours)} \times \text{Work Center Hourly Rate}$$
  This operational labor/overhead cost is debited to `Stock Valuation Asset` and credited to a `Manufacturing Labor Overhead Absorption Account`, ensuring product inventory reflects full absorption manufacturing cost.

---

### Section 7: Stakeholder Politics & Change Leadership (Q47 – Q50+)

#### Q47: How do you manage conflicting requirements between the Sales Director and the CFO?
- **Answer**: Facilitate a joint alignment workshop centered on corporate financial goals. Translate each department's pain points into quantifiable metrics (Sales: deal velocity and competitive agility; Finance: gross margin protection and cash flow risk). Architect a compromise through automated policy tiers (e.g. Sales reps have autonomy up to 10% discount; 10–20% requires Sales Director; $> 20\%$ requires Finance approval). Present the solution as a collaborative governance policy rather than one department defeating the other.

#### Q48: How do you respond when an executive sponsor demands an unrealistic Go-Live date that threatens project quality?
- **Answer**: Present an objective **Iron Triangle Analysis** (Scope, Time, Cost):
  *"We can achieve this accelerated Go-Live date, but to do so without catastrophic operational risk, we must reduce Scope."*
  Present a clear MVP (Minimum Viable Product) proposal: isolate core commercial transactions (Order, Ship, Bill) for Phase 1 Go-Live, while deferring non-critical features (advanced automated freight bidding, complex custom reporting) to Phase 2. Back up your recommendation with risk data from defect counts and UAT test coverage.

#### Q49: What is your strategy for handling vocal user resistance during UAT?
- **Answer**: Identify the root cause of the resistance. Often, vocal resistance stems from fear of obsolescence, inadequate training, or perceived loss of control. I identify informal opinion leaders among the resistant staff, schedule one-on-one sessions to understand their daily operational pain points, and give them direct ownership of configuring their daily dashboard views. Converting vocal skeptics into system champions turns the biggest project risk into an adoption engine.

#### Q50: How do you structure hypercare support following Go-Live?
- **Answer**:
  1. Maintain an on-site / virtual Command Center with dedicated functional leads and senior technical developers.
  2. Establish a rapid triage SLA: P1 blockers (cannot ship or bill) resolved within 1 hour; P2 major issues within 4 hours.
  3. Conduct daily morning standups with department leads to review open ticket counts and operational blockers.
  4. Run daily reconciliation checks on inventory moves and financial ledgers during the first 14 days.
  5. Formal handover to standard Business-As-Usual (BAU) support only after ticket volume stabilizes at low baseline levels for two consecutive weeks.
