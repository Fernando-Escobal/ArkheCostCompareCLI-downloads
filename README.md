# Arkhe Cost Compare CLI

This is a local CLI that compares team and constellation combinations using a local gcsim executable.

This README is for users who receive the packaged file:

- arkhe-cost-compare-cli-0.1.1.tgz

## What you need

- Windows, Linux, or macOS
- Node.js 18+
- Local gcsim executable

## Install from .tgz

Run from the folder where the package exists:

```powershell
npm install -g .\arkhe-cost-compare-cli-0.1.1.tgz
```

```bash
npm install -g ./arkhe-cost-compare-cli-0.1.1.tgz
```

Verify installation:

```powershell
arkhe-cc help
```

## Quick start

1. Create a working folder for your runs.
2. Add your scenario file (for example: example.scenario.txt).
3. Ensure gcsim is reachable.

By default, if simulation.gcsimPath is empty, the CLI tries:

- Windows: ../gcsim/gcsim.exe, ../gcsim/gcsim_windows_amd64.exe
- Linux: ../gcsim/gcsim, ../gcsim/gcsim_linux_amd64
- macOS: ../gcsim/gcsim, ../gcsim/gcsim_darwin_arm64, ../gcsim/gcsim_darwin_amd64

You can also pass it explicitly with --gcsimPath.

## Basic commands

Validate your environment:

```powershell
arkhe-cc doctor
```

Validate and preview scenario workload:

```powershell
arkhe-cc dry-run --scenario .\example.scenario.txt
```

Run simulations:

```powershell
arkhe-cc run --scenario .\example.scenario.txt
```

Run a short smoke test:

```powershell
arkhe-cc run --scenario .\example.scenario.txt --maxRuns 2
```

Use a specific gcsim path:

```powershell
arkhe-cc run --scenario .\example.scenario.txt --gcsimPath D:\tools\gcsim\gcsim.exe
```

```bash
arkhe-cc run --scenario ./example.scenario.txt --gcsimPath /opt/gcsim/gcsim
```

## Scenario notes

- Required sections: [meta], [simulation], [rotations], [team], [selection], [output]
- Team must contain slot1..slot4
- slotN.weapon4 is required
- If requireWeight100 is true, rotation weights must sum to 100
- liquidRolls must include at least one integer

## Output

The CLI writes results based on your [output] section, typically:

- CSV summary table
- JSON detailed output
