Promise原理
https://zhuanlan.zhihu.com/p/83965949
# new Promise((resolve, reject)=>{})
fn入参是resolve、reject
立即执行传入的函数fn
fn函数里可以访问到api传入的resolve, reject（fn的入参是api传入的resolve和reject）
用户自行调用resolve和reject，对Promise的状态进行转移。
初始的Promise的状态是pending，可转移成rejected或fullfilled

# then((onFullFilled, onRejected)=>{})
fn入参是value，初始promise用户自己resolve的值 或者上一个 thenPromise返回的值（由api内部自己resolve）
给promise的回调函数队列推入一个回调函数fn
当且仅当promise的状态变化时，将执行回调函数队列里的回调函数（称为foo）
then返回一个新的Promise，有自己的状态转移，状态转移在回调函数foo中进行，也就是在回调函数中一定要调用这个Promise的resolve或者reject，否则then()无法进行链式调用，也即是then().then()里的第二个then无法被调用。
