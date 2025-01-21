# A Comprehensive Guide to Threat Intelligence Discovery Using FOFA



# Abstract

Previously, we published detailed articles on asset discovery, covering threat groups such as COLDRIVER, Ducktail criminal organizations, Sidewinder group, and other infamous representatives. 

We also delved into the analysis of ObserverStealer infoStealer malware and the threat hunting against APT Bitter, revealing the secrets behind network threats for our audience. Furthermore, we demonstrated how FOFA can be used to unearth valuable threat intelligence within complex network environments, continually expanding the search scope to uncover more significant threats. 

The feedback and positive reviews we received were overwhelming, therefore, we decided to publish the FOFA Asset Discovery Manual, which was only for internal personnel. 

This article will systematically elaborate on the entire process of asset Discovery through FOFA, starting from the input source scenarios, sample collection channels, and IOC Attribution Confirmation methods, to the core techniques of building FOFA syntax based on corresponding attributes, false positive handling and result verification, etc. With all of the above, we will display to you, a comprehensive guide of FOFA threat intelligence discovery.

# Workflow Chart

![73702317338](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/en/1737449841098.png)

# Input Source

## Scenario

*    **Knowing the organization, Seeking the latest live IOC assets**（Our focus is finding certain APT group's IOC assets to perform asset discovery. For example, by targeting the OceanLous (also known as APT32, SeaLotus) group, we retrieve the most up-to-date active IOC assets associated with the group.）
*    **Knowing the IOC, Seeking the latest live asset of the corresponding organization with Network Mapping**（If it is an IP of an unknown organization, skip directly to step two to confirm the organizational affiliation. If the organization can be identified, then directly confirm the relevant information of the organization; if it is an unknown organization or the organization has not been named, conduct the extension directly.）


![image.png](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737450073141.png)

## Sample Collection Channels

Below are several channels that regularly update the latest samples from the APT organizations. We can specifically perform asset discovery to find sources for a particular APT organization we want to target.

### Intelligence Websites

- 360 APT Panorama Radar https://apt.360.net/aptlist
-  Redqueen Threat Intelligence Center\_Intelligence Query\_Intelligence Subscription https://redqueen.tj-un.com/home/advanced 
- Qianxin Threat Intelligence Center https://ti.qianxin.com/apt/?type=map



- Starmap Dbappsecurity https://ti.dbappsecurity.com.cn/apt/list



### GitHub Open-Source Project

```
https://github.com/stamparm/maltrail
https://github.com/blackorbird/APT_REPORT
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/835e0e98-9946-4d65-a3b6-149c318d80fc-1737021993561.png)

### Analysis Article on APT Organizations from the WeChat Official Accounts

Below we will share several WeChat official accounts that regularly update information and intelligence regarding the APT organizations.

- 360 Threat Intelligence Center (360威胁情报中心)
- Tencent Security Threat Intelligence Center (腾讯安全威胁情报中心)
- Qianxin Threat Intelligence Center (奇安信威胁情报中心)
- NSFOCUS Threat Intelligence (绿盟科技威胁情报)
- DBAPPSecurity Threat Intelligence Center (安恒威胁情报中心)
- Antiy AVL Threat Intelligence Center (安天AVL威胁情报中心)
- Threat Hunter (威胁猎人Threat Hunter)
- National Cyber Threat Intelligence Sharing Open Platform (国家网络威胁情报共享开放平台)
- Threat Page (ThreatPage全球威胁情报)

### Open Intelligence on Twitter

There are many excellent security researchers or teams on Twitter who will release some valuable intelligence, and this intelligence often has a strong timeliness. Due to limited space, only the authors of the tweets quoted in the historical extension tweets are listed here:

- Yogesh Londhe：<https://x.com/suyog41>
- Cyber Team：<https://x.com/Cyberteam008>
- Group-IB Threat Intelligence：<https://x.com/GroupIB_TI>
- Chris Duggan：<https://x.com/TLP_R3D>

###  Sample Repository

```plaintext
https://bazaar.abuse.ch/browse/
https://app.any.run/
https://ata.360.net/
https://www.virustotal.com/gui/
https://s.threatbook.com/
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/a73ea2d1-8a1f-48be-ae1a-6559ef7dbea3.png)



