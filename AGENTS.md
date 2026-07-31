# AGENTS.md · episode-vla-pi

## 是什么 / 不是什么

Jeff 的具身智能学习仓：Episode1 机械臂的 VLA 实战（遥操、数据采集、ACT/SmolVLA/Pi0 训练与推理）
的**任务配置、实验记录与学习笔记**。
**不是**设备驱动（在 `lerobot_robot_episode1`），**不是**硬件 infra（在 `~/episode-robot-dev-framework/`），
**不是**模型代码——策略训练全部用官方 lerobot 的能力。

## 任务 → 去哪查

| 想干什么 | 去哪 |
|---|---|
| 查 episode 全家（路径/端口/硬件事实/真相源） | `docs/registry.md` |
| 查教程做到哪、每章对应什么命令 | `docs/tutorial-progress.md` |
| 跑任务一的采集/训练 | `configs/task1-pick-place.md` |
| 记/查实验 | `docs/experiments.md` |
| 改设备行为（钳制/校准/协议） | `../lerobot_robot_episode1/`（别在本仓改） |
| 查主臂舵机编号等硬件事实 | `~/episode-robot-dev-framework/episode-leader-arm/README.md` |

## 目录地图

- `docs/` — registry（全家登记）、教程进度、实验记录
- `configs/` — 每个任务一份配置（命令模板 + 参数说明 + 出处）
- `datasets/` — LeRobot 数据集（gitignore，不入库）
- `outputs/` — 训练权重与评估产物（gitignore，不入库）

## 怎么跑起来

```bash
source ~/2026-summer-career-projects/lerobot_robot_episode1/.venv/bin/activate
episode1-doctor        # 硬件自检,全绿才往下
```

采集/训练/推理命令一律从 `configs/` 对应任务文件里取，不在聊天里现编。

## 红线

- ⛔ **真机操作由 Jeff 亲手执行**：凡会连接/上电/驱动真实硬件的命令（串口舵机、上位机 TCP、
  机械臂运动），agent 只准备命令和解释，绝不代跑。
- ⛔ **GPU 训练走 `tsp` 队列**，开跑前读工作区根的 `GPU训练队列.md`；起训授权只认 Jeff 明示。
- ⛔ **禁硬编码**：命令里的值要有出处（教程章节/硬件事实/前次实验）；新值写进 config 文件并注释来源，
  不许只在聊天记录里存在。
- ⛔ CAN 总线互斥：上位机（gui_server）与 ROS 控制器同抢 `/dev/pcanusb32`，同一时刻只能开一个。
- ⛔ 不改官方 lerobot 源码；设备差异只能经 `lerobot_robot_episode1` 的 config 字段进入。
- 数据集/权重不进 git（`datasets/`、`outputs/` 已 gitignore）。

## 提交约定

- 不写 `Co-Authored-By`；禁 `git add -A`；push 由作者发话。

## 备注

若本地同目录存在 `CLAUDE.md`，请一并阅读（内部开发笔记，未入库）。
