title: JS浏览器编程
date: 2013/3/25 15:25:55 
updated: 2013/3/25 15:25:55
tags:
- 技术
- JS
- 前端
categories: JS
---
#浏览器编程
浏览器本身也是由很多对象组成的。如`window`对象它表示浏览器的窗体，我们已经使用了window对象的两个方法`alert()`和`prompt()`方法，还有`document`对象，用于加载浏览器中的页面本身。通常把浏览器为javascript所提供的对象的集合称为浏览器对象模型（Brow Ob Model，DOM）.浏览器对象模型是一个具有层次的对象结构，在层级的顶端是windwo对象。window对象代表了浏览器的框架以及浏览器相关的所有一切。如滚动条，导航按键等都包含在window对象中。

##window对象
它代表浏览器的框架或者浏览器的窗体，在某种程序上它也代表了浏览器本身。它包含了大量关于浏览器的属性。`window`对象是一个全局对象，实际上全局函数与全局变量都是该全局对象的属性，当声明一个全局函数或全局变量时，实际上是创建了该全局变量的一个属性。alert()实际上是window对象的一个方法，由于window对象是一个全局对象，因此使用alert()而不是window.alert()也是正确的。

window对象的某些属性本身也是一个对象，如`document`对象，`navigator`对象，`histroy`对象，`screen`和`location`对象。

其中，document代表浏览器的页面。

histroy包含用户所访问过的页面的历史记录

navigator包含了浏览器自身的相关信息

screen包含了客户端计算机显示器显示能力的信息。

location包含了浏览器所加载的当前页面的URL的详细信息。

修改浏览器状态栏信息用defaultStatus属性，window.defaultStatus = "Hello"

##history对象
history对象具有一个`length`属性，使用length属性，就能获得浏览器历史栈中页面的个数。history对象具有`back()`方法和`forward()`方法，histroy对象还具有`go()`方法，该方法接受一个参数，该参数指出以当前页面为基准，在历史栈中前进或后退几个页面。

##location对象
location对象包含了大量有关于当前页面位置的有用信息，它不仅包含了当前页面的统一资源定位器（URL）信息，还包含了提供web服务的服务器信息，连接到服务器的端口号及所用协议信息。通过location对象的`href`属性，`hostname`属性，`port`属性，`protocol`属性，就可以获得这些信息。

如果我们要导航到另一个页面，可用两种方法`location.href()`,`location.replace()`不同之处在于一个是加到历史栈的顶部一个是替换，替换的通过历史页面将返回不到当前页面。

##navigator对象
它包含了运行当前脚本的浏览器和操作系统的大量相关信息。它最常见的用途就是用于区分不同的浏览器

##screen对象
它包含了大量有关于客户端计算机显示能力的信息。具有`height`属性和`width`属性，这两个属性以像素为单位。还有`colorDepth`属性，表示显示屏颜色的色彩位数

##document对象
它具有很多与HTML文档相关联的属性，这些属性都是数组类型。其中几个重要的属性是`forms`数组，`images`数组和`links`数组。

对于每一个具有href属性的超链接标记<a>，浏览器将创建一个对应的A对象，link[]数组用于表示页面上所有A对象的集合，就像前面看到的images[]数组包含img对象一样

##事件处理
事件处理器由关键字on加上要处理的事件名组成。如click事件，onclick


##表单
我们可以直接通过表单名称访问表单对象，如`document.myFrom`来访问这个表单对象，myFrom为表单名，另外也可通过document对象的`forms[]`数组属性来访问这个表单对象
<form>标记的大部分属性都可以作为Form对象的属性进行访问，特别是，Form对象的name属性映射到<form>标记的name属性。

所有的表单元素都有`focus()`与`blur()`方法，获得焦点与失去焦点，都有`onfocus()`与`onblur()`事件

button支持`onmousedown`事件和`onmouseup`事件

##文本框
文本框还具有两个属性，size属性和maxlength属性。size属性决定文本框具有多少个字符的宽度，maxlength属性用于决定用户可以在文本框中输入多少个字符。它还具有select()方法用于选择或加亮文本框中的文本。除了常见的onfocus和onblur事件外，text对象还有`onchange,onselect,onkeydown,onkeypress`和`onkeyup`事件处理器，当用户选择文本框的值时会触发`onselect`事件，当且仅当文本框失去焦点时的值与获得焦点时的值不同时，才会触发文本框的`onchange`事件。

