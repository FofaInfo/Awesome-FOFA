# 通过FOFA进行DeepSeek仿冒资产发现实战

![lQLPJv8zNjAYBX_NArHNBQCwX3hUCxLxUyoHmeComQLNAA_1280_689.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/b504219d-71d6-41d5-9e84-0aeb2cbf533e.png)

# 概述

2025年1月，DeepSeek最新开源大模型R1一经公布便引发全球开发者热议，影响力不断扩大。根据similarweb数据显示，deepseek.com1月总访问量突破27700万。随着DeepSeek热度的连续攀升，网络安全问题也再次进入大众视野，成为公众讨论的热门话题。

根据[友商报告](https://www.qianxin.com/news/detail?news_id=13026)：“仿冒DeepSeek的网站、钓鱼网站已经超过2千个”，这样的数据着实惊人，表明针对DeepSeek的仿冒网站已经形成规模化。实际情况果真如此吗？为了一探究竟，本文将利用FOFA网络空间资产测绘引擎对DeepSeek相关资产进行一次全面的资产测绘，通过对应用分布、仿冒特征等多个方面的分析，发现真实存在的仿冒网站和潜在网络安全威胁。

# DeepSeek非官方相关资产情况

为了匹配所有的DeepSeek相关页面，我们使用 host="deepseek" || title="deepseek" 限定域名或title中包含“deepseek”的目标，结合type="subdomain"筛选所有服务（网站类）资产，构造语法如下:

```plaintext
(host="deepseek" || title="deepseek") && type="subdomain"
```

结果显示，当前DeepSeek相关页面全球总资产数量达到3099个，通过跟踪时间发现近期正在以每日过百的数量快速增加。

![图片 2](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/fadb2da3-9793-45b3-8103-638a0c074c16.png)

## 应用分布

从FOFA匹配的结果中，除了官方资产信息，相关的资产主要分布在6个类：本地搭建与客户端类906个，内容资讯类771个，域名注册商类232个，论坛类152个，虚拟货币相关类62个，仿冒与钓鱼网站类38个。

![图片 3](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/d2744e9b-e9e4-4507-8d43-2b5c4526060b.png)

应用分布饼状图

在本地部署应用中，Ollama正在成为AI本地部署的首选，其资产数量在全网达到了54000。由于很多用户并不是专业开发者，往往会忽视安全问题使用默认配置，例如Ollama在docker默认安装下缺少验证并开放端口到外网，导致大量服务在互联网上裸奔，Chat Nio大量资产可以使用默认管理账户root/chatnio123456直接登录等等。

![图片 4](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/1a542418-d7c5-48c9-95a5-4cd6ea4db262.png)

Ollama搜索结果

如图，某近千用户的AI运营平台可以使用默认管理账户随意进入，价格设定、公告通知可被随意篡改，服务日志存在大量用户数据被泄露。默认配置问题正在成为当前DeepSeek本地部署与客户端类应用的首要威胁。

![图片 2](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/39e7a248-8c2a-4a94-a0a9-6f7a2e4206d5.png)

某管理后台聊天记录泄露

# 仿冒网站资产发现实战

在仿冒网站发现方面，FOFA作为一款强大的互联网资产搜索引擎，提供了丰富的工具和功能，能够帮助用户快速发现和分析仿冒网站。接下来，我们将探讨如何利用FOFA进行有效搜索，寻找到与DeepSeek相关的仿冒网站。

## 基于被仿冒网站寻找样本

仿冒网站一般会提取官网的html源代码，并在此基础上加上恶意诱导用户的链接或输入框进行钓鱼攻击。因此从被仿冒网站的角度提取特征是寻找仿冒网站样本的有效方法。

此次拓线目标为DeepSeek（全称杭州深度求索人工智能基础技术研究有限公司）。通过借助工信部的ICP备案管理系统我们得到DeepSeek的官方域名：deepseek.com。

![图片 2](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/d6232ba2-5696-4ddc-822c-b3dce6745337.png)

beian.miit.gov.cn

仿冒网站，顾名思义，首先要仿，既然仿那么网站的内部信息就是其最有可能被模仿甚至直接使用的对象。通过目标网站我们可以提取出多维度信息。

![图片 3](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/2f11fe2d-19da-4981-9758-3e2f4ced0c14.png)

www.deepseek\[.\]com

### LOGO

LOGO 是网站最醒目的标志性元素。如果一个仿冒网站的 LOGO 不够逼真，就如同画龙未点睛，难以以假乱真。因此，从 LOGO 入手进行分析，是识别仿冒网站的有效方法。

![图片 2](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/61775e8f-e018-4fae-8018-0bd95f0a3b30.png)

在目标站点使用F12打开开发者工具，选择LOGO图片我们可以得到img标签的alt属性和图片的源cdn.deepseek.com/logo.png，为了方便复刻目标，搭建者往往会直接采用目标的图片源或者标签属性，因此以这两个元素构造规则：

```plaintext
body="alt=\"DeepSeek Logo\"" || body="cdn.deepseek.com/logo.png"
```

可以看到结果有165条，但是还存在很多引用到deepseek的网站，所以选择deepseek的icon再次进行搜索。

![图片 4](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/657c3a6f-a066-4ccf-9556-487e8f3f9e5f.png)

构造规则：

```plaintext
(body="alt=\"DeepSeek Logo\"" || body="cdn.deepseek.com/logo.png") && icon_hash="-118645384"
```

![图片 5](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/e1a5191a-d12c-4e7c-a3db-3c3e2816abbc.png)

可以看到匹配结果降到了27条，选择判定目标deepseek-v3\[.\]live。

![图片 6](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/59f553ed-4d24-48a5-b524-fb4de3bf2889.png)

deepseek-v3\[.\]live

可以看到其内部完全仿照了DeepSeek官网的布局，但是Start Now和Get DeepSeek App被替换成了Connect Wallet。

最后通过人工筛选出如下仿冒网站：

*   deepseek-v3\[.\]live
    
*   deepseek-umxvljvoilcnxih.app-tools\[.\]info
    
*   deepsekk\[.\]sbs
    
*   deepseekonchain\[.\]com
    
*   deepseek-6phm9gg3zoacooy.app-tools\[.\]info
    

### 备案号与版权声明

网站备案号是工业和信息化部关于网站备案管理系统颁发的网络信息许可证号码，通过网站备案号可以找到该网站所属的公司。仿冒者常常直接使用被仿冒者的备案号和版权声明来模仿受害者。

备案号和版权声明往往在一个网站的最下侧，通过官方网站的中文页面和英文页面我们可以得到版权声明“2025 杭州深度求索人工智能基础技术研究有限公司 版权所有”“2025 DeepSeek. All rights reserved.”和备案号“浙ICP备2023025841号”。

![图片 7](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/86d36457-ecc4-4bef-ab11-76de46b0519c.png)

根据已知信息，我们可以构造规则：

```plaintext
body="2025 杭州深度求索人工智能基础技术研究有限公司 版权所有" || body="2025 DeepSeek. All rights reserved." || body="浙ICP备2023025841号"
```

可以看到有35条匹配结果。

![图片 8](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/6d7b3140-b253-48c4-a5ba-85434c0a51da.png)

这里以chatbot-deepseek\[.\]com为例，该网站通过模仿DeepSeek首页将登录选项替换成文件下载。解压后存在恶意文件。

![图片 2](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/ad4a17c8-8190-40fb-9f80-c91393b459f2.png)

chatbot-deepseek\[.\]com

![图片 3](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/4fc22908-a14b-4724-9495-cfd7e87dbeba.png)

压缩包内容

另一例替换页面元素为下载可执行文件的恶意网站，发现其在后续执行中从github下载恶意程序：

![图片 1](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/a37bf1ae-5262-4cbd-b19f-77f04b1e3262.png)

home-deepseek\[.\]com

![图片 2](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/72c97eee-b402-40e6-bd6c-38ab24c83033.png)

恶意bat程序

最终筛选出以下仿冒网站（后面结果不包含已筛选出的仿冒网站）：

*   chatbot-deepseek\[.\]com
    
*   deepseekapk\[.\]pro
    
*   deepseekonchain\[.\]com
    
*   deepseek-r1\[.\]xyz
    
*   deepseekapp\[.\]cloud
    
*   intelligence-deepseek\[.\]com
    
*   deepseekaz\[.\]top
    
*   home-deepseek\[.\]com
    
*   r1platform-deepseek\[.\]com
    
*   www.deepseek\[.\]family
    

### 产品信息

一个公司的核心产品介绍往往也是被仿冒的重灾区，仿冒者为了仿照目标往往会选取最核心的产品介绍进行拷贝。

显然，deepseek官方最核心的产品就是deepseek模型，其介绍就在官网的首页。

![图片 9](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/4faf22bd-ac75-4883-84fb-f0ab3663a4ac.png)

从产品介绍提取规则：

```plaintext
body="DeepSeek-R1 is now live and open source, rivaling OpenAI's Model o1. Available on web, app, and API. Click for details." || body="DeepSeek-V3 achieves a significant breakthrough in inference speed over previous models. "
```

![图片 10](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/aff18823-9096-4902-94a2-e166315719ae.png)

以deepseekllm\[.\]pro为例，目标以DeepSeek官方的名义发行虚拟货币，其性质明显具有诈骗意味。

![图片 4](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/5987d4d1-7379-409d-8a27-b73c50271b42.png)

deepseekllm\[.\]pro

![图片 5](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/c642391d-7384-435f-ba78-d3be48eb8b66.png)

页面带有official等误导词汇

最终筛选出如下仿冒网站：

*   deepseekllm\[.\]pro
    
*   deepseeklogin\[.\]com
    

### 其他页面

除了首页信息，其他页面也是潜在被仿冒的对象。而登录页面和下载页面往往是重灾区。这里通过DeepSeek首页下侧导航栏获取更多的子页面。

![图片 11](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/aa4f0938-1226-4d31-8a75-5e18afd4fdbe.png)

#### download.deepseek.com

该页面是deepseek的下载导航站。

![图片 12](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/565c4b37-b568-4020-a992-eee08bc4f316.png)

根据下载导航站中的信息构造规则：

```plaintext
body="下载 DeepSeek App" || title="DeepSeek App" || body="Download DeepSeek App now to experience intelligent AI assistant."
```

![图片 13](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/57f05330-f2e3-4789-98ed-7b59778f8728.png)

目前暂未发现仿照官方下载页面的网站，但是存在一些页面利用deepseek关键词将下载链接导向自己的文件站而不是官方，这种倒流方式存在明显误导倾向，其下载的文件也存在附带病毒的可能。

最终筛选仿冒网站如下：

*   deepseekapp\[.\]download
    
*   deepseekdownload\[.\]org
    
*   deepseekapp\[.\]in
    
*   deepseekapp\[.\]info
    

## 基于互联网公开信息寻找样本

通过互联网公开信息可以获取到仿冒网站样本。以这些样本为基础进行分析同样可以拓线出有价值的结果。

一些样本获取渠道：

*   [12321网络不良与垃圾信息举报受理中心](https://www.12321.cn/)
    
*   [PhishTank](https://phishtank.org/)
    
*   [非法仿冒机构信息公示-中国证券业协会](https://www.sac.net.cn/)
    
*   微信公众号或其他博客的公开文章
    

通过公开来源获得以下仿冒网站：

*   deepseeklogins\[.\]com
    
*   deepseeklogin\[.\]us
    
*   deepseek.net\[.\]ua
    

根据以上分析最终获得24条初始样本。

# 分析样本特征&拓线

当获取到初始样本后可以根据已知的样本特征进行拓线，从而获得到更多的仿冒网站。

## 基于FOFA规则特征提取进行拓线

这里的基本思路是：拿到样本IP后，查看该样本的源码内容，提取出该类网站共有的特征，构造FOFA规则进行匹配。

特征的提取一般围绕两个方面：一方面是频繁出现的关键词，代表仿冒的倾向，例如银行仿冒网站常常出现“贷款”，通过附加关键词可能得到更多的相关网站；另一方面是网页中的恶意代码，通过恶意代码可以快速提取出部署者所惯有的特征，例如某仿冒者管用手法是在仿冒网站中增加导向恶意网站的js语句，那么根据该js语句就能快速筛选出一批该行为者的部署站点，实现行为画像。

### DeepSeek+Wallet

在已获得的样本中发现存在一些仿冒网站结合deepseek关键词引导用户登录在线钱包。

例如：www.deepseekonchain\[.\]com，这些网站都频繁提到“钱包”，因此提取“wallet”、“coin”和“Total supply”作为关键词。

![图片 14](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/e43fde23-dcb0-4910-967f-29cf932d6c30.png)

www.deepseekonchain\[.\]com

构造规则：

```plaintext
(title="deepseek" || host="deepseek") && (body="wallet" || body="coin" || body="Total supply")
```

结果如下：

![图片 16](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/1737547d-5ab7-475e-a8ed-64dbc2643439.png)

对结果进行分析发现存在大量以deepseek官方名义或借deepseek热度发行的虚拟货币。由于虚拟货币的性质，发行者往往通过夸大货币前景与虚假背书的方式进行实际意义上的诈骗，此外，还存在一些以Airdrop为名义的诈骗网站，如deep-whitelist\[.\]com提示上传recovery phrase连接钱包就是典型的空投诈骗。

![图片 7](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/4296a5f1-c46c-48f4-ab72-943889b937f0.png)

deep-whitelist\[.\]com

虚拟货币相关拓线结果:

*   deepseekt\[.\]org
    
*   www.agent-alessa\[.\]xyz
    
*   deepinu\[.\]xyz
    
*   deepseekllm\[.\]pro
    
*   deepseeksol\[.\]club
    
*   deepseekfwog\[.\]site
    
*   deep-whitelist\[.\]com
    
*   deepseekai\[.\]club
    
*   deepseek-wl\[.\]com
    
*   www.deepseekceo\[.\]vip
    
*   34.92.225\[.\]248
    
*   deepseekceo\[.\]vip
    
*   deepseek2025\[.\]com
    
*   deepseek2025\[.\]xyz
    
*   deepseeklab\[.\]xyz
    
*   jlt-ai\[.\]pro
    
*   deepseekkb\[.\]com
    

## 基于FOFA的统计聚合面板功能进行拓线

对于某一仿冒分类的网站，我们可以提取其粗略规则，再加上地区或host等限制，然后利用fofa的统计聚合面板功能，观察其title和域名快捷判断哪些可能是仿冒网站，然后再做具体分析。由于仿冒网站往往为了流量会同时部署大量相似的网站，而利用fofa特有的fid指纹可以实现对相似网站进行聚合。

这里构造规则：

```plaintext
title="deepseek" && (region="HK" || region="TW" || country="US" || country="ZA" || country="SG" || country="JP") && domain!="deepseek.com"
```

点击网站指纹排名开启聚合面板。

![图片 8](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/382029e6-9395-486c-a135-1820b90aff25.png)

通过fofa的聚合面板进一步的筛选，浏览title内容快速判断是否可能为仿冒，然后点击访问进一步判断。

![图片 21](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/2ea6bdcf-9fed-465d-b7de-20a8af15c82b.png)

通过该方法最终获得虚拟货币相关网站：

*   missdeepai\[.\]club
    
*   agent-alessa\[.\]xyz
    
*   deepseek-buy\[.\]click
    

![图片 8](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/553f1573-d6d5-4399-be33-20bfeae6fdf2.png)

deepseek-buy\[.\]click

## 基于FOFA ICON Hash进行拓线

对于某类具有图标的仿冒网站，我们可以从FOFA上提取其对应的Icon Hash，通过图标聚类进行拓线。

![图片 1](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/8a71f24d-4ca0-429e-82a9-80ee72b83d7e.png)

搜索当前目标时可以看到大量与DeepSeek图标相似的ICON，这时我们可以选择相似的图标进行批量搜索。在搜索结果中可以发现之前得到的deepseek-v3\[.\]live正在其中。

![图片 2](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/40c9a120-0059-4632-b481-ae8d85e6c5b6.png)

除了利用FOFA进行资产测绘外，我们还可以通过其他外部资源和工具来补充拓线，例如当同C段多个嫌疑IP时主动扫描C段、使用WHOIS查询注册者信息追踪关联域名、检查无效证书，以及定期筛选新增的近似域名。

最终通过拓线我们获得到20个相关虚拟货币域名。

将43个站点结果进行聚合，我们发现仿冒网站和空气币网站尤其喜爱通过Cloudflare隐蔽真实IP与规避跟踪。

![图片 1](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/2c23edb8-ddaf-4326-9592-88374760ce65.png)

43个结果的Cloud聚合统计

我们通过同样的方式对官方资产进行Cloud聚合：

![图片 29](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOLXz3RD8mOpz2/img/23dc46c9-a901-4fc4-a714-9e6457897176.png)

官方资产云厂商聚合图

相关IP结果去重后分布如下：

*   华为云：17个（含核心业务域名 api.deepseek.com、chat.deepseek.com 等）
    
*   Cloudflare：4个
    
*   CloudFront：2个
    
*   阿里云：1个
    
*   腾讯云：1个
    

其中，14个IP被标记为CDN节点，覆盖华为云（8个）、Cloudflare（4个）及CloudFront（2个）。核心业务系统（如API服务和chat服务）均部署于华为云，承载了流量压力的主要部分。从聚合统计结果我们能够得出，服务所在的云厂商也是判断可疑网站的重要依据。

# 总结

基于资产测绘与拓线分析实战，我们最终发现24个有效存活（截至定稿前）的仿冒网站和潜在威胁网站，以及20个虚拟货币相关的站点，这一结果与友商所宣称的“仿冒 DeepSeek的网站、钓鱼网站已经超过2千个”的结论存在显著差距，造成这种差异的核心原因在于对仿冒网站的定义不同。我们希望通过更为严谨的表述，避免对公众的风险认知产生误导。

聚焦DeepSeek现象级增长背后的安全风险，我们梳理出当下三大核心问题：

1. 安全基线隐患：产品低部署成本特性在吸引海量用户的同时，也暴露出用户群体普遍缺乏安全配置意识的问题。这一问题形成了算力盗用、数据泄露等安全事件的温床。

2. 漏洞影响放大：客户端规模的高速增长使潜在漏洞的威胁度大幅攀升。攻击者可借助自动化工具构建攻击链，实现批量化攻击，为大规模安全事件埋下了隐患。

3. 仿冒生态威胁：DeepSeek 的热度同样吸引了一些威胁行为者的关注，这些仿冒网站不仅存在对用户的误导，还可能隐藏威胁活动，例如信息窃取，这增加了网络环境的复杂性。

面对复杂的安全态势，FOFA 将持续发挥重要作用，通过监测和识别仿冒网站及网络威胁，为客户提供真实有效的安全情报。同时，我们呼吁大众增强安全意识，定期检查和更新安全配置，以降低被攻击的风险。

# References

[https://www.qianxin.com/news/detail?news\_id=13026](https://www.qianxin.com/news/detail?news\_id=13026)

# 附录

**仿冒网站和潜在威胁网站：**

www.deepseek\[.\]family

r1platform-deepseek\[.\]com

deepseek-v3\[.\]live

deepseek-umxvljvoilcnxih.app-tools\[.\]info

deepsekk\[.\]sbs

deepseekonchain\[.\]com

deepseek-6phm9gg3zoacooy.app-tools\[.\]info

chatbot-deepseek\[.\]com

deepseekapk\[.\]pro

deepseek-r1\[.\]xyz

deepseekapp\[.\]cloud

intelligence-deepseek\[.\]com

deepseekllm\[.\]pro

deepseeklogin\[.\]com

deepseekapp\[.\]download

deepseekdownload\[.\]org

deepseekapp\[.\]in

deepseekapp\[.\]info

deepseeklogins\[.\]com

deepseeklogin\[.\]us

deepseek.net\[.\]ua

deepseekaz\[.\]top

home-deepseek\[.\]com

**虚拟货币相关网站：**

deepseekt\[.\]org

www.agent-alessa\[.\]xyz

deepinu\[.\]xyz

deepseekllm\[.\]pro

deepseeksol\[.\]club

deepseekfwog\[.\]site

deep-whitelist\[.\]com

deepseekai\[.\]club

deepseek-wl\[.\]com

www.deepseekceo\[.\]vip

34.92.225\[.\]248

deepseekceo\[.\]vip

deepseek2025\[.\]com

deepseek2025\[.\]xyz

deepseeklab\[.\]xyz

jlt-ai\[.\]pro

missdeepai\[.\]club

agent-alessa\[.\]xyz

deepseek-buy\[.\]click

deepseekkb\[.\]com

# tip

后续我们将持续对文章内容进行优化与完善，最新版本可以在 [https://github.com/FofaInfo/Awesome-FOFA](https://github.com/FofaInfo/Awesome-FOFA) 中查看，欢迎大家随时关注并提出宝贵建议~