# 项目整理与修改记录

## 1. 数据校验与口径修正

- 校验了 `combined_temperature_test_data.csv` 与原始 Excel `人体温度传感器测试.xlsx` 的一致性。
- 确认数值列和原始清洗逻辑逐行一致：107 条有效记录、9 个核心字段，没有发现列错位或数值搬运错误。
- 发现并说明了一个关键时间问题：`场景123登记表` 原始只记录时分秒，原整合脚本统一补成 `2026-03-31`，这会影响跨真实日期排序和相邻变化分析。
- 没有覆盖原始 CSV，而是另存校正后的处理表：
  - `analysis_output/processed_temperature_data_with_corrected_dates.csv`
- 新增数据校验说明：
  - `analysis_output/combined_data_validation.md`

## 2. 统计分析与报告产物

- 新增可重复运行的统计脚本：
  - `analyze_temperature_statistics.py`
- 生成/更新统计报告：
  - `analysis_output/temperature_statistical_report.md`
  - `analysis_output/final_temperature_report.md`
- 输出多份统计 CSV，方便复核关键数字：
  - `analysis_output/overall_device_metrics.csv`
  - `analysis_output/xiaomi_category_metrics.csv`
  - `analysis_output/xiaomi_subject_metrics.csv`
  - `analysis_output/xiaomi_scene_metrics.csv`
  - `analysis_output/trend_delta_metrics.csv`
  - `analysis_output/core_skin_similarity_level_metrics.csv`
  - `analysis_output/core_skin_similarity_closeness.csv`
  - `analysis_output/core_skin_similarity_trend_metrics.csv`
  - `analysis_output/core_skin_threshold_bins.csv`
  - `analysis_output/sweat_level_summary.csv`
  - `analysis_output/sweat_trend_summary.csv`

## 3. 新增图表与分析结论

- 新增了“设备更像核心温还是皮温”的分析，区分：
  - 绝对数值更接近谁。
  - 相邻变化趋势更接近谁。
- 新增胸部皮温阈值分析：当 `CORE_Chest_Skin` 升到约 `35.7-36.0 ℃` 以上时，小米/水银读数更容易在绝对值上靠近皮温。
- 新增运动出汗点前后探索性分析，明确出汗标记只有 2 个，不能建立通用阈值。
- 生成图表：
  - `analysis_output/plots/device_core_skin_similarity.png`
  - `analysis_output/plots/chest_skin_threshold_skinlike.png`
  - `analysis_output/plots/sweat_motion_response.png`

## 4. 静态网页报告

- 按产品/算法内部报告定位，新增静态网页：
  - `analysis_output/web_report/index.html`
  - `analysis_output/web_report/styles.css`
- 页面内容包括：
  - 温度的主要用途。
  - 用户对核心温、皮温、DPG 的真实穿戴需求。
  - CORE 1 / CORE 2 / 直肠胶囊 / 水银温度计 / 小米温度的能力对比。
  - 小米实测关键指标和风险判断。
  - 算法门槛说明，例如经期、入睡、昼夜节律、运动热负荷需要的温度表现。
  - DPG 与 Hammock 曲线的网页 SVG 示意。
- 将网页使用的图片复制到：
  - `analysis_output/web_report/assets/`
- 网页不依赖外部 CDN 或网络，可直接通过本地浏览器打开。

## 5. 关键结论整理

- 小米温度相对 CORE 胸核心温估计：
  - MAE：`0.47 ℃`
  - RMSE：`0.60 ℃`
  - ±0.3 ℃ 内：`45%`
- 水银温度计相对 CORE 胸核心温估计：
  - MAE：`0.54 ℃`
  - Pearson r：`0.56`
- 小米温度整体 `74%` 的点数值更接近 CORE 核心温，但趋势变化更受皮温/局部环境影响。
- 当 CORE 胸皮温在 `36-36.5 ℃` 区间时，小米有 `88%` 的点数值更接近皮温。
- 当前小米温度更适合作为低权重辅助信号，不建议独立承担女性周期、夜间恢复、昼夜节律等核心算法输入。
- CORE 1 在本项目中继续称为“伪金标/CORE 1 胸核心温估计”，没有写成直肠胶囊级真实金标。

## 6. 使用的 Codex 功能或工具

- 文件与代码检索：
  - 使用 `rg`、`sed`、`find`、`ls`、`head` 等命令查看项目结构、脚本、CSV、报告和图表资源。
- 数据分析：
  - 使用 Codex 工作区自带 Python 运行时读取 Excel/CSV，进行 pandas 统计分析。
  - 使用 Spreadsheets 技能处理表格类数据分析口径。
- 文件修改：
  - 使用 `apply_patch` 新增和修改脚本、报告、网页 HTML/CSS。
- 运行与验证：
  - 使用 `exec_command` 执行 Python 脚本、校验输出文件、检查关键数值。
  - 使用 `py_compile` 验证 Python 脚本语法。
- 图像查看：
  - 使用 `view_image` 检查生成的 PNG 图表和网页截图。
- 网页验证：
  - 使用 Node REPL / Playwright 调用本机 Chrome 检查静态网页。
  - 验证桌面端和 390px 移动端无横向溢出、图片加载正常、导航锚点有效。
- 规划与协作：
  - 使用 Plan Mode 先整理网页实施计划，再在 Default 模式中执行实现。

## 7. 未改动或保留的内容

- 未覆盖原始 Excel：
  - `人体温度传感器测试.xlsx`
- 未覆盖原始整合 CSV：
  - `combined_temperature_test_data.csv`
- 原始绘图脚本和既有 `output_fig/` 图片保留。
- 所有新增分析均放在 `analysis_output/` 或新增脚本中，便于追溯和复现。
