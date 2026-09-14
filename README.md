# EW AI Power Digital Twin

EW AI Power Digital Twin is a Skills-only ChatGPT plugin that guides a user from an electrical-system description, specification, tender document, single-line diagram or industrial control diagram to a downloadable Power + Control Digital Twin JSON file.

EW AI Power Digital Twin 是純 Skill 形式的 ChatGPT 外掛，引導使用者將電力系統文字描述、規範、標書、單線圖或工業控制圖轉換為可下載的 Power + Control Digital Twin JSON。

Current source version / 目前原始碼版本：`0.5.1`. This patch prevents AI-generated self-connections, requires a separate star-point bus for star-delta starters, requires all evidenced delta cross-connections, and keeps control wiring on control-compatible terminals. / 本修正版禁止 AI 產生元件自我連線，星三角啟動器必須使用獨立星點、保留完整且有證據的三角跨接，控制配線也必須使用正確的控制端子領域。

## Contract boundary / 契約邊界

- Authoritative schema: `schemas/power-digital-twin.schema.json`
- Current schema version: `0.3.0` (backward-compatible imports for `0.1.0` and `0.2.0` remain defined by the contract)
- Power + Control components include contactors, overload relays, fuses, control transformers, push buttons, timer relays and auxiliary contacts.
- Explicit terminals, power connections, control connections, coils, NO/NC contacts, delays, interlocks and motor star/delta relationships are represented as contract data rather than hidden in metadata.
- Missing engineering facts remain `null`; the Skill must not invent typical values.
- Every external power/control connection joins two distinct components. Internal poles, contacts and windings are component semantics rather than self-wires.
- Generated files remain `DRAFT` or `REVIEW_REQUIRED`; only the destination engineering system may grant `MODEL_READY` after human review.
- This repository does not contain the Mac mini service, VeraGridEngine, EDC credentials, customer drawings or model weights.

## Typical flow / 典型流程

1. Describe the power or control system, or upload an authorized specification, single-line drawing or industrial control drawing in ChatGPT.
2. Answer guided questions for missing voltage, transformer, protection, conductor, load, terminal and control-logic data.
3. Download the generated schema-versioned JSON.
4. Open it in the authenticated AI Power Digital Twin Dynamic Simulation System for validation, drawing, EDC SUID/CUID mapping, human approval and governed simulation.

## Documents / 文件

- `skills/power-digital-twin-json/SKILL.md` — AI behavior and safety contract
- `schemas/power-digital-twin.schema.json` — authoritative JSON Schema
- `examples/` — synthetic valid examples
- `examples/star_delta_motor_control.json` — schema `0.3.0` star-delta motor power and control example
- `PRIVACY.md`, `TERMS.md`, `SUPPORT.md` — public policy and support information

Report issues with synthetic or redacted examples only. Never attach passwords, API keys, EDC credentials, customer drawings or confidential logs.
