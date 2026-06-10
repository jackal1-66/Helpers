---
applyTo: "**/*.sh,**/alidist/**"
---

# alidist conventions

- Required recipe fields: `package`, `version`, `tag`, `source` (if external).
- Version bump must match the `tag` field exactly.
- All build deps listed under `requires:` must be valid alidist packages.
- Use `incremental_recipe` for large packages (O2, O2Physics) to speed CI.
- A version bump of O2 or O2Physics must be accompanied by a compatibility check
  against O2DPG anchored production configs.
- `prefer_system` only allowed for well-isolated system libraries.
