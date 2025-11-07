# SolaX-Automation-in-Home-Assistant

# ⚡ Home Assistant – DAI / RBC Coordination Framework (SolaX & Octopus Energy)

This repository contains the **DAI (Dynamic AI-driven Inverter control)** and **RBC (Robust Bias Correction)** framework I use to coordinate a **SolaX Hybrid inverter** system under **Octopus Energy** tariffs. It focuses on *how to run a multi-automation energy stack safely and predictably* — where planning, learning, and inverter control all interact.

- **Site context**
  - Parallel SolaX installation: **2 × X1 Gen4 Hybrid** inverters
  - **23 kWh** total battery storage
  - **Fully electric house with ASHP** (air-source heat pump)
  - Octopus **Flux** (export-centric) and **Cosy** (time-of-use) tariff support
  - Automatic handling for **Free Energy** and **Energy Saving** sessions

> This repo is about **architecture and coordination**. Device/entity names will differ in your setup.

---

## 🎯 Goals of the Integration

- **Minimise Grid Import**
- **Minimise PV Clipping** (avoid throwing away solar at mid-day)
- **Maximise Export Revenue** (especially on Flux)
- **Auto-correct forecast error** via **RBC bias** so estimates track reality over time
- **Automatically support** Octopus **Free Energy** and **Energy Saving** sessions

---

## 🧠 DAI vs RBC — Who does what?

| Layer | Responsibility | Output | Writes to Inverter? |
|------|-----------------|--------|---------------------|
| **RBC** | Learns how reality differs from forecasts (kWh / °C) and adjusts expectations | Bias & Adjusted values | **No** |
| **DAI** | Uses Adjusted values to plan charge targets, evening buffer %, and mode decisions | Safe, timed actions | **Yes — Master-only** |

- **RBC = learn & stabilise.**  
- **DAI = plan & act.**

All inverter control commands are **idempotent**, **dwell-protected**, and sent to the **Master** inverter only (`solax_house_*`). The second inverter is **telemetry-only** for reads.

---

## 🌡️ Why ASHP Matters (Seasonality)

With a **fully electric house and ASHP**, winter demand can be **significantly higher** and more volatile than summer. To avoid under-charging on cold days, the framework includes an **RBC Minimum Temperature** learning domain that corrects the **forecast minimum temperature** toward **actual** overnight lows. This adjusted min-temp feeds a **Cold Day** classifier used by the DAI Grid-Charge Planner, so the system carries more energy into colder mornings *without guesswork*. (Terminology aligns with the High Level Design v1.2.) :contentReference[oaicite:1]{index=1}

---

## 🔁 RBC Learning Domains (aligned to HLD v1.2)

All RBC families use the same **canonical** bias pattern (producer → safety net → adjusted updater/mapper → watchdog). :contentReference[oaicite:2]{index=2}

| RBC Domain | Learns Bias In… | Stores | Consumed By |
|-----------|------------------|--------|-------------|
| **PV Daily** | Solar generation (kWh) | `input_number.rbc_bias_pv_daily` | DAI solar planning |
| **Demand Daily** *(reference pattern)* | Whole-day usage (kWh) | `input_number.rbc_bias_demand_daily` | DAI grid-charge planning |
| **Evening Window (16–22)** | Evening consumption (kWh) → **% mapper** | `input_number.rbc_evening_bias_kwh` | DAI Evening Buffer controller |
| **Minimum Temperature** | Forecast vs actual overnight low (°C) | `input_number.rbc_temp_bias_c` | DAI Cold-Day classification → planning |

**Octopus sessions:** Free-Energy / Energy-Saving events are surfaced to DAI as **planning constraints/windows**. DAI adjusts targets, avoids clashes, and prioritises cheap/free import or curtailed export as appropriate.

---

## 🔧 DAI Planning & Control

DAI uses RBC **Adjusted** values + tariff windows to:
- Decide **whether/how much** to charge overnight (Cosy) or time windows
- Maintain an **Evening Buffer %** so early evening doesn’t hit the grid
- Balance **PV clipping mitigation** by raising midday consumption/charging when surplus is likely
- Optimise **Flux** export moments without starving the evening

All actions log to the **Logbook** and are traceable in **Automation Traces**.

---

## 📂 Repository Layout

| Path | Contents | Purpose |
|------|----------|---------|
| `automations/` | Planners, bias producers, safety nets, orchestrators, watchdogs | Controls when things happen |
| `scripts/` | Shared compute/mapping routines (e.g., Bias→%) | Keeps logic consistent & reusable |
| `sensors/` | Derived/statistics sensors (Demand, PV, Min Temp) | Make state and extremes visible |
| `helpers/` | Required input helpers (numbers/booleans/text) | Persist learned/planned state |
| `dashboards/` | Optional Lovelace cards | See what the system is thinking |
| `docs/` | Prompt Library, Governance Checklist, Workflow Diagram | Maintainability & onboarding |

---

## ✅ Getting Started (practical)

1. **Create helpers** listed in `helpers/HELPERS_MAP.md`.
2. Import automations in this sequence:
   1) RBC Bias Producers (Daily Demand, PV Daily, Evening, **Min Temp**)  
   2) RBC Safety Nets (+15 min)  
   3) **Evening Bias → Percent Mapper**  
   4) RBC Watchdog (23:59 audit)  
   5) DAI Grid-Charge Planner (Cosy/Flux aware)  
   6) DAI Evening Buffer Controller
3. Run in **“no inverter writes”** mode first; validate traces & logbook.
4. Enable write actions **last** (Master inverter only).

---

## 🧭 Why this stays stable at scale

- **One automation = one responsibility**
- **Helpers store state** (no hidden/inferred state)
- Shared **Bias engine** across all RBC domains for consistent behaviour
- **Guard rails** on every write (windows, SoC, tariff pairing, idempotence)
- **Master-only** inverter writes to prevent race conditions

---

## ⚠️ Notes & Safety

- Start conservatively; validate over several days.
- Don’t place your HA config/repo inside **OneDrive/iCloud/Dropbox** sync folders.
- Test Octopus session handling on a mild day before relying on it during extremes.

---

## 📝 License

MIT — contributions welcome if you have improvements, dashboards, or alternative heuristics that play nicely with this structure.
