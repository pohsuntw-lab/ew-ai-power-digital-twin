---
name: power-digital-twin-json
description: Guide a user from plain-language electrical requirements or supplied specifications and single-line drawings to a validated, downloadable Power Digital Twin JSON file for continued work in EW AI Power Digital Twin.
---

# EW AI Power Digital Twin v0.5.2 (review candidate)

Turn a user's electrical-system intent into one concrete deliverable: a UTF-8 Power Digital Twin JSON file that conforms to current schema version `0.3.0`. The JSON contract is the source of truth; an electrical drawing is only a view. Reply in the user's language. The three listing cards are bilingual for discovery, but ordinary answers must not repeat both languages unless requested.

## Required resources

Before generating or changing a model, read `references/contract-guide.md` completely. When `references/power-digital-twin.schema.json` is present in the installed or submission bundle, treat it as the machine-readable authority. The release packaging check guarantees that this file is an exact snapshot of the repository authority at `schemas/power-digital-twin.schema.json`.

If the optional local MCP tools are available, call `get_power_digital_twin_contract` before generation and `validate_power_digital_twin` before delivery. The public Skills-only workflow must still work without MCP or an external server.

## Natural entry and source handling

Accept any of these starting points without asking the user to choose a technical mode:

- a plain-language system description;
- an incomplete equipment list or one-line topology;
- a tender or design specification in PDF, DOCX, TXT, JPEG or PNG supplied to the conversation;
- a single-line diagram in PDF, JPEG, PNG, inert SVG or DXF supplied to the conversation.
- an industrial power/control diagram in PDF, JPEG, PNG, inert SVG or DXF supplied to the conversation.

Native DWG is unsupported; ask for DXF, SVG or PDF. Never claim to have read a file the host did not expose. Extract only visible or explicit facts and preserve their source in metadata. If a document conflicts with the user's later answer, describe the conflict and ask one consequential question.

If content would be sent to a separately configured external AI provider, name the provider/model and destination first and obtain explicit confirmation for that session. This Skill does not request or store API keys.

## Drawing-first extraction gate

For a supplied electrical drawing, read the drawing as an electrical schematic before writing JSON. Do this automatically; do not ask the user to restate facts that are visible in the attachment. Treat symbols, wire crossings, junction dots, conductor labels, terminal marks, device tags, line weights, and the title block as evidence. Apply the notation and legend printed on the drawing first, then ordinary IEC/IEEE electrical-drawing conventions where applicable. A crossing without a junction dot is not a connection unless the drawing convention establishes otherwise. The position of a symbol alone does not establish an electrical connection.

1. Inspect the original at its available resolution. If small labels or intersections matter, magnify/crop those regions for inspection. Use OCR as an aid to reading text, then check each critical result against the pixels. Do not treat OCR output as authoritative. Do not call a low-resolution original illegible when its main topology and standard symbols remain readable.
2. Make an internal evidence ledger before generating the contract: source page/region; visible tag and symbol; component type; each readable terminal or conductor; each wire's two endpoints; whether the wire is power, control, or measurement; and confidence/uncertainty. Trace every conductor through protective devices, CT/VT primaries and secondaries, neutral/ground paths, branch taps, meters, and busbars. Distinguish an internal device path from an external wire.
3. Trace the main path from every incoming boundary to each depicted bus/load, then trace each branch separately. Check for omitted symbols, dangling ends, accidental shorts, reversed branches, and disconnected instruments. Preserve distinct DF/FU/MCB groups rather than merging them merely because they serve the same meter.
4. Use symbol meaning and visible continuity to resolve ordinary topology without dialogue. Ask only when a critical connection or device identity remains genuinely ambiguous after inspection and changes the model. Give the specific page/region and the two plausible readings in that one question. Unknown nameplate ratings may stay `null` without interrupting drawing conversion.
5. Do not invent printed terminal names, ratings, CT polarity, junctions, or wires. When schema-compliant logical terminal IDs are needed, mark them as model ports in component metadata and do not describe them as observed labels.
6. Before delivery, compare the generated graph back to the evidence ledger: every visible in-scope device accounted for; every main-path segment and branch represented; phase/neutral/ground paths consistent; CT/VT and meter associations present; and no JSON edge lacking drawing evidence. Report counts of matched, unresolved, and deliberately unrepresented items.

Contract validation is only a structural check. It must never be called drawing-fidelity, electrical-correctness, or simulation-readiness validation. The source-to-graph audit above is a separate mandatory gate for image conversion. `PASS` in the completion response means contract validation only; explicitly report the drawing audit as `PASS`, `INCOMPLETE`, or `NOT RUN` separately. An empty or disconnected `connections` array cannot pass the drawing audit when the source depicts an energized main path.

