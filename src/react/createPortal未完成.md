# createPortal




```
	// html
  	<div id="app">
        <div id="app-root"></div>
        <div id="modal-root"></div>
    </div>
```
```
// jsx
import React from 'react'
import ReactDOM from 'react-dom'
const appRoot = document.getElementById('app-root');
const modalRoot = document.getElementById('modal-root');

// Let's create a Modal component that is an abstraction around
// the portal API.
class App extends React.Component {
    render () {
        const fromPortal = ReactDOM.createPortal(
            this.props.children,
            modalRoot
        )
        return <div className='root' onClick={()=>{console.log('className=root click')}}>
            {fromPortal}
        </div>
    }
}

// 重点观察这个span标签的渲染位置
// createPortal方式，span上点击的触发，会冒泡到class='root';render方式则不会冒泡到class='root'
ReactDOM.render(
<App>
    <span onClick={()=>{console.log('span click')}}>click son</span>
</App>, appRoot);

```


渲染出来的结果：
```
<div id="app">
     <div id="app-root">
     		<div class="root"></div>
     </div>
     <div id="modal-root">
     		<span>click son</span>
     </div>
</div>
```