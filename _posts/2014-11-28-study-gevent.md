---
layout: post
title: "学习gevent之基本用法"
description: "学习gevent的用法，并实现一个异步查询电话号码归属地"
category: python
tags: [python, gevent, concurrent, urllib2]
---
{% include JB/setup %}

花了点时间学习gevent, 目前只了解其基本用法，但还不清楚实现原理。

## 一些学习资源：

* gevent site [http://gevent.org/](http://gevent.org/)
* gevent in GitHub [https://github.com/gevent/gevent](https://github.com/gevent/gevent)
* Gevent Tutorial [http://sdiehl.github.io/gevent-tutorial/](http://sdiehl.github.io/gevent-tutorial/)
* Gevent Tutorial 中文翻译 [http://xlambda.com/gevent-tutorial/](http://xlambda.com/gevent-tutorial/)

## 简单用法

下面的代码来自[Gevent Tutorial](http://sdiehl.github.io/gevent-tutorial/). 首先定义两函数`foo()`和`bar()`来实现具体的操作，再由`gevent.spawn()`创建`Greenlet`对象来执行`foo()`/`bar()`，最后`gevent.joinall()`来等待两`greenlet`执行结束。`foo()`/`bar()`中调用`gevent.sleep(0)`来显式切换到不同的`Greenlet`。

{% highlight python %}
import gevent

def foo():
    print('Running in foo')
    gevent.sleep(0)
    print('Explicit context switch to foo again')

def bar():
    print('Explicit context to bar')
    gevent.sleep(0)
    print('Implicit context switch back to bar')

gevent.joinall([
    gevent.spawn(foo),
    gevent.spawn(bar),
])
{% endhighlight %}

要点：

* 使用`gevent.spawn()`或[直接创建`Greenlet`](http://gevent.org/intro.html#lightweight-pseudothreads)来新建执行单元
* `join()`函数来等待所有执行单元结束
* 执行的函数里在需要等待时能切换到其它，比如`gevent.sleep(0)`， 或是网络等待事件
* [Monkey patching](http://gevent.org/intro.html#monkey-patching)打补丁来支持gevent, 如：

{% highlight python %}
>>> from gevent import monkey; monkey.patch_socket()
>>> import urllib2 # it is usable from multiple greenlets now
{% endhighlight %}

* 提供一些数据结构来支持Greenlet之间的通信，如`gevent.queue.Queue`

## 使用gevent来实现并行查询数据

作为练习，实现的功能是手机号码归属地查询。代码仅为学习交流之用，故隐藏了查询所使用的url, 切勿用于其它目的，且本人不对使用产生的任何后果负责。

* `tasks = Queue()`用于存放phone number, `leadWorker()`生成号码放在`tasks`中，`queryWorker()`从`tasks`中取出一个号码查询
* 查询的结果放入`resultQue = Queue()`，在所有查询结束后写入文件
* 1个`leadWorker()`和10个`queryWorker()`同时工作

{% highlight python %}
#!/usr/bin/env python
# -*- coding:utf-8 -*-

import gevent;
from gevent import monkey;  monkey.patch_socket(); monkey.patch_ssl()
from gevent.queue import Queue
import urllib2
import json
import random

REQ_URL = 'https://....xxx.com/callback?...&phone={0}&...'

tasks = Queue()
resultQue = Queue()

completed = False

def queryWorker(n):
    while not tasks.empty() or not completed:
        phn = tasks.get()
        print n, ' - ', phn
        req = urllib2.Request(REQ_URL.format(phn), headers = {'User-Agent':'Mozilla/5.0 (Windows NT 6.1; WOW64) Chrome/39.0.2171.71'})
        resp = urllib2.urlopen(req)
        resp_content = resp.read()
        if len(resp_content) > 0:
	    i = resp_content.find('(')
	    if i > 0:
	        j = resp_content.rfind(')')
		jsonData = json.loads(resp_content[i+1:j])
                if jsonData['data']:
		    line = '{0}|{1}|{2}|{3}'.format(phn, jsonData[u'data'][u'operator'].encode('utf-8'), jsonData[u'data'][u'area'].encode('utf-8'), jsonData[u'data'][u'area_operator'].encode('utf-8'))
                    resultQue.put(line)
        gevent.sleep(0.5)
    print n, ' Completed'

def leadWorker():
    global completed
    n = 0
    for i in xrange(10):
        for j in xrange(10000):
            t = ''.join([str(random.randint(0,9)) for _ in range(4)])
            phn = '13%d%04d%s'%(i, j, t)
            tasks.put(phn)
            n += 1
            if n == 100:
                gevent.sleep(30)
            n = 0
    completed = True
    print 'Lead Worker Completed'

workers = [gevent.spawn(queryWorker, i) for i in range(10)]
workers.append(gevent.spawn(leadWorker))
gevent.joinall(workers)

fd = open('phn_area.txt', 'w')
for line in resultQue:
    fd.write(line)
    fd.write('\n')
fd.close()
{% endhighlight %}
