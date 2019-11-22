---
title: 五分钟学会用Charles抓包
date: 2019-06-13 11:41:10
tags:
- 移动端
- 抓包
- 混合开发
categories: 前端
---
### Charles介绍
Charles可能很多人不熟悉，但是另外一个windows下的Fiddler很多人应该不陌生的；它们都是同性质的代理抓包工具；

正常情况下，Chrome DevTool已经满足了日常web开发的需求，但是有的特性：编辑request的参数、重定向request请求的资源、编辑response的数据，ChromeDevTool就很蛋疼了，而且查看和调试移动端资源时候Chrome也并不好用；

#### 我常借用Charles做这些事情 ：

+ 抓取 Http 和 Https 的请求和响应，抓包是最常用的了。
+ 重发网络请求，方便后端调试，复杂和特殊情况下的一件重发还是非常爽的（捕获的记录，直接repeat就可以了，如果想修改还可以修改）。
+ 修改网络请求参数（客户端向服务器发送的时候，可以修改后再转发出去）。
网络请求的截获和动态修改。
+ 支持模拟慢速网络，主要是模仿手机上的2G/3G/4G的访问流程。
+ 支持本地映射和远程映射，比如你可以把线上资源映射到本地某个文件夹下，这样可以方便的处理一些特殊情况下的bug和线上调试（网络的css，js等资源用的是本地代码，这些你可以本地随便修改，数据之类的都是线上的环境，方面在线调试）；
+ 可以抓手机端访问的资源（如果是配置HOST的环境，手机可以借用host配置进入测试环境）
#### 安装
+ [下载](https://www.charlesproxy.com/download/)
+ 安装后长这样
>![image.png](https://upload-images.jianshu.io/upload_images/4539636-180696d9002e8d67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 配置
+ 手机和电脑连上同一个局域网
+ 打开Charles客户端，点击Proxy->Proxy Settings菜单，设置默认端口8888
>![image.png](https://upload-images.jianshu.io/upload_images/4539636-f6d1bfc18138748e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
+  打开Charles客户端，点击Help->Local Ip Address，查看本机IP
>![image.png](https://upload-images.jianshu.io/upload_images/4539636-2f53aed0bec5c1eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+ 打开手机局域网设置，点击代理，设置为你本机IP和上面默认的8888端口
>![这里的服务器地址应该是192.168.20.111,图片上的写错了](https://upload-images.jianshu.io/upload_images/4539636-42f4fc8dec2116af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+ 打开Charles客户端，点击Poxy->macOs Proxy
+ 等待一会，Charles会弹出是否允许，点击allow
>![image.png](https://upload-images.jianshu.io/upload_images/4539636-792cc727406daa23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

+ Charles就会抓到你手机上的请求地址
>![image.png](https://upload-images.jianshu.io/upload_images/4539636-1adae9720654edb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### https请求是unknow问题
抓到包后会发现有些请求是unknow，看不到具体的请求，原因是你的手机没有安装ssl证书、并且电脑需要配置代理网址
>![image.png](https://upload-images.jianshu.io/upload_images/4539636-dfc9a57d312a7be3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
+ 解决方法：
  1、打开Charles, 点击Help->SSL Proxying->Install Charles Root Certificate，安装后去钥匙串中允许Charles的证书始终信任
>![image.png](https://upload-images.jianshu.io/upload_images/4539636-ccd631cb9b81eb52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  2、 手机浏览器输入[chls.pro/ssl](http://chls.pro/ssl)安装证书（通用->描述文文件与设备管理->选中Charles证书安装），安装好之后要设置信任该证书（通用->关于本机->设置信任该证书）,不然抓包仍然会显示unknown类型。

  3、在Charles中，设置Proxy->SSL Proxying Settings中的SSL Proxying的代理  网址。直接点击Add->OK即可，不用输入host和port，就可以默认代理所有请求
>![image.png](https://upload-images.jianshu.io/upload_images/4539636-6a8eaa512c58ba46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




