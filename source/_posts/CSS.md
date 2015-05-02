title: CSS
date: 2015/5/2 14:19:24 
updated: 2015/5/2 14:19:28 
tags:
- 前端
- CSS

categories: 前端
---
##CSS特性-继承
>网页中子元素，将继承父元素的样式（因为有些子元素本身就有默认值，所以它就覆盖了父元素中的属性） 

哪些属性会被继承?

1. 文本

		 color（颜色，a 元素除外）
		 direction（方向）
		 font（字体）
		 font-family（字体系列）
		 font-size（字体大小）
		 font-style（用于设置斜体）
		 font-variant（用于设置小型大写字母）
		 font-weight（用于设置粗体）
		 letter-spacing（字母间距）
		 line-height（行高）
		 text-align（用于设置对齐方式）
		 text-indent（用于设置首行缩进）
		 text-transform（用于修改大小写）
		 visibility（可见性）
		 white-space（用于指定如何处理空格）
		 word-spacing（字间距）
2. 列表

		 list-style（列表样式）
		 list-style-image（用于为列表指定定制的标记）
		 list-style-position（用于确定列表标记的位置）
		 list-style-type（用于设置列表的标记）
3. 表格
 
		 border-collapse（用于控制表格相邻单元格的边框是否合并为单一边框）
		 border-spacing（用于指定表格边框之间的空隙大小）
		 caption-side（用于设置表格标题的位置）
		 empty-cells（用于设置是否显示表格中的空单元格）
4. 页面设置（对于印刷物）

		 orphans（用于设置当元素内部发生分页时在页面底部需要保留的最少行数）
		 page-break-inside（用于设置元素内部的分页方式）
		 widows（用于设置当元素内部发生分页时在页面顶部需要保留的最少行数）
5. 其他

		cursor（鼠标指针）
		quotes（用于指定引号样式）

##属性值的类型
>每个css属性对于它可以接受哪些值都有不同规定

1. inherit 	对于任何属性，如果希望显式地指出该属性的值与对应元素的父元素对该属性设定的值相同，可以使用inherit值.   如假设有一个article元素，其中包含几个段落。article元素设置了一个边框。边框通常不会被继承，因此  p {border: inherit;}这条规则可以让这些段落获得相同的边框样式。
2. 预定义的值
3. 长度和百分数   
4. 纯数字， 只有极少数css属性接受不带单位的数字，其中常见的就 是line-height,z-index和opacity
5. url 如图像
6. css 颜色
7. RGB  可以通过红，绿，蓝的量来构建自己的颜色 color: rgb(89,0,127)
8. 十六进制数  color: #895632;  两位两位红绿蓝
9. 更多CSS3提供的指定颜色的方式：RGBA,HSLA和HSL
	- RGBA:  在rgb的基础上加了一个代表alpha透明度的A,  可以在红，绿，蓝数值后面加上一个用以指定透明度的0到1之间的小数   rgba(89,0,127,0.75)
	- HSL: HSL代表色相（hue）,饱和度（saturation）,和亮度（lightness）,其中色相的取值范围为0~360，饱和度和亮度均为百分数  color: hsl(280,100%,25%);
	- HSLA:  hsl的基础上加了一个透明度

##样式表的优先级别
1. 嵌入样式表（位于style元素内，在文件的一个特殊位置声明）与任何链接的外部样式表之间的关系取决于它们在html中的相对位置。在两者发生冲突时，如果link元素在HTML代码中出现的早，style元素就会覆盖链接的样式表，如果link元素出现的晚，其中的样式及其包含的任何导入样式表就会覆盖style元素的规则。
2. 内联样式（实际上直接应用于元素）。在外部样式表和嵌入样式表之后。由于顺序最靠后，其优先级是最高的。一旦应用到任何地方，都会覆盖与之冲突的其他样式
3. 关于冲突的样式的顺序对优先级的影响，有一种例外情况，就是标记！important的样式总是具有最高的优先级，无论它在任何地方。如p {margin-top: 1em !important;}.不过，要尽量避免这种做法。

##定义选择器
>选择器决定样式规则应用于哪些元素

1. 创建选择器<br>
选择器可以定义5个不同的标准来选择要进行格式化的元素：
	- 元素的类型或名称
	- 元素所在的上下文
	- 元素的类或ID
	- 元素的伪类或伪元素
	- 元素是否有某些属性和值
2. 按名称选择元素
	
		元素名 ｛
		        属性名：值
		    ｝
	`*` （星号）匹配代码中的任何元素名称。可以在一个选择器中使用一组元素名称，名称之间用逗号分隔。
3. 按类或ID选择元素

	- 类选择器   `点号类名  .类名`
	- ID选择器  `#id值` 
4. 按上下文选择元素
>在css中，可以根据元素的祖先，父元素或同胞元素来定位它们

