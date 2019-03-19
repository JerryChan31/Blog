<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [基于 Node.js 的服务器自动化部署搭建实录](#%E5%9F%BA%E4%BA%8E-nodejs-%E7%9A%84%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%87%AA%E5%8A%A8%E5%8C%96%E9%83%A8%E7%BD%B2%E6%90%AD%E5%BB%BA%E5%AE%9E%E5%BD%95)
  - [在服务器上安装 Node.js](#%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E5%AE%89%E8%A3%85-nodejs)
  - [编写拉取仓库、重启服务器脚本](#%E7%BC%96%E5%86%99%E6%8B%89%E5%8F%96%E4%BB%93%E5%BA%93%E9%87%8D%E5%90%AF%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%84%9A%E6%9C%AC)
  - [配置 Github 仓库的 Webhook 设置](#%E9%85%8D%E7%BD%AE-github-%E4%BB%93%E5%BA%93%E7%9A%84-webhook-%E8%AE%BE%E7%BD%AE)
  - [配置 Node.js 脚本](#%E9%85%8D%E7%BD%AE-nodejs-%E8%84%9A%E6%9C%AC)
  - [其他问题](#%E5%85%B6%E4%BB%96%E9%97%AE%E9%A2%98)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 基于 Node.js 的服务器自动化部署搭建实录

> 摘要：本文主要记录我在服务器上使用 GitHub 的 Webhooks 进行网站自动化部署的过程。最终效果：开发终端向 Github 仓库推送代码后，服务器端自动拉取仓库并重启服务器。搭建过程主要参考了 [lovelucy.info](https://www.lovelucy.info/auto-deploy-website-by-webhooks-of-github-and-gitlab.html) 的博客。

> 2019-03-19更新：给毕业设计搭建代码自动部署，遇到了脚本调用守护进程时，守护进程不能正常执行的问题。在此非常感谢[node中创建服务进程](http://www.cnblogs.com/accordion/p/6828097.html#commentform)这篇文章给予我的帮助，完美地解决了我的问题。

搭建环境：我正在使用的是 Vultr 的服务器，系统版本是 CentOS 7 x64。

## 在服务器上安装 Node.js

经过踩坑，通过更新EPEL库使用yum下载nodejs的方法，下载的版本比较老旧。我个人搭建过程中使用了 [Node.js 官网](https://nodejs.org/zh-cn/download/) 的 Linux 二进制文件来安装 Node.js，我个人也更推荐这种做法。下载安装包时要留意 Linux 的版本。（X86/X64）

    // Node.js 安装目录
    $ cd /usr/local/bin 

    // Node.js 官网提供的 Linux 二进制文件
    $ wget https://nodejs.org/dist/v8.12.0/node-v8.12.0-linux-x64.tar.xz

    // 解压缩
    $ tar xvJf node-v8.12.0-linux-x64.tar.xz

    // 配置环境变量
    $ vi /etc/profile

    // 添加以下内容到文件末尾 ====================
    export NODE_HOME=/usr/local/bin/nodejs

    export PATH=$PATH:$NODE_HOME/bin

    export NODE_PATH=$NODE_HOME/lib/node_modules

    // 添加以上内容到文件末尾 ====================

    // 保存退出后source一下，使其立即生效
    $ source /etc/profile 
    
    // 检查是否安装成功
    $ node -v
    $ npm -v

至此，成功在服务器上安装最新版本的 Node.js。

> 如果不是安装在`/usr/local/bin`目录，需要通过`ln -s node安装目录/bin/node /usr/local/bin`以及`ln -s node安装目录/bin/npm /usr/local/bin` 的方法来给 node 和 npm 创建链接。

## 编写拉取仓库、重启服务器脚本

一个可供参考的例子：deploy.sh

    #!/bin/bash
 
    WEB_PATH='/home/nodejs-be-demo'

    echo "Start deployment"
    cd $WEB_PATH
    echo "pulling source code..."
    git reset --hard origin/master
    git clean -f
    git pull
    git checkout master
    npm install
    npm run start
    echo "Finished."

可以按照自己的需要编写脚本。

## 配置 Github 仓库的 Webhook 设置

1. 在要配置的 Github 的设置页面找到 Webhooks 选项，点击「Add webhook」。
2. 配置 Payload URL（接受 POST 请求的服务器 URL）。
3. Secret（可以理解为配对暗号）。
4. Content-type 选择 `application/json`。
5. 其余默认设置即可。

## 配置 Node.js 脚本

在配置 Node.js 脚本之前，需要先安装依赖，这里用到了一个中间件[ github-webhook-handler ](https://github.com/rvagg/github-webhook-handler)，以及进程管理服务[ forever ](https://github.com/foreverjs/forever)。

    $ npm install -g github-webhook-handler
    $ npm install -g forever

脚本内容如下：deploy.js

    var http = require('http')
    var createHandler = require('github-webhook-handler')
    var handler = createHandler({ path: '/autodeploy', secret: 'mySecret' }) 
    // 上面的 secret 保持和 GitHub 后台设置的一致
    
    function run_cmd(cmd, args, callback) {
    var spawn = require('child_process').spawn;
    var child = spawn(cmd, args, {
        detached: true,
        stdio: [process.stdin,process.stdout,process.stderr]
    });
    var resp = "";
    
    child.stdout.on('data', function(buffer) { resp += buffer.toString(); });
    child.stdout.on('end', function() { callback (resp) });
    }
    
    http.createServer(function (req, res) {
    handler(req, res, function (err) {
        res.statusCode = 404
        res.end('no such location')
    })
    }).listen(7777)
    // 这里是监听的端口号

    handler.on('error', function (err) {
    console.error('Error:', err.message)
    })
    
    handler.on('push', function (event) {
    console.log('Received a push event for %s to %s',
        event.payload.repository.name,
        event.payload.ref);
    run_cmd('sh', ['./deploy.sh'], function(text){ console.log(text) });
    })
    
    /*
    handler.on('issues', function (event) {
    console.log('Received an issue event for % action=%s: #%d %s',
        event.payload.repository.name,
        event.payload.action,
        event.payload.issue.number,
        event.payload.issue.title)
    })
    */

***
**2019-03-19更新：**
脚本在`spawn`的参数列表中加入了第三个参数：
```js
{
  detached: true,
  stdio: [process.stdin,process.stdout,process.stderr]
}
```
>在 node 环境下，如果不针对子进程的 stdio 做一些特殊处理父进程其实不会真正退出，而是直到子进程执行完毕后再退出。之所以出现这种情况是由于 node 创建子进程时默认会通过 pipe 方式将子进程的输出导流到父进程的 stream 中（childProcess.stdout、childProcess.stderr），提供在父进程中输出子进程消息的能力。
>`detached`选项可以让node原生帮我们创建一个daemon进程，设置`datached`为true可以创建一个新的session和进程组，子进程的pid为新创建进程组的组pid，这与setsid起到相同的作用。此时的子进程已经和其父进程属于两个session，因此父进程的退出和中断信号不会传递给子进程，子进程不会接受到父进程的中断信号自然也不会退出。当父进程结束之后，子进程变为孤儿进程从而被init进程接收，ppid设置为1。

加入这两个参数的原因是因为，在使用原来的js脚本执行bash脚本时，如果bash脚本中使用pm2这样的守护程序，在bash脚本执行结束后，执行过程中创建的pm2进程也会中断掉。

上面引用段落引用自[node中创建服务进程](http://www.cnblogs.com/accordion/p/6828097.html#commentform)，我的个人理解是：

设置`detached`为`true`之后，`spawn`在运行时创建了新的session和进程组，父进程的的退出和中断信号不会传递给子进程。

设置`stdio: [process.stdin,process.stdout,process.stderr]`之后，子进程的stdio被设置为当前终端，这样一来bash脚本的环境就相当于提升了一级，直接和终端进行io交互。

***


这里 Node.js 监听的是 7777 端口，你也可以使用 Nginx 反向代理到 80 端口。

用下面的命令测试一下，接收到 push 之后控制台会有输出，你可以在配置 webhook 的页面下重复触发同一个hook，无需多次 commit & push。

    $ node deploy.js

如果没什么问题，forever 就可以开起来了。

    $ forever start deploy.js

***

**2019-03-19更新：**
进程守护工具更新：pm2
比起forever，pm2提供了更全面的监控功能，可用于产品级服务。
[pm2 使用介绍](https://segmentfault.com/a/1190000002539204)

用法：
```bash
$ npm install pm2 -g     # 命令行安装 pm2 
$ pm2 start app.js -i 4  # 后台运行pm2，启动4个app.js 
                         # 也可以把'max' 参数传递给 start
                         # 正确的进程数目依赖于Cpu的核心数目
$ pm2 start app.js --name my-api # 命名进程
$ pm2 list               # 显示所有进程状态
$ pm2 monit              # 监视所有进程
$ pm2 logs               # 显示所有进程日志
$ pm2 stop all           # 停止所有进程
$ pm2 restart all        # 重启所有进程
$ pm2 reload all         # 0 秒停机重载进程 (用于 NETWORKED 进程)
$ pm2 stop 0             # 停止指定的进程
$ pm2 restart 0          # 重启指定的进程
$ pm2 startup            # 产生 init 脚本 保持进程活着
$ pm2 web                # 运行健壮的 computer API endpoint (http://localhost:9615)
$ pm2 delete 0           # 杀死指定的进程
$ pm2 delete all         # 杀死全部进程
```

运行进程的不同方式：
```bash
$ pm2 start app.js -i max    # 根据有效CPU数目启动最大进程数目
$ pm2 start app.js -i 3      # 启动3个进程
$ pm2 start app.js -x        #用fork模式启动 app.js 而不是使用 cluster
$ pm2 start app.js -x -- -a 23   # 用fork模式启动 app.js 并且传递参数 (-a 23)
$ pm2 start app.js --name serverone  # 启动一个进程并把它命名为 serverone
$ pm2 stop serverone       # 停止 serverone 进程
$ pm2 start app.json        # 启动进程, 在 app.json里设置选项
$ pm2 start app.js -i max -- -a 23                   #在--之后给 app.js 传递参数
$ pm2 start app.js -i max -e err.log -o out.log  # 启动 并 生成一个配置文件
你也可以执行用其他语言编写的app  ( fork 模式):
$ pm2 start my-bash-script.sh    -x --interpreter bash
$ pm2 start my-python-script.py -x --interpreter python
```

***


## 其他问题

部署过程中可能遇到无法访问对应端口的问题，需要检查一下服务器的防火墙设置。大部分服务器都是白名单机制，只开放特定的端口。

CentOS 7 下默认使用的防火墙是 FirewallD，之前版本请搜索[ iptables ](https://www.google.com/search?rlz=1C1SQJL_zh-CNJP783JP783&ei=9Ju3W5jhKsa28QXfmJjoDw&q=iptables&oq=iptables&gs_l=psy-ab.3..35i39k1l2j0i67k1l6j0l2.40278.41079.0.41520.8.7.0.0.0.0.229.754.0j3j1.4.0....0...1.1j4.64.psy-ab..4.4.754....0.5yd0BKPQ-nc)。
另：[FirewallD 和 iptables 的区别？](https://linux.cn/article-8098-1.html)

FirewallD 相关命令：

 - 启动服务，并在系统引导式启动该服务

        sudo systemctl start firewalld
        sudo systemctl enable firewalld
 - 停止并禁用

        sudo systemctl stop firewalld
        sudo systemctl disable firewalld

 - 检查防火墙状态。输出应该是 running 或者 not running。

        sudo firewall-cmd --state
    
 - 允许或拒绝任意端口/协议（如：12345端口，该规则在 public 区域）

        sudo firewall-cmd --zone=public --add-port=12345/tcp --permanent
        sudo firewall-cmd --zone=public --remove-port=12345/tcp --permanent

 - 重新加载 FirewallD 使规则立即生效

        sudo firewall-cmd --reload

 - 查看特定区域的所有配置

        sudo firewall-cmd --zone=public --list-all
    
    示例输出：

        public (default, active)
            interfaces: ens160
            sources:
            services: dhcpv6-client http ssh
            ports: 12345/tcp
            masquerade: no
            forward-ports:
            icmp-blocks:
            rich rules:

更多详细的 FirewallD 配置，请看 [CentOS 上的 FirewallD 简明指南](https://linux.cn/article-8098-1.html)。