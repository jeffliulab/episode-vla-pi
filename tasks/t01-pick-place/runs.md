# t01-pick-place · 流水账

> 每次采集 / 训练 / 评测一条，**含失败的**。
> ⛔ 不抄超参（在 checkpoint 里）、不抄集数 fps（在 `meta/info.json` 里）——
> 这里只写「发生了什么、为什么、结论是什么」。
> 有结论价值的往 [`../../docs/experiments.md`](../../docs/experiments.md) 汇一条。

## 条目模板

```
## YYYY-MM-DD · <采集|训练|评测>
- 做了什么:
- 产物:       datasets/... 或 outputs/train/...
- 结果:       <观察到的现象或指标>
- 废弃了吗:   <有没有删掉哪些集，为什么删>
- 结论/下一步:
```

---

（暂无条目。第一条预期：装完相机后确认两路相机序号 + 试录几集找 `episode_time_s` 的节奏。）
