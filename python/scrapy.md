# python爬虫之scrapy框架（待完成）

待重新整理


生成项目
scrapy startproject tutorial
```
tutorial/
   scrapy.cfg
   tutorial/
       __init__.py
       items.py
       pipelines.py
       settings.py
       spiders/
           __init__.py
           baike.py
           ...
```
配置

抓取
```
#baike.py
from scrapy.spider import BaseSpider
class DmozSpider(BaseSpider):
    name = "dmoz"
    allowed_domains = ["dmoz.org"]
    start_urls = [
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Books/",
        "http://www.dmoz.org/Computers/Programming/Languages/Python/Resources/"
    ]
    def parse(self, response):
        filename = response.url.split("/")[-2]
        open(filename, 'wb').write(response.body)
```
过滤

输出

