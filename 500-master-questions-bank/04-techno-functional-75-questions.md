# 500 Master Questions Bank: Volume 4
## 75 Techno-Functional Lead & ERP Solution Architect Interview Questions

> 🧭 **Navigation**: [⬅️ Previous: Volume 3: 100 Senior AI Questions](03-senior-ai-100-questions.md) | [🏠 500 Master Questions Bank Index](README.md) | [➡️ Next: Volume 5: 75 Odoo Lead Questions](05-odoo-technical-lead-75-questions.md)

---

This volume contains **75 deep scenario-based questions and answers** tailored for Techno-Functional Leads, Principal ERP Consultants, and Solution Architects. It bridges boardroom business requirements, accounting integrity, supply chain operations, and technical software execution.

---

### Section 1: ERP Implementation Methodology, Fit-Gap & Scope Governance (Q1 – Q15)

#### Q1: What is the architectural difference between a BRD, FSD, and TSD?
- **Answer**:
  - **BRD (Business Requirement Document)**: Authored by business process owners; defines the commercial objective and problem statement without software jargon (e.g., "Field reps must capture customer orders offline and sync when connected").
  - **FSD (Functional Specification Document)**: Authored by the Techno-Functional Lead; translates the BRD into functional application workflows, state machines, validation rules, screen wireframes, and access control matrices (e.g., "IndexedDB stores orders locally in OWL POS; background worker syncs via `/api/v1/orders` with conflict resolution based on timestamp").
  - **TSD (Technical Specification Document)**: Authored by the Technical Lead; defines database schemas, ORM model inheritance, SQL indexes, API contract specs, algorithm complexity, and unit test suites.

#### Q2: How do you handle a stubborn C-level executive who demands heavy customizations to recreate their legacy 20-year-old green-screen workflow?
- **Answer**: Apply the **Fit-Gap & Total Cost of Ownership (TCO) Framework**:
  1. **Acknowledge and Validate**: Frame the discussion around business outcomes rather than UI nostalgia. Ask: *"What underlying business KPI does this specific screen layout protect?"*
  2. **Demonstrate Standard Best Practice**: Conduct a side-by-side Conference Room Pilot (CRP) showing how modern standard ERP workflows achieve the same outcome with 60% fewer clicks and automated notifications.
  3. **Quantify the Upgrade & Maintenance Debt**: Present the financial TCO: custom UI layers increase migration costs by $300\text{k}+$, delay version upgrades by 18 months, and break mobile/tablet responsiveness.
  4. **Categorize in Fit-Gap Matrix**: If the legacy feature provides zero defensible competitive edge, classify as **Process Change (Adopt Standard)**. If it represents a proprietary secret sauce, encapsulate it as a clean, decoupled micro-extension with stable API contracts.

#### Q3: What is a Conference Room Pilot (CRP), and how many iterations should an enterprise project run?
- **Answer**: A CRP is an interactive workshop where operational end-users and process owners execute end-to-end operational scenarios using real historical company data on a configured test environment.
  - **CRP 1 (Proof of Concept)**: Validates standard out-of-the-box fit against core high-volume processes (O2C, P2P). Identifies genuine functional gaps early.
  - **CRP 2 (Integrated System Validation)**: Validates custom modules, external API integrations (payment gateways, 3PL shippers, tax engines), and complex multi-company transactions.
  - **CRP 3 / User Acceptance Testing (UAT)**: Operational sign-off by all department heads under simulated peak-volume conditions.

#### Q4: How do you structure a hybrid Agile-Waterfall governance model for an ERP rollout?
- **Answer**:
  - **Waterfall Envelope**: Project charter, budget governance, scope baselining, enterprise architecture blueprints, cutover scheduling, and legal go-live sign-offs require strict stage gates (Waterfall).
  - **Agile Execution Engine**: The Build and Configuration phase runs in 2-week Agile sprints: backlog refinement, module configuration, iterative user story reviews with process champions, and CI/CD automated deployments to staging.

#### Q5: What are the mandatory criteria for a Go/No-Go cutover decision?
- **Answer**:
  1. **Defect Threshold**: Zero open P1 (Critical/Blocker) and P2 (Major) defects; any open P3/P4 defects have documented operational workarounds.
  2. **Financial Golden Reconciliation**: Staging migration dry-run shows General Ledger trial balance, AR aging, AP aging, and Inventory asset valuation match the legacy system down to the exact cent.
  3. **End-to-End Test Execution**: 100% of critical business paths validated in UAT with formal stakeholder sign-offs.
  4. **User Readiness**: $\ge 90\%$ of operational staff have completed mandatory simulated training modules.
  5. **Cutover Runbook & Rollback Plan**: Hour-by-hour operational runbook with identified cutover team roles, external freeze windows, and a validated rollback/restore procedure.

#### Q6: What is the ADKAR change management framework, and how does a Lead apply it?
- **Answer**:
  - **A (Awareness)**: Town halls communicating *why* the old ERP is being sunsetted (security vulnerabilities, lack of API integration, slow reporting).
  - **D (Desire)**: Department-level workshops demonstrating personal benefits (automated invoice reconciliation eliminating weekend overtime).
  - **K (Knowledge)**: Role-based training documentation, short video walk-throughs, and step-by-step SOPs.
  - **A (Ability)**: Hands-on sandbox simulation sessions where workers process real daily loads without penalty for errors.
  - **R (Reinforcement)**: Post-go-live floor-walking "super-users", daily triage standups, deprecating old Excel macros, and celebrating operational milestones.

#### Q7: How do you prevent and control scope creep when client department heads request "minor tweaks" during sprints?
- **Answer**:
  1. Measure every request against the approved **Scope Baseline / Statement of Work (SOW)**.
  2. If the request is outside baseline, ask: *"Is this required for Day-1 legal compliance or basic revenue generation?"*
  3. If not, log it in the **Phase 2 / Continuous Improvement Backlog**.
  4. If the client insists, issue a formal **Change Request (CR)** evaluating three vectors: dollar impact, sprint schedule delay, and resource constraints. The CR requires executive steering committee approval before coding begins.

