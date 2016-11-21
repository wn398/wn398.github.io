title: "bootstrap插件"
date: 2016/11/21 星期一 PM 5:43:34 
updated: 2016/11/21 星期一 PM 5:43:38 
tags:
- bootstrap
- 前端

categories: 前端框架
---
## 使用bootstrap插件

bootstrap包含了丰富的组件，组件还是静态对象，如果要让这些组件动起来，还需要JavaScript插件配合。简单地说，组件是框架和样式，而插件是交互行为和动态效果。

### 使用bootstrap插件的方式 
  Bootstrap提供了两种调用插件的方式，一种是data定义法，另一种是JavaScript调用法。
      
1. data调用
这种方法最适用，方便，只需要在页面中目标元素上定义data属性，就可以启用插件，且不用写一行JS代码，这是BootStrap的一等API，建议大家首选这种方式。   `data-toggle`是bootstrap激活特定插件的专用自定义属性，它的值为对应插件的字符串名称。另外，大部分bootstrap插件还需要data-targe属性配合使用。 data-target也是一个bootstrap自定义属性，用来指定控制对象，该属性值为一个jQuery选择符。
注意：在某些特殊情况下，可能需要禁用这种默认动作。bootstrap提供了禁用data属性API的方式，通过解除绑定在body上的被命名为`“data-api”`的事件即可实现。代码：`$('body').off('.data-api').`
还可以解除特定插件的事件绑定，只要插件名和data-api链接在一起作为参数使用：`$('body').off('.alert.data-api')`
2. JavaScript调用   bootstrap插件也支持javascrip调用。所有插件都可以单独或链式调用，与jQuery插件用法相同。
大部分Bootstrap插件都支持自定义事件，这些事件以英语动词原型和过去分词来表示。动态原形的如show在事件执行前触发，过去分词形式的如shown在动作执行后触发。

### 模态对话框
bootstrap对话框需要`bootstrap-model.js`支持。
基本用法：

		<a href="#myModal" class="btn" data-toggle="modal">弹出对话框</a>
		<div id="myModal" class="modal hide fade">
		    <h1>弹出对话框</h1>
		    <p>这是弹出的对话框吗？</p>
		</div>

使用modal样式类可以定义弹出对话框的外框，如果引用`modal-header`,`modal-body`和`modal-footer`三个类样式，可以定义弹出对话框的标题区，主体区和脚注区。
标准对话框中包含两个关闭按钮，一个是对话框右上角的关闭图标，另一个是页脚区域的关闭按钮。这两份个关闭对话框的标签通过自定义属性`data-dismiss`触发对话框关闭行为，`data-dismiss`属性值指定了要关闭的对话框组件。
打开对话框的行为通过`<a>`标签来实现，其中`href`属性通过锚记与对话框（`<div id="myModal">`标签）建立绑定关系，然后通过自定义属性`data-toggle`激活对话框显示行为，`data-toggle`属性值指定了要打开对话框的组件。

	<a href="#myModal" class="btn" data-toggle="modal">弹出对话框</a>
	<div id="myModal" class="modal hide fade">
	    <div class="modal-header">
	        <button class="close" data-dismiss="modal">×</button>
	        <h3>标题</h3>
	    </div>
	    <div class="modal-body">
	        <p>正文</p>
	    </div>
	    <div class="modal-footer">
	        <button class="btn btn-info" data-dismiss="modal">关闭</button>
	    </div>
	</div>
调用对话框

1. HTML属性调用    使用`data-toggle`属性定义激活插件的类型，对于对话框插件来说，即设置为`data-toggle="modal"`,  设置具体打开的目标对象。如上面的例子。当激活对象为按钮或者其他元素时，可以设置自定义属性`data-target`为对话框包含框的ID值，以绑定目标对象，指向某个要被启动的对话框。
`<button data-toggle="modal" data-target="#myModal" class="btn">打开对话框</button>`
当激活元素为超链接元素时，可以直接在href属性上设置对话框包含框的ID值，以锚点的形式绑定目标对象，以指向某个将要被启动的对话框。
`<a href="#myModal" data-toggle="modal" class="btn">打开对话框</a>`
2. javascript调用 
 如果需要设计更复杂的对话框调用，则建议使用javascript调用。其实，JavaScript调用也比较简单，直接使用modal（）构造函数即可，其用法与jQuery插件用法一致。如上面针对超链接标签绑定click事件：

		<script type="text/JavaScript">
		 $(function(){
		$(".btn").click(function(){
		$("#myModal").modal();
		})
		})
		</script>
`modal（）`构造函数可以传递一个配置对象，该对象包含的配置属性如下：

		backdrop   boolean 默认true 是否显示背景遮罩层，同时设置单击对话框其它区域是否关闭对话框。默认单击时会自动隐藏对话框和遮罩层。
		keyboard boolean 默认true 是否允许Esc键关闭对话框
		show  boolean true 在初始状态是否显示对话框，默认为true
		remote 类型path 默认false 设置一个远程url，bootstrap将利用jQuery加载该连接页面，并把响应的数据添加到对话框的modal-body包含框中。也可以使用以下方式加载远程数据：<a data-toggle="modal" href="remote.html" data-target="#modal">click me</a>

注意，如果使用HTML属性调用对话框时，上面的选项也可以通过data属性传递给组件。对于data属性，将选项名称附于data-字符串之后，类型于`data-backdrop=""`.

		modal()构造函数也可以接收特定字符串参数，以方便手动控制显示或隐藏。
		modal("toggle")   手动打开或隐藏一个对话框
		modal("show")  手动打开一个对话框
		modal("hide")  手动隐藏一个对话框。
		Bootstrap为对话框插件定义了4个事件，用来响应特定操作阶段的用户行为，如下：
		show  当调用显示对话框的方法是会触 发该事件
		shown 当对话框显示完毕后触发该事件
		hide  当调用隐藏对话框的方法时会触发该事件
		hidden 当对话框隐藏完毕后触发该事件


			<script type="text/javascript">
			$(function(){
				$("#myModal").on("show",function(){
					alert("对话框开始打开");
				})
				$("#myModal").on("shown",function(){
					alert("对话框已经打开");
				})
				$("#myModal").on("hide",function(){
					alert("对话框开始关闭");
				})
				$("#myModal").on("hidden",function(){
					alert("对话框已经关闭");
				})	
				$("#myModal").modal({
					backdrop:false,
					show:true
				});			
				$(".btn").click(function(){
					$("#myModal").modal("toggle");
				})
			})
			</script>

