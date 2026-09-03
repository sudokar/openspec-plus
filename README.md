<p align="center">
  <img src="assets/banner.png" alt="OpenSpec Plus — Better Thinking. Better Specs. Better Software." width="100%" />
</p>

<p align="center">
  <strong>Agentic skills for OpenSpec that enforce deep discovery, testable specs, validated design, and disciplined execution.</strong>
</p>

<p align="center">
  <sub>Built with care for developers who believe good software starts with good thinking.</sub>
</p>

<p align="center">
  <a href="#-install--update">⚡ Install / Update</a> &bull;
  <a href="#config">⚙️ Configuration</a> &bull;
  <a href="#-manual-installation">🔧 Manual Setup</a> &bull;
  <a href="CHANGELOG.md">📝 Changelog</a>
</p>

<p align="center">
  <a href="https://github.com/sudokar/openspec-plus/stargazers"><img src="https://img.shields.io/github/stars/sudokar/openspec-plus?style=social" alt="GitHub Stars" /></a>
  &nbsp;
  <a href="https://github.com/sudokar/openspec-plus/releases/latest"><img src="https://img.shields.io/github/v/release/sudokar/openspec-plus" alt="Version" /></a>
  &nbsp;
  <img src="https://img.shields.io/github/license/sudokar/openspec-plus" alt="License" />
</p>

---

## What is OpenSpec Plus?

