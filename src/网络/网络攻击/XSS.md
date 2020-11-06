|类型|存储区*|插入点*| <br>
|存储型 XSS|后端数据库|HTML| <br>
|反射型 XSS|URL|HTML| <br>
|DOM 型 XSS|后端数据库/前端存储/URL|前端 JavaScript|<br>


# XSS
## 存储型
- 恶意代码要存储到目标网站的数据库
- 服务端渲染的时候，将恶意代码拼接在HTML中返回给浏览器

## 反射型XSS
- 攻击者构造了一个URL，引导用户点击
- 用户访问这个URL的时候，服务端又把代码从url取出，拼接在HTML中返回

## DOM型XSS
- 攻击者构造url，引导用户点击
- 前端javascript读取url中的代码，并执行

## 2个关键
- 攻击者提交
- 浏览器执行

## 解决办法
### 一、HTML提供的方法
- CSP：
	- 禁止加载外域代码：script-src 'self'
	- 禁止外域提交： form-action 'none'
- HTTP-only
	- 禁止javascript读取某些敏感Cookie
- 验证码

### 二、遵守代码安全性规范
- 输入侧过滤？
	- 输入侧过滤能够在某些情况下解决特定的 XSS 问题，但会引入很大的不确定性和乱码问题。在防范 XSS 攻击时应避免此类方法。
- 预防存储型和反射型
	- 改成纯前端渲染，代码和数据分隔开
	- 对HTML做充分转义，使用转义库 
		- 对代码插入点做转义
		
		```
			<input value="<%= Encode.forHtml(UNTRUSTED) %>" />
		``` 
- 预防DOM型XSS
	- 常见于纯前端渲染
	- 策略：
		- 小心使用innerHTML、outerHTML、eval()、document.write()，使用textContent()、setAttribute()代替
		- 避免内联事件
		
		```
		onLoad="onload('{{data}}')"、onClick="go('{{action}}')"
		```
		- react技术栈：不使用dangerouslySetInnerHTML
	