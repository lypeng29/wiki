# scrapy学习

`pip install scrapy`
现在装的是1.8版本的

## 最基础的爬简书标题

```python
import scrapy

class JianshuSpider(scrapy.Spider):
	"""
	获取简书首页文章标题与链接
	"""
	name = 'JianshuSpider'
	start_urls = ['https://www.jianshu.com']
	custom_settings = {
		'DEFAULT_REQUEST_HEADERS' : {
			'Referer': 'https://www.jianshu.com/',
			'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.25'
		}
	}

	def parse(self,response):
		for title in response.css(".note-list>li"):
			yield {'title':title.css('a.title ::text').get(),'href':title.css('a.title ::attr(href)')}
```

运行`scrapy runspider JianshuSpider`即可看到效果，拿到的结果可以保存txt，放到MySQL，mongodb,或者其他，你想怎么弄，就怎么处理~

## 解释：
设置好start_urls爬取的网址，custom_settings一些配置后，scrapy自动请求，返回response,交给parse函数处理

response响应对象有status,text,css,body,xpath等等属性，dir(response)

## 页面内容处理的三种方式

`title = response.css('title')`
scrapy自己的规则获取页面内容

`title = response.xpath('/title')`
xpath方法解析页面内容

`title = re.find('/<title>(.*)<\/title>/',response.text)`
正则处理

## 主动发送请求
scrapy.Request(url,callback)

## scrapy shell web_url 返回403
修改scrapy默认配置文件default_settings.py中的user-agent

## 存放到MySQL

当然可以直接在spider里面写db.insert(sql)，或者稍微复杂点使用scrapy自带的pipeline

scrapy startproject jianshu
scrapy gens

300 400 300