> 本文档由机器翻译生成，仅供参考。

# CSS 代码片段

打开 **Settings → Appearance → CSS snippets**，点击文件夹图标，创建 `tabsdown.css`。只需添加你想修改的规则：

```css
.tabsdown {
	--tabsdown-gap: 0.5rem;
	--tabsdown-resolved-radius: 999px;
	--tabsdown-content-spacing: 1rem;
	--tabsdown-horizontal-padding: 1.5rem;
}

.tabsdown__tab {
	background-color: var(--background-secondary);
	color: var(--text-muted);
}

.tabsdown__tab[aria-selected="true"] {
	background-color: var(--interactive-accent);
	color: var(--text-on-accent);
}
```

上方背景色规则针对 **Button** 外观风格。如果已安装 Style Settings，请先前往 **Settings → Style Settings → Tabsdown → Personality → Button** 再使用这些规则。

`--tabsdown-resolved-radius` 直接覆盖每个 Button 行列外角以及 Rail 轨道的实际圆角半径。Rail 标签页的圆角半径由该值减去轨道内边距计算得出，最小为零。相邻 Button 角保持主题的 `--radius-s`。Underline 和 Separator 标签页始终保持直角。若要改用 `--tabsdown-radius`，请在 Style Settings 的圆角半径选项中选择 **Custom**。

`--tabsdown-horizontal-padding` 接受 CSS 长度值，如 `px` 或 `rem`。它是默认/基准值；Compact 密度模式会取其三分之一，因此 `1.5rem` 在 Compact 模式下变为 `0.5rem`。

保存文件后，返回 **CSS snippets**，点击 **Reload snippets** 并启用 `tabsdown`。更换主题后可能需要重新调整覆盖规则。
