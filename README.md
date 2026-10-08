# SlipTip prediction ledger

Every prediction SlipTip grades in its Results tab is fingerprinted here
**before kickoff**, so nobody - SlipTip included - can change a prediction
after seeing the result.

## How it works

1. Before kickoff, SlipTip publishes a batch's SHA-256 fingerprint
   (`commitments/.../batch-N.sha256`) and an OpenTimestamps proof
   (`batch-N.json.ots`). The fingerprint reveals nothing about the
   predictions.
2. After the games, the batch itself is published
   (`batches/.../batch-N.json`).
3. If even one number in the batch had been changed, its fingerprint would
   no longer match the one published before kickoff.

## Check a batch yourself

```
shasum -a 256 batches/PL/2026/batch-000001.json
```

The result must match the first line of
`commitments/PL/2026/batch-000001.sha256`, and that file's commit must come
before the `first_kickoff` it lists.

For proof that doesn't rely on GitHub's dates, use OpenTimestamps
(`pip install opentimestamps-client`):

```
ots upgrade commitments/PL/2026/batch-000001.json.ots
ots verify commitments/PL/2026/batch-000001.json.ots -f batches/PL/2026/batch-000001.json
```

More at https://www.sliptip.co.uk/ledger
