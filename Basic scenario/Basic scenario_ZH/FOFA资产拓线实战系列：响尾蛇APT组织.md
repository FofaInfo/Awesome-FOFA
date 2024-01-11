# FOFA资产拓线实战系列：响尾蛇APT组织

![0](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinderen.png)

## 概述

在我们的前几篇文章中，我们通过实际的案例，以更接近用户的角度分享和展示了如何使用FOFA进行资产拓线，收获了非常多反馈和好评。因此，我们决定将“FOFA资产拓线实战”打造成一个系列，不仅将展示更多的数据情报，也将分享更多的FOFA操作技巧。

本篇文章通过分析已公开的5条IOC来对响尾蛇APT组织进行资产拓线，形成该组织的FOFA规则，**并最终共获得193条拓线结果**。

同时我们借助了奇安信威胁情报平台、微步在线和VirusTotal三款行业领先的工具进行了交叉验证。在验证过程中，我们发现这三个平台对于响尾蛇APT组织的覆盖存在一定的差异。具体来说，**奇安信的覆盖137条、微步覆盖118条、Virustotal覆盖 138条，存在三个平台都未覆盖24条的情况。**

由于各平台的属性不同，集成了FOFA平台的持续信息收集能力，一旦识别了组织特征，便可动态获取最新资产信息并及时覆盖，从而更快获取威胁情报，提升客户价值。

基于参考报告中使用了shodan，我们也用shodan进行了验证。但是其由于机制原因已无报告相同结果。但通过我们的验证，使用相同特征进行拓线，**仍发现有参考报告之外的存活资产存在。**

除此之外，我们的研究还发现，响尾蛇APT组织更倾向于针对政府、能源、军事和矿产等领域进行攻击。这一点在我们的拓线结果中得到了明确的反映。
文章将会详细描述针对于IOC的拓线思路以及方法，并在文章末尾附有拓线及对比表格。
## 线索分析思路

### 线索一：

我们通过MikhailKasimov 在GitHub上总结的IOC进行提取，找到以下三个域名：


```
neger.site
semain.tech
aliit.org
```

GitHub开源项目：

https://github.com/stamparm/maltrail/blob/3db4f2069cd7befcbfd2e0d1fe447773f1f755ca/trails/static/malware/apt_sidewinder.txt

看到原始数据来源是Bridewell公司的分析报告。使用FOFA来对这三条域名进行查询，看一下他的特征是怎样的。

```domain="neger.site"```

![1](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder1.png)


```domain="semain.tech"```

![2](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder2.png)

```domain="aliit.org"```

![3](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder3.png)

从FOFA的展示结果来看，他们明显有相同JARM指纹特征。
我们提取这条JARM指纹进行查询

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7"
```

![4](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder4.png)

发现有247,319条资产，说明通过这个单一特征存在很大的误差，所以需要去查找三条IOC的其他共性特征。
仔细对比发现，前两个域名的header都存在404 Not Found、Server: nginx、Content-Type: text/html的特征。

![5](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder5.png)

![6](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder6.png)

但是aliit.org的Server为nginx/1.23.2，和其他两条不一样。我们先研究另外两个IOC。
通过提取前两个域名的相同特征，加入到FOFA的搜索语句如下：


```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html"
```

![7](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder7.png)

明显看到圈定范围变少了，但是通过地区排名可以发现还是存在部分误差，需要继续提取有效特征。
通过header响应头及网站正文确认，他的Content-Length都为183或535个字符串。
可利用此特征加入到FOFA语句中继续进行查询：

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535")
```

![8](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder8.png)

目前结果正常了，我们需要做一步判定，加上最初的两个IOC进行验证，是否为包含关系。

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535") && (domain="neger.site" || domain="semain.tech")
```

![9](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder9.png)

确认没有问题，所以通过前两条域名最终拓线到的FOFA规则为：

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535")
```



