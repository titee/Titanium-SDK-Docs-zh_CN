# Working with Local Data Sources

>即使是最基础的app通常也有一些存数的必要. 可能你想保存用户的名字 或者配置. 你的app 会在没网的情况下 下载产品信息来保证可用, 或者, 可能用户使用你的应用拍照片,稍后你要保存成files. 在这种情况下, 存数数据到用户的设备上是不明智的. 本章, 你会探索 Titanium的 uniform, cross-platform methods 来存储本地数据.

## 本章目的

1.Choosing a Persistence Strategy for your Application

>在本章, 你将会查阅到多种,存储本地数据到用户设备上. 并且决定考虑更好的使用每一种技术.

2.Lightweight Persistence with the Properties API

>接下来, 你将会学到怎样用app的资源 去存储简单 复杂的数据, 以及之后怎么取出.

3.Working with a SQLite Database

>在本章,  你将学到 用Titanium.Database module 与 SQLite3 RDMS 交互.

4.Filesystem Access and Storage

>最后, 你将会学到怎么使用 Titanium.Filesystem module 操控文件和目录. 

So let's get started.