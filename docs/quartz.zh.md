> 本文档由机器翻译生成，仅供参考。

# 使用 Quartz 发布

Obsidian 在应用内渲染 `tabsdown` 块。若要在 [Quartz](https://quartz.jzhao.xyz/) 站点上渲染相同的语法，请安装独立的 [quartz-tabsdown](https://github.com/grafanaKibana/quartz-tabsdown) 插件：

```bash
npx quartz plugin add github:grafanaKibana/quartz-tabsdown
```

Quartz 插件与 Tabsdown 共享同一解析器和外观默认值。交互式标签页需要 JavaScript 支持。若未启用 JavaScript，Quartz 会按顺序在各标签标题下依次渲染所有面板内容。

Tabsdown 1.4.0 将每个块级设置以 `property=value` 形式保存，包括 `position=` 和 `layout=`，并要求解析器、配置和 CSS 支持相互匹配。请确保 Obsidian 插件与 Quartz 插件使用兼容 1.4.0 的版本；旧版 Quartz 插件可能仍能渲染面板，但会忽略或错误解析这些值。版本适配进展请参见 [quartz-tabsdown issue #12](https://github.com/grafanaKibana/quartz-tabsdown/issues/12)。
