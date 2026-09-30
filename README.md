# 基于 GitHub 的 Markdown 博客系统

用 **GitHub 仓库里的 Markdown 文件** 写文章，**GitHub Pages** 托管，**GitHub Discussions + giscus** 做评论。
没有服务器、没有数据库、没有后台，全部托管在 GitHub 免费额度内。

---

## 一、方案总览

```
        你写的东西                    自动发生的事                      读者看到的
┌──────────────────────┐    ┌──────────────────────────┐    ┌─────────────────────┐
│ content/posts/*.md   │    │  GitHub Actions          │    │  GitHub Pages       │
│  (Markdown 文件)      │───▶│  hugo --minify           │───▶│  https://<用户名>    │
│  git push 到 main     │    │  产物上传为 Pages artifact │    │   .github.io        │
└──────────────────────┘    └──────────────────────────┘    └──────────┬──────────┘
                                                                       │
                                                          页面底部 giscus 脚本
                                                                       │
                                                          每个页面 ⇄ 一个
                                                    GitHub Discussions 讨论帖
```

### 技术选型

| 环节 | 方案 | 为什么 |
| --- | --- | --- |
| 内容源 | 仓库内 Markdown | 你要求的；顺便获得版本历史、diff、协作能力 |
| 静态生成器 | **Hugo**（extended） | 构建毫秒级，主题生态最成熟，一个二进制即全部 |
| 主题 | **PaperMod**（git submodule） | 自带归档/搜索/目录/暗色模式/代码高亮，开箱即用 |
| 构建 | **GitHub Actions** | 免费，push 即触发，无需本地环境也能发布 |
| 托管 | **GitHub Pages** | 纯静态、免运维、免费 HTTPS |
| 评论 | **giscus** | 数据存在 GitHub Discussions，身份用 GitHub 账号，天然防垃圾 |
| 搜索 | Fuse.js（主题内置） | 纯前端，构建时生成 `index.json` |

> **关于「Issue」的说明**：giscus 用的是 GitHub **Discussions** 而不是 Issues。
> Discussions 是 GitHub 后来推出的、专门用于社区讨论的功能（Issues 原本是为缺陷跟踪设计的）。
> 评论区本质仍是「GitHub 上一个可公开讨论的帖子」，登录、@、表情、引用、Markdown 全套语法都在。
> 若你必须严格落在 Issues 上，参见文末「改用 utterances」。

### 成本 / 限制

- 费用：0 元（GitHub Pages 公开仓库无流量/构建费用）
- 私有仓库：Pages 需 Pro，且 giscus 要求评论载体仓库公开 → **建议仓库设为 public**
- 单文件/仓库体积上限：Pages 站点 1 GB，单次构建 10 分钟（本站几秒）
- 国内访问：`*.github.io` 可能不稳定，可绑定自定义域名 + CDN（可选）

---

## 二、目录结构

```
blog/
├── .github/workflows/hugo.yaml      # CI：push 后自动构建并发布到 Pages
├── .gitmodules                      # 记录 PaperMod 主题子模块
├── .gitignore                       # 忽略 public/ 等构建产物
├── hugo.yaml                        # 站点总配置（★ 你平时只需要改这个文件的几处）
├── README.md                        # 本文件
├── archetypes/
│   └── default.md                   # 新建文章的 front matter 模板
├── assets/css/extended/
│   └── custom.css                   # 自定义样式（PaperMod 自动加载）
├── content/                         # ★★ 你的工作区：只在这里写 md
│   ├── about.md                     # 关于页
│   ├── archives.md                  # 归档页（layout: archives）
│   ├── search.md                    # 搜索页（layout: search）
│   └── posts/                       # 全部博文
│       ├── hello-world.md           # 示例文章
│       └── markdown-cheatsheet.md   # 示例：Markdown/Shortcode 速查
├── layouts/_partials/
│   └── comments.html                # giscus 评论区（读 hugo.yaml 的 params.giscus）
├── static/                          # 原样拷贝到站点根目录的资源
│   └── images/                      # 放图片，引用写 /images/xxx.png
└── themes/PaperMod/                 # 主题（git submodule，不要手动改这里的文件）
```

**核心心智模型**：除 `content/` 和 `hugo.yaml` 外，其它都是「配置好就不用再碰」的基础设施。

