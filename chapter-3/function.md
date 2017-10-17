# 全局函数和常量

全局函数和常量
    + [YP框架常量](#constant)
    + [YP框架函数](#function)

><span id='constant'>**YP框架常量**</span>

```php
// 前端资源目录
define('FRONT_PATH', __DIR__ . DIRECTORY_SEPARATOR);

// 定义根路径
define('ROOT_PATH', dirname(__DIR__) . DIRECTORY_SEPARATOR);

// 定义应用路径
define('APP_PATH', rtrim(dirname(__DIR__) . '/app', '/') . DIRECTORY_SEPARATOR);

// 定义框架路径
define('SYSTEM_PATH', rtrim(__DIR__, '/') . DIRECTORY_SEPARATOR);

// 定义测试路径
define('TEST_PATH', rtrim(dirname(__DIR__) . '/test', '/') . DIRECTORY_SEPARATOR);

// 重写目录
define('WRITE_PATH', rtrim(dirname(__DIR__) . '/write', '/') . DIRECTORY_SEPARATOR);

// 缓存目录
define('CACHE_PATH', rtrim(dirname(__DIR__) . '/cache', '/') . DIRECTORY_SEPARATOR);

// 定义环境常量
define('ENVIRONMENT', isset($_SERVER['YP_ENV']) ? $_SERVER['YP_ENV'] : 'prod');

// 跟踪调试
define('SHOW_DEBUG_BACKTRACE', true);

// 调试模式 0:关闭调试 1:开始调试
define('YP_DEBUG', 1);

// 定义应用的命名空间
define('APP_NAMESPACE', 'App');

// 定义退出的常量的状态码
defined('EXIT_ERROR')     || define('EXIT_ERROR',     1);      // 一般错误
defined('EXIT__AUTO_MIN') || define('EXIT__AUTO_MIN', 9);      // 最低自动分配错误
defined('EXIT__AUTO_MAX') || define('EXIT__AUTO_MAX', 125);    // 最高自动分配错误

// 定义composer的自动加载文件路径
define('COMPOSER_PATH', ROOT_PATH . 'vendor/autoload.php');

```

><span id='function'>**YP框架函数**</span>

框架的函数定义在system目录下的Functions.php和app/Functions目录下的Functions.php中,在此文档就不在做过多的介绍。
