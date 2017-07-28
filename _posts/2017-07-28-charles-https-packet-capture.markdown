---
layout:     post
title:      "使用Charles对Https请求进行抓包"
date:       2017-07-28 10:45:00
author:     "yycoder"
header-img: "img/post-bg-unix-linux.jpg"
header-mask: 0.3
catalog:    true
tags:
    - charles
    - https
    - 抓包
    - iOS
---

>由于近期公司的API接口升级成https协议，有时候想查看一下线上环境的返回结果，
按照Charles抓取http协议数据的方式是不能正确的看到https协议请求和返回结果的，
那Charles能抓取https数据吗？  
答案是：可以的！  
接下来我们看下如何操作：

# PC安装Charles SSL根证书

选择`Install Charles Root Certificate`菜单，Charles工具栏路径如下：

    Help -> SSL Proxying -> Install Charles Root Certificate
   
![](/img/in-post/post-charles-https/charles-https-pc-ssl.jpg)
# 设置Charles http代理设置
选择`Proxy Setting`菜单，Charles工具栏路径如下：

    Proxy -> Proxy Setting
    
![](/img/in-post/post-charles-https/charles-http-setting.jpg)

勾选上`Install Charles Root Certificate`,一般使用默认端口`8888`,然后点击`ok`
![](/img/in-post/post-charles-https/charles-http-setting-info.jpg)

# iPhone手机设置http代理
前提是手机和电脑在一个局域网，打开手机`设置` -> `无线局域网`，点击网络详情，
`HTTP代理`选`手动`,并设置服务器为电脑IP，端口设置成Charles http代理设置的端口`8888`,即可
![](/img/in-post/post-charles-https/charles-http-iPhone-set.jpg)

# iPhone手机安装Charles SSL根证书
Charles选择`Install Charles Root Certificate on a Mobile Device or Remote Browser`,Charles工具栏路径如下：

    Help -> SSL Proxying -> Install Charles Root Certificate on a Mobile Device or Remote Browser
    
![](/img/in-post/post-charles-https/charles-https-ssl-pc-alert.jpg)  
![](/img/in-post/post-charles-https/charles-https-ssl-alert.jpg) 
   
然后打开手机的浏览器访问`chl.pro/ssl`,按照提示安装证书   

# Charles SSL Proxying 设置
选择Charles`Proxy` -> `SSL Proxying Setting `,点击`Add`,Host和Port都填写`*`,
抓取任意站点、任意端口的数据，勾选`Enable SSL Proxying`
![](/img/in-post/post-charles-https/charles-https-pc-ssl-proxy.jpg) 

# 特别要注意的事项
不知道从什么时候开始 iOS多了个信任的操作 原来这块貌似是默认是信任吧，如果不勾选，将会出现`unknown`的错误
在`设置` -> `通用` -> `关于本机` -> `证书信任设置`