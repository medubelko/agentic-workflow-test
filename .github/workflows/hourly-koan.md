---
emoji: 🎋
description: Write a new Zen koan to the koans directory every hour
on:
  schedule:
    - cron: "0 * * * *"
  workflow_dispatch:
permissions:
  contents: read
strict: true
safe-outputs:
  create-pull-request:
    title-prefix: "[koan] "
    labels: [koan, automated]
    draft: false
    auto-merge: true
    allowed-files:
      - "koans/**"
    if-no-changes: warn
---

# Hourly Koan Writer

## Task

Generate a unique, original Zen koan and commit it as a new file in the `koans/` directory.

## Steps

1. Use bash to determine the current UTC timestamp:
   ```bash
   date -u +"%Y-%m-%dT%H"
   ```
   Use this to form the filename: `koans/YYYY-MM-DDTHH.md`

2. Check whether that file already exists:
   ```bash
   test -f koans/<timestamp>.md && echo "exists" || echo "new"
   ```
   If the file already exists, call `noop` with the message "Koan for this hour already written."

3. Generate a new, original Zen koan — a short paradoxical statement or dialogue in the style of classical koans (e.g., Rinzai or Soto). It should be thought-provoking, concise, and not a well-known existing koan.

4. Write the file `koans/<timestamp>.md` with this exact structure:
   ```markdown
   # Koan

   <koan text>

   ---

   *Written at <ISO 8601 UTC timestamp, e.g. 2025-06-14T19:00Z>*
   ```

5. Use `create-pull-request` with:
   - Title: the first 6–8 words of the koan
   - Body: the full koan text

## Safe Outputs

- Use `create-pull-request` to commit the new koan file. The PR will auto-merge if branch rules permit.
- Use `noop` with a brief explanation when the file for this hour already exists or no koan was generated.
