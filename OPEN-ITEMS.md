# Books — open items

Single self-contained `index.html`, house style from `../PROJECT-HANDOFF.md`. Storage: one
`localStorage` key, `books.v1`, schema 1.

## To do

- **Deploy.** Needs a new public repo on the `mrholdingsworth` account (the same one as Cal) with
  Pages turned on. `gh` isn't installed, so the repo has to be created in the GitHub web UI first.
  Keep `.nojekyll` alongside `index.html`.

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
- **Reports default to as-of today**, covering the calendar year to date. There are quick buttons
  for prior year ends.
