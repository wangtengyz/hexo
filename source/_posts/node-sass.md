---
title: node-sass 安装失败的原因及解决办法
# categories: 2019-11-28 20:16:41
tags: node.js #文章标签 可以省略
description: node.js sass #你对本页的描述
date: 2019-11-28 20:16:41
---
### node-sass 安装失败的原因及解决办法
目录
* 一、node-sass 安装失败的原因
* 二、解决办法
* 三、安装失败后重新安装问题
* 四、其他问题
<!-- more -->

### 一、node-sass 安装失败的原因

最近开发其他中，npm install 时偶尔遇到报错：没有安装node-sass 安装失败的问题，百度之后发现是被墙了，但根据百度的方法换了淘宝镜像和用了vpn都安装失败，最后发现原来是因为没有卸载之前安装失败的包导致的。本人最后的解决方案是先卸载以前安装失败的包，npm uninstall node-sass，然后使用淘宝镜像源重新安装了一遍就成功了。

npm 安装 node-sass 依赖时，会从 github.com 上下载 .node 文件。由于国内网络环境的问题，这个下载时间可能会很长，甚至导致超时失败。

解决方案就是使用其他源，或者使用工具下载，然后将安装源指定到本地。

### 二、解决办法

#### 解决方法一：使用淘宝镜像源（推荐）
设置变量 sass_binary_site，指向淘宝镜像地址。示例：
```
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/

// 也可以设置系统环境变量的方式。示例
// mac 下
SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install node-sass

// window 下
set SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ && npm install node-sass
```
或者设置全局镜像源：
```
npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/
```
或者安装nrm切换源：
```
    Mac 上全局安装要加 sudo
    npm install -g nrm 
    // 查看所有的可用的源
    nrm ls
    // 切换源
    nrm use 源的名字
```

#### 解决方法二：使用 cnpm
使用 cnpm 安装 node-sass 会默认从淘宝镜像源下载，也是一个办法：
```
cnpm install node-sass
```

#### 解决方法三：创建.npmrc文件
在项目根目录创建.npmrc文件，复制下面代码到该文件。
```
phantomjs_cdnurl=http://cnpmjs.org/downloads
sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
registry=https://registry.npm.taobao.org
```
保存后 删除之前安装失败的包(第一次安装请跳过此步)
```
npm uninstall node-sass
```
重新安装
```
npm install node-sass
```
此方式有时还是会失效报错，改用方法一成功

### 三、安装失败后重新安装问题
之前安装失败，再安装就不去下载了，怎么办呢？那就先卸载再安装：
```
npm uninstall node-sass
npm i node-sass --sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```

### 四、其他问题
有时候，按照上面的方法都试过，在项目编译或者打包的时候还是node-sass报错的话，那就需要看看log日志了，可能是其他npm包版本不对导致，再试试排查其他npm，博主就遇到一次安装上面的方法一下载好node-saas后，编译报错，要我重新安装node-sass，重新安装几次都是这样。后面发现是一个npm包ice-script版本的问题，将它后退到v1.9.3版本就好了。出了问题，不要着急，多看看log日志，排查原因，大多数都是想关联的npm包版本不对。