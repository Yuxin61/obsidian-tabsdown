> 本文档由机器翻译生成，仅供参考。

# 从其他插件嵌入标签页

已拥有实时 DOM 面板的插件可以将这些面板传递给 Tabsdown，从而获得相同的样式和动画效果。Tabsdown 会将面板移入其布局中，不会克隆或以 Markdown 方式渲染它们。

```ts
interface TabsdownApi {
	mountTabs(
		container: HTMLElement,
		options: {
			label: string;
			selection?: string | null;
			tabs: readonly {
				id: string;
				label: string;
				panel: HTMLElement;
			}[];
			onSelectionChange?: (
				selection: string | null,
				previous: string | null,
			) => void;
		},
	): {
		readonly selection: string | null;
		setSelection(id: string | null): void;
		setAvailable(id: string, available: boolean): void;
		destroy(): void;
	};
}

const tabsdown = this.app.plugins.getPlugin("tabsdown") as TabsdownApi | null;
const tabs = tabsdown?.mountTabs(container, {
	label: "**Trace** and watch",
	selection: null,
	tabs: [
		{ id: "trace", label: "**Trace**", panel: traceElement },
		{ id: "watch", label: "`Watch`", panel: watchElement },
	],
	onSelectionChange(selection) {
		// null when the open panel was collapsed
	},
});

tabs?.setSelection("trace");
tabs?.setAvailable("watch", false);
tabs?.destroy();
```

挂载的标签页作为可折叠开关使用。除非传入初始 ID，否则选中状态从 `null` 开始；再次激活已打开的标签页会将其关闭。围栏式 `tabsdown` 块保持其正常的首标签选中行为。

- `destroy()` 会恢复面板原有的 `id`、`role`、`tabindex`、`hidden` 和 `aria-labelledby` 属性值，并将面板归还至 `container`。重复调用是安全的。禁用插件时，Tabsdown 也会销毁所有活动控制器。
- 按钮使用原生的 Enter、Space 和 Tab 行为。该组合不会被声明为标签列表，因为它可以在无选中状态下启动。若已聚焦的标签页变为不可用，焦点会移至下一个可用标签页。
- Tabsdown 会为需要名称的面板自动命名。带有 `aria-label` 或 `aria-labelledby` 的面板保留其原有属性；拥有自身控件的面板不会进入 Tab 键顺序，以确保其控件优先获得焦点。
- 调用 `destroy()` 后，Tabsdown 不会移动焦点。若销毁操作源于用户输入，请手动将焦点设置到替代控件上。
- `onSelectionChange` 在用户操作激活或某标签页变为不可用导致其面板关闭时触发。挂载时以及调用 `setSelection` 后不会触发；在回调内调用控制器方法是安全的。
- 挂载的标签页在移动端及视口宽度不超过 28rem 时使用紧凑间距。移动端标签文字遵循 Obsidian 的紧凑 UI 字体和文字缩放规则，默认设置下自动应用 18px 水平内边距。触控 Rail 最小高度为 36px，且标签页宽度至少 44px；其他样式保持 44 × 44 px 的触控目标。Rail 无额外垂直轨道内边距，并会随文字变大而扩展。宽屏桌面布局保持全局密度设置。
- 请挂载到文档内，以便 Tabsdown 能正确解析动画和减少动效样式。容器未挂载到文档时，将回退为固定动画时长。
- 请勿挂载到已渲染的 `tabsdown` 面板内部。Markdown 刷新可能替换该面板，从而在不销毁控件的情况下使其失效。
