+++
author = ""
categories = ["tech"]
date = "2016-07-08T15:56:46+08:00"
description = ""
chronologies = ["2016"]
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
title = "解决老版本Ruby安装时的openssl问题"

+++

今天要用一个老的Ruby repo，同步下来发现用的Ruby的版本是1.9.3-p484。安装了ruby之后安装bundler提示如下错误：

```bash
/Users/home/.rbenv/versions/1.9.3-p484/lib/ruby/1.9.1/rubygems/remote_fetcher.rb:329:in `rescue in connection_for': cannot load such file -- openssl (LoadError)
```

经过一番研究，发现需要在安装的时候指定openssl的路径，在Mac上面找到openssl目录的方法如下：

```brew list openssl```

输出类似如下：

~~~bash
/usr/local/Cellar/openssl/1.0.2h_1/bin/c_rehash
/usr/local/Cellar/openssl/1.0.2h_1/bin/openssl
/usr/local/Cellar/openssl/1.0.2h_1/include/openssl/ (75 files)
/usr/local/Cellar/openssl/1.0.2h_1/lib/libcrypto.1.0.0.dylib
/usr/local/Cellar/openssl/1.0.2h_1/lib/libssl.1.0.0.dylib
/usr/local/Cellar/openssl/1.0.2h_1/lib/engines/ (12 files)
/usr/local/Cellar/openssl/1.0.2h_1/lib/pkgconfig/ (3 files)
/usr/local/Cellar/openssl/1.0.2h_1/lib/ (4 other files)
/usr/local/Cellar/openssl/1.0.2h_1/share/man/ (1588 files)
~~~

这个`/usr/local/Cellar/openssl/1.0.2h_1`就是我们要用的目录。

之后就简单了，使用下面的命令重新安装一下Ruby就好了。

```bash
RUBY_CONFIGURE_OPTS=--with-openssl-dir=/usr/local/Cellar/openssl/1.0.2h_1 rbenv install
```