下面我们继续看另一条IOC：aliit.org发现其特征与前两个域名的特征基本类似 只有Content-Length为555、Server为nginx/1.23.2这两处不同。

![10](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder10.png)

通过同样的方式提取特征，整理为FOFA语法进行查询：

```
header="HTTP/1.1 404 Not Found" && header="Server: nginx/1.23.2" && header="Content-Type: text/html" && header="Content-Length: 555" && jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7"
```

![11](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder11.png)


### 线索二：

线索二来自于社交媒体，有安全研究员公开的响尾蛇组织IOC，和以上的IOC不重合，我们正好可以再进行一波拓线补充。

![12](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder12.png)

取其中的主域进行分析：

```
direct888.net
fia-gov.net
```

使用FOFA进行搜索，确认他的资产特征：

```domain="direct888.net"```

![13](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder13.png)

```domain="fia-gov.net"```

![14](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder14.png)

很明显，很熟悉。果然，他们的JARM指纹是一样的，且其他特征的表现和上面的线索特征保持一致，只有content-length是228。
将这些特征进行整合成FOFA的规则就是：

```
jarm="1dd40d40d00040d1dc1dd40d1dd40de9ab649921aa9add8c37a8978aa3ea88" && header="Content-Length: 228" && header="HTTP/1.1 404 Not Found" && header="Server: nginx"
```

![15](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder15.png)

