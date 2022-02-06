---
title: 'Chrome OS 安装思路（旧）'
date: '2018-11-10'
tags: ['Chrome OS', 'PC']
showToc: true
TocOpen: false
---
***17/10/2020 更新：此方法现在看来已经过时，且无法解决TPM2设备镜像导致的种种问题，不再建议使用，仅供技术参考和交流学习。***

---

Chrome OS并不提供普通PC可安装的镜像，可是大多数Chromium OS无法满足大家的需求，所以我们可以挑战一个新思路：通过官方的Chrome OS恢复镜像，将其安装到PC上。 **说在前头，这个方法仅适用于支持UEFI启动方式的电脑，如果你的电脑只支持传统的Legacy，那就可以关掉这篇文章了。**

---

## 0. 所需材料

- 一个至少8G的U盘，并**备份好其中的数据**，我们会**清除这上面的所有数据**
- 一台电脑，并**备份好硬盘里的数据**，我们会**清除硬盘里所有的数据**
- FydeOS系统镜像。注意，***请不要使用普通Chromium OS的镜像***（如cloudready，Arnoldthebat版本……），因为这些版本中缺少`sgdisk`这个工具
- 一个与你电脑配置接近的Chrome设备的恢复镜像，下载地址：[https://cros-updates-serving.appspot.com/](https://cros-updates-serving.appspot.com/) （需翻墙），如果你不方便翻墙，你也可以去[这个文件](https://dl.google.com/chromeos/recovery/recovery.conf)里寻找下载链接。不论是在前一个地址还是后一个文件里获得的下载链接均无需翻墙便可以下载，下载后解压缩得到的`.bin`文件就是我们要的

## 1. 准备工作

- 将FydeOS的镜像写入U盘，推荐使用Etcher，当然，Rufus，win32diskimager也行
- 重启电脑，从刚写入好的U盘开机，并创建一个用户，进入到系统桌面
- 在文件管理器中把以`.bin`结尾的Chrome OS镜像复制到下载内容目录，并重命名为`chromeos.bin`，如果在复制过程中出现死机等情况，可以直接在FydeOS中下载zip文件并解压，然后再重命名

## 2. 写入硬盘

- 打开浏览器页面，再按`Ctrl+Alt+T`，这时Crosh Shell页面便会出现，此刻显示的应该是

```bash
   crosh>
```

- 我们先输入命令：`shell`，然后回车，便进入了Bash Shell

```bash
    chronos@localhost / $
```

- 这时我们输入命令：`cd ~/Downloads`，进入下载内容目录

```bash
    chronos@localhost ~/Downloads $
```

- 接下来我们输入：`sudo su`，切换到root用户，终端显示如下

```bash
    We trust you have received the usual lecture from the local System
    Administrator. It usually boils down to these three things:

        #1) Respect the privacy of others.
        #2) Think before you type.
        #3) With great power comes great responsibility.

    Password:
```

- 这时候输入`chronos`，密码不会显示出来，光标也不会动，输完回车即可，终端会变成这样

```bash
    localhost Downloads #
```

- 输入`fdisk -l`，查询一下你所要安装到哪块硬盘，通常，你电脑里的硬盘会以`sdx`的形式显示，当然也可能是`mmcblk0`或者`nvme0`。根据提示的磁盘空间大小和已分区的数量，判断你要安装到的那块硬盘，比如`sda`。以下以`sda`为例，**以下所有命令中的`/dev/sda`都要替换成你要安装的那块硬盘**，接下来将Chrome OS镜像写入硬盘，就像这样

```bash
    dd if=chromeos.bin of=/dev/sda iflag=fullblock oflag=sync status=progress bs=4M
```

然后，等待写入完成，进入下一个阶段。

## 3. 一些必要更改

### 3.1 扩大并格式化用户数据分区

如果你需要将硬盘中全部空间都分配给Chrome OS，请按照步骤来，如果不打算如此，并且你比较熟悉Linux操作，则可跳过前两步。

- 现在，我们输入

```bash
    partx -s -b -g -o start /dev/sda1
```

- 再输入

```bash
    lsblk -b -d -n -o size /dev/sda
```

- 找一个计算器，将`lsblk -b -d -n -o size /dev/sda`得到的结果除以512，再减去`partx -s -b -g -o start /dev/sda1`得到的结果，最后减去33，
- 接下来，我们输入

```bash
    sgdisk -e /dev/sda
```

- 然后输入`cgpt add -i 1 -s 计算器算出来的数值 /dev/sda`，以此扩大用户数据分区(如果你比较熟悉Linux操作，也可以自行修改该分区的大小，但至少要4G)，就像这样，

```bash
    cgpt add -i 1 -s 112815887 /dev/sda
```

- 接着，终止`cros-disks`服务，

```bash
    initctl stop cros-disks
```

- 格式化用户数据分区，

```bash
    mkfs.ext4 -F /dev/sda1
```

### 3.2 更改启动选项

- 挂载`EFI`分区

```bash
    mount /dev/sda12 /mnt
```

- 更改默认启动项为`local image A`

```bash
    sed -e 's/defaultA=2/defaultA=0/' -i /mnt/efi/boot/grub.cfg
```

- 开启开发者模式（如果不开启此选项，则无法安装第三方来源的安卓应用和使用Crosh Shell）

```bash
    sed -e 's/cros_efi/cros_efi cros_debug/' -i /mnt/efi/boot/grub.cfg
```

## 4. 大功告成

这时候便可以重启并拔掉U盘，如无意外，就可以享用你的Chrome OS啦！ 如果出现`Your system is repairing itself. Please wait`字样也不用慌张，稍等五分钟它会自动重启，这下就同样可以享用你的Chrome OS啦。

## 5.其他方案

如果这篇教程无法很好地适用于你的电脑，你可以试试外国网友的[Project Croissant](https://github.com/imperador/chromefy)。