# Confirm IOC Attribution to an Organization and its Related Information.

If the IOC belongs to a known organization, the related information of the organization must be retrieved directly; if the IOC belongs to an unknown organization, its attribution must first be confirmed, and then the related information of the organization must be retrieved.

## Confirm the Attributed Organization

Generally, the prioritized method is to search for the IP/domain on various threat intelligence platforms to see if it is tagged with a threat organization label. Additionally, an alternative method is to perform a direct Google search, which requires manual verification.

E.g.: The initial IOC is an IP 45.41.204\[.\]18, which is tagged as "OceanLotus Group" in both ThreatBook and Qianxin Threat Intelligence Center, which allows us to infer that this organization is the OceanLotus APT group.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451769006.png)

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451774403.png)

E.g.: The domain sheicen\[.\]info is tagged as "Patchwork Group" in both ThreatBook and Qianxin Threat Intelligence Center, which allows us to infer that this organization is Patchwork.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451781064.png)

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451786001.png)

Additionally, there are the following situations: The domain neger\[.\]site is only tagged as "Sidewinder Group" in ThreatBook, while the domain mofagov\[.\]live is only tagged as "Sidewinder" in the Qianxin Threat Intelligence Center. This is due to the different characteristics of each platform, and we need to cross-verify across multiple threat intelligence platforms to confirm the organization.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451803261.png)



![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451797269.png)

### Common Threat Intelligence Communities

**ThreatBook**

