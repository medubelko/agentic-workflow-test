# Agentic Workflow Test

An experiment with agentic workflows in GitHub.


## Create an agentic workflow

Setting up a workflow like the one in this repository doesn't take much time or effort.

You'll need:

- GitHub CLI on version 2.0.0 or higher
- A remote repository to test in, with a local copy
- A company-provided Copilot subscription
- An LLM client, like Copilot CLI, the Copilot VS Code extension, or OpenCode
- Local access to a model with tool calling capability. One of the Claude or Gemini models from your Copilot subscription is sufficient.

First, initialize the basic agent setup in your repository:

```bash
gh aw init
```

GitHub CLI inserts several utility files, including a workflow agent, an agent skill for making workflows, and instructions for the agent's behavior.

Next, decide on a prompt for the workflow you want the agent to make for you. For your first workflow, start with something simple. The one used for this repository was:

> Workflow that writes a koan to the `koans` directory every hour

Open your local LLM client or start a new session. Run the workflow builder skill:

```
/agentic-workflows <workflow prompt>
```

With the workflow agent persona and instructions, the LLM constructs the workflow. It may ask for your input at different turns, but it infers your intent based on the prompt, and designs accordingly.

Once the LLM is finished, two new files are in your repository – the Markdown copy of the workflow rules plus the LLM prompt (`<workflow>.md`), and the lockfile for the workflow (`<workflow>.lock.yml`). The Markdown file is the human- and LLM-readable recipe for the workflow, which you'll edit and adjust to refine the workflow behaviour and results you want. The lockfile is the actual workflow, that GitHub reads and executes in your remote repository. The GitHub CLI creates the lockfile from the Markdown file, and you should never edit it directly. It's a very, very long file, and not meant for humans.

In the Markdown file, edit the permissions so that it consumes your company-provided Copilot subscription, not your personal one:

```md
 permissions:
   contents: read
+   copilot-requests: write
```

With the Markdown file edited, regenerate the lockfile:

```bash
gh aw compile
```

Commit your changes and push to your remote repository. The workflow will run automatically based on the schedule or triggers the agent added.

For your first workflow, it's best to test it before letting it run unsupervised. By default, your new workflow can be called manually. On GitHub, open the **Actions** tab of your repository, select the workflow, and click **Run workflow** to force it to run.


## Reading for further development

For a complete reference for these workflows, see the [GitHub Agentic Workflows](https://github.github.com/gh-aw/introduction/overview/) documentation. As of 14 June 2026, the quality of these docs is inconsistent, because much of it was vibe-written.

For an overview and a less detailed variation of these instructions, try [Creating a workflow](https://docs.github.com/en/copilot/how-tos/github-agentic-workflows/creating-github-agentic-workflows#creating-a-workflow) in the GitHub docs. As of 14 June 2026, some of the details about specifying the Copilot subscriber contradict the agentic workflows documentation.

GitHub provides pre-made agentic workflows. Take a look in the [githubnext/agentics](https://github.com/githubnext/agentics) repository for a smorgasboard of examples.
