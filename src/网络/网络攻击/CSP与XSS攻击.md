# CSP与XSS攻击
## 基本概念
- CSP：内容安全策略
- 机制：以白名单的形式，配置可信任的内容来源
	- 只要白名单中的内容能正常执行（js、css、img等），非白名单内容无法正常执行
- 目的：
	- 减少跨站脚本攻击(XSS)
	- 减少运营商劫持的内容注入攻击
- 不支持CSP的浏览器将会自动忽略CSP信息，不会有什么影响

## CSP语法
```
<meta http-equiv="Content-Security-Policy" content="script-src 'self'">
```

- 策略类型：有2种
	- 阻止不安全资源：Content-Security-Policy
	- 仅上报，不阻止不安全资源：Content-Security-Policy-Report-Only
	- 当定义多个策略的时候，浏览器会优先采用最先定义的。
- 指令合集
	- default-src：针对所有类型的资源
	- script-src
	- img-src
	- style-src
	- media-src 
	- form-action
- 指令的值
	- '*' ：任何值
	- 'none'：不允许任何内容
	- 'self'：只允许同源内容
	- 'www.jd.com'：允许特定域名
	- 'https'：允许加载https资源
 
## 用法
- META标签
	- 页面多的话，需要每个页面都加上
```
<meta http-equiv="Content-Security-Policy" content="script-src 'self'">
```
- HTTP Header：返回html文件的服务器加上头部
	- 可以同时支持多个页面

## 实践
- 先用Meta标签的report-only：制定方案
- 再用HTTP Header Content-Security-Policy-Report-Only： report-only调试
- 再用HTTP Header Content-Security-Policy进行真正拦截
