# python-automation

`python-automation` is a workspace for small Python packages around automation,
PLC-style control, stepper motors, CNC motion, motion profiles, and robotics.

The repository is organized as a collection of package submodules. Each package
can be installed and developed on its own, while the packages also build on each
other for larger automation experiments and demos.

## Packages

| Package | Purpose |
| :-- | :-- |
| `automation-motion` | Shared one-dimensional motion profiles and plotting utilities. |
| `pyberryplc` | SoftPLC framework for Raspberry Pi and software-backed PLC testing. |
| `pyberryplc-stepper` | Stepper motor drivers, rotators, TMC2208 UART helpers, and PLC integration tools. |
| `pyberryplc-cnc` | CNC adapters that compile Cartesian paths to stepper pulse-train data. |
| `python-robot` | Robotics package for frames, transformations, manipulators, motion planning, and visualization. |

## Repository Layout

```text
python-automation/
  packages/
    automation-motion/
    pyberryplc/
    pyberryplc-stepper/
    pyberryplc-cnc/
    python-robot/
  docs/
  README.md
  LICENSE.md
```

Each package has its own `pyproject.toml`, `README.md`, source tree, and, where
available, docs, demos, or tests.

## Cloning

This workspace tracks the packages as Git submodules.

Clone everything in one step:

```bash
git clone --recurse-submodules <repository-url>
cd python-automation
```

Or, after cloning an already existing checkout:

```bash
git submodule update --init --recursive
```

## Installation

The packages target Python `>=3.10,<3.13`.

For local development, install packages in editable mode from the repository
root. A practical full-workspace order is:

```bash
python -m pip install -e packages/automation-motion
python -m pip install -e packages/pyberryplc
python -m pip install -e packages/pyberryplc-stepper
python -m pip install -e packages/python-robot
python -m pip install -e packages/pyberryplc-cnc
```

Install only the packages you need. For example, PLC-only work can start with
`pyberryplc`, while robotics work usually starts with `automation-motion` and
`python-robot`.

For package-specific development dependencies:

```bash
python -m pip install -e "packages/<package-name>[dev]"
```

## Hardware Notes

Several packages can run in pure software for development and testing.

Physical Raspberry Pi GPIO, stepper motor, or CNC experiments require suitable
hardware, correct wiring, and the required Linux services or device access. In
particular, Raspberry Pi GPIO workflows may require the `pigpio` daemon:

```bash
sudo apt install python3-gpiozero pigpio
sudo systemctl enable --now pigpiod
```

See the package-level READMEs and user guides before connecting hardware.

## Where To Start

- Start with `automation-motion` for reusable
  one-dimensional motion profiles.
- Start with `pyberryplc` for PLC-style control logic,
  software-backed I/O, and browser-based test UIs.
- Add `pyberryplc-stepper` when a PLC
  application needs stepper motor motion.
- Add `pyberryplc-cnc` for CNC-style XYZ motion
  compiled to stepper trajectories.
- Start with `python-robot` for robot kinematics,
  trajectory planning, and visualization.

## Documentation

Package documentation is kept close to each package. In a local checkout, start
with:

- `packages/pyberryplc/docs/user_guide`
- `packages/pyberryplc-stepper/docs/user_guide`
- `packages/pyberryplc-cnc/docs`
- `packages/python-robot/demos`

The demos are intentionally small and practical. They are useful starting
points for learning how the packages fit together.

## License

The workspace and packages use the MIT license. See `LICENSE.md` and
package-level license files for details.
