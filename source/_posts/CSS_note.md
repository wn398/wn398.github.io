title: CSS_note
date: 2015/3/31 10:44:36 
updated: 2015/3/31 10:44:42 
tags:
- css
- 前端
categories: 前端
---
#CSS基本概念
##CSS的特性： 
1. 继承--网页中子元素，将继承父元素的样式（因为有些子元素本身就有默认值，所以它就覆盖了父元素中的属性） 
2. 层叠--网页中子元素定义了与父元素相同的样式，则子元素的样式将覆盖掉父元素的样式。后面定义的样式，会覆盖前面定义的样式（子元素如果定义了与父元素相同的样式，会覆盖掉父元素的样式） 
##CSS样式的优先权： 
4种方式的优先权：内联式【行内样式】---》内嵌式【内部式】---》链接式【外部式】---》@import导入式

**行内样式**：应用于一个标签`<p style="css"></p>` 

**内嵌式**：应用于一类标签(在head中加`<style>p{css}</style>`) 

**外部链接**：`<link href="url" rel='stylesheet' type="text/css"/>`推荐使用 

**导入式**：`<style type="text/css">@import url('basic.css');</style>`导入式与内嵌式是相类似的，都是将样式加入到网页里（缺点1，导入式会占用html空间。2.有些浏览器，解析会有问题。浏览器会，最后读取@import中内容） 
@import url('basic.css');也可以应用到css文件中 

Css优先权：就近原则 

1. 作用范围越小，优先权越高 
2. 离要修饰目标越近的样式优先权越高。 
3. 选择符优先权：行内>id>class>标签选择符 
4. 特殊情况，我们可以提升某个属性的权限，我们可以采用`！important`语法来提升重要性（加在属性后面，如`color:green !important;` ie6不支持 

##伪类
伪类语法：元素名称{属性：值}  如超链接的鼠标经过状态 `a:hover{text-decoratin:underline}` 

- 未访问的链接：`a:link`	
- 已访问的链接 `a:visited`	 
- 鼠标移动到链接上`a:hover`
- 鼠标按下链接 `a:active`(如果鼠标上的效果不起作用，请修改链接状态顺序l-v-h-a):

focus也是伪类 

##css选择符类型： 
1. 标签类选择符（html标签）。
2. 类选择符(某一类属性)。某一个元素可以加多个类,如`<p class="a1 a2"/>`类名与类名之间以空格隔开 
3. id类选择符（某一个id） 类名与id名不能以数字开关 
4. 通配选择符：`*`表示所有（作用范围很广，但效率很低）,前端开发的极限就是代码精简，加快了页面显示速度 
5. 如果我们针对某个元素中的子元素进行控制，可以使用嵌套的方式，不必再为子元素设置id（`父元素 子元素｛属性：值｝`） 
6. 选择符分组（集体控制）：将同样的样式定义用于多个选择符，选择符之间用逗号分隔。如`p,div,warning` 
7. 标签指定式选择符：如果既想使用`id`或`class`，也想同时使用标签选择符,`h1#content{}`表示针对所有id为content的h1标签, `h1.p1{}`表示针对所有class为p1的h1标签 
8. 组合选择符：将以上选择符进行组合使用 `h1.p1,#content h1{}`

##CSS中元素的分类： 
我们在布局页面的时候，会将html标签分为两种，`块状元素`和`内联元素`（我们平时用到的标签div和p就是块状元素，链接标签a就是内联元素），可以通过css修改元素的类型
 
1. 块状元素：｛display:block｝ 

一般是其他元素的容器，可容纳内联元素和其他块状元素，块状元素排斥其他元素与其位于同一行，宽度和高度起作用，常见的块状元素为div，body,h1-h6等,块状元素很霸道：能设宽和高！，独占一行！ 
2. 内联元素｛display:inline｝ 

内联元素只能容纳文本或者其他内联元素，它允许其他内联元素与其位于同一行，但宽度和高度不起作用，常见内联元素为`a,em,span`等,内联元素很温柔：不能设宽和高！不影响换行！（就像橡皮筋包住字）

