---
description: Write a new Zen koan every hour
on:
  schedule:
    - cron: "0 * * * *"
  workflow_dispatch:
permissions:
  contents: read
  copilot-requests: write
strict: true
safe-outputs:
  create-pull-request:
    title-prefix: "[workflow] "
    labels: [automated]
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

2. Generate a new, original Zen koan — a short paradoxical statement or dialogue in the style of classical koans (e.g., Rinzai or Soto). It should be thought-provoking, concise, and not a well-known existing koan.

3. Write the file `koans/<timestamp>.md` with this exact structure:
   ```markdown
   # Koan

   <koan text>
   ```

4. Commit the new koan file using conventional commit syntax, but use type `chore: <timestamp>`. Use the same timestamp as the file.

5. Use `create-pull-request` with:
   - Title: add koan for <timestamp>
   - Body: the full koan text

## Safe Outputs

- Use `create-pull-request` to commit the new koan file. The PR will auto-merge if branch rules permit.
- Use `noop` with a brief explanation when the file for this hour already exists or no koan was generated.
