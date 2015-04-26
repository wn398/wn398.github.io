title: HTML5
date: 2015/4/26 11:09:33 
updated: 2015/4/26 11:09:37 
tags:
- 技术
- html
- 前端
categories: 前端
---
##Html5 结构
`<header>` 在body元素里可以使用header标记一些标题，可以有多个,它的里面可以使用`<nav>`元素包含要导航的链接.

`<main>`元素指定主体，在一个页面只有一个

`<articale>` 在body元素里的，可以有多个，可以嵌套

`<section>` 代表文档或应用的一个区块

`<aside>`指定附注栏 侧栏（与主体内容相关性没有很强的关系）

`<footer>`类似于header，他们都是对div主义性的一个补充

同header,footer,main,article,section,aside,nav,h1~h6,p和其他很多元素一样，默认情况下，div从新的一行开始显示。

HTML5引入header,footer,main,article,section,aside和nav，它们可以成为具有独立含义的元素以取代div。

###meta
此标记用于定义文件信息，对网页文件进行说明，便于搜索引擎查找，放置于`<head></head>`之间 

1. 设置搜索引擎关键字：`<meta name="keywords" content="value">`多个关键字内容之间用","分隔
2. 设置描述`<meta name="description" content="value">` 
3. 设置作者`<meta name="author" content="value">` 
4. 设置字符集`<meta http-equiv="content-type" content="text/html;charset=gb2312">`
5. 设置页面定时跳转`<meta http-equiv="refresh" content="2;url=http://www.baidu.com"/>`经过2秒后跳转 

###body

		<body bgcolor="背景颜色" backgroud="背景图片" text="文本颜色" link="链接文本颜色" alink="激活的链接文本" vlink="访问过文本颜色" leftmargin="左边距" rightmargin="右边距" topmargin="上边距" bottommargin=“下边距”>

