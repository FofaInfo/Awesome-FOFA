# FOFA资产拓线实战系列：APT-C-23 Android端分析

![0](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/fofa%E6%8A%80%E6%9C%AF%E5%88%86%E4%BA%AB%E5%B0%81%E9%9D%A2.png)

## 概述

本篇内容专注于对APT-C-23（双尾蝎）在Android端的恶意活动进行深度剖析。
APT-C-23，又称Arid  Viper、Micropsia、Frozen  Cell、Desert  Falcon，其主要攻击范围涵盖中东地区相关国家的教育机构、军事机构等重要领域。网络攻击行动以窃取敏感信息为主，具备针对Windows、Android、iOS多平台的攻击能力。攻击者通常利用自定义移动端恶意软件从目标处收集敏感信息，并在受感染的设备上部署其他恶意软件。
通过分析已公开的3条IOC，本研究使用FOFA对其进行资产拓线。 **鉴于获取的7条结果都没有被威胁情报平台覆盖，因此同时针对拓线所得的恶意APP进行逆向分析，以确认拓线结果的真实性。**

**以及经研究分析，该组织会利用约会软件来进行窃密的主要方法及其应用软件恶意代码相较于IOC来源更加隐蔽，攻击者通过持续优化Android代码使得用户更难发现这种恶意行为。**

## APT-C-23 Android IOC获取

IOC来源于360威胁情报中心烽火实验室的文章《APT-C-23（双尾蝎）组织最新攻击活动分析》和《针对巴以地区围绕卡塔尔世界杯的攻击活动》。

```
https://blaxaplayer.com/
https://www.bbalignit.com/
https://kora442.com/
```

## FOFA规则提取

根据获取IOC的文章中，可以明确的知晓APT-C-23组织使用的手法是修改一些知名APP应用，并制作了相应的钓鱼网站进行载荷投递。

![1](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-1.png)

我们先在FOFA中搜寻我们获取到的三个钓鱼网站相关信息。

![2](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-2.png)

根据FOFA上的特征，我们可以看到。Blaxaplayer.com与bbalignit.com这两个网站所使用的证书是相同的，但是和kora442.com证书JARM指纹不一样，无法将其作为查询规则。

关注其他的特点，两个网站均采用LiteSpeed服务，而在kora442.com上并未发现相关内容。

因此，我们将关注点到他们的网站正文上，通过查看网站正文，对比三个钓鱼网站，发现其网页开头均包含keywords、description和canonical的相同特征。

![3](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-3.png)

这几个特征比较常见，虽然能进行标定，但是结果一定非常大，所以我们需要继续尝试从网站原文中获取其他的相似特征。
查看这三个IOC从它的原文中下载相关的部分，发现有两个特征存在。一个是getAppVersionOne()，另一个是getApp()。

![4](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-4.png)


这是目前找到的突出特征，我们将所有特征进行一个组合，生成FOFA的查询语法，这里需要特殊注意的是，包含特殊字符需要加上转义符哦。

```
status_code="200" && body="name=\"keywords\"" && body="name=\"description\"" && body="rel=\"canonical\""  && (body="onclick=\"getApp()\"" || body="onclick=\"getAppVersionOne()\"")
```

![5](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-5.png)


可以看到圈定了60条数据和15条独立IP。但是在我们进行逐一验证的时候，发现仍然存在多余的数据，因此需要再次对这个查询语句进行优化。
这时候可以锁定到js上面，js也是在查找相同特征网站的时候，非常好用的一个特征。所以我们在上述查询语句的基础上，添加了相似的myScript.js进行继续筛选，注意这也是或者的关系。

```
status_code="200" && body="name=\"keywords\"" && body="name=\"description\"" && body="rel=\"canonical\"" && (body="onclick=\"getApp()\"" || body="onclick=\"getAppVersionOne()\"") && (body="src=\"js/myScript.js\"" || body="src=\"assets/js/myScript.js\"" || body="src=\"js/script.js\"")
```

![6](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-6.png)

最终定位到49条资产，11条独立IP。

在排除IOC已公开的3个钓鱼网站及关于世界杯相关的钓鱼网站后，对剩余资产进行去重处理，共获得7个域名，均为社交类应用程序。然而，在进一步验证结果正确性过程中，我们发现了一个问题，所以我们需要对拓线的结果进行逐一验证。

## 结果验证

