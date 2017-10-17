# Model

YP框架采用的ORM是Laravel的Eloquent,现在讲解下Eloquent。

Model

+ [什么是Eloquent](#jianjie)
+ [定义模型与约定](#defind)
+ [基本用法](#use)
    + [查询](#select)
    + [搜索条件](#where)
    + [模糊搜索](#like)
    + [分组](#group)
    + [排序](#order)
    + [分页与计数](#limit)
    + [插入数据](#insert)
    + [更新数据](#update)
+ [获取单个模型或者集合](#getmodel)
    + [取回集合](#getaggregate)



><span id='jianjie'>**什么是Eloquent？**</span>

**Eloquent** 是 Laravel 的 'ORM'，即 'Object Relational Mapping'，对象关系映射。ORM 的出现是为了帮我们把对数据库的操作变得更加地方便。
Eloquent 让一个 'Model类' 对应一张数据库表，并且在底层封装了很多 'function'，可以让 Model 类非常方便地调用。在YP框架的models目录(app/models)下创建 'Article.php' 文件,以这模型为例大致说明一下。这个文件的代码如下：

```php
/**
 * User: yongli
 * Date: 17/6/11
 * Time: 13:44
 * Email: yong.li@szypwl.com
 * Copyright: 深圳优品未来科技有限公司
 */
use YP\Core\YP_Model as Model;

/**
 * Class ArticleModel
 */
class ArticleModel extends Model
{
    // 表名
    protected $table = 'blog_article';

}
```
`protected $table = 'blog_article'`这行设置是该模型操作的表。
这个类简直再简单不过了，没有指定命名空间，没有构造函数，这个文件就只有两个有实际意义的东西： `ArticleModel` 和 `Model`。没错，Eloquent 就是这么屌炸天，只需要继承一下 Model 类，就可以干 `first() find() where() orderBy()` 等非常非常多的事情，这就是面向对象的强大威力。

><span id='defind'>**定义模型与约定**</span>

在YP框架中,我们将应用中的模型文件都放在`app/models`目录下,模型命名采用首字母大写驼峰命名 + `Model`如(ArticleModel),模型的基类是`YP\Core\YP_Model`,也就是说所有的模型定义时都需要继承`YP_Model`这个基类。
模型文件比较简单,一般模型文件只需要定义一个该模型所需要的操作的表就可以使用该模型了,代码很少,如果需要关联其他表查询,则还需要定义表之间的关系方法。接下来我们来看下,怎么定义一个模型
```php
// 钻饰产品模型
class DiamondInlayModel extends \YP\Core\YP_Model
{
    // 定义操作的表名
    protected $table = 'zb_retail_inlay_diamond';

    /**
     * 定义商品与商品分类一对一关系
     */
    public function hasOneCategory()
    {
        return $this->hasOne('\Goods\CategoryModel','id','cid');
    }

    /**
     * 定义商品与库存一对多关系
     */
    public function getProduct()
    {
        return $this->hasMany('\Goods\ProductModel','id','gid');
    }

}
```
从上面可看出,定义一对一的关系需调用`Eloquent`中的`hasOne`方法,而一对多的关系调用的是`hasMany`方法。以上就是一个模型的定义,接下来讲解怎么去使用这个模型了。

><span id='use'>**基本用法**</span>

YP_Model继承了Eloquent,在YP框架中,我们在控制器中去使用模型,通过命名空间的方式将模型引用到控制器中,我们分Sql语句中关键字的的顺序来讲解其用法。

><span id='select'>**查询**</span>

查询是我们用的最多的,那么在我们是怎么使用的了,首先我们将模型引用到控制器中,我们还是以钻饰模型为例,以下是控制器`DiamondInlay`代码
```php
namespace App\Controllers\Goods;

use App\Controllers\Admin\Auth;
use DiamondInlayModel;

/**
 * 钻饰货品管理
 *
 * Class DiamondInlay
 *
 * @package App\Controllers\Goods
 */
class DiamondInlay extends Auth
{
    // 查询表中所有的字段
    $result = DiamondInlayModel::select('*')->get()->toArray();
    // 或者
    $result = DiamondInlayModel::get()->toArray();

    // 查询表中部分字段
    $result = DiamondInlayModel::select(['字段1', '字段2', ...])->get()->toArray();
}
```

><span id='where'>**搜索条件**</span>

`Eloquent` 中`where`使用,如果是对某个字段进行条件筛选,有以下两种写法
```php
    // 筛选出ID为5的数据
    $result = DiamondInlayModel::whereId(5)->get()->toArray();
    // 或者
    $result = DiamondInlayModel::where('id', 5)->get()->toArray();

```

