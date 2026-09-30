---
title: "Markdown 写作速查"
date: 2025-01-02T10:00:00+08:00
draft: false
author: "你的名字"
summary: "写博客会用到的 Markdown 语法与 Hugo 专属扩展（shortcode）速查表。"
categories: ["教程"]
tags: ["Markdown", "Hugo"]
ShowToc: true
comments: true
---

## 一、front matter 字段说明

每篇文章开头的 `---` 之间就是 YAML front matter：

| 字段 | 作用 |
| --- | --- |
| `title` | 标题 |
| `date` | 发布时间（决定排序；未来时间 + `buildFuture: false` = 定时发布） |
| `lastmod` | 最后修改时间 |
| `draft` | `true` 时本地可见、线上不发布 |
| `summary` | 列表页摘要与 SEO 描述 |
| `categories` | 分类（顶部导航「分类」页聚合） |
| `tags` | 标签 |
| `ShowToc` | 是否显示目录 |
| `comments` | 是否显示评论区 |
| `cover.image` | 封面图路径 |

## 二、Hugo 专属 shortcode

PaperMod 内置了几个常用 shortcode，语法是 `{{</* ... */>}}`（实际书写时去掉注释符号）。

### 折叠块

```text
{{</* collapse title="点我展开" */>}}
被折叠的内容
{{</* /collapse */>}}
```

### 图片（带说明文字）

```text
{{</* figure src="/images/a.png" title="标题" caption="说明文字" */>}}
```

### 内联图片（可控制宽高）

```text
{{</* inTextImg src="/images/a.png" alt="图" width="60%" */>}}
```

### 视频 / 音频

```text
{{</* video src="/videos/demo.mp4" */>}}
{{</* audio src="/audio/demo.mp3" */>}}
```

## 三、转义与常见坑

- 正文中出现 `{{ }}` 需要转义，否则 Hugo 会当成模板
- 文件名用英文小写 + 连字符，URL 更友好：`my-first-post.md`
- 图片建议放 `static/images/`，引用路径以 `/images/` 开头
- 中文标题会自动成为页面 `title`，无需额外设置

## 四、本地预览

```bash
# 启动本地服务器（含草稿）
hugo server -D

# 构建到 public/
hugo --minify
```

## 五、发布

```bash
git add .
git commit -m "post: 新文章"
git push
```
