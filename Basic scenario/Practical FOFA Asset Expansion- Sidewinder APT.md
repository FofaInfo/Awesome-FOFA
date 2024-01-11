# Practical FOFA Asset Expansion: Sidewinder APT

![0](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinderen.png)

## Overview

In our previous articles, we shared and demonstrated how to use FOFA for asset expansion through actual cases from a user's perspective, which received a lot of feedback and praise. Therefore, we decided to make the "Practical FOFA Asset Expansion" into a series, which will not only show more data intelligence but also share more FOFA operation skills.

This article analyzes the Sidewinder APT for asset expansion through 5 publicly released IOCs, forming the FOFA rules for this APT, **and finally obtained 193 results**.

At the same time, we used Qi Anxin Threat Intelligence Platform, Threatbook and VirusTotal, three leading tools in the industry, for cross-validation. During the verification process, we found that these three platforms have certain differences in the coverage of the Sidewinder APT. Specifically, **Qi Anxin covers 137 strips, Threatbook covers 118 strips, Virustotal covers 138 strips, and there are 24 strips that are not covered by all three platforms**.

Due to the different attributes of each platform, once the APT's characteristics are identified with the continuous information collection capabilities of the FOFA platform, we can dynamically obtain the latest asset information and cover it in a timely manner, thereby obtaining threat intelligence faster and enhancing customer value.

Based on the reference report using shodan, we also verified it with shodan. However, due to the mechanism, the same results have not been reported. But through our verification, using the same feature for expansion, **we still found that there are surviving assets outside the reference report**.

In addition, our research also found that the Sidewinder APT is more inclined to attack the government, energy, military and mineral sectors. This point is clearly reflected in our expansion results.
The article will detail the expansion ideas and methods for IOC, and a comparison table for expansion will be attached at the end of the article.

## Clue Analysis

### Clue One:

We extracted the following three domain names by extracting the IOC summarized by MikhailKasimov on GitHub:


```
neger.site
semain.tech
aliit.org
```

GitHub open source project address:

https://github.com/stamparm/maltrail/blob/3db4f2069cd7befcbfd2e0d1fe447773f1f755ca/trails/static/malware/apt_sidewinder.txt

Seeing that the original data source is the analysis report of Bridewell. Use FOFA to query these three domain names to see what their features are.

```domain="neger.site"```

![1](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder1.png)


```domain="semain.tech"```

![2](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder2.png)

```domain="aliit.org"```

![3](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder3.png)

From the display results of FOFA, they obviously have the same JARM fingerprint feature.
We extract this JARM fingerprint for query

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7"
```


![4](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder4.png)

There are 247,319 assets found, indicating that there is a large error in using this single feature, so you need to search for other common features of the three IOCs.
Careful comparison found that the headers of the first two domain names both have features of 404 Not Found, Server: nginx, Content-Type: text/html.

![5](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder5.png)

![6](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder6.png)

But the Server of aliit.org is nginx/1.23.2, which is different from the other two. We will study the other two IOCs first.
By extracting the same features of the first two domain names, add it to the FOFA search statement as follows:


```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html"
```

![7](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder7.png)

It is clear that the circled range has become smaller, but there are still some errors in the regional ranking, and valid features need to be extracted.

Through the header response header and website text confirmation, his Content-Length is all 183 or 535 characters.

This feature can be added to the FOFA statement for further query:

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535")
```

![8](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder8.png)

The results are normal now, we need to make a determination, add the initial two IOCs for verification, whether it is a relationship of inclusion.

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535") && (domain="neger.site" || domain="semain.tech")
```

![9](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder9.png)

Confirmed that there is no problem, so the final expansion line to the FOFA rule through the first two domain names is:

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535")
```



Next, let's look at another IOC: aliit.org found that its features are basically similar to the features of the first two domain names, only Content-Length is 555, and Server is nginx/1.23.2.

![10](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder10.png)

In the same way, extract the features, organize them into FOFA syntax for query:

```
header="HTTP/1.1 404 Not Found" && header="Server: nginx/1.23.2" && header="Content-Type: text/html" && header="Content-Length: 555" && jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7"
```

![11](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder11.png)


### Clue Two:

Clue two comes from twitter @Cyberteam008, with the IOC of the Sidewinder  publicly disclosed by security researchers, and the above IOC does not overlap, we can just make a wave of expansion supplement.

