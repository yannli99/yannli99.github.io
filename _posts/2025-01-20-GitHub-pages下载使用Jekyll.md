# GitHub pages下载使用Jekyll

GitHub官网推荐使用`Jekyll`搭建静态网站（[GitHub Pages](https://docs.github.com/zh/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll)），但是根据[Jekyll官网](https://jekyllrb.com/)的教程安装Ruby费了老大一份劲。

主要是`Jekyll`官网的教程版本太低了，而Ubuntu对Ruby源的维护版本也太落后了，所以安装途中会有很多问题，推荐使用[rbenv](https://github.com/rbenv/rbenv)去安装`ruby`，根据项目官网的教程使用git去安装。
```bash
# 使用http下载太卡了，推荐使用ssh下载
$ git clone git@github.com:rbenv/rbenv.git ~/.rbenv
# 启动rbenv脚本
$ ~/.rbenv/bin/rbenv init
# 如果没有install命令需要下载这个`ruby-build`
$ git clone git@github.com:rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build
```
配置镜像到`~/.bash_profile`里。[rbenv 使用指南](https://ruby-china.org/wiki/rbenv-guide)
```bash
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
export RUBY_BUILD_MIRROR_URL=https://cache.ruby-china.com
```

下载完成后，使用`rbenv install -l`命令去查看各个发行版本，最新版本都是3.X的，而Jekyll教程里还是2.X版本的。
```bash
$ rbenv install -l
3.1.6
3.2.6
3.3.7
3.4.1
jruby-9.4.9.0
mruby-3.3.0
picoruby-3.0.0
truffleruby-24.1.1
truffleruby+graalvm-24.1.1
```
选择一个版本下载，数字开头的都是稳定版本，单词开头的是特殊版本
```bash
$ rbenv install 3.4.1
```
下载完成后，运行`rbenv global 3.4.1`设置系统的ruby版本，使用`-v`参数验证安装版本
```bash
$ rbenv global 3.4.1
$ ruby -v
ruby 3.4.1 (2024-12-25 revision 48d4efcb85) +PRISM [x86_64-linux]
$ gem -v
3.6.2
```
安装完ruby后再使用gem安装`jekyll`就不会有任何版本问题了
```bash
$ gem install bundler jekyll
$ jekyll -v
jekyll 4.3.4
```
