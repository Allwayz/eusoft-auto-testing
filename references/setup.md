# 环境检测和安装

只在 macOS 上按这里安装。检测通过就不要重复安装。

## 安卓工程

目录里要同时有：

- `settings.gradle` 或 `settings.gradle.kts`
- `app/build.gradle` 或 `app/build.gradle.kts`
- `app/src/main/AndroidManifest.xml`

缺任何一项就不是可测的安卓应用工程，停下来说明缺什么。

从 Gradle 文件读取 `applicationId`。有 `applicationId` 就用它，不要用 `namespace` 代替。

## 安装包

用 Android SDK 的 `aapt` 或 `aapt2` 读包名。先找 `aapt`，没有再用 `aapt2`：

```bash
AAPT="$(find "${ANDROID_HOME:-$HOME/Library/Android/sdk}/build-tools" -type f \( -name aapt -o -name aapt2 \) | sort | tail -n 1)"
"$AAPT" dump badging "<apk>" | awk -F"'" '/^package:/{print $2; exit}'
```

这个包名必须和工程的 `applicationId` 完全相同。不同就停，并写出两个值。文件名不必和工程目录同名。

找不到 `aapt` 时不要猜包名。请用户安装 Android SDK Build-Tools，或给出 `aapt` 的路径。

## Pinky

Pinky App 就是要装的东西。从 dmg 拖进「应用程序」，和 Homebrew cask 装的是同一个应用。`pinkyctl` 只是 Homebrew 放在 PATH 上的启动脚本，内容是执行 `/Applications/Pinky.app/Contents/MacOS/Pinky`。App 里的可执行文件接受同样的命令，包括 `mcp`。

下面任一成立就视为已安装，不要再要求安装：

- `command -v pinkyctl` 有结果
- `/Applications/Pinky.app` 存在

只有两个都没有时，才征求同意后安装。有 Homebrew 就用：

```bash
brew install --cask diygod/tap/pinky
```

没有 Homebrew 时，不要换别的安装脚本。请用户从 [Pinky 发布页](https://github.com/DIYgod/pinky/releases/latest) 下载对应 Mac 的 dmg，把 Pinky 拖进「应用程序」。

装完再检测一次。App 和 `pinkyctl` 仍然都没有就停。

## HomeTrans

先看 Node.js。`node -v` 低于 22.18 时，不要安装 HomeTrans，请用户先升级 Node.js。

检测：

```bash
command -v ht || command -v hometrans
```

没有时，只有用户同意才运行：

```bash
npm install -g @buaa_smat/hometrans
```

装完用 `ht --version` 确认。

`homegraph` 不在 PATH 上时，单独征求同意后再运行：

```bash
ht init
```

告诉用户这条命令会交互选择编辑器，并可能把环境变量写入 shell 配置。用户拒绝时不要强行初始化。

## 生成用例所需的技能

`ht-to-pinky` 只接受 `hmos-test-case-generation` 写出的 `test_case.md`。

在这些位置查找 `hmos-spec-generate/SKILL.md` 和 `hmos-test-case-generation/SKILL.md`：

- 用户给出的 HomeTrans 源码目录
- `~/.agents/skills`
- `~/.codex/skills`
- `npm root -g` 下的 `@buaa_smat/hometrans/skills`

两份都找到才继续。只找到 `a2h-spec-generate` 不够。

都没有时，征求同意后克隆：

```bash
git clone https://gitcode.com/SMAT/HomeTrans.git
```

克隆目录由用户指定；没指定就放在安卓工程的父目录旁边，目录名 `HomeTrans`。然后使用其中的 `skills/hmos-spec-generate` 和 `skills/hmos-test-case-generation`。

## ht-to-pinky

检测：

```bash
test -f "${CODEX_HOME:-$HOME/.codex}/skills/ht-to-pinky/SKILL.md"
```

没有时，只有用户同意才运行：

```bash
python3 "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-installer/scripts/install-skill-from-github.py" \
  --repo Allwayz/ht-to-pinky \
  --path ht-to-pinky \
  --ref master
```

必须带 `--ref master`。安装器默认分支是 `main`，这个仓库的默认分支不是 `main`。目标目录已存在时安装器会拒绝覆盖；那时改为使用已有技能，不要删掉它。
