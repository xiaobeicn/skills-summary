### 自动加载类

```php
<?php
  // 首先，定义你的自动载入的函数
  function autoload($className){
      include_once($className . '.php');
  }
  
  // 然后，注册它。
  spl_autoload_register('autoload');
  
  // 最后，使用它
  // 因为我们没包含一个定义有 DemoClass 的文件，所以自动加载器会介入并包含 DemoClass.php。
  // 在本例中，假定在 DemoClass.php 文件中定义了 DemoClass 类。
  $var = new DemoClass();
?>
```

### 验证邮件地址

```php
<?php
  filter_var('sgamgee@example.com', FILTER_VALIDATE_EMAIL);
  //Returns "sgamgee@example.com". This is a valid email address.
  
  filter_var('sauron@mordor', FILTER_VALIDATE_EMAIL);
  // Returns boolean false! This is *not* a valid email address.
?>
```

### 净化 HTML 输入和输出

```php
<?php
  // Oh no!  The user has submitted malicious HTML, and we have to display it in our web app!
  $evilHtml = '<div onclick="xss();">Mua-ha-ha!  Twiddling my evil mustache...</div>';
  
  // Use the ENT_QUOTES flag to make sure both single and double quotes are escaped.
  // Use the UTF-8 character encoding if you've stored the text as UTF-8 (as you should have).
  // See the UTF-8 section in this document for more details.
  $safeHtml = htmlentities($evilHtml, ENT_QUOTES, 'UTF-8');
  // $safeHtml is now fully escaped HTML.  You can output $safeHtml to your users without fear!
?>
```

### 检测一个值是否为 null 或 false

```php
<?php
  $x = 0;
  $y = null;
  
  // Is $x null?
  if($x == null)
      print('Oops! $x is 0, not null!');
  
  // Is $y null?
  if(is_null($y))
      print('Great, but could be faster.');
  
  if($y === null)
      print('Perfect!');
  
  // Does the string abc contain the character a?
  if(strpos('abc', 'a'))
      // GOTCHA!  strpos returns 0, indicating it wishes to return the position of the first character.
      // But PHP interpretes 0 as false, so we never reach this print statement!
      print('Found it!'); 
  
  //Solution: use !== (the opposite of ===) to see if strpos() returns 0, or boolean false.   
  if(strpos('abc', 'a') !== false)
      print('Found it for real this time!');
?>
```

### 让项目支持laravel一样的自动环境检测(by [overtrue](https://github.com/overtrue))

代码在**开发**机器上自动调用开发配置，当为产品环境自动调用产品环境配置，让你的应用自动化识别运行环境的方法：

1. 添加环境列表：

  app/env.php:
  ```php
  <?php
  
  return [
      // 环境名 => [hostname1, hostname2, ...] ， 环境名请通过shell命令: hostname 得到
      'local'      => ['overtrue', 'domain.local'], 
      'production' => ['*'], // 除了local的机器外，其它为产品环境
  ];
  ```
2. 环境识别函数：

  ```php
  /**
   * 获取应用运行环境
   *
   * @return string
   */
  function env($test = null)
  {
      $envs = include __DIR__ . '/env.php';
      foreach ($envs as $env => $hostnames) {
          if (in_array(gethostname(), $hostnames) || in_array('*', $hostnames)) {
              return $test ? $env == $test : $env;
          }
      }
  
      return $test ? 'production' === $test : 'production';
  }
  ```
3. 根据环境值读取对应的配置：

  ```php
  $env = env(); // local
  // do you things.

  ```
得到了环境名，就拿他去读取相应的配置文件吧，更多可以参考：https://github.com/overtrue/rester

----

### 生成唯一订单号

```php
/**
 *
 * uniqid - 官方是这样说的：
 * Gets a prefixed unique identifier based on the current time in microseconds.
 */
function build_order_no()
{
    return date('Ymd').substr(implode(NULL, array_map('ord', str_split(substr(uniqid(), 7, 13), 1))), 0, 8);
}
```

----

### 5行vs9行 不用递归实现无限分类数据的树形格式化

```php
<?php
/**
 * 方法一：
 * 此方法由@Tonton 提供
 * @form http://levi.cg.am
 * @date 2012-12-12
 */
function genTree5(Array $items) {
    foreach ($items as $item)
        $items[$item['pid']]['son'][$item['id']] = &$items[$item['id']];
    return isset($items[0]['son']) ? $items[0]['son'] : array();
}
 
/**
 * 方法二：将数据格式化成树形结构
 * @author Xuefen.Tong
 * @form http://levi.cg.am
 * @param array $items
 * @return array
 */
function genTree9(Array $items) {
    $tree = array();    //格式化好的树
    foreach ($items as $item)
        if (isset($items[$item['pid']]))
            $items[$item['pid']]['son'][] = &$items[$item['id']];
        else
            $tree[] = &$items[$item['id']];
    return $tree;
}

$items = array(
    1 => array('id' => 1, 'pid' => 0, 'name' => '江西省'),
    2 => array('id' => 2, 'pid' => 0, 'name' => '黑龙江省'),
    3 => array('id' => 3, 'pid' => 1, 'name' => '南昌市'),
    4 => array('id' => 4, 'pid' => 2, 'name' => '哈尔滨市'),
    5 => array('id' => 5, 'pid' => 2, 'name' => '鸡西市'),
    6 => array('id' => 6, 'pid' => 4, 'name' => '香坊区'),
    7 => array('id' => 7, 'pid' => 4, 'name' => '南岗区'),
    8 => array('id' => 8, 'pid' => 6, 'name' => '和兴路'),
    9 => array('id' => 9, 'pid' => 7, 'name' => '西大直街'),
    10 => array('id' => 10, 'pid' => 8, 'name' => '东北林业大学'),
    11 => array('id' => 11, 'pid' => 9, 'name' => '哈尔滨工业大学'),
    12 => array('id' => 12, 'pid' => 8, 'name' => '哈尔滨师范大学'),
    13 => array('id' => 13, 'pid' => 1, 'name' => '赣州市'),
    14 => array('id' => 14, 'pid' => 13, 'name' => '赣县'),
    15 => array('id' => 15, 'pid' => 13, 'name' => '于都县'),
    16 => array('id' => 16, 'pid' => 14, 'name' => '茅店镇'),
    17 => array('id' => 17, 'pid' => 14, 'name' => '大田乡'),
    18 => array('id' => 18, 'pid' => 16, 'name' => '义源村'),
    19 => array('id' => 19, 'pid' => 16, 'name' => '上坝村'),
);
 
header("Content-Type: text/html; charset=utf-8");
echo "<pre>";
print_r(genTree5($items));
print_r(genTree9($items));
echo "</pre>";
```

>via:http://levi.cg.am/archives/2115

---





