# 第一章 延迟与带宽

## 对网络通信决定性影响的2个方面
- **延迟**：分组从信息源发送到目的地所需要的时间
- **带宽**：逻辑或物理通信路径最大的吞吐量 

## 延迟的构成
PS：下文出现的消息、数据、分组暂时看成概念类似的东西

- **传播延迟**：消息从发送端到接收端需要的时间
	> `传播时延 = 传播距离 / 信号在介质中传播速度`
	
	> **传播时延**是一个比特从一台路由器传播到另一台路由器所需要的时间，它是两台路由器之间距离的函数，而与分组长度或者链路传输速率无关。
- **传输延迟**：把消息中的所有比特转移到链路中需要的时间
	> `发送时延 = 数据长度 / 发送速率`，也可以说成是`消息长度`和`链路速率`的函数
	
	> 从网卡或者路由器队列递交网络链路所需要的时间
	
	> **传输时延**是路由器推出分组所需要的时间，与两台路由器之间的距离无关（传输速率就是路由器或者主机网卡，把要进行传送的比特流，放到输出链路上的速度）
- **处理延迟**：处理`分组首部`、`检查位错误`及`确定分组目标`所需的时间
 > 分组到达路由器之后，路由器必须检测分组的首部，以确定出站路由，并且还可能对数据进行检查，这些都要花时间。这些检查通常由硬件完成
- **排队延迟**：到来的分组排队等待处理的时间
> 分组到达的速度超过了路由器的 处理能力，那么分组就要在入站缓冲区排队


# 第二章 TCP
## 三次握手
## 拥塞预防及控制
- 拥塞崩溃现象：
	- 原因：节点间带宽容量不对称
	- 场景：IP和TCP同时使用的时候，IP网关连接不同带宽的网络时
	- 现象：分组的往返时间已经超过了所有主机的最大中断间隔，主机会不断重发，制造越来越多的数据报副本，缓冲区将被数据报副本填满。
	- 删除缓冲区的副本不再能解决问题，因此引入如下解决方案
- 流量控制：
	- 关键词： **流量控制窗口(或接收窗口rwnd)**
	- 目的：预防发送端向接收端发送过多数据，否则接收端会因为负载重、忙碌、缓冲区大小有限而无法处理。
	- 机制：tcp连接的每一方都要通告自己的接收窗口(rwnd)，包括能够保存数据缓冲区空间的大小
	
		每个ACK分组都会携带相应的最新rwnd，两端动态调整数据流速。使之适应发送端和接收端的容量及处理		能力
	- 缺陷：流量控制智能预防发送端向接收端发送过多数据，但是没有预防向网络中发送过多数据
		某个中间的网关分组越积越多，最终会导致分组被删除，从而降低网络传输效率。
		因此需要一个估算机制，并根据网络状况改变发送速度
- 慢启动
	- 关键词： **拥塞窗口（cwnd）** 
	- 机制
		* 新增变量：拥塞窗口大小（cwnd）。不会交换这个变量，在发送端维护这个私有变量。
		* 新增规则：即客户端与服务器之间**最大可以传输(未经 ACK 确认的)数据量**取 rwnd 和 cwnd 变量中的最小值。（rwnd和cwnd中较小的一个控制发送方发送数据的速率）
		* 如何确定拥塞窗口大小：根据一个阈值（ssthresh），动态变化的。
		
	- 详细描述

	  初始的拥塞窗口一般是4 段(RFC 2581)。<br>
	  每收到一个 ACK， 慢启动算法就会告诉服务器可以将它的 cwnd 窗口增加 1 个 TCP 段
	  
	- 减少慢启动的影响
		
	   	为减少增长到拥塞窗口的时间，可以减少客户端与服务器之间的往返时间。<br>
      	比如， 把服务器部署到地理上靠近客户端的地方。要么，就把初始拥塞窗口大小增加到 RFC 9828 规定的 10段。
      	
   - 缺点
   
   		限制了可用的吞吐量，而这对于小文件传输非常不利。<br>
   		对于突发、短暂的请求，常常会出现还没有达到最大窗口请求就被终止的情况。