#### Q8: What is the difference between Master Data, Transactional Data, and Configuration Data?
- **Answer**:
  - **Configuration Data**: Underlying rules and structures set once during setup (Chart of Accounts, Tax Rules, Currencies, Warehouse Locations, Payment Terms).
  - **Master Data**: Core business nouns that persist across transactions (Customers, Vendors, Products, Bill of Materials, Price Lists).
  - **Transactional Data**: Dynamic verb records documenting business events (Sales Orders, Invoices, Delivery Slips, Stock Moves, Journal Entries).

#### Q9: How do you design an ETL data migration pipeline from a legacy ERP?
- **Answer**:
  1. **Extract**: Dump legacy tables into an intermediate staging PostgreSQL schema.
  2. **Cleanse & Transform**: Deduplicate customer records by tax ID / email, standardize country/state ISO codes, convert string addresses to structured partner models, map old ledger accounts to new Chart of Accounts.
  3. **Validation Scripts**: Run pre-load validation queries checking for orphaned foreign keys, null mandatory fields, and mismatched currency codes.
  4. **Load (Sequential Hierarchy)**: Users/Companies $\to$ Accounts/Taxes $\to$ Partners $\to$ Product Categories $\to$ Products $\to$ Opening Balances (GL, Stock, Open AR/AP).
  5. **Post-Load Audit**: Run automated reconciliation queries comparing row counts and financial totals.

#### Q10: How do you handle legacy historical transactions during cutover (e.g., 5 years of historical invoices)?
- **Answer**: Never migrate 5 years of historical transactional documents (Sales Orders, Invoices, Stock Moves) into the new operational database! It bloats the database, distorts reporting, and risks data integrity bugs. Instead:
  1. Migrate open balances only: Open AR invoices, open AP bills, open purchase orders, open sales orders, and opening inventory asset valuation.
  2. Archive historical closed transactions into a read-only **Data Lake / Snowflake / BigQuery data warehouse**.
  3. Provide an embedded BI reporting dashboard or lightweight lookup tool inside the new ERP for customer service reps to view read-only historical purchase history.

#### Q11: What is a RACI matrix and how is it used during an ERP implementation?
- **Answer**: RACI defines roles for every project deliverable:
  - **R (Responsible)**: The person who does the work (e.g., Techno-Functional Lead configures the tax engine).
  - **A (Accountable)**: The single individual with final decision authority (e.g., VP of Finance signs off on the Chart of Accounts).
  - **C (Consulted)**: Subject Matter Experts providing input (e.g., Warehouse Manager consulted on stock picking routes).
  - **I (Informed)**: Stakeholders kept updated on progress (e.g., Sales Reps informed of go-live dates).

#### Q12: How do you resolve conflicting business requirements between Sales and Operations?
- **Answer**: Sales typically prioritizes frictionless booking (zero stock validation, generous credit limits), while Operations prioritizes inventory accuracy and on-time fulfillment.
  - **Resolution**: Align both parties around core financial and customer retention metrics. Configure the system with automated soft-allocation: allow Sales to confirm orders against expected incoming purchase shipments (ATP - Available to Promise), while configuring hard inventory validation at delivery slip validation. Implement automated credit limit warning prompts rather than hard blocks for tier-1 accounts.

#### Q13: What is "Hypercare" and what metrics determine its conclusion?
- **Answer**: Hypercare is the 30-to-60 day post-go-live stabilization period where the implementation team provides dedicated, high-touch operational support.
  - **Exit Criteria**:
    1. Zero open P1/P2 tickets for 10 consecutive business days.
    2. First successful monthly financial period close completed on schedule.
    3. Daily transaction volume meets or exceeds pre-go-live baseline.
    4. Operational ticket handover to internal Tier-1/Tier-2 support desk completed.

#### Q14: How do you audit ERP system access for SOX / GDPR compliance?
- **Answer**:
  - **Segregation of Duties (SoD)**: Ensure no single user has rights to both create a vendor and approve payments, or create a sales order and post customer credit notes.
  - **Role-Based Access Control (RBAC)**: Assign permissions strictly via security groups, never directly to individual user accounts.
  - **Audit Logging**: Enable immutable audit trails (`mail.tracking.value` / PostgreSQL triggers) capturing `user_id`, `timestamp`, `old_value`, and `new_value` on all financial, banking, and inventory master records.

#### Q15: How do you structure a Cutover Runbook?
- **Answer**: A minute-by-minute timeline covering:
  - **T minus 5 Days**: Announce system freeze; lock down legacy master data changes.
  - **T minus 2 Days**: Final delta data extraction from legacy system.
  - **T minus 1 Day**: Run data transformation scripts; load master data into production.
  - **Cutover Weekend (T-0)**: Load opening GL trial balance, opening inventory count, open AR/AP balances; execute Golden Reconciliation; verify integrations.
  - **Go-Live Morning**: Open system to end users; activate real-time API syncs; begin Hypercare standups.

---

### Section 2: Order-to-Cash (O2C) & Revenue Architecture (Q16 – Q30)

#### Q16: Walk through the complete lifecycle and accounting entries of the standard Order-to-Cash (O2C) cycle under perpetual inventory.
- **Answer**:
  1. **Quotation $\to$ Sales Order Confirmation**: No financial entries posted. Inventory transitions from "Available" to "Reserved".
  2. **Fulfillment / Delivery Order Validated**:
     - Debit: **Cost of Goods Sold (COGS)** (Asset expense)
     - Credit: **Stock Interim / Inventory Valuation Account** (Asset reduction)
  3. **Customer Invoice Validated**:
     - Debit: **Accounts Receivable (AR)** (Asset)
     - Credit: **Product Sales Revenue** (Income)
     - Credit: **Sales Tax Payable** (Liability)
  4. **Payment Received & Bank Reconciliation**:
     - Debit: **Bank Account** (Asset)
     - Credit: **Accounts Receivable (AR)** (Asset cleared)

