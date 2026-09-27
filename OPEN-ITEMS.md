# Books — open items

Single self-contained `index.html`, house style from `../PROJECT-HANDOFF.md`. Storage: one
`localStorage` key, `books.v1`, schema 1.

## To do

Live at https://mrholdingsworth.github.io/Financials/ (repo `mrholdingsworth/Financials`, Pages from
`main` / root). The path is case-sensitive.

Picked from the gap review on 2026-09-26. Grouped by area; order within a group is not priority.

### Controls and data integrity
- **Period close / lock date.** A "books closed through" date. Posting, editing or deleting before
  it is blocked unless deliberately unlocked. Today nothing protects a year that has already been
  reported.
- **Audit trail / change log.** Record each post, edit and delete, with when it happened and the
  before/after values. At present an edit silently overwrites.
- **Void instead of delete.** A voided entry stays in the journal, marked void, with a reason.
- **Sequential entry numbering with gap detection.** Entry and account IDs share one counter
  (`root.seq`), so entry numbers skip. Give entries their own unbroken sequence and report any gaps.
- **Adjusting-entry flag.** Tag each entry as normal, adjusting, reclassification or
  auditor-proposed, and filter and report by tag.
- **Integrity self-check tool.** Rebuild every balance and confirm: the trial balance ties, cash flow
  ties, retained earnings rolls forward, and no entry references a missing account.
- **Duplicate-entry warning.** Same date, amount and accounts as an existing entry. A flag, not a veto.
- **Future-dated entry warning.** For example, a typo'd year like 2062.

### Storage, backup and security
- **Storage-size monitoring.** localStorage tops out at roughly 5 MB. Show usage and warn well before
  the limit, not when a save fails.
- **Automatic backup reminders**, or scheduled local backup files through the File System Access API.
- **Versioned backups.** Keep the last N snapshots in IndexedDB so a bad import can be rolled back.
- **Encryption at rest with a passphrase.** The books currently sit unencrypted in browser storage.
- **Multiple companies / books** in one browser, with a switcher.
- **Backup file checksum**, so a corrupted or hand-edited backup is detected on import.

### Chart of accounts
- **Account hierarchy.** Parent and sub-accounts with roll-up totals.
- **More subtypes:** unearned revenue, accrued interest, allowance for doubtful accounts, sales
  discounts/returns (contra revenue), treasury stock, dividends payable, deferred tax, intangibles
  and accumulated amortisation, right-of-use assets, lease liabilities. Each one needs a statement
  placement and a cash flow bucket decided.
- **Account descriptions / notes** saying what belongs in the account.
- **Account merge.** Move all history from one account into another, then retire the first.
- **Tax-line mapping per account** (Schedule C, 1120-S, 1065 lines).
- **Normal-balance override** for unusual accounts.

### Opening balances and setup
- **Opening balance entry wizard.** Enter a trial balance as of a start date.
- **Mid-year start.** Year-to-date opening balances for revenue and expense accounts.

### Journal entry workflow
- **Line-level memos.** Today there's one memo per entry.
- **Attachments / source documents** (receipts, invoices, statements) linked to entries and stored in
  IndexedDB. Decide how they travel in backups — size matters here.
- **Copy / duplicate an entry.**
- **Keyboard-driven entry.** Enter adds a line, and account selection gets type-ahead search in place
  of the long select.
- **Batch entry grid** for keying many entries quickly.
- **Recurring: warn before a schedule ends**, not only after. Settle how early once there's a real
  schedule to judge by.
- **Recurring: variable amounts**, such as loan amortisation where the interest/principal split
  changes each payment.
- **Recurring: skip a single occurrence.**
- **Recurring: a "last day of month" rule** that isn't tied to the anchor's day.

### Cash and banking
- **Bank reconciliation.** Mark items cleared, track outstanding cheques and deposits, keep a
  reconciliation report per statement date, and lock reconciled items.
- **Transfers between cash accounts** as a first-class action.
- **Negative cash warning.** Suggest reclassifying an overdraft as a liability on the balance sheet.
- **Petty cash handling.**
- **Cheque register / cheque numbering.**

### Receivables and payables
- **Customers and vendors** as records, attached to entry lines.
- **Invoices and bills** that post to AR/AP.
- **AR and AP aging reports** (current / 30 / 60 / 90+).
- **Allowance for doubtful accounts / bad-debt write-offs.**

### Fixed assets
- **Fixed asset register:** cost, in-service date, useful life, method, salvage value.
- **Automatic depreciation schedules** (straight line, declining balance, units of production) that
  generate the entries. Likely builds on the recurring engine.
