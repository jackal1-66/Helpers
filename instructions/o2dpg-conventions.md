---
applyTo: "**/O2DPG/**,**/MC/**,**/DATA/**,**/WORKFLOW/**,**/PDP/**"
---

# O2DPG conventions

- `workflow.json`: required fields per task: `name`, `executable`, `options`.
- Production config naming: `<period>_<pass>_<type>.json`.
- Anchored MC: AliceO2 + O2Physics tags must exist in alidist before merging.
- QC task configs must reference valid QC check class names (fully qualified).
- New MC generator configs need a matching entry in `MC/config/`.
- Pipeline changes must not break existing `o2dpg_sim_workflow.py` CLI interface.
