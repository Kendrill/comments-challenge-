# NovaTech — Procure-to-Pay (P2P) Reference Process

## Internal Regulation NVT-PROC-2024-001

**Effective date:** 2024-01-01
**Approved by:** VP of Procurement, NovaTech
**Scope:** All procurement activities across Company Codes 1000 and 2000

---

## 1. Mandatory Process Steps (in order)

```
Purchase Requisition Created (ME51N)
        |
        v
Purchase Requisition Released (ME54N)
        |
        v  [if Net_Value > 500,000 RUB]
   RFQ Created (ME41)
        |
        v
   Quotation Received (ME47)
        |
        v
Purchase Order Created (ME21N)
        |
        v
Purchase Order Released (ME28)
        |
        v
Goods Receipt Posted (MIGO, mvt 101)
        |
        v
Invoice Receipt Posted (MIRO)
        |
        v
Invoice Verified (MIR7)
        |
        v
Payment Run Executed (F110)
        |
        v
Payment Completed (F110)
```

## 2. SLA Requirements (per step)

| Step | SLA | Measurement |
|------|-----|-------------|
| PR Created → PR Released | **<= 3 business days** | From PR creation to first release |
| PR Released → PO Created | **<= 2 business days** | Buyer must create PO promptly |
| RFQ Created → Quotation Received | **<= 7 business days** | Supplier response time |
| PO Created → PO Released | **<= 3 business days** | Approval cycle |
| PO Released → Goods Receipt | **<= 14 calendar days** | Delivery lead time |
| Goods Receipt → Invoice Receipt | **<= 5 business days** | Supplier invoicing |
| Invoice Receipt → Invoice Verified | **<= 5 business days** | AP team verification |
| Invoice Verified → Payment Executed | **<= payment terms** | Net 30 or Net 60 per contract |

## 3. Compliance Rules

### Rule 1: No Maverick Buying
Every Purchase Order (ME21N) **MUST** be preceded by a Purchase Requisition (ME51N) and its release (ME54N). Direct PO creation without PR is a compliance violation.

**Violation indicator:** Activity "Purchase Order Created" without prior "Purchase Requisition Created" in the same case.

### Rule 2: No Retroactive Purchase Orders
Goods Receipt (MIGO) **MUST NOT** occur before Purchase Order Release (ME28). Creating a PO after goods have already been received is prohibited.

**Violation indicator:** "Goods Receipt Posted" timestamp is earlier than "Purchase Order Created" timestamp in the same case.

### Rule 3: Mandatory Competitive Bidding
For all procurement with Net_Value **> 500,000 RUB**, a formal RFQ process is mandatory. The case must include "RFQ Created" (ME41) and "Quotation Received" (ME47) steps.

**Violation indicator:** Cases with Net_Value > 500,000 RUB that skip directly from "PR Released" to "PO Created" without RFQ/Quotation steps.

### Rule 4: Three-Way Match Required
Invoice verification must confirm a three-way match:
- Purchase Order amount matches
- Goods Receipt quantity matches
- Invoice amount matches

Invoices failing the match must be blocked (MRBR) and resolved before payment.

**Violation indicator:** "Invoice Blocked" (MRBR) activity indicates a match failure.

### Rule 5: No Duplicate Invoices
Each Purchase Order should have at most **one** Invoice Receipt. Multiple invoices for the same case may indicate duplicate payment risk.

**Violation indicator:** More than one "Invoice Receipt Posted" for the same Case_ID.

### Rule 6: Goods Receipt Integrity
Goods Receipt reversals (MIGO mvt type 102) must not exceed **5%** of total GR transactions. High reversal rates indicate quality control issues with vendors.

**Violation indicator:** "Goods Receipt Reversal" activity in the case.

### Rule 7: Process Completion
All procurement cases must reach "Payment Completed" within **90 calendar days** of PR creation. Cases that stall without completion ("zombie cases") must be escalated.

**Violation indicator:** Cases without "Payment Completed" activity, or cases where the last activity timestamp is > 30 days old without completion.

## 4. Expected Metrics (targets)

| Metric | Target |
|--------|--------|
| Average end-to-end lead time | **<= 35 calendar days** |
| PR Approval rate within SLA | **>= 90%** |
| Maverick Buying rate | **<= 2%** |
| Retroactive PO rate | **0%** (zero tolerance) |
| Three-way match success rate | **>= 85%** |
| GR Reversal rate | **<= 5%** |
| Process completion rate | **>= 95%** |
| Duplicate invoice rate | **0%** |