[OpenSpec](https://github.com/Fission-AI/OpenSpec) defines the workflow. OpenSpec Plus enforces the structure and discipline. It's designed to fit into the existing OpenSpec workflow rather than replace it.

| Phase | Without Plus | With Plus |
|---|---|---|
| 🔍 **Proposal** | Agent jumps to solutions; scope creep starts immediately | Right problem scoped through structured discovery before any solution work |
| 📋 **Spec** | Vague requirements; nothing testable; "done" is subjective | Unambiguous requirements with testable acceptance scenarios |
| 🏗️ **Design** | First idea gets used; user doesn't own the decision | Alternatives explored with trade-offs; user chooses the direction |
| 🗂️ **Tasks** | Grouped by tech layer; no testable outcome per group | Vertical slices where each group delivers something the user can verify |
| ⚡ **Apply** | No spec verification; failing tests skipped; issues silently fixed in code | Spec-verified, quality-gated per slice; failures escalated, never bypassed |
| 🧪 **TDD** | Tests are optional or an afterthought — doesn't exist in vanilla | Every line of production code justified by a failing test first |

If this resonates, ⭐ [star the repo](https://github.com/sudokar/openspec-plus) — it helps others discover it and keeps the project going.

**Supported coding agents:**

| Agent | Compatible |
|---|:---:|
| OpenCode | ✅ |
| Claude Code | ✅ |
| Windsurf/Devin | ✅ |
| Cursor | ✅ |
| GitHub Copilot | ✅ |
| Antigravity | ✅ |
| Pi Code | ✅ |
| Others | ✅ |

---

## ⚡ Install / Update

> **Prerequisites:** [OpenSpec](https://github.com/Fission-AI/OpenSpec) initialized in your project (`openspec init`) and a supported AI coding agent.

Copy the prompt below and paste it directly into your AI coding agent. It will download, install, and configure
everything automatically.

> **This same prompt works for both first-time installation and updates.** Run it anytime to get the latest version.

```text
Install/Update OpenSpec Plus Skills

Your task is to install or update OpenSpec Plus skills
and configuration from the GitHub repository https://github.com/sudokar/openspec-plus
into the current project.

Follow these steps in order. Ask the user when indicated — do not assume answers.

Step 1 — Verify OpenSpec is set up

Check if an `openspec/` directory exists in the current working directory.
- If it does NOT exist: inform the user that OpenSpec is not set up in this project.
  Suggest they run `openspec init` first. STOP here — do not continue.
- If it exists: proceed to Step 2.

Step 2 — Determine skills directory

Detect the coding agent environment by checking which of these directories exist
in the current working directory:
- `.opencode/` → suggest `.opencode/skills/` as default
- `.claude/` → suggest `.claude/skills/` as default
- `.cursor/` → suggest `.cursor/skills/` as default
- `.antigravity/` → suggest `.antigravity/skills/` as default

If multiple are found, list them. If none are found, do not suggest a default.

Ask the user: "Where should I install the skills?" and present the detected default
(if any). Let them confirm or provide a custom path.

Step 3 — Download from GitHub

Download the repository https://github.com/sudokar/openspec-plus to a temporary
location. Use whatever method is available to you (`git clone --depth 1`,
`gh repo clone`, `curl`, direct file download, etc.). You only need the `skills/`
directory, `VERSION` file, `openspec/config.yaml`, and `openspec/.plus/config.yaml`
from the repository.

Step 4 — Install skills and VERSION file

From the downloaded repository:
1. Copy all `openspec-plus-*` directories inside `skills/` to the skills directory
   confirmed in Step 2. Each skill is a directory containing a `SKILL.md` and
   potentially additional files — copy the entire directory recursively.
2. Copy the `VERSION` file to `openspec/.plus/VERSION` in the current project.
   Create the `openspec/.plus/` directory if it does not exist.

- If the target skills directory does not exist, create it.
- If a skill directory already exists at the target, overwrite its contents
  (this is an update).
- Track which skills were newly installed vs updated (the directory existed before).

Step 5 — Merge config.yaml

Check if `openspec/config.yaml` exists in the current project:

- If it does NOT exist: copy the downloaded `openspec/config.yaml` from the
  repository as-is to `openspec/config.yaml` in the current project.
- If it DOES exist: merge the downloaded config into the existing one:
  - For the `context` field: append any lines from the downloaded config that
    are not already present in the existing context.
  - For each key under `rules` (e.g., `proposal`, `specs`, `design`, `tasks`):
    append any rule entries from the downloaded config that are not already
    present in the existing array.
  - Do NOT remove or modify any existing content in the user's config.yaml.
    Only add what is missing.

Step 6 — Merge openspec/.plus/config.yaml

Check if `openspec/.plus/config.yaml` exists in the current project:

- If it does NOT exist: copy the downloaded `openspec/.plus/config.yaml` as-is
  to `openspec/.plus/config.yaml` in the current project. Create the
  `openspec/.plus/` directory if it does not exist.
- If it DOES exist: deep-merge by key path (this file nests scalars under
  `settings.*`, `settings.apply.*` — not arrays, so this is a different merge shape
  than Step 5's array-append):
  for each key path present in the downloaded file but absent from the
  existing one, add it with the downloaded default value. Do NOT overwrite,
  remove, or modify any key the existing file already has, even if its value
  differs from the new default — the user may have deliberately customized it.

Step 7 — Clean up

Remove the temporary download directory.

Step 8 — Summary report

Provide a brief summary with:
- The skills directory used
- How many skills were installed (noting how many were new vs updated)
- Whether config.yaml was created, merged, or left unchanged
- Whether openspec/.plus/config.yaml was created, merged (how many new keys), or left unchanged

Keep the summary concise — no line-by-line breakdown.

Recommend the user to restart their code editor or coding agent for the skills to take effect.
```

---

## 🚀 First Steps After Install

Once installed and your agent restarted, try it on a real change right away:

1. Start a new change with `/opsx:propose` or `/opsx:new` in your coding agent
2. Let the Proposal skill guide you through scoped discovery
3. Work through each phases (plan to build) — notice the difference from vanilla

The fastest way to feel the value is to run it on something you're actually building.

---

<a id="config"></a>

## ⚙️ Configuration

Tune behavior via `openspec/.plus/config.yaml` (separate from OpenSpec's own `openspec/config.yaml`; installed/updated automatically per the steps above). Every key is optional — omit any of them for today's default behavior:

```yaml
settings:
  questionMode: sequential   # sequential (default) | batch

  autoUpdateCheck: true        # true (default) | false

  apply:
    executionMode: ask          # ask (default) | subagent | inline
    parallelism: ask             # ask (default) | always | never
```

| Key | Effect |
|---|---|
| `questionMode: batch` | Proposal/design/spec/tasks group each phase's questions into as few question-tool rounds as possible instead of one at a time — same real answers, no assumptions, just fewer round trips. Most valuable for per-prompt-priced model providers, especially premium/high-tier models where each round trip is expensive. Config-only, no conversational trigger. |
| `autoUpdateCheck: false` | Skips proposal's weekly version check + upgrade question entirely — useful offline or on network-restricted setups. |
| `apply.executionMode` | Set to `subagent` or `inline` to skip apply's Subagent-vs-Inline question every run. |
| `apply.parallelism` | Set to `always` or `never` to skip apply's parallel-dispatch question. |

---

## 🔄 Stay Updated

OpenSpec Plus improves continuously. Here's how to stay in the loop:

- ⭐ **Star this repo** — helps others discover it and bookmarks it for
  you: [sudokar/openspec-plus](https://github.com/sudokar/openspec-plus)
- 🔔 **Auto-check** *(recommended)* — skills automatically check for updates weekly and notify you in-session; just
  re-run the install prompt when prompted (disable via `settings.autoUpdateCheck: false`, see [Configuration](#-configuration))
- 👁️ **GitHub Watch** — click **Watch → Custom → Pull requests** to get notified when new features ship
- 📝 **Changelog** — check [CHANGELOG.md](CHANGELOG.md) periodically to see what's new

---

## 🔧 Manual Installation

If you prefer not to use the AI prompt:

1. Clone the repository:
   ```bash
   git clone --depth 1 https://github.com/sudokar/openspec-plus.git /tmp/openspec-plus
   ```

2. Copy the skills to your agent's directory and the VERSION file to `openspec/.plus/` (example for OpenCode):
   ```bash
   cp -r /tmp/openspec-plus/skills/openspec-plus-* .opencode/skills/
   mkdir -p openspec/.plus && cp /tmp/openspec-plus/VERSION openspec/.plus/VERSION
   ```

3. Merge the config rules into your `openspec/config.yaml`:
   ```bash
   cat /tmp/openspec-plus/openspec/config.yaml
   # Manually add the context and rules entries to your existing config.yaml
   ```

4. Install or merge `openspec/.plus/config.yaml` (OpenSpec Plus's own settings — see [Configuration](#-configuration)):
   ```bash
   mkdir -p openspec/.plus
   test -f openspec/.plus/config.yaml || cp /tmp/openspec-plus/openspec/.plus/config.yaml openspec/.plus/config.yaml
   # File already existed? Add only missing keys by hand — never overwrite
   ```

5. Clean up:
   ```bash
   rm -rf /tmp/openspec-plus
   ```

---

## 🤝 Contributing

Contributions are welcome! Here are some ways to get involved:

- **Improve existing skills** — better discovery questions, stronger gates, sharper review prompts
- **Propose new skills** — ideas for phases or workflows not yet covered
- **Report issues** — something not working or behaving unexpectedly? [Open an issue](https://github.com/sudokar/openspec-plus/issues)
- **Share feedback** — tried it on a real project? [Start a discussion](https://github.com/sudokar/openspec-plus/discussions) and tell us what worked and what didn't

Not sure where to start? Browse [open issues](https://github.com/sudokar/openspec-plus/issues) or jump into [discussions](https://github.com/sudokar/openspec-plus/discussions).

---

## 📄 License

MIT