textarea 这还具有cols宽度属性与rows高度属性，还有一个wrap属性，用以决定当用户在文本区域中输入到一行的结束时如何处理。wrap默认值是soft,它表示用户在一行的结尾处无须输入Enter键，当输入内容超过文本域的右边界时会自动转到下一行。为了打开文本区域的自动换行功能，可以将wrap属性的值设置为soft或hard其中之一。如果要关闭回车换行把wrap设置为off即可

checkbox复选框，radio单选按钮，具有相同名字的单选按钮将组成一个单选按钮组

select标记将创建一个select对象，如果我们想将列表框定义为一个下拉列表框，只需将select标记的size设置为1.如果允许用户从列表框或下拉列表框中一次选择多个选项，只需在<select>标记的定义中添加一个multiple属性即可。

##框架frame
很多web应用程序利用框架（frame）来分隔浏览器窗口。对于一个不包含框架的页面来说，仅存在一个window对象，但对于一个包含框架集的页面来说，页面中的每一个框架都具有一个相应的window对象.
 
 如果一个框架中包含了子框架，则这些子框架所对应的window对象都是该框架的window对象的子对象
 
 每一个框架都具有一个单独的窗口，并具有一个自己的window对象。另外，使用window对象的parent属性，就可以得到框架集中所包含的任何框架集页面的window对象。通过引用window对象的parent属性，就可以访问父window对象的各种属性和方法，就像访问当前页面的window对象的各种属性和方法一样。此外，还可以访问在框架集页面中定义的各种JavaScript变量和函数。

##Top Window
有这样一种关系Top Window(fraMenu,fraMain(fraTop,fraBottom))包含关系。那么，如果在fraBottom框架页中，我们要能过代码访问fraMenu框架页的window对象，该怎么办？实际上，我们通过window.parent.parent就可以引用顶层窗口的window对象，而fraMenu框架页又是顶层窗口的一个子对象。要访问这个子对象，可以采用3种方法，这3种方法有相同的效果。1.通过顶层窗口window对象的frames[]数组属性访问，其中fraMenu框架页在frames[]数组的索引为0;window.parent.parent.frames[0].
2.也可以把fraMenu框架页的名称作为frames[]数组的索引;window.parent.parent.frames["frameMenu"]
3.还可以直接通过fraMenu框架页的名称来引用fraMenu框架页：window.parent.parent.fraMenu
  window对象的top属性将直接返回一个对框架集中最顶层框架的引用。

##window对象的open方法
window对象具有open()方法，用以打开一个新的浏览器窗口。该方法可以接收3个参数。其中第3个参数是可选的。该方法将返回一个对新打开窗口的引用。第一个参数是要在新窗口中加载的页面的URL，第二个参数是为新窗口分配的名字。该名字并不是在脚本中用以引用该窗口，而是用在某些HTML标记中作为target属性的值使用，如用在超链接或表单的target属性中。如用在超链接或表单的target属性中。如将窗口的第二个参数设置为myWindow，并在原窗口的页面上设置一个如下链接<a href="test3.html" target=myWindow>Test3.html</a>
那么单击时，test3.html页面将加载在名为myWindow的新窗口中。第三个参数为指定新窗口width属性和height属性的值

		var newWindow = window.open("test2.htm","myWindow","width=250,height=250");

为新的浏览器窗口添加特性，通过第三个参数，可以控制新窗口的特性，如新窗口的大小，新窗口在屏幕中的起始益，用户是否可以改变新窗口的大小，以及新窗口是否具有工具栏等，将相应的属性设置为yes或1时，将打开该属性，将该属性设置为no或0时，将关闭该属性，要将某个浏览器窗口的属性设置为yes打开，只需在window.open()方法的第三个参数中包含这个属性的名称即可，并不需要为属性指定属性值。

前面我们介绍过window.open()方法用以打开一个新窗口，并返回一个对新窗口的引用，我们可以访问新窗口的属性和方法，并且可以访问新窗口的document对象，如果把打开的新窗口的浏览器窗口称为源窗口，那么如何在新窗口中访问源窗口呢，在新窗口中访问源窗口的window对象，关键是使用新窗口的`opener`属性。opener属性将返回一个对打开当前新窗口的源窗口window对象的引用。可以使用如下代码将源窗口背景色修改为红色：

		window.opener.document.bgColor="red";
window.opener属性的使用方法与在框架集中使用window.parent属性和window.top属性类似。

