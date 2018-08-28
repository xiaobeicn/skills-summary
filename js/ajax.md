1、原生Javascript实现监听滚动条ajax加载(GET方法)

```javascript
function scrollLoad(options){
    if(window.innerHeight+document.body.scrollTop+options.heightOffset>=document.body.scrollHeight){
        window.onscroll=null;
        var xmlhttp;
        if(window.XMLHttpRequest){
            xmlhttp=new XMLHttpRequest();
        }
        else{
            xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
        }
        xmlhttp.onreadystatechange = function(){
            if(xmlhttp.readyState==1 && options.before!=undefined)
                options.before();
            if(xmlhttp.readyState==4 && options.after!=undefined)
                options.after(xmlhttp.responseText,xmlhttp.status);
        }
        xmlhttp.open("GET",options.url,true);
        xmlhttp.send();
        if(!options.isStopped(xmlhttp.responseText,xmlhttp.status)) window.onscroll=function(){
          scrollLoad(options);
        }
        else window.onscroll=null;
    }
};
window.onscroll=function(){
    scrollLoad({
        url: "./",    //相对路径
        heightOffset: 30,   //触发加载的距离底部的距离
        after: function(responseText,status){   //ajax加载之后调用
            console.log("success in loading");
            console.log(responseText);
        },
        isStopped: function(responseText,status){   //确定何时需要停止监听，需要返回boolean类型
            if(responseText==null)
                return true;
            else return false;
        },
        before: function(){     //ajax加载之前调用
          console.log("ajax is about to run");
        }
    });
};
```

