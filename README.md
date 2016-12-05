# Node js for gitlab wehbook



## 运行依赖包

**[NVM](https://github.com/creationix/nvm)**

Node 版本管理工具，在各个平台安装Node.js非常方便，不过由于国内网络环境特殊情况，需要配合加速器（[淘宝npm](https://npm.taobao.org/mirrors/node)）加速包安装

```shell
$sudo wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.32.1/install.sh | bash

$ source ~/.bashrc

$sudo nvm install v6.9.1

```

**NPM**

Node.js 包管理器，推荐使用CNPM

**[CNPM](http://www.lambq.com/2016/05/12/cnpm-install-and-config/)**

淘宝NPM镜像，安装Node.js各种包非常快速

```shell

$ npm --registry=https://registry.npm.taobao.org install cnpm -g

```

[**Node-Gitlab-Webhook**](https://github.com/rolfn/node-gitlab-hook)

Gitlab Webhook工具，使用Node.js实现，对于Docker容器运行环境非常方便，具体配置参考链接。


**[PHP-Gitlab-Webhook](https://github.com/bravist/gitlab-webhook-php)**

Gitlab Webhook工具，使用PHP实现



## 用法

- cnpm安装gitlabhook

```shell
$ cnpm install gitlabhook
```

- 配置gitlabhook.conf，需要特别注意：
  - 指定项目根目录
  - 主机运行sshd 
  - 添加ssh公钥到gitlab
  - 仓库需要有权限
  -  仓库需先在主机clone，同时与仓库名称保持一致

```shell
{
  "tasks": {
    "*": [
      "echo 'GitLab Server: %s' > /tmp/gitlabhook.tmp",
      "echo 'Repository: %r' >> /tmp/gitlabhook.tmp",
      "echo 'Repository branch: %b' >> /tmp/gitlabhook.tmp",
      "echo 'Repository remote url: %g' >> /tmp/gitlabhook.tmp",
      "echo $(date) >> /tmp/gitlabhook.tmp",
      "cd /mnt/www/%r >> /tmp/gitlabhook.tmp", 
      "sudo -u www git checkout develop >> /tmp/gitlabhook.tmp",
      "sudo -u www git pull origin develop >> /tmp/gitlabhook.tmp"
    ]
  },
  "keep":false
}
```

- 运行

```shell
$ sudo /home/user/node_modules/gitlabhook/gitlabhook-server.js &

#使用nohup添加守护进程
$ sudo nohup node /home/user/node_modules/gitlabhook/gitlabhook-server.js & 

# 查看防火墙
$ sudo service iptables status 

# 编辑防火墙配置
$ sudo vi /etc/sysconfig/iptables

# 重启防火墙

$ sudo service iptables restart

```

- 结束

```shell
$sudo  ps aux | grep gitlabhook
root     23043  0.0  0.2 975996 21604 pts/4    Sl+  12:49   0:00 node /home/user/node_modules/gitlabhook/gitlabhook-server.js

$sudo kill 23043
```


## 守护进程管理webhook

使用PM2监控与管理Webhook进程，参考地址：http://pm2.keymetrics.io/

```shell

$cnpm install pm2 -g

$ pm2 start /home/user/node_modules/gitlabhook/gitlabhook-server.js

$ pm2 list

┌───────────────────┬────┬──────┬───────┬────────┬─────────┬────────┬─────┬───────────┬──────────┐
│ App name          │ id │ mode │ pid   │ status │ restart │ uptime │ cpu │ mem       │ watching │
├───────────────────┼────┼──────┼───────┼────────┼─────────┼────────┼─────┼───────────┼──────────┤
│ gitlabhook-server │ 0  │ fork │ 26072 │ online │ 0       │ 5m     │ 0%  │ 18.6 MB   │ disabled │
└───────────────────┴────┴──────┴───────┴────────┴─────────┴────────┴─────┴───────────┴──────────┘
 Use `pm2 show <id|name>` to get more details about an app
```

如果，出现status=errored，请使用pm2 show gitlabhook-server，查看错误
```shell
$ cat /user/.pm2/logs/gitlabhook-server-out-0.logs 
```
如果报错
```shell
can't read config file:  gitlabhook.conf
self: GitLabHook {
  configFile: 'gitlabhook.conf',
  configPathes: [ '/etc/gitlabhook/', '/usr/local/etc/gitlabhook/', '.' ],

```
gitlabhook.conf复制一份到/etc/gitlabhook/gitlabhook.conf

```shell
$ cp /gitlabhook.conf /etc/gitlabhook/gitlabhook.conf
```



## 参考资源

https://npm.taobao.org/
http://riny.net/2014/cnpm/
https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-a-centos-7-server#InstallNodeUsingtheNodeVersionManager
https://github.com/creationix/nvm#install-script
http://www.lambq.com/2016/05/12/nodejs-install-and-config/
http://www.lambq.com/2016/05/12/cnpm-install-and-config/



# License

MIT
