# Twig模板引擎

**YP**框架采用**Twig**作为模板引擎组件，之前用过的大部分框架采用**Smarty**模板引擎，**Smarty**模板引擎与**Twig**模板引擎相比，具有以下**不足之处**

+ 公共部分内容越加越多了，不需要用的js、css在一些页面也被强制引进来了
+ 新页面的css只能写在网页的body内，看起来总让人不爽。
+ 左侧、头部、尾部若有特殊显示，操作起来不方便，只能在公共地方去做判断了。
基于以上**Smarty**模板引擎不足之处的几点，YP框架在开发之初就选用**Twig**模板引擎作为模板渲染的组件。

**Twig模板引擎**具有"类继承的思想"，公用的资源放在'基类'布局页面中，子页面只需要继承"基类"布局页面就可以实现整个页面的渲染。
为了方便大家使用Twig模板引擎,在YP框架中使用Twig跟在其他框架使用Smarty模板引擎一样。分配变量到模板采用`$this->assign('变量名称'，变量)`方法，模板渲染采用`$this->display()`方法进行模板渲染。

>实例说明

首先我们来看看一个应用的例子，以下是一个“基类”页面的布局

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    {% block head %}
    <title>优品框架</title>
    <link href="/Static/css/bootstrap.min.css" rel="stylesheet">
    <link href="/Static/font-awesome/css/font-awesome.css" rel="stylesheet">
    <script src="/Static/js/jquery-2.1.1.js"></script>
    {% endblock %}
</head>

<body>
<div id="wrapper">
    {% block left %}
    <nav class="navbar-default navbar-static-side" role="navigation">
        <div class="sidebar-collapse">
            <ul class="nav metismenu" id="side-menu">
                <li {% if c in ['Brand', 'Category' ,'Attribute', 'Style'] %} class="active" {% endif%}>
                    <a href="#"><i class="fa fa-th-large"></i> <span class="nav-label">货品信息</span> <span
                            class="fa arrow"></span></a>
                    <ul class="nav nav-second-level collapse {% if c in ['Brand', 'Category' ,'Attribute', 'Style'] %} in {% endif %}" >
                        <li {% if c =='Brand' %} class="active" {% endif %}><a href="/Goods/Brand/getBrand">品牌管理</a></li>
                        <li {% if c =='Category' %} class="active" {% endif %}><a href="/Goods/Category/getCategory">类别管理</a></li>
                        <li {% if c =='Attribute' %} class="active" {% endif %}><a href="/Goods/Attribute/getAttribute">属性管理</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </nav>
    {% endblock %}
    <div id="page-wrapper" class="gray-bg">
        {% block top %}
        <div class="row border-bottom">
            <nav class="navbar navbar-static-top" role="navigation" style="margin-bottom: 0">
                <div class="navbar-header">
                    <a class="navbar-minimalize minimalize-styl-2 btn btn-primary " href="#"><i class="fa fa-bars"></i>
                    </a>
                </div>
                <ul class="nav navbar-top-links navbar-right">
                    <li>
                        <span class="m-r-sm text-muted welcome-message">欢迎YP框架工具包。</span>
                    </li>
                    <li>
                        <a href="login.html">
                            <i class="fa fa-sign-out"></i> 退出
                        </a>
                    </li>
                </ul>
            </nav>
        </div>
    	{% endblock %}
        {% block content %}
        {% endblock %}
        {% block footer%}
        <div class="footer">
            <div>
                <strong>Copyright</strong> 深圳优品未来科技有限公司 &copy; 2014-2017
            </div>
        </div>
        {% endblock %}
    </div>
</div>
{% block script %}

# 写javascript脚本代码 
{% endblock %}

</body>
</html>

```
上述布局中你将发现这样的标签代码`{% head block %}`和`{% endblock %}`,他们定义一个布局模块。所有的子页面只要继承这`基类`页面就可以渲染整个页码。

以下就是继承代码例子

```html
// 继承基类的模板
{% extends "Admin/Public/base.html" %}
{% block head %}
# 子页面需要额外加的头部信息
<link href="/Static/css/plugins/footable/footable.core.css" rel="stylesheet">
// 继承'基类'页面的头部信息
{{parent()}}
{% endblock %}

{% block content %}

# 写每个页码需要填充的内容
{% endblock %}

{% block script %}
 # 写javascript脚本代码

 // 继承'基类'页面的javascript脚本代码
{{ parent()}}
{% endblock %}

```
如果你的子页面只需要改变内容，也就是头部、尾部、左侧的导航共用'基类'布局的话，只需要在子页面中加入如下代码

```html
// 继承基类的模板
{% extends "Admin/Public/base.html" %}

{% block content %}

# 写每个页码需要填充的内容
{% endblock %}

```
是不是很简单啊，接下来讲几个常用的标签使用

>FOR标签

语法：

```
{% for k,v in data %}   
	// 处理循环的业务
{% endfor %}
```
k代表下标，v代表值，data代表要循环的数组对象

>IF标签

语法：
```
{% if a == b %}

{% elseif b==c %}

{% else %}

{%  endif %}
```
在此就不详细讲解其他标签了，想要了解更多标签的使用点击[这里](https://twig.symfony.com/doc/2.x/)查看官方文档




