# 应急云仓数字化指挥中心 (Anlan Cloud Warehouse Dashboard)

这是一个基于 Vue 3 + Vite + ECharts + Tailwind CSS 开发的现代化大屏指挥中心项目。

## 📦 项目功能

- **资源一张网 (左侧面板)**:
  - 展示政府与社会化物资储备占比 (双环图)
  - 物资分类统计 (生活保障、医疗防护、防汛救灾)
  - 一云多仓列表 (支持区级/街道级切换，点击摄像头图标可查看监控)
- **GIS 地图指挥 (中间面板)**:
  - 基于 GeoJSON 的杭州地图可视化
  - 重点突出上城区核心区域
  - 展示各类储备点位 (政府、社会化、社区级、物流点)
  - 动态流向图展示物资调配路径
- **降本增效分析 (右侧面板)**:
  - 核心指标看板 (节约成本、实际调用金额)
  - 动态趋势图表 (使用量/水位切换)
  - 供应商信用评价体系

## 🚀 快速开始

### 1. 环境准备
确保你的电脑上已经安装了 [Node.js](https://nodejs.org/) (推荐 v16 或更高版本)。

### 2. 安装依赖
在项目根目录下打开终端，运行以下命令安装项目所需的依赖包：

```bash
npm install
```

### 3. 启动开发服务器
安装完成后，运行以下命令启动本地开发环境：

```bash
npm run dev
```

启动成功后，控制台会显示访问地址 (通常是 `http://localhost:5173`)，在浏览器中打开该地址即可预览。

### 4. 构建生产版本
如果需要部署上线，可以运行构建命令：

```bash
npm run build
```
构建产物将生成在 `dist` 目录下。

## 🛠️ 技术栈

- **前端框架**: [Vue 3](https://vuejs.org/) (Composition API)
- **构建工具**: [Vite](https://vitejs.dev/)
- **样式库**: [Tailwind CSS](https://tailwindcss.com/)
- **图表库**: [ECharts](https://echarts.apache.org/)
- **地图支持**: GeoJSON (本地离线地图数据)

## 📁 目录结构

```
src/
├── components/          # 业务组件
│   ├── charts/          # 图表组件 (双环图、趋势图)
│   ├── LeftPanel.vue    # 左侧面板
│   ├── CenterPanel.vue  # 中间地图面板
│   ├── RightPanel.vue   # 右侧面板
│   ├── Header.vue       # 顶部导航
│   └── ...
├── App.vue              # 根组件
└── main.js              # 入口文件
public/
└── hangzhou.json        # 杭州地图数据
```

## 📝 注意事项

- 本项目使用本地 GeoJSON 地图数据，无需申请地图 API Key 即可运行。
- 监控视频功能为模拟效果 (Mock)，点击摄像头图标即可体验。
