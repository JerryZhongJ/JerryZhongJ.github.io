---
title: CDN 加速
---

## CDN
CDN是内容分发网络, 相当于对内容进行缓存, 并将内容分发到距离用户较近的边缘服务器. 当用户访问内容时, 就可获得较快的访问速度, 减少拥塞. [^definition]

[^definition]: <https://baike.baidu.com/item/CDN#4>

### 工作原理

#### 传统访问过程

一台主机的域名解析工作由一台本地的DNS服务器代理完成, 叫做**Local DNS**.
1. 主机向Local DNS发送请求.
2. Local DNS首先向Root DNS发送请求, Root DNS回复授权DNS服务器以及该IP
3. 接着Local DNS再向授权DNS服务器发送请求. 
4. Local DNS不断重复此过程, 直到解析到域名的IP.
5. 接着Local DNS才把解析到的IP返回给主机

#### CDN加速的访问过程

1. 主机向Local DNS请求解析某个域名`a`
2. Local DNS首先向Root DNS发送请求, Root DNS回复授权DNS服务器以及该IP.
3. 接着Local DNS再向授权DNS服务器发送请求. 但此时授权DNS一般返回一个CNAME记录, 表明`a`是`b`的一个别名, 而`b`是cdn的一个域名. 
4. Local DNS向CDN的调度服务器请求解析`b`, 而调度服务器会根据Local DNS的位置等信息回复一个最佳节点的IP.
5. Local DNS将IP返回给主机, 主机跟着IP访问到了CDN节点上的内容.


### 回源 
[^backtosource]

[^backtosource]: <https://cloud.tencent.com/developer/article/1439913>
回源(back to source)是指在用户发送请求报文后, 响应请求的是源站, 而不是缓存服务器. 

一般的CDN都是回源的, 即当缓存服务器没有内容, 或内容已到期, 那么服务器会去源站拉取内容. 如果没有人访问, 缓存服务器不会主动拉取内容.

源站也可以主动推送内容到CDN节点

**协议回源**: 回源使用的协议与用户访问时的协议一致. 若用户以`HTTPS`访问, 那么缓存服务器也以`HTTPS`协议拉取内容, 而不会返回以`HTTP`拉取的内容(假如有的话).

### CNAME

`a`的CNAME记录`b`, 表示`a`是`b`的别名. 

若把CNAME简单地记作"别名"的话, 容易理解成`a`的别名是`b`, 但其实不对.

因此记住, **被解析的**是别名.[^cname]

[^cname]: <https://zh.wikipedia.org/wiki/CNAME%E8%AE%B0%E5%BD%95#%E8%AF%AF%E4%BC%9A>

## 域名
### 域名注册
[^register]

[^register]: <cloudflare.com/zh-cn/learning/dns/glossary/what-is-a-domain-name-registrar/>

区分几个机构: 域名注册商, 域名管理机构, ICANN的IANA

IANA: 管理域名的最高机构, 但只负责管理根域名服务器, 和一些争议性域名

域名管理机构: 负责管理顶级域, 受IANA管理.

域名注册商: 注册管理机构将域名注册的商业销售委托给注册商. 注册商并不实际管理和维护域名, 它只是域名的经销商.

注意: 用户只是在租用域名, 并不实际拥有它. 

域名注册

![](https://www.cloudflare.com/img/learning/dns/glossary/what-is-a-domain-name-registrar/registrar-flow.png)

### 域名转移
### 域名接入与管理
### 域名备案

可备案的定义域名, 参见<http://xn--eqrt2g.xn--vuq861b/>
### DNS域名解析服务商



## Github Pages + jdDelivr

### JsDelivr