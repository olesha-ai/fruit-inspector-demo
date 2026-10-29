# Fruit Inspector

**Build 0.10.0** · Windows x64 · Linux x64 · Nim · ONNX Runtime (CPU) · conveyor station demo (eval)

Fruit Inspector is **conveyor inspection**: fruit on a belt; quality is graded only in the **control zone** (centre cell **C**), not anywhere in the frame.

- **This repo:** runnable **Windows and Linux demos** (ImGui shell + three inference libraries). No Nim product source on `main`.
- **Same three inference DLLs for your own UI:** [fruit-inspector-engine](https://github.com/olesha-ai/fruit-inspector-engine) · [HOST.md](https://github.com/olesha-ai/fruit-inspector-engine/blob/main/HOST.md)

## Download

| File | Use |
|------|-----|
| **[Fruit_Inspector_DLL.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_DLL.7z)** | **Windows (0.10.0):** `Fruit_Inspector.exe`, **`trig.exe`**, three fruit DLLs, ORT CPU + MSVC in `lib/cpu/`, apple models. **Use this on Win64.** |
| **[Fruit_Inspector_linux_app_x64.zip](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_linux_app_x64.zip)** | **Linux x64 (0.10.0):** portable folder — binary, three `.so`, bundled ORT, models, `run.sh`. Debian 12/13, Ubuntu 22.04+. |
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
- **`trig.exe`** — TCP client for **`GOOD <id>` / `BAD <id>`** from the inspector (see **trig**)
- `fruit_infer.dll`, `fruit_cat.dll`, `fruit_features.dll` — ONNX Runtime inference; the exe is a **shell** (no in-process ORT)
- `lib/cpu/` — `onnxruntime.dll`, `onnxruntime_providers_shared.dll`, plus **MSVC CRT** (`MSVCP140*`, `VCRUNTIME140*`) so you do not install VC++ Redist separately
- `libgcc_s_seh-1.dll`, `libstdc++-6.dll`, `libwinpthread-1.dll` — MinGW runtime for the Nim binaries
- `model/apple/` — `det.onnx`, `cat.onnx`, `features.json` (apple pack only)
- `settings.json`, `LICENSE.md`, `licenses/` (keep the whole folder)

**Not in the default archive:**

- `lib/openvino/`, `lib/cuda/` — optional; copy from a full dev tree if you switch EP away from CPU
- Default `"ort_provider": "cpu"` in `settings.json`

**Product mode:** conveyor **station** (cell **C**); pan-tilt / neck tracking is **off** in this build (`NeckTrackingEnabled = false` in the shipped product).

## Contents of `Fruit_Inspector_linux_app_x64.zip`

- `Fruit_Inspector` — main app (ext4 / home disk); same binary as `Fruit_Inspector.exe` for FAT USB launchers
- `fruit_infer.so`, `fruit_cat.so`, `fruit_features.so` — ONNX Runtime inference; the app is a **shell**
- `lib/linux/` — bundled ONNX Runtime CPU
- `model/apple/`, `settings.json`, `licenses/`
- `run.sh`, `install_to_home.sh`, `Fruit_Inspector.desktop` — launch (use **`run.sh`** on USB; see `README.txt` inside the zip)

**CPU:** x86_64 with **SSE4.2 + AVX1** (AVX2 not required for this demo drop). **Not** ARM / Raspberry Pi.

**Not offered here yet:**

- ARM demo
- Sample host app (C#, Lazarus, …) — see the engine repo
- Factory PLC chain (stop belt → ready → grade) — direction only, not shipped
- Other crops — apple only unless added later

---

## Pictures

![demo](./infer.gif)

![processor](./processor.jpg)

![speed](./speed.jpg)

**Test bench:** Intel i5-11400, 32 GB RAM, no discrete GPU, USB camera 1920×1080.

**Speed (that bench, CPU EP):** Station path: YOLO on control cell **C** (640) plus ROI refine **256**, then features + Cat. Console `[infer]` fps depends on that dual path and pre/post — often well above live preview. Live camera preview is about **30 fps** (camera limit, not infer). `speed.jpg` may show an older setup; trust `[infer]` when you run from cmd.

---

## Idea

- 3×3 grid; **C** = control zone
- Up to two apples on the line; stable IDs; focus on nearest to C; sticky after leave C
- Cat (LightGBM) only when the focus apple centre is inside C
- Pipeline: YOLOX on **C** (640) → ROI 256 → 44 features → Cat → **GOOD / BAD** on screen and on Out TCP

---

## Station behaviour

- No full-frame chase — work in **C** with an ID queue
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
                      ↓
              Out TCP  "GOOD|BAD <id>"  →  trig.exe (LAN display / integration)
```

---

## trig

**Included in the Windows demo archive.** Desktop client that connects to the inspector over TCP (host/port from `settings.json`: `ip_addr`, `ip_port`). After auth, it shows **`GOOD <apple id>`** and **`BAD <apple id>`** — English only, no coordinates, no turret FIRE.

Use it on a second PC or beside the line to prove the **Out** contract without writing your own host. Full factory story (stop section → ready → grade) is future work; on the desk, Cat runs when the focus apple is in **C**.

---

## Stack

- Nim, ImGui (GLFW); camera: Windows Media Foundation or Linux V4L2 → NV12
- ONNX Runtime CPU in the release archives
- `settings.json`: provider, models, camera, threads, Out bind — edit by hand or Settings → Apply

---

## Run (Windows)

1. Download [Fruit_Inspector_DLL.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_DLL.7z) and unpack.
2. One tree: `Fruit_Inspector.exe`, **`trig.exe`**, three fruit DLLs, MinGW DLLs next to exe, `lib/cpu/`, `model/apple/`, `settings.json`, `licenses/`.
3. Set `camera_index` and `ort_provider` (`cpu` first).
4. From cmd: `Fruit_Inspector.exe` for `[infer]` fps on stdout.
5. UI: START / STOP; Esc confirms quit.
6. **trig:** start `trig.exe`, connect to the inspector IP/port (same machine: loopback + port from `settings.json`).

## Run (Linux)

1. Download [Fruit_Inspector_linux_app_x64.zip](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/0.10.0/Fruit_Inspector_linux_app_x64.zip) and unzip.
2. `chmod +x run.sh` (ext4) or always: `bash /path/to/Fruit_Inspector_linux_app_x64/run.sh`.
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
