#### python3.7 批量去除图片名中空格

![](C:\Users\wangrunfa\Pictures\Camera Roll\批注 2019-12-01 192811.png)

![](C:\Users\wangrunfa\Pictures\Camera Roll\批注 2019-12-01 192934.png)



![](C:\Users\wangrunfa\Pictures\Camera Roll\批注 2019-12-01 193004.png)

```python
# -*- coding:utf8 -*-

from pathlib2 import Path

#图片存放路径
imagePath='E:\\stuphoto\\imageTest\\'
imageSize=0
# 批量去除图片名空格
if __name__ == '__main__':
    #"*"代表所有图片，可用于筛选
    for item in Path(imagePath).rglob("*"):
        # 获取图片名
        #replace()遍历字符串 --》替换
        imgName = item.name.replace(" ","")
        #重命名图片名  路径加图片名
        item.rename(imagePath+imgName)
        print( str(imageSize) + " 张图片"" 去空格 成功: "+imgName)
        imageSize = imageSize + 1
```



直接运行报错

```python
Connected to pydev debugger (build 191.6605.12)
Traceback (most recent call last):
  File "D:\Program Files\JetBrains\PyCharm Community Edition 2019.1.1\helpers\pydev\pydevd.py", line 1741, in <module>
    main()
  File "D:\Program Files\JetBrains\PyCharm Community Edition 2019.1.1\helpers\pydev\pydevd.py", line 1735, in main
    globals = debugger.run(setup['file'], None, None, is_module)
  File "D:\Program Files\JetBrains\PyCharm Community Edition 2019.1.1\helpers\pydev\pydevd.py", line 1135, in run
    pydev_imports.execfile(file, globals, locals)  # execute the script
  File "D:\Program Files\JetBrains\PyCharm Community Edition 2019.1.1\helpers\pydev\_pydev_imps\_pydev_execfile.py", line 18, in execfile
    exec(compile(contents+"\n", file, 'exec'), glob, loc)
  File "C:/Users/wangrunfa/PycharmProjects/untitled/venv/Lib/encodings/updateImage.py", line 4, in <module>
    from pathlib2 import Path
ModuleNotFoundError: No module named 'pathlib2'
```

ModuleNotFoundError: No module named 'pathlib2'

模块未找到错误：没有名为 'pathlib2'的模块

没有安装'pathlib2'模块

解决方法，安装'pathlib2'模块

以pyCharm为例File/Settings/project:untitled/project:interpreter



![批注 2019-12-01 193900](C:\Users\wangrunfa\Pictures\Camera Roll\批注 2019-12-01 193900.png)

![](C:\Users\wangrunfa\Pictures\Camera Roll\批注 2019-12-01 194020.png)

![](C:\Users\wangrunfa\Pictures\Camera Roll\批注 2019-12-01 194226.png)

![](C:\Users\wangrunfa\Pictures\Camera Roll\批注 2019-12-01 194249.png)

再次运行代码

就哦了



# 爬取图片

```python
import requests
from lxml import etree
from urllib.request import urlopen
from bs4 import BeautifulSoup
import pymysql
import urllib
import urllib.request
import uuid
from PIL import Image

imageStoreUrl = 'E:\\stuphoto2\\'


def get_conn():
    conn = pymysql.connect(host='101.132.70.82',
                           user='root',
                           passwd='123456',
                           port=3306,
                           db='petproject',
                           charset='utf8')
    return conn


def update(sql, args, PageSize):
    try:
        conn = get_conn()
        cur = conn.cursor()
        cur.execute(sql, args)
        conn.commit()
        cur.close()
        conn.close()
    except:
        print("数据库错误！" + args)
        xunhaun(PageSize, 500);

def getHTMLText(tophtmls):
    maxTryNum = 60
    for tries in range(maxTryNum):
        try:
            kv = {"user-agent": "Mizilla/5.0"}
            response = requests.get(tophtmls, headers=kv, timeout=60)
            return response.text
        except:
            if tries < (maxTryNum - 1):
                continue
            else:
                print("Has tried %d times to access url %s, all failed!" % (maxTryNum, tophtmls))
                break


def xunhaun(kaitoushu, jieweishu):

    PageSize=0
    try:
        for xunhuanSize in range(kaitoushu, jieweishu):
            print(xunhuanSize)
            tophtmls = 'https://www.doutula.com/photo/list?page=' + str(xunhuanSize)
            PageSize = xunhuanSize+1
            ints = 0
            html = getHTMLText(tophtmls)
            bs = BeautifulSoup(html, 'html.parser')
            allHref = bs.find_all('a')
            hrefList = []
            for allHrefs in allHref:
                Hrefs = allHrefs.get('href')
                if Hrefs != None:
                    if Hrefs.find('https://www.doutula.com/photo/') != -1 and Hrefs.find('list') == -1:
                        ints = ints + 1
                        htmls2 = Hrefs
                        twohtml2 = requests.get(htmls2)
                        etree_html2 = etree.HTML(twohtml2.text)
                        titles = etree_html2.xpath('//*[@class="pic-title"]/h1/a/text()')
                        times = etree_html2.xpath('//*[@class="pic-title"]/div/span/text()')
                        url = etree_html2.xpath('//*[@class="swiper-slide"]/div[1]/table/tbody/tr[1]/td/img/@src')
                        label = etree_html2.xpath('//*[@class="pic-tips"]/a/text()')

                        stuNum = str(uuid.uuid1()).upper().replace('-', '')
                        stuNums = stuNum + '.' + url[0][-3:]

                        try:
                            request = urllib.request.Request(url[0])
                            response = urllib.request.urlopen(request)
                            get_img = response.read()
                            with open(imageStoreUrl + str(stuNums), 'wb') as fp:
                                fp.write(get_img)
                        except:
                            print('访问空')
                            urls = 'http://ww1.sinaimg.cn/large/9150e4e5gy1g8trmcege7j20t30qqgmt.jpg'
                            request = urllib.request.Request(urls)
                            response = urllib.request.urlopen(request)
                            get_img = response.read()
                            with open(imageStoreUrl + str(stuNums), 'wb') as fp:
                                fp.write(get_img)
                        finally:
                            imagess = Image.open(imageStoreUrl + str(stuNums))
                            imageHeights = imagess.size[1] / imagess.size[0]
                            #sql语句
                            sql = 'INSERT INTO t_expression(title,time,url,label,random_id,image_height,original_time) VALUES(%s,%s,%s,%s,%s,%s,NOW())'
                            dese = (str(titles[0]), str(times[0]), str(url[0]), str(','.join(label)), str(stuNums),imageHeights)
                            update(sql, dese , PageSize)
    except:
        print("循环错误！")

        xunhaun(PageSize, 500);

xunhaun(840, 1000);

```