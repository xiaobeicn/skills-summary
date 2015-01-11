### 多维数组排序
```php
$arr = array(
  array('key1' => 940, 'key2' => 'blash'),
  array('key1' => 23, 'key2' => 'this'),
  array('key1' => 894, 'key2' => 'that'),
);

function asc_number_sort ($x, $y)
{
  if ($x['key1] > $y['key1']) {
    return true;
  } elseif ($x['key1] < $y['key1']) {
    return false;
  } else{
    return 0;
  }
}

usort($arr, 'asc_number_sort');
```

---