###下拉项
1. HTML属性调用
在超链接或按钮上添加`data-toggle="dropdown"`属性，即可激活下拉项交互行为。  注意：为了保证超链接`<a>`标签的URL符合规范，建议使用`data-target`属性代替`href="#"`,而href属性用来执行链接操作

		<div class="dropdown">
		<a href="/" class="btn" data-toggle="dropdown" data-target="#">按钮<i class="icon-arrow-down"></i></a>
		<ul class="dropdown-menu">
		<li><a href="#">...</a></li>
		</ul>
		</div>
2. JavaScript调用

		$(function(){
		$(".btn").dropdown('toggle')
		})
###滚动监听
bootstrap的`scrollspy`(滚动监听)插件能够根据滚动的位置，自动更新导航条中对应的导航项。
第一步：使用滚动监听插件之前，应在页面中导入`bootstrap-scrollspy.js`脚本文件或`bootstrap.js`脚本文件
第二步：设计导航条，在导航条中包含一个下拉项，分别为导航条列表项和下拉项设计锚点链接，锚记分别为`“#1”“#2”“#3”“#4”“#5”`。同时为导航条外框定义ID值 （`id="menu"`）,以方便滚动临沂

			<div id="menu" class="navbar navbar-fixed-top">
			<div class="navbar-inner">
			<ul class="nav">
			<li><a href="#1">列表1</a></li>
			<li><a href="#2">列表2</a></li>
			<li class="dropdown"><a href="#" data-toggle="dropdown">下拉列表<b class="caret"></b></a>
			<ul class="dropdown-menu">
			<li><a href="#3">列表3</a></li>
			<li><a href="#4">列表4</a></li>
			<li class="divider"></li>
			<li><a href="#5">列表5</a></li>
			</ul>
			</li>
			</ul>
			</div>
			</div>
第三步：设计监听对象。这里设计一个包含框，其中存放多个子内容框，代码如下。在内容框中，为每个标题设置锚点位置，即为每个`<h3>`标签定义ID值，对应值分别为`1，2，3，4，5`

			<div class="scrollspy">
			<h3 id="1">列表1<h3>
			<p><img src="images/1.jpg"></p>
			<h3 id="1">列表2<h3>
			<p><img src="images/2.jpg"></p>
			<h3 id="1">列表3<h3>
			<p><img src="images/3.jpg"></p>
			<h3 id="1">列表4<h3>
			<p><img src="images/4.jpg"></p>
			<h3 id="1">列表5<h3>
			<p><img src="images/5.jpg"></p>
			</div>
第4步：为监听对象(`<div class="scrollspy">`)定义样式类，设计该包含框为固定大小，并显示滚动条：

			.scrollspy{
			width:520px;
			height:300px;
			overflow:scroll;
			}
			.scrollspy-example img{width:500px;}
第5步：为监听对象设置被监听的Data属性：`data-spy="scroll"`,指定监听的导航条：`data-target="#menu"`,定义监听过程中流动条偏移位置：`data-spy="scroll"  data-offset="30".`完成代码如下：

			<div data-spy="scroll"  data-target="#menu" data-offset="30" class="scrollspy"></div>

控制滚动监听：
1. 通过data属性调用流动监听。 在页面为被监听的元素定义`data-spy="scroll" `属性，即可激活Bootstrap滚动监听插件。如果要监听浏览器的内容流动，则可以为`<body>`标签添加`data-spy="scroll"`属性`<body data-spy="scroll">`然后，使用`data-target="目标对象" `定义监听的导航结构。
2. 通过javascript调用流动监听
直接为被监听的对象绑定`scrollspy()`方法即可。
   在设计流动监听时，必须为导航条中的链接指定相应的目标ID，如`<a href="#1">列表1<a>`必须与页面中类似`<h3 id="1">列表1</h3>`的标签相呼应，即要为导航条设计好页内锚点。
`scrollspy()`构造函数能够接收一个参数对象，在其中可以设置滚动偏移的值，当该属性为正值时，则流动条向上偏移，为负值时将向下偏移。
对于Bootstrap的插件来说，所有参数都可以通过`data`属性或Javascrip传递。对于data属性，将参数名附着到`data-`后面。如针对上面的`offset`配置参数，可以在HTML中通过`data-offset=""`进行相同的配置。
滚动监听插件定义了一个事件：`activate`。该事件在当一个新的导航项目被激活时触发。如，下面的示例利用activate事件跟踪当前菜单项，判断如果当前项目为下拉菜单的包含框，即下拉菜单的父元素（`<li class="dropdown">`）,则展开下拉菜单，否则收起下拉菜单。

		$("body").on("activate",function(e){
		if(e.target && $(e.target).hasClass("dropdown")){
		$(e.target).children("ul.dropdown-menu").css("display","block":);
		}else{
		$(e.target).parent().find("ul.dropdown-menu").css("display","none");
		}
		})
滚动监听插件还定义了一个方法：`scrollspy('refresh')`.当滚动监听所作用的DOM有增删页面元素的操作时，需要调用下面的refresh方法：

		$('[data-spy="scroll"]').each(function (){
		var $spy=$(this).scrollspy('refresh')
		});

