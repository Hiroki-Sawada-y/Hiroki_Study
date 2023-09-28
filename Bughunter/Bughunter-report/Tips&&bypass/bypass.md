## WAF Cloudflare bypass leads to First bounty 🤑🤑
#Cloudflare #bypass 
### Summary
> 使用IP地址作为解析器，而不是使用目标地址，从而规避或绕过Cloudflare WAF的安全措施
### Step
1. wapplyzer 分析网站是否使用cloudflare
2. ping website
3. browser输入ip地址不让使用
4. shodan.io       hostname: xxx.com
5. 75💵
### Impact
Cloudflare bypasses can have a significant impact as any adversary is now able to communicate with the origin server directly enabling them to perform unfiltered attacks (such as denial-of-service) and data retrieval
