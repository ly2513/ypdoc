# YP URL

**YP URL**

默认情况下，YP中的URL被设计为搜索引擎和人性化。YP使用基于谓词资源的方法，而不是使用标准的“查询字符串”方法与动态系统同义的URL，

```
example.com/Admin/User/addSession

example.com/Home/index
```
**URI语句**

URL中的语句与模型-视图-控制器方法相关，通常代表：
```
 example.com/directorie/class/method/ID

 example.com/class/method/ID

```

第一个段代表应该调用的控制器类。
第二个段代表应该调用的类方法。
第三个和任何其他段代表将被传递给控制器​​的ID和任何变量。
该URI图书馆和网址助手包含的功能，更方便地与你的URI的数据。此外，您可以使用URI路由功能重新映射您的URL，以获得更大的灵活性。

删除index.php文件
默认情况下，index.php文件将包含在您的URL中：

```url
example.com/index.php/Admin/User/addSession
```
如果您的服务器支持重写URL，您可以轻松地通过URL重写来隐藏该文件。这由不同的服务器处理不同，但我们将在这里显示两个最常见的Web服务器的示例。

**Apache Web服务器**

Apache必须启用mod_rewrite扩展。如果已开启，您可以使用.htaccess文件与一些简单的规则。有一个这样文件的例子，除了指定的项目之外，一切都被重定向：

```apache
RewriteEngine On
RewriteCond％{REQUEST_FILENAME}！-f
RewriteCond％{REQUEST_FILENAME}！-d
RewriteRule ^（。*）$ index.php / $ 1 [L]
```

在此示例中，除了存在目录和现有文件之外的任何HTTP请求都将被视为对您的index.php文件的请求。

**注意**

+ 这些具体规则可能不适用于所有服务器配置。

+ 确保从上述规则中排除您可能需要从外部世界访问的任何影响。

**NGINX**

在Nginx下，您可以定义一个位置块并使用try_files指令获得与上述Apache配置相同的效果：

```nginx
location / {
    try_files $uri $uri/ /index.php/$args;
}

```
这将首先查找与URI匹配的文件或目录（从根和别名指令的设置构建每个文件的完整路径），然后将该请求与任何参数一起发送到index.php文件。