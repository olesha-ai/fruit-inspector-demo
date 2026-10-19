 Fruit Inspector

build **0.10.0** · Windows x64 · Nim · ONNX Runtime · conveyor demo (eval)

Fruit Inspector is **conveyor inspection**: fruit on a belt; quality is graded only in the **control zone** (centre cell **C**), not anywhere in the frame.

Libraries: **[fruit-inspector-engine](https://github.com/olesha-ai/fruit-inspector-engine)** (same three DLLs + [HOST.md](https://github.com/olesha-ai/fruit-inspector-engine/blob/main/HOST.md) for your own UI).  
This repo: **runnable Windows demo** (exe shell + DLLs), not the Nim source tree.

**Download (main, GUI + libraries):** [Fruit_Inspector_DLL.7z](https://github.com/olesha-ai/fruit-inspector-demo/releases/download/V0.10.0/Fruit_Inspector_DLL.7z) · [All release assets](https://github.com/olesha-ai/fruit-inspector-demo/releases/tag/V0.10.0)

---

## What exists today (honest)

**In this GitHub repo (main branch):**

- This README, [LICENSE.md](LICENSE.md), [licenses/](licenses/)
- Screenshots / GIF (`infer.gif`, `processor.jpg`, `speed.jpg`)
- **No** Nim product source here — only the eval demo package in **Releases**

**In `Fruit_Inspector_DLL.7z` (use this one — exe + three DLLs + ORT + models):**

- `Fruit_Inspector.exe` — UI, 3×3 grid, conveyor focus/sticky, camera (Windows Media Foundation → **NV12**)
- `fruit_infer.dll`, `fruit_cat.dll`, `fruit_features.dll` — **all** ORT inference in DLLs; exe is a **shell** (no in-process ORT fallback)
- `lib/cpu/onnxruntime.dll`
- `model/apple/` — `det.onnx`, `cat.onnx`, `features.json` (**apple pack only**)
- `settings.json`, full `licenses/` folder

**Often not in the public demo zip** (unless I explicitly add them):

- `lib/openvino/`, `lib/cuda/` — EP folders for OpenVINO/CUDA; my public drop is usually **CPU only** (`ort_provider`: `cpu`)
- `trig.exe` — separate LAN helper; may or may not be bundled (see **trig** below)
- Pan-tilt / neck hardware — **off** in this product build (`neck_com_port` 0)

**Does not exist (yet):**

- Linux / ARM build in this demo repo
- Sample client project (C#, Lazarus, …) — headers + engine zip only
- “Full factory line” trig chain (stop belt → grade) — design direction, not finished product
- Other crops in the zip — only **apple** models unless I add packs later

---

## Pictures

![demo](./infer.gif)

![processor](./processor.jpg)

![speed](./speed.jpg)

Test bench: Intel **i5-11400**, 32 GB RAM, **no discrete GPU**, USB camera 1920×1080.

**Speed — honest numbers:**  
With **dual YOLO** (640 + **256 ROI refine**) and the **full loop** (preprocess + ORT + postprocess), CPU EP gives about **~115 fps** on the console infer loop on my desk — **lower** than with only the main detect path; the second model and pre/post cost time.  
Live camera preview ~**30 fps** (camera, not infer).  
`speed.jpg` may not match the current dual-model loop; use `[infer]` from cmd on your PC.

---

## Idea

- **3×3 grid**; **C** = control zone
- Up to **two** apples on the line; stable IDs; **focus** on nearest to C; **sticky** after leave C
- **Cat** runs only when focus centre is **inside C**
- Pipeline: **YOLOX** 640 → ROI **256** → **44 features** → Cat → GOOD / BAD on screen

---

## Station behaviour

- No full-frame chase — C + ID queue
- Approaching C: detect/track only
- In C: features + verdict
- Left C: next sticky ID

---

## trig

Small **LAN client** (`tools/trig` in dev; optional `trig.exe` in zip). Shows GOOD/BAD over TCP from the inspector.  
**What is not done:** PLC/stop-the-belt “ready to grade” — that is the intended factory story, not what the desk demo does today (Cat on **enter C**).

---

## Stack (what the running demo actually uses)

- Nim + ImGui (GLFW), **Windows** capture → NV12
- ONNX Runtime **CPU** in the usual public zip
- `settings.json` can say `openvino` / `cuda` if **you** add the matching `lib/` trees — not promised in the default download

---

## Run (after you have the zip)

1. Unpack locally (not from a network drive).
2. Same folder: exe, three DLLs, `lib/cpu/`, `model/apple/`, `settings.json`, `licenses/`.
3. Set `camera_index`, `ort_provider` (`cpu` first).
4. Cmd: `Fruit_Inspector.exe` if you want `[infer]` fps on stdout.
5. UI: START / STOP; Esc confirms quit.

---

## License

Testing, teaching, research, feedback — yes.  
Commercial production under this license — no.  
[LICENSE.md](LICENSE.md), [licenses/Fruit_Inspector_LICENSE.md](licenses/Fruit_Inspector_LICENSE.md), [licenses/THIRD_PARTY.md](licenses/THIRD_PARTY.md) — keep `licenses/` in the zip.

Commercial: olesha-ai.

Trademarks belong to their owners.

---

olesha-ai
