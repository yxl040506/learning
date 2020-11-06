# FP、FCP、FMP
## FP（First Paint）
- 首次绘制，一般指白屏时间
- 含义：浏览器首次向屏幕传输像素的时间点，（页面在屏幕上首次发生视觉变化）
- 统计逻辑：performance.getEntriesByType('paint')数组第1个
- 举例：
	- 绘制非默认背景色
	
# FCP（First Content Paint）
- 首次内容绘制，一般指首屏时间
- 含义：首次绘制来自DOM的内容
- 统计逻辑：performance.getEntriesByType('paint')数组第2个
- 举例：
	- 文本、图片、SVG、canvas

FP和FCP可能是相同的时间，也可能是先FP后FCP。

## FMP（First Meaningful Paint）
- 目前尚无标准化的定义
- 将页面评分最高的可见内容出现在屏幕上的时间
- 对元素进行评分
	- 取元素与可视区域的交叉区域面积、可见度、 权重值之间的乘积为元素评分
- 取高分元素的渲染时间作为FMP
