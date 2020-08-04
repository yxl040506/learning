# react事件

## 事件注册
### 1、事件委托
- addEventListener给 document注册了一个冒泡事件
- 优点：减少页面的注册事件数量，减少内存开销，优化浏览器性能

### 2、listenerMap
一开始是个空对象
- 根据事件类型获取对应的listener，是针对全局的、顶层的listener，

```
case DiscreteEvent:
      listener = dispatchDiscreteEvent.bind(null, topLevelType, PLUGIN_EVENT_SYSTEM, container);
      break;

    case UserBlockingEvent:
      listener = dispatchUserBlockingUpdate.bind(null, topLevelType, PLUGIN_EVENT_SYSTEM, container);
      break;

    case ContinuousEvent:
    default:
      listener = dispatchEvent.bind(null, topLevelType, PLUGIN_EVENT_SYSTEM, container);
      break;
```
- 增加顶层事件监听
```
// element一般就是document
element.addEventListener(eventType, listener, false);
listenerMap.set(topLevelType, null) // topLevelType: 'click' {click: null}
```

## 事件执行
### 开始dispatchEvent
handleTopLevel

根据原生event找到触发事件的target
### 1、从对象池取出事件对象
- 找到对应的事件plugin，比如最普通的点击事件、键盘、焦点、鼠标等，都对应了不同的合成事件构造类，每种事件都有一个对象池。
- 使用plugin获取合成事件对象，使用当前的事件属性进行初始化赋值

### 2、事件冒泡的实现
- 往事件捕获和事件冒泡2个node遍历方向收集每层节点的回调函数（存到合成事件对象_dispatchListeners和_dispatchInstances）
- 遍历dispatchListeners，遇到stopPropagation()则停止遍历

### 3、队列执行
## 事件清理
- 未使用event.persistent()的，调用析构函数将合成事件对象的属性均置为空，然后push回对象池