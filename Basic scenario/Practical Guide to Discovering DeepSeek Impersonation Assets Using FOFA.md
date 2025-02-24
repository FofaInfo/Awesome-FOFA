# Practical Guide to Discovering DeepSeek Impersonation Assets Using FOFA

## Overview

In January 2025, DeepSeek's latest open-source large model, R1, sparked global discussion among developers upon its release, and its influence continues to grow. According to SimilarWeb data, deepseek.com received over 277 million visits in January. As DeepSeek's popularity continues to rise, cybersecurity issues have once again entered the public spotlight, becoming a hot topic of discussion.

According to a [report from a business partner](https://www.qianxin.com/news/detail?news_id=13026), "There are already over 2,000 websites impersonating DeepSeek and phishing sites," which is a staggering figure, indicating that impersonation websites targeting DeepSeek have become widespread. Is this really the case? To find out, this article will use the FOFA cyberspace asset mapping engine to conduct a comprehensive asset mapping of DeepSeek-related assets. Through analysis of application distribution and impersonation characteristics, we aim to discover real impersonation websites and potential cybersecurity threats.

## DeepSeek Unofficial Related Assets

To match all DeepSeek-related pages, we use `host="deepseek" || title="deepseek"` to limit the targets to those with "deepseek" in their domain or title, combined with `type="subdomain"` to filter all service (website) assets. The syntax is as follows:

```
(host="deepseek" || title="deepseek") && type="subdomain"
```

The results show that the total number of DeepSeek-related pages globally has reached 3,099, and this number is increasing rapidly by over 100 per day.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110344951.png)

### Application Distribution

From the FOFA matching results, apart from official asset information, related assets are mainly distributed across six categories: local deployment and client applications (906), content and information (771), domain registrars (232), forums (152), virtual currency-related (62), and impersonation and phishing websites (38).

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110352117.png)

Pie chart of application distribution

In local deployment applications, Ollama is becoming the preferred choice for AI local deployment, with its asset count reaching 54,000 across the internet. Since many users are not professional developers, they often overlook security issues and use default configurations. For example, Ollama's default Docker installation lacks verification and exposes ports to the internet, leading to many services being exposed online. Many Chat Nio assets can be accessed using the default admin account root/chatnio123456.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110358090.png)

Ollama search results

As shown in the image, an AI operation platform with nearly a thousand users can be accessed using the default admin account, allowing price settings and announcements to be arbitrarily modified, and service logs containing a large amount of user data to be leaked. Default configuration issues are becoming the primary threat in current DeepSeek local deployment and client applications.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110362830.png)

Chat log leakage in a management backend

## Impersonation Website Asset Discovery Practice

In terms of impersonation website discovery, FOFA, as a powerful internet asset search engine, provides rich tools and functions that can help users quickly discover and analyze impersonation websites. Next, we will explore how to use FOFA for effective searches to find DeepSeek-related impersonation websites.

### Finding Samples Based on Impersonated Websites

Impersonation websites generally extract the HTML source code of the official website and add malicious links or input boxes to induce users to phishing attacks. Therefore, extracting features from the impersonated website is an effective method for finding impersonation website samples.

The target for this investigation is DeepSeek (full name: Hangzhou DeepSeek Artificial Intelligence Basic Technology Research Co., Ltd.). Through the Ministry of Industry and Information Technology's ICP filing management system, we obtained DeepSeek's official domain: deepseek.com.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110369877.png)

beian.miit.gov.cn

Impersonation websites, as the name suggests, first need to imitate. Since they imitate, the internal information of the website is the most likely to be copied or directly used. Through the target website, we can extract multi-dimensional information.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110375723.png)

www.deepseek.com

#### LOGO

The LOGO is the most prominent iconic element of a website. If the LOGO of an impersonation website is not realistic enough, it is like drawing a dragon without dotting the eyes, making it difficult to pass as genuine. Therefore, analyzing the LOGO is an effective method for identifying impersonation websites.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110381864.png)

On the target site, using F12 to open the developer tools and selecting the LOGO image, we can get the img tag's alt attribute and the image source cdn.deepseek.com/logo.png. To facilitate replication, builders often directly use the target's image source or tag attributes. Therefore, we construct rules based on these two elements:

```
body="alt=\"DeepSeek Logo\"" || body="cdn.deepseek.com/logo.png"
```

