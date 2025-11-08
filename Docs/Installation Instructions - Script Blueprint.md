# 🧩 Implementation Guide  
### RBC — Update Bias (Robust EWMA) • Script Blueprint v1.5

---

## Overview

This blueprint provides a **generic, reusable bias updater** that applies a robust **Exponentially Weighted Moving Average (EWMA)** to `(Actual − Estimate)` and updates a numeric bias helper accordingly.  
It is used by all RBC Producer and Safety Net automations for **Demand**, **PV**, and **Temperature** domains.

**Key functions**
- Calculates and smooths the daily bias automatically.
- Caps extreme deviations.
- Ensures idempotence via a “last processed date” text helper.
- Logs to the Logbook for traceability.

---

## ⚙️ Step-by-Step Installation

### **Step 1 — Locate your HA Configuration Folder**
In Home Assistant, open the File Editor, Studio Code Server, or SSH.  
Find the folder containing your `configuration.yaml`.

---

### **Step 2 — Create the Blueprint Folder**
Create this path if it doesn’t already exist:

config/blueprints/script/rbc/

yaml
Copy code

---

### **Step 3 — Add the Blueprint File**
Create the file:

config/blueprints/script/rbc/rbc_update_bias_robust_ewma_v1_5.yaml

yaml
Copy code

Paste the entire YAML blueprint (no edits). Save and close.

---

### **Step 4 — Reload Scripts**
In Home Assistant UI:

**Developer Tools → YAML → Reload Scripts**  
*(Or restart HA if preferred.)*

The blueprint will now appear in the “Create Script from Blueprint” list.

---

### **Step 5 — Create a Script from the Blueprint**

**Navigate to:**  
`Settings → Automations & Scenes → Scripts → + Create Script → From Blueprint`

Select:

> **RBC — Update Bias (Robust EWMA) • Script Blueprint v1.5**

---

### **Step 6 — Bind Inputs**

For each instance of this script, assign:

| Input | Description | Example Entity |
|-------|--------------|----------------|
| **Actual total (entity)** | The measured total for the day or period | `sensor.brenchley_total_load_today_kwh` |
| **Estimate total (entity)** | The forecast or expected value | `input_number.dai_expected_usage_daily_kwh` |
| **Bias helper (input_number)** | Where the computed bias is stored | `input_number.rbc_bias_demand_daily` |
| **Last processed period (input_text)** | Stores the last processed date to avoid duplicates | `input_text.rbc_last_period_demand_daily` |

Optional tunables:

| Parameter | Default | Notes |
|------------|----------|-------|
| **Half-life (days)** | 4 | Controls smoothing speed; higher = slower response |
| **Error clip ratio** | 0.5 | Clipping bound relative to Estimate (min 0.5 abs) |
| **Max absolute bias** | 60 | Prevents runaway learning |
| **Require non-zero inputs** | true | Skips update if both Actual & Estimate are zero |
| **Log level** | normal | quiet / normal / verbose |

---

### **Step 7 — Recommended Domain Bindings**

| Domain | Actual | Estimate | Bias Helper | Last Period Text |
|---------|---------|-----------|--------------|------------------|
| **Demand Daily** | `sensor.brenchley_total_load_today_kwh` | `input_number.dai_expected_usage_daily_kwh` | `input_number.rbc_bias_demand_daily` | `input_text.rbc_last_period_demand_daily` |
| **Evening 16–22 h** | `sensor.dai_actual_demand_16_22_kwh` | `input_number.dai_forecast_demand_16_22_kwh` | `input_number.rbc_evening_bias_kwh` | `input_text.rbc_last_period_evening_demand` |
| **PV Daily** | `<YOUR_PV_TODAY_KWH_SENSOR>` | `input_number.rbc_adjusted_pv_daily_kwh` | `input_number.rbc_bias_pv_daily` | `input_text.rbc_last_period_pv` |
| **Temperature Min** | `sensor.rbc_outside_temperature_today_min_degc` | `input_number.rbc_temperature_estimate_low_c` | `input_number.rbc_temp_bias_c` | `input_text.rbc_last_period_temperature` |

*(Replace entities as needed — keep Actual = measured, Estimate = forecast, Bias = output, Last Period = stamp.)*

---

### **Step 8 — Connect the Blueprint Script to Producers**

Update or verify that your **Producer** and **Safety Net** automations call the appropriate script entity:

| Domain | Script Entity |
|---------|----------------|
| Demand Daily | `script.rbc_update_bias_demand_daily` |
| Evening 16–22 h | `script.rbc_update_bias_evening_16_22` |
| PV Daily | `script.rbc_update_bias_pv_daily` |
| Temperature Min | `script.rbc_update_bias_temperature` |

No other automation logic changes are needed.

---

### **Step 9 — Verify Operation**

- Open **Logbook** and confirm entries like  
  _“RBC Bias Updater – Updated bias for YYYY-MM-DD → X.XX”_
- Ensure bias helper (`input_number.rbc_bias_*`) changes as expected.
- Ensure last period helper (`input_text.rbc_last_period_*`) updates to today’s date.
- A second run on the same day should correctly report “Skipped: already processed period …”

---

### **Step 10 — Tuning & Safety Checklist**

- ✅ Start with defaults (half-life = 4, clip ratio = 0.5, max bias = 60).  
- ⏳ Increase half-life → smoother trend.  
- ⚡ Decrease half-life → faster adaptation.  
- 🧱 Lower clip ratio → stronger suppression of spikes.  
- 🚫 Keep require-nonzero = true unless you explicitly want 0-valued learning.

---

## ✅ Acceptance Checks

| Check | Expected Outcome |
|-------|------------------|
| Script can be created from blueprint | ✅ Appears under “Create from Blueprint” |
| Producers point to correct script entity | ✅ |
| Bias helper changes after run | ✅ |
| Last period helper updates | ✅ |
| Second run same day skips | ✅ |

---

## 🔄 Rollback

Disable or delete the script instances you created.  
The blueprint file itself is safe to keep—it does nothing until referenced.

---

## 🧾 Redactions Report

- **None applied.**  
  This blueprint contains no hard-coded entity IDs, credentials, or sensitive references.  
  It is fully generic and safe for public distribution.

---

*Document created by ChatGPT (GPT-5 Thinking) — November 2025*