- **Disposals with gain/loss calculation**, split out correctly in the cash flow statement. Today a
  disposal reads as an investing inflow plus a mis-signed depreciation line, and the Chart of
  accounts hint warns about it.
- **Capitalisation threshold** setting, warning when an expense line exceeds it.

### Reporting
- **Comparative P&L:** prior year, prior period, and variance in amount and %. The balance sheet
  already has a comparative column.
- **Monthly and quarterly P&L columns** across the fiscal year.
- **Statement of changes in equity** — the fourth statement.
- **General ledger detail report**, exportable, for a period across all accounts.
- **Journal report** of all entries in a date range, for printing or audit.
- **Custom date ranges.** Reports are tied to fiscal year to date today.
- **Budget vs actual**, with budgets entered per account per month.
- **Reclassification of contra balances on statements**, e.g. AR with a credit balance or customer
  deposits.

### UX and accessibility
- **Global search** across entries, accounts and amounts.
- **Keyboard shortcuts**, e.g. N for new entry and / to search. Esc already closes modals.
- **Help / glossary** explaining each subtype and where it lands on the statements.
- **Screen-reader labels** on icon-only buttons, and focus trapping inside modals.
- **Light theme option.** The house style is dark-only by design, so this is a deliberate departure.
- **Drill-down.** Click a statement line to open that account's ledger for the period.

### Engineering hygiene
- **Automated self-test page.** Load the demo books and assert every hand-checked total (70,070
  total assets, 19,040 YTD net income, 85,330 trial balance, 47,470 ending cash…). This is the
  check that has been run by hand after each change.
- **Schema migration test** against old backup files.
- **Performance check at 10,000+ entries.** `sums()` rescans every entry for each report.
- **Service worker for offline use / install as an app.**

## Versions

The version shown in the footer lives in `index.html`. It is separate from `SCHEMA`, which changes
only when the stored shape needs a migration.

**Currently 0.9, pre-release.** Steve will say when to move to **1.0**, the initial release. Don't
bump the version on your own before then.

Pre-release work so far: the journal, chart of accounts, ledger and backup; the three statements;
modal tools; an empty starting chart of accounts; an adjustable fiscal year end; the trial balance
(debit/credit and single-column); recurring entries; PDF/Excel/CSV export; the "B" tab icon; and the
version footer with its data-storage disclaimer.

## Ideas, not started

- CSV import of bank transactions, turned into draft entries.
- Recurring: a "no end" option. Left out on purpose — every schedule ends, so that each one
  eventually comes back for review. Revisit if open-ended schedules become a nuisance to extend.
- Split out the current portion of long-term debt in the balance sheet and cash flow statement.

Also reviewed on 2026-09-26 and left off the To do list: reversing entries; undo; import
validation beyond what exists; cloud sync; enforcing account number ranges by type; starter chart
templates; conversion from other software; entry templates; percentage splits; approval workflow;
bank CSV/OFX/QFX import; payment application; customer/vendor statements; 1099 tracking; credit
memos, refunds and deposits; book vs tax depreciation; accrual, prepaid and deferral schedules;
sales tax, payroll and inventory; KPI tiles, direct-method cash flow, notes, PDF cover page and
contents, cash-basis toggle, classes/departments, jobs and charts; all audit-support tooling
(lead sheets, JE testing, materiality, sampling, audit data exports); tax mapping beyond the
per-account line; multi-currency and consolidation; mobile layout pass; onboarding checklist.

## Decided / done

- **Amounts are stored as integer cents.** Float dollars would drift over a year of postings.
  Inputs take at most 2 decimals and reject anything more, so a typo never gets silently rounded.
- **Statements use 2dp and put negatives in parentheses.** That's a departure from the handoff's
  whole-units rule for account-scale figures. Books have to tie to the cent, and parentheses are
  the accounting convention.
- **The year closes itself.** Retained earnings = RE-account balance + all net income before Jan 1
  of the as-of year. No closing entries get posted. If the user posts one by hand, that year's P&L
  reads zero; the balance sheet footnote warns about this.
- **The subtype is the only classification.** Balance-sheet section and cash-flow bucket both come
  from it: cash → cash; current asset/liability → operating working capital; accumulated
  depreciation → non-cash add-back; fixed/other asset → investing; long-term liability and all
  equity → financing.
- **Cash flow uses the indirect method, computed from balance-sheet changes**, so it ties to cash
  exactly by construction. The "Ties to cash" pill would only fail on imported unbalanced entries.
- **Accounts used in entries can't be deleted**, only deactivated. Deactivating hides the account
  from the entry picker; history is unaffected.
