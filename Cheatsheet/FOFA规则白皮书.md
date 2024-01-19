# 什么是规则？

我们一直在提到一个东西，它就是关键特征。当你具备一个你需要查找信息的关键特征时，你就可以搜到它，比如（IP地址、域名、网站标题、网站图标等等）都可以通过FOFA进行搜索。

那么FOFA针对于这些特征做了什么呢？我们针对于这些资产的多个关键特征进行组合和整理，形成了一个个规则指纹集。

通俗来讲，我们对很多数据做了导航页，或者可以理解为书本的目录。可以帮助用户更快速、准确地找到所需的信息。比如说你想要搜索某个品牌的摄像头，FOFA已经将这些摄像头的特征整理，并形成了一个导航标签，你只需要在搜索框中输入比如海康威视（Hikvision）的摄像头，就可以找到FOFA整理好的各个品牌的摄像头信息。

## 作用场景

指纹规则集的核心能力在于帮助用户在梳理资产时，无需手动收集资产特征，直接搜索指纹规则名称即可，且在搜索中FOFA会直接提供关联推荐功能。同时，用户可以创建自定义规则进行使用，将复杂的查询语法创建成一条规则直接使用，且任何通过平台审核的自定义规则都会根据其重要程度给予创建者不同的F币奖励。

如下图所示，可直接模糊搜索相应规则：

![1](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture1.png)

![2](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture2.png)

具体组件分层有如下5大类，包含软、硬件层面：

|分类|举例|
|:----|:----|
|应用|软件系统等|
|支持|框架、中间件等|
|服务|Web服务器、数据库、服务器等|
|操作系统|Linux、Windows、Unix等|
|硬件|华为交换机、TP-Link路由器、Brother打印机|

FOFA规则也显示在每条数据左下角**IP聚合**按钮中，查询一条数据可以根据规则关联直接通过一键点击到达所关联规则的全部数据，具体展示如下图所示：

![3](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture3.png)

可以看到此条数据中，应用、支持、服务、系统四大类中都已有相关的规则，因为其是网站数据，所以没有硬件规则。为什么要做一个区分呢？

### 规则的种类

规则分为两种，分为**服务规则**与**网站规则**，因为一个IP上会绑定多个host（主机口），并且每个host的内容不会完全一致。

在基于协议返回信息时的不同进行区分，表示展示了具体的区别。

|规则分类|区别|
|:----|:----|
|服务|所有协议称之为服务，http/https协议不包含html源代码|
|网站|http/https抓包，包含html源代码且解析了其他字段|

在FOFA中有3种直观区分两种规则数据的方法：

**方法1：** 在查询关键词时用type和&&（与）进行结合查询：

    * "关键词"&&type="subdomain" 查询结果为网站；
    * "关键词"&&type="service"查询结果为服务。
以port=80为例

port="80" && type="service"为服务

port="80" && type="subdomain"为网站。

**方法2：** 根据搜索结果的右上角栏，可以看到类型分布列表，重点要知道在搜索中，协议=服务。以port=80搜索结果举例，如图5所示：

![4](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture4.png)

ps：如未显示左边列表，请点击右上角all按钮。

**方法3：** 查看每条数据下方是否有 **网站正文** 按钮，有按钮则代表本条数据为网站，没有此按钮则代表本条数据为服务。

展示网站数据，type=subdomain

![5](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture5.png)

展示协议服务数据，type=service

![6](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture6.png)

# 规则可提取字段

在FOFA的查询语法中，只有部分语法可用于规则的字段提取。

其中服务包括3个，网站则包括5个字段，如下图所示：

|服务|网站|
|:----|:----|
|banner：版本信息|server：header中的一个字段，表示相应服务器。server效率＞header|
|cert：证书|title：网页html中提取的标题，有较多价值|
|protocol：协议|body：网页html中提取的主体|
|    |header：HTTP/https协议相应头部|
|    |cert：证书|

需要注意的是，所有的规则都是基于厂商为标准的，所以与个人对象或地理位置相关的字段都不能录规则。

## 规则多的服务/网站

下面将根据规则的多少进行一个排序：

