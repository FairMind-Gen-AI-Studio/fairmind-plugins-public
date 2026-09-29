# fairmind-plugins

Claude Code plugin marketplace by [Fairmind](https://fairmind.ai). This repository lists two plugins, each in its own repository and each scoped to a different stage of the product lifecycle: **design and accessibility** for the UI, **coding workflow** for the engineering team.

| Plugin | Scope | Install |
|---|---|---|
| [`fairmind-design`](https://github.com/FairMind-Gen-AI-Studio/fairmind-design) | Verify React components against Figma, audit design tokens, run WCAG 2.1 AA accessibility audits, generate Code Connect mappings | `/plugin install fairmind-design@fairmind-plugins` |
| [`fairmind-coding`](https://github.com/FairMind-Gen-AI-Studio/fairmind-coding) | Six role-based agents, thirteen skills, lifecycle hooks, nineteen commands, plus an opt-in loop mode with an executed gate. Runs standalone; the Fairmind MCP and a `.fairmind/` session workspace enable connected mode | `/plugin install fairmind-coding@fairmind-plugins` |

The two plugins are independent. Install one, the other, or both. This
repository is the **marketplace only** — it lists the two plugins above and
points at their own repositories; neither plugin's code lives here.

> **This is a preview distribution.** All three repositories (this one and the
> two plugins it lists) are built from a single private development repository
> by a publishing pipeline. Pull requests against any of them cannot be
> merged — file an issue instead. Two things are deliberately not in this
> build: the plugins' test suites, and the FairMind judge integration.

---

## Install

### 1. Add the marketplace

```text
/plugin marketplace add FairMind-Gen-AI-Studio/fairmind-plugins-public
```

This pulls the marketplace metadata from `.claude-plugin/marketplace.json` so Claude Code knows which plugins are available. The marketplace registers itself as `fairmind-plugins` — that is the name the install commands below refer to.

### 2. Install the plugins you want

```text
/plugin install fairmind-design@fairmind-plugins
/plugin install fairmind-coding@fairmind-plugins
```

The `@fairmind-plugins` suffix disambiguates if you have multiple marketplaces installed.

### 3. Verify

```text
/agents     # plugin agents listed (10 fairmind-design agents, 6 fairmind-coding agents)
/help       # slash commands listed (/design-verify, /a11y-audit, /fix-issue, /sonarqube-fix, ...)
```

If something is missing, the most common cause is a missing MCP prerequisite — see below.

---

## Plugins

### fairmind-design

Design and accessibility toolkit. Ten subagents, four orchestration commands, four reference skills.

**What it does**
- Verifies React components match their Figma source, value-by-value and state-by-state
- Enforces semantic design tokens, flags hardcoded colors / spacing / typography
- Generates and maintains Figma Code Connect mappings (`*.figma.tsx`)
- Runs full WCAG 2.1 AA audits — contrast, keyboard, screen reader, ARIA — via Playwright + axe-core
- Reviews component composition (design-system reuse, prop typing, semantics)
- Closes design tasks with a mandatory compliance check against the host project's own rules

**Headline commands** — `/design-verify`, `/a11y-audit`, `/code-connect`, `/design-task-close`.

**MCP prerequisites** — Figma (`mcp__claude_ai_Figma__*`), Playwright (`mcp__plugin_playwright_playwright__*`). This plugin is MCP-bound: without them its agents fail at the first MCP call.

Full reference: [fairmind-design's own README](https://github.com/FairMind-Gen-AI-Studio/fairmind-design/blob/main/README.md).

### fairmind-coding

Coding workflow plugin. Six role-based agents, thirteen skills, lifecycle hooks, nineteen commands.

**What it does**
- The **Technical Lead / Architect** bootstraps a `.fairmind/<project>/<session>/` workspace and returns the ordered plan the command dispatches — never implements
- The **Software Engineer**, **QA Engineer**, **Code Reviewer**, **Security Engineer**, and **Debugging Specialist** implement and validate against the plan, each with its own journal
- Hooks key off `.fairmind/active-context.json` to enforce scoped writes, refuse turn-end when a key agent skipped its journal, run the loop-mode gate, and record tool-call traces and sub-agent token usage
- **Loop mode** (`/fairmind-loop`) turns acceptance criteria into a machine-checkable stop condition and lets an executed gate drive implement→verify→iterate under a user-confirmed budget, with a final human gate; `/fairmind-add-check` authors custom checks and `/harness-audit` scores how loop-ready a repo is
- The remaining commands cover issue triage (`/fix-issue`, `/fix-frontend-issue`), SonarCloud cleanup (`/sonarqube-fix`), reporting and test scaffolding (`/report`, `/make-tests`, `/de-slop`), and the GitHub PR workflow (`/gh-commit`, `/gh-fix-ci`, `/gh-review-pr`, `/gh-address-pr-comments`)

**Prerequisites** — `python3`, `git`, and `bash` are enough: the plugin runs standalone on any repository. The Fairmind (`mcp__Fairmind__*`), Playwright, and MongoDB MCP servers are optional and only enable connected mode; the GitHub commands and the journal hook also want `gh` and `jq` on `$PATH`, and `/sonarqube-fix` needs `SONAR_TOKEN` plus a `sonar-project.properties` file.

Full reference: [fairmind-coding's own README](https://github.com/FairMind-Gen-AI-Studio/fairmind-coding/blob/main/README.md).

---

## What leaves your machine

`fairmind-coding` can send records to Fairmind by more than one route, on different preconditions and with different controls. The account of what each route sends, when it fires, and which of them a repository can switch off is maintained in **one place**, and it is installed alongside the plugin:

```text
~/.claude/plugins/**/fairmind-coding/README.md   →   "What leaves your machine"
```

Read it there. It is deliberately not summarised here: a second copy of a disclosure is a second thing to keep true, and the one that drifts is always the copy.

`fairmind-design` has no such route.

## Requirements

Both plugins assume Claude Code with the `/plugin` command. The MCP servers each plugin needs are listed above; authenticate them once in Claude Code's MCP settings — nothing in this repository configures them for you.

## Layout

This repository carries the marketplace only:

```
.claude-plugin/marketplace.json   marketplace registration; each entry is an
                                   unpinned HTTPS git url pointing at one of
                                   the two plugin repositories below
docsite/                          the documentation site source
```

Each plugin's own code lives in its own repository, with the plugin AT THE
REPOSITORY ROOT (`.claude-plugin/plugin.json`, `agents/`, `commands/`,
`skills/`, and — for `fairmind-coding` — `hooks/`, `scripts/`,
`INTERNALS.md`):

- [`FairMind-Gen-AI-Studio/fairmind-design`](https://github.com/FairMind-Gen-AI-Studio/fairmind-design)
- [`FairMind-Gen-AI-Studio/fairmind-coding`](https://github.com/FairMind-Gen-AI-Studio/fairmind-coding)

## Updating an installed plugin

After a marketplace update on the remote, refresh locally:

```text
/plugin marketplace update fairmind-plugins
/plugin install fairmind-design@fairmind-plugins      # reinstall to pick up the new version
```

## Uninstall

```text
/plugin uninstall fairmind-design@fairmind-plugins
/plugin uninstall fairmind-coding@fairmind-plugins
/plugin marketplace remove fairmind-plugins
```

## License

MIT — see [LICENSE](LICENSE).
