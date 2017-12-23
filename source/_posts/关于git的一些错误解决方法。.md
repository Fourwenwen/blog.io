---
title: 关于git的一些错误解决方法。
date: 2015-03-22 14:18:38
tags: git
---

## 当要push代码到git时，出现提示：master:master [rejected -non-fast-forward]
问题（Non-fast-forward）的出现原因在于：git仓库中已经有一部分代码，所以它不允许你直接把你的代码覆盖上去。于是你有2个选择方式：

1，强推，即利用强覆盖方式用你本地的代码替代git仓库内的内容

git push -f

2，先把git的东西fetch到你本地然后merge后再push

$ git fetch

$ git merge

在eclipse中的操作与上面原理一样：

1，右击项目，Team-remoto-retch form；

2，然后在执行Team-merge。


## git提交失败的解决办法，提交后提示：fatal: recursion detected in die handler

问题原因：
问题原因是http.postBuffer默认上限为1M所致。在git的配置里将http.postBuffer变量改大一些即可，比如将上限设为500M：
git config --global http.postBuffer 524288000

在哪里执行以上命令呢？
打开git bash命令行工具。
注意要加上--global。网上很多资料都没加这个参数。不加执行的话会报以下错误的：
error:could not lock config file .git/config: no such file or directory.

## eclipse安装了最新版本的egit，显示完成，但是eclipse没有出现相对应的功能。

这个问题浪费我很长的时间，个人认为是兼容性的问题。最后我还是安装了低版本的egit。

## 用 eclipse 向开源中国的 git push 时提示 “git: insufficient data written” 怎么解决？

出现这个问题是我用eclipse自带的git插件，出现的。
最后我解决的方式是，重新安装了一个egit插件。并成功上传到git。
