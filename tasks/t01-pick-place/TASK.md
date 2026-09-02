# t01-pick-place · 单臂抓取放置

> 对应教程 2.8（离线站 `$EPISODE1_DOCS/教程/serve.sh` → http://127.0.0.1:7788/pages/2-8-task1-pick-place.html）。
> 命令已从教程的恩培 fork 改写成「官方 lerobot + 插件包」路线。
> ⚠️ **真机命令由 Jeff 亲手跑**；GPU 训练走本机 `tsp` 单卡队列。

## 1. 目标

遥操从臂把物品抓起、放进盒子；采一批数据训 ACT/SmolVLA，再用策略自主完成同一动作。

## 2. 物理布置 ⏳ 待填（装完相机、摆好场地后补，连同 `setup.jpg`）

| 项 | 值 |
|---|---|
| 腕部相机 `handeye` 的 `index_or_path` | ⏳ 待测（教程是 2，⛔ 以 `lerobot-find-cameras opencv` 实测为准） |
| 固定位相机 `fixed` 的 `index_or_path` | ⏳ 待测（教程是 0，同上） |
| 固定位机位（高度 / 角度 / 距工作区距离） | ⏳ 待填 |
| 抓取物品 | ⏳ 待定（教程用杨桃；换别的要同步改 `single_task` 那句英文） |
| 放置容器 | ⏳ 待定（教程用蓝盒子） |
| 物体摆放范围 | ⏳ 待填（教程：分多个位置，一个位置约 10 集） |
| 光照 | ⏳ 待填（顶灯 / 台灯 / 有无窗外自然光——阴天晴天会影响，值得记一句） |

⭐ **拍照存 `setup.jpg`**（可多张：`setup-front.jpg` / `setup-top.jpg`）。
补采数据、换机位后复现、写教程配图，全靠它。⛔ 别只写文字描述。

## 3. 成功判据 ⏳ 待定

LeRobot 不管这件事，得你自己定，否则没法评测。建议至少写清：

- 单集**采集**算成功：物品最终完全在容器内 / 中途没掉 / 没撞到桌面。
- 策略**推理**算成功：同上，且在 `duration` 内完成。
- 目标成功率：⏳ 待定（教程没给数字；先采完第一批跑个基线再定）。

## 4. 采集参数（含为什么是这个值）

| 参数 | 值 | 为什么 |
|---|---|---|
| `--dataset.num_episodes` | 100 | 教程给的量。⚠️ 中断后续录加 `--resume=true`（在 RecordConfig 顶层，**不是** `--dataset.resume`），此时 `num_episodes` 是**追加**数 |
| `--dataset.episode_time_s` | 15 | 教程值。⛔ **先练几集找节奏**，确认能完整做完任务再定；做不完就加 |
| `--dataset.reset_time_s` | 3 | 摆回物品的时间 |
| `--dataset.fps` | 30 | 与相机 fps 一致 |
| 相机分辨率 | 320×240 | 教程值。ACT 输入不需要高分辨率，越大越慢 |
| `fourcc` | `MJPG` | ⭐ 官方 0.6 的 `OpenCVCameraConfig` **原生支持这个字段**（已实测），教程时代那个硬写进驱动的 hack 不用移植 |
| `--robot.use_radian` | `true` 采集 | 教程用弧度制录（Pi0 需要）；ACT/SmolVLA 训练前转角度，见 §6.2 |
| `speed_mode` | `record` | ⚠️ 待厂商确认 `record_forpi` 的速度参数，目前等同 `record` |

## 5. 已知坑

- ⚠️ **舵机夹爪不能长时间堵转夹物**（教程 2.10 警告：会烧毁 / 卡顿）。
- ⚠️ **推理时相机序号与角度制必须和采集时完全一致**，否则策略输入分布对不上。
- ⚠️ 第一次跑策略**旁边有人**，异常立刻断从臂电（⛔ 切电瞬间关节失力下塌，需人手扶住）。
- ⛔ CAN 互斥：跑这条线时 gui_server 独占 `/dev/pcanusb32`，**ROS 那条栈必须关掉**。
- ⏳ 采集中发现的新坑往这里追加，别只留在脑子里。

## 6. 命令

### 6.0 前置

