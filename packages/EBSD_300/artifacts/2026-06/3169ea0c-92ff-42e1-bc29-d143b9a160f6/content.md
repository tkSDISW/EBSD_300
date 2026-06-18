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

---

## 2026-06-18T12:42:26Z — issue

ISSUE-RD-005 | Routine Execution Protocol (step 4) assumes all routines have a capella_model_repo resource, causing unnecessary clone steps for artifact-only routines.

The 10-step KP Routine Execution Protocol in the v4 system prompt states that step 5 should clone the capella_model_repo for every declared resource. However, many routines (such as pure artifact extraction or report generation routines) may have no Capella resource at all. The system prompt does note "only if the routine declares it" but the surrounding language implies capella_model_repo is the expected default, which may lead KP to attempt a clone unnecessarily.

Recommendation: Make the system prompt more explicit that step 5 is a conditional branch: if no capella_model_repo resource is declared, skip all model cloning entirely. Consider adding a resource type enum to the schema documentation so authors choose explicitly from [capella_model_repo, artifact_repo, external_api] etc. rather than free-texting the type.

**References:** [Extract Requirements from Needs Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json)

---

## 2026-06-18T12:42:34Z — issue

ISSUE-RD-006 | No standard pattern for the post-execution summary step in the prompt_template.

Each routine author currently hand-crafts the Step 8 summary output format (tables, field lists, data quality callouts). This will produce inconsistent engineer experience across routines. There is no shared summary template, no standard set of "always include" fields, and no guidance on how verbose or terse the output should be.

Recommendation: Define a standard summary block convention in the KP system prompt or a shared prompt_def that routine authors can reference via Jinja2 include or copy-paste. Suggested standard fields: routine_id, diagram/input source, count of objects processed, artifact ID(s) written, push commit SHA, elapsed steps, data quality flags. Allow per-routine extension beyond this baseline.

**References:** [Extract Requirements from Needs Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json)

---

## 2026-06-18T13:24:12Z — milestone

Routine extract_requirements_from_diagram_v1 completed — extracted 45 requirements from '[LAB] Requirements' (LA). Artifact ID: db738ce2-e5e6-4198-9e58-5adbcce9f5b2

**References:** [[LAB] Requirements — Requirements Table (table)](/packages/EBSD_300/artifacts/2026-06/db738ce2-e5e6-4198-9e58-5adbcce9f5b2/content.csv)

---

## 2026-06-18T13:26:30Z — milestone

First successful execution of generalised routine extract_requirements_from_diagram_v1 against [LAB] Requirements diagram. Routine ran cleanly on first attempt with no debugging required — 45 requirements extracted, artifact written, pushed to main, and immediately visible in the artifact web viewer. Engineer confirmed artifacts readable as a dashboard in the viewer.

**References:** [Extract Requirements from Capella Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json), [[LAB] Requirements — Requirements Table (table)](/packages/EBSD_300/artifacts/2026-06/db738ce2-e5e6-4198-9e58-5adbcce9f5b2/content.csv)

---

## 2026-06-18T13:26:44Z — observation

OBS-EBSD-001 · Routine generalisation confirmed — extract_requirements_from_diagram_v1 executes correctly across diagram types without modification.

The routine was originally authored against [SAB] Context with Needs (3 requirements) and generalised from extract_needs_from_diagram_v1 to extract_requirements_from_diagram_v1 by updating the id, name, description, and tags only — no structural or template changes. It was then immediately executed against [LAB] Requirements (a different diagram type, different phase, 45 requirements) and produced a correct, complete artifact on the first run with no debugging.

This confirms the routine_def variable parameterisation approach works as designed. The diagram_name and phase variables are sufficient to redirect the full execution — browse, resolve, fabric generation, parsing, CSV build, artifact write, push, and summary — to any target diagram without author intervention.

The engineer also confirmed that the produced artifact is immediately readable in the artifact web viewer as a dashboard, validating the end-to-end pipeline from model → routine → artifact → viewer in a single session. This is the first complete demonstration of that pipeline on the EBSD_300 project.

Connection to OBS-014 (artifact store reduces chat to a trigger): the [LAB] Requirements extraction required a single invocation message. No setup, no context reconstruction, no interactive debugging. The routine held the method; the variable held the target. One message, one clean artifact.

Connection to OBS-011 (CNC analogy): the engineer loaded the stock ([LAB] Requirements), pressed start, and the machine delivered the finished part. Routine library is proving out.

**References:** [Extract Requirements from Capella Diagram (routine_def)](/packages/EBSD_300/artifacts/2026-06/434be3f3-e826-41cf-af6a-31875f5b6aed/content.json)
