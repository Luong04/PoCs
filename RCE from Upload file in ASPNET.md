# RCE from Upload file in ASP.NET
This is my first writeup, hope you like it!
Last week, I received a task finding RCE vulns for Redteam ops. Object is a large system provided transport services - V(I don't want to reveal).
## Recon
After recon some subdomains of V, I define some subdomains which has potential to exploit, but it require login credential. So I used google dorking and found an internal document containing username and default password for another subdomain. However, I still use those usernames to login this side because the usernames are typically the company's email account. About passwords, I make a password lists from information I have gathered and default password. This web allows performing bruteforce and not encrypt username and password request :), so it's not too difficult to find some valid account.
## Find potential endpoints.
This is a management website, so there are quite many functions. But as I mentioned before, I have to find RCE vulns, so I focus on endpoints may raise upload insecure file, SSTI or deserialization. After some attempts, I found they use WAF of a third party (v*****l) so every SSTI and deserialize effort I tried was not bring positive results. My last hope put on Upload files, and certainly, it prevents a lot of files, including dynamic file and (txt, html,...).
![Client Side detect](/assets/client-side-detect.png)
![UI](/assets/client.jpg)
## Bypass
It's seem that there is a check at client-side, so I upload a png file - ....png and used Burpsuite to Intercept request, changed it name to ....aspx:
![WAF detect](/assets/waf.jpg)
As I expected, WAF detect malicious files, it retrieved response with header 301 (cloudrity). So I tried some cases may bypass it, such as Double Extension Attack, null byte, bypass content-type,... and it works with this payload:
![Server handle](/assets/code.jpg "San Juan Mountains")
A bit dissapointed :), I almost no hope for this situation, after discussion with other guys and research, I found code allow upload config file, So I push this web.config file to allow upload aspx file (webshell).
We can access the file uploaded by this path:
![Upload web.config file](/assets/webconfig.jpg)
![Upload web.config file](/assets/success.jpg)

## Persistant
Execute and compile payload by webshell => memshell 
