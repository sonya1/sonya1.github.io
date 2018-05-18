---
layout: post
title: ssh协议解析
category: 后台
tags: c语言 ssh
description: 
---

根据TCP头部的源端口、目的端口是否为22，来判断是否是SSH协议报文。

-1.	版本号
 
![图](https://sonya1.github.io/assets/img/blog/ssh_1.png)

过滤掉tcp头部之后，判断是否含有“SSH”，取“SSH-”后面的三位即为版本号。

-2.	加密方式
 
![图](https://sonya1.github.io/assets/img/blog/ssh_2.png)

不含“SSH”字符的SSH报文为算法协商或密钥交换报文

```C
struct ssh_head
{
    unsigned int pckLen;  //32位Packet Length  一定有（除了版本协商）
    unsigned char padLen;     //8位Padding Length
    unsigned char msgCode;  //8位Msg code
};

```

根据message code来判断是算法协商还是密钥交换。
Msgcode=20：算法协商。格式见下。
 
![图](https://sonya1.github.io/assets/img/blog/ssh_3.png)

Algorithm的结构体：

```C
struct algorithms{
   char cookie[16];   //16字节 cookie
   unsigned int kexLen;   //32位 kex_length
};

```

获得kex_algorithms length的长度，截取kex_algorithms string。
接着获取4字节的server_host_key_algorithms length的长度，截取server_host_key_algorithms string。
接着获取4字节的encryption_algorithms_client_to_server length的长度，截取encryption_algorithms_client_to_server string，即为加密算法。

-3.	密钥交换信息
Msgcode=30/31/32/33/34五种情况为密钥交换报文。
(1)	Msgcode=30或32的时候，交换信息结构相同，截图如下。

![图](https://sonya1.github.io/assets/img/blog/ssh_4.png)

![图](https://sonya1.github.io/assets/img/blog/ssh_5.png)

先获取4字节的Multi Precision Integer Length的值，然后输出DH client e的值。

(2)	Msgcode=31的时候，交换信息结构不相同，截图如下（Centos和redhat不一样）。所以msgcode=31的包，只输入一个包时，无法进行解析。要通过它前面包的状态进行解析才可以。前面的包msgcode=30时，31的包的结构如图1所示。前面的包msgcode=34时，31的包的结构如图2所示。
 
![图](https://sonya1.github.io/assets/img/blog/ssh_6.png)

![图](https://sonya1.github.io/assets/img/blog/ssh_7.png)
 
(3)	Msgcode=34的时候，交换信息结构相同，截图如下。

![图](https://sonya1.github.io/assets/img/blog/ssh_8.png)

获取4字节的DH GEX Min的值，再获取4字节的DH GEX Number of Bits值，再获取4字节的DH GEX Max的值。
(4)	Msgcode=33的时候，交换信息结构相同，截图如下。
 
![图](https://sonya1.github.io/assets/img/blog/ssh_9.png)

先获取4字节的Host Key Length,输出相应长度的KEX DH host key值。输出4字节的Multi Precision Integer Length的值，输出DH server f值，输出KEX DH H signature Length值，输出KEX DH H signature值。
