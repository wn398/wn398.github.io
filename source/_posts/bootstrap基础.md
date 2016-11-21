title: "bootstrap基础"
date: 2016/11/21 星期一 PM 5:07:22 
updated: 2016/11/21 星期一 PM 5:07:28 
tags:
- bootstrap
- 前端

categories: 前端框架
---
##bootstrap基础

1. 为了让`<div>`在各种浏览器下产生同样的居中效果，将`margin-left`和`margin-right`的值设置为`auto`
2. bootStrap定义的全局`font-size`为14px，`line-height`为20px，`color`为`#333333`，`background-color`为`#ffffff`
3. Less中当需要引入无效的CSS语法或者LESS不能识别的字符时（通常是一些IE的Hack）.要避免LESS抛出异常，并避免LESS编译。此时，可以在字符串前加上~,并将需要转义的字符串用""包含起来，表示避免编译该行字符串。如：

		.class {
		filter: ~"progid:DXImageTransform.Microsoft.Alpha(opacity=20)";
		}

4. JavaScript表达式可以在LESS文件中使用，通过反引号的方式使用
5. Bootstrap变量全部部署在`variables.less`文件中（`bootstrap-master/less`）
6.  在BootStrap中所有混合都存储在`mixins.less`文件中，如果需要增加混合可以直接集成到`utilities.less`中，方便调用
7. bootStrap菜单标准结构如下：

		<ul class="nav nav-pills">
		<li class="dropdown">
		<a href="#" data-toggle="dropdown">激活元素</a>
		<ul class="dropdown-menu">
		<li><a href="#">菜单项1</a></li>
		<li><a href="#">菜单项2</a></li>
		<li><a href="#">菜单项3</a></li>
		  </ul>
		</li>
		~~~~
		</ul>
使用`dropdown-submenu`类可以设计二级菜单，用法很简单：在现有下拉菜单结构中，向任意的`<li>`标签添加`dropdown-submenu`类，即可自动赋予一个二级菜单。然后在该`<li>`标签中再嵌套一个下拉菜单容器`<ul class="dropdown-menu">`
通过添加包含`divider`类的标签，可以在下拉菜单中插入一条分隔线。如`<li class="divider">`
把下拉菜单组件包含框改为`dropup`，即可让下拉菜单向上弹出。
`pull-right`可以设计右对齐菜单，`pull-left`可以设计左对齐菜单。如果为下级菜单添加`pull-left`，则可以让它向左弹出显示。
8. 按钮组是将多个按键集合成一个页面组件。只需要使用`btn-group`类和一系列的`<a>`或者`<button>`标签，就可以轻易生成一个按钮组或按钮工具条如：

		<div class=" btn-group">
		<p class="btn">按键1</p>
		<li class="btn">按键2</li>
		<a class="btn">按钮3</a>
		<span class="btn">按键4</span>
		</div>
要将多个按钮组(btn-group)包含在一个btn-toolbar中,可以设计一个按钮工具条，以此设计一个更复杂的按钮组件。
通过添加`btn-group-vertical`样式类，可以设计垂直分布的按钮组
9. 设计按钮式下拉菜单  通过`data-toggle="dropdown"`触发下拉菜单交互显现，如：

		<div class="btn-group">
		<a class="btn" href="#" data-toggle="dropdown">下拉菜单<i calss="caret"></i><a>
		<ul class="dropdown-menu">
		<li><a href="#">菜单项1</a></li>
		<li><a href="#">菜单项2</a></li>
		<li><a href="#">菜单项3</a></li>
		</ul>
		<a class="btn" href="#">按钮</a>
		</div>
在设计下拉菜单时要引入`bootstrap-dropdown.js`
按钮式下拉菜单可以兼容所有尺寸的按钮，当使用`btn-large`,`btn-small`,`btn-mini`分别设计按钮大小时，下拉菜单能够自动调整显示的位置，以便紧贴按钮下沿显示。
10. 导航   bootstrap允许使用相同的标签，不同的类，实现不同风格的导航样式，具有非常高的可定制性。所有的导航组件，包括标签页，`pills`,导航列表标签，都必须使用`nav`类实现基础的导航效果。除了常见的导航，还可以利用`nav-stacked`类实现堆叠式的导航版式。

	1. 所有导航组件都具有相同的结构，并共用一一个样式类nav.基本结构代码如下：
	
			<ul class="nav">
			<li class="active"><a href="#">首页</a></li>
			<li><a href="#">导航标题1</a></li>
			<li><a href="#">导航标题2</a></li>
			</ul>
	2. 设计标签页  为导航结构添加nav-tabs样式类，就可以设计标签页（Tab,或称选项卡）。
	3. 设计pills导航  为导航结构添加`nav-pills`样式类，就可以设计pills（胶囊式）导航。
	4. 通过`pull-right`,`pull-left`设置导航对齐方式
	5. 导航组件在默认状态下是水平显示的，如果添加一个nav-stacked样式类即可让组件以堆叠式进行排列，即恢复列表的默认垂直方式显示。
