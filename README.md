<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" alt="Cua logo" height="150" srcset="img/logo_white.png">
    <source media="(prefers-color-scheme: light)" alt="Cua logo" height="150" srcset="img/logo_black.png">
    <img alt="Cua logo" height="150" src="img/logo_black.png">
  </picture>

  <!-- <h1>Cua</h1> -->

  [![Python](https://img.shields.io/badge/Python-333333?logo=python&logoColor=white&labelColor=333333)](#)
  [![Swift](https://img.shields.io/badge/Swift-F05138?logo=swift&logoColor=white)](#)
  [![macOS](https://img.shields.io/badge/macOS-000000?logo=apple&logoColor=F0F0F0)](#)
  [![Discord](https://img.shields.io/badge/Discord-%235865F2.svg?&logo=discord&logoColor=white)](https://discord.com/invite/mVnXXpdE85)
</div>

Cua (pronounced "koo-ah", short for Computer-Use Agent) is an open-source framework that combines high-performance virtualization with AI agent capabilities to enable secure, isolated environments for AI systems to interact with desktop applications.

## What is Cua?

Cua offers two primary capabilities in a single integrated framework:

1. **High-Performance Virtualization** - Create and run macOS/Linux virtual machines on Apple Silicon with near-native performance (up to 90% of native speed) using `Apple's Virtualization.Framework`.

2. **Computer-Use Interface & Agent** - A framework that allows AI systems to observe and control these virtual environments - interacting with applications, browsing the web, writing code, and performing complex workflows.

## Why Use Cua?

- **Security & Isolation**: Run AI agents in fully isolated virtual environments instead of giving them access to your main system
- **Performance**: [Near-native performance](https://browser.geekbench.com/v6/cpu/compare/11283746?baseline=11102709) on Apple Silicon
- **Flexibility**: Run macOS or Linux environments with the same framework
- **Reproducibility**: Create consistent, deterministic environments for AI agent workflows
- **LLM Integration**: Built-in support for connecting to various LLM providers

## System Requirements

- Mac with Apple Silicon (M1/M2/M3/M4 series)
- macOS 14 (Sonoma) or newer
- Python 3.10+ (for Computer and Agent libraries)
- Disk space for VM images (40GB+ recommended)

## Quick Start

### Option 1: Lume CLI Only (VM Management)
If you only need the virtualization capabilities:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/trycua/cua/main/libs/lume/scripts/install.sh)"
```

For Lume usage instructions, refer to the [Lume documentation](./libs/lume/README.md).

### Option 2: Full Computer-Use Agent Capabilities
If you want to use AI agents with virtualized environments:

1. Install the Lume CLI:
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/trycua/cua/main/libs/lume/scripts/install.sh)"
   ```

2. Install the Python libraries:
   ```bash
   pip install cua-computer cua-agent[all]
   ```

3. Use the libraries in your Python code:
   ```python
   from cua.computer import Computer
   from cua.agent import ComputerAgent, LLM, AgentLoop, LLMProvider

   async with Computer(verbosity=logging.DEBUG) as macos_computer:
     agent = ComputerAgent(
         computer=macos_computer,
         loop=AgentLoop.OPENAI, # or AgentLoop.ANTHROPIC, or AgentLoop.OMNI
         model=LLM(provider=LLMProvider.OPENAI) # or LLM(provider=LLMProvider.ANTHROPIC)
     )

     tasks = [
         "Look for a repository named trycua/cua on GitHub.",
     ]

     for task in tasks:
       async for result in agent.run(task):
         print(result)
   ```
   
   Explore the [Agent Notebook](./notebooks/) for a ready-to-run example.

4. Optionally, you can use the Agent with a Gradio UI:

   ```python
   from utils import load_dotenv_files
   load_dotenv_files()
    
   from agent.ui.gradio.app import create_gradio_ui
   
   app = create_gradio_ui()
   app.launch(share=False)
   ```

5. For Developers only (contribute and use latest features):
   ```bash
   # Clone the repository
   git clone https://github.com/trycua/cua.git
   cd cua
   
   # Open the project in VSCode
   code ./vscode/py.code-workspace

   # Build the project
   ./scripts/build.sh
   ```
   
   See our [Developer-Guide](./docs/Developer-Guide.md) for more information.

## Monorepo Libraries

| Library | Description | Installation | Version |
|---------|-------------|--------------|---------|
| [**Lume**](./libs/lume/README.md) | CLI for running macOS/Linux VMs with near-native performance using Apple's `Virtualization.Framework`. | [![Download](https://img.shields.io/badge/Download-333333?style=for-the-badge&logo=github&logoColor=white)](https://github.com/trycua/cua/releases/latest/download/lume.pkg.tar.gz) | [![GitHub release](https://img.shields.io/github/v/release/trycua/cua?color=333333)](https://github.com/trycua/cua/releases) |
| [**Computer**](./libs/computer/README.md) | Computer-Use Interface (CUI) framework for interacting with macOS/Linux sandboxes | `pip install cua-computer` | [![PyPI](https://img.shields.io/pypi/v/cua-computer?color=333333)](https://pypi.org/project/cua-computer/) |
| [**Agent**](./libs/agent/README.md) | Computer-Use Agent (CUA) framework for running agentic workflows in macOS/Linux dedicated sandboxes | `pip install cua-agent` | [![PyPI](https://img.shields.io/pypi/v/cua-agent?color=333333)](https://pypi.org/project/cua-agent/) |

## Docs

For the best onboarding experience with the packages in this monorepo, we recommend starting with the [Computer](./libs/computer/README.md) documentation to cover the core functionality of the Computer sandbox, then exploring the [Agent](./libs/agent/README.md) documentation to understand Cua's AI agent capabilities, and finally working through the Notebook examples.

- [Lume](./libs/lume/README.md)
- [Computer](./libs/computer/README.md)
- [Agent](./libs/agent/README.md)
- [Notebooks](./notebooks/)

## Demos

Demos of the Computer-Use Agent in action. Share your most impressive demos in Cua's [Discord community](https://discord.com/invite/mVnXXpdE85)!

<details open>
<summary><b>AI-Gradio: multi-app workflow requiring browser, VS Code and terminal access</b></summary>
<br>
<div align="center">
    <video src="https://github.com/user-attachments/assets/723a115d-1a07-4c8e-b517-88fbdf53ed0f" width="800" controls></video>
</div>

</details>

<details open>
<summary><b>Notebook: Fix GitHub issue in Cursor</b></summary>
<br>
<div align="center">
    <video src="https://github.com/user-attachments/assets/f67f0107-a1e1-46dc-aa9f-0146eb077077" width="800" controls></video>
</div>

</details>

## Accessory Libraries

| Library | Description | Installation | Version |
|---------|-------------|--------------|---------|
| [**Core**](./libs/core/README.md) | Core functionality and utilities used by other Cua packages | `pip install cua-core` | [![PyPI](https://img.shields.io/pypi/v/cua-core?color=333333)](https://pypi.org/project/cua-core/) |
| [**PyLume**](./libs/pylume/README.md) | Python bindings for Lume | `pip install pylume` | [![PyPI](https://img.shields.io/pypi/v/pylume?color=333333)](https://pypi.org/project/pylume/) |
| [**Computer Server**](./libs/computer-server/README.md) | Server component for the Computer-Use Interface (CUI) framework | `pip install cua-computer-server` | [![PyPI](https://img.shields.io/pypi/v/cua-computer-server?color=333333)](https://pypi.org/project/cua-computer-server/) |
| [**SOM**](./libs/som/README.md) | Self-of-Mark library for Agent | `pip install cua-som` | [![PyPI](https://img.shields.io/pypi/v/cua-som?color=333333)](https://pypi.org/project/cua-som/) |

## Contributing

We welcome and greatly appreciate contributions to Cua! Whether you're improving documentation, adding new features, fixing bugs, or adding new VM images, your efforts help make lume better for everyone. For detailed instructions on how to contribute, please refer to our [Contributing Guidelines](CONTRIBUTING.md).

Join our [Discord community](https://discord.com/invite/mVnXXpdE85) to discuss ideas or get assistance.

## License

Cua is open-sourced under the MIT License - see the [LICENSE](LICENSE) file for details.

## Trademarks

Apple, macOS, and Apple Silicon are trademarks of Apple Inc. Ubuntu and Canonical are registered trademarks of Canonical Ltd. This project is not affiliated with, endorsed by, or sponsored by Apple Inc. or Canonical Ltd.

## Stargazers over time

[![Stargazers over time](https://starchart.cc/trycua/cua.svg?variant=adaptive)](https://starchart.cc/trycua/cua)

## Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tbody>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/f-trycua"><img src="https://avatars.githubusercontent.com/u/195596869?v=4?s=100" width="100px;" alt="f-trycua"/><br /><sub><b>f-trycua</b></sub></a><br /><a href="#code-f-trycua" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="http://pepicrft.me"><img src="https://avatars.githubusercontent.com/u/663605?v=4?s=100" width="100px;" alt="Pedro Piñera Buendía"/><br /><sub><b>Pedro Piñera Buendía</b></sub></a><br /><a href="#code-pepicrft" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://iamit.in"><img src="https://avatars.githubusercontent.com/u/5647941?v=4?s=100" width="100px;" alt="Amit Kumar"/><br /><sub><b>Amit Kumar</b></sub></a><br /><a href="#code-aktech" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://productsway.com/"><img src="https://avatars.githubusercontent.com/u/870029?v=4?s=100" width="100px;" alt="Dung Duc Huynh (Kaka)"/><br /><sub><b>Dung Duc Huynh (Kaka)</b></sub></a><br /><a href="#code-jellydn" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="http://zaydkrunz.com"><img src="https://avatars.githubusercontent.com/u/70227235?v=4?s=100" width="100px;" alt="Zayd Krunz"/><br /><sub><b>Zayd Krunz</b></sub></a><br /><a href="#code-ShrootBuck" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/PrashantRaj18198"><img src="https://avatars.githubusercontent.com/u/23168997?v=4?s=100" width="100px;" alt="Prashant Raj"/><br /><sub><b>Prashant Raj</b></sub></a><br /><a href="#code-PrashantRaj18198" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://www.mobile.dev"><img src="https://avatars.githubusercontent.com/u/847683?v=4?s=100" width="100px;" alt="Leland Takamine"/><br /><sub><b>Leland Takamine</b></sub></a><br /><a href="#code-Leland-Takamine" title="Code">💻</a></td>
    </tr>
    <tr>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/ddupont808"><img src="https://avatars.githubusercontent.com/u/3820588?v=4?s=100" width="100px;" alt="ddupont"/><br /><sub><b>ddupont</b></sub></a><br /><a href="#code-ddupont808" title="Code">💻</a></td>
      <td align="center" valign="top" width="14.28%"><a href="https://github.com/Lizzard1123"><img src="https://avatars.githubusercontent.com/u/46036335?v=4?s=100" width="100px;" alt="Ethan Gutierrez"/><br /><sub><b>Ethan Gutierrez</b></sub></a><br /><a href="#code-Lizzard1123" title="Code">💻</a></td>
    </tr>
  </tbody>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->
