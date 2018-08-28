### 缓存变量
DOM遍历是昂贵的，所以尽量将会重用的元素缓存。
```javascript
// 糟糕

h = $('#element').height();
$('#element').css('height',h-20);

// 建议

$element = $('#element');
h = $element.height();
$element.css('height',h-20);
```
### 避免全局变量
jQuery与javascript一样，一般来说,最好确保你的变量在函数作用域内。
```javascript
// 糟糕

$element = $('#element');
h = $element.height();
$element.css('height',h-20);

// 建议

var $element = $('#element');
var h = $element.height();
$element.css('height',h-20);
```
### 使用匈牙利命名法
在变量前加$前缀，便于识别出jQuery对象。
```javascript
// 糟糕

var first = $('#first');
var second = $('#second');
var value = $first.val();

// 建议 - 在jQuery对象前加$前缀

var $first = $('#first');
var $second = $('#second'),
var value = $first.val();
```
### 使用 Var 链（单 Var 模式）
将多条var语句合并为一条语句，我建议将未赋值的变量放到后面。
```javascript
var
  $first = $('#first'),
  $second = $('#second'),
  value = $first.val(),
  k = 3,
  cookiestring = 'SOMECOOKIESPLEASE',
  i,
  j,
  myArray = {};
```
### 请使用’On’
在新版jQuery中，更短的 on(“click”) 用来取代类似 click() 这样的函数。在之前的版本中 on() 就是 bind()。自从jQuery 1.7版本后，on() 附加事件处理程序的首选方法。然而，出于一致性考虑，你可以简单的全部使用 on()方法。
```javascript
// 糟糕

$first.click(function(){
    $first.css('border','1px solid red');
    $first.css('color','blue');
});

$first.hover(function(){
    $first.css('border','1px solid red');
})

// 建议
$first.on('click',function(){
    $first.css('border','1px solid red');
    $first.css('color','blue');
})

$first.on('hover',function(){
    $first.css('border','1px solid red');
})
```
### 精简javascript
一般来说,最好尽可能合并函数。
```javascript
// 糟糕

$first.click(function(){
    $first.css('border','1px solid red');
    $first.css('color','blue');
});

// 建议

$first.on('click',function(){
    $first.css({
        'border':'1px solid red',
        'color':'blue'
    });
});
```
### 链式操作
jQuery实现方法的链式操作是非常容易的。下面利用这一点。
```javascript
// 糟糕

$second.html(value);
$second.on('click',function(){
    alert('hello everybody');
});
$second.fadeIn('slow');
$second.animate({height:'120px'},500);

// 建议

$second.html(value);
$second.on('click',function(){
    alert('hello everybody');
}).fadeIn('slow').animate({height:'120px'},500);
```
### 维持代码的可读性
伴随着精简代码和使用链式的同时，可能带来代码的难以阅读。添加缩紧和换行能起到很好的效果。
```javascript
// 糟糕

$second.html(value);
$second.on('click',function(){
    alert('hello everybody');
}).fadeIn('slow').animate({height:'120px'},500);

// 建议

$second.html(value);
$second
    .on('click',function(){ alert('hello everybody');})
    .fadeIn('slow')
    .animate({height:'120px'},500);
```
### 选择短路求值
短路求值是一个从左到右求值的表达式，用 &&（逻辑与）或 ||（逻辑或）操作符。
```javascript
// 糟糕

function initVar($myVar) {
    if(!$myVar) {
        $myVar = $('#selector');
    }
}

// 建议

function initVar($myVar) {
    $myVar = $myVar || $('#selector');
}
```
### 选择捷径
精简代码的其中一种方式是利用编码捷径。
```javascript
// 糟糕

if(collection.length > 0){..}

// 建议

if(collection.length){..}
```
### 繁重的操作中分离元素
如果你打算对DOM元素做大量操作（连续设置多个属性或css样式），建议首先分离元素然后在添加。
```javascript
// 糟糕

var
    $container = $("#container"),
    $containerLi = $("#container li"),
    $element = null;

$element = $containerLi.first();
//... 许多复杂的操作

// better

var
    $container = $("#container"),
    $containerLi = $container.find("li"),
    $element = null;

$element = $containerLi.first().detach();
//... 许多复杂的操作

$container.append($element);
```
### 熟记技巧
你可能对使用jQuery中的方法缺少经验,一定要查看的文档，可能会有一个更好或更快的方法来使用它。
```javascript
// 糟糕

$('#id').data(key,value);

// 建议 (高效)

$.data('#id',key,value);
```
### 使用子查询缓存的父元素
正如前面所提到的，DOM遍历是一项昂贵的操作。典型做法是缓存父元素并在选择子元素时重用这些缓存元素。
```javascript
// 糟糕

var
    $container = $('#container'),
    $containerLi = $('#container li'),
    $containerLiSpan = $('#container li span');

// 建议 (高效)

var
    $container = $('#container '),
    $containerLi = $container.find('li'),
    $containerLiSpan= $containerLi.find('span');
```
### 避免通用选择符
将通用选择符放到后代选择符中，性能非常糟糕。
```javascript
// 糟糕

$('.container > *');

// 建议

$('.container').children();
```
### 避免隐式通用选择符
通用选择符有时是隐式的，不容易发现。
```javascript
// 糟糕

$('.someclass :radio');

// 建议

$('.someclass input:radio');
```
### 优化选择符
例如，Id选择符应该是唯一的，所以没有必要添加额外的选择符。
```javascript
// 糟糕

$('div#myid');
$('div#footer a.myLink');

// 建议
$('#myid');
$('#footer .myLink');
```
### 避免多个ID选择符
在此强调，ID 选择符应该是唯一的，不需要添加额外的选择符，更不需要多个后代ID选择符。
```javascript
// 糟糕

$('#outer #inner');

// 建议

$('#inner');
```
### 坚持最新版本
新版本通常更好：更轻量级，更高效。显然，你需要考虑你要支持的代码的兼容性。例如，2.0版本不支持ie 6/7/8。