- [ ] 2.4 真机对拍已通过（遥操 6 关节 + 夹爪正常）
- [ ] 相机已装（教程 2.3），`lerobot-find-cameras opencv` 确认两路 30fps
- [ ] 上位机前置五项完成（见 [`../../docs/registry.md` §4](../../docs/registry.md)）
- [ ] `episode1-doctor` 全绿

```bash
source ../../../lerobot_robot_episode1/.venv/bin/activate   # 从本任务目录出发
cd ../..                                                    # 命令都在仓根跑
```

### 6.1 采集

```bash
lerobot-record \
    --robot.type=episode1_follower --robot.id=episode1_follower \
    --robot.ip_address=localhost --robot.port=12345 \
    --robot.speed_mode=record --robot.use_radian=true \
    --robot.cameras="{handeye: {type: opencv, index_or_path: <腕部序号>, width: 320, height: 240, fps: 30, fourcc: MJPG}, fixed: {type: opencv, index_or_path: <固定位序号>, width: 320, height: 240, fps: 30, fourcc: MJPG}}" \
    --teleop.type=episode1_leader --teleop.id=episode1_leader \
    --teleop.port=/dev/ttyACM0 --teleop.speed_mode=record --teleop.use_radian=true \
    --dataset.repo_id=jeff/t01-pick-place \
    --dataset.root=datasets/t01-pick-place \
    --dataset.push_to_hub=false \
    --dataset.num_episodes=100 \
    --dataset.episode_time_s=15 \
    --dataset.reset_time_s=3 \
    --dataset.single_task="Put the starfruit to the blue box" \
    --dataset.fps=30 \
    --display_data=true
```

### 6.2 弧度 → 角度（ACT / SmolVLA 用）

```bash
python ../lerobot_robot_episode1/scripts/convert_radians_to_degrees.py \
    --source-repo-id=jeff/t01-pick-place \
    --source-dataset-root=datasets/t01-pick-place \
    --target-repo-id=jeff/t01-pick-place-degrees \
    --output-path=datasets/t01-pick-place-degrees \
    --max-episodes=100
```

### 6.3 数据集检查

```bash
lerobot-dataset-viz --repo-id jeff/t01-pick-place-degrees --root datasets/t01-pick-place-degrees
```

看四件事：集数够、相机画面是视频不是静帧、关节值无异常（角度制不应超量程）、无残缺帧。

### 6.4 训练（⛔ 走 tsp 队列）

```bash
tsp -L t01-act bash -c '
source ../lerobot_robot_episode1/.venv/bin/activate
lerobot-train \
    --dataset.repo_id=jeff/t01-pick-place-degrees \
    --dataset.root='"$PWD"'/datasets/t01-pick-place-degrees \
    --policy.type=act \
    --output_dir='"$PWD"'/outputs/train/t01-pick-place-act-r1 \
    --job_name=t01-pick-place-act-r1 \
    --policy.device=cuda \
    --policy.push_to_hub=false \
    --wandb.enable=false \
    --batch_size=16 \
    --num_workers=4 \
    --steps=200000
'
```

⚠️ 教程给的是 `batch_size=32 / num_workers=8`，本机单卡 16GB **先调小到 16 / 4**，OOM 再降。

**SmolVLA**（微调）：加 `--policy.path=lerobot/smolvla_base`，steps 教程用 20000，其余同上。
国内先 `export HF_ENDPOINT=https://hf-mirror.com` 并预下 `lerobot/smolvla_base`
与 `HuggingFaceTB/SmolVLM2-500M-Video-Instruct`。

**Pi0**：⛔ 本机 16GB 不够（LoRA 微调 >22.5GB、全量 >70GB），届时租云。

### 6.5 推理

```bash
lerobot-rollout \
    --strategy.type=base \
    --policy.path=outputs/train/t01-pick-place-act-r1/checkpoints/last/pretrained_model \
    --robot.type=episode1_follower --robot.id=episode1_follower \
    --robot.ip_address=localhost --robot.port=12345 \
    --robot.speed_mode=inference --robot.use_radian=false \
    --robot.cameras="{与采集时完全一致}" \
    --task="Put the starfruit to the blue box" \
    --duration=120 \
    --display_data=true
```

`--strategy.type=episodic` 可分集评估并录数据，详见 `lerobot-rollout --help`。

---

**流水账写在 [`runs.md`](runs.md)，结论汇到 [`../../docs/experiments.md`](../../docs/experiments.md)。**