#字符串操作
1. split()方法
此方法根据某种分割标志将一个字符串拆分为一个由子串组成的数组。拆分字符串的依据就是作为参数传入split()方法的分割标志符，分割标志符可以是一个字符，也可以是一个字符串。此方法结果返回一个数组，但最后一个元素是空字符串
2. replace方法将查找指定子串是否存在，当找到匹配的子串时，replace（）方法将把匹配的子串替换为我们所设置的指定字符串。myString.replace("may","june")
3. search（）方法用以在字符串中查找特定子串，如果子串被找到，则返回子串在字符串中的位置，否则返回-1.search()方法仅接收一个参数。即要在字符串中查找的子串。
4. match()方法与search()方法非常类似，不同之处在于，search()方法返回的是查找到子串的位置，而match()方法返回的是一个数组，该数组中的每一个元素包含一个查找到的匹配子串。

#JS正则表达式
在javascript中，正则表达式是通过`RegExp`对象来使用的，RegExp对象与String.Array类似，也是JavaScript中的内建对象。要创建一个新的RegExp对象，可以采用两种方法，最简单的办法是采用正则表达式字面值，如下面代码：
  
	`var myRegExp = /\b'|'\b/;`其中斜杠（/）用于表示正则表达式的开始和结束。
另一种方法是 `var myRegExp = new RegExp("\\b'|'\\b");`
 
正则表达式具有三个特性：
1. g表示全局匹配，而不是默认的匹配第一个后就不查找了
2. i表示忽略字母的大小写
3. m多行标记  

##正则表达式：特殊元字符

1. 文本，数字和标点符号元字符

`\d`  0-9的任何数字

`\D`  任何非数字字符

`\w`  任何单词字符，即A-Z,a-z,0-9之间的字符，以及下划线字符

`\W` 任何非单词字符

`\s` 任何空白字符，包含水平制表符，换行符，回车符，走纸符以及垂直制表符

`\S` 任何非空白字符

`.`  除了换行符（\n）之外的任何单个字符

`[...]` 匹配位于方括号之内的任何一个字符

`[^...]` 匹配除了方括号之内字符之外的任何字符

2. 重复字符
表示位于重复字符之前的项或字符需要重复匹配的次数

`{n}` 前面的项重复n次

`{n,}` 前面的项重复n次，或者n次以上

`{n,m}` 前面的项至少出现n次，最多出现m次

 `?` 前面的项出现0次或1次

 `+` 前面的项出现1次或多次，但至少出现1次

 `* `前面的项出现0次或多次

3. 位置元字 符
用以设置正则表达式是匹配字符串开始位置的子串，还是匹配结束位置的子串，或者匹配模式字符的分界线位置。 

`^ `该元字符表示匹配字符串的开始位置，或者匹配多行字符串每一行的开始位置，对于多行字符串（即包含回车符的字符串），在定义正则表达式的时候，需要设置多行标记m如/myreg ex/m

`$` 该元字符表示匹配字符串的结束位置，或者匹配多行字符串每一行的结束位置。对于多行字符串（即包含回车符的字符串），在定义正则表达式的时候，需要设置多行标记m如/myreg ex/m

`\b` 该元字符匹配单词的分界位置，本质上，单词的分界位置就是位于一个单词字符与一个非单词字符之间的位置

`\B` 该元字符匹配单词的非分界位置

4. 正则表达式分组 在正则表达式中，圆括号是用以表示对模式字符串进行分组的元字符，圆括号中的表达式又称为一个子模式或子正则表达式。子模式将作为一个整体进行匹配，而不是对子模式中的一部分进行匹配。

要引用与正则表达式分组相匹配的子串，可以使用`$`符号，后跟分组的序号。如下所示的正则表达式：var myRegExp =/(\d)(\W)/g $1表示对第一个分组（\d）的引用，而$2表示对第二个分组(\W)的引用。另外，在这个正则表达式中，还设置了全局标志g，以保证所有与模式相匹配的子串都被替换---而不仅仅是替换第一个匹配的子串。
  如想把 `var myString = "1999,2000,2001"`;替换为“the year 1999,the year 2000,the year 2001”

		 var myRegExp =/(\d{4})/g;
		myString = myString.replace(myRegExp,"the year $1");

#JS时间，日期，定时器
我们需要一种标准的时间值来表示不同地点的同一时间，这就是协调世界时（Coordinated Universal Time,UTC）.它的前身是格林尼治标准时间（Greenwich Mean Time,GMT）
 JavaScript中的Date对象提供了许多方法以支持UTC时间。如setUTCHours()方法

##一次性计时器
只需要使用window对象的`setTimeout()`方法

