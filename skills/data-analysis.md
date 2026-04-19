# Chat2DuckDB 智能数据分析

> SOLO 适配文档 | 原始项目：data-analysis | 版本 1.0.0

---

## 技能概述

| 项目 | 内容 |
|------|------|
| **技能名称** | Chat2DuckDB 智能数据分析 |
| **原始来源** | manju/data-analysis |
| **核心定位** | 基于 DuckDB 引擎的高效数据分析工具 |
| **适用平台** | SOLO Agent 环境 |

---

## 触发词

当用户提到以下关键词时，应激活此技能：

| 类别 | 触发词 |
|------|--------|
| 数据分析 | 数据分析、数据分析一下、帮我分析数据 |
| 数据查询 | 数据查询、SQL查询、查一下数据 |
| SQL | SQL、写SQL、执行SQL |
| 数据探索 | 数据探索、看看数据、数据长什么样 |
| 数据文件 | CSV、JSON、Parquet、Excel、表格 |
| 数据报告 | 数据报告、分析报告、数据洞察 |

---

## 核心功能

### 1. 数据探索（Describe 模式）

- **基本信息**：总行数、列数、表结构
- **数值列统计**：平均值、中位数、标准差、最大/最小值
- **分类列统计**：唯一值数量、最常见值、Top 值分布
- **日期列统计**：最早/最晚日期、唯一日期数
- **数据质量**：缺失值统计、完整性分析

### 2. SQL 查询执行

- **智能 SQL 生成**：根据自然语言描述自动生成 SQL
- **自动重试机制**：最多 3 次智能重试
- **SQL 校正引擎**：
  - 语法错误自动修复（移除多余分号、逗号等）
  - 列名错误智能纠正（基于编辑距离匹配）
  - 引号规范化（双引号转单引号）
  - SQL 关键字大小写规范化
- **数据抽样**：支持按比例抽样查询，快速验证逻辑

### 3. 结果导出

- 支持 CSV/Excel/JSON/Parquet 格式导出
- 查询结果格式化输出
- 执行时间和性能统计
- 数据洞察和业务建议生成

---

## 支持格式

| 格式 | 扩展名 | 说明 |
|------|--------|------|
| CSV | .csv | 逗号分隔值文件 |
| JSON | .json | JSON 格式文件 |
| Parquet | .parquet | 列式存储格式 |
| Excel | .xlsx / .xls | Excel 表格文件 |

> Excel 文件默认读取第一个工作表，可通过 `excel_sheet` 参数指定其他工作表

---

## 使用方法

### 数据探索

```bash
# 完整统计模式（推荐）
python scripts/duckdb_analyzer.py --file_path ./data.csv --mode describe

# 简单模式（仅基本信息）
python scripts/duckdb_analyzer.py --file_path ./data.csv --mode describe --simple

# 导出分析报告
python scripts/duckdb_analyzer.py --file_path ./data.csv --mode describe --output report.json

# Excel 文件（指定工作表）
python scripts/duckdb_analyzer.py --file_path ./data.xlsx --excel_sheet "sheetTitle" --mode describe
```

### SQL 查询

```bash
# 基础查询
python scripts/duckdb_analyzer.py --file_path ./data.csv --mode query \
  --sql "SELECT * FROM data LIMIT 10"

# 聚合查询
python scripts/duckdb_analyzer.py --file_path ./data.csv --mode query \
  --sql "SELECT category, SUM(price * quantity) as total_sales FROM data GROUP BY category"

# 抽样验证（先在小样本上测试）
python scripts/duckdb_analyzer.py --file_path ./data.csv --mode query \
  --sql "SELECT * FROM data WHERE price > 100" --sample_fraction 0.1

# 导出查询结果
python scripts/duckdb_analyzer.py --file_path ./data.csv --mode query \
  --sql "SELECT * FROM data" --output result.csv

# 持久化到 DuckDB 文件
python scripts/duckdb_analyzer.py --file_path ./data.csv --mode query \
  --persist_db_path ./analysis.duckdb --persist_table \
  --sql "SELECT category, SUM(price * quantity) as total_sales FROM data GROUP BY category"
```

---

## SQL 语法约束

- 仅使用 DuckDB SQL 方言，不使用其他数据库的专有语法
- 字段名支持英文和中文查询
- 包含中文、空格、连字符、冒号等特殊字符的字段名，必须使用双引号
- 当中文字段未加双引号时，查询引擎会自动校正并重试
- 支持中文标点自动转换（如 `，；（）` 转 `,;()`）
- 默认表名为 `data`
- 生成 SQL 时优先保证可执行性，再进行性能优化

### 字段名示例

```sql
-- 特殊字符字段名必须用双引号
SELECT "销售渠道", SUM("售后退款-仅退货金额") AS total_return
FROM data
GROUP BY "销售渠道"
ORDER BY total_return DESC
LIMIT 10

-- 普通中文字段可不用双引号
SELECT 销售渠道, SUM(售后退款-仅退货金额) AS total_return
FROM data
```

---

## Pandas 使用边界

- Pandas 仅用于读取文件与将 DataFrame 注册到 DuckDB
- Pandas 可用于注册前的数据安全预处理（如 `inf/-inf -> NULL`）
- 不使用 Pandas 做业务聚合分析、统计计算或口径产出
- 最终分析结果必须通过 DuckDB SQL 查询 `data` 表生成

---

## 输出格式说明

### Describe 模式输出

- **基本信息**：数据规模概览
- **表结构**：字段名和数据类型
- **数值列统计**：描述性统计指标
- **分类列统计**：分布和频率信息
- **日期列统计**：时间范围信息
- **数据质量**：缺失值统计
- **数据样本**：前 5 行数据预览

### Query 模式输出

- **SQL 校正记录**：如果有自动校正，会显示校正内容
- **查询结果**：执行时间、重试次数、结果行数
- **数据预览**：完整的查询结果表格

---

## 最佳实践

1. **先探索后查询**：先用 describe 模式了解数据结构，再生成 SQL
2. **复杂查询先抽样**：对于复杂查询，先用 `--sample_fraction` 参数在小样本上验证
3. **合理使用 LIMIT**：查询结果超过 1000 行时，建议使用 LIMIT 或聚合查询
4. **利用自动校正**：SQL 错误时会自动重试和校正，无需手动干预
5. **大数据集使用抽样验证后再执行完整查询**
6. **聚合查询比全表查询更高效**

---

## 反模式清单

| 错误做法 | 问题说明 | 正确做法 |
|---------|---------|---------|
| 不探索数据就直接写SQL | 不知道字段名和数据类型，容易出错 | 先用 describe 模式了解数据结构 |
| 大数据集直接全表查询 | 内存不足，执行缓慢 | 先用抽样验证，再执行完整查询 |
| 忽略中文标点 | SQL语法错误 | 引擎会自动转换，但最好手动检查 |
| 用 Pandas 做聚合分析 | 违反设计原则，效率低 | 使用 DuckDB SQL 查询 |
| 不检查文件路径 | 文件找不到报错 | 使用绝对路径，检查文件是否存在 |
| Excel 不指定工作表 | 默认读第一个，可能不是想要的 | 用 `--excel_sheet` 指定 |
| 查询结果不限制行数 | 输出过多，难以阅读 | 使用 LIMIT 限制结果数量 |