### 标签页
设计标签页组件结构，在设计HTML结构时，要注意两个问题：第一，导航区内每个超链接的链接定义为锚点链接，锚点值指向对应的标签内容框的ID值 。第二，导航内容区域，需要使用`tab-content`类定义外包含框，使用`tab-pane`类定义每个`tab`内容框。最后，在导航区域内为每个超链接定义`data-toggle="tab"`,激活标签页插件。对于下拉菜单选项，也可以通过该属性激活它们对应的行为。完整结构代码如下：

			<ul class="nav nav-tabs">
		        <li class="active"><a href="#tab1" data-toggle="tab">超值特惠</a></li>
		        <li><a href="#tab2" data-toggle="tab">当季推荐</a></li>
		        <li><a href="#tab3" data-toggle="tab">潮流搭配</a></li>
		        <li class="dropdown"><a href="#" class="dropdown-toggle" data-toggle="dropdown">更多选择 <b class="caret"></b></a>
		            <ul class="dropdown-menu">
		                <li><a href="#tab4" data-toggle="tab">新品速递</a></li>
		                <li><a href="#tab5" data-toggle="tab">特卖商品</a></li>
		            </ul>
		        </li>
		    </ul>
		    <div class="tab-content">
		        <div class="tab-pane active" id="tab1"><img src="images/1.png"></div>
		        <div class="tab-pane fade" id="tab2"><img src="images/2.png"></div>
		        <div class="tab-pane fade" id="tab3"><img src="images/3.png"></div>
		        <div class="tab-pane fade" id="tab4"><img src="images/4.png"></div>
		        <div class="tab-pane fade" id="tab5"><img src="images/5.png"></div>
		    </div>

1. html调用tab标签插件：只需要在导航标签或者导航pill的超链接中添加`data-toggle="tab"`或者`data-toggle="pill"`属性即可。同时确保导航包含框添加`nav`和`nav-tabs`样式类。
2. Javascrip方式调用tab标签插件，调用方法是在每个超链接的单击事件中调用`tab('show')`方法，显示对应的标签内容框。如：

		<script type="text/Javascript'>
		$(function(){
		$(".nav-tabs a").click(function(e){
		e.preventDefault();//阻止超链接的默认行为
		$(this).tab('show')
		})
		})
标签页插件包含两个事件：
	1. `show()` 在一个标签选项被显示前将触发。通过`e.target`和`e.relatedTarget`可以获取当前触发的Tab标签和前面一个被激活的Tab标签
	2. `shown()` 在一个标签选项被显示之后触发。

###工具提示
在网页设计中，经常需要提示用户某些功能或相关信息，而HTML自带的title属性比较简陋：样式单调，功能单一，用法呆板。Bootstrap定义了一个工具提示插件。需要`bootstrap-tooltip.js`

在页面中设计一个超链接，定义title属性，设置工具提示文本信息：
`<a href="http://www.baidu.com/" title="百度一下" ><img src="images/logo.gif"></a>`
出于性能方面的考虑，bootstrap没有支持工具提示插件通过data属激活，因此用户必须手动调用。调用通过`tooltip()`方法来实现：

		<script type="text/javascript">
		$(function(){
		$("a").mouseover(function(){
		$(this).tooltip('show')
		})
		})
通过`data-placement=""`属性可以设置提示信息的显示位置，取值包括`top`,`right`,`bottom`,`left`。

控制工具提示插件：
在使用工具提示插件时，可以通过JavaScript触发，核心代码如下：
`$("#example").tooltip(options)`
其中`$("#example")`表示匹配的页面元素，options是一个参数对象，可以配置工具提示的相关设置属性：

		animation boolean类型 默认true 是否应用css淡入淡出过渡特效显示工具提示
		html boolean类型 默认false 是否插入HTML字符串，如果设置为false，则使用jQuery的text()方法插入文本，就不用担心XSS攻击。
		placement 类型string或function 默认top，设置提示位置，有top,bottom,left,right.
		selector string类型  默认false 设置一个选择器字符串，，则具体提示针对选择器匹配的目标进行显示。
		title 类型string或function 默认“”如果title属性不存在，则需要显示的提示文本。
		trigger 类型String  默认‘hover focus’ 设置工具提示的触发方式，包括单击（click）,鼠标经过（hover）,获取焦点（focus）或者手动（manual）可以指定多种方式，多种方式之间通过空格进行分隔。
		delay  类型number或object 默认0， 延迟显示和隐藏工具提示，不适用于手动触发类型。如果提供一个数值，则表示隐藏或者显示的延迟时间，如果是一个对象结构，可以这样进行设置{show:500,hide:100}分别表示显示和隐藏的时间
		container 类型string或false,默认false 是否追加一个特定的元素容器提示：body.
		可以通过data属性或javascript传递参数。对于data属性，将参数名附着到data-后面即可，如data-animation="".也可以针对单个工具提示指定单独的data属性。

### 弹出提示
弹出提示是工具提示的子类，建立在工具提示插件基础之上进行拓展。它比工具提示插件多了一个content参数，除了一些默认值不同外，用法基本相同。与工具提示插件一样，弹出提示也没有自定义事件。  弹出提示插件需要`bootstrap-popover.js`文件支持。

在页面中设计一个超链接，定义title属性，设置弹出提示标题信息，定义`data-content`属性，设置弹出提示的正文内容，代码如下：
`<a href="#" class="btn btn-large btn-success" title="弹出提示标题"  data-content="这里将显示正文">单击查看效果</a>`
 出于性能方面考虑，Bootstrap没有支持弹出提示插件通过data属性激活，需要手动调用，调用方法是`popover()`

			$(function(){
			$("a").popover();
			});
与工具提示默认显示位置不同，弹出提示默认显示位置在目标右侧，通过`data-placement=""`属性可以设置提示信息的显示位置，取值包括`top,right,bottom,left`。 
与工具插件类似，弹出插件也可以通过参数控制。参数与工具插件的参数一样。

### 警告框
  
设计一个警告框包含框，并添加一个关闭按钮：

		<div class="alert fade in">
		<button type="button" class="close" data-dismiss="alert">&times;</button>
		<strong>警告框标题</strong>说明文字
		</div>
通过js为某个警告框添加关闭功能：
	`$(".alert").alert("close")`
如果不使用js，则在HTML标签中定义属性即可，仅需要将`data-dismiss="alert"`添加到关闭按钮上，即可自动赋予某个警告框关闭功能。警告框支持两个事件，close 在关闭前触发，closed在关闭后触发。

### 按钮
Bootstrap把按钮设计得非常智能，只有一个调用接口，使用很方便。需要`bootstrap-button.js`。 一般情况下，要激活按钮的行为，可以使用`data-toggle`属性，用法如下：
`<button type="button" class="btn btn-primary" data-toggle="button">激活按钮</button>`

