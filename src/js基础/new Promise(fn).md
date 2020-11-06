Promise原理
https://zhuanlan.zhihu.com/p/83965949
# new的时候
new Promise(fn)
- 给用户传递内部的2个参数，一个this.onFullfilled, 一个this.onRejected

# this.onFullfilled & this.onRejected
- 进行状态转移
	- 接受用户传进来的result
- 调用then回调函数
	- setTimeout(this.flushCbQueue, 0)

# then函数
- 接受用户传进来
- 返回一个子promise
- promise里执行
	- 父Promise如果是pending：往cb数组里push一个cb
	- 父Promise如果是resolved或rejected：执行该cb（只执行这一个） 

# 执行then里的回调函数
- 何时执行？
	- 父Promise状态不为Pending时，就调用
- 目的是要推进子promise的状态变化
	- 也就是我们要调用promise的resolve或reject 
- 如何调用？
	- 父Promise为Resolved时，就调用子promise的resolve方法
		- 如果用户没有传then参数，则resolve的值是父Promise resolve的值：resolve(result)
		- 如果用户传了then的第一个参数onFullfiled，则resolve(onFullfiled(result))
	- 父Promise为Rejected时，就调用onRejected