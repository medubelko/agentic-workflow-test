---
description: Create a rolling digest of all changes in a documentation site and publish them as GitHub issues
on:
  schedule:
    - cron: "5 9 * * 1"  # Weekly on Mondays at 09:05
  workflow_dispatch:
    inputs:
      SITE:
        description: Root URL of the documentation site. Defaults to Chisel docs.
        required: false
        type: string
        default: https://ubuntu.com/chisel/docs/latest
permissions:
  contents: read
  issues: read
  copilot-requests: write
tools:
  github:
    mode: gh-proxy
    toolsets:
      - issues
env:
  SITE: https://ubuntu.com/chisel/docs/latest
  DATA_DIR: /tmp/gh-aw/data/
  LLMS_TXT_NEW: /tmp/gh-aw/data/llms-full-new.txt
  LLMS_TXT_OLD: /tmp/gh-aw/data/llms-full-old.txt
  LLMS_TXT_DIFF: /tmp/gh-aw/data/llms-full-diff.txt
steps:
  - name: Fetch previous llms.txt
    uses: actions/download-artifact@v4
    id: download
    with:
      name: llms-full-txt
      path: ${{ env.DATA_DIR }}
    continue-on-error: true
  - name: Fetch current llms-full.txt and create diff
    run: |
      SITE="${{ github.event.inputs.SITE || env.SITE }}"

      mkdir -p /tmp/gh-aw/data
      curl -fsSL "${SITE%/}/llms-full.txt" -o "$LLMS_TXT_NEW"

      if ${{ steps.download.outcome != 'failure' }}; then
        cp "${{ env.DATA_DIR}}/llms-full.txt" "$LLMS_TXT_OLD"
        diff "$LLMS_TXT_OLD" "$LLMS_TXT_NEW" > "$LLMS_TXT_DIFF" || true
      else
        touch "$LLMS_TXT_DIFF"
      fi
  - name: Store latest llms.txt
    uses: actions/upload-artifact@v4
    with:
      name: llms-full-txt
      path: ${{ env.LLMS_TXT_NEW }}
      overwrite: true
      retention-days: 15
safe-outputs:
  create-issue:
    title-prefix: "[Docs digest]"
    labels: [automated]
    group-by-day: true
  noop:
    report-as-issue: false
network:
  allowed:
    - ubuntu.com
    - canonical.com
    - documentation.ubuntu.com
---

# Docs Digest

## Task

Read `/tmp/gh-aw/data/llms-full-diff.txt`. It contains either:

- A unified diff between the previous and latest `llms-full.txt` (normal run), or
- Nothing, when there are no changes or no prior content exists.

If the file is empty, stop. Do not create an issue.

### If changes are detected in `llms-full-diff.txt`

Produce a plain-English digest of what changed:

1. Group changes by section or topic heading where identifiable; otherwise, group by change type.
2. Describe additions, removals, and significant rewrites as separate bullet groups.

Create a GitHub issue:

- **Title**: `Docs digest — YYYY-MM-DD` (use today's UTC date)
- **Body**: the plain-English digest produced above.


## Safe Outputs

- Use `create-issue` with the digest as the body.