### 摒弃弃用方法
关注每个新版本的废弃方法是非常重要的并尽量避免使用这些方法。
```javascript
// 糟糕 - live 已经废弃

$('#stuff').live('click', function() {
  console.log('hooray');
});

// 建议
$('#stuff').on('click', function() {
  console.log('hooray');
});
// 注：此处可能不当，应为live能实现实时绑定，delegate或许更合适
```
### 利用CDN

谷歌的CND能保证选择离用户最近的缓存并迅速响应。（使用谷歌CND请自行搜索地址，此处地址以不能使用，推荐jquery官网提供的CDN）。

### 必要时组合jQuery和javascript原生代码

如上所述，jQuery就是javascript，这意味着用jQuery能做的事情，同样可以用原生代码来做。原生代码（或vanilla）的可读性和可维护性可能不如jQuery，而且代码更长。但也意味着更高效（通常更接近底层代码可读性越差，性能越高，例如：汇编，当然需要更强大的人才可以）。牢记没有任何框架能比原生代码更小，更轻，更高效（注：测试链接已失效，可上网搜索测试代码）。

鉴于vanilla 和 jQuery之间的性能差异，我强烈建议吸收两人的精华，使用（可能的话）和jQuery等价的原生代码。

> via: http://www.codeceo.com/article/jquery-coding-high-qulity.html

----

### 简易jQuery输入框模糊查询匹配select

```javascript
$("#search").keyup(function(){
    //过滤空
    var keyword = $(this).val().toLowerCase().replace(/(^\s*)|(\s*$)/g, "");
    if (keyword) {
        //使用正则
        var reg = new RegExp(keyword,'i');
        //遍历option
        $('option').each(function(key,val){
            //定义要搜索的字符串
            var ref = $(this).attr('ref');
            //如果搜到，设置select值
            if(ref && reg.test( ref ) ){
                $("select").val($(val).val());
                return false; 
            }
        });//end each
    }//end if keyword
});
```    

----

### 解决iframe自适应高度
下面的两种方法自选其一就行了。一个是放在和iframe同页面的，一个是放在test.html页面的。
注意别放错了地方。
iframe的代码中，注意要写ID，没有ID查找不到

