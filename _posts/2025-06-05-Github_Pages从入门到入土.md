---
title:      "Github_Pages从入门到入土"
date:       2025-06-06 14:15:00 +0800
categories: [学习笔记]
tags:       [jekyll]     
mathjax:    false
---


# 0. GitHub Pages为何物？
GitHub Pages是GitHub提供的静态网站免费托管服务，允许用户直接从GitHub仓库免费发布网页或博客。它主要用于展示个人、项目或组织的静态内容（如 HTML、CSS、JavaScript 和 Markdown 文件），无需服务器配置，适合文档、作品集、项目主页等。GitHub Pages是开发者快速上线静态网站的首选方案，结合版本控制与自动化，兼顾简便与灵活性。

## 核心特点
**免费托管**

每个GitHub账号可拥有一个免费站点（如 用户名.github.io），项目仓库也可单独部署项目页面。

**支持静态生成器**

原生支持Jekyll（自动编译 Markdown为HTML），也可手动配置其他工具（如 Hugo、Hexo等）。

**自定义域名**

可绑定自己的域名（如 example.com），并支持 HTTPS。

**自动化构建**

通过GitHub Actions或直接推送代码触发自动部署。

**开源集成**

与GitHub仓库无缝衔接，代码变更实时同步到网站。

## 适用场景
**个人博客/简历：** 用 Markdown 写文章，通过 Jekyll 生成静态页面。

**项目文档：** 为开源项目托管说明文档（如 docs/ 目录）。

**作品集：** 展示设计、开发等作品。

**小型企业官网：** 低成本发布公司信息。

# 1. 准备工作——Ruby环境的安装
Jekyll基于Ruby，因此需要先安装Ruby环境。本文采用Windows 10/11上的WSL方式在本地安装，WSL需要Windows 10 version 1607或更新版本。也可以在Windows上直接使用Ruby+Devkit安装，其实更简单一些，但这里出于个人习惯没有选用。

## WSL的安装
打开管理员终端

    wsl --install

等待安装完成创建账户及口令。

备份系统源

    cd /etc/apt
    sudo mv sources.list sources.list.bak

将源切换到国内的阿里源或清华源，这里以阿里源为例

vi sources.list

添加以下内容

    deb https://mirrors.aliyun.com/ubuntu/ noble main restricted universe multiverse
    deb-src https://mirrors.aliyun.com/ubuntu/ noble main restricted universe multiverse
 
    deb https://mirrors.aliyun.com/ubuntu/ noble-security main restricted universe multiverse
    deb-src https://mirrors.aliyun.com/ubuntu/ noble-security main restricted universe multiverse
 
    deb https://mirrors.aliyun.com/ubuntu/ noble-updates main restricted universe multiverse
    deb-src https://mirrors.aliyun.com/ubuntu/ noble-updates main restricted universe multiverse
 
    # deb https://mirrors.aliyun.com/ubuntu/ noble-proposed main restricted universe multiverse
    # deb-src https://mirrors.aliyun.com/ubuntu/ noble-proposed main restricted universe multiverse
 
    deb https://mirrors.aliyun.com/ubuntu/ noble-backports main restricted universe multiverse
    deb-src https://mirrors.aliyun.com/ubuntu/ noble-backports main restricted universe multiverse

之后:wq保存并update/upgrade。

    $ sudo apt-get update -y && sudo apt-get upgrade -y

在24.04中也可以修改新位置（/etc/apt/sources.list.d/ubuntu.sources）
添加以下内容：

	# 阿里云
	Types: deb
	URIs: http://mirrors.aliyun.com/ubuntu/
	Suites: noble noble-updates noble-security
	Components: main restricted universe multiverse
	Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
    
之后同样:wq保存并update/upgrade，也可同样实现目的。

## 安装ruby相关环境（包括gems）
安装ruby相关环境

    $ sudo apt-get install ruby3.2 ruby3.2-dev build-essential dh-autoreconf

检查gems源

    $ gem sources l

更改gems源为国内源

    $ gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/

检查是否切换成功

    $ gem sources -l

更新gems

    $ sudo gem update

## 安装JavaScript相关环境
由于后续操作会生成JavaScript文件，需事先安装Node.js和npm

    $ sudo apt install nodejs npm

查看 node 版本

    $ node --version

# 2. 小试牛刀——Jekyll环境的安装
## 安装jekyll环境
安装jekyll

    $ sudo gem install jekyll 

检查Jekyll版本

    $ jekyll -v

安装 Bundler，他会为你自动安装其他所需的程序

    $ sudo gem install bundler

安装依赖包

    $ sudo gem install tzinfo-data tzinfo minima

