### 清除子元素浮动clearfix方法

```css
/*简洁版*/
.clearfix:before, .clearfix:after {
	content:"";
	display:table;
}
.clearfix:after{
	clear:both;
	overflow:hidden;
}
.clearfix{
    zoom:1;
}
/* 经典版 */
.clearfix:after {
    visibility: hidden;
    display: block;
    font-size: 0;
    content: " ";
    clear: both;
    height: 0;
}
* html .clearfix             { zoom: 1; } /* IE6 */
*:first-child+html .clearfix { zoom: 1; } /* IE7 */
注：clearfix的方法主要就是在浮动元素的父元素上加上一个clearfix class，然后这个父元素的框就会包括所有的浮动子元素。
```

### 清除子元素浮动overflow方法

```css
/* overflow:auto */
#demo2{
	overflow:auto;*zoom:1;
}
/*或 overflow:hidden */
#demo2{
	overflow:hidden;*zoom:1;
}
注：这种方法主要是对父元素设置css，所以不需要加个class，下面的inline-block方法相同，只需设置父元素的css即可
```

### 清除子元素浮动inline-block方法

```css
#demo3{
	display:inline-block;*display:inline;*zoom:1;
}
```

----

### 单行省略号纯css解决方案

```css
selector{
	-o-text-overflow: ellipsis;/*兼容opera*/
	text-overflow: ellipsis;/*这就是省略号喽*/		  
	overflow: hidden;/*设置超过的隐藏*/
	white-space: nowrap;/*设置不折行*/
	width:200px;/*设置宽度*/
}
```

----

### 解决中文版Chrome下不能显示小于12px的字体

```css
html{-webkit-text-size-adjust: none;}
```

----

### 垂直对齐

```css
.verticalcenter{
position: relative;
top: 50%;
-webkit-transform: translateY(-50%);
-o-transform: translateY(-50%);
transform: translateY(-50%);
}
```
### 根据文件格式引用不同样式

```css 
a[href^="http://"]{
padding-right: 20px;
background: url(external.gif) no-repeat center right;
}
/* emails */
a[href^="mailto:"]{
padding-right: 20px;
background: url(email.png) no-repeat center right;
}
 
/* pdfs */
a[href$=".pdf"]{
padding-right: 20px;
background: url(pdf.png) no-repeat center right;
}
````
http://jsfiddle.net/agusesetiyono/3sL1r0mw/light/

### 跨浏览器图像灰度

```css
<svg xmlns="http://www.w3.org/2000/svg">
<filter id="grayscale">
<feColorMatrix type="matrix" values="0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0.3333 0.3333 0.3333 0 0 0 0 0 1 0"></feColorMatrix>
</filter>
</svg>
```

### CSS 列表自动宽度

```css
white-space: nowrap;
```

### 显示阴影

```css
.box-shadow {
background-color: #FF8020;
width: 160px;
height: 90px;
margin-top: -45px;
margin-left: -80px;
position: absolute;
top: 50%;
left: 50%;
}
.box-shadow:after {
content: "";
width: 150px;
height: 1px;
margin-top: 88px;
margin-left: -75px;
display: block;
position: absolute;
left: 50%;
z-index: -1;
-webkit-box-shadow: 0px 0px 8px 2px #000000;
-moz-box-shadow: 0px 0px 8px 2px #000000;
box-shadow: 0px 0px 8px 2px #000000;
}
```

### 长文本封装

```css
pre {
white-space: pre-line;
word-wrap: break-word;
}
```

### 模糊文本

```css
.blurry-text {
color: transparent;
text-shadow: 0 0 5px rgba(0,0,0,0.5);
}
```

### 使用CSS制作动画省略号

```css
.loading:after {
	overflow: hidden;
	display: inline-block;
	vertical-align: bottom;
	animation: ellipsis 2s infinite;
	content: "\2026"; /* ascii code for the ellipsis character */
}
@keyframes ellipsis {
	from {
		width: 2px;
	}
	to {
		width: 15px;
	}
}
```

----