#### Q17: What is the functional and accounting difference between Invoicing on "Ordered Quantities" vs "Delivered Quantities"?
- **Answer**:
  - **Ordered Quantities**: Invoice can be created and posted immediately upon Sales Order confirmation, before physical shipment occurs. Common in services, pre-paid wholesale, or make-to-order subscriptions. Risk: If shipment is canceled or short-shipped, customer was overcharged, requiring a credit note.
  - **Delivered Quantities**: Invoicing is strictly blocked until the delivery order is validated in the warehouse. Invoices only pull quantities validated in stock moves (`qty_delivered`). Mandatory for physical distribution to prevent customer billing disputes and balance sheet distortions.

#### Q18: How does an ERP handle Drop-Shipping functionally and financially?
- **Answer**:
  - **Functional Flow**: Customer orders product from Merchant $\to$ Sales Order confirmation automatically triggers a Purchase Order directly to 3rd-Party Vendor $\to$ Vendor ships directly to End Customer $\to$ Merchant validates a virtual drop-ship receipt $\to$ Merchant invoices customer.
  - **Accounting Flow**:
    - Vendor Bill: Debit **COGS / Drop-Ship Expense**, Credit **Accounts Payable**.
    - Customer Invoice: Debit **Accounts Receivable**, Credit **Sales Revenue**.
    - Physical inventory never enters the merchant's warehouse balance sheet.

#### Q19: How do you design an automated Credit Limit Management system in an ERP?
- **Answer**:
  - **Formula**:
    $$\text{Total Exposure} = \text{Open Unpaid Invoices} + \text{Invoiced Unposted} + \text{Confirmed Pending SOs} + \text{Draft Unconfirmed SOs}$$
  - **Rules**:
    1. If $\text{Total Exposure} + \text{Current Order Value} > \text{Customer Credit Limit}$, the Sales Order is blocked from automatic confirmation and shifted to `Waiting Credit Approval`.
    2. Automated email triggers to the Credit Risk Team.
    3. Allow overriding authority only to designated Financial Controllers (`group_credit_manager`).

#### Q20: How does ASC 606 / IFRS 15 affect subscription and multi-element sales contracts?
- **Answer**: ASC 606 mandates a 5-step model: Identify contract, identify distinct performance obligations, determine transaction price, allocate price based on Standalone Selling Price (SSP), and recognize revenue as obligations are satisfied.
  - **ERP Implementation**: When a customer purchases a hardware device (\$1,200) bundled with a 12-month software subscription (\$1,200/yr) for a discounted total bundle price of \$2,000:
    - Allocate revenue based on SSP ratio ($50\% / 50\%$).
    - On Day 1 (Delivery): Recognize \$1,000 hardware revenue immediately.
    - Day 1: Post \$1,000 to **Deferred Revenue (Liability)**.
    - Months 1–12: Asynchronous cron job posts monthly journal entry recognizing $\$1,000 / 12 = \$83.33$ from Deferred Revenue into Active Subscription Revenue.

#### Q21: What is the functional purpose of a Customer Credit Note (Credit Memo), and how does it differ from a Refund?
- **Answer**:
  - **Credit Note**: A formal accounting legal document that reverses an invoice in full or part, reducing the Accounts Receivable balance.
  - **Refund**: The actual cash outflow transferring funds from the company's bank account back to the customer. A credit note can either be held on account to offset future invoices or paid out via a refund transaction.

#### Q22: How do you implement Price Lists with multi-tiered volume discounts and customer categories?
- **Answer**: Use a rule-based hierarchical engine:
  1. Priority order: Specific Customer Variant Price $\to$ Customer Group Category Price $\to$ Global Public Price.
  2. Dynamic computation types:
     - Fixed price (e.g., \$45.00).
     - Percentage discount (e.g., List Price $- 15\%$).
     - Formula-based (e.g., Cost $+ 20\%$ margin with min/max price bounds).
  3. Tiered quantities: Break rules by minimum quantity (e.g., 1–49 units = \$10/ea, 50–99 units = \$8/ea, 100+ units = \$6.50/ea).

#### Q23: How do you architect an omnichannel retail setup with 50 brick-and-mortar stores and an online Shopify store?
- **Answer**:
  - **Centralized Master Catalog**: Single source of truth for products, barcodes, and standard pricing.
  - **Multi-Warehouse Routing**: Each retail store is a dedicated stock location with an isolated Point-of-Sale (POS) cache.
  - **Real-Time Inventory Allocation**: Shopify queries the central ERP API for ATP (Available to Promise) stock across distribution fulfillment centers.
  - **Offline Resilience**: Retail POS registers use client-side IndexedDB to ensure checkout continues during internet outages, syncing transactions asynchronously when connection restores.

#### Q24: What is an RMA (Return Merchandise Authorization) process, and what stock moves are involved?
- **Answer**:
  1. Customer requests return $\to$ Support initiates RMA document.
  2. Customer ships goods back $\to$ Warehouse receives into a **Quality Control / Inspection Location**.
  3. Quality Inspection decision:
     - **Pass (Restock)**: Move from QC $\to$ Available Stock. Triggers replacement delivery or Customer Credit Note.
     - **Fail (Scrap)**: Move from QC $\to$ Scrap / Loss Location. Debits Scrap Expense account.
     - **Rework / Repair**: Move from QC $\to$ Internal Repair Shop.

