# SlipTip prediction ledger

<!-- ledger-readme-v4 -->

Every prediction SlipTip grades in its Results tab is fingerprinted here
**before kickoff**, so nobody - SlipTip included - can change a prediction
after seeing the result.

## How it works

1. **Before kickoff**, SlipTip publishes a batch file listing a fingerprint
   (SHA-256) for each game's prediction - `batches/<league>/<season>/batch-N.json` -
   plus that file's own fingerprint (`commitments/.../batch-N.sha256`) and an
   OpenTimestamps proof (`commitments/.../batch-N.json.ots`). Fingerprints
   reveal nothing about the predictions.
2. **About 3 hours after each game kicks off**, that game's full prediction is
   published: `games/<league>/<season>/batch-N/<game>.json`. It includes a
   random "salt", so a fingerprint can't be cracked by guessing percentages.
3. If even one number in a prediction had been changed, its fingerprint would
   no longer match the one published before kickoff.

## Check a prediction yourself

```
shasum -a 256 games/PL/2026/batch-000009/<game>.json
```

That code must appear next to the game in `batches/PL/2026/batch-000009.json`. Then:

```
shasum -a 256 batches/PL/2026/batch-000009.json
```

must match the first line of `commitments/PL/2026/batch-000009.sha256`.

For proof of timing that doesn't rely on GitHub's dates, use OpenTimestamps
(`pip install opentimestamps-client`):

```
ots upgrade commitments/PL/2026/batch-000009.json.ots
ots verify commitments/PL/2026/batch-000009.json.ots -f batches/PL/2026/batch-000009.json
```

Predictions are made week by week: each game's prediction is locked about 48
hours before kickoff. Until 8 October 2026 every remaining game of the season
was locked the first time it was seen; those early predictions (fingerprinted
on 8 October) were replaced for games more than 48 hours away. Where a game has
two fingerprints, the graded one is the later, `@`-stamped one, locked within
48 hours of kickoff. The early one is still revealed after the game, unchanged.

Batches 1-8 are a first format: one file per league with every remaining game
of the season, fingerprinted on 8 October 2026. Each is published as
`batches/<league>/<season>/batch-00000N.json` once all its games are played
(end of season), and checks the same way against its `.sha256`. The same
predictions are also in the per-game batches from 9 onwards.

More at https://www.sliptip.co.uk/ledger
