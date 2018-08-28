### Curl远程获取数据
```php
/**
 * post请求接口
 *
 * @param $url string           
 * @param $str vars           
 * @return $result
 */
function post_request($url, $str)
{
	$ch = curl_init();
	curl_setopt($ch, CURLOPT_URL, $url);
	curl_setopt($ch, CURLOPT_POST, 1);
	curl_setopt($ch, CURLOPT_USERAGENT, "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)");
	curl_setopt($ch, CURLOPT_TIMEOUT, 300);
	curl_setopt($ch, CURLOPT_HTTPHEADER, array(
		'Accept-Language: zh-cn', 
		'Connection: Keep-Alive', 
		'Cache-Control: no-cache'
	));
	curl_setopt($ch, CURLOPT_POSTFIELDS, $str);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
	curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 15);
	$result = curl_exec($ch);
	
	curl_close($ch);
	return $result === false ? false : trim($result);
}

function get_request($url, $timeout = 15)
{
	$ch = curl_init();
	curl_setopt($ch, CURLOPT_URL, $url);
	curl_setopt($ch, CURLOPT_HTTPHEADER, array(
		'Expect:'
	));
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
	curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, $timeout);
	curl_setopt($ch, CURLOPT_USERAGENT, "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.2; SV1; .NET CLR 1.1.4322)");
	$result = curl_exec($ch);
	curl_close($ch);
	return $result === false ? false : trim($result);
}
```