至此已完成Jekyll环境的搭建，可以创建Jekyll项目了。

## jekyll初体验
上面jekyll已完成Jekyll环境的搭建，可以创建Jekyll项目了，如在/website新建一个Jekyll项目项目

    $ jekyll new website

切换到website路径

    $ cd website

运行项目生成博客网页

    $ bundle exec jekyll serve 

即可在http://127.0.0.1:4000或http://localhost:4000浏览刚刚发布的网页了。

若localhost:4000无法访问,可能是由于windows网系统的4000端口被占用了，可以尝试切换端口或通过netstat -an查询端口信息,找到占用端口的程序关闭它。

如果端口被占用可以指定端口

    $ bundle exec jekyll serve --port 1234 

即可将端口指定为1234。

为了后续套用模板及优化方便，我们先开了解一下jekyll的目录结构

    _layouts （存放页面模板，md或html文件的内容会填充模板）

    _sass（存放样式表）

    _includes （可以复用在其它页面被include的html页面）

    _posts（博客文章页面）

    assets（原生的资源文件）

    js

    css

    image

    _config.yml （全局配置文件）

    index.html, index.md, README.md （首页index.html优先级最高，如果没有index，默认启用README.md文件）

    自定义文件和目录


Jekyll 使用主题模板（Themes  Templates）配合Liquid语言的方式将静态网站的可维护性和动态灵活性结合，避免了手动重复劳动，同时保留了静态页面的高性能和安全性，大大简化了建站流程。甚至对于没有Web前端开发经验的用户也能极简、高效、免费的快速搭建个人网站。

Jekyll 的社区日益壮大，诞生了许多精美的主题、插件、教程和其他相关资源。

几个Jekyll主题模板下载站点 

    GitHub.com #jekyll-theme repos

    jamstackthemes.dev

    jekyllthemes.org

    jekyllthemes.io

    builtatlightspeed.com

# 3. 初露锋芒——Jekyll主题模板应用
经过一番乱花渐欲迷人眼的比较，最终选择Chirpy作为主题模板开始进一步的搭建。Chirpy是一个基于 Jekyll 的现代化开源主题，专为技术博客、个人网站和文档设计。

Chirpy可以直接通过前往官方的模版页面Chirpy Start部署：点击按钮Use this template > Create a new repository，将新的repository命名为xxxx.github.io，其中xxxx为GitHub用户名。为了方便直接修改源码，实现自定义的功能，这里将整个项目下载至本地。

    $ git clone https://github.com/cotes2020/jekyll-theme-chirpy

博客页面的常用配置信息存放在根目录的./_config.yml，常用的变量有：

    •	lang - 语言

    •	timezone - 时区

    •	title - 网站标题

    •	tagline - 网站副标题

    •	description - 网站描述

    •	url – 网站地址，如https://xxxx.github.io

    •	avatar - 作者头像，如https:// xxxx.github.io /imgs/avatar.jpg

相应修改即可全局跟随。

在子目录_data下还存放有一些可选填的配置文件，主要用于设置网页的外观，可根据需求修改。以下是对这些配置文件的简要说明：

    博客的语言配置存放在_data/locate/，其中英语的配置文件为_data/locate/en.yml。

    文章的作者信息存放在_data/authors.yml，可填写多个作者。

    侧边菜单栏的社交账号信息存放在_data/contact.yml。比如我不想显示Twitter，只需在配置文件中注释掉相应内容。

    文章的分享信息存放在_data/share.yml。

再前往项目所在目录，运行初始化程序。

    $ bash ./tools/init.sh

待完成初始化后，会给出需要安装的依赖库，安装即可

    $ sudo bundle install

根据项目文件生成静态网页文件

    $ sudo bundle

在服务器上部署博客前，也可以根据项目文件生成静态网页并部署至本地服务器可以在 http://127.0.0.1:1234 或 http://localhost:1234预览网页效果。

    $ sudo bundle exec jekyll serve --port 1234

# 4. 渐入佳境——部署至GitHub Pages
在实现本地运行后，我们将博客部署到GitHub Pages上。

在部署之前，我们需要在GitHub上创建博客专用的repository，并命名为xxxx.github.io，xxxx为GitHub用户名。

在Settings/Pages/Build and deployment的Source一栏选中GitHub Actions选项。

然后找到GitHub Actions选项的下方出现的GitHub Pages Jekyll配置文件，点击Configure按钮，会显示配置文件的具体内容，再点击文件右上角的Commit changes...，即可在仓库中生成配置文件。

切换本地文件的远程仓库地址

    $ git remote set-url origin git@github.com:xxxx/xxxx.github.io