---

## 三、上线步骤（一次性，约 10 分钟）

### 步骤 1：创建仓库

在 GitHub 新建仓库，名字必须是：

```
<你的用户名>.github.io
```

> 例：用户名 `alice` → 仓库名 `alice.github.io`。这是 GitHub 的「User Pages」约定，
> 这样站点地址就是干净的 `https://alice.github.io/`，不需要任何子路径配置。
> 仓库设为 **Public**（否则 Pages 与评论都不可用）。

### 步骤 2：推送本地代码

```bash
cd /home/upi/Project/ai_project/blog
git add .
git commit -m "chore: 初始化 Hugo 博客"
git remote add origin git@github.com:<你的用户名>/<你的用户名>.github.io.git
git push -u origin main
```

> 注意 `themes/PaperMod` 是子模块，`git add .` 会同时提交 `.gitmodules` 与 gitlink 记录。
> Actions 里的 `submodules: recursive` 会把它拉下来，不需要额外操作。

### 步骤 3：开启 Pages

仓库 → **Settings → Pages → Build and deployment → Source** 选择 **GitHub Actions**。

推送后 **Actions** 标签页能看到 `Deploy Hugo site to Pages` 流程跑完，
访问 `https://<你的用户名>.github.io/` 即可看到站点。

### 步骤 4：替换占位符

全局搜索替换 `USERNAME`（共 5 处，都在 `hugo.yaml` 和 `content/about.md`）：

| 文件 | 字段 |
| --- | --- |
| `hugo.yaml` | `baseURL` |
| `hugo.yaml` | `params.author`、`params.description`、`params.title` |
| `hugo.yaml` | `params.socialIcons[].url` |
| `hugo.yaml` | `params.editPost.URL` |
| `content/about.md` | GitHub 链接、邮箱 |

改完 `git push`，站点会自动重建。

### 步骤 5：开启评论（giscus）

1. **开启 Discussions**：仓库 → **Settings → General → Features** → 勾选 **Discussions**。
2. **新建分类**：仓库 → **Discussions → Categories**，确认存在 `Announcements`
   （默认自带；也可以新建一个 `Comments` 分类）。
3. **安装 giscus App**：访问 <https://github.com/apps/giscus> → **Install** →
   授权给该仓库。
4. **获取 ID**：打开 <https://giscus.app/zh-CN>，在「仓库」输入框填
   `<你的用户名>/<你的用户名>.github.io`，选择刚确认的分类，页面会给出类似：

   ```
   <script src="https://giscus.app/client.js"
           data-repo="alice/alice.github.io"
           data-repo-id="R_kgDOLxxxxxxxx"
           data-category="Announcements"
           data-category-id="DIC_kwDOLxxxxxxxx"
           ...>
   ```

   把 `data-repo-id` 和 `data-category-id` 两个值抄下来。

5. **填入 `hugo.yaml`**：

   ```yaml
   params:
     giscus:
       repo: "alice/alice.github.io"
       repoId: "R_kgDOLxxxxxxxx"        # ← 粘贴
       category: "Announcements"
       categoryId: "DIC_kwDOLxxxxxxxx"  # ← 粘贴
   ```

6. `git push`。评论即刻生效。

> 未填 ID 时，文章底部会显示一段「评论区尚未启用」的提示，不会报错，方便你先上线再慢慢配。

---

## 四、日常写作

### 方式 A：本地写（推荐，可实时预览）

```bash
# 首次需安装 Hugo extended（≥ 0.146.0）
#   macOS:   brew install hugo
#   Windows: 官方 release 下载 hugo_extended_*.zip
#   Linux:   见 https://gohugo.io/installation/linux/

hugo new posts/my-new-post.md   # 生成带 front matter 的草稿
hugo server -D                  # 本地预览 http://localhost:1313
```

写完把 `draft: true` 改成 `false` 再发布。

### 方式 B：直接在 GitHub 网页上写（零本地环境）

仓库 → `content/posts/` → **Add file → Create new file** → 命名为 `xxx.md` →
粘贴 front matter + 正文 → **Commit changes** → Action 自动发布。

### 发布

```bash
git add .
git commit -m "post: 文章标题"
git push
```

大约 30~60 秒后线上更新。

### front matter 速查

