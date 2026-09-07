# Session Record: 发布 GNFS 入门教程

- Time: 2026-09-07T19:00:40+08:00
- Window: 当前发布任务开始（精确开始时间未记录）至 2026-09-07T19:00:40+08:00
- Previous Record: none
- Commit: pending（由包含本记录的提交确定）
- Branch: main
- Task: 将大整数分解的逐步解释文档发布到个人博客。
- Source Sessions:
  - Harness: Codex
  - Evidence: 当前 Codex 会话的明确发布指令。
  - Checked: 当前会话、仓库 README、Jekyll 配置、布局、已有文章、Git 状态、GitHub Pages 配置。
  - Used: 已完成的教程正文及本次发布验证结果。
  - Unavailable: 未检索与当前博客仓库无明确关联的其他会话。

## Outcome

新增 `_posts/2026-09-07-gnfs-step-by-step.md`，使用现有 post 布局与 MathJax，文章包含 12 步讲解和自动目录。公开页面只保留可独立阅读的内容与公共参考资料。

固定地址为 `/2026/09/07/gnfs-step-by-step.html`。文章时间包含 +08:00 时区，发布时刻已到。

本地使用 Kramdown 2.5.2、GFM parser 1.1.0 和 mathjax 输出验证：14 个二级章节（12 步及两个附录）、5 张表格、134 处行内公式、48 处独立公式，无解析警告、无残留美元分隔符。教程的小整数例子已复算。

## Engineering Context

- 博客由 GitHub Pages 从 main 分支根目录构建，使用 Jekyll/Minima；已有 `_includes/custom-head.html` 加载 MathJax 3。
- Kramdown 行内数学使用双美元分隔符，能输出 MathJax 默认支持的行内格式；无需改变站点共享脚本。
- 含未转义竖线的绝对值表达式会被 GFM 解析为表格，文章使用 `\lvert` 和 `\rvert` 避免冲突。
- 构建依赖安装在仓库外的临时目录；不修改主题、依赖声明或其他文章。
- 后续同类发布应检查 GitHub Pages 对应提交的构建结果，并在浏览器核对公式与移动端溢出。

## Open Questions And Risks

本地已验证 Markdown 转换；完整站点构建和公开页面排版需要在推送后通过 Pages 和浏览器确认。数学排版沿用站点现有的外部 MathJax CDN。
