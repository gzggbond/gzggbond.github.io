---
title: Scrapy爬虫基础
date: 2025-01-23 18:41:49
excerpt: 将整个互联网看作是蜘蛛网，各网站数据为猎物，程序员为狩猎者，因此爬虫顾名思义即程序员通过各种手段获取互联网上的数据，Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架
tags:
  - 数据分析
categories:
  - [Python,数据分析]
---

# 1. 爬虫简介

将整个互联网看作是蜘蛛网，各网站数据为猎物，程序员为狩猎者，因此爬虫顾名思义即程序员通过各种手段获取互联网上的数据。

# 2. urllib

urllib 是 python 标准库种用于发送网络请求的库，掌握基本命令可以便于批量获取网络数据

## 2.1 访问服务器

```python
import urllib.request
url = 'http://www.baidu.com'
# 模拟浏览器访问（返回的是HTTPResponse对象）
response = urllib.request.urlopen(url)
# 获取网页内容，读取到的是二进制数据，通过decode解码为utf-8形式
# response还有readlines，getcode，geturl，getheaders等方法
html = response.read().decode('utf-8')
```

## 2.2 下载资源

> 传入资源地址， 下载图片，视频等资源

```python
import urllib.request
# 图片地址
url_retrieve = 'https://img1.baidu.com/it/u=959337756,4186275445&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=889'
# 下载图片并存储到本地，重命名为1.jpg
urllib.request.urlretrieve(url_retrieve, '1.jpg')
```

## 2.3 自定义请求对象

**为什么需要？**

https 请求头部中有 User Agent 字段，使得服务器能够识别客户使用的操作系统及版本，CPU 类型、浏览器及版本等信息，而当使用脚本发出普通请求时，这一字段为空，此时服务端返回的响应内容会有所保留。使用自定义请求对象添加此部分信息即可解决问题。

User Agent 字段获取方式如下

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501021717345.png" alt="image-20250102171743218" style="zoom: 80%;" />

```python
import urllib.request
url = 'https://www.baidu.com'
header = {
    'User-Agent': 'Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Mobile Safari/537.36'}
# 自定义请求对象，post请求数据由data关键字传入字典
request = urllib.request.Request(url, headers=header)
# 发送请求
response = urllib.request.urlopen(request)
# 读取响应内容
content = response.read().decode('utf-8')
```

## 2.4 请求编码

> 在 request 中传入中文无法被识别，因此需要将文本转换为 Unicode 格式再传入

```python
import urllib.parse
# 得到周杰伦的unicode编码
name = urllib.qarse.quote('周杰伦')
# 转换多个参数且用&连接【可直接与get请求拼接】
data = {
    'wd':'周杰伦',
    'sex':'男'
}
# wd=xxxx&sex=yyyy
# 如果用于post请求则还需要使用encode('utf-8')转为字节流
a = urllib.parse.urllencode(data)
```

## 2.5 代理服务器

> 当一个IP地址短时间内多次向服务器发送请求时，可能会被拉黑，此时可以使用代理服务器模拟多用户，实现反爬

```python
import urllib.request
url = 'http://www.baidu.com/s?wd=IP'
header = {
    'User-Agent': 'Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Mobile Safari/537.36'
}
# 云平台查询得到的IP地址【百度搜索“快代理”】
proxies = {
    'http': '8.130.71.75:3128'
}
request = urllib.request.Request(url, headers=header)
# handler可以自定义更复杂的头部信息，此处添加访问IP地址
handler = urllib.request.ProxyHandler(proxies=proxies)
# handler固定使用步骤：handler,builder_opner,open
opener = urllib.request.build_opener(handler)
# 发送请求
response = opener.open(request)
# 读取响应内容
content = response.read().decode('utf-8')
with open('main.html', 'w', encoding='utf-8') as f:
    f.write(content)
```

若手上有大量IP地址，可以通过代理池的方式随机选择访问IP，防止地址被封

```python
proxies_pool = [{'http': '8.130.71.75:1111'},{'http': '8.130.71.75:2222'}]
proxies = random.choice(proxies_pool)
```

# 3. 解析网页

## 3.1 XPath

> Python 使用 XPath 提取网页目标元素依赖于 lxml 库

```python
from lxml import etree
# 解析本地的html文件
html_tree = etree.parse('xx.html')
# 解析服务器响应的response
html_tree = etree.HTML(response.read().decode('utf-8'))
# xpath路径解析后结果，返回一个列表
res = html_tree.xpath('xpath路径')
```

## 3.2 JSonPath

> Python使用 jsonpath 第三方库提取 json 格式响应数据，jsonpath只能解析本地文件

```python
import json 
import jsonpath
# 加载json数据
obj = json.load('xxx')
# 具体语法使用时查询即可
res = jsonpath.jsonpath(obj,'jsonpath语法')
```

