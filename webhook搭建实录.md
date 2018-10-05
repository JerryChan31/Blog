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

搭建环境：我正在使用的是 Vultr 的服务器，系统版本是 CentOS 7 x64。

## 在服务器上安装 Node.js

最简单的方法：从 EPEL 库安装 Node.js

    $ sudo yum install epel-release
    $ sudo yum install nodejs
    // 检查是否成功安装
    $ node --version
    $ npm --version

（注：未经本人测试，版本可能相对较老。）

我个人搭建过程中使用了 [Node.js 官网](https://nodejs.org/zh-cn/download/) 的 Linux 二进制文件来安装 Node.js。下载安装包时要留意 Linux 的版本。（X86/X64）

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
    var child = spawn(cmd, args);
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

这里 Node.js 监听的是 7777 端口，你也可以使用 Nginx 反向代理到 80 端口。

用下面的命令测试一下，接收到 push 之后控制台会有输出：

    $ node deploy.js

如果没什么问题，forever 就可以开起来了。

    $ forever start deploy.js

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