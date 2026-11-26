<p align="center">
  <img src="assets/SpiritLlama_Logo.svg" alt="SpiritLlama Desktop" width="96" />
</p>

<h1 align="center">SpiritLlama Desktop — Alpha v1.1.110</h1>

<p align="center">
  <strong>Local Vulkan LLM + CodexSpaces Agentic Desktop Harness (Windows x64)</strong><br/>
  Built with Spirit Bird AICodex Design • Self-Contained .NET 9 WPF + WebView2 + RotorQuant/Vulkan Engine
</p>

<p align="center">
  <a href="https://github.com/iarxii/SpiritLlama/releases/latest">
    <img src="https://img.shields.io/badge/Download-Alpha%20v1.1.110-FD3D13?style=for-the-badge" alt="Download Alpha v1.1.110" />
  </a>
</p>

---

## Downloads (Windows 10 / 11 x64)

Binary installers and portable archives are hosted on [**GitHub Releases**](https://github.com/iarxii/SpiritLlama/releases):

| Package | Description | Download |
| :--- | :--- | :--- |
| **Windows Setup Installer (`.exe`)** | Recommended wizard installer (`~59.4 MB`). Installs Desktop & Start Menu shortcuts, checks WebView2 Runtime, and isolates user logs/config under `%LocalAppData%\SpiritLlama`. | [Download Setup `.exe`](https://github.com/iarxii/SpiritLlama/releases/download/v1.1.110-alpha/SpiritLlama-Alpha-v1.1.110%2B20261125-2145-75c50fc-win-x64-setup.exe) |
| **Portable Archive (`.zip`)** | Extract-and-run self-contained folder (`~92.2 MB`) including `portable.mode` so config and logs stay inside the extracted directory. | [Download Portable `.zip`](https://github.com/iarxii/SpiritLlama/releases/download/v1.1.110-alpha/SpiritLlama-Alpha-v1.1.110%2B20261125-2145-75c50fc-win-x64-portable.zip) |
| **Checksums (`SHA256SUMS.txt`)** | SHA-256 verification hashes for all release binaries. | [`SHA256SUMS.txt`](SHA256SUMS.txt) |

---

## What Is Bundled

- **SpiritLlama Desktop (`.NET 9 win-x64` self-contained)** — No separate `.NET` runtime installation required.
- **Vulkan Inference Engine (`engine\llama-server.exe` + `ggml-vulkan.dll`)** — Local GGUF inference accelerated via Vulkan GPU offload (Intel Arc / Iris Xe / discrete GPUs) with CPU fallback.
- **Canvas IDE & Live Preview Browser Automation** — Built-in Monaco editor, split terminals, interactive HTML/React preview with agent DOM control, and screenshot inspection.
- **CodexSpaces & FinTrader Analytics Lab** — Dedicated domain harnesses including live yFinance charts, USD/ZAR conversion, strategy parametrization (`Brothers FX Zones`, `David Perk Blocks`, `Volatility Breakout`, `Mean Reversion`), `Market Signals Analysis Node`, and `Execute | Risk Rules` discipline controls.
- **Multi-Provider Support** — Connect local GGUF / Ollama servers or configure cloud providers (NVIDIA NIM, Gemini, OpenAI, Anthropic, Cloudflare Workers AI, OpenRouter, Groq, DeepSeek).

---

## System Requirements

| Component | Requirement | Notes |
| :--- | :--- | :--- |
| **OS** | Windows 10 (1809+) or Windows 11 (64-bit) | x64 compatible |
| **.NET Runtime** | **None required** | Bundled self-contained inside the release |
| **Microsoft Edge WebView2** | Evergreen Runtime | Pre-installed on Windows 11 & modern Windows 10; checked automatically by Setup |
| **Visual C++ Redistributable** | VC++ 2015–2022 (x64) | Required by `llama-server.exe` (`winget install Microsoft.VCRedist.2015+.x64` if missing) |
| **GPU Acceleration *(Optional)*** | Vulkan-capable GPU drivers | Recommended for Intel Core Ultra / Arc / iGPU offload |

See [`docs/RELEASE_PACKAGING_REQUIREMENTS.md`](docs/RELEASE_PACKAGING_REQUIREMENTS.md) and [`CHANGELOG.md`](CHANGELOG.md) for full details.

---

## Verifying Download Integrity (PowerShell)

```powershell
Get-FileHash .\SpiritLlama-Alpha-v1.1.110+20261125-2145-75c50fc-win-x64-setup.exe -Algorithm SHA256
```
Compare the lowercase hash output against [`SHA256SUMS.txt`](SHA256SUMS.txt).

---

## License

Released under the [MIT License](LICENSE).
