---
name: webtest
description: >
  Web 自动化测试与软件测试大赛（慕测）实战技能：Selenium 浏览器操作、反爬/人机验证
  会话策略、页面对象与任务队列、截图/Excel 交付规范。当用户提到"软件测试大赛"
  "慕测""Selenium""Web 自动化""携程测试"或需要真实浏览器操作网页时使用。
---

# Web 自动化与软件测试大赛

## 先看环境

- 便携 Chrome：`D:\WebTest\browsers\chrome-win64\chrome.exe`（本机无系统 Chrome）
- 驱动：`D:\WebTest\browsers\chromedriver-win64\chromedriver.exe`（必须同版本）
- 工具包：`D:\Projects\_tools\webtest\`（browser / daemon / ctrip / selenium_kit）
- 知识：`workbench/docs/Web自动化与软件测试.md`

## 标准流程

1. **读附件**：用 `office_read` 读需求文档、注意事项、模板；用 `openpyxl` 看 Excel 表头，**不要凭印象填**。
2. **探页面**：先 `dump_html` 拿 DOM，按文本/属性定位；确有必要再截图（截图最贵）。
3. **起会话**：`browser.launch(profile=...)` → 人工过验证/登录 → 之后所有用例在**同一会话**跑。
4. **批量执行**：复杂多步用 `daemon.py --tasks <dir>`，一条任务一个脚本，输出落 `out_*.txt`。
5. **截图**：只调模板的 `take_screenshot`；一条用例一张，调试完删多余。
6. **交付**：Excel 10 列填全 → `py_compile` → `screenshots.zip`（zip，不是 rar）→ 核对命名/大小写/驱动路径。

## 硬性规范（会扣分）

- 1 需求 = 1 个 `test_<软件名>_R00X` 函数，不多不少；
- 只改 test-code 区，截图函数原样；
- 文件 UTF-8；驱动路径官方；
- 需求编号 `R001` 不能写成 `r001`/`ROO1`；截图文件名字段与真实文件逐字一致。

## 前置自检（开工前逐条过，避免返工）

- [ ] 浏览器/驱动同版本：`chrome.exe --version` 与 `chromedriver --version` 一致；
- [ ] 页面结构先 `dom_summary`/`dump_html` 再考虑截图（读图最贵）；
- [ ] 冷门城市（无锡/海口/合肥…）**必须输入搜索**，不要点字母分组；
- [ ] 日期范围**只能从“今天”拖**；点单日=单日出发；跨月拖动无效；
- [ ] 携程 flights 出现 `whaleguard block`/`verify.ctrip.com` → 人工过一次，之后用同一会话；
- [ ] 交付前跑 `smoke.py --attach <port>` 验证选择器没失效；
- [ ] 截图一条用例只留一张；ZIP 不是 RAR；驱动路径官方；UTF-8。

## 坑（都踩过）

- 携程 flights 对自动化/CDP 会返回 `whaleguard block`，改 UA 无效 → 人工验证 + 持久会话；
- 城市列表虚拟滚动，冷门城市必须输入搜索；
- 日期范围只能从“今天”拖；点单日变成单日出发；跨月拖动不生效；
- Windows 下 `tempfile.mkstemp` 要 `os.close(fd)`，否则文件删不掉；
- DSH 沙箱不能写桌面，快捷方式放 `D:\Projects\WebTest\`。
