# OmniToolbox.Localizations

这里保存 Omni 本体、公共控件和内置模块的官方词条。Common 嵌入各语言 XLIFF，`OmniLoc` 按稳定 ID 查询；本地与在线作者词条随模块源码或 DLL 交付。

## 语言与资源

| 语言 | 语言代码 | 文件 | 要求 |
| --- | --- | --- | --- |
| 简体中文 | `zh-CN` | `ChineseSimplified.xlf` | 完整源文，词条非空 |
| 繁体中文 | `zh-TW` | `TraditionalChinese.xlf` | 从简体生成，键集和占位符完整一致 |
| 英语 | `en` | `English.xlf` | 键集和占位符完整一致，译文非空 |
| 韩语 | `ko` | `Korean.xlf` | 键集和占位符完整一致，译文非空 |
| 日语 | `ja` | `Japanese.xlf` | 键集和占位符完整一致，译文非空 |

语言代码、文件名及界面名称由 `UILanguages` 集中定义，`Supported` 指定以上五种界面语言。`UILanguage` 保留德语、法语的既有枚举值供配置和模块契约兼容，不在界面中列出；韩语追加为 `Korean = 6`。译文文件的 `source-language` 为 `zh-CN`，`target-language` 使用对应代码；源文件不声明 `target-language`。

游戏原有术语按同表、同行的目标客户端原文维护；插件自有说明按实际功能翻译。语言身份由资源映射和 XLIFF 语言声明确定，不按文本字形推断。保留格式参数、命令、数字、单位及游戏符号，命令示例中的说明性参数名称可翻译。

本体词库的键集完整且译文全部非空时，该语言才可选择。每个模块单独检查词库完整性；缺少当前语言的模块显示“尚未翻译”，由宿主管理暂停与恢复。读取缺少或为空的词条会明确失败。

## 修改与生成

维护简体源文时保留已有 ID、`xml:space`、格式化占位符和命令含义。新增完整句子使用独立词条。作者填写简体文本后，繁体由生产转换器生成。

在 Omni 主仓库根目录运行：

```powershell
dotnet run --project tools/OmniToolbox.LocalizationTool -c Release -- --generate-traditional OmniToolbox.Localizations
dotnet run --project tools/OmniToolbox.LocalizationTool -c Release -- --validate-localizations OmniToolbox.Localizations
```

校验检查资源名称、语言角色、重复 ID、已提供的界面译文完整性、源文和键集归属、非法字符和占位符。简繁资源必须存在，简繁转换须保持一致；缺少其他语言文件时，该语言不可选择。失败返回非零退出码。

## 模块词条

API 1 作者使用 `ModuleLocalization.Create(source, translations)`。`source` 必须包含非空 `Title`、`Description` 和全部实际使用词条；宿主自动生成繁体，其余语言通过可选字典逐步补充。登记数据冻结复制，不保存作者实例或可变词库引用。

内置模块使用 `ModuleLocalization.FromResources` 显式绑定模块键与官方资源 ID，并取得所有已有语言的原始文本。界面绘制时查询当前语言；自有显示或搜索缓存按 `OmniLoc.Revision` 更新。语言切换由宿主异步入口执行。

TreeTools 提供通用转换与搜索，TreeToolKit 接收调用方传入的显示文字；插件专属词条由 Common 与各模块持有。