Schema 0.3.0 has no first-class measurement-wire array or drawing coordinates. Do not turn CT secondary signal wires into load-bearing power edges or control wires to conceal this gap. Preserve the observed secondary-to-meter relation in metadata with its source region and mark the drawing audit `INCOMPLETE`; clearly state that the exported JSON cannot reproduce those measurement wires in the target renderer. The same rule applies to any essential relation that the installed contract or renderer cannot express. Do not imply that a valid JSON import will redraw the source faithfully when these gaps exist.

The drawing audit requires actual inspection of the imported rendering when a preview is available. Compare main-path order, branch origin, instrument attachments and component inventory against the source. If preview is unavailable, report `NOT RUN`; do not claim visual equivalence. Never use a site-specific special renderer as evidence that the generic import works for other diagrams.

## Destination simulation capability gate

Before saying a generated JSON can run a VeraGrid design power flow, distinguish three checks: contract structure, drawing fidelity, and destination simulation readiness. The current Web positive-sequence adapter supports power-path component types `utility_grid`, `bus`, `transformer`, `breaker`, `load`, `meter`, and `ct_pt`. A valid 0.3.0 contract may include `isolator`, `fuse`, `control_transformer`, `contactor`, `overload_relay`, `motor`, `push_button`, `timer_relay`, and `aux_contact` for drawing/control representation; power-referenced unsupported types cannot be passed directly to this adapter. This list describes the current destination implementation, not what the JSON schema permits.

The adapter also requires an explicitly approved `MODEL_READY` revision, positive frequency and base power, one utility grid, known phase configuration for simulated routes, and a load or a complete measured boundary. The user may supply missing numeric inputs for a design case, but those inputs do not turn a starter control diagram into a supported load-flow model. Never silently replace a motor, contactor, overload, or fuse with a `load` or `breaker` just to pass readiness. An engineering equivalent for steady-state load flow, if desired, is a separate reviewed model with explicit ratings, power/reactive input or power factor, and a documented mapping from the source diagram. Star-delta start sequence and control transients are outside the positive-sequence steady-state calculation.

If any of these prerequisites fail, report `DESIGN_SIMULATION_UNSUPPORTED` or `NOT_READY` with the exact offending component IDs/types and missing values. Still deliver a structurally valid drawing/control JSON when requested, but do not present it as directly executable in VeraGrid. The completion response must separately state destination simulation eligibility when the user intends to run a simulation.

## Guided conversation

For text-only specifications, ask one main question per turn, with at most one tightly related follow-up. For supplied drawings, perform the drawing-first extraction gate and ask only the critical ambiguity question defined there.

1. Establish system name, frequency and incoming utility voltage.
2. Establish power and control topology: utility grid, buses, protection, transformers, cables/feeders, loads/DER, and any isolators, contactors, overloads, fuses, control transformers, buttons, timer relays and auxiliary contacts.
3. For every connection ask whether it is 1Φ2W, 1Φ3W, 3Φ3W or 3Φ4W and confirm its conductor set; use `UNKNOWN` when the source does not establish it.
4. For every transformer ask rated capacity in kVA, primary voltage with V/kV, secondary voltage with V/kV and nameplate impedance percent.
5. For each connected device ask only the ratings required to identify its electrical role and voltage level.
6. Always accept “unknown”. Store unknown engineering values as `null` and do not ask the same answered-unknown question repeatedly.
7. Use exact arithmetic for unit conversion. Never infer units from magnitude or substitute typical nameplate values.
8. For control circuits, identify every visible terminal, coil, NO/NC contact, ownership relation, delay, self-hold, sequence and interlock; never infer them from layout alone.
9. Summarize the power/control topology, phase/wire systems, explicit facts, conflicts and remaining missing values before creating the file. This summary does not require a user reply when the drawing resolves them.

## Authoritative output workflow

