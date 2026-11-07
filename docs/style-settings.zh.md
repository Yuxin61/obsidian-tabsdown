> 本文档由机器翻译生成，仅供参考。

# Style Settings

Tabsdown 无需 [Style Settings](https://github.com/mgmeyers/obsidian-style-settings) 即可正常使用。安装后，可使用以下控制项：

| 分组 | 控制项 |
| --- | --- |
| 常规（General） | **尺寸（Size）：** 紧凑（Compact）或默认（Default）。**样式（Personality）：** 按钮（Button）、下划线（Underline）、分隔线（Separator）或轨道（Rail）。**溢出（Overflow）：** 滚动（Scroll）或换行（Wrap）。**配色（Palette）：** 主色（Primary）或次色（Secondary）。**强调色（Accent）：** 自定义颜色，未设置时使用主题强调色。**对齐（Alignment）：** 起始（Start）、居中（Center）或等宽（Equal width）。默认值：尺寸 = Default，样式 = Rail，溢出 = Scroll，配色 = Primary，对齐 = Equal width。 |
| 标签外观（Tab appearance） | 可选主题按钮描边（默认关闭）；圆角半径设为 Auto（当前主题的小圆角值）或自定义（Custom），范围 0–24 px（默认自定义值 4 px）；下划线粗细 1–8 px（默认 2 px）；下划线位置为 Auto、Top、Right、Bottom 或 Left。 |
| 布局（Layout） | 标签间距 0–48 px（默认 4 px）；水平内边距与内容间距 0–48 px（默认分别为 36 px 和 12 px）；水平内边距为默认/基准值，紧凑模式使用其三分之一；侧边列表宽度 192–320 px（默认 192 px）。 |
| 图标与标签（Icons and labels） | 图标大小 12–32 px（默认 16 px）；图标间距 0–16 px（默认 6 px）；选中标签字重：细（Thinner，400）、默认（Default，宽屏桌面 600 / 移动端及窄视口原生 Medium）或粗（Bolder，700）。 |
| 嵌套块（Nested blocks） | 扁平（Flat，默认）或卡片（Card）。嵌套标签默认使用次色（Secondary）配色，除非块本身覆盖该设置。 |
| 动效（Motion） | 动画速度 0–500 ms（默认 160 ms），以及禁用动画的开关。动画默认启用。 |

## 位置覆盖（Position overrides）

Top、Bottom、Left、Right 四个位置各自可覆盖样式（Personality）、配色（Palette）和对齐（Alignment）。Top 和 Bottom 默认继承 Rail；Left 和 Right 默认继承 Underline。**Inherit defaults** 可恢复全局设置。这些覆盖适用于围栏式 Markdown 块；通过 `mountTabs` 创建的标签使用全局设置。

已编写的块可直接通过右键菜单对应子菜单覆盖位置（Position）、溢出（Overflow）、密度（Density）、样式（Personality）、配色（Palette）和对齐（Alignment）。当前选项会被勾选，修改立即保存，每个 **Inherit** 条目会显示当前实际解析的值。优先级层叠顺序为：块覆盖 → 移动端/窄容器自动紧凑密度 → 位置覆盖 → 全局设置 → 内置回退值。嵌套块独立解析，`mountTabs` 使用全局外观设置并自动适配移动端尺寸。`%% htabs %%` 块不支持块级覆盖，其右键菜单仅显示添加（Add）、重命名（Rename）和删除（Delete）。

## 行为说明

- 下划线（Underline）Auto 模式：Top、Bottom 及挂载控件使用底边，Left 使用右边，Right 使用左边。
- 圆角半径默认为 Auto，跟随当前主题。Custom 模式将滑块值应用于每个 Button 行列的外角及 Rail 轨道。每个 Rail 标签的圆角为轨道圆角减去内边距，最小为零，以确保两种密度模式下间距一致。相邻 Button 角保留主题默认圆角。Underline 和 Separator 标签保持直角。
- Left 和 Right 标签在宽布局中始终填满侧边列表，在窄容器中变为内容上方的等宽行；Alignment 控制列表位置，不影响这种结构性尺寸。Equal width 搭配 Wrap 时保持完整行对齐，并扩展最后一行。
- 未配置的编写块在移动端始终为紧凑（Compact）模式，当其容器宽度不超过 28rem 时（包括桌面端分栏）也会变为紧凑模式。紧凑模式同时减小标签内边距和 Rail 轨道内边距。显式设置 Default 或 Compact 后不随设备或宽度变化。
- 通过 `mountTabs` 创建的标签在移动端以及视口宽度不超过 28rem 时同样变为紧凑模式。其动态面板保留原始包含块，包括固定定位的浮层。
- 移动端标签使用主题紧凑 UI 字体（`--font-ui-smaller`，默认移动端设置约 13px）和 Medium 选中字重，遵循 Obsidian 的首选文字缩放；已编写的粗体标签和显式 Thinner/Bolder 偏好会被保留。宽屏桌面标签保持 `--font-ui-small`。
- 移动端自动间距使用水平内边距设置的一半（默认 36px 设置下为 18px）。显式设置 Default 或 Compact 密度时保留各自的内边距。
- 触控/粗指针 Rail 最小总高度为 36px，标签宽度至少 44px。其他样式保持至少 44 × 44 px 的触控目标。Rail 无额外垂直轨道内边距，在文字较大时自动增高。
- 主题颜色、焦点样式和减少动效行为仍由 Obsidian 提供。

## 移动端尺寸参考

Obsidian 在其[字体排版参考文档](https://github.com/obsidianmd/obsidian-developer-docs/blob/main/en/Reference/CSS%20variables/Foundations/Typography.md)中推荐对控件使用 UI 字体变量，[移动端文字缩放](https://obsidian.md/changelog/2026-10-05-mobile-v1.14.4/)将笔记字体大小与系统首选缩放比例结合使用。紧凑标签角色是 Tabsdown 的设计选择，并非 Obsidian 已发布的分段控件规范。已安装的 Obsidian 1.15.0 CSS 对移动端标签预览标题使用 `--font-ui-smaller`，移动端按钮使用 Medium 字重，原生输入高度使用 `--touch-size-m`（44px）。该较小 UI token 在 16px 基准、缩放比例 1 时约为 12.8px，并随主题和无障碍设置动态响应。
