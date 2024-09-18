---
title: "Site | 为什么我又回归 Zola 了？"
date: 2024-02-05
categories: ["site"]
math: true
tags: ["site"]

---

> [2024-09-18] 鞭尸，回到 Jekyll 了
{: .prompt-warning }

对的，我又回归 Zola 了，只能说真的香

> 幸亏之前没立什么再不折腾博客主题之类的 hhh 😂
{: .prompt-info }

其实静态博客能提供的功能都大差不差：博文，分类，评论，还有拓展的一些 markdown 语法。之前看重 Jekyll 是因为它在 Github 上原生支持，所以决定用它。但是虽然听上去方便，但实际用起来还是有些不方便，比如文章中不能出现 `http`，不然 Github Action 检查时会出错；Jekyll 在 Windows 上运行还得安装 ruby，感觉还是有点重了；~~再者就是有点审美疲劳了~~；Jekyll 项目的文件夹结构我也有点不喜欢，`_posts` 和 `_tabs` 是分开的

基于上面的一些原因，再加上我早就看上了现在用的这个主题 [serene](https://github.com/isunjn/serene)，我决定使用 Zola，一个 `exe` 可执行文件就可以完成所有的事情，速度也很快。幸亏现在的文章不是很多，迁移的时候也没费太大力气

现在就是只管写 Markdown 文件就好了，整体流程和以前用 Hugo 差不多其实，在私有仓库保存源码，再用 Action 推到 `miaohn.github.io` 上就成了。图片选择与文章存到一起，我对图床还是有些不放心
