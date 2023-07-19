Unleash the Power of Workflow: Unleash Your Creativity in Open Site Discovery
==================================================================

Background Overview
-------------------

As we all know, when conducting asset collection, FOFA engineers need to process the extracted metadata from FOFA to generate the required business data. In this process, various needs may arise, such as detecting if a site is alive or quickly determining page snapshots. There are also many open-source tools available on the market to assist in data cleaning and processing.

So, how can we quickly make use of FOFA in one go? Today, we will introduce a well-known tool - workflows. Through an example of a "Open Site Discovery" workflow, we will show you how you can freely process and manipulate data based on FOFA's data to obtain what you want.

Open Site Discovery
----------------

In the Open Lab section of the FOFA personal center, you can find this feature. Let's watch a video demonstration.

[![Open site discovery](https://res.cloudinary.com/marcomontalbano/image/upload/v1686814454/video_to_markdown/images/youtube--_diL4pUOK4A-c05b58ac6eb4c4700831b2b3070cd403.jpg)](https://youtu.be/_diL4pUOK4A "Open site discovery")

In this workflow, what steps did we take? By entering the query statement into the "Open Site Search" feature, you can quickly filter out usable websites. By clicking on the URL information on the right, you can navigate to the corresponding page.

In the example shown in the video, how did we achieve and obtain directly usable results? **1\. Website Availability Test; 2. Determining if login/activation/payment is required; 3. Page screenshot; 4. Generating a personalized navigation.** All in one go, collecting directly usable sites. Next, I will analysis this workflow and explain what each step is for, and what needs to be adjusted if you change the target to generate your desired open site search.

Full Workflow Analysis
-------------------------------

This is the complete workflow for open site discovery, consisting of a total of 11 assembled modules.

![pCu2TsS.png](https://s1.ax1x.com/2023/06/15/pCu2TsS.png)

### 1\. Module: Data Import

The first part is the easiest to understand, importing data from FOFA, which can be configured to include:

* FOFA search query;
* Fields to be imported;
* Amount of data to be imported;

In the example shown, the FOFA search query is `fid="qObSo5JrR9FIkIqprX7XKg=="`, the field to be imported is the host field, and 20 records are imported.

![pCu2xzV.png](https://s1.ax1x.com/2023/06/15/pCu2xzV.png)

We can see that a "Format to URL" module is also integrated below, which is primarily used to format the host field obtained from FOFA into URL format. Here, it is optional which field you want to format as URL format.

### 2\. Module: Website Availability Test

The second part primarily focuses on live or not of the target sites, composed of HTTP requests and ZQ syntax.

In the HTTP Request module, the first step is to set the individual asset request time in the top-right settings button, **default is 10 seconds**. The URI is the destination address for the HTTP request, and the request content is the value corresponding to the host field obtained from the FetchFofa block. The request method is GET.

Lastly, fill in the required request headers: `{"User-Agent": "Mozilla/5.0 (platform; rv:geckoversion) Gecko/geckotrail Firefox/firefoxversion"}` This completes the configuration before sending the request. **In general, no modifications are needed.**

![pCuRPZ4.png](https://s1.ax1x.com/2023/06/15/pCuRPZ4.png)

The accompanying ZQ syntax is:

`where http_status_code==200 | drop http_results, http_result, http_status_code`

This means to retrieve assets with a HTTP status code of 200 and remove redundant fields.

This module is already a standard template for Website Availability, so for most cases, the only part that needs modification is the debugging of the request time.

### 3\. Module: Text Detection

The third part involves dynamic rendering and text detection.

#### 3.1 Page Rendering

Why do we need to render HTTP pages? This is because modern web pages often contain a large amount of dynamic content that is generated on the client-side (i.e., the browser) using JavaScript. When making an HTTP request, by default, only the raw HTML content is obtained without executing JavaScript or performing rendering. This can result in incomplete or missing dynamically generated content.

By rendering HTTP pages, we can simulate user behavior in a browser, including executing JavaScript code, loading dynamic resources, and filling out forms. This ensures that the obtained web page content is consistent with what is seen in an actual browser, including all dynamic content and interactive functionality.

The rendering module also allows for setting the default rendering time. If the rendering time is too short, it may lead to the following issues:

**Incomplete dynamic content:**

Some web pages generate content dynamically using JavaScript. If the rendering time is too short, it may not fully execute the JavaScript code or load dynamic resources, resulting in incomplete web page content. This means that we may miss certain portions of content that are dynamically generated. In this case, we may miss invalid websites that trigger a 403 error.

**Missing critical information:**

Some web pages use JavaScript to fill out forms, load asynchronous data, or perform other crucial operations. If the rendering time is too short, these operations may not be executed, resulting in missing critical information that could affect subsequent data extraction and analysis tasks.

**Incorrect page structure:**

Certain web pages may modify the page structure or styles dynamically using JavaScript after the page has finished loading, such as adding, removing, or hiding elements. If the rendering time is too short, these dynamic changes may not be captured, leading to errors or inaccuracies when parsing the web page content.

For each specific target, the appropriate rendering time needs to be determined based on specific requirements and the characteristics of the target website. In some cases, a shorter rendering time may be sufficient to obtain the required information, while in other cases, a longer rendering time may be needed to ensure the retrieval of complete dynamic content. Experimentation and adjustments are necessary for each specific crawling task to find the optimal rendering time.

Therefore, we have included the rendering time configuration in the settings button next to "Render DOM," allowing users to modify the rendering time and request timeout according to their specific needs.

#### 3.2 Text Classification

In the text classification module, the field name for saving is called "not\_valid," which translates to "invalid sites" in Chinese.

Now, let's define what constitutes an invalid site:

* Sites that cannot display an AI chatbox
* Sites that cannot be accessed
* Sites that display errors, including 500, 403, and other error messages
* Sites that require login, invitation codes, or CAPTCHA verification

**To summarize, for us, invalid sites are those that cannot be freely used.**

Text classification serves as a tool for filtering out invalid sites.

![pCuRQdH.png](https://s1.ax1x.com/2023/06/15/pCuRQdH.png)

We can click the filter button at the right end of the "filters" row to filter out assets that contain the specified keywords. In the current example, when the mentioned keywords appear, we mark the site as not directly usable and exclude it.

![pCubvWD.png](https://s1.ax1x.com/2023/06/15/pCubvWD.png)

Similarly, we add a ZQ syntax module for assistance. The ZQ syntax is:

`where not_valid!="true" | drop not_valid, rendered`

This means that assets containing the filtering field will be directly removed, while the remaining assets will be kept.

This module is very useful and can also be separated to filter out junk assets, for those who understand.

**Further Optimization of Text Classification**

However, excluding only invalid sites is not ideal. After each execution, there are always a few websites that take a long time to load, no matter how long we wait.

To further filter out these sites, we added a check to the "not\_valid" field to determine if it is "false," indicating a valid site.

After going through all the checks for whether a page is valid or not, what remains are either empty pages or valid pages. Therefore, in the final ZQ statement, we filter by "not\_valid != 'true'". Of course, it can also be changed to "not\_valid == 'false'" for a more refined result.

### 4\. Module Split: Page Screenshot

The fourth part involves taking page snapshots. After completing the alive check and filtering out invalid assets, the next step is to take page snapshots of the remaining assets for identification purposes. Similarly, the default time for page snapshots can be modified according to your needs.

Assisting ZQ syntax:

`title:=screenshot.title | screenshot_base64:=screenshot.base64 | drop screenshot | url:=host | drop host`

This means that the output block from the "Screenshot" module is formatted into the fields required by "WebNavigation."

Assisting JQ syntax:

`.lastupdatetime= (now | strftime("%F %T"))`

This saves the current page snapshot time to the text for easy identification.

[![pCuqmlQ.png](https://s1.ax1x.com/2023/06/15/pCuqmlQ.png)](https://imgse.com/i/pCuqmlQ)

### 5\. Module Split: Data Output

The final data output module provides the option to output the data in the default format of a web navigation. However, you can also switch to output as an Excel spreadsheet by toggling the switch in the left panel. This step is quite self-explanatory!

[![pCuqlT0.png](https://s1.ax1x.com/2023/06/15/pCuqlT0.png)](https://imgse.com/i/pCuqlT0)

Let's mention that there is an auxiliary syntax that appears throughout the workflow, which is ZQ. The official website for ZQ syntax is: [https://zed.brimdata.io/docs](https://zed.brimdata.io/docs)

Interested users can study it in advance, or you can wait until we provide pre-built modules that can be directly used.

Conclusion
----------

By understanding the principles of the Open Site Discovery workflow, congratulations, you have mastered the workflow! Because it's really simple!

In fact, this workflow for Open Site Discovery Search already includes several scenarios that can be individually split and used, such as performing alive checks, keyword filtering, and data deduplication. I believe some users have already customized their workflows to perform various operations.

In the future, we will also provide explanations for individual functional components, splitting them into separate scenarios to help you better utilize the workflow!

Regarding the current billing model, executing a workflow deducts 50 F points per minute, but there is no charge if the total execution time is less than 10 seconds. When the FOFA data retrieval exceeds 100 queries, your own FOFA API key will be used for retrieval.

For users with Business and Corporate memberships, we offer monthly free credit allowances that are automatically refreshed every month. Both membership types receive free credits each month, allowing you to freely explore all the features of the OpenAI Lab. We wish you all a pleasant experience in [https://fofa.info](https://en.fofa.info)

