### 一、问题？

浏览器发起http请求的协议版本的来源？

1、浏览器默认？

2、服务器设置？

服务器设置：浏览器怎么获取服务的http版本号，猜想应该是建立连接的时候

### 二、http请求分析

[https://cn.bing.com/search?q=XX&form=CHRDEF&sp=-1&pq=xx&sc=8-2&qs=n&sk=&cvid=93B350A2F6BF4C2BB5D66704FE4B51BC&ghsh=0&ghacc=0&ghpl=](https://cn.bing.com/search?q=XX&form=CHRDEF&sp=-1&pq=xx&sc=8-2&qs=n&sk=&cvid=93B350A2F6BF4C2BB5D66704FE4B51BC&ghsh=0&ghacc=0&ghpl=)

站点：[https://cn.bing.com/](https://cn.bing.com/)

请求路径：/search   ，不同的请求路径对应不同的handler   mmorpg自定义协议，不同的协议编号对于不同的hander

参数：以?开始，参数为xx =bb,参数之间与&间隔

http请求有一个content_length   包的长度，本质是一个TCP服务，协议为http
