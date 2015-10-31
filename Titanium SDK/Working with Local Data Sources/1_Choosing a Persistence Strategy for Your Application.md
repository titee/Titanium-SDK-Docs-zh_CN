# Choosing a Persistence Strategy for Your Application

- Properties
- Database
- Filesystem
- What kind of data storage should I use?

## Objective 目标

> Titanium provides various means to save data to the user's mobile device. In this chapter, you will examine those options and select the best technique for a given situation.

**Titanium 提供多种方法来保存数据到用户的手机设备. 在本章, 你将会查阅到那些options, 选择更好的技术来解决你的问题.**

## Contents 内容

> Even the most rudimentary applications usually have some data storage requirements. As always, Titanium provides access to all the native functionality via its convenient, uniform interface. To use a device's local storage, the following objects are needed:

**即使是最基础的app通常也有一些存数的必要. 一如既往, Titanium 通过自身的 convenient, uniform interface 来提供所有的本地function访问.  为了是用设备的本地存储空间, 以下objects是必须的:**

- Titanium.App.Properties is ideal for storing application-related settings
- Titanium.Database gives access to local SQLite3 databases
- Titanium.Filesystem facilitates file and directory manipulation

Each of these enable data to persist on a device across application restarts, power cycles, re-installation and even migration to a new device.

**这个设备上每一个这些启用的数据持续在一个设备上, 跨平台包括重启,关机循环, 重装或者迁移到新设备上.**

## Properties 特性

> The Titanium.App.Properties API provides a lightweight key/value store. Titanium provides methods for reading and writing string, integer, boolean, and array values in properties. Any data that can be JSON serialized can be stored in an application property. Properties are a great place to store small chunks of data, such as configuration data for your app. But if you've got a lot of data to store, or you need to set up relationships between those data points, you'd be better served putting it in a database.
 
**Titanium.App.Properties 的API 提供一个轻量级的 key/value 存储. Titanium 的properties 提供读写 string, interger, boolean and array values的方法. 任何数据可以JSON化,存储到application property. Properties 是存储小块数据的最好地方. 例如 app的配置信息, 但是如果你有部分的数据需要存,或者需要建立一些关系数据. 你最好存储到数据库里.**

## Database 数据库

> Both iOS and Android include a SQLite3 database engine. If you're not familiar with SQLite, you might want to take a brief side trip to http://sqlite.org. In a nutshell, SQLite is a simplified database management system that supports most of the SQL92 specification, including most of the familiar SQL statements and even transactions.

**IOS 和 Android 都有 SQLite3 数据库的引擎, 如果你不熟悉 SQLite, 去学一下.  当下, SQLite 是一个简单的数据库管理系统, 支持 SQL92的规范. **

> The database is the appropriate place to store lots of structured data or when you need transactional support. You can define tables and columns, and enforce relationships between tables just as you would with a SQLServer or MySQL database. Data persists in the database until the user uninstalls your app or until you overwrite or remove the data.

**数据库是存储结构化数据,交互支持的最好地方. 你可以建表,字段.存数数据关系. Data 在数据库中持续到用户卸载app或者重写删除数据.**

## Filesystem 文件系统

> Titanium.Filesystem let's you read from and write to files on the user's device. Broadly speaking, your app is restricted to reading and writing its own files and cannot access files created by other apps. On Android devices, your app can save files to internal or external (SD card) storage. Files are a great place to store images or other binary data.

**Titanium.Filesystem 可以让你从用户设备上读取文件. 大点说, 你的app 限制 读取 ,写入 它自身的文件. 并且你可以保存文件到内 外存储空间(如SD card). 文件 是存图片或者其他二进制数据的最好地方.**

## What kind of data storage should I use?

The decision about which of the three local storage options you choose is usually determined by the following:

**以下是3种本地存储方式 可供你选择使用:**

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

> Although the local database has the capability to store images in blob (binary) format, this won't lead to optimal performance from your application. Instead, use Titanium.Database to store the image file path and name in the database, and Titanium.Filesystem to manage the physical files.

**虽然 本地数据库有能力存图片为二进制形式, 最好别这么做. 替代方式, use Titanium.Database 存储图片文件的path和名称.  Titanium.Filesystem来管理文件.**

## References and Further Reading 延伸阅读

### API docs:

 - Properties API
 - Database API
 - Filesystem API
 - Persistence demo app
 - http://sqlite.org

## Summary

In this chapter, you examined the local storage options available to your Titanium apps. And we gave you some recommendations of which option to use in various situations. While we didn't dig into the implementation details yet, you got a chance to see when to use each of these persistence options. In the upcoming sections, we'll dig deep into each storage option.
Created with Scroll EclipseHelp Exporter for Confluence.