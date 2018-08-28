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

----

### 验证邮件地址

```php
<?php
  filter_var('sgamgee@example.com', FILTER_VALIDATE_EMAIL);
  //Returns "sgamgee@example.com". This is a valid email address.
  
  filter_var('sauron@mordor', FILTER_VALIDATE_EMAIL);
  // Returns boolean false! This is *not* a valid email address.
?>
```

----

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

----

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

----

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
// 有人反映下面代码，不靠谱，冲撞率极高，这里就仅做学习，不推荐正式环境使用了
/**
 *
 * uniqid - 官方是这样说的：
 * Gets a prefixed unique identifier based on the current time in microseconds.
 */
function build_order_no()
{
    return date('Ymd').substr(implode(NULL, array_map('ord', str_split(substr(uniqid(), 7, 13), 1))), 0, 8);
}

// 补充：来自ECSHOP订单号生成函数：/includes/lib_order.php文件中的get_order_sn()
/**
 * 得到新订单号
 * @return  string
 */
function build_order_no()
{
    /* 选择一个随机的方案 */
    // Note: 自 PHP 4.2.0 起，不再需要用 srand() 或 mt_srand() 给随机数发生器播种 ，因为现在是由系统自动完成的。 
    mt_srand((double) microtime() * 1000000);
 
    return date('Ymd') . str_pad(mt_rand(1, 99999), 5, '0', STR_PAD_LEFT);
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

### 解决中文用base64_decode解密的时候,出现乱码
* 利用PHP函数设置页面的头文件编码

```php
header("Content-Type:text/html;charset=utf-8");
```

* 有一些中文字符,用GET形式传过来的时候,+号会被替换成空格.

```php
$key = base64_decode(str_replace(" ","+",$_GET['key']));
```

----

### 缓冲区刷新

```php
function flush_buffers()
{
	ob_end_flush();
	ob_flush();
	flush();
	ob_start('ob_callback');
}

function ob_callback($buffer)
{
	return $buffer . str_repeat(' ', max(0, 4097 - strlen($buffer)));
}
```

----

### 简单的页面缓存
如果你的项目不是基于 CMS 系统或框架，打造一个简单的缓存系统将会非常实在。下面的代码很简单，但是对小网站而言能切切实实解决问题

```php
<?php  
    // define the path and name of cached file  
    $cachefile = 'cached-files/'.date('M-d-Y').'.php';  
    // define how long we want to keep the file in seconds. I set mine to 5 hours.  
    $cachetime = 18000;  
    // Check if the cached file is still fresh. If it is, serve it up and exit.  
    if (file_exists($cachefile) && time() - $cachetime < filemtime($cachefile)) {  
    include($cachefile);  
        exit;  
    }  
    // if there is either no file OR the file to too old, render the page and capture the HTML.  
    ob_start();  
?>  
    <html>  
        output all your html here.  
    </html>  
<?php  
    // We're done! Save the cached content to a file  
    $fp = fopen($cachefile, 'w');  
    fwrite($fp, ob_get_contents());  
    fclose($fp);  
    // finally send browser output  
    ob_end_flush();  
?>
```

> via: http://wesbos.com/simple-php-page-caching-technique/

----

### 在 PHP 中计算距离
这是一个非常有用的距离计算函数，利用纬度和经度计算从 A 地点到 B地点的距离。该函数可以返回英里，公里，海里三种单位类型的距离。

```php
function distance($lat1, $lon1, $lat2, $lon2, $unit) {   
  $theta = $lon1 - $lon2;  
  $dist = sin(deg2rad($lat1)) * sin(deg2rad($lat2)) +  cos(deg2rad($lat1)) * cos(deg2rad($lat2)) * cos(deg2rad($theta));  
  $dist = acos($dist);  
  $dist = rad2deg($dist);  
  $miles = $dist * 60 * 1.1515;  
  $unit = strtoupper($unit);  
 
  if ($unit == "K") {  
    return ($miles * 1.609344);  
  } else if ($unit == "N") {  
      return ($miles * 0.8684);  
    } else {  
        return $miles;  
      }  
}

//使用方法：
echo distance(32.9697, -96.80322, 29.46786, -98.53506, "k")." kilometers";
```

----

###将秒数转换为时间(年、月、日、小时…)
这个有用的函数能将秒数表示的事件转换为年、月、日、小时等时间格式。

```php
function Sec2Time($time){  
  if(is_numeric($time)){  
    $value = array(  
      "years" => 0, "days" => 0, "hours" => 0,  
      "minutes" => 0, "seconds" => 0,  
    );  
    if($time >= 31556926){  
      $value["years"] = floor($time/31556926);  
      $time = ($time%31556926);  
    }  
    if($time >= 86400){  
      $value["days"] = floor($time/86400);  
      $time = ($time%86400);  
    }  
    if($time >= 3600){  
      $value["hours"] = floor($time/3600);  
      $time = ($time%3600);  
    }  
    if($time >= 60){  
      $value["minutes"] = floor($time/60);  
      $time = ($time%60);  
    }  
    $value["seconds"] = floor($time);  
    return (array) $value;  
  }else{  
    return (bool) FALSE;  
  }  
}
```

----

### 强制下载文件
一些诸如 mp3 类型的文件，通常会在客户端浏览器中直接被播放或使用。如果你希望它们强制被下载，也没问题。可以使用以下代码：

```php
function downloadFile($file){  
        $file_name = $file;  
        $mime = 'application/force-download';  
    header('Pragma: public');     // required  
    header('Expires: 0');        // no cache  
    header('Cache-Control: must-revalidate, post-check=0, pre-check=0');  
    header('Cache-Control: private',false);  
    header('Content-Type: '.$mime);  
    header('Content-Disposition: attachment; filename="'.basename($file_name).'"');  
    header('Content-Transfer-Encoding: binary');  
    header('Connection: close');  
    readfile($file_name);        // push it out  
    exit();  
}
```
----

### PHP加密解密
PHP加密和解密函数可以用来加密一些有用的字符串存放在数据库里，并且通过可逆解密字符串，该函数使用了base64和MD5加密和解密。

```php
function encryptDecrypt($key, $string, $decrypt){ 
    if($decrypt){ 
        $decrypted = rtrim(mcrypt_decrypt(MCRYPT_RIJNDAEL_256, md5($key), base64_decode($string), MCRYPT_MODE_CBC, md5(md5($key))), "12"); 
        return $decrypted; 
    }else{ 
        $encrypted = base64_encode(mcrypt_encrypt(MCRYPT_RIJNDAEL_256, md5($key), $string, MCRYPT_MODE_CBC, md5(md5($key)))); 
        return $encrypted; 
    } 
} 
```

----

### PHP获取文件大小并格式化

```php
function formatSize($size) { 
    $sizes = array(" Bytes", " KB", " MB", " GB", " TB", " PB", " EB", " ZB", " YB"); 
    if ($size == 0) {  
        return('n/a');  
    } else { 
      return (round($size/pow(1024, ($i = floor(log($size, 1024)))), 2) . $sizes[$i]);  
    } 
} 
```

### PHP获取当前页面URL

```php
function curPageURL() { 
    $pageURL = 'http'; 
    if (!empty($_SERVER['HTTPS'])) {$pageURL .= "s";} 
    $pageURL .= "://"; 
    if ($_SERVER["SERVER_PORT"] != "80") { 
        $pageURL .= $_SERVER["SERVER_NAME"].":".$_SERVER["SERVER_PORT"].$_SERVER["REQUEST_URI"]; 
    } else { 
        $pageURL .= $_SERVER["SERVER_NAME"].$_SERVER["REQUEST_URI"]; 
    } 
    return $pageURL; 
} 
```

----

### PHP强制下载文件

----

```php
function download($filename){ 
    if ((isset($filename))&&(file_exists($filename))){ 
       header("Content-length: ".filesize($filename)); 
       header('Content-Type: application/octet-stream'); 
       header('Content-Disposition: attachment; filename="' . $filename . '"'); 
       readfile("$filename"); 
    } else { 
       echo "Looks like file does not exist!"; 
    } 
} 
```

### PHP截取字符串长度
我们经常会遇到需要截取字符串(含中文汉字)长度的情况，比如标题显示不能超过多少字符，超出的长度用...表示，以下函数可以满足你的需求。

```php
/* 
 Utf-8、gb2312都支持的汉字截取函数 
 cut_str(字符串, 截取长度, 开始长度, 编码); 
 编码默认为 utf-8 
 开始长度默认为 0 
*/ 
function cutStr($string, $sublen, $start = 0, $code = 'UTF-8'){ 
    if($code == 'UTF-8'){ 
        $pa = "/[\x01-\x7f]|[\xc2-\xdf][\x80-\xbf]|\xe0[\xa0-\xbf][\x80-\xbf]|[\xe1-\xef][\x80-\xbf][\x80-\xbf]|\xf0[\x90-\xbf][\x80-\xbf][\x80-\xbf]|[\xf1-\xf7][\x80-\xbf][\x80-\xbf][\x80-\xbf]/"; 
        preg_match_all($pa, $string, $t_string); 
 
        if(count($t_string[0]) - $start > $sublen) return join('', array_slice($t_string[0], $start, $sublen))."..."; 
        return join('', array_slice($t_string[0], $start, $sublen)); 
    }else{ 
        $start = $start*2; 
        $sublen = $sublen*2; 
        $strlen = strlen($string); 
        $tmpstr = ''; 
 
        for($i=0; $i<$strlen; $i++){ 
            if($i>=$start && $i<($start+$sublen)){ 
                if(ord(substr($string, $i, 1))>129){ 
                    $tmpstr.= substr($string, $i, 2); 
                }else{ 
                    $tmpstr.= substr($string, $i, 1); 
                } 
            } 
            if(ord(substr($string, $i, 1))>129) $i++; 
        } 
        if(strlen($tmpstr)<$strlen ) $tmpstr.= "..."; 
        return $tmpstr; 
    } 
} 
```

----

### PHP获取客户端真实IP

```php
//获取用户真实IP 
function getIp() { 
    if (getenv("HTTP_CLIENT_IP") && strcasecmp(getenv("HTTP_CLIENT_IP"), "unknown")) 
        $ip = getenv("HTTP_CLIENT_IP"); 
    else 
        if (getenv("HTTP_X_FORWARDED_FOR") && strcasecmp(getenv("HTTP_X_FORWARDED_FOR"), "unknown")) 
            $ip = getenv("HTTP_X_FORWARDED_FOR"); 
        else 
            if (getenv("REMOTE_ADDR") && strcasecmp(getenv("REMOTE_ADDR"), "unknown")) 
                $ip = getenv("REMOTE_ADDR"); 
            else 
                if (isset ($_SERVER['REMOTE_ADDR']) && $_SERVER['REMOTE_ADDR'] && strcasecmp($_SERVER['REMOTE_ADDR'], "unknown")) 
                    $ip = $_SERVER['REMOTE_ADDR']; 
                else 
                    $ip = "unknown"; 
    return ($ip); 
} 
```

----

### PHP防止SQL注入

```php
function injCheck($sql_str) {  
    $check = preg_match('/select|insert|update|delete|\'|\/\*|\*|\.\.\/|\.\/|union|into|load_file|outfile/', $sql_str); 
    if ($check) { 
        echo '非法字符！！'; 
        exit; 
    } else { 
        return $sql_str; 
    } 
} 
```

----

### PHP页面提示与跳转

```php
function message($msgTitle,$message,$jumpUrl){ 
    $str = '<!DOCTYPE HTML>'; 
    $str .= '<html>'; 
    $str .= '<head>'; 
    $str .= '<meta charset="utf-8">'; 
    $str .= '<title>页面提示</title>'; 
    $str .= '<style type="text/css">'; 
    $str .= '*{margin:0; padding:0}a{color:#369; text-decoration:none;}a:hover{text-decoration:underline}body{height:100%; font:12px/18px Tahoma, Arial,  sans-serif; color:#424242; background:#fff}.message{width:450px; height:120px; margin:16% auto; border:1px solid #99b1c4; background:#ecf7fb}.message h3{height:28px; line-height:28px; background:#2c91c6; text-align:center; color:#fff; font-size:14px}.msg_txt{padding:10px; margin-top:8px}.msg_txt h4{line-height:26px; font-size:14px}.msg_txt h4.red{color:#f30}.msg_txt p{line-height:22px}'; 
    $str .= '</style>'; 
    $str .= '</head>'; 
    $str .= '<body>'; 
    $str .= '<div class="message">'; 
    $str .= '<h3>'.$msgTitle.'</h3>'; 
    $str .= '<div class="msg_txt">'; 
    $str .= '<h4 class="red">'.$message.'</h4>'; 
    $str .= '<p>系统将在 <span style="color:blue;font-weight:bold">3</span> 秒后自动跳转,如果不想等待,直接点击 <a href="{$jumpUrl}">这里</a> 跳转</p>'; 
    $str .= "<script>setTimeout('location.replace(\'".$jumpUrl."\')',2000)</script>"; 
    $str .= '</div>'; 
    $str .= '</div>'; 
    $str .= '</body>'; 
    $str .= '</html>'; 
    echo $str; 
} 
```

----

### PHP计算时长

```php
function changeTimeType($seconds) { 
    if ($seconds > 3600) { 
        $hours = intval($seconds / 3600); 
        $minutes = $seconds % 3600; 
        $time = $hours . ":" . gmstrftime('%M:%S', $minutes); 
    } else { 
        $time = gmstrftime('%H:%M:%S', $seconds); 
    } 
    return $time; 
} 
```

----

### PHP打印数组

```php
function cf_dd(){
	array_map(function($x) { var_dump($x); }, func_get_args()); die;
}
```

----

### 遍历删除目录和目录下所有文件

```php
function cf_del_dir($dir){
	if (!is_dir($dir)){
		return false;
	}
	$handle = opendir($dir);
	while (($file = readdir($handle)) !== false){
		if ($file != "." && $file != ".."){
			is_dir("$dir/$file")?	cf_del_dir("$dir/$file"):@unlink("$dir/$file");
		}
	}
	if (readdir($handle) == false){
		closedir($handle);
		@rmdir($dir);
	}
}
```	

----

### 纠正不完整HTML(by [overtrue](https://github.com/overtrue))

```php
function force_balance_tags( $text ) {
    $tagstack = array();
    $stacksize = 0;
    $tagqueue = '';
    $newtext = '';
    // Known single-entity/self-closing tags
    $single_tags = array( 'area', 'base', 'basefont', 'br', 'col', 'command', 'embed', 'frame', 'hr', 'img', 'input', 'isindex', 'link', 'meta', 'param', 'source' );
    // Tags that can be immediately nested within themselves
    $nestable_tags = array( 'blockquote', 'div', 'object', 'q', 'span' );

    // WP bug fix for comments - in case you REALLY meant to type '< !--'
    $text = str_replace('< !--', '<    !--', $text);
    // WP bug fix for LOVE <3 (and other situations with '<' before a number)
    $text = preg_replace('#<([0-9]{1})#', '&lt;$1', $text);

    while ( preg_match("/<(\/?[\w:]*)\s*([^>]*)>/", $text, $regex) ) {
        $newtext .= $tagqueue;

        $i = strpos($text, $regex[0]);
        $l = strlen($regex[0]);

        // clear the shifter
        $tagqueue = '';
        // Pop or Push
        if ( isset($regex[1][0]) && '/' == $regex[1][0] ) { // End Tag
            $tag = strtolower(substr($regex[1],1));
            // if too many closing tags
            if( $stacksize <= 0 ) {
                $tag = '';
                // or close to be safe $tag = '/' . $tag;
            }
            // if stacktop value = tag close value then pop
            else if ( $tagstack[$stacksize - 1] == $tag ) { // found closing tag
                $tag = '</' . $tag . '>'; // Close Tag
                // Pop
                array_pop( $tagstack );
                $stacksize--;
            } else { // closing tag not at top, search for it
                for ( $j = $stacksize-1; $j >= 0; $j-- ) {
                    if ( $tagstack[$j] == $tag ) {
                    // add tag to tagqueue
                        for ( $k = $stacksize-1; $k >= $j; $k--) {
                            $tagqueue .= '</' . array_pop( $tagstack ) . '>';
                            $stacksize--;
                        }
                        break;
                    }
                }
                $tag = '';
            }
        } else { // Begin Tag
            $tag = strtolower($regex[1]);

            // Tag Cleaning

            // If it's an empty tag "< >", do nothing
            if ( '' == $tag ) {
                // do nothing
            }
            // ElseIf it presents itself as a self-closing tag...
            elseif ( substr( $regex[2], -1 ) == '/' ) {
                // ...but it isn't a known single-entity self-closing tag, then don't let it be treated as such and
                // immediately close it with a closing tag (the tag will encapsulate no text as a result)
                if ( ! in_array( $tag, $single_tags ) )
                    $regex[2] = trim( substr( $regex[2], 0, -1 ) ) . "></$tag";
            }
            // ElseIf it's a known single-entity tag but it doesn't close itself, do so
            elseif ( in_array($tag, $single_tags) ) {
                $regex[2] .= '/';
            }
            // Else it's not a single-entity tag
            else {
                // If the top of the stack is the same as the tag we want to push, close previous tag
                if ( $stacksize > 0 && !in_array($tag, $nestable_tags) && $tagstack[$stacksize - 1] == $tag ) {
                    $tagqueue = '</' . array_pop( $tagstack ) . '>';
                    $stacksize--;
                }
                $stacksize = array_push( $tagstack, $tag );
            }

            // Attributes
            $attributes = $regex[2];
            if( ! empty( $attributes ) && $attributes[0] != '>' )
                $attributes = ' ' . $attributes;

            $tag = '<' . $tag . $attributes . '>';
            //If already queuing a close tag, then put this tag on, too
            if ( !empty($tagqueue) ) {
                $tagqueue .= $tag;
                $tag = '';
            }
        }
        $newtext .= substr($text, 0, $i) . $tag;
        $text = substr($text, $i + $l);
    }

    // Clear Tag Queue
    $newtext .= $tagqueue;

    // Add Remaining text
    $newtext .= $text;

    // Empty Stack
    while( $x = array_pop($tagstack) )
        $newtext .= '</' . $x . '>'; // Add remaining tags to close

    // WP fix for the bug with HTML comments
    $newtext = str_replace("< !--","<!--",$newtext);
    $newtext = str_replace("<    !--","< !--",$newtext);

    return $newtext;
}
```

demo:

```php
echo force_balance_tags('<p><a>overtrue<p>');
// <p><a>overtrue</a><p>
```

----

### 生成随机的颜色值

```php
function randomColor() { 
    $str = '#'; 
    for($i = 0 ; $i < 6 ; $i++) { 
        $randNum = rand(0 , 15); 
        switch ($randNum) { 
            case 10: $randNum = 'A'; break; 
            case 11: $randNum = 'B'; break; 
            case 12: $randNum = 'C'; break; 
            case 13: $randNum = 'D'; break; 
            case 14: $randNum = 'E'; break; 
            case 15: $randNum = 'F'; break; 
        } 
        $str .= $randNum; 
    } 
    return $str; 
} 
```

### 2个时间的间隔
```php
$from = '2014-08-31 18:00:00';
$to = '2014-09-03 09:56:00';

$fromDate = new DateTime($from);
$toDate = new DateTime($to);

$dateIntval = $fromDate->diff($toDate);

echo $dateIntval->format('相差%r%y年%m月%d天%h小时%i分%s秒');

```

### 提取字符串 中文，英文，数字
```php
$pattern = '/[^\x{4e00}-\x{9fa5}\d\w]+/u';
$res = preg_replace($pattern, '', $str);
var_dump($res);
```

### 检测文件编码
```php
function detect_encoding($file) {
    $list = array('GBK', 'UTF-8', 'UTF-16LE', 'UTF-16BE', 'ISO-8859-1');
    $str = file_get_contents($file);
    foreach ($list as $item) {
        $tmp = mb_convert_encoding($str, $item, $item);
        if (md5($tmp) == md5($str)) {
            return $item;
        }
    }
    return null;
}
```

### 自动解析编码读入文件
```php
function auto_read($file, $charset='UTF-8') {
    $list = array('GBK', 'UTF-8', 'UTF-16LE', 'UTF-16BE', 'ISO-8859-1');
    $str = file_get_contents($file);
    foreach ($list as $item) {
        $tmp = mb_convert_encoding($str, $item, $item);
        if (md5($tmp) == md5($str)) {
            return mb_convert_encoding($str, $charset, $item);
        }
    }
    return "";
}
```
