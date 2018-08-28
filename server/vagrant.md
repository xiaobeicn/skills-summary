## 加入 laravel/homestead box

```shell
vagrant box add laravel/homestead
```
### [提示] 命令行下下载速度太慢的话可以利用工具（比如：QQ旋风下载助手）下载以下链接加速.

> https://vagrantcloud.com/laravel/homestead/version/9/provider/virtualbox.box

下载后执行以下命令, 修改 /path/to/virtualbox.box 为正确的 path.

* Linux
```shell
vagrant box add laravel/homestead /path/to/virtualbox.box 
```

* Win
```shell
vagrant box add laravel/homestead file:///F:/vagrant/box/virtualbox.box 
```

## 修改 homestead 的配置
```shell
---
ip: "11.11.10.10"
memory: 2048
cpus: 1

authorize: /Users/me/.ssh/id_rsa.pub

keys:
    - /Users/me/.ssh/id_rsa

folders:
    - map: /Users/me/code/my_project/laravel
      to: /home/vagrant/laravel

sites:
    - map: mytwitter.app
      to: /home/vagrant/laravel/public
      hhvm: true
      
variables:
    - key: APP_ENV
      value: local
```

> `/Users/me/`      开发这本机地址
> `/home/vagrant/`  虚拟机路径，一般不用修改  

---


推荐文章 => https://github.com/astaxie/Go-in-Action/blob/master/ebook/zh/01.3.md