##CSS盒子模型 
1. 盒子中的内容：content
2. 盒子的边框border 
3. 盒子边框与内容之间的距离，称为填充-padding，内边距（内补丁） 
4. 多个盒子存在，盒子与盒子之间的距离，称为边界---margin，外边距（外补丁） 
边界margin,边框border，填充padding,内容content

###css盒子的相关属性 
1. 内容属性：内容本身的宽=width,内容本身的高=height 
2. 内容填充属性：内容与边界之间的距离 `padding`:（如果为4个参数则顺序为上，右，下，左）`padding-top,padding-right,padding-bottom,padding-left` 

在定义盒子的宽度时，要考虑到，内填充，边框，边界的存在,在使用外边距的时候要注意浏览器的兼容性，除了值为0的情况下，其它所有值都要加单位
 
由于各个浏览器存在着内外边距的默认值，还不同，所以我们需要将所有浏览器的默认内外边距都从零开始计算，在实际工作中，不要用`*｛margin:0;padding:0｝`效率太低，所以用到了哪些元素就设置那些元素 

css边框属性： 
让盒子水平居中，只要让盒子的左右外边距设为auto 
##页面模块的常用css命名
头：header	内容：content	尾：footer	

导航：nav	侧栏：sidebar	栏目：column	

页面外围控制整体布局宽度：wrapper	左右中：left right center	

登录条：loginbar 标志：logo	热点：hot

新闻：news	下载：download 广告：banner

页面主体：main	子导航：subnav	菜单：menu 

子菜单：submenu	版权：copyright	友情链接：friendlinks 


#CSS控制属性

##css控制文本 
- 设置字体样式：`font-style:normal正常italic斜体` 
- 修饰文字：`text-decoration:none正常underline下划线overline上划线line-through删除线` 
- 字符间距：`letter-spacing:normal默认length长度单位` 
- 单词间距：`word-spacing:normal默认length长度单位` 
- 设置文本缩进：`text-indent:2em可以为负值` 
- 文本水平对齐方式：`text-align:left,center,right` 
- 对象中空白处理：`white-space：normal[自动换行]pre[换行和空白受保护]nowrap[强制在同一行显示]` 
- 文本大小写控制：`text-transform:none[正常大小]capitalize[每个单词的第一个字母转换成大写]uppercase[转换成大写]lowercase[转换成小写]`
- 元素的垂直对齐方式：`vertical-align：sub[设置文字为下标]super[设置文字为上标]top[把元素的顶端与行中最高元素的顶端对齐]text-bottom[把元素的底端与父元素字体的底端对齐]` 
我们在应用css样式时，一定要注意它的默认值，因为不同的浏览器它的css默认值是不同的，为了网页的兼容性，尽量使用通用属性，不用存在兼容性差别的属性 

##css控制字体 
- 设置字号：`font-size:12px;`
- 设置字色：`color:#000000`
- 设置字体：`font-family:Arila,'宋体'`
- 设置行高（行与行之间的间距）：`line-height:150% =	line-height:1.5em` 
- 设置字体粗细：`font-weight:normal[正常]	bold[粗体]` 

##css控制背景 
- 背景：`background:颜色，图片，平铺方式，固定方式，位置` 
- 背景颜色：`background-color:#CCC` 
- 背景图像：`background-image:url` 
- 背景图像重复方式：`background-repeat:repeat,no-repeat,repeat-x,repeat-y` 
- 背景图像的位置：`background-position:top[left center right] center[left center right] bottom[left center right],x坐标 y坐标 `
- 背景图像的依附方式：`background-attachment:scroll,fixed` 

&背景图片默认情况下在水平和垂直平铺 

#div+css布局： 
1. 默认的文档流布局方式 
2. 浮动布局 float 
3. 定位布局 

position控制元素的位置

- static静态定位，页面中的每一个对象的默认值
- absolute绝对定位：将对象从文档流中分离出来，通过设置left,right,top.bottom四个方向相对于父级对象进行绝对定位。如果不存在这样的父对象，是依据body对象。
- relative相对定位：对象不从文档流中分离，通过设置left,right,top,bottom四个方向相对于自身位置进行相对定位）

