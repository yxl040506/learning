# HTTP请求中的Keep-Alive模式，是怎么区分多个请求的？
## Keep-Alive模式
我们都知道HTTP是基于TCP的，每一个HTTP请求都需要进行三步握手。如果一个页面对某一个域名有多个请求，就会进行频繁的建立连接和断开连接。所以HTTP 1.0中出现了Connection: keep-alive，用于建立长连接，即我们所说的Keep-Alive模式。下图是普通模式和长连接模式的请求对比：
 

<img src='./img/httpkeepalive.png' />
 

## HTTP/1.0中默认使用Connection: close。在HTTP/1.1中已经默认使用Connection: keep-alive。

通过对比可以看出，Keep-Alive模式更加高效，因为避免了连接建立和释放的开销。但是，如果一个连接是不会断开的，那么多个请求之间如何进行区分呢？也就是说浏览器是如何知道当前请求已经完成了呢？为了解决这个问题，HTTP对header中又添加了一个Content-Length字段。

## 解决方案一：Content-Length
- 判断原理：Content-Length表示实体内容的长度。浏览器通过这个字段来判断当前请求的数据是否已经全部接收。
- 静态资源：当浏览器请求的是一个静态资源时，即服务器能明确知道返回内容的长度时，可以设置Content-Length来控制请求的结束。
- 动态内容：但当服务器并不知道请求结果的长度时，如一个动态的页面或者数据，Content-Length就无法解决上面的问题，这个时候就需要用到Transfer-Encoding字段。

## 解决方案二：Transfer-Encoding
- 最后一个chunk的长度为0时
- Transfer-Encoding是指传输编码，还有一个类似的字段叫做：Content-Encoding。
- 两者的区别
	- Content-Encoding用于对实体内容的压缩编码，比如Content-Encoding: gzip；
	- Transfer-Encoding则改变了报文的格式，
	- 比如上面的问题中，当服务端无法知道实体内容的长度时，就可以通过指定Transfer-Encoding: chunked来告知浏览器当前的编码是将数据分成一块一块传递的。当然, 还可以指定Transfer-Encoding: gzip, chunked表明实体内容不仅是gzip压缩的，还是分块传递的。最后，当浏览器接收到一个长度为0的chunked时， 知道当前请求内容已全部接收。