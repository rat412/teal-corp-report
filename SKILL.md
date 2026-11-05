---
name: teal-corp-report
description: 青绿色企业工作汇报PPT模板风格（Teal Corporate Report）。当需要生成正式工作汇报/进展汇报类PPT、且要求青绿（teal）企业风格时使用。提供封面/目录/正文/结束四种页面骨架与视觉规范。触发词：工作汇报PPT、青绿风格PPT、企业汇报模板、进展汇报。
---

# 青绿企业汇报 PPT 风格 Skill（Teal Corporate Report）

一套从实际汇报文件中提取的可复用风格规范。所有页面基于 slidep DSL（受限 JSX），画布 1280×720。

## 一、视觉规范（硬约束）

### 配色

| 用途 | 色值 | 说明 |
|---|---|---|
| 主标题 / 强调 | `#00918D` | 亮青色，内容页标题、章节高亮、表头 |
| 色带 / 封面大色块 | `#00706B` | 深青绿，封面与结束页横带（用 band.png 纹理图） |
| 目录高亮条 | `#008A80` | 目录当前章节实心条 |
| 正文描边框 | `#BFE0DA` | 1.5px 圆角描边，白底 |
| 浅色卡片底 | `#E9F4F0` | 薄荷绿，要点卡片 |
| 正文文字 | `#1E293B` | 近黑 |
| 弱化文字 / 页码 | `#707070` / `#64748B` | |
| 辅助蓝 | `#548BD7` | 第二序列 / 区分色 |

### 字体
- 中文：微软雅黑
- 西文/数字：Arial
- 字号阶梯（1280×720 画布）：封面主标 62 / 结束页大字 72 / 页面标题 34 bold / 正文 19 / 卡片小标 21 bold / 卡片正文 14-15 / 页码 14

### 素材（本 skill 自带，assets/ 下）
- `logo.png` 占位 LOGO（901×307，**发布前请替换为使用方授权的 LOGO**）
- `band.png` 封面/结束页中部青绿色带纹理（1760×488）
- `catalog_bg.png` 目录页左侧弧形青绿面板（1280×720 整页背景）

### 页面骨架坐标

**封面**（templates/cover.slide）：
- LOGO 左上角 left:0 top:5 w393 h133
- 色带 band.png left:0 top:193 w1280 h356
- 色带内细白线两条：y=259 与 y=547（宽 1254，h2，白，opacity 0.85）
- 主标题白色 62px bold 居中 top:315；副标题白色 24px 居中 top:440（无副标题可省略）

**目录**（templates/toc.slide，含 ACTIVE 参数高亮当前章）：
- 整页背景 catalog_bg.png
- 白色竖条 left:138 top:240 w5 h86；"目录" 白色 60px bold left:170 top:238；"CONTENTS" 白色 18px left:172 top:322（width 必须 ≥500，否则截断）
- 右侧条目区 left:620 top:195 w620：每项 = 72px 描边圆（3px #008A80，白底，内填章节序号 28px #00706B）+ 横条（h60，圆与条 marginLeft:-18 衔接；当前章 background:#008A80 白字 bold，其余 #F2F2F2 深字 + 1px #D9D9D9 描边）

**正文页**：两种骨架，均含 LOGO + 标题 + 圆角描边正文框 + 右下页码：
- `templates/content.slide`：正文框 + 下方图形区（Image 占位）
- `templates/content_cards.slide`：正文框 + 三张薄荷绿要点卡片

骨架坐标：
- LOGO 小号 left:42 top:20 w90 h31
- 标题 34px bold #00918D，left:145 top:22
- 正文段放在**圆角描边框**：left:42 top:76 width:1196，border 1.5px #BFE0DA，radius 10，padding 14px 24px，白底；文字 19px #1E293B，lineHeight 1.55，首行缩进两个全角空格
- 下方图形区：架构图/截图用 Image，left 42、width 1196、borderRadius 10，top 按正文框底边 +16 起
- 页码右下角：right:56 top:678，14px #707070 Arial（封面/目录/结束页不加页码）

**结束页**（templates/ending.slide）：与封面同骨架，色带内只放 {{结束语}} 72px 白色 bold 居中 top:325。

**模板原则**：templates/ 下所有文件只含样式与 `{{占位符}}`，不含任何实质业务内容；生成时用实际内容替换占位符。

## 二、生成流程

1. 把本 skill 的 `assets/` 三个文件复制到目标项目目录的 `assets/` 下（logo.png 按需替换）
2. 复制 `templates/` 中对应骨架为每页的 `.slide` 文件，替换标题/正文/图片/页码占位
3. `slidep create "<项目目录>/<文件名>.pptx"` 创建空白文件，`present_files` 打开预览
4. 逐页 `slidep upsert-dsl <pptx> --dsl-file slides/NN.slide`（追加）并 `slidep lint <pptx> --dsl-file slides/NN.slide --page-index <idx>` 检查
5. 完成后渲染抽查：`slidep screenshot <pptx> --page-index <n> --out x.png`

## 三、注意事项（踩过的坑）

- DSL 中 map 循环的 `key` 属性必须加引号或花括号（`key={i}` 或 `key="0"`），写 `key=0` 会报语法错误
- 文本中的引号用中文引号“”，避免 JSX 属性冲突
- 目录页 CONTENTS 的 Text 必须显式给 width ≥ 500，否则长字距下会被截断
- 修改某页用 `upsert-dsl --page-index N` 是**替换**该页，不是插入；插入新页只能追加
- 个别 pptx 含特殊嵌入内容时 LibreOffice 无法加载（source file could not be loaded），改用 `slidep inspect` / `slidep screenshot` 验证
