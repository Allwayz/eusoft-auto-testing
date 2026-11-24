---
name: eusoft-auto-testing
description: "Orchestrate Android app testing from a source project and APK through rough requirements, HomeTrans SPEC and test cases, ht-to-pinky, and one Pinky case at a time, then save a report. Use when the user asks to auto-test an Android app or run the HomeTrans-to-Pinky workflow. Ask before every install command."
---

# Eusoft Auto Testing

在 macOS 上把一个安卓应用从源码和安装包跑到 Pinky 测试报告。这是编排技能：检测环境、在用户同意后安装缺失工具、再按顺序产出中间文件。不要改安卓工程。

命令和判定细节在 [references/setup.md](references/setup.md)。产物目录在 [references/workflow.md](references/workflow.md)。

## 先拿到路径

用户已经给出的安卓工程路径和 APK 路径直接用。缺哪个，只问哪一个。不要为了已经说过的路径再问一遍。

可选输入：粗需求文件、输出目录、控件对照。没有粗需求就稍后用 Pinky 看能打开的界面来写。没有输出目录就用工程父目录下的 `<工程名>-auto-testing`。该目录已存在时不要覆盖，改用 `-2`、`-3`。

## 检查工程和安装包

按 [references/setup.md](references/setup.md) 确认这是安卓工程，并比较 APK 的包名和工程里的 `applicationId`。

不一致就停，列出两边的值。不要继续用这个安装包。

## 安装缺失工具

每一项都先检测。已经可用就跳过，并说明检测到的命令。

缺 Pinky、HomeTrans、Node.js 或 Homebrew 时，先把 [references/setup.md](references/setup.md) 里的那一条命令原样给用户，问是否批准执行。用户没有明确同意之前，不要运行安装命令。用户拒绝就停在这一项，说明后面哪一步因此做不了。

`ht init` 单独征求同意。它会提问，并把技能和环境变量写进本机。

`ht-to-pinky` 没有安装时，也先征求同意，再用技能安装器从 GitHub 安装。仓库默认分支是 `master`，命令里必须带 `--ref master`。

## 跑工作流

环境通过之后，按 [references/workflow.md](references/workflow.md) 顺序做：

1. 粗需求。用户给了文件就用文件。否则用 Pinky 只看登录前能到达的页面，写成 HomeTrans 能读的空行分段需求。
2. 用含 `hmos-spec-generate` 和 `hmos-test-case-generation` 的 HomeTrans 技能生成 SPEC 和 `test_case.md`。已发布的 npm 包里的 `a2h-spec-generate` 不是这个契约，不能拿它的输出去喂 `ht-to-pinky`。
3. 读取并遵守已安装的 `ht-to-pinky` 技能，生成 Pinky 短用例。不要改 HomeTrans 的用例。
4. 一次只把一条短用例交给 Pinky。不要把整份文件丢进去。
5. 写测试报告。通过、失败、未执行都要在，未执行要写原因。

登录墙后面的页面、相机、二维码、删账号和删内容都不要测。没有控件对照时，不要猜「点击继续」是哪个按钮。
