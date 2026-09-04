# 教程进度与本仓命令对照

> 教程 = 恩培《实战 VLA 具身智能》（离线站 http://127.0.0.1:7788，`$EPISODE1_DOCS/教程/serve.sh` 起站）。
> 教程原文用恩培魔改 fork；**本仓一律走官方 lerobot + 插件包**，对照关系见下。
> 状态更新到：2026-07-31。

> ⭐ 2026-09-04 教程站章节重排：新增第 1 章「机械臂设置」，原第 1~3 章顺延为 2~4 章（原 2.x 现为 3.x，页面文件名同步改为 `3-*`）。
> 本文下面的编号沿用**重排前**的原编号，看站时自行 +1。

## 第 2 章 实战 VLA 具身智能

| 章节 | 内容 | 状态 | 本仓/插件对应 |
|---|---|---|---|
| 2.1 | 课程导引（概念） | ✅ 已读 | — |
| 2.2 | 遥操作主臂安装 | ✅ 2026-07-30 完成 | 编号用的 Linux 工具在 `$EPISODE_HOME/episode-leader-arm/`（替代教程的 Windows 舵机上位机） |
| 2.3 | 遥操作系统相机安装 | ⏳ 未做 | 相机 config 直接带 `fourcc: MJPG`（官方 0.6 原生支持，无需教程时代的 hack） |
| 2.4 | 单臂遥操系统校准、测试 | 🔵 进行中 | 见下表 |
| 2.5 | 双臂校准 | ⏭️ 跳过（只有一台主臂） | — |
| 2.6 / 2.7 | ACT / Pi0 原理 | ✅ 已读 | — |
| 2.8 | 任务一：单臂抓取放置 | ⏳ | [../tasks/t01-pick-place/TASK.md](../tasks/t01-pick-place/TASK.md) |
| 2.9 | 任务二：单臂开关抽屉 | ⏳ | 同 2.8 管线（届时按 `tasks/README.md` 新建 `tasks/t02-drawer/`） |
| 2.10 | 任务三：双臂倒水 | ⏭️ 跳过 | — |

## 2.4 逐步对照（教程命令 → 本仓命令）

| 教程步骤 | 教程原文（恩培 fork） | 本仓（官方 lerobot + 插件） |
|---|---|---|
| 环境 | conda + clone fork + `pip install -e .` | ✅ 已完成：`lerobot_robot_episode1/.venv`（官方 0.6.0 + 插件） |
| 中位校准 | `python -m lerobot.set_middle --port=/dev/ttyACM0` | `episode1-set-middle`（校准存官方 calibration_dir） |
| 上位机前置 | Response=None、归零、关日志刷新 | 同教程（人工，见 [registry.md §4](registry.md)） |
| 低速遥操 | `python -m lerobot.teleoperate --robot.type=enpei_follower --teleop.type=enpei_leader --enpei_speed_mode=record ...` | `lerobot-teleoperate --robot.type=episode1_follower --robot.speed_mode=record --teleop.type=episode1_leader --teleop.speed_mode=record ...` |
| 找相机 | `python -m lerobot.find_cameras opencv` | `lerobot-find-cameras opencv`（官方自带，未变） |
| 从臂摆安装位 | `python -m lerobot.episode_default_position` | `episode1-default-position` |

## 真机对拍清单（当前进行中的这一步，Jeff 亲手）

1. `episode1-doctor` 全绿（主臂接电插 USB；上位机 TCP 项需先开 gui_server）
2. `episode1-set-middle`（七关节中位 2048，退出时保存零点）
3. 上位机前置五项（registry.md §4）
4. 低速 teleop：6 关节跟随 + 夹爪可控 = 2.4 对拍成功
5. ⚠️ 首抓有人协助，异常断从臂电（手托关节）

## 第 4 章 Soma Zero 下棋（并行线）

走 `anima-zero` + `open-chess-robot`，见 `open-chess-robot/README.md`。
其中 4.6「引入 LeRobot 遥操数据采集」与本仓会合（用同一套插件采下棋数据）。
