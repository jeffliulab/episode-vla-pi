[![Language: English](https://img.shields.io/badge/Language-English-2f81f7?style=flat-square)](README.md) [![语言: 简体中文](https://img.shields.io/badge/语言-简体中文-e67e22?style=flat-square)](README_zh.md)

# episode-vla-pi

[![lerobot](https://img.shields.io/badge/lerobot-%3E%3D0.6%2C%3C0.7-ff9d00?style=flat-square)](https://github.com/huggingface/lerobot) [![Status: Learning](https://img.shields.io/badge/Status-Learning-blueviolet?style=flat-square)]() [![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-blue?style=flat-square)](LICENSE)

> 🤖 If you are an AI agent, read [AGENTS.md](AGENTS.md) first.

An embodied-AI learning project: walking the full Vision-Language-Action pipeline on a real
Episode1 robot arm — teleoperation, data collection, VLA training (ACT / SmolVLA / Pi0), and
policy deployment. This repo is the task-and-experiment layer: configs, logs, and notes. The
device support lives in [`lerobot_robot_episode1`](https://github.com/jeffliulab/lerobot_robot_episode1),
a plugin for stock LeRobot — no fork anywhere in the stack.

---

## Overview

VLA models don't come with data — you teleoperate the arm to create it. This repo follows
that pipeline task by task (first: pick-and-place), keeping every run's config, dataset
convention, and result in one place. It sits on top of two pieces: the vendor hardware stack
(drivers, vendor control server, ROS 2 sim) and the LeRobot plugin that makes the hardware
speak LeRobot's language.

## Key features

- **One folder per task**: `tasks/<slug>/` holds the hand-written record — a task card
  (`TASK.md`), a **photo of the physical setup** (`setup.jpg`), and a per-task log
  (`runs.md`). Datasets and checkpoints stay out of it. See `tasks/README.md`.
- **No duplication of LeRobot metadata**: fps, feature shapes, episode counts already live
  in the dataset's `meta/`, and hyperparameters ship with the checkpoint. Task cards record
  only what LeRobot cannot: physical setup, success criteria, failures, and the links
  between a task, its recordings, its training runs, and its results.
- **Experiment ledger**: `docs/experiments.md` records every run — outcome, artifacts,
  conclusion.
- **Component registry**: `docs/registry.md` maps the whole Episode stack — repos, ports,
  device nodes, hardware facts, and the vendor-server prerequisites.
- **Progress tracking**: `docs/tutorial-progress.md` pairs each tutorial chapter with the
  exact plugin-route command that replaces the vendor fork's.

## Quick start

Run everything from this repo's root — paths are relative: datasets and outputs land in
this repo, and the environment lives in the sibling `../lerobot_robot_episode1/`.

```bash
source ../lerobot_robot_episode1/.venv/bin/activate
episode1-doctor    # hardware self-check first, always
```

Then follow `docs/tutorial-progress.md` chapter by chapter; task commands (e.g.
`tasks/t01-pick-place/TASK.md` §6) are copy-paste ready once the placeholders are filled.

Every command that powers or moves real hardware is run by a human operator; GPU training
goes through the machine's `tsp` queue. See `AGENTS.md` for the full red lines.

## The line's repos

| Repo | Role |
|---|---|
| episode-vla-pi (this one) | Tasks, configs, experiment records |
| [`lerobot_robot_episode1`](https://github.com/jeffliulab/lerobot_robot_episode1) | LeRobot device plugin for the arms |
| [`open-chess-robot`](https://github.com/jeffliulab/open-chess-robot) | Sister line: chess body in Gazebo |
| [`anima-zero`](https://github.com/jeffliulab/anima-zero) | Sister line: the brain framework |

## License

Apache-2.0 © 2026 Jeff Liu
