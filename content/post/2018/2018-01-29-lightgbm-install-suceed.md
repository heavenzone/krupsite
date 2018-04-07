---
title: lightGBM安装成功
author: Heaven Zone
date: '2018-01-29'
slug: lightgbm-install-suceed
categories:
  - lightGBM
  - R
tags:
  - R
  - lightGBM
  - 机器学习
description: 'lightGBM安装成功'
---


## lightGBM的安装

###  环境

- win10
- R version 3.4.3 (2017-11-30) -- "Kite-Eating Tree"
- Platform: x86_64-w64-mingw32/x64 (64-bit)
- Microsoft Visual Studio Community 2017
	- Microsoft Visual C++ 2017 (猜测需要)
	- Visual Studio Tools for CMake (猜测需要)
	- 在用VS打开`\LightGBM\windows`项目的时候，VS提示安装一些组件，不确定是哪些组件。
- [cmake](https://cmake.org/download/) 64bit
- Rtools 64bit

系统环境变量PATH

- `C:\RBuildTools\3.4\bin`
- `C:\Program Files\CMake\bin`
- `C:\Program Files\R\R-3.4.3\bin`

其实CMake和VS不知道是否二选一。

### 安装

#### 方法一

```
library(devtools)
options(devtools.install.args = "--no-multiarch")
install_git("https://github.com/Microsoft/LightGBM", subdir = "R-package")
```

#### 方法二

```
& 'C:\Program Files\R\R-3.4.3\bin\R.exe' CMD INSTALL --build . --no-multiarch
```

这样就直接安装好lightgbm包。另外也会在`\LightGBM\R-package`文件夹，也就是当前文件夹下生成了一个`lightgbm_2.1.0.zip`文件，貌似在Rstudio下`install.packages("d:/LightGBM/R-package/lightgbm_2.1.0.zip")`，系统提示不能安装，但是直接解压到扩展包的文件夹，然后重启Rstudio，扩展包lightgbm可以用，没问题。


#### 方法三（未尝试）

```
devtools::install_github("Laurae2/lgbdl", force = TRUE)
```

```
lgb.dl(commit = "master",
       compiler = "vs",
       repo = "https://github.com/Microsoft/LightGBM")
```

如果已经有已编译好的`lib_lightgbm.dll`，可以这样：

```
lgb.dl(commit = "master",
       libdll = "C:\\LightGBM\\windows\\x64\\DLL\\lib_lightgbm.dll", # YOUR PRECOMPILED DLL
       repo = "https://github.com/Microsoft/LightGBM")
```

如果使用GPU：

```
lgb.dl(commit = "master",
       compiler = "vs", # Remove this for MinGW + GPU installation
       repo = "https://github.com/Microsoft/LightGBM",
       use_gpu = TRUE)
```

### 参考资料

- <https://github.com/Microsoft/LightGBM/issues/912#issuecomment-329496254>
- <https://github.com/Microsoft/LightGBM/tree/master/R-package>
- <https://github.com/Microsoft/LightGBM/blob/master/docs/Installation-Guide.rst>

