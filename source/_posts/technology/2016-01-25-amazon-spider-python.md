---
layout: post
title: 爬虫防屏蔽
category: 技术
tags: python,spider
keywords: spider
description: spider amazon
---

##介绍

最近爬了amazon,发现amazon服务器,连续访问100多次后爬虫就会经常失败了,

查找了下失败的原因,发现amazon会检测Ip一旦发现请求次数过多,就会跳转到输入验证码的网页

需要输入正确验证码才可继续愉快的访问,搞掉验证码是个麻烦活,执意研究验证码就跨到图像分析那个学科了(不过据说某些库识别率还行)。。

Gg整理正常的防屏蔽方法,总结一下比较好的解决方案.

#### ADSL重启拨号

大家都知道adsl重拨号的话,会换一个新的ip地址,那就可以写脚本设置时间重拨adsl,或者先用爬虫爬着,发现开始跳转到验证码页面了,再调用重拨adsl的脚本

#### 爬代理服务器地址

很机智的解决办法，代理服务器可以很好的解决ip被屏蔽的问题

不过代理服务器的网站域名经常更换,我就不提供了,大家自行Gg吧,百度说不定也会有惊喜

要注意的是爬下来的代理服务器,最好检测一下是否可用!

参照下面篇博客的checkProxy()函数

https://blog.linuxeye.com/410.html

```
#!/usr/bin/env python
#coding:utf-8
#BLOG: blog.linuxeye.com
import urllib2
import re
import threading
import time
import MySQLdb
rawProxyList = []
checkedProxyList = []
#抓取代理网站
targets = []
for i in xrange(1,42):
        target = r"http://www.proxy.com.ru/list_%d.html" % i
        targets.append(target)
#抓取代理服务器正则
p = re.compile(r'''<tr><b><td>(\d+)</td><td>(.+?)</td><td>(\d+)</td><td>(.+?)</td><td>(.+?)</td></b></tr>''')
#获取代理的类
class ProxyGet(threading.Thread):
    def __init__(self,target):
        threading.Thread.__init__(self)
        self.target = target
    def getProxy(self):
        print "代理服务器目标网站： " + self.target
        req = urllib2.urlopen(self.target)
        result = req.read()
        #print chardet.detect(result)
        matchs = p.findall(result)
#       print matchs
        for row in matchs:
            ip=row[1]
            port =row[2]
            addr = row[4].decode("cp936").encode("utf-8")
            proxy = [ip,port,addr]
            print proxy
            rawProxyList.append(proxy)
    def run(self):
        self.getProxy()
#检验代理的类
class ProxyCheck(threading.Thread):
    def __init__(self,proxyList):
        threading.Thread.__init__(self)
        self.proxyList = proxyList
        self.timeout = 5
        self.testUrl = "http://www.baidu.com/"
        self.testStr = "030173"
    def checkProxy(self):
        cookies = urllib2.HTTPCookieProcessor()
        for proxy in self.proxyList:
            proxyHandler = urllib2.ProxyHandler({"http" : r'http://%s:%s' %(proxy[0],proxy[1])})
            #print r'http://%s:%s' %(proxy[0],proxy[1])
            opener = urllib2.build_opener(cookies,proxyHandler)
            opener.addheaders = [('User-agent', 'Mozilla/5.0 (Windows NT 6.2; WOW64; rv:22.0) Gecko/20100101 Firefox/22.0')]
            #urllib2.install_opener(opener)
            t1 = time.time()
            try:
                #req = urllib2.urlopen("http://www.baidu.com", timeout=self.timeout)
                req = opener.open(self.testUrl, timeout=self.timeout)
                #print "urlopen is ok...."
                result = req.read()
                #print "read html...."
                timeused = time.time() - t1
                pos = result.find(self.testStr)
                #print "pos is %s" %pos
                if pos > 1:
                    checkedProxyList.append((proxy[0],proxy[1],proxy[2],timeused))
                    #print "ok ip: %s %s %s %s" %(proxy[0],proxy[1],proxy[2],timeused)
                else:
                     continue
            except Exception,e:
                #print e.message
                continue
    def run(self):
        self.checkProxy()
if __name__ == "__main__":
    getThreads = []
    checkThreads = []
#对每个目标网站开启一个线程负责抓取代理
for i in range(len(targets)):
    t = ProxyGet(targets[i])
    getThreads.append(t)
for i in range(len(getThreads)):
    getThreads[i].start()
for i in range(len(getThreads)):
    getThreads[i].join()
print '.'*10+"总共抓取了%s个代理" %len(rawProxyList) +'.'*10
#开启20个线程负责校验，将抓取到的代理分成20份，每个线程校验一份
for i in range(20):
    t = ProxyCheck(rawProxyList[((len(rawProxyList)+19)/20) * i:((len(rawProxyList)+19)/20) * (i+1)])
    checkThreads.append(t)
for i in range(len(checkThreads)):
    checkThreads[i].start()
for i in range(len(checkThreads)):
    checkThreads[i].join()
print '.'*10+"总共有%s个代理通过校验" %len(checkedProxyList) +'.'*10
#插入数据库，表结构自己创建，四个字段ip,port,speed,address
def db_insert(insert_list):
    try:
        conn = MySQLdb.connect(host="localhost", user="root", passwd="admin",db="m_common",charset='utf8')
        cursor = conn.cursor()
        cursor.execute('delete from proxy')
        cursor.execute('alter table proxy AUTO_INCREMENT=1')
        cursor.executemany("INSERT INTO proxy(ip,port,speed,address) VALUES (%s,%s,%s,%s)",insert_list)
        conn.commit()
        cursor.close()
        conn.close()
    except MySQLdb.Error,e:
        print "Mysql Error %d: %s" % (e.args[0], e.args[1])
#代理排序持久化
proxy_ok = []
f= open("proxy_list.txt",'w+')
for proxy in sorted(checkedProxyList,cmp=lambda x,y:cmp(x[3],y[3])):
    if proxy[3] < 8:
        #print "checked proxy is: %s:%s\t%s\t%s" %(proxy[0],proxy[1],proxy[2],proxy[3])
        proxy_ok.append((proxy[0],proxy[1],proxy[3],proxy[2]))
        f.write("%s:%s\t%s\t%s\n"%(proxy[0],proxy[1],proxy[2],proxy[3]))
f.close()
db_insert(proxy_ok)

```

想了解我的amazon爬虫?

代码在github spider-comments项目下的amazon-spider-comments

https://github.com/fankcoder/spider-comments