#### Q25: How do you handle customer prepayments / down payments in ERP accounting?
- **Answer**:
  - When customer pays 30% advance on a \$100,000 capital equipment order:
    - Step 1: Create Down Payment Invoice.
    - Debit: **Bank Account** (\$30,000)
    - Credit: **Customer Deposits / Prepayment Liability** (\$30,000) (Not recognized as revenue yet!).
  - Step 2: Final delivery and final invoice generated (\$100,000):
    - Debit: **Accounts Receivable** (\$70,000)
    - Debit: **Customer Deposits Liability** (\$30,000) (clears liability)
    - Credit: **Equipment Sales Revenue** (\$100,000)

#### Q26: What is the difference between FOB Origin (Shipping Point) and FOB Destination?
- **Answer**:
  - **FOB Origin**: Ownership, title, and risk transfer to the buyer as soon as the carrier picks up the goods at the seller's loading dock. The buyer pays freight and books inventory in transit.
  - **FOB Destination**: Ownership and risk transfer to the buyer only when the goods physically arrive at the buyer's warehouse. The seller is responsible for freight and bears loss risk during transport.

#### Q27: How do you configure automated Sales Commission calculation in an ERP?
- **Answer**: Link commission rules to **Invoiced Margin** or **Cash Collected**, never raw Sales Order totals!
  - **Formula**:
    $$\text{Commission} = (\text{Invoice Line Total} - \text{Product Standard Cost}) \times \text{Commission Rate \%}$$
  - Trigger calculation upon **Invoice Payment** rather than invoice validation to prevent paying commissions on defaulted or canceled invoices.

#### Q28: How do you prevent inventory race conditions when two customers simultaneously purchase the last available unit online?
- **Answer**: Use **Two-Phase Inventory Reservation**:
  1. High-speed distributed cache (Redis) runs an atomic decrement script (`DECRBY`) when user clicks "Checkout".
  2. Creates a temporary lock reservation with a 10-minute TTL.
  3. If payment succeeds, ERP validates database stock move using `SELECT FOR UPDATE` on `stock_quant`.
  4. If payment times out, Redis reservation expires and increments available stock back.

#### Q29: What is the functional role of Customer Statements and Dunning Letters?
- **Answer**:
  - **Customer Statement**: Chronological summary of all invoices, payments, and open balances sent monthly to commercial clients.
  - **Dunning Letters**: Tiered escalation notices for overdue debt:
    - Level 1 (1–15 days past due): Friendly reminder email.
    - Level 2 (16–30 days past due): Formal demand with interest charge warning.
    - Level 3 (31–60 days past due): Automated account credit block; sales reps prevented from booking new orders.
    - Level 4 (60+ days): Legal escalation and collection referral.

#### Q30: How do you design an Electronic Data Interchange (EDI) workflow for major retailers (e.g., Walmart, Amazon)?
- **Answer**:
  - **EDI 850 (Purchase Order)**: Ingested from retailer via AS2 protocol $\to$ translated via middleware (Stedi/MuleSoft) $\to$ calls ERP API to create draft Sales Order.
  - **EDI 855 (PO Acknowledgment)**: ERP validates stock availability and transmits confirmation back.
  - **EDI 856 (Advance Shipping Notice - ASN)**: Triggered when warehouse validates shipment; includes GS1-128 serial shipping container barcodes (SSCC).
  - **EDI 810 (Commercial Invoice)**: Generated automatically upon delivery and transmitted electronically for automated billing.

---

### Section 3: Procure-to-Pay (P2P), Vendor Management & 3-Way Matching (Q31 – Q45)

#### Q31: Walk through the complete lifecycle and accounting entries of the standard Procure-to-Pay (P2P) cycle.
- **Answer**:
  1. **Purchase Requisition $\to$ RFQ $\to$ Purchase Order (PO)**: No financial entry posted. PO creates committed procurement cost.
  2. **Goods Receipt (GRN Validated)**:
     - Debit: **Stock Valuation Account / Interim Stock Received** (Asset increase)
     - Credit: **GR/IR (Goods Receipt / Invoice Receipt) Accrual** (Current Liability)
  3. **Vendor Bill (AP Invoice Received & Matched)**:
     - Debit: **GR/IR Accrual Account** (Liability cleared)
     - Debit: **Input VAT / Tax Receivable** (Asset)
     - Credit: **Accounts Payable (AP)** (Liability)
  4. **Payment to Vendor**:
     - Debit: **Accounts Payable (AP)** (Liability cleared)
     - Credit: **Bank Account** (Asset reduction)

#### Q32: Explain 3-Way Matching and how an ERP enforces tolerance thresholds.
- **Answer**:
  - **3-Way Match Verification**: Compares three source documents:
    1. **Purchase Order**: Agreed quantity and unit price.
    2. **Warehouse Goods Receipt**: Actual physically received quantity.
    3. **Vendor Bill**: Billed quantity and billed unit price.
  - **Tolerance Rules**: Configured with dollar and percentage thresholds (e.g., $\le 2\%$ or $\le \$50$ variance allowed).
    - If Billed Qty $>$ Received Qty, or Billed Price $>$ PO Price beyond tolerance, the bill is automatically placed on **Payment Hold** requiring Purchasing Manager sign-off.

#### Q33: What is the GR/IR (Goods Receipt / Invoice Receipt) clearing account and why does it cause reconciliation headaches?
- **Answer**: GR/IR is a balance sheet accrual clearing account representing goods received for which no vendor bill has been posted (or vice versa).
  - **Reconciliation Issues**:
    - Vendor short-ships, but bills for full quantity.
    - Vendor bills at a higher unit price than agreed on PO.
    - Exchange rate fluctuations between goods receipt date and bill posting date.
    - Goods returned to vendor without logging a Return GRN.
  - **Solution**: Monthly GR/IR automated clearing runs; write off immaterial micro-variances to an **Unbilled Goods Received Variance** expense account.

