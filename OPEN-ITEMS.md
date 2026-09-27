# Books — open items

Single self-contained `index.html`, house style from `../PROJECT-HANDOFF.md`. Storage: one
`localStorage` key, `books.v1`, schema 1.

## To do

- Nothing open. Live at https://mrholdingsworth.github.io/Financials/ (repo
  `mrholdingsworth/Financials`, Pages from `main` / root). The path is case-sensitive.

## Ideas, not started

- CSV import of bank transactions, turned into draft entries.
- A comparative prior-year column on the P&L. The balance sheet already has one.
- Recurring: a "no end" option. Left out on purpose — every schedule ends, so that each one
  eventually comes back for review. Revisit if open-ended schedules become a nuisance to extend.
- Recurring: warn a few weeks before a schedule finishes, not only after. Settle it once there's a
  real schedule to see how early the warning would need to come.
- Split out the current portion of long-term debt, and gains/losses on asset disposals, in the cash
  flow statement.

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
