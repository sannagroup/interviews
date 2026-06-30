# What the bank actually does

Banks lie a little. They re-send the same transaction. They occasionally re-send it with a slightly different description after a fix. They sometimes reverse a charge and re-issue it with a corrected amount. They sometimes give two genuinely-different transactions identical-looking content.

The ingestion service is the gatekeeper of trust for everything downstream, including the AI agents that categorize transactions and the accountants who file the books. If a duplicate slips through, the books are wrong. If a real transaction is skipped as "probably a duplicate", money goes missing from the ledger.

Below are some of the patterns seen on real bank feeds. They're all present, in some form, in `incoming-batch.json` and `existing-transactions.json`. There may be others. The exact expected handling for each is deliberately not spelled out — that's part of the design call. The right choice should be made for each case, and explained.

## 1. The re-send

The bank delivers the same transaction more than once. The internal `id` field rotates on each delivery, but the `booking_reference` assigned is stable. The same transaction can arrive in two different batches, hours or days apart.

## 2. The fixed description

The bank's first delivery had a garbled or auto-truncated description (e.g. `REF #XX91-PNDG`). A later delivery has the same `booking_reference`, same amount, same value date, but a cleaned-up description.

## 3. The reversal + correction

A previously-ingested transaction was wrong. The bank reverses it (sends the same `booking_reference` with the inverse amount) and re-charges with the correct amount as a new `booking_reference`. The original, the reversal, and the correction may arrive separately, or together, or out of order.

## 4. The genuine double-pay

Two legitimately distinct transactions with identical amounts, identical counterparty, identical day, and similar descriptions, but different `booking_reference`s assigned by the bank. (The owner bought two coffees from the same café an hour apart.) These are NOT duplicates.

## 5. The amount mismatch

A transaction arrives with the same `booking_reference` as a row already in the database, but the amount has changed significantly (not a small rounding correction, an order-of-magnitude difference). This is rare enough that the right move probably isn't to blindly accept it.

## 6. Missing or empty fields

Counterparty `null`. Description blank. Amount present, booking reference present. These can't be rejected (money moved), but they also can't be treated as complete.

## 7. Future-dated transactions

A row with `value_date` in the future. Real (a scheduled transfer, a forward-dated invoice) or wrong (timezone bug somewhere). The bank says trust them.

## 8. Out-of-order delivery within a batch

A correction arrives in the same batch as its original, but earlier in the array. The service can't depend on the order of `incoming-batch.json` matching causal order.

## What the design should make visible

For each pattern, the service should make a clear decision and record why. The right answer set is roughly:

- `inserted`: new row, persist it.
- `skipped_duplicate`: same logical transaction already on file, no change.
- `updated`: same logical transaction, but new information (e.g. description fix) is worth recording.
- `flagged_for_review`: something doesn't add up. Don't act unilaterally. Record the suspicion.
- `reversed` / `corrected`: bookkeeping for reversal + re-charge pairs.

The exact taxonomy is open. The categories above are illustrative, not prescribed. What matters is that:

- Every incoming row produces exactly one decision.
- Re-running the same batch produces the same DB state and the same decisions (idempotency).
- The decision is auditable. Six months from now, an accountant asks "why is this transaction not in the books?", and the decision log answers that.
- Partial failure doesn't lose data or cause divergence.

Handling every imaginable case isn't expected. What is expected is clarity, in the code and in the walkthrough, about which cases were handled, which were punted on, and what the punt would cost.