```html
<iframe src="test.html" id="main" width="700" height="300" frameborder="0" scrolling="auto"></iframe>
```

方法一：
//注意：下面的代码是放在test.html调用

```javascript
$(window.parent.document).find("#main").load(function(){
var main = $(window.parent.document).find("#main");
var thisheight = $(document).height()+30;
main.height(thisheight);
});
```

方法二：
//注意：下面的代码是放在和iframe同一个页面调用

```javascript
$("#main").load(function(){
var mainheight = $(this).contents().find("body").height()+30;
$(this).height(mainheight);
}); 
```

###  jQuery全选/反选功能实例
```javascript
            $("#CheckAll").click(function(){
                $("[name=items]:checkbox").attr("checked", true);
            })
            $("#CheckNo").click(function(){
                $("[name=items]:checkbox").attr("checked", false);
            })
            $("#CheckRev").click(function(){
                $("[name=items]:checkbox").each(function(){
                    //$(this).attr("checked", !$(this).attr("checked"));
                    this.checked = !this.checked;
                })
            })
            $("#send").click(function(){
                var str="你选中的是：\r\n";
                $("[name=items]:checkbox:checked").each(function(){
                    str += $(this).val()+"\r\n";
                })
                alert(str);
            })
```

---

### 禁止右键
```javascript
$(document).ready(function() {
    //catch the right-click context menu
    $(document).bind("contextmenu",function(e) {                
        //warning prompt - optional
        alert("No right-clicking!");
 
        //delete the default context menu
        return false;
    });
});
```

### 文本缩放
```javascript
$(document).ready(function() {
    //find the current font size
    var originalFontSize = $('html').css('font-size');
 
    //Increase the text size
    $(".increaseFont").click(function() {
        var currentFontSize = $('html').css('font-size');
        var currentFontSizeNumber = parseFloat(currentFontSize, 10);
 
        var newFontSize = currentFontSizeNumber*1.2;
        $('html').css('font-size', newFontSize);
        return false;
    });
 
    //Decrease the Text Size
    $(".decreaseFont").click(function() {
        var currentFontSize = $('html').css('font-size');
        var currentFontSizeNum = parseFloat(currentFontSize, 10);
 
        var newFontSize = currentFontSizeNum*0.8;
        $('html').css('font-size', newFontSize);
        return false;
    });
 
    // Reset Font Size
    $(".resetFont").click(function(){
    $('html').css('font-size', originalFontSize);
  });
});
```

### 在新窗口打开链接
```javascript
$(document).ready(function() {
    //select all anchor tags that have http in the href
    //and apply the target=_blank
    $("a[href^='http']").attr('target','_blank');
});
```

### 样式表切换
```javascript
$(document).ready(function() {
    $("a.cssSwap").click(function() {
        //swap the link rel attribute with the value in the rel   
        $('link[rel=stylesheet]').attr('href' , $(this).attr('rel'));
    });
});
```

### 回到顶部
```javascript
$(document).ready(function() {
    //when the id="top" link is clicked
    $('#top').click(function() {
        //scoll the page back to the top
        $(document).scrollTo(0,500);
    }
});
```

### 获取鼠标的 X、Y 坐标
```javascript
$().mousemove(function(e){
    //display the x and y axis values inside the P element
    $('p').html("X Axis : " + e.pageX + " | Y Axis " + e.pageY);
});
```

### 检测当前鼠标的坐标
```javascript
$(document).ready(function() {
    $().mousemove(function(e){
    $('# MouseCoordinates ').html("X Axis Position = " + e.pageX + " and Y Axis Position = " + e.pageY);
});
```

### 预加载图片
```javascript
jQuery.preloadImagesInWebPage = function() {
    for(var ctr = 0; ctr<arguments.length; ctr++){
        jQuery("").attr("src", arguments[ctr]);
    }
}

// 调用方法：
$.preloadImages("image1.gif", "image2.gif", "image3.gif");
// 判断图片是否已加载：
$('#imageObject').attr('src', 'image1.gif').load(function() {
    alert('The image has been loaded…');
});
```

> from:(http://www.cnblogs.com/lhb25/p/useful-jquery-tips-and-tricks.html)

---



