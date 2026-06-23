<p align="center">
  <img src="assets/banner.png" alt="OpenSpec Plus — Better Thinking. Better Specs. Better Software." width="100%" />
</p>

<p align="center">
  <strong>Agentic skills for OpenSpec that enforce deep discovery, testable specs, validated design, and disciplined execution.</strong>
</p>

<p align="center">
  <sub>Built with care for developers who believe in thinking before coding.</sub>
</p>

<p align="center">
  <a href="#-install--update">⚡ Install / Update</a> &bull;
  <a href="#-manual-installation">🔧 Manual Setup</a> &bull;
  <a href="CHANGELOG.md">📝 Changelog</a>
</p>

---

## What is OpenSpec Plus?

[OpenSpec](https://github.com/Fission-AI/OpenSpec) defines the workflow. OpenSpec Plus enforces the structure and discipline.

| Phase | Why it matters |
|---|---|
| 🔍 **Proposal** | Right problem scoped before any solution work begins |
| 📋 **Spec** | Unambiguous requirements with testable acceptance scenarios |
| 🏗️ **Design** | Informed architecture choices with explored alternatives |
| 🗂️ **Tasks** | Dependency-aware execution plan with nothing missed |
| ⚡ **Apply** | Reviewed, gated implementation that stays true to spec |
| 🧪 **TDD** | Every line of code justified by a failing test first |

Works with any AI coding agent: **OpenCode** · **Claude Code** · **Windsurf** · **Cursor** · **GitHub Copilot** · **Antigravity** · **Pi Code** · and others.

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
directory, `VERSION` file, and `openspec/config.yaml` from the repository.

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

Step 6 — Clean up

Remove the temporary download directory.

Step 7 — Summary report

Provide a brief summary with:
- The skills directory used
- How many skills were installed (noting how many were new vs updated)
- Whether config.yaml was created, merged, or left unchanged

Keep the summary concise — no line-by-line breakdown.

Recommend the user to restart their code editor or coding agent for the skills to take effect.
```

---

## 🔄 Stay Updated

OpenSpec Plus improves continuously. Here's how to stay in the loop:

- ⭐ **Star this repo** — helps others discover it and bookmarks it for
  you: [sudokar/openspec-plus](https://github.com/sudokar/openspec-plus)
- 🔔 **Auto-check** *(recommended)* — skills automatically check for updates weekly and notify you in-session; just
  re-run the install prompt when prompted
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

4. Clean up:
   ```bash
   rm -rf /tmp/openspec-plus
   ```

---

## 🤝 Contributing

Contributions are welcome! Feel free to open issues, suggest improvements, or submit pull requests.

---

## 💛 Support

If OpenSpec Plus is saving you time and improving your workflow, consider supporting its development:

<a href="https://github.com/sponsors/sudokar"><img src="https://img.shields.io/badge/Sponsor-♥-ea4aaa?logo=github" alt="GitHub Sponsors" /></a>
<br>
<a href="https://buymeacoffee.com/sudokar"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" height="28" /></a>

---

## 📄 License

MIT
