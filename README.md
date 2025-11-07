# SolaX-Automation-in-Home-Assistant

# ⚡ Home Assistant – SolaX Integration (Custom YAML Examples)

This repository contains my working **YAML automations, templates, and helper usage patterns** for managing a **SolaX Hybrid Inverter** setup in Home Assistant.

The goal of this repo is to help Home Assistant users who already have their SolaX inverter exposed in HA, but want to go further than basic monitoring — including coordinated inverter mode control, battery charge strategy, and scenario-based automation.

> **Note:** My advanced DAI/RBC adaptive energy management logic is stored in a separate repository.  
> This repository focuses specifically on the practical **SolaX integration layer**.

---

## 🏠 System Overview

My system consists of:

| Component | Details |
|---------|---------|
| Inverters | SolaX X1 Hybrid (parallel configuration) |
| Battery Storage | ~23 kWh total |
| Home Assistant | Running on HA OS |
| SolaX Integration Method | HA native SolaX integration (or API / MQTT depending on environment) |

Your hardware does **not** need to match exactly — the YAML logic is designed to be **adaptable**.

---

## 📦 What This Repository Includes

| Folder / File | Description |
|--------------|-------------|
| `sensors/` | Template & derived sensors for state visibility (PV, SoC, grid, charge limits, daily totals, etc.) |
| `automations/` | Automations for inverter mode switching, scheduled charging, export behaviour, state decision logic |
| `helpers/` | Input helpers used to store state, flags, thresholds and configuration values |
| `lovelace/` *(optional)* | Dashboard cards for monitoring battery, PV, grid flow & system mode |

These YAML files are meant to be **reference patterns**, not “plug and play”.  
Entity names may need adjustment based on your system.

---

## 🎯 What Problem This Solves

The SolaX integration exposes **raw inverter controls**, but does not provide:

- Strategy logic
- Charge target scheduling
- Export vs self-use decisioning
- Day-ahead planning
- Override layers

This repository provides **automation structures** that make the inverter:
- Smarter
- More predictable
- More energy-cost-aware

---

## ⚙️ Requirements

To use / adapt the YAML here, you should already:
- Have SolaX shown in Home Assistant (via built-in integration, MQTT bridge, or local API)
- Be comfortable editing YAML in HA
- Understand how helpers, triggers, and conditions work
- Be familiar with **one automation = one responsibility** pattern (recommended)

If any of these are unfamiliar, start with:
https://www.home-assistant.io/docs/automation/

---

## 🔍 Before You Import Anything

You will need to:
1. Search for `solax_` entity names that differ in your setup
2. Create any referenced **input numbers**, **input booleans**, or **scenes**
3. Test in a **non-critical time window** before relying on automation

This avoids unintended inverter behaviour.

---

## 📊 Optional: Recommended Dashboard Cards

A simple battery + solar usage overview works well:
- **Energy Dashboard**
- **Power Flow Card / Power Flow Plus**
- **ApexCharts for historical SoC vs solar**

If you want, I can include ready-made dashboard cards — just open an Issue.

---

## 💬 Support / Questions / Sharing

If you adapt or improve this setup:
- Please open an **Issue**
- Or submit a **Pull Request**

Sharing improvements strengthens the entire HA + SolaX ecosystem.

---

## ⚠️ Disclaimer

This configuration controls **real hardware**.  
Applying incorrect charge limits or mode switching may affect battery health or energy billing.

Make changes **intentionally** and test **incrementally**.

---