1. 加载状态
通过按钮可以设计状态提示，当单击按钮时，会显示loading状态信息。下面示例设计一个按钮，当单击按钮时将调用button("loading")方法，触发按钮的加载状态

		<script type="text/JavaScript">
		$(function(){
		$(".btn").click(function(){
		$(this).button("loading");
		})
		})
		<button data-loading-text="正在加载。。" class="btn btn-primary">加载</button>
上面代码中，通过`data-loading-text`属性定义加载的信息文本，通过`button（“loading”）`方法激活按钮的加载状态行为。
2. 模拟复选框组
 复选框组是组复选框，可以实现多选操作。使用按钮组模拟复选框组，能够设计更具个性的复选框样式。 如下例中设计3个按钮组成一组，然后通过`data-toggle="buttons-checkbox"`属性把它们定义为复选框组，单击的按键将显示深色的背景，再次单击将会显示浅色背景效果：

		<div class="btn-group" data-toggle="buttons-checkbox">
		    <button type="button" class="btn btn-primary">语文</button>
		    <button type="button" class="btn btn-primary">数学</button>
		    <button type="button" class="btn btn-primary">英语</button>
		</div>
3. 模拟单选按钮组
下面代码中设计两个按钮组成一组，然后通过`data-toggle="buttons-radio"`属性把它们定义为单选按钮组，单击的按钮将显示深色的背景，这样可以在两份个按钮之间进行切换：

		<div class="btn-group" data-toggle="buttons-radio">
		    <button type="button" class="btn btn-primary">男</button>
		    <button type="button" class="btn btn-primary">女</button>
		</div>
4. 重置和完成状态
在按钮处于加载状态或者其他状态中时，可以使用`data-reset-text`和`data-complete-text`属性设置重置和完成提示信息，然后通过`button("reset")`和`button("complete")`方法来激活它们。
  如下面示例中为按钮设计3个状态 ，一个是加载状态，一个是完成状态，一个是重置状态，然后设计当单击按钮时触发加载状态，延迟2秒之后显示加载完成状态提示，再过2秒之后显示重置按钮状态。

		<script type="text/javascript">
		$(function(){
			$(".btn").click(function(){
				$(this).button("loading");
				setTimeout((function(_this){
					return function(){
						$(_this).button("complete");
					}
				})(this), 2000);	
				setTimeout((function(_this){
					return function(){
						$(_this).button("reset");
					}
				})(this), 4000);		
			})
		});  
		     
		</script>
		<button  class="btn btn-primary" data-loading-text="正在加载..." data-reset-text="重新加载" data-complete-text="完成加载">加载</button>
5. 状态切换
使用`data-toggle`属性可以激活按钮的行为状态，实现在激活和未激活之间进行状态切换。下面代码可以激活按钮行为特性，单击时将会激活按钮，再次单击将会让按钮恢复为默认状态
`<button type="button" class="btn" data-toggle="button">按钮状态切换</button>`
也可以使用JavaScript脚本来激活按钮的行为状态。针对上面的这行代码，转换为JavaScript脚本形式激活，则代码如下：

		<script type="text/JavaScript">
		$(function(){
		$(".btn").click(function(){
		$(this).button("toggle");
		})
		})
		</scrpt>
		<button type="button" class="btn">按钮状态切换</button>

### 折叠插件
折叠插件需要`bootstrap-collapse.js`文件支持
折叠插件具有复杂的结构，调用比较简单，可以通过data属性调用，也可以用JS调用：
第一步：在使用折叠插件之前，导入相关文件 和样式表
第二步：设计折叠包含框，定义`accordion`类样式，设计ID值，该值将为作`data-parent`属性的引用，以确保当前折叠插件中只有一个选项能够打开。在折叠外包含框内设计3个子容器，引入`accordion-group`类名。

		<div class="accordion" id="box">
		<div class="accordion-group"></div>
		<div class="accordion-group"></div>
		<div class="accordion-group"></div>
		</div>
第三步：设计折叠的选项面板。每个面板包含两份部分：第一部分是标题（`<div class="accordion-heading">`）,在该子框中呆以添加导航标题，第二部分是内容主体部分（`<div class="accordion-body">`）.一个完整的折叠单元如下：

		<div class="accordion-group">
		<div class="accordion-heading"><a class="accordion-toggle" href="#">财经/生活</a></div>
		<div class="accordion-body collapse in">
		<div class="accordion-inner"><img src="images/1.png"></div>
		</div>
第四步：为了把标题和内容框捆绑在一起，可以通过锚链接的方法,把`<div class="accordion-heading">`和`<div class="accordion-body collapse in">`连在一起，如下：

		<div class="accordion-group">
		<div class="accordion-heading"><a class="accordion-toggle" href="#1">财经/生活</a></div>
		<div id = "1"  class="accordion-body collapse in">
		<div class="accordion-inner"><img src="images/1.png"></div>
		</div>
第5步：激活折叠交互行为。为标题区块的超链接定义`data-toggle="collapse"`激活折叠交互行为。同时，通过定义`data-parent="#box"`属性，设置折叠的包含框，以便在该框内只能够显示一个单元项目。
`<div class="accordion-heading"><a class="accordion-toggle" href="#1"  data-parent="#box">财经/生活</a></div>`
第6步：完成折叠整个结构的设计，完整代码如下：

		<div class="accordion" id="box">
		    <div class="accordion-group">
		        <div class="accordion-heading"><a class="accordion-toggle" data-toggle="collapse" data-parent="#box" href="#1">财经/生活</a> </div>
		        <div id="1" class="accordion-body collapse in">
		            <div class="accordion-inner"><img src="images/1.png"></div>
		        </div>
		    </div>
		    <div class="accordion-group">
		        <div class="accordion-heading"><a class="accordion-toggle" data-toggle="collapse" data-parent="#box" href="#2">搞笑/广告</a> </div>
		        <div id="2" class="accordion-body collapse">
		            <div class="accordion-inner"><img src="images/2.png"></div>
		        </div>
		    </div>
		    <div class="accordion-group">
		        <div class="accordion-heading"><a class="accordion-toggle" data-toggle="collapse" data-parent="#box" href="#3">特色/旅游</a> </div>
		        <div id="3" class="accordion-body collapse">
		            <div class="accordion-inner"><img src="images/3.png"></div>
		        </div>
		    </div>    
		</div>
