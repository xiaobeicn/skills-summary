### 监听查询SQL写入到日志(by [overtrue](https://github.com/overtrue))：

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

### 自动对 POST, PUT 或者 DELETE 的请求进行 csrf 认证

```php
Route::when('*', 'csrf', array('post', 'put', 'delete'));
```

---

### 路由参数全局正则表达式模式
```php
Route::pattern('id', '\d+');
Route::pattern('hash', '[a-z0-9]+');
Route::pattern('hex', '[a-f0-9]+');
Route::pattern('uuid', '[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}');
Route::pattern('base', '[a-zA-Z0-9]+');
Route::pattern('slug', '[a-z0-9-]+');
Route::pattern('username', '[a-z0-9_-]{3,16}');
```

### 路由直接返回 json Response
```php
//array
Route::get('/request-json-array', function(){
    
    $array = array('foo', 'bar');
    
    //this route should returns json response
    return $array;
    
});

//or eloquent model

Route::get('/request-json-model', function(){
    
    //if 'User' is an eloquent model, this route should returns json response
    return User::all();
    
});
```

### 显示所有sql
```php
// Display all SQL executed in Eloquent
Event::listen('illuminate.query', function($query)
{
    var_dump($query);
});
```





