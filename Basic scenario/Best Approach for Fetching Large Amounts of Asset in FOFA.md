# Best Approach for Fetching Large Amounts of Asset in FOFA

## Pain Points
Currently, the FOFA API interface we use relies on Elasticsearch's Start/Size mechanism. However, this mechanism is not very friendly for deep pagination. When users attempt to retrieve large amounts of data through multiple page requests, they may encounter issues such as network fluctuations or sluggish Elasticsearch performance, resulting in problems and the inability to fetch data in bulk. Finding an effective solution for efficiently retrieving large amounts of data is a pressing issue for us.

## Solution
Let's take a look at the current new solutions available: one for efficiently retrieving large amounts of data at once and another for accessing large amounts of data in real-time for integration purposes.

### 1. Page Download
For users who do not require real-time data and need to fetch a large amount of data at once, page download is the most stable option. It allows for downloading large datasets with a maximum of 10 million records in a single click. The download process takes approximately 1 hour and 40 minutes for 10 million records and is delivered in a compressed format. Although it takes relatively longer, it provides greater stability.

![](https://github.com/FofaInfo/Awesome-FOFA/blob/8e7780b05422abacc98a14821a25d9ab4fcd604d/Storage/search_next1.png)

Note: When exporting fields that include large fields such as banner/header/cert/body/structinfo/icon, the maximum supported download limit is 100,000 records per request.

### 2. Search After API
However, if you require large-scale, real-time data retrieval for integration purposes, the Search After API can be your choice.

When retrieving a large volume of data for the same search query, you can use the continuous pagination interface. This interface allows you to continuously fetch all the data, supports retrying failed requests, and ensures data integrity.

The difference between this interface and the basic interface is that it uses `nextid` for pagination instead of `page`. The `nextid` value is returned in each response, and if `nextid` is not passed in subsequent requests, it defaults to returning the first page of data.

Official API documentation: [FOFA API Document](https://fofa.info/api)

Dedicated API ➡️ Continuous Pagination API

Here's an example using curl:

```
curl -X GET "https://fofa.info/api/v1/search/next?qbase64=dGl0bGU9IueZvuW6piI%3D&size=10&email=your-email&key=your-key&next=id"
```

If you prefer not to make manual changes, no problem. We have already updated this interface in the official Python SDK library, which can be executed via the command line. Below are the relevant links and a demonstration:

![](https://github.com/FofaInfo/Awesome-FOFA/blob/84df55d4d17379222eb9df49209c6f4912723c8a/Storage/Image.gif)

From the animated demonstration, you can see that the SDK not only supports one-time delivery of large data export tasks but also enables real-time retrieval of dynamic assets, making it more stable than regular interfaces.

You can update and use the SDK library through the following links: [Python SDK Library](https://pypi.org/project/FOFA-py/) or [Official GitHub Repository](https://github.com/fofapro/fofa-py).

## Approach Overview
The first approach, downloading data in bulk using the page download feature, is straightforward—click to download and wait for the data to be fetched. The only

 issue to address is the compression of the downloaded package.

The second approach, fulfilling the need for integration programs to retrieve large amounts of data in batches, is more resource-intensive. However, we can impose certain limitations to prevent unnecessary time and effort. Nonetheless, prioritizing user satisfaction has always been our goal. After conducting market research and considering industry best practices, we ultimately chose the Search After solution.

Elasticsearch recommends two solutions: the Scroll API and the Search After solution.

> According to Elasticsearch's official documentation: Pagination of results can be achieved using the `from` and `size` parameters, but the cost becomes prohibitive when deep pagination is reached. The `index.max_result_window` is set to 10,000 by default as a protective measure. Search requests require heap memory and time proportional to `from + size`. While the Scroll API is recommended for deep scrolling, the overhead of maintaining scroll context makes it unsuitable for real-time user requests. The Search After parameter addresses this issue by providing an active cursor, which uses the results of the previous page to assist in fetching the next page's results. Search After is not a solution for freely jumping to random pages but allows for simultaneous scrolling of multiple queries.

Based on industry research and official recommendations, we conducted analysis and experiments. While the Scroll API requires maintaining scroll context and becomes unusable once the scroll ID expires or encounters network instability, the Search After solution does not require additional context maintenance. Therefore, considering the pain points, we ultimately selected the Search After approach.

## Final Remarks
FOFA has always adhered to a user-centric philosophy and greatly values the support of ethical hackers and industry recognition, which serve as significant driving forces for our progress. We are enthusiastic about your needs and feedback and are committed to continuously solving problems and maintaining our technological focus to help you create greater value. At FOFA, we firmly believe that user satisfaction and success are our relentless pursuits.

## References
1. [Elasticsearch Scroll API](https://www.elastic.co/guide/en/elasticsearch/reference/current/scroll-api.html)
2. [Elasticsearch Search After](https://www.elastic.co/guide/en/elasticsearch/reference/6.2/search-request-search-after.html)
3. [FOFA API Document](https://en.fofa.info/api/batches_pages)