1. 按祖先元素选择要格式化的元素（空格号）<br>
输入ancestor,  输入一个空格，输入希望格式化的选择器。如  `article p{}`  aritlce下面的p,不管隔几代继承
2. 按父元素选择要格式化的元素（>号）<br>
为父元素的直接后代定义规则。输入parent,输入>(大于号)，输入child
如  `.architect > p{}`   类为architect的元素的直接子元素中p元素
3. 按相邻同胞元素选择要格式化的元素（+号）<br>
选择相邻的同胞元素
4. 普通同胞元素选择符（~号）<br>
可以选择出现在另一同胞元素后面的同胞元素。（可以直接相邻，也可不直接相邻）
5. 选择第一个或最后一个元素<br>
使用`：first-child`和`：last-child`伪类
6. 选择元素的第一个字母或者第一行<br>
可以分别使用`：first-letter`和`：first-line`伪元素只选择元素的第一个字母或第一行
7. 按状态选择注册元素
	- `：link`  从未激活或指向，当前也没有被激活或指向的链接的外观
	- `：visited` 访问者已激活过的链接的外观
	- `：focus`  链接通过键盘选择并准备好激活的
	- `：hover` 光标指向链接时链接的外观
	- `：activie` 激活过的链接的外观
	
	由于链接可能同时处于多种状态（如同时处于激活和鼠标停留状态），且晚出现的规则会覆盖前面出现的规则，所以一般按以下顺序定义：link,visited,focus,hover,active
8. 按属性选择元素
	可以对具有给定属性或属性值的元素进行格式化。CSS提供了多种方式匹配属性和属性值，包括只检查属性名，检查全部或部分属性值。如果在选择器中忽略属性值，就可以为具有给定属性的元素应用样式，而不管具体的属性值是什么。 用方括号表示

	 ![](/images/css/属性选择参考表.png)

9. 指定元素组<br>
 用逗号分隔各选择元素