#### Q34: What is a Blanket Purchase Order (Call-Off Order) and when is it used?
- **Answer**: A long-term contractual agreement with a supplier to deliver specified quantities at locked prices over an extended timeframe (e.g., 100,000 units of raw steel over 12 months).
  - Rather than creating 50 separate POs, the procurement team issues individual **Releases / Call-Offs** against the blanket contract as production demands require, tracking aggregate delivered quantities against the master commitment.

#### Q35: How does an ERP handle Vendor Rebates and Volume Discounts?
- **Answer**:
  - **Upfront Discount**: Deducted directly on PO line items (reduces inventory acquisition cost immediately).
  - **Retrospective / Retroactive Volume Rebate**: Supplier agrees: *"If you purchase $> \$1,000,000$ in a calendar year, we refund 5% at year-end."*
    - Accrue monthly: Debit **Vendor Rebate Receivable (Asset)**, Credit **COGS / Purchase Rebate Income**.
    - Year-End settlement: Vendor issues a credit note offsetting Accounts Payable.

#### Q36: What is Landed Cost and how is it distributed across imported goods?
- **Answer**: Landed Cost encompasses all ancillary costs incurred to bring imported inventory to a saleable state (Ocean Freight, Customs Duties, Marine Insurance, Port Handling).
  - **Allocation Methods**:
    1. **By Weight**: Ideal for heavy bulk raw materials (steel, chemicals).
    2. **By Volume (CBM)**: Ideal for bulky lightweight cargo.
    3. **By Monetary Value**: Standard for high-value electronics and luxury goods.
    4. **Equal Division**: Per line item.
  - **Accounting Entry**:
    - Debit: **Inventory Asset Account** (increases unit valuation of products)
    - Credit: **Landed Cost Interim / Freight Accrual Account**

#### Q37: How do you automate Purchase Requisitions using MRP reordering rules (Min/Max)?
- **Answer**:
  - **Parameters**:
    - **Min Quantity**: Safety stock threshold.
    - **Max Quantity**: Target replenishment ceiling.
    - **Quantity Multiple**: Supplier batch packaging (e.g., order in multiples of 50).
    - **Lead Time**: Vendor manufacturing + shipping days.
  - **Formula**:
    $$\text{Forecasted Stock} = \text{Current On-Hand} + \text{Incoming Approved POs} - \text{Outgoing Confirmed SOs}$$
    If $\text{Forecasted Stock} < \text{Min}$, the system automatically generates a draft PO for:
    $$\text{Order Qty} = \lceil (\text{Max} - \text{Forecasted Stock}) / \text{Multiple} \rceil \times \text{Multiple}$$

