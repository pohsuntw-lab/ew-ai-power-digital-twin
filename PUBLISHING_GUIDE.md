# EW AI Power Digital Twin v0.3.0 Publishing Guide / 發布指引

## Release form / 發布形式

Publish this version as a **Skills-only** ChatGPT plugin. The user-facing job is guided text/document/drawing interpretation followed by a downloadable schema-version `0.1.0` JSON file. The local MCP implementation remains a deterministic development and validation aid; it is not part of the public submission bundle and the Mac mini is not exposed to the Internet.

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
3. `Convert my specification or single-line diagram to JSON. / 請將規範或單線圖轉成 JSON。`

## Required checks / 必要檢查

1. Build the clean bundle with `.venv/bin/python plugins/power-digital-twin/scripts/package_submission.py`.
2. Confirm the bundled Schema SHA-256 equals the repository authority.
3. Run the positive and negative submission cases; record actual results without rewriting expectations to force a pass.
4. Confirm generated answers create a downloadable `.json` file, not only a code block.
5. Confirm missing engineering facts remain `null`, and AI never returns `MODEL_READY`.
6. Run repository automated tests, Markdown checking, license inventory and secret scanning.
7. Push the reviewed commit to GitHub before uploading the bundle in the OpenAI Platform plugin portal.
8. Complete required developer/business verification and truthful legal attestations in the portal.
9. Submit for OpenAI review. Publish only after approval and a final owner decision; do not merge to `main` automatically.

## Web handoff / Web 銜接

The generated file is opened by the authenticated AI Power Digital Twin Dynamic Simulation System. Import must validate the contract before displaying or converting it. Users then review/edit the topology, map EDC SUID/CUID channels to exact diagram entities, complete the hash-bound human `MODEL_READY` gate and only then request VeraGrid calculation. The plugin itself performs none of those runtime actions.
