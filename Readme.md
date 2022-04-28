<!--
 * @Author: Shuangchi He / Yulv
 * @Email: yulvchi@qq.com
 * @Date: 2022-04-28 11:49:52
 * @Motto: Entities should not be multiplied unnecessarily.
 * @LastEditors: Shuangchi He
 * @LastEditTime: 2022-04-28 12:27:41
 * @FilePath: /Jekyll-Pages/Readme.md
 * @Description: Init from https://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html
-->

<h1><center> Blog on GitHub Pages using Jekyll </h1></center>

使用Jekyll在GitHub页面上写博客。

---

- [1. 致谢](#1-致谢)
- [2. git分支设置](#2-git分支设置)
- [3. 创建设置文件](#3-创建设置文件)
- [4. 创建模板文件](#4-创建模板文件)
- [5. 创建文章](#5-创建文章)
- [6. 创建首页](#6-创建首页)
- [7. 发布blog](#7-发布blog)

---

# 1. 致谢

本项目基于[搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](https://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)进行实践和整理。

# 2. git分支设置

创建一个没有父节点的分支`gh-pages`。因为github规定，只有该分支中的页面，才会生成网页文件。

``` bash
git checkout --orphan gh-pages
```

# 3. 创建设置文件

在项目根目录下，创建名为`_config.yml`的jekyll设置文件，其具体内容见[_config.yml](./_config.yml)。其他设置可用默认选项，具体解释见[官网](https://github.com/mojombo/jekyll/wiki/Configuration)。

# 4. 创建模板文件

在项目根目录下，创建`_layouts`目录，用于存放模板文件，其具体内容见[default.html](./_layouts/default.html)。

`default.html`是Blog的默认模板。Jekyll使用[Liquid模板语言](https://github.com/shopify/liquid/wiki/liquid-for-designers)，`{{ page.title }}`表示文章标题，`{{ content }}`表示文章内容,更多模板变量请参考[官方文档](https://github.com/mojombo/jekyll/wiki/Template-Data)。

# 5. 创建文章

在项目根目录下，创建`_posts`目录，用于存放blog文章，如[2022-04-28-hello-world.html](./_posts/2022-04-28-hello-world.html)。

- 文件名必须为`年-月-日-文章标题.后缀名`的格式。后缀名有为html、md等。
- 每篇文章的头部，必须有设置元数据的yaml文件头。它用三根短划线`---`，标记开始和结束，里面每一行设置一种元数据。`layout:default`，表示该文章的模板使用`_layouts`目录下的`default.html`；`title: 你好，世界`，表示该文章的标题是`你好，世界`，如果不设置这个值，默认使用嵌入文件名的标题，即`hello world`。
- `{{ page.title }}`就是文件头中设置的`你好，世界`，`{{ page.date }}`则是嵌入文件名的日期（也可在文件头重新定义date变量），`| date_to_string`表示将page.date变量转化成人类可读的格式。

# 6. 创建首页

创建index.html文件，具体内容见[index.html](./index.html)。

`{% for post in site.posts %}`，表示对所有帖子进行遍历。
Liquid模板语言规定，`输出内容使用两层大括号`，`单纯的命令使用一层大括号`。至于`{{site.baseurl}}`就是_config.yml中设置的baseurl变量。

# 7. 发布blog

``` bash
git stage .
git commit -m "提交的描述"
git push origin gh-pages
```

发布成功后，可在 [yulv-git.github.io/Jekyll-Pages](https://yulv-git.github.io/Jekyll-Pages) 网页上看到blog。(`yulv-git`换成您的GitHub用户名。)