url: [https://x.threatbook.com/](https://x.threatbook.com/)

**Qianxin Threat Intelligence Center**

url: [https://ti.qianxin.com/](https://ti.qianxin.com/)

**Virustotal**

url: [https://www.virustotal.com/](https://www.virustotal.com/)

**Starmap Dbappsecurity Platform**

url: [https://ti.dbappsecurity.com.cn/](https://ti.dbappsecurity.com.cn/)

**RedQueen**

url: [https://redqueen.tj-un.com/home/index](https://redqueen.tj-un.com/home/index)

**360 Security Brain。**

url: [https://ti.360.net/](https://ti.360.net/)

**Antiy Threat Intelligence Center**

url: [https://www.antiycloud.com/](https://www.antiycloud.com/)

**VenusEye**

url: [https://www.venuseye.com.cn/ip/](https://www.venuseye.com.cn/ip/)

**Tencent Security Threat Intelligence Center**

url: [https://tix.qq.com/](https://tix.qq.com/)

**NSFOCUS Threat Intelligence Center.**

url: [https://ti.nsfocus.com/](https://ti.nsfocus.com/)

### Google

Directly Search the domain sheicen\[.\]info, Through manual assistance to determine if this domain is a C2 domain controlled by the Patchwork Group.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451089777.png)

## Obtain Related Information of the Organization

After confirming the corresponding APT that the IOC attributes to, acquiring its related information is critical to us. After all, "Know yourself and know your enemy, and you will never be in danger in a hundred battles". This not only helps deepen the understanding of the organization but also aids in further intelligence gathering and analysis, specifically asset discovery.

Usually, we need to gather the background information below:

*   **Name**: Confirm and record the official name or aliases of the APT organization.
    
*   **Source**: Understand information such as the origin and funding sources of the organization.
    
*   **Main Affected Countries/Regions**: Identify the most affected geographic regions.
    
*   **Affected Industries**: Identify the primary sectors targeted by the attacks, such as government, finance, energy, etc.
    
*   **Organization Activity Period**: Confirm and clarify the historical activity period of the organization and its current status.
    
*   **Attacking Methods**: Study the specific techniques and tactics used by the APT, such as phishing emails, zero-day exploits, malware, etc.


The intelligence websites shared above basically cover all the background information of APT organizations. We can directly search for the organization's name to quickly understand the background information of the organization. The determination of the sample activity period requires analysis and manual judgment.

E.g.: Search for the organization "OceanLotus" in the Qianxin Threat Intelligence Center, and in the group overview module, there is basic background information about the organization, such as attacker type, suspected origin, affected industries, attack methods, and more.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451104854.png)

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451111458.png)

**Determination of Sample Activity Period:**

Open the article closest to the time when we conduct asset discovery analysis in the Qianxin Threat Intelligence Center: ["New Trend in MSI File Abuse: New OceanLotus Group Uses MST Files to Deliver Emotet for the First Time"](https://ti.qianxin.com/blog/articles/new%20-trend-in-msi-file-abuse-new-oceanlotus-group-first-to-use-mst-files-to-deliver-special-trojan-cn/). The article mentions that 'Resume.pdf' is the first use of a new technique of MSI file abuse, with the creation date of the file being October 9th. This confirms that the organization's batch of samples has been active since October.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/8ffd8f9a-1797-44c0-891d-d132d5e7f687.png)

# Asset Discovery (IOC Common Feature Extraction)

Summarize the IOCs we have obtained. If there are multiple, extract their common features; if there is only one, directly extract its features to form FOFA rules.

## Fields Eligible For Asset Discovery

**Sometimes a single field cannot fully represent all the characteristics of a website; usually, we need to combine fields to form FOFA syntax**. Below are some fields that can be used for asset discovery:

```
fid (A unique identifier formed by FOFA by aggregating multiple key features. Through FID, users can quickly find website assets with similar characteristics)
jarm (JARM is an active Transport Layer Security (TLS) server fingerprinting tool)
header_hash（FOFA's header_hash feature refers to the hash value of the HTTP response header information）
body_hash (The hash value calculated from the HTML body)
banner（Protocol's banner information）
cert（Certification）
header（HTTP header information）
server（Website server）
port（Open port）
icon_hash (The hash value of the icon that identifies the device)
certs_issuer_org (Organization of the certificate issuer)
certs_issuer_cn (Certificate issuer common name)
certs_subject_org（Organization of the certificate holder）
certs_subject_cn (Certificate holder common name)
lastupdatetime (FOFA last updated time)
tls_ja3s (JA3S is used for SSL/TLS communication on the server side, and its fingerprint is generated using the SSL version, cipher, and SSLExtension attributes from the ServerHello packet)
tls_version（TLS protocol version）
```

## Attribute Selection

Attribute Selection is usually based on the following factors below: 

*   **Uniqueness**: Select attributes can uniquely identify APT organization activities to reduce false positives and improve accuracy.
    
*   **Stability**: Select attributes that are not prone to frequent changes, such as certain specific tech stacks or service configurations.
    
*   **Detectability**: Select attributes that can be easily detected through network scanning or intelligence gathering.
    
*   **Relevance**: Select attributes that are closely related to the behavior patterns of the APT organization.
    

We already know which attributes can be used for asset discovery. For us to quickly identify APT organization-related assets, below are some features that can be prioritized, along with their reasons:

1.  **Certificate Features**: First, prioritize certificate features, as certificate information is **relatively stable and not prone to frequent changes**, and can be treated as an important characteristic when identifying assets of the APT. Certificates provide detailed information about the server's identity, including the issuer, validity period, public key, etc. Certain attributes of the certificate, such as the organization name in the issuer (Issuer) and subject (Subject), can serve as important clues for identifying APT organization assets.
    
2.  **TLS Fingerprint**: Secondly, select TLS fingerprints, such as JARM or JA3S fingerprints. These fingerprints are **based on detailed information from the TLS handshake process and can uniquely identify a server's TLS configuration**. TLS fingerprints can help identify APT assets that maintain the same TLS configuration, even if their IP address or domain name changes.

3.  **HTTP Response Header Characteristics**: HTTP response header characteristics, such as the Server header, Content-Type, Content-Length, etc. These characteristics can provide information about the server software, page content, and response size. By analyzing these characteristics, it is possible to identify **specific server configurations or behavior patterns related to APT organizations, and this information is relatively stable and can be easily detected through network scanning**.
    
4.  **HTTP Response Banner Features**: Banner features, such as service version information in the HTTP response, can also serve as clues for identifying APT assets. This information may remain consistent across the APT organization's attack infrastructure, helping to identify related assets.
    

### Certificate-based Fingerprint Association

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451155287.png)

The server certificate includes the following information:

* Issuer
  
* Valid from, Valid to (Certificate Validity Period)
  
* Public key
  
* Subject (Certificate Holder)
  
* Signature algorithm
  
* Thumbprint, Thumbprint algorithm (Fingerprint and Fingerprint Algorithm)
  

E.g.：

A security search from the Cyber Team sends out a [post](https://mobile.x.com/Cyberteam008/status/1789156897318207976), sharing a certificate commonly used by the Lazarus group, which has been reused across multiple domains. The certificate can be uniquely identified by its Serial Number.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/84540997-9f87-4a0d-96d4-3d18baf652b7.png)

Through this characteristic, six independent IPs and multiple associated domains are found to belong to this organization after conducting asset discovery.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451183431.png)

### TLS Fingerprint Association

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/285195f3-05a5-400d-ade4-d24b090ba5a5.png)

* The JA3 method is used to collect the decimal byte values of the following fields in the Client Hello packet: version, acceptable ciphers, extension list, elliptic curve cipher, and elliptic curve cipher format, which helps in identifying the client. The JA3/S method is used for the server side of SSL/TLS communication, and the fingerprint is generated using the attributes in the ServerHello packet, such as SSL version, cipher, and SSLExtension. **JA3 and JA3/S are mainly based on traffic, where the server generates different JA3S fingerprints for different clients.**
  
*   **JARM, on the other hand, is a fully active scan that generates fingerprints, and the server can produce a unique JARM fingerprint.** It is an active Transport Layer Security (TLS) server fingerprinting tool that can quickly verify whether all servers in the group have the same TLS configuration.
    

E.g.:

A special JARM fingerprint can be used to distinguish whether a server has deployed a C2 service. For example, on GitHub, the project "nuclei-templates" developed and maintained by the ProjectDiscovery team includes [scripts](https://github.com/projectdiscovery/nuclei-templates/tree/main/network/jarm/c2) that can be used to identify C2 services.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/6e272bce-5d33-402c-a3b4-afacbf2a8155.png)

The JARM fingerprint using cobalt-strike-c2 in the FOFA search results is as follows, and clearly, the data volume is abnormal.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451346084.png)

Here, we can also perform a JARM reverse search on the search results for app="COBALTSTRIKE-Team Server".

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/6e677858-4a2d-4136-b630-d3b76fc84d82.png)

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/8c8a265a-686a-4c21-bd8f-f0545cc94b01.png)

By comparing the data volume from the two search results here, it is clear that **the JARM fingerprint equals the C2 fingerprint ≠ service is C2**. Combining this with our previous practice, we can conclude the following: JARM is not very reliable for identifying upper-layer applications like CobaltStrike; it only serves as an auxiliary tool. In actual practice, it is necessary to combine multiple pieces of information for judgment.

### Header-based Fingerprint Association

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451363791.png)

HTTP header includes information below：

* Content-Type: The media type and encoding format that represent the content
  
* Content-Length: Represents the length of the body of the transmitted request/response.
  
* Server: Used to return the software information related to the server.
  
* header\_hash: A hash value generated by removing the value from the HTTP or HTTPS response header information.
  

E.g.:

Below is an asset discovery example shared by a security researcher 9823f\_ on Twitter: using **HEDnsExtractor** (a tool specifically used to extract packets or domains from DNS traffic) and **Httpx** (a fast web application reconnaissance tool, whose modules can efficiently handle HTTP requests and responses) with the help of analysis.

```
HEDnsExtractor -target 206.188.196.37 -silent | httpx -title -tech-detect -location -radom-agent -silent

HEDnsExtractor -target 206.188.196.37 -silent   (target the ip 206.188.196.37, obtain domain information associated with it while reducing redundant output)

httpx -title -tech-detect -location -radom-agent -silent  (Identify the title, website fingerprint, and redirect location of the domains just obtained, while randomizing the User-agent to simulate normal user behavior)

-title (Show website title)
-tech-detect (Display the website fingerprint detected based on Wappalyzer)
-location (Display the redirect location header)
-radom-agent (Proxy)
-silent (Simplified mode)
```

 The output would be：

```
http://hmaajijghahmhij.top [http://www.google.com] [Nginx 1.18.0,Ubuntu]
http://mjjagccfegadkej.top [http://www.google.com] [Nginx 1.18.0,Ubuntu]
```

Based on the results, summarize their common features:

```
Both organizations are identified as "BLNWX"
The redirect location in the HTTP response is consistently "Location: http://www.google.com"
Both servers are identified as nginx/1.18.0 (Ubuntu)
Both domains are not empty
```

Combine to form the final FOFA syntax: 

```
org="BLNWX" && header="Location: http://www.google.com" && header="Server: nginx/1.18.0 (Ubuntu)" && domain!=""
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/4544c729-cf46-4ed1-a59f-20c41fc01c0c.png)The asset discovery results in 33 independent IPs of this template type.

### Banner-based Fingerprint Association

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451481032.png)

Banner information, also known as service identification or service banner, is the initial information sent by the server to the client when a connection is established between the client and the server. This information typically includes the server's name, version number, operating system, supported protocols, etc. However, since **Banners can be manually modified, spoofed, or obfuscated, the accuracy of asset discovery and identification based solely on Banner field information cannot be guaranteed**. You can refer to the article [Practical FOFA Asset Expansion:Sidewinder APT](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Expansion-%20Sidewinder%20APT.md), which uses the combination of C2 banner and port characteristics for asset discovery.

### Port-based Fingerprint Association

Ports, as communication interfaces open on a server, often carry important network connection information. **In some cases, attackers may hard-code callback port numbers in remote access tools (RAT) or phishing emails to establish a covert connection with the target server. Additionally, in botnets, attackers may also enable protocols like Telnet, SSH, etc., to manage and control compromised devices.** Therefore, by monitoring and analyzing specific ports, potential target servers can be identified, enabling tracking and defense against these malicious activities.

E.g.: During the tracking of an APT, we found that after successfully compromising the router, the APT organization would open port 22 on the target,  and all banner information would contain the "SSH-2.0-OpenSSH\_6.7p2" characteristic. Therefore, by first obtaining the router asset and then associating it through the port, we can filter out routers that may become part of a botnet.

First, collect this batch of router assets through iocn\_hash

```
icon_hash="-1438236046"
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451532264.png)

The results clearly show false positives. At this point, we can use the Gofofa tool to bulk search for targets that match the following characteristics.

```
Port 22 is open on the target
The banner contains "SSH-2.0-OpenSSH_6.7p2"
```

The Gofofa command is generated as follows:

```
fofa search -i 1.txt --template "ip={} && port=22 && banner=SSH-2.0-OpenSSH_6.7p2" -o 2.csv

## -i (ip file that is being bulk searched)
## -template (bulk search)
## -o (output file)
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/8dc2f8a0-9fe8-4512-9613-e868ac950212.png)

The output results are the IPs that match the characteristics of compromised routers.

##  General Approach

Refer to the asset discovery approach in [Practical FOFA Asset Expansion:Sidewinder APT](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Expansion-%20Sidewinder%20APT.md)

```
IOC：
neger[.]site
semain[.]tech
```

Use FOFA to query these two domains consecutively and observe their characteristics.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/76d31a0b-fcf5-45f3-9e94-babedb17f3f2-1737450739886.png)

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/06e572d7-758a-4229-bd7a-91c86a816145.png)

During the observation process, three IOC results from FOFA were found, and the headers in all of them contained the following two characteristics:

```
HTTP/1.1 404 Not Found
Content-Type: text/html
```

However, the number of websites containing these two characteristics is too large and can be temporarily ignored. Then, it was found that information such as ASN organizations was different for each, and there was no common title or similar features. Finally, through extensive observation and finding common points, it was discovered that they share the same JARM fingerprint, which is extracted as follows:

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7"
```

Relying on a single feature for judgment leads to a large margin of error, so it is necessary to find other common features among the three IOCs. It was found that the headers of the first two domains both contain "404 Not Found", "Server: nginx", "Content-Type: text/html", and "Content-Length" values of 183 or 535 characters. These characteristics can also be added to the FOFA syntax for further querying. The current syntax is as follows:

```
jarm="3fd3fd0003fd3fd21c3fd3fd3fd3fd703dc1bf20eb9604decefea997eabff7" && header="HTTP/1.1 404 Not Found" && header="Server: nginx" && header="Content-Type: text/html" && (header="Content-Length: 183" || header="Content-Length: 535")
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/0ab1e04e-d6c2-4566-a438-f53f3417fdf5.png)

# False Positive Handling

Based on the asset discovery results above, we can perform false positive detection. If any of the following false positive scenarios occur, proceed with the false positive removal step.  After removing the false positives, perform another round of false positive detection on the current asset discovery results. Repeat this process until all detectable false positives are excluded, and output the final asset discovery results. The false positive handling process is shown in the figure below:

![image.png](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737450788282.png)

## False Positive Detection Scenarios

### Excessive Data Volume

When the number of query results reaches the scale of tens of thousands, based on the quantity alone, there is a high possibility of false positives (typically, APT organization assets do not expose that many on the internet in a short time, except for botnet assets). At this point, it is necessary to filter with other features to control the result count to a level that can be manually intervened (for example, for subsequent active probing and secondary judgment).

E.g.: simply querying ' **banner="404 Not Found"** ' yields 316493580 asset discovery results. Upon closer inspection, it was found that the selected characteristic lacks uniqueness, and there is a significant variation in the websites within the results.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/1737451573379.png)

### Obvious False Positives Present

#### Domestic IPs

Generally, VPSs used by APT organizations with foreign backgrounds are located overseas. If the asset discovery results show a certain proportion of domestic samples, it may indicate a false positive. However, special cases should not be ruled out, such as when an APT organization continues to use the current victim's machine as a puppet, which requires special judgment.

### Large Gap Between Asset Discovery Results and Organization's Activity Period

Compare the last updated time of the FOFA asset discovery result samples with the organization's activity period that we obtained. If the update time differs too much from the timing of the intelligence event, the confidence level is lower.

E.g.: When conducting asset discovery for OceanLotus in mid-October, based on the available clues, it could be inferred that this batch of samples had already been active since October. However, in the asset discovery results, we found an IP with a location in China, and the most recent update time was 2024-01-01, which is too far from our initial assumption of the activity period. Therefore, it is considered a false positive.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/a87b0cbc-e8d6-4c72-b0e9-75070fff2acf.png)

### Low/No Coverage in the Intelligence Community

Check whether the asset discovery results are tagged with the APT organization name in open-source intelligence communities.  If there are no tags or the number of tagged samples is very low compared to the asset discovery results, but other auxiliary verification methods (such as special path collisions) are available, then use other methods for verification.  If there are no other auxiliary verification methods, only compare them with the intelligence community, if the percentage of coverage is lower than 30%, then the asset discovery syntax needs to be re-verified.

**Special Case:** When asset discovery through IOC results in only three or four assets across the entire network, but only the IOC is tagged by the intelligence community with the APT organization label and none of the newly discovered assets are tagged, it may be because the number of assets is small. In this case, consider the results trustworthy, and a manual re-evaluation is required.

## Removing False Positives

The method that is prioritized to remove false positives is to readjust the FOFA fields we have summarized, with other methods used as supplementary.

### Readjust FOFA Fields or Take the Intersection

Constructing FOFA rules is not an instant process. A single feature may result in thousands of outcomes, with obvious false positives. **We need to continually adjust and refine the summarized FOFA rules based on the results. First, we should consider whether the selected FOFA fields are reasonable and whether any key information has been omitted.** For example, whether the known C2 IP has temporal characteristics, such as the time it was detected on a port or the certificate's registration time, whether the certificate issuer is from the same vendor, whether the servers used share the same characteristics, etc

For example, refer to [Practical FOFA Asset Discovery:COLDRIVER](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Discovery-%20COLDRIVER.md) on existing C2 asset discovery.

```
C2:
45.133.216[.]15：3000
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/ba0e5f68-c2a0-46c9-b21c-3eddf18eaea3.png)

Clue 1: By querying port 3000 and the banner information of the port, 17 records and 16 IPs were found.

```
banner="\x15\x03\x03\x00\x02\x022" && port="3000"
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/be1e6de1-1a52-4171-b14b-4ec87eab1d03.png)

After observing these 17 records, it was found that their ports and banner information were highly similar, but there were still obvious false positives. Therefore, simply searching through banners and ports is not enough, and further conditions need to be added. We then added certificate conditions as well and used the following FOFA syntax, which resulted in 5 records.

```
banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd"
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/92843b90-bd72-4361-aafe-ef85e85e5e29.png)

