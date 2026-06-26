# Docs digest

Provides a meaningful summary of what changed in online documentation on a set schedule.

## Basic usage

First, set up agentic workflows in your repository:

```bash
gh aw init
```

Copy the `docs-digest.lock.yml` workflow file from this repository into your repository's `.github/workflows` directory.

The workflow will run every Monday at 9:05 AM. If you need to run the workflow at different time and frequency, adjust the `schedule` key to a period that works for you. Use [crontab guru](https://crontab.guru/) to format a cron expression for the value.


## Requirements

- GitHub CLI with agentic workflows extension
- A repo in an organization with a GitHub Copilot license
- Target documentation that provides an `llms-full.txt` at its site root
