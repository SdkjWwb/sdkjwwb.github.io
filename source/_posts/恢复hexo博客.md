---
title: 恢复 Hexo 博客
date: 2026-09-17 12:38:26
categories: 学习
tags: [Hexo, 教程]
sticky: 1
---

换电脑、重装系统，或本地目录弄丢之后，可以按下面两种方式把博客找回来。

- **方法一**：全新环境从零安装 Hexo，再装主题、改配置、部署。适合第一次搭建，或 GitHub 上没有源码备份。
- **方法二**：从 GitHub 的 `hexo` 分支把源码拉回来。本站已经把源码备份在仓库里，**优先用这种方法**。

本站当前信息，方便对照：


| 项目      | 值                                                                             |
| ------- | ----------------------------------------------------------------------------- |
| 站点地址    | [https://sdkjwwb.github.io](https://sdkjwwb.github.io)                        |
| 仓库      | [SdkjWwb/sdkjwwb.github.io](https://github.com/SdkjWwb/sdkjwwb.github.io.git) |
| 源码分支    | `hexo`                                                                        |
| 部署分支    | `master`（GitHub Pages 实际访问的静态文件）                                              |
| Hexo 版本 | 8.x                                                                           |
| 主题      | [hexo-theme-fluid](https://github.com/fluid-dev/hexo-theme-fluid) 1.9.9       |


---



## 方法一：全新环境从零搭建



### 1. 安装 Node.js 和 Git

Hexo 依赖 Node.js，主题和部署都依赖 Git。

- Node.js：到 [https://nodejs.org](https://nodejs.org) 安装 LTS 版本。装完后确认：

```bash
node -v
npm -v
```

- Git：到 [https://git-scm.com](https://git-scm.com) 安装。装完后确认：

```bash
git -v
```

Windows 上建议安装时勾选把 Git 加入 PATH，之后在 PowerShell 或 Git Bash 里都能用。

### 2. 安装 Hexo CLI

```bash
npm install hexo-cli -g
hexo -v
```

注意包名是 `hexo-cli`，中间没有空格。不要写成 `hexo -cli`。

### 3. 初始化站点

```bash
hexo init blog
cd blog
npm install
```

`hexo init` 会生成站点骨架，包括 `_config.yml`、`source/_posts/`、`themes/` 等。

本仓库已经是初始化好的站点，换机后一般不需要再执行 `hexo init`，直接看方法二。

### 4. 安装 Fluid 主题

```bash
cd themes
git clone https://github.com/fluid-dev/hexo-theme-fluid.git hexo-theme-fluid
```

克隆完成后，编辑站点根目录的 `_config.yml`，把主题改成：

```yaml
theme: hexo-theme-fluid
```

主题自己的配置在 `themes/hexo-theme-fluid/_config.yml`。站点标题、链接、部署等改根目录的 `_config.yml`；外观、导航、评论等改主题配置。

### 5. 调整站点配置

根目录 `_config.yml` 至少确认这几项：

```yaml
title: 我的博客
author: wangwenbiao
language: zh-CN
timezone: Asia/Shanghai
url: https://sdkjwwb.github.io

theme: hexo-theme-fluid

deploy:
  type: git
  repo: https://github.com/SdkjWwb/sdkjwwb.github.io.git
  branch: master
```

部署插件需要单独安装：

```bash
npm install hexo-deployer-git --save
```



### 6. 本地预览

```bash
hexo clean
hexo generate
hexo server
```

浏览器打开 [http://localhost:4000](http://localhost:4000)。`hexo server` 也可以简写成 `hexo s`。

### 7. 部署到 GitHub Pages

```bash
hexo deploy
```

这条命令会把 `public/` 推到仓库的 `master` 分支。源码继续留在 `hexo` 分支，不要把源码和生成后的静态文件混在同一个分支里。

---



## 方法二：从 GitHub 备份恢复（推荐）

本仓库已经把 Hexo 源码备份在 `hexo` 分支上，换机后按下面做即可。

### 1. 克隆源码分支

```bash
git clone -b hexo https://github.com/SdkjWwb/sdkjwwb.github.io.git blog
cd blog
```

如果已经克隆了仓库但当前在 `master` 上，切换到源码分支：

```bash
git checkout hexo
```



### 2. 安装依赖

```bash
npm install
```

`package.json` 里已经包含 Hexo 8、渲染器、以及 `hexo-deployer-git`。一般不需要再全局安装一遍 `hexo-cli`，用项目本地的命令即可：

```bash
npx hexo version
```



### 3. 确认主题还在

主题目录应是 `themes/hexo-theme-fluid`。如果这个目录是空的，重新克隆一次：

```bash
git clone https://github.com/fluid-dev/hexo-theme-fluid.git themes/hexo-theme-fluid
```

然后确认根目录 `_config.yml` 里是：

```yaml
theme: hexo-theme-fluid
```



### 4. 预览和重新部署

```bash
npx hexo clean
npx hexo s
```

确认本地没问题后再部署：

```bash
npx hexo generate
npx hexo deploy
```

部署只更新 `master` 上的静态页面。文章、配置、主题这些源码改完后，还要提交并推送到 `hexo` 分支，下次换机才能继续用方法二恢复：

```bash
git add .
git commit -m "Backup hexo source"
git push origin hexo
```

---



## 文章头部（Front-matter）

每篇文章开头必须用两行 `---` 包住 YAML。中间是元数据，后面才是正文。推荐这样写：

```yaml
---
title: 我的第一篇文章
date: 2025-08-16 15:00:00
categories: 学习
tags: [Hexo, 教程]
sticky: 1
---
```

本站用的是 Fluid 主题，有几个常见字段和 Butterfly 不一样，直接照搬会不生效：

| 字段 | 作用 | 本站能不能用 |
| --- | --- | --- |
| `title` / `date` / `categories` / `tags` | 标题、时间、分类、标签 | 能，Hexo 自带 |
| `sticky: 1` | 首页置顶，数字越大越靠前 | 能。Fluid 认的是 `sticky`，不是 `top` |
| `top: true` | 部分主题的置顶写法 | 本站无效 |
| `abbrlink: 1234` | 固定短链接 | 需要先装 `hexo-abbrlink`，目前没装 |
| `password: 123` | 文章加密 | 需要先装 `hexo-blog-encrypt`，目前没装 |

`tags: [Hexo, 教程]` 这种一行写法是合法 YAML。不要漏掉 front-matter 结尾那一行 `---`，否则 Hexo 会把后面的表格、标题都当成 YAML，直接报错。

新建文章：

```bash
hexo new "文章标题"
```

生成的文件在 `source/_posts/` 里，把头部改成上面的格式，再写正文即可。

---

## 常用命令


| 命令                | 作用               |
| ----------------- | ---------------- |
| `hexo new "文章标题"` | 新建文章             |
| `hexo s`          | 本地预览             |
| `hexo g`          | 生成静态文件           |
| `hexo d`          | 部署到 GitHub Pages |
| `hexo clean`      | 清除缓存和 `public/`  |


`hexo g` 和 `hexo d` 可以连写：

```bash
hexo clean && hexo g -d
```

---



## 注意

1. **分清两个分支**：`hexo` 放源码，`master` 放生成后的网页。恢复博客时一定要检出 `hexo`。
2. **主题路径**：是 `themes/`，不是 `theme/`。
3. **front-matter 必须闭合**：开头和元数据结束后各有一行 `---`。`tags: [Hexo, 教程]` 这种写法可以，但结尾的 `---` 不能少。
4. **先本地预览再部署**：`hexo s` 没问题后再 `hexo d`，避免把坏掉的页面推上网。