## 3.3 BeautifulSoup

> bs4 库作用与 lxml 库类似，但是效率低，优点是接口设计更符合使用习惯

```python
from bs4 import BeautifulSoup
# 服务器响应的文件生成对象
soup = BeautifulSoup(response.read().decode(),'lxml')
# 本地文件生成对象
soup = BeautifulSoup(open('xx.html'),'lxml')
```

**常用方法**

```python
# 找到第一个<a>
soup.a
# 返回第一个<a>标签的所有属性，以字典的形式存储
soup.a.attrs
# 返回当前定位元素中value属性值
soup.attrs.value

# 找到第一个title为xxx且classw为yyy的<a>
soup.find('a',title='xxx',class_='yyy')
# 找到所有<a>和<span>标签，只返回前两个
soup.find_all(['a','span'],limit=2)

# 返回所有<a>，逗号分隔拼接可以获取多个不同标签
soup.select('a')
# 支持类选择器【.】,Id选择器【#】以及属性选择器
# 支持层级选择器【空格是不严格子代，>是严格子代】
# 查找拥有id的属性的<li>标签，也可以指定id为具体值【用双引号】
soup.select('li[id]')

# 获取节点文本内容
obj.get_text()
# 通过key,value的格式可以获得对应属性值
obj['name']
```

# 4. UI自动化

> 有些网页当使用请求的方式无法获得所有数据，因此需要使用UI自动化的方式获取目标数据

Selenium 提供了 UI 自动化方案，但是加载页面效率较低，可以考虑使用无浏览器界面的自动化

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
import time
# 获得浏览器对象
def share_brower(browser_path='D:\Chrome\Google\Chrome\Application\chrome.exe'):
    # 驱动路径
    option = Options()
    # 设置浏览器启动地址
    option.binary_location = browser_path
    option.add_argument('--headless')  # 无头模式
    option.add_argument('--disable-gpu')  # 禁用gpu加速
    # 创建浏览器对象
    browser = webdriver.Chrome(options=option)
    return browser

# 上网
url = 'http://www.baidu.com/'
browser = share_brower()
browser.get(url)
# 截图
browser.save_screenshot('baidu.png')
```

# 5. requests库

> requests 是一个用于发送请求的第三方库，相对于 urllib 而言操作更加便捷

```python
# request 发送请求后得到的返回值记为 r
# 得到返回文本
r.text 
# 定制编码方式
r.encoding 
# 获取请求的ur1
r.url
# 返回文本的字节类型
r.content
# 响应的状态码
r.status_code
# 响应的头信息
r.headers
```

**发送请求样例**

> 若要保证多个请求在同一个域里，可以通过获取 session 对象再进行请求调用

```python
import requests
url = 'https://www.baidu.com/s'
# 请求头
headers = {
    'User-Agent': 'Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/102.0.0.0 Mobile Safari/537.36'
}
# 请求参数
data = {
    'wd': '广东'
}
# 传入，post请求中params对应是data关键字
r = requests.get(url, params=data, headers=headers)
# 指定编码
r.encoding = 'utf-8'
print(r.text)
```

**开启代理**

```python
# 只需要准备好ip和端口，传入proxies参数即可
proxy = {
    'http':'xxx.xxx.xxx.xxx:yyy'
}
request.get(url,params,headers,proxies=proxy)
```

# 6. scrapy库

> Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。可以应用在包括数据挖掘、信息处理或存储历史数据等一系列的程序中

## 6.1 基础结构介绍

```shell
# 创建爬虫项目，名称为scrapy_test_project
scrapy startproject scrapy_test_project
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501201612696.png" alt="image-20250120161213582" style="zoom:50%;" />

**项目组成介绍**

```python
项目名称
	项目名称
    	spiders
        	__init__.py
            自定义爬虫文件		# 实现爬虫核心功能的文件
    __init__.py
    items.py				# 定义数据结构的地方，是一个继承自scrapy.Item的类
    middlewares.py			# 中间件，代理
    pipelines.py			# 管道文件，里面只有一个类，用于处理下载数据的后续处理，默认是300						      优先级，值越小优先级越高（1-1000）
    settings.py				# 配置文件，比如是否遵守robots协议等
```

**进入到 spiders 目录下，生成爬虫文件**

```shell
# 创建一个叫baidu的爬虫，用于爬取www.baidu.com内容
scrapy genspider baidu www.baidu.com
# 执行爬虫【在根目录下的settings中注释ROBOTSTXT_OBEY = True】
scrapy crawl baidu
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501201633157.png" alt="image-20250120163346089" style="zoom: 50%;" />

**response的属性和方法**

```python
# 获取响应的字符串
response.text	
# 获取的是二进制数据
response.body
# xpath过滤
response.xpath()
# 提取selector对象的data属性值
response.extract()
# 提取selector列表的第一个数据
response.extract_first()
```

**执行过程简介**

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501201714364.png" alt="image-20250120171422288" style="zoom:67%;" />

```shell
# 开启调试模式，可在控制台查看response相关信息
scrapy shell 目标网站
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501210951574.png" alt="image-20250121095130473" style="zoom: 50%;" />