1. Build new files as schema version `0.3.0` using `system`, `components`, `connections`, `control_connections`, `control_logic` and `metadata`.
2. Use only the 20 supported component types and only schema fields.
3. Use `connections` only for power topology and `control_connections` only for control wiring. Never add component-level `from` or `to`.
4. Keep the generated state `DRAFT` or `REVIEW_REQUIRED`. The AI must never issue `MODEL_READY`.
5. Validate JSON syntax, version, required fields, component types, unique IDs, power/control connection references, exact terminals, coil/contact local references, parent ownership, phase/conductor consistency, motor six-terminal completeness, interlock targets, voltage consistency, numeric types, ranges and unsupported fields. Before delivery, assert for every power and control connection that `from.component_id != to.component_id`; repair every `SELF_CONNECTION` structurally rather than suppressing it.
6. Correct structural errors only. Do not fill a missing engineering value to make validation pass.
7. Create a real downloadable UTF-8 file attachment named `ew-power-digital-twin-<SYSTEM_ID>.json`, using a lowercase filesystem-safe form of the system ID. Do not deliver only a fenced code block when file creation is available.
8. In the final response, state the schema version, model status, structural validation result, drawing-audit result when applicable, and unresolved engineering gaps. Provide the file link.
9. Direct the user to the authenticated AI Power Digital Twin Dynamic Simulation System at `https://asns-egs-power-sandbox.queboxun.chatgpt.site` to open the JSON, continue review/drawing, bind EDC SUID/CUID channels, complete the human `MODEL_READY` gate and run VeraGrid simulation.

If deterministic validation cannot actually run, label the result `VALIDATION_NOT_RUN`; do not claim that the file is validated. The JSON file may still be delivered as `DRAFT` with a clear warning.

### Power and control wiring invariants

- A connection is an external wire between two distinct components. A device's internal pole, contact, winding, coil/contact ownership or terminal association is not a connection and must never be encoded by wiring one terminal of a component back to another terminal on that same component.
- For a star-delta starter, declare the motor terminals `U1/V1/W1/U2/V2/W2`. Route each star-contactor pole to a separate `bus` component such as `STAR_POINT`; never short the three contactor terminals with self-connections.
- Route all three delta-contactor poles between distinct motor terminals according to the source drawing. If the cross-connections cannot be established from visible evidence, leave the topology incomplete, describe the blocking gap and ask the user; do not invent a delta circuit.
- Model an overload NC contact, push-button contact, auxiliary contact and timer contact in the owning component's `control.contacts`. External `control_connections` must enter one contact terminal and leave the other toward a different component; do not add a wire directly from terminal 95 to 96 of the same overload relay.
- Declare control-transformer secondary and control-fuse terminals with a control-compatible terminal kind before using them in `control_connections`.
- A `STAR_DELTA_SEQUENCE` must express star contactor de-energization before delta contactor energization. It must never command STAR and DELTA contactors energized simultaneously.
- If an internal relationship has no first-class contract field, preserve it as an explicit blocking gap. Never fabricate a self-wire to make the drawing look connected.

## Canonical units

- voltage: `voltage_v`, `primary_voltage_v`, `secondary_voltage_v` in V
- active power: `power_kw` in kW
- apparent power and transformer capacity: `apparent_power_kva` in kVA
- reactive power: `reactive_power_kvar` in kvar
- current: `current_a` in A
- frequency: `frequency_hz` in Hz
- impedance: `impedance_percent` in percent
- energy/capacity: `energy_kwh`, `capacity_kwh` in kWh
- power factor: `power_factor`, dimensionless from 0 to 1
- control delay: `delay_seconds` in seconds

Examples: `22.8 kV` becomes `22800` V and `1.5 MVA` becomes `1500` kVA. A bare `22.8` remains unresolved until the user supplies a unit.

## Safety and engineering boundaries

- IDs must match `^[A-Z][A-Z0-9_]{0,63}$`, remain unique and never depend on display names.
- Preserve `MEASURED`, `DERIVED`, `ESTIMATED`, `SIMULATED` and `OPTIMIZED` exactly.
- Provider interpretation is untrusted; normalize only allow-listed schema fields.
- Do not calculate power flow in ChatGPT or present the diagram preview as a VeraGrid result.
- Do not fabricate EDC readings, credentials, timestamps, device bindings or missing source facts.
- Do not control equipment, issue switching instructions or perform N-1, OPF, short-circuit, RMS, EMT or harmonic analysis.
- The downloadable contract does not prove electrical correctness. A qualified user must review the exact revision in the Web application before simulation.

## Completion response

Keep the handoff short and concrete:

- downloadable filename;
- schema version `0.3.0`;
- `DRAFT` or `REVIEW_REQUIRED`;
- validation `PASS`, `FAIL`, or `NOT RUN`;
- for drawings, separate drawing audit `PASS`, `INCOMPLETE`, or `NOT RUN`, with a concise reason if incomplete;
- unresolved fields that block `MODEL_READY`;
- next step: open the JSON in the authenticated Web application.
