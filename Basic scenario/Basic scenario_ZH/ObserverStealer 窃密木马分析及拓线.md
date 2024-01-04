# ObserverStealer 窃密木马分析及拓线

## 写在前面
自今年5月起，我们在多个黑客论坛中发现了一款名为ObserverStealer的窃密木马正在被售卖。这款木马具有强大的恶意功能，它能够窃取用户的浏览器数据，上传指定目录的文件，获取屏幕截图，甚至下载和运行其他恶意载荷。

在我们的监控过程中，也观察到了此类事件的发生。

因此，今天我们将从这个角度出发，通过各种渠道进行IOC收集，同时利用FOFA和any.run这两款工具，对该组织进行深度的资产拓线分析。

## 原始IOC收集
 
**原始样本分析**

近期监控发现TG上突然出现了许多以贩卖个人用户浏览器保存信息而获利的组织。包括用户的个人账号密码、个人电脑信息等。而且其中的样本存在大量中国IP的账号。

![observer1](../../Storage/observerstealer/observer1.png)

从他们给的样本数据中，发现了这是一个叫做stealc steader（偷窃者）的组织，可以看到将样本数据进行解析后，直接会出现他们抓取到用户保存在浏览器的账号和密码。

![observer2](../../Storage/observerstealer/observer2.png)

![observer3](../../Storage/observerstealer/observer3.png)

![observer4](../../Storage/observerstealer/observer4.png)


甚至根据样本能看到被控人的截图，好家伙。

![observer5](../../Storage/observerstealer/observer5.png)


我们通过这些线索继续检索，发现了这个TG账号，由此账号推断，这个组织来自于俄语地区，且他们长期在xss.is和exploit.in上发广告卖数据。

![observer6](../../Storage/observerstealer/observer6.png)

**互联网检索**

  
我们通过以上获得的信息进行检索，发现Group-IB安全团队在5月份的时候就揭露了类似的组织，并称其为Observer Stealer（观察偷窃者），Group-IB揭露了有人在xss.is网站上以150美元/月的价格出售用户浏览器保存的账号密码信息、个人电脑信息。和我们发现的情况一样！   

通过Group-IB的推文信息可以发现，这个平台可以直接生成exe执行文件，并去收集相关电脑上的信息，而且披露的该平台的样本数据截图，和我们获取到的数据结构非常相似！

![observer7](../../Storage/observerstealer/observer7.png)

![observer8](../../Storage/observerstealer/observer8.png)




**病毒样本分析**

 
在使用关键词“Observer Stealer”在app.any.run病毒分析站进行搜索后，我们发现了三个病毒样本。

经过初步的样本分析，我们发现它们会进行类似于认证的操作，随后从外部服务器加载DLL执行。接着，它们会向外联地址持续性地发送TXT和image文档。

通过这三个病毒样本，我们获得了相关的路径和初始的C2地址。研究还发现，这些病毒有不同的攻击行为方式，这表明并非只有一个组织在执行这些操作。


![observer9](../../Storage/observerstealer/observer9.png)

```
path:          
/freebl3.dll          
/libcrypto.dll          
/mozglue.dll          
/nss3.dll          
/mozglue.dll          
/softokn3.dll          
/sqlite3.dll
C2:          
http://5.42.64.13:3000          
http://5.42.64.41:1337          
http://91.103.252.17:8912
```


## 利用FOFA进行拓线 

经过上述的几个方法，我们获取到了一些样本，下面我们将会使用FOFA尝试进行进一步的资产拓线，以获取更多不为人知的改组织的资产。    

**线索1：** 从上述获取到的图标进行搜索，获取到4条数据。    

