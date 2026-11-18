# hwinfo-mcp

A local MCP server that exposes HWiNFO telemetry and reports from Windows to MCP-compatible clients, including GitHub Copilot in VS Code.

## What it provides

- Live temperatures, clocks, voltages, fan speeds, utilization, and power from HWiNFO Shared Memory v2.
- Memory timings and hardware report data parsed from HWiNFO reports.
- Six MCP tools: `get_live_sensors`, `get_memory_sensors`, `get_memory_timings`, `get_hardware_report`, `get_raw_report`, and `hwinfo_status`.

The server does not tune or change GPU settings. Live sensor tools read HWiNFO's shared-memory mapping. Report tools may launch HWiNFO to generate a report and may read a report from `HWINFO_REPORT_DIR`.

## Requirements

- Windows with HWiNFO64 installed.
- HWiNFO's **Shared Memory Support** enabled for live sensors.
- Python 3.10+ to install the wheel or run from source. Python is not required when using the standalone executable.

HWiNFO's free edition may stop publishing shared memory after about 12 hours; restarting HWiNFO restores it. Automatic report generation may depend on HWiNFO edition and version. If it does not produce a report, save one with **File → Save Report** and configure `HWINFO_REPORT_DIR`.

## Download and install

Download assets from this fork's [latest release](https://github.com/acabellocano/hwinfo-mcp/releases/latest):

- **Standalone Windows executable:** download `hwinfo-mcp.exe`. No Python installation is needed.
- **Python wheel:** download `hwinfo_mcp-0.1.1-py3-none-any.whl`, then install it:

  ```powershell
  python -m pip install .\hwinfo_mcp-0.1.1-py3-none-any.whl
  ```

- **Install from this repository** (requires Git and Python):

  ```powershell
  python -m pip install "git+https://github.com/acabellocano/hwinfo-mcp.git"
  ```

## Configure Copilot in VS Code

The global Copilot MCP configuration is `%USERPROFILE%\.copilot\mcp-config.json`. In VS Code, you can also open **Chat: Open Customizations → MCP Servers** and add a user-level server.

For the standalone executable, add an entry like this, replacing the example path with the location of the downloaded file:

```json
{
  "mcpServers": {
    "hwinfo-mcp": {
      "type": "stdio",
      "command": "C:\\Tools\\hwinfo-mcp.exe",
      "args": []
    }
  }
}
```

For a Python installation, point `command` to the Python executable in the environment where the package was installed and use `"args": ["-m", "hwinfo_mcp"]`.

Restart or reload the MCP server in VS Code, then call `hwinfo_status` to check the connection. For live GPU telemetry, call `get_live_sensors` with a filter such as `"GPU"`.

## Other MCP clients

For Claude Code, register the installed command:

```powershell
claude mcp add hwinfo-mcp -- hwinfo-mcp
```

For Claude Desktop or another MCP client, use its stdio server configuration with the executable or Python command shown above.

## Configuration

| Environment variable | Purpose | Default |
|---|---|---|
| `HWINFO_EXE` | Full path to `HWiNFO64.exe` | Searched in common install locations and `PATH` |
| `HWINFO_REPORT_DIR` | Directory containing manually saved reports | Unset |
| `HWINFO_REPORT_TIMEOUT` | Seconds to wait for a generated report | `45` |

## Build and release

The `release` GitHub Actions workflow builds a Python source distribution, wheel, and standalone Windows executable when a `v*` tag is pushed. Published files are attached to this repository's [GitHub Releases](https://github.com/acabellocano/hwinfo-mcp/releases).

## Validation and limitations

The live shared-memory reader was validated on Windows against a running HWiNFO mapping, including live RTX 5070 sensor readings. Other hardware configurations and HWiNFO report generation/parsing have not been tested as broadly; report output can vary by HWiNFO version and edition.

This fork is based on [david-morgenstern/hwinfo-mcp](https://github.com/david-morgenstern/hwinfo-mcp). The upstream MIT license and copyright notice are retained in [`LICENSE`](LICENSE).
