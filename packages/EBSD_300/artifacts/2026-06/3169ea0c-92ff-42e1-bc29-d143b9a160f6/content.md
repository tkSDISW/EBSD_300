# EBSD_300 SE Knowledge Partner Work Log

This log captures milestones, observations, decisions, and issues arising from SE KP sessions on the EBSD_300 project.

---

## 2026-06-18T12:41:44Z — milestone

Session established. Cloned EBSD_300 on master. Browsed LA diagrams, generated fabric for [LAB] Extreme Impacted Requirements, extracted 45 requirements into table artifact (86b4ebde). Extracted 3 SA-level needs from [SAB] Context with Needs into table artifact (dbc57d0d). Created and validated routine_def extract_needs_from_diagram_v1 (434be3f3). All artifacts pushed to main.

**References:** [LAB Extreme Impacted Requirements — Requirement Table (table)](/packages/EBSD_300/artifacts/2026-06/86b4ebde-435e-4f1e-b926-d36dab7728aa/content.csv), [SAB Context with Needs — SA-Level Needs Table (table)](/packages/EBSD_300/artifacts/2026-06/dbc57d0d-97db-4eba-9d0b-cdb806936cd5/content.csv), [Extract Requirements from Needs Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json)

---

## 2026-06-18T12:41:54Z — issue

ISSUE-RD-001 | routine_def schema not documented in KP system prompt or any accessible SKILL.md.

When authoring extract_needs_from_diagram_v1, the schema had to be discovered iteratively through server-side validation errors (3 write-validate-fix cycles). Required fields such as variable 'type', resource 'id' and 'mcp_tool', and output 'package' were only revealed at validation time.

Recommendation: Add a routine_def SKILL.md (or inline schema reference in the KP system prompt under the artifact-repo section) that documents the full required structure: routine_def top-level key, variable fields (name, type, description, required, default), resource fields (id, type, mcp_tool, description), pre_flight entry format, prompt_template Jinja2 conventions, and output fields (name, type, package, description, required). Include a minimal working example.

**References:** [Extract Requirements from Needs Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json)
