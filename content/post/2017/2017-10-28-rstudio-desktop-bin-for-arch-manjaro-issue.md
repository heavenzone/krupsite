---
title: "在arch-manjaro捣鼓rstudio遇到的问题"
author: "Heaven Zone"
date: "2017-10-28"
categories: ["rstudio"]
tags: ["arch", "manjaro", "linux", "rstudio"]
---

## 官网资料

先看看官网，一共放了两个64位的rstudio的deb包，

1. RStudio 1.1.383 - Ubuntu 12.04-15.10/Debian 8 (64-bit) 	
  - 文件名：rstudio-1.1.383-amd64.deb
  - 文件大小：97.4 MB 	
  - MD5：3eede231b7206a7eebbf090f4991358f
  - 下载链接：<https://download1.rstudio.org/rstudio-1.1.383-amd64.deb>
2. RStudio 1.1.383 - Ubuntu 16.04+/Debian 9+ (64-bit) 	
  - 文件名：rstudio-xenial-1.1.383-amd64.deb
  - 文件大小：65 MB 	
  - MD5：fccec7cbf773c3464ea6cbb91fc2ec28
  - 下载链接：<https://download1.rstudio.org/rstudio-xenial-1.1.383-amd64.deb>
  

## aur上关于rstudio的资料  

可以再看看下面几个链接，是aur上关于rstudio-desktop的。

- <https://aur.archlinux.org/packages/rstudio-desktop-bin/>
- <https://pastebin.com/n2mzRfQR>
- <https://gist.github.com/anonymous/efc868e4cdb686848209be8c5917608d>
- <https://aur.archlinux.org/packages/rstudio-desktop-git/>


输入`yaourt rstudio`，可以看到有几个关于rstudio的，有一个1.0.153的是一个旧的版本，目前官方最新的版本是1.1.383，aur上的rstudio-desktop-bin和rstudio-desktop-git都是这个最新版的。

下面看看网友的留言：

