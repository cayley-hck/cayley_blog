---
title: 'CocoaPods->IOS的类库管理工具'
date: 2016-08-31 21:50:10
tags:
---
CocoaPods是什么？
CocoaPods应该是iOS的类库管理工具了，通过cocoaPods，只需要一行命令就可以完全解决包依赖问题，而且绝大部分有名的开源类库，都支持CocoaPods。

官方网址:https://cocoapods.org/

安装:
Mac 下都自带 ruby，使用 ruby 的 gem 命令即可下载安装：

gem命令:

{% codeblock %}

appledeMac-mini:~ apple$ gem --help
RubyGems is a sophisticated package manager for Ruby.  This is a
basic help message containing pointers to more information.

  Usage:
    gem -h/--help
    gem -v/--version
    gem command [arguments...] [options...]

  Examples:
    gem install rake
    gem list --local
    gem build package.gemspec
    gem help install

  Further help:
    gem help commands            list all 'gem' commands
    gem help examples            show some examples of usage
    gem help platforms           show information about platforms
    gem help <COMMAND>           show help on COMMAND
                                   (e.g. 'gem help install')
    gem server                   present a web page at
                                 http://localhost:8808/
                                 with info about installed gems
  Further information:
    http://guides.rubygems.org

{% endcodeblock %}   

安装命令
{% codeblock %}
appledeMac-mini:~ apple$ sudo gem install cocoapods
Password:
Fetching: i18n-0.7.0.gem (100%)
Successfully installed i18n-0.7.0
Fetching: thread_safe-0.3.5.gem (100%)
Successfully installed thread_safe-0.3.5
Fetching: tzinfo-1.2.2.gem (100%)
Successfully installed tzinfo-1.2.2
Fetching: minitest-5.8.4.gem (100%)
Successfully installed minitest-5.8.4
Fetching: activesupport-4.2.6.gem (100%)
Successfully installed activesupport-4.2.6
Fetching: nap-1.1.0.gem (100%)
Successfully installed nap-1.1.0
Fetching: fuzzy_match-2.0.4.gem (100%)
Successfully installed fuzzy_match-2.0.4
Fetching: cocoapods-core-0.39.0.gem (100%)
Successfully installed cocoapods-core-0.39.0
Fetching: claide-0.9.1.gem (100%)
Successfully installed claide-0.9.1
Fetching: colored-1.2.gem (100%)
Successfully installed colored-1.2
Fetching: xcodeproj-0.28.2.gem (100%)
Successfully installed xcodeproj-0.28.2
Fetching: cocoapods-downloader-0.9.3.gem (100%)
Successfully installed cocoapods-downloader-0.9.3
Fetching: cocoapods-plugins-0.4.2.gem (100%)
Successfully installed cocoapods-plugins-0.4.2
Fetching: cocoapods-search-0.1.0.gem (100%)
Successfully installed cocoapods-search-0.1.0
Fetching: cocoapods-stats-0.6.2.gem (100%)
Successfully installed cocoapods-stats-0.6.2
Fetching: cocoapods-try-0.5.1.gem (100%)
Successfully installed cocoapods-try-0.5.1
Fetching: netrc-0.7.8.gem (100%)
Successfully installed netrc-0.7.8
Fetching: cocoapods-trunk-0.6.4.gem (100%)
Successfully installed cocoapods-trunk-0.6.4
Fetching: molinillo-0.4.4.gem (100%)
Successfully installed molinillo-0.4.4
Fetching: escape-0.0.4.gem (100%)
Successfully installed escape-0.0.4
Fetching: cocoapods-0.39.0.gem (100%)
Successfully installed cocoapods-0.39.0
Parsing documentation for i18n-0.7.0
Installing ri documentation for i18n-0.7.0
Parsing documentation for thread_safe-0.3.5
Installing ri documentation for thread_safe-0.3.5
Parsing documentation for tzinfo-1.2.2
Installing ri documentation for tzinfo-1.2.2
Parsing documentation for minitest-5.8.4
Installing ri documentation for minitest-5.8.4
Parsing documentation for activesupport-4.2.6
unable to convert "\x84" from ASCII-8BIT to UTF-8 for lib/active_support/values/unicode_tables.dat, skipping
Installing ri documentation for activesupport-4.2.6
Parsing documentation for nap-1.1.0
Installing ri documentation for nap-1.1.0
Parsing documentation for fuzzy_match-2.0.4
Installing ri documentation for fuzzy_match-2.0.4
Parsing documentation for cocoapods-core-0.39.0
Installing ri documentation for cocoapods-core-0.39.0
Parsing documentation for claide-0.9.1
Installing ri documentation for claide-0.9.1
Parsing documentation for colored-1.2
Installing ri documentation for colored-1.2
Parsing documentation for xcodeproj-0.28.2
Installing ri documentation for xcodeproj-0.28.2
Parsing documentation for cocoapods-downloader-0.9.3
Installing ri documentation for cocoapods-downloader-0.9.3
Parsing documentation for cocoapods-plugins-0.4.2
Installing ri documentation for cocoapods-plugins-0.4.2
Parsing documentation for cocoapods-search-0.1.0
Installing ri documentation for cocoapods-search-0.1.0
Parsing documentation for cocoapods-stats-0.6.2
Installing ri documentation for cocoapods-stats-0.6.2
Parsing documentation for cocoapods-try-0.5.1
Installing ri documentation for cocoapods-try-0.5.1
Parsing documentation for netrc-0.7.8
Installing ri documentation for netrc-0.7.8
Parsing documentation for cocoapods-trunk-0.6.4
Installing ri documentation for cocoapods-trunk-0.6.4
Parsing documentation for molinillo-0.4.4
Installing ri documentation for molinillo-0.4.4
Parsing documentation for escape-0.0.4
Installing ri documentation for escape-0.0.4
Parsing documentation for cocoapods-0.39.0
Installing ri documentation for cocoapods-0.39.0
21 gems installed

