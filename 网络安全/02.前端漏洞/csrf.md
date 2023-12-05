> Cross-Site Request Forgery，又称为跨站请求伪造

# Csrf基础
## 了解

### 删帖子 GET请求 
```html
<a href='https://small-min.blog.com/delete?id=3'>開始測驗</a>
```

```html
<img src='https://small-min.blog.com/delete?id=3' width='0' height='0' /> <a href='/test'>開始測驗</a>
```

### 删帖子 POST请求
把删除的功能做成POST，这样不就无法透过`<a>`或是`<img>`来攻击了吗？除非，有哪个HTML 元素可以发送POST request。
```html
<form action="https://small-min.blog.com/delete" method="POST"> <input type="hidden" name="id" value="3"/> <input type="submit" value="開始測驗"/> </form>
```


开一个看不见的iframe，让form submit 之后的结果出现在iframe 里面，而且这个form 还可以自动submit，完全不需要经过小明的任何操作。
```html
<iframe style="display:none" name="csrf-frame"></iframe> <form method='POST' action='https://small-min.blog.com/delete' target="csrf-frame" id="csrf-form"> <input type='hidden' name='id' value='3'> <input type='submit' value='submit'> </form> <script>document.getElementById("csrf-form").submit()</script>
```

### API JSON格式
以HTML 的form 来说，`enctype`只支援三种：
1. `application/x-www-form-urlencoded` ：大多数情况
2. `multipart/form-data`：  上传档案
3. `text/plain`

**补充**
1. 解析json通常content type为appliaction/json，但对一些服务器来说，如果request的content type不是appliaction/json，他会抛出错误。
2. 而错的那一半则是因为对另外一些伺服器来讲，只要body 内容是JSON 格式，就算content type 带 也是`text/plain`可以接受的，而JSON 格式的body 可以利用底下的表单拼出来：
```html
<form action="https://small-min.blog.com/delete" method="post" enctype="text/plain"> <input name='{"id":3, "ignore_me":"' value='test"}' type='hidden'> <input type="submit" value="delete!"/> </form>
```
`<form>`产生request body 的规则是`name=value`，所以上面的表单会产生的request body 是：

```js
{"id":3, "ignore_me":"=test"}
```

## 防御
> Cross-site -- 限制从别的来源发出的request
> csrf的request与用户的request的区别在于origin的不同

###  检查referer / origin header
1. 有些状况下可能不会带referer 或是origin，你就没东西可以检查了。
2. 有些使用者可能会关闭带referer 的功能，这时候你的伺服器就会拒绝掉由真的使用者发出的request。
3. 判定是不是合法origin 的程式码必须要保证没有bug，例如：
```html
const referer = request.headers.referer;
if (referer.indexOf('small-min.blog.com') > -1) { 
// pass 
}
```
如果攻击者的网页是`small-min.blog.com.attack.com`的话，你的检查就被绕过了。

所以，检查`referer`或是`origin`并不是一个很完善的解法。

### 图形验证码/短信验证码
重要操作可以，不然容易影响用户体验

### CSRF token
我们在form 里面加上一个隐藏的栏位，叫做`csrf_token`，这里面填的值由伺服器随机产生，每一次表单操作都应该产生一个新的，并且存在伺服器的session 资料中。

```html
<form action="https://small-min.blog.com/delete" method="POST">
  <input type="hidden" name="id" value="3"/>
  <input type="hidden" name="csrf_token" value="fj1iro2jro12ijoi1"/>
  <input type="submit" value="刪除文章"/>
</form>
```

按下送出之后，伺服器比对表单中的`csrf_token`与自己session 里面存的是不是一样的，是的话就代表这的确是由自己的网站发出的request。

### Double Submit Cookie
这个解法的前半段与刚刚的相似，由伺服器产生一组随机的token 并且加在form 上面。但不同的点在于，除了不用把这个值写在session 以外，同时也设定一个名叫 的`csrf_token`cookie，值也是同一组token。

```html
Set-Cookie: csrf_token=fj1iro2jro12ijoi1

<form action="https://small-min.blog.com/delete" method="POST">
  <input type="hidden" name="id" value="3"/>
  <input type="hidden" name="csrf_token" value="fj1iro2jro12ijoi1"/>
  <input type="submit" value="刪除文章"/>
</form>
```

