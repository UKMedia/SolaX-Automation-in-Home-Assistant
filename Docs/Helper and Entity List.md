# 🧩 DAI + RBC Home Assistant — Helpers & Entities Map  
*(Generated for Public Distribution — Sanitised, Nov 2025)*

---

## 1️⃣ Helpers Map — Template

| Friendly name | Entity ID | Helper type | Unit / Options | Range / Step | Owner (DAI / RBC / Octopus) | Purpose / Notes |
|---|---|---|---|---|---|---|
|  |  | input_number / input_boolean / input_select / input_text / input_datetime |  |  |  |  |

---

## 2️⃣ Entities Map — Template

| Entity ID | Friendly name | Domain | Source / Integration | Used in (card / section) | Notes |
|---|---|---|---|---|---|
|  |  | sensor / select / number / climate / light / person | SolaX / ApexCharts / Met Office / … |  |  |

---

## 3️⃣ Helpers Map — Pre-Populated (from Brenchley Dashboard)

| Friendly name | Entity ID | Type | Unit / Options | Range / Step | Owner | Purpose / Notes |
|---|---|---|---|---|---|---|
| Detailed View | input_boolean.detailed_view | input_boolean | On / Off | — | HA | Toggle extra cards |
| Evening buffer Auto Calc ? | input_boolean.dai_evening_buffer_auto | input_boolean | On / Off | — | DAI | Auto vs manual buffer |
| Planner Override (Tomorrow) | input_boolean.dai_session_override_active | input_boolean | On / Off | — | DAI | Prevent planner raise |
| Debug Mode (Planner) | input_boolean.dai_debug_night_planner | input_boolean | On / Off | — | DAI | Verbose logging |
| Cold Day Flag | input_boolean.dai_cold_day_flag | input_boolean | On / Off | — | DAI | Force 100 % target |
| Temperature Estimate Low | input_number.rbc_temperature_estimate_low_c | input_number | °C | −20…20 / 0.1 | RBC | Forecast min temp |
| Temperature Bias | input_number.rbc_temp_bias_c | input_number | °C | −10…10 / 0.1 | RBC | Learned bias |
| Adjusted Min Temp | input_number.rbc_adjusted_min_temp_c | input_number | °C | −20…20 / 0.1 | RBC | Estimate + bias |
| RBC PV Adjusted | input_number.rbc_adjusted_pv_daily_kwh | input_number | kWh | 0…100 / 0.1 | RBC | Adjusted PV daily |
| RBC Demand Adjusted (Daily) | input_number.rbc_adjusted_demand_daily_kwh | input_number | kWh | 0…100 / 0.1 | RBC | Adjusted load daily |
| RBC Demand Adjusted (Next Horizon) | input_number.rbc_adjusted_demand_next_horizon_kwh | input_number | kWh | 0…100 / 0.1 | RBC | 16–22 window |
| RBC PV Bias | input_number.rbc_bias_pv_daily | input_number | kWh | −50…50 / 0.1 | RBC | Learned PV bias |
| RBC Demand Bias (08–16) | input_number.rbc_bias_demand_08_16 | input_number | kWh | −50…50 / 0.1 | RBC | Intra-day bias |
| RBC Demand Bias (Daily) | input_number.rbc_bias_demand_daily | input_number | kWh | −50…50 / 0.1 | RBC | Daily bias |
| Evening Bias (kWh) | input_number.rbc_evening_bias_kwh | input_number | kWh | −20…20 / 0.1 | RBC | Evening bias |
| Evening Buffer Auto Suggest % | input_number.dai_evening_buffer_auto_suggest_pct | input_number | % | 0…100 / 1 | DAI | Auto buffer suggestion |
| Evening Buffer Manual % | input_number.dai_evening_buffer_pct | input_number | % | 0…100 / 1 | DAI | Manual override |
| Target SoC (upper) | number.solax_house_selfuse_nightcharge_upper_soc | number | % | 0…100 / 1 | SolaX | Inverter setpoint |
| Start Discharge | input_number.soc_start_discharge_level | input_number | % | 0…100 / 1 | DAI | Anti-clipping |
| Stop Discharge | input_number.stop_discharge_level | input_number | % | 0…100 / 1 | DAI | Anti-clipping |
| SoC Hard Floor | input_number.dai_min_soc_hard_floor | input_number | % | 0…100 / 1 | DAI | Safety floor |
| Min SoC Floor — Cosy | input_number.dai_min_soc_floor_cosy | input_number | % | 0…100 / 1 | DAI | Tariff floor |
| Min SoC Floor — Flux | input_number.dai_min_soc_floor_flux | input_number | % | 0…100 / 1 | DAI | Tariff floor |
| Constant Buffer | input_number.dai_soc_buffer_pct | input_number | % | 0…50 / 1 | DAI | Planner buffer |
| Cold Temp Threshold | input_number.dai_cold_temp_threshold_c | input_number | °C | −10…15 / 0.5 | DAI | 100 % override threshold |
| Total Battery Capacity | input_number.total_battery_capacity_kwh | input_number | kWh | 0…100 / 0.1 | DAI | System capacity |
| Usage Baseline (Flux) | input_number.dai_usage_baseline_flux | input_number | kWh | 0…60 / 0.1 | DAI | Planner baseline |
| Seasonal Usage Factor | input_number.dai_usage_seasonal_multiplier | input_number | × | 0.5…2.0 / 0.05 | DAI | Seasonal multiplier |
| Demand Delta | input_number.dai_usage_margin_kwh | input_number | kWh | −20…20 / 0.1 | DAI | Planner offset |
| Variance Correction | input_number.dai_variance_correction_pct | input_number | % | −50…50 / 1 | DAI | Variance guard |
| Forecast Bias (DAI) | input_number.dai_forecast_bias | input_number | kWh | −20…20 / 0.1 | DAI | Anti-clipping bias |
| PV Surplus Threshold | input_number.dai_pv_surplus_threshold_w | input_number | W | 0…4000 / 50 | DAI | Live gate threshold |
| Hysteresis (min) | input_number.dai_hysteresis_min | input_number | min | 0…60 / 1 | DAI | Anti-clipping debounce |
| Feed-in Stop SoC | input_number.dai_feedin_stop_soc | input_number | % | 0…100 / 1 | DAI | Feed-in cap |
| Adjusted Solar Forecast | input_number.adjusted_solar_forecast | input_number | kWh | 0…60 / 0.1 | DAI | Fallback PV forecast |
| Tariff Warn Minutes | input_number.tariff_desync_warn_minutes | input_number | min | 0…240 / 5 | Octopus | Desync warn threshold |
| Tariff Halt Minutes | input_number.tariff_desync_halt_minutes | input_number | min | 0…240 / 5 | Octopus | Desync halt threshold |
| Night Planner Reason | input_text.dai_night_planner_reason | input_text | — | — | DAI | Last planner reason |
| RBC Horizon Log | input_text.rbc_horizon_log | input_text | — | — | RBC | Last horizon log |
| RBC Last Period (Evening) | input_text.rbc_last_period_evening_demand | input_text | — | — | RBC | Evening stamp |
| RBC Last Period (Temp) | input_text.rbc_last_period_temperature | input_text | — | — | RBC | Temperature stamp |
| Cosy Morning Start / End | input_datetime.dai_cosy_morning_start / _end | input_datetime | HH:MM | — | Octopus | Tariff window |
| Cosy Afternoon Start / End | input_datetime.dai_cosy_afternoon_start / _end | input_datetime | HH:MM | — | Octopus | Tariff window |
| Cosy Overnight Start / End | input_datetime.dai_cosy_overnight_start / _end | input_datetime | HH:MM | — | Octopus | Tariff window |
| Flux Overnight Start / End | input_datetime.dai_flux_overnight_start / _end | input_datetime | HH:MM | — | Octopus | Tariff window |
| Feed-in Cut-off | input_datetime.dai_latest_feedin_cutoff | input_datetime | HH:MM | — | DAI | AM anti-clip gate |
| Import Tariff | input_select.octopus_import_tariff | input_select | Cosy / Flux / … | — | Octopus | Active import tariff |
| Export Tariff | input_select.octopus_export_tariff | input_select | Outgoing Flux / … | — | Octopus | Active export tariff |
| Tariff (Display) | input_select.octopus_tariff_d | input_select | Cosy / Flux | — | Octopus | UI selector |
| Free Energy Session (Today) | input_select.free_energy_session_today | input_select | Off / Session / etc. | — | Octopus | Free energy session status |