Clue 2: By searching through the certificates, we found that the certificate's Organization: Internet Widgits Pty Ltd should be the default value when configuring the certificate. Analysis revealed a key clue: the certificate validity period. We then conducted a search using the certificate's validity period and the default organization name.

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/c48e59c4-20ef-4f9b-be79-ae2c016445fa.png)

```
cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/172acf79-b789-4659-a3e0-51d816690f27.png)

By taking the intersection of Clue 1 and Clue 2, we obtained three records, which were found to be from August 2023 to October 2023. This coincides with the period that the Google Threat Analysis Group considers the organization to be active.

```
banner="\x15\x03\x03\x00\x02\x022" && port="3000" && cert="Internet Widgits Pty Ltd" && cert="2023-06-23 15:59 UTC"
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/eb7b2444-d1af-4a14-ba20-1ead4a2e829c.png)

### Path Collision

This method can be used when summarizing IOC features. If a fixed download path for downloading backdoors is found, FOFAX can be used as an auxiliary tool to make a judgment.

For example:  in the article [Practical FOFA Asset Expansion:Ducktail](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Expansion%EF%BC%9ADucktail.md), C2 asset discovery was performed. After forming the syntax based on features, it was found that the discovery results numbered in the tens of thousands. Due to the high false positive rate of this template, it is necessary to combine the paths/api/check obtained during early IOC analysis for further judgment. The path here comes from the sample's trace analysis. By tracking the initial IOC file hash and submitting it to the VT sandbox, the communication path was traced, revealing picture-based malware. By actively accessing the target, the sample was obtained and further analysis led to this address. In addition to online sandboxes, reverse engineering and active connection (trying on a virtual machine, careful to avoid command execution) can also be used to gather more information from the target.

