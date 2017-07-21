title: Hexo配置
date: 2016-02-04 11:02:35
tags: Hexo
category:
---

** Hexo配置 ：** <Excerpt in index | 首页摘要\>
关于Hexo的一些配置，这是初来乍到的开始。
<!-- more -->
<The rest of contents | 余下全文\>

# Hexo配置GitHub博客
* 好久都想搞一下博客这个东西，每次都弄了好长时间又放弃了，感觉对于写前端的我，真是麻烦，最近快过年了，最后一个版本也上线了，闲了一段时间，想起来Github上的博客还闲置着，终于有时间搞搞了。

* 网上已经有很多对于hexo的配置安装，我这里只是写我自己安装步骤和遇到的问题。

# 开始部署

## 首先在Github上有hexo的源代码 这里是连接[hexo](https://github.com/hexojs/hexo)
* 安装hexo
```
npm install hexo-cli -g
```
* 通过终端cd 进入到自己的博客文件夹
```
hexo init
```
会有如下文件在你的博客目录
[~  tree -L 1 demo
demo
├── _config.yml
├── package.json
├── scaffolds
├── source
└── themes

3 directories, 2 files]
> 这样一个简单的博客初始化就完成了。不过还不能运行，下来我让它跑起来。

# 本地运行

* 现在用`hexo generate`生成静态文件，然后用`hexo server`运行本地服务器，打开浏览器输入<http://0.0.0.0:4000/>查看效果。如果发现有问题，在md文件改了之后，刷新页面就可以看到更改的效果了

# 配置
* 接下来我们要让代码运行到github上

## _config.yml文件配置
* 主要是这一部分的设置，会影响到你在网页上的展示，`如果你出现404页面`，就是因为这里没有设置你自己的博客代码地址
```
deploy:
  type: git  #最新版3.X把这个github缩写成git了，记得空格.
  repository: "https://github.com/tiandabao/tiandabao.github.io.git"
  branch: master
```
然后提交到github上就行了


## 其实在这个过程中有好多坑，这里是我遇到的和解决办法

* 在运行`hexo generate`时候会出现如下代码

```
{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
```

解决办法。
```
$ npm install hexo --no-optional
```
> 注意 这个地方运行上面的代码是要在自己博客的目录下，不然不能生效。

* 在运行`hexo deploy`时候有时候会提示如下
```
ERROR Deployer not found : git
```
解决方法
在自己博客目录下运行
```
npm install hexo-deployer-git --save
```

* 在提交github的时候会出现类似于这样的代码
```
modified:   .DS_Store
modified:   .deploy_git (modified content, untracked content)
modified:   themes/yilia (modified content, untracked content)
```
这部分不用管，主题是从远端clone的， 主题设置可以看这篇博客[yilia](http://litten.github.io/2014/08/31/hexo-theme-yilia/)
`在设置主题的时候，有个头像问题，一直没解决，就是图片的地址，处理方法：我把图片放到public/images/avatar.jpg下，提交到github后我可以找到我图片的地址“https://github.com/tiandabao/tiandabao.github.io/blob/master/images/avatar.jpg“但是在主题里我设置了这个url显示不出来，后来我发现，在你要显示的图片网页上右键，复制图片网址，“https://github.com/tiandabao/tiandabao.github.io/blob/master/images/avatar.jpg?raw=true”这时图片的地址后面多了个?raw=true，这时候把这个地址赋值给你主题头像字段`

```
error: Your local changes to the following files would be overwritten by merge:
	.DS_Store
Please, commit your changes or stash them before you can merge.
Aborting
```
出现上面的问题，处理步骤删除本地.DS_Store，在进行pull或者push，由于.DS_Store文件存储有冲突，删掉会重新生成一分

**每次提交到github前记得hexo g 和 hexo d， 不然也页面显示不出来**


> 友情参考
<https://segmentfault.com/a/1190000002398039>
<http://litten.github.io/2014/08/31/hexo-theme-yilia/>
<http://www.jianshu.com/p/b7886271e21a>
