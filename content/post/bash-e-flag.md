+++
author = ""
categories = ["bash"]
date = "2016-08-01T22:56:17+08:00"
description = ""
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
title = "一场由BASH的-E参数引发的血案"

+++

最近有一个CI会偶发性的失败，而且频率还挺高，天天看着血色的CI状态面板，真是够够的了。最害怕偶发性的问题了，次次都失败比这个还更能让我更容易接受一些。但不管怎么样，CI总得修啊。和当时写脚本的哥们研究了一下，一致认为问题应该是由nodejs的包没有成功安装导致的。这个脚本是当做docker容器的entrypoint跑的，奇怪的地方在于即使我们在后面有判断如果未能成功安装会再次尝试安装的代码，但是根本不会执行，第一次没有安装成功的话容器直接就退出了。因为每次出问题都是sass没有安装成功退出的，所以心底里一直怀疑是这个包那里有问题，导致了容器直接崩溃退出了。问了周边一些用nodejs的人，都或多或少的遇到安装sass失败的情况，但对于如何提高安装成功率除了重复安装都没有什么好的解决办法。

既然都没有更好的办法，加上原来的代码在第一次失败之后只有一次尝试安装，虽然现实情况来看从来就没有跑到过那里就退出了，但是总是感觉这样不好看，就把这部分重新写了一下，搞了一个不死不休的循环，大概类似如下：
``` bash
cd /dev/null
until [[ $? -eq 0 ]]; do
rm -rf node_modules && npm cache clean && npm install
done
```
在until之前故意写了的那个cd是为了得到一个非0的返回码确保能进入循环。

把这个跑了一下，发现容器直接在`cd /dev/null`那里失败之后就退出了，难道bash的任何错误就会直接让容器终止？没有听说过这种事情啊，遍寻各种资料也都没有发现docker有这样的问题和特性。难道是docker镜像的问题？重写了一个简单测试的脚本当做entrypoint开一个容器跑了一下，发现错误之后的代码是能执行下去，并没有让容器退出。那就不是容器的问题，是脚本的问题了，抬头看了一下CI里面的脚本，发现原来脚本的shebang（就是第一行最开始的#!，如有兴趣了解更多请[猛击这里](https://en.wikipedia.org/wiki/Shebang_(Unix))）那里给bash的解释器加了一个`-e`的参数，男人了一下bash，对这个`-e`的解释如下：
``` text
-e  Exit  immediately  if  a  pipeline  (which may consist of a single simple
    command),  a subshell command enclosed in parentheses, or one of the commands executed as
    part of a command list enclosed by braces (see SHELL GRAMMAR above) exits  with a nonzero
    status.  The shell does not exit if the command that fails is part of the command list
    immediately following a while or until keyword, part of the test following the if or elif
    reserved words, part  of  any command  executed in a && or || list except the command
    following the final && or ||, any command in a pipeline but the last, or if the command's
    return value is being inverted with !.  A trap on ERR, if set, is executed before the
    shell  exits.   This  option applies to the shell environment and each subshell
    environment separately (see COMMAND EXECUTION ENVIRONMENT above), and may cause subshells
    to exit before executing all the commands in the subshell.
```

原来问题在这里，以前还从来没有注意过`-e`这个神奇的东西，今天又涨了一下姿势，以后看谁不顺眼，就偷偷的给他的解释器加个`-e`去，嘿嘿。

终于我们的容器不用再一报错就退出了，CI也能正常的跑下去了，虽然可能nodejs会碰到重复安装的问题，但是只是一个时间问题而已了。
