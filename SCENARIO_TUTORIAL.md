# Scenario File Tutorial

A scenario file (`.txt` or `.scenario.txt`) is a plain-text configuration file
that tells **Arkhe Cost Compare CLI** what to simulate and how to compare results.

---

## File Structure

A scenario file is divided into six sections:

```ini
[meta]
[simulation]
[rotations]
[team]
[selection]
[output]
```

Lines that start with `#` or `;` are comments and are ignored.
Every other non-empty line must follow the `key=value` format.

---

## [meta]

General information about this scenario.

| Key         | Required | Description                                 |
| ----------- | -------- | ------------------------------------------- |
| `name`    | No       | A label for the scenario (used in logs).    |
| `version` | No       | Your own version number. Defaults to `1`. |

```ini
[meta]
name=my_team_comparison
version=1
```

---

## [simulation]

Controls how gcsim runs each build.

| Key              | Default    | Description                                                                                                                   |
| ---------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `iterations`   | `100`    | Number of gcsim iterations per build. Higher = more accurate, slower.                                                         |
| `rots`         | `4`      | Number of rotation cycles gcsim simulates.                                                                                    |
| `parallel`     | `4`      | How many gcsim processes run in parallel. Keep at or below your CPU core count.                                               |
| `timeoutMs`    | `120000` | Max milliseconds allowed per gcsim run before it is killed.                                                                   |
| `gcsimPath`    | _(auto)_ | Absolute or relative path to the `gcsim` executable. If empty, the CLI looks for `../gcsim/gcsim.exe` relative to itself. |
| `erIterations` | `350`    | Iterations used when computing Energy Recharge requirements.                                                                  |
| `erPercentile` | `0.8`    | ER percentile target (0–1).`0.8` means "cover 80% of runs".                                                                |
| `enableER`     | `false`  | Set `true` to automatically compute and apply ER thresholds per character.                                                  |
| `substatOptim` | `false`  | Set `true` to run gcsim's substat optimizer before the comparison.                                                          |
| `gcsimVerbose` | `false`  | Set `true` to print full gcsim stdout to the console (useful for debugging).                                                |

```ini
[simulation]
iterations=200
rots=4
parallel=8
timeoutMs=180000
gcsimPath=
erIterations=350
erPercentile=0.8
enableER=false
substatOptim=false
gcsimVerbose=false
```

> **Tip:** Start with `iterations=100` for quick tests, then raise to `500`+
> for final comparisons.

---

## [rotations]

Defines the rotation sequences to compare.
Each rotation has three fields: `label`, `weight`, and `input`.

```ini
rN.label=<name>
rN.weight=<integer 0–100>
rN.input=<rotation string in gcsim action format>
```

- `N` is any number: `r1`, `r2`, `r3`, …
- `weight` values must sum to `100` when `requireWeight100=true` (the default).
- The `input` string is the skill sequence passed to gcsim — use gcsim's action
  notation (`E`, `Q`, `A`, `D`, `>` for ordering, etc.).

```ini
[rotations]
r1.label=rotation_NA_first
r1.weight=50
r1.input=Hu Tao E > Yelan EQ > Zhongli EQ > Xingqiu EQ > Hu Tao AAAAAAA Q

r2.label=rotation_Q_first
r2.weight=50
r2.input=Yelan EQ > Zhongli EQ > Xingqiu EQ > Hu Tao E AAAAAAA Q
```

> You can have as many rotations as you want. The final DPS score for
> each build is the weighted average across all rotations.

---

## [team]

Defines the four team members — one per slot.
Each slot has six fields: `character`, `cons`, `weapon4`, `weapon5`, `set`, and `main`.

```ini
slotN.character=<character id>
slotN.cons=<constellation options>
slotN.weapon4=<4-star weapon id>
slotN.weapon5=<5-star weapon id>       # optional
slotN.set=<artifact set id>
slotN.main=<main stat shorthand>
```

