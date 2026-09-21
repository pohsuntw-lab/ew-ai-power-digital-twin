# EW AI Power Digital Twin

EW AI Power Digital Twin is a Skills-only ChatGPT plugin that guides a user from an electrical-system description, specification, tender document, single-line diagram or industrial control diagram to a downloadable Power + Control Digital Twin JSON file.

EW AI Power Digital Twin 是純 Skill 形式的 ChatGPT 外掛，引導使用者將電力系統文字描述、規範、標書、單線圖或工業控制圖轉換為可下載的 Power + Control Digital Twin JSON。

Current source version / 目前原始碼版本：`0.5.2` (review candidate). Drawing conversion now requires electrical-symbol and conductor inspection, source-to-graph reconciliation, and separate reporting of contract validation and drawing fidelity. / 圖紙轉換須先檢查電氣符號與導線連續性、逐項核對原圖與 JSON 拓撲，並分別報告契約驗證及圖紙還原結果。本候選版本尚未代表 ChatGPT 外掛已發布。

## Contract boundary / 契約邊界

- Authoritative schema: `schemas/power-digital-twin.schema.json`
- Current schema version: `0.3.0` (backward-compatible imports for `0.1.0` and `0.2.0` remain defined by the contract)
- Power + Control components include contactors, overload relays, fuses, control transformers, push buttons, timer relays and auxiliary contacts.
- Explicit terminals, power connections, control connections, coils, NO/NC contacts, delays, interlocks and motor star/delta relationships are represented as contract data rather than hidden in metadata.
- Missing engineering facts remain `null`; the Skill must not invent typical values.
- Every external power/control connection joins two distinct components. Internal poles, contacts and windings are component semantics rather than self-wires.
- Generated files remain `DRAFT` or `REVIEW_REQUIRED`; only the destination engineering system may grant `MODEL_READY` after human review.
- Contract 0.3.0 does not yet express CT/VT secondary measurement wires or exact source drawing coordinates. A drawing that needs these relations must be reported as incomplete rather than described as faithfully redrawn.
- This repository does not contain the Mac mini service, VeraGridEngine, EDC credentials, customer drawings or model weights.

## Typical flow / 典型流程

1. Describe the power or control system, or upload an authorized specification, single-line drawing or industrial control drawing in ChatGPT.
2. For drawings, the Skill reads visible electrical symbols and connections first. It asks only about a critical ambiguity that remains after inspection; unreadable ratings stay unknown.
3. Download the generated schema-versioned JSON.
4. Open it in the authenticated AI Power Digital Twin Dynamic Simulation System for validation, drawing, EDC SUID/CUID mapping, human approval and governed simulation.

## Documents / 文件

- `skills/power-digital-twin-json/SKILL.md` — AI behavior and safety contract
- `schemas/power-digital-twin.schema.json` — authoritative JSON Schema
- `examples/` — synthetic valid examples
- `examples/star_delta_motor_control.json` — schema `0.3.0` star-delta motor power and control example
- `tests/fixtures/metering-panel.svg` — synthetic drawing for source-to-graph regression cases
- `PRIVACY.md`, `TERMS.md`, `SUPPORT.md` — public policy and support information

Report issues with synthetic or redacted examples only. Never attach passwords, API keys, EDC credentials, customer drawings or confidential logs.