![12](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder12.png)

Take the main domain for analysis:

```
direct888.net
fia-gov.net
```

Use FOFA for search to confirm his asset features:

```domain="direct888.net"```

![13](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder13.png)

```domain="fia-gov.net"```

![14](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder14.png)

Obviously, very familiar. Sure enough, their JARM fingerprints are the same, and other features are consistent with the features of the above clues, only content-length is 228.
These features are integrated into the FOFA rules, which is:

```
jarm="1dd40d40d00040d1dc1dd40d1dd40de9ab649921aa9add8c37a8978aa3ea88" && header="Content-Length: 228" && header="HTTP/1.1 404 Not Found" && header="Server: nginx"
```

![15](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder15.png)

In this expansion, we have organized three asset rules for this  according to the IOC.


```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535")
```
```
header="HTTP/1.1 404 Not Found" && header="Server: nginx/1.23.2" && header="Content-Type: text/html" && header="Content-Length: 555" && jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7"
```

```
jarm="1dd40d40d00040d1dc1dd40d1dd40de9ab649921aa9add8c37a8978aa3ea88" && header="Content-Length: 228" && header="HTTP/1.1 404 Not Found" && header="Server: nginx"
```

## Asset Verification

The report published by Bridewell company, which we used as a reference, relied on shodan for information collection. Hence, we also used shodan to confirm the rules we had derived. However, due to some mechanism, it's not possible to obtain more information.

![16](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder16.png)

![17](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder17.png)

![18](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder18.png)

However, taking samples from the results obtained by using the same features for discovrey, the data is still alive, and Shodan couldn't retrieve it.

![19](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder19.png)

![20](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder20.png)

In addition, we also verified the assets of the Sidewinder APT  traced by FOFA on three platforms, and found that there were differences in markings on each platform. The following situations exist:
countpro.info has been marked as Sidewinder.

![21](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder21.png)

neger.site, Qi Anxin Threat Intelligence Platform did not mark it as Sidewinder.

![22](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder22.png)

mofagov.live, Threatbook did not mark it as Sidewinder.

![23](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder23.png)

The clue IOC obtained: direct888.net was marked as Sidewinder, but the direct888.org traced in FOFA was not marked.

![24](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder24.png)

