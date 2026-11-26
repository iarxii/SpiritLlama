# SpiritLlama Desktop — Release & Windows Installer Requirements

This document describes all build-time and end-user runtime requirements for packaging the **SpiritLlama Desktop** release (`Alpha v1.1.110+`) via [`package_release.bat`](../package_release.bat).

---

## 1. Build Machine Prerequisites (To Create Releases & Installers)

| Requirement | Minimum / Recommended | Purpose | Verification Command |
| :--- | :--- | :--- | :--- |
| **Windows OS** | Windows 10 (1809+) or Windows 11 x64 | WPF + WebView2 (`net9.0-windows`) publish target & Inno Setup compilation | `ver` |
| **.NET SDK** | **.NET 9.0 SDK** (`net9.0-windows`) | Compiles [`desktop/SpiritLlama.Desktop.csproj`](../desktop/SpiritLlama.Desktop.csproj), runs C# test suite, and publishes self-contained `win-x64` binaries | `dotnet --list-sdks` |
| **Inno Setup** *(Recommended)* | **Inno Setup 6.x** (`ISCC.exe`) | Compiles [`installer/SpiritLlama.iss`](../installer/SpiritLlama.iss) into `SpiritLlama-Alpha-v<VERSION>-win-x64-setup.exe` | `where iscc` |
| **Node.js** | **Node.js 20+ LTS** | Runs UI harness & syntax verification (`tests/ui_harness_tests.mjs`) inside the pre-release quality gate | `node -v` |
| **Git** | **Git 2.30+** | Reads commit SHA (`git rev-parse --short HEAD`), verifies `README.md` changelog state, and stamps release filenames | `git --version` |
| **PowerShell** | PowerShell 5.1+ (built-in) or PowerShell 7 | Runs [`tests/run_all_tests.ps1`](../tests/run_all_tests.ps1), [`scripts/stage_release.ps1`](../scripts/stage_release.ps1), ZIP creation & SHA-256 checksums | `powershell -Command "$PSVersionTable.PSVersion"` |
| **Vulkan Inference Engine** | Prebuilt [`llama-cpp-turboquant\build\bin\Release`](../llama-cpp-turboquant/build/bin/Release) | Supplies `llama-server.exe`, `llama.dll`, `ggml-vulkan.dll`, and runtime DLLs bundled into `engine\` | `dir llama-cpp-turboquant\build\bin\Release\llama-server.exe` |

### Installing Missing Windows Installer Tooling (Inno Setup 6)

If `ISCC.exe` is not installed yet, `package_release.bat` still creates the portable `.zip` archive cleanly and notifies you to install Inno Setup 6 for the `.exe` setup wizard.

Install Inno Setup 6 quickly via `winget`:
```powershell
winget install --id JRSoftware.InnoSetup -e --source winget
```
Or download the installer from <https://jrsoftware.org/isdl.php>.  
`package_release.bat` automatically checks `PATH`, `%ProgramFiles(x86)%\Inno Setup 6\ISCC.exe`, `%ProgramFiles%\Inno Setup 6\ISCC.exe`, and `%LocalAppData%\Programs\Inno Setup 6\ISCC.exe`.

### Rebuilding the Vulkan Engine Binaries *(Only if `llama-server.exe` is missing)*

If [`llama-cpp-turboquant\build\bin\Release\llama-server.exe`](../llama-cpp-turboquant/build/bin/Release/llama-server.exe) is missing on a fresh clone:
1. Install **Visual Studio Build Tools 2022** (Desktop development with C++ + CMake).
2. Install **LunarG Vulkan SDK** (<https://vulkan.lunarg.com/sdk/home>).
3. Run [`install_rotorquant.ps1`](../install_rotorquant.ps1) (or `cmake -B llama-cpp-turboquant\build -S llama-cpp-turboquant -DGGML_VULKAN=ON && cmake --build llama-cpp-turboquant\build --config Release`).

---

## 2. End-User Machine Requirements (To Run the Installed App)

Because releases publish `--self-contained true` for `win-x64`, users do **not** need the .NET SDK or .NET Desktop Runtime pre-installed.

| Runtime Component | Included? | Notes |
| :--- | :--- | :--- |
| **.NET 9 Desktop Runtime (`win-x64`)** | **Bundled** | Self-contained publish inside release folder / installer |
| **Vulkan `llama-server.exe` + `ggml-vulkan.dll`** | **Bundled** | Placed in `<InstallDir>\engine\` (`test-*.exe` binaries excluded) |
| **Microsoft Visual C++ 2015–2022 Redistributable (x64)** | Required by `llama-server.exe` / Vulkan | Present on most Windows 10/11 PCs; if missing: `winget install Microsoft.VCRedist.2015+.x64` |
| **Microsoft Edge WebView2 Evergreen Runtime** | Checked by Installer | Built into Windows 11 and updated Windows 10 systems |
| **GPU / NPU Drivers *(Optional for Hardware Offload)*** | System Drivers | Intel Arc / Iris Xe Graphics (Vulkan) or Intel Core Ultra NPU driver `32.0.100.3104+` |
| **GGUF Models / Ollama** | User-selected at runtime | Select local `.gguf` models or configure cloud providers (NVIDIA NIM, Gemini, OpenAI, Cloudflare, etc.) in Settings |

---

## 3. Running `package_release.bat`

### Interactive Mode (Mirrors `AI_Codex\vscode-extension\package.bat`)
```bat
package_release.bat
```
- Prompts for version bump (`[1] Patch`, `[2] Minor`, `[3] Major`, `[4] None`, 10s default).
- Verifies `README.md` changelog edits.
- Runs full C# + Node UI test suites via [`tests\run_all_tests.ps1`](../tests/run_all_tests.ps1).
- Publishes `SpiritLlama.Desktop`, stages UI/Skills/Vulkan engine assets, builds Portable `.zip` + Inno Setup `.exe`, writes `SHA256SUMS.txt`, and keeps only the newest 3 builds in `releases\`.

### Unattended / CI Mode
```bat
:: Keep Alpha v1.1.110 without interactive changelog prompt:
package_release.bat none skip

:: Bump patch (1.1.110 -> 1.1.111) and skip changelog prompt:
package_release.bat patch skip
```

---

## 4. Packaged Output Directory Structure

```text
releases\
├── staging\SpiritLlama-Alpha-v1.1.110-win-x64\
│   ├── SpiritLlama.Desktop.exe        (WPF host launcher, self-contained)
│   ├── ui\                            (WebView2 interface & local vendor assets)
│   ├── skills\                        (Mandatory & situational agent SKILL.md files)
│   ├── cli\                           (Python helper probes e.g. gguf_probe.py)
│   ├── engine\                        (llama-server.exe, llama-cli.exe, llama.dll, ggml-vulkan.dll, ...)
│   ├── select_model.ps1
│   ├── run_gpu_diagnostics.bat
│   ├── gpu_diagnostics.ps1
│   ├── LICENSE & README.md
├── SpiritLlama-Alpha-v1.1.110+<TIMESTAMP>-<SHA>-win-x64-portable.zip
├── SpiritLlama-Alpha-v1.1.110+<TIMESTAMP>-<SHA>-win-x64-setup.exe   (when Inno Setup 6 ISCC.exe is installed)
└── SHA256SUMS.txt
```

When installed via Setup into `%LocalAppData%\Programs\SpiritLlama` (per-user) or `C:\Program Files\SpiritLlama` (admin), writable logs, `engine_config.ini`, and WebView2 cache automatically isolate to `%LocalAppData%\SpiritLlama\`.
