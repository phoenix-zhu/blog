+++

author = ""

categories = ["devops","automation"]

date = "2017-05-29T14:07:21+08:00"

chronologies = ["2017"]

description = ""

featured = ""

featuredalt = ""

featuredpath = ""

linktitle = ""

title = "关于Deployment，Provisioning和Orchestration的区别"

+++

这几个词并不是什么新鲜词，但我们在读英文文档的时候经常会碰到，它们之间的区别是什么其实也是一直困扰我的一个问题，尤其是 ***Deployment*** 和 ***Provisioning*** ，我曾经问过一个英语是母语的外国友人这两个之间的区别，他的解释是 ***Provisioning*** 是 ***Deployment*** 的一部分，不过今天看到一篇Blog([What's Deployment Versus Provisioning Versus Orchestration?](http://codefol.io/posts/deployment-versus-provisioning-versus-orchestration))详细的解释了三者之间的区别，就顺便转过来帮助大家理解一下三者的定义。



 ***Deployment*** 是指把一个全新的或者新版本的应用(Application)安装/更新到一台已经配置好的服务器上。开发人员喜欢使用这个词，而且他们会把前期的配置服务器环境以及安装应用所需的依赖包的过程也认为是 **deploy** 的环节，也就是说 ***Deployment*** 包含了 ***Provisioning***。 ***Deployment*** 的时候多使用 [Capistrano](http://capistranorb.com/) or [Mina](http://nadarei.co/mina/)。运维人员会用 ***Provisioning*** 这个词来表示前期业务环境的准备过程，比如购买服务或者虚拟机，设备的网络接入，系统安装和相关的环境配置等。对于运维人员来讲，***Deployment*** 就是前面狭义上定义的应用安装的过程而已，并不包含 ***Provisioning***。***Provisioning*** 的时候多使用 [Chef](http://getchef.io/), [Ansible](http://ansible.com/) or [Puppet](http://puppetlabs.com/)。



这么看来，***Deployment*** 和 ***Provisioning*** 是母语系国家开发和运维人员因为角色不同对于 **基础环境配置** 和 **部署** 概念大小理解上的细微差异而偏好使用的两个词。刚好我问的那位外国友人刚好是一个开发人员，所以也不难理解他为什么解释说 ***Provisioning*** 是 ***Deployment*** 的一个部分。话说作为一个苦逼的非母语系的运维人员，我个人有限的英语词库里面对于 **部署** 的理解就是 ***Deployment***，而 **基础环境配置** 这种事情可能会用 ***Environment setup*** 这种直接的表述，这也是我为什么在看到 ***Provisioning*** 这个词的时候迷惑的地方了。



最后来说说 ***Orchestration*** 这个词，这个词源于交响乐队的乐曲编排，在IT行业中，被用来抽象的描述指以一种规律性运行或者可以自制的东西，更接地气的解释是大规模系统的部署和控制方式。我个人更多的理解为**根据系统的特性按照既定的策略安装配置所需的环境**。这个词我们看到的基本上都是把它译为 ***编排*** 了，也算是比较表意的了。相对而言，不同角色在这个词理解上的差异没有那么大。文章中举了一些例子来帮助理解这个词，顺便摘过来：

* 在所有的数据库服务器上运行最新的数据迁移脚本，但不包含应用服务器
* 在应用服务器上检查当前的软件版本并安装所需的补丁，最后汇报结果
* 在所有的服务器上使用`ps`命令检查并确认`Daemontools`进程在运行
* 干掉所有的`Node.js`进程

通常来讲，上面所提及的那些用来做用来做 ***Deployment*** 和 ***Provisioning*** 的都可以算是 ***Orchestration*** 工具，另外还有一些常用的 ***Orchestration*** 工具，比如[Fabric](http://www.fabfile.org/), [MCollective](https://puppetlabs.com/mcollective), [Salt](http://saltstack.com/)等也都是可以用来做 ***Deployment*** 和 ***Provisioning*** 的。



文章最后提及了一个词***Configuration Management***，这个是 ***Provisioning*** 的一部分，但通常来讲，我们说 ***Provisioning*** 是指第一次这个过程，而***Configuration Management*** 是重复性的一个检查并使之达到一个收敛的状态。