{% endcodeblock %}

$ pod setup

但是如果gem太老,可以使用命令升级 gem:
{% codeblock %}
sudo gem update --system
{% endcodeblock %}

另外，ruby 的软件源 https://rubygems.org 使用的是亚马逊的云服务，所以被墙了，
需要更新一下 ruby 的源，使用如下代码将官方的 ruby 源替换成国内淘宝的源：

先移除现有https://rubygems.org源
{% codeblock %}
appledeMac-mini:my-v2ex apple$ gem sources --remove https://rubygems.org/
https://rubygems.org/ removed from sources
{% endcodeblock %}

添加淘宝源

{% codeblock %}
appledeMac-mini:my-v2ex apple$ gem sources -a https://ruby.taobao.org/
{% endcodeblock %}

查看所有源
{% codeblock %}
https://ruby.taobao.org/ added to sources
appledeMac-mini:my-v2ex apple$ gem sources -l
*** CURRENT SOURCES ***

https://ruby.taobao.org/
{% endcodeblock %}

使用 CocoaPods

使用时需要新建一个名为 Podfile 的文件，将依赖的库名字依次列在文件中即可

{% codeblock %}
appledeMac-mini:my-v2ex apple$ vim Podfile 

platform:ios,'8.0'
inhibit_all_warnings!
use_frameworks!

def pods
    pod 'SnapKit', '~> 0.18.0'
    pod 'Alamofire', '~> 3.1.4'
    pod 'ObjectMapper', '~> 1.1.1'
    pod 'AlamofireObjectMapper', '~> 2.1.0'
    pod 'Ji', '~> 1.2.0'
    pod 'DrawerController', '~> 1.1.0'
    pod 'Kingfisher', '~> 1.9.1'
    pod 'KVOController', '~> 1.0.3'
    pod 'YYText', '~> 0.9.18'
    pod 'FXBlurView', '~> 1.6.4'
    pod 'SVProgressHUD', '~> 1.1.3'
    pod 'MJRefresh', '~> 3.1.0'
    pod 'KeychainSwift', '~> 3.0.11'
    pod 'INSImageView', '~> 0.1.5'
    pod 'CXSwipeGestureRecognizer', '~> 1.0.2'
    pod '1PasswordExtension', '~> 1.8'
    pod 'Shimmer', '~> 1.0.2'
    pod 'Fabric'
    pod 'Crashlytics'
end

target 'my-v2ex' do
    pods
end
{% endcodeblock %}

