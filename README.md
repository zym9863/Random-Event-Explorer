# 随机事件探索器 (Random Event Explorer)

[English](./README_EN.md) | 简体中文

一个基于 Vue 3 + TypeScript + Vite 开发的交互式随机事件模拟与概率计算工具。

## 项目介绍

随机事件探索器是一个用于探索概率世界奥秘的交互式应用。通过大量模拟来观察随机事件的统计规律，帮助用户直观理解概率论的基本概念。

## 主要功能

### 随机事件模拟实验室

- 支持多种随机事件类型：抛硬币、掷骰子、抽牌
- 可自定义模拟次数
- 提供多种结果展示方式：
  - 模拟结果列表
  - 频率统计表格
  - 频率分布图表

### 概率计算器

- 提供常见概率计算工具
- 支持多种概率分布
- 直观展示计算结果

## 技术栈

- **前端框架**：Vue 3
- **开发语言**：TypeScript
- **构建工具**：Vite
- **样式**：CSS (Scoped CSS)

## 项目结构

```
src/
├── assets/        # 静态资源
├── components/    # 组件
│   ├── HelloWorld.vue
│   ├── ProbabilityCalculator.vue
│   └── RandomEventLab.vue
├── App.vue        # 应用主组件
├── main.ts        # 入口文件
└── style.css      # 全局样式
```

## 开发指南

### 环境要求

- Node.js 14.0+
- npm 或 yarn

### 安装依赖

```bash
npm install
# 或
yarn
```

### 开发服务器

```bash
npm run dev
# 或
yarn dev
```

### 构建生产版本

```bash
npm run build
# 或
yarn build
```

## 了解更多

- [Vue 3 文档](https://v3.vuejs.org/)
- [Vue 3 `<script setup>` SFC 文档](https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup)
- [Vue TypeScript 指南](https://vuejs.org/guide/typescript/overview.html#project-setup)
