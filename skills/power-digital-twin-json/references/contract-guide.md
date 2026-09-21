# Power Digital Twin JSON contract guide

This guide summarizes the repository's machine-readable authority, `schemas/power-digital-twin.schema.json`. The packaged `power-digital-twin.schema.json` snapshot is authoritative for exact field validation.

## Root object

The 0.3.0 root contains exactly `schema_version`, `system`, `components`, `connections`, `control_connections`, `control_logic` and `metadata`. Versions 0.1.0 and 0.2.0 remain backward-compatible imports. Unsupported root or nested fields are errors.

## Components

Supported power types are `utility_grid`, `bus`, `transformer`, `breaker`, `cable`, `load`, `motor`, `generator`, `pv`, `ess`, `meter` and `ct_pt`. Version 0.3.0 adds `isolator`, `contactor`, `overload_relay`, `fuse`, `control_transformer`, `push_button`, `timer_relay` and `aux_contact`.

Every component requires `id`, `type`, `name`, `rated_parameters`, `operating_parameters`, `status` and `metadata`. Control components declare machine-readable terminals; coils and contacts reference only their owner's terminals. Contact normal state is explicitly `NO` or `NC`. Missing engineering values, including timer delay or coil rating, use `null`.

## Topology

`connections` contains only power wiring; `control_connections` contains only control wiring. IDs are unique across both arrays. Every endpoint names a component and an exact declared terminal such as `A1`, `13`, `95` or `U1`. `control_logic` declares energize, self-hold, delay, mutual-interlock and star-delta sequence behavior by component ID; layout coordinates never imply logic.

Every 0.2.0 or 0.3.0 power connection declares one `phase_configuration`: `SINGLE_PHASE_TWO_WIRE`, `SINGLE_PHASE_THREE_WIRE`, `THREE_PHASE_THREE_WIRE`, `THREE_PHASE_FOUR_WIRE`, `INDIVIDUAL_CONDUCTOR` or `UNKNOWN`. Individual conductors carry exactly one conductor. Power wiring uses only POWER/GROUND terminals; control wiring uses only CONTROL/COIL/CONTACT/GROUND terminals. A star-delta motor declares U1/V1/W1/U2/V2/W2, and its sequence outputs to one STAR and one DELTA contactor. A mutual interlock targets exactly two contactors. Never infer these facts from voltage magnitude or drawing style.

Every referenced component must exist. Every power or control connection must join two distinct component IDs; self-connections are invalid. Internal poles, contacts, windings and terminal associations are component semantics, not external wires. A star-delta starter uses a separate `bus` such as `STAR_POINT` for the star junction and explicit cross-component delta-contactor wiring. If those cross-connections are not visible, preserve a blocking gap instead of inventing a self-wire. Known endpoint voltages must agree within the validator tolerance; a transformer is the intentional voltage-changing boundary because its primary and secondary terminals carry their respective voltage ratings.

## Missing values and evidence

Unknown engineering parameters are `null`, never typical values. Use `DRAFT` while topology or major facts are incomplete and `REVIEW_REQUIRED` when the document is ready for human review. Only the Web application's hash-bound human approval can establish `MODEL_READY`.

Generated facts derived directly from the user's explicit description normally use `DERIVED`. Do not label AI-extracted or user-stated values as `MEASURED`; that label is reserved for actual measurement evidence.

For drawing input, record the source page and region and distinguish visible labels from logical model ports. A structurally valid contract can still omit every connection; schema validation does not prove that the drawing was transcribed completely. Reconcile the component and connection graph against the drawing before claiming a successful conversion. Keep distinct source symbols and branches distinct in the model unless the drawing itself establishes a common device.

Version 0.3.0 cannot represent CT/VT secondary measurement wires as first-class connections: power connections reject MEASUREMENT endpoints and control connections are for control wiring. It also cannot store source-faithful coordinates or wire routes. Preserve such observations in metadata, set the drawing audit to `INCOMPLETE`, and disclose that the Web drawing will omit them. Never reclassify measurement wiring as power or control merely to get a connected-looking preview. A later version of the contract and Web renderer must add first-class measurement connectivity before these circuits can be faithfully exchanged.

## Minimal valid example

```json
{
  "schema_version": "0.3.0",
  "system": {"id": "PLANT01", "name": "Plant", "status": "REVIEW_REQUIRED", "frequency_hz": 60, "base_power_kva": null},
  "components": [{"id": "GRID01", "type": "utility_grid", "name": "Utility Grid", "rated_parameters": {"voltage_v": 22800, "frequency_hz": 60}, "operating_parameters": {"voltage_v": null}, "status": "IN_SERVICE", "metadata": {"source": "USER_PROVIDED", "evidence_label": "DERIVED", "confidence": null, "notes": []}}],
  "connections": [],
  "control_connections": [],
  "control_logic": [],
  "metadata": {"model_revision": "0.3.0", "created_at": "2026-09-14T00:00:00Z", "updated_at": "2026-09-14T00:00:00Z", "source": "USER_PROVIDED", "evidence_label": "DERIVED", "notes": []}
}
```

Timestamps must reflect actual file-generation time rather than copying the example timestamp.

## Invalid patterns

- `"impedance_percent": 6` when the user never supplied or enabled an exact derivation: fabricated engineering data.
- `"id": "Main Bus"`: invalid ID because it contains whitespace.
- component-level `"from"` / `"to"`: conflicting topology representation.
- a connection referencing `BUS03` when no such component exists.
- a connection whose `from.component_id` and `to.component_id` are both `KM3`; use a separate star-point bus or component semantics instead.
- `"voltage_kv": 22.8`: unsupported unit field; use `"voltage_v": 22800` only after the unit is explicit.
- AI-generated `"status": "MODEL_READY"`: human approval boundary violation.
