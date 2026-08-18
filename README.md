# NYC AP/IB 教育公平性探索分析

基于纽约州教育部（NYSED）2023–2024 学年 AP / IB 课程与评估公开数据，完成数据清洗、分层探索性分析，并用交互可视化回答教育机会如何在不同群体、地区与课程之间分配。
讲解视频：https://www.bilibili.com/video/BV1NTSKB2EnV/?vd_source=b7f6a28b749c1ab4df5026e760caa934

## 研究问题

1. **教育公平性**：不同社会经济背景、族裔、性别、ELL、残障等群体在 AP/IB 参与度与达标率上是否存在显著差异？
2. **地理与资源配置**：N/RC、县、学区、学校层级的课程覆盖与成果如何分布？城市 vs 非城市、高需求 vs 低需求学区差异如何？
3. **成绩结构与抑制**：等级分布、高分率，以及隐私抑制（小样本用 `-` 隐藏）如何影响公平性解读？

数据来源：[NYSED 公开数据门户](https://data.nysed.gov/downloads.php)  
数据集：Advanced Placement and International Baccalaureate Course and Assessment Data 2024（评估表约 11 万行）。

## 主要工作

### 数据治理

- 从 Access `.mdb` 导出后做约束校验（枚举、非空、层级字段）
- 处理隐私抑制值 `-`、重复行、层级口径不一致
- 按聚合层级拆分：全州 / N/RC / 县 / 学区 / 学校
- 派生指标：达标率、高分率、群体 Gap、抑制标记

达标口径：

- AP：`level3 + level4 + level5`
- IB：`level4 + level5 + level6 + level7`
- 仅当 `tested_student_cnt >= 5` 且未被抑制时计算率值

### 探索性分析

- 州级 AP vs IB 参与规模与等级结构
- 24 个人口统计子群的参与率 / 达标率 / Gap
- N/RC、县、学区地理对比与覆盖热力
- 学科与课程热度、资源-结果象限

### 交互可视化

前端按三个研究问题拆页，使用 D3.js 呈现：

| 页面 | 内容 |
| --- | --- |
| `前端/research1.html` | 教育公平性：群体参与、达标、抑制 |
| `前端/问题二/research2.html` | 地理与资源：覆盖树图、县域热力、资源-结果散点 |
| `前端/research3.html` | 成绩结构与影响因素 |

## 技术栈

- **数据处理**：Python、Pandas、NumPy
- **可视化前端**：HTML / CSS / JavaScript、D3.js
- **图表类型**：柱状图、堆叠图、洛伦兹曲线、树图、散点图、分级着色地图

## 项目结构

```text
exploratory-analysis-project/
├── 数据/                      # 原始与中间数据
├── cleaned_data/              # 清洗后的评估表
├── analysis_results/          # EDA 输出
├── src/
│   ├── data_cleaning.py       # 约束校验与清洗
│   ├── fairness_eda.py        # 公平性指标与图表
│   └── question3_eda.py       # 问题三分析
├── scripts/                   # 层级拆分、去重、树图数据生成
├── 前端/                      # 交互可视化
├── 教育公平性探索性分析方案.md
├── 问题三探索性分析方案.md
├── 字段与约束总结.md
└── 项目计划书.md
```

## 快速开始

### 环境

```bash
python -m venv venv
# Windows
venv\Scripts\activate
pip install -r requirements.txt
```

### 数据清洗

```bash
python src/data_cleaning.py
```

输出位于 `cleaned_data/`，含清洗后 CSV 与违规报告。

### 公平性 EDA

```bash
python src/fairness_eda.py
```

### 查看前端

```bash
cd 前端
python -m http.server 8080
```

浏览器打开：

- `http://localhost:8080/research1.html`
- `http://localhost:8080/research2.html`（若从 `问题二/` 打开，注意相对路径）
- `http://localhost:8080/research3.html`

## 关键口径说明

- 数据为学校/学区等聚合统计，不是学生个体记录
- 小样本会被 NYSED 抑制，公平性讨论必须同时报告抑制比例
- 五个 `aggregation_index` 不能直接混算，分析前先按层级拆分

更完整的字段约束、脏数据问题与分析方案见仓库根目录对应 Markdown 文档。
