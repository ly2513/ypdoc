#服务

`YP`中的为所有类都被提供“服务”。这仅仅意味着，代替对类名进行硬编码以加载，要调用的类在非常简单的配置文件中定义。该文件作为一种工厂类型来创建所需类的新实例。

一个简单的例子可能会使事情变得更清楚，所以想象你需要拉入一个Timer类的实例。最简单的方法就是创建一个新的类的实例：

```php
$timer = new  \YP\Debug\Timer();

```
这样做很棒。也许这个有一些默认不提供的高级报告定时器。为了做到这一点，您现在必须找到应用程序中使用了timer类的所有位置。由于您可能已将其保留在适当位置，以使应用程序的性能日志不断运行，这可能是一个耗时且容易出错的方法来处理此问题。那服务就派上用场了。

我们不用创建实例，而是让一个中心类为我们创建一个类的实例。这个类保持很简单。它仅包含我们要用作服务的每个类的一个方法。该方法通常返回该类的共享实例，传递它可能具有的任何依赖关系。然后，我们将使用调用此新类的代码替换定时器创建代码：

```php
$timer = \Config\Services::timer();

```
当需要更改所使用的实现时，您可以修改服务配置文件，并且在整个应用程序中自动进行更改，而无需执行任何操作。现在你只需要利用任何新的功能，你很好。非常简单和抗错误。

**注意**

建议只在控制器内创建服务。其他文件（如模型和库）应具有将依赖关系传递给构造方法。

**便利功能**

为了获得这些功能始终可用,服务提供了两项功能。。

第一个是service()，它返回所请求服务的新实例。唯一必需的参数是服务的名称。这与服务文件中的方法名称相同：
 ```php
$ogger = service('logger');
```
如果创建方法需要附加参数，则可以在服务名称后面传递它们。

第二个函数`shared_service()`与`service()`一样工作，但返回所需服务的共享实例：

```php
$log = shared_service('log');
```

**定义服务**

为了使服务正常工作，你使用必须要能够依靠每个类具有固定API或接口。几乎所有的`YP`类都提供了一个他们坚持的接口。当您要扩展或替换核心类时，您只需要确保满足接口的要求，并且知道类是兼容的。

例如，`YP_Filter`类实现了`YP_FilterInterface`。当你想创建一个替代品，提供了不同的方式来创建的路线，你只需要创建一个实现一个新的类`YP_FilterInterface`：

```php
class MY_Filter  YP\Core\YP_FilterInterface
{
    //这里实现的必要方法。
}
```
最后，修改`/app/Config/Services.php`创建的新实例`MY_Filter` 而不是`YP\Core\YP_Filter`：

```php
public static function filters()
{
    return  new  \App\Core\MY_Filter();
}
```

**共享类**

有时您需要要求只创建一个服务实例。这可以通过从工厂方法中调用的`getSharedInstance()`方法轻松处理。这样可以检查是否已经在类中创建并保存了一个实例，如果没有，则创建一个新的实例。所有的工厂方法都提供`$getShared = true`作为最后一个参数。你应该坚持的方法：

```php
class  Services
{
    public static function routes($getShared = false)
    {
        if(!$getShared) {
            return new \YP\Core\RouteCollection();
        }

        return self::getSharedInstance('routes');
    }
}
```

