# Fruit Inspector — Non-Commercial Evaluation License

**This is not a commercial license.**

This **evaluation build** may be used only for **testing**, **student / teaching use**, and **scientific research**. Source code may be published separately under other terms; the **binary evaluation package** is governed by this document. No commercial, industrial, or production rights are granted here.

Copyright © 2026 olesha-ai. All rights reserved.

## 1. Permitted use

You may download, install, and run the compiled Fruit Inspector binaries (`Fruit_Inspector.exe`, `fruit_features.dll`, optional `trig.exe`, associated DLLs / models) **only** for:

- Personal or lab testing and benchmarking (including desk / conveyor mock-ups)
- Student coursework, teaching demos, university projects
- Scientific research and non-commercial academic experiments
- Sending the author feedback (performance, cameras, models, line logic, bugs)

## 2. Prohibited without a separate written agreement

Under this license you may **not**:

- Use the software for commercial, industrial, OEM, or paid production purposes
- Deploy it on a factory sorting / inspection **production line** as a commercial product or paid service
- Sell, rent, or sublicense the binaries or model packs for profit
- Reverse-engineer or decompile the binaries to extract source (where source is not separately offered)
- Redistribute the evaluation package publicly without this license still applying
- Remove copyright / brand notices from the binaries or embedded assets

## 3. No commercial rights here

If you need commercial, OEM, or production-line deployment, that requires a **separate written agreement**. Until then you have no commercial rights under this file.

Contact: GitHub Issue (`license-inquiry`) or the author’s GitHub profile.

## 4. Liability

THIS SOFTWARE IS PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND. THE AUTHOR IS NOT LIABLE FOR DAMAGES FROM USE OR MISUSE, INCLUDING DOWNTIME, HARDWARE DAMAGE, DATA LOSS, OR WRONG GOOD/BAD CLASSIFICATIONS. YOU RUN IT AT YOUR OWN RISK.

## 5. Third-party (not our IP)

Fruit Inspector links against and may ship DLLs / model files from others. This license does **not** own those projects. See [THIRD_PARTY.md](THIRD_PARTY.md) and the other files in this folder.

| Component | Typical license |
|-----------|-----------------|
| Intel OpenVINO | Apache 2.0 |
| ONNX Runtime | MIT |
| YOLOX (detector line) | Apache 2.0 (upstream) |
| LightGBM / Cat ONNX (if shipped) | upstream + trained weights in eval package |
| `fruit_features` | part of this product; eval terms above |
| Nim, Dear ImGui, GLFW, NiGui, libyuv | MIT / zlib / BSD as upstream |
| Google Noto Sans (UI font, if embedded) | SIL Open Font License 1.1 |
| MinGW runtime DLLs next to the exe | GCC runtime exception / upstream |
| Windows Media Foundation | Microsoft platform terms |

Keep the `licenses/` folder with any zip. Full upstream texts for redistributed DLLs belong here.

Fruit Inspector is not an Intel, Microsoft, or Megvii product and is not endorsed by them. Trademarks belong to their owners.

Vision / Out rails share heritage with [EdgeInfer](https://github.com/olesha-ai/edgeinfer-eval) and [PanTilt](https://github.com/olesha-ai/pan-tilt-ai-tracker); those projects have their own license texts.

---

*By running `Fruit_Inspector.exe` from this evaluation package you agree to the limits above.*