`window.setTimeout("your javascript code",milliseconds_delay)`第一个参数为执行的代码，第二个参数为计时器延迟的时间，以毫秒为单位，此方法返回一个数值即计时器ID要在计时器触发前停止它，可以用`window.clearTimeout()`方法，该方法仅接收一个参数，即由setTimeout()方法返回的唯一的计时器ID号
 
##间隔性计时器
可以用`setInterval()`方法和`clearInterval()`方法，与一次性计时器参数类似

#使用cookie
使用cookie的关键在于使用`document`对象的`cookie`属性，使用cookie属性，可以在JavaScript代码中创建cookie，或者访问cookie中的数据。

cookie字符串，当创建一个cookie时，需要创建一个cookie字符串，cookie字符串包含6个需要设置的子串：name(名称)，value(值)，expires(过期时间)，path(路径)、domain(域)、secure(安全性)，其中后4个子串是可先的。在cookie值字符串中，分号不能包含在值字符串中，因为在cookie字符串中将分号作为不同部分的分隔符。cookie如果不设置过期时间，则默认浏览器关闭后失效，这也就是会话cookie

#JS字符转码
escape()函数和unescape()函数对特殊字符进行编码和解码。escape()函数可以将非字母或数字字符转换为Latin-1字符集中对应的十六进制编码，并在编码之前加上一个百分号（%）字符。

要使某个元素采用绝对定位只要将该元素的position属性设置为absolute要使某个元素采用相对定位，只需将该元素的position属性设置为relative即可，用于定位 元素的CSS关键属性是left属性和top属性

#JS操作CSS
1. 使用javascript改变style对象属性形式如下：

		oHtmlElement.style.cssProperty = value;
通常情况下，某个CSS属性的名称与该属性在CSS文件中的名称是相同的,如color，但是，在某些情况下，CSS属性的名称与其在CSS文件中的名称略有不同，包含一个连字符（-)的CSS属性就是一个典型例外。对于包含一个连字符（-）的CSS属性，需要去掉连字符，并把连字符之后的第一个字母改成大写形式。使用style对象来访问元素的样式属性时，只能访问到元素的内联样式属性。对于在style标记中的样式属性，则不能通过style对象来访问

2. 修改class属性

		oHtmlElement.className = sNewClassName;
使用它来修改元素的样式具有两个好处，第一，它减少了需要编写的javaScript代码的数量，第二，可以将元素的样式设置从JavaScript代码中抽取处理，放在独立的CSS文件中。

3. 移动元素
与使用style对象来设置元素样式类似，使用js来移动元素，可以使用position属性来改变元素的定位方式（即相对定位或绝对定位），也可以使用left和top属性将元属设置到指定的位置。
 
现代浏览器中的Dom模型暴露了HTML元素的offsetTop属性和offsetLeft属性。这两个属性将返回相对于父元素的位置偏移量。offsetTop属性和offsetLeft属性的返回值都是数值类型，因此，很容易检查元素的当前位置是否位于动画的移动路线上。

#DOM

##基本的dom对象
1. Node 文档中的每一个节点都是一个Node对象
2. NodeList 一个包含多个Node对象的数组，通常用来表示一个元素所包含的子节点
3. NameNodeMap 此对象建立了节点名称和节点之间的一一映射关系，从而可以利用节点名称直接访问节点
 
##高级dom对象
节点可以分为元素节点，属性节点，或纯文本节点

`Document` 表示文档的根节点

`DocumentType` xml文档的dtd或schema的对象表现形式

`DocumentFragment`文档碎片对象，它是一个不完整的document对象，主要用于暂时存放没有加入Dom树的文档片段

`EntityReference` 表示一个xml文档中的实体引用

`Element`表示文档中的一个元素

`Attr` 表示文档中元素的一个属性

`ProcessingInstruction` 表示一个处理指令

`Comment` 表示xml文档或HTML文档中的注释

`Text` 表示构成元素子节点的纯文本

`CDATASection` xml文档中CDATA节的对象表现形式

`Entity` 表示DTD中定义的不解析实体

`Notation`体表在DTD中定义的记号

###Document对象方法
返回一个元素或元素的集合
1. getElementById(idname)返回一个对文档中指定id元素节点对象的引用
2. getElementsByTagName(tagname)返回一个对象数组，要引用集合中某一个特定的元素，还必须指定该元素在集合中的索引值，通过集合的`item()`方法，即可返回指定索引值的元素。

documentElement 返回对文档最顶层元素的引用（即根元素，如<html/>元素）

Element对象仅包含一个属性 tagName返回当前节点的标记名称

