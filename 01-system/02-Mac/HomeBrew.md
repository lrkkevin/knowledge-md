[TOC]



### 一、HomeBrew 常用命令

| brew search [package-name]    | 搜索包                            |      |
| ----------------------------- | --------------------------------- | ---- |
| brew info [package-name]      | 查看包的信息                      |      |
| brew install [package-name]   | 安装包                            |      |
| brew outdated                 | 查看所有有更新版本的软件          |      |
| brew upgrade                  | 更新所有包                        |      |
| brew upgrade [package-name]   | 更新某个包                        |      |
| brew uninstall [package-name] | 卸载某个包                        |      |
| brew cleanup                  | 清理所有软件的旧版                |      |
| brew cleanup [软件名]         | 清理软件的旧版                    |      |
| brew list                     | 列出所有安装的包                  |      |
| brew cask                     | 列出所有 **HomeBrew Cask** 的命令 |      |



### 二、brew 和 brew cask 的区别
brew 是从下载源码解压然后 ./configure && make install ，同时会包含相关依存库。并自动配置好各种环境变量，而且易于卸载。

而 **brew cask** 是 已经编译好了的应用包 （.dmg/.pkg），仅仅是下载解压，放在统一的目录中（/opt/homebrew-cask/Caskroom），省掉了自己去下载、解压、拖拽（安装）等蛋疼步骤，同样，卸载相当容易与干净。这个对一般用户来说会比较方便，包含很多在 AppStore 里没有的常用软件。

### 三、管理后台软件
诸如 Nginx、MySQL 等软件，都是有一些服务端软件在后台运行，如果你希望对这些软件进行管理，可以使用 brew services 命令来进行管理

- brew     services list： 查看所有服务
- brew     services run [服务名]: 单次运行某个服务
- brew     services start [服务名]: 运行某个服务，并设置开机自动运行。
- brew     services stop [服务名]：停止某个服务
- brew     services restart：重启某个服务。

###  四、检查 **Hombrew** 环境
如果你的 Hombrew 没有办法正常的工作，你可以执行 brew doctor 来开启 Homebrew 自带的检查，从而确认有哪些问题，并进行修复。

### 五、更新 **Homebrew**
Homebrew 经常会在执行命令的时候触发更新，不过如果你想要主动检查更新，可以执行 brew update 来唤起 Homebrew 的更新。

### 六、**添加一个新的** tap
homebrew 官方在安装的时候会有一些 tap 但是在使用时，依然会需要安装一些特殊的 tap ，这个时候，我们就要用到 tap 的命令来添加新的 tap.

在添加 tap 时，输入命令 brew tap [user/repo] ，就可以完成添加 tap 了

#### 1、常用 tap

在使用 homebrew 时，我们一般会添加几个常用的 tap，来确保我们有足够的软件来安装。
* Caskroom
Caskroom 是 Homebrew 下一个非常出名的 tap ，有了 caskroom，我们就可以安装一些有图形化界面的软件了，比如 VSCode、Typora 等软件。
使用起来也非常简单，最新版 Homebrew 中，你可以直接使用 brew cask install [软件名] 来安装特定的软件，homebrew 会自动安装 Caskroom。

* homebrew-cask-fonts
程序员难免要安装一些代码字体，这样才能更好的写代码，Homebrew 也提供了方便我们安装字体的 tap。
在使用时，你需要先添加对应的 tap ，然后执行安装即可了，比如我们要安装 source code pro ，只需要执行如下命令。

brew tap homebrew/cask-fonts

brew cask install font-source-code-pro

### 七、使用技巧

* 切换国内的镜像源
Homebrew 默认使用的是国外的源，在下载时速度可能会比较慢。好在国内的清华大学和中科大提供了 Homebrew 的镜像源，我们可以很轻松的切换源，从而提升我们的下载速度。

* 使用中科大的镜像
执行如下命令，即可切换为中科大的镜像
cd "$(brew --repo)"
git remote set-url origin git://mirrors.ustc.edu.cn/brew.git
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin git://mirrors.ustc.edu.cn/homebrew-core.git

* 使用清华大学的镜像
执行如下命令，即可切换为清华大学的镜像
git -C "$(brew --repo)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git
git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

* 使用**Brewfile**完成环境迁移
设备永久了，我们的电脑中会有大量的软件，如果你需要迁移环境，重新安装会是一个大麻烦，好在 Homebrew 本身为我们提供了一个非常好用的环境迁移的工具 —— Homebrew Bundle
你首先需要在之前的电脑中执行 brew bundle dump 来完成当前环境的导出,导出完成后，你会得到一个 *Brewfile*。
然后将 *Brewfile* 复制到新的电脑中，并执行 brew bundle 来开始安装的过程。

* 使用网页搜索**Caskroom**的软件
Brew Caskroom 并没有提供搜索的命令，不过我们可以借助一些网站来进行搜索，一个是 Homebrew 官方的 Caskrrom 页面：https://formulae.brew.sh/cask/
在这个页面，你可以看到所有被收录的页面，在命令行中输入对应的软件就可以安装了。

你也可以访问 http://macappstore.org/，在网站中输入你要安装的软件，点击搜索，在弹出的页面中，查看安装指南即可。