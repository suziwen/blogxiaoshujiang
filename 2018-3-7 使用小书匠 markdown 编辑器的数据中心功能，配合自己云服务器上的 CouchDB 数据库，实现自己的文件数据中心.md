---
title: 2018-3-7 使用小书匠 markdown 编辑器的数据中心功能，配合自己云服务器上的 CouchDB 数据库，实现自己的文件数据中心
tags: 教程,数据中心,CouchDB,小书匠
grammar_cjkRuby: true
---

听说最近开 QQ 飞车的人很多，但有了车，又不知道做什么好，好像只能吃灰。。。。或者安慰下自己，当做拿来学习各种服务器配置，但学习总不能一学就好几年吧，大学各种课程加起来也就四年，还不包括寒暑假，学个两三个月也就差不多了，剩下的时间感觉还是得吃灰。


今天不谈风花雪月，什么什么小书匠 markdown 编辑器如何如何沉鱼落雁(貌似无盐，难看得要死)，markdown 语法怎么怎么优雅大方，行云流水(矫柔造作，各种不兼容，难记得要命)，就说说一个比较阳春白雪(下里巴人)的功能，如何自定义数据中心，实现自己的文件数据库。让自己的机子不会那吗快进入吃灰状态，确保自己的投入能够最大化的得到回报。

当然搭建 CouchDB 数据库不是只能在 QQ 飞车上进行，其他各种车比如阿里车，亚马逊车，其他各种云服务器车也是可以的，甚至连树莓派车一样可以照开不误。拿着国际驾照的，就在国外开，拿着国内驾照的，就在国内开。拿着 C 驾照的，就拉着自己家人开，拿着 A 驾照的，就开着大卡车拉着一群人。。。。。。像咱这种只有两个轮子的脚踏车，一样天涯海角的转-_-!!


# 好处

搭建自己的数据中心，避免数据被各种绑架。

# 要求

## 操作系统

CouchDB 支持目前所有的主流操作系统。比如 Fedora, CentOS, Ubuntu, Windows, MacOS等等，
这里只讲解 CentOS 下的安装，其他操作系统安装可以参考 CouchDB 官方的[安装手册][1]。

CentOS 7.3 64位，使用的是各种云官方提供的镜像。

## CouchDB

安装的是 2.1.1 版本。

## 硬件信息

CPU	1核
内存	2GB(其实512M内存就够了，本人就在另一台 512M内存的机子下一直使用着)
公网带宽	1Mbps

## 其他

ssh 客户端，用来远程登录你的服务器。Windows 用户可以使用 putty 或者 xshell 等工具，Linux 系统可以直接使用 ssh 命令 `ssh 用户名@服务器地址`

# 操作步骤


## CouchDB 安装配置步骤

通过 ssh 客户端登录后，进行如下操作（为了讲解的方便，下面的操作默认使用 root 进行，如果你是非 root 用户，需要自己进行 sudo 操作）

### 步骤

1.   添加 CouchDB 官方源。创建文件 `/etc/yum.repos.d/bintray-apache-couchdb-rpm.repo`, 并粘贴如下内容

```
[bintray--apache-couchdb-rpm]
name=bintray--apache-couchdb-rpm
baseurl=http://apache.bintray.com/couchdb-rpm/el$releasever/$basearch/
gpgcheck=0
repo_gpgcheck=0
enabled=1
```

2. 安装  `yum-plugin-fastestmirror`， `epel-release` 和 `couchdb`。其中 `yum-plugin-fastestmirror` 用来提高安装文件的下载速度，可以不安装。分别执行如下命令。
	```
	yum -y install yum-plugin-fastestmirror
	yum -y install epel-release
	yum -y install couchdb
	```
   在安装 couchdb 时，由于需要连接到 CouchDB 官网去下载对应的文件， 可能需要等待比较长的时间。

3. 修改配置文件 `/opt/couchdb/etc/local.ini`。
    1. 在 `[chttpd]` 节点里， 把 `;bind_address = 127.0.0.1` 修改成 `bind_address = 0.0.0.0`。
    2. 在 `[chttpd]` 节点位置，也就是刚才 `bind_address` 下面添加一行 `require_valid_user = true`.
    3. 在 `[admins]` 节点里，将 `;admin = mysecretpassword` 修改为 `admin = xiaoshujiang` , 其中 `admin` 为**用户名**, `xiaoshujiang`为**密码**，可以根据自己需要设定
    4. 在 `[couch_httpd_auth]` 节点里，将 `; require_valid_user = false` 修改为  `require_valid_user = true`。 
    5. 在 `[httpd]` 节点下，添加一行 `enable_cors = true`.
    6. 在文件的结尾添加下面的内容
		```
		[cors]
		origins = *
		credentials = true
		headers = accept, authorization, content-type, origin, referer
		methods = GET, PUT, POST, HEAD, DELETE
		```
	7. 这个是修改前的[配置文件][2]， 这个是修改后的[配置文件][3]，用户可以对比这两个文件修改的地方
