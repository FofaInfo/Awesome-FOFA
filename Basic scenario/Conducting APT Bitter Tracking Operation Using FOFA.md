# Conducting APT Bitter Tracking Operation Using FOFA

## Introduction

When using FOFA to expand assets, we may encounter a situation where we may not know how to proceed when the target asset does not have obvious valid key features. If we only extract key field features, that may not be enough to achieve our final goal. Therefore, this sharing will take an actual combat as an example, starting from the initial information , combined with rich experience and methods, we identified more surviving assets of this APT Team through multi-angle recognition.

## Clue Analysis Thinking

Firstly, we saw a domain name Indicator of Compromise (IOC for short) found by [suyog41](https://twitter.com/suyog41/status/1717061493068640648), 
 through social media twitter, and nothing else.

![bitter0.1](https://github.com/FofaInfo/Awesome-FOFA/blob/6496bcf4f1d0d9aedd55e5f7488bab3a44faa774/Storage/bitter0.1.png)

```
webandersondesign.com
```

Based on the known content, we are going to discover more surviving asset information of the same group on the Internet today.

Starting with [FOFA](https://en.fofa.info), it was found that it has non-obvious features, mostly in the 403 state, with port 443 open and a certificate existed.

![bitter0.2](https://github.com/FofaInfo/Awesome-FOFA/blob/6496bcf4f1d0d9aedd55e5f7488bab3a44faa774/Storage/bitter0.2.png)

No valid features can be directly extracted, the number of searches with the original features is too large.

However, the first step we need to do is to reconfirm whether the related team corresponding to this asset is accurate. Use [Threatbook](https://threatbook.io/) to query its domain name and find more valid information. The following results can be seen. Through the query, it is confirmed that these two are the IOC of the APT Bitter.

![bitter0.3](https://github.com/FofaInfo/Awesome-FOFA/blob/6496bcf4f1d0d9aedd55e5f7488bab3a44faa774/Storage/bitter0.3.png)

In addition, through Google search, it was found that an open-source project named [maltrail](https://github.com/stamparm/maltrail) credit by [Mikhail kasimov](https://twitter.com/500mk500) [Maltrail](https://twitter.com/maltrail) has included this IOC information.

https://github.com/stamparm/maltrail/blob/master/trails/static/malware/apt_bitter.txt

![bitter0.4](https://github.com/FofaInfo/Awesome-FOFA/blob/6496bcf4f1d0d9aedd55e5f7488bab3a44faa774/Storage/bitter0.4.png)

After having more than two IOC information, the next step is to extract the common features of multiple IOC. The overall idea of this step is to compare the common parts of the two IOC, such as title, body, cert, etc.

At the same time, public information shows that the main attack method of this Team is to send OFFICE document classes through phishing, so the domain names extracted from IOC are actually the carriers of Trojans. It is found through analysis that the download link features of the Trojan are all:

**Domain/Random second-level directory/Random name PHP file? Parameter=username*computername**

![bitter6](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter6.png)

Through the analysis of the public samples, we found that their features on FOFA are as follows:

```
1. The title feature of the homepage is title="403 Forbidden"
2. Server header information: server="LiteSpeed"
3. Response header size: header="Content-Length: 1229"
4. Alternative service header: header='"Alt-Svc: h3=:443"'
```

But in fact, the range of these general features is too large, belonging to the general features of LiteSpeed.

![bitter7](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter7.png)

Don't be discouraged, the point is here, let's see what useful characteristics we can extract. Through comparative analysis, more clues are found:

```
1. Their service behavior features will definitely open 80,443 (SSL)
2. The domain name will definitely set CNAME
3. The certificate belongs to the free certificate, and the certificate must be a valid certificate without expiration
4. And the certificate subject will definitely bind a domain name
5. The website does not have a Favicon icon
```

![bitter8](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter8.png)

![bitter9](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter9.png)

So we first integrate these features at hand and start with the known feature splicing.

```
header='Alt-Svc: h3=":443"' && title="403 Forbidden" && header="Content-Length: 1229" && port="443" && server="LiteSpeed" && cert.issuer.cn="R3"
```

The features are arranged, and then add his characteristics, here are the single column of the FOFA statement corresponding to each feature.

```
1. domain!="" (domain name is not empty)
2. cert.is_valid=true (certificate is trustworthy)
3. cert.is_expired=false (certificate has not expired)
4. icon_hash="" (Favicon chart is empty)
5. cert.subject.cn*="*.*" (the certificate holder will definitely bind the domain name)
6. cname!="" (CName is not empty)
```

Here is a preliminary syntax splicing, so take the data of the past three months, so add a `after="2023-08-01"`
The preliminary syntax splicing results are as follows:

```
header='Alt-Svc: h3=":443"' && title="403 Forbidden" && header="Content-Length: 1229" && port="443" && server="LiteSpeed" && cert.issuer.cn="R3" && domain!="" && cert.is_valid=true && cert.is_expired=false && icon_hash="" && cert.subject.cn*="*.*" && cname!="" && after="2023-08-01"
```

![bitter10](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter10.png)

To be on the safe side, we need to verify the correctness of the extracted features above to confirm whether it is a containment relationship with the sample.

So add `host="webandersondesign.com"`

```
header='Alt-Svc: h3=":443"' && title="403 Forbidden" && header="Content-Length: 1229" && port="443" && server="LiteSpeed" && cert.issuer.cn="R3" && domain!="" && cert.is_valid=true && cert.is_expired=false && icon_hash="" && cert.subject.cn*="*.*" && cname!="" && after="2023-08-01" && host="webandersondesign.com"
```

![bitter11](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter11.png)

The current result range is still relatively rough, the next step can continue to analyze its public samples.

Through analysis, it is found that the machines that this Team puts Trojans on are shared web spaces, which belong to the web service that can be opened as soon as the account is registered, rather than separate server permissions (expressed as binding multiple domain names). We perform statistical analysis on the (Org) information of the existing samples. Here we can see that this Team will purchase web services for placement at several fixed service providers.

Public sample address:
https://raw.githubusercontent.com/stamparm/maltrail/master/trails/static/malware/apt_bitter.txt

Integrate the related Team information publicly disclosed into our grammar, as follows, there are 839 assets and 208 unique IPs:

```
header='Alt-Svc: h3=":443"' && title="403 Forbidden" && header="Content-Length: 1229" && port="443" && server="LiteSpeed" && cert.issuer.cn="R3" && domain!="" && cert.is_valid=true && cert.is_expired=false && icon_hash="" && cert.subject.cn*="*.*" && cname!="" && (org="ARTERIA Networks Corporation" || org="Advania Island ehf" || org="HOSTWINDS" || org="Host Sailor Ltd" || org="Akamai Connected Cloud" || org="NAMECHEAP-NET" || org="Iws Networks LLC" || org="Verdina Ltd." || org="AMAZON-02" || org="Melbikomas UAB" || org="GROUP-IID-01" || org="GLOBALCOMPASS" || org="Contabo GmbH" || org="INCAPSULA" || org="Neerja Softwares Pvt Ltd" || org="Commission on Science and Technology for" || org="Belcloud LTD" || org="DIGITALOCEAN-ASN" || org="QUICKPACKET")
```

![bitter12](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter12.png)

Randomly query from the results here to the threat intelligence platform, just take the first one to try, you can see that some domain names have been marked as the tags of the Bitter Team (APT Bitter) or malicious tags.

![bitter13](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter13.png)

Then the last step, we perform collision matching through the URL PATH collected from the public samples.

Sample address:
https://raw.githubusercontent.com/stamparm/maltrail/master/trails/static/malware/apt_bitter.txt

The tool used here is fofax, the tool download address:

https://github.com/xiecat/fofax

The specific collision syntax is as follows:

```
fofax -q ' header="Alt-Svc" && title="403 Forbidden" && header="Content-Length: 1229" && port="443" && cert.is_valid=true && cert.is_expired=false && icon_hash="" && cert.subject.cn*="*.*" && server="LiteSpeed" && cert.issuer.cn="R3" && cname!="" && domain!="" && (org="ARTERIA Networks Corporation" || org="Advania Island ehf" || org="HOSTWINDS" || org="Host Sailor Ltd" || org="Akamai Connected Cloud" || org="NAMECHEAP-NET" || org="Iws Networks LLC" || org="Verdina Ltd." || org="AMAZON-02" || org="Melbikomas UAB" || org="GROUP-IID-01" || org="GLOBALCOMPASS" || org="Contabo GmbH" || org="INCAPSULA" || org="Neerja Softwares Pvt Ltd" || org="Commission on Science and Technology for" || org="Belcloud LTD" || org="DIGITALOCEAN-ASN" || org="QUICKPACKET")' -fs 1000 | httpx -mc 404 -path "/c4ca4238a0b923820dcc509a6f75849b" |seds/c4ca4238a0b923820dcc509a6f75849b//g | httpx -path apt_path.txt -sc -mc 200,403
```

![bitter14](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter14.png)

![bitter15](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter15.png)

![bitter16](https://github.com/FofaInfo/Awesome-FOFA/blob/1f903e5d026f6e0b59f8751aaf749edec8a0c2dc/Storage/bitter16.png)

## Conclusion

This case is typical for two reasons: first, the initial information provided directly is very small; second, the asset does not have obvious feature performance.

I hope that the methods used in this actual combat case can provide some inspiration for you, especially when faced with assets lacking obvious valid features, how we can cleverly use OSINT tools and FOFA to carry out in-depth information collection and analysis.

This case shows how to flexibly use FOFA's filtering syntax, analyze various characteristics, and find more possibilities. Various methods have been tried, such as using equal or not equal to empty operations, certificate filtering syntax, and fuzzy matching, etc., these are very useful strategies and methods, and they will achieve unexpected results.

In general, this actual combat case not only provides specific operation steps for how to deal with similar problems, but also provides valuable insights for us to face other complex problems. I hope you can get inspiration from this case, learn how to flexibly use these methods, and solve practical problems more effectively.

Our Official URL: https://en.fofa.info

Contact us: service@baimaohui.net

Telegram: https://t.me/+-5xC1wYcwollY

