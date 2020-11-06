# setTimeout实现原理和使用注意
## 消息队列 
- 所有运行在主线程上的任务，都需要添加到消息队列
- 事件循环系统按顺序执行消息队列中的任务
- setTimeout会建立一个任务，并添加到定时器延时队列

```
void MainTherad(){
  for(;;){
    //执行消息队列中的任务
    Task task = task_queue.takeTask();
    ProcessTask(task);

    //执行延迟队列中的任务
    ProcessDelayTask()

    if(!keep_running) //如果设置了退出标志，那么直接退出线程循环
        break; 
  }
}
```

## 注意事项
- 当前任务执行过久，会延迟到期定时器任务执行
- setTimeout存在嵌套调用，超过5次之后，会设置最短执行间隔为4秒
- 未激活的页面，setTimeout执行的最小间隔是1000ms
- 延迟执行时间有最大值：Chrome、Safari、Firefox 都是以 32 个 bit 来存储延时值的，32bit 最大只能存放的数字是 2147483647 毫秒

## setTimeout校准