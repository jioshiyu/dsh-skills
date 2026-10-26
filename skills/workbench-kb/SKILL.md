---
name: workbench-kb
description: >
  Personal knowledge base for this machine's environment, tools, models, courses,
  projects and experiments, located at D:\Projects\workbench\docs. Use when the user
  asks about "电脑环境", "Python/Node/Git 怎么配", "工具怎么用", "模型价格/额度",
  "实验报告", "SmartShop 项目", "上次那个报错", or asks to look something up in
  their notes/knowledge base.
---

# 工作台知识库检索

知识库位置：`D:\Projects\workbench\docs\`（Markdown，含环境档案、工具手册、软件清单、模型对比、实验报告索引、收件箱）。

## 检索方式

优先用检索脚本，不要盲目 read 整个目录：

```powershell
python D:\Projects\workbench\scripts\wb.py search "关键词1 关键词2"
```

- 脚本把命中结果写到 `D:\Projects\workbench\data\kb_last.json`（UTF-8），用 read 工具打开该文件查看；
- 结果字段：`score` / `doc` / `heading` / `engine` / `snippet`；
- 再按 `doc` 用 read 工具读取对应文档的完整章节。

## 何时使用

- 用户问本机环境、路径、已装软件、模型价格/余额、工具用法、实验报告、项目细节；
- 用户说"我之前记过""知识库里有没有"；
- 回答前先查一次，避免凭记忆编造路径或版本号。

## 写入约定

- 优先用原生工具 `mcp__kb__kb_append(note)`：一句话写清「问题 + 解法」，默认进 `inbox.md` 的「待整理」；
- 解决完非平凡问题（调试超过 10 分钟、踩坑、新配置）后，**主动**追加一条，不要等用户提醒；
- 确认过的知识 → 写到对应专题文档（环境/工具/模型/项目），写完调 `mcp__kb__kb_reindex`；
- 文档修改后运行 `python D:\Projects\workbench\scripts\wb.py session collect` 刷新工作台数据（旧名 `collect` 仍可用，会打 `W_DEPRECATED`）。

## 改完代码后的自检

```powershell
python D:\Projects\workbench\scripts\wb.py test    # 单元 + 端到端 + MCP
python D:\Projects\workbench\scripts\wb.py audit   # 旧入口/生成物/接线
python D:\Projects\workbench\scripts\wb.py eval    # 检索回归
python D:\Projects\workbench\scripts\wb.py docs    # 文档导航/关系图 + 断链检查
```

## 注意

- `dashboard/data.js` 含余额快照，只在本地，不要提交到公开仓库；
- 文档可能过期：版本号/价格类答案以检索结果中的"生成时间"为准，并提示用户核对。