10. 组合使用选择器
css3所有伪类选择器： [http://www.w3.org/TR/css3-selectors/#selectors](http://www.w3.org/TR/css3-selectors/#selectors)

##为文本添加样式
>使用css可以修改文本的字段，大小，粗细，倾斜，行高，前景和背景颜色，间距和对齐方式，可以决定是否为文本添加下划线或删除线，可以将文本转化为全部使用大写字母，或小写字母

###设置字体
 `font-family`(虽然font-family属性是继承的，但有几个元素不会继承父元素的字体设置，其中有表单的select,textarea和input元素)。  提示使用通用的font属性可以一次性定义字体，大小和行高。

###创建斜体
 `font-style:italic/oblique`    取消斜体：`font-style:normal`
`font-style`是继承的

###应用粗体
 `font-weight:bold或100-900之间100的倍数`，400代表正常或bolder,lighter,  normal取消粗体

###字体大小
 `font-size:value`直接使用像素指定要使用的特定字号，或使用百分数，em或rem指定相对于父元素文本的大小。一般情况下，浏览器默认字体大小是16px,rem（root em）同em很相像，不过它总是以根元素为参照系。根元素是html元素。而em是相对于父元素大小为参照的。  font-size的属性是继承的. 如：`font-size:0.6rem`

###设置行高
`line-height:value`   行高指段落内每行之间的距离，数字为字体大小的倍数

###设置颜色
`color:value`    除了link元素，其他元素都会继承body元素中的color属性

###设置背景
几乎可以对所有元素应用背景样式，甚至是表单和图像，设置背景有很属性可以利用，包括`background-color`,`backgound-image`,`background-repeat`,`background-attachment`及`background-position`等。

  还可以使用background简记法，该属性将上述属性合并了。
`background-repeat`可以取四个值：`repeat`,`repeat-x`,`repeat-y`和`no-repeat`.忽略`backgound-repeat`相当于设置为repeat，从横向和纵向重复

控制背景图像是否随页面滚动`background-attachment`,值有`fixed`或`scroll`,或`local`，只有访问者滚动背景图像所在的元素（而不是整个页面）时，背景图像才移动。忽略此项，浏览器通常会使用默认值`scroll`.

`background-position:x,y`,其中x和y可以表示为距离左上角的绝对距离或百分数，也可以用left(左对齐)，center(居中)或right(右对齐)表示x, 用top(顶端对齐)，center(居中)或bottom(底端对齐)表示y.

`background-color`的默认值是`transparent`透明，`background-image`的默认值是`none`,`background-position`的默认值是0，0（等价于top,left左上角）

默认情况下，元素的背景会填充其内容和内边距所在的区域，并延伸到边框的边缘

###控制间距
指定字间距 `word-spacing:length` 这里length是一个带单位的数字
`letter-spacing:length`.要将字母间距和单词间距设为默认值，可以使用`normal`或0.（`word-spacing`和`letter-spacing`是继承的）

###添加缩进
`text-indent`可以指定段落第一行前面应该空出多大的空间。我们可以为其他元素应用 text-indent属性，而不仅是段落元素。但默认情况下对`em`,`strong`,`cite`等内联元素没有效果，可以将它们设置为`display:block`或`displacy:inline-block`就可以强制为其应用`text-indent`属性。`text-indent`属性是继承的。如果使用百分数或em值，那么只有生成的大小会被继承，而不管继承的元素的父元素的大小。

###对齐文本
`text-align :left,right,center,justify两端对齐`。text-align对于块元素是起作用的，对于行内元素则不起作用。`text-align`属性是继承的。它的默认取决于文档的语言和书写系统，大多数情况下是左对齐

###修改文本的大小写
`text-transform:capitalize`让每个单词的首字母大写，`uppercase`让所有字母大写，`lowercase`让所有字母小写，或输入none让文本本来的样子（可以用来取消继承的值）它是继承的

###使用小型大写字母
很多字体都有对应的小型大写字母变体，其中一些字母是大写的，但缩小到了小写字母大小。`font-variant`调用小型大写字母

###装饰文本
`text-decoration:underline`添加下划线，`overline`添加上划线，`line-through`添加删除线，`none`取消文本装饰

###设置空白属性
默认情况下，html文档里的多个空格和回车会显示为一个空格，或被忽略。如果要让浏览器显示这些额外的空格，可以使用`white-space`属性，`pre`让浏览器显示原文本中所有空格和回车，`nowrap`，确保所有空格不断行，也就是文本全部显示在一行。或者`normal`，按正常方式 处理空格

##用CSS进行布局
###盒子模型
![](/images/css/盒子模型.png)

>平常css属性里设置的宽度，高度都是指内容的宽度和高度，即不算边框和边距的长度。

### 控制元素的显示类型和可见性
块级元素被设置成`display:block`（对于li元素为`display:list-item`）,而行内元素被设置为`display:inline`.  在设置时有一种混合显示方式`inline-block`，让元素与其他内容出现在同一行，同时具有块级元素的属性。设置为`inline`的元素会忽略任何`width`,`height`,`margin-top`和`margin-bottom`设置，不过设置为`inline-block`的元素可以使用这些属性。所有的display类型都接受background样式。 `none`属性，隐藏元素，并将其从文档流中完全移除。

文档流：  默认情况下，元素会按照它们在HTML中自上而下出现的次序显示（这称为文档流，document flow）,并在每个非行内元素的开头和结尾处换行。

`visibility`属性可以控制元素的可见性、与display不同的是，使用`visibility`隐藏元素时，元素及其内容应该出现的位置会留下一片空白区域。隐藏元素的空白区域仍然会在文档流中占据位置。

设置为`display:inline`的元素不接受`padding`的设置，但`padding-top`和`padding-bottom`会越界进入相邻元素的区域，而不是局限于该元素本身的空间。

设置为`display：none`或`visibility:hidden`的元素里的所有内容（包括所有后代）也会受到影响。

###设置元素的高度和宽度
`width:w`   其中的w是元素内容区域的宽度，可以表示为长度（带单位，如px，em等）或父元素的百分数，或使用`auto`让浏览器计算宽度（这是默认值）,`height`类似

###在元素周围添加内边距
内边距是元素内容周围，边框以内的空间。`padding`: 4个值上右下左,使用两个值，前一个对应上下两边，后一个值对应左右两边，使用三个值，第一个对应于上边，第二值对应左右两边，第三个值对应下边。也可以输入`padding-top`, `padding-right`,`padding-bottom`,`padding-left`单独为一个边添加内边距

默认情况下，元素的width和height不包含padding的大小。内边距不是继承的

### 设置边框
边框可应用于任何元素
- 定义边框风格： `border-style: type`，其中的type可以是`none`,`dotted`(点线)，`ashed`(虚线)，`solid`(实线)，`double`(双线)，`groove`(槽线)，`ridge`(脊线)，`inset`(凹边)或（凸边）
- 设置边框宽度  `border-width`
- 设置边框颜色 `border-color`
- 可以使用简记法同时设置多个边框属性
- 输入`border`+ `-top`,`-right`,`-bottom`或`left`将边框效果限制在某一条边上，如`border: 1px solid black.   border` 简写属性及各个边框属性（border-width、border-style 和bordercolor）均可接受一至四个值。如果使用一个值，那么它会应用于全部四个边。如果使用两个值，那么前一个值会应用于上下两边， 后一个值会应用于左右两边。如果使用三个值，那么第一个值会应用于上边，第二个值 会应用于左右两边，第三个值会应用于下边。 如果使用四个值，那么它们会按照时钟顺序，依次应用于上、右、下、左四个边。

要让边框显示出来，至少必须定义边框样式。如果没有定义样式，就不会显示边框。 
边框样式的默认值是`border-style: none`。css3中引入了`border-image`属性。边框不是继承的

###设置元素周围的外边距
输入`margin: x` ，其中的x是要添加的空间量，可以表示为长度、相对父元素宽度的百分数或`auto`。 也可以输入`margin-top: x ;、margin-right: x ;、margin-bottom: x ;`或者`margin-left: x ;` 为元素的一条边应用外边距。

margin属性的`auto`值依赖于`width`属性的值.

如果元素位于另一个元素的上面，对于相互接触的两个margin（即元素相互接触的下外边距和上外边距），仅使用其中较大的一个，另一个外边距会被叠加。左右外边距不叠加。 外边距不是继承的

让网页的主体内容在浏览器里水平居 中显示是一种常见的样式，要实现这种样式其实并不难。首先，对内容的容器添加width（也可以用max-width）。然后将其左右外边距都设为auto。 这样做的话，浏览器会基于浏览器宽度和容器宽度之差计算这些外边距。

当em 值用于内边距和外边距时，它的值是相对于元素的字体大小的，而不是相对于父元素的字体大小的（你可能会这样认为）

###使元素浮动

可以通过`float` 属性使元素浮动在文本或其他元素上。可以使用这种技术让文本环绕在图像或者其他元素周围，也可以使用这种技术创建多栏布局等，可选的值有`left`,`right`,`none`, float属性不是继承的

####控制元素浮动的位置
浮动的元素对文档流的影响与非浮动的元素是不同的。图像等浮动元素会让接下来的内容环绕在它周围。不过，它并不会影响父元素或其他祖先元素的高度，从这一点来说，它不属于文档流的一部分。也就是说父元素的高度可能没有它的高度大，只有普通文档流的内容会影响父元素的高度。浮动元素完全不影响父元素的高度

清除浮动`clear`,如果对某个元素使用该属性，该元素和它后面的元素就会显示在浮动元素的下面。

####控制元素浮动位置的步骤
1. 输入clear:
2. 输入left，阻止元素浮动在该元素的左边；或输入right，阻止元素浮动在该元素的右边； 或输入both，阻止元素浮动在该元素的左右两边；或输入none（默认值），允许元素浮动 在该元素的左右两边。 
3. 如果要让浮动元素的祖先元素在高 度上包含浮动元素，并消除浮动，可以使用 
clearfix （浮动元素不会影响祖先元素的高度，如果父元素下的所有子元素都是浮动的，则这个父元素div的高度就为0，则父元素后面的元素就会顶上来形成内容重叠）或  overflow 方法替代第(1) 步和第(2) 步。

使用clearfix清除浮动的方法，在父元素上加类clearfix

	.clearfix:before,
	.clearfix:after {
	    content: " ";
	    display: table;
	}
	.clearfix:after {
	    clear: both;
	}
	.clearfix {
	    *zoom: 1;
	}

应该将clear 属性添加到不希望环绕浮 动对象的元素上。因此，如果要让一个元素在右侧没有浮动 元素（以及任何靠向右侧的元素）之后才显示，就为它添加clear: right;（而不是为浮 动的元素添加此样式规则）。而clearfix 和overflow 方法是应用于浮动元素的父元素或 祖先元素的

浮动元素的display属性会变成display:block;哪怕将其设置为display:inline,(无论是浏览器的默认样式还是手动显式设置),该属性依然是block

###对元素进行相对定位
每个元素在页面的文档流中都有一个自然位置，相对于这个原始位置进行移动就称为相对定位。周围的元素完全不受此影响。

####偏移自然流中元素的步骤
1. 输入position: relative;。
2. 输入top、right、bottom 或left。 再输入:d ，这里的d 是希望元素从它的自然位置偏移的距离，可以表示为绝对值（如 10px）、相对值（如2em）或百分数。值可负可正。需要添加其他偏移量，可重复这一步。如：
		
		.example {
		        position: relative;
		        top: 35px;
		        left: 100px;
		}

其他元素不会受到偏移的影响，仍然 按照这个元素原来的盒子进行排列（此元素原来的位置将空白）。设置了相对定位的内容可能与其他内容重叠，这取决于top、right、bottom 和left 的值。使用相对定位、绝对定位或固定定 位时，对于相互重叠的元素，可以用z-index 属性指定它们的叠放次序。 对元素设置position: static，可以 覆盖position: relative 设置。static 是元素的默认值，这就是元素出现在常规文档流中的原因

####使用overflow 创建自清除浮动元素
通常，可以对浮动元素的父元素使用 overflow 属性以替代clearfix 方法。例如，在示例页面中可以使用以下代码： 

	.container {
	overflow: hidden;
	}
在某些情况下，`overflow: hidden;` 会将内容截断，对此要多加注意。有时使用
`overflow: auto;` 也有效，但这样做可能会 出现一个滚动条，这显然是我们不希望看到的
可以将clearfix 或 overflow 应用到浮动元素的任何一个非父元素的祖先元素。这样做不会让父元素变高，但祖先元素的高度会包含浮动元素。

###对元素进行绝对定位
通过对元素进行绝对定位，即指定它们 相对于body或最近的已定位祖先元素的精确位置，可以让元素脱离正常的文档流。

这与相对定位不同，绝对定位的元素不会在原先的位置留下空白。这与让元素浮动也不同。对于绝对定位的元素，其他元素不会环绕在它的周围。事实上，其他内容不知道它的存在，它也不知道其他内容的存在。

对元素进行绝对定位的步骤
1. 输入position: absolute;。
2. 根据需要，输入top、right、bottom 或left。再输入:d ，这里的d 是希望元素相对于 
其祖先元素偏移的距离（如10px 或2em）或相对于其祖先的百分数
3. 根据需要，对其他方向重复第(2) 步。
4. 根据需要，对希望成为绝对定位参照 体的祖先元素添加position: relative;如果跳过这 
一步，将对元素相对于body 计算偏移量

还有一种定位类型称为固定定位。当访问者滚动浏览器窗口时，页面内容通常随之上下移动。如果对元素设置position: fixed;，它就会固定在浏览器窗口中。当访问者上下滚动浏览器窗口时，该元素不会随 之移动，页面的其余部分仍照常滚动。这跟固定背景图像的工作原理类似。固定定位在很多移动浏览器中效果不佳，因此如果你希望自己的网页能很好地适应移动设备，最好不要使用固定定位。

定位不是继承的

###在栈中定位元素
在栈中定位元素
输入`z-index: n` ，其中的n 是表示元素 在定位过的对象堆中的层级的数字。z-index 的值越大，元素在堆中就越高。z-index 属性仅对定位过的元素（即 设为绝对定位、相对定位或固定定位的元素）有效。对一个在文档流中的元素设置再大的都没有用。

z-index属性不是继承的

###处理溢出
元素并不总是包含在它们自己的盒子里。这可能是因为盒子不够大，例如，图像比它的容器更宽就会溢出；也可能是因为使用负的外边距或绝对定位让内容处于盒子的外面；还有可能你对元素设置了显式高度，它的内 容太高而无法装入盒子内部。无论是哪种情况，都可以使用overflow 属性控制元素在盒 子外面的部分，

决定浏览器如何处理溢出的步骤
1. 输入overflow:。
2. 输入visible，让元素盒子中的所有 内容可见，这是默认项；或者输入hidden，隐藏元素盒子容纳不了的内容；或者输入scroll，无论元素是否需要，都在元素上添加滚动条； 或者输入auto，让滚动条仅在访问者访问溢出内容时出现。
overflow 属性还可以很方便地清除float，overflow 属性不是继承的

###垂直对齐元素
可以使用除默认对齐方式以外的多种方 式对齐元素，让它们在页面上显得较为整齐

使元素垂直对齐的步骤
1. 输入vertical-align:。
2. 输入baseline，使元素的基准线对齐 父元素的基准线；或者输入middle， 使元素位于父元素中央；或者输入sub，使元素成为父元素的下标；或者输入super，使元素成为父元素的上标；或者输入text-top，使元素的顶部对齐父元素的顶部；或者输入text-bottom，使元素的底部对 齐父元素的底部；或者输入top，使元素的顶部对齐当前行 
里最高元素的顶部；或者输入bottom，使元素的底部对齐当前行里最低元素的底部； 
或者输入元素行高的百分比，可以是正 数，也可以是负数；或者输入某个值（正负均可，单位为像素或em）分别按照特定的值向上或者向下移动元素

可以使用vertical-align 设置表格单 元格中内容的对齐方式。通常，默认的样式是中间对齐，而不像表格以外的内容那样与基线对齐。除了表格以外，vertical-align 属性仅适 
用于行内元素，不能应用于块级元素

###修改鼠标指针
一般情况下，浏览器负责控制鼠标指针 的形状。大多数时候使用箭头形状，指向链 
接时使用手指形状，等等。使用CSS 可以修改指针的形状
![](/images/修改鼠标指针.png)

##构建响应式网站
###构建响应式网站基于三点
1. 灵活的图像和媒体。图像和媒体资源的尺寸是用百分数定义的，从而可以根据环境进行缩放。 
2. 灵活的、基于网格的布局，也就是流式布局。对于响应式网站， 所有的width 属性都用百分数设定，因此所有的布局成分都是相对的。其他水平属性通常也会使用相对单位（em、百分数和rem 等）
3. 媒体查询。使用这项技术，可以根据 媒体特征（如浏览器可视页面区域的宽度）对设计进行调整。

###创建可伸缩的图像
默认情况下， 图像显示的尺寸是HTML中指定的width 和height 属性值。如果不指定这些属性值，图像就会自动按照其原始尺寸 显示。此外，你还可以通过CSS 以像素为单位设置width 和height。

制作可伸缩图像的步骤:

1. 对任何想做成可伸缩图像的图像，在 HTML 的img 标签中省略width 和height 属 
性。
2. 在样式表中，为每个想做成可伸缩图像的图像应用`max-width: 100%`.可以使用`background-size` 属性对背景图像进行缩放。还可以使用`video`, `embed`, `object { max-width: 100%; }` 让HTML5 视频及其他媒体变成可伸缩的（同样也不要在HTML中为它们指定width 和height
###创建弹性布局风格

创建弹性布局的步骤
对于需要某个宽度实现预期布局的元素，设置`width: percentage`;， 其中percentage 
表示你希望元素在水平方向上占据容器空间的比例。通常说来，不必设置width: 100%;，因为默认设置为display:block; 的元素（如p 以及其他很多元素）或 手动设置为display: block; 的元素在默认情况下会占据整个可用空间。作为可选的一步，对包含整个页面内容的元素设置max-width: value;，其中value表示你希望页面最多可增长到的最大宽度。通常，value 以像素为单位，不过也可以使用百分数、em 值或其他单位值。如果父元素有水平方向上的内边距， 它为子元素建立的容器就会变小。还可以对元素设置基于百分数的 margin 和padding 值。内边距和外边距的em 值是相对于元素的,font-size 的，而基于百分数的值则是相对于包含元素的容器的。百分比都相对于父元素的
###理解和实现媒体查询
第一种方式是使用link 元素的media属性，位于head 内。

	<head>
	...
	<link rel="stylesheet" href= "your-styles.css" media="screen" /> 
	</head>
第二种方式是在样式表中使用@media 规则

	/* 只用于打印的样式 */
	@media print {
	    header[role="banner"] nav,
	    . ad {
	    display: none;
	    }
	}
还有第三种方式，即使用@import 规则，我们不讨论这种方法，因为它会影响性能

媒体查询增强了媒体类型方法，允许根据特定的设备特性定位样式。要调整网站的呈现样式，让其适应不同的屏幕尺寸，采用媒体查询特别方便。下面列出了可以包含在媒体查询里的媒体特性。
 
	 width（宽度）
	 height（高度）
	 device-width（设备宽度）
	 device-height（设备高度）
	 orientation（方向）
	 aspect-ratio（高宽比）
	 device-aspect-ratio（设备高宽比）
	 color（颜色）
	 color-index（颜色数）
	 monochrome（单色）
	 resolution（分辨率）
	 scan（扫描）
	 grid（栅格）

还有一些非标准的媒体特性，如

	 -webkit-device-pixel-rati（o WebKit①设备像素比） 
	 -moz-device-pixel-ratio（Mozilla ②设备像素比）
除了orientation、scan 和grid 以外，上述属性均可添加min- 和max- 前缀。min- 
前缀定位的是“大于等于”对应值的目标，而max- 前缀定位的则是“小于等于”对应值 
的目标。

以下是媒体 查询的基本语法。 

1. 指向外部样式表的链接：

	<link rel="stylesheet" media="logic  type and (feature: value)"  href="your-stylesheet.css" />
2. 位于样式表中的媒体查询：

	@media logic type and (feature:  value) { 
	/* 目标CSS样式规则写在这里 */
	}

例：
		<link rel="stylesheet" media="only
		 screen and (min-width: 480px)"
		 href="styles-480.css" />
		
		@media only screen and (min-width:
		 480px) {
		p {
		color: red;
		font-weight: bold;
		}
		}
回到语法，让我们看看这些代码的组成 部分。
1. logic（逻辑）部分是可选的，其值可以是only 或not。only 关键字可以确 保旧的浏览器不读取余下的媒体查询，同时一并忽略链接的样式表。not 关键字可以对媒体查询的结果求反，让其反面为真。例如，使用media="not screen" 会在媒体类型为screen 以外的任何类型时加载样式表。

2. type（类型）部分是媒体类型，如screen、print 等。 
3. feature : value 对是可选的，但一旦包含它们，它们必须用括号包围且前面要有and 这个字。feature 是预定义的媒体特性， 如min-width、maxwidth或者resolution。对color、color-index 和monochrome 特性来说，value 是可选的。

###组合使用
构建响应式Web 页面
1. 创建内容和HTML。
2. 在HTML 页面的head 元素中， 输 入<meta name="viewport" content="width=device-width" /> 或 <meta name="viewport" 
content="width= device-width, initialscale=1" />。 
3. 创建适用于所有设备的基准样式（参 见“移动优先方法”）。确保页面中的图像 
可伸缩
4. 识别出适合你的内容的断点。创建相关的媒体查询，让布局适应从小屏幕到大屏 
幕的不同可视区域宽度。（参见“逐步完善 布局”）。 
5. 如果需要为第(4) 步中页面的一些内 容指定宽度，使用百分数（参见12.3 节）。 
6. 选择你希望的旧版IE 显示页面的方式
7. 开始测试（参见“测试响应式网页”）。
8. 根据需要，修改第(3) ～ (5) 步中的 CSS，并进行测试，直到页面在各种设备下都 
呈现出预期的效果。

##使用CSS3进行增强
每个主流浏览器都有其自身的前缀：`-webkit-(webkit/safari/旧版本chrome)`,`-moz-(firefox),-ms-(Internet Explorer)`,`-o-(Opera)`.  应该将前缀放在CSS属性名的前面。

###为元素创建圆角
使用CSS3，可以在不引入额外的标记或 图像的情况下，为大多数元素（包括表单元素、图像，甚至段落文本）创建圆角

1. 为元素创建四个相同的圆角<br>
(1) 这一步可选， 输入-webkit-borderradius: r ，这里的r 是圆角的半径大小，表示为长度（带单位）。 
(2) 输入border-radius: r ，这里的r 是 圆角的半径大小，使用与第(1) 步中相同的值

2. 为元素创建一个圆角<br>
(1) 这一步可选， 输入-webkit-bordertop- left-radius: r ，这里的r 是左上方圆角的半径大小，表示为长度（带单位）。 
(2) 输入border-top-left-radius: r，这里的r 使用与第(1) 步中相同的值。这是该属性的标准长形式语法。注意这些步骤仅演示了如何创建左上方圆角，此外，还可以单独创建其他方位的圆角， 
步骤如下。
 创建右上方圆角：用 top-right 替换 第(1) 步和第(2) 步中的top-left。创建右下方圆角：用 bottom-right 替 换第(1) 步和第(2) 步中的top-left。 创建左下方圆角：用 bottom-left 替换第(1) 步和第(2) 步中的top-left。
3. 创建椭圆形圆角<br>
(1) 这一步可选， 输入-webkit-borderradius: x /y ，其中x 是圆角在水平方向上的 
半径大小，y 是圆角在垂直方向上的半径大小，均表示为长度（带单位）。 
(2) 输入border-radius: x /y ，其中x 和 y 跟第(1) 步中的值相等
4. 使用border-radius 创建圆形<br>
(1) 输入-webkit-border-radius: r ， 这 里的r 是元素的半径大小（带长度单位）。要创建圆形，可以使用短形式的语法，r 的值应该等于元素高度或宽度的一半。 
(2) 输入border-radius: r ，这里的r 是 元素的半径大小（带长度单位），跟第(1) 步 
中的r 相等

border-radius 仅影响施加该样式的元素的角，不会影响其子元素的角.
有时元素的背景（这里讲的不是子元 素的背景）会透过其圆角。为了避免这种情况， 
可以在元素的border-radius 声明后面增加一 条样式规则：background-clip: padding-box;。

###为文本添加阴影
1. 为元素的文本添加阴影<br>
(1) 输入text-shadow:。
(2) 分别输入表示x-offset（水平偏移 量）、y-offset（垂直偏移量）、blur-radius（模 
糊半径）和color 的值（前三个值带长度单位，四个值之间不用逗号分隔），例如-2px 3px 7px #999

2. 为元素的文本添加多重阴影<br>
(1) 输入text-shadow:。
(2) 分别输入x-offset、y-offset、blurradius和color 的值（前三个带长度单位，四个值之间不用逗号分隔）。blur-radius 的值是可选的。
(3) 输入,（逗号）。
(4) 对四种属性使用不同的值重复第(2) 步。

3. 将text-shadow 改回默认值<br>
输入text-shadow: none;。

text-shadow 属性接受四个值：带长度 单位的x-offset、带长度单位的y-offset、可 
选的带长度单位的blur-radius以及color值。如果不指定blur-radius， 将假定其值为0

text-shadow属性是继承的。

为其他元素添加阴影
使用text-shadow 属性可以为元素的文 本添加阴影，使用box-shadow 属性则可以为 
元素本身添加阴影，它们的基础属性集是相同的，不过 box-shadow 还允许使用两个可选的属性—— inset 关键字属性和spread 属性（用于扩张 或收缩阴影）。

box-shadow 属性接受六个值：带长度单 位的x-offset 和y-offset、可选的带长度单位的blur-radius、可选的inset 关键字、可 选的带长度单位的spread 值及color 值。如果不指定blur-radius 和spread 的值，则设为0。inset关键字可以让阴影位于元素内部。box-shadow 属性是不继承的

###应用多重背景
为单个HTML 元素指定多个背景是CSS3 引入的一个特性
1. 为单个元素应用多重背景图像<br>
(1) 输入background-color: b ， 这里的 b 是希望为元素应用的备用背景颜色
(2) 输入background-image: u ，这里的u是绝对或相对路径图像引用的URL 列表（用 
逗号分隔）。
(3) 输入background-position: p ，这里 的p 是成对的x-offset 和y-offset（可以是 
正的，也可以是负的；带长度单位或者关键 字，如center top）的集合，用逗号分隔。 
对于第(2) 步中指定的每个URL，都应有一 组x-offset 和y-offset。 
(4) 输入background-repeat: r ，这里的 r 是repeat-x、repeat-y 或no-repeat 值，用 
逗号分隔，第(2) 步中指定的每个URL 对应一个值。

###使用渐变背景
渐变背景也是CSS3 中的新特性，通过它可以在不使用图像的情况下创建从一种颜 色到另一种颜色的过渡
1. 创建备用背景颜色 输入background: color 或者backgroundcolor: color ，这里的color 可以是十六进制 数、RGB 值以及其他任何支持的颜色名称，另外也可以使用图像。最好不要将RGBA、HSL 或HSLA 值作为备用背景颜色， 因为 IE8 及以前的版本不支持。
2. 定义线性渐变<br>
(1) 输入background: linear-gradient(。
(2) 如果你希望渐变的方向是从上往下（默认方向），则跳过这一步。输入方向后面加一个逗号， 这里的方 向指的是to top、to right、to left、to bottom right、to bottom left、to top right 或to top left 这样的值。或者输入方向后面加一个逗号， 这里 的方向指的是角度值（ 如45deg、107deg、180deg 或310deg）。(3) 根据后面讲到的“指定颜色”或“指 定颜色和颜色的停止位置”，定义渐变的颜色。 
(4) 输入); 完成渐变。

3. 定义径向渐变<br>
(1) 输入background: radial-gradient(。
(2) 指定渐变的形状。如果你希望渐变的 形状根据第(3) 步中指定的尺寸自行确定，则 
跳过这一步。否则，输入circle 或ellipse。（注意这一声明在某些情况下会被忽略。） 
(3) 指定渐变的尺寸。如果你希望渐变的 尺寸为自动指定的值（默认值为arthestcorner， 
最远的角），则跳过这一步。输入size ，这里的size 可以是同时代表 渐变宽度和高度的一个长度值（如200px 或 7em），也可以是代表宽度和高度的一对值（如 390px 175px 或60% 85%）。注意，如果只使 用一个值，则这个值不能是百分数。或者输入size ，这里的size 是closestside 、farthest-side、closest-corner 或 farthest-corner。这些关键字代表相对于渐变的中心，渐变可以伸展到多大的空间。边 界决定了渐变的尺寸。 
(4) 指定渐变的位置。如果你希望渐变从元素的中心开始（默认值），则跳过这一步 
输入pos ， 这里的pos 是at top、at  right、at left、at bottom right、at bottom 
left、at top right 或at top left 等表示渐 变中心位置的值。或者输入pos ，这里的pos 是表示渐变中 心位置的一对坐标，并以at 开头。例如at 200px 43px、at 33% 70%、at 50% -10px 等。（仅指定一对。） 
(5) 如果指定了第(2) 步至第(4) 步中的任 何一步，输入一个逗号。需要说明的是，如 
果指定了这些步骤中的多个值，这些值之间 不需要以逗号分隔。 
(6) 根据后面讲到的“指定颜色”或“指 定颜色和颜色的停止位置”，定义渐变的颜色。 
(7) 输入); 完成渐变。
4. 指定颜色<br>
输入至少两种颜色，每个颜色之间用逗 号分隔。指定的第一个颜色出现在渐变的开 
始位置，最后一个颜色出现在渐变的结束位 置。对于径向渐变，它们分别为最里边的颜 
色和最外边的颜色。颜色值可以是十六进制数、RGB、RGBA、HSL 或HSLA 值的组合。除非指定一种颜色向另一种颜色过渡的位置，否则浏览器会自行决定不同颜色之间的过渡。
5. 指定颜色和颜色的停止位置根据“ 指定颜色” 中的说明进行操作。如果需要，对每个颜色值指定一个百分数以控制颜色出现在渐变中的位置

###为元素设置不透明度
使用opacity 属性可以修改元素（包括图 像）的透明度
修改元素不透明度的方法
输入`opacity: o` ，这里的o 表示元素的 不透明程度（两位小数，不带单位）。opacity 的默认值为1，该属性值可以使用0.00（完全透明）至1.00 （ 完全不透明） 之间的两位小数。opacity 影响的是整个元素（ 包括其内容）， 而 
background-color: rgba(128,0,64,.6); 这样的设置仅影响背景的透明度。opacity 属
性并不是继承的。opacity 的值小于1 的元 素的子元素也会受到影响，但这些子元素的 
opacity 值仍为1。
生成内容的效果
使用:before 和:after 伪元素可以很方便地为页面添加一些令人难以置信的设计效果。它们可以与content 属性结合使用，从而创建所谓的生成内容（generated content）。 
生成内容指的是通过CSS 创建的内容，而不是由HTML 生成的。可以 对生成内容添加背景颜色、背景图像（包括渐变）、宽度、高度、圆角等样式。

	/* 气泡下的三角形 */
	.cities:after {
	border: solid transparent;
	border-top-color: #2b2b2b;
	border-width: 15px;
	content: " "; /* 空格 */
	height: 0;
	left: 50%;
	margin-left: -15px;
	position: absolute;
	top: 99.9%;
	width: 0;
	}
##Javascript事件：
	 onblur：访问者离开先前获得焦点的元素（参见onfocus）。 
	 onchange：访问者改变元素的值或内 容。通常用于表单字段
	 onclick：访问者点击特定的区域或在 元素（如链接）获得焦点时按下回车键。 
	 ondblclick：访问者双击特定的区域。
	 onfocus：访问者选择、点击或用制表 键将焦点移至特定的元素。 
	 onkeydown：在指定的元素上，访问者 按下一个键。 
	 onkeypress：在指定的元素上，访问者按下并松开一个键。
	 onkeyup：在指定的元素上，访问者在输入后松开一个键。 
	 onload：浏览器完成页面的加载，包 括所有的外部文件（如图像、样式表、JavaScript 等）。 
	 onmousedown：在指定的元素上，访问者按下鼠标键。 
	 onmousemove：访问者移动鼠标指针。
	 onmouseout：访问者在鼠标指针停留 的特定元素上移开鼠标。 
	 onmouseover：访问者将鼠标指向元素。
	 onmouseup：访问者在点击元素后松开 鼠标键（与nmousedown 相反）。 
	 onreset：访问者点击表单的重置按钮 或在该按钮获得焦点时按下回车键。 
	 onselect：访问者选择元素中的一个 或多个字符。 
	 onsubmit：访问者点击表单的提交按 钮或在该按钮获得焦点时按下回车键。

##测试和调试网页
验证代码的步骤

1. 首先使用http://html5.validator.nu或W3C 的http://validator.w3.org 对HTML 进行检查。
2. 修复标出来的HTML错误，保存修改。需要的话，将文件再次上传到服务器，再重复第(1) 步。
3. 可以使用http://jigsaw.w3.org/css-validator/检查CSS 错误。类似地，修复你看到的错误，再重新检查页面。

##CSS属性和值--参考
![](/images/css/CSS属性和值1.png)
![](/images/css/CSS属性和值2.png)
![](/images/css/CSS属性和值3.png)
![](/images/css/CSS属性和值4.png)
![](/images/css/CSS属性和值5.png)
![](/images/css/CSS属性和值6.png)
![](/images/css/CSS属性和值7.png)
##CSS选择器和结合符--参考
![](/images/css/CSS选择器和结合符1.png)
![](/images/css/CSS选择器和结合符2.png)
![](/images/css/CSS选择器和结合符3.png)
