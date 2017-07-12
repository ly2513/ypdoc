
## HTTP请求
为了充分利用YP框架，您需要对HTTP请求和响应的工作原理有一个基本的了解。由于这是您在开发Web应用程序时所使用的工具，因此理解HTTP背后的概念对于所有开发人员想要顺利成功的去开发来说都是必须的。

本章的第一部分给出了一个概述。在概念出来之后，我们将讨论如何使用YP框架中的请求和响应。

**什么是HTTP？**

HTTP只是一种基于文本的语言，允许两台机器相互通信。当浏览器请求页面时，它会询问服务器是否可以获取页面。然后，服务器准备该页面并将响应发回给要求的浏览器。您可以使用一些复杂性，但基本原理显然是一样的。

HTTP是用于描述该语言的术语。它代表超文本传输​​协议。开发Web应用程序时的目标是始终了解浏览器的请求，并能够适当地进行响应。

**请求**

每当客户端发出请求（网络浏览器，智能手机应用程序等）时，它会向服务器发送一个小文本消息并等待响应。

请求将如下所示：
```
GET / HTTP/1.1
Host 127.0.0.1:1025
Accept: text/html
User-Agent: Chrome/46.0.2490.80
```
此信息显示知道客户端请求的所有必要信息。它告诉请求的方法（GET，POST，DELETE等）及其支持的HTTP版本。

该请求还包括一些可选的请求头，其可以包含各种各样的信息，例如客户端希望显示的内容是什么语言，客户端接受的格式的类型等等。如果你想查看，维基百科有一篇文章，列出[所有标题字段](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)。

**响应**

一旦服务器收到请求，您的应用程序将获取该信息并生成一些输出。服务器将捆绑您的输出作为响应的一部分到客户端。这也表示为一个简单的通信，看起来像这样：

```
HTTP/1.1 200 OK
Server: nginx/1.10.0
Date: Thu, 05 Nov 2015 05:33:22 GMT
Content-Type: text/html; charset=UTF-8

< html >
        。 。 。
</ html >
```
响应告诉客户它正在使用哪个版本的HTTP规范，最重要的是状态码（200）。状态代码是已经被标准化对客户端具有非常具体含义的许多代码之一。这可以告诉他们这是成功的（200），或者没有找到该页面（404）。[在这](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml)你可以获取HTTP状态代码的完整列表。

**使用请求和响应**

虽然PHP提供了与请求和响应标头交互的方法，但是和大多数框架一样，YP框架抽象出它们，以使它们具有一致的简单接口。所述`YP_IncomingRequest`类是HTTP请求的一种面向对象的表示。它提供您需要的一切：

```
use YP\Core\YP_IncomingRequest as IncomingRequest;

$request = new IncomingRequest(new \Config\App(), new \YP\Core\YP_Uri()）;

// 获得请求的Uri
$request->uri->getPath （）;

// 获得$_REQUEST['foo'] 、$_GET['foo']和$_POST['foo'] 值
$request->getVar('foo');
$request->getGet('foo');
$request->getPost('foo');

//通过AJAX调用,返回JSON
$ request->getJSON();

//获得服务器变量($_SERVER)
$request->getServer('Host');

//检索HTTP请求头，使用不区分大小写的名称
$request->getHeader('host');
$request->getHeader('Content-Type');

// 获得请求方法(GET，POST，PUT等)
$request->getMethod();
```

YP还提供了一个`YP_Response`类，它是HTTP响应的面向对象表示形式。这给您一个简单而强大的方式来构建您对客户的回应：

```
use YP\Core\YP_Response as Response;

$response = new Response();

$response->setStatusCode(Response::HTTP_OK);
$ response->setBody （$ output ）;
$ response->setHeader('Content-type', 'text/html');
$ response->noCache();

//将输出发送到浏览器
$response->send();

```
此外，Response类允许您使用HTTP缓存层获得最佳性能。