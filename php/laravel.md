# Laravel

1、监听查询SQL写入到日志(by [overtrue](https://github.com/overtrue))：

```php
if (App::environment() != 'production') {
    //记录SQL
    Event::listen('illuminate.query', function($sql, $params)
    {
        array_unshift($params, str_replace('?', '%s', $sql));
        Log::info(call_user_func_array('sprintf', $params));
    });
}
```

2、自动对 POST, PUT 或者 DELETE 的请求进行 csrf 认证

```php
Route::when('*', 'csrf', array('post', 'put', 'delete'));
```
