**The correct way to use Fuzzy Search in FOFA**

**Background**

The original intention of developing the fuzzy search function was to solve the problem of incomplete word segmentation, including differnet language characters. These word segmentation issues may cause users to be unable to search for results during the search process. For example, when searching for `"banner"` data, a MySQL banner data contains the `"ubuntu"` keyword, but due to the correct segmentation of the system being `"0ubuntu0"`, it results in users unable to find matching results.

Surprisingly, during this process, we found that the fuzzy search function can actually be applied to more specific search scenarios. This article will introduce various ways of using fuzzy search to FOFA engineers, hoping to bring some convenience and inspiration to everyone.

**Scenario Demonstration:**

Basic Usage Method Demonstration:
Firstly, we take the domain name as an example, and perform a fuzzy matching search for host="test.baidu.com" search results.

`host="test.baidu.com"`
[![ppQWYb4.jpg](https://s1.ax1x.com/2023/03/13/ppQWYb4.jpg)](https://imgse.com/i/ppQWYb4)

If we need assets with the main domain baidu.com but with subdomains containing the keyword "test", where the "test" keyword suffix may have other character, FOFA query would be:

`host*="test*.baidu.com"`
[![ppQWNVJ.jpg](https://s1.ax1x.com/2023/03/13/ppQWNVJ.jpg)](https://imgse.com/i/ppQWNVJ)

If we need assets with the main domain baidu.com but with subdomains containing the keyword "test" where the "test" keyword may have other characters before or after it, the FOFA query would be:

`host*="*test*.baidu.com"`
[![ppQWJrF.jpg](https://s1.ax1x.com/2023/03/13/ppQWJrF.jpg)](https://imgse.com/i/ppQWJrF)

If we need to search for assets with the main domain baidu.com but with subdomains containing the keyword "test" followed by exactly one additional character, the FOFA query would be:

`host*="test?.baidu.com"`
[![ppQW3vT.jpg](https://s1.ax1x.com/2023/03/13/ppQW3vT.jpg)](https://imgse.com/i/ppQW3vT)

For example, if we need to search for subdomains with exactly five characters following the keyword "test", the FOFA query would be:

`host*="?????.baidu.com"`
[![ppQWGKU.jpg](https://s1.ax1x.com/2023/03/13/ppQWGKU.jpg)](https://imgse.com/i/ppQWGKU)

We can also perform multiple fuzzy searches by combining the search statements. 
For example, if we need to search for assets with subdomains containing the keyword "test" and the main domain baidu.com, and then further filter out those with any characters after "test" and ".baidu.com", we can use the following FOFA query:

`host*="test*.baidu.com.*"`
[![ppQWUa9.jpg](https://s1.ax1x.com/2023/03/13/ppQWUa9.jpg)](https://imgse.com/i/ppQWUa9)

Ohterwise, if we need to search for all domains containing the keyword "test" regardless of their position in the domain name, we can use the following FOFA query:

`host*="*test*"`
[![ppQWa5R.jpg](https://s1.ax1x.com/2023/03/13/ppQWa5R.jpg)](https://imgse.com/i/ppQWa5R)

In this case, the "*" symbol represents a wildcard, which will match any character or string before or after the keyword "test" in the domain name. The search will filter out all assets with domains containing the keyword "test", regardless of any characters before or after it.

Of course, searching for all domains containing the keyword 'test' may result in a very wide scope of results. Here, we are only introducing this search method as a way to unlock more possibilities for our users. In fact, this search method can also be used to search for edge assets.

It's important to carefully consider the search criteria and use the appropriate search methods to obtain the most relevant and accurate results. This may involve combining multiple search statements, applying filters, and refining the search criteria based on the specific goals of the search.

**Focused Scenario Demonstration**

Through the previous sections, we have learned that fuzzy search can solve the problem of incomplete word segmentation and understand how to use fuzzy search. Now, let's focus on a small scenario to demonstrate the use of fuzzy search.

Let's take MySQL as an example: There are multiple versions of MySQL, and we will conduct a search on FOFA:

`banner="mysql version"`
[![ppQWwP1.jpg](https://s1.ax1x.com/2023/03/13/ppQWwP1.jpg)](https://imgse.com/i/ppQWwP1)

As you can see, there are over 10 million results from the past year for all versions. We can now use fuzzy search to narrow down the search range by version.

For example, if we want to search for all versions matching the pattern 5.?.*, we can use the following search query:

`banner="mysql version" && banner*="5.?.*"`
[![ppQW08x.jpg](https://s1.ax1x.com/2023/03/13/ppQW08x.jpg)](https://imgse.com/i/ppQW08x)

To further narrow down the search results, we can focus on the data with a specific version, such as 5.5.4+. The search query for this requirement can be formulated as follows on FOFA:

`banner="mysql version" && banner*="5.5.4*"`
[![ppQWB26.jpg](https://s1.ax1x.com/2023/03/13/ppQWB26.jpg)](https://imgse.com/i/ppQWB26)

This technique can be applied to various scenarios and is especially useful when dealing with partial or incomplete search terms. Keep exploring and experimenting with different search queries to uncover more insights and opportunities.

**Others**

SNMP protocol has multiple versions, and I want to find them all at once using fuzzy search syntax.

`protocol*="snmp*"`
[![ppQWDxK.png](https://s1.ax1x.com/2023/03/13/ppQWDxK.png)](https://imgse.com/i/ppQWDxK)

Suddenly forgot the name of the cloud service provider "Cloudflare", use fuzzy search to quickly find the target you need.

`cloud_name*="Cloud*"`
[![ppQWyrD.png](https://s1.ax1x.com/2023/03/13/ppQWyrD.png)](https://imgse.com/i/ppQWyrD)

To search for assets with a four-digit port using fuzzy search for a known search result, you can use the following syntax in FOFA:

`domain="fofa.info" && port*="????"`
[![ppQWsKO.png](https://s1.ax1x.com/2023/03/13/ppQWsKO.png)](https://imgse.com/i/ppQWsKO)

**What is Fuzzy Search?**

Fuzzy search is a search technique used to find results in a dataset that are similar to a query or keyword. Fuzzy search can help FOFA engineers better match and search keywords, making search results more comprehensive and accurate. Compared to exact search, fuzzy search is more flexible and can handle various irregular inputs, such as spelling errors, variants, abbreviations, and language differences.

In the FOFA platform, fuzzy search can be achieved by using wildcard matching. For example, when searching, you can use the wildcard "*" and "?" in keywords to match different characters and strings for fuzzy search. It is believed that you have roughly understood the usage of fuzzy search in the previous demonstration, and here is an explanation of the usage:
On FOFA, fuzzy search is achieved by adding a wildcard "*" or "?" after "=". However, "*=" is not a wildcard, it is just a symbol used to indicate the use of fuzzy search.
At the same time, the wildcard "*" and "?" represent different meanings:

`* `represents an unlimited number of matches, and can be used to replace 0, 1, or multiple characters;

`? `can only replace one character.

**Conclusion**

FOFA's fuzzy search function provides users with more search flexibility and accuracy, allowing them to search for target assets and vulnerabilities more openly through the use of wildcards. This article briefly introduces FOFA's fuzzy search function and the basic usage of fuzzy search through wildcards.

There are still many unexplored search methods waiting to be developed in FOFA's fuzzy search function. FOFA engineers can continuously explore and innovate according to different needs and scenarios to develop more efficient and accurate search methods.
Personal members and above or F-point can use this function. We look forward to everyone's creative ideas!

Our Official URL: https://en.fofa.info

Contact us: service@baimaohui.net

Telegram: https://t.me/+-5xC1wYcwollY