[icon_hash="-1529439559"](https://en.fofa.info/result?qbase64=aWNvbl9oYXNoPSItMTUyOTQzOTU1OSI%3D)

![observer10](../../Storage/observerstealer/observer10.png)

**线索2：** 病毒样本的C2地址特征 

样本病毒每次进行远程加载DLL时，都需要进行一次验证，且鉴定失败就会显示404。通过这条IOC，我们发现了这两个特征。

[ip="5.42.64.13"](https://en.fofa.info/result?qbase64=aXA9IjUuNDIuNjQuMTMi)

![observer11](../../Storage/observerstealer/observer11.png)


将这两个特征拼接可得搜索语法，但是结果有点太多了，不太正常。

[banner="access-control-expose-headers: Content-Type, Authorization" && banner="404 Not Found"](https://en.fofa.info/result?qbase64=YmFubmVyPSJhY2Nlc3MtY29udHJvbC1leHBvc2UtaGVhZGVyczogQ29udGVudC1UeXBlLCBBdXRob3JpemF0aW9uIiAmJiBiYW5uZXI9IjQwNCBOb3QgRm91bmQi)


![observer12](../../Storage/observerstealer/observer12.png)


我们冷静下来重新分析，如果都通过该平台去加载这种行为，拿它每一次的404返回页面内容应该是一样的，那么长度信息也可以进行提取，同时满足三个特征的资产才是我们要找的。

生成的新语法有6个地址，5个独立IP。     

[banner="access-control-expose-headers: Content-Type, Authorization"&&banner="404 Not Found" && banner="Content-Length: 40"](https://en.fofa.info/result?qbase64=YmFubmVyPSJhY2Nlc3MtY29udHJvbC1leHBvc2UtaGVhZGVyczogQ29udGVudC1UeXBlLCBBdXRob3JpemF0aW9uIiYmYmFubmVyPSI0MDQgTm90IEZvdW5kIiAmJiBiYW5uZXI9IkNvbnRlbnQtTGVuZ3RoOiA0MCI%3D)

![observer13](../../Storage/observerstealer/observer13.png)


拿到这些资产后，可以继续去样本网站进行查询，试试这样是否会发现更多的线索。

**结果1** 

5.42.64.13: 最早样本时间为2023年6月13日。该样本特征为同一个进程中利用了其他C2地址来下载exe可执行文件和上传txt文档。

![observer14](../../Storage/observerstealer/observer14.png)

![observer15](../../Storage/observerstealer/observer15.png)



他所关联的C2地址为：        

```
5.42.66.1          
94.142.138.116
```

**结果2** 

5.42.64.41：最早样本时间为2023年6⽉5⽇。是一种新的利⽤⽅式，它会通过共享⽂件平台这种⽅式下载恶意⽂件并运⾏。

![observer16](../../Storage/observerstealer/observer16.png)

https://www.4sync.com/web/directDownload/5cAUlxF1/fOHYSFp2.2d5b0e87aace84bf3807a7c917adfb0d

![observer17](../../Storage/observerstealer/observer17.png)

**结果3**    

91.103.252.17：最早样本时间为2023年7⽉4号。⼜发现了与上述两个结果不同的利⽤⽅式，它会通过⽩名单 windowsupdate.com跟godaddy.com去加载恶意⽂件进行⼆次利⽤。

![observer18](../../Storage/observerstealer/observer18.png)

**结果4** 

77.73.134.51：最早样本时间为2023年5⽉15号。该IP直接对应到了observer stealer组织演示该平台的时间跟IP。

![observer19](../../Storage/observerstealer/observer19.png)

为什么要选取这几个结果IOC进行分析呢？因为它们很有代表性且可以得出有意思的结论。

我们根据攻击方式和时间线（基本都是在6月到7月出现第一批样本数据）来说，可以判断这些IP并不是同一个组织，初步判断有三批不同的团队在通过这个平台进行病毒传播。

而且通过分析病毒行为发现，该病毒在5月份以后的版本出现了远控的功能，可以控制下载或者访问挂载的文件。

我们刚才通过病毒关联发现了两个有用的IP，可以在FOFA上进行进一步的拓线。

先从5.42.66.1入手，发现了熟悉的开放端口3000，根据相关样本相近的结果显示，这个程序访问的80端口上挂载这一个myliunx的服务。

![observer20](../../Storage/observerstealer/observer20.png)

现在已知，该组织一般会通过3000端口去加载DLL，80端口POST文件利用，那么结合这两个特征，先通过标题和80端口检索结果。

有8条资产，6个独立ip。

[title=="myLinx" && port="80"](https://en.fofa.info/result?qbase64=dGl0bGU9PSJteUxpbngiICYmIHBvcnQ9IjgwIg%3D%3D)

![observer21](../../Storage/observerstealer/observer21.png)


```
77.91.76.15          
5.42.66.1          
104.21.76.48(cloundflare)          
172.67.187.177(cloundflare)          
208.113.165.34          
135.181.213.164
```

我们去除掉cloundflare 资产分别查看这4个IP是否开放3000端⼝。

发现IP归属地为东欧地区的IP都开放了3000端⼝，结合我们之前对病毒⾏为分析的结论：在远程加载DLL之前需要验证，所以3000端⼝返回包应该为 404。

所以基本确认 77.91.76.15和5.42.66.1是这个组织另外的C2地址。

![observer22](../../Storage/observerstealer/observer22.png)

![observer23](../../Storage/observerstealer/observer23.png)



现在看第二个关联IP，94.142.138.116。通过在FOFA上搜索，发现是一个nginx服务，这个服务器不符合我们上述分析总结的一些特征，不过这个ip的8081端口竟然部署了RisePro，怀疑是这个组织的一台控制机。

![observer24](../../Storage/observerstealer/observer24.png)


这次关联ip的分析结果如下：

```
c2地址：          
77.91.76.15          
5.42.66.1          
受控主机          
94.142.138.116
```

## 总结   
我们这次通过分析Stealer偷窃者类木马的行为特征，结合FOFA和any.run，去探寻他们背后的更多资产和特征。

这个木马其主要表现为进行类似认证的外部访问操作，加载外部服务器挂载的DLL执行，以及持续向外联地址POST发送txt和image文件，并伴有一些远控行为。

通过对病毒样本的分析，我们发现了三种不同的攻击行为方式，确认了存在多个组织在进行此类活动的结论。并且通过FOFA拓线发现并确认了该组织的服务器以及C2服务器。

希望这篇文章可以为大家带来更多的思考，互联网非常的有趣，抽丝剥茧总能发现隐藏在最深处的线索。   

**信息汇总**

```
DLL path          
/freebl3.dll          
/libcrypto.dll          
/mozglue.dll          
/nss3.dll          
/mozglue.dll          
/softokn3.dll          
/sqlite3.dll
```

```
C2:          
5.42.64.41:1234          
179.43.155.205:81          
91.103.252.16:2424          
91.215.85.38:1234          
5.42.64.13:3000          
91.103.252.17:8912          
91.215.85.38:3000          
5.42.64.41:1337          
77.73.134.51:3000          
77.73.134.51:3001          
77.91.76.15          
5.42.66.1          
94.142.138.116
```

```  
Trojan hash MD5：          
3a4bd9f63354d5c840069528f2245b8a          
94a5c959239a6cd8f580e5f088552369          
57cebb3b7e7f6362fd8282c0a62e8ed9          
8daeb676138ec7a6173f8cae4d9b5146          
9213ec7fc25ac1f8e2fb318bb2d399e7          
e5dbe60dbe305d5b512a93c80f2575ad          
be266f86c6927fedc41f106002e6c9c2          
fd72250981f4893adf7b46f44d41444d          
fc6c1ca41a6d39ded7ed9aa8a4d85585          
781b967891fc70a56149bf82dfad4fa4          
508971e96c961d6b88d56701cd189bb2          
1780096fea2ee66aedd59718c7686f15          
298b4efda09bf8d79b5deb84ca87fbae          
7f8d6ce69e8b0d9c09208ad39b1d1440          
d382782d8fa8574354ac6033f95eb5ca          
df3795e6842e839cf45e694b7164ee17          
bee39fe76d04c610256938a58ac5d660          
d86704134f65f0ebe87032f76864db5a          
df3795e6842e839cf45e694b7164ee17          
c28cc92a7c78b96bec58fa3e5398074a          
21001efc52912f4fac0ec8b4a5837313          
88344d191754f08133b2e798b836638d          
c31d52eb807ac1b269d66ceafa3012b9          
d6b12bcfdf067e283a062a542d96c6e0          
0160251d7bd26c489df555fbe24dd9be          
59565c1d8b5fd88759ea9e225379783b          
e884f6eec5e4ab58a189203001c6acc9
```

## 参考链接：   
https://www.antiy.cn/research/notice&report/research_report/ObserverStealer_Analysis.html

https://twitter.com/TLP_R3D/status/1662508869665402880

https://twitter.com/GroupIB_TI/status/1658805021880795136

https://medium.com/@cyberhust1er/observerstealer-unmasking-the-new-contender-in-cybercrime-6e54a40d801d