###Node对象的属性
可以在文档树中导航
1. firstChild 返回元素子节点列表（childNodes）中的第一个节点
2. lastChild 返回元素子节点列表中的最后一个节点
3. previousSibling在同级子节点中，返回当前子节点的前一个兄弟节点
4. nextSibling 在同级子节点中，返回当前子节点的的后一个兄弟节点
5. ownerDocument返回包含当前节点的文档的根节点
6. parentNode 返回树结构中当前节点的父节点
7. nodeName 返回当前元素节点的名称
8. nodeType 返回一个数字，表示当前节点的类型 文本节点3这对区别不同浏览器中使用很有用
9. nodeValue 以纯文本形式返回的当前节点的值

值得注意的是，对于Firefox,Opera和Safari浏览器，情况有所不同，这几个浏览器把HTML标记与标记之间的空白也视为文本节点，因此对于Firefox,Opera和Safari浏览器，<body/>节点之下还包含了空白文本节点

###Node对象的方法
添加和移除文档中的元素
1. appendChild(newNode)将一个新节点newNode添加到子节点列表的末尾，该方法将返回一个对新插入节点对象的引用
2. cloneNode(cloneChildren)返回当前节点的一个副本，该方法接收一个布尔值作为参数，如果该参数的值为true，则克隆当前节点及其所有的子节点，如果该参数的值为false，则仅克隆当前节点而不包含其子节点。
3. hasChildNodes()检查当前节点是否含有子节点，如果有子节点，则返回一个true值
4. insertBefore(newNode,referenceNode)在指定的referenceNode节点之前，插入一个新节点newNode，该方法将返回一个对新插入节点对象的引用
5. removeChild(childNode)从node对象的子节点列表中，删除由参数childNode指定的子节点，该方法将返回一个对所有删除节点对象的引用
6. replaceChild(newChild,oldChild) 将参数oldChild指定的子节点替换为参数newChild指定的新节点，该方法将返回一个对被替换节点对象的引用。

###document对象的方法：
添加和移除文档中的元素

1. createElement(elementName)根据由elementName参数指定的标记名称来创建一个新元素。该方法将返回一个对新创建元素的对象的引用
2. createTextNode(text)用参数text指定的文本来创建一个文本节点，并返回一个对新创建的文本节点的引用 。
3. createAttribute(attributeName)用参数attributeName指定的名称来创建一个属性，并返回一个对新创建属性节点对象的引用。

###element对象的属性方法
1. getAttribute（attributeName）返回由参数attributeName指定的属性的值
2. setArrtibute (attributeName,value)将attributeName属性的值设置为由参数value指定的值
3. remvoeAttribute(attributeName)移除attributeName属性的值，并将其恢复为默认值

###event对象的属性
1. bubbles 一个逻辑值，表示是否允许事件冒泡（即事件从一个底层元素向上传递给上一层元素）
2. cancelable 一个逻辑值，表示是否可以取消事件的默认行为
3. currentTarget 表示当前事件目前所指向的元素节点
4. eventPhase 一个表示事件流当前阶段的数字值
5. target(仅对支持Dom的浏览器) 该属性表示引发当前事件的元素对象，在Dom的事件模型中，文本节点也会触发DOM中的事件
6. timeStamp(仅对Firefox浏览器) 表示事件发生的时间
7. type 表示当前事件的名称
另外dom事件模型还引入了一个MouseEvent对象，该对象专门用以处理由鼠标操作所引发的各种事件。

###MouseEvent对象的属性
1. altKey 一个逻辑值，表示当事件发生时，Alt键是否被按下
2. button 对于鼠标事件，该属性表示鼠标的哪一个按钮被按下
3. clientX 表示当事件发生时，鼠标指针在客户端区域中的横坐标
4. clientY 表示当事件发生时，鼠标指针在客户端区域中的纵坐标
5. ctrlKey 一个逻辑值 ，表示当事件发生时ctrl键是否被按下
6. metaKey 一个逻辑值，表示当事件发生时，Meta键是否被按下
7. relatedTarget（仅对支持Dom的浏览器） 在Dom事件模型中，文本节点也可触发mouseover事件，该属性表示与事件相关的元素节点，类似于IE浏览器中的event.toElement属性和event.fromElement属性
8. screenX 表示当事件发生时，鼠标指针相对于计算机屏幕区域中的横坐标
9. screenY 表示当事件发生时，鼠标指针相对于计算机屏幕区域中的纵坐标
10. shiftKey 一个逻辑值，表示当事件发生时，Shift键是否被按下