* snmp、PJL；规则最多，很多有设备类型、型号等信息。
    * telnet、rtsp、pptp、smtp、pop3、imap；有规则但比较少。
        * ssh、ftp；有一些规则，大部分没有。
            * 其他；基本上可以忽略。
其中数量最多的规则还是在网站里面，网站可以不看service，直接看subdomain即可。

### 词义解析

|词句|全名|中文名|
|:----|:----|:----|
|snmp|Simple Network Management Protocol|简单网络管理协议|
|PJL|printer-job-language|打印机作业语言|
|telnet|——————————————————|远程终端协议|
|rtsp|Real Time Streaming Protocol|实时流传输协议|
|pptp|Point to Point Tunneling Protocol|点对点隧道协议|
|smtp|simple Mail Transfer Protocol|简单邮件传输协议|
|pop3|Post Office Protocol-Version3|邮局协议版本3|
|imap|Internet Message Access Protocol|交互邮件访问协议|
|ssh|Secure Shell|安全外壳协议|
|ftp|File Transfer Protocol|文件传输协议|

# 规则提取

本章将对不同规则种类的提取流程进行介绍。

## 服务规则提取流程

1. 根据不同协议提取”主关键词“（含有多个字段）。

2. **分析此”关键词“中是否含有能提取信息（类型、厂商和产品类型的字段）？**

    1. 是，提取信息，生成”待查询表达式“（如banner="关键字"）
    2. 否（字段无法提取，如\xff\xfb\x01\xff\xfb\x03\xff\xfd\x18\xff\xfd\x1fC），跳过，提取结束。
3. FOFA中执行查询”待查寻表达式“，是否有查询结果？

    1. 是，记录查询结果数并执行4.
    2. 否，执行2.
4. 查询结果是否最多？

    1. 是，执行5.
    2. 否，执行2.
5. 使用google或Bing搜索引擎查询该字段并确认类型、厂商、厂商官方网站和产品型号。

6. 遵守规则规范，将规则录入。

**注意：3和4可以理解为规则正确性检测步骤。4中“查询结果最多”指提取的信息是最优字段，提取的关键字不同查询结果可能不一样。**

如何提取到有用的关键信息是规则提取的开始工作，也是重中之重，接下来将着重讲解一下如何确定服务规则的关键字。

1. 确认协议中是否有明显的厂商信息，如Huawei、ZTE、HIKVISION等。
2. 确定协议中是否有明显的设备类型信息，如router、switch、printer等。
3. 确认协议中是否有明显的设备型号信息，如DocuPrint CM315/318z、F100-C-G等。
大部分的协议中，关键特征（我们也称之为指纹）只有一个，少部分有两个，极少数会出现三个指纹特征。所以在提取指纹特征的时候，可以根据规则所在层级的不同，录多条规则。

### banner（版本信息）提取信息类别和部分协议示例

1. **只含设备厂商名，如下图所示。**

```plain
banner="MikroTik"
```
![7](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture7.png)
经过Google/Bing查询发现MikroTik是一个造路由器的厂商，此规则可以确认这些IP地址的设备使用了MikroTik路由器。

2. **只含有产品类别，如下图所示。**

```plain
banner="Remote Management Console"
```
![8](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture8.png)

根据banner中的信息可以确定其为远程管理控制类产品。

**3. 含有厂商名和产品型号，如下图所示。**


```plain
banner="SHARP F0-2081"
```
在Google/Bing查询产品类别，为夏普的打印机，型号FO-2081。
![9](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture9.png)

### 不同协议示例

不同协议返回的banner信息不同，选取了部分协议进行举例。

1. **snmp协议（能提取信息较多，且提取的信息大部分可以生成规则）**

分析关键词，能作为可提取信息有产品，厂商“Samsung”和产品型号“X7500GX”，根据这两条信息生成规则。

```plain
banner="Samsung X7500GX"
```
生成的规则正确与否，需FOFA查询确认，这是一个定位了 Samsung的多功能打印机（设备具体分类需Google/Bing搜索分析和确认，型号为X7500GX。

![10](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture10.png)

2. **telnet协议（能提取信息较少，大部分不能生成规则）**

banner返回信息没有可提取的有效信息。

![11](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture11.png)

3. **telnets协议（能提取信息较少，大部分不能生成规则）**

