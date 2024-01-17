![ollama1](https://github.com/FofaInfo/Awesome-FOFA/blob/ab0c3fa0965d1095b66c80fddce381d28b401674/Storage/ai_finder/fofa%E6%8A%80%E6%9C%AF%E5%88%86%E4%BA%AB%E5%B0%81%E9%9D%A2.png)

# 如何使用FOFA玩转大模型

## 前言

去年，OpenAI公司推出的人工智能大模型无疑成为全球顶尖的技术革新，对我们的生活产生了深远影响。起初，大家通过API接口和开源工具直接放在公网上进行使用，开创了一种新的趋势。然而，随着行业的进步，这种做法已逐渐减少，但是，取而代之的是更多新的开源大模型项目的涌现。

同时会诞生的还有打包各种开源语言大模型的工具，Ollama就是其中非常有名的一款，开发者可以轻松在macOS上部署和测试他们的语言模型。

大多数场景下，这些模型主要在本地使用，但在某些情况下，开发者可能会选择在公网上部署模型或开放公网访问。一旦开放公网访问，那么我们用FOFA就懂得都懂了。如何玩，怎么玩？

**今天就使用Ollama进行抛砖引玉，同时创建了新的大模型专题，期待师傅们结合FOFA和Fofahub开发出更多的玩法。**


## 开盲盒的快乐
我们已经将资产提取、存活判断、信息提取、模型选择填充都使用Fofahub进行了自动化处理，并搭建了一个临时的前端页面供大家使用。
所以现在只需要按照视频中的方式，点击使用就可以了，性子急的同学已经可以去玩了。

[![product_aggregation](https://store.fofa.info/fofahub/doc/video/ai_finder_en.jpg)](https://store.fofa.info/fofahub/doc/video/ai_finder_20240110_en.mp4)

**注意：开盲盒的快乐就在于，开出来的开源大模型有各种各样的版本，所以最好在使用的时候先问一句，你是谁。**

当然，我们也总结了一些目前开源的大模型和web前端到大模型专题中，里面也会有一些可以直接用的大模型哦，我们也十分欢迎和师傅们一起进行探讨，找到更多存在于公网上的大模型信息。

![ollama2](https://github.com/FofaInfo/Awesome-FOFA/blob/0f1feb4a48950e333cba5fb7944171eada95b773/Storage/ai_finder/ollama2.png)


## 原理解析

整个工作流的流程还是比较简单的。我们需要确认的几个步骤以及想要达到的效果：自动进行目录判断、存活判断、获取到服务器上运行的模型列表、自动在前端HTML页面填充，生成可使用的导航页面，达到一键使用的逻辑判断。          

首先获取FOFA上的ollama服务器列表的link字段。 
         
![ollama3](https://github.com/FofaInfo/Awesome-FOFA/blob/0f1feb4a48950e333cba5fb7944171eada95b773/Storage/ai_finder/ollama3.png)

再对link字段添加 /api/tags 路径，判断存活并获取服务器上运行的模型列表。

![ollama4](https://github.com/FofaInfo/Awesome-FOFA/blob/0f1feb4a48950e333cba5fb7944171eada95b773/Storage/ai_finder/ollama4.png)

将服务器路径与模型列表进行逐个展示，每个展示数据在 web navigation 页对应一个积木块，点开时自动进行调取地址和存活模型的选择和填充。          

![ollama5](https://github.com/FofaInfo/Awesome-FOFA/blob/0f1feb4a48950e333cba5fb7944171eada95b773/Storage/ai_finder/ollama5.png)

          

## 总结   

互联网犹如一座无尽的宝藏矿山，每一次我们发掘出有趣的发现，都无法抵挡住利用Fofahub将其流程自动化并与大家共享的冲动。仅需几个积木块，我们就能轻松实现目标。

我们也期待这能激发我们的工程师们的灵感，通过FOFA，我们希望他们能探索更多互联网的奇妙之处，挖掘出更多的有趣之物。
