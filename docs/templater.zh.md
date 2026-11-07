> 本文档由机器翻译生成，仅供参考。

# Templater 配方

[Templater](https://github.com/SilentVoid13/Templater) 会先将模板展开为普通 Markdown，Tabsdown 再通过 Obsidian 渲染生成的围栏式 `tabsdown` 块。Tabsdown 不检测 Templater、不在运行时调用其 API、不要求加载顺序，也不执行未展开的 Templater 表达式。

以下配方使用 Templater 官方文档中的 JavaScript 执行命令、输出变量 `tR` 以及 [`tp.system.prompt`](https://silentvoid13.github.io/Templater/internal-functions/internal-modules/system-module.html)。

## 静态模板

将以下内容保存为 Templater 模板文件：

~~~~markdown
````tabsdown
tab: Overview

Write the overview here.

tab: Example

```text
Nested fenced content is safe because the outer fence is longer.
```
````
~~~~

外层围栏必须比任何 tab 体内的围栏更长。对于嵌套的三反引号块，四个反引号即可；如果 tab 体内包含连续四个反引号，则外层需要用五个。

## 交互式提示模板

此模板先请求以逗号分隔的标签名，再逐一为每个 tab 体弹出多行输入框。若提供的标签少于两个、存在重复或为空，模板将拒绝执行。取消任意提示均不会插入不完整的 `tabsdown` 块。

模板会统计生成源码中所有连续反引号的最大长度，并将外层围栏设为比该长度多一个字符（最少四个）。

~~~~markdown
<%*
const labelsInput = await tp.system.prompt(
	"Tab labels, separated by commas",
	"Overview, Details",
	false,
	false,
	true,
);

if (labelsInput !== null) {
	const labels = labelsInput
		.split(",")
		.map((label) => label.trim())
		.filter(Boolean);
	const uniqueLabels = [...new Set(labels)];

	if (labels.length >= 2 && uniqueLabels.length === labels.length) {
		const tabs = [];
		let cancelled = false;

		for (const label of labels) {
			const body = await tp.system.prompt(
				`Markdown for "${label}"`,
				"",
				false,
				true,
			);

			if (body === null) {
				cancelled = true;
				break;
			}

			tabs.push(`tab: ${label}\n\n${body}`);
		}

		if (!cancelled) {
			const source = tabs.join("\n\n");
			const runs = source.match(/`+/g) ?? [];
			const longest = runs.reduce(
				(maximum, run) => Math.max(maximum, run.length),
				3,
			);
			const fence = "`".repeat(longest + 1);
			tR += `${fence}tabsdown\n${source}\n${fence}`;
		}
	}
}
%>
~~~~

每个提示的 `throw_on_cancel` 均为 `false`，因此取消时 Templater 返回 `null`。模板在追加到 `tR` 之前会检查该值，已收集的部分标签或内容不会被插入。

第一个提示为单行输入，通过第五个参数选中默认值。每个 tab 体的提示通过第四个参数启用多行输入。在此精简配方中，标签名不能包含逗号；如需逗号，请直接编辑生成的 Markdown。

## 使用生成的块

1. 通过 Templater 插入任一模板。
2. 确认结果是包含至少两个 tab 标记的普通围栏式 Markdown。
3. 在 Live Preview 中将光标移至块外，或切换到 Reading View。

如果 Templater 已禁用或未安装，已生成的 `tabsdown` Markdown 仍可正常工作。若模板表达式仍然可见，请运行 Templater 或手动替换该表达式；Tabsdown 刻意不对其求值。