11.  绑定导航和下拉菜单    下拉菜单（`dropdown`）是一个独立的组件，它可以与页面中任何元素（如按钮，导航等）捆绑使用。我们将一段HTML代码和下拉菜单捆绑在一起，然后借助下拉菜单的Javascrip插件，即可设计一个导航菜单。
	1. 设计标签页下拉菜单  在标签页选项中，包含一个下拉菜单结构，然后为标签项添加dropdown类，为下拉菜单结构添加`dropdwon-menu`。最后在标签项的超链接中绑定激活属性`data-toggle="dropdown"`,整个效果设计完毕
	2. 设计pills下拉菜单  针对`pills`导航结构，也可以进行相同的操作，设计一个`pills`下拉菜单。
12. 设计导航列表   导航列表是一种简洁式导航样式，与标签页，pills下拉菜单具有相同的列表结构，只要引入`nav-list`样式类即可。导航列表经常被用在侧栏位置。导航列表可以相互嵌套，用来设计多级导航列表结构。其用法与下拉菜单相似，只需为嵌套的列表结构添加`nav`和`nav-list`样式类即可。如：

		<ul class="nav nav-list">
		<li class="active"><a href="#">首页</a></li>
		<li ><a href="#" >微客</a></li>
		<li><a href="#" >微博</a>
		<ul class="nav nav-list">
		<li><a href="#">登陆</a></li>
		<li><a href="#">注册</a></li>
		<li><a href="#">退出</a></li>
		</ul>
		</li>
		</ul>
13. 激活标签页   让标签页每个tab项能自由切换，并能控制tab项目对应内容框的显示和隐藏。具体方法如下：
	1. 需要用到Jquery插件的支持，并导入`bootstrap-tab.jsp`
	2. 在标签页结构基础上，添加内容包含框，通过tab-content定义包含框为标签页的内容显示框。在内容包含框中插入与标签页结构对应的多个子内容框，并使用`tab-pane`进行定义
	3. 为每个内容框定义id值，并在标签列表项为每个超链接绑定锚链接
	4. 为每个标签项超链接定义`data-toggle="tab"`属性，激活标签页的交互行为。

			<div>
			<ul calss="nav nav-tabs">
			<li class = "active"><a href="#tab1" data-toggle="tab">首页</a></li>
			<li><a href="#tab2" data-toggle="tab">微客</a></li>
			<li><a href="#tab3" data-toggle="tab">微博</a></li>
			</ul>
			
			<div class="tab-content">
			<div class="tab-pane active" id="tab1">首页内容框</div>
			<div class="tab-pane" id="tab2">微客内容框</div>
			<div class="tab-pane" id="tab3">微博内容框</div>
			</div>
如果要设计标签页淡入效果，只需为每个标签页选项`tab-pane`添加`fade`类即可
14. Bootstrap融合了4种标签页布局样式：top（默认）,right,bottom和left。要设计标签页布局，向标签页组件包含框中添加布局类即可。`tabs-right`,`tabs-left`,`tabs-bottom`
15.  设计导航条  导航条是一个长方形区块，其中可以包含导航或按钮，以方便用户执行导航操作。Bootstrap配合使用`navbar`和`navbar-inner`类定义导航条包含框，此时的导航条是一个空白区域：如：

			<div class="navbar">
				<div class="navbar-inner">
					<a class="brand" href="#">网站名称</a>
					<ul class="nav">
						<li class="active"><a href="#">首页</a></li>
						<li><a href="#">导航标题1</a></li>
						<li><a href="#">导航标题2</a></li>
					</ul>
				</div>
			</div>
