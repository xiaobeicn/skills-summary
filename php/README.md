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








