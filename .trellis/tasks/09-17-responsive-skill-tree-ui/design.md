# 技术设计：响应式技能树界面

## 设计目标

只调整界面布局状态和视觉层级。

不改动学习进度状态、技能树数据和存档协议。

## 现有边界

- 页面全部位于 `index.html`。
- CSS、HTML 和 JavaScript 都以内嵌方式组织。
- `.app` 使用 CSS Grid 包含 `.sidebar` 和 `.main`。
- `.workspace` 使用 Flex 包含 `.center` 和 `.detail`。
- `renderSidebar`、`renderMap`、`renderLibrary`、`renderJournal` 和 `renderDetail` 负责渲染内容。
- `wireEvents` 负责集中绑定页面事件。
- 移动端已有 `#mobileMenu`、`#mobileBackdrop` 和 `#openDetail`。
- 移动端详情面板已有 `.detail.open` 抽屉状态。

## 布局方案

### 左侧导航

- 复用 `#mobileMenu` 作为统一导航入口。
- 桌面端显示该按钮，并切换 `.app.sidebar-collapsed`。
- 桌面端收起时将 Grid 左侧轨道收为零宽。
- 桌面端收起时保留主内容顶部的展开按钮。
- 手机端保持固定宽度抽屉和遮罩逻辑。
- 桌面端不使用 `localStorage` 保存收起状态。
- 刷新页面后依靠默认 DOM 状态恢复展开布局。

### 右侧详情

- 复用 `#openDetail` 作为详情面板开关。
- 在详情头部保留关闭按钮，并扩展为桌面端可见。
- 大于现有详情抽屉断点时，使用 `.detail-collapsed` 隐藏详情列。
- 详情列收起后，中间区域自动获得剩余宽度。
- 不大于现有详情抽屉断点时，继续使用 `.detail.open` 的覆盖层动画。
- 开关按钮根据当前状态同步 `aria-label`、`title` 和 `aria-expanded`。

### 窄屏排版

- 保留现有 `1030px` 和 `730px` 断点。
- 调整顶部标题、统计、视图导航和地图工具栏的弹性布局。
- 允许工具栏在窄屏换行，避免搜索框和操作按钮互相挤压。
- 保持地图容器的独立滚动和缩放行为。
- 保持教材网格、任务网格和日志表单的窄屏单列退化。
- 为新增和现有图标按钮统一保留可操作尺寸。

## 状态与数据流

界面状态只存在于 DOM class 和少量运行时变量中。

```text
用户点击布局按钮
        ↓
切换 app/sidebar/detail 的界面 class
        ↓
同步 aria-label、title、aria-expanded
        ↓
CSS 改变可见区域
```

- 不调用 `save`。
- 不调用 `checkpoint`。
- 不修改 `state`。
- 不触发 `render`，避免重置地图滚动位置和详情滚动位置。
- 现有节点选择、地图缩放和内容渲染继续使用原逻辑。

## 事件设计

- 新增小型状态同步函数，集中处理两个面板的按钮文案和无障碍属性。
- `#mobileMenu` 在手机端打开导航抽屉，在桌面端切换左侧导航收起状态。
- `#mobileBackdrop` 只负责关闭手机端导航抽屉。
- `#openDetail` 在所有视口切换详情面板状态。
- 详情中的 `data-action="detail-close"` 调用统一关闭函数。
- Escape 关闭手机端导航抽屉和详情抽屉。
- 窗口尺寸变化只刷新布局状态，不重置内容状态。

## 兼容与风险

### 兼容性

- 不修改 `validateState` 的白名单字段。
- 不修改 `defaultState`、`save`、`load` 和导入导出逻辑。
- 不引入新的资源、依赖或网络请求。
- 不改变现有 CSS 颜色语义和中文文案。

### 风险控制

- 详情隐藏只能改变面板可见性，不能清空选中节点。
- 导航收起只能改变 Grid 轨道和抽屉状态，不能触发视图切换。
- 响应式规则必须覆盖详情收起、手机抽屉和键盘 Escape 三种状态。
- `prefers-reduced-motion` 下复用现有全局降运动规则，并避免新增强制动画。

## 验证策略

- 使用浏览器检查 375×812、768×1024、1280×800 和 1440×900 视口。
- 检查页面宽度、侧栏收起、详情收起、抽屉遮罩和键盘关闭行为。
- 检查三个视图的导航和主要操作。
- 使用页面已有的 `window.XianTu` 接口检查选择和状态未被布局操作改变。
- 使用 `node --check` 检查提取后的内嵌 JavaScript 语法。
