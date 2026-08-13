# tasks/ · 任务记录约定

**一个操作任务一个目录，目录里只放「人写的东西」。**
数据集与训练产物不放这儿——它们大、且已 gitignore，落在 `../datasets/` 与 `../outputs/`。

## ⛔ 第一原则：不抄 LeRobot 已经记下的东西

LeRobot 的数据集是**自描述**的，下面这些采完就自动落盘，**任务卡里一个字都别抄**
（抄了就是两份真相源，必然有一份过期）：

| 已经在数据集里了 | 在哪 |
|---|---|
| fps、robot_type、每个 feature 的名字与形状、总集数/总帧数 | `meta/info.json` |
| 任务的自然语言描述 | `meta/tasks.parquet` |
| 每一集的长度与索引 | `meta/episodes/*.parquet` |
| 归一化统计量 | `meta/stats.json` |
| **完整训练超参** | 随 checkpoint 存盘（`outputs/train/<job>/`） |

**任务卡只写 LeRobot 记不下来的四样**：物理布置、成功判据、失败记录、串联关系。

## 约定一：一个 slug 贯穿四处

任务 slug 格式 `t<两位编号>-<英文短名>`，例如 `t01-pick-place`。

| 用在哪 | 长什么样 |
|---|---|
| 任务目录 | `tasks/t01-pick-place/` |
| 数据集 root | `../datasets/t01-pick-place/`（转换集加后缀 `-degrees`） |
| 数据集 repo_id | `jeff/t01-pick-place` |
| 训练 job_name / output_dir | `t01-pick-place-act-r1`（`r1` = 第几轮训练） |

⭐ **用编号前缀**是因为任务会越堆越多，按字母排会乱；编号让排序 = 时间顺序，
跟 locomotion 那边 `S3-实验1` 的习惯一致。
⛔ 四处必须一致，别在某一处图省事换个写法——将来排查「这个权重是哪批数据训的」全靠它。

## 约定二：一任务一目录，固定三个文件

```
tasks/t01-pick-place/
├── TASK.md      任务卡：目标 / 物理布置 / 成功判据 / 采集参数及理由 / 已知坑 / 命令
├── setup.jpg    ⭐ 机位与物体摆放的实拍照（可多张：setup-front.jpg / setup-top.jpg）
└── runs.md      本任务的流水账：每次采集 / 训练 / 评测一条
```

⭐ **`setup.jpg` 是这套约定里性价比最高的一样东西。** 一张实拍照顶一千字，
拍它零成本，而它解决的是数据集**不可复现的头号原因**——三个月后要补 20 集，
凭什么把相机和物体摆回当初的样子？

## 约定三：台账只记串联和结论

跨任务的总台账在 [`../docs/experiments.md`](../docs/experiments.md)，每条只写：
日期 / 任务 slug / 阶段 / 指向 dataset 与 output 的路径 / 结果一句 / 结论一句。
⛔ **不抄超参**（在 checkpoint 里）。本任务内部的细账写在自己的 `runs.md`。

## ⛔ 什么时候才写脚本

**判据：同一段命令你手抄第三遍的时候。**

- 采完 **2 个**任务再考虑 `scripts/record.sh <slug>`（从 TASK.md 读参数）。⛔ 不是现在。
- 采完 **4–5 个**任务、训过 3 轮以上，才谈得上抽成框架——那时你才知道
  哪些参数真要变、哪些永远不变。

> 参照：`open-source-projects/yanshi-rl-lab` 能长成框架，是因为 locomotion 那边
> 已经跑过几十次训练、共性摸清了。操作这边现在**一次都还没跑过**，
> 此刻设计的抽象必然是错的，还会变成后面想删又不敢删的东西。
> （核心规则 2「选能满足当前需求的最简单实现」、规则 3「先跑通最小的端到端」。）

## 新建一个任务怎么做

1. `mkdir tasks/t0N-<短名>`
2. 抄 `t01-pick-place/TASK.md` 当模板，六节都填上（物理布置那节等硬件摆好了拍照再补）
3. 建空的 `runs.md`
4. 采集时 `--dataset.root` / `--dataset.repo_id` 严格按约定一
