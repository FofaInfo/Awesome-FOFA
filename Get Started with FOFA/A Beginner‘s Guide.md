## A Beginner’s Guide

### What is FOFA? 
FOFA is a search engine for mapping the cyberspace, aimed at helping users search for internet assets on the public network. Simply put, FOFA operates like Google or Baidu, where users can input keywords to match data containing those keywords. However, unlike Google or Baidu, the data searched by FOFA includes assets such as cameras, printers, databases, and operating systems.

Cyberspace mapping can be considered a “map” of the cyberspace. Similar to how Google Maps map out terrains through satellite images, cyberspace mapping also detects global network assets through technological probes.

However, the cyberspace is divided into public and private networks. FOFA mainly detects assets on the public network, similar to the buildings (IP addresses) on Google Maps. Although the exterior structure and scale of the building can be seen, the interior cannot be understood. This is because violating the privacy of the private network is illegal.

### What can FOFA do?
One of the functions of FOFA is to help enterprises comb through their public-facing assets and protect them to the greatest extent possible. For example, just as viruses can enter the human body through multiple channels, enterprise devices may also be vulnerable to attacks by hackers. While companies can reduce their exposure by taking protective measures, such as wearing masks and protective clothing, hackers may still be able to invade the corporate network through small openings or aerosols. Similarly, enterprise devices may have multiple vulnerabilities, thereby posing a risk of being attacked by hackers.

Analogous to the way doctors detect the flu, when vulnerabilities arise, enterprises can quickly check whether their assets are affected and take timely measures to prevent the vulnerabilities from being exploited by criminals. This helps companies protect their network security.

Of course, in addition to combing through and protecting their own assets, there is also a group of people called “Ethical hacker” who use FOFA to discover vulnerabilities. They are like a hunters. When they discover a product with a universal vulnerability, they can quickly collect information on the total number of assets and regional distribution of this product across the entire network through FOFA search, assess the scope of the vulnerability’s impact, and help companies address their network security issues.

Similarly, researchers at universities or institutions can also use FOFA to quickly identify the global distribution of a particular product. For example, how many cameras from a certain manufacturer have been deployed worldwide, and how many are in each country/region. This can provide them with real data support for their research.

The FOFA platform is not only useful in the field of network security, but also for individuals looking for interesting things. The following example will demonstrate the capabilities of FOFA through a simple case.

### Syntax of FOFA

Recently, for example, with the popularity of ChatGPT, we can use FOFA to search for websites that include the keyword “ChatGPT” in the title. The search syntax can be ``` title="chatgpt" ```. 

We can then search more accurately for websites that use the ChatGPT framework, as these websites’ titles may not include the “ChatGPT” keyword.

By viewing the source code of these websites using FOFA, we discovered some common key features, such as "loading-wrap"  and "balls".

Therefore, we can use FOFA to search for websites that include both “loading-wrap” and “balls” in the body of the website, in order to find all websites that use this open-source framework. After completing this step, the search results already include websites that use the same ChatGPT open-source framework.

Next, we can add two filters: one to filter whether the website has a domain ```is_domain=true```, and the other to filter whether the certificate is validity ```cert.is_valid=true```. 

Why filter these two items? By filtering based on these criteria, we can determine whether the website is prepared for long-term operation on the public network (to ensure stability of use), otherwise, the saved results may quickly become unusable if the owner takes them offline. At this point, our search syntax is websites that include the “loading-wrap” and “balls” features in the body of the website, have a domain, and have a valid certificate.

```body="loading-wrap" && body="balls" && is_domain=true && cert.is_valid=true```

We can also filter the results based on the country/region of the IP address, for example, using the syntax && country="US", or directly clicking on the search results statistics on the left to only show websites with US IP addresses. 

The search result syntax in this case would be:
```body="loading-wrap" && body="balls" && is_domain=true && cert.is_valid=true && country="US"```.

Better to show the result by video.

[![A Beginner‘s Guide](https://res.cloudinary.com/marcomontalbano/image/upload/v1681739395/video_to_markdown/images/youtube--Nb91PSQDQG4-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://www.youtube.com/watch?v=Nb91PSQDQG4 "Get Started with FOFA")

In this demonstration, we used website title (title), website content (body), filtering syntax (is_domain, cert.is_valid), country (country), and logical operator AND (&&) as elements. 

This approach is similar to how ethical hackers use the FOFA platform to search for assets, but with a different purpose - to search for specific keywords and targets.

FOFA platform is highly flexible and can be customized for different needs, making it very convenient and practical.
Of course, the search results may also contain other relevant features, which can be used to further expand or narrow down the search scope. For example, the website title could be “ChatGPT Web” or its website favicon (icon_hash) could be used as a search parameter. These keywords can also be searched on FOFA.

We have already entered these search features, so you can quickly search for what you want by simply clicking on the corresponding filters.

![](https://github.com/githublogin0101/111/blob/main/3.png?raw=true)

And we’ll see what that is in the next chapter.

### Fingerprint Rule of FOFA

We have been mentioning something called “key features” all along. When you have a key feature to search for the information you need, you can find it using FOFA, such as IP addresses, domain names, website titles, website icons, etc.

So what does FOFA do for these features? We combine and organize multiple key features of these assets to form a set of rule fingerprints. Simply put, we have created a navigation page or a table of contents for a lot of data. This can help users find the information they need more quickly and accurately. 

For example, if you want to search for a certain brand of camera, FOFA has organized the features of these cameras and formed a navigation tag. You only need to enter, for example, Hikvision cameras in the search box, and you can find the information of cameras of various brands that FOFA has organized. This greatly saves the time and energy of users searching for the information they need. Like this, by entering “hikvision”, you can reach your goal with one click:

![](https://github.com/githublogin0101/111/blob/main/%E5%9B%BE%E7%89%872.gif?raw=true)

### Feature ID (FID)

In addition, FOFA has a great engine feature called FID, which stands for Feature ID.

We can think of it this way: everyone has their own file, which records information such as gender, age, occupation, and place of birth. Based on your ID number, we can identify you as a person.

For a website, it also has its own information. We create a file (FID) for it and put its “gender”, “age”, “occupation” and other information in the file (FID). Then we use this file to match all websites with similar features. For example, if the file (FID) shows that this person is male, 20 years old, a programmer, born in Beijing, and unmarried, then we can match all websites on the Internet with similar features.

It should be noted that the FID feature on the FOFA platform is a label formed by FOFA itself by aggregating multiple key features, rather than the website’s own tag. In the statistics on the search results page or in the data, we can see it.

Continuing with the example of title=“chatgpt”, we can see that FID has identified websites built on different frameworks with the title “chatgpt”. FID can also help you find more assets with the same features directly.

![](https://github.com/githublogin0101/111/blob/main/%E5%9B%BE%E7%89%87.gif?raw=true)

### Conclusion
By using simple and easy-to-understand language and a simple example, we have introduced the basic search capabilities, special feature rule sets, and FID of FOFA. 

Congratulations, you have become a junior FOFA engineer!

We hope this article can help readers who are interested in the FOFA platform. If you have understood the content of this article, then we look forward to you using FOFA to explore the cyberspace.

Next, you can further learn about advanced syntax usage, search tips, interesting FOFA features, API applications, or real-world usage scenarios. Let’s continue to explore together!









