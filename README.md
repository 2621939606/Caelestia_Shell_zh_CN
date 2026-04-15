# Caelestia Shell 简体中文汉化

将 [Caelestia Shell](https://github.com/caelestia-dots/caelestia) 界面从英文替换为简体中文。

## 原理

脚本将源目录（`/etc/xdg/quickshell/Caelestia`）复制到用户配置目录（`~/.config/quickshell/caelestia`），然后根据 `zh_CN.json` 翻译字典，直接将 QML 文件中的英文字符串替换为中文。Quickshell 会优先加载用户配置目录。

## 文件说明

| 文件 | 说明 |
|---|---|
| `zh_CN.json` | 翻译字典（英文 → 中文映射） |
| `install_zh_CN.py` | Python 汉化脚本|
| `TRANSLATION_GUIDE.md` | 翻译词条编写规则（维护者参考） |

## 快速开始

### Linux 部署

```bash
cd /path/to/Caelestia/assets/translations
python3 install_zh_CN.py
```
#### 脚本和Json文件需要在同目录下
如果不存在用户配置`~/.config/quickshell/caelestia`，脚本会自动从 `/etc/xdg/quickshell/Caelestia` 复制到 `~/.config/quickshell/caelestia` 并汉化。完成后重启 Caelestia Shell 即可生效。

### 指定源目录

```bash
python3 install_zh_CN.py /custom/path/to/caelestia
```

### 指定源目录和输出目录

```bash
python3 install_zh_CN.py /path/to/source /path/to/output
```

### Windows 测试

```powershell
cd assets\translations
python install_zh_CN.py ..\.. .\test_output
```

## 注意事项

### 在原有目录配置进行汉化

选择此选项时，请确保用户配置的目录结构和官方相同。

### 重新复制会清空用户配置

选择重新复制时，脚本会**删除整个目标目录后重新复制**。如果你在 `~/.config/quickshell/caelestia` 下有自定义配置（壁纸路径、主题设置等），请提前备份。

### 不可重复执行

脚本是直接替换英文为中文。已汉化的目录中英文原文已不存在，再次运行会匹配不到任何内容。如需重新汉化（如更新翻译后），请选择重新复制。

### 源文件不会被修改

脚本只修改输出目录（用户配置目录）中的副本，不会修改 `/etc/xdg/quickshell/Caelestia` 下的源文件。

### 依赖

- **Python 3.6+**（Linux 通常预装）
- 无第三方库依赖，仅使用标准库

### 翻译覆盖范围

当前已翻译 **94 个 QML 文件**，**654 条词条**，覆盖：

- 控制中心（网络、蓝牙、音频、外观、任务栏、通知、启动器、仪表盘）
- 锁屏界面
- 顶栏弹出面板（电池、网络、蓝牙、托盘、键盘布局）
- 仪表盘（媒体、性能监控、天气）
- 文件对话框
- 通知、录屏、窗口信息等

### 更新翻译后

修改 `zh_CN.json` 后重新运行脚本，选择重新复制即可。翻译字典的编写规则见 [TRANSLATION_GUIDE.md](TRANSLATION_GUIDE.md)。

## 贡献翻译

欢迎补充和修正翻译。编辑 `zh_CN.json` 时请遵循 [TRANSLATION_GUIDE.md](TRANSLATION_GUIDE.md) 中的规则，确保：

1. 上下文名与 QML 文件名一致
2. `_comment` 字段包含正确的文件路径
3. 英文原文与 QML 源码完全一致（包括空格、大小写、特殊字符）
4. 保留 `%1`、`%2` 等占位符不翻译

可用以下命令验证 JSON 格式：

```bash
python3 -m json.tool zh_CN.json > /dev/null && echo "JSON OK"
```
