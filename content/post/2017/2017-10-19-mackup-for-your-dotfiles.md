---
title: "Mackup for your dotfiles"
author: "Heaven Zone"
date: "2017-10-19"
categories: ["Linux"]
tags: ["mackup", "dotfiles", "linux"]
---



## 简介

最近学会了用git管理一些文件，并学会用R blogdown + github + netlify做博客网站，于是想怎么用git来管理linux下面的配置文件呢？那么多配置文件在不同文件夹，岂不是要建很多个repo？也想到把配置文件全部放在一个文件夹，然后做链接，但是操作起来还是比较麻烦，百度了一下，发现还真的有不少的方案可以使用，可以看下面参考资料部分。

这里介绍用mackup，作者应该是用mac的，不过linux也可以使用。

我用的是manjaro，直接`yaourt mackup`就安装成功了。

## 参考资料

关于dotfiles的资料可以参考下面几个链接：

- <http://dotfiles.github.io>
- <https://wiki.archlinux.org/index.php/Dotfiles>
- <https://github.com/kobus-v-schoor/dotgit>
- <https://github.com/lra/mackup>


## 用法

- `mackup backup`

这个命令实际上是做了下面三个步骤：

1. `cp`复制配置文件到你指定用来备份的文件夹
2. `rm`删除原来的配置文件
3. `ln`在原来的配置文件处建立链接文件到备份的配置文件

- `mackup uninstall`

这个命令是用来恢复原来模样，

1. `rm`删除链接文件
2. `cp`复制备份的配置文件到原来的位置
3. 备份文件夹里面已备份的配置文件保留在那里

- `mackup restore`

这个命令是用来在新电脑里面恢复这些配置用的，例如你用`mackup backup`做了备份，用U盘复制到另外一台电脑，就可以用`mackup restore`命令恢复配置，两台电脑的配置就同步了。当然也可以用git来管理。


## mackup支持的保存方式

- dropbox
- google drive
- copy
- icloud
- box
- **file_system**

下面将用**file_system**的方式做介绍。

mackup支持直接输入软件名称就可以直接备份相关的配置文件，不需要手动指定配置文件，支持的软件列表可以查看这里：<https://github.com/lra/mackup>。


## 备份指定的配置文件

### `.mackup.cfg`文件

- `touch .mackup.cfg`建立mackup的配置文件
- `mkdir .mackup`建立用于保存自建应用的配置文件
- `cd .mackup && touch myapp1.cfg && touch myapp2.cfg`建立两个配置文件


然后`vi .mackup.cfg`输入下面内容：

```shell
[storage]
engine = file_system
path = .dotfiles
directory = Mackup

[applications_to_sync]
myapp1
bash

[applications_to_ignore]
myapp2
```

#### storage：储存方式

`engine`是用来设置储存方式的，你可以用dropbox等网盘，不过支持的基本都是国外的，所以我们这里使用file_system方式。

`path`设置备份配置文件的路径，该路径是相对于主目录的，`.dotfiles`文件夹将会在主目录`~/`下建立。

`directory`设置保存配置文件的文件夹，这个文件夹将会在设置的path下生成，默认是Mackup。


#### applications_to_sync：同步的应用

这部分可以直接输入mackup默认支持的应用名称，例如`bash`，当然也可以到这里查看详细列表，链接：<https://github.com/lra/mackup>。

上面myapp1是按照`.mackup`文件夹下面的`myapp1.cfg`的文件名来输入的，不要扩展名`.cfg`。

#### applications_to_ignore：忽略的应用

这里忽略的应用的写法跟**同步的应用**部分一样。


这个时候可以输入`mackup list`查看到myapp1和myapp2已经在列表中了。

### 测试mackup

- 在`~/`下面建立测试文件夹和文件

```shell
mkdir test11
mkdir test12
mkdir test2
touch test11/config11
touch test12/config12
touch test2/config2
```

- `vi myapp1.cfg`输入以下内容：

```
[application]
name = test1

[configuration_files]
test11/config11
test12
```

- `vi myapp2.cfg`输入以下内容：

```
[application]
name = test2

[configuration_files]
test2
```

建立好测试环境，现在就可以测试了：

```shell
$ mackup backup
$ ls -al
$ mackup uninstall
$ mackup restore
```

`la -al`输出可以看到`test2`文件夹并没有生成链接文件，因为在`applications_to_ignore`输入了myapp2这个应用了，所以忽略了，所以这个忽略应用的作用主要是用来忽略`.mackup`文件夹下面的某些配置文件，而不删除这些文件。

最后就是用git管理备份文件夹了。

**Enjoy**!