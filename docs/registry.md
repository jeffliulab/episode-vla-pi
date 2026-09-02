# Episode 全家登记总表（registry）

> 本页是 episode 具身智能线所有组件的**单一登记处**：每个组件是什么、在哪、什么状态、真相源是谁。
> 信息变了就改这里，别处只引用不复制。最后核对：2026-07-31。

## 1. 硬件 infra：`$EPISODE_HOME`（Episode1 开发套件仓）

Episode1 机械臂的完整软件栈，2026-07-07 起外置为独立的机器人 infra 仓（私有）。
⛔ 路径一律走 `$EPISODE_HOME`（在 `~/.bashrc` 里定义，是这套件路径的唯一真值），别写死绝对路径。

| 子目录 | 角色 | 状态 / 关键事实 |
|---|---|---|
| `episode-driver/` | 从臂 CAN 驱动地基：PEAK 驱动源码+重编签名、厂商 Python SDK、标定工具、ROS 控制器源码 | ✅ 全链路已验证。CAN 设备 `/dev/pcanusb32`（PCAN-USB `0c72:000c`）；内核已 apt-mark hold |
| `episode-server/` | 厂商上位机 `gui_server_uni`（V0.9.9.3）：运动学核心 + **TCP 服务器 `localhost:12345`** | ✅ lerobot follower 的唯一控臂通道。回零姿态在 `homing_config.json` |
| `episode-ros-ws/` | ROS2 jazzy colcon 工作区（MoveIt + Gazebo + 网页 teleop :8110），=`$EPISODE_WS` | ✅ 8 包已过。与 lerobot 线是**平行/互斥**关系（同抢 CAN） |
| `$EPISODE1_DOCS/教程/` | 离线教程站（⭐ 2026-09-02 脱子模块、独立仓 `jeffliulab/episode-tutorials`，随整机档案（`Docs/`）走） | ✅ 本地起站：`./serve.sh` → http://127.0.0.1:7788 |
| `episode-leader-arm/` | 主臂舵机工具（编号/读状态/验证映射），2026-07-30 建 | ✅ 编号任务已完成。⚠️ README 有过时残留（temp/ 旧路径、"脚本未编写"矛盾句），未入 git |

⛔ **CAN 互斥规则**：gui_server 与 ROS 控制器同一时刻只能开一个（独占 `/dev/pcanusb32`）。

## 2. 设备插件：`lerobot_robot_episode1/`（工作区，公开仓）

官方 lerobot（PyPI ≥0.6,<0.7，**未修改**）的第三方插件，注册：

- `episode1_follower` — 从臂（Episode1 本体，TCP → `localhost:12345`）
- `episode1_leader` — 主臂（7 个飞特 ST-3215 舵机，CH343 → `/dev/ttyACM0`，1 Mbps）
- CLI：`episode1-doctor` / `episode1-set-middle` / `episode1-default-position`

真相源：仓内 `AGENTS.md`、`docs/ROADMAP.md`（Phase 1–5 规划）。
⛔ **不要再用恩培 fork**（`enpeizhao/lerobot_single_student`）——与官方同名 `lerobot` 发行包，必冲突。

## 3. 硬件事实（主臂遥操臂）

| 事实 | 值 | 出处 |
|---|---|---|
| 舵机 | ST-3215-C046 × 7（型号码 777=STS3215） | 物料清单（教程附录） |
| 舵机 ID = 关节 | J1 底座=1 → J7 夹爪=7（已编号+串联复扫+关节映射抽验） | episode-leader-arm/README.md |
| 驱动板 | CH343 串口芯片（`1a86:55d3`）→ `/dev/ttyACM0`，内核自带驱动 | 2026-07-30 实测 |
| 波特率 | 1,000,000（1 Mbps） | 同上 |
| 电源 | 12V10A；实测 12.4V（厂商确认"就是 12V 电机"；`过压`标志属正常） | 厂商回复 2026-07-30 |
| 校准文件 | 官方 `~/.cache/huggingface/lerobot/calibration/`，按 robot id 存放 | lerobot 约定 |
| 串口权限 | 用户须在 `dialout` 组（已配置） | 2026-07-30 |

⚠️ 未结案小尾巴：说明书实物只标 6.0V/7.4V，与厂商口径矛盾未解释——舵机异常发热时回查。

## 4. 上位机（从臂）前置要求（教程 2.4 §1.3，全人工）

遥操前必须：① 6 个驱动板 Response 参数改 None；② 上位机 ≥ V0.9.8（本机 V0.9.9.3 ✓）；
③ 归零、回默认位置；④ 关闭「启用日志」「启用状态刷新」；⑤ 保持上位机开着（它就是 12345 服务）。

## 5. 端口 / 设备节点速查

| 端口/节点 | 用途 | 谁在用 |
|---|---|---|
| `localhost:12345` | 从臂 TCP API | gui_server_uni（lerobot follower 连它） |
| `127.0.0.1:7788` | 教程站 | `$EPISODE1_DOCS/教程/serve.sh` |
| `/dev/ttyACM0` | 主臂舵机总线 | episode1_leader |
| `/dev/pcanusb32` | 从臂 CAN | gui_server **或** ROS（互斥） |
| `:8106` | gazebo-chess 世界服务 | open-chess-robot（下棋线） |
| `:8110` | ROS 网页 teleop | episode-ros-ws |
| `:8100` | ANIMA 网页 | anima-zero（下棋线）。2026-08-05 从 `:3000` 迁来 |

## 6. 并行线（不是本仓的事，但常被问起）

- `open-chess-robot/`（工作区，前身为 soma-zero，2026-07-31 改名）：下棋身体线，Gazebo 仿真
- `anima-zero/`：大脑编排（System 2），下棋线
- 教程第 4 章（Soma Zero 下棋）走这两仓；第 2 章（VLA 实战）走本仓

## 7. 教程站章节地图

离线站 http://127.0.0.1:7788（`$EPISODE1_DOCS/教程/serve.sh` 起站）。
逐章进度与本仓命令对照 → [tutorial-progress.md](tutorial-progress.md)。
注意：教程第 2 章原文按「恩培魔改 fork」教学，**命令以本仓 `tasks/<slug>/TASK.md` 为准**（插件路线对照表见 tutorial-progress.md）。
