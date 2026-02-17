# FI — Financial Accounting

The Financial Accounting module provides natural language access to SAP FI data and processes: financial documents, account balances, open items, and summaries through conversational interaction.

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| General Ledger (G/L posting, balance) | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Accounts Payable (AP) | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Accounts Receivable (AR) | :white_check_mark: | :white_check_mark: | — |
| Fixed Assets (Asset master, depreciation) | :white_check_mark: | — | — |
| Reporting FI (trial balance, intercompany) | :white_check_mark: | — | — |

---

## Use Cases

### General Ledger — Post manual journal entry

!!! example "User prompt"
    *"Post a journal entry in company code 1000 with debit to account 400000 (general expenses) for 5,000 EUR and credit to account 113100 (bank). Text: 'Consulting service payment'."*

**What happens:** Posts the G/L document in the company code with the given accounts, amounts, and text. Returns the created document number.

---

### General Ledger — Display financial document

!!! example "User prompt"
    *"Display financial document 1400000001 in company code 1000 for fiscal year 2026 with all line items, amounts, and assignments."*

**What happens:** Retrieves document header and line items; amounts and assignments match FB03.

---

### General Ledger — Reverse document

!!! example "User prompt"
    *"Reverse document 1400000001 in company code 1000 with reversal reason 01 and posting date today. Show the reversal document created."*

**What happens:** Posts the reversal document with the given reason and posting date; returns the reversal document.

---

### General Ledger — G/L account balance by period

!!! example "User prompt"
    *"Show balance for G/L account 113100 in company code 1000 for periods 1 to 12 of fiscal year 2025, with monthly breakdown of debit, credit, and cumulative balance."*

**What happens:** Returns balance by period (debit, credit, cumulative balance); values align with FAGLB03.

---

### Accounts Payable — Post supplier invoice (no PO)

!!! example "User prompt"
    *"Post supplier invoice from vendor 100045 for 12,500 EUR with allocation to expense account 476000 (external services) in company code 1000. Invoice date today, reference: FAC-2026-001."*

**What happens:** Posts the AP invoice and creates the open item; returns the document number.

---

### Accounts Payable — Open items

!!! example "User prompt"
    *"Show all open items for vendor 100045 in company code 1000, sorted by due date. Indicate which are overdue and the total outstanding."*

**What happens:** Returns open AP line items for the vendor and company code; totals and aging match FBL1N.

---

### Accounts Payable — Aging

!!! example "User prompt"
    *"Generate an accounts payable aging report for company code 1000 with buckets 0-30/31-60/61-90/>90 days. Show top 10 vendors by overdue amount and total per bucket."*

**What happens:** Returns AP aging by bucket, top 10 vendors by overdue amount, and totals per range.

---

### Accounts Payable — Clear open items

!!! example "User prompt"
    *"Clear items for vendor 100045 in company code 1000: invoice 5100000234 against payment 1500000567. Show the clearing document."*

**What happens:** Clears the selected items and returns the clearing document.

---

### Accounts Receivable — Post customer invoice (no SD)

!!! example "User prompt"
    *"Post customer invoice to customer 200100 for 25,000 EUR with allocation to revenue account 700000 in company code 1000. Invoice date today, reference: VENTA-2026-001."*

**What happens:** Posts the AR invoice and creates the open item; returns the document number.

---

### Accounts Receivable — Open items

!!! example "User prompt"
    *"Show all open items for customer 200100 in company code 1000. Include due date, amount, days overdue, and dunning level."*

**What happens:** Returns open AR line items; data matches FBL5N.

---

### Accounts Receivable — Aging

!!! example "User prompt"
    *"Generate an accounts receivable aging report for company code 1000 with buckets 0-30/31-60/61-90/>90 days. Show top 10 customers by outstanding balance and total per bucket."*

**What happens:** Returns AR aging by bucket, top 10 customers by balance, and totals per range.

---

### Accounts Receivable — Post incoming payment and clear

!!! example "User prompt"
    *"Post incoming payment from customer 200100 for 25,000 EUR to bank account 113100. Clear against invoice VENTA-2026-001. Show clearing document."*

**What happens:** Posts the incoming payment and clears it against the invoice; returns the payment and clearing documents.

---

### Fixed Assets — Display asset

!!! example "User prompt"
    *"Show the complete fixed asset card for asset 10000001-0 in company code 1000: description, asset class, cost center, capitalization date, acquisition value, accumulated depreciation, and net book value."*

**What happens:** Returns full asset master and depreciation data; values match AS03.

---

### Fixed Assets — Depreciation simulation

!!! example "User prompt"
    *"Simulate depreciation for asset 10000001-0 in company code 1000 for the next 12 months. Show monthly depreciation, cumulative, and residual value."*

**What happens:** Returns projected depreciation for the next 12 months (monthly amount, cumulative, residual value).

---

### Reporting FI — Trial balance

!!! example "User prompt"
    *"Generate the trial balance for company code 1000 for January 2026. Show by account: opening balance, debit movements, credit movements, and closing balance."*

**What happens:** Returns trial balance by account with opening balance, debit/credit movements, and closing balance.

---

### Reporting FI — Intercompany reconciliation

!!! example "User prompt"
    *"Show intercompany balances between company code 1000 and company code 2000. Identify unreconciled differences and list the documents that cause them."*

**What happens:** Returns intercompany balances and flags unreconciled differences with originating documents.

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_JOURNALENTRYITEMBASIC_SRV` | Journal entry line items | ECC 6.0+ |
| `API_FINANCIALDOCUMENT_SRV` | Financial documents | S/4 1909+ |
| `API_OPEN_ITEMS_SRV` | Open items AP/AR | ECC 6.0+ |
| `API_GLACCOUNTBALANCE_SRV` | G/L account balances | S/4 1909+ |
| `API_SUPPLIERINVOICE_PROCESS_SRV` | Supplier invoices | S/4 1909+ |
| `API_FIXEDASSET_SRV` | Fixed assets | S/4 1909+ |
| `BAPI_ACC_DOCUMENT_POST` / `BAPI_ACC_DOCUMENT_GET` / `BAPI_ACC_DOCUMENT_REV_POST` | Document post/display/reverse (RFC) | ECC 6.0+ |

!!! note "Availability"
    Services may vary by SAP version and activated OData. Confirm with your Basis team.

---

## Limitations

!!! warning "Current Limitations"
    - All queries respect SAP authorizations; users only see data they are allowed to access in SAP.
    - Automatic payment run (F110) is not offered via ABA (requires manual approval in SAP).
    - Multi-currency amounts show in document currency with local equivalent where available.
