# python实际编写过程中的问题整理

1. 类型转换与字符串拼接（+号）字符串替换
存在：int str float 等等 (没有string函数)
```
itemid = 125845
url = 'http://item.jd.com/' + str(itemid) + '.html'

imgsrc = imgsrc.replace('/n5','/n1')
```
2. 相等是两个等号 == 
id=123
print(id==123) //true
3. 判断文件（夹）是否存在,并创建
```
if os.path.exists(filename) == True :
    //获取内容，内容为空则删除
    if len(html) == 0:
        os.remove(filename)

if os.path.exists(r'E:\pic')==False:
    os.mkdir(r'E:\pic')
```

r 普通字符串，否则\p会被转义
u 含有中文字符
b 字节

4. 读写文件
```
f = open(filename,'rt',encoding='gbk')
data = f.read()
f.close()
或者用with
with open(filename,'w',encoding='gbk') as f:
    f.write(data)
```
5. 发送请求
```
    # --------------------------------------------------------
    # requests 请求说明
    # 请求方式有get/post/put/delete
    # 可以传入的参数有,url,headers,data,timeout,cookies,files等
    # url = 'str'
    # headers = {'a':'1','b':'2'}
    # data = {'a':'1','b':'2'}
    # timeout = 3 #单位秒
    # cookies = {'a':'1','b':'2'}
    # files = {'file': open('/path/a.xls', 'rb')}
    # -------------------------------------------------
    # 请求结果后面可以跟content|text获取内容 + decode转码
    # -------------------------
    示例如下：
    get_content = requests.get(url='httpxxxx',headers={'User-Agent':'xxx'},timeout=30).text.decode('utf-8)
    post_content = requests.post(url='httpxxxx',headers={'User-Agent':'xxx'},data={'a':1,'b':2},timeout=30).text.decode('utf-8)
```
6. 正则用法
```
import re
ress = re.compile(r'url: "(.*)",id').search(txt)
if ress:
   print (ress.group())
else:
   print ("No match!!")
ress = re.search(r'url: "(.*)",id',txt).group() #返回所有匹配的字符
ress = re.search(r'url: "(.*)",id',txt).group(1) #返回正则括号中匹配的字符
ress = re.search(r'url: "(.*)",id',txt).span() #返回起始结束位置
```
默认search与match都是单行匹配，加flags=re.DOTALL实现多行匹配
`duty = re.search(r'<div class="bmsg job_msg inbox">(.*?)<div', content, flags=re.DOTALL)`

7. soup用法
```
    from bs4 import BeautifulSoup
    soup = BeautifulSoup(txt, 'lxml')
    
    soup.title.text
    soup.find('ul',class_="lh")
    soup.find_all('img')

    imgurl=[]
    for link in soup.find_all('img'):
        imgsrc = 'https:'+link.get('src')
        imgurl.append(imgsrc)
``` 
8. 数据库连接
mongodb
```
    pip install pymongo
    from pymongo import MongoClient
    conn = MongoClient('mongodb://lypeng:jobs89757Aa@119.29.52.50:27017')
    db = conn.local
```

9. 列表list操作
```
nums = ['a','b','c']
for i in nums:
    print(i)
nums.insert(3,'d') #index,value
nums.append('e')
print(nums[4])
```

10. 字典dict
```
score={'zhang':80,'liu':90,'ma':85}
score['zhang']
score.get('zhang')
score.get('li',100)
score.update({'zhang':88})
```

9. 线程与进程

10. 图形化gui编程之tkinter

11. 图像处理

12. 邮件发送

13. ...
