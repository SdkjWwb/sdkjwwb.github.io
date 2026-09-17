---
title: Git 入门：博客备份与日常工作协作
date: 2026-09-17 14:52:13
categories: 学习
tags: [Git, 教程]
---

Git 不只是写博客时用来备份源码，平时做项目、和同事协作时同样离不开它。没有版本控制，改坏了难回退；多人改同一仓库，也容易互相覆盖。

本文分两条线：

- **个人博客**：本站源码在 `hexo` 分支，GitHub Pages 在 `master` 分支。
- **工作协作**：常见是从主分支拉功能分支，改完提 PR / Merge Request，合并后再上线。

---

## 什么是 Git

Git 是一个分布式版本控制系统，用来跟踪文件的变化。简单说，它就像给项目里的每个文件装了一台时光机——你可以回到任意历史版本，也能看清每次改了什么、谁改的、为什么改。

常见用途：

1. **本地留档**：每次提交都是一个可回退的快照。
2. **远程备份**：推到 GitHub / GitLab 后，换电脑也能把代码拉回来。
3. **团队协作**：多人并行开发，用分支隔离改动，用 PR 做代码评审再合并。

---

## 核心概念

| 概念 | 含义 | 博客例子 | 工作例子 |
| --- | --- | --- | --- |
| 仓库（Repository） | 被 Git 管理的项目目录 | `E:\hexo\blog` | 公司的业务仓库 |
| 提交（Commit） | 一次快照，记录本次改了什么 | 「新增文章：Git 入门」 | `fix: 修复登录态过期` |
| 分支（Branch） | 独立的开发线，互不干扰 | 源码 `hexo`，Pages `master` | `main` + `feature/登录页` |
| 远程仓库（Remote） | 托管在网上的仓库 | `SdkjWwb/sdkjwwb.github.io` | GitLab / GitHub 上的团队仓库 |
| 工作区 | 你正在编辑的文件 | 刚改完的 `git.md` | 刚改完的某个 `.java` / `.ts` |
| 暂存区 | 已 add、准备提交的改动 | `git add` 之后 | 同上 |

日常流程可以记成：

**工作区 → add → 暂存区 → commit → 本地仓库 → push → 远程仓库**

---

## 安装与首次配置