![25](https://github.com/FofaInfo/Awesome-FOFA/blob/640f5d1c9cd43e4ce0be5311c9ae9ac17e2675b0/Storage/sidewinderimage/sidewinder25.png)

Therefore, given this situation, we have done a detailed verification of the threat intelligence platform with the rules after the line extension, and introduced new tools. The tools used for verification include Qi Anxin Threat Intelligence Platform, Threatbooks, and Virustotal, with coverage of 137, 118, and 138, respectively, and there are cases where all three platforms are not covered.

Due to the different attributes of each platform, once the FOFA  Cyberspace mapping platform confirms the characteristics of the , as long as the  is still online, it can add this rule to the monitoring system. This will allow us to dynamically obtain the latest asset information of the  and quickly cover the labels. This approach can obtain threat intelligence more quickly, thereby creating greater value for customers.

The results of the verification are summarized below, and detailed calibration information can be viewed in Appendix 1.

| FOFA Returned Results | Qi Anxin Uncovered | Threatbook Uncovered | Virustotal Uncovered | Not Covered by All |
|:--------------------------:|:------------------:|:---------------------:|:---------------------:|:------------------:|
|            193             |         56         |          75            |           55           |         24         |

## Conclusion

By analyzing part of the IOC for line extension, it can be found that the  tends to use keywords related to the official domain name of the  related to the target of the attack to construct the domain name. For example, they use "*fia-gov*", "*mof-gov*", "*mofa-gov*", "*govnp*", "*govpk*" and other keywords to disguise as military and government-related domain names. In addition, they also construct domain names by shifting characters, hiding, etc., such as "*direct888.org", "*directt88.org", "*downlod.net", "*downlod.com" and so on to attack.

The threat intelligence comparison results are Qi Anxin covers 137, Threatbook covers 118, Virustotal covers 138, and none of the three platforms cover 24.

By using a small part of the IOC, analyzing and extracting features step by step to carry out asset line extension, when the line extension is completed, it actually exists as a FOFA rule. Combined with the continuous information collection ability of the FOFA platform, once the  characteristics are identified and kept online, it can dynamically obtain the latest asset information and cover it in time, thereby obtaining threat intelligence more quickly and enhancing customer value.

Based on the method of using shodan for information collection in the Bridewell company's report, we also used shodan to verify the collated rules. Although there is no same result due to mechanism reasons. But through our verification, using the same features for query, there are still surviving assets outside the report.

This process mainly relies on the powerful search function of FOFA, but the key is the ability to find clues and reason logically. I hope this case can provide everyone with how to use FOFA more effectively for asset discovery and information collection, so as to make full use of the ability of FOFA.

## References: 

https://www.bridewell.com/insights/news/detail/the-distinctive-rattle-of-apt-sidewinder
https://github.com/stamparm/maltrail/blob/3db4f2069cd7befcbfd2e0d1fe447773f1f755ca/trails/static/malware/apt_sidewinder.txt
https://www.freebuf.com/news/373902.html
https://twitter.com/Cyberteam008/status/1743505453529940004

## Appendix1: 

| IOC | FOFA Last update time | Threatbook | Qianxin | Virustotal |
| --- | ---                   | ---        | ---     | ---        |
| academy.lesporc.live | 2023-07-08 | Sidewinder |  |  |
| aeryple.xyz | 2023-03-17 | Sidewinder | Sidewinder | Malicous |
| agency.lesporc.live | 2023-07-08  | Sidewinder |  |  |
| aliit.org | 2023-05-01  | Sidewinder | Sidewinder | Sidewinder |
| amuck.scoler.tech | 2023-05-02 | Sidewinder | Sidewinder_23_05_17 | Sidewinder |
| api.argus.trondheim.bama.zoopit.no | 2023-11-19  |  |  |  |
| avail.freay.tech | 2023-06-21  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| awrah.live | 2023-04-03  | Sidewinder | Sidewinder | Sidewinder |
| axis.heplor.biz | 2023-11-01 | Sidewinder | Sidewinder_23_05_17 | Malicous |
| basic.gruh.site | 2023-06-24 | Sidewinder | Sidewinder_23_05_17 | Malicous |
| bless.agarg.tech | 2023-03-31 | Sidewinder | Sidewinder | Malicous |
| bol-south.org | 2023-01-10  | Sidewinder | Sidewinder_23_05_17 | Sidewinder |
| brac.tech | 2023-06-12  | Sidewinder | Sidewinder | Sidewinder |
| brave.agarg.tech | 2023-03-27  | Sidewinder | Sidewinder | Malicous |
| cater.sphery.live | 2023-03-30  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| cdn.awrah.live | 2023-01-10 | Sidewinder | Sidewinder |  |
| cdn.cpec.site | 2023-04-22  | Sidewinder | Sidewinder | Malicous |
| cdn.dolper.top | 2023-06-20  | Sidewinder | Sidewinder | Malicous |
| cdn.dr-doom.xyz | 2023-04-18  | Sidewinder | Sidewinder |  |
| cdn.gearfill.biz | 2023-07-29  | Sidewinder | Sidewinder |  |
| cdn.geoloc.top | 2023-06-24 | Sidewinder |  |  |
| cdn.hread.live | 2023-04-27  | Sidewinder | Sidewinder |  |
| cdn.plors.tech | 2023-05-27  | Sidewinder | Sidewinder |  |
| cdn.preag.info | 2023-05-26  | Sidewinder |  |  |
| cdn.preat.info | 2023-05-26  | Malicous | Sidewinder |  |
| cdn.prol.info | 2023-04-16  | Sidewinder |  |  |
| cdn.verocal.info | 2023-05-26  |  | Sidewinder |  |
| ceiling.kalpo.xyz | 2023-07-05  | Sidewinder | Sidewinder | Malicous |
| cert.repta.live | 2023-02-20  | Sidewinder | Sidewinder | Malicous |
| cirt-gov-mm.fia-gov.net | 2024-01-02  | Sidewinder | Sidewinder | Malicous |
| civil.leyra.tech | 2023-04-23  | Sidewinder | Sidewinder |  |
| climb.kalpo.xyz | 2023-07-05  | Sidewinder | Sidewinder | Malicous |
| cluster.jotse.info | 2023-05-01  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| cpec.site | 2023-04-28  | Sidewinder | Sidewinder | Sidewinder |
| csla.blesis.live | 2023-04-24  | Sidewinder |  |  |
| cssc-net.co | 2023-01-16  | Sidewinder | Sidewinder | Sidewinder |
| cvix.cc | 2023-01-17  |  |  | Malicous |
| density.meplor.xyz | 2023-06-21  | Sidewinder | Sidewinder |  |
| deputy.meplor.xyz | 2023-06-07  | Sidewinder | Sidewinder |  |
| direct888.net | 2023-12-13  | Sidewinder | Sidewinder | Malicous |
| direct888.org | 2023-09-08  |  |  | Malicous |
| directt88.org | 2023-01-17  | Sidewinder | Sidewinder_23_05_17 | SideWinder |
| dolper.top | 2023-06-09  | Sidewinder | Sidewinder | SideWinder |
| donwloaded.net | 2024-01-04 |  |  | Malicous |
| dr-doom.xyz | 2023-04-22  | Sidewinder | Sidewinder | SideWinder |
| dsmes.xyz | 2023-03-21  | Sidewinder | Sidewinder | SideWinder |
| employ.fdrek.live | 2023-03-29  | Sidewinder | Sidewinder |  |
| energy.fdrek.live | 2023-03-30  | Sidewinder | Sidewinder |  |
| fia-gov.com | 2023-05-01  | Sidewinder | Sidewinder | SideWinder |
| fia-gov.net | 2024-01-01  | Sidewinder |  | Malicous |
| focus.mectel.tech | 2023-05-01  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| focus.semain.tech | 2023-10-19  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| found.neger.site | 2024-01-01  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| found.troks.site | 2023-04-27  |  | Sidewinder_23_05_17 | Malicous |
| gearfill.biz | 2023-07-29  | Sidewinder | Sidewinder | SideWinder |
| geoloc.top | 2023-06-24  | Sidewinder | Sidewinder_23_05_17 | SideWinder |
| groove.olipy.info | 2023-04-30  | Malicous | Sidewinder_23_05_17 | Malicous |
| guide.graty.tech | 2023-10-18  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| hread.live | 2023-04-14  | Sidewinder | Sidewinder | SideWinder |
| islamic-path.com | 2023-04-30  | Sidewinder | Sidewinder_23_05_17 | SideWinder |
| kito.countpro.info | 2023-09-05  | Sidewinder | Sidewinder | Malicous |
| lax036.relay.arandomserver.com | 2023-03-06  |  |  |  |
| leyra.tech | 2023-04-23 | Sidewinder | Sidewinder | SideWinder |
| lnkly.net | 2023-04-18  |  |  |  |
| mat.trelin.tech | 2023-06-23  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| mfagov.org | 2023-01-24 | Sidewinder | Sidewinder_23_05_17 | SideWinder |
| mod-gov.org | 2023-02-08  | Sidewinder | Sidewinder | Malicous |
| mofa-gov-bd.fia-gov.net | 2024-01-02  | Sidewinder | Sidewinder | Malicous |
| mofagov.live | 2023-07-05  |  | Sidewinder | Malicous |
| mu-api.anyremote.cn | 2023-03-01  |  |  |  |
| mxhichina.info | 2023-01-25  |  |  |  |
| myanmar-gov-mm.fia-gov.net | 2024-01-02  | Sidewinder |  | Malicous |
| myoffice.fia-gov.net | 2024-01-02  | Sidewinder |  | Malicous |
| nextgen.fia-gov.net | 2024-01-02  | Sidewinder |  |  |
| ns.seiffenn.nohost.me | 2023-03-06  |  |  |  |
| ntc-pk.org | 2023-01-25  | Sidewinder | Sidewinder | SideWinder |
| olerpic.info | 2023-02-08  | Malicous | Sidewinder_18_05_22 | SideWinder |
| opmcm-gov-np.fia-gov.net | 2024-01-02  | Sidewinder | Sidewinder |  |
| opt.freay.tech | 2023-06-20  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| pak-gov.info | 2023-01-16  | Sidewinder | Sidewinder | SideWinder |
| plors.tech | 2023-06-01  | Sidewinder | Sidewinder | SideWinder |
| pmdu-gov.org | 2023-02-02  | Sidewinder | Sidewinder | Malicous |
| police-circular-gov-bd.fia-gov.net | 2024-01-02  | Sidewinder | Sidewinder | Malicous |
| portal.breat.info | 2023-04-30  | Sidewinder | Sidewinder | Malicous |
| preag.info | 2023-06-04  | Sidewinder | Sidewinder | SideWinder |
| preat.info | 2024-01-01  | Malicous | Sidewinder | SideWinder |
| ptcl-gov.org | 2023-01-10 | Sidewinder | Sidewinder | Malicous |
| resolve.preat.info | 2023-11-12  | Malicous | Sidewinder |  |
| reveal.troks.site | 2023-04-09  |  | Sidewinder_23_05_17 | Malicous |
| roof.wsink.live | 2023-03-17  | Sidewinder | Sidewinder | Malicous |
| rugby.wsink.live | 2023-03-17  | Sidewinder | Sidewinder | Malicous |
| seiffenn.nohost.me | 2023-06-06  |  |  |  |
| storeapp.site | 2023-01-17  |  | Sidewinder | SideWinder |
| test.api.68wx.com | 2023-03-01  |  |  |  |
| test.api.g.luohu8.com | 2023-06-24  |  |  |  |
| test.api.hzy.68wx.com | 2023-03-01  |  |  |  |
| test.es.68wx.com | 2023-03-01  |  |  |  |
| tinurl.click | 2023-01-11  |  | Sidewinder | SideWinder |
| torsey.xyz | 2023-07-05  | Sidewinder |  |  |
| toss.tercom.site | 2023-06-21  | Sidewinder |  |  |
| true-islam.org | 2023-04-21  |  |  | Malicous |
| trust-crypto.net | 2023-05-21  |  |  |  |
| utilize.elopter.top | 2023-06-20  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| verocal.info | 2023-06-26  |  | Sidewinder | SideWinder |
| wide.storeapp.site | 2023-01-17  |  | Sidewinder | Malicous |
| wind.ridlay.live | 2023-04-30  |  | Sidewinder | SideWinder |
| www.aliit.org | 2023-05-01  | Sidewinder |  | SideWinder |
| www.awrah.live | 2023-03-29  | Sidewinder | Sidewinder | SideWinder |
| www.bol-south.org | 2023-01-10  | Sidewinder |  | SideWinder |
| www.brac.tech | 2023-06-12  | Sidewinder | Sidewinder | SideWinder |
| www.cpec.site | 2023-04-28  | Sidewinder | Sidewinder | SideWinder |
| www.cssc-net.co | 2023-01-16  | Sidewinder | Sidewinder | SideWinder |
| www.cvix.cc | 2023-01-17  |  |  | Malicous |
| www.direct888.net | 2023-12-13  | Sidewinder | Sidewinder | Malicous |
| www.direct888.org | 2023-09-08  |  |  | Malicous |
| www.directt88.org | 2023-01-17  | Sidewinder |  | SideWinder |
| www.dolper.top | 2023-07-23  | Sidewinder | Sidewinder | SideWinder |
| www.donwloaded.net | 2024-01-04  |  |  | Malicous |
| www.dsmes.xyz | 2023-03-21  | Sidewinder | Sidewinder | SideWinder |
| www.fia-gov.com | 2023-05-01  | Sidewinder | Sidewinder | SideWinder |
| www.fia-gov.net | 2024-01-01  | Sidewinder |  |  |
| www.gearfill.biz | 2023-07-29  | Sidewinder | Sidewinder | SideWinder |
| www.geoloc.top | 2023-06-24  | Sidewinder |  | SideWinder |
| www.hread.live | 2023-05-25  | Sidewinder | Sidewinder | SideWinder |
| www.islamic-path.com | 2023-04-30  | Sidewinder |  | SideWinder |
| www.lnkly.net | 2023-04-18  |  |  |  |
| www.mfagov.org | 2023-01-24  | Sidewinder |  | SideWinder |
| www.mod-gov.org | 2023-02-08  | Sidewinder | Sidewinder | Malicous |
| www.mofa-gov-bd.fia-gov.net | 2024-01-02  | Sidewinder |  | Malicous |
| www.mofagov.live | 2023-07-05  |  | Sidewinder | Malicous |
| www.mxhichina.info | 2023-01-25  | Sidewinder |  |  |
| www.pak-gov.info | 2023-01-16  | Sidewinder | Sidewinder | SideWinder |
| www.plors.tech | 2023-04-25  | Sidewinder | Sidewinder | SideWinder |
| www.pmdu-gov.org | 2023-02-02  | Sidewinder | Sidewinder | Malicous |
| www.preag.info | 2023-03-27  | Sidewinder |  | Malicous |
| www.preat.info | 2024-01-01  | Sidewinder | Sidewinder | SideWinder |
| www.ptcl-gov.org | 2023-01-10  | Sidewinder | Sidewinder | Malicous |
| www.seiffenn.nohost.me | 2023-03-03  |  |  |  |
| www.shortney.org | 2023-03-30  | Sidewinder | Sidewinder | Malicous |
| www.tiinly.co | 2023-03-28  | Sidewinder | Sidewinder | SideWinder |
| www.tinurl.click | 2023-01-11  |  | Sidewinder | SideWinder |
| www.torsey.xyz | 2023-06-20  | Sidewinder |  |  |
| www.true-islam.org | 2023-04-21  |  |  | Malicous |
| www.verocal.info | 2023-06-26  |  | Sidewinder |  |
| xmpp-upload.seiffenn.nohost.me | 2023-03-03  |  |  |  |
| zone.vtray.tech | 2023-06-14  | Sidewinder | Sidewinder_23_05_17 | Malicous |
| 104.128.189.242 | 2023-04-21  |  |  | SideWinder |
| 106.14.215.130 | 2023-01-19  |  | APT、Snake |  |
| 149.154.154.65 | 2023-01-17  |  | Sidewinder | SideWinder |
| 151.236.21.16 | 2023-09-20  |  | APT、Snake | SideWinder |
| 158.255.212.140 | 2024-01-01  |  | APT、Snake | SideWinder |
| 161.129.64.98 | 2023-02-16  |  | APT、Snake | SideWinder |
| 172.93.162.117 | 2023-04-14  | spam、Malicous | APT、Snake | SideWinder |
| 172.93.189.46 | 2023-05-27  | spam、Malicous | APT、Snake | SideWinder |
| 172.96.189.157 | 2024-01-01  |  | APT、Snake | SideWinder |
| 179.43.141.203 | 2023-01-24  | Botnet、Malicous | APT、Snake |  SideWinder |
| 185.228.83.78 | 2023-03-03  |  | APT、Snake |  SideWinder |
| 192.71.166.145 | 2023-03-24  |  | APT、Snake | Malicous |
| 193.142.58.105 | 2023-10-02  |  |  |  |
| 193.42.36.50 | 2023-05-31  |  | APT、Snake | SideWinder  |
| 194.61.121.216 | 2023-01-25  |  | APT、Snake | SideWinder  |
| 194.68.225.13 | 2023-03-16  | Botnet、Malicous | APT、Snake | SideWinder  |
| 195.133.192.40 | 2023-04-16 |  | APT、Snake | SideWinder  |
| 196.189.44.42 | 2024-01-01  |  |  |  |
| 198.252.108.132 | 2023-02-01  |  |  |  |
| 198.252.108.33 | 2023-03-16  |  | APT、Snake | SideWinder  |
| 2.58.14.149 | 2023-07-07  |  | HIJACKED、SCANNER |  |
| 2.58.14.249 | 2023-04-21  |  | APT、Snake | SideWinder  |
| 2.58.15.71 | 2024-01-04  |  |  |  |
| 212.83.46.37 | 2023-12-09  | spam、Malicous | SPAM |  |
| 45.14.107.153 | 2023-02-01  |  | Sidewinder | SideWinder  |
| 45.147.230.157 | 2023-03-23  | spam、Malicous | APT、Snake | SideWinder  |
| 46.30.188.174 | 2023-03-22  |  | APT、Snake | SideWinder  |
| 46.30.189.53 | 2023-06-02  |  | APT、Snake | SideWinder  |
| 5.149.249.186 | 2023-03-23  |  | APT、Snake | Malicous |
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
| 91.245.253.73 | 2023-04-24  | spam、Malicous | APT、Snake、SPAM | Malicous |