![12](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture12.png)

分析关键词中的信息可以确认其为LANCOM Systems的产品，Google/bing查询得知是LANCOM SYSTEM的VPN-ROUTER，型号为1781VAW。

规则：

```plain
banner="LANCOM 1781VAW" && protocol="telnets"
```
注意：含有ssl加密的协议可以在他的cert中再次提取信息。

4. **pptp协议（大部分只能提取厂商信息）**

![13](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture13.png)

根据banner中的内容可以确认产品商为DrayTek，所以可以为其IP地址打上DrayTek。

### **规则提取不全示例**

提取不全的规则：

```plain
banner="ZXR10 Carrier-class High-end Routing Switch of ZTE Corporation"
```

![14](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture14.png)

分析这个规则，发现ZXR10可能为某个产品的型号/系列号，且含Routing、Switch和ZTX，所以这些关键词中可以认为是中兴的某个设备，其类型可能是switch也可能是routing。

所以关键词通过不同的逻辑组合在FOFA上查询确认，可以拆分为多条规则。

规则1：routing of可以确认其为交换机。

```plain
banner="ZXR10" && banner="Switch"
```

规则2：routing of可以确认其为路由器；switch中含有routing，所以使用排除，得到只含有routing的资产。

```plain
banner="ZXR10" && banner="Router" && banner!="Switch"
```

**注意：为什么不直接使用ZXR10做查询字段？因为在其查询结果中不仅仅只是交换机、路由器，还有其他设备。**

在整个协议规则的提取中，其中最难的部分就是找准关键字，这是整个流程的核心，没有捷径可以走，只能通过多录入，熟能生巧。也要多用google和bing这种搜索引擎进行信息搜集。总的来说即1，找准关键指纹特征；2，找到所属厂商；3，确定其所属的分类；4，确定其所属的分层。

## 网页规则提取流程

1. 根据查询关键词构建第一次查询表达式；
2. FOFA查询；
3. 查询结果中打开IP地址对应的网页信息；
4. 打开网页的源代码；
5. 提取相关信息（相关信息是可以准确定位产品的关键字段）；
6. 再次构建查询表达式，再次执行查询；
7. 规则正确性检查；
    1. 如果是规则，执行8，规则提取结束。
    2. 如果不是规则，再次执行6.
8. 使用google和bing查询关键词，确定产品类别、产品型号、厂商和厂商官网；
9. 遵守规则录入规范，将规则录入。

同样，网站规则的重中之重一样是关键词的提取，通过打开web界面查看是否有明确的设备类型、厂商信息及设备型号信息。提取关键信息后通过google和bing搜索并补全对应的其他信息。

### 网页规则提取实战示例

通过FOFA搜索到网页数据，并点击其Web界面进行打开；

![15](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture15.png)

通过web界面发现”AS-304T“关键字，猜测应该是个型号，并没有发现厂商和类型的关键词；

![16](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture16.png)

使用”AS-304T“关键字在Google/Bing里面的搜索找到它的厂商官网和类别；

![17](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture17.png)

查看body源码（可通过网站正文按钮或web界面打开源代码）发现nasModel ='AS-304T'可以作为规则特征；

![18](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture18.png)

带入语法再次搜索确认规则准确性，验证成功后录入规则。

```plain
body="nasmodel ='AS-304T'"
```

![19](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture19.png)
### 支持语法录入规则示例

1. **title网站标题**

录入形式为title="关键词"，title中含有可能为产品标识的信息，比如语法如下：

```plain
title="CorCloud工业设备远程监控管理系统"
```
![20](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture20.png)

唯一IP地址，打开网页正文。

![21](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture21.png)

虽然body中含有corcloud这个关键词，而且有很多重复的assets/global路径，但是使用body="corcloud"搜索发现，会命中他的官网，而且另一个关键词都有非corcloud产品的资产，并且他的cert和header中没有能够标识的地方，所以只能用网站正文来识别这个产品。

2. **body网站正文**

在网页源代码HTML中，"<body〉</body〉"的内容占比为90%以上，其成为了分析规则的重要组成部分，且在body提取的规则，覆盖的IP较多。

网页中某些元素的路径，例如JS路径、页内图片路径，都有可能成为规则。

