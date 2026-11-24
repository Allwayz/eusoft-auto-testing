# eusoft-auto-testing

Codex 技能。在 macOS 上把一个安卓应用从源码和 APK 跑到 Pinky 测试报告。

它只编排已有工具，不改安卓工程，也不改 HomeTrans 生成的用例。缺少 Pinky、HomeTrans 或 `ht-to-pinky` 时，会先把安装命令给你，你明确同意后才执行。

## 安装

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-installer/scripts/install-skill-from-github.py" \
  --repo Allwayz/eusoft-auto-testing \
  --path . \
  --ref master \
  --name eusoft-auto-testing
```

仓库默认分支是 `master`，不要省略 `--ref master`。目标目录已存在时，安装器不会覆盖。

安装后在 Codex 里用 `$eusoft-auto-testing` 调用。

## 需要你提供

- 安卓工程路径
- APK 路径

已经在提示里说过的路径会直接用。可选：粗需求文件、输出目录、控件对照。

APK 包名必须和工程里的 `applicationId` 一致，否则停止。

## 工作流

```text
Android 源码 + APK
  → 粗需求
  → HomeTrans：homegraph + SPEC + test_case.md
  → ht-to-pinky
  → 一次一条交给 Pinky
  → 测试报告
```

没有粗需求时，用 Pinky 只看登录前实际打开的页面来写。登录墙后面的内容写成「未进入」，不编造。

HomeTrans 必须使用含 `hmos-spec-generate` 和 `hmos-test-case-generation` 的技能。npm 包里的 `a2h-spec-generate` 不能喂给 `ht-to-pinky`。

## 产物

默认写在安卓工程父目录的 `<工程名>-auto-testing`。目录已存在时改用 `-2`、`-3`，不覆盖。

```text
<输出目录>/
  00-粗需求.md
  specs/
  testcases/
  pinky/test_case.md
  report.md
```

## 不会做的事

- 不测相机、二维码、删账号、删内容
- 不测登录墙后面看不到的页面
- 没有控件对照时，不把「点击继续」猜成某个按钮
- 不把整份用例一次丢给 Pinky
- 你拒绝安装时，停在那一项，不继续依赖它的后续步骤

细节在 [references/setup.md](references/setup.md) 和 [references/workflow.md](references/workflow.md)。
