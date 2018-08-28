### 多维数组排序
```php
$arr = array(
  array('key1' => 940, 'key2' => 'blash'),
  array('key1' => 23, 'key2' => 'this'),
  array('key1' => 894, 'key2' => 'that'),
);

function asc_number_sort ($x, $y)
{
  if ($x['key1'] > $y['key1']) {
    return true;
  } elseif ($x['key1'] < $y['key1']) {
    return false;
  } else{
    return 0;
  }
}

usort($arr, 'asc_number_sort');
```

#### 根据数组的key值某几位排序
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

### in_array 弱类型比较
```php
$k = 'hello';
$arr = array(0,1,2,3);
var_dump(in_array($k,$arr));        // true
var_dump(in_array($k,$arr,true));   // false
```

###  判断数组key是否存在
```php
$arr = array('key' => null);
var_dump(isset($arr['key']));
var_dump(array_key_exists($arr, 'key'));
    > Output for hhvm-3.3.1 - 3.8.0   [bool(false), bool(false)]
    > Output for 5.3.0 - 7.0.0beta2   [bool(false), NULL]

```
