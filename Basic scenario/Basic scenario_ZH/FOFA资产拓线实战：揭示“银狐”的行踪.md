# FOFA资产拓线实战：揭示“银狐”的行踪

## 写在前面

FOFA 在资产和线索拓线方面非常有优势，但是很多师傅并不是很理解如何操作，及应该提取哪些特征。需要注意的线索来自哪里？用哪种语法可以实现拓线？          

主要还是缺失线索查找的思路和方法，如何将线索和语法活学活用的使用起来，完成资产收集和线索拓线是网络安全不可或缺的重要一环。      

今天我们将一步一步地探讨这个话题，以“银狐”组织为例，通过多个实例演示如何使用 FOFA 进一步拓展样本资产，以扩大我们的战果！ 

## 案例介绍 

“银狐”是一个已经被广泛地去中心化传播的黑客工具，根据微步的研究发现，任何攻击者都可以获取和使用，目前检测到的活跃且被公开的团伙多达5个，还有更多不知名或者未公开黑产在持续使用银狐木马。

"银狐" 木马是一种专门针对企事业单位的管理、财务、销售和电商卖家进行钓鱼攻击的恶意软件。攻击者通过远程控制木马获取计算机控制权限，长期滞留并监控用户操作，然后使用聊天工具软件进行诈骗。

常见的传播路径有三种：    

1. **即时通信传播**，使用QQ、微信等发送诱导性文件或链接。
2. **钓鱼网站传播**，伪装成税务机关的网站，使用微信钓鱼。
3. **虚假软件传播**，伪装成常见软件，购买流量在搜索引擎上传播。
    
此类木马使用广告软件进行捆绑式推广，利用复杂形式的白利用，并呈现多阶段的投递方式。          
本文将会聚焦于FOFA的拓线能力，分享当拿到样本资产后，如何使用FOFA将战果扩大化。

## 样本拓线思路展示

样本一来源于360沙箱云报告：《360沙箱提醒您：注意防范“银狐”木马》

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/1-silverfox.png)

根据已知线索，我们获得了域名"b.zjsdfg.cn"，和其解析的 IP 地址为 "211.99.98.76"。同时，根据截图的线索，我们了解到他的原文包含关键词 "钉钉电脑版" 和 "为企业解决办公协同问题"。   

将这些线索转化为对应的FOFA语法查询如下：

```
ip="211.99.98.76"

body="钉钉电脑版" && body="为企业解决办公协同问题"
```

首先，让我们通过 FOFA 进行 IP 查询，看看是否能找到与这个 IP 相关的信息。       

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/2-silverfox.png)

经过 IP 查询，我们确认该 IP 下的疑似钓鱼网站标题为 "钉钉电脑版-应用市场下载"，但这不是我们的重点。 

重点在于，在相同的 IP 下，我们发现了新的线索："down.cstny.xyz"。您可以使用以下查询语法来检查与该 IP 相关的更多信息：

`ip="211.99.98.76"`


![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/3-silverfox.png)


使用资产关联的SDK特征进行查询，我们获得了 24 条记录，其中包括 6 个独立的 IP 地址。

`sdk_hash="5IcXyBJ8QrxlDLQFTl2DCHG0Z42JHfk6"`

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/4-silverfox.png)

继续深挖他的其他特征，标题和body中的关键词，有18条资产和6个独立ip命中。

`title="百度网盘电脑版官方下载" && body="模板"`

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/5-silverfox.png)

我们根据搜到的资产，查看FOFA保存的网站原文，可以看到很有意思的变化趋势。

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/6-silverfox.png)

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/7-silverfox.png)

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/8--silverfox.png)

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/9-silverfox.png)


从我们打开的网页原文中，我们可以清晰地观察到木马的下载链接及其变化过程。我们可以看到它从本地存储过渡到了云存储，除此之外，还出现了明显的相似特征。

这些相似特征可以进一步扩大我们的信息收集。在原始信息中，我们可以看到一些显著的相同特征，如 "downapp"、"count"、"fileurl" 等。此外，它们还共享相同的 "js_name" 特征，这也已经显露出来。

因此，我们可以继续使用以下搜索语法来深入查询，这个查询结果命中了 28 条数据和 7 个独立的 IP 地址。

