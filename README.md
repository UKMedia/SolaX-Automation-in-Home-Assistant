# ⚡ Home Assistant – DAI / RBC Coordination Framework (SolaX & Octopus Energy)

This repository contains the **DAI (Dynamic AI-driven Inverter control)** and **RBC (Robust Bias Correction)** framework I use to coordinate a **SolaX Hybrid inverter** system under **Octopus Energy** tariffs.  
It focuses on *how to run a multi-automation energy stack safely and predictably* — where **planning**, **learning**, and **inverter control** all interact.

> This repository is a **reference implementation**.  
> Your entity names and helper IDs will differ; adapt them using the included templates and ChatGPT prompts.

Please refer to the Docs section for Installation instructions

---

## 📦 Reference Implementation Scope
This project is released as a **Reference Implementation + Assisted Rebuilder** rather than a turnkey package.

| Component | Purpose |
|------------|----------|
| **YAML code** | Working DAI + RBC automations/scripts as reference |
| **Design docs** | High-Level Design + Quick Start summaries |
| **Templates** | `entity_mapping_template.md` and `helper_manifest.md` |
| **Prompt Library** | ChatGPT prompts to regenerate, compare, and verify YAML |
| **Blueprints (optional)** | Planned later for the stable RBC kernel only |

Users adapt locally by completing the mapping templates and running the **Regenerator Prompt**.  
This avoids hard-coded entity names while keeping every configuration safe and self-auditing.

---

## ✅ Getting Started
1. **Complete** the templates:  
   `templates/entity_mapping_template.md` and `templates/helper_manifest.md`.  
2. **Use** the **Implementation Prompt Library** in `/prompts/` to:  
   - Extract entities from your YAML  
   - Verify helper alignment  
   - Regenerate code for your setup  
3. **Import automations** in this sequence:  
   - RBC Bias Producers → Safety Nets → Mapper → Watchdog → DAI Planners → DAI Buffer Controller  
4. Run through **Monitor-only → Dry-Run → Live Writes (Master only)**.  
5. Validate with the acceptance-test prompts before enabling inverter writes.

---

## 🎯 Goals of the Integration
- Minimise **Grid Import**  
- Minimise **PV Clipping** (avoid throwing away solar mid-day)  
- Maximise **Export Revenue** (especially on Flux)  
- **Auto-correct forecast error** via RBC bias learning  
- Seamlessly support Octopus **Free Energy** and **Energy Saving** sessions  

---

## 🧠 DAI vs RBC — Who Does What?

| Layer | Responsibility | Output | Writes to Inverter? |
|-------|----------------|---------|---------------------|
| **RBC** | Learns how reality differs from forecasts (kWh / °C) | Bias & Adjusted values | **No** |
| **DAI** | Uses Adjusted values to plan charge targets and mode decisions | Safe, timed actions | **Yes – Master only** |

- **RBC = learn & stabilise**  
- **DAI = plan & act**

All inverter commands are **idempotent**, **dwell-protected**, and written only to the **Master inverter** (`solax_house_*`).  
The second inverter is **telemetry-only**.

---

## 🏠 Site Context
- Parallel SolaX installation: **2 × X1 Gen4 Hybrid** inverters  
- **≈ 23 kWh battery storage**  
- **Fully electric home with ASHP** (Air-Source Heat Pump)  
- Octopus **Flux** (export-centric) and **Cosy** (time-of-use) tariff support  
- Automatic handling for **Free Energy** and **Energy Saving** sessions  

---

## 🌡️ Why ASHP Matters (Seasonality)
With a fully electric ASHP house, winter demand is far higher and more volatile.  
The framework’s **RBC Minimum Temperature** domain learns forecast vs actual overnight lows to drive a **Cold-Day flag** used by the DAI Grid-Charge Planner, ensuring extra energy is carried into cold mornings automatically (HLD v1.2 alignment).

---

## 🔁 RBC Learning Domains
All RBC domains share the same canonical bias pattern *(producer → safety net → mapper → watchdog)*.

| Domain | Learns Bias In | Stores Result | Used By |
|---------|----------------|----------------|---------|
| **PV Daily** | Solar generation (kWh) | `input_number.rbc_bias_pv_daily` | DAI solar planner |
| **Demand Daily** | Whole-day usage (kWh) | `input_number.rbc_bias_demand_daily` | DAI grid-charge planner |
| **Evening 16-22** | Evening use (kWh → %) | `input_number.rbc_evening_bias_kwh` | DAI Evening Buffer |
| **Minimum Temp** | Forecast vs actual (°C) | `input_number.rbc_temp_bias_c` | DAI Cold-Day logic |

---

## 🔧 DAI Planning & Control
DAI combines RBC Adjusted values with tariff windows to plan:
- Overnight charging (Cosy) or timed windows (Flux)  
- **Evening Buffer %** so early evening stays off-grid  
- **PV clip mitigation** via mid-day charging  
- **Flux export optimisation** without evening shortfall  

All actions are logged and traceable through HA Traces and the Logbook.

---

## 🧭 Design Principles
- **One automation = one responsibility**  
- **Helpers store state** (no hidden logic)  
- **Shared Bias engine** across RBC domains  
- **Strong guard-rails** on every write (window, SoC, tariff pairing, idempotence)  
- **Master-only writes** prevent race conditions  

---

## ⚠️ Safety & Support
- Validate over several days before live writes.  
- Keep HA config outside cloud-sync folders (OneDrive, iCloud, Dropbox).  
- Use acceptance-test prompts before enabling inverter writes.  
- Community support only – issues and PRs welcome.

---

## 📝 License
MIT License • Use at your own risk • Credit welcome if you fork or extend.

---

# 🧰 Implementation Prompt Library (`/prompts/`)

| File | Purpose |
|------|----------|
| **01_extract_entities.md** | Parse YAML and output all referenced entities/helpers (CSV or JSON). |
| **02_compare_yamls.md** | Behavioural diff report between reference and user-generated YAML. |
| **03_regenerator.md** | Generate compliant YAML from design + entity/helper maps. |
| **04_helper_auditor.md** | Check helper presence and unit/min/max/step vs manifest. |
| **05_preflight_planner.md** | Run dry-run SoC planning without inverter writes. |
| **06_acceptance_tests.md** | Auto-generate “Trigger → Expected Outcome” acceptance cases. |
| **07_entity_map_builder.md** | Build an entity-map CSV directly from pasted YAML. |
| **08_risk_review.md** | Identify potential grid import/export risks and suggest guards. |
| **09_doc_sync_snippet.md** | Draft Design Notes for repo documentation. |

Each file contains a complete ChatGPT prompt — copy, paste, run.  
All prompts are designed for HA users who know YAML but are new to ChatGPT.  
Outputs are structured for quick import into spreadsheets or docs.
