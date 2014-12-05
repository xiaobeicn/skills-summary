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

####根据数组的key值某几位排序

一个应用场景是HBase数据库行键包含日期，然后需要按照这个日期排序。
```php
function SortByKey(&$arr, $offset, $length)
{
	$cmp = function($a, $b) use ($offset, $length){
		$a = substr($a, $offset, $length);
		$b = substr($b, $offset, $length);
		if ($a == $b) {
			return 0;
		}
		return ($a > $b) ? -1 : 1;
	};
	uksort($arr, $cmp);
}
```
