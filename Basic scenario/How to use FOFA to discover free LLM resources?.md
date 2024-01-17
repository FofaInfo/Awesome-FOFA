![ollama1](https://github.com/FofaInfo/Awesome-FOFA/blob/08551983c9c46fe3b06f09768321d81e09ef3dda/Storage/ai_finder/ollama1.png)

# How to use FOFA to discover free LLM resources?

## Introduction

Last year, the artificial intelligence large model launched by OpenAI undoubtedly became the world's top technological innovation, which has had a profound impact on our lives. Initially, people used the API interface and open-source tools directly on the public network, creating a new trend. However, as the industry has progressed, this practice has gradually decreased, but what has emerged instead are more new open-source large model projects.

At the same time, tools that package various open source language large models are also born, and Ollama is one of the very famous ones. Developers can easily deploy and test their language models on macOS.

In most scenarios, these models are mainly used locally, but in some cases, developers may choose to deploy models or open public network access on the public network. Once public network access is opened, then we understand everything with FOFA. How to discover it?

**We will use Ollama to lead the way, and at the same time created a new LLM subject, looking forward to masters combining FOFA and Fofahub to develop more gameplay.**

## The Joy of Opening Blind Boxes

We have already automated asset extraction, survival judgment, information extraction, model selection and filling with Fofahub, and built a temporary front-end page for everyone to use.
So now you just need to follow the way in the video and click to use it. 

[![product_aggregation](https://store.fofa.info/fofahub/doc/video/ai_finder_en.jpg)](https://store.fofa.info/fofahub/doc/video/ai_finder_20240110_en.mp4)

**Note: Opening blind boxes lies in the various versions of the open source large models that are opened, so it is best to ask first when using, who are you.**

Of course, we have also summarized some currently open source large models and web front ends into the LLM subject, and there will also be some large models that can be used directly. We are also very welcome to discuss with the masters and find more models information on the public network.

![ollama2](https://github.com/FofaInfo/Awesome-FOFA/blob/0f1feb4a48950e333cba5fb7944171eada95b773/Storage/ai_finder/ollama2.png)

## Principle Analysis

The entire workflow is quite simple. We need to confirm a few steps and the effects we want to achieve: automatically judge the directory, judge the survival, get the list of models running on the server, automatically fill in the front-end HTML page, generate a usable navigation page, and achieve one-click use The logic judgment.

First, get the link field of the ollama server list on FOFA.

![ollama3](https://github.com/FofaInfo/Awesome-FOFA/blob/0f1feb4a48950e333cba5fb7944171eada95b773/Storage/ai_finder/ollama3.png)

Then add the /api/tags path to the link field, judge the survival and get the list of models running on the server.

![ollama4](https://github.com/FofaInfo/Awesome-FOFA/blob/0f1feb4a48950e333cba5fb7944171eada95b773/Storage/ai_finder/ollama4.png)

Display the server path and model list one by one, each display data corresponds to a building block on the web navigation page, and it automatically calls the address and selects and fills the surviving model when it is opened.

![ollama5](https://github.com/FofaInfo/Awesome-FOFA/blob/0f1feb4a48950e333cba5fb7944171eada95b773/Storage/ai_finder/ollama5.png)


## Conclusion

The Internet is like an endless treasure mine. Every time we discover something interesting, we can't resist the impulse to automate the process with Fofahub and share it with everyone. With just a few building blocks, we can easily achieve our goals.

We also expect this to inspire our engineers. Through FOFA, we hope they can explore more of the wonders of the Internet and discover more interesting things.