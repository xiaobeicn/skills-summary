```php
//冒泡排序
function bubble () {
    $arr = array(13,56,10,1,65,4,9);
    $len = count($arr);
    for ($i=0;$i<$len-1;$i++) {
        for ($j=$i+1;$j<$len;$j++) {
            if ($arr[$i]<$arr[$j]) {
                $tmp = $arr[$i];
                $arr[$i] = $arr[$j];
                $arr[$j] = $tmp;
            }
        }
    }
    return $arr;
}
 
//插入排序
function insert() {
    $arr = array(13,56,10,1,65,4,9);
    $len = count($arr);
    for ($i=1;$i<$len;$i++) {
        $tmp = $arr[$i];
        $j = $i-1;
        while ($j>=0 && $tmp<$arr[$j]) {
            $arr[$j+1] = $arr[$j];
            $j--;
        }
        $arr[$j+1] = $tmp;
    }
    return $arr;
}
 
//选择排序
function select() {
    $arr = array(13,56,10,1,65,4,9);
    $len = count($arr);
    for ($i=0;$i<$len;$i++) {
        for ($j=$i+1;$j<$len;$j++) {
            if($arr[$i]>$arr[$j]) {
                $tmp = $arr[$i];
                $arr[$i] = $arr[$j];
                $arr[$j] = $tmp;
            }
        }
    }
    return $arr;
}
 
//快速排序(递归)
function quick($arr) {
    $len = count($arr);
    if ($len <= 1) {
        return $arr;
    }
    $arr_l = array();
    $arr_r = array();
    $key = $arr[0];
    for ($i=1;$i<$len;$i++) {
        if ($arr[$i]>$key) {
            $arr_r[] = $arr[$i];
        } else {
            $arr_l[] = $arr[$i];
        }
    }
    $arr_l = quick($arr_l);
    $arr_r = quick($arr_r);
    return array_merge($arr_l, array($key), $arr_r);
}
//  $arr = array(13,56,10,1,65,4,9);
//  print_r(quick($arr));
//  return ;
 
//二分法查找
function erfen() {
    $num = 2;
    $arr = array(1,2,4,5,6);
    $len = count($arr);
    if ($len == 0) {
        return -1;
    }
    $start = 0;
    $end = $len-1;
    while ($start<$end) {
        $mid = ($len+1)/2;
        if ($mid == $num) {
            return $mid;
        } else if ($mid > $num) {
            $end = $mid-1;
        } else if ($mid < $num) {
            $start = $mid+1;
        }
    }
    //return -1;//如果找不到返回-1;
}
```
