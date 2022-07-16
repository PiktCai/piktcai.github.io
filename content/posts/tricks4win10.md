---
title: 'Windows 10 使用技巧'
date: '2021-09-24'
tags: ['技术', '收集']
---
每次安装都会用到，遂记录。

---

## 1. 设置小鹤双拼

使用注册表项以实现便捷添加。

其内容是：

```powershell
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\InputMethod\Settings\CHS]
"UserDefinedDoublePinyinScheme0"="小鹤双拼*2*^*iuvdjhcwfg^xmlnpbksqszxkrltvyovt"
```

也可直接下载：

[xiaohe.reg](https://cdn.jsdelivr.net/gh/cilunga1024/ifpkw@master/attachments/xiaohe.reg?download=true)

## 2. 设置 UTC 时间

在 Powershell 中以管理员权限运行如下命令：

```powershell
reg add “HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation” /v RealTimeIsUniversal /d 1 /t REG_QWORD /f
```

## Ref

1. [wensonsmith ——《Win10 使用注册表一键添加小鹤双拼方案》](https://iwenson.com/win10-add-xiaohe/)
2. [delphij's Chaos —— 《将 Windows 10 的系统时钟时间设置为UTC》](https://blog.delphij.net/posts/2019/08/-windows-10-utc/)