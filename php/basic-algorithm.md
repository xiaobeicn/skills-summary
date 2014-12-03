### 冒泡算法,排序算法，由于在排序过程中总是小数往前放，大数往后放，相当于气泡往上升，所以称作冒泡排序

```php
$array = array(a,f,c,b,e,h,j,i,g);
function maopao_fun($array){

    if($len <= 1) {
        return $arr;
    }

    $count = count($array);
    for($i=0;$i<$count;$i++){
        for($j=$count-1;$j>$i;$j--){
            if($array[$j] > $array[$j-1]){
                $tmp = $array[$j];
                $array[$j] = $array[$j-1];
                $array[$j-1] = $tmp;
            }
        }
    }
    return $array;
}
```

### 快速排序,
快速排序（Quicksort）是对冒泡排序的一种改进。由C. A. R. Hoare在1962年提出。它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

```php
function quickSort($arr){
    $len = count($arr);
    if($len <= 1) {
        return $arr;
    }
    $key = $arr[0];
    $left_arr    = array();
    $right_arr    = array();

    for($i=1; $i<$len; $i++){
        if($arr[$i] <= $key){
            $left_arr[] = $arr[$i];
        } else {
            $right_arr[] = $arr[$i];
        }
    }

    $left_arr    = quickSort($left_arr);
    $right_arr    = quickSort($right_arr);
    return array_merge($left_arr, array($key), $right_arr);
}
```
### 选择排序
每一趟从待排序的数据元素中选出最小（或最大）的一个元素，顺序放在已排好序的数列的最后，直到全部待排序的数据元素排完。 选择排序是不稳定的排序方法

```php
function select_sort($arr){
    $count = count($arr);
    for($i=0; $i<$count; $i++){
        for($j=$i+1; $j<$count; $j++){
            if ($arr[$i] > $arr[$j]){
                $tmp = $arr[$i];
                $arr[$i] = $arr[$j];
                $arr[$j] = $tmp;
            }
        }
    }
    return $arr;
}
```

### 插入排序
从第一个元素开始，该元素可以认为已经被排序取出下一个元素，在已经排序的元素序列中从后向前扫描如果该元素（已排序）大于新元素，将该元素移到下一位置重复步骤3，直到找到已排序的元素小于或者等于新元素的位置将新元素插入到下一位置中重复步骤2

```php
function insert_sort($arr){
    $count = count($arr);
    for($i=1; $i<$count; $i++){
        $tmp = $arr[$i];
        $j = $i - 1;
        while($arr[$j] > $tmp){
            $arr[$j+1] = $arr[$j];
            $arr[$j] = $tmp;
            $j--;
        }
    }
    return $arr;
}

$arr = array(49,38,65,97,76,13,27);
print_r(insert_sort($arr));
```