#### Q38: How do you handle Consignment Stock from both a buyer's and seller's perspective?
- **Answer**:
  - **Vendor Consignment (We hold supplier's goods in our warehouse)**:
    - Physical goods are stored in our location, but ownership remains with the supplier. No balance sheet entry on receipt!
    - When consumed in manufacturing or sold to customer: System automatically triggers a self-billing vendor invoice and posts inventory asset consumption.
  - **Customer Consignment (We place our goods at customer's retail store)**:
    - Move stock from Internal Warehouse to Partner Consignment Location. Retained on our balance sheet.
    - Monthly consumption report from customer triggers commercial sales invoice and COGS recognition.

#### Q39: What is ERS (Evaluated Receipt Settlement)?
- **Answer**: A streamlined P2P process where the buyer does not wait for a vendor invoice! Based on an established legal agreement, the ERP automatically generates the vendor bill upon successful Goods Receipt validation, matching the PO price and received quantity. Eliminates 90% of accounts payable data entry.

#### Q40: How do you enforce Vendor Performance KPIs (OTIF) in an ERP?
- **Answer**:
  - **OTIF (On-Time, In-Full)**:
    - **On-Time**: $\text{Delivery Date} \le \text{Promised PO Schedule Date}$.
    - **In-Full**: $\text{Received Quantity} = \text{PO Ordered Quantity}$.
  - System automatically calculates vendor reliability scores:
    $$\text{OTIF \%} = \frac{\text{Orders Delivered On-Time and In-Full}}{\text{Total Orders Placed}} \times 100$$
  - Underperforming vendors are automatically deprioritized by procurement routing engines.

#### Q41: How do you configure Segregation of Duties (SoD) in procurement to eliminate fraud?
- **Answer**:
  1. The user who creates/requests a Purchase Requisition cannot approve the Purchase Order.
  2. The warehouse personnel validating Goods Receipts cannot edit PO unit prices or access AP billing screens.
  3. The AP clerk entering vendor bills cannot execute bank payment disbursements.
  4. Any modification to a vendor's bank account routing number requires dual-authorization and out-of-band phone verification.

#### Q42: How do you handle Multi-Currency Purchase Orders and Foreign Exchange (FX) variance?
- **Answer**:
  - PO placed in EUR (Rate: 1.05 USD/EUR): 10,000 EUR (\$10,500).
  - Goods Receipt posted when rate is 1.08 USD/EUR:
    - Inventory Asset Debited at \$10,800. GR/IR Credited at \$10,800.
  - Vendor Bill received when rate is 1.10 USD/EUR:
    - GR/IR Debited at \$10,800.
    - **FX Realized Loss Debited at \$200**.
    - Accounts Payable Credited at \$11,000.

#### Q43: What is a Vendor Portal and what capabilities should it offer?
- **Answer**: A secure external web portal where certified suppliers can:
  - View published RFQs and submit binding bids.
  - Acknowledge approved POs and input confirmed shipping dates.
  - Submit Advance Shipping Notices (ASNs) with tracking numbers.
  - Upload electronic invoices and inspect real-time payment status.

#### Q44: How do you manage Vendor Disqualification and Blacklisting?
- **Answer**: Configure a vendor status state machine: `Draft` $\to$ `Under Audit` $\to$ `Approved` $\to$ `Warning` $\to$ `Blacklisted`.
  - Setting a vendor to `Blacklisted` instantly blocks all users from creating new RFQs/POs, blocks receipt validation for open orders, and flags existing open AP balances for financial audit.

#### Q45: How do you handle Subcontracting (Outsourced Manufacturing) in P2P?
- **Answer**:
  - Issue Subcontracting PO to partner for finished goods.
  - ERP automatically creates a delivery order shipping raw components from internal warehouse to the Subcontractor's virtual location.
  - Upon receipt of finished product: System automatically consumes the raw materials from the subcontractor's location and records the service fee as landed manufacturing cost.

---

### Section 4: Inventory, Multi-Warehouse Routing & Stock Costing (Q46 – Q60)

#### Q46: Compare Standard Costing, Average Costing (AVCO), and FIFO with concrete balance sheet impacts.
- **Answer**:
  - **Standard Costing**: Fixed predetermined baseline cost set by management. Price variances between actual PO price and standard cost are flushed directly to a **Purchase Price Variance (PPV)** P&L account.
  - **AVCO (Moving Average Cost)**: Recalculates unit cost with every incoming purchase receipt:
    $$\text{New Cost} = \frac{(\text{Current Qty} \times \text{Old Cost}) + (\text{Incoming Qty} \times \text{Incoming Price})}{\text{Current Qty} + \text{Incoming Qty}}$$
    Smooths volatility; simple to manage.
  - **FIFO (First-In, First-Out)**: Tracks discrete inventory valuation layers. Outgoing shipments consume the oldest remaining purchase layer. During inflationary periods, FIFO yields higher asset valuation and higher net income (lower COGS).

#### Q47: What happens under FIFO costing if inventory dips into negative balance due to operational timing errors?
- **Answer**: Negative inventory breaks strict FIFO valuation because there is no existing cost layer to consume!
  - **ERP Handling**: The system creates a temporary negative layer evaluated at the last known standard cost. When a subsequent purchase receipt arrives, the ERP executes a **Cost Realignment / Retroactive Valuation Adjustment**, posting variance adjustments to the COGS account to true up historical distortions. Best practice: Strictly disable negative inventory via configuration!

#### Q48: Explain Push Rules vs Pull Rules in supply chain routing.
- **Answer**:
  - **Pull Rule (Demand-Driven / Make-to-Order)**: A demand at location $B$ (e.g., Customer Order at Output) triggers a requirement upstream at location $A$ (e.g., Packing). The movement only occurs *because* a downstream need requested it.
  - **Push Rule (Supply-Driven / Batch Movement)**: Goods arriving at location $A$ (e.g., Goods Receipt) automatically generate an immediate move to location $B$ (e.g., Quality Control) regardless of downstream demand.

#### Q49: What is Cross-Docking and how do you configure it in an ERP?
- **Answer**: Incoming inbound shipments are transferred directly to outbound shipping docks without ever entering long-term storage locations.
  - **Configuration**: Setup a specialized Cross-Dock warehouse route: Receipts $\to$ Cross-Dock Location $\to$ Outbound Delivery. Triggered when an incoming shipment matches an active, urgent backordered Sales Order. Slashes storage costs and handling labor.

#### Q50: How does Wave Picking differ from Batch Picking and Cluster Picking?
- **Answer**:
  - **Batch Picking**: A picker gathers items for multiple orders simultaneously into a single bin to minimize travel distance (e.g., picking 50 units of Product A for 50 different orders). Requires a secondary sorting phase.
  - **Cluster Picking**: A picker pushes a cart with multiple individual customer totes, sorting items directly into the correct customer order bin at the pick face.
  - **Wave Picking**: Orders are grouped into operational "waves" released at scheduled time windows based on common criteria (carrier departure time, priority shipping tier, warehouse zone).

#### Q51: How do you design an automated Expiry Date / FEFO (First-Expired, First-Out) picking strategy?
- **Answer**:
  - Track three dates per serial/lot: **Manufacture Date**, **Removal Date**, and **End of Life Date**.
  - Configure warehouse removal strategy on product categories to `FEFO`.
  - When a Sales Order delivery slip is reserved, the ORM search query orders available stock quants by `removal_date ASC`. Expired lots past removal date are automatically excluded from reservation queries.

#### Q52: How do you conduct an Annual Physical Inventory Count vs Continuous Cycle Counting?
- **Answer**:
  - **Annual Physical Count**: Warehouse completely halts shipping operations for 48–72 hours. Staff counts every SKU. High operational disruption; large surprise balance sheet write-offs.
  - **Continuous Cycle Counting (ABC Analysis)**: Warehouse operates continuously. Products categorized by value/velocity:
    - Class A (Top 20% value): Counted monthly.
    - Class B (Next 30% value): Counted quarterly.
    - Class C (Remaining 50%): Counted semi-annually.
    Discrepancies are investigated immediately; operations never halt.

#### Q53: What is the difference between an Internal Transfer, a Scrap Order, and an Inventory Adjustment?
- **Answer**:
  - **Internal Transfer**: Moves goods between two internal physical locations (e.g., `WH/Stock/Shelf1` $\to$ `WH/Stock/Shelf2`). Zero balance sheet impact.
  - **Scrap Order**: Moves damaged/expired goods from internal location to a virtual Scrap location. Debits **Scrap Expense (P&L)**, Credits **Inventory Asset**.
  - **Inventory Adjustment**: Reconciles physical count differences with system records. If physical count is lower, debits **Inventory Shrinkage Expense**; if higher, credits **Inventory Gain**.

#### Q54: What is Inter-Company Stock Transfer and how is it reconciled?
- **Answer**: Transferring inventory between two legally distinct subsidiaries owned by the same holding group.
  - Cannot be a simple internal transfer! Must be executed as an arms-length commercial transaction: Subsidiary A sells to Subsidiary B (Inter-company PO $\to$ Inter-company SO).
  - Produces sales revenue and receivable in Sub A; produces purchase cost and payable in Sub B.
  - **Reconciliation**: Group consolidation eliminates inter-company revenue and unrealized inter-company inventory profit margins at period close.

#### Q55: How do you track Kits / Phantom BOMs in warehouse operations?
- **Answer**: A Kit (Phantom BOM) has no physical inventory of its own! It is a commercial packaging concept (e.g., "Computer Bundle" = Monitor + Keyboard + Desktop).
  - In Sales: Rep sells 1 unit of Computer Bundle.
  - In Warehouse: Delivery slip explodes into 1 Monitor, 1 Keyboard, 1 Desktop. Inventory reservations and picking occur strictly on the component level.

#### Q56: How do Putaway Rules optimize warehouse storage efficiency?
- **Answer**: When goods arrive at the receiving dock, Putaway Rules automatically assign the optimal storage location based on:
  - Product category / hazardous material restrictions.
  - Weight and dimensions (heavy pallets assigned to ground-level slots).
  - Velocity / turnover rate (fastest-moving SKUs placed closest to packing stations).

#### Q57: How do you handle Return to Vendor (RTV) for defective components discovered on the assembly line?
- **Answer**:
  1. Assembly worker logs defect $\to$ creates Stock Move from `Manufacturing/WIP` to `WH/Quality Control/RTV`.
  2. Quality inspector approves $\to$ generates outbound RTV delivery slip to supplier.
  3. Validating RTV shipment automatically generates a draft **Vendor Credit Note** in Accounts Payable, crediting Inventory Asset and debiting Accounts Payable.

#### Q58: What is Safety Stock, and what formula balances stockout risk against carrying cost?
- **Answer**: Safety Stock buffers against demand spikes and supplier lead-time delays:
  $$\text{Safety Stock} = Z \times \sigma_{LT} \times D_{\text{avg}}$$
  - $Z$: Service level factor (e.g., $1.65$ for 95% service level, $2.33$ for 99%).
  - $\sigma_{LT}$: Standard deviation of lead time.
  - $D_{\text{avg}}$: Average daily sales volume.

#### Q59: How does Catch Weight Management work in food and meat processing ERPs?
- **Answer**: Products are priced by weight (kg/lbs) but inventoried by discrete packaging units (boxes/carcasses).
  - Example: A butcher receives 10 boxes of beef steaks. Total theoretical weight is 200 kg, but actual weight is 204.3 kg.
  - The ERP must track **Dual Units of Measure (Dual UoM)**: stock quantity = 10 boxes, financial valuation and customer invoicing = 204.3 kg.

#### Q60: How do you configure 3PL (Third-Party Logistics) integration via webhook/API?
- **Answer**:
  - ERP triggers webhook on SO confirmation $\to$ transmits outbound shipping request to 3PL API.
  - 3PL receives, picks, packs, and ships goods.
  - 3PL fires webhook back to ERP with tracking number, carrier code, and verified pick quantities $\to$ ERP automatically validates internal delivery order and generates customer invoice.

---

### Section 5: Financial Accounting, Multi-Currency, ASC 606 & Consolidation (Q61 – Q75)

#### Q61: Explain the fundamental double-entry balance sheet equation and how ERP journal entries enforce it.
- **Answer**:
  $$\text{Assets} = \text{Liabilities} + \text{Equity}$$
  Every financial transaction in the ERP creates a Journal Entry consisting of at least two journal items. The system strictly validates:
  $$\sum \text{Debits} \equiv \sum \text{Credits}$$
  If debits do not equal credits down to the micro-cent, the database transaction is rejected with an unposted balancing error.

#### Q62: What is the difference between Cash Accounting and Accrual Accounting?
- **Answer**:
  - **Cash Accounting**: Revenue is recognized only when cash enters the bank; expenses are recognized only when cash leaves. Only viable for micro-businesses.
  - **Accrual Accounting (GAAP/IFRS standard)**: Revenue is recognized when earned (delivery of goods/services); expenses are recognized when incurred (receipt of goods/services), matching revenues with their associated costs regardless of cash timing.

#### Q63: How does Foreign Exchange (FX) Unrealized vs Realized Gain/Loss work in ERP accounting?
- **Answer**:
  - **Realized Gain/Loss**: Occurs when an invoice in foreign currency is actually settled via bank payment at a different exchange rate than when invoiced. The difference is permanently posted to the P&L **Realized Exchange Gain/Loss** account.
  - **Unrealized Gain/Loss (Balance Sheet Revaluation)**: At monthly close, open unpaid AR and AP balances in foreign currencies are revalued against the closing exchange rate on the last day of the month:
    - Debit/Credit: Open AR/AP balance.
    - Offset: **Unrealized Exchange Gain/Loss (P&L)**.
    - System automatically posts a reversing journal entry on Day 1 of the subsequent month.

#### Q64: What is Inter-Company Elimination in financial consolidation?
- **Answer**: When consolidating financial statements across parent and subsidiary companies, transactions between internal legal entities must be eliminated to prevent artificial balance sheet and revenue inflation:
  1. Eliminate inter-company sales and purchase costs.
  2. Eliminate inter-company receivables and payables.
  3. Eliminate unrealized inventory profit margin (profit booked by Sub A on goods sitting unsold in Sub B's warehouse).
  4. Eliminate inter-company loans and dividend distributions.

#### Q65: How do you design an Asset Depreciation Schedule (Straight-Line vs Declining Balance)?
- **Answer**:
  - **Straight-Line**:
    $$\text{Annual Depreciation} = \frac{\text{Acquisition Cost} - \text{Salvage Value}}{\text{Useful Life (Years)}}$$
    Posts identical monthly depreciation entries: Debit **Depreciation Expense (P&L)**, Credit **Accumulated Depreciation (Contra-Asset)**.
  - **Declining Balance**: Accelerates depreciation into earlier years using a multiple (e.g., 200% double-declining balance):
    $$\text{Period Depreciation} = \text{Book Value at Start of Year} \times \left(\frac{\text{Rate}}{\text{Useful Life}}\right)$$

#### Q66: What is the functional role of a Suspense Account during bank reconciliation?
- **Answer**: A temporary clearing account used when cash flows into or out of the bank statement, but the underlying customer invoice, vendor bill, or tax payment cannot immediately be identified. When the accountant later uncovers the corresponding invoice, a balancing journal entry moves the balance from Suspense to Accounts Receivable.

#### Q67: How does an ERP handle Automated Bank Feeds and Statement Reconciliation rules?
- **Answer**:
  - Connects to bank via Plaid, Yodlee, or EBICS to pull daily MT940 / CAMT.053 statement files.
  - Runs hierarchical automated reconciliation rules:
    1. Match exact invoice reference / payment communication token.
    2. Match partner name + exact transaction amount within date window ($\pm 3$ days).
    3. Match recurring bank fee transactions to pre-configured expense accounts.
  - Matched items are auto-reconciled; unmatched items are queued for accountant review.

#### Q68: What is Deferred Revenue and Accrued Revenue?
- **Answer**:
  - **Deferred Revenue (Unearned Revenue)**: Cash has been received from customer, but goods/services have not yet been delivered (e.g., annual SaaS subscription paid upfront). Recorded as a **Current Liability**.
  - **Accrued Revenue**: Services/goods have been delivered, but the customer has not yet been invoiced (e.g., milestone consulting completed at month-end). Recorded as an **Asset**.

#### Q69: How do you manage Statutory Tax Compliance (VAT / GST / Sales Tax) across multiple jurisdictions?
- **Answer**:
  - **Fiscal Positions**: Mapping rules that automatically replace standard domestic tax rates with export tax rates (0% zero-rated) or regional state taxes based on customer shipping address.
  - **Reverse Charge Mechanism**: In B2B cross-border transactions, the buyer accounts for both input and output VAT, netting out to zero cash liability.
  - **Tax Audit Report**: ERP compiles period tax return reports showing Net Base Amount, Tax Collected, Tax Paid, and Net Payable/Refundable.

#### Q70: What is a Fiscal Year-End Closing Sequence in an enterprise ERP?
- **Answer**:
  1. Complete bank reconciliation and post all pending depreciation/accruals.
  2. Run foreign currency revaluation.
  3. Post inventory physical count variance adjustments.
  4. Run automated inter-company eliminations.
  5. Generate Trial Balance, Profit & Loss, and Balance Sheet reports for external audit.
  6. Execute Year-End Closing Entry: Net income/loss from P&L revenue and expense accounts is transferred to **Retained Earnings (Equity)**, zeroing out all P&L accounts for Day 1 of the new fiscal year.
  7. Lock accounting dates to prevent unauthorized back-dated entries.

#### Q71: How do you handle Petty Cash management and reconciliation?
- **Answer**:
  - Established as an Imprest fund (e.g., \$500 fixed limit).
  - Handled as a distinct cash journal in the ERP.
  - Custodian uploads receipt vouchers for minor office purchases.
  - When balance drops below threshold, custodian requests replenishment: system posts expenses to appropriate P&L accounts and transfers cash from Main Bank to Petty Cash journal.

#### Q72: What is the difference between a Balance Sheet, P&L (Income Statement), and Cash Flow Statement?
- **Answer**:
  - **Balance Sheet**: Snapshot of financial health at a single point in time (Assets, Liabilities, Equity).
  - **P&L**: Measures profitability over a duration of time (Revenue $-$ COGS $-$ OPEX $=$ Net Income).
  - **Cash Flow Statement**: Tracks actual movement of liquid cash across three categories: Operating Activities, Investing Activities, and Financing Activities. Explains why a company can show high net profit on P&L while running out of cash in the bank!

#### Q73: How does Cost Center / Analytic Accounting differ from General Ledger (Financial) Accounting?
- **Answer**:
  - **Financial Accounting (GL)**: Statutory, legal compliance according to GAAP/IFRS. Rigid Chart of Accounts driven by external tax and auditing requirements.
  - **Analytic / Cost Center Accounting**: Internal managerial accounting. Tags operational transactions with multi-dimensional dimensions (Department, Project, Product Line, Sales Territory) without altering legal balance sheet entries. Enables departmental P&L budgeting and project profitability analysis.

#### Q74: How do you configure Credit Card Payment Surcharges compliant with local regulations?
- **Answer**:
  - Setup payment acquirer surcharge rules: apply fixed fee (e.g., \$0.30) + variable percentage (e.g., 2.9%).
  - Ensure local legal compliance (surcharges banned under EU PSD2; capped in California/UK).
  - In ERP: Surcharge automatically adds a dedicated non-taxable or taxable fee line item to the sales order prior to checkout payment capture.

#### Q75: How do you design an ERP Disaster Recovery (DR) and Business Continuity Plan for financial databases?
- **Answer**:
  - **RPO (Recovery Point Objective)**: Maximum acceptable data loss duration (target: $\le 1$ minute). Enforced via PostgreSQL synchronous streaming replication to a standby regional node with continuous WAL archiving to Amazon S3.
  - **RTO (Recovery Time Objective)**: Maximum acceptable system downtime (target: $\le 15$ minutes). Enforced via automated DNS failover (Route 53) pointing to the standby hot node with pre-warmed application containers.
  - **Quarterly DR Drill**: Mandatory unannounced simulated failover testing during non-peak windows to validate backup integrity and executive communication chains.

---

> 🧭 **Navigation**: [⬅️ Previous: Volume 3: 100 Senior AI Questions](03-senior-ai-100-questions.md) | [🏠 500 Master Questions Bank Index](README.md) | [➡️ Next: Volume 5: 75 Odoo Lead Questions](05-odoo-technical-lead-75-questions.md)
