# FOFA资产拓线实战系列：COLDRIVER

![0](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriverzh.png)

## 概述
COLDRIVER（也称为 UNC4057、Star Blizzard 和 Callisto），这是一个俄罗斯威胁组织，专注于针对非政府组织、前情报和军官以及北约政府的知名人士的凭据网络钓鱼活动。

**本文尝试根据线索追踪COLDRIVER近期的活动痕迹，利用FOFA平台寻找COLDRIVER组织的痕迹，最终在FOFA平台上发现了2条与2024年1月18日公开的C2非常相似的数据。活动时间为2023年8月到10月期间。不过新发现的可疑IOC暂时没有发现未知资产的样本，但我们认为这两个IP存在高可疑性。**

## 原始样本及C2地址收集
Google威胁分析小组在2024年1月18日公布了一篇关于APT组织 COLDRIVER 的钓鱼攻击分析文章。该文章发现在2023年8月到9月期间APT组织 COLDRIVER 处在活跃状态，并通过样本获取到了一个C2地址。

![1](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver1.png)

通过该文章我们得到了最初的IOC信息

```
path:
/ws

c2:
45.133.216[.]15：3000
```

## 资产拓线

使用FOFA进行查询文中提到的C2地址：

![2](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver2.png)

可以看到FOFA上更新时间为 2023-10-16 与报告报导时间比较接近。从该条FOFA数据发现有效的信息很少，只有端口为3000，协议TLS的banner信息以及存在证书这几点。

```
1、端口为3000
FOFA语法：port="3000"
2、协议TLS banner信息
FOFA语法：banner="\x15\x03\x03\x00\x02\x022"
3、存在证书
```

通过以上几个已知信息想到几条可以去尝试拓线的思路。

通过3000端口和其端口的banner信息进行搜索以及通过是否存在相同的证书进行搜索。

**拓线一：通过3000端口及端口的banner信息查询，发现17条记录16个IP。**

`banner="\x15\x03\x03\x00\x02\x022" && port="3000"`

![3](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver3.png)

观察这17条数据后发现他们端口及banner信息高度相似，但是还是存在明显的数据误报。所以仅仅通过banner跟端口这种方式去查找不行需要进一步添加条件，于是我们把证书条件也进行添加，得到了5条数据：

`banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd"`

![4](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver4.png)

**拓线二：通过证书查找，我们发现证书的 Organization:Internet Widgits Pty Ltd 应该是配置证书时的默认值。**

分析发现了一个较为关键的线索就是证书有效期。我们通过证书有效期跟证书默认组织名进行搜索

![5](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver5.png)

`cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"`

![6](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver6.png)

搜索出来6个IP，这6个IP是我们认为证书信息高度一致。

小结：

将拓线一跟拓线二的数据组合碰撞一下得到3条数据

`banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"`

![7](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver7.png)

```
IP：
45.133.216.15:3000
95.164.17.94:3000
89.19.211.240:3000

```

通过查看这三条数据被发现的时间，发现为2023年8月到2023年10月。恰好位于Google威胁分析小组认为该组织活跃的时间内。如下：

![8](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver8.png)

![9](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver9.png)

![10](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver10.png)

## 验证

查询验证上述得到的3个IP。不过除了目前谷歌公布的IP 45.133.216.15:3000 我们并没有在在微步、奇安信、virustotal查询发现其他2个IP的相关情报及样本证明。详情如下：

![11](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver11.png)

![12](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver12.png)

![13](https://github.com/FofaInfo/Awesome-FOFA/blob/529738afb527a50b18b885c19b2c769fb0394402/Storage/coldriver/coldriver13.png)

## 总结

利用FOFA平台寻找COLDRIVER组织的痕迹，最终在FOFA平台上发现了2条与2024年1月18日公开的C2非常相似的数据。活动时间为2023年8月到10月期间。不过新发现的可疑IOC暂时没有发现未知资产的样本，但我们认为这两个IP存在高可疑性。本次拓线语法如下：

```
拓线FOFA语法一：
banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd"
拓线FOFA语法二：
cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"
最终语法：
banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"
```

## 引用

https://blog.google/threat-analysis-group/google-tag-coldriver-russian-phishing-malware/

## 附录

```
可疑地址：
95.164.17.94:3000
89.19.211.240:3000
C2地址：
45.133.216.15:3000
样本HASH(SHA256)
0f6b9d2ada67cebc8c0f03786c442c61c05cef5b92641ec4c1bdd8f5baeb2ee1
A949ec428116489f5e77cefc67fea475017e0f50d2289e17c3eb053072adcf24
C97acea1a6ef59d58a498f1e1f0e0648d6979c4325de3ee726038df1fc2e831d
Ac270310b5410e7430fe7e36a079525cd8724b002b38e13a6ee6e09b326f4847
```