Bootstrap的折叠只能以垂直形式出现，利用`data-toggle="collapse"`属性可以设计折叠面板。如为按钮定义了`data-toggle="collapse"`属性，同时使用`data-target="#box"`属性把当前按钮与一个面板捆绑在一起，当单击按钮时，能够自动隐藏或显示面板。

		<a class="btn btn-primary" data-toggle="collapse" data-target="#box">折叠面板</a>
		<div id="box" class="in">
		    <div ><img src="images/1.png"></div>
		</div>

js调用方法`$(".collapse").collapse();`
`collapse()`方法可以包含一个配置对象，该对象包含以下两份个配置参数：
`parent:`设置折叠包含框，类型为选择器，默认false,如果指定的父元素下包含多个折叠项目，则在同一时刻只能够显示一个项目，
`toggle:` 是否切换可折叠元素调用，布尔值，默认为true.

### 轮播
轮播插件`bootstrap-carousel.js`
Bootstrap提供的轮播插件比较简单，只能自左到右，而且与其他组件不一样，需要自己手动初始化一下。
第1步，设计轮播包含框，定义`carousel`类样式，设计ID值，该值作为`data-parent`属性的引用，以确保当前轮播插件中各种控制图标和按钮进行定位。在轮播外包含框内设计两份个子容器，用来设计轮播图标和轮播信息框
第2步：设计轮播包含框，定义`carousel`类样式，设计ID值，该值将作为`data-parent`属性的引用，以确保当前轮播插件中各种控制图标和按钮进行定位。在轮播外包含框内设计两个子容器，用来设计轮播标识图标框和轮播信息框，

		<div id="box" class="carousel slide">
		<ol class="carousel-indicators">
		<li data-target="#box" data-slide-to="0" class="active"></li>
		<li data-target="#box" data-slide-to="1"></li>
		<li data-target="#box" data-slide-to="2"></li>
		</ol>
		<div class="carousel-inner"></div>
		<a class="left carousel-control" href="#box" data-slide="prev" >&lsaquo;</a>
		<a class="right carousel-control" href="#box" data-slide="next">&rsaquo;</a>
		</div>

`<ol class="carousel-indicators">`包含框定义了3个指示图标，显示当前图片的播放顺序，在这个列表结构中，使用`data-target="#box"`指定目标包含容器为`<div id="box">`,使用`data-slide-to="0"`定义播放顺序的下标。
`<div class="carousel-inner'>`包含框准备放置要轮播的图片和说明文字。在`<div id="box">`轮播框最后插入两个控制按钮，使用`carousel-control`定义按钮样式，left和right定义按钮靠齐位置，使用data-slide定义按钮控制的行为方式。
第三步：设计轮播的选项面板。每个轮播项目都包含在`<div class="item">`子框中，每个项目包含两部分：第一部分是图片，第二部分是图片描述（`<div class="carousel-caption">`）.一个完整的轮播项目如下：

		<div class="item">
		<img src="images/1.jpg" alt="">
		<div calss="carousel-caption">
		<h4>标题</h4>
		<p>描述文本</p>
		</div>
		</div>
第4步：整个轮播代码：

		<div id="box" class="carousel slide">
		    <ol class="carousel-indicators">
		        <li data-target="#box" data-slide-to="0" class="active"></li>
		        <li data-target="#box" data-slide-to="1"></li>
		        <li data-target="#box" data-slide-to="2"></li>
		    </ol>
		    <div class="carousel-inner">
		        <div class="item active">
		        	<img src="images/1.jpg" alt="">
		            <div class="carousel-caption">
		                <h4>宇宙</h4>
		                <p>宇宙（Universe）是由空间、时间、物质和能量，所构成的统一体。是一切空间和时间的综合。一般理解的宇宙指我们所存在的一个时空连续系统，包括其间的所有物质、能量和事件。宇宙根据大爆炸宇宙模型推算，宇宙年龄大约138.2亿年。</p>
		            </div>
		        </div>
		        <div class="item">
		        	<img src="images/2.jpg" alt="">
		            <div class="carousel-caption">
		                <h4>图片标题2</h4>
		                <p>描述文本2</p>
		            </div>
		        </div>
		        <div class="item">
		        	<img src="images/3.jpg" alt="">
		            <div class="carousel-caption">
		                <h4>图片标题3</h4>
		                <p>描述文本3</p>
		            </div>
		        </div>
		    </div>
		    <a class="left carousel-control" href="#box" data-slide="prev">&lsaquo;</a> 
		    <a class="right carousel-control" href="#box" data-slide="next">&rsaquo;</a> 
		</div>

javascript调用轮播很简单，只需要在脚本中调用`carousel()`方法即可。

		<script type="text/JavaScript">
		$(function(){
		$(".carousel").carousel('cycle');
		})
`carousel()`方法包含两个配置参数，

		interval  类型数字，默认5000 在自动轮播过程中，展示每帧所停留的时间。如果是false,轮播不会自动启动。
		pause 类型string 默认值 'hover' 当鼠标在轮播区域内暂停循环，在区域外时则继续循环。
		实际上，当我们配置carousel()方法的配置参数之后，轮播插件就能够自动播放动画，因此也可以不用再次调用$(".carousel").carousel('cycle')方法
		.carousel()方法还包含多种特殊调用，简单说：
		.carousel('cycle'):从左向右循环播放
		.carousel('pause')停止循环播放
		.carousel(number) 循环到指定帧，下标从0开始
		.carousel('prev') 返回到上一帧
		.carousel('next') 下一帧
		轮播插件中定义了两个事件，slide：当slide实例方法被调用时，该事件会被立即触发。
		slid 当切换完一帧后触发

### 提示输入
输入提示是一个表单输入辅助类型的插件，简单，易于扩展，可迅速地为表单中的文本输入框创建优雅的输入提示。需要`bootstrap-typeahead.js`文件支持。

