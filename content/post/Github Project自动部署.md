---
title: "Github Project自动部署"
date: 2019-05-05:00:00+08:00
lastmod: 2019-05-05T00:00:00+08:00
draft: false
tags: ["CI/CD", "coding"]
categories: ["Tech"]
keywords: ["github", "deploy", "Circle CI", "IFTTT", "CI/CD"]
description: "deploy your Github Project automatically via CircleCI and send notification when deploy succeed"

weight: 10
contentCopyright: MIT
mathjax: true
autoCollapseToc: true
---
# Init

Circle CI提供[免费plan](https://github.com/marketplace/circleci/plan/MDIyOk1hcmtldHBsYWNlTGlzdGluZ1BsYW45MA==#pricing-and-setup),对github项目有很好的支持,在repo里可以一键接入circle ci.并且支持private repo,这也是选择Circle CI的原因之一

需要的东西:

- Github project repo
- 一台VPS
- Circle CI的配置yaml文件
- IFTTT event

# Step

这里假设项目都已经完成并且有Test Case

1. 关联github账号和Circle CI(好像点几下就好了..会自动给repo权限之类的)
2. 添加Circle CI配置文件到项目目录([How to write config yaml](https://circleci.com/docs/))
    1. **build** *配置镜像 → 装dependince → run Test →* build success
    2. **deploy** 通过ssh登录到VPS,执行部署命令(这里我用的docker-compose)
3. 在Circle CI项目设置中配置SSH(`$SSH_USER $SSH_HOST`)
4. 登录VPS配置github ssh key,从而实现每次部署前从github pull最新代码

[Adding a new SSH key to your GitHub account - GitHub Help](https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account)

1. 在Circle CI中配置CI登录到VPS的ssh key(**can copy it from local machine**)

    **Creating an SSH key on Linux**

    The tools to create and use SSH are standard, and should be present on most Linux distributions. With the following commands, you can generate ssh key.

    - Run: 要用`ssh-keygen -m pem`  因为CircleCI不支持OpenSSH

        Note: Since CircleCI cannot decrypt SSH keys, every new key must have an empty passphrase. CircleCI also will not accept OpenSSH’s default file format - use ssh-keygen -m pem if you are using OpenSSH to generate your key.

    - Press enter when asked where you want to save the key (this will use the default location).
    - Enter a passphrase for your key.
    - Run `cat ~/.ssh/id_rsa.pub` - this will give you the key in the proper format to paste into the control panel.
    - Make sure you backup the `~/.ssh/id_rsa` file. This cannot be recovered if it is lost.

    ### **配置VPS**

    创建VPS的时候可以直接制定SSH密钥登录。。如果是已经创建好的VPS。

    1. 将`id_rsa.pub` 放到`/root/.ssh`目录下。并改名为`authorized_keys`
    2. 修改sshd_config配置文件`vi /etc/ssh/sshd_config`
    3. 重启SSH服务，Ubuntu使用命令`sudo /etc/init.d/ssh restart`

    sshd_config配置文件修改相关选项如下：

    ```bash
    RSAAuthentication yes #RSA认证
    PubkeyAuthentication yes #开启公钥验证
    AuthorizedKeysFile .ssh/authorized_keys #验证文件路径
    PasswordAuthentication no #禁止密码认证
    PermitEmptyPasswords no #禁止空密码
    ```

2. 部署成功后通过IFTTT发消息到手机(在code中实现 [IFTTT Webhooks](https://help.ifttt.com/hc/en-us/articles/115010230347-The-Webhooks-Service))
3. 做一些Code change,try it!

### Code

Circle CI配置*(.circleci/config.yml)*

```bash
version: 2
jobs:
  build:
    docker:
      - image: circleci/python:3.7
    working_directory: ~/auto_trading
    steps:
      - checkout
      - run:
          name: install dependencies
          command: |
            pipenv install --dev
  deploy:
    machine:
      enabled: true
    steps:
      - run:
          name: Deploy Over SSH
          command: |
            ssh $SSH_USER@$SSH_HOST "cd /root/workspace/project_name && git pull && docker-compose build && docker-compose up -d"
workflows:
  version: 2
  build-and-deploy:
    jobs:
      - build
      - deploy:
          requires:
            - build
          filters:
            branches:
              only: master
```

### 成果

每次有code change只要push到github就可以进行自动化的CI/CD

![http://www.ruanyifeng.com/blogimg/asset/2015/bg2015092302.jpg](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015092302.jpg)

### Reference

[持续集成是什么？ - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html)

[Deployment - CircleCI](https://circleci.com/docs/2.0/deployment/)

[Vultr: High Performance SSD Cloud.](https://www.vultr.com/docs/how-do-i-generate-ssh-keys/)