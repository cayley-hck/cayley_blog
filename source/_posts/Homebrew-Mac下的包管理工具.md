---
title: Homebrew-Mac下的包管理工具
date: 2016-09-20 17:28:13
tags:
---
Homebrew是一款自由及开放源代码的软件包管理系统，用以简化Mac OS X系统上的软件安装过程,使用GitHub仓库的用户贡献扩大对软件包的支持。
Homebrew以Ruby语言写成，针对于Mac OS X操作系统自带Ruby的版本。默认安装在/usr/local，由一个核心git版本库构成，以使用户能更新Homebrew。包管理器使用一种称为“公式”（formula）的DSL脚本来管理依赖、下载源代码及配置和编译软件，从源代码中构建软件。称为“瓶”（bottle）的二进制包是用默认选项预编译好的公式

安装：
安装非常简单，用系统自带ruby就可以了。
项目地址：https://github.com/Homebrew/brew
{% codeblock %}
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
{% endcodeblock %}

默认安装的路径为：
HOMEBREW_REPOSITORY: /usr/local/Homebrew

默认的安装的软件包路径为：
HOMEBREW_CELLAR: /usr/local/Cellar

可以改变这环境变量

export HOMEBREW_CELLAR= /usr/local/Cellar


使用：

  brew search [TEXT|/REGEX/]  搜索软件包
  brew (info|home|options) [FORMULA...] 查看软件包内容信息
  brew install FORMULA...	安装软件包
  brew update	更新Homebrew
  brew upgrade [FORMULA...]	 升级软件包，第三个参数可以指定升级的软件包，默认升级所有
  brew uninstall FORMULA...	卸载软件包
  brew list [FORMULA...]	查看已安装的软件包

Troubleshooting:
  brew config		配置信息
  brew doctor		诊断
  brew install -vd FORMULA

Developers:
  brew create [URL [--no-fetch]]
  brew edit [FORMULA...]
  https://github.com/Homebrew/brew/blob/master/share/doc/homebrew/Formula-Cookbook.md

Further help:
  man brew
  brew help [COMMAND]
  brew home

Tap:
rew tap:
    List all installed taps.

brew tap [--full] user/repo [URL]:
    Tap a formula repository.

    With URL unspecified, taps a formula repository from GitHub using HTTPS.
    Since so many taps are hosted on GitHub, this command is a shortcut for
    tap user/repo https://github.com/user/homebrew-repo.

    With URL specified, taps a formula repository from anywhere, using
    any transport protocol that git handles. The one-argument form of tap
    simplifies but also limits. This two-argument command makes no
    assumptions, so taps can be cloned from places other than GitHub and
    using protocols other than HTTPS, e.g., SSH, GIT, HTTP, FTP(S), RSYNC.

    By default, the repository is cloned as a shallow copy (--depth=1), but
    if --full is passed, a full clone will be used. To convert a shallow copy
    to a full copy, you can retap passing --full without first untapping.

    tap is re-runnable and exits successfully if there's nothing to do.
    However, retapping with a different URL will cause an exception, so first
    untap if you need to modify the URL.

brew tap --repair:
    Migrate tapped formulae from symlink-based to directory-based structure.

brew tap --list-official:
    List all official taps.

brew tap --list-pinned:
    List all pinned taps.


Homebrew Cask:

它是对已经编译好了的应用包 （.dmg/.pkg），仅仅是下载解压，放在统一的目录中，并链接到appliction目录，省掉了自己去下载、解压、拖拽（安装）等步骤，同样，卸载相当容易与干净。

项目地址：https://github.com/caskroom/homebrew-cask

通过 homebrew cask 来安装 app，首先是安装

brew tap caskroom/cask && brew install brew-cask

使用：

Commands:

    audit                  verifies installability of Casks
    cat                    dump raw source of the given Cask to the standard output
    cleanup                清除下载的缓存文件
    create                 creates the given Cask and opens it in an editor
    doctor                 诊断
    edit                   edits the given Cask
    fetch                  下载软件包到本地，但不安装
    home                   打开官方网站
    info                   显示软件包信息
    install                安装软件包
    list                   查看已安装的如阿年包
    search                 搜索软件包
    style                  checks Cask style using RuboCop(代码风格检查器),首次使用会先安装 rubocop-cask
    uninstall              卸载软件包
    update                 更新，等同于brew update
    zap                    zaps all files associated with the given Cask

See also "man brew-cask"

默认配置信息为：
==> Homebrew-Cask Staging Location:
/usr/local/Homebrew/Caskroom
==> Homebrew-Cask Cached Downloads:
/Users/cayley/Library/Caches/Homebrew/Cask
3 files, 119.2M (warning: run "brew cask cleanup")
==> Homebrew-Cask Default Tap Path:
/usr/local/Homebrew/Library/Taps/caskroom/homebrew-cask
==> Homebrew-Cask Alternate Cask Taps:
/usr/local/Homebrew/Library/Taps/caskroom/homebrew-versions

也可以对这些配置进行修改：
例如：

export HOMEBREW_CASK_OPTS="--appdir=/Applications --caskroom=/usr/local/Homebrew/Caskroom"

#将这个配置加入到.zchrc或者.bash_profile中
 
 #生效
 source ~/.zchrc


Caskbrew：
如果你不熟悉终端命令，可以下载cakebrew，它是homebrew的客户端，可以实现常用的搜索、安装、卸载操作
官网下载安装

https://www.cakebrew.com

安装：
{% codeblock %}
brew cask install cakebrew
{% endcodeblock %}

问题：

看官方https://github.com/Homebrew/brew/blob/master/docs/Common-Issues.md  

常用安装包历史记录下：
                            
brew cask install google-chrome 

brew cask install iterm2

brew cask install navicat-for-mysql

brew cask install  shadowsocksx

brew cask install phpstorm (ps:http://idea.qinxi1992.cn/)

brew cask install docker

brew cask install sublime-text

brew cask install virtualbox

