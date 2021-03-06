# 缓存
## 一、缓存是什么
- 将经常访问的数据复制到*靠近应用程序*的快速存储
	- 缓存，一般有与之相对应的离应用程序更远的 持久存储 
	
#### 作用：
- 提供系统的*性能*和*伸缩性* 
- 改善客户端应用程序的响应时间

#### 引入缓存带来的问题
- 数据不一致问题
- 系统复杂度 

#### 应用场景
- 数据相对静态
- 数据访问速度相对缓存较慢
- 数据激烈的资源竞争
- 数据与客户端距离较远，访问速度慢

#### 缓存的分类
- 专用缓存：
	- 存在哪：进程中的内存缓存
	- 优点：访问快
	- 缺点：大小有限：受限于托管进程的计算机上可用内存大小
	- 应用：
		- 适度的静态数据量
- 共享缓存
	- 存在哪：服务器集群
	- 优点
		- 伸缩性：可轻松通过堆机器来扩展缓存
		- 引用程序只会将请求发送到缓存服务
	- 缺点：
		- 缓存的访问速度较慢，因为不再保留在应用程序实例本地

## 二、缓存更新模式
- Cache Aside
	- 应用程序先更新数据到数据库
	- 应用程序再让缓存失效
- ReadWrite Through
	- 读未命中：
		- 缓存服务器负责从数据库加载最新数据
	- 写数据时更新缓存：
		- 如果命中缓存，则更新缓存，有缓存服务器更新数据库
- Write Behind
	- 更新数据时，只更新缓存，不更新数据库
	- 缓存异步、批量更新数据库  



	
