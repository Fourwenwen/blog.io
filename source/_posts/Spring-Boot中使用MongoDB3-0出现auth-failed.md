---
title: Spring Boot中使用MongoDB3.0出现auth failed
date: 2017-04-18 11:06:45
tags: spring mongodb spring-boot auth-faile
---

基于最近MongoDB数据库勒索事件，导致很多小公司的因为MongoDB出现数据丢失问题。归根到底还是数据库裸奔问题，很多小企业的MongoDb是没有做任何安全措施。。。例如我们公司。。。哈哈。

所以我们公司毫无意外的中招了，被人勒索要求给1比特币，哈哈。最后我们没给，肯定不给啊，我们MongoDB里面存储的数据仅仅是一些没有状态的预生成的短连接而已，没所谓。我们重新生一批就好。

这块业务不是我写的，结果就让我来搞掂MongoDB,防止给别人下次还清空数据库。（躺着都中枪。。。）

## 预防手段

其实这些真的没什么好说的，来来去去就几样，只是之前搞这块业务的人懒得搞。

- **先把MongoDB数据库的账户密码设置好了。**
- **然后禁止公网访问。防火墙走起来。**
- **如果是一定要公网的，就通过ip白名单。**
- **常做数据备份是没错的。**

-------------------

## 正题

我感觉我前面的废话有点多了，前面并不是今天我们要讨论的。而是我在进行上面的这些工作的时候，出现了一个问题，就是MongoDB我创建完了用户之后，然后在项目中怎样都报"auth failed"错误，我就火了，我在客户端各种姿势都可以验证，怎么用在项目中就不行了。

吓得我立马去百度了一波，没有什么事情是百度解决不了，然后又那就谷歌。

在MonGoDB官网3.0中的发行说明中有一条这些写着：
https://docs.mongodb.com/v3.0/release-notes/3.0-scram/
![这里写图片描述](http://img.blog.csdn.net/20170418110517313?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDE1MDQ2Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
就是3.0版本的创建用户将默认使用SCRAM-SHA1认证机制。

然后查看我们项目中mongo-java-driver-2.12.5.jar中的MongoCredential类中：
![这里写图片描述](http://img.blog.csdn.net/20170418174729207?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDE1MDQ2Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
该版本的jar包中是没有SCRAM-SHA1认证方式的。

##解决办法

 请尽快升级的jar包的版本。。。

```
 <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <!--<version>1.2.4.RELEASE</version>-->
        <version>1.3.6.RELEASE</version>
    </parent>
```
因为我用的是spring boot所以直接把整个版本都升了。

有些人觉得就mongoDB的问题，把整一个spring boot升级有点小题大做，确实，升级可能会导致已有的代码出现不兼容问题。所以归根到底就是mongo-java-driver.jar的问题，单独把这个jar升级也是可以的，不过要留意下spring版本是否兼容。

## 结语
其实就是升级jar的问题，我的废话好像有点多了，我只是想找到问题原因，然后才是解决方案。如有错漏请各位大佬们原谅，并指正过来，万分感谢。



---------
