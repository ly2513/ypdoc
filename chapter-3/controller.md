# 控制器

**控制器**

**控制器**是应用程序的**核心**，因为它们决定了如何处理HTTP请求。

**页面内容目录**

+ 控制器
    + [什么是控制器？](#what)
    + [我们写个：Hello World！](#hello)
    + [方法](#method)
    + [将URI段传递给您的方法](#uri)
    + [定义默认控制器](#controller)
    + [重映射方法调用](#map)
    + [私人方法](#private)
    + [将控制器组织到子目录中](#option)
    + [类构造函数](#constrct)
    + [包括的属性](#attr)
        + [请求对象](#request)
        + [响应对象](#response)
        + [日志对象](#log)
        + [forceHTTPS](#https)
    + [验证$_POST数据](#post)

><span id='what'>**什么是控制器？**</span>

控制器只是一个类文件，以一种与URI相关联的方式命名。

考虑这个URI：
```
example.com/index.php/Blog/
```
在上面的例子中，YP将尝试找到一个控制器名为Blog.php文件并加载它。

当控制器的名称与URI的第一个段匹配时，它将被加载。

><span id='hello'>**我们写个：Hello World！**</span>

让我们创建一个简单的控制器，以便您可以在操作中看到它。使用您的文本编辑器，创建一个名为Blog.php的文件，并在其中放入以下代码：

```php
<?php
class Blog extends \YP\Core\YP_Controller
{
        public function index()
        {
            echo 'Hello World!';
        }
}
```
然后将该文件保存到`/app/Controllers/`目录中。

**重要提示**

+ 文件必须叫做“Blog.php”，大写字母为“B”。现在使用类似于此的网址访问您的网站：

`example.com/index.php/Blog`
如果你做的对，你应该看到：
```
Hello World!
```

+ 类名必须以大写字母开头。这是有效的：

```php
<?php
class Blog  extends  \YP\Core\YP_Controller
{

}
```

这是不正确的：

```php
<?php
class Blog extends \YP\Core\YP_Controller
{

}
```

此外，始终确保您的控制器继承父控制器类，以便它可以继承其父类的所有方法。

><span id='method'>**方法**</span>

在上面的例子中，方法名是`index()`。如果URI 的第二个段为空，那么默认情况下，“index”方法总是加载。显示你的“Hello World”消息的另一种方式是：

`example.com/index.php/blog/index/`URI的第二个段确定控制器中调用哪个方法。

让我们试试吧, 向控制器添加新方法：

```php
<?php
class Blog  extends \YP\Core\YP_Controller
{

        public function index()
        {
            echo 'Hello World!';
        }

        public  function  comments()
        {
            echo  'Look at this!';
        }
}
```
现在加载以下URL以查看注释方法：
`example.com/index.php/blog/comments/`你应该看到你的新消息。

><span id='uri'>**将URI语句传递给您的方法**</span>

如果您的URI包含两个以上的语句，那么它们将作为参数传递给您的方法。

例如，假设你有一个这样的URI：

`example.com/index.php/products/shoes/sandals/123`
您的方法将被传递URI段3和4（“凉鞋”和“123”）：

```php
<?php
class Products extends \YP\Core\YP_Controller
{

    public function shoes($sandals, $id)
    {
            echo  $sandals;
            echo  $id ;
    }
}
```

**重要提示**

如果您使用URI路由功能，传递给您的方法的参数将是重新路由的。

><span id='controller'>**定义默认控制器**</span>

当URI不存在时，可以将YP加载到默认控制器，如仅请求您的站点根URL一样。要指定默认控制器，请打开应用程序`/Config/Routes.php` 文件并设置此变量：

```php
$routes->setDefaultController('Blog');
```
“Blog”是您要使用的控制器类的名称。如果您现在加载`index.php`文件，而不指定任何URI语句，则默认情况下将显示`“Hello World”`消息。

有关更多信息，请参阅URI路由文档的“路由配置选项”部分 。

<span id='map'>**重映射方法调用**</span>

如上所述，URI的第二段通常确定控制器中哪个方法被调用。YP允许您通过使用`_remap()`方法来覆盖此行为：

```php
public  function  _remap （）
{
        //这里的一些代码...
}

```

**重要提示**

如果你的控制器包含一个名为`_remap()`的方法，不管你的URI包含什么，它将始终被调用。它覆盖了URI确定调用哪个方法的正常行为，允许您定义自己的方法路由规则。
被覆盖的方法调用（通常是URI的第二个段）将作为参数传递给_remap（）方法：

```php
public function _remap ($method)
{
    if($method === 'some_method')
    {
        $this->$method();
    } else {
        $this->default_method();
    }
}
```
方法名后面的任何额外的段传递到_remap（）中。这些参数可以传递给方法来模拟YP的默认行为。

例：

```php
public function _remap($method，...$params）
{
    $method = 'process_' .$method;
    if(method_exists($this, $method))
    {
        return $this->$method(...$params);
    }
    show_404();
}
```

><span id='private'>**私有或保护方法**</span>

在某些情况下，您可能希望公开访问隐藏某些方法。为了实现这一点，只需将该方法声明为私有或受保护的，并且不会通过URL请求来提供该方法。例如，如果你有一个这样的方法：

```php
protected function utility()
{
        // some code
}
```
尝试通过URL访问它，如此，将无法正常工作：

```
example.com/blog/utility/

```

><span id='option'>**将控制器组织到子目录中**</span>

如果您正在构建大型应用程序，则可能希望将控制器分层组织或构建为子目录。YP框架允许你这样做。

简单地在`app/Controllers/`下创建子目录，并将控制器类放在其中。

**注意**

+ 使用此功能时，您的URI的第一个段必须指定该文件夹。例如，假设你有一个位于这里的控制器：
```
app/controllers/products/Shoes.php
```
+ 要调用上述控制器，您的URI将如下所示：

```
example.com/products/shoes/show/123
```
每个子目录可能包含一个默认控制器，如果URL仅包含子目录，则将调用该控制器。只需将一个控制器与您的应用程序/Config/Routes.php文件中指定的“default_controller”的名称相匹配。

YP还允许您使用其URI路由功能重新映射URI 。

><span id='constrct'>**类构造函数**</span>

如果您打算在任何控制器中使用构造函数，则 必须在其中放置以下代码行：
```php

parent::__construct(...$params);
或
parent::initialization();
```
这一行是必要的原因是因为您的本地构造函数将覆盖父控制器类中的一个，所以我们需要手动调用它。

例：
```
<?php
class Blog  extends \YP\Core\YP_Controller
{
    public function __construct(...$params)
    {
        parent::__construct(...$params);

        //你自己的构造函数代码
    }
    # 或者
    public function initialization()
    {
        parent::initialization();
        //你自己的构造函数代码
    }
}
```
如果需要设置一些默认值，构造函数是有用的，或者在实例化类时运行默认进程。构造函数不能返回一个值，但是它们可以做一些默认的工作。

><span id='attr'>**包括的属性**</span>

您创建的每个控制器都应该扩展CodeIgniter \ Controller类。该类提供了所有控制器可用的几个功能。

><span id='request'>**请求对象**</span>

应用程序的主要请求实例始终可用作类属性$ this->请求。

><span id='response'>**响应对象**</span>

应用程序的主要响应实例始终作为类属性$ this->响应。

><span id='log'>**日志对象**</span>

Logger类的一个实例可以作为类属性 $ this-> logger。

><span id='https'>**forceHTTPS**</span>

所有控制器都可以使用强制通过HTTPS访问方法的方便方法：

```php
if(!$this->request->isSecure())
{
    $this->forceHTTPS();
}
```

默认情况下，在支持HTTP Strict Transport Security标头的现代浏览器中，此调用应强制浏览器将非HTTPS调用转换为HTTPS调用一年。您可以通过将持续时间（以秒为单位）作为第一个参数进行修改：

```php
if(!$this->request->isSecure())
{
    $this->forceHTTPS(31536000);     //一年
}
```

**注意**

您可以使用许多基于时间的常量，包括YEAR，MONTH等。
助手
您可以将辅助文件数组定义为类属性。无论何时加载控制器，这些帮助文件将自动加载到内存中，以便您可以在控制器内的任何位置使用它们的方法：

```php

 MyController  extends\YP\Core\YP_Controller
{
    protected $helpers = [ 'url' ， 'form' ];
}
```

><span id='post'>**验证$ _POST数据**</span>

控制器还提供了一种方便的方法来使$ _POST数据更加简单一些，validate（）将当前请求作为第一个实例，要测试的规则数组作为第二个参数，以及可选地，定制的数组如果项目不通过，则显示错误消息。该验证库文档 对规则和信息阵列格式的详细信息，以及现有的规则：

```php
public  function  updateUser （int  $ userID ）
{
    if  （！ $ this - > validate （$ this - > request ， [
        'email'  =>  “required | is_unique [users.email，id，{ $ userID } ]” ，
        'name '  =>  'required | alpha_numeric_spaces'
    ]））
    {
        return  view （'users / update' ， [
            'errors'  =>  $ this - > errors
        ]）;
    }

    //如果成功，请在这里写业务代码...
}
```

如果您发现在配置文件中保留规则更简单，可以使用`Config\Validation.php`中定义的名称替换$ rules数组：

```php
public  function  updateUser （int  $ userID ）
{
    if  （！ $ this - > validate （$ this - > request ， 'userRules' ））
    {
        return  view （'users / update' ， [
            'errors'  =>  $ this - > errors
        ]）;
    }

    //如果成功，请在这里写业务代码...
}
```
注意

验证也可以在模型中自动处理。您处理有效期由您决定，您会发现控制器中的某些情况比之后的模型更简单，反之亦然。