验证是否关联成功

    $ git remote -v  # 应显示 fetch 和 push 的地址

切换本地分支到main

    $ git branch -m master main

检查当前分支

    $ git branch

同步远程文件到本地并允许合并无关历史

    $ git pull origin main --allow-unrelated-histories

将本地内容push到github

    $ git push origin main

完成上述步骤后， GitHub Actions便会自动生成静态网页并部署到GitHub Page上。

之后如果要在博客上发布新的文章，只需遵循以下步骤：

    在_posts目录中添加新的Markdown文件

    执行命令生成静态网页

        $ sudo bundle exec jekyll serve

    添加到git更新列表并确认
        $ git add .
        $ git commit -m "chore: just do it"

    推送至GitHub仓库

        $ git push origin main

更新后的网页会自动部署到GitHub Page上。

有时会提示要求输入账号密码，但其实在2021年8月13号之后必须用ssh keys作为验证的token，别的都不好使。这时查看需要查看这个项目的remote到底是什么

    $ git config --get remote.origin.url

如果返回的是

    https://github.com/xxxx/xxxx.github.io.git

这证实在clone的时候误用了https协议，https协议会每次要求你输入账户密码。于是我们需要更改remote协议

    $ git remote set-url origin git@github.com:xxxx/xxxx.github.io.git

再次查看remote协议，得到

    $ git@github.com：xxxx/xxxx.github.io.git

再次尝试git push，一切顺利！


# 5. 炉火纯青——个性化调整与美化

## 5.1 让网页显示数学公式
在上传一篇文献阅读笔记到Github page时发现公式无法正常显示，比较常用的一种方式就是借用MathJax帮助渲染。

在_includes文件夹中添加mathjax_support.html文件，内容如下：

    <script type="text/x-mathjax-config">
    MathJax.Hub.Config({
        TeX: {
            equationNumbers: {
            autoNumber: "AMS"
            }
        },
        extensions: ["tex2jax.js"],
        jax: ["input/TeX", "output/HTML-CSS"],
        tex2jax: {
        inlineMath: [ ['$','$'], ["\\(","\\)"] ],
        displayMath: [ ['$$','$$'], ["\\[","\\]"] ],
        processEscapes: true,
        "HTML-CSS": { fonts: ["TeX"] }
        }
    });
    MathJax.Hub.Register.MessageHook("Math Processing Error",function (message) {
            alert("Math Processing Error: "+message[1]);
        });
    MathJax.Hub.Register.MessageHook("TeX Jax - parse error",function (message) {
            alert("Math Processing Error: "+message[1]);
        });
    </script>
    <script
    type="text/javascript"
    async
    src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML"
    ></script>

其中 src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-MML-AM_CHTML" 这一行不可以被替换。

因为mathjax解析比较耗资源，若文章里没有公式就不必加载了。因此可在_layouts/default.html中再添加一个开关，若文章里用了公式，添加使能即可。

以下内容添加到_layouts/default.html中调用（调用位置为 &#x7B;% include head.html %&#x7D; 下面一行）

&nbsp;&nbsp;&nbsp;&nbsp;&#x20;&#x20;&#x20;&#x20;&#x7B;&#x25;&#x20;&#x69;&#x66;&#x20;&#x70;&#x61;&#x67;&#x65;&#x2E;&#x6D;&#x61;&#x74;&#x68;&#x6A;&#x61;&#x78;&#x20;&#x25;&#x7D;

&nbsp;&nbsp;&nbsp;&nbsp;&#x20;&#x20;&#x20;&#x20;&#x7B;&#x25;&#x20;&#x69;&#x6E;&#x63;&#x6C;&#x75;&#x64;&#x65;&#x20;&#x6D;&#x61;&#x74;&#x68;&#x6A;&#x61;&#x78;&#x5F;&#x73;&#x75;&#x70;&#x70;&#x6F;&#x72;&#x74;&#x2E;&#x68;&#x74;&#x6D;&#x6C;&#x20;&#x25;&#x7D;

&nbsp;&nbsp;&nbsp;&nbsp;&#x20;&#x20;&#x20;&#x20;&#x7B;&#x25;&#x20;&#x65;&#x6E;&#x64;&#x69;&#x66;&#x20;&#x25;&#x7D;

做好这两步之后，只需要在需要用到公式渲染的文章开头添加上mathjax: true即可，如下：

    title:      "what is FFT？"
    date:       2025-04-19
    categories: [Note123]
    tags:       [math, time_series, signal processing] 
    mathjax:    true




## 暂时还没想好，不过可以参考以下链接：

    https://ittousei.github.io/posts/customize-my-blog/
