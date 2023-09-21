## [什么是GraphQL?](https://portswigger.net/web-security/graphql/what-is-graphql)
> GraphQL是一种API查询语言,GraphQL服务定义了一个合约，客户端可以通过它与服务器进行通信。客户端不需要知道数据驻留在哪里。相反，客户端向GraphQL服务器发送查询，该服务器从相关位置获取数据。
### schema
```GraphQL
#Example schema definition 

type Product { 
	id: ID! 
	name: String! 
	description: String! 
	price: Int 
	}


```

### query
```graphql
#Example query 

query myGetProductQuery { 
	getProduct(id: 123) 
		{ 
		name description 
		} 
		}
```

  

### mutations
```
  #Example mutation request

    mutation {
        createProduct(name: "Flamin' Cocktail Glasses", listed: "yes") {
            id
            name
            listed
        }
    }

```

```
    #Example mutation response

    {
        "data": {
            "createProduct": {
                "id": 123,
                "name": "Flamin' Cocktail Glasses",
                "listed": "yes"
            }
        }
    }

```


## 扩展

### 工具
1. https://github.com/2fd/graphdoc【生成文档】
2. https://github.com/graphql/graphql-playground【生成文档】
3. https://github.com/skevy/graphiql-app【生成文档】
4. https://github.com/graphql-kit/graphql-voyager【生成文档】
5. https://chrome.google.com/webstore/detail/chromeiql/fkkiamalmpiidkljmicmjfbieiclmeij【生成文档】
6. https://github.com/nikitastupin/clairvoyance【即使禁用了内省，也可以获取GraphQL API模式】
插件
7. https://github.com/doyensec/inql【Burp Suite扩展】
8. https://github.com/dolevf/graphw00f【GraphQL Server指纹识别】
9. https://github.com/assetnote/batchql【GraphQL安全审计脚本，重点是执行批量GraphQL查询和变化】
10. https://github.com/swisskyrepo/GraphQLmap【GraphQLmap是一个脚本引擎，用于与graphql端点进行交互以进行渗透测试。】
11. https://github.com/nicholasaleks/CrackQL【CrackQL是一个GraphQL密码暴力破解和模糊实用程序。】

![](media/graphlql-bug-money.png)

### Paper
[# GraphQL API vulnerabilities](https://portswigger.net/web-security/graphql)
[# KCon 2022 议题分享：自动化 API 漏洞挖掘](https://paper.seebug.org/1964/)
[# hackone报告](https://github.com/reddelexc/hackerone-reports/blob/master/tops_by_bug_type/TOPGRAPHQL.md)