---
title: "我用 AI 搭了一个个人网站——全过程记录"
date: 2026-07-06
summary: "没有任何开发经验的产品经理，用 AI 对话 30 分钟搭建了一个完全免费的个人博客。"
---

## 起因

我想做一个个人网站，记录自己使用各种 AI 产品的实操心得。头几篇文章就把我自己用 AI 工具的真实操作过程发上去。网站要求 PC 和手机都能访问，一开始只有我自己发内容，其他人可以自由浏览。

我没有任何开发经验，是个产品经理。所以整个过程都是跟 AI（bluecode）对话完成的。

---

## 第一步：确定需求

AI 先问了我几个关键问题：

- **内容形式**：图文教程为主（大量截图 + 文字步骤说明）
- **技术背景**：我是产品经理，没有开发经验
- **发布方式**：希望简单免费
- **视觉风格**：极简，类似 Apple 风格
- **移动端**：不需要原生 App，响应式网页 + PWA（添加到手机主屏幕）就够了

## 第二步：选择技术方案

AI 给了我三个方案对比：

| 方案 | 说明 | 费用 |
|------|------|------|
| Hugo + GitHub Pages | 写 Markdown 自动生成网站 | 免费 |
| Astro + GitHub Pages | 更灵活但更复杂 | 免费 |
| Notion + NotionNext | 写作体验好但依赖第三方 | 0-$12/月 |

最终选了 **Hugo + GitHub + Cloudflare Pages**。理由：

- 完全免费
- 国内访问速度比 GitHub Pages 快（Cloudflare 有亚洲节点）
- 以后发文章只需要写 Markdown 然后 `git push`，网站自动更新

## 第三步：理解发布流程

这是以后每次发文章的流程：

```
1. 用文本编辑器写 Markdown 文件
2. 把截图放到文章同目录的 images/ 文件夹
3. 终端执行：git add . && git commit -m "发布：文章标题" && git push
4. 等 1-2 分钟，网站自动更新
```

原理很简单：
- **GitHub** = 文件仓库，存所有文章原稿
- **Cloudflare Pages** = 自动化工厂，监视仓库，一有变化就重新生成网站

## 第四步：确定设计

经过讨论，最终设计：

- **网站名**：逍遥游
- **首页**：列表流布局（左缩略图 + 右标题/摘要/日期）
- **文章页**：单栏居中 720px，图片全宽，点击可放大
- **导航**：只有"首页"和"关于"两个入口
- **无标签、无分类、无搜索**——极简到底

## 第五步：AI 自动搭建项目

确认设计后，AI 直接在我电脑上执行了所有搭建工作：

1. 安装 Hugo（静态网站生成器）
2. 创建项目目录结构
3. 编写 HTML 模板（首页、文章页、导航、页脚）
4. 编写 CSS 样式（极简风格、响应式适配）
5. 配置 PWA（让手机可以"添加到主屏幕"）
6. 写了一篇示例文章
7. 构建验证通过

整个过程大概 5 分钟，我只需要看着就行。

## 第六步：推送到 GitHub

这一步需要我自己操作。

首先在 GitHub 网站上创建了一个名为 `xiaoyaoyou` 的仓库，然后在终端执行：

```bash
cd ~/xiaoyaoyou
git remote add origin https://github.com/zmymmmm/xiaoyaoyou.git
git branch -M main
git push -u origin main
```

**踩坑**：GitHub 不再支持用密码推送代码，需要用 Personal Access Token。

解决方法：
1. 打开 https://github.com/settings/tokens
2. 生成一个 token（勾选 repo 权限）
3. 推送时密码栏粘贴这个 token

## 第七步：部署到 Cloudflare Pages

这一步在 Cloudflare 网站上操作：

1. 注册 Cloudflare 账号
2. 在首页点 "Create app"
3. **注意**：不要选 Worker！要找到页面底部 "Looking to deploy Pages? Get started" 链接
4. 选择 "Import an existing Git repository" → Get started
5. 连接 GitHub，选择 `xiaoyaoyou` 仓库
6. 构建设置：
   - Framework preset: Hugo
   - Build command: `hugo`
   - Build output directory: `public`
   - Environment variable: `HUGO_VERSION` = `0.147.0`
7. 点 "Save and Deploy"

等待 1-2 分钟，看到 "Success! Your project is deployed to Region: Earth"。

**我的网站地址**：https://xiaoyaoyou-3tq.pages.dev

## 踩坑总结

1. **GitHub 认证**：不能用密码，必须用 Personal Access Token
2. **Cloudflare 界面混乱**：Workers 和 Pages 入口合并了，很容易误入 Worker 创建流程。记住要找 "Pages" 的入口
3. **`cd` 命令要单独执行**：先进入目录，再执行后续命令

## 最终成果

- 一个完全免费的个人博客
- PC 和手机都能访问
- iPhone 上可以"添加到主屏幕"当 App 用
- 以后发文章只需要：写 Markdown → git push → 自动上线
- 整个搭建过程从零开始大约 30 分钟（包括注册账号和踩坑时间）

---

*这篇文章本身就是用这个流程发布的。*
