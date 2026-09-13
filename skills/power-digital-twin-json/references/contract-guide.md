# Power Digital Twin JSON contract guide

This guide summarizes the repository's machine-readable authority, `schemas/power-digital-twin.schema.json`. The packaged `power-digital-twin.schema.json` snapshot is authoritative for exact field validation.

## Root object

The root must contain exactly `schema_version`, `system`, `components`, `connections` and `metadata`. `schema_version` is exactly `0.1.0`. Unsupported root or nested fields are errors.

## Components

Supported types are `utility_grid`, `bus`, `transformer`, `breaker`, `cable`, `load`, `motor`, `generator`, `pv`, `ess`, `meter` and `ct_pt`.

Every component requires `id`, `type`, `name`, `rated_parameters`, `operating_parameters`, `status` and `metadata`. Missing engineering values use `null`. Do not omit the required parameter objects.

## Topology

Each connection requires a unique ID, `from`, `to`, status and metadata. Each endpoint contains `component_id` and one terminal from `PORT`, `LINE`, `LOAD`, `PRIMARY`, `SECONDARY`. Transformer endpoints must explicitly use `PRIMARY` or `SECONDARY`; those two terminals are reserved for transformers.

Every referenced component must exist. Self-connections are invalid. Known endpoint voltages must agree within the validator tolerance; a transformer is the intentional voltage-changing boundary because its primary and secondary terminals carry their respective voltage ratings.

## Missing values and evidence

Unknown engineering parameters are `null`, never typical values. Use `DRAFT` while topology or major facts are incomplete and `REVIEW_REQUIRED` when the document is ready for human review. Only the Web application's hash-bound human approval can establish `MODEL_READY`.

Generated facts derived directly from the user's explicit description normally use `DERIVED`. Do not label AI-extracted or user-stated values as `MEASURED`; that label is reserved for actual measurement evidence.

## Minimal valid example

```json
{
  "schema_version": "0.1.0",
  "system": {"id": "PLANT01", "name": "Plant", "status": "REVIEW_REQUIRED", "frequency_hz": 60, "base_power_kva": null},
  "components": [{"id": "GRID01", "type": "utility_grid", "name": "Utility Grid", "rated_parameters": {"voltage_v": 22800, "frequency_hz": 60}, "operating_parameters": {"voltage_v": null}, "status": "IN_SERVICE", "metadata": {"source": "USER_PROVIDED", "evidence_label": "DERIVED", "confidence": null, "notes": []}}],
  "connections": [],
  "metadata": {"model_revision": "0.1.0", "created_at": "2026-09-13T00:00:00Z", "updated_at": "2026-09-13T00:00:00Z", "source": "USER_PROVIDED", "evidence_label": "DERIVED", "notes": []}
}
```

Timestamps must reflect actual file-generation time rather than copying the example timestamp.

## Invalid patterns

- `"impedance_percent": 6` when the user never supplied or enabled an exact derivation: fabricated engineering data.
- `"id": "Main Bus"`: invalid ID because it contains whitespace.
- component-level `"from"` / `"to"`: conflicting topology representation.
- a connection referencing `BUS03` when no such component exists.
- `"voltage_kv": 22.8`: unsupported unit field; use `"voltage_v": 22800` only after the unit is explicit.
- AI-generated `"status": "MODEL_READY"`: human approval boundary violation.