`js_name="static/js/quanzhan.js" && body="downapp" `

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/10-silverfox.png)

在完成了ip的线索拓线后，我们将聚焦在第二个线索，即样本原始页面所拥有的特征进行提取：

`body="钉钉电脑版" && body="为企业解决办公协同问题"`

而且这次根据结果，我们发现了多条新的特征资产。通过以下标题：

`title="钉钉电脑版-鲁大师应用市场下载"`

我们获得了 3 条搜索结果，这将帮助我们更深入地了解 "钉钉电脑版" 相关的信息。

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/11-silverfox.png)

此外，我们还发现了新的 IP 地址：43.248.190.199。通过对该 IP 进行搜索，我们发现了新的特征线索，其中新的标题线索为：

`title="OBS录屏⼤师 _官⽹_简单易⽤的超清录屏软件_电脑录屏⼤师"`


这个新线索将有助于进一步扩大我们的信息收集和分析，获取到3个独立域名。

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/12-silverfox.png)

通过分析他body中的js_md5 值加上上面发现的该工具的通用特征可以进一步进行语法拼接。

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/13-silverfox.png)

构造出来新的钓鱼网站查询语法是：

`js_md5="a13f7f208ba534681deadb1ec7a2e54a" && body="downapp" && (body="count" || body="fileUrl")`

搜索结果中又出现了新的标题线索，我们通过使用标题语法继续搜索，获取到了18条独立子域名，2个独立ip。

`title="剪影·拍拍_官⽹_简单好⽤的视频剪辑软件_电脑视频剪辑"`

检索到的结果如下：


```
jy15.lianhuawangluo03.xyz
jy.hehuashangwu04.xyz
jy.hehuashangwu01.xyz
jy.lianhuawangluo17.xyz
ad.jianying-pro.cc
jy15.lianhuawangluo09.xyz
jy.hehuashangwu04.xyz
jy.hehuashangwu01.xyz
jy.hecishangwu.xyz
jy.hecishangwu.xyz
jy.lianhuawangluo17.xyz
150.109.76.206
124.156.185.102
jy.hehuashangwu20.xyz
jy1.hehuashangwu11.xyz
jy15.lianhuawangluo09.xyz
ad.jy2023.cc
jy15.lianhuawangluo03.xyz
jy.hehuashangwu20.xyz
jy1.hehuashangwu11.xyz
124.156.134.59
```

在观察结果这个结果时，我们能发现了一些规则，这些域名大多都是以 "**hehuashangwu**" 和 "**lianhuawangluo**" 后面跟着两位数字的域名组合方式。

那么我们可以使用**FOFA的模糊搜索功能**来构建新的查询语法，命中107条资产，34个独立ip。

`host*="*.lianhuawangluo??.xyz" || host*="*.hehuashangwu??.xyz"
`

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/14-silverfox.png)

到这里根据这个样本的资产，我们已经完成了很多特征的提取，我们最后在根据已知特征构建新的语法，梭哈一波。

`body="function downapp" && (body="count" || body="fileUrl") && (body="exe" || body="msi" || body="zip")`

成功查到136条资产，35个独立ip，通过搜索结果来看出现了更多的钓鱼网站，其中有抖音桌面、WPS、百度网盘、迅雷、winrar等等，这些钓鱼网站都伪装成与该木马相关，这是一个有趣的发现。

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/15-silverfox.png)

好了，我们整理整理思路，继续进行另一个样本的探索。
         
样本二来源于火绒报告：《毒鼠”后门病毒再升级 通过伪造官网传播》

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/16-silverfox.png)

我们通过 FOFA 平台查询到了与该资产相关的信息。这次，我们将直接使用资产上标记的 fid 值进行拓线：

`fid="VAaTqhs0Tw/lp4YjN7vWlw=="`

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/17-silverfox.png)

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/18-silverfox.png)

根据当前资产线索，我们成功查询到了 12 条资产和 4 个独立的 IP 地址。通过这些信息，我们获得了木马的下载地址：

`hxxps://vv[i]ipp.xykr[s]ii.cn/tsetup-x64Chinese.exe`