在页面设计一个简单的文本输入框，在文本框中通过`data-provide="typeahead"`属性激活输入提示插件功能，使用`data-items="4"`属性设置每次最多提示的条数，使用`data-source`属性设置提示的数据源，以数组形式进行传递。

		<input type="text" class="span3" data-provide="typeahead" data-items="4" data-source='[
		"CSS: Cascading Style Sheets，层叠格式表",
		"CGI（Common Gateway Interface，通用网关接口）", 
		"DCD: Document Content Description for XML: XML文件内容描述",
		"DTD: Document Type Definition，文件类型定义", 
		"HTML（HyperText Markup Language，超文本标记语言）", 
		"JVM: Java Virtual Machine, Java虚拟机",
		"SGML: Standard Generalized Markup Language，标准通用标记语言 ",
		"XML: Extensible Markup Language（可扩展标记语言）",
		"XSL: Extensible Style Sheet Language（可扩展设计语言）", 
		"SMTP（Simple Mail Transfer Protocol，简单邮件传输协议）", 
		"VPN: virtual private network，虚拟局域网"
		]'>
输入提示插件其实就是其他 UI库的自动完成，只要引入JavaScript就能用了。要求目标文本域至少有两个属性：`data-provider="typeahead"` 和`data-source`. data-source是一个经过`unescapeHTML`的字符串数组，不过还是建议使用JavaSrcipt初始化后，动态更新source属性。
通过js调用`$(".typeahead").typeahead()`
`typeahead()`方法包含一个配置对象：

		source  类型array,function 默认[] 用于查询的数据源，可以是一个字符串数组或是一个函数。函数会接收两个参数，分别是输入域中的query值和process回调函数。函数可能会被同步调用，直接返回数据源，或者异步调用，通过process回调函数的唯一一个参数。
		items 类型数字，默认8，下拉菜单中显示的最大条目数
		minLenght 类型数字 默认1 触发提示所需的最小字符个数
		matcher 类型函数 该函数用于决定某个查询是否匹配某个条目。它接受唯一一个参数item,表示当前需要测试的条目。使用this.query引用当前查询字符串。如果匹配查询，就返回一个布尔值true.
		sorter 类型函数  默认值exact,match,case,sensitive,case insesitive.该函数用来排序提示项。它接受唯一一个参数item,并且其变量范围在typeahead实例内。使用this.query引用当前查询字符串。
		updater 类型函数，默认returns seleted item 此方法用于返回选中的条目。它接受唯一一个参数item,并且其变量范围在typeahead实例内。
		
		highlighter 类型function默认值highlights all defualt matches 该函数用来高亮自动完成的结果。它接受唯一一个参数item,并且变量范围在typeahead实例内。应该返回html


### 附加导航
附加导航（`affix`）就是通过为特定元素添加或移除`affix`类，实现在窗口中固定或不固定显示。当页面加载完毕时，附加导航插件会搜索页面上所有定义了`data-spy="affix"`的元素，然后找其`data-offset-top`或`data-offset-bottom`属性，即离页面顶部或底部少于多少像素就会放弃固定，即移出affix类，当滚动条滚动页面超出这个偏移距离时，目标元素就会固定在窗口中指定位置不再滚动。需要`bootstrap-affix.js`文件支持。
第一步：添加相关文件
第二步：设计一个完整的页面结构，顶部是Hero单元（`<div class=“hero-unit”>`），用来显示网页标题信息。主体部分是一个流式布局`(<div class="row-fluid">)`,左侧是一个导航菜单`<div class="span3" id="menu"> `右侧是主体信息栏<body>标签内包含代码如下：

		<div class="hero-unit">
			<h1 class="">网页标题</h1>
		    <a class="btn btn-large btn-success">更多</a>
		</div>
		<div class="row-fluid">
		    <div class="span3" id="menu" >
		        <ul class="nav nav-list "  data-spy="affix" data-offset-top="280">
		            <li><a href="#1"><i class="icon-chevron-right"></i> 列表1</a></li>
		            <li><a href="#2"><i class="icon-chevron-right"></i> 列表2</a></li>
		            <li><a href="#3"><i class="icon-chevron-right"></i> 列表3</a></li>
		            <li><a href="#4"><i class="icon-chevron-right"></i> 列表4</a></li>
		            <li><a href="#5"><i class="icon-chevron-right"></i> 列表5</a></li>
		            <li><a href="#6"><i class="icon-chevron-right"></i> 列表6</a></li>
		            <li><a href="#7"><i class="icon-chevron-right"></i> 列表7</a></li>
		            <li><a href="#8"><i class="icon-chevron-right"></i> 列表8</a></li>
		        </ul>
		    </div>
		    <div class="span9">
		        <fieldset id="1">
		            <legend>列表1</legend>
		            <div class="fieldset-content">
		                <p><img src="images/1.jpg"></p>
		            </div>
		        </fieldset>
		        <fieldset id="2">
		            <legend>列表2</legend>
		            <div class="fieldset-content">
		                <p> <img src="images/2.jpg"></p>
		            </div>
		        </fieldset>
		        <fieldset id="3">
		            <legend>列表3</legend>
		            <div class="fieldset-content">
		                <p><img src="images/3.jpg"></p>
		            </div>
		        </fieldset>
		        <fieldset id="4">
		            <legend>列表4</legend>
		            <div class="fieldset-content">
		                <p><img src="images/4.jpg"></p>
		            </div>
		        </fieldset>
		        <fieldset id="5">
		            <legend>列表5</legend>
		            <div class="fieldset-content">
		                <p><img src="images/5.jpg"></p>
		            </div>
		        </fieldset>
		        <fieldset id="6">
		            <legend>列表6</legend>
		            <div class="fieldset-content">
		                <p><img src="images/6.jpg"></p>
		            </div>
		        </fieldset>
		        <fieldset id="7">
		            <legend>列表7</legend>
		            <div class="fieldset-content">
		                <p><img src="images/7.jpg"></p>
		            </div>
		        </fieldset>
		        <fieldset id="8">
		            <legend>列表8</legend>
		            <div class="fieldset-content">
		                <p><img src="images/8.jpg"></p>
		            </div>
		        </fieldset>        
		    </div>
		</div>