There are 165 results, but many of them are websites referencing DeepSeek. Therefore, we further search using DeepSeek's icon.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110388741.png)

Construct the rule:

```
(body="alt=\"DeepSeek Logo\"" || body="cdn.deepseek.com/logo.png") && icon_hash="-118645384"
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110399749.png)

The matching results are reduced to 27. We select the target deepseek-v3.live for judgment.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110406647.png)

deepseek-v3.live

As can be seen, its internal layout completely mimics the DeepSeek official website, but "Start Now" and "Get DeepSeek App" have been replaced with "Connect Wallet."

Finally, through manual screening, the following impersonation websites are identified:

- deepseek-v3.live
- deepseek-umxvljvoilcnxih.app-tools.info
- deepsekk.sbs
- deepseekonchain.com
- deepseek-6phm9gg3zoacooy.app-tools.info

#### Filing Number and Copyright Statement

The website filing number is the network information license number issued by the Ministry of Industry and Information Technology's website filing management system. Through the website filing number, the company to which the website belongs can be found. Impersonators often directly use the filing number and copyright statement of the impersonated party to mimic the victim.

The filing number and copyright statement are usually located at the bottom of a website. Through the official website's Chinese and English pages, we can obtain the copyright statement "2025 Hangzhou DeepSeek Artificial Intelligence Basic Technology Research Co., Ltd. All rights reserved" and the filing number "Zhejiang ICP No. 2023025841."

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110413316.png)

Based on the known information, we can construct the rule:

```
body="2025 Hangzhou DeepSeek Artificial Intelligence Basic Technology Research Co., Ltd. All rights reserved" || body="2025 DeepSeek. All rights reserved." || body="Zhejiang ICP No. 2023025841"
```

There are 35 matching results.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110645973.png)

Here, chatbot-deepseek.com is taken as an example. This website mimics the DeepSeek homepage and replaces the login option with a file download. After decompression, malicious files are found.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110424422.png)

chatbot-deepseek.com

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110430162.png)

Compressed file contents

Another example is a malicious website that replaces page elements with downloadable executable files. It was found that during subsequent execution, it downloads malicious programs from GitHub:

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110435949.png)

home-deepseek.com

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110449224.png)

Malicious BAT program

Finally, the following impersonation websites are screened out (the following results do not include previously screened impersonation websites):

- chatbot-deepseek.com
- deepseekapk.pro
- deepseekonchain.com
- deepseek-r1.xyz
- deepseekapp.cloud
- intelligence-deepseek.com
- deepseekaz.top
- home-deepseek.com
- r1platform-deepseek.com
- www.deepseek.family

#### Product Information

A company's core product introductions are often heavily targeted for impersonation. Impersonators often copy the most core product introductions to mimic the target.

Clearly, DeepSeek's most core product is the DeepSeek model, and its introduction is on the official website's homepage.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110455729.png)

Extract rules from the product introduction:

```
body="DeepSeek-R1 is now live and open source, rivaling OpenAI's Model o1. Available on web, app, and API. Click for details." || body="DeepSeek-V3 achieves a significant breakthrough in inference speed over previous models."
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110495114.png)

Taking deepseekllm.pro as an example, the target issues virtual currency in the name of DeepSeek's official, which is clearly fraudulent in nature.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110501124.png)

deepseekllm.pro

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110506695.png)

Page contains misleading terms such as "official"

Finally, the following impersonation websites are screened out:

- deepseekllm.pro
- deepseeklogin.com

#### Other Pages

In addition to homepage information, other pages are also potential targets for impersonation. Login pages and download pages are often heavily targeted. Here, more subpages are obtained through the navigation bar at the bottom of the DeepSeek homepage.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110515902.png)

##### download.deepseek.com

This page is DeepSeek's download navigation site.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110522437.png)

Construct rules based on the information from the download navigation site:

