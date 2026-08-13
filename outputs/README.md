# outputs/ · 训练权重与评估产物存放约定

- **全部 gitignore，不入库。**
- **命名严格跟任务 slug 走**（见 [`../tasks/README.md`](../tasks/README.md) 约定一）：
  - 训练：`outputs/train/<slug>-<策略>-r<N>/` ← `--output_dir`，`--job_name` 用同一串
    （`r<N>` = 这个任务的第几轮训练，例：`t01-pick-place-act-r1`）
  - 评估：`outputs/eval/<slug>-<策略>-r<N>/`
- ⭐ **完整训练超参随 checkpoint 存盘**，⛔ 别再抄进任务卡或台账——
  要查用什么参数训的，去 `outputs/train/<job>/` 里翻。
- checkpoint 很大（Pi0 单个 ~12GB），定期清理只留 last / best。
- 要长期保留的权重，在 [`../docs/experiments.md`](../docs/experiments.md) 对应条目里登记路径。
