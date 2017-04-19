#!/usr/bin/env python
# -*- coding:utf-8 -*-
import urllib
import urllib2
import re
import thread
import time

class FH:

    def __init__(self):
        self.pageIndex = 1
        self.user_agent = 'Mozilla/5.0 (Windows NT 10.0; WOW64)'
        self.headers = {'User-Agent' :self.user_agent}
        self.list = []


    def getPage(self,pageIndex):
        try:
            url = 'http://app.finance.ifeng.com/list/stock.php?t=ha&f=chg_pct&o=desc&p=' + str(pageIndex)
            request = urllib2.Request(url,headers=self.headers)
            response = urllib2.urlopen(request)
            pageCode = response.read().decode('utf-8')
            return pageCode
        except urllib2.URLError,e:
            if hasattr(e,"reason"):
                print "error",e.reason
                return None

    def getPageItems(self,pageIndex):
        pageCode = self.getPage(pageIndex)
        if not pageCode:
            print "page load error"
            return None
        pattern = re.compile('<td><a href="(.*?)" target="_blank">(.*?)</a></td>.*?target="_blank">(.*?)</a></td>',re.S)

        items = re.findall(pattern,pageCode)
        pagelist = []
        for item in items:
            pagelist.append([item[0].strip(),item[1].strip(),item[2].strip()])
            print(item[0])
            print(item[1])
            print(item[2])
        return pagelist

    def loadPage(self):
        if len(self.list)<2:
            pagelist = self.getPageItems(self.pageIndex)
            if pagelist:
                self.list.append(pagelist)
                self.pageIndex +=1


    def start(self):
        print u'正在读取'
        self.loadPage()
        nowPage = 0
        pagelist = self.list[0]
        while nowPage<15:
                nowPage +=1
                del self.list[0]
                self.loadPage()


spider = FH()
spider.start()