```
body="Download DeepSeek App" || title="DeepSeek App" || body="Download DeepSeek App now to experience intelligent AI assistant."
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110527864.png)

Currently, no websites impersonating the official download page have been found, but there are some pages that use DeepSeek keywords to direct download links to their own file stations instead of the official one. This diversion method is clearly misleading, and the downloaded files may contain viruses.

Finally, the following impersonation websites are screened out:

- deepseekapp.download
- deepseekdownload.org
- deepseekapp.in
- deepseekapp.info

### Finding Samples Based on Public Internet Information

Samples of impersonation websites can be obtained through public internet information. Analyzing these samples can also lead to valuable results.

Some sample acquisition channels:

- [12321 Reporting Center for Harmful and Spam Information](https://www.12321.cn/)
- [PhishTank](https://phishtank.org/)
- [Illegal Impersonation Agency Information Publicity - China Securities Association](https://www.sac.net.cn/)
- Public articles on WeChat official accounts or other blogs

The following impersonation websites are obtained from public sources:

- deepseeklogins.com
- deepseeklogin.us
- deepseek.net.ua

Based on the above analysis, 24 initial samples are obtained.

## Analyzing Sample Characteristics & Expanding the Search

After obtaining initial samples, the search can be expanded based on known sample characteristics to discover more impersonation websites.

### Expanding Search Based on FOFA Rule Feature Extraction

The basic idea here is: after obtaining the sample IP, view the source code content of the sample, extract the common characteristics of such websites, and construct FOFA rules for matching.

Feature extraction generally revolves around two aspects: one is frequently appearing keywords, representing the tendency of impersonation, such as "loan" often appearing in bank impersonation websites. By adding keywords, more related websites can be obtained. The other is malicious code in web pages. Through malicious code, the habitual characteristics of the deployer can be quickly extracted. For example, if an impersonator often adds js statements directing to malicious websites in impersonation websites, then a batch of deployment sites of this actor can be quickly screened based on this js statement, achieving behavior profiling.

#### DeepSeek+Wallet

In the obtained samples, it is found that some impersonation websites combine DeepSeek keywords to guide users to log in to online wallets.

For example: www.deepseekonchain.com, these websites frequently mention "wallet," so "wallet," "coin," and "Total supply" are extracted as keywords.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110538873.png)

www.deepseekonchain.com

Construct the rule:

```
(title="deepseek" || host="deepseek") && (body="wallet" || body="coin" || body="Total supply")
```

The results are as follows:

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110544440.png)

Analyzing the results, it is found that there are a large number of virtual currencies issued in the name of DeepSeek's official or taking advantage of DeepSeek's popularity. Due to the nature of virtual currencies, issuers often exaggerate the prospects of the currency and use false endorsements to conduct actual fraud. In addition, there are some scam websites in the name of Airdrop, such as deep-whitelist[.]com prompting to upload recovery phrase to connect the wallet, which is a typical airdrop scam.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110550033.png)

deep-whitelist.com

Virtual currency-related expansion results:

- deepseekt.org
- www.agent-alessa.xyz
- deepinu.xyz
- deepseekllm.pro
- deepseeksol.club
- deepseekfwog.site
- deep-whitelist.com
- deepseekai.club
- deepseek-wl.com
- www.deepseekceo.vip
- 34.92.225.248
- deepseekceo.vip
- deepseek2025.com
- deepseek2025.xyz
- deepseeklab.xyz
- jlt-ai.pro
- deepseekkb.com



### Expanding the Search Using FOFA's Statistical Aggregation Panel

For a certain category of impersonation websites, we can extract rough rules and add restrictions such as region or host. Then, by utilizing FOFA's statistical aggregation panel, we can quickly judge which websites might be impersonation sites based on their titles and domains, and conduct further analysis. Since impersonation websites often deploy numerous similar sites simultaneously for traffic purposes, FOFA's unique FID fingerprint can be used to aggregate similar websites.

Here, we construct the following rule:

```
title="deepseek" && (region="HK" || region="TW" || country="US" || country="ZA" || country="SG" || country="JP") && domain!="deepseek.com"
```

Click on the website fingerprint ranking to enable the aggregation panel.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110557525.png)

Further filter through FOFA's aggregation panel, browse the title content to quickly determine if it might be an impersonation site, and then click to visit for further judgment.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110562970.png)

Using this method, we ultimately identified the following virtual currency-related websites:

- missdeepai.club
- agent-alessa.xyz
- deepseek-buy.click

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110569229.png)

deepseek-buy.click

## Expanding the Search Based on FOFA Icon Hash

For certain types of impersonation websites with icons, we can extract the corresponding Icon Hash from FOFA and perform clustering based on the icons.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110574540.png)

When searching for the current target, a large number of icons similar to DeepSeek's can be seen. At this point, we can select similar icons for batch searching. In the search results, we can find that the previously identified deepseek-v3[.]live is among them.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110580588.png)

In addition to using FOFA for asset mapping, we can also supplement the search with other external resources and tools, such as actively scanning C segments when multiple suspicious IPs are in the same C segment, using WHOIS to query registrant information to track associated domains, checking invalid certificates, and regularly filtering newly added similar domains.

Ultimately, through the search expansion, we identified 20 related virtual currency domains.

Aggregating the results of 43 sites, we found that impersonation websites and scam coin websites particularly favor using Cloudflare to conceal real IPs and evade tracking.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110587222.png)

Cloud aggregation statistics of 43 results

We performed the same Cloud aggregation on official assets:

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Storage/deepseek/1740110593849.png)

Cloud provider aggregation of official assets

The distribution of related IP results after deduplication is as follows:

- Huawei Cloud: 17 (including core business domains such as api.deepseek.com and chat.deepseek.com)
- Cloudflare: 4
- CloudFront: 2
- Alibaba Cloud: 1
- Tencent Cloud: 1

Among them, 14 IPs are marked as CDN nodes, covering Huawei Cloud (8), Cloudflare (4), and CloudFront (2). Core business systems (such as API services and chat services) are all deployed on Huawei Cloud, bearing the majority of the traffic load. From the aggregation statistics, we can conclude that the cloud provider where the service is hosted is also an important factor in judging suspicious websites.

## Summary

Based on asset mapping and search expansion analysis, we ultimately identified 24 active (as of the final draft) impersonation websites and potential threat websites, as well as 20 virtual currency-related sites. This result significantly differs from the competitor's claim that "there are over 2,000 impersonation and phishing websites targeting DeepSeek." The core reason for this discrepancy lies in the different definitions of impersonation websites. We hope to avoid misleading public risk perception through more rigorous expression.

Focusing on the security risks behind DeepSeek's phenomenal growth, we have identified three core issues:

1. **Security Baseline Risks**: The low deployment cost of the product attracts a massive user base, but it also exposes the widespread lack of security configuration awareness among users. This issue creates a breeding ground for security incidents such as computing power theft and data leaks.
2. **Amplified Impact of Vulnerabilities**: The rapid growth of the client-side scale significantly increases the threat level of potential vulnerabilities. Attackers can use automated tools to build attack chains, enabling batch attacks and laying the groundwork for large-scale security incidents.
3. **Impersonation Ecosystem Threats**: DeepSeek's popularity has also attracted the attention of threat actors. These impersonation websites not only mislead users but may also hide malicious activities, such as information theft, increasing the complexity of the network environment.

In the face of a complex security landscape, FOFA will continue to play an important role by monitoring and identifying impersonation websites and network threats, providing customers with real and effective security intelligence. At the same time, we call on the public to enhance security awareness, regularly check and update security configurations, and reduce the risk of being attacked.

## References

<https://www.qianxin.com/news/detail?news_id=13026>

## Appendix

**Impersonation Websites and Potential Threat Websites:**

- www.deepseek.family
- r1platform-deepseek.com
- deepseek-v3.live
- deepseek-umxvljvoilcnxih.app-tools.info
- deepsekk.sbs
- deepseekonchain.com
- deepseek-6phm9gg3zoacooy.app-tools.info
- chatbot-deepseek.com
- deepseekapk.pro
- deepseek-r1.xyz
- deepseekapp.cloud
- intelligence-deepseek.com
- deepseekllm.pro
- deepseeklogin.com
- deepseekapp.download
- deepseekdownload.org
- deepseekapp.in
- deepseekapp.info
- deepseeklogins.com
- deepseeklogin.us
- deepseek.net.ua
- deepseekaz.top
- home-deepseek.com

**Virtual Currency-Related Websites:**

- deepseekt.org
- www.agent-alessa.xyz
- deepinu.xyz
- deepseekllm.pro
- deepseeksol.club
- deepseekfwog.site
- deep-whitelist.com
- deepseekai.club
- deepseek-wl.com
- www.deepseekceo.vip
- 34.92.225.248
- deepseekceo.vip
- deepseek2025.com
- deepseek2025.xyz
- deepseeklab.xyz
- jlt-ai.pro
- missdeepai.club
- agent-alessa.xyz
- deepseek-buy.click
- deepseekkb.com