**settings.py 日志设置**

```python
# 设置日志显示等级(CRITICAL,ERROR,WARNING,INFO,DEBUG)
# 默认等级是DEBUG，只要出现了DEBUG或以上等级的日志都会被打印
LOG_LEVEL = DEBUG
# 将屏幕显示的信息全部记录到文件中，屏幕不再显示，文件以.log结尾
LOG_FILE = xxx.log
```

## 6.2 爬取当当网数据实战

> 我们的目标是获取[青春爱情文学](https://category.dangdang.com/pg1-cp01.01.02.00.00.00.html)前三页的所有书名，图片与价格信息

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501221423334.png" alt="image-20250122142350141" style="zoom:50%;" />

1. 创建爬虫项目，手动创建 books 以及 books/img 目录方便后续存储相关信息

   ```shell
   # 创建名为scrapy_dangdang的爬虫项目
   scrapy startproject scrapy_dangdang
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501221438411.png" alt="image-20250122143832360" style="zoom: 33%;" />

2. 编辑数据结构文件 items.py ，将需要存储的变量都交由框架管理

   ```python
   import scrapy
   class ScrapyDangdangItem(scrapy.Item):
       # define the fields for your item here like:
       # name = scrapy.Field()
       # 要下载的数据都有什么
       # 图片
       src = scrapy.Field()
       # 名字
       name = scrapy.Field()
       # 价格
       price = scrapy.Field()
   ```

3. 在 spiders 目录下创建爬虫核心文件，指明初始页面并命名为 dangdang

   ```shell
   scrapy genspider dangdang https://category.dangdang.com/pg1-cp01.01.02.00.00.00.html
   ```

   根据处理逻辑编辑该文件

   ```python
   import scrapy
   from scrapy_dangdang.items import ScrapyDangdangItem
   class DangdangSpider(scrapy.Spider):
       # 爬虫名称
       name = "dangdang"
       allowed_domains = ["category.dangdang.com"]
       start_urls = ["https://category.dangdang.com/pg1-cp01.01.02.00.00.00.html"]
       # 需要爬取的文章页码
       page = 1
       # 执行爬虫文件时自动调用此方法
   
       def parse(self, response):
           # 根据调试网页获得的目标xpath路径
           # src = '//ul[@class='bigimg']//li/a[1]/img/@data-original'
           # alt = '//ul[@class="bigimg"]//li/a[1]/img/@alt'
           # price = '//ul[@class='bigimg']//li/p[@class='price']/span[1]/text()'
           li_list = response.xpath('//ul[@class="bigimg"]//li')
           for li in li_list:
               # 懒加载图片，data-original属性值是真实地址
               src = li.xpath('.//a[1]/img/@data-original').extract_first()
               # data-original不存在时，使用src数属性值作为地址
               if src is None:
                   src = li.xpath('.//a[1]/img/@src').extract_first()
               name = li.xpath('.//a[1]/img/@alt').extract_first()
               price = li.xpath(
                   './/p[@class="price"]/span[1]/text()').extract_first()
               # 将解析的目标值封装并交由管道处理
               book = ScrapyDangdangItem(src=src, name=name, price=price)
               yield book
           # 爬取前3页的数据
           if self.page <= 2:
               self.page += 1
               # 新页面地址
               url = f'http://category.dangdang.com/pg{self.page}-cp01.01.02.00.00.00.html'
               # scrapy.Request是scrapy的get请求，callback传入需要调用的下一个函数
               # 此处是向url发送get请求后重新调用parse方法
               yield scrapy.Request(url, callback=self.parse)
   ```

4. 编辑管道文件 pipelines.py，用于下载处理爬虫所返回的数据

   ```python
   # useful for handling different item types with a single interface
   from itemadapter import ItemAdapter
   import urllib.request
   # 用于存储json文件
   class ScrapyDangdangPipeline:
       # 在爬虫文件开始前执行的方法
       def open_spider(self, spider):
           self.res = []
       def process_item(self, item, spider):
           # 将单引号替换为双引号
           temp = str(item).replace('\'', '\"')
           self.res.append(temp)
           return item
       # 在爬虫文件结束后执行的方法
       def close_spider(self, spider):
           with open('./books/books.json', 'w', encoding='utf-8') as f:
               # 以逗号作为分隔符将元素拼接并写入json文件
               f.write(f'[{",".join(self.res)}]')
   
   # 用于处理图片文件
   class DangDangDownloadPipeline:
       # 在爬虫文件开始前执行的方法
       def open_spider(self, spider):
           pass
       def process_item(self, item, spider):
           pic_url = 'http:' + item.get('src')
           pic_name = './books/img/' + item.get('name') + '.jpg'
           urllib.request.urlretrieve(pic_url, pic_name)
           return item
       # 在爬虫文件结束后执行的方法
       def close_spider(self, spider):
           pass
   ```

   管道处理设计完毕后，还需要配合 settings.py 设置管道除了优先级，同时记得注释ROBOTSTXT_OBEY = True【君子协议，为 True 则不允许爬取特定网站】

   ```python
   ITEM_PIPELINES = {
       # 值越小，管道优先级越高
       "scrapy_dangdang.pipelines.ScrapyDangdangPipeline": 300,
       "scrapy_dangdang.pipelines.DangDangDownloadPipeline": 301,
   }
   ```

5. 进入 spiders 目录，执行爬虫文件，完成资源爬取

   ```shell
   scrapy crawl dangdang
   ```

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501221508456.png" alt="image-20250122150853367" style="zoom:67%;" />

   <img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501221508184.png" alt="image-20250122150805960" style="zoom: 33%;" />

## 6.3 post请求处理

先前使用 start_urls 的方式指定初始访问界面，但在post请求中由于需要请求头，因此无法使用这种方式，所以针对post请求我们需要重新定义一种方式进行处理

```python
import scrapy
import json

class TransPostSpider(scrapy.Spider):
    name = "trans_post"
    allowed_domains = ["fanyi.baidu.com"]
    # post请求需要指定请求体，start_urls无法做到
    # start_urls = ["https://fanyi.baidu.com/mtpe-individual/multimodal#/"]
    # 自定义请求体发送post请求

    def start_requests(self):
        url = 'https://fanyi.baidu.com/sug'
        data = {
            'kw': '你好'
        }
        # scrapy中的post，callback指定请求完毕后后续处理的函数
        yield scrapy.FormRequest(url, formdata=data, callback=self.parse)

    def parse(self, response):
        content = json.loads(response.text)
        print(content)
```

# 7. CrawlSpider

CrawlSpider 继承至 scrapy.Spider，在解析网页内容的时候，它可以根据链接规则提取出指定的链接，然后再向这些链接发送请求，非常适用于爬取网页后需要提取链接进行二次爬取的情况。

**其爬虫类创建命令稍有不同**

```shell
# 创建爬虫项目
scrapy startproject scrapy_dushu
# 爬虫类名称为read，爬取网站为www.dushu.com
scrapy genspider -t crawl read https://www.dushu.com/book/1107_1.html
```

**使用链接提取器过滤得到目标链接**

```python
scrapy.linkextractors.LinkExtractor(
	allow=(),	# 通过正则表达式过滤
    restrict_xpaths=(),	# 通过xpath过滤（定位到a元素即可）
)
```

## 爬取读书网实战

> 我们的目标是获取[计算机网络/读书网](https://www.dushu.com/book/1107_1.html)首页所能看见页面里所有书名，图片链接信息

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501231054593.png" alt="image-20250123105450352" style="zoom:67%;" />

除了爬虫核心函数有所不同，其余setting，pipeline，items的用法类似，此处不再赘述，根据需求自行修改即可

```py
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from scrapy_dushu.items import ScrapyDushuItem


class ReadSpider(CrawlSpider):
    name = "read"
    allowed_domains = ["www.dushu.com"]
    start_urls = ["https://www.dushu.com/book/1107_1.html"]

    rules = (Rule(
        LinkExtractor(
            # 正则表达式写法
            # allow=r'/book/1107_\d+\.html',
            # xpath写法
            restrict_xpaths=("//div[@class='pages']/a")
        ),
        # 重复调用的函数名称
        callback="parse_item",
        # 为False时不会在新页面中再次调用匹配规则，即最初过滤得到的链接不会实时变动
        follow=False),)

    def parse_item(self, response):
        # 定位到所有图片
        img_list = response.xpath(
            "//div[@class='book-info']//a/img")
        for img in img_list:
            # 获取图书名称
            name = img.xpath("./@alt").extract_first()
            # 获取图书封面图片地址
            src = img.xpath("./@data-original").extract_first()
            if src is None:
                src = img.xpath("./@src").extract_first()
            # 封装为数据结构交由管道处理
            book = ScrapyDushuItem(name=name, src=src)
            yield book
```

<img src="https://z-cloud-pic-1313046262.cos.ap-guangzhou.myqcloud.com/img/202501231109115.png" alt="image-20250123110949980" style="zoom: 50%;" />

