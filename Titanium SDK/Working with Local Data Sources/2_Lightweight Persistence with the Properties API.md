# Lightweight Persistence with the Properties API

- Reading and Writing Properties
- Storing JS objects as JSON in properties
- Hands-on Practice
- References and Further Reading

## Objective

>在本章, 我们将深入挖掘 Ti.App.Properties API. 你将会学到 怎么存储简单或者复杂的数据到 app properties中, 并且 取出.

## Contents

> iOS and Android 都存储 app properties 到文件系统的指定文件中. iOS的 properties 是 NSUserDefaults, 存储在 app的library目录下的 名为.plist 文件. Android 存储到标准的 xml文件到 /data/data/com.domainname.appname/shared_prefs/titanium.xml 中. Titanium 提供 一个合适的方式去 set get app properties. 这个方式就是 Titanium.App.Properties API.

> properties 并没有指定 存储数据的大小 数量规定. 然而, 一个应用的 property data是随应用启动加载到内存中, 知道关闭应用. 这能快速取用, 但是这同时增加的应用的内存使用成本.**

## Reading and Writing Properties

Titanium.App.Properties has six sets of get/set methods for handling six different data types:

- **getBool() / setBool()**: for booleans (true, false)
- **getDouble() / setDouble()**: for double-precision floating point numbers
- **getInt() / setInt()**: for integers
- **getList() / setList()**: for arrays
- **getString() / setString()**: for strings

>get 方法 接收 property name 和它的默认值,
如果property之前没有被set, 默认值会返回. 每一个set方法 包含 值键对.
如下示范:

```
var window = Titanium.UI.createWindow({
	backgroundColor:'#999'
});
var myArray = [ { name:'Name 1', address:'1 Main St'},
				{name:'Name 2', address:'2 Main St'}, 
				{name:'Name 3', address:'3 Main St'},
				{name:'Name 4', address:'4 Main St' } ];

Ti.App.Properties.setString('myString','This is a string');
Ti.App.Properties.setInt('myInt',10);
Ti.App.Properties.setBool('myBool',true);
Ti.App.Properties.setDouble('myDouble',10.6);
Ti.App.Properties.setList('myList',myArray);
// **********************************************
// Notice the use of the second argument of the get* methods below
// that would be returned if no property exists with that name
// **********************************************
Ti.API.info("String: "+Ti.App.Properties.getString('myString','This is a string default'));
Ti.API.info("Integer: "+Ti.App.Properties.getInt('myInt',20));
Ti.API.info("Boolean: "+Ti.App.Properties.getBool('myBool',false));
Ti.API.info("Double: "+Ti.App.Properties.getDouble('myDouble',20.6));
Ti.API.info("List: "+Ti.App.Properties.getList('myList'));
window.open();
```

This code outputs the following results:

```
String: This is a string
Integer: 10
Boolean: true
Double: 10.600000381469727
List:
{ 'address' : '1 Main St' 'name' : 'Name 1', },
{ 'address' : '2 Main St' 'name' : 'Name 2', },
{ 'address' : '3 Main St' 'name' : 'Name 3', },
{ 'address' : '4 Main St' 'name' : 'Name 4', }

```

## Storing JS objects as JSON in properties

If you have a complex Javascript object, you can convert it to a Titanium.App.Properties.setString() method.

```
var window = Titanium.UI.createWindow({
	backgroundColor:'#999'
});
var weatherData = { "reports" : [ 
								{ "city": "Mountain View", "condition": "Cloudy", "icon": "http://www.worldweather.org/img_cartoon/pic23.gif" }, 
								{ "city": "Washington, DC", "condition": "Mostly Cloudy", "icon": "http://www.worldweather.org/img_cartoon/pic20.gif" }, 
								{ "city": "Brasilia", "condition": "Thunderstorm", "icon": "http://www.worldweather.org/img_cartoon/pic02.gif" } 
								] };
									
Ti.App.Properties.setString('myJSON', JSON.stringify(weatherData));
var retrievedJSON=Ti.App.Properties.getString('myJSON', 'myJSON not found');
Ti.API.info("The myJSON property contains: " + retrievedJSON);
window.open();
```

This will output the following to the log:


The myJSON property contains: 

{"reports":[{"city":"Mountain View","condition":"Cloudy","icon":"http://www.worldweather.org/img_cartoon/pic23.gif"},{"city":"Washington, DC","condition":"Mostly Cloudy","icon":"http://www.worldweather.org/img_cartoon/pic20.gif"},{"city":"Brasilia","condition":"Thunderstorm","icon":"http://www.worldweather.org/img_cartoon/pic02.gif"}]}


This stored JSON string can later be converted back to a Javascript object using JSON.parse():

```
var myObject = JSON.parse(Ti.App.Properties.getString('myJSON'));
```