```yaml
---
title: "文章标题"
date: 2025-01-01T10:00:00+08:00
lastmod: 2025-01-01T10:00:00+08:00
draft: false                      # true = 只在本地可见
summary: "列表页摘要"
categories: ["技术"]              # 顶部导航「分类」页聚合
tags: ["Hugo", "教程"]
ShowToc: true                     # 显示目录
comments: true                    # 显示评论区
cover:
  image: "/images/cover.jpg"      # 封面图（可选）
---
```

> `date` 设为未来时间且 `buildFuture: false`（默认）＝ **定时发布**。

更多语法与 Hugo shortcode 见 `content/posts/markdown-cheatsheet.md`。

---

## 五、评论是怎么工作的

- giscus 读取页面的 `<script data-mapping="pathname">`，用 **文章路径** 去 Discussions 里找
  对应的讨论帖。
- **不存在就自动创建**：第一个读者打开页面时，giscus 用你的 GitHub App 授权在指定
  category 下建帖，标题就是文章标题，并回链到文章 URL。
- 之后所有评论都追加在这个帖子里，你在 GitHub 上能直接看到、能回复、能锁定、能删除。
- **要删评论**：去 Discussions 对应帖子操作即可，站点无需重新构建。
- **改文章路径 = 换评论区**：因为映射键是 `pathname`，重命名 md 文件会「丢失」旧评论。
  想保住旧评论，可在 Discussions 里手动改帖子的 URL 映射，或把 `mapping` 改成
  `og:title`（主题已输出 og 标签）。

---

## 六、可选增强

| 需求 | 做法 |
| --- | --- |
| 自定义域名 | 仓库 Settings → Pages → Custom domain 填域名；再在 `static/CNAME` 写域名（也可由 Pages 自动管理）+ DNS 加 CNAME 记录 |
| 访问统计 | 在 `layouts/_partials/extend_head.html` 加一段 Umami / Google Analytics 脚本 |
| 更多社交分享 | 改 `hugo.yaml` 的 `params.socialIcons` |
| 首页个人卡片 | 见 `hugo.yaml` 末尾注释的 `profileMode` |
| 显示相关文章 | `hugo.yaml` 加 `params.ShowRelated: true` |
| 数学公式 | `hugo.yaml` 开 `markup.goldmark.extensions.passthrough` + 引入 KaTeX |
| 定时发布 | front matter 的 `date` 写未来时间 |
| 改用 utterances（严格 Issues） | 见下 |

### 改用 utterances（评论落 GitHub Issues）

删掉 `layouts/_partials/comments.html`，换成：

```html
<script src="https://utteranc.es/client.js"
        repo="<用户名>/<用户名>.github.io"
        issue-term="pathname"
        label="comment"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>
```

然后在 <https://github.com/apps/utterances> 安装 App 并授权仓库即可。
代价：项目已基本停止维护，无表情反应、无回复嵌套优化，且无法像 giscus 那样自动跟随暗色主题。

---

## 七、本地构建验证（可选）

```bash
hugo --gc --minify     # 产物在 public/，可用于检查报错
```

本项目已验证：Hugo v0.167.0 + PaperMod v8.0（2025-09），构建成功、29 个页面、0 错误。
构建日志中可能出现 2 条 `deprecated .Language.LanguageDirection / .LanguageCode` 警告，
它们来自主题内部的 `baseof.html` 与 `rss.xml`，与本项目配置无关，等主题更新即可消除。

### 常见问题

| 现象 | 原因 / 解决 |
| --- | --- |
| Actions 报 `module not found` / 主题缺失 | 子模块未提交，执行 `git submodule status` 检查，确认 `.gitmodules` 与 `themes/PaperMod` gitlink 都在 |
| 页面 404，仓库根目录出现 `README` 站点 | Pages Source 选了「Deploy from a branch」，改成 **GitHub Actions** |
| 评论显示提示语 | `hugo.yaml` 里 `params.giscus.repoId`/`categoryId` 还是占位符 |
| 评论报 `giscus is not installed` | 未安装 giscus App 或未授权该仓库 |
| 站内搜索无结果 | 确认 `hugo.yaml` 的 `outputs.home` 含 `JSON`，且 `content/search.md` 存在 |
| 本地 `hugo` 命令报 Sass 错误 | 装的是非 extended 版，需 `hugo_extended` |
