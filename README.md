# WOT-UI Input Number Bug Demo

基于 uni-app 的 `wd-input-number` 组件 Bug 反馈演示项目

## 项目配置

- **Vue**: 3.5.27
- **uni-app**: 3.0.0
- **wot-design-uni**: 1.14.0
- **TypeScript**: 5.8.3
- **Node**: >= 18.12.0
- **pnpm**: >= 8.7.0

## 安装

```bash
# 安装依赖
pnpm install
```

## 开发

```bash
# H5 调试
pnpm dev:h5

# 微信小程序调试
pnpm dev:mp-weixin

# App 调试
pnpm dev:app
```

## 构建

```bash
# H5 构建
pnpm build:h5

# 微信小程序构建
pnpm build:mp-weixin

# App 构建
pnpm build:app
```

## 项目结构

```
.
├── src/
│   ├── pages/
│   │   └── index/
│   │       └── index.vue          # Bug 演示页面
│   ├── App.vue                    # 应用根组件
│   ├── main.ts                    # 应用入口
│   └── pages.json                 # 页面配置
├── package.json                   # 项目依赖
├── tsconfig.json                  # TypeScript 配置
├── vite.config.ts                 # Vite 配置
└── README.md                      # 项目说明
```

## 组件配置

```vue
<wd-input-number
  v-model="value"
  :precision="2"
  :step="0.01"
  :min="2"
  :immediate-change="false"
/>
```

## Bug 描述

该项目用于反馈 `wot-design-uni` 中 `wd-input-number` 组件的问题。
