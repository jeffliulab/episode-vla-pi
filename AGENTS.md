# AGENTS.md · episode-vla-pi

## 是什么 / 不是什么

Jeff 的具身智能学习仓：Episode1 机械臂的 VLA 实战（遥操、数据采集、ACT/SmolVLA/Pi0 训练与推理）
的**任务配置、实验记录与学习笔记**。
**不是**设备驱动（在 `lerobot_robot_episode1`），**不是**硬件 infra（在 `$EPISODE_HOME`，即 Episode1 开发套件仓），
**不是**模型代码——策略训练全部用官方 lerobot 的能力。

## 任务 → 去哪查

| 想干什么 | 去哪 |
|---|---|
| 查 episode 全家（路径/端口/硬件事实/真相源） | `docs/registry.md` |
| 查教程做到哪、每章对应什么命令 | `docs/tutorial-progress.md` |
| **新建一个任务 / 任务怎么记** | **`tasks/README.md`（约定正本）** |
| 跑任务一的采集/训练 | `tasks/t01-pick-place/TASK.md` |
| 记/查实验 | `docs/experiments.md`（跨任务总台账）+ 各任务自己的 `runs.md` |
| 改设备行为（钳制/校准/协议） | `../lerobot_robot_episode1/`（别在本仓改） |
| 查主臂舵机编号等硬件事实 | `$EPISODE_HOME/episode-leader-arm/README.md` |

## 目录地图

**核心切分：`tasks/` 装人写的（小文本，入库），`datasets/` `outputs/` 装机器产的（大，不入库）。**

- `tasks/` — **一个操作任务一个目录**，约定见 `tasks/README.md`。每个任务三个文件：
  `TASK.md`（任务卡）、`setup.jpg`（⭐ 机位与摆放实拍照）、`runs.md`（本任务流水账）
- `docs/` — registry（全家登记）、教程进度、跨任务实验总台账
- `datasets/` — LeRobot 数据集（gitignore，不入库）
- `outputs/` — 训练权重与评估产物（gitignore，不入库）

⛔ **任务卡里不抄 LeRobot 已经记下的东西**（fps / feature 形状 / 集数在数据集的
`meta/info.json` 里，超参随 checkpoint 存盘）。任务卡只写 LeRobot 记不下来的四样：
**物理布置、成功判据、失败记录、串联关系**。详见 `tasks/README.md`。

## 怎么跑起来

⚠️ **所有命令都从本仓根目录执行**——路径都是相对的（数据集/产物写进本仓的 `datasets/`、
`outputs/`，环境在同级的 `../lerobot_robot_episode1/`）。换目录跑会找不到东西。

```bash
source ../lerobot_robot_episode1/.venv/bin/activate
episode1-doctor        # 硬件自检,全绿才往下
```

采集/训练/推理命令一律从 `tasks/<slug>/TASK.md` 的第 6 节里取，不在聊天里现编。

## 红线

- ⛔ **真机操作由 Jeff 亲手执行**：凡会连接/上电/驱动真实硬件的命令（串口舵机、上位机 TCP、
  机械臂运动），agent 只准备命令和解释，绝不代跑。
- ⛔ **GPU 训练走 `tsp` 队列**，开跑前读工作区根的 `GPU训练队列.md`；起训授权只认 Jeff 明示。
- ⛔ **禁硬编码**：命令里的值要有出处（教程章节/硬件事实/前次实验）；新值写进任务卡并注明来源，
  不许只在聊天记录里存在。
- ⛔ **一个 slug 贯穿四处**（任务目录 / dataset root / repo_id / 训练 job_name），别在某处图省事换写法——
  将来排查「这个权重是哪批数据训的」全靠它。格式见 `tasks/README.md`。
- ⛔ **别过早写脚本**：判据是「同一段命令手抄第三遍」。采完 2 个任务再考虑封装，
  4–5 个任务后才谈得上框架。现在没跑过一次，此刻设计的抽象必然是错的。
- ⛔ CAN 总线互斥：上位机（gui_server）与 ROS 控制器同抢 `/dev/pcanusb32`，同一时刻只能开一个。
- ⛔ 不改官方 lerobot 源码；设备差异只能经 `lerobot_robot_episode1` 的 config 字段进入。
- 数据集/权重不进 git（`datasets/`、`outputs/` 已 gitignore）。

## 提交约定

- 不写 `Co-Authored-By`；禁 `git add -A`；push 由作者发话。

## 备注

若本地同目录存在 `CLAUDE.md`，请一并阅读（内部开发笔记，未入库）。