- **Reports default to as-of today**, covering the fiscal year to date. There are quick buttons for
  prior fiscal year ends.
- **Tools open as modals** over a blurred page, and close with ×, Esc or a backdrop click. The
  entry draft survives closing, so a stray click loses nothing. (Pass 1, item 1.)
- **The chart of accounts starts empty.** The sample chart now ships only with the demo, which is
  offered only when the books are completely empty. Erase all books clears accounts too, but keeps
  the company name and fiscal year end. (Pass 1, item 2.)
- **The fiscal year end is a month** (`root.fyEnd`, 1–12, default 12). The year always ends on that
  month's last day, which comes from `new Date(y, m, 0)`, so a February year end is the 28th or 29th
  by the calendar. A fiscal year is named for the calendar year it ends in. All boundary math is
  done on YYYY-MM-DD strings, so there are no time zone or DST shifts. `validDate()` rejects dates
  that don't exist (2027-02-29, 2026-04-31), including on import. Tested: 2000/2028 leap,
  2100/2027 not, an entry on 2028-02-29 lands in FY2028 and 2028-03-01 starts FY2029. We chose
  "last day of month" over an arbitrary day, so a Feb 28 year end never has to guess about leap years.
  (Pass 1, item 3.)
- **Trial balance view** (pass 2, item 1). A "3 statements | Trial balance" switch sits in the
  report header, and the choice is saved in `root.view`. Balance-sheet accounts show as of the
  date. Income and expense accounts show fiscal-year-to-date. Earlier years' income is folded into
  retained earnings (or a synthetic line if there's no RE account), so the trial balance and the
  statements always read the same books. Zero balances are hidden.
- **Recurring entries** (pass 2, item 2).
  - Setup: a "Make this recurring" checkbox on new entries opens the schedule fields — every
    N weeks, months, quarters or years, stopping after N times or on a date. The entry you post
    is occurrence 1. A live preview lists the dates and says how many are already due.
  - Posting: an occurrence posts only once its date arrives — on load, or when the tab regains
    focus — as an ordinary entry tagged `rec`, marked ↻ in the journal. We chose never to
    pre-post future entries, so the books hold only what has happened. A backdated schedule
    catches up immediately, with a confirm step if more than 12 occurrences are due.
  - Dates: occurrence k = step(anchor, k × every), always computed from the anchor, so
    month-end schedules clamp without drifting (Jan 31 → Feb 28 → Mar 31). Week steps go
    through `Date.UTC`, so DST can't shift them.
  - Review: when a schedule completes, its status becomes `ended`. The New entry button then
    gets an amber ring and a count badge, and clicking it opens the Recurring tab. From there
    you can Extend it (pre-filled with the same run again) or mark it reviewed, which archives
    it. Active schedules can be edited, stopped or deleted.
  - Edits: editing a schedule changes only future occurrences. Only a changed next date or
    rhythm re-anchors it. Posted entries are never rewritten.
  - Accounts: an account a live schedule posts to can't be deleted.
- **Trial balance single-column view** (pass 3). Debits are positive and credits negative, with a
  minus sign rather than parentheses — confirmed as correct, because it's the shape audit
  software imports. The choice is saved in `root.tbMode`.
- **Export replaces Print** (pass 3), offering PDF, Excel (.xlsx) and CSV of whatever view is
  showing, including the single-column trial balance.
  - Single source: each statement is built once as data (`balanceSheet()` etc. return rows), and
    `stmtHtml()`, `toPDF()`, `toXLSX()` and `toCSV()` all read that same object. This closes off
    the "two renderers drift" trap.
  - No libraries, in keeping with the house rule. The .xlsx is a store-only zip, with the CRC and
    headers written by hand. The PDF uses built-in Helvetica / Helvetica-Bold with WinAnsi
    encoding, so no fonts are embedded. Right-aligned figures use the standard AFM glyph widths.
  - The trial balance CSV and xlsx are import-shaped: one header row (Account number, Account
    name, Type, then the amount columns), one row per account, and figures as plain `-1234.56`.
    The xlsx adds a Totals line after a blank row. The statements' CSV is a readable stacked layout
    instead.
  - Verified: the zip CRCs, the EOCD, and every XML part parsing; the PDF xref offsets and stream
    lengths; and a rendered PDF checked by eye.
  - Not verified: opening the .xlsx in Excel itself — there's no Excel on this machine.
- **Red for negatives:** on each cash flow section subtotal, every P&L subtotal, and every equity
  line (draws are naturally red). Line items elsewhere stay neutral. Positive net income stays green,
  and other positive grand totals stay gold. (Pass 1, item 6.)