当我们要想使用绝对定位时，必须要有两个条件：1.必须给父元素加定位属性，一般建议使用positiona:relative;2.给子元素，加绝对定位position:absolute同时要加方向属性。 
绝对定位是以父元素为基准点，进行定位，会脱离文档流。相对定位是根据其自身为基准点，进行定位。会离开原位置，但还占着原来的空间。 

##常见的div+css布局方式 
浮动（float）是css实现布局的一种方式，包括div在内的任何元素都可以浮动的方式进行显示。值：none：不浮动。left:对象向左浮动，而后面的内容流向对象的右侧,right:对象向右浮动，而后面的内容流向对象的左侧。浮动是将块元素的霸道属性，独占一行的行为取消，允许别人和它在同一行。浮动其实是，这个块，从原来的文档流模式中，分离出来，它后面的对象，就视它不存在。 

浮动，先浮后动，浮动的对象会先漂浮起来，离开原来的位置，后动就是它后面的对象，会向它原来的位置上，动起来 
—————————————————————————— 
###清除浮动
就是可以去掉前面对象浮动，对后面对象的影响,当元素有浮动属性时，会对其父元素或后面的元素产生影响，会出现一个布局错乱的现象，可以通过清除浮动的方法来解决，浮动元素的影响 
浮动的清理：（clear）值：
 
- none:默认值，允许两边都可以有浮动对象 
- left:不充许左边有浮动对象 
- right:不充许右边有浮动对象 
- both:左右两侧不允许有浮动对象 

当父元素没有指定高度时，并且它的子元素有浮动，这时这个父元素的高度不会自动增加，则导致父元素的一些东西（如背景）无法显示，此时要清除浮动带来的影响 
清除浮动的三种方法： 

1. 额外标签法： 

	这种方法应该说是最简单的一种，w3c建议在容器末尾增加一个`clear:both`的元素，强迫容器适应它的高度以便装下所有float元素 
	
		<div id="main"> 
		<div id='left'>左盒子</div> 
		<div id="right">右盒子</div> 
		<div class="clear"></div>增加一个空div 
		</div> 
		<div id="footer">底部</div> 
	这种方法的缺点就是会增加代码 
2. 父元素使用overflow方法： 

	通过设置父元素overflow值设置为hidden，是最简单的清除浮动方法，但如果子元素使用了定位布局，就会很难实现 
3. 利用伪对象after方法： 

定义一个类，使用伪对象after，控制浮动元素影响 网上最流行的清除浮动代码：
 
	.clearFix:after{ 
	clear:both; 
	display:block; 
	visibility:hidden; 
	height:0; 
	line-height:0; 
	content:''; 
	} 
	.clearFix{zoom:1}/*解决ie67兼容问题，高度*/ 


浮动的特别说明：如果浮动元素的高度不同，那么它们向下移动时可能被其它浮动元素“卡住”，直到有足够空间。

如果使用了浮动属性：则该元素不管是不是块元素，都会按照display:block来显示。
如果不希望别的元素在某个元素的左边或右边，可以使用清除浮动的方法`clear:right;clear:left;clear:both`

相对定位，相对该元素应当显示的左上角重新定位，虽然它脱离了标准流，但它的空间，默认被占用。
![](/images/相对定位.png)
绝对定位，对该元素最近的那个脱离了标准流的元素定位，如果没有父元素，则相对body左上角定位。
![](/images/绝对定位.png)
Fixed定位，就不管怎样，总是以视窗左上角定位。

left top属性对static定位不起作用，要使用margin-left/top
###css中溢出的使用： 
设置当对象的内容超过其指定高度及宽度时如何管理内容：`overflow:visible`(默认值，不剪切内容也不添加滚动条)`auto`(在必需时对象内容才会被裁切或显示滚动条)，`hidden`(不显示超过对象尺寸的内容)`scroll`(总是显示滚动条) 

ie6的双倍边距问题加一个`display=inline`