本次拓线我们根据IOC整理出来了三条该组织的资产规则如下。

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535")
```
```
header="HTTP/1.1 404 Not Found" && header="Server: nginx/1.23.2" && header="Content-Type: text/html" && header="Content-Length: 555" && jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7"
```

```
jarm="1dd40d40d00040d1dc1dd40d1dd40de9ab649921aa9add8c37a8978aa3ea88" && header="Content-Length: 228" && header="HTTP/1.1 404 Not Found" && header="Server: nginx"
```

## 拓线资产验证

因为参考文章中Bridewell公司所出的报告中用到了shodan进行信息收集，所以我们也通过shodan平台对整理出来的规则进行了确认，可能因为机制的原因，已经无法看到更多。

![16](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder16.png)

![17](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder17.png)

![18](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder18.png)

但是，通过我们使用相同特征所拓线的结果进行取样验证，数据依旧是存活的状态，Shodan无法获取。
donwloaded.net

![19](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder19.png)

![20](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder20.png)


除此之外，我们也针对于使用FOFA拓线出来的响尾蛇APT组织资产进行三方平台验证，验证过程中发现各个平台存在一定的标定差异性，以下几种情况都存在。
countpro.info都被标定为响尾蛇。

![21](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder21.png)

neger.site，奇安信威胁情报平台未标记为响尾蛇。

![22](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder22.png)

mofagov.live，微步未标记为响尾蛇。

![23](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder23.png)

其中获得的线索IOC： direct888.net 都被标定为响尾蛇，但是后续在FOFA上拓线出来的direct888.org 则没有被标定。

![24](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder24.png)

![25](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder25.png)



所以针对于这种情况存在，我们用拓线后的规则对于威胁情报平台做了一次详细验证，并引入新的工具。验证使用的工具包括奇安信威胁情报平台、微步在线和Virustotal，覆盖数分别为奇安信137条、微步118条和Virustotal 138条，并存在三个平台都未覆盖的情况。

由于各平台的属性不同，FOFA网络空间测绘平台凭借其持续不断的信息收集能力，一旦确认了组织的特征，只要该组织仍在线，就可以将该规则添加到监控系统中。这将允许我们动态地获取该组织的最新资产信息，并迅速进行标签覆盖。这种方式可以更快地获取威胁情报，从而为客户创造更大的价值。

验证结果总结如下，详细标定信息可查看附录1。


| FOFA拓线结果 | 奇安信未覆盖  | 微步未覆盖  | Virustotal未覆盖  | 都未覆盖 |
|:----------|:----------|:----------|:----------|:----------|
| 193条    | 56条    | 75条    | 55条    | 24条    |



## 总结

通过对部分IOC进行拓线分析，可以发现该组织倾向于利用与攻击目标相关的机构官方域名的关键词来构造域名。例如，他们使用“*fia-gov*”，“*mof-gov*”，“*mofa-gov*”，“*govnp*”，“*govpk*”等关键词伪装成军事和政府相关的域名。另外，他们还通过字符的移位、隐藏等手段构造域名，例如“*direct888.org”，“*directt88.org”，“*downlod.net”，“*downlod.com”等进行攻击。

威胁情报对比结果为奇安信覆盖137条、微步覆盖118条、Virustotal覆盖 138条，并存在三个平台都未覆盖24条的情况。

通过使用少部分IOC，逐步分析并提取特征来进行资产拓线，当拓线完成后，实际上是形成了FOFA规则所存在，结合FOFA平台的持续信息收集能力，一旦识别了组织特征并保持其在线，便可动态获取最新资产信息并及时覆盖，从而更快获取威胁情报，提升客户价值。

基于Bridewell公司报告中使用shodan进行信息收集的方法，我们也用shodan验证了整理的规则。尽管由于机制原因已无报告相同结果。但通过我们的验证，使用相同特征进行拓线，仍发现报告之外的存活资产存在。

这个过程主要依赖于FOFA强大的搜索功能，但更关键的是发现线索和进行逻辑推理的能力。希望这个案例能对大家如何更有效地利用FOFA进行资产拓线和信息收集提供帮助，以充分利用FOFA的能力。

## 参考文章: 

https://www.bridewell.com/insights/news/detail/the-distinctive-rattle-of-apt-sidewinder
https://github.com/stamparm/maltrail/blob/3db4f2069cd7befcbfd2e0d1fe447773f1f755ca/trails/static/malware/apt_sidewinder.txt
https://www.freebuf.com/news/373902.html
https://twitter.com/Cyberteam008/status/1743505453529940004

## 附录1：本次案例拓线去重结果及平台对比

| IOC | FOFA最后更新时间 | 微步 | 奇安信威胁情报中心 | Virustotal |
| --- | --- | --- | --- | --- |
| academy.lesporc.live | 2023-07-08 | Sidewinder |  |  |
| aeryple.xyz | 2023-03-17 | Sidewinder | Sidewinder | 恶意 |
| agency.lesporc.live | 2023-07-08  | Sidewinder |  |  |
| aliit.org | 2023-05-01  | Sidewinder | Sidewinder | Sidewinder |
| amuck.scoler.tech | 2023-05-02 | Sidewinder | Sidewinder_23_05_17 | Sidewinder |
| api.argus.trondheim.bama.zoopit.no | 2023-11-19  |  |  |  |
| avail.freay.tech | 2023-06-21  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| awrah.live | 2023-04-03  | Sidewinder | Sidewinder | Sidewinder |
| axis.heplor.biz | 2023-11-01 | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| basic.gruh.site | 2023-06-24 | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| bless.agarg.tech | 2023-03-31 | Sidewinder | Sidewinder | 恶意 |
| bol-south.org | 2023-01-10  | Sidewinder | Sidewinder_23_05_17 | Sidewinder |
| brac.tech | 2023-06-12  | Sidewinder | Sidewinder | Sidewinder |
| brave.agarg.tech | 2023-03-27  | Sidewinder | Sidewinder | 恶意 |
| cater.sphery.live | 2023-03-30  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| cdn.awrah.live | 2023-01-10 | Sidewinder | Sidewinder |  |
| cdn.cpec.site | 2023-04-22  | Sidewinder | Sidewinder | 恶意 |
| cdn.dolper.top | 2023-06-20  | Sidewinder | Sidewinder | 恶意 |
| cdn.dr-doom.xyz | 2023-04-18  | Sidewinder | Sidewinder |  |
| cdn.gearfill.biz | 2023-07-29  | Sidewinder | Sidewinder |  |
| cdn.geoloc.top | 2023-06-24 | Sidewinder |  |  |
| cdn.hread.live | 2023-04-27  | Sidewinder | Sidewinder |  |
| cdn.plors.tech | 2023-05-27  | Sidewinder | Sidewinder |  |
| cdn.preag.info | 2023-05-26  | Sidewinder |  |  |
| cdn.preat.info | 2023-05-26  | 恶意软件 | Sidewinder |  |
| cdn.prol.info | 2023-04-16  | Sidewinder |  |  |
| cdn.verocal.info | 2023-05-26  |  | Sidewinder |  |
| ceiling.kalpo.xyz | 2023-07-05  | Sidewinder | Sidewinder | 恶意 |
| cert.repta.live | 2023-02-20  | Sidewinder | Sidewinder | 恶意 |
| cirt-gov-mm.fia-gov.net | 2024-01-02  | Sidewinder | Sidewinder | 恶意 |
| civil.leyra.tech | 2023-04-23  | Sidewinder | Sidewinder |  |
| climb.kalpo.xyz | 2023-07-05  | Sidewinder | Sidewinder | 恶意 |
| cluster.jotse.info | 2023-05-01  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| cpec.site | 2023-04-28  | Sidewinder | Sidewinder | Sidewinder |
| csla.blesis.live | 2023-04-24  | Sidewinder |  |  |
| cssc-net.co | 2023-01-16  | Sidewinder | Sidewinder | Sidewinder |
| cvix.cc | 2023-01-17  |  |  | 恶意 |
| density.meplor.xyz | 2023-06-21  | Sidewinder | Sidewinder |  |
| deputy.meplor.xyz | 2023-06-07  | Sidewinder | Sidewinder |  |
| direct888.net | 2023-12-13  | Sidewinder | Sidewinder | 恶意 |
| direct888.org | 2023-09-08  |  |  | 恶意 |
| directt88.org | 2023-01-17  | Sidewinder | Sidewinder_23_05_17 | SideWinder |
| dolper.top | 2023-06-09  | Sidewinder | Sidewinder | SideWinder |
| donwloaded.net | 2024-01-04 |  |  | 恶意 |
| dr-doom.xyz | 2023-04-22  | Sidewinder | Sidewinder | SideWinder |
| dsmes.xyz | 2023-03-21  | Sidewinder | Sidewinder | SideWinder |
| employ.fdrek.live | 2023-03-29  | Sidewinder | Sidewinder |  |
| energy.fdrek.live | 2023-03-30  | Sidewinder | Sidewinder |  |
| fia-gov.com | 2023-05-01  | Sidewinder | Sidewinder | SideWinder |
| fia-gov.net | 2024-01-01  | Sidewinder |  | 恶意 |
| focus.mectel.tech | 2023-05-01  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| focus.semain.tech | 2023-10-19  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| found.neger.site | 2024-01-01  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| found.troks.site | 2023-04-27  |  | Sidewinder_23_05_17 | 恶意 |
| gearfill.biz | 2023-07-29  | Sidewinder | Sidewinder | SideWinder |
| geoloc.top | 2023-06-24  | Sidewinder | Sidewinder_23_05_17 | SideWinder |
| groove.olipy.info | 2023-04-30  | 恶意软件 | Sidewinder_23_05_17 | 恶意 |
| guide.graty.tech | 2023-10-18  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| hread.live | 2023-04-14  | Sidewinder | Sidewinder | SideWinder |
| islamic-path.com | 2023-04-30  | Sidewinder | Sidewinder_23_05_17 | SideWinder |
| kito.countpro.info | 2023-09-05  | Sidewinder | Sidewinder | 恶意 |
| lax036.relay.arandomserver.com | 2023-03-06  |  |  |  |
| leyra.tech | 2023-04-23 | Sidewinder | Sidewinder | SideWinder |
| lnkly.net | 2023-04-18  |  |  |  |
| mat.trelin.tech | 2023-06-23  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| mfagov.org | 2023-01-24 | Sidewinder | Sidewinder_23_05_17 | SideWinder |
| mod-gov.org | 2023-02-08  | Sidewinder | Sidewinder | 恶意 |
| mofa-gov-bd.fia-gov.net | 2024-01-02  | Sidewinder | Sidewinder | 恶意 |
| mofagov.live | 2023-07-05  |  | Sidewinder | 恶意 |
| mu-api.anyremote.cn | 2023-03-01  |  |  |  |
| mxhichina.info | 2023-01-25  |  |  |  |
| myanmar-gov-mm.fia-gov.net | 2024-01-02  | Sidewinder |  | 恶意 |
| myoffice.fia-gov.net | 2024-01-02  | Sidewinder |  | 恶意 |
| nextgen.fia-gov.net | 2024-01-02  | Sidewinder |  |  |
| ns.seiffenn.nohost.me | 2023-03-06  |  |  |  |
| ntc-pk.org | 2023-01-25  | Sidewinder | Sidewinder | SideWinder |
| olerpic.info | 2023-02-08  | 恶意软件 | Sidewinder_18_05_22 | SideWinder |
| opmcm-gov-np.fia-gov.net | 2024-01-02  | Sidewinder | Sidewinder |  |
| opt.freay.tech | 2023-06-20  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| pak-gov.info | 2023-01-16  | Sidewinder | Sidewinder | SideWinder |
| plors.tech | 2023-06-01  | Sidewinder | Sidewinder | SideWinder |
| pmdu-gov.org | 2023-02-02  | Sidewinder | Sidewinder | 恶意 |
| police-circular-gov-bd.fia-gov.net | 2024-01-02  | Sidewinder | Sidewinder | 恶意 |
| portal.breat.info | 2023-04-30  | Sidewinder | Sidewinder | 恶意 |
| preag.info | 2023-06-04  | Sidewinder | Sidewinder | SideWinder |
| preat.info | 2024-01-01  | 恶意软件 | Sidewinder | SideWinder |
| ptcl-gov.org | 2023-01-10 | Sidewinder | Sidewinder | 恶意 |
| resolve.preat.info | 2023-11-12  | 恶意软件 | Sidewinder |  |
| reveal.troks.site | 2023-04-09  |  | Sidewinder_23_05_17 | 恶意 |
| roof.wsink.live | 2023-03-17  | Sidewinder | Sidewinder | 恶意 |
| rugby.wsink.live | 2023-03-17  | Sidewinder | Sidewinder | 恶意 |
| seiffenn.nohost.me | 2023-06-06  |  |  |  |
| storeapp.site | 2023-01-17  |  | Sidewinder | SideWinder |
| test.api.68wx.com | 2023-03-01  |  |  |  |
| test.api.g.luohu8.com | 2023-06-24  |  |  |  |
| test.api.hzy.68wx.com | 2023-03-01  |  |  |  |
| test.es.68wx.com | 2023-03-01  |  |  |  |
| tinurl.click | 2023-01-11  |  | Sidewinder | SideWinder |
| torsey.xyz | 2023-07-05  | Sidewinder |  |  |
| toss.tercom.site | 2023-06-21  | Sidewinder |  |  |
| true-islam.org | 2023-04-21  |  |  | 恶意 |
| trust-crypto.net | 2023-05-21  |  |  |  |
| utilize.elopter.top | 2023-06-20  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| verocal.info | 2023-06-26  |  | Sidewinder | SideWinder |
| wide.storeapp.site | 2023-01-17  |  | Sidewinder | 恶意 |
| wind.ridlay.live | 2023-04-30  |  | Sidewinder | SideWinder |
| www.aliit.org | 2023-05-01  | Sidewinder |  | SideWinder |
| www.awrah.live | 2023-03-29  | Sidewinder | Sidewinder | SideWinder |
| www.bol-south.org | 2023-01-10  | Sidewinder |  | SideWinder |
| www.brac.tech | 2023-06-12  | Sidewinder | Sidewinder | SideWinder |
| www.cpec.site | 2023-04-28  | Sidewinder | Sidewinder | SideWinder |
| www.cssc-net.co | 2023-01-16  | Sidewinder | Sidewinder | SideWinder |
| www.cvix.cc | 2023-01-17  |  |  | 恶意 |
| www.direct888.net | 2023-12-13  | Sidewinder | Sidewinder | 恶意 |
| www.direct888.org | 2023-09-08  |  |  | 恶意 |
| www.directt88.org | 2023-01-17  | Sidewinder |  | SideWinder |
| www.dolper.top | 2023-07-23  | Sidewinder | Sidewinder | SideWinder |
| www.donwloaded.net | 2024-01-04  |  |  | 恶意 |
| www.dsmes.xyz | 2023-03-21  | Sidewinder | Sidewinder | SideWinder |
| www.fia-gov.com | 2023-05-01  | Sidewinder | Sidewinder | SideWinder |
| www.fia-gov.net | 2024-01-01  | Sidewinder |  |  |
| www.gearfill.biz | 2023-07-29  | Sidewinder | Sidewinder | SideWinder |
| www.geoloc.top | 2023-06-24  | Sidewinder |  | SideWinder |
| www.hread.live | 2023-05-25  | Sidewinder | Sidewinder | SideWinder |
| www.islamic-path.com | 2023-04-30  | Sidewinder |  | SideWinder |
| www.lnkly.net | 2023-04-18  |  |  |  |
| www.mfagov.org | 2023-01-24  | Sidewinder |  | SideWinder |
| www.mod-gov.org | 2023-02-08  | Sidewinder | Sidewinder | 恶意 |
| www.mofa-gov-bd.fia-gov.net | 2024-01-02  | Sidewinder |  | 恶意 |
| www.mofagov.live | 2023-07-05  |  | Sidewinder | 恶意 |
| www.mxhichina.info | 2023-01-25  | Sidewinder |  |  |
| www.pak-gov.info | 2023-01-16  | Sidewinder | Sidewinder | SideWinder |
| www.plors.tech | 2023-04-25  | Sidewinder | Sidewinder | SideWinder |
| www.pmdu-gov.org | 2023-02-02  | Sidewinder | Sidewinder | 恶意 |
| www.preag.info | 2023-03-27  | Sidewinder |  | 恶意 |
| www.preat.info | 2024-01-01  | Sidewinder | Sidewinder | SideWinder |
| www.ptcl-gov.org | 2023-01-10  | Sidewinder | Sidewinder | 恶意 |
| www.seiffenn.nohost.me | 2023-03-03  |  |  |  |
| www.shortney.org | 2023-03-30  | Sidewinder | Sidewinder | 恶意 |
| www.tiinly.co | 2023-03-28  | Sidewinder | Sidewinder | SideWinder |
| www.tinurl.click | 2023-01-11  |  | Sidewinder | SideWinder |
| www.torsey.xyz | 2023-06-20  | Sidewinder |  |  |
| www.true-islam.org | 2023-04-21  |  |  | 恶意 |
| www.verocal.info | 2023-06-26  |  | Sidewinder |  |
| xmpp-upload.seiffenn.nohost.me | 2023-03-03  |  |  |  |
| zone.vtray.tech | 2023-06-14  | Sidewinder | Sidewinder_23_05_17 | 恶意 |
| 104.128.189.242 | 2023-04-21  |  |  | SideWinder |
| 106.14.215.130 | 2023-01-19  |  | APT、Snake |  |
| 149.154.154.65 | 2023-01-17  |  | Sidewinder | SideWinder |
| 151.236.21.16 | 2023-09-20  |  | APT、Snake | SideWinder |
| 158.255.212.140 | 2024-01-01  |  | APT、Snake | SideWinder |
| 161.129.64.98 | 2023-02-16  |  | APT、Snake | SideWinder |
| 172.93.162.117 | 2023-04-14  | 垃圾邮件、恶意 | APT、Snake | SideWinder |
| 172.93.189.46 | 2023-05-27  | 垃圾邮件、恶意 | APT、Snake | SideWinder |
| 172.96.189.157 | 2024-01-01  |  | APT、Snake | SideWinder |
| 179.43.141.203 | 2023-01-24  | 傀儡机、恶意 | APT、Snake |  SideWinder |
| 185.228.83.78 | 2023-03-03  |  | APT、Snake |  SideWinder |
| 192.71.166.145 | 2023-03-24  |  | APT、Snake | 恶意 |
| 193.142.58.105 | 2023-10-02  |  |  |  |
| 193.42.36.50 | 2023-05-31  |  | APT、Snake | SideWinder  |
| 194.61.121.216 | 2023-01-25  |  | APT、Snake | SideWinder  |
| 194.68.225.13 | 2023-03-16  | 傀儡机、恶意 | APT、Snake | SideWinder  |
| 195.133.192.40 | 2023-04-16 |  | APT、Snake | SideWinder  |
| 196.189.44.42 | 2024-01-01  |  |  |  |
| 198.252.108.132 | 2023-02-01  |  |  |  |
| 198.252.108.33 | 2023-03-16  |  | APT、Snake | SideWinder  |
| 2.58.14.149 | 2023-07-07  |  | HIJACKED、SCANNER |  |
| 2.58.14.249 | 2023-04-21  |  | APT、Snake | SideWinder  |
| 2.58.15.71 | 2024-01-04  |  |  |  |
| 212.83.46.37 | 2023-12-09  | 垃圾邮件、恶意 | SPAM |  |
| 45.14.107.153 | 2023-02-01  |  | Sidewinder | SideWinder  |
| 45.147.230.157 | 2023-03-23  | 垃圾邮件、恶意 | APT、Snake | SideWinder  |
| 46.30.188.174 | 2023-03-22  |  | APT、Snake | SideWinder  |
| 46.30.189.53 | 2023-06-02  |  | APT、Snake | SideWinder  |
| 5.149.249.186 | 2023-03-23  |  | APT、Snake | 恶意 |
| 5.180.114.198 | 2023-12-13  |  |  |  |
| 5.2.74.116 | 2023-07-23  |  | APT、Snake | SideWinder  |
| 5.2.76.232 | 2023-06-23  |  | APT、Snake | SideWinder  |
| 5.230.75.40 | 2023-06-26  |  | APT、Snake | SideWinder  |
| 5.255.100.119 | 2023-03-23  |  | APT、Snake | SideWinder  |
| 5.255.104.154 | 2023-03-26  |  | APT、Snake | SideWinder  |
| 5.255.104.209 | 2023-12-25  |  | APT、Snake | SideWinder  |
| 5.255.105.243 | 2023-02-02  |  |  |  |
| 5.255.98.158 | 2023-06-21  |  | APT、Snake | SideWinder  |
| 77.83.196.59 | 2024-01-01  |  | SPAM |  |
| 77.83.198.158 | 2023-12-03  |  | APT、Snake、SPAM | SideWinder  |
| 77.83.198.33 | 2023-03-03  |  | APT、Snake | SideWinder  |
| 79.141.174.208 | 2023-02-15  |  | APT、Snake | SideWinder  |
| 8.218.132.121 | 2023-01-25  |  |  |  |
| 83.171.236.187 | 2023-07-11  |  |  |  |
| 84.39.181.122 | 2023-01-25 |  |  |  |
| 89.248.171.166 | 2023-06-24  |  | APT、Snake | SideWinder  |
| 91.245.253.73 | 2023-04-24  | 垃圾邮件、恶意 | APT、Snake、SPAM | 恶意 |
