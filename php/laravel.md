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

### 在BaseModel中定义了multiwhere来支持多where
```php
// 多where
public function scopeMultiwhere($query, $arr)
{
    if (!is_array($arr)) {
        return $query;
    }

    foreach ($arr as $key => $value) {
        $query = $query->where($key, $value);
    }
    return $query;
}
```

### 日志分文件存储
```php
// 1.首先自定义了日志编辑类：
<?php
use Monolog\Logger;
use Monolog\Handler\StreamHandler;
use Illuminate\Log\Writer;
 
class BLogger
{
    // 所有的LOG都要求在这里注册
    const LOG_ERROR = 'error';
    const LOG_SHOP = 'shop';
    const LOG_QUERY = 'query';
    const LOG_LOGIN = 'login';
 
    private static $loggers = array();
 
    // 获取一个实例
    public static function getLogger($type = self::LOG_ERROR, $day = 30)
    {
        if (empty(self::$loggers[$type])) {
            self::$loggers[$type] = new Writer(new Logger($type));
        }
        $log = self::$loggers[$type];
        $log->useDailyFiles(storage_path().'/logs/'. $type .'.log', $day);
        return $log;
    }
}

// 2.然后在app/start/global.php中修改错误日志回调函数为：
App::error(function(Exception $exception, $code)
{
    // 如果没有路径就直接跳转到登录页面
    if ($exception instanceof NotFoundHttpException) {
        return Redirect::route('login');
    }
 
     Log::error($exception);
 
    $err = [
        'message' => $exception->getMessage(),
        'file' => $exception->getFile(),
        'line' => $exception->getLine(),
        'code' => $exception->getCode(),
        'url' => Request::url(),
        'input' => Input::all(),
    ];
    BLogger::getLogger(BLogger::LOG_ERROR)->error($err);
});
```




