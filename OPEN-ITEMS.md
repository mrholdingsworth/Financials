# Books — open items

Single self-contained `index.html`, house style from `../PROJECT-HANDOFF.md`. Storage: one
`localStorage` key, `books.v1`, schema 1.

## To do

- Nothing open. Live at https://mrholdingsworth.github.io/Financials/ (repo
  `mrholdingsworth/Financials`, Pages from `main` / root). The path is case-sensitive.

## Ideas, not started

- CSV import of bank transactions, turned into draft entries.
- Recurring entries (rent, depreciation, loan payments).
- A comparative prior-year column on the P&L. The balance sheet already has one.
- A trial balance view. The balance-sheet pill covers the "does it balance" question for now.
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
- **Red for negatives:** on each cash flow section subtotal, every P&L subtotal, and every equity
  line (draws are naturally red). Line items elsewhere stay neutral. Positive net income stays green,
  and other positive grand totals stay gold. (Pass 1, item 6.)