第三步：设计滚动监听，在`<body>`标签上定义`data-spy="scroll"`属性，启动滚动监听插件，同时定义`data-target="#menu"`属性，设置监听对象为附加导航列表框
`<body data-spy="scroll" data-target="#menu">`
第四步：设计附加导航。附加导航是一种导航智能定位插件，它能够根据滚动条的位置，确实是执行定位显示，还是随文档流自然流动显示。在导航列框上（`<ul class="nav nav-list">`）定义`data-spy="affix"`,启动附加导航插件，同时设置`data-offset-top="280"`属性，设置智能监控条件为距离顶部偏移位置为280像素，即当流动条向下滚动，导航列表与顶部偏移距离开始大于280像素时，导航列表被固定在窗口左上角位置显示，否则允许随文档自由滚动。
`<ul class="nav nav-list" data-spy="affix" data-offset-top="280">`
第5步：在页面自定义样式表中定义附加导航距离顶部位置为20px,作为固定位置显示的精确值

		.nav-list{top:20px}
		
		通过JS调用affix()方法：
		<script type="text/javaScript">
		$(function(){
		$(".nav-list").affix({
		offset:{top:280}
		})
		})

配置颜色选择器使用`Colorpicker for bootstrap`插件
日期选择器   `Datepicker for Bootstrap`


## jQuery UI
jQuery UI Bootstrap是一个简洁而优美的jQuery UI主题，它受到Bootstrap的启发，将其特性引入jQuery UI组件中。jQuery UI Bootstrap提供了漂亮精致的网页排版方式以及导航，表单，按钮等网页设计中常用的元素，并且符合HTML和css标准规范。用户可以在这个主题中使用Bootstrap的全部组件，且完美兼容Twitter Bootstrap.
## BootStrap 快速开发社区分享网站
1. 定制Bootstrap
第一步定制网页配色系统。每个网站都有自己的色彩风格，这在设计之初就应该基本确定。几个基本颜色：
网页背景色，网页前景色（字段颜色），超链接颜色，鼠标经过颜色。
第二步定制栅格系统。 Bootstrap默认栅格系统：网页宽度940px,12格，每格宽60像素，栅格间隔20px
第三步：除了页面基本色彩问题，栅格系统，也可以根据需要定制各种组件的样式或者标签基本样式。
2. 首页设计
在全面考虑好网站的栏目，结构，风格和配色等基本问题之后，就可以动手制作首页了。在网站开发中，首页设计和制作将占整个制作时间的40%。
如何设计好首页：
	1. 确定首页的功能模块。首页上实现的主要内容和功能。一般站点需要这样一些模块：网站名称（logo）,广告条（banner）,主菜单(menu),新闻（news）,搜索（search）,链接(links),版权（copyright）等。另外还有灯箱广告，宣传性新闻，链接(links)和版权（copyright）.
	2. 设计首页的版面。 在功能模块确定后，开始设计首页的版面。就像搭积木一样，每个模块是一个单位积木，如何拼搭出一座漂亮的房子，就需要创意和想象力了。设计版面的最好方法就是：找一张白纸，一支笔，先将理想中的草图勾勒出来，然后再用网页制作软件实现。
	3. 处理技术上的细节。完成页面的规划，设计工作后，后期的制作过程就是一些具体的技术活了。
  借助CSS背景定位技术可以把很多小图片合成到一张图片上。

## Bootstrap源码分析
jQuery允许开发人员自定义jQuery扩展功能，并提供了友好的接口。Bootstrap框架也是jQuery的一个扩展插件，因此也应该遵循jQuery插件的基本设计原则，并保持相同用法。

### jQuery插件形式：
开发jQuery插件包括三种形式：
1. jQuery方法   这种形式的插件是把一些常用或者重复使用的功能定义为方法，然后绑定到jQuery对象上，从而成为jQuery对象的一个扩展方法。对jQuery包装集进行操作的方法即所谓的jQuery命令。
大部分jQuery插件都是这种形式的插件，这种插件是将对象方法封装起来，用于对通过jQuery选择器获取的jQuery对象进行操作，从而发挥jQuery强大的选择器优势。有很多jQuery内部方法也是通过jQuery脚本内部通过这种形式插入到jQuery框架中，如parent(),appendTo(),addClass()等方法。
Bootstrap插件都是这种形式的插件，在使用时可以直接在jQuery对象上调用bootstrap方法
2. 工具函数
在$(jQuery的别名)上直接定义实用工具函数，把自定义的工具函数独立附加到jQuery命名空间下，从而作为jQuery作用域下的公共函数使用。
如jQuery的ajax()方法就是利用这种途经内部定义的全局函数。由于全局函数没有绑定到jQuery对象上，故不能够在选择器获取的jQuery对象上直接调用，需要通过jQuery.fn()或$.fn()方法进行引用
3. 自定义选择器
jQuery提供了强大的选择器，当然用户也可以自定义选择器，以满足特定环境下选择元素的需要。

jQuery插件规范：
	1. 命名规则    jquery.  plug-in_name.js  其中plug-in_name表示插件的名称，在这个文件中，所有全局函数都应该包含在名为plug-in_name的对象中。如果插件只有一个函数，则可以考虑使用jQuery.plug-in_name()形式。
  插件中的对象方法可以灵活命名，但是应保持相同的命名风格。如果定义多个应运，建议在方法名前添加插件名前缀，以保持清晰。不建议使用过于简短的名称，语义含糊的缩写名，或公共方法名，如set(),get()等，这样容易与外界的方法混淆。
