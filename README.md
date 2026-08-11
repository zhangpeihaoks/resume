# 张沛豪 · 简历（Firefly 主题）

静态简历站点，部署到 **Cloudflare Pages / Workers Static Assets**。

## 目录结构

```
resume/
├── public/                # ← 部署目录（wrangler assets.directory）
│   ├── index.html         # 简历页面（含内联 CSS + 本地字体 @font-face）
│   └── fonts/             # 8 个 woff2（Space Grotesk / IBM Plex Sans / JetBrains Mono，latin 子集）
├── wrangler.jsonc         # 部署配置：assets.directory = "public"
├── package.json
└── .gitignore
```

> 仓库根目录的 `index.html` / `fonts/` 已迁移进 `public/`。**不要在根目录放待部署文件**，否则会被 `node_modules` 一起算进资源目录。

## 为什么用 public/ 隔离

Cloudflare 构建流程：`npm clean-install` → `npx wrangler deploy`。
`wrangler` 会把 `assets.directory` 指向的目录**整个**当作静态资源上传，单文件上限 **25 MiB**。
若 `assets.directory` 是仓库根 `.`，则 `node_modules/`（含 wrangler 自带的 122 MiB `workerd` 二进制）会被一并打包 → `Asset too large` 报错。
把静态文件收进 `public/` 后，`node_modules` 再大也不参与上传，彻底规避。

## 部署（Cloudflare Pages）

1. 仓库已连接 Cloudflare Pages，构建命令为 `npx wrangler deploy`。
2. `wrangler.jsonc` 已设 `assets.directory: "public"`，构建机只会部署 `public/` 下的文件（≈196 KB）。
3. push 到 `main` 即触发重新部署。
4. 自定义域：`resume.zhangpeihao.top`（在 CF Pages 项目里添加，按提示加 CNAME，CF 免费发证书）。

本地预览：`npm run preview`（需本机装过 `wrangler`）。

## 头像

`index.html` 中头像走 Gitee 图床 URL（`raw.giteeusercontent.com/quajiu/imgs/...`）。
若图床限流导致照片不显示，把证件照放到 `public/证件照.png` 并把 `img src` 改为 `./证件照.png` 即可。

## 字体

西文字体已用 `@fontsource` 提取 woff2 进 `public/fonts/`，`@font-face` 本地引用，不依赖 Google Fonts（国内 HR 打开也不会掉字体）。
中文字形走系统字体回退（PingFang SC / 微软雅黑 / 思源黑体）。
