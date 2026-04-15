# zh_CN.json 翻译词条编写规则

## 文件概述

`zh_CN.json` 是 Caelestia Shell 的简体中文翻译字典。`install_zh_CN.py` 脚本读取此文件，将英文原文替换为中文译文。

## 基本结构

```json
{
    "_comment_上下文名": "描述 → 文件相对路径",
    "上下文名": {
        "English source": "中文翻译",
        "Another string": "另一个翻译"
    }
}
```

## 规则

### 1. 上下文名 (Context Key)

- **必须与 QML 文件名完全一致**（去掉 `.qml` 后缀）
- 例：`AudioPane.qml` → 上下文名为 `"AudioPane"`
- 区分大小写

### 2. `_comment` 注释字段

每个上下文 **必须** 有一个对应的 `_comment_上下文名` 字段，脚本依靠它定位目标文件。

#### 单文件格式

```json
"_comment_AudioPane": "音频设置面板 → modules/controlcenter/audio/AudioPane.qml"
```

格式：`"任意描述 → 从项目根目录开始的相对路径"`

- 必须包含 `→`（Unicode 箭头 U+2192）
- 路径从项目根目录开始（如 `modules/`、`services/`、`config/`、`components/`）
- 路径使用 `/` 分隔（不用 `\`）

#### 合并文件格式（同名文件）

当项目中有多个同名 QML 文件时，它们的翻译合并在同一个上下文下：

```json
"_comment_Settings": "⚠ 合并: modules/controlcenter/launcher/Settings.qml + modules/controlcenter/bluetooth/Settings.qml"
```

格式：`"⚠ 合并: 路径1 + 路径2 + 路径3"`

- 必须包含 `:`（冒号）
- 多个路径用 ` + ` 分隔
- 脚本会在所有列出的文件中尝试替换

### 3. 翻译词条

```json
"上下文名": {
    "English source text": "中文翻译文本"
}
```

- **Key**：QML 源文件中的英文原文（必须与源码完全一致）
- **Value**：对应的中文翻译
- Key 和 Value 相同时（如无需翻译的专有名词），脚本会自动跳过

### 4. 如何找到英文原文

脚本支持三种 QML 字符串模式：

#### 模式 1：`qsTr("...")`（最常见）

```qml
// QML 源码
text: qsTr("Audio Settings")
```

```json
// JSON 写法
"Audio Settings": "音频设置"
```

#### 模式 2：`qsTr(`...`)`（模板字符串）

```qml
// QML 源码
text: qsTr(`"%1" selected`)
```

```json
// JSON 写法
"\"%1\" selected": "已选择\"%1\""
```

#### 模式 3：`label: "..."`（仅 PaneRegistry）

```qml
// QML 源码
readonly property string label: "network"
```

```json
// JSON 写法（上下文必须是 PaneRegistry）
"network": "网络"
```

### 5. 特殊字符处理

| 源码中的字符 | JSON 中的写法 | 说明 |
|---|---|---|
| `\n` | `\n` | 换行符，直接写 |
| `"` | `\"` | 引号需转义 |
| `<b>`, `</b>` | `<b>`, `</b>` | HTML 标签原样保留 |
| `%1`, `%2` | `%1`, `%2` | Qt 占位符，保留不翻译 |

示例：

```json
"Caps lock and Num lock are ON.\nKeyboard layout: %1": "大写锁定与数字锁定均已开启。\n键盘布局：%1"
```

### 6. `_` 开头的 Key 被忽略

任何以 `_` 开头的 Key 都会被脚本忽略，可以用来写注释：

```json
"_comment": "这是全局注释",
"_comment_AudioPane": "这是 AudioPane 的路径注释"
```

## 添加新翻译的步骤

### 步骤 1：找到目标 QML 文件

确认要翻译的 QML 文件路径，例：`modules/dashboard/NewWidget.qml`

### 步骤 2：提取英文字符串

在 QML 文件中搜索 `qsTr(`，记录所有英文字符串：

```qml
title: qsTr("My Widget")
description: qsTr("Widget description")
```

### 步骤 3：添加到 JSON

```json
"_comment_NewWidget": "新组件描述 → modules/dashboard/NewWidget.qml",
"NewWidget": {
    "My Widget": "我的组件",
    "Widget description": "组件描述"
}
```

### 步骤 4：验证

```bash
# Windows 测试
python install_zh_CN.py ..\.. .\test_output

# Linux 部署
python install_zh_CN.py
```

## 同名文件合并示例

项目中有 `modules/launcher/Content.qml` 和 `modules/dashboard/Content.qml`，两者都叫 `Content.qml`：

```json
"_comment_Content": "⚠ 合并: modules/launcher/Content.qml + modules/dashboard/Content.qml",
"Content": {
    "Type \"%1\" for commands": "输入\"%1\"以使用命令",
    "Dashboard": "仪表盘",
    "Media": "媒体",
    "Performance": "性能",
    "Weather": "天气"
}
```

脚本会在两个文件中都尝试替换，各自只替换自己包含的字符串。

## 常见错误

| 错误 | 原因 | 修复 |
|---|---|---|
| 词条显示"未匹配" | 英文原文与 QML 源码不一致 | 检查空格、大小写、特殊字符 |
| 整个上下文未匹配 | `_comment` 路径错误或缺失 | 检查路径是否从项目根目录开始 |
| 替换了不该替换的 | Key 太短（如 `"On"`）匹配到其他位置 | 确认 `qsTr("On")` 确实存在于源码中 |
| JSON 解析报错 | 格式错误（多余逗号、缺引号） | 用 `python -m json.tool zh_CN.json` 验证 |