`N` must be `1`, `2`, `3`, or `4` — exactly four slots are required.

### `cons` — Constellation options

Use `|` to specify multiple values to compare across.
The CLI generates a separate build for every combination of cons values.

```ini
slot1.cons=0|2|6       # compares C0, C2, and C6
slot2.cons=6           # always C6, not varied
```

### `weapon4` and `weapon5`

- `weapon4` is **required** — it is the fallback weapon used in all builds.
- `weapon5` is optional — when filled in, the CLI additionally tests builds
  with this 5-star weapon.
- Format: `r<refinement><weapon_id>` — all lowercase, no spaces.

```ini
slot1.weapon4=r5sacrificialfragments
slot1.weapon5=r1athousandfloatingdreams
```

> If you do not want to test a 5-star, leave `weapon5=` empty.

### `set` — Artifact set

The gcsim artifact set identifier, prefixed with the piece count.

```ini
slot1.set=4deepwood
slot2.set=4emblem
slot3.set=2cw2pw         # mixed sets
```

### `main` — Main stat shorthand

A 3-character code describing the main stats of Sands / Goblet / Circlet:

| Position | Meaning           | Common values                                                                            |
| -------- | ----------------- | ---------------------------------------------------------------------------------------- |
| 1st char | Sands main stat   | `e` = EM, `h` = HP%, `a` = ATK%, `d` = DEF%, `r` = ER%                         |
| 2nd char | Goblet main stat  | `e` = EM, `h` = HP%, `a` = ATK%, `d` = DEF%, `p` = Pyro/Hydro/etc. Dmg%        |
| 3rd char | Circlet main stat | `e` = EM, `h` = HP%, `a` = ATK%, `d` = DEF%, `c` = Crit Rate, `r` = Crit DMG |

Examples:

- `edc` → EM Sands / DMG% Goblet / Crit Circlet
- `adc` → ATK% Sands / DMG% Goblet / Crit Circlet
- `hdc` → HP% Sands / DMG% Goblet / Crit Circlet
- `eec` → EM Sands / EM Goblet / Crit Circlet

```ini
slot1.main=edc
slot2.main=adc
slot3.main=eec
slot4.main=hdc
```

### Full team example

```ini
[team]
slot1.character=hutao
slot1.cons=0|1
slot1.weapon4=r5dragonsbane
slot1.weapon5=r1staffofhoma
slot1.set=4shimenawa
slot1.main=hdc

slot2.character=xingqiu
slot2.cons=6
slot2.weapon4=r5favoniussword
slot2.weapon5=
slot2.set=4emblem
slot2.main=adc

slot3.character=zhongli
slot3.cons=0
slot3.weapon4=r5blacktassel
slot3.weapon5=
slot3.set=4tenacity
slot3.main=hdc

slot4.character=yelan
slot4.cons=0|1
slot4.weapon4=r5favoniuswarbow
slot4.weapon5=r1elegyfortheend
slot4.set=4emblem
slot4.main=hdc
```

---

## [selection]

Controls which build combinations are tested.

| Key                  | Default        | Description                                                              |
| -------------------- | -------------- | ------------------------------------------------------------------------ |
| `liquidRolls`      | _(required)_ | Comma-separated list of total liquid roll counts to simulate.            |
| `requireWeight100` | `true`       | If `true`, validates that all rotation weights sum to exactly `100`. |

```ini
[selection]
liquidRolls=20,22,24
requireWeight100=true
```

**`liquidRolls`** represents how many "free" artifact rolls you distribute among
your characters (substat rolls available for optimization). The CLI runs every
combination of: `cons values × weapon choices × liquidRolls values`.

> Use `dry-run` to preview how many total builds will be generated before
> committing to a full `run`.

---

## [output]

Controls where results are saved and how they are sorted.

