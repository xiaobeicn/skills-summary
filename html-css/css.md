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