4.  修改完配置文件后，通过命令行执行 `service couchdb start` 启动就可以了。
5.  访问 `http://服务器ip地址:5984/_utils/index.html`  , 浏览器会弹出用户名认证窗口，输入刚才在配置文件里使用的用户名(admin)和密码(xiaoshujiang)，能够正常访问就表示数据库搭建完成，可以接下来小书匠编辑器配置的操作了。这里需要注意的是，如果您的服务器开启了端口访问限制，记得取消对 5984 端口的限制访问。

到这里 CouchDB 数据库的安装配置已经完成，下面一节的配置需要用户到小书匠编辑器上进行操作。在进行下一节配置操作时，先记录下这几个数据点

```
数据库地址: http://{服务器ip地址}:5984/{数据库名}
用户名: admin
密码: xiaoshujiang
```

其中数据库名可以是任何字母数字的组合，不需要先创建，只要提供一个合法的数据库名就可以了。

### 注

1. 如果您的服务器打开端口访问的限制功能，记得让  `5984` 端口可以访问。

## 小书匠编辑器自定义数据中心配置步骤

在前面一节 CouchDB 数据库安装并配置完成后，就可以按照下面的步骤配置小书匠客户端，实现自定义数据中心。

### 步骤


1. 注册小书匠用户，使用小书匠的[客户端][4]或者网页版本 http://markdown.xiaoshujiang.com ,  点击`小书匠主按钮>用户>用户注册`， 注册一个用户。

![用户注册][5]

2. 注册成功后，只要进行了邮箱验证，点击 `我已付费，提醒管理员` 按钮，系统就会自动给你一个临时会员的资格。

![开通临时会员][6]

3. 成功开启临时会员后，通过 `小书匠主按钮>数据>同步`  进入数据管理界面，在`服务器`选项里选择 `自定义`，输入数据库地址 `http://ip_host:5984/xsjtest`, 在帐号和密码框里输入你在 couchdb 设置的用户名和密码 (admin/xiaoshujiang). 点击 `应用`按钮 。

![自定义数据中心步骤][7]

4. 创建一篇新的文章，数据就会自动同步到你自定义数据库里了。

![数据同步][8]

![数据同步2][9]

5. 另外，可以到`小书匠主按钮>用户>用户管理界面>用户配置`里，把实时同步配置参数打开，这样在其他办公环境或者其他电脑里，只要用小书匠的账号登录，系统就会把自定义数据中心的配置信息同步过来，并同步你自己的数据文件到新的环境下，继续未完成文章的编辑。

![用户参数同步配置][10]

### 注

1. 为了让大家更好的体验小书匠自定义数据中心功能，临时会员功能时间限制会加长，在系统收回临时会员功能前，您可以使用小书匠自定义数据中心的所有功能。
2. 如果您觉得小书匠的自定义数据中心功能很好用，小书匠 markdown 编辑器也满足您的使用需求，可以考虑付费支持一下。具体付款升级流程可以查看[这里][11]


# 其他

该教程只是讲解了如何快速搭建一个个人使用的文件数据库，省略了比较多的细节配置说明。如果你想了解这些配置的用途，或者有一些个性化的需求定制，比如多用户，权限控制，集群等，CouchDB 也是支持的，可以参考 CouchDB 的[官方文档][12]。


  [1]: http://docs.couchdb.org/en/2.1.1/install/index.html
  [2]: https://github.com/suziwen/blogxiaoshujiang/blob/master/local.ini.before
  [3]: https://github.com/suziwen/blogxiaoshujiang/blob/master/local.ini.after
  [4]: http://soft.xiaoshujiang.com/download.html
  [5]: ./images/1520497153443.jpg
  [6]: ./images/1520496469826.jpg
  [7]: ./images/%E8%87%AA%E5%AE%9A%E4%B9%89%E6%95%B0%E6%8D%AE%E4%B8%AD%E5%BF%83%E6%AD%A5%E9%AA%A4.png "自定义数据中心步骤"
  [8]: ./images/1520498009096.jpg
  [9]: ./images/1520498117329.jpg
  [10]: ./images/1520497965287.jpg
  [11]: http://soft.xiaoshujiang.com/priceflow.html
  [12]: http://docs.couchdb.org/en/2.1.1/intro/index.html