```shell
## 执行gitlab-server.js

$ pm2 start ~/node_modules/gitlabhook/gitlabhook-server.js

## 查看gitlab-server端口是否开启

$ netstat -tunlp | grep 3420

```

####  在Gitlab仓库中配置Webhooks

首先如果你的gitlab仓库和webhook后台在同一台机器上，需要先开启允许本地webhook。进入admin控制后台页面，setting->outbound requests
![webhooks-1](https://docs.gitlab.com/ee/security/img/outbound_requests_section.png)

登录你的Gitlab项目Integrations，添加Push events。URL:http://x.x.x.x:3420


#### 提交版本并测试
点击页面edit，test选择push event事件进行测试

