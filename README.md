# Vue-Bits Skill

基于 [vue-bits.dev](https://vue-bits.dev) 极简创意设计哲学的 Vue 3 组件开发技能。专为消除 AI 生成前端代码的模板化、同质化问题而设计。

## 设计哲学

- **少即是多** — 移除一切不必要的装饰元素
- **沉浸式交互** — 组件响应鼠标、滚动，让页面"活"起来
- **精致的克制** — 动画服务于内容，是微妙的视觉增强，而非喧宾夺主
- **暗色优先** — 大多数组件内置 `dark` prop，无缝配合 Tailwind `dark:` 类
- **高性能** — 基于 `requestAnimationFrame`，GPU 加速，零依赖或极简依赖

## 组件分类

### 动画 (Animations)
| 组件 | 描述 |
|------|------|
| Splash Cursor | 鼠标驱动的流体色彩扩散 (WebGL 流体模拟) |
| Text Scramble | 终端风格字符随机化渐变为目标文本 |
| Profile Card | 3D 卡片沿椭圆轨道旋转，带物理惯性 |
| Infinite Menu | 圆形按钮点击后弹性分裂为多个子项 |
| Circular Gallery | 元素排列于圆形轨道，拖拽旋转，支持自动滚动 |
| AnimatedContent | 内容区域的进入/离开过渡动画 |

### 文本动画 (Text Animations)
| 组件 | 描述 |
|------|------|
| Curved Loop | 文字沿 SVG `<textPath>` 曲线路径无限滚动 |
| SplitText | 逐字/逐词飞入动画 (GSAP 风格，可配置交错延迟) |
| Text Pressure | 压力/变形文本效果 |
| Neon | 霓虹灯管发光文本，可配置颜色和闪烁 |
| Typewriter | 逐字符打字效果 |
| Kaleidoscope | 万花筒式文本变换 |
| Flow Light | 流光扫过式跑马灯文本效果 |

### 背景 (Backgrounds)
| 组件 | 描述 |
|------|------|
| Pixel Blast | 深层粒子宇宙，像素簇呼吸/聚合，鼠标响应 |
| Grid Distortion | 网格扭曲/变形，涟漪式图案扰动 |
| Hyperspeed | 赛博朋克隧道穿越，滚轮透视拉伸 |
| Silk | 丝绸般流畅的动态背景 |
| Sparkles | 星光粒子闪烁背景，可配置密度和颜色 |
| Neon Grid | 霓虹灯管质感网格背景 |
| Ripple | 涟漪扩散背景 |
| FaultyTerminal | 老式终端 CRT 闪烁/故障效果背景 |

### UI 组件
| 组件 | 描述 |
|------|------|
| GradientButton | 渐变按钮，hover 缩放，可配置颜色断点 |
| Infinite Menu | 无限滚动菜单 |
| Circular Gallery | 圆形图片画廊 |
| Profile Card | 3D 个人资料卡片 |

## 触发方式

当你向 Claude Code 描述以下任意需求时，此技能将**自动激活**：

- 开发任何 Vue 前端组件
- 提到关键词：动画、动效、文本动画、背景、组件、UI
- 优化现有前端界面或组件
- 任何与前端视觉呈现或交互效果相关的请求

## 工作流程

技能激活后遵循严格的流程：

1. **停止所有代码生成** — 不会立即输出代码
2. **设计偏好询问** — 通过标准问卷了解你的需求：
   - 视觉风格偏好（极简现代、赛博朋克、复古怀旧、玻璃拟态等）
   - 动画效果类型（无限循环、悬停触发、滚动触发等）
   - 交互功能需求（可拖拽、响应式、深色模式等）
   - 配色方案和字体要求
3. **确认后开发** — 仅在你明确回复偏好后才开始编写组件

## 质量标准

### 设计准则
- 严格遵循 vue-bits.dev "少即是多" 的设计哲学
- 动画稳定在 60fps，避免生硬过渡和过度动效
- 配色满足 WCAG 无障碍对比度标准
- 组件高度可定制，Props 接口清晰
- 默认支持暗色模式

### 代码规范
- Vue 3 Composition API + `<script setup>` 语法
- 完整 TypeScript 类型定义
- CSS 自定义属性用于主题定制
- 使用 `requestAnimationFrame` 替代 `setInterval`
- GPU 加速属性优先：`transform`、`opacity`
- 支持 `prefers-reduced-motion` 媒体查询

### 通用 Props 模式

```typescript
interface VueBitsComponentProps {
  // 视觉
  colorFrom?: string       // 渐变起始色
  colorTo?: string         // 渐变结束色
  dark?: boolean           // 暗色模式开关
  // 动画
  duration?: number        // 动画时长 (秒)
  delay?: number           // 交错延迟 (秒)
  ease?: string            // 缓动函数
  threshold?: number       // IntersectionObserver 阈值 (0-1)
  // 行为
  paused?: boolean         // 暂停动画
  repeat?: number | 'indefinite'
  // 内容
  text?: string            // 文本内容
  className?: string       // 额外 CSS 类
}
```

### CSS 自定义属性约定

```css
.component-root {
  --vb-color-from: #6366f1;
  --vb-color-to: #a855f7;
  --vb-duration: 1.5s;
  --vb-delay: 0s;
  --vb-ease: cubic-bezier(0.16, 1, 0.3, 1);
  --vb-font-size: 1rem;
  --vb-gap: 0.5rem;
}
```

## 性能优化检查清单

- `requestAnimationFrame` 而非 `setInterval`
- 仅动画化 GPU 属性：`transform`、`opacity`、`filter`
- 谨慎使用 `will-change`，动画结束后移除
- 使用 `contain: layout style paint` 隔离重绘范围
- `IntersectionObserver` 暂停屏幕外动画
- 响应 `prefers-reduced-motion` 用户偏好
- 简单路径动画优先使用 SVG（可访问性更好）
- 使用 `requestAnimationFrame` 作为自然的 resize/mousemove 节流

## 禁止行为

- **绝不**在询问用户偏好前生成组件代码
- **绝不**生成模板化、千篇一律的 AI 风格代码
- **绝不**使用过于复杂或晦涩的代码结构
- **绝不**添加用户未明确要求的功能
- **绝不**输出不完整或无法运行的代码
- **绝不**使用 `<marquee>` 等已废弃的 HTML 元素
- **绝不**硬编码颜色 — 始终使用 CSS 自定义属性或 Props
- **绝不**跳过 TypeScript 类型定义
- **绝不**动画化会触发布局的属性 (`width`、`height`、`margin` 等)

## 文件结构

```
vue-bits/
├── SKILL.md                            # 技能定义与完整规则
├── README.md                           # 本文件
└── reference/
    └── implementation-patterns.md      # 核心实现模式参考
```

## 许可

本技能遵循 [vue-bits.dev](https://vue-bits.dev) 的设计哲学，用于在 Claude Code 中构建高质量 Vue 3 组件。