- 拥塞预防
	- 出现的场景：<br>
	慢启动以保守的窗口初始化连接，随后每次往返都会成倍提高传输的数据量，
		- 直到`超过接收端的流量控制窗口`，即系统配置的拥塞窗口阈值(ssthresh)，拥塞窗口不再以2倍增长，而是每次加一
		- 一旦`有分组丢失`为止，此时拥塞预防算法介入，必须采取删包措施，调整窗口大小（ssthresh设置为出现拥塞时的发送窗口大小的一半。然后把拥塞窗口设置为1，重新执行慢开始算法）
	- 判断网络拥塞的标志：出现丢包（缓冲区放不下了，分组就会被删除)
	- 机制：<br>
	重置拥塞窗口，按自己的算法增大窗口，避免丢包。增大到某个时刻，又丢包，这个过程再从头开始。
	TCP连接的吞吐量曲线呈锯齿形
	
- 拥塞预防算法和慢启动的相互制衡
	- 操作对象都是拥塞窗口cwnd
		- 当cwnd小于ssthresh时，使用慢开始算法；当cwnd大于ssthresh时，改用拥塞避免算法。 
		- 拥塞避免算法让拥塞窗口缓慢增长，即每经过一个往返时间RTT就把发送方的拥塞窗口cwnd加1，而不是加倍。这样拥塞窗口按线性规律缓慢增长。
		- 一旦出现丢包，就把慢开始门限设置为出现拥塞时的发送窗口大小的一半。然后把拥塞窗口设置为1，执行慢开始算法
	
	
## 带宽延迟积
- 存在的含义：用于计算最优窗口大小（即充分利用带宽的情况下）
- why：发送端和接收端之间在途未确认的最大数据量，取决于cwnd和rwnd的最小值。
只要超过了未确认的最大数据量，都必须停下来等待另一方ACK确认某些分组才能继续，需要等待多长时间呢？取决于往返时间RTT.
- 结论：带宽延迟积 约等于 最优窗口大小

如果在高速连接的客户端与服务器之间，实际传输速度只有可用带宽的几分之一，那窗口大小很可能就是罪魁祸首。

- 1、rwnd太小：某一饱和端通告的接收窗口很小

- 2、cwnd大小：要网络拥堵和丢包导致拥塞窗口重置

- 3、流量增长过快导致对连接吞吐量施加了限制

## 队首阻塞
- 场景：每个TCP分组必须按顺序传送。如果某一分组没能到达接收端，那么后续分钟必须保存在接收端的TCP缓冲区。
- 结果：应用层对缓冲区中排队的分组一无所知，必须要等全部分组到达才能访问数据。只能在通过套接字读数据时感觉到延迟交付。

## TCP如何保证可靠传输的？
- 基本的分组错误检测与纠正
- 按序交付
- 丢包重发
- 保证网络最高效率的流量控制
- 拥塞控制和预防机制

## 针对TCP的优化建议
- 服务器配置调优
	- 增大TCP的初始拥塞窗口：
	
		确保 cwnd 大小为 10;
		让TCP在第一次往返就传输较多数据，随后的速度提升也很明显
		针对突发性短暂连接
	- 慢启动重启
	
		在连接空闲时，禁用慢启动（禁用空闲后的慢启动）
		改善瞬时发送数据的长TCP连接的性能
		
	- 窗口缩放
		
		启用窗口缩放，可以增大最大接收窗口大小
		让高延迟的连接达到更好吞吐量
	
	- TCP快速打开
		
		允许在第一个SYN分组中发送应用程序数据
		需要客户端和服务器共同支持
		 
- 应用程序行为调优
	- 能少发就少发: 压缩、减少传输冗余数据、减少下载不必要的资源
	- 缩短传输距离：CDN
	- 重用已建立的TCP连接：把慢启动和其他拥塞控制机制的影响降到最低

- TCP挥手小知识点：
	- 当TCP的一端发起主动关闭，在发出最后一个ACK包后，即第3次握 手完成后发送了第四次握手的ACK包后就进入了TIME_WAIT状态，必须在此状态上停留两倍的MSL时间，
	- 主要目的：
		- 怕最后一个 ACK包对方没收到，那么对方在超时后将重发第三次握手的FIN包，主动关闭端接到重发的FIN包后可以再发一个ACK应答包
		- 在TIME_WAIT状态 时两端的端口不能使用，要等到2MSL时间结束才可继续使用。当连接处于2MSL等待阶段时任何迟到的报文段都将被丢弃。

# 第三章
