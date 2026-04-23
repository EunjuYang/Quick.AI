<div align="center">

# ☄️ Quick.AI

### LLMs on your device — fast, private, offline.

_Efficient causal language model inference built on top of [NNTrainer](https://github.com/nntrainer/nntrainer), designed to run Qwen, GPT-OSS, Gemma, and more on phones, laptops, and embedded hardware._

<p>
  <a href="https://github.com/EunjuYang/Quick.AI/actions/workflows/ci-linux.yml"><img src="https://github.com/EunjuYang/Quick.AI/actions/workflows/ci-linux.yml/badge.svg" alt="Linux Build"/></a>
  <a href="https://github.com/EunjuYang/Quick.AI/actions/workflows/ci-android.yml"><img src="https://github.com/EunjuYang/Quick.AI/actions/workflows/ci-android.yml/badge.svg" alt="Android Build"/></a>
  <a href="https://github.com/EunjuYang/Quick.AI/actions/workflows/cpp-linter.yml"><img src="https://github.com/EunjuYang/Quick.AI/actions/workflows/cpp-linter.yml/badge.svg" alt="C++ Format"/></a>
  <a href="https://github.com/EunjuYang/Quick.AI/actions/workflows/codeql.yml"><img src="https://github.com/EunjuYang/Quick.AI/actions/workflows/codeql.yml/badge.svg" alt="CodeQL"/></a>
  <img src="https://img.shields.io/badge/license-Apache%202.0-blue.svg" alt="License"/>
  <img src="https://img.shields.io/badge/C%2B%2B-17-00599C?logo=c%2B%2B&logoColor=white" alt="C++17"/>
  <img src="https://img.shields.io/badge/Android-NDK%20r26d-3DDC84?logo=android&logoColor=white" alt="Android NDK"/>
  <img src="https://img.shields.io/badge/platform-Linux%20%7C%20Android-lightgrey" alt="Platform"/>
</p>

</div>

---

## ✨ Why Quick.AI?

- 🔒 **Runs locally, fully offline** — your prompts and model weights never leave the device.
- 🧠 **MoE-ready** — execute large Mixture-of-Experts models (Qwen3-MoE 30B, GPT-OSS 20B) in as little as **1.3 GB** of RAM thanks to Flash Storage Utilization (FSU).
- ⚡ **Fast on commodity hardware** — hand-tuned kernels for ARMv8.2-a (FP16, dotprod, i8mm) and AVX2 on x86_64.
- 🧩 **Pluggable architecture** — each transformer building block (RMSNorm, SwiGLU, QKV, MHA core, tied embeddings, …) ships as an independently loadable layer plugin.
- 🔌 **C + C++ APIs** — embed Quick.AI in any native app, including Android via JNI.
- 📦 **Small footprint** — single ~13 MB `libquick_dot_ai.so` plus per-layer plugins; no Python runtime required at inference time.

---

## 🎬 See it in action

### 📱 MoE on a phone

On-device Mixture-of-Experts inference, streamed directly from flash storage:

| GPT-OSS 20B | Qwen3-MoE 30B-A3B |
|:---:|:---:|
| <img src="docs/videos/GPT_OSS_20B_Demo.gif" width="360"/> | <img src="docs/videos/Qwen_30B_Demo.gif" width="360"/> |

### 💻 FSU: loading experts on the fly

The same Qwen3-30B-A3B model, on the same machine — once as a conventional "load everything into RAM" run, once with Quick.AI's on-the-fly expert loading:

| Load whole model (Qwen3-30B-A3B) | Load experts on-the-fly (Quick.AI / FSU) |
|:---:|:---:|
| <img src="docs/videos/moe-full.gif" width="360"/> | <img src="docs/videos/moe-on-the-fly.gif" width="360"/> |
| 🐘 **Memory: 16.5 GB** | 🪶 **Memory: 1.3 GB** |

> _That's a ~12× reduction in peak memory while keeping the same user-visible behavior._ Try it yourself with the models under `models/*-slim`.

---

## 🤖 Supported models

| Family | Variants | Notes |
|---|---|---|
| **Llama** | 1B / 3B / 7B-class | reference architecture |
| **Qwen2** | 0.5B – 7B | causal LM |
| **Qwen3** | 0.6B, 1.7B, 4B, 7B, 14B, 32B | [HF: Qwen3-4B](https://huggingface.co/Qwen/Qwen3-4B) |
| **Qwen3-MoE** | 30B-A3B | [HF: Qwen3-30B-A3B](https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507) · FSU-enabled |
| **GPT-OSS** | MoE 20B, 120B | [HF: gpt-oss-20b](https://huggingface.co/openai/gpt-oss-20b) · FSU-enabled |
| **Gemma 3** | all causal variants | incl. sentence-embedding head |

Bring your own architecture by writing a small causal-LM subclass — see the [model documentation](models/README.md) for the recipe.

---

## 🚀 Quick start (desktop / Linux)

Quick.AI is built with Meson and depends on [NNTrainer](https://github.com/nntrainer/nntrainer) as a git submodule under `subprojects/nntrainer`.

```bash
# 1. Clone with submodules
git clone --recursive https://github.com/EunjuYang/Quick.AI.git
cd Quick.AI

# 2. Install system deps (Ubuntu 22.04 / 24.04)
sudo apt-get install -y libopenblas-dev libflatbuffers-dev flatbuffers-compiler \
                        build-essential pkg-config
pip install meson ninja

# 3. Build
meson setup build -Denable-fp16=true -Dthread-backend=omp -Domp-num-threads=4
ninja -C build

# 4. Run
export OMP_NUM_THREADS=4 OMP_WAIT_POLICY=active OMP_PROC_BIND=true OMP_PLACES=cores
./build/quick_dot_ai_run ./res/qwen3/qwen3-4b/
```

### Prepare a model

Drop a model into `res/<name>/` containing:
`config.json`, `generation_config.json`, `tokenizer.json`, `tokenizer_config.json`, `vocab.json`, `nntr_config.json`, and the NNTrainer weight `.bin` file that `nntr_config.json` references.

---

## 📱 Android build

Quick.AI ships a fully modular Android build chain (core → API → test app), wired through `ndk-build`.

**Prerequisites:** Android NDK (r21d+), CMake, [Rust](https://rustup.rs) (for `tokenizers-cpp`), `adb`.

```bash
export ANDROID_NDK=/path/to/android-ndk
./build_android.sh       # builds libquick_dot_ai_core.so + quick_dot_ai + quick_dot_ai_quantize
./build_api_lib.sh       # (optional) libquick_dot_ai_api.so
./build_test_app.sh      # (optional) quick_dot_ai_test_api
./install_android.sh     # pushes artifacts to /data/local/tmp/quick_dot_ai/ on the device
```

Run on device:

```bash
adb shell /data/local/tmp/quick_dot_ai/run_quick_dot_ai.sh <model_path>
adb shell /data/local/tmp/quick_dot_ai/run_test_api.sh <model_name> "<prompt>"
```

| Script | Output |
|---|---|
| `build_android.sh` | `libquick_dot_ai_core.so`, `quick_dot_ai`, `quick_dot_ai_quantize` |
| `build_api_lib.sh` | `libquick_dot_ai_api.so` |
| `build_test_app.sh` | `quick_dot_ai_test_api` |

All artifacts land under `jni/libs/arm64-v8a/`.

---

## 🪶 Quantization

Shrink a FP32 checkpoint with `quick_dot_ai_quantize`:

```bash
# Default: FC layers → Q4_0, embedding → FP32
./build/quick_dot_ai_quantize /path/to/qwen3-4b

# Fine-grained dtypes
./build/quick_dot_ai_quantize /path/to/qwen3-4b \
    --fc_dtype Q4_0 --embd_dtype Q6_K --lmhead_dtype FP16

# Write into a separate output dir
./build/quick_dot_ai_quantize /path/to/qwen3-4b -o /out/qwen3-4b-q40
```

**Supported dtypes:** `FP32`, `FP16`, `Q4_0`, `Q4_K`, `Q6_K`.

> ⚠️ **Q4_0 is architecture-specific** — an x86-quantized Q4_0 `.bin` is not byte-compatible with ARM and vice versa. Run `quick_dot_ai_quantize` on the same ISA you plan to serve from.

After quantization, point `quick_dot_ai_run` at the quantized directory (or `mv nntr_config_quantized.json nntr_config.json` in place and rerun).

---

## 🏗️ Architecture at a glance

```
┌─────────────────────── Quick.AI ──────────────────────┐
│                                                       │
│  quick_dot_ai_run / _quantize / _test_api (binaries)  │
│                        │                              │
│  libquick_dot_ai.so  ──┼──  libquick_dot_ai_api.so    │
│                        │                              │
│  per-layer plugins (rms_norm, swiglu, mha_core, …)    │
│                        │                              │
│           NNTrainer (subprojects/nntrainer)           │
│                        │                              │
│         OpenBLAS  ·  OpenMP  ·  Flatbuffers           │
└───────────────────────────────────────────────────────┘
```

- C++ code lives under `namespace quick_dot_ai`.
- NNTrainer is pulled in as a Meson subproject; Quick.AI disables NNTrainer's own Applications and tests to keep the dependency build lean.
- The C API (`api/causal_lm_api.h`) is the stable surface for host integrations — it hasn't been renamed, so existing embedders keep building.

---

## 🧪 Continuous integration

Every PR runs:

- ✅ **Linux build** — Meson + Ninja on Ubuntu 22.04 & 24.04
- ✅ **Android build** — `arm64-v8a`, NDK r26d, Rust `aarch64-linux-android`
- ✅ **C++ format check** — clang-format 14 against `.clang-format`
- ✅ **CodeQL** — security/quality static analysis

CI configuration: [`.github/workflows/`](.github/workflows/).

---

## 📚 Further reading

- [Model implementation guide](models/README.md)
- [C API reference](api/README.md)
- [Benchmark tooling](benchmarks/README.md)
- Papers & talks:
  - [Memory-Efficient LLM Inference on Edge Devices with NNTrainer](https://youtu.be/J2tUmi4bwMY?si=rJyiXkwr5iFrMhIK) — Open Source Summit 2025 Seoul
  - [A New Frontier of AI: On-Device AI Training and Personalization](https://dl.acm.org/doi/abs/10.1145/3639477.3639716) — ICSE-SEIP 2024
  - [NNTrainer: Light-Weight On-Device Training Framework](https://arxiv.org/pdf/2206.04688.pdf) — arXiv 2022

---

## 🤝 Contributing

PRs and issues are very welcome. Before you open one:

1. Run `meson setup build && ninja -C build` locally — the same command CI uses.
2. Run `clang-format -i` on any changed C/C++ files (config in `.clang-format`).
3. If you're adding a new model family, drop it under `models/<your_family>/` and wire it into `models/meson.build` — the factory in `factory.h` does the rest.

## 📄 License

Quick.AI is released under the [Apache License 2.0](LICENSE). NNTrainer, bundled as a submodule, is also Apache-2.0.

## 📖 Citation

If Quick.AI is useful for your research, please cite the NNTrainer paper it builds on:

```bibtex
@inproceedings{10.1145/3639477.3639716,
  author    = {Moon, Jijoong and Lee, Hyeonseok and Chu, Jiho and Park, Donghak and Hong, Seungbaek and Seo, Hyungjun and Jeong, Donghyeon and Kong, Sungsik and Ham, Myungjoo},
  title     = {A New Frontier of AI: On-Device AI Training and Personalization},
  booktitle = {Proceedings of the 46th International Conference on Software Engineering: Software Engineering in Practice},
  series    = {ICSE-SEIP '24},
  year      = {2024},
  pages     = {323--333},
  doi       = {10.1145/3639477.3639716}
}
```
