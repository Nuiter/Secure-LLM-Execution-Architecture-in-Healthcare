# 🚀 n8n-API-unleashed 🚀

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![GitHub Stars](https://img.shields.io/github/stars/Nuiter/n8n-API-unleashed?style=social)](https://github.com/Nuiter/n8n-API-unleashed/stargazers) [![GitHub Forks](https://img.shields.io/github/forks/Nuiter/n8n-API-unleashed?style=social)](https://github.com/Nuiter/n8n-API-unleashed/network/members) [![Project Status: Active](https://img.shields.io/badge/status-active-success.svg)](https://github.com/Nuiter/n8n-API-unleashed) [![Built with Docker](https://img.shields.io/badge/Built%20with-Docker-blue?logo=docker)](https://www.docker.com/)

A Docker Compose stack to unleash the true potential of n8n by freeing it from the dependency on paid APIs.

This project allows you to deploy an n8n instance alongside a 100% local AI server, enabling you to run powerful Large Language Models (LLMs) directly on your own machine.

Take back control. Say goodbye to monthly API bills. Your automation, your rules, your AI.

---

## ✨ Features

- **API Freedom:** Run AI-driven workflows with zero API costs, no rate limits, and complete privacy.
- **Self-Hosted n8n:** Your private, limitless instance of n8n, ready to automate everything.
- **Local AI Server:** Configured by default to run on **CPU** (powered by Llama.cpp), ensuring maximum compatibility across a wide range of hardware.
- **GPU Scaling Path:** Includes a fully documented configuration to leverage your **NVIDIA GPU** for maximum performance.
- **One-Command Deployment:** The entire stack is brought up with the magic of Docker Compose.

---

## 🔧 Prerequisites

1.  **Docker & Docker Compose:** [Installation Guide](https://docs.docker.com/get-docker/).
2.  **Git** to clone the repository.

**For the optional GPU setup:**

-   An **NVIDIA GPU** with sufficient VRAM for the desired model.
-   Up-to-date **NVIDIA Drivers** on your host machine.
-   **NVIDIA Container Toolkit:** [Installation Guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html).

---

## ⚡ Quickstart Guide (CPU by Default)

This is the recommended setup to get you started.

### 1. Clone the Repository

```bash
git clone https://github.com/Nuiter/n8n-API-unleashed.git
cd n8n-API-unleashed
```

### 2. Download a Model

This stack uses the GGUF model format, which is optimized for CPU inference.

1.  Create the model directory: `mkdir model-cache`
2.  Download a model. We recommend starting with **Microsoft's Phi-3 Mini Instruct**. It offers an incredible balance of performance and quality for its small size. You can find quantized versions at [TheBloke's repository](https://huggingface.co/TheBloke/Phi-3-mini-4k-instruct-GGUF). A well-balanced choice is `phi-3-mini-4k-instruct.Q4_K_M.gguf`.
3.  Move the downloaded `.gguf` file into the `model-cache` directory.
4.  Ensure the filename in the directory matches the one specified in the `command` of the `ia-server-cpu` service in the `docker-compose.yml` file.

### 3. Launch the Stack

From the project's root directory, run:

```bash
docker-compose up -d
```

The first time you run this, Docker will download the necessary images, which may take several minutes.

### 4. Access the Services

-   **n8n:** `http://localhost:5678`
-   **AI API (CPU):** `http://localhost:8080`

---

## 🧠 n8n Integration

Use an **`HTTP Request`** node in n8n to communicate with your local AI server. The API is OpenAI compatible, which makes it incredibly easy to use.

-   **URL:** `http://ia-server-cpu:8080/v1/chat/completions` *(Inside the Docker network, you can use the service name)*
-   **Headers:**
    -   `Content-Type`: `application/json`
-   **Body (JSON):**
    ```json
    {
      "messages": [{"role": "user", "content": "Write a poem about Docker."}]
    }
    ```

---

## 🎮 (Advanced) Upgrading to GPU

If you have a powerful NVIDIA GPU and want maximum performance, this is the recommended upgrade path.

1.  **Stop the current stack:** `docker-compose down`
2.  **Edit `docker-compose.yml`:**
    -   Comment out or delete the `ia-server-cpu` service.
    -   Uncomment the `ia-server-gpu` service.
3.  **Model for GPU:** The TGI server for GPU does not use the GGUF format. For a significant performance and quality upgrade, we recommend a powerful model like **Mixtral**. TGI will download it for you if it's not found in `model-cache`. Ensure the `--model-id` in the `command` of the `ia-server-gpu` service points to your desired model on the Hugging Face Hub (e.g., `mistralai/Mixtral-8x7B-Instruct-v0.1`).
4.  **Relaunch the stack:** `docker-compose up -d`

The API for the TGI server is slightly different. The `Body` of the `HTTP Request` node should be structured like this:

```json
{
  "inputs": "Your prompt here...",
  "parameters": {
    "max_new_tokens": 256
  }
}
```
For more details, please refer to the official [TGI documentation](https://huggingface.co/docs/text-generation-inference/index).

---

## 📜 License

This project is licensed under the MIT License. See the `LICENSE` file for details.

## ✍️ How to Cite

If you find this project useful in your research or work, please consider citing it. You can use the "Cite this repository" widget on the right sidebar of the GitHub repository page.