```plain
body="/lfradius/"
```
![22](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture22.png)

当然，他也可能是网站链接到其他网站url的一部分。

除此之外，某些网页标签的属性href=、src=、等。

可提取元素为href="/themes/Gargoyle/images/favicon.png"的特征。

需要特别注意的是，在生成body的查询语法时，需要在内容本身包含的引号前加斜杠\进行转义，所以最终语法是：

```plain
body="href=\"/themes/Gargoyle/images/favicon.png\""
```

![23](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture23.png)

3. **header响应头**

Header响应头中一些特征也可以提取到重要信息。

比如WWW-Authenticate字段，w3c认证字段可能有厂商信息和产品信息。或者比如Context-Type中哦可能可以提取到他的开发语言。我们以WWW-Authenticate为例：

```plain
header="Basic realm=\" [JouleTemp - please login as guest or admin] \""
```
![24](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture24.png)

4. **server服务**

因为有些产品会使用特别的服务器，所以header中server字段也能提取。我们针对于server进行单独形成语法的原因是因为其中可提取的信息较多，比使用header效率高一些。

比如查看下面这个信息：

```plain
title="Sunny WebBox"
```

![25](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture25.png)

最终成型的规则是：

```plain
server="Sunny WebBox" || title="Sunny WebBox"
```
5. cert证书部分

证书中也是含有厂商和产品信息比较多的地方。

在利用下面这个语法进行查询的时候，他们返回的信息证书中的commonName都用的是Aircontrol，通过google查询，可以确定这个特征是标识Ubiquiti Networks, Inc.的AirControl系统，类别为工业控制。

```plain
cert="AirControl"
```

![26](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture26.png)

网站规则相对于服务规则而言，会因为网站字段过多所以相对于资产的覆盖面更广，同样的在使用关键字段找到厂商之后可以查看同厂商其他产品的规则，可以记录多条规则。

需要注意的是，最好不要使用server字段录规则，使用header会更加精准，如：header="Server: LHS" || banner="Server: LHS"

此外，网站body字段内容较多可选择作为特征的关键字也多尽可能的规范化！

例如：nasModel ='AS-304T'的搜索语句为：body="nasmodel ='AS-304T'"

没有这种键值对的特征尽可能带上反斜线（转义）；

例如：body="class=\"login-nas-model\">AS-304T"

因为网站数据规则内容很关键，但是也很难查找，所以需要花费较多的时间成本去寻找，且需要不断地进行关键词尝试。

## 规则查重

可以通过两方面进行查重，以上文中提到的H3C Firewall SecPath F100为例进行演示。

**方法1：** 通过点击数据左下方的IP聚合键查看组件分层。

![27](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture27.png)

图中可以明确看到此两条规则已被录入规则库。

**方法2：** 在规则列表直接进行规则搜索。ps.使用搜索功能或录入规则是需要将空格以-进行连接。

![28](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture28.png)

图中可以看到，此条规则已经被录入。

# 规则录入

### 录入界面介绍

自定义规则录入位置为：个人中心→我的规则→添加规则

其中带*为必填项，前文中提到需要收集规则的厂商和分类及其官网是需要规则录入处进行填写。

选择你所录入规则的分类，如：

![29](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture29.png)

根据所演示的进行规则录入，如下图：

