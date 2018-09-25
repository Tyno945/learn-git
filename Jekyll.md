# Jekyll学习

## 环境搭建及基础

Jekyll 是一个简单的博客形态的静态站点生产机器。

搭建jekyll基本步骤:

* 安装ruby(2.0.0以上)
* 切换ruby的[source来源](https://gems.ruby-china.org/)
* 通过gem命令安装jekyll
* github上fork心仪的jekyll模板，本地jekyll serve运行相应的博客


### Windows系统下安装

The easiest way to run Jekyll is by using the [RubyInstaller](https://rubyinstaller.org/) for Windows.

1. 下载RubyInstaller，默认安装
2. 安装Jekyll and Bundler，`gem install jekyll bundler`
3. 检查安装`jekyll -v`


### 快速开始

```bash
# Install Jekyll and Bundler gems through RubyGems
gem install jekyll bundler

# Create a new Jekyll site at ./myblog
jekyll new myblog

# Change into your new directory
cd myblog

bundle install

# Build the site on the preview server
bundle exec jekyll serve

# Now browse to http://localhost:4000
```

```
报错
Error:  Permission denied - bind(2) for 127.0.0.1:4000

原因：这个是由于127.0.0.1：4000这个端口被占用引起的

解决：
第一：找到4000端口被哪个进程占用了
netstat -aon | findstr "4000"

第二：关闭冲突的进程 

参考https://segmentfault.com/q/1010000010483290/a-1020000010487387
```

生产环境命令行

`$ JEKYLL_ENV=production jekyll build`

### 新增博客文章

所有的文章都在 _posts 文件夹中。这些文件可以用 Markdown 编写，也可以用 Textile 格式编写。只要文件中有 YAML 头信息，它们就会从源格式转化成 HTML 页面，从而成为你的静态网站的一部分。

发表一篇新文章，你所需要做的就是在 _posts 文件夹中创建一个新的文件。文件名的命名非常重要。Jekyll 要求一篇文章的文件名遵循下面的格式：

`年-月-日-标题.MARKUP`

```
新增文章没有生成
Jekyll post not generated

* The post is not placed in the _posts directory.
* The post has incorrect title. Posts should be named YEAR-MONTH-DAY-title.MARKUP (Note the MARKUP extension, which is usually .md or .markdown)
* The post's date is in the future. You can make the post visible by setting future: true in _config.yml (documentation)
* The post has published: false in its front matter. Set it to true.

参考：
https://stackoverflow.com/questions/30625044/jekyll-post-not-generated
```

### 全局配置文件

所有全局配置的变量都在`_config.yml`文件中，注意修改`_config.yml`文件，不会即时生效，需要重新启动服务

## 参考资料

1. [官网](https://jekyllrb.com/)

2. [使用Jekyll搭建自己的博客-全教程](https://www.jianshu.com/p/c04475ba80e4)