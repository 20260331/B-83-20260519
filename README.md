# wtxs

这是一个基于 Electron + Vue 3 + Element Plus + TypeScript 的桌面应用项目。

## 技术栈

- **Electron**: 桌面应用运行时
- **Vue 3**: 前端框架
- **Element Plus**: UI 组件库
- **TypeScript**: 静态类型检查
- **Vite**: 构建工具

## 目录结构

```
wtxs/
├── electron/          # Electron 主进程代码
│   ├── main.ts        # 主进程入口
│   └── preload.ts     # 预加载脚本
├── src/               # Vue 3 渲染进程代码
│   ├── assets/        # 静态资源
│   ├── components/    # Vue 组件
│   ├── App.vue        # 根组件
│   ├── main.ts        # 渲染进程入口
│   └── style.css      # 全局样式
├── public/            # 公共静态资源
├── dist-electron/     # Electron 构建产物
├── dist/              # 页面构建产物
├── package.json       # 项目配置文件
├── vite.config.mts    # Vite 配置文件
└── tsconfig.json      # TypeScript 配置文件
```


## 开发指南

### 安装依赖

```bash
npm install
```

### 启动开发环境

```bash
npm run dev
```

### 构建生产版本

```bash
npm run build
```
