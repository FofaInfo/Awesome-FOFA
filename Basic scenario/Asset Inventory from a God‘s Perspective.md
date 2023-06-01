Asset Inventory from a God's Perspective
=====================================

Background Pain Points
----------------------

Asset attack surface inventory is one of the core use cases of FOFA. Whether you are a client, a service provider, or a ethical hacker FOFA engineer, asset inventory is an essential skill for using FOFA.

However, the process of conducting asset inventory requires a significant amount of time and effort, both in terms of query formulation and clue collection. Writing scripts to collect assets using FOFA can be time-consuming and labor-intensive, requiring consideration of accuracy, effectiveness, data cleansing, and the need for some development skills. Often, there is a tight deadline and a heavy workload, which calls for an efficient tool that can perform asset inventory from a God's perspective.

Function Demonstration
----------------------

Let's have a visual experience of this new perspective through the following video.

[![Attack surface inventory](https://res.cloudinary.com/marcomontalbano/image/upload/v1685626653/video_to_markdown/images/youtube--Dox1v7Dj5R8-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://youtu.be/Dox1v7Dj5R8 "Attack surface inventory")

The overall process is fast, and we can divide the process shown in the video into three main parts.

**Step 1: Assisting users in finding asset clues**

Based on the search results of the primary domain, the information is clustered from points to surfaces, expanding the results from 0 to 1. Several major categories that are most likely to generate clue information are extracted: certificate organizations, domains contained in certificates, IP ranges, domain information, and ASN organizations. They are then displayed in clusters, providing users with direct result clues.

**Step 2: Automatic aggregation of clues to consolidate the results**

Based on the input clues, related asset clues can be quickly extracted through asset clustering. Whether it's certificates or highly related IP ranges, they can be added as clues. It is important to note that these clues can be used not only for aggregation purposes but also as asset profiles. Therefore, all confirmed clues are stored in the left clue box, and users can download all selected clues.

**Step 3: Conversion from metadata to business data**

That's the download step! To handle cases of wildcard resolution and duplicates, our download function includes a deduplication feature. In the video, the second option was chosen as the default. Deduplication of downloaded data is divided into three levels:

* Level 1: No deduplication; requires all data.
* Level 2: Deduplicates duplicate assets in wildcard domain and service/protocol categories.
* Level 3: Directly retrieves IP and subdomain assets from search results.

With these three steps, asset inventory can be quickly completed. As seen in the video, the entire process is straightforward: enter the domain, add clues, download assets. Next, let's put it to the test!

Practical Comparison
--------------------

Let's take an example from HackerOne for a practical demonstration. Using PayPal as an example, we will conduct asset inventory for the company based on its primary domain, paypal.com.

Comparing the results of asset inventory with the dataset downloaded from Chaos DB:

chaos-Add represents the additional number of domains in Chaos DB, and fofa-add represents the additional number of domains from FOFA. "Same" indicates the number of domains that are the same.

[![p9zrmex.png](https://s1.ax1x.com/2023/06/01/p9zrmex.png)](https://imgse.com/i/p9zrmex)

Root domains represent the number of root domains, and from the graph, it.

However, the number of subdomains in the Chaos dataset is much higher. This is because the data downloaded from FOFA has undergone a series of cleaning processes, while Chaos DB's dataset still includes some unresolved subdomains. Therefore, we perform subdomain resolution on the Chaos DB and FOFA data again to compare them, thus eliminating the ineffective assets that cannot be resolved:

[![p9zrMFO.png](https://s1.ax1x.com/2023/06/01/p9zrMFO.png)](https://imgse.com/i/p9zrMFO)

From the above graph, we can see that after DNS resolution, the loss rate of some domains in the Chaos DB data is relatively high:

For example, the loss rate for 'paypal' is 61%, and for 'xoom.com' it is as high as 81%.

Loss Rate = (Number of domains before resolution - Number of domains after resolution) / Number of domains before resolution

FOFA's data remains relatively unchanged, so in terms of validity, FOFA's performance is quite outstanding. However, we can see that the number of subdomain data in the Chaos DB is still very high.

This is because in the normal usage process, after domain names are resolved by DNS, they still need to undergo HTTP request detection to find the target site. Therefore, we add an additional HTTP request detection layer. Let's take a look at the results after detection:

Note: Here, we use httpx based on the original domain data (not filtered by dnsx), and only detect web requests on the default ports 80 and 443. Other ports are ignored. Although FOFA provides data for other ports, for the Chaos DB dataset, more active scanning is required to discover them. Additionally, there is still a lack of port scanning on IP addresses associated with domain names. For the purpose of comparison, FOFA's data also includes only domain names, and then uses httpx for filtering, considering only the valid domain names on ports 80 and 443.

[![p9zr3SH.png](https://s1.ax1x.com/2023/06/01/p9zr3SH.png)](https://imgse.com/i/p9zr3SH)

In most cases, FOFA has a larger data volume. However, there is a significant difference in the root domain data between 'paydiant.com' and 'venmo.com'. Looking at the subdomain lists, we can see that Chaos DB has many wildcard domain resolutions or websites with the same web content. In actual bug bounty tasks, these sites are also filtered and cleaned. Therefore, we continue the data cleaning process. The following is a comparison after filtering out similar HTTP request content:

[![p9zrJOI.png](https://s1.ax1x.com/2023/06/01/p9zrJOI.png)](https://imgse.com/i/p9zrJOI)


Based on the above graph, it can be seen that after removing similar HTTP request content, in most cases, FOFA's data volume is leading.

OneForAll is another tool that automates the entire process, including DNS resolution and HTTP request steps. It also provides additional fields such as title, geolocation, etc. However, it can only discover assets for specified root domains. The comparison shown above is based on the 'paypal.com' root domain using OneForAll results with 'request=1', and after removing duplicate HTTP data. Please note that this is only using default parameters and checking ports 80 and 443.

[![p9zrN0P.png](https://s1.ax1x.com/2023/06/01/p9zrN0P.png)](https://imgse.com/i/p9zrN0P)

Using OneForAll can automate the manual steps of DNS resolution and HTTP requests mentioned above, but it also takes about half an hour to complete a single root domain like 'paypal'. In terms of efficiency, FOFA's operation process is faster and simpler.


Ideally, combining the results from various tools would provide comprehensive and complete data, but it requires more manual operations. If you need a lightweight and fast way to obtain relevant assets for a target, FOFA's attack surface Inventory is a quick, efficient, and result-rich approach. It is a passive collection method, which addresses our choice and pain points.

| Tool/Feature | FOFA Attack Surface Inventory | Chaos DB Dataset with dnsx and httpx Processing | Subdomain Collection with Amass, dnsx, and httpx Processing | OneForAll |
|:----------|:----------|:----------|:----------|:----------|
| Associated Asset Discovery Capability | 4 - Discovers more related subdomains, but may include non-target domains | 2 - Can discover the root domains of an organization, but only downloads data from the provided list | 1 - Manually specifies a single root domain | 1 - Manually specifies a single root domain |
| Subdomain Discovery Capability | 4 - More subdomains, but may miss some | 3 - Fewer valid subdomains | 4 - More subdomains, but may miss some | 4 - More subdomains, but may miss some |
| Data Quality | 4 - Higher data validity, but may include some expired data | 1 - Poor, with a higher loss rate | 1 - Poor, with a higher loss rate | 5 - Higher data validity |
| Data Depth | 5 - Provides additional fields | 1 - Only includes domain names | 1 - Only includes domain names | 3 - Provides additional fields |
| Efficiency | 5 - Simple, fast, passive collection | 1 - Requires additional processing after downloading | 1 - Longer execution time and additional processing | 3 - Active collection, automatically retrieves data for a single domain, relatively fast |
| Ease of Use | 4 - Web interface for direct operation | 4 - Direct download, but requires additional processing | 3 - Command-line operation | 3 - Command-line operation |

Based on the results, compared to other tools that obtain subdomain lists and perform DNS resolution and HTTP request checks step by step, FOFA's attack surface Inventory has the following advantages:

**1. Efficiency Advantage**

Unlike other tools that require additional manual processing after acquiring assets, which is time-consuming and labor-intensive, FOFA's attack surface Inventory significantly improves the efficiency of attack surface Inventory and reduces time costs.

**2. Data Breadth Advantage**

At each stage of data collection, DNS resolution, and HTTP access, FOFA demonstrates its extensive data collection capabilities.

Compared to open-source tools, FOFA-based tools can discover more related root domains from a single root domain clue by utilizing information such as certificate organizations, ASN organizations, IP ranges, etc., and collect more relevant IP addresses. In contrast, open-source tools usually start with a single domain name as a starting point and may not provide such comprehensive IP information. The example only compares subdomains.

**3. Data Quality and Validity Advantage**

FOFA places greater emphasis on data validity during the asset Inventory process, providing more accurate and accessible network asset information.

Compared to open-source data, FOFA-based tools only provide valid and accessible data, while domain names collected by open-source tools may include unresolved domains, internal IP addresses, or inaccessible domains, requiring additional filtering. For example, in the case of 'braintree' and 'paypal' domains, although FOFA's data volume is slightly less than Chaos DB in the raw data acquisition stage, FOFA ensures a higher volume of valid data after subsequent DNS resolution and HTTP access.

**4. Data Richness Advantage**

FOFA-based attack surface Inventory provides more asset feature data, such as ASN, geographical location, title, body, cert, etc.

The comparison tools used in this analysis have been open-sourced on GitHub, and interested users can access them for comparison.

Link: [https://github.com/FofaInfo/fofa_assets_diff](https://github.com/FofaInfo/fofa_assets_diff)

## Summary ##

By analyzing the pain points of user asset collection, we have explored three aspects: assisting users in finding asset clues, automatically aggregating and integrating the clues, and converting metadata into business data. We have developed an asset Inventory approach based on a "god's" perspective. This approach greatly simplifies the asset discovery process, improves efficiency, and helps enterprises, organizations, and individuals better manage and protect their external security threats.

Of course, this feature is currently in beta and requires F-points to use. We will continue to optimize this feature, including the data cleaning function of the downloaded data, and adjust the usage cost as necessary. We welcome users to provide feedback on its usage, and providing feedback to the bot can also earn F-points as a reward.

Easter Egg: By appending '?&fid&icon_hash&sdk_hash' to the URL, i.e., [https://en.fofa.info/extensions/assets?&fid&icon_hash&sdk_hash](https://en.fofa.info/extensions/assets?&fid&icon_hash&sdk_hash), you can unlock additional clustering clues that we have added during the exploration process.