默认情况下，导航条是静态（`static`），不是定位显示（`fixed,absolute`）.完整的导航条一般包含项目（或网站）名称和导航项。项目名称使用brand样式类进行设计，一般位于导航条的左侧。如果将导航条放入`container`包含框中，可以限制导航条的宽度，默认为960px。使用`divider-vertical`可以为导航条添加项目分隔线，其用法与divider样式类用法相同。
16.  绑定表单和下拉菜单。 如果希望在导航条中放置一个表单，需要为表单框添加`navbar-form`样式类，同时设置对齐方式（`pull-left`或`pull-right`）.   在导航条内为<form>添加`navbar-search`，并为输入文本框添加`search-query`，即可获得一个搜索表单。
17.  导航条布局  导航条可以在页面中进行固定布局，如固定显示在浏览器窗口的顶部或底部，也可以改变导航条的样式风格，或者设计响应式导航条。
	1. 置顶导航条   为导航条外包含框添加`navbar-fixed-top`类，就可以导航条置顶显示。注意，为了确保导航条不覆盖其他页面内容，建议给`<body>`增加40px的`padding-top（内补）`。一定要在Bootstrap核心css文件`（bootstrap.css）`之后，响应式css(`bootstrap-responsive.css`)文件之前添加该样式。
	2. 置底导航条   同样，如果为导航条外包含标签添加`navbar-fixed-bottom`样式类，则可以让导航条置底显示。此时，也应该为<body>标签定义底部补白为40像素，以避免导航条遮盖住网页正文内容。
	3. 设计导航条反色效果    通过为导航条外包含框添加`navbar-inverse`样式，可以设计反色效果的导航条。
	4. 响应式导航条    响应式导航条能够根据窗口宽度自动调整导航条的显示状态。方法是为需要自动响应的导航框添加`nav-collapse`和`collapse`样式类，然后添加一个按钮，宝座`btn-navbar`样式类，并为该按钮设置`data-toggle="collapse"`属性，激活响应式交互，同时使用`data-target=".navbar-responsive-collapse"`属性绑定与导航框之间的响应联系。如：

					<div class="navbar">
						<div class="navbar-inner">
							<a class="btn btn-navbar" data-toggle="collapse" data-target=".navbar-responsive-collapse">
							<span class="icon-bar"></span>
							<span class="icon-bar"></span>
							<span class="icon-bar"></span>
							</a>
						
							<a class="brand" href="#">响应式导航条</a>
							<ul class="nav nav-collapse collapse navbar-responsive-collapse">
							<li class="active"><a href="#">首页</a></li>
							<li><a href="#">微博</a></li>
							<li><a href="#">微信</a></li>
							</ul>
						</div>
					</div>

18. 设计面包屑  使用`breadcrumb`样式类，可以把列表结构设计成面包屑导航样式。用法比较简单，如在下面的示例中直接为`<ul>`标签添加`breadcrumb`类，然后使用`<span class="divider">`标签插入一个分隔符，即可设计出面包屑组件效果。

					<ul class="breadcrumb">
						<li>
							<a href="#">首页</a>
							<span class="divider">-></span>
						</li>
						<li>
							<a href="#">新闻频道</a>
							<span class="divider">-></span>	
						</li>
						<li>
							<a href="#">国内新闻</a>
							<span class="divider">-></span>
						</li>
						<li class="active">新闻详细页</li>
					</ul>

19. 设计分页组件  页码（pagination）也是非常常用的页面要素，Bootstrap提供两份种风格的页码组件。
使用`pagination`类可以设计标准的分布组件样式。如下面代码中使用`<div class="pagination">`标签作为分页组件的包含框：

			<div class="pagination">
			<ul>
			<li><a href="#">上一页</a></li>
			<li><a href="#">1</a></li>
			<li><a href="#">2</a></li>
			<li><a href="#">3</a></li>
			<li><a href="#">4</a></li>
			<li><a href="#">下一页</a></li>
			</ul>
			</div>
`pagination`类只能够应用于列表框外包含框上。把`pagination`类直接添加到`<ul>`标签上(`<ul class="pagination">`)是无效的。
设置分布组件大小 ：`pagination-large`,`pagination-small`,`pagination-mini`.  
设置对齐方式：`pagination-centered`和`pagiantion-right`默认是左对齐的

20.  设计翻页组件。  翻页组件是另类分页组件样式，它用更少的标签和样式来创建简单的"前一页"和“后一页”这种分页方式非常适用于简单的网站，如博客或者杂志网站。
  使用pager类样式可以设计翻页组件，该组件仅有两个列表项。如：

		<ul class="pager">
		<li><a href="#">上一页</a></li>
		<li><a href="#">下一页</a><li>
		</ul>
