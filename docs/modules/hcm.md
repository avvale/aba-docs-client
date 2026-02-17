# HCM — Human Capital Management

The Human Capital Management module provides natural language access to employee master data, organizational structure, time management, payroll results, training, and HR analytics (headcount, turnover, contracts, absenteeism, vacancies).

## Scope

| Area | Read | Create | Modify |
|------|:----:|:------:|:------:|
| Employee Master Data | :white_check_mark: | — | — |
| Organizational Structure | :white_check_mark: | — | — |
| Time Management (quotas, absences) | :white_check_mark: | — | — |
| Time Recording (CATS) | :white_check_mark: | :white_check_mark: | — |
| Payroll Results | :white_check_mark: | — | — |
| Training (planned/completed) | :white_check_mark: | — | — |
| Reporting HR (turnover, payroll mass, contracts, absenteeism, vacancies) | :white_check_mark: | — | — |
| Recruitment | :material-clock-outline: | — | — |
| Travel Management | :material-clock-outline: | — | — |

---

## Use Cases

### Employee Master — Display employee

!!! example "User prompt"
    *"Show employee card for employee 10023: name, date of birth, address, company code, personnel area, subdivision, position, cost center, seniority date, and status."*

**What happens:** Returns employee master data (basic infotypes): name, DOB, address, company code, personnel area, subdivision, position, cost center, seniority date, and status; aligns with PA20.

---

### Employee Master — Search by name/department

!!! example "User prompt"
    *"Search for employees with last name 'García' in IT division of company code 1000. Show position, location, email, and phone extension."*

**What happens:** Searches employees by partial name and org unit; returns position, location, email, and phone extension.

---

### Employee Master — Position change history

!!! example "User prompt"
    *"Show position history for employee 10023: dates of each change, position, organizational unit, reason for change, and salary variation if applicable."*

**What happens:** Returns employment history (IT0001): dates, position, org unit, reason for change, and salary variation when available. Depends on exposed infotype history.

---

### Org Structure — Org chart

!!! example "User prompt"
    *"Show the org chart of the Finance department (org unit 50001000) in company code 1000: manager, positions, incumbents, and vacancies."*

**What happens:** Returns org structure for the unit: manager, positions, incumbents, and vacant positions. Availability may depend on PPOME/org APIs (🟡 Media).

---

### Org Structure — Headcount by department

!!! example "User prompt"
    *"Genera un reporte de headcount por departamento en sociedad 1000 a fecha de hoy. Muestra: total activos, altas del mes, bajas del mes, y ratio temporal/indefinido."*

**What happens:** Returns headcount by department: total active, hires and leavers in the month, and temporary/permanent ratio.

---

### Org Structure — Vacancies by department

!!! example "User prompt"
    *"List open vacancies by department in company code 1000: position, description, date since vacant, hierarchical level, and if it is in recruitment process."*

**What happens:** Returns open vacancies by department with position, description, vacancy date, level, and recruitment-in-progress flag. Depends on org/position data (PPOME).

---

### Time Management — Vacation balance

!!! example "User prompt"
    *"Show vacation balance for employee 10023 for 2026: days assigned, taken, remaining to take, and deadline."*

**What happens:** Returns time quotas (PA2006): assigned, taken, remaining, and deadline; aligns with PA61. Depends on time schema and exposed APIs.

---

### Time Management — Team absences this week

!!! example "User prompt"
    *"Show planned absences for this week in the IT department of company code 1000: employee, absence type (vacation/illness/training), dates, and coverage."*

**What happens:** Returns planned absences for the org and period with employee, absence type, dates, and coverage; supports team planning (PT_BAL00).

---

### Time Recording — CATS (working time)

!!! example "User prompt"
    *"Record 8 hours of work for employee 10023 today: 6 hours on internal order IO-300001 and 2 hours on training. Activity type: normal."*

**What happens:** Posts time sheet (CATS) for the employee: hours per day and activity (internal order, training, etc.). Employee must have CATS profile. Creation depends on exposed time-recording APIs.

---

### Payroll — Last payslip

!!! example "User prompt"
    *"Show the latest payroll result for employee 10023: gross salary, deductions (income tax, social security), benefits in kind, net pay, and bank account for payment."*

**What happens:** Returns latest payroll result (gross, deductions, net, bank account). **Highly sensitive;** access is subject to strict authorizations and may be disabled in many landscapes (🔴 Baja).

---

### Payroll — Payroll mass by department

!!! example "User prompt"
    *"Generate a payroll mass report by department in company code 1000 for fiscal year 2026 YTD: base salary, supplements, employer social security cost, and total labor cost. Compare with budget."*

**What happens:** Returns payroll mass by department with base, supplements, employer social security, and total labor cost; optional budget comparison. **Highly sensitive;** typically restricted to HR/Controlling (🔴 Baja).

---

### Training — Planned training for employee

!!! example "User prompt"
    *"Show planned training for employee 10023 in 2026: course, date, duration, status (completed/pending/in progress), and certification obtained if applicable."*

**What happens:** Returns training plan for the employee: course, date, duration, status, and certification when applicable. Depends on training catalog and assigned plans (LSO).

---

### Reporting HR — Turnover by department

!!! example "User prompt"
    *"Calculate turnover rate by department in company code 1000 for the last 12 months: voluntary, involuntary, and total. Compare with previous period and highlight departments with anomalous turnover."*

**What happens:** Returns turnover rate by department (voluntary, involuntary, total), year-over-year comparison, and highlights anomalous departments.

---

### Reporting HR — Contract expiry alerts (temporary)

!!! example "User prompt"
    *"List all employees with temporary contracts expiring in the next 30 days in company code 1000. Show employee, department, end date, and if there is a renewal request."*

**What happens:** Returns employees with temporary contracts expiring in the next 30 days; includes department, end date, and renewal request status. Supports legal and workforce planning (PA0016).

---

### Reporting HR — Absenteeism ratio and trend

!!! example "User prompt"
    *"Calculate absenteeism ratio by department in company code 1000 for the last 6 months: absence days / working days. Show monthly trend and top 3 departments with highest ratio."*

**What happens:** Returns absenteeism ratio (absence days / working days) by department with monthly trend and top 3 departments; supports HR analytics (PT_BAL00/PA2001).

---

## OData Services Used

| Service | Description | SAP Version |
|---------|-------------|-------------|
| `API_BUSINESS_PARTNER` | Employee / business partner master | S/4 1909+ |
| HCM Infotype services | PA/PT infotypes (master, time, payroll) | ECC 6.0+ |
| Org structure / position services | Organizational units, positions, vacancies | ECC 6.0+ / S/4 |

!!! note "Availability"
    Org chart (PPOME), time quotas (PA61), CATS, payroll results (PCL2), training catalog, and absence analytics depend on which HCM APIs and infotypes are activated in your system. Many scenarios require custom or additional OData or RFC exposure.

---

## Limitations

!!! warning "Current Limitations"
    - **Data sensitivity:** HCM data is highly sensitive. Strict authorization and masking apply. Payroll (payslip, payroll mass) is often **not** exposed via ABA in production; access is restricted to authorized HR/Controlling roles and may be disabled by policy.
    - **API coverage:** Org structure (PPOME), time management (PA61, PT_BAL00), time recording (CATS), payroll (PCL2), and training (LSO) may require additional or custom APIs depending on your SAP version and HR module setup.
    - Self-service (e.g. leave requests, travel) is on the roadmap. Data availability depends on exposed infotypes and services.
