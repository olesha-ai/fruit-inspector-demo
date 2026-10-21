# Fruit Inspector

**Build 0.10.0** · Windows x64 · Linux x64 · Nim · ONNX Runtime (CPU) · conveyor station demo (eval)

Fruit Inspector is **conveyor inspection**: fruit on a belt; quality is graded only in the **control zone** (centre cell **C**), not anywhere in the frame.

- **This repo:** runnable **Windows and Linux demos** (ImGui shell + inference libraries). No Nim product source on `main`.
- **Same three inference DLLs for your own UI:** [fruit-inspector-engine](https://github.com/olesha-ai/fruit-inspector-engine) · [HOST.md](https://github.com/olesha-ai/fruit-inspector-engine/blob/main/HOST.md)

## Download

| File | Use |
|------|-----|
| **[Fruit_Inspector_DLL.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_DLL.7z)** | **Windows (0.10.0):** `Fruit_Inspector.exe`, three DLLs, ORT, apple models. **Use this on Win64.** |
| **[Fruit_Inspector_linux_x64.zip](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_linux_x64.zip)** | **Linux x64 (0.10.0):** portable folder — binary, three `.so`, bundled ORT, models, `run.sh`. Debian 12/13, Ubuntu 22.04+. |
| [Fruit_Inspector.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector.7z) | Older Windows archive on the same release; not the full three-DLL layout. Reference only. |

All assets: [Release 0.10.0](https://github.com/olesha-ai/fruit-inspector-demo/releases/tag/0.10.0)

Windows: unpack with [7-Zip](https://www.7-zip.org/) or similar. Linux: unzip the zip. Use a local folder, not a network drive.

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

## Contents of `Fruit_Inspector_linux_x64.zip`

- `Fruit_Inspector` — main app (ext4 / home disk); same binary as `Fruit_Inspector.exe` for FAT USB launchers
- `fruit_infer.so`, `fruit_cat.so`, `fruit_features.so` — ONNX Runtime inference; the app is a **shell**
- `lib/linux/` — bundled ONNX Runtime CPU
- `model/apple/`, `settings.json`, `licenses/`
- `run.sh`, `install_to_home.sh`, `Fruit_Inspector.desktop` — launch (use **`run.sh`** on USB; see `README.txt` inside the zip)

**CPU:** x86_64 with **SSE4.2 + AVX1** (AVX2 not required for this demo drop). **Not** ARM / Raspberry Pi.

**Not offered here yet:**

- ARM demo
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
Camera (NV12) → Fruit_Inspector (UI + conveyor)
                      ↓
              fruit_infer (.dll / .so) (YOLO)
                      ↓
              RGB crop on host
                      ↓
              fruit_features → fruit_cat
```

---

## trig

Optional LAN client for GOOD/BAD over TCP from the inspector. Full line story (stop section → ready → grade) is not implemented; on the desk, Cat runs on enter C.

---

## Stack

- Nim, ImGui (GLFW); camera: Windows Media Foundation or Linux V4L2 → NV12
- ONNX Runtime CPU in the release archives
- `settings.json`: provider, models, camera, threads — edit by hand or Settings → Apply

---

## Run (Windows)

1. Download [Fruit_Inspector_DLL.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_DLL.7z) and unpack.
2. One tree: exe, three DLLs, `lib/cpu/`, `model/apple/`, `settings.json`, `licenses/`.
3. Set `camera_index` and `ort_provider` (`cpu` first).
4. From cmd: `Fruit_Inspector.exe` for `[infer]` fps on stdout.
5. UI: START / STOP; Esc confirms quit.

## Run (Linux)

1. Download [Fruit_Inspector_linux_x64.zip](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_linux_x64.zip) and unzip.
2. `chmod +x run.sh` (ext4) or always: `bash /path/to/Fruit_Inspector_linux_x64/run.sh`.
3. Install once: GL/Mesa/X11 libs, `v4l-utils`; user in group `video` for USB camera.
4. On FAT USB: do **not** run bare `Fruit_Inspector` — use `.desktop`, `Fruit_Inspector.exe`, or `run.sh` (details in zip `README.txt`).
5. Same UI behaviour as Windows; logs under `logs/`.

---

## License

Testing, teaching, research, and feedback — yes. Commercial production under this license — no.

[LICENSE.md](LICENSE.md), [licenses/Fruit_Inspector_LICENSE.md](licenses/Fruit_Inspector_LICENSE.md), [licenses/THIRD_PARTY.md](licenses/THIRD_PARTY.md). Keep `licenses/` next to the exe.

Commercial inquiries: olesha-ai.

Intel, OpenVINO, Microsoft, Megvii, and other names are trademarks of their respective owners.

---

olesha-ai