当使用者按下送出的时候，伺服器比对cookie 内的`csrf_token`与form 里面的`csrf_token`，检查是否有值并且相等，就知道是不是网站发的了。

为什么这样可以防御呢？

假设现在攻击者想要发起攻击，根据前面讲的CSRF 原理，cookie 中的`csrf_token`会一起送到server，但是表单里面的`csrf_token`呢？攻击者在别的origin 底下看不到目标网站的cookie，更看不到表单内容，因此他不会知道正确的值是什么。

### 纯前端的Double Submit Cookie
那为什么由前端来产生这个token 也可以呢？因为这个token 本身的目的其实不包含任何资讯，只是为了「不让攻击者」猜出而已，所以由前端还是后来来产生都是一样的，只要确保不被猜出来即可。

Double Submit Cookie 的核心概念是：「攻击者的没办法读写目标网站的cookie，所以request 中的token 会跟cookie 内的不一样」，只要能满足这个条件，就能阻挡攻击。

### 不使用cookie做身份验证
前后端分离的架构，把前端跟后端完全切开，前端就只是一个静态网站，后端则只有提供纯资料的API，网页跟画面的显示百分之百交给前端负责。而前后端的网域通常也会分开，例如说前端在`https://huli.tw`，后端在`https://api.huli.tw`等等。

在这种架构下，比起传统的cookie-based 的身份验证，有更多网站会选择使用JWT 搭配HTTP header，把验证身份的token 存在浏览器的localStorage 里面，向后端发送request 时放在header`Authorization`中，像这样：

```
GET /me HTTP/1.1
Host: api.huli.tw
Authorization: Bearer {JWT_TOKEN}
```

像这种的验证方式就完全没有使用到cookie，因此这机制天生就对CSRF 免疫，不会有CSRF 的问题。

但缺点就是：例如说cookie 可以用`HttpOnly`这个属性让浏览器读取不到，让攻击者没办法直接偷到token，但是并`localStorage`没有类似的机制，一旦被XSS 攻击，攻击者就可以轻松把token 拿走。
### custom header

拿来使用的范例是表单跟图片，而这些送
出请求的方式不能带上HTTP header，因此前端在打API 的时候，可以带上一个之类的heaedr，如此`X-Version: web`一来后端就可以根据有没有这个header，辨识出这个请求是不是合法的。

虽然乍听之下没问题，但要小心的是我们刚刚才提过的CORS 设定。

除了表单或是图片，攻击者也可以利用fetch 直接发出一个跨站的请求，并且含有header：

```js
fetch(target, {
  method: 'POST',
  headers: {
    'X-Version': 'web'
  }
})
```

但是带有自订header 的请求是非简单请求，因此需要通过preflight request 的检查，才会真正发送出去。

## 案例
1. [[ GCP 2022 ] Few bugs in the google cloud shell](https://obmiblog.blogspot.com/2022/12/gcp-2022-few-bugs-in-google-cloud-shell.html)
2. [EmojiDeploy: Smile! Your Azure web service just got RCE'd ._.](https://ermetic.com/blog/azure/emojideploy-smile-your-azure-web-service-just-got-rced/)


## 引用
1. [day23 csrf 一点就通](https://ithelp.ithome.com.tw/articles/10325588)
2. [Example of silently submitting a POST FORM (CSRF)](http://stackoverflow.com/questions/17940811/example-of-silently-submitting-a-post-form-csrf)
3. [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)#Prevention_measures_that_do_NOT_work)
4. [Cross-Site Request Forgery (CSRF) Prevention Cheat Sheet](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet)
5. [一次较为深刻的CSRF认识](http://m.2cto.com/article/201505/400902.html)
6. [[技术分享] Cross-site Request Forgery (Part 2)](http://cyrilwang.pixnet.net/blog/post/31813672)
7. [Spring Security Reference](http://docs.spring.io/spring-security/site/docs/3.2.5.RELEASE/reference/htmlsingle/#csrf)
8. [CSRF 攻击的应对之道](https://www.ibm.com/developerworks/cn/web/1102_niugang_csrf/)

# Same-site cookie Csrf的救星