对于发现的未知IOC，需要做进一步的对比验证，但是在做对比的时候，发现发现的新的IOC在威胁情报平台上基本都没有被覆盖，所以我们对得到的结果逐一验证，以确认IOC的真实性。最终查验结果见附录。

![7](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-7.png)


逐一对拓线结果进行分析，所有网站都使用完全相同的模板来展示其内容，只有内容、标题和图片略有不同，页面特征和我们已知的几个钓鱼网站极为相似。

![8](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-8.png)

同时，根据我们参考的360烽火实验室的文章中的分析可知，钓鱼网站均存在myscript.js特征。

![9](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-9.png)

### 拓线结果分析1

我们将下载的app进行分析后发现，该app和IOC来源问找那个中的app特征并不一致，没有明显的类似executecommands以及获取到如whatsapp信息相关的代码片段。

```
https://www.reblychat.com
https://clemochat.com
https://mail.wislisapp.com
https://mail.lapizachat.com
```

![10](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-10.png)

但是在思科威胁情报研究团队于23年10月的报告《Arid Viper disguising mobile spyware as updates for non-malicious Android applications》中或许可以窥探一丝联系。

该文章称APT-C-23部署的一些恶意软件使用 Google 的 Firebase 平台作为恶意应用程序的 C2 基础设施。

Firebase 平台主要充当 C2 通道来发出命令以及下载和上传文件，该平台还能够向恶意软件提供新的 C2 服务器地址，以便它可以从 Firebase 平台切换到另一个攻击者控制的 C2 主机。

Arid Viper 在此次活动中使用的 Firebase 项目之一可以追溯到 2021 年，并且有一些与之相关的非恶意移动 APK，这表明运营商多年来一直在尝试创建、测试和推广其他 APK 软件。

![11](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-11.png) 

该组织通过运营大量约会软件进行窃密，通过文章中所展示的恶意代码片段进行搜索仍没有找到相同的代码片段，怀疑攻击者对代码再次进行了升级使得攻击行为更加隐蔽。
同时，有意思的是这些app中有四个app只要存在一个其他便无法安装，因此可以判断这四个app实际上是换皮的同一套app。

![12](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-12.png) 

我们使用模拟器对应用进行安装，在拥有Google框架的前提下仍被提示需要更新Google Play服务。

![13](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-13.png) 

而且，这些app使用的firebase开发平台与参考文章中的描述是一致的。

![14](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-14.png) 

在提示Google Play更新的代码中可以看到多语言的存在，由此可以判断目标群体是比较明确的。

![15](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-15.png) 

除此之外，在getAntiSet方法的ANTI_SET中包含大量安卓设备杀毒软件包名，并通过拼接的方式进行组合。

![16](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-16.png) 

该类中checkValidation方法，是通过判断PackageManager.checkIfAppInstall来确定是否执行onchecked(true)。

![17](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-17.png) 

跟踪PackageManager.checkIfAppInstall可以看到该方法是用于检查设备上是否安装了特定的应用程序，而传入的值就是上面提到的杀毒软件包名，当app中不存在这些杀毒软件时返回true。

![18](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-18.png) 

由于我们的设备中不存在这些杀毒软件，因此程序执行了onchecked(true)，并最终返回了true。继续跟踪来确认改代码的作用。它是用来确认是否执行更行Google Play，当存在杀毒软件则不进行更新提示，当不存在杀毒软件时则进行更新提示。

![19](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-19.png) 

当我们执行更新时，恶意更新包updatte.apk是存储在这些app的files的文件夹中，当受害者点击update后，会自动为受害者安装恶意程序。

![20](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-20.png) 

安装成功后，它会记录联系人信息。

![21](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-21.png) 

![22](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-22.png) 

![23](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-23.png) 



再使用HttpManager.sendProcess方法用于传输信息。

![24](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-24.png) 

![25](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-25.png) 


发送的数据包如下，上述的四个域名均存在该接口apps/indx.php

![26](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-26.png) 

对update.apk这个恶意程序进行分析，它的代码存在混淆，且再安装时会索取所有的权限。

![27](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-27.png) 

![28](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-28.png) 


同时会再次判断安卓设备中是否存在杀毒软件。

![29](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-29.png) 

还存在一些其他字符的拼接行为。

![30](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-30.png) 

对部分字符内容进行拼接，可以发现一些信息。

![31](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-31.png) 

