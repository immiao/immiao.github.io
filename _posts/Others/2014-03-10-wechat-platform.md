---
layout: post
comments: true
categories: Others
---

> WeChat (Chinese: 微信; pinyin: About this sound Wēixìn; literally: "micro message") is a mobile text and voice messaging communication service developed by Tencent in China, first released in January 2011. It is one of the largest standalone messaging apps by monthly active users. To benefit teachers and students in my college, our team set up a Wechat platform, which supports querying personal information, querying curriculum, querying grade, introducing communities, introducing activities and etc.


## Auto Responder

> Auto Responder is one of the modules in our project and I was responsible for its development and maintenance.

I set up the server for our project on Sina Cloud. The principle is as following:

* get the user input from our Wechat platform
* send POST request to [http://www.xiaohuangji.com](http://www.xiaohuangji.com), which is a auto-response online robot
* parse XML returned by the website
* show the response to the user

Some core codes on our Sina Cloud server are shown as below :

{% highlight python%}
# -*- coding: utf-8 -*-
import hashlib
import web
import lxml
import time
import os
import urllib2,json
from lxml import etree
import urllib

class WeixinInterface:

    def __init__(self):
        self.app_root = os.path.dirname(__file__)
        self.templates_root = os.path.join(self.app_root, 'templates')
        self.render = web.template.render(self.templates_root)

    def GET(self):
        #get input parameter
        data = web.input()
        signature=data.signature
        timestamp=data.timestamp
        nonce=data.nonce
        echostr=data.echostr
        #your own token
        token="immiao"
        #sort by lexicographic order
        list=[token,timestamp,nonce]
        list.sort()
        sha1=hashlib.sha1()
        map(sha1.update,list)
        hashcode=sha1.hexdigest()
        #sha1 encryption algorithm

        #if the request is from Wechat，response echostr
        if hashcode == signature:
            return echostr
        
    def POST(self):
        str_xml = web.data() #get data from POST
        xml = etree.fromstring(str_xml)#parse XML
        content=xml.find("Content").text#get the input from the users
        msgType=xml.find("MsgType").text
        fromUser=xml.find("FromUserName").text
        toUser=xml.find("ToUserName").text

        url = 'http://www.xiaohuangji.com/ajax.php'
        content = content.encode('utf-8')
        values = {'para':content}
        data = urllib.urlencode(values)
        #print data
        req = urllib2.Request(url, data)
        response = urllib2.urlopen(req)
        the_page = response.read()
        #print the_page

        if the_page!='':
            return self.render.reply_text(fromUser,toUser,int(time.time()),the_page)
        else:
            return self.render.reply_text(fromUser,toUser,int(time.time()),u'Can not recognize your input')
{% endhighlight %}

## Exhibition

![exhibition](./exhibition.png)