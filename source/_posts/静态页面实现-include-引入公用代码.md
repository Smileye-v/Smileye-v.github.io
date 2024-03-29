---
title: 静态页面实现 include 引入公用代码
tags: [php, gulp]
categories: [编程, php]
date: 2021-03-16
type: tags
toc: true
---

静态页面实现 include 引入公用代码的示例

<!--more-->

> 原文链接: [静态页面实现 include 引入公用代码的示例](https://www.jb51.net/article/124448.html)

一直以来，我司的前端都是用 php 的 include 函数来实现引入 header 、footer 这些公用代码的，就像下面这样：

```html
<!-- index.php -->

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
  </head>
  <body>
    <?php include('header.php'); ?>
    <div>页面主体部分</div>
    <?php include('footer.php'); ?>
  </body>
</html>
```

```html
<!-- header.php -->
<header>这是头部</header>
```

```html
<!-- footer.php -->
<footer>这是底部</footer>
```

直到最近某个项目需要做一个 webapp，是通过 HBuilder 将静态页面打包成 APP，这就让我碰到难题了。
如果是小项目，那就直接手动多复制粘贴几遍，但如果页面较多，复制粘贴的方案明显不靠谱，维护成本也高。
在查了很多资料后，最终确定用 gulp 来解决，具体操作如下：

### 安装 gulp 和 gulp-file-include

首先新建个文件夹，在终端里定位到文件夹的位置，然后进行 npm 初始化

```
npm init
```

然后安装 gulp

```
npm install gulp --save-dev
```

接着安装 gulp-file-include

```
npm install gulp-file-include --save-dev
```

### 新建并配置 gulpfile.js

接着我们手动新建一个 js 文件取名为 gulpfile，并在里面写入如下代码：

```js
var gulp = require("gulp");
var fileinclude = require("gulp-file-include");

gulp.task("fileinclude", function () {
  // 适配page中所有文件夹下的所有html，排除page下的include文件夹中html
  gulp
    .src(["page/**/*.html", "!page/include/**.html"])
    .pipe(
      fileinclude({
        prefix: "@@",
        basepath: "@file",
      })
    )
    .pipe(gulp.dest("dist"));
});
```

### 创建项目目录结构，并添加测试代码

项目的整体目录结构应该是这样

```
app

　page

　　include

　　　header.html

　　　footer.html

　　index.html

　gulpfile.js

　package.json
```

然后我们添加测试代码，header.html 和 footer.html 没太多好说的，主要是 index.html 要特别注意引入的方式，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
  </head>
  <body>
    @@include('include/header.html')
    <div>页面主体部分</div>
    @@include('include/footer.html')
  </body>
</html>
```

### 运行

在终端里敲入以下代码，看执行效果

```
gulp fileinclude
```

会发现，多了个 dist 文件夹，里面有一个 index.html 文件，gulp-file-include 已经帮我们把最终编译好的 index.html 文件生成好了。
可能你已经能举一反三了，在 gulpfile.js 里，我们可以手动设置最终生成文件的位置，就是这句话

```
gulp.dest('dist')
```

### 自动编译

静态页面引入公用代码的问题已经解决了，但每次编写源 html 后，都要去终端里手动执行下编译操作还是很麻烦，那能不能让文件自动编译呢？答案一定是可以的。

gulp 有个 watch 方法，就是监听文件是否有变动的，我们只需稍微修改下 gulpfile.js 文件，增加一段监听代码，如下：

```js
var gulp = require("gulp");
var fileinclude = require("gulp-file-include");

gulp.task("fileinclude", function () {
  // 适配page中所有文件夹下的所有html，排除page下的include文件夹中html
  gulp
    .src(["page/**/*.html", "!page/include/**.html"])
    .pipe(
      fileinclude({
        prefix: "@@",
        basepath: "@file",
      })
    )
    .pipe(gulp.dest("dist"));
});

gulp.task("watch", function () {
  gulp.watch("page/**/*.html", ["fileinclude"]);
});
```

写好之后，我们只需在终端里执行

```
gulp watch
```

我们每次保存源 html 后，gulp 就会自动帮我们编译一遍。

<a href="https://www.jb51.net/article/124448.htm">原文地址</a>
