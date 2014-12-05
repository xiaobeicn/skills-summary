### Python

#### 几种简单装饰器的实现

```Python
#一 简单使用
def dec(func):
    def _dec():
        print "before func"
        func()
        print "after func"
    return _dec

#二 装饰带有参数的函数

def dec(func):
    def _dec(a,b):
        print "bofore func(a,b)"
        func(a,b)
        print "after func(a,b)"
    return _dec

#三 装饰带有不确定参数的函数 

def dec(func):
    def _dec(*args, **kwargs):
        print "bofore func(*args, **kwargs)"
        func(*args, **kwargs)
        print "after func(*args, **kwargs)"
    return _dec

#四 装饰器本身带有参数,python web框架通常会看到类似这种@get('/test')
def dec(arg):
    def _dec(func):
        def __dec():
            print arg
            func()
        return __dec
    return _dec
```
#### for else语句,for执行完之后如果没跳出才执行else
```Python
for i in [1,2,3]:
    if i == 2:
        break
else:
    print("because break happens,i will never run")
```

