title: html_note
date: 2015/3/31 9:59:54 
updated: 2015/3/31 10:00:05 
tags:
- html
- 前端
categories: 前端
---
#html(Hyper Text Mark-up Language) 

##meta
此标记用于定义文件信息，对网页文件进行说明，便于搜索引擎查找，放置于`<head></head>`之间 

1. 设置搜索引擎关键字：`<meta name="keywords" content="value">`多个关键字内容之间用","分隔
2. 设置描述`<meta name="description" content="value">` 
3. 设置作者`<meta name="author" content="value">` 
4. 设置字符集`<meta http-equiv="content-type" content="text/html;charset=gb2312">`
5. 设置页面定时跳转`<meta http-equiv="refresh" content="2;url=http://www.baidu.com"/>`经过2秒后跳转 
##body

		<body bacolor="背景颜色" backgroud="背景图片" text="文本颜色" link="链接文本颜色" alink="激活的链接文本" vlink="访问过文本颜色" leftmargin="左边距" rightmargin="右边距" topmargin="上边距" bottommargin=“下边距”>
默认情况下网页内容与浏览器边框之间是有距离的,可以通过上面边距控制 
##文本控制
- 加粗`<b>文本</b>` 
- 倾斜`<i>文本</i>` 
- 加强语气(加粗)`<strong>文本</strong>`
- 加强语气（倾斜）`<em>文本</em>` 
- 下划线`<u>文本</u>` 
- 删除线`<s>文本</s>` 
- 上标`<sup>文本</sup>` 
- 下标`<sub>文本</sub>` 
- 段落标记`<p align="left,center,right"></p>` 
- 水平直线`<hr width="80%" size="宽度 " noshade="是否显示凹槽">` 
- 定义一个块引用`<blockquote cite="引用地址">内容</blockquote>` 
##预定义
`<pre></pre>`用来预定义 通常用来表示源代码，它用来表示文本的格式 
##列表标记 
无序列表
	
		<ul type="项目符号类型disc,quare,"><li></li></ul>

有序列表
	
	<ol><li></li></ol>	

定义列表： 

	<dl> 
	<dt>标题1</dt> 
	<dd>内容1</dd> 
	<dd>内容1</dd> 
	<dd>内容1</dd> 
	<dt>标题2</dt> 
	<dd>内容2</dd> 
	<dd>内容2</dd> 
	<dd>内容2</dd> 
	
	</dl> 
##图像标签`<img/>` 
- src属性值url
- alt规定图片的替代文本（图片无法显示时） 
- title,鼠标悬停时显示的内容
- border图像边框
- align（left,right,top,middle,bottom）
- vspace（定义图像顶部和底部的空白）
- hspace(定义图像左侧和右侧的空白) 

##table
- table标签中，cellpadding表示表格单元格边框与其内部内容之间空间的大小默认为2（表格边距） 
- cellspacing设置表格单元格之间空间的大小，默认为2（单元格间距） 
- `<td rowspan="列数">`设置行合并的数目 
- `<td colspan="行数">`设置列合并的数目 

		<table> 
		<thead></thead>表格表头 
		<tbody></tbody>表内容 
		<tfoot></tfoot>表尾 
		</table> 
##超链接
`<a href="" title="注释" target="打开链接窗口的形式">`

- _blank在新窗口中打开 
- _self在自身窗口中打开（默认值） 
- _parent在上一级窗口中打开，框架会经常使用 
- _top在浏览器的整个窗口中打开，忽略任何框架 

###局部链接（锚点） 
跳转到同一网页或其他文档中的指定位置 

1. 创建锚点：`<a name="锚点名称">显示内容</a>` 
2. 链接锚点：`<a href="#锚点名称">`

###空链接的使用 
- 设为首页`<a href="#" onclick="this.style.behavior='url(#default#homepage)';this.sethomepage('http://www.sohu.com')">设为首页</a>`
- 添加收藏`<a href="#" onclick="javascript:window.external.addfavorite('http://www.sohu.com','搜狐')">加入收藏夹</a>` 
##图片按钮
`<input name="图片按钮名称" tpye="image" src="图片路径"/>`
##表单
`<fieldset>...</fieldset>`定义围绕表单中元素的边框

`<legend>..</legend>`为fieldset元素定义标题 

##插入多媒体元素标记 
flash动画的插入使用`<embed src width height wmode="transparent"(使flash背景透明)>...</embed>`（这种方式为以前的，可能存在兼容性）另一种是用dw生成的用object标签包含的模式 

##滚动字幕 
`<marquee>滚动的文字</marquee>` 

- direction="滚动方向"left,right,up,down; 
- behavior="滚动方式" scroll（一圈一圈绕着走）slide(只走一次)alternate(来回的走) 
loop="滚动的循环次数"若未指定则循环不止（loop="infinite"） 
- bgcolor=“背景颜色”
- width="宽度"
- height="高度" 
- onmouseover="this.stop()"
- onmouseout="this.start()" 
- scrollamount="速度" 数值越大速度越快 
- scrolldelay="延时"(走一步，停一停) 
（这个标签也是w3c不支持了，可能有兼容性） 
##iframe
iframe标签可以把另一个页面嵌入到当前页面。浮动窗口的效果

		<a href="http://ww.taobao.com.cn" target="iframe1">连接到taobao</a>
		<ifr66666ame name="iframe1" src="http://www.baidu.com">
点击链接后，替换成taobao

##多媒体标签
`<embed src=""/>`

##背景音乐
`<bgsound src="" start="mouseover"/>`

##插入视频剪辑
`<img src="url.gif" dynsrc="url.avi">`
用url.avi这一文件来播放视频，用url.gif这一图像作为视频的封面，即浏览器尚未完全读入avi文件时，先在avi播放区域显示该图像
何时开始播放`<img start="#"> #= fileopen,mouseover`


##图像映射

		<img src="mapimg.gif" usemap="#Face">
		<map name="Face">
		<!Text Botton>
		    <area shape="rect" href="page.html" coords="120,20,280,60">

##线包字
`<fieldset><legend>信息：</legend><form></form></fieldset>`