到 [https://git-scm.com](https://git-scm.com) 安装 Git。装完后确认：

```bash
git -v
```

第一次使用前，先配置姓名和邮箱（会出现在提交记录里；公司项目通常要求用公司邮箱）：

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

查看当前配置：

```bash
git config --list
```

---

## 常用命令

### 1. 初始化仓库

在项目目录下执行一次即可。已有远程仓库时，更常见是直接 `clone`，而不是本地再 `init`。

```bash
git init
```

### 2. 查看状态

最常用。随时看改了什么、哪些还没提交：

```bash
git status
```

### 3. 加入暂存区

```bash
git add .
git add source/_posts/git.md
```

第一条添加所有改动，第二条只添加某个文件。工作里也建议先 `status`，再按需 `add`，避免把本地调试文件一并提交。

### 4. 提交
```bash
git commit -m "新增文章：Git 入门"
```

说明尽量写清楚「做了什么」。工作里更常见带类型前缀，见下文「提交说明怎么写」。

### 5. 查看历史

```bash
git log --oneline
```

### 6. 关联远程仓库（第一次）

```bash
git remote add origin https://github.com/SdkjWwb/sdkjwwb.github.io.git
git remote -v
```

本站已经关联好了，一般只需 `git remote -v` 确认。公司项目通常是 `clone` 下来就已经带好 `origin`。

### 7. 推送到远程

```bash
git push -u origin hexo
git push
```

第一次推某个分支用 `-u` 建立跟踪；以后在同一分支上直接 `git push`。工作里推的往往是自己的功能分支，而不是直接推 `main`。

### 8. 克隆与拉取

把远程仓库拉到本地：

```bash
git clone -b hexo https://github.com/SdkjWwb/sdkjwwb.github.io.git blog
git clone https://github.com/公司组织/项目名.git
```

本地已有仓库，同步远程最新改动：

```bash
git pull
```

上班第一件事，很多团队习惯先 `git pull`（或先 `fetch` 再合并），再开始改代码。

### 9. 分支相关

```bash
git branch
git switch main
git switch -c feature/login-page
```

- `git branch`：看本地有哪些分支，当前在哪一条
- `git switch main`：切回主分支（有的仓库叫 `master`）
- `git switch -c ...`：新建并切换到新分支

旧写法 `git checkout` 也能切分支，新版本更推荐 `git switch`。

---

## 工作中怎么用 Git

### 团队协作日常流程

多数公司项目不会直接在 `main` / `master` 上改，而是：

1. 切到主分支，拉最新代码
2. 新建功能分支
3. 改代码、自测
4. 提交并推到远程
5. 在 GitHub / GitLab 提 Pull Request / Merge Request
6. 同事 Review 通过后合并进主分支

对应命令大致是：

```bash
git switch main
git pull
git switch -c feature/login-page
```

改完后：

```bash
git status
git add .
git commit -m "feat: 增加登录页"
git push -u origin feature/login-page
```

然后到网页上创建 PR。合并完成后，本地主分支再拉一次最新：

```bash
git switch main
git pull
```

### 分支怎么命名

常见约定（团队可能略有不同，以团队规范为准）：

| 前缀 | 用途 | 例子 |
| --- | --- | --- |
| `feature/` | 新功能 | `feature/login-page` |
| `fix/` | 修 bug | `fix/order-null` |
| `hotfix/` | 线上紧急修复 | `hotfix/pay-crash` |
| `chore/` | 杂项、工具、依赖 | `chore/upgrade-deps` |

### 提交说明怎么写

个人博客可以写「新增 Git 入门」。工作里更建议短句说清意图，很多团队用约定式提交：

```text
feat: 增加导出 Excel
fix: 修复登录态过期
docs: 补充接口说明
refactor: 拆分订单查询逻辑
chore: 更新依赖版本
```

原则是：**让别人只看提交记录，也知道这次改动解决了什么问题**。

### 多人协作注意点

1. **先拉再改**：动手前先 `git pull`，减少和别人的改动撞车。
2. **不要直接推主分支**：多数团队要求走 PR，主分支有保护规则。
3. **一个分支一件事**：登录页和修订单 bug 分开两条分支，Review 更清晰。
4. **出现冲突时**：`git pull` 或合并时 Git 会标出冲突文件。打开文件，处理 `<<<<<<<` / `=======` / `>>>>>>>` 标记，改完后再：

```bash
git add .
git commit -m "resolve: 合并冲突"
```

入门阶段先掌握「手动解决冲突再提交」即可；`rebase` 等进阶操作以后再学。

### 博客用法 vs 工作用法

| 场景 | 常见做法 |
| --- | --- |
| 个人博客 | 多在 `hexo` 上直接改、提交、`push` |
| 公司项目 | 功能分支开发 + PR 评审，合并进 `main` |
| 个人博客 | 自己对质量负责，流程可以简单 |
| 公司项目 | 还要考虑 Review、CI、发布节奏 |

命令是同一套，差别主要在**分支策略和协作流程**。

---

## 本站实际用法（博客）

本仓库约定：

| 分支 | 放什么 |
| --- | --- |
| `hexo` | Hexo 源码：文章、配置、主题 |
| `master` | `hexo deploy` 生成后的静态网页 |

改完文章或配置后，备份源码一般是：

```bash
git status
git add .
git commit -m "Backup hexo source"
git push origin hexo
```

如果当前已经在 `hexo` 且设置了上游，最后一步也可以简写成 `git push`。

---

## 和 Hexo 部署的关系

这两件事不要混：

| 操作 | 作用 |
| --- | --- |
| `git push origin hexo` | 把源码备份到 GitHub 的 `hexo` 分支 |
| `hexo deploy` | 把生成好的网页推到 `master`，更新站点 |

只 `git push` 不会更新 https://sdkjwwb.github.io 上的页面；只 `hexo deploy` 也不会把 `_posts` 源码备份到 `hexo` 分支。日常建议两边都做：

1. 本地 `hexo s` 预览没问题
2. `hexo clean && hexo g -d` 部署网页
3. `git add` → commit → `push origin hexo` 备份源码

---

## 注意

1. **先看状态再提交**：养成 `git status` 的习惯，确认暂存的文件是你想提交的。
2. **分清场景**：博客源码在 `hexo`；公司项目先弄清主分支叫 `main` 还是 `master`，以及能不能直接推。
3. **别提交不该提交的东西**：博客的 `.gitignore` 已忽略 `node_modules/`、`public/`、`db.json`；工作项目里也不要提交密钥、本地配置、构建产物。
4. **提交说明写清楚**：博客如「新增 Git 入门」；工作如 `fix: 修复登录态过期`。
5. **不要轻易 force push**：尤其是已经推到远程、别人可能基于它开发的分支。

---

## 小结

先记住这条主链路：

```bash
git status
git add .
git commit -m "说明这次改了什么"
git push
```

再按场景补两招：

- **博客**：在 `hexo` 分支备份源码，需要更新站点时再 `hexo deploy`
- **工作**：从 `main` 拉功能分支，推远程后提 PR，合并后再回到主分支 `pull`

把 `pull`、`switch`、`log --oneline` 也用熟，日常开发和协作就基本够用了。