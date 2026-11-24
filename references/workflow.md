# 工作流和产物

输出目录里保留这些文件。不要把它们写进安卓工程。

```text
<输出目录>/
  00-粗需求.md
  specs/
  testcases/
  pinky/test_case.md
  report.md
```

## 粗需求

用户给了需求文件就复制为 `00-粗需求.md`，不要改写它的范围。

否则用 Pinky 打开给定的 APK 所安装的应用，只记录登录前实际打开过的页面。一条需求一段，段之间空一行。相机、二维码、删账号和删内容不写进去。登录墙后面看不到的功能写成一句「未进入」，不要编造页面。

## SPEC 和 HomeTrans 用例

读取找到的 `hmos-spec-generate`，用 `00-粗需求.md` 和安卓工程生成 `specs/`。需要 homegraph 时按那个技能自己的说明索引工程。

然后读取 `hmos-test-case-generation`，为每个 `*-SPEC.md` 生成 `testcases/<功能>/test_case.md`。这两份技能怎么调用，以它们的 `SKILL.md` 为准，不要另造格式。

生成后的 SPEC 和 `test_case.md` 在后续步骤中保持不动。

## Pinky 用例

读取 `${CODEX_HOME:-$HOME/.codex}/skills/ht-to-pinky/SKILL.md` 并按它转换。输出写到本目录的 `pinky/test_case.md`。用户如果另外给了控件对照，一并传给那个技能。

## 执行

从 `pinky/test_case.md` 里一次取一条可执行用例交给 Pinky。每条都写明应用包名、要点的可见文字，以及通过时必须出现的原文。

一条结束并记录结果后，再发下一条。失败时停在这条，不要为了跑完后面的用例而改步骤。浏览器、Cookie、通知如果跳出应用，单独成一条，并允许用户拒绝。

不要执行文首或「未转换」里禁止的操作。

## 报告

`report.md` 用一个表：

| 用例 | 结果 | 依据 |
|---|---|---|
| 来源 Scenario | 通过、失败或未执行 | 屏幕上的原文，或未执行的原因 |

结果只有这三种。闪过的过渡文案不能写成通过。未转换的用例写未执行，原因沿用 Pinky 用例里的原因。

报告开头写工程路径、APK 路径、包名、输出目录，以及 Pinky、HomeTrans、`ht-to-pinky` 是本次检测到的还是本次安装的。
