# ICAFusion × FLIR 复现记录

采用作者的 YOLOv5l 双路实现，在 FLIR-aligned 上训练 60 轮。ICAFusion 在检测前用双向交叉注意力交换 RGB 与热红外特征的信息。本次保留了官方三尺度融合、混合池化和默认迭代设置。 [论文](https://arxiv.org/abs/2308.07504) · [本次采用的作者代码版本](https://github.com/chanchanchan97/ICAFusion/tree/6c06b831c70f484b7a434d51cd71e2a2586d1998)

## 数据与训练设置

本次使用 4,129 对训练图像和 1,013 对评估图像，检测行人、汽车和自行车。评估集用于逐轮验证和选权重。

<table>
<thead><tr><th>项目</th><th>设置</th></tr></thead>
<tbody><tr><td>训练输入、batch、workers</td><td>640×640，8，8</td></tr></tbody>
<tbody><tr><td>训练轮数</td><td>60 轮</td></tr></tbody>
<tbody><tr><td>优化器</td><td>SGD，初始学习率 0.01，动量 0.937，权重衰减 0.0005</td></tr></tbody>
<tbody><tr><td>调度与增强</td><td>余弦学习率调度，启用 Mosaic 和随机水平翻转</td></tr></tbody>
<tbody><tr><td>验证输入</td><td>原始图像 640×512，经官方验证加载器填充后为 672×544</td></tr></tbody>
</table>

实际运行累计约 4 小时 25 分钟。 [训练超参数](configs/hyp.yaml)

## 复现结果

<table>
<thead><tr><th>结果来源</th><th align="right">mAP50</th></tr></thead>
<tbody><tr><td>论文 FLIR 结果</td><td align="right">79.20%</td></tr></tbody>
<tbody><tr><td>作者仓库修复代码后的更新结果</td><td align="right">82.80%</td></tr></tbody>
<tbody><tr><td>本次第 9 轮</td><td align="right"><strong>82.48%</strong></td></tr></tbody>
<tbody><tr><td>本次第 60 轮</td><td align="right">78.81%</td></tr></tbody>
</table>

本次最佳值与作者仓库的更新结果相差 0.32 个百分点。引用时应标出论文和仓库更新结果各自的来源。标注与作者发布版本、预训练权重是否逐项一致，仍需核对。 [预印本表 10](https://arxiv.org/html/2308.07504v1#S4.T10) · [作者仓库的结果更新](https://github.com/chanchanchan97/ICAFusion#results-update)

![ICAFusion 在 FLIR 上的论文结果、作者更新结果与本次复现结果](figures/map50-comparison.png)

逐轮 CSV 的 `epoch` 从 0 开始，`epoch=8` 对应上面表格的第 9 轮。结束时自动复评的最佳权重 mAP50 约为 82.5%，对应日志的显示精度。 [60 轮原始指标](metrics/results.csv)

![ICAFusion 在 FLIR 上的逐轮验证 mAP50](figures/map50-curve.png)

本次完成了一组融合模型训练。单模态贡献和不同融合设置的影响需要另外训练对照组。
