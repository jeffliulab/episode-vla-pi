[![Language: English](https://img.shields.io/badge/Language-English-2f81f7?style=flat-square)](README.md) [![语言: 简体中文](https://img.shields.io/badge/语言-简体中文-e67e22?style=flat-square)](README_zh.md)

# episode-vla-pi

[![lerobot](https://img.shields.io/badge/lerobot-%3E%3D0.6%2C%3C0.7-ff9d00?style=flat-square)](https://github.com/huggingface/lerobot) [![Status: Learning](https://img.shields.io/badge/Status-Learning-blueviolet?style=flat-square)]() [![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-blue?style=flat-square)](LICENSE)

> 🤖 如果你是 AI agent，先读 [AGENTS.md](AGENTS.md)。

一个具身智能学习项目：在真实的 Episode1 机械臂上走通 VLA 全链路——遥操作、数据采集、
VLA 训练（ACT / SmolVLA / Pi0）、策略部署。本仓是任务与实验层：配置、记录、笔记。
设备支持在 [`lerobot_robot_episode1`](https://github.com/jeffliulab/lerobot_robot_episode1)
（原版 LeRobot 的插件）——整条栈里没有任何 fork。

---

## 概览

VLA 模型没有现成数据——要靠遥操作一条一条采出来。本仓按任务推进这条流水线（第一个
任务是抓取放置），把每一轮的配置、数据集约定和结果收在一处。它站在两块基石上：厂商
硬件栈（驱动、上位机、ROS 2 仿真）和让硬件说 LeRobot 语言的插件包。

## 关键特性

- **任务配置进仓，不留聊天记录**：每个任务的采集 / 转换 / 训练 / 推理命令都在
  `configs/`，带参数出处和安全注记。
- **实验台账**：`docs/experiments.md` 记录每一轮——数据量、训练参数、结果、产物。
- **组件登记处**：`docs/registry.md` 是整个 Episode 栈的地图——仓、端口、设备节点、
  硬件事实、上位机前置要求。
- **进度跟踪**：`docs/tutorial-progress.md` 把教程每一章对应到插件路线的确切命令。

## 快速上手

```bash
source ~/2026-summer-career-projects/lerobot_robot_episode1/.venv/bin/activate
episode1-doctor    # 永远先自检
```

然后照 `docs/tutorial-progress.md` 逐章推进；任务命令（如
`configs/task1-pick-place.md`）填好占位符即可直接复制执行。

所有让真实硬件带电/运动的命令由人来执行；GPU 训练走本机 `tsp` 队列。完整红线见
`AGENTS.md`。

## 这条线的仓

| 仓 | 角色 |
|---|---|
| episode-vla-pi（本仓） | 任务、配置、实验记录 |
| [`lerobot_robot_episode1`](https://github.com/jeffliulab/lerobot_robot_episode1) | 双臂的 LeRobot 设备插件 |
| [`open-chess-robot`](https://github.com/jeffliulab/open-chess-robot) | 姊妹线：Gazebo 下棋身体 |
| [`anima-zero`](https://github.com/jeffliulab/anima-zero) | 姊妹线：大脑框架 |

## 许可

Apache-2.0 © 2026 Jeff Liu
