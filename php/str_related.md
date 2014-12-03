###字符串相关

####生成随机字符串
```php
<?php
    function randstr($len) {
        $c = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
        $ret = "";
        $charlen = strlen($c);
        for($i=0; $i<$len; $i++) { 
            $ret.= $c[rand()%$charlen]; 
        }
        return $ret; 
	}
?>
```