OooO00o方法负责构建 HTTP POST 请求，发送 GZIP 压缩的 JSON 数据，并处理响应。

![32](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-32.png) 

o00Oo0.OooOo负责获取设备信息，并将数据上传到服务器。

![33](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-33.png) 


![34](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-34.png) 


### 拓线结果分析2

对剩余的两个app进行分析，其中vovanil安装失败，两个app网站js与上述可疑的js一致。

```
https://wobomov.com/
https://voevanil.com/
```

![35](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-35.png) 


同样的套路，安装同样提示需要进行更新。

![36](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-36.png) 

找到提示update的代码位置如下。

![37](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-37.png) 

向上查找它的引用，onResume的方法检查相机权限，并当且未安装制定包名字为GConfig.PT_PACKAGE_NAME的应用时，调用上述showCustomDialog2 的方法显示自定义对话框提示版本更新。

![38](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-38.png) 

跳到声明GConfig.PT_PACKAGE_NAME的地方，PT_PACKAGE_NAME字段通过拼接的方式获取最终包名，经过拼接显示该包名为com.video.graphics，因此该应用会检测是否安装了另一个app，如果没有安装则提示更新为用户下载com.video.graphics并安装，行为模式一致，所以不用做进一步的分析了。

![39](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-39.png) 

### 拓线结果分析3

除了上述软件之外，数据中存在一条APT-C-23针对世界杯的另一个钓鱼网站。

```
wcup22qat.com
```

![40](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-40.png) 


通过FOFA查看该网站原文，除了图标、标题等描述外和IOC展现的特征完全一致。

![41](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-41.png) 

![42](https://github.com/FofaInfo/Awesome-FOFA/blob/b5a26630505bd5e14880fcc8a7121301ca13415e/Storage/APT-C-23/c23-42.png) 


通过分析上述应用攻击模式和代码特征，均符合APT-C-23的特征，因此可以判断上述app以及世界杯相关钓鱼网站均为APT-C-23相关资产。

## 总结

该研究始于对已公开的3条IOC的分析。通过运用FOFA的网络空间测绘技术，我们对这些IOC进行了资产拓线。因为获取的7条结果并未被威胁情报平台所覆盖。因此，我们决定对拓线所得的恶意APP进行逆向分析，以确认拓线结果的真实性。

经过对部分IOC的拓线分析，我们发现该组织通过运营大量约会软件进行窃密。攻击者采用了极具巧妙的手段，如通过提示更新Google  Play商店或提示软件更新，进一步植入恶意应用。相较于IOC来源，这些恶意APP的代码更加隐蔽，难以察觉。

攻击者通过持续优化Android代码，使用户在正常使用过程中很难发现恶意行为，从而导致个人信息泄露。网络安全风险无处不在，像这种披着约会软件的恶意程序，在全世界范围内都是窃密的重灾区。

## 参考文章

1.Arid Viper disguising mobile spyware as updates for non-malicious Android applications
https://blog.talosintelligence.com/arid-viper-mobile-spyware
2.针对巴以地区围绕卡塔尔世界杯的攻击活动
https://mp.weixin.qq.com/s/48Atw1b6Oe7A-vlsKHYWwg
3.APT-C-23（双尾蝎）组织最新攻击活动分析
https://mp.weixin.qq.com/s/NomfjAjGYdsOpLBtiOSZpA

## 附录

IOC：
```
blaxaplayer.com 
bbalignit.com
kora442.com
wcup22qat.com
reblychat.com
wobomov.com
voevanil.com
clemochat.com
wislisapp.com
lapizachat.com
```

威胁情报平台标定情况：
参考文章中的IOC：

| 域名  | 微步  | VirusTotal  | 奇安信  |
|:----------|:----------|:----------|:----------|
| blaxaplayer.com    | -    | 恶意    | APT-C-23    |
| bbalignit.com    | -    | 恶意    | -    |
| kora442.com    | -    | 恶意   | Android.Generic C&C    |



拓线出来的IOC：

| 域名  | 微步  | VirusTotal  | 奇安信  |
|:----------|:----------|:----------|:----------|
| wcup22qat.com    | -    | -    | -    |
| reblychat.com    | -   | -    | -    |
| wobomov.com    | -    | 恶意    | -    |
| voevanil.com    | -    | -    | -   |
| clemochat.com    | -    | -    | -    |
| wislisapp.com    | -    | -    | -   |
| lapizachat.com    | -    | -    | -   |