通过解析它的ip，可以通过开源平台查询whois绑定的用户身份信息等，当然我们今天只使用FOFA进行线索扩充，就不进一步的演示其他的内容啦。

我们继续探索样本三，来自于微步在线的报告：《因势象形：警惕银狐组织发起新一轮钓鱼攻击》。

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/19-silverfox.png)

通过他的域名luthj.sbs进行查询，向之前一样获取其资产的关键特征进行裂变。 

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/20-silverfox.png)

当然不管是通过FOFA特有的FID指纹，还是使用上面提到的SDK特征都可以进行进一步的进行线索扩充，这次我们选择提取他body信息中的关键特征。

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/21-silverfox.png)

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/22-silverfox.png)


根据提取的特征，我们生成了以下搜索语句，找到了109条结果和10个独立的IP地址。

`body="暗影⽹络⽂件传输系统 kiftd v1.1.0-RELEASE" && body="票据服务"`

![](https://github.com/FofaInfo/Awesome-FOFA/blob/e42d8cf91263f8c55da353ed75aafe198c77bcf4/Storage/23-silverfox.png)


# 总结

银狐这个工具的使用广泛，其主要思路是通过伪装成常见软件的下载页面制作钓鱼网站，然后通过钓鱼邮件的形式发送到受害者的邮箱，诱导他们下载木马文件。

通过对一系列资产的拓线发现，一些相关的钓鱼网站域名遵循着一定的规则，可以根据这些规则来拓线到相应的钓鱼网站资产。

此外，不同类型的钓鱼网站通常包含有相同的关键词，如 "js_name" 或 "downapp"，这提示这些钓鱼网站可能来自同一个组织。

在这次真实的银狐案例分析中，我们使用了多种 FOFA 查询语法，包括语法 ip、body、host、sdk_hash、title、js_name、js_md5、fid 以及模糊匹配功能，来进行资产拓线。

整个拓线流程依赖于 FOFA 的强大搜索功能，但更重要的是发现线索和思考的方法。希望这个案例能帮助师傅们更好地利用 FOFA 进行资产拓线和信息收集，并最大化地发挥 FOFA 的价值。

**本次案例拓线后去重结果的如下：**

```
103.143.159.111
103.143.159.94
103.143.159.98
103.15.104.242
103.163.46.172
103.253.13.59
103.36.166.149
104.21.15.115
104.21.16.191
104.21.30.24
104.21.33.112
104.21.4.219
104.21.44.41
104.21.50.201
104.21.63.17
104.21.67.152
104.21.83.241
104.21.89.234
110.42.2.115
114.134.189.99
114.29.254.8
114.29.255.45
121.37.160.16
123.60.48.116
124.156.134.59
124.156.185.102
150.109.68.68
150.109.76.206
154.213.26.46
156.241.132.69
172.67.130.220
172.67.140.212
172.67.143.80
172.67.148.236
172.67.150.109
172.67.161.227
172.67.166.144
172.67.177.134
172.67.178.193
172.67.183.119
172.67.192.54
172.67.194.205
172.67.197.152
172.67.202.4
18.166.188.156
18.228.225.125
206.238.115.108
211.99.98.76
211.99.99.150
23.225.205.171
23.225.205.173
23.225.7.163
23.225.7.166
2345zip.hehuashangwu02.xyz
2345zip.hehuashangwu02.xyz:21
361.ploos.top
38.47.106.189
43.129.172.114
43.154.136.10
43.154.192.213
43.154.23.202
43.154.49.3
43.154.61.105
43.154.80.187
43.155.69.56
43.248.190.199
45.116.166.251
45.116.166.27
45.116.166.40
45.125.51.25
45.125.51.7
45.204.83.22
45.204.83.42
47.240.76.132
47.242.43.15
59.56.110.104
60.204.174.33
8.217.38.145
96.43.110.12
96.43.110.26
96.43.110.27
a.fhuehuy7.cn
a.zjsdfg.cn
a1.nykoy06.top
aa.nykoy01.shop
aa1.sdsl07.top
aa2.sdsl07.top
aa3.sdsl07.top
ab.nykoy01.shop
ad.jianying-pro.cc
ad.jy2023.cc
ad.nykoy01.shop
al.fapiaozx.com
antey.sbs
asdfghwin02.hhzef.cn
asdfwspp03.whroz.cn
atjzw.sbs
autodiscover.atjzw.sbs
autodiscover.ghfdt.sbs
autodiscover.ijytr.sbs
autodiscover.ktfgr.sbs
autodiscover.nefgs.sbs
autodiscover.pjuyt.sbs
autodiscover.vrheg.sbs
autodiscover.yrfgd.sbs
b.fheuheg8.cn
b1.nykoy06.top
c.zjsdfg.cn
cff01.027jly.com
cpanel.ghfdt.sbs
cpanel.ijytr.sbs
cpanel.ktfgr.sbs
cpanel.nefgs.sbs
cpanel.pjuyt.sbs
cpanel.vrheg.sbs
cpanel.yrfgd.sbs
cpcalendars.atjzw.sbs
cpcalendars.ghfdt.sbs
cpcalendars.ijytr.sbs
cpcalendars.ktfgr.sbs
cpcalendars.nefgs.sbs
cpcalendars.pjuyt.sbs
cpcalendars.vrheg.sbs
cpcalendars.yrfgd.sbs
cpcontacts.atjzw.sbs
cpcontacts.ghfdt.sbs
cpcontacts.ijytr.sbs
cpcontacts.ktfgr.sbs
cpcontacts.nefgs.sbs
cpcontacts.pjuyt.sbs
cpcontacts.vrheg.sbs
cw.mandongzuoxinxi.cn
dd.sdsl06.top
dd001.wolfing1235.cn
ding.qdjyswkj.com
ding.yincaitong.com.cn
dingd.wolfing1234.cn
dingding.fjeihg3.cn
dou.qdjyswkj.com
doushop.lianhuawangluo07.xyz
down.cstny.xyz
down.qianniu.icu
down.qianniu2023.cc
dsf01.whnmzw.cn
fapiaoi.com
fwiop.club
fyjughk.top
fyjughk.xyz
fz.mandongzuoxinxi.cn
ghfdt.sbs
hfmzkj.top
hjklnmwps04.hhzef.cn
huiyi.sxnjal.cn
huiyix.icu
hy.fjehh9.cn
ijytr.sbs
jetdh.sbs
jhges.sbs
jy.hecishangwu.xyz
jy.hehuashangwu01.xyz
jy.hehuashangwu04.xyz
jy.hehuashangwu20.xyz
jy.lianhuawangluo17.xyz
jy1.hehuashangwu11.xyz
jy15.lianhuawangluo03.xyz
jy15.lianhuawangluo09.xyz
ktedy.sbs
ktfgr.sbs
kyy.fdjh7889.top
lian.qianmouren.top
luthj.sbs
m.atjzw.sbs
m.ghfdt.sbs
m.ijytr.sbs
m.ktfgr.sbs
m.nefgs.sbs
m.pjuyt.sbs
m.vrheg.sbs
m.yrfgd.sbs
mail.ghfdt.sbs
mail.ktfgr.sbs
mail.nefgs.sbs
mail.pjuyt.sbs
mail.vrheg.sbs
nefgs.sbs
office.hehuashangwu07.xyz
office1.lianhuawangluo20.xyz
office2.hehuashangwu13.xyz
office2.hehuashangwu20.xyz
office2.lianhuawangluo02.xyz
office2.lianhuawangluo08.xyz
office22.lianhuawangluo15.xyz
p.fjehyy6.cn
p.fjeihg9.cn
p.njcsdaq.top
pdf.nykoy06.life
pdf.ogkkl.top
piaojufw.cyou
ppdf.nykoy01.top
pssabe.mboworld.com
pyxiaoyuan.com
qn.hflh2.cn
qwertps01.whroz.cn
rar2.hehuashangwu16.xyz
sa.asog510.com
sdf.kemanxing.top
sg.pdfqo05.top
sg.yysk982.com
shanghu.hehuashangwu12.xyz
smtp.atjzw.sbs
smtp.ghfdt.sbs
smtp.ijytr.sbs
smtp.ktfgr.sbs
smtp.nefgs.sbs
smtp.pjuyt.sbs
smtp.vrheg.sbs
smtp.yrfgd.sbs
sogou1.hehuashangwu10.xyz
sogou2.lianhuawangluo04.xyz
sogou2.lianhuawangluo10.xyz
sougou22.lianhuawangluo24.xyz
sougou22.lianhuawangluo25.xyz
sss.fhgges.cn
telegramde.sbs
telegramvesl.org
telegrrram.com
txhy.qfmailw.com
tyujlih.icu
urbgv.sbs
vrheg.sbs
w.fegee8.cn
w.iejhfh5.cn
wang.hfqc3.cn
wang.iowxk1456.top
wang.mboworld.com
wang.yyghzmd.cn
wangp.winaaa.top
webdisk.atjzw.sbs
webdisk.ghfdt.sbs
webdisk.ijytr.sbs
webdisk.ktfgr.sbs
webdisk.nefgs.sbs
webdisk.pjuyt.sbs
webdisk.vrheg.sbs
webdisk.yrfgd.sbs
webmail.atjzw.sbs
webmail.ijytr.sbs
webmail.nefgs.sbs
webmail.pjuyt.sbs
webmail.vrheg.sbs
webmail.yrfgd.sbs
wf1.sdsl02.top
win.tzhzkj.com
winar.nykoy01.top
winrar.nykoy06.life
wkl.nykoy01.top
wp.fhufe9.cn
wp.herdc.com
wp.hflh2.cn
wp.hfmzwl.top
wp.hfyx3.cn
wp.pdfqo05.top
wp.wsp51si.top
wp.ycmzwy.cn
wppp1.hfmzwlkj.top
wps.nykoy06.life
wps.qdjyswkj.com
wps2.hehuashangwu05.xyz
wpss.nykoy01.top
wwp.sagh5293.top
wwps.tzhzkj.com
www.atjzw.sbs
www.fwiop.club
www.ghfdt.sbs
www.hehuashangwu02.xyz
www.hehuashangwu04.xyz
www.hehuashangwu05.xyz
www.hehuashangwu06.xyz
www.hehuashangwu07.xyz
www.hehuashangwu08.xyz
www.hehuashangwu09.xyz
www.hehuashangwu14.xyz
www.hehuashangwu19.xyz
www.hehuashangwu20.xyz
www.ijytr.sbs
www.ktfgr.sbs
www.lianhuawangluo13.xyz
www.lianhuawangluo14.xyz
www.lianhuawangluo20.xyz
www.lianhuawangluo21.xyz
www.lianhuawangluo22.xyz
www.lianhuawangluo24.xyz
www.lianhuawangluo28.xyz
www.lianhuawangluo29.xyz
www.lianhuawangluo38.xyz
www.lianhuawangluo39.xyz
www.lianhuawangluo39.xyz:22
www.lianhuawangluo39.xyz:43080
www.luthj.sbs
www.piaojufw.top
www.pjuyt.sbs
www.swqe.sbs
www.telegramde.sbs
www.telegramvesl.org
www.telegrrram.com
www.vrheg.sbs
www.yfapiao.cyou
www.yfapiao.top
www.yrfgd.sbs
www.yunfpzx.com
xjtdf.sbs
xl.hflh2.cn
xun.hfyx1.cn
xunl.hfqc3.cn
xunlei11.hehuashangwu15.xyz
xunlei11.lianhuawangluo01.xyz
xunlei11.lianhuawangluo12.xyz
xunlei11.lianhuawangluo23.xyz
xunlei11.lianhuawangluo25.xyz
xw.wsopkf.top
xwbb.mmwu710.com
yrfgd.sbs
yunfpzx.com
yunvfapiao.com
yxc16.chenqingwen.top
yyds.hnxbkjyxgs.com
yyts08.hhzef.cn
zip1.hehuashangwu18.xyz
zip2.lianhuawangluo05.xyz
zip2.lianhuawangluo11.xyz
zuo.zhangsilei.top
zxcvbbnnca03.hhzef.cn
zxcvvbrar02.whroz.cn
```


# 参考文章:  
     
https://mp.weixin.qq.com/s/ae1iOSrUOrGBhERyjqZJIQ     
https://mp.weixin.qq.com/s/jy_iVqXB3QLgsaxApVXc5A         
https://mp.weixin.qq.com/s/WmLekqCN3sOy3_JQlMvyVg 



