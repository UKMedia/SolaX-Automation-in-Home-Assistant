# SolaX-Automation-in-Home-Assistant

# ⚡ Home Assistant — DAI / RBC Energy Automation Framework (SolaX)

This repository contains my **DAI (Dynamic AI-driven Inverter control)** and **RBC (Robust Bias Correction)** automations, scripts, helpers, and supporting YAML for **SolaX Hybrid inverter** systems running under **Home Assistant**.

It is intended for **medium-level HA users** who want a **coherent, scalable energy-automation architecture** that goes beyond one-off automations: day-ahead planning, safe inverter control, and bias-learning loops that adapt to real usage and PV.

---

## 🧭 What DAI / RBC Does (at a glance)

- **DAI — Planning & Control**
  - Day-ahead **grid-charge planning** (respecting tariff windows)
  - **Mode orchestration** (Self-Use, Charge, Force-Discharge) with safety guards
  - **Evening Buffer** logic to ensure evening supply without surprise imports
  - Idempotent writes with confirmation/dwell and restart-safe behaviour

- **RBC — Learning & Stability**
  - Bias learning for **PV Daily**, **Demand Daily**, and **Evening (16–22)** windows
  - “Safety-first” adjusted forecasts to prevent under-charging events
  - Upward-only same-day adjustment and daily lock points
  - Consistent mapper pipeline (Bias → Percent suggestions)

> This framework is designed to be **predictable**, **explainable**, and **safe**, with clear separation of responsibilities across automations and scripts.

---

## 🏗 Repository Structure

| Path            | Purpose |
|-----------------|---------|
| `automations/`  | All DAI/RBC automations (one job per automation; UI-editor safe) |
| `scripts/`      | Reusable script blocks (e.g., bias update, mapper calls, mode writes) |
| `sensors/`      | Template/derived sensors used by planners and learners |
| `helpers/`      | Helper definitions (IDs/names list, not config.yaml blobs) |
| `dashboards/`   | Optional Lovelace cards (planning status, RBC visuals, trace aids) |
| `docs/`         | Design notes, governance rules, helper maps, diagrams |

> Entity IDs will likely differ in your setup. Search/replace carefully and test in a non-critical window.

---

## ✅ Prerequisites

- **Home Assistant** (recent core; UI automation editor in use)
- **SolaX** inverter entities available in HA (native integration / MQTT / local API)
- **Octopus Energy** (or similar ToU tariff) if you want charge planning
- Comfort with:
  - Creating **helpers** (input_number / input_boolean / input_text / input_select)
  - Importing YAML automations via the **UI editor** or splitting packages
  - Reviewing **Automation Traces** and **Logbook**

---

## 🔑 Core Design Principles

- **One automation = one responsibility** (orchestrators call scripts; scripts do work)
- **Helpers as shared state** (explicit reads/writes; no silent side-effects)
- **Scenes/flags as control layers** to avoid automations fighting each other
- **Master-only inverter writes** (single authority to the inverter write path)
- **UI-editor-safe YAML** (no anchors/includes; clear `trigger/condition/action`)
- **Idempotent, confirm-and-dwell writes** to prevent flapping

---

## 🛡️ Safety & Governance Summary

- Writes are **guarded** (time windows, tariff pairing, SoC and mode checks).
- Every write path logs to **Logbook** for auditability.
- Automations include **trigger IDs** and **default branches**.
- Restart-safe: scheduled **safety nets** (e.g., 23:40/23:55 bias/mapper stamps).
- Bias flow uses a **single, shared mapper** (Bias → % suggestion) for consistency.

> You still **must** validate against your hardware and tariff. Start conservative.

---

## 🚀 Getting Started (minimal path)

1. **Clone** this repo and open in VS Code (via GitHub Desktop is fine).
2. In HA, **create the referenced helpers** shown in `helpers/HELPERS_MAP.md`.
3. Import automations **one by one**, starting with:
   - RBC bias producers (Daily, Evening)
   - Bias→% mapper
   - DAI planners (Grid-Charge Planner, Evening Buffer)
4. Adjust entity IDs (battery SoC, inverter mode entities, tariff select, etc.).
5. Test in a **harmless window** (no overnight charge yet). Review trace/logs.
6. Enable grid-charge writes last, after dry-run validation.

---

## 📊 Dashboards (optional)

The `dashboards/` folder includes example cards for:
- **Planning status** (planned vs adjusted energy)
- **Bias traces** (Daily PV, Daily Demand, Evening 16–22)
- **Charge windows** and **resulting inverter commands**

Import these after the core automations are stable.

---

## 🧪 Troubleshooting Tips

- If an expected write doesn’t happen, check:
  - The **mode/scene flag** isn’t blocking
  - The **helper map** (which automation “owns” a write)
  - **Tariff pairing/desync** rules (Cosy/Flux alignment)
  - The **safety net** ran (23:40/23:55) and stamped bias/percent
- Use **Automation Trace** + **Logbook** together for root-cause.

---

## 🤝 Contributing / Issues

- Open an **Issue** for questions or adaptation help (other inverters, tariffs)
- PRs welcome for:
  - New dashboards
  - Additional learners (morning window, weekend profiles)
  - Integrations with other tariff providers

---

## ⚠️ Disclaimer

This framework can **control real charging/discharging hardware**.  
Improper configuration may affect battery health or billing.  
Adopt conservatively, validate with traces, and roll out gradually.

---

## 📜 License

MIT (unless otherwise noted within files).
