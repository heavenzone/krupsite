---
title: "打开模块DevicePowerOn电源失败"
date: "2018-03-19"
slug: vmware-device-power-on-error
categories:
  - vmware
tags:
  - vmware
keywords:
  - vmware
---


# 问题

重装vmware后，打开以前的虚拟机，出现下面的错误`打开模块DevicePowerOn电源失败`：

```
无法打开内核设备“\\.\VMCIDev\VMX”: 重叠 I/O 操作在进行中。
你想要在安装 VMware Workstation 前重启吗?打开模块DevicePowerOn电源失败。
```

无法打开虚拟机。

# 解决办法

安装服务`3、磁盘映射功能`后解决。

