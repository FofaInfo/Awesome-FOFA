## **前言**

当初开发模糊搜索功能的初衷是为了解决分词不完整的问题，包括中文字符和英文字符的分词。这些分词问题可能会导致用户在搜索过程中无法搜到结果。例如，在搜索“banner”数据时，一条MySQL的banner数据中包含"ubuntu"关键词，但是由于系统正确的分词是"0ubuntu0"，导致大家搜索不到匹配结果。

令人惊喜的是，在此过程中我们发现，模糊搜索功能其实可以适用于更多特定的搜索场景。本文即将向各位FOFA工程师介绍模糊搜索的多样玩法，希望为大家带来一些便捷和启发。

## **使用场景演示**

### **1\. 基础使用方法演示**

这里我们以域名为例，如**host="test.baidu.com"**的搜索结果，针对其进行模糊匹配搜索。

```
host="test.baidu.com"
```

![1678369291_6409e20b4784556b84097.png!small](https://image.3001.net/images/20230309/1678369291_6409e20b4784556b84097.png!small)

我们需要的是，主域名为**baidu.com**但子域名包含test关键词的资产，test关键词后缀可能有其他信息，那么FOFA的搜索语句就是：

```
host*="test*.baidu.com"
```

![1678691929_640ece590a6d9d7024ced.jpg!small?1678691930508](https://image.3001.net/images/20230313/1678691929_640ece590a6d9d7024ced.jpg!small?1678691930508)

<br>
这时我们继续变更需求，需要主域名为**baidu.com**但子域名中包含test关键词的资产，test关键词前后可能有其他字符，那么FOFA的搜索语句就是：

```
host*="*test*.baidu.com"
```

![1678369313_6409e2213607f99ddaa31.png!small](https://image.3001.net/images/20230309/1678369313_6409e2213607f99ddaa31.png!small)

再变更一下目标，需要查询主域为**baidu.com**但是子域中是test+1个字符的关键词资产，那么FOFA的搜索语句是：

```
host*="test?.baidu.com"
```

![1678369327_6409e22f5f12a1c1f1da3.png!small](https://image.3001.net/images/20230309/1678369327_6409e22f5f12a1c1f1da3.png!small)

多个字符，就是添加多个?需要多少就添加多少，比如我需要子域字符为5个的，甚至可以是：

```
host*="?????.baidu.com"
```

![1678369336_6409e238942f04dbb6148.png!small](https://image.3001.net/images/20230309/1678369336_6409e238942f04dbb6148.png!small)

也可以多次进行模糊搜索，test和.baidu.com直接模糊匹配，以及后面继续接字符的资产。

```
host*="test*.baidu.com.*"
```

![1678369346_6409e2422aacda85a5428.png!small](https://image.3001.net/images/20230309/1678369346_6409e2422aacda85a5428.png!small)

也可以这样搜，我需要所有包含test关键词的域名，不管他在哪个位置，继续变更搜法：

```
host*="*test*"
```

![1678369354_6409e24a82e7c437794a8.png!small](https://image.3001.net/images/20230309/1678369354_6409e24a82e7c437794a8.png!small)

当然，这样搜的结果就是范围特别广了，这里只是作为解锁更多搜索方式为大家介绍使用，其实也可以用这样的形式对边缘资产进行搜索。

### **2. 聚焦场景演示**

我们通过前文已经了解到，他可以解决分词的问题，也了解到了模糊搜索的使用方法。那我们聚焦一个小场景，为大家展示模糊搜索的使用。

我们以mysql作为例子：mysql有多个版本存在，先在FOFA上进行搜索：

```
banner="mysql version"
```

![1678369365_6409e255280655e33fde6.png!small](https://image.3001.net/images/20230309/1678369365_6409e255280655e33fde6.png!small)

<br>
可以看到近一年有1000多万的数据，这里是搜索了所有的版本的，现在可以使用模糊搜索对于版本进行一个范围区分。

我们需要5.?.\*的所有版本，就可以这样进行搜索：

```
banner="mysql version" && banner*="5.?.*"
```

![1678369375_6409e25f7259caf22a822.png!small](https://image.3001.net/images/20230309/1678369375_6409e25f7259caf22a822.png!small)

<br>
继续缩小范围，我们仅需要版本为5.5.4+类型的数据，那可以这样进行搜索：

```
banner="mysql version" && banner*="5.5.4*"
```
<br>
![1678369386_6409e26af3efa2df90f6b.png!small](https://image.3001.net/images/20230309/1678369386_6409e26af3efa2df90f6b.png!small)

<br>
通过搜索结果展示，我们成功聚焦到了Mysql Version：5.5.4\*的各种版本，可以继续对目标进行聚焦，方法是一样的。

### **3. 更多玩法**

SNMP协议有多个版本，我想一次性找全，使用模糊搜索的语法为：

```
protocol*="snmp*"
```

![1678369396_6409e2742a6e68a873362.png!small](https://image.3001.net/images/20230309/1678369396_6409e2742a6e68a873362.png!small)

<br>
突然忘记云服务商Cloudflare的名字，使用模糊搜索，快速找到自己需要的目标：

```
cloud_name*="Cloud*"
```

![1678369404_6409e27c3952828069b31.png!small](https://image.3001.net/images/20230309/1678369404_6409e27c3952828069b31.png!small)

<br>
对于已知的搜索结果，模糊搜索端口是四位数的资产。

```
domain="fofa.info" && port*="????"
```

![1678369413_6409e285c88d55dff5211.png!small](https://image.3001.net/images/20230309/1678369413_6409e285c88d55dff5211.png!small)

## **模糊搜索是什么？**
<br>
模糊搜索是一种搜索技巧，用于在数据集中查找与查询词或关键字相似的结果。

模糊搜索可以帮助FOFA工程师更好地匹配和搜索关键词，使得搜索结果更加全面、准确。与精确搜索相比，模糊搜索更加灵活，可以处理各种不规则的输入，例如拼写错误、变体、缩写和语言差异等。

在FOFA平台中，使用通配符匹配功能就可以实现模糊搜索的效果。例如，在搜索时，可以在关键词中使用通配符“\*”和“?”来匹配不同的字符和字符串，以进行模糊搜索。相信大家在前面的演示中已经大概明白了模糊搜索的使用方法，这里再对用法进行一个说明：

在FOFA上，模糊搜索是通过在\*=后面添加通配符 \* 或 ? 来实现的，但 \*= 并不是通配符，仅仅是用来表示使用模糊搜索功能的符号。同时，通配符\*和?代表着不同的意思：

\*表示匹配的数量不受限制，可以用来代替 0 个、1 个或多个字符；

? 仅仅可替代 1 个字符。

## **总结**
<br>
FOFA的模糊搜索功能为大家提供了更多的搜索灵活性和准确性，让大家可以通过通配符的使用更加开放性地搜索到目标资产和漏洞。本文简单介绍了FOFA的模糊搜索功能，以及通过通配符进行模糊搜索的基本用法。

FOFA的模糊搜索功能仍然有很多未被探索的搜索方式等待开发。FOFA工程师们可以根据不同的需求和场景，不断尝试创新，开发出更加高效、精准的搜索方式。

个人版及以上的订阅会员或F点单功能消费就可以使用该功能哦，期待大家的脑洞！