翻页组件默认是居中对齐的，也可以把两份个按钮分别置于两侧。具体方法是：分别为两个选项引入`previouse`和`next`类样式。
21.  标签与徽章      标签与徽章可以作为页面装饰性或提示性短语被广泛应用，在传统设计中标签和徽章主要通过图标来设计。
标签是一个很好用的页面小要素，Bootstrap具有多种颜色标签，表达不同的页面信息。只需要简单使用`label`样式类即可。徽章是细小而简单的组件，用于指示或计算某种类别的要素，在E-mail客户端很常见。
与按钮的btn一样，Bootstrap为标签和徽章设计了两套样式风格。标签样式通过label样式类实现，而徽章通过badge样式类实现。两套风格的基本外观相同，都是通过同一个样式实现，以行内块状显示，字体比较小，为11.844px,字体加精显示，文本适当添加一点阴影效果，字体颜色为白色，背景色为灰色。
当然，两套风格也存在差异，标签呈现圆角矩形外观，而徽章呈现椭圆形外观。
如在`<span>`标签中，分别引入标签和徽章样式：
`<span class="label">标签样式</span>`
`<span class="badge">徽章样式</span>`
在标签样式中，Bootstrap提供了一套可选样式方案：

		label-important: 重要，通过醒目的视觉变化（深红色），
		label-info:信息  通过舒适的色彩设计（浅蓝色）
		label-success:成功,通过积极的亮绿色
		label-warning:警告，通过通知黄色
		label-inverse:反向,通过黑色背景
同理bootstrap也为徽章提供了一套可选样式与标签类似。
注意：当标签和徽章元素内不包含任何文本，则bootstrap会隐藏这些标签和徽章元素，以实现标签和徽章的折叠。
22. 缩略图  
缩略图（`thumbnail`）可以作为图片，视频，文字的风格结构展示。实现默认形式的缩略图，只需要简单的`thumbnails`标签。`Thumbnails`多应用于图片，视频的搜索结果等页面，还可以链接到其他页面。它具有很好的可定制性，可以将文章片段，按钮等标签融入缩略图，同时可以混合与匹配不同大小的缩略图。、
  缩略图能够给图片，视频，文本等加入栅格功能。组成缩略图的标签很简单：使用`<ul>`标签包裹任意数量的`<li>`即可。首先需要 在`<ul>`标签中引入`thumbnails`样式类，指定当前列表框为缩略图集，然后在`<a>`标签中引入`thumbnail`类，设置当前超链接包含图像为缩略图效果。
如：

		<ul class="thumbnails">
		<li class="span1">
		<a href="#" class="thumbnail"><img src="images/1.jpg"></a>
		</li>
		<li class="span2">
		<a href="#" class="thumbnail"><img src="images/1.jpg"></a>
		</li>
		<li class="span3">
		<a href="#" class="thumbnail"><img src="images/1.jpg"></a>
		</li>
		<li class="span4">
		<a href="#" class="thumbnail"><img src="images/1.jpg"></a>
		</li>
		</ul>
在缩略图中自定义HTML内容，标签的变化不大，为了放入块级内容，可以把`<a>`替换成`<div>`。
23.  警告框
使用alert类可以设计提示框组件，效果类似于IE浏览器的提示框。如在下面的代码中为`<div>`标签引入alert类，并在警告框包含框中包含一个关闭按钮和一条提示信息。

			<div class="alert">
			<button type="button" class="close">&times;</button>
			<strong>警告！</strong>确定要删除当前信息？
			</div>
通过添加其他类，可以改变警告框的语义：

			alert-error: 错误  浅红色背景
			alert-danger  危险  浅红色背景
			alert-success  成功，浅绿色背景
			alert-info  信息  浅蓝色背景
如果提示信息比较多，需要占用很多区域，则可以通过添加`alert-block`类，以增加警告框上下方向的补白，让警告框看起来更大方。如，为长提示信息添加一个`alert-block`类，增加上下补白，避免警告框看起来狭窄

			<div class="alert alert-info alert-block">
			<button type="button" class="close">&times;</button>
			<strong>提示<strong>容内容内容内容内容内容内容内容内容内容内
			</div>
通过为警告框添加关闭按钮，可以关闭警告框。在上面的示例中，已经添加了一个关闭按钮，但是该按钮不具备交互功能。
在Safari和Opera浏览器移动版上，当使用<a>标签关闭警告框时，除了添加`data-dismiss="alert"`属性外，还需要包含`href="#"`属性。

			<div class="alert">
			<a href="#" class="close" data-dismiss="alert">&times;</a>
			<strong>警告！</strong>确定要删除信息吗、
			</div>
