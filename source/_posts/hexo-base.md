---
title: hexo base
date: 2019-07-05 20:23:31
tags: JS
top: 10
---
### 前言
##### 使用github pages服务搭建博客的好处：
+ 全是静态文件，访问速度快；
+ 免费方便，不用花一分钱就可以搭建一个自由的个人博客，不需要服务器不需要后台；
+ 可以随意绑定自己的域名；
+ 数据安全，基于github的版本管理，想恢复到哪个历史版本都行；
+ 博客内容可以轻松打包、转移、发布到其它平台；
<!--more-->
#### 1、准备工作
##### 1.1 必备工具与技能
> 有一个github账号，没有的话去注册一个；<br>安装了node.js、npm，并了解相关基础知识<br>安装了git for windows（或者其它git客户端）
##### 1.2 本教程使用的环境
> Windows10<br>node.js@10.16.0<br>git@2.22.0.windows.1<br>hexo@3.9.0
#### 2、hexo使用
##### 2.1 简介
> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。<br>[官网地址]( http://hexo.io)<br>[github地址](https://github.com/hexojs/hexo)
##### 2.2 原理
> 由于github pages存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动态内容，假如每次写完一篇文章都要手动更新博文目录和相关链接信息，相信谁都会疯掉，所以hexo所做的就是将这些md文件都放在本地，每次写完文章后调用写好的命令来批量完成相关页面的生成，然后再将有改动的页面提交到github。
##### 2.3 注意事项
+ 很多命令既可以用Windows的cmd来完成，也可以使用git bash来完成，但是部分命令会有一些问题，为避免不必要的问题，建议全部使用git bash来执行
+ hexo不同版本差别较大，很多文章的配置信息都是基于2.x的，所以注意不要被误导；
+ hexo有2种 **_config.yml** 文件，一个是根目录下的全局的_config.yml，一个是各个**theme**下的；
##### 2.4 安装
```
$ npm install -g hexo-cli
```
##### 2.5 初始化
> 新建一个名为myblog的文件夹（名字可以随便取），比如我的是D:\store\myblog，由于这个文件夹将来就作为你存放代码的地方，所以最好不要随便放。
```
$ cd /d/store/myblog/
$ hexo init
```
> hexo会自动下载一些文件到这个目录，包括node_modules、package.json等
```
$ cd myblog //切换到目录
$ npm install //安装依赖
$ hexo generate //生成静态页面至public目录
$ hexo server //启动服务
```
> 执行hexo generate，hexo就会在public文件夹生成相关html文件<br>hexo server是开启本地预览服务，打开浏览器访问 http://localhost:4000 即可看到内容<br>第一次初始化的时候hexo已经帮我们写了一篇名为 Hello World 的文章,默认的主题比较丑
##### 2.6 修改主题
[官方主题地址](https://hexo.io/themes/)
> 首先下载一个自己喜欢的主题，用hexo-theme-yilia主题为例,下载后的主题放在themes文件夹下
```
$ cd /d/store/myblog
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
> 修改根目录下_config.yml中的theme: landscape改为theme: yilia，然后重新执行hexo g来重新生成，hexo s查看效果
#### 3、搭建github博客
##### 3.1 创建仓库
> 新建一个名为你的用户名.github.io的仓库，比如说，如果你的github用户名是test，那么你就新建test.github.io的仓库，将来你的网站访问地址就是http://test.github.io 了。<br>每一个github账户最多只能创建一个这样可以直接使用域名访问的仓库。
###### 注意事项：
+ 必须是你的用户名，其它名称无效
+ 创建空仓库，任何描述与README文件都不要加，预防一会上传冲突
##### 3.2 提交到远程仓库
```
$ cd /d/store/myblog/public
$ git init
$ git remote add origin git@github.com:xxx/xxx.github.io.git
$ git add .
$ git commit -m"init"
$ git push origin master
$ git checkout -b git-pages
$ git push origin git-pages
```
###### 注意事项
+ 已经配置好SSH key
+ 在github上面如果有git-pages分支（github规定），这个仓库就会开启git page，就可以使用https://username.github.io/来访问
##### 3.3 写博客并上传到github
```
$ cd /d/store/myblog
$ hexo new 'first-blog'//hexo会帮我们在_posts下生成相关md文件
```
> 我们只需要打开这个文件就可以开始写博客了；<br>当然你也可以直接自己新建md文件，用这个命令的好处是帮我们自动生成了时间；<br>一般完整格式如下：
```
---
title: postName #文章页面上的显示名称
date: 2019-07-05 15:30:16 #文章生成时间，一般不改，当然也可以任意修改
categories: 默认分类 #分类
tags: [tag1,tag2,tag3] #文章标签，可空，多标签请用格式，注意:后面有个空格
description: 附加一段文章摘要，字数最好在140字以内，会出现在meta的description里面
---
以下是正文
```
> 文章写好后生成并部署到GitHub，首先安装部署插件
```
$ npm install hexo-deployer-git --save
```
> 配置_config.yml中有关deploy的部分：
```
deploy:
  type: git
  repository: git@github.com:xxx/xxx.github.io.git
  branch: master
```
> 生成与部署,执行一下命令
```
$ hexo g
$ hexo d //将本次有改动的代码全部提交
```
+ **此时访问https://xxx.github.io就可以看到新写的文章了**
#### 4、绑定域名
> 不绑定域名肯定也是可以的，就用默认的 xxx.github.io 来访问，如果你想更个性一点，想拥有一个属于自己的域名也是OK的。<br>首先你要注册一个域名，域名注册godaddy、阿里云等都可以！
+ 绑定域名分2种情况：带www和不带www的；
> 域名配置最常见有2种方式，CNAME和A记录，CNAME填写域名，A记录填写IP，由于不带www方式只能采用A记录，所以必须先ping一下你的用户名.github.io的IP，然后到你的域名DNS设置页，将A记录指向你ping出来的IP，将CNAME指向你的用户名.github.io，这样可以保证无论是否添加www都可以访问
+ 新建CNAME文件
>到你的项目根目录新建一个名为CNAME的文件（无后缀），里面填写你的域名，加不加www看你自己喜好
+ 加不加www规则如下：
> 如果你填写的是没有www的，比如 mygit.me，那么无论是访问 http://www.mygit.me 还是 http://mygit.me ，都会自动跳转到 http://mygit.me<br>如果你填写的是带www的，比如 www.mygit.me ，那么无论是访问 http://www.mygit.me 还是 http://mygit.me ，都会自动跳转到 http://www.mygit.me<br>如果你填写的是其它子域名，比如 abc.mygit.me，那么访问 http://abc.mygit.me 没问题，但是访问 http://mygit.me ，不会自动跳转到 http://abc.mygit.me
+ 重新生成部署
```
$ hexo g
$ hexo d
```
> 此时就可以使用域名访问你的博客了<br>绑定了新域名之后，原来的xxx.github.io并没有失效，而是会自动跳转到你的新域名。
#### 5、常用hexo命令
```
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
```
###### 缩写
```
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```
> 至此hexo的基本使用就完成了，接下来就是自己配置优化自己的博客了；

**新手可能一脸懵逼，大牛可能一脸蔑视。希望大家都有收获（还不快去打赏⊙﹏⊙）！！！**
