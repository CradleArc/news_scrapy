
### python环境

```C++
conda create -n news_rec_py3 python==3.8
```

### Scrapy安装

```C++
pip install scrapy
```

#### scrapy项目结构

默认情况下，所有scrapy项目的项目结构都是相似的，在指定目录对应的命令行中输入如下命令，就会在当前目录创建一个scrapy项目

```
scrapy startproject myproject
```

项目的目录结构如下：

```C++
myproject/
    scrapy.cfg
    
    myproject/  
        __init__.py
        items.py
        middlewares.py
        pipelines.py
        settings.py
        spiders/
            __init__.py
```

- scrapy.cfg: 项目配置文件
- myproject/ : 项目python模块, 代码将从这里导入
- **myproject/ items.py: 项目items文件，**
- **myproject/ pipelines.py: 项目管道文件，将爬取的数据进行持久化存储**
- myproject/ settings.py: 项目配置文件，可以配置数据库等
- **myproject/ spiders/: 放置spider的目录，爬虫的具体逻辑就是在这里实现的（具体逻辑写在spider.py文件中）,可以使用命令行创建spider，也可以直接在这个文件夹中创建spider相关的py文件**
- myproject/ middlewares：中间件，请求和响应都将经过他，可以配置请求头、代理、cookie、会话维持等

#### spider

对于spider来说，抓取周期是这样的：

1. 首先生成对第一个URL进行爬网的初始请求，然后指定一个回调函数，该函数使用从这些请求下载的响应进行调用。要执行的第一个请求是通过调用 `start_requests()` 方法，该方法(默认情况下)生成 `Request` 中指定的URL的 `start_urls` 以及 `parse` 方法作为请求的回调函数。
2. 在回调函数中，解析响应(网页)并返回 [item objects](https://www.osgeo.cn/scrapy/topics/items.html#topics-items) ， `Request` 对象，或这些对象的可迭代。这些请求还将包含一个回调(可能相同)，然后由Scrapy下载，然后由指定的回调处理它们的响应。
3. 在回调函数中，解析页面内容，通常使用 [选择器](https://www.osgeo.cn/scrapy/topics/selectors.html#topics-selectors) （但您也可以使用beautifulsoup、lxml或任何您喜欢的机制）并使用解析的数据生成项。
4. 最后，从spider返回的项目通常被持久化到数据库（在某些 [Item Pipeline](https://www.osgeo.cn/scrapy/topics/item-pipeline.html#topics-item-pipeline) ）或者使用 [Feed 导出](https://www.osgeo.cn/scrapy/topics/feed-exports.html#topics-feed-exports) .

#### Xpath

XPath 是一门在 XML 文档中查找信息的语言，XPath 可用来在 XML 文档中对元素和属性进行遍历。在爬虫的时候使用xpath来选择我们想要爬取的内容是非常方便的。

**划重点：**

1. xpath路径表达式：**XPath 使用路径表达式来选取 XML 文档中的节点或者节点集。这些路径表达式和我们在常规的电脑文件系统中看到的表达式非常相似。节点是通过沿着路径 (path) 或者步 (steps) 来选取的。

2. 了解如何使用xpath语法选取我们想要的内容，所以需要熟悉xpath的基本语法


**环境准备：**

1. 首先Ubuntu系统里面需要安装好MongoDB数据库，这个可以参考开源项目MongoDB基础
2. python环境中安装好了scrapy, pymongo包

**项目逻辑：**

1. 每天定时从新浪新闻网站上爬取新闻数据存储到mongodb数据库中，并且需要监控每天爬取新闻的状态（比如某天爬取的数据特别少可能是哪里出了问题，需要进行排查）
2. 每天爬取新闻的时候只爬取当天日期的新闻，主要是为了防止相同的新闻重复爬取（当然这个也不能完全避免爬取重复的新闻，爬取新闻之后需要有一些单独的去重的逻辑）
3. 爬虫项目中实现三个核心文件，分别是sina.py（spider）,items.py（抽取数据的规范化及字段的定义），pipelines.py（数据写入数据库）

**项目结构：**

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211103214124327.png" alt="image-20211103214124327" style="zoom: 80%;" />



运行项目命令

```
sh run_scrapy_sina.sh
```

最终查看数据库中的数据：

<img src="http://ryluo.oss-cn-chengdu.aliyuncs.com/图片image-20211103214611171.png" alt="image-20211103214611171" style="zoom:80%;" />

### 参考资料

1. [MongoDB基础](https://github.com/datawhalechina/fun-rec/blob/master/docs/%E7%AC%AC%E4%BA%8C%E7%AB%A0%20%E6%8E%A8%E8%8D%90%E7%B3%BB%E7%BB%9F%E5%AE%9E%E6%88%98/2.2%E6%96%B0%E9%97%BB%E6%8E%A8%E8%8D%90%E7%B3%BB%E7%BB%9F%E5%AE%9E%E6%88%98/docs/MongoDB%E5%9F%BA%E7%A1%80.md)
2. [Scrapy框架新手入门教程](https://blog.csdn.net/sxf1061700625/article/details/106866547/)
3. [scrapy中文文档](https://www.osgeo.cn/scrapy/index.html)
4. [Xpath教程](https://www.w3school.com.cn/xpath/index.asp)
5. https://github.com/Ingram7/NewsinaSpider

6. https://www.cnblogs.com/zlslch/p/6931838.html

