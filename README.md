
# 🚀 MLX-LM Batch Server: High-Throughput Inference on Apple Silicon

**MLX-LM Batch Server** is a lightweight, high-performance inference server designed specifically for Apple Silicon. By implementing **Dynamic Batching** and **Radix Tree-based Prompt Caching**, it significantly boosts LLM throughput for concurrent requests.

> [!IMPORTANT]
> **Performance Boost:** Achieve **4x ~ 5x speedup** in throughput (TPS) with a concurrency of 8 compared to standard sequential inference.

---

## ✨ Key Features

* **⚡ Dynamic Batching:** Automatically merges multiple concurrent requests into a single inference batch, maximizing Apple Silicon GPU utilization.
* **🧠 Radix Tree Prompt Caching:** Intelligent KV cache reuse for shared prefixes (system prompts, chat history), drastically reducing **Time To First Token (TTFT)**.
* **🔌 OpenAI Compatible:** Drop-in replacement for OpenAI API (`/v1/chat/completions`), supporting both **Streaming** and Non-streaming modes.
* **📊 Built-in Benchmarking:** Professional tools included to measure TPS, TTFT, and Inter-Token Latency (ITL).

---

## 🛠️ Installation

Install directly from GitHub using `pip`:

```bash
pip install git+https://github.com/trillionmonster/mlx-lm-batch-server.git

```

*Ensure you have `mlx-lm` and `numpy` installed in your environment.*

---

## 🚀 Quick Start

### 1. Launch the Server

Start the server with a single command. We recommend enabling the prompt cache for multi-turn conversations.

```bash
# Basic usage
mlx_lm.batch_server --model mlx-community/Llama-3.2-3B-Instruct-4bit --port 8080

# High-performance mode (Recommended)
mlx_lm.batch_server \
  --model mlx-community/Llama-3.2-3B-Instruct-4bit \
  --use-prompt-cache \
  --radix-cache-size 2147483648  # 2GB Cache

```

### 2. Standard API Call

The server is compatible with the OpenAI SDK or `curl`:

```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "default",
    "messages": [{"role": "user", "content": "Explain Dynamic Batching in one sentence."}],
    "stream": true
  }'

```

---

## ⚙️ Configuration

| Argument | Description | Default |
| --- | --- | --- |
| `--model` | Model path or Hugging Face repo ID (**Required**) | - |
| `--batch-size` | Maximum batch size for concurrent requests | `32` |
| `--use-prompt-cache` | Enable Radix Tree-based prompt caching | `False` |
| `--radix-cache-size` | Size of the Radix Cache in bytes | `1073741824` (1GB) |
| `--port` | Port to listen on | `8080` |
| `--host` | Host address to bind | `127.0.0.1` |

---

## 📈 Performance & Benchmarking

### Stress Testing

Use the included `benchmark_client.py` to evaluate performance under load:

```bash
python mlx_lm/examples/benchmark_client.py \
  --concurrency 8 \
  --requests 50 \
  --stream \
  --model mlx-community/Llama-3.2-3B-Instruct-4bit

```

### Metrics Explained

* **TPS (Tokens Per Second):** Overall system throughput.
* **TTFT (Time To First Token):** Critical for perceived responsiveness.
* **ITL (Inter-Token Latency):** Smoothness of the generation.

### Prompt Cache Validation

To verify the acceleration of KV cache reuse:

```bash
python mlx_lm/examples/test_prompt_cache.py --model mlx-community/Llama-3.2-3B-Instruct-4bit

```

---

## 🗺️ Roadmap

* [ ] Support for Continuous Batching (vLLM style).
* [ ] Multi-LoRA adapter swapping support.
* [ ] Better observability with Prometheus metrics.

---

## 📄 License

This project is licensed under the [MIT License](https://www.google.com/search?q=LICENSE).

---