| Key          | Default               | Description                                                             |
| ------------ | --------------------- | ----------------------------------------------------------------------- |
| `csv`      | `./out/results.csv` | Path for the CSV output file.                                           |
| `json`     | _(empty)_           | Path for the JSON output file. Leave empty to skip.                     |
| `sortBy`   | `weightedDps`       | Column to sort results by. Options:`weightedDps`, `dps`, `label`. |
| `sortDir`  | `desc`              | Sort direction.`desc` = highest first, `asc` = lowest first.        |
| `topN`     | `0`                 | Keep only the top N results.`0` = keep all.                           |
| `keepTemp` | `false`             | If `true`, the temporary gcsim files are not deleted after the run.   |
| `tempDir`  | `./.tmp`            | Directory for intermediate per-build gcsim files.                       |

```ini
[output]
csv=./out/results.csv
json=./out/results.json
sortBy=weightedDps
sortDir=desc
topN=10
keepTemp=false
tempDir=./.tmp
```

---

## How combinations are generated

The total number of builds simulated is:

```
builds = product(consOptions per slot) × product(weaponChoices per slot) × len(liquidRolls)
```

For each slot, `weaponChoices` is `1` (only weapon4) if `weapon5` is empty,
or `2` (weapon4 + weapon5) if a 5-star is provided.

**Example** with the team above:

| Slot    | Cons options | Weapon choices        |
| ------- | ------------ | --------------------- |
| Hu Tao  | 0, 1 → 2    | staff + dragon → 2   |
| Xingqiu | 6 → 1       | only weapon4 → 1     |
| Zhongli | 0 → 1       | only weapon4 → 1     |
| Yelan   | 0, 1 → 2    | elegy + favonius → 2 |

With `liquidRolls=20,22,24` (3 values):

```
2 × 2 × 1 × 1 × 2 × 2 × 3 = 48 builds
```

Run `arkhe-cc dry-run --scenario your_file.txt` to get the exact count before
starting a full simulation.

---

## Complete minimal example

```ini
[meta]
name=quick_test
version=1

[simulation]
iterations=100
rots=4
parallel=4
timeoutMs=120000
gcsimPath=
erIterations=350
erPercentile=0.8
enableER=false
substatOptim=false
gcsimVerbose=false

[rotations]
r1.label=rotation_a
r1.weight=50
r1.input=Nahida EQ > Xingqiu EQ > Yelan EQ > Kuki E

r2.label=rotation_b
r2.weight=50
r2.input=Yelan EQ > Nahida EQ > Xingqiu EQ > Kuki E

[team]
slot1.character=nahida
slot1.cons=0|2
slot1.weapon4=r5sacrificialfragments
slot1.weapon5=r1athousandfloatingdreams
slot1.set=4deepwood
slot1.main=edc

slot2.character=xingqiu
slot2.cons=6
slot2.weapon4=r5favoniussword
slot2.weapon5=
slot2.set=4emblem
slot2.main=adc

slot3.character=kuki
slot3.cons=0|2|4
slot3.weapon4=r5ironsting
slot3.weapon5=r1freedomsworn
slot3.set=4flowerofparadiselost
slot3.main=eec

slot4.character=yelan
slot4.cons=0|1
slot4.weapon4=r5favoniuswarbow
slot4.weapon5=r1elegyfortheend
slot4.set=4emblem
slot4.main=hdc

[selection]
liquidRolls=20,22,24
requireWeight100=true

[output]
csv=./out/results.csv
json=./out/results.json
sortBy=weightedDps
sortDir=desc
topN=0
keepTemp=false
tempDir=./.tmp
```

---

## Quick workflow

```bash
# 1. Preview how many builds will run
arkhe-cc dry-run --scenario my_scenario.txt

# 2. Validate your environment (gcsim found, all paths OK)
arkhe-cc doctor

# 3. Run the full comparison
arkhe-cc run --scenario my_scenario.txt

# 4. Limit to the best 10 results only
arkhe-cc run --scenario my_scenario.txt --maxRuns 10
```

Results are written to the paths defined in `[output]`.
