# OmniToolbox.Localizations

这里保存 Omni 本体、公共控件和随本体发布的模块词条。模块作者自己的词条仍随模块交付，不提交到这个仓库。

## 文字如何流到界面

```text
本仓库 XLIFF
    ↓
OmniToolbox.Common
    ↓
OmniLoc 注册、校验、简繁转换
    ↓
OmniToolbox.Tree / TreePublic / TreeHouseOnline
    ↓
窗口、命令、搜索与模块标题
```

| 文件 | 用途 |
| --- | --- |
| `ChineseSimplified.xlf` | 简体原文、稳定 ID、占位符和命令格式 |
| `TraditionalChinese.xlf` | 由 Omni 自动生成的繁体译文 |

繁体不需要人工维护。语言切换会刷新文字、别名和搜索缓存，不重启业务模块。

## 修改词条

只改简体文件，并保留：

- 已有词条 ID。
- `xml:space`。
- 格式化占位符，例如 `{0}`。
- 命令名称和参数含义。

新增或修改文字后，在 Omni 主仓库根目录依次生成和校验：

```powershell
dotnet run --project tests/OmniToolbox.Test -c Release -- --generate-traditional OmniToolbox.Localizations
dotnet run --project tests/OmniToolbox.Test -c Release -- --validate-localizations OmniToolbox.Localizations
```

校验会检查重复 ID、空词条、简繁键集和占位符；任何失败都会返回非零退出码。

## 系统架构中的位置

| 项目 | 词条归属 |
| --- | --- |
| [OmniToolbox.Common](https://github.com/YouShux/OmniToolbox.Common) | 嵌入并注册官方词条 |
| `OmniToolbox.Tree` | 使用 Common 提供的官方词条 |
| [OmniToolbox.TreePublic](https://github.com/YouShux/OmniToolbox.TreePublic) | 使用 Common 提供的官方词条 |
| [TreeTools](https://github.com/YouShux/TreeTools) | 不保存界面文字，只提供搜索和简繁转换工具 |
| [TreeToolKit](https://github.com/YouShux/TreeToolKit) | 不保存界面文字，由调用方传入显示内容 |
| [TreeHouseOnline](https://github.com/YouShux/OmniToolbox.TreeHouseOnline) | 在模块源码或 DLL 内交付作者词条 |
