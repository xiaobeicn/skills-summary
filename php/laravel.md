# Laravel

1、监听查询SQL写入到日志：

```php
if (App::environment() != 'production') {
    //记录SQL
    Event::listen('illuminate.query', function($sql, $param)
    {
        array_unshift($params, str_replace('?', '%s', $sql));
        Log::info(call_user_func_array('sprintf', $params));
    });
}
```
