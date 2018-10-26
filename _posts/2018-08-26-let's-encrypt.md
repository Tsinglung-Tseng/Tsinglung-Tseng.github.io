---
title: let's encrypt
key: 20180826
tags: 法器集合
---

## HTTPS 那些事
腾讯Bugly有个很棒的讲解，看[这里](https://segmentfault.com/a/1190000004199917)

### HTTPS 基础：
![yo](http://i.imgur.com/0Mynsnm.gif)

### TLS/SSL 原理：
* 对称加密 
    * 同key加密同key解  
    * 信息传输1对1，需要共享相同的密码，密码的安全是保证信息安全的基础
    * 服务器和 N 个客户端通信，需要维持 N 个密码记录，且缺少修改密码的机制
* 非对称加密 
    * 私钥加密公钥解，公钥加密私钥解
    * 信息传输1对多，服务器只需要维持一个私钥就能够和多个客户端进行加密通信
    * 但服务器发出的信息能够被所有的客户端解密，且该算法的计算复杂，加密速度慢
* 散列 
    * 不能单独实现防篡改（因为明文传输，中间人可以修改信息之后重新计算信息摘要）
    * 根据散列值无法还原出原始输入数据，所以单向散列函数不是加密

因此：
![yo](http://i.imgur.com/2snzFs9.gif)

## PKI体系
### RSA 身份验证的隐患
![](http://i.imgur.com/69EUIf1.png)

### 身份验证-CA 和证书
![](http://i.imgur.com/cMUd921.png)

## Let's Encrypt

