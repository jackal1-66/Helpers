# ALICE Personal Helpers

A collection of personal tools and configurations for working with the ALICE O2 ecosystem (O2DPG, AliceO2, O2Physics, alidist).

## Contents

```
~/.copilot/
├── agents/
│   └── o2pr-reviewer.agent.md   # GitHub Copilot custom agent for PR review
├── instructions/
│   ├── aliceo2-conventions.md   # AliceO2 coding conventions (auto-applied)
│   ├── alidist-conventions.md   # alidist recipe conventions (auto-applied)
│   └── o2dpg-conventions.md     # O2DPG workflow conventions (auto-applied)
~/
└── alice-pr-dashboard.html      # Standalone PR monitor dashboard (browser)
```

---

## GitHub Copilot Agent: `o2pr-reviewer`

**Usage:** In GitHub Copilot Chat, type `@o2pr-reviewer <Repo> #<PR>`, e.g.:

```
@o2pr-reviewer O2Physics #1234
@o2pr-reviewer O2DPG #567
@o2pr-reviewer AliceO2 #8910
@o2pr-reviewer alidist #42
```

### Supported repositories

| Short name | GitHub org/repo |
|---|---|
| `O2Physics` | `AliceO2Group/O2Physics` |
| `AliceO2` (alias `O2`) | `AliceO2Group/AliceO2` |
| `O2DPG` | `AliceO2Group/O2DPG` |
| `QualityControl` | `AliceO2Group/QualityControl` |
| `alidist` | `alisw/alidist` |

### What it does

1. **Fetches the PR diff** — changed files and patches.
2. **Fetches CI status** — finds failed/running workflow runs, drills into failed jobs and extracts relevant log lines (`##[error]`, `FAILED`, `fatal`, `Segmentation fault`, etc.).
3. **Cross-repo coherence check** — searches AliceO2Group/alisw for novel terms introduced by the PR (new class names, macros, configurables, renamed APIs) to catch naming conflicts and downstream impacts.
4. **Applies repo-specific conventions** — from the instruction files below.

### Output format (always in this order)

| Section | Contents |
|---|---|
| 📋 **Summary** | What the PR does and why, 2–3 sentences |
| 🔴 **CI Status** | Failed job → extracted error lines → root cause → suggested fix. ✅ if all green, ⏳ if still running |
| 🐛 **Bugs** | File + line + fix. "None found" if clean |
| 📐 **Conventions** | Violations only, with file+line. "OK" if compliant |
| 🔗 **Cross-repo coherence** | Search results per novel term |
| ⚡ **Performance** | Table copies, missing `sliceByCached`, hot-loop issues |
| ✅ **Verdict** | `Approve` / `Request Changes` / `Needs Discussion` + one-line reason |

> **Note:** CI log fetching requires a GitHub token with `actions:read` scope. If a 403 is returned, CI verdict is based on the run `conclusion` field alone.

---

## Copilot Instructions (auto-applied)

These files are automatically applied by GitHub Copilot when editing files in the matching paths.

### AliceO2 conventions
**Applies to:** `**/AliceO2/**`, `**/Detectors/**`, `**/DataFormats/**`, `**/Framework/**`, `**/GPU/**`, `**/Steer/**`, `**/Simulation/**`

- No raw owning pointers — use `std::unique_ptr` / `gsl::span`.
- `DataHeader` + `DataProcessingHeader` used for all DPL messages.
- GPU code (`GPU/` subtree): follow `GPUCommonDef`, no STL in device code.
- Detector namespaces: `o2::<det>::` (e.g. `o2::tpc::`, `o2::its::`).
- New data types registered in `DataFormats/` and added to `LinkDef` if ROOT-serialised.
- Unit tests expected for new algorithms under `<module>/test/`.

### alidist conventions
**Applies to:** `**/*.sh`, `**/alidist/**`

- Required recipe fields: `package`, `version`, `tag`, `source` (if external).
- Version bump must match the `tag` field exactly.
- All build deps listed under `requires:` must be valid alidist packages.
- Use `incremental_recipe` for large packages (O2, O2Physics) to speed CI.
- A version bump of O2 or O2Physics must be accompanied by a compatibility check against O2DPG anchored production configs.
- `prefer_system` only allowed for well-isolated system libraries.

### O2DPG conventions
**Applies to:** `**/O2DPG/**`, `**/MC/**`, `**/DATA/**`, `**/WORKFLOW/**`, `**/PDP/**`

- `workflow.json`: required fields per task: `name`, `executable`, `options`.
- Production config naming: `<period>_<pass>_<type>.json`.
- Anchored MC: AliceO2 + O2Physics tags must exist in alidist before merging.
- QC task configs must reference valid QC check class names (fully qualified).
- New MC generator configs need a matching entry in `MC/config/`.
- Pipeline changes must not break existing `o2dpg_sim_workflow.py` CLI interface.

---

## PR Monitor Dashboard (`alice-pr-dashboard.html`)

A self-contained single-file HTML dashboard for monitoring open pull requests across the ALICE O2 repositories.

### Features

- **Tabs:** O2DPG · AliceO2 · alidist · O2Physics · All
- **CI status badges** with expandable per-check breakdown (✅ pass · ❌ fail · ⏳ pending · ⬜ skipped)
- **`jackal1-66` filter** — toggle to show only PRs where that user is author, reviewer, or commenter
- **Search** by title, PR number, or author; filter by CI state
- **Auto-refresh** every 3 minutes
- **Dark / light theme** toggle
- **GitHub token support** — raises API rate limit from 60 to 5 000 req/h

### Opening with a token

**Linux:**
```bash
xdg-open "file://$HOME/alice-pr-dashboard.html?token=$(gh auth token)"
```

**macOS:**
```bash
open "alice-pr-dashboard.html?token=$(gh auth token)"
```

The token is stored in `localStorage` and sent only to `api.github.com`. Generate one at:
<https://github.com/settings/tokens/new?scopes=repo&description=ALICE+PR+Dashboard>

---

## Installation

### 1. Clone / copy this repo

```bash
git clone <this-repo-url> ~/alice-personal-helpers
```

### 2. Link Copilot configuration

```bash
mkdir -p ~/.copilot/{agents,instructions}

# Instructions (auto-applied by Copilot)
ln -sf ~/alice-personal-helpers/instructions/aliceo2-conventions.md  ~/.copilot/instructions/
ln -sf ~/alice-personal-helpers/instructions/alidist-conventions.md  ~/.copilot/instructions/
ln -sf ~/alice-personal-helpers/instructions/o2dpg-conventions.md    ~/.copilot/instructions/

# Custom agent
ln -sf ~/alice-personal-helpers/agents/o2pr-reviewer.agent.md ~/.copilot/agents/
```

### 3. Dashboard

Copy or symlink `alice-pr-dashboard.html` anywhere convenient, then open it in a browser as shown above.

---

## Requirements

- **GitHub Copilot** subscription (for the agent and instructions).
- **GitHub CLI** (`gh`) — optional, for token injection via `$(gh auth token)`.
- A modern browser — for the dashboard.
