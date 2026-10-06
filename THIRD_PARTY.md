# Third-party licenses (Fruit Inspector)

Fruit Inspector **uses and may redistribute** the components below.  
**This folder must ship with the evaluation zip.** Where possible, include the **full license/NOTICE text** from the vendor bundle (ORT/OpenVINO download, model cards).

| Component | Role in Fruit Inspector | Typical license | Notes |
|-----------|-------------------------|-----------------|-------|
| **ONNX Runtime** | Inference EP (`cpu`, optional CUDA) | [MIT](https://github.com/microsoft/onnxruntime/blob/main/LICENSE) | `onnxruntime.dll`, provider DLLs |
| **Intel OpenVINO** | Optional ORT EP | [Apache 2.0](https://github.com/openvinotoolkit/openvino/blob/master/LICENSE) | DLLs under `lib/openvino/` |
| **YOLOX** | Detector ONNX (`det.onnx`) | Apache 2.0 (Megvii / upstream) | Weights trained/exported for this product |
| **LightGBM** | Cat classifier ONNX (`cat.onnx`) | BSD-3-Clause (upstream) | Trained weights shipped in `model/` |
| **Nim** | Language / runtime | MIT | Compiler not shipped; runtime linked in exe |
| **Dear ImGui** | UI | MIT | via nimgl |
| **GLFW** | Window / GL | zlib/libpng | via nimgl |
| **NiGui** | Hidden host window | Check upstream NiGui / nigui.nimble | Vendored subset |
| **libyuv** | NV12 → RGB (camera path) | BSD-3-Clause | Static link in camera build |
| **Noto Sans** | UI font (embedded) | [SIL OFL 1.1](https://scripts.sil.org/OFL) | See `ico/NotoSans-OFL.txt` in source tree |
| **stb** | Image load (icons) | Public domain / MIT | via stb_image |
| **MinGW runtime** | `libgcc`, `libstdc++`, `libwinpthread` | GCC exception / upstream | Next to exe on Windows |
| **Media Foundation** | USB camera (Windows) | Microsoft platform terms | System / redistributable per Microsoft |

## What to copy into `licenses/` for a release zip

Minimum recommended files (names may match your vendor tree):

1. `ONNX_Runtime_LICENSE.txt` — from ORT release package  
2. `OpenVINO_LICENSE.txt` — from OpenVINO runtime bundle  
3. `YOLOX_NOTICE.txt` — Apache 2.0 notice + link to upstream YOLOX  
4. `LightGBM_LICENSE.txt` — BSD notice for LightGBM if Cat ONNX is included  
5. `NotoSans_OFL.txt` — font license (or copy from product `ico/NotoSans-OFL.txt`)  
6. `Fruit_Inspector_LICENSE.md` — **our** eval license (this repo)

## Not affiliated

Intel®, OpenVINO™, ONNX Runtime, Microsoft®, Windows®, Megvii / YOLOX names are trademarks of their owners. Fruit Inspector is an independent project by olesha-ai.
