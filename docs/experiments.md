# 跨任务实验总台账

> **这里只记「串联」和「结论」**，一条一行级别，别写细节。
> - 细账写在各任务自己的 `tasks/<slug>/runs.md`。
> - ⛔ **不抄超参**（随 checkpoint 存盘）、**不抄 fps / 集数 / feature 形状**
>   （在数据集的 `meta/info.json` 里）——抄了就是两份真相源，必然有一份过期。
> - 记事实，不记感受；**失败也记**。

## 条目模板

```
## YYYY-MM-DD · <任务 slug> · <阶段:采集/训练/评测>
- 任务卡: tasks/<slug>/TASK.md
- 产物:   datasets/<slug>[-degrees]  |  outputs/train/<slug>-<策略>-r<N>
- 结果:   <一句话：指标或现象>
- 结论:   <一句话：下一步依据>
```

---

（暂无条目。第一轮预期：t01-pick-place 采集，见 [`../tasks/t01-pick-place/TASK.md`](../tasks/t01-pick-place/TASK.md)。）
