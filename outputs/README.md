# outputs/ · 训练权重与评估产物存放约定

- 训练输出：`outputs/train/<任务>_<策略>/`（`--output_dir` 指过来）。
- 推理评估：`outputs/eval/<任务>_<策略>/`。
- **全部 gitignore，不入库**。checkpoint 很大（Pi0 单个 ~12GB），定期清理只留 last/best。
- 要长期保留的权重，登记到 `../docs/experiments.md` 对应条目里。
