# datasets/ · LeRobot 数据集存放约定

- 本目录是采集数据的默认落点，**全部 gitignore，不入库**。
- **命名严格跟任务 slug 走**（见 [`../tasks/README.md`](../tasks/README.md) 约定一）：
  - 弧度制原始集：`datasets/<slug>/` ← `--dataset.root`
  - 角度制转换集：`datasets/<slug>-degrees/`（ACT / SmolVLA 训练用）
  - 两者并列存放，别互相覆盖。
- ⭐ **数据集是自描述的**：fps、robot_type、feature 形状、集数都在它自己的
  `meta/info.json` 里，任务的自然语言描述在 `meta/tasks.parquet` 里。
  ⛔ 别把这些再抄进任务卡——抄了就有两份真相源。
- 删除标准：对应任务结题且权重已产出后，原始集可删（转换集保留到报告/教程写完）。
