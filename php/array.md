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
### 多维数组去重
```php
$arr = array(
    array('num'=>110,'id'=>1,'status'=>2),
    array('num'=>110,'id'=>1,'status'=>2),
    array('num'=>110,'id'=>3,'status'=>4),
    array('num'=>112,'id'=>1,'status'=>6),
    );
function assoc_unique(&$arr, $key,$key2='') { 
        $res=array();
        for($i=0;$i<count($arr);$i++) {
            $k = empty($key2)?$arr[$i][$key]:$arr[$i][$key].$arr[$i][$key2];
            if(!isset($res[$k])) { 
                $res[$k] = $arr[$i];
            }
        }
        $arr=array_values($res);
    }
    assoc_unique2($arr,'num','id');//按num和id去重
    //assoc_unique($arr,'num');//按num去重
    print_r($arr);
```

---

