# 任务一：单臂抓取放置（教程 2.8）

> 目标：遥操从臂把物品（教程用杨桃）抓起放进盒子，采 100 集数据，训 ACT/SmolVLA，推理验证。
> 参数出处：教程 2.8（http://127.0.0.1:7788/pages/2-8-task1-pick-place.html），命令已改写为插件路线。
> ⚠️ 真机命令由 Jeff 亲手跑；GPU 训练走 `tsp` 队列（先读 `~/2026-summer-career-projects/GPU训练队列.md`）。

## 0. 前置

- [ ] 2.4 真机对拍已通过（遥操 6 关节 + 夹爪正常）
- [ ] 相机已装（教程 2.3）：腕部 handeye + 固定位 fixed，`lerobot-find-cameras opencv` 确认两路 30fps
- [ ] 上位机前置五项完成（见 [../docs/registry.md §4](../docs/registry.md)）

## 1. 数据采集（教程 §2.1）

```bash
source ~/2026-summer-career-projects/lerobot_robot_episode1/.venv/bin/activate

lerobot-record \
    --robot.type=episode1_follower --robot.id=episode1_follower \
    --robot.ip_address=localhost --robot.port=12345 \
    --robot.speed_mode=record --robot.use_radian=true \
    --robot.cameras="{handeye: {type: opencv, index_or_path: <腕部相机序号>, width: 320, height: 240, fps: 30, fourcc: MJPG}, fixed: {type: opencv, index_or_path: <固定位相机序号>, width: 320, height: 240, fps: 30, fourcc: MJPG}}" \
    --teleop.type=episode1_leader --teleop.id=episode1_leader \
    --teleop.port=/dev/ttyACM0 --teleop.speed_mode=record --teleop.use_radian=true \
    --dataset.repo_id=jeff/task1_pick_place \
    --dataset.root=datasets/task1_pick_place \
    --dataset.push_to_hub=false \
    --dataset.num_episodes=100 \
    --dataset.episode_time_s=15 \
    --dataset.reset_time_s=3 \
    --dataset.single_task="Put the starfruit to the blue box" \
    --dataset.fps=30 \
    --display_data=true
```

要点（教程经验）：

- `<腕部/固定位相机序号>` 用 `lerobot-find-cameras opencv` 确认（教程里腕部=2、固定位=0，**以实拍为准**）
- 先练几集找节奏，确认 `episode_time_s` 能完整做完任务再正式录
- 物品分多个位置摆放，一个位置约 10 集
- 中断后续录：加 `--resume=true`（⚠️ 在 RecordConfig 顶层，不是 `--dataset.resume`；num_episodes 是**追加**集数）
- ⚠️ 舵机夹爪不能长时间堵转夹物（教程 2.10 警告：会烧毁/卡顿）
- 教程用弧度制录（Pi0 需要），ACT/SmolVLA 训练前转角度（见 §2）

## 2. 弧度 → 角度（教程 §2.3，ACT/SmolVLA 用）

```bash
python ~/2026-summer-career-projects/lerobot_robot_episode1/scripts/convert_radians_to_degrees.py \
    --source-repo-id=jeff/task1_pick_place \
    --source-dataset-root=datasets/task1_pick_place \
    --target-repo-id=jeff/task1_pick_place_degrees \
    --output-path=datasets/task1_pick_place_degrees \
    --max-episodes=100
```

## 3. 数据集检查（教程 §2.3）

官方可视化：`lerobot-dataset-viz --repo-id jeff/task1_pick_place_degrees --root datasets/task1_pick_place_degrees`
（官方 0.6 自带；若子命令名有出入，`lerobot-dataset-viz --help` 查）
检查：集数够、相机画面是视频非静帧、关节值无异常（角度制不应超量程）、无残缺帧。

## 4. 训练（教程 §3；⛔ 走 tsp 队列）

ACT（官方 train，教程参数：batch_size=32、num_workers=8、steps=200000；本机 16GB 先调小 batch）：

```bash
tsp -L task1-act bash -c '
source ~/2026-summer-career-projects/lerobot_robot_episode1/.venv/bin/activate
lerobot-train \
    --dataset.repo_id=jeff/task1_pick_place_degrees \
    --dataset.root='"$PWD"'/datasets/task1_pick_place_degrees \
    --policy.type=act \
    --output_dir='"$PWD"'/outputs/train/task1_act \
    --job_name=task1_act \
    --policy.device=cuda \
    --policy.push_to_hub=false \
    --wandb.enable=false \
    --batch_size=16 \
    --num_workers=4 \
    --steps=200000
'
```

SmolVLA（微调，国内先 `export HF_ENDPOINT=https://hf-mirror.com` 并预下
`lerobot/smolvla_base` 与 `HuggingFaceTB/SmolVLM2-500M-Video-Instruct`）：
`--policy.path=lerobot/smolvla_base`，steps 教程用 20000，其余同上。

Pi0：另开单元（需恩培 openpi fork + 大显存，厂商用 H20；本机 16GB 不够，届时租云）。

## 5. 推理（教程 §4.1）

官方 0.6 用 `lerobot-rollout`（真机跑策略的专用 CLI，替代教程 fork 的 `lerobot.test_policy`）：

```bash
lerobot-rollout \
    --strategy.type=base \
    --policy.path=outputs/train/task1_act/checkpoints/last/pretrained_model \
    --robot.type=episode1_follower --robot.id=episode1_follower \
    --robot.ip_address=localhost --robot.port=12345 \
    --robot.speed_mode=inference --robot.use_radian=false \
    --robot.cameras="{同采集时}" \
    --task="Put the starfruit to the blue box" \
    --duration=120 \
    --display_data=true
```

⚠️ 推理时相机序号/角度制必须与采集一致；第一次跑旁边有人，异常断从臂电。
（rollout 还支持 `--strategy.type=episodic` 分集评估并录数据，详见 `lerobot-rollout --help`。）
