# ☄️ Quick.AI — CausalLM Inference with NNTrainer

This application provides a standalone executable and an optional C API to run causal LLM models using NNTrainer.
It supports *inference* mode (text generation) on various devices, including Android.

## Features

- **Standalone Application (`quick_dot_ai_run`)**: A command-line tool to load models and generate text.
- **C API (Optional)**: A lightweight C interface (`libquick_dot_ai_api.so`) for integrating LLM capabilities into other applications (e.g., Android JNI, iOS, or other C/C++ apps).
- **Core Library**: The core implementation is separated into `libquick_dot_ai_core.so` for modularity.
- **Supported Backends**: CPU (OpenMP), with GPU/NPU support planned.

## Supported models

- Llama
- Qwen3 (0.6B, 1.7B, 4B, 7B, 14B, 32B) [[link](https://huggingface.co/Qwen/Qwen3-4B)]
- Qwen3-MoE (30B-A3B) [[link](https://huggingface.co/Qwen/Qwen3-30B-A3B-Instruct-2507)]
- GPT-OSS (MoE: 20B, 120B) [[link](https://huggingface.co/openai/gpt-oss-20b)]
- You can try your own model with custom layers!
- Feel free to contribute! 😊

For more details, please refer to the [Model Documentation](models/README.md).

## Quick.AI API

Quick.AI exposes a C API for easy integration with other applications (e.g., Android JNI).
The API allows loading models, running inference, and retrieving performance metrics.

For detailed documentation, please refer to [API Documentation](api/README.md).

## How to run

### 1. Prepare Model Files
- Download and copy the model files from huggingface to `res/{model}` directory.
- The folder should contain:
    - `config.json`
    - `generation_config.json`
    - `tokenizer.json`
    - `tokenizer_config.json`
    - `vocab.json`
    - `nntr_config.json`
    - nntrainer weight binfile (matches with the name in `nntr_config.json`)

### 2. PC Build & Test

This repository builds against `nntrainer`, which is bundled as a git submodule under `subprojects/nntrainer`. Clone with submodules (or init them after cloning):

```bash
$ git clone --recursive https://github.com/EunjuYang/Quick.AI.git
$ cd Quick.AI
# or, if already cloned without --recursive:
$ git submodule update --init --recursive
```

Compile the application:

```bash
$ meson setup build -Denable-fp16=true -Dthread-backend=omp -Domp-num-threads=4
$ ninja -C build
```

Run the model:

```bash
$ export OMP_THREAD_LIMIT=16 && export OMP_WAIT_POLICY=active && export OMP_PROC_BIND=true && export OMP_PLACES=cores && export OMP_NUM_THREADS=4
$ ./build/quick_dot_ai_run {your model config folder}
```

e.g.,
```bash
$ ./build/quick_dot_ai_run ./res/qwen3/qwen3-4b/
```

### 3. Android Build & Test

The Android build process is modularized to support building the core library, API library, and test applications independently.

#### Prerequisites
- Android NDK (e.g., r21d or later)
- CMake
- Rust (for tokenizers-cpp)
- ADB (Android Debug Bridge)

#### Build Scripts

The following scripts are provided at the repository root to handle the build process:

1.  **`build_android.sh`** (Core + App):
    - Builds `nntrainer` core library for Android.
    - Builds `tokenizers-cpp` dependency if missing.
    - Compiles **`libquick_dot_ai_core.so`** (Core logic) and **`quick_dot_ai`** (Main Executable).
    - **Usage**: `./build_android.sh`

2.  **`build_api_lib.sh`** (API Library):
    - Requires `libquick_dot_ai_core.so` (run `build_android.sh` first).
    - Compiles **`libquick_dot_ai_api.so`** (C-API wrapper).
    - **Usage**: `./build_api_lib.sh`

3.  **`build_test_app.sh`** (Test App):
    - Requires both Core and API libraries.
    - Compiles **`quick_dot_ai_test_api`** (Simple C++ test app for API).
    - **Usage**: `./build_test_app.sh`

4.  **`install_android.sh`**:
    - Installs all built artifacts to a connected Android device.
    - Creates helper scripts (`run_quick_dot_ai.sh`, `run_test_api.sh`) on the device.
    - **Usage**: `./install_android.sh`

#### Build Instructions

1.  **Set NDK Path**:
    ```bash
    export ANDROID_NDK=/path/to/your/android-ndk
    ```

2.  **Build Core & Main App**:
    ```bash
    ./build_android.sh
    ```
    Artifacts in `jni/libs/arm64-v8a/`:
    - `libquick_dot_ai_core.so`
    - `quick_dot_ai`

3.  **Build API Library (Optional)**:
    ```bash
    ./build_api_lib.sh
    ```
    Artifacts:
    - `libquick_dot_ai_api.so`

4.  **Build Test App (Optional)**:
    ```bash
    ./build_test_app.sh
    ```
    Artifacts:
    - `quick_dot_ai_test_api`

5.  **Install & Run**:
    ```bash
    ./install_android.sh
    ```

    **Run Main App:**
    ```bash
    adb shell /data/local/tmp/quick_dot_ai/run_quick_dot_ai.sh [model_path]
    ```

    **Run API Test:**
    ```bash
    adb shell /data/local/tmp/quick_dot_ai/run_test_api.sh [model_name] [prompt]
    ```
## Quantizing Models

Quick.AI provides a quantization utility (`quick_dot_ai_quantize`) that converts FP32 CausalLM model weights to lower-precision data types, reducing model size for efficient on-device inference.

### Supported Quantization Types

| Data Type | Description |
|-----------|-------------|
| `FP32`    | 32-bit floating point (default for embedding/LM head) |
| `FP16`    | 16-bit floating point |
| `Q4_0`    | 4-bit quantization (default for FC layers) |
| `Q4_K`    | 4-bit K-quant quantization |
| `Q6_K`    | 6-bit K-quant quantization |

> **Note (Q4_0 platform dependency):** `Q4_0` quantization produces platform-specific binary formats — the output generated on x86 is **not compatible** with ARM, and vice versa. You must run `quick_dot_ai_quantize` on the **same platform architecture** where the quantized model will be used for inference. Cross-platform quantization is not yet supported.


### Prerequisites

The model directory must contain the following files:
- `config.json` – model architecture configuration
- `generation_config.json` – generation parameters
- `nntr_config.json` – NNTrainer-specific configuration
- `.bin` weight file – FP32 model weights

### Building

The quantization utility is built automatically with the Quick.AI application:

```bash
meson setup build && ninja -C build
# The executable is: build/quick_dot_ai_quantize
```

### Usage

```
quick_dot_ai_quantize <model_path> [options]
```

**Options:**

| Option | Description | Default |
|--------|-------------|---------|
| `--output`, `-o <path>` | Output directory | Same as `<model_path>` |
| `--fc_dtype <type>` | Target dtype for FC (fully-connected) layers | `Q4_0` |
| `--embd_dtype <type>` | Target dtype for embedding layer | `FP32` |
| `--lmhead_dtype <type>` | Target dtype for LM head layer | Same as `embd_dtype` |
| `--output_bin <name>` | Output `.bin` filename | Auto-generated |
| `--config <path>` | Use a target `nntr_config.json` for dtype settings | – |

### Examples

```bash
# Quantize FC layers to Q4_0 (default), embedding stays FP32:
quick_dot_ai_quantize /path/to/qwen3-4b

# Quantize FC layers to Q4_0 and embedding to Q6_K:
quick_dot_ai_quantize /path/to/qwen3-4b --fc_dtype Q4_0 --embd_dtype Q6_K

# Quantize to a different output directory:
quick_dot_ai_quantize /path/to/qwen3-4b -o /output/qwen3-4b-q4

# Use a pre-configured target nntr_config.json:
quick_dot_ai_quantize /path/to/qwen3-4b --config /path/to/target_nntr_config.json
```

### Output

The utility produces:
1. A quantized `.bin` weight file (filename auto-generated or specified via `--output_bin`)
2. A new `nntr_config_quantized.json` (or `nntr_config.json` if output directory differs from source)

After quantization, run the quantized model:
```bash
# If output is in the same directory:
mv /path/to/model/nntr_config_quantized.json /path/to/model/nntr_config.json
quick_dot_ai_run /path/to/model

# If output is in a different directory:
cp /path/to/model/config.json /path/to/model/generation_config.json /output/dir/
quick_dot_ai_run /output/dir
```
