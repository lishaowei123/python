# python
百度电影

#!/usr/bin/python
# -*- coding: UTF-8 -*-

import requests
from lxml import etree
import threading
import urllib
import re
import time
import random
import json
import hashlib
_agent = [
    "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; AcooBrowser; .NET CLR 1.1.4322; .NET CLR 2.0.50727)",
    "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0; Acoo Browser; SLCC1; .NET CLR 2.0.50727; Media Center PC 5.0; .NET CLR 3.0.04506)",
    "Mozilla/4.0 (compatible; MSIE 7.0; AOL 9.5; AOLBuild 4337.35; Windows NT 5.1; .NET CLR 1.1.4322; .NET CLR 2.0.50727)",
    "Mozilla/5.0 (Windows; U; MSIE 9.0; Windows NT 9.0; en-US)",
    "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 2.0.50727; Media Center PC 6.0)",
    "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 1.0.3705; .NET CLR 1.1.4322)",
    "Mozilla/4.0 (compatible; MSIE 7.0b; Windows NT 5.2; .NET CLR 1.1.4322; .NET CLR 2.0.50727; InfoPath.2; .NET CLR 3.0.04506.30)",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN) AppleWebKit/523.15 (KHTML, like Gecko, Safari/419.3) Arora/0.3 (Change: 287 c9dfb30)",
    "Mozilla/5.0 (X11; U; Linux; en-US) AppleWebKit/527+ (KHTML, like Gecko, Safari/419.3) Arora/0.6",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.2pre) Gecko/20070215 K-Ninja/2.1.1",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN; rv:1.9) Gecko/20080705 Firefox/3.0 Kapiko/3.0",
    "Mozilla/5.0 (X11; Linux i686; U;) Gecko/20070322 Kazehakase/0.4.5",
    "Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.8) Gecko Fedora/1.9.0.8-1.fc10 Kazehakase/0.5.6",
    "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.56 Safari/535.11",
    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_3) AppleWebKit/535.20 (KHTML, like Gecko) Chrome/19.0.1036.7 Safari/535.20",
    "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; fr) Presto/2.9.168 Version/11.52",
]
header = {'Connection': 'keep-alive',
          'Cache-Control': 'max-age=0',
          'Upgrade-Insecure-Requests': '1',
          'User-Agent': _agent[random.randint(0, len(_agent) - 1)],
          'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
          'Accept-Encoding': 'gzip, deflate, sdch',
          'Accept-Language': 'zh-CN,zh;q=0.8',
          }
proxies = []

url = "http://www.xicidaili.com/nt/"


def get_ip():
    htmls = requests.get(url, headers=header, proxies=proxies).content
    tree = etree.HTML(htmls)

    for i in tree.xpath('//tr')[1:]:
        ps = {}
        data = i.xpath('//td/text()')
        ps[data[5]] = data[5] + '://' + ':'.join(data[0:2])
        proxies.append(ps)
    return proxies
get_ip()
print(proxies)
def saveHtml(file_name,file_content):
#    注意windows文件命名的禁用符，比如 /
    with open (file_name.replace('/','_')+".html","wb") as f:
#   写文件用bytes而不是str，所以要转码
        f.write( file_content )
# 获取地址百度地址和密码
class Counter(threading.Thread):

    def __init__(self, lock, threadName, requests, url,name,id):
        '''@summary: 初始化对象。
           @param lock: 琐对象。
           @param threadName: 线程名称。
        '''
        print(threadName+'lishao-start-run')
        super(Counter, self).__init__(name=threadName)
        self.lock = lock
        self.requests = requests
        self.url = url
        self.name = name
        self.id = id

    def _data_get(self):
        try:
            html = requests.get(self.url)
            url = re.findall(r'href="(https://pan.baidu.com/s/.*?|http://pan.baidu.com/s/.*?)"', html.content.decode('utf-8'))
            password = re.findall(r'密码(：|；|: )(\w{0,4})', html.content.decode('utf-8'))
            print(password)
            print(password[0][1])
            try:
                password = password[0][1]
            except BaseException as e:
                print(e)
                password = ''
            print(url[0])
            print(password)
            print(self.name)
            # tags = re.findall(r'\[(.*?)\]',self.name)
            # # print(tags)
            # tags = ",".join([str(x).replace("[", "").replace("]","") for x in tags])
            # print(tags)

            try:
                url1 = "http://www.baidu.com/s?"
                print(url1)
                html = requests.get(url1,params={
                    'wd':"site:movie.douban.com {}".format(self.name)
                })
                print(html.content)
                select = etree.HTML(html.content)
                # saveHtml("text1", html.content)
                a = select.xpath('//h3[@class="t"]/a/@href')
                print(a)
                html = requests.get(a[0])
                select = etree.HTML(html.content)
                # print(html.content)
                ase = select.xpath('//img/@src')
                img = ase[0]
            except BaseException as e:
                print(e)
                img = ''
            requests.post('http://localhost/basic/index.php?r=bian/update', {
                'id': self.id,
                'password': password,
                'url': url[0],
                'img':img
            })
        except BaseException as e:
            requests.post('http://localhost/basic/index.php?r=bian/edit', {
                'id': self.id,
            })
            print(e)

    def run(self):
        global count
        self.lock.acquire()
        self._data_get()
        self.lock.release()


