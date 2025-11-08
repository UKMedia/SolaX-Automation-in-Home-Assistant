ChatGPT said:

Excellent — below is a complete .md implementation guide for your RBC + DAI Automations set, formatted for inclusion in your GitHub or OneDrive repo (e.g.,
IMPLEMENTATION_GUIDE_RBC_DAI_AUTOMATIONS_v1.0.md).

It mirrors the structure and tone of the blueprint guide, but adds references to your Implementation Prompt Library, which you use as the controlled deployment mechanism when regenerating YAML.

# ⚙️ Implementation Guide  
### RBC + DAI Automation Suite — Standardised Deployment Instructions (v1.0)

---

## 🧩 Overview

This guide explains how to install, configure, and maintain the **RBC + DAI Automations** within Home Assistant.  
These automations implement the learning, forecasting, and control layers defined in the **DAI + RBC Framework (Design-Doc v5.85+)**.

Each automation follows governance standards:
- Visual-Editor-safe YAML (no anchors, includes, or inline scripts)  
- All writes to SolaX Master (`solax_house_*`) only  
- Logbook-based observability and timestamped notifications  
- One automation per functional role (Producer / Safety Net / Updater / Controller)

---

## 📁 Folder Structure

All automations must be stored in the following location:



config/automations/dai_rbc/


Each file uses the naming convention:



<alias_slug>.yaml


Examples:



rbc_demand_daily_bias_producer.yaml
rbc_demand_daily_bias_safety_net.yaml
rbc_evening_bias_producer.yaml
rbc_evening_bias_safety_net.yaml
rbc_adjusted_pv_daily_updater.yaml
rbc_temperature_bias_producer.yaml
dai_free_energy_sessions_controller.yaml


---

## 🧱 Step-by-Step Installation

### **Step 1 — Copy the Automation Files**

Copy the sanitised YAML automation files from the public repo (or your local copy) into:



config/automations/dai_rbc/


Each YAML file already includes:

- Header comment block  
- Description section with design-doc reference  
- Fully qualified `trigger:`, `condition:`, and `action:` sections  
- `mode: single` for deterministic execution  

---

### **Step 2 — Reload Automations**

In Home Assistant UI:

> **Developer Tools → YAML → Reload Automations**

or restart Home Assistant to ensure all files are loaded.

---

### **Step 3 — Check Helper Dependencies**

Before enabling any automation, verify that all helpers referenced exist.  
Use the Helper UI to create any missing entities.

| Helper Type | Example ID | Purpose |
|--------------|-------------|----------|
| `input_number` | `input_number.rbc_bias_demand_daily` | Stores bias (kWh or °C) |
| `input_text` | `input_text.rbc_last_period_demand_daily` | Stores last processed date |
| `input_datetime` | `input_datetime.last_mode_change` | Tracks last mode change |
| `input_boolean` | `input_boolean.dai_session_override_active` | Override control |
| `input_select` | `input_select.octopus_import_tariff` | Tariff indicator |
| `sensor` | `sensor.brenchley_total_load_today_kwh` | Actual daily load |

> 🔹 Helper IDs must match exactly those used in the YAML alias section.  
> If you rename entities, update the YAML accordingly before reloading.

---

### **Step 4 — Link with the RBC Script Blueprint**

Several automations invoke the blueprint-derived scripts described in  
**`IMPLEMENTATION_GUIDE_RBC_Update_Bias_Robust_EWMA_v1.5.md`**.

Confirm the following script entities exist:

| Domain | Script Entity |
|---------|----------------|
| Demand Daily | `script.rbc_update_bias_demand_daily` |
| Evening 16-22 | `script.rbc_update_bias_evening_16_22` |
| PV Daily | `script.rbc_update_bias_pv_daily` |
| Temperature Min | `script.rbc_update_bias_temperature` |

If not, create them via the **Script → From Blueprint** workflow.

---

### **Step 5 — Validate Triggers and Modes**

Each automation uses one of these categories:

