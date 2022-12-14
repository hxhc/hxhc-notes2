---
{"dg-publish":true,"permalink":"/4-web-clip/one-drive-zotero/"}
---


# 利用OneDrive实现两台电脑间Zotero文献库的同步 - 知乎

个人主力电脑有两台，在办公室则用台式，在其它场合则用笔记本，故需要在两台电脑间同步文献。之前一直是用的 zotero 自带的 300M 云空间，高枕无忧直到某天被告知空间已满上传失败，于是琢磨着换成外部的云同步。而手上正好又有现成的 1TB 的 ondrive，就简单尝试了一下。

## 1. Mklink指令

直接将数据文件放入 OneDrive，在两台电脑上的将 zotero 的数据文件路径指定到其上，这种简单的方式被 zotero 报 warning 后，就想到了 Mklink 指令。

![](https://pic2.zhimg.com/v2-dec4bbe49fe1d76f442c1a216d16b3d9_b.jpg)

> [!quote] 官方警告的原因：
>  *Storing the Zotero data directory directly in a cloud storage folder is extremely likely to corrupt your Zotero database and should not be done.* (The same is true of most database-backed programs.) The forums contain [many](https://www.zotero.org/forum/discussion/13359/) [threads](https://www.zotero.org/forum/discussion/27900/synching-to-dropbox/) [about](https://www.zotero.org/forum/discussion/6128/dropbox-and-zotero-15-case-conflicts/) [the problems](https://www.zotero.org/forum/discussion/24593/backing-up-a-large-database-without-corrupting-it/) that users have faced with Dropbox- or Google Drive–based setups.（将 Zotero 数据目录直接存储在云存储文件夹中极有可能损坏 Zotero 数据库，因此不应该这样做。(大多数数据库支持的程序也是如此。)这些论坛包含了很多关于用户在 Dropbox 或 Google Drive 设置时遇到的问题的帖子。）

Mklink指令是window下用于创建符号链接的工具，在NTFS文件系统中可以创建目录或文件符号或硬链接。具体可以参见[mklink | Microsoft Docs](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mklink)以及[win10下使用mklink命令给C盘软件搬家 - LifeOfCoding - 博客园 (cnblogs.com)](https://www.cnblogs.com/life-of-coding/p/10871831.html)

![](https://pic3.zhimg.com/v2-d5e336f59783e20d12817d1f80497c36_b.jpg)

## 2. Zotero的数据文件

Zotero的默认数据文件路径是"C:\\Users\\用户名\\Zotero"，文件目录如下图所示

![](https://pic2.zhimg.com/v2-c5e363fb6bc713e22585a8b681745c31_b.jpg)

其中所有文献附件全部存放在storage文件夹中，所以我们只需同步这个文件夹就行。

## 3. 创建数据文件间的链接

首先创建一台电脑上zotero数据文件和onedrive的链接关系，CMD命令（管理员权限打开）如下：

```shell
Mklink /j "E:\OneDrive\Zotero_storage" "C:\Users\用户名\Zotero\storage"
```

这样就在OneDrive中为storage文件创建了一个名为`Zotero_storage`的“入口”（该文件夹自动生成，不需要提前新建，否则会报错“当文件已存在时，无法创建该文件”）。该入口文件不占用具体的空间，对该入口文件夹的访问和内容修改都会被链接到对原始storage文件的访问和内容修改（但又不同于普通快捷方式，普通快捷方式在onedrive中无法同步相应内容）。

接着在另一台电脑上创建onedrive上`Zotero_storage`文件和的本地zotero数据文件的链接关系。首先需要删除本地Zotero文件中的storage文件（路径`C:\\Users\\用户名\\Zotero\\storage` ），然后用Mklink指令创建一个onedrive中数据文件的入口替代。CMD指令如下：

```shell
Mklink /j "C:\Users\用户名\Zotero\storage" "C:\Users\用户名\OneDrive\Zotero_storage"
```

至此实现了两台电脑间的文献库同步

![](https://pic3.zhimg.com/v2-aa34851dc94103cacfdbc405b0a9202a_b.jpg)

不足之处是偶尔批注不能同步成功，也可能是我两台电脑操作时间间隔过短，具体有待检验。（已确定是操作时间间隔过段，会导致版本冲突不能覆盖。正常使用则笔记同步很顺利）


<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">




==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


# Text Elements
Zotero cloud ^PLEDi3Rg

data ^AVh1fYRv

zotero Local in master PC ^2nYVPodO

zotero Local in slave PC ^2upcqXHR

data ^QrWrKQ1Q

attachments ^k1ZXlaVj

OneDrive master ^YE0vFE1f

Actual location
E:\OneDrive\Zotero_storage ^7adiQMQ6

Link (MkLink):
C:\Users\user\Zotero\storage ^i73QmlQu

OneDrive slave ^A8yHYGqO

Actual location
C:\Users\user\OneDrive\Zotero_storage ^d7sckug2

Link (MkLink):
C:\Users\user\Zotero\storage ^Rzx7vmWl

attachments ^03hIh8kH


# Embedded files
198ad55870cdce7b42ae93ba02e33f9fecbd457b: [[~Attachments/zotero_folder_setting.png]]
11e7583f9c4c67d03a59d021fa327ca2bc682d63: [[~Attachments/zotero_zotfile_setting.png]]



</div></div>


## 4. 其他参考

1.  [sync[Zotero Documentation]](https://www.zotero.org/support/sync)
2.  [Zotero 同步 Onedrive - 123456 的文章 - 知乎](https://zhuanlan.zhihu.com/p/31869164)