lock = threading.Lock()
i = 0


# def _data_get(url):
#     try:
#         html = requests.get(url)
#         url = re.findall(r'<a target="_blank" href="(.*?)">百度云盘</a>', html.content.decode('utf-8'))
#         password = re.findall(r'密码：(\w{0,4})', html.content.decode('utf-8'))
#         print(url)
#         print(password)
#     except BaseException as e:
#         print(e)
# _data_get('http://www.xiexingeini.com/62388.htm')

#
def worker(i,n,tags):
    rr = requests.post('http://localhost/basic/index.php?r=bian/create', {
        'name': i,
        'source_url': n,
        'tags': tags
    })
    print(rr.content)
if __name__ == '__main__':
    # Counter(lock, "thread-" + str(i), requests, 'http://www.zzeg.cn.com/43720.htm','sad','5ad83cc430a3d').start()
    i = 0
    # url = 'http://www.xiexingeini.com/fl/dy/page/1'
    # url = 'http://www.xiexingeini.com/fl/gcj/page/1'
    while True:

        try:
            html = requests.get('http://localhost/basic/index.php?r=bian/items')
            html = json.loads(html.content)
            for index in range(len(html)):
                i = i + 1
                try:
                    Counter(lock, "thread-" + str(i), requests, html[index]['source_url'], html[index]['name'],html[index]['id']).start()
                except BaseException as e:

                    if e == "can't start new thread":
                        print(e)
                        time.sleep(180)
                    else:
                        print('xxxx error')
                        requests.post('http://localhost/basic/index.php?r=bian/edit', {
                            'id': html[index]['id'],
                        })
        except BaseException as e:
                print('url error')
time.sleep(3860000)
# for index1 in range(10000):
#         index = index1+742
#         try:
#                 html = requests.get('http://www.xiexingeini.com/page/{}'.format(index))
#                 html = etree.HTML(html.content)
#                 urls = html.xpath('//header/h2[@class="entry-title"]/a/@href')
#                 titles = html.xpath('//header/h2[@class="entry-title"]/a/text()')
#                 threads = []
#                 print(urls)
#                 # Counter(lock, "thread-" + str(i), requests, data_quere.get()).start()
#                 for index in range(len(urls)):
#
#                     print(urls[index])
#                     print(titles[index])
#                     tags = re.findall(r'\[(.*?)\]', titles[index])
#                     # print(tags)
#                     tags = ",".join([str(x).replace("[", "").replace("]", "") for x in tags])
#                     print(tags)
#                     threads.append(threading.Thread(target=worker, args=(titles[index],urls[index],tags,)))
#                     i = i + 1
#                     print(i)
#                 for t in threads:
#                         t.start()
#
#                     # Counter(lock, "thread-" + str(i), requests, urls[index],titles[index]).start()
#
#
#         except BaseException as e:
#             print(e)
#             print('url error')




# ip = proxies[random.randint(0, len(proxies) - 1)]
# print(ip)
#
# img = requests.get('http://api.douban.com/v2/movie/search?q={}'.format("超人"), proxies={
#     'https':'https://14.153.53.80:3128',
# }).content
# print(img)
# img = json.loads(img)
# average = img['subjects'][0]['rating']['average']
# imgs = img['subjects'][0]['images']['small']
# year = img['subjects'][0]['year']