Bootstrap插件由于是在Bootstrap框架下进行定义，因而在插件命名时，没有严格遵循jQuery插件命名规则，而是遵循自己的一套规则，以bootstrap为前缀，后面附加功能名称，如bootstrap-affix.js,bootstrap-alert.js等
### 编码规则 
1. 所有新方法都附加到jQuery.fn对象上
2. 所有新功能都附加到jQuery对象上
3. this指代
jQuery插件内的this应该引用jQuery对象
让所有插件在引用this时，知道从jQuery接收到哪个对象。所有jQuery方法都是在一个jQuery对象的环境中调用的，因此函数体中this关键字总是指向该函数的上下文，即this此时是一个包含多个DOM元素的伪数组（Object对象）
但是，在插件函数的内部方法中，this不再指代当前jQuery对象，而是jQuery对象中包含的每一个DOM元素。如，在Bootstrap插件bootstrap-alert.js中，外层的this指代匹配所有DOM元素的jQuery对象，而在each()函数内，this指代的是每个匹配的DOM元素，因此在使用时还必须把this包装为jQuery对象（var $this=$(this)）才能够正确使用。
4. 迭代元素
 使用this.each()迭代匹配的元素。    插件应该调用this.each()来迭代所有匹配的元素，然后依次操作每个DOM元素。在this.each()方法体内，this就不再引用jQuery对象，而是引用当前匹配的DOM元素对象
5. 返回jQuery对象
 插件应该有返回值，除了特定需求外，所有方法都必须返回jQuery对象。一般都应该返回当前上下文环境中的jQuery对象，即this关键字引用的数组。通过这种方式，可以保持jQuery框架内方法的连续行为，即链式语法。如果匹配的对象集合被修改，则应该通过调用pushStack()方法创建新的jQuery对象，并返回这个新对象，如果返回值不是jQuery对象，则应该明确说明。
6. 语法严谨
插件中定义的所有方法或函数，末尾都必须加上分号,以方便代码压缩。
7. 区别jQuery和$
在插件代码中总是使用“jQuery”而不是"$"
$并不总是等于jQuery，这一点很重要。对于 `var JQ= jQuery.noConflict()`函数，如果将jQuery替换为$别名，那么就会引发错误。另外，其他JavaScript框架也可能使用$别名。 在复杂的插件中，如果全部使用"jQuery"代替"$",又会让人难以接受这种复杂的写法，为了解决这个问题，建议使用如下插件模式：

		(function($){
		//在插件包中使用$代替jQuery
		})(jQuery);
这个包装函数接受一个参数，该参数传递的是jQuery全局对象，由于参数被命名为$,因此在函数体内就可以安全使用$别名，而不用担心命名冲突。

jQuery插件封装
封装jQuery插件第一步是定义一个独立域，代码如下：

		(function($){
		//自定义插件代码
		})(jQuery);   //封装插件
确定创建插件类型，选择创建方式。如，创建一个设置元素字体颜色的插件，则应该创建jQuery对象方法。考虑到jQuery提供了插件扩展方法extend()，调用该方法定义插件会更为规范。

		(function($){
		$.extend($.fn,{
		//函数列表
		})
		})(jQuery);//封装插件
一般插件都会接受参数，用来控制插件的行为。根据jQuery设计习惯，可以把所有参数以列表形式封装在选项对象中。如，设置元素字体颜色的插件，应该允许用户设置字体颜色，同时还要考虑如果用户没有设置颜色，则应确保使用默认色进行设置。代码如下：

		(function ($){
		$.extend($.fn,{
		color: function(options){        //自定义插件名称
		var options = $.extend({   //参数选项对象处理
		bcolor: "white",       //背景色默认值 
		fcolor: "black"    //前景色默认值 
		},options);
		return this.each(function(){       //返回匹配的jQuery对象
		$(this).css("color",options.fcolor);    //遍历设置每个DOM元素字体颜色
		$(this).css("backgroundColor",options.bcolor);
		})
		}
		})
		})(jQuery);


如果插件包含很多函数，那么要想在设计时希望这么多函数不搅乱命名空间，也不会被完全暴露 ，唯一的方法就是使用闭包。为了创建闭包，可以将整个插件封装在一个函数中即可。


### Bootstrap设计思想
1. 类型化  
类型化设计是样式优化最有效的途径之一。Bootstrap主要使用类（Class）选择器定义样式库，适当通过标签选择器进行样式重设，以及类型个性化设计的限制。
在Bootstrap.css文档中，用户会发现样式类与标签样式构成了全部样式，但是Bootstrap并没有大量重置标签默认样式，仅对个别浏览器解析存在差异的标签进行样式统一，同时对于一些标签缺陷样式进行修补，以实现标准化视觉设计要求。
大量地使用样式类，尽量避免破坏标签默认样式，这是Bootstrap的设计准则。
 下面几点是Bootstrap在设计时遵循的基本原则：
	1. CSS的类应该体现最小化效果设计原则。这样就能够更灵活地应用样式类。所谓最小化效果单元就是一个样式类中，如果几个声明被分开之后，没有被重复利用的价值，就不应该再分开定义。
	2. CSS类型体现通用性。所谓通用性是指具备广泛的应用价值。定义类时除了上面讲的应该尽可能定义小的样式单元，还应该保证所定义的类具有代表性。
	3. 当定义CSS类库时，要遵循规律，如在命名样式类时要有规律，这样在使用和参阅时也可以快速浏览。如，有关按钮样式类的定义中，通过btn前辍，把所有与按钮相关的样式统一起来，这样能够方便使用，也方便管理。

CSS所定义的100多种属性中只有一部分具有继承性，而不是全部都具有继承性。具体来讲，拥有继承性的属性包括如下几大类：
	1. 字体属性
	2. 文本属性（个别属性不支持继承）
	3. 表格属性（个别属性不支持继承）
	4. 列表属性
	5. 打印属性 （部分属性支持继承）
	6. 声音属性（部分属性支持继承）
	7. 另外鼠标样式也具有继承性
而盒模型，布局，定位，背景，轮廓和内容等类属性都不具备继承性。

CSS继承的结构性主要体现在内部结构会自动继承外部结构的可继承属性。因此，当希望统一整个CSS的字体，字号，字体颜色，行高等基本样式时，不妨在html和body元素中进行定义，然后通过继承性实现网页字体样式的统一。

### Bootstrap在设计思路上遵循的步骤
首先，使用CSS继承性来统一CSS样式表中的基本样式。
然后，对于特定对象，组件所需要的特殊样式，可以通过重定义的方法来修正继承所带来的问题
最后，如果某种特殊样式使用比较普遍，通过定义特殊类的方式实现修正，如果这种特殊样式使用不是很普遍，则直接合并到组件样式中，避免类的泛滥。

