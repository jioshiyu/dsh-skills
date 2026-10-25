---
name: study-repo
description: >
  把课程作业/题库/实验整理成「可编译、可执行、可对拍」的独立仓库（notebook 系列）。
  当用户说"整理桌面上的 XX 作业""像算法/汇编那样做""做成可以跑的仓库""上交 github"，
  或要做底层/硬件/系统类实验的多题汇总时使用。含四层骨架、七步流程、图片四规则、发布清单。
---

# 把一堆作业做成 notebook 仓库

## 四层骨架（缺一层就不是这个系列）

| 层 | 要找的东西 | 没有怎么办 |
|---|---|---|
| 运行时 | 能执行原程序的环境 | **自己造最小运行时**（汇编仓库是自己写 8086 模拟器） |
| 参考模型 | 独立实现的"正确答案" | 用 Python 重写一遍语义，不照抄原实现 |
| 对拍 | 固定用例 + 随机输入比对 | `shared/check.py` 一套壳，逐题适配 |
| 方法论 | `docs/methodology.md` + `lessons.md` | 每批结束把坑写成规则 |

**先问一句**：这个项目里，`shared/` 有没有 ≥3 道题共用的文件？没有就别建仓库，直接做题。

## 七步流程（每题）

1. 读原版，先确认它**有没有输出**（没有就必须自己定义"什么算对"）；
2. 定 I/O 约定（全仓库统一，别每题自创）：输入 token 化、输出 + 换行、读键不回显；
3. 写参考模型 `tests/ref.py`（`expected(inp)`，屏幕类再写 `expected_screen(inp)`）；
4. 固定用例 `tests/cases.json`（原题数值必进 + 边界：0/1、最大、溢出、空集）；
5. 写重写版 `main`，**保留原版要练的机制**，只把 I/O 标准化；
6. `python shared/check.py <题> --stress 40`，红了先怀疑自己的期望值；
7. 写进 `docs/problems.md`：练什么 / 原版做法 / 重写点 / 关键指令。

## 判据与红线

- 用例里的 `expect` 与 `ref.py` **必须独立写**——两者不一致说明至少一个错（这条抓到过 3 处写错的期望值）；
- 重写版按语义写对，**原版的 bug 不要复制**，但要在 docs 里写清"原版哪里错、为什么"；
- 不支持的指令/中断**直接报错**，绝不静默猜（这是能拿它做对拍的前提）；
- 屏幕/图形类程序也能测：把屏幕文本当输出比对。

## 图片四规则（省 token + 有产物）

1. 读图必须有产出：落盘到 `docs/images/` 并被引用，或写出一条文本化结论；
2. 能用结构化文本（DOM 摘要、表格抽取、屏幕文本）代替就不读图；
3. 图片优先提交"生成脚本 + 数据"，而不是裸 PNG（见 `shared/screen_png.py`）；
4. 收尾自问：这次读的图，有几张变成了仓库里的图或断言？0 张 = 没回本。

## 发布清单

1. 目录/文档去标识化（学号、姓名、课程老师）；
2. 网页建**空仓库**（不勾 README/gitignore/LICENSE）；
3. `git remote add origin … && git push -u origin main`（沙箱内会被拒，需普通终端）；
4. CI：装工具链 → `check.py`（固定用例）+ `check.py --stress 30`；
5. README 加"姊妹仓库"链接 + topic `notebook-family`。

## 成本纪律

- 开工先跑一次 `wb.py usage --guard 3000000` 记基线，每 20 轮自查；
- 长源码先 `grep` 标号/中断调用看结构，再决定读哪段；
- GBK/PDF 等要转码的源，先转存 `_scratch/`，不要反复读原目录；
- 汇报用表格替段落：**体积看输入、账单看输出**（命中率 99%+ 时输出约占一半费用）。

## 仓库族与模板（项目多了怎么连）

- **每个仓库根放 `family.json`**：`name / kind / topic / runtime / items / ci / shared_reusable / skills / docs / status / homepage`；
- `python wb.py family --ci` → 生成 `docs/family.md`：仓库表（含最近提交、Actions 结论）+ 可复用资产表 + 跨仓 Mermaid 图。
  新项目一落盘就自动进表，**不需要记得去改某个文档**；
- 新项目起手：`cp -r notebook-template my-xxx && rm -rf .git && git init`，
  改 `family.json` 与 README 顶部，再 `python tools/scaffold.py problems/<分类>/<slug>`；
- `wb.py family --check` 会列出"有 .git 但没写 family.json"的仓库——那就是即将变成孤岛的仓库。

## 对拍不止 stdout（三种输出都要能比）

| 类型 | ref.py 里提供 | 典型场景 |
|---|---|---|
| 文本 | `expected(inp)` | 绝大多数题 |
| 屏幕 | `expected_screen(inp)` | TUI / 图形 / 寄存器面板（80x25） |
| 文件 | `expected_disk(inp)` | 字典、日志、带持久化的程序（配内存虚拟盘） |

> 屏幕/文件对拍能把"看得见但没法断言的输出"变成可回归的测试——这是本项目区别于普通作业仓库的关键。