###使用ARIA改善可访问性
WAI-ARIA(web accessibility Initiative's accessible rich Internet applications)无障碍网页倡议-无障碍的富互联网应用。它会使用属性来填补一些语义上的空白

1. 地标角色
如何跳至页面的页脚，如何跳到开头
role = "banner"  添加到页面级的header元素，每个页面只用一次
role = "navigation"  与nav元素是对应关系，可以使用多次
role = "main"  与main元素是对应关系，每个页面只使用一次
role = "complementary"(补充性内容)  与aside元素是对应关系，可以使用多次
role = "contentinfo"(内容信息)  添加到页脚，通常是一些版权信息等，每个页面仅使用一次

可以使用title属性，为网站上任何部分加上提示标签，鼠标移上去后显示的内容

##html5文本
1. 添加段落

	html会忽略你在文本编辑器中输入的回车符和其他额外的空格，要在网页中开始一个新的段落，应用使用p元素。
2. 指定细则
	
	small表示细则一类的旁注。通常包括免责声明，注意事项，法律限制，版权信息等，还可以表示署名，或者满足许可要求。它通常是行内文本的一小块，而不是包含多个段落或其他元素的文本
3. 标记重要和强调的文本
	
	strong元素表示内容的重要性，而em则表示内容的着重点。
4. 创建图

	输入`<figuer>`，作为可选步骤输入<figcaption>开始图的标题，添加图像，视频，数据表格等内容的代码创建图，现代浏览器在默认情况下会为figure添加40px宽的左右外边距
5. 指明引用或参考
	
	使用cite元素可以指明对某内容源的引用或参考，cite只用于参考源本身，而不是从中引述的内容
6. 引述文本
	
	有两个特殊的元素用以标记引述的文本。blockquote元素表示单独存在的引述。而q元素则用于短的引述，如句子里的引述。
7. 指定时间
	
	time元素标记时间，日期或时间段
8. 解释缩写词
	
	使用abbr元素标记缩写词并解释其含义，使用可选的title属性提供缩写词的全称。`<abbr title="">`缩写词`</abbr>`
9. 定义术语
	
	dfn元素来定义术语，通常dfn元素以斜体显示，与cite一样
10. 创建上标和下标
	
	`<sub>`下标  `<sup>`上标
11. 添加作者联系信息

	address定义有关的作者，相关人士或组织的联系信息
12. 标注编辑和不再准确的文本

	代表添加内容的ins元素和标记已删除的元素del元素（浏览器通常对已删除的文本加上删除线，对插入的文本加上划线）,del和ins都支持两个属性，cite和datetime,cite属性（区别于cite元素）用于提供一个URL，指向说明编辑原因的页面。 datetime提供编辑的时间
13. 标记代码  

	如果你的内容包含代码示例或文件名，可以使用code元素
14. 使用预格式化的文本
	
	通常，浏览器会将所有额外的回车和空格压缩，并根据窗口的大小自动换行，预格式化的文本可以保持文本固有的换行和空格。`<pre>`输入或复制希望以原样显示的文本，包括所需要的空格，回车和换行。除非是代码，不要用任何HTML标记这些文本`</pre>`  pre默认从新的一行开始显示
15. 突出显示文本

	mark标记，会给文本加上黄色背景，不要仅仅因为要给文字加上背景色或其他视觉上的考虑而使用mark.如果只是要给一块文字添加样式，又没有合适的语义化元素，可以使用span元素，并用css为其添加样式
16. 创建换行

	手动换行`<br>`, 要确保使用br是最后的选择，因为该元素将表现样式带入了HTML,而不是让所有的呈现样式都交由css控制。对于诗歌，街道地址等应该紧挨着出现的短行，都适合用br元素。
17. 创建span
	
	同div一样，span元素是没有任何语义的。不同的是，span只适合包围字词或短语内容，而div适合包含块级内容。
18. 其它元素（极少情况下用的元素，或者浏览器对它们支持还不完善）
	- u元素  同b,i,s和small一样，HTML5重新定义了u元素，使之不再是无语义用于表现的元素。以前，u元素用来为文本添加下划线，现在u元素用于非文本注解。HTML5对它的定义：u元素为一块文字添加明显的非文本注解，比如在中文中将文本标记为专有名词
	- wbr元素  HTML5为br元素引进了一个相近的元素，它代表”一个可换行处“，wbr不会强制换行，而是让浏览器知道哪里可以根据需要进行换行。
	- ruby,rp和rt元素   旁注标记（ruby annotation）是东亚语言中一种惯用符号，通常用于表示生僻字的发音。这些小的注解字符出现在它们标的字符的上方或右方。ruby元素以及它们的子元素rt和rp是html5中为内容添加旁注的机制。rt指明对基准字符进行注解的旁注字符。可选的rp元素用于在不支持ruby的浏览器中的旁注文本周围显示括号。
	```{bash}
		<ruby>
		北 <rp>(</rp><rt>bai</rt><rp>)</rp>
		京 <rp>(</rp><rt>jing</rt><rp>)</rp>
		</ruby>
	```
	- meter元素  它表示任务的完成度或已知范围的测量结果。html5建议浏览器在呈现meter时，在旁边显示一个类似温度计的图形.	`<meter value="0.6">完成度</meter>`
	- progress 元素  是html5的新元素，指示某项任务的完成进度.`<progress max="100" value="37">37%  saved</progress>`

###文本控制
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
- `<pre></pre>`用来预定义 通常用来表示源代码，它用来表示文本的格式 

###滚动字幕 
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

##图像
PNG和GIF都支持透明，JPEG则不支持。在GIF格式中，一个像素要么是透明的，要么是不透明的。这叫**索引色透明**。 png却支持索引色透明，也支持alpha透明。

- 使用alt属性
	
	可以为图像添加一段描述性文本，当图像出于某种原因不显示的时候，就将这段文字显示出来。

- 为网站添加图标

	我们在浏览器选项卡，历史记录，书签页，收藏夹和地址栏中看到的（与网站相关）的小图标称为favicon,这个词是 favorites icon(收藏夹图标)的简称。将图标图像放在网站的根目录里。浏览器会自动在根目录寻找这些特定的文件名，找到后就将图标显示出来。也可以将图标文件放在根目录外以外的其他文件夹，这样的话，就需要在html中添加link元素，从而让浏览器得以找到这些图标。

###图像标签`<img/>` 
- src属性值url
- alt规定图片的替代文本（图片无法显示时） 
- title,鼠标悬停时显示的内容
- border图像边框
- align（left,right,top,middle,bottom）
- vspace（定义图像顶部和底部的空白）
- hspace(定义图像左侧和右侧的空白) 

##链接
- 使用rel属性

	rel属性是可选的，但对于指向另一网站的链接，推荐包含这个值，它描述包含链接的页面和链接指向的页面之间的有关系。如`rel="external"`,

- html块级链接：

	html5几乎允许在链接内包含任何类型的元素或元素组

- 使用锚

	激活一个链接会将用户带到对应网页的顶端，如果要想用户跳至网页的特定区域，可以创建一个锚，并在链接中引用该锚。  `<a href="#anchor-name">`  其中anchor-name是目标的id属性值，如果锚位于另一个文档，就使用`<a href="page.html#anchor-name">`引用该区域。如果锚位于另一台服务器上的页面，则需要输入`<a href="http://www.site.com/directory/page.html#anchor-name">`.可以创建指向任何URL的链接--RSS源，图像，希望访问者下载的文件，电子邮件地址，电话号码等,电话用tel:电话号码

###超链接
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

###iframe
iframe标签可以把另一个页面嵌入到当前页面。浮动窗口的效果

		<a href="http://ww.taobao.com.cn" target="iframe1">连接到taobao</a>
		<ifr66666ame name="iframe1" src="http://www.baidu.com">
点击链接后，替换成taobao
##表单
HTML5 通过引入新的表单元素、输入类型和属性，以及内置的对必填字段、电子邮件地址、URL 以及定制模式的验证，让这一切变得很轻松。这些特性不光帮助了设计人员和开发人员，也让网站访问者的体验有了很大的提升。

对表单元素进行组织的步骤

1. 在form 开始标签的下面、任何希望包含在第一个组的表单元素的上面，输入`<fieldset>`。 
2. 如果需要，输入`<legend>`。（如果需要包含legend，它必须是fieldset 里的第一个元素。）
3. 输入标签的文本。
4. 输入`</legend>` 以完成标签。
5. 如果不使用legend，则创建一个标题从而可以识别属于该fieldset 的一组控件。 
6. 创建属于第一组的表单元素
7. 输入`</fieldset>` 结束第一组表单元素。
8. 为每一组表单元素重复第(1) 步至第(7) 步。

legend 元素可以提高表单的可访问性。对于每个表单字段，屏幕阅读器都会将与之关联的legend 文本念出来，从而让访问者了解字段的上下文。这种行为在不同的屏幕阅读器和浏览器上并不完全一样，不同的模式下也不一样

###创建文本框
1. 如果需要，输入用于让访问者识别文 本框的标签，例如`<label for="idlabel ">LastName:</label>`，其中idlabel 匹配第(4)步中的标签，
2. 输入`<input type="text"`
3. 输入name="dataname"，这里的dataname 是用于让服务器（和脚本）识别输入数据的文本。 
4. 如果在第(1) 步中创建了label，输入id="idlabel "，这里的idlabel 跟第(1) 步中 for 属性的文本一样。这跟具有显式文本框的label 元素关联。很多程序员将id 和name 设为同一个值，尽管这并不是必需的。 
5. 如果需要，输入value="default "，这里的default 是这个字段中最初显示的数据，如果访问者没有输入别的内容的话，这一数据将被发送到服务器。
6. 如果需要，输入placeholder="hinttext"，这里的hinttext 是这个字段中最初显示的数据，用于指导用户的输入。 当input 元素获得焦点时，这些文本将会消失，让用户输入内容。 
7. 如果需要，输入required="required"，表示仅在这个字段有值的情况下才能提交表单
8. 如果需要，输入autofocus 或autofocus="autofocus"（在HTML5 中这两种方法均可）。如果这是第一个拥有此属性的表单控件，input 元素在页面加载时会默认获得焦点。
9. 如果需要，通过输入size="n " 定义文本框的大小，这里的n 是需要设置的文本框宽度，以字符为单位。也可以使用CSS 设置输入框的宽度
10. 如果需要，输入maxlength="n "，这里的n 是该文本框允许输入的最大字符数。 
11. 最后，输入> 或/>，结束文本框。

###线包字
`<fieldset><legend>信息：</legend><form></form></fieldset>`

###使用autocomplete属性
如输入Ma 后，Firefox 根据先前的表单输入给出了一些包含同样字母的建议文本。我们可以从提示文本列表中进行选择，也可以继续输入。如果整个表单或者这个特定的input 设置了autocomplete="off"，就不会显示这些提示，浏览器也不会保存用户所输入的内容

###为表单组件添加正式标签
1. 输入`<label`。
2. 如果需要，输入`for="idlabel ">`，这里的idlabel 是对应表单元素的id 属性值。 
3. 输入标签的内容。
4. 输入`</label>`。
5. 如果创建的是表单元素，确保包括id 属性，对应于第(2) 步中指定的for 属性。

###创建电子邮件框、搜索框、电话框和URL框 
1. 输入用于让访问者识别输入框的标签 
2. 对于电子邮件框，输入`<input type="email"`； 对于搜索框， 输入`<input type="search"`；对于电话框，输入`<input type="tel"`； 
对于URL 框，输入`<input type="url"`。
3. 输入id="idlabel "， 这里的idlabel跟第(1) 步中label 的for 属性相同。 
4. 输入name="dataname"，这里的dataname 是用于让服务器识别输入数据的文本。 
5. 如果需要，输入value="default "，这里的default 是最初显示在字段里的数据，也是访问者什么都不输入时将要发送给服务器的数据。
6. 如果需要，输入placeholder="hinttext"，这里的hinttext 是最初显示在字段里作为对 用户输入内容的提示的数据 
7. 如果需要，输入required 或者required= "required"。 
8. 如果需要，输入autofocus或者autofocus= "autofocus"。 
9. 如果需要，通过输入size="n " 定义框的大小，这里的n 是需要设置的框的宽度，以字符为单位。也可以使用CSS 设置输入框的宽度。
10. 如果需要，输入maxlength="n "，这里的n 是该框允许输入的最大字符数。 
11. 最后，输入> 或者/>，结束输入框（两种方法在HTML5 中均可）。

单选框：radiobox 
复选框：checkbox 
文本域：textarea 
选择框：select/option(optiongroup)

##列表
列表在默认情况下是从左边缩进的，如果将内容方向指定为从右向左（例 如在语言为希伯来语的情况下），列表就会通过右侧外边距进行缩进。要指定内容方向，可以在页面的html 元素中设置dir 属性，如 `<html dir="rtl" lang="he">`还可以在body里面的元素上设置dir 和lang 以覆盖html 元素上的设置。dir 属性的默认值为ltr。

创建列表时，无论是有序列表还是无序列表，都可以选择出现在列表项目左侧的标记的类型
###选择标记
在样式表中， 输入`list-style-type: marker` ，这里的marker 是以下属性值中的一种。

- disc（圆点，●）
- circle（圆圈，○）
- square（方块，■）
- decimal（数字，1、2、3……）
- upper-alpha（ 大 写 字 母，A、B、C ……） 
- lower-alpha（ 小 写 字 母，a、b、c ……） 
- upper-roman（大写罗马数字，Ⅰ、Ⅱ、Ⅲ、Ⅳ ……）
- lower-roman（小写罗马数字，i、ii、iii、iv……）

###显示无标记列表
在样式表规则中，输入`list-style-type: none`。

###使用定制的标记
如果对使用圆圈、方块、圆点以及罗马数字这些标记感到厌倦，也可以使用图像创建自己定制的标记
使用定制的标记

1. 在目标列表或列表项的样式规则中，输入list-style: none; 以取消常规的标记。 
2. 在目标列表的样式规则中， 设置margin-left 和/ 或padding-left 属性，指定列表项目缩进的大小。为了在不同的浏览器上实现相似的效果，通常需要同时设置这两个属性。注意，如果为内容设置了dir="rtl"，那么就应该设置margin-right 和padding-right 属性。
3. 在目标列表的li 元素的样式规则中，输入background: url(image.ext ) repeattype horizontal vertical;，其中image.ext 是要作为定制标记的图像的路径和文件名，repeat-type 是no-repeat、repeat-x 和repeat-y 中的一种（通常设为no-repeat），
horizontal 和vertical 值表示列表项目中背景图像的位置
4. 输入padding-left: value ;，这里的value 应不小于背景图像的宽度，以防列表项 目的内容覆盖到定制标记的上面。

		ul {
		/* 取消默认标记 */
		list-style: none;
		/* 删除列表项的缩进 */
		margin-left: 0;
		padding-left: 0;
		}
		li {
		/* 显示定制的标记 */
		background: url(../img/checkmark.png)
		➝ no-repeat 0 0;
		}

###对列表进行重新编号
可以使用value:`<li value="5">`

###控制标记的位置
1. 在目标列表或列表项目的样式表规则 中，输入list-style-position:。 
2. 输入inside 让标记缩在文本块内，或者输入outside 让标记显示在列表项目文本的左边（这是默认的设置）。list-style-position 可应用于ul、ol 或li。结果是一样的，除非针对特殊的li（如某一类的li）设置不同的样式，从而在一个列表中同时拥有内部标记和外部标记。

###为嵌套列表设置样式的步骤
1. 要设置最外层列表的样式， 输入toplevel li {style _rules }，其中，toplevel是最外层列表的类型（如ol、ul），style_rules 是要应用的样式。 
2. 对于第二级列表， 输入toplevel 2ndlevel {style_rules }， 其中，toplevel 对应于第(1) 步中的toplevel ，2ndlevel 则是第二级列表的类型，而style_rules 是它需要应用的样式。
3. 对于第三级列表， 输入toplevel 2ndlevel 3rdlevel {style_rules }， 其中，toplevel 和2ndlevel 对应于第(1) 步和第(2)步中的toplevel 和2ndlevel ，3rdlevel 则是第三级列表的类型。而style_rules 则是它需要应用的样式。
4. 对于要设置样式的每个嵌套列表，继续以上述方法进行设置。

###创建描述列表
根据HTML5 规范，“由名称及其值构成的组合可以是术语和定义、元数据主题和值、问题和答案， 以及任何其他的名– 值组。”每个列表都包含在dl 中，其中的每个 名– 值组都有一个或多个dt 元素（名称或术语）以及一个或多个dd 元素（它们的值）。
创建描述列表的步骤

1. 输入`<dl>`。
2. 输入`<dt>`。
3. 输入需要描述或定义的单词或短语，包括任何额外的语义元素（如dfn）。 
4. 输入`</dt>` 以完成名–值组中的名称部分。
5. 如果名– 值组中有一个以上的名称或 术语，可根据需要重复第(2) 步至第(4) 步
6. 输入`<dd>`。
7. 输入第(3) 步中输入的术语或名称的描述。
8. 输入`</dd>` 以完成名– 值组中的描述 （值）部分。 
9. 如果名– 值组中有一个以上的定义值，可根据需要重复第(6) 步至第(8) 步。 
10. 对每个由术语和描述构成的组，重复第(2) 步至第(9) 步。 
11. 输入`</dl>` 以完成整个描述列表。

##视频，音频和其他多媒体
###在网页中添加单个视频的步骤
1. 获取视频资源。
2. 输入`<video src="my-video.ext"></video>`，这里的my-video.ext 是视频文件的位置、名称和扩展名。就这么简单！ 
###video 属性
除了src，还有哪些属性可以用在video 元素上呢？

- src（源） 指定视频文件的URL
- autoplay（自动播放） 当视频可以播放时立即开始播放
- controls（控件） 添加浏览器为视频设置的默认控件
- muted（静音） 让视频静音
- loop（循环） 让视频循环播放
- poster（海报） 指定视频加载时要显示的图像（而不显示视频的第一帧）。接受所需图像文件的URL
- width（宽度） 视频的宽度（以像素为单位），通常默认为300
- height（高度） 视频的高度（以像素为单位），通常默认为150
- preload（预加载） 告诉浏览器要加载的视频内容的多少。可以是以下三个值：
    - none 表示不加载任何视频
	- metadata 表示仅加载视频的元数据（如长度、尺寸等）
	- auto 表示让浏览器决定怎样做（这是默认的设置）
###为视频添加自动播放和循环播放
输入`<video src="my-video.ext " autoplay  loop></video>`，这里的my-video.ext 指向你的视频文件。
###为视频指定海报图像
输入`<video src="my-video.ext " controls  poster="my-poster.jpg "></video>`，其中，myvideo.ext 指向你的视频文件，my-poster.jpg是想用做海报图像的图像。

###告诉浏览器不预先加载视频的步骤
输入`<video src="my-video.ext " preload="none" controls></video>`， 这里的 my-video.ext 指向你的视频文件。preload 的默认值是auto。在none 和auto 之间有一个不错的中间值， 即preload="metadata"。这样做会让浏览器仅获取视频的基本信息，如尺寸、时长甚至一些关键的帧。在开始播放之前，浏览器不会显示白色的矩形，而且视频的尺寸也会与实际尺寸一致。

###如何获得所有浏览器支持
要获得所有兼容HTML5 的浏览器的支持，至少需要提供两种格式的视频：MP4 和WebM。如何做到呢？这时就要用到HTML5 的source 元素了。通常，source 元素用于定义一个以上的媒体元素

	<video width="369" height="208" controls>
	<source src="paddle-steamer.mp4" type="video/mp4">
	<source src="paddle-steamer.webm" type="video/webm">
	<p><a href="paddle-steamer.mp4"> Download the video</a></p>
	</video>

###在网页中添加带控件的单个音频文件的步骤
输入`<audio src="my-audio.ext" controls></audio>`， 其中的my-audio.ext 是音频文件的位置、名称和扩展名。

- src（源） 指定音频文件的URL
- autoplay（自动播放） 当音频可以播放时立即开始播放
- controls（控件） 添加浏览器为音频设置的默认控件
- muted（静音） 让音频静音
- loop（循环） 让音频循环播放
- preload（预加载） 告诉浏览器要加载的音频内容的多少。可以是以下三个值：
	-  none 表示不加载任何音频；
	-  metadata 表示仅加载音频的元数据（如长度）；
	-  auto表示让浏览器决定怎样做（这是默认的设置）

###高级多媒体
使用HTML5 原生多媒体的另一个好处是可以利用很多来自HTML5 或与HTML5 相关的新特性和新功能。本节主要讨论其中的两个：canvas 元素和SVG。

1. 通过canvas 操作视频
使用canvas 元素及相应的JavaScript API 可以在网页上描制并创建动画.可以对HTML5 视频应用这些API，因为video 元素可以同其他HTML 元素一样进行处理，因此它也可以被canvas 访问和获取。
通过JavaScript API，可以从播放的视频中抓取图像，并在canvas 元素中重新绘制该图像，从而创建视频的截图。通过API可以对单个图像像素进行操作，同时由于可以根据视频在canvas 中创建图像，因而可以调整视频的像素。例如，可以将视频转化为灰度模式。这只是让你对通过canvas 操作video 元素建立一些简单的概念，对这一主题的深入

2. 联合使用SVG 和视频
探讨已经超出了本书的范围。

##表格
创建表格结构的步骤
1. 输入`<table>`。
2. 如果需要， 输入`<caption>caption content </caption>`，其中的caption content
是对表格的描述。
3. 如果需要，在要创建的表格部分的第一个tr 元素之前，输入`<thead>`、`<tbody>`或`<tfoot>`（需要说明的是，`<tbody>` 不能在`<thead>` 之前）。
4. 输入`<tr>` 定义行的开始。
5. 输入`<th scope="scopetype ">` 开始标题单元格（其中的scopetype 可以是col、
row、colgroup 或rowgroup），或者输入<td>
定义数据单元格的开始。
6. 输入单元格的内容。
7. 输入`</th>` 结束标题单元格，或者输入`</td>` 结束数据单元格。
8. 对行内的每个单元格重复第(5) 步至第(7) 步。
9. 输入`</tr>` 结束行。
10. 为所在表格部分内的每个行重复第(4) 步至第(9) 步。
11. 如果在第(3) 步中开始了一个表格部分，就根据需要使用</thead>、</tbody> 或
</tfoot> 结束这个部分。
12. 为每个表格部分重复第(3) 步至第(11) 步。注意，一个表格只能有一个thead和tfoot，但可以有多个tbody 元素。
13. 输入</table> 以结束表格。在默认情况下，表格被压得很扁。通过应用一些CSS，可以为单元格添加一些空间让它们扩大一些（使用padding），添加边框以指示单元格的边界（使用border），还可以对文本进行格式化。这些样式都有助于更好地理解表格

1. 让单元格跨越两个或两个以上列的步骤 `colspan="n ">`
2. 让单元格跨越两个或两个以上行的步骤 `rowspan="n ">`

#HTML5元素参考
![](/images/html/html参考1.png)
![](/images/html/html参考2.png)
![](/images/html/html参考3.png)
![](/images/html/html参考4.png)
![](/images/html/html参考5.png)
![](/images/html/html参考6.png)
![](/images/html/html参考7.png)
![](/images/html/html参考8.png)
![](/images/html/html参考9.png)
![](/images/html/html参考10.png)
