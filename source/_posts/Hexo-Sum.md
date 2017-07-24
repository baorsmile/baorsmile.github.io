title: Hexo Sum
date: 2017-07-22 22:34:35
tags: Hexo
category:
comments: true
---

** Hexo Sum ：** <Excerpt in index | 首页摘要\>
Hexo主题，博文插入图片，添加评论，添加浏览数，加密博文
<!-- more -->
<The rest of contents | 余下全文\>

# 博文插入图片
* 修改`_config.yml`里面的`post_asset_folder:true`
* 在根目录下执行`npm install hexo-asset-image --save`安装插件
* `hexo n "new"`生成一个新的博文，在`source/_posts/new`会生一个`new`文件夹,用来存放图片
* `![你想输入的替代文字](new/xxx.jpg)`

# 添加搜狐畅言评论
* 举例我用的主题是`smackdown`在`themes/smackdown/layout/_partial/article.ejs`里面最后添加如下代码
```
<% if (!index && post.comments){ %>
<section id="comments">
<!--高速版，加载速度快，使用前需测试页面的兼容性-->
    <div id="SOHUCS" sid="<%= page.title %>" style="padding: 0px 30px 0px 46px;"></div>
    <script>
         (function(){
               var appid = '你的 AppID',
               conf = '你的 Appkey';
               var doc = document,
               s = doc.createElement('script'),
               h = doc.getElementsByTagName('head')[0] || doc.head || doc.documentElement;
               s.type = 'text/javascript';
               s.charset = 'utf-8';
               s.src =  'http://assets.changyan.sohu.com/upload/changyan.js?conf='+ conf +'&appid=' + appid;
               h.insertBefore(s,h.firstChild);
               window.SCS_NO_IFRAME = true;
         })()
    </script>
</section>
<% } %>
```
* 畅言目前需要自己的域名网站备案才可以使用，具体的创建可以按照这篇博文实现[Github pages + Hexo 博客 yilia 主题使用畅言评论系统](http://blog.csdn.net/tzs_1041218129/article/details/70163194)


# 添加阅读量
* 可以参考[为hexo-theme-smackdown主题添加阅读数](http://blog.smackdown.gebilaowu.cn/2016/10/31/hexo%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E9%98%85%E8%AF%BB%E6%95%B0/)

# 文章加密
* 可参考：[hexo-blog-encrypt](https://github.com/MikeCoder/hexo-blog-encrypt/blob/master/ReadMe.zh.md)
* 这个插件目前只适合[`Next`](https://github.com/iissnan/hexo-theme-next)主题
