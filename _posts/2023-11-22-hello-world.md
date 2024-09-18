---
title: "Site | Hello World"
date: 2023-11-22
draft: false

categories: ["site"]
math: true
tags: ["site"]
---

又是一次从零开始的旅程。

想起来我的第一篇博客是在大一写的，主题应该是如何搭建 `hexo`。那个时候还没转到计算机，觉得有一个自己的网站是一件很厉害的事情。倒是写了几篇学习方面的笔记，但我把更多的时间用来折腾博客主题和更多功能了，以为自己是在学“技术”，其实就是把别人写好的配置文件抄来抄去 hhh，现在想来确实做了很多没什么营养的事。捣鼓了好久的主题到头来没写过一篇真正意义上的文章。

不过折腾了这么多，我还是稍微整理一下我都用过哪些博客生成器。因为用 `Typecho`、`WordPress` 和 `Halo` 这些都要配置服务器，而我又只想白嫖，所以个人从始至终都是用的静态博客生成器。整体的流程为：先使用 Markdown 写好文章，然后 push 到 Github 上，Github Action 会使用你指定的博客生成器将博客生成为网页代码并部署到 Github Pages 上。听着似乎比较复杂，但其实只要第一次设置好，后面再写就只要“写文章->push”就好了。

|                方案                |                                        特点                                         |
| :--------------------------------: | :---------------------------------------------------------------------------------: |
| [hexo](https://hexo.io/index.html) |           最出名的博客生成器了吧，文档全主题多，只是文章多了生成会比较慢            |
|     [hugo](https://gohugo.io/)     | 拿 go template 写的，主要的优点就是比 hexo 快，文章多了生成速度也不慢，只是主题偏少 |
|  [zola](https://www.getzola.org/)  | 非常快且轻量的博客生成器，比 hugo 还快，只需一个可执行文件就可以做所有的事。主题少  |
|            自己写生成器            |     详见 <https://blog.hamaluik.ca/posts/build-your-own-static-site-generator/>     |

现在将博客转为 Jekyll，主要也是不想太折腾，而且 Github Pages 对 Jekyll 原生支持，所以我找了个 [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 主题[随便两下](https://github.com/cotes2020/chirpy-starter)就把博客搭起来了，而且该有的功能也都有（评论、标签、目录、搜索之类的）。后面开始慢慢写点东西吧，尽量保证每个月至少两三篇