---

## 4️⃣ Entities Map — Non-Helper Devices / Sensors / Selects

| Entity ID | Friendly | Domain | Source | Used in | Notes |
|---|---|---|---|---|---|
| weather.forecast_brenchley | Brenchley Weather | weather | Met Office | Home view (clock-weather) | — |
| sensor.ashp_hot_water_outside_temperature | Outside Temp | sensor | ASHP | Home tiles / Planner | Canonical outdoor temp |
| sensor.heating_current_temperature | Inside Temp | sensor | Heating | Home tiles | — |
| sensor.ashp_hot_water_tank_temperature | Hot Water | sensor | ASHP | Home tiles | — |
| climate.heating | Heating Control | climate | ASHP | Bubble climate | — |
| person.simon_angell / person.helen_angell | Simon / Helen | person | HA Person Tracker | Home | Presence cards |
| select.hub_mode | Hubitat Mode | select | Hubitat | Home | Hub status |
| light.internal_security_lights_virtual_device | Internal Lights | light | HA | Home | Virtual switch |
| light.external_security_lights_virtual_device | External Lights | light | HA | Home | Virtual switch |
| select.solax_house_charger_use_mode | Inverter Mode | select | SolaX | Many views | Self-Use / Feed-In / … |
| select.solax_house_manual_mode_select | Manual Mode | select | SolaX | Manual only | — |
| select.solax_house_selfuse_night_charge_enable | Grid Enabled | select | SolaX | Planner / Controls | Enable grid charge |
| select.solax_house_charge_and_discharge_period2_enable | 2nd Period | select | SolaX | Control view | Period 2 toggle |
| number.today_s_net_energy_usage | Net Usage | sensor (number) | Template | Home | Derived metric |
| sensor.solax_* and sensor.brenchley_* | Power / Energy / SoC etc. | sensor | SolaX / template | Live Energy / Stats | Full mapping in code comments |
| number.solax_house_export_control_user_limit | Max Sell Power | number | SolaX | Sunsynk card | — |
| select.dai_actual_demand_16_22_kwh | Actual 16–22 | select | DAI template | RBC Evening | Demand categorisation |

---

## 5️⃣ Notes

- All helpers should be created using the **Home Assistant UI** (not YAML) as per governance §3.3.  
- Keep this file as the **master entity-map** for future audits and design-doc updates.  
- If new automations or dashboards add entities, update this list before commit.  
- Cross-reference this file in each YAML `description:` as:  
  `Ref: helpers_entities_map.md (Nov 2025)`  

---

📄 *Generated automatically by ChatGPT (GPT-5 Thinking) for Simon Angell – DAI + RBC Project, Nov 2025.*
