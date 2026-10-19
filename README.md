# Fruit Inspector

**Build 0.10.0** · Windows x64 · Nim · ONNX Runtime (CPU) · conveyor station demo (eval)

Fruit Inspector is **conveyor inspection**: fruit on a belt; quality is graded only in the **control zone** (centre cell **C**), not anywhere in the frame.

- **This repo:** runnable **Windows demo** (ImGui shell + DLLs). No Nim product source on `main`.
- **Same three inference DLLs for your own UI:** [fruit-inspector-engine](https://github.com/olesha-ai/fruit-inspector-engine) · [HOST.md](https://github.com/olesha-ai/fruit-inspector-engine/blob/main/HOST.md)

## Download

| File | Use |
|------|-----|
| **[Fruit_Inspector_DLL.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_DLL.7z)** | **Current package (0.10.0):** `Fruit_Inspector.exe`, `fruit_infer.dll`, `fruit_cat.dll`, `fruit_features.dll`, ORT, and apple models. **Use this file.** |
| [Fruit_Inspector.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector.7z) | Older archive on the same release; not the full three-DLL layout. Reference only. |

All assets: [Release 0.10.0](https://github.com/olesha-ai/fruit-inspector-demo/releases/tag/0.10.0)

Unpack with [7-Zip](https://www.7-zip.org/) or similar. Use a local folder, not a network drive.

---

## What is on `main`

- [README.md](README.md), [LICENSE.md](LICENSE.md), [licenses/](licenses/)
- Demo media: `infer.gif`, `processor.jpg`, `speed.jpg`
- Binaries live in **[Releases](https://github.com/olesha-ai/fruit-inspector-demo/releases)** only

## Contents of `Fruit_Inspector_DLL.7z`

- `Fruit_Inspector.exe` — UI, 3×3 grid, conveyor focus/sticky, Windows camera (NV12)
- `fruit_infer.dll`, `fruit_cat.dll`, `fruit_features.dll` — all ONNX Runtime inference; the exe is a **shell** (no in-process ORT)
- `lib/cpu/onnxruntime.dll`
- `model/apple/` — `det.onnx`, `cat.onnx`, `features.json` (apple pack only)
- `settings.json`, `licenses/` (keep the whole folder)

**Not in the default archive:**

- `lib/openvino/`, `lib/cuda/` — bring your own if you switch EP; default is `"ort_provider": "cpu"`
- `trig.exe` — optional LAN helper (see **trig**); usually not bundled
- Pan-tilt / neck — off in this drop (`neck_com_port`: 0)

**Not offered here yet:**

- Linux / ARM demo
- Sample host app (C#, Lazarus, …) — see the engine repo
- Factory PLC chain (stop belt → grade) — direction only, not shipped
- Other crops — apple only unless added later

---

## Pictures

![demo](./infer.gif)

![processor](./processor.jpg)

![speed](./speed.jpg)

**Test bench:** Intel i5-11400, 32 GB RAM, no discrete GPU, USB camera 1920×1080.

**Speed (that bench, CPU EP):** With dual YOLO (640 full frame + 256 ROI refine) and the full loop (preprocess + ORT + postprocess), the console infer loop is about **115 fps** — lower than with only the main detect path; the second model and extra pre/post cost time. Live camera preview is about **30 fps** (camera limit, not infer). `speed.jpg` may show an older setup; trust `[infer]` when you run from cmd.

---

## Idea

- 3×3 grid; **C** = control zone
- Up to two apples on the line; stable IDs; focus on nearest to C; sticky after leave C
- Cat (LightGBM) only when the focus apple centre is inside C
- Pipeline: YOLOX 640 → ROI 256 → 44 features → Cat → GOOD / BAD on screen

---

## Station behaviour

- No full-frame chase — work in C with an ID queue
- Approaching C: detect and track only
- In C: features and verdict
- Left C: sticky handoff to the next ID

---

## Architecture (0.10.0)

```
Camera (NV12) → Fruit_Inspector.exe (UI + conveyor)
                      ↓
              fruit_infer.dll (YOLO)
                      ↓
              RGB crop on host
                      ↓
              fruit_features.dll → fruit_cat.dll
```

---

## trig

Optional LAN client for GOOD/BAD over TCP from the inspector. Full line story (stop section → ready → grade) is not implemented; on the desk, Cat runs on enter C.

---

## Stack

- Nim, ImGui (GLFW), Windows Media Foundation → NV12
- ONNX Runtime CPU in `Fruit_Inspector_DLL.7z`
- `settings.json`: provider, models, camera, threads — edit by hand or Settings → Apply

---

## Run

1. Download [Fruit_Inspector_DLL.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_DLL.7z) and unpack.
2. One tree: exe, three DLLs, `lib/cpu/`, `model/apple/`, `settings.json`, `licenses/`.
3. Set `camera_index` and `ort_provider` (`cpu` first).
4. From cmd: `Fruit_Inspector.exe` for `[infer]` fps on stdout.
5. UI: START / STOP; Esc confirms quit.

---

## License

Testing, teaching, research, and feedback — yes. Commercial production under this license — no.

[LICENSE.md](LICENSE.md), [licenses/Fruit_Inspector_LICENSE.md](licenses/Fruit_Inspector_LICENSE.md), [licenses/THIRD_PARTY.md](licenses/THIRD_PARTY.md). Keep `licenses/` next to the exe.

Commercial inquiries: olesha-ai.

Intel, OpenVINO, Microsoft, Megvii, and other names are trademarks of their respective owners.

---

olesha-ai
