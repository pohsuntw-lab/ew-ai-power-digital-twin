# EW AI Power Digital Twin v0.5.3 Publishing Guide / 發布指引

## Release form / 發布形式

Version `0.5.3` is a **Skills-only review candidate**. It retains Contract `0.3.0` and adds drawing-first extraction, a source-to-graph audit, and a separate destination simulation capability gate. Contract validation, drawing fidelity and VeraGrid eligibility must be reported separately. The 0.3.0 limitation for CT/VT measurement wiring and drawing coordinates must be disclosed, never hidden by an invented power/control connection. Version 0.5.2 was approved in the portal but remains unpublished; its earlier ZIP lacks the simulation capability gate and must not be published as the complete fix. This source update is not evidence that the ChatGPT plugin has been updated or published. The local MCP remains an unpublished deterministic development aid.

## Listing / 上架資訊

- Name: `EW AI Power Digital Twin`
- Developer: `Embodied Worker`
- Subtitle: `Text to Grid JSON｜文字生電力圖`
- Website: `https://www.embodiedworker.com`
- Support: `https://github.com/pohsuntw-lab/ew-ai-power-digital-twin/issues`
- Privacy: `https://github.com/pohsuntw-lab/ew-ai-power-digital-twin/blob/main/PRIVACY.md`
- Terms: `https://github.com/pohsuntw-lab/ew-ai-power-digital-twin/blob/main/TERMS.md`
- Approved mark: `assets/icon.png` and `assets/logo.png`

## Three starter cards / 三張提示卡

1. `Create a downloadable power-system JSON from my description. / 請把我的描述轉成可下載的電力系統 JSON。`
2. `Guide me through missing ratings, then export JSON. / 請引導我補齊設備規格，再匯出 JSON。`
3. `Convert my specification, single-line diagram, or control diagram to JSON. / 請將規範、單線圖或控制圖轉成 JSON。`

## Required checks / 必要檢查

1. Build the clean bundle with `.venv/bin/python plugins/power-digital-twin/scripts/package_submission.py`.
2. Confirm the bundled Schema SHA-256 equals the repository authority.
3. Run the positive and negative submission cases against their real fixtures; record actual results without rewriting expectations to force a pass. `POS-009` must trace the main path, `NEG-005` must flag a structurally valid but disconnected model, and `POS-010`/`NEG-006` must distinguish valid star-delta drawing JSON from unsupported direct VeraGrid simulation. Verify that the synthetic drawing fixture contains no user-uploaded drawing.
4. Confirm generated answers create a downloadable `.json` file, not only a code block.
5. Confirm missing engineering facts remain `null`, and AI never returns `MODEL_READY`.
6. Run repository automated tests, Markdown checking, license inventory and secret scanning.
7. Push the reviewed commit to GitHub before uploading the bundle in the OpenAI Platform plugin portal.
8. Complete required developer/business verification and truthful legal attestations in the portal.
9. Submit for OpenAI review. Publish only after approval and a final owner decision; do not merge to `main` automatically.

Version `0.5.0` completed this sequence and was published at `https://chatgpt.com/plugins/plugins_6aa67d96732c819182bcd9b52eb9fe3d`. Its OpenAI submission ID is `appsub_6aa796a4a6308191b9f3dcfccaabdb53`. Confirm the current live version in the portal before publishing 0.5.3. Record 0.5.3 scan, review and publication evidence only after those actions succeed.

## Web handoff / Web 銜接

The generated file is opened by the authenticated AI Power Digital Twin Dynamic Simulation System. Import must validate the contract before displaying or converting it. Users then review/edit the topology, map EDC SUID/CUID channels to exact diagram entities, complete the hash-bound human `MODEL_READY` gate and only then request VeraGrid calculation. The plugin itself performs none of those runtime actions.
