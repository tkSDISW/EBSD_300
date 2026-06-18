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

---

## 2026-06-18T12:42:01Z — issue

ISSUE-RD-002 | validate_routine_def returns all errors in a single pass but does not indicate which fields are valid. 

During authoring, validation only reports what is broken, not what is correct. After the first fix pass, it was unclear whether previously-passing fields had regressed. A "passed checks" list alongside "errors" would allow faster iterative authoring and reduce the risk of inadvertently breaking a previously valid section while fixing another.

Recommendation: Extend the validate_routine_def response to include a "passed" array listing fields that were verified clean, in addition to "errors" and "warnings". Alternatively, provide a --verbose mode.

**References:** [Extract Requirements from Needs Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json)

---

## 2026-06-18T12:42:09Z — issue

ISSUE-RD-003 | pre_flight_checks field name inconsistency between system prompt and schema validator.

The KP system prompt (v4) documents the pre-flight block as "pre_flight_checks", but the validate_routine_def server reports the correct field name as "pre_flight" and the summary key "has_pre_flight". Writing "pre_flight_checks" caused the validator to silently ignore the block (has_pre_flight: false) on the first attempt, with no warning raised.

Recommendation: (a) Align system prompt documentation to use "pre_flight" consistently, OR (b) have the validator emit a warning when it detects an unrecognised top-level key inside routine_def (such as "pre_flight_checks") that looks like a near-miss for a known field.

**References:** [Extract Requirements from Needs Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json)

---

## 2026-06-18T12:42:17Z — issue

ISSUE-RD-004 | No dry-run or render-only mode for routine_def prompt_template.

The Jinja2 prompt_template is the most complex part of a routine_def and the hardest to validate — validate_routine_def checks structure but does not render the template with test variable values to confirm the Jinja2 is syntactically correct and produces sensible output. Errors in template syntax (unclosed blocks, wrong variable names) will only surface at execution time.

Recommendation: Add a render_routine_prompt tool (analogous to render_prompt for prompt_def artifacts) that accepts a routine_def artifact_id and a variables dict, renders the prompt_template with those values, and returns the rendered string. This allows the author to proof the template before the routine is ever executed in a live session.

**References:** [Extract Requirements from Needs Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json)