> The 1.0.153 version works without gstreamer0.10. However the rstudio team only published a 64 bits package (here : https://download1.rstudio.org/rstudio-xenial-1.0.153-amd64.deb) for debian 9 or Ubuntu 16. I tested it, it works fine and is no more dependent of the gstreamer0.10 package.

> Other 1.0.153 packages (for Ubuntu 12.04 or Fedora) still need gstreamer0.10 but they are released both for 32 and 64 bits architectures.


可以看到官网对同一个版本是放出了两个不同版本的分别是针对ubuntu12.04和ubuntu16.04的，而文件名中带**xenial**的就是针对ubuntu16.04版本的，另外针对ubuntu12.04的rstudio版本有两个依赖分别是**gstreamer0.10-base**和**gstreamer0.10-base-plugin**，可以用yaourt安装，我尝试过修改PKGBUILD文件，删掉这两个依赖成功安装rstudio-desktop-bin，但是启动不了rstudio，然后安装了这两个依赖就可以启动了。

由于aur上面的rstudio-desktop-bin使用的文件是**rstudio-1.1.383-amd64.deb**，也就是针对的是ubuntu12.04的，所以总觉得**rstudio-xenial-1.1.383-amd64.deb**会更先进一些，所以就想安装带xenial的版本，可是我直接修改PKGBUILD文件的source链接为<https://download1.rstudio.org/rstudio-xenial-1.1.383-amd64.deb>，并修改对应的MD5，可是安装不成功，出现错误。

不过好在看了这个链接的PKGBUILD：<https://pastebin.com/n2mzRfQR>，虽然这个链接针对的版本是rstudio-1.0.153，不过看到里面的source链接名是带xenial字样的，于是又做了修改把source链接改为<https://download1.rstudio.org/rstudio-xenial-1.1.383-amd64.deb>，再修改版本号为`1.1.383`，再修改MD5为：`fccec7cbf773c3464ea6cbb91fc2ec28`，居然还安装成功了，并可以安装成功，并不需要过时的两个依赖：**gstreamer0.10-base**和**gstreamer0.10-base-plugin**。

为什么不用**rstudio-desktop-git**？我尝试了，但是忘了哪个链接下载很慢，而且编译环境对于我来讲比较复杂，没安装成功。而且依赖比较多，不太想安装这个，虽然我很想试一下从源代码编译安装rstudio。


## 关于ibus与rstudio

其实很想用linux的搜狗输入法，但是由于搜狗输入法是基于fcitx的，而它却对rstudio不是很友好，虽然网上有一些方案处理，但是我没有尝试成功过，所以使用ibus + yong小小输入法。

最近在捣鼓manjaro，尝试安装了好几个不同版本的manjaro玩了一下。

首先是在manjaro-gnome上玩，也成功安装了rstudio-desktop-bin-1.1.383。然后又成功安装了ibus和yong小小输入法，开头还纳闷怎么输入`ibus-daemon`不能启动ibus，百度了一下，才知道gnome-shell这玩意整合了ibus，需要在**区域和语言**的地方添加输入法，其实这个整合也蛮不错的嘛，衰就衰在我没成功在rstudio上输入中文，无论是用ibus+yong还是ibus-pinyin。

接着在另外一台安装了manjaro-Cinnamon版本的台式电脑上也安装了rstudio-desktop-bin，发现可以用ibus+yong输入中文了，天哪，终于搞掂了？

然后呢，又在dell笔记本上把manjaro-gnome删掉，重新安装了一个manjaro-xfce版本，也是安装了rstudio-desktop-bin和ibus+yong，居然不能输入中文，我晕，然后又不知道怎么搞的，竟然又可以输入中文，最后才发现事实真相是这样的：

- 先启动ibus-daemon，再启动rstudio，可以输入中文。
- 先启动rstudio，再打开ibus-daemon，**不能**输入中文。

原来这个还有先后顺序的。

PS：其实rstudio for win版本对输入法（我用搜狗输入法）也是有一个小问题的，偶尔会出现搜狗输入法不见了，按`Ctrl + Space`也切换不出输入法，需要切换到其他程序，然后再切换回rstudio，输入法又回来了，于是又可以输入中文了。


## 安装xenial版rstudio的PKGBUILD


最后附上修改好的安装xenial版本的rstudio的PKGBUILD：

```
# Maintainer: Meow < leon.tty1 at gmail dot com >
 
# Get download links and md5 sums for latest version of RStudio desktop
## R code #############
#
#  require(XML)
#  page = htmlTreeParse("http://www.rstudio.com/products/rstudio/download/",useInternalNodes = T)
#  links = sapply(getNodeSet(page,'//table[@class="downloads"]/thead/tr/th[text()="Installers"]/../../..//a[contains(@href,".deb")]'),xmlGetAttr,'href')
#  md5sums = sapply(getNodeSet(page,'//table[@class="downloads"]/thead/tr/th[text()="Installers"]/../../..//a[contains(@href,".deb")]/../..//code'),xmlValue)
#  print(cbind(links,md5sums))
#
#######################
 
pkgname=rstudio-desktop-bin
pkgver=1.1.383
pkgrel=1
pkgdesc="A new integrated development environment (IDE) for R (binary version from RStudio official website)"
arch=('i686' 'x86_64')
license=('GPL')
url="http://www.rstudio.org/"
depends=('r' 'gstreamer' 'gst-plugins-base' 'hicolor-icon-theme' 'libxcomposite' 'libxslt' 'shared-mime-info' 'libxrandr' 'pandoc' 'pandoc-citeproc')
#makedepends=('patchelf')
conflicts=('rstudio-desktop' 'rstudio-desktop-git' 'rstudio-desktop-preview-bin')
provides=("rstudio-desktop=${pkgver}")
options=(!strip)
 
_x86md5=21ca14bffcdc1a2361ead2d763d0313d
_x64md5=fccec7cbf773c3464ea6cbb91fc2ec28
 
case "$CARCH" in
    'i686')
        _arch=i386
    _archx=
        md5sums=($_x86md5)
        ;;
    'x86_64')
        _arch=amd64
    _archx=64
        md5sums=($_x64md5)
        ;;
esac    
source=("https://download1.rstudio.org/rstudio-xenial-${pkgver}-${_arch}.deb")
install="$pkgname".install
 
package() {
 
    shopt -s extglob
 
  msg "Converting debian package..."
 
  cd "$srcdir"
  tar xpf data.tar.xz -C "$pkgdir"
  install -dm755 "$pkgdir/usr/bin"
 
  #cd "$pkgdir/usr/lib/rstudio/bin"
  #ln -sf /usr/lib/libncursesw.so.6 libtinfo.so.5
  #ln -sf /usr/lib/libedit.so.0  libedit.so.2
 
#  cd "$pkgdir/usr/lib/rstudio/bin/rsclang"
#  patchelf --set-rpath '$ORIGIN/..' libclang.so
 
  cd "$pkgdir/usr/lib/rstudio/bin/pandoc"
  ln -sf /usr/bin/pandoc ./
  ln -sf /usr/bin/pandoc-citeproc ./
 
#  cd "$pkgdir/usr/lib/rstudio/bin/plugins"
#  ls */*.so | xargs -n1 patchelf --set-rpath '$ORIGIN/../..'
 
  find "$pkgdir/usr" -type d -print0 | xargs -0 chmod 755
  find "$pkgdir/usr" -type f -name '*.so.*' -print0 | xargs -0 chmod 644
 
  cd "$pkgdir/usr/lib/rstudio/bin"
  ls libQt*.so.*| grep '\.[0-9]\{1,\}\.[0-9]\{1,\}\.[0-9]\{1,\}$'|
  while read x;do
    if [[ ! -e "${x%.+([0-9]).+([0-9])}" ]];then
      ln -s "$x" "${x%.+([0-9]).+([0-9])}"
    fi
  done
  ls lib*.so.* | grep '\.so\.[0-9]\{1,\}\.[0-9]\{1,\}$'|
  while read x;do
    if [[ ! -e "${x%.+([0-9])}" ]];then
      ln -s "$x" "${x%.+([0-9])}"
    fi
  done
 
  cd "$pkgdir/usr/bin"
  #ln -s -f ../lib/rstudio/bin/rstudio rstudio-bin
  echo '#!/bin/sh
export QT_DIR=/usr/lib/rstudio/bin
export QT_PLUGIN_PATH=$QT_DIR/plugins
export QT_QPA_PLATFORM_PLUGIN_PATH=$QT_PLUGIN_PATH/platforms
export KDEDIRS=/usr
exec /usr/lib/rstudio/bin/rstudio "$@"
' > "$pkgdir/usr/bin/rstudio-bin"
  chmod 755 "$pkgdir/usr/bin/rstudio-bin"
 
  sed -i 's|/usr/lib/rstudio/bin/rstudio|/usr/bin/rstudio-bin|' "$pkgdir/usr/share/applications/rstudio.desktop"
}
# vim:ft=sh tabstop=2 expandtab

```