然后将编辑好的 Podfile 文件放到项目根目录中，执行如下命令即可：
{% codeblock %}
cd "your project home"
pod install
{% endcodeblock %}

现在，你的所有第三方库都已经下载完成并且设置好了编译参数和依赖，你只需要记住如下 2 点即可：
使用 CocoaPods 生成的 .xcworkspace 文件来打开工程，而不是以前的 .xcodeproj 文件。
每次更改了 Podfile 文件，你需要重新执行一次pod update命令。

具体使用命令如下:
{% codeblock %}
Usage:

    $ pod COMMAND

      CocoaPods, the Cocoa library package manager.

Commands:

    + cache      Manipulate the CocoaPods cache
    + init       Generate a Podfile for the current directory.
    + install    Install project dependencies to Podfile.lock versions
    + ipc        Inter-process communication
    + lib        Develop pods
    + list       List pods
    + outdated   Show outdated project dependencies
    + plugins    Show available CocoaPods plugins
    + repo       Manage spec-repositories
    + search     Search for pods.
    + setup      Setup the CocoaPods environment
    + spec       Manage pod specs
    + trunk      Interact with the CocoaPods API (e.g. publishing new specs)
    + try        Try a Pod!
    + update     Update outdated project dependencies and create new
                 Podfile.lock

Options:

    --silent     Show nothing
    --version    Show the version of the tool
    --verbose    Show more debugging information
    --no-ansi    Show output without ANSI codes
    --help       Show help banner of specified command


appledeMac-mini:my-v2ex apple$ pod install
Updating local specs repositories

CocoaPods 1.0.0.beta.6 is available.
To update use: `gem install cocoapods --pre`
[!] This is a test version we'd love you to try.

For more information see http://blog.cocoapods.org
and the CHANGELOG for this version http://git.io/BaH8pQ.

Analyzing dependencies
Downloading dependencies
Installing 1PasswordExtension (1.8)
Installing Alamofire (3.1.5)
Installing AlamofireObjectMapper (2.1.0)
Installing CXSwipeGestureRecognizer (1.0.2)
Installing Crashlytics (3.7.0)
Installing DrawerController (1.1.0)
Installing FXBlurView (1.6.4)
Installing Fabric (1.6.7)
Installing INSImageView (0.1.6)

Installing Ji (1.2.0)
Installing KVOController (1.0.3)
Installing KeychainSwift (3.0.11)
Installing Kingfisher (1.9.3)
Installing MJRefresh (3.1.0)
Installing ObjectMapper (1.1.5)
Installing SVProgressHUD (1.1.3)
Installing Shimmer (1.0.2)
^[[21~Installing SnapKit (0.18.0)
Installing YYText (0.9.19)
Generating Pods project
Integrating client project

[!] Please close any current Xcode sessions and use `my-v2ex.xcworkspace` for this project from now on.
Sending stats
Pod installation complete! There are 19 dependencies from the Podfile and 19
total pods installed.
{% endcodeblock %}

关于 Podfile.lock
执行pod install之后，除了 Podfile 外，CocoaPods 还会生成一个名为Podfile.lock的文件，Podfile.lock 应该加入到版本控制里面，不应该把这个文件加入到.gitignore中。因为Podfile.lock会锁定当前各依赖库的版本，之后如果多次执行pod install 不会更改版本，要pod update才会改Podfile.lock了。这样多人协作的时候，可以防止第三方库升级时造成大家各自的第三方库版本不一致。

那些踩过的坑
1)
appledeMac-mini:my-v2ex apple$ pod install
Updating local specs repositories

CocoaPods 1.0.0.beta.6 is available.
To update use: `gem install cocoapods --pre`
[!] This is a test version we'd love you to try.

For more information see http://blog.cocoapods.org
and the CHANGELOG for this version http://git.io/BaH8pQ.

Analyzing dependencies
[!] Could not automatically select an Xcode project. Specify one in your Podfile like so:

    xcodeproj 'path/to/Project.xcodeproj'
当把CocoaPods生成的workspace移动到上层目录时，需要改下Pods.xcconfig和工程里的一些设置
解决方法:
在Podfile文件里指定下工程目录
......  
xcodeproj 'my-v2ex/my-v2ex.xcodeproj'   
......  


