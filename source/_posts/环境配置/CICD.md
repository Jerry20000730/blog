---
title: CICD流水线搭建流程 [GitLab + Ubuntu]
tags: [CICD]
categories: 环境配置
cover: images/CICD/cicd.svg
excerpt: 记录一下在ubuntu服务器上搭建CICD (未使用Docker)
---

# Prerequisite
1. GitLab 仓库
2. Ubuntu (18.04/20.04/22.04) 服务器
3. 编程语言runtime

# 1. 创建GitLab仓库

![create gitlab repo](/images/CICD/gitlab-new-project.png)

# 2. 创建GitLab Runner

服务器上先安装`gitlab-runner`服务
```shell
sudo apt-get install gitlab-runner
```

检查安装是否成功
```shell
systemctl status gitlab-runner
```

会显示
```
● gitlab-runner.service - GitLab Runner
   Loaded: loaded (/etc/systemd/system/gitlab-runner.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2020-06-01 09:01:49 UTC; 4s ago
 Main PID: 16653 (gitlab-runner)
    Tasks: 6 (limit: 1152)
   CGroup: /system.slice/gitlab-runner.service
           └─16653 /usr/lib/gitlab-runner/gitlab-runner run --working-directory /home/gitlab-runner --config /etc/gitlab
```

其次在GitLab上进行绑定，进入仓库 - Settings - CICD - Runners

![gitlab runner location](/images/CICD/gitlab-runner.png)

点击 `New Project Runner`, 然后写一下tag和description（确保你知道这个是那个服务器和干什么的），然后选中 `Run untagged project`，确保你不会因为该项目没有tag而无法在runner上跑

![gitlab runner configure](/images/CICD/gitlab-runner-configure.png)

# 3. 绑定gitlab-runner和服务器
当 `gitlab-runner` 注册之后，需要在服务器上进行token绑定

![gitlab runner binding](/images/CICD/new-runner-created.png)

输入基础命令
```shell
gitlab-runner register --url [project_URL] --token [runner_token]
```
然后可能会让你输入几个关键信息

1. executor: 参照gitlab官方给出的[executor类型](https://docs.gitlab.com/runner/executors/)，我们因为是个小项目，暂时没有使用docker，所以选择shell
2. docker-image: 如果选择docker作为executor的话，就会要求配置docker镜像
3. description: 描述一下
等信息，其它我没遇到，遇到了再记录

# 4. 编写 `.gitlab-ci.yml`

首先是要填写流水线的阶段
```yaml
stages:
  - build
  - test
  - deploy
```

其次是各阶段所需要的命令和生成的文件单独列举出来，因为我们这个是个springboot项目，每次推送main分支之后就会进行maven编译，生成target文件夹下的jar包，然后在java runtime运行，流水线思路简单。

```yaml
build:
  stage: build
  script:
    - mvn clean package
  artifacts:
    paths:
      - target/*.jar
    expire_in: 1 week

test:
  stage: test
  script:
    - mvn test

deploy:
  stage: deploy
  script:
    - mkdir -p $DEPLOY_PATH
    - cp target/$APP_NAME $DEPLOY_PATH/
    - |
      while pgrep -f "$APP_NAME" > /dev/null; do
        pkill -f "$APP_NAME"
        sleep 1
      done
    - nohup java -jar $DEPLOY_PATH/$APP_NAME --spring.profiles.active=prod > $DEPLOY_PATH/app.log 2>&1 &
```

其中
```yaml
    - |
      while pgrep -f "$APP_NAME" > /dev/null; do
        pkill -f "$APP_NAME"
        sleep 1
      done
    - nohup java -jar $DEPLOY_PATH/$APP_NAME --spring.profiles.active=prod > $DEPLOY_PATH/app.log 2>&1 &
```
是为了保证能够确保关掉上一次跑的进程。

# Reference
1. How To Set Up a Continuous Deployment Pipeline with GitLab CI/CD on Ubuntu [https://www.digitalocean.com/community/tutorials/how-to-set-up-a-continuous-deployment-pipeline-with-gitlab-on-ubuntu](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-continuous-deployment-pipeline-with-gitlab-on-ubuntu)
2. Use CI/CD to build your application [https://docs.gitlab.com/ee/topics/build_your_application.html](https://docs.gitlab.com/ee/topics/build_your_application.html)