或者使用带有data属性的`<button>`标签。当使用`<button>`时，必须包含`type="button"`属性，否则无法执行

			<div class="alert">
			<button type="button" class="close" data-dismiss="alert">&times;</button>
			<strong>警告！</strong>确定要删除信息吗?
			</div>
如果希望通过js代码快速关闭警告框，可以使用bootstrap定义的警告框jQuery插件，然后与jQuery框架一起引入到页面中即可（`bootstrap-alert.js`）

24. 进度条
进度条一般由嵌套的两份层结构标签构成，外层标签引入progress类，用来设计进度槽，内层标签引入bar类，用来设计进度条。基本结构如下：

			<div class="progress">
			<div class="bar" style="width: 50%;"></div>
			</div>
进度条默认样式是带有垂直渐变的进度条，进度槽显示为灰色。
	1. 设计条纹样式  通过`progress-striped`类实现
	2. 设计动态条纹样式   如果为`progress-striped`添加`active`样式，可创建一个从右向左变化的条纹样式
	3. 设置个性进度条    与警告框一样，进度条也允许通过添加其他类，改变进度条的背景效果。如：

			progress-info 浅蓝色背景  progress-success  浅绿色背景
			progress-warning 浅黄色背景  progress-danger 浅红色背景

25.  媒体    媒体对象是一类特殊版式的区块样式，用来设计图文混排或者与文本混排的效果，作为抽象的结构样式，媒体对象可用于构建不同类型的组件，设计具有在文本内容的左或右对齐的图片。
 1. 媒体版式   在默认情况下，媒体对象组件的默认样式是在内容区域的左侧或右侧浮动一个媒体对象，如图片，视频，音频，Flash动画等。
    构件媒体对象组件需要3个类样式，具体说明如下：

			media: 创建媒体对象组件包含框
			media-object:定义媒体对象，如图片，视频，音频，动画
			media-body:定义媒体对象的正文区域。在该区域可以使用media-heading定义媒体对象组件的正文标题。
如下例:

		<div class="media"> <a class="pull-left" href="#"> <img class="media-object"  src="images/nayun.png"> </a>
		    <div class="media-body">
		        <h2 class="media-heading">马云：金融业要服务80%以前没有被服务好的客户，需要新的思想与技术</h2>
		        <div class="media">
		            <p>在今天举行的“外滩国际金融峰会”上，复星老板郭广昌在致辞里说，他希望问同来开会的马云：互联网金融和金融互联网，到底给我们带来哪些机会、带来哪些挑战。</p>
		            <p>这个问题太大，马主席肯定也不会现场具体做答。在事情没做之前就大声嚷嚷、指点大家应该怎么去做的，是老师，不是商人。马云当过老师，但归根结底是商人。所以他的公开讲话经常是这样的：给世界提出问题，但并不会给出答案——哪怕他其实已在暗中埋头答题。</p>
		            <p>他做过的电商是这样，眼下正在做的物流骨干网是这样（你能清晰知道菜鸟具体在做哪些工作吗？不知道），阿里参与的金融领域诸多事宜也是这样。</p>
		            <p>郭广昌们得不到具体的答案不要紧，这并不妨碍我们去听下马云对金融世界提出的问题：“如何能服务好剩下的那80%的客户？”马云认为，凭着互联网思想与技术，这个在传统金融业里难以求解的问题是可以求解出来的。</p>
		        </div>
		    </div>
		</div>
 2. bootstrap为媒体对象提供了列表结构，通过引入media-list样式类，可以设计媒体对象列表效果。在媒体列表结构中，每个列表项目都是一个独立的媒体对象组件。

26.  版式
  `Hero-unit`是一个轻量级的可扩展组件，主要用于市场推广网站显示大量的要素，而`Page-header`则用于简单地为页面段落的头部设置一个合适的空间和排版形式。
   1. 设计hero区块使用`hero-unit`类，在hero区块可以添加标题，说明性文本，导航按钮等

			<div class="hero-unit">
			<h1>Hero标题</h1>
			<p>说明性文字</p>
			<p><a class="btn btn-primary btn-large">更多</a></p>
			</div>
page-header可以设计网页标题，相当于一个标题框

27.  well组件    用来设计一个内嵌容器，能够很好地包含指定对象或者页面内容。如
`<div class="well"><img src="images/4.jpg" style="width:200px"></div>`  通过well中添加`well-large`或者`well-small`可以调整容器的补白空间大小和圆角大小。

28.  使用close样式类可以设计关闭图标按钮