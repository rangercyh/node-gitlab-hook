#### 1. 安装Node环境

推荐使用Node版本管理工具[nvm](https://github.com/creationix/nvm)。

```shell
## 使用curl工具下载安装
$ wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash

## 在当前窗口使用nvm
$ source ~/.bash_profile

## 查看远程仓库Node版本
$ nvm ls-remote

## 选择Node版本执行安装
$ nvm install v7.8.0

## 查看已安装Node环境
$ node -v
```

最新版本Node已经自动安装了包管理工具`npm`，国内用户推荐使用淘宝的镜像工具`cnpm`。

```shell
## 使用cnpm替换npm
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```



#### 2. 安装NodeJS Gitlabhook扩展包

进入NodeJS包管理在线仓库[npmjs](https://www.npmjs.com/)，搜索`gitlabhook`，可以查看相关配置信息。

```shell
## 全局安装gitlabhook
$ npm install --global gitlabhook

##查看gitlabhook安装路径
$ npm


```

配置`gitlabhook.conf`

```shell
##进入 ~/.nvm/versions/node/v7.8.0/lib/node_modules
$ cd ~/.nvm/versions/node/v7.8.0/lib/node_modules/gitlabhook

##复制gitlabhook.conf至/etc/gitlabhook.conf
$ cp gitlabhook.conf /etc/gitlabhook.conf

## 编辑/etc/gitlabhook.conf
$ vi /etc/gitlabhook.conf

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

这里特别提醒：

+ CentOS 宿主机上需要安装git版本管理工具`yum install -y git`
+ CentOS 宿主机上默认还是需要通过git命令去远程克隆仓库，CentOS宿主机需要配置SSH-Key并在gitlab中进行配置，否则会报错：“无权限克隆仓库”
+ 需要使用CentOS 宿主机上的虚拟主机目录的拥有者去使用git命令，比如配置文件中使用`www`用户去克隆仓库
+ 注意在你的CentOS 宿主机上确认克隆仓库的版本，如果是测试环境建议使用`develop`分支，如果是生产环境建议使用`master`分支


#### 3. 安装NodeJS 进程管理工具PM2

使用NodeJS 的进程管理工具[PM2](http://pm2.keymetrics.io/)方便监控并管理NodeJS进程。

```shell
## 全局安装PM2

$ npm install --global pm2

## 查看正在运行的进程

$ pm2 list

## 监控当前运行的进程

$ pm2 monit
```



#### 4.  运行NodeJS Gitlabhook

```shell
## 执行gitlab-server.js

$ pm2 start ~/.nvm/versions/node/v7.8.0/lib/node_modules/gitlabhook/gitlabhook-server.js

## 查看gitlab-server端口是否开启

$ netstat -tunlp | grep 3420

## 在本地确认远程3420端口是否可访问

$ telnet 138.68.24.82 3420

Trying 138.68.24.82...
Connected to 138.68.24.82.
Escape character is '^]'.

##如果看到上面的信息，表示Gitlab Server已经开始运行，如果没有，可以查看CentOS防火墙是否允许3420端口访问
```

####  5. 在Gitlab仓库中配置Webhooks

登录你的Gitlab项目管理后台，搜索`Webhooks`并添加Push events（不同版本的Gitlab配置Webhooks可能不一样）。

![webhooks-1](https://chenghuiyong.com/images/Webhooks/wehooks-1.png)

![webhooks](https://chenghuiyong.com/images/Webhooks/wehooks-2.png)

#### 6. 提交版本并测试

在你的本地开发仓库中，执行`git push `，进入CentOS服务器检查代码是否自动同步。



使用NodeJS版本的Gitlab Webhooks解除了与Web服务器强依赖关系，同时服务器运行环境源码保持干净灵活，非常适合Docker的运行环境。另外，此项目不仅仅可以运行在Gitlab版本仓库中，同样也可以运行在Github、Coding等运行环境中。
