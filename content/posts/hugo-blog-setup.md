---
title: "用 Hugo + Cloudflare Pages 搭建免费技术博客"
date: 2026-03-03
draft: false
tags: ["Hugo", "Cloudflare", "建站"]
categories: ["效率提升"]
summary: "手把手教你用 Hugo 静态网站生成器和 Cloudflare Pages 搭建一个免费、快速、支持 AdSense 的技术博客。"
---

## 为什么选择 Hugo + Cloudflare Pages

| 方案 | 费用 | 速度 | 维护难度 |
|------|------|------|----------|
| WordPress + 服务器 | ¥500+/年 | 中等 | 高 |
| Hugo + Cloudflare | 免费 | 极快 | 低 |
| Next.js + Vercel | 免费 | 快 | 中等 |

Hugo 是目前最快的静态网站生成器，配合 Cloudflare Pages 的全球 CDN，可以实现极致的访问速度。

## 搭建步骤

### 1. 安装 Hugo

```bash
# Windows (winget)
winget install Hugo.Hugo.Extended

# macOS
brew install hugo

# Linux
snap install hugo
```

### 2. 创建站点

```bash
hugo new site my-blog
cd my-blog
git init
```

### 3. 添加主题

推荐使用 PaperMod 主题，简洁美观且 SEO 友好：

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

### 4. 配置站点

编辑 `hugo.toml`，设置站点基本信息、主题参数和菜单。

### 5. 创建文章

```bash
hugo new posts/my-first-post.md
```

用 Markdown 编写内容，Hugo 会自动将其转换为静态 HTML。

### 6. 本地预览

```bash
hugo server -D
```

打开 `http://localhost:1313` 即可预览。

### 7. 部署到 Cloudflare Pages

1. 将代码推送到 GitHub
2. 登录 Cloudflare Dashboard → Pages
3. 连接 GitHub 仓库
4. 构建设置：命令 `hugo`，输出目录 `public`
5. 绑定自定义域名

每次 `git push`，Cloudflare 会自动构建部署。

## 日常维护

写文章的工作流非常简单：

```
写 Markdown → git add → git commit → git push
```

推送后 Cloudflare 自动构建，几十秒就能上线。

## 总结

Hugo + Cloudflare Pages 是目前性价比最高的技术博客方案，零成本、高性能、低维护。

---

*下一篇将介绍如何为 Hugo 博客接入 Google AdSense 广告。*
