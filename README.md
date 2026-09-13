# EW AI Power Digital Twin

EW AI Power Digital Twin is a Skills-only ChatGPT plugin that guides a user from an electrical-system description, specification, tender document or single-line diagram to a downloadable Power Digital Twin JSON file.

EW AI Power Digital Twin 是純 Skill 形式的 ChatGPT 外掛，引導使用者將電力系統文字描述、規範、標書或單線圖轉換為可下載的 Power Digital Twin JSON。

## Contract boundary / 契約邊界

- Authoritative schema: `schemas/power-digital-twin.schema.json`
- Schema version: `0.1.0`
- Missing engineering facts remain `null`; the Skill must not invent typical values.
- Generated files remain `DRAFT` or `REVIEW_REQUIRED`; only the destination engineering system may grant `MODEL_READY` after human review.
- This repository does not contain the Mac mini service, VeraGridEngine, EDC credentials, customer drawings or model weights.

## Typical flow / 典型流程

1. Describe the power system or upload an authorized specification or single-line drawing in ChatGPT.
2. Answer guided questions for missing voltage, transformer, protection, conductor and load data.
3. Download the generated schema-versioned JSON.
4. Open it in the authenticated AI Power Digital Twin Dynamic Simulation System for validation, drawing, EDC SUID/CUID mapping, human approval and governed simulation.

## Documents / 文件

- `skills/power-digital-twin-json/SKILL.md` — AI behavior and safety contract
- `schemas/power-digital-twin.schema.json` — authoritative JSON Schema
- `examples/` — synthetic valid examples
- `PRIVACY.md`, `TERMS.md`, `SUPPORT.md` — public policy and support information

Report issues with synthetic or redacted examples only. Never attach passwords, API keys, EDC credentials, customer drawings or confidential logs.