![30](https://github.com/FofaInfo/Awesome-FOFA/blob/b17f7b806332106dd3748ec4adabbd220549d540/Storage/Rule_cheatsheet/Picture30.png)

### **厂商网站规范**

* 能寻找到官网：使用其官网网址（截至c om/cn/gov/org等一级域名）， 如 “[https://www.siemens.com/global/en/home.html](https://www.siemens.com/global/en/home.html) ” 使用 [https://www.siemens.com/](https://www.siemens.com/)
* 找不到官网的情况：使用[https://fofa.](https://fofa.so/)info/
* 开源软件：能找到网址的使用其网址，找不到的使用[https://fofa.info/](https://fofa.so/)
**注：网址以斜线（/）结尾**

### 规则名规范

规则名形式：公司简称或缩写-产品（中划线），如：大华-SD65XX HN。 其中，有些没有厂商简称或缩写的使用全称。

	产品知其型号，使用准确型号。

	不知其型号，使用“公司产品”（如：华为-公司产品）。

### 规则名称归一化规范

1. **能找到正确厂商的规则**
* 厂商的规则名称有以下的情况存在：
* logo中，可以直接用的就直接使用。
* 中英文混合，用左侧或者上侧的文字；如果上下或左右内容是一个公司的 整体，则取全部。
* 缩写单词用下划线连接。
* 官网没有logo，使用软硬件产品上的logo。
* logo里面没有文字的使用广而告之的公司名称。
* 被收购的官网打不开或者跳转的使用新的公司名称。
* 如果打不开去bing中或google搜索有wiki展示的logo使用logo。中的厂商 简称。
* apache托管的项目用页面的名称。
* logo 中是公司全称的用常用缩写。
* 被收购的厂商可以考虑保留厂商名称，不过最开头的厂商是新的厂商名称。
2. **没有厂商的规则：**
* 所有的开源产品，都用产品本身。
* 版权信息就是网址认定没有厂商，属于开源产品。
* 如果logo是公司其中一个产品。
* 所有org结尾全是开源产品。
* 网络模块除非有明确公司的，否则都不带公司信息。
**注：公司简称/缩写/全称中有“-”（中划线），使用"_”（下划线） 代替“-”。**

# 问题与解答

**问：什么是资产？**

答：资产是指在计算机体系里的软、硬件设备，由系统（如邮件系统）、 web服务器/中间件（如Apache服务器）、操作系统（如Windows）和硬件（如思科路由器）四部分组成。

**问： 什么是OEM？**

答：OEM汉语意思是“原始设备制造商”，基本含义是定牌生产合作， 俗称“代工”，OEM产品是为品牌厂商度身订造的，生产后也只能使用该 品牌名称，绝对不能冠上生产者自己的名称再进行生产。

**问：为什么会产生OEM的网站？**

答：因为有些企业的一部分业务不会亲自来做，会以“外包”的形式让 其他公司制作。而“外包公司”做一个产品模型，通过修改部分内容使 其达到不同的显示样式。

**问：什么是打标签？**

	答：给IP地址生成一种标签，来准确定位含有的资产，以及使用标签 可以对IP地址分类，例如（有哪些IP地址含有TP-LINK的AC1200路 由器）。

**问：什么是网页标签？**

	答：网页标签指的是网页源代码（html）的一部分。例如：〈title〉 AAA</title>，其被称作网页的title标签，网页标题为AAA。

**问：什么是全文检索？**

	答：全文检索是计算机程序通过扫描文章中的每一个词，对每一个词建立一个索引，指明该词在文章中出现的次数和位置，当用户查询时根据建立 的索引查找，类似于通过字典的检索字表查字的过程。

**问：我在分析网站源码时，遇到部分代码加密，我怎样处理？**

	答:html部分内容被加密（显示没有规律），使用JavaScript代码（网页Script标签内的内容）做提取信息。

**问：我确定我的规则定位的是一个产品，但不知其类型、厂商和型号，那我是否录入？**

	答：否。因为规则的目的是用来准确定位某些业务系统的，所以在只知道某种产品但其他信息不知的情况下将其录入，是毫无意义的。

**问：我不能确定产品具体类型，但可以确定厂商，那是否录入此规则吗？**

答：是。因为此规则可以对与其涵盖的IP地址打厂商标签。

**问：为什么从banner提取的信息在FOFA查询不到？**

	答：分词不正确导致的。因为FOFA搜索引擎里分词符有点（".”）、冒 号（“：”）、中横线（"-”）和斜杠（"/”），当使用除此之外（如：下划线“ "，等）的符号分隔时，会查询不到结果。

**问：产品型号中含有中划线（“-”），遵循录入规范录入时是否将中划线去掉吗？**

	答：否。“产品-型号”的中划线是为统一规则名的格式，型号中含有中划线没有影响。

**问：在规则录入填规则名这步，我知道厂商信息，但不知道具体型号和类型，为什么产品型号要用“公司产品”代替？**

	答：因为使用“公司产品”是为统一规则名的格式，且后期对含有厂商 信息的这些IP进行二次挖掘。

