# 国家药品不良反应监测平台大屏

这是一个基于 Vue 3、TypeScript、Vite、Tailwind CSS 4 和 AntV L7 构建的药品不良反应监测可视化大屏。项目面向监管态势展示场景，提供全国风险地图、预警事件流、分类趋势、患者结构、哨点网络和系统性能等模块。

## 技术栈

- Vue 3 + `<script setup lang="ts">`
- TypeScript
- Vite 6
- Tailwind CSS 4
- AntV L7 / L7 Maps
- Lucide Vue 图标
- pnpm 包管理

## 功能概览

- 全国药品不良反应监测地图：使用 AntV L7 展示省界、风险柱体和脉冲点位。
- 省份下钻联动：点击省界或点位后筛选右侧预警事件流。
- 实时预警流：支持按药品、症状、省份搜索，并按红、橙、黄风险等级过滤。
- 预警处置状态：支持从等待研判到综合处置、响应完成的状态流转。
- 趋势分析：展示近六个月化药、中药、器械、化妆品风险趋势，可切换曲线显示。
- 患者和症状结构：支持症状占比与年龄划分两类数据视图。
- 布局编辑：支持解锁后拖拽交换大屏组件位置，并可重置布局。
- 模拟预警：可一键插入模拟高危预警，验证地图和事件流联动。

## 快速开始

### 环境要求

- Node.js 20 或更高版本建议
- pnpm

### 安装依赖

```bash
pnpm install
```

### 本地开发

```bash
pnpm dev
```

默认开发服务监听 `0.0.0.0:3000`。浏览器访问：

```text
http://localhost:3000
```

### 类型检查

```bash
pnpm lint
```

当前 `lint` 脚本执行 `vue-tsc --noEmit`，用于 TypeScript 和 Vue 模板类型检查。

### 构建生产包

```bash
pnpm build
```

构建产物输出到 `dist/`。

### 预览构建产物

```bash
pnpm preview
```

### 清理构建产物

```bash
pnpm clean
```

## 环境变量

当前版本使用 AntV L7 内置 Map 适配器和远程 GeoJSON 省界数据，不再需要高德地图 token。

如果后续恢复高德底图，可参考 `.env.example` 增加：

```env
VITE_AMAP_KEY=""
VITE_AMAP_SECURITY_CODE=""
```

## 目录结构

```text
.
├── .codex/
│   └── skills/
│       └── daping-dev-standards/   # 本项目后续开发规范 skill
├── assets/                         # 静态素材
├── src/
│   ├── components/
│   │   ├── AlertStream.vue         # 预警事件流与处置状态
│   │   ├── BreakdownChart.vue      # 症状和年龄结构
│   │   ├── ChinaMap.vue            # AntV L7 全国风险地图
│   │   ├── StatsPanel.vue          # 哨点网络和系统支撑指标
│   │   └── TrendChart.vue          # 分类趋势折线图
│   ├── App.vue                     # 大屏主布局、数据状态和组件编排
│   ├── index.css                   # 全局样式、Tailwind 入口和字体
│   ├── main.ts                     # Vue 应用入口
│   └── types.ts                    # 共享业务类型
├── index.html
├── package.json
├── pnpm-lock.yaml
├── tsconfig.json
└── vite.config.ts
```

## 核心数据模型

共享类型集中在 `src/types.ts`：

- `Widget`：大屏组件布局元数据，包含组件 ID、标题、所属列和排序。
- `Alert`：药品风险预警事件，包含省份、药品、批号、风险级别、症状、时间和处置状态。
- `DiseaseData`：结构化统计项，用于症状占比和年龄分布。
- `TrendPoint`：分类趋势月度数据。
- `ProvinceNode`：省份风险节点，用于地图点位、告警数量和风险级别。

新增组件或业务数据时，应优先扩展这些共享类型，避免在组件内部重复声明不一致的数据结构。

## 开发规范

### 组件组织

- 页面级编排放在 `src/App.vue`。
- 可复用展示或交互模块放在 `src/components/`。
- 共享类型放在 `src/types.ts`。
- 组件 props 和 emits 必须使用 TypeScript 显式声明。
- 组件内部状态优先使用 `ref`、`computed`、`watch`，避免把派生状态写成可变数据。

### UI 和交互

- 保持大屏风格一致：深色底、青蓝主强调色、红橙黄风险色、紧凑信息密度。
- 新增按钮、筛选器、状态标识时优先使用 Lucide Vue 图标。
- 不要在紧凑面板内使用大字号标题或营销式文案。
- 固定格式模块要设置稳定尺寸约束，避免 hover、筛选、空状态造成布局跳动。
- 中文展示文案应面向监管业务场景，避免占位式或泛化描述。

### 地图和外部数据

- `ChinaMap.vue` 当前依赖 `https://geo.datav.aliyun.com/areas_v3/bound/100000_full.json` 获取省界。
- 地图加载失败时必须保留点位层或其他降级展示，避免主面板空白。
- 涉及地图层更新时，要注意 `onBeforeUnmount` 中销毁 L7 `Scene`，避免内存泄漏。

### 样式

- 使用 Tailwind CSS 工具类为主。
- 全局基础样式放在 `src/index.css`。
- 组件特有动画或深度选择器放在对应 `.vue` 文件的 `<style scoped>`。
- 不要引入新的 UI 框架，除非明确需要并同步更新 README 和开发规范 skill。

### 质量检查

每次完成代码改动后至少运行：

```bash
pnpm lint
```

涉及构建配置、依赖、地图渲染或生产发布时，再运行：

```bash
pnpm build
```

涉及视觉布局、响应式、地图或拖拽交互时，应启动本地服务并在浏览器中检查桌面和移动视口。

## 项目内 Codex Skill

本仓库包含项目专用 skill：

```text
.codex/skills/daping-dev-standards
```

后续让 Codex 修改本项目时，可以显式要求：

```text
使用 $daping-dev-standards 开发规范处理这个需求
```

该 skill 会提醒后续开发者优先遵守本项目的技术栈、目录边界、组件模式、视觉风格、地图降级和验证命令。
