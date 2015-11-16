# Choosing a Persistence Strategy for Your Application

- Properties
- Database
- Filesystem
- What kind of data storage should I use?

## 目标

>Titanium 提供多种方法来保存数据到用户的手机设备. 在本章, 你将会查阅到那些options, 选择更好的技术来解决你的问题.

## 内容

> 即使是最基础的app通常也有一些存数的必要. 一如既往, Titanium 通过自身的 convenient, uniform interface 来提供所有的本地function访问.  为了是用设备的本地存储空间, 以下objects是必须的:

- Titanium.App.Properties is ideal for storing application-related settings
- Titanium.Database gives access to local SQLite3 databases
- Titanium.Filesystem facilitates file and directory manipulation

Each of these enable data to persist on a device across application restarts, power cycles, re-installation and even migration to a new device.

>这个设备上每一个这些启用的数据持续在一个设备上, 跨平台包括重启,关机循环, 重装或者迁移到新设备上.

## Properties 特性

> Titanium.App.Properties 的API 提供一个轻量级的 key/value 存储. Titanium 的properties 提供读写 string, interger, boolean and array values的方法. 任何数据可以JSON化,存储到application property. Properties 是存储小块数据的最好地方. 例如 app的配置信息, 但是如果你有部分的数据需要存,或者需要建立一些关系数据. 你最好存储到数据库里.

## Database 数据库

> IOS 和 Android 都有 SQLite3 数据库的引擎, 如果你不熟悉 SQLite, 去学一下.  当下, SQLite 是一个简单的数据库管理系统, 支持 SQL92的规范. 

> 数据库是存储结构化数据,交互支持的最好地方. 你可以建表,字段.存数数据关系. Data 在数据库中持续到用户卸载app或者重写删除数据.

## Filesystem 文件系统

> Titanium.Filesystem 可以让你从用户设备上读取文件. 大点说, 你的app 限制 读取 ,写入 它自身的文件. 并且你可以保存文件到内 外存储空间(如SD card). 文件 是存图片或者其他二进制数据的最好地方.

## What kind of data storage should I use?

The decision about which of the three local storage options you choose is usually determined by the following:

以下是3种本地存储方式 可供你选择使用:

1. **Application Properties** - used when one or all of the following is true:

 - the data consists of simple key/value pairs
 - the data is related to the application rather than the user
 - the data does not require other data in order to be meaningful     or useful
 - there only needs to be one version of the data stored at any one time

2. **Database** - used this when one or all of the following is true:

	- there are many similar data items
	- items of data relate to each other
	- you require flexibility over how the data will be presented when you retrieve it
	- the data accumulates over time, such as transaction, logging or archiving data

3. **Filesystem** - used when one or all of the following is true:

	- the data is already provided in file format
	- the data is an image file

> 虽然 本地数据库有能力存图片为二进制形式, 最好别这么做. 替代方式, use Titanium.Database 存储图片文件的path和名称.  Titanium.Filesystem来管理文件.