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
    + [删除数据](#delete)
    + [原声语句执行](#original)
    + [事务](#affair)



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

`Eloquent` 中`where`使用,如果是对某个字段进行条件筛选,有以下两种常用的写法
```php
// 筛选出ID为5的数据
$result = DiamondInlayModel::whereId(5)->get()->toArray();
// 或者
$result = DiamondInlayModel::where('id', 5)->get()->toArray();

```

如果搜索条件是个范围,例如筛选ID大于等于10的记录
```php
// 筛选出ID大于等于10的数据
$result = DiamondInlayModel::where('id', '>=', 10)->get()->toArray();
```

如果在某个数组范围的条件, 例如筛选出ID在数组array(3,6,10,40)的记录,采用`whereIn`
```php
// 筛选出ID为3、6、10、40的数据
$result = DiamondInlayModel::whereIn('id', [3, 6, 10, 40])->get()->toArray();
```

`where and `且条件和`where or`或条件在Eloquent中的写法如下
```php
// 筛选出ID大于等于10且用户为3的数据, 用户的ID的数据库字段create_by
$result = DiamondInlayModel::where('id', '>=', 10)->whereCreateBy(3)->get()->toArray();
// 或
$result = DiamondInlayModel::where('id', '>=', 10)->where('create_by', 3)->get()->toArray();
```
或条件使用**whereOr()**
```php
// 筛选出ID大于等于10或用户为3的数据, 用户的ID的数据库字段create_by
$result = DiamondInlayModel::where('id', '>=', 10)->whereOr('create_by', 3)->get()->toArray();
```

><span id='like'>**模糊搜索**</span>

Eloquent中**like()**的使用,

模糊搜索名称为 "18K心形" 的钻饰
```php
// 搜索18K心形的钻饰
$result = DiamondInlayModel::like('name', '%18K心形%')->get()->toArray();

// 搜索名称以18K心形开头的钻饰
$result = DiamondInlayModel::like('name', '18K心形%')->get()->toArray();

// 搜索名称以18K心形结尾的钻饰
$result = DiamondInlayModel::like('name', '%18K心形')->get()->toArray();
```
><span id='group'>**分组**</span>

采用**groupBy()**进行分组,例如: 根据商品的状态分组,例如:
```php
// 根据商品的状态分组
$result = DiamondInlayModel::select('*')->groupBy('status')->get()->toArray();
```

><span id='order'>**排序**</span>

Eloquent中使用**orderBy()**对结果集进行排序,例如:
```php
// 根据商品的添加时间倒序排
$result = DiamondInlayModel::select('*')->orderBy('create_time', 'desc')->get()->toArray();

// 根据商品的添加时间升序排
$result = DiamondInlayModel::select('*')->orderBy('create_time', 'asc')->get()->toArray();
```

><span id='limit'>**分页与计数**</span>

在应用中通常我们使用的数据库软件是mysql, 我常用`limit`进行分页处理, 在Eloquent中,则采用`skip()`方法和`take()`方法进行分页处理。例如:
```php
// 对商品数据进行每页显示15条处理
$result = DiamondInlayModel::select('*')->skip(('当前页码' - 1 ) * 15)->take(15)->get()->toArray();
```

如需对查出的记录进行统计, 可以使用`count()`方法进行统计结果数量。例如:
```php
// 对商品数据进行每页显示15条处理
$number = DiamondInlayModel::select('*')->skip(('当前页码' - 1 ) * 15)->take(15)->count();
```

><span id='insert'>**插入数据**</span>

插入数据可以分为单条插入和批量插入两种, 分别通过`insertGetId()`方法和`insert()`方法实现。例如:
```php
// 插入的数据
$data = ['name1', 'status1', 'create_by1', 'create_time1', ... ];
// 插入一条商品数据, 操作成功后, 该方法将返回自增ID, 失败返回false
$number = DiamondInlayModel::insertGetId($data);


// 批量插入的数据
$data = [
    ['name1', 'status1', 'create_by1', 'create_time1', ... ],
    ['name2', 'status2', 'create_by2', 'create_time2', ... ],
    ...
];

// 批量插入多条商品数据, 操作成功后, 该方法将返回true, 失败返回false
$number = DiamondInlayModel::insert($data);

```

><span id='update'>**更新数据**</span>

更新数据同样分单条更新和多条更新, 分布通过`update()`方法和`batchUpdate()`方法实现。例如
```php
// 更新的数据
$data = [2, 'name1', 'status1', 'create_by1', 'create_time1', ... ];
// 更新一条商品数据, 操作成功后, 该方法将返回true, 失败返回false
$number = DiamondInlayModel::whereId(2)->insertGetId($data);

// 批量更新的数据
$data = [
    [2, 'name1', 'status1', 'create_by1', 'create_time1', ... ],
    [3, 'name2', 'status2', 'create_by2', 'create_time2', ... ],
    ...
];

// 批量更新多条商品数据, 操作成功后, 该方法将返回true, 失败返回false
$number = DiamondInlayModel::batchUpdate($data);

```

><span id='delete'>**删除数据**</span>

删除数据使用`update()`方法对数据进行软删除的。例如:
```php
    // 删除ID为4的商品数据
    $status = DiamondInlayModel::whereId(4)->update['is_delete' => 1];
```

以上关键字查询都可以进行链式操作,只是在讲解时方便对某个关键字方法理解演示, 没有采用链式操作。

><span id='original'>**原声语句执行**</span>

在开发的应用过程中, 或多或少需要执行一些复杂的sql语句,如果采用Eloquent去拼复杂的SQL时, Eloquent使用起来就显得特别复杂, 这时我们会更多的选择使用原声SQL语句。
那么在框架中怎么执行原声SQL语句了, 通常框架给出两种不同方式的来实现原声SQL的执行。以下是实现的代码:
```php
// 数据库模型
use Illuminate\Database\Capsule\Manager as DB;

// 订单ID
$orderIds = [3, 4, 73];
// 原声SQL的查询语句
$sql = 'SELECT ga.name, ga.image , rog.order_id FROM zb_retail_order_goods AS rog  LEFT JOIN zb_retail_goods_all AS ga ON rog.gid = ga.goods_id AND ga.type = rog.goods_type WHERE rog.order_id in ( ' . $orderIds . ')';
// 结果集
$result = DB::select($sql);

// 原声插入SQL语句
DB::insert('insert into users (id, name) values (?, ?)', [1, 'Dayle']);

// 原声更新SQL语句
$affected = DB::update('update users set votes = 100 where name = ?', ['John']);

// 原声的删除SQL语句(物理删除)
$deleted = DB::delete('delete from users');

```

><span id='affair'>**事务**</span>

现在的应用的处理数据常常一个操作需要同时对多个数据表的数据进行处理, 为保证操作的数据都操作成功,这样数据的关联任何时候都是一致的。这样的场景下需要用到事务,
目前mysql数据库软件常用的事务存储引擎是`innoDB`。所以我们在设计数据库的时候一般会选择`innoDB`作为数据库的存储引擎。
那么问题来了,在YP框架中怎么使用事务了? 框架给出了两种方式实现事务,一种是原声的model,一种是Eloquent实现,代码如下:
```php
$build = DiamondInlayModel::select();
// 开启事务
$build->getConnection()->beginTransaction();

// 提交事务
$build->getConnection()->commit();

// 回滚事务
$build->getConnection()->rollBack();

```

第二种方式如下:
```php
// 数据库模型
use Illuminate\Database\Capsule\Manager as DB;

// 开启事务
DB::beginTransaction();

// 提交事务
DB::commit();

// 事务回滚
DB::rollBack();

```