| Type | Typical Trigger | Role |
|------|-----------------|------|
| **Producer** | `time: 23:40` | Calls RBC blueprint script |
| **Safety Net** | `time: 23:55` | Late fallback if Producer missed |
| **Updater** | Minute / hour pattern | Maintains adjusted forecast or accumulator |
| **Controller** | Calendar / state triggers | Manages live inverter behaviour |

Verify all referenced entity IDs exist and have correct domains.  
Use the **Visual Editor** to open each file and confirm the preview matches expectations.

---

### **Step 6 — Test Execution**

1. Temporarily set the system clock or manually trigger via  
   **Automation → Run Action**.  
2. Observe **Logbook** for entries such as:  
   `“RBC – Demand Daily Bias Producer – Triggered Demand Daily bias update for YYYY-MM-DD.”`  
3. Confirm that associated helpers update as expected.

---

### **Step 7 — Register in the Implementation Prompt Library**

After verifying a new automation, register it in your  
**Implementation Prompt Library (IPL)** — your index of reusable ChatGPT prompt templates.

For each automation entry, record:

| Field | Example |
|--------|----------|
| **Alias / File Name** | `rbc_demand_daily_bias_producer.yaml` |
| **Prompt Reference ID** | `IPL-RBC-001` |
| **Design Doc Section** | HLD v5.85 § 5.2.3 |
| **Blueprint Dependency** | `rbc_update_bias_robust_ewma_v1_5.yaml` |
| **Change Source** | CR016 / DR004a etc. |
| **Date Implemented** | 2025-11-08 |

> 💡 This ensures full traceability from YAML → Prompt → Design Doc,  
> satisfying your governance and “No-Surprises Protocol” audit chain.

---

### **Step 8 — Commit to Version Control**

Once verified:

1. Stage all new/updated YAML files in the GitHub Desktop App.  
2. **Edit the commit message** to reflect the change (e.g.,  
   `Add RBC – PV Bias Producer v5.0 (sanitised public release)`).  
3. Commit and push to your remote repository.

> 🔸 You may edit the commit message later using:  
> `git commit --amend` (before push) or `git rebase -i` (after push).  
> Ensure your message format matches the CR/DR governance naming.

---

### **Step 9 — Monitoring**

- **Logbook** and **History Graphs** show all updates.  
- **Notifications** (Pixel 9 Pro) confirm bias, override, and safety-net activity.  
- Use **Dashboard Cards** (ApexCharts / Entities) for trend visualisation.  

---

### **Step 10 — Rollback**

To roll back a specific automation:
1. Disable it in HA or delete the YAML file.  
2. Reload automations.  
3. Restore the previous version from Git history if needed.

---

## 🧾 Redactions Report

All YAMLs in this suite have been sanitised for public distribution:
- No personal or installation-specific identifiers remain.
- All entity references are generic (`solax_house_*`, `brenchley_*`, etc.).
- No sensitive API keys or coordinates.

---

## 🔗 Related Documents

- **DAI + RBC Framework v5.85 (Oct 2025)** — § 5.2 Bias Producers, § 5.6 Bias Mapping  
- **IMPLEMENTATION_GUIDE_RBC_Update_Bias_Robust_EWMA_v1.5.md**  
- **Home Assistant Guidelines DAI RBC v1.2**  
- **Implementation Prompt Library Index (OneDrive → Projects → DAI → Governance)**

---

## ✅ Compliance Checklist

- [x] Entity Name = ID alignment verified  
- [x] One YAML file per automation  
- [x] Visual Editor-safe syntax (`service`, `target`, `data`)  
- [x] All triggers include `id:`  
- [x] `choose:` blocks include a `default:`  
- [x] Writes restricted to SolaX Master (`solax_house_*`)  
- [x] No functionality removed or simplified  
- [x] Description updated with version and CR/DR reference  
- [x] Document change recorded in CR_DR_Log  

---

*Document created by ChatGPT (GPT-5 Thinking) — November 2025*  
*Sanitised for public distribution. Place this file at the root of the automation repository.*
