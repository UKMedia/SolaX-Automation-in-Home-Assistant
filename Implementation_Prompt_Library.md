# 🧰 Implementation Prompt Library

Each prompt below can live as a **separate file** inside your `/prompts/` folder  
(e.g. `/prompts/01_extract_entities.md`, `/prompts/02_compare_yamls.md`, etc.).  
Alternatively, you can keep this as a **single combined library file** if you prefer to manage them in one place.  
Each section begins with a clear `##` heading so you can easily split them later if needed.

All prompts are designed for **Home Assistant–experienced users** who are new to prompt-based code generation.  
Outputs are structured as CSV, JSON, or Markdown tables — no commentary — so they can drop directly into spreadsheets or docs.

---

## 01_extract_entities.md
**Purpose:** Identify all entities and helpers used in any YAML (automations or scripts).

```text
You are an expert Home Assistant YAML reviewer.
Analyze the YAML files I provide and extract all referenced entities and helpers.

Output: a CSV with columns:
role,entity_id,type,domain,used_in,file,notes

Rules:
- role ∈ {trigger, condition, variable-source, action-read, action-write, service-target, blueprint-input}
- type ∈ {sensor,input_number,input_boolean,input_text,input_datetime,select,scene,script,automation,template_sensor}
- domain = entity domain.
- used_in = alias of the automation/script.
- file = filename I give you (or "stdin" if pasted).
- notes = include unit or step hints if visible and flag if it’s a Master-only write target.

Return only the CSV table. No narrative.
```

---

## 02_compare_yamls.md
**Purpose:** Compare your YAML to the reference YAML and highlight meaningful differences.

```text
You are a Home Assistant diff reviewer.
Compare two YAML bundles:
A = reference (mine)
B = candidate (yours)

Output: Markdown sections:
1) Summary: Same/Drift verdict per automation alias
2) Trigger parity table
3) Condition deltas
4) Writes & targets – include confirm/idempotence check
5) Helper/unit mismatches
6) Safety invariants (override gating, reserve floor, confirm-after-write)
7) Risk flags (possible overcharge or unwanted import)

Be concise and structured. No raw line diffs.
When ready, I will supply A and B.
```

---

## 03_regenerator.md
**Purpose:** Rebuild YAML for another user’s system using design logic + mapping files.

```text
You are an HA code generator.
Inputs provided:
- Design summary
- entity_map.csv
- helper_manifest.csv

Task: generate YAML automations and scripts that match the design behaviour
but use my entities and helpers.

Rules:
- Visual-Editor–safe syntax (service + target + data)
- One automation per code block
- Include alias and description header with version + short change history
- Master-only writes; no renames
- Inline comments for actions; none for dashboards

At the end, produce a 10-line “Compliance Checklist” confirming:
entity alignment, triggers have id, choose has default, times quoted,
no YAML anchors, monitor→dry-run→live gates, idempotent writes with confirm.

Design summary: …
entity_map.csv: …
helper_manifest.csv: …
```

---

## 04_helper_auditor.md
**Purpose:** Confirm helpers exist and match expected configuration.

```text
You are a Helper Auditor.
Compare my helper_manifest.csv against the YAML I provide.

Output a Markdown table:
helper_id | expected (unit/min/max/step) | found | status | fix

Rules:
- Status ∈ {ok, missing, wrong_unit, wrong_range, unused}
- If the helper exists but unused, mark "unused"
- No commentary, just table
```

---

## 05_preflight_planner.md
**Purpose:** Simulate DAI planning (no inverter writes).

```text
You are a DAI Preflight Planner.
Read my DAI configuration and today’s inputs (price schedule, forecasts).

Compute the plan and output a table:
time | price | cheap? | pv_forecast | demand_forecast | planned_soc | action (none/plan charge/hold)

Then summarize:
- earliest planned charge start
- planned end time
- lowest and highest SoC in plan
- any reserve floor breaches

Return only the table and summary.
```

---

## 06_acceptance_tests.md
**Purpose:** Auto-generate test cases for verification.

```text
You are an HA test designer.
Given an automation summary, generate 8–12 acceptance tests formatted:
Trigger → Expected Outcome

Include edge cases for:
- cheap window start/end
- override ON/OFF
- end-of-window release
- confirm-after-write
- safety net fallback

Automation summary: …
```

---

## 07_entity_map_builder.md
**Purpose:** Build an entity map from YAML (quick start).

```text
You are an Entity Map Builder.
From the YAML I paste, create a CSV with:
alias,role,entity_id,expected_domain,notes

List only entities that a user must remap
(no HA system or built-ins).

Output CSV only.
```

---

## 08_risk_review.md
**Purpose:** Identify potential risks before enabling inverter writes.

```text
You are a Home Assistant risk assessor.
Review this YAML’s triggers, conditions, and actions.

List two bullet lists:

**Risks**
- Each potential scenario that could cause unintended import/export, over-discharge, or missed charge.

**Mitigations**
- Specific conditions, guards, or helper checks to add.

Be concise and safety focused.
```

---

## 09_doc_sync_snippet.md
**Purpose:** Draft “Design Notes” text for documentation sync.

```text
You are a documentation assistant.
Write a 150–180 word ‘Design Notes’ section summarising:
- Purpose of the automation
- Key inputs and outputs
- Interaction with other DAI/RBC layers
- Any safety invariants (override control, idempotence, window guards)

Style: clear, factual, Markdown-friendly.
No YAML or bullet formatting needed beyond bold headings.
```

---

✅ **Usage tip:**  
Copy any prompt into ChatGPT, paste your YAML or data when asked, and save the output to `/analysis/` or `/docs/` as needed.  
Each prompt enforces your governance standards (entity alignment, Master-only writes, compliance checklists) while staying beginner-friendly for prompt-based users.