```
IP
138.201.8[.]186
FOFA Syntax
header="404 Not Found" && header="Transfer-Encoding: chunked" && header="Server: Microsoft-IIS" && icon_hash="" && title="" && header_hash="-324809210"
```

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/28185971-04e0-40c9-bb8e-0d1b67389234.png)

Using Fofax and Httpx (It is recommended to use the officially developed tool [Gofofa](https://github.com/FofaInfo/GoFOFA)) to further remove false positives.

```
fofax -q 'header="404 Not Found" && header="Transfer-Encoding: chunked" && header="Server: Microsoft-IIS" && icon_hash="" && title="" && header_hash="-324809210"' -fs 10000 | httpx -path /api/check -sc -cl -mc 200 --ml 355

#-fs (Number of Returned Results)
#-path (Path to be Probed)
#-sc  (Display the response status code)
#-cl  (Display the response content length)
#-mc  (Match responses with the specified status code)
#-ml  (Match responses with the specified content length)
```

A batch of new live IOCs is obtained:

![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/8b1216ef-da64-4097-bbcf-fdab186f096b.png)

### Reverse Engineering

When an IOC appears in the form of a phishing website, it may contain attachments such as APK files. By performing reverse engineering on the attachments and summarizing their characteristics, analyzing the attachments from the addresses in the FOFA query results can enhance the credibility of the asset discovery results. For example, in [Practical FOFA Asset Expansion:APT-C-23 Android Malware](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Expansion-%20APT-C-23%20Android%20Malware.md), reverse engineering the APK revealed that the app checks whether another app is installed during installation.

> Jump to the place where GConfig.PT_PACKAGE_NAME is declared. The PT_PACKAGE_NAME field obtains the final package name through concatenation. After concatenation, the package name is shown as com.video.graphics. Therefore, this application will check whether another app is installed. If not, it will prompt the user to update, download com.video.graphics, and install it. Since the behavior patterns are consistent, there is no need for further analysis.
>
> ![img](https://github.com/FofaInfo/Awesome-FOFA/blob/442962de5c33479b313d4971cf377f432ba60152/Storage/Comprehensive_Guide/48c9b427-4ccd-4589-9db6-942c630e5f46.png)



### Other Scenarios

There is a scenario where, by analyzing the IOCs, it is found that all target domains are https://***.com with no subdomains. In this case, relying solely on FOFA syntax can no longer meet the requirements, so secondary filtering of the results is needed. Similar situations will certainly be encountered in actual analysis, so a case-by-case approach is required.

# Verifying Asset Discovery Results

## Statistics

Search the asset discovery results on other threat intelligence platforms to see if they are tagged with the organization's label. If a certain proportion of the results are tagged, it indicates that the asset discovery results have a certain level of credibility.

E.g.: Use the following FOFA syntax for asset discovery, and the results show 6 IPs

```
banner="HTTP/1.1 404 Not Found" && banner="Server: nginx" && (port="4443" || port="8443") && banner="Content-Length: 146" && banner="Content-Type: text/html; charset=utf-8" && jarm="21d19d00021d21d00042d43d0000005ad20eceaf7f71ae0887d2ff117bf97f" && country!="CN"
```

Verify on other threat intelligence websites, and the results are as follows:

|  ip  |  Qianxin  |  Threatbook  |  virustotal  |
| --- | --- | --- | --- |
|  103.106.231\[.\]87  |  \- |  \- |  \- |
|  103.91.67\[.\]74  |  OceanLotus  |  OceanLotus  |  Malicious  |
|  190.211.254\[.\]203  |  OceanLotus  |  OceanLotus  |  \- |
|  223.165.5\[.\]187  |  \- |  \- |  \- |
|  193.138.195\[.\]192  |  OceanLotus  |  OceanLotus  |  \- |
|  45.41.204\[.\]18  |  OceanLotus  |  OceanLotus  |  Malicious  |

This indicates that the asset discovery results from this FOFA syntax have a high level of credibility.

# Conclusions:

In this article, we have thoroughly explored key steps such as the input sources for FOFA asset discovery, sample acquisition channels, and methods for confirming IOC attribution to organizations. We have also delved into how to use the information obtained to perform more precise and efficient asset discovery operations with FOFA, including how to select appropriate features to construct FOFA syntax and how to handle potential false positives during the discovery process.

Thus, we have presented a complete guide to threat intelligence discovery using FOFA. We hope this article can help practitioners better utilize FOFA for asset discovery and information gathering, maximizing the value of FOFA.

# References:

["New Trend in MSI File Abuse: New OceanLotus Group Uses MST Files to Deliver Emotet for the First Time"](https://ti.qianxin.com/blog/articles/new%20-trend-in-msi-file-abuse-new-oceanlotus-group-first-to-use-mst-files-to-deliver-special-trojan-cn/)

https://mobile.x.com/Cyberteam008/status/1789156897318207976

https://github.com/projectdiscovery/nuclei-templates/tree/main/network/jarm/c2

https://github.com/FofaInfo/GoFOFA

[Practical FOFA Asset Expansion:Sidewinder APT](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Expansion-%20Sidewinder%20APT.md)

[Practical FOFA Asset Discovery:COLDRIVER](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Discovery-%20COLDRIVER.md)

[Practical FOFA Asset Expansion:Ducktail](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Expansion%EF%BC%9ADucktail.md)

[Practical FOFA Asset Expansion:APT-C-23 Android Malware](https://github.com/FofaInfo/Awesome-FOFA/blob/main/Basic%20scenario/Practical%20FOFA%20Asset%20Expansion-%20APT-C-23%20Android%20Malware.md)
